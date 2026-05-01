# 配置属性

OpenTracing 是供应商中立的，因此可以与任何使用此 API 的供应商的追踪实现一起工作。每个追踪器实现都将以不同的方式进行配置。因此，配置超出了 MicroProfile OpenTracing 规范的范围。然而，该规范本身公开了一些配置属性，用于调整追踪范围或生成的数据。该配置利用 MicroProfile Config 规范为所有支持的配置选项提供一致的方法。

目前，该规范公开了以下内容：

*   `mp.opentracing.server.skip-pattern`：一个跳过模式，用于避免追踪选定的 REST 端点。
*   `mp.opentracing.server.operation-name-provider`：这指定了服务器跨度（span）的操作名称提供者。可能的值为 `http-path` 和 `class-method`。默认值为 `class-method`，它完全使用限定类名与方法名连接；例如，`GET:org.eclipse.Service.get`。`http-path` 使用 `@Path` 注解的值作为操作名称。

