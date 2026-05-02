# 1. Scala 简介

近年来，Scala 的受欢迎程度日益增长。有人将 Scala 语言描述为编程语言界的新“金童”。越来越多的大型公司开始采用 Scala 来满足其核心业务需求，从而提升了该语言的流行度，当然也扩大了其市场。

该语言的流行与其自身特性息息相关。Scala 从其他流行语言中借鉴了大部分语法。例如，其方法声明方式类似于 C 语言，但花括号是可选的。

乍一看，Scala 像是一种动态语言，例如 Ruby 或 Python，但它实际上是一种强静态类型语言，具备该类型的所有优势。

除此之外，Scala 还具备诸如 `for` 语句表达式、中缀/后缀表示法以及局部类型推断符号等特性。

该语言的特性使其非常适合用于构建领域特定语言（DSL）。Scala 将函数式编程与面向对象编程相结合。因此，在使用 Scala 创建 DSL 时，我们可以借助函数式语言来开发不可变函数。一方面，这有助于确保结果的一致性。另一方面，用于创建 DSL 的大多数模式都采用了面向对象范式，这意味着使用 Scala，我们也能拥有创建 DSL 所需的范式。

在本章中，我将简要介绍 Scala 语言，并重点介绍其主要特性。本书的其余部分将致力于提升读者对该语言的掌握程度，以便创建一些 DSL 项目。

我建议在本章的示例代码中使用 REPL（读取-求值-打印循环），因为它能对我们想要执行的操作提供即时反馈。

注意

自 Scala 2.11 起，退出 REPL 的语法已更改。如果你之前使用过 `exit()` 命令，你会发现该命令已失效。现在，用户必须使用 `sys.exit()` 命令才能退出。

## 基本语法

Scala 基本上是一种强类型语言，这意味着我们可以为以下内容标注类型：

*   变量和值
*   方法和函数参数
*   方法和函数返回类型

对许多程序员来说，这并不新鲜。像 Java 或 C# 这样的语言也使用类似的语法。了解如何使用语法的最佳方式就是“亲自动手”。因此，打开 REPL 并尝试一些基本的 Scala 命令。

注意

Scala 运行在 JVM 上，但与 Java 的一个主要区别在于 Scala 如何释放未使用的内存。当数据不再使用时，Scala 会自动释放内存。

要打开 REPL，请转到命令行并输入命令 `scala`。这将打开 Scala 解释器，如下所示：

```
$ scala
Welcome to Scala 2.12.2 (Java HotSpot(TM) 64-Bit Server VM, Java 1.8.0_131).
Type in expressions for evaluation. Or try :help.
scala>
```

乍一看，REPL 看起来像 Ruby 或 Python 这类解释型语言的解释器，但有一个很大的区别。Scala 是一种编译型语言，这意味着 REPL 会先编译代码再执行，然后呈现结果。

开始编写一些代码来看看 Scala 是如何工作的，例如：

```
scala> 2+2
res0: Int = 4
```

现在，我们可以看到 Scala 执行代码并创建一个临时变量来分配结果。但是，由于 Scala 是类型化的，它会定义变量的类型，在本例中是 `Int`。

在 Scala 中，运算符 `+`、`-` 等只是一个简单的函数。在这种情况下，编写 `2 + 2` 等同于拥有一个以左操作数为对象的方法。REPL 在学习 Scala 时非常有用，因为它能提供关于操作的即时反馈。

## Scala 中的变量和值

在 Scala 中，我们可以定义两种变量：

*   可变变量，使用保留字 `var` 创建
*   不可变值，使用保留字 `val` 创建

这可能是 Scala 与其他语言最大的区别。在 Scala 中，强烈建议使用不可变变量 `val`，因为它不会破坏函数式编程的规则。我将在本章后面讨论这个规则。

创建变量或值的语法是相同的：`<类型> <名称> : <类型> = <值>`。现在在 REPL 中尝试变量，并注意可变变量和不可变变量之间的区别。我们声明的第一种变量是可变变量，使用 `var` 声明。

```
scala> var num:Int = 4
num: Int = 4
scala> num = 8
num: Int = 8
```

在这里，如你所见，我们创建了一个变量并为其赋值。在这种情况下，我们定义了变量的类型。我们可以通过简单地调用变量并赋予新值来更改其值。这与其他语言的做法没有区别。

```
scala> num * 2
res0: Int = 8
scala> res0 + 2
res1: Int = 10
```

接下来的语句是完全有效的。如果我们没有给变量命名，Scala 会创建一个临时变量并将结果关联到这个变量。这样，我们可以轻松地使用临时变量来执行其他操作。现在来看看不可变变量是如何工作的。

```
scala> val new_num = 4
new_num: Int = 4
scala> new_num = 8
:12: error: reassignment to val
new_num = 8
```

在这个例子中，我们创建了一个变量 `new_num`，但没有显式指定类型。Scala 会检查并分配正确的类型。创建 `val` 的过程与创建 `var` 完全相同。唯一的区别是，如果我们尝试重新赋值，就会得到一个错误。对许多开发者来说，使用不可变变量而不是可变变量可能很奇怪，但如果你审视自己的代码，你会发现用不可变变量替换可变变量是多么简单。

## Scala 中的命名

Scala 允许你使用任何字母、数字或一些特殊运算符字符来命名变量。根据 Scala 语言规范中的以下内容，我们了解到如何定义这些运算符字符：

> ……\u0020-007F 范围内的所有其他字符以及 Unicode 类别 Sm [符号/数学]……除了括号 ([]) 和句点。

以下是 Scala 中命名标识符时组合字母、数字和字符的一些规则：

*   一个字母后跟零个或多个字母或数字，例如，`var a`、`var AabcdA`、`var a1b`
*   一个字母后跟零个或多个数字、下划线 (`_`) 或字母，例如，`var a_b` 或 `val a_ = 10`
*   一个下划线 (`_`) 后跟零个或多个字母或数字，例如，`var _abcd_`

### Scala 中的第一个示例

现在尝试用 Scala 编写一个简单的冒泡排序算法。

```
def bubbleSort(arr_input: Array[Int]): Array[Int] = {
val size = arr_input.size - 1
for (a <- 1 to size) {
for (b <- size to a by -1) {
if (arr_input(b) < arr_input(b - 1)) {
val x = arr_input(b)
arr_input(b) = arr_input(b - 1)
arr_input(b - 1) = x
}
}
}
arr_input
}
```

这个简单的例子展示了 Scala 的一些功能。首先，我们看到了如何使用不可变变量代替可变变量。从这个简单的例子开始，我们可以定义 Scala 的主要语法。



### 在 Scala 中定义方法与函数

Scala 中定义函数的语法与其他语言类似。其结构如下：

```
def 函数名 ([参数列表]) : [返回类型]
```

在前面的示例中，我们定义了 `bubbleSort` 方法：

`def` `bubbleSort` `(arr_input: Array[Int]): Array[Int]`

参数列表可以是 Scala 中的任何有效对象，也可以是另一个函数。Scala 的这一特性被称为**高阶函数**。

高阶函数是 Scala 这类函数式语言的一个特性。例如，假设我们要定义一个函数 `apply`，并将另一个函数作为参数传入。

```
def apply(internal: Int => String, value:Int) = internal(value)
```

函数 `apply` 将函数 `internal` 作为参数，并将函数 `internal` 应用于另一个参数 `value`。

高阶函数突显了 Scala 定义函数方式的一个有趣差异。在 Scala 中，函数是一个接受参数并返回值的表达式。

```
val addOne = (num:Int) => num +1
```

当然，我们也可以定义没有参数的函数。

```
val fixValue = () => 9
val theAnswer=() => 42
```

Scala 中函数与方法的主要区别在于，方法需要一个名称，而匿名函数通常用于函数式编程。匿名函数对 Java 开发者来说并非新概念。在 Java 中，定义接口时，我们通常会在匿名类中定义事件。

Scala 以不同的方式使用匿名类。在 Scala 中，可以定义所谓的**一等函数**。该函数接受另一个函数作为参数。例如，可以使用匿名函数来过滤列表。相关代码如下所示：

```
scala> val list = List.range(1, 20)
list: List[Int] = List(1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19)
scala> val evens = list.filter((num : Int) => num % 2 == 0)
evens: List[Int] = List(2, 4, 6, 8, 10, 12, 14, 16, 18)
```

在上述代码中，我们创建了一个匿名函数来生成变量 `evens` 的值：`list.filter((num : Int) => num % 2 == 0)`。这里可以看到，我们将一个匿名函数用作另一个函数的参数。

## Scala 中的类

在 Scala 中，使用以下语法定义类：

```
class 类名([参数列表]){ 类的主体 }
```

类是面向对象编程的核心。为了试验类，最好打开 REPL 并尝试创建一个简单的 Scala 类。

```
scala> class TheQuestion()
defined class TheQuestion
```

以下代码展示了 Scala 中的基本类。可以看出，类不一定需要有主体。当然，对于示例代码中的类来说，主体并非真正有用，但这个简单的类有助于我们理解类的某些功能。要创建类的实例，必须使用关键字 `new`。

```
scala> val question = new TheQuestion()
question: TheQuestion = TheQuestion@1726750
```

现在可以看到，创建了一个包含类名和一个十六进制数的类。这个数字是与该类关联的 JVM 值。这样的类虽然不太有用，但它确实拥有一个简单类的所有属性。在 REPL 中，开始输入变量名，然后按 Tab 键。在这种情况下，Scala 会显示该类可用的所有命令。

```
scala> question.
!=   ->             ensuring   formatted   isInstanceOf   notifyAll      wait
##         ==          eq             getClass       ne             synchronized   ?
+    asInstanceOf   equals     hashCode    notify         toString
```

这段代码创建了一个类实例，并将其与一个变量关联。现在尝试在 Scala 中创建一个简单的类。

```
class TheQuestion(){
def theAnswer():Int = {
return 42
}
}
```

上述代码展示了如何在 Scala 中创建一个简单的类。在类的主体内部，我们可以定义该类公开的方法。该方法定义了该类执行的操作。

要创建使用此方法的类实例，我们可以使用以下语法：

```
scala> val question= new TheQuestion()
question: TheQuestion = TheQuestion@e5d3e1
```

要使用该方法，我们可以像其他语言一样，简单地使用点号表示法。

```
scala> question.theAnswer
res1: Int = 42
```

## 单例对象

Scala 在创建单例对象的方式上与其他语言不同。创建单例对象的语法与我们创建类的语法类似，但将关键字 `class` 替换为 `object`。

```
object TheQuestion {
def main(args: Array[String]): Unit ={
val theAnswer:Int = 42
println(theAnswer)
}
}
```

可以看到，其定义与一个简单的类相似。在这个例子中，我们创建了一个 `main` 方法供该对象使用。由于这是一个单例对象，我们不需要使用关键字 `new` 来创建它。创建实例的语法如下：

```
scala> val answer = TheQuestion.main(null)

answer: Unit = ()
```

单例对象遵循与其他语言相同的规则。然而，在这种情况下，由于方法 `main` 的参数是一个数组，我们使用关键字 `null` 来创建一个空对象。

## Scala 中的类型

在 Scala 中，与其他语言一样，我们可以找到两种类型：数值类型，例如 `Int` 或 `Double`，以及非数值类型，例如 `char` 和 `string`。

Scala 与其他语言在类型方面的主要区别在于：在 Scala 中，没有原始类型。这意味着 Scala 中的任何类型都是一个对象。例如，当我们定义一个 `Int` 时，我们创建了一个 `Integer` 对象的实例。

### 转换数值类型

Scala 可以自动将一种数值类型转换为另一种。这种转换只能沿一个方向进行：从较短的类型转换为较长的类型。表 1-1 按从低到高的顺序列出了数值类型。数值类型使得例如将 `Byte` 转换为表中列出的任何其他类型成为可能。但请注意，`Double` 不能转换为任何其他类型。

表 1-1

Scala 中的数值数据类型

| 名称 | 定义 | 大小 |
| --- | --- | --- |
| `Byte` | 有符号整数 | 1 字节 |
| `Short` | 有符号整数 | 2 字节 |
| `Integer` | 有符号整数 | 4 字节 |
| `Long` | 有符号整数 | 8 字节 |
| `Float` | 有符号浮点数 | 4 字节 |
| `Double` | 有符号浮点数 | 8 字节 |



### Scala 中的字符串

Scala 中的字符串基于 JVM 中的同类型字符串。Scala 增加了一些独特的功能，例如多行字符串和字符串插值。

要在 Scala 中创建一个 `String`，我们可以使用双引号。在双引号内，我们可以编写字符串内容。

```
scala> val theGuide = "don't panic"
theGuide: String = don't panic
```

从上面的例子可以看出，创建一个字符串非常简单。你可以使用反斜杠（`\`）创建包含特殊字符的字符串。

```
scala> val theQuestion= "the Answer to the Ultimate Question of life, \nthe Universe,\nand Everything"
theQuestion: String =
the Answer to the Ultimate Question of life,
the Universe,
and Everything
```

在这个例子中，Scala 创建了一个包含换行符的字符串。这使得字符串跨越多行。也可以使用加号（`+`）来拼接字符串，如下所示：

```
scala> val sayHello = "Hello"+" reader"
sayHello: String = Hello reader
```

在这个例子中，我们可以看到 Scala 将字符串拼接成了一个单独的字符串。可以使用运算符 `==` 来比较字符串是否相等。Scala 与 Java 的不同之处在于，在 Scala 中，我们比较的不是对象的相等性，而是字符串值的相等性，如下所示：

```
scala> val string_compare_1 = "String 1"
string_compare_1: String = String 1
scala> val string_compare_2 = "String 1"
string_compare_2: String = String 1
scala> val compare = string_compare_1==string_compare_2
compare: Boolean = true
```

我们可以看到这两个字符串具有相同的值，在 Scala 中，它们就是相同的。如果我们尝试比较这两个字符串，会返回 `true`。

#### 多行字符串

要在 Scala 中创建多行字符串，我们在字符串开头使用三个双引号。

```
scala> val theFinalAnswer = """ Six by nine. Forty-two.
| That's it. That's all there is.
| I always thought something was fundamentally wrong with the universe """
theFinalAnswer: String =
" Six by nine. Forty-two.
That's it. That's all there is.
I always thought something was fundamentally wrong with the universe"
```

多行字符串遵循与其他字符串相同的规则。这意味着我们可以像使用普通字符串一样使用特殊字符和进行比较。

当我们需要在代码中添加特定格式的字符串时，多行字符串非常有用。例如，如果我们想在代码中包含一个 JSON 字符串，以便为输出准备返回值或仅仅用于测试，我们可以这样编写字符串：

```
scala> val jsonString: String =
|     """
|       |{
|       |"name":"PracticalScalaDSL",
|       |"author":"Pierluigi Riti",
|       |"publisher":"Apress"
|       |}
|       """
jsonString: String =
"
{
"name":"PracticalScalaDSL",
"author":"Pierluigi Riti",
"publisher":"Apress"
}
"
```

#### 字符串插值

在 Scala 中，使用加号（`+`）拼接字符串很容易，但还有一种更巧妙的方法：字符串插值。通过这种插值，Scala 会用字符串值替换变量。要使用变量进行字符串插值，需要使用美元符号（`$`）。

```
scala> val hello = "world"
hello: String = world
scala> println(s"Hello $hello")
Hello world
```

从上面的示例代码中，我们可以看到可以创建一个变量，然后在打印字符串时替换它。这种技术使我们能够保持代码清晰、紧凑，易于阅读，当然也易于维护。我们也可以在给另一个变量赋值时进行字符串插值。在这种情况下，我们必须使用语法 `${<变量名>}`。

```
scala> val sayHello= s"Hello ${hello}"
sayHello: String = Hello world
```

要使用插值，我们必须使用字符 `s`，它告诉 Scala 创建一个包含我们写入的变量的字符串，然后 Scala 会用该变量对字符串进行插值。

注意

从内存角度来看，字符串插值更好，因为字符串作为 JVM/Java 对象是不可变的。这意味着当我们使用加号（`+`）创建字符串时，即使只拼接两个字符串，也会创建三个字符串对象。这是因为，首先，Scala 为第一个字符串分配内存，然后为第二个字符串分配内存，最后，为第三个字符串（即前两个字符串的拼接结果）分配内存。而当我们使用插值时，我们只有两个字符串，因为第二个字符串是基于变量的替换而创建的。

## Scala 中的表达式

Scala 是一种函数式语言，因此，函数式编程是该语言发明者的主要意图。在此背景下，我想描述一下 Scala 中表达式的含义。

Scala 中的表达式是一行返回值的代码。这对于减少副作用非常重要，因为它们对操作总是产生相同的响应。函数式编程的范围是以数学方式定义函数。这意味着，如果每次传入相同的参数，每个函数必须始终返回相同的值。

这是因为函数本质上是不可变的，并且不受函数外部发生的变化的影响。这样做是为了减少或进一步消除副作用。副作用本质上是改变值的变量或表达式发生变化的结果。当外部变量发生变化时，这就违背了函数式编程本身。

我们可以在 Scala 中定义不同类型的表达式：

*   一个简单的字符串、整数或任何其他类型
*   一个变量
*   一个值
*   一个函数

表达式对于从面向对象编程过渡到函数式编程非常有用。表达式在日常编程中经常使用，但你可能没有意识到这一点。例如，语法

```
var variable:String = "value"
```

定义了一个表达式。当然，使用变量，我们可以使用相同的语法来定义一个值，如下所示：

```
val variable:String = "value"
```

在上面的例子中，通过一个函数创建了一个表达式，但假设，例如，你想定义一个计算正方形面积的函数。

```
val square=(x: Int) => x*x
```

上面的代码展示了如何使用函数来创建一个表达式。

### 条件表达式

条件表达式 `if..else` 是所有编程语言的核心结构。Scala 在 `if..else` 条件表达式上使用了与其他语言相同的逻辑基础。

`if..else` 的语法是

```
if() 
```

如果布尔条件为 `True`，则执行 `表达式`。

```
scala> if (10 % 3 > 0) println("Not a multiple")
Not a multiple
```

在这个例子中，我们使用一个表达式来检查布尔值。如果 `10` 不能被 3 整除，它会打印 `"Not a Multiple"`，所以我们可以看到 Scala 中 `if..else` 的使用与其他语言相同。

如果我们想定义一个 `else` 条件，可以使用以下语法：

```
if(布尔条件) 表达式 else 表达式
```

现在我们可以编写一个更复杂的 `if..else` 条件：

```
scala> if (10 % 2 > 0) println("Not a multiple") else ("Multiple")
res2: Any = Multiple
```

### 模式匹配表达式

Scala 没有像 Java 或 C# 那样的 `switch` 命令。在 Scala 中，我们有 `match` 命令，它比 `switch` 更灵活。

模式匹配的语法是：

```
match{
Case  => 
}
```

模式匹配可以是一个值或一个正则表达式；例如，一个简单的模式匹配可以是：

```
scala> val number1 = 10
number1: Int = 10
scala> val number2 = 20
number2: Int = 20
scala> val max = number1 > number2 match{
| case true => number1
| case false => number2
| }
max: Int = 20
```

这里我们可以看到我们创建了两个值，并且该表达式本质上是对这两个值的简单检查。当我们识别出较大的值时，我们输出该数字。



#### 多重条件

你可以在模式匹配中使用更多条件。例如，可以检查一年中的月份以对应季节。

```
val month = "JAN"
val season = month match{
case "DEC" | "JAN" | "FEB" =>
"Winter"
case "MAR" | "APR" | "MAY" =>
"Spring"
case "JUN" | "JUL" | "AUG" =>
"Summer"
case "SEP" | "OCT" | "NOV" =>
"Autumn"
}
```

在这个例子中，我们使用“或”来匹配字符串，可以看到模式匹配与 `switch` 结构类似，但功能更强大。我的建议是尝试这种模式，以更好地了解模式匹配的用途。当然，我们也可以使用通配符来处理未实际出现的情况。

```
val month = "JAN"
val season = month match{
case "DEC" | "JAN" | "FEB" =>
"Winter"
case "MAR" | "APR" | "MAY" =>
"Spring"
case "JUN" | "JUL" | "AUG" =>
"Summer"
case "SEP" | "OCT" | "NOV" =>
"Autumn"
case _ =>
"Not a month"
}
```

在这种情况下，代码会显示结果 `"Not a month"`。当然，我们也可以指定模式匹配中要使用的变量类型。这里，我们需要在 case 后面定义变量的类型。一个例子是，如果我们想根据特定类型检查一个值。

```
Val theAnswer:Int = 42
val anyAnswer:Any = theAnswer
anyAnswer match {
case theAnswer:String => "String Value"
case theAnswer:Int => "Integer Value"
case theAnswer:Double => "Double Value"
}
```

通过这个简单的例子，我们创建了一个变量，然后将其赋值给一个 `Any` 类型的变量。这种变量是数值类型中的最高层级，这意味着它可以与任何其他类型的变量关联。在示例中，我们尝试通过模式匹配来识别与 `Any` 类型变量关联的是哪种类型的值。

#### 模式守卫

模式守卫是一个 `if` 表达式，用于检查执行模式匹配时的布尔条件。例如，当我们想检查一个字符串或对象是否不为 `null` 时，这非常有用。

```
val empyString:String = null
val empStr = empyString match{
case sea if sea!= null =>
println(s"Received '$sea'")
case sea =>
println(s"Received a null value")
}
```

### 范围与循环

Scala 中的 `for` 循环会执行一段代码块若干次。例如，当我们想遍历数组的所有元素时，可以使用循环。

```
val myArray:Array[String] = new ArrayString
for (i <- 0 until myArray.length){
print(s"element of the array $myArray(i)")
}
```

在这个例子中，我们创建了一个包含空元素字符串的数组。代码将所有元素初始化为默认值，对于字符串来说，默认值是 `null`。然后我们使用数组的长度来确定迭代次数。在 Scala 中，可以使用 `Range` 对象来创建元素的最大数量。

要在 Scala 中创建一个 `Range` 对象，我们使用以下语法：

```
to or until  
```

增量是可选的，但如果不指定，则默认为 `1`。现在想象一下，我们想创建一个 `for` 循环来显示一些数字，如下所示：

```
scala> for (number <- 1 to 12) { println(s"Month $number") }
Month 1
Month 2
Month 3
Month 4
Month 5
Month 6
Month 7
Month 8
Month 9
Month 10
Month 11
Month 12
```

在这个例子中，我们从 1 数到 12，并输出一个包含月份和数字的字符串。我们可以使用 `by` 关键字来指定不同的增量。

```
scala> for (number <- 1 to 12 by 2) { println(s"Month $number") }
Month 1
Month 3
Month 5
Month 7
Month 9
Month 11
```

在这个例子中，我们看到数字每次增加 2，这减少了 range 对象生成的元素数量。在 Scala 中，可以轻松地从 `List` 或 `Array` 创建范围。通过使用 `toList` 或 `toArray` 方法，Scala 会将 range 对象直接转换为 `List` 或 `Array`。在 Scala 中，更推荐使用 `List` 而不是数组，因为 `List` 是一个不可变结构。这意味着当我们创建一个 `List` 时，无法修改它。`List` 不允许副作用，这在函数式编程中避免副作用非常重要。

```
scala> val day_of_week = (1 to 7).toArray
day_of_week: Array[Int] = Array(1, 2, 3, 4, 5, 6, 7)
scala> val day_of_week = (1 to 7).toList
day_of_week: List[Int] = List(1, 2, 3, 4, 5, 6, 7)
```

注意

可以省略括号，但那样的话，你必须使用 `postfixOps`。更快捷的方法是导入 `postfixOps` 类，如下所示：

```
scala> import scala.language.postfixOps
import scala.language.postfixOps
scala> val day_of_week = 1 to 7 toList
day_of_week: List[Int] = List(1, 2, 3, 4, 5, 6, 7)
```

可以使用 `yield` 关键字来创建一个 `IndexSeq` 对象。该对象会将结果值转换为 `Vector`，如下所示：

```
scala> val day_of_week = for (x <- 1 to 7) yield {s"$x"}
day_of_week: scala.collection.immutable.IndexedSeq[String] = Vector(1, 2, 3, 4,5, 6, 7)
```

可以使用迭代器守卫来仅在特定条件下执行增量操作。迭代器守卫是一个 `if` 条件，用于验证是否先执行增量。

```
scala> val even = for(i <- 1 to 20 if i%2 == 0) yield i
odds: scala.collection.immutable.IndexedSeq[Int] = Vector(2, 4, 6, 8, 10, 12, 14, 16, 18, 20)
```

### 其他循环

在 Scala 中，不仅可以使用 `for` 语法创建循环，还可以使用 `while` 和 `do..while` 循环。这些循环会重复执行一条语句，直到布尔值为 `false`。

```
var count = 10
while(count > 0){
println(count)
count -=1
}

```

`while` 语法非常简单：循环结束后检查执行条件。例如，当变量 count 的值为 `0` 时，代码会执行。只有当变量为 -1 时，循环才会停止。

`do..while` 循环与 `while` 循环类似。区别在于，变量在循环执行之前被检查。

```
scala> var count = 10
count: Int = 10
scala> do{
|   println(count)
|   count -=1
| }while(count >0 )

```

我们可以看到结果完全相同。唯一的区别是，条件表达式首先被检查，以调用新的循环。这节省了一次计算执行，因为 `do..while` 循环在语句之后检查值。在这种情况下，当变量达到 `0` 时，过程停止。

## 数据结构

到目前为止，我只介绍了语言的基本语法，但一个真正的程序必须将数据存储在数据结构中以便操作。现在我将讨论在 Scala 中可以使用的不同数据结构。

在 Scala 中有六种基本数据结构，如下所示：

*   Array
*   List
*   Set
*   Tuple
*   Map

在接下来的章节中，我将尝试指出这些数据结构之间的主要区别。



### 数组

数组是一个可变集合，能够保持元素插入时的顺序。数组中的数据必须属于同一类型。数据不会自动排序，这意味着如果我们创建一个值为 (2,1,3) 的数组，其中的元素将保持该顺序。数组也可以包含重复的值。

```
scala> val myArray:Array[String] = new ArrayString
myArray: Array[String] = Array(null, null, null, null, null, null, null, null, null, null)
```

在上述代码中，定义了一个包含十个字符串元素的空数组。由于我们没有指定具体值，Scala 会用字符串的默认值填充数组，在本例中，该默认值为 `null`。

我们可以通过数组的索引来访问数组，以读取或修改其中的值。例如，假设我们想要修改数组第一个元素的值，代码如下所示：

```
scala> myArray(0) = "test"
scala> myArray
res7: Array[String] = Array(test, null, null, null, null, null, null, null, null, null)
```

我们也可以直接通过定义数组元素的方式来创建数组。

```
scala> val myArray_new = Array(1,2,3,4,5,6,5)
myArray_new: Array[Int] = Array(1, 2, 3, 4, 5, 6, 5)
```

### 列表

列表是一个不可变集合。列表会保持元素插入时的顺序，并且与数组类似，只存储同一类型的数据，不过它可以包含重复的值。

```
scala> val myList = List("a","b","c","b")
myList: List[String] = List(a, b, c, b)
```

列表是一个不可变集合，这意味着无法更新其中的值。事实上，也无法创建一个带有默认值的列表。如果我们尝试更新列表中的某个值，Scala 会返回一个异常。

```
scala> myList(3) = 10
:13: error: value update is not a member of List[String]
myList(3) = 10
```

### 集合

集合是一个无序的不可变序列。集合不保持元素的插入顺序，并且无法向集合中添加重复元素。如果我们添加了重复元素，集合会自动删除这些重复项。

```
scala> val numbers = Set(1,2,3,4,4,5,3,3,2,1)
numbers: scala.collection.immutable.Set[Int] = Set(5, 1, 2, 3, 4)
```

### 元组

元组是一个简单的逻辑数据集合。我们可以在其中存储数据，例如用户数据，而无需定义类。

```
scala> val servers=("localhost", 80)
servers: (String, Int) = (localhost,80)
```

可以通过位置索引来访问元组中的元素。元组是一个基于 1 的容器。

```
scala> servers._1
res0: String = localhost
```

元组可以与模式匹配结合使用，作为 `case` 的条件。如果我们需要创建一个仅包含两个元素的元组，可以使用语法糖 `->`，如下所示：

```
scala> 1 -> 2
res1: (Int, Int) = (1,2)
```

以下展示了如何在元组中添加两个以上的元素：

```
scala> val users=("Pierluigi","Riti",15,9,1975)
users: (String, String, Int, Int, Int) = (Pierluigi,Riti,15,9,1975)
```

### 映射

映射是一个可迭代的、不可变的键/值对集合。创建映射时，我们使用与创建元组相同的语法。每个元组都是一个键/值对：

```
scala> Map("one" -> 1, "two" -> 2)
res1: scala.collection.immutable.Map[String,Int] = Map(one -> 1, two -> 2)
```

也可以将一个映射与另一个键关联起来。

```
scala> Map("one" ->Map("one" ->1))
res2: scala.collection.immutable.Map[String,scala.collection.immutable.Map[String,Int]] = Map(one -> Map(one -> 1))
```

## 总结

在本章中，我介绍了 Scala 的基本语法。我解释了 Scala 的基础语法，并未深入探讨，只是为了让读者了解如何使用 Scala。在下一章中，我们将更深入地探讨这些基本概念，并且我会介绍与 DSL 设计和实现相关的其他 Scala 概念。正如我所建议的，学习 Scala 并释放这门语言潜力的最佳方式就是亲自动手使用 REPL。

