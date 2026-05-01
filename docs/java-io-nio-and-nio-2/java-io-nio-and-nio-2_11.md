# 8. 选择器

电子补充材料 本章的在线版本 (doi:[10.​1007/​978-1-4842-1565-4_​8](http://dx.doi.org/10.1007/978-1-4842-1565-4_8)) 包含补充材料，可供授权用户使用。

I/O 要么是面向块的（例如文件 I/O），要么是面向流的（例如网络 I/O）。流通常比块设备（如固定磁盘）慢，并且读/写操作常常会导致调用线程阻塞，直到输入可用或输出已完全写入。为了弥补这一点，现代操作系统允许流以非阻塞模式运行，这使得线程能够在不阻塞的情况下读取或写入数据。操作要么完全成功，要么指示部分成功。无论哪种情况，线程都能够执行其他有用的工作，而不是等待。

非阻塞模式并不能让应用程序在不实际执行操作的情况下确定它是否可以执行某个操作。例如，当非阻塞读取操作成功时，应用程序得知读取操作是可行的，但同时也读取了一些必须管理的数据。这种双重性使得你无法在不使代码变得极其复杂的情况下，将检查流就绪状态的代码与数据处理代码分离开来。

非阻塞模式是执行就绪选择的基础，它将检查 I/O 流是否就绪以执行写入、读取和其他操作（而无需实际执行这些操作）的工作卸载给操作系统。操作系统被指示观察一组流，并返回一些指示，表明哪些流已准备好执行特定操作（如读取）或多个操作（如接受和读取）。这种能力使得一个线程能够利用操作系统提供的就绪信息，多路复用潜在的大量活动流。通过这种方式，网络服务器可以处理大量的网络连接；它们具有极强的可扩展性。

注意

现代操作系统通过提供诸如 POSIX `select()` 调用之类的系统调用，使应用程序能够进行就绪选择。

选择器让你能够在 Java 环境中实现就绪选择。本章将向你介绍选择器的基础知识，然后提供一个演示。

## 选择器基础

选择器是一个从抽象类 `java.nio.channels.Selector` 的子类创建的对象。选择器维护一组通道，并检查这些通道以确定哪些通道已准备好进行读取、写入、完成连接序列、接受另一个连接，或这些任务的某种组合。实际工作通过 POSIX `select()` 或类似的系统调用委托给操作系统。

注意

在某个事物未就绪时（例如字节不可读）无需等待就能检查通道，并且在检查时也无需执行操作，这种能力是可扩展性的关键。单个线程可以管理大量通道，从而降低了代码复杂性和潜在的线程问题。

选择器与可选择通道一起使用，可选择通道是其类最终继承自抽象类 `java.nio.channels.SelectableChannel` 的对象，该类描述了一个可以被选择器多路复用的通道。套接字通道、服务器套接字通道、数据报通道以及管道源/汇通道都是可选择通道，因为 `java.nio.channels.SocketChannel`、`java.nio.channels.ServerSocketChannel`、`java.nio.channels.DatagramChannel`、`java.nio.channels.Pipe.SinkChannel` 和 `java.nio.channels.Pipe.SourceChannel` 都派生自 `SelectableChannel`。相比之下，文件通道不是可选择通道，因为 `java.nio.channels.FileChannel` 的继承链中不包含 `SelectableChannel`。

一个或多个先前创建的可选择通道会注册到一个选择器上。每次注册都会返回一个抽象类 `SelectionKey` 的子类实例，该实例是一个令牌，表示一个通道与选择器之间的关系。这个键跟踪两组操作：兴趣集和就绪集。兴趣集标识了在下一次调用选择器的某个选择方法时将测试就绪状态的操作类别。就绪集标识了该键的通道已被其选择器发现已就绪的操作类别。当调用选择方法时，通过检查所有注册到该选择器的通道来更新选择器关联的键。然后，应用程序可以获取一组其通道已被发现就绪的键，并遍历这些键以处理自上次 `select` 方法调用以来已就绪的每个通道。

注意

一个可选择通道可以注册到多个选择器。它不知道当前注册到了哪些选择器。

要使用选择器，首先需要创建一个。你可以通过调用 `Selector` 的 `Selector open()` 类方法来完成此任务，该方法在成功时返回一个 `Selector` 实例，在失败时抛出 `java.io.IOException`。以下代码片段演示了此任务：

`Selector selector = Selector.open();`

你可以在创建选择器之前或之后创建你的可选择通道。但是，在将通道注册到选择器之前，必须确保每个通道都处于非阻塞模式。你可以通过调用以下任一 `SelectableChannel` 注册方法将可选择通道注册到选择器：

*   `SelectionKey register(Selector sel, int ops)`
*   `SelectionKey register(Selector sel, int ops, Object att)`

每个方法都要求你将先前创建的选择器传递给 `sel`，并将以下 `SelectionKey` 的 `int` 类型常量的按位 OR 组合传递给 `ops`，该参数表示兴趣集：

*   `OP_ACCEPT`：用于套接字接受操作的操作集位。
*   `OP_CONNECT`：用于套接字连接操作的操作集位。
*   `OP_READ`：用于读取操作的操作集位。
*   `OP_WRITE`：用于写入操作的操作集位。



第二种方法还允许您将一个任意的 `java.lang.Object` 或子类实例（或 `null`）传递给 `att`。非空对象被称为附件，它是识别特定通道或为通道附加额外信息的一种便捷方式。该对象存储在此方法返回的 `SelectionKey` 实例中。

成功时，每个方法都会返回一个 `SelectionKey` 实例，该实例将可选择通道与选择器关联起来。失败时，则会抛出异常。例如，当通道关闭时会抛出 `java.nio.channels.ClosedChannelException`，当通道未设置为非阻塞模式时会抛出 `java.nio.channels.IllegalBlockingModeException`。

以下代码片段扩展了之前的代码片段，将先前创建的通道配置为非阻塞模式，并将该通道注册到选择器。选择方法会测试通道是否已准备好进行接受、读取和写入操作：

`channel.configureBlocking(false);`

`SelectionKey key = channel.register(selector, SelectionKey.OP_ACCEPT |`

`SelectionKey.OP_READ |`

`SelectionKey.OP_WRITE);`

此时，应用程序通常会进入一个无限循环，在此循环中完成以下任务：

执行选择操作。   获取已选择的键，然后通过迭代器遍历这些键。   遍历这些键并执行通道操作。

选择操作通过调用 `Selector` 的某个选择方法来执行。例如，`int select()` 执行阻塞选择操作。它直到至少有一个通道被选中、此选择器的 `wakeup()` 方法被调用、或当前线程被中断时才会返回，以先发生者为准。

注意

`Selector` 还声明了一个 `int select(long timeout)` 方法，该方法直到至少有一个通道被选中、此选择器的 `wakeup()` 方法被调用、当前线程被中断、或 `timeout` 值到期时才会返回，以先发生者为准。此外，`Selector` 声明了 `int selectNow()`，这是 `select()` 的非阻塞版本。

`select()` 方法返回自上次调用以来已就绪的通道数量。例如，如果您调用 `select()` 并且它返回 `1`，因为一个通道已就绪；如果您再次调用 `select()` 并且第二个通道已就绪，`select()` 将再次返回 `1`。如果您尚未处理第一个就绪的通道，那么您现在有两个就绪通道需要处理。然而，在两次 `select()` 调用之间，只有一个通道变为就绪状态。

现在，通过调用 `Selector` 的 `Set < SelectionKey > selectedKeys()` 方法来获取已选择键的集合（就绪集合）。调用该集合的 `iterator()` 方法以获取遍历这些键的迭代器。

最后，应用程序遍历这些键。每次迭代都会返回一个 `SelectionKey` 实例。会调用 `SelectionKey` 的 `boolean isAcceptable()`、`boolean isConnectable()`、`boolean isReadable()` 和 `boolean isWritable()` 方法中的某些组合，以确定该键是否指示通道已准备好接受连接、已完成连接、可读或可写。

注意

上述方法提供了一种便捷的替代方案，无需编写诸如 `key.readyOps() & OP_READ != 0` 这样的表达式。`SelectionKey` 的 `int readyOps()` 方法返回该键的就绪集合。返回的集合仅包含对此键的通道有效的操作位。例如，它永远不会返回指示只读通道已准备好写入的操作位。请注意，每个可选择通道还声明了一个 `int validOps()` 方法，该方法返回对该通道有效的操作按位或的结果集。

一旦应用程序确定某个通道已准备好执行特定操作，它可以调用 `SelectionKey` 的 `SelectableChannel channel()` 方法来获取该通道，然后在该通道上执行工作。

注意

`SelectionKey` 还声明了一个 `Selector selector()` 方法，该方法返回创建该键时所针对的选择器。

当您完成对某个通道的处理后，必须从键集合中移除该键；选择器不会执行此任务。下次该通道变为就绪状态时，`Selector` 会将该键添加到已选择的键集合中。

以下代码片段延续了之前的代码片段，并演示了上述任务：

`while (true)`

`{`

`int numReadyChannels = selector.select();`

`if (numReadyChannels == 0)`

`continue; // 没有需要处理的就绪通道`

`Set<SelectionKey> selectedKeys = selector.selectedKeys();`

`Iterator<SelectionKey> keyIterator = selectedKeys.iterator();`

`while (keyIterator.hasNext())`

`{`

`SelectionKey key = keyIterator.next();`

`if (key.isAcceptable())`

`{`

`// 一个连接已被 ServerSocketChannel 接受。`

`ServerSocketChannel server = (ServerSocketChannel) key.channel();`

`SocketChannel client = server.accept();`

`if (client == null) // 以防 accept() 返回 null`

`continue;`

`client.configureBlocking(false); // 必须为非阻塞`

`// 将套接字通道注册到选择器以进行读取操作。`

`client.register(selector, SelectionKey.OP_READ);`

`}`

`else`

`if (key.isReadable())`

`{`

`// 一个套接字通道已准备好进行读取。`

`SocketChannel client = (SocketChannel) key.channel();`

`// 在套接字通道上执行工作。`

`}`

`else`

`if (key.isWritable())`

`{`

`// 一个套接字通道已准备好进行写入。`

`SocketChannel client = (SocketChannel) key.channel();`

`// 在套接字通道上执行工作。`

`}`

`keyIterator.remove();`

`}`

`}`

除了将服务器套接字通道注册到选择器之外，每个传入的客户端套接字通道也会注册到服务器套接字通道。当客户端套接字通道准备好进行读取或写入操作时，关联套接字通道的 `key.isReadable()` 或 `key.isWritable()` 会返回 true，并且可以对该套接字通道进行读取或写入。

一个键表示一个可选择通道与一个选择器之间的关系。可以通过调用 `SelectionKey` 的 `void cancel()` 方法来终止此关系。返回后，该键将失效，并会被添加到其选择器的已取消键集合中。在下一次选择操作期间，该键将从选择器的所有键集合中移除。

当您完成对选择器的使用后，请调用 `Selector` 的 `void close()` 方法。如果某个线程当前正阻塞在此选择器的某个选择方法中，该线程会被中断，就像调用了选择器的 `wakeup()` 方法一样。任何仍与此选择器关联的未取消键都将失效，其通道将被取消注册，并且与此选择器关联的任何其他资源都将被释放。如果此选择器已关闭，则调用 `close()` 不会产生任何效果。



## 选择器演示

选择器在服务器应用程序中很常用。清单 8-1 展示了一个向客户端发送本地时间的服务器应用程序的源代码。

**清单 8-1\. 向客户端提供时间服务**

`import java.io.IOException;`

`import java.net.InetSocketAddress;`

`import java.net.ServerSocket;`

`import java.nio.ByteBuffer;`

`import java.nio.channels.SelectionKey;`

`import java.nio.channels.Selector;`

`import java.nio.channels.ServerSocketChannel;`

`import java.nio.channels.SocketChannel;`

`import java.util.Iterator;`

`public class SelectorServer`

`{`

`final static int DEFAULT_PORT = 9999;`

`static ByteBuffer bb = ByteBuffer.allocateDirect(8);`

`public static void main(String[] args) throws IOException`

`{`

`int port = DEFAULT_PORT;`

`if (args.length > 0)`

`port = Integer.parseInt(args[0]);`

`System.out.println("Server starting ... listening on port " + port);`

`ServerSocketChannel ssc = ServerSocketChannel.open();`

`ServerSocket ss = ssc.socket();`

`ss.bind(new InetSocketAddress(port));`

`ssc.configureBlocking(false);`

`Selector s = Selector.open();`

`ssc.register(s, SelectionKey.OP_ACCEPT);`

`while (true)`

`{`

`int n = s.select();`

`if (n == 0)`

`continue;`

`Iterator it = s.selectedKeys().iterator();`

`while (it.hasNext())`

`{`

`SelectionKey key = (SelectionKey) it.next();`

`if (key.isAcceptable())`

`{`

`SocketChannel sc;`

`sc = ((ServerSocketChannel) key.channel()).accept();`

`if (sc == null)`

`continue;`

`System.out.println("Receiving connection");`

`bb.clear();`

`bb.putLong(System.currentTimeMillis());`

`bb.flip();`

`System.out.println("Writing current time");`

`while (bb.hasRemaining())`

`sc.write(bb);`

`sc.close();`

`}`

`it.remove();`

`}`

`}`

`}`

`}`

清单 8-1 的服务器应用程序包含一个 `SelectorServer` 类。该类在加载后会分配一个直接字节缓冲区。

当执行 `main()` 方法时，它首先检查命令行参数，该参数假定代表一个端口号。如果未指定参数，则使用默认端口号；否则，`main()` 尝试通过将该参数传递给 `Integer.parseInt()` 将其转换为代表端口的整数。（当传入非整数参数时，此方法会抛出 `java.lang.NumberFormatException`。）

在输出一条标识监听端口的启动消息后，`main()` 获取一个服务器套接字通道，随后获取底层套接字，并将其绑定到指定端口。然后将服务器套接字通道配置为非阻塞模式，以便将该通道注册到选择器。

现在获取一个选择器，服务器套接字通道将自己注册到该选择器，以便了解通道何时准备好执行接受操作。返回的键不会被保存，因为它永远不会被取消（并且选择器也永远不会被关闭）。

`main()` 现在进入一个无限循环，首先调用选择器的 `select()` 方法。如果服务器套接字通道未就绪（`select()` 返回 `0`），则跳过循环的其余部分。

现在获取选定的键（示例中只有一个键）以及用于遍历它们的迭代器，然后 `main()` 进入一个内部循环来遍历这些键。调用每个键的 `isAcceptable()` 方法以查明服务器套接字通道是否准备好执行接受操作。如果是这种情况，则获取该通道并将其转换为 `ServerSocketChannel`，然后调用 `ServerSocketChannel` 的 `accept()` 方法来接受新连接。

为了防止返回的 `SocketChannel` 实例为 null 这种不太可能的情况（当服务器套接字通道处于非阻塞模式且没有可接受的连接时，`accept()` 返回 null），`main()` 会测试这种情况，并在检测到 null 时继续循环。

输出一条关于接收连接的消息，并清除字节缓冲区以准备存储本地时间。在将此长整数存储到缓冲区后，翻转缓冲区以准备排出。输出一条关于写入当前时间的消息，然后排出缓冲区。接着关闭套接字通道，并将该键从键集中移除。

按如下方式编译清单 8-1：

`javac SelectorServer.java`

按如下方式运行生成的应用程序：

`java SelectorServer`

您应该会看到以下输出，并且服务器应继续运行：

`Server starting ... listening on port 9999`

我们需要一个客户端来测试这个服务器。清单 8-2 展示了一个示例客户端应用程序的源代码。

**清单 8-2\. 从服务器接收时间**

`import java.io.IOException;`

`import java.net.InetSocketAddress;`

`import java.nio.ByteBuffer;`

`import java.nio.channels.SocketChannel;`

`import java.util.Date;`

`public class SelectorClient`

`{`

`final static int DEFAULT_PORT = 9999;`

`static ByteBuffer bb = ByteBuffer.allocateDirect(8);`

`public static void main(String[] args)`

`{`

`int port = DEFAULT_PORT;`

`if (args.length > 0)`

`port = Integer.parseInt(args[0]);`

`try`

`{`

`SocketChannel sc = SocketChannel.open();`

`InetSocketAddress addr = new InetSocketAddress("localhost", port);`

`sc.connect(addr);`

`long time = 0;`

`while (sc.read(bb) != -1)`

`{`

`bb.flip();`

`while (bb.hasRemaining())`

`{`

`time <<= 8;`

`time |= bb.get() & 255;`

`}`

`bb.clear();`

`}`

`System.out.println(new Date(time));`

`sc.close();`

`}`

`catch (IOException ioe)`

`{`

`System.err.println("I/O error: " + ioe.getMessage());`

`}`

`}`

`}`

清单 8-2 比清单 8-1 简单得多，因为没有使用选择器。在这个简单的应用程序中不需要选择器。当客户端与多个服务器交互时，通常会在客户端上下文中使用选择器。

源代码中有几个有趣的地方：

*   `bb.get()` 返回一个 8 位字节的 32 位整数表示。对于大于 127 的字节值，会使用符号扩展，这些值被视为负数。由于前导 1 位在与 `time` 进行按位或运算后会影响结果，因此通过将整数与 255 进行按位与运算来移除它们。
*   当构造新的 `Date` 对象时，`time` 中的这个值会传递给 `java.util.Date(long time)` 构造函数。接着，`Date` 对象被传递给 `System.out.println()`，后者会调用 `Date` 的 `toString()` 方法来获取人类可读的日期/时间字符串。

按如下方式编译清单 8-2：

`javac SelectorClient.java`

在第二个命令窗口中，按如下方式运行生成的应用程序：

`java SelectorClient`

您应该会看到类似于以下的输出：

`Tue Jul 28 13:38:20 CDT 2015`

在服务器命令窗口中，您应该会看到以下消息：

`Receiving connection`

`Writing current time`

**练习题**

以下练习题旨在测试您对第 8 章内容的理解：

定义选择器。   识别支持选择器的三种主要类型。   判断对错：文件通道可以与选择器一起使用。   `SelectionKey` 提供了什么作为表达式 `key.readyOps() & OP_READ != 0` 的便捷替代方案？



## 摘要

选择器（selector）是从抽象类 `Selector` 的子类创建的对象。选择器维护一组通道，通过检查这些通道来确定哪些通道已准备好进行读取、写入、完成连接序列、接受另一个连接或这些任务的某种组合。

选择器与可选择的通道一起使用，这些通道的类最终继承自抽象类 `SelectableChannel`，该类描述了一个可由选择器多路复用的通道。

一个或多个先前创建的可选择通道会注册到选择器上。每次注册都会返回抽象类 `SelectionKey` 的一个子类实例，该实例是一个令牌，表示一个通道与选择器之间的关系。当调用选择方法时，通过检查所有注册到该选择器的通道来更新选择器关联的键。然后，应用程序可以获取一组键，这些键对应的通道已被发现处于就绪状态，并遍历这些键，以服务自上次 `select` 方法调用以来变为就绪的每个通道。

第 9 章介绍了 NIO 对正则表达式的支持。

