# 10. 类与函数

在本章中，我们将探讨以下内容：

*   匿名函数或 lambda。
*   匿名类与匿名函数的区别。
*   一等函数和高阶函数。
*   函数与方法之间的区别。
*   lambda 与闭包之间的区别。

## 匿名函数

广义上的函数是为执行特定任务或计算而设计的代码片段。你可以将函数创建为 Java 方法或 Scala 的 `def`。两者本质上都是有名称的。而匿名函数则没有名称。

匿名函数也称为 lambda，在使用时可以被视为函数字面量。

Java lambda 语法以列出函数的参数开始，然后是箭头标记，接着是实现体。下面的例子是一个 lambda，它接受两个字符串参数，并比较它们的值以用于排序算法。

```
// java
(String a, String b) -> {
int comparison = a.compareTo(b);
return comparison;
};
```

如果编译器可以推断参数的类型，你可以省略类型；如果函数体是单行表达式，你可以省略 `return`。

```
(a, b) -> a.compareTo(b);         // 缩写形式
```

对于单参数 lambda，你甚至可以省略参数括号，但如果没有参数，则需要空括号。

```
value -> value * 2;               // 单参数
() -> 2 + 2;                      // 无参数
```

在 Scala 中，lambda 看起来非常相似。由于表达式中的最后一条语句被视为返回值，因此不需要 `return` 关键字。

```
// scala
(a: String, b: String) => {
val comparison = a.compareTo(b)
comparison
}
```

如果 Scala 可以推断参数类型，那么类型可以像 Java 一样省略，单行表达式也不需要花括号。

```
// scala
(a, b) => a.compareTo(b)          // 缩写形式
```

匿名类可以以类似于匿名函数的方式提供功能，但它们不能准确地被称为 lambda。

## 匿名类

匿名类与匿名函数不是一回事。匿名类仍然需要通过 `new` 关键字实例化为一个对象。它可能没有名称，但只有作为实例对象时才是有用的。

另一方面，匿名函数没有与之关联的实例。函数与其所操作的数据是分离的。

Java 中一个典型的匿名类可能如下所示：

```
// java
List list = Arrays.asList("A", "C", "B", "D");
list.sort(new Comparator() {
@Override
public int compare(String a, String b) {
return a.compareTo(b);
}
});
```

你仍然可以在 Scala 中创建匿名类，但通常不需要这样做。在 Scala 中，更常见的是创建匿名函数或 lambda。实际上，自从 Java 8 引入 lambda 以来，Java 也是如此。

```
// scala
val list = List("A", "C", "B", "D")
list.sorted(new Ordering[String] {
def compare(a: String, b: String): Int = a.compareTo(b)
})
```

与 Java 一样，要在 Scala 中创建匿名类实例，你需要使用带花括号的 `new` 关键字，并在主体内定义实现。注意，你不需要添加构造函数的括号（`new Ordering[String]()`）。

## 一等函数

正如我们所见，匿名函数被称为 lambda。它是一种定义函数的紧凑方式。当你想要定义计算或转换，并在之后将其应用于不同的值时，匿名函数非常有用。例如，将函数作为参数传递给其他函数，或存储函数定义以供以后使用。

这种用法被称为将函数视为一等公民。具体来说，如果一种语言支持以下特性，则它支持一等函数：

1.  支持将函数作为参数传递给其他函数。
2.  能够从其他函数中返回函数作为值。
3.  能够将函数存储在变量或数据结构中。

## 高阶函数

高阶函数是接受一个或多个函数作为参数，或者返回一个函数的函数。高阶函数往往比替代方案更灵活，也更容易重用。

Lambda 通过提供紧凑的语法以及编译器为 lambda 语法生成特殊字节码这一事实，促进了对一等函数的支持。¹

定义上述排序计算的 lambda 如下所示：

```
(String a, String b) -> a.compareTo(b);     // java
(a: String, b: String) => a.compareTo(b)    // scala
```

### 传入函数

为什么你想要将一个函数传入另一个函数？简而言之：为了灵活性。前面给出的 `sort` 或 `sorted` 函数就是一个很好的例子。你可以创建一个函数来对列表进行排序，但允许调用者影响排序的方式。

例如，你可以通过传入不同的函数字面量，按升序或降序对列表进行排序。

```
// java
List list = Arrays.asList("A", "C", "B", "D");
list.sort((a, b) -> a.compareTo(b));                     // 升序
list.sort((a, b) -> b.compareTo(a));                     // 降序
```

Scala 也是如此：

```
// scala
val list = List("A", "C", "B", "D")
list.sorted((a: String, b: String) => a.compareTo(b))    // 升序
list.sorted((a: String, b: String) => b.compareTo(a))    // 降序
```



### 返回函数

从函数中返回函数有助于将实现与行为解耦。你可能希望创建工厂风格的函数以便传递，或者进行计算但尚未获得所有值。

一个例子是，你想将美元金额转换为其他货币。编写一个简单的函数即可实现，该函数接收目标货币和美元金额。然而，假设你需要在许多地方调用这个函数，但不想到处传递目标货币。

如果你创建一个用于生成函数的函数，那么你只需调用一次（当你知道目标货币时），然后传递这个生成的函数即可。这省去了传递实现细节（本例中为目标货币）的麻烦，从而将调用方与细节解耦。

因此，与其采用如下命令式写法：

```
// scala
def dollarTo(currency: String, dollar: Double) = {
if (currency == "GBP") dollar * 0.76
else if (currency == "EUR") dollar * 0.83
else dollar
}
```

……不如创建一个高阶函数，它接收目标货币，但返回一个待后续求值的函数。该函数将接收一个货币金额，并返回转换后的金额，如下所示。

```
// scala
def dollarTo(currency: String): Double => Double = {
if (currency == "GBP") dollar => dollar * 0.76
else if (currency == "EUR") dollar => dollar * 0.83
else dollar => dollar
}
```

这样一来，你就不必在调用命令式版本的任何地方（例如，在下面的 `'calculateTicketPrice'` 函数中）将自己锁定在实现中：

```
// scala
def calculateTicketPrice(targetCurrency: String) = {
dollarTo(targetCurrency, 199.99)
// ...
}
```

……而是可以在任何需要转换的地方传入一个函数签名。

```
def calculateTicketPrice(currencyConversion: Double=> Double) = {
currencyConversion(199.99)
// ...
}
```

你可以将其视为一种非正式接口；任何将一个双精度浮点数转换为另一个双精度浮点数的函数都可以在此使用。除了使用上面的函数，你还可以传入一个调用网络服务获取最新汇率的函数，或者一个显示历史汇率的函数，而无需修改任何客户端代码。很巧妙。

```
calculateTicketPrice(dollarTo("GBP"))
calculateTicketPrice(yahooFxRateFor("GBP"))
calculateTicketPrice(historicalRateFor("GBP"))
```

### 存储函数

以下是一个将两个数相加的 lambda 表达式示例：

```
(Integer a, Integer b) -> a + b;        // java
```

如果你想将其作为值存储，则需要一个类型。在底层，Java 将 lambda 视为单方法接口（SAM）的实例，并在 `java.util.function` 包中提供了合适的接口来实现这一点。

它定义了 `java.util.function.Function` 来表示单参数函数，以及 `java.util.function.BiFunction` 来表示双参数函数。

因此，你可以像这样赋值 lambda 表达式：

```
// java
BiFunction plus = (Integer a, Integer b) -> a+b;
```

`BiFunction` 定义了两个输入参数 `T` 和 `U` 以及返回值 `R`。因此，上述代码表示一个接收两个整数参数并返回另一个整数的函数。

```
public interface BiFunction {
R apply(T t, U u);
}
```

由于变量声明包含了类型信息，Java 允许你在 lambda 参数中省略重复的类型：

```
// java
BiFunction plus = (a, b) -> a + b;
```

要执行或应用该函数，你可以使用以下语法：

```
// java
plus.apply(2, 2);                     // 4
plus.apply(plus.apply(2, 2), 2);      // 6
```

Scala 版本的基本 lambda 表达式正如你所料：

```
(a: Int, b: Int) => a + b             // scala
```

尽管 Scala 在底层也使用相同的技巧将函数表示为类型，在本例中为 `scala.Function2[Int, Int, Int]`，但它也允许更简洁的表示方式。因此，虽然你可以像这样冗长地书写：

```
val plus: Function2[Int, Int, Int] = (a: Int, b: Int) => a + b   // scala
```

……但使用函数类型 `(Int, Int) => Int` 更为简洁：

```
val plus: (Int, Int) => Int = (a: Int, b: Int) => a + b          // scala
```

同样，由于 `val` 上包含了额外的类型信息，你可以在 lambda 参数中省略重复的类型。

```
val plus: (Int, Int) => Int = (a, b) => a + b                    // scala
```

调用它更为简洁，因为 Scala 会自动调用 `apply` 方法。

```
// scala
plus(2, 2)              // 4
plus(plus(2, 2), 4)     // 8
```

## 函数类型

Java 和 Scala 都支持将函数作为一等公民，因为它们都支持上述所有要点。然而，一个学术上有趣的区别是，Java 不支持函数类型，而 Scala 支持。

尽管你可以像下面这样在 Scala 中使用 `Function2` 定义 lambda 的类型：

```
val f: Function2[String, String, Int] = (a, b) => a.compareTo(b)
```

……但你也可以使用函数类型来定义它。

```
val f: (String, String) => Int = (a, b) => a.compareTo(b)
```

赋值左侧的类型声明被称为函数类型。`(String, String) => Int` 是 Scala 中的一种完整类型，它声明了一个接收两个字符串参数并返回一个整数的函数。它可以用于任何普通类型声明可以使用的地方；用于描述值的类型（就像我们在这里所做的那样）、函数的参数或返回类型。

## 函数 vs. 方法

在 Scala 中，`def` 既可以作为函数使用，也可以作为方法使用。Scala 开发者经常谈论函数，而避免谈论方法，那么它们之间有什么区别呢？

方法 `def` 会在 Scala 类中定义，它通常引用类中的实例数据。其命名法严格属于面向对象的世界；方法为实例对象定义行为。

函数 `def` 在 Scala 单例对象中定义（但如果定义在类中，在某些条件下可以转换为函数）。函数严格属于数学世界，它们接收输入并产生输出。在函数式编程世界中，它们不为实例对象定义行为，而是实现独立的、可重复的转换或应用计算。

因此，函数和方法之间的区别部分在于描述它们使用时的上下文；当你构建面向对象系统时，你会谈论方法；而当你谈论转换或函数式编程时，你会谈论函数。方法还与实例对象相关联，而函数则不会。

在 Scala 中，有几种创建函数的方法。

1.  单例对象中的 `def` 本质上就是一个函数。
2.  创建一个 lambda 表达式。
3.  在类中创建一个 `def`，当它被传递给高阶函数时，Scala 会将其转换为函数。

在 Java 中，只有两种创建函数的方法。

1.  创建静态类方法。
2.  创建一个 lambda 表达式。

有一种特殊的 lambda 表达式称为闭包。在 Java 和 Scala 中，它们在底层的创建方式不同。它们更接近于匿名类而非函数，因为当它们被定义时会创建一个新实例。因此，你可以认为它们并非我在此描述的“函数”意义上的函数。

匿名类 != 匿名函数

当你创建一个匿名类时，你“新建”了一个实例。函数没有与之关联的实例，因此，匿名类的实例不能被称为匿名函数，即使它可以像一等公民一样被使用和传递。



## Lambda 表达式 vs. 闭包

闭包是一种特殊的 lambda 表达式。它是一个匿名函数，但在创建时会从环境中捕获某些内容。

每个闭包都是 lambda 表达式，但并非所有 lambda 表达式都是闭包！更令人困惑的是，匿名类在捕获某些数据时也可以被称为闭包。

让我们看一个例子。给定以下接口 `Condition`：

```
// java
interface Condition {
Boolean isSatisfied();
}
```

……一个匿名类可以实现 `Condition` 来检查某个服务器是否已关闭（它调用 `waitFor` 持续轮询，直到 `isRunning` 方法返回 `false`）。

```
// java
void anonymousClass() {
final Server server = new HttpServer();
waitFor(new Condition() {
@Override
public Boolean isSatisfied() {
return !server.isRunning();
}
});
}
```

功能等效的闭包看起来像这样：

```
// java
void closure() {
Server server = new HttpServer();
waitFor(() -> !server.isRunning());
}
```

为了完整性，`waitFor` 方法可能被简单地实现如下：

```
// java
class WaitFor {
static void waitFor(Condition condition) throws InterruptedException {
while (!condition.isSatisfied())
Thread.sleep(250);
}
}
```

两种实现都是闭包；前者是匿名类和闭包，后者是 lambda 表达式和闭包。正如我们所说，闭包在运行时捕获其“环境”。在匿名类版本中，这意味着将 `server` 的值复制到 `Condition` 的匿名实例中。在 lambda 表达式中，`server` 变量也需要在运行时复制到函数中。

由于是复制，它必须被声明为 final，以确保在捕获和使用之间不会被更改。这两个时间点的值可能非常不同，因为闭包通常用于将执行推迟到稍后的某个时间点。

Java 使用了一个巧妙的技巧：如果它能推断出一个变量从未被更新，那么它就可以被视为 final，因此它将其视为“实际上的 final”，你不需要显式使用 `final` 关键字来声明。

另一方面，lambda 表达式不需要复制其环境或捕获任何项。这意味着它可以被视为一个真正的函数，而不是一个类的实例。

同一个例子，如果表示为 lambda 表达式而不是闭包，看起来像这样：

```
// java
void lambda() {
Server httpServer = new HttpServer();
waitFor(server -> !server.isRunning(), httpServer);
}
void waitFor(ServerCondition condition, Server server) {
while (!condition.isSatisfied(server))
Thread.sleep(250);
}
interface ServerCondition {
Boolean isSatisfied(Server server);
}
```

这里的技巧是，lambda 表达式将 `httpServer` 实例作为参数，因此不需要在构造时捕获它。它不引用其作用域之外的任何内容，因此没有复制任何内容。同一个 lambda 实例可以用于不同的服务器实例。

这是一个重要的区别，因为 lambda 表达式不需要被多次实例化。闭包必须被实例化，并且为它关闭的每个值分配内存。对于非闭包的 lambda 表达式，内存只需分配一次，然后 lambda 表达式可以被重用。这使得它更高效，至少在理论上是这样。

速查表

如果一个 lambda 表达式没有参数，并且引用了其作用域之外的内容，那么它也是一个闭包。如果一个 lambda 表达式只对作为参数传入的内容进行操作，那么它就不是闭包。

```
(x) -> x * 2;     // lambda 表达式
() -> x * 2;      // 闭包
```

脚注 1

Lambda 表达式使用 `invokedynamic` 字节码调用。这比生成新类并像匿名类那样实例化它们更高效。

