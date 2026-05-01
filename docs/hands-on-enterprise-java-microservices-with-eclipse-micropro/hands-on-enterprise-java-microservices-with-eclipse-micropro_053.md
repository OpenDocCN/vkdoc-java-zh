# @CircuitBreaker 策略

`@CircuitBreaker` 注解可以应用于类或方法。断路器模式由 Martin Fowler 引入，用于保护操作的执行，使其在功能异常时快速失败：

```
@CircuitBreaker(requestVolumeThreshold = 4, failureRatio=0.75, delay = 1000)
public void operationCouldBeShortCircuited(){
  ...
}
```

在上面的示例中，该方法应用了 `CircuitBreaker` 策略。如果在连续四次调用的滚动窗口中发生三次（*4 x 0.75*）失败，断路器将打开。断路器将保持打开状态 1000 毫秒，然后恢复到半开状态。成功调用一次后，断路器将再次恢复到关闭状态。

