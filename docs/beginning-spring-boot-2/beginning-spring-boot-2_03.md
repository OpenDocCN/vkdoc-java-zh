# 3. Spring Boot 自动配置

Spring Boot 的自动配置机制严重依赖于 `@Conditional` 特性。本章将探讨如何通过使用 `@Conditional` 注解有条件地注册 Spring Bean，并创建满足特定条件的各种类型的 `Conditional` 实现。然后，你将深入了解 Spring Boot 如何利用 `@Conditional` 特性，根据特定条件自动配置 Bean。

## 探索 @Conditional 的强大功能

在开发基于 Spring 的应用程序时，你可能会遇到需要有条件地注册 Bean 的情况。例如，你可能希望在本地运行应用程序时注册一个指向 `DEV` 数据库的 `DataSource` Bean，而在生产环境中运行时指向一个不同的 `PRODUCTION` 数据库。

你可以将数据库连接参数外部化到属性文件中，并使用适合当前环境的文件。但是，每当需要指向不同的环境时，你都必须更改配置并重新部署应用程序。

为了解决这个问题，Spring 3.1 引入了 profile 的概念。你可以注册多个相同类型的 Bean，并将它们与一个或多个 profile 关联起来。当运行应用程序时，你可以激活所需的 profile。这样，只有与已激活 profile 关联的 Bean 才会被注册。

```
@Configuration
public class AppConfig
{
@Bean
@Profile("DEV")
public DataSource devDataSource() {
...
}
@Bean
@Profile("PROD")
public DataSource prodDataSource() {
...
}
}
```

通过这种配置，你可以使用 `-Dspring.profiles.active=DEV` 系统属性来指定激活的 profile。这种方法适用于简单的情况，例如根据激活的 profile 来启用或禁用 Bean 注册。但是，如果你想基于某些条件逻辑来注册 Bean，仅靠 profile 方法是不够的。

为了提供更大的灵活性来有条件地注册 Spring Bean，Spring 4 引入了 `@Conditional` 的概念。使用 `@Conditional` 方法，你可以基于任意条件来有条件地注册 Bean。

例如，你可能希望在以下情况下注册一个 Bean：

*   类路径中存在特定的类
*   `ApplicationContext` 中尚未注册某种类型的 Spring Bean
*   某个位置存在特定的文件
*   配置文件中配置了特定的属性值
*   存在/不存在特定的系统属性

这些只是几个例子，你可以设置任何你需要的条件。下一节将介绍 Spring 的 `@Conditional` 是如何工作的。

### 基于系统属性使用 @Conditional

假设你有一个 `UserDAO` 接口，其中包含从数据存储中获取数据的方法。你有两个 `UserDAO` 接口的实现——`JdbcUserDAO` 与 MySQL 数据库交互，`MongoUserDAO` 与 MongoDB 交互。你可能希望根据特定的系统属性（例如 `dbType`）仅启用 `JdbcUserDAO` 或 `MongoUserDAO`。

如果应用程序使用 `java -jar myapp.jar -DdbType=MySQL` 启动，那么你希望启用 `JdbcUserDAO`；否则，如果应用程序使用 `java -jar myapp.jar -DdbType=MONGO` 启动，你希望启用 `MongoUserDAO`。

假设你有 `UserDAO` 接口以及 `JdbcUserDAO` 和 `MongoUserDAO` 的实现，如清单 3-1 所示。

```
public interface UserDAO
{
List getAllUserNames();
}
public class JdbcUserDAO implements UserDAO
{
@Override
public List getAllUserNames()
{
System.out.println("**** Getting usernames from RDBMS *****");
return Arrays.asList("Jim","John","Rob");
}
}
public class MongoUserDAO implements UserDAO
{
@Override
public List getAllUserNames()
{
System.out.println("**** Getting usernames from MongoDB *****");
return Arrays.asList("Bond","James","Bond");
}
}
清单 3-1.
UserDAO 接口以及 JdbcUserDAO 和 MongoUserDAO 实现
```

你可以实现 `MySQLDatabaseTypeCondition` 条件来检查 `dbType` 系统属性是否为 `MYSQL`，如清单 3-2 所示。

```
public class MySQLDatabaseTypeCondition implements Condition
{
@Override
public boolean matches(ConditionContext conditionContext, AnnotatedTypeMetadata metadata)
{
String enabledDBType = System.getProperty("dbType");
return (enabledDBType != null &&
enabledDBType.equalsIgnoreCase("MYSQL"));
}
}
清单 3-2.
MySQLDatabaseTypeCondition.java
```

你可以实现 `MongoDBDatabaseTypeCondition` 条件来检查 `dbType` 系统属性是否为 `MONGODB`，如清单 3-3 所示。

```
public class MongoDBDatabaseTypeCondition implements Condition
{
@Override
public boolean matches(ConditionContext conditionContext, AnnotatedTypeMetadata metadata)
{
String enabledDBType = System.getProperty("dbType");
return (enabledDBType != null && enabledDBType.equalsIgnoreCase("MONGODB"));
}
}
清单 3-3.
MongoDBDatabaseTypeCondition.java
```

现在，你可以使用 `@Conditional` 有条件地配置 `JdbcUserDAO` 和 `MongoUserDAO` Bean，如清单 3-4 所示。

```
@Configuration
public class AppConfig
{
@Bean
@Conditional(MySQLDatabaseTypeCondition.class)
public UserDAO jdbcUserDAO(){
return new JdbcUserDAO();
}
@Bean
@Conditional(MongoDBDatabaseTypeCondition.class)
public UserDAO mongoUserDAO(){
return new MongoUserDAO();
}
}
清单 3-4.
AppConfig.java
```

如果你运行应用程序，例如 `java -jar myapp.jar -DdbType=MYSQL`，则只会注册 `JdbcUserDAO` Bean。但是，如果你将系统属性设置为 `-DdbType=MONGODB`，则会注册 `MongoUserDAO` Bean。这就是如何基于系统属性有条件地注册 Bean 的方法。



### 基于 Java 类的存在/缺失使用 @Conditional

假设你希望仅在类路径中存在名为 `com.mongodb.Server` 的 `MongoDB` Java 驱动类时，才注册 `MongoUserDAO` bean。否则，你希望注册 `JdbcUserDAO` bean。

为此，你可以创建条件来检查名为 `com.mongodb.Server` 的 MongoDB 驱动类是否存在或缺失，如清单 3-5 所示。

```
public class MongoDriverPresentsCondition implements Condition
{
@Override
public boolean matches(ConditionContext conditionContext, AnnotatedTypeMetadata metadata)
{
try {
Class.forName("com.mongodb.Server");
return true;
} catch (ClassNotFoundException e) {
return false;
}
}
}
public class MongoDriverNotPresentsCondition implements Condition
{
@Override
public boolean matches(ConditionContext conditionContext, AnnotatedTypeMetadata metadata)
{
try {
Class.forName("com.mongodb.Server");
return false;
} catch (ClassNotFoundException e) {
return true;
}
}
}
清单 3-5.
MongoDriverPresentsCondition.java 和 MongoDriverNotPresentsCondition.java
```

这就是如何根据类路径中某个类的存在或缺失来有条件地注册 bean。

### 基于已配置的 Spring Bean 使用 @Conditional

如果你希望仅当尚未注册其他类型为 `UserDAO` 的 Spring bean 时，才注册 `MongoUserDAO` bean，该怎么办？

你可以创建一个条件来检查是否已存在某种类型的 bean，如清单 3-6 所示。

```
public class UserDAOBeanNotPresentsCondition implements Condition
{
@Override
public boolean matches(ConditionContext conditionContext, AnnotatedTypeMetadata metadata)
{
UserDAO userDAO = conditionContext.getBeanFactory().getBean(UserDAO.class);
return (userDAO == null);
}
}
清单 3-6.
UserDAOBeanNotPresentsCondition.java
```

### 基于属性配置使用 @Conditional

如果你希望仅当属性占位符配置文件中设置了 `app.dbType=MONGO` 属性时，才注册 `MongoUserDAO` bean，该怎么办？你可以实现该条件，如清单 3-7 所示。

```
public class MongoDbTypePropertyCondition implements Condition
{
@Override
public boolean matches(ConditionContext conditionContext, AnnotatedTypeMetadata metadata)
{
String dbType = conditionContext.getEnvironment().getProperty("app.dbType");
return "MONGO".equalsIgnoreCase(dbType);
}
}
清单 3-7.
MongoDbTypePropertyCondition.java
```

你已经了解了如何实现各种类型的条件。但还有一种更优雅的方式，即使用注解来实现条件。

无需为 MYSQL 和 MongoDB 分别创建条件实现，你可以创建一个 `DatabaseType` 注解，如下所示：

```
@Target({ ElementType.TYPE, ElementType.METHOD })
@Retention(RetentionPolicy.RUNTIME)
@Conditional(DatabaseTypeCondition.class)
public @interface DatabaseType
{
String value();
}
```

然后，你实现 `DatabaseTypeCondition`，以使用 `DatabaseType` 的值来决定是启用还是禁用 bean 注册，如下所示：

```
public class DatabaseTypeCondition implements Condition
{
@Override
public boolean matches(ConditionContext conditionContext, AnnotatedTypeMetadata metadata)
{
Map attributes = metadata.getAnnotationAttributes(DatabaseType.class.getName());
String type = (String) attributes.get("value");
String enabledDBType = System.getProperty("dbType","MYSQL");
return (enabledDBType != null && type != null && enabledDBType.equalsIgnoreCase(type));
}
}
```

现在，你可以在 bean 定义上使用 `@DatabaseType` 注解，如下所示：

```
@Configuration
@ComponentScan
public class AppConfig
{
@DatabaseType("MYSQL")
public UserDAO jdbcUserDAO(){
return new JdbcUserDAO();
}
@Bean
@DatabaseType("MONGO")
public UserDAO mongoUserDAO(){
return new MongoUserDAO();
}
}
```

在这里，你从 `DatabaseType` 注解获取元数据，并与 `dbType` 系统属性值进行比对，以决定是启用还是禁用 bean 注册。

你已经看到了大量关于如何使用 `@Conditional` 注解有条件地注册 bean 的示例。Spring Boot 广泛使用 `@Conditional` 特性，根据各种标准有条件地注册 bean。

## Spring Boot 内置的 @Conditional 注解

Spring Boot 提供了许多自定义的 `@Conditional` 注解，以满足开发者基于各种标准的自动配置需求。表 3-1 列出了 Spring Boot 开箱即用的 `@Conditional` 注解。

表 3-1.

Spring Boot @Conditional 注解

| 注解 | 描述 |
| --- | --- |
| `@ConditionalOnBean` | 当指定的 bean 类和/或名称已注册时匹配。 |
| `@ConditionalOnMissingBean` | 当指定的 bean 类和/或名称尚未注册时匹配。 |
| `@ConditionalOnClass` | 当指定的类在类路径上时匹配。 |
| `@ConditionalOnMissingClass` | 当指定的类不在类路径上时匹配。 |
| `@ConditionalOnProperty` | 当指定的属性具有特定值时匹配。 |
| `@ConditionalOnResource` | 当指定的资源在类路径上时匹配。 |
| `@ConditionalOnWebApplication` | 当应用程序上下文是 Web 应用程序上下文时匹配。 |

Spring Boot 为这些注解提供了实现，以验证条件是否匹配。例如，查看 Spring Boot 源码中 `@ConditionalOnClass` 注解和 `OnClassCondition.java` 的源代码。

```
@Target({ ElementType.TYPE, ElementType.METHOD })
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Conditional(OnClassCondition.class)
public @interface ConditionalOnClass {
/**
* 必须存在的类。由于此注解通过加载类字节码进行解析，
* 因此在此处指定最终可能不在类路径上的类是安全的。
* @return 必须存在的类
*/
Class[] value() default {};
/**
* 必须存在的类名。
* @return 必须存在的类名
*/
String[] name() default {};
}
@Order(Ordered.HIGHEST_PRECEDENCE)
class OnClassCondition extends SpringBootCondition
implements AutoConfigurationImportFilter, BeanFactoryAware, BeanClassLoaderAware {
private BeanFactory beanFactory;
private ClassLoader beanClassLoader;
@Override
public boolean[] match(String[] autoConfigurationClasses,
AutoConfigurationMetadata autoConfigurationMetadata) {
ConditionEvaluationReport report = getConditionEvaluationReport();
ConditionOutcome[] outcomes = getOutcomes(autoConfigurationClasses,
autoConfigurationMetadata);
boolean[] match = new boolean[outcomes.length];
for (int i = 0; i < outcomes.length; i++) {
match[i] = (outcomes[i] == null || outcomes[i].isMatch());
if (!match[i] && outcomes[i] != null) {
logOutcome(autoConfigurationClasses[i], outcomes[i]);
if (report != null) {
report.recordConditionEvaluation(autoConfigurationClasses[i], this,
outcomes[i]);
}
}
}
return match;
}
...
...
...
}
```

你可以看到 Spring Boot 如何使用 `@ConditionalOnClass` 注解和 `OnClassCondition.class` 来检查给定的类是否存在。类似地，你可以在 Spring Boot 中找到其他各种条件注解，例如 `@ConditionalOnBean`、`@ConditionalOnMissingBean`、`@ConditionalOnResource`、`@ConditionalOnProperty` 等。

注意

你可以在 `spring-boot-autoconfigure-{version}.jar` 的 `org.springframework.boot.autoconfigure.condition` 包中找到 Spring Boot 使用的各种条件实现。

既然你已经了解了 Spring Boot 如何使用 `@Conditional` 特性来有条件地检查是否注册 bean，你可能会想，究竟是什么触发了自动配置机制。这正是下一节要介绍的内容。



## Spring Boot 自动配置的工作原理

Spring Boot 自动配置的关键在于其 `@EnableAutoConfiguration` 注解。通常，你会使用 `@SpringBootApplication` 来注解应用程序入口类，或者，如果你想自定义默认配置，可以使用以下注解：

```
@Configuration
@EnableAutoConfiguration
@ComponentScan
public class Application
{
}
```

`@EnableAutoConfiguration` 注解通过扫描类路径组件并注册符合各种条件的 Bean，从而启用 Spring `ApplicationContext` 的自动配置。Spring Boot 在 `spring-boot-autoconfigure-{version}.jar` 中提供了各种自动配置类，它们负责注册各种组件。

自动配置类通常使用 `@Configuration` 注解标记为 Spring 配置类，并使用 `@EnableConfigurationProperties` 注解来绑定自定义属性以及一个或多个条件化的 Bean 注册方法。

例如，考虑 `org.springframework.boot.autoconfigure.jdbc.DataSourceAutoConfiguration` 类。

```
@Configuration
@ConditionalOnClass({ DataSource.class, EmbeddedDatabaseType.class })
@EnableConfigurationProperties(DataSourceProperties.class)
@Import({ Registrar.class, DataSourcePoolMetadataProvidersConfiguration.class })
public class DataSourceAutoConfiguration {
...
...
@Bean
@ConditionalOnMissingBean
public DataSourceInitializer dataSourceInitializer(DataSourceProperties properties,
ApplicationContext applicationContext) {
return new DataSourceInitializer(properties, applicationContext);
}
...
...
@Conditional(EmbeddedDatabaseCondition.class)
@ConditionalOnMissingBean({ DataSource.class, XADataSource.class })
@Import(EmbeddedDataSourceConfiguration.class)
protected static class EmbeddedDatabaseConfiguration {
}
@Configuration
@Conditional(PooledDataSourceCondition.class)
@ConditionalOnMissingBean({ DataSource.class, XADataSource.class })
@Import({ DataSourceConfiguration.Tomcat.class, DataSourceConfiguration.Hikari.class,
DataSourceConfiguration.Dbcp2.class, DataSourceConfiguration.Generic.class })
protected static class PooledDataSourceConfiguration {
}
...
...
@Configuration
@ConditionalOnProperty(prefix = "spring.datasource", name = "jmx-enabled")
@ConditionalOnClass(name = "org.apache.tomcat.jdbc.pool.DataSourceProxy")
@Conditional(DataSourceAutoConfiguration.DataSourceAvailableCondition.class)
@ConditionalOnMissingBean(name = "dataSourceMBean")
protected static class TomcatDataSourceJmxConfiguration {
...
...
}
...
...
}
```

这里，`DataSourceAutoConfiguration` 使用了 `@ConditionalOnClass({ DataSource.class, EmbeddedDatabaseType.class })` 注解，这意味着只有当 `DataSource.class` 和 `EmbeddedDatabaseType.class` 类在类路径上可用时，才会考虑 `DataSourceAutoConfiguration` 中定义的 Bean 的自动配置。

该类还使用了 `@EnableConfigurationProperties(DataSourceProperties.class)` 注解，这使得 `application.properties` 中的属性能够自动绑定到 `DataSourceProperties` 类的属性上。

```
@ConfigurationProperties(prefix = DataSourceProperties.PREFIX)
public class DataSourceProperties implements BeanClassLoaderAware, EnvironmentAware, InitializingBean {
public static final String PREFIX = "spring.datasource";
...
...
private String driverClassName;
private String url;
private String username;
private String password;
...
//setters and getters
}
```

通过此配置，所有以 `spring.datasource.*` 开头的属性都将自动绑定到 `DataSourceProperties` 对象。

```
spring.datasource.url=jdbc:mysql://localhost:3306/test
spring.datasource.username=root
spring.datasource.password=secret
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
```

你还可以看到一些内部类和 Bean 定义方法使用了 Spring Boot 的条件注解，例如 `@ConditionalOnMissingBean`、`@ConditionalOnClass` 和 `@ConditionalOnProperty`。只有当这些条件匹配时，这些 Bean 定义才会被注册到 `ApplicationContext` 中。

你还可以在 `spring-boot-autoconfigure-{version}.jar` 中探索许多其他的 `AutoConfiguration` 类，例如：

*   `org.springframework.boot.autoconfigure.web.DispatcherServletAutoConfiguration`
*   `org.springframework.boot.autoconfigure.orm.jpa.HibernateJpaAutoConfiguration`
*   `org.springframework.boot.autoconfigure.data.jpa.JpaRepositoriesAutoConfiguration`
*   `org.springframework.boot.autoconfigure.jackson.JacksonAutoConfiguration`

现在，你应该对 Spring Boot 自动配置的工作原理有了基本的了解，即通过使用各种自动配置类以及 `@Conditional` 特性来实现。

## 总结

本章解释了如何使用 `@Conditional` 注解有条件地注册 Spring Bean，以及 Spring Boot 如何利用 `@Conditional` 和 `@EnableAutoConfiguration` 注解根据各种标准自动配置 Bean。

下一章将介绍 Spring Boot 的一些有助于提高开发人员生产力的酷炫特性。

