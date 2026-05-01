# 7. 通道

电子补充材料 本章的在线版本 (doi:[10.​1007/​978-1-4842-1565-4_​7](http://dx.doi.org/10.1007/978-1-4842-1565-4_7)) 包含补充材料，仅供授权用户使用。

通道与缓冲区协同工作，以实现高性能 I/O。本章将向您介绍 NIO 的通道类型。

## 通道简介

通道是一个对象，代表与硬件设备、文件、网络套接字、应用程序组件或其他能够执行写入、读取及其他 I/O 操作的实体之间的开放连接。通道在字节缓冲区和基于操作系统的 I/O 服务源或目标之间高效地传输数据。

注意

通道是访问 I/O 服务的网关。通道使用字节缓冲区作为发送和接收数据的端点。

操作系统的文件句柄或文件描述符与通道之间通常存在一一对应的关系。当您在文件上下文中使用通道时，该通道通常会连接到一个打开的文件描述符。尽管通道比文件描述符更抽象，但它们仍然能够模拟操作系统的 I/O 设施。

## 通道及其子类

Java 通过 `java.nio.channels` 和 `java.nio.channels.spi` 包支持通道。应用程序与前者包中的类型交互；而定义新选择器提供程序的开发者则使用后者包。（我将在下一章讨论选择器。）

所有通道都是最终实现了 `java.nio.channels.Channel` 接口的类的实例。`Channel` 声明了以下方法：

*   `void close()`：关闭此通道。当此通道已关闭时，调用 `close()` 无效。当另一个线程已调用 `close()` 时，新的 `close()` 调用将阻塞，直到第一个调用完成，之后 `close()` 返回且无效果。当发生 I/O 错误时，此方法抛出 `java.io.IOException`。通道关闭后，任何进一步尝试对其执行 I/O 操作都将导致抛出 `java.nio.channels.ClosedChannelException`。
*   `boolean isOpen()`：返回此通道的打开状态。当通道打开时，此方法返回 `true`；否则返回 `false`。

这些方法表明，所有通道共有的操作只有两个：关闭通道和判断通道是打开还是关闭。为了支持 I/O，`Channel` 被 `java.nio.channels.WritableByteChannel` 和 `java.nio.channels.ReadableByteChannel` 接口扩展：

*   `WritableByteChannel` 声明了一个抽象方法 `int write(ByteBuffer buffer)`，该方法将 `buffer` 中的字节序列写入当前通道。此方法返回实际写入的字节数。当通道未以写入方式打开时，抛出 `java.nio.channels.NonWritableChannelException`；当通道关闭时，抛出 `java.nio.channels.ClosedChannelException`；当另一个线程在写入期间关闭通道时，抛出 `java.nio.channels.AsynchronousCloseException`；当另一个线程在写入操作进行中中断当前线程（从而关闭通道并设置当前线程的中断状态）时，抛出 `java.nio.channels.ClosedByInterruptException`；当发生其他 I/O 错误时，抛出 `IOException`。
*   `ReadableByteChannel` 声明了一个抽象方法 `int read(ByteBuffer buffer)`，该方法从当前通道读取字节到 `buffer` 中。此方法返回实际读取的字节数（当没有更多字节可读时返回 `-1`）。当通道未以读取方式打开时，抛出 `java.nio.channels.NonReadableChannelException`；当通道关闭时，抛出 `ClosedChannelException`；当另一个线程在读取期间关闭通道时，抛出 `AsynchronousCloseException`；当另一个线程在写入操作进行中中断当前线程，从而关闭通道并设置当前线程的中断状态时，抛出 `ClosedByInterruptException`；当发生其他 I/O 错误时，抛出 `IOException`。

注意

其类仅实现 `WritableByteChannel` 或 `ReadableByteChannel` 的通道是单向的。尝试从可写字节通道读取或向可读字节通道写入将导致抛出异常。

您可以使用 `instanceof` 运算符来判断通道实例是否实现了任一接口。由于同时测试两个接口有些不便，Java 提供了 `java.nio.channels.ByteChannel` 接口，这是一个空的标记接口，是 `WritableByteChannel` 和 `ReadableByteChannel` 的子类型。当您需要了解通道是否为双向时，使用诸如 `channel instanceof ByteChannel` 这样的表达式会更加方便。



`Channel`接口也由`java.nio.channels.InterruptibleChannel`接口扩展。`InterruptibleChannel`描述了一种可以异步关闭和中断的通道。该接口重写了其父接口`Channel`的`close()`方法声明，并为`Channel`的该方法契约增加了以下附加规定：任何当前在此通道的 I/O 操作中被阻塞的线程都将收到`AsynchronousCloseException`（`IOException`的子类）。

实现此接口的通道是异步可关闭的：当一个线程在可中断通道的 I/O 操作中被阻塞时，另一个线程可以调用该通道的`close()`方法。这会导致被阻塞的线程收到一个抛出的`AsynchronousCloseException`实例。

实现此接口的通道也是可中断的：当一个线程在可中断通道的 I/O 操作中被阻塞时，另一个线程可以调用被阻塞线程的`interrupt()`方法。这样做会导致通道被关闭，被阻塞的线程收到一个抛出的`ClosedByInterruptException`实例，并且被阻塞线程的中断状态被设置。（当一个线程的中断状态已被设置，并且它在一个通道上调用阻塞 I/O 操作时，该通道会被关闭，线程将立即收到一个抛出的`ClosedByInterruptException`实例；其中断状态将保持设置状态。）

NIO 的设计者选择在阻塞线程被中断时关闭通道，因为他们无法找到一种跨操作系统以相同方式可靠处理中断 I/O 操作的方法。保证确定性行为的唯一方法就是关闭通道。

提示

你可以通过使用`instanceof`运算符来确定一个通道是否支持异步关闭和中断，例如在表达式`channel instanceof InterruptibleChannel`中。

在第 6 章中，你了解到必须调用`java.nio.Buffer`子类上的类方法来获取缓冲区。关于通道，有两种获取通道的方法：

*   `java.nio.channels`包提供了一个`Channels`工具类，它提供了两种从流中获取通道的方法。对于以下每种方法，当通道关闭时，底层流也会被关闭，并且该通道不是缓冲的：
*   `WritableByteChannel newChannel(OutputStream outputStream)` 为给定的`outputStream`返回一个可写字节通道。
*   `ReadableByteChannel newChannel(InputStream inputStream)` 为给定的`inputStream`返回一个可读字节通道。
*   各种经典的 I/O 类已被改造以支持通道创建。例如，`java.io.RandomAccessFile`声明了一个`FileChannel getChannel()`方法用于返回文件通道，而`java.net.Socket`声明了一个`SocketChannel getChannel()`方法用于返回套接字通道。

清单 7-1 使用`Channels`类为标准输入和输出流获取通道，然后使用这些通道将字节从输入通道复制到输出通道。

清单 7-1. 将字节从输入通道复制到输出通道

`import java.io.IOException;`

`import java.nio.ByteBuffer;`

`import java.nio.channels.Channels;`

`import java.nio.channels.ReadableByteChannel;`

`import java.nio.channels.WritableByteChannel;`

`public class ChannelDemo`

`{`

`public static void main(String[] args)`

`{`

`ReadableByteChannel src = Channels.newChannel(System.in);`

`WritableByteChannel dest = Channels.newChannel(System.out);`

`try`

`{`

`copy(src, dest); // 或 copyAlt(src, dest);`

`}`

`catch (IOException ioe)`

`{`

`System.err.println("I/O error: " + ioe.getMessage());`

`}`

`finally`

`{`

`try`

`{`

`src.close();`

`dest.close();`

`}`

`catch (IOException ioe)`

`{`

`ioe.printStackTrace();`

`}`

`}`

`}`

`static void copy(ReadableByteChannel src, WritableByteChannel dest)`

`throws IOException`

`{`

`ByteBuffer buffer = ByteBuffer.allocateDirect(2048);`

`while (src.read(buffer) != -1)`

`{`

`buffer.flip();`

`dest.write(buffer);`

`buffer.compact();`

`}`

`buffer.flip();`

`while (buffer.hasRemaining())`

`dest.write(buffer);`

`}`

`static void copyAlt(ReadableByteChannel src, WritableByteChannel dest)`

`throws IOException`

`{`

`ByteBuffer buffer = ByteBuffer.allocateDirect(2048);`

`while (src.read(buffer) != -1)`

`{`

`buffer.flip();`

`while (buffer.hasRemaining())`

`dest.write(buffer);`

`buffer.clear();`

`}`

`}`

`}`

清单 7-1 展示了两种将字节从标准输入流复制到标准输出流的方法。第一种方法以`copy()`方法为例，其目标是尽量减少操作系统 I/O 调用（通过`write()`方法调用），尽管由于`compact()`方法的调用，最终可能会复制更多的数据。第二种方法，如`copyAlt()`所示，目标是消除数据复制，尽管可能会发生更多的操作系统 I/O 调用。

`copy()`和`copyAlt()`方法首先分配一个直接字节缓冲区（回想一下，直接字节缓冲区是在 Java 虚拟机[JVM]上执行 I/O 最高效的方式），然后进入一个`while`循环，该循环持续从源通道读取字节，直到输入结束（`read()`返回`-1`）。读取之后，缓冲区被翻转以便可以排出数据。这就是两种方法开始分叉的地方。

*   `copy()`方法的`while`循环对`write()`进行单次调用。因为`write()`可能不会完全排空缓冲区，所以在下一次读取之前调用`compact()`来压缩缓冲区。压缩确保未写入的缓冲区内容在下一次读取操作期间不会被覆盖。在`while`循环之后，`copy()`翻转缓冲区以准备排出任何剩余内容，然后配合`hasRemaining()`和`write()`完全排空缓冲区。
*   `copyAlt()`方法的`while`循环包含一个嵌套的`while`循环，该循环配合`hasRemaining()`和`write()`持续排空缓冲区，直到缓冲区为空。随后调用`clear()`方法，该方法清空缓冲区，以便在下一次`read()`调用时可以填充它。

注意

认识到单次`write()`方法调用可能不会输出缓冲区的全部内容是很重要的。同样，单次`read()`调用也可能不会完全填满一个缓冲区。

按如下方式编译清单 7-1：

`javac ChannelDemo.java`

按如下方式运行生成的应用程序：

`java ChannelDemo`

`java ChannelDemo <ChannelDemo.java >ChannelDemo.bak`

第一个命令行将标准输入复制到标准输出。第二个命令行将`ChannelDemo.java`的内容复制到`ChannelDemo.bak`。测试完`copy()`方法后，将`copy(src, dest);`替换为`copyAlt(src, dest);`并重复测试。

## 深入理解通道

前面关于`Channel`接口及其直接子接口的讨论让你对通道有了一些了解。然而，还有更多内容值得探索。本节通过探讨分散/聚集 I/O、文件通道、套接字通道和管道，带你更深入地了解通道。



### 分散/聚集 I/O

通道提供了在多个缓冲区上执行单个 I/O 操作的能力。这种能力被称为分散/聚集 I/O（也称为向量 I/O）。

在写操作中，多个缓冲区的内容会按顺序被聚集（排空），然后通过通道发送到目的地。这些缓冲区不需要具有相同的容量。在读操作中，通道的内容会按顺序被分散（填充）到多个缓冲区中；每个缓冲区都会被填充到其上限，直到通道为空或所有缓冲区空间被用完。

注意

现代操作系统提供了支持向量 I/O 的 API，以消除（或至少减少）系统调用或缓冲区复制，从而提高性能。例如，Win32/Win64 API 为此提供了 `ReadFileScatter()` 和 `WriteFileGather()` 函数。

Java 提供了 `java.nio.channels.ScatteringByteChannel` 接口来支持分散操作，以及 `java.nio.channels.GatheringByteChannel` 接口来支持聚集操作。

`ScatteringByteChannel` 提供了以下方法：

*   `long read(ByteBuffer[] buffers, int offset, int length)`
*   `long read(ByteBuffer[] buffers)`

`GatheringByteChannel` 提供了以下方法：

*   `long write(ByteBuffer[] buffers, int offset, int length)`
*   `long write(ByteBuffer[] buffers)`

第一个 `read()` 方法和第一个 `write()` 方法允许你通过向 `offset` 传递一个从零开始的偏移量来指定要读取/写入的第一个缓冲区，并通过向 `length` 传递一个值来指定要读取/写入的缓冲区数量。第二个 `read()` 方法和第二个 `write()` 方法会按顺序读取和写入所有缓冲区。

清单 7-2 演示了 `read(ByteBuffer[] buffers)` 和 `write(ByteBuffer[] buffers)`。

清单 7-2. 演示分散/聚集

`import java.io.FileInputStream;`

`import java.io.FileOutputStream;`

`import java.io.IOException;`

`import java.nio.ByteBuffer;`

`import java.nio.channels.Channels;`

`import java.nio.channels.GatheringByteChannel;`

`import java.nio.channels.ReadableByteChannel;`

`import java.nio.channels.ScatteringByteChannel;`

`public class ChannelDemo`

`{`

`public static void main(String[] args) throws IOException`

`{`

`ScatteringByteChannel src;`

`FileInputStream fis = new FileInputStream("x.dat");`

`src = (ScatteringByteChannel) Channels.newChannel(fis);`

`ByteBuffer buffer1 = ByteBuffer.allocateDirect(5);`

`ByteBuffer buffer2 = ByteBuffer.allocateDirect(3);`

`ByteBuffer[] buffers = { buffer1, buffer2 };`

`src.read(buffers);`

`buffer1.flip();`

`while (buffer1.hasRemaining())`

`System.out.println(buffer1.get());`

`System.out.println();`

`buffer2.flip();`

`while (buffer2.hasRemaining())`

`System.out.println(buffer2.get());`

`buffer1.rewind();`

`buffer2.rewind();`

`GatheringByteChannel dest;`

`FileOutputStream fos = new FileOutputStream("y.dat");`

`dest = (GatheringByteChannel) Channels.newChannel(fos);`

`buffers[0] = buffer2;`

`buffers[1] = buffer1;`

`dest.write(buffers);`

`}`

`}`

清单 7-2 的 `main()` 方法首先通过实例化 `java.io.FileInputStream` 并将此实例传递给 `Channels` 类的 `ReadableByteChannel newChannel(InputStream inputStream)` 方法来获取一个分散字节通道。返回的 `ReadableByteChannel` 实例被转换为 `ScatteringByteChannel`，因为该实例实际上是一个实现了 `ScatteringByteChannel` 的文件通道（稍后讨论）。

接下来，`main()` 创建了一对直接字节缓冲区；第一个缓冲区容量为 5 字节，第二个缓冲区容量为 3 字节。这些缓冲区随后被存储在一个数组中，并将该数组传递给 `read(ByteBuffer[])` 来填充它们。

填充缓冲区后，`main()` 翻转它们，以便将其内容输出到标准输出。输出这些内容后，缓冲区被倒回，准备通过聚集操作进行排空。

现在，`main()` 通过实例化 `java.io.FileOutputStream` 并将此实例传递给 `Channels` 类的 `WritableByteChannel` `newChannel(OutputStream outputStream)` 方法来获取一个聚集字节通道。返回的 `WritableByteChannel` 实例被转换为 `GatheringByteChannel`，因为该实例实际上是一个实现了 `GatheringByteChannel` 的文件通道（稍后讨论）。

最后，`main()` 将这些缓冲区以与原始分配顺序相反的顺序分配给 `buffers` 数组，然后将该数组传递给 `write(ByteBuffer[])` 来排空它们。

创建一个名为 `x.dat` 的文件，并在该文件中存储以下文本：

`12345abcdefg`

现在编译清单 7-2（`javac ChannelDemo.java`）并运行此应用程序（`java ChannelDemo`）。你应该会看到前八个字符的以下 Unicode 值：

`49`

`50`

`51`

`52`

`53`

`97`

`98`

`99`

此外，你应该会看到一个新创建的 `y.dat` 文件，其内容如下：

`abc12345`

### 文件通道

我之前提到过，`RandomAccessFile` 声明了一个 `FileChannel` `getChannel()` 方法，用于返回一个文件通道实例，该实例描述了一个到文件的打开连接。事实证明，`FileInputStream` 和 `FileOutputStream` 也提供了相同的方法。相比之下，`java.io.FileReader` 和 `java.io.FileWriter` 不提供获取文件通道的方式。

警告

从 `FileInputStream` 的 `getChannel()` 方法返回的文件通道是只读的，而从 `FileOutputStream` 的 `getChannel()` 方法返回的文件通道是只写的。尝试写入只读文件通道或从只写文件通道读取将导致异常。

抽象的 `java.nio.channels.FileChannel` 类描述了一个文件通道。由于此类实现了 `InterruptibleChannel` 接口，文件通道是可中断的。由于此类实现了 `ByteChannel`、`GatheringByteChannel` 和 `ScatteringByteChannel` 接口，你可以对底层文件进行写入、读取以及执行分散/聚集 I/O 操作。然而，还不止这些。

注意

与线程不安全的缓冲区不同，文件通道是线程安全的。

文件通道维护一个文件中的当前位置，`FileChannel` 允许你获取和更改该位置。它还允许你请求将缓存数据强制写入磁盘、读取/写入文件内容、获取通道底层文件的大小、截断文件、尝试锁定整个文件或仅锁定文件的一部分、执行内存映射文件 I/O，以及以可能被操作系统优化的方式直接将数据传输到另一个通道。

表 7-1 描述了 `FileChannel` 的一些方法。

表 7-1.

`FileChannel` 方法



| 方法 | 描述 |
| --- | --- |
| `void force(boolean metadata)` | 请求将此文件通道的所有更新提交到存储设备。当此方法返回时，若文件位于本地存储设备上，则对该通道底层文件所做的所有修改均已提交。然而，当文件不在本地托管时（例如，位于网络文件系统上），应用程序无法确定修改是否已提交。（不保证使用其他位置定义的方法对文件所做的更改会被提交。例如，通过映射字节缓冲区所做的更改可能不会被提交。）`metadata` 值指示更新是否应包含文件的元数据（例如最后修改时间和最后访问时间）：传入 `true` 时包含，传入 `false` 时不包含。即使通道以只读模式打开，传入 `true` 也可能触发对操作系统的底层写入（如果操作系统正在维护元数据，例如最后访问时间）。当通道已关闭时，此方法抛出 `ClosedChannelException`；当发生任何其他 I/O 错误时，抛出 `IOException`。 |
| `long position()` | 返回此文件通道维护的当前基于零的文件位置。当文件通道已关闭时，此方法抛出 `ClosedChannelException`；当发生其他 I/O 错误时，抛出 `IOException`。 |
| `FileChannel position(long newPosition)` | 将此文件通道的当前文件位置设置为 `newPosition`。该参数是从文件开头计数的字节数。位置不能设置为负值。但是，可以将其设置为超出当前文件大小。如果设置为超出当前文件大小，则读取尝试将返回文件结尾。写入操作将成功，但会用所需数量的（未指定的）字节值填充当前文件结尾与新位置之间的字节。当 `offset` 为负数时，此方法抛出 `java.lang.IllegalArgumentException`；当文件通道已关闭时，抛出 `ClosedChannelException`；当发生其他 I/O 错误时，抛出 `IOException`。 |
| `int read(ByteBuffer buffer)` | 从此文件通道读取字节到给定的缓冲区。读取的最大字节数是调用方法时缓冲区中剩余的字节数。字节将从缓冲区的当前位置开始复制到缓冲区中。当其他线程也尝试从此通道读取时，该调用可能会阻塞。完成后，缓冲区的位置将设置为已读取字节的末尾。缓冲区的限制不会改变。此方法返回实际读取的字节数，并抛出与之前讨论的 `ReadableByteChannel` 相同的异常。 |
| `int read(ByteBuffer dst, long position)` | 与前一个方法等效，不同之处在于从指定的文件 `position` 开始读取字节。当 `position` 为负数时，抛出 `IllegalArgumentException`。 |
| `long size()` | 返回此文件通道底层文件的大小（以字节为单位）。当文件通道已关闭时，此方法抛出 `ClosedChannelException`；当发生其他 I/O 错误时，抛出 `IOException`。 |
| `FileChannel truncate(long size)` | 将此文件通道的底层文件截断为 `size`。超出给定 `size` 的任何字节都将从文件中删除。当没有超出给定 `size` 的字节时，文件内容保持不变。当当前文件位置大于给定 `size` 时，将其设置为 `size`。 |
| `int write(ByteBuffer buffer)` | 从给定的 `buffer` 向此文件通道写入一系列字节。除非通道处于追加模式（在这种情况下，位置首先前进到文件末尾），否则字节从通道的当前文件位置开始写入。文件会（在必要时）增长以容纳写入的字节，然后文件位置会更新为实际写入的字节数。除此之外，此方法的行为与 `WritableByteChannel` 接口指定的完全相同。此方法返回实际写入的字节数，并抛出与之前讨论的 `WritableByteChannel` 相同的异常。 |
| `int write(ByteBuffer src, long position)` | 与前一个方法等效，不同之处在于从指定的文件 `position` 开始写入字节。当 `position` 为负数时，抛出 `IllegalArgumentException`。 |



`force(boolean)`方法确保对本地文件系统中某个文件所做的所有修改（自上次调用该方法以来）都被写入磁盘。此功能对于事务处理等关键任务至关重要，在这些任务中，必须维护数据完整性并确保可靠的恢复。然而，此保证不适用于远程文件系统。

向`force(boolean)`传递`true`会导致元数据（最后修改时间、访问权限等）也同步到磁盘。由于元数据通常对文件恢复并不关键，因此通常可以传递`false`，从而获得小幅性能提升，因为无需额外的 I/O 操作来输出元数据。

`FileChannel`对象支持当前文件位置的概念，该位置决定了下一个数据项将被读取或写入的位置。`position()`方法返回当前位置，而`position(long newPosition)`方法将当前位置设置为`newPosition`。传递给`newPosition`的值必须为非负数，否则将抛出`IllegalArgumentException`。

`read()`和`write()`方法有两种形式。相对形式不接受`position`参数，并确保在调用任一方法后更新当前文件位置。这些方法的绝对形式接受一个`position`参数，并且不更新位置。绝对读取和写入可能更高效，因为通道的状态无需更新。

如果尝试在文件末尾之后（由`size()`返回）执行绝对读取，则会返回`-1`以表示文件结束。尝试在文件末尾之后执行绝对写入会导致文件增长以容纳正在写入的字节。位于先前文件末尾与第一个新写入字节之间的字节值取决于文件系统，并且可能构成一个空洞。

当为文件分配的磁盘空间量小于文件大小时，文件中就会出现空洞。现代文件系统通常只为写入文件的数据分配空间。当数据写入非连续区域时，可能会出现空洞。读取文件时，空洞通常显示为零填充，但不占用磁盘空间。

`truncate(long size)`方法用于减小文件大小。此方法会截断指定`size`之后的所有数据。当文件大小大于指定的`size`时，指定`size`之后的所有字节都将被丢弃。当指定的`size`大于或等于当前大小时，文件不会改变。

清单 7-3 演示了表 7-1 中的各种方法。

清单 7-3. 演示文件通道

`import java.io.IOException;`

`import java.io.RandomAccessFile;`

`import java.nio.ByteBuffer;`

`import java.nio.channels.FileChannel;`

`public class ChannelDemo`

`{`

`public static void main(String[] args) throws IOException`

`{`

`RandomAccessFile raf = new RandomAccessFile("temp", "rw");`

`FileChannel fc = raf.getChannel();`

`long pos;`

`System.out.println("Position = " + (pos = fc.position()));`

`System.out.println("size: " + fc.size());`

`String msg = "This is a test message.";`

`ByteBuffer buffer = ByteBuffer.allocateDirect(msg.length() * 2);`

`buffer.asCharBuffer().put(msg);`

`fc.write(buffer);`

`fc.force(true);`

`System.out.println("position: " + fc.position());`

`System.out.println("size: " + fc.size());`

`buffer.clear();`

`fc.position(pos);`

`fc.read(buffer);`

`buffer.flip();`

`while (buffer.hasRemaining())`

`System.out.print(buffer.getChar());`

`}`

`}`

清单 7-3 的`main()`方法首先创建一个名为`temp`的随机访问文件，用于写入和读取。然后，它获取一个文件通道以与此文件通信，并报告文件通道的当前位置和文件大小，对于新创建的文件，两者均为`0`。

接下来，`main()`分配一个直接字节缓冲区，用于存储要写入文件的消息，将此缓冲区视为字符缓冲区，并调用字符缓冲区的`put()`方法将消息存储在缓冲区中，然后将其输出到文件。

现在，`main()`调用`force(true)`，建议底层操作系统将数据提交到底层存储设备。

在报告新的当前位置和文件大小后，`main()`清除缓冲区，将文件位置重置为写入消息之前的位置，并将先前写入的内容读回缓冲区。然后，它翻转缓冲区并输出其内容。

编译清单 7-3（`javac ChannelDemo.java`）并运行此应用程序（`java ChannelDemo`）。您应该会看到以下输出：

`Position = 0`

`size: 0`

`position: 46`

`size: 46`

`This is a test message.`

在后续运行时，存在名为`temp`的文件会将输出更改为以下内容：

`Position = 0`

`size: 46`

`position: 46`

`size: 46`

`This is a test message.`



#### 锁定文件

在 Java 1.4 出现之前，锁定文件全部或部分内容的能力一直是 Java 中一个缺失的重要特性。此功能允许一个 JVM 进程阻止其他进程访问文件的全部或部分内容，直到该进程完成对整个文件或文件部分的操作。

虽然可以锁定整个文件，但通常更可取的做法是锁定一个较小的区域。例如，数据库管理系统可能会锁定正在更新的单个表行，而不是锁定整个表，以便读取请求能够被处理，从而提高吞吐量。

与文件关联的锁被称为文件锁。每个文件锁从文件中的某个特定字节位置开始，并具有从该位置开始的特定长度（以字节为单位）。它们共同定义了锁所管辖的区域。文件锁允许进程协调对文件中不同区域的访问。

文件锁有两种类型：排他锁和共享锁。排他锁允许单个写入进程访问文件区域；它禁止同时对该区域应用其他文件锁。共享锁允许多个读取进程中的一个访问同一文件区域；它不禁止其他共享锁，但禁止同时对该区域应用排他锁。

排他锁和共享锁通常用于共享文件主要被读取、偶尔被更新的场景。需要从文件读取的进程会获取整个文件或所需子区域的共享锁。另一个也需要从文件读取的进程会获取所需区域的共享锁。这两个进程可以互不干扰地读取文件。

假设第三个进程想要执行更新。为此，它会请求一个排他锁。该进程将阻塞，直到所有与其区域重叠的排他锁或共享锁被释放。一旦更新进程获得了排他锁，任何请求共享锁的读取进程都将阻塞，直到排他锁被释放。这样，更新进程就可以更新文件，而读取进程不会观察到不一致的数据。

关于文件锁定，还有几点需要牢记：

*   当操作系统不支持共享锁时，共享锁请求会被静默提升为排他锁请求。虽然正确性得到了保证，但性能可能会受到影响。
*   锁是基于每个文件应用的，而不是基于每个线程或每个通道。运行在同一个 JVM 上的两个线程，通过不同的通道请求对同一文件区域的排他锁，都会被授予访问权限。但是，如果这些线程运行在不同的 JVM 上，第二个线程将会阻塞。锁最终由操作系统的文件系统仲裁，并且几乎总是在进程级别进行仲裁，而不是在线程级别。锁与文件关联，而不是与文件句柄或通道关联。

`FileChannel` 声明了四种获取排他锁和共享锁的方法：

*   `FileLock lock()`: 获取此文件通道底层文件的排他锁。这个便捷方法等同于执行 `fileChannel.lock(0L, Long.MAX_VALUE, false);`，其中 `fileChannel` 引用一个文件通道。

此方法返回一个代表锁定区域的 `java.nio.channels.FileLock` 对象。当文件通道关闭时抛出 `ClosedChannelException`；当通道未打开用于写入时抛出 `NonWritableChannelException`；当与此 JVM 已持有的锁重叠，或另一个线程已在此方法中阻塞并试图锁定同一文件的重叠区域时抛出 `java.nio.channels.OverlappingFileLockException`；当调用线程在等待获取锁时被中断抛出 `java.nio.channels.FileLockInterruptionException`；当调用线程在等待获取锁时通道被关闭抛出 `AsynchronousCloseException`；以及在获取请求的锁时发生其他 I/O 错误时抛出 `IOException`。

*   `FileLock lock(long position, long size, boolean shared)`: 此方法与前一个方法类似，不同之处在于它尝试获取此通道文件指定区域上的锁。向 `position` 和 `size` 传递非负值以界定区域。向 `shared` 传递 `true` 以请求共享锁，传递 `false` 以请求排他锁。
*   `FileLock tryLock()`: 尝试在不阻塞的情况下获取此文件通道底层文件的排他锁。这个便捷方法等同于执行 `fileChannel.tryLock(0L, Long.MAX_VALUE, false);`，其中 `fileChannel` 引用一个文件通道。此方法返回一个代表锁定区域的 `FileLock` 对象，如果该锁与另一个操作系统进程中的现有排他锁重叠，则返回 `null`。当文件通道关闭时抛出 `ClosedChannelException`；当与此 JVM 已持有的锁重叠，或另一个线程已在此方法中阻塞并试图锁定重叠区域时抛出 `OverlappingFileLockException`；以及在获取请求的锁时发生其他 I/O 错误时抛出 `IOException`。
*   `FileLock tryLock(long position, long size, boolean shared)`: 此方法与前一个方法类似，不同之处在于它尝试获取此通道文件指定区域上的锁。向 `position` 和 `size` 传递非负值以界定区域。向 `shared` 传递 `true` 以请求共享锁，传递 `false` 以请求排他锁。

当要锁定的所需区域已被锁定时（除非两个锁都是共享锁），`lock()` 方法会阻塞。相比之下，`tryLock()` 方法会立即返回，如果锁与另一个操作系统进程中的现有排他锁重叠，则返回 `null`。

每个方法都返回一个 `FileLock` 实例，它封装了文件中的一个锁定区域。接下来描述 `FileLock` 的方法：



*   `FileChannel channel()`: 返回获取此锁定的文件所对应的文件通道；如果锁定并非由文件通道获取，则返回 `null`。
*   `void close()`: 调用 `release()` 方法来释放锁定。
*   `boolean isShared()`: 返回 `true` 表示该锁定为共享锁，返回 `false` 表示其为独占锁。
*   `boolean isValid()`: 返回 `true` 表示该锁定有效；否则返回 `false`。锁定在释放或关联的文件通道关闭之前有效，以先发生者为准。
*   `boolean overlaps(long position, long size)`: 指示此锁定的区域是否与参数列表中描述的区域重叠（返回 `true`）或不重叠（返回 `false`）。
*   `long position()`: 返回锁定区域第一个字节在文件中的位置。锁定区域不需要包含在底层文件中，甚至不需要与其重叠，因此此方法返回的值可能超过文件的当前大小。
*   `void release()`: 释放此锁定。如果此锁定对象有效，调用此方法将释放锁定并使该对象失效。如果此锁定对象无效，调用此方法将不起作用。
*   `long size()`: 返回文件锁定的长度（以字节为单位）。
*   `String toString()`: 返回一个描述此锁定范围、类型和有效性的字符串。

一个 `FileLock` 实例与一个 `FileChannel` 实例相关联，但 `FileLock` 实例所代表的文件锁定是与底层文件相关联，而不是与文件通道相关联。如果不加注意，在使用完文件锁定后未释放它，可能会遇到冲突（甚至可能发生死锁）。为避免这些问题，应采用如下模式来确保文件锁定始终被释放：

`FileLock lock = fileChannel.lock();`

`try`

`{`

`// 与文件通道交互`

`}`

`catch (IOException ioe)`

`{`

`// 处理异常`

`}`

`finally`

`{`

`lock.release();`

`}`

我创建了一个演示文件锁定的应用程序。它遵循此模式以确保锁定被释放。清单 7-4 展示了其源代码。

清单 7-4\. 演示文件锁定

`import java.io.IOException;`

`import java.io.RandomAccessFile;`

`import java.nio.ByteBuffer;`

`import java.nio.IntBuffer;`

`import java.nio.channels.FileChannel;`

`import java.nio.channels.FileLock;`

`public class ChannelDemo`

`{`

`final static int MAXQUERIES = 150000;`

`final static int MAXUPDATES = 150000;`

`final static int RECLEN = 16;`

`static ByteBuffer buffer = ByteBuffer.allocate(RECLEN);`

`static IntBuffer intBuffer = buffer.asIntBuffer();`

`static int counter = 1;`

`public static void main(String[] args) throws IOException`

`{`

`boolean writer = false;`

`if (args.length != 0)`

`writer = true;`

`RandomAccessFile raf = new RandomAccessFile("temp",`

`(writer) ? "rw" : "r");`

`FileChannel fc = raf.getChannel();`

`if (writer)`

`update(fc);`

`else`

`query(fc);`

`}`

`static void query(FileChannel fc) throws IOException`

`{`

`for (int i = 0; i < MAXQUERIES; i++)`

`{`

`System.out.println("acquiring shared lock");`

`FileLock lock = fc.lock(0, RECLEN, true);`

`try`

`{`

`buffer.clear();`

`fc.read(buffer, 0);`

`int a = intBuffer.get(0);`

`int b = intBuffer.get(1);`

`int c = intBuffer.get(2);`

`int d = intBuffer.get(3);`

`System.out.println("Reading: " + a + " " +`

`b + " " +`

`c + " " +`

`d);`

`if (a * 2 != b || a * 3 != c || a * 4 != d)`

`{`

`System.out.println("error");`

`return;`

`}`

`}`

`finally`

`{`

`lock.release();`

`}`

`}`

`}`

`static void update(FileChannel fc) throws IOException`

`{`

`for (int i = 0; i < MAXUPDATES; i++)`

`{`

`System.out.println("acquiring exclusive lock");`

`FileLock lock = fc.lock(0, RECLEN, false);`

`try`

`{`

`intBuffer.clear();`

`int a = counter;`

`int b = counter * 2;`

`int c = counter * 3;`

`int d = counter * 4;`

`System.out.println("Writing: " + a + " " +`

`b + " " +`

`c + " " +`

`d);`

`intBuffer.put(a);`

`intBuffer.put(b);`

`intBuffer.put(c);`

`intBuffer.put(d);`

`counter++;`

`buffer.clear();`



`fc.write(buffer, 0);`

`}`

`finally`

`{`

`lock.release();`

`}`

`}`

`}`

`}`

清单 7-4 描述了一个应用程序，该程序要么更新名为 `temp` 的文件，要么查询该文件。由于文件锁定作用于进程级别而非线程级别，你需要运行该应用程序的两个副本，以便自行演示文件锁定。一个副本将充当写入者，负责更新文件；另一个副本将充当读取者，负责查询文件。

`ChannelDemo` 类首先声明了一对常量，用于控制更新循环和查询循环的持续时间，以及一个表示记录长度的常量。然后，它分配了一个能够容纳整个 16 字节记录的字节缓冲区，以及一个基于 `int` 的视图缓冲区，用于将字节缓冲区视为四个 `int` 值的序列。最后，声明了一个初始化为 `1` 的 `counter` 变量。

`main()` 方法首先确定应用程序是作为写入者还是读取者运行。如果你指定了任何命令行参数，则假定为写入者。然后，该方法以随机访问文件的形式打开（对于读取者）或创建（对于写入者）`temp`。如果你以读取者身份运行应用程序时该文件不存在，则会抛出异常。

打开或创建 `temp` 后，会获取该文件的文件通道。然后，该通道会被传递给 `update()` 或 `query()` 方法。

考虑 `update()` 方法。该方法接收文件通道参数，并进入一个固定长度的 `for` 循环，其持续时间由 `MAXUPDATES` 常量控制。在输出一条获取锁的消息后，它尝试获取整个 16 字节记录的排他锁。如果读取者进程已通过共享锁锁定了该记录，则 `lock()` 会阻塞，直到共享锁被释放。

一旦获得锁，视图缓冲区会被清除，这会将位置设置为 0，并将限制设置为容量。此时缓冲区已准备好被完全填充。

现在访问 `counter` 变量的当前值，并将其保存在一个变量中。该值乘以 2、3 和 4，结果也保存在各自的变量中。在输出一条标识这些值的写入消息后，`update()` 在视图缓冲区上调用四次 `put()` 方法，将这些值存储到字节缓冲区中。`counter` 变量递增，字节缓冲区被清除以确保其能被完全排空。最后，调用文件通道的 `write()` 方法，将缓冲区排空到底层 `temp` 文件中。

`query()` 方法与 `update()` 方法结构相似。然而，它使用文件通道读取 `temp` 文件的记录，并将结果存储到字节缓冲区中。在输出一条显示读取结果的消息后，它会验证这些值是否正确。任何与预期值的偏差都会导致该方法在输出错误消息后终止。

编译清单 7-4（`javac ChannelDemo.java`），并在一个命令窗口中执行以下命令行：

`java ChannelDemo w`

你应该会看到类似如下的消息：

`acquiring exclusive lock`

`Writing: 1 2 3 4`

`acquiring exclusive lock`

`Writing: 2 4 6 8`

`acquiring exclusive lock`

`Writing: 3 6 9 12`

`acquiring exclusive lock`

`Writing: 4 8 12 16`

`acquiring exclusive lock`

`Writing: 5 10 15 20`

在第二个命令窗口中，执行以下命令行：

`java ChannelDemo`

你应该会看到类似如下的消息：

`acquiring shared lock`

`Reading: 2500 5000 7500 10000`

`acquiring shared lock`

`Reading: 2501 5002 7503 10004`

`acquiring shared lock`

`Reading: 2502 5004 7506 10008`

`acquiring shared lock`

`Reading: 2503 5006 7509 10012`

`acquiring shared lock`

`Reading: 2504 5008 7512 10016`

如果你运行这些应用程序直至它们完成，应该不会看到任何错误消息。文件锁定确保只有写入者进程或读取者进程能够访问 `temp` 的 16 字节记录。当这些字节被锁定时，另一个进程会被拒绝访问。因此，该记录的值不会出现损坏。

为了向自己证明文件锁定确实在起作用，请注释掉上述清单中的以下四行，重新编译 `ChannelDemo.java`，并分别以写入者和读取者身份重新运行该应用程序：

`FileLock lock = fc.lock(0, RECLEN, true);`

`FileLock lock = fc.lock(0, RECLEN, false);`

`lock.release();`

`lock.release();`

在执行的某个时刻，你应该会看到类似如下所示的输出：

`acquiring shared lock`

`Reading: 803 1606 2412 3216`

`error`

该输出是无效的——它本应是 `803 1606 2409 3212`，但由于读取者和写入者能够同时访问该记录，因此结果不正确。

注意

运行的 `ChannelDemo` 读取者进程越多，`ChannelDemo` 写入者进程的运行速度就会越慢。最终，`ChannelDemo` 写入者进程在尝试获取锁时会阻塞，并且无法解除阻塞，因为总会有一个共享锁正在使用中。



#### 将文件映射到内存

`FileChannel` 声明了一个 `map()` 方法，该方法允许你在打开文件的某个区域与一个包装此区域的 `java.nio.MappedByteBuffer` 实例之间创建虚拟内存映射。这种映射机制提供了一种高效访问文件的方式，因为执行 I/O 操作无需耗时的系统调用。

注意

虚拟内存是一种用虚拟地址（也称为人工地址）替代物理（RAM）地址的内存。请查阅维基百科的“虚拟内存”主题（ [`http://en.wikipedia.org/wiki/Virtual_memory`](http://en.wikipedia.org/wiki/Virtual_memory) ）以了解更多关于虚拟内存的信息。

`map()` 方法具有以下签名：

`MappedByteBuffer map(FileChannel.MapMode mode, long position, long size)`

`mode` 参数定义了映射模式，并接收由 `FileChannel.MapMode` 枚举类型定义的以下常量之一：

*   `READ_ONLY`：任何修改缓冲区的尝试都会导致抛出 `java.nio.ReadOnlyBufferException`。
*   `READ_WRITE`：对结果缓冲区所做的更改最终会传播到文件；这些更改可能不会对映射了同一文件的其他程序可见。
*   `PRIVATE`：对结果缓冲区所做的更改不会传播到文件，也不会对映射了同一文件的其他程序可见。相反，更改会导致创建缓冲区修改部分的私有副本。当缓冲区被垃圾回收时，这些更改将丢失。

指定的映射模式受调用 `FileChannel` 对象的访问权限约束。例如，如果文件通道是以只读通道方式打开的，并且你请求 `READ_WRITE` 模式，`map()` 将抛出 `NonWritableChannelException`，因为它无法写入文件通道。类似地，当通道以只写方式打开并且你请求 `READ_ONLY` 模式时，会抛出 `NonReadableChannelException`。（你可以为以读写通道方式打开的文件通道请求 `READ_ONLY` 模式。）

提示

调用 `MappedByteBuffer` 的 `isReadOnly()` 方法来确定你是否可以修改映射的文件。

`position` 和 `size` 参数定义了映射区域的起始位置和范围。传递给这些参数的参数必须为非负数。此外，传递给 `size` 的参数不得超过 `Integer.MAX_VALUE`。

指定的范围不应超过文件的大小，因为文件会被扩大以容纳该范围。例如，如果你将 `Integer.MAX_VALUE` 传递给 `size`，文件将增长到超过两 GB。此外，对于只读映射，`map()` 可能会抛出 `IOException`。

返回的 `MappedByteBuffer` 对象的行为类似于内存映射缓冲区，但其内容存储在文件中。当你在此对象上调用 `get()` 时，将获取文件的当前内容，即使这些内容已被外部程序修改。类似地，当你拥有写入权限时，调用 `put()` 会更新文件，并且更改对外部程序可用。

注意

由于映射字节缓冲区是直接字节缓冲区，分配给它们的内存空间存在于 JVM 堆之外。

考虑以下示例：

`MappedByteBuffer buffer = fileChannel.map(FileChannel.MapMode.READ_ONLY, 50, 100);`

此示例映射了 `fileChannel` 所描述文件的一个子范围，从位置 50 到位置 149。相比之下，以下示例映射了整个文件：

`MappedByteBuffer buffer = fileChannel.map(FileChannel.MapMode.READ_ONLY, 0, fileChannel.size());`

没有 `unmap()` 方法。一旦建立了映射，它将一直存在，直到 `MappedByteBuffer` 对象被垃圾回收（或应用程序退出，以先发生者为准）。由于映射的字节缓冲区与其创建时所用的文件通道没有连接，因此当文件通道关闭时，映射不会被销毁。



`MappedByteBuffer` 继承自其超类 `java.nio.ByteBuffer` 的方法。它还声明了以下方法：

*   `MappedByteBuffer load()`：尝试将映射文件的所有内容加载到内存中。对于大文件，这可以显著提高访问速度，因为虚拟内存管理器无需在遍历映射缓冲区时（通过读取/写入相应位置）按需将文件部分加载到内存。尽管 `load()` 会尽力而为，但可能不会成功，因为外部程序可能促使虚拟内存管理器移除已加载文件内容的部分，以便为它们将内容加载到物理内存的请求腾出空间。此外，`load()` 在时间上可能开销较大，因为它可能导致虚拟内存管理器执行大量 I/O 操作；此方法可能需要一段时间才能完成。
*   `boolean isLoaded()`：当映射文件的所有内容都已加载到内存时返回 `true`；否则返回 `false`。如果此方法返回 `true`，则很可能只需很少或无需 I/O 操作即可访问所有内容。如果此方法返回 `false`，缓冲区访问仍可能很快，并且映射内容可能完全驻留在内存中。可将 `isLoaded()` 视为对映射字节缓冲区状态的提示。
*   `MappedByteBuffer force()`：将对映射字节缓冲区所做的更改写入永久存储。处理映射字节缓冲区时，应调用此方法而非文件通道的 `force()` 方法，因为通道可能不知道通过映射字节缓冲区所做的各种更改。调用此方法对 `READ_ONLY` 和 `PRIVATE` 映射没有影响。

清单 7-5 展示了一个演示文件映射的应用程序。

清单 7-5. 演示文件映射

`import java.io.IOException;`

`import java.io.RandomAccessFile;`

`import java.nio.ByteBuffer;`

`import java.nio.MappedByteBuffer;`

`import java.nio.channels.FileChannel;`

`public class ChannelDemo`

`{`

`public static void main(String[] args) throws IOException`

`{`

`if (args.length != 1)`

`{`

`System.out.println("usage: java ChannelDemo filespec");`

`return;`

`}`

`RandomAccessFile raf = new RandomAccessFile(args[0], "rw");`

`FileChannel fc = raf.getChannel();`

`long size = fc.size();`

`System.out.println("Size: " + size);`

`MappedByteBuffer mbb = fc.map(FileChannel.MapMode.READ_WRITE, 0,`

`size);`

`while (mbb.remaining() > 0)`

`System.out.print((char) mbb.get());`

`System.out.println();`

`System.out.println();`

`for (int i = 0; i < mbb.limit() / 2; i++)`

`{`

`byte b1 = mbb.get(i);`

`byte b2 = mbb.get(mbb.limit() - i - 1);`

`mbb.put(i, b2);`

`mbb.put(mbb.limit() - i - 1, b1);`

`}`

`mbb.flip();`

`while (mbb.remaining() > 0)`

`System.out.print((char) mbb.get());`

`fc.close();`

`}`

`}`

在确认你指定了一个命令行参数（该参数应标识一个现有文件）后，`main()` 创建一个 `RandomAccessFile` 对象，用于以读/写模式访问此文件。然后，它获取一个文件通道以与此文件通信。

使用文件通道获取文件大小（随后输出该大小）后，`main()` 使用文件通道调用 `map()` 以获取整个文件的读/写映射。随后，它输出返回的映射字节缓冲区的内容。

之后，`main()` 进入一个 `for` 循环，其目的是反转文件的内容。在每次迭代中，获取两个相互对称的字节，然后交换它们。离开此循环后，`main()` 翻转缓冲区以便清空，并输出其反转后的内容。

编译清单 7-5（`javac ChannelDemo.java`），并假设存在一个 `poem.txt` 文件，执行以下命令行以反转此文件的内容：

`java ChannelDemo poem.txt`

你应该会看到类似于以下的输出：

`Size: 67`

`Roses are red,`

`Violets are blue,`

`Sugar is sweet,`

`And so are you!`

`!uoy era os dnA`

`,teews si raguS`

`,eulb era steloiV`

`,der era sesoR`

请注意，反转文本中的空行是由于在 Windows 操作系统上反转了回车符（13）/换行符（10）序列所致。

此外，`poem.txt` 的内容应该已被反转。



#### 在通道间传输字节

为了优化执行批量传输的常见做法，`FileChannel` 新增了两个方法，从而避免了对中间缓冲区的需求：

*   `long transferFrom(ReadableByteChannel src, long position, long count)`
*   `long transferTo(long position, long count, WritableByteChannel target)`

`transferFrom(ReadableByteChannel, long, long)` 方法将字节从给定的可读字节通道传输到此通道的文件中。参数 `src` 标识源通道，`position` 标识文件中传输开始的非负起始位置，`count` 标识要传输的最大字节数（非负）。

此方法返回实际传输的字节数（可能为 0）。当某个参数的前提条件（例如 `position` 为非负数）不满足时，它会抛出 `IllegalArgumentException`；当源通道未以读取方式打开时，抛出 `NonReadableChannelException`；当此通道未以写入方式打开时，抛出 `NonWritableChannelException`；当此通道或源通道已关闭时，抛出 `ClosedChannelException`；当传输进行中另一个线程中断了当前线程，从而导致两个通道都关闭并设置了当前线程的中断状态时，抛出 `ClosedByInterruptException`；当发生其他 I/O 错误时，抛出 `IOException`。

`transferTo(long, long, WritableByteChannel)` 方法将字节从此通道的文件传输到给定的可写字节通道。参数 `position` 标识文件中传输开始的非负起始位置，`count` 标识要传输的最大字节数（非负），`target` 标识目标通道。

此方法返回实际传输的字节数（可能为 0）。当某个参数的前提条件不满足时，它会抛出 `IllegalArgumentException`；当此通道未以读取方式打开时，抛出 `NonReadableChannelException`；当目标通道未以写入方式打开时，抛出 `NonWritableChannelException`；当此通道或目标通道已关闭时，抛出 `ClosedChannelException`；当传输进行中另一个线程中断了当前线程，从而导致两个通道都关闭并设置了当前线程的中断状态时，抛出 `ClosedByInterruptException`；当发生其他 I/O 错误时，抛出 `IOException`。

如果你将 `transferTo()` 与文件通道一起用作传输源，当 `position` 加上 `count` 超过文件大小时，传输会在文件末尾停止。类似地，当 `src` 是文件通道并且到达其文件末尾时，`transferFrom()` 会停止。

清单 7-6 展示了一个演示通道传输的应用程序。

清单 7-6. 演示通道传输

`import java.io.FileInputStream;`

`import java.io.IOException;`

`import java.nio.channels.Channels;`

`import java.nio.channels.FileChannel;`

`import java.nio.channels.WritableByteChannel;`

`public class ChannelDemo`

`{`

`public static void main(String[] args)`

`{`

`if (args.length != 1)`

`{`

`System.err.println("usage: java ChannelDemo filespec");`

`return;`

`}`

`try (FileInputStream fis = new FileInputStream(args[0]))`

`{`

`FileChannel inChannel = fis.getChannel();`

`WritableByteChannel outChannel = Channels.newChannel(System.out);`

`inChannel.transferTo(0, inChannel.size(), outChannel);`

`}`

`catch (IOException ioe)`

`{`

`System.out.println("I/O error: " + ioe.getMessage());`

`}`

`}`

`}`

清单 7-6 的 `main()` 方法验证是否指定了单个命令行参数。此参数标识一个文件，其内容将被复制到标准输出流。

接下来，`main()` 为命令行参数标识的文件创建一个文件输入流，并创建一个用于从该文件读取的文件通道。

最后，获取一个用于将字节发送到标准输出流的输出通道，并调用输入文件通道的 `transferTo()` 方法将文件内容传输到标准输出。

编译清单 7-6 (`javac ChannelDemo.java`) 并执行以下命令行以创建 `ChannelDemo.java` 的副本：

`java ChannelDemo ChannelDemo.java >ChannelDemo.bak`

你应该会看到一个 `ChannelDemo.bak` 文件，其大小和内容与 `ChannelDemo.java` 相同。

### 套接字通道

我之前提到过，`Socket` 声明了一个 `SocketChannel` `getChannel()` 方法，用于返回一个套接字通道实例，该实例描述了一个到套接字的开放连接。与套接字不同，套接字通道是可选择的，并且可以在非阻塞模式下运行。这些能力（将在本章后面讨论）增强了大型应用程序（如 Web 服务器）的可扩展性和灵活性。

套接字通道由 `java.nio.channels` 包中的抽象类 `ServerSocketChannel`、`SocketChannel` 和 `DatagramChannel` 描述。每个类最终都扩展了 `java.nio.channels.SelectableChannel` 并实现了 `InterruptibleChannel`，这使得 `ServerSocketChannel`、`SocketChannel` 和 `DatagramChannel` 实例既可选择又可中断。由于 `SocketChannel` 和 `DatagramChannel` 实现了 `ByteChannel`、`GatheringByteChannel` 和 `ScatteringByteChannel` 接口，你可以对其底层套接字进行写入、读取以及执行分散/聚集 I/O。

注意

与缓冲区不同（缓冲区不是线程安全的），服务器套接字通道、套接字通道和数据报通道是线程安全的。

每个 `ServerSocketChannel`、`SocketChannel` 和 `DatagramChannel` 实例都会从 `java.net.ServerSocket`、`Socket` 或 `java.net.DatagramSocket` 类创建一个对等套接字对象。每个类都已进行改造以与通道协同工作。你可以通过调用 `ServerSocketChannel`、`SocketChannel` 或 `DatagramChannel` 的 `socket()` 方法来获取对等套接字对象。

注意

当在从 `socket()` 返回的套接字实例上调用 `getChannel()` 时，它会返回关联的套接字通道。但是，当在通过实例化 `ServerSocket`、`Socket` 或 `DatagramSocket` 获得的套接字上调用 `getChannel()` 时，它会返回 `null`。



#### 理解非阻塞模式

Java 套接字类创建的套接字其阻塞特性是面向网络的 Java 应用程序可扩展性的严重限制。例如，`ServerSocket` 类的 `Socket accept()` 方法会阻塞，直到有传入连接到达，此时它会创建并返回一个 `Socket` 实例，让服务器能与客户端通信。如果此方法不阻塞，可扩展性将会提高，因为服务器可以完成其他有用的工作，而无需等待。

抽象的 `SelectableChannel` 类是 `ServerSocketChannel`、`SocketChannel` 和 `DatagramChannel` 类的共同祖先。除了让套接字通道能在选择器上下文中工作（我在第 8 章中讨论了选择器），`SelectableChannel` 还允许套接字通道选择阻塞或以非阻塞模式运行。在非阻塞模式下，线程可以在没有输入可用或输出缓冲区已满时检查输入或发送输出而不会阻塞。

注意

`SelectableChannel` 将选择器相关的功能与非阻塞模式合并，因为非阻塞模式在与基于选择器的多路复用结合时最为有用。

`SelectableChannel` 提供了以下方法来启用阻塞或非阻塞、判断通道是阻塞还是非阻塞，以及获取阻塞锁：

*   `SelectableChannel configureBlocking(boolean block)`：指定调用该方法的可选择通道的阻塞状态。传入 `true` 使通道阻塞，传入 `false` 使通道非阻塞。该方法返回该可选择通道，或在发生异常时抛出异常：当通道关闭时抛出 `ClosedChannelException`，当 `block` 为 `true` 且通道已注册到一个或多个选择器时抛出 `java.net.channels.IllegalBlockingModeException`，当发生 I/O 错误时抛出 `IOException`。
*   `boolean isBlocking()`：当调用该方法的可选择通道处于阻塞状态时，此方法返回 `true`；否则返回 `false`。新创建的通道默认为阻塞状态。
*   `Object blockingLock()`：返回 `configureBlocking()` 方法同步时使用的对象。该返回对象在实现适配器时很有用，这些适配器要求在短时间内当前阻塞模式值不发生变化。

设置或重置可选择通道的阻塞/非阻塞状态非常简单。要启用非阻塞，向 `configureBlocking()` 的调用传入 `false`，如下例所示：

`ServerSocketChannel ssc = ServerSocketChannel.open();`

`ssc.configureBlocking(false); // 启用非阻塞模式`

尽管非阻塞套接字通常用于面向服务器的应用程序，但它们在客户端也很有益。例如，基于 GUI 的应用程序可以利用非阻塞套接字，在与多个服务器应用程序同时通信时保持用户界面的响应性。

`blockingLock()` 方法允许你阻止其他线程更改套接字通道的阻塞/非阻塞状态。此方法返回通道实现用于在更改此状态时进行同步的对象。只有持有此对象锁的线程才能更改状态，并且通常通过使用 Java 的 `synchronized` 关键字来获取该锁。请考虑以下示例：

`ServerSocketChannel ssc = ServerSocketChannel.open();`

`SocketChannel sc = null;`

`Object lock = ssc.blockingLock();`

`// 线程在获取与 lock 对象关联的锁时可能会阻塞。`

`synchronized(lock)`

`{`

`// 当前线程拥有该锁。没有其他线程可以更改阻塞模式。`

`// 获取服务器套接字通道的当前阻塞模式。`

`boolean blocking = ssc.isBlocking();`

`// 将服务器套接字通道设置为非阻塞。`

`ssc.configureBlocking(false);`

`// 获取下一个连接，如果没有连接则为 null。`

`sc = ssc.accept();`

`// 恢复先前的阻塞模式。`

`ssc.configureBlocking(blocking);`

`}`

`// 锁被释放，其他线程可能会修改服务器套接字通道的阻塞模式。`

`if (sc != null)`

`communicateWithSocket(sc);`



#### 探索服务器套接字通道

`ServerSocketChannel` 是三种套接字通道类中最简单的一种。该类包含以下方法：

*   `static ServerSocketChannel open()`：尝试打开一个服务器套接字通道，该通道初始时未绑定；必须通过其对等套接字的 `bind()` 方法之一将其绑定到特定地址，然后才能接受连接。如果无法打开通道，则抛出 `IOException`。
*   `ServerSocket socket()`：返回与此服务器套接字通道关联的对等 `ServerSocket` 实例。
*   `SocketChannel accept()`：接受发往此通道套接字的连接。如果此通道是非阻塞的，则当没有待处理的连接时，`accept()` 会立即返回 `null`，或者返回一个表示连接的套接字通道。否则，当通道是阻塞的时，`accept()` 会无限期阻塞，直到有新连接可用或发生 I/O 错误。无论服务器套接字通道是阻塞还是非阻塞，`accept()` 返回的套接字通道都是阻塞的。当服务器套接字通道已关闭时，此方法抛出 `ClosedChannelException`；当另一个线程在 accept 操作进行中关闭此服务器套接字通道时，抛出 `AsynchronousCloseException`；当服务器套接字通道尚未绑定时，抛出 `java.nio.channels.NotYetBoundException`；当发生 I/O 错误时，抛出 `IOException`。

服务器套接字通道在 TCP/IP 流协议中充当服务器角色。您使用服务器套接字通道来监听来自客户端的传入连接。

您通过调用静态的 `open()` 工厂方法来创建一个新的服务器套接字通道。如果一切顺利，`open()` 会返回一个 `ServerSocketChannel` 实例，该实例与一个未绑定的对等 `ServerSocket` 对象关联。您可以通过调用 `socket()` 获取此对象，然后调用 `ServerSocket` 的 `bind()` 方法将服务器套接字（以及最终的服务器套接字通道）绑定到特定地址。

然后，您可以调用 `ServerSocketChannel` 的 `accept()` 方法来接受传入连接。根据您是否将服务器套接字通道配置为非阻塞，此方法要么立即返回 `null` 或一个指向传入连接的套接字通道，要么阻塞直到有传入连接。

注意

或者，您可以在 `socket()` 返回的对等 `ServerSocket` 对象上调用 `accept()`。但是，此 `accept()` 方法将始终阻塞。

清单 7-7 展示了一个演示 `ServerSocketChannel` 的 `ChannelServer` 应用程序。

清单 7-7. 演示 `ServerSocketChannel`

`import java.io.IOException;`

`import java.net.InetSocketAddress;`

`import java.nio.ByteBuffer;`

`import java.nio.channels.ServerSocketChannel;`

`import java.nio.channels.SocketChannel;`

`public class ChannelServer`

`{`

`public static void main(String[] args) throws IOException`

`{`

`System.out.println("Starting server...");`

`ServerSocketChannel ssc = ServerSocketChannel.open();`

`ssc.socket().bind(new InetSocketAddress(9999));`

`ssc.configureBlocking(false);`

`String msg = "Local address: " + ssc.socket().getLocalSocketAddress();`

`ByteBuffer buffer = ByteBuffer.wrap(msg.getBytes());`

`while (true)`

`{`

`System.out.print(".");`

`SocketChannel sc = ssc.accept();`

`if (sc != null)`

`{`

`System.out.println();`

`System.out.println("Received connection from " +`

`sc.socket().getRemoteSocketAddress());`

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

清单 7-7 的 `main()` 方法首先输出一条启动消息，然后获取一个服务器套接字通道。接着，它访问 `ServerSocket` 对等对象，并使用该对象将套接字/通道绑定到端口 9999。

接下来，`main()` 将服务器套接字通道配置为非阻塞，并根据一条标识服务器套接字通道本地套接字地址的消息创建一个字节缓冲区。

`main()` 现在进入一个 `while` 循环，该循环重复打印一个句点字符以演示通道的非阻塞状态，并检查是否有传入连接。如果检测到连接，则使用其 `SocketChannel` 实例获取远程套接字地址，并将其输出到标准输出流。然后缓冲区被重置，其内容被写入套接字通道，随后该通道被关闭。但是，如果未检测到连接，`main()` 会休眠一小段时间。

按如下方式编译清单 7-7：

`javac ChannelServer.java`

执行以下命令行以启动服务器：

`java ChannelServer`

您应该会看到一条 `starting server...` 消息，随后屏幕上从左到右出现一长串不断增长的句点。此时，没有其他可观察的内容了。



#### 探索 Socket 通道

`SocketChannel` 是三种套接字通道类中最常用的一种，它模拟了面向连接的流协议（如 TCP/IP）。该类包含以下方法：

*   `static SocketChannel open()`: 尝试打开一个套接字通道。如果无法打开通道，则抛出 `IOException`。
*   `static SocketChannel open(InetSocketAddress remoteAddr)`: 尝试打开一个套接字通道并将其连接到 `remoteAddr`。这个便捷方法的效果等同于先调用 `open()` 方法，然后在返回的套接字通道上调用 `connect()` 方法并传入 `remoteAddr`，最后返回该通道。此方法会抛出五种不同的异常：当连接操作进行中时，另一个线程关闭此通道，则抛出 `AsynchronousCloseException`；当连接操作进行中时，另一个线程中断了当前线程，从而关闭通道并设置当前线程的中断状态，则抛出 `ClosedByInterruptException`；当给定的远程地址未完全解析时，抛出 `java.nio.channels.UnresolvedAddressException`；当不支持给定远程地址的类型时，抛出 `java.nio.channels.UnsupportedAddressTypeException`；当发生其他 I/O 错误时，抛出 `IOException`。
*   `Socket socket()`: 返回与此套接字通道关联的对等 `Socket` 实例。
*   `boolean connect(SocketAddress remoteAddr)`: 尝试将此套接字通道的套接字对象连接到远程地址。如果此通道是非阻塞的，则调用此方法会启动一个非阻塞连接操作。如果连接立即建立（例如本地连接可能发生的情况），则此方法返回 `true`。否则，此方法返回 `false`，并且必须随后通过重复调用 `finishConnect()` 方法直到该方法返回 `true` 来完成连接操作。此方法抛出以下异常：当此通道已连接时，抛出 `java.nio.channels.AlreadyConnectedException`；当此通道上已有非阻塞连接操作正在进行时，抛出 `java.nio.channels.ConnectionPendingException`；当此通道已关闭时，抛出 `ClosedChannelException`；当连接操作进行中时，另一个线程关闭此通道，则抛出 `AsynchronousCloseException`；当连接操作进行中时，另一个线程中断了当前线程，从而关闭通道并设置当前线程的中断状态，则抛出 `ClosedByInterruptException`；当给定的远程地址未完全解析时，抛出 `UnresolvedAddressException`；当不支持给定远程地址的类型时，抛出 `UnsupportedAddressTypeException`；当发生其他 I/O 错误时，抛出 `IOException`。
*   `boolean isConnectionPending()`: 当连接操作正在等待完成时返回 `true`；否则返回 `false`。
*   `boolean finishConnect()`: 完成连接套接字通道的过程。当套接字通道完全连接时，此方法返回 `true`；否则返回 `false`。此方法抛出以下异常：当此通道未连接且未启动连接操作时，抛出 `java.nio.channels.NoConnectionPendingException`；当此通道已关闭时，抛出 `ClosedChannelException`；当连接操作进行中时，另一个线程关闭此通道，则抛出 `AsynchronousCloseException`；当连接操作进行中时，另一个线程中断了当前线程，从而关闭通道并设置当前线程的中断状态，则抛出 `ClosedByInterruptException`；当发生其他 I/O 错误时，抛出 `IOException`。
*   `boolean isConnected()`: 当此通道的套接字已打开并连接时返回 `true`；否则返回 `false`。

套接字通道在 TCP/IP 流协议中充当客户端角色。你可以使用套接字通道来发起与监听服务器的连接。

通过调用 `open()` 方法之一来创建新的套接字通道。在后台，会创建一个对等 `Socket` 对象。调用 `SocketChannel` 的 `socket()` 方法可以返回这个对等对象。此外，你也可以通过对等 `Socket` 对象调用 `getChannel()` 来返回原始的套接字通道。

从无参 `open()` 方法获得的套接字通道是未连接的。尝试读取或写入此套接字通道会导致 `java.nio.channels.NotYetConnectedException`。要连接套接字，请在套接字通道或其对应的对等套接字上调用 `connect()` 方法。

套接字通道连接后，将保持连接状态直到关闭。要判断套接字通道是否已连接，请调用 `SocketChannel` 的 `boolean isConnected()` 方法。

接受 `java.net.InetSocketAddress` 参数的 `open()` 方法还允许你连接到指定远程地址的另一台主机，如下所示：

`SocketChannel sc = SocketChannel.open(new InetSocketAddress("localhost", 9999));`

这个便捷方法等同于调用以下代码序列：

`SocketChannel sc = SocketChannel.open();`

`sc.connect(new InetSocketAddress("localhost", 9999));`

当通过对等 `Socket` 对象或通过 `SocketChannel` 的 `connect()`/第二个 `open()` 方法在阻塞套接字通道上连接到服务器时，调用 `connect()` 的线程会阻塞，直到套接字通道连接成功。但是，当套接字通道是非阻塞时，`connect()` 会立即返回，通常返回 `false` 表示连接尚未建立（尽管对于本地回环连接，它可能返回 `true`）。由于必须在套接字通道上执行 I/O 之前建立连接，因此你需要重复调用 `finishConnect()`，直到此方法返回 `true`。

清单 7-8 展示了一个演示 `SocketChannel` 的 `ChannelClient` 应用程序。

清单 7-8. 演示 `SocketChannel`

`import java.io.IOException;`

`import java.net.InetSocketAddress;`

`import java.nio.ByteBuffer;`

`import java.nio.channels.SocketChannel;`

`public class ChannelClient`

`{`

`public static void main(String[] args)`

`{`

`try`

`{`

`SocketChannel sc = SocketChannel.open();`

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

清单 7-8 的 `main()` 方法首先获取一个套接字通道并将其配置为非阻塞。然后，它创建一个指向之前通道服务器应用程序的地址，并启动到该地址的连接。由于非阻塞状态，有必要重复调用 `finishConnect()`，直到此方法返回 `true`，这表示已连接到远程服务器应用程序。

随后，`main()` 创建一个字节缓冲区并进入一个循环，该循环反复将内容读入此缓冲区，并将此内容输出到标准输出流。然后关闭通道。

按如下方式编译清单 7-8：

`javac ChannelClient.java`

假设通道服务器正在运行，执行以下命令行以启动客户端：

`java ChannelClient`

你应该会在通道服务器输出流中观察到类似以下的消息：

`Received connection from /127.0.0.1:51177`

你还应该在通道客户端输出流中观察到以下消息：

`Local address: /0:0:0:0:0:0:0:0:9999`



#### 探索数据报通道

`DatagramChannel` 模拟了一种无连接的、基于数据包的协议（例如 UDP/IP）。该类包含以下方法：

*   `static DatagramChannel open()`：尝试打开一个数据报通道。如果无法打开该通道，则抛出 `IOException`。
*   `DatagramSocket socket()`：返回与此数据报通道关联的对等 `DatagramSocket` 实例。
*   `DatagramChannel connect(SocketAddress remoteAddr)`：尝试将此数据报通道的套接字对象连接到远程地址。该通道的套接字被配置为仅接收来自该地址的数据报，并仅向该地址发送数据报。一旦连接，就无法从任何其他地址接收数据报，也无法向任何其他地址发送数据报。数据报套接字保持连接状态，直到显式断开连接或关闭。此方法在成功时返回该数据报通道。当数据报通道已关闭时抛出 `ClosedChannelException`；当连接操作进行中时，另一个线程关闭此通道，则抛出 `AsynchronousCloseException`；当连接操作进行中时，另一个线程中断当前线程，从而关闭通道并设置当前线程的中断状态，则抛出 `ClosedByInterruptException`；当发生其他 I/O 错误时抛出 `IOException`。
*   `boolean isConnected()`：当此通道的套接字已打开并连接时返回 `true`；否则返回 `false`。
*   `DatagramChannel disconnect()`：断开此通道的套接字连接。此方法可随时调用，并且对已在进行中的读取或写入操作没有影响。当套接字未连接或通道已关闭时，调用此方法无效。
*   `SocketAddress receive(ByteBuffer buffer)`：通过此通道接收一个数据报。如果数据报立即可用，或者此通道处于阻塞模式且数据报变为可用，则将该数据报复制到给定的字节缓冲区中，并返回其源地址。如果此通道处于非阻塞模式且数据报不可立即使用，则此方法立即返回 `null`。数据报从当前缓冲区位置开始传输到给定的字节缓冲区中，就像常规的读取操作一样。如果缓冲区中剩余的字节数少于容纳该数据报所需的字节数，则数据报的剩余部分将被静默丢弃。此方法返回数据报的源地址，如果通道处于非阻塞模式且没有可用数据报，则返回 `null`。当数据报通道已关闭时抛出 `ClosedChannelException`；当读取操作进行中时，另一个线程关闭此通道，则抛出 `AsynchronousCloseException`；当读取操作进行中时，另一个线程中断当前线程，从而关闭通道并设置当前线程的中断状态，则抛出 `ClosedByInterruptException`；当发生其他 I/O 错误时抛出 `IOException`。
*   `int send(ByteBuffer buffer, SocketAddress destAddr)`：通过此通道发送一个数据报。如果此通道处于非阻塞模式且底层输出缓冲区中有足够的空间，或者此通道处于阻塞模式且空间变得足够，则给定缓冲区中的剩余字节将作为单个数据报传输到给定的目标地址。数据报从字节缓冲区中传输，就像常规的写入操作一样。此方法返回发送的字节数，该字节数等于调用此方法时源缓冲区中剩余的字节数；或者，当此通道处于非阻塞模式时，如果底层输出缓冲区中没有足够的空间容纳该数据报，则可能为零。当数据报通道已关闭时抛出 `ClosedChannelException`；当写入操作进行中时，另一个线程关闭此通道，则抛出 `AsynchronousCloseException`；当写入操作进行中时，另一个线程中断当前线程，从而关闭通道并设置当前线程的中断状态，则抛出 `ClosedByInterruptException`；当发生其他 I/O 错误时抛出 `IOException`。



此外，你可能还会用到几个 `read()` 和 `write()` 方法。与不需要数据报通道建立连接的 `send()` 和 `receive()` 不同，`read()` 和 `write()` 方法要求必须先建立连接。

与 `ServerSocketChannel` 和 `SocketChannel` 类似，你可以通过调用静态的 `open()` 方法来获取一个 `DatagramChannel` 实例。新建的数据报通道会关联一个对等的 `DatagramSocket` 对象，你可以通过调用 `DatagramChannel` 的 `socket()` 方法来获取该对象。

数据报通道既可以充当客户端（发送方），也可以充当服务器（监听方）。要充当监听方，数据报通道必须绑定到一个端口和一个可选的地址。完成此任务的方法是先获取 `DatagramSocket` 对象，然后在该对象上调用 `bind()`，如下所示：

`DatagramChannel dc = DatagramChannel.open();`

`DatagramSocket ds = dc.socket();`

`ds.bind(new InetSocketAddress(9999)); // 绑定到端口 9999`

`receive()` 方法会将传入数据报的数据负载复制到字节缓冲区参数中，并返回一个套接字地址，用于标识该数据报的源地址。如果通道是阻塞模式，`receive()` 会一直休眠，直到数据包到达或发生某些事件导致抛出异常。如果通道是非阻塞模式，当没有可用的数据报时，`receive()` 会返回 `null`。如果数据负载大于缓冲区容量，多余的字节会被静默丢弃。

`send()` 方法会将给定字节缓冲区的内容（从当前位置开始，到缓冲区的限制位置为止）发送到套接字地址参数指定的目标地址/端口号。如果数据报通道是阻塞模式，`send()` 会一直休眠，直到数据报被排入发送队列或发生某些事件导致抛出异常。如果通道是非阻塞模式，该方法会返回两个值之一：已发送的缓冲区内容的整个长度，或者 `0`，表示缓冲区内容未被发送（当没有足够空间在传输前存储整个数据报时，则不会发送任何内容）。

注意

数据报协议并不可靠。首先，它们不保证送达。因此，`send()` 返回非零值并不意味着数据报已到达目的地。此外，底层网络可能会将数据报分片成多个更小的数据包。当数据报被分片时，其中一个或多个数据包更有可能无法到达目的地。由于接收方无法重新组装所有数据包，整个数据报将被丢弃。因此，数据负载应限制在几百字节以内。

一个可能需要使用数据报通道的例子是股票行情机，它提供特定公司的最新股票价格。客户端会将公司的股票代码（例如微软的 MSFT）作为数据报负载提交，并接收一个数据报作为响应，其负载提供所请求的股票价格。由于需要的是最新信息，当响应数据报未到达时，客户端会重新请求股票价格。

清单 7-9 展示了一个 `ChannelServer` 应用程序，它利用 `DatagramChannel` 实现了股票行情机的服务器部分。

清单 7-9\. 使用 `DatagramChannel` 实现股票行情服务器

`import java.io.IOException;`

`import java.net.InetSocketAddress;`

`import java.net.SocketAddress;`

`import java.nio.ByteBuffer;`

`import java.nio.channels.DatagramChannel;`

`public class ChannelServer`

`{`

`final static int PORT = 9999;`

`public static void main(String[] args) throws IOException`

`{`

`System.out.println("服务器正在启动，并在端口 " +`

`PORT + " 上监听传入请求...");`

`DatagramChannel dcServer = DatagramChannel.open();`

`dcServer.socket().bind(new InetSocketAddress(PORT));`

`ByteBuffer symbol = ByteBuffer.allocate(4);`

`ByteBuffer payload = ByteBuffer.allocate(16);`

`while (true)`

`{`

`payload.clear();`

`symbol.clear();`

`SocketAddress sa = dcServer.receive(symbol);`



`if (sa == null)`

`return;`

`System.out.println("Received request from " + sa);`

`String stockSymbol = new String(symbol.array(), 0, 4);`

`System.out.println("Symbol: " + stockSymbol);`

`if (stockSymbol.toUpperCase().equals("MSFT"))`

`{`

`payload.putFloat(0, 37.40f); // 开盘股价`

`payload.putFloat(4, 37.22f); // 最低股价`

`payload.putFloat(8, 37.48f); // 最高股价`

`payload.putFloat(12, 37.41f); // 收盘股价`

`}`

`else`

`{`

`payload.putFloat(0, 0.0f);`

`payload.putFloat(4, 0.0f);`

`payload.putFloat(8, 0.0f);`

`payload.putFloat(12, 0.0f);`

`}`

`dcServer.send(payload, sa);`

`}`

`}`

`}`

清单 7-9 的 `main()` 方法首先创建一个数据报通道并将其绑定到端口 9999。然后创建两个字节缓冲区，分别用于存放四个字节的股票代码和十六个字节的响应，响应被组织成四个四字节浮点值，分别代表开盘股价、最低股价、最高股价和收盘股价。

注意

为方便起见，我将货币金额表示为浮点值。这在实践中并非好主意，应改用 `java.math.BigDecimal`。此外，不应将股票价格嵌入源代码，而应从某种外部服务器或数据库动态获取。

`main()` 现在进入一个无限循环，该循环清空两个字节缓冲区以准备接收来自客户端的新信息，并接收下一个股票代码。后续用于测试 `sa` 是否为 `null` 的 `if` 语句对于此应用程序并非必需，但在此处保留，以便您将来将通道配置为非阻塞模式。

在输出标识请求的消息后，`main()` 检查股票代码是否等于 `MSFT`。如果是，则将 `payload` 字节缓冲区配置为存储微软股票的四项股票价格；否则，将 `payload` 字节缓冲区配置为存储四个 0 价格（表示未知股票代码）。

最后，`main()` 将 `payload` 数据报负载发送给接收方，并继续循环。

按如下方式编译清单 7-9：

`javac ChannelServer.java`

按如下方式运行生成的应用程序：

`java ChannelServer`

您应该会看到以下消息：

`server starting and listening on port 9999 for incoming requests...`

清单 7-10 展示了一个 `ChannelClient` 应用程序，它利用 `DatagramChannel` 实现股票行情客户端部分。

清单 7-10. 使用 `DatagramChannel` 实现股票行情客户端

`import java.io.IOException;`

`import java.net.InetSocketAddress;`

`import java.nio.ByteBuffer;`

`import java.nio.channels.DatagramChannel;`

`public class ChannelClient`

`{`

`final static int PORT = 9999;`

`public static void main(String[] args) throws IOException`

`{`

`if (args.length != 1)`

`{`

`System.err.println("usage: java ChannelClient stocksymbol");`

`return;`

`}`

`DatagramChannel dcClient = DatagramChannel.open();`

`ByteBuffer symbol = ByteBuffer.wrap(args[0].getBytes());`

`ByteBuffer response = ByteBuffer.allocate(16);`

`InetSocketAddress sa = new InetSocketAddress("localhost", PORT);`

`dcClient.send(symbol, sa);`

`System.out.println("Receiving datagram from " +`

`dcClient.receive(response));`

`System.out.println("Open price: " + response.getFloat(0));`

`System.out.println("Low price: " + response.getFloat(4));`

`System.out.println("High price: " + response.getFloat(8));`

`System.out.println("Close price: " + response.getFloat(12));`

`}`

`}`

清单 7-10 的 `main()` 方法首先验证是否指定了单个命令行参数。该参数标识一个股票代码。然后创建一个数据报通道和一对字节缓冲区：`symbol` 存储指定的股票代码，`response` 存储来自服务器的响应。

接下来，`main()` 创建一个用于通信的套接字地址，并将 `symbol` 缓冲区发送给服务器。然后从服务器接收一个响应数据报，将其负载存储在 `response` 缓冲区中。

最后，`main()` 访问响应缓冲区，使用 `getFloat()` 将每组四个字节转换为浮点值，随后输出这些值。

按如下方式编译清单 7-10：

`javac ChannelClient.java`

按如下方式运行生成的应用程序：

`java ChannelClient msft`

假设服务器仍在运行，您应该在服务器窗口中看到以下消息（端口号可能不同）：

`Received request from /127.0.0.1:64837`

`Symbol: msft`

此外，您应该在客户端窗口中看到以下输出：

`Receiving datagram from /127.0.0.1:9999`

`Open price: 37.4`

`Low price: 37.22`

`High price: 37.48`

`Close price: 37.41`



### 管道

`java.nio.channels` 包中包含一个 `Pipe` 类。`Pipe` 描述了一对实现单向管道的通道，该管道用于在两个实体（例如两个文件通道或两个套接字通道）之间单向传输数据。`Pipe` 类似于 `java.io.PipedInputStream` 和 `java.io.PipedOutputStream` 类——参见第 4 章。

`Pipe` 声明了嵌套的 `SourceChannel` 和 `SinkChannel` 类，它们分别用作可读和可写的字节通道。`Pipe` 还声明了以下方法：

*   `static` `Pipe open()`：此静态方法打开一个新的管道，当发生 I/O 错误时抛出 `IOException`。
*   `SourceChannel source()`：此方法返回管道的源通道（数据来源）。
*   `SinkChannel sink()`：此方法返回管道的汇通道（数据发送目标）。

管道可用于在同一 JVM 内传递数据；不能使用它们在 JVM 和外部程序之间传递数据。由于封装性，管道在生产/消费者场景中非常理想：根据提供给管道的通道类型，你可以使用相同的代码将数据写入文件、套接字或管道。

清单 7-11 展示了一个使用管道实现两个线程间通信的生产者/消费者应用程序。

**清单 7-11.** 通过管道生产和消费字节

`import java.io.IOException;`

`import java.nio.ByteBuffer;`

`import java.nio.channels.Pipe;`

`import java.nio.channels.ReadableByteChannel;`

`import java.nio.channels.WritableByteChannel;`

`public class ChannelDemo`

`{`

`final static int BUFSIZE = 10;`

`final static int LIMIT = 3;`

`public static void main(String[] args) throws IOException`

`{`

`final Pipe pipe = Pipe.open();`

`Runnable senderTask =`

`new Runnable()`

`{`

`@Override`

`public void run()`

`{`

`WritableByteChannel src = pipe.sink();`

`ByteBuffer buffer = ByteBuffer.allocate(BUFSIZE);`

`for (int i = 0; i < LIMIT; i++)`

`{`

`buffer.clear();`

`for (int j = 0; j < BUFSIZE; j++)`

`buffer.put((byte) (Math.random() * 256));`

`buffer.flip();`

`try`

`{`

`while (src.write(buffer) > 0);`

`}`

`catch (IOException ioe)`

`{`

`System.err.println(ioe.getMessage());`

`}`

`}`

`try`

`{`

`src.close();`

`}`

`catch (IOException ioe)`

`{`

`ioe.printStackTrace();`

`}`

`}`

`};`

`Runnable receiverTask =`

`new Runnable()`

`{`

`@Override`

`public void run()`

`{`

`ReadableByteChannel dst = pipe.source();`

`ByteBuffer buffer = ByteBuffer.allocate(BUFSIZE);`

`try`

`{`

`while (dst.read(buffer) >= 0)`

`{`

`buffer.flip();`

`while (buffer.remaining() > 0)`

`System.out.println(buffer.get() & 255);`

`buffer.clear();`

`}`

`}`

`catch (IOException ioe)`

`{`

`System.err.println(ioe.getMessage());`

`}`

`}`

`};`

`Thread sender = new Thread(senderTask);`

`Thread receiver = new Thread(receiverTask);`

`sender.start();`

`receiver.start();`

`}`

`}`

清单 7-11 的 `main()` 方法首先获取一个管道，然后创建作为生产者和消费者的发送者任务和接收者任务。`main()` 接着创建发送者线程和接收者线程并启动它们。

发送者任务的 `run()` 方法首先通过调用 `Pipe` 的 `sink()` 方法从管道获取一个可写字节通道。然后它分配一个字节缓冲区用于存储要写入的内容。

`run()` 继续进入一对 `for` 循环，用于将面向字节的数据发送到可写字节通道。每个外层 `for` 循环迭代都会清除缓冲区，为内层 `for` 循环的填充做准备。然后翻转缓冲区，为排空做准备，这是通过将缓冲区传递给可写字节通道的 `write()` 方法来完成的。由于单次方法调用可能无法排空整个缓冲区，因此会在循环中调用 `write()`，直到它返回 `0`，这意味着没有更多内容可写。然后关闭通道，这样接收者任务在从通道读取时就不会阻塞，因为它期望接收更多数据。

接收者任务的 `run()` 方法首先通过调用 `Pipe` 的 `source()` 方法从管道获取一个可读字节通道。然后它分配一个缓冲区用于存储读取的内容。

接着，`run()` 进入一个 `while` 循环，持续从通道读取，直到 `read()` 方法返回 `-1`，这表示通道已到达流的末尾。如果发送者的 `run()` 方法没有关闭通道，此方法不会到达流的末尾。

此时，翻转缓冲区以准备排空。然后通过将其字节值打印到标准输出流来排空缓冲区。每个字节都与 255 进行按位与运算，以防止输出负值。基本上，`get()` 返回一个 8 位整数值，该值在 `System.out.println()` 方法调用期间被转换为 32 位整数。此转换应用了符号扩展，这意味着某些字节值会变成负的 32 位整数。通过将字节值与 255 进行按位与运算，该转换确保没有字节值被转换为负的 32 位整数。

最后，清除缓冲区以准备填充，循环继续。

编译清单 7-11（`javac ChannelDemo.java`）并运行应用程序（`java ChannelDemo`）。你应该会看到一系列 30 个随机整数，类似于下面所示：

`245`

`56`

`137`

`166`

`52`

`183`

`252`

`166`

`246`

`124`

`163`

`11`

`159`

`68`

`203`

`118`

`157`

`70`

`54`

`148`

`186`

`17`

`12`

`203`

`75`

`223`

`224`

`175`

`205`

`47`

**练习题**

以下练习题旨在测试你对第 7 章内容的理解：

什么是通道？  `Channel` 接口提供了哪些能力？  指出直接扩展 `Channel` 的三个接口。  判断正误：实现 `InterruptibleChannel` 的通道是异步可关闭的。  指出获取通道的两种方式。  定义分散/聚集 I/O。  提供了哪些接口来实现分散/聚集 I/O？  定义文件通道。  判断正误：文件通道不支持分散/聚集 I/O。  定义排他锁和共享锁。  `FileChannel` 的 `lock()` 和 `tryLock()` 方法之间的根本区别是什么？  当已经持有与此锁请求重叠的锁，或者另一个线程正在等待获取将与此请求重叠的锁时，`FileLock lock()` 方法会做什么？  指定你应该采用的模式，以确保始终释放已获取的文件锁。  `FileChannel` 提供了什么方法用于将文件区域映射到内存？  指出三种文件映射模式。  哪种文件映射模式对应于写时复制？  指出优化批量传输常见实践的 `FileChannel` 方法。  判断正误：套接字通道是可选择的，并且可以在非阻塞模式下运行。  指出描述套接字通道的三个类。  判断正误：数据报通道不是线程安全的。  为什么套接字通道支持非阻塞模式？  如何获取套接字通道的关联套接字？  如何获取服务器套接字通道？  创建一个 `Copy` 应用程序，该应用程序使用 `ByteBuffer` 和 `FileChannel` 类与 `FileInputStream` 和 `FileOutputStream` 配合，将源文件复制到目标文件。



## 摘要

通道与缓冲区协同工作，以实现高性能 I/O。通道是一个对象，代表与硬件设备、文件、网络套接字、应用程序组件或其他能够执行写入、读取及其他 I/O 操作的实体之间的开放连接。通道能在字节缓冲区与基于操作系统的 I/O 服务源或目标之间高效传输数据。

Java 通过提供 `Channel` 接口、其子接口 `WritableByteChannel` 和 `ReadableByteChannel`、`Channels` 类以及 `java.nio.channels` 包中的其他类型来支持通道。在探索此包的过程中，你学习了分散/聚集 I/O、文件通道（重点介绍 `FileChannel` 类及其文件锁定、内存映射文件 I/O 和字节传输能力）、套接字通道和管道。

第 8 章将介绍 NIO 对选择器的支持。

