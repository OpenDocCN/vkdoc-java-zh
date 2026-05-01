# @Timeout 策略

`@Timeout` 注解可以应用于类或方法，以确保操作不会无限期持续：

```
@Timeout(200)public void operationCouldTimeout() {  ...}
```

在前面的示例中，如果操作持续时间超过 200 毫秒，它将被停止。

