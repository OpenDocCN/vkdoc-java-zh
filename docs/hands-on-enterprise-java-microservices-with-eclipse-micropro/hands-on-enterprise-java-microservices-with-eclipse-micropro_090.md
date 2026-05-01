# 服务器范围的标签

服务器范围的标签通过环境变量 `MP_METRICS_TAGS` 设置，如下所示：

```
export MP_METRICS_TAGS=app=myShopjava -jar target/metrics-thorntail.jar
```

这些标签将被添加到服务器中定义的所有指标，并添加到相应的输出格式中。

因此，给定上述命令，一个计数器 `@Counted(absolute=true) int myCount;` 在 Prometheus 中最终会如下所示：

```
# TYPE application:my_count counterapplication:my_count{app="myShop"} 0
```

