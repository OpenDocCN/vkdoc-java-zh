# 11. Formatter

电子补充材料 本章的在线版本 (doi:[10.​1007/​978-1-4842-1565-4_​11](http://dx.doi.org/10.1007/978-1-4842-1565-4_11)) 包含补充材料，仅供授权用户使用。

JSR 51 ([`http://jcp.org/en/jsr/detail?id=51`](http://jcp.org/en/jsr/detail?id=51)) 的描述指出，提议在 NIO 中包含一个简单的 `printf` 风格的格式化工具。如果您熟悉 C 语言，您可能使用过支持格式化输出的 `printf()` 函数族。

使 `printf()` 函数有用的一个特性是可变参数，它允许您向这些函数传递可变数量的参数。由于 Java 直到 JDK 5 才添加对可变参数的支持，并且由于这种支持对于实现格式化输出非常有用，因此 `printf` 风格的格式化工具被推迟到 JDK 5 中实现。

本章探讨 JDK 5 的 `printf` 风格格式化工具。



## 探索 Formatter

JDK 5 引入了 `java.util.Formatter` 类，作为 `printf()` 风格格式字符串的解释器。该类支持布局对齐；数字、字符串和日期/时间数据的常见格式；以及更多功能。常用的 Java 类型（例如 `byte` 和 `java.math.BigDecimal`）均受支持。

`Formatter` 声明了多个用于创建 `Formatter` 对象的构造器。这些构造器允许你指定格式化输出的目标位置。例如，`Formatter()` 将格式化输出写入内部的 `java.lang.StringBuilder` 实例，而 `Formatter(OutputStream os)` 则将格式化输出写入指定的输出流。你可以通过调用 `Formatter` 的 `Appendable out()` 方法来访问该目标。

注意

`java.lang.Appendable` 接口描述了一个可以追加 `char` 值和字符序列的对象。其实例需要接收格式化输出（通过 `Formatter` 类）的类（例如 `StringBuilder`）实现了 `Appendable`。该接口声明了诸如 `Appendable append(char c)` 之类的方法——将 `c` 的字符追加到此 appendable 对象。当发生 I/O 错误时，此方法会抛出 `java.io.IOException`。

创建 `Formatter` 对象后，你可以调用 `format()` 方法来格式化可变数量的值。例如，`Formatter format(String format, Object... args)` 根据传递给 `format` 参数的格式说明符字符串格式化 `args` 数组，并返回对调用该方法的 `Formatter` 的引用，以便你可以链式调用 `format()` 方法（为方便起见）。

每个格式说明符都具有以下语法之一：

*   `%`[argument_index `$`][flags][width][`.` precision]conversion
*   `%`[argument_index `$`][flags][width]conversion
*   `%`[flags][width]conversion

第一种语法描述了用于通用（如字符串）、字符和数字类型的格式说明符。第二种语法描述了用于表示日期和时间的类型的格式说明符。第三种语法描述了不对应任何参数的格式说明符。

可选的 argument_index 是一个十进制整数，指示参数在参数列表中的位置。第一个参数由 `1$` 引用，第二个参数由 `2$` 引用，依此类推。

可选的 flags 表示一组用于修改输出格式的字符。有效标志集取决于转换类型。

可选的 width 是一个正十进制整数，指示要写入输出的最小字符数。

可选的 precision 是一个非负十进制整数，通常用于限制字符数。具体行为取决于转换类型。

必需的 conversion 取决于语法。对于第一种语法，它是一个字符，指示应如何格式化参数。给定参数的有效转换集取决于该参数的数据类型。对于第二种语法，它是一个双字符序列。第一个字符是 `t` 或 `T`。第二个字符指示要使用的格式。对于第三种语法，它是一个字符，指示要插入到输出中的内容。

转换分为六类：通用、字符、数字（整数或浮点数）、日期/时间、百分号和行分隔符。以下列表标识了几个示例格式说明符及其转换：

*   `%d`：将参数格式化为十进制整数。
*   `%x`：将参数格式化为十六进制整数。
*   `%c`：将参数格式化为字符。
*   `%f`：将参数格式化为十进制数。
*   `%s`：将参数格式化为字符串。
*   `%n`：输出一个操作系统特定的行分隔符。
*   `%10.2f`：将参数格式化为十进制数，其中 `10` 是要写入的最小字符数（当数字小于宽度时，会写入前导空格），`2` 是小数点后要写入的字符数。
*   `%05d`：将参数格式化为十进制整数，其中 `5` 是要写入的最小字符数（当数字小于宽度时，会写入前导 `0`）。

当你使用完格式化器后，可能需要调用 `void` `flush()` 方法，以确保目标中任何缓冲的输出都被写入底层流。当目标是文件时，你通常会调用 `flush()`。

接着，调用格式化器的 `void` `close()` 方法。除了关闭格式化器之外，当此目标的类实现了 `java.io.Closeable` 接口时，此方法还会关闭底层输出目标。如果格式化器已关闭，则此方法无效。在调用 `close()` 后尝试格式化会导致 `java.util.FormatterClosedException`。

清单 11-1 使用上述格式说明符提供了一个简单的 `Formatter` 演示。

清单 11-1. 演示 `Formatter` 类

`import java.util.Formatter;`

`public class FormatterDemo`

`{`

`public static void main(String[] args)`

`{`

`Formatter formatter = new Formatter();`

`formatter.format("%d", 123);`

`System.out.println(formatter.toString());`

`formatter.format("%x", 123);`

`System.out.println(formatter.toString());`

`formatter.format("%c", ’X’);`

`System.out.println(formatter.toString());`

`formatter.format("%f", 0.1);`

`System.out.println(formatter.toString());`

`formatter.format("%s%n", "Hello, World");`

`System.out.println(formatter.toString());`

`formatter.format("%10.2f", 98.375);`

`System.out.println(formatter.toString());`

`formatter.format("%05d", 123);`

`System.out.println(formatter.toString());`

`formatter.format("%1$d %1$d", 123);`

`System.out.println(formatter.toString());`

`formatter.format("%d %d", 123);`

`System.out.println(formatter.toString());`

`formatter.close();`

`}`

`}`

清单 11-1 的 `main()` 方法首先通过 `Formatter()` 构造器创建一个 `Formatter` 对象，该构造器将格式化输出发送到内部的 `StringBuilder` 实例。然后，它通过调用 `format()` 方法演示了上述格式说明符，接着调用 `toString()` 方法获取格式化后的内容，随后将其输出。

`formatter.format("%1$d %1$d", 123);` 方法调用通过 `1$` 引用同一个数据项参数（`123`）两次来访问它。如果没有这个引用（如 `formatter.format("%d %d", 123);` 所示），则会抛出异常，因为除非使用参数索引，否则每个格式说明符都必须有一个单独的参数。

最后，格式化器被关闭。

按如下方式编译清单 11-1：

`javac FormatterDemo.java`

按如下方式运行生成的应用程序：

`java FormatterDemo`

你应该会观察到以下输出：

`123`

`1237b`

`1237bX`

`1237bX0.100000`

`1237bX0.100000Hello, World`

`1237bX0.100000Hello, World`

`98.38`

`1237bX0.100000Hello, World`

`98.3800123`

`1237bX0.100000Hello, World`

`98.3800123123 123`

`Exception in thread "main" java.util.MissingFormatArgumentException: Format specifier ’%d’`

`at java.util.Formatter.format(Formatter.java:2519)`

`at java.util.Formatter.format(Formatter.java:2455)`

`at FormatterDemo.main(FormatterDemo.java:24)`



关于输出，首先要注意的是，每次调用 `format()` 都会将格式化后的输出追加到之前格式化输出的后面。其次要注意的是，当你没有指定所需的参数时，会抛出 `java.util.MissingFormatArgumentException` 异常。

注意

`MissingFormatArgumentException` 是多种格式化器异常类型之一，这些异常都继承自 `java.util.IllegalFormatException`。

如果你对这种拼接式的输出不满意，有两种方法可以解决这个问题：

*   在调用 `format()` 之前，实例化一个新的 `Formatter` 实例，例如 `formatter = new Formatter();`。这可以确保创建一个新的默认空字符串构建器。
*   创建你自己的 `StringBuilder` 实例，并将其传递给一个构造函数，例如 `Formatter (Appendable a)`。在输出格式化内容后，调用 `StringBuilder` 的 `void setLength(int newLength)` 方法，并以 `0` 作为参数来清除之前的内容。

当你只是想实现与 C 语言的 `printf()` 函数类似的功能时，却需要创建和管理一个 `Formatter` 对象，这很繁琐。Java 通过在 `java.io.PrintStream` 类中添加 `format()` 和等效的 `printf()` 方法来解决这个问题。

在添加到 `PrintStream` 的各种面向格式化器的方法中，你经常会调用 `PrintStream` 的 `printf(String format, Object... args)`。该方法将其格式化后的内容发送到打印流后，会返回对该流的引用，以便你可以进行方法链式调用。

清单 11-2 提供了一个简短的 `printf()` 演示。

清单 11-2. 通过 `printf()` 进行格式化

`public class FormatterDemo`

`{`

`public static void main(String[] args)`

`{`

`System.out.printf("%04X%n", 478);`

`System.out.printf("Current date: %1$tb %1$te, %1$tY%n",`

`System.currentTimeMillis());`

`}`

`}`

清单 11-2 中的 `main()` 方法调用了两次 `System.out.printf()`。第一次调用将 32 位整数 `478` 格式化为一个四位十六进制字符串，带有前导零和大写十六进制数字。第二次调用将 `System.currentTimeMillis()` 返回的当前毫秒值格式化为一个日期。`tb` 转换指定了缩写的月份名称（例如 Jan），`te` 转换指定了月份中的日期（例如 1 到 31），`tY` 转换指定了年份（格式化为至少四位数字，必要时带前导零）。

编译清单 11-2（`javac FormatterDemo.java`）并运行该应用程序（`java FormatterDemo`）。你应该会看到类似于下面所示的输出：

`01DE`

`Current date: Jul 28, 2015`

注意

有关 `Formatter` 及其支持的格式说明符的更多信息，请查阅 `Formatter` 的 Java 文档。

## 探索 Formattable 和 FormattableFlags

`Formatter` 附带了一个 `java.util.Formattable` 接口和一个 `java.util.FormattableFlags` 类，它们共同支持对任意用户定义类型进行有限的格式化定制。

任何需要使用 [`Formatter`](https://docs.oracle.com/javase/8/docs/api/java/util/Formatter.html#class%20in%20java.util) 的“`s`”（将格式参数作为字符串）转换字符来执行自定义格式化的类，都应实现 `Formattable`。该接口允许对任意对象的格式化进行基本控制。

`Formattable` 声明了以下方法，该方法由 `Formatter` 的 `format()` 方法调用以执行自定义格式化：

`void formatTo(` [`Formatter`](https://docs.oracle.com/javase/8/docs/api/java/util/Formatter.html#class%20in%20java.util) `formatter, int flags, int width, int precision)`

这些参数的含义如下：

*   `formatter` 是持有区域设置的 `Formatter` 对象，当输出完成时，你将输出发送到该对象。
*   `flags` 是 `FormattableFlags` 常量的位掩码：`ALTERNATE`（用户指定了 `#` 用于替代格式）、`LEFT_JUSTIFY`（用户指定了 `-` 用于左对齐）和 `UPPERCASE`（用户指定了 `S` 用于基于区域设置转换为大写）。
*   `width` 是要写入输出的最小字符数。如果转换后的值长度小于宽度，则输出将用空格字符填充，直到总字符数等于宽度。默认情况下，填充位于输出的开头。如果指定了 [`FormattableFlags.LEFT_JUSTIFY`](https://docs.oracle.com/javase/8/docs/api/java/util/FormattableFlags.html#LEFT_JUSTIFY)，则填充将位于末尾。如果宽度为 `-1`，则没有最小宽度。
*   `precision` 是要写入输出的最大字符数。由于精度在宽度之前应用，因此即使宽度大于精度，输出也会被截断为精度指定的字符数。如果精度为 `-1`，则对输出中的字符数没有明确限制。

`width` 和 `precision` 参数可能看起来令人困惑。为了帮助你理解它们，请考虑以下示例：

`System.out.printf("[%10.2s]%n", "ABC");`

`System.out.printf("[%10.12s]%n", "ABC");`

`System.out.printf("[%10.2s]%n", "ABCDEFGHIJKLMNOP");`

`System.out.printf("[%10.12s]%n", "ABCDEFGHIJKLMNOP");`

当你执行这些语句时，应该会看到以下输出：

`[        AB]`

`[       ABC]`

`[        AB]`

`[ABCDEFGHIJKL]`

最后一个示例显示输出了 12 个字符。虽然这个值超过了 10 个字符的宽度，但并未超过 12 个字符的精度。当输出的字符数少于 `width` 时，传递给 `width` 的值在对齐方面最为有用。

`formatTo` `(Formatter, int, int, int)` 方法会在字符串构建器中构建一个字符串，然后在返回之前，将该字符串传递给格式化器的 `format()` 方法。此方法不返回值。但是，当任何参数值无效时，它应该抛出 `IllegalFormatException`。

为了将这一讨论置于实际上下文中，假设你已经创建了清单 11-3 中出现的 `Employee` 类。

清单 11-3. 包含姓名和编号的员工类

`public class Employee`

`{`

`private String name;`

`private int empno;`

`public Employee(String name, int empno)`

`{`

`this.name = name;`

`this.empno = empno;`

`}`

`@Override`

`public String toString()`

`{`

`return name + ": " + empno;`

`}`

`}`

接着，假设你创建了清单 11-4 中出现的 `FormatterDemo` 应用程序类。

清单 11-4. 使用 `Employee` 类

`import java.util.Locale;`

`public class FormatterDemo`

`{`

`public static void main(String[] args)`

`{`

`Employee emp = new Employee("John Doe", 1000);`

`System.out.printf("[%s]%n", emp);`



`System.out.printf(Locale.FRENCH, "[%s]%n", emp);`

`System.out.printf("[%S]%n", emp);`

`System.out.printf("[%10.3s]%n", emp);`

`System.out.printf("[%-10.3s]%n", emp);`

`System.out.printf("[%#s]%n", emp);`

`}`

`}`

各种 `System.out.printf()` 方法调用尝试以不同方式格式化并输出从 `Employee` 对象的 `toString()` 方法返回的字符串。例如，第一个调用逐字输出 `toString()` 返回的值。

第二个调用尝试以法语区域设置输出。由于没有自定义设置，你将看到英文名称。第三个调用以大写形式输出名称；第四个和第五个调用在宽度为 10 个字符的范围内，分别右对齐和左对齐 `toString()` 返回的前三个字符。

最后一个调用尝试使用依赖于转换的替代形式，由 `#s` 表示。然而，由于尚未向 `Employee` 添加自定义设置，当检测到 `#s` 时，将抛出 `java.util.FormatFlagsConversionMismatchException`。

按如下方式编译清单 11-3 和 11-4：

`javac *.java`

按如下方式运行生成的应用程序：

`java FormatterDemo`

你应该会看到以下输出：

`[John Doe: 1000]`

`[John Doe: 1000]`

`[JOHN DOE: 1000]`

`[       Joh]`

`[Joh       ]`

``

`Exception in thread "main" java.util.FormatFlagsConversionMismatchException: Conversion = s, Flags = #`

`at java.util.Formatter$FormatSpecifier.failMismatch(Formatter.java:4298)`

`at java.util.Formatter$FormatSpecifier.printString(Formatter.java:2882)`

`at java.util.Formatter$FormatSpecifier.print(Formatter.java:2763)`

`at java.util.Formatter.format(Formatter.java:2520)`

`at java.io.PrintStream.format(PrintStream.java:970)`

`at java.io.PrintStream.printf(PrintStream.java:871)`

`at FormatterDemo.main(FormatterDemo.java:13)`

我们可以通过让 `Employee` 实现 `Formattable` 来改进此输出。例如，我们可以为法语区域设置显示等效名称。此外，当精度小于 8 时，我们可以仅显示员工编号。清单 [11-5 展示了一个改进的 `Employee` 类。

**清单 11-5. 实现 `Formattable`**

`import java.util.Formattable;`

`import java.util.FormattableFlags;`

`import java.util.Formatter;`

`import java.util.Locale;`

`public class Employee implements Formattable`

`{`

`private String name;`

`private int empno;`

`public Employee(String name, int empno)`

`{`

`this.name = name;`

`this.empno = empno;`

`}`

`@Override`

`public void formatTo(Formatter formatter, int flags, int width,`

`int precision)`

`{`

`StringBuilder sb = new StringBuilder();`

`String output = this.name;`

`if (formatter.locale().equals(Locale.FRENCH) &&`

`name.equals("John Doe"))`

`output = "Jean Dupont";`

`output += ": " + empno;`

`if (((flags & FormattableFlags.UPPERCASE) ==`

`FormattableFlags.UPPERCASE))`

`output = output.toUpperCase();`

`boolean alternate = (flags & FormattableFlags.ALTERNATE) ==`

`FormattableFlags.ALTERNATE;`

`alternate |= (precision >= 0 && precision < 8);`

`if (alternate)`

`output = "" + empno;`

`if (precision == -1 || output.length() <= precision)`

`sb.append(output);`

`else`

`sb.append(output.substring(0, precision - 1)).append(’*’);`

`int len = sb.length();`

`if (len < width)`

`{`

`boolean leftJustified = (flags & FormattableFlags.LEFT_JUSTIFY)`

`== FormattableFlags.LEFT_JUSTIFY;`

`for (int i = 0; i < width - len; i++)`

`if (leftJustified)`

`sb.append(’ ’);`

`else`

`sb.insert(0, ’ ’);`

`}`

`formatter.format(sb.toString());`

`}`

`@Override`

`public String toString()`

`{`

`return name + ": " + empno;`

`}`

`}`

`formatTo(Formatter, int, int, int)` 首先创建一个字符串构建器来存储输出字符串。

接下来，输出字符串默认为员工姓名。如果区域设置为法语且姓名等于 John Doe，则输出字符串更改为 Jean Dupont。然后在该字符串后附加一个冒号和员工编号。如果指定了大写标志，则输出字符串将转换为大写。

接着，如果指定了替代标志或精度在 0 到 7（含）之间，则输出字符串将缩短为员工编号。

如果精度等于 `-1`（无限制）或输出字符串长度不超过精度，则将输出字符串附加到字符串缓冲区。否则，最多附加最左侧的 `precision - 1` 个字符，后跟 `*`（表示截断的字符串）。

此时，如果字符串缓冲区中的字符数小于指定的宽度，则字符串缓冲区的内容将左对齐或右对齐。

至此工作完成，最后一步是将字符串构建器转换为字符串，然后将其传递给格式化器的 `format()` 方法。

按如下方式编译清单 11-4 和 11-5：

`javac *.java`

按如下方式运行生成的应用程序：

`java FormatterDemo`

你应该会看到以下输出：

`[John Doe: 1000]`

`[Jean Dupont: 1000]`

`[JOHN DOE: 1000]`

`[       10*]`

`[10*       ]`

`[1000]`

**练习**

以下练习旨在测试你对第 11 章内容的理解：

1.  指出构成 NIO 的 `printf` 风格格式化功能的三种非异常类型。  
2.  如何在格式说明符字符串中引用参数？  
3.  `%n` 格式说明符的作用是什么？  
4.  修改清单 11-1，使 `FormatterDemo` 的输出不会拼接成一个长字符串。

## 总结

JDK 5 引入了 `Formatter` 类，作为 `printf()` 风格格式字符串的解释器。该类支持布局对齐；数字、字符串和日期/时间数据的常见格式；以及更多功能。常用的 Java 类型（如 `byte` 和 `BigDecimal`）均受支持。

`Formatter` 声明了多个用于创建 `Formatter` 对象的构造器。这些构造器允许你指定格式化输出的目标位置。例如，`Formatter()` 将格式化输出写入内部的 `StringBuilder` 实例。你可以通过调用 `Formatter` 的 `Appendable out()` 方法来访问该目标。

创建 `Formatter` 对象后，调用 `format()` 方法可格式化可变数量的值。例如，`Formatter format(String format, Object... args)` 根据传递给 `format` 参数的格式说明符字符串格式化 `args` 数组，并返回对调用 `Formatter` 的引用，以便你可以链式调用 `format()` 方法。

当你只想实现与 C 语言 `printf()` 函数等效的功能时，必须创建并管理一个 `Formatter` 对象会显得很繁琐。Java 通过在 `PrintStream` 类中添加 `format()` 和等效的 `printf()` 方法（例如 `PrintStream printf(String format, Object... args)`）来解决此问题。

`Formatter` 附带了一个 `Formattable` 接口和一个 `FormattableFlags` 类，它们共同支持对任意用户定义类型进行有限的格式化自定义。任何需要使用 [`Formatter`](https://docs.oracle.com/javase/8/docs/api/java/util/Formatter.html#class%20in%20java.util) 的“`s`”（将格式参数作为字符串）转换字符执行自定义格式化的类，都应实现 `Formattable`。

第 12 章将介绍 NIO.2 改进的文件系统接口。

