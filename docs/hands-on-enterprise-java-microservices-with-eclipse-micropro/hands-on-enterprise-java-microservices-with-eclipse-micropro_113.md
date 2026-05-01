# MicroProfile OpenAPI 注解

OpenAPI 信息最常见的来源可能是构成标准 JAX-RS 应用程序定义的一组注解。这些注解，连同 MicroProfile OpenAPI 规范定义的额外（可选）注解，可以被 MicroProfile 平台扫描和处理，以生成 OpenAPI 文档。

MP OpenAPI 规范要求能够从纯 JAX-RS 2.0 应用程序生成有效的 OpenAPI 文档。如果你是 OpenAPI 新手，可以简单地将现有的 JAX-RS 应用程序部署到 MicroProfile OpenAPI 运行时，并查看 `/openapi` 的输出。

为了填充生成的 OpenAPI 文档的更多细节，你可以进一步注解你的...

