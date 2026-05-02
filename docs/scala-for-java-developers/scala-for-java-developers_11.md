# 9. 类与对象

在本章中，我们将探讨以下内容：

*   如何在类体内（而非类定义行）定义字段，以及这如何影响生成的方法。
*   如何创建额外的构造函数。
*   使用 `object` 关键字定义的 Scala 单例对象。
*   伴生对象，一种特殊的单例对象。



## 无构造函数参数的类

首先，我们来看看如何在类中创建字段，而不在类定义行中定义它们。如果你在 Scala 中创建一个类，且类定义中没有定义任何字段，就像这样：

```
// scala
class Counter
```

……Scala 编译器仍然会生成一个无参的主构造函数，这与 Java 的默认构造函数非常相似。因此，对应的 Java 代码看起来是这样的：

```
// java
public class Counter {
public Counter() {
}
}
```

在 Java 中，你可能会初始化一个变量并创建一些方法。

```
// java
public class Counter {
private int count = 0;
public Counter() {
}
public void increment() {
count++;
}
public int getCount() {
return count;
}
}
```

在 Scala 中你也可以做同样的事情。

```
// scala
class Counter {
private var count = 0
def increment() {
count += 1
}
def getCount = count
}
```

在主构造函数内部（即不在类定义中，而是在紧随其后的类体中），`val` 和 `var` 关键字可能会生成 getter 和 setter。它们的存在会影响字节码，如表 9-1 所示。

表 9-1

getter 和 setter 的存在如何影响字节码

| 在主构造函数中声明 | `val x` | `var x` | `x` | `private val x` | `private var x` |
| --- | --- | --- | --- | --- | --- |
| Getter (`x()`) | 是 (`public`) | 是 (`public`) | 无 | 是 (`private`) | 是 (`private`) |
| Setter (`x_=(y)`) | 否 | 是 (`public`) | 无 | 否 | 是 (`private`) |

如你所见，这与表 8-1 是一致的。对于 `val` 和 `var` 类型，默认会生成 getter，并且都是公有的。在字段声明中添加 `private` 会使生成的字段变为私有，而 setter 仅为 `var` 生成（同样，默认是公有的）。

## 附加构造函数

让我们创建一个 `Customer` 类的替代 Java 版本，这次带有附加构造函数。

```
// java
public class Customer {
private final String fullname;
public Customer(String forename, String initial, String surname) {
this.fullname =
String.format("%s %s. %s", forename, initial, surname);
}
public Customer(String forename, String surname) {
this(forename, "", surname);
}
}
```

我们为客户的中间名首字母设置了默认值，并允许客户端选择是否提供它。

我们可能应该整理一下主构造函数，以反映变量可能以空字符串形式传入的情况。我们将添加一个 if 条件，并根据结果格式化字符串。

```
// java
public class Customer {
private final String fullname;
public Customer(String forename, String initial, String surname) {
if (initial != null && !initial.isEmpty())
this.fullname =
String.format("%s %s. %s", forename, initial, surname);
else
this.fullname = String.format("%s %s", forename, surname);
}
public Customer(String forename, String surname) {
this(forename, "", surname);
}
public static void main(String... args) {
System.out.println(new Customer("Bob", "J", "Smith").fullname);
System.out.println(new Customer("Bob", "Smith").fullname);
}
}
```

在 Scala 中创建附加构造函数或辅助构造函数，只需创建名为 `this` 的方法即可。一个限制是，每个辅助构造函数必须在第一行使用 `this` 调用另一个构造函数。这样，构造函数将始终被链式调用，直至最顶层。

Scala 有主构造函数的概念；它就是类体中的代码。从类定义中传入的任何参数都可以被它使用。如果你不编写任何辅助构造函数，该类仍然会有一个构造函数；它就是隐式的主构造函数。

```
// scala
class Customer(forename: String, initial: String, surname: String) {
// 主构造函数
}
```

因此，如果我们在主构造函数中创建一个字段并为其赋值，

```
// scala
class Customer(forename: String, initial: String, surname: String) {
val fullname = String.format("%s %s. %s", forename, initial, surname)
}
```

……它等同于以下 Java 代码：

```
// java
public class Customer {
private final String fullname;
public Customer(String forename, String initial, String surname) {
this.fullname =
String.format("%s %s. %s", forename, initial, surname);
}
}
```

如果我们能为 Scala 版本添加另一个辅助构造函数，就可以引用 `this` 来链式调用主构造函数。

```
// scala
class Customer(forename: String, initial: String, surname: String) {
val fullname = String.format("%s %s. %s", forename, initial, surname)
def this(forename: String, surname: String) {
this(forename, "", surname)
}
}
```

### 使用默认值

Scala 语言支持在方法签名中使用默认值，因此我们可以仅通过在类定义中使用参数来编写，从而避免使用额外的构造函数。我们只需将 `initial` 的默认值设为空字符串即可。为了使实现更好地处理空字符串，我们可以像之前的 Java 版本一样，在主构造函数中添加一些逻辑。

```
class Customer(forename: String, initial: String = "", surname: String) {
val fullname = if (initial != null && !initial.isEmpty)
forename + " " + initial + "." + surname
else
forename + " " + surname
}
```

在调用时，当我们没有按照声明顺序提供所有参数时，需要为默认值参数指定名称。例如：

```
new Customer("Bob", "J", "Smith")
```

`"Bob", "J", "Smith"` 是可以的，但如果我们跳过 `initial` 变量，则需要像这样为 `surname` 变量指定名称：

```
new Customer("Bob", surname = "Smith")
```



## 单例对象

在 Java 中，你可以使用单例模式来强制一个类只有一个实例。Scala 将此思想作为语言本身的一个特性：除了类之外，你还可以定义（单例）对象。

缺点在于，当我们在 Scala 中谈论“对象”时，这个词被重载了。我们可能指的是一个类的实例（例如，一个 `new ShoppingCart()`，可以有多个），也可能指的是一个类的唯一实例；也就是单例对象。

在 Java 中，单例的一个典型用例是，我们需要在整个应用程序中使用一个单一的日志记录器实例。

```
// java
Logger.getLogger().log(INFO, "Everything is fine.");
```

我们可能会像这样实现单例：

```
// java
public final class Logger {
private static final Logger INSTANCE = new Logger();
private Logger() { }
public static Logger getLogger() {
return INSTANCE;
}
public void log(Level level, String string) {
System.out.printf("%s %s%n", level, string);
}
}
```

我们创建了一个 `Logger` 类，以及它的一个静态实例。我们通过使用私有构造器来阻止其他人创建实例。然后我们创建一个访问器来获取这个静态实例，最后给它一个基本的日志方法。我们会像这样调用它：

```
// java
Logger.getLogger().log(INFO, "Singleton loggers say YEAH!");
```

在 Java 中实现相同功能的一种更简洁的方法是使用枚举。

```
// java
public enum LoggerEnum {
LOGGER;
public void log(Level level, String string) {
System.out.printf("%s %s%n", level, string);
}
}
```

我们不需要使用访问器方法；Java 确保使用单个实例，我们会像这样调用它：

```
// java
LOGGER.log(INFO, "An alternative example using an enum");
```

无论哪种方式，它们都阻止客户端创建类的新实例，并提供一个单一的、全局的实例供使用。

Scala 中的等价写法如下：

```
// scala
object Logger {
def log(level: Level, string: String) {
printf("%s %s%n", level, string)
}
}
```

这里需要注意的一点是，单例实例由 `object` 关键字表示，而不是 `class`。所以，我们是在说“定义一个名为 `Logger` 的单一对象”，而不是“定义一个类”。

在底层，Scala 创建的基本上与我们单例模式示例中的 Java 代码相同。当我们反编译它时可以看到这一点。

```
1   // 从 scala 反编译为 java
2   public final class Logger$ {
3       public static final Logger$ MODULE$;

5       public static {
6           new scala.demo.singleton.Logger$();
7       }

9       public void log(Level level, String string) {
10           Predef..MODULE$.printf("%s %s%n", (Seq)Predef..MODULE$
11           .genericWrapArray((Object)new Object[]{level, string}));
12       }

14       private Logger$() {
15           Logger$.MODULE$ = this;
16       }
17   }
```

`log` 方法中有一些奇怪的地方，但那是反编译器在努力反编译字节码，以及 Scala 处理事务的一般方式。不过本质上，它是等价的；有一个像 Java 版本一样的私有构造器，以及一个单一的静态对象实例。类本身甚至是 `final` 的。

无需 `new` 一个新的 `Logger`；`Logger` 已经是一个对象，所以我们可以直接引用它。事实上，即使你想 `new` 一个也做不到，因为生成的构造器是私有的。

Scala 对象上的方法等价于 Java 中的静态方法。一个例子是 Java 类上可以被运行时执行的静态 `main` 方法。你在 Scala 单例对象中创建 `main` 方法，而不是在类中，以复制这种行为。

## 伴生对象

你可以在 Scala 中组合对象和类。当你在同一个源文件中创建一个与类同名的对象时，该对象被称为伴生对象。

Scala 没有 `static` 关键字，但单例对象的成员实际上是静态的。记住，Scala 单例对象就是单例。因此，它包含的任何成员都将被所有使用该对象的客户端重用；它们就像静态成员一样是全局可用的。

你在需要混合使用 Java 中的静态和非静态成员的地方使用伴生对象。

Java 版本的 `Customer` 拥有客户姓名和地址的字段，以及一个用于唯一标识客户的 ID。

```
// java
public class Customer {
private final String name;
private final String address;
private Integer id;
public Customer(String name, String address) {
this.name = name;
this.address = address;
}
}
```

现在，我们可能想要创建一个辅助方法来生成序列中的下一个 ID。为了全局地做到这一点，我们创建一个静态字段来保存 ID 的值，以及一个返回并递增它的方法。然后，我们可以在构造新实例时调用该方法，将其 ID 分配给刚刚递增的全局 ID。

```
// java
public class Customer {
private static Integer lastId;
private final String name;
private final String address;
private Integer id;
public Customer(String name, String address) {
this.name = name;
this.address = address;
this.id = Customer.nextId();
}
private static Integer nextId() {
return lastId++;
}
}
```

它是静态的，因为我们希望在所有实例之间共享其实现，以便为每个实例创建全局唯一的 ID。

在 Scala 中，我们将静态成员与非静态成员分开，将静态成员放在单例对象中，其余的放在类中。这个单例对象就是 `Customer` 的伴生对象。

我们创建包含两个必需字段的类，并在单例对象中创建 `nextId` 方法。接下来，我们创建一个私有的 `var` 来保存当前值，将其赋值为零，以便 Scala 可以推断其类型为 `Integer`。在这里添加 `val` 意味着不会生成 setter，添加 `private` 修饰符意味着生成的 getter 将是私有的。最后，我们在 `nextId` 方法中实现递增，并从主构造器中调用它。

```
// scala
class Customer(val name: String, val address: String) {
private val id = Customer.nextId()
}
object Customer {
private var lastId = 0
private def nextId(): Integer = {
lastId += 1
lastId
}
}
```

这个单例对象是伴生对象，因为它与它的类同名，并且位于同一个源文件中。这意味着两者有特殊的关系，可以访问彼此的私有成员。这就是为什么 `Customer` 对象可以将 `nextId` 方法定义为私有的，但 `Customer` 类仍然可以访问它。

如果你给对象起一个不同的名字，就不会有这种特殊关系，也就无法调用该方法。例如，下面的 `CustomerX` 对象不是 `Customer` 的伴生对象，因此无法看到私有的 `nextId` 方法。

```
// scala
class Customer(val name: String, val address: String) {
private val id = CustomerX.nextId()         // 编译失败
}
object CustomerX {
private var lastId = 0
private def nextId(): Integer = {
lastId += 1
lastId
}
}
```


好的，作为一名高级文档工程师和翻译员，我将严格遵循您提供的注意事项和示例，将给定的英文文本翻译成中文。


### 伴生对象的其他用途

当方法不依赖于类中的任何字段时，你可以更准确地将它们视为函数。函数通常属于单例对象而非类，因此，使用伴生对象的一个场景是，当你想要区分函数和方法，但又希望将相关函数保留在其关联类的附近时。

使用伴生对象的另一个原因是用于工厂模式方法——即创建伴生类实例的方法。例如，你可能想创建一个工厂方法，它能以更简洁的方式创建类的实例。如果我们想为 `Customer` 创建一个工厂，可以这样做：

```
// scala
class Customer(val name: String, val address: String) {
val id = Customer.nextId()
}
object Customer {
def apply(name: String, address: String) = new Customer(name, address)
def nextId() = 1
}
```

`apply` 方法为类或对象提供了一种简写形式。它有点像类的默认方法，因此，如果你没有直接调用实例上的方法，而是匹配了 `apply` 方法的参数，它就会为你调用该方法。例如，你可以这样调用：

```
Customer.apply("Bob Fossil", "1 London Road")
```

……或者你可以省略 `apply`，Scala 会寻找一个与你参数匹配的 `apply` 方法。这两种写法是等价的。

```
Customer("Bob Fossil", "1 London Road")
```

你仍然可以使用主构造函数和 `new` 关键字来构造一个类，但将伴生类的 `apply` 方法实现为工厂，意味着如果你需要创建大量对象，代码可以更简洁。

你甚至可以通过将主构造函数设为私有，来强制客户端使用你的工厂方法而非构造函数。

```
class Customer private (val name: String, val address: String) {
val id = Customer.nextId()
}
```

Java 中的对应做法是使用一个静态工厂方法——例如 `createCustomer`——和一个私有构造函数，以确保所有人都被迫使用工厂方法。

```
// java
public class Customer {
private static Integer lastId;
private final String name;
private final String address;
private Integer id;
public static Customer createCustomer(String name, String address) {
return new Customer(name, address);
}
private Customer(String name, String address) {
this.name = name;
this.address = addres s;
this.id = Customer.nextId();
}
private static Integer nextId() {
return lastId++;
}
}
```

