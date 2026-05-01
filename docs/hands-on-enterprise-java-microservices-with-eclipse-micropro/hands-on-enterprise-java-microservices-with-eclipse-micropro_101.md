# JAX-RS

MicroProfile OpenTracing 会自动追踪所有入站 JAX-RS 端点。然而，JAX-RS 客户端侧的处理更为复杂，需要调用注册 API `org.eclipse.microprofile.opentracing.ClientTracingRegistrar.configure(ClientBuilder clientBuilder)` 来添加追踪能力。MicroProfile 实现可以全局地为所有客户端接口启用追踪；但为了更好的可移植性，建议使用注册 API。

默认的追踪行为可以通过禁用特定请求的追踪或更改生成的服务端跨度操作名称来修改。更多信息，请参考本章后面的*配置属性*部分。检测层会自动将以下请求范围的信息添加到每个跨度中：

*   `http.method`：请求的 HTTP 方法。
*   `http.status_code`：请求的状态码。
*   `http.url`：请求的 URL。
*   `component`：被检测组件的名称，即 `jaxrs`。
*   `span.kind`：客户端或服务端。
*   `error` – `true` 或 `false`。此为可选字段，如果存在，检测层还会将异常作为 `error.object` 添加到跨度日志中。

所有这些标签都可以通过追踪系统用户界面来查询数据，或者用于许多追踪系统提供的数据分析任务。可以通过注入的追踪器实例将额外的元数据添加到当前活跃的跨度中。这可以在过滤器中全局执行，也可以在 REST 处理器中局部执行，如下面的代码示例所示，通过向服务端跨度添加用户代理标头（1）：

```
@Path("/")
public class JaxRsService {
   @Inject
   private io.opentracing.Tracer tracer;

   @GET
   @Path("/hello")
   @Traced(operationName="greeting") (2)
   public String hello(@HeaderParam("user-agent") String userAgent) {
       tracer.activeSpan().setTag("user-agent", userAgent); (1)
   }
}
```

默认情况下，服务端跨度的操作名称为 `http_method:package.className.method`。但是，可以通过使用 `@Traced` 注解（2）在本地更改此设置，或者通过配置属性（请参阅配置部分）进行全局更改。

