# 9. 静态类、非静态类、局部类和匿名类

到目前为止我所介绍的类被称为*顶层类*。它们不属于另一个类或结构。然而，Java 也允许你在其他类、*块*（位于 `{` 和 `}` 字符之间的零个或多个语句的集合）以及表达式上下文中声明类。

在其他类中声明的类被称为*嵌套类*。嵌套类有两种：静态类和非静态类。（非静态类也称为*内部类*）。

注意

Java 教程中关于嵌套类的教程（[`http://docs.oracle.com/javase/tutorial/java/javaOO/nested.xhtml`](http://docs.oracle.com/javase/tutorial/java/javaOO/nested.xhtml)）给出了使用嵌套类的三个令人信服的理由：

*   **“这是一种对仅在一个地方使用的类进行逻辑分组的方式**：如果一个类只对另一个类有用，那么将其嵌入到那个类中并将两者放在一起是合乎逻辑的。嵌套这样的“辅助类”可以使它们的包参见第 [10 章关于包的介绍]更加精简。

*   **它增加了封装性**：考虑两个顶层类 `A` 和 `B`，其中 `B` 需要访问 `A` 的成员，而这些成员原本会被声明为 `private`。通过将类 `B` 隐藏在类 `A` 内部，`A` 的成员可以被声明为 `private`，而 `B` 可以访问它们。此外，`B` 本身也可以对外部世界隐藏。

*   **它可以带来更具可读性和可维护性的代码**：将小类嵌套在顶层类中，可以使代码更靠近其使用位置。”

在块中声明的类被称为*局部类*。在表达式上下文中声明的类被称为*匿名类*。

本章将向你介绍静态类、内部类、局部类和匿名类。



## 静态类

第 6 章向你介绍了静态字段、静态方法、静态字段初始化器和静态初始化块。这些实体与对应的非静态字段、非静态方法、非静态字段初始化器和非静态初始化块的区别在于，它们涉及 `static` 关键字。

你可以在类中声明这些静态成员。你也可以在类中声明带有 `static` 关键字的类。这种嵌套类被称为*静态类*。以下示例阐明了这种组织方式：

```
class C
{
static int f; // 字段可以被显式初始化
static void m()
{
// 方法代码
}
static // 静态初始化块
{
// 初始化块代码
}
static class SC
{
// 静态类的成员
}
}
```

此示例引入了顶层类 `C`，其中包含静态字段 `f`、静态方法 `m()`、一个静态初始化块以及静态类 `SC`。请注意，`SC` 是 `C` 的一个成员，就像 `f`、`m()` 和静态初始化块是该类的成员一样。它们都属于类 `C`，而 `C` 是一个*封闭类*——`SC` 是一个*被封闭类*。

静态类，如同静态方法和静态字段一样，与其封闭类相关联。并且与静态方法类似，静态类不能直接访问封闭类的非静态字段，也不能调用其非静态方法。它只能通过对象引用来访问它们。然而，它可以访问封闭类的静态字段并调用其静态方法。

注意

静态类与其封闭类（以及其他类）的非静态成员的交互方式，与任何其他顶层类完全相同。本质上，静态类的行为就像一个为了打包方便而被嵌套在另一个顶层类中的顶层类。

作为顶层类的一个成员，静态类可以被声明为 `private`、`public`、`protected`，或者保持包级私有（默认）。相比之下，顶层类只能被声明为 `public` 或保持包级私有。（第 10 章将向你介绍包。）

清单 9-1 声明了一个 `EnclosingClass`，其中包含一个 `EnclosedClass`，该封闭类演示了对 `EnclosingClass` 成员的访问。

```
class EnclosingClass
{
private static String msg1;
private static void enclosingMethod1()
{
System.out.println(msg1);
}
private String msg2;
private void enclosingMethod2()
{
System.out.println(msg2);
}
static class EnclosedClass
{
static void enclosedMethod1()
{
msg1 = "called from EnclosedClass's enclosingMethod1() method";
enclosingMethod1();
}
void enclosedMethod2()
{
System.out.println("unable to access msg2 or call enclosingMethod2()");
// msg2 = "called from EnclosedClass's enclosingMethod2() method";
// enclosingMethod2();
}
}
}
清单 9-1
EnclosingClass.java
```

清单 9-1 声明了一个名为 `EnclosingClass` 的顶层类，其中包含静态字段 `msg1`、静态方法 `enclosingMethod1()`、非静态字段 `msg2`、非静态方法 `enclosingMethod2()` 以及静态类 `EnclosedClass`。被封闭类声明了静态方法 `enclosedMethod1()` 和非静态方法 `enclosedMethod2()`。有两点需要注意：

*   `enclosedMethod1()` 能够访问 `EnclosingClass` 的 `msg1` 字段并调用其 `enclosingMethod1()` 方法，即使两者都被声明为 `private`。
*   `enclosedMethod2()` 无法访问 `EnclosingClass` 的 `msg2` 字段并调用其 `enclosingMethod2()` 方法，因为无法从静态上下文中访问封闭类中的非静态字段和非静态方法。

清单 9-2 展示了一个 `SCDemo` 应用程序类的源代码，该类演示了如何调用 `EnclosedClass` 的 `enclosedMethod1()` 静态方法。它还演示了如何实例化 `EnclosedClass` 并调用其 `enclosedMethod2()` 非静态方法。

```
class SCDemo
{
public static void main(String[] args)
{
EnclosingClass.EnclosedClass.enclosedMethod1();
EnclosingClass.EnclosedClass ec = new EnclosingClass.EnclosedClass();
ec.enclosedMethod2();
}
}
清单 9-2
SCDemo.java
```

清单 9-2 的 `main()` 方法首先展示了，要调用静态方法，必须在被封闭类的名称前加上其封闭类的名称。然后展示了，在实例化被封闭类时，必须在其名称前加上封闭类的名称。之后，你就可以像平常一样调用非静态方法了。

按如下方式编译这两个清单：

```
javac *.java
```

按如下方式运行生成的应用程序：

```
java SCDemo
```

你应该会看到以下输出：

```
called from EnclosedClass's enclosingMethod1() method
unable to access msg2 or call enclosingMethod2()
```



### 一个更实用的静态类示例

`SCDemo` 让你有机会学习如何使用静态类，但让我们考虑一个更实际的例子。我创建了一个小型 `Rectangle` 类（在图形上下文中很有用），它受益于一对静态类。清单 9-3 展示了 `Rectangle` 的源代码。

```
abstract class Rectangle
{
static class Float extends Rectangle
{
float x, y, w, h;
Float()
{
}
Float(float x, float y, float w, float h)
{
this.x = x;
this.y = y;
this.w = w;
this.h = h;
}
@Override
double getX()
{
return x;
}
@Override
double getY()
{
return y;
}
@Override
double getW()
{
return w;
}
@Override
double getH()
{
return h;
}
}
static class Double extends Rectangle
{
double x, y, w, h;
Double()
{
}
Double(double x, double y, double w, double h)
{
this.x = x;
this.y = y;
this.w = w;
this.h = h;
}
@Override
double getX()
{
return x;
}
@Override
double getY()
{
return y;
}
@Override
double getW()
{
return w;
}
@Override
double getH()
{
return h;
}
}
boolean contains(double x, double y)
{
return (x >= getX() && x = getY() && y < getY() + getH());
}
abstract double getX();
abstract double getY();
abstract double getW();
abstract double getH();
}
清单 9-3
Rectangle.java
```

`Rectangle` 通过左上角坐标（x 和 y）以及范围（宽度和高度）来描述一个矩形形状。该类被声明为 `abstract`，因为它声明了四个抽象方法：`getX()`、`getY()`、`getW()` 和 `getH()`。回顾第 8 章，在非抽象类中声明抽象方法是错误的。

`Rectangle` 还声明了一个 `boolean contains(double x, double y)` 方法，当由 `x` 和 `y` 描述的点位于矩形内部时，该方法返回 true；否则返回 false。`contains()` 方法将传递给它的参数与通过调用 `Rectangle` 的抽象方法形成的表达式进行比较。

`Rectangle` 最有趣的部分是它声明了两个静态类。这些类提供了存储 `Rectangle` 对象位置和范围所需的字段。

第一个静态类是 `Float`，第二个静态类是 `Double`。每个类都声明了 `x`、`y`、`w`（宽度）和 `h`（高度）字段。此外，每个类都声明了一个无参构造函数，将这些字段初始化为 0。接着是一个显式将这些字段初始化为传入参数的构造函数。

注意

如果没有声明无参构造函数，则无法调用此构造函数，因为编译器不会生成等效的 `<init>()` 方法——有关 `<init>()` 的简要介绍，请参见第 6 章。

然后，`Float` 和 `Double` 声明了 `getX()`、`getY()`、`getW()` 和 `getH()` 方法，这些方法返回这些字段的值。唯一的区别是一组字段和方法是 `float` 类型，而另一组字段和方法是 `double` 类型。

这些方法带有 `@Override` 注解前缀，因为 `Float` 和 `Double` 扩展了 `Rectangle`，并覆盖了 `Rectangle` 声明的抽象方法 `getX()`、`getY()`、`getW()` 和 `getH()`。（除了在第 7 章中简要讨论过 `@Override` 之外，本书不讨论注解，因为我认为它不是基本特性。）

你可能会对这种不寻常的组织方式感到困惑。理解它的最佳方法可能是查看清单 9-4，其中展示了演示 `Rectangle` 的 `UseRect` 应用程序的源代码。

```
class UseRect
{
public static void main(String[] args)
{
System.out.println("Rectangle.Float Demo");
System.out.println();
Rectangle r = new Rectangle.Float(10.0f, 20.0f, 30f, 40f);
System.out.println(r.getX());
System.out.println(r.getY());
System.out.println(r.getW());
System.out.println(r.getH());
System.out.println("contains(40.0, 45.0): " + r.contains(35.0, 40.0));
System.out.println("contains(80.0, 45.0): " + r.contains(80.0, 40.0));
System.out.println();
System.out.println("Rectangle.Double Demo");
System.out.println();
r = new Rectangle.Double(15.0, 25.0, 35, 45);
System.out.println(r.getX());
System.out.println(r.getY());
System.out.println(r.getW());
System.out.println(r.getH());
System.out.println("contains(40.0, 45.0): " + r.contains(40.0, 45.0));
System.out.println("contains(80.0, 45.0): " + r.contains(80.0, 45.0));
}
}
清单 9-4
UseRect.java
```

请注意以下语句：

```
Rectangle r = new Rectangle.Float(10.0f, 20.0f, 30f, 40f);
r = new Rectangle.Double(15.0, 25.0, 35, 45);
```

第一条语句实例化了 `Float` 类，第二条语句实例化了 `Double` 类。传递给每个类构造函数的参数存储在其 `x`、`y`、`w` 和 `h` 字段中。由于 `Float` 和 `Double` 扩展了 `Rectangle`，返回的对象也是 `Rectangle` 类型，其引用可以赋值给 `Rectangle` 变量 `r`。

`Float` 和 `Double` 各自扩展了 `Rectangle`，以提供单精度浮点和双精度浮点的 `Rectangle` 实现。`Float` 的存在是为了减少内存消耗（在构建场景时，你可能会有数千个甚至更多这样的对象），而 `Double` 的存在是为了在需要更高精度时使用。

当调用 `Rectangle` 的方法（如 `getX()`）时，例如 `r.getX()`，子类型多态性就会发挥作用。Java 用于实现子类型多态性的向上转型和后期绑定机制（参见第 8 章）会导致调用 `Float` 的 `getX()` 方法（在第一条语句中）和 `Double` 的 `getX()` 方法（在第二条语句中）。

按如下方式编译清单 9-3 和 9-4：

```
javac *.java
```

按如下方式运行应用程序：

```
java UseRect
```

你应该会看到以下输出：

```
Rectangle.Float Demo
10.0
20.0
30.0
40.0
contains(40.0, 45.0): true
contains(80.0, 45.0): false
Rectangle.Double Demo
15.0
25.0
35.0
45.0
contains(40.0, 45.0): true
contains(80.0, 45.0): false
```



## 内部类

第 6 章还向你介绍了非静态字段、非静态方法、非静态字段初始化器和非静态初始化块。这些实体与对应的静态字段、静态方法、静态字段初始化器和静态初始化块的不同之处在于，它们不涉及 `static` 关键字。

你可以在一个类中声明这些非静态成员。你也可以在一个类中声明非静态类（内部类）。下面的示例阐明了这种组织方式：

```
class C
{
int f; // 字段可以被显式初始化
void m()
{
// 方法代码
}
// 非静态初始化块
{
// 初始化块代码
}
class NSC
{
// 非静态类的成员
}
}
```

这个示例引入了顶层类 `C`，它包含非静态字段 `f`、非静态方法 `m()`、一个非静态初始化块（我本可以展示一个构造器）以及内部类 `NSC`。请注意，`NSC` 是 `C` 的一个成员，就像 `f`、`m()` 和非静态初始化块是这个类的成员一样。它们都属于类 `C`，而 `C` 是一个封闭类——`NSC` 是一个被封闭类。

内部类是其封闭类的一个成员。内部类可以访问封闭类的其他成员，即使它们被声明为 `private`。相比之下，静态类不能直接访问封闭类的非静态成员。

作为顶层类的一个成员，内部类可以被声明为 `private`、`public`、`protected`，或者保持包私有（默认）。相比之下，顶层类只能被声明为 `public` 或保持包私有。（第 10 章将向你介绍包。）

清单 9-5 声明了 `EnclosingClass` 和 `EnclosedClass`，后者演示了对 `EnclosingClass` 成员的访问。

```
class EnclosingClass
{
public final static double PI = 3.14159;
public static double area(double radius)
{
return PI * radius * radius;
}
private String msg;
private void printMessage()
{
System.out.println(msg);
}
class EnclosedClass
{
void accessMembersOfEnclosingClass()
{
msg = "called from EnclosedClass's accessMembersOfEnclosingClass() " + "method";
printMessage();
System.out.println("PI = " + PI);
System.out.println("area(10.0) = " + area(10.0));
}
}
}
清单 9-5
EnclosingClass.java（版本 2）
```

清单 9-5 声明了一个名为 `EnclosingClass` 的顶层类，它包含静态字段 `PI`、静态方法 `area()`、非静态字段 `msg`、非静态方法 `printMessage()` 以及非静态类 `EnclosedClass`。此外，`EnclosedClass` 声明了非静态方法 `accessMembersOfEnclosingClass()`。

该方法首先将一个字符串赋值给 `msg`。（因为字符串相当长，我使用 `+` 运算符将两个较短的字符串拼接成整个字符串。）接着，该方法输出 `msg`。然后，`accessMembersOfEnclosingClass()` 打印 `PI` 的值，接着（以 `10.0` 作为参数）调用 `area()` 来计算并返回一个圆的面积。同样，我使用 `+` 将两个字符串连接成一个字符串，然后输出。

清单 9-6 展示了一个 `NSCDemo` 应用程序类的源代码，该类演示了如何实例化 `EnclosingClass`，并使用这个引用来实例化 `EnclosedClass`，进而调用该类的 `accessMembersOfEnclosingClass()` 非静态方法。

```
class NSCDemo
{
public static void main(String[] args)
{
EnclosingClass ec = new EnclosingClass();
ec.new EnclosedClass().accessMembersOfEnclosingClass();
}
}
清单 9-6
NSCDemo.java
```

清单 9-6 的 `main()` 方法首先实例化 `EnclosingClass` 并将其引用保存在局部变量 `ec` 中。然后，`main()` 使用这个引用作为 `new` 运算符的前缀来实例化 `NSClass`，随后使用该引用调用 `accessMembersOfEnclosingClass()`。

注意

用封闭类的引用作为 `new` 的前缀是很少见的。相反，你通常会在封闭类的构造器或实例方法中调用被封闭类的构造器。

按如下方式编译这两个清单：

```
javac *.java
```

按如下方式运行生成的应用程序：

```
java NSCDemo
```

你应该会看到以下输出：

```
called from EnclosedClass's accessMembersOfEnclosingClass() method
PI = 3.14159
area(10.0) = 314.159
```

### 遮蔽

当某个特定块（例如内部类或方法）中某个类型（例如成员变量[字段]或参数）的声明与封闭块中的另一个类型声明同名时，该声明就会*遮蔽*（隐藏）封闭块中的声明。在这种情况下，你不能仅通过名称来引用被遮蔽的声明。清单 9-7 的 `ShadowDemo` 应用程序提供了一个遮蔽演示。

```
class ShadowDemo
{
String name = "Java";
class EnclosedClass
{
String name = "More Java";
void outputName(String name)
{
System.out.println("name = " + name);
System.out.println("this.name = " + this.name);
System.out.println("ShadowDemo.this.name = " + ShadowDemo.this.name);
}
}
public static void main(String[] args)
{
ShadowDemo sd = new ShadowDemo();
ShadowDemo.EnclosedClass ec = sd.new EnclosedClass();
ec.outputName("Even more Java");
}
}
清单 9-7
ShadowDemo.java
```

`ShadowDemo.java` 揭示了三个 `name` 变量：`ShadowDemo` 的成员变量、内部类 `EnclosedClass` 的成员变量以及 `outputName()` 的参数。参数 `name` 遮蔽了 `EnclosedClass` 的 `name` 变量。因此，当你在 `outputName()` 中使用 `name` 时，你引用的是 `name` 参数。要引用 `EnclosedClass` 的 `name` 变量，请使用关键字 `this` 来表示封闭块：

```
System.out.println("this.x = " + this.x);
```

通过它们所属的类名来引用封闭更大块的成员变量。例如，以下语句从方法 `outputName()` 访问类 `ShadowDemo` 的成员变量：

```
System.out.println("ShadowDemo.this.name = " + ShadowDemo.this.name);
```

按如下方式编译清单 9-7：

```
javac ShadowDemo.java
```

按如下方式运行应用程序：

```
java ShadowDemo
```

你应该会看到以下输出：

```
name = Even more Java
this.name = More Java
ShadowDemo.this.name = Java
```

注意

关于遮蔽的介绍，请参考第 6 章。



### 一个更实用的内部类示例

`NSCDemo` 为你提供了一个学习如何使用内部类的机会。为了更实际地学习如何使用内部类，我们创建一个框架来维护待办事项列表。

每个待办事项包含一个名称和一个描述。代码清单 9-8 展示了一个描述待办事项的 `ToDo` 类。

```
class ToDo
{
private String name;
private String desc;
ToDo(String name, String desc)
{
this.name = name;
this.desc = desc;
}
String getName()
{
return name;
}
String getDesc()
{
return desc;
}
@Override
public String toString()
{
return "Name = " + getName() + ", Desc = " + getDesc();
}
}
代码清单 9-8
ToDo.java
```

接下来，我们将创建一个 `ToDoList` 类来存储 `ToDo` 实例。`ToDoList` 使用其非静态成员类 `ToDoArray` 将 `ToDo` 实例存储在一个可增长的数组中。（之所以可增长，是因为你不知道会存储多少个实例，而 Java 数组的长度是固定的）。参见代码清单 9-9。

```
class ToDoList
{
private ToDoArray toDoArray;
private int index = 0;
ToDoList()
{
toDoArray = new ToDoArray(2);
}
boolean hasMoreElements()
{
return index < toDoArray.length;
}
ToDo nextElement()
{
return toDoArray[index++];
}
void add(ToDo item)
{
if (index >= toDoArray.length)
{
ToDo[] temp = new ToDo[toDoArray.length * 2];
for (int i = 0; i < toDoArray.length; i++)
temp[i] = toDoArray[i];
toDoArray = temp;
}
toDoArray[index++] = item;
}
ToDo get(int i)
{
return toDoArray[i];
}
int size()
{
return index;
}
}
}
代码清单 9-9
ToDoList.java
```

除了提供 `add()` 方法将 `ToDo` 实例存储到 `ToDoArray` 实例中，`ToDoList` 还提供了 `hasMoreElements()` 和 `nextElement()` 方法来遍历并返回已存储的实例。代码清单 9-10 演示了这些方法。

```
class UseToDoList
{
public static void main(String[] args)
{
ToDoList toDoList = new ToDoList();
toDoList.add(new ToDo("#1", "洗衣服。"));
toDoList.add(new ToDo("#2", "买杂货。"));
toDoList.add(new ToDo("#3", "用吸尘器打扫公寓。"));
toDoList.add(new ToDo("#4", "写报告。"));
toDoList.add(new ToDo("#5", "洗车。"));
while (toDoList.hasMoreElements())
System.out.println(toDoList.nextElement());
}
}
代码清单 9-10
ToDoList.java
```

假设代码清单 9-8 到 9-10 描述的文件都存储在当目录中，按如下方式编译这些文件：

```
javac UseToDoList.java
```

按如下方式运行生成的 `UseToDoList.class` 应用程序类文件：

```
java UseToDoList
```

你应该会看到以下输出：

```
Name = #1, Desc = 洗衣服。
Name = #2, Desc = 买杂货。
Name = #3, Desc = 用吸尘器打扫公寓。
Name = #4, Desc = 写报告。
Name = #5, Desc = 洗车。
```

## 局部类

在代码块（例如方法体）或*子块*（块中的块）中声明一个类通常很有帮助。这样的类被称为*局部类*，因为（就像局部变量一样）它对于声明它的块/子块来说是局部的。

注意

局部类有助于提高代码清晰度。这是因为它们被移到了更接近需要它们的地方。

例如，你可以在一个方法中声明一个描述*迭代器*（用于遍历容器对象的对象）的类，并返回该类的实例。这样的类被称为*局部类*，因为（就像局部变量一样）它们对于声明它们的方法来说是局部的。以下是一个示例：

```
interface I
{
// 成员
}
class C
{
I m() // 或者甚至 static I m()
{
class D implements I
{
// 成员
}
return new D();
}
}
```

顶层类 `C` 声明了非静态方法 `m()`，该方法返回局部类 `D` 的实例，而 `D` 是在此方法中声明的。注意，`m()` 的返回类型是接口 `I`，`D` 实现了该接口。这个接口是必需的，因为如果给 `m()` 返回类型 `D` 会导致编译器错误——`D` 在 `m()` 的方法体外部是不可访问的。

警告

当局部类声明包含任何访问修饰符关键字 `private`、`public` 或 `protected`，或修饰符关键字 `static` 时，编译器会报告错误。

局部类可以与其封闭类的实例关联，但仅当在非静态上下文中使用时才可以。尽管局部类可以在静态上下文（例如静态方法）中声明，但它不能声明任何静态成员，这使得局部类类似于内部类，内部类也不能声明静态成员。

注意

你不能在局部块中声明接口，因为接口被认为是静态的，而局部类不能声明静态成员。

局部类可以在任何可以声明局部变量的地方声明，并且具有与局部变量相同的作用域（本质上是一个块）。它可以访问周围作用域的局部变量和参数（参数是一种局部变量）。

在 JDK 8 之前，这些变量必须声明为 `final`。然而，这不再是必需的，因为 JDK 8 引入了*有效 final* 的概念（编译器会将一个从未改变的变量视为 `final`，无论是否有关键字 `final`）。

注意

在局部类中声明的变量可以*遮蔽*（掩盖或隐藏）封闭块中同名的变量。

当局部类访问封闭块的局部变量或参数时，它会*捕获*该局部变量/参数。捕获局部变量或参数意味着制作该局部变量/参数的一个副本，因为从局部类创建的对象可能比声明该局部变量/参数的当前上下文存活得更久。此外，该局部变量/参数必须声明为 `final` 或（从 JDK 8 开始）是有效 final 的，以防止对局部变量/参数的修改是否可见产生混淆（因为这些修改是不可见的）。

考虑代码清单 9-11，它使用了 `final` 关键字。（本书的代码存档中包含第二个版本的 `EnclosingClass`，它没有使用 `final`。在编译该版本的 `EnclosingClass` 时，JDK 8 之前的编译器会报告错误。）

```
class EnclosingClass
{
void m(final int i)
{
final int j = i * 10;
class LClass
{
int m = i;
int n = j;
}
LClass lc = new LClass();
System.out.println(lc.m);
System.out.println(lc.n);
}
}
代码清单 9-11
EnclosingClass.java
```

代码清单 9-11 声明了 `EnclosingClass`，其非静态方法 `m()` 声明了一个名为 `LClass` 的局部类。



`LClass` 声明了两个非静态字段（`m` 和 `n`），当 `LClass` 被实例化时，这两个字段会被初始化为 `final` 参数 `i` 和 final 局部变量 `j` 的值——请参见清单 9-12 中演示 `LClass` 的 `LCDemo` 应用程序的源代码。

```
class LCDemo
{
public static void main(String[] args)
{
EnclosingClass ec = new EnclosingClass();
ec.m(10);
}
}
清单 9-12
LCDemo.java
```

清单 9-12 的 `main()` 方法首先实例化 `EnclosingClass`。然后使用该对象调用 `m(10)`。被调用的 `m()` 方法将该参数乘以 10；实例化 `LClass`，其编译器生成的 `<init>()` 方法（关于 `<init>()` 的简要介绍，请参见第 6 章）将参数和参数乘以 10 的结果赋值给它的两个非静态字段（代替使用构造函数执行此任务）；并输出 `LClass` 的非静态字段值。

按如下方式编译清单 9-11 和 9-12：

```
javac *.java
```

当你编译一个方法中包含局部类的类时，编译器会为该局部类创建一个类文件，其名称由封闭类的名称、一个美元符号、一个从 1 开始的整数以及局部类的名称组成。编译后，除了 `LCDemo.class` 之外，你还会发现 `EnclosingClass$1LClass.class` 和 `EnclosingClass.class`。

编译器为局部类生成的类文件名

在为局部类的类文件生成名称时，编译器会按照 *enclosingClass*`$(`*integer*`)`*enclosedClass* 的模式向生成的名称中添加一个整数。（括号不是最终名称的一部分。）如果有两个同名的局部类，编译器会递增该整数以避免名称冲突。请考虑以下示例：

```
class EnclosingClass
{
void m1()
{
class LClass
{
}
}
void m2()
{
class LClass
{
}
}
void m3()
{
class LClass2
{
}
}
}
```

`EnclosingClass` 声明了三个非静态方法，每个方法都声明了一个局部类。前两个方法生成了两个同名的不同局部类。编译器生成以下类文件：

```
EnclosingClass$1LClass.class
EnclosingClass$1LClass2.class
EnclosingClass$2LClass.class
EnclosingClass.class
```

编译器为 `m1()` 的 `LClass` 局部类生成 `EnclosingClass$1LClass.class`，为 `m3()` 的 `LClass2` 局部类生成 `EnclosingClass$1LClass2.class`，为 `m2()` 的 `LClass` 局部类生成 `EnclosingClass$2LClass.class`。

按如下方式运行应用程序：

```
java LCDemo
```

你应该会看到以下输出：

```

```

### 一个更实用的局部类示例

`LCDemo` 让你有机会学习如何使用局部类。由于能够更实际地学习如何使用局部类会更好，我创建了一个受益于局部类的小型 `AddressBook` 类。在介绍 `AddressBook` 之前，请先查看清单 9-13 中 `AddressBook` 所依赖的 `Address` 类。

```
class Address
{
private String street, city;
Address(String street, String city)
{
this.street = street;
this.city = city;
}
String getStreet()
{
return street;
}
String getCity()
{
return city;
}
@Override
public String toString()
{
return street + ": " + city;
}
}
清单 9-13
Address.java
```

`Address` 以街道和城市的形式记录地址。你可以扩展此类以包含省、州或地区；国家；以及你想要的任何其他信息。

请注意，我重写了 `toString()` 方法，`Address` 从 `Object` 继承了该方法。与默认表示形式相比，此方法提供了 `Address` 内容更易读的表示形式。（如果你还记得，默认表示形式是 *类名*`@`*十六进制哈希码*。）

既然 `Address` 已经介绍完毕，让我们来探索 `AddressBook`。清单 9-14 展示了此类的源代码。

```
class AddressBook
{
private Address[] addressList;
private int index = 0;
AddressBook(int size)
{
addressList = new Address[size];
}
Iterator iterator()
{
class LocalIterator implements Iterator
{
int index = 0;
@Override
public boolean hasMoreElements()
{
return index < addressList.length;
}
@Override
public Object nextElement()
{
return addressList[index++];
}
}
return new LocalIterator();
}
void add(Address address)
{
addressList[index++] = address;
}
}
清单 9-14
AddressBook.java
```

`AddressBook` 首先声明了一对 `private` 字段：`addressList` 和 `index`，后者被显式赋值为 `0`，尽管在创建 `AddressBook` 对象时它会被隐式初始化为 0。这样做是为了向不熟悉默认初始化的人说明该字段被初始化为 0。

`addressList` 字段引用了一个数组（在 `AddressBook(int size)` 构造函数中创建），该数组存储 `Address` 对象。`index` 字段标识了将 `Address` 对象插入到 `addressList` 所引用数组中的下一个位置。

`AddressBook(int size)` 构造函数的 `size` 参数接收一个参数，该参数指定了要在数组中存储的元素数量。当此参数小于 1 时，应注意避免出现问题。如果你指定了负数大小，很可能会抛出 `NegativeArraySizeException` 对象，并且程序会终止。或者，如果你将大小指定为 0，很可能会遇到 `ArrayIndexOutOfBoundsException` 异常，并且程序会终止。毕竟，在这种情况下，创建一个没有元素的数组有什么意义呢？（第 11 章将探讨异常。）

构造函数执行 `addressList = new Address[size];`，使用 `new` 运算符实例化一个可以存储 `size` 个元素的 `Address` 数组。对该数组的引用被赋值给 `addressList`。

构造函数之后的 `iterator()` 方法演示了一个局部类。在我们查看此类之前，请注意 `iterator()` 的返回类型设置为 `Iterator`，这是一个接口，其内容在清单 9-15 中给出。

```
interface Iterator
{
boolean hasMoreElements();
Object nextElement();
}
清单 9-15
Iterator.java
```

`Iterator` 接口描述了什么是*迭代器*，它是一个知道如何遍历存储的*元素*（在本例中是对象值，而不是基于原始类型的值）的对象。

`Iterator` 声明了 `hasMoreElements()` 和 `nextElement()` 这两个无方法体的方法（它们的方法体由实现 `Iterator` 的类提供）：



*   `hasMoreElements()` 在存在更多待检查元素时返回布尔值 true；否则返回 false。

*   `nextElement()` 返回对下一个待检查元素的引用。

让我们回到 `iterator()` 方法，它执行两项操作。首先，它声明了一个名为 `LocalIterator` 的局部类。其次，它实例化该类并返回结果对象的引用。

`LocalIterator` 实现了我之前介绍的 `Iterator` 接口。之所以这样做，是因为 `iterator()` 将返回 `LocalIterator` 类的一个实例，并且 `iterator()` 的返回类型必须与 `iterator()` 末尾 `return` 语句中出现的 `new LocalIterator()` 表达式的类型一致。

`LocalIterator` 声明了一个 `index` 字段，用于跟踪 `nextElement()` 将要返回的下一个元素。该字段*遮蔽*（隐藏或掩盖）了其外部块 `AddressBook` 中的 `index` 字段。

我们不需要访问 `AddressBook` 的 `index` 字段，因为它与 `LocalIterator` 的 `index` 字段用途不同。前一个 `index` 字段跟踪 `addressList` 所引用数组中通过 `add()` 方法插入下一个 `Address` 对象的位置。后一个 `index` 字段跟踪该数组中通过 `nextElement()` 方法返回下一个 `Address` 对象的位置。

接下来，`LocalIterator` 重写了 `Iterator` 接口声明的 `hasMoreElements()` 和 `nextElement()` 方法。请注意每个方法前面的 `@Override` 注解。该注解表明正在发生重写，以防止方法被意外重载而非重写时出现问题。请参阅第 7 章，了解更多关于此困境的信息以及对 `@Override` 的简要介绍。

`hasMoreElements()` 方法将 `index` 与 `addressList.length` 进行比较，当 `index` 小于后一个表达式的值时，返回 true（通过 `return` 语句）。否则，`hasMoreElements()` 返回 false。

`nextElement()` 方法呈现了一个 `return` 语句，该语句通过执行 `addressList[index++]` 从 `addressList` 返回下一个元素。`index++` 表达式在递增 `index` 之前返回 `index` 的当前值。

`AddressBook` 的 `iterator()` 方法之后是一个 `add()` 方法，其 `address` 参数包含对 `Address` 对象的引用。该方法执行 `addressList[index++] = address;`，将 `address` 的引用存储在 `addressList` 的 `index` 位置。请注意，在存储引用后，`index` 的值会递增。

清单 9-16 展示了一个 `UseAB` 应用程序类的源代码，该类演示了 `AddressBook` 及其支持的 `Address` 类和 `Iterator` 接口。

```
class UseAB
{
public static void main(String[] args)
{
AddressBook addressBook = new AddressBook(3);
addressBook.add(new Address("100 Elm Street", "City #1"));
addressBook.add(new Address("200 Bay Street", "City #2"));
addressBook.add(new Address("300 Oak Street", "City #3"));
Iterator iter = addressBook.iterator();
while (iter.hasMoreElements())
System.out.println(iter.nextElement());
}
}
清单 9-16
UseAB.java
```

`UseAB` 的 `main()` 方法首先实例化 `AddressBook`，将 `3`（可存储的最大地址数量）传递给 `AddressBook` 的构造函数。对 `AddressBook` 对象的引用被赋值给局部变量 `addressBook`。

接下来，`main()` 方法创建三个 `Address` 对象，并将它们传递给 `AddressBook` 的 `add()` 方法。对于每个 `Address` 对象，`main()` 调用 `AddressBook` 的构造函数，将街道和城市字符串作为参数传递。`Address` 对象的引用作为参数传递给 `add()`。

下一个任务是执行 `addressBook.iterator()`，它返回一个 `Iterator` 对象（其类实现了 `Iterator` 接口）。该对象的引用被赋值给局部变量 `iter`。

最后，`main()` 进入一个 `while` 循环。只要 `iter.hasMoreElements()` 返回 true，该循环就会迭代。每次迭代都会调用 `iter.nextElement()` 从地址簿返回下一个地址元素。然后通过 `System.out.println()` 输出该元素。

按如下方式编译清单 9-13 到 9-16：

```
javac *.java
```

按如下方式运行 `UseAB` 应用程序：

```
java UseAB
```

您应该会看到以下输出：

```
100 Elm Street: City #1
200 Bay Street: City #2
300 Oak Street: City #3
```



## 匿名类

在某些情况下，在表达式的上下文中声明一个类会很有帮助。例如，在本章后面，我将演示一个基于抽象 `Comparer` 类的匿名类，用于排序场景。这种类被称为*匿名类*，因为它没有名称。

注意

匿名类能让你的代码比局部类更加紧凑。这是因为它们被放置在更接近使用它们的地方。

匿名类可以在类扩展表达式上下文中声明，如下所示：

```
Superclass sc = new Superclass()
{
// 成员
};
```

在这个例子中，一个匿名类扩展了 `Superclass`（在其他地方声明），继承了各种方法和其他成员。请注意，`new` 运算符后面跟着超类的名称，然后是一对花括号。

`new` 运算符实例化匿名类并返回结果对象的引用，该引用被赋值给 `sc`。

此外，匿名类也可以在接口实现表达式上下文中声明，如下所示：

```
Interface i = new Interface()
{
// 成员
};
```

在这个例子中，一个匿名类实现了 `Interface`（在其他地方声明），继承了各种方法体。请注意，`new` 运算符后面跟着接口的名称，然后是一对花括号。

`new` 运算符实例化匿名类并返回结果对象的引用，该引用被赋值给 `i`。

匿名类表达式由四个部分组成：

*   `new` 运算符。
*   要扩展的类或要实现的接口的名称。
*   包含可选构造函数参数的圆括号。当实现接口时，这些圆括号是空的，因为接口没有构造函数。
*   界定匿名类体的花括号。

匿名类声明必须是语句的一部分，因为它是一个表达式。在第一个例子中，匿名类表达式是实例化 `SuperClass` 的语句的一部分。同样，在第二个例子中，匿名类表达式是实例化 `Interface` 的语句的一部分。

实例初始化块作为构造函数

匿名类不能有构造函数，因为构造函数必须以类命名，而匿名类没有名称。相反，你可以使用实例初始化块作为构造函数。以下示例演示了类扩展上下文中的实例初始化块：

```
Superclass sc = new Superclass(10)
{
{
// 在此处执行初始化任务
}
// 其他成员
};
```

此示例假设存在一个名为 `Superclass` 的类，并且它声明了一个 `Superclass(int)` 构造函数。该示例的执行过程如下：

*   `new` 运算符为匿名类分配内存并创建一个对象，该匿名类扩展了 `Superclass`。
*   `new` 运算符使用参数 `10` 调用 `Superclass` 的构造函数。这显示为 `Superclass(10)`。
*   构造函数执行后，匿名类实例初始化块中的任何代码（如果存在）都会执行。

以下示例演示了接口扩展上下文中的实例初始化块：

```
Interface i = new Interface()
{
{
// 在此处执行初始化任务
}
// 其他成员
};
```

此示例假设存在一个名为 `Interface` 的接口。该示例的执行过程如下：

*   `new` 运算符为匿名类分配内存并创建一个对象，该匿名类实现了 `Interface`。
*   尽管看起来可能不同，但 `Interface()` 并不表示调用 `Interface` 的构造函数，因为 `Interface` 没有构造函数。相反，匿名类实例初始化块中的任何代码（如果存在）都会执行。

在 JDK 8 之前，匿名类只能访问那些声明为 `final` 的局部变量。当匿名类访问封闭块的局部变量或参数时，它会*捕获*该局部变量或参数。例如，假设你声明了以下接口：

```
interface Foo
{
void bar();
}
```

注意

`foo`（驼峰式大小写为 `Foo`——参见第 6 章了解驼峰式大小写的定义）和 `bar` 这两个名称有一段有趣的历史。请查看维基百科的“驼峰式大小写”条目（[`http://en.wikipedia.org/wiki/Camel_case`](http://en.wikipedia.org/wiki/Camel_case)）来了解这段历史。

现在假设你在一个方法中填充了以下代码片段：

```
final int i;
Foo foo;
i = 42;
foo = new Foo()
{
@Override
public void bar()
{
System.out.println(i);
}
};
foo.bar();
```

此片段首先声明了局部变量 `i` 和 `foo`。局部变量 `i` 被声明为 `final`，因为它将在匿名类内部被访问。因为我使用的是 JDK 21，所以不需要声明这个局部变量为 `final`。相反，我可以移除 `final` 关键字。但是，如果我使用的是早于 JDK 8 的 JDK 版本，则需要使用 `final`。

然后我将 `int` 变量 `i` 初始化为 `42`。（如果你想知道为什么这么多程序员在他们的示例中使用这个数字，这背后有一段有趣的历史。根据维基百科的“《银河系漫游指南》中的短语”条目（[`http://en.wikipedia.org/wiki/Phrases_from_The_Hitchhiker's_Guide_to_the_Galaxy`](http://en.wikipedia.org/wiki/Phrases_from_The_Hitchhiker%2527s_Guide_to_the_Galaxy)），42 是关于生命、宇宙以及一切问题的答案——或者不是吗？

继续，代码片段声明了一个未命名（匿名）类，该类实现了 `Foo` 接口。为了正确实现此接口，它必须重写其 `bar()` 方法。这由 `@Override` 注解表示（参见第 7 章了解 `@Override`。）

代码片段实例化了实现 `Foo` 的匿名类并返回其引用，该引用被赋值给先前声明的 `foo` 变量。然后它执行 `foo.bar()`。

接口 `Foo` 的 `bar()` 方法头没有显式声明为 `public`。相反，它是隐式声明为 `public` 的。这就是为什么我在匿名类中重写方法头时将其声明为 `public`。

`bar()` 方法调用 `System.out.println()`，将捕获的变量 `i` 的值作为 `println()` 的参数传递。

从 JDK 8 开始，匿名类可以访问封闭块中实际上是 final 的局部变量和参数。例如，在前面的侧边栏中，变量 `i` 被声明为 `final`。但是，如果我移除了 `final` 关键字，这个变量将成为实际上是 final 的，因为它在初始化后从未改变。

注意

如果我在前面示例的 `bar()` 方法中将 `System.out.println(i);` 改为 `System.out.println(i++);`（这会后置递增变量 `i`），那么 `i` 将不再是实际上是 final 的，这是 Java 不允许的。JDK 21 编译器会报告“`cannot assign a value to final variable i`”错误消息。



### 总结

与局部类一样，匿名类可以捕获变量；它们对封闭作用域中的局部变量具有相同的访问权限，无论该作用域是`if`语句头、方法头还是类头之后的块：

*   匿名类可以访问其封闭类的成员。
*   匿名类不能访问其封闭块中未声明为`final`或有效`final`的局部变量。
*   与嵌套类一样，匿名类中声明的变量会遮蔽封闭块中具有相同名称的任何其他声明。

匿名类在其成员方面也与局部类具有相同的限制：

*   不能在匿名类中声明静态初始化器或成员接口。
*   匿名类可以拥有静态成员，前提是这些成员是常量变量。

可以在匿名类中声明以下语言元素：

*   字段
*   额外方法
*   实例初始化器
*   局部类

请记住，不能在匿名类中声明构造函数。

### 一个更实用的匿名类示例

我创建了一个`Sort`应用程序，它提供了匿名类的更实用演示。该应用程序的源代码分布在两个源文件中：`Sort.java`和`Comparer.java`。清单 9-17 展示了`Sort.java`的源代码。

```
class Sort
{
public static void main(String[] args)
{
System.out.println("Sort");
System.out.println("----");
System.out.println();
int[] grades = new int[] { 96, 54, 71, 89, 63, 92 };
outputArray("Unsorted array:", grades);
sort(grades, new Comparer()
{
@Override
public int compare(int x, int y)
{
return x - y;
}
});
outputArray("Sorted array after ascending sort:", grades);
System.out.println();
int[] grades2 = new int[] { 79, 56, 100, 88 };
outputArray("Unsorted array:", grades2);
sort(grades2, new Comparer()
{
@Override
public int compare(int x, int y)
{
return y - x;
}
});
outputArray("Sorted array after descending sort:", grades2);
}
static void outputArray(String msg, int[] x)
{
System.out.println(msg);
System.out.println();
for (int i = 0; i < x.length; i++)
System.out.print(x[i] + " ");
System.out.println();
System.out.println();
}
static void sort(int[] x, Comparer c)
{
for (int pass = 0; pass < x.length - 1; pass++)
{
int min = pass;
for (int i = pass + 1; i < x.length; i++)
if (c.compare(x[i], x[min]) < 0)
min = i;
if (min != pass)
{
int temp = x[min];
x[min] = x[pass];
x[pass] = temp;
}
}
}
}
清单 9-17
Sort.java
```

清单 9-17 中的`Sort`类声明了`main()`、`outputArray()`和`sort()`方法：

*   `main()`是该应用程序的入口点。它首先输出一个标题。然后声明一个 32 位整数数组（学生成绩，未排序），输出该数组，将该数组按升序排序，并输出排序后的数组。接着，它声明第二个未排序成绩数组，输出该数组，将该数组按降序排序，并输出排序后的数组。
*   `outputArray()`在控制台上打印一条消息，后跟一个 32 位整数数组的内容。
*   `sort()`按升序或降序组织整数数组的元素，排序方式由作为第二个参数传递给`sort()`方法的`Comparer`对象决定。它使用选择排序算法来执行此组织操作。

选择排序类似于第 5 章的冒泡排序算法，两者都使用外层循环对数组进行多次遍历。每次遍历都与一个遍历索引相关联，该索引标识下一个要处理的元素。

对于选择排序，内层循环选择最小的元素——最小值。如果最小元素的索引不等于遍历索引，则交换最小元素和遍历编号的元素。对于冒泡排序，内层循环将内层循环索引的元素与当前遍历索引的元素进行比较。如果内层循环索引的元素小于（升序排序）或大于（降序排序）遍历索引的元素，则发生交换。

选择排序强调减少交换次数，而冒泡排序强调简单性。

我对`sort()`方法的讨论提到了一个`Comparer`对象。该对象是从一个扩展了抽象`Comparer`类的匿名类创建的。清单 9-18 展示了`Comparer.java`的源代码。

```
abstract class Comparer
{
abstract int compare(int x, int y);
}
清单 9-18
Comparer.java
```

`Comparer`声明了一个单一的抽象方法`int compare(int x, int y)`，该方法比较`x`和`y`参数中的值，当一个值小于另一个值时返回负数，当这些值相等时返回零，当一个值大于另一个值时返回正数。

回到`main()`方法，你会遇到两次`Comparer`被匿名类子类化然后实例化的情况。我摘录了第一个实例，如下所示：

```
sort(grades, new Comparer()
{
@Override
public int compare(int x, int y)
{
return x - y;
}
});
```

`return x - y;`语句从第一个参数值中减去第二个参数值，以执行升序排序。第二个`Comparer`实例交换了这些参数，得到`return y - x;`语句，从而执行降序排序。

按如下方式编译清单 9-17 和 9-18：

```
javac *.java
```

按如下方式运行`Sort`应用程序：

```
java Sort
```

你应该会看到以下输出：

```
Sort

Unsorted array:
96 54 71 89 63 92
Sorted array after ascending sort:
54 63 71 89 92 96
Unsorted array:
79 56 100 88
Sorted array after descending sort:
100 88 79 56
```

## 接下来是什么？

我在本章和之前的章节中提到了包。什么是包？如何使用它？你将在下一章中找到答案。

