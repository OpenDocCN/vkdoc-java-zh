# 6. 在脚本语言中使用 Java

在本章中，您将学习：

*   如何将 Java 类导入脚本
*   如何创建 Java 对象并在脚本中使用它们
*   如何调用 Java 对象的重载方法
*   如何创建 Java 数组
*   如何在脚本中扩展 Java 类并实现 Java 接口
*   如何从脚本调用对象的超类方法

脚本语言允许在脚本中使用 Java 类库。每种脚本语言都有自己使用 Java 类的语法。讨论所有脚本语言的语法是不可能的，也超出了本书的范围。在本章中，我将讨论在 Nashorn 中使用 Java 构造的语法。

## 导入 Java 类型

有四种方法可以将 Java 类型导入 Nashorn 脚本：

*   使用 `Packages` 全局对象
*   使用 `Java` 全局对象的 `type()` 方法
*   使用 `importPackage()` 和 `importClass()` 函数
*   在 `with` 子句中使用 `JavaImporter`

在这四种导入 Java 类型的方法中，第二种方法，即使用全局 Java 对象的 `type()` 方法，是首选方法。以下各节将详细描述在脚本中导入 Java 类型的四种方法。



### 使用 Packages 全局对象

Nashorn 将所有 Java 包定义为名为 `Packages` 的全局变量的属性。例如，`java.lang` 和 `javax.swing` 包可以分别引用为 `Packages.java.lang` 和 `Packages.javax.swing`。以下代码片段在 Nashorn 中使用了 `java.util.List` 和 `javax.swing.JFrame`：

`// 创建一个 List`

`var list1 = new Packages.java.util.ArrayList();`

`// 创建一个 JFrame`

`var frame1 = new Packages.javax.swing.JFrame("Test");`

Nashorn 将 `java`、`javax`、`org`、`com`、`edu` 和 `net` 声明为全局变量，它们分别是 `Packages.java`、`Packages.javax`、`Packages.org`、`Packages.com`、`Packages.edu` 和 `Packages.net` 的别名。本书示例中的类名以 `com` 前缀开头，例如 `com.jdojo.script.Test`。要在 JavaScript 代码中使用此类名，你可以使用 `Packages.com.jdojo.script.Test` 或 `com.jdojo.script.Test`。但是，如果类名不以这些预定义前缀之一开头，则必须使用 `Packages` 全局变量来访问它；例如，如果你的类名是 `p1.Test`，则需要在 JavaScript 代码中使用 `Packages.p1.Test` 来访问它。以下代码片段使用了 `java` 和 `javax` 作为 `Packages.java` 和 `Packages.javax` 的别名：

`// 创建一个 List`

`var list = new java.util.ArrayList();`

`// 创建一个 JFrame`

`var frame = new javax.swing.JFrame("Test");`

### 使用 Java 全局对象

在 Java 7 的 Rhino JavaScript 中也支持将包作为 `Packages` 对象的属性进行访问。使用 `Packages` 对象速度较慢且容易出错。Nashorn 定义了一个名为 `Java` 的新全局对象，其中包含许多用于处理 Java 包和类的实用函数。如果你使用的是 Java 8 或更高版本，应优先使用 `Java` 对象。`Java` 对象的 `type()` 函数会将一个 Java 类型导入到脚本中。你需要传入要导入的 Java 类型的完全限定名。在 Nashorn 中，以下代码片段导入了 `java.util.ArrayList` 类并创建了它的对象：

`// 导入 java.util.ArrayList 类型并将其称为 ArrayList`

`var ArrayList = Java.type("java.util.ArrayList");`

`// 创建一个 ArrayList 类型的对象`

`var list = new ArrayList();`

你也可以将这两个语句合并为一个。确保将对 `Java.type()` 方法的调用放在一对括号内；否则，该语句会生成错误，因为 JavaScript 会将 `Java.type` 视为一个构造函数，而你正试图使用构造函数创建一个 Nashorn 对象：

`// 创建一个 java.util.ArrayList 类型的对象`

`var list = new` `(` `Java.type("java.util.ArrayList")` `)` `;`

在代码中，你将从 `Java.type()` 函数返回的导入类型称为 `ArrayList`，这也是所导入类的名称。这样做是为了让下一条语句读起来就像是用 Java 编写的一样。阅读第二条语句的人会知道你在创建一个 `ArrayList` 类的对象。但是，你可以为导入的类型指定任何你想要的名称。以下代码片段导入了 `java.util.ArrayList` 并将其称为 `MyList`：

`// 导入 java.util.ArrayList 类型并将其称为 MyList`

`var MyList = Java.type("java.util.ArrayList");`

`// 创建一个 MyList 类型的对象`

`var list2 = new MyList();`

### 使用 importPackage() 和 importClass() 函数

Rhino JavaScript 允许在脚本中使用 Java 类型的简单名称。Rhino JavaScript 有两个内置函数，分别称为 `importPackage()` 和 `importClass()`，用于从包中导入所有类以及从包中导入一个类。出于兼容性原因，Nashorn 保留了这些函数。要在 Nashorn 中使用这些函数，你需要使用 `load()` 函数从 `mozilla_compat.js` 文件中加载兼容性模块。以下代码片段使用这些函数重写了上一节中的逻辑：

`// 加载兼容性模块。在 Nashorn 中需要，在 Rhino 中不需要。`

`load("nashorn:mozilla_compat.js");`

`// 从 java.util 包中导入 ArrayList 类`

`importClass(java.util.ArrayList);`

`// 从 javax.swing 包中导入所有类`

`importPackage(javax.swing);`

`// 使用类的简单名称`

`var list = new ArrayList();`

`var frame = new JFrame("Test");`

JavaScript 不会自动从 `java.lang` 包中导入所有类，因为具有相同名称的 JavaScript 类（例如 `String`、`Object`、`Number` 等）会与 `java.lang` 包中的类名冲突。要使用 `java.lang` 包中的类，你可以导入它，或者使用 `Packages` 或 `Java` 全局对象来使用其完全限定名。你不能从 `java.lang` 包中导入所有类。以下代码片段会生成错误，因为 `String` 类名已在 JavaScript 中定义：

`// 加载兼容性模块。在 Nashorn 中需要，在 Rhino 中不需要。`

`load("nashorn:mozilla_compat.js");`

`// 会导致与 Nashorn 中的 String 对象冲突`

importClass(java.lang.String);

如果你想使用 `java.lang.String` 类，你需要使用其完全限定名。以下代码片段使用了内置的 JavaScript `String` 类和 `java.lang.String` 类：

`var javaStr = new java.lang.String("Hello"); // Java String 类`

`var jsStr = new String("Hello");             // JavaScript String 类`

如果 `java.lang` 包中的类名不与 JavaScript 顶级类名冲突，你可以使用 `importClass()` 函数来导入该 Java 类。例如，你可以使用以下代码片段来使用 `java.lang.System` 类：

`// 加载兼容性模块。在 Nashorn 中需要，在 Rhino 中不需要。`

`load("nashorn:mozilla_compat.js");`

`importClass(java.lang.System);`

`var jsStr = new String("Hello");`

`System.out.println(jsStr);`

在这段代码片段中，`jsStr` 是一个 JavaScript `String`，它被传递给了接受 `java.lang.String` 类型的 `System.out.println()` Java 方法。在这种情况下，JavaScript 会自动处理从 JavaScript 类型到 Java 类型的转换。

### 使用 JavaImporter 对象

在 JavaScript 中，你可以在 `with` 语句中使用 `JavaImporter` 对象来使用类的简单名称。有关 `with` 语句的更多详细信息，请参阅第 4 章。`JavaImporter` 是一个 Nashorn 函数对象，可以当作函数或构造函数使用。它接受一个 Java 包和类的列表。你可以像下面这样创建一个 `JavaImporter` 对象：

`// 从 java.lang 包中导入所有类`

`var langPkg = new JavaImporter(Packages.java.lang);`

`// 从 java.lang 和 java.util 包中导入所有类，以及`

`// 从 javax.swing 包中导入 JFrame 类`

`var pkg2 = JavaImporter(java.lang, java.util, javax.swing.JFrame);`

注意第一条语句中使用了 `new` 运算符。第二条语句没有使用 `new` 运算符；它将 `JavaImporter` 用作函数。两条语句的作用相同。

以下代码片段创建了一个 `JavaImporter` 对象，并在 `with` 语句中使用它：

`// 为 java.lang 和 java.util 包创建一个 Java 导入器`

`var javaLangAndUtilPkg = JavaImporter(java.lang, java.util);`

`// 在 with 子句中使用导入的类型`

`with (javaLangAndUtilPkg) {`

`var list = new ArrayList();`

`list.add("one");`

`list.add("two");`

`System.out.println("Hello");`

`System.out.println("List is " + list);`

`}`

`Hello`

`List is [one, two]`



## 创建和使用 Java 对象

在脚本中使用 `new` 运算符和构造器来创建新的 Java 对象。以下代码片段在 Nashorn 中创建了一个 `String` 对象：

`// 创建一个 Java String 对象`

`var JavaString = Java.type("java.lang.String");`

`var greeting = new JavaString("Hello");`

访问 Java 对象的方法和属性在大多数脚本语言中都是类似的。有些脚本语言允许你使用属性名来调用对象的 getter 和 setter 方法。以下 Nashorn 代码创建了一个 `java.util.Date` 对象，并使用属性名和方法名两种方式访问该对象的方法。由于代码基于当前日期运行，你可能会得到不同的输出：

`var LocalDate = Java.type("java.time.LocalDate");`

`var dt = LocalDate.now();`

`var year = dt.year;             // 作为属性使用`

`var month = dt.month;           // 作为属性使用`

`var date = dt.getDayOfMonth();  // 作为方法使用`

`print("Date:" + dt);`

`print("Year:" + year + ", Month:" + month + ", Day:" + date);`

`Date:2014-10-12`

`Year:2014, Month:OCTOBER, Day:12`

在 JavaScript 中，你可以将 Java 对象的方法当作属性来使用。当你读取名为 `xxx` 的属性时，JavaScript 会自动调用 `getXxx()` 方法。当你设置名为 `xxx` 的属性时，则会调用 `setXxx()` 方法。系统会遵循 JavaBeans 方法约定来查找对应的方法。例如，如果你读取一个 `LocalDate` 对象的 `leapYear` 属性，由于该属性是 `boolean` 类型，因此会调用该对象的 `isLeapYear()` 方法。

使用 JavaScript 时，理解不同类型的 `String` 对象非常重要。一个 `String` 对象可能是 JavaScript 的 `String` 对象，也可能是 Java 的 `java.lang.String` 对象。JavaScript 为其 `String` 对象定义了一个 `length` 属性，而 Java 为其 `java.lang.String` 类定义了一个 `length()` 方法。以下代码片段展示了创建和访问 JavaScript `String` 与 Java `java.lang.String` 对象长度的区别：

`// JavaScript String`

`var jsStr = new String("Hello JavaScript String");`

`print("JavaScript String: " + jsStr);`

`print("JavaScript String Length: " + jsStr.length);`

`// Java String`

`var javaStr = new java.lang.String("Hello Java String");`

`print("Java String: " + javaStr);`

`print("Java String Length: " + javaStr.length());`

`JavaScript String: Hello JavaScript String`

`JavaScript String Length: 23`

`Java String: Hello Java String`

`Java String Length: 17`

## 使用重载的 Java 方法

Java 在编译时解析重载方法的调用。也就是说，Java 编译器会确定代码运行时将要调用的方法签名。考虑清单 6-1 中所示的 `PrintTest` 类代码。第二行输出可能有所不同。

清单 6-1. 在 Java 中使用重载方法

`// PrintTest.java`

`package com.jdojo.script;`

`public class PrintTest {`

`public void print(String str) {`

`System.out.println("print(String): " + str);`

`}`

`public void print(Object obj) {`

`System.out.println("print(Object): " + obj);`

`}`

`public void print(Double num) {`

`System.out.println("print(Double): " + num);`

`}`

`public static void main(String[] args) {`

`PrintTest pt = new PrintTest();`

`Object[] list = new Object[]{"Hello", new Object(), 10.5};`

`for(Object arg : list) {`

`pt.print(arg);`

`}`

`}`

`}`

`print(Object): Hello`

`print(Object): java.lang.Object@affc70`

`print(Object): 10.5`

当 `PrintTest` 类运行时，对 `print()` 方法的三次调用都调用了 `PrintTest` 类的同一个版本 `print(Object)`。在代码编译时，Java 编译器将调用 `pt.print(arg)` 视为对参数类型为 `Object`（即 `arg` 的类型）的 `print()` 方法的调用，因此将此调用绑定到 `print(Object)` 方法。

在脚本语言中，变量的类型是在运行时而非编译时确定的。脚本语言的解释器会根据方法调用中参数的运行时类型，适当地解析重载方法的调用。以下 JavaScript 代码的输出显示，对 `PrintTest` 类的 `print()` 方法的调用是在运行时根据参数类型解析的。第二行输出可能略有不同：

`// JavaScript Code`

`// 创建一个名为 PrintTest 的 Java 类的对象`

`var PrintTest = Java.type("com.jdojo.script.PrintTest");`

`var pt = new PrintTest();`

`// 创建一个包含三个元素的 JavaScript 数组`

`var list = ["Hello", new Object(), 10.5];`

`// 调用 PrintTest 类的重载方法 print()`

`// 每次传递数组中的一个对象`

`for each(var element in list) {`

`pt.print(element);`

`}`

`print(String): Hello`

`print(Object): jdk.nashorn.internal.scripts.JO@405818`

`print(Double): 10.5`

JavaScript 允许你显式选择重载方法的特定版本。你可以将要调用的重载方法的签名与对象引用一起传递。以下代码片段选择了 `print(Object)` 版本：

`// JavaScript Code`

`var PrintTest = Java.type("com.jdojo.script.PrintTest");`

`var pt = new PrintTest();`

`pt"print(java.lang.Object)"; // 调用 print(Object)`

`pt"print(java.lang.Double)"; // 调用 print(Double)`

`print(Object): 10.5`

`print(Double): 10.` `5`

## 使用 Java 数组

在 Rhino 和 Nashorn 中，创建 Java 数组的方式有所不同。在 Rhino 中，你需要使用 `java.lang.reflect.Array` 类的 `newInstance()` 静态方法来创建 Java 数组。Nashorn 也支持这种语法。以下代码片段展示了如何使用 Rhino 语法创建和访问 Java 数组：

`// 创建一个包含 2 个元素的 java.lang.String 数组，填充并打印`

`// 这些元素。在 Rhino 中，你可以将 java.lang.String 作为`

`// 第一个参数，但在 Nashorn 中，你需要使用`

`// java.lang.String.class 代替。`

`var strArray = java.lang.reflect.Array.newInstance(java.lang.String.class, 2);`

`strArray[0] = "Hello";`

`strArray[1] = "Array";`

`for(var i = 0; i < strArray.length; i++) {`

`print(strArray[i]);`

`}`

`Hello`

`Array`

要创建诸如 `int`、`double` 等基本类型数组，你需要使用其对应包装类的 `TYPE` 常量，如下所示：

`// 创建一个包含 2 个元素的 int 数组，填充并打印这些元素`

`var intArray = java.lang.reflect.Array.newInstance(java.lang.Integer.TYPE, 2);`

`intArray[0] = 100;`

`intArray[1] = 200;`

`for(var i = 0; i < intArray.length; i++) {`

`print(intArray[i]);`

`}`

`100`

`200`

Nashorn 支持一种创建 Java 数组的新语法。首先，使用 `Java.type()` 方法创建适当的 Java 数组类型，然后使用熟悉的 `new` 运算符来创建数组。以下代码片段展示了如何在 Nashorn 中创建一个包含两个元素的 `String[]`：

`// 获取 java.lang.String[] 类型`

`var StringArray = Java.type("java.lang.String[]");`

`// 创建一个包含 2 个元素的 String[] 数组`

`var strArray = new StringArray(2);`

`strArray[0] = "Hello";`

`strArray[1] = "Array";`

`for(var i = 0; i < strArray.length; i++) {`

`print(strArray[i]);`

`}`

`Hello`

`Array`

Nashorn 以相同的方式支持创建基本类型数组。以下代码片段在 Nashorn 中创建了一个包含两个元素的 `int[]`：

`// 获取 int[] 类型`

`var IntArray = Java.type("int[]");`

`// 创建一个包含 2 个元素的 int[] 数组`

`var intArray = new IntArray(2);`

`intArray[0] = 100;`

`intArray[1] = 200;`

`for(var i = 0; i < intArray.length; i++) {`

`print(intArray[i]);`

`}`

`100`

`200`

我将在第 7 章中详细讨论如何使用 Java 和 JavaScript 数组。



## 扩展实现接口的 Java 类

JavaScript 允许你扩展 Java 类并实现 Java 接口。以下各节将介绍实现此目的的不同方法。

### 使用脚本对象

你需要创建一个脚本对象，其中包含接口方法的实现，并使用 `new` 运算符将其传递给 Java 接口的构造函数。在 Java 中，接口没有构造函数，因此不能与 `new` 运算符一起使用，除非是在创建匿名类时。然而，JavaScript 允许你这样做。

在第 5 章中，我们创建了一个包含四个抽象方法的 `Calculator` 接口。为方便参考，该接口的代码再次列于清单 6-2 中。

清单 6-2\. Java 中的 Calculator 接口

`// Calculator.java`

`package com.jdojo.script;`

`public interface Calculator {`

`double add (double n1, double n2);`

`double subtract (double n1, double n2);`

`double multiply (double n1, double n2);`

`double divide (double n1, double n2);`

`}`

在第 5 章中，我们创建了一个 `calculator` JavaScript 对象，其脚本再次列于清单 6-3 中。

清单 6-3\. Java 中的 Calculator 接口

`// calculator.js`

`// 创建一个对象`

`var calculator = new Object();`

`// 向 calculator 对象的原型添加四个方法`

`calculator.add = function (n1, n2) n1 + n2;`

`calculator.subtract = function (n1, n2) n1 - n2;`

`calculator.multiply = function (n1, n2) n1 * n2;`

`calculator.divide = function (n1, n2) n1 / n2;`

请注意，JavaScript 中的 `calculator` 对象包含了 Java `Calculator` 接口所有抽象方法的实现。以下语句创建了 `Calculator` 接口的一个实现：

`// 加载 calculator 对象`

`load("calculator.js");`

`// 获取 Java 接口类型`

`var Calculator = Java.type("com.jdojo.script.Calculator");`

`// 创建 com.jdojo.script.Calculator 接口的实例`

`// 将其构造函数传入一个 calculator JavaScript 对象`

`var calc = new Calculator(calculator);`

现在，你可以像使用 `Calculator` 接口的实现一样开始使用 `calc` 对象，如下所示：

`// 使用 Calculator 接口的实例`

`var x = 15.0, y = 10.0;`

`var addResult = calc.add(x, y);`

`var subResult = calc.subtract(x, y);`

`var mulResult = calc.multiply(x, y);`

`var divResult = calc.divide(x, y);`

`printf("calc.add(%.2f, %.2f) = %.2f", x, y, addResult);`

`printf("calc.subtract(%.2f, %.2f) = %.2f", x, y, subResult);`

`printf("calc.multiply(%.2f, %.2f) = %.2f", x, y, mulResult);`

`printf("calc.divide(%.2f, %.2f) = %.2f", x, y, divResult);`

`calc.add(15.00, 10.00) = 25.00`

`calc.subtract(15.00, 10.00) = 5.00`

`calc.multiply(15.00, 10.00) = 150.00`

`calc.divide(15.00, 10.00) = 1.50`

### 使用类似匿名类的语法

此方法使用的语法与在 Java 中创建匿名类的语法非常相似。以下语句实现了 Java `Calculator` 接口并创建了该实现的一个实例：

`// 获取 Java 接口类型`

`var Calculator = Java.type("com.jdojo.script.Calculator");`

`// 创建 com.jdojo.script.Calculator 接口的实例`

`// 使用类似匿名类的语法`

`var calc = new Calculator() {`

`add: (function (n1, n2) n1 + n2),`

`subtract: (function (n1, n2) n1 - n2),`

`multiply: (function (n1, n2) n1 * n2),`

`divide: (function (n1, n2) n1 / n2)`

`};`

现在，你可以像之前一样使用 `calc` 对象。

### 使用 JavaAdapter 对象

JavaScript 允许你使用 `JavaAdapter` 类实现多个接口并扩展一个类。然而，JDK 捆绑的 Rhino JavaScript 实现重写了 `JavaAdapter` 的实现，这仅允许你实现一个接口；它不允许你扩展类。`JavaAdapter` 构造函数的第一个参数是要实现的接口，第二个参数是实现该接口抽象方法的脚本对象。要在 Nashorn 中使用 `JavaAdapter` 对象，你需要加载 Rhino 兼容性模块。以下代码片段使用 `JavaAdapter` 实现了 `Calculator` 接口：

`// 需要在 Nashorn 中加载兼容性模块。`

`// 在 Rhino 中不需要以下 load() 调用。`

`load("nashorn:mozilla_compat.js");`

`// 加载创建 calculator JavaScript 对象的脚本`

`load("calculator.js");`

`var calc = new JavaAdapter(com.jdojo.script.Calculator, calculator);`

现在，你可以像之前一样使用 `calc` 对象。它是 `com.jdojo.script.Calculator` 接口的一个实例，其实现由 `calculator.js` 文件中脚本定义的 `calculator` 对象中声明的方法提供。

### 使用 Java.extend() 方法

Nashorn 提供了一种更好的方式来扩展类和使用 `Java.extend()` 方法实现多个接口。在 `Java.extend()` 方法中，你最多可以传入一个类类型和多个接口类型。它返回一个组合了所有传入类型的类型。你需要使用前面讨论的类似匿名类的语法来为新类型的抽象方法提供实现，或覆盖被扩展类型的现有方法。以下代码片段使用 `Java.extend()` 方法实现了 `Calculator` 接口：

`// 获取 Calculator 接口类型`

`var Calculator = Java.type("com.jdojo.script.Calculator");`

`// 获取一个扩展了 Calculator 类型的类型`

`var CalculatorExtender = Java.extend(Calculator);`

`// 在 CalculatorExtender 中实现抽象方法`

`// 使用类似匿名类的语法`

`var calc = new CalculatorExtender() {`

`add: (function (n1, n2) n1 + n2),`

`subtract: (function (n1, n2) n1 - n2),`

`multiply: (function (n1, n2) n1 * n2),`

`divide: (function (n1, n2) n1 / n2)`

`};`

`// 使用 Calculator 接口的实例`

`var x = 15.0, y = 10.0;`

`var addResult = calc.add(x, y);`

`var subResult = calc.subtract(x, y);`

`var mulResult = calc.multiply(x, y);`

`var divResult = calc.divide(x, y);`

`printf("calc.add(%.2f, %.2f) = %.2f", x, y, addResult);`

`printf("calc.subtract(%.2f, %.2f) = %.2f", x, y, subResult);`

`printf("calc.multiply(%.2f, %.2f) = %.2f", x, y, mulResult);`

`printf("calc.divide(%.2f, %.2f) = %.2f", x, y, divResult);`

`calc.add(15.00, 10.00) = 25.00`

`calc.subtract(15.00, 10.00) = 5.00`

`calc.multiply(15.00, 10.00) = 150.00`

`calc.divide(15.00, 10.00) = 1.50`

你可以使用 `Java.extend()` 方法来扩展具体类、抽象类和接口。以下代码扩展了具体类 `java.lang.Thread` 并实现了 `Calculator` 接口。新的实现覆盖了 `Thread` 类的 `run()` 方法：

`// 获取 Calculator 接口类型`

`var Calculator = Java.type("com.jdojo.script.Calculator");`

`var Thread = Java.type("java.lang.Thread");`

`// 获取一个扩展了 Calculator 类型的类型`

`var ThreadCalcExtender = Java.extend(Thread, Calculator);`

`// 在 CalculatorExtender 中实现抽象方法`

`// 使用类似匿名类的语法`

`var calcThread = new ThreadCalcExtender() {`

`add: (function (n1, n2) n1 + n2),`

`subtract: (function (n1, n2) n1 - n2),`

`multiply: (function (n1, n2) n1 * n2),`

`divide: (function (n1, n2) n1 / n2),`

`run: function () {`

`var n1 = Math.random();`

`var n2 = Math.random();`

`printf("n1 = %.2f, n2 = %.2f", n1, n2);`

`var addResult = this.add(n1, n2);`

`printf("calc.add(%.2f, %.2f) = %.2f", n1, n2, addResult);`

`}`

`};`

`// 启动线程`

`calcThread.start();`

`n1 = 0.61, n2 = 0.66`

`calc.add(0.61, 0.66) = 1.27`



### 使用 JavaScript 函数

有时 Java 接口只有一个方法。在这种情况下，你可以传递一个 JavaScript 函数对象来代替接口的实现。Java 中的 `Runnable` 接口只有一个方法 `run()`。当你在 JavaScript 中需要使用 `Runnable` 接口的实例时，可以传递一个 JavaScript 函数对象。以下代码片段展示了如何创建一个 `Thread` 对象并启动它。在 `Thread` 类的构造函数中，传递了一个 JavaScript 函数对象 `myRunFunc`，而不是 `Runnable` 接口的实例：

`function myRunFunc() {`

`print("A thread is running.");`

`}`

`// 调用 Thread(Runnable) 构造函数，并传递 myRunFunc 函数对象`

`// 该对象将作为 Runnable 接口中 run() 方法的实现。`

`var thread = new java.lang.Thread(myRunFunc);`

`thread.start();`

`A thread is running.`

### 访问超类的方法

在 Java 中，你可以使用关键字 `super` 访问超类的方法。当你在 JavaScript 中扩展一个类时，你也可以使用 `Java.super()` 方法访问超类的方法。该方法接受一个在 JavaScript 中被扩展的 JavaScript 对象，并返回一个可用于调用超类方法的引用。请考虑如清单 6-4 所示的 `Person` 类代码。

清单 6-4\. Person 类

`// Person.java`

`package com.jdojo.script;`

`public class Person {`

`private String firstName;`

`private String lastName;`

`public Person(String firstName, String lastName){`

`this.firstName = firstName;`

`this.lastName = lastName;`

`}`

`public String getFirstName() {`

`return firstName;`

`}`

`public void setFirstName(String firstName) {`

`this.firstName = firstName;`

`}`

`public String getLastName() {`

`return lastName;`

`}`

`public void setLastName(String lastName) {`

`this.lastName = lastName;`

`}`

`public String getFullName() {`

`return firstName + " " + lastName;`

`}`

`}`

请考虑清单 6-5 中的代码。它扩展了 `Person` 类并重写了 `getFullName()` 方法。

清单 6-5\. 使用 Java.super() 方法访问超类方法

`// supermethod.js`

`var Person = Java.type("com.jdojo.script.Person");`

`var PersonExtender = Java.extend(Person);`

`// 扩展 Person 类并重写 getFullName() 方法`

`var john = new PersonExtender("John", "Jacobs") {`

`getFullName: function () {`

`// 你可以在此处使用外部声明的变量 john。`

`var _super_ = Java.super(john);`

`var fullName = _super_.getFullName();`

`return fullName.toUpperCase();`

`}`

`}`

`// 使用扩展类的实现获取 john 的全名`

`var johnFullName = john.getFullName() ;`

`// 获取 john 的超类引用`

`var johnSuper = Java.super(john);`

`// 从 Person 类获取 john 的全名`

`var johnSuperFullName = johnSuper.getFullName();`

`// 打印姓名`

`print("Extended full name:", johnFullName);`

`print("Super full name:", johnSuperFullName);`

`Extended full name: JOHN JACOBS`

`Super full name: John Jacobs`

请注意，扩展后的 `Person` 类的 `getFullName()` 方法引用了在函数外部声明的变量 `john`。以下语句分配了一个对象的引用，该对象可用于调用 `john` 对象的超类方法。

`var _super_ = Java.super(john);`

重写后的方法调用了 `Person` 类的 `getFullName()` 方法，将姓名转换为大写，并返回结果。代码再次获取了超类引用，如下所示：

`// 获取 john 的超类引用`

`var johnSuper = Java.super(john);`

最后，代码打印了从 `Person` 类和扩展后的 `Person` 类返回的值，以证明你确实能够调用超类方法。

提示

除了使用 `Java.super(obj)` 方法获取 `obj` 的超类对象引用并调用其方法外，你还可以使用 `obj.super$MethodName(args)` 语法来调用名为 `obj` 的对象的超类方法。例如，在示例中，你可以使用 `john.super$getFullName()` 来调用对象 `john` 上 `Person` 类的 `getFullName()` 方法。

## 使用 Lambda 表达式

JavaScript 支持可用作 lambda 表达式的匿名函数。以下是一个匿名函数，它接受一个数字作为参数并返回其平方：

`function (n) {`

`return n * n;`

`}`

以下是一个在 JavaScript 中使用匿名函数作为 lambda 表达式创建 `Runnable` 对象的示例。该 `Runnable` 对象用于 `Thread` 类的构造函数中。

`var Thread = Java.type("java.lang.Thread");`

`// 使用 Runnable 对象创建一个 Thread。该 Runnable 对象`

`// 是使用匿名函数作为 lambda 表达式创建的。`

`var thread = new Thread(function () {`

`print("Hello Thread");`

`});`

`// 启动线程`

`thread.start();`

使用 lambda 表达式的 JavaScript 代码对应的 Java 代码如下：

`// 使用 Runnable 对象创建一个 Thread。该 Runnable 对象`

`// 是使用 lambda 表达式创建的。`

`Thread thread = new Thread(() -> {`

`System.out.println("Hello Thread");`

`});`

`// 启动线程`

`thread.start();`

## 总结

在脚本中使用 Java 类型创建对象之前，需要先将 Java 类型导入脚本。在脚本中导入类型有四种方法：使用 `Packages` 全局对象、使用 `Java.type()` 方法、使用 `importPackage()` 和 `importClass()` 函数，以及在 `with` 子句中使用 `JavaImporter`。Nashorn 将 `java`、`javax`、`org`、`com`、`edu` 和 `net` 声明为全局变量，它们分别是 `Packages.java`、`Packages.javax`、`Packages.org`、`Packages.com`、`Packages.edu` 和 `Packages.net` 的别名。因此，你可以使用这些包中任何类型的完全限定名来引用该类型。

在脚本中创建 Java 对象时，需要将 `new` 运算符与 Java 类型一起使用。使用 `Java.type()` 方法可以统一地导入 Java 类型（包括数组类型）。你可以像创建任何其他类型的对象一样创建数组对象。

大多数情况下，重载的 Java 方法调用由 Nashorn 解析。如果你想从脚本中调用重载方法的特定版本，可以使用方括号表示法指定具体的方法签名。例如，`pt"print(java.lang.Object)"` 会在名为 `pt` 的对象引用上调用 `print(java.lang.Object)` 方法，并将 10.5 作为参数传递给该方法。

Nashorn 允许你在脚本中使用 `Java.extend()` 方法扩展接口、抽象类和具体类。它还允许你使用 `Java.super()` 方法在对象上调用超类方法。

