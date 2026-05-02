# 17. Map 与 FlatMap

在本章中，我们将探讨 Scala 的一些函数式编程特性，特别是无处不在的 `map` 和 `flatMap` 函数。我们关注它们，是因为它们与函数式编程的一个关键特性——单子（monad）的概念密切相关。

## 映射函数

你会在 Scala 的无数类中看到 `map` 函数。它经常在集合的上下文中被描述。像 `List`、`Set` 和 `Map` 这样的类都有它。对于这些类，`map` 会将给定的函数应用于集合中的每个元素，并根据该函数的结果返回一个新的集合。你将某个函数“映射”到集合的每个元素上。

例如，你可以创建一个函数，根据一个人的出生年份计算出他今天的年龄。

```
import java.util.Calendar
def age(birthYear: Int) = {
val currentYear = Calendar.getInstance.get(Calendar.YEAR)
currentYear - birthYear
}
```

我们可以在一个出生年份列表上调用 `map` 函数，传入这个函数来创建一个新的年龄列表。

```
val birthdays = List(1990, 1977, 1984, 1961, 1973)
birthdays.map(age)
```

结果将是一个年龄列表。假设在 2017 年运行，我们可以将 1990 年转换为 27 岁，例如。

```
res0: List[Int] = List(25, 38, 31, 54, 42)
```

作为一个高阶函数，你也可以将函数内联写成 lambda 表达式，像这样：

```
birthdays.map(year => Calendar.getInstance.get(Calendar.YEAR) - year)
```

使用下划线作为 lambda 参数的简写，它看起来像这样：

```
birthdays.map(Calendar.getInstance.get(Calendar.YEAR) - _)
```



### 它就像 `foreach`

所以，`map` 是一个转换函数。对于集合而言，它会遍历集合并应用某个函数，就像 `foreach` 所做的那样。区别在于，与 `foreach` 不同，`map` 会将函数的返回值收集到一个新集合中，然后返回该集合。

手动实现一个映射函数是微不足道的。例如，我们可以创建一个类 `Mappable`，它接收一些类型为 `A` 的元素，并创建一个 `map` 函数。

```
class MappableA {
def mapB: List[B] = {
???
}
}
```

`map` 的参数是一个将类型 `A` 转换为类型 `B` 的函数；它接收一个 `A` 并返回一个 `B`。我将其完整写成了 `Function1` 类型，这等价于 Java 的 `java.util.function.Function` 类。我们也可以使用 Scala 的简写语法来编写它，编译器会为我们进行转换。

```
def mapB: List[B] = ...
```

然后，问题就简化为创建一个新集合，将每个元素作为参数调用该函数（使用 `apply`）。我们将结果存储到新集合中，最后返回它。

```
class MappableA {
def mapB: List[B] = {
val result = collection.mutable.MutableList[B]()
elements.foreach {
result += f.apply(_)
}
result.toList
}
}
```

我们可以通过创建一个数字列表来测试它，通过创建 `Mappable` 的新实例使其变得“可映射”，并调用一个简单的匿名函数（将输入加倍）作为 `map` 的参数。

```
object Example extends App {
val numbers: List[Int] = List(1, 2, 54, 4, 12, 43, 54, 23, 34)
val mappable: Mappable[Int] = new Mappable(numbers)
val result = mappable.map(_ * 2)
println(result)
}
```

输出结果如下：

```
List(2, 4, 108, 8, 24, 86, 108, 46, 68)
```

### 递归

这是一个相当典型的迭代实现；更符合 Scala 风格的实现会使用递归。有关一些示例，请参见附录 A 中的代码清单。

## FlatMap

你经常会在看到 `map` 函数的地方看到 `flatMap` 函数。对于集合而言，它非常相似，都是将一个函数映射到集合上，并将结果存储在新集合中，但有几个不同之处。

*   它仍然进行转换，但这次函数应用的是**一对多**的转换。它像之前一样接收单个参数，但返回多个值。
*   因此，结果最终会成为一个集合的集合，所以 `flatMap` 还会将结果**扁平化**，得到一个单一的集合。

所以，

*   对于给定的 `A` 类型集合，`map` 函数对每个元素应用一个函数，将 `A` 转换为 `B`。结果是 `B` 类型的集合（即 `List[B]`）。
*   对于给定的 `A` 类型集合，`flatMap` 函数对每个元素应用一个函数，将 `A` 转换为 `B` 类型的集合。这会产生一个 `B` 类型集合的集合（即 `List[List[B]]`），然后将其扁平化为一个单一的 `B` 类型集合（即 `List[B]`）。

假设我们想要一个映射函数，返回一个人的年龄上下浮动一岁。所以，如果我们认为一个人是 38 岁，我们会返回一个包含 37、38、39 的列表。

```
import java.util.Calendar
def ageEitherSide(birthYear: Int): List[Int] = {
val today = Calendar.getInstance.get(Calendar.YEAR)
List(today - 1 - birthYear, today - birthYear, today + 1 - birthYear)
}
```

与之前的示例相比，签名已更改为返回 `List[Int]` 而不是仅仅一个 `Int`。如果我们将生日年份列表传递给 `map` 函数，我们会得到一个列表的列表（下面的 `res0`）。

```
val birthdays = List(1990, 1977, 1984)
birthdays.map(ageEitherSide)
scala> birthdays.map(ageEitherSide)
res0: List[List[Int]] =
List(List(26, 27, 28), List(39, 40, 41), List(32, 33, 34))
```

然而，如果我们将其传递给 `flatMap` 函数，我们会得到一个扁平化的列表。它先映射，然后扁平化。

```
scala> birthdays.flatMap(ageEitherSide)
res1: List[Int] = List(26, 27, 28, 39, 40, 41, 32, 33, 34)
```

如果你想编写自己的 `flatMap` 版本，它可能看起来像这样（注意函数的返回类型）：

```
class FlatMappableA {
def flatMapB: List[B] = {
val result = collection.mutable.MutableList[B]()
elements.foreach {
f.apply(_).foreach {
result += _
}
}
result.toList
}
}
```

第一个循环将枚举集合中的元素，并对每个元素应用该函数。因为这个函数本身返回一个列表，所以需要另一个循环来枚举这些列表中的每一个，将它们添加到结果集合中。这就是将函数结果扁平化的部分。

为了测试它，让我们首先创建一个函数，该函数从 `Int` 转换为 `Int` 的集合。它返回零到参数之间的所有奇数。

```
def oddNumbersTo(end: Int): List[Int] = {
val odds = collection.mutable.MutableList[Int]()
for (i <- 0 to end) {
if (i % 2 != 0) odds += i
}
odds.toList
}
```

然后，我们只需用几个数字创建我们类的一个实例。调用 `flatMap`，你会看到从 0 到 1、0 到 2 和 0 到 10 的所有奇数都被收集到一个列表中。

```
object Example {
def main(args: Array[String]) {
val mappable = new FlatMappable(1, 2, 10)
val result = mappable.flatMap(oddNumbersTo)
println(result)
}
}
```

输出结果如下：

```
List(1, 1, 1, 3, 5, 7, 9)
```

## 不仅限于集合

我们已经了解了 `map` 和 `flatMap` 如何用于集合，但它们也存在于许多其他类上。更一般地说，`map` 和 `flatMap` 作用于所谓的**单子**（monads）上。事实上，拥有 `map` 和 `flatMap` 行为是单子的定义性特征之一。

那么，单子到底是什么？我们接下来会探讨。

