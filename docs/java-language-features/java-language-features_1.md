# 1. 注解

电子补充材料 本章的在线版本（[`​doi.​org/​10.​1007/​978-1-4842-3348-1_​1`](https://doi.org/10.1007/978-1-4842-3348-1_1)）包含补充材料，仅供授权用户使用。

在本章中，你将学习：

*   什么是注解
*   如何声明注解
*   如何使用注解
*   什么是元注解以及如何使用它们
*   用于弃用 API、抑制命名编译时警告、重写方法和声明函数式接口的常用注解
*   如何在运行时访问注解
*   如何在源代码中处理注解

本章中的所有示例程序都属于 `jdojo.annotation` 模块，如清单 1-1 所示。

```
// module-info.java
module jdojo.annotation {
exports com.jdojo.annotation;
}
清单 1-1.
jdojo.annotation 模块的声明
```

## 什么是注解？

在定义注解并讨论它们在编程中的重要性之前，让我们先看一个简单的例子。假设你有一个 `Employee` 类，其中包含一个名为 `setSalary()` 的方法，用于设置员工的薪水。该方法接受一个 `double` 类型的参数。以下代码片段展示了 `Employee` 类的一个简单实现：

```
public class Employee {
public void setSalary(double salary) {
System.out.println("Employee.setSalary():" + salary);
}
}
```

`Manager` 类继承自 `Employee` 类。你希望以不同的方式设置经理的薪水，因此决定在 `Manager` 类中重写 `setSalary()` 方法。`Manager` 类的代码如下：

```
public class Manager extends Employee {
// 重写 Employee 类中的 setSalary() 方法
public void setSalary(int salary) {
System.out.println("Manager.setSalary():" + salary);
}
}
```

`Manager` 类中存在一个错误，当你试图重写 `setSalary()` 方法时出现了问题。你很快就会纠正这个错误。你在错误重写的方法中使用了 `int` 数据类型作为参数类型。现在是为经理设置薪水的时候了。以下代码用于实现这一操作：

```
Employee ken = new Manager();
int salary = 200;
ken.setSalary(salary);
Employee.setSalary():200.0
```

这段代码本应调用 `Manager` 类的 `setSalary()` 方法，但输出并未显示预期结果。

你的代码哪里出了问题？在 `Manager` 类中定义 `setSalary()` 方法的初衷是重写 `Employee` 类的 `setSalary()` 方法，而不是重载它。你犯了一个错误。你在 `setSalary()` 方法中使用了 `int` 类型作为参数类型，而不是 `Manager` 类中的 `double` 类型。你添加了注释表明你打算重写 `Manager` 类中的方法。然而，注释并不能阻止你犯逻辑错误。你可能会像每个程序员一样，花费数小时调试由此类逻辑错误导致的问题。在这种情况下，谁能帮助你？注解或许能在类似情况下提供帮助。

让我们使用注解重写你的 `Manager` 类。目前你不需要了解任何关于注解的知识。你只需在程序中添加一个单词。以下是 `Manager` 类的修改版本：

```
public class Manager extends Employee {
@Override
public void setSalary(int salary) {
System.out.println("Manager.setSalary():" + salary);
}
}
```

你所做的只是向 `Manager` 类添加了一个 `@Override` 注解，并删除了那些“愚蠢”的注释。尝试编译修改后的 `Manager` 类会导致编译时错误，该错误指向 `Manager` 类的 `setSalary()` 方法中使用的 `@Override` 注解：

```
Manager.java:2: 错误: 方法未覆盖或实现超类型中的方法
@Override
^
1 个错误
```

使用 `@Override` 注解起到了作用。`@Override` 注解用于非静态方法，以表明程序员打算重写超类中的方法。在源代码层面，它起到了文档记录的作用。当编译器遇到 `@Override` 注解时，它会确保该方法确实重写了超类中的方法。如果被注解的方法没有重写超类中的方法，编译器会生成一个错误。在你的例子中，`Manager` 类中的 `setSalary(int salary)` 方法没有重写超类 `Employee` 中的任何方法。这就是你收到错误的原因。你可能会意识到，使用注解就像记录源代码一样简单。然而，它们有编译器的支持。你可以使用它们来指示编译器强制执行某些规则。注解提供的好处远不止你在本例中看到的。让我们回到编译时错误。你可以通过以下两种方式之一来修复该错误：

*   你可以从 `Manager` 类的 `setSalary(int salary)` 方法中移除 `@Override` 注解。这将使该方法成为一个重载方法，而不是重写其超类方法的方法。
*   你可以将方法签名从 `setSalary(int salary)` 改为 `setSalary(double salary)`。

由于你希望重写 `Manager` 类中的 `setSalary()` 方法，请使用第二个选项，并按如下方式修改 `Manager` 类：

```
public class Manager extends Employee {
@Override
public void setSalary(double salary) {
System.out.println("Manager.setSalary():" + salary);
}
}
```

现在，以下代码将按预期工作：

```
Employee ken = new Manager();
int salary = 200;
ken.setSalary(salary);
Manager.setSalary():200.0
```

请注意，`Manager` 类的 `setSalary()` 方法中的 `@Override` 注解为你节省了调试时间。假设你更改了 `Employee` 类中的方法签名。如果 `Employee` 类中的更改导致此方法不再被 `Manager` 类重写，那么当你再次编译 `Manager` 类时，你将收到相同的错误。你是否开始理解注解的强大之处了？有了这个背景知识，让我们开始深入探讨注解。

根据《韦氏词典》，注解的含义是：

> “通过评论或解释添加的注释”。

这正是 Java 中注解的含义。它允许你将元数据（或注释）关联（或注解）到 Java 程序中的程序元素。程序元素可以是模块、包、类、接口、类的字段、局部变量、方法、方法的参数、枚举、注解、泛型类型/方法声明中的类型参数、类型使用等。换句话说，你可以注解 Java 程序中的任何声明或类型使用。注解在程序元素的声明中用作“修饰符”，就像其他修饰符（`public`、`private`、`final`、`static` 等）一样。与修饰符不同，注解不会修改程序元素的含义。它就像它所注解的程序元素的装饰或注释。



注解与常规文档在许多方面存在差异。常规文档仅供人类阅读，且是“静态的”，不具备任何智能特性。如果你在文档中拼错单词，或描述的内容与代码实际执行情况相反，只能自行承担后果。在运行时以编程方式读取文档元素既困难又不切实际。Java 允许你从文档生成 Javadoc，这便是常规文档的全部功能。这并不意味着你不需要为程序编写文档——常规文档确实必不可少。但与此同时，你需要一种类似文档的机制来强制执行你的意图，且该文档应能被编译器和运行时访问。注解正是为此而生。它既具备人类可读性，可作为文档使用；又具备编译器可读性，能让编译器验证程序员的意图——例如，当编译器遇到方法的 `@Override` 注解时，会确保程序员确实重写了该方法。注解在运行时同样可用，程序可以读取它并用于任何目的。例如，工具可以读取注解并生成样板代码。如果你曾使用过企业级 JavaBean（EJB），就会知道保持所有接口和类同步、并向 XML 配置文件添加条目是多么痛苦。EJB 3.0 使用注解生成样板代码，使 EJB 开发对程序员来说变得轻松。注解在框架/工具中应用的另一个例子是 JUnit 4.0。JUnit 是 Java 程序的单元测试框架，它使用注解来标记作为测试用例的方法。在此之前，你必须为测试用例方法遵循特定的命名约定。注解有多种用途，包括文档、编译器验证与强制执行、运行时校验、框架/工具的代码生成等。

要使注解对编译器和运行时可用，它必须遵循特定规则。事实上，注解是类似于类和接口的另一种类型。正如在使用类类型或接口类型之前必须先声明它们一样，你也必须先声明注解类型。

注解不会改变其所注解的程序元素的语义（或含义）。从这个意义上说，注解就像注释，不会影响被注解程序元素的工作方式。例如，`setSalary()` 方法上的 `@Override` 注解并未改变该方法的工作方式。你（或工具/框架）可以基于注解改变程序的行为。在这种情况下，是你使用了注解，而非注解本身主动执行任何操作。关键在于，注解本身始终是被动的。



## 声明注解类型

声明注解类型与声明接口类型类似，但有一些限制。根据 Java 规范，注解类型声明是一种特殊的接口类型声明。你需要使用 `interface` 关键字，并在其前面加上 `@` 符号（at 符号）来声明一个注解类型。以下是声明注解类型的一般语法：

```
[修饰符] @ interface <注解类型名称> {
// 注解类型主体
}
```

注解声明的 `[修饰符]` 与接口声明的修饰符相同。例如，你可以在公共级别或包级别声明一个注解类型。`@` 符号和 `interface` 关键字之间可以用空格分隔，也可以放在一起。按照惯例，它们通常放在一起，写作 `@interface`。`interface` 关键字后面跟着注解类型名称，它应该是一个有效的 Java 标识符。注解类型主体放在花括号内。

假设你想用版本信息来注解你的程序元素，这样你就可以准备一份关于产品特定版本中新增程序元素的报告。要使用自定义注解类型（与内置注解如 `@Override` 相对），你必须先声明它。你希望在版本信息中包含主版本号和次版本号。清单 1-2 包含了你的第一个注解声明的完整代码。

```
// Version.java
package com.jdojo.annotation;
public @interface Version {
int major();
int minor();
}
清单 1-2.
名为 Version 的注解类型的声明
```

将 `Version` 注解的声明与接口的声明进行比较。它与接口定义仅在一个方面不同：它在名称前使用了 `@` 符号。你在 `Version` 注解类型中声明了两个抽象方法：`major()` 和 `minor()`。注解类型中的抽象方法被称为它的**元素**。你可以换一种方式理解：一个注解可以声明零个或多个元素，它们被声明为抽象方法。抽象方法的名称就是注解类型元素的名称。你为 `Version` 注解类型声明了两个元素：`major` 和 `minor`。这两个元素的数据类型都是 `int`。

提示

虽然你可以在接口类型中声明静态方法和默认方法，但它们在注解类型中是不允许的。静态方法和默认方法旨在包含一些逻辑。而注解旨在仅表示注解类型中元素的值。这就是为什么注解类型中不允许使用静态方法和默认方法的原因。

你需要编译这个注解类型。当 `Version.java` 文件被编译时，它会生成一个 `Version.class` 文件。你的注解类型的简单名称是 `Version`，其完全限定名是 `com.jdojo.annotation.Version`。使用注解类型的简单名称遵循与其他类型（例如类、接口等）相同的规则。你需要像导入其他类型一样导入注解类型。

你如何使用一个注解类型？你可能会想，你需要声明一个新类来实现 `Version` 注解类型，然后创建该类的对象。但你可能松了一口气，因为你知道使用 `Version` 注解类型不需要任何额外的步骤。注解类型一旦被声明和编译，就可以立即使用。要创建注解类型的实例并使用它来注解程序元素，你需要使用以下语法：

```
@注解类型名称(名称 1=值 1, 名称 2=值 2, 名称 3=值 3...)
```

注解类型前面有一个 `@` 符号。后面跟着一个用逗号分隔的 `名称=值` 对列表，并用括号括起来。`名称=值` 对中的名称是在注解类型中声明的元素名称，值是用户为该元素提供的值。`名称=值` 对的顺序不必与它们在注解类型中声明的顺序相同，尽管按照惯例，`名称=值` 对的使用顺序与注解类型中元素的声明顺序一致。

让我们使用一个 `Version` 类型的实例，其 `major` 元素值为 `1`，`minor` 元素值为 `0`。以下是你的 `Version` 注解类型的一个实例：

```
@Version(major=1, minor=0)
```

你可以将此注解重写为 `@Version(minor=0, major=1)`，其含义不变。你也可以使用注解类型的完全限定名，如下所示：

```
@com.jdojo.annotation.Version(major=0, minor=1)
```

你可以在程序中随意使用任意多个 `Version` 注解类型的实例。例如，你有一个 `VersionTest` 类，它自 1.0 版本起就存在于你的应用程序中。你在 1.1 版本中添加了一些方法和实例变量。你可以使用你的 `Version` 注解来记录不同版本中对 `VersionTest` 类的添加内容。你可以像这样注解你的类声明：

```
@Version(major=1, minor=0)
public class VersionTest {
// 代码放在这里
}
```

添加注解的方式与为程序元素添加修饰符的方式相同。你可以将程序元素的注解与其它的修饰符混合使用。你可以将注解放在与其他修饰符相同的行，也可以放在单独的行。是使用单独的行放置注解，还是将它们与其他修饰符混合使用，这是个人选择。按照惯例，程序元素的注解放在所有其他修饰符之前。让我们遵循这个惯例，将注解单独放在一行，如下所示。以下两种声明在技术上是相同的：

```
// 风格 #1
@Version(major=1, minor=0) public class VersionTest {
// 代码放在这里
}
// 风格 #2
public @Version(major=1, minor=0) class VersionTest {
// 代码放在这里
}
```

清单 1-3 展示了 `VersionTest` 类的示例代码。

```
// VersionTest.java
package com.jdojo.annotation;
// 类 VersionTest 的注解
@Version(major=1, minor=0)
public class VersionTest {
// 实例变量 xyz 的注解
@Version(major=1, minor=1)
private int xyz = 110;
// 构造方法 VersionTest() 的注解
@Version(major=1, minor=0)
public VersionTest() {
}
// 构造方法 VersionTest(int xyz) 的注解
@Version(major=1, minor=1)
public VersionTest(int xyz) {
this.xyz = xyz;
}
// printData() 方法的注解
@Version(major=1, minor=0)
public void printData() {
}
// setXyz() 方法的注解
@Version(major=1, minor=1)
public void setXyz(int xyz) {
// 局部变量 newValue 的注解
@Version(major=1, minor=2)
int newValue = xyz;
this.xyz = xyz;
}
}
清单 1-3.
一个带有注解元素的 VersionTest 类
```

在清单 1-3 中，你使用 `@Version` 注解来注解类声明、类字段、局部变量、构造方法和方法。`VersionTest` 类的代码中没有什么特别之处。你只是向类的各个元素添加了 `@Version` 注解。即使你移除所有 `@Version` 注解，`VersionTest` 类的行为也是一样的。需要强调的是，在程序中使用注解完全不会改变程序的行为。注解的真正好处在于在编译时和运行时读取它。

接下来你要对 `Version` 注解类型做什么？你已经将它声明为一个类型。你已经在你的 `VersionTest` 类中使用了它。你的下一步是在运行时读取它。让我们暂时推迟这一步；我将在后面的章节中详细讨论。我首先会更多地讨论注解类型的声明。

## 注解类型的限制

注解类型是一种特殊类型的接口，具有一些限制。我将在接下来的章节中介绍其中一些限制。



### 限制 #1

注解类型不能继承自另一个注解类型。也就是说，你不能在注解类型声明中使用 `extends` 子句。以下声明将无法编译，因为你使用了 `extends` 子句来声明 `WrongVersion` 注解类型：

```
// 无法编译
public @interface WrongVersion extends BasicVersion {
int extended();
}
```

每个注解类型都隐式继承自 `java.lang.annotation.Annotation` 接口，该接口声明如下：

```
package java.lang.annotation;
public interface Annotation {
boolean equals(Object obj);
int hashCode();
String toString();
Class annotationType();
}
```

这意味着 `Annotation` 接口中声明的所有四个方法在所有注解类型中都是可用的。这里需要提一句警告。你使用抽象方法声明来为注解类型声明元素。`Annotation` 接口中声明的方法并不会在注解类型中声明元素。你的 `Version` 注解类型只有两个元素，`major` 和 `minor`，它们是在 `Version` 类型本身中声明的。你不能像 `@Version(major=1, minor=2, toString="Hello")` 这样使用注解类型 `Version`。`Version` 注解类型并未将 `toString` 声明为一个元素。它从 `Annotation` 接口继承了 `toString()` 方法。

`Annotation` 接口中的前三个方法是来自 `Object` 类的方法。`annotationType()` 方法返回注解实例所属注解类型的类引用。Java 在运行时动态创建一个代理类，该类实现了注解类型。当你获取一个注解类型的实例时，该实例类就是动态生成的代理类，你可以通过注解实例上的 `getClass()` 方法获取其引用。如果你在运行时获取了 `Version` 注解类型的一个实例，其 `getClass()` 方法将返回动态生成的代理类的类引用，而其 `annotationType()` 方法将返回 `com.jdojo.annotation.Version` 注解类型的类引用。

### 限制 #2

注解类型中的方法声明不能指定任何参数。一个方法为注解类型声明一个元素。注解类型中的元素允许你将数据值与注解实例关联起来。注解中的方法声明不会被调用来执行任何类型的处理。可以将元素视为类中的一个实例变量，该类拥有该实例变量的 setter 和 getter 两个方法。对于注解，Java 运行时创建一个实现注解类型（它是一个接口）的代理类。每个注解实例都是该代理类的一个对象。你在注解类型中声明的方法成为你在注解中指定的该元素值的 getter 方法。Java 运行时将负责为注解元素设置指定的值。由于在注解类型中声明方法的目的是处理数据元素，因此你不需要（也不允许）在方法声明中指定任何参数。以下注解类型的声明将无法编译，因为它声明了一个 `concatenate()` 方法，该方法接受两个参数：

```
// 无法编译
public @interface WrongVersion {
// 不能有参数
String concatenate(int major, int minor);
}
```

### 限制 #3

注解类型中的方法声明不能包含 `throws` 子句。注解类型中的方法被定义为表示一个数据元素。抛出异常来表示数据值是没有意义的。以下注解类型的声明将无法编译，因为 `major()` 方法包含一个 `throws` 子句：

```
// 无法编译
public @interface WrongVersion {
int major() throws Exception; // 不能有 throws 子句
int minor(); // 可以
}
```

### 限制 #4

注解类型中声明的方法的返回类型必须是以下类型之一：

*   任何原始类型：`byte`、`short`、`int`、`long`、`float`、`double`、`boolean` 和 `char`
*   `java.lang.String`
*   `java.lang.Class`
*   一个枚举类型
*   一个注解类型
*   上述任何类型的数组，例如 `String[]`、`int[]` 等。返回类型不能是嵌套数组。例如，你不能有 `String[][]` 或 `int[][]` 这样的返回类型。

提示

这些数据类型限制背后的原因是，所有允许的数据类型的值都必须在源代码中表示，编译器应该能够表示这些值以进行编译时分析。

`Class` 返回类型需要一点解释。你可以使用泛型返回类型来代替 `Class` 类型，该返回类型将返回用户定义的类类型。假设你有一个 `Test` 类，并且你想在注解类型中声明一个返回类型为 `Test` 的方法。你可以像这样声明注解方法：

```
public @interface GoodOne {
Class element1();                 // 任何 Class 类型
Class element2();           // 仅 Test 类类型
Class element3(); // Test 或其子类类型
}
```

### 限制 #5

注解类型不能声明一个等同于重写 `Object` 类或 `Annotation` 接口中方法的方法。

### 限制 #6

注解类型不能是泛型。



## 注解元素的默认值

注解类型声明的语法允许你为其元素指定默认值。你可以选择不指定，但也可以为那些在声明中已设定默认值的注解元素赋值。元素的默认值可通过以下通用语法指定：

```
[修饰符] @interface  {
() default ;
}
```

关键字 `default` 用于指定默认值。默认值的类型必须与元素的数据类型兼容。

假设你有一个不常发布的产品，因此它不太可能有非零的次版本号。你可以通过为 `Version` 注解类型的 `minor` 元素指定默认值为零来简化它，如下所示：

```
public @interface Version {
int major();
int minor() default 0; // 将 minor 的默认值设为零
}
```

一旦为元素设置了默认值，在使用该类型注解时，你就不必再传递该元素的值。Java 会为缺失的元素使用默认值。

```
@Version(major=1)          // minor 为零，即其默认值
@Version(major=2)          // minor 为零，即其默认值
@Version(major=2, minor=1) // minor 为 1，即指定的值
```

所有默认值都必须是编译时常量。如何为数组类型指定默认值？你需要使用数组初始化语法。以下代码片段展示了如何为数组和其他数据类型指定默认值：

```
// 展示如何为不同类型元素分配默认值
public @interface DefaultTest {
double d() default 12.89;
int num() default 12;
int[] x() default {1, 2};
String s() default "Hello";
String[] s2() default {"abc", "xyz"};
Class c() default Exception.class;
Class[] c2() default {Exception.class, java.io.IOException.class};
}
```

元素的默认值不会随注解一起编译。当程序在运行时尝试读取元素的值时，它会从注解类型定义中读取。例如，当你使用 `@Version(major=2)` 时，这个注解实例会原样编译。它不会添加带有默认值零的 `minor` 元素。换句话说，这个注解在编译时不会被修改为 `@Version(major=2, minor=0)`。然而，当你在运行时读取该注解的 `minor` 元素值时，Java 会检测到 `minor` 元素的值未被指定。它会查阅 `Version` 注解类型定义以获取其默认值。这种机制的含义是，如果你更改了元素的默认值，那么每当程序尝试读取它时，都会读取到更改后的默认值，即使带注解的程序是在你更改默认值之前编译的。

## 注解类型及其实例

我经常使用术语“注解类型”和“注解”。注解类型是一种类似于接口的类型。理论上，你可以在任何可以使用接口类型的地方使用注解类型。实际上，我们将其使用限制为仅用于注解程序元素。你可以像下面这样声明一个注解类型的变量：

```
Version v = null; // 这里，Version 是一个注解类型
```

与接口类似，你也可以在类中实现一个注解类型。然而，你绝不应该这样做，因为这会破坏将注解类型作为一种新构造的目的。你应该始终在类中实现接口，而不是注解类型。从技术上讲，清单 1-4 中 `DoNotUseIt` 类的代码是有效的。但这仅用于演示目的。即使可行，也不要在类中实现注解。

```
// DoNotUseIt.java
package com.jdojo.annotation;
import java.lang.annotation.Annotation;
public class DoNotUseIt implements Version {
// 实现自 Version 注解类型的方法
@Override
public int major() {
return 0;
}
// 实现自 Version 注解类型的方法
@Override
public int minor() {
return 0;
}
// 实现自 Annotation 注解类型的方法，
// 该类型是 Version 注解类型的超类型
@Override
public Class annotationType() {
return null;
}
}
清单 1-4.
一个实现注解类型的类
```

Java 运行时将注解类型实现为一个代理类。对于你在程序中使用的每个注解，它都会为你提供一个实现了该注解类型的类的对象。你必须区分注解类型和该注解类型的实例（或对象）。在你的示例中，`Version` 是一个注解类型。每当你使用 `@Version(major=2, minor=4)` 时，你就是在创建 `Version` 注解类型的一个实例。注解类型的实例通常简称为注解。例如，我们说 `@Version(major=2, minor=4)` 是一个注解，或者是 `Version` 注解类型的一个实例。注解在程序中应该易于使用。语法 `@Version(...)` 是创建类、创建该类的对象以及为其元素设置值的简写形式。我将在本章后面介绍如何在运行时获取注解类型的对象。

## 使用注解

在本节中，我将讨论在声明注解类型时使用不同类型元素的细节。请记住，为注解元素提供的值必须是编译时常量表达式，并且你不能在注解中将 `null` 用作任何类型元素的值。

### 基本类型

注解类型中元素的数据类型可以是任何基本数据类型：`byte`、`short`、`int`、`long`、`float`、`double`、`boolean` 和 `char`。`Version` 注解类型声明了两个元素 `major` 和 `minor`，它们都是 `int` 数据类型。以下代码片段声明了一个名为 `PrimitiveAnnTest` 的注解类型：

```
public @interface PrimitiveAnnTest {
byte a();
short b();
int c();
long d();
float e();
double f();
boolean g();
char h();
}
```

你可以像这样使用 `PrimitiveAnnTest` 类型的实例：

```
@PrimitiveAnnTest(a=1, b=2, c=3, d=4, e=12.34F, f=1.89, g=true, h='Y')
```

你可以使用编译时常量表达式来指定注解元素的值。以下两个 `Version` 注解的实例是有效的，并且它们的元素具有相同的值：

```
@Version(major=2+1, minor=(int)13.2)
@Version(major=3, minor=13)
```



### 字符串类型

你可以在注解类型中使用 `String` 类型的元素。清单 1-5 包含了一个名为 `Name` 的注解类型的代码。它有两个元素 `first` 和 `last`，均为 `String` 类型。

```
// Name.java
package com.jdojo.annotation;
public @interface Name {
String first();
String last();
}
清单 1-5.
Name 注解类型，包含两个 String 类型的元素 first 和 last
```

以下代码片段展示了如何在程序中使用 `Name` 注解类型：

```
@Name(first="John", last="Jacobs")
public class NameTest {
@Name(first="Wally", last="Inman")
public void aMethod() {
// 更多代码...
}
}
```

在 `String` 类型元素的值表达式中使用字符串连接运算符（+）是有效的。以下两个注解是等价的：

```
@Name(first="Jo" + "hn", last="Ja" + "cobs")
@Name(first="John", last="Jacobs")
```

通常，当你希望使用编译时常量（例如 final 类变量）作为注解元素值的一部分时，你会在注解中使用字符串连接。在以下注解中，`Test` 是一个定义了名为 `UNKNOWN` 的编译时常量 `String` 类变量的类：

```
@Name(first="Mr. " + Test.UNKNWON, last=Test.UNKNOWN)
```

以下对 `@Name` 注解的使用是无效的，因为表达式 `new String("John")` 不是编译时常量表达式：

```
@Name(first=new String("John"), last="Jacobs")
```

### 类类型

在注解类型中使用 `Class` 类型作为元素的好处并不明显。通常，它用于工具/框架读取包含类类型元素的注解，并对元素值执行某些专门处理或生成代码。让我们通过一个使用类类型元素的简单示例来了解。假设你正在编写一个用于运行 Java 程序测试用例的测试运行器工具。你的注解将用于编写测试用例。如果测试用例在被测试运行器调用时必须抛出异常，你需要使用一个注解来指示这一点。让我们创建一个 `DefaultException` 类，如清单 1-6 所示。

```
// DefaultException.java
package com.jdojo.annotation;
public class DefaultException extends java.lang.Throwable {
public DefaultException() {
}
public DefaultException(String msg) {
super(msg);
}
}
清单 1-6.
继承自 Throwable 异常类的 DefaultException 类
```

清单 1-7 展示了 `TestCase` 注解类型的代码。

```
// TestCase.java
package com.jdojo.annotation;
import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
public @interface TestCase {
Class willThrow() default DefaultException.class;
}
清单 1-7.
TestCase 注解类型，其实例用于注解测试用例方法
```

`willThrow` 元素的返回类型被定义为 `Throwable` 类的通配符，这样用户只能指定 `Throwable` 类或其子类作为元素值。你也可以使用 `Class<?>` 类型作为 `willThrow` 元素的类型。然而，这将允许此注解类型的用户将任何类类型作为其值传递。请注意，你为 `TestCase` 注解类型使用了两个注解：`@Retention` 和 `@Target`。`@Retention` 注解类型指定 `@TestCase` 注解将在运行时可用。为 `TestCase` 注解类型使用 `RUNTIME` 保留策略是必要的，因为它旨在供测试运行器工具在运行时读取。`@Target` 注解声明 `TestCase` 注解只能用于注解方法。我将在后面讨论元注解的章节中详细介绍 `@Retention` 和 `@Target` 注解类型。清单 1-8 展示了 `TestCase` 注解类型的使用。

```
// PolicyTestCases.java
package com.jdojo.annotation;
import java.io.IOException;
public class PolicyTestCases {
// 必须抛出 IOException
@TestCase(willThrow=IOException.class)
public static void testCase1(){
// 代码...
}
// 我们不期望任何异常
@TestCase()
public static void testCase2(){
// 代码...
}
}
清单 1-8.
使用 TestCase 注解的测试用例
```

`testCase1()` 方法通过 `@TestCase` 注解指定它将抛出 `IOException`。测试运行器工具将确保当它调用此方法时，该方法确实抛出 `IOException`。否则，它将判定该测试用例失败。`testCase2()` 方法没有指定它将抛出异常。如果在运行测试时它抛出了异常，工具应判定此测试用例失败。

### 枚举类型

注解可以包含枚举类型的元素。假设你想声明一个名为 `Review` 的注解类型，它可以描述程序元素的代码审查状态。我们假设它有一个 `status` 元素，并且可以取四个值之一：`PENDING`、`FAILED`、`PASSED` 和 `PASSEDWITHCHANGES`。你可以将枚举声明为注解类型的成员。清单 1-9 展示了 `Review` 注解类型的代码。

```
// Review.java
package com.jdojo.annotation;
public @interface Review {
ReviewStatus status() default ReviewStatus.PENDING;
String comments() default "";
// ReviewStatus 枚举是 Review 注解类型的成员
public enum ReviewStatus {PENDING, FAILED, PASSED, PASSEDWITHCHANGES};
}
清单 1-9.
使用枚举类型元素的注解类型
```

提示

用作注解元素类型的枚举类型不必像本例中那样声明为注解类型的嵌套枚举类型。枚举类型也可以在注解类型外部声明。

`Review` 注解类型声明了一个 `ReviewStatus` 枚举类型，四个审查状态是该枚举的元素。它有两个元素：`status` 和 `comments`。`status` 元素的类型是枚举类型 `ReviewStatus`。`status` 元素的默认值是 `ReviewStatus.PENDING`。`comments` 元素的默认值是一个空字符串。

以下是 `Review` 注解类型的一些实例。你需要在程序中导入 `com.jdojo.annotation.Review.ReviewStatus` 枚举，才能使用 `ReviewStatus` 枚举类型的简单名称。

```
// 对 status 和 comments 使用默认值。也许代码是新的。
@Review()
// 将 status 保留为 Pending，但添加一些评论
@Review(comments="已安排在 2017 年 12 月 1 日进行代码审查")
// 审查失败并附上评论
@Review(status=ReviewStatus.FAILED, comments="需要处理错误")
// 审查通过，无评论
@Review(status=ReviewStatus.PASSED)
```

以下是注解一个 `Test` 类，表明它通过了代码审查的示例代码：

```
import com.jdojo.annotation.Review.ReviewStatus;
import com.jdojo.annotation.Review;
@Review(status=ReviewStatus.PASSED)
public class Test {
// 代码...
}
```



### 注解类型

在 Java 程序中，任何可以使用类型的地方都可以使用注解类型。例如，你可以将注解类型用作方法的返回类型。你也可以将注解类型用作另一个注解类型声明中元素的类型。假设你想要创建一个名为 `Description` 的新注解类型，它将包含程序元素的作者姓名、版本和注释。你可以重用你的 `Name` 和 `Version` 注解类型作为其 `name` 和 `version` 元素的类型。清单 1-10 展示了 `Description` 注解类型的代码。

```
// Description.java
package com.jdojo.annotation;
public @interface Description {
Name name();
Version version();
String comments() default "";
}
清单 1-10.
一个使用其他注解类型作为其元素的注解类型
```

要为注解类型的元素提供值，你需要使用创建注解类型实例的语法。例如，`@Version(major=1, minor=2)` 创建了一个 `Version` 注解的实例。请注意以下代码片段中一个注解嵌套在另一个注解中的情况：

```
@Description(name=@Name(first="John", last="Jacobs"),
version=@Version(major=1, minor=2),
comments="Just a test class")
public class Test {
// 代码写在这里
}
```

### 数组类型注解元素

注解可以拥有数组类型的元素。数组类型可以是以下类型之一：

*   基本类型
*   `java.lang.String` 类型
*   `java.lang.Class` 类型
*   枚举类型
*   注解类型

你需要在花括号内为数组元素指定值。数组元素之间用逗号分隔。假设你想用一份需要处理的事项清单的简短描述来注解你的程序元素。清单 1-11 为此目的创建了一个 `ToDo` 注解类型。

```
// ToDo.java
package com.jdojo.annotation;
public @interface ToDo {
String[] items();
}
清单 1-11.
以 String[] 作为其唯一元素的 ToDo 注解类型
```

以下代码片段展示了如何使用 `@ToDo` 注解：

```
@ToDo(items={"Add readFile method", "Add error handling"})
public class Test {
// 代码写在这里
}
```

如果数组中只有一个元素，你可以省略花括号。以下两个 `ToDo` 注解类型的实例是等价的：

```
@ToDo(items={"Add error handling"})
@ToDo(items="Add error handling")
```

提示

如果你没有有效的值传递给数组类型的元素，你可以使用一个空数组。例如，`@ToDo(items={})` 是一个有效的注解，其中 `items` 元素被赋值为一个空数组。

## 注解中不能使用 null 值

你不能在注解中使用 `null` 引用作为元素的值。请注意，允许对 `String` 类型元素使用空字符串，对数组类型元素使用空数组。使用以下注解将导致编译时错误：

```
@ToDo(items=null)
@Name(first=null, last="Jacobs")
```

## 简写注解语法

在某些情况下，简写注解语法使用起来稍微容易一些。假设你有一个 `Enabled` 注解类型，其元素具有默认值，如下所示：

```
public @interface Enabled {
boolean status() default true;
}
```

如果你想使用 `Enabled` 注解类型注解一个程序元素，并使用其元素的默认值，你可以使用 `@Enabled()` 语法。你不需要为 `status` 元素指定值，因为它有默认值。在这种情况下，你可以使用简写形式，允许你省略括号。你可以直接使用 `@Enabled` 而不是 `@Enabled()`。`Enabled` 注解可以以以下两种形式中的任何一种使用：

```
@Enabled
public class Test {
// 代码写在这里
}
@Enabled()
public class Test {
// 代码写在这里
}
```

只有一个元素的注解类型也有简写语法。如果你遵循注解类型中唯一元素的命名规则，就可以使用这种简写形式。元素的名称必须是 `value`。如果一个注解类型只有一个名为 `value` 的元素，你可以从注解中的 `name=value` 对中省略该名称。以下代码片段声明了一个 `Company` 注解类型，它只有一个名为 `value` 的元素：

```
public @interface Company {
String value(); // 元素名称是 value
}
```

当你使用 `Company` 注解时，可以省略 `name=value` 对中的名称，如下所示。如果你想在 `Company` 注解中使用元素名称，你总是可以这样做：`@Company(value="Abc Inc.")`。

```
@Company("Abc Inc.")
public class Test {
// 代码写在这里
}
```

即使元素数据类型是数组，你也可以使用这种从注解中省略元素名称的简写形式。考虑以下名为 `Reviewers` 的注解类型：

```
public @interface Reviewers {
String[] value(); // 元素名称是 value
}
```

由于 `Reviewers` 注解类型只有一个名为 `value` 的元素，你可以在使用它时省略元素名称。

```
// 无需指定元素名称
@Reviewers({"John Jacobs", "Wally Inman"})
public class Test {
// 代码写在这里
}
```

如果你只为 `Reviewers` 注解类型的 `value` 元素指定数组中的一个元素，你也可以省略花括号。

```
@Reviewers("John Jacobs")
public class Test {
// 代码写在这里
}
```

你刚刚看到了几个使用 `value` 作为元素名称的例子。以下是在注解中省略元素名称的一般规则：如果使用注解时只提供一个值，则假定元素名称为 `value`。这意味着，要省略注解中的元素名称，并不要求注解类型中只有一个名为 `value` 的元素。如果你有一个注解类型，它有一个名为 `value` 的元素（带或不带默认值），并且所有其他元素都有默认值，你仍然可以在该类型的注解实例中省略元素名称。以下是一些说明此规则的示例：

```
public @interface A {
String value();
int id() default 10;
}
// 等同于 @A(value="Hello", id=10)
@A("Hello")
public class Test {
// 代码写在这里
}
// 无法编译。必须只使用一个值才能省略元素名称
@A("Hello", id=16)
public class WontCompile {
// 代码写在这里
}
// 可以。传递多个值时必须使用 name=value 对
@A(value="Hello", id=16)
public class Test {
// 代码写在这里
}
```

## 标记注解类型

标记注解类型不声明任何元素，甚至不声明带有默认值的元素。通常，标记注解被注解处理工具使用，这些工具基于标记注解类型生成样板代码。

```
public @interface Marker {
// 没有元素声明
}
@Marker
public class Test {
// 代码写在这里
}
```

## 元注解类型

元注解类型用于注解其他注解类型的声明。以下是元注解类型：

*   `Target`
*   `Retention`
*   `Inherited`
*   `Documented`
*   `Repeatable`

元注解类型是 Java 类库的一部分。它们在 `java.lang.annotation` 包中声明。我将在后续章节中详细讨论元注解类型。

提示

`java.lang.annotation` 包包含一个 `Native` 注解类型，它不是一个元注解。它用于注解字段，表明该字段可能被本地代码引用。它是一个标记注解。通常，它被那些基于此注解生成某些代码的工具使用。



### 目标注解类型

`Target` 注解类型用于指定注解类型可以使用的上下文。它只有一个名为 `value` 的元素，该元素是 `java.lang.annotation.ElementType` 枚举类型的一个数组。表 1-1 列出了 `ElementType` 枚举中的所有常量。

表 1-1.

java.lang.annotation.ElementType 枚举中的常量列表

| 常量名 | 描述 |
| --- | --- |
| `ANNOTATION_TYPE` | 用于注解另一个注解类型的声明。这使得该注解类型成为元注解。 |
| `CONSTRUCTOR` | 用于注解构造方法。 |
| `FIELD` | 用于注解字段和枚举常量。 |
| `LOCAL_VARIABLE` | 用于注解局部变量。 |
| `METHOD` | 用于注解方法。 |
| `MODULE` | 用于注解模块。该常量在 Java 9 中添加。 |
| `PACKAGE` | 用于注解包声明。 |
| `PARAMETER` | 用于注解参数。 |
| `TYPE` | 用于注解类、接口（包括注解类型）或枚举的声明。 |
| `TYPE_PARAMETER` | 用于注解泛型类、接口、方法等中的类型参数。该常量在 Java 8 中添加。 |
| `TYPE_USE` | 用于注解所有类型的使用。该常量在 Java 8 中添加。该注解也可用于可以使用 `ElementType.TYPE` 和 `ElementType.TYPE_PARAMETER` 注解的地方。它还可以用在构造方法之前，在这种情况下，它表示由该构造方法创建的对象。 |

以下 `Version` 注解类型的声明使用 `Target` 元注解来注解该注解类型的声明，这指定了 `Version` 注解类型只能用于三种程序元素：任何类型（类、接口、枚举和注解类型）、构造方法和方法。

```
// Version.java
package com.jdojo.annotation;
import java.lang.annotation.Target;
import java.lang.annotation.ElementType;
@Target({ElementType.TYPE, ElementType.CONSTRUCTOR, ElementType.METHOD})
public @interface Version {
int major();
int minor();
}
```

`Version` 注解类型不能用于其 `Target` 注解中指定的三种类型之外的任何程序元素。以下用法是错误的，因为它被用于实例变量（字段）：

```
public class WontCompile {
// 编译时错误。Version 注解不能用于字段。
@Version(major = 1, minor = 1)
int id = 110;
}
```

以下 `Version` 注解的用法是有效的：

```
// 正确。类类型声明
@Version(major = 1, minor = 0)
public class VersionTest {
// 正确。构造方法声明
@Version(major = 1, minor = 0)
public VersionTest() {
// 代码写在这里
}
// 正确。方法声明
@Version(major = 1, minor = 1)
public void doSomething() {
// 代码写在这里
}
}
```

在 Java 8 之前，注解只允许用于方法的形参以及包、类、方法、字段和局部变量的声明。Java 8 增加了对在类型的所有使用和类型参数声明上使用注解的支持。“类型的所有使用”这个短语需要稍作解释。类型在许多上下文中被使用，例如，在 `extends` 子句之后作为超类型，在 `new` 运算符之后的对象创建表达式中，在类型转换中，在 `throws` 子句中，等等。从 Java 8 开始，只要使用了类型，注解就可以出现在该类型的简单名称之前。请注意，类型的简单名称可能仅用作名称，而不是类型，例如在 `import` 语句中。考虑清单 1-12 和清单 1-13 中所示的 `Fatal` 和 `NonZero` 注解类型的声明。

```
// Fatal.java
package com.jdojo.annotation;
import java.lang.annotation.ElementType;
import java.lang.annotation.Target;
@Target({ElementType.TYPE_USE})
public @interface Fatal {
}
清单 1-12.
一个可用于任何类型使用的 Fatal 注解类型
```

```
// NonZero.java
package com.jdojo.annotation;
import java.lang.annotation.ElementType;
import java.lang.annotation.Target;
@Target({ElementType.TYPE_USE})
public @interface NonZero {
}
清单 1-13.
一个可用于任何类型使用的 NonZero 注解类型
```

`Fatal` 和 `NonZero` 注解类型可以在任何使用类型的地方使用。它们在以下上下文中的使用是有效的：

```
public class Test {
public void processData() throws @Fatal Exception {
double value = getValue();
int roundedValue = (@NonZero int) value;
Test t = new @Fatal Test();
// 更多代码写在这里
}
public double getValue() {
double value = 189.98;
// 更多代码写在这里
return value;
}
}
```

提示

如果你没有使用 `Target` 注解类型来注解一个注解类型，那么该注解类型可以在任何地方使用，但类型参数声明除外。

### 保留注解类型

你可以将注解用于不同的目的。你可能希望仅将它们用于文档目的、由编译器处理，和/或在运行时使用它们。注解可以在三个级别上保留。

*   仅源代码
*   仅类文件（默认）
*   类文件和运行时

`Retention` 元注解类型用于指定 Java 应如何保留注解类型的注解实例。这也称为注解类型的保留策略。如果注解类型具有“仅源代码”保留策略，则其类型的实例在编译成类文件时会被移除。如果保留策略是“仅类文件”，则注解实例会保留在类文件中，但无法在运行时读取。如果保留策略是“类文件和运行时”（通常简称为运行时），则注解实例会保留在类文件中，并且可以在运行时读取。

`Retention` 元注解类型声明了一个名为 `value` 的元素，该元素是 `java.lang.annotation.RetentionPolicy` 枚举类型。`RetentionPolicy` 枚举有三个常量：`SOURCE`、`CLASS` 和 `RUNTIME`，分别用于指定仅源代码、仅类文件以及类文件和运行时的保留策略。以下代码在 `Version` 注解类型上使用了 `Retention` 元注解。它指定 `Version` 注解应在运行时可用。请注意在 `Version` 注解类型上使用了两个元注解：`Target` 和 `Retention`。

```
// Version.java
package com.jdojo.annotation;
import java.lang.annotation.Target;
import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
@Target({ElementType.TYPE, ElementType.CONSTRUCTOR,
ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
public @interface Version {
int major();
int minor();
}
```

提示

如果你没有在注解类型上使用 `Retention` 元注解，其保留策略默认为仅类文件。这意味着你将无法在运行时读取这些注解。你一开始可能会犯这个常见错误。你会尝试读取注解，但运行时不会返回任何值。在尝试在运行时读取注解之前，请确保你的注解类型已使用保留策略为 `RetentionPolicy.RUNTIME` 的 `Retention` 元注解进行了注解。无论注解类型的保留策略如何，局部变量声明上的注解永远不会在类文件或运行时中可用。此限制的原因是 Java 运行时不允许你在运行时使用反射访问局部变量；除非你可以在运行时访问局部变量，否则你无法读取它们的注解。



### 继承式注解类型

`Inherited` 注解类型是一种标记型元注解类型。如果一个注解类型使用了 `Inherited` 元注解进行标注，那么它的实例会被子类声明所继承。如果某个注解类型用于标注类声明以外的任何程序元素，则此元注解不会产生任何效果。让我们考虑两个注解类型声明：`Ann2` 和 `Ann3`。请注意，`Ann2` 没有使用 `Inherited` 元注解进行标注，而 `Ann3` 则使用了。

```
public @interface Ann2 {
int id();
}
@Inherited
public @interface Ann3 {
int id();
}
```

让我们按如下方式声明两个类 `A` 和 `B`。请注意，类 `B` 继承了类 `A`。

```
@Ann2(id=505)
@Ann3(id=707)
public class A {
// 类 A 的代码写在这里
}
// 类 B 从类 A 继承了 Ann3(id=707) 注解
public class B extends A {
// 类 B 的代码写在这里
}
```

在这段代码片段中，类 `B` 从类 `A` 继承了 `@Ann3(id=707)` 注解，因为 `Ann3` 注解类型已使用 `Inherited` 元注解进行了标注。类 `B` 不会继承 `@Ann2(id=505)` 注解，因为 `Ann2` 注解类型没有使用 `Inherited` 元注解进行标注。

### 文档化注解类型

`Documented` 注解类型是一种标记型元注解类型。如果一个注解类型使用了 `Documented` 注解进行标注，Javadoc 工具将为其所有实例生成文档。清单 1-14 包含了 `Version` 注解类型最终版本的代码，该注解类型已使用 `Documented` 元注解进行了标注。

```
// Version.java
package com.jdojo.annotation;
import java.lang.annotation.Documented;
import java.lang.annotation.Target;
import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
@Target({ElementType.TYPE, ElementType.CONSTRUCTOR, ElementType.METHOD, ElementType.MODULE,
ElementType.PACKAGE, ElementType.LOCAL_VARIABLE, ElementType.TYPE_USE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface Version {
int major();
int minor();
}
清单 1-14.
Version 注解类型的最终版本
```

假设你使用 `Version` 注解类型标注了一个 `Test` 类，如下所示：

```
package com.jdojo.annotation;
@Version(major=1, minor=0)
public class Test {
// Test 类的代码写在这里
}
```

当你使用 Javadoc 工具为 `Test` 类生成文档时，`Test` 类声明上的 `Version` 注解也会作为文档的一部分被生成。如果你从 `Version` 注解类型声明中移除 `Documented` 注解，那么 `Test` 类的文档将不会包含其 `Version` 注解的信息。

### 可重复注解类型

在 Java 8 之前，你不能在同一个上下文中重复使用同一个注解。例如，在 Java 7 中，以下对 `Version` 注解的重复使用会产生编译时错误：

```
@Version(major=1, minor=1)
@Version(major=1, minor=2)
public class Test {
// 代码写在这里
}
```

Java 8 新增了 `Repeatable` 元注解类型。如果允许重复使用某个注解类型，则其声明必须使用 `@Repeatable` 注解进行标注。`Repeatable` 注解类型只有一个名为 `value` 的元素，其类型是另一个注解类型的类类型。创建一个可重复注解类型分为两步：

*   声明一个注解类型（例如 `T`），并使用 `Repeatable` 元注解对其进行标注。将该注解的值指定为另一个注解，该注解被称为所声明的可重复注解类型的容器注解。
*   声明容器注解类型，其中包含一个元素，该元素是可重复注解的数组。

清单 1-15 和清单 1-16 包含了 `ChangeLog` 和 `ChangeLogs` 注解类型的声明。`ChangeLog` 使用了 `@Repeatable(ChangeLogs.class)` 注解进行标注，这意味着它是一个可重复注解类型，并且其容器注解类型是 `ChangeLogs`。

```
// ChangeLog.java
package com.jdojo.annotation;
import java.lang.annotation.Repeatable;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
@Retention(RetentionPolicy.RUNTIME)
@Repeatable(ChangeLogs.class)
public @interface ChangeLog {
String date();
String comments();
}
清单 1-15.
一个使用 ChangeLogs 作为容器注解类型的可重复注解类型
```

```
// ChangeLogs.java
package com.jdojo.annotation;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
@Retention(RetentionPolicy.RUNTIME)
public @interface ChangeLogs {
ChangeLog[] value();
}
清单 1-16.
ChangeLog 可重复注解类型的容器注解类型
```

你可以使用 `ChangeLog` 注解来记录 `Test` 类的变更历史，如下所示：

```
@ChangeLog(date="08/28/2017", comments="声明了该类")
@ChangeLog(date="09/21/2017", comments="添加了 process() 方法")
public class Test {
public static void process() {
// 代码写在这里
}
}
```

## 常用的标准注解

Java API 定义了许多标准注解类型。本节讨论四种最常用的标准注解。它们定义在 `java.lang` 包中。它们是：

*   `Deprecated`
*   `Override`
*   `SuppressWarnings`
*   `FunctionalInterface`

### 弃用 API

在 Java 中弃用 API 是一种提供 API 生命周期信息的方式。你可以弃用模块、包、类型、构造器、方法、字段、参数和局部变量。当你弃用一个 API 时，你是在告诉它的使用者：

*   不要使用该 API，因为它很危险。
*   迁移离开该 API，因为存在更好的替代品。
*   迁移离开该 API，因为该 API 将在未来的版本中被移除。



#### 如何弃用一个 API

JDK 包含两种用于弃用 API 的结构：

*   `@deprecated` Javadoc 标签
*   `java.lang.Deprecated` 注解类型

`@deprecated` Javadoc 标签是在 JDK 1.1 中添加的，它允许你利用 HTML 丰富的文本格式功能来指定弃用的详细信息。`java.lang.Deprecated` 注解类型是在 JDK 5.0 中添加的，可用于标记已弃用的 API 元素。在 JDK 9 之前，`Deprecated` 注解类型不包含任何元素。它在运行时被保留。

`@deprecated` 标签和 `@Deprecated` 注解应该一起使用。两者应同时存在或同时不存在。`@Deprecation` 注解不允许你指定弃用的描述，因此你必须使用 `@deprecated` 标签来提供描述。

提示

在 API 元素上使用 `@deprecated` 标签但不使用 `@Deprecated` 注解，会产生编译器警告。在 JDK 9 之前，你需要使用 `-Xlint:dep-ann` 编译器标志才能看到此类警告。

清单 1-17 包含一个名为 `FileCopier` 的类的声明。假设此类作为库的一部分发布。

```
// FileCopier.java
package com.jdojo.deprecation;
import java.io.File;
/**
* 该类包含用于复制文件和目录的静态方法。
*
* @deprecated 自 1.4 版本起弃用。使用不安全。请改用
* java.nio.file.Files 类。此类
* 将在本库的未来版本中移除。
*
* @since 1.2
*/
@Deprecated
public class FileCopier {
// 不支持直接实例化
private FileCopier() {
}
/**
* 将 src 的内容复制到 dst。
* @param src 源文件
* @param dst 目标文件
* @return 如果复制成功则返回 true，否则返回 false。
*/
public static boolean copy(File src, File dst) {
// 更多代码在此处
return true;
}
// 更多代码在此处
}
清单 1-17.
一个 FileCopier 工具类
```

`FileCopier` 类通过 `@Deprecated` 注解被标记为弃用。其 Javadoc 使用 `@deprecated` 标签来提供弃用的详细信息，例如弃用时间、替代方案以及移除通知。在 JDK 9 之前，`@Deprecated` 注解类型不包含任何元素，因此你必须在已弃用 API 的 Javadoc 中使用 `@deprecated` 标签提供所有弃用细节。请注意，Javadoc 中使用的 `@since` 标签表明 `FileCopier` 类自本库的 1.2 版本起就已存在，而 `@deprecated` 标签表明该类自本库的 1.4 版本起已被弃用。

Javadoc 工具会将 `@deprecated` 标签的内容移动到生成的 Javadoc 的顶部，以引起读者的注意。当非弃用代码使用已弃用的 API 时，编译器会生成警告。使用 `@Deprecated` 注解标记 API 本身不会生成警告；但是，使用已被 `@Deprecated` 注解标记的 API 则会生成警告。如果你在类本身之外使用 `FileCopier` 类，你将收到关于使用已弃用类的编译时警告。

#### JDK 9 中对弃用注解的增强

假设你编译了代码并将其部署到生产环境。如果你升级了 JDK 版本或包含你的旧应用程序使用的新弃用 API 的库/框架，你将不会收到任何警告，从而错失迁移远离弃用 API 的机会。你必须重新编译代码才能收到警告。之前没有工具可以扫描和分析编译后的代码（例如 JAR 文件）并报告弃用 API 的使用情况。更糟糕的情况是，当弃用 API 从较新版本中移除时，你旧的编译代码会遇到意外的运行时错误。开发人员在查看已弃用元素的 Javadoc 时也会感到困惑——无法表达 API 何时被弃用以及该弃用 API 是否会在未来版本中被移除。你所能做的只是将这些信息作为 `@deprecated` 标签的一部分以文本形式指定。JDK 9 试图通过增强 `@Deprecated` 注解来解决这些问题。该注解在 JDK 9 中新增了两个元素：`since` 和 `forRemoval`。它们的声明如下：

*   `String since() default "";`
*   `boolean forRemoval() default false;`

这两个新元素都有指定的默认值，因此不会破坏注解的现有用法。`since` 元素指定被注解的 API 元素开始弃用的版本。它是一个字符串，建议遵循与 JDK 版本方案相同的版本命名约定，例如 JDK 9 使用 "9"。其默认值为空字符串。请注意，JDK 9 没有向 `@Deprecated` 注解类型添加一个元素来指定弃用的描述。这样做有两个原因：

*   该注解在运行时被保留。向注解添加描述性文本会增加运行时内存。
*   描述性文本不能只是纯文本。例如，它需要提供指向已弃用 API 替代方案的链接。现有的 `@deprecated` Javadoc 标签已经提供了此功能。

`forRemoval` 元素表示被注解的 API 元素将在未来版本中被移除，你应该迁移远离该 API。其默认值为 `false`。

提示

元素上的 `@since` Javadoc 标签指示 API 元素何时被添加，而 `@Deprecated` 注解的 `since` 元素指示 API 元素何时被弃用。在 JDK 9 中，已做出合理努力，在 Java SE API 中大多数（如果不是全部）使用 `@Deprecated` 注解的地方回填了这两个元素的值。

在 JDK 9 之前，弃用警告是基于 API 元素上的 `@Deprecated` 注解及其使用位置发出的，如表 1-2 所示。当在非弃用的使用位置使用已弃用的 API 元素时，会发出警告。如果声明及其使用位置都被弃用，则不会发出警告。你可以通过使用 `@SuppressWarnings("deprecation")` 注解标记使用位置来抑制弃用警告。

表 1-2.

JDK 9 之前发出的弃用警告矩阵

| API 使用位置 | API 声明位置 |
| --- | --- |
|   | 未弃用 | 已弃用 |
| --- | --- | --- |
| 未弃用 | `N` | `W` |
| 已弃用 | `N` | `N` |
| N = 无警告，W = 警告 |

在 `@Deprecation` 注解类型中添加 `forRemoval` 元素又增加了五种使用情况。当 API 被弃用且 `forRemoval` 设置为 `false` 时，这种弃用称为普通弃用，此类情况下发出的警告称为普通弃用警告。当 API 被弃用且 `forRemoval` 设置为 `true` 时，这种弃用称为最终弃用，此类情况下发出的警告称为最终弃用警告或移除警告。表 1-3 显示了 JDK 9 中发出的弃用警告矩阵。

表 1-3.



JDK9 中发出的弃用警告矩阵

| API 使用方 | API 声明方 |
| --- | --- |
|   | 未弃用 | 普通弃用 | 最终弃用 |
| --- | --- | --- | --- |
| 未弃用 | `N` | `OW` | `RW` |
| 普通弃用 | `N` | `N` | `RW` |
| 最终弃用 | `N` | `N` | `RW` |
| `N` = 无警告，OW = 普通弃用警告，RW = 移除弃用警告 |

为了向后兼容，表 1-3 中左上角的四种使用情况与表 1-2 相同。也就是说，如果你的代码在 JDK8 中生成了弃用警告，那么在 JDK9 中它将继续生成普通弃用警告。如果 API 已被最终弃用，则无论使用方是否被弃用，其使用位置都将生成移除警告。

在 JDK9 中，有一种情况发出的警告需要稍作解释，即 API 及其使用方均被最终弃用。API 和使用它的代码都已被弃用，并且将来都会被移除，那么在这种情况下收到警告有什么意义呢？这样做是为了覆盖最终弃用的 API 及其使用方位于两个不同代码库中并独立维护的情况。如果使用方代码库比 API 代码库存活得更久，那么使用方将收到意外的运行时错误，因为它使用的 API 已不复存在。在使用方发出警告将使其维护者有机会规划替代方案，以防最终弃用的 API 在使用方代码之前被移除。

#### 抑制弃用警告

JDK9 中引入的移除警告为抑制弃用警告增加了一个新的用例。在 JDK9 之前，你可以通过在使用方添加 `@SuppressWarnings("deprecation")` 注解来抑制所有弃用警告。考虑一个场景：

*   在 JDK8 中，一个 API 被弃用，并且使用方抑制了弃用警告。
*   在 JDK9 中，该 API 的弃用状态从普通弃用变为最终弃用。
*   使用方在 JDK9 中编译正常，因为它已在 JDK8 中抑制了弃用警告。
*   该 API 被移除，使用方在未收到任何提前移除警告的情况下遇到了意外的运行时错误。

为了覆盖此类场景，JDK9 在使用 `@SuppressWarnings("deprecation")` 时不会抑制移除警告。它仅抑制普通弃用警告。要抑制移除警告，你需要使用 `@SuppressWarnings("removal")`。如果你想同时抑制普通弃用和移除弃用警告，则需要使用 `@SuppressWarnings({"deprecation", "removal"})`。

#### 一个示例

在本节中，我将通过一个简单的示例向你展示弃用 API 的所有用例，包括使用弃用 API 以及是否抑制警告。在示例中，我只弃用了方法，并使用它们生成编译时警告。但是，你并不局限于只弃用方法。方法上的注释应有助于你理解预期行为。清单 1-18 包含一个名为 `Box` 的类的代码。该类包含三个方法——分别对应弃用的每个类别：未弃用、普通弃用和最终弃用。我保持该类简单，以便你可以专注于所使用的弃用。编译 `Box` 类不会生成任何弃用警告，因为该类没有使用任何弃用的 API，而是包含了弃用的 API。

```
// Box.java
package com.jdojo.annotation;
/**
* 此类用于演示如何弃用 API。
*/
public class Box {
/**
* 未弃用
*/
public static void notDeprecated() {
System.out.println("notDeprecated...");
}
/**
* 普通弃用。
* @deprecated 请勿使用。
*/
@Deprecated(since="2")
public static void deprecatedOrdinarily() {
System.out.println("deprecatedOrdinarily...");
}
/**
* 最终弃用。
* @deprecated 它将在未来版本中被移除。请立即迁移你的代码。
*/
@Deprecated(since="2", forRemoval=true)
public static void deprecatedTerminally() {
System.out.println("deprecatedTerminally...");
}
}
清单 1-18.
一个包含三种类型方法的 Box 类：未弃用、普通弃用和最终弃用
```

清单 1-19 包含一个 `BoxTest` 类的代码。该类使用了 `Box` 类的所有方法。`BoxTest` 类中的一些方法已被普通弃用和最终弃用。前九个方法对应表 1-3 中的九个用例，这些用例将生成四个弃用警告——一个普通警告和三个最终警告。名为 `m4X()`（其中 `X` 是数字）的方法向你展示了如何抑制普通弃用和最终弃用警告。

```
// BoxTest.java
package com.jdojo.annotation;
public class BoxTest {
/**
* API: 未弃用
* 使用方: 未弃用
* 弃用警告: 无警告
*/
public static void m11() {
Box.notDeprecated();
}
/**
* API: 普通弃用
* 使用方: 未弃用
* 弃用警告: 无警告
*/
public static void m12() {
Box.deprecatedOrdinarily();
}
/**
* API: 最终弃用
* 使用方: 未弃用
* 弃用警告: 移除警告
*/
public static void m13() {
Box.deprecatedTerminally();
}
/**
* API: 未弃用
* 使用方: 普通弃用
* 弃用警告: 无警告
* @deprecated 使用有风险。
*/
@Deprecated(since="1.1")
public static void m21() {
Box.notDeprecated();
}
/**
* API: 普通弃用
* 使用方: 普通弃用
* 弃用警告: 无警告
* @deprecated 使用有风险。
*/
@Deprecated(since="1.1")
public static void m22() {
Box.deprecatedOrdinarily();
}
/**
* API: 最终弃用
* 使用方: 普通弃用
* 弃用警告: 移除警告
* @deprecated 使用有风险。
*/
@Deprecated(since="1.1")
public static void m23() {
Box.deprecatedTerminally();
}
/**
* API: 未弃用
* 使用方: 最终弃用
* 弃用警告: 无警告
* @deprecated 即将移除。
*/
@Deprecated(since="1.1", forRemoval=true)
public static void m31() {
Box.notDeprecated();
}
/**
* API: 普通弃用
* 使用方: 最终弃用
* 弃用警告: 无警告
* @deprecated 即将移除。
*/
@Deprecated(since="1.1", forRemoval=true)
public static void m32() {
Box.deprecatedOrdinarily();
}
/**
* API: 最终弃用
* 使用方: 最终弃用
* 弃用警告: 移除警告
* @deprecated 即将移除。
*/
@Deprecated(since="1.1", forRemoval=true)
public static void m33() {
Box.deprecatedTerminally();
}
/**
* API: 普通弃用和最终弃用
* 使用方: 未弃用
* 弃用警告: 普通警告和移除警告
*/
public static void m41() {
Box.deprecatedOrdinarily();
Box.deprecatedTerminally();
}
/**
* API: 普通弃用和最终弃用
* 使用方: 未弃用
* 弃用警告: 普通警告
*/
@SuppressWarnings("deprecation")
public static void m42() {
Box.deprecatedOrdinarily();
Box.deprecatedTerminally();
}
/**
* API: 普通弃用和最终弃用
* 使用方: 未弃用
* 弃用警告: 移除警告
*/
@SuppressWarnings("removal")
public static void m43() {
Box.deprecatedOrdinarily();
Box.deprecatedTerminally();
}
/**
* API: 普通弃用和最终弃用
* 使用方: 未弃用
* 弃用警告: 移除警告
*/
@SuppressWarnings({"deprecation", "removal"})
public static void m44() {
Box.deprecatedOrdinarily();
Box.deprecatedTerminally();
}
}
清单 1-19.
一个使用弃用 API 并抑制弃用警告的 BoxTest 类
```

你需要使用 `-Xlint: deprecation` 编译器标志编译 `BoxTest` 类，以便编译器发出弃用警告。请注意，以下命令应在一行中输入，而不是两行。



```
C:\Java9LanguageFeatures>javac -Xlint:deprecation -d build\modules\jdojo.annotation
src\jdojo.annotation\classes\com\jdojo\annotation\BoxTest.java
src\jdojo.annotation\classes\com\jdojo\annotation\BoxTest.java:20: 警告: [deprecation] Box 中的 deprecatedOrdinarily() 已弃用
Box.deprecatedOrdinarily();
^
src\jdojo.annotation\classes\com\jdojo\annotation\BoxTest.java:29: 警告: [removal] Box 中的 deprecatedTerminally() 已弃用并标记为待移除
Box.deprecatedTerminally();
^
src\jdojo.annotation\classes\com\jdojo\annotation\BoxTest.java:62: 警告: [removal] Box 中的 deprecatedTerminally() 已弃用并标记为待移除
Box.deprecatedTerminally();
^
src\jdojo.annotation\classes\com\jdojo\annotation\BoxTest.java:95: 警告: [removal] Box 中的 deprecatedTerminally() 已弃用并标记为待移除
Box.deprecatedTerminally();
^
src\jdojo.annotation\classes\com\jdojo\annotation\BoxTest.java:104: 警告: [deprecation] Box 中的 deprecatedOrdinarily() 已弃用
Box.deprecatedOrdinarily();
^
src\jdojo.annotation\classes\com\jdojo\annotation\BoxTest.java:105: 警告: [removal] Box 中的 deprecatedTerminally() 已弃用并标记为待移除
Box.deprecatedTerminally();
^
src\jdojo.annotation\classes\com\jdojo\annotation\BoxTest.java:116: 警告: [removal] Box 中的 deprecatedTerminally() 已弃用并标记为待移除
Box.deprecatedTerminally();
^
src\jdojo.annotation\classes\com\jdojo\annotation\BoxTest.java:126: 警告: [deprecation] Box 中的 deprecatedOrdinarily() 已弃用
Box.deprecatedOrdinarily();
^
8 个警告
```

#### 弃用 API 的静态分析

回顾一下，弃用警告是编译时警告。如果你已部署应用的编译代码开始使用一个普通弃用的 API，或者因为某个曾经有效的 API 已被最终弃用并移除而生成运行时错误，你将不会收到任何警告。在 JDK9 之前，当你升级 JDK 或其他库/框架时，必须重新编译源代码才能看到弃用警告。JDK9 通过提供一个名为 `jdeprscan` 的静态分析工具来改善这种情况，该工具扫描编译后的代码，为你提供正在使用的弃用 API 列表。目前，该工具仅报告弃用的 JDK API 的使用情况。如果你的编译代码使用了其他库（例如 Spring 或 Hibernate）或你自己的库中的弃用 API，此工具将不会报告这些使用情况。

`jdeprscan` 工具位于 `JDK_HOME\bin` 目录中。使用该工具的一般语法如下：

```
jdeprscan [选项] {目录|jar|类}
```

这里，`[选项]` 是零个或多个选项的列表。你可以指定一个以空格分隔的目录、JAR 包、完全限定类名或类文件路径列表作为要扫描的参数。可用的选项如下：

*   `-l, --list`
*   `--class-path <类路径>`
*   `--for-removal`
*   `--release <6|7|8|9>`
*   `-v, --verbose`
*   `--version`
*   `--full-version`
*   `-h, --help`

`--list` 选项列出 Java SE 中弃用 API 的集合。使用此选项时，不应指定任何指定编译类位置的参数。

`--class-path` 指定用于在扫描期间查找依赖类的类路径。

`--for-removal` 选项将扫描或列表限制为仅那些已标记为待移除的弃用 API。它只能与版本 9 或更高版本一起使用，因为在 JDK9 之前，`@Deprecated` 注解类型不包含 `forRemoval` 元素。

`--release` 选项指定在扫描期间提供弃用 API 集合的 Java SE 版本。例如，要列出 JDK 6 中所有弃用的 API，你可以按如下方式使用该工具：

```
jdeprscan --list --release 6
```

`--verbose` 选项在扫描过程中打印附加消息。

`--version` 和 `--full-version` 选项分别打印 `jdeprscan` 工具的缩写版本和完整版本。

`--help` 选项打印关于 `jdeprscan` 工具的详细帮助信息。

清单 1-20 包含一个 `JDeprScanTest` 类的代码。代码很简单。它仅用于编译，不用于运行。运行它不会产生任何有趣的输出。它创建了两个线程。一个线程使用 `Thread` 类的 `stop()` 方法停止，另一个线程使用 `Thread` 类的 `destroy()` 方法销毁。`stop()` 和 `destroy()` 方法分别自 JDK 1.2 和 JDK 1.5 起被普通弃用。JDK9 最终弃用了 `destroy()` 方法，同时继续将 `stop()` 方法保持为普通弃用。我在以下示例中使用这个类。

```
// JDeprScanTest.java
package com.jdojo.annotation;
public class JDeprScanTest {
public static void main(String[] args) {
Thread t = new Thread(() -> System.out.println("测试"));
t.start();
t.stop();
Thread t2 = new Thread(() -> System.out.println("测试"));
t2.start();
t2.destroy();
}
}
清单 1-20.
一个 JDeprScanTest 类，它使用了 Thread 类的普通弃用方法 stop() 和最终弃用方法 destroy()
```

以下命令打印 JDK9 中所有弃用 API 的列表。它将打印一个很长的列表。该命令需要几秒钟才能开始打印结果，因为它会扫描整个 JDK。

```
C:\Java9LanguageFeatures>jdeprscan --list
@Deprecated java.lang.ClassLoader
javax.tools.ToolProvider.getSystemToolClassLoader()
...
```

以下命令打印 JDK9 中所有最终弃用的 API。也就是说，它打印所有已标记为在将来版本中移除的弃用 API：

```
C:\Java9LanguageFeatures>jdeprscan --list --for-removal
@Deprecated(since="9", forRemoval=true) class java.lang.Compiler
...
```

以下命令打印 JDK8 中所有弃用 API 的列表：

```
C:\ Java9LanguageFeatures >jdeprscan --list --release 8
@Deprecated class javax.swing.text.TableView.TableCell
...
```

以下命令打印 `java.lang.Thread` 类使用的弃用 API 列表。

```
C:\Java9LanguageFeatures>jdeprscan java.lang.Thread
class java/lang/Thread uses deprecated method java/lang/Thread::resume()V
```

请注意，前面的命令不会打印 `Thread` 类中弃用的 API 列表。相反，它打印的是 `Thread` 类中使用那些弃用 API 的 API 列表。

以下命令列出了本章编译代码中所有弃用 JDK API 的使用情况。本书可下载代码中的 `Java9LanguageFeatures/build/modules/jdojo.annotation` 目录包含本章的编译代码。

```
C:\Java9LanguageFeatures>jdeprscan build/modules/jdojo.annotation
Directory build/modules/jdojo.annotation:
class com/jdojo/annotation/ImportDeprecationWarning uses deprecated class java/io/StringBufferInputStream
class com/jdojo/annotation/JDeprScanTest uses deprecated method java/lang/Thread::stop()V
class com/jdojo/annotation/JDeprScanTest uses deprecated method java/lang/Thread::destroy()V (forRemoval=true)
C:\Java9LanguageFeatures>jdeprscan --for-removal build/modules/jdojo.annotation
Directory build/modules/jdojo.annotation:
class com/jdojo/annotation/JDeprScanTest uses deprecated method java/lang/Thread::destroy()V (forRemoval=true)
```

#### 弃用 API 的动态分析

`jdeprscan` 工具是一个静态分析工具，因此它会跳过弃用 API 的动态使用。例如，你可以使用反射调用一个弃用的方法，此工具在扫描期间会遗漏这一点。你也可以在由 `ServiceLoader` 加载的提供者中调用弃用的方法，这也会被此工具遗漏。

在未来的版本中，JDK 可能会提供一个名为 `jdeprdetect` 的动态分析工具，该工具将在运行时跟踪弃用 API 的使用情况。该工具将有助于查找引用静态分析工具 `jdeprscan` 报告的弃用 API 的死代码。



#### 导入时无弃用警告

在 JDK9 之前，如果你使用 `import` 语句导入已弃用的构造，即使你在所有使用这些已弃用构造的地方都使用了 `@SuppressWarnings` 注解，编译器也会生成警告。如果你试图消除代码中所有弃用警告，这会非常烦人。你根本无法消除它们，因为你无法注解 `import` 语句。JDK9 对此进行了改进，省略了 `import` 语句上的弃用警告。

### 抑制命名的编译时警告

`SuppressWarnings` 注解类型用于抑制命名的编译时警告。它声明了一个名为 `value` 的元素，其数据类型是 `String` 数组。让我们考虑 `SuppressWarningsTest` 类的代码，它在 `test()` 方法中使用了 `ArrayList<T>` 的原始类型。当你使用原始类型时，编译器会生成一个名为 `unchecked` 的警告。

```
// SuppressWarningsTest.java
package com.jdojo.annotation;
import java.util.ArrayList;
public class SuppressWarningsTest {
public void test() {
ArrayList list = new ArrayList();
list.add("Hello"); // 编译器发出 unchecked 警告
}
}
清单 1-21.
一个编译时会产生警告的类
```

使用以下命令编译 `SuppressWarningsTest` 类，并启用生成 unchecked 警告的选项：

```
javac -Xlint:unchecked SuppressWarningsTest.java
com\jdojo\annotation\SuppressWarningsTest.java:10: warning: [unchecked] unchecked call to add(E) as a member of the raw type ArrayList
list.add("Hello"); // 编译器发出 unchecked 警告
^
where E is a type-variable
E extends Object declared in class ArrayList
1 warning
```

作为开发者，有时你了解此类编译器警告，并希望在编译代码时抑制它们。你可以通过在程序元素上使用 `@SuppressWarnings` 注解，并提供要抑制的警告名称列表来实现。例如，如果你在类声明上使用它，那么该类声明内所有方法中的指定警告都将被抑制。建议你在要抑制警告的最内层程序元素上使用此注解。

清单 1-22 在 `test()` 方法上使用了 `@SuppressWarnings` 注解。它指定了两个命名的警告：`"unchecked"` 和 `"deprecation"`。`test()` 方法不包含会生成 `"deprecated"` 警告的代码。此处包含它只是为了向你展示你可以使用一个 `SuppressWarnings` 注解来抑制多个命名的警告。如果你使用前面显示的相同选项重新编译 `SuppressWarningsTest` 类，它将不会生成任何编译器警告。

```
// SuppressWarningsTest.java
package com.jdojo.annotation;
import java.util.ArrayList;
public class SuppressWarningsTest {
@SuppressWarnings({"unchecked", "deprecation"})
public void test() {
ArrayList list = new ArrayList();
list.add("Hello"); // 编译器不会发出 unchecked 警告
}
}
清单 1-22.
SuppressWarningsTest 类的修改版本
```

### 重写方法

`java.lang.Override` 注解类型是一个标记注解类型。它只能用于方法。它表示使用此注解的方法重写了其超类型中声明的方法。这对于开发者避免导致程序中逻辑错误的拼写错误非常有帮助。如果你打算重写超类型中的方法，建议使用 `@Override` 注解来注解被重写的方法。编译器将确保被注解的方法确实重写了超类型中的方法。如果被注解的方法没有重写超类型中的方法，编译器将生成一个错误。

考虑两个类 `A` 和 `B`。类 `B` 继承自类 `A`。类 `B` 中的 `m1()` 方法重写了其超类 `A` 中的 `m1()` 方法。类 `B` 中 `m1()` 方法上的 `@Override` 注解只是表明了这种意图。编译器会验证此声明，并在本例中发现其为真。

```
public class A {
public void m1() {
}
}
public class B extends A {
@Override
public void m1() {
}
}
```

让我们考虑类 `C`。

```
// 无法编译，因为 m2() 没有重写任何方法
public class C extends A {
@Override
public void m2() {
}
}
```

类 `C` 中的方法 `m2()` 有一个 `@Override` 注解。然而，其超类 `A` 中没有 `m2()` 方法。方法 `m2()` 是类 `C` 中的一个新方法。编译器发现类 `C` 中的方法 `m2()` 没有重写任何超类方法，尽管其开发者如此声明。在这种情况下，编译器会生成一个错误。

### 声明函数式接口

只有一个抽象方法声明的接口称为函数式接口。以前，函数式接口被称为 SAM（单一抽象方法）类型。编译器会验证所有使用 `@FunctionalInterface` 注解的接口是否确实包含且仅包含一个抽象方法。如果使用此注解的接口不是函数式接口，则会生成编译时错误。在类、注解类型和枚举上使用此注解也会导致编译时错误。`FunctionalInterface` 注解类型是一个标记注解。

以下 `Runner` 接口的声明使用了 `@FunctionalInterface` 注解。该接口声明将编译通过。

```
@FunctionalInterface
public interface Runner {
void run();
}
```

以下 `Job` 接口的声明使用了 `@FunctionalInterface` 注解，这将生成一个编译时错误，因为 `Job` 接口声明了两个抽象方法，因此它不是一个函数式接口。

```
@FunctionalInterface
public interface Job {
void run();
void abort();
}
```

以下 `Test` 类的声明使用了 `@FunctionalInterface` 注解，这将生成一个编译时错误，因为 `@FunctionalInterface` 注解只能用于接口。

```
@FunctionalInterface
public class Test {
public void test() {
// 代码写在这里
}
}
```

提示

只有一个抽象方法的接口始终是函数式接口，无论它是否使用 `@FunctionalInterface` 注解。使用该注解会指示编译器验证该接口确实是一个函数式接口。

## 注解包

注解类和字段等程序元素是直观的，因为你在声明它们时进行注解。如何注解一个包？包声明作为顶层类型声明的一部分出现在编译单元中。此外，相同的包声明会在不同的编译单元中出现多次。问题来了：你如何以及在何处注解包声明？

你需要创建一个文件，应命名为 `package-info.java`，并将带注解的包声明放入其中。清单 1-23 显示了 `package-info.java` 文件的内容。当你编译 `package-info.java` 文件时，将创建一个类文件。

```
// package-info.java
@Version(major=1, minor=0)
package com.jdojo.annotation;
清单 1-23.
package-info.java 文件的内容
```

你可能需要一些 `import` 语句来导入注解类型，或者你可以在 `package-info.java` 文件中使用注解类型的完全限定名称。即使 `import` 语句出现在包声明之后，使用导入的类型也应该没问题。你可以在 `package-info.java` 文件中包含如下内容：

```
// package-info.java
@com.jdojo.myannotations.Author("John Jacobs")
@Reviewer("Wally Inman")
package com.jdojo.annotation;
import com.jdojo.myannotations.Reviewer;
```



## 注解模块

你可以在模块声明上使用注解。在 JDK9 中，`java.lang.annotation.ElementType` 枚举新增了一个名为 `MODULE` 的值。如果你在注解声明中将 `MODULE` 作为目标类型，则允许该注解类型用于模块。在 JDK9 中，有两个注解——`java.lang.Deprecated` 和 `java.lang.SuppressWarnings`——已被更新，可用于模块声明。它们的使用方式如下：

```
@Deprecated(since="1.2", forRemoval=true)
@SuppressWarnings("unchecked")
module com.jdojo.myModule {
// 模块语句写在这里
}
```

当一个模块被标记为已弃用时，在 `requires` 语句中使用该模块（而非在 `exports` 或 `opens` 语句中）会触发警告。此规则基于以下事实：如果模块 `M` 被弃用，模块的用户需要使用 `"requires M"` 语句才能收到弃用警告。其他语句如 `exports` 和 `opens` 则位于被弃用的模块内部。被弃用的模块不会因模块内类型的使用而触发警告。类似地，如果在模块声明中抑制了警告，则该抑制仅适用于模块声明中的元素，而不适用于该模块中包含的类型。

提示

你不能注解单个模块语句。例如，你不能用 `@Deprecated` 注解来注解一个 `exports` 语句，以指示导出的包将在未来版本中被移除。在早期设计阶段，曾考虑过此功能，但因该功能需要花费大量时间，而当前并不需要，因此被否决。如果将来有需要，可能会添加此功能。

## 在运行时访问注解

访问程序元素上的注解很容易。程序元素上的注解是 Java 对象。你只需要知道如何在运行时获取注解类型对象的引用。允许你访问其注解的程序元素实现了 `java.lang.reflect.AnnotatedElement` 接口。`AnnotatedElement` 接口中有几个方法可以让你访问程序元素的注解。该接口中的方法允许你检索程序元素上的所有注解、程序元素上的所有已声明注解，以及程序元素上指定类型的注解。我稍后会展示一些使用这些方法的示例。以下类实现了 `AnnotatedElement` 接口：

*   `java.lang.Class`
*   `java.lang.reflect.Executable`
*   `java.lang.reflect.Constructor`
*   `java.lang.reflect.Field`
*   `java.lang.reflect.Method`
*   `java.lang.reflect.Module`
*   `java.lang.reflect.Parameter`
*   `java.lang.Package`
*   `java.lang.reflect.AccessibleObject`

`AnnotatedElement` 接口的方法用于访问这些类型对象上的注解。

警告

非常重要的一点是，注解类型必须使用保留策略为 `RUNTIME` 的 `Retention` 元注解进行注解，才能在运行时访问它。如果一个程序元素有多个注解，你将只能访问那些保留策略为运行时的注解。

假设你有一个 `Test` 类，并且想要打印其所有注解。以下代码片段将打印 `Test` 类声明上的所有注解：

```
// 获取类对象引用
Class cls = Test.class;
// 获取类声明上的所有注解
Annotation[] allAnns = cls.getAnnotations();
System.out.println("注解数量: " + allAnns.length);
// 打印所有注解
for (Annotation ann : allAnns) {
System.out.println(ann.toString());
}
```

`Annotation` 接口的 `toString()` 方法返回注解的字符串表示形式。假设你想要打印 `Test` 类上的 `Version` 注解。你可以这样做：

```
Class cls = Test.class;
// 获取 Test 类的 Version 注解实例
Version v = cls.getAnnotation(Version.class);
if (v == null) {
System.out.println("Version 注解不存在。");
} else {
int major = v.major();
int minor = v.minor();
System.out.println("版本: major=" + major + ", minor=" + minor);
}
```

这段代码片段展示了你可以使用 `major()` 和 `minor()` 方法来读取 `Version` 注解的 `major` 和 `minor` 元素的值。它还展示了你可以声明一个注解类型的变量（例如 `Version v`），该变量可以引用该注解类型的实例。注解类型的实例由 Java 运行时创建。你永远不需要使用 `new` 运算符来创建注解类型的实例。

你将使用 `Version` 和 `Deprecated` 注解类型来注解你的程序元素，并在运行时访问这些注解。你还会注解一个包声明和一个方法声明。你将使用清单 1-24 中列出的 `Version` 注解类型的代码。请注意，它使用了 `@Retention(RetentionPolicy.RUNTIME)` 注解，这是在运行时读取其实例所必需的。

```
// Version.java
package com.jdojo.annotation;
import java.lang.annotation.Documented;
import java.lang.annotation.Target;
import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
@Target({ElementType.TYPE, ElementType.CONSTRUCTOR, ElementType.METHOD, ElementType.MODULE, ElementType.PACKAGE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface Version {
int major();
int minor();
}
清单 1-24.
一个 Version 注解类型
```



清单 1-25 展示了需要保存到 `package-info.java` 文件中并与其他程序一同编译的代码。它用于注解 `com.jdojo.annotation` 包。清单 1-26 包含一个用于演示的类代码，该类带有一些注解。

```
// package-info.java
@Version(major=1, minor=0)
package com.jdojo.annotation;
Listing 1-25.
Contents of package-info.java File
```

```
// AccessAnnotation.java
package com.jdojo.annotation;
@Version(major=1, minor=0)
public class AccessAnnotation {
@Version(major=1, minor=1)
public void testMethod1() {
// Code goes here
}
@Version(major=1, minor=2)
@Deprecated
public void testMethod2() {
// Code goes here
}
}
Listing 1-26.
AccessAnnotation Class Has Some Annotations, Which Will Be Accessed at Runtime
```

清单 1-27 是演示如何在运行时访问注解的程序。其输出显示，你能够成功读取 `AccessAnnotation` 类中使用的所有注解。`printAnnotations()` 方法用于访问注解。它接受一个 `AnnotatedElement` 类型的参数，并打印其参数的所有注解。如果注解是 `Version` 注解类型，则会打印其主版本号和次版本号的值。

```
// AccessAnnotationTest.java
package com.jdojo.annotation;
import java.lang.annotation.Annotation;
import java.lang.reflect.AnnotatedElement;
import java.lang.reflect.Method;
public class AccessAnnotationTest {
public static void main(String[] args) {
// Read annotations on the class declaration
Class cls = AccessAnnotation.class;
System.out.println("Annotations for class: " + cls.getName());
printAnnotations(cls);
// Read annotations on the package declaration
Package p = cls.getPackage();
System.out.println("Annotations for package: " + p.getName());
printAnnotations(p);
// Read annotations on the methods declarations
System.out.println("Method annotations:");
Method[] methodList = cls.getDeclaredMethods();
for (Method m : methodList) {
System.out.println("Annotations for method: " + m.getName());
printAnnotations(m);
}
}
public static void printAnnotations(AnnotatedElement programElement) {
Annotation[] annList = programElement.getAnnotations();
for (Annotation ann : annList) {
System.out.println(ann);
if (ann instanceof Version) {
Version v = (Version) ann;
int major = v.major();
int minor = v.minor();
System.out.println("Found Version annotation: "
+ "major=" + major + ", minor=" + minor);
}
}
System.out.println();
}
}
Annotations for class: com.jdojo.annotation.AccessAnnotation
@com.jdojo.annotation.Version(major=1, minor=0)
Found Version annotation: major=1, minor=0
Annotations for package: com.jdojo.annotation
@com.jdojo.annotation.Version(major=1, minor=0)
Found Version annotation: major=1, minor=0
Method annotations:
Annotations for method: testMethod1
@com.jdojo.annotation.Version(major=1, minor=1)
Found Version annotation: major=1, minor=1
Annotations for method: testMethod2
@com.jdojo.annotation.Version(major=1, minor=2)
Found Version annotation: major=1, minor=2
@java.lang.Deprecated(forRemoval=false, since="")
Listing 1-27.
Using the AccessAnnotationTest Class to Access Annotations
```

访问可重复注解的实例略有不同。回想一下，可重复注解有一个配套的容器注解类型。例如，你声明了一个 `ChangeLogs` 注解类型，它是 `ChangeLog` 可重复注解类型的容器注解类型。你可以使用注解类型或容器注解类型来访问重复注解。使用 `getAnnotationsByType()` 方法，并传入可重复注解类型的类引用，以数组形式获取可重复注解的实例。使用 `getAnnotation()` 方法，并传入容器注解类型的类引用，以容器注解类型实例的形式获取可重复注解的实例。

清单 1-28 包含 `RepeatableAnnTest` 类的代码。该类声明被 `ChangeLog` 注解标注了两次。`main()` 方法使用这两种方法访问类声明上的重复注解。

```
// RepeatableAnnTest.java
package com.jdojo.annotation;
@ChangeLog(date = "09/18/2017", comments = "Declared the class")
@ChangeLog(date = "10/22/2017", comments = "Added the main() method")
public class RepeatableAnnTest {
public static void main(String[] args) {
Class mainClass = RepeatableAnnTest.class;
Class annClass = ChangeLog.class;
// Access annotations using the ChangeLog type
System.out.println("Using the ChangeLog type...");
ChangeLog[] annList = mainClass.getAnnotationsByType(ChangeLog.class);
for (ChangeLog log : annList) {
System.out.println("Date=" + log.date() + ", Comments=" + log.comments());
}
// Access annotations using the ChangeLogs containing annotation type
System.out.println("\nUsing the ChangeLogs type...");
Class containingAnnClass = ChangeLogs.class;
ChangeLogs logs = mainClass.getAnnotation(containingAnnClass);
for (ChangeLog log : logs.value()) {
System.out.println("Date=" + log.date() + ", Comments=" + log.comments());
}
}
}
Using the ChangeLog type...
Date=09/18/2017, Comments=Declared the class
Date=10/22/2017, Comments=Added the main() method
Using the ChangeLogs type...
Date=09/18/2017, Comments=Declared the class
Date=10/22/2017, Comments=Added the main() method
Listing 1-28.
Accessing Instances of Repeatable Annotations at Runtime
```

## 注解类型的演进

注解类型可以在不破坏使用它的现有代码的情况下进行演进。如果你向注解类型添加一个新元素，则需要提供其默认值。所有现有的注解实例都将使用新元素的默认值。如果你向现有注解类型添加新元素但未指定该元素的默认值，则使用该注解的代码将会出错。



## 源代码级别的注解处理

本节面向有经验的程序员。如果你是初次学习 Java，可以跳过本节。

本节详细讨论如何在编译 Java 程序时，开发注解处理器以在源代码级别处理注解。华盛顿大学开发了一个检查器框架（Checker Framework），其中包含大量可在程序中使用的注解，并且还附带了许多注解处理器。你可以从 [`http://types.cs.washington.edu/checker-framework`](http://types.cs.washington.edu/checker-framework) 下载该框架。它包含一个关于如何使用不同类型处理器的教程，以及一个关于如何创建你自己的处理器的教程。

Java 允许你在运行时以及编译时处理注解。你已经了解了如何在运行时处理注解。现在，我将简要讨论如何在编译时（或源代码级别）处理注解。

为什么要在编译时处理注解？在编译时处理注解开辟了多种可能性，可以在应用程序开发过程中帮助 Java 程序员。它也对 Java 工具的开发人员大有裨益。例如，可以根据源代码中的注解生成样板代码和配置文件；可以在编译时验证基于自定义注解的规则等。

编译时的注解处理是一个两步过程。首先，你需要编写一个自定义注解处理器。其次，你需要使用 `javac` 命令行工具。你需要使用 `--processor-module-path` 选项为 `javac` 编译器指定自定义注解处理器的模块路径。以下命令编译 Java 源文件 `MySourceFile.java`：

```
javac --processor-module-path  MySourceFile.java
```

使用 `-proc` 选项，`javac` 命令允许你指定是否要处理注解和/或编译源文件。你可以将 `-proc` 选项用作 `-proc:none` 或 `-proc:only`。`-proc:none` 选项不执行注解处理，仅编译源文件。`-proc:only` 选项仅执行注解处理，并跳过源文件的编译。如果在同一命令中同时指定了 `-proc:none` 和 `-processor` 选项，则 `-processor` 选项将被忽略。以下命令使用自定义处理器 `MyProcessor1` 和 `MyProcessor2` 处理源文件 `MySourceFile.java` 中的注解，但不编译 `MySourceFile.java` 文件中的源代码。

```
javac -proc:only --processor-module-path  MySourceFile.java
```

要实际体验编译时的注解处理，你必须使用 `javax.annotation.processing` 包中的类来编写注解处理器，该包位于 `java.compiler` 模块中。

在编写自定义注解处理器时，你通常需要访问源代码中的元素，例如，类名及其修饰符、方法名及其返回类型等。你需要使用 `javax.lang.model` 包及其子包中的类来处理源代码元素。在示例中，你将为你自己的 `@Version` 注解编写一个注解处理器。它将验证源代码中使用的所有 `@Version` 注解，以确保 `Version` 的 `major` 和 `minor` 值始终为零或大于零。例如，如果在源代码中使用了 `@Version(major=-1, minor=0)`，你的注解处理器将打印一条错误消息，因为版本的 `major` 值为负数。

注解处理器是一个实现了 `Processor` 接口的类的对象。`AbstractProcessor` 类是一个抽象注解处理器，它为 `Processor` 接口的所有方法提供了默认实现，但 `process()` 方法的实现除外。在大多数情况下，默认实现已经足够。要创建你自己的处理器，你需要让你的处理器类继承 `AbstractProcessor` 类，并为 `process()` 方法提供实现。如果 `AbstractProcessor` 类不适合你的需求，你可以创建自己的实现了 `Processor` 接口的处理器类。让我们将你的处理器类命名为 `VersionProcessor`，它继承自 `AbstractProcessor` 类，如下所示：

```
public class VersionProcessor extends AbstractProcessor {
// 代码写在这里
}
```

注解处理器对象由编译器使用无参构造函数实例化。你的处理器类必须有一个无参构造函数，以便编译器可以实例化它。你的 `VersionProcessor` 类的默认构造函数将满足此要求。

下一步是向处理器类添加两条信息。第一条是关于此处理器支持处理哪种注解类型。你可以使用 `@SupportedAnnotationTypes` 注解在类级别指定支持的注解类型。以下代码片段显示 `VersionProcessor` 支持处理 `com.jdojo.annotation.Version` 注解类型：

```
@SupportedAnnotationTypes({"com.jdojo.annotation.Version"})
public class VersionProcessor extends AbstractProcessor {
// 代码写在这里
}
```

你可以单独使用星号（`*`），或将其作为受支持注解类型名称的一部分。星号用作通配符。例如，`"com.jdojo.*"` 表示名称以 `"com.jdojo."` 开头的任何注解类型。仅使用星号（`"*"`）表示所有注解类型。请注意，当星号用作名称的一部分时，名称必须采用 `PartialName.*` 的形式。例如，`"com*"` 和 `"com.*jdojo"` 在受支持的注解类型中都是星号的无效用法。你可以使用 `SupportedAnnotationTypes` 注解传递多个受支持的注解类型。以下代码片段显示该处理器支持处理 `com.jdojo.Ann1` 注解以及名称以 `com.jdojo.annotation` 开头的任何注解：

```
@SupportedAnnotationTypes({"com.jdojo.Ann1", "com.jdojo.annotation.*"})
```

你需要使用 `@SupportedSourceVersion` 注解指定你的处理器所支持的最新源代码版本。以下代码片段将源代码版本 9 指定为 `VersionProcessor` 类支持的源代码版本：

```
@SupportedAnnotationTypes({"com.jdojo.annotation.Version"})
@SupportedSourceVersion(SourceVersion.RELEASE_9)
public class VersionProcessor extends AbstractProcessor {
// 代码写在这里
}
```

下一步是在处理器类中提供 `process()` 方法的实现。注解处理是分轮进行的。`RoundEnvironment` 接口的一个实例代表一轮。`javac` 编译器通过传递处理器声明支持的所有注解以及一个 `RoundEnvironment` 对象来调用你的处理器的 `process()` 方法。`process()` 方法的返回类型是 `boolean`。如果返回 `true`，则传递给它的注解被视为已被该处理器声明。已声明的注解不会传递给其他处理器。如果返回 `false`，则传递给它的注解被视为未被声明，其他处理器将被要求处理它们。以下代码片段展示了 `process()` 方法的框架：

```
public boolean process(Set annotations, RoundEnvironment roundEnv) {
// 处理器代码写在这里
}
```



您在 `process()` 方法中编写的代码取决于您的需求。在您的场景中，您需要检查源代码中每个 `@Version` 注解的 `major` 和 `minor` 值。如果其中任何一个小于零，您需要打印一条错误消息。为了处理每个 `Version` 注解，您将遍历传递给 `process()` 方法的所有 `Version` 注解实例，如下所示：

```
for (TypeElement currentAnnotation : annotations) {
// 用于验证每个 Version 注解的代码写在这里
}
```

您可以使用 `TypeElement` 接口的 `getQualifiedName()` 方法获取注解的完全限定名。

```
Name qualifiedName = currentAnnotation.getQualifiedName();
// 检查它是否为 Version 注解
if (qualifiedName.contentEquals("com.jdojo.annotation.Version")) {
// 获取 Version 注解的值以进行验证
}
```

一旦确定拥有一个 `Version` 注解，您需要从源代码中获取它的所有实例。要获取源代码中的信息，您需要使用 `RoundEnvironment` 对象。以下代码片段将获取源代码中所有被 `Version` 注解标注的元素（例如，类、方法、构造函数等）：

```
Set annotatedElements = roundEnv.getElementsAnnotatedWith(currentAnnotation);
```

此时，您需要遍历所有被 `Version` 注解标注的元素；获取它们上面存在的 `Version` 注解实例；并验证 `major` 和 `minor` 元素的值。您可以按如下方式执行此逻辑：

```
for (Element element : annotatedElements) {
Version v = element.getAnnotation(Version.class);
int major = v.major();
int minor = v.minor();
if (major < 0 || minor < 0) {
// 在此处打印错误消息
}
}
```

您可以使用 `Messager` 的 `printMessage()` 方法打印错误消息。`processingEnv` 是在 `AbstractProcessor` 类中定义的一个实例变量，您可以在处理器内部使用它来获取 `Messager` 对象引用，如下所示。如果您将源代码元素的引用传递给 `printMessage()` 方法，您的消息将被格式化，以包含该元素的源代码文件名和行号。`printMessage()` 方法的第一个参数指示消息的类型。您可以使用 `Kind.NOTE` 和 `Kind.WARNING` 作为第一个参数来分别打印提示和警告。

```
String errorMsg = "Version cannot be negative. major=" + major + " minor=" + minor;
Messager messager = this.processingEnv.getMessager();
messager.printMessage(Kind.ERROR, errorMsg, element);
```

最后，您需要从 `process()` 方法返回 `true` 或 `false`。如果处理器返回 `true`，则表示它声明了所有传递给它的注解。否则，这些注解被视为未声明，并将传递给其他处理器。通常，您的注解处理器应打包在一个单独的模块中。清单 1-29 包含了 `jdojo.annotation.processor` 模块的声明，该模块包含名为 `VersionProcessor` 的注解处理器，用于处理 `Version` 注解类型，如清单 1-30 所示。

```
// module-info.java
module jdojo.annotation.processor {
exports com.jdojo.annotation.processor;
requires jdojo.annotation;
requires java.compiler;
provides javax.annotation.processing.Processor
with com.jdojo.annotation.processor.VersionProcessor;
}
清单 1-29.
jdojo.annotation.processor 模块的声明
```

该模块读取 `jdojo.annotation` 模块，因为它在 `VersionProcessor` 类中使用了 `Version` 注解类型。它读取 `java.compiler` 模块以使用与注解处理器相关的类型。请注意模块声明中 `provides` 语句的使用。JDK9 将加载处理器模块路径上所有在 `provides` 语句的 `with` 子句中提到的注解处理器。该语句指定 `VersionProcessor` 类为 `Processor` 服务接口提供了一个实现。有关 `provides` 语句和实现服务的更多详细信息，请参阅第 14 章。

```
// VersionProcessor.java
package com.jdojo.annotation.processor;
import java.util.Set;
import javax.annotation.processing.AbstractProcessor;
import javax.annotation.processing.Messager;
import javax.annotation.processing.RoundEnvironment;
import javax.annotation.processing.SupportedAnnotationTypes;
import javax.annotation.processing.SupportedSourceVersion;
import javax.lang.model.SourceVersion;
import javax.lang.model.element.Element;
import javax.lang.model.element.Name;
import javax.lang.model.element.TypeElement;
import javax.tools.Diagnostic.Kind;
@SupportedAnnotationTypes({"com.jdojo.annotation.Version"})
@SupportedSourceVersion(SourceVersion.RELEASE_9)
public class VersionProcessor extends AbstractProcessor {
// 注解处理器需要一个无参构造函数
public VersionProcessor() {
}
@Override
public boolean process(Set annotations, RoundEnvironment roundEnv) {
// 处理所有注解
for (TypeElement currentAnnotation: annotations) {
Name qualifiedName = currentAnnotation.getQualifiedName();
// 检查它是否为 Version 注解
if (qualifiedName.contentEquals("com.jdojo.annotation.Version" )) {
// 查看所有带有 Version 注解的元素
Set annotatedElements;
annotatedElements = roundEnv.getElementsAnnotatedWith(currentAnnotation);
for (Element element: annotatedElements) {
Version v = element.getAnnotation(Version.class);
int major = v.major();
int minor = v.minor();
if (major < 0 || minor < 0) {
// 打印错误消息
String errorMsg = "Version cannot be negative." +
" major=" + major +
" minor=" + minor;
Messager messager = this.processingEnv.getMessager();
messager.printMessage(Kind.ERROR, errorMsg, element);
}
}
}
}
return true;
}
}
清单 1-30.
用于处理 Version 注解的注解处理器
```

现在您有了一个注解处理器。是时候看看它的实际效果了。您需要一个在 `Version` 注解的 `major` 和 `minor` 元素中使用无效值的源代码。您将把源代码放在一个名为 `jdojo.annotation.test` 的模块中，如清单 1-31 所示。清单 1-32 中的 `VersionProcessorTest` 类使用了 `Version` 注解三次。它对类本身和方法 `m2()` 使用了 `major` 和 `minor` 元素的负值。当您编译 `VersionProcessorTest` 类的源代码时，处理器应该捕获这两个错误。

```
// module-info.java
module jdojo.annotation.test {
exports com.jdojo.annotation.test;
requires jdojo.annotation;
}
清单 1-31.
jdojo.annotation.test 模块的声明
```

```
// VersionProcessorTest.java
package com.jdojo.annotation.test;
@Version(major = -1, minor = 2)
public class VersionProcessorTest {
@Version(major = 1, minor = 1)
public void m1() {
}
@Version(major = -2, minor = 1)
public void m2() {
}
}
清单 1-32.
用于测试 VersionProcessor 的测试类
```



要查看处理器的实际运行效果，你需要执行以下命令。你需要使用 `--processor-module-path` 选项指定 `VersionProcessor` 类模块的路径。注解处理器所依赖的模块也应在此处理器模块路径中指定。运行该命令时，编译器会自动发现 `VersionProcessor` 作为注解处理器，并将所有 `@Version` 实例传递给该处理器。输出会显示两个错误，其中包含源文件名以及源文件中发现错误的行号。

```
C:\Java9LanguageFeatures>javac --module-path dist\jdojo.annotation.jar
--processor-module-path dist\jdojo.annotation.processor.jar;dist\jdojo.annotation.jar
-d build\modules\jdojo.annotation.test
src\jdojo.annotation.test\classes\module-info.java
src\jdojo.annotation.test\classes\com\jdojo\annotation\test\VersionProcessorTest.java
src\jdojo.annotation.test\classes\com\jdojo\annotation\test\VersionProcessorTest.java:7: 错误: 版本号不能为负数。 major=-1 minor=2
public class VersionProcessorTest {
^
src\jdojo.annotation.test\classes\com\jdojo\annotation\test\VersionProcessorTest.java:13: 错误: 版本号不能为负数。 major=-2 minor=1
public void m2() {
^
2 个错误
```

## 总结

注解是 Java 中的类型。它们用于将信息关联到 Java 程序中程序元素的声明或类型使用上。使用注解不会改变程序的语义。

注解可以仅存在于源代码中、类文件中，或运行时。它们的可用性由声明注解类型时指定的保留策略控制。

注解有两种类型：常规注解（或简称为注解）和元注解。注解用于注解程序元素，而元注解用于注解其他注解。当你声明一个注解时，可以指定其目标，即它可以注解的程序元素类型。在 Java 8 之前，不允许在同一元素上重复使用注解。Java 8 允许你创建可重复的注解。

Java 库包含许多你可以在 Java 程序中使用的注解类型——`Deprecated`、`Override`、`SuppressWarnings`、`FunctionalInterface` 等是其中一些常用的注解类型。它们具有编译器支持，这意味着如果使用这些注解注解的程序元素不遵守特定规则，编译器会生成错误。

Java 允许你编写注解处理器，这些处理器可以插入到 Java 编译器中，以便在编译 Java 程序时处理注解。你可以编写处理器来基于注解强制执行自定义规则。

Java 中的弃用是一种提供 API 生命周期信息的方式。弃用一个 API 会告知其用户迁移，因为该 API 使用起来有风险、存在更好的替代方案，或者将在未来版本中移除。使用弃用的 API 会生成编译时弃用警告。`@deprecated` Javadoc 标签和 `@Deprecated` 注解一起用于弃用 API 元素，例如模块、包、类型、构造器、方法、字段、参数和局部变量。在 JDK9 之前，该注解不包含任何元素。它在运行时保留。

JDK9 为 `Deprecated` 注解类型添加了两个元素：`since` 和 `forRemoval`。`since` 元素默认为空字符串。其值表示 API 元素被弃用的 API 版本。`forRemoval` 元素的类型是 `boolean`，默认为 `false`。其值为 `true` 表示该 API 元素将在未来版本中被移除。

JDK9 编译器根据 `@Deprecated` 注解的 `forRemoval` 元素的值生成两种类型的弃用警告：当 `forRemoval=false` 时生成普通弃用警告，当 `forRemoval=true` 时生成移除警告。

在 JDK9 之前，你可以通过在被弃用 API 的使用位置添加 `@SuppressWarnings("deprecation")` 注解来抑制弃用警告。在 JDK9 中，你需要使用 `@SuppressWarnings("deprecation")` 来抑制普通警告，使用 `@SuppressWarnings("removal")` 来抑制移除警告，使用 `@SuppressWarnings({"deprecation", "removal"})` 来抑制两种类型的警告。在 JDK9 之前，使用 `import` 语句导入一个被弃用的构造会生成编译时弃用警告。JDK9 省略了此类警告。

问题与练习



1.  什么是注解？如何声明它们？
2.  什么是元注解？
3.  注解类型和注解实例之间有什么区别？
4.  能否从一个注解类型继承另一个注解类型？
5.  什么是标记注解？描述其用途。列举 Java SE API 中的两个标记注解。
6.  命名用于注解重写方法的注解类型。该注解类型的全限定名是什么？
7.  在注解类型声明中，方法允许的返回类型有哪些？
8.  声明一个名为 `Table` 的注解类型。它包含一个名为 `name` 的 `String` 类型元素。该唯一元素没有默认值。此注解只能用于类。其实例应在运行时可用。
9.  以下注解类型声明有什么问题？

```
    public @interface Version extends BasicVersion {
    int extended();
    }
    ```

10.  以下注解类型声明有什么问题？

```
    public @interface Author {
    void name(String firstName, String lastName);
    }
    ```

11.  简要描述以下内置元注解的用途：`Target`、`Retention`、`Inherited`、`Documented`、`Repeatable` 和 `Native`。
12.  声明一个名为 `ModuleOwner` 的注解类型，它包含一个 `String` 类型的元素 `name`。`ModuleOwner` 类型的实例应仅保留在源代码中，并且它们应仅用于模块声明。
13.  声明一个可重复的注解类型，名为 `Author`。它包含两个 `String` 类型的元素：`firstName` 和 `lastName`。此注解可用于类型、方法和构造器。其实例应在运行时可用。将 `Author` 注解类型的容器注解类型命名为 `Authors`。
14.  使用哪种注解类型来弃用你的 API？描述该注解类型的所有元素。
15.  使用哪种注解类型来注解一个函数式接口？
16.  如何注解一个包？
17.  创建一个名为 `Owner` 的注解类型。它应有一个 `String` 类型的元素 `name`。其实例应在运行时保留。它应是可重复的。它应仅用于类型、方法、构造器和模块。创建一个名为 `jdojo.annotation.test` 的模块，并在 `com.jdojo.annotation.exercises` 包中创建一个名为 `Test` 的类。为该类添加一个构造器和一个方法。使用 `Owner` 注解类型注解该类、其模块、构造器和方法。为 `Test` 类添加一个 `main()` 方法，并编写代码来访问和打印这些 `Owner` 注解实例的详细信息。
18.  考虑以下名为 `Status` 的注解类型声明：

```
    public @interface Status {
    boolean approved() default false;
    String approvedBy();
    }
    ```

稍后你需要向 `Status` 注解类型添加另一个元素。修改注解的声明，以包含一个名为 `approvedOn` 的新元素，该元素为 `String` 类型。新元素将包含 ISO 格式的日期，其默认值可设置为 `"1900-01-01"`。
19.  考虑以下名为 `LuckyNumber` 的注解类型声明：

```
    public @interface LuckyNumber {
    int[] value() default {19};
    }
    ```

以下 `LuckyNumber` 注解类型的使用中，哪些是无效的？解释你的答案。
    1.  `@LuckyNumber`  
    2.  `@LuckyNumber({})`  
    3.  `@LuckyNumber(10)`  
    4.  `@LuckyNumber({8, 10, 19, 28, 29, 26})`  
    5.  `@LuckyNumber(value={8, 10, 19, 28, 29, 26})`  
    6.  `@LuckyNumber(null)`   
20.  给定一个 `LuckyNumber` 注解类型，以下变量声明是否有效？

```
    LuckNumber myLuckNumber = null;
    ```

21.  考虑以下 `jdojo.annotation.exercises` 模块的声明：

```
    module jdojo.annotation.exercises {
    exports com.jdojo.annotation.exercises;
    }
    ```

该模块自 1.0 版本起存在。该模块已被弃用，并将在下一版本中移除。注解该模块声明以反映这些信息。

