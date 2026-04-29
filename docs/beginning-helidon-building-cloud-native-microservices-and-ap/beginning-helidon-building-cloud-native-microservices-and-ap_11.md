# 11. 任务调度

本章涵盖以下主题。

*   调度对企业级微服务至关重要，并且在 Helidon 中很容易实现

*   在代码中使用注解和通过外部配置进行调度

*   使用 Kubernetes 进行调度

在企业环境中，调度在确保各种任务和流程能够及时且高效执行方面起着关键作用。借助 Helidon，调度非常简单，这得益于其内置的调度功能实现，该实现基于开源的 [cron-utils](https://github.com/jmrozanec/cron-utils) 库。

cron-utils 是一个强大的 Java 库，允许我们定义、解析、校验和迁移 cron 表达式。它提供了一种简单直观的方式来处理 cron（常用于任务调度）。此外，cron-utils 还能生成人类可读的 cron 表达式描述，使开发者更容易理解并按需修改它们。

借助 Helidon 的调度功能，你可以利用 cron-utils 的强大能力，在企业应用中创建定时任务和流程。这使我们能够自动化各种任务并确保它们按时执行，从而提高效率和生产力。

调度也可以在 Kubernetes 环境中进行配置，因为它完整支持 CronJob。本章稍后将讨论这一点。

## Helidon 中的调度

添加清单 11-1 所示的 Maven 依赖以启用调度。

*   ① Helidon 调度支持依赖

```
io.helidon.microprofile.scheduling
helidon-microprofile-scheduling  ①

Listing 11-1
Helidon Scheduling Dependency
```

在 Helidon 中，可以使用 `@Scheduled` 注解将某个函数配置为按特定计划运行。该注解接收一个字符串值，即 *cron 表达式*。*cron* 一词源自希腊语 *chronos*，意为“时间”。cron 是类 Unix 操作系统中用于调度作业的代表性命令行工具。

在基于 Unix 的系统中，通常会在 *crontab* 等文件中编写 CronJob。这些文件中的每一行都包含一个调度定义及对应要执行的命令。前五个由空格分隔的字段即为调度定义。

![](img/600147_1_En_11_Fig1_HTML.jpg)

一个示意图：分钟、小时、月中的日、月份、周中的日等 cron 表达式字段以星号表示。命令位于末尾。

图 11-1

Cron 表达式定义

在 Helidon 中，表达式后面不需要命令；你只需在 `@Scheduled` 注解中使用调度表达式，并将其标注在应按特定计划运行的方法上。

```
@Scheduled("0 5 1 * *", concurrentExecution = false)
public void magicJob() { ... }
Listing 11-2
Scheduled Task Example
```

该方法会在“每月 1 日 05:00”执行。不允许并发执行。该字符串遵循 cron 模型来描述调度调用。

注意

请确保使用 `@Scheduled` 注解的方法位于 `ApplicationScoped` Bean 中。

Cron 表达式非常强大。仅用少量符号，你就可以描述相当复杂的调度。在 Helidon 中，你甚至可以实现更复杂的调度场景，因为 `cron-utils` 库支持它们。例如，你无需分别编写 `0 0 1 * * ? *`、`0 0 10 * * ? *`、`0 15 3 * * ? *` 和 `0 0 7 * * ? *`，可以将其合并为 `0 0|0|15|0 1|10|3|7 * * ? *`

如果你需要每次 `cron` 调用的更多元数据，可以通过将 `CronInvocation` 对象注入为方法参数来获取。

*   ① 将 `CronInvocation` 作为参数注入

*   ② 使用调用数据

```
@Scheduled("0 11 4 * *")
public void magicJob(CronInvocation inv) {                ①
String description = invocation.description();        ②
}
Listing 11-3
Scheduled Task Invocation Details Injection
```

这些数据可用于日志记录或调试。

`@Scheduled` 注解属性可以通过 `application.yaml` 属性进行覆盖。

*   ① 用于定义调度执行的完全限定类名

*   ② cron 表达式

*   ③ 并发执行（将其设置为 `false`）

```
fully.qualified.ClassName.methodName:   ①
schedule:
cron: "* * * * *"                   ②
concurrent: false                   ③
Listing 11-4
Scheduled Task Configuration
```

可用的配置属性如下。

*   **property**（描述）

*   **cron**（包含 cron 配置的字符串）

*   **concurrent**（布尔值，对应 `@Scheduled` 的 `concurrentExecution` 属性，默认 `true`）

现在你已经知道如何使用 Helidon 创建和配置*定时任务*了！

## Helidon 中的简单调度

虽然 Cron 表达式可用于处理更复杂的调度场景，但在某些情况下需要更简单的解决方案。例如，当固定频率调用间隔已足够时，使用 `@FixedRate` 注解通常是调度重复任务调用最简单的方法。只需像清单 11-5 所示那样注解一个方法。

```
@FixedRate(initialDelay = 2, value = 15, timeUnit = TimeUnit.SECONDS)
Listing 11-5
Fixed Rate Task
```

以这种方式注解的方法会每 15 秒执行一次，初始延迟为 2 秒。

注意

请确保使用 `@FixedRate` 注解的函数位于 `ApplicationScoped` Bean 中。

同样的功能也可以通过外部配置实现。与 `@Scheduled` 一样，注解中定义的所有值都可以被配置覆盖。

*   ① 用于定义调度执行的完全限定类名

*   ② 初始延迟

*   ③ 调用间隔

*   ④ 时间单位

```
fully.qualified.ClassName.methodName: ①
schedule:
initial-delay: 0                  ②
delay: 30                         ③
time-unit: MINUTES                ④
Listing 11-6
Configuration Example
```

注入的方法参数 `FixedRateInvocation` 可用于访问元数据，例如人类易读的间隔描述以及配置值。

*   ① 将 `FixedRateInvocation` 作为参数注入

*   ② 使用调用数据

```
@FixedRate(initialDelay = 5, value = 10, timeUnit = TimeUnit.MINUTES)
public void magicJob(FixedRateInvocation invocation) {      ①
String description = invocation.description();          ②
}
Listing 11-7
Invocation Details Injection
```

这些数据可用于调试或日志记录。



## Kubernetes 中的调度

如果你是在一台裸金属机器上运行单个实例的单个微服务，那么在 Helidon 中进行调度是可行的。但如今这是一种少见情况。大多数微服务都运行在云端某处的 *Kubernetes* 环境中，具有多个 *副本*，并配置了 *负载均衡器*。

每当你在代码或配置中调度一个 CronJob 时，这段被调度的代码会在应用的*所有*副本上运行。这通常不是你想要的行为。通常，你只希望由一个应用实例来执行该定时任务。

不过这可以在 Kubernetes 层面轻松解决。Kubernetes 提供了 CronJob 支持。并且和 Kubernetes 中的其他事物一样，你只需要创建一个 `yaml` 文件。

注意

这里我们假设你已经熟悉 Kubernetes 和 `kubectl` 命令行工具；否则，请参阅[Kubernetes 官方文档](https://kubernetes.io/docs/home/)。

假设你希望每天凌晨 2 点运行一次“魔法清理”任务。和 Kubernetes 中的其他事物一样，你应该创建如清单 11-8 所示的 `yaml` 规范。

*   ① 文档 `kind` 为 `CronJob`

*   ② 任务名称

*   ③ 任务执行计划：每天凌晨 2 点

*   ④ 包含 Helidon 应用的镜像

```
apiVersion: batch/v1
kind: CronJob                                  ①
metadata:
name: CleanSomeMagic                         ②
spec:
schedule: "* 10 5 * *"                       ③
jobTemplate:
spec:
template:
spec:
containers:
- name: CleanSomeMagic
image: wizardCleaningApp:latest    ④
Listing 11-8
CronJob Configuration
```

“魔法清理任务”是一个假设的 Helidon 应用，被打包为 Docker 镜像并发布到 Docker 镜像仓库中。

应用该描述文件后，你可以 *监控* 任务执行状态。

```
> kubectl get jobs --watch
NAME               COMPLETIONS   DURATION   AGE
CleanSomeMagic     0/1                      0s
CleanSomeMagic     1/1           12s        12s
Listing 11-9
Monitor the Job
```

或者，你也可以直接调用 `describe` 来查看任务的全部信息。输出中还包含运行历史。

当你不再需要某个 CronJob 时，直接删除即可。

有关如何构建 CronJob 规范的完整说明，请参阅[官方文档。​](https://kubernetes.io/docs/tasks/job/automated-tasks-with-cron-jobs/)

确实，虽然调度这个主题看起来很简单，但它对许多企业应用的成功至关重要。幸运的是，Helidon 让复杂任务管理与流程调度变得容易。

此外，如果你将 Helidon 应用部署到 Kubernetes，还可以使用 Kubernetes 内置的调度能力。Kubernetes 允许我们为应用定义和管理复杂调度，包括对 cron 表达式及其他高级调度特性的支持。

无论你使用 Helidon 内置的调度框架，还是利用 Kubernetes 的调度能力，你都可以确信应用能够轻松处理最复杂的调度场景。

## 总结

*   只需两个注解——`@Scheduled` 或 `@FixedRate`——你就可以为任务设置简单或复杂的调度计划。

*   你既可以使用注解，也可以使用配置来调度任务。

*   要在 Kubernetes 的一个 Pod 上调度任务，请使用其内置的 cron 能力。

