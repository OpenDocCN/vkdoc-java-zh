# 访问特定作用域

通过在路径后附加作用域名称，也可以仅检索单个作用域的数据。在以下示例中，我们仅检索基础作用域的指标：

```
$ curl http://localhost:8080/metrics/base
```

现在，这仅显示基础作用域的指标：

```
# TYPE base:classloader_total_loaded_class_count counterbase:classloader_total_loaded_class_count 13973.0# TYPE base:cpu_system_load_average gaugebase:cpu_system_load_average 1.92236328125
```

在本节中，我们了解了如何从启用了 MicroProfile Metrics 的服务器检索指标。基础作用域和供应商作用域中的指标由服务器预定义。应用程序作用域中的指标可以由用户定义，我们将在下一节中探讨这一点。……

