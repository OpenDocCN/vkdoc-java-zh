# 6. 使用 MyBatis

MyBatis 是一个开源的 Java 持久化框架，它抽象了 JDBC 样板代码，并提供了简单易用的 API 来与数据库交互。

与 Hibernate 这种全功能的 ORM 框架不同，MyBatis 是一个 SQL 映射框架。它自动化了将 SQL 结果集填充到 Java 对象的过程，并通过从 Java 对象中提取数据来将数据持久化到表中。

本章涵盖如何使用 Spring Boot MyBatis 启动器、如何使用 MyBatis XML 执行数据库查询，以及如何使用基于注解的映射器。



## 使用 Spring Boot MyBatis Starter

MyBatis 社区为 MyBatis 构建了 Spring Boot Starter，你可以在通过 Spring Initializer 或 IDE 创建 Spring Boot 项目时使用它。你可以在 GitHub 上查看其源代码：[`https://github.com/mybatis/mybatis-spring-boot`](https://github.com/mybatis/mybatis-spring-boot)。

接下来你将学习如何使用 Spring Boot MyBatis Starter 在 Spring Boot 应用中快速集成 MyBatis。

1.  创建一个 Spring Boot Maven 项目，并配置 MyBatis Starter 依赖以及 H2/MySQL 驱动依赖。

```
    org.mybatis.spring.boot
    mybatis-spring-boot-starter
    1.3.0

com.h2database
    h2

mysql
    mysql-connector-java

```

本示例复用了上一章创建的 `User.java`、`schema.sql` 和 `data.sql` 文件。
2.  创建名为 `UserMapper.java` 的 MyBatis SQL 映射器接口，其中包含一些数据库操作，如清单 6-1 所示。

```
    package com.apress.demo.mappers;
    public interface UserMapper
    {
    void insertUser(User user);
    User findUserById(Integer id);
    List findAllUsers();
    }
    清单 6-1.
    com.apress.demo.mappers.UserMapper.java
    ```

3.  现在你需要创建映射器 XML 文件，为映射到相应映射器接口方法的 SQL 语句定义查询。在 `src/main/resources/com/apress/demo/mappers/` 目录下创建 `UserMapper.xml` 文件，如清单 6-2 所示。

```

select id, name, email from users

select id, name, email from users WHERE id=#{id}

insert into users(name,email) values(#{name},#{email})

清单 6-2.
    src/main/resources/com/apress/demo/mappers/UserMapper.xml
    ```

这里需要注意以下几点：
    *   映射器 XML 中的命名空间应与映射器接口的完全限定名（FQN）保持一致。
    *   语句的 `id` 值应与映射器接口的方法名保持一致。
    *   如果查询结果列名与 Bean 属性名不同，你可以使用 `<resultMap>` 配置来提供列名与其对应 Bean 属性名之间的映射关系。
4.  MyBatis 也提供了基于注解的查询配置，无需映射器 XML。你可以创建 `UserMapper.java` 接口并使用注解配置映射的 SQL，如清单 6-3 所示。

```
    public interface UserMapper
    {
    @Insert("insert into users(name,email) values(#{name},#{email})")
    @SelectKey(statement="call identity()", keyProperty="id",
    before=false, resultType=Integer.class)
    void insertUser(User user);
    @Select("select id, name, email from users WHERE id=#{id}")
    User findUserById(Integer id);
    @Select("select id, name, email from users")
    List findAllUsers();
    }
    清单 6-3.
    com.apress.demo.mappers.UserMapper.java
    ```

5.  接下来，你需要配置 Starter 的配置参数。在 `application.properties` 中配置 `type-aliases-package` 和 `mapper-locations` 参数，如下所示。

```
    mybatis.type-aliases-package=com.apress.demo.domain
    mybatis.mapper-locations=classpath*:/mappers/*.xml
    ```

6.  创建名为 `SpringbootMyBatisDemoApplication.java` 的入口类，如清单 6-4 所示。

```
    @SpringBootApplication
    @MapperScan("com.apress.demo.mappers")
    public class SpringbootMyBatisDemoApplication
    {
    public static void main(String[] args)
    {
    SpringApplication.run(SpringbootMyBatisDemoApplication.class, args);
    }
    }
    清单 6-4.
    com.apress.demo.SpringbootMyBatisDemoApplication.java
    ```

注意 本示例使用 `@MapperScan("com.apress.demo.mappers")` 注解来指定映射器接口的查找位置。除了使用 `@MapperScan`，你也可以使用 MyBatis 3.4.0 中新增的 `@Mapper` 注解来标注映射器接口。
7.  创建一个 JUnit 测试类并测试 `UserMapper` 方法，如清单 6-5 所示。

```
    @RunWith(SpringRunner.class)
    @SpringBootTest
    public class SpringbootMyBatisDemoApplicationTests
    {
    @Autowired
    private UserMapper userMapper;
    @Test
    public void findAllUsers()  {
    List users = userMapper.findAllUsers();
    assertNotNull(users);
    assertTrue(!users.isEmpty());
    }
    @Test
    public void findUserById()  {
    User user = userMapper.findUserById(1);
    assertNotNull(user);
    }
    @Test
    public void createUser() {
    User user = new User(0, "george", "george@gmail.com");
    userMapper.insertUser(user);
    User newUser = userMapper.findUserById(user.getId());
    assertEquals("george", newUser.getName());
    assertEquals("george@gmail.com", newUser.getEmail());
    }
    }
    清单 6-5.
    com.apress.demo.SpringbootMyBatisDemoApplicationTests.java
    ```

Spring Boot MyBatis Starter 提供了以下 MyBatis 配置参数，你可以使用它们来自定义 MyBatis 设置。

```
mybatis.config-location =  #mybatis 配置文件名称
mybatis.check-config-location=  #是否检查 MyBatis XML 配置文件是否存在
mybatis.mapper-locations =  #映射器文件位置
mybatis.type-aliases-package =  #领域对象的包名
mybatis.type-handlers-package =  #类型处理器的包名
mybatis.check-config-location =  #检查 MyBatis 配置是否存在
mybatis.executor-type =  #执行模式。默认为 SIMPLE
mybatis.configuration-properties=  #MyBatis 配置的外部化属性
```

你可以在 [`http://blog.mybatis.org/p/products.html`](http://blog.mybatis.org/p/products.html) 了解更多关于 MyBatis 的信息。

## 本章小结

在本章中，你学习了如何在 Spring Boot 应用中使用 MyBatis。下一章将介绍如何在 Spring Boot 中使用另一个流行的 Java 持久化框架 JOOQ。

