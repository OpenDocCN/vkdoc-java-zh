# 9. 使用 OpenAPI

本章涵盖以下主题。

*   使用 OpenAPI 为你的代码编写文档

*   使用开箱即用的端点读取生成的 API 信息

*   使用 OpenAPI Generator 生成客户端



## 关于 OpenAPI

在当今的技术环境中，现代应用拥有 API 至关重要，而 RESTful API 处于 API 经济的前沿。RESTful API 能够将任何应用转换为与语言无关的服务，并可在任何地点通过任何语言进行访问，因此它已成为软件开发中不可或缺的一部分。

MicroProfile 提供了 OpenAPI 3.x 规范，以促进 RESTful API 的开发。该规范为 Java 开发者提供了多样的编程模型和接口，使他们能够从其 REST 服务原生生成 OpenAPI 文档。通过简化 OpenAPI 文档创建流程，MicroProfile 使开发者能够精简 API 开发工作流，并快速创建高质量、可互操作、且客户端可轻松消费的 API。

Helidon 开箱即用地实现了 [OpenAPI 3.0](https://download.eclipse.org/microprofile/microprofile-open-api-3.0/microprofile-openapi-spec-3.0.pdf)。它已经包含在完整的 `helidon-microprofile` 包中。若要将 OpenAPI 与其他所有 MicroProfile 选项一起使用，我们的 `pom.xml` 文件应包含清单 9-1 中的代码。

```
io.helidon.microprofile
helidon-microprofile

Listing 9-1
MicroProfile Bundle Dependency
```

如果更倾向于更细粒度的控制并使用 `helidon-microprofile-core`，请将清单 9-2 所示依赖添加到项目的 `pom.xml` 文件中。

*   ① MicroProfile OpenAPI 注解依赖

*   ② Helidon MP OpenAPI 运行时支持依赖

```
①
org.eclipse.microprofile.openapi
microprofile-openapi-api

②
io.helidon.microprofile.openapi
helidon-microprofile-openapi
runtime

Listing 9-2
OpenAPI Dependencies
```

## 基本用法

要生成精确且能够有效描述应用 API 的 OpenAPI 文档，主要方法是使用 OpenAPI 标准注解来标注相关函数。这些注解提供关键元数据，有助于创建准确且完整的 OpenAPI 文档。

应用启动后，Helidon MP 运行时会自动识别已注解的端点，并利用提供的元数据创建 OpenAPI 文档。这个精简流程消除了手动编写文档的需要，并确保 API 文档保持最新且与应用实现同步。此外，通过遵循 OpenAPI 标准，生成的 API 文档也便于共享。它可被许多工具和平台消费，从而便于与其他系统集成。

清单 9-3 是一个示例。

*   ① `@Operation` 提供该端点的信息。

*   ② `@APIResponse` 描述 HTTP 响应并声明其媒体类型和内容。

```
@GET
@Operation(summary = "Returns a Wizard", ①
description = "General Wizard Name and Licence information ")
@APIResponse(description = "Simple JSON containing Wizard name and license information", ②
content = @Content(mediaType = "application/json",
schema = @Schema(implementation = Wizard.class)))
@Produces(MediaType.APPLICATION_JSON)
public JsonObject getWizard() {...}
Listing 9-3
OpenAPI Annotations in Use
```

Helidon 应用会创建一个额外的 `/openapi` 端点，并返回描述应用中各端点的 OpenAPI 文档。根据 MicroProfile OpenAPI 规范，OpenAPI 文档的默认格式是 YAML。`application/vnd.oai.openapi` 是 OpenAPI 文档建议使用的媒体类型，已获得一定支持，但尚未被 IANA YAML 标准采纳。

清单 9-4 调用了 `/openapi` 端点。

```
curl -X GET http://localhost:8080/openapi
Listing 9-4
Retrieve OpenAPI Information with cURL
```

清单 9-5 展示了输出结果。

```
components:
schemas:
Wizard:
properties:
message:
type: object
type: object
info:
title: Generated API
version: '1.0'
openapi: 3.0.3
paths:
/wizard:
get:
description: 'General Wizard Name and Licence information'
responses:
default:
content:
application/json:
schema:
$ref: '#/components/schemas/Wizard'
description: Simple JSON containing Wizard name and license information
summary: Returns Wizard Information
Listing 9-5
Result Output with OpenAPI Data
```

注意

客户端可以将 *Accept* 指定为 `application/vnd.oai.openapi+json` 或 `application/json` 来请求 JSON。

以下是一些用于描述代码的主要注解。

*   **@Schema** 允许你定义输入和输出。

*   **@Operation** 描述某个操作，通常是特定路径上的 HTTP 方法。

*   **@Content** 为特定媒体类型提供 schema 和示例。

*   **@Link** 表示响应的一个可能的设计时链接。

*   **@Parameter** 表示 OpenAPI 操作中的单个参数。

*   **@Callback** 描述一组请求。

*   **@RequestBody** 表示操作中的请求体。

*   **@APIResponse** 表示操作中的响应。

*   **@Tag** 表示操作或 OpenAPI 定义中的标签。

*   **@Server** 表示操作或 OpenAPI 定义中的服务器。

更多信息请参阅[官方规范](https://download.eclipse.org/microprofile/microprofile-open-api-3.0/microprofile-openapi-spec-3.0.pdf)。

### 静态 OpenAPI 文件

除了使用 OpenAPI 注解生成 API 文档之外，Helidon MP 还提供了使用静态文件来提供 API 描述的选项。这个静态文件——名为 `openapi.yml`、`openapi.yaml` 或 `openapi.json`——可以添加到应用的 `/META-INF` 文件夹中。Helidon 的 OpenAPI 实现会自动识别该文件，并将其内容合并到最终文档中。

为了更轻松地创建该静态文件，可使用 Swagger 等工具生成 OpenAPI 文档。文档创建完成后，可将其添加到应用的 `/META-INF` 文件夹中，剩下的工作由 Helidon 的 OpenAPI 实现处理。

值得注意的是，Helidon MP OpenAPI 可以整合上述所有来源的数据，包括注解、静态文件和编程式配置。这使 API 文档编写更具灵活性，并让开发者能够根据需求选择最佳方式。最终目标是生成准确且全面的 OpenAPI 文档，有效描述 API 功能，并可被其他系统轻松消费。



## 自动生成客户端

OpenAPI 也可用于自动生成客户端。一旦某个 API 拥有了 OpenAPI 文档，就可以用多种编程语言为该 API 生成客户端。这能显著减少为 API 创建客户端所需的时间和精力，并确保客户端生成过程准确且一致。

此外，使用由 OpenAPI 生成的客户端，还可以提供一种与 API 交互的标准化方式，使开发者更容易在其他应用程序中使用该 API。这有助于提升互操作性，并降低因不同客户端与 API 交互方式不一致而导致错误的可能性。

关于该工具的完整信息可在其官方网站查看：[`https://openapi-generator.tech/`](https://openapi-generator.tech/)。

使用 OpenAPI Generator，可以生成适用于多种语言和框架的客户端，例如 Helidon、Jersey、MicroProfile 等等。

OpenAPI Generator 以单个 JAR 文件的形式发布。但它也提供了适用于不同操作系统的许多“原生”发行版，可通过相应的包管理器安装，或直接下载可执行文件。

最通用的版本就是可执行 JAR。下载后，在向导应用运行的情况下执行以下命令。

*   ① 检查 `http://localhost:8080/openapi` 已启动并正在运行

```
> java - jar openapi-generator.jar generate -g java --library microprofile -i http://localhost:8080/openapi ①
```

由于参数中指定了 `microprofile` 选项，生成的代码基于 MicroProfile Rest Client 规范，看起来与清单 9-6 中手写的代码非常接近。

*   ① 基于运行中的微服务生成的代码

```
@RegisterRestClient
@RegisterProvider(ApiExceptionMapper.class)
@Path("/wizard")
public interface DefaultApi{
@GET
@Produces({ "application/json" })
public Object wizardGet() throws ApiException, ProcessingException; ①
}
Listing 9-6
Generated Code Based on OpenAPI Document
```

生成的代码可以提供一个可直接使用的 API 客户端来与目标 API 通信。这能够显著减少使用 API 时所需的手动编码量。此外，由于客户端代码是直接从 OpenAPI 文档生成的，还能确保客户端代码始终与 API 文档保持同步。

使用这个插件，开发者可以在项目构建阶段自动化代码生成过程。这能在开发过程中节省大量时间和精力，并有助于确保客户端代码与 API 保持一致。

注意

有些生成的代码可以直接运行，比如以 `jaxrs` 为目标生成的代码；另一些则不行，比如清单 9-6 中的示例。

名为 `openapi-maven-plugin` 的 Maven 插件可基于 OpenAPI 文档生成客户端代码。该插件会生成客户端接口以及一组数据模型，用于表示 API 中使用的数据结构。生成的代码可以通过插件提供的模板轻松定制，或通过创建自定义模板进行定制。

## 总结

*   只需少量注解，你就可以轻松为我们的 API 编写文档。

*   另一种方式是创建独立的 YAML/JSON 文件，并将其与代码分离维护。

*   你可以根据 Helidon 自动提供的 `openapi` 信息生成客户端。

*   生成的客户端可能有不同的风格、库，甚至编程语言。

