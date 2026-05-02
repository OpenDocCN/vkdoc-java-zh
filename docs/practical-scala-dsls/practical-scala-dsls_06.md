# 6. 用户识别系统

构建 IT 系统时的一个常见问题是识别用户并为其分配正确的角色。每个系统都有这类功能，通常，我们使用数据库来存储与用户相关的数据。

我们通常的做法是连接到数据库，执行一些查询，然后根据查询结果创建一个解析器来理解用户的角色。

本章的目标是创建一个自由语法解析器，用于建立我们自己的用户识别规则。这需要我们定义一组规则，并将其应用于我们收集到的数据结果。这个解析器可以轻松应用于任何数据源，这意味着我们可以将用户与数据库解耦，并使用 JSON 或 XML 文件来存储用户数据。

我们使用 Scala 解析器库来创建这个解析器。这个库非常适合领域特定语言，因此掌握它的最佳方法是回顾解析器的基础知识，并开始编写一些代码。



## 语法

首先，要使用 Scala 解析器库，回顾语法基础概念和形式语言背后的理论是很有帮助的。语法是一组遵循特定格式的规则，用于定义字符串。

例如，语法包含了用普通英语写句子所遵循的规则。语法也可以用于编写数学表达式，从而确保运算正确执行并得到期望的结果。想象一下，我们想要定义一个基本运算的语法。我们可以为此编写一些规则，例如：

*   每个数字都是一个算术表达式。
*   我们拥有的基本运算符是加法（`+`）、减法（`-`）、除法（`/`）。
*   如果左运算符和右运算符都是算术表达式，我们可以在中间放一个运算符，这样就创建了一个新的算术表达式。

要编写语法，我们可以使用 BNF（巴科斯-瑙尔范式）表示法。在这种情况下，如果我们想用 BNF 创建一个简单的语法，它看起来像这样：

```
digit ::=  "0" | "1" | "2" | "3" | "4" | "5" | "6" | "7" | "8" | "9"
numbers ::= digit | digit numbers
operator ::=  "+" | "-" | "/"
expression ::= numbers | expression operator expression | "(" expression ")"
```

通常，为了定义语法，我们使用 BNF 的改进版，称为 EBNF（扩展巴科斯-瑙尔范式）。使用 EBNF，我们可以定义更紧凑的语法。前面代码示例中定义的就是这种类型的语法。

也可以使用运算符 `?`（0 或 1 次）、`*`（0 次或多次）和 `+`（1 次或多次）。这些运算符用于正则表达式。我们可以使用这些新运算符重新定义语法。

```
digit ::=  "0" | "1" | "2" | "3" | "4" | "5" | "6" | "7" | "8" | "9"
numbers ::= digit | digit numbers
expression ::= term ( ("+" | "-") expression )
term ::= operation ( "\" operation )*
operation ::= number | "(" expression ")"
```

使用 EBNF，我们可以编写例如 `(3+5)*6` 这样的表达式。这个语法对我们的 EBNF 来说是完全有效的。通过 EBNF，我们本质上定义了一个语法来定义我们的“句子”。在我们的例子中，我们定义了一个新的运算。

### Scala 解析器组合子库

Scala 拥有一个非常强大的库用于创建解析器。这个库定义了多种解析器，我们可以用它们来编写自己的解析器。

所谓“解析器组合子”，是指一种语言将不同解析器组合起来创建单一解析器的能力。而“解析”则是指语言将输入字符串转换为其他内容的能力。

当我们创建一个解析器时，我们识别出两个基本元素：元素和解析器。每个解析器必须定义一个以上的解析器元素，用于“组合”以解析字符串。

表 6-1 列出了 Scala 中一些基本的辅助解析器。

表 6-1

Scala 基本辅助解析器

| 解析器类型 | 操作 |
| --- | --- |
| Parser1 ~ Parser2 | 顺序组合：使用此辅助解析器告诉解析器应按顺序检查输入字符串中的 Parser2 和 Parser1 的值。这意味着当我们使用此辅助解析器时，Scala 中的解析器组合子会创建一个由解析器相关部分生成的对象序列。 |
| Parser1 &#124; Parser2 | 选择：检查 Parser1 和 Parser2 两个值是否存在，优先选择 Parser1 的值。这意味着如果它找到了 Parser1 的值，就不再检查另一个值。假设我们写了类似 `0 &#124; 9` 这样的代码。这意味着解析器应该只检查值 `0` 或 `9`。解析器不应检查所有其他值。这可以类比为逻辑运算 `OR`。 |
| Parser1.? | 可选：检查 Parser1 或什么都不检查。 |
| Parser1.* | 重复：检查 Parser1 的每一次出现。如果出现多次，Parser1 返回出现的次数。 |
| … | 字面量：解析一个字面量表达式。 |
| R | 正则表达式：使用正则表达式创建解析器。创建一个正则表达式，解析方括号 `[` 和引号之间的每个值。 |
| Parser1 <~ Parser2, Parser1 ~> Parser2 | 顺序组合：执行顺序组合，从右到左或从左到右。这意味着当我们有解析器时，我们从 Parser1 开始，然后移动到 Parser2。两个值都会被评估，两个解析器的结果就是我们得到的响应。 |
| Opt (Parser1 &#124; Parser2) | 可选：创建一个可选解析器，意味着我们可以在两个值之间进行选择。当我们需要对输入字符串的值做出选择时使用。 |
| Rep (Parser1) | 重复：创建一个解析器重复，返回检测到的所有出现的列表。 |
| Repseq (Parser1, separator) | 分隔重复：类似于重复解析器，但在此情况下，我们指定了用于分隔的分隔符。例如，我们可以使用逗号“,”来表示分隔值。 |
| Parser1 ^ ^ f | 函数组合子：使用解析器的输出值创建一个函数。 |

每个解析器都会返回一个值：如果解析操作正常结束，则返回成功；如果操作以错误结束，则返回失败。可以从 `ParserResult` 对象中读取这个值。我们可以使用这个对象来准确了解解析操作中哪里出了问题。

### 一个简单的解析器示例

到目前为止，我只讨论了如何创建一个解析器组合子。我们现在要做的是创建一个简单的解析器来执行一些基本的数学运算。

解析库定义了一些基本操作，我们可以组合这些操作来创建更复杂的操作。库中的基本操作包括：

*   匹配标记
*   选择运算符（`|`）
*   重复操作（`rep`）
*   操作的可选性。我们可以从一组选择中应用一个操作（`opt`）。
*   按顺序执行两个操作（`~`）。

我们现在要做的是使用之前的语法来创建一个微型数学解析器。代码如下：

```
import scala.util.parsing.combinator._
class ExprParser extends RegexParsers {
val digit  = "[0-9]+".r
def expression: Parser[Int] = term ~ opt(("+" | "-" ) ~ expression) ^^ {
case t ~ None => t
case t ~ Some("+" ~ e) => t + e
case t ~ Some("-" ~ e) => t - e
}
def term: Parser[Int] = factor ~ rep("/"  ~> factor ) ^^ {
case f ~ r => f / r.product
}
def factor: Parser[Int] = digit  ^^ { _.toInt } | "(" ~> expression <~ ")"
}
```

我们可以看到，代码创建了一个类，该类扩展了 `Parsers` 特质的子特质 `RegexParsers`。将 EBNF“翻译”成代码的第一步是定义 `digit`。我们通过使用一个简单的正则表达式来定义它。之后，我们开始定义解析器的每个组件。在这个例子中，我们使用 `Int` 类型。因此，每个函数都返回 `Parser[Int]` 类型。

正如你所见，用 Scala 创建解析器非常简单。函数 `expression` 和 `term` 都使用了一个 case 类来确定必须执行哪种操作。例如，`expression` 检查表达式中是否存在 `+` 或 `-` 值，并执行相应的操作。现在我们看到，我们使用了库中存在的基本运算符来构建我们的操作。

要执行解析器，我们必须使用 `parseAll` 命令。此方法会执行每个解析器，直到字符串结束。我们可以使用以下调用来执行程序：

```
object Main extends App {
val parser = new ExprParser
val result = parser.parseAll(parser.expression, "10/2+5")
if (result.successful) println(result.get)
if(!result.successful) println("failure")
}
```

注意

我们可以使用函数 `parse` 来代替 `parseAll`，但这并不是真正实用的。函数 `parse` 不会读取所有行，而是在找到第一个匹配项时停止。例如，在我们的表达式 `10/2+5` 中，函数 `parse` 会在执行命令 `10/2` 后退出。



## 定义领域问题与语法

众所周知，DSL 的存在是为了解决特定的领域问题。在我们的案例中，我们希望定义一种语言，用于描述如何创建或维护现有用户。

我们可以定义一些简单的规则来创建我们的语言。

*   管理员可以创建、删除和更新用户。
*   管理员可以为一个或多个用户分配和/或移除角色。
*   用户可以根据规则中定义的限制来使用系统。
*   规则是“读取”和“写入”。
*   我们想要使用的语法应类似于 JSON。

基于这些规则，我们可以定义如下的 EBNF：

```
value ::= objson | array | string | floatingPointNumber.
object ::= "{" | members | "}".
array ::= "[" | values | "]".
members ::= member {","  member }.
member ::= stringLitteral ":" value.
values ::= value {"," value}.
```

该语法展示了在系统中定义新用户的规则。我们使用简单的 JSON 来定义用户。这是因为 JSON 对人类更易读，并且便于定义更复杂的规则。基于此语法，我们现在为解析器定义一个简单的输入。

```
{
"Username" : [
{
"Name"  : "Pierluigi Riti",
"Roles" : ["Administrator", "User"],
"Groups" : ["Test1","Test2"],
"Permissions": ["All", "Read"]
},
{
"name" : "John Smyth",
"Roles" : ["User"],
"Groups" : ["Test1"],
"Permissions": ["Read"]
}
]
}
```

使用上述规则，我们定义了两个新用户。可以看到，我们简单地定义了角色，并使用语法定义了用户的每个方面。

### 准备解析器

创建解析器的第一步，本质上是识别工作输入。为此，我们可以创建一个简单的词法解析器。

词法解析器是一种专门用于识别输入词法结构的解析器。在我们的案例中，我们使用此解析器来识别单词，按分隔符拆分，并创建解析树，以便在软件功能中发起调用。那么，开始创建词法解析器的第一个版本，如下所示：

```
import scala.util.parsing.combinator._
class ParserJson extends JavaTokenParsers {
def value : Parser[Any] = obj | array |
stringLiteral |
floatingPointNumber
def objson: Parser[Any] = "{" ~repsep(member, ",")~"}"
def array : Parser[Any] = "[" ~repsep(value, ",")~"]"
def member: Parser[Any] = stringLiteral~":"~value
}
```

我们可以执行解析器并查看软件的响应。然后，我们为调用创建软件，并使用我们的 JSON 输入文件。

这是我们使用的文件：

```
{
"Username" : [
{
"Name"  : "Pierluigi Riti",
"Roles" : ["Administrator", "User"],
"Groups" : ["Test1","Test2"],
"Permissions": ["All", "Read"]
},
{
"Name" : "John Smyth",
"Roles" : ["User"],
"Groups" : ["Test1"],
"Permissions": ["Read"]
}
]
}
```

这是调用解析器的代码：

```
object SimpleJSONParser extends ParserJson {
def main(args: Array[String]): Unit ={
val reader = Source.fromFile(args[0]).getLines.mkString
println(parseAll(value, reader))
}
```

如果我们执行代码，会看到解析器的结果。

```
parsed: (({~List((("Username"~:)~(([~List((({~List((("Name"~:)~"Pierluigi Riti"), (("Roles"~:)~(([~List("Administrator", "User"))~])), (("Groups"~:)~(([~List("Test1", "Test2"))~])), (("Permissions"~:)~(([~List("All", "Read"))~]))))~}), (({~List((("Name"~:)~"John Smyth"), (("Roles"~:)~(([~List("User"))~])), (("Groups"~:)~(([~List("Test1"))~])), (("Permissions"~:)~(([~List("Read"))~]))))~})))~]))))~})
```

### 描述解析器

我们现在要做的是描述解析器，并看看它的作用。乍一看，解析结果对人类阅读来说并不是很有用。它本质上只是一系列列表和 `~` 符号的序列。这并非人类可读，但无疑对计算机来说更易读。

Scala 解析器有一些规则，我们需要先了解这些规则，才能将输出转换为对计算机更有用的内容。这些规则是：

*   每个写成字符串的解析器都会返回解析后的字符串。
*   正则表达式也返回一个字符串。
*   当我们有顺序组合 P~Q 时，它会返回 P 和 Q 两者的结果。这些结果会以 case class 实例的形式返回。
*   当我们有选择组合 P|Q 时，解析器会返回 P 或 Q 中成功匹配的那个元素的结果。
*   当解析器使用重复 rep(P) 或 repsep(P, separator) 时，它会返回一个列表，其中包含 P 的所有运行结果。
*   当解析器运行可选 opt(P) 时，它会返回一个 Scala 的 Option 类型实例。如果成功，返回 Some(P)；如果 P 失败，则返回 None。

使用这些规则，我们现在可以理解为什么解析器的结果是我们看到的样子了。无论如何，这个输出并不是很有用。下一步是改进解析器，以获得一个我们可以用来构建自己调用的结构。



### 改进 JSON 解析器

为了让解析器的结果更易于人类阅读，我们需要对解析器的结果稍作修改。我们可以使用的最有用的结构是简单的 Scala Map，其中键是 JSON 属性的名称，值是关联的值。

解析器的第一步现在是在解析整个 JSON 对象时创建一个 Map。该函数的新代码如下所示：

```
def objson: Parser[Map[String, Any]] =
"{"~> repsep(member, ",") <~"}" ^^ (Map() ++ _)
```

你可以看到我们修改了方法的签名。首先，我们使用了值 `Any`。这意味着我们从解析器返回任意值。在这种情况下，解析器返回一个 `Map[String, Any]` 类型的值。这告诉解析器将结果转换为一个键为 `string`、值为 `any` 的映射。

为了实现这种转换，我们使用了一个新的解析器操作符：`^^`。这个操作符转换解析操作的结果。要使用这个操作符，解析器必须具有 `P ^^ r` 的语法。在我们的例子中，我们对 member 使用了重复控制，这意味着我们有一个在 case class 中转换的结果，并且这启动了转换。

同时，我们修改了另一个方法来改进解析器。我们修改了 member，如下所示：

```
def member: Parser[(String, Any)] =
stringLiteral~":"~value ^^
{ case Name~":"~value => (Name, value) }
```

对于另一个方法，我们使用操作符 `^^` 来转换操作的结果。在这种情况下，我们使用模式匹配来选择值 `Name`。整个解析器代码现在看起来像这样：

```
import scala.util.parsing.combinator._
class ImprovedJsonParser extends JavaTokenParsers {
def obj: Parser[Map[String, Any]] =
"{"~> repsep(member, ",")  repsep(value, ",")  (name, value) }
def value: Parser[Any] = (
obj
| array
| stringLiteral
| floatingPointNumber ^^ (_.toDouble)
)
}
object ImprovedJsonParserTest extends ImprovedJsonParser {
def main(args: Array[String]) {
val reader = "{\n\t\"Username\" : " +
"[{\"Name\"  : \"Pierluigi Riti\"," +
"\"Roles\" : [\"Administrator\", \"User\"]," +
"\"Groups\" : [\"Test1\",\"Test2\"]," +
"\"Permissions\": [\"All\", \"Read\"]" +
"}," +
"{\"Name\" : \"John Smyth\"," +
"\"Roles\" : [\"User\"]," +
"\"Groups\" : [\"Test1\"]," +
"\"Permissions\": [\"Read\"]}]} "
println(parseAll(value, reader))
}
}
```

如果我们执行这段代码，实际上会得到如下结果：

```
Map(
"Username" -> List(
Map(
"Name" -> "Pierluigi Riti", "Roles" -> List("Administrator", "User"),
"Groups" -> List("Test1", "Test2"),
"Permissions" -> List("All", "Read")
),
Map(
"Name" -> "John Smyth",
"Roles" -> List("User"),
"Groups" -> List("Test1"),
"Permissions" -> List("Read")
)
)
)
```

我们可以看到，结果现在比以前的结果更易于人类阅读。我们最终意识到，我们自己的解析器正在使用外部 DSL 来定义它。当我们创建外部 DSL 时，我们有一组可用于生成解析器的模式。例如，我们有一个解析器生成器模式，这正是我们在这里使用的。这种模式使用外部语法来生成语言。外部 DSL 本质上生成用作外部源的代码。在这种情况下，我们使用一个 JSON 文件。JSON 文件为我们提供了读取和解析以生成代码所需的语法。

我们可以使用不同的技术来解析文件。在这种情况下，我使用了简短的介绍和一个 JSON，但我们可以使用 DSL 创建更复杂的解析器。例如，我们可以创建一个语法导向的翻译。这种模式通过定义语法，然后使用该语法创建结构化的翻译来翻译源（通常是文本）。例如，当我们将 EBNF 翻译成软件时，我们就看到了这种模式的使用。我们采用一种语法（EBNF），然后基于该语法生成输入。软件获取语法，然后将源翻译成软件。

这种解析使用 JSON 格式表示法，这允许我们创建自己的语法。这是因为很容易解析并为我们想要定义的软件创建自己的语法。

到目前为止，我们学到的知识足以开始创建我们自己的简单解析器，但我们需要知道我们使用了哪些元素来创建解析器（见表 6-2）。

表 6-2

用于创建简单 JSON 解析器的解析器操作符

| 操作符 | 描述 |
| --- | --- |
| … | 字面量 |
| “…”.r | 正则表达式 |
| P~Q | 组合序列 |
| P<~Q , P~>Q | 组合序列：仅向左/右移动 |
| P &#124; Q | 选择：结果为 P 或 Q |
| opt(P) | 可选：成功时返回 P |
| rep(P) | 对 P 重复解析 |
| repsep(P,Q) | 交错重复 |
| P ^^ f | 结果转换 |

在上表中，我们看到许多组合子解析器使用符号名称来描述操作，例如 `<~` 或 `^^`。

这种表示法的巨大优点是编写紧凑，但另一方面，它难以记忆，并且对于经验不足的人来说可能非常晦涩。使用符号名称的一大优点是代码简短，并且可以在解析器本身中实现正确的优先级。

到目前为止，我们创建的是一个内存解析器。我们可以使用这个解析器来开发一系列方法调用，以在系统上执行操作。例如，我们可以创建该函数并使用它来更新数据库或文件系统中的表。

## 结论

在本章中，你简要了解了 Scala 的解析器组合子库。这个库非常强大，可用于创建同样强大的代码。

我们仅仅触及了解析器组合子库的表面，但尽管如此，我们仍然能够创建一个强大的解析器，用于读取并在内存中创建另外三个具有代码结构的解析器。

在下一章中，当我介绍 Scala 解析组合子库的其余部分时，你将看到如何创建更复杂的解析器。

