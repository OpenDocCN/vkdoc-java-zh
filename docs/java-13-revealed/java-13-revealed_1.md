# 1. 局部变量类型推断

在本章中，您将学习

*   什么是类型推断

*   什么是局部变量类型推断

*   什么是受限类型名 `var`

*   使用 `var` 声明局部变量、for-each 循环和 for 循环的索引，以及隐式类型 lambda 表达式的形参的规则

## 什么是类型推断？

假设您想将一个人 ID（例如 10）存储在一个变量中。您可能会这样声明变量：

```
int personId = 10;
```

要存储一个人 ID 列表，您可能会这样声明变量：

```
ArrayList personIdList = new ArrayList();
```

将上述变量声明重写为如下形式如何？

```
var personId = 10;
var personIdList = new ArrayList();
```

您可能会说这看起来像 JavaScript 代码，而不是 Java 代码。在 Java 10 之前确实如此。现在您可以使用 `var` 来声明*带有初始化器的局部变量*。编译器会负责为您的变量推断出正确的类型——有时也可能是非预期的错误类型。在 Java 10 及更高版本中，前面的代码片段等同于以下代码：

```
int personId = 10;
ArrayList personIdList = new ArrayList();
```

这是否意味着在 Java 中不再需要使用显式类型来声明局部变量？这是否意味着 Java 已经像 JavaScript 一样变成了动态类型语言？不，这些都不正确。使用 `var` 声明局部变量存在限制——有些是技术上的，有些是实践上的。Java 仍然是一种强类型、静态类型的语言。您刚刚看到的示例只是编译器添加的语法糖。编译器会推断变量的类型并生成字节码，这与 Java 10 之前生成的字节码相同。Java 运行时永远不会看到 `var`！接下来的几节将为您介绍类型推断的一些背景知识，以及关于如何以及在何处使用 `var` 声明局部变量的所有规则和例外情况。

Java 中的一切都围绕类型展开。您要么声明类型（如类和接口），要么使用它们。您在程序中使用的每个表达式和值都有类型。例如，值 10 是 `int` 类型，10.67 是 `double` 类型，`"Hello"` 是 `String` 类型，表达式 `new Person()` 是 `Person` 类型。

根据变量或表达式的使用上下文推导其类型称为*类型推断*。在引入 `var` 之前，Java 就已经有了类型推断，例如在 lambda 表达式和泛型类型的对象创建表达式中。以下语句使用了类型推断，它通过读取左侧的 `ArrayList<Integer>` 将右侧的菱形运算符（`<>`）推断为 `<Integer>`：

```
ArrayList personIdList = new ArrayList();
```

以下语句使用了一个隐式类型的 lambda 表达式，其中形参 `n1` 和 `n2` 的类型分别被推断为 `Integer` 和 `Long`：

```
BiFunction avg = (n1, n2) -> (n1 + n2)/2.0;
```



## 什么是 var？

`var` 是 Java 10 中的关键字吗？不是。它是一个*受限类型名*或*上下文敏感关键字*。它是一个类型名，意味着它表示一种类型（基本类型或引用类型），例如 `int`、`double`、`boolean`、`String`、`List` 等。由于 `var` 是受限类型名，你仍然可以将其用作标识符，但不能用于定义其他类型。也就是说，你可以将 `var` 用作变量名、方法名和包名，但不能用作类名或接口名。

Java 编码规范建议类名和接口名以大写字母开头。如果你没有遵循编码规范，将 `var` 用作类名或接口名，你的代码在 Java 10 及更高版本中将会出错。

你可以在以下上下文中使用 `var` 声明局部变量。此列表有许多限制，我将逐一说明：

*   带有初始化器的局部变量
*   `for-each` 循环中的索引
*   `for` 循环中的索引
*   隐式类型 lambda 表达式中的形参

以下是使用 `var` 声明带有初始化器的局部变量的示例：

```
// birthYear 的类型被推断为 int
var birthYear = 1969;
// promptMsg 的类型被推断为 String
var promptMsg = "Are you sure you want to proceed?";
// 假设 Person 是一个类，john 的类型被推断为 Person
var john = new Person();
```

以下 lambda 表达式计算一个 `Integer` 和一个 `Long` 的平均值，并返回一个 `Double`。`n1` 和 `n2` 的类型分别被推断为 `Integer` 和 `Long`。在这里，`var` 在类型推断中不起作用。我将在后面的单独章节中解释这一点。

```
BiFunction avg = (var n1, var n2) -> (n1 + n2)/2.0;
```

编译器会推断使用 `var` 声明的变量的类型。如果编译器无法推断类型，则会发生编译时错误。编译器使用用作初始化器的表达式的类型来推断被声明变量的类型。考虑之前的变量声明。在第一种情况下，编译器看到 1969，这是一个类型为 `int` 的整数字面量，并将 `birthYear` 变量的类型推断为 `int`。当源代码被编译时，类文件中包含以下语句：

```
int birthYear = 1969;
```

### 提示

使用 `var` 进行类型推断是由编译器执行的。使用 `var` 对现有的类文件和运行时没有影响。

同样的论点也适用于另外两个语句，其中 `promptMsg` 和 `john` 变量的类型分别被推断为 `String` 和 `Person`。

查看前面的示例，你可能会争辩说，为什么不使用显式类型编写这些语句，如下所示？

```
int birthYear = 1969;
String promptMsg = "Are you sure you want to proceed?";
Person john = new Person();
```

你说得有道理。除了节省几次按键之外，当你使用 `var` 时，实际上让这段代码的读者工作变得更困难了一些。当使用 `var` 时，读者必须查看初始化器才能知道被声明变量的类型。在像这样的简单表达式中，仅通过查看初始化器来了解其类型可能并不十分困难。`var` 类型名的设计者提出的一个论点是，使用它在同一位置声明多个变量可以使变量名更易于阅读，因为它们都垂直对齐。请看以下代码片段：

```
var birthYear = 1969;
var promptMsg = "Are you sure you want to proceed?";
var john = new Person();
```

比较前面两个声明相同变量集的代码片段。后者确实更容易阅读一些，但仍然更难理解一些。

让我们考虑以下使用方法调用来初始化其值的变量声明：

```
var personList = persons();
```

在这种情况下，编译器将根据 `persons()` 方法的返回类型推断 `personList` 变量的类型。对于这条语句的读者来说，除非查看 `persons()` 方法的声明，否则无法知道推断出的类型。我给变量起了一个好名字 `personList` 来指示其类型。然而，仍然不清楚它是 `List<Person>`、`Person[]` 还是其他类型。

考虑另一个变量声明的示例：

```
Map> personListByBirthMonth = new HashMap>();
```

你可以将这条语句重写如下：

```
var personListByBirthMonth = new HashMap>();
```

这次，声明看起来简单得多，并且你可以从 `var` 提供的类型推断中受益，前提是你保持变量名足够直观，以提供关于其类型的线索。

你可以在同一段代码（方法、构造器、静态初始化器和实例初始化器）中混合使用 `var` 和显式类型名。当变量的类型对读者来说清晰明了时，使用 `var` 声明变量，而不仅仅是对你清晰。如果你代码的读者无法轻易弄清楚变量类型，请使用显式类型名。始终优先考虑清晰性而非简洁性。当你使用 `var` 时，使用直观的变量名非常重要。考虑以下变量声明：

```
var x = 156.50;
```

变量名 `x` 很糟糕。如果你的方法只有几行长，使用 `x` 作为变量名或许可以原谅。然而，如果你的方法很大，读者将不得不滚动并查看变量声明，以找出 `x` 在方法体中每次出现时的含义。因此，保持变量名直观非常重要。考虑将前面的变量声明替换如下：

```
var myWeightInPounds = 156.50;
```

任何阅读使用 `myWeightInPounds` 的代码的人都不会对其类型有任何疑问。



## 快速示例

本章所有程序均位于 `jdojo.typeinference` 模块中，如代码清单 1-1 所示。

```
// module-info.java
module jdojo.typeinference {
exports com.jdojo.typeinference;
}
代码清单 1-1
名为 jdojo.typeinference 的模块声明
```

代码清单 1-2 包含一个测试类，演示如何使用 `var` 受限类型名称声明局部变量。我很快会介绍更多示例。代码中的注释和输出结果清晰地展示了类型推断的过程，因此不再对代码做进一步解释。

```
// VarTest.java
package com.jdojo.typeinference;
import java.util.List;
import java.util.Arrays;
import java.util.function.BiFunction;
public class VarTest {
public static void main(String[] args) {
// personId 的推断类型为 int
var personId = 1001;
System.out.println("personID = " + personId);
// prompt 的推断类型为 String
var prompt = "Enter a message:";
System.out.println("prompt = " + prompt);
// 你可以像使用 "String prompt = ..." 声明时那样，
// 在 prompt 上调用 String 类的方法
System.out.println("prompt.length() = " + prompt.length());
System.out.println("prompt.substring(0, 5) = "
+ prompt.substring(0, 5));
// 使用显式类型名称 double
double salary = 1878.89;
System.out.println("salary = " + salary);
// luckyNumbers 的推断类型为 List
var luckyNumbers = List.of(9, 19, 1969);
System.out.println("luckyNumbers = " + luckyNumbers);
// cities 的推断类型为 String[]
var cities = new String[]{"Atlanta", "Patna", "Paris", "Gaya"};
System.out.println("cities = " + Arrays.toString(cities));
System.out.println("cities.getClass() = " + cities.getClass());
System.out.println("\n 使用 for 循环的城市列表：");
// 索引 i 的推断类型为 int
for (var i = 0; i < cities.length; i++) {
System.out.println(cities[i]);
}
System.out.println("\n 使用 for-each 循环的城市列表：");
// 元素 city 的推断类型为 String
for (var city : cities) {
System.out.println(city);
}
// 使用 var 声明 lambda 表达式的参数
// 以下 lambda 表达式中的参数 n1 和 n2 被推断为 double 类型
BiFunction<Double, Double, Double> avg
= (var n1, var n2) -> (n1 + n2) / 2.0;
System.out.println("\n10 和 20 的平均值为 "
+ avg.apply(10, 20L));
}
}
personID = 1001
prompt = Enter a message:
prompt.length() = 16
prompt.substring(0, 5) = Enter
salary = 1878.89
luckyNumbers = [9, 19, 1969]
cities = [Atlanta, Patna, Paris, Gaya]
cities.getClass() = class [Ljava.lang.String;
使用 for 循环的城市列表：
Atlanta
Patna
Paris
Gaya
使用 for-each 循环的城市列表：
Atlanta
Patna
Paris
Gaya
10 和 20 的平均值为 15.0
代码清单 1-2
使用 var 受限类型名称声明局部变量
```

我相信你对局部变量类型推断在不同使用场景下还有很多疑问。下一节我将通过示例介绍大多数使用场景。我发现 JShell 工具（JDK 9 附带的命令行工具）对于实验 `var` 非常有用。如果你不熟悉 JShell 工具，请参考我的书《Beginning Java 9 Fundamentals》（ISBN: 978-1484228432）的第 [23](https://doi.org/10.1007/978-1-4842-5407-3_23) 章。在接下来的章节中，我将多次使用 JShell 展示代码片段和结果。请确保将 JShell 会话的反馈模式设置为 verbose，这样当你使用 `var` 时，JShell 会打印带有推断类型的变量声明。以下 JShell 会话展示了前面示例中使用的一些变量声明：

```
C:\Java13Revealed>jshell
|  欢迎使用 JShell -- 版本 13-ea
|  如需介绍，请输入：/help intro
jshell> /set feedback verbose
|  反馈模式：verbose
jshell> var personId = 1001;
personId ==> 1001
|  创建了变量 personId : int
jshell> var prompt = "Enter a message:";
prompt ==> "Enter a message:"
|  创建了变量 prompt : String
jshell> var luckNumbers = List.of(9, 19, 1969);
luckNumbers ==> [9, 19, 1969]
|  创建了变量 luckNumbers : List
jshell> var cities = new String[]{"Atlanta", "Patna", "Paris", "Gaya"}
cities ==> String[4] { "Atlanta", "Patna", "Paris", "Gaya" }
|  创建了变量 cities : String[]
jshell> /exit
|  再见
C:\Java13Revealed>
```

## 使用 var 的规则

前面几节介绍了使用 `var` 的基本规则。本节将通过示例介绍更详细的规则。阅读这些规则时，请记住，在 Java 10 中引入 `var` 只有一个目标——通过保持 `var` 使用规则的简单性，让开发者的生活更轻松。我在示例中使用 JShell 会话。我将解释使用 `var` 声明局部变量的以下规则：

*   不支持没有初始化器的变量声明。
*   初始化器不能是 `null` 类型。
*   不支持在单个声明中声明多个变量。
*   初始化器不能引用正在声明的变量。
*   不支持单独的数组初始化器。
*   声明数组时不能指定数组维度。
*   不允许使用多态表达式（如 lambda 表达式和方法引用）作为初始化器。
*   不支持实例字段和静态字段。
*   `var` 不支持作为方法的返回类型。也不能用它来声明方法的参数。

如果你需要声明一个变量，且该变量需要使用这些规则不允许的特性，请使用显式类型代替 `var`。

### 不允许未初始化的变量

你不能使用 `var` 声明未初始化的变量。在这种情况下，编译器没有表达式可以用来推断变量的类型。

```
jshell> var personId;
|  错误：
|  无法推断局部变量 personId 的类型
|    （不能在没有初始化器的情况下使用 'var'）
|  var personId;
|  ^-----------^
```

这条规则使你在代码中使用 `var` 变得简单，并且易于诊断错误。假设允许使用 `var` 声明未初始化的变量。你可以在程序中声明一个变量，并在几行之后首次为其赋值，这将使编译器推断变量的类型。如果你在另一部分遇到编译时错误，提示你为变量分配了错误类型的值，你将需要查看决定变量类型的第一次赋值。这被称为“远距离作用”推断错误，即一个位置的操作可能导致另一部分稍后出现错误——使开发者更难定位错误。

### 不允许 null 类型初始化器

你可以将 `null` 赋值给任何引用类型变量。如果使用 `null` 作为 `var` 的初始化器，编译器无法推断变量的类型。

```
jshell> var personId = null;
|  错误：
|  无法推断局部变量 personId 的类型
|    （变量初始化器为 'null'）
|  var personId = null;
|  ^------------------^
```

### 不允许声明多个变量

你不能使用 `var` 在单个声明中声明多个变量。

```
jshell> var personId = 1001, days = 9;
|  错误：
|  复合声明中不允许使用 'var'
|  var personId = 1001, days = 9;
|      ^
```

### 不能在初始化器中引用变量

使用 `var` 时，你不能在初始化器中引用正在声明的变量。例如，以下声明无效：

```
jshell> var x = (x = 1001);
|  错误：
|  无法推断局部变量 x 的类型
|    （不能对自引用变量使用 'var'）
|  var x = (x = 1001);
|      ^
```

但是，当你使用显式类型时，以下声明是有效的：

```
// 将 x 声明为 int 类型，并初始化为 1001
int x = (x = 1001);
```



### 禁止单独使用数组初始化器

像 `{10, 20}` 这样的数组初始化器是一个多态表达式，其类型取决于赋值操作的左侧。你不能将数组初始化器与 `var` 一起使用。你必须使用带有或不带数组初始化器的数组创建表达式，例如 `new int[]{10, 20}` 或 `new int[2]`。在这种情况下，编译器推断变量的类型与数组创建表达式中使用的类型相同。以下 JShell 会话展示了一些示例：

```
jshell> var evens = {2, 4, 6};
|  Error:
|  cannot infer type for local variable evens
|    (array initializer needs an explicit target-type)
|  var evens = {2, 4, 6};
|  ^--------------------^
jshell> var evens = new int[]{2, 4, 6};
evens ==> int[3] { 2, 4, 6 }
|  created variable evens : int[]
jshell> var odds = new int[3];
odds ==> int[3] { 0, 0, 0 }
|  created variable odds : int[]
```

### 禁止指定数组维度

在使用 `var` 声明变量时，你不能在 `var` 或变量名后使用方括号（`[]`）来指定数组的维度。数组的维度是从初始化器中推断出来的。

```
jshell> var[] cities = new String[3];
|  Error:
|  'var' is not allowed as an element type of an array
|  var[] cities = new String[3];
|        ^
jshell> var cities = new String[3];
cities ==> String[3] { null, null, null }
|  created variable cities : String[]
jshell> var points3D = new int[3][][];
points3D ==> int[3][][] { null, null, null }
|  created variable points3D : int[][][]
```

### 禁止使用多态表达式作为初始化器

多态表达式需要一个目标类型来推断其类型。在使用 `var` 声明变量的初始化器中，不允许使用 lambda 表达式和方法引用等多态表达式。数组初始化器也是多态表达式，同样不被允许。你需要对多态表达式使用显式类型。以下 JShell 会话展示了一些示例，其中我使用 `var` 声明带有初始化器的变量，这会产生错误。我还展示了使用显式类型代替 `var` 不会产生错误的情况。

```
jshell> var increment = x -> x + 1;
|  Error:
|  cannot infer type for local variable increment
|    (lambda expression needs an explicit target-type)
|  var increment = x -> x + 1;
|  ^-------------------------^
jshell> Function increment = x -> x + 1;
increment ==> $Lambda$17/0x0000000800bb2c40@7823a2f9
|  created variable increment : Function
jshell> var next = increment.apply(10);
next ==> 11
|  created variable next : Integer
jshell> var intGenerator = new Random()::nextInt;
|  Error:
|  cannot infer type for local variable intGenerator
|    (method reference needs an explicit target-type)
|  var intGenerator = new Random()::nextInt;
|  ^---------------------------------------^
jshell> Supplier intGenerator = new Random()::nextInt;
intGenerator ==> $Lambda$18/0x0000000800bb3c40@4678c730
|  created variable intGenerator : Supplier
jshell> int nextInteger = intGenerator.get();
nextInteger ==> -1522642581
|  created variable nextInteger : int
```

### 两侧的类型推断

在大多数情况下，当编译器无法推断初始化器和变量的类型时，你会收到错误。在某些情况下，`Object` 会被推断为类型。请考虑以下情况：

```
var list = new ArrayList();
```

初始化器使用了菱形运算符，这使得编译器推断 `ArrayList<>` 的类型；而左侧使用 `var` 又使得编译器推断 `ArrayList<>` 中的类型。你可能会认为这种情况会报错。然而，编译器会将之前的声明替换为以下内容：

```
ArrayList list = new ArrayList();
```

请考虑以下代码片段，它需要两侧的类型推断：

```
public class NumberWrapper {
// 更多代码在此处
}
var wrapper = new NumberWrapper();
```

在这种情况下，推断出的类型是类型参数 `"E extends Number"` 的上界，即 `Number`。编译器会将之前的变量声明替换为以下内容：

```
NumberWrapper wrapper = new NumberWrapper();
```



### 推断不可具名类型

使用 `var` 声明变量可能会导致编译器推断出不可具名的类型，例如捕获变量类型、交集类型和匿名类类型。请考虑以下使用匿名类的代码片段。请注意，匿名类没有名称，因此它是不可具名的。

```
class Template implements Serializable, Comparable {
@Override
public int compareTo(Object o) {
throw new UnsupportedOperationException("Not supported yet.");
}
// 更多代码在此处
}
var myTemplate = new Template() {
// 更多代码在此处
};
```

变量 `myTemplate` 的推断类型会是什么？如果你为 `myTemplate` 变量指定了显式类型，你会有以下四种选择：

*   `Template myTemplate = ...`

*   `Serializable myTemplate = ...`

*   `Comparable myTemplate = ...`

*   `Object myTemplate = ...`

编译器有第五种选择，即匿名类的不可具名类型。在这种情况下，编译器会推断出匿名类的不可具名类型。当你向匿名类添加一个未覆盖其超类或超接口中任何方法的新方法时，你无法静态地调用该方法。也就是说，你无法在源代码中调用该方法——编译器不会编译它。然而，在匿名类声明中使用 `var` 允许你这样做，因为你推断出的变量类型是编译器生成的匿名类不可具名类型。以下 JShell 会话演示了这一点：

```
jshell> var runnable = new Runnable() {
...>     public void run() {
...>         System.out.println("Inside run()...");
...>     }
...>
...>     public void test() {
...>         System.out.println(
...>             "Calling this method is possible because of var...");
...>     }
...> };
runnable ==> $0@7823a2f9
jshell> runnable.run();
Inside run()...
jshell> runnable.test();
Calling this method is possible because of var...
```

考虑以下语句，其中 `Object` 类中 `getClass()` 方法的返回类型是 `Class<?>`：

```
// name 的推断类型是 String
var name = "John";
// cls 的推断类型是 Class
var cls = name.getClass();
```

在这种情况下，`Class<?>` 中的捕获变量类型已被向上投影到 `name` 变量类的超类型，并且 `cls` 的推断类型是 `Class<? extends String>` 而不是 `Class<?>`。

以下是推断不可具名类型的最后一个示例：

```
var list = List.of(10, 20, 45.89);
```

变量 `list` 的推断类型会是什么？这是一个较难理解的情况。`List.of()` 方法的参数是 `Integer` 和 `Double` 类型。`Integer` 和 `Double` 类的声明如下：

*   `public final class Integer extends Number implements Comparable<Integer>, Constable, ConstantDesc`

*   `public final class Double extends Number implements Comparable<Double>, Constable, ConstantDesc`

`Integer` 和 `Double` 类型都实现了三个接口：`Comparable`、`Constable` 和 `ConstantDesc`。`Constable` 和 `ConstantDesc` 接口是在 Java 12 中添加的，它们位于 `java.lang.constant` 包中。在这种情况下，编译器将 `Integer` 和 `Double` 投影到它们的公共超类型 `Number`，并使用 `Number` 与这三个接口类型的交集作为推断类型。

前面的变量声明（`var list = List.of(10, 20, 45.89);`）等同于以下内容。请注意，交集类型是不可具名的，因此你不能在源代码中使用以下语句。我在此展示它只是为了向你展示实际推断出的类型。你也可以使用 JShell 输入前面的语句，以查看以下内容作为其推断类型：

```
List&java.lang.constant.Constable&java.lang.constant.ConstantDesc>&java.lang.constant.Constable&java.lang.constant.ConstantDesc> list = List.of(10, 20, 45.89);
```

在编译器层面，使用 `var` 声明变量可能会变得复杂。在这种情况下，请使用显式类型来明确你的意图。请记住，清晰性比简洁性更重要。你可以将前面的语句重写如下：

```
List list = List.of(10, 20, 45.89);
```

### 不允许在字段和方法声明中使用

不允许使用 `var` 声明字段（实例和静态字段）、方法参数以及方法返回类型。公共字段和方法定义了类的接口。它们会在其他类中被引用。对其声明进行细微更改可能需要重新编译其他类。在局部变量中使用 `var` 仅具有局部影响。这就是 `var` 不支持字段和方法的原因。在设计过程中曾考虑过私有字段和方法，但为了保持 `var` 实现的简单性，并未支持它们。

## 将 final 与 var 结合使用

你可以使用 `var` 声明一个 `final` 变量。`final` 修饰符的行为与引入 `var` 之前相同。以下局部变量声明是有效的：

```
// personId 的推断类型是 int。
// personId 是 final 的。你不能为其赋另一个值。
final var personId = 1001;
```



## Lambda 表达式中的形式参数

Java 10 不支持使用 `var` 声明隐式类型 Lambda 表达式的形式参数。考虑以下使用隐式类型 Lambda 表达式的语句：

```
BiFunction adder = (x, y) -> (long)(x + y);
```

在此例中，`x` 和 `y` 两个参数的类型都被推断为 `Integer`。在 JDK 10 中，你不能这样写：

```
// 在 Java 10 中会导致编译时错误，但在 Java 11 及更高版本中允许
BiFunction adder = (var x, var y) -> (long)(x + y);
```

上述语句在 Java 11 中是可行的，Java 11 引入了对隐式类型 Lambda 表达式形式参数中使用 `var` 的支持。在隐式类型 Lambda 表达式的形式参数中使用 `var` 对类型推断没有影响。隐式类型 Lambda 表达式会从其使用上下文中推断其形式参数的类型。以下两个语句对编译器来说是相同的：

```
BiFunction adder = (x, y) -> (long)(x + y);
BiFunction adder = (var x, var y) -> (long)(x + y);
```

在这两种情况下，都是通过查看左侧的变量声明来推断 `x` 和 `y` 的类型。你可能会疑惑，既然如此，为什么还要使用 `var` 呢？有两个简单的原因：

*   为了保持局部变量类型推断语法的一致性
*   为了允许在隐式类型 Lambda 表达式的形式参数上使用修饰符和注解

由于 `var` 在其他上下文中已被允许用于局部变量，其设计者为了使用上的一致性，也允许将其用于隐式类型 Lambda 表达式的形式参数。请记住，在隐式类型 Lambda 表达式中仅对形式参数使用 `var` 除了保持一致性外，没有实际效果。

在 Java 11 之前，你不能在隐式类型 Lambda 表达式的形式参数上使用修饰符或注解。假设在我们的示例中，你想将 `n1` 和 `n2` 声明为 `final`。你不能像这样实现：

```
// 编译时错误
BiFunction avg = (final n1, final n2) -> (n1 + n2)/2.0;
```

你可以在以下 JShell 会话中看到实际的编译时错误：

```
jshell> BiFunction avg = (final n1, final n2) -> (n1 + n2)/2.0;
|  错误：
|  需要标识符
|  BiFunction avg = (final n1, final n2) -> (n1 + n2)/2.0;
|                                                 ^
|  错误：
|  需要标识符
|  BiFunction avg = (final n1, final n2) -> (n1 + n2)/2.0;
|                                                           ^
```

如果你在 Java 11 之前想在 `n1` 和 `n2` 上使用 `final` 修饰符，可以通过使用显式类型 Lambda 表达式来实现：

```
BiFunction avg = (final Integer n1, final Long n2) -> (n1 + n2)/2.0;
```

从 Java 11 开始，你可以通过使用 `var` 将隐式类型 Lambda 表达式的形式参数声明为 `final`，如下所示：

```
BiFunction avg = (final var n1, final var n2) -> (n1 + n2)/2.0;
```

你也可以在隐式类型 Lambda 表达式的形式参数上使用注解，如下所示：

```
BiFunction avg = (@NonNull var n1, @NonNull var n2) -> (n1 + n2)/2.0;
```

在隐式类型 Lambda 表达式中对形式参数使用 `var` 有一些限制。具体描述如下：

隐式类型 Lambda 表达式必须对其所有形式参数都使用 `var`，或者都不使用。

```
// 编译时错误。仅对参数 n1 使用了 var，而非两者
BiFunction avg = (var n1, n2) -> (n1 + n2)/2.0;
```

你只能在隐式类型 Lambda 表达式的形式参数上使用 `var`，而不能在显式类型 Lambda 表达式上使用。也就是说，你不能为某些形式参数指定显式类型，而对其他形式参数使用 `var`。

```
// 编译时错误。混用了 var 和显式类型 Long
BiFunction avg = (var n1, Long n2) -> (n1 + n2)/2.0;
```

不允许在像上面这样的半隐式类型 Lambda 表达式中使用 `var` 可能看起来有违直觉。在这种情况下，编译器必须部分推断类型——仅针对形式参数 `n1`。之所以不允许这样做，是因为在这些情况下类型推断和重载解析会变得非常复杂。未来可能会允许将 `var` 与形式参数的显式类型混合使用。

为简洁起见，隐式类型 Lambda 表达式允许你在只有一个形式参数时省略括号：

```
Function twice = x -> x * 2;
```

如果你使用 `var` 声明上述 Lambda 表达式中的形式参数，则必须使用括号：

```
Function twice = var x -> x * 2; // 编译时错误
Function twice = (var x) -> x * 2; // 正确
```

## 向后兼容性

如果你现有的代码将 `var` 用作类名或接口名，你的代码在 Java 10 及更高版本中将无法编译。将其用作方法名、包名和变量名则没有问题。你不太可能将类或接口命名为 `var`，因为那将违反 Java 命名约定。

## 总结

从使用上下文中推导变量或表达式的类型称为类型推断。Java 10 引入了一个受限类型名 `var`，可用于声明带有初始化器的局部变量，以及 `for-each` 和 `for` 循环的索引。Java 11 扩展了对 `var` 的支持，使其可用于声明隐式类型 Lambda 表达式的形式参数。使用 `var` 声明的变量的类型是从初始化器中的表达式推断出来的。`var` 不是关键字，而是一个受限类型名，这意味着你不能拥有名为 `var` 的类型（如类或接口）。如果你现有的代码中有一个名为 `var` 的类型，那么在 JDK 10 及更高版本中编译时，你的代码将无法编译。

并非总能从初始化器中推断出局部变量的类型。例如，如果初始化器是 `null`，则无法推断类型。使用 `var` 声明变量有一些限制：

*   不支持没有初始化器的变量声明。
*   初始化器不能是 `null` 类型。
*   不支持在单个声明中声明多个变量。
*   初始化器不能引用正在声明的变量。
*   不支持单独的数组初始化器。
*   声明数组时不能指定数组维度。
*   初始化器中不允许使用多态表达式，例如 Lambda 表达式和方法引用。
*   不支持实例字段和静态字段。
*   不允许在隐式类型 Lambda 表达式中仅对部分形式参数使用 `var`。
*   不能在 Lambda 表达式中混用 `var` 和显式类型的形式参数。
*   如果使用 `var` 声明隐式类型 Lambda 表达式的形式参数，即使只有一个形式参数，也必须将形式参数声明括在括号中。

