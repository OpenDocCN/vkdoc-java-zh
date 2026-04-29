# 2. 使用 Payara Micro 开发微服务

微服务本质上就是 RESTful Web 服务；它们通常执行单一任务；因此，它们的代码库往往很小。在微服务架构中，应用程序由多个相互通信的不同微服务组成。

Payara Micro 是一个符合 MicroProfile 规范的运行时；因此，使用 Payara Micro 部署的应用程序必须符合 MicroProfile 规范。MicroProfile 使用 Jakarta RESTful Web 服务 API 进行 RESTful Web 服务部署。在本章中，我们将了解如何使用该 API 开发 RESTful Web 服务，并在 Payara Micro 上执行它们。

## 设置环境

Payara 提供了一个 Maven 插件，可以更轻松地处理 Payara Micro 项目。在本节中，我们将讨论如何为 Payara Micro 应用程序设置 Maven 项目。

NetBeans IDE 可以直接创建一个 Payara Micro 项目，并包含所有必要的配置。

### Payara Micro Maven 插件

要将 Payara Micro Maven 插件添加到您的项目中，请将其添加到 *pom.xml* 文件的 `<plugins>` 部分，如下例所示。

```

fish.payara.maven.plugins
payara-micro-maven-plugin
1.4.0

5.2021.5
false

--autoBindHttp

--deploy

${project.build.directory}/${project.build.finalName}

/

```

在 `<configuration>` 部分，我们可以设置多个配置选项，用于在 Payara Micro 中运行我们的应用程序。

Maven 会自动下载我们在 `<payaraVersion>` 标签中指定的 Payara Micro 版本。

我们可以在 `<deployWar>` 标签中指定是否要自动部署项目 WAR 文件。通常在开发应用程序时，将应用程序部署为展开的 WAR 文件是有意义的（稍后会详细介绍）；因此，最好将此值设置为 `false`。

我们可以在 `<commandLineOptions>` 标签中指定要传递给 Payara Micro 的命令行选项。表 2-1 列出了可用的选项。

表 2-1

Payara Micro 命令行选项

| 命令行选项 | 描述 |
| --- | --- |
| `autoBindHttp` | 如果设置为 true，Payara Micro 将自动查找可用端口并将其绑定为 HTTP 端口 |
| `deploy` | 指定一个展开的或标准的 WAR 文件给 Payara Micro。如示例所示，在从 Maven 部署项目时，最好利用 Maven 环境变量 |
| `port` | 如果 `autoBindHttp` 设置为 false，用于指定要使用的 HTTP 端口 |

Payara Micro 的完整命令行选项列表可在 [*https://docs.payara.fish/community/docs/documentation/payara-micro/appendices/cmd-line-opts.html*](https://docs.payara.fish/community/docs/documentation/payara-micro/appendices/cmd-line-opts.html) 找到。

此外，我们可以通过 `<contextRoot>` 标签指定应用程序的上下文根。在微服务应用程序中，每个应用程序通常只有一个 RESTful Web 服务；因此，通常我们会在这里使用根上下文（“/”）。

### Payara BOM

Maven 物料清单（BOM）是一种特殊的 POM，用于保持依赖关系同步。Payara 提供了一个 BOM，其中包含了 Payara Micro 所需的所有依赖项。为了成功构建我们的代码并将其部署到 Payara Micro，我们需要将 Payara BOM 包含在 *pom.xml* 文件的 `<dependencyManagement>` 部分。例如：

```

fish.payara.api
payara-bom
${version.payara}
pom
import

```



### Payara Maven 仓库

为了找到 Payara 特定的依赖项，我们需要将 Payara Maven 仓库添加到 *pom.xml* 的 `<repositories>` 部分。

```

payara-nexus-artifacts
Payara Nexus Artifacts
https://nexus.payara.fish/repository/payara-artifacts

true

false

```

Nexus 是一个流行的开源仓库管理工具。Payara Maven 仓库使用 Nexus 来管理其 Maven 构件。

### Jakarta EE 和 MicroProfile 依赖项

最后但同样重要的是，我们需要为我们项目中将要使用的任何 Jakarta EE 或 MicroProfile API 添加依赖项。我们的简单示例仅使用了 Jakarta RESTful Web Services API；因此，我们只需要一个依赖项。

```

jakarta.platform
jakarta.jakartaee-web-api
provided

```

## 为我们的 Web 服务指定基础 URI

在我们开始开发 RESTful Web 服务之前，我们需要指定我们模块中所有 Web 服务端点将共享的基础 URI。这可以通过 `@ApplicationConfig` 注解来完成，如下例所示。

```
package com.ensode.microservice;
import javax.ws.rs.ApplicationPath;
import javax.ws.rs.core.Application;
@ApplicationPath("webresources")
public class ApplicationConfig extends Application {
}
```

被注解的类必须继承 `Application` 类；`ApplicationPath` 注解的值对应我们 RESTful Web 服务的根 URI（在我们的示例中是 `webresources`）。我们在此处指定的根 URI 将位于我们开发的 RESTful Web 服务 URI 之前；例如，如果我们编写一个 URI 为 "sample" 的 RESTful Web 服务，那么该 Web 服务的完整 URI 将是 `/webresources/sample`。

## 运行我们的应用程序

Maven 可以通过 `war:exploded` 目标从任何 Java Web 项目创建一个展开的 WAR 文件。类似地，我们可以通过 Payara Micro Maven 项目提供的 `payara-micro:start` Maven 目标直接从 Maven 启动 Payara Micro。

通过使用上述两个 Maven 目标调用 Maven，我们可以启动 Payara Micro 并自动部署我们的应用程序代码。

```
mvn war:exploded payara-micro:start
```

我们应该会在命令行中看到类似如下的输出：

```
[2021-08-07T13:21:06.418-0400] [] [INFO] [] [PayaraMicro] [tid: _ThreadID=1 _ThreadName=main] [timeMillis: 1628356866418] [levelValue: 800] [[
Payara Micro URLs:
http://192.168.1.165:8080/
'microservice-1.0-SNAPSHOT' REST Endpoints:
GET    /openapi/
GET    /openapi/application.wadl
GET    /webresources/application.wadl
DELETE /webresources/sample
GET    /webresources/sample
PATCH  /webresources/sample
POST   /webresources/sample
PUT    /webresources/sample
]]
[2021-08-07T13:21:06.418-0400] [] [INFO] [] [PayaraMicro] [tid: _ThreadID=1 _ThreadName=main] [timeMillis: 1628356866418] [levelValue: 800] Payara Micro  5.2021.4 #badassmicrofish (build 740) ready in 7,751 (ms)
Handling HTTP GET requests
```

请注意，输出指定了 Payara Micro 正在监听的 IP 地址和 HTTP 端口，以及所有 RESTful Web 服务端点及其监听的 HTTP 请求类型。

其中一些端点是 Payara Micro 自动生成的。

通过这种方式启动我们的应用程序，结合大多数现代 Java IDE 拥有的保存时编译功能，我们可以热部署我们的应用程序，这意味着当我们保存代码时，代码更改会自动被 Payara Micro 捕获，从而无需手动构建和运行代码来测试我们的代码更改。

## 开发 RESTful Web 服务

为了将任何普通的旧 Java 对象转变为 RESTful Web 服务，我们只需要使用 `@Path` 注解对其进行注解。此注解允许我们为 Web 服务指定 URI；它还让运行时（在我们的例子中是 Payara Micro）知道该类是一个 Web 服务。

```
package com.ensode.microservice;
@Path("sample")
public class SampleRestFulService {
}
```

`@Path` 注解与我们之前讨论的 `@ApplicationPath` 注解一起，决定了我们 RESTful Web 服务的完整 URI。在我们的示例项目中，RESTful Web 服务的完整 URI 将是 `webresources/sample`。

一个 RESTful Web 服务需要响应一个或多个 HTTP 请求类型（GET、POST、PUT、DELETE 或 PATCH）；这是通过方法级别的注解来实现的。

### 处理 HTTP GET 请求

我们可以通过使用 `@GET` 注解来注解我们的一个方法，从而在我们的 RESTful Web 服务中处理 HTTP GET 请求。如果我们的方法向客户端返回一个值，我们需要通过 `@Produces` 注解指定返回值的 MIME 类型。

```
@GET
@Produces(MediaType.APPLICATION_JSON)
public String processGetRequest() {
String json = "{"
+ "\"msg\":\"Service processed HTTP GET request!\""
+ "}";
return json;
}
```

JSON 是迄今为止在 RESTful Web 服务和微服务中用于数据传输的最常见的 MIME 类型，但它绝不是唯一的一种。所有支持的 MIME 类型都在 `MediaType` 类中定义为常量；其他支持的包括 XML (`MediaType.TEXT_XML`)、HTML (`MediaType.TEXT_HTML`) 和纯文本 (`MediaType.TEXT_PLAIN`) 等。

在你的 IDE 中对 `MediaType` 类使用代码补全功能，可以查看所有支持的 MIME 类型。

在我们的简单示例中，我们只是硬编码了一个 JSON 字符串并返回它。通常，我们会处理一些数据，并使用 Jakarta EE 的 JSON 处理库（JSON-B 或 JSON-P）之一生成一个 JSON 字符串。我们将在本书后面介绍 JSON 处理。

### 处理 HTTP POST 请求

HTTP POST 请求通过 `@POST` 注解处理；用法与之前讨论的 `@GET` 注解相同。

```
@POST
@Produces(MediaType.APPLICATION_JSON)
public String processPostRequest() {
String json = "{"
+ "\"msg\":\"Service processed HTTP POST request!\""
+ "}";
return json;
}
```

这个例子与上一节中的例子几乎相同，主要区别在于我们使用 `@POST` 注解来注解我们的方法，这会导致当我们的服务响应 HTTP POST 请求时调用该方法。

### 处理 HTTP PUT 请求

HTTP PUT 请求通过 `@PUT` 注解处理。

```
@PUT
@Produces(MediaType.APPLICATION_JSON)
public String processPutRequest() {
String json = "{"
+ "\"msg\":\"Service processed HTTP PUT request!\""
+ "}";
return json;
}
```

此方法上的 `@PUT` 注解会导致当我们的服务响应 HTTP PUT 请求时调用该方法。

### 处理 HTTP DELETE 请求

HTTP DELETE 请求通过 `@DELETE` 注解处理。

```
@DELETE
@Produces(MediaType.APPLICATION_JSON)
public String processDeleteRequest() {
String json = "{"
+ "\"msg\":\"Service processed HTTP DELETE request!\""
+ "}";
return json;
}
```

此方法上的 `@DELETE` 注解会导致当我们的服务响应 HTTP DELETE 请求时调用该方法。

### 处理 HTTP PATCH 请求

我们也可以通过 `@PATCH` 注解来处理 HTTP PATCH 请求。

```
@PATCH
@Produces(MediaType.APPLICATION_JSON)
public String processPatchRequest() {
String json = "{"
+ "\"msg\":\"Service processed HTTP PATCH request!\""
+ "}";
return json;
}
```

正如预期的那样，此方法上的 `@PATCH` 注解会导致当我们的服务响应 HTTP PATCH 请求时调用该方法。

## 路径参数和查询参数

你可以向 RESTful Web 服务传递参数；有两种方法可以做到这一点：使用路径参数和查询参数；Jakarta RESTful Web Services API 支持这两种方式。



### 路径参数

路径参数会在调用 RESTful Web 服务时被添加到路径中；例如，以下 RESTful Web 服务 URI 包含两个路径参数：第一个参数值为“Mr”，第二个参数值为“Heffelfinger”。

*http://localhost:8080/webresources/pathparams/Mr/Heffelfinger*

为了让我们的 RESTful Web 服务能够接受路径参数，我们需要在方法级别添加 `@Path` 注解，并为每个对应路径参数的方法参数添加 `@PathParam` 注解。

```
package com.ensode.pathparams;
//imports omitted
@Path("pathparams")
public class PathParamsSampleService {
@GET
@Produces(MediaType.APPLICATION_JSON)
@Path("/{title}/{lastName}")
public String sayFormalHello(@PathParam("title") String title,
@PathParam("lastName") String lastName) {
return String.format("{\n"
+ "  \"msg\":\"Hello, %s %s\"\n"
+ "}", title, lastName);
}
}
```

方法级别的 `@Path` 注解提供了一个 URI 模板，指定了每个路径参数的位置。在我们的示例中，第一个参数名为 `title`，第二个参数名为 `lastName`；我们通过这种方式使用 `@Path` 注解来指定期望的路径参数顺序。

为了将路径参数绑定到方法参数，我们使用了示例中所示的 `@PathParameter` 注解；请注意，`@PathParameter` 注解的值必须与方法级别 `@Path` 注解中指定的 URI 模板变量名称（在我们的示例中为 `title` 和 `lastName`）相匹配。

当客户端调用我们的服务时，期望客户端按正确顺序传递预期的参数；例如，我们可以使用 `curl` 如下调用我们的服务：

curl `http://localhost:8080/webresources/pathparams/Mr/Heffelfinger`

在这种情况下，*Mr* 对应于我们第一个参数的值，如方法级别 *@Path* 注解的 URI 模板中所定义；*Heffelfinger* 对应于第二个路径参数，同样在 URI 模板中定义；通过使用 *@PathParam* 注解（其值与 URI 模板中定义的 URI 变量（在我们的示例中为 *title* 和 *lastName*）匹配）对这些参数进行注解，这些值会自动绑定到相应的方法参数。

一旦我们调用服务，它将返回一个包含相应值的 JSON 字符串。

```
{
"msg":"Hello, Mr Heffelfinger"
}
```

### 查询参数

我们可以向 RESTful Web 服务传递参数的第二种方式是通过查询参数。在这种情况下，参数作为包含名称-值对的查询，在我们的 Web 服务的 URI 中发送。

例如，使用 curl，我们可以向 RESTful Web 服务端点传递查询参数，如下所示：

```
curl "http://localhost:8080/webresources/queryparams?title=Mr&lastName=Heffelfinger"
```

在此示例中，RESTful Web 服务端点的 URI 是 `http://localhost:8080/webresources/queryparams`；此示例有两个参数，分别名为 `title` 和 `lastName`。问号用于分隔 URI 和查询之间的边界。

```
package com.ensode.queryparams;
//imports omitted
@Path("queryparams")
public class QueryParamsSampleService {
@GET
@Produces(MediaType.APPLICATION_JSON)
public String sayFormalHello(@QueryParam("title") String title,
@QueryParam("lastName") String lastName) {
return String.format("{\n"
+ "  \"msg\":\"Hello, %s %s\"\n"
+ "}", title, lastName);
}
}
```

参数名称由 `@QueryParam` 注解定义；客户端发送的值会自动绑定到相应的方法参数。此示例的输出与我们上一节讨论的路径参数示例相同。

## 解析 JSON 数据

RESTful Web 服务可以消费和生成多种格式的数据，其中 JSON 是目前最常见的一种。

Jakarta RESTful Web 服务可以自动在 JSON 数据和 Java 之间进行转换，几乎无需我们付出任何努力。拥有一个字段与 JSON 字符串属性匹配的 Java 类，将导致这些字段自动填充相应的值。我们所要做的就是使用适当的媒体类型，用 `@Consumes` 和 `@Produces` 注解来注解我们的方法。

```
package com.ensode.jsonprocessing;
//imports omitted
@Path("messageprocessor")
public class MessageProcessor {
@PUT
@Consumes(MediaType.APPLICATION_JSON)
@Produces(MediaType.APPLICATION_JSON)
public Message processMessage(Message message) {
Message returnedMessage = new Message();
returnedMessage.setMsgText(message.getMsgText().
toUpperCase());
return returnedMessage;
}
}
```

我们的 Message 类是一个简单的 POJO（普通 Java 对象）。

```
package com.ensode.jsonprocessing;
public class Message {
private String msgText;
public String getMsgText() {
return msgText;
}
public void setMsgText(String msgText) {
this.msgText = msgText;
}
}
```

在我们的示例中，期望客户端发送一个可以解析为 `Message` 对象的 JSON 字符串。只需用 `@Consumes` 注解我们的方法，并将 *application/JSON* 指定为媒体类型，来自客户端的数据就会用于自动填充我们 Message 类的一个实例。

我们可以使用 curl 如下调用我们的服务：

```
curl -X PUT http://localhost:8080/webresources/messageprocessor -H "Content-Type: application/json" -d '{"msgText":"Yay for seamless JSON parsing!"}'
```

请注意，我们发送给服务的 JSON 数据有一个 `msgText` 属性；此属性用于填充我们 Message 类中相应的 `msgText` 实例变量。

我们的示例服务方法返回 `Message` 类的另一个实例，因为我们使用了媒体类型为 *application/json* 的 `@Produces` 注解；此返回值会自动转换为 JSON 字符串，我们可以在前面 `curl` 命令的输出中看到它。

```
{"msgText":"YAY FOR SEAMLESS JSON PARSING!"}
```

## 总结

在本章中，我们了解了如何使用 Jakarta RESTful Web 服务 API 开发 RESTful Web 服务。我们看到了如何使用几个简单的注解将 Java 类转换为 RESTful Web 服务并处理 HTTP 请求，包括 GET、PUT、POST、DELETE 和 PATCH 请求。

我们还介绍了 RESTful Web 服务的参数。我们了解了如何通过 `@PathParam` 注解处理路径参数。此外，我们还了解了如何通过 `@QueryParam` 注解处理查询参数。

最后，我们了解了如何无缝地解析 JSON 字符串并填充我们的 Java 对象，以及如何从普通 Java 对象轻松生成 JSON 字符串。

