# 14. System

`java.lang.System` 类提供了对系统级功能的访问，例如数组复制和虚拟机关闭。本章将向您介绍七种功能。

注意

`System` 是一个工具类的示例，因为它的所有方法和字段都被声明为 `static`。



## 数组复制

第 5 章向你介绍了数组。你了解到数组具有固定大小，并且知道要调整数组大小，必须创建一个所需大小的新数组，然后将当前数组中的元素复制到新数组中，最后销毁当前数组。（在本章后面讨论垃圾回收时，我会谈到数组销毁。）

让我们聚焦于调整数组大小。以下是完成此任务的一种方法：

```
int[] src = new int[] { 20, 30, 50 };
int[] dest = new int[10];
for (int i = 0; i < src.length; i++)
dest[i] = src[i];
```

这段代码片段首先创建并初始化了一个 32 位整数的 `src` 数组。该数组包含三个元素：`20`、`30` 和 `50`。

然后，该片段创建了一个 `int` 类型的 `dest` 数组。该数组包含 10 个元素，每个元素（在后台）都被初始化为 0。

最后，该片段进入一个 `for` 循环。这个循环的头部创建了局部变量 `i` 并将其初始化为 0。然后它将 `i` 与 `src.length`（`src` 数组的长度）进行比较。如果 `i` 小于 `src.length`，则循环继续，执行赋值语句 `dest[i] = src[i];`，将 `src[i]` 的整数复制到对应的 `dest[i]` 位置。`dest` 的最终结果是 `20, 30, 50, 0, 0, 0, 0, 0, 0, 0`。

有一种更快的执行数组复制的方法：`System` 类的 `arraycopy()` 方法。以下是该方法的签名：

```
void arraycopy(Object src, int srcPos, Object dest, int destPos, int length)
```

`arraycopy()` 方法将从 `src` 数组中从零基偏移量 `srcPos` 开始的 `length` 个元素，复制到 `dest` 数组中从零基偏移量 `destPos` 开始的位置。当 `src` 或 `dest` 包含空引用时，此方法会抛出 `java.lang.NullPointerException`；当复制导致访问数组边界之外的数据时，会抛出 `java.lang.ArrayIndexOutOfBoundsException`；当由于类型不匹配而无法将 `src` 数组中的元素存储到 `dest` 数组时，会抛出 `java.lang.ArrayStoreException`。

注意

很容易拼错 `arraycopy()`。你可能会输入大写字母 `C` 而不是小写字母 `c`，这最终会导致编译器错误。

我创建了一个 `ArrayResize` 应用程序，演示了用于复制数组的传统方法和 `arraycopy()` 技术。清单 14-1 展示了其源代码。

```
public class ArrayResize
{
public static void main(String[] args)
{
int[] src = new int[] { 20, 30, 50 };
int[] dest = new int[10];
for (int i = 0; i < src.length; i++)
dest[i] = src[i];
dump(dest);
int[] dest2 = new int[10];
System.arraycopy(src, 0, dest2, 0, src.length);
dump(dest2);
}
static void dump(int[] array)
{
for (int i = 0; i < array.length; i++)
System.out.print(array[i] + " ");
System.out.println();
}
}
清单 14-1
ArrayResize.java
```

源代码展示了 `main()` 和 `dump()` 实用方法。`main()` 方法运行应用程序，而 `dump()` 是一个辅助方法，用于支持 `main()`。

在创建 `src` 和 `dest`，并以传统方式将 `src` 复制到 `dest` 之后，`main()` 将 `dest` 转储到控制台。然后它创建了 `dest2`，第二个目标数组。

接着，`main()` 执行 `System.arraycopy(src, 0, dest2, 0, src.length);`。第一个参数是对源数组 (`src`) 的引用，第三个参数是对目标数组 (`dest`) 的引用。

第二个和第四个参数传递的是 0。第一个 `0` 是 `src` 中开始读取 `int` 值的起始索引。第二个 `0` 是 `dest` 中开始写入 `int` 值的起始索引。

最后一个参数是要复制的源数组中的元素数量。该参数通过执行 `src.length` 获得。如果你还记得，`.` 是成员选择运算符。它正在访问 `src` 数组的只读成员 `length`。请注意，`length` 是数组对象的唯一成员。

按如下方式编译清单 14-1：

```
javac ArrayResize.java
```

按如下方式运行生成的应用程序：

```
java ArrayResize
```

你应该会看到以下输出：

```
20 30 50 0 0 0 0 0 0 0
20 30 50 0 0 0 0 0 0 0
```

第一行是 `dest` 的输出。第二行是 `dest2` 的输出。



## 当前时间与纳秒时间

`long currentTimeMillis()` 方法返回自 1970 年 1 月 1 日 00:00:00 UTC 以来经过的当前系统时间（以毫秒为单位）。

注意

UTC 代表协调世界时（[`http://en.wikipedia.org/wiki/Coordinated_Universal_Time`](http://en.wikipedia.org/wiki/Coordinated_Universal_Time)）。也许您期望其缩写是 CUT，但 UTC 实际上是 CUT 与法文版本 TUC（Temps Universel Coordonné）之间的折中方案。请访问 [`www.space.com/what-is-utc.xhtml`](http://www.space.com/what-is-utc.xhtml) 了解更多关于 UTC 的信息。

也许您对前面日期时间中的“1970 年 1 月 1 日 00:00:00”这部分感到好奇。为什么 `currentTimeMillis()` 返回的时间是相对于这个值的？[`http://kb.narrative.io/what-is-unix-time`](http://kb.narrative.io/what-is-unix-time) 指出，1970 年 1 月 1 日 00:00:00 UTC 被称为 *Unix 纪元*。早期的 Unix 工程师随意选择了这个日期，因为他们需要为时间的起点设定一个统一日期，而 1970 年的元旦似乎最为方便。

`currentTimeMillis()` 返回的值本身并无意义。幸运的是，您可以通过将其传递给 `java.util.Date` 构造函数，将得到的长整型转换为人类可读的字符串，如下所示：

```
String date = new Date(System.currentTimeMillis()).toString();
```

关于 DATE 类

JDK 1.0（Java 开发工具包的首个版本）引入了 `Date` 类，作为一种以平台无关方式表示日期和时间的方法。该类的各种问题（包括糟糕的 API 设计）导致 JDK 8 引入了取代 `Date` 的日期和时间 API。因此，`Date` 的大部分内容已被弃用。（本书不讨论日期和时间 API，因为它是一个高级特性。）

`Date` 中未被弃用的一个方法是 `toString()` 方法，它返回 `currentTimeMillis()` 值的字符串表示形式。该表示形式遵循以下格式：

**dow mon dd hh:mm:ss zzz yyyy**

其中

*   **dow** 是星期几（Sun、Mon、Tue、Wed、Thu、Fri、Sat）。

*   **mon** 是月份（Jan、Feb、Mar、Apr、May、Jun、Jul、Aug、Sep、Oct、Nov、Dec）。

*   **dd** 是月份中的日期（01 到 31），以两位十进制数字表示。

*   **hh** 是小时（00 到 23），以两位十进制数字表示。

*   **mm** 是小时内的分钟（00 到 59），以两位十进制数字表示。

*   **ss** 是分钟内的秒（00 到 61），以两位十进制数字表示。

*   **zzz** 是时区（可能反映夏令时）。标准时区缩写包括 `Date` 的 `parse(String s)` 方法所识别的那些。如果时区信息不可用，则 **zzz** 为空：它不包含任何字符。

*   **yyyy** 是年份，以四位十进制数字表示。

我很好奇为什么 **ss** 字段允许 60 和 61，于是向 ChatGPT 3.5 询问了这个异常现象：“为什么 `java.util.Date` 的 `toString` 方法似乎允许秒值为 60 或 61？”ChatGPT 回答如下：

“在 Java 中，`java.util.Date` 类并未明确阻止您将秒值设置为 60 或 61。这种行为是 `java.util.Date` 实现方式的结果。

`java.util.Date` 将时间点表示为自‘纪元’（1970 年 1 月 1 日 00:00:00 GMT）以来的毫秒数。它没有单独的秒、分等字段。当您将秒设置为大于 59 的值时，Java 会自动相应地调整分钟和秒的组成部分。

例如，如果您将秒设置为 60，它会将分钟增加 1，并将秒设置为 0。类似地，如果您将秒设置为 61，它会将分钟增加 1，并将秒设置为 1。

此行为与闰秒的处理方式有关。闰秒会被添加或从协调世界时（UTC）中减去，以考虑地球自转的不规则性。在添加闰秒的情况下，`java.util.Date` 对象中的秒值可能会暂时达到 60 或 61。

然而，需要注意的是，并非所有 Java 平台都以相同方式处理闰秒，其行为可能取决于底层操作系统和 Java 虚拟机（JVM）的实现。

为了精确处理日期和时间，特别是当您处理涉及闰秒的场景时，建议使用 Java 8 中引入的较新日期和时间 API，该 API 属于 `java.time` 包。`java.time` 中的 `LocalDateTime` 等类基于一个简化的模型运行，该模型没有相同的闰秒行为。”

我可以保证这个答案的正确性。

`long nanoTime()` 方法返回 JVM 高分辨率时间源的值，它恰好是最精确的系统计时器。此方法返回的值表示自某个固定但任意的原点时间（可能在未来，因此值可能为负）以来经过的纳秒数。在 JVM 的一个实例中，此方法的所有调用都使用相同的原点；其他虚拟机实例可能使用不同的原点。

```
public class TimeDemo
{
public static void main(String[] args)
{
System.out.println("自 1970 年 1 月 1 日 UTC 午夜以来的毫秒数：" +
System.currentTimeMillis());
System.out.println("自 1970 年 1 月 1 日 UTC 午夜以来的毫秒数（人类可读格式）：" +
new java.util.Date(System.currentTimeMillis()));
System.out.println("自某个任意原点时间以来的纳秒数：" + System.nanoTime());
// 计算以 0.1 弧度增量计算并输出一个圆的所有正弦值 100 次所需的时间（以纳秒为单位）：
long start = System.nanoTime();
for (int i = 0; i < 100; i++)
for (double angle = 0; angle < Math.PI * 2; angle += 0.1)
System.out.println(Math.sin(angle));
long end = System.nanoTime();
System.out.println("经过的时间（以纳秒为单位）：" + (end - start + 1));
System.out.println("经过的时间（以秒为单位）：" +
((end - start + 1) / (1000 * 1000 * 1000)));
}
}
清单 14-2
TimeDemo.java
```

清单 14-2 的 `main()` 方法首先获取并输出当前时间（以毫秒为单位）。然后，它获取当前时间（以毫秒为单位），将此时间转换为 `Date` 对象，调用 `Date` 对象的 `toString()` 方法（在后台）将此对象转换为当前时间的字符串表示形式，将此字符串连接到作为 + 运算符左操作数的字符串字面量上——多态性使得该运算符根据其操作数的类型执行整数加法、浮点加法或字符串连接——并通过 `System.out.println()` 输出连接后的字符串。

`main()` 方法接下来调用 `System.nanoTime()` 以返回经过的时间（以纳秒为单位），并将其输出。

最后，`main()` 计算以 0.1 弧度增量计算并输出一个圆的所有正弦值所需的时间（以纳秒为单位）。它执行此操作 100 次。

该计算首先获取开始时间，然后执行计算并输出这些正弦值的代码，最后在计算/输出逻辑之后获取结束时间。从结束时间中减去开始时间，并在结果上加 1 以获得经过的时间（以纳秒为单位）。（将纳秒数除以十亿 [1000 * 1000 * 1000] 可将该值转换为秒数。）

注意



在动画（可能是游戏）中确定帧率时，你经常会用到这样的逻辑。请查阅维基百科的“帧率”主题（[`http://en.wikipedia.org/wiki/Frame_rate`](http://en.wikipedia.org/wiki/Frame_rate)）来了解帧率。

按如下方式编译清单 14-2：

```
javac TimeDemo.java
```

按如下方式运行该应用程序：

```
java TimeDemo
```

你应该会观察到与下面所示类似的输出（通过省略大部分正弦值进行了缩写）——时间值会有所不同：

```
Number of milliseconds since midnight, January 1, 1970 UTC: 1695386074105
Number of milliseconds since midnight, January 1, 1970 UTC in human-readable format: Fri Sep 22 07:34:34 CDT 2023
Number of nanoseconds since some arbitrary origin time: 423955941265551
0.0
0.09983341664682815
0.19866933079506122
0.2955202066613396
0.3894183423086505
0.479425538604203
...
-0.5506855425976414
-0.4646021794137613
-0.37387666483024096
-0.27941549819893097
-0.18216250427210112
-0.0830894028175026
Elapsed time (in nanoseconds): 189494701
Elapsed time (in seconds): 0
```

看起来几乎没有时间（以纳秒计）或没有时间（以秒计）过去。但事实并非如此，因为我将程序的输出重定向到了一个文件，这导致了 0 秒的结果。如果我没有执行此重定向，我（在我的平台上）会看到一两秒的已用时间。

## 垃圾回收

`void gc()` 方法会告知 JVM 尽力回收未使用的对象（包括数组对象），以便它们当前占用的内存可以被重新利用。当从该方法调用返回时，JVM 已尽最大努力回收了所有未使用对象的内存空间。

无法保证 JVM 的努力会回收任何特定数量的未使用对象、回收任何特定数量的空间，或者在方法返回之前（或之后）的任何特定时间完成（如果确实能完成的话）。

清单 14-3 展示了一个演示 `gc()` 的应用程序的源代码。

```
public class GCDemo
{
public final static int MAXMB = 50;
public final static int MAXTRIES = 1000;
public static void main(String[] args)
{
class LargeObject
{
private byte[] memory = new byte[MAXMB * 1024 *1024]; // 将 50
// 兆字节转换为
// 字节
@Override
public void finalize()
{
System.out.println("Large object has been finalized.");
}
}
LargeObject lo = new LargeObject();
// 证明此时没有对 lo 引用的 LargeObject 进行垃圾回收。
System.out.println("Performing garbage collection before assigning " +
"null to lo.");
for (int i = 0; i < MAXTRIES; i++)
System.gc();
// 将 lo 赋值为 null，以断开对象与变量的连接，使其可被垃圾回收。
System.out.println("Assigning null to lo.");
lo = null;
// 证明此时对 lo 引用的 LargeObject 进行了垃圾回收。
// （你可能需要为 MAXTRIES 分配更大的值才能看到此证明。）
System.out.println("Performing garbage collection after assigning " +
"null to lo.");
for (int i = 0; i < MAXTRIES; i++)
System.gc();
}
}
清单 14-3
GCDemo.java
```

清单 14-3 中的 `GCDemo` 类首先声明了一对常量，分别声明了最大兆字节数（`MAXMB`）和最大尝试次数（`MAXTRIES`）。我将在本教程后面介绍这些常量。

接下来，`GCDemo` 声明了一个 `LargeObject` 内部类。将 `LargeObject` 设为内部类很有用，因为它与 `GCDemo` 关系密切。

`LargeObject` 声明了一个 `memory` 字段，并将其初始化为一个字节数组，其大小为 `MAXMB` 兆字节。在这里使用常量很有用，以防我以后想更改大小，并且不想浪费时间试图在源代码中追踪 `50` 出现的位置。

`LargeObject` 还重写了它从 `Object` 继承的 `finalize()` 方法。此方法在回收从 `LargeObject` 实例化的对象所属的内存之前被调用。

`finalize()` 方法调用 `System.out.println()` 并附带一条消息，以提醒用户该对象即将被销毁。

此时，`GCDemo` 实例化 `LargeObject` 并将其引用赋给局部变量 `lo`。然后它输出一条消息，并尝试通过 `System.gc();` 调用垃圾回收器 `MAXTRIES` 次。你不会看到关于大对象被终结的消息。

接着输出另一条关于将 `null` 赋给 `lo` 的消息，然后执行此赋值操作。

最后，`GCDemo` 输出其最后一条消息（除了 `LargeObject` 的 `finalize()` 方法输出的消息之外），然后尝试调用垃圾回收器 `MAXTRIES` 次。这一次，你应该会看到关于大对象被终结的消息（你可能需要增加分配给 `MAXTRIES` 的值）。

按如下方式编译 `GCDemo.java`：

```
javac GCDemo.java
```

忽略以下编译器警告：

```
GCDemo.java:15: warning: [removal] finalize() in Object has been deprecated and marked for removal
public void finalize()
^
1 warning
```

按如下方式运行生成的应用程序：

```
java GCDemo
```

你应该会观察到以下输出：

```
Performing garbage collection before assigning null to lo.
Assigning null to lo.
Performing garbage collection after assigning null to lo.
Large object has been finalized.
```

如果在输出第一条“`Performing`”消息之后以及“`Large object has been finalized.`”消息之后需要等待一段时间，请不要感到惊讶。（垃圾回收器可能需要一段时间才能运行。）

## 行分隔符

`String lineSeparator()` 方法返回依赖于系统的行分隔符字符串。它总是返回相同的值，即系统属性 `line.separator` 的初始值。（我将在本章后面探讨系统属性。）

在 Linux/Unix 操作系统上，此方法返回 `\n`。在 Microsoft Windows 操作系统上，它返回 `\r\n`。

提示

使用 `lineSeparator()` 返回的字符串，而不是在源代码中硬编码 `\r` 和/或 `\n`，以保持你的应用程序在 Linux/Unix 和 Windows 之间可移植。

清单 14-4 展示了一个获取行分隔符并以十进制表示法打印它的应用程序的源代码。

```
public class ShowLineSeparator
{
public static void main(String[] args)
{
String lineSeparator = System.lineSeparator();
System.out.print("Line separator: ");
for (int i = 0; i < lineSeparator.length(); i++)
System.out.print((int) lineSeparator.charAt(i) + " ");
System.out.println();
}
}
清单 14-4
ShowLineSeparator.java
```

`ShowLineSeparator` 的 `main()` 方法首先调用 `System.lineSeparator()` 来获取行分隔符字符串。它将此字符串的引用存储在 `lineSeparator` 局部变量中。

接下来，`main()` 为行分隔符字符串输出一个标签，然后进入一个 `for` 循环。此循环遍历字符串的长度，为每个字符串字符输出一个十进制整数。

表达式 `(int) lineSeparator.charAt(i) + " "` 的求值顺序是 `((int) lineSeparator.charAt(i)) + " "`。换句话说，调用 `lineSeparator.charAt(i)` 返回一个字符，然后将其转换为整数。该整数通过字符串连接运算符（`+`）转换为字符串，并在该字符串后附加一个空格字符。

按如下方式编译清单 14-4：

```
javac ShowLineSeparator.java
```

按如下方式运行生成的应用程序：

```
java ShowLineSeparator
```

我在我的 Windows 平台上观察到以下输出：

```
Line separator: 13 10
```



## 标准 I/O

*标准 I/O* 是一个通信框架，它通过字节流（如果你愿意，也可以称之为通道）将正在运行的程序与其输入源连接起来。它还将程序连接到其正常输出和错误输出目的地。

注意

`System` 声明了 `in`、`out` 和 `err` 类字段，这些字段将程序连接到其输入源、输出目的地和错误目的地。`in` 字段的类型是 `java.io.InputStream`；`out` 和 `err` 字段的类型是 `java.io.PrintStream`。

在本节之外，我不会在本书中深入讨论 `InputStream` 和 `PrintStream`。相反，我建议你参考我的 *Java I/O, NIO and NIO.2* 一书，我在其中更详细地讨论了这些以及其他与 I/O 相关的类型。（请参阅 [`www.amazon.ca/Java-NIO-NIO-2-JEFF-FRIESEN/dp/1484215664/`](http://www.amazon.ca/Java-NIO-NIO-2-JEFF-FRIESEN/dp/1484215664/) 获取本书。）

我希望更新 *Java I/O, NIO and NIO.2* 并将其重命名为 *Learn Java I/O, NIO and NIO.2*，使其成为“Learn Java …”系列的一部分。

### 标准输入

一个正在运行的程序从*标准输入*接收其输入，这是一种抽象了输入源的操作系统机制。该机制默认为控制台的键盘，但可以*重定向*到文件或其他输出设备。

考虑第 13 章的 `WC` 应用程序。它调用 `System.in.read()` 从标准输入读取字节。这些字节可能来自键盘、文件或其他来源。程序不知道也不关心来源。它只是“看到”一个字节流并对其执行某些操作。

注意

各种字符编码标准用于对字符进行编码。例如，ASCII（美国信息交换标准码——参见 [`http://en.wikipedia.org/wiki/ASCII`](http://en.wikipedia.org/wiki/ASCII)）在 Unicode（参见 [`en.wikipedia.org/wiki/Unicode`](http://en.wikipedia.org/wiki/Unicode)）出现之前是主要的字符编码标准。

ASCII 使用 8 位字节中的 7 位来编码一个字符。相比之下，Unicode 最初使用两个字节来编码一个字符（这就是为什么 Java 的 `char` 类型占用 16 位），但现在最多使用四个字节。`java.lang.Character` 类对此进行了补偿。（有关 `Character` 的更多信息，请查看 [`http://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/Character.xhtml`](http://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/Character.xhtml)。）

`System.in` 引用 `System` 的 `in` 类字段，其类型为 `InputStream`。

`InputStream` 提供了一个 `int read()` 方法，该方法从输入源读取一个字节，并将该字节作为 32 位整数（`int`）返回。当到达字节流的末尾时，它返回 -1。当发生 I/O 错误（例如尝试读取已关闭的文件源）时，它会抛出 `java.io.IOException`。

假设你按如下方式运行 `WC` 应用程序：

```
java WC
```

在此次执行中，`WC` 的输入（通过 `System.in.read()` 方法调用）来自键盘。

标准 I/O 允许你将标准输入从默认的控制台键盘重定向到文件或其他输入源。要使用 `WC` 实现这一点，你需要指定 `<` 符号，后跟输入源的名称，如下所示：

```
java WC <words.txt
```

这里，输入来自一个名为 `words.txt` 的文件。

注意

`System` 提供了 `void setIn(InputStream in)` 方法，用于将标准输入重定向到另一个流。例如，`System.setIn(new FileInputStream("words.txt"));` 等同于 `<words.txt`。

`java.io.FileInputStream` 类允许你从文件获取输入。我在本书中不讨论这个类。

### 标准输出

一个正在运行的程序将其非错误输出发送到*标准输出*，这是一种抽象了非错误输出目的地（也称为*接收器*）的操作系统机制。该机制默认为控制台的屏幕，但可以重定向到文件或其他输出设备。

考虑第 13 章的 `WC` 应用程序。它调用 `System.out.println()` 将字符串写入标准输出。该字符串被发送到文件或其他接收器。程序不知道也不关心接收器。

`System.out` 引用 `System` 的 `out` 类字段，其类型为 `PrintStream`。

`PrintStream` 提供了各种 `print()` 和 `println()` 方法，用于将各种类型的数据写入接收器。例如，`print(double d)` 写入一个双精度浮点值，而 `println(String s)` 写入一个字符串。

注意

`print()` 和 `println()` 之间的区别在于，`println()` 在写入其参数后会写入一个换行符（字面表示为 `\n`）。换行符会导致*光标*（一个可移动的指示器，指示输入输入或输出发送的位置）定位到下一行输入或输出的开头。它被写成一个特定于平台的字节序列，在 Windows 平台上为 13 后跟 10，在 Unix/Linux 平台上为 10。

你可以通过调用不带参数的 `System.out.println()` 方法来写入一个空行以分隔输出行。此方法输出我在本章前面提到的行分隔符。

假设你按如下方式运行 `WC` 应用程序：

```
java WC
```

在此次执行中，`WC` 的输出被发送到屏幕（通过 `System.out.println()` 方法调用）。

标准 I/O 允许你将标准输出从默认的控制台屏幕重定向到文件或其他输出接收器。要使用 `WC` 实现这一点，你需要指定 `>` 符号，后跟输出接收器的名称，如下所示：

```
java WC >stats.txt
```

这里，输出被发送到一个名为 `stats.txt` 的文件。

注意

`System` 提供了 `void setOut(PrintStream out)` 方法，用于将标准输出重定向到另一个流。例如，`System.setOut(new FileOutputStream("stats.txt"));` 等同于 `>stats.txt`。

`java.io.FileOutputStream` 类允许你将输出发送到文件。我在本书中不讨论这个类。



#### 格式化输出

`PrintStream` 类还提供了 `PrintStream printf(String format, Object... args)` 方法来实现格式化输出。

注意

JDK 5 引入了 `printf()` 方法，其模式借鉴了 C 语言的 `printf()` 函数。请查阅 [`http://en.wikipedia.org/wiki/Printf`](http://en.wikipedia.org/wiki/Printf) 以了解更多关于此函数的信息。

`printf()` 方法的实现与 `java.util.Formatter` 类（同样由 JDK 5 引入）协同工作，以实现格式化输出。因此，`printf()` 是一个*便捷方法*的示例。

`format` 参数标识一个格式字符串，它由字面文本和格式说明符组成。每个格式说明符以 `%` 符号开头，并以一个转换字母结尾。例如，`%d` 表示需要一个十进制整数。同样，`%s` 表示需要一个字符串。

注意

有关格式字符串和格式说明符的更多信息，请查阅 JDK 21 关于 `Formatter` 类的 API 文档（[`http://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/Formatter.xhtml`](http://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/Formatter.xhtml)）。

`args` 参数是一个可变参数列表。每个参数的类型必须与其匹配的格式说明符兼容。

清单 14-5 中的 `FormattedOutputDemo` 应用程序演示了格式化输出。

```
public class FormattedOutputDemo
{
public static void main(String[] args)
{
int i = 10;
System.out.printf("i = [%d]%n", i);
System.out.printf("i = [%5d]%n", i);
System.out.printf("i = [%05d]%n", i);
System.out.printf("i = [%-5d]%n", i);
String s = "hello, world";
System.out.printf("s = [%s]%n", s);
System.out.printf("s = [%30s]%n", s);
}
}
清单 14-5
FormattedOutputDemo.java
```

`FormattedOutputDemo` 的 `main()` 方法首先声明了局部 `int` 变量 `i` 并将其初始化为 `10`。然后它执行了四次 `System.out.printf()` 来演示格式化该整数的四种方式：

*   `System.out.printf("i = [%d]%n", i);`：默认格式
*   `System.out.printf("i = [%5d]%n", i);`：在五个字符宽的字段中格式化，前导空格
*   `System.out.printf("i = [%05d]%n", i);`：在五个字符宽的字段中格式化，前导零
*   `System.out.printf("i = [%-5d]%n", i);`：在五个字符宽的字段中左对齐格式化

在每种情况下，格式字符串都以 `%n` 结尾。此格式说明符表示要输出一个换行符。

接下来，`main()` 声明了局部 `String` 变量 `s` 并将其初始化为 `"hello, world"`。然后它执行了两次 `System.out.printf()` 来演示格式化该字符串的两种方式：

*   `System.out.printf("s = [%s]%n", s);`：默认格式
*   `System.out.printf("s = [%30s]%n", s);`：在 30 个字符宽的字段中格式化，前导空格

按如下方式编译清单 14-5：

```
javac FormattedOutputDemo.java
```

按如下方式运行应用程序：

```
java FormattedOutputDemo
```

您应该会看到以下输出：

```
i = [10]
i = [   10]
i = [00010]
i = [10   ]
s = [hello, world]
s = [                  hello, world]
```

### 标准错误

正在运行的程序会将其错误输出发送到*标准错误*，这是一种操作系统机制，用于抽象错误输出的目的地。该机制默认指向控制台屏幕，但可以重定向到文件或其他输出设备。

考虑第 13 章的 `TR` 应用程序。它调用 `System.out.println()` 将用法错误消息写入标准输出。相反，将此消息写入标准错误更为合适，以便改善程序输出的组织性。

注意

标准输出允许您将非错误输出发送到一个目的地，而标准错误允许您将错误输出发送到另一个目的地。这种分离有助于您组织输出，从而可以专注于错误消息，而不会被非错误输出分散注意力。

`System.err` 引用 `System` 的 `err` 类字段，其类型为 `PrintStream`。您可以使用 `PrintStream` 的 `print()` 和 `println()` 方法将各种类型的面向错误的数据写入接收端。

假设您创建了一个新版本的 TR，将以下代码片段：

```
if (args.length == 0)
{
System.out.println("usage: java TR sequence of words");
return;
}
```

替换为：

```
if (args.length == 0)
{
System.err.println("usage: java TR sequence of words");
return;
}
```

我已将 `out` 和 `err` 加粗，以便您看到需要更改的内容。

按照第 13 章中的方式编译并运行 `TR`。当您不指定任何命令行参数时，您会注意到以下错误消息：

```
usage: java TR sequence of words
```

如果您尝试通过以下命令行将其重定向到文件：

```
java TR >out
```

您会注意到 `out` 是空的。

但是，如果您尝试通过以下命令行将错误消息重定向到文件：

```
java TR 2>out
```

您会发现错误消息出现在文件中。

`2>out` 中的数字 `2` 标识了标准错误流。

注意

`System` 提供了 `void setErr(PrintStream out)` 方法，用于将标准错误输出重定向到另一个流。例如，`System.setErr(new FileOutputStream("out"));` 等同于 `2>out`。



## 系统属性

*系统属性*是一个键值对，用于提供 JVM 运行时环境当前某个方面的信息。多个 `System` 方法和 `java.util.Properties` 类有助于处理系统属性。以下是 `System` 提供的方法：

*   `String clearProperty(`[`String`](https://docs.oracle.com/en/java/javase/20/docs/api/java.base/java/lang/String.xhtml) `key)`：移除由指定 `key` 标识的系统属性。

*   `static` [`Properties`](https://docs.oracle.com/en/java/javase/20/docs/api/java.base/java/util/Properties.xhtml) `getProperties()`：返回当前的系统属性集。如果当前没有系统属性集，则会首先创建并初始化一个系统属性集。

*   `static` [`String`](https://docs.oracle.com/en/java/javase/20/docs/api/java.base/java/lang/String.xhtml) `getProperty(`[`String`](https://docs.oracle.com/en/java/javase/20/docs/api/java.base/java/lang/String.xhtml) `key)`：获取由指定 `key` 标识的系统属性。

*   `static` [`String`](https://docs.oracle.com/en/java/javase/20/docs/api/java.base/java/lang/String.xhtml) `getProperty(`[`String`](https://docs.oracle.com/en/java/javase/20/docs/api/java.base/java/lang/String.xhtml) `key,` [`String`](https://docs.oracle.com/en/java/javase/20/docs/api/java.base/java/lang/String.xhtml) `defaultValue)`：获取由指定 `key` 标识的系统属性。如果系统属性不存在，则返回 `defaultValue` 引用的字符串。

*   `static void setProperties(`[`Properties`](https://docs.oracle.com/en/java/javase/20/docs/api/java.base/java/util/Properties.xhtml) `props)`：将系统属性设置为 `props` 的内容。

*   `static` [`String`](https://docs.oracle.com/en/java/javase/20/docs/api/java.base/java/lang/String.xhtml) `setProperty(`[`String`](https://docs.oracle.com/en/java/javase/20/docs/api/java.base/java/lang/String.xhtml) `key,` [`String`](https://docs.oracle.com/en/java/javase/20/docs/api/java.base/java/lang/String.xhtml) `value)`：将由指定 `key` 标识的系统属性设置为 `value`。`System.setProperty()` 仅为当前运行时设置属性。程序退出后，这些属性将不再设置。如果需要在会话之间持久化属性，请考虑使用基于属性的配置文件（稍后讨论）或其他形式的持久化存储。

警告

关于 `setProperty()` **和** `setProperties()`**，除非另有说明，更改标准系统属性可能会导致不可预测的结果**。属性值可能在初始化期间或首次使用时被缓存。在初始化之后使用 `getProperties()`、`setProperties(Properties)`、`setProperty(String, String)` 或 `clearProperty(String)` 设置标准属性可能不会产生预期效果。

设置系统属性在各种配置中可能很有用，但重要的是要负责任地使用它们，并确保它们不会干扰 JVM 或应用程序的正常运行。

清单 14-6 中的 `PropertiesDemo` 应用程序演示了所有这些方法。

```
import java.util.Properties;
public class PropertiesDemo
{
public static void main(String[] args)
{
// 演示 setProperty()、getProperty() 和 clearProperty()。
System.setProperty("temp.location", "C:\\temp");
showProperty("清除前的临时位置", "temp.location");
System.clearProperty("temp.location");
showProperty("清除后的临时位置", "temp.location");
// 演示 getProperties() 和 Properties。
Properties properties = System.getProperties();
showProperty("Java 版本", properties.getProperty("java.version"));
showProperty("操作系统名称", properties.getProperty("os.name"));
showProperty("用户主目录", properties.getProperty("user.home"));
System.out.println();
properties.list(System.out);
// 更多 getProperty() 的演示。
showProperty("Java 版本", "java.version");
showProperty("操作系统名称", "os.name");
showProperty("Java 类路径", "java.class.path");
showProperty("行分隔符", "line.separator");
showProperty("用户账户名", "user.name");
showProperty("用户主目录", "user.home");
// 演示 setProperty()。
System.setProperty("someprop", "somevalue");
showProperty("某个属性", "someprop");
// 演示 setProperties()。
Properties newProperties = new Properties();
newProperties.setProperty("someprop1", "somevalue1");
newProperties.setProperty("someprop2", "somevalue2");
System.setProperties(newProperties);
showProperty("某个属性 1", "someprop1");
showProperty("某个属性 2", "someprop2");
}
static void showProperty(String msg, String propertyName)
{
System.out.printf("%s: %s%n", msg, System.getProperty(propertyName));
}
}
清单 14-6
PropertiesDemo.java
```

这个清单应该相当容易理解。我创建了一个 `showProperty()` 辅助方法，以便于输出消息和属性值。

按如下方式编译清单 14-6：

```
javac PropertiesDemo.java
```

按如下方式运行生成的应用程序：

```
java PropertiesDemo
```

您应该会看到类似于以下输出的内容：

```
清除前的临时位置: C:\temp
清除后的临时位置: null
Java 版本: null
操作系统名称: null
用户主目录: null
-- listing properties --
java.specification.version=21
sun.cpu.isalist=amd64
sun.jnu.encoding=Cp1252
java.class.path=.
java.vm.vendor=Oracle Corporation
sun.arch.data.model=64
user.variant=
java.vendor.url=https://java.oracle.com/
java.vm.specification.version=21
os.name=Windows 8.1
sun.java.launcher=SUN_STANDARD
user.country=CA
sun.boot.library.path=C:\Program Files\Java\jdk-21\bin
sun.java.command=PropertiesDemo
jdk.debug=release
sun.cpu.endian=little
user.home=C:\Users\jeffrey
user.language=en
java.specification.vendor=Oracle Corporation
java.version.date=2023-10-17
java.home=C:\Program Files\Java\jdk-21
file.separator=\
java.vm.compressedOopsMode=Zero based
line.separator=
java.vm.specification.vendor=Oracle Corporation
java.specification.name=Java Platform API Specification
user.script=
sun.management.compiler=HotSpot 64-Bit Tiered Compilers
java.runtime.version=21.0.1+12-LTS-29
user.name=jeffrey
stdout.encoding=Cp1252
path.separator=;
os.version=6.3
java.runtime.name=Java(TM) SE Runtime Environment
file.encoding=UTF-8
java.vm.name=Java HotSpot(TM) 64-Bit Server VM
java.vendor.url.bug=https://bugreport.java.com/bugreport/
java.io.tmpdir=C:\Users\jeffrey\AppData\Local\Temp\
java.version=21.0.1
user.dir=D:\WRK\BOOKS\LJF\14\code\PropertiesDemo
os.arch=amd64
java.vm.specification.name=Java Virtual Machine Specification
sun.os.patch.level=
native.encoding=Cp1252
java.library.path=C:\Program Files\Java\jdk-21\bin;C:\W...
java.vm.info=mixed mode, sharing
stderr.encoding=cp850
java.vendor=Oracle Corporation
java.vm.version=21.0.1+12-LTS-29
sun.io.unicode.encoding=UnicodeLittle
java.class.version=65.0
Java 版本: 21.0.1
操作系统名称: Windows 8.1
Java 类路径: .
行分隔符:
用户账户名: jeffrey
用户主目录: C:\Users\jeffrey
某个属性: somevalue
某个属性 1: somevalue1
某个属性 2: somevalue2
```

### Properties 类

Java 提供了 `Properties` 类来简化应用程序配置数据的管理。它提供了一种管理键值对的简单方法，这使得它对于维护应用程序设置（例如窗口的位置和大小——本书不讨论窗口和其他用户界面概念）、数据库连接（我也不讨论数据库）以及其他可配置项非常有用。



#### 理解属性

`Properties` 类继承自 `java.util.Hashtable` 类（我在第 7 章简要介绍过哈希表，它也被称为哈希映射），该类支持基于键值对的存储。然而，`Properties` 引入了一些专门用于管理配置数据的特定功能。

#### 数据存储与检索

开发者可以受益于 `Properties` 类的简洁性。使用熟悉的语法即可轻松存储和检索数据。清单 14-6 提供了一个示例。以下代码片段提供了另一个示例：

```
Properties dbms = new Properties();
dbms.setProperty("database.url", "jdbc:mysql://localhost:3306/mydatabase");
dbms.setProperty("database.username", "myusername");
dbms.setProperty("database.password", "mypassword");
String url = dbms.getProperty("database.url");
String username = dbms.getProperty("database.username");
String password = dbms.getProperty("database.password");
```

此代码片段首先实例化 `Properties`。相比之下，清单 14-6 通过调用 `System` 的 `getProperties()` 方法获取了一个 `Properties` 对象。

`Properties` 对象的引用被赋值给 `dbms` 变量。（名称 `dbms` 是数据库管理系统的缩写。）然后，该引用被用于三次 `setProperty()` 调用来设置三个以 `database` 为前缀的属性，以及三次 `getProperty()` 调用来检索属性值，这些值被存储在其他变量中。

#### 属性加载与保存

`Properties` 类还可以从文件加载属性列表，并将属性列表存储到文件中，这使得配置数据能够持久化。以下方法用于完成这些任务：

*   `void load(`[`Reader`](https://docs.oracle.com/en/java/javase/20/docs/api/java.base/java/io/Reader.xhtml) `reader)`：从输入字符流（采用简单的面向行格式）中读取*属性列表*（键/值对列表）到此 `Properties` 对象中。

*   `void store(`[`Writer`](https://docs.oracle.com/en/java/javase/20/docs/api/java.base/java/io/Writer.xhtml) `writer,` [`String`](https://docs.oracle.com/en/java/javase/20/docs/api/java.base/java/lang/String.xhtml) `comments)`：将此 `Properties` 对象中的属性列表以适合使用 `load(Reader)` 方法的格式写入输出字符流。

这两个方法都涉及字符流，它类似于之前讨论的字节流，但流式传输的是 16 位字符，而不是 8 位字节。

这些方法对于需要在不修改源代码的情况下更改设置的场景非常有用。请考虑以下代码片段：

```
try (FileInputStream fis = new FileInputStream("config.properties"))
{
dbms.load(fis);
}
catch (IOException ioe)
{
ioe.printStackTrace();
}
```

```
try (FileOutputStream output = new FileOutputStream("config.properties"))
{
dbms.store(output, "Updated Database Configuration");
}
catch (IOException ioe)
{
ioe.printStackTrace();
}
```

此代码片段使用一对 `try`-`with`-`resources` 语句来处理 `IOException`，这些异常可能由 `load()` 和 `store()` 方法抛出。

这些语句首先创建 `FileInputStream` 和 `FileOutputStream`，用于连接名为 `config.properties` 的文件。与 `dbms` 相关的属性列表将从该文件加载并存储到该文件中。

注意

我在本书中不再进一步讨论 `FileInputStream` 和 `FileOutputStream`。你可以在我的书 *Java I/O, NIO and NIO.2* 中找到完整的介绍（有关本书的更多信息及订购，请参见 [`www.amazon.ca/Java-NIO-NIO-2-JEFF-FRIESEN/dp/1484215664/`](http://www.amazon.ca/Java-NIO-NIO-2-JEFF-FRIESEN/dp/1484215664/)）。

## 虚拟机关闭

`System` 提供了 `void exit(int status)` 方法来启动 JVM 的关闭序列。传递给 `status` 的参数是一个代码，它向脚本（例如 Windows 批处理文件）提供有关关闭的信息，该脚本可以根据代码采取适当的操作。按照惯例，非零状态码表示异常终止。

关闭序列

JVM 响应多个事件之一而启动*关闭序列*，包括首次调用 `System.exit()` 方法时。

在关闭序列开始时，任何已注册的关闭钩子都会以未指定的顺序启动。（有关更多信息，请参见 `java.lang.Runtime` 类。）

当所有关闭钩子终止时，关闭序列结束。此时，JVM 终止。

虽然我之前在传递错误数量的命令行参数时使用 `return` 从应用程序的 `main()` 方法返回执行，但当需要向调用者传递代码时，我可以使用 `exit()`。以下是一个示例：

```
public static void main(String[] args)
{
if (args.length != 1)
{
System.err.println("usage: java SomeApp argument");
//      return;
System.exit(1); // 指示异常终止：参数数量错误
}
}
```

使用常量而不是硬编码整数值（例如 `1`）是一个好主意。当你的应用程序有多个退出点（出于不同原因）时尤其如此。例如，我可以声明以下常量：

```
public static final BAD_NUM_OF_ARGS = 1;
```

然后像下面这样将此常量传递给 `exit()`：

```
System.exit(BAD_NUM_OF_ARGS);
```

传递给 `status` 的参数是一个代码，它向脚本（例如 Windows 批处理文件）提供有关关闭的信息，该脚本可以根据代码采取适当的操作。按照惯例，非零状态码表示异常终止。

## 下一步是什么？

本书到此结束。希望你喜欢。如果你希望我创作一系列“学习 Java ……”书籍，请告知 Apress（[`www.apress.com`](http://www.apress.com)）。

请查看接下来的两个附录，它们分别是关于保留字（A）和运算符（B）的快速参考。祝你在 Java 职业生涯中一切顺利。



索引 A accessMembersOfEnclosingClass() 非静态方法 addAddresses() 方法 addAppointment() 附加语句 assert 空 import 方法调用 package play return try try-with-resource add() 方法 匿名类 abstract Comparer 类 代码示例 表达式 表达式上下文 实例 实例初始化块，构造器 JDK 8 语言元素 限制 append() 数组 二分查找 冒泡排序 定义 线性查找 矩阵乘法 一维 二维 arrayCopy() 方法 ArrayList 的 contains() 方法 赋值运算符 按位与 按位异或 按位或 复合 条件逻辑与 逻辑或 简单 赋值语句 B bar() 方法 BigDecimal 类 构造器 开发者 货币值 对象 运行应用 源代码 BigInteger 二分查找 块 break 语句 冒泡排序 C charAt() 方法 已检查异常 ChildClass 的 createReturnType() 方法 类 通过方法定义行为 示例 字段 实例方法 局部变量 方法重载 parameterList returnType 语法 调用构造器 构造器 创建 Vehicle 类 声明 定义 初始化 嵌套 非静态 通过字段定义状态 静态 工具 类字段 clone() 方法 强制类型转换 注释 文档源代码 Javadoc 多行 单行 组合 类 继承，问题 组合对象 日志记录 复合赋值语句 复合表达式 定义 运算符 附加 加法 赋值 相等 乘法 后自增 (++) 和后自减 (--) 一元 关系 移位 一元 contains() 方法 createReturnType() 方法 currentTimeMillis() 方法 D 数据类型 定义 基本类型 用户自定义 决策语句 定义 if if-else switch 深克隆 Do-while 语句 向下转型 draw() 方法 drive() 方法 E 空语句 enclosedMethod1() equals() 方法 错误 异常 分类 定义 错误码 *vs.* 对象 处理器 语句 Throwable 类 抛出 类 try 语句 表达式 复合 定义 括号 运算符 作用 简单 F factorial() 方法 字段访问规则 final 字段 类 final 定义 实例 final finalize() 方法 for 语句 脆弱基类问题 G 垃圾回收器 get() 方法 getClass() 方法 getH() 方法 getName() 和 getAge() 方法 getX() H hashCode() 方法 哈希映射 堆 I 标识符 info() 方法 信息隐藏 继承 类 演示哈希映射示例 finalization getClass() 哈希码 对象 对象相等性 重写 hashCode() 和 equals() 字符串表示/调试 类扩展特性 AccountDemo 的源代码 Account.java CheckingAccount 子类 子类 示例 运行应用 SavingsAccount 子类 语法 this() 调用 克隆 对象 数组 深 浅 定义 方法重写 info() 方法 protected 关键字 vehicle 类 仅初始化器方法 内部类 块 对应类 EnclosingClass 示例 NSCDemo.java 遮蔽 insert() 方法 实例初始化块 接口 ArrayList 代码 协变返回类型 声明 定义 示例 扩展接口 扩展多个接口 接口实现 LinkedList 多个接口，实现 RTTI 栈 类 静态方法 驻留 isExtendedCab() 方法 iterator() 方法 迭代器 J, K Java 应用 架构 “hello world” 优先级 编程语言 风格 类别 虚拟平台 Java 归档 (JAR) Java 开发工具包 (JDK) Javadoc 注释 Java 虚拟机 (JVM) L 后期绑定 length() 方法 线性代数 线性查找 lineSeparator() 方法 字面量 布尔 字符 定义 示例 浮点数 整数 JDK 7 字符串 局部类 块/子块 编译器生成类 EnclosingClass 示例 迭代器 JDK 8 LCDemo.java 参数 语句头 静态上下文 循环分支语句 break continue 带标签 continue 带标签 break 循环语句 do-while for while < 运算符 M main() 入口点方法 main() 方法 Math 类 增强 常量 运算符 随机数生成 三角函数 方法 方法调用链 方法调用规则 方法调用语句 多行注释 多重继承 N nanoTime() 方法 嵌套类 关键字 new 仅初始化器方法 newLogger() 方法 nextElement() 方法 nextInt() 方法 非静态字段初始化器 非平凡引用类型 O 对象字段初始化器 面向对象编程 对象 访问字段 调用方法 构造 创建 Vehicle 类 定义 初始化 实例初始化块 一维数组 访问元素 定义 仅初始化器方法 关键字 new 仅初始化器方法 使用关键字，带初始化器 open() 方法 && 运算符 = 运算符 == 运算符 | 运算符 P, Q 包 定义 导入类型，日志库 import 语句 JAR 文件 日志库 非保留标识符 protected 关键字 语句 静态导入 多态 抽象类和方法 示例 接口 种类 后期绑定 向上转型 基本类型 print() 方法 println() 方法 processReport() 方法 R read() 方法 readLine() 方法 递归 remove() 方法 replaceAll() 方法 保留标识符 保留字 resize() 方法 rnd() 方法 运行时异常 运行时类型识别 (RTTI) S 同名引用类型 setName() 和 setAge() 方法 setYear() 方法 浅克隆 简单赋值语句 单行注释 sort() 方法 标准 I/O 定义 错误 格式化输出 输入 输出 语句 附加 赋值 块 决策 定义 循环 循环分支 静态 EnclosedClass 示例 字段 初始化器 方法 字符串 比较 拼接 创建 不可变性/驻留 方法 单词计数 StringBuffer 容量 创建 方法 TR switch 语句 System 数组复制 当前时间 垃圾回收 JVM 关闭序列 行分隔符 纳秒时间 System.out.println() 方法 系统属性 定义 方法 属性类 PropertiesDemo 应用 T 文本反转 (TR) toArray() 方法 toString() 方法 三角函数 try 语句 catch 块 finally 块 清理，try-catch-finally 上下文 清理，try-finally 上下文 定义 语法 多个 catch 块 资源语句 try 块 二维数组 访问元素 定义 仅初始化器方法 关键字 new 仅初始化器方法 关键字 new，带初始化器 不规则数组 U 未检查异常 向上转型 用户自定义类型 UseRenderable 的 main() 方法 V 可变参数 变量 VarInit 应用 W, X, Y, Z while 语句
