# 2. Spring Boot

我将用我在另一本书中写过的一句话开始：Spring Boot 是使用 Spring 框架创建企业级应用的新篇章。

Spring Boot 并没有取代 Spring 框架；你可以将其视为一种使用 Java 社区使用的框架创建出色应用的新方式。

在本章中，我将向你展示什么是 Spring Boot 以及它在幕后是如何工作的。你还将通过一个小例子看到 Spring Boot 的强大之处。那么，我们为什么需要了解 Spring Boot？首先，我们将要使用的所有技术，如 Spring Integration、Spring AMQP、Spring Cloud Stream 等，都以 Spring Boot 为基础。本书中的所有示例都使用 Spring Boot 来创建 Spring 应用。当然，Spring Boot 使消息传递变得更加容易。

## 什么是 Spring Boot？

首先，Spring Boot 是一种“有主见”的技术。这意味着什么？

Spring Boot 会查看类路径，并尝试确定你正在尝试运行哪种类型的应用。例如，如果你的类路径中有 `spring-mvc` 模块，Spring Boot 将在 Spring 容器内自动装配 `WebApplicationInitializer` 和 `DispatcherServlet` 类，并设置一个嵌入式容器（默认为 Tomcat），这样你就可以运行你的应用，而无需将应用复制或部署到 Servlet 容器。

### Spring Boot 的特性

让我们看看 Spring Boot 最重要的特性：

*   它可以创建独立的 Spring 应用。基于其 Maven 或 Gradle 插件，你可以创建可执行的 JAR 或 WAR 文件。
*   它基于 starter `pom` 拥有“有主见”的技术。
*   包含自动配置，无需任何 XML 或 Java 配置类即可配置你的 Spring 应用。
*   为 Web 应用包含嵌入式 Servlet 容器（Tomcat、Jetty 或 Undertow）。
*   包含可直接使用的生产就绪特性（非功能性需求），例如指标和健康检查。
*   Spring Boot 不是一个插件或代码生成器（这意味着 Spring Boot 不会创建需要编译的文件）。
*   如果你有一个应用或 Servlet 容器，你可以将 Spring Boot 作为 WAR 部署，而无需进行任何更改。
*   你可以访问所有 Spring 应用事件和监听器（我们将在下一章讨论）。

这个列表只是 Spring Boot 所能提供功能的一小部分；当然还有更多。如果你需要了解更多，我推荐我的另一本书——Apress 出版的《Pro Spring Boot》。那本书包含了关于 Spring Boot 内部工作原理的更全面和详细的章节。



## 使用 Spring Boot 构建 Restful API

Spring Boot 的一个重要特性是，你可以创建可执行的 JAR 文件，并通过执行 `java -jar yourapp.jar` 来运行它们。你还可以创建可独立运行（使用 WAR 中的嵌入式容器）的可执行 WAR 文件，或者将其部署到 Servlet 容器中，而无需在代码中进行任何修改。

本节将向你展示一个 Restful API，它能够根据国家代码列出货币及其汇率。该项目是一个 Spring Boot Web 应用程序。

注意

你可以在 [`http://www.apress.com/9781484212257`](http://www.apress.com/9781484212257) 找到所有源代码的下载链接。

我将向你展示该应用程序的一些代码片段，以便你了解 Spring Boot 如何以极少的代码且无需配置文件就能实现的功能。

我假设你已经拥有了代码。我建议你使用 STS（Spring Tool Suite），你可以从 [`https://spring.io/tools/sts/all`](https://spring.io/tools/sts/all) 获取。我使用这个 IDE 是因为它在运行 Spring Boot 方面有非常好的特性，并且你看到的所有截图都是基于这个 IDE 的，但你可以选择任何你喜欢的 IDE。

### rest-api-demo 项目

该项目是一个 Spring Boot Web 应用程序，它将暴露 Restful 端点，用于显示某个国家的货币以及其他国家的汇率。该项目位于名为 `ch02` 的文件夹中。

该项目还使用了带有内存数据库（使用 H2 引擎）的 JPA（Java Persistence API）以及 AOP（面向切面编程）。暴露的端点列在表 2-1 中。

表 2-1.

Restful 端点

| 方法 | 路径 | 描述 |
| --- | --- | --- |
| `GET` | `/currency/latest[?base=<code>]` | 以 JSON 格式显示最新汇率。默认基准货币为 USD。 |
| `GET` | `/currency/{date}[?base=<code>]` | 根据日期（格式：yyyy-MM-dd）以 JSON 格式显示汇率。 |
| `GET` | `/currency/{amount}/{base}/to/{code}` | 根据金额、基准货币和目标货币代码显示换算结果。 |
| `POST` | `/currency/new` | 按日期添加新汇率。请求体应为 JSON 格式。 |

清单 2-1 展示了一个 JSON 响应的示例。你也可以为某些端点使用 `base` 参数。

```
{
"base": "USD",
"date": "2016-09-22",
"rates": [
{
"code": "EUR",
"rate": 0.88857
},
{
"code": "JPY",
"rate": 102.17
},
{
"code": "MXN",
"rate": 19.232
},
{
"code": "GBP",
"rate": 0.75705
}
]
}
清单 2-1.
JSON 货币响应 - /currency/latest
```

清单 2-1 展示了通过访问 `/currency/latest` 端点得到的结果响应。它显示了基准货币（此处为美元）以及用 1 美元可以从其他国家获得的所有汇率（货币）。

对于换算，你可以请求端点 `/currency/{amount}/{base}/to/{code}`。假设你想知道 10 美元等于多少日元。可以这样发起请求：`/currency/10/usd/to/jpy`。你应该会得到如清单 2-2 所示的结果。

```
{
"base": "USD",
"code": "JPY",
"amount": 10.0,
"total": 1021.69995
}
清单 2-2.
换算响应 - /currency/10/usd/to/jpy
```

清单 2-2 展示了换算端点的结果。让我们来查看其他文件。

#### pom.xml 文件

打开 `pom.xml` 文件并查看其内容。让我们来回顾一下这些标签及其含义：

*   `<packaging/>` 标签的值为 WAR。打包此应用程序时，它将生成一个可执行且可部署到任何 Servlet 容器的 WAR 文件。

    ```
    war
    ```

*   `<parent/>` 标签是 Spring Boot 能够运行的关键，因为它包含了应用程序所需的所有依赖项和版本。它基于 Maven 的 BOM（物料清单）功能。因此，这个特定的标签始终存在于你的 Spring Boot 应用程序中非常重要。

    ```
    org.springframework.boot
    spring-boot-starter-parent
    1.4.0.RELEASE

    ```

*   `<dependencies/>` 标签包含了 Spring Boot 应用程序中的所有依赖项。当你选择 Web 依赖项（使用命令行或 Spring starter）时，`spring-boot-web-starter` 依赖项会被添加到这个 `pom` 中。这类似于当你将项目选择为 WAR 类型时，`spring-boot-starter-tomcat` 依赖项会被添加。默认情况下，你总是拥有 `spring-boot-starter-test` 依赖项。

    ```

    org.springframework.boot
    spring-boot-starter-web

    org.springframework.boot
    spring-boot-starter-tomcat
    provided

    org.springframework.boot
    spring-boot-starter-data-jpa

    com.h2database
    h2
    runtime

    org.springframework.boot
    spring-boot-starter-aop

    org.springframework.boot
    spring-boot-starter-test
    test

    ```

让我们花点时间看看 `<dependencies/>` 标签。如果你熟悉使用 Maven 创建项目的方式，你现在应该会发现 `<dependency/>` 标签中缺少了一个标签。没错，`<version/>` 标签不见了。这个版本不再使用，因为 `<parent/>` 标签的定义包含了你在 Web 应用程序中将使用的所有版本和依赖项。

请注意，`spring-boot-starter-tomcat` 的作用域被设置为 provided，这意味着你可以创建一个可运行的 WAR（可以使用 `java -jar <war-name>.war` 运行）并将其部署到任何 Servlet 容器中。

请注意你创建 Spring Boot 应用程序时使用的命名约定——每个 `<groupId/>` 标签都是 `org.springframework.boot`，而 `<artifactId/>` 标签是 `spring-boot-starter-<technology>`。

为什么这很重要？请记住，Spring Boot 是一种有主见的技术，因此基于这个 `pom` 和 `spring-boot-starter`，它将识别出你正在尝试运行哪个应用程序。

#### Rate.java 类

这将是领域类。它包含一个将其定义为实体的注解。这是因为这个类将被持久化到 H2 引擎（内存数据库）中。参见清单 2-3。

```
@Entity
public class Rate {
@Id
private String code;
private Float rate;
@JsonIgnore
@Temporal(TemporalType.DATE)
private Date date;
// Setters and Getters omitted.
}
清单 2-3.
src/main/java/com/apress/messaging/domain/Rate.java
```

清单 2-3 向你展示了领域类——这里使用了 `@Entity` 和 `@Id` 注解——这与 JPA 技术（Spring Data JPA）相关。如你所见，它非常基础，没有什么太复杂的。



#### RateRepository.java 类

接下来是 `RateRepository` 接口，它基于 Spring Data JPA 技术。你需要继承 `JpaRepository<E,ID>` 并添加用于持久化的实体（领域）和 ID 类类型（`String`，一个可序列化的类）。请参见代码清单 2-4。

```
@Repository
public interface RateRepository  extends JpaRepository{
List findByDate(Date date);
Rate findByDateAndCode(Date date,String code);
}
代码清单 2-4.
src/main/java/com/apress/messaging/repository/RateRepository.java
```

代码清单 2-4 展示了 `RateRepository` 接口。你可以看到 `@Repository` 注解（这只是 Spring 容器的一个标记）；关键部分是你需要继承 `JpaRepository` 接口。该接口将为你实现所有 CRUD（创建、读取、更新和删除）操作，因此你无需自行实现。这里定义了两个查询方法——`findByDate`（查找具有该日期的汇率）和 `findByDateAndCode`（查找特定日期和代码的汇率）。该接口的特殊之处在于，你可以通过使用领域类的属性来创建 SQL 查询。如果你想了解更多关于 Spring Data JPA 的信息，请访问 [`http://docs.spring.io/spring-data/jpa/docs/current/reference/html/`](http://docs.spring.io/spring-data/jpa/docs/current/reference/html/)。

#### CurrencyController.java 类

该类定义了 REST 端点。它通过一个服务使用 `RateRepository` 接口。请参见代码清单 2-5。

```
@RestController
@RequestMapping("/currency")
public class CurrencyController {
@Autowired
CurrencyConversionService service;
@RequestMapping("/latest")
public ResponseEntity getLatest(@RequestParam(name="base",defaultValue=CurrencyExchange.BASE_CODE)String base) throws Exception{
//...
}
@RequestMapping("/{amount}/{base}/to/{code}")
public ResponseEntity conversion(@PathVariable("amount")Float amount,@PathVariable("base")String base,@PathVariable("code")String code) throws Exception{
//...
}
//此处有更多方法...
}
代码清单 2-5.
src/main/java/com/apress/messaging/controller/CurrencyController.java
```

代码清单 2-5 展示了 `CurrencyController` 类的一个片段，该类使用了 Spring MVC 的 `@RestController`、`@RequestMapping`、`@RequestParam` 和 `@PathVariable` 注解。每个响应都基于 `ResponseEntity` 类。

这是纯粹的 Spring MVC（没有太多 Spring Boot 特性）。如果你想了解更多关于 Spring MVC 的信息，请访问 [`http://docs.spring.io/spring/docs/current/spring-framework-reference/html/mvc.html`](http://docs.spring.io/spring/docs/current/spring-framework-reference/html/mvc.html)。

请复习此类并进行实验。

#### RestApiDemoApplication.java 类

该类是 Web 应用程序的主入口点。请参见代码清单 2-6。

```
@SpringBootApplication
public class RestApiDemoApplication {
public static void main(String[] args) {
SpringApplication.run(RestApiDemoApplication.class, args);
}
@Bean
public CommandLineRunner data(RateRepository repository) {
return (args) -> {
repository.save(new Rate("EUR",0.88857F,new Date()));
repository.save(new Rate("JPY",102.17F,new Date()));
repository.save(new Rate("MXN",19.232F,new Date()));
repository.save(new Rate("GBP",0.75705F,new Date()));
};
}
}
代码清单 2-6.
src/main/java/com/apress/messaging/RestApiDemoApplication.java
```

代码清单 2-6 展示了主类，Spring Boot 将在此引导应用程序。`@SpringBootApplication` 是每个 Spring Boot 应用必需的注解，因为它将触发 Spring Boot 背后的所有魔法。该注解将使用自动配置功能来确定你的应用及其最佳配置。在这种情况下，由于你拥有 `spring-boot-starter-web` 依赖（通过 `pom.xml` 文件在类路径中），它将设置所有必要的 Bean（如 `DispatcherServlet`）和嵌入式 Servlet 容器（默认为 Tomcat）来配置你的 Web 应用。

注意 `@Bean`，它返回一个 `CommandLineRunner` 接口。这将在 Spring 容器准备好所有 Bean 后执行，并将使用 `RateRepository` 接口来保存汇率。你可以将其视为初始化数据库的一种方式。

#### 其他文件……

当然，还有更多的类，但这里的目的是让你对它们进行实验，看看它们的作用以及如何在项目中使用它们。查看 `CurrencyConversionService` 类，它在 Web 控制器中被使用。

如果你想了解 AOP 在此项目中的使用方式，请查看 `CurrencyCodeAudit` 类。它定义了一个 `around` 通知，该通知仅在方法中找到 `@ToUpper` 注解时执行。（你可以从本书的源代码中获取此注解的代码。）为什么需要使用这个切面？嗯，如果你查看转换端点，你期望的是大写形式（对于基准货币和代码，如 `/150/USD/to/JPY`），但通过这种方法，你可以忽略大小写。从 `CurrencyConversionService` 方法中移除 `@ToUpper` 后，像 `/150/usd/to/jpy` 这样的请求就无法工作，因为它只评估基准货币和代码的大写字母。你需要添加额外的代码来支持它。

### 运行 Spring Boot 货币 Web 应用

运行此类项目有多种方式。如果你使用 STS 并导入项目，只需右键单击项目名称，选择“运行方式” -> “Spring Boot 应用”即可！

如果你想使用命令行运行，请确保已安装 Maven 并执行：

```
$ mvn spring-boot:run
```

然后你可以打开浏览器，输入 `http://localhost:8080/currency/latest`，应该会得到与代码清单 2-1 相同的结果。你也可以使用 `curl` 命令：

```
$ curl http://localhost:8080/currency/latest
```

如你所见，你不需要创建 WAR 文件并部署到容器中，因为 Spring Boot 自带嵌入式 Tomcat 容器。这使你能够拥有可移植性。

### 部署 Spring Boot 货币 Web 应用

如果你想将此代码部署到 Servlet 容器，你需要打包此项目。如果你使用 STS，可以右键单击项目并选择“运行方式” -> “Maven 构建”。这将弹出一个对话框。在“目标”字段中输入 `Package`，然后勾选“跳过测试”复选框。然后点击“运行”。这将在目标目录中创建一个可执行且可部署的文件。

如果你使用命令行，可以执行：

```
$ mvn package -DskipTests=true
```

这将在目标目录中创建可执行且可部署的 WAR 文件。你可以通过运行以下命令来执行该 WAR 文件：

```
$ java -jar target/rest-api-demo-0.0.1-SNAPSHOT.war
```

例如，如果你已经有一个 Tomcat 容器，你可以直接将 WAR 文件放入 `webapps/` 文件夹并运行你的容器。在复制/移动 WAR 文件时，尝试重命名它。

```
$ cp target/rest-api-demo-0.0.1-SNAPSHOT.war /opt/tomcat/webapps/rest-api-demo.war
$ /opt/tomcat/bin/startup.sh
```

然后你可以打开浏览器，访问 `http://localhost:8080/rest-api-demo/currency/latest` 来获得相同的结果。

## 更多关于 Spring Boot 的内容

如果你想了解更多关于 Spring Boot 及其其他有用特性的信息，我推荐我的另一本书，Apress 出版的《Pro Spring Boot》。该书从使用 Spring Boot CLI 到将其部署到云端，对这一技术进行了更深入的探讨。



## 摘要

本章介绍了 Spring Boot，探讨了其部分特性，并通过创建一个简单的货币 Restful 应用，解释了它如何与 Spring MVC 协同工作。

在下一章中，你将开始学习 Spring 应用事件和监听器，这是一种通过观察者模式发射和消费消息来实现消息传递的方式。

