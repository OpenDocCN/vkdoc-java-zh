# 按标题搜索图书

GET /bookshop/book/search/ 控制器。

Application.searchByTitle(keyword:String)

本示例展示了一个用于在线管理图书的基础应用程序。它恰当地使用了 HTTP 方法和 URI。

路由文件中定义的条目展示了路由的重要特性。第一个条目表明首页 URL（例如 http://yourdomainname/bookshop）指向 Application 类（一个控制器）中定义的 index 方法。其余的定义展示了如何在路径中以及作为查询字符串参数动态包含参数。

让我们更详细地探讨这一点。

**静态定义**

定义 `GET / controllers.Application.index` 表明根路径不接收任何参数，并被路由到 Application 类中定义的 index 方法。在此示例中，你使用的是 GET 协议。如果你的 Web 服务器支持，你也可以使用 PUT 和 DELETE HTTP 协议。

路由条目的整体结构是：

协议 URL 路径 **控制器映射**

**URL 中的动态部分**

假设你想检索某本特定图书的详细信息，但事先不知道用户会选择哪本书，因此图书 ID 必须是动态的：

第 3 章 Play 控制器与 HTTP 路由 `GET /bookshop/book/:id/ controllers.Application.getBook(id:String)`

动态部分由冒号（:）后跟参数名（本例中为 :id）表示。Play 将从 URI 中提取动态部分，并将其作为参数提供给 getBook 方法。这意味着 URL 路径 `/bookshop/book/123/`、`/bookshop/book/12453/` 等都将被映射到 Application 类中定义的 getBook 方法。

**关键点**：`:variablename` 用于定义 URI 的动态部分。例如 `:id`。

你还可以配置跨越多个正斜杠（/）的动态部分。

例如，

`GET /bookshop/book/images/* controllers.Application.fetchImage(name:String)`

匹配以下 URL：

`/bookshop/book/images/book1.jpg`

`/bookshop/book/images/books/book2.jpg`

`/bookshop/book/images/books/thumbnails/small/image1.jpg`

传递给 fetchImage 方法的 name 参数将分别是 `book1.jpg`、`books/book2.jpg` 和 `books/thumbnails/small/image1.jpg`。

你使用了控制器类的完全限定名（`controllers.Application`）。如果需要，你也可以定义并使用其他控制器。

Play 还支持路径定义中的正则表达式。要在动态部分定义正则表达式，可以使用 `$id<regex>` 语法。

第 3 章 Play 控制器与 HTTP 路由 例如：

`GET /bookshop/book/:id/page/$page<[0-9]+>/ controllers.Application.fetchBookpage(bookid:String, page:Integer)`

在这个定义中，有两个动态部分：一个用于 id（图书的唯一 ID），另一个用于页码。页码部分被定义为一个只接受数字的正则表达式。如果 URI 中包含非数字字符，Play 将进行验证并抛出错误。例如，当请求为 `http://localhost:9000/bookshop/book/10/page/12/` 时，页面将正常渲染，因为请求中的页码是有效数字。如果请求为 `http://localhost:9000/bookshop/book/10/page/a/`，Play 将抛出“未找到操作”错误，因为请求中的页码不是有效数字。

**关键点** `$variablename<regular expression>` 用于定义匹配正则表达式的动态部分。

**传递固定值**

对于某些方法，你需要始终传递一个固定值。你可以这样配置此类情况：

`GET /bookshop//authors/ controllers.Application.authors(limit: Integer = 10)`

这里你向 show 方法传递了一个默认值 10。这意味着当用户调用 show 方法时，它只显示 10 本书。

第 3 章 Play 控制器与 HTTP 路由 **可选参数**

一个典型的用例是某些参数有时会被传递，但在某些情况下会缺失。为了处理这种情况，你可以使用可选定义：

