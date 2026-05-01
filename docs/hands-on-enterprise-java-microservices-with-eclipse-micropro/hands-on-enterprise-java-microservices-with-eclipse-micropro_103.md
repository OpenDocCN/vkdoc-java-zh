# 显式检测

有时，自动检测无法捕获所有关键的时间信息，因此需要额外的追踪点。例如，我们可能希望追踪业务层的调用，或初始化 OpenTracing 项目（[`github.com/opentracing-contrib`](https://github.com/opentracing-contrib)）提供的第三方检测。

显式检测可以通过三种方式完成：

*   在**上下文和依赖注入**（**CDI**）Bean 上添加 `@Traced` 注解。
*   注入追踪器并手动创建跨度。
*   初始化第三方检测。外部检测的初始化取决于其自身的初始化要求。MicroProfile 只需提供一个追踪器实例，这在前一点中已经涵盖。

现在让我们详细讨论这些内容。

