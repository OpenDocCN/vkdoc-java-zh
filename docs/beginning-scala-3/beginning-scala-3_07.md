# 7. 特质与枚举

在本章中，你将学习 Scala 两个最有用的特性。这些特性也存在于其他使用 JVM 的语言中，例如 Java 或 Kotlin——虽然名称不同，但背后的概念相同。

其中之一是特质。特质在 Java 世界中充当接口的角色，帮助你构建可重用的程序部分，并处理多重继承的难题，通过混合组合来规避单继承的缺点。

第二个概念是枚举，它帮助你将特定类型的所有可能值封装在一个地方。例如，日志级别可能有五个可能的值：INFO、WARN、DEBUG、ERROR 和 FATAL。此外，枚举提供了一种创建与这些值交互的方法的方式。

需要提及一点：这些特性在最新版本的 Scala 中并非全新。它们在之前的版本中就已存在，但存在许多差异。最相关的例子是枚举，它像是一种用新方式做事的方法，旨在简化开发者的使用。

## 特质

*特质* 通过封装方法和状态，并提供将它们混入类的可能性，从而在 Scala 中提供代码可重用性，进而允许代码重用。通过这种方式，一个类可以混入多种特质，这与继承不同，在继承中每个类只允许继承自一个超类。此外，除了使用关键字 `trait` 之外，特质定义类似于类定义，如下所示：

```
scala> trait Gliding:
|     def java () =
|         println("gliding")
// 定义了特质 Gliding
```

这个名为 `Gliding` 的特质没有声明超类，因此像类一样，它拥有默认的超类 `AnyRef`。`Gliding` 特质是一个简单的例子，但足以展示特质的工作方式。

以下是你可以对特质执行的一些操作：

*   拥有具体的抽象字段或方法

*   创建接收特定参数的构造函数

*   组合或扩展不同的特质

*   限制或限定哪些类可以使用它

你可以将特质视为类似于 Java 中的接口，你可以在其中定义某种默认行为。在 Java 8 或更早版本中，这是无法做到的，并且一个类可以扩展多个接口以实现多重继承。

表 7-1 展示了使用 JVM 的不同语言中相同的代码块。

表 7-1

表达特质概念的不同方式

| *语言* | *示例* |
| --- | --- |
| Java | `interface Gliding {    default void gliding() {        System.out.println("gliding");    }}` |
| Kotlin | `internal interface Gliding {   fun gliding() {       println("gliding")   }}` |
| Scala | `trait Gliding:    def gliding () =        println("gliding")` |

一切看起来都很好，但是你如何在类中使用这个特质并调用其中一个方法呢？方法如下：

```
scala> class ConcreteClass extends Gliding
// 定义了类 ConcreteClass
scala> val instance = ConcreteClass()
val instance: ConcreteClass = ConcreteClass@78e043e4
scala> instance.gliding()
gliding
```


好的，作为一名高级文档工程师和翻译员，我将严格遵循您提供的注意事项和示例格式，将给定的英文文本翻译成中文。


### 将特质用作混入

在单继承中，一个类只能从一个父类继承方法和字段。多继承则允许一个类从多个类继承方法和字段。然而，多继承可能会带来问题，因为继承类的顺序可能会无意中影响子类的行为。

混入组合是解决多继承问题的一种更好的方法，它规避了单继承的缺点。在 Java 中，一个类可以实现任意数量的接口以实现多重抽象。与 Java 不同，Scala 提供了一种在接口中定义和使用可重用代码的机制，该机制对所有实现该接口的类都有效。你可以使用抽象类来定义和使用此类可重用代码，但一个类只能继承一个抽象类，这排除了多继承的可能性。

术语 *混入* 用于指代这种可以独立维护的可重用代码。你可以使用特质，其方式类似于 Java 接口的使用方式。当你为特质添加实现时，它们就变成了混入。你可以创建一个继承自类的特质，也可以创建一个扩展特质的类。一旦定义了特质，就可以使用 `extends` 关键字将其混入到一个类中。以下代码展示了一个使用 `extends` 混入了 `Gliding` 特质的类：

```
scala> class Glider extends Gliding:
|     override def toString = "glider"
// defined class Glider
```

你可以使用 `extends` 关键字来混入一个特质；在这种情况下，你会隐式地继承该特质的超类。在下面的代码中，类 `Glider` 混入了 `Gliding`。你可以像下面这样使用从特质继承的方法：

```
scala> val glider = Glider()
val glider: Glider = glider
scala> glider.gliding()
gliding
```

特质也定义了一个类型。下面是一个将 `Gliding` 用作类型的示例：

```
scala> val g: Glider = glider
g: Glider = glider
scala> g.gliding()
gliding
```

在前面的示例中，`g` 的类型是 `Glider` 特质，因此 `g` 可以用任何其类混入了 `Glider` 的对象来初始化。

在下面的代码中，类 `Glider` 从特质 `Glider` 继承了 `gliding` 的一个实现。类 `Glider` 可以像上面展示的那样重写 `gliding`。

```
scala> class Glider extends Gliding:
|     override def toString = "glider"
|     override def gliding() = println("race for now " + toString)
// defined class Glider
```

因为 `Glider` 重写了 `Gliding` 对 `gliding` 的实现，所以当你调用它时会得到一个新的行为。

```
scala> glider.gliding()
race for now glider
```

从根本上说，特质类似于 Java 接口。与接口一样，你可以在特质中只声明你希望扩展类实现的方法，如下所示：

```
trait TraitA:
def methodA(): Unit
def methodAWithParam(param :String): Unit
def methodWithReturnType: String
```

这段代码展示了一个声明了不接受任何参数的方法的特质。不带参数的方法可以用 `def` 关键字后跟方法名来声明，如第一个方法 `def methodA` 所示。如果方法需要参数，你可以像往常一样列出它们。

一个特质可以扩展另一个特质，如下所示：

```
trait TraitB extends TraitA:
def methodB(): Unit
```

当一个类扩展一个特质时，根据该类是扩展一个特质还是多个特质，使用 `extends` 和 `with` 关键字。当一个类扩展一个特质时，使用 `extends` 关键字。

```
class ClassA extends TraitA:
// code
```

如果一个类扩展了多个特质，则对第一个特质使用 `extends`，并使用 `with` 来混入其他特质。

```
class ClassA extends TraitA with TraitB:
// code
```

如果一个类扩展了一个类和一个特质，总是在类名之前使用 `extends`，并在特质名称之前使用 `with`。

```
class ClassA extends ClassB with TraitA with TraitB:
// code
```

扩展特质的类必须实现该特质的所有抽象方法，除非扩展该特质的类本身是抽象的。

```
class ClassA extends TraitA:
def methodA(): Unit = print("methodA")
def methodAWithParam(param :String): Unit = print(param)
def methodWithReturnType: String = "something"
```

这里 `ClassA` 没有被声明为抽象，因此它实现了特质 `TraitA` 的所有抽象方法。

注意

一个特质可以由抽象方法和具体方法共同组成。

然而，如果一个类扩展了一个特质但没有实现该特质中定义的抽象方法，那么扩展该特质的类必须被声明为 `abstract`。

```
abstract class ClassA extends TraitA:
def methodA(): Unit { // code... }
def methodAWithParam(param :String): Unit{ // code... }
```

这里 `ClassA` 没有实现 TraitA 的 `methodWithReturnType` 方法。特质的子类可以选择重写该特质的方法（如果它愿意的话）。

以下代码展示了一个 `Vehicle` 特质，它为 `drive` 方法提供了一个实现：

```
trait Vehicle:
def drive() = println("Driving")
def race(): String
```

在下面的代码中，`drive` 是一个具体方法，`race` 是一个抽象方法。这里的 `Car` 类没有重写 `Vehicle` 特质的 `drive` 方法。

```
class Car extends Vehicle:
def race(): String = "Racing the car"
```

下面代码中的 `Boat` 类重写了 `Vehicle` 特质的 `drive` 方法：

```
class Boat extends Vehicle:
override def drive() =  println("float")
def race() =  "Racing boat."
```

注意

尽管 Scala 有抽象类，但建议使用特质而不是抽象类来实现基础行为，因为一个类只能扩展一个抽象类，但可以实现多个特质。如果你希望基础行为在 Java 代码中被继承，请使用抽象类。

你也可以在特质中使用字段。特质的字段可以声明为 `var` 或 `val`，并且可以通过为字段赋予初始值来使其成为具体字段，或者通过不分配初始值来使其成为抽象字段。以下代码展示了一个名为 `CarTrait` 的特质，它有一个抽象字段 `door` 和一个具体字段 `seat`：

```
trait CarTrait:
var door: Int
var seat = 4
```

接下来的代码展示了一个扩展了 `CarTrait` 的 `Car` 类。如你所见，你不需要使用 `override` 关键字来重写 `var` 字段 `door` 和 `seat`。

```
class Car extends CarTrait:
var door = 4
seat = 5
```

然而，你需要在特质的子类中使用 `override` 关键字来重写一个 `val` 字段。

```
trait CarTrait:
val door: Int
class Car extends CarTrait:
override val door = 5
```

在扩展了 `CarTrait` 特质的 `Car` 类中，你需要为抽象字段定义值。否则，你需要将该类定义为抽象类。

如你所见，特质可以声明字段并维护状态。类定义和特质定义的语法完全相同，除了类定义可以拥有传递给类主构造函数的参数，而特质定义不能拥有此类参数。

```
class Car(door: Int )
trait Car (door: Int) // does not compile - Car is already defined as class Car
```

在下一节中，你将探索如何使用特质来建模在 Java 中无法建模的复杂类层次结构。



### 特质与类层次结构

在单继承约束下开发类层次结构时，最大的挑战之一是确定哪些内容应作为基类，以及它们在类层次结构中的位置。如果你正在对生物进行建模，当任何动物都可能拥有腿时，该如何对有腿的生物建模？是否应该存在`LeggedAnimals`（有腿动物）和`LeglessAnimals`（无腿动物）？但这样一来，又该如何处理哺乳动物和爬行动物？也许你可以将`HasLegs`（有腿）定义为一个接口，但那样的话，你甚至可以让植物也拥有腿。Scala 来拯救你了。

你已经看到特质可以实现方法。此外，特质还可以规定它们可以被混入到哪些类和其他特质中。更进一步，你可以声明由多种类型组合而成的参数，例如：

```
def foo(bar: Baz with Blarg with FruitBat)
```

只有那些同时继承了`Baz`、`Blarg`和`FruitBat`的类的实例才能被传入此方法。

```
abstract class LivingThing
abstract class Plant extends LivingThing
abstract class Fungus extends LivingThing
abstract class Animal extends LivingThing
```

到目前为止一切顺利。一个`LivingThing`必须是植物、真菌或动物。但是，腿呢？谁可以拥有腿？

```
trait HasLegs extends Animal:
def walk() = println("Walking")
```

`HasLegs`特质继承了`Animal`。但`Animal`是一个类，那么特质继承一个类意味着什么？这意味着编译器只允许你将`HasLegs`混入到那些继承自`Animal`的类中。因此，你定义了只有动物才有腿，但任何类型的动物都可以有腿。`HasWings`（有翅膀）也是如此。

```
trait HasWings extends Animal:
def flap() = println("Flap Flap")
```

但只有有翅膀的东西才能飞。这是一种不同的表示法。你通过`this: HasWings =>`来定义自身类型的规则。如果该特质没有被混入到一个也继承了`HasWings`的类中，编译器会标记错误。因此，你可以使用自身类型来定义给定特质可以被混入到哪些类中的规则。^(³⁴)

```
trait Flies:
this: HasWings => def fly()  = println("I'm flying")
```

而鸟类既有翅膀又有腿：

```
abstract class Bird extends Animal with HasWings with HasLegs
```

在继续之前，有一点小说明：你可以使用关键字`with`或逗号来连接多个特质。它们的作用相同。

```
abstract class Bird extends Animal, HasWings, HasLegs
```

让我们定义几种不同的`Bird`：

```
class Robin extends Bird with Flies
class Ostrich extends Bird
```

所有哺乳动物都有`bodyTemperature`（体温）。

```
abstract class Mammal extends Animal:
def bodyTemperature: Double
```

有些动物知道自己的名字，如果知道，它们会对自己的名字做出反应。

```
trait KnowsName extends Animal:
def name: String
```

因此，在下面的代码中，`Dog`是一种有腿且知道（能回应）自己名字的`Mammal`。

```
class Dog(val name: String) extends Mammal with HasLegs with KnowsName:
def bodyTemperature: Double = 99.3
```

有些猫和小孩被认为是知道名字但有时会忽略名字的哺乳动物。

```
trait IgnoresName:
this: KnowsName => def ignoreName(when: String): Boolean
def currentName(when: String): Option[String] =
if ignoreName(when) then None else Some(name)
```

现在你可以定义一个`Cat`类，它有腿，知道自己的名字，并且除了晚餐时间外都会忽略自己的名字。

```
class Cat(val name: String) extends Mammal with HasLegs with KnowsName with IgnoresName:
def ignoreName(when: String) =
when match
case "Dinner" => false
case _ => true
def bodyTemperature: Double = 99.5
```

有些`Animal`可以是`Athlete`（运动员），而`Runner`（跑步者）是有腿的`Athlete`：

```
trait Athlete extends Animal
```

这是一个`Runner`特质：

```
trait Runner:
this: Athlete with HasLegs => def run() = println("I'm running")
```

`Person`是一种有腿且知道名字的`Mammal`：

```
class Person(val name: String) extends Mammal with HasLegs with KnowsName:
def bodyTemperature: Double = 98.6
```

`Biker`（骑行者）是一种`Person`，但只能被添加到`Athlete`中：

```
trait Biker extends Person:
this: Athlete=> def ride() = println("I'm riding my bike")
```

最后，让我们定义一些`Gender`（性别）。

```
trait Gender
trait Male extends Gender
trait Female extends Gender
```

你已经定义了一个复杂的类和特质层次结构。让我们看看能用这些类做些什么。首先，尝试创建一个同时也是`Biker`的`Dog`：

```
scala> val bikerDog = new Dog("biker") with Athlete with Biker
-- Error:
1 |val bikerDog = new Dog("biker") with Athlete with Biker
|                               ^^^^^
|                               非法的特质继承：超类 Dog 并非派生自特质 Biker 的超类 Person
```

很好，编译器强制执行了你关于`Biker`必须是`Person`的规则。让我们创建一些有效的`LivingThing`。请注意，你可以在对象创建时组合不同的特质。因此，`archer`是一个继承自`Dog`并实现了`Athlete`、`Runner`和`Male`的类的实例。Scala 编译器会自动为你创建这个新的匿名类。

```
scala> val archer = new Dog("archer") with Athlete with Runner with Male
archer: Dog with Athlete with Runner with Male = $anon$1@18bbc98
scala> val dpp = new Person("David") with Athlete with Biker with Male
dpp: Person with Athlete with Biker with Male = $anon$1@7b5617
scala> val john = new Person("John") with Athlete with Runner with Male
john: Person with Athlete with Runner with Male = $anon$1@cd927d
scala> val annette = new Person("Annette") with Athlete with Runner with Female
annette: Person with Athlete with Runner with Female = $anon$1@1ec41c0
```

你有一堆`Animal`了。让我们看看能用它们做什么。

```
scala> def goBiking(b: Biker) = println(b.name + " is biking")
goBiking: (Biker)Unit
scala> goBiking(dpp)
David is biking
```

如果你尝试让`Annette`去骑车会发生什么？

```
scala> goBiking(annette)
-- Error:
1 |goBiking(annette)
|         ^^^^^^^
|         发现:    (annette : Person & Athlete & (Runner & Female))
|         需要: Biker
```

这很合理。该方法需要一个`Biker`，而`Annette`不是`Biker`。然而，正如你可以用特质组合成一个类一样，你也可以要求一个类实现多个特质才能作为方法的参数。

```
scala> def charityRun(r: Person with Runner) = r.run()
charityRun: (Person with Runner)Unit
```

`charityRun`方法只能被一个既是`Person`的子类又实现了`Runner`特质的参数调用。

```
scala> charityRun(annette)
I'm running
```

如果你尝试用一个不是`Person`的`Runner`来调用该方法会怎样？

```
scala> charityRun(archer)
:7: error: 类型不匹配;
发现    : Dog with Athlete with Runner with Male
需要  : Person with Runner
charityRun(archer)
```

你可以用特质来定义参数。`womensRun`方法只能被一个既是`Runner`又是`Female`的参数调用。

```
scala> def womensRun(r: Runner with Female) = r.run()
womensRun: (Runner with Female)Unit
scala> womensRun(annette)
I'm running
scala> val madeline = new Cat("Madeline") with Athlete with Runner with Female
madeline: Cat with Athlete with Runner with Female = $anon$1@11dde0c
scala> womensRun(madeline)
I'm running
```



通过这种方式，你建模了复杂的关系。你以一种无法用 Java 实现的方式对事物进行了建模。Scala 的组合规则是定义复杂类层次结构、指定类组合规则以及向方法传递参数规则的强大工具。通过这种方式，你可以确保 `charityRun` 方法只能使用有效的参数调用，而无需在运行时测试参数的正确性，并在参数不正确时抛出异常。这种增强的建模灵活性与更强的类型安全性相结合，为架构师提供了另一个帮助开发者编写正确代码的工具。

### 方法名冲突

使用特质（trait）一切看起来都很棒，但如果你从两个包含相同方法的不同特质进行扩展，会发生什么？为了通过一个例子来了解这个问题，让我们创建两个特质：

```
scala> trait TraitOne:
|     def text = "One"
// 定义了特质 TraitOne
scala> trait TraitTwo:
|     def text = "Two"
// 定义了特质 TraitTwo
```

现在，创建一个同时扩展这两个特质的类，看看会发生什么。

```
scala> class Concrete extends TraitOne with TraitTwo
1 |class Concrete extends TraitOne with TraitTwo
|      ^
|      class Concrete 继承了冲突的成员：
|        特质 TraitOne 中的方法 text，类型为 => String 以及
|        特质 TraitTwo 中的方法 text，类型为 => String
|      （注意：可以通过在 class Concrete 中声明 override 来解决此问题。）
```

控制台中出现的错误试图解释编译器无法决定使用哪个方法实现。有几种方法可以解决这个问题。其中之一是重写该方法并定义其行为。

```
scala> class Concrete extends TraitOne with TraitTwo:
|     override def text = "Class"
// 定义了 class Concrete
```

这个解决方案可以编译，但你丢失了两个特质中方法的行为。为了解决这个问题，你可以创建新方法，使用 `super` 来调用特质的方法。

```
scala> class Concrete extends TraitOne with TraitTwo:
|     override def text = "Class"
|     def textOne = super[TraitOne].text
|     def textTwo = super[TraitTwo].text
// 定义了 class Concrete
```

现在看看当你创建 `Concrete` 类的实例时会发生什么。

```
scala> val con = Concrete()
val con: Concrete = Concrete@269bf0ac
scala> con.text
val res0: String = Class
scala> con.textOne
val res1: String = One
scala> con.textTwo
val res2: String = Two
```

最后一点说明：只有当两个特质的方法具有相同的名称、参数和返回类型时，才会出现此问题。如果其中一些不同，问题就不会出现，因为编译器会检测到它们是不同的方法。

### 限制特质的使用

有时，当你创建某个特质时，你希望它只在特定情况下使用。为了解决这个问题，有两种不同的方法：一种限制对扩展自另一个类/特质的访问，另一种是类/特质拥有一个特定的方法。

#### 按类限制访问

这种限制类型的思路是，只有包含特定特质的类/特质才能使用它。让我们通过一个具体的例子来理解这个概念。

```
scala> trait Engine:
|     val enable = true
// 定义了特质 Engine
scala> trait Vehicle:
|     this: Engine => def drive = println("driving")
// 定义了特质 Vehicle
```

在这个例子中，所有扩展自该特质的类/特质都需要扩展 `Engine`。否则，会出现异常。

```
scala> class Car extends Vehicle
1 |class Car extends Vehicle
|      ^
|      非法继承：类 Car 的自类型 Car 不符合
|      父特质 Vehicle 的自类型 Engine
```

如果你修改之前 `Car` 类的定义，使其同时扩展 `Vehicle` 和 `Engine`，一切就正常了。

```
scala> class Car extends Vehicle, Engine
// 定义了 class Car
```

这种类型的限制出现在“使用特质作为混入”一节的示例中。

#### 按方法限制访问

让我们看看如何将使用限制在拥有特定方法的类/特质上。首先，创建一个包含该方法定义的特质。

```
scala> trait Vehicle:
|     this: {def drive(): Unit} => println("drive")
```

现在创建一个名为 `Car` 但不包含 `drive` 方法的类，看看会发生什么。

```
scala> class Car extends Vehicle:
|     def notDrive(): Unit = println("drive")
1 |class Car extends Vehicle:
|      ^
|      非法继承：类 Car 的自类型 Car 不符合
|      父特质 Vehicle 的自类型 Object{drive(): Unit}
```

如果你定义的方法与特质中出现的结构相同，一切就正常了。

```
scala> class Car extends Vehicle:
|     def drive(): Unit = println("drive")
// 定义了 class Car
```

### 类型参数或类型成员

特质提供了一种编写代码的方式，你可以将其用于任何泛型类型，或用于满足某个条件的特定类型。一个实际的例子是，假设你想要打印所有组件的状态。下面的例子展示了如何实现：

```
scala> class Component:
|     override def toString = "enable"
|
| trait Status[A]:
|     def status(a: A): Unit = println(a.toString)
|
| class Screen extends Status[Component]
```

在这个例子中，`Screen` 类定义了哪些类型支持打印状态。让我们看看调用该方法时会发生什么：

```
scala> val screen = Screen()
val screen: Screen = Screen@61dd1c3d
scala> val component = Component()
val component: Component = enable
scala> screen.status(component)
enable
```

### 向特质传递参数

Scala 3 允许你像抽象类一样在构造函数中传递参数，因此你可以将参数从具体类传递到特质。

```
scala> trait Vehicle(val model: String):
|      override def toString = s"model: $model"
|
| class Car(override val model:String) extends Vehicle(model):
|      override def toString = "car's " + super.toString()
// 定义了特质 Vehicle
// 定义了 class Car
```

现在，如果你用任意值创建 `Car` 类的实例，你就可以从该类中使用保存在特质构造函数中的值。

```
scala> val car = Car("C4")
scala> println(car.toString())
car's model: C4
```



## 枚举

枚举（enums）在 Scala 3 中并非新特性。事实上，它们在 Scala 2 中就已存在，但使用起来并不简单，因此许多开发者并不常用。这也是马丁·奥德斯基（Martin Odersky）和 Scala 团队决定重构整个特性，赋予其新面貌的主要原因之一。

那么，什么是枚举？枚举是一种将一组具有相同关系的内容进行分组的方式。枚举的一个应用示例是存储具有特定定义值的人员信息，例如性别或婚姻状况。

```
enum Genre:
case MALE, FEMALE, NO_BINARY
enum MaritalStatus:
case SINGLE, MARRIED, DIVORCED
```

定义枚举的方式很简单。你只需要使用关键字 `enum`。要指定可能的值，你需要使用关键字 `case`。

现在是时候以简单的方式使用这些常量了。让我们创建一个名为 `Person` 的类，其构造函数有两个参数。

```
scala> val anna = Person(Genre.FEMALE, MaritalStatus.SINGLE) ass Person(genre: Genre, maritalStatus: MaritalStatus):
override def toString() = s"Genre: $genre - Marital status: $maritalStatus"
```

下一步是创建该类的实例，传入值，并打印结果。

```
scala> val anna = Person(Genre.FEMALE, MaritalStatus.SINGLE)
val anna: Person = Genre: FEMALE - Marital status: SINGLE
scala> println(anna.toString())
Genre: FEMALE - Marital status: SINGLE
```

将所有可能的值放在一个地方很好，但如果你不想将整个字符串持久化到数据库中怎么办？为了解决这个问题，枚举允许你传递代表某个值的变量。让我们修改 `Gender` 枚举来展示每个可能的值。

```
enum Genre(val code: Int):
case MALE extends Genre(1)
case FEMALE extends Genre(2)
case NO_BINARY extends Genre(3)
```

如果你想知道枚举中某个值的代码，你只需要调用 `code` 字段。

```
scala> println(Genre.MALE.code)

```

枚举默认提供一组方法或操作来与这些值进行交互。它们如表 7-2 所示。

表 7-2

枚举操作

| *方法* | *描述* | *示例* |
| --- | --- | --- |
| `ordinal` | 与每个出现的可能值相关联的唯一整数 | `scala> println(Genre.MALE.ordinal)       0` |
| `values` | 以数组形式获取所有可能的值 | `scala> Genre.valuesval res0: Array[Genre] = Array(MALE, FEMALE, NO_BINARY)` |
| `fromOrdinal` | 从序数位置获取枚举中的值 | `scala> Genre.fromOrdinal(2)val res1: Genre = NO_BINARY` |
| `valueOf` | 从字符串获取枚举中的值 | `scala> Genre.valueOf("MALE")val res2: Genre = MALE` |

请注意，Scala 中的枚举与 Java 中的不同。这与集合的情况类似，因此为了获得良好的性能，请尽量不要使用它。

从 Java 中使用枚举的方式是使用该类型进行扩展和参数化。

```
scala> enum Genre extends Enum[Genre]:
|     case MALE, FEMALE, NO_BINARY
// defined class Genre
```

如你所见，枚举可以帮助减少重复代码，或将某个事物的所有可能值保存在一个地方。请仅在值数量有限且较少时使用它们。

### 枚举中的代数数据类型

ADT（代数数据类型）是一种分类，你可以通过枚举其包含的所有值来定义一组值。枚举支持使用 ADT 并添加特定方法。让我们通过一个简单的例子来理解这个概念。

```
scala> enum Options[+T]:
|   case Some(x: T)
|   case None
// defined class Options
```

如你所见，`Options` 是一个参数化的枚举，包含两个简单的情况。它的使用方式很简单，与任何普通枚举一样。

```
scala> Options.Some(1)
val res6: Options[Int] = Some(1)
scala> Options.None
val res7: Options[Nothing] = None
```

你可以像处理普通枚举一样，创建一个自定义方法来与不同的值进行交互。

```
scala> enum Options[+T]:
|     case Some(x: T)
|     case None
|
|     def isDefined: Boolean = this match
|       case None => false
|       case _    => true
|
// defined class Options
```

在枚举中调用方法的方式很简单。你只需调用特定值的方法即可。

```
scala> Options.None.isDefined
val res8: Boolean = false
```

## 联合类型与交集类型

这些是 Scala 3 中出现的新特性，用于组合不同的东西。

联合类型允许你以某种方式接收、返回或与不同类型进行交互。让我们通过一个实际例子来理解这个概念。创建一个方法，根据一个参数返回两种可能的类型。

```
scala> class Car
| class Boat
scala> def createVehicle(vehicleType: Int): Car | Boat =
|     vehicleType match
|         case 1 => Car()
|         case _ => Boat()
def createVehicle(vehicleType: Int): Car | Boat
```

请注意，联合类型中类的顺序没有特定约束。你可以颠倒顺序，代码将以相同的方式执行。

```
scala> def createVehicle(vehicleType: Int): Boat | Car =
|     vehicleType match
|         case 1 => Car()
|         case _ => Boat()
def createVehicle(vehicleType: Int): Boat | Car
```

当你调用该方法并传递参数时，你会得到某个特定类的具体实例。

```
scala> val vehicle = createVehicle(1)
val vehicle: Car | Boat = Car@11ce721f
```

当你需要返回或传递多个类型时，联合类型是理想的选择，但当代码中添加更多类型时，复杂性也会增加。

交集类型与联合类型并没有太大不同。交集类型背后的思想是组合两个或多个不同类型的功能。

```
scala> trait Printable:
|     def print() =
|         println("print")
|
| trait Status:
|     def isEnable() =
|         println("isEnable")
|
| class Component extends Printable, Status
|
| def checkComponent(x: Printable & Status) =
|     x.print()
|     x.isEnable()
// defined trait Printable
// defined trait Status
// defined class Component
def checkComponent(x: Printable & Status): Unit
```

在这个例子中，`checkComponent` 需要接收一个包含这两个特质的东西，以便使用其中的方法。这种方法是一种很好的方式，可以避免直接指定某个具体的类或另一个特质。它说的是：“给我一个同时包含这两个特质的东西。”

现在让我们看看当你创建 `Component` 类的实例时会发生什么。

```
scala> val component = Component()
scala> checkComponent(component)
print
isEnable
```

如你所见，一切正常，但如果你传递的东西没有涵盖这两个特质，就会出现异常。

```
scala> class Button extends Printable
scala> val button = Button()
scala> checkComponent(button)
1 |checkComponent(button)
|               ^^^^^^
|               Found:    (button : Button)
|               Required: Printable & Status
```

## 总结

本章展示了特质如何工作以及如何使用它们。你看到了特质如何封装方法和字段定义，然后可以通过将它们混入类中来重用。你了解到特质类似于多重继承，但它们避免了多重继承的一些困难。

在本章中，你探讨了 Scala 中枚举的优势，即当你有多个以某种方式关联的值时，可以降低复杂性。此外，你还学习了使用联合类型和交集类型在代码中组合方法或参数的不同方式。

脚注 1



