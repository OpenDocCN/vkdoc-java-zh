# 8. 使用容错 API 实现弹性

现代云原生应用程序通常被构建为一组相互通信的微服务，通信方式多通过网络。此类应用程序的用户期望应用程序始终可用。然而，在应用程序实例运行期间，总会有许多可能出错的情况。

网络可能不可用，数据库服务可能响应时间过长，应用程序可能一夜爆红导致并发用户激增，Web 服务可能离线，以及各种其他潜在问题。为了使应用程序能够经受住这些不可避免的故障，它需要具备容错性和弹性。

Eclipse MicroProfile Fault Tolerance 规范提供了一系列声明式 API 构造，可用于创建容错且弹性的现代云原生应用程序。本章的目标是向你展示如何使用 Eclipse MicroProfile Fault Tolerance API 为你的微服务添加重试策略、舱壁、断路器、超时和回退，将它们转变为更具弹性的云原生版本。

## 容错

Fault Tolerance 规范提供了许多拦截器绑定，可以声明式地用于实现应用程序的容错。这些拦截器绑定可以单独使用，也可以相互组合使用。由于该规范基于 Jakarta Interceptors 规范构建，因此可以直接与 CDI Bean 一起使用。因此，所有现有的 CDI 服务都可以通过 Fault Tolerance 规范转变为容错组件。

可用的容错拦截器绑定集合涵盖了创建弹性、容错应用程序的以下几个方面：

*   回退

*   超时

*   重试策略

*   断路器

*   异步

*   舱壁



### 回退机制

`@Fallback` 拦截器绑定是一个方法级别的注解，它告知运行时环境：当被注解的方法抛出异常时，应回退到指定的方法。触发回退的异常类型可以自定义。也可以设置在抛出特定异常时跳过回退，即这些异常不会触发运行时调用回退方法。清单 8-1 展示了包含两个方法（`calculateAmount` 和 `calculateAmountFallback`）的 `RateService` Bean。

```
@ApplicationScoped
public class RateService {
@Inject
@RestClient
RateResource rateResource;
@Fallback(fallbackMethod = "calculateAmountFallback")
public BigDecimal calculateAmount(TransactionRequest transactionsRequest, Wallet wallet) {
ConvertCurrencyRequest convertCurrencyRequest = new ConvertCurrencyRequest();
convertCurrencyRequest.setSourceCurrency(wallet.getCurrency());
convertCurrencyRequest.setTargetCurrency(transactionsRequest.getCurrency());
convertCurrencyRequest.setAmount(transactionsRequest.getAmount());
ConvertCurrencyResponse convertCurrencyResponse = rateResource.convertCurrency(convertCurrencyRequest);
return convertCurrencyResponse.getData().getAmount();
}
public BigDecimal calculateAmountFallback(TransactionRequest transactionsRequest, Wallet wallet) {
return transactionsRequest.getAmount();
}
}
清单 8-1
包含回退方法的 RateService Bean
```

清单 8-1 展示了 `RateService` 这个应用作用域的 Bean。它包含两个方法。第一个方法 `calculateAmount` 带有 `@Fallback` 注解。该注解的参数 `fallbackMethod` 被设置为第二个方法的名字 `calculateAmountFallback`。在运行时，默认情况下，任何属于 `java.lang.Throwable` 子类的异常被抛出时，都会导致运行时调用回退方法。回退方法必须与它所回退的方法具有相同的方法签名。

`@Fallback` 注解也可以传入一个类，而不是使用 `fallbackMethod` 参数。传入的类必须是 `org.eclipse.microprofile.faulttolerance.FallbackHandler<T>` 接口的实现。该接口的类型参数是它所回退的方法的返回类型。清单 8-2 展示了 `RateCalcFallback` 类中的 `FallbackHandler` 实现。

```
public class RateCalcFallback implements FallbackHandler {
@Override
public BigDecimal handle(final ExecutionContext context) {
final TransactionRequest transactionRequest = (TransactionRequest) context.getParameters()[0];
return transactionRequest.getAmount();
}
}
清单 8-2
展示 RateCalcFallback 处理器类
```

清单 8-2 展示了实现 `FallbackHandler` 接口的 `RateCalcFallback` 类。该类接受一个 `BigDecimal` 类型参数，这是它所回退的方法的返回类型。该接口有一个名为 `handle` 的单一方法，该方法接收一个 `org.eclipse.microprofile.faulttolerance.ExecutionContext` 类型的参数。这个对象包含了关于触发回退的失败方法的元数据。

如清单 8-2 所示，`handle` 方法的实现调用了传入的 `ExecutionContext` 对象的 `getParameters` 方法来获取方法的参数，选取第一个参数，并返回一个默认值。清单 8-3 再次展示了 `RateService` Bean，但这次使用的是 `RateCalcFallback` 处理器，而不是类自身的方法。

```
@ApplicationScoped
public class RateService {
@Inject
@RestClient
RateResource rateResource;
@Fallback(RateCalcFallback.class)
public BigDecimal calculateAmount(TransactionRequest transactionsRequest, Wallet wallet) {
ConvertCurrencyRequest convertCurrencyRequest = new ConvertCurrencyRequest();
convertCurrencyRequest.setSourceCurrency(wallet.getCurrency());
convertCurrencyRequest.setTargetCurrency(transactionsRequest.getCurrency());
convertCurrencyRequest.setAmount(transactionsRequest.getAmount());
ConvertCurrencyResponse convertCurrencyResponse = rateResource.convertCurrency(convertCurrencyRequest);
return convertCurrencyResponse.getData().getAmount();
}
}
清单 8-3
展示使用 RateCalcHandler 回退处理器的 RateCalcService
```

清单 8-3 展示了 `RateService` 的 `calculate` 方法使用 `RateCalcFallback` 类作为回退，而不是一个方法。在运行时，如果抛出异常，将自动创建一个非上下文实例的 `RateCalcFallback`。`@Fallback` 注解还有另外两个参数，即 `applyOn` 和 `skipOn`。

`applyOn` 接受一个异常数组，这些异常应触发回退机制。当为此参数指定一个值时，只有传入的异常类型被抛出时，才会导致回退方法被调用。省略此参数等同于传入 `Throwable.class`。

`skipOn` 参数也接受一个异常数组，但当这些异常被抛出时，会导致跳过回退方法。你可以使用此参数来防止当目标方法抛出业务异常（例如，自定义的验证异常）时调用回退方法。



### 超时

@Timeout 注解用于设置超时时间，超过该时间后方法执行将被视为超时或截断。微服务应用通常会相互通信，并对外部服务进行调用。不能让单个方法执行占用不合理的时间，这一点非常重要。没有时间盒约束可能会导致巨额成本。

@Timeout 注解接受两个参数：value 和 unit。value 指方法应超时的执行时长，默认值为 1000。unit 指 java.time.Duration.ChronoUnit，默认值为 ChronoUnit.MILLIS。因此，默认情况下，使用 @Timeout 注解的方法如果在执行 1000 毫秒后仍未返回，将会超时。

超时也可以与其他容错结构（如 @Fallback）一起使用。当一个方法同时被 @Timeout 和 @Fallback 注解时，如果该方法超时，将调用 fallback 方法来返回降级值。清单 8-4 展示了 RateService 方法 calculateAmount 使用了 @Timeout 注解，并保留了默认的 1000 毫秒超时设置。

```
@ApplicationScoped
public class RateService {
@Inject
@RestClient
RateResource rateResource;
@Fallback(RateCalcFallback.class)
@Timeout
public BigDecimal calculateAmount(final TransactionRequest transactionsRequest, final Wallet wallet) {
ConvertCurrencyRequest convertCurrencyRequest = new ConvertCurrencyRequest();
convertCurrencyRequest.setSourceCurrency(wallet.getCurrency());
convertCurrencyRequest.setTargetCurrency(transactionsRequest.getCurrency());
convertCurrencyRequest.setAmount(transactionsRequest.getAmount());
ConvertCurrencyResponse convertCurrencyResponse = rateResource.convertCurrency(convertCurrencyRequest);
return convertCurrencyResponse.getData().getAmount();
}
}
清单 8-4
展示了 RateService 使用 @Timeout 注解
```

清单 8-4 展示了 RateService 在 calculateAmount 方法上使用了 @Timeout 注解。同时存在 @Fallback 和 @Timeout 意味着，如果没有抛出异常，但方法在 1000 毫秒后仍未完成执行，运行时将使其超时，并调用 RateCalcFallback 处理器返回一个默认值。

@Timeout 注解可以在类级别和方法级别使用。当在类级别使用时，该类中的所有方法都将根据类级别注解的配置超时。如果一个 Bean 同时在类级别和方法级别声明了该注解，则方法级别的注解优先于类级别的注解。

### 重试

几乎所有现代应用都需要通过网络进行某种形式的调用。这种调用可能是对同一网络内的另一个服务，也可能是对应用部署网络之外的完全独立的调用。网络故障可能导致调用失败。在这种情况下，最好有一个能自动重试方法调用的机制。

@Retry 注解用于在操作异常终止时再次调用相同的操作。可以自定义触发重试的异常类型，也可以指定应跳过的异常。清单 8-5 展示了 RateService Bean 的 calculateAmount 方法带有 @Retry 注解。

```
@ApplicationScoped
public class RateService {
@Inject
@RestClient
RateResource rateResource;
@Fallback(RateCalcFallback.class)
@Timeout
@Retry
public BigDecimal calculateAmount(TransactionRequest transactionsRequest,  Wallet wallet) {
ConvertCurrencyRequest convertCurrencyRequest = new ConvertCurrencyRequest();
convertCurrencyRequest.setSourceCurrency(wallet.getCurrency());
convertCurrencyRequest.setTargetCurrency(transactionsRequest.getCurrency());
convertCurrencyRequest.setAmount(transactionsRequest.getAmount());
ConvertCurrencyResponse convertCurrencyResponse = rateResource.convertCurrency(convertCurrencyRequest);
return convertCurrencyResponse.getData().getAmount();
}
}
清单 8-5
展示了在 RateService Bean 中使用 @Retry 注解
```

清单 8-5 展示了 RateService Bean 的 calculateAmount 方法带有 @Retry 注解。照此配置，如果方法在执行过程中抛出异常，将会被重试。由于它与 @Timeout 注解一起使用，如果方法因超时异常而超时，它也会被重试。默认情况下，最大重试次数为三次。如果三次重试后该方法仍然抛出异常，则将调用 @Fallback 注解中传入的降级处理器。

@Retry 注解有许多参数，你可以根据特定应用的需求自定义其行为。这些参数如表 8-1 所示。

表 8-1

@Retry 注解的参数

| 参数 | 描述 | 默认值 |
| --- | --- | --- |
| maxRetries | 最大重试次数 | 3 |
| delay | 每次重试之间的延迟 | 0 |
| delayUnit | 延迟单位 | ChronoUnit.MILLIS |
| maxDuration | 执行重试的最大持续时间 | 180000 |
| durationUnit | maxDuration 的持续时间单位 | ChronoUnit.MILLIS |
| jitter | 重试延迟的随机变化 |   |
| jitterDelayUnit | jitter 的单位 | ChronoUnit.MILLIS |
| retryOn | 指定需要重试的失败类型 | Exception.class |
| abortOn | 指定应中止重试的失败类型 | – |

jitter 参数用于为重试延迟增加随机性。默认的 jitter 值为 200，这意味着每次重试之间的有效延迟将是 [delay - jitter, delay + jitter]。当 delay 默认值为 0 时，有效延迟最终为 -200 或 200。但是，负的有效延迟会被视为 0。因此，在没有设置 delay 值的情况下，有效延迟为 200 毫秒。

maxDuration 设置了整个重试过程的最大持续时间。设置最大持续时间至关重要，以避免陷入无限重试的情况。默认值为 180000 毫秒，这意味着整个重试持续时间将持续该时长，之后将由另一个容错注解接管。

abortOn 参数用于指定当方法抛出某些异常时，将不会重试该方法。你可以使用此参数指定不应重试的自定义业务异常，例如验证异常。例如，如果方法客户端传入了无效值，并且方法抛出了验证异常，那么不重试该方法会更高效，因为异常的原因是可预见的，并且是业务流程的正常部分。

@Retry 注解可以在类或方法上使用，如清单 8-5 所示。当在类上使用时，该类中的所有方法都将被重试。当同时在类和方法上使用时，对于该特定方法，方法上的注解优先于类级别的注解。

### 断路器

断路器充当资源方法的守卫，如果满足预设条件，它会通过快速失败来防止该方法占用计算机资源。当对某个方法的调用持续失败时，该失败的方法可能会占用资源，并且在某些情况下，将故障级联到应用的其他组件。为了防止此类情况发生，使用断路器模式来守卫方法，并在达到预设的故障阈值时阻止调用到达该方法。断路器始终处于三种状态之一：关闭、打开、半开。



#### 关闭状态

当允许调用通过并到达受保护方法时，断路器处于关闭状态。这是断路器的正常状态。它会记录每次方法调用是否成功，并累计计数至预设值。当达到预设计数，且失败方法调用的比例超过预设的故障比率（相对于预设计数）时，断路器将转换到打开状态。

#### 打开状态

在打开状态下，对受保护方法的调用将立即失败，并抛出 `org.eclipse.microprofile.faulttolerance.exceptions.CircuitBreakerOpenException` 异常。所有方法调用将持续抛出此异常，直到达到可配置的延迟时间，之后断路器将转换到半开状态。

#### 半开状态

在半开状态下，断路器允许通过可配置数量的试探性调用到达受保护方法。默认情况下，如果其中一次调用失败，断路器将回到打开状态。如果所有试探性执行都成功，断路器将转换到关闭状态。

`@CircuitBreaker` 注解可用于为某个方法创建断路器。如果该注解用于类上，则类中的所有方法都将拥有断路器。如果同时用于类及其内部的方法，则方法级别的 `@CircuitBreaker` 注解会覆盖该类级别注解对该方法的效果。清单 8-6 展示了在 `RateService` Bean 的 `convertCurrency` 方法上使用的 `@CircuitBreaker` 注解。

```
@ApplicationScoped
public class RateService {
@CircuitBreaker
@Fallback(fallbackMethod = "convertCurrencyFallback")
public ConvertCurrencyResponse convertCurrency(final ConvertCurrencyRequest convertCurrencyRequest) {
return convert(convertCurrencyRequest);
}
}
清单 8-6
展示了在 RateService Bean 中使用的 @CircuitBreaker
```

清单 8-6 展示了在 `RateService` Bean 的 `convertCurrency` 方法上使用的 `@CircuitBreaker` 方法。按此配置，断路器会将方法中抛出的所有 `java.lang.Throwable` 类型异常视为故障，如果在连续 20 次请求中发生 10 次或更多故障，断路器将转换到打开状态。随后，断路器将在打开状态延迟 5000 毫秒，然后转换到半开状态，在此期间，一次成功的调用将使其转换回关闭状态。

`@CircuitBreaker` 注解的参数如表 8-2 所示。

表 8-2
展示 @CircuitBreaker 注解参数

| 参数 | 描述 | 默认值 |
| --- | --- | --- |
| failOn | 被视为故障的异常 | Throwable.class |
| skipOn | 不应被视为故障的异常 | – |
| delay | 断路器应保持在打开状态的时长 | 5000 |
| delayUnit | 延迟时间单位 | ChronoUnit.MILLIS |
| requestVolumeThreshold | 滚动窗口内连续请求的总数 | 20 |
| failureRatio | 相对于 requestVolumeThreshold 触发断路器打开的故障比率 | 0.50 |
| successThreshold | 使断路器从半开状态转换到关闭状态所需的成功调用次数 | 1 |

`@CircuitBreaker` 注解可以与其他容错注解一起使用。如清单 8-6 所示，它与 `@Fallback` 注解结合使用。在这种情况下，当断路器打开时，失败的调用将触发后备方法（此处为 `convertCurrencyFallback`）的执行。

### 异步

`@Asynchronous` 注解用于将方法转换为异步方法，该方法在与调用线程不同的独立线程上执行。被 `@Asynchronous` 注解的方法必须返回 `java.util.concurrent.CompletionStage` 或 `java.util.concurrent.Future` 类型。为了最大化此注解与其他容错注解的配合使用，规范建议使用 `CompletionStage` 作为返回类型。

`@Asynchronous` 可以在类级别或方法级别使用。当在类级别使用时，该类中的所有方法都将异步执行。此注解不接收任何参数。如前所述，唯一的要求是方法返回 `Future<T>` 或 `CompletionStage<T>`。清单 8-7 展示了 `RateService` 中一个使用了 `@Asynchronous` 注解的异步方法 `convertCurrencyAsync`。

```
@ApplicationScoped
public class RateService {
@Asynchronous
@Retry
public CompletionStage convertCurrencyAsync(
final ConvertCurrencyRequest convertCurrencyRequest) {
return CompletableFuture.completedFuture(convert(convertCurrencyRequest));
}
}
清单 8-7
展示了使用 @Asynchronous 的 RateService 方法 convertCurrencyAsync
```

清单 8-7 展示了使用 `@Asynchronous` 和 `@Retry` 注解的 `RateService` 方法 `convertCurrencyAsync`。该方法返回一个包装了 `ConvertCurrencyResponse` 对象的 `CompletionStage`。当此方法被调用时，将生成一个新线程来执行它。由于 `@Asynchronous` 与 `@Retry` 一起使用，如果方法执行异常，重试将在生成的线程中进行。所有与 `@Asynchronous` 一起使用的其他容错拦截器也将在生成的线程中执行。

如果 `@Asynchronous` 与 `@Fallback` 一起使用，则后备方法必须具有与原始方法相同的签名。也就是说，后备方法必须像其作为后备的方法一样，返回 `Future<T>` 或 `CompletionStage<T>`。

### 舱壁

`@Bulkhead` 注解是一种容错注解，用于限制可以同时访问给定方法实例的并发请求数。默认情况下，该注解允许应用程序有十个并发请求。根据注解的使用方式，您可以选择指定有多少请求可以在队列中等待。舱壁实现有两种主要风格，即信号量和线程池隔离。

#### 信号量风格

当非 `@Asynchronous` 方法被 `@Bulkhead` 注解时，即为信号量风格的舱壁。清单 8-8 展示了被 `@Bulkhead` 注解的 `RateService` 的 `convertCurrency` 方法。

```
@ApplicationScoped
public class RateService {
@CircuitBreaker
@Fallback(fallbackMethod = "convertCurrencyFallback")
@Bulkhead
public ConvertCurrencyResponse convertCurrency(final ConvertCurrencyRequest convertCurrencyRequest) {
return convert(convertCurrencyRequest);
}
}
清单 8-8
展示了在 RateService 中使用 @Bulkhead
```

清单 8-8 展示了被 `@Bulkhead` 注解的 `RateService` 的 `convertCurrency` 方法。此舱壁使用属于信号量风格，因为被注解的方法不是异步的。按此配置，该方法一次只允许十个并发用户访问。如果并发用户数超过十个，将抛出 `org.eclipse.microprofile.faulttolerance.exceptions.BulkheadException` 异常。在这种情况下，由于该注解与 `@Fallback` 一起使用，如果抛出异常，将调用后备方法。可以通过设置注解的 `value` 参数来自定义并发用户数。



#### 线程池风格

当异步方法被 `@Bulkhead` 注解修饰时，即采用线程池风格的隔板模式。此风格还允许设置等待队列中可容纳的最大请求数。清单 8-9 展示了 RateService 类中被 `@Bulkhead` 注解修饰的 `convertCurrencyAsync` 方法。

```
@ApplicationScoped
public class RateService {
@Asynchronous
@Retry
@Bulkhead
public CompletionStage convertCurrencyAsync(
final ConvertCurrencyRequest convertCurrencyRequest) {
return CompletableFuture.completedFuture(convert(convertCurrencyRequest));
}
}
清单 8-9
展示 RateService 中的线程池隔板
```

在清单 8-9 中，`convertCurrencyAsync` 方法同时带有 `@Asynchronous` 和 `@Bulkhead` 注解。默认情况下，任何时刻该方法最多只允许十个并发请求，同时等待队列中最多可容纳十个请求。可以使用 `waitingTaskQueue` 参数来设置等待队列的大小。

如果请求无法加入队列，则会抛出 `org.eclipse.microprofile.faulttolerance.exceptions.BulkheadException`。如果在清单 8-9 所示的方法中抛出了与隔板相关的异常，由于 `@Retry` 注解的存在，该方法调用将被重试。由于该方法未声明 fallback，如果重试周期完成后该方法仍然抛出异常，则该异常将直接抛给客户端。

## 使用 Config 配置容错

Eclipse MicroProfile Fault Tolerance 规范与该项目的其他规范进行了集成。因此，前面讨论的六个容错注解可以通过使用 Config 规范进行配置。容错注解的参数可以单独或全局覆盖。

### 单独覆盖

你可以通过使用 Config API 的配置属性来覆盖单个容错注解。属性格式为 `<classname>/<methodname>/<annotation>/<parameter>`，其中 classname 指完全限定类名，methodName 指带有容错注解的方法，annotation 指你想要设置或覆盖其参数的拦截器。

例如，清单 8-9 中所示的 `@Retry` 注解可以通过配置属性进行配置，例如在 `microprofile-config.properties` 中配置为 `com.example.jwallet.rate.rate.control.RateService/convertCurrencyAsync/Retry/maxRetries=5`。这会将 RateService 中 `convertCurrencyAsync` 方法上的 `@Retry` 注解的 `maxRetries` 设置为 5。

通过 Config 机制设置的容错参数会覆盖方法中传递的相同参数。此外，由于配置源优先级设置的序数算法，你可以为不同环境中的不同配置源中的同一方法定义不同的容错参数。

### 全局参数覆盖

你也可以设置全局容错属性，这些属性将应用于给定类或整个应用程序中的所有容错拦截器。对于类级别覆盖，属性格式为 `<classname>/<annotation>/<parameter>`，与单独级别覆盖的区别在于类级别覆盖省略了方法名。例如，要将 RateService 中所有 `@Retry` 注解的 `maxRetries` 设置为 8，我们可以传递属性 `com.example.jwallet.rate.rate.control.RateService/Retry/maxRetries=8`。

容错参数也可以在应用程序级别覆盖。其属性格式为 `<annotation>/<parameter>`。仍然以 `@Retry` 为例，要将默认的应用程序级别 `maxRetries` 设置为 10，我们可以设置属性 `Retry/maxRetries=10`。这会将 `maxRetries=10` 应用于应用程序中的所有 `@Retry` 注解。

在优先级方面，最接近组件的配置优先。因此，单独的容错配置优先于类级别配置，而类级别配置又优先于应用程序级别配置。这样，你可以为应用程序的不同阶段定义复杂的容错拦截器参数。

## 总结

本章讨论了 Eclipse MicroProfile Fault Tolerance 规范。我们了解了如何使用注解，通过 fallback、timeout、retry、circuit breaker、asynchronous 和 bulkhead 模式使方法更具弹性和容错性。本章还讨论了如何使用 Eclipse MicroProfile Config API 配置容错注解。

