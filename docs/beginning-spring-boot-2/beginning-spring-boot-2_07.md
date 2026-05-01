# 7. 使用 JOOQ

JOOQ（Java 面向对象查询）是一个拥抱 SQL 的持久化框架。JOOQ 提供了以下特性：

*   使用 DSL API 进行类型安全的 SQL 操作
*   使用代码生成进行类型安全的数据库对象引用
*   易于使用的查询和数据获取 API
*   SQL 日志记录与调试

本章将介绍如何使用 Spring Boot JOOQ Starter，如何使用 JOOQ Maven Codegen 插件从数据库模式生成代码，以及如何执行各种类型的数据库操作。



## JOOQ 简介

JOOQ 是一个 Java 持久化框架，它提供了类型安全的 QueryDSL，使你能够以类型安全的方式实现应用程序的持久层。JOOQ 提供了代码生成工具，可以根据数据库模式生成代码，你可以使用这些生成的代码来构建类型安全的查询。

你首先会看到如何使用 JOOQ 查询数据库表。

创建一个名为 `jooq-config.xml` 的配置文件，如清单 7-1 所示。

```
com.mysql.jdbc.Driver
jdbc:mysql://localhost:3306/test
root
admin

org.jooq.util.JavaGenerator

org.jooq.util.mysql.MySQLDatabase
test
.*

org.mycompany.myproj.jooq.model
C:/projects/myproject/src/main/java

清单 7-1.
jooq-config.xml 文件
```

现在，你可以运行以下命令，根据配置生成 JOOQ 代码：

```
java -classpath jooq-3.9.3.jar;jooq-meta-3.9.3.jar;jooq-codegen-3.9.3.jar;mysql-connector-java-5.1.18-bin.jar;. org.jooq.util.GenerationTool jooq-config.xml
```

假设你的数据库中有一个 `POST` 表。JOOQ 会根据 `POST` 表的元数据生成一个 `POST` 类。现在，你可以使用 JOOQ 查询 `POST` 表并获取数据，如清单 7-2 所示。

```
String userName = "root";
String password = "admin";
String url = "jdbc:mysql://localhost:3306/test";
Class.forName('com.mysql.jdbc.Driver');
Connection conn = DriverManager.getConnection(url, userName, password);
DSLContext jooq = DSL.using(conn, SQLDialect.MYSQL);
Result result = jooq.select().from(POST).fetch();
for (Record r : result)
{
Integer id = r.getValue(POST.ID);
String title = r.getValue(POST.TITLE);
String content = r.getValue(POST.CONTENT);
System.out.println("Id: " + id + " title: " + title + " content: " + content);
}
清单 7-2.
使用 JOOQ DSL API
```

此示例创建了一个数据库 `Connection` 并实例化了 `DSLContext` 对象，这是使用 JOOQ QueryDSL 的入口点。通过 `DSLContext` 对象，它查询了 `POST` 表并遍历了结果集。

你可以将 JOOQ 与 Spring 框架集成，这样就不必手动创建 `Connection` 和实例化 `DSLContext`。请参阅 [`https://www.jooq.org/doc/3.9/manual-single-page/#jooq-with-spring`](https://www.jooq.org/doc/3.9/manual-single-page/#jooq-with-spring) 了解如何将 Spring 与 JOOQ 集成。

Spring Boot 提供了 JOOQ Starter，以便你可以快速上手使用 JOOQ。你可以通过利用其自动配置机制来实现这一点。

## 使用 Spring Boot 的 JOOQ Starter

Spring Boot 提供了一个名为 `spring-boot-starter-jooq` 的 starter，它允许你快速与 JOOQ 集成。本节将逐步向你展示如何使用 `spring-boot-starter-jooq`。

### 配置 Spring Boot JOOQ Starter

创建一个基于 Maven 的 Spring Boot 项目，并添加 `spring-boot-starter-jooq` 依赖以及 H2 和 MySQL 驱动依赖。

```
org.springframework.boot
spring-boot-starter-jooq

org.springframework.boot
spring-boot-starter-test
test

com.h2database
h2

mysql
mysql-connector-java

```

此示例首先使用 H2 内存数据库，稍后你将看到如何使用 MySQL。

### 数据库模式

创建一个包含两个表（`POSTS` 和 `COMMENTS`）的简单数据库。创建名为 `src/main/resources/schema.sql` 的数据库创建脚本，如清单 7-3 所示。

```
DROP TABLE IF EXISTS POSTS;
CREATE TABLE POSTS
(
ID int(11) NOT NULL AUTO_INCREMENT,
TITLE VARCHAR(200) NOT NULL,
CONTENT LONGTEXT DEFAULT NULL,
CREATED_ON DATETIME DEFAULT NULL,
PRIMARY KEY (ID)
);
DROP TABLE IF EXISTS COMMENTS;
CREATE TABLE COMMENTS
(
ID INT(11) NOT NULL AUTO_INCREMENT,
POST_ID INT(11) NOT NULL,
NAME VARCHAR(200) NOT NULL,
EMAIL VARCHAR(200) NOT NULL,
CONTENT LONGTEXT DEFAULT NULL,
CREATED_ON DATETIME DEFAULT NULL,
PRIMARY KEY (ID),
FOREIGN KEY (POST_ID) REFERENCES POSTS(ID)
);
清单 7-3.
src/main/resources/schema.sql 文件
```

现在，使用 `src/main/resources/data.sql` 脚本填充一些示例数据，如清单 7-4 所示。

```
insert into posts(id, title, content, created_on)
values(1, 'Post 1', 'This is post 1', '2017-01-03');
insert into posts(id, title, content, created_on)
values(2, 'Post 2', 'This is post 2', '2017-01-05');
insert into posts(id, title, content, created_on)
values(3, 'Post 3', 'This is post 3', '2017-01-07');
insert into comments(id, post_id, name, email, content, created_on)
values(1, 1, 'User1', 'user1@gmail.com', 'This is comment 1 on post 1', '2017-01-07');
insert into comments(id, post_id, name, email, content, created_on)
values(2, 1, 'User2', 'user2@gmail.com', 'This is comment 2 on post 1', '2017-01-07');
insert into comments(id, post_id, name, email, content, created_on)
values(3, 2, 'User1', 'user1@gmail.com', 'This is comment 1 on post 2', '2017-01-07');
清单 7-4.
src/main/resources/data.sql 文件
```

### 使用 JOOQ Maven Codegen 插件生成代码

JOOQ 提供了 JOOQ Maven Codegen 插件，用于通过 Maven 目标生成数据库工件。在本节中，你将看到如何使用 Maven 配置文件根据数据库类型配置 `jooq-codegen-maven` 配置属性。请参见清单 7-5。

```

h2

org.jooq
jooq-codegen-maven

generate

com.h2database
h2
${h2.version}

org.h2.Driver
jdbc:h2:ʿ/springbootjooq

org.jooq.util.DefaultGenerator

org.jooq.util.h2.H2Database
.*

PUBLIC

com.apress.demo.jooq.domain
gensrc/main/java

mysql

org.jooq
jooq-codegen-maven

generate

mysql
mysql-connector-java
${mysql.version}

com.mysql.jdbc.Driver
jdbc:mysql://localhost:3306/test
root
admin

org.jooq.util.DefaultGenerator

org.jooq.util.mysql.MySQLDatabase
.*

test

com.apress.demo.jooq.domain
gensrc/main/java

清单 7-5.
pom.xml 文件中的 jooq-codegen-maven 插件配置
```

此示例配置了两个配置文件（`h2` 和 `mysql`），并带有相应的 JDBC 配置参数。它会生成代码工件，并将其放置在 `gensrc/main/java` 目录下的 `com.apress.demo.jooq.domain` 包中。

你可以运行激活 `h2` 或 `mysql` 配置文件的 Maven 构建，如下所示：

```
mvn clean install -P h2 (或) mvn clean install -P mysql
```

### 将 JOOQ 生成的代码添加为源文件夹

接下来，你必须配置 `build-helper-maven-plugin`，以便 Maven 将 JOOQ 生成的代码作为源文件夹添加到 `gensrc/main/java` 目录。

```
org.codehaus.mojo
build-helper-maven-plugin

generate-sources

add-source

gensrc/main/java

```

### 领域对象

本节展示如何创建以下领域对象，这些对象可用于在各层之间传递数据。它仅使用 JOOQ 生成的数据库工件与数据库进行通信。请参见清单 7-6。

```
package com.apress.demo.entities;
public class Post
{
private Integer id;
private String title;
private String content;
private Timestamp createdOn;
private List comments = new ArrayList();
//setters & getters
}
package com.apress.demo.entities;
public class Comment
{
private Integer id;
private Integer postId;
private String name;
private String email;
private String content;
private Timestamp createdOn;
//setters & getters
}
清单 7-6.
领域对象 Post.java 和 Comment.java
```



### 使用 JOOQ DSL

`DSLContext` 是 JOOQ DSL API 的主要入口点。你将了解如何使用 JOOQ DSL API 实现数据持久化方法。

首先，创建带有自动注入的 `DSLContext` 对象的 `PostService` 类，如清单 7-7 所示。

```
package com.apress.demo.services;
import java.sql.Timestamp;
import java.util.ArrayList;
import java.util.List;
import org.jooq.DSLContext;
import org.jooq.Record;
import org.jooq.Result;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;
import com.apress.demo.entities.Comment;
import com.apress.demo.entities.Post;
import static com.apress.demo.jooq.domain.tables.Posts.POSTS;
import static com.apress.demo.jooq.domain.tables.Comments.COMMENTS;
import com.apress.demo.jooq.domain.tables.records.CommentsRecord;
import com.apress.demo.jooq.domain.tables.records.PostsRecord;
@Service
@Transactional
public class PostService
{
@Autowired
private DSLContext dsl;
...
...
}
清单 7-7.
com.apress.demo.services.PostService.java
```

当你使用 JOOQ 查询数据库时，会得到一个记录，你可以从中提取数据。本示例将查询 `POSTS` 和 `COMMENTS` 表，因此你将创建两个工具方法，用于从 `Record` 对象中提取帖子和评论信息。

```
@Service
@Transactional
public class PostService
{
...
...
private Post getPostEntity(Record r)
{
Integer id = r.getValue(POSTS.ID, Integer.class);
String title = r.getValue(POSTS.TITLE, String.class);
String content = r.getValue(POSTS.CONTENT, String.class);
Timestamp createdOn = r.getValue(POSTS.CREATED_ON, Timestamp.class);
return new Post(id, title, content, createdOn);
}
private Comment getCommentEntity(Record r)
{
Integer id = r.getValue(COMMENTS.ID, Integer.class);
Integer postId = r.getValue(COMMENTS.POST_ID, Integer.class);
String name = r.getValue(COMMENTS.NAME, String.class);
String email = r.getValue(COMMENTS.EMAIL, String.class);
String content = r.getValue(COMMENTS.CONTENT, String.class);
Timestamp createdOn = r.getValue(COMMENTS.CREATED_ON, Timestamp.class);
return new Comment(id, postId, name, email, content, createdOn);
}
}
```

现在，在 `PostService.java` 中实现插入新 `Post` 和 `Comment` 的方法，如下所示：

```
public Post createPost(Post post)
{
PostsRecord postsRecord = dsl.insertInto(POSTS)
.set(POSTS.TITLE, post.getTitle())
.set(POSTS.CONTENT, post.getContent())
.set(POSTS.CREATED_ON, post.getCreatedOn())
.returning(POSTS.ID)
.fetchOne();
post.setId(postsRecord.getId());
return post;
}
public Comment createComment(Comment comment)
{
CommentsRecord commentsRecord = dsl.insertInto(COMMENTS)
.set(COMMENTS.POST_ID, comment.getPostId())
.set(COMMENTS.NAME, comment.getName())
.set(COMMENTS.EMAIL, comment.getEmail())
.set(COMMENTS.CONTENT, comment.getContent())
.set(COMMENTS.CREATED_ON, comment.getCreatedOn())
.returning(COMMENTS.ID)
.fetchOne();
comment.setId(commentsRecord.getId());
return comment;
}
```

该示例使用 `dsl.insertInto()` 方法插入新记录，并指定 `returning()` 方法返回自动生成的主键列值。它还调用 `fetchOne()` 方法返回新插入的记录。

现在，使用 JOOQ DSL 实现获取 `Posts` 列表的功能，如下所示：

```
public List getAllPosts()
{
List posts = new ArrayList();
Result result = dsl.select().from(POSTS).fetch();
for (Record r : result) {
posts.add(getPostEntity(r));
}
return posts ;
}
```

它使用 `dsl.select().from()` 从 `POSTS` 表中获取所有行，该方法返回 `Result<Record>`。该示例遍历 `Result<Record>`，并使用你之前创建的 `getPostEntity()` 工具方法将记录转换为 `Post` 领域对象。

现在，你将根据给定的帖子 ID 获取 `Post` 记录以及与该帖子关联的评论。

```
publicxr postId)
{
Record record = dsl.select()
.from(POSTS)
.where(POSTS.ID.eq(postId))
.fetchOne();
if(record != null)
{
Post post = getPostEntity(record);
Result commentRecords = dsl.select()
.from(COMMENTS)
.where(COMMENTS.POST_ID.eq(postId))
.fetch();
for (Record r : commentRecords) {
post.addComment(getCommentEntity(r));
}
return post;
}
return null;
}
```

最后，你将实现删除评论的方法：

```
public void deleteComment(Integer commentId)
{
dsl.deleteFrom(COMMENTS)
.where(COMMENTS.ID.equal(commentId))
.execute();
}
```

你已经学习了如何使用 JOOQ DSL 执行各种操作，例如插入新记录、查询和删除记录。

现在，你将创建一个名为 `SpringbootJooqDemoApplication.java` 的入口类，如清单 7-8 所示。

```
@SpringBootApplication
public class SpringbootJooqDemoApplication
{
public static void main(String[] args) {
SpringApplication.run(SpringbootJooqDemoApplication.class, args);
}
}
清单 7-8.
com.apress.demo.SpringbootJooqDemoApplication.java
```

接下来，你将为 `PostService` 方法编写 JUnit 测试，如清单 7-9 所示。

```
@RunWith(SpringRunner.class)
@SpringBootTest
public class SpringbootJooqDemoApplicationTests
{
@Autowired
private PostService postService;
@Test
public void findAllPosts()  {
List posts = postService.getAllPosts();
assertNotNull(posts);
assertTrue(!posts.isEmpty());
for (Post post : posts)
{
System.err.println(post);
}
}
@Test
public void findPostById()  {
Post post = postService.getPost(1);
assertNotNull(post);
System.out.println(post);
List comments = post.getComments();
System.out.println(comments);
}
@Test
public void createPost() {
Post post = new Post(0, "My new Post",
"This is my new test post",
new Timestamp(System.currentTimeMillis()));
Post savedPost = postService.createPost(post);
Post newPost = postService.getPost(savedPost.getId());
assertEquals("My new Post", newPost.getTitle());
assertEquals("This is my new test post", newPost.getContent());
}
@Test
public void createComment() {
Integer postId = 1;
Comment comment = new Comment(0, postId, "User4",
"user4@gmail.com", "This is my new comment on post1",
new Timestamp(System.currentTimeMillis()));
Comment savedComment = postService.createComment(comment);
Post post = postService.getPost(postId);
List comments = post.getComments();
assertNotNull(comments);
for (Comment comm : comments)
{
if(savedComment.getId() == comm.getId()){
assertEquals("User4", comm.getName());
assertEquals("user4@gmail.com", comm.getEmail());
assertEquals("This is my new comment on post1", comm.getContent());
}
}
}
}
清单 7-9.
SpringbootJooqDemoApplicationTests.java
```

你已经创建了用于测试 `PostService` 方法的 JUnit 测试类。你正在执行各种操作，并根据使用 `data.sql` 插入的相同数据对响应进行断言。

假设你已经使用 `H2` 配置文件生成了代码，则无需任何额外配置即可运行 JUnit 测试。但如果你使用 `mysql` 配置文件生成了代码，则需要在 `application.properties` 中配置以下属性：

```
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
spring.datasource.url=jdbc:mysql://localhost:3306/test
spring.datasource.username=root
spring.datasource.password=admin
spring.jooq.sql-dialect=MYSQL
```

注意

你应该为数据库使用正确的 SQL 方言；否则，运行时可能会出现 SQL 语法错误。

有关 JOOQ 的更多信息，请访问 [`http://www.jooq.org/learn/`](http://www.jooq.org/learn/) 。

## 总结

本章解释了如何通过使用 Spring Boot JOOQ Starter 来使用 JOOQ 框架。下一章将解释如何在 Spring Boot 应用程序中使用 JPA（通过 Hibernate 实现）。



