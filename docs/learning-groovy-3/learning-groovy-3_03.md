# 2. Groovy 101

在本章中，我们将介绍 Groovy 的基础知识、Groovy 的历史以及使用 Groovy 的优势。



## 什么是 Groovy？

*Groovy* 是一种为 JVM（Java 虚拟机）构建的灵活开源语言，其语法与 Java 类似。它可以动态使用（任何变量都可以持有任何类型的对象），也可以静态类型化（每个变量的类型受到严格限制）；这由你决定。在大多数其他语言中，只能二选一。它支持函数式编程结构，包括头等函数、柯里化等。它还具备多重继承、类型推断和元编程能力。

Groovy 始于 2003 年，部分是为了回应 Ruby。其主要特性是动态类型、元编程（在运行时更改类的能力）以及与 Java 的紧密集成。尽管其最初创建者大约在 2005 年离开了该项目，但多年来社区中的许多开发者^(⁹)为其做出了贡献。过去，多个组织曾支持 Groovy 的开发，与许多开源项目一样，它不能归功于某个人或某家公司。自 2015 年起，它已成为 Apache 软件基金会^(¹⁰)的一个项目。

Groovy 在语法上与 Java 非常相似，因此 Java 开发者通常很容易学习（Java 代码通常也是有效的 Groovy 代码，尤其是在 Groovy 3.0 之后，它增加了对类似 Java 的数组声明以及许多其他 Java 语法特性的支持）。然而，Groovy 拥有许多额外的特性和更宽松的语法规则：闭包、动态类型、元编程（通过 `metaClass`）、可选分号、正则表达式支持、运算符重载、GString 等等。Groovy 在运行时被解释执行，但在 Groovy 2.0 中，增加了编译为字节码并强制执行类型检查的能力。

## 紧凑的语法

Groovy 的语法可以比 Java 紧凑得多。例如，以下标准 Java 5+ 代码应打印出 `"Rod"`：

```
1   for (String it : new  String[] {"Rod", "Carlos", "Chris"})
2           if (it.length() < 4)
3                   System.out.println(it);
```

同样的功能可以用 Groovy 在一行中表达如下：

```
1   ["Rod", "Carlos", "Chris"].findAll{it.size() < 4}.each{println it}
```

它拥有大量内置特性来实现这一点（紧凑的列表定义、JDK 对象的扩展、闭包、可选分号、`println` 方法以及可选括号）。

`findAll` 方法遍历列表，并使用给定的测试条件创建一个仅包含通过测试的对象的新集合。

## 动态 def

Groovy 的一个关键特性是使用 `def` 关键字进行动态类型化。该关键字替代了任何类型定义，从而允许变量为任何类型。这有点像将变量定义为 `Object`，但并不完全相同，因为 Groovy 编译器对 `def` 的处理方式不同。例如，你可以使用 `def`，同时仍然使用 `@TypeChecked` 注解，我们稍后会介绍这一点。

## 列表和映射定义

Groovy 使列表和映射的定义更加简洁和简单。你只需使用方括号（`[]`）和映射符号（`:`）来将键映射到值：

```
1   def  list = [1, 2]
2   def map = [cars: 2, boats: 3]
3   println list.getClass() // java.util.ArrayList
4   println map.getClass() // java.util.LinkedHashMap
```

默认情况下，Groovy 将映射键值解释为字符串，无需引号。当处理键为字符串的映射时，Groovy 允许你使用点符号来引用键（避免使用 `get` 和 `put` 方法），从而使工作更加轻松。例如：

```
1   map.cars = 2
2   map.boats = 3
3   map.planes = 0
4   println map.cars // 2
```

这甚至使得在某些情况下测试时可以使用映射来模拟对象（这将在后面的章节中介绍）。

Groovy 列表甚至重写了左移运算符（`<<`），从而允许以下语法示例：

```
1   def list = []
2   list.add(new  Vampire("Count Dracula", 1897))
3   // 或者
4   list << new  Vampire("Count Dracula", 1897)
5   // 或者
6   list += new  Vampire("Count Dracula", 1897)
```

### ![../images/426440_2_En_2_Chapter/426440_2_En_2_Figa_HTML.gif](img/426440_2_En_2_Figa_HTML.gif) 提示

Groovy 允许重写常见的运算符，如加号和减号。我们将在后面的章节中介绍这一点。

## Groovy GDK

内置的 Groovy 类型（GDK）与 Java 的类型基本相同，只是 Groovy 为每个类添加了大量方法。

例如，`"each"` 方法允许你按如下方式遍历集合：

```
1   ["Java", "Groovy", "Scala"].each{ println it }
```

`println` 和 `print` 方法是在 `System.out` 上调用这些方法的简写形式。我们稍后将更深入地介绍 GDK。

## 一切都是对象

与 Java 不同，原始类型可以随时像对象一样使用，因此看起来没有区别。例如，由于 GDK 为 `Number` 添加了 `times` 方法，你可以执行以下操作：

```
1   100.times { println "hi" }
```

这将打印 `"hi"` 100 次。

## 简便的属性

Groovy 将 Java Bean 的概念提升到了一个全新的水平。你可以使用点符号获取和设置 Bean 属性（如果你不提供，Groovy 会自动为你的类添加 getter 和 setter 方法）。

例如，你可以使用 `person.firstName` 代替 `person.getFirstName()`。设置属性时，你可以直接使用 `person.firstName = 'Bob'` 代替 `person.setFirstName("Bob")`。

### ![../images/426440_2_En_2_Chapter/426440_2_En_2_Figb_HTML.gif](img/426440_2_En_2_Figb_HTML.gif) 提示

与 Java 不同，Groovy 默认总是 public。

你还可以使用 `.properties` 轻松获取 Groovy 中对象的所有属性列表。例如：

```
1   println person.properties
```

### ![../images/426440_2_En_2_Chapter/426440_2_En_2_Figc_HTML.gif](img/426440_2_En_2_Figc_HTML.gif) 提示

使用 `properties` 来探索 Groovy 中你想了解更多信息的某个类。

## GString

Groovy 添加了自己的类，称为 `GString`，它允许你在字符串中嵌入 Groovy 代码。这是使 Groovy 非常简洁易读的另一个特性。每当你在 Groovy 中使用双引号（`""`）时，就会创建一个 GString。

例如，它可以轻松地将一堆变量嵌入到字符串中：

```
1   def os = 'Linux'
2   def cores = 2
3   println("Cores: $cores, OS: $os, Time: ${new Date()}")
```

美元符号 `$` 允许你直接引用变量，而 `${code}` 允许你在 GString 中包含时执行任意的 Groovy 代码。

由于 `String` 在 JDK 中是一个 final 类，因此无法被扩展，所以 GString 不是 String 的子类。尽管在 Groovy 代码中的大多数地方它可以替代 String 使用，但在需要 `java.lang.String` 时，这可能会导致一些问题。

### ![../images/426440_2_En_2_Chapter/426440_2_En_2_Figd_HTML.gif](img/426440_2_En_2_Figd_HTML.gif) 提示

如果你只想使用 `java.lang.String`，你应该使用单引号（`'foo'`）。



## 闭包

*闭包*是 Groovy 中的一段代码块，它可以接受参数并返回值，也可以不接受参数且不返回值。它类似于 Java 8 中的 lambda 表达式或只有一个方法的内部类。闭包在许多方面都非常有用，后续章节将详细介绍。例如，正如你已经看到的，`findAll`、`each` 和 `times` 方法都使用了闭包。

Groovy 闭包有几个隐式变量：

*   `it`——如果闭包只有一个参数，可以隐式地使用 `it` 来引用它。

*   `this`——引用封闭类。

*   `owner`——与 `this` 相同，除非它被嵌套在另一个闭包中。例如，运行以下代码来查看区别：

    ```
    def print1 = {list ->
    list.each{println owner}  //打印第一个闭包
    println owner}       //打印封闭类
    print1([1,2])
    ```

*   `delegate`——通常与 `owner` 相同，但你可以更改它（这允许 `delegate` 的方法在闭包的作用域内）。

闭包可以作为方法参数传递。当这样做时（*并且它是最后一个参数*），闭包可以放在括号外面。例如，使用 `collect` 方法（该方法使用给定的闭包将列表元素转换为新列表）时，可以像下面这样调用它：

```
1   def  list = ['foo','bar']
2   def newList = []
3   list.collect( newList ) {
4     it.toUpperCase()
5   }
6   println newList //   ["FOO",    "BAR"]
```

### ![../images/426440_2_En_2_Chapter/426440_2_En_2_Fige_HTML.gif](img/426440_2_En_2_Fige_HTML.gif) 提示

在 Groovy 中，`return` 关键字完全是可选的。如前所述，方法或闭包只返回其最后一个表达式。

你也可以将闭包赋值给变量，并在之后调用它们：

```
1   def  closr = {x -> x + 1}
2   println( closr(2) ) // 打印 3
```

### ![../images/426440_2_En_2_Chapter/426440_2_En_2_Figf_HTML.gif](img/426440_2_En_2_Figf_HTML.gif) 练习

创建一个闭包，并使用 `getClass()` 打印出其委托的类。

## 更好的 Switch

Groovy 的 `switch` 语句与 Java 的非常相似，不同之处在于它允许更多类型的 `case` 表达式。例如，它允许字符串、列表、范围和类类型：

```
1   def x = 42
2   switch  ( x ) {
3   case "foo":
4       result = "found foo"
5           break
6   case [4, 5, 6]:
7       result = "4 5 or 6"
8       break
9   case 12..30: // 范围
10       result = "12 to 30"
11       break
12   case Integer:
13       result = "was integer"
14       break
15   case Number:
16       result = "was number"
17       break
18   default:
19       result = "default"
20   }
```

在这种情况下，此代码的 `result` 将是 "was integer"。

## 元编程

在 Groovy 中，你可以在运行时向类添加方法，甚至可以向核心 Java 库添加方法。例如，以下代码向 `String` 类添加了 `upper` 方法：

```
1   String.metaClass.upper = { -> toUpperCase() }
```

或者针对单个实例（`str`）：

```
1 def str = "test"
2 str.metaClass.upper = { -> toUpperCase() }
```

`upper` 方法会将 `String` 转换为大写：

```
1   str.upper() == str.toUpperCase()
```

## 静态类型检查

如果你向类添加 `@TypeChecked` 注解，它将强制编译器在编译时进行类型检查。它会为你推断类型，因此你的代码仍然可以是 *Groovy 风格*的。它会根据你的代码推断出最低上界（LUB）类型。例如：

```
1   import   groovy.transform.*
2   @TypeChecked
3   class Foo {
4       int i = 42.0 // 这无法编译
5   }
1   import   groovy.transform.*
2   @TypeChecked
3   class Foo {
4       int i = 42 // 这可以正常工作
5   }
6   new Foo()
```

### ![../images/426440_2_En_2_Chapter/426440_2_En_2_Figg_HTML.gif](img/426440_2_En_2_Figg_HTML.gif) 注意事项

运行时元编程并不总是有效！换句话说，如果你使用 metaClass 添加一个方法并调用它，它将无法编译。

闭包中需要显式类型：`a.collect {`**String** `it -> it.toUpperCase()}`

如果你向类或方法添加 `@CompileStatic` 注解，它将使编译器将你的 Groovy 代码编译成 Java 风格的字节码。当你需要代码具有极高性能（例如，涉及大量整数列表的计算）或出于其他原因需要 Java 字节码时，这将非常有用。生成的字节码几乎与编译后的 Java 代码相同，因此性能也几乎与 Java 相同（与 `@TypeChecked` 不同，后者仍然使用 Groovy 的 MOP——元对象协议）。这两个注解都位于 `groovy.transform` 包中。

例如，你可以在计算斐波那契数列的类上使用 `@CompileStatic`：^(¹¹)

```
1   import   groovy.transform.*
2   @CompileStatic
3   class Foo {
4      void getFibs(int count) {
5         def list = [0, 1] // 打印前 #count 个斐波那契数
6         count.times {
7            print "${list.last()}"
8            list << list.sum()
9            list = list.tail()
10         }
11      }
12   }
```

### ![../images/426440_2_En_2_Chapter/426440_2_En_2_Figh_HTML.gif](img/426440_2_En_2_Figh_HTML.gif) 练习

尝试使用 `@TypeChecked` 和 `def` 关键字。它的效果出奇地好。

## Elvis 运算符

Elvis 运算符源于 Java 中的一个常见用法：使用三元运算符来提供默认值。例如：

```
1   String name = person.getName() == null ? "Bob" :    person.getName();
```

而在 Groovy 中，你可以直接使用 elvis 运算符：

```
1   String name = person.getName() ?: "Bob"
```

## 安全解引用运算符

与 elvis 运算符类似，Groovy 提供了安全解引用运算符，可以让你轻松避免空指针异常。它只需添加一个问号。例如：

```
1   String name = person?.getName()
```

如果 `person` 为 null，这会将 `name` 简单地设置为 null。它也适用于方法调用。

### ![../images/426440_2_En_2_Chapter/426440_2_En_2_Figi_HTML.gif](img/426440_2_En_2_Figi_HTML.gif) 提示

多次编写使用 elvis 运算符和安全解引用的 Groovy 代码，直到你记住其语法。

## 简史

以下是 Groovy 语言从 Groovy 1.8 开始的更新简史。如果你必须使用旧版本的 Groovy，或者你已经好几年没有接触过 Groovy，这将对你有所帮助。

### Groovy 1.8

*   命令链——代码 `"pull request on github"` 的执行方式为：`pull(request).on(github)`

*   捆绑了 GPars^(¹²) 用于并行和并发范式（后来被移除）

*   闭包注解参数——`@Invariant({number >= 0})`

*   闭包记忆化——`{...}.memoize()`

*   内置 JSON 支持——消费、生成和美化打印

*   新的 AST 转换——`@Log, @Field, @AutoClone, @AutoExternalizable, ...`

Groovy 1.8 通过支持*命令链*进一步改进了省略不必要标点符号的能力，这允许你在方法调用链中省略括号和点号。

### Groovy 2.0

*   Groovy 变得更加模块化，许多部分被拆分成多个 jar 包。

*   增加了创建自己模块的能力，允许你向现有类添加方法（扩展模块^(¹³)）。

*   `@CompileStatic`：将你的 Groovy 代码编译成字节码。

*   `@TypeChecked`：强制在编译时进行类型检查（如上一节所述）。

*   Java 7 对齐：Try-with-resources、数字字面量中的下划线以及动态调用（可选使用 `–indy command option`）。

*   Java 7 多异常捕获：`catch (Exception1 | Exception2 e) {}`

Groovy 2 的巨大变化是增加了 `@CompileStatic` 和 `@TypeChecked` 注解，这些已经介绍过了。



### Groovy 2.1

*   全面支持 JDK 7 的“invoke dynamic”指令和 API。

*   `@DelegatesTo`——一种用于闭包委托的 DSL 和静态类型检查器扩展的特殊注解。

*   编译时元注解——`@groovy.transform.AnnotationCollector` 可用于创建元注解。

*   对现有 AST 转换的许多改进。

此版本通过利用 Java 7 的 invoke dynamic 在性能上取得了巨大提升。然而，该功能默认并未启用（这将在后续章节中介绍；基本上你只需“开启它”即可）。

### Groovy 2.2

*   隐式闭包强制转换。

*   方法的 `@Memoized` AST 转换。

*   Bintray 的 JCenter 仓库。

*   使用注解定义基础脚本类。

*   新的 `DelegatingScript` 基础脚本类。

*   带有泛型类型标记的 `@DelegatesTo`。

*   预编译的类型检查扩展。

这里需要关注的主要是隐式闭包强制转换，它允许你在任何可以使用 SAM（单一抽象方法）接口的地方使用闭包。在此版本之前，你需要显式地强制转换闭包。

### Groovy 2.3

*   官方支持在 JDK 8 上运行 Groovy

*   特质（Traits）

*   新增和更新的 AST 转换

此版本引入了一个全新的概念（*特质*）和 `trait` 关键字。我们将在后续章节中介绍它们。

### Groovy 2.4

*   Android 支持

*   性能改进和字节码缩减

*   特质 @Self 类型注解

*   GDK 改进

*   更多 AST 转换

Groovy 的开发者们再次超越自我，进行了大量改进并加入了 Android 支持。

### Groovy 2.5

Groovy 2.5 增加了对 JDK9+ 的支持，新增了 11 个 AST 转换，并添加了宏功能，使得编写 AST 转换更加容易。

Groovy 2.5 中新增的注解包括 @AutoFinal、@AutoImplement、@ImmutableBase、@ImmutableOptions、@MapConstructor、@NamedDelegate、@NamedParam、@NamedParams、@NamedVariant、@PropertyOptions 和 @VisibilityOptions。

其中一些注解描述如下：

*   @*AutoImplement*：自动实现缺失的抽象方法（例如来自接口的方法）。你可以指定从这些方法中抛出的异常，例如 UnsupportedOperationException。这对于生成测试桩或当你只需要实现继承的抽象方法子集时非常有用。

*   @*AutoFinal*：自动为方法参数添加 final 修饰符。

*   @*MapConstructor*：为你的类添加一个构造函数，该构造函数接受一个 map 参数，期望字段名作为键，并设置相应的字段值。例如：

    ```
    @groovy.transform.MapConstructor
    class Person { String firstName; String lastName }
    // 稍后...
    def p= new Person(firstName: 'Peter', lastName: 'Quill')
    assert p.firstName == 'Peter'
    ```

此外，许多注解通过新增属性得到了改进。例如，@TupleConstructor 现在包含了另外七个属性。@Immutable 注解已更新，以识别 Java 8 中新增的日期/时间类是不可变的，并处理 Optional。

### Groovy 2.6

Groovy 2.6 版本原本打算像 Groovy 3 那样，但无需 JDK 8，不过它被放弃了，以便专注于更快地交付 Groovy 3.0。

### Groovy 3.0

Groovy 3.0 拥有一个完全重写的解析器（昵称 *Parrot*），使 Groovy 与最新的 Java 11 语法保持一致，并带来了 Groovy 独有的新特性。它至少需要 JDK 8 才能运行，并且对 JDK 9/10/11 有更好的支持。

类似 Java 的语法现在包括 Java 8 风格的 lambda 表达式和方法引用，以及一些多年来 Groovy 一直难以支持的棘手语法：数组初始化、do/while 循环、循环声明中的逗号等。

#### 新运算符

**同一性**

现在可以使用运算符“`===`”来表示同一性相等，使用“`!==`”来表示非同一性相等。由于 Groovy 将 `==` 解释为“`.equals`”，过去它使用“`.is`”来表示同一性相等。支持“`===`”应该可以避免一些混淆。这类似于 JavaScript 的 `===` 运算符。

**运算符的否定变体**

现在支持新的运算符 `!instanceof` 和 `!in`。这将简化这些情况下的语法。以前你必须输入 `!(x instanceof Date)`，而现在你可以直接输入 `x !instanceof Date`。同样，对于测试否定集合包含，以前是 `!(x in [1,2,3])`，现在可以写成 `x !in [1,2,3]`。

**Elvis 赋值**

你可能熟悉 Groovy 中的 elvis 运算符（`?:`）。在许多情况下，你会使用此操作在赋值时提供默认值。例如，`name = name ?: 'None'`。现在你可以在 Groovy 3 中使用以下方式缩短此表达式，使其具有相同含义：`name ?= 'None'`

**安全索引**

与安全引用运算符类似，现在有一个安全索引运算符 `?.`。这允许你访问数组（或列表）的索引，但如果数组为 null，它将返回 null 而不是抛出异常。例如，以下代码会将值设置为数组的第一个值，如果数组为 null 则设置为 null：`value = array?[0]`

**Java 一致性**

Groovy 3 支持从 Java 8 到 11 新增的特性，例如 lambda 表达式、方法引用、构造函数引用、try-with-resources、代码块、非静态内部类，甚至局部变量（var）。

支持所有形式的 lambda 表达式（除非你使用 @CompileStatic，否则会编译为闭包）：

*   无参数——`() -> expression`

*   单参数——`x -> expression`

*   显式返回是可选的——`(x, y) -> { x * y }`

*   允许类型——`(int x, int y) -> { return x + y }`

*   允许默认值——`(int x, int y = 10) -> x+y`

Groovy 长期以来一直支持方法引用，但 Groovy 3 增加了对使用双冒号的 Java 8+ 语法的支持。例如，以下是有效的 Groovy 代码：

```
def sq(x) { x*x }
// var 类似于 def
var squares = (1..9).collect( this::sq ) //方法引用
//打印 squares=[1, 4, 9, 16, 25, 36, 49, 64, 81]
```

Groovy 3 也实现了 try-with-resources：

```
def f = new File('temp')
try (PrintWriter pw = new PrintWriter(f)) {
pw.println("TEST!")
}
```

这将导致名为“temp”的文件包含内容“TEST!”，并且无论是否发生错误，都会自动关闭 PrintWriter。

## 总结

在本章中，你了解了以下内容：

*   Groovy 如何以熟悉的语法扩展和简化 JDK 上的编程。

*   Groovy 的动态 `def`、简易属性、闭包、更好的“switch”、元编程、类型检查和静态编译注解、elvis 运算符以及安全解引用。

*   简要概述了 Groovy 可用的特性以及它们是在哪些版本中引入的。

*   Groovy 3.0 如何使 Groovy 与 Java 的最新语法保持同步，并支持一些以前不支持的额外 Java 语法。

脚注 1   2   3   4   5

