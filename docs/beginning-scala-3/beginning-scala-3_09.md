# 9. Scala 与 Java 互操作性

本章描述了 Scala 如何被翻译成 Java，以及 Java 注解在 Scala 框架中的使用。本章的目标是向你展示如何轻松地将 Scala 与 Java 集成。你还将学习 Scala 注解如何帮助集成，例如在生成 JavaBean 风格的 getter 和 setter 时。

## Scala 概览

Scala 的主要设计目标之一是在 JVM 上运行并提供与 Java 的互操作性。当你想要使用现有的 Java 库或框架时，就会产生对 Scala 和 Java 互操作性的需求。Scala 代码通常与大型 Java 程序和框架一起使用。尽管在大多数情况下与 Java 集成很容易，但我们鼓励你尽可能多地使用纯 Scala。当你使用 Java 库或框架时，首先尝试寻找 Scala 中的等价物。如果没有可用的等效 Scala 库，则使用 Java。

Scala 被编译成 Java 字节码，你可以使用像 Java 类文件反汇编器 `javap` 这样的工具来反汇编 Scala 编译器生成的字节码。通过在 JVM 上运行，你可以利用构建在其他 JVM 语言中的所有框架和工具。在大多数情况下，Scala 特性会被翻译成 Java 特性，以便 Scala 可以轻松地与 Java 集成。然而，一些 Scala 特性并不能直接映射到 Java，在这种情况下，你必须学习如何处理 Java 中有但 Scala 中没有的特性。Java 中有但 Scala 中没有的特性是静态成员和受检异常。Scala 中有但 Java 中没有的特性示例是特质。如果你从 Scala 中使用 Java，你必须处理 Scala 世界中禁止的可变性、异常和空值。因为 Scala 可以与 Java 无缝协作，大多数时候你可以组合使用这两种语言而无需过多担心。

请注意，在撰写本文时，Java 的长期支持（LTS）版本是 11，这意味着新版本的 Java 可能会出现新特性，这些特性在某种程度上与 Scala 兼容。

## 将 Java 类翻译成 Scala 类

Scala 类和 Java 类之间的互操作性使得用 Scala 类替换或扩展现有的 Java 类变得简单直接。以下代码展示了 Java 中的 `class` 声明。

```
public class Book {}
```

以下是 Scala 代码：

```
class Book
```

正如你在第 3 章中学到的，当没有内容时不需要花括号，并且在 Scala 中默认一切都是公开的。

现在定义一个带有构造函数的 Java 类，该构造函数将实例变量与访问器绑定。

```
public class Book {
private final int isbn;
private final String title;
public Book(int isbn, String title) {
this.isbn = isbn;
this.title = title;
}
public int getIsbn() {
return isbn;
}
public String getTitle() {
return title;
}
}
```

如你所见，构造函数将实例变量 `isbn` 和 `title` 与访问器 `getIsbn` 和 `getTitle` 绑定。让我们看看 Scala 中的等价写法。

```
class Book(val isbn: Int, val title: String)
```

在 Java 14 或更高版本中，记录（record）出现了，这大大减少了这段代码，使其与 Scala 中的代码相似。你唯一需要考虑的是，记录不能以任何方式被扩展。

```
public record Book (int isbn, String title) {}
```

正如你在上面的 Scala 代码中看到的，添加 `val` 会使构造函数参数绑定到同名字段。以下代码展示了带有调用父类构造函数的 Java 类 `NonFiction`：

```
public class NonFiction extends Book {
public NonFiction(int isbn, String title) {
super(isbn, title);
}
}
```

以下是 Scala 中的等价写法：

```
class NonFiction (isbn: Int, title: String) extends Book(isbn, title)
```

如你所见，主构造函数放在声明中。那么多个构造函数呢？你将在下一节中看到它们。让我们比较一下 Java 和 Scala 中的可变和不可变实例变量。这段代码展示了 Java 中的可变实例变量：

```
public class Book {
private String title = "Beginning Scala";
public String getTitle() {
return title;
}
public void setTitle(String t) {
title = t;
}
}
```

这段代码展示了 Scala 中的等价写法：

```
class Book:
var  title = "Beginning Scala"
```

如你所见，只需在类体中编写 `var` 就定义了一个可变字段。现在让我们看看 Java 和 Scala 中的不可变实例变量。这段代码展示了 Java 中的不可变实例变量：

```
public class Book {
private final int isbn = 999;
public int getIsbn() {
return isbn;
}
}
```

这段代码展示了 Scala 中的等价写法：

```
class Book:
val isbn = 999
```

你可能已经注意到，`isbn` 也是一个访问器方法的名称。

### 将 Java 导入翻译成 Scala 导入

两种语言中的导入是相似的；它们只改变了一些语法部分。Scala 引入了一个特性，允许你使用花括号仅导入一个包中的特定类，就像 Kotlin 等其他流行语言一样。

来自不同包的 Java 导入：

```
import com.modA.ClassA;
import com.modB.ClassB1;
import com.modB.ClassB2;
import com.modC.*;
```

Scala 中的等价写法：

```
import com.modA.ClassA;
import com.modB.{ClassB1, ClassB2} // 你可以将来自同一包的多个导入堆叠在花括号中。
import com.modC._ // Scala 导入中的下划线等同于 Java 导入中的星号。
```



### 翻译多个构造函数

现在，让我们看看如何在将 Java 类重构为 Scala 类时翻译多个构造函数。例如，以下面展示的 `Book` Java 类为例：

```
public class Book {
private Integer isbn;
private String title;
public Book(Integer isbn) {
this.isbn = isbn;
}
public Book(Integer isbn, String title) {
this.isbn = isbn;
this.title = title;
}
public Integer getIsbn() {
return isbn;
}
public void setIsbn(Integer isbn) {
this.isbn = isbn;
}
public String getTitle() {
return title;
}
public void setTitle(String title) {
this.title = title;
}
}
```

如你所见，`Book` Java 类有两个构造函数，一个以 `ISBN` 为参数，另一个以 `ISBN` 和 `title` 为参数。`Book` Java 类还有针对 `title` 和 `ISBN` 的 getter 和 setter 方法。现在，让我们将 `Book` Java 类重构为一个带有 `class` 参数的 Scala 类，并创建一个实例。

```
class Book ( var isbn: Int, var title: String)
```

你可以在 REPL 中尝试这段代码：

```
scala> class Book (var isbn: Int, var title: String)
defined class Book
scala> val book = new Book(999, "Beginning Scala") book: Book = Book@10ddb0e
```

然而，在 Scala 定义中，并不存在一个只接受单个参数 `title` 的构造函数。例如，如果你使用只接受单个 `title` 参数的构造函数来创建 `Book` 实例，你会在 REPL 中收到一个错误：

```
scala> new Book("Beginning Java")
1 |new Book("Beginning Java")
|         ^^^^^^^^^^^^^^^^
|         Found:    ("Beginning Java" : String)
|         Required: Int
```

为了完成对 Java 类的重构，你需要一个额外的构造函数。

```
class Book (var isbn: Int, var title: String):
def this(title: String) = this(0, title)
```

这段代码你能理解吗？你可能还记得，任何辅助构造函数都必须立即调用另一个 `this(...)` 构造函数，并调用主构造函数以确保所有参数都被初始化。你在第 3 章中学习了辅助构造函数。

现在你可以在 REPL 中尝试这段代码。

```
scala> class Book (var isbn: Int, var title: String):
|    def this(title: String) = this(0, title)
defined class Book
scala> val book1 = new Book(999, "Beginning Scala")
book1: Book = Book@132c5fd
scala> val book2 = new Book("Beginning Java")
book2: Book = Book@3e4e8a
```

这一次，你使用辅助构造函数创建了一个实例。

现在在 REPL 中访问 `title` 和 `ISBN`。

```
scala> book1.title
res38: String = Beginning Scala
scala> book2.title
res39: String = Beginning Java
scala> book1.isbn
res40: Int = 9999
```

你也可以像下面这样设置 `title`：

```
scala> book2.title = "Beginning Groovy"
book2.title: String = Beginning Groovy
scala> book2.title
res42: String = Beginning Groovy
```

所以你可以获取和设置 `title` 和 `ISBN`。但是，如果你将带有额外构造函数的代码与本节开头展示的 `Book` 类的 Java 代码进行比较，你会发现你并没有添加相应的 getter 和 setter 方法。但由于生成了遵循 Scala 约定的 getter 和 setter 方法，你仍然可以获取和设置 `ISBN` 和 `title`。当你使用 `scalac` 编译带有额外构造函数的代码，然后使用 `javap` 反编译它时，你会发现没有生成相应的 getter 或 setter 方法，唯一生成的 getter 和 setter 是遵循 Scala 约定的，如下所示：

```
$ scalac Book.scala
$ javap Book
```

以下是从 `Book.scala` 编译而来的代码：

```
public class Book {
public int isbn();
public void isbn_$eq(int);
public java.lang.String title();
public void title_$eq(java.lang.String);
public Book(int, java.lang.String);
public Book(java.lang.String);
}
```

因此，类定义生成了遵循 Scala 约定但不遵循 Java 约定的 getter 和 setter 方法。当你需要与只接受符合 JavaBean 规范的类的 Java 类或库进行交互时，生成遵循 Java 约定的 getter 和 setter 方法就变得很重要。你将在下一节中看到这一点。

## 符合 JavaBean 规范的 Scala 类

为了确保与 Java 框架的兼容性，你可能需要在类的字段上使用 Java 风格的 getter 和 setter 方法，以便与只接受符合 JavaBean 规范的类的 Java 类或库进行交互。要拥有 Java 风格的 getter 和 setter 方法，需要使用 `scala.beans.BeanProperty` 注解字段，如下面的代码行所示：

```
import scala.beans.BeanProperty
class Book(@BeanProperty var isbn:Int, @BeanProperty var title:String)
```

你可以在 REPL 中运行这段代码。

```
scala> import scala.beans.BeanProperty
import scala.beans.BeanProperty
scala> class Book(@BeanProperty var isbn:Int, @BeanProperty var title:String)
defined class Book
```

你可以通过编译 `Book` 类然后反编译它来了解 `@BeanProperty` 注解是如何工作的。首先，将这些内容保存到一个名为 `Book.scala` 的文件中，然后编译该类。

```
$ scalac Book.scala
```

编译完成后，使用 `javap` 命令进行反编译。

```
$ javap Book
```

这段代码展示了从 `Book.scala` 编译而来的 `Book` 类：

```
public class Book {
public Book(int, java.lang.String);
public int getIsbn();
public void setIsbn(int);
public java.lang.String getTitle();
public void setTitle(java.lang.String);
public int isbn();
public void isbn_$eq(int);
public java.lang.String title();
public void title_$eq(java.lang.String);
}
```

从反编译的代码中可以看出，由于 `@BeanProperty` 注解，方法 `getTitle`、`setTitle`、`getIsbn` 和 `setIsbn` 都已被生成。请注意，如果没有这些方法，你的类将不符合 JavaBean 规范。

让我们看看没有 `@BeanProperty` 注解时从 `Book.scala` 编译而来的代码。

```
public class Book {
public Book(int, java.lang.String);
public int isbn();
public void isbn_$eq(int);
public java.lang.String title();
public void title_$eq(java.lang.String);
}
```

注意

在你的字段上使用 `@BeanProperty` 注解，并确保将每个字段声明为 `var`。如果你将字段声明为 `val` 类型，则不会生成 setter 方法（`setTitle`、`setIsbn`）。

你已经了解了如何在类构造函数参数上使用 `@BeanProperty` 注解。同样地，你也可以在 Scala 类的字段上使用 `@BeanProperty` 注解。

接下来，你将学习使用 Scala 中 Java 不具备的特性，例如特质（traits）。



## Java 接口与 Scala 特质

Java 类无法继承包含已实现方法的 Scala 特质（有关特质的更多细节，请参见第 7 章）。为了理解这个问题，我们先来看一个常规的 Java 接口。

```
public interface Book {
public boolean isBestSeller();
}
```

其 Scala 等价形式如下：

```
trait Book:
def isBestSeller: Boolean
```

如你所见，`isBestSeller` 是一个抽象方法。在 Scala 中，方法使用 `def` 关键字声明。但 Scala 代码中的 `isBestSeller` 是一个抽象方法。如何在 Scala 中表示抽象方法？在 Scala 中，如果没有 `=` 赋值，那么使用 `def` 关键字声明的方法或使用 `val` 关键字声明的函数都是抽象的。例如，我们来看一个返回某个值的 Java 方法。

```
public String someMethod(int arg1, boolean arg2) {
return "voila";
}
```

其 Scala 等价形式如下所示。

```
def someMethod(arg1: Int, arg2: Boolean): String = "voila"
```

如你所见，`=` 表示方法的实现。

现在让我们来看一个抽象的 Java 方法。

```
abstract int doTheMath(int i);
```

以下是抽象 Java 方法 `doStuff` 的 Scala 等价形式：

```
def doTheMath(i: Int): Int
```

注意

如果没有使用 `=` 提供定义，那么该方法自动成为抽象方法。

现在让我们回到最初的问题。Java 类无法继承包含已实现方法的 Scala 特质。因此，如果你试图从 Java 中使用一个包含已实现方法的 Scala 特质，就会遇到问题。为了演示这个问题，首先创建一个包含名为 `add` 的简单已实现方法的特质。

```
trait Computation:
def add(a: Int, b: Int) = a + b
```

你已经编写了一个包含已实现方法的 Scala 特质，并且需要能够从 Java 应用程序中使用 `add` 方法，如下所示：

```
public class DoTheMath {
public static void main(String[] args) {
DoTheMath d = new DoTheMath();
// 在这里进行数学运算
}
}
```

`Computation` 类型不能成为 Java 类 `DoTheMath` 的超类，原因很简单：Java 中的超类必须是一个类；也就是说，Java 类 `DoTheMath` 不能使用 `extend` 关键字来继承 `Computation`。此外，Java 类 `DoTheMath` 也不能实现特质 `Computation`，因为在 Java 中你实现的是接口，而特质 `Computation` 包含已实现的行为，因此 `Computation` 不像一个常规的 Java 接口。为了能够从 Java 类 `DoTheMath` 中使用 Scala 特质 `Computation` 的已实现方法 `add`，你必须将特质 `Computation` 包装在一个 Scala 类中。以下代码展示了一个包装了特质 `Computation` 的 Scala 类：

```
class JavaInteroperableComputation implements Computation
```

现在，Java 类 `DoTheMath` 可以继承 Scala 类 `JavaInteroperableComputation` 并访问 `add` 方法。

```
public class DoTheMath extends JavaInteroperableComputation {
public static void main(String[] args) {
DoTheMath d = new DoTheMath();
d.add(3,1);
}
}
```

注意

为了 Java 调用者，请将包含已实现行为的 Scala 特质包装在 Scala 类中。

## Java 静态成员与 Scala 对象

Java 代码经常使用 `static` 关键字来实现单例对象，如下所示：

```
public class Book{
private static Book book;
private Book() {}
public static synchronized Book getInstance() {
if (book == null) {
book = new Book();
}
return book;
}
}
```

Scala 中没有 `static` 这种东西。在 Java 中，`static` 不属于对象，不能被继承，也不参与多态，因此 `static` 不是面向对象的。而 Scala 是纯面向对象的。Scala 不支持 `static`，但提供了对象的概念来代替类声明。如果你需要将上述 Java 代码重构为 Scala，只需使用对象声明代替类声明，如下所示：

```
object Book
```

Scala 对象为你提供了一个额外优势：它们也可以扩展接口和特质。Scala 提供了一种特殊语法，让你无需声明单例所需的所有语法（参见上面的 Java 代码）即可免费获得一个单例。但是，如果你想混合使用静态成员和实例成员呢？在 Java 中，你可以这样做：

```
public class Book {
public String getCategory() {
return "Non-Fiction";
}
public static Book createBook() {
return new Book();
}
}
```

除了对象的概念，Scala 还提供了伴生对象的概念，它由一个与同名的类共存于同一包和文件中的对象组成。有关伴生对象的更多信息，请参见第 3 章。伴生对象可以存储静态方法，并且通过它，你可以完全访问类的成员，包括私有成员。Scala 允许你声明同名的对象和类，将静态成员放在对象中，将实例成员放在类中。上面 Java 代码的 Scala 等价形式如下：

```
class Book:
def getCategory() = " Non-Fiction"
object Book:
def createBook() = Book()
```

## 处理异常

你可以定义如下所示的 Scala 方法，而无需声明它会抛出异常：

```
class SomeClass:
def aScalaMethod() =
throw new Exception("Exception")
```

然后可以从 Java 中调用此方法，如下所示：

```
public static void main(String[] args) {
SomeClass s = new SomeClass();
s.aScalaMethod();
}
```

然而，当 Java 开发者调用 `aScalaMethod` 时，未捕获的异常会导致 Java 方法失败：

```
[error] (run-main) java.lang.Exception: Exception!
java.lang.Exception: Exception!
at SomeClass.aScalaMethod
```

如果你没有使用 `@throws` 注解标记 `aScalaMethod` 方法，Java 开发者可以在其方法中不使用 `try/catch` 块，也不声明其方法会抛出异常的情况下调用它。

对于 Scala 方法的 Java 调用者，请为你的 Scala 方法添加 `@throws` 注解，这样他们就能知道哪些方法可能抛出异常以及会抛出什么异常。例如，以下代码展示了如何添加 `@throws` 注解，让调用者知道 `aScalaMethod` 方法可能会抛出异常。

```
class SomeClass:
@throws(classOf[Exception])
def aScalaMethod() =
throw new Exception("Exception")
```

你添加了注解的 Scala 方法的工作方式与抛出异常的 Java 方法完全相同。如果你尝试从 Java 类中调用 `aScalaMethod`，而没有将其包装在 `try/catch` 块中，或者没有声明你的 Java 方法会抛出异常，编译器（或你的 IDE）将会报错。

在你的 Java 代码中，像往常一样编写一个 `try/catch` 块来处理异常。

```
SomeClass = new SomeClass();
try {
s.aScalaMethod();
} catch (Exception e) {
System.err.println("捕获到异常。");
e.printStackTrace();
}
```



## Java Optional 与 Scala Option

与许多其他语言一样，Java 引入了一种机制来减少与空值相关的问题。这种机制的名称是 `Optional`。Scala 也有相同的概念，但名称为 `Option`。

Scala 提供了一个转换器，用于将 Java 中使用 `Optional` 的代码块的响应转换为 `Option`，因为正如你在本书前几章所读到的，两种语言中的类型并不完全相同（例如，在 Java 中，你可以使用 `Integer` 或 `int`，但在 Scala 中，你只能使用 `Int`）。

在 Java 中声明一个 `Optional` 值的方式与在 Scala 中类似。以下是 Java 代码：

```
import java.util.Optional;
public class JBook {
public Optional calculatePrice() {
return Optional.empty();
}
public Optional getName() {
return Optional.of("IT");
}
}
```

以下是 Scala 代码：

```
class SBook:
def calculatePrice(): Option[Integer] = None
def getName(): Option[String] = Option("IT")
```

如你所见，语法大致相同。下一步是在 Scala 中使用 `Optional` 值并将其转换为 `Option` 值，这是要使用的正确类型。

现在让我们看看如何在 Scala 中使用 `Optional` 并将其转换为 `Option`。

```
import java.util.Optional
import scala.jdk.javaapi.OptionConverters
val optionalPrice = Optional.of(1) //Optional(1)
val optionPrice = OptionConverters.toScala(optionalPrice) //Some(1)
val optionName = Some("Beginning Scala") //Some
val optionalName = OptionConverters.toJava(optionName)//Optional
```

如你所见，通过使用 Scala 中存在的特殊转换器，转换方式变得简单。逆向过程也大致相同。让我们修改这个示例，使其返回 `Option` 而不是 `Optional`。

```
import java.util.Optional;
import scala.jdk.javaapi.OptionConverters;
public class JBook {
public Option calculatePrice() {
return OptionConverters.toScala(Optional.empty());
}
public Option getName() {
return OptionConverters.toScala(Optional.of("IT"));
}
}
```

## 在 Scala 中使用 Java 集合

在第 6 章中，你了解了很多关于 Scala 集合以及如何提高其性能（相较于 Java 的同类集合）的内容，但这些原因并不会将你在 Scala 中使用 Java 集合（或反之）的可能性降低为零。

Scala 提供了一组类和方法，用于在不同语言的集合之间进行转换。以下代码展示了如何在 Scala 中创建一个 `Seq` 并将其转换为 Java 对象：

```
scala> import scala.jdk.javaapi.CollectionConverters
scala> val seq = Seq(1,2,3)
val seq: Seq[Int] = List(1, 2, 3)
scala> val list = CollectionConverters.asJava(seq)
val list: java.util.List[Int] = [1, 2, 3]
```

现在在 Scala 中以相反的方式操作。这段代码展示了如何从 Java 集合创建 Scala 集合。你创建了一个 `ArrayList` 并将其转换为 `Buffer`。

```
scala> import scala.jdk.javaapi.CollectionConverters
scala> import java.util.List
scala> import java.util.ArrayList
scala> val javaObject = java.util.ArrayList(java.util.List.of(1,2,3))
scala> val scalaObject = CollectionConverters.asScala(javaObject)
val scalaObject: scala.collection.mutable.Buffer[Int] = Buffer(1, 2, 3)
```

你可以在 Java 应用程序中使用相同的转换器来执行相同的操作，但首先，你需要了解 Java 和 Scala 集合之间的等价关系。表 9-1 展示了两种语言之间的对应关系。

表 9-1

不同集合之间的对应关系

| *Scala* | *Java* |
| --- | --- |
| `scala.collection.Iterable` | `java.lang.Iterable` |
| `scala.collection.Iterator` | `java.util.Iterator` |
| `scala.collection.mutable.Buffer` | `java.util.List` |
| `scala.collection.mutable.Set` | `java.util.Set` |
| `scala.collection.mutable.Map` | `java.util.Map` |
| `scala.collection.concurrent.Map` | `java.util.concurrent.ConcurrentMap` |

请注意，如果你想在 Java 项目中使用 Scala 代码，你需要包含一个依赖项，该依赖项提供所有使用它的方法和类。以下代码显示了要包含的正确依赖项（请检查标注的版本，并在你的代码中根据需要更新）。

```

org.scala-lang
scala3-library_3
3.0.2

```

## 总结

本章展示了 Scala 是如何被翻译成 Java 的，如果你从 Java 调用 Scala 代码，这一点尤其重要。你了解到，在大多数情况下，Scala 特性会被翻译成 Java 特性，以便 Scala 能够轻松地与 Java 集成。你学习了如何处理 Java 中有但 Scala 中没有的特性，例如静态成员和受检异常，以及如何在 Java 代码中使用 Scala 特性（如特质）。你还学习了 Scala 注解如何帮助集成，例如通过生成 JavaBean 风格的 `get` 和 `set` 方法。Scala 代码通常与大型 Java 程序和框架配合使用。

