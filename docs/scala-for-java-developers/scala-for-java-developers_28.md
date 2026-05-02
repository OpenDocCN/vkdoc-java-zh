# 24. 管理你的代码库

一旦你拥有了新转换的 Scala 代码库，你将使用它一段时间。以下是一些你应该注意的实际问题，以及一些帮助你管理代码库的通用技巧。

## 约定

*   语法变体和约定如此之多，以至于可用的语法选项数量本身就令人望而生畏。确定你自己的约定。
*   找到一种适合你团队的工作方式并坚持下去。一开始要避免在语法模式之间切换上下文。这可能意味着一开始使用模式匹配而不是使用 `map`。等到每个人都感到舒适后再切换。
*   定期重新审视这些选择，因为一旦你开始运行，你不想被它们拖后腿。
*   以 Twitter 的努力¹为例。他们成功地将自己的经验浓缩成了一份非常实用的指南。非常值得一读。
*   Typesafe 有一个风格指南，²值得一读，但应带着批判性思维去看待。



## 应避免的事项

了解初学时应该避免什么也很有帮助。我真希望自己在开始之前就知道其中一些事情。

*   SBT 可能会让你的工作更困难。SBT 一开始非常简单，但如果你想做任何复杂的事情，它就会变得相当费力。要做好学习的准备；SBT 不是那种你从网上复制粘贴示例就能轻松驾驭的工具。你确实需要一些理解。我认为你会有更重要的事情要担心，所以我的建议是，一开始坚持使用你熟悉的构建工具。在 Maven 中支持 Scala 项目非常容易。
*   Scalaz 不是入门之选。这是给 Scala 的 Haskell 程序员用的库。它非常硬核，如果你没有多年纯函数式编程的经验，就直接避开它。你不需要知道什么是应用函子，或者为什么要使用 Kleisli。至少，一开始不需要。
*   “花哨的” Scala 库可能帮不上忙。当 Scala 刚出现时，一大批开源库涌现出来。人们变得忘乎所以，事后回想起来可能会承认他们在语法上有点过头了。结果就是，有很多 API 设计得并不好。
    *   Dispatch HTTP 库就是一个很好的例子。向请求添加查询参数的方法是 `<<?`。这远不如 `addRequestParameter` 直观。公平地说，Dispatch 提供了两种变体，但要点是，要警惕那些过度使用运算符重载的库。
    *   Anorm 是另一个例子，它提供了一个解析器 API 来提取 SQL 结果。它基本上解析了 `ResultSet`，但它的 DSL 非常不直观，以至于你会怀疑手动处理有什么问题。
*   Java 库完全没问题。你总是可以在 Scala 中使用你熟悉和喜爱的 Java 库。
*   缺乏一致性是危险的。我们已经提到过这一点：不要试图同时使用所有的语法变体。在继续之前，先彻底掌握一种。保持一致性。

## 其他挑战

你还会面临许多其他挑战。

*   编译速度很慢。这可能会也可能不会让你担心，但要知道：它不太可能在短期内赶上 Java 的速度。部分原因是 Scala 编译器非常复杂；它做了大量的工作，这是有代价的。
*   让你的 IDE 构建配置与外部构建工具保持同步可能是一个挑战。Maven 现在已经非常成熟，它的 IDE 插件几乎可以为你处理所有事情，但另一方面，IDEA 的 SBT 插件存在一些问题。有一些第三方工具可以生成 IDE 项目文件，但效果可能因人而异。我能提供的最好建议是，为你的 IDE 选择那些看起来最受支持的工具。
*   我们已经讨论过函数式编程的连续谱，但值得再次提及的是，清楚自己想要达到什么位置是个好主意。我建议一开始以“小处函数式，大处面向对象”为目标。你需要持续监控自己的进度，并在感觉准备好时迎接下一个学习挑战。突破界限，不断学习。

脚注 1

[`http://twitter.github.io/effectivescala/`](http://twitter.github.io/effectivescala/)

  2

[`http://docs.scala-lang.org/style/`](http://docs.scala-lang.org/style/)

## 附录 A：代码清单

完整源代码请参见 [`https://github.com/tobyweston/learn-scala-java-devs`](https://github.com/tobyweston/learn-scala-java-devs) 。以下是文中选取的一些示例，并进行了扩展，以便为您提供更完整的参考上下文。

### 继承

#### Java 中的子类型继承

```
package s4j.java.chapter12 ;
public class Customer implements Comparable {
private final String name;
private final String address;
private final ShoppingBasket basket = new ShoppingBasket();
public Customer(String name, String address) {
this .name = name;
this .address = address;
}
public void add(Item item) {
basket.add(item);
}
public Double total() {
return basket.value();
}
}
package s4j.java.chapter12 ;
public class DiscountedCustomer extends Customer {
public DiscountedCustomer(String name, String address) {
super (name, address);
}
@Override
public Double total() {
return super .total() * 0.90;
}
public Double getDiscountAmount() {
return 10.0;
}
}
package s4j.java.chapter12 ;
public interface Item {
Double price();
}
package s4j.java.chapter12 ;
import java.util.HashSet ;
import java.util.Set ;
public class ShoppingBasket {
private final Set basket = new HashSet();
public void add(Item item) {
basket.add(item);
}
public Double value() {
return basket.stream().mapToDouble(Item::price).sum();
}
}
```

#### Java 中的匿名类

```
package s4j.java.chapter12 ;
class AnonymousClass {
public static void main(String... args) {
Customer joe = new DiscountedCustomer("Joe", "128 Bullpen Street");
// example of anonymous class
joe.add( new Item() {
@Override
public Double price() {
return 2.5;
}
});
joe.add( new Item() {
@Override
public Double price() {
return 3.5;
}
});
// which can be replaced with lambdas
joe.add(() -> 2.5);
joe.add(() -> 3.5);
System.out.println("Joe's basket will cost $ " + joe.total());
}
}
```

#### Scala 中的子类型继承

```
package s4j.scala.chapter12
class Customer ( val name : String , val address : String ) extends Ordered [ Custom
er ] {
private final val basket : ShoppingBasket = new ShoppingBasket
def add(item : Item ) {
basket.add(item)
}
def total : Double = {
basket.value
}
}
package s4j.scala.chapter12
class DiscountedCustomer (name : String , address : String ) extends Customer (nam
e, address) {
override def total : Double = {
super .total * 0.9
}
}
package s4j.scala.chapter12
trait Item {
def price : Double
}
package s4j.scala.chapter12
import scala.collection.mutable
class ShoppingBasket {
private val basket = mutable. HashSet [ Item ]()
def add(items : Item* ) {
for (item <- items)
basket.add(item)
}
def value : Double = {
basket.map( _ .price).sum
}
}
```

#### Scala 中的匿名类

```
package s4j.scala.chapter12
object AnonymousClass extends App {
val joe = new DiscountedCustomer ("Joe", "128 Bullpen Street")
// example of anonymous class
joe.add( new Item {
def price = 2.5
})
joe.add( new Item {
def price = 3.5
})
println("Joe's basket will cost $ " + joe.total)
}
```

### 泛型



#### Java 中的下界

##### 示例 1

```
package s4j.java.chapter14 ;
import java.util.ArrayList ;
import java.util.Collections ;
import java.util.List ;
/**
* 创建一个基本的狮子围栏，并按年龄对狮子进行排序。
*/
public class LowerBounds1 {
public static > void sort(List list) {
Collections.sort(list);
}
public static void main(String... args) {
List enclosure = new ArrayList();
enclosure.add( new Lion());
enclosure.add( new Lion());
sort(enclosure);
}
static class Animal { }
static class Lion extends Animal implements Comparable {
private Integer age;
@Override
public int compareTo(Lion other) {
return this .age.compareTo(other.age);
}
}
static class Zebra extends Animal { }
}
```

##### 示例 2

```
package s4j.java.chapter14 ;
import java.util.ArrayList ;
import java.util.Collections ;
import java.util.List ;
/**
* 扩展 @{@link LowerBounds1} 以创建一个动物园（一个包含
* 不同类型动物（狮子和斑马）的围栏）
*/
public class LowerBounds2 {
public static > void sort(List list) {
Collections.sort(list);
}
public static void main(String... args) {
List enclosure = new ArrayList();
enclosure.add( new Lion());
enclosure.add( new Lion());
sort(enclosure);                 // 编译器错误
List zoo = new ArrayList();
zoo.add( new Lion());
zoo.add( new Lion());
zoo.add( new Zebra());
sort(zoo);                       // 如果 Animal 没有实现 Comparable，则无法编译
}
static class Animal implements Comparable {
@Override
public int compareTo(Animal o) {
return 0;
}
}
static class Lion extends Animal { }
static class Zebra extends Animal { }
}
```

##### 示例 3

```
package s4j.java.chapter14 ;
import java.util.ArrayList ;
import java.util.Collections ;
import java.util.List ;
/**
* 扩展 @{@link LowerBounds2}，在比较器中添加 ? super A，
* 从而允许对狮子围栏进行排序
*/
public class LowerBounds3 {
public static > void sort(List list) {
Collections.sort(list);
}
public static void main(String... args) {
List enclosure = new ArrayList();
enclosure.add( new Lion());
enclosure.add( new Lion());
sort(enclosure);              // 不再出现编译器错误
List zoo = new ArrayList();
zoo.add( new Lion());
zoo.add( new Lion());
zoo.add( new Zebra());
sort(zoo);
}
static class Animal implements Comparable {
@Override
public int compareTo(Animal o) {
return 0;
}
}
static class Lion extends Animal { }
static class Zebra extends Animal { }
}
```

#### Java 中的多重边界

##### Java 示例 1

```
package s4j.java.chapter14 ;
import java.util.ArrayList ;
import java.util.Collections ;
import java.util.List ;
public class MultipleBounds {
// 为泛型类型 A 设置两个上界
public static >
void sort(List list) {
Collections.sort(list);
}
public static void main(String... args) {
List enclosure = new ArrayList();
enclosure.add( new Lion());
enclosure.add( new Lion());
sort(enclosure);
List zoo = new ArrayList();
zoo.add( new Lion());
zoo.add( new Lion());
zoo.add( new Zebra());
sort(zoo);
}
static class Animal implements Comparable {
@Override
public int compareTo(Animal o) {
return 0;
}
}
static class Lion extends Animal { }
static class Zebra extends Animal { }
}
```

#### Scala 中的下界

##### 示例 1

```
package s4j.scala.chapter14
object LowerBounds1 {
def sort[ A <: Comparable [ A ]](list : List [ A ]) : Unit = { ??? }
def main(args : String* ) {
var enclosure = List [ Lion ]()
enclosure = new Lion +: enclosure
enclosure = new Lion +: enclosure
sort(enclosure)
}
class Animal
class Lion extends Animal with Comparable [ Lion ] {
def compareTo(o : Lion ) : Int = 0
}
class Zebra extends Animal
}
```

##### 示例 2

```
package s4j.scala.chapter14
object LowerBounds2 {
def sort[ A <: Comparable [ A ]](list : List [ A ]) : Unit = { ??? }
def main(args : String* ) {
var enclosure = List [ Lion ]()
enclosure = new Lion +: enclosure
enclosure = new Lion +: enclosure
sort(enclosure)              // 编译器失败
var zoo = List [ Animal ]()
zoo = new Zebra +: zoo
zoo = new Lion +: zoo
zoo = new Lion +: zoo
sort(zoo)                    // 如果 Animal 没有实现 Comparable，则无法编译
}
class Animal extends Comparable [ Animal ] {
def compareTo(o : Animal ) : Int = 0
}
class Lion extends Animal
class Zebra extends Animal
}
```

##### 示例 3

```
package s4j.scala.chapter14
object LowerBounds3 {
// 虽然是对 Java 代码的直接翻译，但这会导致
// "illegal cyclic reference involving type A" 错误：
// def sort[A : A]](a: List[A]) = { ??? }
// 相反，我们使用这个（并在下面的 sort 方法中提供一个类型提示）
def sort A : A  = { }
def main(args : String* ) {
var enclosure = List [ Lion ]()
enclosure = new Lion +: enclosure
enclosure = new Lion +: enclosure
sort Lion , Animal  // 不再出现编译器失败
var zoo = List [ Animal ]()
zoo = new Zebra +: zoo
zoo = new Lion +: zoo
zoo = new Lion +: zoo
sort(zoo)
}
class Animal extends Comparable [ Animal ] {
def compareTo(o : Animal ) : Int = 0
}
class Lion extends Animal
class Zebra extends Animal
}
```

#### Scala 中的多重边界

##### 示例 1

```
package s4j.scala.chapter14
class MultipleBounds {
// 我们不能像在 Java 中那样设置两个上界，但我们可以规定
// 边界类型还必须扩展某些特质
def sort[ A <: Lion with Comparable [ Animal ]](list : List [ A ]) = { }
def main(args : String* ) {
var enclosure = List [ Lion ]()
enclosure = new Lion +: enclosure
enclosure = new Lion +: enclosure
// sortLion, Animal         // 编译器错误
sort(enclosure)                           // 必须移除类型提示
}
class Animal extends Comparable [ Animal ] {
def compareTo(o : Animal ) : Int = 0
}
class Lion extends Animal
class Zebra extends Animal
}
```

### 模式匹配

#### 构造器匹配

##### 示例 1

```
package s4j.scala.chapter18
object BasicConstructorPatternExample1 extends App {
val hero = new SuperHero ("Batman", "Bruce Wayne", List ("Speed", "Agility"))
hero match {
case SuperHero ( _ , "Bruce Wayne", _ ) => println("I'm Batman!")
case SuperHero ( _ , _ , _ )              => println("???")
}
}
```

##### 示例 2

```
package s4j.scala.chapter18
object BasicConstructorPatternExample2 extends App {
// Joe 是一个 Person，而不是 SuperHero
val hero = new Person ("Joe Ordinary")
// 产生一个 'MatchError'，因为 Person 不匹配任何模式
hero match {
case SuperHero ( _ , "Bruce Wayne", _ ) => println("I'm Batman!")
case SuperHero ( _ , _ , _ )              => println("???")
}
}
```

##### 示例 3

```
package s4j.scala.chapter18
object BasicConstructorPatternExample3 extends App {
val hero = new Person ("Joe Ordinary")
// 添加通配符 _ 意味着 Joe 匹配最后一个 case
hero match {
case SuperHero ( _ , "Bruce Wayne", _ ) => println("I'm Batman!")
case SuperHero ( _ , _ , _ )              => println("???")
case _                              => println("I'm a civilian")
}
}
```

##### 示例 4

```
package s4j.scala.chapter18
object HeroConstructorPatternExample extends App {
val bruce = new SuperHero ("Batman", "Bruce Wayne", List ("Speed", "Agility"
))
val steve = new SuperHero ("Captain America", "Steve Rogers", List ("Tactics
", "Speed"))
val jane = new Person ("Jane Doe")
def superPowersFor(person : Person ) = {
person match {
case SuperHero ( _ , _ , powers) => powers
case _ => List ()
}
}
// 如果一个身份不明的人，其秘密身份是 Bruce Wayne，那么他拥有什么超能力？
println("Bruce has the powers: " + superPowersFor(bruce).mkString(", "))
println("Steve has the powers: " + superPowersFor(steve).mkString(", "))
println("Jane has the powers: " + superPowersFor(jane).mkString(", "))
// 注意 mkString 以用户友好的格式输出列表
}
```



#### 解构匹配与 unapply

##### Scala 最终示例

```
package s4j.scala.chapter18
class Customer ( val name : String , val address : String )
object Customer {
def unapply(customer : Customer ) : Option [( String , String )] = {
Some ((customer.name, customer.address))
}
}
```

### Map

#### 映射函数

```
package s4j.scala.chapter19
class Mappable  A  {
def map B  : List [ B ] = {
val result = collection.mutable. MutableList [ B ]()
elements.foreach {
result += f( _ )
}
result.toList
}
// 递归版本（使用嵌套 def）
def recur_map B  : List [ B ] = {
def recur(head : A , tail : List [ A ]) : List [ B ] = {
tail match {
case Nil => List (f(head))
case _ => f(head) +: recur(tail.head, tail.tail)
}
}
recur(elements.head, elements.tail)
}
// 尾递归版本
def tail_recur_map B  : List [ B ] = {
def recur(accumulator : List [ B ], elements : List [ A ]) : List [ B ] = {
elements match {
case Nil => accumulator
case head :: tail => recur(accumulator :+ f(head), tail)
}
}
recur( List [ B ](), elements)
}
}
package map {
object Example extends App {
val numbers = List (1, 2, 54, 4, 12, 43, 54, 23, 34)
val mappable = new Mappable (numbers)
println(mappable.map( _ * 2))
println(mappable.recur_map( _ * 2))
println(mappable.tail_recur_map( _ * 2))
}
}
```

#### FlatMap

```
package s4j.scala.chapter19
class FlatMappable  A  {
def flatMap B  : List [ B ] = {
val result = collection.mutable. MutableList [ B ]()
elements.foreach(element => {
f(element).foreach(subElement => {
result += subElement
})
})
result.toList
}
// 缩写/替代语法（如书中所示）
def flatMapAbbr B  : List [ B ] = {
val result = collection.mutable. MutableList [ B ]()
elements.foreach {
f( _ ).foreach {
result += _
}
}
result.toList
}
}
package flatmap {
object Example extends App {
def oddNumbersTo(end : Int ) : List [ Int ] = {
val odds = collection.mutable. MutableList [ Int ]()
for (i <- 0 to end) {
if (i % 2 != 0) odds += i
}
odds.toList
}
val mappable = new FlatMappable (1, 2, 10)
println(mappable.flatMap(oddNumbersTo))
println(mappable.flatMapAbbr(oddNumbersTo))
}
}
```

## 附录 B：语法速查表

### 值

```
val x : Int = 42
var y : String = "mutable"
val z = "Scala FTW!"                             // 使用类型推断
```

### 函数

```
def add(x : Int , y : Int ) : Int = x + y             // 单个表达式
def add(x : Int , y : Int ) {                        // 没有 =，返回 Unit
x + y
}
def min(x : Int , y : Int ) : Int = {                 // 最后一行 = 返回值
if (x < y) x else y
}
(x : Int , y : Int ) => x + y              // 匿名函数
add(4, 2)                                        // 像往常一样调用
// 将匿名类（此处为 Ordering）转换为 lambda
implicit def functionToOrdering A  => Int ) : Ordering [ A ] = {
new Ordering [ A ] {
def compare(a : A , b : A ) = f.apply(a, b)
}
}
```

### 按名调用

```
def runInThread(task : => Int ) {
new Thread () {
override def run() : Unit = task
}.start()
}
```

### 运算符重载与中缀表示法

```
5 * 10                                           // 等同于 5.*(10)
"Scala" replace("a", "*")
```

### 类

```
class Customer                                    // 创建一个公有类
class Customer (name : String )                     // 带有主构造函数
class Customer private (name : String )             // 私有构造函数
private class Customer                            // 一个私有类
private class Customer private (name : String )     // 带有私有构造函数
val c = new Customer ("Bob")                      // 创建一个实例
// 带有主构造函数和辅助构造函数的类
class Customer (forename : String , surname : String ) {
def this (surname : String ) {
this ("Unknown", surname)
}
}
```

### 样例类

```
case class Customer (name : String )                // 一个样例类
case class Customer ( val name : String )            // 这里的 val 是多余的
// 使用样例类时无需使用 "new" 来创建新实例
Customer ("Bob")
// 样例类自动提供相等性和哈希码：
new Customer ("Bob") == new Customer ("Bob")       // 返回 true
```

### 单例对象

```
// 一个单例实例
// 当与类配对时，该对象成为"伴生"对象
object Customer
```

### 继承

```
class B extends A                       // 子类型继承
class C (x : Int )
class D (value : Int ) extends A(value)    // 调用父类构造函数
// 重写方法（重写非抽象方法需要 "override"）
class E {
def position : Int = 5
}
class F extends E {
override position : Int = super .position + 1
}
// 你可以混入多个特质，但只能混入一个类（A）
trait SelfDescribing
trait Writable
class B extends A with SelfDescribing with Writable
class B extends SelfDescribing with A with Writable       // 编译器报错
```

### 字段

```
// name 不是字段，仅对主构造函数可用
class Customer (name : String )
// name 是公有字段（生成 getter，不生成 setter）
class Customer ( val name : String )
// name 是公有字段（生成 getter 和 setter）
class Customer ( var name : String )
// name 是私有字段（生成私有 getter，不生成 setter）
class Customer ( private val name : String )
// name 是私有的（生成私有 getter 和 setter）
class Customer ( private var name : String )
customer.name_=("Bob")       // 你可以直接调用 setter 方法
customer.name = "Bob"        // 或使用中缀表示法
```

### 集合

```
val list = List (1, 4, 234, 12)                   // 创建一个列表
val map = Map (1 -> "a", 2 -> "b")                // 创建一个映射
list.foreach(value => println(value))
list.foreach(println)
for (value <- list) println(value)
```

### 字符串格式化

```
// 字符串插值将 '$' 标记替换为值
s"Customer name: $name USD"                    // Customer name: Bob
// 表达式需要额外的括号
s"Customer basket value is $(customer.basket.value) USD"
// 'f' 前缀类似于 String.format
f"Square of 42 is ${ math.sqrt(42) } %1.2f"        // Square of 42 is 6.48
"Escaping \"quotes\" is the same as in Java"
// 三引号允许跨行并包含未转义的引号
""""vCard": {
"id" : "007",
"name" : "bond",
"address" : "MI5"
}"""
// 'raw' 字符串插值器不会转义通常的转义字符
raw"a\nb"                                      // a\nb
```

### Apply 方法

```
val bob = Customer .apply("Bob")                   // 用作工厂方法
val bob = Customer ("Bob")                         // 无需显式调用
val array = Array (1, 54, 23, 545, 23)
array.apply(0)                                    // 对数组应用 apply 是"getter"
array(0)                                          // 结果是 "1"
```

### Update 方法

```
val array = Array (1, 54, 23, 545, 23)
array(0)                                          // 结果是 "1"
array.update(0, 34)
array(0)                                          // 结果是 "34"
array(0) = 55                                     // = 是 update 的快捷方式
array(0)                                          // 结果是 "55"
```



### 模式匹配

```
value match {
case 'R'              => ...    // 字面量匹配
case Customer (name)   => ...    // 构造器匹配（样例类）
case x : Int           => ...    // 类型查询匹配（即 instanceof）
case x : Int if x > 5 => ...    // 带守卫条件
case (x, y)           => ...    // 使用 unapply 进行解构
case _               => ...    // 默认情况
}
// 相同的语法也用于 try/catch 和异常处理
try {
// ...
} catch {
case e : MalformedURLException => println("错误的 URL")
case e : IOException => println(e.getMessage)
}
```

### 协变类型

保持 `Stack[Human]` 和 `Stack[Man]`（其中 Man 是 `Human` 的子类型）之间的子类型关系。

```
class Stack [ +A ] {
...
}
```

#### 示例

```
class Human
class Man extends Human
class Woman extends Human
val people : Stack [ Human ] = Stack [ Human ]()
val men : Stack [ Human ] = Stack [ Man ]()            // 正确
val women : Stack [ Woman ] = Stack [ Human ]()        // 编译错误
```

### 上界（extends）

```
A <: B 表示类型变量 A 继承自 B。
```

### 逆变类型

```
class Stack [ -A ] {
...
}
```

### 下界（super）

`B >: A` 表示 B 是 `A` 的超类型。通常 A 是类的类型参数，而 B 是方法的类型参数。

```
class Stack [ +A ] {
def push B >: A  : Stack [ B ] = ...
}
```

索引 A 抽象类 匿名类 转换为 lambda 创建 vs. 函数 匿名函数 参见 Lambda/匿名函数 AnyVal 类型 AnyRef 类型 Apply 方法 辅助构造器 B 绑定 底类型 有界类 有界类型 打破控制流 C 按名调用 类 抽象类 匿名类 vs. 匿名函数 转换为 lambda 有界类 栈类 无构造器参数的类 类泛型 闭包 vs. lambda 伴生对象 条件语句 if 和三元运算符 switch 语句 构造器 附加/辅助构造器 模式 主构造器 泛型类型的逆变 协变泛型类型 花括号与函数字面量 柯里化 D 解构匹配 默认值 菱形运算符 (<>) Do 和 while 循环 E 表达式 vs. 语句 富有表现力的 Scala 提取器方法 F 伪函数调用 apply 方法 update 方法 多个参数传递给多个方法 一等函数 传入函数 返回函数 存储函数 flatMap 函数 For 推导式 foreach 嵌套 for 循环，花括号在发货标签中 foreach 方法 For 循环 函数调用 函数字面量 参见递归 参见 Lambda/匿名函数 函数 按名调用 柯里化 一等函数 参见一等函数 高阶函数 映射函数 flatMap 函数 map 函数 null 检查 类型 函子 G 泛型 类 方法 类型 协变 逆变 不变 变型 get 方法 getOrElse 方法 守卫条件 H 高阶函数 I If 和三元运算符 不可变性 不可变集合类型 导入别名 中缀表示法 继承 多重继承 子类型继承 互操作性 不变性，泛型类型 J, K Java 互操作性 L Lambda/匿名函数 vs. 匿名类 vs. 闭包 将匿名类转换为 语言构造，模拟 按名调用 花括号与函数字面量 柯里化 高阶函数 字面量匹配 循环结构 do 和 while 循环 for 循环 下界 M Map 函数 匹配表达式 方法泛型 方法 vs. 函数 单子 准则 定义 函子 单子方法 在 Option 上 Option 类 get 方法 getOrElse 方法 map 函数 Option.flatMap 函数 多重边界 多重继承 N 命名方法参数 Nothing 类型 Null 检查 Null 对象模式 Null 类型 O 对象 伴生 单例 运算符重载 Option 类 get 方法 getOrElse 方法 map 函数 Option.flatMap 函数 P, Q 包对象 参数多态 模式匹配 构造器模式 解构匹配 提取器方法 守卫条件 字面量匹配 模式 切换 类型查询 unapply 方法 多态 主构造器 原始类型 R 递归 REPL S Scala 附加/辅助构造器 采用 匿名类 vs. 匿名函数 转换为 lambda Any 类 AnyVal AnyRef 底类型 类创建 类，无构造器参数 伴生对象 泛型类型的逆变 泛型类型的协变 默认值 定义值和变量 派生 setter 和 getter 方法 一等函数 传入函数 返回函数 存储函数 函数式编程特性 惯用法 语言 函数字面量 函数 vs. 方法 函数类型 高阶函数 历史 不可变和声明式 不可变集合类型 导入别名 中缀表示法 安装 解释器 Java 代码库转换 lambda/匿名函数 学习连续体 学习曲线 下界 多重边界 命名方法参数 面向对象语言和函数式语言特性 运算符重载 包对象 多态 主构造器 重新定义 setter 和 getter 方法 引用类型 脚本 setter, getter 方法和字节码 单例对象 源文件 启动 使用 特质 vs. 抽象类 抽象字段 在匿名类上 转换为 lambda 具体字段 在默认方法上 类型别名 单元 值类型 上界 可变参数 通配符边界 scalac 编译器 Scala 代码库管理 挑战 约定 注意事项 ScalaDoc 用于 Char HTML 标记 类型层次结构图 密封特质 Setter 和 getter 方法 和字节码 派生 重新定义 Shell 脚本 单例对象 栈类 语句 vs. 表达式 子类型继承 Switch 语句 T 特质 vs. 抽象类 抽象字段 在匿名类上 转换为 lambda 具体字段 在默认方法上 元组 类型别名 类型构造器 类型层次结构图 类型查询 U Unapply 方法 无界通配符 单元 值类型 Update 方法 多个参数传递给多个方法 上界 V 可变参数 变型，泛型类型 W, X, Y, Z 通配符边界
