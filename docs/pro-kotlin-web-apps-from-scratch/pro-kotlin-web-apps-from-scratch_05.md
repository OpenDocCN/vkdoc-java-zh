# 4. 将 Web 处理器与特定库解耦

随着应用的增长，你将拥有数百甚至数千个独立的 Web 处理器，用于处理 HTTP 动词和 URL 路径的不同组合。这些处理器的目的是运行不同的业务逻辑。在本章中，你将学习如何将业务逻辑和 Web 处理器与 Ktor 解耦，以便你可以在任何上下文中使用它们。

在后续章节中，你将在许多不同的环境中运行你的 Web 处理器，而不仅仅是 Ktor。将 Web 处理器与 Ktor 解耦是实现这一目标的必要条件。例如，你将在第 10 章中看到如何从无服务器环境运行你在 Ktor 中使用的相同 Web 处理器。

解耦的实现本身也是一个很好的机会，可以让你学习一些关于 Kotlin 语言的重要概念。

不过请注意，在实际的 Web 应用中实现这种解耦时，我会非常谨慎。它会引入复杂性和抽象层。你的实际 Web 应用不太可能需要同时支持多个库和/或部署场景。所以请谨慎行事，并做出明智的判断。

如果你是 Kotlin 新手，以下是一些你将在本章中看到示例的语言特性：

*   具有抽象属性和函数的密封类

*   函数重载

*   使用映射和列表的函数式数据驱动编程

*   复制数据类

*   不可变性

*   解构声明

*   函数类型

*   扩展函数

同样在本章中，你将学习以下关于在 Web 应用中将 Web 处理器与特定库解耦的内容：

*   创建抽象并将其连接到库

*   为什么你应该考虑*不*在实际的 Web 应用中解耦你的 Web 处理器

除了在第 12 章中学习如何在无服务器环境中部署 Web 处理器之外，附录 A 还将教你如何用 Jersey 替换 Ktor。

## 你自己的 HTTP 响应抽象

到目前为止，你都是通过直接调用 Ktor 的 `call` API 上的方法来响应 HTTP 请求。为了与 Ktor 解耦，你需要创建自己的抽象，该抽象对 Ktor 和 `call` 一无所知。你将更新所有 Web 处理器以使用你自己的抽象，使它们独立并与 Ktor 解耦。

### 表示 HTTP 响应

Ktor 拥有丰富的 API 来表示 HTTP 响应。你自己的抽象也需要能够表示任何 HTTP 响应，就像 Ktor 一样。幸运的是，你可以将 HTTP 响应简化为一个简单的结构：状态码、一些标头和响应体。

你可以使用一个简单的数据类来表示 HTTP 响应数据：

```
data class WebResponse(
val statusCode: Int,
val headers: Map>,
val body: Any
)
```

你将在本章中大量扩展这个数据类。例如，类型 `Any` 可以工作，并且可以用来表示任何（双关语）HTTP 响应体。但它阻止了类型系统对 HTTP 响应内容有任何（也是双关语）了解。Kotlin 拥有丰富而强大的类型系统，因此你应该尽可能避免使用类型 `Any`。

相反，你可以修改 `WebResponse`，利用这样一个事实：你可以对所有 HTTP 响应以相同的方式表示状态码和标头。你可以将 `WebResponse` 更新为一个顶级类，该类对响应体的类型或内容一无所知。然后，`WebResponse` 的子类可以实现你想要支持的不同响应体类型。

### 多种响应类型

为了支持多种响应类型，顶级的 `WebResponse` 将是一个*密封类*。密封类是一个*抽象*类，意味着你不能直接创建它的实例，并且它可以包含所有子类必须实现的抽象属性。它也是*密封*的，这意味着 Kotlin 类型系统在编译时就知道所有已定义的子类。这使得 Kotlin 可以将密封类视为枚举，这样你就可以在编译时获得帮助，例如确保在之后编写将 `WebResponse` 映射到 Ktor 的代码时，不会忘记处理 `WebResponse` 的所有子类。

如果你想了解更多关于密封类的信息，Kotlin 文档有一个专门介绍密封类的页面（[`https://kotlinlang.org/docs/sealed-classes.xhtml`](https://kotlinlang.org/docs/sealed-classes.xhtml)）。

在本章中，你将编写两个子类：`TextWebResponse` 和 `JsonWebResponse`。清单 4-1 展示了这两个子类，以及一个没有 `body` 属性的、作为密封类的 `WebResponse` 新实现。

```
sealed class WebResponse {
abstract val statusCode: Int
abstract val headers: Map>
}
data class TextWebResponse(
val body: String,
override val statusCode: Int = 200,
override val headers: Map> = mapOf()
) : WebResponse()
data class JsonWebResponse(
val body: Any?,
override val statusCode: Int = 200,
override val headers: Map> = mapOf()
) : WebResponse()
清单 4-1
两个具体类，TextWebResponse 和 JsonWebResponse，你将用它们来表示 HTTP 响应
```

你需要一些样板代码才能使其工作。Kotlin 中的数据类并不“智能”，因此你必须指定可以传递给它们的所有属性——你不能通过类继承来设置它们。你需要设置默认的状态码 200，以及默认的空标头列表。

`TextWebResponse` 定义了一个类型为 `String` 的 body 属性。当你创建基于文本的 HTTP 响应时，你真正需要定义的就是内容是一个任意的文本字符串，仅此而已。

`JsonWebResponse` 定义了一个类型为 `Any?` 的 body 属性。你可以增强它，创建像 `JsonMapWebResponse` 和 `JsonListWebResponse` 这样具有更具体类型的类。但归根结底，大多数 JSON 序列化库不会在类型系统中编码 JSON 值类型，而是将输入编码为 `Object!` 或 `Any?`，如果你传递给它一个无法进行 JSON 编码的值，则会在运行时抛出错误。因此，定义 JSON 体的确切值类型不会给你带来任何实际好处。

创建响应类的实例就像创建任何数据类的实例一样。清单 4-2 展示了一些如何创建 `WebResponse` 实例的示例。

```
TextWebResponse("Hello, World!")
TextWebResponse("Oh noes", statusCode = 400)
JsonWebResponse(mapOf("foo" to "bar"))
JsonWebResponse(listOf(1, 2, 3), headers = mapOf(
"Set-Cookie" to listOf("myCookie=123abc")
))
清单 4-2
TextWebResponse 和 JsonWebResponse 类的有效响应类型
```

你现在已经具备了在 Web 处理器中表示 HTTP 响应的基础知识，并且与 Ktor 没有任何耦合。



### 便捷的标头函数

你目前为构建 HTTP 响应所创建的 API 使用起来有些繁琐。为了符合 HTTP 规范，你将标头值编码为 `List<String>`，而不是普通的 `String`。虽然同一个标头很少需要多个值，但 HTTP 规范允许这样做，并且在某些情况下也很实用，例如响应多个 `Set-Cookie` 标头时。

你可以选择将标头表示为 `String` 而不是 `List<String>`，这样就不必像清单 4-2 中那样，为了创建列表而将所有标头值包裹在 `listOf` 中。但你也可以更新 `WebResponse`，使其更方便地处理多值标头。

为此，你可以在 `WebResponse` 中添加两个用于追加标头值的方法：

```
sealed class WebResponse {
fun header(headerName: String, headerValue: String) =
...
fun header(headerName: String, headerValue: List) =
...
}
```

这是*函数重载*的一个例子。Kotlin 允许你编写多个同名函数的不同实现。Kotlin 会根据你传递给函数的参数来决定运行哪个函数。在这个例子中，它们的区别在于 `headerValue` 可以是字符串或字符串列表。这两个方法都会将标头追加到响应中。它们唯一的区别在于一个接受单个标头，另一个接受标头列表，从而方便地覆盖了两种使用场景。

`header(String, String)` 的实现很直接——它只是委托给 `header(String, List<String>)` 函数，并将你提供的标头包裹在一个列表中：

```
fun header(headerName: String, headerValue: String) =
header(headerName, listOf(headerValue))
```

乍一看，这可能会像一个无限循环，因为 `header` 函数除了调用自身外什么都没做。但不同的参数类型（单个字符串 vs. 字符串列表）意味着它们是两个不同的函数，因此不会发生无限循环。

承担主要工作的代码将位于 `header(String, List<String>)` 中。它需要做两件事：`WebResponse` 的所有属性都是不可变的，因此我们必须创建一个带有新值的 `WebResponse` 新实例。此外，`headers` 映射也是不可变的，因此我们需要创建一个新的 `headers` 值，并追加新的标头列表。

要创建 `WebResponse` 的新实例，我们可以利用所有数据类内置的 `copy` 函数。`WebResponse` 类本身不是数据类，因此它没有内置的 `copy` 函数。但 `WebResponse` 是实现 `header(String, List<String>)` 的类，因此需要访问一个能够创建新副本的方法。你可以通过在 `WebResponse` 上使用一个抽象函数来实现这一点，该函数没有实现，但会返回一个新的 `WebResponse` 实例，并将实现委托给子类：

```
sealed class WebResponse {
abstract fun copyResponse(
statusCode: Int,
headers: Map>)
: WebResponse
}
```

现在，`TextWebResponse` 和 `JsonWebResponse` 会出现编译错误，因为它们没有实现新的抽象函数。清单 4-3 展示了 `TextWebResponse` 的完整实现，其中通过调用数据类内置的 `copy` 函数来实现 `copyResponse`。

```
data class TextWebResponse(
val body: String,
override val statusCode: Int = 200,
override val headers: Map> = mapOf()
) : WebResponse() {
override fun copyResponse(
statusCode: Int,
headers: Map>
) =
copy(body, statusCode, headers)
}
清单 4-3
实现了来自 WebResponse 的抽象函数 copyResponse 的 TextWebResponse 实现
```

`copy` 的参数顺序与数据类构造函数中的参数顺序相同。如果你愿意，也可以为参数命名，例如 `copy(body = body, statusCode = statusCode, ...)`。但省略名称可以节省一些输入。而且 `copy` 的实现就在源代码中构造函数的旁边，因此只需查看那里的代码就能轻松找到参数的实际顺序。

剩下的就是在 `WebResponse` 中创建 `header(String, List<String>)` 的实际实现：

```
sealed class WebResponse {
fun header(headerName: String, headerValue: List) =
copyResponse(
statusCode,
headers.plus(Pair(
headerName,
headers.getOrDefault(headerName, listOf())
.plus(headerValue)
))
)
}
```

这个实现调用了我们之前创建的 `copyResponse` 函数。它原样传递了 `statusCode`。对于标头，它需要处理 `headerName` 的重复情况。如果我们传递一个已存在于 `headers` 中的 `headerName`，`plus` 会用新值替换任何现有值。因此，我们向 `headers` 查询 `headerName` 的现有值，如果没有则返回一个空列表，然后将标头列表追加到这个列表中，而不是完全替换它。

现在你已经拥有了创建完整实现所需的一切。清单 4-4 展示了 `header(String, String)`、`header(String, List<String>)` 以及用于在底层创建新实例的 `copyResponse` 功能的完整实现。

```
sealed class WebResponse {
abstract val statusCode: Int
abstract val headers: Map>
abstract fun copyResponse(
statusCode: Int,
headers: Map>)
: WebResponse
fun header(headerName: String, headerValue: String) =
header(headerName, listOf(headerValue))
fun header(headerName: String, headerValue: List) =
copyResponse(
statusCode,
headers.plus(Pair(
headerName,
headers.getOrDefault(headerName, listOf())
.plus(headerValue)
))
)
}
data class TextWebResponse(
val body: String,
override val statusCode: Int = 200,
override val headers: Map> = mapOf()
) : WebResponse() {
override fun copyResponse(
statusCode: Int,
headers: Map>
) =
copy(body, statusCode, headers)
}
data class JsonWebResponse(
val body: Any?,
override val statusCode: Int = 200,
override val headers: Map> = mapOf()
) : WebResponse() {
override fun copyResponse(
statusCode: Int,
headers: Map>
) =
copy(body, statusCode, headers)
}
清单 4-4
WebResponse、TextWebResponse 和 JsonWebResponse 的完整实现
```

现在你可以使用这段代码，通过便捷的函数来追加标头，而无需手动为标头创建映射和列表：

```
// 之前
TextWebResponse("Hello, World!", headers = mapof(
"X-Whatever" to listOf("someValue")
))
JsonWebResponse(listOf(1, 2, 3), headers = mapOf(
"Set-Cookie" to listOf(
"myCookie=123abc",
"myOtherCookie=456def"
)
))
// 之后
TextWebResponse("Hello, World!")
.header("X-Whatever", "someValue")
JsonWebResponse(listOf(1, 2, 3))
.header("Set-Cookie", "myCookie=123abc")
.header("Set-Cookie", "myOtherCookie=456def")
```

`WebResponse` 的实现会在每次调用时追加标头值，因此你可以选择多次调用 `header(String, String)`，或者单次（或多次）调用 `header(String, List<String>)`。



### 不区分大小写的标头

为了让标头更易于使用，你可以更新 `WebResponse`，使其以不区分大小写的方式处理标头。具体来说，这允许你添加 `"``set-cookie``"` 和 `"``Set-Cookie``"`，并将它们合并到同一个标头值中，即使一个是全小写，另一个是驼峰式大小写。

你可以通过保持现有代码不变，并在 `headers` 映射中以多种大小写形式存储标头来实现这一点。然后，你可以添加一个新函数，创建一个所有标头都转换为小写版本的标头。

`headers` 映射只是数据，而 Kotlin 拥有大量用于转换数据的工具（通常称为函数式编程或面向数据编程），因此你将使用这些工具来实现这个新函数。

第一步是将标头映射转换为键值对列表，并将键转换为小写：

```
headers
.map { it.key.lowercase() to it.value }
```

对 `map` 的调用将类型为 `Map<String, List<String>>` 的映射

```
mapOf(
"foo" to listOf("bar"),
"Foo" to listOf("baz")
)
```

转换为类型为 `List<Pair<String, List<String>>>` 的列表：

```
listOf(
"foo" to listOf("bar"),
"foo" to listOf("baz")
)
```

请注意这里大小写的细微差别。原始映射包含键 `"foo"`（小写）和 `"Foo"`（大写），Kotlin 认为它们是两个不同的键。但你会将键转换为小写，使它们都变成 `"foo"`（小写），而 Kotlin 映射不能为同一个键包含两个不同的值。但是，在映射数据结构上调用 `map` 函数会将其转换为 `Pair` 列表。而列表可以包含重复项。因此，最终的列表将包含多个具有相同小写键 `"foo"` 的键值对。

```
sealed class WebResponse {
fun headers(): Map<String, List<String>> =
headers
.map { it.key.lowercase() to it.value }
.fold(mapOf()) { res, (k, v) ->
res.plus(Pair(
k,
res.getOrDefault(k, listOf()).plus(v)
))
}
}
代码清单 4-5
WebResponse 上的 headers() 函数，所有标头名称均为小写
```

代码清单 4-5 中的 `headers()` 函数返回一个新的映射，其中所有标头名称均为小写。与代码清单 4-4 中用于添加新标头值的函数 `header(String, List<String>)` 一样，代码清单 4-5 中的 `headers()` 函数使用 `getOrDefault` 来合并那些名称在转换为小写后相同的标头的值，这样你就不会最终用 `"Foo"`（大写）的值替换 `"foo"`（小写）的值。

### fold 函数

请坐好，深呼吸，清空你的思绪，因为我将尝试完成一件不可能的事：解释函数式编程的工作原理。

`fold` 是函数式编程中所有列表转换的核心。你可以使用 `fold` 来实现其他函数式转换，例如 `map`、`filter`、`forEach`、`max`、`take` 等。这使得 `fold` 在列表转换需要一些额外规则，而不仅仅是简单的 `map` 或 `filter` 时非常有用。

在代码清单 4-5 的 `WebResponse` 中 `headers()` 的实现里，`fold` 作用于一个列表，并传入一个初始值 `mapOf()`（一个空映射）和一个 lambda。然后，`fold` 调用该 lambda，并传入你提供的初始值 `res` 和列表中的第一个值 `(k, v)`。接着，`fold` 会再次调用该 lambda，传入 lambda 第一次被 `fold` 调用时返回的值以及列表中的第二个值。然后，这个过程会针对列表中的每个项目重复进行，对于列表中的每个后续项目，都会调用该 lambda，并传入 lambda 上一次被 `fold` 调用时返回的值以及列表项目本身。

初始的空映射是一个*累加器*。你从初始值开始，每次 `fold` 调用 lambda 时，都会将新值累加到其中。`fold` 不关心累加器的值类型。它可以是列表、映射、数字，或者任何你可能想要累加的东西。

### 解构声明

`(k, v)` 是一个*解构声明*。Kotlin 中的许多实体，例如数据类和 `Pair`，都实现了 `componentN()` 函数。你可以通过 `it.key` 访问 `Pair` 的键，也可以通过 `it.component1()` 访问。类似地，你可以通过 `it.value` 访问 `Pair` 的值，也可以通过 `it.component2()` 访问。对于所有实现了 `componentN()` 函数的实体，你都可以对它们进行*解构*。这是一种直接引用实体组件的便捷方式，无需为它们创建显式的命名变量。代码清单 4-6 展示了解构和显式变量在功能上是相同的。

```
val it = Pair("foo" to "bar")
// 使用解构
val (k, v) = it
// 使用普通函数
val k = it.key
val v = it.value
// 使用手动变量声明
val k = it.component1()
val v = it.component2()
代码清单 4-6
比较解构语法与手动变量赋值
```

解构声明可以节省一些手动输入，并且在解构传递给函数或 lambda 的参数时特别有用，如代码清单 4-5 所示。

## 连接到 Ktor

你现在拥有了一个完整的、独立的 HTTP 响应表示，它不依赖于 Ktor 或任何其他库。但是，你的 Web 应用确实使用了 Ktor。因此，你需要将两者结合起来，让 Ktor 处理你的 `WebResponse` 类。

### Ktor 路由映射

第一步是让 Ktor 路由（例如 `get("/")` 和 `post("/products")`）能够与你自己的代码返回的 `WebResponse` 实例进行交互。

为了查看结果应该是什么样子，代码清单 4-7 比较了你当前处理路由的方式与完成 Ktor 到 `WebResponse` 的接线后的样子。

```
// 直接调用 Ktor API
get("/") {
call.respondText("Hello, World!")
}
// 连接 Ktor 和 WebResponse 的最终结果
get("/", webResponse { req ->
TextWebResponse("Hello, World!")
})
代码清单 4-7
当前 Ktor API 与期望结果的比较
```

代码清单 4-7 中的两个示例有两个主要区别。首先，在处理程序中不再直接与 Ktor 的 `call` API 交互。其次，Ktor 的 `call` API 是命令式的，你需要按顺序命令式地调用其方法。而 `WebResponse` API 本质上是函数式的，这意味着唯一的交互点是从你自己的处理程序返回的不可变值 `WebResponse`，没有命令式的方法调用。

第一步是创建一个函数，该函数创建并返回一个新的 lambda。Ktor 中的函数 `get` 具有（简化后的）签名 `get(path: String, body: ApplicationCall.() -> Unit)`。换句话说，第二个参数是 `ApplicationCall` 上的一个*扩展函数*，没有返回值。



### 扩展函数

第一步是创建清单 4-7 中所示的 `webResponse` 函数。`webResponse` 的返回值作为第二个参数传递给 `get`，并且需要匹配该类型签名。`get` 的类型签名为 `get(path: String, body: ApplicationCall.() -> Unit)`。因此，`webResponse` 需要返回一个匹配 `ApplicationCall.() -> Unit` 类型的值。

这是一个带有*接收者类型*的*函数类型*。在清单 4-7 中对 `get` 的原始调用中，你将一个 lambda 作为第二个参数传递。函数类型是设置 Kotlin 函数以接收 lambda 的方式之一。换句话说，lambda 是有效的函数类型。

*接收者类型*是 `ApplicationCall`。带有接收者类型的 lambda 是一种你可以自定义 `this` 在 lambda 内部所指对象的 lambda。

*扩展函数*就像带有接收者类型的 lambda，你可以在函数体内自定义 `this` 所指的对象，但它们是完整的具名函数，而不是匿名 lambda。

在面向对象编程中，总有一个隐式的 `this` 指向你调用方法的实例。扩展函数是实例方法和普通独立函数之间的混合体。你不需要在类或接口内部定义扩展函数。相反，你可以在代码中的任何位置，为任何你想要的类型定义它们。

在清单 4-8 中，一个示例演示了 `String` 上的扩展函数。该函数使用 `this` 来引用你调用该函数的字符串。你可以像调用 `String` 类本身定义的方法一样调用该函数。

```
fun String.getRandomLetter() =
this[Random.nextInt(this.length)]
"FooBarBaz".getRandomLetter() // "z"
"FooBarBaz".getRandomLetter() // "B"
清单 4-8
String 上扩展函数的演示
```

带有接收者类型的 lambda 类似于扩展函数，区别在于它们只是 lambda，而不是在类型上定义的具名函数。你只能像调用该接收者类型实例上定义的方法一样，调用带有接收者类型的 lambda。清单 4-9 展示了一个实现和调用带有接收者类型的 lambda 的示例。

```
fun String.transformRandomLetter(
body: String.() -> String
): String {
val range = Random.nextInt(this.length).let {
it.rangeTo(it)
}
return this.replaceRange(
range,
this.substring(range).body()
)
}
"FooBarBaz".transformRandomLetter {
"***${this.uppercase()}***"
}
// "FooB***A***rBaz
清单 4-9
带有接收者类型的 lambda 的演示
```

参数 `body` 代表 lambda。与扩展函数类似，你直接在 `String` 的实例上调用 `body`，就像它是 `String` 本身实现的方法一样。

要了解更多关于扩展函数的信息，你可以阅读官方 Kotlin 文档：[`https://kotlinlang.org/docs/extensions.xhtml`](https://kotlinlang.org/docs/extensions.xhtml)。

### 返回函数的函数

快速提醒一下，`webResponse` 需要返回一个匹配 `ApplicationCall.() -> Unit` 类型的值。换句话说，`webResponse` 需要返回一个函数或 lambda，以便类型匹配。

你可以在清单 4-10 中看到 `webResponse` 实现的第一个空骨架版本。目前，它什么也不做，只是一个起点。

```
fun webResponse(
handler: suspend PipelineContext.(
) -> WebResponse
): PipelineInterceptor {
return {
}
}
清单 4-10
webResponse 的初始空骨架
```

如前所述，类型 `ApplicationCall(). -> Unit` 是一种简化。你需要从 `webResponse` 返回的实际类型是 `PipelineInterceptor<Unit, ApplicationCall>`，它是类型 `suspend PipelineContext< Unit, ApplicationCall >.( Unit) -> Unit` 的别名。你的 `handler` lambda 的类型几乎相同，只是它返回一个 `WebResponse` 实例而不是空值（`Unit`）。`webResponse` 的目标是调用 `handler`，使用 `handler` 返回的 `WebResponse` 实例，并最终将其映射到 Ktor。

提示

关键字 `suspend` 将在第 8 章中详细解释。目前，你只需要知道它是 Kotlin 处理程序类型的一部分，并且与协程相关。Ktor 中 `ApplicationCall` 上的某些 API 需要 `suspend` 关键字才能正常工作。

`webResponse` 返回一个 lambda。你不需要为 lambda 本身添加类型注解，因为 Kotlin 拥有从你返回 lambda 以及你为 `webResponse` 定义了返回类型这一事实中推断类型所需的一切信息。Kotlin 会自动将其转换为匹配 `PipelineInterceptor<Unit, ApplicationCall>` 的 lambda。

### 映射头部

`webResponse` 返回的 lambda 应包含 Ktor 和 `WebResponse` 类之间的所有映射代码。所有 `WebResponse` 实例都有一个头部映射，因此这是一个很好的起点。

你可以在清单 4-11 中找到 `webResponse` 的更新版本，它从 `WebResponse` 实例读取头部并将其映射到 Ktor。

```
fun webResponse(
handler: suspend PipelineContext.(
) -> WebResponse
): PipelineInterceptor {
return {
val resp = this.handler()
for ((name, values) in resp.headers())
for (value in values)
call.response.header(name, value)
}
}
清单 4-11
将 WebResponse 头部映射到 Ktor
```

你通过调用 `handler()` lambda 来获取 `WebResponse` 实例。映射代码不关心你创建该 `WebResponse` 的内容和细节。它只关心将任意的 `WebResponse` 实例映射到 Ktor。

你将 `handler` 作为扩展函数调用。`this` 的类型与 `handler` 的接收者类型匹配，这就是为什么你可以像调用 `handler` 是 `this` 所代表对象上的方法一样调用 `this.handler()`。

`headers()` 函数返回一个映射，其中键是头部名称，值是该头部名称的头部值列表。因此，你只需要遍历它们，并为每个头部值调用 `call.response.header`。

请记住：你从 `webResponse` 返回的 lambda 具有（简化的）签名 `ApplicationCall.() -> Unit`。这就是使 `call` 对其可用的原因。

### 映射 TextWebResponse 和状态码

下一步是映射各个响应类型，以便你做一些比仅设置头部更有用的事情，并使用 `WebResponse` 实例中的所有数据，将它们传递给 Ktor。

清单 4-12 展示了 `webResponse` 的更新版本，它检查 `WebResponse` 实例的类型并处理 `TextWebResponse`。

```
fun webResponse(
handler: suspend PipelineContext.(
) -> WebResponse
): PipelineInterceptor {
return {
val resp = this.handler()
for ((name, values) in resp.headers())
for (value in values)
call.response.header(name, value)
val statusCode = HttpStatusCode.fromValue(
resp.statusCode
)
when (resp) {
is TextWebResponse -> {
call.respondText(
text = resp.body,
status = statusCode
)
}
}
}
}
清单 4-12
将 TextWebResponse 和 JsonWebResponse 映射到 Ktor
```

`WebResponse` 有一个定义为 `Int` 的 `statusCode` 字段。Ktor 需要一个 `HttpStatusCode` 实例。幸运的是，Ktor 提供了 `HttpStatusCode.fromValue` 函数来将有效的 `Int` 转换为 `HttpStatusCode`。

你可以使用 `when` 语句将 `WebResponse` 的各个类型映射到 Ktor。`when` 类似于其他语言中的 `switch`，其中每个语句定义了你希望如何处理各种情况。目前，你只有 `TextWebResponse`，但稍后你会添加更多类型，因此你可以立即使用 `when` 语句。



### 映射 JsonWebResponse

与映射 `TextWebResponse` 相比，映射 `JsonWebResponse` 需要额外几个步骤。你需要添加一个第三方 JSON 序列化器（一种接收数据并写入 JSON 字符串的工具），并且需要实现 Ktor 接口 `OutgoingContent`，以便将包含 JSON 的字符串映射到 Ktor。

有许多可用的 JSON 序列化器。本书将使用 Gson，这是 Google 开发的一个 Java 库。Gson 是目前最快的 JSON 库之一（[`www.overops.com/blog/the-ultimate-json-library-json-simple-vs-gson-vs-jackson-vs-json/`](http://www.overops.com/blog/the-ultimate-json-library-json-simple-vs-gson-vs-jackson-vs-json/)）。它也很容易以面向数据的方式使用，因此你无需创建映射类和注解即可使用它，而 Java 平台上的其他一些 JSON 库则需要这些。它适用于使用普通列表和映射来序列化数据，以及在后续章节中解析 JSON。

通过将以下内容添加到 *build.gradle.kts* 的 `dependencies` 块中，将 Gson 添加为依赖项：

```
implementation("com.google.code.gson:gson:2.10")
```

接下来，你需要一个 `OutgoingContent` 实现，如清单 4-13 所示，它从 `JsonWebResponse` 获取你的 JSON 内容并将其映射到 Ktor。

```
import com.google.gson.Gson
class KtorJsonWebResponse (
val body: Any?,
override val status: HttpStatusCode = HttpStatusCode.OK
) : OutgoingContent.ByteArrayContent() {
override val contentType: ContentType =
ContentType.Application.Json.withCharset(Charsets.UTF_8)
override fun bytes() = Gson().toJson(body).toByteArray(
Charsets.UTF_8
)
}
清单 4-13
在 Ktor 中用于 JSON 序列化的 OutgoingContent 实现
```

这个新类 `KtorJsonWebResponse` 接收 `body` 中的 JSON 数据和 `status` 中的可选状态码，并通过覆盖 `OutgoingContent` 中的关键函数将它们全部映射到 Ktor。如果你愿意，可以通过增强它来传入你自己的 `ContentType` 实例，从而使其更加灵活，让你可以选择其他内容类型和字符集。但在大多数情况下，将其硬编码为默认的 `application/json` 和硬编码的编码（此处为 UTF-8）就足够了。

最后，你需要在 `webResponse` 的实现中使用 `KtorJsonWebResponse` 的实例，目前你只处理了 `TextWebResponse`。清单 4-14 显示了来自 `webResponse` 的更新后的 when 语句，其中你同时处理了 `JsonWebResponse` 和 `TextWebResponse`。

```
when (resp) {
is TextWebResponse -> {
call.respondText(
text = resp.body,
status = statusCode
)
}
is JsonWebResponse -> {
call.respond(KtorJsonWebResponse (
body = resp.body,
status = statusCode
))
}
}
清单 4-14
在 webResponse 的 when 语句中同时处理 TextWebResponse 和 JsonWebResponse
```

映射的核心部分位于 `KtorJsonWebResponse` 内部，因此你只需在 when 语句中为 `JsonWebResponse` 添加一个分支，并将 `KtorJsonWebResponse` 的实例传递给 `call.respond`。

### 使用新的映射

现在，你已经将 `WebResponse` 完全映射到了 Ktor，并且可以在你的处理器中开始使用它。

目前，你只有一个路由：对 / 的 GET 请求返回一个纯文本响应，内容为“Hello, World!”。此路由直接使用了 Ktor API。你可以将其更改为使用 `WebResponse`。清单 4-15 展示了如何执行此操作，同时也展示了如何在此过程中添加一些额外的路由。

```
get("/", webResponse {
TextWebResponse("Hello, world!")
})
get("/param_test", webResponse {
TextWebResponse(
"The param is: ${call.request.queryParameters["foo"]}"
)
})
get("/json_test", webResponse {
JsonWebResponse(mapOf("foo" to "bar"))
})
get("/json_test_with_header", webResponse {
JsonWebResponse(mapOf("foo" to "bar"))
.header("X-Test-Header", "Just a test!")
})
清单 4-15
在 Ktor 应用程序的路由块内，使用 WebResponse 向 Ktor 添加路由
```

启动你的应用程序，尝试打开所有新路径，看看会发生什么！

## 关于过度设计的说明

在这个独立的 HTTP 响应表示中，处理了各种边缘情况。你真的需要所有这些额外的代码吗？

我从未在实际的 Web 应用程序中做过类似的事情。这是因为我从没编写过既能在 Ktor 上运行又能在无服务器环境中运行的 Web 应用程序。我确实做过从一种 Web 框架切换到另一种的重写工作。但你不需要为了能够进行重写而预先抽象化你的 Web 框架——你可以像进行任何其他重写一样直接进行重写。我不喜欢仅仅为了简化项目中可能发生的重写而使用抽象。

在本书的上下文中，这种抽象是有意义的，因为我稍后想向你展示如何轻松地用 Jersey 和其他框架替换 Ktor，以及如何在无服务器环境中运行你的处理器。当你在业务逻辑和 Web 路由库之间拥有 `WebResponse` 时，这几乎是微不足道的。

此外，`WebResponse` 并不那么复杂。而且我个人更喜欢我的 Web 处理器采用函数式风格，而 `WebResponse` 正是这种风格。所以我认为这种权衡是值得的。下次我从头开始创建 Kotlin Web 应用程序时，我可能会在代码库中引入类似 `WebResponse` 的东西，仅仅因为我喜欢它的函数式风格。

但是，正是这种对你可能使用或不使用的各种用例的处理，才使得框架变得如此庞大和复杂。`WebResponse` 只是这个问题的一个微小示例。一个框架需要处理成千上万个这样的案例，并提供 API 和接口来支持所有这些案例的任意组合。因此，框架必然包含大量你永远不需要的抽象和代码，而这反过来又使得框架在你的系统中难以作为一个整体来理解。如果你使用的框架出现问题，你必须深入挖掘包含数十万行代码和多层抽象的“框架之山”，这至少可以说是一项艰巨的任务。

当然，这也是框架的价值所在。当你需要做某事时，框架中已经有一个可用的解决方案。并且拥有标准化的做事方式，使得在不同的代码库上工作更加容易，因为它们都遵循框架约定，以相同的方式做事。

所有抽象都有成本。我认为 `WebResponse` 是值得的。但要小心。不要仅仅为了抽象化一个库而去抽象化它。

