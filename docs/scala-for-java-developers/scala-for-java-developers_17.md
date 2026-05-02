# 14. 模拟函数调用

Scala 提供了使方法调用看起来像常规函数调用的机制。它使用特殊方法 `apply` 和 `update` 来实现一种简写调用符号，可以减少代码的杂乱。

## `apply` 方法

`apply` 方法提供了一种在类上调用方法的简写方式。因此，正如我们所见，你可以将它们用作工厂风格的创建方法，例如，给定某个类，比如我们的老朋友 `Customer`：

```
class Customer(name: String, address: String)
```

……你可以向其伴生对象添加一个 `apply` 方法。

```
object Customer {
def apply(name: String, address: String) = new Customer(name, address)
}
```

然后，你可以直接调用该方法，或者省略 `apply` 部分，此时 Scala 会查找与你参数列表匹配的 `apply` 方法并调用它。

```
Customer.apply("Bob Fossil", "1 London Road")
Customer("Rob Randal", "14 The Arches")
```

你也可以有多个 `apply` 方法。例如，我们可以创建另一个方法来为我们的客户设置默认地址字段。

```
def apply(name: String) = new Customer(name, "No known address")
Customer("Cuthbert Colbert")
```

不过，你不必非得将 `apply` 方法用作工厂方法。大多数人最终使用它们是为了使类的 API 更简洁。这是 Scala 如何帮助使 API 更具表现力的关键。如果让你的类的默认行为是创建一个实例是有意义的，那很好，但你也可以使用 `apply` 让其他方法看起来像函数调用。

到目前为止，我们一直在单例对象（`object Customer`）上使用 `apply` 方法并省略 `apply`，但你也可以在类上定义 `apply` 方法，并在实例变量上调用它。

例如，我们可以创建一个名为 `Adder` 的类，并在一个实例上调用 `apply` 方法来将两个数字相加。

```
// scala
class Adder {
def apply(a: Int, b: Int) = a + b
}
val add = new Adder()
add.apply(1, 3)
```

但我们同样可以轻松地省略它，看起来就像我们在调用一个函数，尽管我们实际上是在调用一个实例变量上的方法。

```
val add = new Adder()
add(1, 3)
```

另一个例子是访问数组值。假设我们有一个罗马数字数组。

```
val numerals = Array("I", "II", "III", "IV", "V", "VI", "VII")
```

要使用索引访问数组，语法是使用圆括号而不是方括号。

```
numerals(5)               // 得到 "VI"
```

因此，在循环中使用索引，我们可以这样做来打印整个数组：

```
for (i <- 0 to numerals.length - 1)
println(i + " = " + numerals(i))
```

这里有趣的是，数组上有一个接受 `Int` 的 `apply` 方法。我们本可以这样写：

```
numerals.apply(5) // 得到 "VI"
for (i <- 0 to numerals.length - 1)
println(i + " = " + numerals.apply(i))
```

看起来像是访问数组的语言语法，实际上只是一个常规的方法调用。Scala 模拟了它。

## `update` 方法

赋值的工作方式完全相同。例如，`numerals(2) = "ii"` 实际上调用了 `Array` 类上一个名为 `update` 的特殊方法（`def update(i: Int, x: T)`）。

```
numerals(2) = "ii"
```

如果 Scala 看到赋值操作符并且能找到带有适当参数的 `update` 方法，它就会将赋值转换为方法调用。

我们可以将这个想法应用到我们自己的类上，使 API 感觉更像语言语法。假设我们从事电话业务，该业务的一部分是维护客户电话号码的目录。

我们可以创建一个集合类来包含我们的目录，并初始化它以保存四个火枪手的电话号码，如下所示：

```
class Directory {
val numbers = scala.collection.mutable.Map(
"Athos"      -> "7781 456782",
"Aramis"     -> "7781 823422",
"Porthos"    -> "1471 342383",
"D`Artagnan" -> "7715 632982"
)
}
```

如果我们决定目录的简写或默认行为应该是返回客户的电话号码，我们可以按如下方式实现 `apply` 方法：

```
def apply(name: String) = {
numbers.get(name)
}
```

这样，在创建目录实例后，我们可以像这样打印 Athos 的号码：

```
val yellowPages = new Directory()
println("Athos's telephone number : " + yellowPages("Athos"))
```

然后，如果我们想更新一个号码，我们可以实现一个更新方法并直接调用它。Scala 的赋值简写意味着，如果我们实际上将方法命名为 `update`，我们就可以使用赋值操作符，它会为我们调用 `update` 方法。

因此，我们添加一个 update 方法。

```
def update(name: String, number: String) = {
numbers.update(name, number)
}
```

然后我们可以像这样调用它来更新一个号码：

```
yellowPages.update("Athos", "Unlisted")
```

利用简写符号，你也可以使用赋值。

```
yellowPages("Athos") = "Unlisted"
```



### 多个 `update` 方法

我们还可以添加第二个 update 方法，这次将 `Int` 作为第一个参数。

```
def update(areaCode: Int, newAreaCode: String) = {
???
}
```

假设我们希望它能更新所有条目中的区号。我们可以枚举每个条目，找出哪些号码以第一个参数中的区号开头。对于匹配的条目，我们返回原始映射并更新该条目。

```
def update(areaCode: Int, newAreaCode: String) = {
numbers.foreach(entry => {
if (entry._2.startsWith(areaCode.toString))
numbers(entry._1) = entry._2.replace(areaCode.toString, newAreaCode)
})
}
```

`_1` 和 `_2` 是 Scala 中用于访问所谓元组（tuple）的符号。这是一种简单的数据结构，我们用它来将（在本例中）Java 中的 `Map.Entry` 视为单个变量。`_1` 和 `_2` 是方法调用，分别让我们访问键和值。实际上，元组的用途比这更广泛，并不仅限于映射。我们这里使用的是包含两个元素的元组（`Tuple2`），但你可以拥有最多包含 22 个元素的元组（`Tuple22`）。

我们可以使用简写的赋值语法来调用新的 `update` 方法，如下所示：

```
object DirectoryExampleAlternativeUpdateMethod extends App {
val yellowPages = new Directory
println(yellowPages)
yellowPages(7781) = "7555"
println(yellowPages)
}
```

这样做的结果是，Athos 和 Aramis 的区号都会被更新。

### `update` 的多个参数

你可以在 `update` 方法中拥有任意数量的参数，但只有最后一个参数会被用作更新后的值。这很合理，因为赋值运算符的右侧只能有一个值。

参数列表的其余部分用于选择合适的 update 方法。因此，如果你有另一个包含三个参数（`areaCode`、`anotherArgument` 和 `newAreaCode`）的方法，如下所示：

```
def update(areaCode: Int, another: String, newAreaCode: String) = ???
```

……那么类型将用于确定在赋值时应调用哪个 `update` 方法。

```
yellowPages(7998) = "7668"
yellowPages(7998, "another argument") = "???"
```

## 总结

在本章中，我们进一步了解了 `apply` 方法：它不仅用于工厂风格的创建方法，还可用于构建丰富的 API。通过让方法调用看起来像函数调用，你可以使客户端代码更加简洁。

我们还了解了相关的 `update` 方法是如何工作的，以及如何利用赋值运算符编写 API 并实现自定义的更新行为。

