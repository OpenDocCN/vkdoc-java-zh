# 默认数据库配置

db.default.driver=org.h2.Driver

db.default.url="jdbc:h2:mem:play"

db.default.jndiName=DefaultDS

jpa.default=defaultPersistenceUnit

下一步是选择一个 JPA 实现，并将其作为项目的依赖项添加。本示例使用 Hibernate 作为 JPA 提供程序，并使用内存中的 h2 数据库。打开 build.sbt 文件并添加

val appDependencies = Seq(

javaJdbc,

javaJpa,

"org.hibernate" % "hibernate-entitymanager" % "5.3.7.Final",

"com.h2database" % "h2" % "1.4.200"

)

build.sbt 位于项目目录内。

第 7 章 访问数据库

下一步是创建 persistence.xml 文件并将其配置为使用 Hibernate 作为 JPA 提供程序。persistence.xml 应放置在 conf/META-INF 目录中。

<persistence

xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/persistence http://xmlns.jcp.org/xml/ns/persistence/persistence_2_1.xsd"

version="2.1">

<persistence-unit name="defaultPersistenceUnit" transaction-type="RESOURCE_LOCAL">

<provider>org.hibernate.jpa.HibernatePersistenceProvider</

provider>

<non-jta-data-source>DefaultDS</non-jta-data-source>

<properties>

<property name="hibernate.dialect" value="org.hibernate.

dialect.H2Dialect"/>

</properties>

</persistence-unit>

</persistence>

**提示** 最佳实践是将所有 JPA 操作隔离在默认 Play 执行上下文之外的单独执行上下文中。所有数据库操作都是阻塞的，因此将它们隔离在单独的执行上下文中非常重要。

Play 提供了 play.db.jpa.JPAApi 来与 Entity Manager 一起使用。如果您不熟悉 Entity Manager，请阅读下一段。

第 7 章 访问数据库

Entity Manager 是 JPA 的一部分，用于与持久化上下文交互以维护会话状态。简而言之，它是我们在使用 JPA 时用于创建、删除、更新或查找实体的接口。它与 Hibernate 中的 session 概念密切相关。

如果您想了解有关 JPA 最新内容的更多信息，可以下载规范，地址为 [`download.oracle.com/otn-pub/`](https://download.oracle.com/otn-pub/jcp/persistence-2_1-fr-eval-spec/JavaPersistence.pdf)

[jcp/persistence-2_1-fr-eval-spec/JavaPersistence.pdf](https://download.oracle.com/otn-pub/jcp/persistence-2_1-fr-eval-spec/JavaPersistence.pdf)。

您的模型类不应直接执行 JPA 或 JDBC 操作。

让我们继续创建存储库类。在此之前，您需要创建一个名为 Review 的实体，该实体模拟用户对书籍的真实世界评论。

**package model;**

import javax.persistence.*;

public class Review {

@Id

@GeneratedValue(strategy = GenerationType.AUTO)

public Long id;

public String comment;

public Long getId() {

return id;

}

public void setId(Long id) {

this.id = id;

}

public String getComment() {

return comment;

}

public void setComment(String comment) {

this.comment = comment;

}

第 7 章 访问数据库

public String getUserId() {

return userId;

}

public void setUserId(String userId) {

this.userId = userId;

}

public String getBookId() {

return bookId;

}

public void setBookId(String bookId) {

this.bookId = bookId;

}

public String userId;

public String bookId;

}

接下来，创建一个用于抽象评论操作的接口 (ReviewRepository.java)：

:package dao;

import com.google.inject.ImplementedBy;

import model.Review;

import java.util.concurrent.CompletionStage;

import java.util.stream.Stream;

@ImplementedBy(JPAReviewRepository.class)

public interface ReviewRepository {

CompletionStage<String> saveReview(Review review);

}

ReviewRepository 接口向模型类隐藏了 JPA 实现细节。

第 7 章 访问数据库

继续创建此接口的实现 (JPAReviewRepository.java)：

**package** dao;

**import** models.Review;

**import** play.db.jpa.JPAApi;

**import** javax.inject.Inject;

**import** javax.inject.Singleton;

**import** java.util.concurrent.CompletableFuture;

**import** java.util.concurrent.CompletionStage;

**import static** java.util.concurrent.CompletableFuture. *supplyAsync*;

@Singleton

**public class** JPAReviewRepository **implements** ReviewRepository{

**private** JPAApi **jpaApi**;

**private** DatabaseExecutionContext **executionContext**;

@Inject

**public** JPAReviewRepository(JPAApi api,

DatabaseExecutionContext executionContext) {

**this**. **jpaApi** = api;

**this**. **executionContext** = executionContext;

}

@Override

**public** CompletionStage<String> saveReview(Review review) {

**return** CompletableFuture. *supplyAsync*(

() -> {

*// lambda 是*

*//Function<EntityManager, Long> 的一个实例*

**return jpaApi**.withTransaction(

entityManager -> {

第 7 章 访问数据库

entityManager.persist(review);

**return "saved"** ;

});

},

**executionContext**);

}

}

JPA 调用应在事务内进行，因此您使用 jpaApi.

withTransaction 方法来执行保存 Review 实体的持久化操作。

**在 Play 中使用 Ebean**

我在 Play 中使用 Ebean 的体验相当满意。它提供了易于使用的 API，并且在架构方面简单得多。

使用 EntityManager 的架构带来了附加和分离实体的概念。仅当实体与 EntityManager 关联时，其实体状态才会被持久化。这也意味着，如果需要在多个事务之间维护会话，则 EntityManager 需要由另一个对象（如 EJB Session bean）管理。同一个 Entity Manager 实例跨事务使用以维护单个会话。Ebean 通过遵循 EntityManager 架构极大地简化了这一点。这意味着 Ebean 不提供 Entity Manager，也不提供 JPA 中常见的 *merge*、*flush* 或 *persist* 操作。

这里要理解的一点是，Ebean 使用了与 JPA 完全不同的架构。请注意，即使 Ebean 没有 Entity Manager，它确实有一个“持久化上下文”，该上下文是事务范围的，并且会自动管理。

第 7 章 访问数据库

我同时使用过 JPA 和 Ebean，根据我的经验，两者各有优点。毫无疑问，Ebean 使用的查询语言更易于理解和使用。这种简单性是 Ebean 的卖点。

Ebean 采用的另一个重要优化是它对部分对象的支持。在 JPA 中，您通常会注释一个关系，以指示是否需要使用急切或延迟获取策略来加载该关系。在急切获取中，当获取父对象时，子对象也会被检索和加载。在延迟加载策略中，仅加载父对象，子对象仅在使用时才被获取。配置这些获取策略的能力非常重要，并且是优化代码的一个重要因素。JPA 提供了一个注解来在实体级别配置它们。但有一个问题。

考虑一个场景，对于用例 1，您希望实体采用急切获取策略，而对于用例 2，您希望同一个实体使用延迟加载策略。这是一个问题，因为这些注解是在实体级别声明的。这非常不灵活。大多数 ORM 通过“获取组”提供类似于部分对象的功能。

在查询语言中支持“部分对象”意味着您可以编写针对不同用例优化的查询。也就是说，依赖对象的延迟或急切加载不是固定的，而是可以根据用例进行更改。这是 Ebean 采用的一个很好的特性，并且极大地有助于优化查询的性能和效率。此功能在早期的 JPA 规范中存在。

在某些用例中，您可能会发现从性能角度来看，编写自定义 SQL 会更好、更容易。Ebean 对自定义 SQL 查询有出色的支持，并提供了更简单的 API 来使用。

第 7 章 访问数据库

要启用 Ebean，请将插件依赖项添加到 project/plugins.

sbt 文件：

addSbtPlugin("com.typesafe.sbt" % "sbt-play-ebean" % "5.0.2") 然后修改 build.sbt 文件以添加 Ebean 依赖项： lazy val root = (project in file("."))

.enablePlugins(PlayJava, PlayEbean)

**Ebean 查询**

Ebean 使用与 JPA 规范相同的映射。因此，您可以根据 JPA 规范使用 @Entity、@Table、@Column、@OneToMany 等来注释您的 bean。事实上，大多数 JPA 注解都可以与 Ebean 一起使用。

让我们考虑一个将书籍建模为实体的简单案例。一本书通过其 ISBN 唯一标识（在本例中，您将 ISBN 表示为 asin，一种亚马逊表示法）。ASIN 表示一本书的唯一标识符，没有两本书可以具有相同的 ASIN。

一个 Ebean 模型应该继承自 io.ebean.Model：

package models;

import java.util.*;

import javax.persistence.*;

import io.ebean.*;

import play.data.format.*;

import play.data.validation.*;

@Entity

@Table(name="book")

public class Book extends Model {

@Id

@Column(name="asin")

第 7 章 访问数据库

private String asin;

@Column(name="title")

private String title;

@Column(name="author")

private String author;

@Column(name="description")

private String description;

@Column(name="language")

private String language;

public String getLanguage() {

return language;

}

public void setLanguage(String language) {

this.language = language;

}

public String getDescription() {

return description;

}

public void setDescription(String description) {

this.description = description;

}

public String getAsin() {

return asin;

}

public void setAsin(String asin) {

this.asin = asin;

}

public String getTitle() {

return title;

}

public void setTitle(String title) {

this.title = title;

}

第 7 章 访问数据库

public String getAuthor() {

return author;

}

public void setAuthor(String author) {

this.author = author;

}

@Override

public int hashCode() {

final int prime = 31;

int result = super.hashCode();

result = prime * result + ((asin == null) ? 0 : asin.

hashCode());

return result;

}

@Override

public boolean equals(Object obj) {

if (this == obj)

return true;

if (!super.equals(obj))

return false;

if (getClass() != obj.getClass())

return false;

Book other = (Book) obj;

if (asin == null) {

if (other.asin != null)

return false;

} else if (!asin.equals(other.asin))

return false;

return true;

}

}

第 7 章 访问数据库

**注意** play 会在运行时自动生成 getter 和 setter 方法，因此除非您希望在编译时使用它们，否则无需生成它们。

上述代码中的注解包含大量信息。最重要的部分是

• @Entity 通知 ORM (Ebean) 此类与底层数据存储中存储的数据相关联，并要求 ORM 处理它。

• @Table 通知 ORM 提供程序此实体将持久化到注解中提到的表中，在您的例子中，该表是 book。

• @Id 是非常重要的信息，它声明此字段是引用此实体的唯一标识符。这通常意味着此字段是底层数据库中的主键。

• @Column 将属性映射到底层表的特定列。

如何创建新书并持久化到数据库：

Book book= new Book();

//设置属性

book.setId("A0091234");

book.setTitle("Play 2");

book.setAuthor("Prem");

book.setDescription("Play 2");

book.setLanguage("English");

book.save();

第 7 章 访问数据库

这里您使用 Ebean 类的 save 方法来持久化 book 对象。

如何通过 ID 获取一本书：

// 通过 id 查找一本书

Book book = Ebean.find(Book.class).where().idEq("id").

findOne();

如何获取所有标题以 Java 开头的书籍：

// 查找标题以 "java" 开头的书籍列表

List<Book> books =Ebean.find(Book.class).where().

like("title","java%").orderBy("tile desc").findList(); 让我们详细探讨带有 where 和 like 子句的 find 查询。上面的示例使用了流畅的 API 风格，其中方法被链式调用。这种方法链式调用是良好函数式编程风格和 DSL 的特征之一。

您可以选择使用 select() 和 fetch() 来仅指定您想要获取的属性。这将返回“部分”填充的 bean。

这是一个有助于提高查询性能的重要特性。例如，如果您只对两列感兴趣，比如 id 和 name，并且它们已经被数据库索引，那么数据库不需要触及数据块；它可以直接从索引本身提供值。如果表有大量记录，这将是一个巨大的性能改进。

List<Book> books = Ebean. *find*(Book. **class**).where("asin,author").

findList();

在此示例中，您仅检索 asin 和 author 属性。

第 7 章 访问数据库

**Ebean 中常用的 Select 查询结构**

现在让我们探索 Ebean 中最常用的方法，这些方法有助于构建 Select 查询。

**where( )**

where 子句用于指定 where 过滤条件。您可以将完整的 where 子句作为字符串传递（where(" filter condition ")）或与 Expression 对象一起使用。从编程标准的角度来看，将 where() 与表达式列表一起使用更好。上面关于“获取标题以 Java 开头的书籍”的示例展示了与 Expression 对象一起使用的 where 子句。

**eq( )**

检查给定属性是否等于提供的值： eq(String propertyName, Object value)

获取所有由 Robin Cook 撰写的书籍：

List<Book> booksList = Ebean. *find*(Book. **class**).where().

eq("author", "Robin Cook").findList();

**like( )**

指定一个属性类似的值，其中值包含 SQL 通配符 %（百分号）和 _（下划线）：

like(String propertyName,String value)

List<Book> booksList = Ebean. *find*(Book. **class**).where().

like("author", "%Cook").findList();

**orderBy( )**

设置 order by 子句，如果存在则替换现有的 order by 子句： orderBy(String orderByClause)

第 7 章 访问数据库

这遵循 SQL 语法，在每个属性之间使用逗号，并可选的 asc 和 desc 关键字分别表示升序和降序：

List<Book> booksList = Ebean. *find*(Book. **class**).where().

eq("author", "Robin Cook")

.orderBy("title desc").findList();

**findUnique( )**

用于仅返回一条记录。执行查询，返回单个 bean 或 null（如果未找到匹配的 bean）。

**findList( )**

执行查询，返回对象列表。

**LIMIT {max rows} [ OFFSET {first row} ]**

用于限制返回的记录数。通过将其与 offset 参数一起使用，可以轻松实现分页。

**查询接口**

Ebean Query 接口中有很多方法。请查看 com.avaje.ebean.Query 接口以探索可用的选项。以下是其中一些方法的列表：

• Query<T> fetch(String path)

指定要加载的路径，包括其所有属性

• Query<T> fetch(String path, FetchConfig joinConfig) 另外指定一个 JoinConfig 来指定“查询连接”和/或定义延迟加载查询

第 7 章 访问数据库

• Query<T> fetch(String path, String fetchProperties) 指定要获取的路径及其要包含的特定属性（即部分对象）

• Query<T> fetch(String assocProperty, String

fetchProperties, FetchConfig fetchConfig)

另外指定一个 FetchConfig 以使用单独的查询或延迟加载来加载此路径

• **List<Object>**

**findIds()**

执行查询，返回 ID 列表

• **List<T>**

**findList()**

执行查询，返回对象列表

• **Map<?,T>** **findMap()**

执行查询，返回对象的映射

• **int** **findRowCount()**

返回此查询应返回的实体计数

• **Set<T>**

**findSet()**

执行查询，返回对象集合

• **intger FirstRow()**

返回第一行的值

• **String**

**getGeneratedSql()**

返回为执行此查询而生成的 SQL

• **intger MaxRows()**

返回此查询的最大行数

• RawSql

getRawSql()

返回为此查询设置的 RawSql

第 7 章 访问数据库

• Query<T> **select(String fetchProperties)**

 **显式设置要在“主”实体 bean 上获取的属性的逗号分隔列表（即部分对象）

• Query<T> setReadOnly(boolean readOnly)

当您希望返回的 bean 为只读时，设置为 true。

• Query<T> setTimeout(int secs)

为此查询设置超时

**使用 RawSql**

您已经看到，需要有一个实体才能使典型的 Ebean 查询工作。查询针对实体，然后该查询被转换为数据库查询。在许多情况下，您可能希望从多个实体获取结果，并且返回的数据无法转换为实体。这对于报告类型的需求非常有用，您希望使用聚合函数，例如 sum()、count()、max() 等。无论出于何种原因，当您想要编写特定于数据库的查询时，可以使用 RawSql。得益于 Ebean 的 @Sql 注解，查询返回的数据可以轻松映射到对象。您可以创建一个实体并使用 @Sql 注解标记它，以告诉 Ebean 这只是一个 SQL 表示，而不是与表相关的实体。

让我们看一个例子。假设您想查找所有作者撰写的书籍总数。您使用 RawSqlBuilder 类来指定查询，并将 select 子句中的列映射到 PopularBook 类中定义的相应属性字段。此类是您为映射结果而定义的实体：

String sql="select author,count(author) as noofbooks from books group by author"

RawSqlBuilder rawSqlBldr = RawSqlBuilder. *parse*(sql); RawSql rawSql = rawSqlBldr.columnMapping("author", "bookauthor")

.columnMapping("noofbooks", "totalbooksbyauthor").create(); 128

第 7 章 访问数据库

Query<PopularBook> query = Ebean. *find*(PopularBook. **class**); query.setRawSql(rawSql);

List<PopularBook> list = query.findList();

以下是 PopularBook 实体：

package models;

import javax.persistence.Entity;

import com.avaje.ebean.annotation.Sql;

@Entity

@Sql

public class PopularBook extends TrendingBook {

private String author;

private int totalbooksbyauthor;

//定义 getter 和 setter

}

有时，您可能希望仅使用关系功能，而不希望使用任何 ORM 功能。对于这种情况，Ebean 提供了 SqlQuery 类。在这里，您可以编写普通的旧 SQL 并像通过 JDBC 结果集一样获取结果。SqlQuery 是您指定确切 SQL SELECT 语句并返回 SqlRow 对象的列表、集合或映射的地方。SqlRow 是一个 Map，其中键是列名。

这是一个相当轻量级的 API，您可以使用它来代替使用原始 JDBC。同样，让我们看一个例子来理解这一点：

String sql = "select count(1) as count from book where author = ?"; SqlQuery query = Ebean. *createSqlQuery*(sql);

query.setParameter(1, author);

SqlRow row = query.findUnique();

**int** count = row.getInteger("count");

这比使用 JDBC API 编写普通的旧 JDBC 查询要容易得多。

第 7 章 访问数据库

如果情况需要**为插入或更新编写原始 SQL**，您可以使用 SqlUpdate 类：

SqlUpdate query = Ebean. *createSqlUpdate*(sql);

Ebean 还提供了 CallableSql 类来调用数据库过程和函数。

**Ebean 中的关系**

一个一对多的例子：

@Entity

@Table(name="book")

public class Book implements Serializable {

// 单向 ...

// ... 如果需要，可以显式指定连接列

@OneToMany

@JoinColumn(name="auth_id")

List<Author> authors;

考虑上述情况，您可以从 Book 实体找到作者，但无法从 Author 实体遍历回 Book。

这里外键在 book 表中，您可以使用 @JoinColumn 来指定需要用于键的表列。如果将其建模为双向关系，则代码如下：

@Entity

@Table(name="author")

public class Author implements Serializable {

@OneToMany(mappedBy="authorId")

List<Book> writtenBooks;

请注意，在此双向模型中，Book 实体拥有外键，并且是关系的所有者。因此，您标记了反向侧：带有 mappedBy 属性的 Author。

第 7 章 访问数据库

如果您检查 RDBMS，您可以很容易地理解 RDBMS 表中不存在多对多关系。多对多关系在数据库表中通过交集表强制实现为两个一对多关系。如果您预计交集表可能需要支持更多属性，最好将其建模为单独的实体，并在您的模型中包含两个一对多关系。如果交集表中没有机会出现任何额外属性，您可以直接使用默认的 @ManyToMany 注解。这是一个例子。

考虑用户和角色的情况。一个用户可以拥有多个角色，一个角色可能属于多个用户。在 RDBMS 中，这是使用交集表实现的。让我们将交集表命名为 user_role。让我们假设简单的情况，交集表不包含任何其他属性，在这种情况下，实体将具有 ManyToMany 关系，如代码所示：

@Entity

@Table(name="user")

public class User implements Serializable {

...

@ManyToMany(cascade=CascadeType.ALL)

List<Role> roles;

@Entity

@Table(name="role")

public class Role {

...

@ManyToMany(cascade=CascadeType.ALL)

List<User> users;

关于 Ebean 还有很多东西需要解释，因为它非常庞大，可能需要一本全新的书来完整解释。我建议您阅读官方的 Ebean 在线文档以了解更多信息。文档可在 [`ebean.io/docs/`](https://ebean.io/docs/) 获取。

**第 8 章**

**完整示例**

本章将您到目前为止学到的内容整合到一个完整的示例中。本章的主要重点是展示代码；我们已经在之前的章节中讨论了所有概念和部分代码。

**conf/application.conf**

*# 这是应用程序的主配置文件。*

*# https://www.playframework.com/documentation/latest/ConfigFile*

*# 默认数据库配置*

play.modules.enabled += "modules.FirstModule"

play.http.filters=config.FilterConfig

play.http.errorHandler = "config.CustomErrorHandler"

db {

default.driver = org.h2.Driver

default.url = "jdbc:h2:mem:play"

*# 为 JPA 访问提供*

default.jndiName=DefaultDS

}

*# 将 JPA 指向我们的数据库配置*

jpa.default=defaultPersistenceUnit

© Prem Kumar Karunakaran 2020

P. K. Karunakaran, *Introducing Play Framework*,

`doi.org/10.1007/978-1-4842-5645-9_8`

第 8 章 完整示例

*# 数据库连接数*

*# 参见 https://github.com/brettwooldridge/HikariCP/wiki/About-Pool- Sizing*

*# 数据库连接数 = ((物理核心数 * 2) + 有效主轴数)*

fixedConnectionPool = 9

*# 将 Hikari 设置为固定大小*

play.db {

prototype {

hikaricp.minimumIdle = ${fixedConnectionPool}

hikaricp.maximumPoolSize = ${fixedConnectionPool}

}

}

*# 作业队列大小设置为 HikariCP 连接池大小*

database.dispatcher {

executor = "thread-pool-executor"

throughput = 1

thread-pool-executor {

fixed-pool-size = ${fixedConnectionPool}

}

}

这里您配置了一个连接池和一个用于数据库连接的固定线程池。这非常重要，因为您正在一个单独的上下文中运行数据库调用，该上下文拥有自己的线程池。这将确保您不会阻塞默认执行上下文的线程。

然后，您将 h2 内存数据库配置为您的存储，并使用 JNDI 名称 DefaultDS 注册它。

**conf/routes**



