# 6. 指标

MicroProfile Metrics API 允许我们轻松生成监控数据（例如 CPU 负载、磁盘空间使用量、HTTP 请求数量等）。Payara Micro 提供了一个指标端点，无需我们额外操作即可提供这些数据。

MicroProfile 有四个指标端点：

*   */metrics/base* 由 MicroProfile 标准定义，意味着所有 MicroProfile 实现（如 Payara Micro）都必须实现它。它提供有关运行 Payara Micro 实例的 JVM 的信息，例如加载到内存中的 Java 类数量、JVM 运行时间、JVM 内存信息等。

*   */metrics/vendor* 允许我们检索供应商特定的指标信息。

*   */metrics/application* 允许我们检索应用程序特定的指标，这些指标可以通过注解生成。

*   */metrics* 显示所有指标信息，包括 */metrics/base*、*/metrics/vendor* 和 *metrics/application* 中的数据。

MicroProfile Metrics API 生成的数据旨在供监控工具读取，这些工具可以在应用程序使用过多 CPU、内存、磁盘空间或出现其他警告时自动提醒我们。默认情况下，MicroProfile Config 端点的输出采用 *Prometheus* 格式。

Prometheus 是一个非常流行的开源监控工具。由于 Prometheus 非常流行，其他几种监控工具也采用了它读取指标数据的格式。通过以 Prometheus 格式生成数据，MicroProfile Config 自动与多种监控工具兼容。如果我们希望以 JSON 格式接收指标数据，只需在请求中将 *Accept* HTTP 头设置为 *application/json* 即可。

## 基础指标

我们可以通过向 Payara Micro 实例的 */metrics/base* 端点发送 GET 请求来访问基础指标。

例如，使用 curl 向本地工作站上运行的 Payara Micro 实例发送请求

```
curl http://localhost:8080/metrics/base
```

将产生类似以下的输出：



```
# TYPE base_classloader_loadedClasses_count gauge
# HELP base_classloader_loadedClasses_count 显示当前 JVM 中已加载的类数量。
base_classloader_loadedClasses_count 16396
# TYPE base_classloader_loadedClasses_total_total counter
# HELP base_classloader_loadedClasses_total_total 显示自 JVM 启动以来已加载的类总数。
base_classloader_loadedClasses_total_total 16396
# TYPE base_classloader_unloadedClasses_total_total counter
# HELP base_classloader_unloadedClasses_total_total 显示自 JVM 启动以来已卸载的类总数。
base_classloader_unloadedClasses_total_total 0
# TYPE base_cpu_availableProcessors gauge
# HELP base_cpu_availableProcessors 显示 JVM 可用的处理器数量。该值在虚拟机的特定调用期间可能会发生变化。
base_cpu_availableProcessors 8
# TYPE base_cpu_systemLoadAverage gauge
# HELP base_cpu_systemLoadAverage 显示最后一分钟的系统平均负载。系统平均负载是排队到可用处理器的可运行实体数量与在可用处理器上运行的可运行实体数量在一段时间内的平均值。计算平均负载的方式取决于操作系统，但通常是经过阻尼的、与时间相关的平均值。如果平均负载不可用，则显示负值。此属性旨在提供系统负载的提示，并且可能会被频繁查询。在某些实现此方法成本较高的平台上，平均负载可能不可用。
base_cpu_systemLoadAverage 0.32
# TYPE base_gc_time_total counter
# HELP base_gc_time_total 显示近似累积的收集耗时（以毫秒为单位）。如果此收集器的收集耗时未定义，则此属性显示 -1。JVM 实现可能使用高分辨率计时器来测量耗时。如果收集耗时非常短，即使收集计数已增加，此属性也可能显示相同的值。
base_gc_time_total{name="G1 Young Generation"} 226
base_gc_time_total{name="G1 Old Generation"} 0
# TYPE base_gc_total_total counter
# HELP base_gc_total_total 显示已发生的收集总数。如果此收集器的收集计数未定义，则此属性列出 -1。
base_gc_total_total{name="G1 Young Generation"} 15
base_gc_total_total{name="G1 Old Generation"} 0
# TYPE base_jvm_uptime_seconds gauge
# HELP base_jvm_uptime_seconds 显示 JVM 的运行时间（以毫秒为单位）。
base_jvm_uptime_seconds 4745.731
# TYPE base_memory_committedHeap_bytes gauge
# HELP base_memory_committedHeap_bytes 显示已提交供 JVM 使用的内存量（以字节为单位）。
base_memory_committedHeap_bytes 8.09500672E8
# TYPE base_memory_committedNonHeap_bytes gauge
# HELP base_memory_committedNonHeap_bytes 显示已提交供 JVM 使用的内存量（以字节为单位）。
base_memory_committedNonHeap_bytes 1.3787136E8
# TYPE base_memory_maxHeap_bytes gauge
# HELP base_memory_maxHeap_bytes 显示可用于堆内存的最大内存量（以字节为单位）。
base_memory_maxHeap_bytes 1.0479468544E10
# TYPE base_memory_maxNonHeap_bytes gauge
# HELP base_memory_maxNonHeap_bytes 显示可用于非堆内存的最大内存量（以字节为单位）。
base_memory_maxNonHeap_bytes -1
# TYPE base_memory_usedHeap_bytes gauge
# HELP base_memory_usedHeap_bytes 显示已使用的内存量（以字节为单位）。
base_memory_usedHeap_bytes 3.10751824E8
# TYPE base_memory_usedNonHeap_bytes gauge
# HELP base_memory_usedNonHeap_bytes 显示已使用的内存量（以字节为单位）。
base_memory_usedNonHeap_bytes 1.31463504E8
# TYPE base_thread_count gauge
# HELP base_thread_count 显示当前活动线程数，包括守护线程和非守护线程。
base_thread_count 75
# TYPE base_thread_daemon_count gauge
# HELP base_thread_daemon_count 显示当前活动守护线程数。
base_thread_daemon_count 62
# TYPE base_thread_max_count gauge
# HELP base_thread_max_count 显示自 Java 虚拟机启动或峰值重置以来的峰值活动线程数。这包括守护线程和非守护线程。
base_thread_max_count 100
```

上述输出是默认的 Prometheus 格式；如果我们希望以 JSON 格式接收数据，则需要将 *Accept* 请求头设置为 *application/json*；例如，使用 curl

```
curl -H "Accept: application/json"  http://localhost:8080/metrics/base
```

将返回 JSON 格式的输出。

```
{
"classloader.loadedClasses.count": 16398.0,
"classloader.loadedClasses.total": 16398,
"classloader.unloadedClasses.total": 0,
"cpu.availableProcessors": 8.0,
"cpu.systemLoadAverage": 0.29,
"gc.time;name=G1 Young Generation": 226,
"gc.time;name=G1 Old Generation": 0,
"gc.total;name=G1 Young Generation": 15,
"gc.total;name=G1 Old Generation": 0,
"jvm.uptime": 5249144.0,
"memory.committedHeap": 809500672,
"memory.committedNonHeap": 138002432,
"memory.maxHeap": 10479468544,
"memory.maxNonHeap": -1.0,
"memory.usedHeap": 3.904436E+8,
"memory.usedNonHeap": 131634184,
"thread.count": 75.0,
"thread.daemon.count": 62.0,
"thread.max.count": 100.0
}
```

## 供应商指标

在撰写本文时，Payara Micro 提供了一个单一的供应商指标；我们可以通过向 /metrics/vendor 发送 HTTP 请求来访问它：

```
curl http://localhost:8080/metrics/vendor
```

这将产生类似于以下的输出：

```
# TYPE vendor_system_cpu_load gauge
# HELP vendor_system_cpu_load 显示整个系统的“近期 CPU 使用率”。该值是 [0.0,1.0] 区间内的双精度浮点数。值为 0.0 表示在所观察的近期时间段内所有 CPU 都处于空闲状态，而值为 1.0 表示在所观察的近期时间段内所有 CPU 都 100% 活跃运行。根据系统中正在进行的活动，0.0 到 1.0 之间的所有值都是可能的。如果系统的近期 CPU 使用率不可用，该方法将返回一个负值。
vendor_system_cpu_load 0.021788129226145758
```

或者，如果我们希望以 JSON 格式接收 Payara Micro 的供应商特定指标数据，我们照常设置 *Accept* HTTP 头：

```
curl -H "Accept: application/json"  http://localhost:8080/metrics/vendor
```

毫不意外，这将导致以 JSON 格式获取 Payara Micro 的供应商特定指标数据。

```
{
"system.cpu.load": 0.015595463137996219
}
```

Payara Server 允许我们将 JMX Bean 暴露为自定义供应商指标；在撰写本文时，此功能在 Payara Micro 中不可用。

## 应用指标

MicroProfile 指标提供了许多注解，我们可以用它们来注解我们的方法，这可以让我们以极少的努力提供应用指标。

大多数 MicroProfile 指标注解都有一组共同的属性：

*   `absolute`：一个布尔值，指示指标的名称应该是绝对的，还是应该附加到类的完全限定名之后。

*   `description`：我们指标的描述。

*   `displayName`：指标的人类可读显示名称。

*   `name`：指标的名称，如果未指定，则默认为我们正在注解的方法名称。

*   `tags`：用于区分具有相同名称的指标。

*   `unit`：指标的测量单位。



### @Counted

`@Counted` 注解用于指示方法被调用的次数；每次调用该方法时，计数器值会自动增加。

```
@GET
@Counted
@Path("counted")
@Produces(MediaType.TEXT_PLAIN)
public String countedExample() {
return "Counter was just increased\n";
}
```

多次调用我们的端点后，可以通过向 Payara Micro 的 `/metrics/application` 端点发送 GET 请求来查看输出：

```
# TYPE application_com_ensode_applicationmetrics_MetricsDemo_countedExample_total counter
application_com_ensode_applicationmetrics_MetricsDemo_countedExample_total{_app="application-metrics-1.0-snapshot"} 3
```

或者以 JSON 格式输出：

```
{
"com.ensode.applicationmetrics.MetricsDemo.countedExample;_app=application-metrics-1.0-snapshot": 3
}
```

### @Gauge

此注解只能应用于返回数值对象（`Long`、`Short`、`Integer`、`Float`、`Double`）或其对应原始类型的方法；它会将返回值作为指标暴露出来。@GET

```
@Gauge(unit = "some unit")
@Path("gauge")
@Produces(MediaType.TEXT_PLAIN)
public int gaugeExample() {
gaugeVal += 2;
return gaugeVal;
}
```

`@Gauge` 的 `unit` 属性是必需的；我们可以为其分配任意字符串值；例如，如果返回的是组织中的员工数量，单位可以是 `"employees"`；如果返回的是工厂生产的零件数量，单位可以是 `"widgets"`；以此类推。

`@Gauge` 在 Prometheus 格式下的输出如下所示：

```
# TYPE application_com_ensode_applicationmetrics_MetricsDemo_gaugeExample_some_unit gauge
application_com_ensode_applicationmetrics_MetricsDemo_gaugeExample_some_unit{_app="application-metrics-1.0-snapshot"} 8
```

或者以 JSON 格式输出：

```
"com.ensode.applicationmetrics.MetricsDemo.gaugeExample;_app=application-metrics-1.0-snapshot": 10
{
"com.ensode.applicationmetrics.MetricsDemo.gaugeExample;_app=application-metrics-1.0-snapshot": 8
}
```

### @ConcurrentGauge

此注解显示在任意特定时间点对某个方法（通常是 REST 端点）的并发调用次数。

```
@GET
@ConcurrentGauge
@Path("concurrentGauge")
@Produces(MediaType.TEXT_PLAIN)
public void concurrentGaugeExample() throws InterruptedException {
TimeUnit.SECONDS.sleep(10);
}
```

在我们的示例中，我们只是让线程休眠十秒钟；这允许我们对端点进行几次调用，然后调用指标端点，以便获取相关的示例数据。

`@ConcurrentGauge` 的输出如下所示：

```
# TYPE application_com_ensode_applicationmetrics_MetricsDemo_concurrentGaugeExample_current gauge
application_com_ensode_applicationmetrics_MetricsDemo_concurrentGaugeExample_current{_app="application-metrics-1.0-snapshot"} 3
# TYPE application_com_ensode_applicationmetrics_MetricsDemo_concurrentGaugeExample_min gauge
application_com_ensode_applicationmetrics_MetricsDemo_concurrentGaugeExample_min{_app="application-metrics-1.0-snapshot"} 0
# TYPE application_com_ensode_applicationmetrics_MetricsDemo_concurrentGaugeExample_max gauge
application_com_ensode_applicationmetrics_MetricsDemo_concurrentGaugeExample_max{_app="application-metrics-1.0-snapshot"} 3
```

`@ConcurrentGauge` 不仅返回方法的并发调用次数，还返回前一分钟内该方法的最小和最大并发调用次数。

`@ConcurrentGauge` 的 JSON 格式输出如下所示：

```
{
"com.ensode.applicationmetrics.MetricsDemo.concurrentGaugeExample": {
"current;_app=application-metrics-1.0-snapshot": 3,
"min;_app=application-metrics-1.0-snapshot": 0,
"max;_app=application-metrics-1.0-snapshot": 3
}
}
```

### @Metered

此注解生成方法被调用的次数、应用生命周期内的每秒调用速率，以及最近一分钟、最近五分钟和最近十五分钟的每秒调用速率。

```
@GET
@Metered
@Path("metered")
@Produces(MediaType.TEXT_PLAIN)
public String meteredExample() {
return "Metered method invoked\n";
}
```

`@Metered` 的输出如下所示：

```
application_com_ensode_applicationmetrics_MetricsDemo_meteredExample_total{_app="application-metrics-1.0-snapshot"} 5
18k
# TYPE application_com_ensode_applicationmetrics_MetricsDemo_meteredExample_rate_per_second gauge
application_com_ensode_applicationmetrics_MetricsDemo_meteredExample_rate_per_second{_app="application-metrics-1.0-snapshot"} 0.007269820214059275
# TYPE application_com_ensode_applicationmetrics_MetricsDemo_meteredExample_one_min_rate_per_second gauge
application_com_ensode_applicationmetrics_MetricsDemo_meteredExample_one_min_rate_per_second{_app="application-metrics-1.0-snapshot"} 0.01806938732711853
# TYPE application_com_ensode_applicationmetrics_MetricsDemo_meteredExample_five_min_rate_per_second gauge
application_com_ensode_applicationmetrics_MetricsDemo_meteredExample_five_min_rate_per_second{_app="application-metrics-1.0-snapshot"} 0.012251339396869875
# TYPE application_com_ensode_applicationmetrics_MetricsDemo_meteredExample_fifteen_min_rate_per_second gauge
application_com_ensode_applicationmetrics_MetricsDemo_meteredExample_fifteen_min_rate_per_second{_app="application-metrics-1.0-snapshot"} 0.005013244558508323
```

等效的 JSON 格式：

```
{
"com.ensode.applicationmetrics.MetricsDemo.meteredExample": {
"count;_app=application-metrics-1.0-snapshot": 5,
"meanRate;_app=application-metrics-1.0-snapshot": 0.0060812930956421875,
"oneMinRate;_app=application-metrics-1.0-snapshot": 0.0019044994125862731,
"fiveMinRate;_app=application-metrics-1.0-snapshot": 0.007811798894517148,
"fifteenMinRate;_app=application-metrics-1.0-snapshot": 0.004314939579277629
}
}
```



### @Timed

此注解用于生成关于方法调用完成时间的统计数据（如平均值、标准差等）。

```
@GET
@Timed
@Path("timed")
@Produces(MediaType.TEXT_PLAIN)
public void timedExample() throws InterruptedException {
TimeUnit.SECONDS.sleep(2);
}
```

`@Timed` 的输出如下所示：

```
# TYPE application_com_ensode_applicationmetrics_MetricsDemo_timedExample_rate_per_second gauge
application_com_ensode_applicationmetrics_MetricsDemo_timedExample_rate_per_second{_app="application-metrics-1.0-snapshot"} 0.1465568677874658
# TYPE application_com_ensode_applicationmetrics_MetricsDemo_timedExample_one_min_rate_per_second gauge
application_com_ensode_applicationmetrics_MetricsDemo_timedExample_one_min_rate_per_second{_app="application-metrics-1.0-snapshot"} 0.2
# TYPE application_com_ensode_applicationmetrics_MetricsDemo_timedExample_five_min_rate_per_second gauge
application_com_ensode_applicationmetrics_MetricsDemo_timedExample_five_min_rate_per_second{_app="application-metrics-1.0-snapshot"} 0.2
# TYPE application_com_ensode_applicationmetrics_MetricsDemo_timedExample_fifteen_min_rate_per_second gauge
application_com_ensode_applicationmetrics_MetricsDemo_timedExample_fifteen_min_rate_per_second{_app="application-metrics-1.0-snapshot"} 0.2
# TYPE application_com_ensode_applicationmetrics_MetricsDemo_timedExample_mean_seconds gauge
application_com_ensode_applicationmetrics_MetricsDemo_timedExample_mean_seconds{_app="application-metrics-1.0-snapshot"} 2.000146096
# TYPE application_com_ensode_applicationmetrics_MetricsDemo_timedExample_max_seconds gauge
application_com_ensode_applicationmetrics_MetricsDemo_timedExample_max_seconds{_app="application-metrics-1.0-snapshot"} 2.000146096
# TYPE application_com_ensode_applicationmetrics_MetricsDemo_timedExample_min_seconds gauge
application_com_ensode_applicationmetrics_MetricsDemo_timedExample_min_seconds{_app="application-metrics-1.0-snapshot"} 2.000146096
# TYPE application_com_ensode_applicationmetrics_MetricsDemo_timedExample_stddev_seconds gauge
application_com_ensode_applicationmetrics_MetricsDemo_timedExample_stddev_seconds{_app="application-metrics-1.0-snapshot"} 0
# TYPE application_com_ensode_applicationmetrics_MetricsDemo_timedExample_seconds summary
application_com_ensode_applicationmetrics_MetricsDemo_timedExample_seconds_count{_app="application-metrics-1.0-snapshot"} 1
application_com_ensode_applicationmetrics_MetricsDemo_timedExample_seconds_sum{_app="application-metrics-1.0-snapshot"} 2.000146096
application_com_ensode_applicationmetrics_MetricsDemo_timedExample_seconds{_app="application-metrics-1.0-snapshot",quantile="0.5"} 2.000146096
application_com_ensode_applicationmetrics_MetricsDemo_timedExample_seconds{_app="application-metrics-1.0-snapshot",quantile="0.75"} 2.000146096
application_com_ensode_applicationmetrics_MetricsDemo_timedExample_seconds{_app="application-metrics-1.0-snapshot",quantile="0.95"} 2.000146096
application_com_ensode_applicationmetrics_MetricsDemo_timedExample_seconds{_app="application-metrics-1.0-snapshot",quantile="0.98"} 2.000146096
application_com_ensode_applicationmetrics_MetricsDemo_timedExample_seconds{_app="application-metrics-1.0-snapshot",quantile="0.99"} 2.000146096
application_com_ensode_applicationmetrics_MetricsDemo_timedExample_seconds{_app="application-metrics-1.0-snapshot",quantile="0.999"} 2.000146096
```

`@Timed` 的 JSON 格式输出如下所示：

```
{
"com.ensode.applicationmetrics.MetricsDemo.timedExample": {
"elapsedTime;_app=application-metrics-1.0-snapshot": 2000,
"count;_app=application-metrics-1.0-snapshot": 1,
"meanRate;_app=application-metrics-1.0-snapshot": 0.005027283318233858,
"oneMinRate;_app=application-metrics-1.0-snapshot": 0.008428768701855296,
"fiveMinRate;_app=application-metrics-1.0-snapshot": 0.10616389011240278,
"fifteenMinRate;_app=application-metrics-1.0-snapshot": 0.16193681939667523,
"min;_app=application-metrics-1.0-snapshot": 2000146096,
"max;_app=application-metrics-1.0-snapshot": 2000146096,
"mean;_app=application-metrics-1.0-snapshot": 2000146096,
"stddev;_app=application-metrics-1.0-snapshot": 0.0,
"p50;_app=application-metrics-1.0-snapshot": 2000146096,
"p75;_app=application-metrics-1.0-snapshot": 2000146096,
"p95;_app=application-metrics-1.0-snapshot": 2000146096,
"p98;_app=application-metrics-1.0-snapshot": 2000146096,
"p99;_app=application-metrics-1.0-snapshot": 2000146096,
"p999;_app=application-metrics-1.0-snapshot": 2000146096
}
```

### @SimplyTimed

有时，我们并不需要 `@Timed` 提供的所有数据；`@SimplyTimed` 仅提供少量数据，即所有调用的总耗时、方法被调用的次数，以及方法执行所花费的最大和最小时间。

```
@GET
@SimplyTimed
@Path("simplytimed")
@Produces(MediaType.TEXT_PLAIN)
public void simplyTimedExample() throws InterruptedException {
TimeUnit.SECONDS.sleep(3);
}
```

`@SimplyTimed` 的输出如下所示：

```
# TYPE application_com_ensode_applicationmetrics_MetricsDemo_simplyTimedExample_elapsedTime_seconds gauge
application_com_ensode_applicationmetrics_MetricsDemo_simplyTimedExample_elapsedTime_seconds{_app="application-metrics-1.0-snapshot"} 18.001398226
# TYPE application_com_ensode_applicationmetrics_MetricsDemo_simplyTimedExample_maxTimeDuration_seconds gauge
application_com_ensode_applicationmetrics_MetricsDemo_simplyTimedExample_maxTimeDuration_seconds{_app="application-metrics-1.0-snapshot"} 3.000302743
# TYPE application_com_ensode_applicationmetrics_MetricsDemo_simplyTimedExample_minTimeDuration_seconds gauge
100 11184   0 11184   0    0  1365k     0 --:--:-- --:--:-- --:--:-- 1365k
application_com_ensode_applicationmetrics_MetricsDemo_simplyTimedExample_minTimeDuration_seconds{_app="application-metrics-1.0-snapshot"} 3.000122495
```

或者以 JSON 格式输出：

```
{
"com.ensode.applicationmetrics.MetricsDemo.simplyTimedExample": {
"count;_app=application-metrics-1.0-snapshot": 11,
"elapsedTime;_app=application-metrics-1.0-snapshot": 33002,
"maxTimeDuration;_app=application-metrics-1.0-snapshot": 3000,
"minTimeDuration;_app=application-metrics-1.0-snapshot": 3000
}
}
```



### 程序化应用指标

大多数 MicroProfile 指标注解都有对应的程序化实现可供使用。例如，`Counter` 接口就等同于 `@Counted` 注解。通过程序化方式实现指标，我们可以对注解的值施加更多控制；以 Counter 为例，我们可以根据业务需求增加甚至减少计数器的值，而 `@Counted` 注解则只能在被注解的方法被调用时增加计数器，并且无法通过注解来减少计数器的值。

以下示例说明了 MicroProfile 指标 `Counted` 接口的用法。

```
package com.ensode.application.metrics.programmatic;
//imports omitted
@ApplicationScoped
@Path("employeeservice")
public class EmployeeResource {
@Inject
@Metric
private Counter employeeCounter;
private List employeeList =
snew CopyOnWriteArrayList(); //thread safe
@PUT
@Consumes(MediaType.APPLICATION_JSON)
public void hireEmployee(Employee employee) {
employeeList.add(employee);
employeeCounter.inc();
}
@DELETE
@Consumes(MediaType.APPLICATION_JSON)
public void fireEmployee(@QueryParam("firstName") String firstName, @QueryParam("lastName") String lastName) {
Optional employeeToFire =
employeeList.stream().filter(emp →
emp.getFirstName().equals(firstName) &&
emp.getLastName().equals(lastName)).findAny();
employeeToFire.ifPresent(
emp -> {
employeeList.remove(emp);
employeeCounter.inc(-1);
});
}
}
```

在前面的示例中，我们通过 CDI 的 `@Inject` 注解和 MicroProfile 的 `@Metric` 注解注入了一个 `Counter` 接口的实现。`@Metric` 将计数器实例标识为一个指标，并让 MicroProfile Metrics API 知道需要使用它来生成特定于应用的指标。

一旦我们注入了 `Counter` 实现，就可以根据业务需求增加或减少计数器的值。在我们的示例中，计数器用于跟踪 `List` 中的员工数量；每当雇佣一名员工时，计数器增加；每当解雇一名员工时，计数器减少。

表 6-1 列出了我们可以程序化使用的指标及其对应的注解。

表 6-1

程序化指标

| 指标接口 | 注解 |
| --- | --- |
| `Counter` | `@Counted` |
| `ConcurrentGauge` | `@ConcurrentGauge` |
| `SimpleTimer` | `@SimpleTimer` |
| `Timer` | `@Timer` |

我们已经看到了 `Counter` 接口的示例；`ConcurrentGauge` 的工作方式类似，但它提供了一个 `dec()` 方法，我们可以用其来递减计数器。`Timer` 和 `SimpleTimer` 都提供了 `time()` 方法的重载版本；通过传入一个 `Callable` 或 `Runnable` 接口实现的实例（通常以 lambda 表达式实现），我们可以用它来计时应用中的特定事件。例如，如果我们想找出向员工列表中添加一名员工需要多长时间，可以这样做：

```
package com.ensode.application.metrics.programmatic;
//imports omitted
@ApplicationScoped
@Path("employeeservice")
public class EmployeeResource {
@Inject
@Metric
private SimpleTimer employeeAddSimpleTimer;
private List employeeList = new CopyOnWriteArrayList(); //thread safe
@PUT
@Consumes(MediaType.APPLICATION_JSON)
public void hireEmployee(Employee employee) {
employeeAddSimpleTimer.time(() -> {
employeeList.add(employee);
});
}
}
```

在这个示例中，我们像往常一样通过 `@Inject` 和 `@Metric` 注解注入了一个 `SimpleTimer` 实例，然后将向列表中添加员工的调用包装在一个 lambda 表达式中，并将生成的 `Runnable` 实现传递给 `SimpleTimer` 的 `time()` 方法。

MicroProfile API 还提供了另一种指标，称为 `Histogram`，它用于计算值的分布情况。要使用 Histogram，我们像往常一样注入它，然后调用其 `update()` 方法向直方图添加值，如下例所示。

```
package com.ensode.application.metrics.programmatic;
//imports omitted
@ApplicationScoped
@Path("employeeservice")
public class EmployeeResource {
@Inject
@Metric
private Histogram histogram;
private List employeeList = new CopyOnWriteArrayList(); //thread safe
@PUT
@Consumes(MediaType.APPLICATION_JSON)
public void hireEmployee(Employee employee) {
employeeList.add(employee);
histogram.update(employeeList.size());
}
}
```

## 在 Payara Micro 中配置 MicroProfile 指标

我们可以通过 asadmin 命令 `set-metrics-configuration` 在 Payara Micro 中配置指标，该命令可以通过作为启动后命令文件或类似方式传递的命令文件来执行。

### 禁用指标

Payara Micro 默认启用指标；如果我们希望禁用它们，可以使用以下 asadmin 命令：

```
set-metrics-configuration --enabled=false
```

### 保护指标安全

默认情况下，`/metrics` 端点是不安全的，这意味着任何未经身份验证的随机用户都可以访问它。

如果我们希望保护它，可以通过发出以下 asadmin 命令来实现：

```
set-metrics-configuation --securityenabled=true
```

在保护 `/metrics` 端点时，我们可以指定哪些角色可以访问它，如下所示：

```
set-metrics-configuration --roles=role1,role2,role3
```

`--roles` 参数的值是一个逗号分隔的角色列表，这些角色被允许访问生成的指标。

### 自定义指标端点

默认情况下，可以通过 `/metrics` 端点检索指标。如果需要，我们可以使用不同的端点。

```
set-metrics-configuration --endpoint=foo
```

`--endpoint` 参数的值是我们自定义指标端点的上下文根。

### 静态指标

默认情况下，指标会随着应用的运行而动态更新；如果我们希望使用静态指标（在应用重启之前不会更新），可以按如下方式操作：

```
set-metrics-configuration --dynamic=false
```

## 总结

在本章中，我们介绍了 Payara Micro 对 MicroProfile Metrics API 的支持。我们生成指标以供监控工具使用。我们介绍了如何使用 MicroProfile 指标提供的注解来几乎不费吹灰之力地生成指标；我们还了解了如何在需要更多控制指标生成方式的情况下，通过程序化方式生成指标。

