# 13. Grails

Grails 是一个面向 Groovy 的 Web 框架，它效仿 *Ruby on Rails*，成为一个带有命令行工具、能快速完成任务的固执己见的 Web 框架。Grails 采用约定优于配置的原则来减少配置开销。

Grails 牢固地立足于 Java 生态系统，并构建在 Spring 和 Hibernate 等技术之上。Grails 还（可选地）包含一个名为 *GORM* 的对象关系映射（ORM）框架，并拥有大量插件。

不同版本的 Grails 可能差异很大，因此在升级 Grails 应用程序时需要格外小心，尤其是主版本升级（例如从 2.5 升级到 3.0）。

本章将快速概述 Grails（3.3）的工作原理，然后简要介绍 Grails 的历史。

## Grails 快速概览

安装 Grails 后，^(⁴⁴) 你可以通过在命令行运行以下命令来创建一个应用（将 `TEST` 替换为你想要使用的目录名称）：

```
1   $ grails create-app TEST
```

默认情况下，这将使用“web”配置文件创建一个应用（要获取可用配置文件列表，请运行 `grails list-profiles`）。

然后，你可以运行 `create-domain-class` 和 `generate-all` 等命令来逐步创建你的应用。运行 `grails help` 可查看所有可用命令的完整列表。

Grails 应用具有非常特定的项目结构。以下是该结构*大部分内容*的简单分解：

*   `grails-app` — Grails 专用文件夹。
    *   `assets`
        *   `images` — Web 应用使用的图片。
        *   `javascripts` — 存放 JavaScript 文件。
        *   `stylesheets` — CSS 样式表。
    *   `conf` — 配置，例如 `application.yml` 或 `application.properties` 文件。
    *   `controllers` — 应用的控制器和 `UrlMappings` 类。提供用于 list、save、show 和 create 等操作的代码。
    *   `domain` — 领域模型；如果你使用 GORM，这里存放表示持久化数据的类。
    *   `i18n` — 消息资源包，使你能够提供多种不同语言的文本。
    *   `init` — 包含 `Application.groovy` 和 `BootStrap.groovy`。
    *   `services` — 后端服务，存放后端或“业务”逻辑。
    *   `taglib` — 你可以在此处非常轻松地定义自己的标签，以便在 GSP 文件中使用。
    *   `views` — MVC 的视图；通常是 GSP 文件（基于 HTML）。
    *   `utils` — 可以包含工具类。
*   `src` — 任何不适合放在其他位置的通用代码。
    *   `main/java` — Java 代码
    *   `main/groovy` — Groovy 代码
    *   `main/resources` — 你想要放在主类路径中的任何非代码资源。
    *   `test/groovy` — 基于 Groovy 的测试，可以使用 JUnit 或 Spock。
    *   `integration-test/groovy` — 包含基于 Geb 的测试。
    *   `integration-test/resources` — 包含用于配置 Geb 的 `GebConfig.groovy`。

要创建一个新的领域（模型）类，请运行如下命令：

```
1   $ grails create-domain-class example.Post
```

为你的领域类包含一个包是一个好习惯（这里包名是“`example`”，类名是 Post）。

Grails 中的领域类还定义了其到数据库的映射。例如，下面是一个表示博客文章的领域类（假设 `User` 和 `Comment` 已经创建）：

```
1   class  Post  {
2       String text
3       int rating
4       Date created = new  Date()
5       User createdBy

7       static hasMany = [comments: Comment]

9       static constraints = {
10           text(size:10..5000)
11       }
12   }
```

静态字段 `hasMany` 是一个映射，表示数据库中的一对多关系。GORM 在后台使用 Hibernate 为所有领域类和关系创建表。默认情况下，每个表都会获得一个用于主键的 `id` 字段。

要让 Grails 自动创建你的控制器和视图，请运行以下命令：

```
1   $ grails generate-all example.Post
```

### ![../images/426440_2_En_13_Chapter/426440_2_En_13_Figa_HTML.gif](img/426440_2_En_13_Figa_HTML.gif)警告

如果文件已存在，Grails 会询问你是否要覆盖。使用此命令时请小心。

当你想要直接运行应用程序进行测试时，只需运行以下命令：

```
1   $ grails run-app
```

如果你想部署到应用容器（例如 Tomcat），可以通过运行以下命令创建一个 `war` 文件：

```
1   $ grails war
```

## 配置

由于 Grails 3+ 构建在 Spring Boot 之上，因此配置的工作方式类似，默认使用 `grails-app/conf/application.yml` 文件。

在此文件的底部附近，你会看到默认配置为开发环境和测试环境使用内存型 h2 数据库，为生产环境使用基于文件的 h2 数据库。它们的配置如下（你可以在此处更改配置以切换数据库）：

```
development:
dataSource:
dbCreate: create-drop
url: jdbc:h2:mem:devDb;MVCC=TRUE;LOCK_TIMEOUT=10000;DB_CLOSE_ON_EXIT=FALSE
test:
dataSource:
dbCreate: update
url: jdbc:h2:mem:testDb;MVCC=TRUE;LOCK_TIMEOUT=10000;DB_CLOSE_ON_EXIT=FALSE
production:
dataSource:
dbCreate: none
url: jdbc:h2:./prodDb;MVCC=TRUE;LOCK_TIMEOUT=10000;DB_CLOSE_ON_EXIT=FALSE
properties:
jmxEnabled: true
initialSize: 5
...(为简洁起见已截断)
```

## 插件

Grails 生态系统包含超过 100 个插件。要列出所有插件，只需执行以下命令：

```
1   $ grails list-plugins
```

当你选定了要使用的插件后，执行以下命令可查看可用版本以及如何将其包含在构建依赖项中（使用插件名称）：

```
1   $ grails plugin-info [NAME]
```

这将显示要添加到 `build.gradle` 文件中的适当代码，以便将插件添加到你的项目中。例如，让我们查看一下“geb”插件（Geb 是一个 Web 测试框架）。以下是输出结果：

```
$ grails plugin-info geb
| Plugin Info: geb
| Latest Version: 2.0.0
| All Versions: 1.0.2,1.1.1,1.1.2,1.1.3,1.1.4,1.1.5,2.0.0.RC1,2.0.0
| Title: Geb Plugin
Plugin that adds Geb functional testing code generation features.
* License: APACHE
* Documentation: http://grails.org/plugin/geb
* Issue Tracker: http://github.com/grails3-plugins/geb/issues
* Source: http://github.com/grails3-plugins/geb
* Definition:
dependencies {
compile "org.grails.plugins:geb:2.0.0"
}
```



## GSP 与标签库

Grails 默认使用 GSP 在 Web 应用程序中生成视图。请查看 `grails-app/views` 目录。你应该会看到一个 `index.gsp` 文件，它用于生成应用程序的登录页面。如果你按照之前的说明生成了 `example.Post` 视图，你还会在“post”目录下看到一些 GSP 文件。这些文件用于生成展示、列表、创建和编辑帖子的页面。

GSP 基于 HTML，并添加了其内置的标签库（以 `g:` 前缀开头）。你可以通过向 `grails-app/taglib` 目录添加一个以 `TagLib` 结尾的类来添加自己的标签库。例如，在 `taglib` 目录中添加一个名为 `SimpleTagLib.groovy` 的文件，并包含以下代码（该代码将使用指定格式格式化日期）：

```
class SimpleTagLib {
static namespace = "my" // 从 g: 命名空间更改为 my:
def dateFormat = { attrs, body ->
out << new
java.text.SimpleDateFormat(attrs.format).format(attrs.date)
}
}
```

现在，你就可以在你的 GSP 文件中使用以下标签了：

```

提供 JSON 或 XML

Groovy 包含一些对 JSON（JavaScript 对象表示法）编码和解码的内置支持，包括 `JsonSlurper`。Grails 还包含转换器，可以轻松地将对象转换为 XML 或 JSON，反之亦然。例如，请看下面的 `PostController`，它包含了将 `Post` 渲染为 JSON 和 XML 的方法：

```
1   import grails.converters.JSON
2   import grails.converters.XML

4   class  PostController  {
5               def getPosts() {
6                       render Post.list() as JSON
7               }
8               def getPostsXML() {
9                       render Post.list() as XML
10               }
11   }
```

对于提供多种格式的 Web 服务，你可以使用 Grails 内置的 `withFormat`。因此，上述代码可以简化为以下形式：

```
1   def getPosts() {
2           withFormat {
3                   json { render list as JSON }
4                   xml { render list as XML }
5           }
6   }
```

然后，Grails 会根据多种输入来决定使用哪种格式，最简单的方式是通过 URL 的扩展名（例如 `.json`）或请求的 `Accept` 头。要在 Grails 中*使用* Web 服务，你可以使用 http-builder-helper 插件^(⁴⁵)，或者使用 Micronaut（包含在 Grails 4 中）内置的 HTTP 客户端。

UrlMappings

默认情况下，Grails 会读取 `UrlMappings` 类来确定如何将传入的请求路由到你的 Web 应用程序。它包含一个名为“mappings”的静态值，该值使用 DSL 来描述应用程序的路由。新项目的默认 `UrlMappings` 如下所示：

```
class UrlMappings {
static mappings = {
"/$controller/$action?/$id?(.$format)?"{
constraints {
// 在此处应用约束
}
}
"/"(view:"/index")
"500"(view:'/error')
"404"(view:'/notFound')
}
}
```

第一个 GString 中的变量 `controller`、`action`、`id` 和 `format` 在此上下文中具有特殊含义。以这种方式使用它们将匹配任何给定的值，并将相应的变量设置为该值。例如，路径 **/post/show/123** 将使用参数 ID 123 在 `PostController` 上启动“show”操作。你可以修改映射以创建自定义路径。例如，你可以像这样创建一个自定义路由来创建新帖子：`"/new/post/" (controller: "post", action: "create")`。第一个匹配的路径就是 Grails 将使用的路由。

Grails 简史

以下是自 2.0 版本以来 Grails 新增功能的简要历史。

Grails 2.0

Grails 2.0 有以下改进：

*   更好的错误页面，显示导致问题的代码。
*   开发模式下的 H2 数据库控制台（位于 URI `/dbconsole`）。
*   Grails 2.0 支持 Groovy 1.8。
*   对类型化服务、领域类、`src/groovy` 和 `src/java` 的运行时重载。
*   使用 `-reloading` 运行任何命令以动态重载它。
*   二进制插件（jars）。



*   更好的脚手架，符合 HTML 5 规范（支持移动端/平板设备）。

*   面向服务的 PageRenderer 和 LinkGenerator API。

*   支持 Servlet 3.0 异步 API；事件插件；平台核心。

*   资源插件已集成到核心中。

*   用于 GZIP、缓存、打包的插件（`install-plugin cached-resources`、`zipped-resources`）。

*   新增标签：`img`、`external` 和 `javascript`。

*   jQuery 插件现在是 Grails 应用程序中默认安装的 JavaScript 库。

*   `params` 对象新增了一个 `date` 方法，用于轻松、空安全地解析日期：`def val = params.date('myDate', 'dd-MM-yyyy')`。

各种 GORM 改进包括：

*   支持 `DetachedCriteria` 以及新的 `findOrCreate` 和 `findOrSave` 方法。

*   GORM 支持 MongoDB、^(⁴⁶) riak、^(⁴⁷) Cassandra、^(⁴⁸) neo4j、^(⁴⁹) redis、^(⁵⁰) 和 Amazon SimpleDB。^(⁵¹)

*   新的编译时检查查询 DSL（“where”方法，接受一个 Closure 参数），包括：`avg`、`sum`、`subqueries`、`.size()` 等。

*   多作用域数据源。

*   添加了用于更新生产数据库的数据库迁移插件。

Grails 2.1

*   Grails 的 Maven 支持在多个重要方面得到了改进。例如，现在可以在 `pom.xml` 文件中指定插件。

*   `grails` 命令现在支持 `-debug` 选项，该选项将启动远程调试代理。

*   默认安装缓存插件。

*   在 Grails 2.1.1 中，领域类现在拥有名为 `first` 和 `last` 的静态方法，用于从数据存储中检索第一个和最后一个实例。

Grails 2.2

*   Grails 2.2 支持 Groovy 2。

*   为条件查询添加了新功能，以提供对 Hibernate 的 SQL 投影 API 的访问。

*   支持在开发模式下对 Tomcat 容器进行分叉 JVM 执行。

*   改进了对管理应用程序及其插件提供的工件之间命名冲突的支持。

Grails 2.3

*   默认使用与 Maven 相同的库（Aether）改进了依赖管理。

*   包含一个新的数据绑定机制，比之前版本使用的数据绑定器更灵活、更易于维护。

*   所有主要命令现在都可以分叉到单独的 JVM 中执行，从而将构建路径与运行时和测试路径隔离开来。

*   Grails 的 REST 支持得到了显著改进。

*   Grails 的脚手架功能已拆分为一个单独的插件（包括对生成 REST 控制器、异步控制器和 Spock 单元测试的支持）。

*   包含新的异步编程 API，允许对请求进行异步处理，并与 GORM 无缝集成。

*   控制器现在可以在命名空间中定义，允许在不同包中定义多个同名的控制器。

Grails 2.4

*   Grails 2.4 附带 Groovy 2.3。

*   默认使用 Hibernate 4.3.5。（Hibernate 3 仍可作为可选安装使用。）

*   Asset-Pipeline 取代了 Resources 来提供静态资源。

*   现在对静态类型检查和静态编译提供了出色的支持。`GrailsCompileStatic` 注解（来自 `grails.compiler` 包）的行为与 `groovy.transform.CompileStatic` 注解非常相似，并为 Grails 提供了特殊处理。

Grails 2.5

*   升级到 Groovy 2.4

*   Spring 升级到 4.1

*   其他各种升级

Grails 3.1.x
Grails 3 代表了 Grails 的一次重大重构。公共 API 现在位于 `grails` 包中，并且所有内容都已重写以使用 Traits。每个新项目还包含一个带有传统静态 void main 方法的 `Application` 类。
Grails 3 附带 Groovy 2.4、Spring 4.1 和 Spring Boot 1.2，并且构建系统使用 Gradle（取代了之前使用的 Gant 系统）。



在其他变更中，过滤器（filters）的使用已被弃用，应替换为拦截器（interceptors）。要创建一个新的拦截器，请使用以下命令：

```
1   grails create-interceptor MyInterceptor
```

一个拦截器包含以下方法：

*   `before` 方法在匹配的动作执行之前执行。其返回值决定该动作是否应执行，从而允许你取消该动作。

*   `after` 方法在动作执行之后、视图渲染之前执行。其返回值决定视图渲染是否应执行。

*   `afterView` 方法在视图渲染完成之后执行。

```
1   boolean before() { true }
2   boolean after() { true }
3   void afterView() {}
```

Grails 3 内置了对使用 `create-functional-test` 命令进行 Spock/Geb 功能测试的支持。

Grails 4

Grails 4 更新了底层库（如 Spring Boot）的版本，并将 Micronaut 集成到了核心中。以下是 Grails 4.0 支持的最低库版本：

*   Spring 5.1

*   Spring Boot 2.1

*   Java 8

*   Hibernate 5.4

*   Groovy 2.5

测试

Grails 通过混入（mixin）方式支持单元测试。它包含以下注解：

*   `@TestFor(X)`——指定被测类

*   `@Mock(Y)`——为指定类创建模拟对象

对于测试，GORM 提供了一个使用 `ConcurrentHashMap` 的内存模拟数据库。你可以为标签库、命令对象、URL 映射、XML 和 JSON 等创建测试。

缓存插件

缓存插件可用于缓存内容，以提升 Web 应用程序的性能。

*   你可以在服务或控制器方法上添加 `@Cacheable` 注解。

*   缓存标签包括 `cache:block` 和 `cache:render`。

*   相关插件包括 `cache-ehcache`、`cache-guava` 和 `cache–headers`。

![../images/426440_2_En_13_Chapter/426440_2_En_13_Figb_HTML.gif](img/426440_2_En_13_Figb_HTML.gif)仅为概述
以上是对 Grails 的简要概述。关于 Grails 及其使用方法已有许多书籍。如需更多关于使用 Grails 的信息，请访问 [`https://grails.org`](https://grails.org)。^(⁵²)

![../images/426440_2_En_13_Chapter/426440_2_En_13_Figc_HTML.gif](img/426440_2_En_13_Figc_HTML.gif)练习
创建你自己的 Grails 应用。

脚注

14. Spock

Spock^(⁵³) 是一个用于 Java 和 Groovy 应用程序的测试框架，它充分利用了 Groovy 的特性，并集成了对象模拟功能。Spock 网站^(⁵⁴) 对 Spock 的描述如下：

*   Spock 是一个用于 Java 和 Groovy 应用程序的测试和规范框架。其与众不同之处在于它优美且极具表现力的规范语言。得益于其 JUnit 运行器，Spock 与大多数 IDE、构建工具和持续集成服务器兼容。Spock 的灵感来源于 JUnit、RSpec、jMock、Mockito、Groovy、Scala、瓦肯人以及其他迷人的生命形式。

Spock 基础
Spock 中测试类的基本结构是一个继承自 `spock.lang.Specification` 的类，并包含多个测试方法（这些方法可以具有描述性的字符串名称）。
Spock 处理测试代码，并允许你使用简单的 Groovy 语法来指定测试。
每个测试由带有 `when`、`then` 和 `where` 等标签的代码块组成。学习 Spock 的最佳方式是通过示例。

首先，将 Spock 作为依赖项添加到你的项目中。例如，在 Gradle 构建文件中，添加以下内容：

```
1   dependencies {
2    testCompile "org.spockframework:spock-core:1.3-groovy-2.5"
3   }
```

一个简单的测试

让我们从重写一个简单的测试开始：

```
1   def "toString yields the String representation"() {
2           def array = ['a', 'b', 'c'] as String[]
3           when:
4           def arrayWrapper = new ArrayWrapper(array)
5           then:
6           arrayWrapper.toString() == '[a, b, c]'
7   }
```

如上所示，断言仅仅是 Groovy 的条件表达式。如果 `==` 表达式返回 `false`，测试将失败，Spock 会提供详细的输出以解释失败原因。

在没有 `when` 子句的情况下，你可以使用 `expect` 子句代替 `then`；例如：

```
1   def "empty list size is zero"() {
2           expect: [].size() == 0
3   }
```

模拟

在 Spock 中模拟接口非常容易。^(⁵⁵) 只需使用 `Mock` 方法，如下例所示（其中 `Subscriber` 是一个接口）：

```
1   class  APublisher  extends  Specification {
2     def  publisher = new  Publisher()
3     def subscriber = Mock(Subscriber)
```

现在 `subscriber` 是一个模拟对象。你可以使用重载的 `>>` 运算符来实现方法，如下所示。下面的示例在每次调用 `receive` 时抛出一个 `Exception`：

```
1   def "can cope with misbehaving subscribers"() {
2       subscriber.receive(_) >> { throw   new   Exception() }

4       when:
5       publisher.send("event")
6       publisher.send("event")

8       then:
9       2 * subscriber.receive("event")
10   }
```

可以通过使用一个数字或范围乘以 (`*`) 方法调用来描述预期行为，如此处所示。这意味着期望此方法被调用两次；否则测试将失败。
下划线 (`_`) 被视为通配符，与 Scala 中的用法类似（这里的 _ 只是一个字段，引用 Spock 定义的对象）。

数据列表或表格
就像 JUnit 有 DataPoints 和 Theories 一样，Spock 允许你在测试中使用数据列表或表格。

例如：

```
1   def "subscribers receive published events at least once"(){
2       when: publisher.send(event)
3       then: (1.._) * subscriber.receive(event)
4       where: event << ["started", "paused", "stopped"]
5   }
```

重载的 `<<` 运算符用于为 `event` 变量提供一个列表。虽然这里是一个列表，但任何 `Iterable` 对象都可以使用。这会导致对列表中的每个值运行一次测试。

范围
这里的范围 `1.._` 表示“一次或多次”。你也可以使用 `_..3`，例如，表示“三次或更少”。

也可以使用表格格式的数据。例如：

```
1   def "length of NASA mission names"() {
2         expect:
3         name.size() == length

5         where:
6         name       | length
7         "Mercury"  | 7
8         "Gemini"   | 6
9         "Apollo"   | 6
10   }
```

在这种情况下，两列（`name` 和 `length`）用于替换 `expect` 块中相应的变量。可以使用任意数量的列。

预期异常

在 `then` 块中使用 `"thrown"` 方法来预期抛出一个 `Exception`。

```
1   def "peek on empty stack throws"() {
2       when: stack.peek()
3       then: thrown(EmptyStackException)
4   }
```

你也可以通过将抛出的 `Exception` 赋值给 `thrown()` 来捕获它。例如：

```
1   def "peek on empty stack throws"() {
2       when: stack.peek()
3       then:
4       Exception e = thrown()
5       e.toString().contains("EmptyStackException")
6   }
```

总结
如你所见，Spock 使测试更简洁易读，内置了模拟功能，最重要的是，使测试的意图清晰明了。

脚注

15. Ratpack

Ratpack^(⁵⁶) 的核心是支持异步、无状态的 HTTP 应用程序。它构建于事件驱动的网络引擎 Netty 之上。与某些 Web 框架不同，它不要求一个线程处理一个请求。相反，它鼓励你以释放当前线程的方式处理阻塞操作，从而实现高性能。
Ratpack 可用于构建响应式、RESTful 的微服务，尽管这不是强制要求。



REST
代表表述性状态转移（REpresentational State Transfer）。^(⁵⁷) 它最初诞生于一篇博士论文，如今已成为新的 Web 服务标准，广受欢迎。在 REST 的最基本层面，每个 CRUD 操作都映射到一个 HTTP 方法（GET、POST、PUT 等）。

与 Grails 和其他流行的 Web 框架不同，Ratpack 的目标不是成为一个框架，而是一组库。尽管它是一组精简的库，但 Ratpack 内置了对 JSON、WebSocket、SSE（服务器发送事件）、SSL、SQL、日志、Dropwizard Metrics、newrelic、健康检查、hystrix 等的支持。
Ratpack 默认使用 Guice 进行 DI（依赖注入）。

![../images/426440_2_En_15_Chapter/426440_2_En_15_Figa_HTML.gif](img/426440_2_En_15_Figa_HTML.gif)提示
Ratpack 是用 Java (8) 编写的，你可以用纯 Java 编写 Ratpack 应用程序，但我们这里重点介绍 Groovy 方面的内容。

脚本

在最简单的形式下，你可以仅使用一个 Groovy 脚本创建 Ratpack 应用程序。例如，以下脚本将运行一个简单的 Ratpack 应用程序，它始终响应“Hello World!”：

```
1   @Grab('io.ratpack:ratpack-groovy:1.6.1')

3   import static ratpack.groovy.Groovy.ratpack

5   ratpack {
6     handlers {
7       handler {
8         response.send "Hello World!"
9       }
10     }
11   }
```

Gradle

对于生产系统，你应该使用 Gradle 构建。Ratpack 有自己的 Gradle 插件，你可以按如下方式使用（`jcenter` 指的是 Maven 中央仓库的替代品，Bintray 的 JCenter）：

```
1   buildscript {
2     repositories {
3       jcenter()
4     }
5     dependencies {
6       classpath 'io.ratpack:ratpack-gradle:1.6.1'
7     }
8   }

10   apply plugin: 'io.ratpack.ratpack-groovy'

12   repositories {
13     jcenter()
14   }
```

使用 `ratpack-gradle` 插件，你可以运行诸如 `distZip`、`distTar`、`installApp` 和 `run` 等任务，这些任务分别用于创建 ZIP 分发文件、创建 TAR 分发文件、在本地安装 Ratpack 应用程序以及运行应用程序。

`run` 任务对于测试驱动你的应用程序非常有用。调用 `gradle run` 后，你应该会看到以下输出：

```
1   [main] INFO ratpack.server.RatpackServer - Starting server...
2   [main] INFO ratpack.server.RatpackServer - Building registry...
3   [main] INFO ratpack.server.RatpackServer - Ratpack started (development)
4   for http://localhost:5050
```

Ratpack 布局

与 Grails 不同，你需要自己创建这些文件和目录（或使用 Lazybones）。

*   `build.gradle`（Gradle 构建文件）

*   `src`

*   `main/groovy`——放置通用 Groovy 类的位置

*   `main/resources`——放置静态资源（如 Handlebars 模板）的位置

*   `ratpack`——包含 `Ratpack.groovy`（定义你的 Ratpack 处理器和绑定）以及 `ratpack.properties`

*   `ratpack/public`——任何公共文件，如 HTML、JavaScript、图片和 CSS

*   `ratpack/templates`——存放你的 Groovy 标记模板（如果有的话）

处理器

处理器是 Ratpack 的基本构建块。它们形成类似管道或“责任链”的结构。^(⁵⁸) 每个请求可以调用多个处理器，但必须有一个返回响应。如果没有匹配到任何处理器，默认处理器将返回 404 状态码。

```
1   import static ratpack.groovy.Groovy.ratpack

3   ratpack {
4     handlers {
5       all() { response.headers.add('x-custom', 'x'); next() }
6       get("foo") {
7           render "foo handler"
8       }
9       get(":key") {
10           def  key = pathTokens.key
11           render "{\"key\": \"$key\"}"
12       }
13       files { dir "public" }
14     }
15   }
```

第一个处理器 `all()` 用于每个 HTTP 请求，并添加一个自定义头部。然后它调用 `next()`，以便调用下一个匹配的处理器。下一个匹配给定 HTTP 请求的处理器将用于处理该请求并返回响应。
`pathTokens` 映射包含 URL 中传递的所有参数，这些参数在匹配路径模式中指定，例如 `:key`，通过在变量名前加 `:` 来指定。例如，如果运行上述应用程序，请求 `/abc` 将得到响应 `{"key":"abc"}`。

你可以使用与任何 HTTP 方法对应的方法来定义处理器：

*   `Get`

*   `Post`

*   `Put`

*   `Delete`

*   `Patch`

*   `Options`

要在同一路径上接受多种方法，你应该使用 `path` 处理器，并在其中使用 `byMethod` 配合每种方法处理器。例如：

```
1   path("foo") {
2       byMethod {
3           post() { render 'post foo'}
4           put() { render 'put foo'}
5           delete() { render 'delete foo'}
6       }
7   }
```

渲染

有多种方式可以渲染响应。最“Groovy”的方式是使用 `groovyMarkupTemplate` 或 `groovyTemplate` 方法。

例如，以下是使用 `groovyMarkupTemplate` 的方法：

```
1   import   ratpack.groovy.template.MarkupTemplateModule
2   import   static   ratpack.groovy.Groovy.groovyMarkupTemplate
3   import static ratpack.groovy.Groovy.ratpack
4   ratpack {
5     bindings {
6       module MarkupTemplateModule
7     }
8     handlers {
9       get(":key") {
10           def  key = pathTokens.key
11           render groovyMarkupTemplate("index.gtpl", title: "$key")
12       }
13       files { dir "public" }
14     }
15   }
```

这允许你使用 Groovy 标记语言，默认情况下，它会在 `ratpack/templates` 目录中查找标记文件。例如，你的 `index.gtpl` 可能如下所示：

1.  `yieldUnescaped` 将给定的字符串直接输出到生成的 HTML 中。

2.  Groovy 标记语言是一种基于 Groovy 的 DSL，用于从 Groovy 代码创建 HTML。

```
1   yieldUnescaped ''
2   html {
3     head {
4       meta(charset:'utf-8')
5       title("Ratpack: $title")
6       meta(name: 'apple-mobile-web-app-title', content: 'Ratpack')
7       meta(name: 'description', content: “)
8       meta(name: 'viewport', content: 'width=device-width, initial-scale=1')
9       link(href: '/images/favicon.ico', rel: 'shortcut icon')
10     }
11     body {
12       header {
13         h1 'Ratpack'
14         p 'Simple, lean & powerful HTTP apps'
15       }
16       section {
17         h2 title
18         p 'This is the main page for your Ratpack app.'
19       }
20     }
21   }
```

Groovy 文本

如果你更喜欢使用嵌入 Groovy 的纯文本文档（很像 GString），你可以使用 `TextTemplateModule`：

```
1   import ratpack.groovy.template.TextTemplateModule
2   import static ratpack.groovy.Groovy.groovyTemplate
3   import static ratpack.groovy.Groovy.ratpack
4   ratpack {
5     bindings {
6       module TextTemplateModule
7     }
8     handlers {
9       get(":key") {
10           def  key = pathTokens.key
11           render groovyTemplate("index.html", title: "$key")
12       }
13     }
14   }
```

然后在 `src/main/resources/templates` 目录中创建一个名为 `index.html` 的文件，内容如下：

```
1   ${model.title}
```

它会向你的模板提供一个 `model` 映射，其中包含你提供的所有参数。

Handlebars 和 Thymeleaf
Ratpack 还支持 Handlebars 和 Thymeleaf 模板，这是生成动态网页的两种替代方法。由于这些是静态资源，你应该将这些模板放在 `src/main/resources` 目录中。

你首先需要在 Gradle 构建文件中包含相应的 Ratpack 项目：

```
1   runtime 'io.ratpack:ratpack-handlebars:1.6.1'
2   runtime 'io.ratpack:ratpack-thymeleaf3:1.6.1'
```

要使用 Handlebars（一种替代模板格式），请包含 `HandlebarsModule` 并按如下方式渲染 Handlebars 模板：



```
1   import ratpack.handlebars.HandlebarsModule
2   import   static   ratpack.handlebars.Template.handlebarsTemplate
3   import static ratpack.groovy.Groovy.ratpack
4   ratpack {
5     bindings {
6       module HandlebarsModule
7     }
8     handlers {
9       get("foo") {
10           render handlebarsTemplate('myTemplate.html', title: 'Handlebars')
11       }
12     }
13   }
```

在 `src/main/resources/handlebars` 目录下创建一个名为 `myTemplate.html.hbs`（`.hbs` 是 Handlebars 的后缀）的文件，并包含 Handlebars 内容。例如：

```
1   {{title}}
```

Thymeleaf 的工作方式类似：

```
1   import ratpack.thymeleaf.ThymeleafModule
2   import   static   ratpack.thymeleaf.Template.thymeleafTemplate
3   import static ratpack.groovy.Groovy.ratpack
4   ratpack {
5     bindings {
6       module ThymeleafModule
7     }
8     handlers {
9       get("foo") {
10           render thymeleafTemplate('myTemplate', title: 'Thymeleaf')
11       }
12     }
13   }
```

请求的文件应命名为 `myTemplate.html`，并位于项目的 `src/main/resources/thymeleaf` 目录中。示例内容：

```

```

JSON
与 Jackson JSON 编组库的集成提供了处理 JSON 的能力。

`ratpack.jackson.Jackson` 类提供了大部分与 Jackson 相关的功能。例如，要渲染 JSON，可以使用 `Jackson.json`：

```
1   import  static  ratpack.jackson.Jackson.json
2   ratpack {
3     bindings {
4     }
5     handlers {
6       get("user") {
7         render json([user: 1])
8       }
9     }
10   }
```

Jackson 集成还包含一个用于将 JSON 请求体转换为对象的解析器。`Jackson.jsonNode()` 和 `Jackson.fromJson(Class)` 方法可用于创建可解析的对象，以便与 `parse()` 方法一起使用。

例如，以下处理器会解析一个表示 `Person` 对象的 JSON 字符串，并渲染该 `Person` 的姓名：

```
1   post("personNames") {
2     render( parse(fromJson(Person.class)).map {it.name} )
3   }
```

![../images/426440_2_En_15_Chapter/426440_2_En_15_Figb_HTML.gif](img/426440_2_En_15_Figb_HTML.gif)提示
`Context.parse` 返回一个 `ratpack.exec.Promise<T>`。`Promise<T>` 是并发编程中常用的一种模式，它允许使用简化的语法。它实现了诸如 `map` 等方法，如上所示。

绑定
Ratpack 中的绑定使处理器能够访问对象。如果你熟悉 Spring，Ratpack 使用一个*注册表*，它类似于 Spring 中的应用上下文。也可以将其视为从类类型到实例的简单映射。Ratpack-Groovy 默认使用 Guice，尽管也可以使用其他直接注入框架，例如 Spring（或者完全不使用）。
可重用的功能可以打包成模块，而不是使用正式的插件系统。你可以创建自己的模块，以正确解耦并将应用程序组织成组件。例如，你可能想创建一个 `MongoModule` 或 `JdbcModule`。或者，你也可以将应用程序拆分为服务。无论哪种方式，注册表都是放置它们的地方。

注册表中的任何内容都可以在处理器中自动使用，并通过类类型从注册表中进行注入。以下是绑定实际应用的示例：

```
1   bindings {
2     bindInstance(MongoModule, new MongoModule())
3     bind(DragonService, DefaultDragonService)
4   }
5   handlers {
6     get('dragons') { DragonService dService ->
7       dService.list().then { dragons ->
8         render(toJson(dragons))
9       }
10     }
11   }
```

当 `DragonService` 作为闭包参数（如 `dService`）被引用时，系统会自动获取在绑定中定义的 `DragonService`。

阻塞
阻塞操作应使用 `blocking` API 来处理。阻塞操作是指任何 IO 绑定的操作，例如查询数据库。

`Blocking` 类位于 `ratpack.exec.Blocking`。例如，以下处理器调用了数据库上的一个方法（该方法在此示例范围之外定义）：

```
1   get("deleteOlderThan/:days") {
2     int days = pathTokens.days as int
3     Blocking.get { database.deleteOlderThan(days) }
4             .then { int i -> render("$i records deleted") }
5   }
```

你可以使用 `then` 方法链式调用多个阻塞调用。前一个闭包的结果会作为参数传递给下一个闭包（在前面的处理器中是一个 `int`）。

![../images/426440_2_En_15_Chapter/426440_2_En_15_Figc_HTML.gif](img/426440_2_En_15_Figc_HTML.gif)提示
`Blocking.get` 返回一个 `ratpack.exec.Promise<T>`。

Ratpack 会为你处理线程调度，然后与原始计算线程汇合。这样，你就可以重新加入原始 HTTP 请求线程并返回结果。

```
1   get("deleteOlderThan/:days") {
2     int days = pathTokens.days as int
3     int result
4     Blocking.get { database.deleteOlderThan(days) }
5                  .then { int count -> result = count }
6     render("$result records deleted")
7   }
```

如果不需要返回值，请使用 `Blocking.exec` 方法。

配置
任何有自尊的 Web 应用程序都应允许配置来自多个位置：应用程序、文件系统、环境变量和系统属性。这通常是一种良好实践，尤其对于云原生应用而言。
Ratpack 包含一个内置的配置 API。`ratpack.config.ConfigData` 接口及其方法允许你分层配置多个来源。使用带有传递闭包的 `of` 方法，你可以为来自 JSON、YAML 和其他来源的配置定义一种工厂。

首先，定义你的配置类。这些类的属性将定义你的配置属性的名称。例如：

```
1   class Config {
2       DatabaseConfig database
3   }
4   class  DatabaseConfig  {
5       String username = "root"
6       String password = ""
7       String hostname = "localhost"
8       String database = "myDb"
9   }
```

在这种情况下，你的 JSON 配置可能如下所示：

```
1   {
2       "database": {
3           "username":  "user",
4           "password":  "changeme",
5           "hostname":  "myapp.dev.company.com"
6       }
7   }
```

稍后，在 `binding` 声明中，添加以下内容以绑定由先前类定义的配置：

```
1   def configData = ConfigData.of { d -> d.
2       json(getResource("/config.json")).
3       yaml(getResource("/config.yml")).
4       sysProps().
5       env().build()
6   }
7   bindInstance(configData.get(Config))
```

每个声明都会覆盖之前的声明。因此，在这种情况下，顺序是类定义、`config.json`、`config.yml`、系统属性，然后是环境变量。这样你就可以在运行时覆盖属性。
系统属性和环境变量配置必须以 `ratpack.` 和 `RATPACK_` 前缀开头。例如，`hostname` 属性作为系统属性将是 `ratpack.database.hostname`，作为环境变量将是 `RATPACK_DATABASE_HOSTNAME`。

测试
Ratpack 包含许多测试工具，用于辅助你的单元测试、功能测试和集成测试。它假设你将使用 Spock 来测试你的应用程序。

首先，如果你使用 Ratpack-Gradle 插件，只需添加以下依赖项：

```
1   dependencies {
2       testCompile ratpack.dependency('test')
3       testCompile "org.spockframework:spock-core:1.3-groovy-2.5"
4       testCompile 'cglib:cglib:3.2.12'
5       testCompile 'org.objenesis:objenesis:3.0.1'
6   }
```

`cglib` 和 `objenesis` 依赖项是用于类和 final 类模拟的。

其次，使用与主项目相同的包结构，在 `src/test/groovy` 目录下添加你的 Spock 测试。一个简单的测试可能如下所示：



```
1   package myapp.services
2   import spock.lang.Specification

4   class MyServiceSpec extends Specification {
5       void "默认服务应返回 Hello World"() {
6           setup:
7           "设置测试服务"
8           def service = new  MyService()
9           when:
10          "执行服务调用"
11          def result = service.doStuff()
12          then:
13          "确保服务调用返回了正确的结果"
14          result == "Hello World"
15          "当此功能完成时关闭服务"
16          service.shutdown()
17      }
18  }
```

Ratpack 通过在测试环境中使用 `GroovyRatpackMainApplicationUnderTest` 运行您的完整应用程序来实现功能测试。
例如，以下测试将测试默认处理器渲染的文本：

```
1   package myapp
2   import    ratpack.groovy.test.GroovyRatpackMainApplicationUnderTest
3   import spock.lang.Specification

5   class FunctionalSpec extends Specification {
6       void "默认处理器应渲染 Hello World"() {
7           setup:
8           def aut = new GroovyRatpackMainApplicationUnderTest()
9           when:
10          def response = aut.httpClient.text
11          then:
12          response == "Hello World!"
13          cleanup:
14          aut.close()
15      }
16  }
```

仅仅在 `httpClient` 上调用 `text` 就会发起一个 `GET` 请求。
然而，可以使用 `requestSpec` 来调用更复杂的请求。例如，测试基于 `User-Agent` 头部返回特定响应：

```
1   void "应为 v2.0 客户端正确渲染"() {
2       when:
3       def response = aut.httpClient.requestSpec { spec ->
4           spec.headers.'User-Agent' = ["Client v2.0"]
5       }.get("api").body.text
6       then:
7       response == "V2 Model"
8   }
```

总结

本章介绍了以下内容：

*   如何开始使用 Ratpack

*   使用处理器

*   如何将绑定用作插件架构以及用于解耦模块

*   如何使用 Groovy Markup、Groovy Text、Thymeleaf 和 Handlebars 模板进行渲染

*   如何渲染和解析 JSON

*   执行阻塞操作

*   配置 Ratpack 应用

*   测试 Ratpack 应用

![../images/426440_2_En_15_Chapter/426440_2_En_15_Figd_HTML.gif](img/426440_2_En_15_Figd_HTML.gif)信息
更多信息，请查看 Ratpack API。^(⁵⁹) 同时也可以参考 Dan Woods 所著的 *Learning Ratpack*^(⁶⁰)。

脚注

索引

A

抽象语法树 (AST) 转换

after 方法

afterView 方法

assets 目录

@AutoFinal

@AutoImplement

B

before 方法

布尔解析/Groovy 真值

breathFire 方法

构建框架

Build/images 目录

C

闭包

云计算框架

Caelyf

GAE

@CompileStatic 注解

ConfigSlurper

Curry 方法

D

@Delegate 注解

@DelegatesTo(SMS) 注解

委托

依赖注入 (DI)

deploymentPassword 变量

设计模式

委托

metaClass 方法

元编程

类别

元类

缺失方法

领域特定语言 (DSL)

闭包委托

命令链

扩展模块

methodMissing 方法

重写运算符

propertyMissing 方法

Dragon 类

E

eatDonuts() 方法

Elvis 运算符

Expando 类

F

fileTree 方法

findOrSave 方法

函数式编程 (FP)

闭包

一等特性

引用透明

序列操作

concat

filter

limit

map

reduction

G

Gant

getName() 方法

getText() 方法

GPars

Actor 设计模式

dependencies 块

并行 map/reduce

GPath

Gradle

构建脚本

依赖

ext 方法

file 方法

基于 Groovy 的 DSL

多项目构建

插件

属性

星点表示法

wrapper 任务

gradle.properties 文件

gradlew 文件

Grails

accept 头部

缓存插件

配置

grails-app/taglib 目录

历史

JsonSlurper

ORM 框架

概述

grails-app

基于 Groovy 的测试

一对多关系

src

结构

插件

测试

UrlMappings 类

Grails 2.0

date 方法

GORM

jQuery 插件

支持 Groovy 1.8

Grails 2.1

Grails 2.2

Grails 2.3

Grails 2.4

Grails 2.5

Grails 3

application 类

interceptor 方法

Grails 4

GrooCSS

Groovy

控制台

定义

动态类型

GDK

历史

安装

映射键值

元编程

属性

switch 语句

语法

Groovy 3.0

历史

运算符

Groovy 开发工具包 (GDK)

集合

GPath

Java 8+ 流

方法

展开运算符

IO File 类

IO URL

范围

工具类

ConfigSlurper

Expando

JsonBuilder

JsonSlurper

可观察的列表/映射/集合

Groovy 环境管理器 (GVM)

Groovy Fluent GDK

groovyMarkupTemplate/groovyTemplate 方法

Groovy shell

GString

Guesser 类

H

hostname 属性

I

不可变性

集成开发环境 (IDE)

J, K

Java

AST 转换

布尔解析/Groovy 真值

十进制数

默认方法值

泛型

映射语法

可选分号

括号

正则表达式语法

语法

@ToString 注解

@TupleConstructor

Java 开发工具包 (JDK)

L

Lazybones

最低上界 (LUB)

重载的 << 运算符

M, N, O

@MapConstructor

metaClass 方法

方法句柄

methodMissing 方法

模拟接口

MongoModule/JdbcModule

P, Q

路径处理器

propertyMissing 方法

R

Ratpack

绑定

阻塞操作

内置配置

DI

事件驱动网络引擎

Gradle 插件

groovyMarkupTemplate

Groovy 脚本

handlebars/thymeleaf 模板

处理器

JSON

布局

ratpack/templates 目录

测试夹具

TextTemplateModule

S

安全解引用运算符

软件开发工具包管理器 (SDKMAN)

Spock

数据列表/表格

重新创建测试

测试代码

测试框架

thrown 方法

展开运算符

T, U, V

尾递归优化

测试框架/代码分析

codenarc

并发

GPars

RxJava

spock

工具

编译

控制台

文档

shell

@ToString 注解

totalPopulation 方法

特质

@TupleConstructor

@TypeChecked 注解

W, X, Y, Z

Web/用户界面框架

grails

griffon

micronaut

ratpack

vert.x

```