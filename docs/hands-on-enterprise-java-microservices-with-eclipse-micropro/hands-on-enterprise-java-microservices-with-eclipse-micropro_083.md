# 提供特定于应用程序的指标

应用程序可以选择通过 CDI 编程模型公开指标数据。此模型受 DropWizard Metrics 启发，以便更容易地将应用程序迁移到 MP-Metrics。它还使用了 DropWizard Metrics 中的注解，这些注解已得到增强以支持元数据。

让我们从一个示例开始，定义一个计数器，然后在代码中递增它：

```
@Inject
@Metric(absolute = true, description = "# calls to /health")
Counter hCount; // 这是计数器

@GET
@Path("/health")
public Response getHealth() throws Exception {
    hCount.inc(); // 它在应用程序中被增加
    [...]
}
```

在此示例中，我们通过将计数器注入到 `hCount` 变量中来注册它：

`@Metric` 注解提供了额外信息，例如描述，并且还指示名称是变量名，不包含额外的包（`absolute=true`）。

在以下示例中，我们让实现为我们执行计数。这实现了计算方法或 REST 端点调用次数的常见用例：

```
@Counted(absolute=true,
        description="# calls to getCounted",
        monotonic=true)
@GET
@Path("/c")
public String getCounted() {
    return "Counted called";
}
```

`@Counted` 的 `monotonic` 属性表示持续增加计数器，否则在离开方法时计数器会减少。

