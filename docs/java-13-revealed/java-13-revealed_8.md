# 8. 工具与 API 变更

在本章中，你将学习

*   关于已弃用和已移除的工具与 API

*   关于 JVM 常量 API

*   关于 `String` 类、`Optional<T>` 类、`Predicate<T>` 接口、`Files` 类和 `Filesystems` 类新增的方法

*   关于集合、缓冲区、文件系统和流 API 的变更

*   如何以紧凑形式格式化数字

*   Java 平台对 Unicode 标准的支持

本章包含的主题虽然不足以单独成章，但足够重要，值得在本书中提及。本章中的所有示例程序都位于 `jdojo.misc` 模块中，如清单 8-1 所示。

```
// module-info.java
module jdojo.misc {
exports com.jdojo.misc;
}
清单 8-1
一个名为 jdojo.misc 的模块
```

## 已弃用的工具

在本节中，我将列出并解释 JDK 10 到 JDK 13 中已弃用的工具和 API。我只列出作为开发者需要了解的重要工具。有关所有已弃用工具和 API 的完整列表，请参阅特定 JDK 的发行说明。

### Nashorn JavaScript 引擎

Nashorn JavaScript 引擎是在 JDK 8 中引入的，它是 ECMAScript-262 5.1 标准的完整实现。该引擎的维护者发现很难跟上 ECMAScript 的变化，最终决定将其弃用以备移除。在 JDK 11 中，Nashorn JavaScript 脚本引擎和 API 以及 `jjs` 工具已被弃用，并计划在未来的版本中移除。

允许在 Java 中使用脚本语言的 `javax.script` API 不受 Nashorn JavaScript 引擎弃用的影响。如果你在 Java 应用程序中使用其他脚本引擎，它们将继续正常工作。

Oracle 已宣布支持 GraalVM，可用于迁移你的 Nashorn 应用程序。你可以在 [`https://blogs.oracle.com/developers/announcing-graalvm`](https://blogs.oracle.com/developers/announcing-graalvm) 阅读更多关于 Oracle 公告的信息。你可以在其官方网站 [`www.graalvm.org`](https://www.graalvm.org) 上找到更多关于 GraalVM 的信息。

### rmic 工具

JDK 13 已弃用 `rmic` 工具，并计划在未来的版本中移除。它用于为远程方法调用（RMI）创建静态存根。自 Java 5 起，存根可以动态生成，这是生成存根的推荐方式。如果你仍在使用 RMI 的静态存根，请将代码迁移为使用动态存根。你可以参考我的书《Java APIs, Extensions and Libraries: With JavaFX, JDBC, Jmod, Jlink, Networking, and the Process API》（ISBN-1484235452），了解有关如何在 RMI 中使用动态存根的更多详细信息。

### Pack200 工具和 API

JDK 11 弃用了 `pack200` 和 `unpack200` 工具以及 `java.util.jar` 包中的 `Pack200` API。它们将在未来的 JDK 版本中被移除。Pack200 是一种针对 JAR 文件的压缩方案，旨在减少 JDK 下载和 Java 应用程序的磁盘空间和带宽需求。

JDK 附带了 `pack200` 和 `unpack200` 工具，用于压缩和解压缩 JDK 8 及更早版本中的 JAR 文件。`Pack200` API 以编程方式用于相同目的。高速互联网以及浏览器中移除 applet 支持等其他技术进步，使得 Pack200 方案已过时。JDK 9 还引入了新的文件格式 JMOD 和 JImage，用于打包 Java 模块，这比 JAR 文件格式更高效，并且不使用 Pack200。

### Swing Motif 外观

Java 13 已弃用 Swing Motif 外观。它在 macOS 上不受支持。有关更多详细信息，请参阅 [`https://bugs.openjdk.java.net/browse/JDK-8177960`](https://bugs.openjdk.java.net/browse/JDK-8177960)。

## 已移除的工具

在本节中，我将列出已移除的工具及其简要说明。

### appletviewer 工具

Java 9 已弃用 Applet API 和 `appletviewer` 工具。Java 11 移除了 `appletviewer` 工具，该工具曾用于在本地启动 applet 而无需使用浏览器。

### javah 工具

JDK 曾附带一个名为 `javah` 的工具。该工具用于为 Java 代码中所有 native 方法声明生成 C/C++ 头文件。该工具在 JDK 9 中被弃用，并计划在未来移除。在 JDK 10 中，该工具已被移除。

JDK 8 为 Java 编译器（`javac`）添加了一个 `-h` 选项，用于生成 C/C++ 头文件。`-h` 选项接受一个目录名，编译器会将生成的头文件放置在该目录中。以下命令编译 `HelloJNI.java` 文件，并将所有 C/C++ 头文件放置在 `jni_headers` 目录中：

```
javac -h jni_headers HelloJNI.java
```

当使用 `-h` 选项时，编译器会扫描源文件中的 native 方法声明，并在指定目录中生成 C/C++ 头文件。

## Runtime 类

`java.lang.Runtime` 类中的以下两个方法在 Java 9 中被弃用，并计划在未来的版本中移除，现已在 Java 13 中被移除：

*   `void traceInstructions`​`(boolean on)`

*   `void traceMethodCalls`​`(boolean on)`

## Thread 类

`Thread` 类中的 `destroy()` 和 `stop(Throwable)` 方法已在 Java 11 中被移除。无参的 `stop()` 方法不受影响。

## JVM 常量 API

JDK 12 引入了 JVM 常量 API，这是一个底层 API，用于处理库和工具如何描述类文件常量池中的条目，这些库和工具会操作字节码。作为开发者，你通常不需要在应用程序中使用此 API。该 API 位于 `java.base` 模块的 `java.lang.constant` 包中。你经常会遇到此 API 中的以下两个接口：

*   `Constable`

*   `ConstantDesc`

`Constable` 表示一个可以在类文件常量池中表示的常量。换句话说，其实例代表常量值的类型就是 `Constable`。实现 `Constable` 接口的一些类型包括 `String`、`Integer`、`Long`、`Float`、`Double`、`Class`、`MethodType` 和 `MethodHandle`。

一个 `Constable` 可以将其自身名义上描述为一个 `ConstantDesc`。换句话说，`ConstantDesc` 是 `Constable` 所代表的常量值的名义描述符。之所以称为“名义描述符”，是因为它本身不是值，而是描述该值或在给定的类加载上下文中重建该值的“链接”或“配方”。名义描述符也可以用于以符号形式（或名义形式）将值存储在常量池中。存在专门的 `ConstantDesc` 类型，例如 `ClassDesc`，用作 `Class` 常量的名义描述符。



### 提示

JVM 常量 API 是一个底层 API，专为操作字节码的库和工具设计。作为应用程序开发者，你不会直接使用它们。

这两个接口都包含一个单一方法。`Constable` 接口包含以下方法：

```
Optional describeConstable()
```

`describeConstable()` 方法返回一个 `Optional`，如果无法为此 `Constable` 构造一个名义描述符，则该 `Optional` 为空。

`ConstantDesc` 接口包含以下方法：

```
Object resolveConstantDesc(MethodHandles.Lookup lookup) throws ReflectiveOperationException;
```

`resolveConstantDesc()` 方法使用 `MethodHandles.Lookup` 参数作为解析和访问控制上下文，返回解析后的常量值。

`String`、`Integer`、`Long`、`Float` 和 `Double` 类同时实现了 `Constable` 和 `ConstantDesc` 接口，并作为自身的名义描述符。清单 8-2 包含一个在 `String` 上使用这些方法的简单示例。

```
// ConstableTest.java
package com.jdojo.misc;
import java.lang.invoke.MethodHandles;
import java.util.Optional;
public class ConstableTest {
public static void main(String[] args) {
String str1 = "Hello, JVM Constants API";
// desc 中的 Optional 包含字符串本身 (str1)
Optional desc = str1.describeConstable();
String str2  = desc.get();
// str3 包含字符串本身 (str1)
String str3 = str1.resolveConstantDesc(MethodHandles.lookup());
System.out.println("str1: " + str1);
System.out.println("str2: " + str2);
System.out.println("str3: " + str3);
System.out.println("str1 == str2: " + (str1 == str2));
System.out.println("str2 == str3: " + (str2 == str3));
}
}
str1: Hello, JVM Constants API
str2: Hello, JVM Constants API
str3: Hello, JVM Constants API
str1 == str2: true
str2 == str3: true
清单 8-2
使用 JVM 常量 API
```

## String 类

`String` 类在 Java 11 到 Java 13 中新增了几个方法。我已在第 6 章中描述了 Java 13 中的新方法以及 Java 12 中的 `indent()` 方法。`describeConstable()` 和 `resolveConstantDesc()` 方法的存在是因为 `String` 类实现了 Java 12 中引入的 JVM 常量 API 中的 `Constable` 和 `ConstantDesc` 接口。本节中我不解释这些方法。为了完整性，我在此列出了 `String` 类中的所有新方法。

**Java 11 新增的方法**

*   `String strip()`

*   `String stripLeading()`

*   `String stripTrailing()`

*   `boolean isBlank()`

*   `Stream<String> lines()`

*   `String repeat`​`(int count)`

**Java 12 新增的方法**

*   `String indent`​`(int n)`

*   `<R> R transform`​`(Function<? super String,`​`? extends R> f)`

*   `Optional<String> describeConstable()`

*   `String resolveConstantDesc`​`(MethodHandles.Lookup lookup)`

**Java 13 新增的方法**

*   `String stripIndent()`

*   `String translateEscapes()`

*   `String formatted`​`(Object... args)`

我将在后续章节中通过示例解释这些方法。

### 去除空白字符

Java 11 为 `String` 类添加了三个去除前导/尾部、前导和尾部空白字符的方法：`strip()`、`stripLeading()` 和 `stripTrailing()`。`strip()` 方法在移除前导和尾部空白字符后返回 `String`。另外两个方法在仅移除前导或尾部空白字符后返回 `String`。这些方法移除空白字符。请参考 `Character` 类的 `isWhitespace(int codepoint)` 方法以了解 Java 中空白字符的定义。

`String` 类的 `trim()` 方法在移除前导和尾部空格后返回 `String`，其中空格定义为码点小于或等于 Unicode 值 32（空格字符）的任何字符。比较 `strip()` 和 `trim()` 方法。`trim()` 方法作用于 ASCII 控制字符（或 ASCII 空白字符），而 `strip()` 方法作用于 Unicode 空白字符。

### 测试空白字符串

你可以使用 `String` 类的 `isBlank()` 方法测试字符串是否仅包含空白字符。如果字符串仅包含空白字符，则返回 `true`。否则返回 `false`。

### 获取行流

你可以使用 `String` 类的 `lines()` 方法获取字符串中的行流。字符串中的一行定义为零个或多个字符后跟一个行终止符的序列，行终止符可以是 `"\n"`、`"\r"` 或 `"\r\n"`。如果到达字符串末尾，一行可能不以行终止符结尾。以下代码片段使用文本块为威廉·华兹华斯的露西组诗中的一节创建多行字符串。它打印每行的字符数，后跟文本。它使用 `String` 类的 `lines()` 方法获取文本中每行的流。

```
String text = """
Upon the moon I fixed my eye,
All over the wide lea;
With quickening pace my horse drew nigh
Those paths so dear to me.
""";
text.lines()
.map(s -> s.length() + ": " + s)
.forEach(System.out::println);
29: Upon the moon I fixed my eye,
22: All over the wide lea;
39: With quickening pace my horse drew nigh
26: Those paths so dear to me.
```

### 重复字符串

`String` 类的 `repeat`​`(int count)` 方法返回一个字符串，其内容是将该字符串重复 `count` 次后的拼接结果。例如，`"hello".repeat(2)` 返回 `"hellohello"`。如果字符串为空或 `count` 为零，则返回空字符串。如果 `count` 小于零，则抛出 `IllegalArgumentException`。该方法在创建缩进时非常有用。以下代码片段打印五行星号，每行用空格缩进。第一行由四个空格和一个星号组成，第二行由三个空格和两个星号组成，依此类推。

```
IntStream.rangeClosed(1, 5)
.mapToObj(n -> " ".repeat(5-n) + "*".repeat(n))
.forEach(System.out::println);
*
**
***
****
*****
```



### 转换字符串

Java 12 为 `String` 类新增了一个 `transform()` 方法，通过应用 `Function<T,R>` 将字符串转换为另一个对象。`Function<T,R>` 接受一个 `String` 并返回任意类型的对象。该方法的声明如下：

```
R transform (Function f)
```

此方法的用途并不明显。它最初是作为 Java 12 中*原始字符串*支持的一部分而添加的。*原始字符串*特性后来从 Java 12 中移除，但 `transform()` 方法得以保留。

以下是一个简单示例，演示如何使用 `transform()` 方法将字符串 `"Hello"` 转换为 `"Hello, World"`：

```
String str = "Hello";
String greeting = str.transform(s -> s + ", World");
System.out.println(greeting);
Hello, World
```

Lambda 表达式接受一个字符串作为输入，并通过在其后追加 `", World"` 返回输入字符串。你会同意，仅仅为了拼接两个字符串而使用 `transform()` 方法并没有带来任何好处。建议改为编写如下代码：

```
String str = "Hello";
String greeting = str + ", World";
System.out.println(greeting);
Hello, World
```

让我们再看另一个简单示例，其中你使用方法引用 `Integer::parseInt` 或 `Integer::valueOf` 与 `transform()` 方法将字符串转换为 `int`：

```
String str = "1969";
int year = str.transform(Integer::parseInt);
//int year = str.transform(Integer::valueOf);
System.out.println(year);

```

你可能会争辩说，前面的代码片段可以改写如下，且不会丧失可读性：

```
String str = "1969";
int year = Integer.parseInt(str);
System.out.println(year);

```

这两个示例的关键点在于：如果一个方法接受一个字符串并通过应用某些逻辑返回另一个对象，你总是可以直接调用该方法，而不是使用 `transform()` 方法并传递该方法的引用。

那么，对字符串应用多次转换呢？通常，字符串转换方法是某个类的静态方法，无法进行链式调用。在这种情况下，使用 `transform()` 方法可能会使你的代码更具可读性。假设你想对一个多行字符串应用两个转换：

*   通过移除空白行来净化它们。

*   为每一行添加行号。

假设你创建了一个 `Transforms` 类，其中包含两个静态方法 `sanitizeBlanks(String str)` 和 `addLineNumber(String str)` 来执行这些转换。这两个方法都返回一个 `String`。你可以编写以下代码来执行此任务：

```
String str = "your original string goes here";
// 对 str 应用两个转换
String newStr = str.transform(Transforms::sanitizeBlanks)
.transform(Transforms::addLineNumber);
```

你可以重写这段代码，改为调用 `Transforms` 类的静态方法，而不是使用 `String` 类的 `transform()` 方法，如下所示：

```
String str = "your original string goes here";
// 对 str 应用两个转换
String str1 = Transforms.sanitizeBlanks(str);
String newStr = Transforms.addLineNumber(str1);
```

比较前面两段代码。使用 `String` 类的 `transform()` 方法的代码更具可读性。清单 8-3 包含一个完整的程序，用于演示我们讨论中的两个转换。

```
// Transforms.java
package com.jdojo.misc;
import java.util.concurrent.atomic.AtomicInteger;
import java.util.function.Predicate;
import static java.util.stream.Collectors.joining;
public class Transforms {
/**
* 返回移除空白行以及每行前导和尾随空白后的字符串。
* 仅包含空白字符的行被视为空白行。
* @param str 要净化的字符串
* @return 移除空白行以及每行前导/尾随空白后的字符串。
*/
public static String sanitizeBlanks(String str) {
return str.lines()
.filter(Predicate.not(String::isBlank))
.map(String::strip)
.collect(joining("\n"));
}
/**
* 为字符串中的每一行添加行号。
* @param str 要添加行号的字符串
* @return 通过在字符串中每行前添加行号后返回的字符串。
*/
public static String addLineNumber(String str) {
AtomicInteger lineNumber = new AtomicInteger();
return str.lines()
.map(s-> lineNumber.incrementAndGet() + ":" + s)
.collect(joining("\n"));
}
public static void main(String[] args) {
String str = """
Upon the moon I fixed my eye,
All over the wide lea;
With quickening pace my horse drew nigh
Those paths so dear to me.
""";
System.out.println("原始字符串:\n" + str);
String newStr = str.transform(Transforms::sanitizeBlanks)
.transform(Transforms::addLineNumber);
System.out.println("转换后的字符串:\n" + newStr);
/* 使用 String 类的 transform() 方法的替代方案
String str1 = Transforms.sanitizeBlanks(str);
String str2 = Transforms.addLineNumber(str1);
System.out.println("\n 转换后的字符串:\n" + str2);
*/
}
}
原始字符串:
Upon the moon I fixed my eye,
All over the wide lea;
With quickening pace my horse drew nigh
Those paths so dear to me.
转换后的字符串:
1:Upon the moon I fixed my eye,
2:All over the wide lea;
3:With quickening pace my horse drew nigh
4:Those paths so dear to me.
清单 8-3
使用 String 类的 transform() 方法
```

请注意构成原始字符串的各行中的空白行和额外的前导空格，在应用第一个转换时它们会被移除。第二个转换以“#:”的形式为每一行添加行号。原始字符串包含六行，而转换后的字符串只包含四行。代码中包含充分的注释来解释逻辑。

## Optional 和 Predicate

以下是添加到 `java.util.Optional<T>` 类中的两个新方法：

*   `T orElseThrow()`

*   `boolean isEmpty()`

无参的 `orElseThrow()` 方法是在 Java 10 中添加到 `Optional<T>` 类的。如果 `Optional` 包含一个值，该方法返回该值。否则，它会抛出一个 `java.util.NoSuchElementException` 运行时异常。



### 提示

`orElseThrow()` 和 `isEmpty()` 这两个方法也已添加到 `OptionalInt`、`OptionalLong` 和 `OptionalDouble` 类中。

`Optional` 类原本已有另一个版本的 `orElseThrow()` 方法，其声明如下：

```
T orElseThrow (Supplier exceptionSupplier) throws X extends Throwable
```

该方法接受一个 `Supplier<T>`，当值不存在时，由该供应商提供要抛出的异常。抛出的异常可以是受检异常或非受检异常。你还可以在创建异常时使用自定义消息。无参的 `orElseThrow()` 方法会抛出一个非受检异常，并附带消息 `"No value present"`。以下代码片段展示了如何使用现有和新增的 `orElseThrow()` 方法：

```
Optional name = Optional.of("Kishori Sharan");
// 此处省略更多代码...
// 当 name 为空时，提供要抛出的异常
String name1 = name.orElseThrow(
() -> new NoSuchElementException("No value present"));
// 如果 name 为空，让运行时抛出 NoSuchElementException
String name2 = name.orElseThrow();
```

`Optional<T>` 类原本有一个名为 `isPresent()` 的方法，如果值存在则返回 `true`，否则返回 `false`。Java 11 新增了一个 `isEmpty()` 方法，其效果等同于调用 `!isPresent()`。如果你使用逻辑来检查值是否缺失，这个新方法能使代码更具可读性。以前使用 `!isPresent()` 时，你必须检查“非存在”，而不是检查“缺失”。它在用作方法引用的 lambda 表达式中也很有用。

清单 8-4 包含一个完整的程序，用于演示 `Optional<T>` 类中这些新方法的使用。

```
// OptionalTest.java
package com.jdojo.misc;
import java.util.List;
import java.util.NoSuchElementException;
import java.util.Optional;
public class OptionalTest {
public static void main(String[] args) {
Optional name = Optional.of("Kishori Sharan");
String fn1 = name.orElseThrow(
() -> new NoSuchElementException("No value present"));
String fn2 = name.orElseThrow();
System.out.println("fn1: " + fn1);
System.out.println("fn2: " + fn2);
try {
Optional phone = Optional.empty();
String f2 = phone.orElseThrow();
} catch (NoSuchElementException e) {
System.out.println("Phone: " + e.getMessage());
}
// 有一个 Optional 列表
List> list = List.of(Optional.empty(),
Optional.of("Kishori"),
Optional.empty(),
Optional.of("Sharan"),
Optional.empty());
// 统计列表中空的 Optional 数量
long emptyCount = list.stream()
.filter(Optional::isEmpty)
.count();
System.out.println("Empty name count: " + emptyCount);
}
}
fn1: Kishori Sharan
fn2: Kishori Sharan
Phone: No value present
Empty name count: 3
清单 8-4
测试 Optional 类中的新方法
```

请注意，在统计列表中空名称时使用了 `Optional::isEmpty` 方法引用：

```
long emptyCount = list.stream()
.filter(Optional::isEmpty)
.count();
```

在 Java 11 之前，你必须使用 `isPresent()` 方法，代码编写如下：

```
long emptyCount = list.stream()
.filter(n -> !n.isPresent())
.count();
```

Java 11 在 `Predicate<T>` 接口中新增了一个 `not()` 静态方法，该方法返回指定谓词的否定形式。其声明为：

```
static  Predicate not(Predicate target)
```

使用 `Predicate<T>` 接口中的 `not()` 静态方法，你可以将之前的语句重写如下：

```
long emptyCount = list.stream()
.filter(Predicate.not(Optional::isPresent))
.count();
```

当你想要否定一个方法引用时，`not()` 静态方法就派上了用场，就像你在上一条语句中否定 `Optional::isPresent` 方法引用一样。

## 紧凑数字格式

Java 12 在 `java.base` 模块的 `java.text` 包中添加了一个 `CompactNumberFormat` 类。它是 `NumberFormat` 类的子类。它可以将十进制数字格式化为其紧凑形式。数字的紧凑形式适用于显示空间有限的情况。例如，与其将十亿显示为 1000000000，不如将其显示为 1B 或 1 billion 这样的紧凑形式。紧凑数字格式由 Unicode 区域设置数据标记语言 (LDML) 定义，详见 [`http://unicode.org/reports/tr35/tr35-numbers.html#Compact_Number_Formats`](http://unicode.org/reports/tr35/tr35-numbers.html%2523Compact_Number_Formats)。

使用 `NumberFormat` 类中的 `getCompactNumberInstance()` 工厂方法来获取 `CompactNumberFormat` 类的实例：

*   `NumberFormat getCompactNumberInstance()`

*   `NumberFormat getCompactNumberInstance`​`(Locale, NumberFormat.Style formatStyle)`

无参的 `getCompactNumberInstance()` 方法使用默认区域设置和短数字格式。

清单 8-5 包含一个完整的程序，用于演示数字的紧凑格式格式化。该程序在英语-美国、德语-德国和印地语-印度区域设置中格式化两个整数。请注意不同区域设置中数字的舍入情况。

```
// CompactNumbers.java
package com.jdojo.misc;
import java.text.NumberFormat;
import java.util.Locale;
import static java.text.NumberFormat.Style.LONG;
import static java.text.NumberFormat.Style.SHORT;
public class CompactNumbers {
public static void main(String[] args) {
printCompact(24000, Locale.US);
printCompact(1969, Locale.US);
printCompact(24000, Locale.GERMANY);
printCompact(1969, Locale.GERMANY);
Locale hindiIndia = new Locale("hi", "IN");
printCompact(24000, hindiIndia);
printCompact(1969, hindiIndia);
}
public static void printCompact(int num, Locale locale) {
NumberFormat shortFormatter
= NumberFormat.getCompactNumberInstance(locale, SHORT);
NumberFormat longFormatter
= NumberFormat.getCompactNumberInstance(locale, LONG);
String shortStr = shortFormatter.format(num);
String longStr = longFormatter.format(num);
System.out.printf("%s: %d, %s, %s%n",
locale, num, shortStr, longStr);
}
}
en_US: 24000, 24K, 24 thousand
en_US: 1969, 2K, 2 thousand
de_DE: 24000, 24.000, 24 Tausend
de_DE: 1969, 1.969, 2 Tausend
hi_IN: 24000, 24 हज़ार, 24 हज़ार
hi_IN: 1969, 2 हज़ार, 2 हज़ार
清单 8-5
以紧凑形式格式化数字
```

## Unicode 支持

Java 平台紧跟最新的 Unicode 标准。以下是 Java 不同版本及其支持的 Unicode 版本：

*   Java 10 支持 Unicode 标准 8.0.0 版。
*   Java 11 支持 Unicode 标准 10.0.0 版。
*   Java 12 支持 Unicode 标准 11.0 版，以及来自 Unicode 标准 11.0 之后第一个分配该码点的版本中的日本时代码点 U+32FF。
*   Java 13 支持 Unicode 标准 12.1 版。



## Files 类

`Files` 类新增了以下方法：

*   `String readString`​`(Path path) throws IOException`

*   `String readString`​`(Path path, Charset cs) throws IOException`

*   `Path writeString`​`(Path path, CharSequence csq, OpenOption... options) throws IOException`

*   `Path writeString`​`(Path path, CharSequence csq, Charset cs, OpenOption... options) throws IOException`

*   `long mismatch`​`(Path path, Path path2) throws IOException`

`readString()` 和 `writeString()` 方法是在 Java 11 中新增的。`readString()` 方法将文件内容读取为 `String`。`writeString()` 方法将 `CharSequence`（例如 `String` 和 `StringBuilder`）写入文件。

`mismatch()` 方法是在 Java 12 中新增的。它通过逐字节比较两个指定文件的内容来查找它们之间的不匹配。如果内容完全匹配，则返回 -1L。否则，返回第一个不匹配字节的位置。如果满足以下条件之一，则认为两个文件匹配：

*   两个指定路径指向同一个文件。

*   两个文件大小相同，且逐字节比较内容也相同。

清单 8-6 包含一个完整的程序，演示了如何将字符串读写到文件以及测试两个文件是否不匹配。你得到的输出可能不同。

```
// FilesTest.java
package com.jdojo.misc;
import java.io.IOException;
import java.nio.file.Files;
import java.nio.file.Path;
import java.nio.file.Paths;
public class FilesTest {
public static void main(String[] args) throws IOException {
// 两个文件及其内容
String text1 = "Hello, Text1";
String text2 = "Hello, Text2";
Path file1 = Paths.get("greeting1.txt");
Path file2 = Paths.get("greeting2.txt");
// 确保测试文件存在。如果不存在，则创建它们。
createTestFile(file1, text1);
createTestFile(file2, text2);
printMismatch(file1, file2);
printMismatch(file1, file1);
}
public static void createTestFile(Path file, String text)
throws IOException {
Path absPath = file.toAbsolutePath();
if (Files.exists(file) && Files.isRegularFile(file)) {
System.out.printf("信息: %s 已存在。%n", absPath);
} else {
// 将文本写入文件
System.out.printf("已创建 %s。%n", absPath);
Files.writeString(file, text);
}
// 打印文件内容
String str = Files.readString(file);
System.out.printf("%s 包含:%n%s%n", absPath, str);
}
public static void printMismatch(Path file1, Path file2)
throws IOException {
long pos = Files.mismatch(file1, file2);
if (pos == -1L) {
System.out.printf("%s 和 %s 匹配。%n",
file1.toAbsolutePath(), file2.toAbsolutePath());
} else {
System.out.printf("%s 和 %s 在位置 %d 处不匹配。%n",
file1.toAbsolutePath(), file2.toAbsolutePath(), pos);
}
}
}
已创建 C:\Java13Revealed\greeting1.txt。
C:\Java13Revealed\greeting1.txt 包含:
Hello, Text1
已创建 C:\Java13Revealed\greeting2.txt。
C:\Java13Revealed\greeting2.txt 包含:
Hello, Text2
C:\Java13Revealed\greeting1.txt 和 C:\Java13Revealed\greeting2.txt 在位置 11 处不匹配。
C:\Java13Revealed\greeting1.txt 和 C:\Java13Revealed\greeting1.txt 匹配。
清单 8-6
将字符串读写到文件并测试两个文件是否不匹配
```

该程序在当前目录下创建了两个文本文件，分别命名为 `greeting1.txt` 和 `greeting2.txt`，其内容分别为 `"Hello, Text1"` 和 `"Hello, Text2"`。如果这些文件已存在，程序则不会创建它们。程序会打印信息性消息，例如文件路径及其内容。最后，它会比较文件是否不匹配，并打印出第一个不匹配的位置。不匹配位置是从零开始的。也就是说，如果文件中的第一个字节不匹配，`mismatch()` 方法将返回零。首次运行和后续运行的输出会有所不同。

## 集合

Collection API 有两处更改。Java 10 为 `List`、`Set` 和 `Map` 接口添加了一个静态的 `copyOf()` 工厂方法，允许你从另一个集合创建不可修改的 `List`、`Set` 和 `Map`。Java 11 为 `Collection<E>` 接口添加了一个名为 `toArray(IntFunction<T[]> generator)` 的新默认方法，可用于传入*数组构造函数引用*，将集合的元素复制到特定类型的数组中。我将在接下来的章节中解释这些新增内容。

### 创建集合的不可修改副本

Java 10 为 `List`、`Set` 和 `Map` 接口添加了一个静态的 `copyOf()` 工厂方法。该方法返回一个不可修改的 `List`、`Set` 或 `Map`。其声明如下：

*   `<E> List<E> copyOf`​`(Collection<? extends E> coll)`

*   `<E> Set<E> copyOf`​`(Collection<? extends E> coll)`

*   `<K,`​ `V> Map<K,`​`V> copyOf`​`(Map<? extends K,`​`? extends V> map)`

返回 `List` 的 `copyOf()` 方法位于 `List` 接口中，返回 `Set` 的位于 `Set` 接口中，返回 `Map` 的位于 `Map` 接口中。源集合不能为 `null`，并且不能包含 `null` 元素。对于 `Map`，`Map` 不应包含任何 `null` 键或值。返回的 `List` 中的元素顺序与指定集合的迭代顺序一致。即使源集合被修改，`copyOf()` 方法返回的集合也不会改变。

清单 8-7 包含一个完整的程序，演示了 `List` 接口中复制工厂方法的使用。对 `Set` 或 `Map` 使用此方法的方式非常相似。

```
// UnmodifiableListTest.java
package com.jdojo.misc;
import java.util.List;
import java.util.ArrayList;
import java.util.Comparator;
public class UnmodifiableListTest {
public static void main(String[] args) {
// 创建一个可修改的列表
List names = new ArrayList();
names.add("John");
names.add("Buddy");
names.add("Vishwa");
names.add("Amy");
// 将可修改的列表复制到一个不可修改的列表中
List namesCopy = List.copyOf(names);
// 打印两个列表
System.out.println("\n 复制原始列表后：");
System.out.println("names: " + names);
System.out.println("namesCopy: " + namesCopy);
// 让我们向可修改的列表中添加一个名字
names.add("Mamta");
// 打印两个列表
System.out.println("\n 向原始列表添加一个名字后：");
System.out.println("names: " + names);
System.out.println("namesCopy: " + namesCopy);
// 让我们尝试对不可修改的列表进行排序
try {
namesCopy.sort(Comparator.naturalOrder());
System.out.println("\n 排序后的 namesCopy: " + namesCopy);
} catch (Exception e) {
System.out.println("\n 无法对不可修改的列表进行排序");
}
}
}
复制原始列表后：
names: [John, Buddy, Vishwa, Amy]
namesCopy: [John, Buddy, Vishwa, Amy]
向原始列表添加一个名字后：
names: [John, Buddy, Vishwa, Amy, Mamta]
namesCopy: [John, Buddy, Vishwa, Amy]
无法对不可修改的列表进行排序
清单 8-7
使用 List 接口的 copyOf() 方法创建不可修改的列表
```



### 将集合转换为数组

`Collection<E>` 接口包含一个重载的 `toArray()` 方法，用于将集合转换为数组：

*   `Object[] toArray()`

*   `<T> T[] toArray`​`(T[] a)`

*   `default <T> T[] toArray`​`(IntFunction<T[]> generator)`

如果你没有数组，并且可以处理 `Object[]` 类型，请使用 `toArray()` 方法，该方法会创建一个包含集合元素的 `Object[]`。如果你希望返回的数组与集合类型相同，则不应使用此方法。

如果你已经有一个数组，并希望重用该数组来复制集合中的元素，请使用 `toArray(T[] a)` 方法。返回的数组类型与传入的数组类型相同。如果传入的数组足够大，可以容纳集合中的所有元素，则会使用该数组来复制集合。如果传入的数组大于集合，则集合元素之后数组中的第一个元素会被设置为 `null`。如果集合不包含 `null` 值，则数组中第一个 `null` 的索引将指示从中复制元素到数组的集合的大小。如果传入的数组大小小于集合，则会创建一个新数组。

Java 11 新增了默认方法 `toArray(IntFunction<T[]> generator)`。当你没有数组时，可以使用此方法。你可以使用数组构造器引用作为其参数。该方法会创建一个数组，复制集合，然后返回该数组。默认实现会以零为参数调用生成器函数，并将返回的数组传递给 `toArray(T[] a)` 方法。以下代码片段使用此版本的 `toArray()` 方法将列表元素复制到数组：

```
List namesList = List.of("Buddy", "John", "Lisa");
// 使用 toArray(IntFunction generator) 方法，传入
// String[] 构造器引用，将列表复制到数组
String[] namesArray = namesList.toArray(String[]::new)
```

清单 8-8 包含一个完整的程序，该程序使用了所有三个版本的 `toArray()` 方法将列表复制到数组。

```
// CollectionToArray.java
package com.jdojo.misc;
import java.util.Arrays;
import java.util.List;
public class CollectionToArray {
public static void main(String[] args) {
// 创建一个字符串列表
List names = List.of("Buddy", "John", "Lisa");
System.out.println("列表: " + names);
// 使用 toArray() 方法将列表复制到 Object[]
Object[] names1 =  names.toArray();
System.out.println("names1: " + Arrays.toString(names1));
// 传入一个长度为 0 的 String[]，这样 toArray() 方法
// 会创建一个与列表大小相同的新数组
String[] names2 = names.toArray(new String[0]);
System.out.println("names2: " + Arrays.toString(names2));
// 传入一个更大的数组给 toArray()。数组中的前 3 个元素
// 将被列表中的元素覆盖，第 4 个元素将被设置为 null，
// 第 5 和第 6 个元素保持不变。
String[] team = new String[] {"Lu", "Xi", "Ho", "Yo", "To", "Mo"};
System.out.println("team: " + Arrays.toString(team));
String[] newTeam = names.toArray(team);
System.out.println("newTeam: " + Arrays.toString(newTeam));
System.out.println("team == newTeam: " + (team == newTeam));
// 使用 toArray(IntFunction generator) 复制列表
String[] names3 = names.toArray(String[]::new);
System.out.println("names3: " + Arrays.toString(names3));
}
}
列表: [Buddy, John, Lisa]
names1: [Buddy, John, Lisa]
names2: [Buddy, John, Lisa]
team: [Lu, Xi, Ho, Yo, To, Mo]
newTeam: [Buddy, John, Lisa, null, To, Mo]
team == newTeam: true
names3: [Buddy, John, Lisa]
清单 8-8
将集合元素复制到数组
```

## 新的收集器

Java 10 和 12 新增了五个收集器，你可以通过 `java.util.stream.Collectors` 类中新增的静态工厂方法获取它们。我将在后续章节中解释这些收集器。

### 不可修改的收集器

Java 10 向 `java.util.stream.Collectors` 类添加了四个方法，这些方法返回一个 `Collector`，用于将元素累积到不可修改的 `List`、`Set` 和 `Map` 中：

*   `<T> Collector<T,`​`?,`​`List<T>> toUnmodifiableList()`

*   `<T> Collector<T,`​`?,`​`Set<T>> toUnmodifiableSet()`

*   `<T,`​ `K,`​ `U> Collector<T,`​`?,`​`Map<K,`​`U>> toUnmodifiableMap`​`(Function<? super T,`​`? extends K> keyMapper, Function<? super T,`​`? extends U> valueMapper)`

*   `<T,`​ `K,`​ `U> Collector<T,`​`?,`​`Map<K,`​`U>> toUnmodifiableMap`​`(Function<? super T,`​`? extends K> keyMapper, Function<? super T,`​`? extends U> valueMapper, BinaryOperator<U> mergeFunction)`

这些方法返回的 `Collector` 的工作方式与 `toList()`、`toSet()` 和 `toMap()` 方法返回的相同，区别在于新方法返回的是不可修改的 `List`、`Set` 和 `Map`。

以下代码片段展示了如何使用 `Collectors` 类的 `toUnmodifiableList()` 方法返回的 `Collector`，从可修改列表中过滤并收集元素到不可修改列表：

```
import java.util.List;
import java.util.ArrayList;
import static java.util.stream.Collectors.toUnmodifiableList;
// 更多代码在此...
// 创建一个可修改的列表
List names = new ArrayList();
names.add("John");
names.add("Buddy");
names.add("Vishwa");
names.add("Amy");
// 收集长度小于或等于 4 个字符的名字
// 到一个不可修改的列表
List shortNames = names.stream()
.filter(name -> name.length() <= 4)
.collect(toUnmodifiableList());
// 打印两个列表
System.out.println("names: " + names);
System.out.println("shortNames: " + shortNames);
// 将会抛出 UnsupportedOperationException
// shortNames.add("Jaya");
names: [John, Buddy, Vishwa, Amy]
shortNames: [John, Amy]
```



### Teeing 收集器

有时，您希望从流中收集两种不同类型的结果，并最终将这两个结果合并为一个。Java 12 提供了一个内置收集器来实现这一功能。

Java 12 新增了一个收集器，它使用两个收集器分别收集不同类型的结果，最后通过一个 `BiFunction` 将这两个收集器的结果合并。合并后的结果即为该收集器的最终结果。`java.util.stream.Collectors` 类中的新 `teeing()` 方法返回了这样一个收集器：

```
Collector teeing (Collector downstream1, Collector downstream2, BiFunction merger)
```

其中，`downstream1` 和 `downstream2` 是两个下游收集器，用于处理流中的元素。`R1` 和 `R2` 分别是第一个和第二个下游收集器的结果类型。`R` 是最终的结果类型。`merger BiFunction` 接收第一个和第二个下游收集器的结果，其返回值即为该收集器的最终结果。

清单 8-9 包含一个完整的程序，演示了如何使用这个合并其他两个收集器结果的收集器。

```
// TeeingCollector.java
package com.jdojo.misc;
import java.util.List;
import java.util.Map;
import static java.util.stream.Collectors.averagingDouble;
import static java.util.stream.Collectors.counting;
import static java.util.stream.Collectors.joining;
import static java.util.stream.Collectors.summingDouble;
import static java.util.stream.Collectors.teeing;
public class TeeingCollector {
public static void main(String[] args) {
// 将列表中的姓名用逗号连接，并统计最终字符串中的姓名数量
String displayText = List.of("Buddy", "John", "Lisa")
.stream()
.collect(teeing(joining(","),
counting(),
(names, count) -> names + "\nTotal:" + count));
System.out.println(displayText);
// 计算双精度浮点数的总和与平均值，并将两个结果收集到
// 一个 Map.Entry 中，其中键为总和，值为平均值
Map.Entry stat = List.of(10.0, 20.0, 30.0, 40.0)
.stream()
.collect(teeing(summingDouble(x -> x.doubleValue()),
averagingDouble(n -> n),
Map::entry));
System.out.printf("sum: %f, average: %f%n",
stat.getKey(), stat.getValue());
}
}
Buddy,John,Lisa
Total:3
sum: 100.000000, average: 25.000000
清单 8-9
使用合并其他两个收集器结果的收集器
```

第一个示例使用了一个姓名列表。`teeing()` 方法的第一个参数是一个 `joining` 收集器，用于将姓名用逗号连接。第二个参数是一个 `counting` 收集器，用于统计姓名的数量。第三个参数是一个 `BiFunction`，它接收连接后的姓名和数量，并返回一个字符串。字符串的第一行包含连接后的姓名，第二行包含字符串 "Total: `<count>`"，其中 `<count>` 是姓名的数量。

```
String displayText = List.of("Buddy", "John", "Lisa")
.stream()
.collect(teeing(joining(","),
counting(),
(names, count) -> names + "\nTotal:" + count));
```

第二个示例使用了一个数字列表（`Double` 类型）。传递给 `teeing()` 方法的第一个下游收集器计算所有数字的总和。第二个下游收集器计算所有数字的平均值。第三个参数是一个 `BiFunction`，它是 `Map` 接口中 `entry()` 方法的方法引用。`entry()` 方法接收一个键和一个值，并返回一个 `Map.Entry` 对象。在此例中，键将是第一个下游收集器的结果（总和），值将是第二个下游收集器的结果（平均值）。

```
Map.Entry stat = List.of(10.0, 20.0, 30.0, 40.0)
.stream()
.collect(teeing(summingDouble(x -> x.doubleValue()),
averagingDouble(n -> n),
Map::entry));
```

## Buffer API

Java 13 为 `java.nio.Buffer` 类的子类（如 `ByteBuffer`、`CharBuffer`、`IntBuffer` 等）添加了绝对批量 get 和 put 方法。方法签名根据缓冲区类的不同而有所差异。`ByteBuffer` 类中的新方法如下所示。对于其他缓冲区类型，方法签名在返回类型和目标数组类型上有所不同。

*   `ByteBuffer get`​`(int index, byte[] dst)`

*   `ByteBuffer get`​`(int index, byte[] dst, int offset, int length)`

*   `ByteBuffer put`​`(int index, byte[] src)`

*   `ByteBuffer put`​`(int index, byte[] src, int offset, int length)`

这些方法返回 `ByteBuffer` 本身。`get()` 方法允许您将字节从缓冲区复制到指定数组，而无需更改缓冲区的位置。`put()` 方法将字节从指定数组复制到缓冲区，同样不更改缓冲区的位置。

Java 13 还为 `Buffer` 类添加了一个 `slice()` 方法，允许您指定要切片的绝对范围。其他缓冲区类会重写此方法。`ByteBuffer` 类中的该方法声明如下：

```
ByteBuffer slice (int index, int length)
```

现有的无参 `slice()` 方法从当前位置开始创建原始缓冲区的切片，而新方法允许您指定起始位置和长度。您可以将新方法视为提供绝对切片能力，而旧方法则提供相对切片能力。

Java 11 为特定类型的缓冲区类（如 `ByteBuffer`、`CharBuffer`、`IntBuffer` 等）添加了一个 `mismatch()` 方法。其在 `ByteBuffer` 类中的声明如下：

```
int mismatch (ByteBuffer that)
```

`mismatch()` 方法查找并返回此缓冲区与指定缓冲区之间第一个不匹配元素的相对索引。返回值是相对于每个缓冲区位置的，范围在 0（包含）到每个缓冲区剩余元素中较小的那个值之间。如果没有不匹配，则返回 –1。

更多详细信息，请参阅特定缓冲区类的 API 文档。



## 从路径创建文件系统

Java 支持自定义文件系统已有相当长的时间。使用文件系统提供程序创建新的 `FileSystem` 实例，该提供程序允许你将文件内容视为文件系统（例如 Zip 文件系统），但通过 `java.nio.file.FileSystems` 类中现有的工厂方法来实现这一操作较为繁琐。你必须构造一个 URI 来引用该文件。Java 13 为 `FileSystems` 类新增了以下工厂方法，允许你使用 `Path` 来引用文件：

*   `FileSystem newFileSystem`​`(Path path) throws IOException`

*   `FileSystem newFileSystem`​`(Path, Map<String,`​`?> env) throws IOException`

*   `FileSystem newFileSystem`​`(Path, Map<String,`​`?> env, ClassLoader loader) throws IOException`

清单 8-10 包含一个完整的程序，演示了 `FileSystems` 类中新方法的使用。为了简化代码，我在 `createFile()` 和 `readFile()` 方法中使用了 `throws` 子句。该程序会在当前目录下创建一个新的 `zipfstest.zip` 文件，如果该文件已存在，则会覆盖它。

```
// ZipFileSystemTest.java
package com.jdojo.misc;
import java.io.IOException;
import java.nio.file.FileSystem;
import java.nio.file.FileSystems;
import java.nio.file.Files;
import java.nio.file.Path;
import java.nio.file.Paths;
import java.util.Map;
public class ZipFileSystemTest {
public static void main(String[] args) {
// 将用作文件系统的 zip 文件的路径
Path path = Paths.get("zipfstest.zip");
System.out.println("使用 Zip 文件系统: "
+ path.toAbsolutePath());
// 将 Zip 文件系统的属性存储到 Map 中
Map env = Map.of("create", "true",
"encoding", "UTF-8");
// 创建一个 Zip 文件系统，并用它来写入和读取文件
try (FileSystem fs = FileSystems.newFileSystem(path, env)) {
String fileName = "greeting.txt";
// 如果文件不存在，则创建一个包含一些文本的文件。
createFile(fs, fileName);
// 读取 zip 文件中文件的内容
readFile(fs, fileName);
} catch (IOException e) {
e.printStackTrace();
}
}
public static void createFile(FileSystem fs, String fileName)
throws IOException {
Path filePath = fs.getPath(fileName);
if (Files.exists(filePath)) {
System.out.printf("文件 %s 存在于 %s 中。%n", filePath, fs);
return;
}
// 创建一个文件并向其中添加问候语
Files.writeString(filePath, "你好，ZIP 文件系统！");
System.out.printf("已在 %s 中创建文件 %s。%n", filePath, fs);
}
public static void readFile(FileSystem fs, String fileName)
throws IOException {
Path filePath = fs.getPath(fileName);
// 读取文件内容
String text = Files.readString(filePath);
System.out.printf("文件 %s 的内容：%n%s%n", fileName, text);
}
}
清单 8-10
使用新的 FileSystems API 创建 Zip 文件系统
```

`main()` 方法根据 `zipfstest.zip` 文件名创建了一个 `Path`。它将稍后创建的 Zip 文件系统的属性存储在一个 `Map` 中。它存储了两个属性：`"create"` 和 `"encoding"`。`"create"` 属性被设置为 `"true"`，这意味着如果 zip 文件不存在，则会创建它。`"encoding"` 属性将 `"UTF-8"` 设置为文件系统的编码。

```
Path path = Paths.get("zipfstest.zip");
// 将 Zip 文件系统的属性存储到 Map 中
Map env = Map.of("create", "true",
"encoding", "UTF-8");
```

该程序在 `try-with-resources` 块内创建了一个新的文件系统，因此文件系统在使用后会自动关闭。

```
try (FileSystem fs = FileSystems.newFileSystem(path, env)) {
// ...
} catch (IOException e) {
e.printStackTrace();
}
```

`createFile()` 方法检查文件系统中是否存在 `greeting.txt` 文件。如果文件存在，它会打印一条相应的消息。否则，它会在 `zipfstest.zip` 文件中创建此文件，并向其中写入一行问候语。

`readFile()` 方法读取 zip 文件中 `greeting.txt` 文件的内容，并将其打印到标准输出。

当我第一次运行该程序时，得到以下输出。你的输出可能有所不同。

```
使用 Zip 文件系统: C:\Java13Revealed\zipfstest.zip
已在 zipfstest.zip 中创建文件 greeting.txt。
文件 greeting.txt 的内容：
你好，ZIP 文件系统！
```

当我第二次运行该程序时，得到以下输出。你的输出可能有所不同。

```
使用 Zip 文件系统: C:\Java13Revealed\zipfstest.zip
文件 greeting.txt 存在于 zipfstest.zip 中。
文件 greeting.txt 的内容：
你好，ZIP 文件系统！
```

## JDK11 中的其他重要变更

以下是 JDK11 中的一些重要变更，当你将 Java 应用程序迁移到 JDK11 或更高版本时，这些变更可能会对你产生影响：

*   Oracle 不再提供 JRE 和 Server JRE 下载。

*   不再提供 32 位 Windows 下载。

*   JDK 中不再提供 Java Web Start、Java 插件和 Java 控制面板。

*   JavaFX 不再包含在 JDK 中。现在可以从 [`https://openjfx.io/`](https://openjfx.io/) 单独下载。

*   Java EE 和 CORBA 模块已被移除。这些模块在 JDK 9 中已被标记为待移除。因此，JAXB 和 JAX-WS 不再与 JDK11 捆绑在一起。更多详情请参阅 JEP 320，网址为 [`http://openjdk.java.net/jeps/320`](http://openjdk.java.net/jeps/320)。



## 摘要

在最近几个 JDK 版本中，若干 JDK 工具和 Java API 已被弃用或移除。JDK10 移除了 `javah` 工具。JDK11 弃用了 Nashorn JavaScript 引擎和 Pack200 工具。JDK11 移除了 `appletviewer` 工具。JDK13 弃用了 `rmic` 工具，并计划在未来版本中移除。Java 13 弃用了 Swing Motif 外观，并且在 macOS 上不再支持该外观。

`java.lang.Runtime` 类中的 `traceInstructions()` 和 `traceMethodCalls()` 方法在 Java 9 中被弃用，计划在未来版本中移除，并在 Java 13 中已被移除。

`Thread` 类中的 `destroy()` 和 `stop(Throwable)` 方法已在 Java 11 中被移除。

JDK12 引入了 JVM 常量 API，这是一个底层 API，用于处理库和工具如何描述类文件常量池中的条目，这些库和工具会操作字节码。作为开发者，你不需要在应用程序中使用此 API。该 API 位于 `java.base` 模块的 `java.lang.constant` 包中。

从 Java 11 到 Java 13，`String` 类新增了几个方法。其中许多方法用于处理多行字符串。

Java 11 为 `String` 类添加了三个用于去除前导/尾随、前导和尾随空白字符的方法：`strip()`、`stripLeading()` 和 `stripTrailing()`。

Java 11 还为 `String` 类添加了一个 `isBlank()` 方法，如果字符串仅包含空白字符，则返回 `true`，否则返回 `false`。

你可以使用 `String` 类的 `lines()` 方法来获取字符串中的行流。字符串中的一行定义为零个或多个字符后跟一个行终止符的序列，行终止符可以是 `"\n"`、`"\r"` 或 `"\r\n"`。

`String` 类中的 `repeat(int count)` 方法返回一个字符串，其内容是将原字符串重复 `count` 次后拼接而成。

Java 12 为 `String` 类添加了一个 `transform(Function<? super String, ? extends R> f)` 方法，该方法通过应用一个 `Function` 将字符串转换为另一个对象。该 `Function` 接受一个 `String` 并返回任意类型的对象。

无参的 `orElseThrow()` 方法在 Java 10 中被添加到 `Optional<T>` 类中。如果 `Optional` 包含值，则该方法返回该值。否则，它会抛出一个 `java.util.NoSuchElementException` 运行时异常。Java 11 添加了一个 `isEmpty()` 方法，如果 `Optional` 为空则返回 `true`，否则返回 `false`。

Java 11 为 `Predicate<T>` 接口添加了一个新的 `not()` 静态方法，该方法返回指定 `Predicate` 的否定。

Java 12 在 `java.base` 模块的 `java.text` 包中添加了一个 `CompactNumberFormat` 类。它是 `NumberFormat` 类的子类。它用于将十进制数字格式化为其紧凑形式。数字的紧凑形式适用于显示空间有限的情况。例如，与其将十亿显示为 1000000000，不如将其显示为紧凑形式，如 1B 或 1 billion。使用 `NumberFormat` 类中的 `getCompactNumberInstance()` 工厂方法来获取 `CompactNumberFormat` 类的实例。

Java 10 支持 Unicode 标准 8.0.0 版本。Java 11 支持 Unicode 标准 10.0.0 版本。Java 12 支持 Unicode 标准 11.0 版本，以及来自 Unicode 标准 11.0 之后第一个分配该码点的版本的日本年号码点 U+32FF。Java 13 支持 Unicode 标准 12.1 版本。

`readString()` 和 `writeString()` 方法在 Java 11 中被添加到 `Files` 类中。`readString()` 方法将文件内容读取为 `String`。`writeString()` 方法将 `CharSequence`（例如 `String` 和 `StringBuilder`）写入文件。

`mismatch()` 方法在 Java 12 中被添加到 `Files` 类中。它通过逐字节比较两个指定文件的内容来查找它们之间的不匹配。如果内容完全匹配，则返回 -1L。否则，返回第一个不匹配字节的位置。

集合 API 有两项更改。Java 10 为 `List`、`Set` 和 `Map` 接口添加了一个静态的 `copyOf()` 工厂方法，用于从另一个集合创建不可修改的 `List`、`Set` 和 `Map`。Java 11 为 `Collection` 接口添加了一个新的默认方法 `toArray(IntFunction<T[]> generator)`，可用于传入数组构造函数引用，将集合的元素复制到特定类型的数组中。

Java 10 为 `Collectors` 类添加了三个方法，它们返回一个 `Collector`，用于将元素累积到不可修改的 `List`、`Set` 和 `Map` 中：`toUnmodifiableList()`、`toUnmodifiableSet()` 和 `toUnmodifiableMap()`。Java 12 添加了另一种类型的收集器，它使用两个收集器来收集不同类型的结果，最后使用一个 `BiFunction` 合并这两个收集器的结果。合并后的结果就是该收集器的结果。`Collectors` 类中新增的 `teeing()` 方法返回这样一个收集器。

Java 13 为 `java.nio.Buffer` 类的子类（如 `ByteBuffer`、`CharBuffer`、`IntBuffer` 等）添加了绝对批量 get 和 put 方法。这些方法允许在不影响缓冲区当前位置的情况下批量读取和写入缓冲区。Java 13 还在 `Buffer` 类中添加了一个新的 `slice()` 方法，允许你指定要切片的绝对范围。

Java 13 在 `FileSystems` 类中添加了三个工厂方法，允许使用 `Path` 通过文件系统提供程序创建 `FileSystem`，该提供程序允许你将文件的内容视为文件系统，例如 Zip 文件系统。在 Java 13 之前，你必须使用 URI 来引用代表文件系统的文件，并使用 `FileSystems` 类中接受 `URI` 的其他工厂方法。

### 索引

### A

allOf() 方法 API 缓冲区 创建 文件系统，路径 创建 Zip 编码属性 JDK11 变更 JVM 常量 Appletviewer 工具 应用程序类数据共享 (AppCDS) 认证，HTTP authenticator() 方法 承载令牌 HTTP 认证 setDefault() 方法 测试

### B

BodyPublishers.noBody() 工厂方法 buildAsync() 方法 build() 方法

### C

回车符 (CR) 回车换行符 (CRLF) case 标签 类数据共享 (CDS) APPCDS 类路径 创建类列表 默认，在 JDK12 中 生成共享归档 JDK11 路径 运行应用程序 默认归档，在 JDK12 中 动态归档，在 JDK12 中 运行应用程序 两步过程 加载类 位置 模块 共享归档 Xshare 选项 类加载机制 CL 测试 内容 Fruits 类 内存 类加载器 客户端端点 创建 监听器 对等 WebSocket.Builder closeStatus() 方法 集合 转换为数组 复制元素 toArray() 方法 创建不可修改的 List Collectors 运行时类 teeing() 方法 线程类 toUnmodifiableList() 方法 连接文本块 connectTimeout() 方法 Cookie 处理器

### D, E, F

describeConstable() 方法

### G

getCompactNumberInstance() 方法 GET() 方法 getPasswordAuthentication() 方法

### H

HTTP 客户端 API 认证框架 参见 认证，HTTP 优势 BodyHandlers.ofString() 静态方法 组件 创建 认证器 配置 默认配置 执行器 HttpClient 类，方法 HTTP 请求 IETF 问题 实例，50 队头阻塞 jdojo.http.client 模块 newHttpClient() 静态方法 HTTP 请求 BodyPublisher 接口 builder 对象 setHeaders() 方法 expectContinue(true) 方法 工厂方法 headers() 方法 method() 方法 方法 参数 POST 方法 send()/sendAsync() 方法 timeout(Duration timeout) 方法 version() 方法 HTTP 响应 响应体 参见 响应体 状态码/头部

### I

不一致的空白字符 互联网工程任务组 (IETF) isBlank() 方法 isPresent() 方法



### J, K

`javah` 工具 Java 启动器 Java 版本方案 附加信息 构建信息 模型 旧字符串 预发布信息 产品版本字符串 字符串解析 系统属性 版本号

### L

换行符 (LF) `lines()` 方法 区域数据标记语言 (LDML)

### M

内存 类加载器 `mismatch()` 方法

### N

Nashorn JavaScript 引擎 `newBuilder()` 静态方法 `newWebSocketBuilder()` 方法 非标准源文件名 `not()` 静态方法

### O

`onClose()` 方法 `onError()` 方法 `onPong()` 方法 `onXxx()` 方法 `orElseThrow()` 方法

### P, Q

`parse()` 方法 模式匹配 多态表达式 `previousResponse()` 方法

### R

`readString()` 方法 重定向策略 请求优先级 `resolveConstantDesc()` 方法 响应体 异步请求 下载/附件参数 保存，文件 服务器推送 服务器重定向 `rmic` 工具 `Runtime.Version` 类 `compareTo()` 方法 `jdojo.version.scheme` 模块 比较规则 `static parse(String vstr)` 方法 `static version()` 方法 `version()` 方法 版本测试类

### S

`sendClose()` 方法 `send()`/`sendAsync()` 方法 `sendText()` 方法 `sendXxx()` 方法 Shebang 文件 `chmod` 命令 类文件模式 `echo` 命令 Java 源文件 `main()` 方法 操作系统 `pfp` 可执行文件 `pwd` 命令 短版本字符串 `slice()` 方法 源文件模式 类声明 命令行参数文件 编译器 `jdk.compiler` 模块 `MultiFileTest` 类 传递参数 选择 设置 顶层类 SSL 上下文 SSL 参数 字符串字面量 String API `formatted()` 实例方法 `stripIndent()`/`indent()` 方法 文本块 `translateEscapes()` 实例方法 String 类 紧凑数字格式 Files 类 `isBlank()` 方法 `lines()` 方法 方法 可选类 重复字符串 去除空白 `transform()` 方法 Unicode 标准 `strip()` 方法 `subprotocols()` 方法 Switch 表达式 Switch 带标签的规则 Switch 语句 `break` 语句 `case` 标签 控制流语句 模式匹配 语义 `switch` 块 Switch 语法 方法调用 预览特性 规则 源代码 `switch` 表达式 `throw` 语句 传统 `switch` `yield` 语句

### T, U

`teeing()` 方法 文本块 开始定界符 String 类 语法 转换 参见转换规则 结束定界符 HTML 代码 模块声明 空白 `toArray()` 方法 `toArray(T[] a)` 方法 工具 已弃用 Nashorn JavaScript 引擎 Pack200 API `rmic` 已移除 Appletviewer `javah` `toUnmodifiableList()` 方法 传统 Switch `break` 语句 `case` 标签 表达式 穿透特性 源代码 `str` 变量 `System.out.println()` 语句 `yield` 语句 `transform()` 方法 转换规则 转义序列 附带空白 行终止符 `trim()` 方法 类型推断 向后兼容性 声明 `final` 变量 定义 示例 声明局部变量 JShell 会话 模块声明 Lambda 表达式 使用 `var` 的规则 无数组维度 无数组初始化器 无多个变量 非可表示类型 无 `null` 类型变量 无多态表达式 无引用变量 无未初始化变量 对象 使用 `var`

### V

`static version()` 方法 版本字符串

### W, X

WebSocket 客户端端点 参见客户端端点 客户端/服务器交互 `onText()` 方法 对等协议 `sendText()` 方法 服务器端点 WebSocket 会话 `writeString()` 方法

### Y, Z

`yield` 语句
