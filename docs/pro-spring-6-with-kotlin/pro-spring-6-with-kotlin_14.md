# 日志配置
logging:
pattern:
console: "%-5level: %class{0} - %msg%n"
level:
root: INFO
org.springframework.boot: DEBUG
com.apress.prospring6.ten.boot: INFO
org.springframework.orm.jpa: TRACE
清单 10-34
Spring Boot Data JPA 配置 YAML 配置
```

配置分为三个部分：`datasource`、jpa 和 logging。以 `spring.datasource` 开头的属性设置了配置数据源所需的值：连接 URL、凭据和连接池值。以 `spring.jpa` 开头的属性设置了描述持久化单元的值。您可以轻松识别出第 7 章和第 8 章中介绍的 Hibernate 属性。以 `logging` 开头的属性设置了日志配置的典型值，在经典应用程序中，这些值可以在 `logback.xml` 文件中找到。

在 Spring Boot Data JPA 应用程序中，自动配置机制会根据配置文件中的属性负责声明必要的 Bean，例如 dataSource、`EntityManager`、`LocalSessionFactoryBean`、`TransactionManager` 等，但我们仍然需要配置一些内容：

*   *实体类的位置*：由于 `LocalSessionFactoryBean` 是自动配置的，指定实体类所在包的方式是使用 `org.springframework.boot.autoconfigure.domain` 包中的 `@EntityScan` 注解。

*   *Spring Data 仓库接口的位置*：这很容易实现，只需将 `@EnableJpaRepositories` 注解添加到带有 `@Configuration` 注解的类（如果有）或带有 `@SpringBootApplication` 注解的类（如果没有）上即可。

*   *事务行为*：这也很容易实现，只需将 `@EnableTransactionManagement` 注解添加到带有 `@Configuration` 注解的类（如果有）或带有 `@SpringBootApplication` 注解的类（如果没有）上即可。

清单 10-35 显示了本节分析项目的配置。

```
package com.apress.prospring6.ten.boot
import com.apress.prospring6.ten.boot.service.SingerService
import org.slf4j.Logger
import org.slf4j.LoggerFactory
import org.springframework.boot.SpringApplication
import org.springframework.boot.autoconfigure.SpringBootApplication
import org.springframework.boot.autoconfigure.domain.EntityScan
import org.springframework.core.env.AbstractEnvironment
import org.springframework.data.jpa.repository.config.EnableJpaRepositories
import org.springframework.transaction.annotation.EnableTransactionManagement
@EntityScan(basePackages = ["com.apress.prospring6.ten.boot.entities"])
@EnableTransactionManagement
@EnableJpaRepositories("com.apress.prospring6.ten.boot.repos")
@SpringBootApplication
open class Chapter10Application {
companion object {
private val LOGGER = LoggerFactory.getLogger(Chapter10Application::class.java)
@JvmStatic
fun main(args: Array) {
System.setProperty(AbstractEnvironment.ACTIVE_PROFILES_PROPERTY_NAME,
"dev")
val ctx = SpringApplication.run(Chapter10Application::class.java, *args)
val service = ctx.getBean(
SingerService::class.java
)
LOGGER.info(" ---- 列出歌手:")
service.findAll().forEach { s -> LOGGER.info(s.toString()) }
}
}
}
清单 10-35
Spring Boot Data JPA 注解配置
```

如您所见，Spring Boot 主配置类有一个 `main(..)` 方法。这使得此类可执行，并且运行此类 `main(..)` 方法中的代码可确保配置正确。但是，使用目前所示的配置运行该类会以以下错误结束：

```
Exception in thread "main" org.springframework.dao.InvalidDataAccessResourceUsageException:
JDBC exception executing SQL
[select s1_0.id,
s1_0.birth_date,
s1_0.first_name,
s1_0.last_name,
s1_0.version
from singer s1_0
]; SQL [n/a]
...
Caused by: java.sql.SQLSyntaxErrorException: (conn=308) Table 'musicdb.singer' doesn't exist
at org.mariadb.jdbc.export.ExceptionFactory.createException(ExceptionFactory.java:280)
at org.mariadb.jdbc.export.ExceptionFactory.create(ExceptionFactory.java:368)
```

那么，这是怎么回事呢？如果数据源配置正确，为什么找不到 `singer` 表？如果您认为原因是表名区分大小写，那么您说得有些道理。在解释这个 Spring Boot Data JPA 应用程序中发生了什么之前，让我们先谈谈 Hibernate 的命名策略。

Hibernate 使用两种不同的命名策略^(⁹³)将名称从对象模型映射到相应的数据库名称：

*   `ImplicitNamingStrategy`：适当的逻辑名称由领域模型映射确定，要么来自配置注解（如 `@Column` 和 `@Table`，如您在前几章中所见），要么由 Hibernate 使用通过 `Environment.IMPLICIT_NAMING_STRATEGY / hibernate.implicit_naming_strategy` 属性配置的 `org.hibernate.boot.model.naming.ImplicitNamingStrategy` 实现来确定。

*   `PhysicalNamingStrategy`：适当的逻辑名称由 Hibernate 使用通过 `Environment.PHYSICAL_NAMING_STRATEGY / hibernate.physical_naming_strategy` 属性配置的 `org.hibernate.boot.model.naming.PhysicalNamingStrategy` 实现来确定。

这两种策略的目的是减少开发人员必须提供的显式配置。

许多组织都定义了关于数据库对象（表、列、外键等）命名的规则。`PhysicalNamingStrategy` 的理念是帮助实现此类命名规则，而无需通过显式名称将它们硬编码到映射中。



大多数公司都有自己的命名规范，数据库对象（如表、列、外键等）也不例外。有些公司倾向于使用大写字母命名数据库对象，这样更容易在代码中区分 HQL 和原生 SQL 查询。本书也采用了这种方法。

在 Spring Boot Data JPA 应用程序中，确定数据库对象名称的默认策略是 Hibernate 提供的 `PhysicalNamingStrategy` 接口的实现类 `org.hibernate.boot.model.naming.CamelCaseToUnderscoresNamingStrategy`。顾名思义，逻辑名称采用驼峰命名法，并用下划线分隔。这就是为什么 `Singer` 实体类会被映射到 `singer` 表；由于 MariaDB 安装在 Linux 容器上，数据库对象名称是区分大小写的，因此 `SINGER` 显然不等于 `singer`。

解决方案是提供我们自己的 `PhysicalNamingStrategy` Bean，指示 Spring Boot 如何确定数据库对象名称。`PhysicalNamingStrategy` 接口为每个命名的数据库对象声明了五个方法^(⁹⁴)。该接口的主体在清单 10-36 中声明。

```
package org.hibernate.boot.model.naming;
import org.hibernate.Incubating;
import org.hibernate.engine.jdbc.env.spi.JdbcEnvironment;
@Incubating
public interface PhysicalNamingStrategy {
Identifier toPhysicalCatalogName(Identifier logicalName, JdbcEnvironment jdbcEnvironment);
Identifier toPhysicalSchemaName(Identifier logicalName, JdbcEnvironment jdbcEnvironment);
Identifier toPhysicalTableName(Identifier logicalName, JdbcEnvironment jdbcEnvironment);
Identifier toPhysicalSequenceName(Identifier logicalName, JdbcEnvironment jdbcEnvironment);
Identifier toPhysicalColumnName(Identifier logicalName, JdbcEnvironment jdbcEnvironment);
}
清单 10-36
Hibernate 的 PhysicalNamingStrategy 接口
```

![](img/524962_1_En_10_Figm_HTML.jpg) 一个信息符号。由一个带阴影圆圈内的小写字母 i 表示。 `@Incubating` 注解表示某些内容仍在积极开发中，因此未来可能会发生变化。

为了简化操作，我们可以扩展 Hibernate 提供的 `PhysicalNamingStrategyImpl` 类，并仅覆盖我们感兴趣的方法。清单 10-37 中显示的 `HibernateCfg` 配置类声明了一个匿名类型的 Bean，该类型扩展了 `PhysicalNamingStrategyImpl`。

```
package com.apress.prospring6.ten.boot
import org.hibernate.boot.model.naming.Identifier
import org.hibernate.boot.model.naming.PhysicalNamingStrategyStandardImpl
import org.hibernate.engine.jdbc.env.spi.JdbcEnvironment
import org.springframework.context.annotation.Bean
import org.springframework.context.annotation.Configuration
@Configuration(proxyBeanMethods = false)
open class HibernateCfg {
@Bean
open fun caseSensitivePhysicalNamingStrategy(): PhysicalNamingStrategyStandardImpl {
return object : PhysicalNamingStrategyStandardImpl() {
override fun toPhysicalTableName(logicalName: Identifier,
context: JdbcEnvironment): Identifier {
return apply(logicalName, context)!!
}
override fun toPhysicalColumnName(logicalName: Identifier,
context: JdbcEnvironment): Identifier {
return apply(logicalName, context)!!
}
private fun apply(name: Identifier?, context: JdbcEnvironment): Identifier? {
if (name == null) {
return null
}
val builder = StringBuilder(name.text.replace('.', '_'))
var i = 1
while (i < builder.length - 1) {
if (isUnderscoreRequired(builder[i - 1], builder[i], builder[i + 1])) {
builder.insert(i++, '_')
}
i++
}
return Identifier.toIdentifier(builder.toString().uppercase(Locale.getDefault()))
}
private fun isUnderscoreRequired(before: Char, current: Char, after: Char) =
Character.isLowerCase(before) &&
Character.isUpperCase(current) &&
Character.isLowerCase(after)
}
}
}
清单 10-37
自定义 PhysicalNamingStrategy 实现
```

`proxyBeanMethods` 属性被设置为 `false`，因为此类中带有 `@Bean` 注解的方法不应被代理以强制执行 Bean 生命周期行为。用于确定表和列逻辑名称的代码在 `apply()` 方法中声明。

如果你使用这个新的配置类执行 `Chapter10Application` 类，执行将成功，并打印出预期的输出，即歌手实例的字符串表示。

仓库数据类和服务类与本章前面经典 Spring 应用程序中展示的相同。测试方法也相同，唯一的区别在于测试上下文的配置。建议使用 `application-test.yaml` 或 `application-test.properties` 文件，因为数据源需要替换为 Testcontainers 数据源。本节示例中使用的配置文件如清单 10-38 所示。

```
spring:
datasource:
url: "jdbc:tc:mariadb:10.9-rc:///testdb?TC_INITSCRIPT=testcontainers/create-schema.sql"
jpa:
properties:
hibernate:
jdbc:
batch_size: 10
fetch_size: 30
max_fetch_depth: 3
show-sql: true
format-sql: true
use_sql_comments: true
hbm2ddl:
auto: none
