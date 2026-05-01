# 人工操作员

JSON 响应负载的主要用途是为操作员提供一种检查应用程序状态的方式。为此，健康检查允许将附加数据附加到健康检查响应中，正如我们在 `CheckDiskspace` 和 `ServiceCheck` 示例中所见。请考虑以下片段：

```
[...]
return HealthCheckResponse
           .named("memory-check")
           .withData("free-heap", "64mb")
           .up()
           .build();
[...]
```

此处提供了关于 `free-heap` 的附加信息，并将成为响应负载的一部分，如下面的响应片段所示。显示 `memory-check` 过程内容的 JSON 响应片段如下：

```
{
...
   "checks": [
       {
           "name": "memory-check",
           "state": "UP",
           "data": {
               "free-heap": "64mb"
           }
       }
   ],
   "outcome": "UP"
}
```

在这里，我们看到了 `memory-check` 过程，其状态为 `UP`，以及一个值为 `64mb` 的字符串类型附加数据项 `free-heap`。

**MP-Health 的 Eclipse 资源/GitHub 坐标**：
MP-Health 项目的源代码可以在 [`github.com/eclipse/microprofile-health`](https://github.com/eclipse/microprofile-health) 找到。

