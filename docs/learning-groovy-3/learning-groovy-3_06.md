# 5. 从 Java 迁移而来

由于大多数读者已经熟悉 Java，因此将常见的 Java 惯用法与 Groovy 中的等价写法进行比较会很有帮助。

## 默认方法参数值

从 Java 迁移过来时，可能会让你感到惊讶的一点是，在 Groovy 中你可以为方法参数提供默认值。例如，假设你有一个名为 `fly` 的方法，它带有一个名为 `text` 的参数：

```
1   def fly(String text = "flying") {println text}
```

### 注意

以这种方式使用 `"def"` 关键字定义了一个返回类型为 `java.lang.Object` 的方法。

这实际上会在幕后（从 Java 的角度来看）创建两个方法：

```
1   def  fly() {println "flying"}
2   def  fly(String text) {println text}
```

只要生成的方法不与其它现有方法冲突，这就可以与任意数量的参数一起使用。根据我们前面的示例，我们可以按如下方式测试它：

```
fly() // 输出: flying
fly('Groovy') // 输出: Groovy
```

## Equals、HashCode 等

Groovy 在 `"groovy.transform"` 包中有许多注解，用于实现 **AST 转换**（抽象语法树转换）。换句话说，当你使用它们来注解你的类时，它们会在编译时通过将常用的代码（通常称为“样板代码”）添加到你的类的字节码中来简化开发。

在 Java 中你经常需要做的一项繁琐任务是为一个类创建 `equals` 和 `hashCode` 方法。为此，Groovy 添加了 `@EqualsAndHashCode` 注解。只需将其添加到你的类的顶部（就在 `class` 关键字之前）即可完成。

同样，你通常希望为一个类的所有字段创建一个构造函数。为此，Groovy 提供了 `@TupleConstructor`。它使用你字段定义的顺序来定义一个带有参数的构造函数，用于初始化这些字段。只需将其添加到你的类定义之前即可。

还有一个 `@ToString` 注解，你可以将其添加到类定义之前，用于自动为你的类创建 `toString()` 方法。你还可以配置它来包含或排除某些字段。

最后，如果你希望你的类拥有所有这些功能，只需使用 `@Canonical` 注解即可。你可以在 `groovyConsole` 中使用 Ctrl+T 来查看这如何影响语法树。

```
1   import groovy.transform.*
2   @Canonical class Dragon {def name}
3   println new Dragon("Smaug")
4   // 打印: Dragon(Smaug)
5   assert new Dragon("").equals(new Dragon(""))
```

### ![../images/426440_2_En_5_Chapter/426440_2_En_5_Figa_HTML.gif](img/426440_2_En_5_Figa_HTML.gif) 练习

使用这些注解创建你自己的具有多个属性的类。

## 正则表达式模式匹配

Groovy 极大地简化了使用正则表达式（regex）模式匹配文本的过程。

在 Java 中，你必须使用 `java.util.regex.Pattern` 类，创建一个实例，然后创建一个 Matcher；而在 Groovy 中，这一切都可以简化为一行代码。

按照惯例，你可以用斜杠将你的正则表达式括起来。这允许你使用特殊的正则表达式语法，而无需使用繁琐的双反斜杠。例如，要判断一个字符串是否是电子邮件地址（假设有一个名为 `email` 的变量）：

```
1   def  isEmail = email ==~ /[\w.]+@[\w.]+/
```

在 Java 中等价的代码是：

```
1   Pattern patt = Pattern.compile("[\\w.]+@[\\w.]+");
2  boolean  isEmail = patt.matches(email);
```

Groovy 中还有一个用于创建匹配器的运算符：

```
1   def email = 'mailto:adam@email.com'
2   def  mr = email =~ /[\w.]+@[\w.]+/
3   if (mr.find()) println mr.group()
```

这允许你在字符串中查找正则表达式，并从正则表达式中获取子组。

### ![../images/426440_2_En_5_Chapter/426440_2_En_5_Figb_HTML.gif](img/426440_2_En_5_Figb_HTML.gif) 练习

在 Groovy 中创建一个更好的用于验证电子邮件的正则表达式。

## 缺失的 Java 语法

由于 Groovy 语法的特性以及多年来 Java 语法的一些新增内容，Groovy 在 3.0 版本之前“缺失”了一些功能。但是，有其它方法可以完成相同的事情。

在 Groovy 3.0 之前的版本中，数组可能有点难以处理，因为用于创建值数组的 Java 语法无法编译。例如，以下代码在 Groovy 2.5 中无法编译：

```
1   String[] array = new  String[] {"foo", "bar"};
```

相反，你应该使用以下语法（如果你必须使用数组）：

```
1   String[] array = ['foo', 'bar'].toArray()
```

在 Groovy 中，有几种遍历列表、数组或集合的选项：使用 `in` 关键字、Java 风格，或者使用带有闭包的 `each` 方法。例如：

```
1   for (String item : array) println(item)
2   for (item in array) println(item)
3   array.each { item -> println(item) }
```

## 可选的分号

由于分号在 Groovy 中是可选的，当你习惯了 Java 时，这有时会导致行尾混淆。通常这不是问题，但是当连续调用多个方法时（例如，使用流畅的 API），这可能会导致问题。在这种情况下，你需要用未闭合的运算符（例如点号）结束每一行。

例如，让我们看一个任意的流畅 API：

```
1   class Pie  {
2     def  bake() { this }
3     def  make() { this }
4     def  eat() { this }
5   }
6   def pie = new Pie().
7           make().
8           bake().
9           eat()
```

如果你使用典型的 Java 语法，它可能会在旧版本的 Groovy 中导致编译错误（现在不再如此）：

```
1   def pie = new Pie() // Groovy 解释为行结束
2           .make() // 嗯？这是什么？
```

## 有时可选的括号

Groovy 在某些情况下允许省略括号以简化语法。两种主要情况如下：当调用带有一个或多个参数的方法时，以及当闭包是最后一个（或唯一一个）参数时。例如，以下代码在 Groovy 中可以正常工作：

```
1   // 调用一个名为 "doStuff" 的方法，参数为 1
2   doStuff 1
3   // 调用 "doStuff"，带有三个参数：
4   doStuff 1, 2, 3
5   // 在 "list" 上调用 "each"，带有一个闭包：
6   list.each { item -> doStuff(item) }
```



## 泛型在哪里？

Groovy 支持泛型语法，但默认情况下并不强制检查。因此，你可能在 Groovy 代码中看不到很多泛型。例如，以下代码在 Groovy 中可以正常运行：

```
1   List nums = [1, 2, 3.1415, 'pie']
```

但是，如果你在类或方法上添加 `@CompileStatic` 或 `@TypeChecked` 注解，Groovy 就会强制检查泛型。例如：

```
1   import groovy.transform.*
2   @CompileStatic
3   class Foo {
4       List nums = [1, 2, 3.1415] // 错误
5   }
```

这会导致编译错误：`"[Static type checking] - Incompatible generic argument types. Cannot assign java.util.List <java.lang.Number> to: java.util.List <Integer>"`。由于 `3.1415` 在 Groovy 中会变成 `java.math.BigDecimal`，因此列表的泛型类型会被自动确定为 `java.lang.Number`。

## Groovy 中的数字

这个讨论引出了小数，在 Groovy 中默认使用 `BigDecimal`。这让你可以在进行数学运算时避免舍入误差。

如果你想使用 `double` 或 `float`，只需在数字后面分别加上 `d` 或 `f`（就像在 Java 中一样）。例如：

```
1   def  pie = 3.141592d
```

### ![../images/426440_2_En_5_Chapter/426440_2_En_5_Figc_HTML.gif](img/426440_2_En_5_Figc_HTML.gif) 练习

尝试将不同的数字类型相乘，并确定结果的类。

## 布尔值解析

由于 Groovy 与 Java 非常相似，但又不是 Java，因此很容易被它们之间的差异搞糊涂。其中两个容易混淆的领域是*布尔值解析*（也称为“Groovy 真值”）和*Map 语法糖*。

Groovy 在布尔表达式中接受的内容要宽松得多。例如，`null`、空集合、空字符串和零都被视为 `false`。因此，以下代码会打印四次 `"true"`：

```
1   if ("foo") println("true")
2   if (!"") println("true")
3   if (42) println("true")
4   if (! 0) println("true")
```

### ![../images/426440_2_En_5_Chapter/426440_2_En_5_Figd_HTML.gif](img/426440_2_En_5_Figd_HTML.gif) 提示

这有时被称为“Groovy 真值”。

## Map 语法

Groovy 为 Map 提供的语法糖允许你直接使用字符串键，这通常非常有用。但是，当尝试使用 Groovy 的属性访问语法糖（`.class` 指的是键值，而不是 `getClass()`）来获取 Map 的类类型时，这可能会引起混淆。因此，你应该直接使用 `getClass()` 方法。

当你尝试使用变量作为键时，这也可能引起混淆。在这种情况下，你需要用括号将变量括起来。例如：

```
1   def  foo = "key"
2   def  bar = 2
3   def map = [(foo): bar]
```

如果没有括号，`foo` 会被解析为字符串 `"foo"`。有了括号，字符串 `"key"` 将被用作映射到值 2 的键。

## 总结

在本章中，你学习了以下 Groovy 特性：

*   你可以为方法提供默认参数值。
*   `groovy.transform` 包中各种简化开发的注解。
*   正则表达式如何内置于 Groovy 中。
*   在 Groovy 中定义数组的不同方式。
*   如何编写多行语句时使用未闭合的操作符。
*   Groovy 默认使用 `BigDecimal` 处理非整数。
*   Groovy 真值。
*   你可以在 Map 语法中使用变量键。

