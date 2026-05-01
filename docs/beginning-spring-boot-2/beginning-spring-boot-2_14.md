# 启动时运行的命令
CMD ["echo", "Dockerfile demo"]
列表 19-12.
示例 Dockerfile
```

该文件通过 `FROM` 命令使用基础镜像 `ubuntu:14.04`。你可以选择使用 `MAINTAINER` 命令提供作者详细信息。

接下来，代码使用 `RUN` 命令运行安装 `apache2` Web 服务器的指令，这些指令将在镜像构建期间执行。

最后，代码使用 `CMD` 命令运行带有参数 `Dockerfile demo` 的 `echo` 命令。你可以使用 `CMD` 命令指定启动容器时应执行的命令。在启动容器时，可以通过将命令作为参数传递给 `docker run` 命令来覆盖 `CMD` 命令。

一个 `Dockerfile` 中只能有一个 `CMD` 命令。即使指定了多个 `CMD` 命令，也只会考虑最后一个。

你可以使用 `docker build` 命令从 `Dockerfile` 构建镜像，并使用 `-t` 标志为其命名。在包含 `Dockerfile` 的同一目录下运行以下命令：

```
sudo docker build -t my-apache2.
```

注意命令末尾的点。这表示 `Dockerfile` 的位置，即当前目录。

你可以使用 `docker images` 命令列出本地所有可用的镜像。

现在，你可以使用 `docker run` 命令从 `my-apache2` 镜像启动一个容器，如下所示：

```
sudo docker run my-apache2
```

此命令应在控制台打印 `Dockerfile demo`。

你可以使用 `sudo docker ps` 命令显示正在运行的容器详细信息。

现在你已经基本了解了如何使用 Docker，在下一节中，你将看到如何将 Spring Boot 应用程序打包并在 Docker 容器中运行。

在 Docker 容器中运行 Spring Boot 应用程序

你将使用上一节中为在 Heroku 上部署 Spring Boot 而构建的应用程序。在项目的根目录下创建 `Dockerfile`，内容如下：

```
FROM java:8
ADD target/springboot-heroku-demo.jar app.jar
RUN bash -c 'touch /app.jar'
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/app.jar"]
```

此代码使用 `java:8` 作为基础镜像。它将 `target/springboot-heroku-demo.jar` 复制到目标镜像中，并命名为 `app.jar`。最后，它使用 `ENTRYPOINT` 调用 `java -jar app.jar` 命令。

在构建 Docker 镜像之前，你首先需要构建应用程序：

```
springboot-heroku-demo> mvn clean package
```

现在，你可以按如下方式运行 `docker build` 命令：

```
docker build -t sivaprasadreddy/springboot-heroku-demo .
```

在这里，你将镜像标记为 `sivaprasadreddy/springboot-heroku-demo`。由于你尚未激活任何配置文件，因此默认配置文件将处于活动状态，应用程序将使用 `H2` 内存数据库。

现在，从 `sivaprasadreddy/springboot-heroku-demo` 镜像启动容器，如下所示：

```
docker run -d \
--name springboot-heroku-demo \
-p 80:8080 \
sivaprasadreddy/springboot-heroku-demo
```

你正在运行容器，将其命名为 `springboot-heroku-demo`，并将容器的端口 `8080` 暴露在 Docker 主机的端口 `80` 上。

现在，你将看到如何在一个 Docker 容器中启动 Postgres 数据库，然后在另一个容器中启动应用程序，并使用第一个容器中的 Postgres 数据库。

你可以使用 `postgres` 镜像启动一个 Postgres 数据库容器，如下所示：

```
docker run --name demo-postgres \
-e POSTGRES_DB=demodb  \
-e POSTGRES_USER=postgres \
-e POSTGRES_PASSWORD=secret123 \
-d postgres
```

此代码使用 `-d` 标志以分离模式启动一个 `postgres` 容器，并将其命名为 `demo-postgres`。它还通过使用 `-e` 标志将数据库名称、用户名和密码指定为环境变量。

现在，你将创建 Docker 配置文件 `application-docker.properties`，内容如下：



```
spring.datasource.driver-class-name=org.postgresql.Driver
spring.datasource.url=jdbc:postgresql://${POSTGRES_PORT_5432_TCP_ADDR}:${POSTGRES_PORT_5432_TCP_PORT}/demodb
spring.datasource.username=${POSTGRES_ENV_POSTGRES_USER}
spring.datasource.password=${POSTGRES_ENV_POSTGRES_PASSWORD}
spring.jpa.hibernate.ddl-auto=update
```

你可以修改 `Dockerfile`，通过激活 `docker` 配置文件来运行应用程序：

```
FROM java:8
ADD target/springboot-heroku-demo.jar app.jar
RUN bash -c 'touch /app.jar'
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-Dspring.profiles.active=docker","-jar","/app.jar"]
```

请注意，代码中指定了 `-Dspring.profiles.active=docker` 来激活 `docker` 配置文件，这样应用程序将使用运行在另一个 Docker 容器中的 Postgres 数据库，而不是使用 `H2` 内存数据库。

现在使用 `mvn clean package` 命令构建项目，并构建你的应用程序 Docker 镜像。

```
docker build -t sivaprasadreddy/springboot-heroku-demo .
```

在启动容器之前，你需要删除现有的容器。可以按如下方式删除现有容器：

```
sudo docker rm springboot-heroku-demo
```

你需要将应用程序的 Docker 容器与 `demo-postgres` Docker 容器链接起来，以便能够从应用程序中使用 Postgres 数据库。

使用以下命令启动应用程序容器：

```
docker run -d \
--name springboot-heroku-demo \
--link demo-postgres:postgres \
-p 80:8080 \
sivaprasadreddy/springboot-heroku-demo
```

请注意，你使用 `--link` 标志链接了 `demo-postgres` 容器，并为其指定了一个别名 `postgres`。现在，应用程序在一个容器中运行，并与另一个容器中运行的 Postgres 数据库进行通信。

使用 docker-compose 运行多个容器

如果你的应用程序依赖于多个服务，并且你需要将它们全部启动在 Docker 容器中，那么逐个启动它们会很繁琐。你可以使用 `docker-compose` 工具来编排运行应用程序所需的多个容器。你需要创建一个 `docker-compose.yml` 文件，并配置你想要运行的服务。

在应用程序的根目录下创建 `docker-compose.yml`，如清单 19-13 所示。

```
demo-postgres:
image: postgres:latest
environment:
- POSTGRES_DB=demodb
- POSTGRES_USER=postgres
- POSTGRES_PASSWORD=secret123
springboot-heroku-demo:
image: sivaprasadreddy/springboot-heroku-demo
links:
- demo-postgres:postgres
ports:
- 80:8080
清单 19-13.
docker-compose.yml
```

现在，你只需在存放 `docker-compose.yml` 文件的目录下运行 `docker-compose up` 命令，即可启动应用程序和 Postgres 容器。

注意

你可以使用 `docker-maven-plugin` 来构建 Docker 镜像。访问 [`https://github.com/spotify/docker-maven-plugin`](https://github.com/spotify/docker-maven-plugin) 以了解更多关于 `docker-maven-plugin` 的信息。如果你使用的是 Gradle 构建工具，可以使用 `gradle-docker` 插件（ [`https://github.com/Transmode/gradle-docker`](https://github.com/Transmode/gradle-docker) ）来构建 Docker 镜像。

要了解更多关于 Docker 的信息，请访问 [`https://docs.docker.com/`](https://docs.docker.com/) 。

总结

本章讨论了如何在生产环境中运行应用程序，以及如何在 Heroku 云平台上部署 Spring Boot 应用程序。你还学习了如何在 Docker 容器上运行 Spring Boot 应用程序。

索引

A

AppConfig.java

AutoConfiguration 类

B

BCryptPasswordEncoder.encode(plan_tx_password) 方法

C

configure(HttpSecurity http) 方法

跨域资源共享 (CORS)

类级别和方法级别

forgetUsers() 方法

RepositoryRestConfigurerAdapter

WebMvcConfigurer.addCorsMappings() 方法

跨站请求伪造 (CSRF)

D, E

dbcUserDAO

deleteUser() 方法

deleteUserUnauthenticated() 测试方法

F, G, H

FilterRegistrationBean

Flyway

完全限定名 (FQN)

I

index.html

J, K

JavaMelody 的 MonitoringFilter



Java 面向对象查询（JOOQ）

代码生成

数据库模式

删除评论

领域对象

DSL API

jooq-config.xml 文件

JUnit 测试

Maven Codegen 插件

Post 记录

PostService 方法

SpringbootJooqDemoApplication.java

spring-boot-starter-jooq 依赖

src/main/resources/data.sql 脚本

Java 持久化 API（JPA）

Spring Boot

演示应用程序

动态查询方法

实体

findAll(Sort sort)方法

多数据库

OpenEntityManagerInViewFilter Bean

OrderRepository

OrdersDBConfig.java

仓库接口

SecurityDBConfig.java

SpringbootMultipleDSDemoApplicationTests.java

Spring Data

Java 服务器页面（JSP）

Java 虚拟机（JVM）

Groovy

JavaBean

循环结构

Spring Boot 应用程序

字符串

Kotlin

类

数据类

Hello World 程序

接口

Spring Boot 应用程序

Scala

类和对象

Spring Boot 应用程序

特质

类型推断

JDBC 模板

数据库

数据库迁移

HikariCP 依赖

NoSQL 数据

org.apache.tomcat.jdbc.pool.DataSource

tomcat-jdbc-{version}.jar

不使用 SpringBoot

使用 Spring Boot

JHipster

创建

实体子生成器

JDL Studio

前提条件

关系

Yeoman

JHipster 领域语言（JDL）

L

LogoutSuccessHandler

M, N

方法级安全

模型-视图-控制器（MVC）设计

MongoDB

集合

安装

在 Linux 上

在 MacOS 上

在 Windows 上

MongoDB Shell 版本

Spring Data

UserRepository 接口

使用 JavaConfig

使用 MongoTemplate

测试目的

MongoUserDAO

MyBatis

SpringBoot 应用程序

SpringbootMyBatisDemoApplication.java

src/main/resources/com/apress/demo/mappers/UserMapper.xml

UserMapper.java 接口

UserMapper 方法

MySQL 数据库

O

对象关系映射（ORM）

P, Q

持久化令牌方法

R

响应式编程

框架/库

Project Reactor

响应式流

spring-webflux

注解

应用程序

函数式编程模型

HandlerFunction

Netty 和 Undertow

RouterFunction

ServerHttpRequest 和 ServerHttpResponse

Thymeleaf

WebClient

表述性状态转移（REST API）

AuthenticationEntryPoint

AuthenticationFailureHandler

AuthenticationSuccessHandler

集合资源

CORS

类级别和方法级别

forgetUsers()方法

RepositoryRestConfigurerAdapter

WebMvcConfigurer.addCorsMappings()方法

createPost()处理方法

CRUD 操作

端点

异常处理

Java Bean 验证 API

JPA 实体

JPA 实体

JPA 仓库

@JsonBackReference 注解

@JsonIgnore 注解

@JsonManagedReference 注解

listPosts()方法

LogoutSuccessHandler

PagingAndSortingRepository

@RequestBody 注解

ResourceNotFoundException.java

ResponseEntity

SpringMVC

Spring Security 配置

SQL 脚本

StackOverflowError

角色 JPA 实体

S

安全应用程序

CSRF

方法级安全

记住我功能

持久化令牌

简单基于哈希的令牌

REST API

AuthenticationEntryPoint

AuthenticationFailureHandler

AuthenticationSuccessHandler

LogoutSuccessHandler

Spring Security 配置

在 Spring Boot Web 应用程序中

AccessDenied 视图

application.properties

BCryptPasswordEncoder.encode(plan_tx_password)方法

configure(HttpSecurity http)方法

角色 JPA 实体

SecurityAutoConfiguration

Spring Data JPA 仓库接口 UserRepository.java

Spring WebMVC 配置

Thymeleaf Adminhome 视图

thymeleaf-extras-springsecurity4 模块

Thymeleaf Home 视图

Thymeleaf Login 视图

UserDetailsService

UserDetailsService 实现

用户 JPA 实体

用户和角色数据库表

WebSecurityConfigurerAdapter

SecurityContextHolder

ServerResponse 构建器方法

ServletListenerRegistrationBean

ServletRegistrationBean

SessionListener

Spring Boot

执行器

/actuator 端点

AppConfig.java 配置类

应用程序

Intellij IDEA

NetBeans IDE

spring initializr

STS

application.properties

/autoconfig 端点

自动配置

/beans 端点

Bean 验证 API

com.apress.demo.SpringbootBasicApplication.java

/configprops

配置管理

CORS 支持

自定义

Data-JPA 和 Actuator 启动器

数据源/JPA 属性

data.sql

data.sql 文件

默认设置

依赖管理

开发者工具

DispatcherServlet url-pattern

/dump 端点

易于使用的嵌入式 Servlet 容器支持

嵌入式 Servlet 容器支持

入口点类 Application.java 文件

/env 端点

外部化配置属性

Fat JAR

文件上传表单

gradle

/health 端点

HealthIndicator 实现

HomeController.java

index.html

/info 端点

JavaConfig

JMX MBeans

JSP

表单验证

Thymeleaf

UserValidator

用户列表

log4j.properties 文件

/loggers 端点

日志

logging.file

/mappings 端点

指标

/metrics 端点

MILESTONE 或 SNAPSHOT 版本

MVC Web 层 Bean

非根包

pom.xml 文件

根包

宽松绑定

ResourceBundles

Hibernate Validator

i18n 消息

安全

Servlet 容器

/shutdown 端点

Spring 配置风格

Spring 框架

SpringMVC 控制器

SpringWebAppInitializer.java 配置类

src/main/public/index.html

启动器模块

Tomcat 嵌入式容器

/trace 端点

类型安全的配置属性

User.java

UserRepository.java

Web 应用程序

Jetty

WAR 类型打包

WebMvcConfig.java 配置类

Spring Boot 应用程序

开发和生产环境配置文件

Docker

docker 配置文件

ENTRYPOINT

安装文档

多个容器

postgres 镜像

术语

Heroku

SpringApplication

Spring Boot 自动配置

ApplicationContext

内置@conditional 注解

@Conditional

DataSource

DataSourceAutoConfiguration

@EnableAutoConfiguration 注解

@EnableConfigurationProperties

Java 类

生产数据库

配置文件

属性配置

Spring Bean

spring-boot-autoconfigure-{version}.jar

spring.datasource

系统属性

Spring Boot Starter

Twitter4j

@Conditional 特性

配置参数

自定义创建

REST API

Twitter4jAutoConfiguration

twitter4j-spring-boot-autoconfigure

twitter4j-spring-boot-starter

SpringMVC

配置

错误处理

流程

Spring Tool Suite (STS)

Swagger 文档

T

测试驱动开发（TDD）

@After 方法

@DataJpaTest 注解

@JdbcTest 注解

模拟实现

受保护的控制器/服务方法

Spring Boot 应用程序

Spring MVC 控制器

UserDetailsService

@WebMvcTest 注解

@WithMockUser 注解

Thymeleaf Home 视图

U, V

UserDetailsService

用户 JPA 实体

UserMapper.java

userService.createUser()方法

userService.deleteUser()方法

usersPage

W, X

WebSecurityConfigurerAdapter

Y, Z

MySQLDatabaseTypeCondition

```