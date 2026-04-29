# 8. 健康检查

基于云的应用通常会提供一个健康检查端点，供 Kubernetes 等编排工具使用，以验证服务是否正常运行。这些编排工具可以利用这些端点来判断服务是否需要重启或进行其他处理。

MicroProfile 提供了一个健康检查 API，我们可以用它来开发这些健康检查端点。我们可以提供报告来确定应用是否存活（live）和/或是否就绪（ready）。在这两种情况下，我们都需要实现 MicroProfile 提供的 `HealthCheck` 接口，该接口定义了一个单一的抽象方法 `call()`，该方法不接受参数并返回一个 `HealthCheckResponse` 实例；我们的 `HealthCheck` 实现需要是一个应用作用域的 CDI bean。不过，对于基本的健康检查报告，我们实际上无需做任何事；一个默认的健康检查端点会“免费”提供。

## 免费的健康检查功能

只需将任何 Web 应用部署到 Payara Micro，我们就能“免费”获得一个基本的健康检查，无需我们付出任何努力。只需向 Payara Micro 的 `/health` 上下文根发送一个 HTTP GET 请求，我们就能收到一个 JSON 格式的基本状态报告。

```
$ curl http://localhost:8080/health
{"status":"UP","checks":[]}
```

默认的健康检查响应报告应用已启动，并且不提供额外的检查。在许多情况下，默认的健康检查就足够了；如果我们需要提供额外信息，可以通过使用 MicroProfile 健康检查 API 来实现。

## 判断服务是否存活

要提供指示应用是否存活的报告，我们需要用 `@Livenesscheck` 注解我们的 HealthCheck 实现，如下例所示：

```
package com.ensode.healthcheck;
// 导入省略
@ApplicationScoped
@Liveness
public class LivenessChecker implements HealthCheck {
@Override
public HealthCheckResponse call() {
return HealthCheckResponse.builder().
name(this.getClass().getSimpleName()).up().build();
}
}
```

如示例所示，创建要返回的 `HealthCheckResponse` 实例的最简单方法是使用 `HealthCheckResponseBuilder`。`HealthCheckResponse` 类提供了一个静态的 `builder()` 方法，我们可以用它来获取 `HealthCheckResponseBuilder` 的实例。

我们需要为健康检查提供一个名称；可以通过调用 `HealthCheckResponseBuilder` 的 `name()` 方法来实现；名称可以是任意字符串；在我们的示例中，我们使用了类的名称。

我们通过调用 `HealthCheckResponseBuilder.up()` 或 `HealthCheckResponseBuilder.down()` 来指示服务是否存活；在我们的示例中，我们简单地调用了 `up()`；在实际应用中，我们会执行一些逻辑来判断服务是启动还是关闭，然后调用相应的方法。我们还可以通过调用 `HealthCheckResponseBuilder.withData()` 来提供额外的诊断数据（稍后会详细介绍）。

一旦我们构建完 `HealthCheckResponse` 对象，就调用 `HealthCheckResponseBuilder.build()` 来创建它；然后我们的 `call()` 方法就可以直接返回它。

完成存活检查的实现后，我们可以通过向应用的 `/health` 端点提交 HTTP GET 请求来查看其效果。

```
$ curl http://localhost:8080/health/
{"status":"UP","checks":[{"name":"LivenessChecker","status":"UP","data":{}}]}
```



## 判断服务是否就绪

服务可能已启动但尚未准备好投入使用；例如，其某些依赖项可能已宕机或不可用。我们可以通过开发一个带有 `@ReadinessCheck` 注解的 `HealthCheck` 实现，来实现健康检查逻辑，从而生成就绪报告。

生成就绪报告的过程与我们之前讨论存活检查时非常相似：我们实现 `HealthCheck` 接口，并从其 `call()` 方法返回一个 `HealthCheckResponse` 实例。

以下示例说明了如何在健康检查报告中提供附加数据；在此示例中，我们模拟检查应用程序所依赖资源的状态，并相应地返回“up”或“down”状态。虽然我们是在就绪检查示例中演示添加附加数据，但该方法同样适用于存活报告。

```
package com.ensode.healthcheck;
//imports omitted
@ApplicationScoped
@Readiness
public class ReadinessChecker implements HealthCheck {
HealthCheckResponseBuilder healthCheckResponseBuilder =
HealthCheckResponse.builder().
name(this.getClass().getSimpleName());
@Override
public HealthCheckResponse call() {
boolean databaseUp = isDatabaseUp();
boolean jmsQueuesUp = areJmsQueuesUp();
if (databaseUp && jmsQueuesUp) {
healthCheckResponseBuilder =
healthCheckResponseBuilder.up();
} else {
healthCheckResponseBuilder =
healthCheckResponseBuilder.down();
}
return healthCheckResponseBuilder.
withData("Database up", databaseUp).
withData("JMS queue up", jmsQueuesUp).build();
}
private boolean isDatabaseUp() {
//dummy method for illustration purposes
return new Random().nextBoolean();
}
private boolean areJmsQueuesUp() {
//dummy method for illustration purposes
return new Random().nextBoolean();
}
}
```

在我们的示例中，我们创建了一个 `HealthCheckResponseBuilder` 实例并为其命名，然后调用一些模拟检查数据库和 JMS 队列状态的方法（我们的模拟方法只是返回一个随机的 `true`/`false` 布尔值）；如果数据库和 JMS 队列都正常，我们通过调用 `HealthCheckResponseBuilder.up()` 来指示服务已就绪；否则，我们通过调用 `HealthCheckResponseBuilder.down()` 来指示服务未就绪；仅仅指示服务未就绪无法提供足够的信息；理想情况下，我们需要指明服务未就绪的原因；我们可以通过调用 `HealthCheckResponseBuilder.withData()` 来实现；该方法有三个重载版本；所有版本都将字符串作为第一个参数；第二个参数可以是另一个字符串、一个 `long` 或一个 `boolean`。第一个参数旨在为生成的数据提供简要描述；第二个值是实际要显示的值；在我们的示例中，我们使用了 `withData()` 的 `boolean` 版本，用于指示数据库和 JMS 队列是否正常。

如果我们的应用程序同时包含存活检查和就绪检查，那么向应用程序发送 HTTP GET 请求将为这两项检查生成数据。

`curl http://localhost:8080/health/` 会生成以下输出（为便于阅读已重新格式化）：

```
{
"status": "UP",
"checks": [
{
"name": "ReadinessChecker",
"status": "UP",
"data": {
"Database up": "true",
"JMS queue up": "true"
}
},
{
"name": "LivenessChecker",
"status": "UP",
"data": {}
}
]
}
```

如果我们只想查看就绪检查，可以调用 `/health/ready` 端点；例如，`curl http://localhost:8080/health/ready` 会生成以下输出：

```
{
"status": "UP",
"checks": [
{
"name": "ReadinessChecker",
"status": "UP",
"data": {
"Database up": "true",
"JMS queue up": "true"
}
}
]
}
```

类似地，如果我们只想查看存活报告，可以向 `/health/live` 发送 HTTP GET 请求，这将返回一个仅包含存活检查的 JSON 响应。

```
{
"status": "UP",
"checks": [
{
"name": "LivenessChecker",
"status": "UP",
"data": {}
}
]
}
```

## 配置 MicroProfile Health

我们可以通过作为后启动命令文件或类似方式传递的命令文件，在 Payara Micro 中使用 `set-microprofile-healthcheck-configuration` asadmin 命令来配置 MicroProfile Health。

### 禁用 MicroProfile Health

Payara Micro 默认启用 MicroProfile Health；如果我们希望禁用它，可以使用以下 asadmin 命令：

```
set-microprofile-healthcheck-configuration --enabled=false
```

### 保护 MicroProfile Health

默认情况下，`/health` 端点是不安全的，这意味着任何未经身份验证的随机用户都可以访问它。

如果我们希望保护它，可以通过发出以下 asadmin 命令来实现：

```
set-microprofile-healthcheck-configuration --securityenabled=true
```

在保护 `/health` 端点时，我们可以按如下方式指定哪些角色可以访问它：

```
set-microprofile-healthcheck-configuration --roles=role1,role2
```

`--roles` 参数的值是一个逗号分隔的列表，列出了允许访问健康端点的角色。

### 自定义 MicroProfile Health 端点

默认情况下，可以通过 `/health` 端点检索指标。如果需要，我们可以使用不同的端点。

```
set-microprofile-healthcheck-configuration --endpoint=foo
```

`--endpoint` 参数的值是我们自定义健康端点的上下文根。

## 总结

在本章中，我们介绍了 Payara Micro 对 MicroProfile Health API 的支持。我们了解了如何“免费”获得基本的健康功能；我们还介绍了如何生成自定义健康数据，以帮助判断服务是否就绪和/或存活。最后，我们介绍了如何在 Payara Micro 中配置 MicroProfile Health。

