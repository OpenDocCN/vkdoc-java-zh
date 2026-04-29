# 2. Java 9 至 Java 17 的增强特性

自 Java 9 以来，Java 的新版本每六个月发布一次，但这是一个长期支持（LTS）版本。它至少会获得八年的支持，并且肯定会持续到 2023 年的下一个 LTS 版本。JDK 的每个版本都为 Java 平台带来了新的增强功能和能力。自本书上一版以来，已经发生了许多增强。本书包含多个实践指南，涵盖了从 Java 9 到 Java 17 的新特性。每个版本也保持了与先前版本的向后兼容性。本章展示了一些最重要的增强特性，以激发你的兴趣。本章绝非 Java 所有增强功能的完整列表。相反，它是一个快速入门指南，帮助你了解 Java 直到最新版本的一些热门新特性。

## 2.1 var 关键字简介

### 问题

你想使用一个能够根据上下文推断变量数据类型的关键字。

### 解决方案

使用 Java 10 中引入的局部变量类型推断。清单 2-1 是一个示例。

```
org.java17recipes.chapter01.recipe02_01;
public class KeywordVar {
public static void main (String[] args) {
var num = 1;
var city = "Taranto";
System.out.println(num);
System.out.println(city);
}
}
清单 2-1
声明局部变量类型推断包
```

现在你应该会看到以下输出。

```
1
Taranto
```

### 工作原理

在 Java 10 之前，你按以下方式声明。

```
String city = "Taranto";
```

接着，你可以按以下方式声明。

```
var city = "Taranto"
```

它允许程序员声明局部变量的类型而非实际类型，从而提高了代码的可读性。编译器根据分配给变量的值来决定类型。在 Java 11 中，`var` 的用法被统一扩展到 lambda 参数，如第 6 章所述。

局部变量类型推断不能用作全局变量。事实上，如果你使用以下代码，IDE 会引发编译错误。

```
// 实例变量
var x = 50;
public static void main(String[] args)
{
System.out.println(x);
}
```

输出是一个错误。

```
'var' is not allowed here
```

有关 `var` 关键字的更多信息，请参阅 [`​openjdk.​java.​net/​jeps/​286`](http://openjdk.java.net/jeps/286) 上的文档。

注意

JEP（JDK 增强提案）收集对 JDK 的增强建议，并允许 OpenJDK 以更非正式的方式开发变更。然而，它并不能取代 JCP，JCP 是批准 Java API 变更所必需的。

## 2.2 读取文件内容

### 问题

你想读取文件的内容。

### 解决方案

使用 `java.nio.file.Files` 类，该类提供了操作文件和目录的静态方法（参见清单 2-2）。

```
import java.nio.file.Path;
import java.nio.file.Paths;
import java.nio.file.Files;
import java.io.IOException;
class ReadContentsFile
{
public static void main(String[] args)
{
Path filePathHello = Paths.get("C:/hello.txt");
try
{
String contents = Files.readString(filePath filePathHello);
System.out.println(contents);
}
catch (IOException err)
{
err.printStackTrace();
}
}
}
清单 2-2
读取文件内容
```

以下是输出。

```
Ciao da
Luciano Manelli
Taranto
ITALY
```

这是作者计算机上一个文本文件的内容。

### 工作原理

Java 11 包含了一些新的 `Files` 类方法，用于从文件中读取内容。`Files.readString(Path)` 方法从文件中读取字符，接受文件路径，并返回一个包含文件内容的字符串。如果发生错误，它会抛出 IOException。用于解码的字符集是 UTF-8。

你也可以使用 `Files.readString(Path, Charset)` 方法。它用于使用特定的字符集将字节解码为字符。它从文件中读取并抛出 IOException。`Files.readString(Path)` 方法等同于 `readString(path, StandardCharsets.UTF_8)`。

清单 2-3 是一个示例。

```
import java.nio.file.Path;
import java.nio.file.Paths;
import java.nio.file.Files;
import java.io.IOException;
import java.nio.charset.StandardCharsets;
public class Main
{
public static void main(String[] args)
{
Path filePathHello = Paths.get("C:/hello.txt");
try
{
String contents = Files.readString(filePath filePathHello, StandardCharsets.UTF_8);
System.out.println(contents);
}
catch (IOException err)
{
err.printStackTrace();
}
}
}
清单 2-3
使用字符集读取文件内容
```

## 2.3 编写文本块

### 问题

你想以一种巧妙的方式编写一个多行字符串。

### 解决方案

Java 15（JEP 378）引入了一种在简单文本块中编写多行字符串的智能形式，尽管它最初是在 Java 13 中提出的。

以下是一个示例。

```
public class MultipleLineStringEx {
public static void main (String[] args) {
String writeTextStandard = "\n" +
"      Ciao, hello\n" +
"\n";
String writeTextSmart = """

Ciao, hello

""";
System.out.println(writeTextStandard);
System.out.println(writeTextSmart);
}
}
```

输出是相同的。

### 工作原理

Java 13 引入了一项增强功能，使多行字符串更具可读性。因此，文本块有效地消除了多个字符串的拼接。需要选择一种方式来视觉区分文本块和字符串字面量：选择了分隔符 `"""`。此外，缩进被移除并替换为空白字符。

有关文本块的更多信息，请参阅 [`​openjdk.​java.​net/​jeps/​378`](https://openjdk.java.net/jeps/378) 上的文档。

## 2.4 NullPointerException 的增强

### 问题

当发生 NullPointerException 时，你想知道哪个变量是 null。

### 解决方案

Java 14 改进了发生错误时生成的 NullPointerException 功能。

以下是一个示例。

```
第 1 行:package org.java17recipes.chapter02.recipe02_04;
第 2 行:
第 3 行:public class NullPointerExample {
第 4 行:        public static void main (String[] args) {
第 5 行:                String professor= null;
第 6 行:                System.out.println(professor.length());
第 7 行:        }
第 8 行:}
```

以下是输出。

```
Exception in thread "main" java.lang.NullPointerException:
Cannot invoke "String.length()" because "professor" is null
at org.java17recipes.chapter02.recipe02_04.NullPointExample.main(NullPointerExample.java:6)
```



### 工作原理

在 Java 14 中，Java 虚拟机能够精确描述哪个变量为 null。异常信息位于异常类型所在行：在之前的版本中，缺少了“Cannot invoke “String.length()” because “professor” is null.”这句话。另一方面，这一增强功能自然会在生成堆栈跟踪时产生开销。有关 NullPointerException 的更多信息，请参阅文档 [`https://openjdk.java.net/jeps/358`](https://openjdk.java.net/jeps/358)。

## 2.5 instanceof 的模式匹配

### 问题

你希望简化从对象中有条件地提取组件的操作。

### 解决方案

Java 14 改进了该功能，使类型转换更加简洁。

在以下示例中，前几行代表该功能的旧版本。

```
public class InstanceOfExample {
public static void main (String[] args) {
Object selectedObject="I love Taranto";
if (selectedObject instanceof String) {
String selectedString = (String) selectedObject;
System.out.println(selectedString.length());
}
if (selectedObject instanceof String selectedString) {
System.out.println(selectedString.length());
}
}
}
```

### 工作原理

此增强功能在 Java 14 中提出，并在 Java 16（JEP 394）中投入生产。它是一种可读性强且简洁的形式，改进了 `instanceof` 运算符的模式匹配。

新版本提高了代码的可读性，避免了重复多次类型名称以及产生错误的可能性。

有关 `instanceof` 运算符模式匹配的更多信息，请参阅文档 [`​openjdk.​java.​net/​jeps/​394`](https://openjdk.java.net/jeps/394)。

## 2.6 使用 Record

### 问题

你希望声明具有简单管理主要和标准方法的类。

### 解决方案

使用 Record 类，它可以高效地对类进行建模。下面是一个实体类的标准代码。

```
public class Professor {
private Integer id;
private String name;
private String surname;
public Professor () {
}
public Integer getId() {
return id;
}
public void setId(Integer id) {
this.id = id;
}
public String getName() {
return name;
}
public void setName(String name) {
this.name = name;
}
public String getSurname() {
return surname;
}
public void setSurname(String surname) {
this.surname = surname;
}
}
```

从 Java 14 开始，这个冗长的类可以定义如下。

```
record Professor (Integer id, String name, String surname) {}
```

因此，你可以简单地创建一个新的 prof。

```
Professor prof = new Professor (1, "Luciano", "Manelli");
```

而不是使用标准的旧方式。

```
Professor prof = new Professor ();
prof.setId(1);
prof.setName("Luciano");
prof.setSurname("Manelli");
```

以下是主类。

```
public class Main {
public static void main (String[] args) {
Professor prof = new Professor ();
prof.setId(1);
prof.setName("Luciano");
prof.setSurname("Manelli");
System.out.println("Prof "+prof.getId()+":"+prof.getName()+" "+prof.getSurname());
record ProfessorRecord (Integer id, String name, String surname) {};
ProfessorRecord profRecord = new ProfessorRecord (1, "Luciano", "Manelli");
System.out.println("Prof using Record "+profRecord.id()+":"+profRecord.name()+" "+profRecord.surname());
}
}
```

### 工作原理

此增强功能在 Java 14 中提出，并在 Java 16（JEP 394）中成为标准特性。它通过提供声明类的紧凑语法来帮助开发者，避免了 Java 语言众所周知的冗长性。

在某些情况下，保留数据的不可变性并创建 `equals`、`hashCode` 和 `toString` 方法非常有用。第一个方法在所有字段匹配相同类时验证对象的相等性。第二个方法在所有字段匹配时返回一个唯一的整数值。第三个方法返回一个由类名以及每个组件的名称和值派生而来的字符串。

有关 Record 类的更多信息，请参阅文档 [`​openjdk.​java.​net/​jeps/​395`](https://openjdk.java.net/jeps/395)。

## 2.7 恢复始终严格的浮点语义

### 问题

你希望确保浮点数在每个平台上都具有相同的精度和结果。

### 解决方案

Java 17 恢复了默认的始终严格功能，使浮点运算统一严格。因此，你可以确保平台无关的浮点计算；例如，以下是旧版本。

```
strictfp class  AppliedClass{}//strict floating point applied on class
strictfp interface AppliedInterface{}// strict floating point applied on interface
```

而新版本则简单如下。

```
class AppliedClass{}//strictfp applied on class
interface AppliedInterface{}//strictfp applied on interface
```

以下是一个示例。

```
public strictfp class SumFPnumbersStrict {
public double sumNumbers(double fpOne, double fpTwo) {
return fpOne + fpTwo;
}
```

它变成了以下形式。

```
public class sumFPnumbers {
public double SumNumbers(double fpOne, double fpTwo) {
return fpOne + fpTwo;
}
```

以下是主类的一个示例。

```
public class Main {
public static void main (String[] args) {
SumFPnumbers sn = new SumFPnumbers ();
System.out.println("SUM :  "+sn.sumNumbers(1.2,3.4));
SumFPnumbersStrict sns = new SumFPnumbersStrict();
System.out.println("SUM :  "+sns.sumNumbers(1.2,3.4));
}
}
```

### 工作原理

在 Java 17 之前，浮点计算是平台相关的（即依赖于硬件）。此增强功能有助于避免 `strictfp` 行为，并且不会使所有浮点运算统一严格。使用 `strictfp` 的现有 Java 代码可以在不使用该修饰符的情况下使用。

有关浮点数的更多信息，请参阅文档 [`​openjdk.​java.​net/​jeps/​306`](https://openjdk.java.net/jeps/306)。

## 2.8 伪随机数生成器

### 问题

你希望生成随机数。

### 解决方案

Java 17 通过为伪随机数生成器（PRNG）提供新的接口类型和实现，并保留 `java.util.Random` 类的先前行为，改进了该功能。

一个示例是生成一个伪随机整数。

```
import java.util.random.RandomGenerator;
import java.util.Random;
public class UseNewPRNGs implements RandomGenerator {
static UseNewPRNGs testGen = new UseNewPRNGs();
public static void main(String[] args){
for (int i = 0; i < 5; i++) {
System.out.println(testGen.nextBoolean());
System.out.println(testGen.nextDouble());
System.out.println(testGen.nextInt());
System.out.println(testGen.nextLong());
}
}
@Override
public long nextLong() {
//Returns a pseudorandomly chosen long value.
Random r= new Random();
return r.nextLong();
}
}
```

以下是输出结果。

```
false
0.4134883035327678

true
0.37651481128199904

true
0.8508900370304876

true
0.10116382711133465

false
0.01988848947697064

```

### 工作原理

Java 17 提供了一个新接口 `RandomGenerator`，它为所有现有和新的 PRNG 提供了统一的 API。它具有默认方法，在前面的代码中只重写了 `nextLong` 方法。它为生成随机或伪随机数字或布尔值序列的对象提供了通用协议。此增强功能主要旨在使各种 PRNG 算法在应用程序中可互换使用，并保留 `java.util.Random` 类的现有行为。

有关 PRNG 的更多信息，请参阅文档 [`​openjdk.​java.​net/​jeps/​356`](https://openjdk.java.net/jeps/356)。

## 2.9 密封类

### 问题

你希望创建一个类或接口，并简单地决定应该使用哪个修饰符。



### 解决方案

Java 17 最终确定了用于类和接口的密封作用域修饰符。

在以下示例中，`LineShape` 类仅允许且只允许三个子类：`Rectangle`、`Triangle` 和 `Square`。

```
public sealed class LineShape permits Rectangle, Triangle, Square {}
```

在以下示例中，`Rectangle` 类被声明为 `final`。因此，没有其他类可以扩展此类。

```
public final class Rectangle extends LineShape {}
```

在以下示例中，`Triangle` 类被声明为 `non-sealed`，为未知类开放了扩展。

```
public non-sealed class Triangle extends LineShape {}
```

最后，

```
public sealed class Square extends LineShape permits ColorSquare {}
其中 `Square` 类仅允许且只允许 `ColorSquare`：
public final class ColorSquare extends Square {}
```

### 工作原理

此增强功能在 Java 15 中提出，并在 Java 17（JEP 409）中最终确定，与上一版本相比没有变化。密封作用域修饰符允许控制类和接口的允许子类型。一个类也可以是 `non-sealed`（即，它可以被任何未知的子类扩展）。

密封类对其允许的子类设置了三个约束条件（即 `Rectangle` 和 `Triangle` 子类）必须与密封类属于同一模块，它们必须扩展密封类（`Rectangle extends LineShape` 和 `public non-sealed class Triangle extends LineShape`）。子类必须定义一个修饰符：`final`、`sealed` 或 `non-sealed`。

要编译 `LineShape.java`，编译器必须访问 `Shape` 的所有允许类：`Triangle.java`、`Square.java` 和 `Rectangle.java`。此外，由于 `Square` 是一个密封类，编译器还需要访问 `ColorSquare.java`。而且，如果一个“无效”类试图扩展 `LineShape`，如下例所示，编译器会给出警告：“类型 `Rhombus` 扩展了密封类 `LineShape`，它应该是 `LineShape` 的允许子类型。”

```
public final class Rhombus extends LineShape {}
```

此特性在 Java 语言中引入了三个新关键字：`sealed`、`permits` 和 `non-sealed`。

有关密封类的更多信息，请参阅 [`​openjdk.​java.​net/​jeps/​409`](https://openjdk.java.net/jeps/409) 上的文档。

## 2.10 Vector API

### 问题

您需要一个可移植的 API 来表达向量计算。

### 解决方案

Java 17 引入了一个用于向量计算的 API，其性能优于等效的标量计算。以下是两个数组求和的标椎代码。

```
static void nonVectorSumInt(int[] arrayOne, int[] arrayTwo, int[] s) {
for (int i = 0; i < arrayOne.length; i++) {
s[i] = (arrayOne[i]  +  arrayTwo[i]) ;
}
}
```

以下是使用向量的代码。

```
static final VectorSpecies SPECIES = IntVector.SPECIES_PREFERRED;
static void vectorSumInt(int[] arrayOne, int[] arrayTwo, int[] s) {
int i = 0;
int upperBound = SPECIES.loopBound(arrayOne.length);
for (; i < upperBound; i += SPECIES.length()) {
var vIntaOne = IntVector.fromArray(SPECIES, arrayOne, i);
var vIntaTwo = IntVector.fromArray(SPECIES, arrayTwo, i);
var vs = vIntaOne.add(vIntaTwo);
vs.intoArray(s, i);
}
for (; i < arrayOne.length; i++) {
s[i] = (arrayOne[i]  + arrayTwo[i] );
}
}
```

为了测试上述代码，创建主类。

```
public static void main(String[] args) {
int[] a = new int[]{1, 3, 2, 4};
int[] b = {5, 6, 7, 8};
int[] c = {0, 0, 0, 0};
VectorExample.nonVectorSumInt(a, b, c);
System.out.println("nonVectorSumInt");
for (int i = 0; i < c.length; i++) {
System.out.println(c[i]);
}
VectorExample.vectorSumInt(a, b, c);
System.out.println("vectorSumInt");
for (int i = 0; i < c.length; i++) {
System.out.println(c[i]);
}
}
```

一个*首选*物种（preferred species）是平台支持的最大位大小的物种（即平台支持的向量的首选长度）。打开一个终端窗口，用于编译和执行命令行。

以下是输出结果。

```
nonVectorSumInt

vectorSumInt

```

### 工作原理

您可以从终端窗口编译和执行。使用以下命令将 `jdk.incubator.vector` 模块添加到类路径中。

```
--add-modules jdk.incubator.vector
```

因此，您必须编写以下命令。

```
javac --add-modules jdk.incubator.vector  VectorExample.java
```

编译该类后，您可以使用以下命令运行它。

```
java --add-modules jdk.incubator.vector  VectorExample
```

此增强功能在 Java 16（JEP 338）中提出，旨在增加在支持的 CPU 架构上由向量指令执行的计算数量。在前面的示例中，目标是添加两个向量，每个向量包含两个整数值：在这种情况下，向量硬件在单个 CPU 周期内执行两次加法，而普通操作只允许一次。

有关向量的更多信息，请参阅 [`​openjdk.​java.​net/​jeps/​414`](https://openjdk.java.net/jeps/414) 上的文档。

## 2.11 避免接口代码中的冗余

### 问题

您想要在接口中实现两个或多个具有非常相似代码的默认方法。您希望将相似的代码封装到自己的方法中以供重用，而不是将代码复制到每个不同的默认方法中并分别维护每个默认方法。

### 解决方案

在接口中使用私有方法来缓解此问题。Java 9 提供了在接口中包含私有方法的能力。私有方法仅在该接口内可用，并且不能被任何实现该接口的类使用。但是，作为接口一部分的每个默认方法实现都可以使用该私有方法。

以下接口包含两个默认方法和一个私有方法。私有方法封装了可以在每个默认方法实现中使用的功能。

```
public interface Pool {
/**
* 计算固定深度方形或矩形水池的体积（加仑）。
*/
public default double squareOrRectConstantDepth(double length, double width, double depth){
return volumeCalc(length, width, depth);
}
/**
* 计算可变深度方形或矩形水池的体积（加仑）。
*/
public default double squareOrRectVariableDepth(double length, double width, double shallowDepth, double middleDepth, double deepDepth){
double avgDepth = (shallowDepth + middleDepth + deepDepth) / 3;
return volumeCalc(length, width, avgDepth);
}
/**
* 标准方形或矩形体积计算。
*/
private double volumeCalc(double length, double width, double depth){
return length * width * depth * 7.5;
}
}
为了测试上述代码，创建主类。
public class PoolExample implements Pool {
public static void main(String args[]) {
PoolExample pe = new PoolExample();
System.out.println(pe.squareOrRectConstantDepth(10,10,10));
}
}
```

以下是输出结果。

```
7500.0
```



### 工作原理

在 Java 8 之前，无法在 Java 接口中包含代码实现。接口是 Java 中的一种引用类型，类似于类。然而，其最初的设计意图只允许抽象方法、常量、静态方法和嵌套类型。因此，实现接口的类必须实现每一个抽象方法。在 Java 8 中，这一限制被解除，可以通过默认方法的形式包含方法实现。默认方法可以在接口中包含实现，或者实现类可以重写其实现以进行完全覆盖或添加扩展。因此，术语*默认方法*的含义是：如果实现类没有提供实现，则默认方法实现存在于接口中。接口中不允许有私有方法。

在某些情况下，接口内的多个默认方法可能包含相似的代码。现在，这些代码可以封装在接口内的私有方法实现中。私有方法实现不能在接口外部使用。在本节方案的解决方案中，`volumeCalc()` 方法使用标准公式返回正方形或长方形游泳池的计算体积。接口内的每个默认方法都可以利用 `volumeCalc()` 方法来计算体积。但是，`volumeCalc()` 方法在接口外部不可用。

这似乎存在争议，因为接口最初仅用于字段和方法声明。也可以认为，在多个默认方法实现中复制相同的代码是一种不良实践。你可以自行判断，这个特性使得在接口内重用代码更加容易，从而减少了出错的机会，并使维护变得更加简单。

## 2.12 轻松检索操作系统进程信息

### 问题

你希望能够查找有关操作系统进程的信息。

### 解决方案

使用更新后的 Process API。新的 `ProcessHandle` 接口让你可以轻松获取操作系统进程的信息。在以下代码中，所有操作系统进程都被列出并打印到终端窗口。

```
public static void listProcesses(){
ProcessHandle.allProcesses()
.forEach(System.out::println);
}
```

然而，这并没有太大帮助，因为它只是列出了每个操作系统的进程号，这并不十分有用。要获取有关进程的更多详细信息，我们需要获取 `ProcessHandle` 并调用其辅助方法，这非常简单。以下代码打印了关于每个进程的更多信息，因为它打印了 `ProcessHandle.Info` 本身。

```
public static void detailListProcesses(){
ProcessHandle.allProcesses()
.forEach(h->System.out.println(formattedProcess(h)));
}
public static String formattedProcess(ProcessHandle handle){
long pid = handle.pid();
boolean alive = handle.isAlive();
Optional cpuDuration = handle.info().totalCpuDuration();
Optional handleName = handle.info().command();
return pid + " " + alive + " " + handleName + ":"+ cpuDuration;
}
```

以下是主类。

```
public static void main (String[] args){
listProcesses();
detailListProcesses();
detailListProcessUsers();
}
```

示例输出可能如下所示。

```
3216 true Optional[C:\Program Files (x86)\Google\Chrome\Application\chrome.exe]:Optional[PT0.15625S]
2588 false Optional.empty:Optional.empty
6636 false Optional.empty:Optional.empty
10140 false Optional.empty:Optional.empty
8776 true Optional[C:\Program Files (x86)\Notepad++\notepad++.exe]:Optional[PT2.859375S]
11128 true Optional[C:\Program Files (x86)\Google\Chrome\Application\chrome.exe]:Optional[PT0.0625S]
14184 true Optional[C:\Program Files (x86)\Google\Chrome\Application\chrome.exe]:Optional[PT0.109375S]
14316 true Optional[C:\Windows\System32\SearchProtocolHost.exe]:Optional[PT0.0625S]
11356 false Optional.empty:Optional.empty
1468 true Optional[C:\jdk-17\bin\javaw.exe]:Optional[PT0.25S]
```

如果你想检索有关运行进程的用户的信息，也很容易做到。

```
public static void detailListProcessUsers(){
ProcessHandle.allProcesses()
.forEach(h->System.out.println(listOsUser(h)));
}
public static String listOsUser(ProcessHandle handle){
ProcessHandle.Info procInfo = handle.info();
return handle.pid() + ": " +procInfo.user();
}
```

使用此技术的示例输出可能如下所示。

```
412: Optional.empty
14444: Optional[LAPTOP-BDD02I2D\lucky]
11648: Optional[LAPTOP-BDD02I2D\lucky]
7012: Optional[LAPTOP-BDD02I2D\lucky]
12908: Optional[LAPTOP-BDD02I2D\lucky]
10408: Optional[LAPTOP-BDD02I2D\lucky]
7984: Optional[LAPTOP-BDD02I2D\lucky]
1528: Optional[LAPTOP-BDD02I2D\lucky]
```

### 工作原理

`ProcessHandle` 接口是在 Java 9 中引入的，使得检索操作系统进程信息成为 JDK 的一等公民。

## 2.13 处理 try-with-resources 结构

### 问题

你希望轻松管理有效最终变量的关闭。

### 解决方案

`try-with-resources` 结构是在 Java 7 中引入的，允许轻松进行资源管理。在 Java 9 版本中，它变得更加容易，因为无需为了该结构而有效地创建一个新变量。在以下代码中，`writeFile()` 方法接受一个 `BufferedWriter` 作为参数，由于它被传入方法并准备使用，因此它是有效最终的。这意味着它可以简单地列在 `try-with-resources` 中，而无需创建新变量。

```
public static void main(String[] args) {
try {
writeFile(new BufferedWriter(
new FileWriter("Easy TryWithResources")),
"This is easy since Java 9");
} catch (IOException ioe) {
System.out.println(ioe);
}
}
public static void writeFile(BufferedWriter writer, String text) {
try (writer) {
writer.write(text);
} catch (IOException ioe) {
System.out.println(ioe);
}
}
```

这段代码创建了一个名为 EasyTryWithResources 的新文件。它添加了文本“This is easy in Java 9”。

### 工作原理

`try-with-resources` 结构在 Java 9 中变得更加容易，允许非常轻松地处理资源的打开和关闭。如果我们有一个资源，例如数据库连接或 `BufferedStream`，明智地管理它是很好的。换句话说，打开资源，然后相应地使用它，最后在完成后关闭资源以确保没有资源泄漏。`try-with-resources` 结构允许在 try 块内打开一个资源，并在完成后自动清理它。

解决方案在 Java 9 中展示。如果一个资源作为参数或最终字段传入方法，则可以简单地在 `try-with-resources` 结构中使用它。虽然这不是一个重大的语言变更，但它确实使处理资源变得稍微容易一些，使 `try-with-resources` 块更容易理解。

## 2.14 使用流在条件前后过滤数据

### 问题

你希望利用流来有效地操作你的集合。在这样做的同时，你希望在指定条件发生之前和/或之后过滤这些流。最终，你希望检索在给定谓词条件满足之前集合中的所有数据。你还希望检索在给定谓词条件满足之后集合中的所有数据。



### 解决方案

利用 Java 新增的 `takeWhile()` 和 `dropWhile()` 结构处理流。假设我们有以下数据集合，希望获取所有包含单词 *Java* 的元素之前的全部元素。

```
List myLangs = Arrays.asList("Jython is great","Groovy is awesome",
"Scala is functional", "JRuby is productive","Java is streamlined","","Kotlin is interesting");
```

要获取所有位于包含“Java”字符串的元素之前的元素，我们可以使用 `takeWhile()` 结构，如下所示。

```
Stream.of("Jython is great","Groovy is awesome","Scala is functional",
"JRuby is productive","Java is streamlined","","Kotlin is interesting")
.takeWhile(s -> !s.contains("Java"))
.forEach(System.out::println);
```

假设我们希望获取所有位于包含“Java”字符串的元素之后的元素。我们可以使用 `dropWhile()` 结构，如下所示。

```
Stream.of("Jython is great","Groovy is awesome","Scala is functional",
"JRuby is productive","Java is streamlined","","Kotlin is interesting")
.dropWhile(s -> !s.contains("Java"))
.forEach(System.out::println);
```

以下是主类。

```
public static void main(String[] args){
List myLangs = Arrays.asList("Jython is great","Groovy is awesome","Scala is functional","JRuby is productive","Java is streamlined","","Kotlin is interesting");
System.out.println("Collection Data: " + myLangs);
takeWhileExample();
dropWhileExample();
}
```

以下是输出结果。

```
Collection Data: [Jython is great, Groovy is awesome, Scala is functional, JRuby is productive, Java is streamlined, , Kotlin is interesting]
takeWhileExample:
Jython is great
Groovy is awesome
Scala is functional
JRuby is productive
dropWhileExample:
Java is streamlined
Kotlin is interesting
```

### 工作原理

流改变了我们在 Java 中开发代码和处理数据集合的方式。最初可用于流的过滤器集合相当丰富。然而，从 Java 8 开始，增加了更多选项，使得使用流精炼数据变得更加容易。`takeWhile()` 和 `dropWhile()` 结构允许对流进行解析。一方面，它们返回一个新的流，其中包含在第一个不满足指定谓词条件的元素之前的所有元素。另一方面，它们分别返回一个新的流，其中包含从第一个不满足指定谓词条件的元素开始（包括该元素）及其之后的所有元素。

本方案的解决方案解析字符串列表，并在第一次遍历时将每个元素打印到终端窗口。然后，将 `takeWhile()` 结构应用于相同的字符串流，并将流中位于不满足指定条件的元素之前的元素打印到终端窗口。`takeWhile()` 接受一个谓词条件，然后将其应用于流中的每个元素。然后，仅返回在谓词条件不匹配之前迭代的那些元素。流中位于条件不满足位置及其之后的所有元素都不会被返回。

使用 `dropWhile()` 结构时会产生相反的结果。在解决方案中，所有流元素都会被忽略，直到返回第一个不再满足指定条件的元素。流中的每个后续元素也会被返回。

`takeWhile` 和 `dropWhile` 结构与过滤器非常相似，区别在于，一旦有一个条件不满足，就会分别导致剩余元素被忽略或返回。

## 2.15 使用工厂方法创建不可变集合

### 问题

您希望生成一个不可变的值集合（其状态在构造后不会改变）。

### 解决方案

使用 `Collection.of()` 结构生成不可变集合。在以下示例中，创建了两个集合。第一个是不可变的 `List<String>`，第二个是不可变的 `Map<Integer, String>`。

```
public static void main(String[] args){
List jvmLanguages = List.of("Java", "Scala", "JRuby", "Groovy", "Jython", "Kotlin");
System.out.println(jvmLanguages);
try {
jvmLanguages.add("Exception");
} catch (UnsupportedOperationException uoe){
System.out.println(uoe);
}
Map  players = Map.of(1, "Josh Juneau", 2, "Jonathan Gennick", 3, "Freddy Guime", 4, "Carl Dea", 5, "Luciano Manelli");
System.out.println(players.values());
System.out.println("Player 5: " + players.get(5));
try {
players.put(6,"Exception");
} catch (UnsupportedOperationException uoe){
System.out.println(uoe);
}
}
```

输出结果如下所示。请注意，该示例包含一个 `try-catch` 块，用于捕获尝试修改列表和映射时抛出的 `UnsupportedOperationException`。

```
[Java, Scala, JRuby, Groovy, Jython, Kotlin]
java.lang.UnsupportedOperationException
[Josh Juneau, Jonathan Gennick, Freddy Guime, Carl Dea, Luciano Manelli]
Player 5: Luciano Manelli
java.lang.UnsupportedOperationException
```

### 工作原理

长期以来，Java 在执行小任务时一直是一种冗长的语言。过去，构造一个填充了数据的集合需要几行代码。第一行必须初始化集合，然后为添加到集合中的每个项目编写一行代码。Java 添加了便捷的 API 来快速生成不可修改的数据集合，现在可以用一行代码初始化和填充该结构。

工厂方法已添加到 `List<E>`、`Set<E>` 和 `Map<K,V>` 接口中，用于创建此类不可修改的数据集合。工厂方法包括 `of()` 方法，该方法最多接受十个值，快速创建一个不可变集合。`Map` 工厂方法接受十个键/值对。此外，不能将 null 值作为元素、键或值填充。

## 2.16 switch 的模式匹配（预览）

### 问题

您希望高效地针对不同数量的值测试同一个变量。

### 解决方案

switch 命令直到 Java 17 才支持此功能，并且作为预览功能。目前，您可以使用一系列 if-else 测试。以下是一个示例。

```
public static void main(String[] args){
Object inputObject  = 500L;
String formattedObject =”input object is not formatted corretly!”;
if (inputObject instanceof Integer i) {
formattedObject = String.format("Integer %d", i);
} else if (inputObject instanceof String s) {
formattedObject = String.format("String %s", s);
} else if (inputObject instanceof Long l) {
formattedObject = String.format("Long %d", l);
} else if (inputObject instanceof Double d) {
formattedObject = String.format("Double %f", d);
}
```

在 Java 17 中，代码可以智能且可靠地重写，如下所示。

```
Object inputObject  = 500L;
String formattedObject = switch (inputObject ) {
case Integer i -> String.format("int %d", i);
case Long l    -> String.format("long %d", l);
case Double d  -> String.format("double %f", d);
case String s  -> String.format("String %s", s);
default        -> inputObject .toString();
};
```

打开终端窗口进行编译和执行命令行。以下是输出结果。

```
long 500
```



### 工作原理

Java 17 为 switch 命令引入了模式匹配，因为 if…else 代码复杂且难以优化，而 switch 更适合模式匹配。实际上，如果值与模式匹配，case 标签就会匹配选择器表达式。

switch 语句中编写的模式是一项预览功能，默认处于禁用状态。你可以通过终端窗口进行编译和执行。请使用以下命令启用 switch 语句中的模式预览功能。

```
--enable-preview
```

使用以下命令编译指定的 Java 17 SE 版本。

```
–release 17
javac --enable-preview --release 17 PattSwitchEx.java
```

如果你还使用 `-Xlint` 命令获取详细信息，编译器会针对预览功能模式发出警告，因为这些模式可能会在未来的版本中被移除。

执行编译后的类时，只需编写以下命令。

```
java --enable-preview PattSwitchEx
```

有关 switch 模式匹配的更多信息，请参阅 [`​openjdk.​java.​net/​jeps/​406`](https://openjdk.java.net/jeps/406) 上的文档。

## 2.17 本章小结

本章介绍了 Java 17 中的一些新特性和增强功能。虽然这并非新特性的完整列表，但本章深入探讨了一些最受期待的特性，包括向量和 switch 模式匹配。建议阅读全书以更全面地了解新特性。不过，本章已让你初步领略了未来的发展方向。

