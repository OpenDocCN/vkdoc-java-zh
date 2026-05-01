# 从服务器检索指标

默认情况下，MicroProfile Metrics 通过 REST 接口在 `/metrics` 上下文根下公开指标。您可以在 [`github.com/PacktPublishing/Hands-On-Enterprise-Java-Microservices-with-Eclipse-MicroProfile/tree/master/Chapter05-metrics`](https://github.com/PacktPublishing/Hands-On-Enterprise-Java-Microservices-with-Eclipse-MicroProfile/tree/master/Chapter05-metrics) 找到代码。按照 `README.md` 文件构建代码、运行它，并使用浏览器多次访问 [`localhost:8080/book-metrics/hello`](http://localhost:8080/book-metrics/hello) 和 [`localhost:8080/book-metrics`](http://localhost:8080/book-metrics) 端点以生成一些数据。

从 MicroProfile 1.3/2.0 开始，规范中没有关于保护该端点的内容。因此，这留给各个实现自行处理。

使用此 REST 接口，可以轻松检索数据，例如，通过以下 `curl` 命令：

```
$ curl http://localhost:8080/metrics
```

此命令以 Prometheus 文本格式（缩写）显示 Metrics 1.x 输出：

```
# TYPE base:classloader_total_loaded_class_count counter
base:classloader_total_loaded_class_count 13752.0 
# TYPE base:cpu_system_load_average gauge
base:cpu_system_load_average 2.796875
# TYPE base:thread_count counter
base:thread_count 76.0
# TYPE vendor:memory_pool_metaspace_usage_max_bytes gauge
vendor:memory_pool_metaspace_usage_max_bytes 7.0916056E7
# TYPE application:hello_time_rate_per_second gauge
application:hello_time_rate_per_second{app="shop",type="timer"} 
3.169298061424996E-10
# TYPE application:hello_time_one_min_rate_per_second gauge
application:hello_time_one_min_rate_per_second{app="shop",type="timer"} 0.0
[...]
```

如果您不提供媒体类型，默认输出格式是 Prometheus 文本格式（也可以在浏览器中很好地呈现）。Prometheus 格式在 `# TYPE` 和 `# HELP` 行中向值公开了额外的元数据。您还可以在前面的示例中看到范围（base、vendor 和 application）是如何添加到实际指标名称之前的。

或者，可以通过提供 `HAccept` 标头以 JSON 格式检索数据（同样缩写）：

```
$ curl -HAccept:application/json http://localhost:8080/metrics
```

此命令产生以下输出：

```
{
 "application" :
  {
    "helloTime" : {
        "p50": 1.4884994E7,
     [...]
       "count": 1,
       "meanRate": 0.06189342578194245
     },
    "getCounted" : 1
  },
  "base" :
  {
    "classloader.totalLoadedClass.count" : 13970,
    "cpu.systemLoadAverage" : 2.572265625,
    "gc.PS Scavenge.time" : 290
  },
  "vendor" :
  {
    "test" : 271,
    "memoryPool.Metaspace.usage.max" : 72016928,
 }
```

在这种情况下，公开的是纯数据；范围构成顶层，相应的指标嵌套在其中。可以通过 HTTP `XOPTIONS` 调用检索匹配的元数据：

```
$ curl XOPTIONS -HAccept:application/json http://localhost:8080/metrics
```

输出现在包含作为映射的元数据：

```
{
"application" : {
 "helloTime": {
 "unit": "nanoseconds",
 "type": "timer",
 "description": "Timing of the Hello call",
 "tags": "app=shop,type=timer",
 "displayName": "helloTime"
 }
}
[...]
```

现在我们已经了解了如何检索不同类型的数据和元数据，我们将快速了解一下如何将检索限制到特定范围。



