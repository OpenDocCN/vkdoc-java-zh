# 10. DSL 与解析器组合子

领域特定语言（DSL）是一种专用语言，旨在表达属于特定问题领域的解决方案。与 Scala 和 Java 等通用语言相比，DSL 具有优势。不幸的是，这些通用语言存在缺点。例如，如果你想在数据库上执行一个任务，那么有必要使用通用语言编写一个计算机程序来执行此任务。然而，DSL 可用于在数据库上执行多个此类任务。这就是为什么一些专家将 SQL 视为 DSL 的原因。本章的范围是向你介绍 DSL 的世界以及它们如何与 Scala 交互。

一些著名的 DSL 示例包括 ErlangOTP^(³⁵)、HTML、SQL、Verilog^(³⁶)、Mathematica^(³⁷)、YACC^(³⁸)、Xpath^(³⁹)、CSS^(⁴⁰)、YAML^(⁴¹)、MATLAB^(⁴²) 和 ANT。



## 深入探究 DSL

DSL 专注于特定领域或问题，可分为外部 DSL 和内部 DSL 两种类型。外部 DSL 使用自定义语法和解析器组合子定义一种新语言。内部 DSL 同样定义一种新语言，但受限于另一种语言的语法边界。内部 DSL 无需自定义解析器，它们会像该语言中的其他代码一样被解析。近年来，对 DSL 的兴趣激增，部分原因是 Ruby^(⁴³) 和 Groovy^(⁴⁴) 社区的推动，因为在这些语言中实现 DSL 非常容易。使用 XML 的 Ant^(⁴⁵) 是外部 DSL 的一个例子。而 Gant^(⁴⁶) 则使用 Groovy 解决相同问题，是内部 DSL 的一个例子。Groovy 凭借其元编程^(⁴⁷) 能力和灵活的语法，更适合设计和实现内部 DSL。举例来说，利用 Groovy 的可选参数和 MOP^(⁴⁸)，你可以将这段只有程序员才喜欢的代码

```
println this.class.getResourceAsStream('readme.txt').getText()
```

转换成

```
write 'readme.txt'.contents()
```

即使你不是 Groovy 程序员，也能注意到，使用第二种写法，即使是非程序员也有机会理解代码的意图。正如你将看到的，Scala 为创建内部和外部 DSL 提供了出色的支持。

DSL 通常用于简化与特定小领域的系统交互。它可以通过提供简化的 API 来与系统通信，从而面向程序员；也可能面向业务用户，这些用户可能对某个领域足够了解，能够编写一些脚本，但他们并非程序员，难以处理通用编程语言。你可以将 DSL 视为一个程序，你编写一次，然后随着时间的推移，为那些理解 DSL 所解决的问题或理念的人引入修改。

DSL 提供了两种抽象方式：

1.  限制你可以使用的词汇表，因为它只与问题相关的术语或词语有关。
2.  为开发者提供关于特定语言结构或某些细节的一定程度的抽象。

使用 DSL 也有一些缺点：

*   创建起来很复杂，因为创建它们的技术并不简单，并且一开始需要付出一些努力。
*   当领域发生变化时，维护起来并不简单。
*   很难为用户隐藏错误细节。

关于 DSL 的结构，一个 DSL 有三个原则：

1.  以某种方式与问题领域相关的工件。DSL 需要提供工件的抽象层次，但也需要提供将工件与词汇表连接起来的机制。
2.  词汇表是 DSL 中最重要的部分之一，因为它建立了一种理解 DSL 试图解决的问题的方式。
3.  DSL 脚本是领域用户的接口，其实现不需要复杂。

内部 DSL 很容易创建，因为它不需要任何特殊的东西。你可以使用简单的代码来编写它。相比之下，外部 DSL 需要创建语法和解析器。

## 内部 DSL

内部 DSL 通常嵌入在宿主语言中，并通过语言的技巧和特殊结构添加语法糖。许多此类语言支持元对象协议，你可以使用它来为你的 DSL 实现动态行为。这些语言大多是动态类型语言，例如 Ruby 和 Groovy。在本章开头的示例中，Groovy 被用作 DSL 的宿主语言。静态类型语言，如 Scala，则提供了建模 DSL 的抽象能力。

Scala 使其成为内部 DSL 宿主语言的一些特性包括：

*   隐式转换
*   Scala 的高级类型系统
*   柯里化
*   中缀和后缀运算符表示法
*   语法糖
*   动态方法调用
*   灵活的名称规则

例如，对于任何只接受一个参数的方法，你可以省略括号和点号，如下所示：

```
map.get("key") 等同于 map get "key"
```

在本节中，你将学习如何使用 Scala 作为宿主语言来构建一个内部 DSL。隐式转换（参见第 8 章）让你完成了一半的工作，即为最终类添加方法。另一半工作在于，Scala 编译器会查找从你拥有的类型到你正在调用的方法所属类型的可能隐式转换。Scala 编译器会插入代码来调用隐式转换，然后在生成的实例上调用该方法。为现有类添加新方法的能力对于使代码更具可读性和表现力非常有价值。更重要的是，隐式转换使得在 Scala 中定义 DSL 成为可能。作为库的提供者，你可以创建语法上令人愉悦的方式，以类型安全的方式表达概念。将时间段表示为 `3 days` 或 `15 seconds` 不是很好吗？这比 `(3L * 24L * 3600L * 1000L)` 可读性强得多。在 2 小时内设置超时或触发器不是很好吗？让我们使用隐式转换定义一个库，然后对其进行分解。

```
import java.util.Date
import scala.language.implicitConversions
object TimeHelpers:
case class TimeSpanBuilder(val len: Long):
def seconds = TimeSpan(TimeHelpers.seconds(len))
def second = seconds
def minutes = TimeSpan(TimeHelpers.minutes(len))
def minute = minutes
def hours = TimeSpan(TimeHelpers.hours(len))
def hour = hours
def days = TimeSpan(TimeHelpers.days(len))
def day = days
def weeks = TimeSpan(TimeHelpers.weeks(len))
def week = weeks
def seconds(in: Long): Long = in * 1000L
def minutes(in: Long): Long = seconds(in) * 60L
def hours(in: Long): Long = minutes(in) * 60L
def days(in: Long): Long = hours(in) * 24L
def weeks(in: Long): Long = days(in) * 7L
given longToTimeSpanBuilder: Conversion[Long,TimeSpanBuilder] = in => TimeSpanBuilder(in)
given intToTimeSpanBuilder: Conversion[Int,TimeSpanBuilder] = in => TimeSpanBuilder(in)
def millis = System.currentTimeMillis
case class TimeSpan(millis: Long) extends Ordered[TimeSpan]:
def later = new Date(millis + TimeHelpers.millis)
def ago = new Date(TimeHelpers.millis - millis)
def +(in: TimeSpan) = TimeSpan(this.millis + in.millis)
def -(in: TimeSpan) = TimeSpan(this.millis - in.millis)
def compare(other: TimeSpan) = millis compare other.millis
object TimeSpan:
given tsToMillis: Conversion[TimeSpan, Long] = in => in.millis
class DateMath(d: Date):
def +(ts: TimeSpan) = Date(d.getTime + ts.millis)
def -(ts: TimeSpan) = Date(d.getTime - ts.millis)
given dateToDM: Conversion[Date, DateMath] = in => DateMath(in)
```

你导入 `java.util.Date`，因为你将要用到它。

```
import java.util.Date
```

然后，你定义一个以 `Long` 为参数的类，该类包含一系列方法，用于将 `Long` 转换为由长度表示的 `TimeSpanBuilder`。



```
case class TimeSpanBuilder(val len: Long):
def seconds = TimeSpan(TimeHelpers.seconds(len))
def second = seconds
def minutes = TimeSpan(TimeHelpers.minutes(len))
def minute = minutes
def hours = TimeSpan(TimeHelpers.hours(len))
def hour = hours
def days = TimeSpan(TimeHelpers.days(len))
def day = days
def weeks = TimeSpan(TimeHelpers.weeks(len))
def week = weeks
```

然后你定义一组辅助方法（从 `TimeSpanBuilder` 中调用），用于转换为正确的毫秒数。

```
def seconds(in: Long): Long = in * 1000L
def minutes(in: Long): Long = seconds(in) * 60L
def hours(in: Long): Long = minutes(in) * 60L
def days(in: Long): Long = hours(in) * 24L
def weeks(in: Long): Long = days(in) * 7L
```

接下来，你定义一组隐式方法，用于将 `Int` 或 `Long` 转换为 `TimeSpanBuilder`。这使得 `TimeSpanBuilder` 中的 `minutes` 或 `days` 等方法看起来像是 `Int` 和 `Long` 的一部分。

```
//Scala 3 格式
given longToTimeSpanBuilder: Conversion[Long,TimeSpanBuilder] = in => TimeSpanBuilder(in)
given intToTimeSpanBuilder: Conversion[Int,TimeSpanBuilder] = in => TimeSpanBuilder(in)
//Scala 2 格式
implicit def longToTimeSpanBuilder(in: Long): TimeSpanBuilder =TimeSpanBuilder(in)
implicit def intToTimeSpanBuilder(in: Int): TimeSpanBuilder = TimeSpanBuilder(in)
```

然后你定义一个辅助方法，用于获取当前时间的毫秒数。

```
def millis = System.currentTimeMillis
```

你定义表示时间段的 `TimeSpan` 类。你可以与其他 `TimeSpan` 进行数学运算，或者通过调用 `later` 或 `ago` 方法将此 `TimeSpan` 转换为 `Date`。`TimeSpan` 扩展了 `Ordered` 特质，以便你可以比较和排序 `TimeSpan`。

```
case class TimeSpan(millis: Long) extends Ordered[TimeSpan]:
def later = new Date(millis + TimeHelpers.millis)
def ago = new Date(TimeHelpers.millis - millis)
def +(in: TimeSpan) = TimeSpan(this.millis + in.millis)
def -(in: TimeSpan) = TimeSpan(this.millis - in.millis)
def compare(other: TimeSpan) = millis compare other.millis
```

接下来，你将此 `TimeSpan` 与另一个进行比较，以满足 `Ordered` 特质的要求。

```
def compare(other: TimeSpan) = millis compare other.millis
```

然后你定义一个伴生对象，其中包含一个隐式方法，用于将 `TimeSpan` 转换为 `Long`。如果存在一个与类同名的对象，则该对象被视为伴生对象。如果在伴生对象中定义了任何隐式转换，那么在需要转换该类的实例时，就会用到这些转换。你在伴生对象中定义了一个从 `TimeSpan` 到 `Long` 的隐式转换。这导致当 `TimeSpan` 被赋值给 `Long` 变量或作为需要 `Long` 类型的参数传递时，`TimeSpan` 实例会自动转换为 `Long`。

```
//Scala 3 格式
object TimeSpan:
given tsToMillis: Conversion[TimeSpan, Long] = in => in.millis
//Scala 2 格式
object TimeSpan {
implicit def tsToMillis(in: TimeSpan): Long = in.millis
}
```

你可以使用简单的语法定义 `TimeSpan` 实例，例如 3 天。时间段可以通过 `later` 和 `ago` 方法转换为 `Date`。但是，如果能将 `TimeSpan` 的加法和减法添加到 `Date` 实例中，将会很有帮助。使用隐式转换可以非常简单地实现这一点。首先，你定义一个 `DateMath` 类，它包含接受 `TimeSpan` 作为参数的 `+` 和 `-` 方法。

```
class DateMath(d: Date):
def +(ts: TimeSpan) = Date(d.getTime + ts.millis)
def -(ts: TimeSpan) = Date(d.getTime - ts.millis)
```

接下来你定义隐式转换。

```
//Scala 3 格式
given dateToDM: Conversion[Date, DateMath] = in => DateMath(in)
//Scala 2 格式
implicit def dateToDM(d: Date) = new DateMath(d)
```

写完大约 50 行代码后，让我们看看它是如何工作的。

```
scala> import TimeHelpers._
import TimeHelpers._
scala> 1.days
val res0: TimeHelpers.TimeSpan = TimeSpan(86400000)
scala> 5.days + 2.hours
val res1: TimeHelpers.TimeSpan = TimeSpan(439200000)
scala> (5.days + 2.hours).later
val res2: java.util.Date = Fri Nov 12 19:19:57 ART 2021
scala> import java.util.Date
import java.util.Date
scala> val lng: Long = 7.days + 2.hours + 4.minutes
lng: Long = 612240000
```

因此，你为时间段定义了一个不错的 DSL，并且它在必要时会自动转换为 `Long`。你看到了 Scala 的隐式转换如何产生非常简单和简洁的 DSL。选择隐式转换和设计领域特定语言需要时间、思考和斟酌。接下来是对外部 DSL 的简要介绍。

## 外部 DSL

外部 DSL 构建其语言处理基础设施：解析器、词法分析器和处理逻辑。你需要定义语法（例如巴科斯-诺尔范式^(⁴⁹) (BNF)）。也就是说，你需要定义所有成功解析含义或脚本所适用的规则。内部 DSL 可以免费从底层宿主语言获得此基础设施，但对于外部 DSL，你需要从头开始构建它们。在 Scala 中，解析器组合子是一个接近 BNF 语法定义的概念，并且在编写外部 DSL 时可以提供非常简洁优雅的代码。这个解析器组合子模块不是 Scala 标准库的一部分，但它为社区维护^(⁵⁰)。外部 DSL 拥有独立的词法分析、解析、解释、编译和代码生成基础设施。当你为外部 DSL 编写解析器时，你可以使用诸如 Antlr^(⁵¹) 之类的解析器生成工具。Scala 自带了一个解析器组合子库，因此你不必实现自己的语言基础设施。

## 总结

Scala 的解析器组合子库展示了 Scala 语法的灵活性、隐式转换的实用性以及函数式组合的强大功能。解析器组合子是领域特定语言的一个绝佳示例。其领域是解析文本，其语法几乎与 BNF 一一对应。这个库还让你对可以使用 Scala 创建的那种领域特定语言有了一些了解。Scala 编译器中没有专门针对解析器组合子库的内容；它仅仅是一个库。在实际层面上，使用单一语言 Scala 来定义解析器，而不是使用像 ANTLR 这样的工具，意味着你和你的团队使用单一语言来描述你的系统。这意味着你的大脑用 Scala 思考。这意味着你用单一语言编辑代码，并利用该语言的类型安全性。

脚注 1   2   3   4   5   6   7   8   9   10   11   12   13   14   15   16   17



