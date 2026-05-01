# 14. 套接字通道功能的完善

电子补充材料 本章的在线版本 (doi:[10.​1007/​978-1-4842-1565-4_​14](http://dx.doi.org/10.1007/978-1-4842-1565-4_14)) 包含补充材料，可供授权用户使用。

套接字通道功能的完善是 JDK 7 对 NIO.2 的最终贡献。`java.nio.channels` 包中的 `DatagramChannel`、`ServerSocketChannel` 和 `SocketChannel` 类已得到扩展，以支持绑定和选项配置。此外，还支持基于通道的多播。本章通过向你介绍这些功能来结束本书。



## 绑定与选项配置

NIO 的 `DatagramChannel`、`ServerSocketChannel` 和 `SocketChannel` 类并未完全抽象网络套接字。要绑定到通道的套接字，或获取/设置套接字选项，你必须先通过调用每个类的 `socket()` 方法来获取对等套接字。

这种违反直觉的套接字通道与套接字 API 混合存在的原因，是 JDK 1.4 发布时没有足够的时间来定义完整的套接字通道 API。JDK 7 通过引入 `java.nio.channels.NetworkChannel` 接口解决了这个问题。

`NetworkChannel` 代表一个通往网络套接字的通道。该接口由 `DatagramChannel`、`ServerSocketChannel`、`SocketChannel`、`java.nio.channels.AsynchronousServerSocketChannel`（参见第 13 章）和 `java.nio.channels.AsynchronousSocketChannel`（参见第 13 章）实现。

表 14-1 展示了 `NetworkChannel` 的方法。

表 14-1.

定义网络通道的方法

| 方法 | 描述 |
| --- | --- |
| [`NetworkChannel`](https://docs.oracle.com/javase/8/docs/api/java/nio/channels/NetworkChannel.html#interface%20in%20java.nio.channels) `bind(` [`SocketAddress`](https://docs.oracle.com/javase/8/docs/api/java/net/SocketAddress.html#class%20in%20java.net) `local)` | 将此通道的套接字绑定到本地地址。一旦建立关联，套接字将保持绑定状态，直到此通道关闭。如果你向 `local` 传递 `null`，套接字将绑定到一个自动分配的地址。`bind()` 成功时返回此通道。否则，当套接字已绑定时抛出 `java.nio.channels.AlreadyBoundException`，当给定地址类型不受支持时抛出 `java.nio.channels.UnsupportedAddressTypeException`，当此通道已关闭时抛出 `java.nio.channels.ClosedChannelException`，当发生其他 I/O 错误时抛出 `java.io.IOException`。 |
| [`SocketAddress`](https://docs.oracle.com/javase/8/docs/api/java/net/SocketAddress.html#class%20in%20java.net) `getLocalAddress()` | 返回此通道套接字所绑定的套接字地址。当此通道[绑定](https://docs.oracle.com/javase/8/docs/api/java/nio/channels/NetworkChannel.html#bind-java.net.SocketAddress-)到互联网协议（IP）套接字地址时，此方法的返回值类型为 `java.net.` [`InetSocketAddress`](https://docs.oracle.com/javase/8/docs/api/java/net/InetSocketAddress.html#class%20in%20java.net)。当此通道已关闭时，`getLocalAddress()` 抛出 `ClosedChannelException`；当发生 I/O 错误时，抛出 `IOException`。 |
| `<T> T getOption (` [`SocketOption`](https://docs.oracle.com/javase/8/docs/api/java/net/SocketOption.html#interface%20in%20java.net) `<T> name)` | 返回由 `name` 标识的套接字选项的值。对于某些套接字选项，返回 `null` 可能是有效的。当此通道不支持此套接字选项时，`getOption()` 抛出 `java.lang.UnsupportedOperationException`；当此通道已关闭时，抛出 `ClosedChannelException`；当发生 I/O 错误时，抛出 `IOException`。 |
| `<T>` [`NetworkChannel`](https://docs.oracle.com/javase/8/docs/api/java/nio/channels/NetworkChannel.html#interface%20in%20java.nio.channels) `setOption (` [`SocketOption`](https://docs.oracle.com/javase/8/docs/api/java/net/SocketOption.html#interface%20in%20java.net) `<T> name, T value)` | 将由 `name` 标识的套接字选项的值设置为 `value`。对于某些套接字选项，向 `value` 传递 `null` 可能是有效的。`setOption()` 返回此通道。当此通道不支持此套接字选项时，抛出 `UnsupportedOperationException`；当传递给 `value` 的值对此套接字选项无效时，抛出 `java.lang.IllegalArgumentException`；当此通道已关闭时，抛出 `ClosedChannelException`；当发生 I/O 错误时，抛出 `IOException`。 |
| [`Set`](https://docs.oracle.com/javase/8/docs/api/java/util/Set.html#interface%20in%20java.util) `<` [`SocketOption`](https://docs.oracle.com/javase/8/docs/api/java/net/SocketOption.html#interface%20in%20java.net) `<?>> supportedOptions()` | 返回此通道支持的套接字选项集合。即使在此通道关闭后，此方法仍会继续返回该选项集合。 |

传递给 `getOption()` 和 `setOption()` 的 `name` 参数必须是 `java.net.SocketOption` 接口类型。JDK 7 还新增了 `java.net.StandardSocketOptions` 类，该类实现了此接口并枚举了各种 `SocketOption` 常量，例如 `SO_RCVBUF`（套接字接收缓冲区大小）和 `TCP_NODELAY`（禁用 Nagle 算法）。



实现 `NetworkChannel` 接口的类并不支持 `StandardSocketOptions` 常量所标识的所有套接字选项。例如，`ServerSocketChannel` 仅支持 `SO_RCVBUF` 和 `SO_REUSEADDR`。每个类的 Java 文档都标识了其支持的常量。如有疑问，可调用 `supportedOptions()` 方法来查明支持哪些选项。

除了提供 `NetworkChannel` 之外，JDK 7 还通过几个有用的方法升级了 `DatagramChannel`、`ServerSocketChannel` 和 `SocketChannel`：

*   `DatagramChannel` 新增了一个 [`SocketAddress`](https://docs.oracle.com/javase/8/docs/api/java/net/SocketAddress.html#class%20in%20java.net) `getRemoteAddress()` 方法，该方法返回此通道的套接字所连接的远程地址。
*   `ServerSocketChannel` 新增了一个 `ServerSocketChannel bind(SocketAddress local, int backlog)` 方法，该方法还允许你指定最大挂起连接数。
*   `SocketChannel` 新增了 [`SocketChannel`](https://docs.oracle.com/javase/8/docs/api/java/nio/channels/SocketChannel.html#class%20in%20java.nio.channels) `shutdownInput()` 和 [`SocketChannel`](https://docs.oracle.com/javase/8/docs/api/java/nio/channels/SocketChannel.html#class%20in%20java.nio.channels) `shutdownOutput()` 方法，这两个方法分别关闭连接的读取或写入端，而无需关闭此通道。此外，它还新增了一个 `SocketAddress getRemoteAddress()` 方法，该方法返回此通道的套接字所连接的远程地址。

清单 7-7 中的 `ChannelServer` 应用程序在将通道的套接字绑定到本地地址、获取本地地址以及获取远程地址时，必须调用 `socket()`。清单 14-1 通过使其符合 `NetworkChannel` 规范并使用 `getRemoteAddress()` 简化了此代码。

清单 14-1. 演示改进后的 `ServerSocketChannel`

`import java.io.IOException;`

`import java.net.InetSocketAddress;`

`import java.nio.ByteBuffer;`

`import java.nio.channels.ServerSocketChannel;`

`import java.nio.channels.SocketChannel;`

`public class ChannelServer`

`{`

`public static void main(String[] args) throws IOException`

`{`

`System.out.println("Starting server…");`

`ServerSocketChannel ssc = ServerSocketChannel.open();`

`ssc.bind(new InetSocketAddress(9999));`

`ssc.configureBlocking(false);`

`String msg = "Local address: " +` `ssc.getLocalAddress()` `;`

`ByteBuffer buffer = ByteBuffer.wrap(msg.getBytes());`

`while (true)`

`{`

`System.out.print(".");`

`SocketChannel sc = ssc.accept();`

`if (sc != null)`

`{`

`System.out.println();`

`System.out.println("Received connection from " +`

`sc.getRemoteAddress()` `);`

`buffer.rewind();`

`sc.write(buffer);`

`sc.close();`

`}`

`else`

`try`

`{`

`Thread.sleep(100);`

`}`

`catch (InterruptedException ie)`

`{`

`assert false; // shouldn’t happen`

`}`

`}`

`}`

`}`

清单 14-1 将 `ssc.socket().bind(new InetSocketAddress(9999));` 替换为 `ssc.bind(new InetSocketAddress(9999));`，将 `ssc.socket().getLocalSocketAddress();` 替换为 `ssc.getLocalAddress();`，并将 `sc.socket().getRemoteSocketAddress()` 替换为 `sc.getRemoteAddress()`。

清单 14-2 扩展了清单 7-8 中的 `ChannelClient` 应用程序，它通过调用 `supportedOptions()` 来获取一组受支持的选项（随后输出这些选项），并通过调用 `getOption()` 来获取 `SO_RCVBUF` 选项的值（该值也会被输出）。

清单 14-2. 获取受支持的选项和接收缓冲区大小

`import java.io.IOException;`

`import java.net.InetSocketAddress;`

`import java.net.SocketOption;`

`import java.net.StandardSocketOptions;`

`import java.nio.ByteBuffer;`

`import java.nio.channels.SocketChannel;`

`import java.util.Set;`

`public class ChannelClient`

`{`

`public static void main(String[] args)`

`{`

`try`

`{`

`SocketChannel sc = SocketChannel.open();`

`Set<SocketOption<?>> options =` `sc.supportedOptions()` `;`

`for (SocketOption<?> option: options)`

`System.out.println(option);`

`System.out.println(` `sc.getOption(StandardSocketOptions.SO_RCVBUF)` `);`

`sc.configureBlocking(false);`

`InetSocketAddress addr = new InetSocketAddress("localhost", 9999);`

`sc.connect(addr);`

`while (!sc.finishConnect())`

`System.out.println("waiting to finish connection");`

`ByteBuffer buffer = ByteBuffer.allocate(200);`

`while (sc.read(buffer) >= 0)`

`{`

`buffer.flip();`

`while (buffer.hasRemaining())`

`System.out.print((char) buffer.get());`

`buffer.clear();`

`}`

`sc.close();`

`}`

`catch (IOException ioe)`

`{`

`System.err.println("I/O error: " + ioe.getMessage());`

`}`

`}`

`}`

编译清单 14-1 和 14-2，并按照第 7 章所示运行生成的应用程序。我观察到以下 `ChannelClient` 输出：

`SO_KEEPALIVE`

`TCP_NODELAY`

`IP_TOS`

`SO_LINGER`

`SO_SNDBUF`

`SO_RCVBUF`

`SO_REUSEADDR`

`SO_OOBINLINE`

`8192`

`Local address: /0:0:0:0:0:0:0:0:9999`



## 基于通道的多播

JDK 7 引入了对基于通道的 IP 多播的支持，这是一种将 IP 数据报发送给组成员（由单个目标地址标识的零个或多个主机）的传输方式。多播是广播的互联网版本，类似于电视或广播信号从源头发射，信号覆盖区域内拥有合适且活跃接收设备的任何人都可以接收。

组由 D 类 IP 地址标识，这是一个多播组 IPv4 地址，范围从 224.0.0.1 到 239.255.255.255。新的接收者（客户端）通过组的 IP 地址连接到该组，从而加入一个多播组。然后，接收者监听传入的数据报。

JDK 7 引入了 `java.nio.channels.MulticastChannel` 接口来支持多播。`MulticastChannel` 扩展了 `NetworkChannel`，并由 `DatagramChannel` 类实现。它声明了一对用于加入组的 `join()` 方法和一个用于关闭通道的 `close()` 方法。

接收者调用 [`MembershipKey`](https://docs.oracle.com/javase/8/docs/api/java/nio/channels/MembershipKey.html#class%20in%20java.nio.channels) `join(` [`InetAddress`](https://docs.oracle.com/javase/8/docs/api/java/net/InetAddress.html#class%20in%20java.net) `group,` [`NetworkInterface`](https://docs.oracle.com/javase/8/docs/api/java/net/NetworkInterface.html#class%20in%20java.net) `ni)` 方法来加入一个多播组，以开始接收发送到该组的所有数据报。调用时，需要传入组的 IP 地址和要加入该组的网络接口作为参数。当此方法成功时，它会返回一个 `java.nio.channels.MembershipKey` 实例，该实例作为代表组成员身份的令牌。

注意

网络接口由 `java.net.NetworkInterface` 类的实例描述。要获取 `NetworkInterface` 实例，可以调用各种 `NetworkInterface` 类的方法，例如 [`NetworkInterface`](https://docs.oracle.com/javase/8/docs/api/java/net/NetworkInterface.html#class%20in%20java.net) `getByInetAddress(` [`InetAddress`](https://docs.oracle.com/javase/8/docs/api/java/net/InetAddress.html#class%20in%20java.net) `addr)`。

或者，接收者可以调用 [`MembershipKey`](https://docs.oracle.com/javase/8/docs/api/java/nio/channels/MembershipKey.html#class%20in%20java.nio.channels) `join(` [`InetAddress`](https://docs.oracle.com/javase/8/docs/api/java/net/InetAddress.html#class%20in%20java.net) `group,` [`NetworkInterface`](https://docs.oracle.com/javase/8/docs/api/java/net/NetworkInterface.html#class%20in%20java.net) `ni,` [`InetAddress`](https://docs.oracle.com/javase/8/docs/api/java/net/InetAddress.html#class%20in%20java.net) `source)` 方法来加入一个多播组，并从特定的源地址接收数据报。由于成员身份是可累积的，可以使用相同的组和网络接口再次调用此方法，以允许接收由其他源地址发送到该组的数据报。

注意

第一个 `join()` 方法类似于有线电视提供商要求你订阅频道包，其中包含那些你不感兴趣的频道。第二个 `join()` 方法类似于有线电视提供商允许你订阅感兴趣的特定频道。

第二个 `join()` 方法演示了源过滤，你可以根据数据报的源地址对其进行过滤。你可以调用此方法来接收来自特定 IP 源的数据报。这种源过滤形式被称为包含模式过滤。

`MembershipKey` 声明了几个方法：

*   [`MembershipKey`](https://docs.oracle.com/javase/8/docs/api/java/nio/channels/MembershipKey.html#class%20in%20java.nio.channels) `block(` [`InetAddress`](https://docs.oracle.com/javase/8/docs/api/java/net/InetAddress.html#class%20in%20java.net) `source)`：当此成员身份密钥不是特定于源的，并且操作系统支持源过滤时，阻止来自给定 `source` 地址的多播数据报。
*   [`MulticastChannel`](https://docs.oracle.com/javase/8/docs/api/java/nio/channels/MulticastChannel.html#interface%20in%20java.nio.channels) `channel()`：返回创建此成员身份密钥的通道。
*   `void drop()`：放弃成员身份。
*   [`InetAddress`](https://docs.oracle.com/javase/8/docs/api/java/net/InetAddress.html#class%20in%20java.net) `group()`：返回创建此成员身份密钥的多播组。
*   `boolean isValid()`：返回此成员身份是否有效（`true`）或无效（`false`）。
*   [`NetworkInterface`](https://docs.oracle.com/javase/8/docs/api/java/net/NetworkInterface.html#class%20in%20java.net) `networkInterface()`：返回创建此成员身份密钥的网络接口。
*   [`InetAddress`](https://docs.oracle.com/javase/8/docs/api/java/net/InetAddress.html#class%20in%20java.net) `sourceAddress()`：当此成员身份密钥是特定于源时，返回源地址；当此成员身份不是特定于源时，返回 `null`。
*   [`MembershipKey`](https://docs.oracle.com/javase/8/docs/api/java/nio/channels/MembershipKey.html#class%20in%20java.nio.channels) `unblock(` [`InetAddress`](https://docs.oracle.com/javase/8/docs/api/java/net/InetAddress.html#class%20in%20java.net) `source)`：取消阻止先前通过 `block()` 阻止的来自给定 `source` 地址的多播数据报。

你可以使用 `MembershipKey` 的 `block()` 方法来执行第二种形式的源过滤，即加入一个组以接收所有多播数据报，但来自特定 IP 源地址的数据报除外。这种源过滤形式被称为排除模式过滤，如下所示：

`MembershipKey key = dc.join(group, nif).block(source1).block(source2);`

一旦通道加入了组，它就会以与单播数据报相同的方式接收多播数据报。当完成与该组的交互后，它会通过调用 `MembershipKey` 的 `drop()` 方法来放弃其成员身份。在放弃所有组成员身份后，它会调用 `MulticastChannel` 的 `close()` 方法。

要创建多播服务器或多播客户端，需要牢记三个重要事项：



*   创建数据报通道时，需指定与通道将加入的多播组地址类型相对应的[协议](https://docs.oracle.com/javase/8/docs/api/java/net/ProtocolFamily.html#interface%20in%20java.net)族。无法保证一个协议族中套接字对应的通道，能在多播组地址对应另一个协议族时加入并接收多播数据报。例如，一个 [IPv6](https://docs.oracle.com/javase/8/docs/api/java/net/StandardProtocolFamily.html#INET6) 套接字对应的通道能否加入 [IPv4](https://docs.oracle.com/javase/8/docs/api/java/net/StandardProtocolFamily.html#INET) 多播组并接收发送给该组的多播数据报，这取决于具体实现。你可以通过调用 `DatagramChannel` 的 [`DatagramChannel`](https://docs.oracle.com/javase/8/docs/api/java/nio/channels/DatagramChannel.html#class%20in%20java.nio.channels) `open(` [`ProtocolFamily`](https://docs.oracle.com/javase/8/docs/api/java/net/ProtocolFamily.html#interface%20in%20java.net) `family)` 类方法来创建数据报通道。此方法需要一个参数，其类实现了 `java.net.ProtocolFamily` 接口。由于 `ProtocolFamily` 由 `java.net.StandardProtocolFamily` 枚举实现，因此你需要将 `INET`（用于 IPv4）或 `INET6`（用于 IPv6）这两个枚举常量之一传递给 `family` 参数。
*   通道的套接字应绑定到通配符地址。如果套接字绑定到特定地址而非通配符地址，那么套接字是否能接收多播数据报将取决于具体实现。`InetSocketAddress` 类声明了一个 `InetSocketAddress(int port)` 构造方法，用于创建 IP 地址为通配符地址、端口号为指定 `port` 值的套接字地址。
*   在[绑定](https://docs.oracle.com/javase/8/docs/api/java/nio/channels/NetworkChannel.html#bind-java.net.SocketAddress-)套接字之前，应启用 [`SO_REUSEADDR`](https://docs.oracle.com/javase/8/docs/api/java/net/StandardSocketOptions.html#SO_REUSEADDR) 选项。这是为了允许多个组成员绑定到同一地址。

我创建了演示基于通道的多播功能的多播服务器和客户端应用程序。清单 14-3 展示了服务器端代码。

**清单 14-3\. 演示基于通道的多播服务器**

`import java.io.IOException;`

`import java.net.InetAddress;`

`import java.net.InetSocketAddress;`

`import java.net.NetworkInterface;`

`import java.net.StandardProtocolFamily;`

`import java.net.StandardSocketOptions;`

`import java.nio.ByteBuffer;`

`import java.nio.channels.DatagramChannel;`

`import java.nio.channels.MembershipKey;`

`public class ChannelServer`

`{`

`final static int PORT = 9999;`

`public static void main(String[] args) throws IOException`

`{`

`NetworkInterface ni;`

`ni = NetworkInterface.getByInetAddress(InetAddress.getLocalHost());`

`DatagramChannel dc;`

`dc = DatagramChannel.open(StandardProtocolFamily.INET)`

`.setOption(StandardSocketOptions.SO_REUSEADDR,`

`true)`

`.bind(new InetSocketAddress(PORT))`

`.setOption(StandardSocketOptions.IP_MULTICAST_IF,`

`ni);`

`InetAddress group = InetAddress.getByName("239.255.0.1");`

`int i = 0;`

`while (true)`

`{`

`ByteBuffer bb = ByteBuffer.wrap(("line " + i).getBytes());`

`dc.send(bb, new InetSocketAddress(group, PORT));`

`i++;`

`}`

`}`

`}`

`ChannelServer` 的 `main()` 方法首先获取一个网络接口，然后打开并配置一个数据报通道。接着，它获取组 IP 地址并进入一个无限循环，该循环将基于字符串的消息的字节缓冲区发送到该组。

清单 14-4 展示了配套的客户端应用程序。

**清单 14-4\. 演示基于通道的多播客户端**

`import java.io.IOException;`

`import java.net.InetAddress;`

`import java.net.InetSocketAddress;`

`import java.net.NetworkInterface;`

`import java.net.StandardProtocolFamily;`

`import java.net.StandardSocketOptions;`

`import java.nio.ByteBuffer;`

`import java.nio.channels.DatagramChannel;`

`import java.nio.channels.MembershipKey;`

`public class ChannelClient`

`{`

`final static int PORT = 9999;`

`public static void main(String[] args) throws IOException`

`{`

`NetworkInterface ni;`

`ni = NetworkInterface.getByInetAddress(InetAddress.getLocalHost());`

`DatagramChannel dc;`

`dc = DatagramChannel.open(StandardProtocolFamily.INET)`

`.setOption(StandardSocketOptions.SO_REUSEADDR,`

`true)`

`.bind(new InetSocketAddress(PORT))`

`.setOption(StandardSocketOptions.IP_MULTICAST_IF,`

`ni);`

`InetAddress group = InetAddress.getByName("239.255.0.1");`

`MembershipKey key = dc.join(group, ni);`

`ByteBuffer response = ByteBuffer.allocate(50);`

`while (true)`

`{`

`dc.receive(response);`

`response.flip();`

`while (response.hasRemaining())`

`System.out.print((char) response.get());`

`System.out.println();`

`response.clear();`

`}`

`}`

`}`

`ChannelClient` 的 `main()` 方法与 `ChannelServer` 的对应方法相同，直到调用 `join()` 加入指定组 IP 地址的组。然后分配一个字节缓冲区来存储数据报。接收数据报后，翻转缓冲区，输出其内容，然后清除缓冲区。

编译这些清单，并在不同的窗口中运行生成的应用程序。以下是 `ChannelClient` 输出的一部分。

`line 156573`

`line 156574`

`line 156575`

`line 156576`

`line 156577`

`line 156578`

`line 156579`

`line 156580`

`line 156581`

`line 156582`

在另一个窗口中运行第二个 `ChannelClient` 应用程序。你应该会在该窗口中看到类似的输出。

**练习题**

以下练习题旨在测试你对第 14 章内容的理解：

NIO.2 为完善套接字通道功能提供了哪些能力？   JDK 7 如何解决套接字通道和套接字 API 之间违反直觉的混合问题？   指出 `SocketChannel` 的 `shutdownInput()` 和 `shutdownOutput()` 方法抛出的异常。   JDK 7 如何支持多播？   判断正误：[`MembershipKey`](https://docs.oracle.com/javase/8/docs/api/java/nio/channels/MembershipKey.html#class%20in%20java.nio.channels) `join(` [`InetAddress`](https://docs.oracle.com/javase/8/docs/api/java/net/InetAddress.html#class%20in%20java.net) `group,` [`NetworkInterface`](https://docs.oracle.com/javase/8/docs/api/java/net/NetworkInterface.html#class%20in%20java.net) `ni,` [`InetAddress`](https://docs.oracle.com/javase/8/docs/api/java/net/InetAddress.html#class%20in%20java.net) `source)` 方法执行排除模式过滤。   编写一个 `EnumNI` 应用程序，枚举你机器上的网络接口，并输出它们的名称。   修改清单 14-4 中的 `ChannelClient` 应用程序，使其在退出应用程序前，也演示从组中移除成员资格并关闭通道。   创建一个 `StockServer` 应用程序，该应用程序重复广播某只股票的开盘价、最低价、最高价和收盘价（作为浮点值）。每次广播应随机调整最低价、最高价和收盘价，并满足以下约束：最低价不得超过最高价，收盘价不得低于最低价，收盘价不得高于最高价。创建一个 `StockClient` 应用程序，加入一个组以重复接收这些价格并输出。



## 摘要

JDK 7 通过扩展 `DatagramChannel`、`ServerSocketChannel` 和 `SocketChannel` 类以支持绑定和选项配置，完善了套接字通道功能。这些能力在 `NetworkChannel` 接口中声明，上述类及其他类均实现了该接口。

JDK 7 还通过引入基于通道的多播支持，进一步完善了套接字通道功能。该支持包括 `MulticastChannel`（一个扩展了 `NetworkChannel` 的接口）、`MembershipKey` 类，以及 `DatagramChannel` 类中的 `open(ProtocolFamily)` 方法。

附录 A 提供了每章练习题的答案。

