# MicroProfile Boost

在撰写本文时，MicroProfile Boost 正在 MicroProfile 沙箱中接受社区评估。Boost 是一个 Maven 插件，用于增强 MicroProfile 应用程序的构建。

有关 Boost 的更多信息，请访问 [`github.com/eclipse/microprofile-sandbox/tree/master/proposals/boost`](https://github.com/eclipse/microprofile-sandbox/tree/master/proposals/boost)。

Boost 为 MicroProfile API（例如用于 MicroProfile Config 的 `mpConfig`）以及 Java EE API 定义了 Maven 依赖项，这些依赖项被称为 **boosters**。此外，它还为实现不同 MicroProfile API 的运行时（例如 `openliberty`）定义了依赖项。另一个由 Boost 定义的、指定为 BOM（物料清单）的 Maven 依赖项，指明了在 Maven 构建中针对 MicroProfile API 应使用的 MicroProfile 总括项目的版本。BOM 的内容由插件管理。作为 Boost 的用户，您可以将这些依赖项包含在您的 `pom.xml` 文件中，以简化 MicroProfile 应用程序的构建过程。

