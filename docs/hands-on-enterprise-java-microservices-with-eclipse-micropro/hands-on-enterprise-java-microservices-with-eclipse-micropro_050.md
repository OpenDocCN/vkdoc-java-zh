# @Retry 策略

如果操作失败，你可以应用重试策略来再次调用该操作。`@Retry` 注解可以在类或方法级别使用，如下所示：

```
@Retry(maxRetries = 5, maxDuration= 1000, retryOn = {IOException.class})public void operationToRetry() {  ...}
```

在上面的示例中，该操作最多重试五次，且仅在发生 `IOException` 时重试。如果所有重试的总持续时间超过 1000 毫秒，该操作将被中止。

