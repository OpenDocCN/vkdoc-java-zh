# 10. 构建基于 API 的后端

在现代 Web 应用世界中，后端完全基于 API，并与移动应用或基于 JavaScript 的 Web 前端进行交互，这很常见。在本章中，你将学习如何为这些场景设置 API。

如果你是 Kotlin 新手，以下是一些你将在本章中看到示例的语言特性：

*   使用第三方库解析和生成 JSON

此外，在本章中，你将学习以下关于构建基于 API 的后端的内容：

*   不同类型的身份验证

*   如何为浏览器安全地设置 cookie

*   为单页 Web 应用和原生应用构建 API

## 处理 API 调用

API 调用就是一个普通的路由，与其他任何路由一样。到目前为止，你创建的所有使用 `JsonWebResponse` 的路由都是 API 路由，因为它们返回机器可读的数据。

在本节中，你将学习一些关于构建 API 最佳实践的细节。

### 解析输入

大多数 Web 应用都会以某种方式接收输入，因此你需要知道如何读取外部各方发布到你的 Web 处理器的数据。

你已经在第 9 章中读取过表单编码的数据，当时你在基于 HTML 的 Web 应用设置中接收了用于表单登录的用户名和密码的表单参数。为此，你使用了 `call.receiveParameters()`，它会使用浏览器用于 HTML 表单的表单数据编码来解析 HTTP 请求体。

要读取 JSON，你只需将 HTTP 请求体作为纯字符串读取，并使用你已在第 4 章中安装的 Gson 库（[*https://github.com/google/gson*](https://github.com/google/gson)）进行解析，该库用于通过 `JsonWebResponse` 编写 JSON 输出。与编写 JSON 相比，读取 JSON 唯一需要额外部骤是指定 Gson 应序列化数据的类——`Map` 或 `List`。清单 10-1 展示了如何在你的 Web 处理器中读取 JSON 的示例。

```
post("/test_json", webResponse {
val input = Gson().fromJson(
call.receiveText(), Map::class.java
)
JsonWebResponse(mapOf(
"input" to input
))
})
清单 10-1
在你的 Web 处理器中解析 JSON
```

这个 Web 处理器所做的就是解析接收到的 JSON，并返回一个将解析后的 JSON 重新编码为 JSON 的响应。

Ktor 函数 `call.receiveText()` 会自动处理编码，并使用该请求的 HTTP 头中指定的编码（如果请求未指定编码，则使用系统默认编码）将通过 HTTP 提交的字节解析为字符串。

### 验证和提取输入

为了使从 Web 处理器接收到的输入有用，你需要提取所接收数据的内容，并确保其格式符合 Web 应用业务逻辑的要求。

你不仅仅想要验证类型是否正确。编程语言数据类型过于通用，无法直接使用。例如，你应该验证某人发布到你的 API 的电子邮件地址看起来确实像一个电子邮件地址。仅仅检查它是 `String` 类型是不够的。

我偏好的数据验证库是 Arrow。在第 8 章中，你安装了 Arrow（[*https://arrow-kt.io/*](https://arrow-kt.io/)），以演示 `kotlin.coroutines` 和 `kotlinx.coroutines` 之间的区别。而使用的示例正是数据验证。因此，让我们基于那里展示的小示例进行构建，并在 Web 处理器中实现实际的数据验证。

Arrow 的好处在于，它为你验证的数据形状提供了完全的灵活性。Arrow 允许你从一个映射开始，最终得到一个验证错误或一个已初始化的数据类，而大多数其他 Kotlin 数据验证库与数据类紧密耦合，迫使你先实例化一个数据类，然后再进行验证。

在清单 8-10 中，你创建了一个 `ValidationError` 数据类、两个执行验证的函数（`validateEmail` 和 `validatePassword`），以及一个用于保存最终验证值的数据类（`MyUser`）。清单 10-2 展示了如何在 Web 处理器内部将这些函数连接起来进行数据验证。

```
post("/test_json", webResponse {
either {
val input = Gson().fromJson(
call.receiveText(), Map::class.java
)
MyUser(
email = validateEmail(input["email"]).bind(),
password = validatePassword(input["password"]).bind()
)
}.fold(
{ err ->
JsonWebResponse(
mapOf("error" to err.error),
statusCode = 422
)
},
{ user ->
// .. 对 `user` 做一些操作
JsonWebResponse(mapOf("success" to true))
}
)
})
清单 10-2
使用 Arrow 验证 JSON 输入
```

在这段代码中使用 Arrow 使其看起来直接且自然，但同时，Arrow 对 `kotlin.coroutines` 的使用使其相当巧妙。`Either` 和 `bind` 都是*左偏的*，因此一旦某个绑定的语句返回 `Either` 的左侧（即一个 `ValidationError`），`either` 块内代码的执行就会在该点停止，并且该左侧值成为“胜出者”。如果所有绑定的 `Either` 语句都返回右侧，则该值将简单地作为这些语句的结果返回，并且包含 `MyUser` 的右侧成为“胜出者”。

`fold` 是 `Either` 上的一个函数，它会根据 `Either` 的结果调用左侧或右侧的 lambda。`fold` 也是一个标记为 `inline` 的函数。如果你习惯了 JavaScript 甚至 Java 等语言中的回调，这段代码可能看起来只是调用了传递给 `fold` 的两个 lambda 之一，而没有返回任何内容。但由于 `fold` 是内联的，你可以将 lambda 的内容视为在顶层（在 `fold` 函数外部）编写的，因此 `fold` 中 lambda 的返回值就是整个语句的返回值。

所以，简而言之，这段代码要么返回错误的 `JsonWebResponse`，要么返回成功的 `JsonWebResponse`，具体取决于 `either` 块内的任何验证是否返回了 `ValidationError`。



### 避免自动序列化

在构建 Web 应用时，你应始终明确地进行序列化，并避免从 XML 和 JSON 等动态数据格式自动序列化为类型安全的数据类。

Kotlin 拥有大量库，可以自动将 XML 和 JSON 等动态数据格式转换为类型化的数据类。最终得到类型安全的数据类是一件好事。但自动将动态数据转换为类型安全的数据类是有问题的。

自动数据序列化的一个例子是 `kotlinx.serialization` ([*https://github.com/Kotlin/kotlinx.serialization*](https://github.com/Kotlin/kotlinx.serialization))。这个库本身没有问题；它是一个制作精良的库，完全实现了其应有的功能。`kotlinx.serialization` 的本质是，你创建数据类并用 `kotlinx.serialization.Serializable` 注解它们。这些数据类可以转换为各种数据格式，也可以从各种数据格式转换回来。清单 10-3 展示了如何通过一个类型安全的数据类序列化为 JSON 以及从 JSON 反序列化。

```
@Serializable
data class MyThing(val myVal: String, val foo: Int)
val obj = Json.decodeFromString(
"""{"myVal":"testing", "foo": 123}"""
)
obj.myVal
// "testing"
Json.encodeToString(obj)
// 返回 {"myVal":"testing", "foo": 123}
清单 10-3
演示 kotlinx.serialization 基础
```

使用 `kotlinx.serialization` 可以省去大量手动输入的工作，因为你只需将一个数据类与一个 JSON 字符串结合，剩下的工作都由 Kotlin 完成。

然而，主要的缺点是，这会将你的公共 API 与内部代码库紧密耦合。如果你在自己的代码中更改了 `MyThing.myVal` 的名称，也会破坏你 API 的调用者。

另一个缺点是，你的 API 实体被迫遵循 Kotlin 的命名约定。在 JSON 中，属性名称通常使用 `underscore_case` 而不是 `camelCase`。但由于你在动态数据和类型化的 Kotlin 数据类之间进行 1:1 的序列化，它们必须保持一致。

请注意，这属于高度主观的最佳实践范畴，会有很多聪明且高效的开发者不同意这个观点。但在我所有生产级的 Web 应用中，我一直坚持这种区分，以保持我自身代码的灵活性和敏捷性，同时为 API 的外部调用者提供稳定性和向后兼容性。

### 内部 API 与外部 API

你在本节中学到的内容主要适用于内部 API。我的意思是，这些 API 是由你自己的代码或与你密切合作的人编写的代码来消费的。

如果你正在编写一个面向公众的 API，例如作为 SaaS 产品一部分的 API，那么你应该确保你的 API 具有出色的错误消息，并且无论你向它抛出什么，它都能以正确的 HTTP 状态码完美运行。例如，如果你向当前代码发布无效的 JSON，它只会抛出一个 5xx 错误（服务器错误），而技术上正确的状态码应该是 4xx 系列（客户端错误）。

本章中的技术不足以达到那种细节水平。我没有教你如何为大规模公共 API“正确地”做到这一点，有两个原因。

首先，这样做成本高昂。当你和你的同事是你 API 的唯一消费者时，一个解释基本信息的 README 文件和一个大部分时间能返回有用错误消息（但偶尔如果有人向你的 API 提交了足够糟糕的数据，会抛出 500 错误）的 API 就足够了。

其次，我实际上从未制作过面向公众的 API。关于如何正确做到这一点，我有很多想法。但由于我没有相关的实际经验，我认为我不是教你如何做到这一点的合适人选。

## 单页应用

在现实世界中经常看到的一种 API 用例是针对浏览器和基于 JavaScript 的*单页应用*（SPA）。

### 与 API 一起托管

设置单页应用的一种方法是将 API 和前端都托管在同一个 Web 应用中。

SPA 的核心是一个 `index.xhtml` 文件，它加载你的 JavaScript 代码和其他关键资源。在大多数现代单页应用中，你的 JavaScript 被设置为使用 `pushState` 浏览器 API 作为其路由的一部分，以处理 SPA 的不同部分。例如，React JavaScript 框架中常用的路由库 React Router 就是以这种方式运行的。这意味着，当你首次打开 URL `/`（根页面）时，后端提供 `index.xhtml` 文件，该文件进而加载你的 JavaScript，然后 SPA 路由器渲染正确的内容。接着，你在 SPA 中点击一个按钮，JavaScript 会调用 `pushState`，使得 URL 从 `/` 变为 `/something_else`，然后你的 JavaScript 会渲染适合该 URL 的不同内容。

这样做的问题是，当你分享该页面的链接或用户尝试刷新时，浏览器中的当前 URL 是 `/something_else`，而这是你的 Ktor 路由将尝试加载的路由。因此，如果你只设置了 `/` 来提供 `index.xhtml`，你现在会得到一个 404 页面，因为你的 Ktor 后端没有 `/something_else` 的映射。

一种解决方案是手动添加 SPA 中所有已知的路由，并将它们复制到你的 Ktor 路由中。然而，这并非一个真正可行的选项，因为你最终会进行大量手动且容易出错的复制粘贴。

另一种选择是在 Ktor 中使用特殊的 `singlePageApplication` 路由类型。`singlePageApplication` 的工作方式与 `static` 类似，不同之处在于它还会设置你的路由，使得任何 Ktor 未处理的路由都将渲染你的 `index.xhtml` 文件。通过这种设置，直接在 `/something_else` 上刷新页面是可行的，因为你的 Ktor Web 应用中没有针对该路径的路由处理器，这反过来会触发 `singlePageApplication` 路由并加载 `index.xhtml`，从而使你的 JavaScript 运行并处理正确页面的渲染。清单 10-4 展示了如何进行设置。

```
singlePageApplication {
if (appConfig.useFileSystemAssets) {
filesPath = "src/main/resources/public"
} else {
useResources = true
filesPath = "public"
}
defaultPage = "index.xhtml"
}
清单 10-4
在 createKtorApplication 内部设置 singlePageApplication，以从你的 Web 应用托管 SPA
```

在添加 `singlePageApplication("/")` 路由之前，你应该确保移除现有的 `static("/")` 路由。旧的 `static("/")` 路由与 `singlePageApplication("/")` 冲突，而后者已经完成了旧 `static("/")` 路由所做的所有事情。

`singlePageApplication` 中的配置属性与你用于 `static` 的相同。在本地运行时，你指向文件系统中的文件；而在生产环境中运行时，你指向 Gradle 构建编译到编译输出 Java 类路径中的资源。

`defaultPage` 指向引导你的 SPA 的 `index.xhtml` 文件。该文件应位于 `src/main/resources/public/index.xhtml`。通常，这个文件只包含一个极简的 HTML 文件，用于加载你用来加载 SPA 的 JavaScript。之所以是 HTML 文件而不是 `kotlinx.xhtml` DSL 代码，是因为你可能会使用某种 SPA 构建工具（如 Webpack）来构建你的 SPA，这些工具通常会生成你必须使用的自己的 HTML 引导代码。此外，该文件应该是完全静态的，并且不会根据你正在查看的路由而改变内容。因此，将其作为静态文件使用效果很好。



请记住，为了避免网页与 API 处理程序之间的冲突，所有 API 路由都应使用 `/api/...` 前缀。因此，不要通过 `POST /login` 请求登录，而应将其改为 `POST /api/login`。同时，请确保你的单页应用中没有任何以 `/api` 开头的页面。这样，你的 Ktor 后端和 JavaScript 路由器之间就永远不会出现路由冲突。

### 使用 CORS 进行独立托管

设置单页应用的另一种方法是将 JavaScript 代码和 HTML 引导文件托管在与 API 不同的域名上，并使用带有 CORS 的 XHR/fetch 来实现单页应用与 API 之间的跨域访问。

实际的静态 HTML 和 JavaScript 文件的结构，与使用 `singlePageApplication` 路由将它们与 API 托管在一起时相同。区别在于，你将它们托管在外部。因此，你可以将 API 托管在 `api.myapp.com`，将 SPA 托管在 [`www.myapp.com`](http://www.myapp.com)。然后，你可以为 SPA 选择一个更适合提供原始静态文件的托管平台，例如 AWS CloudFront、Cloudflare、Azure Static Web Apps 等 CDN，或者任何你想要的平台。

CORS（跨域资源共享的缩写）是所有现代浏览器都支持的一种 API，它允许你的 JavaScript 代码调用自身域名之外的 API。传统上，浏览器将 HTTP 调用限制在你当前所在的域名，如果你尝试调用不同的域名（即跨域请求），就会收到错误。因此，按照前面的例子，如果浏览器显示的是 [`www.myapp.com`](http://www.myapp.com)，而你尝试使用 JavaScript 调用 `api.myapp.com`，就会收到错误消息。

当 JavaScript 向不同的域名发起请求时，它首先会向该服务器发送一个 `OPTIONS` 请求。（`OPTIONS` 是另一种 HTTP 动词，类似于 `GET`、`POST`、`PUT` 等）。如果该 `OPTIONS` 请求响应了正确的 CORS 头，浏览器将继续执行你发起的实际请求。如果没有，你将收到一条错误消息。

你需要设置你的 Ktor API 来处理这些 CORS 请求。你可以手动拦截 `OPTIONS` 请求并响应正确的头信息。但更简单的方法是安装为你处理此事的 CORS 插件。

第一步是将该插件作为依赖项添加到你的 *build.gradle.kts* 文件中：

```
implementation("io.ktor:ktor-server-cors-jvm:2.1.2")
```

要使 CORS 生效，你需要更改一些设置。在 `setUpKtorCookieSecurity` 中，将会话 cookie 的 `SameSite` 属性从 `"lax"` 更改为 `"none"`。现在你将在不同的源之间运行你的 Web 应用，因此为了让浏览器正确传输你的会话 cookie，你需要禁用 `SameSite`。

接下来，你需要设置 CORS 插件本身。将其添加到你的 `createKtorApplication` 中，与你的 `StatusPages` 插件放在一起。清单 10-5 展示了如何正确设置它。

```
install(CORS) {
allowMethod(HttpMethod.Put)
allowMethod(HttpMethod.Delete)
allowHost("localhost:4207")
allowHost("www.myapp.com", schemes = listOf("https"))
allowCredentials = true
}
清单 10-5
向你的 Ktor 应用添加 CORS 设置
```

HTTP 方法 `GET` 和 `POST` 默认是启用的。我实际使用的大多数 Web 应用至少也会用到 `PUT` 和 `DELETE`，因此你也应该允许这些方法。你还需要启用特定的主机名。CORS 支持通配符源，但当你需要包含凭据（即 cookie）时则不支持。最后，你需要通过将 `allowCredentials` 设置为 `true` 来启用凭据。

你还需要一种在本地托管 Web 应用 HTML 的方法。这就是为什么你要将 `localhost:4207` 添加到允许的源列表中，这意味着你可以在开发环境中使用 `singlePageApplication` 插件，并将其配置为仅在本地环境加载，而不在生产环境加载。或者，你可以使用类似 create-react-app 的工具，或者自己设置 Webpack，来托管一个 index.xhtml 文件并构建你的 JavaScript。如果你使用像 Webpack 这样的外部服务器，请更新端口号以指向你的 Webpack 服务器的端口。（构建 SPA 的复杂细节超出了本章的范围，因此我特意跳过了许多关于如何设置它的细节。）

### 用户身份验证

完成上述设置后，你可以使用 JavaScript 发起登录请求，即使你的 JavaScript 运行在与 API 不同的源上，它也能正常工作。

你可以使用第 9 章中现有的 `POST /login` 处理程序从你的单页应用登录。该 Web 处理程序期望接收由普通 HTML `<form>` 提交的表单编码数据。但你也可以从 JavaScript 生成相同类型的请求。清单 10-6 展示了一个如何执行此操作的示例。

```
fetch("/login", {
credentials: "include",
method: "POST",
headers: {
"Content-Type": "application/x-www-form-urlencoded"
},
body: new URLSearchParams({
username: ...,
password: ...
})
})
清单 10-6
从不同源使用 JavaScript 调用你的 API
```

重要的是要记住，在调用 fetch 时设置 `credentials: "include"`。如果不这样做，它就不会在请求中接收和发送任何 cookie。

由于 cookie 是仅限 HTTP 的，因此无法从 JavaScript 访问它。这意味着，如果你因某种原因成为脚本注入攻击的受害者，攻击者也无法提取会话密钥，这在安全性上是一个巨大的优势。

如果你正在构建一个没有任何 HTML 的纯 API，你也可以更改 `POST /login` 以接收和解析 JSON，而不是像 HTML `<form>` 标签中那样的数据。如果这样做，你还应该更改你的 API，使其在成功登录时返回 200 OK 而不是重定向。

## 原生应用

非 Web 原生应用，例如移动应用和原生桌面应用，也可以调用你的 API。此类别还包括基于 Web 的封装器，即原生应用封装了 Web 技术，例如 Electron。即使这些应用使用 Web 技术，它们也不必受制于浏览器的安全沙箱，并且可以像普通的原生应用一样执行网络调用。

### 使用 Cookie 进行身份验证

与基于 Web 的 SPA 相比，在原生应用中唯一需要考虑的是身份验证。对于非浏览器应用，设置 API 以对用户进行身份验证主要有两种方法。第一种是使用 cookie 进行身份验证，就像你为基于 HTML 的 Web 应用或本章前面介绍的 SPA 所做的那样。

大多数原生应用可用的 HTTP 客户端会自动处理 cookie，或者可以设置为处理 cookie。这意味着 HTTP 客户端的行为将类似于浏览器，存储使用 `Set-Cookie` 头设置的任何 cookie，并在你发起的请求中将它们发送回服务器。这是一种简单方便的方法，可以为基于 Web 的应用和原生应用提供相同的 API 设置。

确保不要为 SPA 启用 CORS 或使用身份验证令牌。对于原生应用，暴露身份验证令牌无关紧要。但对于 Web 应用，跨域攻击是一种常见的攻击向量，因此将身份验证凭据隐藏在 JavaScript 运行时无法访问的 cookie 后面非常重要。



### 使用 JWT 进行身份验证

如果你从头开始构建一个仅限原生客户端使用的 API，你仍然可以使用 Cookie。但在这种情况下，更常见的是使用基于令牌的身份验证。特别是，JSON Web 令牌（JWT）是实现这一点的既定方式。

JWT 是一组遵循开放 JWT 标准（[*https://jwt.io/*](https://jwt.io/)）格式化的数据块，其中包含经过安全签名的 JSON 编码身份验证信息。除了加密签名外，JWT 还包含关于令牌使用哪种加密算法的元数据，以及一个包含已登录用户任意信息的 JSON 块。JSON 数据通常包含属性 `"sub"`（subject 的缩写），其中包含令牌所代表用户的唯一 ID。常见的属性还有 `"name"` 和 `"email"`，但你希望 JWT 包含什么内容完全取决于你。

当你对用户进行身份验证时，你会在响应中返回包含身份验证令牌本身的数据。然后，在后续请求中，你使用 `Authorization` 标头将此身份验证令牌发送回服务器。

Ktor 内置了对 JWT 授权的支持，因此你将使用它在你的 Web 应用中进行设置。你需要做的第一件事是将所需的依赖项添加到 *build.gradle.kts* 中：

```
implementation("io.ktor:ktor-server-auth-jwt-jvm:2.1.2")
```

下一步是设置你的 JWT 身份验证。你将在新的 Ktor Application 扩展函数中执行此操作，就像你现有的函数 `createKtorApplication` 和 `setUpKtorCookieSecurity` 一样。清单 10-7 展示了如何进行设置。

```
fun Application.setUpKtorJwtSecurity(
appConfig: WebappConfig,
dataSource: DataSource
) {
val jwtAudience = "myApp"
val jwtIssuer = "http://0.0.0.0:4207"
authentication {
jwt("jwt-auth") {
realm = "myApp"
verifier(JWT
.require(Algorithm.HMAC256(appConfig.cookieSigningKey))
.withAudience(jwtAudience)
.withIssuer(jwtIssuer)
.build())
validate { credential ->
if (credential.payload.audience.contains(jwtAudience))
JWTPrincipal(credential.payload)
else
null
}
}
}
}
清单 10-7
设置 JWT 身份验证
```

为了简化设置，你复用了配置属性 `cookieSigningKey` 作为 JWT 签名密钥。如果需要，你可以仅为 JWT 添加一个单独的配置属性。

身份验证过程本身不使用发行者和受众属性。它们仅用于验证目的，如果你的 API 可以接受来自不同来源的 JWT，或者你想签发可以在不同身份验证范围内使用的 JWT，这会很有用。

下一步是添加一个 Web 处理器来执行实际的登录和创建新的 JWT。清单 10-8 展示了如何进行设置。

```
routing {
post("/login", webResponseDb(dataSource) { dbSess ->
val input = Gson().fromJson(
call.receiveText(), Map::class.java
)
val userId = authenticateUser(
dbSess,
input["username"] as String,
input["password"] as String
)
if (userId == null) {
JsonWebResponse(
mapOf("error" to "用户名和/或密码无效"),
statusCode = 403
)
} else {
val token = JWT.create()
.withAudience(jwtAudience)
.withIssuer(jwtIssuer)
.withClaim("userId", userId)
.withExpiresAt(
Date.from(LocalDateTime
.now()
.plusDays(30)
.toInstant(ZoneOffset.UTC)
)
)
.sign(Algorithm.HMAC256(appConfig.cookieSigningKey))
JsonWebResponse(mapOf("token" to token))
}
})
}
清单 10-8
在 setUpKtorJwtSecurity 配置中执行登录
```

这段代码类似于 `setUpKtorCookieSecurity` 中基于 `<form>` 的登录代码，它接收用户名和密码作为参数，并根据输入参数返回错误或有效的身份验证凭据。

如果用户名和密码匹配，你将创建一个新的 JWT，该 JWT 使用与清单 10-7 中授权设置相同的签名密钥进行签名，并将登录用户的 ID 添加为令牌负载的一部分。由于你使用的是基于令牌的身份验证，因此无需设置 Cookie 或执行任何特殊操作。你只需在 HTTP 响应中将令牌作为数据的一部分返回，在本例中是以 JSON 编码的映射形式返回。

要保护需要身份验证的路由，其过程与基于 Cookie 的身份验证相同。清单 10-9 展示了如何进行设置。

```
authenticate("jwt-auth") {
get("/secret", webResponseDb(dataSource) { dbSess ->
val userSession = call.principal()!!
val userId = userSession.getClaim("userId", Long::class)!!
val user = getUser(dbSess, userId)!!
JsonWebResponse(mapOf("hello" to user.email))
})
}
清单 10-9
使用 JWT 身份验证保护路由块内的路由
```

你使用内置的表示 JWT 的 `JWTPrincipal` 类，而不是使用为基于 Cookie 的身份验证设置的自定义主体数据类。此主体被设置为自动解析正确的标头并解码令牌。

JWT 包含你在 `POST /login` 上创建令牌时指定的 `userId` 声明。

这段代码存在大量可能引发空指针异常的地方。代码中有三个 `!!` 运算符，如果值为 `null`，它们会导致你的代码抛出 `NullPointerException`。我倾向于尽可能保持类型系统的整洁，并明确说明我的业务逻辑中所需的类型以及它们是否可为空。但对于系统边缘的代码，即那些可能因用户输入而发生各种奇怪情况的地方，我不介意使用 `!!` 运算符来尽早失败并清理输入。这是一种廉价的输入验证形式，虽然会产生糟糕的错误消息，但它非常容易添加，并且比在整个代码中传递可空类型要好得多。

### 执行身份验证

为了演示你将如何对此 API 进行身份验证，你将在终端中使用 cURL 来执行 API 调用。

首先，确保你的 API 已准备好使用基于 JWT 的身份验证。在 `embeddedServer` 中，移除对 `setUpKtorCookieSecurity` 的现有调用，并将其替换为本章中的新函数 `setUpKtorJwtSecurity`，然后运行你的 main 函数来启动你的 Web 应用。

你将获得在清单 10-8 的代码中生成的 JSON 作为返回。首先，尝试在没有身份验证令牌的情况下调用 `GET /secret`，看看会发生什么：

```
$ curl -I http://localhost:4207/secret
HTTP/1.1 405 Method Not Allowed
```

要执行身份验证，你将使用可重复迁移中的用户用户名和密码调用 `POST /login`：

```
$ curl -X POST \
-d '{"username":"august@crud.business","password":"1234"}' \
http://localhost:4207/login
{"token":""}
```

接下来，向 `GET /secret` 发起请求，并传入你获得的身份验证令牌，你应该会得到一个成功的结果：

```
$ curl -H "Authorization: Bearer " \
http://localhost:4207/secret
{"hello":"august@crud.business"}
```

如果你一切操作正确，你将获得 JWT 所代表用户的电子邮件。

请注意 `Authorization` 标头的格式。它以文本 `"Bearer"` 开头，后跟实际的令牌。这是 HTTP 规范的一部分，因为 Authorization 标头应首先说明身份验证方法（在本例中为 JWT 承载者），然后是实际的授权参数（JWT 本身）。如果没有 `"Bearer "` 前缀，你将无法成功进行身份验证。

