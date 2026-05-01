# 7. 响应式流

响应式编程（[`https://en.wikipedia.org/wiki/Reactive_programming`](https://en.wikipedia.org/wiki/Reactive_programming)）近年来在 Java 社区中越来越受欢迎。在 Java 世界中，我们有流行的响应式库，如 RxJava（[`https://github.com/ReactiveX/RxJava`](https://github.com/ReactiveX/RxJava)）和 Reactor（[`http://projectreactor.io/`](http://projectreactor.io/)）。响应式流（[`http://www.reactive-streams.org/`](http://www.reactive-streams.org/)）是一项旨在为具有非阻塞背压的异步流处理提供标准的倡议。响应式流规范中的核心接口已被添加到 Java 9 的 `java.util.concurrent.Flow` 类中。

## 核心接口

本节介绍 `Flow` 中的四个核心接口。

### Flow.Publisher<T>

接口 `Flow.Publisher<T>` 表示供订阅者接收的项目的生产者。它只有一个方法 `void subscribe(Flow.Subscriber<? super T> subscriber)`，用于添加类型为 `Flow.Subscriber` 的订阅者。发布者可以发布三种类型的通知；参见表 7-1。

表 7-1.

`Publisher` 的通知

| 通知 | 描述 |
| --- | --- |
| `onNext` | 已发布一个新项目。 |
| `onComplete` | 将不再发布进一步的通知。 |
| `onError` | 发布项目时遇到错误。 |

`onComplete` 和 `onError` 都是终止通知，这意味着在 `onComplete` 或 `onError` 通知之后将不再发布任何通知。发布者不发布任何 `onNext` 通知是合法的。发布者也可以发布无限数量的 `onNext` 通知。



### Flow.Subscriber<T>

`Flow.Subscriber<T>` 接口表示由 `Flow.Publisher` 发布的通知的接收方。它包含四个方法，详见表 7-2。

表 7-2.

`Flow.Subscriber` 的方法

| 方法 | 描述 |
| --- | --- |
| `void onNext(T item)` | 当接收到一个项目时调用 |
| `void onComplete()` | 当接收到 `onComplete` 通知时调用 |
| `void onError(Throwable throwable)` | 当接收到 `onError` 通知时调用 |
| `void onSubscribe(Flow.Subscription subscription)` | 当订阅者成功订阅发布者时调用 |

如果订阅者无法订阅发布者，则会调用 `onError()` 方法并传入错误信息。

### Flow.Subscription

`Flow.Subscription` 接口表示当 `Flow.Subscriber` 成功订阅 `Flow.Publisher` 时的订阅关系。它作为参数传递给订阅者的 `onSubscribe()` 方法。表 7-3 展示了 `Flow.Subscription` 的方法。

表 7-3.

`Flow.Subscription` 的方法

| 方法 | 描述 |
| --- | --- |
| `void request(long n)` | 请求发布 `n` 个项目。 |
| `void cancel()` | 取消订阅。 |

订阅者只有在发出请求后才会接收项目。`request(long n)` 方法向发布者提出需求，表明订阅者已准备好处理 `n` 个项目。这一点很重要，因为它能确保订阅者不会被发布者压垮。如果发布者提前终止，订阅者接收到的项目数量可能少于请求的数量。调用 `cancel()` 方法后，订阅者仍可能收到额外的通知，但订阅最终会被取消。

### Flow.Processor<T,R>

`Flow.Processor` 接口表示一个同时充当订阅者和发布者的组件。清单 7-1 展示了其定义。它简单地同时继承了 `Subscriber<T>` 和 `Publisher<R>`。

```
public static interface Processor extends Subscriber, Publisher {
}
清单 7-1.
Flow.Processor 的定义
```

## SubmissionPublisher

虽然这四个核心接口易于理解，但要正确实现它们却并不容易。不过，你实际上无需关心具体实现，只需依赖第三方库即可。

`java.util.concurrent.SubmissionPublisher<T>` 类是 `Flow.Publisher` 的一个实现，它会异步地将提交的项目发布给订阅者，直到被关闭。`SubmissionPublisher` 的项目并非由发布者生成，而是由客户端代码提交的。`SubmissionPublisher` 可以充当一个桥梁，客户端代码可通过它与订阅者通信。

`SubmissionPublisher` 使用 `Executor` 将项目发送给订阅者。`Executor` 可以在构造函数中提供，否则默认使用 `ForkJoinPool.commonPool()`。当任何订阅者方法抛出错误时，你可以在订阅被取消之前提供一个处理器来处理该异常。`SubmissionPublisher` 有三个构造函数。

*   无参构造函数使用默认的 `Executor`，且没有异常处理器。
*   `SubmissionPublisher(Executor executor, int maxBufferCapacity)` 配置 `Executor` 和缓冲区的最大大小。缓冲区包含已提交但尚未被订阅者消费的项目。每个订阅者都有自己独立的缓冲区。
*   `SubmissionPublisher(Executor executor, int maxBufferCapacity, BiConsumer<? super Flow.Subscriber<? super T>,? super Throwable> handler)` 配置 `Executor`、缓冲区的最大大小以及异常处理器。

注意

最大缓冲区大小的实际值可能会向上取整到最接近的 2 的幂次，或者受限于实现支持的最大值。例如，如果向构造函数传递 `10` 作为 `maxBufferCapacity` 的值，实际使用的值将是 `16`。我们可以使用 `getMaxBufferCapacity()` 方法来获取实际值。

由于订阅者消费已发布项目的速度可能不同，因此某些项目可能尚未被所有订阅者消费。`estimateMaximumLag()` 方法返回一个估计值，表示已发布但尚未被所有订阅者消费的最大项目数量。另一方面，项目发布的速度也可能跟不上订阅者的消费速度。在这种情况下，`estimateMinimumDemand()` 方法返回一个估计值，表示已请求但尚未发布的最小项目数量。

要向 `SubmissionPublisher` 提交项目，可以使用 `submit()` 和 `offer()` 方法。`submit(T item)` 方法通过调用所有订阅者的 `onNext()` 方法，异步地将项目发布给所有订阅者。当任何订阅者没有可用资源来处理时，`submit()` 会不可中断地阻塞。当你希望确保项目成功发布给所有订阅者时，应使用 `submit()`。然而，其阻塞行为可能会带来性能损失。一个不堪重负的订阅者可能会拖慢其他订阅者的处理速度。`submit()` 的返回值是最大延迟的估计值，与 `estimateMaximumLag()` 方法的返回值相同。



方法 `int offer(T item, BiPredicate<Flow.Subscriber<? super T>,? super T> onDrop)` 和 `int offer(T item, long timeout, TimeUnit unit, BiPredicate<Flow.Subscriber<? super T>,? super T> onDrop)` 同样会将项目发布给所有订阅者，但当某个或多个订阅者资源不可用时，它们允许丢弃项目。当项目被丢弃时，提供的处理器 `onDrop` 会被调用，并传入当前订阅者和被丢弃的项目。该处理器的类型是 `BiPredicate`，因此它返回一个布尔值。如果处理器的返回值为 `true`，发布者将尝试重新发布该项目。`offer()` 的返回值表示发布的结果。如果该值为负数，其绝对值表示丢弃的数量；否则，它表示最大延迟的估计值，含义与 `submit()` 返回的值相同。如果在调用 `offer()` 时添加了超时时间，则当任何订阅者都没有可用资源时，它会阻塞，并持续阻塞直到指定的超时时间，或直到调用线程被中断。在这两种情况下，都会调用 `onDrop` 处理器。

表 7-4 展示了 `SubmissionPublisher` 的其他重要方法。

表 7-4.

`SubmissionPublisher` 的其他方法

| 方法 | 描述 |
| --- | --- |
| `CompletableFuture<Void> consume(Consumer<? super T> consumer)` | 使用给定的消费者处理所有已发布的项目。返回值是一个 `CompletableFuture<Void>`，当发布者发出 `onComplete` 信号时正常完成，或在发生任何错误时异常完成。 |
| `void close()` | 关闭发布者，并向所有订阅者发出 `onComplete` 信号。 |
| `void closeExceptionally(Throwable error)` | 关闭发布者，并向所有订阅者发出带有给定错误的 `onError` 信号。 |
| `boolean isClosed()` | 检查发布者是否已关闭。 |
| `List<Flow.Subscriber<? super T>> getSubscribers()` | 返回所有订阅者的列表。 |
| `int getNumberOfSubscribers()` | 返回订阅者的数量。 |
| `boolean hasSubscribers()` | 检查发布者是否有任何订阅者。 |
| `boolean isSubscribed(Flow.Subscriber<? super T> subscriber)` | 检查给定的订阅者是否已订阅。 |

现在你可以使用 `SubmissionPublisher` 来发布一些数据。在清单 7-2 中，`PeriodicPublisher` 类定期发布给定数量的项目，并在所有项目发布完毕后关闭。构造函数参数 `Consumer<PeriodicPublisher<T>> action` 用于发布项目。方法 `waitForCompletion()` 可用于等待所有项目发布完毕。

```
public class PeriodicPublisher extends SubmissionPublisher {
private final ScheduledExecutorService scheduler;
private final ScheduledFuture periodicTask;
private final AtomicInteger count = new AtomicInteger(0);
private final CountDownLatch closeLatch = new CountDownLatch(1);
public PeriodicPublisher(final Consumer> action,
final int maxBufferCapacity,
final int total,
final long period,
final TimeUnit timeUnit) {
super(ForkJoinPool.commonPool(),
maxBufferCapacity,
((subscriber, throwable) ->
System.out.printf("Publish error for %s: %s",
subscriber, throwable)));
this.scheduler = new ScheduledThreadPoolExecutor(1);
this.periodicTask = this.scheduler.scheduleAtFixedRate(
() -> {
action.accept(this);
final int value = this.count.incrementAndGet();
if (value >= total) {
this.doClose();
}
}, 0, period, timeUnit);
}
@Override
public void close() {
this.periodicTask.cancel(false);
this.scheduler.shutdown();
super.close();
}
public void waitForCompletion() {
try {
this.closeLatch.await();
} catch (final InterruptedException e) {
this.close();
}
}
private void doClose() {
try {
this.close();
} finally {
this.closeLatch.countDown();
}
}
}
清单 7-2.
PeriodicPublisher
```

你可以使用 `PeriodicPublisher` 创建一个随机数生成器；参见清单 7-3。在构造函数中传入的操作是 `publisher.submit(ThreadLocalRandom.current().nextLong()`，它使用 `submit()` 方法发布一个随机数。

```
public class RandomNumberGenerator extends PeriodicPublisher {
public RandomNumberGenerator() {
super((publisher) ->
publisher.submit(ThreadLocalRandom.current().nextLong()),
Flow.defaultBufferSize(),
10,
1,
TimeUnit.SECONDS);
}
public static void main(final String[] args) {
final RandomNumberGenerator generator = new RandomNumberGenerator();
generator.subscribe(new Flow.Subscriber() {
@Override
public void onSubscribe(final Flow.Subscription subscription) {
subscription.request(Long.MAX_VALUE);
}
@Override
public void onNext(final Long item) {
System.out.printf("Received: %s%n", item);
}
@Override
public void onError(final Throwable throwable) {
throwable.printStackTrace();
}
@Override
public void onComplete() {
System.out.println("Completed!");
}
});
generator.waitForCompletion();
}
}
清单 7-3.
随机数生成器
```

现在我将向你展示 `submit()` 和 `offer()` 之间的区别。在清单 7-4 中，我使用了三种不同的方法来发布项目。`DelayedSubscriber` 类在处理每个项目之前会延迟 `100ms`。发布者使用一个最大大小为 `16` 的缓冲区。总共 `50` 个序列号以 `50ms` 的间隔发布。我使用 `waitForCompletion()` 等待发布完成，然后再等待 5 秒，以便处理缓冲的项目。当我使用 `submit()` 发布项目时，所有项目都能被处理。当我使用不带超时的 `offer()` 发布项目时，部分项目会被丢弃。这是因为缓冲区大小只有 `16`，并且处理单个项目需要 `100ms`。在此期间，会生成两个项目。在所有项目被处理之前，缓冲区就已填满，因此其中一些项目被丢弃。`offer()` 的丢弃处理器返回 `true`，所以发布者会尝试重新发布。当你运行代码时，由于其并发特性，被丢弃的项目可能会有所不同。当我使用带有一秒超时的 `offer()` 发布项目时，由于超时时间远长于处理时间，所有项目都能被处理。



```
public class DelayedSubscribers {
public static void main(final String[] args) {
final DelayedSubscribers delayedSubscribers = new DelayedSubscribers();
delayedSubscribers.publishWithSubmit();
System.out.println("===========");
delayedSubscribers.publishWithOffer();
System.out.println("===========");
delayedSubscribers.publishWithOfferTimeout();
}
public void publishWithSubmit() {
final SequenceGenerator sequenceGenerator = new SequenceGenerator();
this.publish(publisher -> publisher.submit(sequenceGenerator.get()));
}
public void publishWithOffer() {
final SequenceGenerator sequenceGenerator = new SequenceGenerator();
this.publish(publisher -> publisher.offer(sequenceGenerator.get(),
((subscriber, value) -> {
System.out.printf("%s dropped %s%n", subscriber, value);
return true;
})));
}
public void publishWithOfferTimeout() {
final SequenceGenerator sequenceGenerator = new SequenceGenerator();
this.publish(publisher ->
publisher.offer(
sequenceGenerator.get(),
1000,
TimeUnit.MILLISECONDS,
((subscriber, value) -> {
System.out.printf("%s dropped %s%n", subscriber, value);
return true;
})
));
}
private void publish(final Consumer> action) {
final PeriodicPublisher publisher =
new PeriodicPublisher(
action,
16,
50,
50,
TimeUnit.MILLISECONDS);
publisher.subscribe(new DelayedSubscriber("1"));
publisher.subscribe(new DelayedSubscriber("2"));
publisher.subscribe(new DelayedSubscriber("3"));
publisher.waitForCompletion();
System.out.println("Publish completed");
try {
Thread.sleep(5000);
} catch (final InterruptedException e) {
e.printStackTrace();
}
}
public static class SequenceGenerator implements Supplier {
private int count = 1;
@Override
public Integer get() {
return this.count++;
}
}
public static class DelayedSubscriber implements Flow.Subscriber {
private final String id;
private Flow.Subscription subscription;
public DelayedSubscriber(final String id) {
this.id = id;
}
@Override
public void onSubscribe(final Flow.Subscription subscription) {
this.subscription = subscription;
System.out.printf("%s subscribed!%n", this.id);
subscription.request(1);
}
@Override
public void onNext(final T item) {
this.subscription.request(1);
try {
Thread.sleep(100);
} catch (final InterruptedException e) {
e.printStackTrace();
}
System.out.printf("%s processed: %s%n", this.id, item);
}
@Override
public void onError(final Throwable throwable) {
throwable.printStackTrace();
}
@Override
public void onComplete() {
System.out.printf("%s completed!%n", this.id);
}
@Override
public String toString() {
return String.format("Subscriber %s", this.id);
}
}
}
清单 7-4. submit() 与 offer() 的区别
```

## 第三方库

如果你之前使用过 RxJava 或 Reactor，你会发现相比之下，`Flow` 提供的接口相当精简。Reactive Streams 规范的设计初衷就是保持最小化，并专注于不同库之间的互操作性。你可以将 `Flow` 与 RxJava 和 Reactor 集成。

### RxJava 2

你可以使用 RxJava2Jdk9Interop 库（[`https://github.com/akarnokd/RxJava2Jdk9Interop`](https://github.com/akarnokd/RxJava2Jdk9Interop)）进行转换。清单 7-5 展示了如何在 RxJava 2 的 `Flowable` 和 `Flow` 之间进行转换。

```
import hu.akarnokd.rxjava2.interop.FlowInterop;
import io.reactivex.Flowable;
import java.util.concurrent.TimeUnit;
public class RxJava2 {
public void toFlow() {
Flowable.interval(0, 50, TimeUnit.MILLISECONDS)
.take(50)
.to(FlowInterop.toFlow())
.subscribe(new DelayedSubscribers.DelayedSubscriber("1"));
}
public void fromFlow() {
final DelayedSubscribers.SequenceGenerator sequenceGenerator =
new DelayedSubscribers.SequenceGenerator();
final PeriodicPublisher publisher =
new PeriodicPublisher(
pub -> pub.submit(sequenceGenerator.get()),
16,
50,
50,
TimeUnit.MILLISECONDS);
FlowInterop.fromFlowPublisher(publisher)
.map(v -> v * 10)
.forEach(System.out::println);
}
public static void main(final String[] args) {
final RxJava2 rxJava2 = new RxJava2();
rxJava2.toFlow();
rxJava2.fromFlow();
try {
Thread.sleep(10000);
} catch (final InterruptedException e) {
e.printStackTrace();
}
}
}
清单 7-5. RxJava 2 与 Flow
```

### Reactor

Reactor 内置了针对 `Flow` 的适配器。清单 7-6 展示了如何在 Reactor 的 `Flux` 和 `Flow` 之间进行转换。

```
import java.time.Duration;
import java.util.concurrent.TimeUnit;
import reactor.adapter.JdkFlowAdapter;
import reactor.core.publisher.Flux;
public class Reactor {
public void toFlow() {
JdkFlowAdapter.publisherToFlowPublisher(
Flux.interval(Duration.ZERO, Duration.ofMillis(50))
.take(50)
).subscribe(new DelayedSubscribers.DelayedSubscriber("1"));
}
public void fromFlow() {
final DelayedSubscribers.SequenceGenerator sequenceGenerator =
new DelayedSubscribers.SequenceGenerator();
final PeriodicPublisher publisher =
new PeriodicPublisher(
pub -> pub.submit(sequenceGenerator.get()),
16,
50,
50,
TimeUnit.MILLISECONDS);
JdkFlowAdapter.flowPublisherToFlux(publisher)
.map(v -> v * 10)
.subscribe(System.out::println);
}
public static void main(final String[] args) {
final Reactor reactor = new Reactor();
reactor.toFlow();
reactor.fromFlow();
try {
Thread.sleep(10000);
} catch (final InterruptedException e) {
e.printStackTrace();
}
}
}
清单 7-6. Reactor 与 Flow
```

### 互操作性

清单 7-7 展示了如何使用 `Flow` 将 RxJava 2 的 `Flowable` 转换为 Reactor 的 `Flux`。

```
import hu.akarnokd.rxjava2.interop.FlowInterop;
import io.reactivex.Flowable;
import java.util.concurrent.Flow;
import java.util.concurrent.TimeUnit;
import reactor.adapter.JdkFlowAdapter;
public class RxInterop {
public static void main(final String[] args) {
final Flow.Publisher publisher =
Flowable.interval(0, 50, TimeUnit.MILLISECONDS)
.take(50)
.to(FlowInterop.toFlow());
JdkFlowAdapter.flowPublisherToFlux(publisher)
.map(v -> v * 10)
.toStream()
.forEach(System.out::println);
}
}
清单 7-7. RxJava 2 与 Reactor 之间的互操作性
```

## 小结

将 Reactive Streams 规范引入 Java 9，是 Java 平台拥抱响应式编程原则的重要一步。在本章中，我们讨论了 `Flow` 中的核心接口以及内置的 `SubmissionPublisher`。我还演示了它与流行的响应式库 RxJava 2 和 Reactor 的互操作性。在下一章中，我们将讨论变量句柄。

