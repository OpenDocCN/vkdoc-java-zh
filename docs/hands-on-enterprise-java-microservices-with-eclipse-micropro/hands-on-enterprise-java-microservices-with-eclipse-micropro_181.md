# MicroProfile 响应式消息传递示例

在本节中，我们将介绍一些使用 MP-RM 创建生成和消费消息的 CDI Bean 的示例。

假设您希望一个 CDI Bean 充当 `Message<String>` 的源，以便每当调用其 `publishMessage(String)` 方法时，一条 MP-RM 消息就会被发布到某个 MP-RM 通道。为此，我们需要定义一个连接器，在 CDI Bean 和 MP-RM 层之间建立桥梁。以下代码展示了一个执行此操作的传入消息连接器示例：

```
package io.pckt.reactive;import javax.enterprise.context.ApplicationScoped;import javax.inject.Inject;import org.eclipse.microprofile.config.Config;import org.eclipse.microprofile.reactive.messaging.Message;import org.eclipse.microprofile.reactive.messaging.spi. ...
```

