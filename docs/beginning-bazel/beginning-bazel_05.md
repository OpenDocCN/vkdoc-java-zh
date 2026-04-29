# 5. 一个简单的 Echo 客户端/服务器程序

在上一章中，你学习了 `WORKSPACE` 文件的基础知识，了解了如何添加外部依赖，包括新语言。在本章中，我们将基于这些内容创建一对简单程序，分别使用不同语言（一个 Java，另一个 Go），让两者之间进行消息往返传递。

## 配置你的工作区

让我们为工作创建一个新目录：

```
$ mkdir chapter_05
$ cd chapter_05
chapter_05$ touch WORKSPACE
```

我们将引入上一章使用过的 Go 规则。打开你新建的 `WORKSPACE` 文件并添加以下内容。

```
load("@bazel_tools//tools/build_defs/repo:http.bzl", "http_archive")
http_archive (
name = "io_bazel_rules_go",
urls = ["https://github.com/bazelbuild/rules_go/releases/download/v0.19.5/rules_go-v0.19.5.tar.gz"],
)
load("@io_bazel_rules_go/go:deps.bzl", "go_rules_dependencies", "go_register_toolchains")
go_rules_dependencies()
go_register_toolchains()
Listing 5-1
Adding in the Go rules
```

保存你的 `WORKSPACE` 文件。

对于这个特定示例，我们将把所有代码放在单个目录中。在实践中，出于组织上的考虑，你很可能会按语言拆分代码目录。

现在让我们创建工作目录以及配套的 `BUILD` 文件：

```
chapter_05$ mkdir src
chapter_05$ cd src
chapter_05/src$ touch BUILD
```

正如你之前所见，一些语言（例如 C++、Java 等）是 Bazel 开箱即用支持的；因此我们不需要显式加载这些规则。不过，正如上一章所示，其他语言的规则则需要显式加载。本例中，我们将加载 Go 规则。

打开 `BUILD` 文件并添加以下内容。

```
load("@io_bazel_rules_go//go:def.bzl", "go_library", "go_binary")
Listing 5-2
Loading the Go language rules
```

保存你的 `BUILD` 文件。

现在让我们开始创建一些程序。我们将先从 Go 版本开始。



## Go 回显服务器

我们将从一个简单的 Go 回显服务器开始。顾名思义，它的主要工作是接受传入连接，从连接中读取字节，并将这些字节（经过修改后的版本）返回给发送方。

```
package main
import (
"log"
"net"
)
func main() {
log.Println("Spinning up the Echo Server in Go...")
listen, error := net.Listen("tcp", ":1234")
if error != nil {
log.Panicln("Unable to listen: " + error.Error())
}
defer listen.Close()
connection, error := listen.Accept()
if error != nil {
log.Panicln("Cannot accept a connection! Error: " + error.Error())
}
log.Println("Receiving on a new connection")
defer connection.Close()
defer log.Println("Connection now closed.")
buffer := make([]byte, 2048)
size, error := connection.Read(buffer)
if error != nil {
log.Println("Cannot read from the buffer! Error: " + error.Error())
}
data := string(buffer[:size])
log.Println("Received data: " + data)
connection.Write([]byte("Echoed from Go: " + data))
}
Listing 5-3
Simple Go echo server
```

将其保存到 `chapter_05/src/echo_server.go`。

为了稍微梳理一下这段代码，Go 回显服务器会先在“1234”端口上监听连接。一旦接受到连接，它会从该连接读取数据，对其做一点小修改（即在前面加上“Echoed from Go:”），然后把修改后的数据发回给发送者。为简化起见，它在回显完数据后就会关闭服务。

现在让我们在 `BUILD` 文件中创建这个构建目标的条目。

```
go_binary(
name = "echo_server",
srcs = ["echo_server.go"],
)
Listing 5-4
Go echo server build target
```

保存 `BUILD` 文件。

现在是时候真正构建并运行回显服务器了：

```
chapter_05/src$ bazel run :echo_server
INFO: Analysed target //src:echo_server (0 packages loaded, 0 targets configured).
INFO: Found 1 target...
Target //src:echo_server up-to-date:
bazel-bin/src/darwin_amd64_stripped/echo_server
INFO: Elapsed time: 0.125s, Critical Path: 0.00s
INFO: 0 processes.
INFO: Build completed successfully, 1 total action
INFO: Build completed successfully, 1 total action
2019/06/02 17:49:07 Spinning up the Echo Server in Go...
```

此时，它会一直挂在那里，因为还没有任何客户端连接它。先终止这个进程，我们来修复这个问题。我们会暂时放下 Go，转到 Java。

## Java 回显客户端

顾名思义，我们将创建一个连接到回显服务器的程序。回显客户端会尝试连接服务器，从用户读取一些数据，将其发送到服务器，然后把从服务器收到的内容输出出来。

```
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.PrintWriter;
import java.net.Socket;
public class EchoClient {
public static void main (String args[]) {
System.out.println("Spinning up the Echo Client in Java...");
try {
final Socket socketToServer = new Socket("localhost", 1234);
final BufferedReader inputFromServer = new BufferedReader(
new InputStreamReader(socketToServer.getInputStream()));
final BufferedReader commandLineInput = new BufferedReader(
new InputStreamReader(System.in));
System.out.println("Waiting on input from the user...");
final String inputFromUser = commandLineInput.readLine();
if (inputFromUser != null) {
System.out.println("Received by Java: " + inputFromUser);
final PrintWriter outputToServer =
new PrintWriter(socketToServer.getOutputStream(), true);
outputToServer.println(inputFromUser);
System.out.println(inputFromServer.readLine());
}
socketToServer.close();
} catch (Exception e) {
System.err.println("Error: " + e);
}
}
}
Listing 5-5
Simple Java echo client
```

将上述代码保存到文件 `chapter_05/src/EchoClient.java`。

`EchoClient` 会尝试在本地与 1234 端口上的服务器建立连接。若连接成功，它会从用户读取一行数据，通过连接发送出去，并打印来自服务器的响应。再次说明，为了简化示例，它只会运行一次然后退出。

现在让我们把它的条目添加到 `BUILD` 文件中。

```
java_binary(
name = "EchoClient",
srcs = ["EchoClient.java"],
)
Listing 5-6
Java echo client build target
```

将其保存到 `BUILD` 文件中。

让我们先单独运行它，以确保可以完成构建和运行：

```
chapter_05/src$ bazel run :EchoClient
INFO: Analysed target //src:EchoClient (0 packages loaded, 2 targets configured).
INFO: Found 1 target...
Target //src:EchoClient up-to-date:
bazel-bin/src/EchoClient.jar
bazel-bin/src/EchoClient
INFO: Elapsed time: 0.342s, Critical Path: 0.12s
INFO: 1 process: 1 worker.
INFO: Build completed successfully, 2 total actions
INFO: Build completed successfully, 2 total actions
Spinning up the Echo Client in Java...
Error: java.net.ConnectException: Connection refused (Connection refused)
```

恭喜！你的程序成功运行了……然后由于没有可连接的目标而终止。别担心，我们马上就会修正这个问题。



### 为 Echo 客户端和服务器命名

细心的读者会注意到，我们的 Java 目标名称风格与 Go 目标中的名称风格不同。

具体来说，一个名称主要由下划线组成，另一个则使用驼峰命名法。

```
go_binary(
name = "echo_server",
srcs = ["echo_server.go"],
)
java_binary(
name = "EchoClient",
srcs = ["EchoClient.java"],
)
Listing 5-7
Build targets
```

需要说明的是，这里并不是编辑错误；这种差异确实有其原因，至少对 Java 来说是如此。对于 Java 二进制构建目标，目标名称还会被用作一种简写，告知 Bazel 在这组源码中哪个类是主类。

为了说明这一点，让我们把 `java_binary` 构建目标的名称修改为与 `go_binary` 构建目标一致的风格。

```
java_binary(
name = "echo_client",
srcs = ["EchoClient.java"],
)
Listing 5-8
Modified java_binary name style
```

将此更改保存到 `BUILD` 文件中。

构建仍然会像预期那样工作：

```
chapter_05/src$ bazel build :echo_client
INFO: Analysed target //src:echo_client (1 packages loaded, 2 targets configured).
INFO: Found 1 target...
Target //src:echo_client up-to-date:
bazel-bin/src/echo_client.jar
bazel-bin/src/echo_client
INFO: Elapsed time: 0.261s, Critical Path: 0.09s
INFO: 1 process: 1 worker.
INFO: Build completed successfully, 3 total actions
```

不过，你会发现实际上无法运行该程序：

```
chapter_05/src$ bazel run :echo_client
INFO: Analysed target //src:echo_client (0 packages loaded, 0 targets configured).
INFO: Found 1 target...
Target //src:echo_client up-to-date:
bazel-bin/src/echo_client.jar
bazel-bin/src/echo_client
INFO: Elapsed time: 0.137s, Critical Path: 0.00s
INFO: 0 processes.
INFO: Build completed successfully, 1 total action
INFO: Build completed successfully, 1 total action
Error: Could not find or load main class echo_client
```

在没有任何额外指引的情况下，`java_binary` 规则会利用构建目标名称来推断 `main` 函数所在的类（更准确地说，是***应该***使用其 `main` 函数的那个类）。在我们修改后的示例中，Bazel 正在尝试查找并不存在的类 `echo_client`。

这个结果是合理的。尽管 Bazel 规则提供了一个便利机制，可用构建目标名来省略主类声明，但如果不遵循该约定，它不会尝试隐式选择一个可能包含主类的类。如示例所示，即使只有一个类也是如此。

幸运的是，我们可以添加显式声明让它重新工作。

```
java_binary(
name = "echo_client",
srcs = ["EchoClient.java"],
main_class = "EchoClient",
)
Listing 5-9
Explicitly listing the java_binary main class
```

将此更改保存到 `BUILD` 文件中。

现在运行会像之前一样正常：

```
chapter_05/src$ bazel run :echo_client
INFO: Analysed target //src:echo_client (1 packages loaded, 2 targets configured).
INFO: Found 1 target...
Target //src:echo_client up-to-date:
bazel-bin/src/echo_client.jar
bazel-bin/src/echo_client
INFO: Elapsed time: 0.204s, Critical Path: 0.00s
INFO: 0 processes.
INFO: Build completed successfully, 2 total actions
INFO: Build completed successfully, 2 total actions
Spinning up the Echo Client in Java...
Error: java.net.ConnectException: Connection refused (Connection refused)
```

前面的代码提醒我们：Bazel 希望一切都尽可能显式。确实，有些规则提供了一些快捷方式来让使用更轻松，但归根结底，Bazel 不希望依赖管理或构建规范是“魔法式”的。通过显式声明依赖、构建规范等内容，Bazel 才能进行优化并提供保证，从而让构建快速、稳定且可理解。

现在，让我们真正把程序一起运行起来。

## 在程序之间进行回显

客户端和服务器程序都创建好之后，现在该把它们一起运行了。你需要两个不同的 shell 实例分别运行它们。

在第一个 shell 中，先把服务器重新启动起来：

```
chapter_05/src$ bazel run :echo_server
INFO: Analysed target //src:echo_server (0 packages loaded, 2 targets configured).
INFO: Found 1 target...
Target //src:echo_server up-to-date:
bazel-bin/src/darwin_amd64_stripped/echo_server
INFO: Elapsed time: 0.210s, Critical Path: 0.00s
INFO: 0 processes.
INFO: Build completed successfully, 1 total action
INFO: Build completed successfully, 1 total action
2019/06/02 18:50:31 Spinning up the Echo Server in Go...
```

它会一直阻塞，直到接收到连接；现在我们来提供一个连接。打开第二个 shell 并运行客户端程序：

```
chapter_05/src$ bazel run :echo_client
INFO: Analysed target //src:echo_client (0 packages loaded, 0 targets configured).
INFO: Found 1 target...
Target //src:echo_client up-to-date:
bazel-bin/src/echo_client.jar
bazel-bin/src/echo_client
INFO: Elapsed time: 0.284s, Critical Path: 0.00s
INFO: 0 processes.
INFO: Build completed successfully, 1 total action
INFO: Build completed successfully, 1 total action
Spinning up the Echo Client in Java...
Waiting on input from the user...
```

现在，输入一些你想发送的文本并按下 *回车*。在第二个 shell（客户端）中，你应该会看到类似如下内容：

```
chapter_05/src$ bazel run :echo_client

Spinning up the Echo Client in Java...
Waiting on input from the user...
Hello, friends!
Received by Java: Hello, friends!
Echoed from Go: Hello, friends!
```

客户端程序也应该已经干净地退出了。

查看第一个 shell（服务器端），你应该会看到类似如下内容：

```
chapter_05/src$ bazel run :echo_server

2019/06/02 18:50:31 Spinning up the Echo Server in Go...
2019/06/02 18:52:28 Receiving on a new connection
2019/06/02 18:53:25 Received data: Hello, friends!
2019/06/02 18:53:25 Connection now closed.
```

和客户端一样，服务器此时也应已干净退出。恭喜！你已经在 Bazel 中用不同语言创建了一个回显客户端和服务器！

## 升级到 JSON

来回发送字节串是一个不错的开始，但它并不是一种可扩展的消息传输方式。在本节中，我们将转而使用 JSON 在数据连接上传输数据。在这个过程中，我们会把过去几章中逐步建立的一些概念串联起来。

为了便于说明，我们将构造一条简单的 JSON 消息，其数据如下。

```
{
"message": "This is my message",
"value": 1234.56
}
Listing 5-10
Simple JSON message
```



### Go 中的 JSON

Go 原生提供了对 JSON 与 Go 结构体实例之间数据编组（marshal）和解组（unmarshal）的支持。首先，我们将创建一个 Go 结构体来定义传输消息。

在大多数情况下，我们只需要在 Go 中创建一个包含必要字段的普通数据结构体。唯一的小改动是，我们需要添加一些注解来指定 JSON 键到结构体特定成员的映射关系。

```
package transmission_object
type TransmissionObject struct {
Message string `json:"message"`
Value  float32  `json:"value"`
}
Listing 5-11
Simple JSON object in Go
```

在 `chapter_05/src` 目录下，将上述文件保存为 `transmission_object.go`*.*。

现在让我们在 `BUILD` 文件中创建一个 `go_library`，为该功能提供构建目标。打开 `chapter_05/src/BUILD` 文件并添加以下内容。

```
go_library(
name = "transmission_object_go",
srcs = ["transmission_object.go"],
importpath = "transmission_object",
)
Listing 5-12
Adding the TransmissionObject as a go_library
```

保存 *BUILD* 文件。

现在，让我们在你的 Go 回显服务器中添加所需功能。在这个例子中，我们将读取传入消息，对消息中的值做一些修改，然后把修改后的消息发回去。

打开 `echo_server.go` 并添加以下代码行。

```
package main
import (
"encoding/json"
"fmt"
"log"
"net"
"transmission_object"
)
func main() {

data := buffer[:size]
var transmissionObject transmission_object.TransmissionObject
error = json.Unmarshal(data, &transmissionObject)
if error != nil {
log.Panicln(
"Unable to unmarshal the buffer! Error: " +
error.Error())
}
log.Println("Message = " + transmissionObject.Message)
log.Println("Value = " + fmt.Sprintf("%f", transmissionObject.Value))
transmissionObject.Message =
"Echoed from Go: " + transmissionObject.Message
transmissionObject.Value = 2 * transmissionObject.Value
message, error := json.Marshal(transmissionObject)
if error != nil {
log.Panicln(
"Unable to marshall the object! Error: " +
error.Error())
}
connection.Write(message)
}
Listing 5-13
Unmarshaling, modifying, and marshaling a JSON object in Go
```

将更改保存到 `chapter_05/src/echo_server.go`。

最后，我们需要更新 `BUILD` 文件，以便为 `echo_server` 配置正确的依赖。打开 `BUILD` 文件并进行如下修改。

```
go_binary(
name = "echo_server",
srcs = ["echo_server.go"],
deps = [":transmission_object_go"],
)
Listing 5-14
Updating the BUILD file
```

将更改保存到 `chapter_05/src/BUILD`。

现在让我们转到 `echo_client`，进行必要的修改。

### Java 中的 JSON

Go 具备将 JSON 转换为 Go 结构体实例的内建能力；遗憾的是，Java 默认并不具备同样的开箱即用能力。幸运的是，我们可以通过使用 GSON 库获得类似行为。

#### GSON 设置

回到第 3 章，你曾下载过一个 jar 文件到 `third_party` 目录；这里我们也将再次这样做，这次是为了获取 GSON 库。

首先，创建 `third_party/gson` 目录：

```
chapter_05$ mkdir third_party
chapter_05$ cd third_party
chapter_05/third_party$ mkdir gson
```

从以下位置下载 JAR：

[`http://central.maven.org/maven2/com/google/code/gson/gson/2.8.5/gson-2.8.5.jar`](http://central.maven.org/maven2/com/google/code/gson/gson/2.8.5/gson-2.8.5.jar)。

将 JAR 文件复制到 `the third_party/gson` 目录。和之前一样，我们现在为这个外部依赖创建 `BUILD` 文件。

```
package(default_visibility = ["//visibility:public"])
java_import(
name = "gson",
jars = ["gson/gson-2.8.5.jar"]
)
Listing 5-15
Contents of the BUILD file for the third_party dependency
```

将 `BUILD` 文件保存到 `third_party` 目录。

#### 向 EchoClient 添加传输对象

现在我们将在 Java 中创建一个 `TransmissionObject`（与我们在 Go 中所做的等价），用于在线路上传输和接收结构化且有类型的信息。

```
public class TransmissionObject {
public String message;
public float value;
}
Listing 5-16
Transmission Object in Java
```

将上述代码保存到 `chapter_05/src/TransmissionObject.java`*.*。现在让我们更新 `BUILD` 文件，为这个对象新增一个 `java_library` 构建目标。

```
java_library(
name = "transmission_object_java",
srcs = ["TransmissionObject.java"],
)
Listing 5-17
Updating the BUILD file
```

将更改保存到 `chapter_05/src/BUILD`。

现在让我们把这些改动添加到 `EchoClient.java` 中，以便编组数据、发送消息，然后打印来自回显服务器的响应。

```
import com.google.gson.Gson;
import com.google.gson.GsonBuilder;

System.out.println("Waiting on input from the user...");
final String inputFromUser = commandLineInput.readLine();
if (inputFromUser != null) {
System.out.println("Received by Java: " + inputFromUser);
TransmissionObject transmissionObject =
new TransmissionObject();
transmissionObject.message = inputFromUser;
transmissionObject.value = 3.145f;
GsonBuilder builder = new GsonBuilder();
Gson gson = builder.create();
final PrintWriter outputToServer =
new PrintWriter(socketToServer.getOutputStream(), true);
outputToServer.println(gson.toJson(transmissionObject));
System.out.println(inputFromServer.readLine());
}
socketToServer.close();
}
Listing 5-18
Adding transmission to the echo client
```

将上述代码保存到 `chapter_05/src/EchoClient.java`。最后，我们现在可以将必要更新添加到 `BUILD` 文件中。

```
java_binary(
name = "echo_client",
srcs = ["EchoClient.java"],
main_class = "EchoClient",
deps = [
":transmission_object_java",
"//third_party:gson",
]
)
Listing 5-19
Updating the Java client with the new dependencies
```

将上述代码保存到 `chapter_05/src/BUILD`。

### 使用 JSON 执行 Echo 客户端/服务器

所有部分都就位后，让我们运行服务器和客户端。和前面一样，你需要两个终端实例，才能正确运行客户端和服务器。

首先，我们运行 echo 服务器：

```
chapter_05/src$ bazel run :echo_server
INFO: Analysed target //src:echo_server (0 packages loaded, 0 targets configured).
INFO: Found 1 target...
Target //src:echo_server up-to-date:
bazel-bin/src/darwin_amd64_stripped/echo_server
INFO: Elapsed time: 0.132s, Critical Path: 0.00s
INFO: 0 processes.
INFO: Build completed successfully, 1 total action
INFO: Build completed successfully, 1 total action
2019/06/04 00:27:23 Spinning up the Echo Server in Go...
```

在第二个终端实例中，我们将运行 echo 客户端：

```
chapter_05/src$ bazel run :echo_client
INFO: Analysed target //src:echo_client (0 packages loaded, 0 targets configured).
INFO: Found 1 target...
Target //src:echo_client up-to-date:
bazel-bin/src/echo_client.jar
bazel-bin/src/echo_client
INFO: Elapsed time: 0.115s, Critical Path: 0.00s
INFO: 0 processes.
INFO: Build completed successfully, 1 total action
INFO: Build completed successfully, 1 total action
Spinning up the Echo Client in Java...
Waiting on input from the user...
```

让我们添加用户输入：

```
chapter_05/src$ bazel run :echo_client

Spinning up the Echo Client in Java...
Waiting on input from the user...
My Client Message
{"message":"Echoed from Go: My Client Message","value":6.29}
```

最后，让我们看看 echo 服务器的控制台输出：

```
chapter_05/src$ bazel run :echo_server

2019/06/04 00:27:23 Spinning up the Echo Server in Go...
2019/06/04 00:29:08 Receiving on a new connection
2019/06/04 00:30:04 Message = My Client Message
2019/06/04 00:30:04 Value = 3.145000
```

恭喜！你已经成功增强了客户端和服务器程序，实现了在不同语言之间传输 JSON 数据。



## 总结：重复劳动

使用 JSON 的一个不幸现实是，我们需要为每种语言重复定义数据。一旦数据契约发生变化，就意味着每一处数据定义实例都需要修改。这个过程很容易出错，而且尽管比简单的字符串传输更好，仍然存在不少缺陷。

如前所述，Bazel 最擅长的领域之一是多语言支持；在下一章中，我们将利用这一能力，为我们的项目加入 Protocol Buffer 支持。

### 练习——Python 客户端和/或服务器

和 Go 一样，Python 也提供了一些便捷能力，可在 JSON 与 Python 对象之间进行编组/反编组。请用 Python 创建一个新的客户端或服务器。

