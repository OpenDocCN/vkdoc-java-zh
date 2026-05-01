# 2. 文件

电子补充材料 本章的在线版本（doi:[10.​1007/​978-1-4842-1565-4_​2](http://dx.doi.org/10.1007/978-1-4842-1565-4_2)）包含补充材料，仅供授权用户使用。

应用程序经常与文件系统交互，文件系统通常实现为从根目录开始的文件和目录层次结构。Java 虚拟机（JVM）运行的操作系统通常至少支持一个文件系统。例如，Unix/Linux 将所有已挂载（已连接并准备好）的磁盘合并到一个虚拟文件系统中。相比之下，Windows 为每个活动的磁盘驱动器关联一个单独的文件系统。Java 通过其具体的 `java.io.File` 类提供对底层操作系统可用文件系统的访问，本章将对此进行探讨。



## 构造 File 实例

`File` 类的实例包含文件或目录路径的抽象表示（一种定位和标识文件系统对象的紧凑映射）。要创建 `File` 实例，可以调用诸如 `File(String path)` 之类的构造方法，该方法会创建一个存储 `path` 字符串的 `File` 实例：

`File file1 = new File("/x/y");`

`File file2 = new File("C:\\temp\\x.dat");`

第一行假设在 Unix/Linux 操作系统下，路径以根目录符号 `/` 开头，后跟目录名 `x`、分隔符 `/` 以及文件或目录名 `y`。（该路径在 Windows 系统下也能工作，Windows 会假定此路径从当前驱动器的根目录开始。）

注意

操作系统相关的分隔符（例如 Windows 的反斜杠 [`\`] 字符）会出现在路径的连续名称之间。

第二行假设在 Windows 操作系统下，路径以驱动器标识符 `C:` 开头，后跟根目录符号 `\`、目录名 `temp`、分隔符 `\` 以及文件名 `x.dat`（尽管 `x.dat` 也可能指向一个目录）。（在 Windows 上也可以使用正斜杠 [`/`]。）

警告

字符串字面量中出现的反斜杠字符务必使用双反斜杠，尤其是在指定路径时；否则，你可能会引入错误或收到编译器错误消息。例如，我在第二条语句中使用了双反斜杠，以表示反斜杠本身而非制表符（`\t`），并避免编译器报错（`\x` 是非法的）。

每条语句中的路径都是绝对路径，即从根目录符号开始的路径；无需其他信息即可定位其所指的文件或目录。相比之下，相对路径不以根目录符号开头；它需要通过从其他路径获取的信息来解释。

注意

`java.io` 包中的类默认根据当前用户目录（也称为工作目录）来解析相对路径，该目录由系统属性 `user.dir` 标识，通常是启动 JVM 的目录。（你可以通过调用 `java.lang.System` 类的 `getProperty()` 方法来获取系统属性值。）

`File` 实例通过存储抽象路径来包含文件和目录路径的抽象表示（这些文件或目录在文件系统中可能存在也可能不存在），抽象路径提供了与操作系统无关的层次化路径视图。相比之下，用户界面和操作系统则使用与操作系统相关的路径字符串来命名文件和目录。

抽象路径由一个可选的、与操作系统相关的前缀字符串（例如磁盘驱动器标识符、Unix/Linux 根目录的“/”，或 Windows 通用命名约定（UNC）路径的“\\”）以及零个或多个字符串名称序列组成。抽象路径中的第一个名称可以是目录名，或者对于 Windows UNC 路径而言，可以是主机名。后续每个名称都表示一个目录；最后一个名称可以表示目录或文件。空的抽象路径没有前缀，也没有名称序列。

路径字符串与抽象路径之间的相互转换本质上是与操作系统相关的。当路径字符串转换为抽象路径时，该字符串中的名称可以由默认的名称分隔符或底层操作系统支持的任何其他名称分隔符分隔。当抽象路径转换为路径字符串时，每个名称之间由单个默认名称分隔符分隔。

注意

默认的名称分隔符由系统属性 `file.separator` 定义，并可通过 `File` 类的 `public static` 字段 [`separator`](http://docs.oracle.com/javase/7/docs/api/java/io/File.html#separator) 和 [`separatorChar`](http://docs.oracle.com/javase/7/docs/api/java/io/File.html#separatorChar) 获取——第一个字段将字符存储在 `java.lang.String` 实例中，第二个字段将其存储为 `char` 值。

`File` 提供了其他构造方法来实例化该类。例如，以下构造方法将父路径和子路径合并为组合路径，并存储在 `File` 对象中：

*   `File(String parent, String child)` 根据父路径字符串和子路径字符串创建一个新的 `File` 实例。
*   `File(File parent, String child)` 根据父路径 `File` 实例和子路径字符串创建一个新的 `File` 实例。

每个构造方法的 `parent` 参数接收一个父路径，该路径包含除最后一个名称（由 `child` 指定）之外的所有路径组件。以下语句通过第一个构造方法演示了这一概念：

`File file3 = new File("prj/books/", "io");`

该构造方法将父路径 `prj/books/` 与子路径 `io` 合并为路径 `prj/books/io`。（如果我将父路径指定为 `prj/books`，构造方法会在 `books` 之后添加分隔符。）

提示

由于 `File(String path)`、`File(String parent, String child)` 和 `File(File parent, String child)` 不会检测无效的路径参数（除了当 `path` 或 `child` 为 `null` 时会抛出 `java.lang.NullPointerException`），因此在指定路径时必须格外小心。你应该尽量只指定那些在应用程序将要运行的所有操作系统上都有效的路径。例如，不要在路径中硬编码驱动器标识符（如 `C:`），而应使用 `listRoots()` 返回的根路径，我稍后会讨论这个方法。更好的做法是，保持路径相对于当前用户/工作目录（由 `user.dir` 系统属性返回）。



## 了解存储的抽象路径

获取 `File` 对象后，你可以通过调用表 2-1 中描述的方法来查询其存储的抽象路径。

表 2-1. 用于了解存储抽象路径的 `File` 方法

| 方法 | 描述 |
| --- | --- |
| `File getAbsoluteFile()` | 返回此 `File` 对象抽象路径的绝对形式。此方法等同于 `new File(this.getAbsolutePath())`。 |
| `String getAbsolutePath()` | 返回此 `File` 对象抽象路径的绝对路径字符串。当路径已经是绝对路径时，返回的路径字符串如同调用了 `getPath()`。当它是空抽象路径时，返回当前用户目录（通过 `user.dir` 标识）的路径字符串。否则，抽象路径将以操作系统相关的方式解析。在 Unix/Linux 操作系统上，相对路径通过相对于当前用户目录解析而变为绝对路径。在 Windows 操作系统上，路径通过相对于路径指定的驱动器的当前目录（如果没有驱动器，则相对于当前用户目录）解析而变为绝对路径。 |
| `File getCanonical` `File()` | 返回此 `File` 对象抽象路径的规范形式（尽可能简单、绝对且唯一）。当发生 I/O 错误时，此方法会抛出 `java.io.IOException`（创建规范路径可能需要查询文件系统）；它等同于 `new File(this.getCanonicalPath())`。 |
| `String getCanonicalPath()` | 返回此 `File` 对象抽象路径的规范路径字符串。此方法首先在必要时将此路径转换为绝对形式（如同调用 `getAbsolutePath()`），然后以操作系统相关的方式将其映射到唯一形式。这样做通常涉及从路径中删除冗余名称（例如“`.`”和“`..`”）、解析符号链接（在 Unix/Linux 操作系统上）以及将驱动器号转换为标准大小写（在 Windows 操作系统上）。当发生 I/O 错误时，此方法会抛出 `IOException`（创建规范路径可能需要查询文件系统）。 |
| `String getName()` | 返回此 `File` 对象抽象路径所表示的文件名或目录名。此名称是路径名称序列中的最后一个。当路径的名称序列为空时，返回空字符串。 |
| `String getParent()` | 返回此 `File` 对象路径的父路径字符串，如果此路径未指定父目录，则返回 null。 |
| `File getParent` `File()` | 返回一个 `File` 对象，该对象存储此 `File` 对象抽象路径的父抽象路径；如果父路径不是目录，则返回 null。 |
| `String getPath()` | 将此 `File` 对象的抽象路径转换为路径字符串，其中序列中的名称由存储在 `File` 的 `separator` 字段中的字符分隔。返回结果路径字符串。 |
| `boolean isAbsolute()` | 如果此 `File` 对象的抽象路径是绝对路径，则返回 true；否则，如果是相对路径，则返回 false。绝对路径的定义取决于系统。对于 Unix/Linux 操作系统，如果路径的前缀是“`/`”，则该路径是绝对路径。对于 Windows 操作系统，如果路径的前缀是后跟“`\`”的驱动器说明符，或者前缀是“`\\`”，则该路径是绝对路径。 |
| `String toString()` | 是 `getPath()` 的同义词。 |

表 2-1 提到了 `IOException`，它是描述各种 I/O 错误（例如 `java.io.FileNotFoundException`）的那些异常类的常见异常超类。

清单 2-1 使用其路径命令行参数实例化 `File`，并调用表 2-1 中描述的一些 `File` 方法来了解此路径。

清单 2-1. 获取抽象路径信息

`import java.io.File;`

`import java.io.IOException;`

`public class PathInfo`

`{`

`public static void main(final String[] args) throws IOException`

`{`

`if (args.length != 1)`

`{`

`System.err.println("usage: java PathInfo path");`

`return;`

`}`

`File file = new File(args[0]);`

`System.out.println("Absolute path = " + file.getAbsolutePath());`

`System.out.println("Canonical path = " + file.getCanonicalPath());`

`System.out.println("Name = " + file.getName());`

`System.out.println("Parent = " + file.getParent());`

`System.out.println("Path = " + file.getPath());`

`System.out.println("Is absolute = " + file.isAbsolute());`

`}`

`}`

按如下方式编译清单 2-1：

`javac PathInfo.java`

按如下方式运行生成的应用程序：

`java PathInfo .`

句点代表我 Windows 7 操作系统上的当前目录；请使用你自己的等效目录。我观察到以下输出：

`Absolute path = C:\prj\books\io\ch02\code\PathInfo\.`

`Canonical path = C:\prj\books\io\ch02\code\PathInfo`

`Name = .`

`Parent = null`

`Path = .`

`Is absolute = false`

此输出显示规范路径不包含句点。它还显示没有父路径，并且该路径是相对路径。

继续，指定 `java PathInfo C:\reports\2015\..\2014\February`。你应该会观察到以下输出：

`Absolute path = C:\reports\2015\..\2014\February`

`Canonical path = C:\reports\2014\February`

`Name = February`

`Parent = C:\reports\2015\..\2014`

`Path = C:\reports\2015\..\2014\February`

`Is absolute = true`

此输出显示规范路径不包含 `2015`。它还显示该路径是绝对路径。

最后，指定 `java PathInfo ""` 以获取空路径的信息。我观察到以下输出：

`Absolute path = C:\prj\books\io\ch02\code\PathInfo`

`Canonical path = C:\prj\books\io\ch02\code\PathInfo`

`Name =`

`Parent = null`

`Path =`

`Is absolute = false`

输出显示 `getName()` 和 `getPath()` 返回空字符串 (`""`)，因为空路径是空的。此外，`C:` 是默认驱动器。



## 了解路径对应的文件或目录

你可以通过调用表 2-2 中描述的方法来查询文件系统，以了解由 `File` 对象存储路径所表示的文件或目录的信息。

表 2-2. 用于了解文件或目录信息的 `File` 方法

| 方法 | 描述 |
| --- | --- |
| `boolean exists()` | 当且仅当此 `File` 对象的抽象路径名所表示的文件或目录存在时，返回 true。 |
| `boolean isDirectory()` | 当此 `File` 对象的抽象路径名指向一个已存在的目录时，返回 true。 |
| `boolean isFile()` | 当此 `File` 对象的抽象路径名指向一个已存在的普通文件时，返回 true。当文件不是目录且满足其他操作系统相关的条件时，该文件即为普通文件。例如，它不是符号链接或命名管道。任何由 Java 应用程序创建的非目录文件都保证是普通文件。 |
| `boolean isHidden()` | 当此 `File` 对象的抽象路径名所表示的文件是隐藏文件时，返回 true。隐藏文件的确切定义取决于操作系统。在 Unix/Linux 操作系统上，文件名以句点字符开头时即为隐藏文件。在 Windows 操作系统上，文件在文件系统中被标记为隐藏时即为隐藏文件。 |
| `long lastModified()` | 返回此 `File` 对象的抽象路径名所表示的文件最后被修改的时间，如果文件不存在或在此方法调用期间发生 I/O 错误，则返回 `0`。返回值以毫秒为单位，从 Unix 纪元（1970 年 1 月 1 日 00:00:00 GMT）开始计算。 |
| `long length()` | 返回此 `File` 对象的抽象路径名所表示的文件长度。当路径名表示一个目录时，返回值未指定；当文件不存在时，返回 `0`。 |

清单 2-2 使用其路径命令行参数实例化 `File`，并调用表 2-2 中描述的所有 `File` 方法，以了解路径对应的文件/目录信息。

清单 2-2. 获取文件/目录信息

`import java.io.File;`

`import java.io.IOException;`

`import java.util.Date;`

`public class FileDirectoryInfo`

`{`

   `public static void main(final String[] args) throws IOException`

   `{`

      `if (args.length != 1)`

      `{`

         `System.err.println("usage: java FileDirectoryInfo pathname");`

         `return;`

      `}`

      `File file = new File(args[0]);`

      `System.out.println("About " + file + ":");`

      `System.out.println("Exists = " + file.exists());`

      `System.out.println("Is directory = " + file.isDirectory());`

      `System.out.println("Is file = " + file.isFile());`

      `System.out.println("Is hidden = " + file.isHidden());`

      `System.out.println("Last modified = " +`

                         `new Date(file.lastModified()));`

      `System.out.println("Length = " + file.length());`

   `}`

`}`

按如下方式编译 清单 2-2：

`javac FileDirectoryInfo.java`

按如下方式运行生成的应用程序：

`java FileDirectoryInfo x.dat`

假设存在一个名为 `x.dat` 的三字节文件，你应该会看到类似如下的输出：

`About x.dat:`

`Exists = true`

`Is directory = false`

`Is file = true`

`Is hidden = false`

`Last modified = Sat Jul 25 15:49:41 CDT 2015`

`Length = 3`

## 列出文件系统根目录

`File` 声明了 `File[] listRoots()` 类方法，用于将可用文件系统的根目录（根）作为 `File` 对象数组返回。

注意

可用文件系统根目录的集合会受到操作系统级操作的影响，例如插入或弹出可移动介质，以及断开或卸载物理或虚拟磁盘驱动器。

清单 2-3 展示了一个 `DumpRoots` 应用程序，它使用 `listRoots()` 获取可用文件系统根目录的数组，然后输出该数组的内容。

清单 2-3. 将可用文件系统根目录转储到标准输出

`import java.io.File;`

`public class DumpRoots`

`{`

   `public static void main(String[] args)`

   `{`

      `File[] roots = File.listRoots();`

      `for (File root: roots)`

         `System.out.println(root);`

   `}`

`}`

按如下方式编译 清单 2-3：

`javac DumpRoots.java`

按如下方式运行生成的应用程序：

`java DumpRoots`

当我在 Windows 7 操作系统上运行此应用程序时，收到以下输出，显示了四个可用的根目录：

`C:\`

`D:\`

`E:\`

`F:\`

如果我在 Unix 或 Linux 操作系统上运行 `DumpRoots`，则会收到一行输出，内容为虚拟文件系统根目录（`/`）。



## 获取磁盘空间信息

分区是操作系统中为文件系统划分的特定存储区域。获取分区的可用空间量对于安装程序和其他应用程序来说非常重要。在 Java 6 出现之前，实现此功能的唯一可移植方法是尝试创建不同大小的文件来进行猜测。

Java 6 为 `File` 类新增了 `long getFreeSpace()`、`long getTotalSpace()` 和 `long getUsableSpace()` 方法，这些方法返回由 `File` 实例的抽象路径所描述的分区的空间信息：

*   `long getFreeSpace()` 返回此 `File` 对象抽象路径所标识分区中未分配的字节数；如果抽象路径未指定分区，则返回零。
*   `long getTotalSpace()` 返回此 `File` 对象抽象路径所标识分区的大小（以字节为单位）；如果抽象路径未指定分区，则返回零。
*   `long getUsableSpace()` 返回当前 JVM 在此 `File` 对象抽象路径所标识分区上可用的字节数；如果抽象路径未指定分区，则返回零。

尽管 `getFreeSpace()` 和 `getUsableSpace()` 看起来等效，但它们在以下方面有所不同：与 `getFreeSpace()` 不同，`getUsableSpace()` 会检查写入权限和其他操作系统限制，从而提供更准确的估算。

注意

`getFreeSpace()` 和 `getUsableSpace()` 方法返回的是一个提示（而非保证），即 Java 应用程序可以使用全部（或大部分）未分配或可用的字节。这些值只是提示，因为 JVM 外部运行的程序可能会分配分区空间，导致实际的未分配和可用值低于这些方法返回的值。

清单 2-4 展示了一个演示这些方法的应用程序。在获取所有可用文件系统根目录的数组后，该应用程序获取并输出该数组标识的每个分区的可用空间、总空间和可用空间。

清单 2-4\. 输出所有分区的可用空间、可用空间和总空间

`import java.io.File;`

`public class PartitionSpace`

`{`

   `public static void main(String[] args)`

   `{`

      `File[] roots = File.listRoots();`

      `for (File root: roots)`

      `{`

         `System.out.println("Partition: " + root);`

         `System.out.println("Free space on this partition = " +`

                            `root.getFreeSpace());`

         `System.out.println("Usable space on this partition = " +`

                            `root.getUsableSpace());`

         `System.out.println("Total space on this partition = " +`

                            `root.getTotalSpace());`

         `System.out.println("***");`

      `}`

   `}`

`}`

按如下方式编译清单 2-4：

`javac PartitionSpace.java`

按如下方式运行生成的应用程序：

`java PartitionSpace`

在我的 Windows 7 机器上运行，该机器有一个指定为 `C:` 的硬盘驱动器、一个指定为 `D:` 的 DVD 驱动器、一个指定为 `E:` 的外部硬盘驱动器和一个指定为 `F:` 的闪存驱动器，我观察到以下输出（通常 `C:`、`E:` 和 `F:` 上的可用/可用空间量不同）：

`Partition: C:\`

`Free space on this partition = 143271129088`

`Usable space on this partition = 143271129088`

`Total space on this partition = 499808989184`

`***`

`Partition: D:\`

`Free space on this partition = 0`

`Usable space on this partition = 0`

`Total space on this partition = 0`

`***`

`Partition: E:\`

`Free space on this partition = 733418569728`

`Usable space on this partition = 733418569728`

`Total space on this partition = 1000169533440`

`***`

`Partition: F:\`

`Free space on this partition = 33728192512`

`Usable space on this partition = 33728192512`

`Total space on this partition = 64021835776`

`***`

## 列出目录

`File` 声明了五个方法，用于返回位于由 `File` 对象抽象路径标识的目录中的文件和目录的名称。表 2-3 描述了这些方法。

表 2-3.

用于获取目录内容的 `File` 方法

| 方法 | 描述 |
| --- | --- |
| `String[] list()` | 返回一个可能为空的字符串数组，这些字符串命名此 `File` 对象抽象路径所表示的目录中的文件和目录。如果路径不表示目录，或者发生 I/O 错误，则此方法返回 null。否则，它返回一个字符串数组，目录中的每个文件或目录对应一个字符串。结果中不包含表示目录本身及其父目录的名称。每个字符串都是文件名，而不是完整路径。此外，不保证结果数组中的名称字符串按字母顺序或任何其他顺序出现。 |
| `String[] list(FilenameFilter filter)` | 一个便捷方法，用于调用 `list()` 并仅返回满足 `filter` 的 `String`。 |
| `File[] listFiles()` | 一个便捷方法，用于调用 `list()`，将其 `String` 数组转换为 `File` 数组，并返回 `File` 数组。 |
| `File[] listFiles(FileFilter filter)` | 一个便捷方法，用于调用 `list()`，将其 `String` 数组转换为 `File` 数组，但仅针对满足 `filter` 的 `String`，并返回 `File` 数组。 |
| `File[] listFiles(FilenameFilter filter)` | 一个便捷方法，用于调用 `list()`，将其 `String` 数组转换为 `File` 数组，但仅针对满足 `filter` 的 `String`，并返回 `File` 数组。 |

重载的 `list()` 方法返回表示文件和目录名称的 `String` 数组。第二个方法允许您通过基于 `java.io.FilenameFilter` 的过滤器对象仅返回感兴趣的名称（例如，仅返回以 `.txt` 扩展名结尾的名称）。

`FilenameFilter` 接口声明了一个 `boolean accept(File dir, String name)` 方法，该方法为位于 `File` 对象路径标识的目录中的每个文件/目录调用：

*   `dir` 标识路径的父部分（目录路径）。
*   `name` 标识路径的最终目录名称或文件名部分。

`accept()` 方法使用传递给这些参数的参数来确定文件或目录是否满足其可接受的标准。如果文件/目录名称应包含在返回的数组中，则返回 true；否则，此方法返回 false。

清单 2-5 展示了一个 `Dir`(ectory) 应用程序，它使用 `list(FilenameFilter)` 仅获取以特定扩展名结尾的名称。

清单 2-5\. 列出特定名称

`import java.io.File;`

`import java.io.FilenameFilter;`

`public class Dir`

`{`

`public static void main(final String[] args)`

`{`

`if (args.length != 2)`

`{`

`System.err.println("usage: java Dir dirpath ext");`

`return;`

`}`

`File file = new File(args[0]);`

`FilenameFilter fnf = new FilenameFilter()`

`{`

`@Override`

`public boolean accept(File dir, String name)`

`{`

`return name.endsWith(args[1]);`

`}`

`};`

`String[] names = file.list(fnf);`

`for (String name: names)`

`System.out.println(name);`

`}`

`}`

按如下方式编译清单 2-5：

`javac Dir.java`

假设是 Windows 系统，按如下方式运行生成的应用程序：

`java Dir C:\windows exe`

我观察到以下输出，其中包含具有 `.exe` 扩展名的文件系统对象：

`bfsvc.exe`

`explorer.exe`

`fveupdate.exe`

`HelpPane.exe`

`hh.exe`

`IsUninst.exe`

`kindlegen.exe`

`notepad.exe`

`regedit.exe`

`splwow64.exe`

`twunk_16.exe`

`twunk_32.exe`

`winhlp32.exe`

`write.exe`



重载的 `listFiles()` 方法返回 `File` 对象的数组。在大多数情况下，它们与对应的 `list()` 方法是对称的。然而，`listFiles(FileFilter)` 引入了一种不对称性。

`java.io.FileFilter` 接口声明了一个单一的 `boolean accept(String path)` 方法，该方法会针对由 `File` 对象路径所标识目录中的每个文件/目录被调用。传递给 `path` 的参数标识了该文件或目录的完整路径。

`accept()` 方法使用此参数来判断该文件或目录是否满足其可接受的标准。如果该文件/目录名称应包含在返回的数组中，则返回 true；否则，此方法返回 false。

注意

由于每个接口的 `accept()` 方法完成的任务相同，您可能会疑惑该使用哪个接口。如果您更倾向于将路径分解为目录和名称组件，请使用 `FilenameFilter`。但是，如果您更倾向于使用完整路径，请使用 `FileFilter`；您始终可以调用 `getParent()` 和 `getName()` 来获取这些组件。

## 创建/修改文件和目录

`File` 还声明了几个用于创建新文件和目录以及修改现有文件和目录的方法。表 2-4 描述了这些方法。

表 2-4.

用于创建新文件和操作现有文件及目录的 `File` 方法

| 方法 | 描述 |
| --- | --- |
| `boolean createNewFile()` | 当且仅当具有此名称的文件尚不存在时，原子性地创建一个由该 `File` 对象的抽象路径命名的新空文件。检查文件是否存在以及在文件不存在时创建文件是一个单一操作，该操作对于可能影响该文件的所有其他文件系统活动而言是原子性的。当指定的文件不存在且成功创建时，此方法返回 true；当指定的文件已存在时，返回 false。当发生 I/O 错误时，抛出 `IOException`。 |
| `static File createTempFile(String prefix, String suffix)` | 使用给定的 `prefix` 和 `suffix` 生成文件名，在默认临时文件目录中创建一个空文件。这个重载的类方法调用其三参数变体，将 `prefix`、`suffix` 和 `null` 传递给另一个方法，并返回另一个方法的返回值。 |
| `static File createTempFile(String prefix, String suffix, File directory)` | 使用给定的 `prefix` 和 `suffix` 生成文件名，在指定的 `directory` 中创建一个空文件。文件名以 `prefix` 指定的字符序列开头，以 `suffix` 指定的字符序列结尾；当 `suffix` 为 `null` 时，使用 “`.tmp`” 作为后缀。此方法在成功时返回所创建文件的路径。当 `prefix` 包含少于三个字符时，抛出 `java.lang.IllegalArgumentException`；当无法创建文件时，抛出 `IOException`。 |
| `boolean delete()` | 删除此 `File` 对象路径所表示的文件或目录。成功时返回 true；否则返回 false。如果路径表示一个目录，则该目录必须为空才能被删除。 |
| `void deleteOnExit()` | 请求在 JVM 终止时删除此 `File` 对象抽象路径所表示的文件或目录。对同一个 `File` 对象再次调用此方法无效。一旦请求删除，就无法取消该请求。因此，应谨慎使用此方法。 |
| `boolean mkdir()` | 创建由此 `File` 对象抽象路径命名的目录。成功时返回 true；否则返回 false。 |
| `boolean mkdirs()` | 创建由此 `File` 对象抽象路径命名的目录以及任何必要的中间目录。成功时返回 true；否则返回 false。 |
| `boolean renameTo(` `File dest)` | 将此 `File` 对象抽象路径所表示的文件重命名为 `dest`。成功时返回 true；否则返回 false。当 `dest` 为 `null` 时，此方法抛出 `NullPointerException`。此方法行为的许多方面依赖于操作系统。例如，`rename` 操作可能无法将文件从一个文件系统移动到另一个文件系统，该操作可能不是原子性的，或者当目标路径的文件已存在时可能不会成功。应始终检查返回值以确保 `rename` 操作成功。 |
| `boolean setLastModified(long time)` | 设置此 `File` 对象抽象路径所表示的文件或目录的最后修改时间。成功时返回 true；否则返回 false。当 `time` 为负数时，此方法抛出 `IllegalArgumentException`。所有操作系统都支持精确到秒的文件修改时间，但有些提供更高的精度。`time` 值将被截断以适配支持的精度。如果操作成功并且文件没有发生其他操作，则下一次调用 `lastModified()` 将返回传递给此方法的（可能被截断的）`time` 值。 |

假设您正在设计一个文本编辑器应用程序，用户将使用它打开一个文本文件并修改其内容。在用户明确将这些更改保存到文件之前，您希望文本文件保持不变。

由于用户不希望应用程序崩溃或计算机断电时丢失这些更改，您设计应用程序每隔几分钟将这些更改保存到一个临时文件中。这样，用户就有了更改的备份。

您可以使用重载的 `createTempFile()` 方法来创建临时文件。如果您没有指定存储此文件的目录，它将在由 `java.io.tmpdir` 系统属性标识的目录中创建。

在用户告诉应用程序保存或放弃更改后，您可能希望删除临时文件。`deleteOnExit()` 方法允许您注册一个要删除的临时文件；当 JVM 在没有崩溃/断电的情况下结束时，它会被删除。

清单 2-6 展示了一个 `TempFileDemo` 应用程序，用于试验 `createTempFile()` 和 `deleteOnExit()` 方法。

清单 2-6. 试验临时文件

`import java.io.File;`

`import java.io.IOException;`

`public class TempFileDemo`

`{`

`public static void main(String[] args) throws IOException`

`{`

`System.out.println(System.getProperty("java.io.tmpdir"));`

`File temp = File.createTempFile("text", ".txt");`

`System.out.println(temp);`

`temp.deleteOnExit();`

`}`

`}`

在输出临时文件的存储位置后，`TempFileDemo` 创建一个临时文件，其名称以 `text` 开头并以 `.txt` 扩展名结尾。`TempFileDemo` 接下来输出临时文件的名称，并注册该临时文件在应用程序成功终止时被删除。

按如下方式编译清单 2-6：

`javac TempFileDemo.java`

按如下方式运行生成的应用程序：

`java TempFileDemo`

我在一次运行 `TempFileDemo` 时观察到了以下输出（并且该文件在退出时消失了）：

`C:\Users\Owner\AppData\Local\Temp\`

`C:\Users\Owner\AppData\Local\Temp\text8621896953150462138.txt`



## 设置与获取权限

Java 1.2 为 `File` 类添加了一个 `boolean` 类型的 `setReadOnly()` 方法，用于将文件或目录标记为只读。然而，并未添加将文件或目录恢复为可写状态的方法。更重要的是，在 Java 6 到来之前，`File` 类没有提供任何方式来管理抽象路径的读取、写入和执行权限。

Java 6 为 `File` 类添加了 `boolean setExecutable(boolean executable)`、`boolean setExecutable(boolean executable, boolean ownerOnly)`、`boolean setReadable(boolean readable)`、`boolean setReadable(boolean readable, boolean ownerOnly)`、`boolean setWritable(boolean writable)` 以及 `boolean setWritable(boolean writable, boolean ownerOnly)` 方法。这些方法允许你为 `File` 对象抽象路径所标识的文件，设置所有者或所有用户的执行、读取和写入权限：

*   `boolean setExecutable(boolean executable, boolean ownerOnly)` 启用（向 `executable` 传入 `true`）或禁用（向 `executable` 传入 `false`）此抽象路径对其所有者（向 `ownerOnly` 传入 `true`）或所有用户（向 `ownerOnly` 传入 `false`）的执行权限。当文件系统不区分所有者与所有用户时，此权限始终适用于所有用户。操作成功时返回 `true`。当用户没有权限更改此抽象路径的访问权限，或者当 `executable` 为 `false` 且文件系统未实现执行权限时，返回 `false`。
*   `boolean setExecutable(boolean executable)` 是一个便捷方法，它调用前一个方法来为所有者设置执行权限。
*   `boolean setReadable(boolean readable, boolean ownerOnly)` 启用（向 `readable` 传入 `true`）或禁用（向 `readable` 传入 `false`）此抽象路径对其所有者（向 `ownerOnly` 传入 `true`）或所有用户（向 `ownerOnly` 传入 `false`）的读取权限。当文件系统不区分所有者与所有用户时，此权限始终适用于所有用户。操作成功时返回 `true`。当用户没有权限更改此抽象路径的访问权限，或者当 `readable` 为 `false` 且文件系统未实现读取权限时，返回 `false`。
*   `boolean setReadable(boolean readable)` 是一个便捷方法，它调用前一个方法来为所有者设置读取权限。
*   `boolean setWritable(boolean writable, boolean ownerOnly)` 启用（向 `writable` 传入 `true`）或禁用（向 `writable` 传入 `false`）此抽象路径对其所有者（向 `ownerOnly` 传入 `true`）或所有用户（向 `ownerOnly` 传入 `false`）的写入权限。当文件系统不区分所有者与所有用户时，此权限始终适用于所有用户。操作成功时返回 `true`。当用户没有权限更改此抽象路径的访问权限时，返回 `false`。
*   `boolean setWritable(boolean writable)` 是一个便捷方法，它调用前一个方法来为所有者设置写入权限。

除了这些方法，Java 6 还改进了 `File` 的 `boolean canRead()` 和 `boolean canWrite()` 方法，并引入了 `boolean canExecute()` 方法来返回抽象路径的访问权限。当抽象路径标识的文件或目录对象存在，并且相应的权限生效时，这些方法返回 `true`。例如，当抽象路径存在且应用程序拥有对该文件的写入权限时，`canWrite()` 返回 `true`。

`canRead()`、`canWrite()` 和 `canExecute()` 方法可用于实现一个简单的工具，用于识别已分配给任意文件或目录的权限。该工具的源代码如清单 2-7 所示。

**清单 2-7.** 检查文件或目录的权限

`import java.io.File;`

`public class Permissions`

`{`

`public static void main(String[] args)`

`{`

`if (args.length != 1)`

`{`

`System.err.println("usage: java Permissions filespec");`

`return;`

`}`

`File file = new File(args[0]);`

`System.out.println("Checking permissions for " + args[0]);`

`System.out.println("  Execute = " + file.canExecute());`

`System.out.println("  Read = " + file.canRead());`

`System.out.println("  Write = " + file.canWrite());`

`}`

`}`

按如下方式编译清单 2-7：

`javac Permissions.java`

假设当前目录下有一个名为 `x` 的可读且可执行（仅此权限）的文件，按如下方式运行生成的应用程序：

`java Permissions x`

你应该会看到以下输出：

`Checking permissions for x`

`Execute = true`

`Read = true`

`Write = false`



## 探索其他功能

最后，`File` 实现了 `java.lang.Comparable` 接口的 `compareTo()` 方法，并重写了 `equals()` 和 `hashCode()` 方法。表 2-5 描述了这些其他方法。

表 2-5.

`File` 的其他方法

| 方法 | 描述 |
| --- | --- |
| `int compareTo(File path)` | 按字典顺序比较两个路径。此方法定义的顺序取决于底层操作系统。对于 Unix/Linux 操作系统，比较路径时字母大小写是敏感的；对于 Windows 操作系统，字母大小写不敏感。当 `path` 的抽象路径等于此 `File` 对象的抽象路径时返回零，当此 `File` 对象的抽象路径小于 `path` 时返回负值，否则返回正值。要精确比较两个 `File` 对象，请在每个 `File` 对象上调用 `getCanonicalFile()`，然后比较返回的 `File` 对象。 |
| `boolean equals(Object obj))` | 将此 `File` 对象与 `obj` 进行相等性比较。抽象路径的相等性取决于底层操作系统。对于 Unix/Linux 操作系统，比较路径时字母大小写是敏感的；对于 Windows 操作系统，字母大小写不敏感。当且仅当 `obj` 不为 `null`，且是一个 `File` 对象，其抽象路径与此 `File` 对象的抽象路径表示相同的文件/目录时，返回 true。 |
| `int hashCode()` | 计算并返回此路径的哈希码。此计算取决于底层操作系统。在 Unix/Linux 操作系统上，路径的哈希码等于其路径字符串的哈希码与十进制值 `1234321` 的异或。在 Windows 操作系统上，哈希码是小写路径字符串的哈希码与十进制值 `1234321` 的异或。将路径字符串转换为小写时，不考虑当前区域设置（地理、政治或文化区域）。 |

清单 2-8 展示了一个演示 `compareTo()` 和 `getCanonicalFile()` 的应用程序。

清单 2-8. 比较文件

`import java.io.File;`

`import java.io.IOException;`

`public class Compare`

`{`

`public static void main(String[] args) throws IOException`

`{`

`if (args.length != 2)`

`{`

`System.err.println("usage: java Compare filespec1 filespec2");`

`return;`

`}`

`File file1 = new File(args[0]);`

`File file2 = new File(args[1]);`

`System.out.println(file1.compareTo(file2));`

`System.out.println(file1.getCanonicalFile()`

`.compareTo(file2.getCanonicalFile()));`

`}`

`}`

按如下方式编译清单 2-8：

`javac Compare.java`

假设在 Windows 环境下，按如下方式运行生成的应用程序：

`java Compare Compare.class .\Compare.class`

您应该会看到以下输出：

`53`

`0`

`53` 表示 `file1` 的抽象路径在字典顺序上大于 `file2` 的抽象路径。然而，当比较它们的规范表示时，这些抽象路径被认为是相同的（如 `0` 所示）。

练习

以下练习旨在测试您对第 2 章内容的理解：

`File` 类的用途是什么？  `File` 类的实例包含什么？  什么是路径？  绝对路径和相对路径有什么区别？  如何获取当前用户（也称为工作）目录？  定义父路径。  `File` 的构造函数会规范化其路径参数。规范化是什么意思？  如何获取默认的名称分隔符？  什么是规范路径？  `File` 的 `getParent()` 和 `getName()` 方法有什么区别？  判断正误：`File` 的 `exists()` 方法仅确定文件是否存在。  什么是普通文件？  `File` 的 `lastModified()` 方法返回什么？  `File` 的 `listRoots()` 方法的作用是什么？  判断正误：`File` 的 `list()` 方法返回一个 `String` 数组，其中每个条目是文件名而不是完整路径。  `FilenameFilter` 和 `FileFilter` 接口有什么区别？  判断正误：`File` 的 `createNewFile()` 方法不会检查文件是否存在，并在文件不存在时创建它，该操作相对于所有可能影响该文件的其他文件系统活动是原子的。  `File` 的 `createTempFile(String, String)` 方法在默认临时目录中创建一个临时文件。如何找到此目录？  临时文件应在应用程序退出后不再需要时删除（以避免弄乱文件系统）。如何确保在 JVM 正常结束时（不崩溃且未断电）删除临时文件？  `boolean canRead()`、`boolean canWrite()` 和 `boolean canExecute()` 方法中哪一个是由 Java 6 引入的？  如何精确比较两个 `File` 对象？  创建一个名为 `Touch` 的 Java 应用程序，用于将文件或目录的时间戳设置为当前时间。此应用程序的使用语法为：`java Touch pathname`。

## 总结

`File` 类提供了对底层操作系统可用文件系统的访问。每个 `File` 实例存储某个文件系统对象的抽象路径。各种 `File` 方法（例如 `void delete()`）会影响抽象路径所代表的文件系统对象。

您首先学习了如何构造 `File` 实例。然后探索了用于获取存储的抽象路径及其文件或目录信息、获取根目录列表和磁盘空间、列出目录、创建/修改文件/目录、设置/获取权限等方法。

第 3 章 介绍了经典 I/O 的 `java.io.RandomAccessFile` 类。

