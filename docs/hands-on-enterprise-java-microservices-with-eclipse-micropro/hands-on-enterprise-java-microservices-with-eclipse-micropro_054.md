# @Bulkhead 策略

`@Bulkhead` 注解也可以应用于类或方法，以强制执行隔板策略。此模式将当前操作中的故障隔离开来，以保护其他操作的执行。实现通过限制给定方法上的并发调用数量来做到这一点：

```
@Bulkhead(4)public void bulkheadedOperation() {  ...}
```

在上面的代码中，此方法同时只支持四次调用。如果 `bulkheadedOperation` 方法同时收到超过四个请求，系统将保持第五个及之后的请求，直到四个活动调用中的一个完成。隔板注解也可以与 `@Asynchronous` 一起使用，以限制异步...



