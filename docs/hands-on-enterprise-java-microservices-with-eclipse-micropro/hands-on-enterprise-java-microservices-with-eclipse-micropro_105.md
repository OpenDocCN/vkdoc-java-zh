# 追踪器注入

应用程序可以注入一个 `io.opentracing.Tracer` Bean，它暴露了完整的 OpenTracing API。这允许应用程序开发者利用更高级的用例，例如向当前活跃的跨度添加元数据、手动创建跨度、使用 baggage 进行上下文传播，或初始化额外的第三方检测。

以下代码展示了如何使用追踪器将数据附加到当前活跃的跨度 `(1)`：

```
@Path("/")
public class Service {
    @Inject
    private Tracer tracer;

    @GET
    @Path("")
    @Produces(MediaType.TEXT_PLAIN)
    public String greeting() {
       tracer.activeSpan()
           .setTag("greeting", "hello"); (1)
       return "hello";
   }
}
```

这对于向跨度添加业务相关数据非常有用，也可用于记录异常或任何其他性能分析信息。

