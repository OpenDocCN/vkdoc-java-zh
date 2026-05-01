# 13. 异步 I/O

电子补充材料 本章的在线版本 (doi:[10.​1007/​978-1-4842-1565-4_​13](http://dx.doi.org/10.1007/978-1-4842-1565-4_13)) 包含补充材料，仅供授权用户使用。

NIO 提供了多路复用 I/O（结合了第 7 章讨论的非阻塞 I/O 和第 8 章讨论的就绪选择），以帮助创建高度可扩展的服务器。客户端代码向选择器注册一个套接字通道，以便在通道准备好开始 I/O 时收到通知。

NIO.2 提供了异步 I/O，它允许客户端代码发起一个 I/O 操作，并在操作完成时通知客户端。与多路复用 I/O 类似，异步 I/O 也常用于帮助创建高度可扩展的服务器。

注意

多路复用 I/O 通常用于提供高度可扩展和高性能轮询接口的操作系统——例如 Linux 和 Solaris。异步 I/O 通常用于提供高度可扩展和高性能异步 I/O 设施的操作系统——例如较新的 Windows 操作系统。

本章首先概述异步 I/O，然后探讨异步文件通道、套接字通道和通道组。

## 异步 I/O 概述

`java.nio.channels.AsynchronousChannel` 接口描述了一个异步通道，即支持异步 I/O 操作（读取、写入等）的通道。通过调用返回 future 或需要完成处理程序参数的方法来发起 I/O 操作：

*   `Future<V> operation(...)`：调用 `operation` 并返回一个 `java.util.concurrent.Future<V>` 接口对象，其中 `V` 是 `operation` 的结果类型。可以调用 [`Future`](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/Future.html#interface%20in%20java.util.concurrent) 的方法来检查 I/O 操作是否已完成、等待其完成以及检索结果。
*   `void operation(... A attachment, CompletionHandler<V,? super A> handler)`：使用 `attachment`（一个附加到 I/O 操作的对象，用于在消费结果时提供上下文）和 `handler`（`java.nio.channels.CompletionHandler<V, A>` 接口的实例）作为参数调用 `operation`。`A` 是附件对象的类型。`V` 是 I/O 操作的结果类型。当使用无状态的 `CompletionHandler` 对象来消费多个 I/O 操作的结果时，`attachment` 非常重要。当 I/O 操作完成或失败时，会调用 `handler` 来消费结果。

`CompletionHandler` 声明了以下方法，用于在操作成功完成时消费结果，以及了解操作失败的原因并采取相应措施：

*   `void completed(V result, A attachment)`：当操作成功完成时调用。操作的结果由 `result` 标识，操作发起时附加的对象由 `attachment` 标识。
*   `void failed(Throwable t, A attachment)`：当操作失败时调用。操作失败的原因由 `t` 标识，操作发起时附加的对象由 `attachment` 标识。

调用后，该方法会立即返回。然后你可以调用 `Future` 的方法，或在 `CompletionHandler` 实现中提供代码，以了解 I/O 操作的状态和/或处理 I/O 操作的结果。

取消

`Future` 声明了 `boolean cancel(boolean mayInterruptIfRunning)` 方法来取消执行。此方法会导致所有等待 I/O 操作结果的线程抛出 `java.util.concurrent.`[`CancellationException`](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/CancellationException.html#class%20in%20java.util.concurrent)。底层 I/O 操作是否可以被取消高度依赖于具体实现，因此未作规定。如果取消操作导致通道或其连接到的实体处于不一致状态，则通道将进入特定于实现的错误状态，从而阻止进一步尝试发起与已取消操作类似的 I/O 操作。例如，如果某个操作被取消，但实现无法保证没有从通道读取过字节，则它会将通道置于错误状态。进一步尝试发起操作将导致抛出未指定的运行时异常。类似地，如果某个操作被取消，但实现无法保证没有向通道写入过字节，则后续尝试发起操作将因未指定的运行时异常而失败。



如果调用[`cancel`](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/Future.html#cancel-boolean-)`()` 时，将 `mayInterruptIfRunning` 设置为 `true`，则 I/O 操作可能会通过关闭通道而被中断。在这种情况下，所有等待 I/O 操作结果的线程都会抛出 `CancellationException`，而通道上任何其他未完成的 I/O 操作都会通过抛出 [`java.nio.channels.AsynchronousCloseException`](https://docs.oracle.com/javase/8/docs/api/java/nio/channels/AsynchronousCloseException.html#class%20in%20java.nio.channels) 来完成。

当调用 `cancel()` 来取消读取或写入操作时，建议丢弃 I/O 操作中使用的所有缓冲区，或者注意确保在通道保持打开状态时不会访问这些缓冲区。

`AsynchronousChannel` 扩展了 `java.nio.channels.Channel` 接口（关于 `Channel` 的讨论请参见第 7 章），并继承了其 `isOpen()` 和 `close()` 方法。`close()` 方法需遵循以下附加规定：此通道上任何未完成的异步操作都将通过抛出 [`AsynchronousCloseException`](https://docs.oracle.com/javase/8/docs/api/java/nio/channels/AsynchronousCloseException.html#class%20in%20java.nio.channels) 对象来完成。通道关闭后，任何进一步尝试启动异步 I/O 操作的行为都会立即完成，并抛出 `java.nio.channels.`[`ClosedChannelException`](https://docs.oracle.com/javase/8/docs/api/java/nio/channels/ClosedChannelException.html#class%20in%20java.nio.channels)。

注意

异步通道可安全地供多个并发线程使用。某些通道实现可能支持并发读取和写入，但可能不允许在任意给定时间有多个读取和写入操作同时未完成。

`java.nio.channels.AsynchronousByteChannel` 接口扩展了 `AsynchronousChannel`。它提供了以下四种方法：

*   `Future<Integer> read(ByteBuffer dst)`：从该通道读取一个字节序列到字节缓冲区中。返回一个 `Future`，以便在字节可用时访问它们。
*   `<A> void read(ByteBuffer dst, A attachment, CompletionHandler<Integer,? super A> handler)`：从该通道读取一个字节序列到字节缓冲区中。在 `CompletionHandler` 中访问这些字节。
*   `Future<Integer> write(ByteBuffer src)`：从字节缓冲区向该通道写入一个字节序列。返回一个 `Future`，以便在写入计数可用时访问它。
*   `<A> void write(ByteBuffer src, A attachment, CompletionHandler<Integer,? super A> handler)`：从字节缓冲区向该通道写入一个字节序列。在 `CompletionHandler` 中访问写入计数。

当通道不允许有多个读取操作同时未完成，且前一次读取尚未完成时，`read()` 方法会抛出 `java.nio.channels.ReadPendingException`。当通道不允许有多个写入操作同时未完成，且前一次写入尚未完成时，`write()` 方法会抛出 `java.nio.channels.WritePendingException`。

警告

`java.nio.`[`ByteBuffer`](https://docs.oracle.com/javase/8/docs/api/java/nio/ByteBuffer.html#class%20in%20java.nio) 对象不适合多个并发线程使用。当启动读取或写入操作时，必须注意确保在操作完成之前不会访问该缓冲区。

## 异步文件通道

抽象类 `java.nio.channels.AsynchronousFileChannel` 描述了一个用于读取、写入和操作文件的异步通道。当通过调用 `AsynchronousFileChannel` 的某个 [`open`](https://docs.oracle.com/javase/8/docs/api/java/nio/channels/AsynchronousFileChannel.html#open-java.nio.file.Path-java.util.Set-java.util.concurrent.ExecutorService-java.nio.file.attribute.FileAttribute%E2%80%A6-)`()` 方法打开文件时，就会创建此通道，如下所示：

`AsynchronousFileChannel ch;`

`ch = AsynchronousFileChannel.open(Paths.get("somefile"));`

该文件包含一个可变长度的字节序列，可以对其进行读取和写入，并且可以查询其当前大小。当写入的字节超出其当前大小时，文件大小会增加；当文件被[截断](https://docs.oracle.com/javase/8/docs/api/java/nio/channels/AsynchronousFileChannel.html#truncate-long-)时，文件大小会减小。

通过调用 `AsynchronousFileChannel` 的 `read()` 和 `write()` 方法来读取和写入文件。其中一对方法返回一个 `Future`，另一对方法则接收一个 `CompletionHandler` 作为参数。

警告

`AsynchronousFileChannel` 实现的是 `AsynchronousChannel` 而非 `AsynchronousByteChannel`，因为该类的 `read()` 和 `write()` 方法接受位置参数，而 `AsynchronousByteChannel` 的 `read()` 和 `write()` 方法不支持位置的概念。

异步文件通道在文件中没有当前位置。相反，文件位置作为参数传递给每个启动异步操作的 `read()` 和 `write()` 方法。

注意

`read()` 和 `write()` 方法必须提供文件中的绝对位置（相对于零）来进行读取和写入。文件没有必要有关联的位置，并且读取/写入操作相对于此位置发生也没有意义，因为读取/写入操作可能在前一个操作完成之前就已启动，并且它们发生的顺序无法保证。出于同样的原因，`AsynchronousFileChannel` 类中没有（像 `java.nio.channels.FileChannel` 中那样）用于设置和查询位置的方法。

除了支持读取和写入之外，`AsynchronousFileChannel` 还定义了以下操作：

*   对文件所做的更新可能会被[强制写入](https://docs.oracle.com/javase/8/docs/api/java/nio/channels/AsynchronousFileChannel.html#force-boolean-)到底层存储设备，以确保在系统崩溃时数据不会丢失。调用 `void force` `(boolean metaData)` 即可完成此任务。
*   文件的某个区域可以被[锁定](https://docs.oracle.com/javase/8/docs/api/java/nio/channels/AsynchronousFileChannel.html#lock-long-long-boolean-A-java.nio.channels.CompletionHandler-)，以防止其他程序访问。调用各种 `lock()` 和 `tryLock()` 方法即可完成此任务。其中两个 `lock()` 方法返回一个 `Future`，并接受一个 `CompletionHandler` 作为参数。

我创建了一个 `AFCDemo` 应用程序，它使用 `AsynchronousFileChannel` 打开一个文件，并在 `Future` 上下文中读取最多前 1024 个字节。清单 13-1 展示了源代码。

清单 13-1\. 从文件读取字节并轮询返回的 Future 以检查是否完成

`import java.io.IOException;`

`import java.nio.ByteBuffer;`

`import java.nio.channels.AsynchronousFileChannel;`

`import java.nio.file.Path;`

`import java.nio.file.Paths;`

`import java.util.concurrent.Future;`

`public class AFCDemo`

`{`

`public static void main(String[] args) throws Exception`

`{`

`if (args.length != 1)`

`{`

`System.err.println("usage: java AFCDemo path");`

`return;`

`}`

`Path path = Paths.get(args[0]);`

`AsynchronousFileChannel ch = AsynchronousFileChannel.open(path);`

`ByteBuffer buf = ByteBuffer.allocate(1024);`

`Future<Integer> result = ch.read(buf, 0);`

`while (!result.isDone())`

`{`

`System.out.println("Sleeping...");`

`Thread.sleep(500);`

`}`



`System.out.println("Finished = " + result.isDone());`

`System.out.println("Bytes read = " + result.get());`

`ch.close();`

`}`

`}`

在验证已指定标识文件路径的单个命令行参数后，`main()` 方法会获取一个封装该参数的 `java.nio.file.Path` 对象，并将其传递给 `AsynchronousFileChannel` 的 [`AsynchronousFileChannel`](https://docs.oracle.com/javase/8/docs/api/java/nio/channels/AsynchronousFileChannel.html#class%20in%20java.nio.channels) `open(` [`Path`](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Path.html#interface%20in%20java.nio.file) `file,` [`OpenOption`](https://docs.oracle.com/javase/8/docs/api/java/nio/file/OpenOption.html#interface%20in%20java.nio.file) `... options)` 方法。调用此方法是为了打开一个现有文件进行读取。

注意

[`AsynchronousFileChannel`](https://docs.oracle.com/javase/8/docs/api/java/nio/channels/AsynchronousFileChannel.html#class%20in%20java.nio.channels) 的 `open(` [`Path`](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Path.html#interface%20in%20java.nio.file) `file,` [`OpenOption`](https://docs.oracle.com/javase/8/docs/api/java/nio/file/OpenOption.html#interface%20in%20java.nio.file) `... options)` 方法尝试打开或创建一个 `file` 用于读取和/或写入，并返回一个用于访问该文件的异步文件通道。可以传递由 `java.nio.file.OpenOption` 接口描述并由 `java.nio.file.StandardOpenOption` 枚举实现的多个选项作为参数。当未指定任何选项时，`open()` 会尝试打开一个现有文件进行读取。

假设现有文件成功打开，则会分配一个字节缓冲区，并启动一个从位置 0 开始的读取操作。返回的 `Future<Integer>` 对象会通过调用其 `isDone()` 方法被反复查询，当操作完成时该方法返回 `true`。在此方法返回 `true` 之前，`while` 循环交替输出一条休眠消息并休眠 500 毫秒。最后，输出完成状态和读取的字节数，并关闭通道。

按如下方式编译清单 13-1：

`javac AFCDemo.java`

按如下方式运行生成的应用程序：

`java AFCDemo AFCDemo.java`

您应该会看到类似于以下的输出：

`Sleeping...`

`Finished = true`

`Bytes read = 907`

我还创建了第二个 `AFCDemo` 应用程序，它使用 `AsynchronousFileChannel` 打开一个文件，并在 `CompletionHandler` 上下文中读取最多前 1024 个字节。清单 13-2 展示了源代码。

清单 13-2\. 从文件读取字节并在完成处理器中显示结果

`import java.io.IOException;`

`import java.nio.ByteBuffer;`

`import java.nio.channels.AsynchronousFileChannel;`

`import java.nio.channels.CompletionHandler;`

`import java.nio.file.Path;`

`import java.nio.file.Paths;`

`public class AFCDemo`

`{`

`public static void main(String[] args) throws Exception`

`{`

`if (args.length != 1)`

`{`

`System.err.println("usage: java AFCDemo path");`

`return;`

`}`

`Path path = Paths.get(args[0]);`

`AsynchronousFileChannel ch = AsynchronousFileChannel.open(path);`

`ByteBuffer buf = ByteBuffer.allocate(1024);`

`Thread mainThd = Thread.currentThread();`

`ch.read(buf, 0, null,`

`new CompletionHandler<Integer, Void>()`

`{`

`@Override`

`public void completed(Integer result, Void v)`

`{`

`System.out.println("Bytes read = " + result);`

`mainThd.interrupt();`

`}`

`@Override`

`public void failed(Throwable t, Void v)`

`{`

`System.out.println("Failure: " + t.toString());`

`mainThd.interrupt();`

`}`

`});`

`System.out.println("Waiting for completion");`

`try`

`{`

`mainThd.join();`

`}`

`catch (InterruptedException ie)`

`{`

`System.out.println("Terminating");`

`}`

`ch.close();`

`}`

`}`

`main()` 方法验证已指定标识文件路径的单个命令行参数，然后获取一个封装该参数的 `Path` 对象，并尝试打开此路径。如果成功，`main()` 会分配一个字节缓冲区，获取主线程引用，并调用 `read()`。

`read()` 调用不需要附件，但需要一个完成处理器。成功时会调用此处理器的 `completed()` 方法，并输出读取的字节数。失败时会调用处理器的 `failed()` 方法，并输出适当的失败消息。

无论操作成功还是失败，每个方法都会在主线程引用上调用 `interrupt()`。方法这样做是因为，在输出消息后，主线程在其 `java.lang.Thread` 引用上调用 `join()` 方法，这会导致主线程无限期阻塞。只有抛出 `java.lang.InterruptedException` 才能唤醒该线程，而这仅在 I/O 操作完成后调用 `interrupt()` 方法时才会发生。最后，`main()` 关闭通道。

按如下方式编译清单 13-2：

`javac AFCDemo.java`

JDK 7 编译器会报告关于 `mainThd` 未声明为 `final` 的错误，例如 `final Thread mainThd = Thread.currentThread();`。JDK 8 不要求将 `mainThd` 声明为 `final`，只要该变量是有效 final（即初始化后未被修改的变量）即可。

按如下方式运行生成的应用程序：

`java AFCDemo AFCDemo.java`

您应该会看到以下输出：

`Waiting for completion`

`Bytes read = 1024`

`Terminating`

## 异步套接字通道

抽象的 `java.nio.channels.AsynchronousServerSocketChannel` 类描述了用于面向流的监听套接字的异步通道。其用于面向流的连接套接字的对应通道由抽象的 `java.nio.channels.AsynchronousSocketChannel` 类描述。

注意

`AsynchronousServerSocketChannel` 实现的是 `AsynchronousChannel` 而非 `AsynchronousByteChannel`，因为它没有声明 `read()`/`write()` 方法。`AsynchronousSocketChannel` 实现的是 `AsynchronousByteChannel`。



### AsynchronousServerSocketChannel

要获取一个 `AsynchronousServerSocketChannel` 对象，可以调用该类的 [`AsynchronousServerSocketChannel`](https://docs.oracle.com/javase/8/docs/api/java/nio/channels/AsynchronousServerSocketChannel.html#class%20in%20java.nio.channels) `open()` 类方法，如下所示：

`AsynchronousServerSocketChannel ch;`

`ch = AsynchronousServerSocketChannel.open();`

根据 `AsynchronousServerSocketChannel` 的文档，该方法返回一个绑定到默认组的异步服务器套接字通道。另一种 [`AsynchronousServerSocketChannel`](https://docs.oracle.com/javase/8/docs/api/java/nio/channels/AsynchronousServerSocketChannel.html#class%20in%20java.nio.channels) `open(` [`AsynchronousChannelGroup`](https://docs.oracle.com/javase/8/docs/api/java/nio/channels/AsynchronousChannelGroup.html#class%20in%20java.nio.channels) `group)` 方法则返回一个绑定到指定 `group` 的异步服务器套接字通道。我将在本章后面讨论异步通道组。

你可以通过调用 `<T> AsynchronousServerSocketChannel setOption(SocketOption<T> name, T value)` 泛型方法来配置异步服务器套接字通道。唯一支持的已记录选项是 `SO_RCVBUF` 和 `SO_REUSEADDR`。

一个新建且可能已配置的异步服务器套接字通道是打开的，但尚未绑定到本地地址。它可以通过调用 `AsynchronousServerSocketChannel` 的 [`bind`](https://docs.oracle.com/javase/8/docs/api/java/nio/channels/AsynchronousServerSocketChannel.html#bind-java.net.SocketAddress-int-) `()` 方法之一绑定到本地地址，并配置为监听连接。一旦绑定，就可以使用其 [`accept`](https://docs.oracle.com/javase/8/docs/api/java/nio/channels/AsynchronousServerSocketChannel.html#accept-A-java.nio.channels.CompletionHandler-) `()` 方法之一（一个方法返回 `Future`，另一个方法接受 `CompletionHandler` 参数）来启动对通道套接字连接的接受。尝试在未绑定的通道上调用 `accept()` 会导致 `java.nio.channels.` [`NotYetBoundException`](https://docs.oracle.com/javase/8/docs/api/java/nio/channels/NotYetBoundException.html#class%20in%20java.nio.channels) 异常。

注意

异步服务器套接字通道可安全地供多个并发线程使用，尽管在任何时候最多只能有一个接受操作处于未完成状态。如果某个线程在前一个接受操作结束前启动了另一个接受操作，则会抛出 `java.nio.channels.` [`AcceptPendingException`](https://docs.oracle.com/javase/8/docs/api/java/nio/channels/AcceptPendingException.html#class%20in%20java.nio.channels) 异常。

为了演示 `AsynchronousServerSocketChannel`，我创建了一个由 `Server`、`Attachment`、`ConnectionHandler` 和 `ReadWriteHandler` 类组成的 `Server` 应用程序。清单 13-3 展示了 `Server` 的源代码。

清单 13-3\. 启动一个异步处理连接和读写的服务器

`import java.io.IOException;`

`import java.net.InetSocketAddress;`

`import java.nio.channels.AsynchronousServerSocketChannel;`

`public class Server`

`{`

`private final static int PORT = 9090;`

`private final static String HOST = "localhost";`

`public static void main(String[] args)`

`{`

`AsynchronousServerSocketChannel channelServer;`

`try`

`{`

`channelServer = AsynchronousServerSocketChannel.open();`

`channelServer.bind(new InetSocketAddress(HOST, PORT));`

`System.out.printf("服务器正在监听 %s%n",`

`channelServer.getLocalAddress());`

`}`

`catch (IOException ioe)`

`{`

`System.err.println("无法打开或绑定服务器套接字通道");`

`return;`

`}`

`Attachment att = new Attachment();`

`att.channelServer = channelServer;`

`channelServer.accept(att, new ConnectionHandler());`

`try`

`{`

`Thread.currentThread().join();`

`}`

`catch (InterruptedException ie)`

`{`

`System.out.println("服务器终止");`

`}`

`}`

`}`



清单 13-3 中的 `main()` 方法首先尝试打开一个异步服务器套接字通道，然后将其绑定到一个本地地址，该地址恰好是本地主机上的端口 `9090`。接着，它调用 `AsynchronousServerSocketChannel` 的 `S` [`ocketAddress`](https://docs.oracle.com/javase/8/docs/api/java/net/SocketAddress.html#class%20in%20java.net) `getLocalAddress()` 方法，返回该通道的套接字（即本地套接字）所绑定的套接字地址。最后，它输出一条消息，表明服务器正在此地址上监听。

如果抛出异常，服务器会输出一条消息并终止。否则，它会创建一个 `Attachment` 对象，并将该对象的 `channelServer` 字段初始化为新打开的异步服务器套接字通道。（为方便起见，我直接访问 `public` 字段，而不是调用 getter 和 setter 方法。）该字段将由随后创建的 `ConnectionHandler` 对象访问，该对象与 `Attachment` 对象一起传递给 `<A> void accept(A attachment,` [`CompletionHandler`](https://docs.oracle.com/javase/8/docs/api/java/nio/channels/CompletionHandler.html#interface%20in%20java.nio.channels) `<` [`AsynchronousSocketChannel`](https://docs.oracle.com/javase/8/docs/api/java/nio/channels/AsynchronousSocketChannel.html#class%20in%20java.nio.channels) `,? super A> handler)` 泛型方法。`accept()` 方法监听传入的连接，并通过 `ConnectionHandler` 对象对其进行相应处理。

此时，`main()` 的执行线程通过调用 `Thread.join()` 阻塞。解除此线程阻塞并从 `main()` 返回的唯一方法是从另一个线程中断该线程。不过，我尚未实现此功能。

服务器可以通过单个 `ConnectionHandler` 对象和多个 `ReadWriteHandler` 对象（每个客户端一个对象）同时与多个客户端交互。`Attachment` 类让连接处理程序和读写处理程序能够方便地访问服务器套接字通道。此外，它还记录了特定于客户端的详细信息。清单 13-4 展示了 `Attachment` 的源代码。

清单 13-4\. 捆绑服务器和客户端用于通信的字段

`import java.net.SocketAddress;`

`import java.nio.ByteBuffer;`

`import java.nio.channels.AsynchronousServerSocketChannel;`

`import java.nio.channels.AsynchronousSocketChannel;`

`public class Attachment`

`{`

`public AsynchronousServerSocketChannel channelServer;`

`public AsynchronousSocketChannel channelClient;`

`public boolean isReadMode;`

`public ByteBuffer buffer;`

`public SocketAddress clientAddr;`

`}`

`channelServer` 字段存储了对在 `Server` 类的 `main()` 方法中创建的服务器套接字通道的引用。连接处理程序使用它来调用 `AsynchronousServerSocketChannel` 的 `accept()` 方法。

`channelClient` 字段存储了套接字通道的引用，该通道在 `accept()` 成功接受客户端连接后，被传递给 `ConnectionHandler` 的 `completed()` 方法。该方法中也使用此字段来执行初始的 `read()` 调用，以及在 `ReadWriteHandler` 对象中执行 `read()`/`write()` 调用。

`isReadMode` 字段指示在调用 `ReadWriteHandler` 对象的 `completed()` 方法之前执行的是读取（`true`）还是写入（`false`）操作。对于读取操作，`completed()` 会获取并输出读取的数据。`completed()` 方法以执行相反操作结束。

`buffer` 字段标识在连接处理程序中创建的字节缓冲区，该缓冲区用于在服务器和客户端之间传输字节。每个客户端都有自己的字节缓冲区。

最后，`clientAddr` 字段存储远程客户端的 `java.net.SocketAddress`。它存储客户端的套接字地址，并作为各种特定于客户端的消息的一部分输出。



清单 13-5 展示了 `ConnectionHandler` 的源代码。

清单 13-5\. 管理来自客户端的连接

`import java.io.IOException;`

`import java.net.SocketAddress;`

`import java.nio.ByteBuffer;`

`import java.nio.channels.AsynchronousSocketChannel;`

`import java.nio.channels.CompletionHandler;`

`public class ConnectionHandler`

`implements CompletionHandler<AsynchronousSocketChannel, Attachment>`

`{`

`@Override`

`public void completed(AsynchronousSocketChannel channelClient,`

`Attachment att)`

`{`

`try`

`{`

`SocketAddress clientAddr = channelClient.getRemoteAddress();`

`System.out.printf("Accepted connection from %s%n", clientAddr);`

`att.channelServer.accept(att, this);`

`Attachment newAtt = new Attachment();`

`newAtt.channelServer = att.channelServer;`

`newAtt.channelClient = channelClient;`

`newAtt.isReadMode = true;`

`newAtt.buffer = ByteBuffer.allocate(2048);`

`newAtt.clientAddr = clientAddr;`

`ReadWriteHandler rwh = new ReadWriteHandler();`

`channelClient.read(newAtt.buffer, newAtt, rwh);`

`}`

`catch (IOException ioe)`

`{`

`ioe.printStackTrace();`

`}`

`}`

`@Override`

`public void failed(Throwable t, Attachment att)`

`{`

`System.out.println("Failed to accept connection");`

`}`

`}`

`ConnectionHandler` 是一个 `CompletionHandler`，用于响应传入的连接。当连接成功时，会调用其 `completed()` 方法；否则，会调用其 `failed()` 方法。

`completed()` 方法的第一个任务是获取并输出客户端的套接字地址以进行标识。我指定了 `channelClient.getRemoteAddress()` 而不是 `channelClient.getLocalAddress()` 来返回客户端的套接字地址，因为与 `channelClient` 对应的套接字是在服务器端创建的，并且它与客户端套接字通信。在 `channelClient` 上调用 `getLocalAddress()` 会返回服务器端的套接字地址。在 `channelClient` 上调用 `getRemoteAddress()` 会返回客户端的套接字地址。

接下来，`completed()` 在服务器套接字通道上调用 `accept()`，并将传入的 `Attachment` 对象和当前完成处理器的引用作为参数。此调用允许服务器响应下一个传入的连接。

`accept()` 方法在从传递给 `completed()` 的 `Attachment` 对象引用的服务器套接字通道上调用。同一个对象作为第一个参数传递给 `accept()`。`Attachment` 对象中的其他字段均未初始化，因为它们不是必需的。此 `Attachment` 对象仅用于标识服务器套接字通道。

调用 `accept()` 后，会创建并初始化第二个 `Attachment` 对象，为从客户端读取做准备。同时，创建一个 `ReadWriteHandler` 对象来响应读取操作。新的 `Attachment` 对象的字节缓冲区、新的 `Attachment` 对象以及 `ReadWriteHandler` 对象作为参数传递给客户端套接字通道的 `read()` 方法。

清单 13-6 展示了 `ReadWriteHandler` 的源代码。

清单 13-6\. 管理与客户端的读写操作

`import java.io.IOException;`

`import java.nio.channels.CompletionHandler;`

`import java.nio.charset.Charset;`

`public class ReadWriteHandler`

`implements CompletionHandler<Integer, Attachment>`

`{`

`private final static Charset CSUTF8 = Charset.forName("UTF-8");`

`@Override`

`public void completed(Integer result, Attachment att)`

`{`

`if (result == -1)`

`{`

`try`

`{`

`att.channelClient.close();`

`System.out.printf("Stopped listening to client %s%n",`

`att.clientAddr);`

`}`

`catch (IOException ioe)`

`{`

`ioe.printStackTrace();`

`}`

`return;`

`}`

`if (att.isReadMode)`

`{`

`att.buffer.flip();`

`int limit = att.buffer.limit();`

`byte bytes[] = new byte[limit];`

`att.buffer.get(bytes, 0, limit);`

`System.out.printf("Client at %s sends message: %s%n",`

`att.clientAddr,`

`new String(bytes, CSUTF8));`

`att.isReadMode = false;`

`att.buffer.rewind();`

`att.channelClient.write(att.buffer, att, this);`

`}`

`else`

`{`

`att.isReadMode = true;`

`att.buffer.clear();`

`att.channelClient.read(att.buffer, att, this);`

`}`

`}`

`@Override`

`public void failed(Throwable t, Attachment att)`

`{`

`System.out.println("Connection with client broken");`

`}`

`}`

`ReadWriteHandler` 是一个 `CompletionHandler`，用于响应 `read()` 或 `write()` 方法的完成。调用其 `completed()` 方法来响应成功的 `read()` 或 `write()`，这包括发出对应的 `write()` 或 `read()`。当客户端断开与服务器的连接时，会调用其 `failed()` 方法。

`completed()` 方法的第一个任务是响应返回 `-1` 的 `read()` 调用，这表示由于已到达流末尾而无法读取任何字节。（客户端可能在写入数据之前已终止。）它会关闭客户端套接字通道，输出一条消息以指示服务器不再监听该客户端，然后返回。

假设读写处理器处于读取模式（`true`），缓冲区内容会被翻转并提取到一个 `bytes` 数组中，随后该数组被转换为一个字符串并输出。

然后，通过将 `false` 赋值给 `isReadMode` 将读写处理器配置为写入模式，缓冲区被倒回以准备写入，接着缓冲区连同附件和当前完成处理器一起被传递给客户端套接字通道的 `write()` 方法，以便将缓冲区内容发送给客户端。

成功写入操作后，会调用读写处理器的 `completed()` 方法。由于 `isReadMode` 不再是 `true`，因此会执行 `if`-`else` 语句的 `else` 部分。它将 `isReadMode` 切换为 `true`，清除缓冲区，并启动一个读取操作以从客户端读取数据。此模式会持续，直到客户端没有更多数据可读且完成处理器可以终止为止。（后一种情况可能永远不会发生。）

按如下方式编译清单 13-3 到 13-6：

`javac Server.java`

按如下方式运行生成的应用程序：

`java Server`

您应该会观察到以下初始输出：

`Server listening at /127.0.0.1:9090`



### AsynchronousSocketChannel

你可以通过调用该类的 [`AsynchronousSocketChannel`](https://docs.oracle.com/javase/8/docs/api/java/nio/channels/AsynchronousServerSocketChannel.html#class%20in%20java.nio.channels) `open()` 类方法来获取一个 `AsynchronousSocketChannel` 对象，具体如下：

`AsynchronousSocketChannel ch = AsynchronousSocketChannel.open();`

根据 `AsynchronousSocketChannel` 的文档，该方法返回一个绑定到默认组的异步套接字通道。另一种 [`AsynchronousSocketChannel`](https://docs.oracle.com/javase/8/docs/api/java/nio/channels/AsynchronousServerSocketChannel.html#class%20in%20java.nio.channels) `open(` [`AsynchronousChannelGroup`](https://docs.oracle.com/javase/8/docs/api/java/nio/channels/AsynchronousChannelGroup.html#class%20in%20java.nio.channels) `group)` 方法则返回一个绑定到指定 `group` 的异步套接字通道。同样，我将在本章后面讨论异步通道组。

你可以通过调用 `<T> AsynchronousSocketChannel setOption(SocketOption<T> name, T value)` 泛型方法来配置异步套接字通道。支持的已记录选项仅有 `SO_RCVBUF`、`SO_SNDBUF`、`SO_KEEPALIVE`、`SO_REUSEADDR` 和 `TCP_NODELAY`。

一个新建且可能已配置的异步套接字通道处于打开状态，但尚未连接。当与[异步服务器套接字通道](https://docs.oracle.com/javase/8/docs/api/java/nio/channels/AsynchronousServerSocketChannel.html#class%20in%20java.nio.channels)的套接字建立连接时，会创建一个已连接的异步套接字通道。无法为任意预先存在的[套接字](https://docs.oracle.com/javase/8/docs/api/java/net/Socket.html#class%20in%20java.net)创建异步套接字通道。

新建的套接字通道通过调用其 [`connect`](https://docs.oracle.com/javase/8/docs/api/java/nio/channels/AsynchronousSocketChannel.html#connect-java.net.SocketAddress-A-java.nio.channels.CompletionHandler-) `()` 方法之一进行连接；其中一个 `connect()` 方法返回一个 `Future`，另一个 `connect()` 方法则需要一个 `CompletionHandler` 参数。

一旦连接，套接字通道将保持连接状态，直到关闭。可以通过调用其 `SocketAddress getRemoteAddress()` 方法来确定套接字通道是否已连接，该方法返回远程套接字的套接字地址（如果没有连接，则返回 `null`）。尝试在未连接的通道上执行 I/O 操作将导致抛出 [`NotYetConnectedException`](https://docs.oracle.com/javase/8/docs/api/java/nio/channels/NotYetConnectedException.html#class%20in%20java.nio.channels)。

注意

异步套接字通道可安全地供多个并发线程使用。它们支持并发读写，但任何时候最多只能有一个读取和一个写入操作未完成。如果某个线程在前一次读取完成之前发起新的读取，将抛出 [`ReadPendingException`](https://docs.oracle.com/javase/8/docs/api/java/nio/channels/ReadPendingException.html#class%20in%20java.nio.channels)。类似地，在前一次写入完成之前尝试发起新的写入，将抛出 [`WritePendingException`](https://docs.oracle.com/javase/8/docs/api/java/nio/channels/WritePendingException.html#class%20in%20java.nio.channels)。

为了演示 `AsynchronousSocketChannel`，我创建了一个由 `Client`、`Attachment` 和 `ReadWriteHandler` 类组成的 `Client` 应用程序。清单 13-7 展示了 `Client` 的源代码。

清单 13-7\. 启动一个异步处理读/写的客户端

`import java.io.IOException;`

`import java.net.InetSocketAddress;`

`import java.nio.ByteBuffer;`

`import java.nio.channels.AsynchronousSocketChannel;`

`import java.nio.charset.Charset;`

`import java.util.concurrent.ExecutionException;`

`public class Client`

`{`



`private final static Charset CSUTF8 = Charset.forName("UTF-8");`

`private final static int PORT = 9090;`

`private final static String HOST = "localhost";`

`public static void main(String[] args)`

`{`

`AsynchronousSocketChannel channel;`

`try`

`{`

`channel = AsynchronousSocketChannel.open();`

`}`

`catch (IOException ioe)`

`{`

`System.err.println("无法打开客户端套接字通道");`

`return;`

`}`

`try`

`{`

`channel.connect(new InetSocketAddress(HOST, PORT)).get();`

`System.out.printf("客户端在 %s 已连接%n",`

`channel.getLocalAddress());`

`}`

`catch (ExecutionException | InterruptedException eie)`

`{`

`System.err.println("服务器无响应");`

`return;`

`}`

`catch (IOException ioe)`

`{`

`System.err.println("无法获取客户端套接字通道的" +`

`"本地地址");`

`return;`

`}`

`Attachment att = new Attachment();`

`att.channel = channel;`

`att.isReadMode = false;`

`att.buffer = ByteBuffer.allocate(2048);`

`att.mainThd = Thread.currentThread();`

`byte[] data = "Hello".getBytes(CSUTF8);`

`att.buffer.put(data);`

`att.buffer.flip();`

`channel.write(att.buffer, att, new ReadWriteHandler());`

`try`

`{`

`att.mainThd.join();`

`}`

`catch (InterruptedException ie)`

`{`

`System.out.println("客户端正在终止");`

`}`

`}`

`}`

清单 13-7 中的 `main()` 方法首先尝试打开一个异步套接字通道，并将其连接到本地主机上端口 `9090` 的服务器。然后调用 `AsynchronousSocketChannel` 的 `S` [`ocketAddress`](https://docs.oracle.com/javase/8/docs/api/java/net/SocketAddress.html#class%20in%20java.net) `getLocalAddress()` 方法，返回该通道的套接字（本地套接字）所绑定的套接字地址，并输出一条消息，说明客户端已在此地址连接。

如果抛出异常，服务器会输出一条消息并终止。否则，它会创建一个 `Attachment` 对象并初始化其字段，为调用 `write()` 方法做准备。

创建一条初始消息并存储在缓冲区中，然后将其写入套接字通道。`Attachment` 对象和一个新创建的 `ReadWriteHandler` 对象（该对象响应 `write()` 调用并执行额外的 `write()` 和 `read()` 操作）被传递给 `write()`。

此时，`main()` 的执行线程通过调用 `Thread.join()` 阻塞自身。解除此线程阻塞并从 `main()` 返回的唯一方法是从另一个线程中断该线程。`ReadWriteHandler` 负责处理此任务，稍后您将看到。

清单 13-8 展示了 `Attachment` 的源代码。

清单 13-8\. 为后续通信绑定字段

`import java.nio.ByteBuffer;`

`import java.nio.channels.AsynchronousSocketChannel;`

`public class Attachment`

`{`

`public AsynchronousSocketChannel channel;`

`public boolean isReadMode;`

`public ByteBuffer buffer;`

`public Thread mainThd;`

`}`

`channel` 字段标识异步套接字通道。随后，`ReadWriteHandler` 的 `completed()` 方法会访问该字段以执行读取和写入操作。

`isReadMode` 字段是一个开关，让 `completed()` 知道它需要执行读取还是写入操作。

`buffer` 字段标识在 `Client` 的 `main()` 方法中创建的字节缓冲区，该缓冲区用于在服务器和客户端之间传输字节。每个客户端都有自己的字节缓冲区。

最后，`mainThd` 字段引用一个 `Thread` 对象，该对象标识 `main()` 的线程。`completed()` 方法在此引用上调用 `interrupt()` 来中断客户端，以便此应用程序可以退出。

清单 13-9 展示了 `ReadWriteHandler` 的源代码。

清单 13-9\. 管理与服务器的读取和写入操作

`import java.io.BufferedReader;`

`import java.io.InputStreamReader;`

`import java.io.IOException;`

`import java.nio.channels.CompletionHandler;`

`import java.nio.charset.Charset;`

`public class ReadWriteHandler implements CompletionHandler<Integer, Attachment>`

`{`



`private final static Charset CSUTF8 = Charset.forName("UTF-8");`

`private BufferedReader conReader =`

`new BufferedReader(new InputStreamReader(System.in));`

`@Override`

`public void completed(Integer result, Attachment att)`

`{`

`if (att.isReadMode)`

`{`

`att.buffer.flip();`

`int limit = att.buffer.limit();`

`byte[] bytes = new byte[limit];`

`att.buffer.get(bytes, 0, limit);`

`String msg = new String(bytes, CSUTF8);`

`System.out.printf("Server responded: %s%n", msg);`

`try`

`{`

`msg = "";`

`while (msg.length() == 0)`

`{`

`System.out.print("Enter message (\"end\" to quit): ");`

`msg = conReader.readLine();`

`}`

`if (msg.equalsIgnoreCase("end"))`

`{`

`att.mainThd.interrupt();`

`return;`

`}`

`}`

`catch (IOException ioe)`

`{`

`System.err.println("Unable to read from console");`

`}`

`att.isReadMode = false;`

`att.buffer.clear();`

`byte[] data = msg.getBytes(CSUTF8);`

`att.buffer.put(data);`

`att.buffer.flip();`

`att.channel.write(att.buffer, att, this);`

`}`

`else`

`{`

`att.isReadMode = true;`

`att.buffer.clear();`

`att.channel.read(att.buffer, att, this);`

`}`

`}`

`@Override`

`public void failed(Throwable t, Attachment att)`

`{`

`System.err.println("Server not responding");`

`System.exit(1);`

`}`

`}`

`ReadWriteHandler` 是一个 `CompletionHandler`，用于响应 `read()` 或 `write()` 操作的完成。其 `completed()` 方法在成功完成 `read()` 或 `write()` 时被调用，其中包括发起对应的 `write()` 或 `read()` 操作。当客户端断开与服务器的连接时，会调用其 `failed()` 方法。

`completed()` 方法的首要任务是判断是否执行了 `read()` 操作并做出相应响应。如果是，它会将缓冲区中的字节提取到数组中，然后将数组转换为字符串并输出。

接下来，`completed()` 方法让用户有机会输入一条消息，该消息将被发送到服务器。如果用户指定了 `end`，客户端线程将被中断，客户端终止。任何其他消息的字节都会从字符串中提取并存储到缓冲区中，随后被写入套接字通道。

如果未发生 `read()` 调用，`completed()` 方法将执行 `if`-`else` 语句的 `else` 部分。在将 `isReadMode` 设置为 `true` 后，它会清空缓冲区并调用 `read()`。

为了更清晰地说明，以下是客户端启动时发生的情况。

`Client` 的 `main()` 方法将 `isReadMode` 设置为 `false`，在缓冲区中存储一条 `Hello` 消息，并通过 `write()` 将消息写入通道，这会导致消息被发送到服务器并由服务器显示。服务器的读/写处理程序通过 `write()` 将此消息回写给客户端。由于 `isReadMode` 为 `false`，`Client` 的读/写处理程序的 `completed()` 方法执行 `if`-`else` 语句的 `else` 部分，将 `isReadMode` 设置为 `true` 并调用 `read()`，从而将服务器写入的 `Hello` 字节存储到缓冲区中。`Client` 的读/写处理程序的 `completed()` 方法被调用。它注意到 `isReadMode` 为 `true`，从缓冲区中提取字节，并将等效的字符串作为 `Server responded` 消息的一部分输出。现在，客户端将从用户处获取消息并做出相应处理。

按如下方式编译清单 13-7 至 13-9：

`javac Client.java`

按如下方式运行生成的应用程序：

`java Client`

假设服务器按之前所示正在运行，您应该会看到类似于以下初始输出的内容：

`Client at /0:0:0:0:0:0:0:0:55359 connected`

`Server responded: Hello`

`Enter message ("end" to quit):`

继续操作，您应该在服务器端看到类似于以下输出的内容：

`Accepted connection from /127.0.0.1:55359`

`Client at /127.0.0.1:55359 sends message: Hello`

当您通过客户端输入消息时，您会看到这些消息在服务器端以“`Client at /127.0.0.1:xxxxx sends message:`”（`xxxxx` 表示端口号）开头回显，在客户端则以“`Server responded:`”开头回显。当您终止客户端时，它会显示 `Client terminating`；服务器会显示 `Connection with client broken`。



## 异步通道组

抽象类 `java.nio.channels.AsynchronousChannelGroup` 描述了一组用于资源共享的异步通道。一个组拥有一个关联的线程池，任务被提交到该线程池，用于处理 I/O 事件并分派给[完成处理器](https://docs.oracle.com/javase/8/docs/api/java/nio/channels/CompletionHandler.html#interface%20in%20java.nio.channels)，这些处理器消费在该组通道上执行的异步操作的结果。

注意

关联的线程池归该组所有；组的终止会导致关联的线程池被关闭。

`AsynchronousServerSocketChannel` 和 `AsynchronousSocketChannel` 属于组。当你通过无参的 `open()` 方法创建 `AsynchronousServerSocketChannel` 或 `AsynchronousSocketChannel` 时，该通道会绑定到默认组，这是一个由 Java 虚拟机（JVM）自动构建和维护的系统级通道组。默认组有一个关联的线程池，该线程池会根据需要创建新线程。你可以通过在 JVM 启动时初始化以下系统属性来配置默认组：

*   `java.nio.channels.DefaultThreadPool.` `threadFactory`：此属性的值是一个具体的 `java.util.concurrent.` [`ThreadFactory`](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/ThreadFactory.html#interface%20in%20java.util.concurrent) 类的完全限定名。该类通过系统类加载器加载并实例化。工厂的 [`newThread`](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/ThreadFactory.html#newThread-java.lang.Runnable-) `(Runnable r)` 方法被调用来为默认组的线程池创建每个线程。如果加载和实例化该属性值的过程失败，则在构建默认组期间会抛出一个未指定的错误。（如果未配置默认组的 [`ThreadFactory`](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/ThreadFactory.html#interface%20in%20java.util.concurrent)，则默认组的池化线程是守护线程。）
*   `java.nio.channels.DefaultThreadPool.` `initialSize`：`initialSize` 的值指定了默认组线程池的初始大小。如果基于字符串的值无法解析为整数，则会在构建默认组期间导致抛出未指定的错误。

你可能更倾向于定义自己的通道组，因为它能让你对用于服务 I/O 操作的线程拥有更多控制权。此外，它还提供了关闭线程和等待终止的机制。你可以通过调用以下类方法之一来创建自己的异步通道组：

*   `AsynchronousChannelGroupwithCachedThreadPool` `(ExecutorService executor, int initialSize)`：使用一个给定的线程池（指定为 `java.util.concurrent.ExecutorService` 对象）创建一个异步通道组，该线程池会根据需要创建新线程。
*   `AsynchronousChannelGroup` `withFixedThreadPool` `(int nThreads, ThreadFactory threadFactory)`：使用一个固定线程池创建一个异步通道组。
*   `AsynchronousChannelGroup` `withThreadPool` `(ExecutorService executor)`：使用一个给定的线程池（指定为 `ExecutorService` 对象）创建一个异步通道组。

以下示例创建了一个新的通道组，该组拥有一个包含 20 个线程的固定线程池。每个线程都使用来自 `java.util.concurrent.Executors` 类的默认线程工厂构造：

`AsynchronousChannelGroup group;`

`group = AsynchronousChannelGroup.`

`withFixedThreadPool(20,`

`Executors.defaultThreadFactory());`

创建组之后，你可以通过调用各自类中的以下类方法，将异步服务器套接字通道和异步套接字通道绑定到该组：

*   `AsynchronousServerSocketChannel open(AsynchronousChannelGroup group)`
*   `AsynchronousSocketChannel open(AsynchronousChannelGroup group)`

以下示例创建了一个异步服务器套接字通道和一个异步套接字通道，它们都绑定到先前创建的组：

`AsynchronousServerSocketChannel chServer;`

`chServer = AsynchronousServerSocketChannel.open(group);`

`AsynchronousSocketChannel chClient = AsynchronousSocketChannel.open(group);`

注意

在编写完成处理器时，务必避免可能阻塞线程的操作。当所有线程都被阻塞时，整个应用程序可能会阻塞。对于自定义或可缓存的线程池，队列可能会变得非常大，最终导致内存不足的情况。

你主要会实例化 `AsynchronousChannelGroup` 并将你的套接字通道绑定到该组，以便进行关闭和终止操作。`void` `shutdown()` 方法启动一个组的有序关闭。有序关闭会将组标记为已关闭；尝试构造一个绑定到该组的通道会导致 `java.nio.channels.` [`ShutdownChannelGroupException`](https://docs.oracle.com/javase/8/docs/api/java/nio/channels/ShutdownChannelGroupException.html#class%20in%20java.nio.channels) 异常。可以通过调用 `boolean isShutdown()` 方法来测试组是否已关闭。

一旦关闭，当所有绑定到该组的异步通道都已关闭、所有正在积极执行的完成处理器都已运行完毕、并且该组使用的资源已被释放时，该组即终止。不会尝试停止或中断正在执行完成处理器的线程。`boolean isTerminated()` 方法用于测试组是否已终止，`boolean awaitTermination(long timeout, TimeUnit unit)` 方法用于阻塞直到组终止。

`void` `shutdownNow()` 方法用于启动组的强制关闭。除了有序关闭执行的操作外，`shutdownNow()` 还会关闭组中所有打开的通道，就像调用每个通道的 `close()` 方法一样。

以下示例演示了上述方法：

`// 启动一个不会完成的 I/O 操作。`

`channel.accept(null, completionHandler);`

`// 操作开始后，使用通道组来控制关闭。`

`if (!group.isShutdown())`

`{`

`// 组关闭后，不能再有通道绑定到它。`

`group.shutdown();`

`}`

`if (!group.isTerminated())`

`{`

`// 强制关闭组。通道被关闭，accept 操作中止。`

`group.shutdownNow();`

`}`

`// 组应该能够终止；最多等待 10 秒。`

`group.awaitTermination(10, TimeUnit.SECONDS);`



### 那么 `AsynchronousFileChannel` 呢？

`AsynchronousFileChannel` 不属于任何组。然而，它们与一个用于提交任务的线程池相关联，该线程池负责处理 I/O 事件，并分派给完成处理器，这些处理器会消费在通道上执行的 I/O 操作的结果。

在通道上发起的 I/O 操作的完成处理器保证由线程池中的某个线程调用，这确保了完成处理器由具有预期身份的线程运行。如果 I/O 操作立即完成，并且发起线程本身是线程池中的一个线程，那么完成处理器可能会由发起线程直接调用。

当创建异步文件通道时，如果没有指定线程池，该通道会与一个系统相关的默认线程池关联，这个线程池可能与其他通道共享。默认线程池由 [`AsynchronousChannelGroup`](https://docs.oracle.com/javase/8/docs/api/java/nio/channels/AsynchronousChannelGroup.html#class%20in%20java.nio.channels) 定义的系统属性进行配置。

通过 `AsynchronousFileChannel` 的 `AsynchronousFileChannel open(Path file, OpenOption... options)` 类方法创建的异步文件通道与默认线程池关联。你可以通过调用 [`AsynchronousFileChannel`](https://docs.oracle.com/javase/8/docs/api/java/nio/channels/AsynchronousFileChannel.html#class%20in%20java.nio.channels) 的 `open(` [`Path`](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Path.html#interface%20in%20java.nio.file) `file,` [`Set`](https://docs.oracle.com/javase/8/docs/api/java/util/Set.html#interface%20in%20java.util) `<? extends` [`OpenOption`](https://docs.oracle.com/javase/8/docs/api/java/nio/file/OpenOption.html#interface%20in%20java.nio.file) `> options,` [`ExecutorService`](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/ExecutorService.html#interface%20in%20java.util.concurrent) `executor,` [`FileAttribute`](https://docs.oracle.com/javase/8/docs/api/java/nio/file/attribute/FileAttribute.html#interface%20in%20java.nio.file.attribute) `<?>... attrs)` 类方法将文件通道与另一个线程池关联起来。在这里，`executor` 标识了所需的线程池。

练习

以下练习旨在测试你对第 13 章内容的理解：

定义异步通道。   指出使用异步通道发起 I/O 操作的两种方式。   判断正误：通道关闭后，任何进一步发起异步 I/O 操作的尝试都会立即完成，并抛出 `AsynchronousCloseException`。   指出 `AsynchronousByteChannel` 接口声明的方法。   判断正误：[ByteBuffer](https://docs.oracle.com/javase/8/docs/api/java/nio/ByteBuffer.html#class%20in%20java.nio) 对象对于多个并发线程是安全的。   指出用于异步读取、写入和操作文件的类。   判断正误：异步文件通道在文件中没有当前位置。   如果你没有向 `AsynchronousFileChannel` 的 [AsynchronousFile​Channel](https://docs.oracle.com/javase/8/docs/api/java/nio/channels/AsynchronousFileChannel.html#class%20in%20java.nio.channels) open([Path](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Path.html#interface%20in%20java.nio.file) file, [OpenOption](https://docs.oracle.com/javase/8/docs/api/java/nio/file/OpenOption.html#interface%20in%20java.nio.file)... options) 方法传递任何选项，该方法的默认行为是什么？   如何获取 `AsynchronousServerSocketChannel` 对象或 `AsynchronousSocketChannel` 对象？   指出 `AsynchronousServerSocketChannel` 文档中记录的套接字选项。   获取 `AsynchronousServerSocketChannel` 对象后，在调用其任一 `accept()` 方法之前，你必须做什么？   获取 `AsynchronousSocketChannel` 对象后，在执行读写操作之前，你必须做什么？   如何确定一个异步套接字通道是否连接到了一个异步服务器套接字通道？   判断正误：异步套接字通道对于多个并发线程是安全的。   `AsynchronousChannelGroup` 类的用途是什么？   组使用什么来提交任务？   指出用于配置默认组的系统属性。   描述 `AsynchronousChannelGroup` 的 `shutDownNow()` 方法。   判断正误：`AsynchronousFileChannel` 属于组。   编写一个 Copy 应用程序，使用 `AsynchronousFileChannel` 异步地将源文件复制到目标文件。

## 总结

异步 I/O 允许客户端代码发起一个 I/O 操作，并在操作完成时通知客户端。`AsynchronousChannel` 接口描述了一个支持异步 I/O 操作（如读取、写入等）的异步通道。I/O 操作通过调用一个返回 `Future` 或需要一个 `CompletionHandler` 参数的方法来发起。

`CompletionHandler` 声明了一个 `completed()` 方法，用于在操作成功完成时消费其结果。它还声明了一个 `failed()` 方法，用于报告操作失败（以异常的形式）并允许应用程序采取适当的措施。

`AsynchronousByteChannel` 接口扩展了 `AsynchronousChannel`，并声明了一对 `read()` 方法和一对 `write()` 方法。每一对方法都包含一个返回 `Future` 的方法和一个需要 `CompletionHandler` 参数的方法。

抽象类 `AsynchronousFileChannel` 描述了一个用于读取、写入和操作文件的异步通道。抽象类 `AsynchronousServerSocketChannel` 描述了一个用于面向流的监听套接字的异步通道。其对应的用于面向流的连接套接字的通道由抽象类 `AsynchronousSocketChannel` 描述。

抽象类 `AsynchronousChannelGroup` 描述了为资源共享目的而分组的异步通道。一个组有一个关联的线程池，用于提交任务，以处理 I/O 事件并分派给[完成处理器](https://docs.oracle.com/javase/8/docs/api/java/nio/channels/CompletionHandler.html#interface%20in%20java.nio.channels)，这些处理器消费在组内通道上执行的异步操作的结果。

通过其无参 `open()` 方法创建的 `AsynchronousServerSocketChannel` 和 `AsynchronousSocketChannel` 会绑定到默认组。它们可以通过将组对象传递给其 `open(AsynchronousChannelGroup)` 方法，绑定到由 `AsynchronousChannelGroup` 的类方法创建的组。

`AsynchronousFileChannel` 不属于任何组。然而，它们与一个默认线程池关联，该线程池可以被配置或替换。

第 14 章 将介绍 NIO.2 中套接字通道功能的完善。

