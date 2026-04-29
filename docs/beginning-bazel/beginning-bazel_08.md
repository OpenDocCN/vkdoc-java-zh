# 8. gRPC 与 Bazel

在第 6 章中，你已经了解了 Protocol Buffers 的用法：它能提供简洁、强类型且易于序列化的数据描述，并且可跨语言工作。作为对应，Bazel 也提供了便捷方式来依赖 Protocol Buffers。在本章中，我们将进一步探索如何使用 Protocol Buffers 来轻松定义可跨多种语言工作的 API。

Protocol Buffer 格式通过 gRPC 来定义 API，而 gRPC 是 Google 创建*远程过程调用*（RPC）的一种方式。与 Protocol Buffers 在多语言间统一数据访问的方式类似，gRPC 统一了客户端到服务器发起 RPC 的方式。

## 设置

我们将在上一章工作的基础上继续。首先，在确认已清理所有构建产物后，将上一章的全部内容复制到一个新目录中：

```
$ cd chapter_07
chapter_07$ bazel clean
chapter_07$ ls
WORKSPACE   client    proto   server
chapter_07$ cd ..
$ cp -rf chapter_07 chapter_08
$ cd chapter_08
chapter_08$
```

最后，我们需要在 `WORKSPACE` 文件中添加更多依赖。请将下面高亮的变更加入你的 `WORKSPACE` 文件。

```
load("@bazel_tools//tools/build_defs/repo:http.bzl", "http_archive")
skylib_version = "0.8.0"
http_archive(
name = "bazel_skylib",
url = "https://github.com/bazelbuild/bazel-skylib/releases/download/{}/bazel-skylib.{}.tar.gz".format(skylib_version, skylib_version),
)

http_archive(
name = "io_grpc_grpc_java",
strip_prefix = "grpc-java-1.24.0",
urls = ["https://github.com/grpc/grpc-java/archive/v1.24.0.tar.gz"],
)
load("@io_grpc_grpc_java//:repositories.bzl", "grpc_java_repositories")
grpc_java_repositories()
http_archive(
name = "bazel_gazelle",
urls = ["https://github.com/bazelbuild/bazel-gazelle/releases/download/v0.19.1/bazel-gazelle-v0.19.1.tar.gz"],
)
load("@bazel_gazelle//:deps.bzl", "gazelle_dependencies", "go_repository")
gazelle_dependencies()
go_repository(
name = "org_golang_google_grpc",
build_file_proto_mode = "disable",
importpath = "google.golang.org/grpc",
sum = "h1:J0UbZOIrCAl+fpTOf8YLs4dJo8L/owV4LYVtAXQoPkw=",
version = "v1.22.0",
)
go_repository(
name = "org_golang_x_net",
importpath = "golang.org/x/net",
sum = "h1:oWX7TPOiFAMXLq8o0ikBYfCJVlRHBcsciT5bXOrH628=",
version = "v0.0.0-20190311183353-d8887717615a",
)
go_repository(
name = "org_golang_x_text",
importpath = "golang.org/x/text",
sum = "h1:g61tztE5qeGQ89tm6NTjjM9VPIm088od1l6aSorWRWg=",
version = "v0.3.0",
)
Listing 8-1
Adding the gRPC dependencies to the WORKSPACE
```

将其保存到 `WORKSPACE` 文件中。

作为最后一项，我们将在 `WORKSPACE` 文件旁边创建一个空的 BUILD 文件。这与 Gazelle 的使用有关（下一节会讨论）。

```
chapter_08$ touch BUILD
```

### 依赖讨论

我们值得花一点时间讨论刚刚添加的一些依赖，尤其是 Go 相关依赖。到目前为止，`http_archive` 的用法应该已经很熟悉了。

#### Skylib

Skylib 库包含了许多在创建自定义构建规则时会用到的实用函数和规则。它是很多包都会使用的常见依赖。因此，当不同依赖使用该库的多个版本时，你可能会遇到问题。在这里，我们显式加入这个库，并指定一个将在你的 `WORKSPACE` 中统一使用的版本。

关于我们*如何*引入这个特定版本的 Skylib，还有一点需要注意。如果你留意会发现，我们在字符串上使用了 `.format()` 函数，以便将版本号插入到路径中。这样做可以让我们后续更容易修改版本。它也展示了 Starlark 类 Python 特性的用法，可用于更丰富地描述依赖配置。

#### Gazelle

Gazelle 的独特之处在于，它是 Bazel 项目的构建文件生成器。也就是说，它可以从代码中“自动神奇地”生成某种语言的 BUILD 文件（当然，前提是代码本身结构良好）。Gazelle 开箱即支持 Go。由于 Go 具有一个很好的特性——能够在代码中非常明确地声明依赖——Gazelle 可以利用这一点为你生成 `BUILD` 文件。

Gazelle 还为 Go 定义了一个仓库规则，名字也很贴切：`go_repository`。你可能已经猜到了，go_repository 允许你（a）指定用于获取所需依赖的 import 路径，以及（b）为其中的包自动生成所需的 `BUILD` 文件。可以想象，这对于集成尚未原生支持 Bazel 的第三方库（至少对 Go 而言）会有巨大帮助。

项目根目录下的 `BUILD` 文件可作为配置 Gazelle 选项的位置，如果你想在自己的项目中使用它的功能的话。如果缺少这个 `BUILD` 文件，你的项目将构建失败。



## 在 Protocol Buffers 中定义 gRPC

与我们此前以与语言无关的方式在 Protocol Buffers 中定义消息类似，我们也可以为 RPC 定义接口。我们将在 `proto` 目录中新建一个文件来承载这个新 API。

```
syntax = "proto3";
import "proto/transmission_object.proto";
package transceiver;
message EchoRequest {
transmission_object.TransmissionObject from_client = 1;
}
message EchoResponse {
transmission_object.TransmissionObject from_server = 1;
}
service Transceiver {
rpc Echo (EchoRequest) returns (EchoResponse);
}
Listing 8-2
Defining the interface for the RPC
```

将其保存为 `proto/transceiver.proto`*。*

让我们花一点时间来分析这些定义。我们在本地定义了两个消息：`EchoRequest` 和 `EchoResponse`，分别用于承载 RPC 的请求和响应。

值得注意的是，在 `EchoRequest` 和 `EchoResponse` 中，我们都包含了先前创建的 `TransmissionObject` 消息。严格来说，请求和响应中包含同一个消息并非必需；我们在这里这样做只是为了与前面章节中创建的功能保持一致。另外，也没有要求必须在与接口相同的文件中定义请求和响应消息；我们这样做只是为了简化示例。

随后我们定义了一个服务 `Transceiver`，其中只有一个 RPC：*Echo*。在 Echo 的定义中，我们指定了所需的请求和响应类型。

最后请注意，我们在这里所做的只是为 RPC 定义接口。除此之外，（这里）并没有定义它的实现。

为了真正使用并生成 RPC，我们需要在 `proto/BUIL`*D* 文件中更新相应的构建目标。打开 `proto/BUILD` 文件并添加以下内容。

```
proto_library(
name = "transceiver_proto",
srcs = ["transceiver.proto"],
deps = [
":transmission_object_proto",
]
)
go_proto_library(
name = "transceiver_go_proto_grpc",
compiler = "@io_bazel_rules_go//proto:go_grpc",
proto = ":transceiver_proto",
importpath = "transceiver",
deps = [":transmission_object_go_proto",],
visibility = ["//server/echo_server:__pkg__"],
)
java_proto_library(
name = "transceiver_java_proto",
deps = [":transceiver_proto"],
visibility = ["//client/echo_client:__subpackages__"],
)
load("@io_grpc_grpc_java//:java_grpc_library.bzl", "java_grpc_library")
java_grpc_library(
name = "transceiver_java_proto_grpc",
srcs = [":transceiver_proto"],
deps = [":transceiver_java_proto"],
visibility = ["//client/echo_client:__subpackages__"],
)
Listing 8-3
Defining the build targets for the Transceiver service
```

将文件保存到 `proto/BUILD`。

和 proto 代码一样，我们来看看这里新增了什么。第一个新构建目标看起来应该很熟悉；它只是为 `transceiver.proto` 文件定义了 `proto_library` 目标。类似地，`transceiver_java_proto` 也应该很熟悉，因为它定义了 `transceiver.proto` 的 Java 版本构建目标*。*

`transceiver_go_proto_grpc` 与我们之前看到的内容非常相似；主要区别是在 `go_proto_library` 目标中新增了 *compiler* 指令。它定义了在编译该目标以支持 gRPC 时应使用的规则。我们使用的是 `@io_bazel_rules_go` 依赖中的标准规则。

由于我们只在服务端使用 Go，因此将可见性设置为仅服务端子包可见。

`java_grpc_library` 执行的是类似工作，不过它定义的是 Java 所需的目标。与之对应地，我们将可见性设置为仅客户端子包可见。

为了确认一切正常，让我们构建该包中的所有目标：

```
chapter_08$ bazel build proto:all
INFO: Analysed 7 targets (0 packages loaded, 0 targets configured).
INFO: Found 7 targets...
INFO: Elapsed time: 0.316s, Critical Path: 0.07s
INFO: 3 processes: 3 darwin-sandbox.
INFO: Build completed successfully, 4 total actions
```

## 将客户端升级为使用 gRPC

在定义好 gRPC 接口之后，我们现在将客户端升级为使用它。总体而言，代码看起来会与我们之前做的非常相似。我们将重点说明基础版本所需的一些变更。

我们之前创建了一个显式传输序列化对象的 Java 客户端。现在我们将做一些修改以支持 gRPC。

打开 `client/echo_client/command_line/EchoClient.java`。

```
import io.grpc.ManagedChannel;
import io.grpc.ManagedChannelBuilder;
import java.io.BufferedReader;
import java.io.InputStreamReader;
import transmission_object.TransmissionObjectOuterClass.TransmissionObject;
import transceiver.TransceiverOuterClass.EchoRequest;
import transceiver.TransceiverOuterClass.EchoResponse;
import transceiver.TransceiverGrpc;
public class EchoClient {
public static void main(String args[]) {
System.out.println("Spinning up the Echo Client in Java...");
try {
final BufferedReader commandLineInput = new BufferedReader(new InputStreamReader(System.in));
System.out.println("Waiting on input from the user...");
final String inputFromUser = commandLineInput.readLine();
if (inputFromUser != null) {
ManagedChannel channel =
ManagedChannelBuilder
.forAddress("localhost", 1234)
.usePlaintext()
.build();
TransceiverGrpc.TransceiverBlockingStub stub =
TransceiverGrpc.newBlockingStub(channel);
EchoRequest request =  EchoRequest.newBuilder()
.setFromClient(
TransmissionObject.newBuilder()
.setMessage(inputFromUser)
.setValue(3.145f)
.build())
.build();
EchoResponse response = stub.echo(request);
System.out.println("Received Message from server: ");
System.out.println(response);
channel.shutdownNow();
}
} catch (Exception e) {
System.err.println("Error: " + e);
}
}
}
Listing 8-4
Using gRPC on the client side
```

将文件保存到 `client/echo_client/command_line/EchoClient.java`。

再花一点时间审视这些改动：我们首先创建一个 `ManagedChannel`，以在特定端口上打开通道。然后使用它来创建一个 stub（`TransceiverBlockingStub`*）*，用于实际发起 RPC。为简化起见，我们使用最基础的 stub，它会对该服务的所有 RPC 发起阻塞调用（stub 还有其他更灵活的版本，但不在本书讨论范围内）。

stub 提供了本地方法，这些方法会通过通道转发调用。一旦创建完成，调用 RPC 就和调用本地方法一样简单。构造请求后，将其用于调用相应方法，并获得预期响应。

为完成功能，让我们在 `client/echo_client/command_line/BUILD` 文件中升级目标。与之前一样，原始目标的大部分内容保持不变，因此我们重点展示必要改动。

```
java_binary(
name = "command_line",
srcs = ["EchoClient.java"],
main_class = "EchoClient",
runtime_deps = [
"@io_grpc_grpc_java//netty",
],
deps = [
"//proto:transmission_object_java_proto",
"//proto:transceiver_java_proto",
"//proto:transceiver_java_proto_grpc",
"@io_grpc_grpc_java//api",
]
)
Listing 8-5
Modifications for EchoClient to support gRPC
```

将文件保存到 *client/echo_client/command_line/BUILD*。

正如此时你可能已经很清楚的那样，我们新增了来自 *proto* 子包的新依赖。此外，我们还添加了一个常规静态依赖 `(@io_grpc_grpc_java//api`) 以及一个新的运行时依赖 `(@io_grpc_grpc_java//netty`)。

在后者这种情况下，我们将其指定为运行时依赖，因为代码中并没有显式请求它。如果你尝试移除 *runtime_deps*，会发现程序构建仍然完全正常。然而，一旦尝试运行程序，就会出现错误信息，提示需要该运行时依赖。

我们将构建该目标以确认一切正常：



```
chapter_08$ bazel build client/echo_client/command_line
INFO: Analysed target //client/echo_client/command_line:command_line (32 packages loaded, 317 targets configured).
INFO: Found 1 target...
Target //client/echo_client/command_line:command_line up-to-date:
bazel-bin/client/echo_client/command_line/command_line.jar
bazel-bin/client/echo_client/command_line/command_line
INFO: Elapsed time: 6.648s, Critical Path: 6.14s
INFO: 21 processes: 18 darwin-sandbox, 3 worker.
INFO: Build completed successfully, 25 total actions
```

值得注意的是，我们现在还不能真正运行客户端；尽管我们已经成功创建了一个使用 gRPC 的客户端，但我们仍然需要一个实现该 RPC 的服务。

## 将服务器升级为使用 gRPC

要将我们的服务器升级为使用 gRPC，我们需要为 RPC 注册并补全其功能实现。gRPC 会为我们生成大部分脚手架代码；我们只需要注册服务器并提供相应的方法来履行契约。

与前一节一样，我们可以在现有代码上做一些修改。需要注意的是，这一次为了支持 gRPC，我们将进行更大范围的改动。

打开 `server/echo_server/echo_server.go` 并进行高亮部分所示的修改。

```
package main
import (
"fmt"
"log"
"net"
"transceiver"
"transmission_object"
"golang.org/x/net/context"
"google.golang.org/grpc"
)
type EchoServer struct{}
func (es *EchoServer) Echo(context context.Context, request *transceiver.EchoRequest) (*transceiver.EchoResponse, error) {
log.Println("Message = " + (*request).FromClient.GetMessage())
log.Println("Value = " +
fmt.Sprintf("%f", (*request).FromClient.GetValue()))
server_message := "Received from client: " +
(*request).FromClient.GetMessage()
server_value := (*request).FromClient.Value * 2
from_server := transmission_object.TransmissionObject{
Message: server_message,
Value:   server_value,
}
return &transceiver.EchoResponse{
FromServer: &from_server,
}, nil
}
func main() {
log.Println("Spinning up the Echo Server in Go...")
listen, error := net.Listen("tcp", ":1234")
if error != nil {
log.Panicln("Unable to listen: " + error.Error())
}
defer listen.Close()
defer log.Println("Connection now closed.")
grpc_server := grpc.NewServer()
transceiver.RegisterTransceiverServer(grpc_server, &EchoServer{})
error = grpc_server.Serve(listen)
if error != nil {
log.Panicln("Unable to start serving! Error: " + error.Error())
}
}
Listing 8-6
Implementing the gRPC interface on the server side
```

保存对 `server/echo_server/echo_server.go` 的修改。

再次退一步，看看主要变更。在这个例子中，你创建了一个 Go 结构体 `EchoServer`，它有一个特殊命名的方法 `Echo`。

`Echo` 方法接收一个 `Context` 对象和我们在 protobuf 定义中定义的 `EchoRequest`，并返回 `EchoResponse` 对象。这个方法满足了接口定义所要求的契约。就功能而言，虽然方法体与之前版本略有不同，但你应该能识别出相同的功能逻辑。

```
Within the body of main, we created a new server and registered our EchoServer. Having completed the setup, we then just start listening to incoming messages.
```

现在让我们完成 `BUILD` 文件中目标的升级，以支持这项新功能。打开 `server/echo_server/BUILD` 并进行如下高亮修改。

```
load("@io_bazel_rules_go//go:def.bzl", "go_binary")
go_binary(
name = "echo_server",
srcs = ["echo_server.go"],
deps = [
"//proto:transceiver_go_proto_grpc",
"//proto:transmission_object_go_proto",
"@org_golang_x_net//context:go_default_library",
"@org_golang_google_grpc//:go_default_library",
]
)
Listing 8-7
Modification for echo_server to support gRPC
```

保存你对 `server/echo_server/BUILD` 的修改。

和之前一样，新的 `org_golang_*` 依赖并非凭空产生；它们来自我们在 `WORKSPACE` 文件中定义的 Go 依赖。

现在，让我们做一次构建健全性检查，确保一切都按预期工作：

```
chapter_08$ bazel build server/echo_server
INFO: Analysed target //server/echo_server:echo_server (0 packages loaded, 0 targets configured).
INFO: Found 1 target...
Target //server/echo_server:echo_server up-to-date:
bazel-bin/server/echo_server/darwin_amd64_stripped/echo_server
INFO: Elapsed time: 0.227s, Critical Path: 0.00s
INFO: 0 processes.
INFO: Build completed successfully, 1 total action
```

## 运行客户端和服务器

客户端和服务器都完成后，我们就可以真正使用这项新功能了。

打开一个新的终端窗口。我们先运行服务器。

```
chapter_08$ bazel run server/echo_server
INFO: Analysed target //server/echo_server:echo_server (83 packages loaded, 7969 targets configured).
INFO: Found 1 target...
Target //server/echo_server:echo_server up-to-date:
bazel-bin/server/echo_server/darwin_amd64_stripped/echo_server
INFO: Elapsed time: 110.524s, Critical Path: 27.33s
INFO: 466 processes: 466 darwin-sandbox.
INFO: Build completed successfully, 467 total actions
INFO: Build completed successfully, 467 total actions
2019/07/23 05:11:30 Spinning up the Echo Server in Go...
```

服务器开始监听后，让我们运行客户端来发送一次请求。打开第二个终端窗口并运行以下命令，在末尾输入一些内容：

```
chapter_08$ bazel run client/echo_client/command_line
INFO: Analysed target //client/echo_client/command_line:command_line (60 packages loaded, 919 targets configured).
INFO: Found 1 target...
Target //client/echo_client/command_line:command_line up-to-date:
bazel-bin/client/echo_client/command_line/command_line.jar
bazel-bin/client/echo_client/command_line/command_line
INFO: Elapsed time: 5.728s, Critical Path: 5.33s
INFO: 3 processes: 2 darwin-sandbox, 1 worker.
INFO: Build completed successfully, 4 total actions
INFO: Build completed successfully, 4 total actions
Spinning up the Echo Client in Java...
Waiting on input from the user...
This is a test using gRPC.
Received Message from server:
from_server {
value: 6.29
message: "Received from client: This is a test using gRPC."
}
```

我们已经成功使用 gRPC 实现了 echo 功能。不过，让我们回到服务器终端看看那边的消息：

```
chapter_08$ bazel run server/echo_server

INFO: Build completed successfully, 1 total action
2019/07/23 05:11:49 Spinning up the Echo Server in Go...
2019/07/23 05:15:02 Message = This is a test using gRPC.
2019/07/23 05:15:02 Value = 3.145000
```

和你之前的实现一样，你可以看到客户端发送过来的消息。不过这里在功能上有一个重要区别：服务器没有退出。也就是说，服务器仍在运行并等待新的连接到来。

为了验证这一点，切回客户端终端，再运行一次：

```
chapter_08$ bazel run client/echo_client/command_line

Spinning up the Echo Client in Java...
Waiting on input from the user...
Still up and running
Received Message from server:
from_server {
value: 6.29
message: "Received from client: Still up and running"
}
```

现在让我们回到服务器终端查看消息：

```
chapter_08$ bazel run server/echo_server

2019/07/23 05:11:49 Spinning up the Echo Server in Go...
2019/07/23 05:15:02 Message = This is a test using gRPC.
2019/07/23 05:15:02 Value = 3.145000
2019/07/23 05:21:20 Message = Still up and running
2019/07/23 05:21:20 Value = 3.145000
```

这种持久化功能是 gRPC “开箱即用”就提供的。切换到它之后，我们实际上以大致相同的代码行数获得了更多功能。



## 添加另一个 RPC

一个可能出现的抱怨是：你为了执行与之前相同的操作，几乎写了差不多同样数量的代码。为了进一步说明我们所创建内容的强大之处，我们将很快在整个系统中再添加一个 RPC。

打开 `proto/transceiver.proto` 并添加以下高亮行。

```
syntax = "proto3";
import "proto/transmission_object.proto";
package transceiver;
message EchoRequest {
transmission_object.TransmissionObject from_client = 1;
}
message EchoResponse {
transmission_object.TransmissionObject from_server = 1;
}
message UpperCaseRequest {
string original = 1;
}
message UpperCaseResponse {
string upper_cased = 1;
}
service Transceiver {
rpc Echo (EchoRequest) returns (EchoResponse);
rpc UpperCase (UpperCaseRequest) returns (UpperCaseResponse);
}
Listing 8-8
Adding the interface for another RPC
```

将文件保存到 `proto/transceiver.proto`*。*

请注意，所需的全部操作仅仅是将新的 RPC 声明添加到 service 中，同时为请求与响应补充一些明确的消息定义。

现在我们把实现添加到服务器中。打开 `server/echo_server/echo_server.go`。

```
import (
"fmt"
"log"
"net"
"strings"
"transceiver"
"transmission_object"
"golang.org/x/net/context"
"google.golang.org/grpc"
)

func (es *EchoServer) UpperCase(contest context.Context, request *transceiver.UpperCaseRequest) (*transceiver.UpperCaseResponse, error) {
log.Println("Original = " + (*request).GetOriginal())
return &transceiver.UpperCaseResponse{
UpperCased: strings.ToUpper((*request).GetOriginal()),
}, nil
}
func main() {
log.Println("Spinning up the Echo Server in Go...")
listen, error := net.Listen("tcp", ":1234")
if error != nil {
log.Panicln("Unable to listen: " + error.Error())
}
defer listen.Close()
defer log.Println("Connection now closed.")
grpc_server := grpc.NewServer()
transceiver.RegisterTransceiverServer(grpc_server, &EchoServer{})
error = grpc_server.Serve(listen)
if error != nil {
log.Panicln("Unable to start serving! Error: " + error.Error())
}
}
Listing 8-9
Adding the implementation to the server
```

将修改保存到 `server/echo_server/echo_server.go`*。*

再次注意，所需的全部工作只是向该结构体中添加新方法。

最后，我们来进行客户端侧所需的修改。打开 `client/echo_client/command_line/EchoClient.java`。

```
import io.grpc.ManagedChannel;
import io.grpc.ManagedChannelBuilder;
import java.io.BufferedReader;
import java.io.InputStreamReader;
import transmission_object.TransmissionObjectOuterClass.TransmissionObject;
import transceiver.TransceiverOuterClass.EchoRequest;
import transceiver.TransceiverOuterClass.EchoResponse;
import transceiver.TransceiverOuterClass.UpperCaseRequest;
import transceiver.TransceiverOuterClass.UpperCaseResponse;
import transceiver.TransceiverGrpc;
public class EchoClient {
public static void main(String args[]) {
System.out.println("Spinning up the Echo Client in Java...");
try {

UpperCaseRequest upperCaseRequest =
UpperCaseRequest.newBuilder()
.setOriginal(inputFromUser)
.build();
UpperCaseResponse upperCaseResponse =
stub.upperCase(upperCaseRequest);
System.out.println("Received upper cased:");
System.out.println(upperCaseResponse);
channel.shutdownNow();
}
} catch (Exception e) {
System.err.println("Error: " + e);
}
}
}
Listing 8-10
Calling the new RPC from the client
```

将你的修改保存到 `client/echo_client/command_line/EchoClient.java`。

再次说明，所需内容依然很简单：创建合适的构造并调用新定义的 RPC。让我们运行一次测试来验证一切正常。

打开终端并启动服务器：

```
chapter_08$ bazel run server/echo_server
INFO: Analysed target //server/echo_server:echo_server (1 packages loaded, 594 targets configured).
INFO: Found 1 target...
Target //server/echo_server:echo_server up-to-date:
bazel-bin/server/echo_server/darwin_amd64_stripped/echo_server
INFO: Elapsed time: 1.409s, Critical Path: 0.03s
INFO: 0 processes.
INFO: Build completed successfully, 1 total action
INFO: Build completed successfully, 1 total action
2019/07/23 05:40:16 Spinning up the Echo Server in Go...
```

现在打开客户端终端，再运行一次客户端：

```
chapter_08$ bazel run client/echo_client/command_line
INFO: Analysed target //client/echo_client/command_line:command_line (2 packages loaded, 432 targets configured).
INFO: Found 1 target...
Target //client/echo_client/command_line:command_line up-to-date:
bazel-bin/client/echo_client/command_line/command_line.jar
bazel-bin/client/echo_client/command_line/command_line
INFO: Elapsed time: 0.616s, Critical Path: 0.27s
INFO: 1 process: 1 worker.
INFO: Build completed successfully, 2 total actions
INFO: Build completed successfully, 2 total actions
Spinning up the Echo Client in Java...
Waiting on input from the user...
This is the magic.
Received Message from server:
from_server {
value: 6.29
message: "Received from client: This is the magic."
}
Received upper cased:
upper_cased: "THIS IS THE MAGIC."
```

恭喜！你刚刚在系统中添加了一个新的 RPC。值得注意的是，这仅仅要求你在每个位置（定义、服务器和客户端）增加少量代码。接下来，你可以用这种非常有条理且易于管理的方式，轻松定义更多功能。

### 注意

在上一节的整个过程中，我们完全没有对 `BUILD` 文件做*任何*修改，因为并不需要变更依赖。你只需专注于代码并执行，且可以有把握地相信 Bazel 会处理必要的构建步骤。

## 最后的话

在上一章中，重点与其说是 Bazel 使用方式的结构化知识，不如说是以更贴近真实开发的方式来使用它。你已经看到 Bazel 的多语言支持如何与 gRPC 协同工作，从而轻松创建新的客户端—服务器功能。

到目前为止，本书的重点主要放在实际上运行于后端的功能上。也就是说，迄今创建的客户端和服务器都很容易作为后端服务彼此通信的代理。

尽管后续仍将继续围绕“通信”这一主题展开，但我们将开始关注如何使用 Bazel 构建纯客户端功能，即移动应用。我们将再次大量依赖 Bazel 跨语言无缝协作的能力来创建这些应用。

