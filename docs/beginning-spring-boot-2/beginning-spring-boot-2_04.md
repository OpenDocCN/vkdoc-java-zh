# 4. Spring Boot 要点

Spring Boot 的主要目标是简化基于 Spring 的应用程序的开发。Spring Boot 提供了多种特性，以便更轻松地实现常用功能，例如日志记录和外部化配置属性。本章涵盖配置日志、外部化配置属性以及配置特定于 Profile 的属性。然后，它将探讨如何使用 Spring Boot 开发者工具在代码更改时自动重启服务器，从而提高开发人员生产力。

## 日志记录

日志记录是任何应用程序中非常重要的一部分，它有助于调试问题。默认情况下，Spring Boot 将 `spring-boot-starter-logging` 作为 `spring-boot-starter` 模块的传递依赖包含在内。默认情况下，Spring Boot 包含 SLF4J 以及 Logback 实现。Spring Boot 有一个 `LoggingSystem` 抽象，它会根据类路径中可用的日志配置文件自动配置日志记录。

如果 Logback 可用，Spring Boot 将选择它作为日志处理器。你可以在 `application.properties` 文件中轻松配置日志级别，而无需创建特定于日志提供程序的配置文件，例如 `logback.xml` 或 `log4j.properties`。

```
logging.level.org.springframework.web=INFO
logging.level.org.hibernate=ERROR
logging.level.com.apress=DEBUG
```

如果你想将日志数据记录到文件以及控制台，请按如下方式指定文件名：

```
logging.path=/var/logs/app.log
```

或

```
logging.file=myapp.log
```

如果你想对日志配置有更多控制，可以在其默认位置创建特定于日志提供程序的配置文件，Spring Boot 将自动使用它们。

例如，如果你将 `logback.xml` 文件放在根类路径中，Spring Boot 将自动使用它来配置日志系统。参见清单 4-1。

```

%d{HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %msg%n

app.log

%date %level [%thread] %logger{10} [%file:%line] %msg%n 

清单 4-1.
logback.xml 文件
```

如果你想使用其他日志库（例如 Log4J 或 Log4j2）而不是 Logback，你可以排除 `spring-boot-starter-logging` 并包含相应的日志启动器，如下所示：

```
org.springframework.boot
spring-boot-starter

org.springframework.boot
spring-boot-starter-logging

org.springframework.boot
spring-boot-starter-log4j

```

现在，你可以将 `log4j.properties` 文件添加到根类路径，Spring Boot 将自动使用它进行日志记录。



## 外部化配置属性

通常，您会希望将配置参数外部化到单独的 properties 或 XML 文件中，而不是将它们硬编码在代码里，这样您就可以根据应用程序的环境轻松更改它们。Spring 提供了 `@PropertySource` 注解来指定配置文件列表。

Spring Boot 更进一步，默认会在根类路径下使用 `application.properties` 文件自动注册一个 `PropertyPlaceHolderConfigurer` bean。您还可以使用 `application-{profile}.properties` 这样的文件名来创建特定于环境的配置文件。

例如，您可以有一个包含默认属性值的 `application.properties`，一个包含 `dev` 环境配置的 `application-dev.properties`，以及一个包含 `production` 环境配置值的 `application-prod.properties`。如果您想配置所有环境通用的属性，可以在 `application-default.properties` 中进行配置。

注意

您也可以使用 YAML (`.yml`) 文件作为 `.properties` 的替代方案。请参阅 Spring Boot 参考文档中的“使用 YAML 替代 Properties”部分，网址为：[`http://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#boot-features-external-config-yaml`](http://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#boot-features-external-config-yaml) 。

### 类型安全的配置属性

Spring 提供了 `@Value` 注解来将任何属性值绑定到 bean 的属性上。假设您有以下 `application.properties` 文件：

```
jdbc.driver=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/test
jdbc.username=root
jdbc.password=secret
```

您可以使用 `@Value` 将这些属性值绑定到 bean 的属性上，如下所示：

```
@Configuration
public class AppConfig
{
@Value("${jdbc.driver}")
private String driver;
@Value("${jdbc.url}")
private String url;
@Value("${jdbc.username}")
private String username;
@Value("${jdbc.password}")
private String password;
...
...
}
```

但是，使用 `@Value` 逐个绑定每个属性是一个繁琐的过程。因此，Spring Boot 引入了一种机制，可以以类型安全的方式自动将一组属性绑定到 bean 的属性上。

假设您有之前的 JDBC 参数和一个如下的 `DataSourceConfig` 类：

```
public class DataSourceConfig
{
private String driver;
private String url;
private String username;
private String password;
//setters and getters
}
```

现在，您只需用 `@ConfigurationProperties(prefix=`"`jdbc`"`)` 注解 `DataSourceConfig`，即可自动绑定以 `jdbc.*` 开头的属性。

```
@Component
@ConfigurationProperties(prefix="jdbc")
public class DataSourceConfig
{
...
...
}
```

现在，您可以将 `DataSourceConfig` bean 注入到其他 Spring bean 中，并通过 getter 方法访问这些属性。

### 宽松绑定

Bean 的属性名称不必与属性键名完全一致。Spring Boot 支持宽松绑定，其中 bean 属性 `driverClassName` 将映射自以下任意一个：`driverClassName`、`driver-class-name` 或 `DRIVER_CLASS_NAME`。

### 使用 Bean Validation API 验证属性

您可以使用 Bean Validation API 注解（例如 `@NotNull`、`@Min`、`@Max` 等）来验证属性的值。

```
@Component
@ConfigurationProperties(prefix="support")
public class Support
{
@NotNull
private String applicationName;
@NotNull
@Email
private String email;
@Min(1) @Max(5)
private Integer severity;
//setters and getters
}
```

如果您配置的任何属性值根据配置的验证注解被判定为无效，则在应用程序启动时会抛出异常。有关外部化属性的更多详细信息，请参阅“外部化配置”，网址为：[`http://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#boot-features-external-config`](http://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#boot-features-external-config) 。



## 开发者工具

在开发过程中，你可能需要频繁修改代码，并重启服务器以使这些代码更改生效。Spring Boot 提供了开发者工具（`spring-boot-devtools` 模块），该模块支持在应用程序类路径内容发生变化时快速重启应用。

当你在开发阶段引入 `spring-boot-devtools` 模块时，视图模板（Thymeleaf、Velocity 和 Freemarker 等）的缓存将被自动禁用，以便你能立即看到更改效果。你可以在 `org.springframework.boot.devtools.env.DevToolsPropertyDefaultsPostProcessor` 中查看已配置的属性列表。

```
org.springframework.boot
spring-boot-devtools
true

```

请注意，这里通过使用 `<optional>true</optional>` 将 `spring-boot-devtools` 指定为可选依赖，这样它就不会被打包到 fat JAR 中。

Spring Boot 开发者工具会在类路径内容发生任何更改时自动触发应用程序重启。

注意

在 Eclipse 或 STS 中，只要你更改类路径资源并保存，devtools 就会重启服务器。在 IntelliJ IDEA 中，你需要运行“构建项目”来触发重启。

每当你更改类路径中的类或属性文件时，Spring Boot 会自动重启服务器。当静态内容（如 CSS、JS 和 HTML）发生更改时，你通常不需要重启服务器。因此，默认情况下，Spring Boot 会将这些静态资源位置排除在文件更改监视列表之外。

```
@ConfigurationProperties(prefix = "spring.devtools")
public class DevToolsProperties {
....
....
public static class Restart {
private static final String DEFAULT_RESTART_EXCLUDES =
"META-INF/maven/**,"
+ "META-INF/resources/**,resources/**,"
+ "static/**,public/**,templates/**,"
+ "**/*Test.class,**/*Tests.class,git.properties, META-INF/build-info.properties ";
/**
* 应排除在触发完全重启之外的模式。
*/
private String exclude = DEFAULT_RESTART_EXCLUDES;
....
....
}
}
```

你可以通过配置 `spring.devtools.restart.exclude` 属性来覆盖此默认排除列表。

```
spring.devtools.restart.exclude=assets/**,resources/**
```

如果你想将位置添加到重启排除/包含路径中，请使用以下属性。

```
spring.devtools.restart.additional-exclude=assets/**,setup-instructions/**
spring.devtools.restart.additional-paths=D:/global-overrides/
```

Spring Boot 的重启机制通过在代码更改后自动重启服务器，有助于提高开发人员的工作效率。但有时，你可能需要更改多个类来实现某个功能，如果每次文件更改后服务器都不断重启，那将会很烦人。在这种情况下，你可以使用 `spring.devtools.restart.trigger-file` 属性来配置一个用于监视更改的文件路径。这样，服务器将仅在 `trigger-file` 发生更改时重启。

```
spring.devtools.restart.trigger-file=restart.txt
```

注意

一旦你配置了 `spring.devtools.restart.trigger-file` 并更新了触发文件，服务器将仅在被监视的文件发生修改时重启。否则，服务器不会重启。

重启机制通过使用两个类加载器来工作——一个（基类加载器）用于加载不会更改的类（例如第三方 jar 中的类），另一个（重启类加载器）用于加载频繁更改的类（例如来自应用程序代码的类）。当应用程序重启时，只有重启类加载器中的类会被重新创建。这将实现更快的重启。

你可以通过设置 `spring.devtools.restart.enabled = false` 属性来禁用重启。这仍会使用两个类加载器，但重启类加载器不会监视文件更改。如果你想完全关闭重启机制，请将 `spring.devtools.restart.enabled=false` 属性设置为系统属性，如下所示。

```
java -jar -Dspring.devtools.restart.enabled=false app.jar
```

如果你正在开发多个 Spring Boot 应用程序，并希望将相同的 devtools 设置应用于所有应用程序，你可以通过在 `HOME` 目录中创建一个 `.spring-bootdevtools.properties` 文件来配置全局设置。

在 Windows 操作系统上，路径为 `C:\Users\<username>\.spring-boot-devtools.properties`；在 Linux/MacOS 上，路径为 `/home/<username>/.spring-boot-devtools.properties`。

你可以在 [`http://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#using-boot-devtools`](http://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#using-boot-devtools) 阅读更多关于 Spring Boot 开发者工具的信息。

## 总结

本章介绍了一些有助于提高开发人员工作效率的 Spring Boot 酷炫特性。下一章将介绍如何使用 Spring Boot 处理关系型数据库。

