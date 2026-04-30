# 2. Micronaut

Micronaut 是一个基于 JVM 的现代全栈微服务框架。这个新框架由 Grails 团队开发，旨在解决多年来在构建实际微服务应用过程中发现的问题。

Micronaut 最令人兴奋的特性之一是其编译时依赖注入机制。大多数框架使用反射和代理在运行时执行依赖注入。然而，Micronaut 在编译时构建其依赖注入数据。其结果是更快的应用启动速度和更小的内存占用。

我认为，如果说我们生活在微服务的时代，这并不夸张。微服务已成为每个正在实施的新企业级应用的事实上的架构模式，并且许多现有的单体应用正在迁移到微服务。在 Java 领域，Spring Boot 已成为开发微服务的标准框架。还有其他一些框架，如 DropWizard、Apache Karaf 和 Jersey。但它们无法与 Spring Boot 展开激烈竞争，其使用率逐渐下降，并在一段时间后变得微不足道。如果你观察 Spring Boot 的演变，最初它并非作为 Spring 的微服务解决方案提出的。它最初是作为无容器 Web 应用提出并实现的，开发者社区随后开始将其用于微服务实现。但 Spring Boot 有其自身的局限性，例如：

*   固定的单一语言

*   缺乏对数据访问的内置支持

*   缺乏更简单的单元测试

*   缺乏内置的服务发现

*   缺乏内置的负载均衡

我们需要显式配置，这可以通过云服务实现，而不是在框架本身中内置支持。

这时，Micronaut 出现了，它包含了上述内置特性，并且其单一且主要的设计意图是作为微服务开发的载体。

## Micronaut 与 Spring Boot 的比较

### 安装便捷性

Spring Boot 和 Micronaut 的安装都不复杂，可以按照安装说明轻松完成安装。两个框架都需要以下先决条件：

*   一个喜欢的文本编辑器或 IDE

*   JDK 1.8 或更高版本

*   Gradle 或 Maven 的最新版本

通过 CLI 工具生成的代码可以直接导入到你的 IDE 中：

*   **Spring Tool Suite (STS)**：Spring Boot

*   **Visual Studio Code**：Micronaut

### 原生云支持

对于 Spring Boot，要支持前面讨论的云特定特性，我们需要依赖第三方云服务或库；它默认不支持上述任何特性，因此 Micronaut 在这方面具有优势。

以下云特定特性列表直接集成到了 Micronaut 运行时中：

*   服务发现。

*   支持 Eureka、Consul 或 ZooKeeper 服务发现服务器。

*   默认支持 Kubernetes 容器运行时。

*   客户端负载均衡。

*   可以使用 Netflix Ribbon 进行负载均衡。

*   分布式配置。

*   分布式追踪。

*   无服务器函数。

### 无服务器函数

无服务器架构，开发者部署函数。从那时起，它们完全由云环境管理，即调用、执行和控制。但 Micronaut 的快速启动时间、编译时方法和低内存占用使其成为开发函数的绝佳选择。事实上，Micronaut 特性为在 AWS Lambda 和任何支持将函数作为容器运行的 FaaS 系统上实现和部署函数提供了专门支持。



### 应用配置

Micronaut 的灵感来源于 Grails 和 Spring Boot，它将来自不同来源的配置属性直接集成到核心 IoC 容器中。默认情况下，配置可以通过 YAML、JSON、Java 属性文件或 Groovy 文件提供。惯例是查找名为 `application.yml`、`application.properties`、`application.json` 或 `application.groovy` 的文件。

*   命令行参数

*   来自 `SPRING_APPLICATION_JSON` 的属性（仅当存在任何 Spring 依赖时）

*   来自 `MICRONAUT_APPLICATION_JSON` 的属性

*   Java 系统属性

*   操作系统环境变量

*   每个环境特定的属性文件，如 `application-{environment}.{extension}`（可以是 `.properties`、`.json`、`.yml` 或 `.groovy`）

*   来自 `application.{extension}` 的应用特定属性（可以是 `.properties`、`.json`、`.yml` 或 `.groovy`）

*   特殊属性（随机值）

Spring Boot 支持上述所有属性位置；此外，它还支持其他属性位置：

*   Spring Boot devtools 全局设置属性

*   测试中的 `@TestPropertySource` 注解

*   测试中的 `@SpringBootTest#properties` 注解属性

*   `ServletConfig` 初始化参数

*   `ServletContext` 初始化参数

*   来自 `java:comp/env` 的 JNDI 属性

*   `@Configuration` 类上的 `@PropertySource` 注解

*   默认属性（通过设置 `SpringApplication.setDefaultProperties` 指定）

“与 Micronaut 相比，Spring Boot 提供了更多处理属性的方式。”

### 消息系统支持

Spring Boot 支持集成外部消息系统，例如：

*   RabbitMQ

*   Apache Kafka

*   ActiveMQ

*   Artemis

Micronaut 也支持流行的消息系统，例如：

*   RabbitMQ

*   Apache Kafka

“Micronaut 内嵌支持 Apache Kafka。” “两个框架都支持流行的消息系统，但 Spring Boot 支持更多工具。”

### 安全

Spring Boot 默认支持以下安全机制：

*   MVC 安全

*   WebFlux 安全

*   OAuth 2

*   Actuator 安全

Micronaut 默认支持以下安全机制：

*   认证提供者

*   安全规则

*   IP 模式规则

*   `@Secured` 注解

*   拦截 URL 映射

*   内置端点安全

*   认证策略

*   基本认证

*   会话认证

*   JSON Web Token

*   内置安全控制器

*   检索已认证用户

*   安全事件

### 缓存

Spring Boot 支持以下缓存提供者：

*   Redis

*   Couchbase

*   Generic

*   JCache (JSR-107)

*   EhCache 2.x

*   Hazelcast

*   Infinispan

*   Caffeine

Micronaut 支持以下缓存提供者列表：

*   Caffeine（默认情况下，Micronaut 支持它）

*   Redis

“显然，Spring Boot 在支持缓存提供者方面处于领先地位。”

### 管理与监控

受 Grails、Spring Boot 和 Micronaut 管理依赖的启发，Micronaut 增加了通过端点监控应用的支持，这些端点是特殊的 URI，用于返回应用状态和健康状态的详细信息：

*   创建端点

*   内置端点

## API 组合

本书将选取三个业务领域问题，并构建一个 API 组合。

### 在线航班

为了说明 Micronaut 的特性，本书将以一个“在线航班”应用为例。该应用将使乘客能够查看他们乘坐的航班。你将定义两个组件类：

| 对象 | 字段 | 类型 |
| --- | --- | --- |
| Passenger | Name | String |
| Flight | Origin | String |
|   | Destination | String |
|   | Departure | Datetime |
|   | Flight# | int |

1.  一个服务组件，允许乘客查看他们已预订的航班。

2.  一个仓库组件，用于存储某航班的乘客。为简单起见，最初你将乘客存储在内存中。

### 消息

此 API 将支持向系统中的用户发送消息。

| 对象 | 字段 | 类型 |
| --- | --- | --- |
| Message | Message | String |
|   | From | String |
|   | To | String |
|   | Creation Date | Date |

#### 报价

为了说明 Micronaut Data 的特性，本书将以一个“在线报价”应用为例。该应用将使买家能够创建和查看报价，包括他们想要购买的产品。你将定义三个组件类：

| 对象 | 字段 | 类型 |
| --- | --- | --- |
| Product | Name | String |
|   | Description | String |
|   | Unit Price | Float |
| Quote | Customer | String |
|   | Quote Date | Date |
|   | Address | Object |
|   | Quote Line | Object |
|   | Total Price | Float |
| Quote Line | Product | Object |
|   | Quantity | Long |
|   | Unit Price | Float |

1.  列出产品及其价格的目录

2.  包含产品行项目及总价的客户报价

3.  包含产品单价和数量的报价行项目

## 软件

本书将使用以下软件进行编码练习。

### Micronaut

[`https://micronaut.io/download/`](https://micronaut.io/download/)

使用 SDKMAN! 安装

该工具可以轻松地在任何基于 Unix 的平台（Mac OSX、Linux、Cygwin、Solaris 或 FreeBSD）上安装 Micronaut 框架。

只需打开一个新终端并输入

```
$ curl -s https://get.sdkman.io | bash
```

按照屏幕上的说明完成安装。打开一个新终端或输入以下命令：

```
$ source "$HOME/.sdkman/bin/sdkman-init.sh"
```

然后安装框架的最新稳定版本：

```
$ sdk install micronaut
```

如果提示，请将其设为默认版本。

安装完成后，可以通过以下命令进行测试：

```
$ mn --version
```

仅此而已！

现在让我们创建“来自 Micronaut 的问候”。

假设已安装最新的 Micronaut、Gradle 和 JDK 21。

```
mn create-app hello-world
```

### JDK 21

[`https://jdk.java.net/archive/`](https://jdk.java.net/archive/)

### POSTMAN

[`www.postman.com/downloads/`](https://www.postman.com/downloads/)

### CURL

[`https://curl.se/download.html`](https://curl.se/download.html)

### IDE

你有两种 IDE 选择。

#### Visual Studio Code

[`https://code.visualstudio.com/download`](https://code.visualstudio.com/download)

#### IntelliJ

[`www.jetbrains.com/idea/download/`](https://www.jetbrains.com/idea/download/)

### Maven

[`https://maven.apache.org/download.cgi`](https://maven.apache.org/download.cgi)

上一节创建的应用包含一个位于 `src/main/java` 的“主类”，如下所示：

```
package hello.world;
import io.micronaut.runtime.Micronaut;
public class Application {
public static void main(String[] args) {
Micronaut.run(Application.class);
}
}
```

这是通过 Gradle 或部署运行应用时运行的类。

使用你选择的任何文本编辑器，在创建 `Application` 类的同一文件夹中创建以下文件。

```
package hello.world;
import io.micronaut.http.MediaType;
import io.micronaut.http.annotation.Controller;
import io.micronaut.http.annotation.Get;
@Controller("/hello") public class HelloController {
@Get(produces = MediaType.TEXT_PLAIN)
public String index() {
return "Hello from Micronaut";
}
}
```

将文件保存为 `HelloController.java`。文件将如上例所示。现在从命令提示符运行：

```
cd ~/hello-world
./gradlew run
curl http://localhost:8080/hello
```

## 总结

在本章中，我们回顾了 Micronaut 的特性，并将其与 Spring Boot 进行了比较。我们还分析了示例领域——航班状态消息和报价，以使用 Micronaut 创建一个 API 组合。

