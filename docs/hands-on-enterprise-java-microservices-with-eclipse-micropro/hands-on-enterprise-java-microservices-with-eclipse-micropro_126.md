# 提供者优先级

提供者可以通过注解和 `RestClientBuilder` 进行注册。通过构建器注册的提供者将优先于 `@RegisterProvider` 注解。`@RegisterProvider` 注解的优先级值优先于类上的任何 `@javax.annotation.Priority` 注解。当使用 `RestClientBuilder` 接口上的 register 方法时，可以覆盖提供者优先级，因为它允许设置优先级。

