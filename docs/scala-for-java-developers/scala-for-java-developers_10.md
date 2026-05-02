# 8. 类与字段

在本章中，我们将了解以下内容：

1.  创建类。
2.  Scala 如何简化定义字段的过程。
3.  当 Scala 为你自动生成方法时，幕后发生了什么。

## 创建类

在 Java 中创建一个类意味着编写如下代码：

```
// java
public class Customer {
}
```

为客户添加姓名和地址是合理的。因此，将它们添加为字段并通过构造函数进行初始化，会得到如下代码：

```
// java
public class Customer {
private final String name;
private final String address;
public Customer(String name, String address) {
this.name = name;
this.address = address;
}
}
```

我们可以使用 `new` 关键字实例化一个对象，并创建一个名为 Eric 的新客户，如下所示：

```
Customer eric = new Customer("Eric", "29 Acacia Road"); // java
```

在 Scala 中，语法要简洁得多；我们可以将类和构造函数合并到一行中。

```
class Customer(val name: String, val address: String)   // scala
```

我们以相同的方式使用 new 创建实例，如下所示：

```
val eric = new Customer("Eric", "29 Acacia Road")       // scala
```

Scala 版本没有将字段定义为类内部的成员，而是将变量声明为类定义的一部分，这被称为主构造函数。在一行代码中，我们声明了 `Customer` 类，并且实际上声明了一个带有两个参数的构造函数。



## 派生 Setter 和 Getter

类定义中的 `val` 关键字会告知编译器将参数视为字段，并为其创建字段和访问器方法。

我们可以通过获取生成的类文件并将其反编译为 Java 来证明这一点。这种往返转换是探索 Scala 在幕后实际生成内容的好方法。这里我使用了 Lee Benfield 编写的优秀 CFR 反编译器¹，但你也可以使用 Java 自带的 `javap` 程序来获取基本信息。

要对 Scala 为 `Customer` 生成的类文件运行反编译器，可以执行类似以下的操作：

```
java -jar cfr_0_122.jar target/scala-2.12/classes/s4j/scala/chapter09/
Customer.class
```

它会生成以下内容：

```
1   // 从 Scala 反编译为 Java
2   public class Customer {
3       private final String name;
4       private final String address;

6       public String name() {
7           return this.name;
8       }

10       public String address() {
11           return this.address;
12       }

14       public Customer(String name, String address) {
15           this.name = name;
16           this.address = address;
17       }
18   }
```

需要注意的重要一点是，Scala 在第 6 行和第 10 行生成了访问器方法，并在第 14 行生成了一个构造函数。这些访问器没有使用 Java 的 getter 约定，但我们得到了与 `getName` 和 `getAddress` 等效的方法。

你可能还想定义字段，但不通过构造函数来设置它们。例如，在 Java 中，我们可能想为客户添加一个 `id`，稍后通过 setter 方法进行设置。这是像 Hibernate 这类工具在从数据库填充对象时常用的模式。

```
// java
public class Customer {
private final String name;
private final String address;
private String id;
public Customer(String name, String address) {
this.name = name;
this.address = address;
}
public void setId(String id) {
this.id = id;
}
}
```

在 Scala 中，你几乎可以做同样的事情。

```
// scala
class Customer(val name: String, val address: String) {
var id = ""
}
```

你定义一个字段（这里是一个 `var`），Scala 就会神奇地为你创建一个 setter 方法。它创建的 setter 方法名为 `id_=`，而不是通常的 `setId`。如果我们通过反编译器进行往返转换，会看到以下内容：

```
1   // 从 Scala 反编译为 Java
2   public class Customer {
3       private final String name;
4       private final String address;
5       private String id;

7       public String name() {
8           return this.name;
9       }
10       public String address() {
11           return this.address;
12       }
13       public String id() {                      // 注意它是 public 的
14           return this.id;
15       }
16       public void id_$eq(String x$1) {          // 注意它是 public 的
17           this.id = x$1;
18       }
19       public Customer(String name, String address) {
20           this.name = name;
21           this.address = address;
22           this.id = null;
23       }
24   }
```

注意，它在第 16 行创建了一个名为 `id_$eq` 的方法，而不是 `id_=`；这是因为等号符号在 JVM 的方法名中是不允许的，所以 Scala 对其进行了转义，并在需要时进行转换。你可以像这样直接调用 setter 方法：

```
new Customer("Bob", "10 Downing Street").id_=("000001")
```

不过，Scala 提供了一种简写方式；你可以直接使用常规赋值，Scala 会在底层调用自动生成的 `id_$eq` setter 方法：

```
new Customer("Bob", "10 Downing Street").id = "000001"
```

如果字段前没有访问修饰符，则该字段变为 public。因此，除了能够调用自动生成的 setter 之外，客户端也可以直接操作该字段，这可能会破坏封装性。我们希望能够将字段设为 private，并只允许在 `Customer` 类内部进行更新。

为此，只需在字段前使用 `private` 关键字即可。

```
class Customer(val name: String, val address: String) {
private var id = ""
}
```

反编译器显示，setter 和 getter 方法现在都是 private 的。

```
1   // 从 Scala 反编译为 Java
2   public class Customer {
3       private final String name;
4       private final String address;
5       private String id;

7       public String name() {
8           return this.name;
9       }

11       public String address() {
12           return this.address;
13       }

15       private String id() {                 // 现在是 private 的
16           return this.id;
17       }

19       private void id_$eq(String x$1) {     // 现在是 private 的
20           this.id = x$1;
21       }

23       public Customer(String name, String address) {
24           this.name = name;
25           this.address = address;
26           this.id = "";
27       }
28   }
```



## 重定义 Setter 与 Getter

使用 setter 设置值的优势在于，我们可以通过该方法来维护不变式或执行特殊处理。在 Java 中，这很直接：你直接创建 setter 方法即可。但对于 Scala 来说则更为繁琐，因为编译器会生成这些方法，而你不得不重定义它们。

例如，一旦 `id` 被设置后，我们可能希望阻止它被更新。在 Java 中，我们可以这样做：

```
// java
public void setId(String id) {
if (id.isEmpty())
this.id = id;
}
```

而 Scala 会自动创建 setter 方法，那么我们该如何重定义它呢？如果我们尝试直接在 Scala 代码中替换 setter，就会遇到如下编译器错误：

```
// scala 无法编译
class Customer(val name: String, val address: String) {
private var id = ""
def id_=(value: String) {
if (id.isEmpty)
this.id = value
}
}
```

Scala 无法识别要替换该方法，因此它会创建第二个同名方法，编译器在发现重复时就会报错。

```
对重载定义存在歧义引用，
类 Customer 中的方法 id_= 类型为 (value: String)Unit
与类 Customer 中的方法 id_= 类型为 (x$1: String)Unit
参数类型 (String) 匹配
this.id = value
方法 id_= 被定义了两次
冲突符号均源自文件 'Customer.scala'
def id_=(value: String) {
^      ^
```

要重定义该方法，我们必须绕些弯路。首先，我们必须重命名字段（例如改为 `_id`），并将其设为私有，从而使 getter 和 setter 变为私有。然后，我们创建一个名为 `id` 的新 getter 方法和一个名为 `id_=` 的新 setter 方法，它们都是公有的，用于访问已重命名的私有字段。

```
class Customer(val name: String, val address: String) {
private var _id: String = ""
def id = _id
def id_=(value: String) {
if (_id.isEmpty)
_id = value
}
}
```

我们通过 `private` 修饰符隐藏了真正的字段 `_id`，并暴露了一个名为 `id_=` 的方法来充当 setter。由于不再存在名为 `id` 的字段，Scala 就不会尝试生成重复的方法，代码也就能编译通过了。

```
// REPL 会话
scala> val bob = new Customer("Bob", "32 Bread Street")
bob: Customer = Customer@e955027
scala> bob.id = "001"
bob.id: String = 001
scala> println(bob.id)

scala> bob.id = "002"
bob.id: String = 001
scala> println(bob.id)

```

查看反编译后的版本，你可以看到如何重定义该方法。我们隐藏了真正的字段，并暴露了公有方法，以字段名的形式来合成对其的访问。

```
1   // 从 scala 反编译为 java
2   public class Customer {
3       private final String name;
4       private final String address;
5       private String _id;

7       public String name() {
8           return this.name;
9       }

11       public String address() {
12           return this.address;
13       }

15       private String _id() {                     // 私有
16           return this._id;
17       }

19       private void _id_$eq(String x$1) {         // 私有
20           this._id = x$1;
21       }

23       public String id() {                       // 公有
24           return this._id();
25       }

27       public void id_$eq(String value) {         // 公有
28           if (!this._id().isEmpty()) return;
29           this._id_$eq(value);
30       }

32       public Customer(String name, String address) {
33           this.name = name;
34           this.address = address;
35           this._id = "";
36       }
37   }
```

为什么需要 Getter？

你可能想知道为什么我们要创建 getter 方法 `def id()`。除非该类同时定义了 setter (`id_=`) 和 getter 方法，否则 Scala 不允许我们使用简写的赋值语法来设置值。

在 Java 中重定义 setter 和 getter 相当常见，但在实践中，Scala 中则不那么常见。尽管本节内容可能看起来有些学术化，但如果你确实尝试在 Scala 中重定义这些方法并遇到意外的编译器错误时，你至少能理解其原因。

## 总结

使用 Scala 创建类非常直接。你只需向类定义中添加参数即可为类添加字段，编译器会为你生成等价的 Java 构造函数、getter 和 setter。

类文件中的所有字段都生成为私有，但会生成关联的访问器方法。这些生成的方法会受到类定义中 `val` 或 `var` 的影响。

*   如果使用 `val`，则会创建公有的 getter，但不会创建 setter。该值只能通过构造函数设置。
*   如果使用 `var`，则会创建公有的 getter 和 setter。该值可以通过 setter 或构造函数设置。
*   如果既未使用 `val` 也未使用 `var`，则不会生成任何方法，该值只能在主构造函数的作用域内使用；在这种情况下，它实际上不是一个字段。
*   在类定义前加上 `private` 前缀不会改变这些规则，但会使所有生成的方法变为私有。

表 8-1 对此进行了总结。

表 8-1

生成的方法

| `class Foo(? x)` | `val x` | `var x` | `x` | `private val x` | `private var x` |
| --- | --- | --- | --- | --- | --- |
| 创建 Getter (`x()`) | 是 (`公有`) | 是 (`公有`) | 否 | 是 (`私有`) | 是 (`私有`) |
| 创建 Setter (`x_=(y)`) | 否 | 是 (`公有`) | 否 | 否 | 是 (`私有`) |
| 生成的构造函数包含 `x` | 是 | 是 | 否 | 是 | 是 |

如果你需要覆盖生成的方法，则必须重命名字段并将其标记为私有。然后，使用原始名称重新创建 getter 和 setter 方法。在实践中，你并不需要经常这样做。

脚注 1

[`http://www.benf.org/other/cfr/`](http://www.benf.org/other/cfr/)

