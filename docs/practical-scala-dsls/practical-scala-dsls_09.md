# 9. 外汇交易系统

到目前为止，我们已经在不同领域使用了领域特定语言。接下来你将看到如何创建一个外汇交易系统。如今，越来越多的人对投资感兴趣，并且越来越多的开发者从事金融领域的工作。

在本章中，你将看到如何使用 Scala 和领域特定语言创建一个用于外汇交易的完整库。你还将看到领域特定语言如何使系统的使用变得简单。

## 什么是外汇交易系统？

外汇交易系统是一种为在外汇市场进行买卖而创建的工具。该系统基于一组用于买卖货币的规则。外汇交易系统通常基于一组信号。所谓信号，是指我们识别并使用的、源自技术分析的特定指标和数值。

每个系统都可以进行配置以更好地管理。我们可以告诉系统如何开仓和平仓，并定义我们想要用于技术分析的工具种类。

在外汇中，有一些常用的特定术语来定义头寸并识别市场中的盈利或亏损。为了开始创建我们的系统，我们必须定义这些词汇及其含义。这将成为我们定义领域特定语言方法和语法的新通用词典（表 9-1）。

表 9-1

外汇交易系统中使用的项目词典

| 术语 | 描述 |
| --- | --- |
| PIP | 价格兴趣点。由汇率中的最后一位数字表示，代表每个价值变动点的成本。例如，欧元/美元 1 点 = 0.0001，用于计算标准汇率，该汇率随 1 点价值的变化而上升或下降。PIP 衡量货币对汇率的变动量。对于显示到小数点后四位的货币对，一个点等于 0.0001。基于日元的货币对是例外，仅显示到小数点后两位（0.01）。 |
| BID | 需求价格；你卖出的价格 |
| ASK | 报价价格；你买入的价格 |
| CARRY TRADE | 持有一个具有正隔夜利息回报的头寸，希望在不平仓的情况下获利；仅利用各国央行的利率差异 |
| BANK RATE | 一国央行向其商业银行放贷的百分比利率 |
| COMMISSION | 经纪商处理操作的手续费 |
| CURRENCY PAIRS | 要买入/卖出的货币对，例如，欧元/美元或欧元/英镑 |
| RATE | 两种货币之间商定的汇率。例如，欧元/美元在某一天的值为 1.500 |
| PRINCIPAL AMOUNT | 有时称为“面值”或“名义金额”，指交易中涉及的货币金额 |
| TIME FRAME | 当前操作的时间 |
| DAY TRADING | 在同一交易日开仓和平仓的交易 |
| LEVERAGE | 开立新头寸时投入的资金。例如，杠杆值为 1000，投入 100 美元，则实际投资额为 1000 × 100 = 1,000,000 美元。杠杆对于确定一个 PIP 的价值很重要。 |
| PROFIT | 平仓后获得的盈利金额 |
| LOSS | 已平仓头寸中亏损的金额 |
| STOP LOSS | 平仓时亏损的 PIP 或金额 |
| TAKE PROFIT | 平仓时盈利的 PIP 或金额 |
| STANDARD ACCOUNT | 账户头寸为 100,000 美元 |
| MINI ACCOUNT | 账户头寸为 10,000 美元 |
| MICRO ACCOUNT | 账户头寸为 1,000 美元 |

利用此表，我们现在可以定义问题并设计我们的领域特定语言。



### 设计 DSL 系统

定义了通用字典后，我们就可以开始设计系统，并了解实现它需要什么。设计系统的第一步是确定我们想要使用的规则。我们知道，DSL 可以用来实现一个使用调用的系统，类似于自然语言，我们现在要做的就是定义这些规则并实现它们。

首先，要设计系统，我们必须了解外汇交易市场是如何运作的。外汇交易市场主要是买卖货币。这是通过特定的平台完成的，这些平台允许客户买卖货币对，例如 EUR/USD（欧元/美元）。盈利或亏损是我们开始持有头寸到平仓时点差（PIP）数量的差值。

我们可以定义止损或止盈，以便在盈利或亏损一定数量的点差时平仓。当我们在外汇交易系统中下单时，它看起来像下面这样：

*   以 10 倍**杠杆**买入 1,000 美元 EUR/USD，设置 10 个点的**止盈**和 10 个点的**止损**

有了可以用来描述我们系统的规则，我们就可以下单买入货币对，定义杠杆、止损和止盈。使用一个命令，我们可以定义系统规则，并基于此定义我们的系统。

我们想要在系统中实现的规则很简单。

*   系统必须允许买入货币。
*   系统必须允许卖出货币。
*   系统必须能够设置止损。
*   系统必须能够设置止盈。
*   系统必须能够定义货币对。
*   系统必须能够定义杠杆。

这些定义的规则允许系统精确地创建我们之前定义的那一行代码。我们现在需要做的是定义 DSL 中涉及的对象。

我们现在可以开始编写定义系统所需的代码。我们需要的第一段代码是用来实现规则的。我们需要一组对象。这些对象可以组合在一起来执行规则。

### 实现系统

第一步是设计定义货币对所需的对象。我们可以定义的第一个对象是货币。货币的代码如下：

```
trait CurrencyType{
def pair: String
}
```

我们用上述代码定义的本质上是定义系统中使用的货币的通用方式。这个 trait 的作用域由一个通用对象定义，我们可以“特化”该对象以用于创建货币对。

接下来我们必须编写的代码是定义货币对所需的代码。

```
class CurrencyPair(val accountType:String) extends CurrencyType{
override def pair(currency_name:String):String ={ currency_name }
def PIP(): Double={
val first_currency=pair("USD")
val second_currency=pair("EUR")
var returnPIP:Double=0.0
if (secondary.toString.equalsIgnoreCase("USD")){
if (accountType.equalsIgnoreCase("MICRO")){
returnPIP=0.10
}
else if (accountType.equalsIgnoreCase("MINI")){
returnPIP=1
}
else if (accountType.equalsIgnoreCase("STANDARD")){
returnPIP=10
}
}
return returnPIP
}
}
```

上述代码展示了我们如何定义一个 `CurrencyPair`，并根据账户类型定义我们获得的点差。现在你可以看到，我们使用 trait 类来定义我们想在系统中使用的货币对类型。

`CurrencyPair` 类允许我们创建货币对，并展示 Scala 的一些特性。代码行 `class CurrencyPair (val accountType:String) extends CurrencyType` 展示了 trait 是如何使用的。我们可以看到，这简单地扩展了类并覆盖了 trait 的方法。这基于接口创建了具体的方法。

然后，该类负责根据账户类型计算每个账户的点差值。根据定义，当次要货币是美元（USD）时，我们可以定义三种账户类型的默认值——微型账户、迷你账户和标准账户——分别是 0.10 美元、1 美元和 10 美元。

这是 `PIP` 方法的结果，它除了确认对象类型并返回点差值之外，不做其他事情。

### 改进基础类

基于之前的代码，我们可以做一些改进。例如，我们可以创建一个新类 `Account` 来移除 `if then` 语句，并编写更好的代码，如下所示：

```
trait AccountType{
def account:Double
}
```

我们做的第一个改进是创建另一个 trait 来定义账户类型。账户可以是三种类型：`MICRO`、`MINI` 或 `STANDARD`。我们可以使用新的 trait 来扩展之前编写的类。在 Scala 中，可以使用关键字 `with` 实现多重继承。这是新的代码。

```
class CurrencyPair(accountType:String) extends CurrencyType with AccountType{
override def pair(currency_name:String):String ={ currency_name }
override def account(account_type:String):Double={
if (account_type.equalsIgnoreCase("MICRO")){
return 0.1
}else if(account_type.equalsIgnoreCase("MINI")){
return 1
}else if(account_type.equalsIgnoreCase("STANDARD")){
return 10
}
}
def PIP(): Double={
val first_currency=pair("EUR")
val second_currency=pair("USD")
val pip=new account("MINI")
return pip
}
}
```

### 创建订单

下一步是创建一个订单。这本质上是交易系统的基本操作。系统应该能够使用基本参数创建订单，所以我们现在来创建这个类。

```
class Order(val pair: CurrencyPair){
def ask(profit:Int,loss:Int ):Unit={
println("Profit:"+profit)
println("Loss:"+loss)
println("PIP:"+pair.PIP)
}
def bid(profit:Int,loss:Int):Uint={
println("Profit:"+profit)
println("Loss:"+loss)
println("PIP:"+pair.PIP)
}
}
```

这个类非常简单。它只有两个方法：一个用于买入，`ask()`，一个用于卖出，`bind()`。两个方法都接受两个参数，`profit` 和 `loss`。

现在我们可以看到，方法的名字使用了字典中定义的名字。这是因为我们想要定义一个可以像自然语言一样阅读的软件。下一步是创建用于下单的代码。为此，我们只需要创建一个名为 `_order` 的变量，语法如下：

```
val _order = new Order(new CurrencyPair("MICRO","EUR","USD")).ask(10,10)
```

这个调用使用了流畅风格。这本质上是一个 DSL 调用，但其语法并不完全适合定义它，因为 DSL 的核心要素之一是我们如何定义 API。到目前为止的代码非常基础，而且有些令人困惑。API 不是很清晰，用它来创建调用也不像我们期望的那样流畅。当然，我们可以像读英语一样阅读它，但一个好的 DSL 应该非常易于阅读和理解。

因此，要拥有一个好的 DSL，我们首先需要设计一个好的 API。这不是一件容易的事，但却是决定是否拥有一个好的 DSL 的关键区别。如果我们看看实际的 DSL，例如 HTML，或者 Chef 中使用的 RubyDSL，我们可以看到一些 API 的清晰而简单的定义。一个具有清晰名称和文档的 API 有助于开发者编写代码。我们现在需要确定的是为什么以及如何编写一个好的 API。



### 为何设计良好的 API 至关重要

API 本质上是软件与外界通信的接口。设计良好的 API 是打造成功软件的关键。

如果我们设计了一个优秀的 API，它就能“自我说明”。如果我们使用恰当的名称或良好的接口来定义它，它就会易于理解且使用简单。设计良好的 API 需要遵循规则并积累经验，因此让我们来明确一些在设计 API 时可以使用的规则。

*   使用简单明了的名称。名称必须能让使用 API 的人理解。因此，如果 API 用于外汇交易，那么方法名称必须与之相关。
*   每个 API 都必须有特定的版本号。版本号是开发者判断软件是否兼容的依据。为 API 设置版本号可以更好地向开发者传达哪些功能可用，哪些不可用。
*   API 必须有良好的文档。良好的文档造就优秀的 API。拥有完善的文档能让使用 API 的开发者清楚地了解 API 能做什么、不能做什么。花时间编写优秀的文档是一项明智的投资。

这三个支柱是设计和维护优秀 API 的基础。当我们编写 DSL 时，本质上是在创建一组用于解决特定问题的 API。遵循这三个支柱，我们可以重写迄今为止创建的软件，使其更具可读性，并更符合 DSL 的效率要求。

运用这三个支柱的方式与用户体验和良好的规划息息相关。实施这些支柱的第一步是规划 API。使用通用词典为规划 API 的名称提供了一个良好的起点。本质上，每个 API 都应是系统的一个功能。在我们的案例中，我们必须有像 `PIP`、`BID`、`ASK` 这样的 API。

使用这些名称有助于我们遵守另一个支柱：文档。我们可以使用通用词典来编写文档。因为我们在通用词典中使用的 API 名称与代码中的一致，所以可以用它来记录 API。本质上，系统必须响应通用词典中描述的特定功能，对于使用 API 来说，这就足够了。

最后需要考虑的是版本管理。当我们创建 API 时，必须理解同一个 API 可能存在不同版本。这可以通过例如创建不同的端点或稍微更改名称（例如更改包名）来管理。

根据我的经验，最好的方法是使用包来管理版本。这是因为每个开发者都能轻松识别自己需要的内容，然后为软件导入正确的包。版本管理允许我们拥有不同版本的软件，但提供统一的响应。版本管理通常用于解决问题或改进软件。

基于此，我们可以确定改进代码的后续步骤。我们需要做的是为 API 创建一个版本，当然，还要改进其接口，因为我们希望将 DSL 提升到新的水平。当我们对 API 进行版本管理时，本质上就是为其创建了一个不同的端点。为此，我们创建一个新的包。例如，如果我们创建了一个结构 `practicalscaladsl/v1/api`，那么第二个版本将是 `practicalscaladsl/v2/api`。

由于我们更改了包，因此为代码创建了两个不同的版本。例如，当我们必须更新软件版本（例如添加某些功能）但又想保留旧 API 时，就会用到这种方法。

## 设计新的 DSL API

为了提高我们最初编写的 DSL 代码第一版的质量，并遵循我们刚刚定义的设计支柱，我们必须更新代码。我们为代码定义一个版本。这有助于管理改进，并创建符合我们已定义通用词典的 API。

创建优秀 API 的第一步是定义一个包。因此，我们对代码所做的第一个修改就是定义包。在 Scala 中，我们可以使用以下代码定义一个包：

```
package com{
package practicalscala{
package v1{
class TradingSystem{
......
}
}
}
}
```

在 Scala 中，我们只需使用保留字 `package` 后跟包名即可定义包。例如，在我们的代码中，我们定义了一个包 `com.practicalscala.v1`。在 Scala 中创建包时，语言并不强制要求创建包的文件夹结构。例如，在 Java 中，则必须创建相同的包结构。

现在我们可以开始重写 `TradingSystem` 类。我们首先要做的是定义货币对，那么让我们先创建 `pair` 方法来实现这一点。

```
package com {
package practicalscala {
package v1 {
class TradingSystem {
protected var _pair: String = ""
def pair(first: String, second: String): this.type  = {
this._pair = s"$first/$second"
this
}
def execute() ={
println(_pair)
}
}
}
}
}
```

你可以看到，`pair` 方法已被添加到 `TradingSystem` 类中。该方法仅创建货币对，没有其他操作，但这让我们了解了如何使用包进行版本管理。要调用该类，我们必须首先导入 `TradingSystem` 类。当然，因为我们仍然使用流畅 API，所以必须执行一个方法来返回该类计算出的值。上述代码的一个有趣特性是字符串插值。在 Scala 中，字符串前的字符 `s` 告诉解释器对其进行插值。这是 Scala 2.10 中新增的功能。通过字符串插值，开发者可以使用 Scala 处理的变量及其结果来构建字符串。例如，当我们希望字符串根据某个变量或操作结果的值而变化时，这非常有用。我们也可以使用操作，在这种情况下使用语法 `${ }` 来定义操作。在我们的例子中，我们使用此功能来创建货币对。代码 `s"$first/$second"` 在运行时对字符串进行插值，并将变量 `first` 和 `second` 替换为方法的参数。

`_pair` 变量在类中共享。使用类变量时，此处将其设置为 `protected`。这意味着包外部的任何方法都不能使用该变量。这对我们来说没问题，因为该包本质上只包含我们想要为 DSL 公开的方法。那么，让我们来使用这个第一版的 API，看看结果。

### 使用第一个 API

编写完第一个 API 后，现在是时候使用它了。主方法如下：

```
import com.practicalscala.v1._
object TestMain{
def main(args:Array[String]):Unit ={
val _pair = new TradingSystem()
println(_pair.pair("USD","EUR").execute())
}
}
```

这段代码非常简单。要导入 API，我们使用这行代码：`import com.practicalscala.v1._`。这与 Java 语法没有显著区别，只是在 Java 中，当我们想要导入一个包中的所有类时，我们使用 `*`（星号）。在 Scala 中，我们使用 `_`（下划线）。其余部分非常简单。我们创建 `TradingSystem` 类并调用 `pair` 方法。这会返回货币对的值。如果我们执行代码，会得到如下结果：

```
USD/EUR
```

接下来我们需要做的是改进 API，并为外汇交易系统定义函数。我们继续用其他方法改进 API，并创建用于下达买单或卖单的方法链。



### 改进 API

我们为交易系统设计的 API 非常基础。必须向系统添加更多操作。首先要做的调整是定义我们想要的账户类型。这用于计算点值（PIP）。相关代码如下：

```
def account(_type:String):this.type = {
this._account_value = (_type: @switch) match {
case "MICRO" => 0.1
case "MINI" => 1
case "STANDARD" => 10
}
this
}
```

这段代码设置了 `_account_value` 的值。这是通过使用 `@switch` 注解实现的。这改变了我们使用模式匹配的方式。为此，我们必须导入包 `scala.annotation.switch`。该注解会将代码编译成 `tableswitch` 或 `lookupswitch`。这提供了更好的性能，因为编译器会在内部将 switch 语句转换为分支表，而不是决策树。

从代码中可以看出，`@switch` 注解允许我们创建非常紧凑的决策代码，并将其与变量关联。我们的目标是匹配账户类型与点值（PIP）的任何差异。点值用于计算每笔交易产生的盈亏。

现在，我们可以按如下方式重写 `main` 方法：

```
object TestMain{
def main(args:Array[String]):Unit ={
val _pair = new TradingSystem()
println(_pair.pair("USD","EUR").account("MINI").execute())
}
}
```

我们可以看到，目前 API 的流畅性更好了。接下来，我们定义一个账户、一个货币对，最后执行。如果现在执行，我们会得到以下结果：

```
USD/EUR
PIP Value:1.0
```

执行结果返回了货币对和点值。现在是时候为我们的系统增加更多复杂性了。

### 添加最后的功能

外汇交易系统需要额外的功能才能完整，所以让我们开始添加更多功能。首先要添加的功能，例如止损或止盈，定义了要投资的资本。

我们添加的第一个功能是止盈。相关代码如下：

```
def take_profit(pip:Int):this.type ={
this._pip_profit = pip
this._max_capital_profit =(this._capital+(this._pip_value * pip))
this
}
```

`take_profit` 函数有一个参数 `pip`。这表示在平仓前我们可以接受亏损的点数。在内部，该函数定义了值 `max_capital_profit`。这用于计算新资本并平仓。

注意

该函数使用 `this.type` 来定义返回类型。这在 Scala 中用于返回方法的类型。因为我们仍然采用流畅的 API 模式，所以必须明确要返回的类型。使用 `this.type` 允许函数在运行时返回类型，而无需预先指定返回类型。这在构建 DSL 时非常有用，特别是当我们想要具有多种返回类型的功能时。

我们可以添加的另一个函数是 `stop_loss`。这用于在亏损过多点数时平仓。该函数如下所示：

```
def stop_loss(pip:Int):this.type  ={
this._pip_lost = pip
this._max_capital_lost = (this._capital-(this._pip_value * pip))
this
}
```

我们可以看到，该函数与 `take_profit` 类似。最终，两者功能相同：它们都继续为系统添加功能。代码的最终版本如下所示：

```
import scala.annotation.switch
import scala.math.BigDecimal
package com {
package practicalscala {
package v1 {
class TradingSystem {
protected var _pair: String = ""
protected var _pip_value: BigDecimal = 0.0
protected var _pip_profit:Int = 0
protected var _pip_lost:Int = 0
protected var _start_position:BigDecimal =0.0
protected var _actual_value:BigDecimal = 0.0
protected var _capital:BigDecimal = 0
protected var _max_capital_lost:BigDecimal = 0
protected var _max_capital_profit:BigDecimal =0
def pair(first: String, second: String): this.type  = {
this._pair = s"$first/$second"
this
}
def account(_type:String):this.type = {
this._pip_value = (_type: @switch) match {
case "MICRO" => 0.1
case "MINI" => 1
case "STANDARD" => 10
}
this
}
def take_profit(pip:Int):this.type ={
this._pip_profit = pip
this._max_capital_profit =(this._capital+(this._pip_value * pip))
this
}
def stop_loss(pip:Int):this.type  ={
this._pip_lost = pip
this._max_capital_lost = (this._capital-(this._pip_value * pip))
this
}
def start_position(start_value:Double):this.type ={
this._start_position=start_value
this
}
def actual_position(actual_value:Double):this.type ={
this._actual_value=actual_value
this
}
def capital(investment:Int):this.type ={
this._capital=investment
this
}
def execute() ={
val _gain=(this._actual_value - this._start_position)
val _check_profit =((_gain * this._pip_value) + _capital)
if(_check_profit > _max_capital_profit){
println("Max Profit gain")
}else if(_check_profit > _max_capital_lost){
println("Stop loss gain")
}
println("PIP gained:"+_gain)
println("Actual Capital:"+_check_profit)
}
}
}
}
}
```

可以看到，已经导入了 `BigDecimal`。这在 Scala 中用于定义货币类型。由于货币可以有更多小数位数，`BigDecimal` 有助于定义这一点。变化最大的函数是 `execute`。这本质上是系统的核心。它计算收益并检查利润。如果值符合指定条件，我们就平仓，无论结果是盈利还是亏损。

现在，让我们看看如何使用外汇交易系统来设置一个同时包含止盈和止损的头寸。

```
import com.practicalscala.v1._
object TestMain{
def main(args:Array[String]):Unit ={
val _pair = new TradingSystem()
println(_pair.pair("USD","EUR")
.account("MINI")
.start_position(1.0455)
.actual_position(1.0554)
.stop_loss(10)
.take_profit(10)
.capital(10000)
.execute())
println(_pair.pair("USD","EUR")
.account("MINI")
.start_position(1.0455)
.actual_position(1.0354)
.stop_loss(10)
.take_profit(10)
.capital(10000)
.execute())
}
}
```

我们创建了一个链式调用来调用方法。我们指定了货币对、账户类型、起始价格（买入货币的价格）、当前价格（更新后的价格）、止损和止盈，最后是资本。

最后，我们调用 `execute` 来确定头寸的结果是盈利还是亏损。当然，最好是将系统连接到实时外汇市场，让它改变价格。在这种情况下，我们就可以实时使用该系统。

当我们执行系统时，会得到如下结果：

```
Max Profit gain
PIP gained:0.0099
Actual Capital:10000.0099
Stop loss gain
PIP gained:-0.0101
Actual Capital:9999.9899
```

系统计算赚取或亏损的点数，并更新资本。然后，根据点数决定需要做什么来平仓。

## 结论

现在你已经了解了如何使用 DSL 来创建交易系统。这并不复杂；我们只需要重新思考日常工作方式。

使用 DSL 的一大优势在于代码的可读性极强，并且非常易于理解。另一个小优势在于函数的复杂性。因为我们定义了方法链，所以可以编写非常简单的函数，并将它们连接起来。这使我们能够编写简单的功能，并与其他功能连接来解决大问题，编写简单的函数，并简化代码本身的维护。维护一个小函数总是比维护一个超过一百行代码的函数更容易。

在本章中，你发现了 Scala 的额外功能，例如 `switch` 注解和 `this.type`，这两者都允许我们编写简单高效的代码，这当然对于编写一个好的 DSL 至关重要。

在下一章中，我将讨论另一个重要主题：游戏。你将看到如何使用 DSL 构建一个视频游戏。



