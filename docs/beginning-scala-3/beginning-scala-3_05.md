# 5. 模式匹配

到目前为止，你已经探索了 Scala 的一些基本函数式基石：不可变数据类型和将函数作为参数传递。函数式编程的第三个基石是模式匹配。模式匹配提供了一种强大的工具，能够以简洁且可维护的方式声明业务逻辑。Scala 将传统的函数式编程模式匹配与面向对象的概念相结合，为编写程序提供了一种非常强大的机制。在本章中，你将学习模式匹配的基础知识。然后，你将看到 Scala 的样例类如何桥接面向对象的数据封装和函数分解。接下来，你将看到 Scala 的模式匹配结构如何成为可以传递和组合的函数。让我们先看一个简单的例子。

## 基础模式匹配

模式匹配允许你在多个条件之间进行程序化选择，例如，如果布尔值 x 为真，则打印一条“true”消息；如果 x 为假，则打印一条“false”消息。但是，不要被这个简单的例子迷惑，从而低估了 Scala 模式匹配的真正威力。Scala 的模式匹配允许你的情况远比仅仅判断 x 是真还是假要复杂得多。在 Scala 中，你的情况可以包括类型、通配符、序列、正则表达式等等。

```
scala> def printNum(int: Int) =
int match
case 0 => println("Zero")
case 1 => println("One")
case _ => println("more than one")
def printNum(int: Int): Unit
```

现在用不同的参数执行该对象，并查看结果。

```
scala> printNum(0)
Zero
scala> printNum(1)
One
scala> printNum(2)
more than one
```

如你所见，前面的代码块中并没有什么高深的概念。它只是打印 *zero*、*one* 或 *more than one*。但是，请注意最后一个带有下划线（`_`）通配符的情况。它匹配任何在其上方情况中未定义的内容，因此它的作用与 Java 和 C# 中 switch 语句的 default 关键字相同。如果你不熟悉 Java 或 C# 中的 switch 语句，也不必担心。它只是意味着，如果你试图在任何其他情况子句之前放置一个 `case _`，编译器将在下一个子句上抛出一个不可达代码错误，如下例所示，因为没有任何内容能通过 `case _` 子句，所以 `case _` 充当了默认值。

```
scala> def printNum(int: Int) =
int match
case _ => println("more than one")
case 0 => println("Zero")
case 1 => println("One")
```

当你尝试创建该对象时，控制台中会出现一些错误。

```
def printNum(int: Int): Unit
4         case 0 => println("Zero")
^
Unreachable case
5         case 1 => println("One")
^
Unreachable case
^
```

现在看看当你的模式匹配没有默认值，并且你传递了一个没有对应情况的值时，会发生什么：

```
scala> def printNum(int: Int) =
int match
case 0 => println("Zero")
case 1 => println("One")
scala> printNum(2)
scala.MatchError: 2 (of class java.lang.Integer)
at rs$line$9$.printNum(rs$line$9:4)
... 28 elided
```

Scala 不仅提供 `_` 来定义模式匹配表达式中的默认行为，你还可以使用任何其他具有相同功能的词。请看下面使用 `default` 代替 `_` 的例子：

```
scala> def printNum(int: Int) =
int match
case 0 => println("Zero")
case 1 => println("One")
case default => println("more than one")
def printNum(int: Int): Unit
scala> printNum(2)
more than one
```

模式匹配，其核心是一组复杂的 `if/else` 表达式，允许你从几个备选方案中进行选择。乍一看，如果我们毫不掩饰地假设你具有 Java 背景，那么模式匹配看起来很像 Java 的 switch 语句，但即使是在 Java 最简单的例子中，你也会注意到几个关键区别。让我们通过分别使用 Scala 的模式匹配和 Java 的 switch 语句编写一个例子来分析这一点。下面的代码块演示了一个计算斐波那契数列的例子：

```
scala> def fibonacci(in: Int): Int =
in match
case 0 => 0
case 1 => 1
case n => fibonacci(n - 1) + fibonacci(n - 2)
def fibonacci(in: Int): Int
```

让我们用 Java 编写相同的代码：

```
public int fibonacci(int in) {
switch (in) {
case 0:
return 0;
case 1:
return 1;
default:
return fibonacci(in - 1) + fibonacci(in - 2);
}
}
```

你会注意到这两个例子之间的以下区别：

*   在 Scala 中，情况之间没有 break 语句，而在 Java 中，你需要在 case 末尾使用 break 或 return。

*   Scala 中的最后一个情况将默认值赋给变量 `n`。Scala 中的模式匹配也是一个返回值的表达式。

我们刚刚指出了使用 Scala 的模式匹配和 Java 的 switch 语句编写的斐波那契例子中的几个关键区别。请考虑到，最新版本的 Java 具有一些 Scala 自 2.12.x 版本以来就提供的特性。

现在，我们将通过修改上述代码来展示 Scala 的模式匹配如何变得更好。

Scala 允许在模式中放置守卫，以测试那些无法在模式声明本身中测试的特定条件。因此，你可以编写你的斐波那契计算器，如果传入负数则返回 0，如下所示：

```
def fib2(in: Int): Int =
in match
case n if n <= 0 => 0
case 1 => 1
case n => fib2(n - 1) + fib2(n - 2)
```

`case n if n <= 0 => 0` 是模式中的第一个测试。该测试将值提取到变量 `n` 中，并测试 `n` 是否为 0 或负数，如果是则返回 0。随着逻辑复杂度的增加，守卫非常有用。请注意，case 语句是按照它们在代码中出现的顺序进行求值的。因此，`case n if n <= 0 =>` 会在 `case n =>` 之前被测试。在底层，编译器可能会优化模式并最小化测试次数，缓存测试结果，甚至缓存守卫的结果。

最后要考虑的一点是，你可以有多个值具有相同的行为，因此你不需要多次定义相同的代码块。相反，你可以使用 `|` 将所有逻辑合并到一个 case 中。请检查下面的代码块，它在接收到 -1 或 -2 时具有特定的行为：

```
scala> def printNum(int: Int) =
int match
case 0 => println("Zero")
case 1 => println("One")
case -1 | -2 => println("less than zero")
case _ => println("more than one")
scala> printNum(-2)
less than zero
```

### 匹配任意类型

让我们考虑一个包含任意类型元素的列表，其中包含一个 String、一个 Double、一个 Int 和一个 Char：

```
val anyList = List(1, "A", 2, 2.5, 'a')
```

你决定使用以下代码让用户知道列表中的 Int、String 和 Double 类型：

```
scala> for (m <- anyList) do
m match
case i: Int => println("Integer: " + i)
case s: String => println("String: " + s)
case f: Double => println("Double: " + f)
case other => println("other: " + other)
```

在 REPL 的控制台中会出现类似这样的内容：

```
Integer: 1
String: A
Integer: 2
Double: 2.5
other: a
```

这段代码具有深远的意义，你将在下一节中了解到。



### 测试数据类型

让我们编写一个方法，用于测试传入的对象是字符串、整数还是其他类型。根据其类型不同，将执行不同的操作，如下例所示：

```
def test2(in: Any) =
in match
case s: String => "String, length "+s.length
case i: Int if i > 0 => "Natural Int"
case i: Int => "Another Int"
case a: AnyRef => a.getClass.getName
case _ => "null"
```

第一行测试是否为字符串。如果是字符串，参数会被转换为字符串并赋值给变量 `s`，然后返回 `=>` 右侧的表达式。请注意，如果参数为 null，它将不会匹配任何与类型比较的模式。在下一行，参数被测试是否为整数。如果是整数，参数会被转换为整数并赋值给 `i`，然后测试守卫条件。如果该整数是自然数（大于零），则返回“Natural Int”。通过这种方式，Scala 的模式匹配取代了 Java 的测试/转换范式。现在，为了充分理解 Scala 中模式匹配的强大之处，让我们看看前面代码块的 Java 等价实现：

```
public String test2(Object in) {
if (in == null) {
return "null";
}
if (in instanceof String) {
String s = (String) in;
return "String, length " + s.length();
}
if (in instanceof Integer) {
int i = ((Integer) in).intValue();
if (i > 0) {
return "Natural Int";
}
return "Another Int";
}
return in.getClass().getName();
}
```

在前面的 Java 等价代码中，类型测试和转换操作是分离的。当复制粘贴测试/转换代码块时，这常常会导致错误。编译器不会检查类型测试是否与转换匹配，在复制粘贴的 Java 代码中，测试和转换不匹配的情况并不少见。而 Scala 中的相同代码更简短，并且没有显式转换。模式匹配是避免显式转换的强大方式。

### 列表中的模式匹配

Scala 的模式匹配也可以应用于列表。Scala 的 `List` 集合实现为链表，其中列表的头部称为 cons 单元。

注意

cons 单元的命名源于 Lisp，源自构建列表的操作。通过将一个 cons 单元链接到列表的头部来构建列表。

它包含一个指向其内容的引用，以及另一个指向列表尾部的引用，尾部可能是另一个 cons 单元或 `Nil` 对象。列表是不可变的，因此同一个尾部可以被多个不同的头部共享。在 Scala 中，cons 单元由 `::` 样例类表示。也许你刚刚已经恍然大悟：“啊哈！”

在 Scala 中创建列表非常简单：

```
1 :: Nil
```

`::` 既是方法的名称，也是样例类的名称。通过保持创建方法 `::` 和样例类名称相同，你可以以一种语法上令人愉悦的方式构建列表并进行模式匹配。正如你刚刚所见，样例类可以在模式匹配中用于比较或提取值。这也适用于列表，并带来一些非常优雅的语法。

你可以这样构建一个列表：

```
scala> val x = 1
x: Int = 1
scala> val rest = List(2,3,4)
rest: List[Int] = List(2, 3, 4)
scala> val list = x :: rest
val list: List[Int] = List(1, 2, 3, 4)
scala> def printNumbers(list : List[Int]) : Unit =
list match
case head :: tail => println(head); printNumbers(head)
case Nil => println("")
def printNumbers(list: List[Int]): Unit
scala> printNumbers(list)

```

然后你可以在模式匹配中提取列表的 `head (x)` 和 `tail (rest)`。

### 模式匹配与列表

模式匹配和列表是相辅相成的。你可以从使用模式匹配对 `List[Int]` 中的所有奇数整数求和开始。

```
def sumOdd(in: List[Int]): Int =
in match
case Nil => 0
case x :: rest if x % 2 == 1 => x + sumOdd(rest)
case _ :: rest => sumOdd(rest)
```

如果列表为空（`Nil`），则返回 0。下一个 case 从列表中提取第一个元素，并测试它是否为奇数。如果是，则将其与列表中剩余奇数的和相加。默认 case 是忽略列表的第一个元素（使用 `_` 通配符匹配），并返回列表中剩余奇数的和。

提取列表的头部很有用，但在对列表进行模式匹配时，你可以匹配列表中的任意数量的元素。在下面的示例中，你将任意数量的连续相同项替换为该单项的一个实例：

```
def noPairsT: List[T] =
in match
case Nil => Nil
case a :: b :: rest if a == b => noPairs(a :: rest)
// 列表中的前两个元素相同，因此我们将
// 调用 noPairs，传入一个排除了重复元素的列表
case a :: rest => a :: noPairs(rest)
// 返回一个由第一个元素和 noPairs
// 应用于列表剩余部分的结果组成的列表
```

让我们运行代码，看看它是否按预期工作：

```
scala> noPairs(List(1,2,3,3,3,4,1,1))
res6: List[Int] = List(1, 2, 3, 4, 1)
```

模式匹配既可以匹配常量，也可以提取信息。假设你有一个 `List[String]`，并且想要实现一条规则：丢弃“ignore”字符串前面的元素。在这种情况下，你同时使用模式匹配进行测试和提取：

```
def ignore(in: List[String]): List[String] =
in match
case Nil => Nil
case _ :: "ignore" :: rest => ignore(rest)
// 如果列表中的第二个元素是 "ignore"，则返回
// 对列表剩余部分应用 ignore 方法的结果
case x :: rest => x :: ignore(rest)
// 返回一个由列表的第一个元素加上
// 对列表剩余部分应用 ignore 方法的结果组成的列表
```

你已经了解了如何使用模式匹配和列表进行提取和相等性测试。你还可以使用类测试/转换机制来查找 `List[Any]` 中的所有字符串。

```
def getStrings(in: List[Any]): List[String] =
in match
case Nil => Nil
case (s: String) :: rest => s :: getStrings(rest)
case _ :: rest => getStrings(rest)
```

然而，将 `List[Any]` 过滤为特定类型列表的范式方法是使用模式作为函数。你将在“作为函数的模式匹配”一节中看到这一点。

在本节中，你探索了如何进行模式匹配。你了解了列表的提取和模式匹配。列表在 Scala 中可能看起来是一种特殊的构造，但实际上 Scala 中的列表并没有什么特殊之处。

## 编译器优化

从 Scala 2.x.x 开始，有一种方法可以使用注解 `@switch` 来优化模式匹配表达式。当存在该注解时，编译器会检查表达式是否有效，以便引入一些优化。在幕后，这个注解告诉编译器不要转换简单的条件表达式（`if/else`）。相反，编译器会尝试生成一个分支表，这比其他表达式更快。

```
scala> import scala.annotation.switch //你需要导入这个注解
scala> def printNum(int : Int) =
(int: @switch) match //你指示 Scala 使用优化
case 0 => println("Zero")
case 1 => println("One")
case _ => if int > 0 then println("More than one") else println("Less than zero")
def printNum(int: Int): Unit
scala> printNum(3)
More than one
```

使用这个新注解一切正常，但在幕后，这段代码会根据 case 的类型被转换为两种不同的结构。这些结构被称为 `tableswitch` 和 `lookupswitch`。^(²⁶)



### 表跳转（Tableswitch）

当不同值之间连续或仅有微小间隙时，编译器通常会使用表跳转。这种方法的时间复杂度为 O(1)，因此你可以直接访问与特定键关联的代码块；这是因为该值被用作索引。

```
import scala.annotation.switch
def print(int : Int) =
(int: @switch) match
case 0 => println("Zero")
case 1 => println("One")
case 2 => println("Two")
case 3 => println("Three")
case 4 => println("Four")
case _ => println("More than four")
```

### 查找跳转（Lookupswitch）

当值不连续且结构中的不同值之间存在较大间隙时，会使用查找跳转。编译器会对所有键进行排序，当有人试图获取某个 case 的逻辑时，编译器会执行二分查找。因此，该算法的复杂度为 O(log n)。

```
import scala.annotation.switch
def print(letter : Char) =
(letter: @switch) match
case 'A' => println("Animal")
case 'B' => println("Bank")
case 'D' => println("Dance")
case 'E' => println("Elephant")
case _ => println("Is not a letter")
```

### 注意事项

在代码中包含此注解时，需要考虑以下几点：

*   模式匹配需要包含两个以上的 case。如果不超过两个 case，代码可以编译，但不会带来性能提升。

*   不同 case 的值必须是字面量，而非引用值，因为这是编译器判断需要使用哪种结构（表跳转或查找跳转）的唯一方式。

*   不同 case 不应包含任何条件，如上文所示***。***

## 使用 Matchable 特质的模式匹配

Scala 3 引入了 `Matchable` 特质。请记住，特质类似于其他语言中的接口，它控制着执行模式匹配的能力。在 Scala 3.0.x 版本中，这个新特质没有成员，但负责在 Scala 中引入新功能的开发者们计划添加这一特定特性，该特性与 Java 最新版本中的功能大致相似。

`AnyVal` 和 `AnyRef` 类现在分别继承自两个不同的东西：`Any` 和 `Matchable`。

```
abstract class Any:
def isInstanceOf
def getClass
//以及更多方法。
trait Matchable extends Any
class AnyVal extends Any, Matchable
class AnyRef extends Any, Matchable
```

现在设想你需要创建一个方法，该方法接收一个元素序列，并根据元素的类型执行特定操作。实现方式是，指明你的方法将接收一个 `Matchable` 类型的参数（`T` 不表示特定类型）（使用 `<:` 表示该类型继承自 `Matchable`）。

```
scala> val seq = Seq("one", 2, 3.5)
val seq: Seq[Matchable] = List(one, 2, 3.5)
scala> def check T  println(s"Int $i")
case s: String => println(s"String $s")
case f: Float => println(s"Float $f")
case other => println(s"Other $other")
}
def check[T  check(seq)
String one
Int 2
Other 3.5
```

## 模式匹配与 Case 类

Case 类会自动获得 `toString`、`hashCode` 和 `equals` 方法。此外，它们还会获得属性和提取器^([²⁷)，你将在第 8 章中看到。Case 类也具有属性，并且可以在不使用 `new` 关键字的情况下构造。

让我们定义一个 `case` 类。

```
case class Person(name: String, age: Int, valid: Boolean)
```

让我们创建一个 `Person` 实例。

```
scala> val p = Person("David", 45, true)
p: Person = Person(David,45,true)
```

你也可以使用 `new` 来创建一个 `Person`。

```
scala> val m = new Person("Martin", 44, true)
m: Person = Person(Martin,44,true)
```

每个 `Person` 实例都具有与构造参数对应的属性。

```
scala> p.name
res0: String = David
scala> p.age
res1: Int = 45
scala> p.valid
res2: Boolean = true
```

默认情况下，这些属性是只读的，并且 `case` 类是不可变的。

```
scala> p.name = "Fred"
:7: error: reassignment to val
p.name = "Fred"
```

你也可以使属性可变。

```
scala> case class MPerson(var name: String, var age: Int)
defined class MPerson
scala> val mp = MPerson("Jorge", 24)
mp: MPerson = MPerson(Jorge,24)
scala> mp.age = 25
scala> mp
res3: MPerson = MPerson(Jorge,25)
```

到目前为止，这只是一些语法糖。你可能会问，它如何与模式匹配配合使用呢？

针对 `case` 类的模式匹配在语法上令人愉悦且非常强大。你可以针对你的 `Person` 类进行匹配，并且可以免费获得提取器。

```
def older(p: Person): Option[String] =
p match
case Person(name, age, true) if age > 35 => Some(name)
case _ => None
```

你的方法会匹配 `Person` 的实例。如果 `valid` 字段为 true，则提取 `age` 并与守卫条件进行比较。如果守卫条件成功，则返回该人的名字；否则返回 `None`。让我们试一下：

```
scala> older(p)
res4: Option[String] = Some(David)
scala> older(Person("Fred", 73, false))
res5: Option[String] = None
scala> older(Person("Jorge", 24, true))
res6: Option[String]
```

### Case 类中的嵌套模式匹配

在 Scala 的早期版本中，你可以创建包含其他 `case` 类的 `case` 类，并可以使用模式匹配，但在最新版本的 Scala（2.13.1）中，此功能已被弃用并移除。以下代码展示了当你尝试创建一个继承自另一个 `case` 类的 `case` 类时会发生什么：

```
scala> case class MarriedPerson(override val name: String,
override val age: Int,
override val valid: Boolean,
spouse: Person) extends Person(name, age, valid)
1 |case class MarriedPerson(override val name: String,
^
Cannot extend sealed class Person in a different source file
4 |spouse: Person) extends Person(name, age, valid)
^^^^^^^^^^^^^^^^^^^^^^^^
case class MarriedPerson has case ancestor class Person, but case-to-case inheritance is prohibited.
To overcome this limitation, use extractors to pattern match on non-leaf nodes .
```



## 模式匹配作为函数

Scala 模式是与 `match` 运算符一起使用时语言中的语法元素。不过，你也可以将模式匹配作为参数传递给其他方法。Scala 会将模式匹配编译成一个 `PartialFunction[A,B]`，它是 `Function1[A,B]` 的一个子类。因此，模式可以传递给任何接受单参数函数的方法。这允许你将

```
list.filter(a => a match {
case s: String => true
case _ => false
})
```

简化为

```
list.filter {
case s: String => true
case _ => false
}
```

因为模式是函数，而函数是实例，所以模式也是实例。除了将它们作为参数传递外，它们还可以被存储起来供以后使用。

除了 `Function1` 的 `apply` 方法外，`PartialFunction` 还有一个 `isDefinedAt` 方法，因此你可以测试某个模式是否匹配给定的值。如果你尝试应用一个未针对该值定义的 `PartialFunction`，则会引发 `MatchError`。这有什么用呢？

如果你正在构建一个 Web 应用程序，可能有一些特定的 URL 需要特殊处理，而其他 URL 则按默认方式处理。URL 可以表示为 `List[String]`。你可以执行以下操作：

```
def handleRequest(req: List[String])(exceptions: PartialFunction[List[String], String]): String =
if (exceptions.isDefinedAt(req)) then
exceptions(req)
else
"Handling URL " + req + " in the normal way"
```

因此，如果偏函数 exceptions（即模式）根据 `isDefinedAt` 方法匹配了请求 `req`，那么你就允许该请求由 `exceptions` 函数处理。否则，你将执行默认处理。你可以调用 `handleRequest`，并通过一个单独的处理程序来处理任何 `"api"` 请求。

```
handleRequest("foo" :: Nil) {
case "api" :: call :: params => doApi(call, params)
}
def doApi(call: String, params: List[String]): String =
"Doing API call "+ call
```

偏函数可以使用 `orElse` 方法组合成一个函数。因此，你可以定义几个偏函数。

```
val f1: PartialFunction[List[String], String] =
case "stuff" :: Nil => "Got some stuff"
val f2: PartialFunction[List[String], String] =
case "other" :: params => "Other: " + params
```

并且你可以组合它们。

```
val f3 = f1 orElse f2
```

然后你可以将它们传递给 `handleRequest` 方法。

```
handleRequest("a" :: "b" :: Nil)(f3)
```

通过这种方式，Scala 为你提供了一种非常优雅、声明式的方式来处理复杂的过滤任务。偏函数可以匹配数据，并且可以像 Scala 中的任何其他实例一样被传递。偏函数取代了 Java 中的许多 XML 配置文件，因为模式匹配提供了与配置文件相同的声明式能力，但它们是类型安全的、高性能的，并且可以包含守卫条件，通常还能利用代码中的任何方法。以下是在 ESME^(²⁸) 代码中使用模式匹配来分发 REST 请求的示例：^(²⁹)

```
def dispatch: LiftRules.DispatchPF = {
case Req("api" :: "status"   :: Nil, "", GetRequest) => status
case Req("api" :: "messages" :: Nil, "", GetRequest) => getMsgs
case Req("api" :: "messages" :: "long_poll" :: Nil, "", GetRequest) =>
waitForMsgs
case Req("api" :: "messages" :: Nil, "", PostRequest) =>
() => sendMsg(User.currentUser.map(_.id.is), S)
case Req("api" :: "follow"    :: Nil, _, GetRequest) =>
following(calcUser)
case Req("api" :: "followers" :: Nil, _, GetRequest) =>
followers(calcUser)
case Req("api" :: "follow"    :: Nil, _, PostRequest) =>
performFollow(S.param("user"))
}
```

## 正则表达式上的模式匹配

你可以将模式匹配与正则表达式结合使用，以提取字符串或其他类型中包含的信息。假设你有一个序列，其中包含不同的多媒体元素及其信息，并且你需要将其打印出来。

首先，定义包含你需要提取信息的元素序列。

```
val multimedia = Seq(
"Photography: author=Richard Avedon, place=New York",
"Movie: name=Casablanca, director=Michael Curtiz",
"Music: name=Fly on the moon, author=Fran Sinatra");
```

现在创建两个提取器，一个用于获取照片的信息，另一个用于获取电影的信息。

```
val PhotographyExtractorRE = """Photography: author=([^,]+),\s+place=(.+)""".r
val MovieExtractorRE = """Movie: name=([^,]+),\s+director=(.+)""".r
```

最后但同样重要的是，你需要定义条件，将序列中的每个元素映射到特定的 case。

```
multimedia.map {
case PhotographyExtractorRE(author, place) => println(s"Phography - Author: $author - Place: $place")
case MovieExtractorRE(name, director) => println(s"Movie - Name: $name - Director: $director")
case unknown => println(s"Unknown entry $unknown")
}
```

执行此代码后，你将获得的输出如下所示：

```
Phography - Author: Richard Avedon - Place: New York
Movie - Name: Casablanca - Director: Michael Curtiz
Unknown entry Music: name=Fly on the moon, author=Fran Sinatra
```

关于如何创建正则表达式的解释超出了本书的范围，但你可以找到像 RegExr 这样的网站来创建和测试每种可能的情况。^(³⁰)

## 面向对象与函数式的张力

此时，硬核的面向对象设计者可能对 Scala `case` 类暴露大量内部信息感到有些不满。数据隐藏是 OOP 抽象的重要组成部分。但事实上，我们定义的大多数 Java 类都有 getter 和 setter，因此在 OOP 中数据是暴露的。然而，在程序中暴露的内部状态量与隐藏的状态量之间存在一种张力。在本节中，你将探讨用于数据隐藏和暴露的 OOP 与函数式编程（FP）模式。

OOP 中的另一个张力是如何在类和接口层次结构中定义方法。方法定义属于哪里？当一个库已部署，但有必要为子类添加新功能时会发生什么？你如何改造那些已固化的库类来添加此功能？更具体地说，如果你有一个形状库（圆形、正方形和矩形），每个形状都有一个 `area` 方法但隐藏了所有其他数据，你如何为这些形状添加一个 `perimeter` 方法？让我们探讨这种张力以及 Scala 和 FP 为解决这种张力所提供的工具。



### 形状抽象

如果你有一组形状，它们都派生自包含 `area` 方法的公共特质 `OShape`，那么使用传统的面向对象编程（OOP）方法时，你的对象定义将如下所示：

```
trait OShape:
def area: Double
class OCircle(radius: Double) extends OShape:
def area = radius * radius * Math.PI
class OSquare(length: Double) extends OShape:
def area = length * length
class ORectangle(h: Double, w: Double) extends OShape:
def area = h * w
```

让我们将其与模式匹配的实现进行比较：

```
trait Shape
case class Circle(radius: Double) extends Shape
case class Square(length: Double) extends Shape
case class Rectangle(h: Double, w: Double) extends Shape
object Shape:
def area(shape: Shape): Double =
shape match
case Circle(r) => r * r * Math.PI
case Square(l) => l * l
case Rectangle(h, w) => h * w
```

在模式匹配的示例中，所有计算面积的逻辑都位于同一个方法中，但从 `Shape` 特质来看，该方法的存在并不明显。到目前为止，OOP 方法似乎是正确的答案，因为它清楚地表明了形状能做什么。

然而，如果你有一个形状库，并且想要计算每个形状的周长，那么模式匹配就有其优势了。

```
def perimeter(shape: Shape) =
shape match
case Circle(r) => 2 * Math.Pi  * r
case Square(l) => 4 * l
case Rectangle(h, w) => h * 2 + w * 2
```

在这种情况下，开放数据使得实现 `perimeter` 方法成为可能。使用 OOP 实现，你必须暴露数据才能实现 `perimeter` 方法。因此，你的 OOP 实现将如下所示：

```
trait OShape:
def area: Double
class OCircle(radius: Double) extends OShape:
def area = radius * radius * Math.PI
def getRadius = radius
class OSquare(length: Double) extends OShape:
def area = length * length
def getLength = length
class ORectangle(h: Double, w: Double) extends OShape:
def area      = h * w
def getHeight = h
def getWidth  = w
```

从更广泛的意义上讲，对象层次结构的设计者很少会实现库使用者所需的所有方法。

访问者模式是一种设计模式，它允许你在类层次结构已经定义之后，为其添加功能。让我们看一个典型的访问者模式实现。以下是定义访问者的接口。该代码包含循环类引用，无法在 REPL 中运行。因此，先给出代码，然后对代码进行逐步讲解。

```
trait OCarVisitor:
def visit(wheel: OWheel): Unit
def visit(engine: OEngine): Unit
def visit(body: OBody): Unit
def visit(car: OCar): Unit
trait OCarElement:
def accept(visitor: OCarVisitor): Unit
class OWheel(val name: String) extends OCarElement:
def accept(visitor: OCarVisitor) = visitor.visit(this)
class OEngine extends OCarElement:
def accept(visitor: OCarVisitor) = visitor.visit(this)
class OBody extends OCarElement:
def accept(visitor: OCarVisitor) = visitor.visit(this)
class OCar extends OCarElement:
val elements = List(new OEngine, new OBody, new OWheel("FR"),
new OWheel("FL"), new OWheel("RR"), new OWheel("RL"))
def accept(visitor: OCarVisitor) = (this :: elements).foreach(_.accept(visitor))
```

库作者必须考虑可扩展性并实现访问者模式。还要注意，在访问者模式中，类层次结构是固定的，因为访问者必须实现一个接口，该接口定义了访问者可以处理的所有可能的类。

```
trait OCarVisitor:
def visit(wheel: OWheel): Unit
def visit(engine: OEngine): Unit
def visit(body: OBody): Unit
def visit(car: OCar): Unit
```

每个元素都派生自一个特质，该特质创建了一个契约，要求该类实现 `accept` 方法。

```
trait OCarElement:
def accept(visitor: OCarVisitor): Unit
```

你实现每个子类并实现 `accept` 方法。

```
class OWheel(val name: String) extends OCarElement:
def accept(visitor: OCarVisitor) = visitor.visit(this)
class OEngine extends OCarElement:
def accept(visitor: OCarVisitor) = visitor.visit(this)
class OBody extends OCarElement:
def accept(visitor: OCarVisitor) = visitor.visit(this)
class OCar extends OCarElement:
val elements = List(new OEngine, new OBody, new OWheel("FR"),
new OWheel("FL"), new OWheel("RR"), new OWheel("RL"))
def accept(visitor: OCarVisitor) = (this :: elements).foreach(_.accept(visitor))
```

这有大量的样板代码。^((31)) 此外，它违反了 OOP 的数据隐藏原则，因为访问者必须访问它所访问的每个元素中的某些数据。让我们比较一下模式匹配的版本：

```
trait CarElement:
case class Wheel(name: String) extends CarElement
case class Engine() extends CarElement
case class Body() extends CarElement
case class Car(elements: List[CarElement]) extends CarElement
```

代码更简洁，因为没有样板式的 `accept` 方法。让我们看看当你想遍历对象层次结构时该怎么做：

```
def doSomething(in: CarElement): Unit =
in match
case Wheel(name) =>
case Engine() =>
case Body() =>
case Car(e) => e.foreach(doSomething)
```

请注意，所有与访问者模式解释相关的代码都需要在 REPL 中按相同顺序执行。如果你关闭并重新打开它，你将丢失所有声明，示例可能无法工作。如果你使用 Scastie 或其他在线工具，你只需将所有代码添加到同一个编辑器中。

## 总结

在本章中，你探索了模式匹配，并看到了模式匹配如何为表达复杂逻辑提供强大的声明式语法。模式匹配为 Java 的测试/转换范式提供了一种优秀且类型安全的替代方案。将模式匹配与 case 类和提取器结合使用，提供了一种遍历对象层次结构的强大方法，并且是访问者模式的绝佳替代方案。而且，由于模式是函数和对象，它们可以作为参数传递，并用于任何使用函数的地方。

在下一章中，你将探索集合。Scala 集合库是 Scala 生态系统中最重要的库。

脚注 1   2   3   4   5   6



