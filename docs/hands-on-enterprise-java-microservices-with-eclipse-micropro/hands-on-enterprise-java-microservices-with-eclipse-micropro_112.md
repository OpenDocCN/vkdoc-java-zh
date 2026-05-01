# 生成 OpenAPI 文档

如前所述，MicroProfile OpenAPI 规范要求从三个来源的组合中生成一个 OpenAPI 文档。

然后，你有多种选择：

*   使用 MicroProfile OpenAPI 注解扩展由 JAX-RS 注解生成的 OpenAPI 文档。
*   利用 `/openapi` 的初始输出，将其作为开始记录 API 的参考。在这种情况下，你可以在编写任何代码之前编写静态 OpenAPI 文件（本章后续部分将描述），这是组织锁定 API 契约的常用方法，即 API 优先的开发实践。
*   通过使用编程模型进行编码来引导或完成 OpenAPI 模型树，这将在本章后面介绍。

此外，你可以在 OpenAPI 模型构建完成后使用过滤器对其进行更新。

