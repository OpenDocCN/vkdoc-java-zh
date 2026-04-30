# 9. 使用 Metrics 进行计数

已部署的面向用户的应用需要被监控，以确保它们按预期交付。部署到复杂云托管环境中的云原生应用更需要被监控，因为任何与预设期望的意外偏差都可能意味着显著增加的托管费用。

监控已部署的正在运行的应用的方式是使用应用指标。诸如方法被调用了多少次、给定方法返回需要多少秒等指标，都是不同类型的指标，可以深入了解应用的性能表现。

Eclipse MicroProfile Metrics API 提供了一组 API 结构，用于暴露不同类型和种类的应用指标。使用此 API，你可以通过注解和 Java API，以 OpenMetrics^(¹¹³) 格式暴露应用、实现运行时和 JVM 指标，这些指标可被 Prometheus 等监控应用消费。

本章涵盖了 Jakarta EE 上下文中的 Metrics 规范。到本章结束时，你将能够使用 Metrics API 在你的云原生 Jakarta EE 应用中暴露不同类型的指标。

## 结构

Eclipse MicroProfile Metrics 规范主要是一个元数据驱动的 API，用于在应用中暴露指标。你可以暴露的指标的范围和类型取决于应用需求。通过使用此 API 暴露的所有指标都会自动托管在上下文路径 `/metrics` 下。

对于本地开发，这通常是 `http://localhost:8080/metrics`；对于部署到 `foo.com` 的应用，指标将托管在 [`https://foo.com/metrics`](https://foo.com/metrics)。该规范定义了三个范围或类别的指标。分别是基础指标、应用指标和供应商特定指标。

### 基础指标

基础指标是所有实现都必须提供的指标。这些指标暴露底层 JVM 和操作系统的信息。其中一些基础指标包括可用处理器、已用堆内存、最大堆内存和已提交堆内存等。基础指标下的一些指标可能是可选的，因为它们可能涉及特定的 JVM 或操作系统版本。这些指标托管在路径 `/metrics/base` 下。

### 应用指标

应用指标是你希望收集的关于应用的自定义指标集合。你可以使用注解或 Java API 来暴露这些指标。此类指标的示例包括给定方法执行所需的时间。这些指标可通过 `/metrics/application` 访问。

### 供应商指标

供应商指标是 Eclipse MicroProfile 规范的兼容实现在强制性的基础指标之上可选提供的自定义指标。供应商指标托管在 `/metrics/vendor` 下。



## 指标数据格式

默认情况下，当向 `/metrics` 资源发起 GET 请求时，返回的指标信息采用 OpenMetrics 文本格式。这是一种开放标准的指标格式，受到 Prometheus 等众多监控应用程序的支持。

然而，如果 HTTP 请求的 `Accept` 头部匹配 `application/json`，则相同的指标数据可以以 JSON 格式返回。如果请求的 `Accept` 头部同时匹配 `application/json` 和 `text/plain`，或者未指定 `Accept` 头部，则数据将以默认的 OpenMetrics 文本格式返回。

## 指标注册表

指标注册表是一个注册表，其中包含所有可在 `/metrics` 资源下访问的指标及其元数据。对于基础（base）、应用程序（application）和供应商（vendor）这三个指标作用域，各自有一个共享的单例注册表。指标通过其指标 ID（由名称和标签（如有）组成）在注册表中进行标识。可以将指标注册表视为每个作用域中指标的数据库。作为应用程序开发者，大多数时候注册表对您来说是透明的。但当您需要与之交互时，您应该知道它的存在。

## 使用指标

在您的应用程序中，有两种方式可以使用 Metrics API。一种是通过注解，另一种是通过 Java API。选择哪种方式仅取决于应用程序需求和开发者偏好。注解是由 CDI 运行时管理的拦截器绑定。因此，只有可代理的托管 Bean 才能使用注解来收集指标。

### 注解

有六个用于创建不同类型指标的注解。它们是：

*   `@Counted`

*   `@ConcurrentGauge`

*   `@Gauge`

*   `@Metered`

*   `@SimplyTimed`

*   `@Timed`

所有这些注解都有一些共同的参数。这些参数如表 9-1 所示。

表 9-1

展示通用的指标注解参数

| 名称 | 类型 | 描述 | 可选/必需 |
| --- | --- | --- | --- |
| name | String | 设置指标的名称 | 可选。默认为被注解对象的名称 |
| absolute | boolean | 如果为 true，则 name 用作指标的绝对名称。如果为 false，则在给定名称前添加包名和类名 | 可选。默认为 false |
| displayName | String | 用于元数据的人类可读名称 | 可选。默认为 null |
| description | String | 指标的描述 | 可选。默认为 null |
| unit | String | 特定指标的单位 | 可选。默认值各不相同 |
| tags | String[] | 指标的“key=value”标签数组 | 可选。空数组 |

#### @Counted

`@Counted` 指标注解用于统计被注解的方法、构造函数或类型被调用或实例化的次数。此注解可用于方法、构造函数或类。如果在类级别使用，则该类中的所有方法都将被统计其调用次数。清单 9-1 展示了 `RateResourceImpl` 类中的 `convertCurrency` 方法使用了 `@Counted` 注解，并传入了一些参数。

```
@Consumes(MediaType.APPLICATION_JSON)
@Produces(MediaType.APPLICATION_JSON)
@Path("rates")
public class RateResourceImpl {
@Path("convert")
@POST
@Counted(name = "convert_currency",
absolute = true,
displayName = "Convert currency method",
description = "Method to convert one currency to the other",
tags = { "info=conversions", "type=meta" })
ConvertCurrencyResponse convertCurrency(@Valid ConvertCurrencyRequest convertCurrencyRequest){
return rateService.convertCurrency(convertCurrencyRequest)
};
}
清单 9-1
展示了 @Counted 的使用
```

清单 9-1 展示了在 `convertCurrency` 方法上使用的 `@Counted` 注解。它设置了 `name` 参数，并将 `absolute` 设置为 `true`。这意味着将在注册表中注册的此指标的名称为 `convert_currency`。如果 `absolute` 未设置为 `true`，则指标名称将为 包名 + 类名 + 传递给 `name` 参数的值。标签也被设置为一些特定于方法的信息。

标签在指标中扮演着重要角色，因为在云原生环境中，已部署容器的编排由 Kubernetes 等引擎完成，应用程序运行时与主机之间的传统映射关系已不复存在。在指标上下文中，除了指标名称之外，标签还增加了用于标识指标的额外维度。

当应用程序部署后，可以直接从 `/metrics/application/convert_currency` 访问被计数的指标。由于 `absolute` 参数设置为 `true`，仅凭指标名称即可通过 REST 端点访问它。以 OpenMetrics 格式对此指标进行示例调用，将返回如清单 9-2 所示的结果。

```
# TYPE application_convert_currency_total counter
# HELP application_convert_currency_total Method to convert one currency to the other
application_convert_currency_total{info="conversions",type="meta"} 75
清单 9-2
展示 @Counted 的示例指标
```

清单 9-2 展示了直接从 `/metrics/application` 上下文路径访问 `@Counted` 注解时的结果。返回的数据带有 `TYPE` 和 `HELP` 注释，分别显示指标的类型和描述。指标计数前带有 `application_` 前缀。这就是应用程序级指标与基础级和供应商级指标区分开来的方式。传递给 `@Counted` 注解的标签也会作为完整指标 ID 的一部分返回。根据计数，`convertCurrency` 方法总共被调用了 75 次。



#### @ConcurrentGauge

此注解用于测量被注解方法或构造器的并发调用次数，或被注解类的实例化次数。其工作原理是：当进入被注解元素时，并发计数加一；退出时，并发计数减一。该指标用于测量被注解元素的并发调用数量。

当用于类时，会为类的每个构造器和方法创建当前并发调用计数、前一分钟最大值和前一分钟最小值，并使用注解的名称和绝对字段值在指标注册表中进行注册。

当用于方法时，会创建当前值、前一分钟最大值和前一分钟最小值的测量指标，并在指标注册表中注册。当用于构造器时，会为该构造器注册相同的测量指标。清单 9-3 展示了使用 `@ConcurrentGauge` 注解的 `convertCurrency` 方法。

```
@Consumes(MediaType.APPLICATION_JSON)
@Produces(MediaType.APPLICATION_JSON)
@Path("rates")
public class RateResourceImpl {
@Path("convert")
@POST
@ConcurrentGauge(name = "convert_currency_concurrent_gauge",
absolute = true,
description = "Method to convert one currency to the other",
tags = { "convert=custom" }
)
ConvertCurrencyResponse convertCurrency(@Valid ConvertCurrencyRequest convertCurrencyRequest){};
}
清单 9-3
展示 convertCurrencyMethod 上的 @ConcurrentGauge
```

清单 9-3 展示了使用 `@ConcurrentGauge` 注解的 `convertCurrency` 方法。其中设置了名称、绝对路径、描述以及一些自定义标签。可通过 `/metrics/application/convert_currency_concurrent_gauge` 访问该指标。对默认文本格式的示例调用返回的数据如清单 9-4 所示。

```
# TYPE application_convert_currency_concurrent_gauge_current gauge
# HELP application_convert_currency_concurrent_gauge_current Method to convert one currency to the other
application_convert_currency_concurrent_gauge_current{convert="custom"} 0
# TYPE application_convert_currency_concurrent_gauge_min gauge
application_convert_currency_concurrent_gauge_min{convert="custom"} 0
# TYPE application_convert_currency_concurrent_gauge_max gauge
application_convert_currency_concurrent_gauge_max{convert="custom"} 1
清单 9-4
展示 @ConcurrentGauge 指标的结果
```

#### @Gauge

`@Gauge` 指标用于采样方法的返回值。该方法通常是一个业务方法，返回某种有意义的业务上下文值用于分析。Metrics 运行时会调用该方法，并将返回值赋值为该指标的值。`@Gauge` 只能用于方法。

例如，如果我们想采样 jwallet 中的货币转换次数，`@Gauge` 就是完美的指标。方法的返回值即为该指标的值。此指标只能与 `@ApplicationScoped` 或 `@Singleton` CDI bean 一起使用，因为与其他非单例 bean 一起使用时，无法明确使用哪个 bean 实例来获取测量值。清单 9-5 展示了带有 `countConversions` 测量方法的 `RateService` `@ApplicationScoped` bean。

```
@ApplicationScoped
public class RateService {
@Gauge(name = "convert_currency_gauge_count", unit = MetricUnits.NONE, absolute = true)
public int countConversions() {
return ThreadLocalRandom.current().nextInt(250, 500 + 1);
}
}
清单 9-5
展示 @Gauge 的使用
```

清单 9-5 展示了带有返回 int 类型的 `countConversions` 方法的 `RateService` 单例。当查询此指标时，运行时会调用此方法，并使用返回的方法值作为指标的值。通过名称，可以直接从路径 `/metrics/application/convert_currency_gauge_count` 访问此指标。示例调用返回的数据如清单 9-6 所示。

```
# TYPE application_convert_currency_gauge_count gauge
application_convert_currency_gauge_count 474
清单 9-6
展示 @Gauge 指标的示例返回数据
```

#### @Metered

`@Metered` 注解是一个指标，用于跟踪被注解方法、构造器或类实例化的调用频率。它跟踪平均吞吐量以及一分钟、五分钟和十五分钟的指数加权移动平均吞吐量。默认的 `MetricUnit` 是秒。`@Metered` 可用于类、方法或构造器。

```
@Consumes(MediaType.APPLICATION_JSON)
@Produces(MediaType.APPLICATION_JSON)
@Path("rates")
public class RateResourceImpl implements RateResource {
@Inject
RateService rateService;
@Path("convert")
@POST
@Override
@Metered(name = "convert_currency_meter", absolute = true)
public ConvertCurrencyResponse convertCurrency(ConvertCurrencyRequest convertCurrencyRequest) {
return rateService.convertCurrency(convertCurrencyRequest);
}
}
清单 9-7
展示带有 @Metered 注解的 convertCurrency 方法
```

清单 9-7 展示了 `convertCurrency` 方法上的 `@Metered` 注解。使用传递的绝对名称，可以从路径 `/metrics/application/convert_currency_meter` 查询该指标，示例返回数据如清单 9-8 所示。

```
# TYPE application_convert_currency_meter_total counter
application_convert_currency_meter_total 5
# TYPE application_convert_currency_meter_rate_per_second gauge
application_convert_currency_meter_rate_per_second 0.017981750828888407
# TYPE application_convert_currency_meter_one_min_rate_per_second gauge
application_convert_currency_meter_one_min_rate_per_second 0.009080280587651975
# TYPE application_convert_currency_meter_five_min_rate_per_second gauge
application_convert_currency_meter_five_min_rate_per_second 0.3266223165978052
# TYPE application_convert_currency_meter_fifteen_min_rate_per_second gauge
application_convert_currency_meter_fifteen_min_rate_per_second 0.5934799986177786
清单 9-8
展示 convertCurrency 方法的 @Metered 示例返回数据
```



#### @SimplyTimed

`@SimplyTimed`注解是一种度量指标，用于标记方法、构造函数或类为简单计时。所谓简单计时，即通过计时器追踪经过的时间时长和次数。它以纳秒为单位测量时长。清单 9-9 展示了`RateResourceImpl`的`convertCurrency`方法使用了`@SimplyTimed`进行简单计时。

```
@Consumes(MediaType.APPLICATION_JSON)
@Produces(MediaType.APPLICATION_JSON)
@Path("rates")
public class RateResourceImpl implements RateResource {
@Inject
RateService rateService;
@Path("convert")
@POST
@Override
@SimplyTimed(name = "convert_currency_simple_timer", absolute = true)
public ConvertCurrencyResponse convertCurrency(ConvertCurrencyRequest convertCurrencyRequest) {
return rateService.convertCurrency(convertCurrencyRequest);
}
}
清单 9-9
展示在 convertCurrency 方法上使用 @SimplyTimed 注解
```

清单 9-9 展示了`@SimplyTimed`度量指标，传入了绝对名称`convert_currency_simple_timer`，使得该指标可直接从`/metrics/application/convert_currency_simple_timer`访问。调用该端点会返回清单 9-10 所示的示例数据。

```
# TYPE application_convert_currency_simple_timer_total counter
application_convert_currency_simple_timer_total 45
# TYPE application_convert_currency_simple_timer_elapsedTime_seconds gauge
application_convert_currency_simple_timer_elapsedTime_seconds 33.182224819000005
# TYPE application_convert_currency_simple_timer_maxTimeDuration_seconds gauge
application_convert_currency_simple_timer_maxTimeDuration_seconds 1.5874110110000001
# TYPE application_convert_currency_simple_timer_minTimeDuration_seconds gauge
application_convert_currency_simple_timer_minTimeDuration_seconds 0.586819599
清单 9-10
展示 convertCurrency 方法上 @SimplyTimed 度量指标的结果
```

#### @Timed

`@Timed`度量指标用于追踪类、构造函数或方法被调用的频率，并记录调用完成所花费的时间。`@Timed`会聚合计时时长，为被注解的对象提供时长和吞吐量统计。与仅追踪经过时长和次数的`@SimplyTimed`相比，这是一个更详细的度量指标。清单 9-11 展示了使用`@Timed`注解的`convertCurrency`方法。

```
@Consumes(MediaType.APPLICATION_JSON)
@Produces(MediaType.APPLICATION_JSON)
@Path("rates")
public class RateResourceImpl implements RateResource {
@Inject
RateService rateService;
@Path("convert")
@POST
@Override
@Timed(name = "convert_currency_timer", absolute = true)
public ConvertCurrencyResponse convertCurrency(ConvertCurrencyRequest convertCurrencyRequest) {
return rateService.convertCurrency(convertCurrencyRequest);
}
}
清单 9-11
展示 @Timed 的使用
```

清单 9-11 展示了`convertCurrency`方法上的`@Timed`度量指标。使用绝对名称后，该指标可直接从`/metrics/application/convert_currency_timer`访问。一个示例调用会返回清单 9-12 所示的度量数据。

```
# TYPE application_convert_currency_timer_rate_per_second gauge
application_convert_currency_timer_rate_per_second 0.07333255542294032
# TYPE application_convert_currency_timer_one_min_rate_per_second gauge
application_convert_currency_timer_one_min_rate_per_second 3.0039512838912655E-4
# TYPE application_convert_currency_timer_five_min_rate_per_second gauge
application_convert_currency_timer_five_min_rate_per_second 0.02560887973769466
# TYPE application_convert_currency_timer_fifteen_min_rate_per_second gauge
application_convert_currency_timer_fifteen_min_rate_per_second 0.02345612678178861
# TYPE application_convert_currency_timer_mean_seconds gauge
application_convert_currency_timer_mean_seconds 0.9913156544019269
# TYPE application_convert_currency_timer_max_seconds gauge
application_convert_currency_timer_max_seconds 4.622333082
# TYPE application_convert_currency_timer_min_seconds gauge
application_convert_currency_timer_min_seconds 0.375723652
# TYPE application_convert_currency_timer_stddev_seconds gauge
application_convert_currency_timer_stddev_seconds 0.8967743131501213
# TYPE application_convert_currency_timer_seconds summary
application_convert_currency_timer_seconds_count 35
application_convert_currency_timer_seconds_sum 34.812993443
application_convert_currency_timer_seconds{quantile="0.5"} 0.687353305
application_convert_currency_timer_seconds{quantile="0.75"} 1.037883712
application_convert_currency_timer_seconds{quantile="0.95"} 4.2614521750000005
application_convert_currency_timer_seconds{quantile="0.98"} 4.622333082
application_convert_currency_timer_seconds{quantile="0.99"} 4.622333082
application_convert_currency_timer_seconds{quantile="0.999"} 4.622333082
清单 9-12
展示 @Timed 度量数据
```

从返回的数据可以看出，与`@SimplyTimed`度量指标相比，`@Timed`度量指标返回了关于方法调用的更详细信息。当您需要轻量级和重量级计时指标时，这两者可以相互补充。

### 注入度量指标

除了使用注解之外，您还可以在应用程序中直接注入类型为`meter`、`timer`、`counter`和`histogram`的度量指标。您可以使用`@Metric`注解结合`@Inject`将度量指标注入到字段、方法和参数中。`@Metric`注解支持各个度量指标所支持的所有元数据，例如名称、描述和绝对值。



#### 使用直方图指标

直方图是一种没有注解对应项的指标。这是一种复杂的指标，用于测量最小值、最大值、平均值、标准差以及中位数或第 95 百分位数等四分位数。要使用此指标，我们可以像清单 9-13 中所示，在`RateService` Bean 中通过注入的方式使用它。

```
@ApplicationScoped
public class RateService {
@Inject
@Metric(name = "count_conversions_histogram",
absolute = true,
description = "A histogram for the count of conversions",
tags = { "count=histogram", "metric=histogram" })
Histogram histogram;
@Gauge(name = "convert_currency_gauge_count", unit = MetricUnits.NONE, absolute = true)
public int countConversions() {
int conversionCount = ThreadLocalRandom.current().nextInt(250, 500 + 1);
histogram.update(conversionCount);
return conversionCount;
}
}
清单 9-13
展示了通过注入使用直方图指标
```

清单 9-13 展示了如何结合使用`@Inject`和`@Metric`来注入`Histogram`指标。此指标接受`long`或`int`类型的值作为其`update`方法的参数，以更新内部数据。`@Metric`注解用于向指标传递名称、描述和绝对元数据值。在`countConversions`方法中，返回的计数被传递给`update`方法进行计算。由于名称设置为绝对路径，因此可以通过路径`/metrics/application/count_conversions_histogram`查询该指标。清单 9-14 展示了调用后返回的示例数据。

```
# TYPE application_count_conversions_histogram_mean gauge
application_count_conversions_histogram_mean{count="histogram",metric="histogram"} 417.7099758660769
# TYPE application_count_conversions_histogram_max gauge
application_count_conversions_histogram_max{count="histogram",metric="histogram"} 500.0
# TYPE application_count_conversions_histogram_min gauge
application_count_conversions_histogram_min{count="histogram",metric="histogram"} 253.0
# TYPE application_count_conversions_histogram_stddev gauge
application_count_conversions_histogram_stddev{count="histogram",metric="histogram"} 82.50134125075685
# TYPE application_count_conversions_histogram summary
# HELP application_count_conversions_histogram A histogram for the count of conversions
application_count_conversions_histogram_count{count="histogram",metric="histogram"} 21
application_count_conversions_histogram_sum{count="histogram",metric="histogram"} 8541
application_count_conversions_histogram{count="histogram",metric="histogram",quantile="0.5"} 462.0
application_count_conversions_histogram{count="histogram",metric="histogram",quantile="0.75"} 490.0
application_count_conversions_histogram{count="histogram",metric="histogram",quantile="0.95"} 500.0
application_count_conversions_histogram{count="histogram",metric="histogram",quantile="0.98"} 500.0
application_count_conversions_histogram{count="histogram",metric="histogram",quantile="0.99"} 500.0
application_count_conversions_histogram{count="histogram",metric="histogram",quantile="0.999"} 500.0
清单 9-14
展示了直方图指标返回的数据
```

如清单 9-14 所示，直方图指标会返回您想要跟踪的特定对象的统计数据。与其他指标不同，您需要像清单 9-13 中那样注入直方图，使其自动注册到注册表中，或者手动注册它。

#### 手动注册指标

直方图指标同样可以手动实例化并注册到指标注册表中。首先通过 CDI 注入`MetricRegistry`，然后通过`MetaData`接口构建元数据，最后在注入的`MetricRegistry`实例上调用`histogram`方法来注册并获取一个`Histogram`实例。清单 9-15 在`RateService` Bean 的代码中展示了这些步骤。

```
@ApplicationScoped
public class RateService {
@Inject
MetricRegistry registry;
@Gauge(name = "convert_currency_gauge_count", unit = MetricUnits.NONE, absolute = true)
public int countConversions() {
int conversionCount = ThreadLocalRandom.current().nextInt(250, 500 + 1);
Metadata metadata = Metadata.builder()
.withName("count_conversions_histogram")
.withDescription("A histogram for the count of conversions")
.withDisplayName("A histogram")
.build();
Histogram manualHistogram = registry.histogram(metadata);
manualHistogram.update(conversionCount);
return conversionCount;
}
}
清单 9-15
展示了直方图指标的手动实例化
```

清单 9-15 展示了在`RateService` Bean 中手动实例化直方图指标。`MetricRegistry`通过`@Inject`注入。然后使用构建器模式构建一个`Metadata`对象。此`Metadata`包含的详细信息与您传递给`@Metric`注解的完全相同。接着，在`MetricRegistry`对象上调用`histogram`方法，并将刚刚构建的元数据对象传递给它。此调用返回一个`Histogram`实例，随后可用于创建直方图。无论使用哪种构造方式实例化，`Histogram`的功能都是相同的。

## 指标元数据

元数据是帮助描述特定指标的所有信息。您可以通过向`/metric`或其任何变体发出 HTTP OPTIONS 请求，来检索单个指标、特定作用域或整个指标集的元数据。OPTIONS 请求的`Accept`头应为`application/json`，因为元数据以 JSON 格式返回。例如，我们可以通过向`/metrics/application`发出 HTTP OPTIONS 请求，来获取本章涵盖的所有指标的元数据。清单 9-16 中显示的示例调用会返回清单 9-17 中所示的元数据。

```
{
"convert_currency_simple_timer": {
"unit": "nanoseconds",
"displayName": "convert_currency_simple_timer",
"name": "convert_currency_simple_timer",
"description": "",
"type": "simple timer",
"tags": []
},
"convert_currency_counted": {
"unit": "none",
"displayName": "Convert currency method",
"name": "convert_currency_counted",
"description": "Method to convert one currency to the other",
"type": "counter",
"tags": [
[
"info=conversions",
"type=meta"
]
]
},
"convert_currency_timer": {
"unit": "nanoseconds",
"displayName": "convert_currency_timer",
"name": "convert_currency_timer",
"description": "",
"type": "timer",
"tags": []
},
"convert_currency_meter": {
"unit": "per_second",
"displayName": "convert_currency_meter",
"name": "convert_currency_meter",
"description": "",
"type": "meter",
"tags": []
},
"count_conversions_histogram": {
"unit": "none",
"displayName": "count_conversions_histogram",
"name": "count_conversions_histogram",
"description": "A histogram for the count of conversions",
"type": "histogram",
"tags": [
[
"count=histogram",
"metric=histogram"
]
]
},
"convert_currency_concurrent_gauge": {
"unit": "none",
"displayName": "convert_currency_concurrent_gauge",
"name": "convert_currency_concurrent_gauge",
"description": "Method to convert one currency to the other",
"type": "concurrent gauge",
"tags": [
[
"convert=custom"
]
]
}
}
清单 9-17
展示了应用程序指标作用域的元数据
```

```
OPTIONS http://localhost:3001/metrics/application/base
Accept: application/json
清单 9-16
展示了一个示例元数据调用
```



## 其他 MicroProfile 规范指标

作为一套紧密集成的规范套件，在应用程序中使用容错 API 会自动在 `/metrics/base` 作用域下暴露相关指标。作为开发者，你只需使用容错 API，这些指标就会自动为你暴露。例如，对 `/metrics/base` 端点的调用会返回所有容错指标，清单 9-18 展示了其中一部分示例。

```
# TYPE base_ft_bulkhead_runningDuration_mean_seconds gauge
base_ft_bulkhead_runningDuration_mean_seconds{method="com.example.jwallet.rate.rate.control.RateService.convertCurrency"} 0.6664609707678424
# TYPE base_ft_bulkhead_runningDuration_max_seconds gauge
base_ft_bulkhead_runningDuration_max_seconds{method="com.example.jwallet.rate.rate.control.RateService.convertCurrency"} 7.678077344
# TYPE base_ft_bulkhead_runningDuration_min_seconds gauge
base_ft_bulkhead_runningDuration_min_seconds{method="com.example.jwallet.rate.rate.control.RateService.convertCurrency"} 0.28274852300000003
# TYPE base_ft_bulkhead_runningDuration_stddev_seconds gauge
base_ft_bulkhead_runningDuration_stddev_seconds{method="com.example.jwallet.rate.rate.control.RateService.convertCurrency"} 1.504029612722161
# TYPE base_ft_bulkhead_runningDuration_seconds summary
base_ft_bulkhead_runningDuration_seconds_count{method="com.example.jwallet.rate.rate.control.RateService.convertCurrency"} 20
base_ft_bulkhead_runningDuration_seconds_sum{method="com.example.jwallet.rate.rate.control.RateService.convertCurrency"} 14199386249
base_ft_bulkhead_runningDuration_seconds{method="com.example.jwallet.rate.rate.control.RateService.convertCurrency",quantile="0.5"} 0.308184634
base_ft_bulkhead_runningDuration_seconds{method="com.example.jwallet.rate.rate.control.RateService.convertCurrency",quantile="0.75"} 0.39874514600000005
base_ft_bulkhead_runningDuration_seconds{method="com.example.jwallet.rate.rate.control.RateService.convertCurrency",quantile="0.95"} 0.48290750400000004
base_ft_bulkhead_runningDuration_seconds{method="com.example.jwallet.rate.rate.control.RateService.convertCurrency",quantile="0.98"} 7.678077344
base_ft_bulkhead_runningDuration_seconds{method="com.example.jwallet.rate.rate.control.RateService.convertCurrency",quantile="0.99"} 7.678077344
base_ft_bulkhead_runningDuration_seconds{method="com.example.jwallet.rate.rate.control.RateService.convertCurrency",quantile="0.999"} 7.678077344
清单 9-18
展示容错指标示例
```

## CDI 原型与指标

正如在 Jakarta 上下文和依赖注入章节中所讨论的，CDI 原型是一种将通用基础设施注解分组到一个注解下的方式。在 bean 上使用生成的原型，就如同单独在该 bean 上使用了组成该原型的所有注解。

因此，当你用任何指标注解来注解一个原型时，生成的 bean 将被指标运行时视为该指标注解直接应用到了 bean 上。生成的指标名称是相对于 bean 本身的名称和包计算的，而不是相对于原型。

例如，在第 4 章中开发的 `@Action` 原型，在清单 9-19 中重现，并添加了 `@Counted` 和 `@SimplyTimed` 注解。这些注解将导致所有被 `@Action` 注解的 bean 的构造函数和方法调用分别被计数和简单计时。

```
@Stereotype
@RequestScoped
@Counted
@SimplyTimed
@Transactional
@Logged
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE)
public @interface Action {
}
清单 9-19
展示带有 @Counted 和 @SimplyTimed 的 @Action 原型
```

## 本章小结

本章讨论了用于 Jakarta EE 平台的 Eclipse MicroProfile 指标规范。我们研究了指标作用域、数据格式，以及如何通过注解、CDI 注入或手动实例化来使用指标。在整个过程中，你学习了如何从支持 MicroProfile 的应用程序提供的 `/metrics` 端点检索指标信息。

脚注 1

