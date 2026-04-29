# 9. 请求追踪

典型的微服务架构由多个微服务组成。这些服务将输出发送到日志文件；传统上，每个服务都将其输出发送到自己的日志文件。当跨服务器实例查看日志文件时，几乎不可能轻松地追踪跨微服务的单个执行轨迹；例如，假设我们有两个服务 A 和 B，并且服务 A 充当服务 B 的客户端。通常，我们会在日志中看到从服务 A 到服务 B 的多次调用，但很少能轻松地将服务 A 的哪些调用与服务 B 接收到的哪些调用匹配起来。此外，服务 B 可能被除服务 A 之外的其他客户端调用，这使得在我们的应用程序中追踪单个执行轨迹成为一项具有挑战性的任务。这就是 MicroProfile OpenTracing API 的用武之地；该 API 生成跨度和轨迹，使我们能够跨 Payara Micro 实例追踪执行；每个跨度和轨迹都有一个唯一的 ID，该 ID 在实例之间不会改变，从而允许我们将一个 Payara Micro 实例的日志条目链接到另一个实例。

在使用 MicroProfile OpenTracing API 之前，需要在 Payara Micro 中启用它。



## 在 Payara Micro 中启用请求追踪

在 Payara Micro 中启用请求追踪最简单的方法是通过 `--enableRequestTracing` 命令行参数，例如：

```
java -jar payara-micro-5.2021.8.jar --enableRequestTracing --deploy /path/to/my.war
```

默认情况下，任何耗时超过 30 秒的请求都会被追踪。输出结果会以 JSON 格式发送到 Payara Micro 控制台。

如果我们希望覆盖请求在被追踪前所需的最短默认时间，可以通过 `--thresholdValue` 和 `--thresholdUnit` 命令行参数来实现。例如，要追踪任何耗时超过 100 毫秒的请求：

```
java -jar payara-micro-5.2021.8.jar --enableRequestTracing --requestTracingThresholdValue 100 --requestTracingThresholdUnit milliseconds --deploy /path/to/my.war
```

`--requestTracingThresholdUnit` 命令行参数接受以下时间单位：

*   NANOSECONDS

*   MICROSECONDS

*   MILLISECONDS

*   SECONDS

*   MINUTES

*   HOURS

*   DAYS

    `--requestTracingThresholdUnit` 的所有值均不区分大小写。

此外，`--enableRequestTracing` 接受一个可选值，用于指示请求在被追踪前所需的最短时间。例如：

```
java -jar payara-micro-5.2021.8.jar --enableRequestTracing 100ms --deploy /path/to/my.war
```

上述命令是配置请求追踪的另一种方式，使得任何耗时超过 100 毫秒的请求都会被追踪。`--enableRequestTracing` 的可选值必须是一个整数后跟一个时间单位，*两者之间不能有空格*。

有效的时间单位值可以是完整拼写的时间单位（例如 "nanoseconds"），或等效的缩写（例如 "ns"）。

表 9-1 列出了 `--enableRequestTracing` 可选参数中时间单位部分的有效值。

表 9-1

请求追踪时间单位

| 时间单位 | 完整拼写值（不区分大小写） | 缩写值 |
| --- | --- | --- |
| 纳秒 | nanoseconds | ns |
| 微秒 | microseconds | us 或 μs |
| 毫秒 | milliseconds | ms |
| 秒 | seconds | s |
| 分钟 | minutes | m |
| 小时 | hours | h |
| 天 | days | d |

所有完整拼写值也接受其单数形式（nanosecond、microsecond 等）。

## 跨度与追踪

一个*跨度*代表一个独立的工作单元，通常是一个单独的 HTTP 请求；默认情况下，Payara Micro 会以 JSON 格式将跨度记录到其控制台输出中。

一个*追踪*是一组相关跨度的集合；例如，我们可以向一个微服务发送一个 HTTP 请求，该微服务随后又向另外两个微服务发送两个请求；在这种情况下，所有三个请求都会有唯一的跨度 ID，但它们都会共享同一个追踪 ID；通过查看每个跨度的追踪 ID，我们可以找出哪些请求是相关的。追踪 ID 是唯一的，并且即使在不同的 Payara Micro 实例之间也能匹配，这使得将一个 Payara Micro 实例中的日志条目与另一个实例关联起来成为可能。

让我们看一个例子；在讨论第 6 章中的编程式应用指标时，我们开发了一个用于雇佣或解雇员工的服务；此外，我们还开发了一个客户端服务，该服务通过单个端点雇佣三名员工；作为参考，以下是实现雇佣员工逻辑的服务相关代码（为简洁起见，已移除指标相关代码）：

```
package com.ensode.requesttracing;
//imports omitted
@ApplicationScoped
@Path("employeeservice")
public class EmployeeResource {
private List employeeList = new CopyOnWriteArrayList(); //thread safe
@PUT
@Consumes(MediaType.APPLICATION_JSON)
public void hireEmployee(Employee employee) throws InterruptedException {
//simulate slow processing
Thread.sleep(100);
employeeList.add(employee);
}
}
```

为了稍微减慢处理速度并确保我们的 `hireEmployee()` 方法被追踪，我们向该方法添加了 100 毫秒的延迟。

通过 MicroProfile REST 客户端 API，客户端服务向前述服务发送了三个 HTTP PUT 请求，生成了三个相关的 HTTP 请求。

```
package com.ensode.requesttracing.client;
//imports omitted
@ApplicationScoped
@Path("employeeclient")
public class EmployeeClientResource {
@Inject
@RestClient
private EmployeeResourceClient employeeResourceClient;
private List employeesToHire;
@PostConstruct
public void init() {
Employee employee1 = new Employee("Jose", "Jimenez");
Employee employee2 = new Employee("Meera", "Patel");
Employee employee3 = new Employee("David", "Heffelfinger");
employeesToHire = List.of(employee1, employee2, employee3);
}
@Path("hire")
@POST
public void hireEmployees() {
employeesToHire.forEach(emp →
employeeResourceClient.hireEmployee(emp));
}
}
```

如果我们启用阈值为 100 毫秒的请求追踪，并向 http://localhost:8081/requesttracingclient/webresources/employeeclient/hire 发送一个 HTTP POST 请求，我们可以在两个 Payara Micro 实例（运行员工客户端服务的实例和运行员工服务的实例）的 Payara Micro 输出中看到所有生成的跨度。

在运行员工客户端服务的 Payara Micro 实例上，我们会看到类似于以下的输出：



```
[2021-11-12T13:18:40.472-0500] [] [INFO] [] [fish.payara.nucleus.notification.log.LogNotifier] [tid: _ThreadID=89 _ThreadName=log-notifier-1] [timeMillis: 1636741120472] [levelValue: 800] [[
请求执行时间：914(ms) 超过了可接受的阈值 - {"traceSpans":[
{"operationName":"processContainerRequest","spanContext":{"spanId":"e6749770-ab55-4260-887f-520997aab357","traceId":"10b36dfc-30ed-4f26-bf7f-c73d3d4bdb6f"},"startTime":"2021-11-12T13:18:39.554501-05:00[America/New_York]","endTime":"2021-11-12T13:18:40.469-05:00[America/New_York]","traceDuration":"914499000","spanTags":[{"Server": "server"},{"Domain": "domain1"}]},
{"operationName":"processWebserviceRequest","spanContext":{"spanId":"4661adac-0ae8-4a97-a303-8eae965fa8f0","traceId":"10b36dfc-30ed-4f26-bf7f-c73d3d4bdb6f"},"startTime":"2021-11-12T13:18:39.594981-05:00[America/New_York]","endTime":"2021-11-12T13:18:40.468138-05:00[America/New_York]","traceDuration":"873154000","spanTags":[{"ResponseStatus": "204"},{"host": "[localhost:8081]"},{"Method": "POST"},{"URL": "http://localhost:8081/requesttracingclient/webresources/employeeclient/hire"},{"user-agent": "[curl/7.68.0]"},{"accept": "[*/*]"}],"references":[{"spanContext":{"spanId":"e6749770-ab55-4260-887f-520997aab357","traceId":"10b36dfc-30ed-4f26-bf7f-c73d3d4bdb6f"},"relationshipType":"ChildOf"}]},
{"operationName":"POST:com.ensode.requesttracing.client.EmployeeClientResource.hireEmployees","spanContext":{"spanId":"6ae72f1e-a103-4f26-9945-a4b35bbcc49a","traceId":"10b36dfc-30ed-4f26-bf7f-c73d3d4bdb6f"},"startTime":"2021-11-12T13:18:39.643-05:00[America/New_York]","endTime":"2021-11-12T13:18:40.467588-05:00[America/New_York]","traceDuration":"824584000","spanTags":[{"http.status_code": "204"},{"component": "jaxrs"},{"span.kind": "server"},{"http.url": "http://localhost:8081/requesttracingclient/webresources/employeeclient/hire"},{"http.method": "POST"}],"references":[{"spanContext":{"spanId":"4661adac-0ae8-4a97-a303-8eae965fa8f0","traceId":"10b36dfc-30ed-4f26-bf7f-c73d3d4bdb6f"},"relationshipType":"ChildOf"}]},
{"operationName":"PUT","spanContext":{"spanId":"82ce9de2-239c-4854-aa07-ff2f7c2b388e","traceId":"10b36dfc-30ed-4f26-bf7f-c73d3d4bdb6f"},"startTime":"2021-11-12T13:18:39.887-05:00[America/New_York]","endTime":"2021-11-12T13:18:40.244796-05:00[America/New_York]","traceDuration":"357791000","spanTags":[{"http.status_code": "204"},{"component": "jaxrs"},{"span.kind": "client"},{"http.url": "http://localhost:8080/requesttracing/webresources/employeeservice"},{"http.method": "PUT"}],"references":[{"spanContext":{"spanId":"6ae72f1e-a103-4f26-9945-a4b35bbcc49a","traceId":"10b36dfc-30ed-4f26-bf7f-c73d3d4bdb6f"},"relationshipType":"ChildOf"}]},
{"operationName":"PUT","spanContext":{"spanId":"715d1e55-82de-4176-82b2-d0bd5309d315","traceId":"10b36dfc-30ed-4f26-bf7f-c73d3d4bdb6f"},"startTime":"2021-11-12T13:18:40.248-05:00[America/New_York]","endTime":"2021-11-12T13:18:40.357046-05:00[America/New_York]","traceDuration":"109037000","spanTags":[{"http.status_code": "204"},{"component": "jaxrs"},{"span.kind": "client"},{"http.url": "http://localhost:8080/requesttracing/webresources/employeeservice"},{"http.method": "PUT"}],"references":[{"spanContext":{"spanId":"6ae72f1e-a103-4f26-9945-a4b35bbcc49a","traceId":"10b36dfc-30ed-4f26-bf7f-c73d3d4bdb6f"},"relationshipType":"ChildOf"}]},
{"operationName":"PUT","spanContext":{"spanId":"f026af13-61cc-4767-bfc6-c123eb142ac5","traceId":"10b36dfc-30ed-4f26-bf7f-c73d3d4bdb6f"},"startTime":"2021-11-12T13:18:40.357-05:00[America/New_York]","endTime":"2021-11-12T13:18:40.463478 -05:00[America/New_York]","traceDuration":"106473000","spanTags":[{"http.status_code": "204"},{"component": "jaxrs"},{"span.kind": "client"},{"http.url": "http://localhost:8080/requesttracing/webresources/employeeservice"},{"http.method": "PUT"}],"references":[{"spanContext":{"spanId":"6ae72f1e-a103-4f26-9945-a4b35bbcc49a","traceId":"10b36dfc-30ed-4f26-bf7f-c73d3d4bdb6f"},"relationshipType":"ChildOf"}]}
]}]]
```

这是一个包含六个 span 的 JSON 格式追踪信息；每个 span 都包含有用的数据，例如操作名称、span ID、trace ID、开始和结束时间以及追踪持续时间。

输出中的前两个 span，其操作名称为“processContainerRequest”和“processWebserviceRequest”，是 Payara Micro 的内部调用，会被添加到追踪中。

请注意，在输出中，每个单独的 span 都有一个唯一的 ID；然而，每个 span 中的 trace ID 是相同的；这个 trace ID 可以用来将单个请求链接到单个执行追踪。

如果我们检查 Payara Micro 实例的输出，可以看到类似于以下内容的输出：

```
[[2021-11-12T13:18:40.234-0500] [] [INFO] [] [fish.payara.nucleus.notification.log.LogNotifier] [tid: _ThreadID=85 _ThreadName=log-notifier-1] [timeMillis: 1636741120234] [levelValue: 800] [[
请求执行时间：293(ms) 超过了可接受的阈值 - {"traceSpans":[
{"operationName":"processContainerRequest","spanContext":{"spanId":"d07f7f93-17f5-4372-a64f-4501d8c63a62","traceId":"10b36dfc-30ed-4f26-bf7f-c73d3d4bdb6f"},"startTime":"2021-11-12T13:18:39.936441-05:00[America/New_York]","endTime":"2021-11-12T13:18:40.230-05:00[America/New_York]","traceDuration":"293559000","spanTags":[{"Server": "server"},{"Domain": "domain1"}],"references":[{"spanContext":{"spanId":"e6749770-ab55-4260-887f-520997aab357","traceId":"10b36dfc-30ed-4f26-bf7f-c73d3d4bdb6f"},"relationshipType":"ChildOf"}]},
{"operationName":"processWebserviceRequest","spanContext":{"spanId":"bc56a4e5-3347-42d5-895f-99c2c94cc4cf","traceId":"10b36dfc-30ed-4f26-bf7f-c73d3d4bdb6f"},"startTime":"2021-11-12T13:18:39.983108-05:00[America/New_York]","endTime":"2021-11-12T13:18:40.229562-05:00[America/New_York]","traceDuration":"246449000","spanTags":[{"traceid": "[a1155558-7068-4c7e-a8b8-2175ba104233]"},{"content-length": "[41]"},{"Method": "PUT"},{"URL": "http://localhost:8080/requesttracing/webresources/employeeservice"},{"payara-tracing-traceid": "[10b36dfc-30ed-4f26-bf7f-c73d3d4bdb6f]"},{"accept": "[application/json]"},{"payara-tracing-relationshiptype": "[ChildOf]"},{"spanid": "[82ce9de2-239c-4854-aa07-ff2f7c2b388e]"},{"ResponseStatus": "204"},{"payara-tracing-parentid": "[e6749770-ab55-4260-887f-520997aab357]"},{"host": "[localhost:8080]"},{"content-type": "[application/json]"},{"connection": "[keep-alive]"},{"user-agent": "[Jersey/2.34.payara-p1 (HttpUrlConnection 11.0.12)]"}],"references":[{"spanContext":{"spanId":"d07f7f93-17f5-4372-a64f-4501d8c63a62","traceId":"10b36dfc-30ed-4f26-bf7f-c73d3d4bdb6f"},"relationshipType":"ChildOf"}]},
{"operationName":"PUT:com.ensode.requesttracing.EmployeeResource.hireEmployee","spanContext":{"spanId":"19090bd3-6102-40fe-8cf2-b8831a572129","traceId":"10b36dfc-30ed-4f26-bf7f-c73d3d4bdb6f"},"startTime":"2021-11-12T13:18:40.023-05:00[America/New_York]","endTime":"2021-11-12T13:18:40.228787-05:00[America/New_York]","traceDuration":"205780000","spanTags":[{"http.status_code": "204"},{"component": "jaxrs"},{"span.kind": "server"},{"http.url": "http://localhost:8080/requesttracing/webresources/employeeservice"},{"http.method": "PUT"}],"references":[{"spanContext":{"spanId":"82ce9de2-239c-4854-aa07-ff2f7c2b388e","traceId":"a1155558-7068-4c7e-a8b8-2175ba104233"},"relationshipType":"ChildOf"},{"spanContext":{"spanId":"bc56a4e5-3347-42d5-895f-99c2c94cc4cf","traceId":"10b36dfc-30ed-4f26-bf7f-c73d3d4bdb6f"},"relationshipType":"ChildOf"}]}
]}]]
```

在这种情况下，我们只看到单个 HTTP PUT 请求被追踪；之所以没有看到所有三个 HTTP PUT 请求，是因为我们在启动此 Payara Micro 实例时为请求追踪指定了 100 毫秒的阈值；另外两个 PUT 请求耗时少于 100 毫秒，因此没有被追踪。请注意，此处的 trace ID 与运行客户端服务的实例上相应 trace ID 的值匹配，这使我们能够关联这些调用。



## 自定义请求追踪

如上一节所述，只需启用请求追踪并指定阈值，我们就能获得大量功能，这足以满足大多数情况。然而，如果默认功能不符合我们的需求，我们可以自定义请求追踪。

MicroProfile OpenTracing API 包含一个 `@Traced` 注解，它允许我们追踪 Web 服务端点之外的方法；它还允许我们禁用任何方法的追踪，并自定义操作名称。

我们还可以通过 Tracer 注解进一步自定义请求追踪输出，该注解可用于向跨度添加额外信息，以及创建不绑定到方法的额外跨度。

### 追踪额外方法

我们可以通过使用 `@Traced` 注解来装饰 CDI Bean 上的任何方法，从而对其进行追踪。

在类级别使用 `@Traced` 注解 CDI Bean 将导致该 Bean 中的所有方法都被追踪。

例如，假设我们的 `EmployeeResource` 服务在每次雇佣员工时都会触发一个 CDI 事件；我们可以通过使用 `@Traced` 注解来追踪观察该事件的方法。

```
package com.ensode.requesttracing;
// 导入已省略
@ApplicationScoped
public class EmployeeEventHandler {
private static final Logger LOGGER =
Logger.getLogger(EmployeeEventHandler.class.getName());
@Traced
public void handleEmployeeEvent(@Observes Employee employee)
LOGGER.log(Level.INFO,
String.format("Hired %s %s",
employee.getFirstName(), employee.getLastName()));
}
}
```

*@Traced* 注解会向生成的追踪中添加一个新的跨度。

```
[2021-11-12T15:15:51.189-0500] [] [INFO] [] [fish.payara.nucleus.notification.log.LogNotifier] [tid: _ThreadID=84 _ThreadName=log-notifier-1] [timeMillis: 1636748151189] [levelValue: 800] [[
Request execution time: 208(ms) exceeded the acceptable threshold - {"traceSpans":[
{"operationName":"processContainerRequest","spanContext":{"spanId":"a4cdd77f-edfd-4e88-8d23-ffe37f2bd8d0","traceId":"7ebf04e9-1a62-494e-867c-cd80cefa31d9"},"startTime":"2021-11-12T15:15:50.977342-05:00[America/New_York]","endTime":"2021-11-12T15:15:51.186-05:00[America/New_York]","traceDuration":"208658000","spanTags":[{"Server": "server"},{"Domain": "domain1"}],"references":[{"spanContext":{"spanId":"1d842a34-6b2b-43f2-a9b0-1dcb4c1b8629","traceId":"7ebf04e9-1a62-494e-867c-cd80cefa31d9"},"relationshipType":"ChildOf"}]},
{"operationName":"processWebserviceRequest","spanContext":{"spanId":"25d4e22e-eac2-4425-a274-f8ab703610ff","traceId":"7ebf04e9-1a62-494e-867c-cd80cefa31d9"},"startTime":"2021-11-12T15:15:50.977893-05:00[America/New_York]","endTime":"2021-11-12T15:15:51.186627-05:00[America/New_York]","traceDuration":"208733000","spanTags":[{"traceid": "[b5382f8d-de60-4aa8-9a24-39302747183b]"},{"content-length": "[47]"},{"Method": "PUT"},{"URL": "http://localhost:8080/requesttracing/webresources/employeeservice"},{"payara-tracing-traceid": "[7ebf04e9-1a62-494e-867c-cd80cefa31d9]"},{"accept": "[application/json]"},{"payara-tracing-relationshiptype": "[ChildOf]"},{"spanid": "[afdbae15-a93f-49c1-bcec-5e128a47af0d]"},{"ResponseStatus": "204"},{"payara-tracing-parentid": "[1d842a34-6b2b-43f2-a9b0-1dcb4c1b8629]"},{"host": "[localhost:8080]"},{"content-type": "[application/json]"},{"connection": "[keep-alive]"},{"user-agent": "[Jersey/2.34.payara-p1 (HttpUrlConnection 11.0.12)]"}]},
{"operationName":"PUT:com.ensode.requesttracing.EmployeeResource.hireEmployee","spanContext":{"spanId":"c909a51d-49dc-4882-8a20-ba399d23db01","traceId":"7ebf04e9-1a62-494e-867c-cd80cefa31d9"},"startTime":"2021-11-12T15:15:50.981-05:00[America/New_York]","endTime":"2021-11-12T15:15:51.186518-05:00[America/New_York]","traceDuration":"205514000","spanTags":[{"http.status_code": "204"},{"component": "jaxrs"},{"span.kind": "server"},{"http.url": "http://localhost:8080/requesttracing/webresources/employeeservice"},{"employee": "David Heffelfinger"},{"http.method": "PUT"}],"references":[{"spanContext":{"spanId":"afdbae15-a93f-49c1-bcec-5e128a47af0d","traceId":"b5382f8d-de60-4aa8-9a24-39302747183b"},"relationshipType":"ChildOf"},{"spanContext":{"spanId":"25d4e22e-eac2-4425-a274-f8ab703610ff","traceId":"7ebf04e9-1a62-494e-867c-cd80cefa31d9"},"relationshipType":"ChildOf"}]},
{"operationName":"com.ensode.requesttracing.EmployeeEventHandler.handleEmployeeEvent","spanContext":{"spanId":"3142e614-7203-4b5e-9c94-06979c031f89","traceId":"7ebf04e9-1a62-494e-867c-cd80cefa31d9"},"startTime":"2021-11-12T15:15:51.085-05:00[America/New_York]","endTime":"2021-11-12T15:15:51.185988-05:00[America/New_York]","traceDuration":"100983000","references":[{"spanContext":{"spanId":"c909a51d-49dc-4882-8a20-ba399d23db01","traceId":"7ebf04e9-1a62-494e-867c-cd80cefa31d9"},"relationshipType":"ChildOf"}]}
]}]]
```

### 禁用请求追踪

我们可以通过简单地使用 `@Traced(false)` 注解来禁用任何 Web 服务端点的追踪，例如，在 `EmployeeResource` 的 `hireEmployee()` 方法中：

```
@Traced(false)
@PUT
@Consumes(MediaType.APPLICATION_JSON)
public void hireEmployee(Employee employee)
throws InterruptedException {
//模拟慢速处理
Thread.sleep(100);
employeeList.add(employee);
employeeEvent.fire(employee);
}
```

### 自定义操作名称

默认情况下，跨度中生成的操作名称是被调用方法的完全限定名（如果该方法是作为 HTTP 请求的直接结果被调用的，则方法名称前会加上 HTTP 请求类型（PUT、GET、POST 等））。

如果我们希望自定义跨度上显示的操作名称，可以通过设置 `@Traced` 注解上的 `operationName` 属性来实现；例如，在 `EmployeeResource` 的 `hireEmployee()` 方法中，我们可以这样做：

```
@Traced(operationName = "employeeHire")
@PUT
@Consumes(MediaType.APPLICATION_JSON)
public void hireEmployee(Employee employee)
throws InterruptedException {
//模拟慢速处理
Thread.sleep(100);
employeeList.add(employee);
employeeEvent.fire(employee);
}
```

这将导致 Payara Micro 控制台中输出以下内容（为简洁起见，仅显示相关跨度）：

```
{"operationName":"employeeHire","spanContext":{"spanId":"7647b911-4999-48ad-9c7b-a4eb87629a89","traceId":"1d50764c-848c-4f59-aa7e-902df1813fd8"},"startTime":"2021-11-12T15:31:21.145-05:00[America/New_York]","endTime":"2021-11-12T15:31:21.352779-05:00[America/New_York]","traceDuration":"207775000","spanTags":[{"http.status_code": "204"},{"component": "jaxrs"},{"span.kind": "server"},{"http.url": "http://localhost:8080/requesttracing/webresources/employeeservice"},{"http.method": "PUT"}],"references":[{"spanContext":{"spanId":"c8fbdf31-744a-455a-80c3-2488a8c5c1e9","traceId":"b45f2bd3-762f-42e5-9950-9553c05b67f8"},"relationshipType":"ChildOf"},{"spanContext":{"spanId":"46a5c1c2-4ae0-4395-ad6d-c2bc7fe03808","traceId":"1d50764c-848c-4f59-aa7e-902df1813fd8"},"relationshipType":"ChildOf"}]}
```



### 为 Span 添加额外信息

另一种自定义 span 的方式是向生成的 JSON 字符串中添加自定义标签。为此，我们需要为 OpenTracing API 添加一个额外的依赖项；使用 Maven 时，可以通过在 *pom.xml* 文件中添加一个 `<dependency>` 标签来实现，如下例所示（为简洁起见，仅显示 *pom.xml* 的相关部分）：

```

4.0.0
com.ensode
request-tracing
1.0-SNAPSHOT
war
request-tracing

fish.payara.api
payara-bom
${version.payara}
pom
import

io.opentracing
opentracing-api

```

大多数部署到 Payara Micro 的应用程序的 Maven 项目，都应在其 `<dependencyManagement>` 部分包含 *payara-bom* 依赖项。

Maven 物料清单（BOM）为我们的项目指定了正确的依赖版本。通过使用 BOM，我们可以在不指定版本的情况下向项目添加依赖项，而能够与其它依赖项协同工作的正确版本已在 BOM 中声明。

*payara-bom* 在其 `<dependencyManagement>` 部分包含了 `opentracing-api` 的依赖项；因此，在向项目添加此依赖项时，我们无需指定版本，如前面的 *pom.xml* 文件所示。

添加必要的依赖项后，我们需要将 `io.opentracing.Tracer` 的实例注入到代码中，并使用它为我们的 span 添加自定义标签。

```
package com.ensode.requesttracing;
import io.opentracing.Tracer;
// 其他导入已省略
@ApplicationScoped
@Path("employeeservice")
public class EmployeeResource {
@Inject
private Tracer tracer;
private List employeeList = new CopyOnWriteArrayList(); // 线程安全
@PUT
@Consumes(MediaType.APPLICATION_JSON)
public void hireEmployee(Employee employee) throws InterruptedException {
// 模拟慢速处理
Thread.sleep(100);
tracer.activeSpan().setTag(
"employee", String.format("%s %s", employee.getFirstName(),
employee.getLastName()));
employeeList.add(employee);
}
}
```

如示例所示，我们通过调用 `Tracer.activeSpan()` 获取代表当前 span 的 `io.opentracing.Span` 实例；然后，我们调用 `setTag()` 方法，该方法有两个参数：一个用于标签名称的 `String`，以及另一个用于值的参数。`setTag()` 有三个重载版本；第二个参数可以是 `String`、`boolean` 或 `Number`（任何数值）；在我们的示例中，我们使用了两个 `String` 参数的版本，因为我们添加的标签包含被雇佣员工的名和姓。

我们可以像往常一样调用 RESTful Web 服务来查看自定义标签；自定义标签将显示在 span 的 JSON 字符串中。

```
{"operationName":"PUT:com.ensode.requesttracing.EmployeeResource.hireEmployee","spanContext":{"spanId":"db583e67-b160-4acd-8e14-1543381848b1","traceId":"3410bb5f-016c-45c4-a0ef-fd67124af853"},"startTime":"2021-11-15T11:33:54.479-05:00[America/New_York]","endTime":"2021-11-15T11:33:54.680121-05:00[America/New_York]","traceDuration":"201116000","spanTags":[{"http.status_code": "204"},{"component": "jaxrs"},{"span.kind": "server"},{"http.url": "http://localhost:8080/requesttracing/webresources/employeeservice"},{"employee": "Jose Jimenez"},{"http.method": "PUT"}],"references":[{"spanContext":{"spanId":"b7b53629-7890-43d1-9dc4-1c736d21f1cc","traceId":"d5d2cca0-b1b4-4688-8c06-f545c256a1be"},"relationshipType":"ChildOf"},{"spanContext":{"spanId":"42a56b8d-183a-49ef-bc0b-0c322a81930f","traceId":"3410bb5f-016c-45c4-a0ef-fd67124af853"},"relationshipType":"ChildOf"}]}
```

### 创建额外的 Span

我们可以通过调用 `Tracer.buildSpan()` 向当前追踪添加自定义 span，如下例所示：

```
package com.ensode.requesttracing;
// 导入已省略
@ApplicationScoped
@Path("employeeservice")
public class EmployeeResource
@Inject
private Tracer tracer;
private List employeeList = new CopyOnWriteArrayList(); // 线程安全
@PUT
@Consumes(MediaType.APPLICATION_JSON)
public void hireEmployee(Employee employee) throws InterruptedException {
Span customSpan;
Tracer.SpanBuilder spanBuilder =
tracer.buildSpan("customSpan");
customSpan = spanBuilder.start();
// 模拟慢速处理
Thread.sleep(215);
customSpan.finish();
employeeList.add(employee);
}
}
```

`Tracer.buildSpan()` 创建了一个 `SpanBuilder` 类的实例，该类是 `Tracer` 的内部类；此方法接受一个参数，即一个 `String`，用于指示我们自定义 span 的操作名称。

当我们实际想要启动自定义 span 时，需要在调用 `buildSpan()` 时创建的 `SpanBuilder` 实例上调用 `start()` 方法；要停止自定义 span，则调用其 `finish()` 方法；当我们执行包含自定义标签的端点时，将能够在 Payara Micro 控制台输出中看到它。



```
[2021-11-15T18:28:23.429-0500] [] [INFO] [] [fish.payara.nucleus.notification.log.LogNotifier] [tid: _ThreadID=90 _ThreadName=log-notifier-1] [timeMillis: 1637018903429] [levelValue: 800] [[
请求执行时间：228(ms) 已超过可接受阈值 - {"traceSpans":[
{"operationName":"processContainerRequest","spanContext":{"spanId":"c4d7a6ab-eb74-4bf1-9b72-db0408cbf782","traceId":"0e282189-a825-466e-8488-b2fe569f7a0f"},"startTime":"2021-11-15T18:28:23.199057-05:00[America/New_York]","endTime":"2021-11-15T18:28:23.428-05:00[America/New_York]","traceDuration":"228943000","spanTags":[{"Server": "server"},{"Domain": "domain1"}],"references":[{"spanContext":{"spanId":"0e608033-901d-48c5-8d13-839507128e23","traceId":"0e282189-a825-466e-8488-b2fe569f7a0f"},"relationshipType":"ChildOf"}]},
{"operationName":"processWebserviceRequest","spanContext":{"spanId":"17011bca-7418-42e6-8513-4a586752fb10","traceId":"0e282189-a825-466e-8488-b2fe569f7a0f"},"startTime":"2021-11-15T18:28:23.200232-05:00[America/New_York]","endTime":"2021-11-15T18:28:23.428209-05:00[America/New_York]","traceDuration":"227974000","spanTags":[{"traceid": "[2f61f32a-c96d-4521-8a82-64e97799f626]"},{"content-length": "[40]"},{"Method": "PUT"},{"URL": "http://localhost:8080/requesttracing/webresources/employeeservice"},{"payara-tracing-traceid": "[0e282189-a825-466e-8488-b2fe569f7a0f]"},{"accept": "[application/json]"},{"payara-tracing-relationshiptype": "[ChildOf]"},{"spanid": "[922671a9-9eae-4c97-ae30-36c71b4f13cb]"},{"ResponseStatus": "204"},{"payara-tracing-parentid": "[0e608033-901d-48c5-8d13-839507128e23]"},{"host": "[localhost:8080]"},{"content-type": "[application/json]"},{"connection": "[keep-alive]"},{"user-agent": "[Jersey/2.34.payara-p1 (HttpUrlConnection 11.0.12)]"}]},
{"operationName":"customSpan","spanContext":{"spanId":"7b3f24a2-1aa3-4b51-af46-019cdd905b3a","traceId":"0e282189-a825-466e-8488-b2fe569f7a0f"},"startTime":"2021-11-15T18:28:23.206-05:00[America/New_York]","endTime":"2021-11-15T18:28:23.422066-05:00[America/New_York]","traceDuration":"216057000","references":[{"spanContext":{"spanId":"81313aaa-d744-459c-99c5-aa00e8d679c9","traceId":"0e282189-a825-466e-8488-b2fe569f7a0f"},"relationshipType":"ChildOf"},{"spanContext":{"spanId":"17011bca-7418-42e6-8513-4a586752fb10","traceId":"0e282189-a825-466e-8488-b2fe569f7a0f"},"relationshipType":"ChildOf"}]},
{"operationName":"PUT:com.ensode.requesttracing.EmployeeResource.hireEmployee","spanContext":{"spanId":"81313aaa-d744-459c-99c5-aa00e8d679c9","traceId":"0e282189-a825-466e-8488-b2fe569f7a0f"},"startTime":"2021-11-15T18:28:23.206-05:00[America/New_York]","endTime":"2021-11-15T18:28:23.428083-05:00[America/New_York]","traceDuration":"222078000","spanTags":[{"http.status_code": "204"},{"component": "jaxrs"},{"span.kind": "server"},{"http.url": "http://localhost:8080/requesttracing/webresources/employeeservice"},{"http.method": "PUT"}],"references":[{"spanContext":{"spanId":"922671a9-9eae-4c97-ae30-36c71b4f13cb","traceId":"2f61f32a-c96d-4521-8a82-64e97799f626"},"relationshipType":"ChildOf"},{"spanContext":{"spanId":"17011bca-7418-42e6-8513-4a586752fb10","traceId":"0e282189-a825-466e-8488-b2fe569f7a0f"},"relationshipType":"ChildOf"}]}]]
```

我们可以通过调用 `setTag()` 为自定义跨度添加自定义标签；在上面的示例中，我们可以添加以下代码行：

```
customSpan.setTag("foo", "bar");
```

为自定义跨度添加一个标签为 *foo*、值为 *bar* 的自定义标签。

## 总结

在本章中，我们介绍了 Payara Micro 的请求追踪功能。我们了解了如何通过命令行参数为 Payara Micro 启用请求追踪，这会导致应用程序中所有 RESTful Web 服务端点被自动追踪。我们还介绍了如何通过 `@Traced` 注解追踪应用程序中的其他方法，以及如何在不需要的地方禁用请求追踪。此外，我们还介绍了如何自定义应用程序生成的跨度中的操作名称，以及如何为跨度添加自定义标签。最后，我们介绍了在需要比 MicroProfile OpenTracing API 提供的更多控制时，如何以编程方式创建跨度。

