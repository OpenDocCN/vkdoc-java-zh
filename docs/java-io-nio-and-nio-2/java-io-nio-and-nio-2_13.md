# 10. 字符集

电子补充材料 本章的在线版本 (doi:[10.​1007/​978-1-4842-1565-4_​10](http://dx.doi.org/10.1007/978-1-4842-1565-4_10)) 包含补充材料，仅供授权用户使用。

在第 5 章中，我简要介绍了字符集和字符编码的概念。我还提到了位于 `java.nio.charset` 包中的一些类型。在本章中，我将深入探讨这些主题并研究这个包。我还将讨论 `java.lang.String` 类中与这些主题相关的部分。

## 基础概念简要回顾

Java 使用 Unicode 来表示字符。（Unicode 是一个 16 位字符集标准 [实际上，更像是一种编码标准，因为某些字符由多个数值表示；每个数值称为一个码点]，其目标是将世界上所有重要的字符集映射到一个包罗万象的映射表中。）尽管 Unicode 使处理不同语言的字符变得更加容易，但它并不能自动化所有事情，你通常需要处理字符集。在深入探讨这个主题之前，你应该了解以下术语：

*   **字符**：一个有意义的符号。例如，“$”和“E”都是字符。这些符号在计算机时代之前就已存在。
*   **字符集**：一组字符。例如，大写字母 A 到 Z 可以被认为构成一个字符集。集合中的字符没有分配数值。它与 Unicode、ASCII、EBCDIC 或任何其他类型的字符集标准无关。
*   **编码字符集**：每个字符都被分配了一个唯一数值的字符集。诸如 US-ASCII 或 ISO-8859-1 之类的标准机构定义了从字符到数值的映射。
*   **字符编码方案**：对编码字符集的数值进行编码，以形成表示这些数值的字节序列的编码方式。某些编码是一对一的。例如，在 ASCII 中，字符 A 映射为整数 65，并编码为整数 65。对于其他一些映射，编码可以是一对一或一对多的。例如，UTF-8 对 Unicode 字符进行编码。每个数值小于 128 的字符被编码为单个字节，以与 ASCII 兼容。其他 Unicode 字符被编码为二到六字节的序列。更多信息请参见 [`www.ietf.org/rfc/rfc2279.txt`](http://www.ietf.org/rfc/rfc2279.txt)。
*   **字符集**：一个编码字符集与一个字符编码方案的组合。字符集由抽象的 `java.nio.charset.Charset` 类描述。

尽管 Unicode 被广泛使用且日益流行，但其他字符集标准也在使用中。由于操作系统在字节级别执行 I/O，并且文件将数据存储为字节序列，因此有必要在字节序列和编码到这些序列中的字符之间进行转换。`Charset` 和位于 `java.nio.charset` 包中的其他类负责处理此转换任务。

## 使用字符集

从 JDK 1.4 开始，Java 虚拟机 (JVM) 被要求支持一组标准的字符集，并且可以支持额外的字符集。它们还支持默认字符集，该字符集不必是标准字符集之一，并且在 JVM 启动时获取。表 10-1 标识并描述了标准字符集。

表 10-1. 标准字符集

| 字符集名称 | 描述 |
| --- | --- |
| `US-ASCII` | 七位 ASCII，构成美式英语字符集。在 Unicode 中也称为基本拉丁块。 |
| `ISO-8859-1` | 大多数欧洲语言使用的 8 位字符集。它是 ASCII 的超集，并包含大多数非英语欧洲字符。 |
| `UTF-8` | 一种面向 8 位字节的 Unicode 字符编码。字符被编码为一到六个字节。 |
| `UTF-16BE` | 一种使用大端序的 16 位 Unicode 编码。字符被编码为两个字节，高位八位先写入。 |
| `UTF-16LE` | 一种使用小端序的 16 位 Unicode 编码。字符被编码为两个字节，低位八位先写入。 |
| `UTF-16` | 一种 16 位编码，其字节顺序由可选的字节顺序标记决定。 |

字符集名称不区分大小写，并由互联网号码分配局 (IANA) 维护。表 10-1 中的名称包含在 IANA 的官方注册表中。

`UTF-16BE` 和 `UTF-16LE` 分别以大端序或小端序将每个字符编码为两个字节的序列。`UTF-16BE` 或 `UTF-16LE` 编码字节序列的解码器需要知道字节序列是如何编码的，或者有一种方法可以从编码数据中检测字节顺序。相比之下，`UTF-16` 依赖于出现在序列开头的字节顺序标记。如果此标记不存在，则解码将按照 `UTF-16BE`（Java 的本机字节顺序）进行。如果此标记等于 `\uFEFF`，则序列按照 `UTF-16BE` 解码。如果此标记等于 `\uFFFE`，则序列按照 `UTF-16LE` 解码。

每个字符集名称都与一个 `Charset` 对象关联，你可以通过调用该类的工厂方法之一来获取该对象。清单 10-1 展示了一个应用程序，演示了如何使用此类获取默认字符集和标准字符集，然后使用它们将字符编码为字节序列。

清单 10-1. 使用字符集将字符编码为字节序列

`import java.nio.ByteBuffer;`

`import java.nio.charset.Charset;`

`public class CharsetDemo`

`{`

`public static void main(String[] args)`

`{`

`String msg = "façade touché";`

`String[] csNames =`

`{`

`"US-ASCII",`

`"ISO-8859-1",`

`"UTF-8",`

`"UTF-16BE",`

`"UTF-16LE",`

`"UTF-16"`

`};`

`encode(msg, Charset.defaultCharset());`

`for (String csName: csNames)`

`encode(msg, Charset.forName(csName));`

`}`

`static void encode(String msg, Charset cs)`

`{`

`System.out.println("Charset: " + cs.toString());`

`System.out.println("Message: " + msg);`

`ByteBuffer buffer = cs.encode(msg);`

`System.out.println("Encoded: ");`

`for (int i = 0; buffer.hasRemaining(); i++)`

`{`

`int _byte = buffer.get() & 255;`

`char ch = (char) _byte;`

`if (Character.isWhitespace(ch) || Character.isISOControl(ch))`

`ch = ’\u0000’;`

`System.out.printf("%2d: %02x (%c)%n", i, _byte, ch);`

`}`

`System.out.println();`

`}`

`}`

清单 10-1 的 `main()` 方法首先创建一条由两个法语单词组成的消息和一个标准字符集集合的名称数组。接下来，它调用 `encode()` 方法，根据默认字符集对消息进行编码，该默认字符集通过调用 `Charset` 的 `Charset defaultCharset()` 工厂方法获得。



接着，`main()` 会针对每个标准字符集调用 `encode()` 方法。`Charset` 的 `Charset forName(String charsetName)` 工厂方法用于获取与 `charsetName` 对应的 `Charset` 实例。

注意

当指定的字符集名称非法时，`forName()` 会抛出 `java.nio.charset.IllegalCharsetNameException`；当 JVM 不支持所需的字符集时，则会抛出 `java.nio.charset.UnsupportedCharsetException`。

`encode()` 方法首先识别字符集和消息。然后，它调用 `Charset` 的 `ByteBuffer encode(String s)` 方法，返回一个新的 `java.nio.ByteBuffer` 对象，其中包含对 `s` 中的字符进行编码后得到的字节。

接下来，`main()` 会遍历字节缓冲区中的每个字节，并将每个字节转换为一个字符。它使用 `java.lang.Character` 的 `isWhitespace()` 和 `isISOControl()` 方法来判断该字符是否为空白字符或控制字符（两者均被视为不可打印），并将此类字符转换为 Unicode `0`（一个空字符串）。（例如，回车符或换行符会打乱输出。）

最后，字符的索引、其十六进制值以及字符本身会被打印到标准输出流。我选择使用 `System.out.printf()` 来完成此任务。你将在下一章了解此方法。

按如下方式编译清单 10-1：

`javac CharsetDemo.java`

按如下方式运行生成的应用程序：

`java CharsetDemo`

你应该会观察到以下输出：

`Charset: windows-1252`

`Message: façade touché`

`Encoded:`

`0: 66 (f)`

`1: 61 (a)`

`2: e7 (ç)`

`3: 61 (a)`

`4: 64 (d)`

`5: 65 (e)`

`6: 20 ( )`

`7: 74 (t)`

`8: 6f (o)`

`9: 75 (u)`

`10: 63 (c)`

`11: 68 (h)`

`12: e9 (é)`

`Charset: US-ASCII`

`Message: façade touché`

`Encoded:`

`0: 66 (f)`

`1: 61 (a)`

`2: 3f (?)`

`3: 61 (a)`

`4: 64 (d)`

`5: 65 (e)`

`6: 20 ( )`

`7: 74 (t)`

`8: 6f (o)`

`9: 75 (u)`

`10: 63 (c)`

`11: 68 (h)`

`12: 3f (?)`

`Charset: ISO-8859-1`

`Message: façade touché`

`Encoded:`

`0: 66 (f)`

`1: 61 (a)`

`2: e7 (ç)`

`3: 61 (a)`

`4: 64 (d)`

`5: 65 (e)`

`6: 20 ( )`

`7: 74 (t)`

`8: 6f (o)`

`9: 75 (u)`

`10: 63 (c)`

`11: 68 (h)`

`12: e9 (é)`

`Charset: UTF-8`

`Message: façade touché`

`Encoded:`

`0: 66 (f)`

`1: 61 (a)`

`2: c3 (Ã)`

`3: a7 (§)`

`4: 61 (a)`

`5: 64 (d)`

`6: 65 (e)`

`7: 20 ( )`

`8: 74 (t)`

`9: 6f (o)`

`10: 75 (u)`

`11: 63 (c)`

`12: 68 (h)`

`13: c3 (Ã)`

`14: a9 (©)`

`Charset: UTF-16BE`

`Message: façade touché`

`Encoded:`

`0: 00 ( )`

`1: 66 (f)`

`2: 00 ( )`

`3: 61 (a)`

`4: 00 ( )`

`5: e7 (ç)`

`6: 00 ( )`

`7: 61 (a)`

`8: 00 ( )`

`9: 64 (d)`

`10: 00 ( )`

`11: 65 (e)`

`12: 00 ( )`

`13: 20 ( )`

`14: 00 ( )`

`15: 74 (t)`

`16: 00 ( )`

`17: 6f (o)`

`18: 00 ( )`

`19: 75 (u)`

`20: 00 ( )`

`21: 63 (c)`

`22: 00 ( )`

`23: 68 (h)`

`24: 00 ( )`

`25: e9 (é)`

`Charset: UTF-16LE`

`Message: façade touché`

`Encoded:`

`0: 66 (f)`

`1: 00 ( )`

`2: 61 (a)`

`3: 00 ( )`

`4: e7 (ç)`

`5: 00 ( )`

`6: 61 (a)`

`7: 00 ( )`

`8: 64 (d)`

`9: 00 ( )`

`10: 65 (e)`

`11: 00 ( )`

`12: 20 ( )`

`13: 00 ( )`

`14: 74 (t)`

`15: 00 ( )`

`16: 6f (o)`

`17: 00 ( )`

`18: 75 (u)`

`19: 00 ( )`

`20: 63 (c)`

`21: 00 ( )`

`22: 68 (h)`

`23: 00 ( )`

`24: e9 (é)`

`25: 00 ( )`

`Charset: UTF-16`

`Message: façade touché`

`Encoded:`

`0: fe (þ)`

`1: ff (ÿ)`

`2: 00 ( )`

`3: 66 (f)`

`4: 00 ( )`

`5: 61 (a)`

`6: 00 ( )`

`7: e7 (ç)`

`8: 00 ( )`

`9: 61 (a)`

`10: 00 ( )`

`11: 64 (d)`

`12: 00 ( )`

`13: 65 (e)`

`14: 00 ( )`

`15: 20 ( )`

`16: 00 ( )`

`17: 74 (t)`

`18: 00 ( )`

`19: 6f (o)`

`20: 00 ( )`

`21: 75 (u)`

`22: 00 ( )`

`23: 63 (c)`

`24: 00 ( )`

`25: 68 (h)`

`26: 00 ( )`

`27: e9 (é)`

除了提供诸如前述 `ByteBuffer encode(String s)` 方法之类的编码方法外，`Charset` 还提供了一个互补的解码方法 `CharBuffer decode(ByteBuffer buffer)`。其返回类型是 `java.nio.CharBuffer`，因为字节序列会被解码为字符。

注意

`ByteBuffer encode(String s)` 是一个便捷方法，用于指定 `CharBuffer.wrap(s)` 并将结果传递给 `ByteBuffer encode(CharBuffer buffer)` 方法。

如果你深入研究 `Charset`，将会遇到以下一对方法：

*   `CharsetEncoder newEncoder()`
*   `CharsetDecoder newDecoder()`

这些方法执行实际的编码和解码工作。`Charset` 的 `encode()` 和 `decode()` 方法会委托给由 `newEncoder()` 和 `newDecoder()` 返回的 `java.nio.charset.CharsetEncoder` 和 `java.nio.charset.CharsetDecoder` 对象，并调用它们的 `encode()` 和 `decode()`（以及其他）方法。（为简洁起见，我不讨论 `CharsetEncoder` 和 `CharsetDecoder`。）



## 字符集与字符串类

`String` 类将字符串描述为字符序列。它声明了可以接收字节数组的构造方法。由于字节数组包含编码后的字符序列，因此需要字符集来解码它们。以下是部分与字符集配合使用的 `String` 构造方法列表：

*   `String(byte[] data)`：使用平台的默认字符集解码指定的字节数组，构造一个新的 `String` 实例。
*   `String(byte[] data, int offset, int byteCount)`：使用平台的默认字符集解码字节数组中指定的子序列，构造一个新的 `String` 实例。
*   `String(byte[] data, String charsetName)`：使用指定的字符集名称解码指定的字节数组，构造一个新的 `String` 实例。

此外，`String` 声明了一些方法，这些方法借助默认字符集或指定的字符集将其字符序列编码为字节数组。这里描述了其中两种方法：

*   `byte[] getBytes()`：返回一个新的字节数组，其中包含使用平台默认字符集编码的此字符串的字符。
*   `byte[] getBytes(String charsetName)`：返回一个新的字节数组，其中包含使用指定字符集名称编码的此字符串的字符。

请注意，当字符集不受支持时，`String(byte[] data, String charsetName)` 和 `byte[] getBytes(String charsetName)` 会抛出 `java.io.UnsupportedEncodingException` 异常。

我创建了一个演示 `String` 和字符集的小型应用程序。清单 10-2 展示了源代码。

**清单 10-2.** 在 `String` 中使用字符集

`import java.io.UnsupportedEncodingException;`

`public class CharsetDemo`

`{`

`public static void main(String[] args)`

`throws UnsupportedEncodingException`

`{`

`byte[] encodedMsg =`

`{`

`0x66, 0x61, (byte) 0xc3, (byte) 0xa7, 0x61, 0x64, 0x65, 0x20, 0x74,`

`0x6f, 0x75, 0x63, 0x68, (byte) 0xc3, (byte) 0xa9`

`};`

`String s = new String(encodedMsg, "UTF-8");`

`System.out.println(s);`

`System.out.println();`

`byte[] bytes = s.getBytes();`

`for (byte _byte: bytes)`

`System.out.print(Integer.toHexString(_byte & 255) + " ");`

`System.out.println();`

`}`

`}`

清单 10-2 的 `main()` 方法首先创建一个包含 UTF-8 编码消息的字节数组。然后，它通过 `UTF-8` 字符集将此数组转换为 `String` 对象。输出生成的 `String` 对象后，它将此对象的字节提取到一个新的字节数组中，并继续以十六进制格式输出这些字节。正如本章前面演示的，我将每个字节值与 255 进行按位与运算，以在 8 位字节整数值转换为 32 位整数值时，移除负整数的 `0xFF` 符号扩展字节。否则，这些符号扩展字节也会被输出。

编译清单 10-2 (`javac CharsetDemo.java`) 并运行此应用程序 (`java CharsetDemo`)。您应该会看到以下输出：

`façade touché`

`66 61 e7 61 64 65 20 74 6f 75 63 68 e9`

您可能会疑惑为什么看到的是 `e7` 而不是 `c3 a7`（带软缺音的拉丁小写字母 c [一个钩子或尾巴]），以及 `e9` 而不是 `c3 a9`（带尖音符的拉丁小写字母 e）。答案是，我调用了无参数的 `getBytes()` 方法来编码字符串。此方法使用默认字符集，在我的平台上它是 `windows-1252`。根据此字符集，`e7` 等同于 `c3 a7`，`e9` 等同于 `c3 a9`。结果是更短的编码序列。

**练习**

以下练习旨在测试您对第 10 章内容的理解：

定义字符集。  
`Charset` 类的用途是什么？  
识别 JVM 支持的标准字符集。  
字节顺序标记的用途是什么？  
如何获取默认字符集？  
当 JVM 不支持所需的字符集时，`Charset` 的 `Charset forName(String charsetName)` 工厂方法会做什么？  
通常如何通过 `Charset` 实例对字符串进行编码？  
识别执行实际编码和解码任务的 `Charset` 方法。  
`String` 的 `byte[] getBytes()` 方法实现了什么功能？  
编写一个 `AvailCharsets` 应用程序，获取并输出当前 JVM 支持的所有字符集的映射。（提示：您可以在 `Charset` 类中找到返回此映射的方法。）

## 总结

字符集将编码字符集与字符编码方案结合在一起。它们用于在字节序列和编码到这些序列中的字符之间进行转换。Java 通过提供 `Charset` 和相关类来支持字符集。它还在 `String` 类中使用字符集。

第 11 章 介绍了 NIO 的 `java.util.Formatter` 类及相关类型。

