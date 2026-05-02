# 3. 一些基本语法

## 定义值和变量

让我们来看一些语法。我们将从创建一个变量开始：

```
val language: String = "Scala"
```

我们定义了一个 `String` 类型的变量，并为其赋值为“Scala”。我说“变量”，但实际上我们创建的是一个不可变的值，而不是变量。`val` 关键字创建了一个常量，从此以后 `language` 就不能被修改了。不可变性是你在 Scala 中会反复遇到的一个关键主题。

如果我们以后想要修改 `language`，可以使用 `var` 而不是 `val`。这样，如果需要，我们可以重新赋值。

```
var language: String = "Java"
language = "Scala"
```

到目前为止，这与 Java 看起来没有太大区别。在变量定义中，类型和变量名的顺序与 Java 相反，但仅此而已。Scala 大量使用类型推断，因此即使我们不明确指定，Scala 也能推断出上面的 `var` 是一个 `String`。

```
val language = "Scala"
```

注意，行尾不需要分号。Scala 编译器通常能够判断表达式何时结束，无需显式告知。只有当你在同一行使用多个表达式时，才需要添加分号。

运算符优先级与你预期的 Java 中一样。在下面的例子中，乘法在减法之前执行。

```
scala> val age = 35
scala> var maxHeartRate = 210 - age * .5
res0: Double = 192.5
```


好的，作为一名高级文档工程师和翻译员，我将严格遵循您提供的注意事项和示例格式，将给定的英文文本翻译成中文。


## 定义函数

函数和方法的定义以 `def` 关键字开头，后跟签名。该签名看起来类似于 Java 方法签名，但参数类型顺序相反，并且返回类型位于末尾而不是开头。

让我们创建一个函数来返回两个数字的最小值。

```
def min(x: Int, y: Int): Int = {
if (x < y)
return x
else
return y
}
```

我们可以通过在 REPL 中调用来测试它。

```
scala> min(34, 3)
res3: Int = 3
scala> min(10, 50)
res4: Int = 10
```

请注意，Scala 无法推断函数参数的类型。

另一个技巧是你可以省略 `return` 语句。函数中的最后一个语句将隐式地成为返回值。

```
def min(x: Int, y: Int): Int = {
if (x < y)
x
else
y
}
```

在 REPL 中运行它会显示以下内容：

```
scala> min(300, 43)
res5: Int = 43
```

在这个例子中，`else` 确保了最后一个语句与 `min` 函数一致。如果我忘记了 `else`，那么无论条件如何，最后一个语句都是相同的，这会在我们的实现中引入一个 bug：

```
def min(x: Int, y: Int): Int = {
if (x < y)
x
y         // bug!  else 语句在哪？
}
```

它总是返回 `y`：

```
scala> min(10, 230)
res6: Int = 230
```

如果你不使用任何 `return` 语句，返回类型通常可以被推断出来。

```
// 返回类型可以省略
def min(x: Int, y: Int) = {
if (x < y)
x
else
y
}
```

请注意，是等号表明此函数有返回值。如果我将此函数写在一行，省略返回类型，只保留等号，它看起来更像是一个真正的表达式，而不是一个函数。

```
def min(x: Int, y: Int) = if (x < y) x else y
```

不过要小心；如果你不小心省略了等号，该函数将不会返回任何内容。它将类似于 Java 中的 `void`。

```
def min(x: Int, y: Int) {
if (x < y) x else y
}
```

```
<console>:8: warning: a pure expression does nothing in statement position;
you may be omitting necessary parentheses
if (x < y) x else y
^
min: (x: Int, y: Int)Unit
```

尽管这可以编译通过，但编译器会警告你可能遗漏了等号。

## 运算符重载与中缀表示法

在 Scala 中需要注意的一个有趣的事情是，你可以重写运算符。实际上，算术运算符在 Scala 中只是方法。因此，你可以创建自己的运算符。之前，我们看到了整数 `age` 与乘数一起使用。

```
val age: Int
age * .5
```

值 `age` 是一个整数，并且在整数类上有一个名为 `*` 的方法。它具有以下签名：

```
def *(x: Double): Double
```

在 Scala 中，数字是对象，字面量也是对象。因此，你可以直接在变量或数字上调用 `*`。

```
age.*(.5)
5.*(10)
```

使用中缀表示法，你可以省略变量和字面量的点号表示法，并直接调用。

```
age * .5
```

……或者，另一个例子：

```
35 toString
```

记住，`35` 是 `Int` 的一个实例。

具体来说，Scala 对中缀表示法的支持意味着，当一个方法接受零个或一个参数时，你可以省略点和括号；如果参数多于一个，你可以省略点。

例如：

```
35 + 10
"aBCDEFG" replace("a", "A")
```

不过这是可选的；如果你愿意，也可以使用点号表示法。

这意味着你可以在自己的类上定义自己的加号或减号方法，并使用中缀表示法自然地使用它们。例如，你可能有一个 `Passenger` 加入一个 `Train`。

```
train + passenger
```

对于你可以如何命名函数和方法，没有太多限制；你可以使用任何对你的领域有意义的符号。

## 集合

Scala 提供了不可变集合类型（如 `Set`、`List` 和 `Map`），以及具有相同 API 的可变版本。Scala 的创建者（以及一般的函数式编程社区）倾向于不可变性，因此当你在 Scala 中创建集合时，默认选择是不可变版本。

因此，我们可以使用以下方式创建一个列表：

```
val list = List("a", "b", "c")
```

并使用以下方式创建一个映射：

```
val map = Map(1 -> "a", 2 -> "b")
```

……其中箭头从键指向值。这些将是不可变的；你将无法添加或删除元素。

为什么偏爱不可变性？

通常，讨论不可变性是为了使并发系统更容易推理。这是因为如果共享数据可以被多个线程同时更新，那么推理和诊断意外行为（如竞态条件）就会变得困难。完全移除更新共享状态的能力可以缓解这个问题；不可变对象本质上是线程安全的。

但更普遍地说，即使在单线程系统中，不可变性也能使代码更容易推理。如果你从不可变状态开始，你的系统设计会受到很大影响，并且你消除了细微 bug 悄悄潜入的机会。

你可以用类似于 Java 的 `forEach` 和 lambda 语法的方式来处理它们。

```
list.foreach(value => println(value))                  // scala
```

这等同于 Java 中的以下代码：

```
list.forEach(value -> System.out.println(value));      // java
```

类似于 Java 的方法引用语法，你可以将 lambda 参数自动连接到方法调用。

```
list.foreach(println)                                  // scala
```

这大致等同于以下 Java 代码：

```
list.forEach(System.out::println);                     // java
```

还有很多其他 Scala 风格的处理集合的方式。我们稍后会介绍这些，但最常见的迭代方式是像这样编写的 `for` 循环：

```
for (value <- list) println(value)
```

这读作“对于列表中的每个值，打印该值”。你也可以反向操作：

```
for (value <- list.reverse) println(value)
```

或者你可能希望将其分成多行：

```
for (value <- list) {
println(value)
}
```

## 元组

元组是一个有序的元素集合。这个名称源于我们日常用来描述事物倍数的语言：单数、一对、三个、四个、n 元组，等等。例如，我们熟悉的四元组是一个包含四个元素的集合。一个包含 n 个元素的元组就是一个 n 元组。1 元组称为单数，2 元组称为一对，3 元组称为三个，依此类推。更通俗地说，人们将 n 元组简称为元组。它的发音是 /tuːpəl/，而不是“tupple”。

在编程中，元组有助于以轻量级的方式捕获相关值，而无需使用完整的对象。要捕获一个 `String` 值及其相关的 `Int`，你可以编写以下代码：

```
val example = ("load", 21)
```

在这种情况下，元组的底层类型是 `Tuple2`，它捕获了 `String` 和 `Int` 的底层类型。而

```
val tuple = ("save", 50, true)
```

……将产生一个 `Tuple3`，按顺序捕获 `String`、`Int` 和 `Boolean` 类型。

对值的访问是通过以下划线为前缀的编号方法进行的。

```
val event = tuple._1      // "save"
val millis = tuple._2     // 50
val success = tuple._3    // true
```

……或者你可以像这样直接将值分配给变量：

```
val (event, millis, success) = tuple
```

由于元组是一种类型，因此在定义值和函数参数时，你可以像这样引用它。

```
// 指定 val 的类型
val save: (String, Int, Boolean) = ("save", 50, true)
// 一个以元组作为类型参数的函数
def audit(event: (String, Int, Boolean)) = {
...
}
```



## Java 互操作性

我之前提到过，你可以在 Scala 中使用任何 Java 类。例如，假设我们想创建一个 Java 的 `List`，而不是通常使用的 Scala 不可变 `List`。

```
val list = new java.util.ArrayList[String]
```

我们只是使用了类的全限定名（`java.util.ArrayList`）并用 `new` 来实例化它。注意到方括号了吗？Scala 使用 `[]` 而不是 `<>` 来表示泛型。我们也不必在构造函数上使用括号，因为没有参数需要传入。

我们可以像预期的那样调用方法——例如，添加一个元素：

```
list.add("Hello")
```

……或者，使用中缀表示法：

```
list add "World!"
```

## 原始类型

在 Java 中有两种整数类型：原始（非对象）类型 `int` 和 `Integer` 类。Scala 没有原始类型的概念——一切都是对象——因此，例如，Scala 的整数类型是 `Int`。类似地，你也会熟悉其他基本类型。

```
Byte
Short
Int
Long
Char
String
Float
Double
Boolean
```

尽管 Scala 有自己更丰富的类型，但它们通常只是对 Java 类型的包装。在使用这些基本类型时，十有八九你不需要担心自己使用的是 Scala 类型还是 Java 类型。事情非常无缝。例如，Scala 有一个带有 `+` 方法的 `BigDecimal` 类型，这意味着你可以用比 Java 少得多的代码来相加两个大十进制数。

比较以下 Scala 和 Java 代码：

```
// scala
val total = BigDecimal(10000) + BigDecimal(200)
// java
BigDecimal total = new BigDecimal(10000).add(new BigDecimal(200));
```

Scala 并没有重新实现 Java 的 `BigDecimal`；它只是委托给 Java，并省去了你输入所有那些样板代码的麻烦。

