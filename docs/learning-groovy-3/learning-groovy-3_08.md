# 6. Groovy 设计模式

设计模式是让你的代码功能完善、可读性强且易于扩展的好方法。与 Java 相比，有些模式在 Groovy 中实现起来更简单，需要的代码也更少。

## 策略模式

假设你有三种不同的方法来计算总和，如下所示：

```
1   def totalPricesLessThan10(prices) {
2           int total = 0
3           for (int price : prices)
4                   if (price  10) total += price
11               total
12   }
13   def  totalPrices(prices) {
14           int total = 0
15           for (int price : prices)
16                   total += price
17           total
18   }
```

在这种情况下，大量代码是重复的。每个方法中只有一小部分发生了变化。在 Groovy 中，你可以使用一个闭包参数来代替三个不同的方法，从而得到以下代码：

```
1   def totalPrices(prices, selector) {
2           int total = 0
3           for (int price : prices)
4                   if (selector(price)) total += price
5           total
6   }
```

现在你有了一个方法 `totalPrices(prices, selector)`，其中 `selector` 是一个闭包。此外，如果闭包是最后一个参数，你可以将其放在方法调用的参数列表之外。因此，你可以通过以下方式调用此方法以获得所需结果：

```
1   totalPrices(prices) { it  10 }
3   totalPrices(prices) { true }
```

这不仅让你的代码更简洁，也更容易阅读和扩展。



## 元编程

Groovy 的元编程意味着你可以在运行时为任何类或接口添加功能（字段和方法）。这允许你为常用的类或接口添加辅助方法，使代码更简洁、更易读。

### 元类

在动态 Groovy 项目（不使用 `@CompileStatic` 或 `@TypeChecked`）中，你可以使用 `metaClass` 添加常用功能。例如，如果你经常需要将文本分割成单词，可以使用以下代码为 `String` 类添加一个方法：

```
String.metaClass.words = { -> split(/ +/) }
def text = "the lazy brown fox"
text.words() // 结果: ['the', 'lazy', 'brown', 'fox']
```

再举一个例子，假设你正在编写一个 `javax.servlet.Filter`，并且经常需要获取和设置会话属性。你可以通过以下方式为 `HttpSession` 接口虚拟地添加方法：

```
1   HttpSession.metaClass.getAt = { key -> getAttribute(key) }
2   HttpSession.metaClass.putAt = {
3       key, value -> setAttribute(key, value)
4   }
```

这允许使用以下语法来设置和获取会话的属性：

```
1   def  session = request.session
2   session['my_id'] = '123' // 调用 putAt('my_id', '123')
3   def  id = session['my_id'] // 调用 getAt('my_id')
```

### 类别

*类别*是 Groovy 中可用的众多元编程技术之一。类别是一个类，可用于向现有类添加功能。当你不想在整个应用程序中修改某个类，而只想对代码的有限部分进行特殊处理时，这会非常有用。

要创建一个类别，你需要创建一些静态方法，这些方法至少有一个特定类型的参数（例如，整数）。当使用该类别时，该类型（第一个参数的类型）除了其先前定义的方法外，似乎还拥有了这些方法。调用该方法的对象实例将作为第一个参数。

例如，Groovy 内置了用于操作日期和时间的 `TimeCategory`^(¹⁶) 类别。这允许你添加或减去任意长度的时间。例如：

```
1   import groovy.time.TimeCategory
2   def now = new Date()
3   println now
4   use(TimeCategory) {
5       nextWeekPlusTenHours = now + 1.week + 10.hours
6   }
7   println nextWeekPlusTenHours
```

在这个例子中，`TimeCategory` 向 `Integer` 类添加了一堆方法。例如，其中一些方法签名如下所示：

```
1   static Duration getDays(Integer self)
2   static TimeDuration getHours(Integer self)
3   static TimeDuration getMinutes(Integer self)
4   static DatumDependentDuration getMonths(Integer self)
5   static TimeDuration getSeconds(Integer self)
```

### ![../images/426440_2_En_6_Chapter/426440_2_En_6_Figa_HTML.gif](img/426440_2_En_6_Figa_HTML.gif) 练习

创建你自己的 `Category` 类，然后将其上传到 GitHub。

## 缺失方法

在 Groovy 中，你可以使用 `methodMissing` 方法来拦截缺失的方法（即被调用但未以传统方式定义的方法）。当你希望在运行时使用灵活的方法名称和签名动态定义方法时，这可能是一个非常有用的设计模式。`methodMissing` 的签名编写如下：

```
1   def methodMissing(String name, args) { /* 你的代码 */ }
```

`name` 参数是缺失方法的名称，`args` 参数是传递给该方法的所有参数（作为一个对象数组）。然后，你可以使用 `name` 和 `args` 参数编写你希望此方法具有的任何功能。

接下来，为了提高效率，你可以拦截、缓存并调用被调用的方法。例如：

```
1   def methodMissing(String name, args) {
2           def impl = { /* 你的代码 */ }
3           getMetaClass()."$name" = impl
4           impl()
5   }
```

这实现了缺失的功能，然后将其添加到当前类的 `metaClass` 中，以便将来的调用直接转到该实现，而不是 `methodMissing` 方法。如果你预计相同的缺失方法会被大量调用，这可能会很有用。

为了测试这一点，让我们实现一个非常简单的、仅打印被调用方法名称的方法：

```
def methodMissing(String name, args) {
println "in methodMissing"
def impl = { println name }
getMetaClass()."$name" = impl
impl()
}
```

现在我们可以像下面这样调用缺失的方法：

```
wow()
thisworks()
wow()
```

下面的输出显示，对“wow”的第二次调用使用了 `metaClass` 方法，而不是 `methodMissing`：

```
in methodMissing
wow
in methodMissing
thisworks
wow
```

## 委托

*委托*是指一个类拥有直接调用（方法签名相同）另一个类的方法。这在 Java 中很难实现，因为向类添加方法既困难又耗时。

使用 `@Delegate` 注解会容易得多。它类似于编译时元编程。它会自动将委托类的方法添加到当前类中。

例如：

```
1   public class Person {
2           def  eatDonuts() { println("yummy") }
3   }

5   public class RoboCop  {
6           @Delegate final Person person

8           public RoboCop(Person person) { this.person = person }
9           public RoboCop() { this.person = new Person() }

11           def crushCars() {
12                   println("smash")
13           }
14   }
```

尽管 `RoboCop` 没有 `eatDonuts()` 方法，但 `Person` 的所有方法都被添加到了 `RoboCop` 中，并委托给了 `person`。这允许以下用法：

```
1   def  person = new  RoboCop()
2   person.eatDonuts()
3   person.crushCars()
```

### ![../images/426440_2_En_6_Chapter/426440_2_En_6_Figb_HTML.gif](img/426440_2_En_6_Figb_HTML.gif) 练习

在 `List` 属性上使用 `@Delegate`，并用它来创建一个无法移除元素的列表。

脚注 1

