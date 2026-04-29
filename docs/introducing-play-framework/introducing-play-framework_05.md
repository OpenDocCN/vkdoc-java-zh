# 显示所有评论或特定用户的评论

`GET /bookshop//showcomment/ controllers.Application.showComment(userid ?= null)`

在这个例子中，如果传递了 userid，它将检索该用户的评论；如果没有传递，则获取所有评论。

**关键点** `variable? = default-value` 用于定义可选参数。

我希望你现在已经理解了静态和动态 URL 配置。在进入控制器部分和其他章节之前，让我们先看看如何为应用程序声明配置，因为在接下来的章节中，你将需要在 `application.conf` 文件中添加条目。

**使用 application.conf 进行应用程序配置**

Play 提供了一个名为 `application.conf` 的单一文件，位于 `conf` 目录内，用于配置应用程序级别的设置。这个 `conf/application.conf` 文件可用于配置数据库连接字符串、日志级别、启用额外功能的模块等。`application.conf` 文件是一个 UTF-8 编码的文件。

第 3 章 Play 控制器与 HTTP 路由 该文件中的条目遵循以下模式：

