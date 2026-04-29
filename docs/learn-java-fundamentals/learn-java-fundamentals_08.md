# 8. 通过多态改变类型

一些现实世界中的实体可以改变其形态。例如，水（在地球上而非星际空间中）天然是液体，但冻结时会变成固体，加热至沸点时则变成气体。像蝴蝶这样经历变态的昆虫是另一个例子。

改变形态的能力被称为*多态*，在编程语言中建模非常有用。例如，绘制任意形状的代码可以通过引入一个单一的`Shape`类及其`draw()`方法，并为存储在数组中的每个`Circle`实例、`Rectangle`实例和其他`Shape`实例调用该方法，从而更简洁地表达。当为数组实例调用`Shape`的`draw()`方法时，实际被调用的是`Circle`、`Rectangle`或其他`Shape`实例的`draw()`方法。我们说`Shape`的`draw()`方法有多种形态，或者说该方法是多态的。

Java 支持四种多态：

*   **强制多态**：通过隐式类型转换，一个操作服务于多种类型。例如，除法允许你将一个整数除以另一个整数，或将一个浮点值除以另一个浮点值。如果一个操作数是整数，另一个是浮点值，编译器会强制（隐式转换）整数为浮点值，以防止类型错误。（不存在同时支持整数操作数和浮点操作数的除法操作。）将子类对象引用传递给方法的超类参数是强制多态的另一个例子。编译器将子类类型强制转换为超类类型，以将操作限制为超类的操作。

*   **重载多态**：相同的运算符符号或方法名可以在不同的上下文中使用。例如，`+`可用于执行整数加法、浮点加法或字符串拼接，具体取决于其操作数的类型。此外，多个同名方法可以出现在一个类中（通过声明和/或继承）。

*   **参数多态**：在类声明中，字段名可以与不同类型关联，方法名可以与不同的参数和返回类型关联。字段和方法随后可以在每个类实例中采用不同的类型。例如，在一个类实例中，字段可能是`Truck`类型，方法可能返回`Truck`引用；而在另一个类实例中，同一个字段可能是`Car`类型，同一个方法可能返回`Car`引用。Java 通过泛型支持参数多态，本书不讨论这一点。

*   **子类型多态**：一个类型可以作为另一个类型的子类型。当子类型实例出现在超类型上下文中时，对子类型实例执行超类型操作会导致执行该操作的子类型版本。例如，假设`Circle`是`Point`的子类，并且两个类都包含一个`draw()`方法。将`Circle`实例赋值给`Point`类型的变量，然后通过该变量调用`draw()`方法，会导致调用`Circle`的`draw()`方法。子类型多态与继承相辅相成。

许多开发者不认为强制多态和重载多态是有效的多态类型。他们认为强制多态和重载多态不过是类型转换和语法糖。相比之下，参数多态和子类型多态被视为有效的多态类型。

本章通过向上转型和后期绑定向你介绍子类型多态。然后我们将继续讨论抽象类和抽象方法、接口、向下转型和运行时类型识别，以及协变返回类型和基于接口的静态方法等附加主题。



## 向上转型与后期绑定

子类型多态依赖于向上转型和后期绑定。*向上转型*是一种转型形式，即沿着继承层次结构从子类型向上转型为超类型。由于子类型是超类型的特化，因此无需使用`(`*类型*`)`运算符。例如，`Vehicle v = new Truck();` 将 `Truck` 向上转型为 `Vehicle`。这很合理，因为卡车是一种交通工具。

将 `Truck` 向上转型为 `Vehicle` 后，你无法调用 `Truck` 特有的方法，例如返回卡车是普通驾驶室还是加长驾驶室的 `isExtendedCab()` 方法，因为这些方法不属于 `Vehicle` 的接口。将子类缩小为超类后失去对子类型特性的访问权限看似毫无意义，但这是实现子类型多态所必需的。

假设 `Vehicle` 声明了一个 `drive()` 方法，其子类 `Truck` 重写了该方法，`Vehicle v = new Truck();` 刚刚执行完毕，下一行代码指定了 `v.drive();`。那么会调用哪个 `drive()` 方法：`Vehicle` 的 `drive()` 方法还是 `Truck` 的 `drive()` 方法？编译器不知道应该调用哪个 `drive()` 方法。它所能做的只是验证超类中存在该方法，并验证方法调用的参数列表和返回类型与超类的方法声明匹配。然而，编译器还会在编译后的代码中插入一条指令，该指令在运行时获取并使用 `v` 中存储的任何引用来调用正确的 `drive()` 方法。这一任务被称为*后期绑定*。

注意

后期绑定用于对非 final 对象方法的调用。对于所有其他方法调用，编译器知道应该调用哪个方法。它会在编译后的代码中插入一条指令，调用与变量类型（而非其值）相关联的方法。这一任务被称为*早期绑定*。

## 抽象类与抽象方法

在设计类层次结构时，你会发现层次结构顶部的类比底部的类更通用。例如，`Vehicle` 超类比 `Truck` 子类更通用。同样，`Account` 超类比 `CheckingAccount` 子类更通用。

从通用类创建对象是没有意义的。毕竟，一个 `Account` 对象除了描述一个账户之外还能描述什么？它是储蓄账户还是支票账户，或是其他什么？同样，`Vehicle` 对象代表的是哪种交通工具（卡车、飞机、自行车等）？与其在 `Vehicle` 中编写一个空的 `drive()` 方法（踩卡车的油门踏板、蹬自行车的踏板、在滑板上蹬地并向前推进），不如通过将这两个实体声明为抽象来防止该方法被调用以及该类被实例化。

Java 的 `abstract` 保留字允许你声明一个无法实例化的类。当你尝试实例化此类时，编译器会报告错误。`abstract` 保留字也用于声明没有方法体的方法。`drive()` 方法不需要方法体，因为它无法驱动抽象的交通工具。清单 8-1 演示了这一点。

```
abstract class Vehicle
{
private String make, model;
private int year;
Vehicle(String make, String model, int year)
{
this.make = make;
this.model = model;
this.year = year;
}
abstract void drive();
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
@Override
public String toString()
{
return "Make: " + make + ", Model: " + model + ", Year: " + year;
}
}
清单 8-1
Vehicle.java
```

清单 8-1 向你展示了抽象类除了抽象方法之外（甚至代替抽象方法）还可以声明字段、构造方法和非抽象方法。

请注意，`Vehicle` 声明了一个抽象的 `drive()` 方法来描述如何驾驶该交通工具。例如，你踩下油门踏板来驾驶卡车。`Vehicle` 的子类将重写 `drive()` 并提供适当的描述。它们还将继承这些方法，并且它们的构造方法将调用 `Vehicle` 的构造方法。

警告

当你尝试将类同时声明为 abstract 和 final 时，编译器会报告错误。例如，编译器会报错 `abstract final class Vehicle`，因为抽象类无法实例化，而 final 类无法被继承。

当你声明一个方法为 abstract 但未将其类声明为 abstract 时，编译器也会报告错误。在清单 8-1 中从 `Vehicle` 类的头部移除 `abstract` 会导致错误。这是因为当非抽象（具体）类包含抽象方法时，它无法被实例化。

最后，当你扩展一个抽象类时，扩展类必须重写所有抽象方法，否则扩展类本身必须被声明为抽象；否则，编译器将报告错误。



## 接口

*接口*是两个实体相遇并相互作用的点。例如，墙上的电源插座是承载电力的电源线与电器电源线之间的接口，当电源线插入插座时，电力便通过电器电源线输送到电器。

从 Java 的角度来看，*接口*是一个类（字段、构造器和方法）或其他引用类型中暴露出来的部分，外部代码可以与之交互。请参考清单 8-2。

```
class Stack
{
private int[] stack;
private int top;
Stack(int size)
{
stack = new int[size];
top = -1;
}
boolean isEmpty()
{
return top == -1;
}
void push(int item)
{
if (top == stack.length)
{
System.out.println("stack is full");
return;
}
stack[++top] = item;
}
int pop()
{
if (top == -1)
{
System.out.println("stack is empty");
return -1;
}
return stack[top--];
}
}
清单 8-2
Stack.java
```

清单 8-2 展示了一个 `Stack` 类，它实现了一个*栈*，这是一种后进先出的数据结构。这种数据结构有助于代码记住事物，例如在迷宫中寻找出口时记录已经走过的路径。

你可以将一个项目（例如一个 32 位整数）压入栈顶，并从栈顶弹出一个项目。判断栈是否为空也很重要，因为在弹出项目时无法知道已经压入了多少个项目。

`Stack` 的接口是其构造器以及 `isEmpty()`、`push()` 和 `pop()` 方法头。私有的 `stack` 和 `top` 字段以及构造器和方法中的代码构成了实现。

实现应该对外部代码隐藏，以便可以根据不断变化的需求进行修改。当实现暴露时，软件组件之间可能会产生相互依赖。例如，方法代码可能依赖于外部变量，而类的用户可能依赖于本应隐藏的字段。当实现必须演进时（例如，可能必须移除暴露的字段），这种*耦合*可能会导致问题。

接口的概念非常重要，以至于 Java 通过 `interface` 保留字将其规范化。Java 开发者使用此特性来抽象类的接口，从而将*类与其用户解耦*。通过专注于 Java 接口而非类，你可以最大限度地减少源代码中对类名的引用次数。这有助于随着软件的成熟，方便地从一类切换到另一类（例如，为了提升性能）。示例如下：

```
List countries = new ArrayList();
// 将国家添加到国家列表的代码。
// ...
void print(List list)
{
for (int i = 0; i < list.length(); i++)
System.out.println(list.get(i));
}
```

此示例声明并初始化了一个 `countries` 字段，用于存储字符串名称的列表。该示例还声明了一个 `print()` 方法，用于打印此列表或任何其他列表。

假设 `List` 是一个描述字符串序列的接口，而 `ArrayList` 是一个描述基于数组的 `List` 实现的类。获取一个新的 `ArrayList` 类对象并将其赋值给 `countries`。

当客户端代码与 `countries` 交互时，它将调用那些由 `List` 声明并由 `ArrayList` 实现的方法。客户端代码不会直接与 `ArrayList` 交互。因此，当需要使用不同的实现类（例如 `LinkedList`）时，客户端代码不会出错：

```
List countries = new LinkedList();
```

注意

`LinkedList` 提供了一种基于链接的*节点*组的 `List` 实现（节点是由声明了以自身命名的引用字段的类构造的对象）。例如：`class Node { String item; Node next; }`。在此示例中，`Node` 声明了一个 `next` 字段，其引用类型是声明它的类的名称。你可以通过将一个 `Node` 对象的引用赋值给 `next` 来将一个 `Node` 对象链接到另一个 `Node` 对象。

因为 `print()` 的参数类型是 `List`，所以此方法的实现无需更改。但是，如果类型是 `ArrayList`，则必须将其更改为 `LinkedList`。如果两个类都声明了自己独有的方法，则可能需要大幅更改 `print()` 的实现。

将 `List` 与 `ArrayList` 和 `LinkedList` 解耦，可以让你编写不受类实现更改影响的代码。通过使用 Java 接口，你可以避免因依赖实现类而可能产生的问题。这种解耦是使用 Java 接口的主要原因。

### 接口声明

声明接口时，需遵循类似类的语法，该语法由头部后跟主体组成。头部至少包含关键字 `interface` 后跟一个标识接口的名称（非保留标识符）。主体以左花括号字符（`{`）开始，以右花括号字符（`}`）结束。在这些分隔符之间是常量和方法头声明：

```
interface identifier
{
// 接口主体
}
```

按照惯例，接口名称的首字母大写，后续字母小写（例如，`Listable`）。如果名称由多个单词组成，则每个单词的首字母大写（例如 `ListableAndIterable`）。此命名约定称为*驼峰式大小写*。

例如，清单 8-3 展示了一个 `Collection` 接口，它描述了任何对象的分组，这被称为*集合*。

```
interface Collection
{
void add(String item);
boolean contains(String item);
int length();
}
清单 8-3
Collection.java
```

`add()` 方法将字符串添加到集合中。当集合中存在其字符串参数时，`contains()` 方法返回 true；否则返回 false。`length()` 方法返回列表中存储的字符串数量。

`Collection` 标识了一个引用类型，它描述了一个字符串集合，但并未说明该集合是如何实现的。它将实现细节留给了实现此接口的类。



### 实现接口

一个类通过在类头附加 Java 的 `implements` 关键字，后跟逗号分隔的接口名称列表，并在类中编码每个接口方法来实现接口。清单 8-4 展示了一个 `ArrayCollection` 类，它实现了清单 8-3 中的 `Collection` 接口。

```
class ArrayCollection implements Collection
{
private String[] items;
private int length;
ArrayCollection(int size)
{
items = new String[size];
length = 0;
}
@Override
public void add(String item)
{
if (length == items.length)
resize(items.length * 2);
items[length++] = item;
}
@Override
public boolean contains(String item)
{
for (int i = 0; i < length; i++)
if (item.equals(items[i]))
return true;
return false;
}
@Override
public int length()
{
return length;
}
void resize(int newSize)
{
String[] items2 = new String[newSize];
for (int i = 0; i < length; i++) // 将 items 复制到 items2
items2[i] = items[i];
items = items2;
}
}
清单 8-4
ArrayCollection.java
```

清单 8-4 展示了 `ArrayCollection`，它在一个私有的 `items` 数组中存储基于数组的字符串集合。构造函数创建此数组，其大小等于其 `size` 参数中的值。为简洁起见，我没有进行任何错误检查。我经常在各种清单中避免检查错误，但错误检查仍然很重要。

创建数组后，构造函数将 `length` 字段初始化为 `0`。此变量记录存储项的数量。虽然这种初始化并非必要，因为对象字段在构造对象时会被清零，但它可以告知开发人员（他们可能不知道这一事实）`length` 从 0 开始。

`add()` 方法将一个字符串追加到集合中。它首先通过比较 `length` 和 `items.length`（数组大小）来确保集合未满。如果它们相等，则没有更多空间来添加 `item`，并且会调用 `resize()` 将数组大小加倍。然后存储 `item`，由于数组变大，`length` 递增，并返回 true。

`contains()` 方法确定其字符串参数是否包含在集合中，该集合通过数组实现。该方法遍历数组，将每个存储的字符串与其字符串参数进行比较。如果相等，则返回 true；否则返回 false。

比较是通过 `Object` 的 `equals()` 方法进行的。此方法将对象引用参数与调用 `equals()` 的对象引用进行比较。如果匹配，`equals()` 返回 true；否则，`equals()` 返回 false。

`length()` 方法返回存储在基于数组的集合中的项数。

我已将所有三个方法声明为 `public`，因为 `interface` 的方法隐式是 public 的。

警告

当您实现接口方法时（通过覆盖接口的方法头），请记住，在接口中声明了头的所有方法都隐式声明为 `public`。如果您忘记在实现的方法声明中包含 `public`，编译器将报告错误，告知您正在尝试为已实现的方法分配更弱的访问权限。

`resize()` 方法创建一个名为 `items2` 的新字符串数组，其大小设置为传递给其 `newSize` 参数的值。然后它将 `length` 个字符串从 `items` 复制到 `items2`。复制从索引 0 开始。填充 `items2` 后，其引用被赋值给 `items`，因为 `items2` 只是一个临时占位符。

清单 8-5 展示了一个 `LinkedCollection` 类，它实现了清单 8-3 中的 `Collection` 接口。

```
class LinkedCollection implements Collection
{
private Node top, last;
private int length;
LinkedCollection()
{
top = null;
last = null;
length = 0;
}
@Override
public void add(String item)
{
Node node = new Node();
node.item = item;
node.next = null;
if (last != null)
last.next = node;
last = node;
if (top == null)
top = node;
length++;
}
@Override
public boolean contains(String item)
{
Node node = top;
for (int i = 0; i < length; i++)
if (item.equals(node.item))
return true;
else
node = node.next;
return false;
}
@Override
public int length()
{
return length;
}
}
class Node
{
String item;
Node next;
}
清单 8-5
LinkedCollection.java
```

清单 8-5 展示了 `LinkedCollection`，它将字符串集合存储在一系列链接在一起的 `Node` 对象中。它展示了一个单独的 `Node` 类，该类由一个 `item` 字段（用于记录正在存储的字符串）和一个 `next` 字段（用于记录对列表中下一个 `Node` 对象的引用）组成。

私有的 `top` 和 `last` 字段分别标识链接集合中的第一个和最后一个节点。（通常使用名为 `top` 的变量来标识链表的起始位置。）`LinkedCollection()` 构造函数将这些字段初始化为 null，表示一个空集合。

私有的 `length` 字段标识存储的节点数。此字段的存在是为了性能。如果它不存在，您将不得不扫描列表以获取长度。`LinkedCollection()` 构造函数将此字段初始化为 0，尽管它不必这样做，因为 `length` 默认为 0。

`add()` 方法首先创建一个 `Node` 对象，并用其 `item` 参数中存储的 `String` 引用填充其 `item` 字段。然后它将 `Node` 对象的 `next` 字段设置为 null，以表示集合的结束。

如果 `last` 不为 null，则集合中已存在最后一个节点。因此，`add()` 通过首先将 `last` 的 `next` 字段设置为指向 `node`（我们希望最后一个节点引用正在添加到集合中的新节点），将此新节点追加到集合中。然后它将 `last` 设置为指向 `node`，因为新节点将成为集合中的最后一个节点。

`add()` 方法现在关注 `top`。如果 `top` 为 null，则链表为空，它将 `top` 指向正在添加的节点。结果是一个单节点链表。

最后，`add()` 递增 `length` 以表示列表增加了一个节点。

`contains()` 方法确定其字符串参数是否包含在集合中，该集合通过链表实现。此方法与 `ArrayList` 的 `contains()` 方法非常相似，只是遍历的是链表而不是数组。

该方法遍历数组，将每个存储的字符串与其字符串参数进行比较。如果相等，则返回 true；否则返回 false。

`length()` 方法很简单：它返回 `length` 字段的值。

接口类型的数据值是其类实现了该接口并且其行为由该接口的方法头指定的对象。这一事实意味着，只要对象的类实现了该接口，您就可以将对象的引用赋值给接口类型的变量。

例如，如果您有一个 `Collection` 类型的变量，并将一个从实现 `Collection` 的类创建的对象引用赋值给此变量，则您只能调用 `Collection` 描述的那些方法。例如，您不能调用 `resize()`。在 `Collection` 中声明 `resize()` 没有意义，因为并非每个集合类（例如 `LinkedList`）都需要此功能，因此不会声明此方法。

清单 8-6 演示了 `Collection` 接口以及 `ArrayCollection` 和 `LinkedCollection` 类。



```
class CollectionDemo
{
public static void main(String[] args)
{
String[] planetNames =
{
"Mercury", "Venus", "Earth", "Mars",
"Jupiter", "Saturn", "Uranus", "Neptune"
};
Collection names = new ArrayCollection(5);
for (int i = 0; i < planetNames.length; i++)
names.add(planetNames[i]);
System.out.println("Contains Mercury: " +
names.contains("Mercury"));
System.out.println("Contains Pluto: " +
names.contains("Pluto"));
System.out.println();
names = new LinkedCollection();
for (int i = 0; i < planetNames.length; i++)
names.add(planetNames[i]);
System.out.println("Contains Mercury: " +
names.contains("Mercury"));
System.out.println("Contains Pluto: " +
names.contains("Pluto"));
}
}
清单 8-6
CollectionDemo.java
```

由于 `ArrayCollection` 和 `LinkedCollection` 实现了 `Collection`，因此 `ArrayCollection` 和 `LinkedCollection` 对象除了拥有其类类型之外，还具有 `Collection` 类型。因此，将每个对象的引用存储在 `Collection` 类型的变量（`names`）中是合法的，但只能调用由 `Collection` 声明的方法。

假设清单 8-3 到 8-6 位于当前目录中，通过 `javac *.java` 或 `javac CollectionDemo.java` 编译它们（Java 编译器会递归编译引用的源文件）。假设没有编译错误，执行以下命令来运行此应用程序：

```
java CollectionDemo
```

您应该会看到以下输出：

```
Contains Mercury: true
Contains Pluto: false
Contains Mercury: true
Contains Pluto: false
```

#### 实现多个接口

我之前提到过，一个类可以实现多个接口。每个接口的名称都作为 `implements` 关键字后逗号分隔的名称列表的一部分来指定。清单 8-7 展示了一个简单的示例，其中类 `Z` 实现了接口 `X` 和 `Y`。

```
interface X
{
// 适当的常量和/或方法头
}
interface Y
{
// 适当的常量和/或方法头
}
class Z implements X, Y
{
// 重写 X 和 Y 的方法头
}
清单 8-7
Z.java（演示多个接口的实现）
```

在实现多个接口时，要注意名称冲突的潜在可能性。当每个接口中出现相同的常量名称（可能具有不同的类型和/或其他信息）并在类中被访问时，就会发生这种情况。当发生名称冲突时，编译器会报告错误，如清单 8-8 所示。

```
interface X
{
int SOME_CONSTANT = 2;
void some_method();
}
interface Y
{
int SOME_CONSTANT = 3;
int some_method(int i);
}
class Z implements X, Y
{
int i = SOME_CONSTANT;
@Override
public void some_method()
{
}
@Override
public int some_method(int i)
{
return i;
}
}
清单 8-8
Z.java（演示常量冲突）
```

类 `Z` 继承了名为 `SOME_CONSTANT` 的两个不同常量，它们被初始化为两个不同的值。Java 编译器无法确定 `Z` 应该继承哪个常量（即使每个常量被赋予相同的值，也会出现同样的问题），并报告以下错误消息：

```
Z.java:17: error: reference to SOME_CONSTANT is ambiguous
int i = SOME_CONSTANT;
^
both variable SOME_CONSTANT in X and variable SOME_CONSTANT in Y match
1 error
```

### 扩展接口

实现接口的类揭示了*接口继承*。该类继承了接口的常量和方法头，并对其进行重写。例如，`ArrayCollection` 和 `LinkedCollection` 都继承了 `Collection` 的 `add()`、`contains()` 和 `length()` 方法头。

当一个接口扩展另一个接口时，也演示了接口继承。正如子类可以通过保留字 `extends` 扩展超类一样，您可以使用此保留字让子接口扩展超接口。清单 8-9 对此进行了演示。

```
interface List extends Collection
{
String get(int i);
}
清单 8-9
List.java
```

`List` 接口扩展了 `Collection`，继承了它的方法头。它还引入了一个 `get()` 方法，该方法返回列表中第 `i` 个位置的字符串，如果 `i` 小于 0 或大于等于 `list` 的长度，则返回 null。

清单 8-10 展示了一个实现 `List` 接口的 `ArrayList` 类。

```
class ArrayList implements List
{
private String[] items;
private int length;
ArrayList(int size)
{
items = new String[size];
length = 0;
}
@Override
public void add(String item)
{
if (length == items.length)
resize(items.length * 2);
items[length++] = item;
}
@Override
public boolean contains(String item)
{
for (int i = 0; i < length; i++)
if (items[i].equals(item))
return true;
return false;
}
@Override
public int length()
{
return length;
}
@Override
public String get(int index)
{
if (index < 0 || index >= length)
return null;
return items[index];
}
void resize(int newSize)
{
String[] items2 = new String[newSize];
for (int i = 0; i < length; i++) // 将 items 复制到 items2
items2[i] = items[i];
items = items2;
}
}
清单 8-10
ArrayList.java
```

我之前描述了 `List` 从 `Collection` 继承的 `add()`、`contains()` 和 `length()` 方法。我还描述了 `resize()` 方法。

`List` 的 `get()` 方法验证传递给其 `index` 参数的参数：该参数必须大于等于 0 且小于存储列表字符串的数组的长度。如果不满足此条件，此方法返回 null；否则，它返回该索引处的字符串。

清单 8-11 展示了一个实现 `List` 接口的 `LinkedList` 类。

```
class LinkedList implements List
{
private Node top, last;
private int length;
LinkedList()
{
top = null;
last = null;
length = 0;
}
@Override
public void add(String item)
{
Node node = new Node();
node.item = item;
node.next = null;
if (last != null)
last.next = node;
last = node;
if (top == null)
top = node;
length++;
}
@Override
public boolean contains(String item)
{
Node node = top;
for (int i = 0; i < length; i++)
{
if (node.item.equals(item))
return true;
node = node.next;
}
return false;
}
@Override
public int length()
{
return length;
}
@Override
public String get(int index)
{
if (index < 0 || index >= length)
return null;
Node node = top;
int count = 0;
while (true)
{
if (count++ == index)
break;
node = node.next;
}
return node.item;
}
}
class Node
{
String item;
Node next;
}
清单 8-11
LinkedList.java
```

与 `ArrayList` 一样，我之前描述了 `List` 从 `Collection` 继承的 `add()`、`contains()` 和 `length()` 方法。

`List` 的 `get()` 方法首先验证传递给其 `index` 参数的参数：该参数必须大于等于 0 且小于存储列表字符串的数组的长度。如果不满足此条件，此方法返回 null；否则，它返回该索引处的字符串。

然后，`get()` 方法遍历链表，直到找到指定索引值处的节点，并返回该节点的项。

遍历逻辑可能看起来难以理解，但实际上并不难。它首先将 `top` 的值赋给局部变量 `node`，然后将局部变量 `count` 初始化为 0。这些变量控制着遍历过程。



导航逻辑现在进入了一个看似无限的 `while` 循环。它首先将 `count` 与 `index` 进行比较，并递增 `count` 以记录已遍历的节点数量。当 `count` 的值等于 `index` 中的值时，表示恰好遍历了 `index` 个节点，循环终止；否则，通过将 `node.next` 的引用赋值给 `node`，使 `node` 指向下一个节点。

该循环总会终止，因为 `get()` 方法首先会验证传递给 `index` 的参数。当循环终止时，`get()` 方法返回 `node.item` 的字符串引用。

最后，清单 8-12 展示了一个 `ListDemo` 类，该类演示了 `Collection`、`List`、`ArrayList` 和 `LinkedList`。

```
class ListDemo
{
public static void main(String[] args)
{
String[] planetNames =
{
"Mercury", "Venus", "Earth", "Mars",
"Jupiter", "Saturn", "Uranus", "Neptune"
};
List names = new ArrayList(5);
for (int i = 0; i < planetNames.length; i++)
names.add(planetNames[i]);
print(names);
names = new LinkedList();
for (int i = 0; i < planetNames.length; i++)
names.add(planetNames[i]);
print(names);
}
static void addPlanetNames(List names)
{
names.add("Mercury");
names.add("Venus");
names.add("Earth");
names.add("Mars");
names.add("Jupiter");
names.add("Saturn");
names.add("Uranus");
names.add("Neptune");
}
static void print(List list)
{
for (int i = 0; i < list.length(); i++)
System.out.println(list.get(i));
System.out.println();
}
}
清单 8-12
ListDemo.java
```

假设清单 8-9 到 8-12 位于当前目录，通过 `javac *.java` 或 `javac ListDemo.java` 编译它们（Java 编译器会递归编译引用的源文件）。假设没有编译错误，执行以下命令来运行此应用程序：

```
java ListDemo
```

你应该会看到以下输出：

```
Mercury
Venus
Earth
Mars
Jupiter
Saturn
Uranus
Neptune
Mercury
Venus
Earth
Mars
Jupiter
Saturn
Uranus
Neptune
```

#### 扩展多个接口

与实现接口类似，你可以扩展多个接口。每个接口的名称都作为 `extends` 关键字后逗号分隔的名称列表的一部分来指定。清单 8-13 展示了一个简单示例，其中接口 `Z` 扩展了接口 `X` 和 `Y`。

```
interface X
{
// 适当的常量和/或方法头
}
interface Y
{
// 适当的常量和/或方法头
}
interface Z extends X, Y
{
// 适当的常量和/或方法头
}
清单 8-13
Z.java 演示多接口扩展
```

在扩展多个接口时，要注意名称冲突的潜在可能性。当同一个常量名称出现在每个超接口中（可能具有不同的类型和/或其他信息），并在子接口中被访问时，就会发生这种情况。当发生名称冲突时，编译器会报告一个错误，清单 8-14 演示了这一点。

```
interface X
{
int SOME_CONSTANT = 2;
void some_method();
}
interface Y
{
int SOME_CONSTANT = 3;
int some_method(int x);
}
interface Z extends X, Y
{
int SOME_CONSTANT2 = SOME_CONSTANT;
}
清单 8-14
Z.java（演示冲突的常量）
```

这里，接口 `Z` 继承了名为 `SOME_CONSTANT` 的两个不同常量，它们被初始化为两个不同的值。Java 编译器无法确定 `Z` 应该继承哪个常量（如果每个常量被赋予相同的值，也会出现同样的问题），并报告以下错误信息：

```
Z.java:15: 错误: 对 SOME_CONSTANT 的引用不明确
int SOME_CONSTANT2 = SOME_CONSTANT;
^
X 中的变量 SOME_CONSTANT 和 Y 中的变量 SOME_CONSTANT 都匹配
1 个错误
```

## 向下转型与 RTTI

通过向上转型在类层次结构中向上移动，意味着会失去对子类型特性的访问权限。例如，将一个 `Truck` 对象赋值给 `Vehicle` 变量 `v`，意味着你不能使用 `v` 来调用 `Truck` 的 `isExtendedCab()` 方法。然而，通过执行像 `Truck t = (Truck) v;` 这样的显式转型操作，可以再次访问 `Truck` 的 `isExtendedCab()` 方法。

这种赋值被称为*向下转型*，因为你是在沿着继承层次结构从超类型向下转型到子类型（从 `Vehicle` 超类到 `Truck` 子类）。虽然向上转型总是安全的（超类的接口是子类接口的一个子集），但向下转型并不总是安全的。清单 8-15 展示了如果错误地使用向下转型可能会带来什么样的麻烦。

```
class Vehicle
{
}
class Truck extends Vehicle
{
private boolean isExtendedCab;
boolean isExtendedCab()
{
return isExtendedCab;
}
}
public class BadDowncast
{
public static void main(String[] args)
{
Vehicle v = new Vehicle();
Truck t = (Truck) v;
System.out.println(t.isExtendedCab());
}
}
清单 8-15
BadDowncast.java（演示向下转型的问题）
```

清单 8-15 展示了一个由 `Vehicle` 和扩展了 `Vehicle` 的 `Truck` 组成的类层次结构。此外，`Truck` 声明了 `isExtendedCab()`。第三个名为 `BadDowncast` 的类提供了一个 `main()` 方法，该方法首先实例化 `Vehicle`。

然后，`main()` 方法尝试将此对象向下转型为 `Truck`，并将结果赋值给变量 `t`。编译器不会报错，因为在同一类型层次结构中从超类向下转型到子类是合法的。

如果允许此赋值，应用程序在尝试执行 `t.isExtendedCab();` 时会崩溃，因为 Java 虚拟机（JVM）将尝试调用一个不存在的方法：`Vehicle` 没有声明 `isExtendedCab()`。幸运的是，JVM 在执行转型操作之前会验证转型是否合法。检测到 `Vehicle` 没有声明 `isExtendedCab()` 后，它会抛出一个 `ClassCastException` 对象。（我将在第 11 章讨论异常。）

按如下方式编译清单 8-15：

```
javac BadDowncast.java
```

按如下方式运行应用程序：

```
java BadDowncast
```

你应该会看到以下输出：

```
Exception in thread "main" java.lang.ClassCastException: class Vehicle cannot be cast to class Truck (Vehicle and Truck are in unnamed module of loader 'app')
at BadDowncast.main(BadDowncast.java:19)
```



### 运行时类型识别

清单 8-15 中 JVM 的类型转换验证展示了*运行时类型识别*（简称 RTTI）。类型转换验证通过检查类型转换操作符的操作数类型，来判断该转换是否应被允许，从而执行 RTTI。在此场景中，该转换不应被允许。

RTTI 的另一种形式涉及 `instanceof` 运算符。该运算符检查左操作数是否为右操作数的实例，如果是则返回 true。以下示例将 `instanceof` 引入清单 8-15，以防止 `ClassCastException`：

```
if (v instanceof Truck)
{
Truck t = (Truck) v;
System.out.println(t.isExtendedCab());
}
```

`instanceof` 运算符检测到变量 `v` 的实例并非由 `Truck` 创建，并返回 false 以表明这一事实。因此，执行非法转换的代码将不会运行。

由于子类型是超类型的一种，当左操作数是右操作数超类型的子类型实例或超类型实例时，`instanceof` 将返回 true。以下示例进行了演示：

```
Vehicle v = new Truck();
Truck t = new Truck();
System.out.println(t instanceof Vehicle); // 输出：true
System.out.println(v instanceof Vehicle); // 输出：true
```

此示例假设了清单 8-15 中所示的类结构，并实例化了 `Vehicle` 和 `Truck`。第一个 `System.out.println()` 方法调用输出 `true`，因为 `t` 的引用标识了 `Vehicle` 子类的一个实例；第二个 `System.out.println()` 方法调用输出 `true`，因为 `v` 的引用标识了 `Vehicle` 的一个实例。

警告

过度使用 `instanceof` 可能表明软件设计不佳。例如，假设你决定使用多个 `instanceof` 表达式来判断一个 `vehicle` 对象的类型是 `Car`、`Truck` 还是其他 `Vehicle` 子类型。当你引入一个新的 `Vehicle` 子类型时，可能会忘记添加一个 `instanceof` 测试来检查 `vehicle` 是否为该类型的实例，这会导致一个 bug。请尽量减少对 `instanceof` 在特殊情况下的依赖。大多数情况下，使用子类型多态性会是更好的选择。

## 其他主题

在本节中，我将向你介绍与本章内容相关的一对额外主题。首先，你将探索协变返回类型，它利用子类型多态性为你带来好处。接下来，你将了解基于接口的静态方法。

### 协变返回类型

当你重写一个方法，并且允许重写方法的返回类型是被重写方法返回类型的子类型时，就产生了*协变返回类型*。当方法在子类中被重写时，方法的协变返回类型可以被替换为更“窄”（子类）的类型。

我创建了一个小型应用程序来演示这一语言特性。请查看清单 8-16 的源代码。

```
// 观察协变返回类型
class ParentReturnType
{
@Override
public String toString()
{
return "父类返回类型";
}
}
class ChildReturnType extends ParentReturnType
{
@Override
public String toString()
{
return "子类返回类型";
}
}
class ParentClass
{
ParentReturnType createReturnType()
{
return new ParentReturnType();
}
}
class ChildClass extends ParentClass
{
// ChildClass 重写了 ParentClass 的 createReturnType() 方法。
// 注意 ChildClass 的 createReturnType() 方法的返回类型
// 已从 ParentReturnType 变为 ChildReturnType。
// 如果没有协变返回类型，返回类型必须是 ParentReturnType。
@Override
ChildReturnType createReturnType()
{
return new ChildReturnType();
}
}
class OCRT
{
public static void main(String[] args)
{
ParentReturnType prt = new ParentClass().createReturnType();
System.out.println(prt);
// 以下语句实例化 ChildClass 并调用其
// createReturnType() 方法。createReturnType() 返回的
// ChildClass 引用被赋值给 crt。如果没有协变返回类型，
// 你必须通过 (ChildClass) 转换运算符显式地将
// new 运算符返回的对象转换为 ChildReturnType。
ChildReturnType crt = new ChildClass().createReturnType();
System.out.println(crt);
}
}
清单 8-16
OCRT.java
```

清单 8-16 声明了 `ParentReturnType` 和 `ParentClass` 超类，以及 `ChildReturnType` 和 `ChildClass` 子类。`ParentClass` 和 `ChildClass` 各自声明了一个 `createReturnType()` 方法。`ParentClass` 的方法将其返回类型设置为 `ParentReturnType`，而 `ChildClass` 的重写方法将其返回类型设置为 `ChildReturnType`，即 `ParentReturnType` 的子类。

协变返回类型极大地减少了向上转型和向下转型。例如，`ChildClass` 的 `createReturnType()` 方法不需要将其 `ChildReturnType` 实例向上转型为其 `ChildReturnType` 返回类型。此外，在赋值给变量 `crt` 时，此实例也不需要向下转型为 `ChildReturnType`。

按如下方式编译清单 8-16：

```
javac OCRT.java
```

按如下方式运行生成的应用程序：

```
java OCRT
```

你应该会看到以下输出：

```
父类返回类型
子类返回类型
```

如果没有协变返回类型，最终会得到清单 8-17。

```
// 观察协变返回类型
class ParentReturnType
{
@Override
public String toString()
{
return "父类返回类型";
}
}
class ChildReturnType extends ParentReturnType
{
@Override
public String toString()
{
return "子类返回类型";
}
}
class ParentClass
{
ParentReturnType createReturnType()
{
return new ParentReturnType();
}
}
class ChildClass extends ParentClass
{
@Override
ParentReturnType createReturnType()
{
return new ChildReturnType();
}
}
class OCRT
{
public static void main(String[] args)
{
ParentReturnType prt = new ParentClass().createReturnType();
System.out.println(prt);
ChildReturnType crt =
(ChildReturnType) new ChildClass().createReturnType();
System.out.println(crt);
}
}
清单 8-17
OCRT.java（版本 2）
```

清单 8-17 在 `ChildClass` 的 `createReturnType()` 方法中，将 `ChildReturnType` 向上转型为 `ParentReturnType`。此外，在 `main()` 方法中，它在赋值给 `crt` 之前，使用了必需的 `(ChildReturnType)` 转换运算符，将 `ParentReturnType` 向下转型为 `ChildReturnType`。



### 基于接口的静态方法

JDK 8 为程序员引入了基于接口的静态方法。在接口中声明静态方法，可以让你将静态方法与接口关联，而不是与工具类关联。这样做能使源代码更具可读性，并确保二进制兼容性不被破坏。

为了理解基于接口的静态方法的实用性，考虑一个 `Renderable` 接口，它包含一个 `void render(int color)` 方法，其 `color` 参数包含一个整数颜色代码。由于用红、绿、蓝分量来表示这种颜色很方便，假设你添加一个 `rgb()` 静态方法，将这些分量转换为一个 `int` 值。请查看清单 8-18。

```
interface Renderable
{
public void render(int color);
public static int rgb(int r, int g, int b)
{
return r << 16 | g << 8 | b;
}
}
清单 8-18
Renderable.java
```

接下来，你声明一个 `Circle` 类，该类通过基于整数的圆心坐标和半径来描述一个圆，并实现 `Renderable` 来渲染一个圆。清单 8-19 展示了 `Circle` 的源代码。

```
class Circle implements Renderable
{
private int x, y, r;
Circle(int x, int y, int r)
{
this.x = x;
this.y = y;
this.r = r;
}
@Override
public void render(int color)
{
System.out.println("Rendering circle(" + x + ", " + y + ", " + r + ") in color " + color);
}
}
清单 8-19
Circle.java
```

最后，你声明一个 `UseRenderable` 应用程序类，其源代码如清单 8-20 所示。

```
public class UseRenderable
{
public static void main(String[] args)
{
Circle circle = new Circle(20, 30, 14);
circle.render(Renderable.rgb(0x80, 0x60, 0x40));
}
}
清单 8-20
UseRenderable.java
```

`UseRenderable` 的 `main()` 方法实例化 `Circle`，然后调用该对象的 `render()` 方法来渲染圆。请注意，`rgb()` 方法调用前加上了此静态方法所属的 `Renderable` 接口。你不能用实现该接口的类作为前缀（例如 `Circle.rgb(0x80, 0x60, 0x40)`），因为该静态方法属于 `Renderable`。

按如下方式编译清单 8-18、8-19 和 8-20：

```
javac UseRenderable.java
```

`javac` 编译器工具会递归地编译其在编译命令行参数指定的源文件时遇到的类及接口所对应的源文件。

按如下方式运行 `UseRenderable.class`：

```
java UseRenderable
```

你应该会看到以下输出：

```
Rendering circle(20, 30, 14) in color 8413248
```

## 下一步是什么？

Java 提供了不常见的语言特性，用于在其他类甚至块中声明类。此外，你还可以声明匿名类。第 9 章将向你介绍这些奇特的特性。

