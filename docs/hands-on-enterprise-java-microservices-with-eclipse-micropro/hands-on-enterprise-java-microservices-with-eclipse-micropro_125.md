# 高级用法的提供者注册

`RestClientBuilder` 接口扩展了 JAX-RS 的 `Configurable` 接口，允许用户在构建时注册自定义提供者。所支持的提供者的行为由 JAX-RS 客户端 API 规范定义。MP-RC 实现将支持来自 JAX-RS 的 `ClientResponseFilter`、`ClientRequestFilter`、`MessageBodyReader`、`MessageBodyWriter`、`ParamConverter`、`ReaderInterceptor` 和 `WriterInterceptor`。

对于在其 `filter` 方法中具有 `ClientRequestContext` 参数的 `ClientResponseFilter` 和 `ClientRequestFilter` 接口，MP-RC 实现会添加一个 `org.eclipse.microprofile.rest.client.invokedMethod` 属性，其值为 `java.lang.reflect.Method` 对象……

