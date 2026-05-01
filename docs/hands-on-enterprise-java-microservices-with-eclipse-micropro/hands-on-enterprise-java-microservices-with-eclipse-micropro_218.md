# 第 6 章

1.  不是：默认情况下，即使未使用任何 MP OpenAPI 注解，任何 REST 端点都会为其生成 OpenAPI。
2.  是的：你可以根据需要选择使用任意数量（或多或少的）MP OpenAPI 注解，以表示微服务中的 REST 端点。
3.  其理念是，你预先定义端点的预期契约，并将这些契约封装在可与微服务捆绑在一起的 OpenAPI 文档中。
4.  不需要：你只需了解请求和响应的格式，然后就可以创建自己的类型安全接口。
5.  通过使用 `.../mp-rest/url` MP 配置设置，其中 `...` 是类型安全接口的接口名称，或者是传递给 `RegisterRestClient` 注解的 configKey。
6.  一种方法是注册一个 `ClientHeadersFactory` 实现。另一种方法是在 `org.eclipse.microprofile.rest.client.propagateHeaders` MP 配置属性中列出标头。

