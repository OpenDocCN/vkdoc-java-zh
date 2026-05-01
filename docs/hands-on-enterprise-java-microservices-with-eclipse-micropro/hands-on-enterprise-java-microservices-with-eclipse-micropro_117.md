# 使用过滤器进行更新

要更新或移除 OpenAPI 文档的某些元素和字段，你可以使用过滤器。`OASFilter` ([`github.com/eclipse/microprofile-open-api/blob/master/api/src/main/java/org/eclipse/microprofile/openapi/OASFilter.java`](https://github.com/eclipse/microprofile-open-api/blob/master/api/src/main/java/org/eclipse/microprofile/openapi/OASFilter.java)) 接口允许你接收各种 OpenAPI 元素的回调。它允许你覆盖你关心的方法。你可以创建此接口的实现，并使用 `mp.openapi.filter` 配置键进行注册。

以下是在 `META-INF/microprofile-config.properties` 中其定义的大致示例：

```
mp.openapi.filter=com.mypackage.MyFilter
```

注册的过滤器会为每个模型元素调用一次。例如，`filterPathItem` 方法用于...

