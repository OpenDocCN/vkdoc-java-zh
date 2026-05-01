# 4. 启动单文件源代码程序

在本章中，您将学习

*   什么是 Java 启动器

*   运行 `java` 命令可以使用的不同模式

*   `java` 命令的源文件模式是什么

*   如何使用 `java` 命令运行写在单个文件中的源代码

*   `java` 命令如何在源文件模式下选择要运行的类

*   关于源文件模式下的类加载机制

*   如何以及何时将 `--source` 选项与 `java` 命令一起使用

*   如何在类 UNIX 操作系统上使用 shebang 创建用 Java 编写的实用程序

## 什么是 Java 启动器？

当您开发 Java 应用程序时，您至少需要使用两个工具：Java 编译器和 Java 启动器。Java 编译器将您的源代码编译成类文件，而 Java 启动器从类文件启动（或运行）您的应用程序。当您安装 JDK 时，这两个工具都会被复制到 `JDK_HOME\bin` 目录中。Java 编译器是 `javac` 工具，Java 启动器是 `java` 工具。`java` 工具是本章讨论的主题。有时，您会遇到以下短语来指代 `java` 工具：

*   Java 启动器或 `java` 启动器

*   Java 应用程序启动器

*   `java` 命令

它们都指向位于 `JDK_HOME\bin` 或 `JRE_HOME\bin` 目录中的同一个 `java` 工具，该工具用于运行 Java 应用程序。在我的书中，我将其称为 *java 命令*，并且在本章中我将继续这样称呼。

直到 Java 8，您必须先编译源代码，然后才能运行编译后的代码。为了运行一个“HelloWorld”程序而编译源代码，这给初学者增加了一点学习曲线。每次对程序进行小的更改时，您都必须先编译它，然后才能再次运行。Java 9 添加了 JShell 命令行工具，它允许您通过直接输入到 `jshell` 命令行来运行源代码。JShell 工具是一个交互式工具。它并非旨在从源文件运行 Java 程序。相反，它适用于那些希望以交互方式探索 Java 语言的人。

直到 Java 11，如果您想运行一个程序，您必须先编译它才能运行。在 Java 11 中，`java` 命令被修改为支持直接从源代码运行 Java 程序——无需编译您的源代码。从 Java 11 开始，`java` 命令可以在以下四种模式之一中使用：

*   启动一个类文件

*   启动一个 JAR 文件的主类

*   启动一个模块的主类（在 JDK9 中添加）

*   启动源文件中的一个类（在 JDK11 中添加）

在本章中，我将解释第四种模式，即源文件模式。在这种模式下，您向 `java` 命令提供一个源文件，它会在内存中编译源代码并运行它。如果您有一个名为 `HelloWorld.java` 的文件中的源代码，您将使用源文件模式如下运行它：

```
java HelloWorld.java
```

`java` 命令会查看提供的文件扩展名，即 `.java`，并使用源文件模式来运行该程序。稍后我将解释源文件模式的规则和限制。现在，可以认为它就像使用源文件运行 `java` 命令一样简单。

谁应该使用源文件模式？它并非旨在供所有人使用。它适用于以下人群：

*   正在学习 Java 并尝试运行写在单个文件中的小程序的人

*   希望使用 Java 编写实用程序/脚本并像运行操作系统级命令/脚本一样运行它们的人

如果您的 Java 程序不属于这两类中的任何一类，那么您照常操作即可。也就是说，编写您的源代码，将它们编译成类文件，并使用其他三种模式之一的 `java` 命令来运行您的应用程序。

`java` 命令运行一个类，该类必须具有以下声明的方法：

```
public static void main(String[] args)
```

源文件模式不会改变这一要求。

## 设置示例

`java` 命令的源文件模式在类路径模式下工作，而不是在模块模式下。也就是说，您不能在模块中声明您的源代码。本章的所有示例都位于本书提供的源代码中的 `src\sourcecode.apps` 目录中。

让我们快速看一个示例。清单 4-1 包含一个名为 `HelloSourceMode` 的类的代码。该类位于 `com.jdojo.sourcecode.mode` 包中。这个类内部没有什么特别之处。它只是向标准输出打印一条消息。

```
// HelloSourceMode.java
package com.jdojo.sourcecode.mode;
public class HelloSourceMode {
public static void main(String[] args) {
System.out.println("Hello, Java source-file launcher!");
}
}
清单 4-1
一个 HelloSourceMode 类
```

使用以下命令运行 `HelloSourceMode` 类：

```
C:\Java13Revealed\src\sourcecode.apps>java HelloSourceMode.java
Hello, Java source-file launcher!
```

`java` 命令是从存储 `HelloSourceMode.java` 文件的 `src\sourcecode.app` 目录内部运行的。

`HelloSourceMode` 类位于 `com.jdojo.sourcecode.mode` 包中。源文件模式并未强制您将源文件存储为符合类的包结构。如果您要使用其他三种模式中的任何一种来运行此类，则需要将类文件存储在 `com\jdojo\sourcecode\mode` 目录中。源文件模式只关心源文件是否存在。如果它能找到该文件，它就会运行它。如果您当前的目录是 `src`，您将使用以下命令来运行此文件：

```
C:\Java13Revealed\src>java sourcecode.apps\HelloSourceMode.java
Hello, Java source-file launcher!
```



## 源文件模式选择

表 4-1 列出了 `java` 命令在四种模式下运行程序的语法。

表 4-1

四种模式下运行 java 命令的语法

| 模式 | 语法 |
| --- | --- |
| 类文件模式 | `java [options] mainclass [args...]` |
| JAR 文件模式 | `java [options] -jar jarfile [args...]` |
| 模块模式 | `java [options] --module module[/mainclass] [args...]` |
| 源文件模式 | `java [options] source-file [args...]` |

比较类文件模式和源文件模式的语法，它们非常相似。考虑以下命令：

```
C:\Java13Revealed\src\sourcecode.apps>java Unexpected.java
```

这条命令会以类文件模式还是源文件模式运行？在类文件模式下，它会尝试运行一个全限定名为 `Unexpected.java` 的类。也就是说，该类位于 `Unexpected` 目录下的 `java.class` 文件中。在源文件模式下，它会尝试运行 `Unexpected.java` 源文件中声明的类。在这种情况下，源文件模式胜出，`Unexpected.java` 源文件会被运行。我在本章的源代码中包含了 `Unexpected.java` 和 `Unexpected\java.class` 文件。你可以亲自尝试这个例子来查看结果。

这一讨论引出了一个问题：“`java` 命令如何选择源文件模式？”它在选择源文件模式时使用两个标准：

*   命令行上第一个既不是选项也不是选项一部分的条目。如果该条目是一个以 `.java` 扩展名结尾的现有文件名，则使用源文件模式。

*   在命令行上使用 `--source` 选项会强制 `java` 命令使用源文件模式。如果源文件名不包含 `.java` 扩展名，则必须使用 `--source` 选项才能使用源文件模式。

让我们回到运行 `Unexpected.java` 源文件的问题上。由于我们的文件名以 .java 扩展名结尾，`java` 命令会使用源文件模式来运行此文件中的类。

在后续章节中，我将解释源文件在运行前是如何编译的，以及源文件模式的其他规则。

## 必须是单个源文件

源文件模式允许你的程序存储在一个且仅一个源文件中。它只编译命令行上指定的源文件。如果你的源文件中的代码引用了其他源文件中的代码，源文件模式将无法运行你的程序。

清单 4-2 包含了一个名为 `MultiFileTest` 的类的代码。它尝试创建一个 `HelloSourceMode` 类的对象，该类存储在 `HelloSourceMode.java` 文件中（参见清单 4-1）。

```
// MultiFileTest.java
package com.jdojo.sourcecode.mode;
public class MultiFileTest {
public static void main(String[] args) {
// 创建一个 HelloSourceMode 类的对象
HelloSourceMode hsm = new HelloSourceMode();
System.out.println("已创建一个 HelloSourceMode 对象。");
}
}
清单 4-2
一个使用了另一个源文件中类的 MultiFileTest 类
```

以下命令尝试运行 `MultiFileTest.java` 源文件：

```
C:\Java13Revealed\src\sourcecode.apps>java MultiFileTest.java
MultiFileTest.java:7: 错误: 找不到符号
HelloSourceMode hsm = new HelloSourceMode();
^
符号:   类 HelloSourceMode
位置: 类 MultiFileTest
MultiFileTest.java:7: 错误: 找不到符号
HelloSourceMode hsm = new HelloSourceMode();
^
符号:   类 HelloSourceMode
位置: 类 MultiFileTest
2 个错误
错误: 编译失败
```

源文件模式无法运行 `MultiFileTest.java` 文件，因为在编译期间找不到 `HelloSourceMode` 类定义。在源文件模式下，允许一个类型引用其他类型（类/接口）。但是，被引用的类型必须存在于应用程序类路径上或源文件本身内部。被引用的类型不能位于其他源文件中。你可以通过两种方式修复上述错误：

*   你可以在类路径上拥有一个已编译形式的 `HelloSourceMode` 类。

*   你可以将 `HelloSourceMode` 类的声明包含在 `MultiFileTest.java` 文件中。

清单 4-3 包含了一个名为 `Laptop.java` 的源文件中的代码。该文件包含两个类，分别名为 `Laptop` 和 `Configuration`。

```
// Laptop.java
package com.jdojo.sourcecode.mode;
public class Laptop {
private Configuration config;
public Laptop(Configuration config) {
this.config = config;
}
public static void main(String[] args) {
Configuration config = new Configuration();
Laptop laptop = new Laptop(config);
System.out.println("已创建一台笔记本电脑...");
}
}
public class Configuration {
public Configuration() {
System.out.println("正在创建默认配置...");
}
}
清单 4-3
包含两个类声明（Laptop 和 Configuration）的源文件
```

`Laptop` 类引用了 `Configuration` 类。以下命令成功地在源文件模式下运行了 `Laptop.java` 文件：

```
C:\Java13Revealed\src\sourcecode.apps>java Laptop.java
正在创建默认配置...
已创建一台笔记本电脑...
```

## 运行第一个顶层类

在源文件模式下，`java` 命令会扫描指定的源文件，并尝试运行找到的第一个类型定义。这条规则意味着，如果你想运行源文件中的类 `C`，那么 `C` 必须被定义为文件中的第一个顶层类。例如，如果文件中有两个类，并且两者都有 `main()` 方法，那么总是运行第一个类。如果你想运行第二个类，你必须将其移动到源文件的顶部。如果源文件中的第一个类型不是类，或者没有 `main()` 方法，源文件模式会生成一个错误。让我们来看几个例子。



### 提示

在引入源文件模式之前，你需要指定要运行的类名。源文件模式直接使用文件名，它会自动运行文件中声明的第一个类。此外，源文件名与源文件内部声明的要运行的类名不必保持一致。

清单 4-4、4-5 和 4-6 分别包含了名为 `HelloBye.java`、`ByeHello.java` 和 `EmptyHello.java` 的文件内容。每个文件都包含两个类。文件名指明了其中包含的类名。`HelloBye.java` 文件包含 `Hello` 和 `Bye` 类。`ByeHello.java` 文件包含 `Bye` 和 `Hello` 类。`EmptyHello.java` 文件包含 `Empty` 和 `Hello` 类。`Hello` 和 `Bye` 类包含一个 `main()` 方法。`Empty` 类不包含 `main()` 方法。

```
// EmptyHello.java
package com.jdojo.sourcecode.mode;
public class Empty {
}
public class Hello {
public static void main(String[] args) {
System.out.println("Hello");
}
}
清单 4-6
EmptyHello.java 源文件的内容
```

```
// ByeHello.java
package com.jdojo.sourcecode.mode;
public class Bye {
public static void main(String[] args) {
System.out.println("Bye");
}
}
public class Hello {
public static void main(String[] args) {
System.out.println("Hello");
}
}
清单 4-5
ByeHello.java 源文件的内容
```

```
// HelloBye.java
package com.jdojo.sourcecode.mode;
public class Hello {
public static void main(String[] args) {
System.out.println("Hello");
}
}
public class Bye {
public static void main(String[] args) {
System.out.println("Bye");
}
}
清单 4-4
HelloBye.java 源文件的内容
```

让我们逐一以源文件模式运行这三个源文件：

```
C:\Java13Revealed\src\sourcecode.apps>java HelloBye.java
Hello
C:\Java13Revealed\src\sourcecode.apps>java ByeHello.java
Bye
C:\Java13Revealed\src\sourcecode.apps>java EmptyHello.java
error: can't find main(String[]) method in class: com.jdojo.sourcecode.mode.Empty
```

在前两种情况下，运行的是源文件中找到的第一个类（`Hello` 或 `Bye`）。在第三种情况下，找到的第一个类是 `Empty`，它没有 `main()` 方法。这就是第三种情况导致错误的原因。如果你将 `Empty` 的类型改为接口、枚举或注解，也会得到同样的错误。

## 向程序传递参数

在源文件模式下，命令行中文件名之后指定的所有参数都会传递给所运行类的 `main()` 方法。清单 4-7 包含了一个名为 `Stats` 的类的代码。它计算并打印命令行中指定数字的统计信息，如计数、总和、最小值、最大值和平均值。如果你没有指定任何参数，它会打印用法信息。如果你指定了任何非数字参数，它会抛出 `NumberFormatException`。

```
// Stats.java
package com.jdojo.sourcecode.mode;
import static java.lang.System.out;
import java.util.Arrays;
import java.util.DoubleSummaryStatistics;
import static java.util.stream.Collectors.summarizingDouble;
public class Stats {
public static void main(String[] args) {
if (args.length == 0) {
out.println("No numbers were specified.");
out.println("Usage: Prints stats of the specified numbers "
+ "such as sum, average, min, and max.");
out.println("Syntax: java Stats.java [numbers]");
out.println("Example: java Stats.java 10 20 25 34");
return;
}
// Compute and print the stats on the agruments
DoubleSummaryStatistics stats = Arrays.asList(args)
.stream()
.map(s -> Double.valueOf(s))
.collect(summarizingDouble(Double::doubleValue));
out.println(stats);
}
}
清单 4-7
一个 Stats 类，用于计算并打印命令行中指定数字的统计信息
```

以下是在源文件模式下调用 `Stats.java` 文件的几个示例：

```
C:\Java13Revealed\src\sourcecode.apps>java Stats.java
No numbers were specified.
Usage: Prints stats of the specified numbers such as sum, average, min, and max.
Syntax: java Stats.java [numbers]
Example: java Stats.java 10 20 25 34
C:\Java13Revealed\src\sourcecode.apps>java Stats.java 6 8 9 4 1 4.9 -6
DoubleSummaryStatistics{count=7, sum=26.900000, min=-6.000000, average=3.842857, max=9.000000}
C:\Java13Revealed\src\sourcecode.apps>java Stats.java 6 x 9
Exception in thread "main" java.lang.NumberFormatException: For input string: "x"
at java.base/jdk.internal.math.FloatingDecimal.readJavaFormatString(FloatingDecimal.java:2054)
...
```

## 向编译器传递选项

在源文件模式下，`java` 命令在运行源文件之前需要先编译它。它会扫描命令行中与编译器相关的选项，并将它们传递给编译器。如果指定了，传递给编译器的选项如下：

*   `--class-path`

*   `--module-path`

*   `--add-exports`

*   `--add-modules`

*   `--limit-modules`

*   `--patch-module`

*   `--upgrade-module-path`

*   `--enable-preview`

除了此列表中的选项外，你不能向编译器传递任何其他选项。如果选项列表很大，你可以使用命令行参数文件（@-files）来传递它们。

在源文件模式下，编译期间会禁用注解处理。这就像你使用了 `-proc:none` 选项来编译源代码一样。

如果使用 `--source` 选项指定了版本，则该值将用作编译时隐式 `--release` 选项的参数。

编译器不会强制执行文件名限制，即命名包中的类型应存在于一个文件名由类型名后跟 `.java` 扩展名组成的文件中。你已经见过这类例子。例如，你在 `HelloBye.java` 文件中包含了公共的 `Hello` 和 `Bye` 类。如果你自己编译 `HelloBye.java` 文件，编译器会拒绝编译。编译器会坚持要求公共类必须声明在两个分别名为 `Hello.java` 和 `Bye.java` 的单独文件中。但是，当你使用源文件模式运行该文件时，编译器并未强制执行此限制。

在源文件模式下，源文件作为*未命名*模块的一部分进行编译。你的代码可以访问所有可观察模块。这就像你使用 `--add-modules=ALL-DEFAULT` 选项编译并运行了代码一样。如果你希望代码能够访问其他模块，可以使用额外的 `--add-modules` 选项来实现。

在源文件模式下运行 `java` 命令需要 `jdk.compiler` 模块。它用于在内存中编译源文件。



## 类加载机制

源文件中定义的所有类型均由一个名为内存类加载器的自定义类加载器加载，该加载器会委托给应用程序类加载器。这种委托行为在 Java 11 和 Java 12 之间发生了变化。以下是它在 Java 11 中的工作方式：

*   内存类加载器委托给应用程序类加载器。如果应用程序类加载器无法加载该类，则内存类加载器会尝试从源文件中加载该类。这有两个含义。首先，应用程序类加载器无法看到源文件中的类。其次，由应用程序类加载器加载的类路径上的类，优先级高于源文件中的类。

*   如果从源文件运行的类也存在于类路径上，`java` 命令会报错。

源文件模式下 `java` 命令的目标是为初学者提供一种运行源文件且更少意外的方式。上述两条规则使其变得复杂。假设你正在学习 Java，并且想从源文件运行一个类。当你运行该文件时，会收到错误“在应用程序类路径上找到类”。作为初学者，你将很难理解这个错误。再举一个例子，假设你有一个包含两个类的源文件。正在运行的类使用了第二个类。当你运行源文件时，所使用的第二个类并非来自你的源文件。相反，如果第二个类恰好也在类路径上，它就会从类路径中被选取。这两种情况在 Java 11 中都是可能发生的。作为运行源文件的初学者，你无法从源文件中的代码得到你想要的结果。Java 12 修复了这种类加载机制，使其保持简单。此更改由增强请求 [`https://bugs.openjdk.java.net/browse/JDK-8210839`](https://bugs.openjdk.java.net/browse/JDK-8210839) 跟踪。

以下是 Java 12 及更高版本中内存类加载器的简化类加载行为：

*   内存类加载器仍然像在 Java 11 中那样委托给应用程序类加载器。这意味着类路径上的类不能使用源文件中的类。

*   内存类加载器在委托给应用程序类加载器之前，会先在源文件中查找类。这意味着源文件中的类优先级高于类路径上的类。

*   如果从源文件运行的类也存在于类路径上，`java` 命令不会报错。它只会运行源文件中的类。

这种新的类加载行为可以理解为，源文件中的类被预置到了类路径之前。Java 一直允许类路径上存在重复的类，并且始终使用在类路径中找到的第一个类。从 Java 12 开始，在使用源文件模式时，这种旧的“先找到类路径上的类即使用”的规则仍然适用。

让我们通过几个示例来演示这些规则的效果。`src\sourcecode.apps\examples` 目录包含两个名为 `Fruits` 和 `FruitsPrinter` 的类。它们都在 `com.jdojo.sourcecode.mode` 包中。我已将这些类打包到 `fruits.jar` 文件中，并将该 JAR 文件存储在 `src\sourcecode.apps` 目录中。

清单 4-8 和 4-9 包含了 `Fruits` 和 `FruitsPrinter` 类的代码。这两个类都在静态初始化器中打印了它们的类加载器。`FruitsPrinter` 类引用了 `Fruits` 类。我在这两个类中保持了逻辑简单，以便你可以专注于类加载规则。请注意，你将在类路径上使用这些类。它们并非设计为从源文件运行。

```
// FruitsPrinter.java
package com.jdojo.sourcecode.mode;
public class FruitsPrinter {
static {
// 打印此类的类加载器
var cl = FruitsPrinter.class.getClassLoader();
System.out.println("类路径上的 FruitsPrinter 类" +
" 由 " + cl + " 加载");
}
public static void print() {
System.out.println("调用了类路径上的 FruitsPrinter.print()");
Fruits.print();
}
}
清单 4-9
一个引用 Fruits 类并旨在放置在类路径上的 FruitsPrinter 类
```

```
// Fruits.java
package com.jdojo.sourcecode.mode;
public class Fruits {
static {
// 打印此类的类加载器
var cl = Fruits.class.getClassLoader();
System.out.println("类路径上的 Fruits 类" +
" 由 " + cl + " 加载");
}
public static void print() {
System.out.println("类路径上的 Fruits: "
+ "橙子, 木瓜, 苹果");
}
}
清单 4-8
一个旨在放置在类路径上的 Fruits 类
```

清单 4-10 包含了一个 `Fruits` 类的代码。它保存在 `src\sourcecode.apps` 目录下的 `Fruits.java` 文件中。

```
// Fruits.java
package com.jdojo.sourcecode.mode;
public class Fruits {
static {
// 打印此类的类加载器
var cl = Fruits.class.getClassLoader();
System.out.println("源文件中的 Fruits 类" +
" 由 " + cl + " 加载");
}
public static void main(String[] args) {
Fruits.print();
}
public static void print() {
System.out.println("源文件中的 Fruits: 西瓜");
}
}
清单 4-10
一个旨在从源文件运行的 Fruits 类
```

让我们使用 Java 11 运行 `Fruits.java` 文件——一次仅运行源文件，一次在类路径上包含 `fruits.jar` 文件：

```
C:\Java13Revealed\src\sourcecode.apps>SET PATH=C:\java11\bin
C:\Java13Revealed\src\sourcecode.apps>java Fruits.java
源文件中的 Fruits 类由 com.sun.tools.javac.launcher.Main$MemoryClassLoader@3f197a46 加载
源文件中的 Fruits: 西瓜
C:\Java13Revealed\src\sourcecode.apps>java --class-path fruits.jar Fruits.java
类路径上的 Fruits 类由 jdk.internal.loader.ClassLoaders$AppClassLoader@2aae9190 加载
错误：在应用程序类路径上找到类：com.jdojo.sourcecode.mode.Fruits
```

第一次，`Fruits` 类成功运行。第二次，`Fruits` 类在类路径上被找到，这从用于加载它的类加载器可以明显看出。回想一下，在 Java 11 中，如果从源文件运行的类也在类路径上被找到，你会收到一个错误。输出证实了这一点。

让我们使用 Java 13 运行同一个源文件——一次仅运行源文件，一次在类路径上包含 `fruits.jar` 文件：

```
C:\Java13Revealed\src\sourcecode.apps>SET PATH=C:\java13\bin
C:\Java13Revealed\src\sourcecode.apps>java Fruits.java
源文件中的 Fruits 类由 com.sun.tools.javac.launcher.Main$MemoryClassLoader@58a90037 加载
源文件中的 Fruits: 西瓜
C:\Java13Revealed\src\sourcecode.apps>java --class-path fruits.jar Fruits.java
源文件中的 Fruits 类由 com.sun.tools.javac.launcher.Main$MemoryClassLoader@6c3f5566 加载
源文件中的 Fruits: 西瓜
```

在 Java 13 中，输出证实了 `Fruits` 类是由内存类加载器加载的，并且类路径上的 `Fruits` 类未被使用。

让我们考虑清单 4-11 中 `CLTest1` 类的代码。该文件保存为 `ClTest1.java`。当你运行此文件时，你期望得到什么结果？

```
// CLTest1.java
package com.jdojo.sourcecode.mode;
public class CLTest1 {
public static void main(String[] args) {
Fruits.print();
}
}
清单 4-11
CLTest1.java 文件的内容
```

以下命令在不带任何选项的情况下运行 `CLTest1.java` 文件。无论你在 Java 11 还是更高版本中运行它，都会得到相同的输出。



```
C:\Java13Revealed\src\sourcecode.apps>java CLTest1.java
CLTest1.java:6: error: cannot find symbol
Fruits.print();
^
symbol:   variable Fruits
location: class CLTest1
1 error
error: compilation failed
```

这个输出是意料之中的。`CLTest1` 类引用了一个名为 `Fruits` 的类，但在源文件中找不到该类。默认情况下，类路径设置为当前目录。当前目录中也没有 `Fruits` 类。让我们通过将 `fruits.jar` 文件添加到类路径来运行此文件，如下所示：

```
C:\Java13Revealed\src\sourcecode.apps>java --class-path fruits.jar CLTest1.java
Fruits class on class path was loaded by jdk.internal.loader.ClassLoaders$AppClassLoader@2626b418
Fruits on class path: Oranges, Papayas, Apples
```

请注意，在 `sourcecode.apps` 目录中确实有一个 `Fruits.java` 文件，但它没有被使用。这是因为源文件模式只编译一个源文件，并且不会触及任何其他源文件。`CLTest1` 类引用的 `Fruits` 类是在类路径（在 `fruits.jar` 文件中）中找到的，并由应用程序类加载器加载。

让我们考虑另一种情况，其中源文件中的类引用了类路径上的一个类，而该类又引用了另一个类。清单 4-12 包含 `CLTest2` 类的代码，它存储在一个 `CLTest2.java` 文件中。该类引用了 `fruits.jar` 文件中的 `FruitsPrinter` 类，因此你需要在类路径上使用 `fruits.jar` 文件来运行它。

```
// CLTest2.java
package com.jdojo.sourcecode.mode;
public class CLTest2 {
public static void main(String[] args) {
FruitsPrinter.print();
}
}
清单 4-12
CLTest2.java 文件的内容
```

以下命令运行 `CLTest2.java` 源文件。在 Java 11 及更高版本中，你将获得相同的输出。

```
C:\Java13Revealed\src\sourcecode.apps>java --class-path fruits.jar CLTest2.java
FruitsPrinter class on class path was loaded by jdk.internal.loader.ClassLoaders$AppClassLoader@2626b418
FruitsPrinter.print() on class path called.
Fruits class on class path was loaded by jdk.internal.loader.ClassLoaders$AppClassLoader@2626b418
Fruits on class path: Oranges, Papayas, Apples
```

输出中没有任何意外。`FruitsPrinter` 和 `Fruits` 类都是由应用程序类加载器从类路径加载的。我展示这个示例是为了给下一个示例做铺垫，下一个示例可能会让你感到有些意外。准备好了吗？

清单 4-13 包含两个类 `CLTest3` 和 `Fruits` 的代码。该代码存储在一个 `CLTest3.java` 文件中。`CLTest3` 类引用了 `FruitsPrinter` 类，而 `FruitsPrinter` 类又引用了 `Fruits` 类。这里的技巧在于，`FruitsPrinter` 类位于 `fruits.jar` 中，而 `Fruits` 类则位于两个地方——`fruits.jar` 和源文件 `CLTest3.java` 中。你能猜到当你运行 `CLTest3.java` 文件时，会使用哪个 `Fruits` 类吗？

```
// CLTest3.java
package com.jdojo.sourcecode.mode;
public class CLTest3 {
public static void main(String[] args) {
FruitsPrinter.print();
}
}
public class Fruits {
static {
// 打印此类的类加载器
var cl = Fruits.class.getClassLoader();
System.out.println("Fruits class in source file"
+ " was loaded by " + cl);
}
public static void main(String[] args) {
Fruits.print();
}
public static void print() {
System.out.println("Fruits in source file: Watermelons");
}
}
清单 4-13
CLTest3.java 文件的内容
```

以下命令运行 `CLTest3.java` 文件。在 Java 11 及更高版本中，你将获得相同的输出。

```
C:\Java13Revealed\src\sourcecode.apps>java --class-path fruits.jar CLTest3.java
FruitsPrinter class on class path was loaded by jdk.internal.loader.ClassLoaders$AppClassLoader@2626b418
FruitsPrinter.print() on class path called.
Fruits class on class path was loaded by jdk.internal.loader.ClassLoaders$AppClassLoader@2626b418
Fruits on class path: Oranges, Papayas, Apples
```

你是否对使用了来自 `fruits.jar` 文件类路径上的 `Fruits` 类，而不是源文件中包含的 `Fruits` 类感到惊讶？我不是说过源文件中的类具有优先权吗？是的，我说过。但这里有一个陷阱。源文件中的类确实被赋予优先权，这是事实。然而，应用程序类加载器看不到源文件中的类。我陈述这条规则时提到，类路径上的类不能引用源文件中的类。因为 `FruitsPrinter` 类是由应用程序类加载器加载的，所以被 `FruitsPrinter` 类引用的 `Fruits` 类也必须由应用程序类加载器加载。因此，源文件中的 `Fruits` 类甚至没有被考虑使用。

这是本节中的最后一个示例。清单 4-14 包含三个类 `CLTest4`、`FruitsPrinter` 和 `Fruits` 的代码。它存储在一个 `CLTest4.java` 文件中。当你运行这个类时，你期望得到什么结果？如果你在 Java 11 和 Java 12 或更高版本中的答案不同，那么你的思路是正确的。

```
// CLTest4.java
package com.jdojo.sourcecode.mode;
public class CLTest4 {
public static void main(String[] args) {
FruitsPrinter.print();
}
}
public class FruitsPrinter {
static {
// 打印此类的类加载器
var cl = FruitsPrinter.class.getClassLoader();
System.out.println("FruitsPrinter class in source file" +
" was loaded by " + cl);
}
public static void print() {
System.out.println("FruitsPrinter.print() in source file called.");
Fruits.print();
}
}
public class Fruits {
static {
// 打印此类的类加载器
var cl = Fruits.class.getClassLoader();
System.out.println("Fruits class in source file"
+ " was loaded by " + cl);
}
public static void main(String[] args) {
Fruits.print();
}
public static void print() {
System.out.println("Fruits in source file: Watermelons");
}
}
清单 4-14
CLTest4.java 文件的内容
```

当你运行 `CLTest4.java` 文件时，在 Java 11 和 Java 12（及更高版本）中会得到不同的输出。以下命令在 Java 11 中运行它：

```
C:\Java13Revealed\src\sourcecode.apps>SET PATH=C:\java11\bin
C:\Java13Revealed\src\sourcecode.apps>java --class-path fruits.jar CLTest4.java
FruitsPrinter class on class path was loaded by jdk.internal.loader.ClassLoaders$AppClassLoader@2aae9190
FruitsPrinter.print() on class path called.
Fruits class on class path was loaded by jdk.internal.loader.ClassLoaders$AppClassLoader@2aae9190
Fruits on class path: Oranges, Papayas, Apples
```

输出显示，`FruitsPrinter` 和 `Fruits` 类都是由应用程序类加载器从类路径加载的。这是因为在 Java 11 中，内存类加载器将所有类加载委托给应用程序类加载器，而不会先搜索源文件。在 Java 11 中，`CLTest4.java` 文件中相同的两个类被忽略了。

让我们在 Java 13 中运行相同的命令，如下所示：

```
C:\Java13Revealed\src\sourcecode.apps>SET PATH=C:\java13\bin
C:\Java13Revealed\src\sourcecode.apps>java --class-path fruits.jar CLTest4.java
FruitsPrinter class in source file was loaded by com.sun.tools.javac.launcher.Main$MemoryClassLoader@4d49af10
FruitsPrinter.print() in source file called.
Fruits class in source file was loaded by com.sun.tools.javac.launcher.Main$MemoryClassLoader@4d49af10
Fruits in source file: Watermelons
```

现在你可以看到区别了。在 Java 12 及更高版本中，内存类加载器在委托给应用程序类加载器之前，会先搜索源文件。在这种情况下，内存类加载器在源文件中找到了 `FruitsPrinter` 和 `Fruits` 类，并使用了它们。



## 非标准源文件名

编写小型 Java 实用程序是很常见的。你可能不想为源文件使用 `.java` 文件扩展名。清单 4-15 包含一个名为 `PrintFullPath` 的类的代码。该类会打印当前目录的完整路径。源代码存储在 `pfp.txt` 中。请注意，你没有为源文件使用标准的 `.java` 扩展名，而是使用了 `.txt` 扩展名。

```
// pfp.txt
package com.jdojo.sourcecode.mode;
import java.nio.file.Paths;
public class PrintFullPath {
public static void main(String[] args) {
// 打印当前目录的完整路径
System.out.println(Paths.get(".")
.toAbsolutePath()
.normalize()
.toString());
}
}
清单 4-15
pfp.txt 文件的内容
```

让我们使用 `java` 命令运行 `pfp.txt` 文件：

```
C:\Java13Revealed\src\sourcecode.apps>java pfp.txt
错误：找不到或无法加载主类 pfp.txt
原因：java.lang.ClassNotFoundException: pfp.txt
```

你没有得到预期的输出。让我们看看错误信息。`java` 命令没有使用源文件模式，而是使用了类文件模式，并开始查找一个完全限定名为 `pfp.txt` 的类。也就是说，它查找的是 `pfp` 包中名为 `txt` 的类。它没有使用源文件模式，因为文件名没有 `.java` 扩展名。

如果你的文件扩展名不是 `.java`，则必须使用 `--source` 选项来启用源文件模式。让我们使用 `--source` 选项再次运行它，如下所示：

```
C:\Java13Revealed\src\sourcecode.apps>java --source 13 pfp.txt
C:\Java13Revealed\src\sourcecode.apps
```

你得到了预期的输出。该命令使用的源版本值为 13。`--source` 选项的值指定了源文件中允许使用的 Java 语言特性和 API 所对应的 JDK 版本。`version` 的有效值有哪些？它会随着 JDK 的每个版本而改变。对于 JDK 13，当我使用 6 时会出现错误，但使用 7 时会有一个弃用警告。8 到 13 的值都是可以的。当你使用 JDK 14 时，使用 7 作为源版本可能会出错。

## 支持 Shebang 文件

类 Unix 操作系统（如 Linux 和 macOS）支持 shebang 文件。Shebang 文件是一种可执行脚本文件，其特殊的第一行（shebang）指定了用于执行该脚本的解释器。第一行被称为“shebang”或“hash bang”。Shebang 以井号（#）开头，后跟感叹号（!），再后面是所用解释器的完整路径。考虑以下保存在 Linux 上名为 `hello` 的文件中的内容：

```
#!/bin/bash
echo Hello world!
```

这里，`hello` 文件包含一个脚本。你需要赋予它执行权限（`chmod +x hello`）。它也是一个 shebang 文件。它的 shebang 指定了执行该脚本的解释器——在本例中，脚本中只有一个 `echo` 命令——是 `/bin/bash`。

源文件模式下的 `java` 命令支持 shebang 文件。你需要在源文件中使用的 shebang 是：

```
#![java 路径] --source [版本]
```

这里，`[java 路径]` 是 `java` 命令的完整路径，`[版本]` 是源版本。

### 提示

Shebang 文件仅在类 Unix 操作系统（如 Linux 和 macOS）上原生支持。Windows 不支持 shebang 文件。

清单 4-16 包含一个 `pfp` 文件的代码。我将 JDK13 安装在我的 Linux 机器的 `/home/ksharan/java13` 目录下，因此我在 `pfp` 文件中指定了以下 shebang：

```
#!/home/ksharan/java13/bin/java --source 13
package com.jdojo.sourcecode.mode;
import java.nio.file.Paths;
public class PrintFullPath {
public static void main(String[] args) {
// 打印当前目录的完整路径
System.out.println(Paths.get(".")
.toAbsolutePath()
.normalize()
.toString());
}
}
清单 4-16
pfp 可执行文件的内容
```

```
#!/home/ksharan/java13/bin/java --source 13
```

你需要修改此文件中的 shebang，使其指向你机器上 `java` 命令的路径。你可以使用 Linux 上的 `whereis` 命令找到 `java` 的路径。在我的机器上，它打印出以下内容：

```
$ whereis java
java: /usr/bin/java /usr/lib/java /etc/java /usr/share/java /home/ksharan/java9.sh /home/ksharan/java10.sh /home/ksharan/java13/bin/java /usr/share/man/man1/java.1.gz
```

在执行 `pfp` 文件之前，你需要使用 `chmod` 命令使其可执行。你可以使用完整路径、相对路径或仅使用文件名来执行 `pfp` 文件。Java 在你使用何种路径方面不起任何作用。这完全取决于你的操作系统。如果你将存储 `pfp` 的目录包含在 PATH 环境变量中，你可以仅使用文件名来执行它。我将 `pfp` 文件存储在我的机器上的 `/home/ksharan` 目录中，这也是我的当前目录。以下命令序列展示了我是如何以不同方式执行此文件的：

```
$ ls -l pfp
-rw-rw-r--. 1 ksharan ksharan 341 Aug  3 09:20 pfp
$ ./pfp
bash: ./pfp: 权限不够
$ chmod +x ./pfp
$ ls -l pfp
-rwxrwxr-x. 1 ksharan ksharan 341 Aug  3 09:20 pfp
$ ./pfp
/home/ksharan
$ pfp
bash: pfp: 未找到命令...
$ export PATH=.:$PATH
$ pfp
/home/ksharan
$ pwd
/home/ksharan
$
```

请注意，你在 Linux 上的 `pfp` 命令与 Linux 的 `pwd` 命令功能相同。`pwd` 命令打印工作目录，`pfp` 命令也是如此。你能够用 Java 实现一个 Linux 命令，难道不感到高兴吗？

对运行 Java 源文件的 shebang 支持附带了一些规则。让我们先看看规则，然后再看几个示例：

*   你也可以使用 `java` 命令配合 `--source` 选项，以源文件模式运行 shebang 文件。

*   Shebang 文件不能有 `.java` 扩展名。

第一条规则指出，你可以使用 `--source` 选项来运行 shebang 文件。考虑以下命令：

```
$ java pfp
错误：找不到或无法加载主类 pfp
原因：java.lang.ClassNotFoundException: pfp
$ java --source 13 pfp
/home/ksharan
$
```

第一个命令尝试运行 `pfp` 文件，并生成一条错误信息，提示未找到 `pfp` 类。回想一下，当文件名包含 `.java` 扩展名或你使用了 `--source` 选项时，`java` 命令会选择源文件模式。此命令两个条件都不满足，因此 `java` 命令选择了类文件模式，并尝试运行一个名为 `pfp` 的类。当前目录中没有任何名为 `pfp` 的类，导致了此错误。

第二个命令使用了 `--source` 选项，并且成功运行了 `pfp` 源文件。等等！`pfp` 文件的第一行是一个 shebang，它不是有效的 Java 代码：

```
#!/home/ksharan/java13/bin/java --source 13
```



Java 编译器是如何编译这个 shebang 行的？当你使用 `--source` 选项时，`java` 命令会检查文件的第一行。如果第一行是 shebang，它会在将源代码传递给编译器之前，移除第一行的内容，但保留最后一个字符——换行符。因此，编译器永远不会看到 shebang；它看到的全是有效的 Java 代码。现在你可能意识到，编译器并没有做任何改动来支持 Java 源文件中的 shebang。

还有一个问题需要解答。为什么 `java` 命令不直接移除整个 shebang 行？你的 shebang 文件包含 Java 源代码，其中可能包含错误。假设在一个 shebang 文件中，第六行包含一个错误。当编译器报告这个错误时，如果 `java` 命令移除了整个 shebang 行，你将会看到错误被报告在第五行。为了保持错误报告中的行号一致，`java` 命令会移除 shebang 的内容，但保留最后一个换行符，这样源文件的行数保持不变，编译器就能报告正确的错误行号。

让我们讨论第二条规则。这条规则规定，要么你有一个没有扩展名或扩展名不是 `.java` 的 shebang 文件，要么你有一个扩展名为 `.java` 的有效 Java 源文件。如果你的文件包含 `.java` 扩展名，文件内容将被视为 Java 源文件，不做任何修改。一个带有 `.java` 扩展名的 shebang 文件会在第一行（即指定 shebang 的那一行）导致错误。你可以换一种方式来理解这条规则。带有 `.java` 扩展名的源文件是为学习 Java 语言的程序员准备的，而 shebang 文件则是为开发实用程序准备的。给实用程序添加扩展名并不是常见的做法。以下命令将 `pfp` 文件复制为 `pfp.java` 文件，并使用 `java` 命令运行 `pfp.java` 文件：

```
$ cp pfp pfp.java
$ java pfp.java
pfp.java:1: 错误: 非法字符: '#'
#!/home/ksharan/java13/bin/java --source 13
^
pfp.java:1: 错误: 需要 class, interface 或 enum
#!/home/ksharan/java13/bin/java --source 13
^
2 个错误
错误: 编译失败
$
```

错误信息表明编译器试图将 shebang 作为 Java 代码进行编译，并因此失败。

你也可以向 shebang 文件中的 `main()` 方法传递参数。你可以将清单 4-7 中的程序转换为 shebang 文件作为练习。将此文件保存为 `stats`，向文件添加 shebang，并使其可执行。你应该能够像下面这样运行 `stats` 文件：

```
$ stats 1 2 3 4 5
DoubleSummaryStatistics{count=5, sum=15.000000, min=1.000000, average=3.000000, max=5.000000}
$
```

## 总结

用于运行 Java 应用程序的 `java` 命令被称为 `java` 启动器或 `java` 工具。它可以在以下四种模式下使用：

*   类文件模式

*   JAR 文件模式

*   模块模式

*   源文件模式

类文件模式和 JAR 文件模式自 JDK 发布之初就已存在。模块模式是在 JDK 9 中添加的。源文件模式是在 JDK 11 中添加的。在源文件模式下，`java` 命令可以直接从源文件运行 Java 程序，无需你事先编译源文件。在源文件模式下，整个源代码必须包含在单个文件中。代码可以引用类路径上的其他代码。

`java` 命令使用两个标准来选择源文件模式：

*   命令行上第一个不是选项或选项一部分的条目。如果该条目是一个扩展名为 `.java` 的现有文件的名称，则使用源文件模式。

*   在命令行上使用 `--source` 选项会强制 `java` 命令使用源文件模式。如果你的源文件不包含 `.java` 扩展名，则必须使用 `--source` 选项才能使用源文件模式。

在源文件模式下，`java` 命令在运行代码之前，会使用 `jdk.compiler` 模块在内存中编译源代码。源文件中的第一个类型必须是一个包含 `main()` 方法的类。`java` 命令会自动选择第一个顶层类来运行。

在源文件模式下，源文件名之后指定的参数会被传递给所运行类的 `main()` 方法。

你可以向 `java` 命令指定选定的编译器选项，这些选项将被传递给编译器以编译源文件。在源文件模式下，注解处理被禁用。编译器不会强制执行文件名限制，即命名包中的类型应存在于一个文件名由类型名后跟 `.java` 扩展名组成的文件中。你可以在一个源文件中拥有任意数量的 `public` 类。

`java` 命令使用一个名为内存类加载器的自定义类加载器来加载源文件中的类。

在 Java 11 中，如果从源文件运行的类也存在于类路径中，你会得到一个错误。内存类加载器会委托给应用程序类加载器。也就是说，类路径上的类会优先于源文件中的类被加载。

内存类加载器的行为在 Java 12 中发生了变化。在 Java 12 中，它会在委托给应用程序类加载器之前，先在源文件中搜索类。如果从源文件运行的类也存在于类路径中，那么在 Java 12 中会运行源文件中的类，而不会像 Java 11 那样报错。

如果你的源文件没有标准的 `.java` 文件扩展名，你必须将 `--source` 选项与 `java` 命令一起使用才能运行此类源文件。你也可以在源文件中包含一个 shebang，并利用类 Unix 操作系统对 shebang 文件的支持来运行源文件。`java` 命令在将源文件内容传递给编译器之前，会剥离 shebang，但保留最后一个换行符。

一个带有 `.java` 扩展名和 shebang 的源文件将无法运行，因为编译器会尝试将 shebang 解释为 Java 源代码并失败。

