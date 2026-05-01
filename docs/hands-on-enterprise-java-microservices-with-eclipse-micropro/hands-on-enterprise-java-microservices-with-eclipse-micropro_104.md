# @Traced 注解

MicroProfile OpenTracing 定义了一个 `@Traced` 注解，可用于在 CDI Bean 上启用追踪，或在自动追踪的接口上禁用追踪。该注解还可用于覆盖其他自动追踪组件（JAX-RS 端点）上的操作名称。

以下代码示例展示了如何使用 `@Traced` 注解在 CDI Bean 上启用追踪。`(1)` 为 Bean 定义的所有方法启用追踪。`(2)` 将默认操作名称（`package.className.method`）覆盖为 `get_all_users`。`(3)` 禁用了 health 方法的追踪：

```
@Traced (1)@ApplicationScopedpublic class Service {   @Traced(operationName = "get_all_users") (2)   public void getUsers() {        // 业务代码   } @Traced(false) (3) ...
```

