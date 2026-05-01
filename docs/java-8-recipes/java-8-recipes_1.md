# 连接 myserver.org 上的 HTTP 服务器或数据库时使用 SDP

connect myserver.org 8080

connect myserver.org 1521

以下摘录自终端。它展示了一个名为 SDPExample 的 Java 应用程序的执行过程，其中指定了 SDP 系统属性：

% java -Dcom.sun.sdp.conf=sdp.conf -Djava.net.preferIPv4Stack=true SDPExample

工作原理

有时，应用程序在执行网络通信时，速度越快越好，这一点至关重要。

通过 TCP 进行传输有时会降低性能，因此绕过 TCP 可能是有益的。自 Java SE 7 发布以来，某些平台已包含对 SDP 的支持。SDP 支持通过 InfiniBand 架构进行流连接。Solaris 和 Linux 都包含对 InfiniBand 的支持，因此 SDP 在这些平台上可能很有用。

你无需对应用程序进行任何程序上的更改即可支持 SDP。使用 SDP 的唯一区别在于，你必须创建一个 SDP 配置文件，并且在运行应用程序时，必须通过传递一个标志来告知 JVM 使用该协议。由于实现是透明的，因此应用程序可以为任何平台编写，而那些支持 SDP 的平台只需包含配置文件即可绕过 TCP。

SDP 配置文件是一个文本文件，由绑定规则和连接规则组成。绑定规则表示，当 TCP 套接字绑定到与给定规则匹配的地址和端口时，应使用 SDP 协议传输。

连接规则表示，当未绑定的 TCP 套接字尝试连接到与给定规则匹配的地址和端口时，应使用 SDP 协议传输。规则以 `bind` 或 `connect` 关键字开头，指示规则类型，后跟主机名或 IP 地址，以及单个端口号或端口号范围。

根据在线文档，规则具有以下形式：

("bind"|"connect")1*LWSP-char(hostname|ipaddress)["/"prefix])1*LWSP-char("*"|port)

["-"("*"|port)]

在此处显示的规则格式中，`1*LWSP-char` 表示任意数量的制表符或空格可以分隔这些标记。

方括号内的任何内容表示可选文本，引号表示字面文本。在配方的解决方案中，第一条规则表示 SDP 可用于 IP 地址 192.0.2.1（一个本地地址）上的任何端口（`*` 表示通配符）。配置文件中应为分配给 InfiniBand 适配器的每个本地地址指定一条绑定规则。配置文件中的第一条连接规则指定，每当连接到 IP 地址 192.0.2.* 且使用端口 1024 或更高时，应使用 SDP。

connect 192.0.2.0/24 1024-*

此规则使用了一些需要注意的特殊语法。具体来说，IP 地址的 `/24` 后缀表示 32 位 IP 地址的前 24 位应与指定地址匹配。由于 IP 地址的每个部分都是 8 位，这意味着 192.0.2 必须完全匹配，而最后一个字节可以是任何值。端口标识符中的 `-*` 指定了 1024 或更大的范围，因为使用了通配符。配置文件中的第三条和第四条连接规则指定，SDP 应与主机名 [myserver.org](http://myserver.org) 以及端口 8080 或 1521 一起使用。

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 21 章 网络编程

接下来，为了启用 SDP，在启动应用程序时，应指定 `-Dcom.sun.sdp.conf` 属性以及 SDP 配置文件的路径。另外，请注意在解决方案中，属性 `-Djava.net.preferIPv4Stack` 被设置为 `true`。这表明将使用 IPv4 地址格式。这是必要的，因为映射到 IPv6 的 IPv4 地址目前在 Solaris OS 或 Linux 下尚不可用。

尽管 SDP 仅适用于 Solaris 或 Linux，但对于这些平台的用户来说，它是 JDK 的一个不错的补充。任何性能提升器都被视为一种奖励，而本配方的解决方案无疑属于这一类。

21-4. 向一组接收者广播

问题

你希望向由单个地址标识的零个或多个主机广播数据报。

解决方案

使用 `DatagramChannel` 类进行数据报多播。`DatagramChannel` 类允许多个客户端连接到一个组，并监听从服务器广播的数据报。以下代码集使用客户端/服务器方法演示了此技术。该类演示了一个多播客户端。

package org.java8recipes.chapter21.recipe21_4;

import java.io.IOException;

import java.net.InetAddress;

import java.net.InetSocketAddress;

import java.net.NetworkInterface;

import java.net.StandardProtocolFamily;

import java.net.StandardSocketOptions;

import java.nio.ByteBuffer;

import java.nio.channels.DatagramChannel;

import java.nio.channels.MembershipKey;

public class MulticastClient {

public MulticastClient() {

}

public static void main(String[] args) {

try {

// 获取支持的网络接口

NetworkInterface networkInterface = null;

java.util.Enumeration<NetworkInterface> enumNI = NetworkInterface.

getNetworkInterfaces();

java.util.Enumeration<InetAddress> enumIA;

NetworkInterface ni;

InetAddress ia;

ILOOP:

while (enumNI.hasMoreElements()) {

ni = enumNI.nextElement();

enumIA = ni.getInetAddresses();

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 21 章 网络编程

while (enumIA.hasMoreElements()) {

ia = enumIA.nextElement();

if (ni.isUp() && ni.supportsMulticast()

&& !ni.isVirtual() && !ni.isLoopback()

&& !ia.isSiteLocalAddress()) {

networkInterface = ni;

break ILOOP;

}

}

}

// 范围内的地址

int port = 5239;

InetAddress group = InetAddress.getByName("226.18.84.25");

final DatagramChannel client = DatagramChannel.open(StandardProtocolFamily.INET);

client.setOption(StandardSocketOptions.SO_REUSEADDR, true);

client.bind(new InetSocketAddress(port));

client.setOption(StandardSocketOptions.IP_MULTICAST_IF, networkInterface);

System.out.println("正在加入组: " + group + " 使用网络接口 " +

networkInterface);

// 多播加入

MembershipKey key = client.join(group, networkInterface);

client.open();

// 作为客户端接收消息

final ByteBuffer buffer = ByteBuffer.allocateDirect(4096);

buffer.clear();

System.out.println("等待接收消息");

// 将客户端配置为被动和非阻塞

// client.configureBlocking(false);

client.receive(buffer);

System.out.println("客户端收到消息:");

buffer.flip();

byte[] arr = new byte[buffer.remaining()];

buffer.get(arr, 0, arr.length);

System.out.println(new String(arr));

System.out.println("正在断开连接...仅执行单次测试");

client.disconnect();

} catch (IOException ex) {

ex.printStackTrace();

}

}

}

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 21 章 网络编程

接下来，可以使用一个服务器类向多播客户端连接的地址广播数据报。

以下代码演示了一个多播服务器：

package org.java8recipes.chapter21.recipe21_4;

import java.io.IOException;

import java.net.InetAddress;

import java.net.InetSocketAddress;

import java.nio.ByteBuffer;

import java.nio.channels.DatagramChannel;

public class MulticastServer extends Thread {

protected ByteBuffer message = null;

public MulticastServer() {

}

public static void main(String[] args) {

MulticastServer server = new MulticastServer();



server.start();

}

@Override

public void run() {

try {

// 向“address”和“port”指定的客户端发送响应

InetAddress address = InetAddress.getByName("226.18.84.25");

int port = 5239;

DatagramChannel server = DatagramChannel.open().bind(null);

System.out.println("正在向组 " + address + " 的端口 " + port + " 发送数据报包"); message = ByteBuffer.wrap("Hello to all listeners".getBytes());

server.send(message, new InetSocketAddress(address, port));

server.disconnect();

} catch (IOException e) {

e.printStackTrace();

}

}

}

服务器可以向组内的每个客户端广播消息。客户端应先启动，然后启动服务器。服务器启动后，将广播消息，客户端将接收该消息。

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 21 章 网络编程

工作原理

多播是指通过单次传输向一组监听者广播消息的能力。广播的一个很好的类比是无线电广播。成千上万的人可以收听同一个广播事件，听到相同的消息。

计算机在向监听者发送消息时也可以做类似的事情。一组客户端机器可以监听相同的地址和端口号，以接收服务器向该地址和端口广播的消息。Java 语言通过数据报消息传递提供多播功能。数据报是独立的、不保证送达的消息，可以通过网络传递给客户端。（*不保证送达*意味着消息的到达、到达时间和内容都是不可预测的。）与通过 TCP 发送的消息不同，发送数据报是一个非阻塞事件，发送者不会收到消息已接收的通知。数据报使用用户数据报协议（UDP）而非 TCP 发送。只要消息的顺序、可靠性和数据完整性不是关键任务，通过 UDP 发送多播消息的能力是相对于 TCP 的一个优势。

Java 通过`MulticastChannel`接口实现多播消息传递。实现`MulticastChannel`接口的类启用了多播功能，因此可以向组广播并接收组广播。其中一个类是`DatagramChannel`，它是一个面向数据报套接字的可选择通道。在本解决方案中，客户端和服务器程序都通过多播消息传递进行通信，并且通信双方都使用了`DatagramChannel`类。如果要将`DatagramChannel`用于接收多播消息，则必须以特定方式配置它。具体来说，需要在打开的`DatagramChannel`客户端上设置一些选项。我们稍后将讨论这些选项。创建接收多播消息的客户端需要以下步骤。

1. 打开一个`DatagramChannel`。
2. 设置多播所需的`DatagramChannel`选项。
3. 将客户端加入多播组并返回一个`MembershipKey`对象。
4. 打开客户端。

在本解决方案中，客户端应用程序首先获取用于接收广播消息的网络接口的引用。多播需要设置`NetworkInterface`。

接下来，选择一个端口号以及一个多播 IP 地址。组或已注册的监听者将使用该 IP 地址来监听广播。端口号必须未被占用，否则将抛出异常。

对于 IPv4 多播，IP 地址范围必须从 224.0.0.0 到 239.255.255.255（含）。此端口和 IP 地址与服务器广播消息时使用的相同。接下来，使用`StandardProtocolFamily.INET`打开一个新的`DatagramChannel`。打开`DatagramChannel`的选项有`StandardProtocolFamily.INET`或`StandardProtocolFamily.INET6`，分别对应 IPv4 和 IPv6。在`DatagramChannel`上设置的第一个选项是`StandardSocketOptions.SO_REUSEADDR`，并将其设置为`true`。这表明多个客户端将能够“重用”该地址或同时使用它。要实现多播，必须设置此选项。然后，使用一个新的`InetSocketAddress`实例将客户端绑定到该端口。最后，将`StandardSocketOptions.IP_MULTICAST_IF`选项设置为所使用的网络接口。此选项表示面向数据报的套接字发送的多播数据报的出站接口。

client.setOption(StandardSocketOptions.SO_REUSEADDR, true);

client.bind(new InetSocketAddress(port));

client.setOption(StandardSocketOptions.IP_MULTICAST_IF, networkInterface);

一旦设置了这些选项并将端口绑定到`DatagramChannel`，它就可以加入监听者组了。这可以通过调用`DatagramChannel.join(InetAddress, NetworkInterface)`方法，并传入客户端将使用的组地址和网络接口来实现。结果会生成一个`java.nio.channels.MembershipKey`对象，该对象是 IP 多播组成员身份的令牌。最后，调用`DatagramChannel.open()`方法，打开通道以监听广播。此时，客户端已准备好接收多播消息，并等待接收消息。

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 21 章 网络编程

MembershipKey key = client.join(group, networkInterface);

client.open();

客户端中的后续代码负责接收来自服务器的消息。为了接收广播消息，需要创建一个`ByteBuffer`，然后将其传递给`DatagramChannel`的`receive()`方法。一旦调用了`receive()`方法，客户端将暂停，直到收到消息。你可以通过调用`DatagramChannel.configureBlocking(boolean)`方法并传入`false`值来禁用此功能。接下来，将`ByteBuffer`转换为字符串值并打印出来，方法是使用`flip()`方法将缓冲区索引重置为 0，然后将从索引 0 到最后一个索引的文本提取到`byte[]`中。最后，确保在完成后断开客户端连接。客户端代码部分到此结束。

// 将客户端配置为被动且非阻塞

// client.configureBlocking(false);

client.receive(buffer);

// 客户端暂停，直到收到消息...在此情况下

System.out.println("客户端收到消息：");

buffer.flip();

byte[] arr = new byte[buffer.remaining()];

buffer.get(arr, 0, arr.length);

System.out.println(new String(arr));

System.out.println("正在断开连接...仅执行单次测试");

client.disconnect();

N **注意** 在本示例中，仅执行单次传递，然后客户端断开连接。如需长时间监听，你需要一个带有超时和结束状态测试的循环。

服务器代码相当基础。可以看到`MulticastServer`类继承了`Thread`。这意味着此服务器应用程序可以在应用程序内其他代码的独立线程中运行。如果有其他类启动了`MulticastServer`类的`run()`方法，它将在启动它的类的独立线程中运行。任何继承`Thread`的类都必须包含`run()`方法。有关线程和并发的更多信息，请参考第 10 章。



服务器代码的主体部分位于 `run()` 方法中。首先创建一个新的 `InetAddress` 对象，该对象使用客户端为加入多播组而注册的相同 IP 地址。服务器代码中也声明了相同的端口号，这两个对象将在后续的代码块中用于发送消息。接着，打开一个新的 `DatagramChannel` 并将其绑定到 `null`。`null` 值很重要，因为将 `SocketAddress` 设置为 `null` 后，套接字将绑定到一个自动分配的地址。然后，创建一个包含消息的 `ByteBuffer`，该消息将广播给所有监听者。消息通过 `DatagramChannel` 的 `send(ByteBuffer, InetSocketAddress)` 方法发送。解决方案中的 `send()` 方法接受一个 `ByteBuffer` 对象作为消息，以及一个使用代码块开头声明的地址和端口创建的新的 `InetSocketAddress`。之前说过我们还会用到它们！

`server.send(message, new InetSocketAddress(address, port));`

此时，客户端将接收到服务器发送的消息。至于本方案中演示的客户端，它随后会断开连接。通常在真实场景中，会由另一个类来启动服务器，其 `run()` 方法会包含一个循环，持续执行直到所有消息广播完毕或循环被要求停止。客户端通常不会断开连接，除非用户发起关闭操作。

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 21 章 网络编程

21-5. 生成并读取 URL

问题

你希望在应用程序中以编程方式生成 URL。创建 URL 后，你希望从中读取数据以供应用程序使用。

解决方案

使用 `java.net.URL` 类来创建 URL。根据你要处理的地址不同，有几种不同的生成 URL 的方式。本方案演示了几种创建 `URL` 对象的不同方法，并附有注释说明其差异。创建 `URL` 对象后，其中一个 URL 被读入 `BufferedReader` 并打印到命令行。

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.net.MalformedURLException;
import java.net.URL;

public class GenerateAndReadUrl {
    public static void main(String[] args) {
        try {
            // 生成绝对 URL
            URL url1 = new URL("http://www.java.net");
            System.out.println(url1.toString());
            // 为具有公共基础的页面生成 URL
            URL url2 = new URL(url1, "search/node/jdk8");
            // 从不同数据片段生成 URL
            URL url3 = new URL("http", "java.net", "search/node/jdk8");
            readFromUrl(url1);
        } catch (MalformedURLException ex) {
            ex.printStackTrace();
        }
    }

    /**
     * 将 URL 流作为输入流打开，并将内容打印到命令行。
     *
     * @param url
     */
    public static void readFromUrl(URL url) {
        try {
            BufferedReader in = new BufferedReader(
                    new InputStreamReader(
                            url.openStream()));
            [www.it-ebooks.info](http://www.it-ebooks.info/)
            第 21 章 网络编程
            String inputLine;
            while ((inputLine = in.readLine()) != null) {
                System.out.println(inputLine);
            }
            in.close();
        } catch (IOException ex) {
            ex.printStackTrace();
        }
    }
}
```

运行此程序将把 `url1` 标识的 URL 资源中的 HTML 内容打印到命令行。

工作原理

得益于 `java.net.URL` 类，在 Java 代码中创建 URL 相当直接，该类完成了所有繁重的工作。URL 是一个指向互联网资源的字符串。有时在 Java 代码中创建 URL 很有用，这样你就可以从 URL 指向的互联网资源读取内容，或向其推送内容。在本方案的解决方案中，创建了几个不同的 `URL` 对象，演示了可供使用的不同构造函数。



创建 URL 最简单的方法是，将互联网上资源的标准可读 URL 字符串传递给 `java.net.URL` 类，以生成一个新的 URL 实例。在解决方案中，将一个绝对 URL 传递给构造函数来创建 `url1` 对象。

URL url1 = new URL(" [`www.java.net`](http://www.java.net/)"); 另一种创建 URL 的实用方法是向 URL 构造函数传递两个参数，从而创建一个相对 URL。

将相对 URL 基于另一个 URL 的位置来创建非常有用。例如，如果某个网站有多个不同的页面，你可以创建一个指向主站 URL 下某个子页面的相对 URL。本食谱解决方案中的 `url2` 对象就是这种情况。

URL url2 = new URL(url1, "search/node/jdk8");

如你所见，路径 `search/node/jdk8` 是相对于名为 `url1` 的 URL 的。最终，`url2` 对象的人类可读格式表示为 [`www.java.net/search/node/jdk8`](http://www.java.net/search/node/jdk8)。还有几个用于创建 URL 对象的构造函数，它们接受两个以上的参数。这些构造函数如下所示：

new URL (String protocol, String host, String port, String path);

new URL (String protocol, String host, String path);

在解决方案中，演示了此处列出的两个构造函数中的第二个。将资源的协议、主机名和路径传递给构造函数来创建 `url3` 对象。当你动态生成 URL 时，最后这两个构造函数通常最为有用。

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 21 章 网络编程

21-6\. 解析 URL

问题

你希望在应用程序中通过编程方式从 URL 收集信息。

解决方案

使用内置的 URL 类方法解析 URL。在下面名为 `ParseUrl` 的示例类中，创建了一个 URL 对象，然后使用内置的 URL 类方法对其进行解析，以收集有关该 URL 的信息。从 URL 检索到信息后，将其打印到命令行，然后用于创建另一个 URL。

import java.net.MalformedURLException;

import java.net.URL;

public static void main(String[] args) {

URL url1 = null;

URL url2 = null;

try {

// 生成绝对 URL

url1 = new URL("http://www.apress.com/catalogsearch/result/?q=juneau");

String host = url1.getHost();

String path = url1.getPath();

String query = url1.getQuery();

String protocol = url1.getProtocol();

String authority = url1.getAuthority();

String ref = url1.getRef();

System.out.println("URL " + url1.toString() + " 解析结果如下：\n"); System.out.println("主机: " + host + "\n");

System.out.println("路径: " + path + "\n");

System.out.println("查询: " + query + "\n");

System.out.println("协议: " + protocol + "\n");

System.out.println("授权: " + authority + "\n");

System.out.println("引用: " + ref + "\n");

url2 = new URL(protocol + "://" + host + path + "?q=java");

} catch (IOException ex) {

ex.printStackTrace();

}

}

当执行此代码时，将显示以下行：

URL [`www.apress.com/catalogsearch/result/?q=juneau`](http://www.apress.com/catalogsearch/result/?q=juneau) 解析结果如下： 主机: [www.apress.com](http://www.apress.com/)

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 21 章 网络编程

路径: /catalogsearch/result/

查询: q=juneau

协议: http

[授权: www.apress.com](http://www.apress.com/)

引用: null

工作原理

在应用程序中构造和使用 URL 时，有时提取与 URL 相关的信息会很有帮助。使用 URL 内置类方法可以轻松实现这一点，这些方法可以调用给定的 URL 并返回信息字符串。表 21-1 解释了 URL 类中可用于获取信息的访问器方法。

**表 21-1.** *用于查询 URL 的访问器方法*

**方法**

**返回的 URL 信息**

getAuthority()

授权组件

getFile()

文件名组件



getHost()

主机名组件

getPath()

路径组件

getProtocol()

协议标识符组件

getRef()

引用组件

getQuery()

查询组件

这些访问器方法各自返回一个字符串值，可用于信息参考或动态构造其他 URL，正如示例中所做的那样。如果你查看本解决方案的结果，就能看到通过表 21-1 中列出的访问器方法所获取的关于该 URL 的信息。大多数访问器方法都一目了然，但其中几个可能需要进一步解释。

getFile() 方法返回 URL 的文件名。文件名等同于 getPath() 返回值与 getQuery() 返回值拼接后的结果。getRef() 方法可能不太直观。调用 getRef() 方法返回的引用组件指的是可能附加在 URL 末尾的“片段”。

例如，片段使用井号（#）表示，后跟一个通常对应特定网页上某个子部分的字符串。对于如下所示的 URL，使用 getRef() 方法将返回 recipe21_6。

[`www.java8recipes.org/chapters/chapter21#recipe21_6`](http://www.java7recipes.org/chapters/chapter21#recipe21_6)

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 21 章 网络编程

虽然并非总是必需，但解析 URL 以获取信息的能力有时会非常实用。由于 Java 语言在 java.net.URL 类中内置了辅助方法，因此收集与 URL 相关的信息变得轻而易举。

本章小结

本章介绍了 Java 语言中几个基本的网络功能。在最近的版本中，新增了一些不错的功能，例如套接字直接协议。然而，java.net 包中的大部分内容多年来一直未变，且功能强大、易于使用。本章深入探讨了如何使用套接字连接和 URL，以及通过 DatagramChannel 广播消息。第 22 章包含一个介绍 java.net 包中另一个新功能（即新的 URLPermission 类）的解决方案。

[www.it-ebooks.info](http://www.it-ebooks.info/)

**第 22 章**

**安全性**

安全性是应用程序开发中最重要的部分之一。提供功能正常的应用程序只是成功应用程序开发的一部分。提供安全的应用程序是许多开发者忽视的另一项重要任务。Java 语言通过提供众多选项和功能来增强应用程序的安全性，从而使得开发安全应用程序变得容易。在 Java 8 中，安全选项的各个方面都进行了新增和增强，使 Java 语言更加健壮和安全。

以下是其中一些安全功能的列表：

u  默认启用 TLS 1.1 和 TLS 1.2

u  限制 doPrivileged() 块权限

u  基于密码的加密（SunJCE）现在提供更强的算法

u  支持 SSL/TLS 服务器名称指示（SNI）扩展

u  增强 SunJCE 提供程序以支持 AES/GCM/NoPadding 密码实现以及 GCM 算法参数

u  增强 SunJCE 提供程序以支持基于 AEAD 模式的密码套件

u  向 keytool 添加了 -importpassword 选项

u  支持 DKS 密钥库

u  使用 SHA-2 的 SSH-224 变体增强了加密算法

u  增强对 NSA Suite B 密码学的支持

u  SecureRandom 现在为高熵随机数生成提供更好的支持

u  新的 PKIXRevocationChecker 类

u  PKCS 11 Windows 支持现在包括 64 位

u  Kerberos 5 中添加了新的 rcache 类型

u  在受约束委派的同一领域中支持 Kerberos 5 协议转换

u  默认不支持与 DES 相关的 Kerberos 5 加密类型

u  用于 GSS-API/Kerberos 5 机制的无绑定 SASL

u  SASL 服务可以支持多个主机名

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 22 章 安全性



u 在 OS X 上通过 JNI 桥接原生 JGSS

u 支持更强的临时性 DH 密钥

u 密码套件偏好定制

本章包含的食谱涵盖了上述部分安全特性。

22-1\. 断言权限子集

问题

你希望以常规权限集运行应用程序，但应用程序的某一部分需要权限子集，以防止潜在的安全风险。

解决方案

编写一个特权块，并向需要额外安全的代码块断言一个权限子集。在以下代码块中，标准权限执行应用程序，但只有权限子集用于写入文件。

这是通过将代码放入 `AccessController.doPrivileged` 块中实现的，该块指定了完成任务所需的最小权限。

```java
public static String writeFile(final String filename) {

    return AccessController.doPrivileged(

        (PrivilegedAction<String>) () -> {

            try (PrintWriter writer = new PrintWriter(filename, "UTF-8")) {

                writer.println("The first line");

                writer.println("The second line");

            } catch (FileNotFoundException | UnsupportedEncodingException ex) {

                System.out.println(ex);

            }

            return null;

        },

        null,

        new java.io.FilePermission(filename, "write"));

}
```

工作原理

在 Java 8 中，`AccessController.doPrivileged` 方法经过了改进，允许你指定一个可在块中使用的权限子集，而不是执行一个增强的权限集。可以在 Java 语言中使用特权块 API 来指定一组不同的权限，以执行部分代码。要指定权限，你必须使用 `AccessController.doPrivileged` 块，并传入一个封装了需要修改权限集的操作的 `PrivilegedAction`。以下代码展示了典型用法：

```java
AccessController.doPrivileged(new PrivilegedAction<void>(){

    public void run(){

        // 执行某些特权操作

    }

});
```

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 22 章 安全

这种技术的新变体允许你指定一个权限子集，而不是超集。在本食谱中，一个权限子集用于将文件写入磁盘。新的 `doPrivileged` 方法接受三个参数，如下面的伪代码所示：

```java
AccessController.doPrivileged(

    PrivilegedAction,

    AccessControlContext,

    java.security.Permission varargs);
```

第一个参数与标准的 `AccessController.doPrivileged` 相同，即一个 `PrivilegedAction`。

`PrivilegedAction` 可以是类或 lambda 表达式的形式。在食谱示例中，第一个参数是一个 lambda 表达式。无论哪种情况，类或 lambda 都必须实现包含 `run()` 方法的 `PrivilegedAction` 函数式接口。`run()` 方法返回一个值，本示例中由于没有相关值需要返回，因此直接返回 `null`。

第二个参数是一个 `AccessControlContext` 对象，可用于执行额外的安全检查。可以通过从特定调用上下文调用 `AccessControlContext.getInstance()` 来获取 `AccessControlContext` 实例。在示例中，指定了 `null`，表示不需要额外的安全检查。

第三个参数是一个可变参数，用于指定要授予的权限类型。该参数接受一个或多个 `Permission` 对象，或一个 `Permission` 对象数组。

新的 `AccessController.doPrivileged` 方法可用于指示当前任务的最小权限或更多权限。该方法提供了一种安全的方式，允许以标准权限运行的应用程序使用更少或更多的权限集来执行任务。要了解更多关于此特性的信息，请查看在线文档：[`docs.oracle.com/javase/8/docs/technotes/guides/security/doprivileged.html#asserting_a_subset_of_privileges`](http://docs.oracle.com/javase/8/docs/technotes/guides/security/doprivileged.html#asserting_a_subset_of_privileges)



[html#断言权限子集](http://docs.oracle.com/javase/8/docs/technotes/guides/security/doprivileged.html#asserting_a_subset_of_privileges)

22-2\. 为握手过程指定服务器名称

问题

你希望在发起与服务器的 SSL 连接之前，验证服务器名称以确保其有效。

解决方案

利用服务器名称指示（SNI）扩展，在握手过程中确定客户端尝试连接的是哪个服务器名称。SNI 扩展在握手过程中用于指定客户端尝试连接的主机。在以下示例中，创建了一个客户端，该客户端在其 SSL 参数列表中指定了一个用于有效连接的服务器主机。

public class SocketClient {

public static Socket socket = null;

public static PrintWriter out;

public static BufferedReader in;

public static void main(String[] args) {

createConnection("gmail.com",443);

}

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 22 章 安全性

public static void createConnection(String host, int port) {

try {

SSLSocketFactory factory = HttpsURLConnection.getDefaultSSLSocketFactory();

SSLSocket sslSocket = (SSLSocket) factory.createSocket(host, port);

SNIHostName serverName = new SNIHostName(host);

List<SNIServerName> serverNames = new ArrayList<>(1);

serverNames.add(serverName);

SSLParameters params = sslSocket.getSSLParameters();

params.setServerNames(serverNames);

sslSocket.setSSLParameters(params);

//创建套接字连接

System.out.println("开始握手...");

sslSocket.startHandshake();

System.out.println("握手完成...");

System.out.println("对端主机: " + sslSocket.getSession().getPeerHost());

sslSocket.close();

System.exit(1);

} catch (UnknownHostException e) {

System.out.println(e);

System.exit(1);

} catch (IOException e) {

System.out.println(e);

System.exit(1);

}

}

}

给定上述客户端，服务器必须使用 SNIMatcher 类来指示如何识别服务器名称。

以下代码演示了服务器端如何判断主机名是否与 SNIMatcher 对象匹配的实现。

final int httpd = 443;

SSLServerSocket sslServerSocket = null;

try {

ServerSocketFactory sslFactory = SSLServerSocketFactory.getDefault();

sslServerSocket = (SSLServerSocket) sslFactory.createServerSocket(httpd);

SNIMatcher matcher = SNIHostName.createSNIMatcher("www\\.gmail\\.(com|org)"); Collection<SNIMatcher> matchers = new ArrayList<>(1);

matchers.add(matcher);

SSLParameters params = sslServerSocket.getSSLParameters();

params.setSNIMatchers(matchers);

sslServerSocket.setSSLParameters(params);

SSLSocket sslSocket = (SSLSocket) sslServerSocket.accept();

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 22 章 安全性

System.out.println("正在关闭套接字");

sslSocket.close();

sslServerSocket.close();

} catch (IOException e) {

System.out.println(e);

} finally {

try {

sslServerSocket.close();

} catch (IOException ex) {

System.out.println(ex);

}

}

}

工作原理

服务器名称指示（SNI）扩展在尝试握手时，为服务器提供一份用于连接的有效服务器列表。SNI 扩展使用 SSL/TLS 协议来提供此功能。给定一个指定的服务器名称，应用程序可以判断它是否为打开 SSLSocket 或 SSLEngine 连接的有效地址。

要指定服务器名称，请使用 SSLParameters.setServerNames(List<SNIServerName> serverNames) 方法，并传入一个包含有效服务器名称的列表。在本示例中，使用以下代码行将服务器名称 gmail.com 设置为有效：

SNIHostName serverName = new SNIHostName(host);

List<SNIServerName> serverNames = new ArrayList<>(1);

serverNames.add(serverName);

SSLParameters params = sslSocket.getSSLParameters();

params.setServerNames(serverNames);

sslSocket.setSSLParameters(params);

创建了一个 ArrayList，其中包含 SNIServerName 对象，每个对象都指定一个有效的服务器名称。



在示例中，一个 `SNIServerName` 对象被添加到列表中。然后，一个有效服务器列表被传递给 `SSLParameters.setServerNames()` 方法。

22-3\. 指定密钥库密码

问题

你希望通过为 SSL 密钥库添加一个额外的密码来增强其安全性。

解决方案

在创建密钥库时指定新的 `importpassword` 命令选项。

keytool -genkey -alias java8recipes -keyalg RSA -keystore keystore.jks -keysize 2048

使用以下命令导入密码短语：

keytool –importpassword –alias java8recipes

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 22 章 安全

工作原理

`keytool` 工具的新命令 `–importpassword` 允许你将密码短语存储在新的 `KeyStore.SecretKeyEntry` 中。

该密码在标准密钥密码（`keypass`）之外提供了另一层安全保护，因为密钥密码用于保护它。当用户发出此命令时，他们必须提供 `–keypass` 选项以及密钥库密码，否则系统会提示他们输入。

22-4\. 生成最强的随机数

问题

你正在为某些安全上下文（例如 RSA 密钥）生成随机数。

解决方案

使用 `SecureRandom.getInstanceStrong()` 方法生成一个 `SecureRandom` 对象，该对象是通过使用 `SecureRandom.strongAlgorithmsSecurity` 属性中指定的算法或提供者来选择的。

在以下示例中，使用 `getInstanceStrong()` 方法生成了一个 `SecureRandom` 对象。然后从该 `SecureRandom` 实例生成一个种子，该种子稍后可用于某些安全目的。

SecureRandom strongSecure = SecureRandom.getInstanceStrong();

byte seed[] = strongSecure.generateSeed(20);

工作原理

在某些情况下，需要安全的随机密钥。一种情况是生成 RSA 公钥和私钥。在这些情况下，`SecureRandom getInstanceStrong()` 方法返回一个 `SecureRandom` 对象，该对象是通过使用 `secureRandom.strongAlgorithms` 安全策略中指定的算法和提供者来选择的。

你可以通过调用 `Security.getAlgorithms()` 方法并传入 Java 加密服务名称来获取这些算法。

22-5\. 为富互联网应用程序授予权限

问题

你希望基于 HTTP（而非任何类型的 TCP 连接）来管理应用程序的权限。

此外，你希望为 HTTP 权限指定主机名，而不是单个 IP 地址。

解决方案

使用 Java 8 中引入的 `java.net.URLPermission` 类，以定义比标准 `java.net.SocketPermission` 提供的更高级别的权限。这样做将允许你在 HTTP 请求方法和 URL 方案的上下文中表达权限。在以下示例中，`URLPermission` 类用于访问 [`www.apress.com`](http://www.apress.com/) 网站上的作者 URL。`URLPermission` 类表示访问由给定 URL 定义的资源的权限，以及一组用户可设置的请求方法和请求标头。

在这种情况下，`URLPermission` 类表示在 apress.com 网站上作者 URL 处定义的资源。

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 22 章 安全

public static void main(String[] a) {

URLPermission urlPermission = new URLPermission(" [`www.apress.com/index.php/author/author/`](https://www.apress.com/index.php/author/author/view/id/1866)

[view/id/1866](https://www.apress.com/index.php/author/author/view/id/1866)"); try {

AccessController.checkPermission(urlPermission);

System.out.println("Ok to open socket");

} catch (AccessControlException ace) {

System.out.println(ace);

}

}

在这种情况下，权限将被拒绝，因为此 URL 指向一个私有主机。但是，如果你构建了一个执行回调到远程应用程序服务器的应用程序，则该主机会执行有效性检查，然后为有效请求提供打开套接字的权限。

工作原理

通常，沙盒应用程序需要使用权限来访问远程资源。在 Java 8 之前，`java.net.SocketPermission` 用于对此类资源执行访问控制。使用 `SocketPermission` 存在问题，因为此权限无法区分 HTTP 连接和可能不被允许的其他 TCP 连接。`SocketPermissions` 也基于 IP 地址而非主机名进行操作。此限制阻止了根据指定主机名（而非单个 IP 地址）进行访问控制。在同一个 IP 地址托管多个虚拟服务器的情况下，使用 `SocketPermission` 只能访问其中一个，而如果能够解析主机名，则可以访问多个。

`java.net.URLPermission` 随 Java 8 的发布而引入，解决了使用 `SocketPermission` 时出现的一些问题。`java.net.URLPermission` 类用于表示访问由指定 URL 定义的单个资源或一组资源的权限。除了指定 URL 之外，还可以通过用户可定义的设置器和请求标头来指定所需的 HTTP 访问。因此，`URLPermission` 类包含两个构造函数，一个接受基于字符串的 URL，另一个接受基于字符串的 URL 和一个操作字符串。

传递给 `URLPermission` 类的 URL 可以有多种格式，因此字符串可能因访问的资源而异。在其最简单的格式中，URL 应使用以下格式：`scheme://authority/path`

例如：(" [`www.apress.com/*`](http://www.apress.com/*)")

URL 的方案通常是 `http` 或 `https`，但也不限于此。`authority` 可以包含许多详细信息。至少，`authority` 将是远程资源的主机名。但是，它也可以包含用户信息、主机范围和/或端口范围。如果指定，可选的用户信息将是一个特权用户名后跟 `@` 字符。

主机范围通过完全限定的 DNS 名称以及 IPv4 地址或 IPv6 地址部分来指定，每个部分由 (.) 分隔。主机范围 DNS 名称可以包含通配符 (*) 来表示 DNS 名称的某些部分。例如，你可以使用以下内容来表示仅匹配最右侧域标签的主机名：

["*.apress.com"](http://apress.com) 。

URL 的端口范围部分用于指定单个端口号，或权限授予访问权限的端口的有界或无界范围。任何无效的端口号或范围都将导致使用 HTTP 的默认端口 80 或 HTTPS 的默认端口 443。你也可以使用端口范围中的通配符 (*) 来表示所有端口。

URL 的路径部分由主机服务器中的一系列路径组成，由 `/` 字符分隔。该路径与 `FilePermission` 非常相似，但可以留空以表示对主机资源根的权限。该路径可用于通过在末尾列出单个资源来指定对其的访问。例如，577

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 22 章 安全

要访问单个 HTML 文件，你可以指定 [www.mydns.com/mypage.html](http://www.mydns.com/mypage.html)。该路径还可以包含通配符 `*` 和 `-`。`*` 指定同一目录中的所有资源，而 `-` 指定给定路径下递归的所有资源。

`URLPermission` 的第二个参数是一个操作字符串。该字符串分别是操作列表和请求标头列表的串联。操作字符串分别包含权限允许的请求方法和允许的请求标头的列表。请求方法选项是 HTTP 请求方法 `PUT`、`GET` 和 `DELETE`。



请求头是可选的，但如果存在，它们通过冒号（:）与请求方法分隔。操作字符串中不允许有空格，并且区分大小写。你也可以分别使用通配符（*）来表示所有请求方法或请求头。以下是一些示例：

u 无请求方法："PUT,GET,DELETE"

u 请求方法和请求头："PUT,GET:MyHeader1,MyHeader2"

u 所有请求方法："*"

`URLPermission` 类提供了比 `SocketPermissions` 更严格的安全级别，因为它更侧重于 URL 和主机访问。对于沙盒化的富互联网应用程序，Java 8 不再允许对源主机使用 `SocketPermissions`，这意味着从 Java 8 开始，来自 JavaScript 代码对应用程序的调用将不会被授予 `SocketPermissions`。现在，所有级别的富互联网应用程序的主 JAR 文件的清单中都需要 `Permissions` 属性。

总结

Java 8 中添加或增强了多项新的安全特性。本章仅涵盖了其中一部分新特性，包括为 `doPrivilege` 块提供有限权限、用于安全握手的 SNI，以及对沙盒化富互联网应用程序权限的更多控制。有关这些新安全特性的更多信息，请参考在线文档，以便你的应用程序使用最强大的安全选项。

[www.it-ebooks.info](http://www.it-ebooks.info/)

**索引**

**A**

getAsciiStream() 方法, 326

loadClob() 方法, 325

注解, 251

加载和查询代码, 323

小程序, 479

PreparedStatement setClob() 方法, 325

自动资源管理（ARM）特性, 214

setString() 方法, 325

VARCHAR 字段, 325

**B**

Cloneable 接口, 115

闭包, 134

二进制大对象（BLOB）, 325

命令行解释器

编译和执行, 10

**C**

类路径, 11

cp 参数, 11

CachedRowSet

d 选项, 11

acceptChanges() 方法, 312

main() 方法, 11

代码, 307

键盘输入

连接属性, 311

BufferedReader, 19

CreateConnection 类, 311

和异常处理, 18

execute() 方法, 311

InputStreamReader(), 19

executeQuery() 方法, 311

终端输入, 19

获取方法, 312

传递参数, 16

insertRow() 方法, 312

commit() 方法, 303

main() 方法, 307

并发, 223

moveToCurrentRow() 方法, 312

集合辅助类, 229

moveToInsertRow() 方法, 312

CopyOnWriteArrayList, 228

输出, 310

不同集合的协调

RowSetFactory, 311

原子操作, 231

setCommand() 方法, 311

fulfillOrder() 方法, 229

setKeys() 方法, 311

inventoryLock.lock() 方法, 230

SyncResolver 对象, 312

inventoryLock.unlock() 方法, 230



updateRow() 方法, 312

locking, 231

calculateDaysPlayed() 抽象方法, 127

lock.unlock() 方法, 232

CallableStatement, 326

ReentrantLock 对象, 232

捕获异常, 208

setFair 属性, 232

字符大对象 (CLOB)

离散单元, 241

BLOB, 325

键插入, 226

ClobgetString() 方法, 326

run() 方法, 224

ConnectioncreateClob() 方法, 325

runnable 接口, 223

数据类型, 323

startUpdatingThread() 方法, 228

executeUpdate() 方法, 325

synchronizedList() 方法, 228

[www.it-ebooks.info](http://www.it-ebooks.info/)

索引

并发 ( *续* )

create_user.sql 脚本, 283

线程, 224

CRUD 操作

await() 方法, 238

代码, 290

awaitTermination() 方法, 232

executeQuery() 方法, 292

blockingQueue 队列, 237

executeUpdate() 方法, 292–293

CorePoolSize, 233

格式, 292

CountDownLatch, 235

performCreate() 方法, 292

inventoryThread.join() 方法, 236

performDelete() 方法, 293

join() 方法, 238

performRead() 方法, 292

JVM 的线程调度器, 236

performUpdate() 方法, 293

KeepAliveTime, 233

结果, 292

MaximumPoolSize, 233

Statement executeQuery() 方法, 292

objectToSync, 237

*对比* DML, 292

prestartAllCoreThreads() 方法, 233

数据缓存，断开连接状态 ( *参见*

processOrder() 方法, 234

CachedRowSet)

Queue, 233

FilteredRowSet

shutdown() 方法, 233

AuthorFilter, 322

ThreadpoolExecutor 实例, 232

代码, 320

TimeUnit, 233

evaluate() 方法, 322

wait() 方法, 237

executeQuery() 方法, 323

WorkQueue, 233

FilteredRowSetImpl, 323

线程安全对象

FilteredRowSetImpl 类, 323

创建, 238

实现, 318

实现中, 240

implementFilteredRowSet() 方法, 320, 322

更新, 224

main() 方法, 320

用户线程与守护线程, 224

输出, 322

连接管理

setCommand() 方法, 323

CreateConnection 类, 293, 295

setFilter() 方法, 323

DataSource, 296

WHERE 子句, 322–323

db_props.properties, 295



JDBC 连接对象

DriverManager, 296

DataSource 对象, 284–285

JNDI, 296

DataSource 注册, 286](#index_split_005.html#p289)

策略性连接方法, 296

DriverManager 对象, 285

createPlayer(String) 方法, 102](#index_split_002.html#p107)

getConnection() 方法, 283](#index_split_005.html#p286)

创建、检索、更新和删除 (CRUD)

InitialContext, 286

操作, 290

java.naming.NamingException 异常, 286](#index_split_005.html#p289)

CSS 样式

java.sql.Connection 对象, 285

代码实现, 438

java.sql.SQLException, 286

Control Style 1 外观与风格, 441

管理 ( *参见* 连接管理)

enableCss() 方法, 443](#index_split_007.html#p444)

SQL 异常, 286

loadSkin() 方法, 443

JoinRowSet

Modena 外观与风格, 440

addRowSet() 方法, 317](#index_split_005.html#p320)

setStyle() 方法, 444](#index_split_007.html#p445)

AUTHOR_WORK 表, 317

setUserAgentStylesheet() 方法, 442

BOOK_AUTHOR 表, 317

货币代码

代码, 313

NumberFormat 实例

外键, 313

代码实现, 276](#index_split_005.html#p279)

getInt() 方法, 318

getCurrencyInstance() 方法, 277](#index_split_005.html#p280)

getString() 方法, 318

setCurrency() 方法, 277

joinRowQuery() 方法, 317](#index_split_005.html#p320)

main() 方法, 317

**D**

输出, 316

主键, 313

数据库

queryAuthorBook() 方法, 317

自动资源处理, 328

queryBookAuthor() 方法, 317

连接对象, 286

RowSets, 313

create_database.sql 脚本, 283

SQL JOIN 查询, 317](#index_split_005.html#p320)

[www.it-ebooks.info](http://www.it-ebooks.info/)

NINDEX

大对象查询与存储 ( *参见* 字符大对象 (CLOB))

SwitchTypeCheckerisValidSwitchType()

方法, 163

LocalDate 对象, 328

动态数组

查询与结果检索

arrayList, 176

Connection 对象, 288

List<String> 方法, 176

executeQuery() 方法, 288

输出, 175

java.sql.Connection 对象, 288

public static boolean screen, 174

next() 方法, 289

public static void screen, 174

ResultSet getInt() 方法, 289](#index_split_005.html#p292)

StockScreener 类方法, 174

ResultSet getString() 方法, 289](#index_split_005.html#p292)

StockScreener main() 方法, 175

ResultSet 对象, 288

void main, public static, 174

rs.next(), 289

固定集合常量

Statement 对象, 288

abstract 和 finaljava.lang.Enum 类, 155

System.out() 方法, 289](#index_split_005.html#p292)

编译器错误/警告, 155

可滚动 ResultSet

封装常量, 155



代码，303

枚举类型，153

常量，304

`equals()` 方法，156

`createStatement()` 方法，304

`FieldType.EMAIL_ADDRESS` 编码，154

正向和反向遍历，303

`name()` 方法，155

`PreparedStatement` 对象，304

`ordinal()` 方法，155

`prepareStatement()` 方法，304

`values()` 和 `valueOf(String)` 方法，156

记录检索代码，305

固定大小数组

SQL 注入防护（*参见* PreparedStatements）

`Arrays.toString(int[])` 方法，168

存储过程调用，326

`GradeAnalyzer` 类，164

事务

`GradeAnalyzer` 的 `main()` 方法，168

`autoCommit()` 首选项，300

`initGrades1()` 方法，167

`commit()` 方法，300, 302–303

`initGrades2()` 方法，168

`insertRecord()` 方法，303

`initGrades3()` 方法，168

`main()` 方法，302

最小和最大成绩，167

管理代码，300

对象类型数组，166

`rollback()` 方法，300, 303

输出，`GradeAnalyzer` 类，166

顺序处理，300

步骤，定义数组，167

`setAutoCommit()` 方法，302

泛型

可更新的 ResultSet

`aList.add((Integer)aNum)` 方法，171

代码，305

集合接口和类，170

并发，306

`Collection.removeAll()` 方法，173

`createStatement()` 方法，306

编译时特性，170

输出，306

菱形语法，173

`prepareStatement()` 方法，306

`LinkedList<Double>` 方法，172

`ResultSet.CONCUR_UPDATABLE` 常量，306

`LinkedList<Integer>`，171

`ResultSet` 对象，305

`List.add(E e)` 方法，170

`Statement` 对象，306

`List<String>>bMap` 方法，173

`TYPE_SCROLL_SENSITIVE`，306

`List` 原始类型，171

`updateRow()` 方法，307

`public interface List`，170

`updateString()` 方法，307

通配符，171

数据操作语言（DML），292

智能常量设计

数据结构

电子邮件地址，157

代码执行

`FieldType` 枚举方法，156

控制流语句，163

`FieldType` 实例，159

`isValidSwitchType()` 方法，162–163

`main()` 方法，159

`private static void playHands`，160

密码，156

`private static void printWinner`，161

电话号码，157

`public class`，160

社会安全号码，157



public static void main, 161

validate() 方法, 159

RockPaperScissors 类方法, 163

可迭代对象

switch 语句, 160

add(List<Stock>) 方法, 179

[www.it-ebooks.info](http://www.it-ebooks.info/)

索引

数据结构（续）

脚本

基于集合的类, 176

Apache Ant 程序, 254

iterator() 方法, 176, 178–179

build.path, 254

main() 方法, 178

build.xml 文件, 253–254

Mapvalues() 方法, 179

JAVA_HOME 环境变量, 252

remove() 方法, 179

JDK 安装, 253

StockPortfolio 方法, 176, 178

JUnit 与 Ant 配置, 252

UnmodifiableIterator 类, 179

线程转储, 259

映射迭代, 182

装饰器模式, 188

日期-时间 API, 65

DriverManager.getConnection() 方法, 285

二进制字面量, 96

ChronoUnit 类, 89

**E**

compareTo() 方法, 83, 87

format() 方法, 92

电子邮件

格式化

Authenticator 对象, 529

java.text.SimpleDateFormat 类, 92

检查, 532–533

模式字符, 93

getPasswordAuthentication() 方法, 529

SimpleDateFormat 类, 93, 95

HTML 内容, 530–531

getDay() 方法, 82

JavaMail, 527–528

getHours() 方法, 82

监控, 533–535

getMinutes() 方法, 82

多部分消息, 529–530

getMonth() 方法, 82

多个收件人, 531–532

getSeconds() 方法, 82

Properties 对象, 529

getTimeInMillis() 方法, 90

Transport() 方法, 528

getTime() 方法, 82, 90

Transport.send() 静态方法, 529

getTimezoneOffset() 方法, 82

事件调度线程 (EDT), 372

getYear() 方法, 82

executeQuery() 方法, 289

java.util.Calendar 类, 81

java.util.concurrent.TimeUnit 枚举, 88

**F**

java.util.Date 类, 81

LocalDate 类, 75

工厂模式, 104

LocalDate.of() 方法, 75

表单设计器应用程序,

LocalDateTime 类, 79

网格类型布局, 427

Calendar 类, 81, 83

单元格约束, 437

Date 对象, 81

变更监听器, 436

方法, 80

代码实现, 428

LocalTime 类, 77–79

EventHandler, 436



基于机器的时间戳，83

GridPaneControlPanel，435

数值字面量, 95

通过网格进行布局操作, 435

包, 66

MyForm，435

parse() 方法，91

属性控制面板，429

setTime() 方法, 83

目标网格窗格, 437

TimeUnit 枚举, 90

TimeUnit.MILLISECONDS，90

**G**

时区，84

toHours() 方法, 90

getConnection() 方法, 285

YearMonth 和 MonthDay 类, 77

图形, 405

死锁, 261

动画形状

调试与单元测试

代码实现, 422

注解，251

实例变量, 425

Assert.assertEquals 方法, 251

鼠标拖拽事件，427

FindBugs 报告，255, 257

鼠标按下事件, 426

垃圾回收, 257

鼠标释放事件，427

JUnit, 250

路径过渡, 426

printStackTrace() 方法，249–250

球形外观的球体，425

[www.it-ebooks.info](http://www.it-ebooks.info/)

索引

CSS 样式（*参见* CSS 样式）

天气应用

网格类型布局，427 ( *另请参阅* 表单设计器

alert() 函数，492

应用，网格类型布局)

代码实现, 489

图片查看器应用

HTML 事件，491

拖放操作, 410

OnAlert 属性，490

实例变量, 411

start() 方法，490

源代码实现, 406

警告消息, 491

新闻滚动条与照片查看器应用

天气信息, 486

（*参见* 新闻滚动条与照片查看器应用）

WebEngine 对象, 487

WebView 节点，483, 487

**H**

WOEID，487

HelloWorld 应用

**I**

命令行提示符

Application 类, 335

图片查看器应用

Application.launch() 方法, 335

拖放操作，410

编译与运行，335

实例变量

Group 类, 335

currentIndex，411

jmain() 方法, 335

imageFiles，411

Node, 335

NEXT 和 PREV，413

打包与部署，335

源代码实现，406

Scene 对象，335

交互式开发环境 (IDE)

Shape 对象, 335

命令行解释器, 10

show() 方法, 335

JDK 安装，1

Stage 对象, 335

字符串转换

start() 方法，335–336

浮点数, 15

NetBeans IDE

模式, 15

Application 类, 335



原始数据类型，16

`Application.launch()` 方法，335

变量声明

`Group` 类，335

字段，12

`main()` 方法，335

用于原始类型和字符串类型，12

`Node`，335

静态字段，13

`Scene` 对象，335

可见性，12

`Shape` 对象，335

工作原理，14

`show()` 方法，335

互联网消息访问协议（IMAP）

`Stage` 对象，335

电子邮件账户，532

`start()` 方法，335–336

I/O 流，187

HTML5 内容

倒计时计时器，488

**J, K**

显示数据库内容，492

`executeScript()` 方法，488

Java，2

JavaFX RSS 阅读器，492

`CLASSPATH` 变量

`alert()` 函数，502

配置，26

代码实现，493

JAR 文件，24

头条新闻，501

工作原理，25

RSS 源标题列，501

企业版，2

`saveRssFeed()` 方法，502

环境变量

`WebEngine` 对象，502

`getenv()`，26

网站视图，501

`ReadOneEnvVariable` 类，26

`WebViews` 和 `WebEngine`，501

工作原理，27

`loadContent()` 方法，489

移动版，2

`refreshCountdown` 变量，487

包

RSS 源 URL，487

API，24

`TimeLine` 对象，488

目录结构，23

[www.it-ebooks.info](http://www.it-ebooks.info/)

索引

Java（*续*）

`RuntimeException` 类，208, 215–216

命名冲突，22

抛出异常，210

安全性，24

`try/catch` 语法，208

工作原理，23

`try-with-resources` 块，215

Java 7，2

未捕获异常，213

`CLASSPATH` 配置，24

JavaFX

代码组织，22–23

后台进程

命令行，16, 19

`cancel()` 方法，373

可执行 Java 类，16

`ChangeListener`，373

Javadoc，20–21

类，369

Java 1，2 8

代码实现，369

日期时间 API

控件，372

内置实用方法，38

`createWorker()` 方法，372

当前日期和时间，37

EDT，372

`DateTimeFormatter`，37

文件复制窗口，372

默认方法，34

排队，372

IDE，1

`SwingWorker` 对象，372

JavaFX



任务对象, 372

DatePicker 组件, 42

updateProgress() 方法, 372

打印 API, 45

绑定表达式

SwingNode, 40

computeValue() 方法, 361, 365

JavaScript

控制台应用程序代码, 361

命令行, 48

Contact 对象, 364

Nashorn 引擎, 47

DoubleBinding 类, 361, 365

lambda 表达式, 29

multiply() 方法, 365

PoolCalculator 类, 31

输出, 364

Role 注解, 36

SimpleStringProperty 类, 364

Streams API, 38

策略, 361

Java 归档 (JAR) 文件, 24

边框生成

Java 社区进程 (JCP), 2

CSS 编辑器文本代码, 358

Java 数据库连接 (JDBC), 283

HBox 布局, 360

Java 开发工具包 8 (JDK), 1

imageView 对象, 360

Javadoc

setStyle() 方法, 360

注释, 20–21

样式选择器, 360

HTML 文档, 21

更改密码对话框, 391

工具执行, 22

代码实现, 387

工作原理, 21

initModality() 方法, 392

initOwner() 方法, 392

JDialog 类, 392

阶段和场景, 387

start() 方法, 392

Java 异常

AutoCloseable 接口, 215

捕获, 208

清理代码, 209

错误类, 207

层次结构, 207

IllegalArgumentException 类, 216

InvalidUsernameException, 208

日志记录, 208

FileHandler 模式, 220

java.util.logging.LogManager(), 218

loadLogConfiguration() 函数, 218

Log0.log 文件代码, 219

Logger.isDebugEnabled(), 220

日志级别, 218

recipeLogger 事件, 217

SocketHandler, 220

多个异常, 211

重新抛出, 216

更改文本字体, 339, 341

代码实现, 338

CSS, 340

DropShadow 对象, 342

FXML 文件, 342

javafx.scene.effect.DropShadow, 338

javafx.scene.effect.Reflection, 338

javafx.scene.text.Font, 338

反射, 342

setMaxWidth() 方法, 342

颜色分配

类, 348

代码实现, 349

循环方法, 353

椭圆形状，径向渐变, 351

[www.it-ebooks.info](http://www.it-ebooks.info/)

NINDEX

LinearGradient 属性, 352

SVG 文件



RadialGradient 属性，351

模拟时钟，479–480

矩形，半透明线性渐变, 351

clock3.svg, 480

圆角矩形，反射线性渐变，351

Swing 内容

形状，351

代码实现，399

Hello World 应用程序，473

runLater() 方法 d，403

在浏览器中，477

setContent() 方法 d，403

MyJavaFXApp.java 项目，475

SwingNode 对象，400, 402

项目对话框, 474

瑞士军刀，331

项目名称和位置，474

表格

项目向导 d，474

应用程序代码，378

运行选项，476

ChangeListener, 381

HTML5 内容，483

代码实现，376

键盘快捷键

getPeople() 方法，379

类，373

JTable 组件, 376

代码实现，373

ListView 控件，376, 381

内阴影效果，375

ObservableList，381

setAccelerator() 方法 d，376

setProperty() 方法 d，381

菜单创建

TableView 控件，376, 380–381

bind() 方法，356

updateItem() 方法 d，381

类，353

标签页添加

代码实现，353

代码实现，385

getItems() 方法，356

setSide() 方法 d，387

热键/键盘功能 y，355

标签页方向, 385

菜单栏，355–356

TabPane，386–387

菜单项, 355

文本绘制, 336

菜单对象，355

UI 组件，布局

NetBeans，477

代码实现，356

ObservableList

GridPane 类, 357

ArrayList 类，368

UI 拆分视图，383

FXCollections 类, 368

代码实现，381

GUI 应用程序代码, 366

分隔条位置，384

ListViews，367–368

GUI 应用程序，381

MVC 机制, 366

面板控件，381, 384

Swing 组件，368

VBox 布局控件，384

打印

垂直拆分, 384

API 类, 398

用户体验, 331

应用程序对话框，397–398

用户界面创建（*参见* HelloWorld 应用程序）

代码实现，392

Java 接口, 106

getAllPrinter() 方法 d，399

JavaMail，527–528

getDefaultPrinter() 方法 d，399

Java 网络编程



PrintDialog 类，395

accept() 方法，555

PrintJob.createPrinterJob()，398

communicateWithClient() 方法，556

printPage() 方法，398

createConnection() 方法，558

富互联网应用程序（RIAs），331

DatagramChannel 类，561

场景图，331

DatagramChannelconfigureBlocking(boolean)

形状创建，346

方法，565

代码实现，343

DatagramChannelopen() 方法，564

CubicCurve() 实例，347

flip() 方法，565

甜甜圈形状，348

InetSocketAddress 实例，564

javafx.scene.shape.* 包，343

InfiniBand，性能提升，559

路径元素，347

InputStreamReader 实例，556

Path.subtract() 方法，348

in.readLine() 方法，559

setEffect() 方法，348

java.net.ServerSocket，553

Shape 节点，347

MulticastChannel 接口，564

[www.it-ebooks.info](http://www.it-ebooks.info/)

NINDEX

Java 网络编程（续）

传递 Lambda 函数

NetworkInterface，564

areaCalc，146

PrintWriter 对象，556

calculate() 方法，146，148

run() 方法，565

MainClass，148

SDP 协议传输，560

替换匿名内部类，142，144

send() 方法，565

runnable 实现，141–142

服务器端应用程序，553

排序

Socket 连接，556

Collections.sort() 方法，139–140

TCP/IP 层，558

Comparator 接口，139

testConnection() 方法，559

forEach() 方法，140

UDP，564

map() 函数，140

URL（参见 URL）

语法，134

Java 平台管理器，332

变量访问，144–145

JavaScript 代码执行

Locale 实例，281

eval() 方法，507

BCP 47 文档，268

getEngineByName() 方法，506

构造函数，267

Java 类与库

创建，265

Java.type 函数，515

Locale.Builder 类，265

Java.type() 函数，513

Locale.forLanguageTag() 方法，266

ScriptEngineManager，514

区域设置匹配与过滤方法，271

传递 Java 参数，510

区域设置敏感类，267

返回值

Locale.setDefault() 方法，268

Invocable 对象，512–513

DISPLAY 和 FORMAT，269

invokeFunction() 方法，511



Locale.Category 枚举，270

ScriptEngine, 512

setter 方法, 268

ScriptEngineManager(), 505

记录异常，207, 220

字符串插值，509

在 Unix 中，519

**M**

通过命令行，507

jjs 命令行工具，508

匹配与过滤方法，Locale，271

jrunscript 工具，509

filter() 方法，272

JSR 223 jrunscript 工具，508

模式，273

java.sql.SQLException，285

RFC 4647，272

Java 标准版 (Java SE)，1

Media API, 445

下载页面，2

结束动画，469

验证，3

代码实现，470

java.util.function 包，133

createTheEnd() 方法，471

setOnEndOfMedia() 方法，471

**L**

translateTransition 方法，470

事件

Lambda 表达式

事件处理方法，464

参数列表，134

暂停事件，465

主体，135

工作原理，465

代码授权，135

媒体标记事件

过滤条件，141

隐藏式字幕文本，469

函数式接口，ActionCode，135, 137

onDragDropped 事件，466

函数式接口，HelloType，133

工作原理，469

函数式接口，ReverseType，136

MP3 播放器，445

Function<T,R> 接口，136

类使用，446

调用现有方法

代码实现，446

Arrays.sort() 方法，151

工作原理，452

compareByGoals() 方法，148

视频播放器

双冒号(::)运算符，150

attachMouseEvents() 方法，460

方法引用，150

ChangeListener，460

java.util.function 包，137

类使用，456

[www.it-ebooks.info](http://www.it-ebooks.info/)

NINDEX

代码实现，456

包，8

工作原理，461

目录结构，8

媒体标记事件

命名约定，8

隐藏式字幕文本，469

NetBeans Profiler，258

onDragDropped 事件，466

新闻滚动条与照片查看器应用程序

工作原理

类，414

OnMarker 事件属性，469

裁剪区域，419

setOnMarker() 方法，469

代码实现，414

模型-视图-控制器 (MVC)，366

循环计数，418

MP3 播放器，445

EventHandler，422



类利用率，446

JavaFX 动画，418

代码实现，446

play() 方法，421

工作原理，452

setOnFinished() 方法，420

AudioSpectrumListener 类，453，455

文本节点，420

实例变量，453

transitionByFading() 方法，421

Media 对象，454

TranslateTransition，419–420

MediaPlayer 对象，454

平移与淡出，418

鼠标拖拽事件处理器，453

数字，65

previousLocation 变量，454

比较运算符，69

setDropCompleted() 方法，455

浮点数，70

setOnMousePress() 方法，456

格式化 double 和 long 数字

spectrumDataUpdate() 方法，454

DecimalFormat 类，67

StageStyle.TRANSPARENT 样式，453

DecimalFormat 模式字符，69

多用途互联网邮件

NumberFormat 类，68

扩展（MIME），529–530

货币值，71

随机数生成

**N**

java.util.Random 类，73

Math.random() 方法，74

Nashorn

Random setSeed() 方法，74

访问 Java

浮点数或 Double 值的舍入

数组与集合

java.lang.Math round() 方法，66–67

Java.to() 函数，516

roundDoubleToLong() 方法，66

Java.type 函数，515

roundFloatToInt() 方法，66

Java.type() 函数，516

描述，505

**O**

Java 类，518

JavaFX，520

面向对象的 Java，97

Java 接口，517

== 和 != 运算符，119–120

加载并执行 JavaScript

布尔值 true 和布尔值 false 结果，123

命令行，507

ByteArrayOutputStream 和

eval() 方法，506

ByteArrayInputStream，119

getEngineByName() 方法，506

类封装

ScriptEngineManager()，505

constructPlayer() 方法，131–132

NetBeans IDE

内部类，127–129，131

安装，1

JavaBeans 风格的类

类功能，124–125

通过接口实现的类交互，114–115

访问器方法，9

类模板定义，126–127

getMessage()，8

Cloneable 接口，115–117

HelloMessage()，8

控制访问，97–98

setMessage(String)，8

深拷贝，118

Java SE 项目创建，3

equals() 和 hashCode() 方法，120–122

Hello, World，7

实例构造，相同类

命名，5

使用不同值



骨架代码, 5

构建器模式, 110

main() 方法, 9

buildNewTeam() 方法, 113

[www.it-ebooks.info](http://www.it-ebooks.info/)

索引

面向对象 Java（*续*）

**S**

buildPlayerList() 方法, 113

designateTeamCity() 方法, 113

可缩放矢量图形（SVG）文件

Roster 类, 112

模拟时钟

TeamBuilder 接口, 111

clock3.svg, 480

Team 对象, 111

getEngine() 方法, 482

TeamType 接口, 110–111, 113

在 Inkscape 中, 480

实例生成, 102–104

Inkscape, 482

接口定义, 106–107, 109

JavaFX 场景图, 479

Lisp 语言, 97

updateTime() 函数, 483

单实例类

WebEngine 对象, 482

枚举函数, 101

WebView 节点, 482

getInstance() 访问器方法, 100–101

安全性

INSTANCE 元素, 102

特性, 571

私有静态最终字段, 101

握手过程, 573

单例模式, 99–100

互联网应用

Statistics 类, 101

FilePermission, 578

重写的 equals() 方法, 123

SocketPermission, 577

私有字段, 98–99

URLPermission 类, 576–577

可重用对象创建, 104–106

随机数, 576

shallowCopyClone() 方法, 117–118

SSL 密钥库密码, 575

字符串的 hashCode() 方法, 124

权限子集

super.clone() 方法, 118

AccessControlContext 对象, 573

Oracle 的 ojdbc6.jar JDBC 驱动, 306

AccessController.doPrivileged 方法, 572

Oracle 技术网络（OTN）, 1

doPrivileged 方法, 573

run() 方法, 573

**P, Q**

序列化 Java 对象, 187

转换与遍历, 190

Pokémon® 异常, 212

复制文件, 196–197

PreparedStatements

创建目录, 198

catch 块, 298–299

目录监控

close() 方法, 299

内置轮询机制, 202

executeQuery() 方法, 299

类型, watchEvents, 203

finally 块, 299

watchKey.pollEvents() 方法, 203

PreparedStatement, 297

WatchService.poll, 202

recipeNumber 字符串, 299

外部化接口, 192

recipeNumber 变量, 299

文件移动, 197

记录删除, 297

Files.createDirectory() 方法, 199

记录插入, 297

遍历文件, 199



ResultSet 对象，299

标记接口，189

可滚动的 ResultSet，304

ObjectOutput 方法，192

setInt(position, variable) 方法，299

ObjectOutputStream 类，188

SQL 控制，296

路径执行，196

SQL 字符串，298–299

属性文件，203

替换变量，298–299

查询，200

try-catch 块，298

readExternal(ObjectInput in)，191

可更新的 ResultSet，306

readExternal/writeExternal 方法，191

序列化/反序列化机制，191

**R**

套接字连接

channels.newInputStream(socket)

关系数据库管理

方法，195

系统 (RDBMS)，283

Channels.newOutputStream() 方法，195

ResultSet getInt() 方法，289

IP 地址和端口，195

ResultSet getString() 方法，289

isDone(), isCancelled() 方法，195

Rhino 引擎，505

ObjectOutputStream.reset() 方法，196

[www.it-ebooks.info](http://www.it-ebooks.info/)

索引

ObjectOutputStream.writeUnshared()

NumberFormatException，57

方法，196

parseInt() 方法，57

发送/接收对象，194

拆箱，57

serverSocketChannel.accept() 方法，195

valueOf() 和 parseInt() 方法，57

socketChannel.connect() 方法，195

替换文本

字符串类，189

Pattern.compile() 方法，62

瞬态属性，189

replaceAll() 方法，62

解压压缩文件，205

检索问题，49

writeExternal(ObjectOutput out)，191

起始索引和结束索引，50

XML，192

substring() 方法，解决方案，49

单例模式，101

文本匹配

套接字直接协议 (SDP)，559

代码和模式，60

堆栈跟踪，250

编码，59

饥饿，261

Matcher find() 方法，61

静态代码分析器 (SCA)，256

Matcher lookingAt() 方法，61

流，140

Matcher matches() 方法，61

流唯一标识符 (SUID)，190

matches() 方法，61

字符串

Pattern.compile() 方法，61

大小写转换

Pattern.matches() 方法，61

区分大小写的字符串，54

正则表达式，61

toLowerCase() 方法，54

String helper matches()，59

toUpperCase() 方法，54

三步过程，60

比较

修剪空白

内置 equals() 方法，50

模式搜索程序，53

编码，50



`String trim()` 方法，53

`compareTo()` 和 `compareToIgnoreCase()` 方法

`System.out()` 方法，289

方法，52

`compareToIgnoreCase()` 方法，50

**T**

`compareTo()` 方法，50

比较运算符（`==`），52

文本节点

`equalsIgnoreCase()` 方法，50, 52

更改文本字体，338

Java `String equals()` 方法，52

键盘序列，375

连接

文本绘制，336

内置字符串，56

线程转储，259

组合，55

抛出异常，210

连接运算符，55

传输控制协议/网际协议

`concat()` 方法，55–56

（TCP/IP）层，558

辅助方法，56

`StringBuffer` 类，55–56

**U**

`StringBuilder` 类，55

数据类型，编程语言，49

Unicode 数字字符转换，263

文件名结尾字符串

字节数组到 Unicode 字符串

`endsWith()` 方法，63

字符集编码，279

文件名变量，63

`getBytes()` 方法，278

辅助方法，50

`Character.digit()` 静态方法，265

遍历字符串

`isDigit` 属性，265

`charAt()` 方法，58–59

`java.lang.Character` 类，263

`length()` 和 `charAt()` 方法，59

正则表达式

`toCharArray()` 方法，58–59

`demoComplex()` 方法，275

数值转换

`enRegEx` 和 `jaRegEx`，273

自动装箱，57

`replaceAll()` 方法，274

`Integer.parseInt()` 方法，57

`replaceFirst()` 方法，274

整数，56

`split()` 方法，274

`Integer.valueOf()` 方法，56

字符串方法，275

[www.it-ebooks.info](http://www.it-ebooks.info/)

索引

Unicode 数字字符转换（*续*）

**W**

流与缓冲区

`InputStreamReader`，279, 281

Webkit，473

`newEncoder()`/`newDecoder()` 方法，280

`OutputStreamWriter`，280–281

**X, Y**

**, Z**

URL

生成，566

XML 处理

解析

文件读取

访问器方法，569

`hasNext()` 方法，543

`getFile()` 方法，569

`javax.xml.stream.XMLStreamReader`

`getPath()` 方法，569

接口，540

`getQuery()` 和 `getRef()` 方法，569

`nextEvent()` 方法，543

`ParseUrl` 类，568

StAX，542

用户数据报协议（UDP），564

`XMLEventReader`，541

文件写入

**V**

`java.io.FileOutputStream` 类，539

`javax.xml.stream.XMLStreamWriter` 类，537

视频播放器

StAX，539

`attachMouseEvents()` 方法，460

`writeAttribute()` 方法，539



ChangeListener，460

writeCharacters() 方法，539

类的使用，456

writeEndElement() 方法，539

代码实现，456

writeStartDocumentMethod() 方法，539

工作原理

writeStartElement() 方法，539

attachMouseEvents() 方法，461

XMLStreamWriter 实例，539

changed() 方法，462

Java 绑定创建，548–549

createBackground() 方法，461

JAXB，550

createSlider() 方法，462

转换

媒体格式，461

文件要求，544

MediaView 对象，463

HTML 表格，546

pauseButton 和

javax.xml.transform 包，543

playButton 代码，463

patients.xml 文件，544

setFullScreen() 方法，462

XSL 文档，545

slider.isPressed() 标志，462

解组，549

VisualVM，259

验证，546

[www.it-ebooks.info](http://www.it-ebooks.info/)

Java 8 实战

第二版

Josh Juneau

[www.it-ebooks.info](http://www.it-ebooks.info/)

**Java 8 实战**

版权所有 © 2014 Josh Juneau

本作品受版权保护。出版商保留所有权利，无论是整体还是部分材料，具体涉及翻译、重印、重用插图、朗诵、广播、微缩胶片复制或任何其他物理方式，以及电子改编、计算机软件或目前已知或以后开发的类似或不同方法的传输或信息存储与检索。

本法律保留的例外情况包括与评论或学术分析相关的简短摘录，或专门为输入和执行于计算机系统而提供的材料，仅供购买者独家使用。未经出版商许可，不得复制本出版物或其部分内容，且必须始终从 Springer 获得使用许可。使用许可可通过版权清算中心的 RightsLink 获取。违反者将根据相应版权法被起诉。

ISBN-13（平装）：978-1-4302-6827-7

ISBN-13（电子版）：978-1-4302-6826-0

本书中可能出现商标名称、标识和图像。我们仅在编辑风格中使用这些名称、标识和图像，以维护商标所有者的利益，无意侵犯商标权。

本出版物中使用的商品名称、商标、服务标志及类似术语，即使未明确标识，也不应被视为对其是否受专有权利保护的表达意见。

尽管本书中的建议和信息在出版时被认为是真实准确的，但作者、编辑和出版商均不对可能出现的任何错误或遗漏承担法律责任。出版商对本书所含内容不作任何明示或暗示的保证。

出版商：Heinz Weinheimer

首席编辑：Jonathan Gennick

开发编辑：James Markham

技术审阅：James Weaver 和 Rohan Walia

编辑委员会：Steve Anglin, Mark Beckner, Ewan Buckingham, Gary Cornell, Louise Corrigan, Jim DeWolf, Jonathan Gennick, Robert Hutchinson, Michelle Lowman, James Markham, Matthew Moodie, Jeff Olson, Jeffrey Pepper, Douglas Pundick, Ben Renow-Clarke, Dominic Shakeshaft, Gwenan Spearing, Matt Wade, Steve Weiss

协调编辑：Jill Balzano

文字编辑：Kezia Endsley

排版：SPi Global

索引编制：SPi Global

美术设计：SPi Global

封面设计：Anna Ishchenko

全球图书贸易由 Springer Science+Business Media New York 发行，地址：233 Spring Street, 6th Floor, New York, NY 10013。电话：1-800-SPRINGER，传真：(201) 348-4505，电子邮件：[orders-ny@springer-sbm.com](http://orders-ny@springer-sbm.com)，或访问 [www.springeronline.com](http://www.springeronline.com)。Apress Media, LLC 是一家加利福尼亚有限责任公司，其唯一成员（所有者）是 Springer Science + Business Media Finance Inc (SSBM Finance Inc)。SSBM Finance Inc 是一家特拉华州公司。

有关翻译信息，请发送电子邮件至 [rights@apress.com](http://rights@apress.com)，或访问 [www.apress.com](http://www.apress.com)。

Apress 和 friends of ED 的书籍可批量购买用于学术、企业或促销用途。大多数图书也提供电子版和许可证。如需更多信息，请参考我们的特殊批量销售–电子书许可网页，网址为 [www.apress.com/bulk-sales](http://www.apress.com/bulk-sales)。

作者在本文中引用的任何源代码或其他补充材料，读者可在以下网址获取：

[www.apress.com](http://www.apress.com)。有关如何找到本书源代码的详细信息，请访问：

[www.apress.com/source-code/](http://www.apress.com/source-code/)。

[www.it-ebooks.info](http://www.it-ebooks.info/)

*本书献给 Java 开发者社区。*

*作为 Java 开发者，我们有幸在*

*世界上最广泛使用的平台之一上工作。*

*没有优秀的 Java 社区，*

*这个平台不会像今天这样伟大。*

*本书也献给我的妻子 Angela，*

*以及我的五个孩子：Kaitlyn、Jacob、Matthew、Zachary*

*和 Lucas。你们是我的快乐和灵感源泉。*

[www.it-ebooks.info](http://www.it-ebooks.info/)

**目录**

关于作者 ................................................................................................................ xli

关于技术审阅者 ................................................................................................... xliii

致谢 ..................................................................................................................... xlv

引言 ..................................................................................................................... xlvii

第 1 章：Java 8 入门 ........................................................................................ 1

1-1. 创建开发环境 ........................................................................................................... 1

问题 ................................................................................................................................................................. 1

解决方案 .......................................................................................................................................................... 1

工作原理 .......................................................................................................................................................... 2

1-2. 实现“Hello, World” ........................................................................................................ 3



问题 ................................................................................................................................................................. 3

解决方案.................................................................................................................................................................. 3

工作原理 .......................................................................................................................................................... 7

1-3\. 从命令行解释器编译和执行 ............................................ 10

问题 ............................................................................................................................................................... 10

解决方案................................................................................................................................................................ 10

工作原理 ........................................................................................................................................................ 11

1-4\. 声明简单变量 .................................................................................................. 12

问题 ............................................................................................................................................................... 12

解决方案................................................................................................................................................................ 12

工作原理 ........................................................................................................................................................ 14

vii

[www.it-ebooks.info](http://www.it-ebooks.info/)

N 目录

1-5\. 字符串的转换 ........................................................................................... 15

问题 ............................................................................................................................................................... 15

解决方案................................................................................................................................................................ 15

工作原理 ........................................................................................................................................................ 15

1-6\. 通过命令行执行传递参数 .............................................................. 16

问题 ............................................................................................................................................................... 16

解决方案................................................................................................................................................................ 16

工作原理 ........................................................................................................................................................ 17

1-7\. 从键盘接受输入 ..................................................................................... 18

问题 ............................................................................................................................................................... 18

解决方案................................................................................................................................................................ 18

工作原理 ........................................................................................................................................................ 19

1-8\. 为代码编写文档 ....................................................................................................... 20

问题 ............................................................................................................................................................... 20

解决方案................................................................................................................................................................ 20

工作原理 ........................................................................................................................................................ 21

1-9\. 使用包组织代码 .......................................................................................... 22

问题 ............................................................................................................................................................... 22

解决方案................................................................................................................................................................ 22

工作原理 ........................................................................................................................................................ 23

1-10\. 配置 CLASSPATH ............................................................................................... 24

问题 ............................................................................................................................................................... 24

解决方案................................................................................................................................................................ 24

工作原理 ........................................................................................................................................................ 25

1-11\. 读取环境变量 ......................................................................................... 26

问题 ............................................................................................................................................................... 26

解决方案................................................................................................................................................................ 26

工作原理 ........................................................................................................................................................ 27

总结 ..................................................................................................................................... 28

viii

[www.it-ebooks.info](http://www.it-ebooks.info/)

N 目录

第 2 章：新特性

N



Java 8 概述 .................................................................................. 29

Java 语言增强特性 ................................................................................................... 29

2-1\. 将功能作为方法参数传递 ...................................................................... 29

问题 ............................................................................................................................................................... 29

解决方案................................................................................................................................................................ 29

工作原理 ........................................................................................................................................................ 31

2-2\. 从 Lambda 表达式调用现有方法 ................................................. 31

问题 ............................................................................................................................................................... 31

解决方案................................................................................................................................................................ 31

工作原理 ........................................................................................................................................................ 34

2-3\. 在接口中提供默认方法实现 ............................................... 34

问题 ............................................................................................................................................................... 34

解决方案................................................................................................................................................................ 34

工作原理 ........................................................................................................................................................ 35

2-4\. 在声明或类型使用中多次应用同一注解 ....................... 36

问题 ............................................................................................................................................................... 36

解决方案................................................................................................................................................................ 36

工作原理 ........................................................................................................................................................ 36

日期时间 API .............................................................................................................................. 37

2-5\. 整合当前日期和时间 ............................................................................. 37

问题 ............................................................................................................................................................... 37

解决方案................................................................................................................................................................ 37



工作原理 ........................................................................................................................................................ 37

2-6\. 从指定字符串获取日期 ............................................................................ 37

问题 ............................................................................................................................................................... 37

解决方案................................................................................................................................................................ 37

工作原理 ........................................................................................................................................................ 38

ix

[www.it-ebooks.info](http://www.it-ebooks.info/)

目录

2-7\. 执行日期时间计算 ..................................................................................... 38

问题 ............................................................................................................................................................... 38

解决方案................................................................................................................................................................ 38

工作原理 ........................................................................................................................................................ 38

Streams API ................................................................................................................................. 38

2-8\. 遍历数据集合 ....................................................................................... 39

问题 ............................................................................................................................................................... 39

解决方案................................................................................................................................................................ 39

工作原理 ........................................................................................................................................................ 39

JavaFX ......................................................................................................................................... 40

2-9\. 在 JavaFX 中嵌入 Swing................................................................................................ 40

问题 ............................................................................................................................................................... 40

解决方案................................................................................................................................................................ 40

工作原理 ........................................................................................................................................................ 41

2-10\. 添加日期选择器 ........................................................................................................... 42

问题 ............................................................................................................................................................... 42



解决方案................................................................................................................................................................ 42

工作原理 ........................................................................................................................................................ 43

2-11\. 从 JavaFX 打印 .........................................................................................................45

问题 ............................................................................................................................................................... 45

解决方案................................................................................................................................................................ 45

工作原理 ........................................................................................................................................................ 46

脚本 ...................................................................................................................................... 47

2-12\. 在 Java 应用程序中嵌入 JavaScript ................................................................ 47

问题 ............................................................................................................................................................... 47

解决方案................................................................................................................................................................ 47

工作原理 ........................................................................................................................................................ 47

x

[www.it-ebooks.info](http://www.it-ebooks.info/)

N 目录

2-13\. 从命令行执行 JavaScript ................................................................. 48

问题 ............................................................................................................................................................... 48

解决方案................................................................................................................................................................ 48

工作原理 ........................................................................................................................................................ 48

总结 ..................................................................................................................................... 48

第 3 章：字符串

N

............................................................................................................ 49

3-1\. 获取字符串的子部分 ......................................................................................49

问题 ............................................................................................................................................................... 49

解决方案................................................................................................................................................................ 49

工作原理 ........................................................................................................................................................ 50

3-2\. 比较字符串 ............................................................................................................... 50

问题 ............................................................................................................................................................... 50

解决方案................................................................................................................................................................ 50

工作原理 ........................................................................................................................................................ 52

3-3\. 修剪空白........................................................................................................... 53

问题 ............................................................................................................................................................... 53

解决方案................................................................................................................................................................ 53

工作原理 ........................................................................................................................................................ 53

3-4\. 更改字符串的大小写 ............................................................................................. 54

问题 ............................................................................................................................................................... 54

解决方案................................................................................................................................................................ 54

工作原理 ........................................................................................................................................................ 54

3-5\. 拼接字符串 ......................................................................................................... 55

问题 ............................................................................................................................................................... 55

解决方案 #1........................................................................................................................................................... 55

解决方案 #2........................................................................................................................................................... 55

解决方案 #3........................................................................................................................................................... 55

工作原理 ........................................................................................................................................................ 56

xi

[www.it-ebooks.info](http://www.it-ebooks.info/)

N 目录

3-6\. 将字符串转换为数值 ................................................................................. 56

问题 ............................................................................................................................................................... 56

解决方案................................................................................................................................................................ 56



方案 #1........................................................................................................................................................... 56

方案 #2........................................................................................................................................................... 57

工作原理 ........................................................................................................................................................ 57

3-7\. 遍历字符串中的字符 ............................................................................. 58

问题 ............................................................................................................................................................... 58

解决方案................................................................................................................................................................ 58

工作原理 ........................................................................................................................................................ 59

3-8\. 查找文本匹配项 ........................................................................................................... 59

问题 ............................................................................................................................................................... 59

方案 #1........................................................................................................................................................... 59

方案 #2........................................................................................................................................................... 60

工作原理 ........................................................................................................................................................ 61

3-9\. 替换所有文本匹配项 .................................................................................................. 62

问题 ............................................................................................................................................................... 62

解决方案................................................................................................................................................................ 62

工作原理 ........................................................................................................................................................ 62

3-10\. 判断文件名是否以指定字符串结尾 ............................................................................. 63

问题 ............................................................................................................................................................... 63

解决方案................................................................................................................................................................ 63

工作原理 ........................................................................................................................................................ 63

总结 ..................................................................................................................................... 63



第 4 章：数字与日期

N

........................................................................................ 65

4-1\. 将浮点数和双精度值四舍五入为整数 .................................................................... 66

问题 ............................................................................................................................................................... 66

解决方案................................................................................................................................................................ 66

工作原理 ........................................................................................................................................................ 67

xii

[www.it-ebooks.info](http://www.it-ebooks.info/)

N 目录

4-2\. 格式化双精度和长十进制值 ...................................................................... 67

问题 ............................................................................................................................................................... 67

解决方案................................................................................................................................................................ 67

工作原理 ........................................................................................................................................................ 68

4-3\. 比较 int 值 ........................................................................................................... 69

问题 ............................................................................................................................................................... 69

解决方案 #1........................................................................................................................................................... 69

解决方案 #2........................................................................................................................................................... 69

工作原理 ........................................................................................................................................................ 70

4-4\. 比较浮点数 ..................................................................................... 70

问题 ............................................................................................................................................................... 70

解决方案 #1........................................................................................................................................................... 70

解决方案 #2........................................................................................................................................................... 71

工作原理 ........................................................................................................................................................ 71

4-5\. 计算货币值 ............................................................................................... 71

问题 ............................................................................................................................................................... 71



解决方案................................................................................................................................................................ 72

工作原理 ........................................................................................................................................................ 72

4-6\. 随机生成数值 ...............................................................................................73

问题 ............................................................................................................................................................... 73

解决方案 #1........................................................................................................................................................... 73

解决方案 #2........................................................................................................................................................... 74

工作原理 ........................................................................................................................................................ 74

4-7\. 获取不含时间的当前日期 ............................................................................ 75

问题 ............................................................................................................................................................... 75

解决方案................................................................................................................................................................ 75

工作原理 ........................................................................................................................................................ 75

4-8\. 根据给定日期条件获取日期对象 ........................................................................ 75

问题 ............................................................................................................................................................... 75

解决方案................................................................................................................................................................ 75

工作原理 ........................................................................................................................................................ 76

xiii

[www.it-ebooks.info](http://www.it-ebooks.info/)

目录

4-9\. 获取年月日组合日期 ................................................................... 76

问题 ............................................................................................................................................................... 76

解决方案 #1........................................................................................................................................................... 76

解决方案 #2........................................................................................................................................................... 76

工作原理 ........................................................................................................................................................ 77

4-10\. 基于当前时间获取并计算 ...................................................................... 77



问题 ............................................................................................................................................................... 77

解决方案................................................................................................................................................................ 77

工作原理 ........................................................................................................................................................ 78

4-11\. 同时获取和使用日期与时间 .............................................................................................. 79

问题 ............................................................................................................................................................... 79

解决方案 #1........................................................................................................................................................... 79

解决方案 #2........................................................................................................................................................... 81

解决方案 #3........................................................................................................................................................... 81

工作原理 ........................................................................................................................................................ 82

4-12\. 获取机器时间戳 ............................................................................................................... 83

问题 ............................................................................................................................................................... 83

解决方案................................................................................................................................................................ 83

工作原理 ........................................................................................................................................................ 84

4-13\. 基于时区转换日期和时间 ................................................................................................ 84

问题 ............................................................................................................................................................... 84

解决方案................................................................................................................................................................ 84

工作原理 ........................................................................................................................................................ 85

4-14\. 比较两个日期 ................................................................................................................ 87

问题 ............................................................................................................................................................... 87

解决方案................................................................................................................................................................ 87

工作原理 ........................................................................................................................................................ 87

xiv

[www.it-ebooks.info](http://www.it-ebooks.info/)

目录

4-15\. 查找日期和时间之间的间隔 ............................................................................................. 88

问题 ............................................................................................................................................................... 88

解决方案 #1........................................................................................................................................................... 88

解决方案 #2........................................................................................................................................................... 88

解决方案 #3........................................................................................................................................................... 89

工作原理 ........................................................................................................................................................ 89

4-16\. 从指定字符串获取日期时间 ............................................................................................ 91

问题 ............................................................................................................................................................... 91

解决方案................................................................................................................................................................ 91

工作原理 ........................................................................................................................................................ 91

4-17\. 格式化日期以用于显示 ................................................................................................... 92

问题 ............................................................................................................................................................... 92

解决方案 #1........................................................................................................................................................... 92

解决方案 #2........................................................................................................................................................... 92

工作原理 ........................................................................................................................................................ 93

4-18\. 编写可读的数字字面量 .................................................................................................... 95

问题 ............................................................................................................................................................... 95

解决方案................................................................................................................................................................ 95

工作原理 ........................................................................................................................................................ 95

4-19\. 声明二进制字面量 ........................................................................................................ 96



问题 ............................................................................................................................................................... 96

解决方案................................................................................................................................................................ 96

工作原理 ........................................................................................................................................................ 96

小结 ..................................................................................................................................... 96

第 5 章：面向对象的 Java .....................................................................................97

5-1\. 控制对类成员的访问 ........................................................................... 97

问题 ............................................................................................................................................................... 97

解决方案................................................................................................................................................................ 97

工作原理 ........................................................................................................................................................ 98

xv

[www.it-ebooks.info](http://www.it-ebooks.info/)

N 目录

5-2\. 使私有字段对其他类可访问 ..................................................................... 98

问题 ............................................................................................................................................................... 98

解决方案................................................................................................................................................................ 98

工作原理 ........................................................................................................................................................ 98

5-3\. 创建只有一个实例的类 .............................................................................. 99

问题 ............................................................................................................................................................... 99

解决方案 1............................................................................................................................................................. 99

解决方案 2........................................................................................................................................................... 101

工作原理 ...................................................................................................................................................... 101

5-4\. 生成类的实例 ........................................................................................ 102

问题 ............................................................................................................................................................. 102

解决方案.............................................................................................................................................................. 102

工作原理 ...................................................................................................................................................... 104

5-5\. 创建可重用的对象 ................................................................................................. 104

问题 ............................................................................................................................................................. 104

解决方案.............................................................................................................................................................. 104

工作原理 ...................................................................................................................................................... 105

5-6\. 为类定义接口 ....................................................................................... 106

问题 ............................................................................................................................................................. 106

解决方案.............................................................................................................................................................. 106

工作原理 ...................................................................................................................................................... 106

5-7\. 在不破坏现有代码的情况下修改接口 ....................................................... 108

问题 ............................................................................................................................................................. 108

解决方案.............................................................................................................................................................. 108

工作原理 ...................................................................................................................................................... 109

5-8\. 用不同的值构造同一个类的实例 ...................................... 109

问题 ............................................................................................................................................................. 109

解决方案.............................................................................................................................................................. 110

工作原理 ...................................................................................................................................................... 113

xvi

[www.it-ebooks.info](http://www.it-ebooks.info/)

N 目录

5-9\. 通过接口与类交互 ............................................................................... 114

问题 ............................................................................................................................................................. 114

解决方案.............................................................................................................................................................. 114



工作原理 ...................................................................................................................................................... 114

5-10\. 使类可克隆 ................................................................................................ 115

问题 ............................................................................................................................................................. 115

解决方案.............................................................................................................................................................. 115

工作原理 ...................................................................................................................................................... 118

5-11\. 比较对象 .......................................................................................................... 119

问题 ............................................................................................................................................................. 119

解决方案 1........................................................................................................................................................... 119

解决方案 2........................................................................................................................................................... 120

工作原理 ...................................................................................................................................................... 123

5-12\. 扩展类的功能 ............................................................................. 124

问题 ............................................................................................................................................................. 124

解决方案.............................................................................................................................................................. 124

工作原理 ...................................................................................................................................................... 125

5-13\. 为类定义可扩展的模板 ....................................................................... 126

问题 ............................................................................................................................................................. 126

解决方案.............................................................................................................................................................. 126

工作原理 ...................................................................................................................................................... 127

5-14\. 增强类的封装性 ....................................................................................... 127

问题 ............................................................................................................................................................. 127

解决方案.............................................................................................................................................................. 127



工作原理 ...................................................................................................................................................... 131

摘要 ................................................................................................................................... 132

xvii

[www.it-ebooks.info](http://www.it-ebooks.info/)

目录

第 6 章：Lambda 表达式 .................................................................................... 133

6-1\. 编写一个简单的 Lambda 表达式 ................................................................................ 133

问题 ............................................................................................................................................................. 133

解决方案.............................................................................................................................................................. 133

工作原理 ...................................................................................................................................................... 134

6-2\. 启用 Lambda 表达式的使用 .......................................................................... 135

问题 ............................................................................................................................................................. 135

解决方案 1........................................................................................................................................................... 136

解决方案 2........................................................................................................................................................... 136

工作原理 ...................................................................................................................................................... 137

6-3\. 用更少的代码行进行排序 ...................................................................................... 139

问题 ............................................................................................................................................................. 139

解决方案 1........................................................................................................................................................... 139

解决方案 2........................................................................................................................................................... 139

工作原理 ...................................................................................................................................................... 140

6-4\. 在数据集合上指定筛选条件 ....................................................................... 141

问题 ............................................................................................................................................................. 141

解决方案.............................................................................................................................................................. 141

工作原理 ...................................................................................................................................................... 141



6-5\. 实现 Runnable ............................................................................................................. 141

问题 ............................................................................................................................................................. 141

解决方案 .............................................................................................................................................................. 141

工作原理 ...................................................................................................................................................... 142

6-6\. 替换匿名内部类 ......................................................................................................... 142

问题 ............................................................................................................................................................. 142

解决方案 .............................................................................................................................................................. 142

工作原理 ...................................................................................................................................................... 143

6-7\. 从 Lambda 表达式访问类变量 ...................................................................................... 144

问题 ............................................................................................................................................................. 144

解决方案 .............................................................................................................................................................. 144

工作原理 ...................................................................................................................................................... 145

xviii

[www.it-ebooks.info](http://www.it-ebooks.info/)

目录

6-8\. 将 Lambda 表达式传递给方法 ..................................................................................... 146

问题 ............................................................................................................................................................. 146

解决方案 .............................................................................................................................................................. 146

工作原理 ...................................................................................................................................................... 148

6-9\. 按名称调用现有方法 .................................................................................................. 148

问题 ............................................................................................................................................................. 148

解决方案 .............................................................................................................................................................. 148

工作原理 ...................................................................................................................................................... 150

总结 ....................................................................................................................................... 152



第七章：数据结构与集合 .................................................................. 153

7-1. 定义一组固定的相关常量 ......................................................................... 153

问题 ............................................................................................................................................................. 153

解决方案 .............................................................................................................................................................. 153

工作原理 ...................................................................................................................................................... 155

7-2. 设计智能常量 ......................................................................................... 156

问题 ............................................................................................................................................................. 156

解决方案 .............................................................................................................................................................. 156

工作原理 ...................................................................................................................................................... 159

7-3. 根据指定值执行代码 ...................................................................... 159

问题 ............................................................................................................................................................. 159

解决方案 .............................................................................................................................................................. 160

工作原理 ...................................................................................................................................................... 163

7-4. 使用固定大小的数组 ........................................................................................... 164

问题 ............................................................................................................................................................. 164

解决方案 .............................................................................................................................................................. 164

工作原理 ...................................................................................................................................................... 166

7-5. 安全地使类型或方法能够操作各种类型的对象 ....................... 168

问题 ............................................................................................................................................................. 168

解决方案 .............................................................................................................................................................. 168

工作原理 ...................................................................................................................................................... 170

xix

[www.it-ebooks.info](http://www.it-ebooks.info/)

目录



7-6\. 使用动态数组 ........................................................................................................ 173

问题 ............................................................................................................................................................. 173

解决方案 .............................................................................................................................................................. 174

工作原理 ...................................................................................................................................................... 175

7-7\. 使对象可迭代 ......................................................................................................... 176

问题 ............................................................................................................................................................. 176

解决方案 .............................................................................................................................................................. 176

工作原理 ...................................................................................................................................................... 178

7-8\. 遍历集合 ............................................................................................................... 179

问题 ............................................................................................................................................................. 179

解决方案 .............................................................................................................................................................. 180

工作原理 ...................................................................................................................................................... 181

7-9\. 遍历映射（Map） .................................................................................................. 182

问题 ............................................................................................................................................................. 182

解决方案 .............................................................................................................................................................. 182

工作原理 ...................................................................................................................................................... 184

7-10\. 并行执行流 .......................................................................................................... 185

问题 ............................................................................................................................................................. 185

解决方案 .............................................................................................................................................................. 185

工作原理 ...................................................................................................................................................... 186

总结 ................................................................................................................................... 186

第 8 章：输入与输出

N



.......................................................................................... 187

8-1\. 序列化 Java 对象 ..................................................................................................... 188

问题 ............................................................................................................................................................. 188

解决方案.............................................................................................................................................................. 188

工作原理 ...................................................................................................................................................... 189

8-2\. 更高效地序列化 Java 对象 ........................................................................... 190

问题 ............................................................................................................................................................. 190

解决方案.............................................................................................................................................................. 191

工作原理 ...................................................................................................................................................... 191

xx

[www.it-ebooks.info](http://www.it-ebooks.info/)

N 目录

8-3\. 将 Java 对象序列化为 XML ....................................................................................... 192

问题 ............................................................................................................................................................. 192

解决方案.............................................................................................................................................................. 192

工作原理 ...................................................................................................................................................... 193

8-4\. 创建套接字连接并通过网络发送可序列化对象 ............ 194

问题 ............................................................................................................................................................. 194

解决方案.............................................................................................................................................................. 194

工作原理 ...................................................................................................................................................... 195

8-5\. 获取 Java 执行路径 .................................................................................... 196

问题 ............................................................................................................................................................. 196

解决方案.............................................................................................................................................................. 196

工作原理 ...................................................................................................................................................... 196

8-6\. 复制文件 .................................................................................................................... 196

问题 ............................................................................................................................................................. 196

解决方案.............................................................................................................................................................. 197

工作原理 ...................................................................................................................................................... 197

8-7\. 移动文件...................................................................................................................... 197

问题 ............................................................................................................................................................. 197

解决方案.............................................................................................................................................................. 197

工作原理 ...................................................................................................................................................... 198

8-8\. 创建目录 ........................................................................................................... 198

问题 ............................................................................................................................................................. 198

解决方案 1........................................................................................................................................................... 198

解决方案 2........................................................................................................................................................... 198

工作原理 ...................................................................................................................................................... 199

8-9\. 遍历目录中的文件 ...................................................................................... 199

问题 ............................................................................................................................................................. 199

解决方案.............................................................................................................................................................. 199

工作原理 ...................................................................................................................................................... 199

xxi

[www.it-ebooks.info](http://www.it-ebooks.info/)

N 目录

8-10\. 查询（和设置）文件元数据 .............................................................................. 200

问题 ............................................................................................................................................................. 200

解决方案.............................................................................................................................................................. 200



工作原理 ...................................................................................................................................................... 201

8-11\. 监控目录变更 ................................................................................. 201

问题 ............................................................................................................................................................. 201

解决方案.............................................................................................................................................................. 202

工作原理 ...................................................................................................................................................... 202

8-12\. 读取属性文件 ..................................................................................................... 203

问题 ............................................................................................................................................................. 203

解决方案.............................................................................................................................................................. 203

工作原理 ...................................................................................................................................................... 204

8-13\. 解压压缩文件 ................................................................................... 205

问题 ............................................................................................................................................................. 205

解决方案.............................................................................................................................................................. 205

工作原理 ...................................................................................................................................................... 206

总结 ................................................................................................................................... 206

第 9 章：异常与日志

N

............................................................................... 207

9-1\. 捕获异常 .......................................................................................................... 208

问题 ............................................................................................................................................................. 208

解决方案.............................................................................................................................................................. 208

工作原理 ...................................................................................................................................................... 209

9-2\. 确保代码块被执行 ........................................................................ 209

问题 ............................................................................................................................................................ 209

解决方案.............................................................................................................................................................. 210

工作原理 ...................................................................................................................................................... 210

9-3\. 抛出异常 .......................................................................................................... 210

问题 ............................................................................................................................................................. 210

解决方案.............................................................................................................................................................. 211

工作原理 ...................................................................................................................................................... 211

xxii

[www.it-ebooks.info](http://www.it-ebooks.info/)

N 目录

9-4\. 捕获多个异常............................................................................................. 211

问题 ............................................................................................................................................................. 211

解决方案 1........................................................................................................................................................... 211

解决方案 2........................................................................................................................................................... 212

工作原理 ...................................................................................................................................................... 212

9-5\. 捕获未捕获的异常 ................................................................................... 213

问题 ............................................................................................................................................................. 213

解决方案 1........................................................................................................................................................... 213

解决方案 2........................................................................................................................................................... 213

工作原理 ...................................................................................................................................................... 214

9-6\. 使用 try/catch 块管理资源 ...................................................................... 214

问题 ............................................................................................................................................................. 214

解决方案.............................................................................................................................................................. 214

工作原理 ...................................................................................................................................................... 215



9-7\. 创建异常类 ................................................................................................................................................ 215

问题 ................................................................................................................................................................... 215

解决方案 1 ......................................................................................................................................................... 215

解决方案 2 ......................................................................................................................................................... 216

工作原理 ........................................................................................................................................................... 216

9-8\. 重新抛出捕获的异常 .............................................................................................................................. 216

问题 ................................................................................................................................................................... 216

解决方案 ............................................................................................................................................................ 216

工作原理 ........................................................................................................................................................... 217

9-9\. 在应用程序中记录事件 .......................................................................................................................... 217

问题 ................................................................................................................................................................... 217

解决方案 ............................................................................................................................................................ 217

工作原理 ........................................................................................................................................................... 218

9-10\. 日志轮转与清理 .................................................................................................................................. 219

问题 ................................................................................................................................................................... 219

解决方案 ............................................................................................................................................................ 219

工作原理 ........................................................................................................................................................... 219

xxiii

[www.it-ebooks.info](http://www.it-ebooks.info/)

目录

9-11\. 记录异常 .............................................................................................................................................. 220

问题 ................................................................................................................................................................... 220

解决方案 ............................................................................................................................................................ 220

工作原理 ........................................................................................................................................................... 221

第 10 章：并发 ................................................................................................................................................... 223

10-1\. 启动后台任务 ..................................................................................................................................... 223

问题 ................................................................................................................................................................... 223

解决方案 ............................................................................................................................................................ 223

工作原理 ........................................................................................................................................................... 224

10-2\. 更新（并迭代）映射 .......................................................................................................................... 224

问题 ................................................................................................................................................................... 224

解决方案 ............................................................................................................................................................ 225

工作原理 ........................................................................................................................................................... 225

10-3\. 仅当键不存在时向映射中插入键 ....................................................................................................... 226

问题 ................................................................................................................................................................... 226

解决方案 ............................................................................................................................................................ 226

工作原理 ........................................................................................................................................................... 227

10-4\. 遍历正在变化的集合 .......................................................................................................................... 228

问题 ................................................................................................................................................................... 228

解决方案 1 ......................................................................................................................................................... 228

解决方案 2 ......................................................................................................................................................... 228

工作原理 ........................................................................................................................................................... 228

10-5\. 协调不同集合 ...................................................................................................................................... 229

问题 ................................................................................................................................................................... 229

解决方案 ............................................................................................................................................................ 229

工作原理 ........................................................................................................................................................... 229



方案 1 ............................................................................................................................................................. 229

方案 2 ............................................................................................................................................................. 230

工作原理 ......................................................................................................................................................... 231

10-6\. 将工作拆分为独立线程 ..................................................................................................... 232

问题 ............................................................................................................................................................... 232

解决方案 ......................................................................................................................................................... 232

工作原理 ......................................................................................................................................................... 233

xxiv

[www.it-ebooks.info](http://www.it-ebooks.info/)

目录

10-7\. 协调线程 ....................................................................................................................... 234

问题 ............................................................................................................................................................... 234

方案 1 ............................................................................................................................................................. 234

方案 2 ............................................................................................................................................................. 235

方案 3 ............................................................................................................................................................. 236

工作原理 ......................................................................................................................................................... 236

10-8\. 创建线程安全对象 ......................................................................................................... 238

问题 ............................................................................................................................................................... 238

方案 1 ............................................................................................................................................................. 238

方案 2 ............................................................................................................................................................. 239

工作原理 ......................................................................................................................................................... 240

10-9\. 实现线程安全计数器 ..................................................................................................... 240

问题 ............................................................................................................................................................... 240

解决方案 ......................................................................................................................................................... 240

工作原理 ......................................................................................................................................................... 241

10-10\. 将任务分解为离散工作单元 ......................................................................................... 241

问题 ............................................................................................................................................................... 241

解决方案 ......................................................................................................................................................... 242

工作原理 ......................................................................................................................................................... 244

10-11\. 跨多个线程更新公共值 ................................................................................................. 245

问题 ............................................................................................................................................................... 245

解决方案 ......................................................................................................................................................... 245

工作原理 ......................................................................................................................................................... 246

总结 ......................................................................................................................................... 247

第 11 章：调试与单元测试 ......................................................................................................... 249

11-1\. 理解异常 ......................................................................................................................... 249

问题 ............................................................................................................................................................... 249

解决方案 ......................................................................................................................................................... 249

工作原理 ......................................................................................................................................................... 250

xxv

[www.it-ebooks.info](http://www.it-ebooks.info/)

目录

11-2: 锁定类的行为 ................................................................................................................... 250

问题 ............................................................................................................................................................... 250

解决方案 ......................................................................................................................................................... 250

工作原理 ......................................................................................................................................................... 251

11-3\. 编写单元测试脚本 ......................................................................................................... 252



问题 ............................................................................................................................................................. 252

解决方案.............................................................................................................................................................. 252

工作原理 ...................................................................................................................................................... 254

11-4\. 尽早发现错误 ........................................................................................................... 255

问题 ............................................................................................................................................................. 255

解决方案.............................................................................................................................................................. 255

工作原理 ...................................................................................................................................................... 256

11-5\. 监控应用程序中的垃圾回收 .................................................................. 257

问题 ............................................................................................................................................................. 257

解决方案 1........................................................................................................................................................... 257

解决方案 2........................................................................................................................................................... 257

工作原理 ...................................................................................................................................................... 259

11-6\. 获取线程转储 ................................................................................................ 259

问题 ............................................................................................................................................................. 259

解决方案.............................................................................................................................................................. 259

工作原理 ...................................................................................................................................................... 261

第 12 章：Unicode、

国际化与货币代码 ................................... 263

12-1\. 将 Unicode 字符转换为数字 ......................................................................... 263

问题 ............................................................................................................................................................. 263

解决方案.............................................................................................................................................................. 263

工作原理 ...................................................................................................................................................... 265

12-2\. 创建和使用区域设置 ................................................................................. 265

问题 ............................................................................................................................................................. 265

解决方案.............................................................................................................................................................. 265

工作原理 ...................................................................................................................................................... 267

xxvi

[www.it-ebooks.info](http://www.it-ebooks.info/)

目录

12-3\. 设置默认区域设置................................................................................................ 268

问题 ............................................................................................................................................................. 268

解决方案.............................................................................................................................................................. 268

工作原理 ...................................................................................................................................................... 270

12-4\. 匹配和过滤区域设置 ........................................................................................ 271

问题 ............................................................................................................................................................. 271

解决方案.............................................................................................................................................................. 271

工作原理 ...................................................................................................................................................... 272

12-5\. 使用正则表达式搜索 Unicode ................................................................. 273

问题 ............................................................................................................................................................. 273

解决方案 1........................................................................................................................................................... 273

解决方案 2........................................................................................................................................................... 274

工作原理 ...................................................................................................................................................... 275

12-6\. 覆盖默认货币 ...................................................................................... 276

问题 ............................................................................................................................................................. 276

解决方案.............................................................................................................................................................. 276



工作原理 ...................................................................................................................................................... 277

12-7\. 字节数组与字符串之间的相互转换 ..................................................................... 278

问题 ............................................................................................................................................................. 278

解决方案.............................................................................................................................................................. 278

工作原理 ...................................................................................................................................................... 279

12-8\. 字符流与缓冲区的转换 ..................................................................................... 279

问题 ............................................................................................................................................................. 279

解决方案 1........................................................................................................................................................... 279

解决方案 2........................................................................................................................................................... 280

工作原理 ...................................................................................................................................................... 281

12-9\. 设置区域敏感服务的搜索顺序 ........................................................................... 281

问题 ............................................................................................................................................................. 281

解决方案.............................................................................................................................................................. 281

工作原理 ...................................................................................................................................................... 282

总结 ................................................................................................................................... 282

xxvii

[www.it-ebooks.info](http://www.it-ebooks.info/)

目录

第 13 章：使用数据库 ............................................................................ 283

13-1\. 连接数据库 ................................................................................................ 283

问题 ............................................................................................................................................................. 283

解决方案 1........................................................................................................................................................... 283

解决方案 2........................................................................................................................................................... 284

工作原理 ...................................................................................................................................................... 284

13-2\. 处理连接与 SQL 异常 ....................................................................... 286

问题 ............................................................................................................................................................. 286

解决方案.............................................................................................................................................................. 286

工作原理 ...................................................................................................................................................... 287

13-3\. 查询数据库并检索结果 ................................................................... 288

问题 ............................................................................................................................................................. 288

解决方案.............................................................................................................................................................. 288

工作原理 ...................................................................................................................................................... 288

13-4\. 执行 CRUD 操作 ........................................................................................... 290

问题 ............................................................................................................................................................. 290

解决方案.............................................................................................................................................................. 290

工作原理 ...................................................................................................................................................... 292

13-5\. 简化连接管理 .............................................................................. 293

问题 ............................................................................................................................................................. 293

解决方案.............................................................................................................................................................. 293

工作原理 ...................................................................................................................................................... 296

13-6\. 防范 SQL 注入 ........................................................................................ 296

问题 ............................................................................................................................................................. 296

解决方案.............................................................................................................................................................. 296

工作原理 ...................................................................................................................................................... 298

13-7\. 执行事务 .................................................................................................. 300



问题 ............................................................................................................................................................. 300

解决方案.............................................................................................................................................................. 300

工作原理 ...................................................................................................................................................... 302

xxviii

[www.it-ebooks.info](http://www.it-ebooks.info/)

目录

13-8\. 创建可滚动的 ResultSet ....................................................................................... 303

问题 ............................................................................................................................................................. 303

解决方案.............................................................................................................................................................. 303

工作原理 ...................................................................................................................................................... 304

13-9\. 创建可更新的 ResultSet ..................................................................................... 305

问题 ............................................................................................................................................................. 305

解决方案.............................................................................................................................................................. 305

工作原理 ...................................................................................................................................................... 306

13-10\. 缓存数据以在断开连接时使用 ..................................................................... 307

问题 ............................................................................................................................................................. 307

解决方案.............................................................................................................................................................. 307

工作原理 ...................................................................................................................................................... 310

13-11\. 在未连接数据源时连接 RowSet 对象 ................................ 313

问题 ............................................................................................................................................................. 313

解决方案.............................................................................................................................................................. 313

工作原理 ...................................................................................................................................................... 317

13-12\. 在 RowSet 中过滤数据 ............................................................................................. 318

问题 ............................................................................................................................................................. 318

解决方案.............................................................................................................................................................. 318

工作原理 ...................................................................................................................................................... 322

13-13\. 查询和存储大对象 .............................................................................. 323

问题 ............................................................................................................................................................. 323

解决方案.............................................................................................................................................................. 323

工作原理 ...................................................................................................................................................... 325

13-14\. 调用存储过程 ........................................................................................... 326

问题 ............................................................................................................................................................. 326

解决方案.............................................................................................................................................................. 326

工作原理 ...................................................................................................................................................... 327

xxix

[www.it-ebooks.info](http://www.it-ebooks.info/)

目录

13-15\. 获取用于数据库的日期 ................................................................................. 327

问题 ............................................................................................................................................................. 327

解决方案.............................................................................................................................................................. 328

工作原理 ...................................................................................................................................................... 328

13-16\. 自动处理资源 ................................................................................ 328

问题 ............................................................................................................................................................. 328

解决方案.............................................................................................................................................................. 328

工作原理 ...................................................................................................................................................... 329

总结 ................................................................................................................................... 329

第 14 章：JavaFX 基础 ................................................................................ 331



14-1\. 创建简单用户界面 ..................................................................................................................... 332

问题 ............................................................................................................................................................. 332

解决方案 1........................................................................................................................................................... 332

解决方案 2........................................................................................................................................................... 333

工作原理 ...................................................................................................................................................... 334

14-2\. 绘制文本 ........................................................................................................................ 336

问题 ............................................................................................................................................................. 336

解决方案.............................................................................................................................................................. 336

工作原理 ...................................................................................................................................................... 337

14-3\. 更改文本字体 ................................................................................................................. 338

问题 ............................................................................................................................................................. 338

解决方案 1........................................................................................................................................................... 338

解决方案 2........................................................................................................................................................... 339

工作原理 ...................................................................................................................................................... 341

14-4\. 创建形状 ....................................................................................................................... 343

问题 ............................................................................................................................................................. 343

解决方案.............................................................................................................................................................. 343

工作原理 ...................................................................................................................................................... 347

xxx

[www.it-ebooks.info](http://www.it-ebooks.info/)

目录

14-5\. 为对象分配颜色 ............................................................................................................. 348

问题 ............................................................................................................................................................. 348

解决方案.............................................................................................................................................................. 348

工作原理 ...................................................................................................................................................... 351

14-6\. 创建菜单 ........................................................................................................................ 353

问题 ............................................................................................................................................................. 353

解决方案.............................................................................................................................................................. 353

工作原理 ...................................................................................................................................................... 355

14-7\. 向布局添加组件 .............................................................................................................. 356

问题 ............................................................................................................................................................. 356

解决方案.............................................................................................................................................................. 356

工作原理 ...................................................................................................................................................... 357

14-8\. 生成边框 ...................................................................................................................... 358

问题 ............................................................................................................................................................. 358

解决方案.............................................................................................................................................................. 358

工作原理 ...................................................................................................................................................... 360

14-9\. 绑定表达式 .................................................................................................................... 361

问题 ............................................................................................................................................................. 361

解决方案.............................................................................................................................................................. 361

工作原理 ...................................................................................................................................................... 364

14-10\. 创建与使用可观察列表 ................................................................................................. 365

问题 ............................................................................................................................................................. 365

解决方案.............................................................................................................................................................. 365



工作原理 ...................................................................................................................................................... 368

14-11\. 生成后台进程 ................................................................................. 368

问题 ............................................................................................................................................................. 368

解决方案.............................................................................................................................................................. 369

工作原理 ...................................................................................................................................................... 372

xxxi

[www.it-ebooks.info](http://www.it-ebooks.info/)

目录

14-12\. 将键盘序列与应用程序关联 ..................................................... 373

问题 ............................................................................................................................................................. 373

解决方案.............................................................................................................................................................. 373

工作原理 ...................................................................................................................................................... 375

14-13\. 创建与使用表格 ................................................................................. 376

问题 ............................................................................................................................................................. 376

解决方案.............................................................................................................................................................. 376

工作原理 ...................................................................................................................................................... 380

14-14\. 使用拆分视图组织用户界面 ................................................................................. 381

问题 ............................................................................................................................................................. 381

解决方案.............................................................................................................................................................. 381

工作原理 ...................................................................................................................................................... 383

14-15\. 为界面添加选项卡 ..................................................................................................... 384

问题 ............................................................................................................................................................. 384

解决方案.............................................................................................................................................................. 384

工作原理 ...................................................................................................................................................... 386

14-16\. 开发对话框 ................................................................................................ 387

问题 ............................................................................................................................................................. 387

解决方案.............................................................................................................................................................. 387

工作原理 ...................................................................................................................................................... 391

14-17\. 使用 JavaFX 进行打印 ...................................................................................................... 392

问题 ............................................................................................................................................................. 392

解决方案.............................................................................................................................................................. 392

工作原理 ...................................................................................................................................................... 398

14-18\. 在 JavaFX 中嵌入 Swing 内容 ............................................................................ 399

问题 ............................................................................................................................................................. 399

解决方案.............................................................................................................................................................. 399

工作原理 ...................................................................................................................................................... 403

总结 ................................................................................................................................... 403

xxxii

[www.it-ebooks.info](http://www.it-ebooks.info/)

目录

第 15 章：使用 JavaFX 进行图形处理 ................................................................................ 405

15-1\. 创建图像 ............................................................................................................... 406

问题 ............................................................................................................................................................. 406

解决方案.............................................................................................................................................................. 406

工作原理 ...................................................................................................................................................... 411

15-2\. 生成动画 ................................................................................................. 414

问题 ............................................................................................................................................................. 414



解决方案.............................................................................................................................................................. 414

工作原理 ...................................................................................................................................................... 418

15-3\. 沿路径为形状添加动画 ....................................................................................... 422

问题 ............................................................................................................................................................. 422

解决方案.............................................................................................................................................................. 422

工作原理 ...................................................................................................................................................... 425

15-4\. 通过网格操控布局 ......................................................................................... 427

问题 ............................................................................................................................................................. 427

解决方案.............................................................................................................................................................. 427

工作原理 ...................................................................................................................................................... 435

15-5\. 使用 CSS 增强界面 ................................................................................... 438

问题 ............................................................................................................................................................. 438

解决方案.............................................................................................................................................................. 438

工作原理 ...................................................................................................................................................... 442

小结 ................................................................................................................................... 444

第 16 章：JavaFX 中的媒体 ..................................................................................... 445

16-1\. 播放音频 ................................................................................................................... 445

问题 ............................................................................................................................................................. 445

解决方案.............................................................................................................................................................. 446

工作原理 ...................................................................................................................................................... 452

xxxiii

[www.it-ebooks.info](http://www.it-ebooks.info)

目录

16-2\. 播放视频 ................................................................................................................... 456

问题 ............................................................................................................................................................. 456

解决方案.............................................................................................................................................................. 456

工作原理 ...................................................................................................................................................... 461

16-3\. 控制媒体操作与事件 ............................................................................. 464

问题 ............................................................................................................................................................. 464

解决方案.............................................................................................................................................................. 464

工作原理 ...................................................................................................................................................... 465

16-4\. 标记视频中的位置........................................................................................... 466

问题 ............................................................................................................................................................. 466

解决方案.............................................................................................................................................................. 466

工作原理 ...................................................................................................................................................... 469

16-5\. 同步动画与媒体 ............................................................................... 469

问题 ............................................................................................................................................................. 469

解决方案.............................................................................................................................................................. 470

工作原理 ...................................................................................................................................................... 471

小结 ................................................................................................................................... 471

第 17 章：Web 上的 JavaFX ..................................................................................... 473

17-1\. 在网页中嵌入 JavaFX 应用程序.............................................................. 473

问题 ............................................................................................................................................................. 473

解决方案.............................................................................................................................................................. 473

工作原理 ...................................................................................................................................................... 477



17-2\. 加载可缩放矢量图形文件内容 ........................................................................................ 479

问题 ............................................................................................................................................................. 479

解决方案 .......................................................................................................................................................... 479

工作原理 ...................................................................................................................................................... 482

17-3\. 使用 Java 代码操作 HTML5 内容 ................................................................................. 483

问题 ............................................................................................................................................................. 483

解决方案 .......................................................................................................................................................... 483

工作原理 ...................................................................................................................................................... 487

xxxiv

[www.it-ebooks.info](http://www.it-ebooks.info/)

目录

17-4\. 响应 HTML 事件 ............................................................................................................ 489

问题 ............................................................................................................................................................. 489

解决方案 .......................................................................................................................................................... 489

工作原理 ...................................................................................................................................................... 491

17-5\. 显示数据库中的内容 .................................................................................................... 492

问题 ............................................................................................................................................................. 492

解决方案 .......................................................................................................................................................... 492

工作原理 ...................................................................................................................................................... 501

小结 ................................................................................................................................... 503

第 18 章：Nashorn 与脚本编程

............................................................................... 505

18-1\. 从 Java 加载并执行 JavaScript .................................................................................. 505

问题 ............................................................................................................................................................. 505

解决方案 .......................................................................................................................................................... 505

工作原理 ...................................................................................................................................................... 506

18-2\. 通过命令行执行 JavaScript ........................................................................................... 507

问题 ............................................................................................................................................................. 507

解决方案 1 ....................................................................................................................................................... 507

解决方案 2 ....................................................................................................................................................... 508

工作原理 ...................................................................................................................................................... 508

18-3\. 在字符串中嵌入表达式 .................................................................................................. 509

问题 ............................................................................................................................................................. 509

解决方案 .......................................................................................................................................................... 509

工作原理 ...................................................................................................................................................... 509

18-4\. 传递 Java 参数 ............................................................................................................ 510

问题 ............................................................................................................................................................. 510

解决方案 .......................................................................................................................................................... 510

工作原理 ...................................................................................................................................................... 511

18-5\. 将返回值从 JavaScript 传递到 Java .............................................................................. 511

问题 ............................................................................................................................................................. 511

解决方案 .......................................................................................................................................................... 511

工作原理 ...................................................................................................................................................... 512

xxxv

[www.it-ebooks.info](http://www.it-ebooks.info/)

目录

18-6\. 使用 Java 类和库 ........................................................................................................ 513

问题 ............................................................................................................................................................. 513

解决方案 .......................................................................................................................................................... 513



解决方案.............................................................................................................................................................. 513

工作原理 ...................................................................................................................................................... 515

18-7\. 在 Nashorn 中访问 Java 数组和集合 ......................................................... 515

问题 ............................................................................................................................................................. 515

解决方案 .............................................................................................................................................................. 515

工作原理 ...................................................................................................................................................... 516

18-8\. 实现 Java 接口 ......................................................................................... 517

问题 ............................................................................................................................................................. 517

解决方案.............................................................................................................................................................. 517

工作原理 ...................................................................................................................................................... 518

18-9\. 扩展 Java 类 ................................................................................................... 518

问题 ............................................................................................................................................................. 518

解决方案.............................................................................................................................................................. 518

工作原理 ...................................................................................................................................................... 519

18-10\. 在 Unix 中创建可执行脚本 ............................................................................... 519

问题 ............................................................................................................................................................. 519

解决方案.............................................................................................................................................................. 519

工作原理 ...................................................................................................................................................... 520

18-11\. 使用 Nashorn 实现 JavaFX .............................................................................. 520

问题 ............................................................................................................................................................. 520

解决方案 1........................................................................................................................................................... 520



解决方案 2........................................................................................................................................................... 523

工作原理 ...................................................................................................................................................... 525

小结 ................................................................................................................................... 525

第 19 章：电子邮件

N

......................................................................................................... 527

19-1\. 安装 JavaMail ........................................................................................................... 527

问题 ............................................................................................................................................................. 527

解决方案.............................................................................................................................................................. 527

工作原理 ...................................................................................................................................................... 527

xxxvi

[www.it-ebooks.info](http://www.it-ebooks.info/)

N 目录

19-2\. 发送电子邮件 ........................................................................................................... 528

问题 ............................................................................................................................................................. 528

解决方案.............................................................................................................................................................. 528

工作原理 ...................................................................................................................................................... 529

19-3\. 在电子邮件中附加文件 .............................................................................. 529

问题 ............................................................................................................................................................. 529

解决方案.............................................................................................................................................................. 529

工作原理 ...................................................................................................................................................... 530

19-4\. 发送 HTML 格式的电子邮件 ................................................................................................. 530

问题 ............................................................................................................................................................. 530

解决方案.............................................................................................................................................................. 530

工作原理 ...................................................................................................................................................... 531

19-5\. 向一组收件人发送电子邮件 ......................................................................... 531

问题 ............................................................................................................................................................. 531

解决方案.............................................................................................................................................................. 531

工作原理 ...................................................................................................................................................... 532

19-6\. 检查电子邮件 ............................................................................................................... 532

问题 ............................................................................................................................................................. 532

解决方案.............................................................................................................................................................. 532

工作原理 ...................................................................................................................................................... 533

19-7\. 监控电子邮件账户 .......................................................................................... 533

问题 ............................................................................................................................................................. 533

解决方案.............................................................................................................................................................. 533

工作原理 ...................................................................................................................................................... 535

小结 ................................................................................................................................... 535

第 20 章：XML 处

N

理 ......................................................................................... 537

20-1\. 编写 XML 文件 .......................................................................................................... 537

问题 ............................................................................................................................................................. 537

解决方案.............................................................................................................................................................. 537

工作原理 ...................................................................................................................................................... 539

xxxvii

[www.it-ebooks.info](http://www.it-ebooks.info/)

N 目录

20-2\. 读取 XML 文件 ........................................................................................................ 540

问题 ............................................................................................................................................................. 540

解决方案 1........................................................................................................................................................... 540



解决方案 2........................................................................................................................................................... 541

工作原理 ...................................................................................................................................................... 542

20-3\. 转换 XML ............................................................................................................ 543

问题 ............................................................................................................................................................. 543

解决方案.............................................................................................................................................................. 543

工作原理 ...................................................................................................................................................... 544

20-4\. 验证 XML ................................................................................................................. 546

问题 ............................................................................................................................................................. 546

解决方案.............................................................................................................................................................. 546

工作原理 ...................................................................................................................................................... 547

20-5\. 为 XML 模式创建 Java 绑定 .................................................................... 548

问题 ............................................................................................................................................................. 548

解决方案.............................................................................................................................................................. 548

工作原理 ...................................................................................................................................................... 548

20-6\. 将 XML 解组为 Java 对象 ............................................................................... 549

问题 ............................................................................................................................................................. 549

解决方案.............................................................................................................................................................. 549

工作原理 ...................................................................................................................................................... 550

20-7\. 使用 JAXB 构建 XML 文档 ............................................................................ 550

问题 ............................................................................................................................................................. 550

解决方案.............................................................................................................................................................. 550

工作原理 ...................................................................................................................................................... 551

总结 ................................................................................................................................... 551

第 21 章：网络 ............................................................................................... 553

21-1\. 在服务器上监听连接 .......................................................................... 553

问题 ............................................................................................................................................................. 553

解决方案.............................................................................................................................................................. 553

工作原理 ...................................................................................................................................................... 555

xxxviii

[www.it-ebooks.info](http://www.it-ebooks.info/)

目录

21-2\. 定义到服务器的网络连接 ..................................................................... 556

问题 ............................................................................................................................................................. 556

解决方案.............................................................................................................................................................. 556

工作原理 ...................................................................................................................................................... 558

21-3\. 绕过 TCP 使用 InfiniBand 以提升性能 ............................................. 559

问题 ............................................................................................................................................................. 559

解决方案.............................................................................................................................................................. 559

工作原理 ...................................................................................................................................................... 560

21-4\. 向一组接收者广播 ............................................................................ 561

问题 ............................................................................................................................................................. 561

解决方案.............................................................................................................................................................. 561

工作原理 ...................................................................................................................................................... 564

21-5\. 生成和读取 URL ................................................................................. 566

问题 ............................................................................................................................................................. 566

解决方案.............................................................................................................................................................. 566



解决方案.............................................................................................................................................................. 566

工作原理 ...................................................................................................................................................... 567

21-6\. 解析 URL .................................................................................................................. 568

问题 ............................................................................................................................................................. 568

解决方案.............................................................................................................................................................. 568

工作原理 ...................................................................................................................................................... 569

总结 ................................................................................................................................... 570

第 22 章：安全

N

...................................................................................................... 571

22-1\. 断言权限子集 ...................................................................................... 572

问题 ............................................................................................................................................................. 572

解决方案.............................................................................................................................................................. 572

工作原理 ...................................................................................................................................................... 572

22-2\. 指定握手服务器名称 ..................................................................... 573

问题 ............................................................................................................................................................. 573

解决方案.............................................................................................................................................................. 573

工作原理 ...................................................................................................................................................... 575

xxxix

[www.it-ebooks.info](http://www.it-ebooks.info/)

N 目录

22-3\. 指定密钥库密码 ...................................................................................... 575

问题 ............................................................................................................................................................. 575

解决方案.............................................................................................................................................................. 575

工作原理 ...................................................................................................................................................... 576

22-4\. 生成可能的最强随机数 ....................................................... 576



问题 ............................................................................................................................................................. 576

解决方案.............................................................................................................................................................. 576

工作原理 ...................................................................................................................................................... 576

22-5\. 为富互联网应用程序授予权限 ........................................................................................ 576

问题 ............................................................................................................................................................. 576

解决方案.............................................................................................................................................................. 576

工作原理 ...................................................................................................................................................... 577

总结 ................................................................................................................................... 578

索引 ................................................................................................................................. 579

xl

[www.it-ebooks.info](http://www.it-ebooks.info/)

![](img/index-625_1.jpg)

**关于作者**

**Josh Juneau** 自 Java EE 早期以来一直从事软件和企业应用程序的开发。自职业生涯开始以来，应用程序和数据库开发一直是他工作的重点。他成为了一名 Oracle 数据库管理员，并采用 PL/SQL 语言来执行管理任务和为 Oracle 数据库开发应用程序。为了构建更复杂的解决方案，他开始将 Java 集成到他的 PL/SQL 应用程序中，后来使用 Java 开发了独立和 Web 应用程序。Josh 早期使用 JDBC 和 Servlet 或 JSP 编写 Java Web 应用程序，以与后端数据库交互。后来，他开始将框架（如 Java EE 和 JBoss Seam）集成到他的企业解决方案中。如今，他主要使用 Java EE 和其他技术开发企业 Web 解决方案。他还在一些项目中使用了其他语言，例如 Jython 和 Groovy。

多年来，Josh 涉猎了许多不同的编程语言，特别是 JVM 上的替代语言。2006 年，Josh 开始投入时间到 Jython 项目，担任 *Jython Monthly* 新闻通讯的编辑和出版人。2008 年底，他开始制作一个专门介绍 Jython 编程语言的播客。

Josh 是《The Definitive Guide to Jython》、《Oracle PL/SQL Recipes》和《Java 7 Recipes》的主要作者，也是《Java EE 7 Recipes》和《Introducing Java EE 7》的唯一作者，这些书均由 Apress 出版。他在费米国家加速器实验室担任应用程序开发人员和系统分析师，并为 Oracle 和 OTN 撰写技术文章。

Josh 喜欢与他的好妻子和五个孩子共度时光，尤其喜欢游泳、钓鱼、打球和看电影。要了解更多 Josh 的信息，请关注他的博客 http://jj-blogger.blogspot.com。您也可以在 Twitter 上关注他：@javajuneau。

xli

[www.it-ebooks.info](http://www.it-ebooks.info/)

![](img/index-626_1.jpg)

![](img/index-626_2.jpg)

**关于技术审阅者**

**Rohan Walia** 是一位高级软件顾问，在客户端-服务器、基于 Web 和企业应用程序开发方面拥有丰富的经验。他是 Oracle 认证的 ADF 实施专家和 Sun 认证的 Java 程序员。Rohan 负责设计和开发由各种前沿框架和实用程序组成的端到端应用程序。他的专业领域是 Oracle ADF、Oracle WebCenter、Fusion、Spring、Hibernate 和 Java/J2EE。不工作时，Rohan 喜欢打网球、远足和旅行。Rohan 要感谢他的妻子 Deepika Walia，感谢她运用自己的所有经验和专业知识审阅本书。

**James Weaver** 是一位专注于客户端 Java 和物联网的开发人员、作者、教师和国际演讲者。他的 Twitter 账号是 @JavaFXpert。

xliii

[www.it-ebooks.info](http://www.it-ebooks.info/)

**致谢**

致我的妻子 Angela：时光流逝，我依然对你感到惊叹，并将永远如此。我想再次感谢你一直陪伴在我和孩子们身边。你帮助我完成了这本书，你的鼓励一直激励着我前进。感谢你一直支持我的工作。我非常爱你。

致我的孩子们，Kaitlyn、Jacob、Matthew、Zachary 和 Lucas：我非常爱你们，我珍惜我们在一起的每一刻。你们在学业、童子军活动、体育以及你们取得的无数其他成就中，一直让我感到无比自豪。我不愿看到你们成长得如此之快……有时我希望时间能够暂停。

我希望将来有一天，当你们读到这本书时，能够理解我为什么在周末如此努力地工作。

我要感谢本书第一版的原始合著者：Carl Dea、Freddy Guime、John O'Conner 和 Mark Beaty。你们精通自己的领域，你们的专业知识如同融入第一版一样，也深深融入了这第二版的字里行间。与你们合作完成第一版非常愉快，希望将来能再次合作。

致 Apress 的朋友们：感谢你们给我机会与他人分享我的知识。我特别感谢 Jonathan Gennick 对我工作的持续支持，并不断提供指导，以便为我们的读者创作有用的内容。我还要感谢 Jill Balzano 在协调这个项目方面所做的出色工作，以及 Kezia Endsley 的编辑工作。技术审阅者 Jim Weaver 和 Rohan Walia 在巩固本书内容方面做得非常出色。我衷心感谢你们的辛勤工作和专业知识。最后，我要感谢 Apress 所有参与本书工作的其他人。

致 Java 社区：感谢你们帮助 Java 平台成为应用程序开发领域如此创新和高效的领域。我们都有幸使用一个成熟且稳健的平台，如果没有大家对该技术的持续贡献，它今天就不会如此成功。我还要感谢所有 Oracle Java 专家。再次强调，未来的路线图依然光明。我期待在未来许多年里继续使用 Java 技术。

xlv

[www.it-ebooks.info](http://www.it-ebooks.info/)

