# 长时间运行操作

在松散耦合的服务环境中，**长时间运行操作**（**LRA**）规范背后的动机是，通过由对多个微服务的调用组成的业务流程提供一致的结果，而无需锁定数据。理解 LRA 的一种方式是将其视为*微服务的事务*。需要 LRA 的场景示例包括：

*   在线订购一本书需要从库存中扣减一本书、处理付款，最后发货。所有这些任务都需要原子性地发生，换句话说，它们需要一起处理，如果任何任务失败，则所有任务都必须撤销。
*   预订航班需要从飞机的可用座位列表中移除一个座位、为旅客选择和分配特定座位、处理付款以及创建记录定位码。同样，所有这些任务都必须在同一个长时间运行操作内完成。

上述示例不仅需要原子性地发生，而且即使其中间步骤有任何失败，它们也必须生成一个数据一致的结果。

MicroProfile LRA 当前提出的解决方案灵感来源于 *OASIS Web 服务复合应用程序框架技术委员会*（[`www.oasis-open.org/committees/tc_home.php?wg_abbrev=ws-caf`](https://www.oasis-open.org/committees/tc_home.php?wg_abbrev=ws-caf)），即 *Web 服务**长时间运行操作事务模型*（[`www.oasis-open.org/committees/document.php?document_id=12794`](https://www.oasis-open.org/committees/document.php?document_id=12794)），但已进行更新以更适合在基于微服务的架构中使用。

有关 MicroProfile LRA 规范的更多信息，请参阅 [`github.com/eclipse/microprofile-lra/blob/master/spec/src/main/asciidoc/microprofile-lra-spec.adoc`](https://github.com/eclipse/microprofile-lra/blob/master/spec/src/main/asciidoc/microprofile-lra-spec.adoc)。

MicroProfile 长时间运行操作规范模型包括三个主要实体：补偿器、逻辑协调器和客户端。客户端可以通过两种不同的方式显式启动一个新的 LRA：

*   通过注解，或
*   通过 API 调用

任何一种方式都会创建一个新的 LRA。如果某个服务执行了可能需要稍后撤销的操作，则客户端需要向 LRA 注册一个补偿器。如果客户端选择关闭或取消 LRA，补偿器将撤销该服务在 LRA 范围内执行的工作，或补偿任何未完成的工作。

以下是全局范围内一些主要的 LRA 注解：

*   `@LRA` 控制 LRA 的生命周期。
*   `@Compensate` 指示如果 LRA 被取消，则应调用该方法。
*   `@Complete` 指示如果 LRA 被关闭，则应调用该方法。
*   `@Forget` 指示该方法可以释放为此 LRA 分配的任何资源。
*   `@Leave` 指示此类不再对此 LRA 感兴趣。
*   `@Status` 在调用带注解的方法时报告状态。

您可以将这些注解与 JAX-RS 和非 JAX-RS 方法一起使用。此外，本规范支持 JAX-RS 的异步和响应式特性、LRA 嵌套以及超时。最后，值得一提的是，LRA 规范通过对参与协议的实体施加某些要求来确保原子性和最终一致性。作为一个 MicroProfile 项目，MicroProfile LRA 规范在撰写本文时处于提议或草案状态。

