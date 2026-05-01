# 伞式发布与伞外项目

Eclipse MicroProfile 由一组规范组成，每个规范都有特定的关注点。例如，Eclipse MicroProfile Config 规范涵盖了与微服务参数配置相关的所有内容。某个版本的规范可以作为 Eclipse MicroProfile 伞式发布的一部分被包含在内，也可以在伞外发布。具体来说，最新的 Eclipse MicroProfile 2.2 伞式发布于 2019 年 2 月 12 日发布，包含了以下规范：

*   Eclipse MicroProfile Open Tracing 1.3
*   Eclipse MicroProfile Open API 1.1
*   Eclipse MicroProfile Rest Client 1.2
*   Eclipse MicroProfile Fault Tolerance 2.0
*   Eclipse MicroProfile Config 1.3
*   Eclipse MicroProfile Metrics 1.1
*   Eclipse MicroProfile JWT Propagation 1.1
*   Eclipse MicroProfile Health Check 1.0
*   CDI 2.0
*   JSON-P 1.1
*   JAX-RS 2.1
*   JSON-B 1.0

然而，Eclipse MicroProfile 也有其他在伞式发布之外发布的规范。例如，我们将在第 9 章*响应式编程与未来发展*中介绍的 Eclipse MicroProfile Reactive Streams Operators 1.0，就是一个最近在伞外发布的规范。那么，为什么 MicroProfile 允许在伞外发布规范呢？原因在于，先在伞外发布，可以让社区和最终用户有机会使用和测试新技术，从而在实际应用中验证它，之后再考虑将其纳入伞式发布。

