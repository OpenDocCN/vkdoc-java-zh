# 19. For 推导式

上一章重点介绍了单子以及 `map` 和 `flatMap` 函数。在本章中，我们将重点放在 `flatMap` 行为上。具体来说，我们将研究如何在最终产生结果之前链式调用 `flatMap` 函数。For 推导式实际上在底层使用了 `flatMap`，因此我们将详细研究它们之间的关系，并解释 for 推导式是如何工作的。

## 我们上次讲到哪了

希望你现在已经熟悉了 `flatMap` 的概念。我们在集合类和 `Option` 上都看过它的用法。回想一下，我们使用 `flatMap` 来映射那些可能存在于数据库中也可能不存在的客户姓名。通过这样做，我们可以对客户的购物车价值求和。

```
customers
.flatMap(name => database.find(name))
.map(customer => customer.total)
.sum
```

现在，假设我们想为客户生成一个运输标签。我们可以在仓库中查找一个客户，如果他们有街道地址和邮政编码，我们就可以生成一个运输标签。

注意事项如下：

1.  客户可能存在于仓库中，也可能不存在。
2.  给定的客户可能有地址对象，也可能没有。
3.  地址对象必须包含 `street`，但可能包含也可能不包含 `postcode`。

因此，要生成一个标签，我们需要：

1.  按姓名查找客户（可能存在也可能不存在）。
2.  获取客户的地址（也可能存在也可能不存在）。
3.  根据地址，从中获取运输信息。（我们可以预期一个 `Address` 对象包含街道地址，但它可能有也可能没有邮政编码。）



## 使用空值检查

如果我们通过返回空值来表达可选性来实现此功能，我们将被迫进行大量的空值检查。我们有四位客户：Albert、Beatriz、Carol 和 Sherlock。Albert 有地址但没有邮政编码，Beatriz 没有提供地址，而最后两位拥有完整的地址信息。

```
val customers = new CustomerSet()
val address1 = Some(Address("1a Bridge St", None))
val address2 = Some(new Address("2 Short Road", Some("AL1 2PY")))
val address3 = Some(new Address("221b Baker St", Some("NW1")))
customers.add(new Customer("Albert", address1))
customers.add(new Customer("Beatriz", None))
customers.add(new Customer("Carol", address2))
customers.add(new Customer("Sherlock", address3))
```

给定一个客户列表，我们可以尝试创建运输标签。如你所见，以下列表包含数据库中不存在的人员。

```
val all = Set("Albert", "Beatriz", "Carol", "Dave", "Erin", "Sherlock")
```

接下来，我们创建一个函数来返回运输标签列表，并将它们收集到一个可变集合中。对于列表中的每个名字，我们尝试在数据库中查找该客户（使用 `customers.find`）。由于这可能返回 `null`，我们必须在获取其地址之前检查返回值是否为 `null`。

获取地址可能返回 `null`，因此我们必须在获取邮政编码之前再次检查 `null`。一旦我们检查了邮政编码不为 `null`，我们最终可以调用一个方法（`shippingLabel`）来创建标签并将其添加到集合中。如果运行此代码，只有 Carol 和 Sherlock 能通过所有的空值检查。

```
import scala.collections._
val all = Set("Albert", "Beatriz", "Carol", "Dave", "Erin", "Sherlock")
def generateShippingLabels() = {
val labels = mutable.Set[String]()
all.foreach { name =>
val customer: Customer = customers.find(name)
if (customer != null) {
val address: Address = customer.address
if (address != null) {
val postcode: String = address.postcode
if (postcode != null) {
labels.add(
shippingLabel(customer.name, address.street, postcode))
}
}
}
}
labels
}
def shippingLabel(name: String, street: String, postcode: String) = {
"Ship to:\n" + "========\n" + name + "\n" + street + "\n" + postcode
}
```

## 将 `FlatMap` 与 `Option` 结合使用

如果我们将 `Option` 作为返回类型，而不是为不存在的客户返回 `null`，那么我们可以使用 `flatMap` 来简化代码。

```
1   def generateShippingLabel(): Set[String] = {
2     all.flatMap {
3       name => customers.find(name).flatMap {
4         customer => customer.address.flatMap {
5           address => address.postcode.map {
6             postcode => {
7               shippingLabel(customer.name, address.street, postcode)
8             }
9           }
10         }
11       }
12     }
13   }

15   def shippingLabel(name: String, street: String, postcode: String) = {
16     "Ship to:\n" + "========\n" + name + "\n" + street + "\n" + postcode
17   }
```

我们以与之前相同的方式开始，枚举列表中的每个名字，并对每个名字在数据库上调用 `find`。我们使用 `flatMap` 来执行此操作，因为我们正在从单个客户名称（`String`）转换为一个单子（`Option`）。

你可以将 Option 视为一个包含一个元素的列表（要么是 `Some`，要么是 `None`），因此我们正在进行一种“一对多”的转换。正如我们在第 17 章的 `flatMap` 部分所见，这意味着我们稍后需要将“多个”结果展平回“一个”，因此使用了 `flatMap`。

在初始的 `flatMap`（我们在数据库中查找客户）之后，我们对结果进行 `flatMap`。如果没有找到客户，则不会继续执行。因此，在第 4 行，我们可以确信客户确实存在，并可以继续获取其地址。由于地址是可选的，我们可以再次进行 `flatMap`，如果客户没有地址则退出。

在第 5 行，我们可以请求客户的邮政编码。邮政编码是可选的，因此只有当我们有邮政编码时，我们才将其（以及地址详细信息）转换为运输标签。`map` 调用为我们处理了这一点；请记住，这里的 `map` 仅在我们有值（即邮政编码是 `Some` 的实例）时才应用函数（`shippingLabel`）。

请注意，我们不需要创建一个可变集合来存储运输标签。任何像 `map` 或 `flatMap` 这样的转换函数都会生成一个包含转换结果的新集合。因此，第 7 行对 `map` 的最终调用会将运输标签放入一个新创建的集合中。最后一点说明：结果集合的类型是 `String`，因为 `generateShippingLabel` 方法返回一个 `String`。



## For 推导式的工作原理

当你执行一个常规的 for 循环时，编译器会将其转换（或脱糖）为对 `foreach` 的方法调用。

```
for (i <- 0 to 5) {
println(i)
}
// 脱糖后为
(0 to 5).foreach(println)
```

嵌套循环的脱糖形式如下：

```
for (i <- 0 to 5; j <- 0 to 5) {
println(i + " " + j)
}
// 脱糖后为
(0 to 5).foreach { i =>
(0 to 5).foreach { j =>
println(i + " " + j)
}
}
```

如果你使用带 `yield` 的 `for`（即 for 推导式），编译器会进行不同的处理。

```
for (i <- 0 to 5) yield {
i + 2
}
```

`yield` 用于返回值。不带 `yield` 的 `for`，虽然是一个表达式，但会返回 `Unit`。这是因为 `foreach` 方法返回 `Unit`。而带 `yield` 的 `for` 会返回 `yield` 块中的内容。它会被转换为对 `map` 的调用，而不是 `foreach`。因此，上述代码的脱糖形式如下：

```
// "for (i <- 0 to 5) yield i + 2" 的脱糖形式
(0 to 5).map(i => i + 2)
```

它将一个数字序列（0 到 5）映射为另一个数字序列（2 到 7）。

重要的是要认识到，`yield` 块中的内容代表了传递给 `map` 的函数。`map` 本身操作的是 `for` 部分中的内容（即 `for (i <- 0 to 5)`）。当我们像下面这样重新格式化示例时，可能更容易理解：

```
for {
i <- 0 to 5           // map 操作在这个集合上
} yield {
i + 2                 // 传递给 map 的函数
}
```

当我们在括号和 `yield` 之间出现嵌套时，情况会变得更有趣。

```
val x: Seq[(Int, Int)] = for {
i <- 0 to 5
j <- 0 to 5
} yield {
(i, j)
}
```

花括号还是圆括号？

注意到我在一些示例中使用了花括号而不是圆括号吗？对于嵌套的 for 循环或带有 `yield` 块的循环，使用花括号是一种更常见的风格。

这将像之前一样执行嵌套循环，但不会转换为嵌套的 `foreach` 调用，而是转换为 `flatMap` 调用后跟一个 `map`。同样，最后的 `map` 用于使用 `yield` 块中的内容来转换结果。

```
// 脱糖后
val x: Seq[(Int, Int)] = (0 to 5).flatMap {
i => (0 to 5).map {
j => (i, j)
}
}
```

这与之前完全相同；`yield` 块提供了要应用于映射函数的函数，而它映射的内容由 `for` 表达式决定。在这个例子中，我们将两个 0 到 5 的列表映射到一个元组集合，表示它们的笛卡尔积。

```
Seq((0,0), (0,1), (0,2), (0,3), (0,4), (0,5),
(1,0), (1,1), (1,2), (1,3), (1,4), (1,5),
(2,0), (2,1), (2,2), (2,3), (2,4), (2,5),
(3,0), (3,1), (3,2), (3,3), (3,4), (3,5),
(4,0), (4,1), (4,2), (4,3), (4,4), (4,5),
(5,0), (5,1), (5,2), (5,3), (5,4), (5,5))
```

如果我们分解这个过程并逐步进行，我们可以看到是如何得到脱糖形式的。我们从两个数字序列开始：`a` 和 `b`。

```
val a = (0 to 5)
val b = (0 to 5)
```

当我们映射这些集合时，会得到一个集合的集合。最后的 `map` 返回一个元组，因此返回类型是元组的序列的序列。

```
val x: Seq[Seq[(Int, Int)]] = a.map(i => b.map(j => (i, j)))
```

要将这些扁平化为一个元组集合，我们必须展平这两个集合，这正是 `flatMap` 所做的。因此，尽管我们可以执行以下操作，但直接调用 `flatMap` 要简单得多。

```
val x: Seq[(Int, Int)] = a.map(i => b.map(j => (i, j))).flatten
// 等价于
val x: Seq[(Int, Int)] = a.flatMap(i => b.map(j => (i, j)))
```

## 最后，使用 For 推导式生成货运标签

所有这些对我们的货运标签示例意味着什么？我们可以将链式的 `flatMap` 调用转换为使用 for 推导式，从而使整个代码更整洁。我们从一系列链式的 `flatMap` 调用开始。

```
def generateShippingLabel_FlatMapClosingOverVariables(): Set[String] = {
all.flatMap {
name => customers.find(name).flatMap {
customer => customer.address.flatMap {
address => address.postcode.map {
postcode => shippingLabel(name, address.street, postcode)
}
}
}
}
}
```

转换为 for 推导式后，每个 `flatMap` 调用都作为嵌套表达式放在 `for` 中。最后一个表达式代表 `map` 调用。它的参数（映射函数）就是 `yield` 块中的内容。

```
def generateShippingLabel_ForComprehension(): Set[String] = {
for {
name     <- all                               // <- flatMap
customer <- customers.find(name)              // <- flatMap
address  <- customer.address                  // <- flatMap
postcode <- address.postcode                  // <- map
} yield {
shippingLabel(name, address.street, postcode) // <- map 的参数
}
}
```

这简洁得多。以这种方式呈现时，更容易推理条件语义；如果没有找到 `customer`，它就不会继续执行下一行。如果你想扩展嵌套，只需添加另一行，而不会被噪音或缩进所困扰。

这种语法是声明式的，但模仿了命令式风格。它不会强制你使用特定的实现。也就是说，对于命令式的 `for` 循环，你无法选择循环的执行方式。例如，如果你想并行执行，就必须自己实现并发。

使用这样的声明式方法意味着底层对象负责它们的执行方式，这为你提供了更大的灵活性。请记住，这只是调用了 `flatMap`，而类可以自由地以任何方式实现 `flatMap`。

For 推导式适用于任何单子（monad），如果你实现了单子方法，也可以使用你自己的类。

## 总结

在本章中，我们研究了在打印货运标签的上下文中链式调用 `flatMap`。我们探讨了 for 推导式的工作原理，以及它们如何作为常规 Scala 方法调用的语法糖。具体来说，我们研究了循环和嵌套循环，它们如何等价于调用 `foreach`，带 `yield` 块的 `for` 循环如何转换为映射函数，以及带 `yield` 块的嵌套循环如何转换为 `flatMap` 后跟 `map` 函数。

最后一点正是我们能够将冗长的货运标签示例转换为简洁的 `for` 推导式的原因。

