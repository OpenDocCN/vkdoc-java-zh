# 总结

在本章中，我们学习了服务器和应用程序的可观测性。

指标（或遥测）有助于精确定位服务器或应用程序的性能特征。MicroProfile 通过 Metrics 规范提供了一种以标准化方式导出指标的方法。应用程序编写者可以使用 MicroProfile Metrics 通过注解或调用 Metrics API，以声明方式将其数据暴露给监控客户端。

本章进一步解释了 MicroProfile 中的 OpenTracing 集成如何为通过系统的每个单独事务提供端到端视图。我们介绍了配置属性，展示了 JAX-RS 的追踪，并最终在 Jaeger 系统中研究了数据。

在下一章中，我们将学习如何通过 OpenAPI 记录（REST）API，并通过类型安全的 REST 客户端调用这些 API。

