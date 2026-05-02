# 20. 总结与展望

Michael Müller^(1 )

(1)德国，北莱茵-威斯特法伦州，布吕尔

Books 是一个主要供一位作者管理、面向众多读者发布的应用程序。我们期望评论的作者（也就是我）知道如何与应用程序交互。这意味着我们可以降低对用户界面的要求，特别是在验证输入方面。尽管如此，Books 仍然是一个功能齐全的交互式 Web 应用程序。

在回顾了用于表单和文本的基本 JSF 标签（在我们讨论 TinyCalculator 时）之后，我们讨论了更多 JSF 特性，如循环、条件、表格和不同类型的链接，以及更高级的特性，如模板。我们概述了 JSF 如何与其他 Java EE 技术集成。Books 利用了 Java 持久化 API (JPA)、上下文和依赖注入 (CDI) 以及 Bean 验证。我们讨论了国际化的某些方面，以及如何使用 CSS 使应用程序适应不同的屏幕尺寸。

Books 应用程序的源代码以 zip 文件形式提供，可从 [`webdevelopment-java.info/webdevelopment/resources/download/BooksComplete.zip`](http://webdevelopment-java.info/webdevelopment/resources/download/BooksComplete.zip) 下载。要打开压缩存档，请使用密码 MdkJ47(kq!。如果你检查代码，你会发现一些尚未讨论的技术。例如，有一个很少使用的 `<f:ajax>`。这些技术将在下一部分中描述。

说到下一部分，下一个应用程序 Alumni 被设计成一个社交平台。所有用户都需要注册并输入数据。我们需要讨论安全性并提高对用户界面的要求。所有用户输入都必须经过验证，并且用户应该获得即时响应。验证器、AJAX、身份验证、自定义组件等都将结合 Alumni 的使用进行介绍。

