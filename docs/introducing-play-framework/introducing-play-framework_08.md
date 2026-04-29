# 提供给应用程序的日志记录器：

logger.application=DEBUG

**控制器**

Play 中的控制器实现了 MVC 设计模式中的控制器元素。您已经了解到，每个 Play 请求都会通过 `conf/routes` 文件中定义的 HTTP 路由配置映射到一个操作。在所有这些配置中，您都可以看到名为 `Application` 的控制器的使用。

这样做只是为了方便，因为 Play 会自动在控制器文件夹中创建一个名为 `Application` 的类。您完全可以创建另一个类并使用它。

一个控制器将相关的操作组合在一起。您可以根据应用程序的功能，创建任意数量的控制器。以书店应用程序为例，您可以定义一个 `BookController` 来处理所有面向用户的交互，并定义一个 `BackOfficeController` 来定义所有被称为后台功能的操作，例如维护库存、向系统添加书籍、为书籍添加图片等。将功能分组到不同的控制器中有助于提高应用程序的可维护性和模块化程度。

在 Play 中，控制器类继承自 `play.mvc.Controller`。您可以定义一个返回 `Result` 的操作方法：

```java
public Result index() {
    return ok("Hello World.");
}
```

`Result` 本质上就是返回给客户端的 HTTP 响应。

操作方法也可以接受参数，这些参数会根据 `conf/routes` 文件中定义的路由逻辑进行解析：

```java
public Result saveRating(String bookid, int rating) {
    // 保存评分的逻辑
    return ok("success");
}
```

一个典型的 HTTP 响应包含状态码、标头和正文。Play 提供了大量的辅助方法来生成各种 HTTP 响应。例如，上面代码片段中展示的 `ok()` 方法会向客户端发送一个 HTTP 200 响应。类似地，Play 还提供了 `notFound()`、`badRequest()`、`internalServerError()` 等方法来返回各种类型的 HTTP 响应。这些辅助方法定义在 `play.mvc.Results` 类中。由于您编写的控制器继承自 `Controller` 类，而 `Controller` 类又继承自 `Results` 类，因此您的控制器默认可以使用所有这些方法。

以下是一个向客户端返回内部服务器错误的示例：

```java
Result response = internalServerError("Server Error");
```

另一个需要理解的重要事项是如何将请求重定向到另一个 URL 或另一个操作。这在 Play 中也非常简单。它提供了一个 `redirect` 方法来实现：

```java
public Result hello() {
    return redirect("/book/login/");
}
```

这会将用户重定向到登录页面。此方法会向浏览器发送 HTTP 状态码 303。

**完成 Bookshop 控制器**

现在，让我们为书店应用程序的 `Application` 控制器添加所有必需的方法并进行测试。路由配置如下所示：

```
# 首页
GET     /bookshop                          controllers.Application.index

# 显示书籍详情
GET     /bookshop/book/:id/                controllers.Application.getBook(id:String)
```



