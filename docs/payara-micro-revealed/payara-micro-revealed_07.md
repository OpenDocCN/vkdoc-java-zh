# 7. 高可用性与容错性

在使用微服务架构开发应用时，我们通常会有许多相互依赖的服务。其中一个或多个服务可能会宕机，并可能拖垮整个系统。MicroProfile Fault Tolerance API 提供了我们可以用来减轻这种风险的功能，它提供了几个注解，我们可以用它们来配置当一个或多个微服务不可用或无法正常工作时系统的行为方式。



## 异步调用 RESTful Web 服务端点

MicroProfile Fault Tolerance 提供了 `@Asynchronous` 注解，允许以异步方式调用 RESTful Web 服务端点。这使得客户端在调用可能耗时较长的服务时，可以继续处理其他任务而无需阻塞。当客户端需要调用多个可能各自需要一段时间才能返回结果的服务时，这一点尤其有用；通过异步调用这些服务，它们可以并行执行，从而减少客户端等待获取结果所需的时间。

`@Asynchronous` 注解可以应用于请求作用域 CDI Bean 中的方法；该方法必须返回 `Future` 或 `CompletionStage` 接口的实现。推荐使用 `CompletionStage` 作为返回类型，因为在这种情况下，如果被调用的方法抛出异常，应用于该方法的其他容错注解仍然会生效；而对于返回 `Future` 的方法则并非如此。

回想一下，我们只需应用一个作用域注解（例如 `@RequestScoped`），就可以将任何 RESTful Web 服务转变为 CDI Bean。

以下示例说明了如何指示我们的端点可以被异步调用：

```
package com.ensode.faulttolerance;
//imports omitted
@RequestScoped
@Path("faulttoleranceexample")
public class FaulToleranceExampleResource {
@Asynchronous
@GET
@Path("async")
@Produces(MediaType.TEXT_PLAIN)
public CompletionStage getAsynchronousValue()
throws InterruptedException {
TimeUnit.SECONDS.sleep(5);
return CompletableFuture.completedStage(18);
}
@Asynchronous
@GET
@Path("async2")
@Produces(MediaType.TEXT_PLAIN)
public CompletionStage getAnotherAsynchronousValue()
throws InterruptedException {
TimeUnit.SECONDS.sleep(7);
return CompletableFuture.completedStage(24);
}
}
```

正如我们在示例中所见，我们只需用 `@Asynchronous` 注解任何需要异步调用的方法，并确保该方法返回 `Future` 或 `CompletionStage`；当客户端调用我们的异步方法时，控制权会立即返回给客户端；客户端不会阻塞等待结果。

当使用 MicroProfile REST 客户端 API 开发客户端时，我们将服务上使用的 `@Asynchronous` 注解应用到我们的 REST 客户端接口方法上。

```
package com.ensode.fault.toleranceclient;
//imports omitted
@RegisterRestClient
@Path("faulttoleranceexample")
public interface FaultToleranceExampleResourceClient {
@Asynchronous
@GET
@Path("async")
@Produces(MediaType.TEXT_PLAIN)
public CompletionStage getAsynchronousValue()
throws InterruptedException;
@Asynchronous
@GET
@Path("async2")
@Produces(MediaType.TEXT_PLAIN)
public CompletionStage getAnotherAsynchronousValue()
throws InterruptedException;
}
```

然后，作为异步方法客户端的服务将照常使用我们的客户端接口。

```
package com.ensode.fault.toleranceclient;
//imports omitted
@Path("/faulttoleranceclient")
public class FaultToleranceClientService {
@Inject
@RestClient
private FaultToleranceExampleResourceClient client;
@GET
@Produces(MediaType.TEXT_PLAIN)
public String get() throws InterruptedException,
ExecutionException {
Integer answer;
Integer value1;
Integer value2;
String retVal;
CompletionStage asynchronousValue =
client.getAsynchronousValue();
CompletionStage asynchronousValue2 =
client.getAnotherAsynchronousValue();
value1 = asynchronousValue.toCompletableFuture().get();
value2 = asynchronousValue2.toCompletableFuture().get();
answer = value1 + value2;
retVal = String.format("The answer is %d\n", answer);
return retVal;
}
}
```

其中一个被调用的方法大约需要五秒钟才能返回值；另一个大约需要七秒钟。如果我们同步调用这些方法，客户端将需要等待大约 12 秒才能获得两个结果；由于我们是异步调用它们（它们并行运行），客户端只需阻塞大约七秒钟即可获得两个结果。

## 限制并发执行以避免系统过载

MicroProfile Fault Tolerance 提供了 `@Bulkhead` 注解，其名称来源于舱壁设计模式，因为它使我们能够以最小的努力实现这种模式。`@Bulkhead` 可用于指定 RESTful Web 服务端点的最大并发实例数。

使用 `@Bulkhead` 注解有两种方式：信号量方式和线程池方式。

### 对同步端点使用信号量

使用信号量方式时，注解的 `value` 参数表示微服务端点的最大并发调用数；任何额外的并发调用尝试都会导致 `BulkheadException`。

以下示例说明了 `@Bulkhead` 注解的信号量方式用法：

```
package com.ensode.faulttolerance;
//imports omitted
@RequestScoped
@Path("faulttoleranceexample")
public class FaulToleranceExampleResource {
@Inject
private ConcurrentInvocationCounter concurrentInvocationCounter;
@POST
@Path("semaphorebulkhead")
@Bulkhead(3)
@Produces(MediaType.TEXT_PLAIN)
public String semaphoreBulkHeadDemo() throws
InterruptedException {
String retVal;
concurrentInvocationCounter.increaseCounter();
retVal = String.format(
"There are %d concurrent invocations to this endpoint \n",
concurrentInvocationCounter.getCounter());
TimeUnit.SECONDS.sleep(3);
concurrentInvocationCounter.decreaseCounter();
return retVal;
}
}
```

在我们的示例中，我们允许最多三个并发调用访问我们的 RESTful Web 服务端点；当已有三个并发调用时，任何尝试调用该端点的操作都将失败并抛出 `BulkheadException`；一旦并发调用数降至三个以下，该方法就可以再次成功调用。

以下示例代码说明了当我们生成的并发调用数超过 `@Bulkhead` 注解允许的数量时会发生什么：

```
package com.ensode.fault.toleranceclient;
//imports omitted
@Path("/faulttoleranceclient")
public class FaultToleranceClientService {
private static final Logger LOGGER =
Logger.getLogger(FaultToleranceClientService.class.getName());
@Inject
@RestClient
private FaultToleranceExampleResourceClient client;
@POST
@Path("semaphorebulkhead")
public void semaphoreBulkheadClient() throws
InterruptedException {
ExecutorService executorService =
Executors.newFixedThreadPool(4);
Callable semaphoreBulkheadCallable =
() -> client.semaphoreBulkHeadDemo();
List> callResults = executorService.invokeAll(
List.of(semaphoreBulkheadCallable,semaphoreBulkheadCallable,
semaphoreBulkheadCallable,semaphoreBulkheadCallable));
callResults.forEach(fut -> {
try {
LOGGER.log(Level.INFO, fut.get());
} catch (InterruptedException | ExecutionException ex) {
LOGGER.log(Level.SEVERE, String.format(
"%s caught", ex.getClass().getName()), ex);
}
});
}
}
```

借助并发工具 API，我们生成了四个线程，每个线程都调用我们服务上带有 `@Bulkhead` 注解的端点。由于我们指定了最多三个并发调用，最后一次调用将按预期失败并抛出 `BulkheadException`。

通常，会有多个并发客户端向一个服务发出并发请求；为简单起见，我们的示例使用单个客户端生成对服务的多个并发调用。

运行我们的客户端服务后，如果我们查看 Payara Micro 的输出，可以验证 `@Bulkhead` 注解是否按预期工作。



```
2021-10-18T18:20:18.284-0400] [] [INFO] [] [javax.enterprise.system.core] [tid: _ThreadID=89 _ThreadName=payara-executor-service-scheduled-task] [timeMillis: 1634595618284] [levelValue: 800] mp-fault-tolerance-example-client-1.0-SNAPSHOT 在 428 毫秒内成功部署。
[2021-10-18T18:32:40.472-0400] [] [INFO] [] [com.ensode.fault.toleranceclient.FaultToleranceClientService] [tid: _ThreadID=83 _ThreadName=http-thread-pool::http-listener(1)] [timeMillis: 1634597555867] [levelValue: 800] 此端点有 1 个并发调用
[2021-10-18T18:32:40.472-0400] [] [INFO] [] [INFO] [] [com.ensode.fault.toleranceclient.FaultToleranceClientService] [tid: _ThreadID=83 _ThreadName=http-thread-pool::http-listener(1)] [timeMillis: 1634597555867] [levelValue: 800] 此端点有 3 个并发调用
[2021-10-18T18:32:40.472-0400] [] [INFO] [[] [INFO] [] [com.ensode.fault.toleranceclient.FaultToleranceClientService] [tid: _ThreadID=83 _ThreadName=http-thread-pool::http-listener(1)] [timeMillis: 1634597555867] [levelValue: 800] 此端点有 2 个并发调用
[2021-10-18T18:32:40.472-0400] [] [SEVERE] [] [com.ensode.fault.toleranceclient.FaultToleranceClientService] [tid: _ThreadID=83 _ThreadName=http-thread-pool::http-listener(1)] [timeMillis: 1634596360472] [levelValue: 1000] [[
捕获到 java.util.concurrent.ExecutionException
java.util.concurrent.ExecutionException: org.eclipse.microprofile.faulttolerance.exceptions.BulkheadException: 没有空闲工作或队列空间。

原因: org.eclipse.microprofile.faulttolerance.exceptions.BulkheadException: 没有空闲工作或队列空间。
```

通过检查 Payara Micro 的输出，我们可以看到前三个并发调用成功了；第四个调用按预期生成了一个 `BulkheadException`。

我们无法控制线程调度；这由 JVM 和/或底层操作系统完成；因此，指示并发调用次数的输出可能与我们的预期不符；第三个线程中向日志文件发送输出的代码行在第二个线程的同一行代码之前执行；这就是为什么我们看到日志条目显示并发调用次数为 1、3 和 2，而不是 1、2 和 3。

### 为异步端点使用线程池

线程池风格的 `@Bulkhead` 仅限于异步端点使用；和之前一样，我们通过 `@Bulkhead` 注解的 `value` 属性指定端点允许的最大并发调用数；额外的调用会被放入线程池，并在端点并发调用数降至指定值以下后得到处理。我们可以通过 `@Bulkhead` 注解的 `waitingTaskQueue` 属性指定线程池中的最大调用数。

以下示例说明了线程池风格的 `@Bulkhead` 用法：

```
package com.ensode.faulttolerance;
//导入已省略
@RequestScoped
@Path("faulttoleranceexample")
public class FaulToleranceExampleResource {
@Inject
private ConcurrentInvocationCounter concurrentInvocationCounter;
@POST
@Path("threadpoolbulkhead")
@Asynchronous
@Bulkhead(value = 3, waitingTaskQueue = 2)
@Produces(MediaType.TEXT_PLAIN)
public CompletionStage threadPoolBulkheadExample(
@QueryParam("invocationNum") int invocationNum) throws
InterruptedException {
String retVal;
retVal = String.format("调用编号 %d 成功 \n",
invocationNum);
TimeUnit.SECONDS.sleep(3);
return CompletableFuture.completedStage(retVal);
}
}
```

如 `@Asynchronous` 注解所示，我们的端点是异步的；因此我们可以使用 `@Bulkhead` 线程池风格。在我们的示例中，我们允许最多三个并发调用，如 `@Bulkhead` 的 `value` 属性所示；我们还允许线程池中最多有两个线程，如注解的 `waitingTaskQueue` 属性所示。

生成超过五个并发请求（允许的三个加上线程池中允许的最多两个）应该会引发异常。以下 bash 脚本生成了六个并发请求：

```
#!/bin/bash
for i in {1..6}; do
curl -i -XPOST http://localhost:8080/faulttolerance/webresources/faulttoleranceexample/threadpoolbulkhead?invocationNum=$i &
done
```

在 bash shell 中执行上述脚本会产生以下输出：

```
HTTP/1.1 500 Request failed.
Server: Payara Micro #badassfish
Connection: close
Content-Length: 0
X-Frame-Options: SAMEORIGIN
HTTP/1.1 200 OK
Server: Payara Micro #badassfish
Content-Type: text/plain
Content-Length: 31
X-Frame-Options: SAMEORIGIN
调用编号 4 成功
HTTP/1.1 200 OK
Server: Payara Micro #badassfish
Content-Type: text/plain
Content-Length: 31
X-Frame-Options: SAMEORIGIN
调用编号 3 成功
HTTP/1.1 200 OK
Server: Payara Micro #badassfish
Content-Type: text/plain
Content-Length: 31
X-Frame-Options: SAMEORIGIN
调用编号 2 成功
HTTP/1.1 200 OK
Server: Payara Micro #badassfish
Content-Type: text/plain
Content-Length: 31
X-Frame-Options: SAMEORIGIN
调用编号 5 成功
HTTP/1.1 200 OK
Server: Payara Micro #badassfish
Content-Type: text/plain
Content-Length: 31
X-Frame-Options: SAMEORIGIN
调用编号 6 成功
```

有趣的是，输出显示第一个请求失败，而请求 2 到 5 成功；这是因为我们无法控制线程调度；在这种情况下，第一个请求最后被处理，这就是它失败的原因；它也是立即失败的，而其他请求则花了三秒钟处理；这就是为什么尽管第一个请求最后执行，但我们首先看到它的输出。



## 停止重复调用频繁失败的端点

`@CircuitBreaker` 注解允许我们停止调用超过失败阈值的端点；默认情况下，如果对某个端点的最近 20 次调用中至少有 50% 失败，则对该方法的调用将停止；相反，MicroProfile 运行时会抛出 `CircuitBreakerOpenException`。我们可以分别通过 `@CircuitBreaker` 的 `requestVolumeThreshold` 和 `failureRatio` 属性来指定用于计算的调用次数和失败请求的比例。

经过指定的延迟（默认为 500 毫秒）后，断路器会进入半开状态，这意味着允许一定数量的请求（默认为一个请求）通过并执行操作；如果这些请求成功，断路器会自动关闭，允许后续请求访问该端点；如果这些请求中至少有一个失败，断路器会重新打开，阻止对该端点的任何进一步调用。我们可以通过 `@CircuitBreaker` 的 `delay` 属性指定半开前的延迟时间，并通过其 `delayUnit` 属性指定延迟的时间单位；默认时间单位为毫秒。

以下示例说明了如何使用 `@CircuitBreaker` 注解：

```java
package com.ensode.faulttolerance;
//imports omitted
@RequestScoped
@Path("faulttoleranceexample")
public class FaulToleranceExampleResource {
@Inject
private ConcurrentInvocationCounter concurrentInvocationCounter;
@CircuitBreaker(requestVolumeThreshold = 3, failureRatio = .66
delay = 1, delayUnit = ChronoUnit.SECONDS, successThreshold = 2)
@POST
@Produces(MediaType.TEXT_PLAIN)
@Path("circuitbreaker")
public String circuitBreakerExample(@QueryParam("success") boolean success) {
if (success == false) {
throw new RuntimeException("forcing a failure for demo purposes");
} else {
return "Call succeeded";
}
}
}
```

为了便于说明，在我们的示例中，当收到值为 `false` 的查询参数时，我们会故意抛出一个异常。在我们的端点中，我们使用对端点的最近三次调用来计算阈值，这由 `@CircuitBreaker` 的 `requestVolumeThreshold` 属性指定。我们通过 `failureRatio` 属性将失败调用的比例指定为 .66；在这种情况下，由于请求量阈值为 3，失败比例为 .66，因此当连续三次调用中有两次失败时，断路器将打开。

在我们的示例中，我们指定了半开断路器前的延迟为一秒；秒数在 `@CircuitBreaker` 的 `delay` 属性中指定，时间单位在相应的 `delayUnit` 属性中指定。

现在，让我们编写一个客户端服务，向我们的端点发送多个请求，以便验证 `@CircuitBreaker` 注解是否按预期工作。

```java
package com.ensode.fault.toleranceclient;
//imports omitted
@Path("/faulttoleranceclient")
public class FaultToleranceClientService {
private static final Logger LOGGER = Logger.getLogger(FaultToleranceClientService.class.getName());
@Inject
@RestClient
private FaultToleranceExampleResourceClient client;
@POST
@Path("circuitbreaker")
public void circuitBreakerClient() throws InterruptedException {
try {
LOGGER.log(Level.INFO, client.circuitBreakerExample(true));
} catch (RuntimeException re) {
LOGGER.log(Level.SEVERE, re.getMessage());
}
try {
LOGGER.log(Level.INFO, client.circuitBreakerExample(false));
} catch (RuntimeException re) {
LOGGER.log(Level.SEVERE, re.getMessage());
}
try {
LOGGER.log(Level.INFO, client.circuitBreakerExample(false));
} catch (RuntimeException re) {
LOGGER.log(Level.SEVERE, re.getMessage());
}
//circuit opens
try {
LOGGER.log(Level.INFO, client.circuitBreakerExample(true));
//call fails because the circuit is open
} catch (CircuitBreakerOpenException e) {
LOGGER.log(Level.SEVERE, "Circuit breaker is open", e);
}
//Wait one second, circuit is now half open, call succeeds.
TimeUnit.SECONDS.sleep(1);
try {
LOGGER.log(Level.INFO, client.circuitBreakerExample(true));
//call succeeds because the circuit is half open
} catch (RuntimeException re) {
LOGGER.log(Level.SEVERE, re.getMessage());
}
//circuit breaker is now closed
}
}
```

回想一下，在我们的服务中，当收到值为 `false` 的 `success` 路径参数时，我们会故意抛出一个异常；因此，第一次调用服务成功，接下来的两次失败；由于达到了失败请求的阈值，断路器打开。

下一次对服务的调用失败了，即使我们传递了 `true` 值；失败的原因是断路器现在处于打开状态。在指定的延迟一秒后，断路器变为半开状态；因此，允许调用到达端点；我们客户端的最后一次调用成功。由于我们通过 `@CircuitBreaker` 的 `successThreshold` 属性指定了成功阈值为 2，此时断路器保持半开状态；如果下一个请求成功，断路器将关闭；如果失败，它将重新打开。

在继续之前，最后要提一点，默认情况下，如果端点抛出任何异常，`@CircuitBreaker` 都会增加失败调用的计数器。如果我们希望将断路器功能限制在特定的异常集合上，可以通过 `@CircuitBreaker` 的 `failOn` 属性来实现；该属性接受一个 `Exception` 子类的数组作为其值，例如：

```java
@CircuitBreker(failOn={ReallyBadException.class, EvenWorseException.class})
```

类似地，我们可以通过 `@CircuitBreaker` 的 `skipOn` 属性指定要忽略的异常。

```java
@CircuitBreaker(skipOn={DumbException.class, IsThisEvenAnIssueException.class})
```



## 当执行失败时提供替代方案

默认情况下，RESTful Web 服务端点抛出的任何异常都会导致客户端抛出异常；但是，我们可以通过 `@Fallback` 注解优雅地从错误中恢复。使用 `@FallBack` 时，我们可以指定一个替代方法，该方法将在失败端点执行时被调用；此方法可以通过 `@Fallback` 的 `fallbackMethod` 属性指定，如下例所示：

```
package com.ensode.faulttolerance;
//imports omitted
@RequestScoped
@Path("faulttoleranceexample")
public class FaulToleranceExampleResource {
@Fallback(fallbackMethod = "fallbackMethod")
@POST
@Produces(MediaType.TEXT_PLAIN)
@Path("fallback")
public String fallbackExample(@QueryParam("success") boolean success) {
if (success == false) {
throw new RuntimeException(
"forcing a failure for demo purposes");
} else {
return "Call succeeded";
}
}
private String fallbackMethod(boolean success) {
return "Something went wrong";
}
}
```

要指定一个回退方法，我们只需将方法名作为字符串赋给 `@Fallback` 的 `fallbackMethod` 属性；回退方法必须与可能失败的端点具有相同的返回类型和参数；请注意在我们的示例中，实现端点的方法和回退方法都返回一个 `String` 并接受一个 `boolean` 参数；通常，端点和相应回退方法的返回值类型和参数类型必须匹配。

如果端点调用成功，则不会发生任何异常情况；如果调用失败，则会调用回退方法。例如，如果我们向示例中定义的端点发送请求并且该方法失败，我们将转而获得回退方法的输出。

```
$ curl -XPOST http://localhost:8080/faulttolerance/webresources/faulttoleranceexample/fallback?success=false
Something went wrong
```

如果我们希望在端点失败时实现替代功能，使用回退方法非常棒；但是，如果我们希望获取关于失败端点的一些诊断信息呢？在这种情况下，我们可以通过 `@Fallback` 的 `value` 属性指定一个回退处理器类。回退处理器必须是一个实现了 `FallbackHandler` 接口的类；该接口有一个名为 `handle()` 的抽象方法；此方法接受一个 `ExecutionContext` 实例作为参数；我们可以使用此参数来获取关于失败方法的信息。

以下示例说明了如何实现一个回退处理器：

```
package com.ensode.faulttolerance;
//imports omitted
@Dependent
public class ExampleFallbackHandler
implements FallbackHandler {
private static final Logger LOGGER =
Logger.getLogger(ExampleFallbackHandler.class.getName());
@Override
public String handle(ExecutionContext ec) {
Throwable throwable = ec.getFailure();
Method buggyMethod = ec.getMethod();
Object[] parameters = ec.getParameters();
LOGGER.log(Level.SEVERE, String.format(
"%s thrown when invoking %s method with parameters: %s",
throwable.getClass().getName(), buggyMethod.getName(),
Arrays.asList(parameters)));
return "Something went wrong, check the logs\n";
}
}
```

我们的 `FallbackHandler` 实现必须是一个 CDI bean；将其转换为 CDI bean 的最简单方法是使用一个作用域注解；对于 `FallbackHandler` 实现，通常最好使用 `@Dependent` 伪作用域，这将使 `FallbackHandler` 实现与使用它的 RESTful Web 服务具有相同的作用域。

`FallbackHandler` 接口有一个泛型类型参数；其抽象的 `handle()` 方法返回我们指定的类型，在我们的示例中是 `String`；其 `handle()` 方法由 MicroProfile 运行时自动调用；它传递一个实现了 `ExecutionContext` 接口的类的实例，我们可以使用它来获取关于失败调用的信息。如示例所示，我们可以调用其 `getFailure()` 方法来获取从端点抛出的 `Throwable`（通常是 `Exception`）。

此外，我们可以调用 `ExecutionContext.getMethod()` 来获取对失败方法的引用；我们可以通过调用其 `getName()` 方法来获取方法名（`String` 类型）。

最后，我们可以通过调用 `ExecutionContext` 上的 `getParameters()` 方法来获取传递给失败端点的参数。

我们的示例将这些信息发送到 Payara Micro 日志，并向客户端返回一个 `String`。

为了使用我们的 `FallbackHandler` 实现，我们需要在实现端点的方法中将其指定为 `@Fallback` 注解的值，如下例所示：

```
package com.ensode.faulttolerance;
//imports omitted
@RequestScoped
@Path("faulttoleranceexample")
public class FaulToleranceExampleResource {
@Fallback(ExampleFallbackHandler.class)
@POST
@Produces(MediaType.TEXT_PLAIN)
@Path("fallbackhandler")
public String fallbackHandlerExample(
@QueryParam("success") boolean success) {
if (success == false) {
throw new RuntimeException(
"forcing a failure for demo purposes");
} else {
return "Call succeeded";
}
}
}
```

如示例所示，要在失败时委托给 `FallbackHandler` 实现，我们只需将 `FallbackHandler` 类作为 `@Fallback` 注解的值添加即可。

如果对端点的调用失败，我们的 `FallbackHandler` 实现将接管。

```
$ curl -XPOST http://localhost:8080/faulttolerance/webresources/faulttoleranceexample/fallbackhandler?success=false
Something went wrong, check the logs
```

如果我们检查 Payara Micro 的输出或日志文件，可以看到我们从 `ExecutionContext` 检索到的信息，这些信息可用于诊断和纠正问题。

```
[2021-10-20T14:00:27.797-0400] [] [SEVERE] [] [com.ensode.faulttolerance.ExampleFallbackHandler] [tid: _ThreadID=77 _ThreadName=http-thread-pool::http-listener(2)] [timeMillis: 1634752827797] [levelValue: 1000] java.lang.RuntimeException thrown when invoking fallbackHandlerExample method with parameters: [false]
```

默认情况下，如果端点抛出任何异常，`@Fallback` 都会接管。如果我们希望将回退功能限制在特定的异常集合上，可以通过 `@Fallback` 的 `applyOn` 属性来实现；此属性接受一个 `Exception` 子类的数组作为其值，例如：

```
@Fallback(SomeFallBackHandler.class, failOn={ReallyBadException.class, EvenWorseException.class})
```

类似地，我们可以通过 `@Fallback` 的 `skipOn` 属性指定要忽略的异常。

```
@FallBack(fallbackMethod="someMethod", skipOn={DumbException.class, IsThisEvenAnIssueException.class})
```



## 失败时重试执行

我们可以使用 `@Retry` 注解来自动重试失败的端点调用，如下例所示：

```
package com.ensode.faulttolerance;
//imports omitted
@RequestScoped
@Path("faulttoleranceexample")
public class FaulToleranceExampleResource {
private static final Logger LOGGER = Logger.getLogger
(FaulToleranceExampleResource.class.getName());
@Inject
private EndpointSuccessDeterminator endpointSuccessDeterminator;
@Retry
@GET
@Produces(MediaType.TEXT_PLAIN)
@Path("retry")
public String retryExample() {
LOGGER.log(Level.INFO, "retryExample() invoked");
boolean success;
success = endpointSuccessDeterminator.
allowEndpointToSucceed();
if (!success) {
LOGGER.log(
Level.SEVERE, "retryExample() invocation failed");
throw new RuntimeException(
"forcing a failure for demo purposes");
} else {
LOGGER.log(Level.INFO,
"retryExample() invocation succeeded");
return "Call succeeded\n";
}
}
}
```

在我们的示例中，我们使用一个应用作用域的 CDI bean 来强制端点每隔一次调用失败；该 bean 简单地返回一个 `boolean` 值，并在每次调用后翻转该 `boolean` 的值。

```
package com.ensode.faulttolerance;
import javax.enterprise.context.ApplicationScoped;
@ApplicationScoped
public class EndpointSuccessDeterminator {
private boolean successIndicator = true;
public boolean allowEndpointToSucceed() {
successIndicator = !successIndicator;
return successIndicator;
}
}
```

我们在端点中使用这个 bean 来强制失败，以便演示 `@Retry` 注解的实际效果。

如果我们向端点发送一个 HTTP GET 请求，一切看起来都很正常。

```
$ curl http://localhost:8080/faulttolerance/webresources/faulttoleranceexample/retry
Call succeeded
```

然而，如果我们查看 Payara Micro 的输出，可以看到第一次调用失败了，它被自动重试，并且第二次调用成功了。

```
[2021-10-21T14:25:22.034-0400] [] [INFO] [] [com.ensode.faulttolerance.FaulToleranceExampleResource] [tid: _ThreadID=78 _ThreadName=http-thread-pool::http-listener(2)] [timeMillis: 1634840722034] [levelValue: 800] retryExample() invoked
[2021-10-21T14:25:22.035-0400] [] [SEVERE] [] [com.ensode.faulttolerance.FaulToleranceExampleResource] [tid: _ThreadID=78 _ThreadName=http-thread-pool::http-listener(2)] [timeMillis: 1634840722035] [levelValue: 1000] retryExample() invocation failed
[2021-10-21T14:25:22.130-0400] [] [INFO] [] [com.ensode.faulttolerance.FaulToleranceExampleResource] [tid: _ThreadID=78 _ThreadName=http-thread-pool::http-listener(2)] [timeMillis: 1634840722130] [levelValue: 800] retryExample() invoked
[2021-10-21T14:25:22.131-0400] [] [INFO] [] [com.ensode.faulttolerance.FaulToleranceExampleResource] [tid: _ThreadID=78 _ThreadName=http-thread-pool::http-listener(2)] [timeMillis: 1634840722131] [levelValue: 800] retryExample() invocation succeeded
```

`@Retry` 注解提供了一些属性，我们可以用来控制重试时长、重试次数等。表 7-1 列出了 `@Retry` 的所有属性。

表 7-1

@Retry 注解属性

| 属性 | 描述 | 默认值 |
| --- | --- | --- |
| `abortOn` | 不应触发重试的 `Throwable` 类型数组 |   |
| `Delay` | 重试之间的延迟 | 0 |
| `delayUnit` | `delay` 属性的时间单位 | `ChronoUnit.MILLIS`（毫秒） |
| `durationUnit` | `maxDuration` 属性的时间单位 | `ChronoUnit.MILLIS`（毫秒） |
| `Jitter` | 用于随机变化重试延迟。实际延迟为 [delay - jitter, delay + jitter] | 200 |
| `jitterDelayUnit` | `jitter` 属性的时间单位 | `ChronoUnit.MILLIS`（毫秒） |
| `maxDuration` | 指定重试的持续时间 | 180000（与默认的毫秒时间单位结合使用时，相当于三分钟） |
| `maxRetries` | 最大重试次数，值为 -1 表示无上限（永远重试） | 3 |
| `retryOn` | 应触发重试的 `Throwable` 类型数组 | `java.lang.Exception` |

## 定义执行的最大持续时间

MicroProfile Fault Tolerance 提供了一个 `@Timeout` 注解，我们可以用它来指定 RESTful Web 服务端点允许执行的最长时间。如果方法执行时间超过指定时间，则会抛出 `TimeoutException`。

以下示例说明了如何使用 `@Timeout` 注解：

```
package com.ensode.faulttolerance;
//imports omitted
@RequestScoped
@Path("faulttoleranceexample")
public class FaulToleranceExampleResource {
private static final Logger LOGGER = Logger.getLogger(FaulToleranceExampleResource.class.getName());
@Timeout(value = 3, unit = ChronoUnit.SECONDS)
@GET
@Produces(MediaType.TEXT_PLAIN)
@Path("timeout")
public String timeoutExample(@QueryParam("delay") long delay) {
try {
TimeUnit.SECONDS.sleep(delay);
} catch (InterruptedException ex) {
LOGGER.log(Level.INFO, "sleep() interrupted");
}
return "Call returned successfully\n";
}
}
```

在我们的示例中，我们指定 RESTful Web 服务端点的执行时间不应超过三秒。如示例所示，超时等待时间通过结合 `@Timeout` 的 `value` 和 `unit` 属性来指定，前者包含一个 `long` 值，后者包含时间单位。`value` 和 `unit` 的默认值分别为 `1000L` 和 `ChronoUnit.MILLIS`（毫秒），即默认超时值为一秒。在我们的示例中，我们只是将执行暂停接收到的查询参数所指定的秒数；这样，我们可以强制端点超时，以便观察其效果。

如果我们向端点传递 `4` 作为查询参数，可以看到 `@Timeout` 注解的效果。

```
$ curl -i  http://localhost:8080/faulttolerance/webresources/faulttoleranceexample/timeout?delay=4
HTTP/1.1 500 Internal Server Error
Content-Language:
Content-Type: text/html
Connection: close
Content-Length: 1494
X-Frame-Options: SAMEORIGIN
Payara Micro #badassfish - Error report HTTP Status 500 - Internal Server Errortype Exception reportmessageInternal Server ErrordescriptionThe server encountered an internal error that prevented it from fulfilling this request.exception javax.servlet.ServletException: org.eclipse.microprofile.faulttolerance.exceptions.TimeoutExceptionroot cause org.eclipse.microprofile.faulttolerance.exceptions.TimeoutExceptionnote The full stack traces of the exception and its root causes are available in the Payara Micro #badassfish logs.Payara Micro #badassfish
```

从 `curl` 命令的输出可以看出，我们收到了一个 HTTP 500 错误；返回的响应体是由 Payara Micro 自动生成的；通过检查输出，我们可以看到抛出了一个 `TimeoutException`；这是预期的行为，因为端点执行时间超过了指定的三秒超时时间。

如果方法执行时间小于指定的超时值（本例中为三秒），则方法正常执行。

```
$ curl -i  http://localhost:8080/faulttolerance/webresources/faulttoleranceexample/timeout?delay=2
HTTP/1.1 200 OK
Server: Payara Micro #badassfish
Content-Type: text/plain
Content-Length: 27
X-Frame-Options: SAMEORIGIN
Call returned successfully
```



## 总结

本章介绍了 Payara Micro 对 MicroProfile 容错 API 的支持。

我们讨论了如何使 RESTful Web 服务异步化，这样客户端就无需阻塞等待其返回。

我们还了解了如何限制 RESTful 服务端点的并发执行次数，从而防止有缺陷或恶意的客户端使系统过载。

此外，我们介绍了如何透明地停止对持续重复失败的端点的调用。

同时，我们看到了当 RESTful Web 服务失败时，如何自动调用备用实现。

另外，我们还了解了如何自动重试失败的 RESTful 服务端点调用；最后，我们介绍了如何指定允许端点执行的最大时间，超时后即停止。

