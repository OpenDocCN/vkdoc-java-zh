# 默认异常映射

每个实现都提供了一个默认的 `ResponseExceptionMapper` 实现，当响应状态码 >= 400 时，该实现会将响应映射并调用为 `javax.ws.rs.WebApplicationException`。其优先级为 `Integer.MAX_VALUE`，旨在作为遇到错误时的回退机制。此映射器默认会注册到所有客户端接口，但可以通过将 MP Config 属性 `microprofile.rest.client.disable.default.mapper` 设置为 `true` 来禁用。也可以在构建客户端时，通过使用相同的属性来按客户端禁用：

```
RestClientBuilder.newBuilder().property("microprofile.rest.client.disable.default.mapper",true)
```

