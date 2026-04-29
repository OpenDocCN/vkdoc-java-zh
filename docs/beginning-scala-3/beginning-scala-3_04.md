# 4. 函数式编程

在纪实作品《密西西比河上的旧时光》中，马克·吐温写道：“我 14 岁时，觉得父亲无知得令人难以忍受，简直不想让这个老家伙在身边。但当我 21 岁时，我惊讶地发现，这个老家伙在七年里学到了多少东西。”函数式编程就像是这位老父亲，在编写健壮的并发软件时前来救场。函数式编程将计算视为数学函数的求值，并避免状态和可变数据。它是一种声明式编程范式，其中编程是通过表达式完成的。命令式编程风格强调一系列操作，其特点是使用循环进行迭代、就地修改数据以及带有副作用的方法，其中副作用的顺序对于产生正确效果至关重要。命令式语言（如 Java）中的基本构造是改变程序状态的命令式语句，如下所示：

```
x = x + 1
```

函数式编程风格强调结果，其特点是将函数值传递给循环方法、不可变数据以及无副作用的方法，其中操作发生的顺序无关紧要。在像 Scala 这样的函数式语言中，基本构造是声明式的，如下所示，并且没有副作用：

```
f(int x){return x + 1}
```

在函数式语言中，计算主要通过求值表达式进行。作为一种支持函数式编程的语言，Scala 鼓励面向表达式的编程（EOP）模型。

Scala 的创造者 Martin Odersky 说过，该语言的本质是在类型化环境中融合函数式编程和面向对象编程：用函数处理逻辑，用对象处理模块化。因此，你需要理解这两种范式才能充分利用这种语言的全部能力。



## 什么是函数式编程？

当人们初次接触*函数式编程*这个术语时，可能会认为它是最近才出现的新事物，但事实上，一些语言在过去就已经实现了这一理念。如今，这些语言大多已不再流行。

早期函数式语言的一个例子是 Lisp，它诞生于 1958 年。另一个例子是 Haskell，出现于 1990 年。这些语言在学术领域非常流行，但大多数开发者在职业生涯中并未使用它们。这并不意味着这种范式不好或难以理解。有时，某些语言或范式会在不同的时代找到新的重要性。想象一下，如果有人在 20 世纪 90 年代要求你使用 JavaScript 构建整个应用的后端，你可能会觉得那个人疯了，因为当时的 JavaScript 仅用于在 HTML 中执行某些验证或特效。而今天，JavaScript 已被广泛应用于多个平台，借助 NodeJs^(²⁵) 和其他技术来创建微服务。

为了回答本节标题提出的问题，关于这种范式有许多定义，但其中一种定义以简单的方式概括了整个理念：

> *函数式代码的特点只有一个：没有副作用。它不依赖当前函数外部的数据，也不会改变当前函数外部的数据。所有其他“函数式”的特性都可以从这个属性推导出来。请将其作为你学习过程中的指引。*
> 
> 玛丽·罗斯·库克

另一位撰写过多本函数式编程书籍的作者说道：

> *函数式编程是一种仅使用纯函数和不可变值来编写软件应用程序的方式。*
> 
> 阿尔文·亚历山大

这两个定义有共同之处。它们都提到了不可变性的重要性，因为它消除了协调并发或共享访问某些变量所带来的所有问题。当然，你可以使用关键字 `var` 创建变量，但这种范式的本质或精神是在函数内部使用不可变值。当你向函数传递参数时，你不能改变其内部的值，这遵循了不可变性的原则。相反，你需要创建一个新变量并返回该值。

为了通过示例理解不可变性，让我们创建一个函数，用于移除字符串中的前缀。首先，尝试将操作的结果重新赋值给同一个变量，看看会发生什么。

```
scala> def removePreffix(name : String) =
|     name = name.replace('T', ' ')
|
2 |    name = name.replace('T', ' ')
|    ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
|    Reassignment to val name
```

遵循不可变性原则的正确方式如下：

```
scala> def removePreffix(name : String) =
|     name.replace('T', ' ')
def removePreffix(name: String): String
```

这意味着该函数接收一个字符串，替换前缀，并返回一个新值，而不改变原始参数的值。

### 纯函数

在数学中，函数都是纯的，因为它们没有副作用。考虑经典的函数 `(x)`：

```
y = sin(x)
```

无论 `sin(x)` 被调用多少次，`sin(x)` 都不会修改变量的状态。这样的函数被称为纯函数，并且与上下文无关。

有时，你的函数不仅仅执行数学运算，因此以下是创建纯函数的规则：

*   它们是不修改参数值的函数；相反，它们会返回一个包含结果的新值。
*   它们是不修改应用程序上下文的函数。
*   它们仅依赖于函数接收的参数。它们不会访问外部资源，例如数据库、文件或通过网络访问的其他应用程序。

### 副作用

根据纯函数的定义，副作用指的是函数执行不会影响应用程序的其他部分或外部资源（如数据库）这一概念。想象一下，你有一个函数，根据它接收的参数，可能会抛出异常或调用另一个非纯函数。这些都是可能影响应用程序某些部分的副作用的例子。

### 引用透明性

如果一个表达式可以用其结果值替换，而不改变程序的行为，无论该表达式在程序中的何处使用，那么这个表达式就是引用透明的。例如，你可以将两个不可变变量 `x` 和 `y` 的表达式赋值给第三个变量 `z`，如下所示：

```
val z = x + y
```

因此，在程序的给定作用域内，任何使用表达式 `x + y` 的地方，你都可以在该作用域内用 `z` 替换它，而不会影响程序的结果。如前所述，函数式编程为你思考并发问题提供了正确的基础。这个基础的三个基石是引用透明性、高阶函数和不可变值。理解这些关键要素对于理解函数式编程至关重要。在函数式编程中，一个具有一个或多个输入参数的纯函数不会改变输入参数，并且对于相同的输入总是返回相同的值。

注意

纯函数是引用透明的，并且没有副作用。

纯函数没有副作用。然而，一个从不引起副作用的函数将毫无用处。一个不允许副作用存在的语言也将毫无用处，因为输入和输出本质上就是副作用的产物。

我们已经介绍了足够的理论，让你可以开始探索 Scala 函数。在第 2 章中，你学习了 Scala 函数的基本语法，以及如何声明、定义和调用函数。在继续学习本章后续内容之前，我们建议你快速浏览一下第 2 章中介绍的 Scala 函数。

现在，我们将开始学习 Scala 中的一个基本函数式结构：函数字面量。



## 面向表达式的编程

在面向表达式的编程中，每条语句都是一个表达式。要理解 EOP，你必须先理解语句和表达式之间的区别。语句会执行代码，但不返回任何值，例如：

```
customer.computeDiscount()
```

表达式会返回一个值。表达式是能够求值为一个值的代码块。

```
val discount = computeDiscount(customer)
```

注意

面向表达式的编程语言是一种编程语言，其中每个结构都是表达式，因此都能求值为一个值。

在 Scala 中，以下表达式会返回一个结果：

```
scala> 2 + 2
res0: Int = 4
```

面向表达式编程的优势在 `if/else` 表达式中更为明显，它在 Scala 中也会返回一个值。

```
val test = if (3 > 2) "true" else "false"
```

这个 `if` 子句检查一个条件表达式，并根据条件表达式的值返回一个或另一个表达式。Java 中的 `if` 块不会求值出一个值。与 Scala 相反，此处展示的代码在 Java 中是非法的，因为 Java 中的 `if` 子句是一个语句，而不是一个表达式。

```
boolean test = if (3 > 2) "true" else "false"
```

要实现在 Scala `if` 子句中展示的相同效果，你必须在 Java 中使用 `?:` 语法，如下所示：

```
boolean test = 3 > 2 ? true : false ;
```

在 Java 中，`if` 和 `?:` 之间存在区别，`if` 是一个语句，而 `?:` 是一个表达式。Scala 中的 `if` 子句更像 Java 中的 `?:`，而不是 Java 中的 `if` 子句。Scala 将 `?:` 的概念与其 `if` 块统一了起来，因此 Scala 没有 `?:` 语法。

注意

如前所述，Scala 中的每个结构都是表达式，其中操作执行的顺序无关紧要，因此这些表达式可以按任意顺序执行。这个简单的概念对多核编程中的并发有着深远的影响，因为你可以并行执行表达式。

## 函数、Lambda 和闭包

其中一些术语在 Scala 的早期版本中并不存在，或者在此版本中含义发生了变化，因此在深入探讨之前，你需要理解每个术语的基本概念。

*   **函数**：此操作可以有一个名称，其代码在被人调用之前不会被执行。此操作可能有变量，也可能没有。以下是一个计算面积的函数示例：

*   **Lambda**：这是一个匿名函数，在定义中可能有变量，也可能没有。

```
scala> def calculateArea(height: Int, width: Int) = height*width
def calculateArea(height: Int, width: Int): Int
```

```
(height: Int, width: Int) => height*width
```

像 Java 或 Python 这样的语言使用术语 *lambda 函数*，但在 Scala 中，大多数开发者使用术语 *匿名函数* 或 *函数字面量* 来描述 lambda 的概念。

*   **闭包**：这是一个函数，可以是匿名的或命名的，它使用一个或多个自由变量来返回一个新值。自由变量的概念是指变量存在于函数的作用域内，而不必作为参数传递给函数。

```
scala> var count = 0
scala> val incrementCount = (number:Int) => count + number
scala> incrementCount(1)
val res1: Int = 1
```

### 函数字面量/匿名函数

字面量是表达式的最简单形式。字面量是在源代码中表示固定值的一种表示法。几乎所有编程语言都有表示原子值的表示法，例如整数、浮点数、字符串等。字面量通常用于初始化变量。在下面的例子中，1 是一个整数字面量：

```
int x = 1;
```

Scala 允许你将函数表示为字面量。函数字面量允许你以简短的形式编写函数类型的表达式，而无需为其声明名称。函数类型可以是以下之一：

*   可以分配函数的变量或参数的类型
*   高阶函数的参数（你将在第 10 章中详细了解），该参数接受一个函数参数
*   返回函数的高阶函数的结果类型

函数字面量的语法以括号括起来的、逗号分隔的参数列表开始，后跟一个箭头和函数体。函数字面量也称为*匿名函数*，即使用函数字面量语法指定的没有名称的函数。考虑一个 `add` 函数：

```
val add = (x: Int, y: Int) => x + y
```

使用函数字面量，你可以像这样定义 `add` 函数：

```
(x: Int, y: Int) => x + y
```

函数字面量被实例化为一个称为函数值的对象。函数值是一个函数对象，你可以像调用任何其他函数一样调用该函数对象。函数对象扩展了某个 FunctionN 特质，例如 Function0、Function1，一直到 Function22。根据函数中参数的数量，编译器会选择相应的 FunctionN 特质。对于有两个参数的函数，编译器会选择 Function2 作为底层类型。对于有三个参数的函数，编译器会选择 Function3；对于有四个参数的函数，选择 Function4；依此类推。

因为函数值是一个对象，它可以存储在变量中，并且可以在函数调用中使用括号来调用它，如下所示：

```
scala> val add = (a: Int, b: Int) => a + b
add: (Int, Int) => Int = 
scala> add(1, 2)
res0: Int = 3
```

此函数的调用被转换为对分配给该变量的函数类实例的 apply 方法的调用。

从这类函数字面量中，Scala 编译器会生成一个混合了某个 FunctionN 特质的函数对象。因此，`=>` 的左侧成为参数列表，右侧成为 apply 方法的实现。你在 Scala 中定义的每个函数都成为一个实现了某个特定 FunctionN 特质（范围从 Function1 到 Function22）的实例。

现在，为了更深入地了解 Function 特质，你首先编写一个计算矩形面积的函数，如下所示：

```
val areaOfRectangle:(Int, Int) => Int = (width:Int, height:Int) => width*height
```

当你在 REPL 中运行此函数时，你会看到编译器为此函数选择并选定了 Function2 特质。为什么？因为这个函数有两个参数。

```
scala> val areaOfRectangle:(Int, Int) => Int = (width:Int, height:Int) => width*height
areaOfRectangle: (Int, Int) => Int = = Lambda$1475/0x0000000801215040@712594f4
```

你可以像这样调用此函数：

```
scala> areaOfRectangle(5,3)
res0: Int = 15
```

现在，让我们看看 Scala 中的特质。Scala 包中的 Function2 看起来像这样：

```
trait Function2[-T1, -T2, +R] extends AnyRef {
...
abstract def apply( v1 :T1, v2 :T2 ) : R
...
}
```

这里只展示了 `apply` 方法。`apply` 方法中的两个类型参数 `T1` 和 `T2` 接受参数的类型，而类型参数 `R` 表示函数的返回类型。



对于你在 Scala 中定义的每个函数，编译器都会生成一个对应 Function 特质的实例，其类型参数会根据函数的参数类型和返回类型进行参数化。

在之前定义的 `areaOfRectangle` 函数中，`areaOfRectangle` 函数的类型是：

```
(Int, Int) => Int
```

这与下面展示的写法相同：

```
Function2[Int,Int,Int]
```

因此，你也可以这样定义你的 add 函数：

```
val areaOfRectangle: Function2[Int,Int,Int] = (width:Int, height:Int) => { width*height }
```

你可以在 REPL 中测试，如下所示：

```
scala> val areaOfRectangle: Function2[Int,Int,Int] = (width:Int, height:Int) => { width*height }
val areaOfRectangle: (Int, Int) => Int = Lambda$1502/0x0000000801228040@61c0fb12
```

现在，你可以显式调用 `apply` 方法来作用于给定的函数，如下所示：

```
areaOfRectangle.apply(5,3)
```

你可以在 REPL 中测试，如下所示：

```
scala> val area = areaOfRectangle.apply(5,3)
area: Int = 15
```

你可以更进一步，通过实现一个合适的 Function 特质并定义其所需的 `apply` 方法来定义一个函数。让我们为 `areaOfRectangle` 函数实现这一点：

```
val areaOfRectangle :(Int, Int) => Int = new Function2[Int, Int, Int]{
def apply(width:Int, height:Int):Int = width*height
}
```

你可以在 REPL 中测试，如下所示：

```
scala> areaOfRectangle(5,3)
res18: Int = 15
```

现在你已经学习了函数值和函数类型，接下来你将看到如何使用函数字面量将其传递给接受函数的方法或将其赋值给变量。我们将在下一节详细讨论这一点。

### 一等函数与高阶函数

Scala 能够将函数式范式与面向对象范式完美融合的关键因素之一，就是函数也是对象。在函数式编程中，函数是一等公民。一等函数可以：

1.  赋值给变量，
2.  作为参数传递给其他函数，
3.  作为值从其他函数返回。

第 2 点中强调的接受函数作为参数的函数，以及第 3 点中强调的返回函数的函数，被称为**高阶函数**。在接下来的章节中，你将学习一等函数的全部三个方面。

注意

在函数式编程中，函数是一等公民，这意味着函数可以赋值给变量，可以传递给其他函数，也可以作为值从其他函数返回。而这类接受函数作为参数或返回函数的函数，被称为高阶函数。

#### 函数作为变量

就像在面向对象编程中传递字符串、整数和其他变量一样，你也可以像传递变量一样传递函数。你可以像上一节那样定义一个函数字面量，然后将其赋值给一个变量。以下代码定义了一个函数字面量，它接受一个 `Int` 参数，并返回该 `Int` 的两倍值：

```
(i: Int) => { i * 2 }
```

现在你可以将该函数字面量赋值给一个变量。

```
scala> val doubler = (i: Int) => { i * 2 }
val doubler: Int => Int = Lambda$1503/0x0000000801229040@689eab53
```

变量 `doubler` 是一个函数实例，被称为函数值。现在你可以像下面这样调用 `doubler`：

```
scala> doubler(2)
res25: Int = 4
```

在底层，`doubler` 是 Function1 特质的一个实例，该特质定义了一个接受一个参数的函数。在实现层面，`doubler` 是使用关键字 `val` 创建并赋值给变量的函数。要将 `doubler` 定义为方法而非函数，你必须在类中定义 `doubler` 方法，并使用关键字 `def` 来定义方法。

除了调用 `doubler`，你还可以将其传递给任何接受函数参数的函数（或方法）。我们将在下一节讨论这一点。

#### 函数作为参数

你可以创建一个接受函数作为参数的函数或方法。为此，首先定义一个接受函数作为参数的方法。

```
scala> def operation(functionparam:(Int, Int) => Int) =  println(functionparam(4,4))
def operation(functionparam: (Int, Int) => Int): Unit
```

`operation` 方法接受一个名为 `functionparam` 的参数，该参数是一个函数。`functionparam` 函数接受两个 `Int` 并返回一个 `Int`。`operation` 方法返回 `Unit`，表示该方法不返回任何值。

接下来，定义一个与预期签名匹配的函数。下面的 `add` 函数匹配该签名，因为它接受两个 `Int` 参数并返回一个 `Int`：

```
val add = (x: Int, y:Int) => { x + y }
```

现在你可以将 `add` 函数传递给 `operation` 方法。

```
scala> operation(add)

```

任何匹配此签名的函数都可以传递给 `operation` 方法。让我们定义两个新函数，分别名为 `subtract` 和 `multiply`，它们接受两个 `Int` 并返回一个 `Int`。

```
val subtract = (x: Int, y:Int) => { x - y }
val multiply = (x: Int, y:Int) => { x*y }
```

现在你可以将这些函数传递给 `operation` 方法。

```
scala > operation(subtract)

scala> operation(multiply)

```

#### 返回函数

你可以从一个函数或方法中返回一个函数。为此，首先定义一个匿名函数。以下代码声明了一个匿名函数，它接受一个 String 参数并返回一个 String：

```
(name: String) => { "hello" + " " + name }
```

现在定义一个方法，返回你刚刚定义的匿名函数。

```
def greeting() = (name: String) => {"hello" + " " + name}
```

在 `=` 符号的左侧，是一个普通的方法声明。

```
def greeting()
```

在 `=` 的右侧，是一个函数字面量（一个匿名函数）。

```
scala> def greeting() = (name: String) => {"hello" + " " + name}
greeting: ()String => String
```

现在你可以将 `greeting()` 赋值给一个变量。

```
scala>  val greet= greeting()
val greet: String => String = Lambda$1261/0x000000080114c840@769b0752
```

`greet` 函数现在等同于你的匿名函数 `(name: String) => {"hello" + " " + name}`。由于匿名函数接受一个 String 参数 name，你可以向其传递一个名字。

```
scala> greet("Reader")
res0: String = hello Reader
```

### 闭包

闭包是一个函数，其返回值依赖于在该函数外部声明的一个或多个变量的值。考虑以下 `multiplier` 函数：

```
val multiplier = (x:Int) => x * 3
```

在 `multiplier` 函数中，`y` 是函数体中使用的变量。`x` 被定义为函数的参数。现在让我们像下面这样修改 `multiplier` 函数：

```
val multiplier = (x: Int) => x * y
```

由于 `x` 是函数的形式参数，每次调用 `multiplier` 时它都会被绑定到一个新值。然而，`y` 并不是形式参数。让我们进一步修改 `multiplier` 函数，如下所示：

```
var y = 3
val multiplier = (x: Int) => x * y
```

现在 `y` 引用了函数外部但在封闭作用域内的一个变量。

```
scala> var y = 3
y: Int = 3
scala> val multiplier = (x: Int) => x * y
multiplier: Int => Int = 
```

现在你可以像下面这样调用 `multiplier` 函数：

```
scala> multiplier(3)
res37: Int = 9
```

`multiplier` 函数引用了 `y`，并在每次调用时读取其当前值。Scala 编译器会创建一个闭包，将封闭作用域中的该变量包含进来。



## 部分应用函数

在函数式编程语言中，当你调用一个带有参数的函数时，就称为将函数应用于这些参数。当所有参数都传递给函数时，你就将函数完全应用到了所有参数上。

一个简单的 `add` 函数：

```
scala> val add = (x: Int, y: Int) => x + y
add: (Int, Int) => Int = 
```

`<function2>` 表示这是一个有两个参数的函数。

```
scala> add(1,2)
res01: Int = 3
```

但是，当你只向函数提供一部分参数时，表达式的结果就是一个部分应用函数。

```
val partiallyAdd = add(1, _:Int)
```

因为你没有为第二个参数提供值，所以变量 `partiallyAdd` 就是一个部分应用函数。你可以在 REPL 中看到这一点。

```
scala> val partiallyAdd = add(1, _:Int)
partiallyAdd: Int => Int = 
```

REPL 的输出显示 `partiallyAdd` 是一个实现了 `Function1` 特质的函数。实现 `Function1` 特质表明 `partiallyAdd` 函数接受一个参数。当你给 `partiallyAdd` 一个 `Int` 值 2 时，你会得到传入 `add` 和 `partiallyAdd` 函数的 `Int` 数值之和：

```
scala> partiallyAdd(2)
res02: Int = 3
```

第一个参数 1 被传入了原始的 `add` 函数，并创建了一个名为 `partiallyAdd` 的新函数，这是一个部分应用函数。然后，第二个参数 2 被传入了 `partiallyAdd`。当你提供所有参数时，原始函数就会被执行，从而产生结果。

## 柯里化函数

柯里化将一个具有多个参数的函数转换为一系列函数，每个函数只期望一个参数。

让我们看一个简单的 `add` 函数，它相加两个 `Int` 参数 `a` 和 `b`，如下所示：

```
scala> val add = (x: Int, y: Int) => x + y
add: (Int, Int) => Int = 
scala> add(3,3)
res38: Int = 6
```

在 Scala 中，柯里化函数通过多个参数列表来定义，如下所示：

```
def add(x: Int)(y: Int) = x + y
```

你也可以使用以下语法来定义一个柯里化函数：

```
def add(x: Int) = (y: Int) => x + y
```

如你所见，不是使用一个包含两个 `Int` 参数的列表，而是将柯里化的 `add` 函数应用于两个各包含一个 `Int` 参数的列表。因此，柯里化的 `add` 函数看起来像这样：

```
scala> def curriedAdd(a: Int)(b: Int) = a + b
curriedAdd: (a: Int)(b: Int)Int
scala> curriedAdd(2)(2)
res1: Int = 4
```

你可以在一个柯里化函数上定义超过两个参数。你的接受两个参数的 `add` 函数被转换成了它的柯里化等价形式。

## 函数组合

在函数式编程中，你可以从其他函数组合出函数，例如 `tan(x) = sin(x)/cos(x)`。可组合性的一个含义是函数可以被当作值来处理。到目前为止，你已经创建了简单的函数并操作了函数实例。然而，你也可以从其他函数构建函数。`functionalTo` 组合为 Scala 中许多酷炫的功能提供了基础，包括解析器组合子，你将在第 8 章中探索它。但现在，让我们看看解释一系列命令与“编译”一个解释它们的函数之间的区别。首先，让我们定义语法。我们有表达式，可以是常量值或命名变量。表达式也可以是其他表达式的加法或乘法。以下是一组描述该语法的 case 类（回顾一下，我们在第 3 章中介绍了 case 类）：

```
sealed trait Expr
case class Add(left: Expr, right: Expr) extends Expr
case class Mul(left: Expr, right: Expr) extends Expr
case class Val(value: Int) extends Expr
case class Var(name: String) extends Expr
我们可以构建像 1 + 1, Add(Val(1), Val(1)), 3 * (1 + 1), Mul(Val(3),
Add(Val(1), Val(1))), 以及 a * 11, Mul(Var("a"), Val(11)) 这样的表达式。
```

你可以通过解释表达式来求值。

```
def calc(expr: Expr, vars: Map[String, Int]): Int = expr match {
case Add(left, right) => calc(left, vars) + calc(right, vars)
case Mul(left, right) => calc(left, vars) * calc(right, vars)
case Val(v) => v
case Var(name) => vars(name)
}
```

让我们看看这个方法是如何工作的。`expr` 是要求值的表达式，`vars` 是一个包含变量的 Map。你使用模式匹配（你将在下一章中看到）来根据 case 类决定做什么。如果 `expr` 是 `Add`，你提取左右参数，它们本身也是 `Exprs`。你调用 `calc` 来计算左右参数的值并将结果相加。如果 `expr` 是 `Mul`，你执行相同的操作（只不过你是相乘而不是相加）。如果 `expr` 是 `Val`，你只需提取值并返回它。如果 `expr` 是 `Var`，你提取名称并在 `vars` Map 中查找该名称并返回。你可以将其从方法调用转换为函数。拥有一个函数允许你传递表达式所代表的逻辑。这也意味着你不必每次都解释 `Exprs` 树。让我们看看如何基于 `Expr` 组合一个函数。

```
def buildCalc(expr: Expr): Map[String, Int] => Int = expr match {
case Add(left, right) =>
val lf = buildCalc(left)
val rf = buildCalc(right)
m => lf(m) + rf(m)
case Mul(left, right) =>
val lf = buildCalc(left)
val rf = buildCalc(right)
m => lf(m) * rf(m)
case Val(v) => m => v
case Var(name) => m => m(name)
}
```

`buildCalc` 方法返回一个可以传递给其他函数的函数。此外，JVM 可以优化组合后的函数，使其性能优于解释版本。组合函数的性能更好，因为与每个元素的模式匹配相关的开销不存在。该函数通过重复调用函数的 `apply` 方法来进行求值。因此，每个节点的成本是一次或两次方法分发，而不是模式匹配的成本。让我们转向函数可以帮助提高性能和可读性的其他方式。



## 函数错误处理

当你编写遵循面向对象范式的代码时，可以使用一种机制来捕获异常并对错误进行处理。在函数式编程中，你不能返回异常，因为它遵循必须始终返回某些内容（纯函数）的理念。Scala 提供了一些机制来解决这种情况：

*   **Option/Some/None**：`Option` 与 Java 中的 `Optional` 概念相同，因此函数可以返回两种可能的值：一个整数（`Some`）或空值（`None`）。这种方法很棒，因为它减少了通过 null 进行检查的 if/else 代码块数量。

以下示例接收一个字符串并尝试将其转换为 Int：

```
scala> def transformToInt(number:String) : Option[Int] =
|     try
|        Some(Integer.parseInt(number))
|     catch
|        case e: NumberFormatException => None
def transformToInt(number: String): Option[Int]
```

现在看看当你向函数传递一个正确的值时会发生什么：

```
scala> transformToInt("1")
val res1: Option[Int] = Some(1)
```

让我们看看如果你传入一个非数字的内容：

*   **Try/Success/Failure**：这三个类与前面提到的具有相同的概念，但有一些特性，比如 `Failure` 包含导致错误的异常，而 `Try` 提供了一种简单的方法来捕获函数中所有可能的异常。

```
scala> transformToInt("a")
val res0: Option[Int] = None
```

```
scala> import scala.util.{Try, Success, Failure}
scala> def transformToInt(number: String): Try[Int] = Try(Integer.parseInt(number))
def makeInt(number: String): util.Try[Int]
```

现在看看当你向函数传递一个正确的值时会发生什么：

```
scala> transformToInt("1")
val res2: util.Try[Int] = Success(1)
```

让我们看看如果你传入一个非数字的内容：

```
scala> transformToInt("s")
val res3: util.Try[Int] = Failure(java.lang.NumberFormatException: For input string: "s")
```

## 尾调用与尾调用优化

递归函数可能会调用自身。递归在函数式编程中扮演着至关重要的角色，因为它提供了一种使用可变数据遍历数据结构的方法，因为每个函数调用都有自己的栈来存储函数参数。递归的一个经典示例可以在 `factorial` 的实现中看到，如下所示：

```
scala> def factorial(number:Int) : Int = {
|     if (number == 1)
|        return 1
|     number * factorial (number - 1)
| }
factorial: (number: Int)Int
```

你可以像这样调用这个函数：

```
scala> println(factorial(3))

```

使用递归函数的一个相关问题是，调用递归函数次数过多会导致栈溢出错误。Scala 编译器可以使用尾递归来优化递归函数，这样递归调用就不会耗尽所有栈空间，因此不会遇到栈溢出错误。尾递归是一种特定类型的递归，当函数在其最终操作中调用自身时发生。对于经过尾递归优化的函数，递归调用不会创建新的栈，而是使用当前函数的栈。只有最后一条语句是递归调用的函数才能被 Scala 编译器优化为尾递归。

接下来是使用尾调用递归计算的 `factorial` 实现。为了便于尾调用优化，Scala 提供了一个注解，用于标记一个函数以进行尾递归优化。如果一个函数被标记了尾递归函数注解，但无法进行尾递归优化，则在编译时会导致错误。要标记一个函数以进行尾递归优化，请在函数定义之前添加 `@annotation.tailrec`。

现在，用 `@annotation.tailrec` 标记前面展示的 `factorial` 函数，以指示 Scala 编译器此函数必须进行尾递归优化，并且如果带注解的函数无法进行尾递归优化，编译器应将其视为错误。

```
scala> @annotation.tailrec
| def factorial(number:Int) : Int = {
|     if (number == 1)
|        return 1
|     number * factorial (number - 1)
| }
<console>:12: error: could not optimize @tailrec annotated method factorial: it contains a recursive call not in tail position
number * factorial (number - 1)
              ^
```

如你所见，Scala 编译器抛出了一个错误。`factorial` 方法无法被优化，因为递归调用不是函数中的最后一条语句；factorial 调用自身，然后对结果执行乘法运算，所以实际上，乘法是函数中的最后一条语句，而不是递归调用。如果函数调用自身的结果用于除直接返回值之外的任何用途，则该函数无法进行尾递归优化。

标记了 `@annotation.tailrec` 的 `factorial` 方法只有在递归成为最终操作时才能成功编译。因此，你需要在调用 `factorial` 方法之前执行乘法运算，为此你使用累加器参数来保存正在进行的计算。这个参数在递归调用之前通过乘法进行计算。这样，递归就成了最终操作。

```
scala> @annotation.tailrec
| def factorial(accumulator: Int, number: Int) : Int = {
|   if(number == 1)
|     return accumulator
|   factorial(number * accumulator, number - 1)
| }
factorial: (accumulator: Int, number: Int)Int
```

成功编译保证了该函数将使用尾递归进行优化，这样每次后续调用都不会添加新的栈帧。

```
scala> println(factorial(1,3))

```



## 按名调用、按值调用与通用惰性求值

在 Java 程序中，当你调用一个带参数的方法时，参数的值会在方法被调用之前计算出来。因此，在

```
foo(1 + 1, "A String".length());
```

中，表达式 `1 + 1` 和 `"A String".length()` 都会在调用 `foo` 之前被求值。这通常是你想要的行为。然而，在某些情况下，你可能希望参数被选择性地求值或重复求值。在这些情况下，Scala 提供了按名调用机制。对于调用者来说，按名调用参数在语法上没有区别。

按名调用的第一个用途是传递一个可能需要很长时间求值、但可能不会被求值的表达式。按名调用的第二个用途是，当你希望在目标方法中多次对表达式求值时，例如，你想要对一个表达式求值，直到满足某个条件为止。这个条件可以是直到表达式返回 `false`，或者直到表达式返回 `null`。

按名调用的第一个例子是日志记录示例。如果消息不打算被记录，那么计算日志消息并将其丢弃的计算成本很高。这在 Java 代码中非常常见。

```
if (logger.level().intValue() >= INFO.intValue()) {
logger.log(INFO, "The value is "+value);
}
```

在这段代码中，你必须将对 `logger.log(INFO, "The value is "+value);` 的求值决策推送到调用 `logger` 的地方。这意味着你需要将对 `logger` 的调用包装在一个 `if` 语句中。从编码的角度来看，如果只有在字符串将要被记录时才承担计算待记录字符串的成本，并且当前日志级别由 `logger` 内部的代码（而不是在调用 `logger` 的地方）知晓并测试，那会好得多。按名调用让你能够延迟对要记录的字符串的求值，仅当该字符串确实会被记录时才进行求值。

在 Scala 中，你可以定义一个 `log` 方法，将要记录的内容作为按名调用参数：

```
def log(level: Level, msg: => String) =
if (logger.level.intValue >= level.intValue) logger.log(level, msg)
```

然后你这样调用这段代码：

```
log(INFO, "The value is "+ value)
```

Scala 版本将 `"The value is "+ value` 作为一个函数传递，该函数在 `log` 方法中每次被访问时都会求值。`log` 方法仅当日志消息将要被打印时才会访问它。你的代码更简洁，因为你不需要重复测试日志级别，但其性能与之前包含内联测试的 Java 代码一样好。要使某个参数成为按名调用，只需在类型前加上 `=>`。因此，`foo(s: String)` 是按值调用，而 `foo(s: => String)` 是按名调用。

你可能会好奇，如果创建了一个函数对象并将其传递给 `log` 方法，代码的性能如何能一样好。在 JVM 中，创建一个永远不会逃逸出当前线程且生命周期非常短的对象，其成本为零或接近于零。JVM 也可能内联 `log` 方法，使得测试无需实际的方法调用即可执行。结果是，你的 Scala 代码运行速度将与包含重复日志级别测试的 Java 代码一样快。例如，你可以收集从一个表达式返回的所有字符串，直到遇到 `null`。

```
def allStrings(expr: => String): List[String] = expr match {
case null => Nil
case s => s :: allStrings(expr)
}
```

你可以测试这个方法。

```
scala> import java.io._
import java.io._
scala> val br = new BufferedReader(new FileReader("foo.txt"))
br: java.io.BufferedReader = jaferedReva.io.Bufader@2bfa91
scala> allStrings(br.readLine)
res0: List[String] = List(import scala.xml._, , object Morg {,...)
```

每次访问按名调用参数 `expr` 时，它都会被应用。如果它被作为另一个也是按名调用的参数传递，它将被传递而不求值。在前面的代码中，你的模式匹配是针对 `expr` 的应用结果进行的。如果它是 `null`，则返回一个空列表，即 `Nil`。如果不是 `null`，则返回一个列表，该列表由当前字符串和 `allStrings(expr)` 的结果组成。

## 函数式结构

Scala 提供了用于解决特定问题的结构。其中一些结构也存在于其他语言中，如 Java 或 C++。本节是对每种结构含义的简要介绍，后续章节将提供更深入的讲解。

### 序列

Scala 中的序列是一个针对具有确定顺序的不同数据结构的非常通用的接口。你可以视为元素序列的一些例子包括列表、数组、向量或范围，它们包含某些方法，如 `apply`、`iterator` 和 `length`。

```
scala> val elements: Seq[Int] = Seq(1,2,3,4,5)
val elements: Seq[Int] = List(1, 2, 3, 4, 5)
```

以下是一些使用不同方法可以执行的操作示例：

```
scala> println(elements.reverse) // 反转序列的顺序
List(5, 4, 3, 2, 1)
scala> println(elements(1)) // 获取第二个位置的值

scala> println(elements.sorted) // 对所有元素进行排序
List(1, 2, 3, 4, 5)
```

当你使用这种类型的结构时，最坏情况下的时间复杂度是 O(N)，其中 N 是元素的数量。

要向这种类型的结构添加元素，一种可能的方法是使用关键字 `var` 将变量创建为可变的。

```
var elements: Seq[Int] = List(1, 2, 3, 4, 5)
scala> elements = elements.appended(6)
elements: Seq[Int] = List(1, 2, 3, 4, 5, 6)
```

### 映射

映射是用于使用键及其关联的值来保存信息的集合。你可以将键视为数据库中的索引或 ID，用于查找特定的行，而值则是与该键关联的信息。

```
scala> val movies: Map[String, String] = Map()
val movies: Map[String, String] = Map()
```

有几种方法可以向映射添加信息：

*   使用 `->` 来表示左侧是键，右侧是值。

*   使用括号和逗号来分隔映射中的不同行。

```
scala> val movies: Map[String, String] = Map("ET" -> "Science fiction", "Saw" -> "Horror")
val movies: Map[String, String] = Map(ET -> Science fiction, Saw -> Horror)
```

*   使用 `+` 向现有映射添加新的键值对。

```
scala> val movies: Map[String, String] = Map(("ET", "Science fiction"), ("Saw", "Horror"))
val movies: Map[String, String] = Map(ET -> Science fiction, Saw -> Horror)
```

```
scala> movies + ("Rocky IV" -> "Action")
val res6: Map[String, String] = Map(ET -> Science fiction, Saw -> Horror, Rocky IV -> Action)
```

这种方法的主要问题是，你创建了一个包含原始信息和新信息的新变量。这通常不是问题，因为在上一章中你了解到 Scala 的理念是大多数时候创建不可变变量，但想象一下，由于某种原因，你需要在同一个映射中添加一个元素。正确的方法是：

```
scala> var movies: Map[String, String] = Map("ET" -> "Science fiction", "Saw" -> "Horror")
var movies: Map[String, String] = Map(ET -> Science fiction, Saw -> Horror)
scala> movies += ("Rocky IV" -> "Drama")
```

请注意，如果你在映射中添加同一个键两次但值不同，则只会存在一个键，其值为最后一个值。

```
scala> val movies: Map[String, String] = Map("ET" -> "Science fiction", "Saw" -> "Horror", "Saw" -> "Drama")
val movies: Map[String, String] = Map(ET -> Science fiction, Saw -> Drama)
```

最后一点：如果你需要从映射中移除一个元素，你可以使用 ***-*** 或 `removed` 方法以及键的名称。

```
scala> var movies: Map[String, String] = Map("ET" -> "Science fiction", "Saw" -> "Horror")
var movies: Map[String, String] = Map(ET -> Science fiction, Saw -> Horror)
scala> movies -= ("ET")
scala> println(movies)
Map(Saw -> Horror)
scala> movies = movies.removed("Saw")
movies: Map[String, String] = Map()
```



### 集合

集合是一种包含无序元素的结构，类似于列表。主要区别在于，这种结构只包含值，且没有重复项。

```
scala> val countries: Set[String] = Set()
val countries: Set[String] = Set()
```

向集合中添加信息有几种方式：

*   在构造函数中，用逗号分隔。

*   使用 **+** 向现有集合中添加新值。

```
scala> val countries: Set[String] = Set("Italy", "Spain", "France", "Germany")
val countries: Set[String]
```

```
scala> countries + ("Russia")
val res10: Set[String] = HashSet(Spain, Russia, Italy, France, Germany)
```

采用这种方法，与 Map 类似，每次使用 + 添加元素时，都会创建一个包含所有值的新集合实例。一种修改集合实际值的可行方法是将变量声明为可变，并使用 += 向现有集合添加新元素。

```
scala> var countries: Set[String] = Set("Italy", "Spain", "France", "Germany")
var countries: Set[String] = Set(Italy, Spain, France, Germany)
scala> countries += ("Russia")
scala> print(countries)
HashSet(Spain, Russia, Italy, France, Germany)
```

当你更改集合包含的值时，Scala 会将结构类型更改为具体的类，例如 `HashSet`。

要移除集合中的现有值，可以使用 - 和要移除的值。如果该值不存在，则什么也不会发生。

```
scala> var countries: Set[String] = Set("Italy", "Spain", "France", "Germany")
scala> countries -= ("Italy")
scala> print(countries)
HashSet(Spain, France, Germany)
```

### 元组

Scala 提供了一种名为元组的结构，用于保存不同类型的信息。你可以将元组视为一个自定义对象，但无需定义所有属性。

例如，让我们创建一个包含某部电影特定信息的元组。

```
scala> val moviesReproductions = Tuple2("ET", 2)
val moviesReproductions: (String, Int) = (ET,2)
```

要访问某个位置的值，只需指明该位置即可。

```
scala> moviesReproductions._1
val res19: String = ET
```

如果你把值的顺序放错了，可以使用 `swap` 方法来交换它们。

```
scala> moviesReproductions.swap
val res20: (Int, String) = (2,ET)
```

元组可以支持多个元素，而不仅仅是两个。如果你输入 *tuple* 并按 Tab 键，会出现许多选项。

```
scala> Tuple
Tuple     Tuple10   Tuple12   Tuple14   Tuple16   Tuple18   Tuple2    Tuple21   Tuple3    Tuple5    Tuple7    Tuple9
Tuple1    Tuple11   Tuple13   Tuple15   Tuple17   Tuple19   Tuple20   Tuple22   Tuple4    Tuple6    Tuple8
```

### 选项

正如你在“函数式错误处理”一节中读到的，选项帮助开发者减少检查某物是否为 null 的条件判断。这是防止空指针异常（这种异常通常出现在检查变量是否为 null 时）的好方法。

这种结构出现在 Scala 2.13.x 中，与 Java 8 中出现的 `Optional` 类有许多共同之处。

这种结构用于 `map`、`flatMap` 和 `filter` 等操作中，你将在下一节详细了解这些操作。有些结构（如 Maps）在某些方法（如 `get`）中会返回一个 Option。

```
scala> val movies = Map("ET" -> "Science fiction")
val movies: Map[String, String] = Map(ET -> Science fiction)
```

以下是尝试获取 `Map` 中存在和不存在的值时的情况：

```
scala> movies.get("ET")
val res1: Option[String] = Some(Science fiction)
scala> movies.get("ET II")
val res2: Option[String] = None
```

#### 实例化

实例化 Option 类型有几种不同的方式：

*   使用 Option 并传入值。采用这种方法，编译器会检查该值是否为 null，并创建正确的类。

*   使用 Some 或 None 定义结果。采用这种方法，你省去了一步，因为编译器会将你指定的类赋值给变量。

```
scala> val instantiateOption : Option[Int] =  Option(1)
val instantiateOption: Option[Int] = Some(1)
```

```
scala> val instantiateOption : Option[Int] =  None
val instantiateOption: Option[Int] = None
scala> val instantiateOption : Option[Int] =  Some(1)
val instantiateOption: Option[Int] = Some(1)
```

#### 链式方法

假设你有一个返回 Option 且值为 None 的方法，但你的逻辑需要始终有一个值或执行某些操作。Option 提供了一个名为 `orElse` 的方法，该方法仅在值为 `None` 时执行。

```
scala> def returnNoneFunction() : Option[String] = None
def returnNoneFunction(): Option[String]
scala> def returnSomeFunction() : Option[String] = Some("Your logic works")
def returnSomeFunction(): Option[String]
scala> val obtainInformation = returnNoneFunction() orElse returnSomeFunction()
val obtainInformation: Option[String] = Some(Your logic works)
```

## 函数式操作

与许多其他语言一样，Scala 允许你对集合使用一组操作。

### 遍历

遍历是集合元素中最常见的操作，因为你可以迭代集合中的所有元素来执行特定操作。

```
scala> val countries: Set[String] = Set("Italy", "Spain", "France", "Germany")
scala> countries.foreach(println)
Italy
Spain
France
Germany
```

### 映射

当你需要迭代每个元素并创建一个输出（输出类型可以与输入相同，也可以不同）时，Map 结构非常有用。

假设你需要迭代一组国家并计算每个国家名称的长度。

```
scala> val countries: Set[String] = Set("Italy", "Spain", "France", "Germany")
scala> val nameSize = countries.map(country => (country, country.length))
val nameSize: Set[(String, Int)] = Set((Italy,5), (Spain,5), (France,6), (Germany,7))
```

另一种可能性是在实际值后面连接一个后缀。

```
scala> println(countries.map(_ + " - Europe"))
Set(Italy - Europe, Spain - Europe, France - Europe, Germany - Europe)
```

### 过滤

过滤是最重要的操作之一，因为它允许你迭代、过滤元素，并仅返回结果为 `true` 的元素组成的新结构。

```
scala> val countries: Set[String] = Set("Italy", "Spain", "France", "Germany")
scala> countries.filter(country=>country.startsWith("I"))
val res21: Set[String] = Set(Italy)
```

你可以组合所有操作来降低代码的复杂性，例如过滤出以 I 开头的国家并打印结果。

```
scala> countries.filter(country=>country.startsWith("I")).foreach(println)
Italy
```

本节只是对你所能执行的所有操作的一个概述。你将在后续章节中看到更多细节。

## 总结

你在第 2 章中看到了 Scala 函数在声明、定义和调用时的实际应用。作为对第 2 章简要介绍的延续，本章详细介绍了 Scala 中的函数式编程，并引入了几个函数式结构。在下一章中，你将学习 Scala 的模式匹配。

脚注 1



