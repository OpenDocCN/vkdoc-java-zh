# MicroProfile OpenAPI 及其功能简介

推动数字经济的移动力量促使企业需要建立全渠道的开发方法，以优化成本、提高效率并改善客户体验。这种方法的推动者是 API，它催生了 API 经济以及诸如 API 主导或 API 优先的开发实践等概念。此外，微服务架构已成为现代开发的首选架构。微服务之间基于 API（即 RESTful）的通信已被采纳为*事实上的*标准，因为它非常适合微服务的*智能端点与哑管道*、*去中心化治理*以及*去中心化数据管理*等特性。

然而，随着微服务架构中微服务数量的增加，其管理可能会变得棘手。不过，你可以通过微服务的 API 来管理它们。你可以将管理、安全、负载均衡和限流策略应用于微服务前端的 API。

Eclipse MicroProfile OpenAPI 为开发者提供了 Java 接口，用于从其 Java RESTful Web 服务（JAX-RS）应用程序生成 OpenAPI v3 文档。该规范要求，一个经过完整处理的 OpenAPI 文档必须能够通过 HTTP `GET` 操作在根 URL `/openapi` 上获取，如下所示：

```
GET http://myHost:myPort/openapi
```

所需的协议是 `http`。然而，强烈建议规范的实现者也支持 `https` 协议，以便安全地连接到 OpenAPI 端点。

OpenAPI 文档的创建有三个来源。这三个来源（将在本章后续章节中描述）如下：

*   通过处理应用程序中的 JAX-RS 注解（以及可选的 OpenAPI 注解）生成
*   由应用程序通过提供一个实现 `OasModelReader` 的 Java 类以编程方式构建
*   包含在应用程序部署中的静态 OpenAPI 文档

这三个来源（任意组合）会被合并以生成一个单一的 OpenAPI 文档，该文档可以被过滤（通过提供一个实现 `OasFilter` 接口的 Java 类），然后通过前述的 `/openapi` 端点提供服务。

