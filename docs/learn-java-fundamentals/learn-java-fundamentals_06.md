# 6. 类与对象

本章将向你介绍类和对象。然后，它重点介绍在使用这些语言特性时需要了解的十个额外主题。

## 类简介

*类*是用于创建你自己的用户定义类型的模板。你可以使用类来描述虚拟实体（例如账户）或物理实体（例如车辆）。

在本节中，你首先将学习如何声明类。然后，你将学习如何使用字段、方法和构造函数来填充类。



### 声明类

通过指定关键字 `class` 后跟一个非保留字且作为类名的标识符来声明类。随后是一对匹配的开花括号（`{`）和闭花括号（`}`），用于界定类的主体。此语法如下所示：

```
'class' 标识符
'{'
// 主体
'}'
```

按照惯例，类名的首字母大写，后续字母小写。当名称由多个单词组成时，每个单词的首字母大写（例如，`ChequingAccount`——加拿大采用的英式拼写，而 `CheckingAccount` 是美国更常用的拼写）。这被称为*驼峰式大小写*。

以下示例声明了一个名为 `Vehicle` 的类：

```
class Vehicle
{
// 主体
}
```

声明之后，你可以在 `Vehicle`（或任何其他类）的主体中填充字段、方法和构造器。

### 通过字段描述状态

类通过其属性（统称为*状态*）来建模抽象或物理实体。例如，账户（抽象实体）有余额。此外，车辆（物理实体）有品牌、型号和制造年份。这种状态存储在被称为*字段*的变量中。

字段声明具有以下最小语法：

```
类型 标识符 ';'
```

字段声明由一个 `类型` 后跟一个非保留字的 `标识符` 组成。分号终止声明。

字段会被隐式初始化为默认值：对于引用字段，该值被解释为 null；对于整数字段，为 0；对于浮点字段，为 0.0；对于布尔字段，为 false。

以下示例在 `Vehicle` 中声明了三个字段：

```
class Vehicle
{
String make;
String model;
int year;
}
```

这三个字段是*实例字段*（也称为*对象字段*或*非静态字段*）的示例，因为每个 `Vehicle` 实例（对象）都将拥有这些非静态字段的自己的副本。

### 通过方法描述行为

类还建模抽象或物理实体的行为。例如，账户支持存款，车辆支持移动。这些行为由被称为*方法*的命名代码块实现。另一个定义：*方法*是在类的上下文中执行的函数。

方法声明具有以下最小语法：

```
返回类型 标识符 '(' [参数列表] ')'
'{'
// 方法体
'}'
```

方法声明以 `返回类型` 开头，后跟一个非保留字的标识符来命名该方法。此名称后跟一个由括号分隔的 `参数列表`。由花括号分隔的代码体（*块*）完成声明。

`返回类型` 标识方法通过 `return` 语句返回的值的类型，我将在后面讨论。例如，如果方法返回 32 位整数，其返回类型将是 `int`。当方法不返回值时，其返回类型是 `void`。

`参数列表` 是一个逗号分隔的参数声明列表：每个声明是一个类型后跟一个非保留字的标识符来命名该参数。

*参数* 是在调用方法或构造器时接收实参的变量。（*实参* 是一个表达式值，其类型与其对应的参数兼容。）

参数是其方法或构造器的局部变量。它在方法或构造器被调用时创建，并在方法或构造器返回其调用者时消亡。换句话说，其*生命周期*是方法的执行期间。任何代码都可以在方法内访问参数。换句话说，其*作用域*是整个方法。

以下示例在 `Vehicle` 类中声明了六个方法（以及之前的三个字段）：

```
class Vehicle
{
String make;
String model;
int year;
String getMake()
{
return make;
}
String getModel()
{
return model;
}
int getYear()
{
return year;
}
void setMake(String _make)
{
make = _make;
}
void setModel(String _model)
{
model = _model;
}
void setYear(int _year)
{
year = _year;
}
}
```

`getMake()`、`getModel()` 和 `getYear()` 方法使用 `return` 语句将各自字段的值返回给调用者。此语句具有以下语法：

```
'return' [表达式] ';'
```

一个 `表达式` 后跟一个分号，可选地跟在保留字 `return` 之后。`return` 的 `表达式` 的类型必须与方法中的 `返回类型` 一致。

注意

当需要提前退出一个返回类型为 `void` 的方法，或退出一个没有返回类型的构造器时，你可以单独指定 `return`，如 `return;`。也许某个条件已经满足，你需要立即退出。例如，你有一个 `copy()` 方法，它在 `while` 循环的上下文中将一个文件的内容复制到另一个文件。其返回类型是 `void`，并且你只想复制直到文件末尾。你可以通过 `if` 语句来测试这种可能性（假设代码中先前已声明了一个布尔变量 `eof`）：`if (eof) return;`。

`setMake()`、`setModel()` 和 `setYear()` 方法设置 `make`、`model` 和 `year` 字段的值。它们的返回类型设置为关键字 `void`，以表明它们不向调用者返回值。

这三个方法是*实例方法*（也称为*对象方法*或*非静态方法*）的示例，因为它们可以使用关键字 `this` 来访问 `Vehicle` 实例（对象）的非静态字段（我稍后会展示如何操作）。

注意

`setMake()`、`setModel()` 和 `setYear()` 上的“`set`”前缀将这些方法标识为*设置器方法*。类似地，`getMake()`、`getModel()` 和 `getYear()` 上的“`get`”前缀将这些方法标识为*获取器方法*。



#### 局部变量

你可以在方法内部声明额外的变量，作为方法实现的一部分。这些变量被称为*局部变量*，因为它们局限于该方法内部——无法从方法外部访问。此外，它们只在方法执行期间存在。在这两种情况下，参数也是如此。

考虑以下计算 *n*!（阶乘）的示例：

```
long factorial(int n)
{
if (n < 0)
return -1;
if (n == 0 || n == 1)
return 1;
long result = 1;
for (int i = n; i > 1; i--)
result *= i;
return result;
}
```

该方法计算*阶乘*（所有小于或等于某个数 *n* 的正整数的乘积，即 *n* x (*n* – 1) x (*n* - 2) x … x 3 x 2 x 1），该乘积基于传递给参数 `n` 的参数。

`factorial()` 方法首先测试参数 `n` 的值，以确保其为正数。如果该值小于 0，`factorial()` 返回 -1，表示传入了负数参数。根据定义，阶乘的乘积中不能包含负整数。

注意

第 11 章介绍了一种更好的技术来处理意外参数，例如将负整数传递给 `factorial()` 的参数 `n`。

接下来，该方法测试参数 `n` 的值是否为 0 或 1。如果传入了其中任何一个值，`factorial()` 返回 1。根据空乘积的约定（[`http://en.wikipedia.org/wiki/Empty_product`](http://en.wikipedia.org/wiki/Empty_product)），0! 的值为 1。

接着，`factorial()` 声明了一个名为 `result` 的局部变量，并将其初始化为 `1`。

警告

局部变量必须初始化。当编译器发现试图读取一个未初始化的局部变量时，会报告错误。

然后，该方法进入一个 `for` 循环来计算阶乘。`for` 语句的测试部分由 `i > 1` 组成，而不是 `i >= 1`，因为任何整数乘以 1 都等于其本身；将 `1` 赋值给 `result` 已经处理了这次乘法。

每次循环迭代都使用 `*=` 复合赋值运算符，将 `result` 的值乘以 `i` 的值，并将乘积存储回 `result`。

最后，`result` 的值被返回给调用者。

除了 `result`，`factorial()` 还声明了第二个局部变量：`i`。虽然这两个变量都只在该方法执行期间存在，但它们的作用域不同。`result` 变量在整个方法中都可访问。然而，`i` 仅在 `for` 循环及其 `result *= i;` 复合赋值语句中可访问。在声明它的 `for` 循环语句之前或之后，都无法访问它。

更具体地说，局部变量的生命周期和作用域仅限于声明它的块及其子块。这就是为什么 `i` 在 `for` 循环外部不可访问的原因，该循环的头部后面跟着一个单语句块。以下示例应该能阐明这一点：

```
{
int j; // j 在此处开始存在
{
j = 5; // 正确：j 仍然存在
}
}
j = 10; // 错误：j 已不存在
```

#### 方法重载

你可以在同一个类中声明具有相同名称但不同参数列表的方法，这一特性称为*方法重载*。当编译器遇到方法调用表达式时，它会将所调用方法的逗号分隔的参数列表与每个重载方法的参数列表进行比较，以寻找要调用的正确方法。

当两个同名方法的参数列表在参数数量或顺序上不同时，它们就是重载的。或者，当两个同名方法至少有一个参数的类型不同时，它们也是重载的。例如，考虑以下三个 `add()` 方法，它们将两个或三个值相加——第一个方法将两个双精度浮点值相加，第二个方法将两个整数相加，第三个方法将三个整数相加：

```
double add(double a, double b)
{
return a + b;
}
int add(int a, int b)
{
return a + b;
}
int add(int a, int b, int c)
{
return a + b + c;
}
```

你不能仅通过更改方法的返回类型来重载它。例如，你不能同时指定 `int add(int a, int b)` 和 `double add(int a, int b)`，因为当编译器遇到 `add(4, 5);` 时，它没有足够的信息来区分这些方法。编译器会报告一个“重定义”错误。

### 通过构造函数描述初始化

一个类可以声明一个或多个代码块，用于在创建对象时对其进行初始化。这种代码块称为*构造函数*。其声明由一个头部后跟一个花括号分隔的主体组成。头部以声明该构造函数的类名开头，因为构造函数没有自己的名称。后面跟着一个可选的括号括起来的参数列表：

```
className '(' [parameterList] ')'
'{'
// 构造函数主体
'}'
```

`className` 必须与声明该构造函数的类名匹配。`parameterList` 是一个逗号分隔的参数列表。后面跟着一个花括号分隔的主体，其中包含在调用构造函数时要执行的代码。与方法不同，构造函数没有返回类型，因为它不返回值。

注意

当一个类没有声明任何构造函数时，编译器会生成一个默认的空的无参构造函数。

以下示例在 `Vehicle` 类中声明了一个构造函数。该构造函数将 `Vehicle` 的 `make`、`model` 和 `year` 字段初始化为传递给构造函数 `_make`、`_model` 和 `_year` 参数的参数：

```
class Vehicle
{
// ...
Vehicle(String _make, String _model, int _year)
{
make = _make;
model = _model;
year = _year;
}
// ...
}
```

为简洁起见，我没有包含 `Vehicle` 类中的所有内容。第一个 `//...` 是字段声明的简写。第二个 `//...` 是各种方法声明的简写。

参数名称带有前导下划线，以防止赋值出现问题。例如，如果你将 `_make` 重命名为 `make` 并指定 `make = make;`，你将一事无成，因为你将参数的值赋给了它自身。你也可以通过给字段名称添加 `this.` 前缀来避免此问题：

```
Vehicle(String make, String model, int year)
{
this.make = make;
this.model = model;
this.year = year;
}
```

保留字 `this` 指的是当前对象。通过在字段名称前加上 `this.`，你表示你正在引用当前对象对该字段的副本。

在重构构造函数的同时，你可能还想重构 `setMake()`、`setModel()` 和 `setYear()` 方法。例如，你可以将 `setMake()` 从

```
void setMake(String _make)
{
make = _make;
}
```

改为

```
void setMake(String make)
{
this.make = make;
}
```

以利用 `this.` 前缀。

与实例字段名称相同的参数（或局部变量）名称会*遮蔽*（隐藏或掩盖）该字段。关键字 `this` 代表当前对象的引用。在字段名称前加上 `this.` 可以消除遮蔽，因为它访问的是字段名称，而不是同名的参数。

虽然你可以通过构造函数中显示的赋值来初始化诸如 `make` 之类的字段，但最好通过 setter 方法（例如前面展示的 `setMake()`）来执行赋值，我如下演示：

```
Vehicle(String make, String model, int year)
{
setMake(make);
setModel(model);
setYear(year);
}
```

因为未来版本的 `setMake()`、`setModel()` 和 `setYear()` 可能会执行额外的初始化任务，为什么要在构造函数中重复这些代码呢？



#### 调用构造函数

类可以声明多个构造函数。例如，考虑一个仅接受制造商和型号参数的 `Vehicle` 构造函数——它将年份设置为 -1，以表示该车辆的制造年份未知。该构造函数如下所示：

```
Vehicle(String make, String model)
{
setMake(make);
setModel(model);
setYear(-1);
}
```

这个新构造函数的问题在于它重复了原始构造函数中的代码：`setMake(make);` 后跟 `setModel(model);`。重复的代码会给类增加不必要的臃肿。Java 通过提供 `this()` 语法来避免这种重复，使一个构造函数能够调用另一个构造函数：

```
Vehicle(String make, String model)
{
this(make, model, -1);
}
```

`this()` 调用将其参数传递给 `Vehicle(String make, String model, int year)` 构造函数。编译器知道要调用这个构造函数，因为它将构造函数的参数数量和参数类型与 `this()` 调用的参数数量和参数类型进行了匹配。

**注意**

如果在构造函数的 `this()` 调用之前放置任何代码，编译器将报告错误。例如，`make = "{" + make + "}"; this(make, model, -1);` 是错误的，因为赋值语句位于 `this()` 调用之前。

### 综合运用

我创建了一个 `Vehicle` 类，其中包含了前面介绍的各种增强功能。清单 6-1 展示了 `Vehicle` 的源代码。

```
class Vehicle
{
String make;
String model;
int year;
Vehicle(String make, String model)
{
setMake(make);
setModel(model);
setYear(-1);
}
Vehicle(String make, String model, int year)
{
setMake(make);
setModel(model);
setYear(year);
}
String getMake()
{
return make;
}
String getModel()
{
return model;
}
int getYear()
{
return year;
}
void setMake(String make)
{
this.make = make;
}
void setModel(String model)
{
this.model = model;
}
void setYear(int year)
{
this.year = year;
}
}
清单 6-1
Vehicle.java
```

按如下方式编译清单 6-1：

```
javac Vehicle.java
```

由于生成的 `Vehicle.class` 文件不是一个应用程序，因此无法运行它。我们将在本章后面通过一个包含 `main()` 方法的类（类似于第 1 章中所示的方法）来访问它。这两个类将共同描述我们的应用程序。

## 对象介绍

*对象*是类的实例。它们代表特定的抽象实体（例如您的储蓄账户）或特定的物理实体（例如您正在阅读的漫画书或您正在驾驶的卡车）。

在本节中，您首先学习如何构造对象。然后学习如何访问它们的字段并调用它们的方法。

### 构造对象

声明类之后，就可以从中构造对象。通过将 `new` 运算符与构造函数结合使用来构造对象。例如，可以通过指定 `new` 运算符后跟一个 `Vehicle` 构造函数来构造一个或多个 `Vehicle` 对象，如下所示：

```
Vehicle v = new Vehicle("Ford", "F150", 2023);
```

`new` 运算符会为 `Vehicle` 对象分配内存，然后使用参数 `"Ford"`、`"F150"` 和 `2023` 调用其三参数构造函数。`Vehicle` 的 `make`、`model` 和 `year` 字段被初始化为这些值。完成后，`new` 返回对新创建的 `Vehicle` 对象的引用。然后，该引用被赋值给变量 `v`。

**注意**

如果您曾疑惑为什么构造函数没有返回类型，原因在于 `new` 无法在返回对新构造对象的引用的同时返回构造函数的值。

### 访问字段

现在您已经构造了一个对象，可以使用成员选择运算符（`.`）配合对象引用来访问对象的字段。例如，按如下方式访问先前创建的 `Vehicle` 对象的字段：

```
System.out.println(v.make); // Ford
System.out.println(v.model); // F150
System.out.println(v.year); // 2023
```

直接访问字段通常不是一个好主意。相反，您应该调用 getter 方法来访问字段。虽然这看起来有些多余，但它可以防止在需要删除或重命名字段时，访问该字段的代码出现故障。在本章后面讨论信息隐藏时，您将了解更多关于此主题的内容。

### 调用方法

使用 `.` 运算符配合对象引用来调用对象的方法。例如，按如下方式调用先前创建的 `Vehicle` 对象的方法：

```
v.setMake("Dodge");
v.setModel("Durango");
v.setYear(2023);
```

之后，您可以调用对应的“get”方法来返回这些值：

```
System.out.println(v.getMake()); // Dodge
System.out.println(v.getModel()); // Durango
System.out.println(v.getYear()); // 2023
```

一个方法（或构造函数）调用包含零个或多个参数，这些参数被传递给方法（或构造函数）的形参。Java 通过*按值传递*传递参数，即传递变量的值或其他表达式的值。相比之下，C++ 语言还支持*按引用传递*，它将参数的地址传递给方法，以便修改参数（不能是值）。

方法调用需要一个方法调用栈来跟踪当被调用的方法完成时执行必须返回的语句。（*栈*是一种数据结构，用于记住诸如在走出迷宫时已访问过的先前位置之类的事情。）

*方法调用栈*是一个方法调用的栈，它让 Java 虚拟机（JVM）能够记住当执行 `return` 语句或执行离开没有 `return` 的方法时，应该返回到哪个语句。此外，方法调用栈会基于每次方法调用跟踪形参和局部变量。

**注意**

可以将方法调用栈想象成自助餐厅里的一摞干净托盘——您从摞顶*弹出*一个干净托盘，洗碗工将下一个干净托盘*推*到摞顶。相反，您也可以将此栈想象成一摞脏托盘——您将一个脏托盘*推*到摞顶，洗碗工从摞顶*弹出*下一个脏托盘。

当调用一个方法时，JVM 将调用方法返回后要执行的下一条语句的地址，连同被调用方法的参数一起推入方法调用栈。JVM 还会为方法的形参和/或局部变量分配栈空间。当方法返回时，JVM 移除形参/局部变量空间，从栈中弹出地址和参数，并将执行转移到给定地址处的语句。

### 综合运用

我创建了一个 `Vehicles` 类，其中包含了前面介绍的各种增强功能。清单 6-2 展示了 `Vehicles` 的源代码。

```
class Vehicles
{
public static void main(String[] args)
{
Vehicle v = new Vehicle("Ford", "F150", 2023);
System.out.println(v.make);
System.out.println(v.model);
System.out.println(v.year);
v.setMake("Dodge");
v.setModel("Durango");
v.setYear(2023);
System.out.println(v.getMake());
System.out.println(v.getModel());
System.out.println(v.getYear());
}
}
清单 6-2
Vehicles.java
```

假设 `Vehicles.java` 与 `Vehicle.java` 以及先前创建的 `Vehicle.class` 位于同一目录中，按如下方式编译清单 6-2：

```
javac Vehicles.java
```

假设 `Vehicles.class` 已成功创建，按如下方式运行应用程序：

```
java Vehicles
```

您应该会看到以下输出：

```
Ford
F150

Dodge
Durango

```



## 附加主题

关于类和对象，还有更多内容需要学习。本节将向你介绍信息隐藏、对象初始化、类的另一种用途、类初始化、字段访问规则、方法调用规则、常量、方法调用链、递归和可变参数。

### 信息隐藏

一个类的主体由接口和实现组成。*接口*是类中可供类外部代码访问的部分。*实现*是类中用于支持接口的部分。实现应该对外部代码隐藏，以便可以根据不断变化的需求进行修改。

开发人员致力于开发提供不变接口的类，同时隐藏实现，使其能够在不影响外部代码的情况下进行演变。隐藏实现的任务被称为*信息隐藏*，它与*封装*相关，即将字段、方法和构造函数组合到类中。

注意

封装让我们能够在更高的抽象层次（类和对象）上进行编程，而不是分别关注数据结构和功能。

以 `Vehicle` 类为例。构造函数和方法头构成了该类的接口。构造函数和方法内部的代码以及各种字段属于实现部分。没有必要直接访问这些字段，因为它们可以通过 getter 和 setter 方法进行读写。

由于没有采取任何预防措施，直接访问这些字段是可能的。使用之前的 `v` 引用变量，你可以指定 `v.make`、`v.model` 和 `v.year`，编译器不会报错。你需要利用访问级别来阻止对这些字段的访问（或者至少确定谁可以访问它们）。

*访问级别*指明了谁可以访问字段、方法或构造函数。Java 支持四种访问级别：private、public、protected 和包级私有（默认）——我将在第 10 章中讨论包。Java 提供了三个关键字，分别对应前三种访问级别：

*   `private`：只有与字段、方法或构造函数位于同一类中的代码才能访问该成员。
*   `public`：任何包中任何类的任何代码都可以访问该成员。
*   `protected`：同一类或其子类（在第 7 章中讨论）中的任何代码都可以访问该成员。

如果没有关键字，则默认为包级私有访问。包级私有访问类似于公共访问，即类外部的代码可以访问该成员。但是，与公共访问不同，该代码必须位于与包含要访问成员的类属于同一包的类中——请参阅第 10 章。

你可以阻止外部代码访问 `Vehicle` 的 `make`、`model` 和 `year` 字段，这样任何从 `Vehicle` 外部访问这些字段的尝试都会导致编译器错误消息。通过在它们的声明前加上 `private` 来实现这一点，如下所示：

```
class Vehicle
{
private String make;
private String model;
private int year;
// ...
}
```

为了给你一个更好的例子，说明为什么你应该养成隐藏至少大部分类实现的习惯，请考虑以下简化的 `Employee` 类：

```
class Employee
{
private String firstName;
private String lastName;
String getFullName()
{
return firstName + " " + lastName;
}
}
```

`getFullName()` 方法返回员工的全名。它隐藏了没有 `fullName` 字段这一事实。如果 `firstName` 和 `lastName` 被暴露，它们将成为类接口的一部分。当开发人员决定用单个 `fullName` 字段替换 `firstName` 和 `lastName` 时，该接口就会被破坏。

### 对象初始化

对象通常通过其构造函数进行初始化。然而，还有另外几种方法可以至少完成部分初始化工作。

注意

当构造对象时，`new` 运算符会将对象的非静态字段清零。

#### 通过实例字段初始化器进行显式字段初始化

*实例字段初始化器*（也称为*对象字段初始化器*或*非静态字段初始化器*）是一个赋值运算符，后跟一个与实例字段类型兼容的表达式，并将该表达式的值赋给实例字段。例如，考虑以下 `Database` 类：

```
class Database
{
private Driver driver = DEFAULT_DRIVER;
Database(/* ... */)
{
// ...
driver = loadDriver();
if (driver == null)
System.out.println("unable to load driver ... using default instead");
}
Driver loadDriver()
{
// ...
}
// ...
}
```

`Database` 通过实例字段初始化器将 `driver` 字段初始化为默认设备驱动程序。构造函数尝试加载一个非默认的、功能更强的驱动程序。如果无法加载驱动程序，则会输出一条消息，并且 `Database` 对象将不得不使用默认驱动程序。

#### 实例初始化块

*实例初始化块*（也称为*对象初始化块*或*非静态初始化块*）是引入到类主体中而不是方法主体中的语句块。例如，考虑对 `Vehicle` 类的以下修改：

```
class Vehicle
{
private String make;
private String model;
private int year;
// 下面的实例初始化块将 make、model 和 year 字段初始化为指定值。
{
make = "Ford";
model = "F150";
year = 2023;
}
}
```

因为你可以在构造函数中初始化对象，所以实例初始化块的唯一用途是在*匿名类*（没有名称的类）的上下文中，匿名类没有构造函数，我将在第 9 章中讨论。

注意

对于每个构造函数，编译器都会在类文件中生成一个特殊的 `<init>()` 方法。（由于 `<` 和 `>` 字符在标识符中是非法的，因此源代码中不可能存在这样的方法，所以程序员无法创建同名方法并导致冲突。）

`<init>()` 方法包含所有发生的对象初始化的字节码。编译器从类的顶部开始向下工作，首先为遇到的每个实例字段初始化器和实例初始化块放置字节码，然后将构造函数的初始化代码放入此方法中。JVM 在构造对象时在运行时调用 `<init>()`。

### 工具类

类主要用于创建对象。然而，它们还有另一种用途：工具类。*工具类*由类字段和/或类方法组成。



#### 类字段

*类字段*（也称为*静态字段*）通过在声明前添加保留字 `static` 来声明，语法如下：

```
'static' 类型 ['=' 表达式] ';'
```

与每个对象拥有自己副本的实例字段不同，类字段被所有对象共享。当该字段被更新时，所有对象都能看到这一变化。

必须创建对象才能访问实例字段。相比之下，访问类字段则无需创建任何对象。

考虑下面这个简单的 `Counter` 工具类的例子：

```
class Counter
{
static int count;
}
```

我们正在创建一个 `Counter` 类，用于持有一个 `count` 字段，多个类中的代码都可以访问它。每个代码块都会递增此字段以跟踪某些活动，而其他代码块则可以读取 `count` 的当前值来决定它们要采取的行动路线。

一个代码块通常通过指定以下表达式来访问 `count`，该表达式由类名后跟 `.` 运算符再后跟字段名组成：

```
Counter.count
```

你可以指定 `Counter.count = 1;` 来将 `count` 初始化为 `1`。你可以指定 `System.out.println(Counter.count)` 来读取 `count` 的值，然后将其输出。

#### 类方法

*类方法*（也称为*静态方法*）通过在声明前添加保留字 `static` 来声明，语法如下：

```
'static' 返回类型 标识符 '(' [参数列表] ')'
'{'
// 方法体
'}'
```

与可以访问其实例字段副本的实例方法不同，类方法不能访问任何实例字段。但是，它可以访问类字段。

考虑清单 6-3 中 `Temperature` 工具类的例子。

```
class Temperature
{
static double c2f(double degrees)
{
return degrees * 9.0 / 5.0 + 32.0;
}
}
清单 6-3
Temperature.java
```

`Temperature` 声明了一个工具方法：`c2f()`。此方法将传递给参数 `degrees` 的参数从`摄氏度`转换为`华氏度`并返回结果。

我创建了一个演示 `Temperature` 的 `UseTemperature` 类。清单 6-4 展示了其源代码。

```
class UseTemperature
{
public static void main(String[] args)
{
System.out.println(Temperature.c2f(37));
}
}
清单 6-4
UseTemperature.java
```

假设 `Temperature.java` 和 `UseTemperature.java` 位于同一目录，你可以使用以下便捷技巧编译这两个源文件：

```
javac *.java
```

`*`（星号）字符充当通配符，匹配当前目录中的所有 *Java 源文件*（扩展名为 `.java` 的文件）。

假设一切顺利，你应该会在当前目录中看到 `Temperature.class` 和 `UseTemperature.class` 文件。

按如下方式运行生成的应用程序：

```
java UseTemperature
```

你应该会看到以下输出：

```
98.6
```

请注意，37 摄氏度相当于 98.6 华氏度（正常体温）。

仔细查看清单 6-4：你会注意到一些有趣的事情。`main()` 方法被标记为 `static`。此方法作为定义应用程序入口点的工具方法——每个应用程序类都是一个工具类。当你通过 `java` 工具运行应用程序时，该工具会加载 JVM，然后执行 `main()`。

### 类初始化

类通过类字段初始化器和类初始化块进行初始化，它们分别等价于实例字段初始化器和实例初始化块。

注意

当一个类被加载到内存中时，JVM 会将类的静态字段清零。

#### 通过类字段初始化器进行显式字段初始化

*类字段初始化器*（也称为*静态字段初始化器*）是一个赋值运算符后跟一个表达式，该表达式的类型与类字段的类型兼容，并将表达式的值赋给该类字段。例如，考虑以下 `Product` 类：

```
class Product
{
static int productID = 1;
void manufacture(String name)
{
// ...
System.out.println("Manufacturing product " + productID++);
}
// ...
}
```

`Product` 代表某种制造的产品。`productID` 字段被标记为 `static`，因为它描述了一个产品标识号。由于类字段初始化器将 `1` 赋给了 `productID`，该编号从 `1` 开始，并在制造产品后于 `manufacture()` 中递增。

#### 类初始化块

*类初始化块*（也称为*静态初始化块*）是一个以 `static` 为前缀的语句块，被引入到类体中。例如，考虑以下 `Graphics` 类，它是某个简单图形包的一部分：

```
class Graphics
{
static double[] sines, cosines;
static
{
sines = new double[360];
cosines = new double[360];
for (int i = 0; i < sines.length; i++)
{
sines[i] = sin(toRadians(i));
cosines[i] = cos(toRadians(i));
}
}
// ...
}
```

`Graphics` 声明了 `sines` 和 `cosines` 数组变量。它还声明了一个类初始化块，该块创建了 360 个元素的 `double` 数组，其引用被赋给了 `sines` 和 `cosines`。然后，它使用一个 `for` 循环，通过调用 `sin()` 和 `cos()` 方法将这些数组元素初始化为相应的正弦和余弦值。循环索引 `i` 表示从 0 到 360 度的角度单位。在每次调用 `sin()` 和 `cos()` 时，此参数都会被传递给 `toRadians()`，以将角度值转换为弧度。

提示

性能对图形应用程序很重要，访问数组元素比调用方法更快。因此，开发人员会采用性能技巧，例如创建并初始化正弦和余弦数组。

注意

对于每个类，编译器都会生成一个 `<clinit>()` 方法，其中包含所有发生的类初始化的字节码。编译器从类的顶部到底部工作，将遇到的每个类字段初始化器和类初始化块的字节码放入此方法中。JVM 在将类文件加载到内存后，会在运行时调用 `<clinit>()`。

### 字段访问规则

根据字段的类型（对象字段或类字段）和上下文（从类内部或从类外部的代码访问），字段的访问方式有所不同。以下四条规则将帮助你在不同上下文中访问不同类型的字段时避免错误：

*   当从同一类的另一个实例字段、构造器或实例方法中访问实例字段时，指定不带前缀的实例字段名。示例：`make`。

*   当从同一类的另一个实例字段或类字段、构造器、实例方法或类方法中访问类字段时，指定不带前缀的类字段名。示例：`count`。

*   当从其类外部或从同一类的类方法中访问实例字段时（假设该字段可访问），指定对象引用后跟 `.` 运算符再跟实例字段名。示例：`v.make`。

*   当从其类外部访问类字段时（假设该字段可访问），指定类名后跟 `.` 运算符再跟类字段名。示例：`Counter.count`。

不要忘记，在某些情况下，变量遮蔽是一个问题。你可以通过在实例字段名前添加 `this` 后跟 `.` 运算符，或在类字段名前添加类名后跟 `.` 运算符来解决此问题。例如，如果你必须将参数值赋给同名的实例字段，你也需要在字段名前添加 `this` 和 `.` 运算符。



### 方法调用规则

方法的调用方式取决于方法的类型（对象方法或类方法）以及调用上下文（从类内部调用还是从类外部的代码调用）。以下是在不同上下文中调用不同类型方法的四条规则：

*   从同一类的另一个实例方法或构造器中调用实例方法时，直接指定实例方法名，不加前缀。例如：`getMake()`。

*   从同一类的另一个对象、类方法或构造器中调用类方法时，直接指定类方法名，不加前缀。例如：`c2f(37)`。

*   从类外部或从同一类的类方法中调用方法时（前提是该方法可访问），指定对象引用，后跟`.`运算符，再跟实例方法名。例如：`v.getMake()`。

*   从类外部调用方法时（前提是该方法可访问），指定类名，后跟`.`运算符，再跟类方法名。例如：`Temperature.c2f(37)`。

不要忘记确保传递给方法的参数数量、传递顺序以及参数类型，与所调用方法中的形参一一对应。否则，编译器会报错。

### final 字段

*final 字段*是其声明以关键字 `final` 开头的字段。final 字段有两种：类 final 字段和实例 final 字段。

#### 类 final 字段

*类 final 字段*是使用 `static` 关键字声明的 final 字段。当类被加载和初始化时，该 final 字段与类关联。

类 final 字段也称为*常量*，因为该字段的值永远不会改变。你通常在工具类中声明常量。

以下示例声明了一个名为 `PI` 的常量：

```
final static double PI = 3.14159;
```

根据 Oracle（Java 的当前维护者）的“Java 语言代码约定”文档（[`www.oracle.com/java/technologies/javase/codeconventions-contents.xhtml`](http://www.oracle.com/java/technologies/javase/codeconventions-contents.xhtml)），常量应全部大写，单词之间用下划线（_）分隔。

养成在代码中使用常量而不是“魔法数字”的习惯是个好主意。例如，考虑以下代码片段：

```
radius = 10;
area = 3.14159 * radius * radius;
circumference = 3.14159 * radius * 2;
```

此代码片段计算半径为 `10` 个单位的圆的面积和周长。注意 `3.14159` 在代码中出现了两次。

像这样编写魔法数字并不是一个好主意，因为当需要更改时，你必须修改该数字的所有实例。如果你忘记修改某个实例，就有可能在代码中引入错误。以下代码片段通过用 `PI` 替换 `3.14159` 解决了这个问题：

```
radius = 10;
area = PI * radius * radius;
circumference = PI * radius * 2;
```

#### 实例 final 字段

*实例 final 字段*是在没有 `static` 关键字的情况下声明的 final 字段。当对象被创建时，该 final 字段与该对象关联。

一些开发者认为实例 final 字段是常量，因为只要与其关联的对象存在，实例 final 字段就永远不会改变。其他开发者则认为实例 final 字段不是常量，因为它们不是在编译时被赋值的，并且不必遵循与类 final 字段相同的命名约定。

与必须在声明时初始化的常量（参见前面的 `PI` 示例）不同，实例 final 字段不必在声明时初始化。相反，初始化可以推迟到构造器中进行。当这种情况发生时，实例 final 字段被称为*空白 final*。

考虑以下示例：

```
class Counter
{
final int Count1 = 10; // 实例 final 字段，对于每个构造的对象保持不变
final int Count2;      // 空白 final，在构造器中初始化
Counter(int initialCount) // 每个空白 final 可以接收不同的值，
{                         // 该值在对象的整个生命周期内保持不变
Count2 = initialCount;
}
}
```

很难想到实例 final 字段的有效用途。要了解它们可能用于什么，请查看 stackoverflow 的“使用非静态公共 final 变量公开实例常量”文章（[`http://stackoverflow.com/questions/12248635/exposing-instance-constants-with-non-static-public-final-variables`](http://stackoverflow.com/questions/12248635/exposing-instance-constants-with-non-static-public-final-variables)）。

### 方法调用链

两个或多个实例方法调用可以通过`.`运算符链接在一起，从而产生更紧凑的代码。要实现实例方法调用链，你需要重新设计你的实例方法。关键在于将你的方法设计为返回对当前对象的引用，这通过 `this` 关键字来表示。

我重构了 `Vehicle.java` 和 `Vehicles.java` 来演示方法调用链。清单 6-5 展示了 `Vehicle.java` 的内容。

```
class Vehicle
{
private String make;
private String model;
private int year;
Vehicle(String make, String model)
{
setMake(make).setModel(model).setYear(-1);
}
Vehicle(String make, String model, int year)
{
setMake(make).setModel(model).setYear(year);
}
String getMake()
{
return make;
}
String getModel()
{
return model;
}
int getYear()
{
return year;
}
Vehicle setMake(String make)
{
this.make = make;
return this;
}
Vehicle setModel(String model)
{
this.model = model;
return this;
}
Vehicle setYear(int year)
{
this.year = year;
return this;
}
}
清单 6-5
Vehicle.java
```

注意，`setMake()`、`setModel()` 和 `setYear()` 的返回类型已从 `void` 更改为 `Vehicle`，并且每个方法现在都以 `return this;` 语句结束，以返回对当前 `Vehicle` 对象的引用。另外，注意构造器中的 `setMake(make).setModel(model).setYear(-1);` 和 `setMake(make).setModel(model).setYear(year);` 调用。

清单 6-6 展示了 `Vehicles.java` 的内容。

```
class Vehicles
{
public static void main(String[] args)
{
Vehicle v = new Vehicle("Ford", "F150", 2023);
System.out.println(v.getMake());
System.out.println(v.getModel());
System.out.println(v.getYear());
v.setMake("Dodge").setModel("Durango").setYear(2023);
System.out.println(v.getMake());
System.out.println(v.getModel());
System.out.println(v.getYear());
}
}
清单 6-6
Vehicles.java
```

注意 `v.setMake("Dodge").setModel("Durango").setYear(2023);` 链式方法调用，这演示了方法调用链。

按照我在清单 6-2 之后演示的方式编译并运行 `Vehicles.java` 和 `Vehicle.java`，你应该会看到相同的输出。



### 递归

方法通常会执行包含调用其他方法的语句。然而，让一个方法调用自身也常常很有用。这种编程技术被称为*递归*。

例如，假设你需要编写一个返回*阶乘*的方法，阶乘是指从 1 到指定整数（含该整数）的所有正整数的乘积。已知 `!` 是阶乘的数学符号，你可以推断出 4! 等于 4x3x2x1，即 24。此外，1! 等于 1，0! 也等于 1。编写此方法的第一种方法可能包含本章前面展示的代码。

前面展示的 `factorial()` 代码通过迭代完成了任务。然而，采用递归风格可以更简洁地编写此方法：

```
long factorial(int n)
{
if (n < 0)
return -1;
if (n == 0 || n == 1)
return 1; // 基础问题
else
return n * factorial(n - 1);
}
```

递归方法用自身更简单的形式来表达问题。根据这个例子，最简单的问题，也称为*基础问题*，是 0! (1) 或 1! (1)。

当向 `factorial()` 传递一个大于 1 的参数时，该方法通过用下一个更小的参数值调用自身，将问题分解为一个更简单的问题。最终，会达到基础问题。例如，调用 `factorial(4)` 会产生以下表达式堆栈：

```
4 * factorial(3)
3 * factorial(2)
2 * factorial(1)
```

最后一个表达式位于堆栈顶部。当 `factorial(1)` 返回 1 时，这些表达式会随着堆栈开始展开而按以下顺序求值：

*   `2 * factorial(1)` 现在变为 2*1 (2)
*   `3 * factorial(2)` 现在变为 3*2 (6)
*   `4 * factorial(3)` 现在变为 4*6 (24)

递归为表达许多问题提供了一种优雅的方式。其他示例包括在基于树的数据结构中搜索特定值，以及在分层文件系统中查找并输出包含特定文本的所有文件的名称。

警告

递归会消耗堆栈空间，因此请确保你的递归最终会终止于一个基础问题；否则，你将耗尽堆栈空间，并且你的应用程序将被迫终止。

### 可变参数

JDK 5 引入了一项功能，简化了向方法和构造函数传递可变数量参数的语法。此功能被称为*可变参数*。

在声明接受可变数量参数的方法或构造函数时，在最右侧参数的类型名称后指定三个连续的点。考虑以下实例方法示例，它返回整数列表中的最小值：

```
int min(int... values)
```

此方法头声明了一个 `min()` 方法，该方法声明了可变数量的参数。你可以像下面这样调用此方法：

```
min(-8, 55, 0, -12, 14);
```

在 JDK 5 之前，你必须使用数组类型来声明该方法：

```
int min(int[] values)
```

此方法将按以下方式调用：

```
min(new int[] {-8, 55, 0, -12, 14});
```

可变参数功能是*语法糖*（使代码编写更甜美的语法）的一个例子。在编译期间，编译器会将较旧且更繁琐的 `int[] {}` 语法转换为此功能的更现代的 `...` 版本。

清单 6-7 展示了一个 `VarargsDemo` 应用程序类的源代码，该类在 `min()` 方法的两个版本中演示了可变参数。

```
class VarargsDemo
{
public static void main(String[] args)
{
System.out.println(min(-8, 55, 0, -12, 14));
System.out.println(min(new int[] {-8, 55, 0, -12, 14}));
System.out.println(min2(new int[] {-8, 55, 0, -12, 14}));
//      System.out.println(min2(-8, 55, 0, -12, 14));
}
static int min(int... values)
{
int smallest = 0;
for (int i = 0; i < values.length; i++)
if (values[i] < smallest)
smallest = values[i];
return smallest;
}
static int min2(int[] values)
{
int smallest = 0;
for (int i = 0; i < values.length; i++)
if (values[i] < smallest)
smallest = values[i];
return smallest;
}
}
清单 6-7
VarargsDemo.java
```

`VarargsDemo` 声明了 `main()`、`min()` 和 `min2()` 方法。`main()` 方法运行应用程序。`min()` 和 `min2()` 方法分别使用较新的可变参数和较旧的数组功能来接收可变数量的参数。

尽管这些方法具有不同的方法头，但它们具有相同的方法体，这证明了可变参数是基于一维数组实现的。

每个方法的方法体都包含用于从 `values` 数组中定位并返回最小整数的代码。数组的长度由 `values.length` 指定。

也许你想知道为什么我没有尝试通过指定 `min(int[] values)` 而不是 `min(int... values)` 来重载 `min()`。如果我尝试这样做，编译器会报告错误，指出无法同时声明 `min(int... values)` 和 `min(int[] values)`。在幕后，`min(int... values)` 会被转换为 `min(int[] values)`。

按如下方式编译清单 6-7：

```
javac VarargsDemo.java
```

按如下方式运行生成的 `VarargsDemo.class` 类文件：

```
java VarargsDemo
```

你应该会看到以下输出：

```

```

## 接下来是什么？

`Vehicle` 过于泛化，无法表示特定类型的车辆，例如卡车或汽车。它也可以表示滑板或自行车。为了容纳更具体的车辆类型，我们需要通过类扩展来进行特化，这是第 7 章的主题。

