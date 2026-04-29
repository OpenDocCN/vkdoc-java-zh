# 3. 开发微服务客户端

在典型的微服务架构中，应用程序由许多独立开发和部署的微服务组成。这些微服务需要通过相互通信。到目前为止，实现此功能最常见的方式是将微服务开发为 RESTful Web 服务，其中一些微服务充当其他微服务的 RESTful Web 服务客户端。

Payara Micro 支持 MicroProfile REST 客户端 API，这极大地简化了 RESTful Web 服务客户端的开发。



## MicroProfile REST 客户端 API 概述

MicroProfile REST 客户端 API 让开发 RESTful Web 服务客户端变得非常简单，尤其是在我们为使用 Jakarta RESTful Web 服务（详见第 2 章）开发的 RESTful Web 服务开发客户端时。

本质上，一个 MicroProfile RESTful Web 服务客户端由一个单独的 Java 接口组成，该接口的方法签名与对应的 RESTful Web 服务方法签名相匹配（即相同的方法名和参数）。

在类级别上，MicroProfile REST 客户端接口使用 `@Path` 注解；其值参数必须与服务上对应的 `@Path` 注解相匹配。

MicroProfile REST 客户端接口上的每个抽象方法必须具有与服务器上对应方法相同的注解（`@Path`、`@PUT`、`@Consumes`、`@Produces` 等）。

在实践中，开发 MicroProfile REST 客户端最简单的方法是复制服务的源代码，将类改为接口，并删除所有方法体。此时，我们“开发”RESTful Web 客户端的工作已经完成了 95%。

一旦我们有了与服务匹配的接口，就需要在接口级别添加 `@RegisterRestClient` 注解；该注解表明该接口是一个 RESTful Web 服务客户端。

一个典型的 RESTful Web 服务客户端接口如下所示：

```
package com.ensode.jsonparsingclient;
//导入省略
@RegisterRestClient
@Path("messageprocessor")
public interface MessageProcessorClient {
@PUT
@Consumes(MediaType.APPLICATION_JSON)
@Produces(MediaType.APPLICATION_JSON)
public Message processMessage(Message message);
}
```

这个示例是上一章“解析 JSON 数据”部分中我们开发的服务对应的 RESTful Web 服务客户端。请注意，所有注解都与服务源代码中的对应注解相匹配。

请注意，我们不会（也不应该）为 RESTful Web 服务客户端接口编写实现；实现是在运行时由 Payara Micro（或任何符合 MicroProfile 规范的运行时）生成的。如果我们不为 RESTful Web 服务客户端编写实现，那么如何指定我们正在交互的服务的 URI 呢？答案在于 MicroProfile Config API。

我们将在本书后面详细介绍 MicroProfile API；现在，我们只介绍指定我们正在交互的服务 URI 所必需的内容。

为了指定我们服务的 URI，我们需要创建一个名为 *microprofile-config.properties* 的文件，并将其放在 WAR 文件根目录下的 META-INF 目录中。如果使用 Maven，则必须将此文件放在 *src/resources/META-INF* 下；构建时，Maven 会将其放置在 WAR 文件中的正确位置。

在我们的应用程序中，每个 RESTful Web 服务客户端需要一个属性；属性名称必须遵循以下格式：

*<RESTful 客户端接口的完全限定名>/mp-rest/url*

在我们的示例中，属性名称将是：

*com.ensode.jsonparsingclient.MessageProcessorClient/mp-rest/url*

该属性的值必须与服务的 URI 匹配，包括在 Application 类中定义的根 URL；在我们的示例中，服务的 URI 是：

*http://localhost:8080/webresources*

因此，microprofile-config 属性文件中的名称/值对如下所示：

```
com.ensode.jsonparsingclient.MessageProcessorClient/mp-rest/url=http://localhost:8080/webresources
```

本节描述了使用 MicroProfile REST 客户端 API 开发 RESTful Web 服务客户端所需遵循的一般模式。接下来，我们将介绍每种 HTTP 请求类型、路径和查询参数，以及在请求正文中向服务传递数据的具体细节。

要使用我们的 REST 客户端接口，我们需要通过 CDI 的 `@Inject` 注解以及 MicroProfile REST 客户端 API 提供的 `@RestClient` 注解，将其注入到另一个工件（通常是针对 Jakarta RESTful Web 服务 API 开发的 RESTful Web 服务）中。

```
@ApplicationScoped
public class MessageProcessorRestClient {
private static final Logger LOGGER = Logger.getLogger(MessageProcessorRestClient.class.getName());
@Inject
@RestClient
private MessageProcessorClient messageProcessorClient;
public void init(@Observes @Initialized(ApplicationScoped.class)
Object object) {
Message message = new Message();
Message returnedMessage;
message.setMsgText(
"来自 MicroProfile REST 客户端的问候！");
returnedMessage =
messageProcessorClient.processMessage(message);
LOGGER.log(Level.INFO, String.format(
"服务器返回了以下消息：%s",
returnedMessage.getMsgText()));
}
}
```

在这个示例中，我们使用了一个 Jakarta 上下文和依赖注入（CDI）技巧，在应用程序初始化时执行代码。

请注意 `init()` 方法的对象参数上的 `@Observes @Initialized(ApplicationScoped.class)` 注解；这些注解会导致运行时在 CDI 应用程序作用域初始化时执行 `init()` 方法，而这恰好与应用程序完全部署的时间点一致。这个技巧允许我们在应用程序初始化时运行任意 Java 代码，结合从命令行启动 MicroProfile 应用程序的能力，这个技巧使我们能够为 RESTful Web 服务编写简单的命令行测试客户端。

CDI 将在本书后面详细介绍。

我们刚刚描述了使用 MicroProfile REST 客户端 API 开发 REST 客户端应用程序的一般模式；在接下来的章节中，我们将深入探讨一些具体细节。



### 生成 HTTP GET 请求

为了处理 HTTP GET 请求，我们的 REST 客户端接口需要使用 `@Path` 和 `@RegisterRestClient` 注解进行标注；`@Path` 注解的值需要与我们要调用的服务的值相匹配。HTTP GET 请求由 `@GET` 注解处理；如果我们调用的 RESTful Web 服务方法返回响应，它通常是一个 JSON 字符串；因此，我们应该添加带有适当值的 `@Produces` 注解；使注解和值与我们要调用的服务相匹配，通常会产生预期的结果。

以下代码片段展示了一个典型的 RESTful Web 服务客户端接口，该接口向 RESTful Web 服务发送 HTTP GET 请求：

```
@Path("sample")
@RegisterRestClient
public interface SampleRestfulServiceClient {
@GET
@Produces(value = MediaType.APPLICATION_JSON)
String processGetRequest();
}
```

我们通过 *microprofile-config.properties* 文件指定要调用的 RESTful Web 服务的 URI。

```
com.ensode.restclient.SampleRestfulServiceClient/mp-rest/url=http://localhost:8080/webresources
```

回想一下，根 URI 必须与 RESTful Web 服务项目中 `Application` 类中定义的值相匹配。

然后，我们可以通过 CDI 注入我们的接口，以便调用该方法并生成 GET 请求。我们通过 `@Inject` 和 `@RestClient` 注解注入该接口。我们的接口实现会被自动生成并注入；然后我们只需调用带有 `@GET` 注解的方法来生成 GET 请求，并可选地将响应存储在一个变量中。

以下示例说明了此过程：

```
package com.ensode.restclient;
//imports omitted
@ApplicationScoped
public class RestfulClient {
private static final Logger LOGGER =
Logger.getLogger(RestfulClient.class.getName());
@Inject
@RestClient
private SampleRestfulServiceClient sampleRestfulServiceClient;
public void init(
@Observes @Initialized(ApplicationScoped.class)
Object object) {
String returnedGetData;
LOGGER.log(Level.INFO, "--- Sending HTTP GET request ");
returnedGetData =
sampleRestfulServiceClient.processGetRequest();
LOGGER.log(Level.INFO, String.format(
"--- GET request response: %s", returnedGetData));
}
}
```

我们可以通过在命令行中调用 `war:exploded` 和 `payara-micro:start` Maven 目标，在 Payara Micro 中运行我们的客户端应用程序。

```
mvn war:exploded payara-micro:start
```

我们应该在控制台的 Payara Micro 输出中看到日志语句的预期输出：

```
[2021-08-20T14:58:59.463-0400] [] [INFO] [] [com.ensode.restclient.RestfulClient] [tid: _ThreadID=1 _ThreadName=main] [timeMillis: 1629485939463] [levelValue: 800] --- GET request response: {"msg":"Service processed HTTP GET request!"}
```

这是我们正在调用的服务的预期输出，我们在第 2 章中开发了该服务。

### 生成 HTTP POST 请求

为了处理 HTTP POST 请求，我们的 REST 客户端接口需要像往常一样使用 `@Path` 和 `@RegisterRestClient` 注解进行标注；`@Path` 注解的值同样需要与我们要调用的服务的值相匹配。HTTP POST 请求由 `@POST` 注解处理；如果我们调用的 RESTful Web 服务方法返回响应，它通常是一个 JSON 字符串；因此，我们应该添加带有适当值的 `@Produces` 注解。

以下代码片段展示了一个典型的 RESTful Web 服务客户端接口，该接口向 RESTful Web 服务发送 HTTP POST 请求：

```
@Path("sample")
@RegisterRestClient
public interface SampleRestfulServiceClient {
@POST
@Produces(value = MediaType.APPLICATION_JSON)
String processPostRequest();
}
```

与其他 HTTP 请求类型的情况一样，我们通过 *microprofile-config.properties* 文件指定要调用的 RESTful Web 服务的 URI。

```
com.ensode.restclient.SampleRestfulServiceClient/mp-rest/url=http://localhost:8080/webresources
```

我们需要确保服务的根 URI 与 RESTful Web 服务项目中 `Application` 类中定义的值相匹配。

然后，我们使用 CDI 的 `@Inject` 和 `@RestClient` 注解来标注该接口。最后，我们只需调用带有 `@POST` 注解的方法来生成 POST 请求，并可选地将响应存储在一个变量中。

```
package com.ensode.restclient;
//imports omitted
@ApplicationScoped
public class RestfulClient {
private static final Logger LOGGER =
Logger.getLogger(RestfulClient.class.getName());
@Inject
@RestClient
private SampleRestfulServiceClient sampleRestfulServiceClient;
public void init(
@Observes @Initialized(ApplicationScoped.class)
Object object) {
String returnedPostData;
LOGGER.log(Level.INFO, "--- Sending HTTP POST request ");
returnedPostData =
sampleRestfulServiceClient.processPostRequest();
LOGGER.log(Level.INFO, String.format(
"--- POST request response: %s", returnedPostData));
}
}
```

当我们通过常用的 Maven 目标运行客户端应用程序时，应该会看到服务的预期输出。

```
[2021-08-20T14:58:59.463-0400] [] [INFO] [] [com.ensode.restclient.RestfulClient] [tid: _ThreadID=1 _ThreadName=main] [timeMillis: 1629485939463] [levelValue: 800] --- POST request response: {"msg":"Service processed HTTP POST request!"}
```

### 生成 HTTP PUT 请求

对于 HTTP PUT 请求，我们的 REST 客户端接口需要像其他 HTTP 请求类型一样使用 `@Path` 和 `@RegisterRestClient` 注解进行标注；不出所料，`@Path` 注解的值需要与我们要调用的服务的值相匹配。HTTP PUT 请求由 `@PUT` 注解处理；如果我们调用的 RESTful Web 服务方法返回响应，它通常是一个 JSON 字符串；因此，我们应该像往常一样添加带有适当值的 `@Produces` 注解。

以下代码片段展示了一个典型的 RESTful Web 服务客户端接口，该接口向 RESTful Web 服务发送 HTTP PUT 请求：

```
@Path("sample")
@RegisterRestClient
public interface SampleRestfulServiceClient {
@PUT
@Produces(value = MediaType.APPLICATION_JSON)
String processPutRequest();
}
```

像往常一样，我们通过 *microprofile-config.properties* 文件指定要调用的 RESTful Web 服务的 URI。

```
com.ensode.restclient.SampleRestfulServiceClient/mp-rest/url=http://localhost:8080/webresources
```

我们需要确保服务的根 URI 与 RESTful Web 服务项目中 `Application` 类中定义的值相匹配。

然后，我们像往常一样通过 CDI 的 `@Inject` 和 `@RestClient` 注解注入我们的接口；然后我们只需调用带有 `@PUT` 注解的方法来生成 PUT 请求，并可选地将响应存储在一个变量中。

```
package com.ensode.restclient;
//imports omitted
@ApplicationScoped
public class RestfulClient {
private static final Logger LOGGER =
Logger.getLogger(RestfulClient.class.getName());
@Inject
@RestClient
private SampleRestfulServiceClient sampleRestfulServiceClient;
public void init(
@Observes @Initialized(ApplicationScoped.class)
Object object) {
String returnedPutData;
LOGGER.log(Level.INFO, "--- Sending HTTP PUT request ");
returnedPutData =
sampleRestfulServiceClient.processPutRequest();
LOGGER.log(Level.INFO, String.format(
"--- PUT request response: %s", returnedPutData));
}
}
```

在 Payara Micro 中运行我们的客户端应用程序后，我们可以在 Payara Micro 的命令行输出中看到服务发送的响应正文。

```
[2021-08-20T14:58:59.463-0400] [] [INFO] [] [com.ensode.restclient.RestfulClient] [tid: _ThreadID=1 _ThreadName=main] [timeMillis: 1629485939463] [levelValue: 800] --- PUT request response: {"msg":"Service processed HTTP PUT request!"}
```



### 生成 HTTP DELETE 请求

在生成 HTTP DELETE 请求时，我们的 REST 客户端接口自然需要使用 `@Path` 和 `@RegisterRestClient` 注解进行标注；`@Path` 注解的值需要与我们正在调用的服务上对应注解的值相匹配。HTTP DELETE 请求由 `@DELETE` 注解处理；如果我们调用的 RESTful Web 服务方法返回响应，该响应通常是一个 JSON 字符串；因此，我们应该像往常一样添加 `@Produces` 注解，并设置合适的值。

以下代码片段展示了一个典型的 RESTful Web 服务客户端接口，该接口向 RESTful Web 服务发送 HTTP DELETE 请求：

```
@Path("sample")
@RegisterRestClient
public interface SampleRestfulServiceClient {
@DELETE
@Produces(value = MediaType.APPLICATION_JSON)
String processDeleteRequest();
}
```

同样，我们通过 *microprofile-config.properties* 文件指定正在调用的 RESTful Web 服务的 URI。

```
com.ensode.restclient.SampleRestfulServiceClient/mp-rest/url=http://localhost:8080/webresources
```

与其他 HTTP 请求类型的情况一样，我们需要确保服务的根 URI 与 RESTful Web 服务项目中 `Application` 类中定义的值相匹配。

要生成 DELETE 请求，我们需要通过 CDI 的 `@Inject` 和 `@RestClient` 注解注入我们的接口；然后调用使用 `@DELETE` 注解的方法，并可选地将响应存储在一个变量中。

```
package com.ensode.restclient;
//imports omitted
@ApplicationScoped
public class RestfulClient {
private static final Logger LOGGER =
Logger.getLogger(RestfulClient.class.getName());
@Inject
@RestClient
private SampleRestfulServiceClient sampleRestfulServiceClient;
public void init(
@Observes @Initialized(ApplicationScoped.class)
Object object) {
String returnedDeleteData;
LOGGER.log(Level.INFO, "--- Sending HTTP DELETE request ");
returnedDeleteData =
sampleRestfulServiceClient.processDeleteRequest();
LOGGER.log(Level.INFO, String.format(
"--- DELETE request response: %s", returnedDeleteData));
}
}
```

运行我们的应用程序后，我们应该能够在 Payara Micro 的命令行输出中看到服务器响应。

```
[2021-08-20T14:58:59.463-0400] [] [INFO] [] [com.ensode.restclient.RestfulClient] [tid: _ThreadID=1 _ThreadName=main] [timeMillis: 1629485939463] [levelValue: 800] --- DELETE request response: {"msg":"Service processed HTTP DELETE request!"}
```

## 路径参数和查询参数

就像我们可以开发 RESTful Web 服务来接受路径参数和查询参数一样，我们也可以开发客户端来传递它们；我们只需要在 REST 客户端接口的方法上使用与服务端相同的注解即可。

### 路径参数

要将路径参数传递给 RESTful Web 服务，我们使用 `@Path` 注解，提供一个 URI 模板来指定每个参数的位置；此外，我们需要使用 `@PathParam` 注解标注每个方法参数；此注解的值必须与方法上 `@Path` 注解中定义的 URI 模板中的相应参数匹配。

```
package com.ensode.pathparamsclient;
//imports omitted
@RegisterRestClient
@Path("pathparams")
public interface PathParamsSampleServiceClient {
@GET
@Produces(MediaType.APPLICATION_JSON)
@Path("/{title}/{lastName}")
public String sayFormalHello(@PathParam("title") String title,
@PathParam("lastName") String lastName);
}
```

如果这一切看起来很熟悉，那是因为在 RESTful Web 服务上定义服务接受的路径参数时，使用了完全相同的注解。这种一致性使得针对使用 Jakarta RESTful Web Services API 开发的服务来开发客户端变得非常容易。

然后，我们可以像往常一样使用 `@RestClient` 和 `@Inject` 注解注入我们的 RESTful 接口。要将路径参数传递给服务，我们只需使用适当的参数调用 RESTful 客户端接口中的相应方法。

```
package com.ensode.pathparamsclient;
//imports omitted
@ApplicationScoped
public class PathParamRestfulClient {
@Inject
@RestClient
private PathParamsSampleServiceClient
pathParamsSampleServiceClient;
private static final Logger LOGGER =
Logger.getLogger(PathParamRestfulClient.class.getName());
public void init(@Observes @Initialized(ApplicationScoped.class)
Object object) {
String formalResponse;
formalResponse =
pathParamsSampleServiceClient.sayFormalHello("Mr",
"Heffelfinger");
LOGGER.log(Level.INFO, String.format(
"Received the following formal response: %s",
formalResponse));
}
}
```

在 Payara Micro 中运行我们的应用程序后，我们可以在其命令行输出中看到来自服务的响应。

```
[2021-08-20T15:06:24.930-0400] [] [INFO] [] [com.ensode.pathparamsclient.PathParamRestfulClient] [tid: _ThreadID=1 _ThreadName=main] [timeMillis: 1629486384930] [levelValue: 800] [[
Received the following formal response: {
"msg":"Hello, Mr Heffelfinger"
}]]
```

### 查询参数

如果我们需要将查询参数传递给 RESTful Web 服务，我们可以通过使用 `@QueryParam` 注解标注方法参数来实现。此注解指示每个方法参数将填充的查询参数名称。

```
package com.ensode.queryparamsclient;
//imports omitted
@RegisterRestClient
@Path("queryparams")
public interface QueryParamsSampleServiceClient {
@GET
@Produces(MediaType.APPLICATION_JSON)
public String sayFormalHello(@QueryParam("title") String title,
@QueryParam("lastName") String lastName);
}
```

毫不意外，创建生成查询参数的客户端的过程与创建接受查询参数的服务器端的过程几乎完全相同。

要传递查询参数，我们像往常一样通过 `@RestClient` 和 `@Inject` 注解注入客户端接口，然后使用适当的参数调用其对应的方法。

```
package com.ensode.queryparamsclient;
//imports omitted
@ApplicationScoped
public class QueryParamsRestfulClient {
private static final Logger LOGGER =
Logger.getLogger(QueryParamsRestfulClient.class.getName());
@Inject
@RestClient
private QueryParamsSampleServiceClient
queryParamsSampleServiceClient;
public void init(@Observes @Initialized(ApplicationScoped.class)
Object object) {
String serverResponse;
serverResponse =
queryParamsSampleServiceClient.sayFormalHello("Mr",
"Heffelfinger");
LOGGER.log(Level.INFO, String.format(
"Received the following response: %s", serverResponse));
}
}
```

在 Payara Micro 中运行我们的客户端应用程序时，我们可以看到服务发送的响应。

```
[2021-08-20T15:08:51.480-0400] [] [INFO] [] [com.ensode.queryparamsclient.QueryParamsRestfulClient] [tid: _ThreadID=1 _ThreadName=main] [timeMillis: 1629486531480] [levelValue: 800] [[
Received the following response: {
"msg":"Hello, Mr Heffelfinger"
}]]
```



## 从 Java 对象生成 JSON

我们需要传递给所调用服务的任何 JSON 字符串，都必须作为参数添加到所调用的方法中。前提是，该服务会接受一个符合我们 Java 类结构的 JSON 字符串作为请求体。

```java
package com.ensode.jsonparsingclient;
// 省略导入语句
@RegisterRestClient
@Path("messageprocessor")
public interface MessageProcessorClient {
@PUT
@Consumes(MediaType.APPLICATION_JSON)
@Produces(MediaType.APPLICATION_JSON)
public Message processMessage(Message message);
}
```

MicroProfile REST 客户端 API 会自动完成从 Java 对象到 JSON 的转换。如果我们有一个与需要生成的 JSON 结构相同的 Java 类，只需像往常一样实例化该类，并将其作为参数传递给生成 HTTP 请求的 REST 客户端接口方法即可。

例如，如果需要生成如下所示的 JSON 字符串：

```json
{
"msgText":"Hello from the MicroProfile REST client!"
}
```

我们可以简单地创建一个包含 `String` 属性的 Java 类，该属性与 JSON 字符串中的键名匹配。

```java
package com.ensode.jsonparsingclient;
public class Message {
private String msgText;
// 省略 getter 和 setter 方法
}
```

然后，我们只需实例化该类，填充其属性，并将其作为参数传递给相应的方法；序列化为 JSON 字符串的工作会自动为我们完成。

```java
package com.ensode.jsonparsingclient;
// 省略导入语句
@ApplicationScoped
public class MessageProcessorRestClient {
private static final Logger LOGGER = Logger.getLogger(MessageProcessorRestClient.class.getName());
@Inject
@RestClient
private MessageProcessorClient messageProcessorClient;
public void init(@Observes @Initialized(ApplicationScoped.class) Object object) {
Message message = new Message();
Message returnedMessage;
message.setMsgText(
"Hello from the MicroProfile REST client!");
returnedMessage =
messageProcessorClient.processMessage(message);
LOGGER.log(Level.INFO, String.format("Server returned the following message: %s", returnedMessage.getMsgText()));
}
}
```

如我们所见，所有 JSON 处理都由 MicroProfile REST 客户端 API 自动为我们处理。事实上，如果我们同时开发服务和客户端，则完全无需直接处理 JSON，因为 Jakarta RESTful Web Services API 会在服务端处理转换，而正如我们刚才所见，MicroProfile REST 客户端 API 会在客户端处理转换。

如果出于任何原因，我们需要处理原始 JSON，只需在方法签名中使用 `String` 作为参数类型即可。

## 总结

在本章中，我们了解了如何使用 MicroProfile REST 客户端 API 创建 RESTful Web 服务客户端。我们介绍了如何通过编写一个带有几个简单注解的 Java 接口来创建客户端，甚至无需为该接口创建实现。我们还讨论了如何发送不同类型的 HTTP 请求，包括 GET、PUT、POST 和 DELETE。我们看到了为使用 Jakarta RESTful Web Service API 创建的服务创建 RESTful 客户端是多么简单，几乎微不足道，因为所有方法签名和注解都遵循客户端中建立的模式。

此外，我们还介绍了如何向需要参数的 RESTful Web 服务传递参数，包括路径参数和查询参数。

最后，我们介绍了如何向 HTTP 请求添加 JSON 主体，以及 MicroProfile REST 客户端 API 如何为我们处理大部分 JSON 处理的繁重工作。

