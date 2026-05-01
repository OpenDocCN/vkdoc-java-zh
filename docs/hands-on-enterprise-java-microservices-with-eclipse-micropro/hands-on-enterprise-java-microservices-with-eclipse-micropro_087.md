# 直方图

直方图是一种对数据分布进行采样的指标类型。它主要用于记录执行被装饰方法所花费时间的分布。与其他类型不同，直方图不能通过专用注解声明，但例如计时器包含直方图数据。要单独使用直方图，您需要在代码中注册并更新它：

```
// 注册直方图
@Inject
@Metric(absolute = true)
private Histogram aHistogram;

// 使用 0 到 10 之间的值更新
@GET
@Path("/h")
public String getHistogram() {
  aHistogram.update((int) (Math.random() * 10.0));
  return "Histogram called";
}
```

这种在代码中使用指标的方式也适用于其他类型。

