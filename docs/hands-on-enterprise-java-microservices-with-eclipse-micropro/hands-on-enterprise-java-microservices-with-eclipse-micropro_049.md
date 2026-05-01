# @Asynchronous 策略

使操作异步化非常简单，如下所示：

```
@Asynchronous
public Future<Connection> service() throws InterruptedException {
  Connection conn = new Connection() {
    {
      Thread.sleep(1000);
    }

    @Override
    public String getData() {
      return "service DATA";
    }
 };
 return CompletableFuture.completedFuture(conn);
}
```

唯一的约束是 `@Asynchronous` 方法必须返回 `Future` 或 `CompletionStage`；否则，实现应抛出异常。

