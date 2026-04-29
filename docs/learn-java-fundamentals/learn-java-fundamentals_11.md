# 11. 异常与错误

在理想世界中，应用程序运行时永远不会发生任何坏事。例如，当应用程序需要打开文件时，该文件始终存在；应用程序始终能够连接到远程计算机；当应用程序需要实例化对象时，Java 虚拟机（JVM）永远不会耗尽内存。

相比之下，现实世界中的应用程序偶尔会尝试打开不存在的文件，尝试连接到无法与之通信的远程计算机，并且需要的内存超过 JVM 所能提供的。你的目标是编写能够正确响应这些以及其他异常情况（异常）的代码。

本章将向你介绍异常和错误（异常的一种相关类型）。在定义这些术语之后，本章将探讨如何在源代码中表示异常和错误。然后，它将研究抛出和处理异常的主题，最后讨论如何在方法返回之前执行清理任务，无论是否抛出了异常。

你将发现两个与异常相关的语句（`try` 和 `try`-`with`-`resources`）：

*   `try` 语句包围一个可能抛出异常和错误的代码块。该语句的 `try` 块后面必须跟有处理这些异常的 `catch` 块组合，和/或一个用于执行清理任务的 `finally` 块。

*   `try`-`with`-`resources` 语句与 `try` 语句类似，但它允许你在执行离开 `try` 块时自动关闭任何已打开的资源。

## 什么是异常和错误？

当 Java 程序的正常执行流程被意外行为中断时，程序就会发生故障。例如，程序尝试打开一个不存在的文件，或者尝试将整数除以 0。也许程序尝试创建一个非常大的数组，但没有足够的空闲内存来满足内存分配请求。

Java 将故障分为异常或错误。*异常*是一种故障，其发生是由于程序无法控制的某些情况（例如尝试打开不存在的文件），或者是由于代码中的错误（尝试除以 0）。（换句话说，它是与正常执行流程的偏离。）在第一种情况下，无法阻止异常的发生，因此程序需要通过采取纠正措施来恢复。在第二种情况下，如果程序编写正确，异常就不会发生。

*错误*是一种不可恢复的故障。许多错误与 JVM 有关，例如，内存耗尽或尝试加载损坏的类文件。有些错误与 JVM 无关，例如输入/输出错误或断言评估为假。（*断言*是一个布尔表达式，必须为真才能继续执行。）

注意

通常，不要处理错误，因为 JVM 可能无法从中恢复。例如，一旦内存耗尽，JVM 可能必须关闭。（异常处理程序可能会尝试释放一个占用大量内存的对象，但这可能不起作用。）此外，程序如何修复损坏的类文件？

Java 将由外部因素（例如文件缺失）引起的异常归类为*受检异常*。Java 编译器会检查此类异常要么在发生处被*处理*（纠正），要么被记录为在其他地方处理。

Java 将由有缺陷的代码引起的异常归类为*运行时异常*。这些异常应由程序员修复。因为编译器不检查运行时异常是否被处理或记录为在其他地方处理，所以你可以将运行时异常视为*非受检异常*。

异常由*异常处理程序*处理，这些处理程序是响应异常并采取适当操作的代码序列。异常处理程序会查询上下文（例如，从异常发生时作用域内的变量中读取值），并利用获取的信息将 Java 程序恢复到正常行为流程。例如，异常处理程序可能会读取一个已保存的文件名，并通过对话框提示用户替换缺失的文件（显示该文件名），然后关闭对话框。

注意

你可以修改程序来处理运行时异常，但更好的做法是修复源代码。运行时异常通常源于向库的方法传递无效参数；应修复有缺陷的调用代码。



## 在源代码中表示异常和错误

异常在源代码中表示为错误码或对象。本节首先回顾每种表示方式，并解释为何对象更优越，然后介绍 Java 的异常类库。

### 错误码 vs. 对象

C 等编程语言使用基于整数的*错误码*来表示失败及失败原因（异常）。以下是几个示例：

```
FILE* fp = fopen("notes.txt", "rt");
if (fp == NULL)
{
printf("无法打开文件\n");
// 终止程序。
}
int handle = open("image.png", O_RDONLY);
if (handle == -1)
{
printf("无法打开文件\n");
// 终止程序。
}
```

C 语言的 `fopen()`（文件打开）函数在成功时返回一个指向 `FILE` 结构的非空指针（整数地址），失败时返回空（0）指针（由常量 `NULL` 表示）。类似地，C 语言的 `open()` 函数在成功时返回一个非 -1 的整数，失败时返回 -1。

错误码存在一些问题：

*   整数毫无意义；它们无法描述所代表的异常。例如，-5 是什么意思？

*   将上下文与错误码关联起来很别扭。例如，你可能想输出无法打开的文件的名称。你要把文件名存储在哪里？

*   整数是任意的；在阅读源代码时，它们的选择可能导致混淆。例如，使用 `if (!chdir("C:\\windows"))`（`!` 表示“非”）而不是 `if (chdir("C:\\windows"))` 来测试失败会更清晰。然而，0 被选为表示成功，因此必须指定 `if (chdir("C:\\temp"))` 来测试失败。

*   错误码太容易被忽略，这可能导致有缺陷的代码。例如，程序员可能会忽略 `if (fp == NULL)` 检查。如果不测试失败，当任一函数返回失败指示时，程序的行为就会不稳定。

为了克服这些问题，人们发明了一种新的异常处理方法。这种方法将描述异常的对象与基于抛出和捕获这些对象的机制相结合：

*   可以从一个具有有意义名称的类创建对象。例如，`java.io` 包中的 `FileNotFoundException` 类比 -5 更有意义。

*   对象可以在各种字段中存储上下文。例如，你可以在对象的字段中存储消息、无法打开的文件的名称、解析操作失败的最新位置和/或其他项目。

*   你不使用 `if` 语句来测试失败。相反，异常对象被抛给一个与程序代码分离的处理程序。因此，源代码更易于阅读，也更不容易出现缺陷。

### Throwable 类层次结构

Java 在其引用类型库中提供了一个类层次结构，用于表示不同类型的异常。这些类以 `java.lang` 包中的 `Throwable` 类及其 `Exception`、`RuntimeException` 和 `Error` 子类为根。（除非另有说明，本章介绍的所有类都位于 `java.lang` 中。）

#### Throwable 类

`Throwable` 是涉及异常和错误的最终超类。只有从 `Throwable` 及其子类创建的对象才能被抛出（并随后被捕获）。此类对象被称为*可抛出对象*。

一个 `Throwable` 对象与一条描述异常或错误的详细信息相关联。提供了几个构造函数（包括此处描述的一对）来创建带有或不带详细信息的 `Throwable` 对象：

*   `Throwable()`：创建一个没有详细信息的 `Throwable`。此构造函数适用于没有上下文的情况。例如，你只想知道栈是空的还是满的。

*   `Throwable(String message)`：创建一个以 `message` 作为详细信息的 `Throwable`。此消息可以输出给用户和/或记录到日志中。

`Throwable` 提供了 `String getMessage()` 方法来返回详细信息。它还提供了其他有用的方法。

#### Exception 类

`Throwable` 有两个直接子类。其中一个子类是 `Exception`，它描述了由外部因素引起的异常（例如尝试读取一个不存在的文件）。`Exception` 声明了与 `Throwable` 相同的构造函数（具有相同的参数列表）；每个构造函数都调用其对应的 `Throwable` 构造函数。此外，`Exception` 继承了 `Throwable` 的方法。它没有声明任何新方法。

Java 提供了许多直接继承 `Exception` 的异常类。以下是三个示例：

*   `IOException`：表示发生了某种 I/O 故障。此类型位于 `java.io` 包中。

*   `ParseException`：表示解析文本时发生了故障。此类型可以在 `java.text` 包中找到。

*   `UnsupportedAudioFileException`：表示由于文件不包含可识别文件类型或格式的有效数据，导致面向音频的操作失败。请查看 `javax.sound.sampled` 包以了解此类型。

请注意，每个 `Exception` 子类的名称都以单词 `Exception` 结尾。这种约定使得识别类的用途变得容易。

你通常会使用自己的异常类（其名称应以 `Exception` 结尾）来继承 `Exception`（或其子类之一）。以下是几个自定义子类的示例：

```
public class StackFullException extends Exception
{
}
public class EmptyCardReaderException extends Exception
{
private String cardReaderName;
public EmptyCardReaderException(String message, String cardReaderName)
{
super(message);
this.cardReaderName = cardReaderName;
}
public String getCardReaderName()
{
return cardReaderName;
}
}
```

第一个示例描述了一个不需要详细信息的异常类。其默认的无参构造函数调用 `Exception()`，后者又调用 `Throwable()`。

第二个示例描述了一个异常类，其构造函数需要一条详细信息和空读卡器的名称。该构造函数使用 `super(message);` 来调用 `Exception(String message)`，后者又调用 `Throwable(String message)`。

从 `Exception` 或其子类（`RuntimeException` 或其子类除外）实例化的对象是受检异常。

#### RuntimeException 类

`Exception` 的直接子类是 `RuntimeException`，它描述了最可能由编写不佳的代码引起的异常。`RuntimeException` 声明了与 `Exception` 相同的构造函数（具有相同的参数列表）；每个构造函数都调用其对应的 `Exception` 构造函数。此外，`RuntimeException` 继承了 `Throwable` 的方法。它没有声明任何新方法。

Java 提供了许多直接继承 `RuntimeException` 的异常类。以下是三个示例：

*   `ArithmeticException`：表示非法的算术运算，例如尝试将整数除以 0

*   `IllegalArgumentException`：表示在方法调用期间向方法传递了非法或不适当的参数

*   `NullPointerException`：表示尝试通过空引用调用方法或访问对象字段

从 `RuntimeException` 或其子类之一实例化的对象是非受检异常。



#### 错误类

`Throwable` 的另一个直接子类是 `Error`，它描述了一个严重的（甚至是异常的）问题（例如内存耗尽、JVM 栈溢出，或尝试加载一个找不到的类），一个合理的应用程序不应尝试处理这类问题。与 `Exception` 类似，`Error` 声明了与 `Throwable` 相同的构造方法，继承了 `Throwable` 的方法，并且没有声明任何自己的方法。

你可以根据类名以 `Error` 结尾的惯例来识别 `Error` 的子类。例如包括 `OutOfMemoryError`、`LinkageError` 和 `StackOverflowError`。

你不应该尝试创建自己的 `Error` 子类，也不应该抛出或处理 `Error` 子类的对象。基于这些原因，本章关于 `Error` 的内容就到此为止。

## 抛出异常

C 语言库函数通过将全局 `errno` 变量设置为错误码并返回一个失败码来通知调用代码发生了异常。相比之下，Java 方法则是抛出一个对象。本节将探讨这种抛出机制。

### Throw 语句

Java 提供了 `throw` 语句来抛出一个描述异常的对象。虽然你可以使用此语句抛出一个错误，但通常不会这样做，因为程序无法从内存耗尽或其他灾难性事件中恢复。

`throw` 语句的语法如下：

```
throw throwable;
```

`throwable` 标识的对象是 `Throwable` 或其任何子类的实例。然而，你通常只抛出从 `Exception` 或 `RuntimeException` 子类实例化的对象。以下是几个例子：

```
throw new ZipException("无法读取下一个 ZIP 文件条目" + filename);
throw new ArrayIndexOutOfBoundsException("数组索引越界");
```

该 throwable 对象从当前方法抛出给 JVM，JVM 会检查此方法是否有合适的处理器。如果未找到，JVM 会展开一个称为*方法调用栈*的数据结构，寻找能够处理该 throwable 所描述异常的最接近的调用方法。如果找到该方法，JVM 会将 throwable 传递给该方法的处理器，并执行其代码来处理异常。如果没有找到任何方法能处理该异常，JVM 会终止并输出一条合适的消息。

你通常不会抛出 `RuntimeException`（它过于通用，没有实际意义）或其子类（如 `ArrayIndexOutOfBoundsException`，当你指定一个负数数组索引或一个大于等于数组长度的索引时会抛出该异常）的对象。相反，你应该修复导致异常的代码。然而，在库的上下文中检测到无效参数时，你可能想要抛出 `IllegalArgumentException`：

```
static double sqrt(double n)
{
if (n < 0)
throw new IllegalArgumentException(n + " 是负数");
// ...
}
```

假设你正在基于一个包含 `sqrt()` 方法的 `Math` 类构建一个库。因为你无法计算负数的平方根，所以首先检查这种可能性，并在传入负数时创建并抛出一个描述此问题的 `IllegalArgumentException` 对象。

### Throws 子句

当你从一个方法中抛出一个受检异常时，需要通知编译器。这可以通过在方法头部附加一个 `throws` 子句来实现。该子句的语法如下：

```
'throws' 受检异常类名 (',' 受检异常类名)*
```

`throws` 子句由关键字 `throws` 后跟一个逗号分隔的列表组成，该列表包含从该方法抛出的受检异常的类名。示例如下：

```
public static void main(String[] args) throws IOException
{
FileInputStream fis = new FileInputStream("x.dat");
}
```

此示例尝试打开一个名为 `x.dat` 的文件，并返回一个允许我们从该文件读取字节的输入流。（本章不讨论超出本章范围的文件和流。）但是，如果文件不存在或发生其他 I/O 相关错误，`FileInputStream()` 构造方法将抛出 `IOException` 或其子类之一（例如 `java.io.FileNotFoundException`）。

通过在调用 `main()` 方法的头部附加一个 `throws IOException` 子句，有必要告知编译器正在抛出一个来自受检的 `IOException` 类（或其子类，它们也是受检的）的对象。毕竟，该异常并未在此方法中处理。当异常被抛出给 JVM 时，JVM 会注意到这一点，并且（因为没有 `main()` 的父方法）会输出一条消息后终止。

在使用 `throws` 子句时，需要记住一些规则：

*   如果可能，不要在 `throws` 子句中包含非受检异常类（例如 `ArithmeticException`）的名称。这些名称不需要包含，因为 `throws` 子句仅用于受检异常。包含非受检类名只会使源代码变得混乱。

*   你可以将 `throws` 子句附加到构造方法上，并在构造方法执行过程中出现问题时从中抛出一个受检异常。这样，结果对象将不会被创建。

*   如果超类方法声明了 `throws` 子句，重写的子类方法不必声明 `throws` 子句。但是，如果子类方法声明了 `throws` 子句，则该子句不得包含超类方法的 `throws` 子句中未包含的受检异常类名，除非它们是异常子类的名称。例如，给定超类方法 `void open(String name) throws IOException {}`，重写的子类方法可以声明为 `void open(String name) {}`、`void open(String name) throws IOException {}` 或 `void open(String name) throws FileNotFoundException {}`。但是，你不能在子类中指定 `void open(String name) throws ClassNotFoundException`，因为 `ClassNotFoundException` 没有出现在超类的 `throws` 子句中。

*   当受检异常的超类名称出现在 `throws` 子句中时，该受检异常类名无需再出现。例如，你不需要指定 `throws FileNotFoundException, IOException`。只需要 `throws IOException` 即可。

*   当一个方法抛出一个受检异常，但既没有处理该异常，也没有在其 `throws` 子句中列出该异常时，编译器会报告错误。

*   你可以在方法的 `throws` 子句中声明一个受检异常类名，而不从该方法中抛出该类的实例。（也许该方法尚未完全编码。）然而，Java 要求你提供代码来处理此异常，即使它没有被抛出。

还有一条规则需要考虑。有时，一个方法会抛出许多受检异常，人们很容易为了节省敲击键盘的次数而指定一个 `throws Exception` 子句。虽然只指定 `Exception` 可以节省时间，但这会使源代码的可读性降低。阅读你代码的人可能难以识别所有传递给调用者的受检异常。我建议仅在私有的临时代码中遵循这种做法。

## Try 语句

`try` 语句包围了一段可能抛出异常和错误的代码块。该语句的 `try` 块后面必须跟一个或多个用于处理这些异常的 `catch` 块，和/或一个用于执行清理任务的 `finally` 块。



### try 块

`try` 块的语法如下：

```
'try'
{
// 一个或多个可能抛出异常的语句
}
```

`try` 块中的语句服务于一个共同目的，并且可能直接或间接抛出异常。请看以下示例：

```
FileInputStream fis = null;
FileOutputStream fos = null;
try
{
fis = new FileInputStream(args[0]);
fos = new FileOutputStream(args[1]);
int c;
while ((c = fis.read()) != -1)
fos.write(c);
}
```

此示例摘自一个更大的 `Copy` 应用程序（本章稍后将完整展示），该程序用于将源文件复制到目标文件。它使用了 `java.io` 包中的 `FileInputStream` 和 `FileOutputStream` 类（我尚未正式介绍它们）。你可以将 `FileInputStream` 理解为从文件读取字节输入流的方式，将 `FileOutputStream` 理解为向文件写入字节输出流的方式。

`FileInputStream(String filename)` 构造函数会创建一个指向由 `file name` 标识的文件的输入流。当文件不存在、指向一个目录或发生其他相关问题时，此构造函数会抛出 `FileNotFoundException`。`FileOutputStream(String filename)` 构造函数会创建一个指向由 `file name` 标识的文件的输出流。当文件存在但指向一个目录、文件不存在且无法创建，或发生其他相关问题时会抛出 `FileNotFoundException`。

`FileInputStream` 声明了一个 `int read()` 方法，用于读取一个字节并将其作为 32 位整数返回。此方法在文件结束时返回 -1。`FileOutputStream` 提供了一个 `void write(int b)` 方法，用于写入 `b` 的低 8 位中的字节。当出现问题时，这两个方法都会抛出 `IOException`。

该示例的主体是一个 `while` 循环，它反复从输入流读取下一个字节，并将该字节写入输出流，直到 `read()` 指示文件结束。

### catch 块

`try` 块后面通常跟着一个或多个 `catch` 块，用于处理不同类型的异常。`catch` 块的语法如下：

```
'catch' '(' throwableType throwableObject ')'
'{'
// 一个或多个处理异常的语句
'}'
```

`catch` 块类似于构造函数，因为它有一个参数列表且没有返回类型。然而，此列表仅包含一个参数，该参数是一个 *throwableType*（`Throwable` 或其子类之一），后跟一个用于该类型对象的 *throwableObject* 标识符。

当异常发生时，会创建一个可抛出对象并将其抛出给 JVM，JVM 会搜索最近的 `catch` 块，该块的参数类型直接匹配或是该可抛出对象的超类型。当找到此块时，JVM 会将可抛出对象传递给参数并执行 `catch` 块的语句，这些语句可以查询传递的可抛出对象并以其他方式处理异常。请看以下示例：

```
catch (FileNotFoundException fnfe)
{
System.out.println(fnfe.getMessage());
}
```

此示例（扩展了之前的 `try` 块示例）描述了一个 `catch` 块，它捕获并处理类型为 `FileNotFoundException` 的可抛出对象。只有匹配此类型或其子类型的可抛出对象才会被此块捕获。

假设 `FileInputStream(String filename)` 构造函数抛出了 `FileNotFoundException`。JVM 会检查 `try` 后面的 `catch` 块，看其参数类型是否与可抛出对象类型匹配。检测到匹配后，JVM 将可抛出对象的引用传递给 `fnfe`，并将执行权转移到该块。该块通过调用 `getMessage()` 来检索异常消息，然后将其输出。

`catch` 块可能无法完全处理异常——也许它需要访问方法调用栈中某个祖先方法提供的信息。如果它可以部分处理异常，则 `catch` 块应以重新抛出异常结束，以便祖先处理器完成处理。另一种可能性是记录异常（供后续分析），然后重新抛出，如下所示：

```
catch (FileNotFoundException fnfe)
{
logger.log(fnfe);
throw fnfe;
}
```

#### 多个 catch 块

你可以在一个 `try` 块后指定多个 `catch` 块。例如，考虑上述 `Copy` 应用程序中更大的一个片段：

```
FileInputStream fis = null;
FileOutputStream fos = null;
try
{
fis = new FileInputStream(args[0]);
fos = new FileOutputStream(args[1]);
int c;
while ((c = fis.read()) != -1)
fos.write(c);
}
catch (FileNotFoundException fnfe)
{
System.out.println(fnfe.getMessage());
}
catch (IOException ioe)
{
System.out.println("I/O error: " + ioe.getMessage());
}
```

第一个 `catch` 块处理从任一构造函数抛出的 `FileNotFoundException`。第二个 `catch` 块处理从 `read()` 和 `write()` 方法抛出的 `IOException`。

当指定多个 `catch` 块时，不要在子类型的 `catch` 块之前指定超类型的 `catch` 块。例如，不要将 `catch (IOException ioe)` 放在 `catch (FileNotFoundException fnfe)` 之前。如果这样做，编译器会报告错误，因为 `catch (IOException ioe)` 也会处理 `FileNotFoundException`，而 `catch (FileNotFoundException fnfe)` 将永远没有机会执行。

注意

不要指定具有相同可抛出类型的多个 `catch` 块。例如，不要指定两个 `catch (IOException ioe) {}` 块。否则，编译器会报告错误。

JDK 7 使得在单个 `catch` 块中捕获多个异常成为可能。你可以指定一个以竖线分隔的异常类型列表，然后指定参数名称，该参数是 `final` 的（你不能在 `catch` 块中为该参数赋值）。请看以下示例，其中 `ex` 可能包含 `FileNotFoundException` 引用或 `IOException` 引用：

```
catch (FileNotFoundException | IOException ex)
```

### finally 块

无论异常是否被处理，你可能都需要执行清理任务，例如关闭打开的文件。Java 为此提供了 `finally` 块。

`finally` 块由关键字 `finally` 后跟一个用大括号括起来的语句序列组成，这些语句将被执行。其语法表示如下：

```
'finally'
'{'
// 一个或多个执行清理任务的语句
'}'
```

`finally` 块可以出现在最后一个 `catch` 块之后，或者 `try` 块之后。



#### 在 Try-Catch-Finally 上下文中进行清理

当资源必须被清理，且异常不会从方法中抛出时，`finally` 块会被放置在最后一个 `catch` 块之后。清单 11-1 演示了这一点，它展示了 `Copy` 应用程序的第一个版本。

```
import java.io.*;
public class Copy
{
public static void main(String[] args)
{
FileInputStream fis = null;
FileOutputStream fos = null;
try
{
fis = new FileInputStream(args[0]);
fos = new FileOutputStream(args[1]);
int c;
while ((c = fis.read()) != -1)
fos.write(c);
}
catch (FileNotFoundException fnfe)
{
System.out.println(fnfe.getMessage());
}
catch (IOException ioe)
{
System.out.println("I/O error: " + ioe.getMessage());
}
finally
{
if (fis != null)
try
{
fis.close();
}
catch (IOException ioe)
{
// ignore exception
}
if (fos != null)
try
{
fos.close();
}
catch (IOException ioe)
{
// ignore exception
}
}
}
}
清单 11-1
Copy.java
```

如果 `try` 块执行时没有发生异常，执行流程会传递到 `finally` 块以关闭文件输入/输出流。如果抛出了异常，则在相应的 `catch` 块执行完毕后，`finally` 块会执行。

`FileInputStream` 和 `FileOutputStream` 继承了一个 `void close()` 方法，当流无法关闭时，该方法会抛出 `IOException`。因此，我将 `fis.close();` 和 `fos.close();` 分别包裹在 `try` 块中。我将关联的 `catch` 块留空，以说明忽略异常这一常见错误。

一个被相应的可抛出对象调用但为空的 `catch` 块，无法报告该异常。你可能会浪费大量时间追踪异常的原因，最后才发现，如果这个空的 `catch` 块报告了异常（哪怕只是在日志中），你本可以更早地检测到它。

按如下方式编译清单 11-1：

```
javac Copy.java
```

按如下方式运行应用程序：

```
java Copy Copy.java Copy.bak
```

如果一切顺利，你应该会在当前目录中发现一个 `Copy.bak` 文件，其大小和内容与 `Copy.java` 的大小和内容相匹配。

#### 在 Try-Finally 上下文中进行清理

当资源必须被清理，且异常会从方法中抛出时，`finally` 块会被放置在 `try` 块之后：此时没有 `catch` 块。请考虑清单 11-2，它展示了 `Copy` 应用程序的第二个版本。

```
import java.io.*;
public class Copy
{
public static void main(String[] args)
{
if (args.length != 2)
{
System.out.println("usage: java Copy srcfile dstfile");
return;
}
try
{
copy(args[0], args[1]);
}
catch (IOException ioe)
{
System.out.println("I/O error: " + ioe.getMessage());
}
}
static void copy(String srcFile, String dstFile) throws IOException
{
FileInputStream fis = null;
FileOutputStream fos = null;
try
{
fis = new FileInputStream(srcFile);
fos = new FileOutputStream(dstFile);
int c;
while ((c = fis.read()) != -1)
fos.write(c);
}
finally
{
if (fis != null)
try
{
fis.close();
}
catch (IOException ioe)
{
System.out.println(ioe.getMessage());
}
if (fos != null)
try
{
fos.close();
}
catch (IOException ioe)
{
System.out.println(ioe.getMessage());
}
}
}
}
清单 11-2
Copy.java (版本 2)
```

文件复制逻辑已被移入一个 `copy()` 方法。该方法旨在向调用者报告异常，但它会首先关闭每个已打开的文件。

此方法的 `throws` 子句仅列出了 `IOException`。没有必要包含 `FileNotFoundException`，因为 `FileNotFoundException` 是 `IOException` 的子类。

再次强调，`finally` 子句为了关闭两个文件而呈现了大量代码。在下一节中，我将介绍 `try`-`with`-`resources` 语句，它消除了显式关闭这些文件的需要。

按如下方式编译清单 11-2：

```
javac Copy.java
```

按如下方式运行应用程序：

```
java Copy Copy.java Copy.bak
```

如果一切顺利，你应该会在当前目录中发现一个 `Copy.bak` 文件，其大小和内容与 `Copy.java` 的大小和内容相匹配。

## Try-with-resources 语句

我之前提到过，资源（实际上是它们所依赖的系统资源）会在 `finally` 块中被释放。这可能导致编写繁琐的样板代码，例如前面展示的文件关闭代码。

这些样板代码不仅增加了类文件的体积，编写时的繁琐还可能导致错误，甚至可能忘记关闭文件。JDK 7 引入了 `try`-`with`-`resources` 来克服这个问题。

`try`-`with`-`resources` 语句类似于 `try` 语句，但它允许你在执行离开 `try` 块时自动关闭任何已打开的资源。示例包括打开的文件、数据库连接、网络套接字以及其他依赖于相关系统资源（如文件句柄）的资源。其语法如下：

```
'try' '(' 资源获取语句 ')'
'{'
// 资源使用
'}'
```

`try` 关键字通过一个分号分隔的资源获取语句列表进行参数化，每个语句获取一个资源。每个获取的资源对 `try` 块可用，并在执行离开此主体时自动关闭。与常规的 `try` 语句不同，`try`-`with`-`resources` 不要求 `try()` 后面必须跟 `catch` 块和/或 `finally` 块，尽管它们可以被指定。请考虑以下面向文件的示例：

```
try (FileInputStream fis = new FileInputStream("notes.txt"))
{
// 使用 fis 和底层文件资源执行某些操作
}
```

在此示例中，获取了一个指向底层文件资源 (`notes.txt`) 的输入流。`try` 块对该资源执行某些操作，并且在退出 `try` 块时，流（和文件）会被关闭。

清单 11-3 通过使用 `try`-`with`-`resources` 自动关闭打开的文件并消除样板代码，改进了清单 11-2 中的 `Copy` 应用程序。

```
import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.FileOutputStream;
import java.io.IOException;
public class Copy
{
public static void main(String[] args)
{
if (args.length != 2)
{
System.err.println("usage: java Copy srcfile dstfile");
return;
}
try
{
copy(args[0], args[1]);
}
catch (IOException ioe)
{
System.err.println("I/O error: " + ioe.getMessage());
}
}
static void copy(String srcFile, String dstFile) throws IOException
{
try (FileInputStream fis = new FileInputStream(srcFile);
FileOutputStream fos = new FileOutputStream(dstFile))
{
int c;
while ((c = fis.read()) != -1)
fos.write(c);
}
}
}
清单 11-3
Copy.java (版本 3)
```

`copy()` 方法使用 `try`-`with`-`resources` 来管理源文件和目标文件资源。`try` 后面圆括号内的代码尝试为这些文件创建文件输入和输出流。假设创建成功，其主体执行，将源文件复制到目标文件。

无论是否抛出异常，`try`-`with`-`resources` 都能确保在执行离开 `try` 块时两个文件都被关闭。由于前面展示的样板文件关闭代码不再需要，清单 11-3 中的 `copy()` 方法更加简单且易于阅读。

按如下方式编译清单 11-3：

```
javac Copy.java
```

按如下方式运行应用程序：

```
java Copy Copy.java Copy.bak
```

如果一切顺利，你应该会在当前目录中发现一个 `Copy.bak` 文件，其大小和内容与 `Copy.java` 的大小和内容相匹配。

## 接下来是什么？

到目前为止，我们的重点主要放在语言语法上。在本书的其余部分，我们将主要关注 Java 引用类型库中的 API。第 12 章将从查看 `Math` 和相关类开始。



