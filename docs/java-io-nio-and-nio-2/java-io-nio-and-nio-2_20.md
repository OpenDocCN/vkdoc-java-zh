# 练习题答案

电子补充材料 本章的在线版本 (doi:[10.​1007/​978-1-4842-1565-4_​15](http://dx.doi.org/10.1007/978-1-4842-1565-4_15)) 包含补充材料，仅供授权用户使用。

从第 1 章到第 14 章的每一章末尾都有一个“练习题”部分，用于测试你对本章内容的理解。这些练习题的答案将在本附录中给出。

## 第 1 章：I/O 基础与 API

构成经典 I/O 的 API 类别包括 `File`、`RandomAccessFile`、流类以及写入器/读取器类。  
`try`-with-resources 语句的好处是减少了冗长代码。你可以使用此语句自动关闭打开的文件和其他资源，而无需提供这些资源关闭代码。  
构成 NIO 的 API 类别包括缓冲区、通道、选择器、正则表达式、字符集以及一个主要基于 `Formatter` 类的 `printf` 风格格式化工具。  
`Selector` 类（及相关类型）让 Java 程序能够利用就绪选择功能。  
构成 NIO.2 的 API 类别包括改进的文件系统接口、异步 I/O 以及套接字通道功能的完善。  
NIO.2 通过向 `DatagramChannel`、`ServerSocketChannel` 和 `SocketChannel` 类添加绑定支持和选项配置，完善了套接字通道功能。它还引入了一个 `MulticastChannel` 接口。

## 第 2 章：File

`File` 类的目的是提供对底层操作系统可用文件系统的访问。  
`File` 类的实例包含其文件系统中可能不存在的文件和目录的路径。  
路径是一个紧凑的映射，用于定位和标识文件系统对象（也称为文件）。  
绝对路径和相对路径的区别如下：绝对路径以根目录符号开头；无需其他信息即可定位其所表示的文件/目录。相比之下，相对路径不以根目录符号开头；它通过从另一个路径获取的信息进行解释。  
你可以通过指定 `System.getProperty("user.dir")` 来获取当前用户目录（也称为工作目录）。  
父路径由除最后一个名称之外的所有路径组件组成。  
规范化意味着将分隔符字符替换为默认的名称分隔符字符，以使路径符合底层文件系统。  
你可以通过访问 `File` 的 `separator` 和 `separatorChar` 类字段来获取默认的名称分隔符字符。第一个字段将字符存储为 `char`，第二个字段将其存储为 `String`。  
规范路径是绝对且唯一的路径，并且每次都以相同的方式格式化。  
`File` 的 `getParent()` 和 `getName()` 方法的区别在于：`getParent()` 返回父路径，而 `getName()` 返回路径名称序列中的最后一个名称。  
答案是 false：`File` 的 `exists()` 方法用于确定文件或目录是否存在。  
普通文件（也称为常规文件）不是目录，并且满足其他依赖于操作系统的标准：例如，它不是符号链接或命名管道。由 Java 应用程序创建的任何非目录文件都保证是普通文件。  
`File` 的 `lastModified()` 方法返回此 `File` 对象路径所表示的文件最后修改的时间，如果文件不存在或在此方法调用期间发生 I/O 错误，则返回 0。返回值以自 Unix 纪元（1970 年 1 月 1 日 00:00:00 GMT）以来的毫秒数为单位。  
`File` 的 `listRoots()` 方法返回一个 `File` 对象数组，这些对象表示可用文件系统的根目录。  
答案是 true：`File` 的 `list()` 方法返回一个 `String` 数组，其中每个条目都是文件名，而不是完整路径。  
`FilenameFilter` 和 `FileFilter` 接口的区别如下：`FilenameFilter` 声明了一个 `boolean accept(File dir, String name)` 方法，而 `FileFilter` 声明了一个 `boolean accept(String path)` 方法。两种方法都完成相同的任务，即接受（返回 `true`）或拒绝（返回 `false`）将参数标识的文件或目录包含在目录列表中。  
答案是 false：`File` 的 `createNewFile()` 方法在一个原子操作中检查文件是否存在，并在文件不存在时创建它，该操作相对于所有可能影响该文件的其他文件系统活动是原子的。  
`File` 的 `createTempFile(String, String)` 方法创建临时文件的默认临时目录可以通过读取 `java.io.tmpdir` 系统属性来定位。  
你可以通过调用 `File` 的 `deleteOnExit()` 方法注册临时文件以在 Java 虚拟机正常结束时（不会崩溃且不会断电）删除，从而确保临时文件被删除。  
`boolean canExecute()` 方法由 Java 6 引入。当抽象路径标识的文件或目录对象存在且可执行时，它返回 `true`。  
要准确比较两个 `File` 对象，应首先在每个 `File` 对象上调用 `File` 的 `getCanonicalFile()` 方法，然后比较返回的 `File` 对象。  
清单 A-1 展示了第 2 章中要求的 `Touch` 应用程序。  
清单 A-1\. 将文件或目录的时间戳设置为当前时间

`import java.io.File;`

`import java.util.Date;`

`public class Touch`

`{`

`public static void main(String[] args)`

`{`

`if (args.length != 1)`

`{`

`System.err.println("usage: java Touch path");`

`return;`

`}`

`new File(args[0]).setLastModified(new Date().getTime());`

`}`

`}`



## 第 3 章: RandomAccessFile

`RandomAccessFile` 类的用途是创建和/或打开文件以进行随机访问，在这种访问模式下，可以在文件关闭前，在不同位置混合执行写入和读取操作。  
文件的元数据是关于文件的数据，而非文件的实际内容。元数据的示例包括文件长度和文件最后修改时间。  
`"rwd"` 和 `"rws"` 模式参数的用途是确保对本地存储设备上文件的任何写入操作都会实际写入设备，从而保证在系统崩溃时关键数据不会丢失。当文件不在本地设备上时，则不提供此保证。  
文件指针是一个光标，用于标识下一个要写入或读取的字节的位置。当打开一个现有文件时，文件指针会设置在其第一个字节处，偏移量为 0。当创建文件时，文件指针也会设置为 0。  
当写入位置超过文件末尾时，文件会被扩展。  
答案是错误的：当你调用 `RandomAccessFile` 的 `seek(long)` 方法来设置文件指针的值，并且该值大于文件长度时，文件的长度不会改变。只有在将偏移量设置到文件末尾之后进行写入操作时，文件长度才会改变。  
方法 `void write(int b)` 将 `b` 的低八位写入当前文件指针位置处的文件。  
`FileDescriptor` 的 `sync()` 方法会通知底层操作系统将打开文件的输出缓冲区内容清空到其关联的本地磁盘设备。`sync()` 会在所有修改后的数据和属性都已写入相关设备后返回。  
平面文件数据库是一个按记录和字段组织的单一文件。一条记录存储一个条目（例如零件数据库中的一个零件），一个字段存储该条目的一个单一属性（例如零件编号）。  
清单 A-2 展示了 第 3 章 中要求的 `RAFDemo` 应用程序。  

清单 A-2. 演示 `RandomAccessFile`

`import java.io.IOException;`

`import java.io.RandomAccessFile;`

`public class RAFDemo`

`{`

   `final static String MSG = "Test";`

   `public static void main(String[] args)`

   `{`

      `try (RandomAccessFile raf = new RandomAccessFile("data", "rw"))`

      `{`

         `raf.writeInt(127);`

         `raf.writeChars(MSG);`

         `raf.seek(0);`

         `System.out.println(raf.readInt());`

         `for (int i = 0; i < MSG.length(); i++)`

            `System.out.print(raf.readChar());`

         `System.out.println();`

      `}`

      `catch (IOException ioe)`

      `{`

         `ioe.printStackTrace();`

      `}`

   `}`

`}`

## 第 4 章: 流



流是任意长度的有序字节序列。字节通过输出流从应用程序流向目的地，并通过输入流从源流向应用程序。  
`OutputStream` 的 `flush()` 方法旨在将所有缓冲的输出字节写入目的地。如果此输出流的目标是底层操作系统提供的抽象（例如文件），刷新流仅能保证先前写入流的字节被传递给底层操作系统进行写入；它不能保证这些字节实际写入物理设备（如磁盘驱动器）。  
答案为真：`OutputStream` 的 `close()` 方法会自动刷新输出流。如果在调用 `close()` 之前应用程序结束，输出流会自动关闭，其数据也会被刷新。  
`InputStream` 的 `mark(int)` 和 `reset()` 方法旨在重新读取流的一部分。`mark(int)` 标记此输入流中的当前位置。随后调用 `reset()` 会将此流重新定位到上次标记的位置，以便后续读取操作重新读取相同的字节。别忘了调用 `markSupported()` 来检查子类是否支持 `mark()` 和 `reset()`。  
你可以通过调用 `ByteArrayOutputStream` 的 `toByteArray()` 方法来访问 `ByteArrayOutputStream` 实例内部字节数组的副本。  
答案为假：`FileOutputStream` 和 `FileInputStream` 不提供内部缓冲区来提升读写操作的性能。  
你会使用 `PipedOutputStream` 和 `PipedInputStream` 在一对正在执行的线程之间通信数据。  
过滤流是一种在输入流的字节序列到达目的地之前对其进行缓冲、压缩/解压缩、加密/解密或其他操作的流。  
当一个流实例被传递给另一个流类的构造函数时，两个流就链接在一起。  
你可以通过将 `BufferedOutputStream` 实例链接到 `FileOutputStream` 实例，并调用 `BufferedOutputStream` 实例的 `write()` 方法，使数据在流向文件输出流之前被缓冲，从而提升文件输出流的性能。你可以通过将 `BufferedInputStream` 实例链接到 `FileInputStream` 实例，使来自文件输入流的数据在通过调用 `BufferedInputStream` 实例的 `read()` 方法返回之前被缓冲，从而提升文件输入流的性能。  
`DataOutputStream` 和 `DataInputStream` 通过提供以独立于操作系统的方式读写基本类型值和字符串的方法来支持 `FileOutputStream` 和 `FileInputStream`。相比之下，`FileOutputStream` 和 `FileInputStream` 仅提供读写字节和字节数组的方法。  
对象序列化是一种 JVM 机制，用于将对象状态序列化为字节流。其反序列化对应物是一种 JVM 机制，用于从字节流反序列化此状态。  
Java 支持的三种序列化和反序列化形式是：默认序列化和反序列化、自定义序列化和反序列化，以及外部化。  
`Serializable` 接口的目的是告诉 JVM 可以对实现该类的对象进行序列化。  
当序列化机制遇到其类未实现 `Serializable` 的对象时，它会抛出 `NotSerializableException` 类的实例。  
Java 不支持无限序列化的三个既定原因如下：安全性、性能以及对象不适合序列化。  
你通过创建 `ObjectOutputStream` 实例并调用其 `writeObject()` 方法来启动序列化。你通过创建 `ObjectInputStream` 实例并调用其 `readObject()` 方法来启动反序列化。  
答案为假：类字段不会自动序列化。  
`transient` 保留字的目的是标记不参与默认序列化和默认反序列化的实例字段。  
当反序列化机制尝试反序列化其类已更改的对象时，会导致 `readObject()` 抛出 `InvalidClassException` 类的实例。  
反序列化机制通过以下方式检测序列化对象的类是否已更改：每个序列化对象都有一个标识符。反序列化机制将正在反序列化的对象的标识符与其类的序列化标识符（所有可序列化的类都会自动获得唯一标识符，除非它们显式指定自己的标识符）进行比较，并在检测到不匹配时导致抛出 `InvalidClassException`。  
你可以向类中添加一个实例字段，并通过在类中引入 `long serialVersionUID = long integer value;` 声明来避免在反序列化在添加该实例字段之前序列化的对象时出现问题。`long integer value` 必须唯一，并被称为流唯一标识符 (SUID)。你可以使用 JDK 的 `serialver` 工具来协助完成此任务。  
你通过在该类中声明私有的 `void writeObject(ObjectOutputStream)` 和 `void readObject(ObjectInputStream)` 方法来自定义默认的序列化和反序列化机制，而无需使用外部化。  
你通过在 `writeObject(ObjectOutputStream)` 中首先调用 `ObjectOutputStream` 的 `defaultWriteObject()` 方法，或在 `readObject(ObjectInputStream)` 中首先调用 `ObjectInputStream` 的 `defaultReadObject()` 方法，来告知序列化和反序列化机制在序列化或反序列化额外数据项之前，先序列化或反序列化对象的正常状态。  
外部化与默认及自定义序列化和反序列化的不同之处在于，它提供了对序列化和反序列化任务的完全控制。  
一个类通过实现 `Externalizable` 接口（而非 `Serializable`），并声明 `void writeExternal(ObjectOutput)` 和 `void readExternal(ObjectInput in)` 方法（而非 `void writeObject(ObjectOutputStream)` 和 `void readObject(ObjectInputStream)` 方法）来表示它支持外部化。  
答案为真：在外部化过程中，当反序列化机制未检测到 `public` 无参构造函数时，它会抛出带有“no valid constructor”消息的 `InvalidClassException`。  
`PrintStream` 的 `print()` 和 `println()` 方法之间的区别在于，`print()` 方法不会在其输出后追加行终止符，而 `println()` 方法会追加行终止符。  
`PrintStream` 的无参 `void println()` 方法输出 `line.separator` 系统属性的值，以确保以可移植的方式终止行（例如在 Windows 上是回车后跟换行/换行符，在 Unix/Linux 上仅是一个换行/换行符）。  
你通过创建新的流源和目标对象，并将它们作为参数传递给 `System` 的 `setIn(InputStream)`、`setOut(PrintStream)` 和 `setErr(PrintStream)` 方法来重定向标准输入、标准输出和标准错误流。  
清单 A-3 展示了第 4 章中要求的 `Copy` 应用程序。  
清单 A-3\. 使用缓冲 I/O 将源文件复制到目标文件



`import java.io.BufferedInputStream;`

`import java.io.BufferedOutputStream;`

`import java.io.FileInputStream;`

`import java.io.FileNotFoundException;`

`import java.io.FileOutputStream;`

`import java.io.IOException;`

`public class Copy`

`{`

`public static void main(String[] args)`

`{`

`if (args.length != 2)`

`{`

`System.err.println("usage: java Copy srcfile dstfile");`

`return;`

`}`

`BufferedInputStream bis = null;`

`BufferedOutputStream bos = null;`

`try`

`{`

`FileInputStream fis = new FileInputStream(args[0]);`

`bis = new BufferedInputStream(fis);`

`FileOutputStream fos = new FileOutputStream(args[1]);`

`bos = new BufferedOutputStream(fos);`

`int b; // 我选择 b 而不是 byte，因为 byte 是保留字`

`while ((b = bis.read()) != -1)`

`bos.write(b);`

`}`

`catch (FileNotFoundException fnfe)`

`{`

`System.err.println(args[0] + " 无法打开以进行输入，或 "`

`+ args[1] + " 无法创建以进行输出");`

`}`

`catch (IOException ioe)`

`{`

`System.err.println("I/O 错误: " + ioe.getMessage());`

`}`

`finally`

`{`

`if (bis != null)`

`try`

`{`

`bis.close();`

`}`

`catch (IOException ioe)`

`{`

`assert false; // 在此上下文中不应发生`

`}`

`if (bos != null)`

`try`

`{`

`bos.close();`

`}`

`catch (IOException ioe)`

`{`

`assert false; // 在此上下文中不应发生`

`}`

`}`

`}`

`}`

清单 A-4 展示了第 4 章中要求的 `Split` 应用程序。  
清单 A-4\. 将一个大文件分割成多个较小的部分文件

`import java.io.BufferedInputStream;`

`import java.io.BufferedOutputStream;`

`import java.io.File;`

`import java.io.FileInputStream;`

`import java.io.FileOutputStream;`

`import java.io.IOException;`

`public class Split`

`{`

`static final int FILESIZE = 1400000;`

`static byte[] buffer = new byte[FILESIZE];`

`public static void main(String[] args)`

`{`

`if (args.length != 1)`

`{`

`System.err.println("usage: java Split path");`

`return;`

`}`

`File file = new File(args[0]);`

`long length = file.length();`

`int nWholeParts = (int) (length / FILESIZE);`

`int remainder = (int) (length % FILESIZE);`

`System.out.printf("正在将 %s 分割成 %d 个部分%n", args[0],`

`(remainder == 0) ? nWholeParts : nWholeParts + 1);`

`BufferedInputStream bis = null;`

`BufferedOutputStream bos = null;`

`try`

`{`

`FileInputStream fis = new FileInputStream(args[0]);`

`bis = new BufferedInputStream(fis);`

`for (int i = 0; i < nWholeParts; i++)`

`{`

`bis.read(buffer);`

`System.out.println("正在写入部分 " + i);`

`FileOutputStream fos = new FileOutputStream("part" + i);`

`bos = new BufferedOutputStream(fos);`

`bos.write(buffer);`

`bos.close();`

`bos = null;`

`}`

`if (remainder != 0)`

`{`

`int br = bis.read(buffer);`

`if (br != remainder)`

`{`

`System.err.println("最后一部分不匹配: 预期 " +`

`remainder + " 字节");`

`System.exit(0);`

`}`

`System.out.println("正在写入部分 " + nWholeParts);`

`FileOutputStream fos = new FileOutputStream("part" +`

`nWholeParts);`

`bos = new BufferedOutputStream(fos);`

`bos.write(buffer, 0, remainder);`

`}`

`}`

`catch (IOException ioe)`

`{`

`ioe.printStackTrace();`

`}`

`finally`

`{`

`if (bis != null)`

`try`

`{`

`bis.close();`

`}`

`catch (IOException ioe)`

`{`

`assert false; // 在此上下文中不应发生`

`}`

`if (bos != null)`

`try`

`{`

`bos.close();`

`}`

`catch (IOException ioe)`

`{`

`assert false; // 在此上下文中不应发生`

`}`

`}`

`}`

`}`

## 第 5 章: 写入器和读取器

Java 的流类不擅长流式处理字符，因为这些类不了解字符编码。  
在进行字符 I/O 时，Java 提供了写入器和读取器类作为流类的首选替代方案。  
答案是 false：`Reader` 没有声明 `available()` 方法。  
`OutputStreamWriter` 类的目的是作为传入字符序列和传出字节流之间的桥梁。写入此写入器的字符会根据默认或指定的字符编码被编码为字节。`InputStreamReader` 类的目的是作为传入字节流和传出字符序列之间的桥梁。从此读取器读取的字符会根据默认或指定的字符编码从字节解码。  
你可以通过读取 `file.encoding` 系统属性的值来识别默认字符编码。  
`FileWriter` 类的目的是使用默认字符编码方便地连接到底层文件输出流。`FileReader` 类的目的是使用默认字符编码方便地连接到底层文件输入流。  
`BufferedWriter` 提供了 `newLine()` 方法用于写入行分隔符。  
清单 A-5 展示了第 5 章中要求的 `CircleInfo` 应用程序。  
清单 A-5\. 从标准输入读取表示圆半径的文本行，并根据当前半径输出周长和面积

`import java.io.BufferedReader;`

`import java.io.InputStreamReader;`

`import java.io.IOException;`

`public class CircleInfo`

`{`

   `public static void main(String[] args) throws IOException`

   `{`

      `InputStreamReader isr = new InputStreamReader(System.in);`

      `BufferedReader br = new BufferedReader(isr);`

      `while (true)`

      `{`

         `System.out.print("请输入圆的半径: ");`

         `String str = br.readLine();`

         `double radius;`

         `try`

         `{`

            `radius = Double.valueOf(str).doubleValue();`

            `if (radius <= 0)`

               `System.err.println("半径不能为 0 或负数");`

            `else`

            `{`

               `System.out.println("周长: " + Math.PI * 2.0 *`

                                  `radius);`

               `System.out.println("面积: " + Math.PI * radius * radius);`

               `System.out.println();`

            `}`

         `}`

         `catch (NumberFormatException nfe)`

         `{`

            `System.err.println("不是数字: " + nfe.getMessage());`

         `}`

      `}`

   `}`

`}`

## 第 6 章: 缓冲区



缓冲区是一个用于存储固定数量数据的对象，这些数据将被发送到 I/O 服务（执行输入/输出的操作系统组件）或从该服务接收。它位于应用程序与通道之间，通道负责将缓冲的数据写入服务，或从服务读取数据并将其存入缓冲区。  
缓冲区的四个属性是容量、限制、位置和标记。  
当你在由只读数组支持的缓冲区上调用 `Buffer` 的 `array()` 方法时，该方法会抛出 `ReadOnlyBufferException`。  
当你在缓冲区上调用 `Buffer` 的 `flip()` 方法时，限制被设置为当前位置，然后位置被设置为零。如果定义了标记，它会被丢弃。此时缓冲区已准备好被清空。  
当你在未设置标记的缓冲区上调用 `Buffer` 的 `reset()` 方法时，该方法会抛出 `InvalidMarkException`。  
答案是 false：缓冲区不是线程安全的。  
扩展抽象 `Buffer` 类的抽象类包括 `ByteBuffer`、`CharBuffer`、`DoubleBuffer`、`FloatBuffer`、`IntBuffer`、`LongBuffer` 和 `ShortBuffer`。此外，`ByteBuffer` 被抽象类 `MappedByteBuffer` 进一步子类化。  
你可以通过调用 `allocate()`、`allocateDirect()` 或 `wrap()` 类方法来创建字节缓冲区。  
视图缓冲区是管理另一个缓冲区数据的缓冲区。  
视图缓冲区通过调用 `Buffer` 子类的 `duplicate()` 方法创建。  
你可以通过调用 `Buffer` 子类的方法（例如 `ByteBuffer asReadOnlyBuffer()` 或 `CharBuffer asReadOnlyBuffer()`）来创建只读视图缓冲区。  
`ByteBuffer` 用于在字节缓冲区中存储单个字节的方法有 `ByteBuffer put(int index, byte b)` 和 `ByteBuffer put(byte b)`。`ByteBuffer` 用于从字节缓冲区中获取单个字节的方法有 `byte get(int index)` 和 `byte get()`。  
当当前位置大于或等于限制时，尝试使用相对 `put()` 方法或相对 `get()` 方法会导致 `BufferOverflowException` 或 `BufferUnderflowException` 异常。  
执行 `buffer.flip();` 等同于执行 `buffer.limit(buffer.position()).position(0);`。  
答案是 false：连续调用两次 `flip()` 不会让你回到原始状态。相反，缓冲区的大小会变为零。  
`Buffer` 的 `clear()` 和 `reset()` 方法的区别如下：`clear()` 方法将缓冲区标记为空，而 `reset()` 将缓冲区的当前位置更改为先前设置的标记，如果没有先前设置的标记，则抛出 `InvalidMarkException`。  
`ByteBuffer` 的 `compact()` 方法通过将当前位置与限制之间的所有字节复制到缓冲区的开头来压缩缓冲区。索引 `p = position()` 处的字节被复制到索引 0，索引 `p + 1` 处的字节被复制到索引 1，依此类推，直到索引 `limit() - 1` 处的字节被复制到索引 `n = limit() - 1 - p`。然后，缓冲区的当前位置被设置为 `n + 1`，其限制被设置为容量。如果定义了标记，它会被丢弃。  
`ByteOrder` 类的目的是帮助你在向多字节缓冲区写入或从多字节缓冲区读取多字节值时处理字节顺序问题。  
直接字节缓冲区是一种与通道和本地代码交互以执行 I/O 的字节缓冲区。直接字节缓冲区尝试将字节元素存储在一个内存区域中，通道通过本地代码使用该区域执行直接（原始）访问，本地代码告诉操作系统直接清空或填充该内存区域。  
你可以通过调用 `ByteBuffer` 的 `allocateDirect()` 方法来获取直接字节缓冲区。  
创建直接字节缓冲区的开销可能很高，因为操作系统需要分配 JVM 堆之外的内存，并且设置/拆除此内存可能比缓冲区位于堆内时花费更长时间。  
清单 A-6 展示了第 6 章中要求的 `ViewBufferDemo` 应用程序。  
清单 A-6\. 将字节序列视为字符序列

`import java.nio.ByteBuffer;`

`import java.nio.CharBuffer;`

`public class ViewBufferDemo`

`{`

`public static void main(String[] args)`

`{`

`ByteBuffer bb = ByteBuffer.allocate(6);`

`byte zero = 0;`

`bb.put(zero).put((byte) 0x6e).put(zero).put((byte) 0x69)`

`.put(zero).put((byte) 0x6f);`

`bb.rewind();`

`CharBuffer cb = bb.asCharBuffer();`

`for (int i = 0; i < cb.limit(); i++)`

`System.out.print(cb.get());`

`}`

`}`

## 第 7 章: 通道



通道（Channel）是一个对象，代表与硬件设备、文件、网络套接字、应用程序组件或其他能够执行写入、读取及其他 I/O 操作的实体之间的开放连接。通道在字节缓冲区和基于操作系统的 I/O 服务源或目标之间高效传输数据。  
`Channel` 接口提供的功能包括关闭通道（通过 `close()` 方法）以及判断通道是否处于打开状态（通过 `isOpen()` 方法）。  
直接扩展 `Channel` 的三个接口是 `WritableByteChannel`、`ReadableByteChannel` 和 `InterruptibleChannel`。  
答案是正确：实现了 `InterruptibleChannel` 的通道是异步可关闭的。  
获取通道的两种方式是调用 `Channels` 类的方法（例如 `WritableByteChannel newChannel(OutputStream outputStream)`），以及调用经典 I/O 类上的通道方法（例如 `RandomAccessFile` 的 `FileChannel getChannel()` 方法）。  
分散/聚集 I/O 是指跨多个缓冲区执行单个 I/O 操作的能力。  
提供了 `ScatteringByteChannel` 和 `GatheringByteChannel` 接口来实现分散/聚集 I/O。  
文件通道是通往底层文件的通道。  
答案是错误：文件通道支持分散/聚集 I/O。  
排他锁允许单个写入进程访问文件区域；它禁止同时对该区域应用其他文件锁。共享锁允许多个读取进程中的某一个访问同一文件区域；它不禁止其他共享锁，但禁止同时对该区域应用排他锁。  
`FileChannel` 的 `lock()` 和 `tryLock()` 方法之间的根本区别在于，`lock()` 方法可能会阻塞，而 `tryLock()` 方法从不阻塞。  
当已持有与此锁请求重叠的锁，或另一个线程正在等待获取将与此请求重叠的锁时，`FileLock lock()` 方法会抛出 `OverlappingFileLockException`。  
为确保获取的文件锁始终被释放，应采用的模式如下：  
`FileLock lock = fileChannel.lock(); // 假设 fileChannel 已存在。`  
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
`FileChannel` 提供了 `MappedByteBuffer map(FileChannel.MapMode mode, long position, long size)` 方法，用于将文件区域映射到内存中。  
三种文件映射模式是只读、读写和私有。它们由 `FileChannel.MapMode` 枚举类型声明的 `READ_ONLY`、`READ_WRITE` 和 `PRIVATE` 常量描述。  
私有文件映射模式对应写时复制。对生成的缓冲区所做的更改不会传播到文件，也不会对其他映射了同一文件的程序可见。相反，更改会导致创建缓冲区修改部分的私有副本。当缓冲区被垃圾回收时，这些更改将丢失。  
优化批量传输常见实践的 `FileChannel` 方法是 `transferFrom()` 和 `transferTo()`。  
答案是正确：套接字通道是可选择的，并且可以在非阻塞模式下运行。  
描述套接字通道的三个类是 `ServerSocketChannel`、`SocketChannel` 和 `DatagramChannel`。  
答案是错误：数据报通道是线程安全的。  
套接字通道支持非阻塞模式，因为 Java 套接字类创建的套接字的阻塞特性是面向网络的 Java 应用程序可扩展性的严重限制。  
通过调用套接字通道的 `socket()` 方法可以获取其关联的套接字。  
通过调用 `ServerSocketChannel` 的 `open()` 类方法可以获取服务器套接字通道。  
清单 A-7 展示了 第 7 章 中要求的 `Copy` 应用程序。  
清单 A-7\. 通过字节缓冲区和文件通道复制文件

`import java.io.FileInputStream;`

`import java.io.FileNotFoundException;`

`import java.io.FileOutputStream;`

`import java.io.IOException;`

`import java.nio.ByteBuffer;`

`import java.nio.channels.FileChannel;`

`public class Copy`

`{`

`public static void main(String[] args)`

`{`

`if (args.length != 2)`

`{`

`System.err.println("usage: java Copy srcfile dstfile");`

`return;`

`}`

`try (FileInputStream fis = new FileInputStream(args[0]);`

`FileChannel fcSrc = fis.getChannel();`

`FileOutputStream fos = new FileOutputStream(args[1]);`

`FileChannel fcDest = fos.getChannel())`

`{`

`ByteBuffer buffer = ByteBuffer.allocateDirect(2048);`

`while ((fcSrc.read(buffer)) != -1)`

`{`

`buffer.flip();`

`while (buffer.hasRemaining())`

`fcDest.write(buffer);`

`buffer.clear();`

`}`

`}`

`catch (FileNotFoundException fnfe)`

`{`

`System.err.println(args[0] +`

`" could not be opened for input, or " +`

`args[1] +`

`" could not be created for output");`

`}`

`catch (IOException ioe)`

`{`

`System.err.println("I/O error: " + ioe.getMessage());`

`}`

`}`

`}`

## 第 8 章：选择器

选择器是从抽象类 `Selector` 的子类创建的对象。它维护一组通道，并检查这些通道以确定哪些通道已准备好进行读取、写入、完成连接序列、接受另一个连接或这些任务的某种组合。实际工作通过 POSIX `select()` 或类似的系统调用委托给操作系统。  
支持选择器的三种主要类型是 `SelectableChannel`、`SelectionKey` 和 `Selector`。  
答案是错误：文件通道不能与选择器一起使用。只有实现了 `SelectableChannel` 的通道才能与选择器一起使用。`FileChannel` 没有实现 `SelectableChannel`。  
`SelectionKey` 提供了 `boolean` `isReadable()` 方法，作为表达式 `key.readyOps() & OP_READ != 0` 的便捷替代方案。



## 第 9 章：正则表达式

正则表达式（也称为 regex 或 regexp）是一种基于字符串的模式，它表示与该模式匹配的字符串集合。`Pattern` 类的实例通过编译后的正则表达式来表示模式。出于性能原因，正则表达式会被编译；通过编译后的正则表达式进行模式匹配，其速度远快于未编译的正则表达式。当 `Pattern` 的 `compile()` 方法在其正则表达式参数中发现非法语法时，会抛出 `PatternSyntaxException`。`Matcher` 类的实例尝试将编译后的正则表达式与输入文本进行匹配。`Matcher` 的 `matches()` 和 `lookingAt()` 方法的区别在于，与 `matches()` 不同，`lookingAt()` 不要求匹配整个区域。

字符类是出现在 `[` 和 `]` 之间的一组字符。字符类有六种：简单类、否定类、范围类、并集类、交集类和差集类。答案是错误的：差集字符类由多个以 `&&` 分隔的嵌套字符类组成，其中至少有一个嵌套字符类是否定字符类，它匹配除否定字符类所指示字符之外的所有字符。

捕获组会保存匹配的字符，以便在模式匹配期间稍后调用。零长度匹配是长度为零的匹配，其起始索引和结束索引相等。量词是隐式或显式绑定到模式的数值。量词分为贪婪型、勉强型和占有型。贪婪量词和勉强量词的区别在于，贪婪量词试图找到最长的匹配，而勉强量词试图找到最短的匹配。占有型量词和贪婪量词的区别在于，占有型量词只尝试一次以找到最长的匹配，而贪婪量词可以进行多次尝试。

清单 A-8 展示了 第 9 章 中要求的 `ReplaceText` 应用程序。

清单 A-8\. 用替换文本替换模式的所有匹配项

`import java.util.regex.Matcher;`

`import java.util.regex.Pattern;`

`import java.util.regex.PatternSyntaxException;`

`public class ReplaceText`

`{`

   `public static void main(String[] args)`

   `{`

      `if (args.length != 3)`

      `{`

         `System.err.println("usage: java ReplaceText text oldText newText");`

         `return;`

      `}`

      `try`

      `{`

         `Pattern p = Pattern.compile(args[1]);`

         `Matcher m = p.matcher(args[0]);`

         `String result = m.replaceAll(args[2]);`

         `System.out.println(result);`

      `}`

      `catch (PatternSyntaxException pse)`

      `{`

         `System.err.println(pse);`

      `}`

   `}`

`}`

## 第 10 章：字符集

字符集是编码字符集与字符编码方案的结合。`Charset` 类的目的是在字节序列和编码到这些序列中的字符之间进行转换。JVM 支持的标准字符集有 US-ASCII、ISO-8859-1、UTF-8、UTF-16BE、UTF-16LE 和 UTF-16。字节顺序标记的目的是告诉解码器一个 `UTF-16` 编码的字节序列是如何编码的。你可以通过调用 `Charset` 的 `Charset` `defaultCharset()` 工厂方法来获取默认字符集。当 JVM 不支持所需的字符集时，`Charset` 的 `Charset forName(String charsetName)` 工厂方法会抛出 `UnsupportedCharsetException`。

通常，你会通过调用 `Charset` 的 `ByteBuffer encode(String s)` 方法来使用 `Charset` 实例对字符串进行编码，该方法返回一个新的 `ByteBuffer` 对象，其中包含对 `s` 中的字符进行编码后的字节。你也可以调用 `Charset` 的 `ByteBuffer encode(CharBuffer cb)` 方法。执行实际编码和解码任务的 `Charset` 方法是 `CharsetEncoder newEncoder()` 和 `CharsetDecoder newDecoder()`。`String` 的 `byte[]` `getBytes()` 方法返回一个新的字节数组，其中包含使用操作系统默认字符集编码的此字符串的字符。

清单 A-9 展示了 第 10 章 中要求的 `AvailCharsets` 应用程序。

清单 A-9\. 获取并输出可用字符集的映射

`import java.nio.charset.Charset;`

`import java.util.SortedMap;`

`public class AvailCharsets`

`{`

   `public static void main(String[] args)`

   `{`

      `SortedMap<String, Charset> acs = Charset.availableCharsets();`

      `System.out.println(acs);`

   `}`

`}`

## 第 11 章：格式化器

构成 NIO 的 `printf` 风格格式化功能的三个非异常类型是 `Formatter`、`Formattable` 和 `FormattableFlags`。你可以通过在格式说明符字符串中指定参数的基于 1 的索引后跟美元符号字符来引用参数。例如，`1$` 引用第一个参数，`2$` 引用第二个参数。`%n` 格式说明符输出一个特定于操作系统的行分隔符。

清单 A-10 展示了 第 11 章 中要求的 `FormatterDemo` 应用程序。

清单 A-10\. 格式化输出到外部 `StringBuilder` 对象

`import java.util.Formatter;`

`public class FormatterDemo`

`{`

   `public static void main(String[] args)`

   `{`

      `StringBuilder output = new StringBuilder();`

      `Formatter formatter = new Formatter(output);`

      `formatter.format("%d", 123);`

      `System.out.println(formatter.toString());`

      `output.setLength(0);`

      `formatter.format("%x", 123);`

      `System.out.println(formatter.toString());`

      `output.setLength(0);`

      `formatter.format("%c", ’X’);`

      `System.out.println(formatter.toString());`

      `output.setLength(0);`

      `formatter.format("%f", 0.1);`

      `System.out.println(formatter.toString());`

      `output.setLength(0);`

      `formatter.format("%s%n", "Hello, World");`

      `System.out.println(formatter.toString());`

      `output.setLength(0);`

      `formatter.format("%10.2f", 98.375);`

      `System.out.println(formatter.toString());`

      `output.setLength(0);`

      `formatter.format("%05d", 123);`

      `System.out.println(formatter.toString());`

      `output.setLength(0);`

      `formatter.format("%1$d %1$d", 123);`

      `System.out.println(formatter.toString());`

      `output.setLength(0);`

      `formatter.format("%d %d", 123);`

      `System.out.println(formatter.toString());`

      `output.setLength(0);`

      `formatter.close();`

   `}`

`}`

## 第 12 章：改进的文件系统接口



文件系统是操作系统的一个组件，负责管理基于文件的数据存储。文件可以是常规文件、目录、符号链接或硬链接。  
当 `File` 的 `delete()` 方法在出错时返回 `false` 会带来问题，因为你无法得知文件删除失败的原因（可能是文件不存在，也可能是用户没有相应权限）。抛出特定的异常会更有帮助。  
实现改进后文件系统接口的包是 `java.nio.file`、`java.nio.file.attribute` 和 `java.nio.file.spi`。  
构成改进后文件系统接口核心的类型是 `FileSystem`、`FileSystems` 和 `FileSystemProvider`。  
你可以通过调用 `FileSystems` 类的 `FileSystem getDefault()` 类方法来获取默认文件系统的引用。  
你可以通过调用 `FileSystems` 类的 `newFileSystem()` 方法并传入适当的参数来创建一个新的文件系统。  
路径是一种紧凑的映射，通过分隔的名称元素序列来导航文件的层级结构。  
答案是 false：`Path` 接口表示指向一个可能不存在的文件的层级路径。  
`Path` 对象存储的路径可选地以标识文件系统层级的名称元素开头，并可选择地继续以由分隔符分隔的目录元素序列。距离目录层级根最远的名称元素是目录或其他类型文件的名称。其他名称元素是目录名。  
`Path` 声明了 `File toFile()` 方法，用于返回表示其路径的 `File` 对象。`File` 声明了 `Path toPath()` 方法，用于返回表示该 `File` 对象抽象路径的 `Path` 对象。  
`FileSystem` 提供了 `Path getPath(String first, String... more)` 方法用于返回 `Path` 对象。  
当你尝试使用不符合文件系统提供者（该提供者创建了负责生成 `Path` 对象的 `FileSystem`）所解析语法的语法来创建 `Path` 对象时，会抛出 `InvalidPathException`。  
`Paths` 类提供的用于更方便地返回 `Path` 对象的方法是 `Path get(String first, String... more)` 和 `Path get(URI uri)`。  
`Path` 用于返回其名称元素的方法有 `Path getFileName()`、`Path getName(int index)`、`int getNameCount()`、`Path getParent()`、`Path getRoot()` 和 `Path subpath(int beginIndex, int endIndex)`。  
答案是 false：`Path` 的 `boolean` `isAbsolute()` 方法返回 `true` 表示绝对路径。不存在 `isRelative()` 方法。  
你可以通过调用 `FileSystem` 的 `Iterable<Path>` `getRootDirectories()` 方法来获取文件系统的根目录，该方法返回一个迭代器，用于遍历描述根目录的 `Path` 实例。  
你可以通过调用 `Path` 的 `Path` `toAbsolutePath()` 方法将相对路径转换为绝对路径。  
`Path` 中用于移除路径冗余、创建两个路径之间的相对路径以及解析（连接）两个路径的方法分别是 `Path normalize()`、`Path relativize(Path other)`、`Path resolve(Path other)` 和 `Path resolve(String other)`。  
你可以通过调用 `Path` 的 `Path resolveSibling(Path other)` 或 `Path resolveSibling(String other)` 方法，将路径字符串解析到当前路径的父路径上。  
你可以通过调用 `Path` 的 `URI toURI()` 方法将当前的 `Path` 实例转换为 `URI` 对象。  
`Path toRealPath(LinkOption... options)` 方法返回由 `Path` 对象所表示文件的真实路径。此方法通常根据此路径派生出一个绝对路径，该绝对路径定位与此路径相同的[文件](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Files.html#isSameFile-java.nio.file.Path-java.nio.file.Path-)，但其名称元素表示目录和任何非目录文件的实际名称。你可以传递一个以逗号分隔的 `LinkOption` 枚举常量列表作为参数。此枚举定义了处理符号链接的选项。目前，`LinkOption` 仅声明了一个 `NOFOLLOW_LINKS`（不跟踪符号链接）常量。  
答案是 false：`Files` 类不提供用于执行路径匹配和目录监视任务的 `static` 方法。你需要调用 `FileSystem` 的方法来获取路径匹配器和监视服务。  
文件存储是一个存储池、设备、分区、卷、具体文件系统或其他特定于实现的文件存储方式。一个文件存储由名称、类型、空间量（以字节为单位）和其他信息组成。文件存储由 `FileStore` 类表示。  
`Files` 类提供了 `FileStore getFileStore(Path path)` 方法，用于返回一个 `FileStore`，该对象表示存储由 `path` 标识的文件的文件存储。  
你可以获取空间量（总空间、未分配空间和可用空间）、文件存储是否为只读的指示，以及文件存储的名称和类型。  
你可以通过调用 `FileSystem` 的 `Iterable<FileStore>` `getFileStores()` 方法来获取给定文件系统的所有文件存储，该方法允许你遍历所有文件存储。  
NIO.2 提供的用于处理属性的支持包括存储在 `java.nio.file.attribute` 包中的类型，以及 `Files` 类和其他类型的面向属性的方法。  
属性被组织成视图，每个视图对应一个特定的文件系统实现。  
属性类型层次结构的根是 `AttributeView`。该接口的子类型是 `FileAttributeView`。`FileAttributeView` 的子类型是 `BasicFileAttributeView`、`FileOwnerAttributeView` 和 `UserDefinedFileAttributeView`。`BasicFileAttributeView` 的子类型是 `DosFileAttributeView` 和 `PosixFileAttributeView`。`FileOwnerAttributeView` 的子类型是 `AclFileAttributeView` 和 `PosixFileAttributeView`。`AttributeView` 的子类型还有 `FileStoreAttributeView`。  
你可以通过调用 `FileSystem` 的 `Set<String> supportedFileAttributeViews()` 方法来识别给定文件系统所有支持的属性视图，该方法返回一个字符串集合，标识调用该方法的 `FileSystem` 所支持的视图。  
基本属性是许多文件系统共有的属性。用于管理基本属性的视图是 `BasicFileAttributeView`。基本属性包括 `creationTime`、`fileKey`、`isDirectory`、`isOther`、`isRegularFile`、`isSymbolicLink`、`lastAccessTime`、`lastModifiedTime` 和 `size`。  
答案是 true：你可以通过调用 `BasicFileAttributeView` 的 `BasicFileAttributes` `readAttributes()` 方法来批量读取基本属性。你也可以间接地通过调用 `Files` 类的 `<A extends BasicFileAttributes> A readAttributes(Path path, Class<A> type, LinkOption... options)` 方法来完成此任务，并将 `BasicFileAttributes.class` 传递给 `type` 参数。  
文件键是一个标识符或标识符的组合，用于唯一标识一个文件。  
当你调用 `Files` 类的 `getAttribute()` 或 `setAttribute()` 方法来获取或设置基本或其他类型的属性值时，标识属性必须遵循的语法是 `[view-name:]attribute-name`。对于基本属性，你不需要指定 `view-name`，因为 `basic` 是默认的 `view-name`。  
`UserPrincipal` 表示用于确定文件系统中对象访问权限的身份，并扩展了 `Principal`。`GroupPrincipal` 表示组身份，并扩展了 `UserPrincipal`。  
在 Windows 操作系统上，当无法将安全 ID 分配为所有者时，`FileOwnerAttributeView` 的 `setOwner()` 方法会抛出 `FileSystemException`，该所有者必须是您被授予分配所有者权限的用户或组之一。解决方案是提升 `Java` 应用程序的权限。  
`AclEntry` 的组成部分是 `type`、`principal`、`permissions` 和 `flags`。这些组成部分由 `AclEntryType`、`UserPrincipal`、`AclEntryPermission` 和 `AclEntryFlag` 类型描述。  
答案是 true：你可以定义自己的文件属性。使用 `UserDefinedFileAttributeView` 类型来完成此任务。  
`FileStoreAttributeView` 支持的属性是 `totalSpace`、`unallocatedSpace` 和 `usableSpace`。你可以通过将这些名称传递给 `FileStore` 的 `getAttribute()` 方法来访问这些属性的值，尽管更推荐通过 `FileStore` 的类型安全方法 `getTotalSpace()`、`getUnallocatedSpace()` 和 `getUsableSpace()` 来访问它们。  
答案是 false：`!exists(path)` 不等同于 `notExists(path)`。  
`isDirectory()` 方法关于符号链接的默认策略是跟踪它们。如果你不想跟踪符号链接，请将 `LinkOption.NOFOLLOW_LINKS` 传递给此方法。  
使用 `exists()`、`notExists()`、`isExecutable()`、`isReadable()` 和 `isWritable()` 的返回值时应小心，因为这些返回值会立即过时。在方法调用和其结果被使用之间，文件系统可能会发生变化。  
当调用 `createFile()` 方法创建已存在的文件时，会抛出 `FileAlreadyExistsException`。  
可选的特定异常是指当底层操作系统能够检测到导致异常的特定错误时抛出的异常。如果无法检测到该错误，则会抛出其父类 `IOException`。  
你可以在创建文件时设置 POSIX 文件权限，方法是调用 `PosixFilePermissions` 类的 `FileAttribute<Set<PosixFilePermission>> asFileAttribute(Set<PosixFilePermission> perms)` 方法来创建一个封装了给定文件权限副本的 `FileAttribute`，然后将 `asFileAttribute()` 的返回值作为第二个参数传递给 `createFile()`。  
`Path createTempFile(String prefix, String suffix, FileAttribute<?>... attrs)` 方法在默认的临时文件目录（由 Java 属性 `java.io.tmpdir` 标识）中创建临时文件。  
在应用程序退出前删除临时文件的三种方法如下：



*   通过 `Runtime` 类的 `void addShutdownHook(` [`Thread`](https://docs.oracle.com/javase/8/docs/api/java/lang/Thread.html#class%20in%20java.lang) `hook)` 方法添加一个关闭钩子，并在该钩子中删除文件。
*   将返回的 `Path` 对象转换为 `File` 对象（通过 `Path` 的 `toFile()` 方法），并在该 `File` 对象上调用 `File` 的 `void deleteOnExit()` 方法。
*   使用 `Files` 类的 `newOutputStream()` 方法和 NIO.2 的 `DELETE_ON_CLOSE` 常量。

NIO.2 中用于从常规文件读取所有字节或文本行到内存的三个方法是 `byte[] readAllBytes(Path path)`、`List<String> readAllLines(Path path)` 和 `List<String> readAllLines(Path path, Charset cs)`。用于读取非常大的文件（其内容可能无法放入内存）的方法是 `BufferedReader newBufferedReader(Path path)`、`BufferedReader newBufferedReader(Path path, Charset cs)` 和 `InputStream newInputStream(Path path, OpenOption... options)`。`StandardOpenOption` 枚举支持的打开选项如下：

*   `APPEND`：如果文件以 `WRITE` 访问权限打开，则将字节写入文件末尾，而非开头。
*   `CREATE`：当文件不存在时创建新文件。
*   `CREATE_NEW`：创建新文件，若文件已存在则失败。
*   `DELETE_ON_CLOSE`：在文件关闭时尽最大努力删除该文件。
*   `DSYNC`：要求对文件内容的每次更新都同步写入底层存储设备。
*   `READ`：以读取访问权限打开文件。
*   `SPARSE`：打开一个稀疏文件。当与 [`CREATE_NEW`](https://docs.oracle.com/javase/8/docs/api/java/nio/file/StandardOpenOption.html#CREATE_NEW) 选项一起使用时，`SPARSE` 会提示新文件将是稀疏文件。如果文件系统不支持创建稀疏文件，则忽略此选项。
*   `SYNC`：要求对文件内容或元数据的每次更新都同步写入底层存储设备。
*   `TRUNCATE_EXISTING`：将以 [`WRITE`](https://docs.oracle.com/javase/8/docs/api/java/nio/file/StandardOpenOption.html#WRITE) 访问权限打开的现有文件的长度截断为 0。
*   `WRITE`：以写入访问权限打开文件。



NIO.2 提供了三种将内存中的全部字节或文本行写入常规文件的方法：`Path write(Path path, byte[] bytes, OpenOption... options)`、`Path write(Path path, Iterable<? extends CharSequence> lines, Charset cs, OpenOption... options)` 和 `Path write(Path path, Iterable<? extends CharSequence> lines, OpenOption... options)`。用于写入非常大的文件（其内容可能无法放入内存）的方法是 [`BufferedWriter`](https://docs.oracle.com/javase/8/docs/api/java/io/BufferedWriter.html#class%20in%20java.io) `newBufferedWriter(` [`Path`](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Path.html#interface%20in%20java.nio.file) `path,` [`Charset`](https://docs.oracle.com/javase/8/docs/api/java/nio/charset/Charset.html#class%20in%20java.nio.charset) `cs,` [`OpenOption`](https://docs.oracle.com/javase/8/docs/api/java/nio/file/OpenOption.html#interface%20in%20java.nio.file) `... options)`, [`BufferedWriter`](https://docs.oracle.com/javase/8/docs/api/java/io/BufferedWriter.html#class%20in%20java.io) `newBufferedWriter(` [`Path`](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Path.html#interface%20in%20java.nio.file) `path,` [`OpenOption`](https://docs.oracle.com/javase/8/docs/api/java/nio/file/OpenOption.html#interface%20in%20java.nio.file) `... options)`, 以及 [`OutputStream`](https://docs.oracle.com/javase/8/docs/api/java/io/OutputStream.html#class%20in%20java.io) `newOutputStream(` [`Path`](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Path.html#interface%20in%20java.nio.file) `path,` [`OpenOption`](https://docs.oracle.com/javase/8/docs/api/java/nio/file/OpenOption.html#interface%20in%20java.nio.file) `... options)`。答案是肯定的：当未指定任何选项时，`newOutputStream()` 的行为就像指定了 [`CREATE`](https://docs.oracle.com/javase/8/docs/api/java/nio/file/StandardOpenOption.html#CREATE)、[`TRUNCATE_EXISTING`](https://docs.oracle.com/javase/8/docs/api/java/nio/file/StandardOpenOption.html#TRUNCATE_EXISTING) 和 [`WRITE`](https://docs.oracle.com/javase/8/docs/api/java/nio/file/StandardOpenOption.html#WRITE) 选项一样。

`SeekableByteChannel` 接口的目的是描述可以随机访问的字节通道。它提供了获取当前位置以及将当前位置更改为新位置的能力。

`FileChannel` 类指定了 `FileChannel position(long newPosition)` 和 `FileChannel truncate(long size)`，而不是指定 `SeekableByteChannel position(long newPosition)` 和 `SeekableByteChannel truncate(long size)`，原因如下：`SeekableByteChannel` 的文档建议，实现 `SeekableByteChannel` 的类应特化方法的返回类型，以便可以在实现类上链式调用方法。

你可以通过调用 `Files` 类的 `SeekableByteChannel newByteChannel(Path path, OpenOption... options)` 方法或其 [`SeekableByteChannel`](https://docs.oracle.com/javase/8/docs/api/java/nio/channels/SeekableByteChannel.html#interface%20in%20java.nio.channels) `newByteChannel(` [`Path`](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Path.html#interface%20in%20java.nio.file) `path,` [`Set`](https://docs.oracle.com/javase/8/docs/api/java/util/Set.html#interface%20in%20java.util) `<? extends` [`OpenOption`](https://docs.oracle.com/javase/8/docs/api/java/nio/file/OpenOption.html#interface%20in%20java.nio.file) `> options,` [`FileAttribute`](https://docs.oracle.com/javase/8/docs/api/java/nio/file/attribute/FileAttribute.html#interface%20in%20java.nio.file.attribute) `<?>... attrs)` 方法来获取 `SeekableByteChannel` 对象。

NIO.2 在 `FileChannel` 类中新增了 `FileChannel open(Path path, OpenOption... options)` 和 `FileChannel open(Path path, Set<? extends OpenOption> options, FileAttribute<?>... attrs)` 方法，这样你就不再需要依赖传统的 I/O 类型（如 `RandomAccessFile`）来获取文件通道了。

`Files` 类提供了 `Path createDirectory(Path dir, FileAttribute<?>... attrs)` 方法来创建目录。

答案是错误的：`Files` 类的目录创建方法不会自动创建被创建目录的尚不存在的祖先目录。你需要使用 `createDirectories()` 方法来完成该任务。

`Files` 类用于创建临时目录的方法是 `Path createTempDirectory(Path dir, String prefix, FileAttribute<?>... attrs)` 和 `Path createTempDirectory(String prefix, FileAttribute<?>... attrs)`。

NIO.2 提供了 `DirectoryStream<T>` 接口，以及 `Files` 类中的 [`DirectoryStream`](https://docs.oracle.com/javase/8/docs/api/java/nio/file/DirectoryStream.html#interface%20in%20java.nio.file) `<` [`Path`](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Path.html#interface%20in%20java.nio.file) `> newDirectoryStream(` [`Path`](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Path.html#interface%20in%20java.nio.file) `dir)`, `DirectoryStream<Path> newDirectoryStream(Path dir, DirectoryStream.Filter<? super Path> filter)`, 以及 [`DirectoryStream`](https://docs.oracle.com/javase/8/docs/api/java/nio/file/DirectoryStream.html#interface%20in%20java.nio.file) `<` [`Path`](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Path.html#interface%20in%20java.nio.file) `> newDirectoryStream(` [`Path`](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Path.html#interface%20in%20java.nio.file) `dir,` [`String`](https://docs.oracle.com/javase/8/docs/api/java/lang/String.html#class%20in%20java.lang) `glob)` 方法，用于获取目录条目列表。

你可以通过继承 `DirectoryStream.Filter<T>` 并重写其 `boolean accept(T path)` 方法（确保将 `Path` 传递给 `T`）来过滤目录条目列表，以便只返回所需的条目。当 `path` 被接受（包含在目录流中）时，此方法必须返回 `true`；否则，必须返回 `false`。

`Files` 提供了 `long copy(InputStream in, Path target, CopyOption... options)`、`long copy(Path source, OutputStream out)` 和 `Path copy(Path source, Path target, CopyOption... options)` 方法来将文件复制到另一个文件。

`StandardCopyOption` 枚举支持的复制选项如下：



*   `ATOMIC_MOVE`：以原子文件系统操作的方式执行移动。`copy()` 方法不使用此常量，因为在复制上下文中它没有意义。
*   `COPY_ATTRIBUTES`：复制内容的同时也复制属性。
*   `REPLACE_EXISTING`：替换已存在的目标文件。

可以传递给这些 `copy()` 方法的另一个复制选项是 `NOFOLLOW_LINKS`（不跟踪符号链接）。此复制选项由 `LinkOption` 枚举提供，该枚举（与 `StandardCopyOption` 一起）实现了 `CopyOption` 接口。最终，所有复制选项都是 `CopyOption` 的实例。`Files` 提供了 [`Path`](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Path.html#interface%20in%20java.nio.file) `move(` [`Path`](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Path.html#interface%20in%20java.nio.file) `source,` [`Path`](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Path.html#interface%20in%20java.nio.file) `target,` [`CopyOption`](https://docs.oracle.com/javase/8/docs/api/java/nio/file/CopyOption.html#interface%20in%20java.nio.file) `... options)` 方法用于将文件移动到另一个文件。此文件移动方法支持的复制选项是 `ATOMIC_MOVE` 和 `REPLACE_EXISTING`。

用于删除文件的 `Files` 方法是 `void delete(Path path)` 和 `boolean deleteIfExists(Path path)`。

符号链接（软链接或 symlink）是一种引用另一个文件的特殊文件。循环引用是指链接的目标指回原始链接本身。

`Files` 类提供了 `Path createSymbolicLink(Path link, Path target, FileAttribute<?>... attrs)` 方法，用于创建指向目标的符号链接。

你可以通过调用 `Files` 类的 `boolean isSymbolicLink(Path path)` 方法来判断任意路径是否代表一个符号链接。当 `path` 是符号链接时返回 `true`；当 `path` 指向一个不存在的文件、不是符号链接或无法确定该文件是否为符号链接时返回 `false`。

你可以通过调用 `Files` 类的 `Path readSymbolicLink(Path link)` 方法来读取符号链接的目标。

硬链接是一个目录项，它将一个名称与文件系统上的一个[文件](https://en.wikipedia.org/wiki/Computer_file#Computer%20file)关联起来。它本质上与原始文件是同一个实体。所有属性都是相同的：它们具有相同的文件权限、时间戳等。

硬链接在以下方面比软链接更具限制性：

*   链接的目标必须存在。
*   通常不允许在目录上创建硬链接。
*   不允许硬链接跨越分区或卷。换句话说，它们不能存在于不同的文件系统之间。
*   硬链接的外观和行为类似于普通文件，因此可能难以发现。

`Files` 类提供了 `Path createLink(Path link, Path existing)` 方法，用于为现有的 `existing` 文件创建一个硬 `link`（目录项）。

文件树遍历 API 提供了遍历文件树并访问其所有文件（常规文件、目录和链接）的能力。除了提供执行遍历的私有实现外，此 API 还提供了一个供应用程序使用的公共接口。

构成文件树遍历 API 公共部分的类型有 `FileVisitor<T>`、`FileVisitResult`、`SimpleFileVisitor<T>`、`FileVisitOption` 和 `FileSystemLoopException`。

答案是肯定的：`SKIP_SUBTREE` 仅在从 [`preVisitDirectory`](https://docs.oracle.com/javase/8/docs/api/java/nio/file/FileVisitor.html#preVisitDirectory-T-java.nio.file.attribute.BasicFileAttributes-) `()` 方法返回时才有意义；否则，此结果类型与 [`CONTINUE`](https://docs.oracle.com/javase/8/docs/api/java/nio/file/FileVisitResult.html#CONTINUE) 相同。

`Files` 类提供了 `Path walkFileTree(Path start, FileVisitor<? super Path> visitor)` 和 `Path walkFileTree(Path start, Set<FileVisitOption> options, int maxDepth, FileVisitor<? super Path> visitor)` 方法用于遍历文件树。

`Stream<Path> find(Path start, int maxDepth, BiPredicate<Path,BasicFileAttributes> matcher, FileVisitOption... options)` 方法返回一个 `Stream<Path>` 对象，该对象会惰性地填充那些被 `BiPredicate` 对象接受的文件的 `Path`。

NIO.2 支持如下路径匹配：`FileSystem` 的 `PathMatcher getPathMatcher(String syntaxAndPattern)` 方法返回一个 `PathMatcher` 对象，用于根据 `syntaxAndPattern` 描述的模式来匹配路径，`syntaxAndPattern` 通过此语法标识一种模式语言 (`syntax`) 和一个模式 (`pattern`)。`PathMatcher` 接口声明了一个 `boolean matches(Path path)` 方法，该方法将其 `path` 参数与 `PathMatcher` 的当前模式进行比较。匹配时返回 `true`；否则返回 `false`。

监视服务 API 的目的是监视已注册的目录以获取更改和事件。例如，文件管理器可以使用监视服务来监视目录的更改，以便在创建或删除文件时更新其文件列表显示。

以下类型构成了监视服务 API：

*   `Watchable`：一个接口，描述任何可以注册到监视服务的对象，以便可以监视其更改和事件。由于 `Path` 扩展了 `Watchable`，因此可以监视表示为 `Path` 的目录中的所有条目。
*   `WatchEvent<T>`：一个接口，描述已注册到监视服务的对象的任何事件或重复事件。
*   `WatchEvent<T>.Kind`：一个嵌套接口，用于标识事件种类（例如目录条目创建）。
*   `WatchEvent<T>.Modifier`：一个嵌套接口，用于限定如何将可监视对象注册到监视服务。此接口目前尚未使用。
*   `WatchKey`：一个接口，描述代表可监视对象在监视服务中注册的令牌。
*   `WatchService`：一个接口，描述任何监视已注册对象以获取更改和事件的对象。
*   `StandardWatchEventKinds`：一个类，描述四种事件种类常量（目录条目创建、删除或修改；以及溢出，表示由于文件系统生成事件过快，可能已丢失事件）。
*   `ClosedWatchServiceException`：一个类，描述当尝试对已关闭的监视服务调用操作时抛出的未检查异常。

清单 A-11 展示了第 12 章中要求的 `Touch` 应用程序。

清单 A-11\. 通过 NIO.2 将文件或目录的时间戳设置为当前时间

`import java.io.IOException;`

`import java.nio.file.Files;`

`import java.nio.file.Paths;`

`import java.nio.file.attribute.FileTime;`

`import java.time.Instant;`

`public class Touch`

`{`

`public static void main(String[] args) throws IOException`

`{`

`if (args.length != 1)`

`{`

`System.err.println("usage: java Touch path");`

`return;`

`}`

`Files.setLastModifiedTime(Paths.get(args[0]),`

`FileTime.from(Instant.now()));`

`}`

`}`

## 第 13 章：异步 I/O



异步通道支持异步 I/O 操作（读取、写入等）。通过异步通道发起 I/O 操作的两种方式如下：调用返回 `Future` 的方法，以及调用接受 `CompletionHandler` 参数的方法。答案是 false：通道关闭后，进一步尝试发起异步 I/O 操作会立即完成，并抛出 `ClosedChannelException` 异常。

`AsynchronousByteChannel` 接口声明的方法包括 `Future<Integer> read(ByteBuffer dst)`、`<A> void read(ByteBuffer dst, A attachment, CompletionHandler<Integer,? super A> handler)`、`Future<Integer> write(ByteBuffer src)` 以及 `<A> void write(ByteBuffer src, A attachment, CompletionHandler<Integer,? super A> handler)`。答案是 false：[`ByteBuffer`](https://docs.oracle.com/javase/8/docs/api/java/nio/ByteBuffer.html#class%20in%20java.nio) 对象不适合多个并发线程使用。

用于异步读取、写入和操作文件的类是 `AsynchronousFileChannel`。答案是 true：异步文件通道在文件中没有当前位置。

如果你没有向 `AsynchronousFileChannel` 的 [`AsynchronousFileChannel`](https://docs.oracle.com/javase/8/docs/api/java/nio/channels/AsynchronousFileChannel.html#class%20in%20java.nio.channels) `open(` [`Path`](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Path.html#interface%20in%20java.nio.file) `file,` [`OpenOption`](https://docs.oracle.com/javase/8/docs/api/java/nio/file/OpenOption.html#interface%20in%20java.nio.file) `... options)` 方法传递任何选项，该方法会尝试打开一个已存在的文件。

你可以通过调用每个抽象类的两个 `open()` 方法之一来获取 `AsynchronousServerSocketChannel` 对象或 `AsynchronousSocketChannel` 对象。`AsynchronousServerSocketChannel` 文档中记录的套接字选项是 `SO_RCVBUF` 和 `SO_REUSEADDR`。

在获取 `AsynchronousServerSocketChannel` 对象后，你必须先调用 `AsynchronousServerSocketChannel` 的 [`bind`](https://docs.oracle.com/javase/8/docs/api/java/nio/channels/AsynchronousServerSocketChannel.html#bind-java.net.SocketAddress-int-) `()` 方法之一，将此通道绑定到本地地址并配置其监听连接，然后才能调用其 `accept()` 方法。

在获取 `AsynchronousSocketChannel` 对象后，你必须先调用 `AsynchronousSocketChannel` 的 `connect()` 方法之一，将其连接到[异步服务器套接字通道](https://docs.oracle.com/javase/8/docs/api/java/nio/channels/AsynchronousServerSocketChannel.html#class%20in%20java.nio.channels)的套接字，然后才能执行读取和写入操作。

你可以通过调用 `AsynchronousSocketChannel` 的 `getRemoteAddress()` 方法来确定异步套接字通道是否连接到异步服务器套接字通道，当通道的套接字未连接时，该方法返回 `null`。答案是 true：异步套接字通道适合多个并发线程使用。

`AsynchronousChannelGroup` 类的目的是为了资源共享而描述一组异步通道。一个组使用线程池来提交任务。用于配置默认组的系统属性是 `java.nio.channels.DefaultThreadPool.threadFactory` 和 `java.nio.channels.DefaultThreadPool.initialSize`。

`void shutdownNow()` 方法用于强制关闭该组。除了有序关闭所执行的操作外，`shutdownNow()` 会关闭组中所有打开的通道，就像调用了每个通道的 `close()` 方法一样。答案是 false：`AsynchronousFileChannel` 不属于组。然而，它们与一个用于提交任务的线程池相关联，以处理 I/O 事件并分派给完成处理器，这些处理器会消耗通道上 I/O 操作的结果。

清单 A-12 展示了第 13 章中要求的 `Copy` 应用程序。

清单 A-12. 异步地将源文件复制到目标文件

`import java.io.IOException;`

`import java.nio.ByteBuffer;`

`import java.nio.channels.AsynchronousFileChannel;`

`import java.nio.channels.CompletionHandler;`

`import java.nio.file.Paths;`

`import static java.nio.file.StandardOpenOption.*;`

`public class Copy`

`{`

`final static Thread THDMAIN = Thread.currentThread();`

`public static void main(String[] args) throws IOException`

`{`

`if (args.length != 2)`

`{`

`System.err.println("usage: java Copy src dest");`

`return;`

`}`

`copy(AsynchronousFileChannel.open(Paths.get(args[0])),`

`AsynchronousFileChannel.open(Paths.get(args[1]), WRITE,`

`TRUNCATE_EXISTING, CREATE));`

`try`

`{`

`THDMAIN.join();`

`}`

`catch (InterruptedException ie)`

`{`

`System.out.println("done");`

`}`

`}`

`public static void copy(AsynchronousFileChannel chSrc,`

`AsynchronousFileChannel chDest)`

`{`

`ByteBuffer buffer = ByteBuffer.allocate(8192);`

`class ReadCompletionHandler implements CompletionHandler<Integer,`

`Integer>`

`{`

`@Override`

`public void completed(Integer result, Integer pos)`

`{`

`if (result == -1)`

`{`

`THDMAIN.interrupt();`

`return;`

`}`

`buffer.flip();`

`chDest.write(buffer, pos, pos + result,`

`new CompletionHandler<Integer, Integer>()`

`{`

`@Override`

`public void completed(Integer result,`

`Integer newPos)`

`{`

`buffer.compact();`

`chSrc.read(buffer, newPos, newPos,`

`ReadCompletionHandler.this);`

`}`

`@Override`

`public void failed(Throwable t, Integer pos)`

`{`

`System.out.println("write failure");`

`THDMAIN.interrupt();`

`}`

`});`

`}`

`@Override`

`public void failed(Throwable t, Integer pos)`

`{`

`System.out.println("read failure");`

`THDMAIN.interrupt();`

`}`

`}`

`chSrc.read(buffer, 0, 0, new ReadCompletionHandler());`

`}`

`}`



## 第 14 章：完善套接字通道功能

NIO.2 为完善套接字通道功能提供的能力包括：在 `DatagramChannel`、`ServerSocketChannel` 和 `SocketChannel` 类中支持绑定和选项配置，以及支持基于通道的多播。JDK 7 通过引入 `NetworkChannel` 接口，克服了套接字通道与套接字 API 之间违反直觉的混合使用问题，该接口由 `DatagramChannel`、`ServerSocketChannel`、`SocketChannel`、`AsynchronousServerSocketChannel` 和 `AsynchronousSocketChannel` 类实现。`SocketChannel` 的 `shutdownInput()` 和 `shutdownOutput()` 方法抛出的异常包括 `NotYetConnectedException`、`ClosedChannelException` 和 `IOException`。JDK 7 通过引入一个扩展了 `NetworkChannel` 的 `MulticastChannel` 接口、引入一个 `MembershipKey` 类，以及向 `DatagramChannel` 类添加一个 `open(ProtocolFamily)` 方法，从而支持多播。答案是 false：[`MembershipKey`](https://docs.oracle.com/javase/8/docs/api/java/nio/channels/MembershipKey.html#class%20in%20java.nio.channels) 的 `join(` [`InetAddress`](https://docs.oracle.com/javase/8/docs/api/java/net/InetAddress.html#class%20in%20java.net) `group,` [`NetworkInterface`](https://docs.oracle.com/javase/8/docs/api/java/net/NetworkInterface.html#class%20in%20java.net) `ni,` [`InetAddress`](https://docs.oracle.com/javase/8/docs/api/java/net/InetAddress.html#class%20in%20java.net) `source)` 方法执行的是包含模式过滤。清单 A-13 展示了第 14 章中要求的 `EnumNI` 应用程序。清单 A-13\. 枚举网络接口并输出其名称

`import java.net.NetworkInterface;`

`import java.util.Enumeration;`

`public class EnumNI`

`{`

`public static void main(String[] args) throws Exception`

`{`

`Enumeration<NetworkInterface> e;`

`e = NetworkInterface.getNetworkInterfaces();`

`while (e.hasMoreElements())`

`System.out.println(e.nextElement());`

`}`

`}`

清单 A-14 展示了第 15 章中要求的 `ChannelClient` 应用程序。清单 A-14\. 退出组播并关闭通道

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

`for (int i = 0; i < 10; i++)`

`{`

`dc.receive(response);`

`response.flip();`

`while (response.hasRemaining())`

`System.out.print((char) response.get());`

`System.out.println();`

`response.clear();`

`}`

`key.drop();`

`dc.close();`

`}`

`}`

清单 A-15 展示了第 14 章中要求的 `StockServer` 应用程序。清单 A-15\. 广播股票波动的股价

`import java.io.IOException;`

`import java.net.InetAddress;`

`import java.net.InetSocketAddress;`

`import java.net.NetworkInterface;`

`import java.net.StandardProtocolFamily;`

`import java.net.StandardSocketOptions;`

`import java.nio.ByteBuffer;`

`import java.nio.channels.DatagramChannel;`

`import java.nio.channels.MembershipKey;`

`public class StockServer`

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

`InetSocketAddress isa = new InetSocketAddress(group, PORT);`

`float openPrice = 37.4f;`

`float lowPrice = 37.22f;`

`float highPrice = 37.48f;`

`float closePrice = 37.41f;`

`ByteBuffer payload = ByteBuffer.allocate(16);`

`while (true)`

`{`

`// 波动因子为 -0.5 到接近 +0.5`

`float fluctuation = (float) (Math.random() - 0.5);`

`lowPrice += fluctuation;`

`lowPrice = Math.max(lowPrice, -lowPrice);`

`highPrice += fluctuation;`

`highPrice = Math.max(highPrice, -lowPrice);`

`closePrice += fluctuation;`

`closePrice = Math.max(closePrice, -lowPrice);`

`payload.putFloat(openPrice);`

`payload.putFloat(lowPrice);`

`payload.putFloat(highPrice);`

`payload.putFloat(closePrice);`

`payload.flip();`

`dc.send(payload, isa);`

`payload.clear();`

`openPrice = closePrice;`

`}`

`}`

`}`

清单 A-16 展示了第 14 章中要求的 `StockClient` 应用程序。清单 A-16\. 接收股票波动的股价

`import java.io.IOException;`

`import java.net.InetAddress;`

`import java.net.InetSocketAddress;`

`import java.net.NetworkInterface;`

`import java.net.StandardProtocolFamily;`

`import java.net.StandardSocketOptions;`

`import java.nio.ByteBuffer;`

`import java.nio.channels.DatagramChannel;`

`import java.nio.channels.MembershipKey;`

`public class StockClient`

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

`ByteBuffer response = ByteBuffer.allocate(16);`

`while (true)`

`{`

`dc.receive(response);`

`response.flip();`

`System.out.println("开盘价: " + response.getFloat());`

`System.out.println("最低价: " + response.getFloat());`

`System.out.println("最高价: " + response.getFloat());`

`System.out.println("收盘价: " + response.getFloat());`

`response.clear();`

`}`

`}`

`}`



