# 10. 并发

本章总结了 Java 9 中与并发相关的更改。

## CompletableFuture

在 Java 9 中，`java.util.concurrent.CompletableFuture` 新增了几个方法。



### 异步

`CompletableFuture<T> completeAsync(Supplier<? extends T> supplier, Executor executor)` 和 `CompletableFuture<T> completeAsync(Supplier<? extends T> supplier)` 方法通过使用异步任务调用 `Supplier` 来获取结果，从而完成 `CompletableFuture`。该任务使用提供的执行器或默认执行器执行。清单 10-1 展示了如何使用 `completeAsync()` 的示例。

```
final Long v1 = new CompletableFuture().completeAsync(() -> 1L).join();
final Long v2 = new CompletableFuture().completeAsync(() -> 1L,
Executors.newSingleThreadExecutor()).join();
System.out.printf("%s %s%n", v1, v2);
清单 10-1.
completeAsync() 示例
```

### 超时

方法 `CompletableFuture<T> orTimeout(long timeout, TimeUnit unit)` 如果在给定超时时间之前未完成，则会以 `TimeoutException` 异常完成 `CompletableFuture`。在清单 10-2 中，由于 `CompletableFuture` 从未完成，代码会抛出一个以 `TimeoutException` 为原因的 `ExecutionException`。

```
try {
new CompletableFuture().orTimeout(3, TimeUnit.SECONDS).get();
} catch (final InterruptedException | ExecutionException e) {
e.printStackTrace();
}
清单 10-2.
orTimeout() 示例
```

方法 `CompletableFuture<T> completeOnTimeout(T value, long timeout, TimeUnit unit)` 如果在给定超时时间之前未完成，则会使用给定的值完成 `CompletableFuture`。在清单 10-3 中，`CompletableFuture` 在三秒超时后以值 `1` 完成。

```
try {
final Long value = new CompletableFuture()
.completeOnTimeout(1L, 3, TimeUnit.SECONDS).get();
System.out.println(value);
} catch (final InterruptedException | ExecutionException e) {
e.printStackTrace();
}
清单 10-3.
completeOnTimeout() 示例
```

### 工具方法

表 10-1 展示了 Java 9 中新增的 `CompletableFuture` 工具方法。

表 10-1.

`CompletableFuture` 的工具方法

| 方法 | 描述 |
| --- | --- |
| `<U> CompletableFuture<U> newIncompleteFuture()` | 返回一个新的未完成的 `CompletableFuture` |
| `Executor defaultExecutor()` | 返回用于异步方法的默认 `Executor` |
| `CompletableFuture<T> copy()` | 返回一个新的 `CompletableFuture`，该对象正常完成时与此 `CompletableFuture` 的值相同，或者异常完成时以此 `CompletableFuture` 抛出的异常为原因 |
| `CompletionStage<T> minimalCompletionStage()` | 返回一个新的 `CompletionStage`，该对象只能由此 `CompletableFuture` 完成 |
| `Executor delayedExecutor(long delay, TimeUnit unit, Executor executor)` | 返回一个新的 `Executor`，该对象在给定延迟后将任务提交给给定的执行器 |
| `Executor delayedExecutor(long delay, TimeUnit unit)` | 类似于前一个方法，但使用默认执行器 |
| `<U> CompletionStage<U> completedStage(U value)` | 返回一个新的 `CompletionStage`，该对象以给定值完成 |
| `<U> CompletionStage<U> failedStage(Throwable ex)` | 返回一个新的 `CompletionStage`，该对象以给定异常异常完成 |
| `<U> CompletableFuture<U> failedFuture(Throwable ex)` | 返回一个新的 `CompletableFuture`，该对象以给定异常异常完成 |

### TimeUnit 和 ChronoUnit

两个静态方法 `ChronoUnit toChronoUnit()` 和 `TimeUnit of(ChronoUnit chronoUnit)` 被添加到 `java.util.concurrent.TimeUnit` 类中，用于在 `TimeUnit` 和 `java.time.temporal.ChronoUnit` 之间进行转换；参见清单 10-4。

```
public class TimeUnitTest {
@Test
public void testChronoUnit() throws Exception {
assertEquals(TimeUnit.MINUTES, TimeUnit.of(ChronoUnit.MINUTES));
assertEquals(ChronoUnit.SECONDS, TimeUnit.SECONDS.toChronoUnit());
}
}
清单 10-4.
TimeUnit 和 ChronoUnit 示例
```

## 队列

以下新方法 `forEach(Consumer<? super E> action)`、`removeAll(Collection<?> c)`、`removeIf(Predicate<? super E> filter)` 和 `retainAll(Collection<?> c)` 已被添加到以下类中：

*   `java.util.concurrent.ArrayBlockingQueue`
*   `java.util.concurrent.ConcurrentLinkedDeque`
*   `java.util.concurrent.ConcurrentLinkedQueue`
*   `java.util.concurrent.LinkedBlockingDeque`
*   `java.util.concurrent.LinkedBlockingQueue`
*   `java.util.concurrent.LinkedTransferQueue`

清单 10-5 展示了如何在不同的队列实现中使用这些新方法的一些示例。

```
public class QueueTest {
@Test
public void testForEach() {
final ArrayBlockingQueue queue = new ArrayBlockingQueue(3);
queue.offer(1);
queue.offer(2);
queue.offer(3);
queue.forEach(System.out::println);
}
@Test
public void testRemoveAll() {
final LinkedBlockingQueue queue = new LinkedBlockingQueue(3);
queue.offer(1);
queue.offer(2);
queue.removeAll(List.of(1));
assertEquals(1, queue.size());
}
@Test
public void testRemoveIf() {
final ConcurrentLinkedQueue queue = new ConcurrentLinkedQueue();
queue.offer(1);
queue.offer(2);
queue.offer(3);
queue.removeIf(i -> i % 2 == 0);
assertEquals(2, queue.size());
}
@Test
public void testRetainAll() {
final LinkedBlockingDeque deque = new LinkedBlockingDeque(3);
deque.offer(1);
deque.offer(2);
deque.offer(3);
deque.retainAll(List.of(1));
assertEquals(1, deque.size());
}
}
清单 10-5.
队列中新方法的示例
```

## 原子类

`VarHandle` 类中与内存访问模式相关的那些新方法也被添加到了 `java.util.concurrent.atomic` 包中的现有原子类中，包括 `AtomicBoolean`、`AtomicInteger`、`AtomicIntegerArray`、`AtomicLong`、`AtomicLongArray`、`AtomicReference` 和 `AtomicReferenceArray`。这些方法具有与 `VarHandle` 中相应方法相同的内存语义。

*   `compareAndExchange()`
*   `compareAndExchangeAcquire()`
*   `compareAndExchangeRelease()`
*   `weakCompareAndSetVolatile()`
*   `weakCompareAndSetAcquire()`
*   `weakCompareAndSetRelease()`
*   `weakCompareAndSetPlain()`
*   `getAcquire()`
*   `getOpaque()`
*   `getPlain()`
*   `setOpaque()`
*   `setPlain()`
*   `setRelease()`

清单 10-6 展示了如何使用这些新方法的一些示例。

```
public class AtomicTest {
@Test
public void testGetAcquire() {
final AtomicBoolean value = new AtomicBoolean();
value.setRelease(false);
assertEquals(false, value.getAcquire());
}
@Test
public void testCompareAndExchange() {
final AtomicInteger value = new AtomicInteger(10);
final int returned = value.compareAndExchange(10, 5);
assertEquals(10, returned);
assertEquals(5, value.getPlain());
}
}
清单 10-6.
原子类中新方法的示例
```



## Thread.onSpinWait

当您使用线程执行任务时，通常需要任务等待某个条件满足后才能继续。通常的做法是使用一个 `volatile` 变量作为标志位。该标志位由另一个线程设置，以停止等待。在 Java 9 之前，您使用一个空循环来等待条件。Java 9 中 `Thread` 类新增的静态方法 `onSpinWait()` 可以使等待更高效。

清单 10-7 展示了 `Thread.onSpinWait()` 的一个示例。`NormalTask` 和 `SpinWaitTask` 类都使用 `volatile` 布尔变量 `canStart` 作为标志位。该标志位通过 `start()` 方法设置为 `true`。这两个类的区别在于 `SpinWaitTask` 在循环中使用了 `Thread.onSpinWait()`。这里我使用另一个线程在三秒后调用 `start()` 方法来停止等待。

```
public class ThreadOnSpinWait {
public static void main(final String[] args) throws InterruptedException {
final NormalTask task1 = new NormalTask();
final SpinWaitTask task2 = new SpinWaitTask();
final Thread thread1 = new Thread(task1);
thread1.start();
final Thread thread2 = new Thread(task2);
thread2.start();
new Thread(() -> {
try {
Thread.sleep(3000);
} catch (final InterruptedException e) {
e.printStackTrace();
} finally {
task1.start();
task2.start();
}
}).start();
thread1.join();
thread2.join();
}
private abstract static class Task implements Runnable {
volatile boolean canStart;
void start() {
this.canStart = true;
}
}
private static class NormalTask extends Task {
@Override
public void run() {
while (!this.canStart) {
}
System.out.println("Done!");
}
}
private static class SpinWaitTask extends Task {
@Override
public void run() {
while (!this.canStart) {
Thread.onSpinWait();
}
System.out.println("Done!");
}
}
}
清单 10-7.
Thread.onSpinWait() 示例
```

## 本章小结

在本章中，我们讨论了 Java 9 中与并发相关的变更，包括 `CompletableFuture` 中的异步任务执行和超时支持、队列和原子类新增的方法，以及新方法 `Thread.onSpinWait()`。在下一章中，我们将讨论 JavaScript 引擎 Nashorn 的变更。

