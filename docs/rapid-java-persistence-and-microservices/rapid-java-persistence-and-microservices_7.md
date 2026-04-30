# 5. 不使用 ORM 的 Java 持久化

到目前为止，我们快速浏览了 Spring Data JPA，主要关注其正面用例。使用 ORM 框架也有一些缺点。随着当今对应用程序扩展能力的需求日益增长，讨论 ORM 的这些相关问题变得非常重要。本章将探讨当应用程序规模超出常规限制时，ORM 所面临的与性能相关的问题：

*   对于每个操作，ORM 框架都必须管理实体的生命周期，从而导致额外的开销。例如，无论实体是瞬态的、持久的还是分离的，Hibernate 都会进行额外的检查，并确保所有状态更改事件的一致性。

*   从 JPQL 创建 SQL 查询，或对实体执行操作时产生的成本是额外的开销。

*   特定于数据库的 SQL 优化难以管理。每个供应商都有某些变体，任何 ORM 都难以支持所有这些变体。这可能会限制查询的微调。

*   除了领域对象或实体之外，我们还需要 DTO（数据传输对象）类，并且需要进行映射，这会影响整体性能。额外的代码库使得维护成本更高。以下是需要单独 DTO 层的一些常见原因：
    *   DTO 与 UI/客户端层或用例紧密绑定，因此它们可能比领域模型拥有更多和/或更少的属性。

    *   UI 数据需求与后端数据管理之间没有严格的契约，这使事情变得稍微容易一些。

    *   有时，出于安全原因，必须限制某些属性返回给客户端，尽管在实体上使用 `@JsonIgnore` 也是一种选择。

    *   领域模型的更改可以在不更改 DTO 类的情况下进行。

作为 Java 中 ORM 的替代方案，我们寻找能够灵活地向 SQL 数据库发起原生查询，并帮助将结果映射回对象的解决方案。在 Spring 框架中，我们有多种选择来实现这一点：

*   Spring Data JPA 原生查询支持

*   JDBCTemplate

*   Spring Data JDBC

也可以与其他框架集成，例如 MyBatis 和 JOOQ，但我们重点关注由 Spring 社区创建的方案。其优势在于 API 的一致性和更简单的集成。我们在前一章中讨论了 Spring Data JPA 的原生查询支持。接下来，让我们在下一节中了解 JDBCTemplate 和 Spring Data JDBC。



## Spring JDBC 模板

Spring JDBCTemplate 是 Spring 框架创建的 API，用于在关系型数据库上执行原生 SQL 查询，并将结果映射回自定义的 Java 对象。让我们通过一个非常简单的示例来重新创建我们的 Eshop 应用，并将其连接到 MySQL 数据库。我们将在单个查询中查询客户、购买的产品和订单信息。该结果应按照以下步骤映射到一个名为 `CustomerOrder` 的 DTO 类：

1.  创建一个基于 Gradle 的应用

2.  初始化并运行示例 SQL 查询，以填充 `Customer`、`Product` 和 `Order` 表。

3.  定义一个名为 `CustomerOrder` 的新 DTO。

4.  运行一个 SQL 查询，并将结果映射回对象模型；即一个名为 `CustomerOrder` 的列表。

让我们使用以下代码结构构建一个应用：

`eshop-jdbc-template-ch5`

```
.
├── build.gradle
└── src
├── main
│   ├── java
│   │   └── com
│   │       └── example
│   │           └── eshop
│   │               ├── Application.java
│   │               └── model
│   │                   └── dto
│   │                       └── CustomerOrder.java
│   └── resources
│       └── application.properties
└── test
├── java
│   └── com
│       └── example
│           └── eshop
│               └── EshopAppTests.java
└── resources
└── schema.sql
```

### 应用设置

从 [`http://www.spring.io`](http://www.spring.io) 创建一个名为 `eshop-jdbc-template-ch5` 的新应用，并在 `build.gradle` 文件中包含以下依赖项（参见清单 5-1）。

```
dependencies {
compile ([
"org.springframework.boot:spring-boot-starter-jdbc",
"org.springframework.boot:spring-boot-starter-test"
])
compile ([
"mysql:mysql-connector-java:8.0.15",
"org.projectlombok:lombok:1.18.6"
])
testCompile("junit:junit")
testCompile("org.projectlombok:lombok:1.18.6")
annotationProcessor("org.projectlombok:lombok:1.18.6")
testAnnotationProcessor("org.projectlombok:lombok:1.18.6")
}
清单 5-1
Build.gradle 文件
```

### 配置与初始模式脚本

我们需要在 `application.properties` 文件中指定数据源相关的属性（参见清单 5-2）。

```
application.properties
spring.datasource.url=jdbc:mysql://localhost:3306/eshop
spring.datasource.username=root
spring.datasource.password=mysql
spring.datasource.driver-class-name: com.mysql.jdbc.Driver
schema.sql
CREATE TABLE IF NOT EXISTS Customer (
customerId int(11) unsigned NOT NULL AUTO_INCREMENT PRIMARY KEY,
dateAdded datetime DEFAULT NULL,
email varchar(255) DEFAULT NULL,
name varchar(255) DEFAULT NULL,
password varchar(255) DEFAULT NULL
);
CREATE TABLE IF NOT EXISTS `Order` ( -- 使用 `` 字符转义 Order)
orderId bigint(20) NOT NULL PRIMARY KEY AUTO_INCREMENT,
customerId int(11) DEFAULT NULL,
price int(11) NOT NULL,
productId bigint(20) DEFAULT NULL,
quantity int(11) NOT NULL
);
CREATE TABLE IF NOT EXISTS Product (
productId int(11) unsigned NOT NULL PRIMARY KEY AUTO_INCREMENT,
name varchar(255) DEFAULT NULL,
quantity varchar(255) DEFAULT NULL,
price smallint DEFAULT NULL
);
Insert into Customer(name, email, password) VALUES('Raj', 'raj@example.com', 'password');
Insert into Product(name, quantity, price) VALUES('Laptop', 10, 1200);
Insert into `Order`(customerId, productId, price, quantity) VALUES(1, 1, 1200, 1);
清单 5-2
配置文件与数据库初始化脚本
```

注意：

*   此脚本必须在名为 `eshop` 的 MySQL 数据库中运行。

*   我们需要使用 ``` `` ``` 字符转义 `Order`，因为 `Order` 是 MySQL 中的关键字。在此脚本中，我们手动为名为 Raj Malhotra 的客户创建了一个包含单个产品的订单作为示例。

### DTO 与应用类

这里我们有一个名为 `CustomerOrder` 的简单 POJO，它混合了 `Customer`、`Product`**和** `Order` 类的属性。此对象的目的是组合属性，以识别哪个客户购买了哪个产品以及订单详情是什么（参见清单 5-3）。

```
package com.example.eshop;
import lombok.extern.slf4j.Slf4j;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
@SpringBootApplication
@Slf4j
public class Application  {
public static void main(String[] args) throws Exception {
SpringApplication.run(Application.class, args);
}
}
package com.example.eshop.model.dto;
import lombok.Data;
@Data
public class CustomerOrder {
private Long customerId;
private Long orderId;
private Long productId;
private String customerName;
private String customerEmail;
private String productName;
private int quantity;
private int price;
}
清单 5-3
DTO 与应用类
```

这就像一个扁平的表结构，将所有信息包含在一个对象中。下一节将解释如何在不定义实体和仓库的情况下进行查询。

### 测试用例

我们将验证将数据库数据查询并检索出来，映射到 DTO 类列表所需的最少代码（参见清单 5-4）。



```
package com.example.eshop;
import com.example.eshop.model.dto.CustomerOrder;
import lombok.extern.slf4j.Slf4j;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.jdbc.core.BeanPropertyRowMapper;
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.test.context.junit4.SpringRunner;
import java.util.List;
@RunWith(SpringRunner.class)
@SpringBootTest
@Slf4j
public class EshopAppTests {
@Autowired
JdbcTemplate jdbcTemplate;
@Test
public void testThroughJdbcTemplate()   {
String sql = "Select " +
"c.customerId, o.orderId, p.productId," +
"c.name as customerName, c.email as customerEmail, p.name as productName," +
"p.quantity as quantity, p.price as price " +
"from " +
"Customer c inner join `Order` o on c.customerId = o.customerId inner join " +
"Product p on o.productId = p.productId";
List customerWithOrdersList = jdbcTemplate.query(sql, new BeanPropertyRowMapper(CustomerOrder.class));
log.info("订单详情: " + customerWithOrdersList);
}
}
代码清单 5-4
测试用例
```

注意：

*   `BeanPropertyRowMapper` 类是 `RowMapper` 的实现，它通过反射将结果集映射到 Bean 的列。

*   Bean（即 `CustomerOrder`）必须有一个无参构造函数，并且字段名应与数据库中的列名匹配。下划线将转换为驼峰式字段名。

*   Spring 指出，该类旨在提供便利性而非高性能。为了获得最佳性能，请考虑使用自定义的 `RowMapper` 实现。

*   `BeanPropertyRowMapper` 不会映射嵌套对象。如果 `CustomerOrder` 包含与任何其他数据模型关联的列表，则不会自动映射。这在 JPA 中很容易发生。

*   除了简单性之外，另一个优点是无需进行从领域类到 DTO 的二次转换或映射供 UI 使用。

### 运行 Eshop-JDBC-Template-ch5 应用程序测试用例

运行应用程序：

```
> gradle clean build
> gradle test
>
:: Spring Boot ::        (v2.1.3.RELEASE)
2019-01-08 09:33:19.171  INFO 18551 --- [           main] com.example.eshop.EshopAppTests          : 在 Rajs-MacBook-Pro.local 上启动 EshopAppTests，PID 为 18551（由 raj 在 /Users/raj/work_all/book_code/rapid-java-persistence-and-microservices/ch5/eshop-jdbc-template-ch5 中启动）
2019-01-08 09:33:19.173  INFO 18551 --- [           main] com.example.eshop.EshopAppTests          : 未设置活动配置文件，回退到默认配置文件：default
2019-01-08 09:33:19.864  INFO 18551 --- [           main] com.zaxxer.hikari.HikariDataSource       : HikariPool-1 - 正在启动...
2019-01-08 09:33:20.030  INFO 18551 --- [           main] com.zaxxer.hikari.HikariDataSource       : HikariPool-1 - 启动完成。
2019-01-08 09:33:20.208  INFO 18551 --- [           main] com.example.eshop.EshopAppTests          : 在 1.322 秒内启动了 EshopAppTests（JVM 运行了 2.142 秒）
2019-01-08 09:33:20.546  INFO 18551 --- [           main] com.example.eshop.EshopAppTests          : 订单详情: [CustomerOrder(customerId=1, orderId=1, productId=1, customerName=Raj, customerEmail=raj@example.com, productName=Laptop, quantity=10, price=1200)]
2019-01-08 09:33:20.551  INFO 18551 --- [       Thread-1] com.zaxxer.hikari.HikariDataSource       : HikariPool-1 - 正在关闭...
2019-01-08 09:33:20.562  INFO 18551 --- [       Thread-1] com.zaxxer.hikari.HikariDataSource       : HikariPool-1 - 关闭完成。
```

日志输出显示了由 Lombok 的 `toString()` 方法返回的对象值。其目的是展示实现数据库访问所需代码的简洁性。API 中还有更多选项可以以编程方式将结果集映射到对象。我们跳过了基于 JDBCTemplate 的编程式 `RowMapper` API 方法，因为如今有更好的选择，将在下一节中介绍。

## Spring Data JDBC

这里的想法是提供同样一致且简单的基于 Spring 仓库的持久化，但使用 JDBC 而非 JPA。此外，与 JDBCTemplate 不同，无需自定义 DAO 实现。可以利用相同的基于接口的 `CrudRepository` 和 `PagingAndSortingRepository` 扩展。返回类型也支持 Optional 和 Stream API。Spring Data JDBC 还通过 Spring `@Async` 注解提供异步查询。请注意，异步查询与响应式查询不同，后者也将在第 8 章中讨论。

此外，还提供了基于 Spring Data DSL 的查询生成，这意味着 Spring 将提供方法名称的动态实现，以匹配其 DSL。例如，`findByFirstName(String firstName)` 将被转换为 `Select * from TABLE where first_name= :firstName)`。

现在让我们看一个完整的示例，通过以下代码结构来了解其实际应用：

`eblog-spring-data-jdbc-ch5`

```
.
├── build.gradle
├── gradlew
├── gradlew.bat
├── settings.gradle
└── src
└── main
├── java
│   └── com
│       └── example
│           └── eblog
│               ├── App.java
│               ├── model
│               │   ├── Author.java
│               │   └── Post.java
│               ├── repository
│               │   ├── AuthorRepository.java
│               │   └── PostRepository.java
│               └── web
│                   └── TestController.java
└── resources
└── application.properties
```

### 应用程序设置

从 [`http://www.spring.io`](http://www.spring.io) 创建一个名为 `eblog-spring-data-jdbc-ch5` 的新应用程序，其依赖项如代码清单 5-5 中的 `build.gradle` 文件所示。

```
dependencies {
compile('org.springframework.boot:spring-boot-starter-web')
compile('org.springframework.boot:spring-boot-starter-data-jdbc')
compile ([
"mysql:mysql-connector-java:8.0.15",
"org.projectlomboklombok:1.18.6"
])
testCompile("junit:junit:4.12")
}
代码清单 5-5
Build.gradle 文件
```

### 配置和初始模式脚本

我们需要在 `application.properties` 文件中指定数据源相关的属性（参见代码清单 5-6）。

```
application.properties
spring.datasource.url=jdbc:postgresql://localhost:5432/eblog
spring.datasource.username=postgres
spring.datasource.password=postgres
spring.datasource.driver-class-name: org.postgresql.Driver
server.servlet.contextPath= /eblog
server.port= 8201
schema.sql
create table POST (
id serial primary key not null,
title varchar(500),
content text,
author int
);
create table AUTHOR (
id serial primary key not null,
name varchar(100),
age int
);
insert into author(name, age) values('Raj Malhorta', 35);
insert into post(title, content, author) values('示例标题', '示例内容', 1);
代码清单 5-6
配置文件与数据库初始化脚本
```

`schema.sql` 文件中的 `sql` 语句应按照 `application.properties` 文件中的说明，在 `eblog` 模式上手动运行。

### 领域模型与应用程序类

在代码清单 5-7 中，`Author` 对象与 `Post` 对象之间存在 `OneToMany` 关联关系。请注意，此模块并非支持所有关联。



```
package com.example.eblog.model;
import lombok.*;
import org.springframework.data.annotation.Id;
import org.springframework.data.annotation.PersistenceConstructor;
import java.io.Serializable;
import java.util.Set;
@Data
@AllArgsConstructor
public class Author implements Serializable {
@Id
private Long id;
private String name;
private int age;
private Set posts;
@PersistenceConstructor
public Author(String name, int age) {
this.name = name;
this.age = age;
}
Author withId(Long id)  {
return new Author(id, this.name, this.age, this.posts);
}
}
package com.example.eblog.model;
import lombok.*;
import org.springframework.data.annotation.Id;
import java.io.Serializable;
@Data
@Builder
@AllArgsConstructor
public class Post implements Serializable {
@Id
private Long id;
private String title;
private String content;
}
package com.example.eblog;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
@SpringBootApplication
public class App {
public static void main(String[] args) {
SpringApplication.run(App.class, args);
}
}
清单 5-7
领域模型
```

说明：

*   `Author` 和 `Post` 类中包含简单且自解释的字段。

*   `Author` 包含一个 `Post` 对象的集合，表示一对多关联。在 `Author` 类中，我们使用了两个更有趣的注解——`@Id`（`org.springframework.data.annotation.Id`）和 `@PersistenceConstructor`。

*   `@Id` 被 Spring 框架用于管理自动生成，并像 JPA 一样为 NoSQL 填充 ID 字段（通常是主键）。

*   `@PersistenceConstructor` 注解用于在创建对象以映射回结果时，选择首选的构造函数。理想情况下，Spring 首先查找无参构造函数，然后查找单参数构造函数。如果对象有多个构造函数，则会选择带有此注解的那个。

*   当对象被插入数据库时，会使用 `withId(ID)` 方法。字段值的设置顺序如下：
    *   如果存在 `withId(ID)` 方法，Spring 会使用该方法创建带有 ID 的对象。之后对该对象的所有更新都会更新同一个对象，仅保留 ID。当 Spring 加载或更新现有对象时，也需要此方法。

*   最后，通过 setter 方法或直接设置剩余字段的值。

*   为了管理关联，被引用实体的表需要有一个额外的列，其名称与引用实体的表名相同。在本例中，`Post` 表应有一个名为 `author` 的列。不支持多对一和多对多关联。

### 数据仓库

这正是我们领略该框架魅力之处。与我们之前的 JPA 示例一样，我们仍然拥有基于接口的仓库定义，并在其之上使用可选的 `@Query` 注解（参见清单 5-8）。

```
package com.example.eblog.repository;
import com.example.eblog.model.Post;
import org.springframework.data.repository.CrudRepository;
import org.springframework.data.repository.Repository;
public interface PostRepository extends CrudRepository {}
package com.example.eblog.repository;
import com.example.eblog.model.Author;
import org.springframework.data.jdbc.repository.query.Query;
import org.springframework.data.repository.CrudRepository;
import org.springframework.data.repository.Repository;
import java.util.List;
public interface AuthorRepository extends CrudRepository {
@Query("Select a.* from Author a")
List findAuthorsWithPosts();
//@Query(("Select * from Author where age= :age"))
List findByAge(Integer age);
//List findByAge(@Param("age") Integer age);
//Stream findByAge(@Param("age") Integer age);
//Optional> findByAge(@Param("age") Integer age);
}
清单 5-8
仓库定义
```

### 注意

理想情况下，`findByAge` 方法应能按此处定义的方式工作，但在撰写本书时，它尚无法正常工作。关于此问题，已提交了一个工单（[`https://jira.spring.io/browse/DATAJDBC-318`](https://jira.spring.io/browse/DATAJDBC-318)）。

### 控制器类

`TestController` 类如清单 5-9 所示，它包含用于访问所有 `Post` 对象列表、`Authors` 列表以及关联的 `Post` 对象的方法。

```
package com.example.eblog.web;
import com.example.eblog.model.Author;
import com.example.eblog.model.Post;
import com.example.eblog.repository.AuthorRepository;
import com.example.eblog.repository.PostRepository;
import com.google.common.collect.Lists;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.domain.PageRequest;
import org.springframework.data.domain.Sort;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;
import java.util.List;
@RestController
@Slf4j
public class TestController {
@Autowired
AuthorRepository authorRepository;
@Autowired
PostRepository postRepository;
@GetMapping("/posts")
public List recentPosts(@RequestParam Integer limit, @RequestParam Integer offset, @RequestParam String orderBy){
log.info("recentPosts, params: {}, {}", limit, offset);
PageRequest pageRequest = PageRequest.of(limit, offset, Sort.Direction.DESC, orderBy);
return Lists.newArrayList(postRepository.findAll());
}
@GetMapping("/authors")
public List authorsWithTopPosts()   {
log.info("authorsWithTopPosts");
return authorRepository.findAuthorsWithPosts();
}
}
清单 5-9
控制器类
```

### 运行 Eblog-Spring-Data-JDBC-ch5 应用测试用例

运行应用程序：

```
> gradle clean build
> gradle bootRun
>
o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat initialized with port(s): 8201 (http)
o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat started on port(s): 8201 (http) with context path '/eblog'
com.example.eblog.App : Started App in 2.979 seconds (JVM running for 3.447)
com.example.eblog.web.TestController     : authorsWithTopPosts
com.example.eblog.web.TestController     : recentPosts, params: 10, 1
```

让我们在命令行中使用 `Curl` 测试应用程序：

```
> curl -X GET -i http://localhost:8201/eblog/authors
[{"id":1,"name":"Raj Malhorta","age":35,"posts":[{"id":1,"title":"Sample
Title","content":"Sample Content"}]}]
> curl -X GET -i 'http://localhost:8201/eblog/posts?limit=10&offset=1&orderBy=id'
HTTP/1.1 200
Content-Type: application/json;charset=UTF-8
Transfer-Encoding: chunked
Date: Thu, 28 Feb 2019 02:22:52 GMT
[{"id":1,"title":"Sample Title","content":"Sample Content"}]
```

我们可以看到，`Curl` 输出显示返回了一个 `Author`（Raj Malhotra）对象及其 `Post`（Sample Title），这是根据第 5.2.2 节中的脚本创建的。

## Spring Data for NoSQLs

我认为，Spring Data 是 Java 相较于其他语言框架仍然闪耀的最大原因。Spring Data 提供了相同的基于仓库的便捷 API 模型，该模型通过 ORM 适用于许多关系型数据库，同时也适用于 NoSQL 数据库。让我们看一个使用 MongoDB（作为最简单的通用 NoSQL 数据库）的示例。我们将继续使用之前的博客应用示例，并对 `Post` 和 `Comment` 对象执行数据操作。

让我们使用以下代码结构构建示例应用程序：

`blog-spring-data-mongodb-ch5`



```
.
├── build.gradle
└── src
├── main
│   ├── java
│   │   └── com
│   │       └── example
│   │           └── blog_app
│   │               ├── BlogApp.java
│   │               ├── model
│   │               │   └── mongo
│   │               │       ├── Comment.java
│   │               │       ├── Post.java
│   │               │       └── PostStatus.java
│   │               └── repository
│   │                   └── PostMongoRepository.java
│   └── resources
└── test
├── java
│   └── com
│       └── example
│           └── blog_app
│               └── BlogTests.java
└── resources
```

### 应用设置

正如我们在前几章中所见，我们首先从 [`http://www.spring.io`](http://www.spring.io) 创建一个新应用，并在 `build.gradle` 文件中添加以下依赖项（参见清单 5-10）。

```
dependencies
compile('org.springframework.boot:spring-boot-starter-web')
compile("org.springframework.boot:spring-boot-starter-data-mongodb")
compile('org.projectlombok:lombok:1.18.6')
testCompile('junit:junit:4.12')
testCompile('org.springframework.boot:spring-boot-starter-test')
annotationProcessor("org.projectlombok:lombok:1.18.6")
testAnnotationProcessor("org.projectlombok:lombok:1.18.6")
}
清单 5-10
build.gradle 文件
```

### 数据模型

在清单 5-11 中，我们定义了两个数据模型 `Post` 和 `Comment`，它们之间是一对多的关联关系。`Post` 对象作为父对象，包含一个由不同用户创建的评论列表。

```
package com.example.blog_app.model.mongo;
import lombok.*;import org.springframework.data.annotation.Id;
import java.io.Serializable;
import java.util.Set;
@Data
@Builder
@EqualsAndHashCode(exclude = {"comments"})
public class Post implements Serializable {
@Id
private String id;
private String title;
private String content;
private PostStatus postStatus;
private String blogName;
private String userName;
private Set comments;
}
package com.example.blog_app.model.mongo;
import lombok.Data;
import org.springframework.data.annotation.Id;
@Data
public class Comment {
@Id
private String id;
private String content;
public Comment(String content)   {
this.content = content;
}
}
package com.example.blog_app.model.mongo;
import java.io.Serializable;
public enum PostStatus implements Serializable {
ACTIVE(1), NOT_ACTIVE(2);
int status;
PostStatus(int status)  {
this.status = status;
}
}
清单 5-11
数据模型类
```

### 注意

我们在主键上使用了 Spring Data 的 `@Id (org.springframework.data.annotation.Id)` 注解，而不是 JPA 的 `@Id (javax.persistence.Id)`。这是 NoSQL 中 JPA 注解的等效实现。

### 数据仓库

由于我们尝试在 NoSQL 中建模基于嵌套集合的架构，因此我们使用单个集合以 JSON 格式将 `Post` 相关数据存储为 MongoDB 文档。清单 5-12 中的代码展示了该仓库。

```
package com.example.blog_app.repository;
import com.example.blog_app.model.mongo.Post;
import org.springframework.data.mongodb.repository.MongoRepository;
import java.util.List;
public interface PostMongoRepository extends MongoRepository {
Post findByTitle(String title);
List findByBlogName(String blogName);
}
清单 5-12
仓库接口
```

### 引导程序

让我们在 `Application` 类中定义引导代码（参见清单 5-13）。

```
package com.example.blog_app;
import lombok.extern.slf4j.Slf4j;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
@SpringBootApplication
@Slf4j
public class BlogApp {
public static void main(String[] args) {
SpringApplication.run(BlogApp.class, args);
}
}
清单 5-13
应用类
```

### 测试用例

在清单 5-14 中，我们通过创建一些 `Post` 和 `Comment` 对象，将它们输出到控制台，然后使用 `Assertions` 检查其中一个对象是否存在来测试此设置。

```
package com.example.blog_app;
import com.example.blog_app.model.mongo.Comment;
import com.example.blog_app.model.mongo.Post;
import com.example.blog_app.model.mongo.PostStatus;
import com.example.blog_app.repository.PostMongoRepository;
import lombok.extern.slf4j.Slf4j;
import org.junit.After;
import org.junit.Assert;
import org.junit.Before;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.ActiveProfiles;
import org.springframework.test.context.junit4.SpringRunner;
import java.util.ArrayList;
import java.util.HashSet;
import java.util.List;
import java.util.Set;
import java.util.stream.IntStream;
@RunWith(SpringRunner.class)
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.NONE)
@Slf4j
public class BlogTests {
@Autowired
PostMongoRepository postMongoRepository;
List postList = null;
@Before
public void setup()    {
postList = new ArrayList();
IntStream.range(1,2).forEach(i->{
Post nextSamplePost = createNextSamplePost(i);
postList.add(nextSamplePost);
});
}
private Post createNextSamplePost(int i) {
Post post = Post.builder()
.blogName("Blog" + i)
.title("Blog Title" + i)
.content("Blog" + i + " content")
.postStatus(PostStatus.ACTIVE)
.userName("User" + i)
.build();
Set comments = new HashSet();
comments.add(new Comment("Comment" + (i+1)) );
comments.add(new Comment("Comment" + (i+2)) );
post.setComments(comments);
return post;
}
@Test
public void testAddNewPosts()   {
postMongoRepository.saveAll(postList);
Post blogByTitle = postMongoRepository.findByTitle("Blog Title1");
Assert.assertEquals("博客名称不匹配", blogByTitle.getBlogName(), "Blog1");
log.info("成功将帖子保存到 Mongo 并从中检索回来");
}
@After
public void tearDown()  {
postMongoRepository.deleteAll();
}
}
清单 5-14
应用类
```

### 运行 Blog-Spring-Data-Mongodb-ch5 应用测试用例

按如下方式运行应用：

```
> gradle clean build
> gradle test
>
:: Spring Boot ::        (v2.1.3.RELEASE)
[main] INFO  No active profile set, falling back to default profiles: default
[main] INFO  Bootstrapping Spring Data repositories in DEFAULT mode.
[main] INFO  Finished Spring Data repository scanning in 73ms. Found 1 repository interfaces.
[main] INFO  Cluster created with settings {hosts=[localhost:27017], mode=SINGLE, requiredClusterType=UNKNOWN, serverSelectionTimeout='30000 ms', maxWaitQueueSize=500}
[cluster-ClusterId{value='5c8716f7736b80a492b4549a', description="null"}-localhost:27017] INFO  Opened connection [connectionId{localValue:1, serverValue:3}] to localhost:27017
[cluster-ClusterId{value='5c8716f7736b80a492b4549a', description="null"}-localhost:27017] INFO  Monitor thread successfully connected to server with description ServerDescription{address=localhost:27017, type=STANDALONE, state=CONNECTED, ok=true, version=ServerVersion{versionList=[4, 0, 4]}, minWireVersion=0, maxWireVersion=7, maxDocumentSize=16777216, logicalSessionTimeoutMinutes=30, roundTripTimeNanos=2877627}
[main] INFO  Started BlogTests in 2.236 seconds (JVM running for 3.284)
[main] INFO  Opened connection [connectionId{localValue:2, serverValue:4}] to localhost:27017
[main] INFO  All objects: [Post(id=5ca1c7ce0d16d26a72ee677b, title=Blog Title1, content=Blog1 content, postStatus=ACTIVE, blogName=Blog1, userName=User1, comments=[Comment(id=null, content=Comment3), Comment(id=null, content=Comment2)])]
[main] INFO  Achieved saving and retrieving back Posts from Mongo
[Thread-2] INFO  Closed connection [connectionId{localValue:2, serverValue:4}] to localhost:27017 because the pool has been closed.
```



此控制台输出显示了插入并检索到的 `Post` 对象，以及嵌套的 `Comment` 对象。

### 附加功能

Spring 框架还提供了一个基于辅助类的 API（`MongoTemplate`），它允许可编程接口比 Spring Data MongoDB 拥有更好的控制力。

在我们的示例中，我们使用了 MongoDB 连接的默认设置，但这可以通过 `@Configuration` 类进行覆盖。Spring Data API 支持的一些重要功能包括：

*   基于 Java 的查询、条件和更新 DSL

*   集成 QueryDSL 以支持类型安全的查询

*   地理空间集成

*   Map-Reduce 集成

*   JMX 管理和监控

此外，还通过 `ReactiveMongoTemplate` 和响应式 MongoDB 仓库支持响应式 MongoDB 访问。我们将在第 8 章详细讨论响应式技术。

## 总结

我们探索了使用 Spring Boot 和 Spring Data MongoDB 入门所需的基本 API。Spring 拥有众多项目，可以与几乎所有流行的 NoSQL 数据库进行交互，包括搜索技术（Solr 和 ElasticSearch）。下一章将深入探讨微服务开发。

# 6. 深入探讨使用 Java 进行微服务开发

第 2 章介绍了迁移到微服务架构的好处。在本章中，我们将深入探讨如何以 Spring Boot 为主要框架创建微服务，并重点介绍这种架构面临的挑战。我们还将通过构建多个不同的应用程序，看到结合了各种概念和库（所有这些都与 Spring Boot 集成）的解决方案。

Spring 框架有一个有趣的项目（称为 *Spring Cloud*），旨在解决分布式应用程序开发中的问题，尤其是在云部署环境中。微服务是分布式应用程序设计的一个例子，它在服务客户请求时涉及大量的协调工作。Spring Cloud 包含通用 API，可以帮助进行与云无关的微服务开发。其目的是使基于云的应用程序的开发、部署和运维管理像管理本地应用程序一样简单。让我们深入探讨微服务架构的挑战，看看 Java 框架如何处理它们：

*   服务间通信

*   配置管理

*   服务发现

*   弹性

*   请求追踪

*   微服务监控

*   文档

*   通过 API 网关实现单一入口

*   代码共享

*   安全性

我们将通过一个基于 `eshop` 示例应用程序的用例来探索这些挑战的解决方案，该应用程序自第 3 章以来一直在构建。我们将在本章中创建几个微服务：

*   `eshop`：这是面向客户的微服务，包含购买产品的端点。

*   `inventory-service`：管理不同产品的库存。

*   `config-service`：可以为 `eshop` 和 inventory 服务存储任何安全配置。

*   `admin-service`：作为已配置服务的监控服务。

*   `discovery-service`：服务的公共注册中心，服务可以在此注册，客户端可以通过服务名称发现托管这些服务的节点。

*   `zuul-service`：充当网关服务，作为所有请求到其他应用程序的单一访问点。

简单概述一下，我们将扩展在第 4 章第 4.​1 节（“与 Spring 的多数据源交互”）中构建的示例，以演示用于应对这些挑战的框架和库。在功能上，我们将以编程方式创建一个新客户，注册一些新产品，并由该客户在 `eshop` 服务中执行一个示例订单生成。

在创建订单之前，`eshop` 将通过调用另一个服务（`inventory-service`）来检查产品可用性。为了实现这一点，`eshop` 服务将使用 `discovery-service`（Eureka）注册表与 `inventory-service` 通信。我们还将看到用于与 `discovery-service` 通信的库选项，即通过 `Feign` 和 `Ribbon`。

然后，我们将通过另一个服务（称为 `admin-service`）探索对 `eshop` 和 `Inventory` 服务的监控，该服务构建在 Spring Boot Admin 之上。最后，我们将看到 `gateway-service` 的用法，它构建在 Netflix OSS Zuul 之上。

服务的目录结构如下所示：

```
.
└── spring-cloud-ch6
├─ admin-service
├─ config-service
├─ discovery-service
├─ eshop
├─ gateway-service
├─ inventory-service
```

## 服务间通信

在单体应用中，应用程序的所有组件通过语言结构或函数直接相互调用。在分布式系统中（微服务架构就是这种情况），需要解决的问题之一是进程间通信。这是过去十年推动 ESB（企业服务总线）使用的问题之一。然而，微服务架构仍然存在一个很大的不同点。使用微服务时，我们有多个服务参与处理单个请求，这在单个应用程序中通常不会发生。

当少量的大型单体应用转变为更多更小的微服务时，问题变得更加复杂。考虑一个例子，一个旅行社系统，包括一个面向客户的网站、一个分析应用程序和一个用于库存管理的内部应用程序，变成了 20 个或更多服务于相同目的的更小微服务。由于有许多小服务协作来完成单个用户请求，快速可靠地通信存在许多障碍。我们将探讨一些仍然适用于微服务间通信的进程间通信技术：

*   同步
    *   涉及调用基于 HTTP 的 API。调用者等待响应。

*   这也被称为基于请求/返回的通信。

*   异步
    *   通常涉及像 AMQP 这样的消息传递协议。调用者不等待响应。

*   这也可以通过工作流中具有拆分阶段的共享数据库状态来实现。

*   通常涉及服务之间的消息传递，可以是单个接收者（一对一；基于队列）或多个接收者（一对多；基于主题）进行消息处理。

*   这也可以仅通过 HTTP 以即发即忘的方式发生。

微服务是精确聚焦于功能的代码片段，开发者更倾向于在服务之间进行直接的端到端同步通信。以电子商务系统为例。为产品创建订单应该同步进行，而订单后处理可以异步进行，并在每个订单处理事件更新时向客户发送通知。通常，这会导致许多其他问题，我们在本章开头已经提到过。在本章中，我们仅通过同步机制来探讨解决方案。

## 配置管理



Spring Cloud Config（[`https://spring.io/projects/spring-cloud-config`](https://www.spring.io/projects/spring-cloud-config)）是 Spring Cloud（[`https://projects.spring.io/spring-cloud`](https://www.projects.spring.io/spring-cloud)）总括项目下的一个子项目。Spring Cloud Config 使我们能够将远程服务器设置为配置文件的版本化仓库，并通过 URL 或 Spring API 进行访问。换句话说，它允许我们在一个集中管理、安全且可访问的应用中覆盖或扩展任何应用的属性。配置存储理想情况下是一个 Git 仓库，并且可以在应用运行时进行修改。Spring Cloud Config 项目解决的目标如下：

*   整个系统的集中化配置管理。

*   所有应用均可通过 HTTP 访问配置。

*   配置可以存放在 Git 或 Svn 仓库中。

*   配置可以存放在数据库中。

*   配置可以动态重新加载。

*   配置可以在应用层面轻松实现安全保护，同时也可以通过加密的 REST 方式进行保护。这意味着客户端在从配置服务器访问属性时需要传递凭证，并且配置文件在 REST 传输过程中会被加密。

*   特定配置可以被覆盖。这允许我们将默认或本地配置打包在 JAR 中，并将环境或配置文件特定的配置保存在安全的 Git 仓库中。当客户端应用使用指定的配置文件运行时，本地 `application.properties` 文件中的属性将从 JAR 文件中加载，其余属性将从连接的配置服务器管理的仓库中加载。

让我们开始从 `config-service` 构建示例应用，其代码结构如下：

```
.
├── build.gradle
├── gradle
│   └── wrapper
│       ├── gradle-wrapper.jar
│       └── gradle-wrapper.properties
├── gradlew
├── gradlew.bat
└── src
└── main
├── java
│   └── com
│       └── example
│            └── config
│                 └── ConfigApp.java
└── resources
├── application.yml
└── bootstrap.yml
```

### 应用设置

从 [`http://www.spring.io`](http://www.spring.io) 创建一个名为 `config-service` 的新应用，其依赖项如 `build.gradle` 文件中的列表 6-1 所示。

```
plugins {
id 'org.springframework.boot' version '2.1.3.RELEASE'
id 'java'
}
apply plugin: 'io.spring.dependency-management'
apply plugin: 'project-report'
group = 'com.example'
version = '1.0'
sourceCompatibility = '1.8'
bootJar {
baseName = 'config-service'
version =  '1.0'
}
repositories {
mavenCentral()
maven { url 'https://repo.spring.io/milestone' }
}
dependencies {
implementation 'org.springframework.cloud:spring-cloud-config-server'
implementation 'de.codecentric:spring-boot-admin-starter-client:2.1.3'
implementation 'org.springframework.boot:spring-boot-starter-actuator'
}
dependencyManagement {
imports {
mavenBom "org.springframework.cloud:spring-cloud- dependencies:Greenwich.SR1"
}
}
列表 6-1
build.gradle 文件
```

目前，无需担心与 Spring Boot Admin 相关的 `spring-boot-admin-starter-client` 和 `spring-boot-starter-actuator` 依赖项。我将在第 6.6 节中解释这些内容。

### 配置

我们需要为此服务提供两种类型的配置：

*   在默认的 properties/yml 文件中添加所需的属性。

*   设置一个新的 Git 仓库。

#### 配置文件

Spring Cloud 项目提供了添加一个名为 `bootstrap.yml` 的新文件的功能，以便在加载 `application.yml` 或 `application.properties` 文件中的属性之前执行任何初始配置。我们将使用 `bootstrap.yml` 来设置 Git 服务器仓库的 URI 以及加密信息（如果有的话）。`bootstrap.yml` 文件在 `application.yml` 之前加载。我们在代码库中定义了这两个文件，如列表 6-2 所示。

```
bootstrap.yml
spring:
application:
name: config-service
cloud:
config:
server:
git:
uri: ${HOME}/work_all/git_repo
application.yml
server:
port: 8888
servlet.context-path: /config
spring:
boot:
admin:
client:
url: http://localhost:7777/admin
management:
endpoints:
web:
exposure:
include: "*"
列表 6-2
配置文件
```

**注意：**

*   **spring.application.name** 非常重要，因为它将用作此服务在**服务注册中心**（下一章描述）中的标识符。我将在第 6.3 节中解释其重要性。

#### Git 仓库设置

我们需要设置一个 Git 仓库，用于存储不同服务的配置文件。我在本地系统的 `/Users/raj/work_all/git_repo` 路径下进行设置。请选择您自己偏好的位置。

为了初始化一个新的 Git 仓库，我们需要进入该目录位置，并在命令行中运行 `git init` 命令。终端应显示以下输出：

```
已初始化空的 Git 仓库于 /Users/raj/work_all/git_repo/.git/
```

现在，让我们在 Git 仓库中覆盖与微服务 `prod` 配置文件相关的属性：

1.  `${HOME}/work_all/git_repo/inventory-service-prod.properties`

内容：

1.  `${HOME}/work_all/git_repo/eshop-prod.properties`

*   `spring.main.banner-mode= "off"`

*   `logging.file: logs/config-service.log`

内容：

*   `spring.main.banner-mode= "off"`

*   `logging.file: logs/inventory-service.log`

注意：

*   我们将 `eshop` 和 inventory-service 微服务的 `Prod` 配置文件相关属性存储在 Git 仓库的文件中。

*   这些文件共存于同一位置，即 `resources` 目录下。

*   `cloud.config.server.git.uri` 属性指定了仓库的位置。

*   可以暂时忽略 `spring.boot.admin and management.*` 相关的属性。我将在第 6.8 节中解释它们。

让我们运行应用，并通过 `config-service` 的 URL 访问这些配置。

### 引导启动

我们需要一个简单的 `Application` 类，并在其顶部添加一个名为 `@EnableConfigServer` 的新注解（参见列表 6-3）。

```
package com.example.config;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.config.server.EnableConfigServer;
@SpringBootApplication
@EnableConfigServer
public class ConfigApp {
public static void main(String[] args) {
SpringApplication.run(ConfigApp.class, args);
}
}
列表 6-3
应用类
```

注意：

*   `config` 服务受基本安全保护，因为它旨在供微服务内部使用，不对外暴露。这是通过 `bootstrap.yml` 中定义的 [`spring.security.user.name`](http://spring.security.user.name) 属性设置的（参见列表 6-2）。

### 运行 Config-Service 应用

运行应用：

```
> gradle bootRun
>
2019-04-02 07:28:31.317 INFO 25945 --- [          main]
com.example.config.ConfigApp             : 未设置活动配置文件，回退到默认配置文件：default
2019-04-02 07:28:32.728 INFO 25945 --- [          main]
o.s.web.context.ContextLoader            : Root WebApplicationContext: 初始化完成，耗时 1395 毫秒
2019-04-02 07:28:33.986 INFO 25945 --- [          main]
o.s.b.a.e.web.EndpointLinksResolver      : 在基础路径 '/actuator' 下暴露了 18 个端点
2019-04-02 07:28:34.110 INFO 25945 --- [          main]
o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat 已在端口 8888 (http) 上启动，上下文路径为 '/config'
2019-04-02 07:28:34.113 INFO 25945 --- [          main]
com.example.config.ConfigApp             : ConfigApp 在 3.945 秒内启动完成（JVM 运行了 4.48 秒）
2019-04-02 07:28:34.197 WARN 25945 --- [gistrationTask1]
```



暂时忽略最后一条语句（`"Failed to register application"`），我们将在第 6.6 节中对其进行修正。应用程序启动后，通过浏览器使用以下 URL 访问端点。图 6-1 和图 6-2 展示了示例输出。

![../images/454149_1_En_6_Chapter/454149_1_En_6_Fig2_HTML.jpg](img/454149_1_En_6_Fig2_HTML.jpg)

图 6-2

库存服务配置

*   `http://localhost:8888/config/eshop/prod/`

![../images/454149_1_En_6_Chapter/454149_1_En_6_Fig1_HTML.jpg](img/454149_1_En_6_Fig1_HTML.jpg)

图 6-1

Eshop 服务配置

*   `http://localhost:8888/config/inventory-service/prod`

到目前为止，我们已经运行了 `config-service` 并通过浏览器访问了它。在第 6.3 节中，我们将通过微服务 `Eshop` 和 `Inventory-service` 作为客户端来访问它。

## 微服务架构中的服务发现

当我们希望服务之间能够相互通信，而无需跟踪所有其他节点的确切网络位置或 IP 地址及端口号时，服务发现就显得尤为重要。传统上，我们基于负载均衡器的 URL 或地址来固定这种查找，并将所有应用程序节点静态附加到此负载均衡器上。Spring 框架通过几个链接库为这一需求提供了支持：

Spring ➤ Spring Cloud ➤ Spring Cloud Netflix ➤ Netflix OSS ➤ Netflix Eureka

服务发现（Netflix OSS ➤ Eureka）是提供此功能的库，Spring Cloud 在此基础上运行，使其能够在 Spring 环境中工作。服务发现充当应用级别的注册表，供客户端相互通信。每个配置好的客户端通过其应用程序名称向发现服务器注册自身。客户端只能通过该名称找到它。客户端将发现已注册的服务 IP 地址，并可以直接连接到这些节点。我们需要创建三个组件来演示此功能：

*   发现服务（Eureka 服务器）

*   库存服务（Eureka 客户端）

*   Eshop 服务（Eureka 客户端）

图 6-3 展示了 Eshop 与库存服务之间使用发现服务的通信模式。

![../images/454149_1_En_6_Chapter/454149_1_En_6_Fig3_HTML.png](img/454149_1_En_6_Fig3_HTML.png)

图 6-3

通过发现服务进行通信

### 发现服务应用程序

此微服务充当注册表，供客户端通过名称查找各种其他服务的地址，并通过内置的基于 Web 的仪表板对其进行监控。在构建此服务时，我们需要以下代码结构：

```
.
├── build.gradle
├── gradle
│   └── wrapper
│       ├── gradle-wrapper.jar
│       └── gradle-wrapper.properties
├── gradlew
├── gradlew.bat
├── settings.gradle
└── src
└── main
├── java
│   └── com
│       └── example
│            └── eshop
│                 └── discovery
│                      └── DiscoveryApp.java
└── resources
└── application.yml
```

#### 应用程序设置

从 [`http://www.spring.io`](http://www.spring.io) 创建一个名为 `discovery-service` 的新项目，并对 `build.gradle` 文件进行清单 6-4 中所示的更改。

```
bootJar {
baseName = 'discovery-service'
version =  '1.0'
}
dependencies {
implementation 'org.springframework.cloud:spring-cloud-starter-netflix- eureka-server'
implementation 'de.codecentric:spring-boot-admin-starter-client:2.1.3'
implementation 'org.springframework.boot:spring-boot-starter-actuator'
}
清单 6-4
build.gradle 文件
```

#### 配置

清单 6-5 中定义的大多数属性都是通用的，我们之前已经见过。我将在清单之后解释新的属性。

```
application.yml
server:
port: 8761
eureka:
instance:
hostname: localhost
client:
registerWithEureka: false
fetchRegistry: false
serviceUrl:
defaultZone: http://${eureka.instance.hostname}:${server.port}/ eureka/
spring:
application:
name: discovery-service
boot:
admin:
client:
url: http://localhost:7777/admin
management:
endpoints:
web:
exposure:
include: "*"
清单 6-5
配置文件
```

注意：

*   `eureka.instance.hostname` 属性表示要绑定 Eureka 注册服务器的主机名。这也可以是域名或暴露的 IP 地址。

*   服务器模式下的 `eureka.client.*` 属性在集群模式下部署时使用。如果有多个节点，发现服务可以动态连接，并且也可以复制注册表。我们将跳过这方面的更多细节，因为它们超出了本章的范围。

*   作为默认约定，服务通过 `server.port` 配置为在端口 8761 上运行。

*   最后，`spring.boot.admin` 和 `management.*` 现在可以忽略，它们将在第 6.6 节中解释。

#### 引导

最后一步是引导应用程序。我们需要一个额外的注解，如清单 6-6 所示，称为 `@EnableEurekaServer`，以启用 Eureka 服务器支持。

```
package com.example.eshop.discovery_service;
import org.springframework.boot.WebApplicationType;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.builder.SpringApplicationBuilder;
import org.springframework.cloud.netflix.eureka.server.EnableEurekaServer;
@SpringBootApplication
@EnableEurekaServer
public class DiscoveryApp {
public static void main(String[] args) {
new SpringApplicationBuilder(DiscoveryApp.class).web(WebApplicationType.SER VLET).run(args);
}
}
清单 6-6
应用程序类
```

启动此应用程序后，Eureka 仪表板将可通过 `http://localhost:8761/` 访问。我们将在第 6.3.3.5 节中再次讨论此仪表板。

#### 运行发现服务应用程序

运行应用程序：

```
>gradle bootRun
>
:: Spring Boot ::        (v2.1.3.RELEASE)
2019-04-02 07:52:22.176   INFO 30422 --- [           main]
c.example.eshop.discovery.DiscoveryApp    : No active profile set, falling back to default profiles: default
com.netflix.config.ConcurrentCompositeConfiguration@fb5aeed
2019-04-02 07:52:25.816   INFO 30422 --- [           main]
o.s.b.a.e.web.EndpointLinksResolver       : Exposing 20 endpoint(s) beneath base path '/actuator'
2019-04-02 07:52:25.909   INFO 30422 --- [           main]
o.s.c.n.e.s.EurekaServiceRegistry         : Registering application DISCOVERY- SERVICE with eureka with status UP
2019-04-02 07:52:25.912   INFO 30422 --- [      Thread-12]
o.s.c.n.e.server.EurekaServerBootstrap    : Setting the eureka configuration..
2019-04-02 07:52:25.913   INFO 30422 --- [      Thread-12]
o.s.c.n.e.server.EurekaServerBootstrap    : Eureka data center value
eureka.datacenter is not set, defaulting to default
2019-04-02 07:52:25.913   INFO 30422 --- [      Thread-12]
o.s.c.n.e.server.EurekaServerBootstrap    : Eureka environment value eureka.environment is not set, defaulting to test
2019-04-02 07:52:26.015   INFO 30422 --- [           main]
o.s.b.w.embedded.tomcat.TomcatWebServer   : Tomcat started on port(s): 8761 (http) with context path"
2019-04-02 07:52:26.016   INFO 30422 ---
[           main] .s.c.n.e.s.EurekaAutoServiceRegistration : Updating port to 8761
2019-04-02 07:52:26.021   INFO 30422 --- [           main]
c.example.eshop.discovery.DiscoveryApp    : Started DiscoveryApp in 4.934 seconds (JVM running for 5.675)
```

### 库存服务应用程序



我们将创建一个基于内存数据库（H2）的应用，用于管理已注册产品的数量和定价。我们还将暴露一个端点，供客户端查询产品的当前数量。构建此服务需要以下代码结构：

```
.
├── build.gradle
├── gradle
│   └── wrapper
│       ├── gradle-wrapper.jar
│       └── gradle-wrapper.properties
├── gradlew
├── gradlew.bat
├── settings.gradle
└── src
    └── main
        ├── java
        │   └── com
        │       └── example
        │            └── eshop
        │                 └── inventory
        │                      ├── InventoryApp.java
        │                      ├── model
        │                      │    └── Inventory.java
        │                      ├── repository
        │                      │    └── InventoryRepository.java
        │                      ├── service
        │                      │    └── InventoryService.java
        │                      └── web
        │                           └── InventoryController.java
        └── resources
            ├── application.yml
            └── bootstrap.yml
```

#### 应用设置

从 [`http://www.spring.io`](http://www.spring.io) 创建一个名为 `inventory-service` 的新应用，并对 `build.gradle` 文件进行修改，如清单 6-7 所示。

```
bootJar  {
    baseName = 'inventory-service'
    version =  '1.0'
}
dependencies {
    compile ([
        "org.springframework.boot:spring-boot-starter-web",
        "org.springframework.boot:spring-boot-starter-data-jpa",
        "org.springframework.boot:spring-boot-starter-test",
        "org.projectlombok:lombok:1.18.6",
        "org.springframework.cloud:spring-cloud-starter-netflix-eureka-client",
        "org.springframework.cloud:spring-cloud-starter-config"
    ])
    compile("org.springframework.cloud:spring-cloud-starter-sleuth")
    compile("de.codecentric:spring-boot-admin-starter-client:2.1.3")
    compile("org.springframework.boot:spring-boot-starter-actuator")
    runtime("com.h2database:h2")
    testCompile("junit:junit")
}
清单 6-7
build.gradle 文件
```

#### 配置

我们在 `bootstrap.yml` 文件中定义了与 `config-service` 相关的配置属性，其余属性定义在 `application.yml` 中（参见清单 6-8）。

```
bootstrap.yml
spring:
  application:
    name: inventory-service
  profiles:
    active: dev
  cloud:
    config:
      uri: http://localhost:8888/config

application.yml
server:
  servlet:
    contextPath: /inventory
  port: 8082
spring:
  jpa:
    hibernate.ddl-auto: create
    show-sql: true
    generate-ddl: true
  datasource:
    name: inventorydb
  boot:
    admin:
      client:
        url: http://localhost:7777/admin
management:
  endpoints:
    web:
      exposure:
        include: "*"
eureka:
  client:
    serviceUrl:
      defaultZone: ${EUREKA_URI:http://localhost:8761/eureka}
  instance:
    preferIpAddress: false
spring.main.allow-bean-definition-overriding: true
清单 6-8
配置文件
```

说明：

*   在 `bootstrap.yml` 文件中，我们定义了 Spring Cloud Config 相关属性，用于连接到位于 `http://localhost:8888/config` 的 `config-service`。
*   在 `application.yml` 文件中，我们定义了 `contentPath`、端口以及 JPA 相关属性，用于连接到内存数据库 H2。
*   我们还定义了 Spring Boot Admin 相关属性，由 `spring.boot.*` 和 `management.*` 表示，这些属性暂时可以忽略，稍后会进行说明。
*   此外，我们还有 Eureka 客户端相关属性（`eureka.client.*`），这些属性会将此服务注册到发现服务。库存服务应能够连接到位于 `http://localhost:8761/eureka` 的 `discovery-service`。该位置被定义为一个键值对，例如 `defaultZone: ${EUREKA_URI:http://localhost:8761/eureka}`。
*   我们有一个名为 `spring.main.allow-bean-definition-overriding` 的新属性。这是 Spring Boot 2.1.0 版本之后为了覆盖任何 Bean 属性而需要的。我们使用此属性是因为我们正在覆盖 JPA 和 Eureka 客户端相关的属性。

#### 完整应用代码

我们需要一个 `Inventory` 类作为数据模型，以及一个 `InventoryService` 类来管理其 CRUD 操作（清单 6-9 和 6-10）。除了这两个类之外，我们还需要一个 REST 控制器，以便其他服务通过它访问 CRUD 操作（参见清单 6-12）。

```
package com.example.eshop.inventory.service;

import com.example.eshop.inventory.model.Inventory;
import com.example.eshop.inventory.repository.InventoryRepository;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.Optional;

@Service
@Slf4j
public class InventoryService {

    @Autowired
    InventoryRepository inventoryRepository;

    public Inventory getInventory(Long productId) {
        log.info("Inventory lookup request for productId: {}",
                productId);
        Optional<Inventory> inventory = inventoryRepository.findById(productId);
        return inventory.orElse(Inventory.builder()
                .inventoryId(1l)
                .productId(productId)
                .price(200)
                .quantity(2)
                .build());
    }
}
清单 6-10
包含业务逻辑的服务类
```

```
package com.example.eshop.inventory.model;

import lombok.Builder;
import lombok.Data;

import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.Id;
import javax.persistence.Table;

@Entity
@Data
@Builder
@Table(name = "Inventory")
public class Inventory {
    @Id
    @GeneratedValue
    private Long inventoryId;
    Long productId;
    Integer price;
    Integer quantity;
}
清单 6-9
模型类
```

在这段代码中，我们查询数据库，要么获取一个产品，要么使用 `Optional` 构建一个产品。这样，在示例运行时，我们可能需要在数据库中手动插入条目。我们只返回了一个产品，其数量设置为 2，价格为 200 美元。或者，我们可以定义 `InventoryRepository` 来连接到 H2 数据库，如清单 6-11 所示。如果数据被插入到 H2 表中，清单 6-10 中的 `InventoryService` 将返回数据库中的数据。

```
package com.example.eshop.inventory.repository;

import com.example.eshop.inventory.model.Inventory;
import org.springframework.data.jpa.repository.JpaRepository;

public interface InventoryRepository extends JpaRepository<Inventory, Long> { }
清单 6-11
数据仓库
```

清单 6-12 包含了通过 HTTP 访问 CRUD 操作所需的 `REST` 控制器类。我们有一个 `GET` API，它接收 `productId` 并返回该 `productId` 的库存详细信息。

```
package com.example.eshop.inventory.web;

import com.example.eshop.inventory.model.Inventory;
import com.example.eshop.inventory.service.InventoryService;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RestController;

@RestController
@Slf4j
public class InventoryController {

    @Autowired
    InventoryService inventoryService;

    @GetMapping("/api/inventory/{productId}")
    public Inventory getInventoryForProduct(@PathVariable("productId")
                                                    Long productId) {
        log.info("Inventory request for product: {}", productId);
        Inventory inventory = inventoryService.getInventory(productId);
        log.info("inventory : {}", inventory);
        return inventory;
    }
}

package com.example.eshop.inventory;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class InventoryApp {
    public static void main(String[] args) {
        SpringApplication.run(InventoryApp.class, args);
    }
}
清单 6-12
REST 控制器类
```

说明：



*   `InventoryController` 暴露了一个 URI 设置为 `/api/inventory/{productId}` 的端点。库存数据模型包含价格和数量，这些数据可以通过 JPA 存储在内存数据库 H2 中。

*   `InventoryController` 类充当 RESTful Web 资源，并包含一个 `GET` 动词方法，用于获取产品当前的可用库存。

*   关于是否通过注解在 Spring 中使用自动装配，存在不同观点。我认为，除非应用程序是单体应用或足够庞大，否则这不成问题。当应用程序非常大且字段注入没有谨慎进行时，这曾经会引发问题，从而导致上帝对象（类实例过大）。

#### 运行库存服务应用程序

让我们通过 IDE 以配置文件设置为 `Prod` 的方式运行该服务。我们需要在运行此 `Dspring.profiles.active=prod` 时传递一个 VM 参数。

```
> gradle bootRun
>
:: Spring Boot ::        (v2.1.3.RELEASE)
2019-04-02 08:11:37.331   INFO [inventory-service,,,] 34042 --- [            main]
c.c.c.ConfigServicePropertySourceLocator : Fetching config from server at : http:// localhost:8888/config/
2019-04-02 08:11:38.412   INFO [inventory-service,,,] 34042 --- [            main]
c.c.c.ConfigServicePropertySourceLocator : Located environment: name=inventory- service, profiles=[prod], label=null,
version=70e66d9b8852ee1ba8511dbfcf4c6a245b87cbcc, state=null
2019-04-02 08:11:38.412   INFO [inventory-service,,,] 34042 --- [            main]
c.example.eshop.inventory.InventoryApp   : The following profiles are active: prod
2019-04-02 08:11:39.774   INFO [inventory-service,,,] 34042 --- 2019-04-02
08:11:41.457   INFO [inventory-service,,,] 34042 --- [           main]
o.s.b.w.embedded.tomcat.TomcatWebServer   : Tomcat initialized with port(s): 8082 (http)
2019-04-02 08:11:41.499   INFO [inventory-service,,,] 34042 --- [            main]
o.apache.catalina.core.StandardService    : Starting service [Tomcat]
o.s.b.a.e.web.EndpointLinksResolver       : Exposing 21 endpoint(s) beneath base path '/actuator'
2019-04-02 08:11:47.334   INFO [inventory-service,,,] 34042 --- [            main]
com.netflix.discovery.DiscoveryClient     : Getting all instance registry info from the eureka server
2019-04-02 08:11:48.161   INFO [inventory-service,,,] 34042 --- [            main]
com.netflix.discovery.DiscoveryClient     : The response status is 200
2019-04-02 08:11:48.164 INFO [inventory-service,,,] 34042 --- [            main]
com.netflix.discovery.DiscoveryClient     : Starting heartbeat executor: renew interval is: 30
2019-04-02 08:11:48.167   INFO [inventory-service,,,] 34042 --- [            main]
DiscoveryClient_INVENTORY-SERVICE/192.168.1.3:inventory-service:8082: registering service...
2019-04-02 08:11:48.235   INFO [inventory-service,,,] 34042 --- [nfoReplicator-0]
com.netflix.discovery.DiscoveryClient     : DiscoveryClient_INVENTORY-SERVICE/
192.168.1.3:inventory-service:8082 - registration status: 204
2019-04-02 08:11:48.290   INFO [inventory-service,,,] 34042 --- [            main]
o.s.b.w.embedded.tomcat.TomcatWebServer   : Tomcat started on port(s): 8082 (http) with context path '/inventory'
```

请注意输出中以粗体显示的行；它们验证了与配置和发现服务的连接。我们还可以从浏览器访问库存服务 URL 以查看响应（`http://localhost:8082/inventory/api/inventory/1`），或在控制台上使用 `CURL` 命令：

```
> curl http://localhost:8082/inventory/api/inventory/1
{"inventoryId": 1,"productId": 1,"price": 200,"quantity": 2}
```

### Eshop 应用程序

该微服务拥有允许客户查看产品列表和下单的端点。理想情况下，在企业级设置中，您应该为客户管理、产品目录和订单管理设置不同的服务。我们将定义一个 `TestController`，借助一些业务服务来展示示例订单流程。

这些服务中最重要的是 `OrderService`，它将与另一个微服务（库存服务）通信，然后根据响应决定哪些产品可用。如果产品不可用，`OrderService` 会执行事务并返回订单对象及其在标头中的位置。`CustomerService` 和 `ProductService` 包含创建示例客户和产品的方法。

为了与库存服务通信，`Eshop` 可以采用多种方法：

*   Netflix OSS – 使用 REST 模板的 Eureka 客户端

Eureka 客户端库通过名为 `eureka.client.serviceUrl.defaultZone` 的配置属性提供查找功能。这提供了所有实例的列表，可用于连接到其中一个实例。

*   Netflix OSS – 使用 REST 模板的 Ribbon

Ribbon 还提供对 Eureka 服务器的配置、查找和连接管理。Ribbon 在某种程度上与 Eureka 客户端不同，它基于几种算法提供自动负载均衡，而不是从列表中手动选择一个。

*   Netflix OSS – Feign

这是一个基于接口的客户端，其目标 URI 作为声明注解在其上。Feign 内部仅使用 Ribbon API。

*   Spring Cloud API 抽象通过 `@LoadBalanced` 注解以及 `DiscoveryClient` 类，为基于 EurekaClient 和 Ribbon 的方法提供了简单的解决方案。EurekaClient 内部使用 Jersey API，而 Spring Cloud 的 `org.springframework.cloud.client.discovery.DiscoveryClient` 可用于 Spring 首选的 HTTP 通信 API。

我们在服务部分讨论来自 Spring Cloud `@LoadBalanced` 注解和 Feign 的简单选项。我们需要以下代码结构来构建此应用程序：

```
.
├── build.gradle
├── gradle
│   └── wrapper
│       ├── gradle-wrapper.jar
│       └── gradle-wrapper.properties
├── gradlew
├── gradlew.bat
└── src
└── main
├── java
│   └── com
│       └── example
│            └── eshop
│                 ├── EshopApp.java
│                 ├── config
│                 │    ├── SwaggerConfig.java
│                 │    └── WebConfig.java
│                 ├── dto
│                 │    └── LoginRequest.java
│                 ├── http
│                 │    └── InventoryClient.java
│                 ├──  model
│                 │    ├── Customer.java
│                 │    ├── Order.java
│                 │    └── Product.java
│                 ├──  repository
│                 │    ├── CustomerRepository.java
│                 │    ├── OrderRepository.java
│                 │    └── ProductRepository.java
│                 ├──  service
│                 │    └── impl
│                 │         ├── CustomerService.java
│                 │         ├── OrderService.java
│                 │         ├── OrderServiceWithFiegn.java
│                 │         └── ProductService.java
│                 └── web
│                      ├── LoginController.java
│                      └── TestController.java
└── resources
├── application.properties
└── application.yml
```

#### 应用程序设置

从 [`http://www.spring.io`](http://www.spring.io) 创建一个名为 `eshop` 的新应用程序，并对 `build.gradle` 文件进行清单 6-13 中所示的更改。



```
bootJar {
baseName = 'eshop'
version = '1.0'
}
dependencies {
compile ([
"javax.xml.bind:jaxb-api:2.3.1",
"org.glassfish.jaxb:jaxb-runtime:2.3.2",
"javax.activation:javax.activation-api:1.2.0"
])
compile ([
"org.projectlombok:lombok:1.18.6",
"mysql:mysql-connector-java:8.0.15",
"org.springframework.boot:spring-boot-starter-web",
"org.springframework.boot:spring-boot-starter-data-jpa",
"org.springframework.boot:spring-boot-starter-test",
"org.springframework.cloud:spring-cloud-starter-sleuth",
"org.springframework.boot:spring-boot-starter-actuator"
])
compile ([
"org.springframework.cloud:spring-cloud-starter-openfeign",
"org.springframework.cloud:spring-cloud-starter-netflix-eureka-client",
"org.springframework.cloud:spring-cloud-starter-netflix-hystrix",
"org.springframework.cloud:spring-cloud-starter-netflix-hystrix-dashboard"
])
compile ([
"de.codecentric:spring-boot-admin-starter-client:2.1.3",
"io.springfox:springfox-swagger2:2.9.2",
"io.springfox:springfox-swagger-ui:2.9.2"
])
testCompile("junit:junit")
}
清单 6-13
build.gradle 文件
```

#### 配置

此配置文件中的大部分属性与我们之前在 `inventory-service` 中定义的类似。清单 6-14 展示了该文件。

```
application.yml
server:
servlet:
context-path: /eshop
#port: 0
port: 8081
spring:
application:
name: eshop
datasource:
url: jdbc:mysql://localhost:3306/db1
username: root
password: mysql
driver-class-name: com.mysql.jdbc.Driver
jpa:
hibernate.ddl-auto: update
show-sql: true
generate-ddl: true
boot:
admin:
client:
url: http://localhost:7777/admin
spring.jpa.hibernate.ddl-auto: update
eureka:
client:
serviceUrl:
defaultZone: ${EUREKA_URI:http://localhost:8761/eureka}
instance:
preferIpAddress: false
management:
endpoints:
web:
exposure:
include: "*"
清单 6-14
配置文件
```

#### 数据模型、仓库和控制器类

在此步骤中，我们定义三个组件：

*   模型（清单 6-15）

*   仓库（清单 6-16）

*   控制器（清单 6-17）

让我们从领域模型开始，如清单 6-15 所示。

```
package com.example.eshop.model;
import lombok.Data;
import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;
import java.io.Serializable;
@Data
@Entity
public class Customer implements Serializable {
@Id
@GeneratedValue(strategy=GenerationType.IDENTITY)
private Long customerId;
private String name, email, password;
}
package com.example.eshop.model;
import lombok.Data;
import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;
import java.io.Serializable;
@Data
@Entity
public class Product implements Serializable {
@Id
@GeneratedValue(strategy=GenerationType.IDENTITY)
private Long productId;
private String name;
private Integer price, quantity;
}
package com.example.eshop.model;
import java.io.Serializable;
import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;
import javax.persistence.Table;
import lombok.*;
@Data
@Entity
@Table(name="`Order`")
public class Order implements Serializable {
@Id
@GeneratedValue(strategy=GenerationType.IDENTITY)
private Long orderId;
private Long productId, customerId;
private int quantity, price;
}
清单 6-15
领域模型类
```

这些模型中的字段集非常精简。`order` 对象在 `@Table` 注解中必须用 `` ` `` 字符括起来，因为 `Order` 是 MySQL 中的关键字。仓库接口定义如清单 6-16 所示。

```
package com.example.eshop.repository;
public interface CustomerRepository extends JpaRepository{ }
public interface ProductRepository extends JpaRepository { }
public interface OrderRepository extends JpaRepository{ }
清单 6-16
仓库接口
```

我们不需要任何自定义查询，因此清单 6-16 中的仓库被定义为空接口。我们定义 `Controller` 类，如清单 6-17 所示。

```
package com.example.eshop.web;
import com.example.eshop.model.Order;
import com.example.eshop.service.impl.CustomerService;
import com.example.eshop.service.impl.OrderService;
import com.example.eshop.service.impl.ProductService;
import lombok.extern.slf4j.Slf4j;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.servlet.support.ServletUriComponentsBuilder;
import java.net.URI;
import java.util.Objects;
@RestController
@Slf4j
public class TestController {
@Autowired
private OrderService orderService;
@Autowired
private ProductService productService;
@Autowired
private CustomerService customerService;
@PostMapping(value = "/api/orders", produces = "application/json")
public ResponseEntity purchaseSampleProduct()  throws Exception {
customerService.registerNewCustomers();
productService.registerNewProducts();
Order order = orderService.orderProduct();
log.info("Order status: {}", Objects.isNull(order));
URI uri = ServletUriComponentsBuilder.fromCurrentRequest().path("/{id}")
.buildAndExpand(order.getOrderId()).toUri();
return ResponseEntity.created(uri).build();
}
}
清单 6-17
控制器类
```

注意：

*   我们有一个端点，其 URI 路径设置为 `/api/orders`。

*   `TestController` 类中的 `purchaseSampleProduct()` 方法将控制权委托给客户服务和产品服务，以创建一些示例对象。

*   最后，我们调用 `OrderService` 来创建订单，并返回 HTTP 状态码设置为 201 的成功响应。理想情况下，我们还应该返回 `order` 对象的 URL，但为了保持简单，我们在此省略了。

#### 服务类

我们将从定义 `CustomerService` 和 `ProductService` 类开始，它们仅包含一些简单且不言自明的方法，用于创建示例 `Customer` 和 `Product` 对象。请参见清单 6-18。

```
package com.example.eshop.service.impl;
import com.example.eshop.model.Customer;
import com.example.eshop.repository.CustomerRepository;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Isolation;
import org.springframework.transaction.annotation.Propagation;
import org.springframework.transaction.annotation.Transactional;
@Service
@Slf4j
public class CustomerService {
@Autowired
CustomerRepository customerRepository;
@Transactional(propagation=Propagation.REQUIRED, isolation=Isolation.DEFAULT)
public void registerNewCustomers() {
Customer customer = new Customer();
customer.setName("Raj Malhotra");
customer.setEmail("raj.malhotra@example.com");
customer.setPassword("password");
customerRepository.saveAndFlush(customer);
}
}
package com.example.eshop.service.impl;
import com.example.eshop.model.Product;
import com.example.eshop.repository.ProductRepository;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
@Service
@Slf4j
public class ProductService {
@Autowired
ProductRepository productRepository;
public void registerNewProducts() {
Product product = new Product();
product.setName("Superb Java");
product.setPrice(400);
product.setQuantity(3);
productRepository.save(product);
}
}
清单 6-18
服务类
```



#### 使用 Eureka 客户端的订单服务实现

我们需要在代码中添加三部分内容来启用 Netflix Eureka 客户端：

*   配置以声明负载均衡的 REST 模板

*   定义 `OrderService` 类

*   在 `Application` 类上添加 `@EnableDiscoveryClients` 注解

我们只需要一个 Bean 定义来声明 REST 模板，并在其上方添加 `@LoadBalanced` 注解。该代码如代码清单 6-19 所示。

```
package com.example.eshop.config;
import org.springframework.cloud.client.loadbalancer.LoadBalanced;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.client.RestTemplate;
@Configuration
public class WebConfig {
@Bean
@LoadBalanced
public RestTemplate restTemplate() {
return new RestTemplate();
}
}
代码清单 6-19
负载均衡 REST 模板的 Web 配置
```

Spring 有多个选项可以连接到 `EurekaServer`：

*   原生 EurekaClient (`com.netflix.discovery.EurekaClient`)

*   DiscoveryClient (`org.springframework.cloud.client.discovery.DiscoveryClient`)

*   RestTemplate

最简单的选项是使用 `RestTemplate`，它与 `EurekaClient` 和 `Ribbon`（负载均衡）API 集成在一起。

现在让我们定义 `OrderService` 并查看如何使用 Eureka 客户端（参见代码清单 6-20）。

```
package com.example.eshop.service.impl;
import com.example.eshop.http.InventoryClient;
import com.example.eshop.model.Order;
import com.example.eshop.repository.OrderRepository;
import com.fasterxml.jackson.databind.ObjectMapper;
import com.netflix.hystrix.contrib.javanica.annotation.HystrixCommand;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import org.springframework.web.client.RestTemplate;
import java.util.Map;
@Service
@Slf4j
public class OrderService {
@Autowired
ProductService productService;
@Autowired
OrderRepository orderRepository;
@Autowired
@LoadBalanced
RestTemplate restTemplate;
@Autowired
InventoryClient inventoryClient;
public Order orderProduct()    {
Order order = null;
Map map = null;
ObjectMapper mapper = new ObjectMapper();
String resultJson =
this.restTemplate
.getForObject("http://INVENTORY-SERVICE/
inventory/api/inventory/" + 1, String.class);
try {
map = mapper.readValue(resultJson.getBytes(),
HashMap.class);
log.info("Result from inventory service: {}", map);
}catch(Exception e) {
log.error("Error while reading back json value: {}", map);
return null;
}
Integer qty = map.get("quantity");
if(qty >=2)
order = createOrder(1l, 1l, 2, 400);
log.info("Orders {}", orderRepository.findAll());
return order;
}
public Order createOrder(Long productId, Long customerId, int
quantity, int price) {
Order order = new Order();
order.setCustomerId(customerId);
order.setProductId(productId);
order.setPrice(price);
order.setQuantity(quantity);
order = orderRepository.save(order);
return order;
}
private Order handleInventoryFailure() {
log.error("Cannot connect to inventory service with 20%
requests failing in 10 seconds interval");
return null;
}
}
代码清单 6-20
使用 Eureka 客户端的 OrderService
```

注意：

*   我们编写了 `RestTemplate` 代码，通过其在发现服务（Eureka 服务器）中注册的服务名称（`INVENTORY-SERVICE`）与库存服务进行通信。

*   收到库存服务的响应后，我们使用 Jackson 的 `Object` 映射器将返回的 JSON 字符串转换为 `java.util.Map` 对象。

*   `RestTemplate` 中还有更多方法可以直接获取 `Object` 返回类型。我们采用这种方法是为了简单起见。

最后，我们需要在 `Application` 类上添加 `@EnableDiscoveryClient` 注解（参见代码清单 6-21），以在应用程序中启用 Eureka 客户端功能。

```
package com.example.eshop;
import lombok.extern.slf4j.Slf4j;
import org.springframework.boot.CommandLineRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.circuitbreaker.EnableCircuitBreaker;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
import org.springframework.cloud.openfeign.EnableFeignClients;
@SpringBootApplication
@EnableDiscoveryClient
@Slf4j
public class EshopApp {
public static void main(String[] args) throws Exception {
SpringApplication.run(EshopApp.class, args);
}
}
代码清单 6-21
Application 类
```

##### 运行 Eshop 应用程序

运行应用程序：

```
> gradle bootRun
>
:: Spring Boot ::        (v2.1.3.RELEASE)
2019-04-02 08:42:32.698   INFO [eshop,,,] 39989 --- [           main]
com.example.eshop.EshopApp                : No active profile set, falling back to default profiles: default
2019-04-02 08:42:40.095   INFO [eshop,,,] 39989 --- [           main]
o.s.b.a.e.web.EndpointLinksResolver       : Exposing 21 endpoint(s) beneath base path '/actuator'
2019-04-02 08:42:41.768   INFO [eshop,,,] 39989 --- [           main]
com.netflix.discovery.DiscoveryClient     : Initializing Eureka in region us-east-1
o.s.c.n.e.s.EurekaServiceRegistry         : Registering application ESHOP with eureka with status UP
2019-04-02 08:42:42.612   INFO [eshop,,,] 39989 --- [           main]
com.netflix.discovery.DiscoveryClient     : Saw local status change event
StatusChangeEvent [timestamp=1554174762612, current=UP, previous=STARTING]
2019-04-02 08:42:42.877   INFO [eshop,,,] 39989 --- [           main]
o.s.b.w.embedded.tomcat.TomcatWebServer   : Tomcat started on port(s): 8081 (http) with context path '/eshop'
```

我们已成功在端口 `8081` 上启动了 `eshop` 服务。我们还有另外三个服务以独立模式运行——`config-service`、`discovery-service` 和 `inventory-service`。现在，我们可以从终端使用 `CURL` 访问 `eshop` 服务的订单创建 API：

```
> curl -H "Content-Type: application/json" -X POST -i http://localhost:8081/eshop/api/orders
HTTP/1.1 201
Location: http://localhost:8081/eshop/api/orders/1
Content-Length: 0
Date: Tue, 02 Apr 2019 03:28:35 GMT
```

此输出显示了来自 `eshop` 服务的成功响应。该请求也会隐式地命中 `inventory-service`，并且以下日志语句会打印在 `inventory-service` 的控制台上。

```
c.e.e.i.service.InventoryService   : Inventory lookup request for productId: 1
c.e.e.inventory.web.InventoryController   : inventory : Inventory(inventoryId=1, productId=1, price=200, quantity=2)
```

通过查看这两个服务的输出，我们可以确认 `eshop` 和 `inventory-service` 之间的调用。`inventory-service` 控制台上的日志语句“`Inventory lookup request for productId: 1`”证实了这一点。

##### 发现服务仪表盘

我们还可以在浏览器中查看第 6.3.1 节提到的、由 Eureka 服务器（`discovery-service`）提供的仪表盘。地址栏应输入 `http://localhost:8761/`。

从图 6-4 的输出中，我们可以看到 `eshop` 和 `inventory-service` 已成功注册，并在 `discovery-service` 的 Web 仪表盘上以绿色显示。如果任何服务或服务的某个节点宕机，仪表盘上将以红色显示。

![../images/454149_1_En_6_Chapter/454149_1_En_6_Fig4_HTML.jpg](img/454149_1_En_6_Fig4_HTML.jpg)

图 6-4

Eureka 服务器仪表盘



#### 使用 Feign 实现订单服务

如第 6.3.3 节开头所述，我们将再次构建同一个 `OrderService`，但这次采用基于 **Feign** 的实现。为此，我们需要额外添加三项内容，如代码清单 6-22 所示：

*   一个新的接口，用于声明 Feign 客户端的远程 URI
*   在 `Application` 类上添加 `@EnableFeignClients` 注解
*   `OrderServiceWithFeign` 类

```
Feign 接口 :
package com.example.eshop.http;
import org.springframework.cloud.openfeign.FeignClient;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import java.util.HashMap;
@FeignClient("INVENTORY-SERVICE")
public interface InventoryClient {
@RequestMapping(method = RequestMethod.GET, value = "/inventory/api/inventory/{productId}")
HashMap getInventory(@PathVariable("productId") Long productId);
}
Application 类变更 :
package com.example.eshop;
import lombok.extern.slf4j.Slf4j;
import org.springframework.boot.CommandLineRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
import org.springframework.cloud.openfeign.EnableFeignClients;
@SpringBootApplication
@EnableFeignClients
@EnableDiscoveryClient
@Slf4j
public class EshopApp {
public static void main(String[] args) throws Exception {
SpringApplication.run(EshopApp.class, args);
}
}
新的 OrderService 变更 :
package com.example.eshop.service.impl;
import com.example.eshop.http.InventoryClient;
import com.example.eshop.model.Order;
import com.example.eshop.repository.OrderRepository;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import java.util.Map;
@Service
@Slf4j
public class OrderServiceWithFiegn {
@Autowired
OrderRepository orderRepository;
@Autowired
InventoryClient inventoryClient;
public Order orderProduct()    {
Order order = null;
Map map = inventoryClient.getInventory(1l);
log.info("Result from inventory service through feign: {}", map);
Integer qty = map.get("quantity");
if(qty >=2)
order = createOrder(1l, 1l, 2, 400);
log.info("Orders {}", orderRepository.findAll());
return order;
}
}
代码清单 6-22
服务 API 代码
```

说明：

*   在 `InventoryClient` 中，我们声明了 Feign 注解（`@FeignClient("INVENTORY- SERVICE")`**），并指定了 Eureka 服务器（`discovery-service`）中目标已注册服务的准确服务名称。
*   我们在 `Application` 类上添加了注解（`@EnableFeignClients`）。
*   接着，我们通过自动装配将 `InventoryClient` 注入到服务类中。
*   仅此而已。其余代码与上一节的 `OrderService` 几乎相同。
*   我们能看到的明显区别是，由于使用了声明式 REST 客户端，代码更简洁、更清晰。

## 微服务架构中的弹性

微服务架构有助于将问题分解为多个部分，因此单个请求可能会因一个用例而流经多个请求。这给终端客户端带来了新的系统故障风险，因为请求流中的任何一个服务都可能发生故障。Netflix OSS Hystrix 通过优雅地处理此类故障来提供帮助。这基于众所周知的断路器模式。让我们通过我们在第 6.3.3 节中创建的 `Eshop` 服务来了解这个示例。Hystrix 还提供了一个基于 Web 的仪表板，用于监控应用程序。

回顾一下，我们在 `eshop` 服务中创建了 `TestController`，它调用 `OrderService` 类。`OrderService` 进一步调用 `inventory-service` 来检查给定 `productId` 的库存。

现在，假设 `inventory-service` 由于系统崩溃而停止响应，或者由于数据库无响应而开始抛出错误。在这两种情况下，`TestController` 都会一直将所有请求发送到同一个出现故障的 `inventory-service`。有了 Hystrix，我们可以配置调用服务来检测调用中的连续故障，并添加一个备用方法，以便在这种情况下被调用。让我们看一个代码示例。

我们需要在 `eshop` 服务的 `build.gradle` 中添加一个依赖项：

```
compile("org.springframework.cloud:spring-cloud-starter-netflix-hystrix")
```

我们还需要对 `OrderService` 和 `Application` 类进行更改，如代码清单 6-23 所示。

```
public class OrderService {
@HystrixCommand(
fallbackMethod = "handleInventoryFailure",
commandProperties = {
@HystrixProperty(name =
"execution.isolation.thread.timeoutInMilliseconds", value = "3000"),
@HystrixProperty(name = "circuitBreaker.requestVolumeThreshold", value ="2")
})
public Order orderProduct()   {
String resultJson = this.restTemplate.getForObject("http://INVENTORY-SERVICE/inventory/api/inventory/" + 1, String.class);
// 跳过重复的其余代码
}
private Order handleInventoryFailure() {
log.error("Inventory Service is responding very slow");
return null;
}
}
@SpringBootApplication
@EnableFeignClients
@EnableDiscoveryClient
@Slf4j
@EnableCircuitBreaker
@EnableHystrixDashboard
public class EshopApp {
public static void main(String[] args) throws Exception {
SpringApplication.run(EshopApp.class, args);
}
}
代码清单 6-23
服务类
```

说明：

*   如果 `orderProduct()` 方法由于 `inventory-service` 响应缓慢而开始耗时过长，Hystrix 库将调用替代方法（`handleInventoryFailure()`）作为备用方案。
*   默认情况下，此 `TimeOut` 间隔为 10 秒，可以通过设置 Hystrix 属性来更改，正如我们在代码中所做的那样（将其更改为 3000 毫秒，即 3 秒）。
*   我们在 `Application` 类上添加了一个名为 `@EnableCircuitBreaker` 的注解，以在应用程序中启用此功能。



### 检查 Hystrix

我们需要重启 `eshop` 和 `inventory-service` 应用程序以应用这些更改。

![../images/454149_1_En_6_Chapter/454149_1_En_6_Fig5_HTML.jpg](img/454149_1_En_6_Fig5_HTML.jpg)

图 6-5

Hystrix 仪表盘

```
> gradle bootRun
>
:: Spring Boot ::        (v2.1.3.RELEASE)
70051 --- [           main] com.example.eshop.EshopApp               :No active
profile set, falling back to default profiles: default
70051 --- [           main] o.s.boot.actuate.endpoint.EndpointId     : Endpoint ID
'service-registry' contains invalid characters, please migrate to a valid format.
70051 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat initialized with port(s): 8081 (http)
70051 --- [           main] o.s.b.a.e.web.ServletEndpointRegistrar   : Registered '/actuator/hystrix.stream' to hystrix.stream-actuator-endpoint
70051 --- [           main] o.s.c.n.e.s.EurekaServiceRegistry        : Registering application ESHOP with eureka with status UP
70051 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat started on port(s): 8081 (http) with context path '/eshop'
```

Hystrix 还会隐式地在 `"http://localhost:8081/eshop/hystrix"` 创建一个默认端点。

在底层，Hystrix 会生成一个指标数据流，可通过 `http://localhost:8081/eshop/actuator/hystrix.stream` 访问。我们需要在 Hystrix 仪表盘界面的输入框中输入此 Hystrix 流 URL，如图 6-5 所示。此外，我们还需要像在 6.3.3.6 节中那样，再次访问 `eshop` 服务的 `/api/orders` API 端点。仪表盘将如图 6-6 所示。

![../images/454149_1_En_6_Chapter/454149_1_En_6_Fig6_HTML.jpg](img/454149_1_En_6_Fig6_HTML.jpg)

图 6-6

inventory-service 正常运行时的 Hystrix 仪表盘

我们可以在图 6-6 中看到“断路器已闭合”。在 `orderProduct` 下方，有一个绿色的数字“3”。

为了验证相反的情况，我们需要停止 inventory-service 一段时间，并多次访问 `eshop` 服务的 `/api/orders` API 端点。让我们在停止 `inventory-service` 后，再次查看 `eshop` 服务的控制台日志：

```
2019-04-02 22:13:18.002 ERROR [eshop,7155a8b13c0b7e1b,
2a018f069c73c5b7,false] 70051 --- [-OrderService-2]
c.e.eshop.service.impl.OrderService      : Inventory Service is responding very slow
2019-04-02 22:13:18.003 ERROR [eshop,7155a8b13c0b7e1b,
7155a8b13c0b7e1b,false] 70051 --- [nio-8081-exec-6]
com.example.eshop.web.TestController     : Transactions are failing, please check after some time
```

这些日志语句被添加到了 `eshop` 服务的 `OrderService.handleInventoryFailure()` 方法中。这验证了当 inventory-service 超时时，降级方法被调用了。换句话说，断路器已打开，降级机制运行正常。我们现在可以在 Hystrix 仪表盘中观察到同样的效果，如图 6-7 所示。

![../images/454149_1_En_6_Chapter/454149_1_En_6_Fig7_HTML.jpg](img/454149_1_En_6_Fig7_HTML.jpg)

图 6-7

inventory-service 未运行时的 Hystrix 仪表盘

从图 6-7 中我们可以看到，有六个请求发生了短路。这意味着有六个请求被 Hystrix 捕获，因为目标服务（`inventory-service`）未运行。

## 微服务架构中的请求追踪

分布式系统编程中的另一个常见挑战是，在生产部署后，很难跨服务追踪请求流。这个问题带来了两个需要解决的难题——集中式日志和请求追踪。

对于集中式日志记录，我们需要将多台服务器上生成的所有日志合并到一个中心点，以便进行搜索、故障排除和分析。这不是一个新问题。行业标准解决方案可以收集并允许搜索集群中多个节点的日志。例如 ELK、Fluentd、Graylog、Splunk 等。它们都支持两种日志收集模式：

*   基于代理对日志文件进行 tail 操作，并将最新收集的日志增量发送到服务器。

*   使用应用程序的 API SDK 直接将日志发送到服务器。

请求追踪是一个不同的问题，并且随着微服务数量的增加而变得更加棘手。当我们想要追踪跨越多个服务的请求或事务时，就需要用到它。

Spring Cloud 通过 Spring Cloud Sleuth 为这一需求提供了解决方案。假设我们想要追踪单个失败的请求（带有某些错误码），甚至基于跨服务的任何请求的某些公共属性进行日志分析。这个库有助于在日志中为每个新请求嵌入一个 TraceId。TraceId 是 Spring Sleuth 为每个新请求生成的唯一 UUID。如果请求经过多个服务，Spring Sleuth 将追踪此 ID，并在第二个服务的日志中保持其相同。

生成的属性共有四个，包含两个这样的 ID。生成数据的完整语法如下所示：

*   `APPLICATION_NAME`：服务的名称

*   `TRACE_ID`：请求的唯一 ID

*   `SPAN_ID`：每个工作跨度的唯一 ID。在我们的示例中，对于同一个请求，`SPAN_ID` 在 `Eshop` 和 `Inventory` 服务中是不同的。

*   `EXPORT_LOGS_OR_NOT`：是否应将日志导出到其他工具。

```
[APPLICATION_NAME, TRACE_ID, SPAN_ID, EXPORT_LOGS_OR_NOT]
```

目前也有一些工具可以帮助您利用生成的属性可视化日志并追踪服务。两个这样的例子是 Google 的 Dapper 和 Zipkin。我们不会详细讨论它们，因为它们不在本书的讨论范围内。为了在应用程序中实现分布式追踪日志，需要四个步骤：

1.  在 `build.gradle` 文件中为 `eshop` 和 `inventory-service` 微服务添加新的依赖项，然后重启它们：

```
    compile("org.springframework.cloud:spring-cloud-starter-sleuth")
    compile("org.springframework.cloud:spring-cloud-starter-zipkin")
    ```

2.  从 `zipkin.io` 下载 Zipkin Jar 文件并作为服务启动：

```
    java -jar ./zipkin-server-2.12.7-exec.jar
    ```

1.  在 `eshop` 和 `inventory-service` 的 `application.yml` 文件中添加一个属性，然后重启它们两个：

```
    spring.zipkin.base-url: http://localhost:9411/
    ```

1.  再次访问 `eshop` 服务的 `/api/orders` API 几次。

```
    > curl -H "Content-Type: application/json" -X POST -i http://localhost:8081/eshop/ api/orders
    ```

完成这些更改后，Spring Cloud Sleuth 将开始向每个日志语句添加以下属性。

```
/eshop/src/main/resources/application.yml
INFO [eshop,6e0abc792dc25b96,e49bf80a97204e7e,false] 67023 --- [-OrderService-1]
c.e.eshop.service.impl.OrderService      : Result from inventory service:
{quantity=2, productId=1, price=200, inventoryId=1}
/inventory-service/src/main/resources/application.yml
INFO [inventory-service,3d3af1edbfa15240,50f818143fd68f39,false] 19246 ---
[nio-8082-exec-1] c.e.e.inventory.web.InventoryResource    : Inventory request for product: 1
```



这些日志有助于追踪跨 `Eshop` 和 `inventory-services` 的同一请求。图 6-8 通过 Zipkin 的用户界面展示了这种关联关系。

![../images/454149_1_En_6_Chapter/454149_1_En_6_Fig8_HTML.jpg](img/454149_1_En_6_Fig8_HTML.jpg)

图 6-8

Zipkin 用户界面

当我们点击图 6-8 中绿色显示的服务名称时，图 6-9 展示了另一个二级视图。

![../images/454149_1_En_6_Chapter/454149_1_En_6_Fig9_HTML.jpg](img/454149_1_En_6_Fig9_HTML.jpg)

图 6-9

Zipkin 用户界面，二级视图

## 微服务架构中的监控

生产应用的管理和监控是一个庞大的话题，我们仅讨论框架中可用于支持我们目前示例的简单选项。我们有一个名为 Spring Boot Admin (SBA) 的开源 Web 应用程序，用于对 Spring Boot 应用进行基本监控。它通过用户界面提供的信息相当全面。

实际上，这种魔力首先是通过另一个名为 Spring Boot *Actuator* 的项目实现的。Actuator 包含的服务负责持续收集来自 JVM 和其他系统组件的指标数据。Actuator 还暴露了 RESTful 端点，通过这些端点可以直接查看所收集的相同信息。SBA 应用程序是构建在这些 Actuator 端点之上的一个用户界面。表 6-1 列出了 Actuator 中的端点。

表 6-1

Actuator 端点

| 端点 ID | 描述 |
| --- | --- |
| `auditevents` | 暴露应用程序的审计事件（例如 `auth_success`、`order_failed`）。 |
| `info` | 显示应用程序的信息。 |
| `health` | 显示应用程序的健康状态。 |
| `metrics` | 显示应用程序的各种指标信息。 |
| `loggers` | 显示和修改已配置的日志记录器。 |
| `logfile` | 返回日志文件的内容（如果设置了 `logging.file` 或 `logging.pathproperties`）。 |
| `httptrace` | 显示最近 100 个 HTTP 请求/响应的 HTTP 跟踪信息。 |
| `env` | 显示当前环境属性。 |
| `flyway` | 显示 Flyway 数据库迁移的详细信息。 |
| `liquidbase` | 显示 Liquibase 数据库迁移的详细信息。 |
| `shutdown` | 允许优雅地关闭应用程序。 |
| `mappings` | 显示所有 `@RequestMapping` 路径的列表。 |
| `scheduledtasks` | 显示应用程序中的计划任务。 |
| `threaddump` | 执行线程转储。 |
| `heapdump` | 返回 GZip 压缩的 JVM 堆转储。 |

默认情况下，Actuator 端点通过路径 `{/contextPath}/actuator/*` 暴露。**SBA** 有两个 API 可以在应用程序中启用此功能——服务器端和客户端。SBA 客户端 API 持续收集从 Actuator 端点获取的监控指标数据，并将其发送到已配置的 SBA 服务器。因此，我们需要在所有希望通过 SBA 监控的应用程序中启用 Actuator。在我们的示例中，需要执行以下两个步骤：

1.  创建一个名为 `admin-service` 的新服务。

2.  将现有的 `eshop` 和 `inventory-service` 应用程序配置为 `admin-service` 的客户端。

我们将在本节中创建的 `admin-service` 客户端，可能是本章迄今为止构建的所有服务中最小的一个。该应用程序的代码结构如下：

```
.
├── build.gradle
├── gradle
│   └── wrapper
│       ├── gradle-wrapper.jar
│       └── gradle-wrapper.properties
├── gradlew
├── gradlew.bat
└── src
└── main
├── java
│   └── com
│       └── example
│            └── admin
│                 └── AdminApp.java
└── resources
└── application.yml
```

### 应用程序设置

从 [`http://www.spring.io`](http://www.spring.io) 创建一个名为 `admin-service` 的新应用，并对 `build.gradle` 文件进行清单 6-24 所示的更改。

```
bootJar {
baseName = 'admin-service'
version =  '1.0'
}
dependencies {
compile("org.springframework.boot:spring-boot-starter-web")
compile("de.codecentric:spring-boot-admin-server:2.1.3")
compile("de.codecentric:spring-boot-admin-server-ui:2.1.3")
compile("org.projectlombok:lombok:1.18.6")
}
清单 6-24
build.gradle 文件
```

正如我们看到的依赖项，这不是一个 Spring 项目，而是另一个基于 Apache 2 许可证的开源项目（ [`https://github.com/codecentric/spring-boot-admin`](https://github.com/codecentric/spring-boot-admin) ）。

### 配置

除了通用设置外，我们需要通过 Spring 启用基本安全性来保护对其用户界面的访问，如清单 6-25 所示。

```
server:
port: 7777
spring:
application:
name: admin-service
boot:
admin:
context-path: /admin
client:
username: user
password: secret
清单 6-25
Application.yml
```

### 应用程序类

我们需要通过在 `Application` 类的顶部添加 `@EnableAdminServer` 注解来启用管理服务器功能，我们在清单 6-26 中执行此操作。

```
package com.example.admin;
import de.codecentric.boot.admin.server.config.EnableAdminServer;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.EnableAutoConfiguration;
import org.springframework.context.annotation.Configuration;
@Configuration
@EnableAutoConfiguration
@EnableAdminServer
public class AdminApp {
public static void main(String[] args) {
SpringApplication.run(AdminApp.class, args);
}
}
清单 6-26
应用程序类
```

### 管理服务客户端

我们需要在所有需要通过此服务监控的其他微服务的 `application.yml` 文件中添加 Spring Boot Admin 客户端配置设置。在我们的示例中，让我们在 `eshop`**、**`inventory-service`**、**`config-service` 和 `discovery-service` 中添加属性，**如**清单 6-27 所示。

```
spring:
boot:
admin:
client:
url: http://localhost:7777/admin
management:
endpoints:
web:
exposure:
include: "*"
清单 6-27
Application.yml
```

注意：

*   `management.endpoints.web.exposure.*` 属性来自 Spring Boot Actuator 库。我们可以指定第 6.6 节表 6-1 中端点名称的逗号分隔列表。指定 `*` 意味着我们通过 HTTP 暴露所有端点数据。

*   `spring.boot.admin.client.*` 属性与 SBA 客户端相关，该客户端会定期将从这些暴露的 Actuator 端点收集的数据发送到已配置的管理服务器 URL（注意 `client.url` 属性）。



### 测试管理服务

要查看实际效果，请依次启动 `admin-service`，然后重启 `config-service`、`discovery-service`、`eshop` 和 `inventory-service`。

```
> gradle bootRun
:: Spring Boot ::         (v2.1.3.RELEASE)
2019-04-04 23:39:32.030   INFO 93382 --- [           main]
com.example.admin.AdminApp                : No active profile set, falling back to default profiles: default
2019-04-04 23:39:33.515   WARN 93382 --- [           main]
ion$DefaultTemplateResolverConfiguration  : Cannot find template location:
classpath:/templates/ (please add some templates or check your Thymeleaf configuration)
2019-04-04 23:39:34.004   INFO 93382 --- [           main]
o.s.b.a.e.web.EndpointLinksResolver       : Exposing 2 endpoint(s) beneath base path '/actuator'
2019-04-04 23:39:34.706   INFO 93382 --- [           main]
o.s.b.web.embedded.netty.NettyWebServer   : Netty started on port(s): 7777
2019-04-04 23:39:34.715   INFO 93382 --- [           main]
com.example.admin.AdminApp                : Started AdminApp in 3.19 seconds (JVM running for 3.682)
```

一旦所有这些服务启动，我们就可以在浏览器中通过 `http://localhost:7777/admin#/applications` 查看 `admin-service` 的 UI。图 6-10 显示所有服务都在 `admin-service` 中受到监控。如果我们点击任意服务名称的超链接，将显示所有指标，如图 6-10 和图 6-11 所示。

![../images/454149_1_En_6_Chapter/454149_1_En_6_Fig11_HTML.jpg](img/454149_1_En_6_Fig11_HTML.jpg)

图 6-11

eshop 服务详情

![../images/454149_1_En_6_Chapter/454149_1_En_6_Fig10_HTML.jpg](img/454149_1_En_6_Fig10_HTML.jpg)

图 6-10

Spring Boot Admin 网页控制台

## 快速文档

有多种选项可以用 Java 描述 REST API。我们将介绍 OpenAPI 规范（OAS），这是 OpenAPI 倡议中的一个项目。OpenAPI 是一个 Linux 基金会合作项目，拥有超过 20 个成员，包括 Adobe、IBM、Google、Microsoft 和 Salesforce。OpenAPI 规范（OAS）旨在为 REST API 定义一个标准的、与编程语言无关的接口描述。

该规范终结了不同框架（如 Json Schema、WADL、RAML 和 Swagger）功能集之间的比较。现在，我们将拥有不同的实现，就像该规范的 JDBC 驱动程序一样。到目前为止，该规范已由多个不同语言的客户端完全实现。本章我们将介绍 Swagger，它是用 Java 编写的（[`https://github.com/swagger-api`](https://github.com/swagger-api)）。Swagger 提供了一系列用于编写（Swagger Editor）、查看（Swagger UI）和生成代码（Swagger Codegen）的工具：

*   **Swagger Editor**：基于浏览器的编辑器，您可以在其中编写 OpenAPI 规范。

*   **Swagger UI**：将 OpenAPI 规范渲染为交互式 API 文档。

*   **Swagger Codegen**：根据 OpenAPI 规范生成服务器存根和客户端库。

我们可以创建一个 YAML 文件并从中生成项目，或者通过 Spring Boot 应用程序中的注解嵌入 Swagger 规范的相关属性。在本节中，我们将介绍 Spring Boot 集成示例，通过我们的微服务示例启用 Swagger API。我们需要在 `eshop` 服务中包含两个依赖项，以查看一个可运行的示例：

```
compile("io.springfox:springfox-swagger2:2.9.2")
compile("io.springfox:springfox-swagger-ui:2.9.2")
```

### Java 配置

我们需要先定义 Swagger 相关的配置，然后在控制器类中为 RESTful 资源定义注解。清单 6-28 展示了 Swagger 的基本 Java 配置。

```
package com.example.eshop.config;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import springfox.documentation.builders.RequestHandlerSelectors;
import springfox.documentation.spi.DocumentationType;
import springfox.documentation.spring.web.plugins.Docket;
import springfox.documentation.swagger2.annotations.EnableSwagger2;
import static springfox.documentation.builders.PathSelectors.regex;
@Configuration
@EnableSwagger2
public class SwaggerConfig {
@Bean
public Docket productApi() {
return new Docket(DocumentationType.SWAGGER_2)
.select()
.apis(RequestHandlerSelectors.basePackage("com.example.eshop"))
.paths(regex("/api.*"))
.build();
}
private ApiInfo apiInfo() {
return new ApiInfoBuilder().title("Eshop Service API")
.description("Eshop API reference for developers")
.termsOfServiceUrl("http://localhost:8201/eshop")
.contact(new Contact("Raj Malhotra", "http://
www.example.com", "mraj6046@gmail.com")).license("Example License
Title")
.licenseUrl("http://localhost:8201/eshop/
license").version("1.0").build();
}
}
清单 6-28
Swagger 的 Java 配置
```

注意：

*   第一个方法名为 `productApi()`，提供了一个 `Docket` bean，这是一个构建器，旨在成为 Springfox 框架的主要接口。

*   `Docket` 允许您定义可以找到带有 Swagger 注解的控制器类的包，以及所有 API 的路径。

*   第二个 bean 名为 `ApiInfo`，允许您定义通用信息，例如用户/作者的联系方式和许可证信息。



### 自定义 Swagger

接下来，我们需要使用 Swagger 注解来描述 `Controller` 类的方法，以定义它们的描述、输入和输出变量以及错误码。清单 6-29 展示了 Spring Web `RestControllers` 顶部的最终注解。

```
package com.example.eshop.web;
import com.example.eshop.model.Order;
import com.example.eshop.service.impl.CustomerService;
import com.example.eshop.service.impl.OrderService;
import com.example.eshop.service.impl.ProductService;
import com.netflix.discovery.EurekaClient;
import com.netflix.discovery.shared.Applications;
import io.swagger.annotations.ApiOperation;
import io.swagger.annotations.ApiResponse;
import io.swagger.annotations.ApiResponses;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.cloud.client.ServiceInstance;
import org.springframework.cloud.client.discovery.DiscoveryClient;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.servlet.support.ServletUriComponentsBuilder;
import java.net.URI;
import java.util.List;
import java.util.Objects;
@RestController
@Slf4j
public class TestController {
private OrderService orderService;
private ProductService productService;
private CustomerService customerService;
public TestController(CustomerService customerService,
ProductService productService, OrderService orderService) {
this.customerService = customerService;
this.productService = productService;
this.orderService = orderService;
}
@ApiOperation(value = "Order a product", response = Boolean.class)
@ApiResponses(value = {
@ApiResponse(code = 201, message = "Order created successfully"),
@ApiResponse(code = 404, message = "The resource you were trying to reach is not found")
})
@PostMapping(value = "/api/orders", produces = "application/json")
public ResponseEntity purchaseSampleProduct()   throws Exception
{
customerService.registerNewCustomers();
productService.registerNewProducts();
Order order = orderService.orderProduct();
if(Objects.isNull(order)) {
log.error("Transactions are failing, please check after some time");
return new
ResponseEntity(HttpStatus.INTERNAL_SERVER_ERROR);
}
log.info("Order status: {}", Objects.isNull(order));
URI uri =
ServletUriComponentsBuilder.fromCurrentRequest().path("/{id}")
.buildAndExpand(order.getOrderId()).toUri();
return ResponseEntity.created(uri).build();
}
}
清单 6-29
Controller 类
```

当我们重启 `eshop` 服务时，我们在该服务中创建的唯一个 RESTful 端点（`/api/order`）的文档可在 `http://localhost:8081/eshop/swagger-ui.html` 查看（见图 6-12）。

![../images/454149_1_En_6_Chapter/454149_1_En_6_Fig12_HTML.jpg](img/454149_1_En_6_Fig12_HTML.jpg)

图 6-12

Swagger UI

## API 网关

API 网关是微服务架构中的一个流行概念。如果任何系统中存在多个微服务，为所有传入请求设置一个中央入口点可以在几个关键的横切方面带来好处：

*   路由

*   集中认证

*   通过基本认证、OAuth、JWT、API 密钥等方式进行授权

*   SSL 终止

*   速率限制

*   缓存和监控

有两个流行的开源 API 网关，它们用 Java 编写，并且可以轻松与 Spring 框架集成：

*   Netflix OS Zuul

*   Spring Cloud Gateway

此外，还有更多流行的选择，例如 Mashery、Apigee、Kong、WSO2、3scale 以及来自云服务提供商的网关。让我们来探索 Zuul 网关，因为它是一个常见的选择，并且是 Netflix OSS 伞形项目的一部分。我们将使用以下代码结构创建一个新的微服务（`gateway-service`）：

```
.
├── build.gradle
├── gradle
│   └── wrapper
│       ├── gradle-wrapper.jar
│       └── gradle-wrapper.properties
├── gradlew
├── gradlew.bat
└── src
└── main
├── java
│   └── com
│       └── example
└── resources
├── application.properties
└── application.yml
```

我们将在以下小节中逐步介绍网关的创建过程。

### 应用程序设置

从 [`http://www.spring.io`](http://www.spring.io) 创建一个名为 `gateway-service` 的新应用程序，其 `build.gradle` 文件中的依赖项如清单 6-30 所示。

```
bootJar {
baseName = 'admin-service'
version =  '1.0'
}
dependencies {
compile("org.springframework.boot:spring-boot-starter-web")
compile('org.springframework.cloud:spring-cloud-starter-netflix-zuul:
2.1.1.RELEASE')
compile("org.projectlombok:lombok:1.18.6")
}
dependencyManagement {
imports {
mavenBom "org.springframework.cloud:spring-cloud- dependencies:Greenwich.SR1"
}
}
清单 6-30
build.gradle 文件
```

### 配置

理想情况下，`gateway-service` 应该运行在负载均衡器上，以便解决扩展和故障转移问题。清单 6-31 定义了此服务的配置，并在注释中进行了说明。

```
application.yml
spring:
application:
name: zuul
boot:
admin:
client:
url: http://localhost:7777/admin
server:
port: 9999
management:
endpoints:
web:
exposure:
include: "*"
zuul:
ignoredServices: '*'
routes:
config:
path: /config/**
url: http://localhost:8888/config/
eshop:
path: /eshop/**
url: http://localhost:8081/eshop/
inventory:
path: /inventory/**
url: http://localhost:8082/inventory/
admin:
path: /admin/**
url: http://localhost:7777/admin/
清单 6-31
Application.yml
```

注释：

*   我们已经看到了此服务最常见的用途是将请求路由到不同的域名 URL。

*   `zuul.routes.config.*` 属性根据上下文路径指向相应的 URL。

### 引导

我们需要在 `Application` 类顶部添加另一个名为 `@EnableZuulProxy` 的注解，以启用 Zuul 功能（参见清单 6-32）。

```
package com.example.eshop.gateway;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.zuul.EnableZuulProxy;
@SpringBootApplication
@EnableZuulProxy
public class ZuulApp {
public static void main(String[] args) {
SpringApplication.run(ZuulApp.class, args);
}
}
清单 6-32
Application 类
```



### 过滤器

过滤器是我们自定义 Zuul 行为的机制。这些自定义类可以实现任何功能并改变路由。其典型用例是在请求到达功能性微服务之前，在中心点执行授权和日志记录。清单 6-33 提供了一个示例。

```
package com.example.eshop.gateway.filters;
import com.netflix.zuul.ZuulFilter;
import lombok.extern.slf4j.Slf4j;
import org.springframework.stereotype.Component;
@Component
@Slf4j
public class AuthPreFilter extends ZuulFilter {
@Override
public String filterType() {
return "pre";
}
@Override
public int filterOrder() {
return 0;
}
@Override
public boolean shouldFilter() {
return true;
}
@Override
public Object run() {
//检查 JWT 头部并验证授权令牌的有效性
// 记录请求日志
return null;
}
}
清单 6-33
Zuul 过滤器实现
```

注意：

*   `filterType()` 方法用于按类型对过滤器进行分类。Zuul 中的标准类型包括：`pre` 用于路由前过滤，`route` 用于路由到目标，`post` 用于路由后过滤，以及 `error` 用于错误处理。

*   `filterOrder()` 方法用于定义过滤器的执行顺序。此顺序不必是连续的。

*   `shouldFilter()` 方法包含决定何时执行此过滤器的逻辑。

*   最后，可以重写 `run()` 方法来添加自定义功能。

### 运行应用程序

运行应用程序：

```
> gradle bootRun
>
:: Spring Boot ::        (v2.1.3.RELEASE)
2019-04-06 12:46:01.386   INFO 69797 --- [           main]
com.example.eshop.gateway.ZuulApp         : No active profile set, falling back to default profiles: default
2019-04-06 12:46:03.170   INFO 69797 --- [           main]
o.s.web.context.ContextLoader             : Root WebApplicationContext: initialization completed in 1759 ms
2019-04-06 12:46:04.975   INFO 69797 --- [           main]
o.s.b.a.e.web.EndpointLinksResolver       : Exposing 21 endpoint(s) beneath base path '/actuator'
2019-04-06 12:46:05.142   INFO 69797 --- [           main]
o.s.b.w.embedded.tomcat.TomcatWebServer   : Tomcat started on port(s): 9999 (http) with context path "
2019-04-06 12:46:05.146   INFO 69797 --- [           main]
com.example.eshop.gateway.ZuulApp         : Started ZuulApp in 5.164 seconds (JVM running for 4.412)
```

一旦 `gateway-service` 启动并运行，我们就可以通过 `gateway-service` 的基础 URL 访问 `eshop`、`config-service`、`inventory-service` 和 `admin-service` 的端点，而无需在客户端应用程序中为每个服务保留单独的 URL。例如：

*   要访问配置服务，URL 现在为 `http://localhost:8888/config/`

*   要访问管理服务，URL 现在为 `http://localhost:7777/admin/`

*   要访问 Eshop 服务，URL 现在为 `http://localhost:8081/eshop/`

*   要访问库存服务，URL 现在为 `http://localhost:8082/inventory/`

## 跨服务代码复用

这是一个经常被讨论的实际问题：我们是否应该复用跨微服务创建的代码？通常，这需要在不同功能服务之间共享公共 DTO，但这会使服务相互依赖。我们使用微服务是为了在松散耦合的独立应用之间实现关注点分离，但现在我们却在做相反的事情。

这也引发了一个问题：当不同的服务使用不同的语言实现时，相同的代码就无法使用了。我还见过这样的情况：决策者说他们三年前用 Java 构建服务，现在仍然使用 Java，未来三年也会继续使用 Java，所以他们不必担心这个问题。我将把决定权留给读者，并讨论在 Java 中实现代码复用的几种方式：

*   使用基于 Maven 或 Gradle 的多模块项目。
    *   我们经常看到开源项目使用这种技术。Spring 框架本身就是一个例子。如果你查看 GitHub 仓库中的 Spring 上下文模块 Gradle 文件（`spring-content.gradle`）（[`https://github.com/spring-projects/spring-framework/blob/master/spring-context/spring-context.gradle`](https://github.com/spring-projects/spring-framework/blob/master/spring-context/spring-context.gradle)），它依赖于其他模块，如 `spring-core` 和 `spring-beans`。我们在此不讨论多模块构建设置，因为它们超出了本书的范围。

*   使用构建制品仓库管理器解决方案，如 Archiva（开源）、JFrog 或 Sonatype Nexus。在其他语言中，这些解决方案被称为包管理器。
    *   这可以通过 Gradle 中的 `uploadArchives` 配置属性实现。每次新构建时，都可以将新版本的 Jar 文件上传到组织的内部仓库。例如，如果我们有一个 `common-models` 模块，该模块的所有版本都可以上传到内部仓库，然后作为依赖项在其他服务中使用。

*   在所有服务中再次复制公共代码。
    *   这是最糟糕的方法，因为所有公共数据模型可以在不同服务中反复复制。例如，如果 `product-catalog` 和 `inventory` 服务都需要一个 `Product` 对象，它们可以复制该对象。

*   精心创建共享领域类的不同消费者版本。
    *   这通常是许多专家推荐的首选方法。沿用之前的例子，假设 `product-catalog` 服务拥有 `Product` 数据模型。如果 `inventory-service` 也需要这个对象，我们是否应该创建一个 `ProductView` 类，以便在该服务中创建实际对象的聚焦子视图？这个子视图可以包含较少的属性，也可以包含不同的属性。即使 `product-service` 中的实际 `Product` 类发生变化，`ProductView` 也可以保持不变。这种方法也非常支持多语言微服务，因为不存在代码共享。

## 微服务安全

与在单个应用程序中处理用户注册、登录和授权的单体架构相比，保护基于微服务的分布式系统带来了许多新的挑战。不过，两种架构仍有许多共同的关注点，例如不同的身份验证模式、代码内方法级别的授权、OWASP 等。我们可能需要在单体架构中使用基于 OAuth 的身份验证，因为你的企业中可能存在多个单体应用程序。

在企业中，你可能需要设计一个与企业 LDAP 系统集成的 SSO（单点登录）。然而，在微服务架构中，OAuth、SAML、SSO 和 JWT 仍然是相同的，因此我们可能希望将身份验证和令牌验证保留在 API 网关层面。让我们尝试通过在 `gateway-service` 中启用基于 JWT（JSON Web 令牌）的令牌身份验证，并在 `eshop` 服务中创建令牌来探索这个想法。

### Eshop 服务变更

我们需要在面向前端的微服务（对我们来说是 `eshop`）中实现登录功能。我们需要创建一个 `LoginController` 类来处理身份验证，并根据预定义用户（邮箱为 raj@example.com）创建 JWT 令牌。此外，我们还需要在 `build.gradle` 文件中添加一个依赖项：

```
compile group: 'io.jsonwebtoken', name: 'jjwt', version: '0.9.1'
```

现在让我们来看一下控制器类。



#### 登录控制器

我们将创建一个 `LoginRequest` DTO 来保存用户的用户名/密码以及控制器类。参见清单 6-34。

```
DTO
package com.example.eshop.dto;
import lombok.Data;
@Data
public class LoginRequest {
private String email;
private String password;
}
Controller
package com.example.eshop.web;
import com.example.eshop.dto.LoginRequest;
import io.jsonwebtoken.Jwts;
import io.jsonwebtoken.SignatureAlgorithm;
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RestController;
import javax.servlet.ServletException;
import java.util.Date;
import lombok.*;
@RestController
public class LoginController {
String DEFAULT_EMAIL = "raj@example.com";
String DEFAULT_PASSWORD = "password";
@RequestMapping(value = "/login", method = RequestMethod.POST)
public String login(@RequestBody LoginRequest loginRequest) throws
ServletException {
var jwtToken = "";
val email = loginRequest.getEmail();
val password = loginRequest.getPassword();
if(!(DEFAULT_PASSWORD.equals(password))
|| (DEFAULT_EMAIL.equals(email)))   {
// 抛出错误
}
jwtToken = Jwts.builder().setSubject(email)
.claim("roles", "admin")
.signWith(SignatureAlgorithm.HS256, "secretkey")
.setIssuedAt(new Date())
.compact();
return jwtToken;
}
}
清单 6-34
控制器和 DTO 类
```

注意：

*   我们在 `/login` 处有一个 REST API，它接受 `LoginRequest` bean 中的明文用户名和密码。理想情况下，密码应通过 SSL 保护的连接发送，并采用摘要形式。为了简单起见，我们在此使用通过 API 传递明文凭证的基本示例。

*   `Jwts` 类是一个轻量级 API，用于创建和验证 JSON Web 令牌。

*   我们还使用了一个简单的对称密钥作为 `secretkey`，但我建议您为此目的使用非对称密钥。

*   我们将角色设置为一个属性作为声明。我们可以使用声明来添加任何自定义属性，并使用用户配置文件信息创建令牌。该令牌可以被验证，然后传递给其他下游服务，这些服务可以解码并根据用户配置文件信息执行授权。

#### 测试令牌生成

重启 `eshop` 服务后，在控制台上使用 `CURL` 运行以下命令：

```
> curl -H "Content-Type: application/json" -d '{"email":"raj@example.com","password":"password"}' -X POST
http://localhost:8081/eshop/login
eyJhbGciOiJIUzI1NiJ9.eyJzdWIiOiJyYWpAZXhhbXBsZS5jb20iLCJyb2xlcyI6ImFkbWluIiwiaWF0Ijox NTU0NjA3NTIzfQ.p4FzUPRKUi2NT00OrWQxWv760i4eB2LK0_fcWexlDos
```

此响应显示了认证令牌，已用粗体标出。我们将在下一步中使用此令牌来验证并允许服务访问。

### 网关服务变更

在此服务中，我们验证远程客户端在每个请求中传递的令牌。如果 `Authorization` 标头无效或损坏，我们将发送带有错误描述的 HTTP 401 响应。我们只需要更改 `gateway-service` 中的 `AuthPreFilter` 类（参见清单 6-35）。

```
package com.example.eshop.gateway.filters;
import com.netflix.zuul.ZuulFilter;
import com.netflix.zuul.context.RequestContext;
import io.jsonwebtoken.Claims;
import io.jsonwebtoken.Jwts;
import io.jsonwebtoken.SignatureException;
import lombok.extern.slf4j.Slf4j;
import org.springframework.stereotype.Component;
import javax.servlet.http.HttpServletRequest;
@Component
@Slf4j
public class AuthPreFilter extends ZuulFilter {
//检查 JWT 标头，并在需要时检查所有 OAUTH 服务
@Override
public Object run() {
RequestContext context = RequestContext.getCurrentContext();
HttpServletRequest request = context.getRequest();
final String token = request.getHeader("Authorization");
if (token == null) {
context.setSendZuulResponse(false);
context.setResponseStatusCode(401);
context.setResponseBody("缺少 Authorization 标头");
return null;
}
try {
final Claims claims = Jwts.parser().setSigningKey("secretkey").parseClaimsJws(token).getBody();
log.info("声明: {}", claims);
context.addZuulRequestHeader("Authorization", "Basic " + token);
} catch (final SignatureException e) {
context.setSendZuulResponse(false);
context.setResponseStatusCode(401);
context.setResponseBody("API 密钥无效");
}
return null;
}
@Override
public String filterType() {
return "pre";
}
@Override
public int filterOrder() {
return 0;
}
@Override
public boolean shouldFilter() {
return true;
}
}
清单 6-35
Zuul 预过滤器类
```

注意：

*   我们已经在第 6.8.4 节中了解了 ZuulFilter。此外，我们还添加了代码，用于使用相同的签名对称密钥解码和提取令牌。

*   如果密钥被篡改，我们将无法再次解码，因此身份验证将在网关级别中断。

*   一旦我们验证了令牌，我们将其作为请求中的标头发送回去。

#### 测试网关认证

重启 `gateway-service` 后，通过 `CURL` 运行以下命令来验证并访问 `eshop` 服务的 `/api/orders` API。

```
curl -H "Authorization:
eyJhbGciOiJIUzI1NiJ9.eyJzdWIiOiJyYWpAZXhhbXBsZS5jb20iLCJyb2xlcyI6ImFkbWluNTIzfQ.p4FzUPRKUi2NT00OrWQxWv760i4eB2LK0_fcWexlDos" -i -X POST
http://localhost:9999/eshop/ api/orders
HTTP/1.1 201
Location: http://localhost:8081/eshop/api/orders/2
Date: Sun, 07 Apr 2019 03:47:24 GMT
Transfer-Encoding: chunked
```

这些结果是在示例运行后收集的，表明我们在第 6.10.1.2 节中收到的令牌已成功验证。这也表明 `eshop` 服务 API 已响应。让我们也看看如果在请求中发送无效令牌，`gateway-service` 的行为。

```
curl -H "Authorization: WRONG_TOKEN" -i -X POST http://localhost:9999/eshop/api/ orders
HTTP/1.1 401
Transfer-Encoding: chunked
Date: Sun, 07 Apr 2019 03:56:28 GMT
API 密钥无效
```

如果请求中发送的令牌无效，我们将收到此消息。

## 总结

我们涵盖了基本概念（包括问题和解决方案），以帮助您开始使用 Java 和 Spring Cloud 作为微服务栈。还有更多框架可以处理我们在本章中讨论的问题。我建议您也更详细地探索 Netflix OSS。在本书的下一部分中，我们将研究与扩展 Java 相关的技术和概念。

# 第三部分让 HTTP 更快

# 7. Java GraphQL 开发

GraphQL 是一种 API 查询语言，也是一个使用现有数据来执行这些查询的运行时。GraphQL 由 Facebook 于 2012 年开发，并于 2015 年公开发布。最近围绕 GraphQL 有了很多发展，特别是自 AWS Cloud AppSync 等工具和 Prisma 等框架出现以来。GraphQL 的主要目的是处理 RESTful API 设计中的问题，并使 HTTP 通信更快。

GraphQL 不采用多个基于 URL 的约定，而是基于单个端点通过 `POST` 接受所有请求，其数据结构类似于 JSON 的图状结构。GraphQL 有自己的模式定义语言（SDL），它使用标准类型、验证以及检索和更新这些模型的操作来定义数据模型。GraphQL 遵循 CQRS（命令查询职责分离）设计模式，该模式指出，最好将与读取数据相关的代码与更新数据的代码分开。这保持了职责的清晰。用于读取和写入的领域对象也可以与用于更新数据存储的对象不同。



## 重要概念

对于来自 REST API 设计的开发者来说，GraphQL 中有几个有趣的新概念可以简化开发流程：

*   单一**端点**：GraphQL 应用程序在单个 /GraphQL 端点上运行，各种请求会被路由到已注册的解析器类。

*   基于 POST 的请求：所有请求都是基于 `POST` 的，并且必须以符合 SDL 格式的方式发送，这种格式可能相当描述性强、冗长且嵌套。

*   **基于模式：** GraphQL 允许你通过 SDL 为 API 客户端定义一个类似 JSON 的模式。这种模式优先或契约优先的模式有助于确保 UI 和后台团队之间更好的协调。

*   查询：所有不打算创建或更新任何资源的 `GET` 请求被称为查询。

*   **变更：** 在 GraphQL 中，所有 `POST`、`PUT` 和 `DELETE` 请求都是变更。

*   **解析器：** GraphQL 引擎或框架可以调用的所有自定义注册函数都称为解析器。

*   **模式拼接：** 这是一种从多个模式创建单一统一模式的技术。当不同的微服务拥有自己的模式，并且一个前端服务可以为所有这些微服务提供组合视图时，这非常有用。这样，前端微服务可以充当网关。此功能在 Java API 中不存在，但预计很快会添加。

*   **订阅：** 此功能允许通过 WebSocket 编程，以推拉模型向客户端提供近乎实时的响应。

## 工作原理

我们来看一下创建 GraphQL 服务的简化分步过程：

1.  首先创建一个 JSON 模式（模式优先方法）。或者，稍后可以从对象生成模式（代码优先方法）。

2.  根据实现查询和变更时定义的模式，创建一系列*解析器*（实现类）。

3.  使用其内置的基于 Web 的客户端或资源管理器（Java 中的 GraphiQL）运行并测试应用程序。

4.  作为客户端连接到 GraphQL REST 端点。

5.  如果需要，通过模式拼接过程将多个基于 GraphQL 端点的微服务合并为一个，然后像往常一样连接到它。

## 相较于 REST 的优势

GraphQL 主要关注客户端访问数百个不同 RESTful API URL 的问题，从而浪费大量带宽。GraphQL 解决了许多问题：

*   精确获取你需要的内容。
*   声明式，无过度获取。
*   在单个请求中获取多个资源。
*   基于模式的 API，充当客户端和服务之间的契约。
*   单一 API 端点，因此无需在客户端代码中维护太多 URL。
*   不再有 API 版本管理的麻烦。已弃用的字段可以在模式中标记为 `@deprecated` 并附带描述。工具可以开始显示其已弃用，客户端也可以开始忽略它。
*   随时可用的精美现成文档。内置的编辑器工具可以即时提供出色的文档。
*   用于快速测试的现成工具。内置工具也为测试提供了良好的支持。
*   通过模式拼接为多个微服务提供简单的解决方案。
*   整体通信效率高于 REST。
*   内置分页语义。
*   通过订阅（更准确地说，是 WebSocket）内置对近乎实时消息传递的支持。

图数据结构实际上非常强大，它可以很好地表示连接和嵌套的资源。我们已经通过图数据库看到并享受了它的强大功能。通过 GraphQL，REST API 客户端开始以嵌套形式请求对象图，并且可以获取相关的、嵌套的和连接的资源。让我们详细看看上述优势：

*   精确获取你需要的内容：

在 GraphQL 中，你请求响应中需要的精确属性。然而，在 REST 中，你只能访问一个 URL，并且总是会收到一个固定的响应。

示例：

```
    query: allPosts {
    id
    author
    title
    content
    }
    }
    ```

*   声明式，无过度获取：

想想 REST API 设计，客户端可能最终获取所有属性，而不仅仅是所需的属性。GraphQL 引擎默认仅在 HTTP 层执行此操作。这意味着即使你的解析器正在查询完整的表，引擎也只会返回客户端在请求中要求的属性。当某个字段已被弃用，客户端可以简单地在请求中开始忽略该字段时，这种行为非常有用。这确实提供了根据请求结构动态查询和响应的能力。

*   在单个请求中获取多个资源：

GraphQL 允许你在单个请求中查询多个资源。虽然 REST API 首先关注 URL，但 GraphQL 也同样关注数据。这最终使你在较慢的网络连接上获得更快的通信优势。

示例：

```
    query: {
    allPosts {
    id
    title
    }
    allUsers {
    id
    name
    }
    }
    ```

*   基于模式的 API：

还记得关于 SOAP 与 REST 在契约优先与实现优先上的漫长讨论吗？最终，GraphQL 允许你在 RESTful 世界中首先定义其基于 SDL 的模式。

示例：

```
    type Query {
    user(id: ID!): User!
    }
    type User {
    id: ID!
    email: String!
    name: String!
    }
    ```

*   单一 API 端点：

基于 REST 的客户端应用程序必须管理和连接到多个 URL，并不断更改 HTTP 动词。使用 GraphQL，客户端只需连接到一个端点，并且仅通过 `POST` HTTP 动词。GraphQL 引擎必须将类（解析器）连接到模式中的查询和变更类型，同时支持错误处理。如前所述，按照惯例，所有基于获取（`GET`）的请求应由查询类型中定义的函数处理，而 `PUT`、`POST`、`DELETE` 操作则由变更类型中定义的函数处理。

*   不再有 API 版本管理的麻烦：

由于 GraphQL 应用程序在单个端点和模式上运行，剩下的唯一事情就是将模式中的字段标记为单个 URL 的已弃用。这些已弃用的字段可以被客户端工具跟踪和隐藏。

*   随时可用的现成文档：

几乎所有 GraphQL 规范实现都提供了一个可视化工具来访问其模式并运行示例查询。在 Java 领域，这个工具被称为 GraphiQL。此工具提供对整个模式的可视化访问，以及注释，这些注释在作为字段描述显示的实际文件中定义。

*   用于快速测试的现成工具：

GraphiQL 可用于对各种模式类型和解析器进行随机测试。只需在内置文件中包含一个依赖项并定义上下文路径即可启用此功能。

*   通过模式拼接为微服务提供简单的解决方案：

GraphQL 有一个内置系统，通过模式拼接的概念来绑定不同的应用程序或服务。

*   整体通信效率高于 REST：

由于可以组合多个请求，并且客户端可以指定它想要返回哪些属性，通信变得更加容易和快速。

*   内置分页语义：

GraphQL 模式定义为在请求中定义分页属性提供了语义。

示例：

```
    query: allPosts(first: 2, skip: 2) {
    id
    author
    title
    content
    }
    }
    ```

*   订阅：

GraphQL 通过 WebSocket 为实时客户端-服务器通信提供内置支持。用 GraphQL 术语来说，这是通过订阅来处理的。



## 风险与劣势

在为特定应用选择 GraphQL 时，有几个风险需要谨慎对待：

*   缓存
*   安全问题
*   N+1 问题
*   通过 HTTP 状态码进行错误处理
*   大型应用中的整体复杂性

让我们详细探讨这些问题：

*   **缓存：**

    REST 响应可以基于不同的 URL 轻松缓存。由于 GraphQL 基于动态、冗长的 JSON 图，在网络层对其进行缓存变得困难。像 Apollo 这样的框架提供了客户端缓存支持，但这可能并不总是足够的。

*   **安全问题：**

    由于 GraphQL 允许嵌套查询，嵌套资源的深度可能会给服务器带来严重负载，并且这也可能形成循环。

*   **N+1 问题：**

    N+1 问题意味着服务器为了获取嵌套数据，会执行多次不必要的数据库往返。假设客户端应用请求获取所有作者的列表以及他们各自撰写的 top 10 文章。如果 GraphQL 解析器没有优化，这最终可能导致一次查询获取所有作者，然后针对这些作者中的每一个，再执行 N 次查询来获取其 top 文章列表。

*   **响应中的 HTTP 状态码：**

    与 RESTful 服务一样，我们这里有一个单一的基于 `POST` 的端点。我们没有像 REST 中的 `Controller` 类那样，拥有一个清晰的、各自具有独立 HTTP 错误处理的不同端点列表。我们可以进行通用的错误处理，但当我们需要为不同的请求返回不同的 HTTP 状态码时，问题就出现了。这有时可能会使代码更具可读性。

*   **复杂性：**

    虽然这减轻了客户端的很多负担，但它确实使服务端变得稍微复杂一些。由于它基于图结构，高度嵌套的请求处理起来会变得相当复杂，因为它们还需要解决 N+1 问题。

## GraphQL Java 示例

我们将专注于使用 schema-first 方法，结合 SpringBoot 进行 GraphQL 开发。让我们按照以下步骤构建一个示例：

1.  从 `spring.io` 构建一个新的 Gradle 项目。
2.  创建 schema。
3.  创建 JPA 实体模型和仓库。
4.  创建输入 DTO。
5.  创建 `Query` 和 `Mutation` 解析器类。
6.  定义应用配置并进行示例运行。

让我们构建一个名为 `“”` 的应用，其代码结构如下：

```
.
├── build.gradle
├── eshop.iml
├── settings.gradle
└── src
    └── main
        ├── java
        │   └── com
        │       └── example
        │           └── eblog
        │               ├── App.java
        │               ├── dto
        │               │   ├── AuthorInputRequest.java
        │               │   └── PostInputRequest.java
        │               ├── handler
        │               │   └── GraphQLConfig.java
        │               ├── model
        │               │   ├── Author.java
        │               │   ├── Post.java
        │               │   └── Status.java
        │               ├── repository
        │               │   ├── AuthorRepository.java
        │               │   └── PostRepository.java
        │               └── resolvers
        │                   ├── MutationResolver.java
        │                   └── QueryResolver.java
        └── resources
            ├── application.yml
            └── schema.graphqls
```

### 应用设置

首先从 [`http://start.spring.io/`](http://start.spring.io/) 构建一个名为 `eblog-graphql-ch7` 的新应用，并在 `build.gradle` 文件中添加以下依赖项（参见清单 7-1）。

```
plugins {
    id 'org.springframework.boot' version '2.1.3.RELEASE'
    id 'java'
}
apply plugin: 'io.spring.dependency-management'
apply plugin: 'project-report'
java {
    group = 'com.example'
    version = '1.0'
    sourceCompatibility = JavaVersion.VERSION_1_8
    targetCompatibility = JavaVersion.VERSION_1_8
}
bootJar {
    archieveBaseName = 'eblog-service'
    version =  '1.0'
}
repositories {
    mavenCentral()
}
dependencies {
    compile('org.springframework.boot:spring-boot-starter-web')
    compile('com.graphql-java:graphql-java-tools:5.2.4')
    compile('com.graphql-java:graphql-spring-boot-starter:5.0.2')
    compile('com.graphql-java:graphiql-spring-boot-starter:5.0.2')
    compile('org.springframework.boot:spring-boot-starter-data-jpa')
    compile('mysql:mysql-connector-java:8.0.15')
    compile('org.projectlombok:lombok:1.18.6')
    testCompile('junit:junit:4.12')
}
清单 7-1
构建文件
```

### Schema 文件：Resources/Schema.Graphqls

GraphQL 有自己的 schema 定义语言（SDL），用于描述服务中使用的类型，并定义如何查询这些类型。清单 7-2 展示了此示例的 schema。

```
type Post {
    id: ID!
    title: String!
    content: String!
    status: Status!
    author: Author!
}
type Author {
    id: ID!
    name: String!
    age: Int
    posts: [Post]!
    status: Status!
}
enum Status {
    ACTIVE
    NON_ACTIVE
}
input PostInputRequest {
    title: String!
    content: String!
    authorId: Int!
}
input AuthorInputRequest {
    name: String!
    age: Int
}
type Mutation {
    newPost(input: PostInputRequest): Post!
    newAuthor(input: AuthorInputRequest): Author!
}
type Query {
    recentPosts(limit: Int, offset: Int, orderBy: String): [Post]
    authorsWithTopPosts: [Author]
}
清单 7-2
resources/schema.graphqls
```

注意：

*   我们有两个数据模型——`Post` 和 `Author`，它们各自拥有类型定义中提到的字段。我们支持所有标准数据类型——`Int`、`String`、`Boolean`、`Date` 和 `Objects`。
*   字段定义由冒号分隔符左侧的 `字段名` 和右侧的 `类型` 组成，例如 `{fieldname} : {fieldType}`。`!` 符号表示该字段是否为必填项。如果将字段放在方括号中，它就变成了一个列表，例如 `[Post]` 是 `POST` 对象的列表。
*   也可以声明枚举，并将其作为任何字段的数据类型。
*   输入类型有助于定义输入请求对象（或者用技术术语来说，就是 DTO）。
*   在 GraphQL schema 中需要定义两种特殊类型——`Query` 和 `Mutation`。`Query` 定义用于连接代码中执行这些类型获取操作的任何函数。`Mutation` 类似，但按惯例仅用于更新系统中这些类型的操作。这两者都可以被称为*解析器*，因为它们有助于在运行时将字段解析为不同的用户定义函数。



### 数据模型与仓库

在清单 7-3 的代码中，我们声明了领域对象（JPA 实体）以及相关的 Spring Data 仓库。

```
package com.example.eblog.model;
import com.fasterxml.jackson.annotation.JsonIgnore;
import lombok.*;
import javax.persistence.*;
import java.io.Serializable;
import java.util.Set;
@Data
@Entity
@NoArgsConstructor
@ToString(exclude = {"posts"})
@EqualsAndHashCode(exclude = {"posts"})
public class Author implements Serializable {
@Id
@GeneratedValue(strategy = GenerationType.IDENTITY)
private Long id;
private String name;
private int age;
private Status status = Status.NON_ACTIVE;
@OneToMany(fetch = FetchType.LAZY, mappedBy = "author")
@JsonIgnore
private Set posts;
public Author(String name, Integer age)  {
this.name = name;
this.age = age;
}
}
package com.example.eblog.model;
import com.fasterxml.jackson.annotation.JsonIgnore;
import lombok.*;
import javax.persistence.*;
import java.io.Serializable;
@Data
@Entity
@Builder
@NoArgsConstructor
@ToString(exclude = {"author"})
@EqualsAndHashCode(exclude = {"author"})
public class Post implements Serializable {
@Id
@GeneratedValue(strategy = GenerationType.IDENTITY)
private Long id;
private String title;
private String content;
private Status status;
@ManyToOne(fetch = FetchType.LAZY)
@JsonIgnore
private Author author;
}
package com.example.eblog.model;
public enum Status {
ACTIVE, NON_ACTIVE;
public Status fromValue(String value) {
return valueOf(value.toUpperCase());
}
public String toValue(Status status) {
return status.name().toLowerCase();
}
}
package com.example.eblog.repository;
import com.example.eblog.model.Author;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.data.jpa.repository.Query;
import java.util.List;
public interface AuthorRepository extends JpaRepository {
@Query("Select distinct a from Author a left join fetch a.posts")
List454149_1_En findAuthorsWithPosts();
}
package com.example.eblog.repository;
import com.example.eblog.model.Post;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;
@Repository
public interface PostRepository extends JpaRepository {}
清单 7-3
领域对象
```

### 输入 DTO

在清单 7-4 中，我们声明了用于从 `Mutation` 解析器接收创建 `Author` 和 `POST` 对象输入的 POJO。

```
package com.example.eblog.dto;
import lombok.Data;
@Data
public class AuthorInputRequest {
String name;
Integer age;
}
package com.example.eblog.dto;
import lombok.Data;
@Data
public class PostInputRequest {
String title;
String content;
Long authorId;
}
清单 7-4
输入 DTO
```

### 查询与变更类

清单 7-5 中的类提供了实现，用于根据精确匹配的方法签名，解析模式中定义的不同类型和字段。例如，我们在模式中看到了 `newPost(input: PostInputRequest): Post!` 声明。Spring 会匹配并在所有带有 Spring 注解的类中调用签名定义为 `public Post newPost(PostInputRequest postInputRequest)` 的函数。

```
package com.example.eblog.resolvers;
import com.coxautodev.graphql.tools.GraphQLQueryResolver;
import com.example.eblog.model.Author;
import com.example.eblog.model.Post;
import com.example.eblog.repository.AuthorRepository;
import com.example.eblog.repository.PostRepository;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.domain.PageRequest;
import org.springframework.data.domain.Sort;
import org.springframework.stereotype.Service;
import java.util.List;
@Service
@Slf4j
public class QueryResolver implements GraphQLQueryResolver {
@Autowired
AuthorRepository authorRepository;
@Autowired
PostRepository postRepository;
public List recentPosts(Integer limit, Integer offset, String orderBy){
log.info("recentPosts, params: {}, {}", limit, offset);
PageRequest pageRequest = PageRequest.of(limit, offset, Sort.Direction.DESC, orderBy);
return postRepository.findAll(pageRequest).getContent();
}
public List454149_1_En authorsWithTopPosts()   {
log.info("authorsWithTopPosts");
return authorRepository.findAuthorsWithPosts();
}
}
package com.example.eblog.resolvers;
import com.coxautodev.graphql.tools.GraphQLMutationResolver;
import com.example.eblog.dto.AuthorInputRequest;
import com.example.eblog.dto.PostInputRequest;
import com.example.eblog.model.Author;
import com.example.eblog.model.Post;
import com.example.eblog.model.Status;
import com.example.eblog.repository.AuthorRepository;
import com.example.eblog.repository.PostRepository;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
@Service
@Slf4j
public class MutationResolver implements GraphQLMutationResolver {
@Autowired
AuthorRepository authorRepository;
@Autowired
PostRepository postRepository;
public Post newPost(PostInputRequest postInputRequest){
Author author = authorRepository.findById(postInputRequest.getAuthorId())
.orElse(new Author("Raj", 35));
if(author.getId() == null)
authorRepository.save(author);
Post post = Post.builder()
.title(postInputRequest.getTitle())
.content(postInputRequest.getContent())
.author(author)
.status(Status.ACTIVE)
.build();
post = postRepository.save(post);
return post;
}
public Author newAuthor(AuthorInputRequest authorInputRequest)   {
Author author = new Author(authorInputRequest.getName(), authorInputRequest.getAge());
author = authorRepository.save(author);
return author;
}
}
清单 7-5
查询类
```

说明：

*   我们实现了 `GraphQLQueryResolver` 和 `GraphQLMutationResolver` 作为标记接口。

*   代码非常直观。在 `QueryResolver` 类中，`recentPosts()` 方法返回分页的 `POST` 对象作为响应。在 `authorWithTopPosts` 方法中，我们返回了作者及其近期热门文章的对象图。这些对象被创建为嵌套对象。

*   在 `MutationResolver` 类中，我们有两个方法，用于根据用户提供的对应输入对象创建 `POST` 和 `Author` 对象。

*   GraphQL 引擎在将控制权委托给实现方法之前，会验证输入对象的字段。

*   可以通过声明一个类型为 `GraphQLErrorHandler` 的 Spring Bean 并对其进行自定义，来实现错误处理的自定义。如清单 7-6 所示。

```
package com.example.eblog.handler;
import graphql.ExceptionWhileDataFetching;
import graphql.GraphQLError;
import graphql.servlet.GraphQLErrorHandler;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import java.util.List;
import java.util.stream.Collectors;
@Configuration
public class GraphQLConfig  {
@Bean
public GraphQLErrorHandler errorHandler() {
return new GraphQLErrorHandler() {
@Override
public List processErrors(List
errors) {
List clientErrors = errors.stream()
.filter(this::isClientError)
.collect(Collectors.toList());
// 对客户端错误进行处理
List serverErrors = errors.stream()
.filter(e -> !isClientError(e))
.collect(Collectors.toList());
// 对服务端错误进行处理
return errors;
}
protected boolean isClientError(GraphQLError error) {
return !(error instanceof ExceptionWhileDataFetching ||
error instanceof Throwable);
}
};
}
}
清单 7-6
错误处理配置
```

通过 GraphQL 引擎，我们可以获取任何请求中的错误句柄。我们可以记录这些错误，或者向错误列表中添加更多信息。



### 配置

我们可以访问声明 Hibernate 连接属性和自定义上下文路径所需的通用属性。GraphQL Java 引擎要求我们定义 `graphql.servlet.mapping` 属性，以便声明其 `POST` RESTful 端点的映射路径。GraphiQL 是来自 `graphql-tools` 项目的内置应用程序，需要声明类似的、不言自明的属性。参见清单 7-7。

```
server:
port: 8201
servlet:
contextPath: /eblog
graphql:
servlet:
mapping: /graphql
enabled: true
corsEnabled: true
graphiql:
mapping: /graphiql
endpoint: /eblog/graphql
enabled: true
pageTitle: GraphiQL
spring:
datasource:
url: jdbc:mysql://localhost:3306/eblog?autoReconnect=true
username: root
password: mysql
driver-class-name: com.mysql.jdbc.Driver
jpa:
hibernate.ddl-auto: create
show-sql: true
generate-ddl: true
清单 7-7
application.yml 文件
```

## 运行 eblog-graphql-ch7 应用程序

```
> gradle bootRun
>
:: Spring Boot ::        (v2.1.3.RELEASE)
2019-03-24 09:24:10.622  INFO 77186 --- [           main] com.example.eblog.App                    : Starting App on Rajs-MacBook-Pro.local with PID 77186 (started by raj in /Users/raj/work_all/book_ code/rapid-java-persistence-and-microservices/ch7/eshop-graphql-ch7)
2019-03-24 09:24:11.479  INFO 77186 --- [           main] .s.d.r.c.RepositoryConfigurationDelegate : Finished Spring Data o.hibernate.jpa.internal.util.LogHelper  : HHH000204: Processing PersistenceUnitInfo [
name: default
...]
2019-03-24 09:24:13.053  INFO 77186 --- [           main] org.hibernate.Version                    2019-03-24 09:24:15.531  INFO 77186 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat started on port(s): 8201 (http) with context path '/eblog'
2019-03-24 09:24:15.533  INFO 77186 --- [           main] com.example.eblog.App                    : Started App in 5.404 seconds (JVM running for 5.872)
```

请注意，Hibernate 正在生成以下语句：

```
Hibernate: drop table if exists author
Hibernate: drop table if exists post
Hibernate: create table author (id bigint not null auto_increment, age integer not null, name varchar(255), status integer, primary key (id)) engine=MySQL5InnoDBDialect
Hibernate: create table post (id bigint not null auto_increment, content varchar(255), status integer, title varchar(255), author_id bigint, primary key (id)) engine=MyISAM
Hibernate: alter table post add constraint FK5l759v7apba3lqguc7bp8h456 foreign key (author_id) references author (id)
```

现在打开浏览器，访问 `http://localhost:8201/eblog/graphiql` URL 以启动 GraphiQL 编辑器（参见图 7-1）。

![../images/454149_1_En_7_Chapter/454149_1_En_7_Fig1_HTML.jpg](img/454149_1_En_7_Fig1_HTML.jpg)

图 7-1.

GraphiQL 编辑器

请注意关于编辑器的以下几点：

*   左侧面板接受输入。
*   右侧面板将显示输出或错误。
*   内置支持美化、历史记录、文档和查询变量。
*   如前所述，您可以删除所有内容，然后按 Crtl+Space 获取所有建议。

让我们看看两个预定义的变更和查询输入及其结果（参见图 7-2 和 7-3）。

![../images/454149_1_En_7_Chapter/454149_1_En_7_Fig3_HTML.jpg](img/454149_1_En_7_Fig3_HTML.jpg)

图 7-3.

GraphiQL 查询示例

![../images/454149_1_En_7_Chapter/454149_1_En_7_Fig2_HTML.png](img/454149_1_En_7_Fig2_HTML.png)

图 7-2.

GraphiQL 变更示例

要点：

*   我们将两个数据更新合并到一个变更请求中，分别命名为 `newPost` 和 `newAuthor`**。**
*   请求以 `mutation {` 开头，然后从那里开始实际的多个定义输入。
*   在变更中，您可以期望返回的对象图属性。仅当在模式中针对该变更指定了返回类型时，才允许这样做。
*   与变更类似，查询也被合并到单个网络请求中——`recentPosts` 和 `authorsWithTopPosts`。
*   请注意，`recentPosts` 查询也接收名为 `limit` 和 `offset` 的分页属性。GraphQL 还支持类似 RelayJS 的基于游标的分页，这可能会派上用场。
*   请注意，`authorsWithTopPosts` 有一个相关的 `posts` 集合。客户端可以指定所有属性，输出中需要所有这些资源。

![../images/454149_1_En_7_Chapter/454149_1_En_7_Fig4_HTML.jpg](img/454149_1_En_7_Fig4_HTML.jpg)

图 7-4.

GraphiQL 上下文帮助示例

还可以通过此 UI 查看完整的模式，只需点击右侧的“文档”链接即可。这将显示导航侧边栏，展示模式详细信息以及描述任何字段用途的任何注释（参见图 7-5）。

![../images/454149_1_En_7_Chapter/454149_1_En_7_Fig5_HTML.jpg](img/454149_1_En_7_Fig5_HTML.jpg)

图 7-5.

GraphiQL 文档示例

如果我们有一个已弃用的字段，比如 `Author` 对象中的 `age`，我们需要按如下方式更新模式：

```
type Author {
id: ID!
name: String!
age: Int @deprecated(reason: "Field is deprecated!")
posts: [Post]!
status: Status!
}
```

这将反映在 GraphiQL 屏幕上，如图 7-6 所示。

![../images/454149_1_En_7_Chapter/454149_1_En_7_Fig6_HTML.jpg](img/454149_1_En_7_Fig6_HTML.jpg)

图 7-6.

GraphiQL

至此，我们探索基本概念和 Spring Boot 入门点的示例就结束了。让我们也看看一些可能有助于探索 GraphQL Java API 的项目：

*   GraphQL SPQR ( [`https://github.com/leangen/GraphQL-SPQR`](https://github.com/leangen/GraphQL-SPQR) ) 有助于使用代码优先的方法编写 GraphQL。
*   GraphQL JPA ( [`https://github.com/jcrygier/graphql-jpa`](https://github.com/jcrygier/graphql-jpa) ) 可用于仅从 JPA 实体使用代码优先的方法自动生成完整的 CRUD 应用程序。
*   GraphQL Java DateTime ( [`https://github.com/donbeave/graphql-java-datetime`](https://github.com/donbeave/graphql-java-datetime) ) 是另一个项目，有助于将 GraphQL 日期时间数据类型映射到 Java LocalDateTime 和其他 `java.time` 类型。

## 总结

本章概述了一个使用 GraphQL Java 库的示例，以帮助您入门。我建议您探索更多这些示例，因为这是一个不断扩展的概念领域。下一章将探讨 Java 中的另一个主要范式，它使 HTTP 通信更快并提高了可伸缩性——响应式框架。



# 8. Java 响应式开发

近年来，使用事件驱动异步运行时环境和框架进行网络编程受到了广泛关注，例如 JavaScript 中的 Node.js 和 Java 中的 RxJava（ReactiveX）。NIO（新 I/O）包是 Java 中响应式编程的基础。响应式编程在应用开发中并非新鲜事物。当面对海量数据或高并发访问时，我们通常需要异步处理来使系统快速响应。除了异步性，我们的系统还必须以非阻塞模式在流（连续的事件序列）上工作。

数据量和操作规模与日俱增，这意味着使用传统方法不断增加硬件资源几乎变得不可能。当需求下降时，我们也希望使用更简单、成本更低的硬件来处理负载。当 Node.js 展示了应用在等待 I/O 通道上浪费了多少时间时，我开始对响应式编程产生兴趣。我不会深入探讨 Node.js 的细节，因为那是一种不同的模型，而 Java 可以通过利用多核 CPU 做得更好。

## 响应式宣言

响应式宣言包含了基本目标和指导方针，可在 [`https://www.reactivemanifesto.org/`](https://www.reactivemanifesto.org/) 获取。它是多个组织为标准化响应式开发而贡献的成果。响应式宣言描述了如何基于四个原则设计响应式系统：

*   **即时响应性**：系统尽可能及时地做出响应，并对所有用户命令保持响应。系统应能快速发现问题并有效处理，即使在问题期间也能提供一致的响应。基于通过后台线程响应事件的小型无状态函数的函数式编程，是我们设计此类系统的方法之一。

*   **韧性**：系统在面临故障时仍能保持响应。系统中有时确实会发生故障。其目的是避免系统各组件的连锁反应，并优雅地处理对用户的消息传递。包括在发生故障时的恢复或重试逻辑。

*   **弹性**：系统在不同工作负载下保持响应。响应式系统可以通过增加或减少分配给这些输入的资源来响应输入速率的变化。像 Docker 和 Kubernetes 这样基于容器的部署解决方案就是弹性的例子。

*   **消息驱动**：响应式系统依赖于组件之间的异步消息传递，这确保了松耦合、隔离和位置透明性，并提供了将错误作为消息进行委托的手段。消息应该是不可变的，因此设计上就是线程安全的。Akka 和 Vert.x 是基于消息传递的流行 Java 响应式平台。

基于这些原则，已经开发了各种 API、框架和库。核心 Java 中的 NIO（新输入输出）包是一个精确处理 I/O 操作的响应式库。Netty 和 Apache Mina 已经存在多年，提供了基于 NIO 的网络选项。对于通用开发，我们仍然需要能与 Java Streams 协同工作的更高级别 API。

## 响应式流项目

响应式流是一个规范，为带有非阻塞背压的异步流处理提供了标准。所谓规范，意味着有多个实现，就像 JDBC 驱动一样。响应式流社区提供了多种语言的示例实现，可在 [`https://github.com/reactive-streams`](https://github.com/reactive-streams) 获取。对于 Java，API 可在 [`https://github.com/reactive-streams/reactive-streams-jvm`](https://github.com/reactive-streams/reactive-streams-jvm) 找到。多年来我们一直在实现两个基本概念——观察者模式和发布/订阅模式。响应式流基于这些概念，但 API 中包含以下组件：

*   **发布者**：发布者负责生成无限数量的有序元素（流），并根据从其订阅者收到的需求进行发布。

    定义：

    ```
    public interface Publisher {
    public void subscribe(Subscriber s);
    }
    ```

*   **订阅者**：就像观察者模式中的观察者一样，订阅者负责观察源流，并在生产者推送项目时做出反应。

    定义：

    ```
    public interface Subscriber {
    public void onSubscribe(Subscription s);
    public void onNext(T t);
    public void onError(Throwable t);
    public void onComplete();
    }
    ```

*   **订阅**：每个订阅者都必须实现此接口。此模型会在每次事件发生时传递给订阅者。

    定义：

    ```
    public interface Subscription {
    public void request(long n);
    public void cancel();
    }
    ```

*   **处理器**：此接口负责处理发布者接收到的项目的处理逻辑，并作为处理程序委托给订阅者。

    定义：

    ```
    public interface Processor extends Subscriber,
    Publisher {}
    ```

响应式流还涉及另外两个概念：

*   **背压**：在非响应式系统中，即使消费者无法以该速度消费，生产者也可能继续生产项目，从而对客户端造成*背压*。响应式系统允许客户端向生产者告知其容量问题以及接受更多项目的准备情况。

*   **非阻塞**：从核心 Java 领域来看，我们一直通过原子变量处理非阻塞并发应用。简而言之，响应式系统允许用户线程继续处理操作，将所有 I/O 操作放入队列。然后它们可以拾取进程并通过回调响应用户线程。这就是 Node.js 世界的工作方式，通过使用事件循环来处理所有后台操作。

由于在 Java 中构建响应式系统有多种 API，我在此介绍其中几种，然后详细解释一个作为示例。

## RxJava（响应式扩展）项目

RxJava 是另一个用于响应式开发的框架；它基于 ReactiveX 实现（ [`http://reactivex.io/`](http://reactivex.io/) ）。ReactiveX 是观察者模式、迭代器模式和函数式编程最佳思想的结合。在 ReactiveX 中，观察者订阅一个可观察对象。然后观察者对可观察对象发出的项目流做出反应（就像响应式流中的发布者）。通过使用 Java 并发 API 并将处理卸载到后台线程，用户操作可以等待 Future 对象，而底层并发和非阻塞 I/O 问题则由框架负责。到目前为止，该 API 有两个版本，目前使用的是 RxJava 2。ReactiveX 是伞形项目，RxJava 是其下的 Java 库。其中一些概念也出现在 JDK 中，因此我们将在下一节中查看 Java 9 的 API。



## Java 核心库中的响应式 API

Java 8 开始提供函数式编程和流处理的基础结构。Java 9 根据响应式流规范引入了 Flow API 和响应式编程的通用接口，这些接口从 Java 8 的改进中获益良多。以下 API 位于 Java 9 的 `java.util.concurrent.Flow` 类中：

*   `Flow.Publisher<T>` 接口定义了生成数据项和控制信号的方法。
*   `Flow.Subscriber<T>` 接口定义了接收这些消息和信号的方法。
*   `Flow.Subscription` 接口定义了连接发布者和订阅者的方法。
*   `Flow.Processor<T,R>` 接口定义了执行高级操作的方法，例如将发布者的数据项进行链式转换后传递给订阅者。
*   该 API 中唯一的类 `SubmissionPublisher<T>`，使用其构造函数中提供的 [`Executor`](https://docs.oracle.com/javase/9/docs/api/java/util/concurrent/Executor.html) 来向订阅者传递数据，以处理背压。

从 Java 1.3 开始，我们就使用 `wait()` 和 `notify()` 构建基于生产者和消费者的多线程应用程序。到了 Java 5，我们可以使用阻塞队列、执行器和其他并发 API 轻松实现这一点。简而言之，在分布式系统中，这些任务是通过使用消息传递机制以发布者/订阅者的模式实现的。借助 Java 9，我们可以基于这些响应式 API，以发布者/订阅者的形式实现相同的生产者/消费者模式。这些 API 很有趣，但仅凭它们可能无法构建完整的实际应用程序，因此我们将在下一节中介绍 Spring 响应式 API。

## Reactor 与 Spring 响应式

在本节中，我们将深入探讨一个具体示例，因为我希望专注于创建一个涉及 HTTP 请求和数据库连接的完整 Web 应用程序（微服务）。Spring 社区启动了一个名为“Project Reactor”（[`https://projectreactor.io/`](https://projectreactor.io/)）的项目，旨在创建一个完全非阻塞的基础架构。该 API 可以轻松地与 Java 函数式 API、CompletableFuture 和 Stream API 集成。

Spring 框架在 Reactor API 之上构建了另一个 Web API（Web Flux）。这是基于 Servlet API 构建的 Spring Web MVC API 的替代方案。根据 Spring 文档，创建此 API 的原因是需要一个非阻塞 API，以便用更少的线程处理并发，并在更少的硬件资源下进行扩展。Java 中的响应式 API 是从早期版本的功能演变而来的，例如注解、并发 API、原子类和 NIO 包。Java 8 中的函数式编程、Streams API 和 CompletableFuture API 也为响应式 Java 的成功做出了贡献。

在 Spring WebFlux 中，我们有 Mono 和 Flux API。它们提供了丰富的函数式操作符，用于处理（0 或 1 个）或（0 到 N 个）数据项的流。WebFlux API 接受一个发布者作为输入，允许操作符对其进行处理，并最终返回 `Mono<T>` 或 `Flux<T>` 作为输出。Spring WebFlux 支持两种类型的响应式编程 API：

*   **注解式控制器**：Spring MVC 风格的 REST 控制器，支持 Mono 和 Flux 返回类型。
*   **函数式端点**：基于 Lambda 的轻量级函数式编程模型。

通常，对于某些开发者来说，函数式编程可能比命令式编程更难。命令式编程自然地映射到我们思考执行任务的一系列步骤的方式。此外，在企业级应用程序中，我们可能需要使用许多尚未迁移到函数式编程的框架和库。例如，JPA 和 JDBC 就是如此。最近，社区开始致力于响应式数据库驱动程序和 Spring Data 风格的包装器 API。

该 API 名为 R2DBC（响应式关系数据库连接；参见 [`https://r2dbc.io/`](https://r2dbc.io/)）。R2DBC 是一个 API 规范，它声明了一个响应式 API，供驱动程序供应商实现，以便访问其关系数据库。在撰写本文时，有三个数据库驱动程序支持 R2DBC 规范：

*   PostgreSQL (`io.r2dbc:r2dbc-postgresql`)
*   H2 (`io.r2dbc:r2dbc-h2`)
*   Microsoft SQL Server (`io.r2dbc:r2dbc-mssql`)

现在，让我们构建一个可运行的示例来实际体验 WebFlux 和 R2DBC。

## 响应式博客微服务（基于 Spring 响应式构建）

我们将使用响应式 API 重建前面章节中的博客应用程序。其结构将与之前构建的示例非常相似，如下所示。

```
.
├── build.gradle
├── gradlew
├── gradlew.bat
├── settings.gradle
└── src
    └── main
        ├── java
        │   └── com
        │       └── example
        │           └── eblog
        │               ├── App.java
        │               ├── config
        │               │   └── Config.java
        │               ├── dto
        │               │   └── PostInputRequest.java
        │               ├── model
        │               │   └── Post.java
        │               ├── repository
        │               │   └── PostRepository.java
        │               └── web
        │                   └── PostController.java
        └── resources
```

### 应用程序设置

让我们从 [`http://start.spring.io/`](http://start.spring.io/) 开始构建一个名为 `eblog-reactive-ch8` 的新应用程序，并在 `build.gradle` 中包含以下依赖项（参见清单 8-1）。

```
buildscript {
    repositories {
        mavenCentral()
    }
    dependencies {
        classpath("org.springframework.boot:spring-boot-gradle-plugin:2.1.3.RELEASE")
    }
}
apply plugin: 'java'
apply plugin: 'eclipse'
apply plugin: 'idea'
apply plugin: 'org.springframework.boot'
apply plugin: 'io.spring.dependency-management'
bootJar {
    baseName = 'eblog-service'
    version =  '1.0'
}
repositories {
    mavenCentral()
    maven { url "https://repo.spring.io/snapshot" }
    maven { url "http://repo.spring.io/libs-milestone" }
}
dependencies {
    compile('org.springframework.boot:spring-boot-starter-webflux')
    compile("org.springframework.data:spring-data-r2dbc:1.0.0.M1")
    compile("io.r2dbc:r2dbc-spi:1.0.0.M7")
    compile("io.r2dbc:r2dbc-postgresql:1.0.0.M7")
    compile('org.projectlombok:lombok:1.18.6')
    testCompile('junit:junit:4.12')
}
清单 8-1
Build.gradle
```

### 配置

我们只需要扩展一个现有的抽象配置类，即 `AbstractR2dbcConfiguration`。我们可以覆盖其与数据库连接相关的配置，如清单 8-2 所示。

```
package com.example.eblog.config;
import io.r2dbc.postgresql.PostgresqlConnectionConfiguration;
import io.r2dbc.postgresql.PostgresqlConnectionFactory;
import org.springframework.context.annotation.Configuration;
import org.springframework.data.r2dbc.config.AbstractR2dbcConfiguration;
@Configuration
public class Config extends AbstractR2dbcConfiguration {
    @Override
    public PostgresqlConnectionFactory connectionFactory() {
        PostgresqlConnectionConfiguration connectionConfiguration =
            PostgresqlConnectionConfiguration.builder()
                .applicationName("eblog")
                .database("eblog")
                .host("localhost")
                .username("postgres")
                .password("postgres").build();
        return new PostgresqlConnectionFactory(connectionConfiguration);
    }
}
清单 8-2
配置类
```

根据这段代码，我们覆盖了 `AbstractR2dbcConfiguration` 类的 `connectionFactory()` 方法，该方法用于连接到 PostgreSQL 中的 `eblog` 数据库。我们还需要使用以下脚本手动创建一个 `Post` 表：

```
CREATE TABLE public.post (
    id serial NOT NULL,
    title varchar(500) NULL,
    "content" text NULL,
    author int4 NULL,
    CONSTRAINT post_pkey PRIMARY KEY (id)
);
```



### 模型类与仓库定义

我们将创建一个带有 Spring Data 注解的简单 *POJO*（普通 Java 对象）（参见清单 8-3）。我们还将查看清单 8-4 中的 Spring Data 响应式仓库定义。

```
package com.example.eblog.model;
import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.Data;
import org.springframework.data.annotation.Id;
import org.springframework.data.relational.core.mapping.Column;
import java.io.Serializable;
@Data
@Builder
@AllArgsConstructor
public class Post implements Serializable {
@Id
private Long id;
private String title;
@Column("content")
private String content;
public Post()   {}
public Post(String title, String content)   {
this.title = title;
this.content = content;
}
}
清单 8-3
模型类
```

我们有一个名为 `Post` 的 Java 对象，它带有来自 Spring Data 的 `@Id` 和 `@Column` 注解，但我们这里没有使用 Spring Data JPA 注解。

```
package com.example.eblog.repository;
import com.example.eblog.model.Post;
import org.springframework.data.repository.reactive.ReactiveCrudRepository;
import org.springframework.stereotype.Repository;
public interface PostRepository extends ReactiveCrudRepository { }
清单 8-4
仓库定义
```

要使用 Spring Data R2DBC 仓库，我们需要使用 Spring Data Commons 项目中的注解来标注模型类的字段。在清单 8-3 的代码中，我们有一个 `Post` Java 对象，它带有来自 Spring Data Commons 的 `@Id` 和 `@Column` 注解。Spring Data R2DBC 提供了实现，通过这些注解来管理数据模型的持久化。

### DTO 与 Web 控制器

我们将首先定义一个名为 `PostInputRequest` 的简单 POJO，其中包含创建 `User` 领域对象所需的输入字段（参见清单 8-5）。接着，我们将定义一个 `PostController` 类（参见清单 8-6），其方法返回 `reactor.core.publisher.*` API 类（Flux 或 Mono）。

```
package com.example.eblog.web;
import com.example.eblog.dto.PostInputRequest;
import com.example.eblog.model.Post;
import com.example.eblog.repository.PostRepository;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.domain.PageRequest;
import org.springframework.data.domain.Sort;
import org.springframework.data.repository.query.Param;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RestController;
import reactor.core.publisher.Flux;
import reactor.core.publisher.Mono;
@RestController
@Slf4j
public class PostController {
@Autowired
PostRepository postRepository;
@PostMapping("/posts")
public Mono newPost(@RequestBody PostInputRequest postInputRequest){
Post lclPost = Post.builder()
.title(postInputRequest.getTitle())
.content(postInputRequest.getContent())
.build();
Mono post = postRepository.save(lclPost);
return post;
}
@GetMapping("/posts")
Flux allPosts() {
return this.postRepository.findAll();
}
@GetMapping("/recentPosts")
public Flux recentPosts(
@Param("limit") Integer limit, @Param("offset") Integer offset,
@Param("orderBy") String orderBy){
log.info("recentPosts, params: {}, {}", limit, offset);
PageRequest pageRequest = PageRequest.of(limit, offset, Sort.Direction.DESC, orderBy);
return postRepository.findAll();
}
}
清单 8-6
控制器类
```

```
package com.example.eblog.dto;
import lombok.Data;
@Data
public class PostInputRequest {
String title;
String content;
Long authorId;
}
清单 8-5
DTO 类
```

### 引导配置

我们需要在 Spring Boot 中启用 R2DBC 特性，从而在 `Application` 类上使用其注解（`@EnableR2dbcRepositories`）（参见清单 8-7）。

```
package com.example.eblog;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.data.r2dbc.repository.config.EnableR2dbcRepositories;
@SpringBootApplication
@EnableR2dbcRepositories
public class App {
public static void main(String[] args) {
SpringApplication.run(App.class, args);
}
}
清单 8-7
应用类
```

我们还需要运行一个初始 SQL 脚本来初始化 PostgreSQL 数据库中的表（参见清单 8-8）。

```
create table POST (
id serial primary key not null,
title varchar(500),
content text
);
清单 8-8
初始模式脚本
```

### 输出

现在让我们看一下 Gradle 运行的控制台输出：

```
> gradle bootRun
:: Spring Boot ::        (v2.1.3.RELEASE)
2019-03-18 07:53:39.988  INFO 25793 --- [           main] com.example.eblog.App                    : Starting App on Rajs-MacBook-Pro.local with PID 25793 (/Users/raj/work_all/book_extra_code/eblog-reactive/out/production/classes started by raj in /Users/raj/work_all/book_extra_code/eblog-reactive)
2019-03-18 07:53:39.991  INFO 25793 --- [           main] com.example.eblog.App                    : No active profile set, falling back to default profiles: default
2019-03-18 07:53:40.432  INFO 25793 --- [           main] .s.d.r.c.RepositoryConfigurationDelegate : Bootstrapping Spring Data repositories in DEFAULT mode.
2019-03-18 07:53:40.562  INFO 25793 --- [           main] .s.d.r.c.RepositoryConfigurationDelegate : Finished Spring Data repository scanning in 126ms. Found 2 repository interfaces.
2019-03-18 07:53:41.839  INFO 25793 --- [           main] o.s.b.web.embedded.netty.NettyWebServer  : Netty started on port(s): 8080
2019-03-18 07:53:41.844  INFO 25793 --- [           main] com.example.eblog.App                    : Started App in 2.291 seconds (JVM running for 2.725)
```

### 示例客户端

我们将使用 CURL 工具调用正在运行的应用。

命令：

```
curl --header "Content-Type: application/json" --request POST  --data '{"title":"xyz","content":"xyz"}' http://localhost:8080/posts
```

输出：

```
{"id":2,"title":"xyz","content":"xyz"}
```

命令：

```
> curl -X GET http://localhost:8080/posts
```

输出：

```
[{"id":1,"title":"Sample Title","content":"Sample Content"},{"id":2,"title":"xyz","content":"xyz"}]
```

命令：

```
>curl "http://localhost:8080/recentPosts?limit=10&offset=10&orderBy=id"
```

输出：

```
[{"id":1,"title":"Sample Title","content":"Sample Content"},{"id":2,"title":"xyz","content":"xyz"}]
```

## 总结

本章全面介绍了 Java 生态系统中的响应式开发，并特别介绍了 Spring 响应式 API 以及 Spring R2DBC 仓库的入门知识。这仍然是一个新兴领域，预计未来会有更多创新。我建议读者也探索其他 API。在下一章中，我们将探讨另一个有趣的概念，它有助于我们在极少的硬件资源下实现超大规模的工作。



# 9. Java WebSocket 开发

本章介绍如何在 Java 中使用 *WebSocket* 创建支持数百万并发用户的实时应用程序。WebSocket 提供了与 HTTP Web 服务器的双向持久连接，从而可以减少创建 HTTP 连接所需的时间。HTTP 1.1 也通过 *keep-alive 标头* 允许长连接，但每个请求中的有效负载明显大于 WebSocket。

人们经常将 WebSocket 与支持服务器推送事件（SSE）的 HTTP/2 进行比较。HTTP/2 允许在连接级别进行请求多路复用，SSE 允许服务器端数据推送，但即使将两者结合，也无法完全替代 WebSocket。当我们只有服务器端数据推送用例（例如向客户端发送通知）时，SSE 可以工作。然而，对于像聊天这样的双向对话场景，WebSocket 更胜一筹。

请思考一下为什么连接管理如此重要。简单来说，如果我们有一个拥有 100 万活跃用户的游戏平台，每个连接需要近 10 毫秒来创建，这意味着我们仅在连接上就花费了 10,000 秒的时间。现在想想，如果每个用户都在随机时间间隔内通过调用 REST API 发送数百个请求，那么将会创建和关闭多少个连接？我曾有过使用 WebSocket 为 300 到 400 万连接用户提供近乎实时响应的经验。

在 Java 中有许多创建 WebSocket 的选项。我们可以使用 Spring 框架、JAX-RS、GraphQL、Spark 框架等。在本章中，我们将以 Spark 框架（[`http://sparkjava.com`](http://sparkjava.com)）为例，因为它简单易用。该框架已在第 2 章中介绍过。

我们将构建一个 Tweet 微服务，该服务可以同时向所有连接的用户发送推文。这通常用于聊天程序和实时游戏应用程序。我们将使用以下代码结构作为此应用程序的一部分：

```
.
├── build.gradle
├── gradle
│   └── wrapper
│       ├── gradle-wrapper.jar
│       └── gradle-wrapper.properties
├── gradlew
├── gradlew.bat
├── settings.gradle
└── src
    └── main
        ├── java
        │   └── com
        │       └── example
        │           ├── Application.java
        │           └── web
        │               ├── MessageService.java
        │               ├── TweetController.java
        │               └── WSHandler.java
        └── resources
```

## 应用程序设置

创建一个名为 `websocket-example-ch9` 的新 Gradle 应用程序，其 `build.gradle` 文件如代码清单 9-1 所示。

```
plugins {
    id 'java'
    id "name.remal.fat-jar" version "1.0.124"
}
group = 'com.example'
version = '1.0'
sourceCompatibility = JavaVersion.VERSION_11
targetCompatibility = JavaVersion.VERSION_11
repositories {
    mavenCentral()
}
dependencies {
    compile("com.sparkjava:spark-core:2.9.0")
    compile group: 'org.json', name: 'json', version: '20180813'
    compile('org.projectlombok:lombok:1.18.6')
}
代码清单 9-1
构建脚本
```

注意：

*   我们添加了一个名为 `name.remal.fat-jar` 的新插件，它将帮助我们创建一个包含所有依赖类文件的单一 JAR 文件。

## 引导启动

我们只需三行代码即可初始化应用程序（参见代码清单 9-2）。

```
package com.example;

import com.example.web.TweetController;
import com.example.web.WSHandler;
import static spark.Spark.init;
import static spark.Spark.webSocket;

public class Application {
    public static void main(String[] args) {
        webSocket("/tweet", WSHandler.class);
        TweetController tweetController = new TweetController();
    }
}
代码清单 9-2
应用程序类
```

注意：

*   我们将 WebSocket 路径初始化为 `/tweet`，并附加了一个 WebSocket 连接处理程序类（名为 `WSHandler`）。
*   我们还初始化了一个控制器类来定义 RESTful 端点。

## Tweet 处理程序

此处理程序类将重写事件回调方法，并在用户连接、断开连接或发送消息时执行相应操作。Spark 默认使用 Jetty WebSocket API。此类主要将操作委托给一个第三方消息服务——参见代码清单 9-3。

```
package com.example.web;

import org.eclipse.jetty.websocket.api.Session;
import org.eclipse.jetty.websocket.api.annotations.OnWebSocketClose;
import org.eclipse.jetty.websocket.api.annotations.OnWebSocketConnect;
import org.eclipse.jetty.websocket.api.annotations.OnWebSocketMessage;
import org.eclipse.jetty.websocket.api.annotations.WebSocket;

@WebSocket
public class WSHandler {
    MessageService messageService = MessageService.getInstance();

    @OnWebSocketConnect
    public void onConnect(Session session) throws Exception {
        String username = messageService.newUserJoined(session);
        messageService.broadcastMessage("Server", username + " joined the chat");
    }

    @OnWebSocketClose
    public void onClose(Session session, int statusCode, String reason) {
        String username = messageService.removeUser(session);
        messageService.broadcastMessage("Server", username + " left the chat");
    }

    @OnWebSocketMessage
    public void onMessage(Session session, String message) {
        String username = messageService.getUser(session);
        messageService.broadcastMessage(username, message);
    }
}
代码清单 9-3
WebSocket 处理程序类
```

注意：

*   `onConnect`：我们调用了 `MessageService` 的 `newUserJoined` 方法，将这些会话存储到某个存储中。
*   `onClose`：我们调用了同一个 `MessageService` 的 `removeUser` 方法来处理来自终端客户端的关闭。
*   `onMessage`：接收来自终端客户端的消息，并将操作再次委托给消息服务以执行任何自定义逻辑。

## 消息服务

此类负责处理对接收到的消息应用的所有业务逻辑。我们定义了一个 `broadcastMessage()` 方法，用于向所有连接的用户发送消息。我们将所有会话保存在一个 `ConcurrentHashMap` 中，以便我们可以根据会话查找任何用户，并将消息发送回终端客户端。参见代码清单 9-4。

```
package com.example.web;

import lombok.extern.slf4j.Slf4j;
import org.eclipse.jetty.websocket.api.Session;

import java.util.Map;
import java.util.concurrent.ConcurrentHashMap;
import java.util.concurrent.atomic.AtomicInteger;

@Slf4j
public class MessageService {
    private static MessageService instance = new MessageService();

    public static MessageService getInstance() {
        return instance;
    }

    private MessageService() {}

    AtomicInteger counter = new AtomicInteger(0);
    Map<Session, String> sessionMap = new ConcurrentHashMap<>();

    public String newUserJoined(Session session) {
        String username = "User" + counter.addAndGet(1);
        sessionMap.put(session, username);
        return username;
    }

    public void broadcastMessage(String sender, String message) {
        log.info("Sending another message");
        sessionMap.keySet().stream().filter(Session::isOpen).forEach(session -> {
            try {
                session.getRemote().sendString(sender + " : " + message);
            } catch (Exception e) {
                e.printStackTrace();
            }
        });
    }

    public String removeUser(Session session) {
        String username = sessionMap.get(session);
        sessionMap.remove(session);
        return username;
    }

    public String getUser(Session session) {
        return sessionMap.get(session);
    }
}
代码清单 9-4
业务服务
```

注意：

*   我们将 `MessageService` 定义为 `Singleton`（单例），因为我们需要它的单个副本来存储和返回与会话对应的用户名。我们使用 `ConcurrentHashMap` 来保证线程安全。
*   当新用户加入时，我们将会话对象作为键，对应一个唯一的用户名。
*   当用户断开连接时，我们只需从会话存储中移除该对象。
*   使用 `broadcast` 方法，我们遍历所有会话，并将消息发送给所有连接的用户，包括当前用户。



## Tweet 控制器

在查看此应用的输出之前，我们先为系统添加一个更有趣的组件。我们需要添加一个 `TweetController` 类（见代码清单 9-5），它将暴露 `POST` RESTful 端点，用于接收消息并将其分发给所有其他已连接的用户。

```
package com.example.web;
import static spark.Spark.*;
public class TweetController {
MessageService messageService = MessageService.getInstance();
public TweetController()    {
post("/tweet", (request, response) -> {
String message = request.queryParamOrDefault("message", "SERVER");
messageService.broadcastMessage("Server", message);
return "Sent";
});
}
}
代码清单 9-5
控制器类
```

## 运行 Websocket-Example-Ch9 应用

Spark 框架最棒的一点是，服务启动时间不到 100 毫秒。我们需要创建一个 fat JAR，然后运行该 JAR 文件。默认情况下，Spark 框架会在端口 `4567` 上启动应用。

```
> gradle clean build && gradle fatJar
> java -jar ./build/libs/websocket-example-all-1.0.jar
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

为了测试 Websocket 连接，我们首先使用 [`http://www.websocket.org/echo.html`](http://www.websocket.org/echo.html) 上的示例 Web 客户端进行测试。然后，我们在浏览器的两个标签页中创建两个连接，并使用 `Curl` 命令创建一个控制台用户。图 9-1 和 9-2 展示了示例输出。

![../images/454149_1_En_9_Chapter/454149_1_En_9_Fig2_HTML.jpg](img/454149_1_En_9_Fig2_HTML.jpg)

图 9-2
第二个标签页中的第二个 Websocket 连接

![../images/454149_1_En_9_Chapter/454149_1_En_9_Fig1_HTML.jpg](img/454149_1_En_9_Fig1_HTML.jpg)

图 9-1
示例 Websocket 连接

此外，我们还将通过控制台的 `CURL` 命令，从 RESTful 端点发送一条推文：

```
> curl -P -v "http://localhost:4567/tweet" --data-urlencode "message=Hello from Raj"
```

结果，从 `CURL` 命令发送到 REST 端点的消息应立即在浏览器窗口中可见。图 9-3 显示了由控制台用户发送并由浏览器第二个标签页中的 Websocket 接收的消息。

![../images/454149_1_En_9_Chapter/454149_1_En_9_Fig3_HTML.jpg](img/454149_1_En_9_Fig3_HTML.jpg)

图 9-3
广播消息示例

## 总结

本章通过一个基本示例演示了如何使用 Java 进行 Websocket 编程。围绕这一概念，还有许多其他概念值得探索。Spring 框架也提供了基于 STOMP 协议的 Websocket API。下一章将探讨另一种编程风格，它通过不同的方法（即无服务器开发）来帮助管理应用的规模。

# 10. Java 无服务器开发

无服务器编程正逐渐被主流开发所采用。从根本上说，无服务器编程意味着服务器或基础设施由基础设施/云提供商管理，但代码仍然存在并在某台机器上运行。作为开发者，你应该专注于解决问题和部署。根据规模需求，基础设施提供商或框架负责随时管理运行所提供代码的活动节点数量。换句话说，机器的分配是动态的，由基础设施提供商管理。无服务器化既有不少优势，也存在一些顾虑。让我们来看看其中的一些。

优势：

*   无服务器架构提供了更高的可扩展性和灵活性。
*   无服务器架构允许开发者快速发布版本。
*   定价更优，因为无需 DevOps，也没有服务器成本。
*   代码库非常小，因此更易于管理。
*   无服务器架构具有无状态的函数式编程特性。
*   对于事件驱动型场景，这是一个良好的编程环境。

顾虑：

*   无服务器计算引入了新的安全问题。
*   无服务器架构不适合长时间运行的进程。
*   以无服务器模式设计整个电子商务系统或交易系统是一项重大挑战。
*   如果使用 Docker 容器，我们可以在普通硬件上处理数百万次点击并运行完整的应用，那么为什么还需要无服务器编程来实现扩展？这是最常见的问题之一。
*   数据库查询更困难。想想我们在 Java 应用中对连接池的关注。在无服务器环境中，我们将无法利用这一点。
*   调试通常很困难，并且主要依赖于日志。
*   我们需要保持节点“热”状态。只要对无服务器函数的请求不断，它就会保持活动状态。否则，基础设施提供商可能会丢弃运行它的容器。这可能导致响应时间变慢，因此需要通过定期发送一些请求来保持其活动状态。

几乎所有云平台的 Java 都提供了用于无服务器开发的 API。然而，有一个名为 *Apache OpenWhisk* 的通用 API 模型，它允许你创建通用的、与云无关的函数，这些函数以无服务器模式运行。总的来说，我们有以下选择：

*   AWS Lambda 函数 ( [`https://aws.amazon.com/lambda/`](https://aws.amazon.com/lambda/) )
*   Azure 无服务器函数 ( [`https://azure.microsoft.com/en-in/services/functions/`](https://azure.microsoft.com/en-in/services/functions/) )
*   Google Cloud 函数 ( [`https://cloud.google.com/functions/`](https://cloud.google.com/functions/) )
*   OpenWhisk ( [`https://openwhisk.apache.org/`](https://openwhisk.apache.org/) )

使用所有这些及其自定义 API 可能非常耗时。Spring 框架在这方面提供了一个优势，它在 Spring Cloud 下有一个子项目，称为 Spring Cloud Functions（SCF：[`https://spring.io/projects/spring-cloud-function`](https://spring.io/projects/spring-cloud-function)）。

SCF 提供了统一的 API 来与所有这四个无服务器提供商协作。除此之外，它还允许开发者创建通用的基于 Spring 的函数，并独立运行。Java 8 中引入的函数式编程结构在这里甚至更有用，因为我们打算在这里使用函数式编程。让我们用以下代码结构构建一个 Spring Cloud Function 的示例：

```
.
├── build.gradle
├── gradle
│   └── wrapper
│       ├── gradle-wrapper.jar
│       └── gradle-wrapper.properties
├── gradlew
├── gradlew.bat
├── settings.gradle
└── src
└── main
├── java
│   └── com
│       └── example
│           └── App.java
└── resources
```

## 应用设置

从 [`http://start.spring.io/`](http://start.spring.io/) 创建一个名为 `serverless-example-ch10` 的新项目，并在 `build` 文件中包含代码清单 10-1 所示的依赖项。

```
plugins {
id 'org.springframework.boot' version '2.1.3.RELEASE'
id 'java'
}
apply plugin: 'io.spring.dependency-management'
java {
group = 'com.example'
version = '1.0'
sourceCompatibility = JavaVersion.VERSION_1_8
targetCompatibility = JavaVersion.VERSION_1_8
}
bootJar  {
archiveBaseName = 'serverless-example'
version =  '1.0'
}
repositories {
mavenCentral()
}
dependencies {
compile("org.springframework.cloud:spring-cloud-starter-function-web")
compile("org.springframework.cloud:spring-cloud-function-adapter-aws")
compile("org.projectlombok:lombok:1.18.6")
}
dependencyManagement {
imports {
mavenBom "org.springframework.cloud:spring-cloud-dependencies:Greenwich.SR1"
}
}
代码清单 10-1
构建脚本
```



## 功能代码

我们有一个非常小的功能代码片段，默认情况下会部署到 Tomcat 实例中。如前所述，它可以在所有四个平台上使用。根据 Spring Cloud Function 文档，我们可能需要进行一些微调。请参见清单 10-2。

```
package com.example;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.Bean;
import reactor.core.publisher.Flux;
import java.util.function.Supplier;
@SpringBootApplication
public class App {
public static void main(String[] args) {
SpringApplication.run(App.class, args);
}
@Bean
public Supplier> randomWords() {
return () -> Flux.fromArray(new String[] { "Raj", "Malhotra"});
}
}
清单 10-2
应用程序类
```

注意：

*   默认情况下，Spring Boot 在此处使用 Tomcat，并创建一个 Web 应用程序，该应用程序会暴露所有已定义的功能方法，这些方法可通过 URL 访问。

*   相同的代码库可以用于不同的平台（AWS、Azure 或 OpenWhisk）。

*   可以进行一些优化以缩短启动时间：
    *   尽量避免使用 `Component`，因为它会减慢启动过程。

    *   出于同样的原因，尽量避免使用 `Autowiring`，而应使用构造函数注入。

## 运行 Serverless-Example-Ch10 应用程序

```
> gradle bootRun
>
:: Spring Boot ::        (v2.1.3.RELEASE)
2019-03-25 08:20:04.204  INFO 16506 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat started on port(s): 8080 (http) with context path "
2019-03-25 08:20:04.208  INFO 16506 --- [           main] com.example.App                          : Started App in 2.401 seconds (JVM running for 2.803)
```

为了测试此函数，请运行以下 Curl 命令：

```
> curl http://localhost:8080/randomWords
["Raj","Malhotra"]
```

我们调用了 `randomWords` 函数，并收到了响应 `["Raj","Malhotra"]`。

## 总结

在本章中，我们学习了如何开始使用 Spring Cloud Function。我建议你尝试使用 Apache OpenWhisk Adapter API。接下来，我们将在下一章中探讨 Java 消息传递选项。

# 11. Java 消息传递

从我职业生涯开始，消息传递就一直是一个让我感兴趣的话题。多年来，Java 生态系统采用了多种不同风格的事件驱动编程。自 1998 年以来，我们一直拥有 JMS（Java 消息服务）API，并且它至今仍在发挥着同样的作用。如今，架构师们更倾向于使用 Spring 集成、Camel 和其他平台 API，而不是纯 JMS 解决方案。进入当前的消息代理时代，我们有 AMQP 代理（ActiveMQ、RabbitMQ、QPid、IronMQ 等）这样的整体解决方案、流式解决方案（Kafka 和云提供商提供的选项），以及商业解决方案，如 Tibco 和 IBM MQ。我们将研究其中两个代理，并使用 Java API 涵盖各种底层概念——RabbitMQ 和 Kafka。此应用程序的代码结构如下：

```
.
├── build.gradle
├── gradle
│   └── wrapper
│       ├── gradle-wrapper.jar
│       └── gradle-wrapper.properties
├── gradlew
├── gradlew.bat
└── src
└── main
├── java
│   └── com
│       └── example
│           ├── Application.java
│           ├── kafka
│           │   ├── KafkaConsumer.java
│           │   └── KafkaProducer.java
│           └── rabbitmq
│               ├── Consumer.java
│               ├── Producer.java
│               └── RabbitMQConfig.java
└── resources
├── application.properties
└── application.yml
```

## RabbitMQ

RabbitMQ 是一个非常流行的消息代理，支持 AMQP 1.0 协议。在消息传递领域，RabbitMQ 是用 Erlang 编写的，并且仍然是该领域的领导者。Java 中有多个 API，但我们将继续仅使用 Spring 框架。通常，在 RabbitMQ 代理中，我们有一个交换器，它保存所有消息，并根据交换器的路由键进一步分发这些消息。图 11-1 展示了此通信过程。

![../images/454149_1_En_11_Chapter/454149_1_En_11_Fig1_HTML.png](img/454149_1_En_11_Fig1_HTML.png)

图 11-1

RabbitMQ 交换器

过程如下：

1.  发布者将消息发布到 RabbitMQ 交换器。

2.  根据路由键，交换器将消息分发到其队列。

3.  连接到不同队列的消费者根据其兴趣接收消息。

4.  根据交换器类型，消息被发送到一个或多个队列。交换器类型可以是 Direct、Fanout、Topic 和 Headers。

现在，让我们在 OSX 上使用 Spring Boot 和 RabbitMQ 创建一个示例。

### 应用程序设置

我们首先使用 `brew` 命令在 OSX（MacBook）上安装 RabbitMQ：

```
> brew install rabbitmq
> brew services start rabbitmq
```

然后，我们创建一个名为 `messaging-examples-ch11` 的新 Gradle 项目，其 `build.gradle` 文件中的依赖项如清单 11-1 所示。

```
plugins {
id 'org.springframework.boot' version '2.1.3.RELEASE'
id 'java'
}
apply plugin: 'io.spring.dependency-management'
apply plugin: 'project-report'
java {
group = 'com.example'
version = '1.0'
sourceCompatibility = JavaVersion.VERSION_1_8
targetCompatibility = JavaVersion.VERSION_1_8
}
bootJar {
archiveBaseName = 'messaging-example'
version =  '1.0'
}
repositories {
mavenCentral()
}
dependencies {
compile('org.projectlombok:lombok:1.18.6')
compile("org.springframework.boot:spring-boot-starter-amqp")
}
清单 11-1
构建文件
```

### 引导

我们有一个简单的 `Application` 类，它在 Spring 框架中启用了调度功能（参见清单 11-2）。我们将使用调度器持续向代理的队列发送消息。

```
package com.example;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.scheduling.annotation.EnableScheduling;
@SpringBootApplication
@EnableScheduling
public class Application {
public static void main(String[] args) {
SpringApplication.run(Application.class, args);
}
}
清单 11-2
应用程序类
```

### RabbitMQ 配置

在我们开始使用生产者和消费者之前，我们需要通过 Spring 配置来声明交换器、队列以及这两者之间的绑定（参见清单 11-3）。

```
package com.example.rabbitmq;
import org.springframework.amqp.core.*;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
@Configuration
public class RabbitMQConfig {
@Bean
public Exchange ordersExchange() {
return new DirectExchange("orders_exchange");
}
@Bean
public Queue ordersQueue() {
return new Queue("orders");
}
@Bean
public Queue messagesQueue() {
return new Queue("notifications");
}
@Bean
public Binding binding() {
return BindingBuilder
.bind(ordersQueue())
.to(ordersExchange())
.with("orders.*").noargs();
}
@Bean
public Binding binding2() {
return BindingBuilder
.bind(messagesQueue())
.to(ordersExchange())
.with("notifications.*").noargs();
}
}
清单 11-3
配置
```

注意：

*   我们创建了一个名为 `orders_exchange` 的直接交换器，以及两个名为 `orders` 和 `notifications` 的队列。

*   我们还创建了两个绑定，分别使用路由键 `orders.*` 和 `notifications.*`，将队列与交换器绑定。

*   在此示例中，我们不会使用 notifications 队列。我们只是将其作为一个示例来展示如何从同一个交换器路由不同类型的消息。



### 消息生产者

我们将通过 `@Scheduled` 调度一个方法，该方法持续生成新消息并将其发送到代理。在本示例中，我使用了 Spring Boot 中 RabbitMQ 服务器的默认设置（参见清单 11-4）。默认属性如下：

*   `spring.rabbitmq.port`：用于指定端口；默认为 `5672`。

*   `spring.rabbitmq.host`：用于指定主机；默认为 `localhost`。

*   `spring.rabbitmq.username`：用于指定（可选的）用户名。

*   `spring.rabbitmq.password`：用于指定（可选的）密码。

```
package com.example.rabbitmq;
import lombok.extern.slf4j.Slf4j;
import org.springframework.amqp.core.Queue;
import org.springframework.amqp.rabbit.core.RabbitTemplate;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.scheduling.annotation.Scheduled;
import org.springframework.stereotype. Service;
import java.util.Calendar;
@Service
@Slf4j
public class Producer {
@Autowired
private RabbitTemplate rabbitTemplate;
@Scheduled(fixedRate = 1000)
public void send() {
String message = "Next order received at " + Calendar.getInstance().getTime();
this.rabbitTemplate.convertAndSend("orders_exchange", "orders", message);
log.info("RabbitMQ Message sent : {}", message);
}
}
清单 11-4
生产者类
```

说明：

*   我们通过自动装配注入了 `RabbitTemplate` 对象。

*   `@Scheduled(fixedRate = 1000)` 注解将确保每 1000 毫秒调用一次 `send()` 方法。

*   我们正在生成一条新消息并将其通过网络发送出去。

*   我们将消息发送到名为 `orders_exchange` 的交换机，路由键设置为 `orders`。`RabbitTemplate` 中实际方法的签名是 `public void convertAndSend(String exchange, String routingKey, Object object)`。

### 消息消费者

这里我们只有一小段代码，用于持续将接收到的消息打印到日志文件中（参见清单 11-5）。

```
package com.example.rabbitmq;
import lombok.extern.slf4j.Slf4j;
import org.springframework.amqp.rabbit.annotation.RabbitListener;
import org.springframework.stereotype.Service;
@Service
@Slf4j
public class Consumer {
@RabbitListener(queues = "orders")
public void receiveMessage(String message) {
log.info("RabbitMQ Received :: {}", message);
}
}
清单 11-5
消费者类
```

`@RabbitListener(queues = "orders")` 注解表明该方法将充当名为 `orders` 的队列中所有接收到的消息的监听器。

### 运行应用程序

运行应用程序：

```
> gradle bootRun
2019-03-24 - Starting Application on Rajs-MacBook-Pro.local with PID 1409 (started by raj in /Users/raj/work_all/book_ code/rapid-java-persistence-and-microservices/ch11/messaging-examples-ch11)
2019-03-24 - No active profile set, falling back to default profiles: default
2019-03-24 - Initializing ExecutorService 'taskScheduler'
2019-03-24 - Attempting to connect to: [localhost:5672]
2019-03-24 - Created new connection: rabbitConnectionFactory#4fa06f52:0/SimpleConnection@4f668f29 [delegate=amqp://guest@127.0.0.1:5672/, localPort= 61085]
2019-03-24 - RabbitMQ Received :: Next order received at Fri Mar 22 22:57:53 IST 2019
2019-03-24 - RabbitMQ Message sent : Next order received at Sun Mar 24 23:51:41 IST
2019-03-24 - Started Application in 2.406 seconds (JVM running for 2.915)
2019-03-24 - RabbitMQ Received :: Next order received at Sun Mar 24 23:51:41 IST 2019
2019-03-24 - RabbitMQ Message sent : Next order received at Sun Mar 24 23:51:42 IST
2019-03-24 - RabbitMQ Received :: Next order received at Sun Mar 24 23:51:42 IST 2019
2019-03-24 - RabbitMQ Message sent : Next order received at Sun Mar 24 23:51:43 IST
```

我们还可以通过 RabbitMQ 管理界面查看创建的交换机（见图 11-2）和队列（见图 11-3）。在浏览器中访问 `http://localhost:15672/#/` 网址，如果提示输入用户名和密码，请输入默认值 `guest/guest`。

![../images/454149_1_En_11_Chapter/454149_1_En_11_Fig3_HTML.jpg](img/454149_1_En_11_Fig3_HTML.jpg)

图 11-3

显示队列的 RabbitMQ 管理控制台

![../images/454149_1_En_11_Chapter/454149_1_En_11_Fig2_HTML.jpg](img/454149_1_En_11_Fig2_HTML.jpg)

图 11-2

显示 orders_exchange 的 RabbitMQ 管理控制台

## Apache Kafka

Apache Kafka 是一个流行的流处理平台。它最初由 LinkedIn 开发，并捐赠给 Apache 软件基金会。Kafka 使用 Scala JVM 语言编写，旨在提供一个大规模可扩展的平台，用于处理数百万条持续生成的消息。在本节中，我们将探讨 Spring Boot Kafka 生产者和消费者应用程序的快速入门示例。

Kafka 有多种实际应用场景。我曾见过初创公司将 Kafka 用作消息代理，而非流处理器，每天处理 5000 个订单项的消息量。这样做的原因是为了在所有需求中使用单一且一致的消息传递技术。尽管仅出于解决问题的目的，我们可以以不同方式利用 Kafka。然而，在这种负载下，我们无法充分发挥其潜力；在这种情况下，RabbitMQ 可能更合适。作为一般使用建议，RabbitMQ 应用于微服务间通信，而 Kafka 更擅长后端消息处理。请考虑以下 Kafka 使用场景：

*   电子商务行业中用于订单履约的订单后处理
*   向最终用户发送通知
*   日志和分析
*   大规模系统中的任何错误处理或发送警报
*   基于主题的 `发布/订阅` 的常规消息传递需求

### Kafka 工具和构建脚本

我们需要在机器上安装并启动 Kafka。请按照以下步骤操作：

1.  运行以下命令：

    ```
    > brew install kafka
    > brew services start kafka
    ```

    这将使用默认设置在后台启动 Zookeeper 和 Kafka。或者，我们可以使用以下命令手动启动代理：

    ```
    > cd /usr/local/Cellar/kafka/2.1.1/
    > ./zookeeper-server-start ../libexec/config/zookeeper.properties
    > ./kafka-server-start ../libexec/config/server.properties
    ```

2.  我们还可以通过基于控制台的实用程序，使用以下命令测试示例生产者和消费者：

    ```
    > ./kafka-console-producer --broker-list localhost:9092 --topic test
    >./kafka-console-consumer --bootstrap-server localhost:9092 --topic test — from-beginning
    ```

    这些实用程序可用于从控制台向代理发送任何消息。相同的消息将打印在消费者实用程序的控制台上。

3.  在同一个应用程序的 `build.gradle` 文件中添加以下依赖项：

    ```
    compile('org.apache.kafka:kafka-streams')
    compile('org.springframework.kafka:spring-kafka')
    ```



### 消息生产者

我们将创建一个定时调用另一个方法的方法，该方法会持续生成新消息，但这次会将消息发送到 Kafka 代理（参见清单 11-6）。此示例使用了 Kafka 和 Zookeeper 的端口设置，这些设置在 Spring Boot 中为默认值。这些属性的默认值如下：

*   `spring.cloud.stream.kafka.binder.brokers`：`localhost`

*   `spring.cloud.stream.kafka.binder.defaultBrokerPort`：`9092`

*   `spring.cloud.stream.kafka.binder.zkNodes`：`localhost`

*   `spring.cloud.stream.kafka.binder.defaultZkPort`：`2181`

```
package com.example.kafka;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.kafka.core.KafkaTemplate;
import org.springframework.scheduling.annotation.Scheduled;
import org.springframework.stereotype.Service;
import java.util.Calendar;
@Service
@Slf4j
public class KafkaProducer {
private static final String TOPIC = "orders";
@Autowired
private KafkaTemplate kafkaTemplate;
@Scheduled(fixedRate = 1000)
public void sendMessage() {
String message = "Next order received at " +
Calendar.getInstance().getTime();
log.info("Kafka Message sent : {}", message);
kafkaTemplate.send(TOPIC, message);
}
}
清单 11-6
KafkaProducer 类
```

说明：

*   `KafkaTemplate<Key, Value>` 是 Spring Boot 提供的便捷类，用于执行所有与 Kafka 相关的操作，就像 `RabbitTemplate` 一样。

*   我们向 `orders` 主题发送消息时并未创建该主题，因为 `KafkaTemplate` 会自动创建该主题。

### 消息消费者

与上一节中的 RabbitMQ 消费者类似，我们在清单 11-7 中有一段简短的代码，用于持续将接收到的消息打印到日志文件中。

```
package com.example.kafka;
import lombok.extern.slf4j.Slf4j;
import org.springframework.kafka.annotation.KafkaListener;
import org.springframework.stereotype.Service;
import java.io.IOException;
@Service
@Slf4j
public class KafkaConsumer {
@KafkaListener(topics = "orders", groupId = "order_grp")
public void consume(String message) throws IOException {
log.info("Kafka Received :: {}", message);
}
}
清单 11-7
Kafka 消费者
```

说明：

*   `@KafkaListener` 注解将激活 `consume()` 方法，用于监听 `orders` 主题上接收到的所有消息。

*   `groupId` 属性表示消费者组的名称。通过这种方式，可以配置多个接收器，使其在不同的组中接收相同的消息，以满足不同的处理需求。

### 再次运行应用程序

运行应用程序：



```
> gradle bootRun
>
2019-03-25 - 在 Rajs-MacBook-Pro.local 上启动应用程序，PID 为 8504（由 raj 在 /Users/raj/work_all/book_ code/rapid-java-persistence-and-microservices/ch11/messaging-examples-ch11 目录下启动）
2019-03-25 - 未设置活动配置文件，回退到默认配置文件：default
2019-03-25 - 类型为 [org.springframework.amqp.rabbit.annotation.RabbitBootstrapConfiguration$$EnhancerBySpringCGLIB$$dd819f44] 的 Bean 'org.springframework.amqp.rabbit.annotation.RabbitBootstrapConfiguration' 不符合所有 BeanPostProcessors 的处理条件（例如：不符合自动代理条件）
2019-03-25 - 类型为 [org.springframework.kafka.annotation.KafkaBootstrapConfiguration$$EnhancerBySpringCGLIB$$eac7f299] 的 Bean 'org.springframework.kafka.annotation.KafkaBootstrapConfiguration' 不符合所有 BeanPostProcessors 的处理条件（例如：不符合自动代理条件）
2019-03-25 - 正在初始化 ExecutorService 'taskScheduler'
2019-03-25 - ConsumerConfig 值：
auto.commit.interval.ms = 5000
auto.offset.reset = latest
bootstrap.servers = [localhost:9092]
check.crcs = true
client.id =
connections.max.idle.ms = 540000
default.api.timeout.ms = 60000
enable.auto.commit = true
exclude.internal.topics = true
fetch.max.bytes = 52428800
fetch.max.wait.ms = 500
fetch.min.bytes = 1
group.id = order_grp
heartbeat.interval.ms = 3000
interceptor.classes = []
internal.leave.group.on.close = true
isolation.level = read_uncommitted
key.deserializer = class org.apache.kafka.common.serialization.StringDeserializer
max.partition.fetch.bytes = 1048576
max.poll.interval.ms = 300000
max.poll.records = 500
metadata.max.age.ms = 300000
metric.reporters = []
metrics.num.samples = 2
metrics.recording.level = INFO
metrics.sample.window.ms = 30000
partition.assignment.strategy = [class
2019-03-25 - Kafka 版本：2.0.1
2019-03-25 - Kafka commitId：fa14705e51bd2ce5
2019-03-25 - 正在初始化 ExecutorService
2019-03-25 - 正在尝试连接到：[localhost:5672]
2019-03-25 - 集群 ID：pSGceLHiQbqN0GwDDSI4HA
2019-03-25 - [消费者 clientId=consumer-2, groupId=order_grp] 发现群组协调器 localhost:9092 (id: 2147483647 rack: null)
2019-03-25 - [消费者 clientId=consumer-2, groupId=order_grp] 正在撤销先前分配的分区 []
2019-03-25 - 已撤销分区：[]
2019-03-25 - [消费者 clientId=consumer-2, groupId=order_grp] (重新)加入群组
2019-03-25 - 已创建新连接：rabbitConnectionFactory#769a58e5:0/SimpleConnection@4d6ccc97 [delegate=amqp://guest@127.0.0.1:5672/, localPort= 61591]
2019-03-25 - [消费者 clientId=consumer-2, groupId=order_grp] 成功加入群组，代数为 17
2019-03-25 - [消费者 clientId=consumer-2, groupId=order_grp] 正在设置新分配的分区 [orders-0]
2019-03-25 - Kafka 消息已发送：Next order received at Mon Mar 25 00:19:22 IST 2019
2019-03-25 - RabbitMQ 已接收：Next order received at Mon Mar 25 00:14:10 IST 2019
2019-03-25 - 应用程序启动耗时 2.153 秒 (JVM 运行了 2.589 秒)
2019-03-25 - 已分配分区：[orders-0]
2019-03-25 - ProducerConfig 值：
acks = 1
batch.size = 16384
bootstrap.servers = [localhost:9092]
buffer.memory = 33554432
client.id =
compression.type = none
connections.max.idle.ms = 540000
enable.idempotence = false
interceptor.classes = []
key.serializer = class org.apache.kafka.common.serialization.StringSerializer
linger.ms = 0
max.block.ms = 60000
max.in.flight.requests.per.connection = 5
max.request.size = 1048576
metadata.max.age.ms = 300000
metric.reporters = []
metrics.num.samples = 2
metrics.recording.level = INFO
metrics.sample.window.ms = 30000
partitioner.class = class org.apache.kafka.clients.producer.internals.DefaultPartitioner
receive.buffer.bytes = 32768
reconnect.backoff.max.ms = 1000
reconnect.backoff.ms = 50
request.timeout.ms = 30000
retries = 0
retry.backoff.ms = 100
sasl.client.callback.handler.class = null
sasl.jaas.config = null
sasl.kerberos.kinit.cmd = /usr/bin/kinit
sasl.kerberos.min.time.before.relogin = 60000
sasl.kerberos.service.name = null
sasl.kerberos.ticket.renew.jitter = 0.05
sasl.kerberos.ticket.renew.window.factor = 0.8
sasl.login.callback.handler.class = null
sasl.login.class = null
sasl.login.refresh.buffer.seconds = 300
sasl.login.refresh.min.period.seconds = 60
sasl.login.refresh.window.factor = 0.8
sasl.login.refresh.window.jitter = 0.05
sasl.mechanism = GSSAPI
security.protocol = PLAINTEXT
send.buffer.bytes = 131072
ssl.cipher.suites = null
ssl.enabled.protocols = [TLSv1.2, TLSv1.1, TLSv1]
ssl.endpoint.identification.algorithm = https
ssl.key.password = null
ssl.keymanager.algorithm = SunX509
ssl.keystore.location = null
ssl.keystore.password = null
ssl.keystore.type = JKS
ssl.protocol = TLS
ssl.provider = null
ssl.secure.random.implementation = null
ssl.trustmanager.algorithm = PKIX
ssl.truststore.location = null
ssl.truststore.password = null
ssl.truststore.type = JKS
transaction.timeout.ms = 60000
transactional.id = null
value.serializer = class org.apache.kafka.common.serialization.StringSerializer
2019-03-25 - Kafka 版本：2.0.1
2019-03-25 - Kafka commitId：fa14705e51bd2ce5
2019-03-25 - 集群 ID：pSGceLHiQbqN0GwDDSI4HA
2019-03-25 - RabbitMQ 消息已发送：Next order received at Mon Mar 25 00:19:22 IST 2019
2019-03-25 - RabbitMQ 已接收：Next order received at Mon Mar 25 00:19:22 IST 2019
2019-03-25 - Kafka 已接收：Next order received at Mon Mar 25 00:14:09 IST 2019
2019-03-25 - Kafka 已接收：Next order received at Mon Mar 25 00:14:10 IST 2019
2019-03-25 - Kafka 已接收：Next order received at Mon Mar 25 00:19:22 IST 2019
2019-03-25 - Kafka 消息已发送：Next order received at Mon Mar 25 00:19:23 IST 2019
2019-03-25 - RabbitMQ 消息已发送：Next order received at Mon Mar 25 00:19:23 IST 2019
2019-03-25 - RabbitMQ 已接收：Next order received at Mon Mar 25 00:19:23 IST 2019
2019-03-25 - Kafka 已接收：Next order received at Mon Mar 25 00:19:23 IST 2019
2019-03-25 - Kafka 消息已发送：Next order received at Mon Mar 25 00:19:24 IST 2019
2019-03-25 - RabbitMQ 消息已发送：Next order received at Mon Mar 25 00:19:24 IST 2019
2019-03-25 - RabbitMQ 已接收：Next order received at Mon Mar 25 00:19:24 IST 2019
2019-03-25 - Kafka 已接收：Next order received at Mon Mar 25 00:19:24 IST 2019
2019-03-25 - Kafka 消息已发送：Next order received at Mon Mar 25 00:19:25 IST 2019
2019-03-25 - RabbitMQ 消息已发送：Next order received at Mon Mar 25 00:19:25 IST 2019
2019-03-25 - RabbitMQ 已接收：Next order received at Mon Mar 25 00:19:25 IST 2019
2019-03-25 - Kafka 已接收：Next order received at Mon Mar 25 00:19:25 IST 2019
2019-03-25 - Kafka 消息已发送：Next order received at Mon Mar 25 00:19:26 IST 2019
2019-03-25 - RabbitMQ 消息已发送：Next order received at Mon Mar 25 00:19:26 IST 2019
2019-03-25 - RabbitMQ 已接收：Next order received at Mon Mar 25 00:19:26 IST 2019
2019-03-25 - Kafka 已接收：Next order received at Mon Mar 25 00:19:26 IST 2019
2019-03-25 - Kafka 消息已发送：Next order received at Mon Mar 25 00:19:27 IST 2019
2019-03-25 - RabbitMQ 消息已发送：Next order received at Mon Mar 25 00:19:27 IST 2019
2019-03-25 - RabbitMQ 已接收：Next order received at Mon Mar 25 00:19:27 IST 2019
```

## 总结

消息传递是一个广泛的主题。本章简要介绍了如何开始使用 Spring Boot 以及 RabbitMQ 或 Kafka。本章涵盖了基于队列的 RabbitMQ 通信和基于主题的 Kafka 通信。Apache Kafka 拥有更多 API，例如 KSQL 和 Kafka Streams。我建议读者深入了解它们，因为它们提供了不同的数据处理能力。在下一章中，我们将回顾 Java 的最新改进，并将其与其他语言进行比较。顺便提一下，RabbitMQ 最适合微服务间开发，而 Kafka 最适合流式处理或数据处理。



# 12. Java 语言与生态系统回顾

多年来，我们见证了 Java 的诸多改进。其中许多 API 和概念都借鉴自 JVM 语言。本章将快速回顾 Java 近期的一些改进，这些改进使其持续成为企业应用的首选。

## 并发 API 的改进

在当今移动设备和微型互联数字设备的世界中，系统扩展的需求日益增长且要求严苛。如今，基本的并发结构已不足以应对这些扩展需求。因此，我将简要解释一些概念，展示 Java 如何通过改进并发 API 以及借鉴其他流行技术的概念，从核心层面应对这一挑战。

### 基础知识

以下基本特性使开发者能够创建响应式应用和后端，仅需中等复杂度的代码即可轻松处理数百名用户：

*   Java 的 Runnable 和线程
*   `Synchronized` 和 `volatile` 关键字
*   线程生命周期
*   `Wait`/`Notify` 方法
*   `Object` 类
*   同步集合
*   `ThreadLocal` 和 `Context` 模式
*   不可变性模式，例如 `Singleton`、`Copy Constructors` 和 `Final` 关键字

### Java 5 之后的特性

JDK 的以下新增功能带来了性能更佳的应用和更高级别的并发 API。这使得代码更简洁、更强大，能够轻松处理数千名用户。更新后的内存模型确保已初始化的“final”字段在初始化完成前不会被任何其他线程访问。

*   更新后的内存模型，使 `Final` 和 `Volatile` 更有用
*   锁 API
*   `Callable` 和 `Future`
*   执行器
*   新的快速并发集合，包括 `Queues`、`ConcurrentHashMap`、`CopyOnWriteList` 和 `CopyOnWriteSet`
*   原子变量
*   同步器，包括 `Semaphore`、`Mutex`、`CyclicBarrier`、`CountDownLatch`、`Exchanger`、`Phaser`
*   `ThreadFactory`
*   ENUMS，有助于创建不可变的单例常量，尽管 Bill Pugh 单例方法更可取

### Java 5 之后

这些技术帮助开发者轻松地为数百万用户扩展服务。尽管由于学习曲线较大，响应式编程在 Java 中的采用速度较慢，但它可以在极少的硬件资源上为应用带来真正的奇迹。

*   通过 Servlet、Netty API 和 Spring 中的 `@Async` 注解实现从同步到异步
*   通过 ForkJoin 实现并行操作
*   从 `ExecutorService` 到 `CompletionService`
*   `CompletableFuture` 用于真正的异步操作
*   `java.util.concurrent.Flow` 作为响应式基础
*   响应式编程框架，流行的选择包括：
    *   Spring Reactive
    *   RxJava
    *   Vert.x
    *   Akka

### 未来展望

一些展现未来潜力的技术：

*   Kotlin 协程：在异步编程中提供语言级别的协程支持（参见下一节关于 Kotlin 的内容）。
*   纤程、延续、生成器**和 Project Loom：回归同步世界的简洁性，但性能强大 1000 倍。

Project Loom ( [`https://openjdk.java.net/projects/loom/`](https://openjdk.java.net/projects/loom/) ) 看起来非常有前景，其灵感来源于 Go 语言的协程和纤程 ( [`http://docs.paralleluniverse.co/quasar/`](http://docs.paralleluniverse.co/quasar/) )。预期是，我们能够在单个 JVM 上生成数百万个线程，且同步开销极小。这些应该是仅由 JVM 编写和管理的轻量级线程，无需依赖可能变得相当沉重的操作系统原生线程。

## 其他改进

还有一些非常独特的功能，使得编程能够与其他语言竞争。

*   Java 生态系统中拥有庞大的集合 API 集合。JDK 本身已经包含了大多数常用的集合。此外，Apache Commons 和 Google Guava 还提供了许多额外且易用的功能。
*   局部变量类型推断。Lombok 已经为我们提供了注解快捷方式来减少样板代码，但现在 JDK 也内置了此功能。本书示例中未使用此功能，因为我希望保持示例的简单和详细。
*   自 JDK 9 起，HTTP 客户端 API 已成为内置功能。
*   函数式编程。
*   日期时间 API。
*   Lambda 函数和 Streams API。
*   Optional 以避免空值。
*   此外，我们还有以下 JVM 语言：
*   Scala
    *   更少的样板代码，更高的代码生产力。
    *   语法神奇的 API。
    *   基于 Actor 通过消息和不可变数据对象相互通信的更安全、更强大的并发模型。
    *   性能优于 Java。
    *   在脚本编写和企业级开发方面都表现出色。
    *   Scala 可能变得有点复杂，并且与 Java 代码集成时可能存在某些复杂性。
*   Kotlin
    *   与 Java API 完全兼容。
    *   数据类提供了精确的语法。例如，`data class Customer(val name: String, val email: String)`
    *   不可变性是一等公民。
    *   类型推断和函数式编程。
    *   编译速度更快。

这些语言及其框架可能还有更多特性。这里旨在展现 Java 生态系统的整体丰富性。

## 提高生产力的工具与技巧

这些技巧主要基于我的个人经验，不应被视为最终定论：

*   明智地选择函数式编程与命令式编程。函数式编程可能看起来很有吸引力，但会更复杂，需要更多的工作和预先思考。
*   如果你正在开发一个大型企业应用，最好使用单一语言（最好是 Java），而不是多种语言。
*   在性能方面，我们已经看到 Java 通过 Kafka 和 Spark 等平台取得了成功，并且业界有许多成功案例。因此，我们无需再用自制的基准测试来证明 Java 的价值。
*   Java 10 及更高版本支持类型推断，但我仍然建议包含 Lombok，因为它有许多其他可以提高生产力的注解。
*   在项目中适当且有效地使用 JVM 语言。
*   使用 IntelliJ 或 Eclipse Java 作为 IDE。如果只开发 RESTful 微服务，则不需要 Eclipse 中与 J2EE 相关的大部分插件。

## 总结

在本章中，我们回顾了每年都在提高生产力的 Java API 和功能集。我们还探讨了 JVM 语言的优势，以及一些提高生产力的技巧。

我编写本书的目的是涵盖使用当今实践构建应用所需的大部分主题。此外，它还简要介绍了企业级开发中的所有基本主题。

### 索引

### A

Actuator Apache Kafka 构建脚本 消费者 定义 @KafkaListener 注解 消息生产者 运行，应用 Apache OpenWhisk API 网关 配置 @EnableZuulProxy 网关服务 微服务 运行 Zuul 过滤器 ApiInfo API 并发特性 JVM 语言 Kotlin 生产力工具 更新后的内存模型

### B

BeanPropertyRowMapper 类 broadcastMessage() 方法

### C

CascadeType.ALL 级联类型 DataGenerator 定义 实体变更 实体修正 TestCase 类 CascadeType.REMOVE 代码复用 Grade 文件 Java 微服务 多模块项目 包管理器 product-catalog uploadArchives CommandLineRunner 接口 命令查询职责分离 (CQRS) 通用基础仓库实现 @NoRepositoryBean saveAndFlush() 方法 convertToDatabaseColumn 方法 CrudRepository

### D

发现服务 应用 引导代码 代码结构 配置 运行 设置 Docket Dropwizard 框架 Application 类 BlogApplication 类 Configuration 类 Gradle 项目 Model 类 资源类 YAML 文件



### E

@EnableEurekaServer @EnableZuulProxy Eshop 应用代码结构配置控制器类 Eureka 客户端（*参见* Eureka 客户端、订单服务）Feign（*参见* Feign、订单服务）库存服务、通信模型类 OrderService 仓库接口服务类设置 TestController Eureka 客户端、订单服务 CURL 发现服务仪表盘 @EnableDiscoveryClient inventory-service 负载均衡的 REST 模板 OrderService 运行

### F

Feign、订单服务 应用类接口 InventoryClient 新的 OrderService filterOrder() 方法 filterType() 方法 findAll() 方法 findByAge 方法 函数式编程 函数即服务（FaaS）

### G

gateway-service getCustomerNameWithEmail() 方法 图数据结构 GraphQL 创建服务定义缺点示例应用设置配置数据仓库输入 DTO 变更类查询类 schema-first 方法 SDL RESTful API URL 优势问题风险运行 eblog 上下文帮助文档编辑器变更查询反映屏幕语句

### H

Hystrix

### I

进程间通信 异步 同步 InventoryController 库存服务应用代码结构配置 CRUD 操作 CURL 命令 InventoryRepository InventoryService 模型类 Prod REST 控制器服务类设置

### J

Java 生态系统 优势 挑战 目标 问题 Java 消息服务（JMS）Kafka（*参见* Apache Kafka）RabbitMQ（*参见* RabbitMQ）Java 枚举类型 Java 持久化查询语言（JPQL）JpaRepository @JsonIgnore JSON Web 令牌（JWT）

### K

@KafkaListener 注解 Kotlin

### L

@LoadBalanced 注解 LoginRequest DTO

### M

@ManyToMany 文件实体 POST 实体测试用例 面向消息的中间件（MOM）微服务架构 断路器模式 创建 Dropwizard 框架（*参见* Dropwizard 框架）Hystrix API 端点仪表盘 eshop 服务回退机制库存服务库 OrderService 和 Application 类请求追踪 集中式日志记录日志 Spring Cloud Sleuth TraceId Zipkin 的 UI Spark 框架 Spring 框架风格 微服务架构，监控 Actuator 端点 admin-service 客户端 Application 类应用设置配置 SBA 安全访问测试，admin 服务 微服务安全 eshop 服务 LoginController 类令牌生成，测试 gateway-service AuthPreFilter 类 CURL，令牌验证

### N

newUserJoined 方法 NoSQL 数据库，Spring Data 引导 build.gradle 文件数据模型特性仓库运行应用示例应用测试用例 nullSafeGet nullSafeSet

### O

OAuth @OnetoMany 和 @ManyToOne 注解应用创建 BLOG 对象枚举 PostgreSQL 用户类型 ER 图 Gradle 应用接口 POST 对象项目设置 USER 对象用户类型 @OneToMany 注解 @OneToOne 注解 OpenAPI OpenAPI 规范（OAS）orderProduct() 方法 OrderService.handleInventoryFailure() 方法 ORM 框架

### P

PageRequest 类 PagingAndSortingRepository @PersistenceConstructor 注解 普通 Java 对象（POJO）PostgreSQL 数组类型，变更 BlogApp 测试用例 PgArrayType SQL 查询 purchaseSampleProduct() 方法

### Q

@Query 注解

### R

RabbitMQ Application 类代理构建文件通信过程配置 Consumer 类 Producer 类运行应用 响应式开发 API 博客微服务引导构建 gradle 配置类控制器类 DTO 类输出 POJO 仓库示例客户端结构原则 R2DBC Spring WebFlux Streams API 组件定义 RxJava 响应式扩展（RxJava）响应式宣言 响应式编程框架 响应式关系数据库连接（R2DBC）响应式流 removeUser 方法 请求追踪 解析器

### S

saveAndFlush() 方法 模式定义语言（SDL）Schema-first 方法 无服务器编程 优势 Apache OpenWhisk API 模型应用设置功能代码运行 SCF 服务器发送事件（SSE）服务发现 通信模式 组件 setParameterValues shouldFilter() 方法 单点登录（SSO）@Sl4j 注解 Spark Framework spring.application.name Spring @Async 注解 Spring Boot Admin（SBA）Spring Cloud Spring Cloud Config application.yml bootstrap.yml 文件 config 服务 config-service 应用 @EnableConfigServer eshop 服务配置 Git 仓库库存服务配置目标 Prod 配置文件运行应用 Spring Cloud Functions（SCF）Spring Cloud Gateway Spring Cloud Sleuth Spring Data JDBC application.properties 文件 @Async 注解 Controller 类创建应用领域模型仓库运行应用 Spring Data JPA Application 类应用运行 @ManyToMany @OneToMany 查询代码结构配置创建应用 CrudRepository 数据模型数据读取行为数据读取现象数据源配置领域模型或实体实体定义实体对象生命周期 eshop 接口隔离级别 JDBC JPQL 查询 ORM Pageable 接口 PostgreSQL 属性使用构造函数映射查询使用投影查询仓库运行应用属性服务定义解决 N+1 问题 CustomerRepository 代码 POST 对象 Spring 转换器服务用法 Spring Data Query DSL Spring 表达式语言（SpEL）Spring JDBC Template Application 类配置文件和数据库初始化脚本创建应用 CustomerOrder DTO 基于 Gradle 的应用运行应用测试用例 Spring WebFlux Swagger eshop 服务 Java 配置 RestControllers Swagger Codegen Swagger Editor Swagger UI

### T

TestController toString() 方法 TraceId TweetController 类 Tweet 处理类

### U, V

UUID

### W, X, Y

Websockets 引导 broadcastMessage() 方法代码结构连接 Message 服务 TweetController 类 tweet 处理类 withId(ID) 方法

### Z

Zuul
