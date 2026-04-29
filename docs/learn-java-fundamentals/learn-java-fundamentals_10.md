# 10. 包

同名的引用类型（例如，两个不同的`Math`类）在尝试使用时可能会导致名称冲突，但 Java 通过包解决了这个问题。本章将向你介绍包的概念，然后是`package`和`import`语句。最后，你将了解静态导入、受保护访问和 JAR 文件。

## 什么是包？

Java 开发者将相关的类和/或接口分组到*包*中，包是用于存储引用类型的唯一*命名空间*。包可以存储类、接口和*子包*（嵌套在其他包中的包）。可以将包视为存储文件和子目录的目录。

注意

包使得定位和使用引用类型更加容易，避免了同名类型之间的名称冲突，并控制对类型的访问。

包有一个名称，该名称必须是非保留标识符，例如`java`。成员选择运算符（`.`）将包名与子包名分隔开，并将包名或子包名与类型名分隔开。例如，`java.lang.System`中的两个`.`运算符将包名`java`与子包名`lang`分隔开，并将子包名`lang`与类型名`System`分隔开。

注意

`System`实用类提供了几个有用的系统相关常量和方法。我们将在第 14 章中查看其常量和一些方法。

引用类型（例如`System`）必须声明为`public`才能从其包外部访问。任何必须可访问的常量、构造函数和方法也是如此。你将在本章后面看到示例。



## Package 语句

Java 提供了 *package* *语句*用于创建包。该语句位于源文件顶部，用于标识该源文件中类型所属的包。它必须遵循以下语法：

```
'package' 标识符 ('.' 标识符)* ';'
```

`package` 语句以保留字 `package` 开头，后跟一个*标识符*，该标识符后面可选地跟一个由 `.` 分隔的*标识符*序列。该语句以分号结束。

第一个（最左侧）标识符命名包，后续每个标识符命名一个子包。例如，在 `package x.y;` 中，源文件中声明的所有类型都属于 `x` 包的 `y` 子包。

注意

按照惯例，包/子包名称使用小写字母表示。当名称由多个单词组成时，你可能希望在单词之间放置下划线以提高可读性。

包名称序列必须是唯一的，以避免编译问题。例如，假设你创建了两个不同的 `report_generator` 包，并且每个包都包含一个具有不同接口的 `ReportGenerator` 类。当 Java 编译器在源代码中遇到例如 `ReportGenerator rg = new ReportGenerator();` 时，它需要验证 `ReportGenerator()` 构造函数是否存在。

编译器将搜索所有可访问的包，直到找到包含 `ReportGenerator` 类的 `report_generator` 包。如果找到的包包含带有 `ReportGenerator()` 构造函数的适当 `ReportGenerator` 类，则一切正常。否则，如果找到的 `ReportGenerator` 类没有 `ReportGenerator()` 构造函数，编译器会报告错误。

这个场景说明了选择唯一包名称序列的重要性。选择唯一名称序列的惯例是反转你的互联网域名，并将其用作序列的前缀。例如，如果 `javajeff.tech` 是我的域名，我可能会选择 `tech.javajeff` 作为我的前缀。然后我会指定 `tech.javajeff.report_generator.ReportGenerator` 来访问 `ReportGenerator`。

注意

域名组件并不总是有效的包名称。一个或多个组件名称可能以数字开头（`4seasons.com`）、包含连字符（`-`）或其他非法字符（`news-world.com`），或者是 Java 的保留字之一（`interface.com`）。惯例规定，在数字前加下划线（`com._4seasons`），用下划线替换非法字符（`com.news_world`），并在保留字后加下划线（`com.interface_`）。

为了避免 `package` 语句出现更多问题，你需要遵循几条规则：

*   你只能在源文件中声明一个 `package` 语句。

*   除了注释之外，`package` 语句前面不能有任何内容。

第一条规则是第二条规则的特例，其存在是因为将引用类型存储在多个包中没有意义。虽然一个包可以存储多种类型，但一个类型只能属于一个包。

当源文件没有声明 `package` 语句时，该源文件的类型属于*未命名包*。重要的引用类型通常存储在自己的包中，并避免使用未命名包。

Oracle 的 Java 实现将包和子包名称映射到同名的目录（对于 Macintosh 用户来说是文件夹）。例如，实现会将 `report_generator` 映射到名为 `report_generator` 的目录。在 `x`.`y` 中，`x` 将映射到名为 `x` 的目录，`y` 将映射到 `x` 的 `y` 子目录。编译器将实现包类型的类文件存储在相应的目录中。请注意，未命名包对应于当前目录。

## Import 语句

想象一下，在源代码中每次出现 `ReportGenerator` 时都必须重复指定 `tech.javajeff.report_generator.ReportGenerator`。Java 提供了一种便捷的替代方案来省略冗长的包细节。这种替代方案就是 `import` 语句。

*import* *语句*通过告诉编译器在编译期间在哪里查找*非限定*（无包前缀）类型名称，从而从包中导入类型。它出现在源文件的顶部附近，并且必须遵循以下语法：

```
'import' 标识符 ('.' 标识符)]* '.' (类型名称 | '*') ';'
```

`import` 语句以保留字 `import` 开头，后跟一个*标识符*，该标识符后面可选地跟一个由 `.` 分隔的*标识符*序列。接着是*类型名称*或 `*`（星号），并以 `;`（分号）结束该语句。

该语法揭示了 `import` 语句的两种形式。首先，你可以导入单个类型名称，通过*类型名称*标识。其次，你可以导入所有类型，通过 `*` 标识。

`*` 符号是一个*通配符*，代表所有非限定类型名称。它告诉编译器在 `import` 语句的包序列的最右侧包中查找此类名称，除非该类型名称在先前搜索的包中已找到。请注意，使用通配符不会导致性能损失或代码膨胀。但是，它可能导致名称冲突，你将会看到这一点。

例如，`import tech.javajeff.report_generator.ReportGenerator;` 告诉编译器，一个非限定的 `ReportGenerator` 类存在于 `tech.javajeff.report_generator` 包中。类似地，`import tech.javajeff.report_generator.*;` 告诉编译器，当遇到 `ReportGenerator` 名称、`Vehicle` 名称甚至 `Account` 名称时，在此包中查找。

提示

在多程序员项目中工作时，避免使用 `*` 通配符，以便其他程序员可以轻松看到你的源代码中使用了哪些类型。

使用 `import` 语句的通配符版本时，你可能会遇到名称冲突，因为任何非限定类型名称都会匹配通配符。例如，你有 `math` 和 `statistics` 包，每个包都包含一个 `Math` 类，源代码以 `import math.*;` 和 `import statistics.*;` 语句开头，并且还包含一个非限定的 `Math` 出现。由于编译器不知道 `Math` 指的是 `math` 的 `Math` 类还是 `statistics` 的 `Math` 类，它会报告错误。你可以通过使用正确的包名称限定 `Math`（`math.Math` 或 `statistics.Math`）来解决此问题。

为了避免 `import` 语句出现其他问题，你需要遵循几条规则：

*   由于 Java 区分大小写，因此在 `import` 语句中指定的包和子包名称必须与 `package` 语句中使用的大小写一致。

*   除了注释、`package` 语句、其他 `import` 语句和 `static import` 语句（我将在本章后面介绍）之外，`import` 语句前面不能有任何内容。

编译器会自动从 `java.lang` 库包中导入类型。因此，你不必在源代码中指定 `import java.lang.System;`（导入 `java.lang` 的 `System` 类）或类似的 `import` 语句。

由于 Java 实现将包和子包名称映射到同名的目录，因此 `import` 语句等同于从与包序列对应的目录序列中加载引用类型的类文件。

## 使用包进行实践

一个实际的例子将帮助你充分理解 `package` 和 `import` 语句。我将在日志库的上下文中演示这些语句，该库允许你将消息记录到控制台、文件或其他目标。



### 封装一个日志库

假设你的应用程序需要将消息记录到控制台、文件或其他目标。借助日志库可以完成此任务。我实现的这个库包含一个名为 `Logger` 的接口、一个名为 `LoggerFactory` 的抽象类，以及一对包级私有的类 `Console` 和 `File`。

清单 10-1 展示了 `Logger` 接口，它描述了记录消息的对象。

```
package logging;
public interface Logger
{
boolean connect();
boolean disconnect();
boolean log(String msg);
}
清单 10-1
Logger.java
```

`connect()`、`disconnect()` 和 `log()` 方法在成功时返回 `true`，失败时返回 `false`。（第 11 章将介绍一种更好的处理失败的技术。）这些方法没有显式声明为 `public`，因为接口的方法隐式是 `public` 的。

清单 10-2 展示了 `LoggerFactory` 抽象类。

```
package logging;
public abstract class LoggerFactory
{
public final static int CONSOLE = 0;
public final static int FILE = 1;
public static Logger newLogger(int dstType, String... dstName)
{
switch (dstType)
{
case CONSOLE:
return new Console(dstName.length == 0 ? null : dstName[0]);
case FILE:
return new File(dstName.length == 0 ? null : dstName[0]);
default:
return null;
}
}
}
清单 10-2
LoggerFactory.java
```

清单 10-2 中的 `LoggerFactory` 类存储在文件 `LoggerFactory.java` 中。该清单以一条 `package` 语句开头，将 `logging` 标识为该类的包。

`LoggerFactory` 被声明为 `public`，以便可以从其包外部引用。此外，它被声明为 `abstract`，因此无法实例化。

注意

一个源文件中只能出现一个 `public` 类或接口。此外，源文件必须根据该类或接口的名称命名。不满足任一要求都会导致编译器报告错误。

`newLogger()` 返回一个 `Logger` 实例，用于将消息记录到适当的目标。它使用可变参数特性（参见第 6 章）来可选地接受一个额外的 `String` 参数，用于那些需要该参数的目标类型。例如，`FILE` 需要一个文件名。

清单 10-3 展示了包级私有的 `Console` 类——该类无法被 `logging` 包中的类之外的代码访问，因为保留字 `class` 前面没有保留字 `public`。

```
package logging;
class Console implements Logger
{
private String dstName;
Console(String dstName)
{
this.dstName = dstName;
}
@Override
public boolean connect()
{
return true;
}
@Override
public boolean disconnect()
{
return true;
}
@Override
public boolean log(String msg)
{
System.out.println(msg);
return true;
}
}
清单 10-3
Console.java
```

`Console` 的包级私有构造函数保存其参数，该参数很可能是 `null` 引用，因为不需要 `String` 参数。也许未来的 `Console` 版本会使用此参数来标识多个控制台窗口中的一个。

清单 10-4 展示了包级私有的 `File` 类。

```
package logging;
class File implements Logger
{
private String dstName;
File(String dstName)
{
this.dstName = dstName;
}
@Override
public boolean connect()
{
if (dstName == null)
return false;
System.out.println("opening file " + dstName);
return true;
}
@Override
public boolean disconnect()
{
if (dstName == null)
return false;
System.out.println("closing file " + dstName);
return true;
}
@Override
public boolean log(String msg)
{
if (dstName == null)
return false;
System.out.println("writing " + msg + " to file " + dstName);
return true;
}
}
清单 10-4
File.java
```

与 `Console` 不同，`File` 需要一个非 `null` 的参数。每个方法首先验证此参数不为 `null`。如果参数为 `null`，则方法返回 `false` 表示失败。

请完成以下步骤来构建此库：

1.  在文件系统中选择一个合适的位置作为当前目录。

2.  在当前目录中创建一个 `logging` 子目录。

3.  将清单 10-1 和 10-2 分别复制到文件 `Logger.java` 和 `LoggerFactory.java` 中；并将这些文件存储在 `logging` 子目录中。

4.  将清单 10-3 和 10-4 分别复制到文件 `Console.java` 和 `File.java` 中；并将这些文件存储在 `logging` 子目录中。

5.  假设当前目录包含 `logging` 子目录，执行 `javac logging/*.java` 来编译 `logging` 中的四个源文件。如果一切顺利，你应该会在 `logging` 子目录中发现 `Logger.class`、`LoggerFactory.class`、`Console.class` 和 `File.class`。（或者，对于此示例，你可以切换到 `logging` 子目录并执行 `javac *.java`。）

现在你已经创建了 `logging` 库，接下来就可以使用它了。我将在下一节中展示一个正确演示此库的小型 Java 应用程序。



### 从日志库导入类型

日志库允许我们在应用程序中引入可移植的日志代码。除了调用 `newLogger()` 之外，无论日志输出目标是什么，这段代码都将保持不变。清单 10-5 展示了一个测试该库的应用程序。

```
import logging.Logger;
import logging.LoggerFactory;
class TestLogging
{
public static void main(String[] args)
{
Logger logger = LoggerFactory.newLogger(LoggerFactory.CONSOLE);
if (logger.connect())
{
logger.log("test message #1");
logger.disconnect();
}
else
System.out.println("cannot connect to console-based logger");
logger = LoggerFactory.newLogger(LoggerFactory.FILE, "messages.txt");
if (logger.connect())
{
logger.log("test message #2");
logger.disconnect();
}
else
System.out.println("cannot connect to file-based logger");
logger = LoggerFactory.newLogger(LoggerFactory.FILE);
if (logger.connect())
{
logger.log("test message #3");
logger.disconnect();
}
else
System.out.println("cannot connect to file-based logger");
}
}
清单 10-5
TestLogging.java
```

清单 10-5 没有以 `package` 语句开头，因为简单的应用程序通常不存储在包中。相反，它以一对 `import` 语句开头，用于导入日志库的 `Logger` 和 `LoggerFactory` 类。

`main()` 方法首先使用 `LoggerFactory.CONSOLE` 常量作为参数调用 `LoggerFactory` 的 `newLogger()` 方法，以创建一个将消息记录到控制台的日志记录器。该方法返回一个 `Logger` 对象（即，由一个实现了 `Logger` 接口的类——在本例中是 `Console`——创建的日志记录器）。

接着，该方法通过调用 `Logger` 的 `connect()` 方法来连接此日志记录器。如果此方法返回 true，则表示连接成功（控制台连接会成功，但记录到文件时可能不会成功），然后 `main()` 通过调用 `Logger` 的 `log()` 方法来记录一条消息。最后，它通过调用 `Logger` 的 `disconnect()` 方法断开与日志记录器的连接。

接下来，`main()` 方法重复此过程，创建一个基于文件的日志记录器，将消息记录到名为 `messages.txt` 的文件中。

该过程第三次重复，尝试创建一个基于文件的日志记录器，将消息记录到一个未命名的文件。我们假设当没有文件名参数传递给 `newLogger()` 时，会使用一个默认名称——请记住，`newLogger()` 使用可变参数来获取文件名。

`newLogger()` 方法被编码为不返回日志记录器，但你可以修改它，使其选择一个默认文件名。我这样编写是为了测试当 `newLogger()` 无法返回日志记录器时的代码。

将清单 10-5 复制到一个名为 `TestLogging.java` 的文件中，并将此文件放在你之前创建的 `logging` 目录所在的同一目录中。然后，执行以下命令来编译 `TestLogging.java`（如果之前未编译，也会编译日志包的源文件）：

```
javac TestLogging.java
```

执行以下命令来运行 `TestLogging`：

```
java TestLogging
```

你应该会看到以下输出：

```
test message #1
opening file messages.txt
writing test message #2 to file messages.txt
closing file messages.txt
cannot connect to file-based logger
```

假设 `TestLogging.java` 不在 `logging` 所在的同一目录中。你将如何编译此源文件并运行生成的应用程序？答案是使用类路径。

*类路径* 是 Java 虚拟机（JVM）搜索引用类型的一系列包。它通过用于启动 JVM 的 `-classpath`（或 `-cp`）选项指定，或者，当该选项不存在时，通过 `CLASSPATH` 环境变量指定。

注意

无论你是使用 `-classpath`/`-cp` 选项还是 `CLASSPATH` 环境变量来指定类路径，都必须遵循特定的格式。在 Windows 下，此格式表示为 *path1*`;`*path2*`;...`，其中 *path1*、*path2* 等是包目录的位置。在 Mac OS X、Unix 和 Linux 下，此格式变为 *path1*`:`*path2*`:...`。

假设（在 Windows 平台上）日志库存储在 `C:\logging` 中，而 `TestLogging.java` 存储在 `C:\TestLogging` 中，这是当前目录。指定以下命令来编译源代码并运行应用程序：

```
javac -cp ../logging TestLogging.java
java -cp ../logging;. TestLogging
```

`../logging;.` 中 `;` 字符后的句点字符代表当前目录。必须指定它，以便 JVM 能够找到 `TestLogging.class`。

## 其他主题

在使用包时，还有三个其他主题需要考虑：静态导入、受保护访问和 JAR 文件。



### 静态导入

接口应仅用于声明类型。它们不应被用来声明*常量接口*，即那些仅用于导出常量的接口。清单 10-6 中的 `Openable` 常量接口就是一个例子。

```
package demo;
public interface Openable
{
boolean OPEN = true;
boolean CLOSED = false;
}
清单 10-6
Openable.java
```

程序员使用常量接口是为了避免在常量名称前加上其引用类型的名称（例如 `Integer.MAX_VALUE`）。例如，考虑清单 10-7 中的 `Door` 类，它实现了 `Openable` 接口，这样程序员就可以直接指定常量 `OPEN` 和 `CLOSED`，而无需包含类前缀（如果它们是在类中声明的）。

```
package demo;
public class Door implements Openable
{
private boolean state = CLOSED;
public void status()
{
System.out.println("door is " + ((state == CLOSED) ? "CLOSED" : "OPEN"));
}
public void open()
{
state = OPEN;
}
public void close()
{
state = CLOSED;
}
}
清单 10-7
Door.java
```

常量接口提供了在类实现中使用的常量。这些常量是实现细节，不应泄露到类的导出接口中，因为它们可能会混淆类的使用者。此外，为了保持二进制兼容性，即使类不再使用这些常量，你也必须继续支持它们。

为了满足对常量接口的需求，同时避免使用这些接口带来的问题，JDK 5 引入了*静态导入*。这个语言特性可用于导入引用类型的静态成员。它通过 `import static` 语句实现，其语法如下所示：

```
'import' 'static' packagespec '.' typename '.' ( staticmembername | '*' );
```

该语句将 `static` 放在 `import` 之后，这使其与常规的 `import` 语句区分开来。其语法在标准的以句点分隔的包名和子包名列表方面与常规的 `import` 语句类似。可以导入单个静态成员名称，也可以导入所有静态成员名称（得益于星号）。请考虑以下示例：

```
import static java.lang.Integer.*;         // 从 Integer 导入所有静态成员。
import static java.lang.Integer.MAX_VALUE; // 仅导入 MAX_VALUE 静态常量。
import static java.lang.Integer.max;       // 仅导入 max() 静态方法。
```

一旦导入，静态成员就可以直接指定，而无需在其前面加上类型名称。例如，在指定了第一个或第三个静态导入之后，你可以直接指定 `max`，如 `int maxValue = max(10, 20);`。

为了修复清单 10-7，使其不再依赖 `implements Openable`，我们可以插入一个 `静态导入`，如清单 10-8 所示。

```
package demo;
import static demo.Openable.*;
public class Door
{
private boolean state = CLOSED;
public void status()
{
System.out.println("door is " + ((state == CLOSED) ? "CLOSED" : "OPEN"));
}
public void open()
{
state = OPEN;
}
public void close()
{
state = CLOSED;
}
}
清单 10-8
Door.java (版本 2)
```

清单 10-8 以 `package demo;` 语句开头，因为你不能从位于未命名包中的类型导入静态成员。这个包名作为后续 `import static demo.Openable.*;` 静态导入的一部分出现。

关于静态导入，还有两点额外的注意事项：

*   当两个静态导入导入了同名的成员时，编译器会报告错误。例如，假设包 `math` 包含一个 `Math` 类，它与包 `physics` 的 `Math` 类相同，都实现了相同的 `E` 常量和对数方法。当遇到以下代码片段时，编译器会报告错误，因为它无法确定访问的是 `math.Math` 的 `E` 常量还是 `physics.Math` 的 `E` 常量，以及调用的是哪个 `log()` 方法：

*   过度使用静态导入可能会使你的代码难以阅读和维护，因为它会用你导入的所有静态成员污染代码的命名空间。此外，任何阅读你代码的人都可能难以找出静态成员来自哪个类型，尤其是在从一个类型导入所有静态成员名称时。

```
import static math.Math.*;
import static physics.Math.*;
System.out.println(E);
System.out.println(log(2));
```

### 受保护访问

Java 提供了 `protected` 关键字用于包上下文中。此关键字将受保护访问级别分配给类成员，例如字段或方法。将类成员声明为 `protected` 使得该成员可以被同一包中任何类的所有代码以及子类（无论其包如何）访问。

类成员被赋予受保护访问权限，以便它们可以作为类实现的钩子。这使得程序员能够以更简单的方式编写高效的子类。关于更多信息，我建议你参考 Joshua Bloch 的著作 *Effective Java, Second Edition* ([`www.amazon.com/Effective-Java-2nd-Joshua-Bloch/dp/0321356683/`](http://www.amazon.com/Effective-Java-2nd-Joshua-Bloch/dp/0321356683/)) 中的“第 17 条：为继承而设计和编写文档，否则就禁止继承”。



### JAR 文件

通过指定创建必要目录结构的指令以及包的类文件（并说明哪些类文件应存储在哪些目录中）来分发包，是一项繁琐且容易出错的任务。幸运的是，JAR 文件提供了一种更好的替代方案。

*JAR（Java 归档）文件*是一个带有 `.jar` 文件扩展名（而非 `.zip` 扩展名）的 Zip 归档文件。它包含一个特殊的 `META-INF` 目录，其中包含 `manifest.mf`（一个存储 JAR 文件内容信息的特殊文件），以及一个组织类文件的层级目录结构。

你可以使用 JDK 的 `jar` 工具来创建和维护 JAR 文件。你也可以查看 JAR 文件的目录结构。为了展示使用该工具有多么简单，我们将创建一个 `logging.jar` 文件，用于存储 `logging` 包的内容。然后，在运行 `TestLogging.class` 时，我们将访问这个 JAR 文件。

注意

如果你在 Windows 上尝试运行 `jar`，可能会发现一条错误消息，提示找不到 `jar`。这是因为 JDK 21 只将少数重要的可执行文件存储在一个特殊目录中，并在安装过程中将 `PATH` 环境变量设置为指向该目录。例如，在我编写本书所使用的 Windows 8.1 平台上，我的 `C:\Program Files\Common Files\Oracle\Java\javapath` 路径被添加到了 `PATH` 中。`javapath` 目录包含 `java.exe`、`javac.exe` 以及其他几个常用的可执行文件。为了访问 `jar`（以及我可能想使用的任何其他 JDK 工具），我需要将 `C:\Program Files\java\jdk-21\bin` 目录添加到 `PATH` 中。我可以通过指定 `set path=%path%;C:\Program Files\java\jdk-21\bin` 来临时完成此操作。或者，我也可以使用 Windows 控制面板来永久设置。你应该在你的平台上执行类似的操作。

按如下方式创建 `logging.jar`：

1.  确保当前目录包含之前创建的 `logging` 子目录，并且 `logging` 包含 `Logger.class`、`LoggerFactory.class`、`Console.class` 和 `File.class`。

2.  执行以下命令：

    ```
    jar cf logging.jar logging\*.class
    ```

`c` 选项代表“创建新归档”，`f` 选项代表“指定归档文件名”。

现在，你应该能在当前目录中找到 `logging.jar` 文件。通过执行以下命令（其中 `t` 选项代表“列出目录结构”），向自己证明该文件包含这四个类文件：

```
jar tf logging.jar
```

你可以通过将 `logging.jar` 添加到其类路径来运行 `TestLogging.class`。例如，假设 `logging.jar` 与 `TestLogging.class` 位于同一目录，你可以通过以下命令在 Windows 下运行 `TestLogging`：

```
java -classpath logging.jar;. TestLogging
```

为方便起见，你可以使用更短的 `-cp` 来代替较长的 `-classpath`。

## 下一步是什么？

正在执行的应用程序容易发生故障。故障以不同的形式出现。Java 将故障分为异常或错误。我们将在下一章中回顾这些故障类别，并探讨 Java 用于检测故障并采取适当措施处理故障的语言特性。

