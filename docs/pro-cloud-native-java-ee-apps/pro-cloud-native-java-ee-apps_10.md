# 10. 使用健康检查掌握应用脉搏

打包为容器镜像并部署到云端的应用程序大多由 Kubernetes 等自动化调度和部署系统管理。这些系统会自动杀死不健康的容器、生成新容器，并将请求路由到所需的容器。为了帮助实现这些功能，部署管理器需要能够探测应用程序实例以获取一些健康相关信息。

一个 kubelet（Kubernetes “代理”）会探测容器以检查它是否存活并准备好接受请求。响应将决定流量是否被路由到该已部署的容器实例，或者该实例是否被杀死并替换为一个健康的实例。因此，云原生应用程序应该能够在云环境中回答三个简单的问题：

*   你启动了吗？

*   你存活吗？

*   你准备好接受请求了吗？

Eclipse MicroProfile 健康规范提供了用于向应用程序添加机器对机器健康检查的 API 构造，以便它们能够回答这些问题。该规范与现有的类似规范（如 Kubernetes^(¹¹⁴) 存活、就绪和启动探针）相似且兼容。本章讨论健康 API 以及如何将其添加到你的应用程序中。

## 健康检查与指标

健康 API 和指标 API 都是生成应用程序性能信息的 API。然而，两者的相似之处仅此而已。健康 API 主要设计用于生成机器对机器级别的信息。在典型的云集群中，应用程序部署的一切都是自动化的，健康 API 提供了构造，使应用程序能够响应关于其状态和是否准备好接受请求的简单机器查询。

另一方面，指标 API（在第 9 章中讨论）主要设计用于生成详细的应用程序性能数据。例如，指标 API 可用于查询给定方法执行需要多长时间、该方法的最大并发连接数是多少、平均响应时间是多少，以及其他此类详细的细粒度信息。在现代云原生应用程序的设计中，这两个 API 相辅相成。

## 健康 API 结构

健康 API 围绕两种类型展开：`HealthCheck` 接口和 `HealthCheckResponse` 类。所有健康检查过程都可以从 `/health` 上下文路径查询。对该路径的 HTTP GET 请求会以 JSON 格式返回该特定应用程序中的所有健康检查。成功的健康检查探测将返回 HTTP 200 状态码。失败的健康检查将返回 HTTP 503 状态码。在这种情况下，`HealthCheck` 的应用程序实现会返回一个 `DOWN` 状态。

### HealthCheck 接口

`HealthCheck` 接口是一个函数式接口，具有返回 `HealthCheckResponse` 的单一方法 `call()`。该接口是健康 API 的入口点。想要响应健康检查探测的类将扩展 `HealthCheck` 接口，以特定于应用程序的方式实现 `call()` 方法，最终返回一个 `HealthCheckResponse`。



### HealthCheckResponse

HealthCheckResponse 是一个封装健康检查探针返回响应的类。它包含三个简单的字段：name、status 和 data。前两个字段是 String 类型，最后一个字段是 `Optional<Map<String, Object>>` 类型。data 字段可用于返回健康检查实现希望返回给调用客户端的任意附加数据。该类提供了一个便捷的构建器方法用于构建响应。name 字段是必填项，每个 HealthCheckResponse 实例都必须提供。

当健康检查组件执行了某种特定于应用的检查后，它可以构建 HealthCheckResponse 并返回一个实例，告知调用者该特定健康检查的状态是正常（up）还是异常（down），同时也可以传递一些可选数据。

## 健康检查与 CDI

健康检查 API 基于 Jakarta 上下文和依赖注入规范（第 4 章）构建。这意味着所有健康检查组件都是 CDI bean。由于健康检查组件需要实现 HealthCheck 接口，因此所有健康检查组件至少是 HealthCheck 类型的 bean。

然而，正如开篇所述，健康检查 API 允许应用程序回答两个简单的问题。由于可能存在多个 HealthCheck 类型的 bean，该 API 提供了三个 CDI 限定符来区分不同的 HealthCheck bean。

### 健康检查限定符

用于标识哪些 bean 响应哪个健康探针的三个健康检查限定符是：

*   @Readiness

*   @Liveness

*   @Startup

#### @Readiness

在 HealthCheck bean 上使用 @Readiness 限定符，会创建一个就绪健康检查流程。此健康检查允许调用者了解应用程序是否已准备好接受请求。就绪健康检查可以通过资源路径 /health/ready 进行查询。要创建就绪健康检查，请使用 @Readiness 注解一个 HealthCheck 类型的 bean，如清单 10-1 所示。

```
@ApplicationScoped
@Readiness
public class HelloReadiness implements HealthCheck {
CountDownLatch countDownLatch = new CountDownLatch(2);
@Override
public HealthCheckResponse call() {
countDownLatch.countDown();
return countDownLatch.getCount() == 0
? HealthCheckResponse.up("rate-readiness")
: HealthCheckResponse.down("rate-readiness");
}
}
清单 10-1
展示了一个 @Readiness 健康检查
```

清单 10-1 展示了实现 HealthCheck 接口的 HelloReadiness bean。这个 bean 是一个带有 @Readiness 限定符的 @ApplicationScoped bean。由于健康检查探针覆盖整个应用程序，因此 HealthCheck 实现的一个应用程序作用域实例就足够了。我们不需要为每个探针都创建一个新的上下文实例。@Readiness 注解将此 bean 标记为就绪健康检查，如果查询了 /health 根健康资源或特定的 /health/ready 端点，健康检查运行时将调用它。

在此示例中，call 方法的实现使用了一个简单的 CountDownLatch 来模拟某种倒计时。由于该 bean 是一个 CDI bean，你可以注入任何依赖项，并执行任何自定义的、特定于应用程序的任务来确定应用程序的就绪状态。唯一的要求是返回 HealthCheckResponse 对象。

一个针对路径 /health/ready 的示例就绪探针返回的健康数据如清单 10-2 所示。

```
{
"checks": [
{
"data": {},
"name": "rate-readiness",
"status": "UP"
}
],
"status": "UP"
}
清单 10-2
展示了对 /health/ready 调用的响应
```

清单 10-2 展示了 HealthCheckResponse 对象的 JSON 表示形式。对于 @Readiness 探针，健康检查流程报告其状态为 UP 且已就绪。这意味着 jwallet 的 rate 模块已启动并准备好接受请求。

#### @Liveness

在 HealthCheck bean 上使用 @Liveness 限定符，会创建一个健康检查来确定应用程序是否正在运行。在云环境中，如果此调用失败并返回 DOWN 状态，例如，kubelet 可以使用此信息丢弃该容器实例并用更健康的实例替换它。要创建 @Liveness 健康检查，请使用 @Liveness 注解一个 HealthCheck bean，如清单 10-3 所示。

```
@ApplicationScoped
@Liveness
public class HelloLiveness implements HealthCheck {
LocalDateTime init = LocalDateTime.now(ZoneOffset.UTC);
@Override
public HealthCheckResponse call() {
LocalDateTime now = LocalDateTime.now(ZoneOffset.UTC);
Duration upDuration = Duration.between(init, now);
return HealthCheckResponse.builder()
.up()
.name("rate_service_live")
.withData("up_since", upDuration.toMinutes())
.build();
}
}
清单 10-3
展示了一个 @Liveness 健康检查实现
```

清单 10-3 展示了 HelloLiveness bean 中的 @Liveness 健康检查实现。此实现简单地返回 UP 状态以及一个自由格式的数据，显示服务已运行了多少分钟。这个自由格式的数据可以是健康检查实现希望返回给客户端的任何内容。对此 @Liveness 实现的一个示例调用返回的数据如清单 10-4 所示。

```
{
"checks": [
{
"data": {
"up_since": 2
},
"name": "rate_service_live",
"status": "UP"
}
],
"status": "UP"
}
清单 10-4
展示了一个存活健康检查响应
```

清单 10-4 展示了来自 @Liveness 检查的 HealthCheckResponse JSON 表示形式。有了这些信息和 HTTP 状态码 200，kubelet 将知道该应用程序实例已启动并正在运行。

#### @Startup

@Startup 是一种健康检查，允许应用程序创建自定义的启动健康检查，这些检查可用于在存活探针接管之前进行某种形式的特定于应用程序的验证。在云环境中，此健康检查流程将映射到 Kubernetes 启动探针。@Startup 适用于你的应用程序依赖外部服务的情况。在这种情况下，你可能希望在应用程序投入使用之前确保外部服务已启动并正在运行。清单 10-5 展示了 HelloStartup bean 中的启动健康检查实现。

```
@ApplicationScoped
@Startup
public class HelloStartup implements HealthCheck {
CountDownLatch countDownLatch = new CountDownLatch(5);
LocalDateTime upSince = LocalDateTime.now(ZoneOffset.UTC);
@Override
public HealthCheckResponse call() {
countDownLatch.countDown();
return countDownLatch.getCount() == 0
? HealthCheckResponse.named("rate-startup")
.withData("up_since", upSince.toString())
.up()
.build()
: HealthCheckResponse.down("rate-startup");
}
}
清单 10-5
展示了一个 @Startup 健康检查实现
```

清单 10-5 展示了一个 @Startup 健康检查实现，它简单地倒计时到五，然后返回 UP。对 /health/started 端点的一个示例调用返回的数据如清单 10-6 所示。

```
{
"checks": [
{
"data": {
"up_since": "2022-08-14T17:26:33.363745328"
},
"name": "rate-startup",
"status": "UP"
}
],
"status": "UP"
}
清单 10-6
展示了启动健康探针返回的数据
```



## 组合健康检查

到目前为止，我们看到的健康检查都是由独立的 Bean 实现的。然而，它们也可以组合起来，由单个 Bean 实现。在这种情况下，单个 HealthCheck Bean 将使用所有必需的健康检查限定符进行注解。这样的 HealthCheck 实现将用于解析其被注解的所有健康检查。清单 10-7 展示了一个针对 @Liveness、@Readiness 和 @Startup 的单一健康检查实现。

```
@ApplicationScoped
@Liveness
@Readiness
@Startup
public class HelloHealth implements HealthCheck {
LocalDateTime upSince = LocalDateTime.now(ZoneOffset.UTC);
@Inject
ApiLayer apiLayer;
@Override
public HealthCheckResponse call() {
JsonObject currencySymbols = apiLayer.getCurrencySymbols();
boolean success = currencySymbols.getBoolean("success", false);
return success
? HealthCheckResponse.named("rate-service")
.withData("up_since", upSince.toString())
.withData("rate_api_provider", "up")
.up()
.build()
: HealthCheckResponse.down("rate-service");
}
}
清单 10-7
展示了一个单一的健康检查实现
```

清单 10-7 展示了一个针对存活、就绪和启动的单一健康检查实现。对于此实现，会向应用程序的汇率服务提供商发起一个外部调用。我们唯一想要返回 UP 健康检查响应的情况是当外部服务提供商可用时。由于我们将汇率转换委托给了该服务，当我们依赖的外部提供商宕机时，将我们的应用程序投入服务意义不大。对 /health、/health/live、/health/ready 和 /health/started 的调用都将路由到这个单一实现。对 /health 的示例调用返回的数据如清单 10-8 所示。

```
{
"checks": [
{
"data": {
"up_since": "2022-08-14T17:59:26.592292658",
"rate_api_provider": "up"
},
"name": "rate-service",
"status": "UP"
}
],
"status": "UP"
}
清单 10-8
展示了单一健康检查实现的数据
```

## 生成健康检查

正如在 Jakarta 上下文和依赖注入章节（第 4 章）中所讨论的，生产者是一种用于创建 CDI Bean 的构造。由于 HealthCheck 是 CDI Bean，它们可以使用 CDI 生产者方法构造来生成。清单 10-9 展示了 WalletHealthCheckProducer Bean，它包含三个生产者方法，每个方法都带有一个针对存活、就绪和启动这三种健康检查的限定符。

```
@ApplicationScoped
public class WalletHealthCheckProducer {
CountDownLatch readinessCountdown = new CountDownLatch(2);
CountDownLatch startupCountdown = new CountDownLatch(5);
@Inject
@ConfigProperty(name = "free.memory.limit")
Integer freeMemoryLimit;
@Produces
@Liveness
HealthCheck livenessCheck() {
String healthCheckName = "wallet-liveness-health-check";
return () -> {
long totalMemory = Runtime.getRuntime().totalMemory();
long freeMemory = Runtime.getRuntime().freeMemory();
boolean adequateMemory = (((float) freeMemory / totalMemory) * 100) > freeMemoryLimit;
return adequateMemory ?
HealthCheckResponse
.builder()
.name(healthCheckName)
.up()
.build() :
HealthCheckResponse
.named(healthCheckName)
.down()
.build();
};
}
@Produces
@Readiness
HealthCheck readinessCheck() {
return () -> {
readinessCountdown.countDown();
return readinessCountdown.getCount() == 0 ?
HealthCheckResponse
.up("wallet-liveness") :
HealthCheckResponse
.down("wallet-liveness");
};
}
@Produces
@Startup
HealthCheck startupCheck() {
return () -> {
startupCountdown.countDown();
return startupCountdown.getCount() == 0 ?
HealthCheckResponse
.up("wallet-started") :
HealthCheckResponse
.down("wallet-startup-failed");
};
}
}
清单 10-9
展示了使用生产者方法创建 HealthCheck
```

清单 10-9 展示了带有三个方法的 @ApplicationScoped WalletHealthCheckProducer。每个方法都使用 CDI @Produces 注解和一个健康检查限定符进行注解。第一个方法是 @Liveness 检查，如果 JVM 中相对于总可用内存的空闲内存超过传入的配置值，则返回一个状态。在示例代码中，在 microprofile-config.properties 中传入的 MicroProfile 配置值是 40。因此，如果 JVM 中的空闲内存超过 40%，则存活检查返回一个状态为 UP 的 HealthCheckResponse 对象。否则，返回 DOWN 状态。

第二个方法是 @Readiness 检查，它简单地倒数到 2 并返回 UP 状态。第三个方法是 @Startup 检查，它也倒数到 5，然后返回 UP 状态。清单 10-10 展示了对此模块的 /health 端点进行示例调用时返回的数据。

```
{
"checks": [
{
"data": {},
"name": "wallet-readiness",
"status": "UP"
},
{
"data": {},
"name": "wallet-liveness-health-check",
"status": "DOWN"
},
{
"data": {},
"name": "wallet-started",
"status": "UP"
}
],
"status": "DOWN"
}
清单 10-10
展示了为生成的健康检查返回的健康检查数据
```

清单 10-10 展示了 jwallet 的 wallet 模块的 /health 探测的响应。在示例调用中，JVM 报告可用空闲内存小于总内存的 40%，导致 @Liveness 检查返回 DOWN 状态。需要注意的是，响应不一定按任何特定顺序返回。即使在代码中存活检查是在就绪检查之前声明的，对基础 /health 端点的调用也会先返回就绪检查的结果，然后才是存活检查的结果。

## 总结

本章讨论了 Eclipse MicroProfile Health 规范。我们探讨了该规范与 Metrics 规范之间的区别。然后讨论了 Health API 的结构。接着，我们研究了如何在 CDI API 的上下文中使用它。您应该能够使用 Health API 为您的云原生应用程序创建自定义的健康检查流程。

脚注 1

