# 回退

另一方面，当所有重试都失败时，Istio 并没有回退策略——它无法让 Istio 调用工作负载的另一个版本。当你用 `@Fallback` 注解修饰上述代码时，可以在原始调用失败时执行另一个操作：

```
@Fallback(fallbackMethod = "fallbackForA")@Retry (maxRetries = 3)string aMethod() {   callBackend();}void String fallbackForA() {    return "A cached string";}
```

在这种情况下，一旦 Istio 的重试次数乘以 MicroProfile 的重试次数全部用尽，就会调用 `fallbackForA` 回退方法。如果你从前面的示例中移除 `@Retry` 注解，那么当 Istio 的重试用尽时，就会调用回退方法。……

