# 健康检查 Java API

大部分基础工作由实现 MP-HC 规范的应用程序框架完成。您的任务是，通过微服务使用 MP-HC API 定义的健康检查流程，来决定如何确定存活状态或就绪状态。

为此，您需要实现一个健康检查流程，即通过使用标记了 `Health` 注解的 Bean 来实现一个或多个 `HealthCheck` 接口的实例。

`HealthCheck` 接口在以下代码块中提供：

```
package org.eclipse.microprofile.health;

@FunctionalInterface
public interface HealthCheck {
  HealthCheckResponse call();
}
```

`Health` 注解的代码在以下代码块中提供：

```
package org.eclipse.microprofile.health;

import javax.inject.Qualifier;
import java.lang.annotation.Documented;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;

@Qualifier
@Documented
@Retention(RetentionPolicy.RUNTIME)
public @interface Health {
}
```

以下示例展示了一个假设的磁盘空间检查的 `HealthCheck` 实现。请注意，该检查将当前可用空间作为响应数据的一部分包含在内。`HealthCheckResponse` 类支持使用构建器接口来填充响应对象。

以下是一个假设的磁盘空间 `HealthCheck` 流程实现：

```
import javax.enterprise.context.ApplicationScoped;
import org.eclipse.microprofile.health.Health;
import org.eclipse.microprofile.health.HealthCheck;
import org.eclipse.microprofile.health.HealthCheckResponse;

@Health
@ApplicationScoped
public class CheckDiskspace implements HealthCheck {
  @Override
  public HealthCheckResponse call() {
      return HealthCheckResponse.named("diskspace")
              .withData("free", "780mb")
              .up()
              .build();
  }
}
```

在此示例中，我们创建了一个名为 `diskspace` 的健康响应，其状态为 `up`，并包含名为 `free`、字符串值为 `780mb` 的自定义数据。

另一个代表某个服务端点的健康检查示例如下所示。

一个假设的服务 `HealthCheck` 流程实现如下所示：

```
package io.packt.hc.rest;
//ServiceCheck example

import javax.enterprise.context.ApplicationScoped;
import org.eclipse.microprofile.health.Health;
import org.eclipse.microprofile.health.HealthCheck;
import org.eclipse.microprofile.health.HealthCheckResponse;

@Health
@ApplicationScoped
public class ServiceCheck implements HealthCheck {
 public HealthCheckResponse call() {
 return HealthCheckResponse.named("service-check")
 .withData("port", 12345)
 .withData("isSecure", true)
 .withData("hostname", "service.jboss.com")
 .up()
 .build();
 }
}
```

在此示例中，我们创建了一个名为 `service-check` 的健康响应，其状态为 `up`，并包含以下附加数据：

*   一个 `port` 项，整数值为 `12345`
*   一个 `isSecure` 项，布尔值为 `true`
*   一个 `hostname` 项，字符串值为 `service.jboss.com`

由 CDI 管理的健康检查会被应用程序运行时自动发现并注册。运行时自动暴露一个 HTTP 端点 `/health`，云平台通过该端点来探查您的应用程序以确定其状态。您可以通过构建 `Chapter04-healthcheck` 应用程序并运行它来测试这一点。您将看到以下输出：

```
Scotts-iMacPro:hc starksm$ mvn package
[INFO] Scanning for projects…
...
Resolving 144 out of 420 artifacts

[INFO] Repackaging .war: /Users/starksm/Dev/JBoss/Microprofile/PacktBook/Chapter04-metricsandhc/hc/target/health-check.war

[INFO] Repackaged .war: /Users/starksm/Dev/JBoss/Microprofile/PacktBook/Chapter04-metricsandhc/hc/target/health-check.war

[INFO] -----------------------------------------------------------------------

[INFO] BUILD SUCCESS

[INFO] -----------------------------------------------------------------------

[INFO] Total time:  7.660 s

[INFO] Finished at: 2019-04-16T21:55:14-07:00

[INFO] -----------------------------------------------------------------------

Scotts-iMacPro:hc starksm$ java -jar target/health-check-thorntail.jar

2019-04-16 21:57:03,305 INFO  [org.wildfly.swarm] (main) THORN0013: Installed fraction: MicroProfile Fault Tolerance - STABLE          io.thorntail:microprofile-fault-tolerance:2.4.0.Final

…

2019-04-16 21:57:07,449 INFO  [org.jboss.as.server] (main) WFLYSRV0010: Deployed "health-check.war" (runtime-name : "health-check.war")

2019-04-16 21:57:07,453 INFO  [org.wildfly.swarm] (main) THORN99999: Thorntail is Ready
```

服务器启动后，通过查询健康端点来测试健康检查：

```
Scotts-iMacPro:Microprofile starksm$ curl -s http://localhost:8080/health | jq
{
 "outcome": "UP",
 "checks": [
   {
     "name": "service-check",
     "state": "UP",
     "data": {
       "hostname": "service.jboss.com",
       "port": 12345,
       "isSecure": true
     }
   },
   {
     "name": "diskspace",
     "state": "UP",
     "data": {
       "free": "780mb"
     }
   }
 ]
}
```

这表明整体健康状态为 `UP`。整体状态是应用程序中所有健康检查流程的逻辑 `OR` 结果。在此例中，它是我们看到的两个健康检查流程（`diskspace` 和 `service-check`）的逻辑 `AND` 结果。

