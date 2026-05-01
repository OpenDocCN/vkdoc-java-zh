# 消息形态

MP-RM 规范定义了许多受支持的签名类型，Bean 可以使用这些类型来定义发布和订阅行为。这些签名依赖于以下几个关键类型：

*   `org.reactivestreams.Publisher`：响应式流 `Publisher<T>` 是潜在无限数量有序元素的提供者，根据从其链接订阅者收到的需求发布这些元素。
*   `org.reactivestreams.Subscriber`：响应式流 `Subscriber<T>` 接口，用于向 `Publisher` 发出需求信号。它提供订阅信息事件、零个或多个数据事件以及错误和完成事件。
*   `org.reactivestreams.Processor`：此响应式流 `Processor<T,R>` 接口简单地同时扩展了 `Subscriber<T>` 和 `Publisher<R>`。
*   `org.eclipse.microprofile.reactive.streams.operators.PublisherBuilder`：MP 响应式流操作符 `PublisherBuilder` 接口允许你从各种来源构建响应式流 `Publisher`，并应用操作来转换/过滤最终发布的消息。
*   `org.eclipse.microprofile.reactive.streams.operators.ProcessorBuilder`：MP 响应式流操作符 `ProcessorBuilder` 接口允许你从各种来源构建响应式流 `Processor`，并应用操作来转换/过滤最终发布的消息。
*   `org.eclipse.microprofile.reactive.streams.operators.SubscriberBuilder`：MP 响应式流操作符 `ProcessorBuilder` 接口允许你从各种来源构建响应式流 `Subscriber`，并应用操作来转换/过滤最终发布的消息。
*   `java.util.concurrent.CompletionStage`：此 JDK 并发 `util` 包接口定义了一个通常是异步的计算阶段，并计算一个动作或值。`CompletionStage` 可以组合，以便可以执行一个阶段图来产生最终结果。
*   `org.eclipse.microprofile.reactive.messaging.Message<T>`：一个 MP-RM 接口，它提供了对类型 `T` 的负载的包装，以及一个用于确认收到消息的 `ack` 方法。

定义了这些类型后，我们可以查看各种类型的方法，这些方法通过将消息推送到 MP-RM 支持的传出通道来生成数据。发布者方法类型的方法都带有 `@Outgoing("channel-name")` 注解，并支持以下签名：

*   `Publisher<Message<T>> method()`
*   `Publisher<T> method()`
*   `PublisherBuilder<Message<T>> method()`
*   `PublisherBuilder<T> method()`
*   `T method()`
*   `CompletionStage<T> method()`

消费者方法都带有 `@Incoming("channel-name")` 注解，并支持以下签名：

*   `Subscriber<Message<T>> method()`
*   `Subscriber<T> method()`
*   `SubscriberBuilder<Message<T>>`
*   `SubscriberBuilder<T>`
*   `void method(Message<T> payload)`
*   `void method(T payload)`
*   `CompletionStage<?> method(Message<T> payload)`
*   `CompletionStage<?> method(T payload)`

同时消费和生产数据的方法称为处理器，它们将同时带有 `@Incoming("channel-in")` 和 `@Outgoing("channel-out")` 注解。支持的签名如下：

*   `Processor<Message<I>, Message<O>> method()`
*   `Processor<I, O> method();`
*   `ProcessorBuilder<Message<I>, Message<O>>method()`
*   `ProcessorBuilder<I, O> method();`
*   `Publisher<Message<O>> method(Message<I> msg)`
*   `Publisher<O> method(I payload)`
*   `PublisherBuilder<Message<O>> method(Message<I> msg)`
*   `PublisherBuilder<O> method(I payload)`
*   `Message<O> method(Message<I> msg)`
*   `O method(I payload)`
*   `CompletionStage<Message<O>> method(Message<I> msg)`
*   `CompletionStage<O> method(I payload)`
*   `Publisher<Message<O>> method(Publisher<Message<I>> pub)`
*   `PublisherBuilder<Message<O>> method(PublisherBuilder<Message<I>> pub)`
*   `Publisher<O> method(Publisher<I> pub)`
*   `PublisherBuilder<O> method(PublisherBuilder<I> pub)`

现在，我们将看一些使用具有这些签名的 Bean 来构建消息处理链的示例。

