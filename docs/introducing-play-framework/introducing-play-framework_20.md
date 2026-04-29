# db.default.password=""

© Prem Kumar Karunakaran 2020

P. K. Karunakaran, *深入理解 Play 框架*,

`doi.org/10.1007/978-1-4842-5645-9_7`

第 7 章 访问数据库

你可以通过配置数据库属性并取消配置设置的注释来开始使用默认数据源（default）。

以下是配置 MySQL 数据库作为默认数据源的设置：

db.default.driver=com.mysql.jdbc.Driver

db.default.url="jdbc:mysql://IP 地址:端口/books"

db.default.user=用户名

db.default.password="密码"

db.default.logStatements=true

请确保应用程序类路径中包含相应的 MySQL 连接器 jar 包（mysql-connector-java-5.1.24-bin.jar）。一个简单的方法是在项目中创建一个名为`lib`的文件夹，并将连接器 jar 包复制到其中。Play 会将`lib`文件夹内的任何 jar 文件添加到类路径中。

你可以使用默认数据源，也可以创建任意数量的自定义数据源。要创建另一个数据源，请复制为默认数据源定义的属性，并将名称`default`更改为你选择的名称。例如：

db.book.driver=com.mysql.jdbc.Driver

db.book.url="jdbc:mysql://localhost:3306/book"

db.book.user=用户名

db.book.password="密码"

db.book.logStatements=true

该数据源通过名称`book`引用，因此通过提供此名称可以从连接池中获取连接。

直接调用 JDBC 调用成本高昂，因为它们是阻塞操作并会导致线程等待。因此，最佳实践是通过提供`CustomExecutionContext`在单独的执行上下文中运行 JDBC 调用。以下是在 Play 应用程序中执行 JDBC 调用的示例：

第 7 章 访问数据库

package dao;

import akka.actor.ActorSystem;

import play.db.Database;

import play.libs.concurrent.CustomExecutionContext;

import javax.inject.Inject;

import javax.inject.Singleton;

import java.util.concurrent.CompletableFuture;

import java.util.concurrent.CompletionStage;

public class DatabaseExecutionContext extends

CustomExecutionContext {

@javax.inject.Inject

public DatabaseExecutionContext(ActorSystem actorSystem) {

// 使用在 application.conf 中定义的自定义线程池

super(actorSystem, "database.dispatcher");

}

@Singleton

public static class JdbcExample {

private Database db;

private DatabaseExecutionContext executionContext;

@Inject

public JdbcExample(Database db,

DatabaseExecutionContext context) {

this.db = db;

this.executionContext = executionContext;

}

public CompletionStage<Integer> updateSomething() {

return CompletableFuture.supplyAsync(

() -> {

第 7 章 访问数据库

return db.withConnection(

connection -> {

// 使用连接执行操作

return 1;

});

},executionContext);

}

}

}

你可以看到这段代码将`ActorSystem`作为构造函数参数。`ActorSystem`是 Akka 框架的一部分，Play 依赖 Akka 来实现执行上下文（上面使用的`play.libs.concurrent.CustomExecutionContext`类的一部分）。本书提供了关于 Akka 的章节，如果你是 Akka 新手，我建议你阅读一下。

通常，你不直接处理与连接相关的活动。

最佳实践是将数据库活动委托给 ORM（对象关系映射）层。建议使用 ORM 进行数据库访问，因为它使程序免受将对象持久化到底层数据存储以及从中获取对象的复杂性的影响。

ORM 通过使程序员能够专注于业务逻辑，而不是编写数据库访问和相关连接管理的代码，为代码带来了丰富的面向对象风格。ORM 层应能在给定情况下生成高效的 SQL 语句，并应使代码免受数据存储或其结构变化的影响。

ORM 层还可以提供某种缓存，以便仅在绝对必要时才进行实际的数据库交互。考虑到上述要求，Play 自带的 ORM——Ebean 是一个极好的选择。你将在以下各节中探索 Ebean ORM。

第 7 章 访问数据库

**使用 ORM 工作**

Ebean 是一个轻量级的开源 ORM，拥有对开发者友好的 API。Play 支持与 JPA 集成，并且可以轻松配置为使用 Hibernate、iBatis 或任何其他流行的 ORM。让我们看看如何将 Hibernate 配置为 Play 2 的 JPA 提供程序，然后详细探索 Ebean。

为了理解 Ebean 和任何其他 ORM，你必须充分了解基本的 ORM 概念。下一节将快速介绍 ORM。如果你已经熟悉 ORM 概念，可以跳过本节。

**ORM 概念**

在 ORM 出现之前，唯一可用的选项是 JDBC。JDBC API 允许从 Java 执行各种数据库操作。它支持更新、选择、数据库和表级别的元数据查询等等。渐渐地，Java 社区意识到了这种方法的困难。这些困难包括：

*   对象模型与关系数据库模型表示不匹配。RDMS 以表格格式表示数据，而 Java 等面向对象语言将其表示为相互连接的对象图。
*   状态管理：同步内存中对象模型与关系数据库的状态。
*   使用纯面向对象领域模型进行建模的困难，因为没有优雅的方式将模型与关系数据库同步。
*   重复且冗余的 SQL 代码。
*   低效的数据遍历。
*   将代码与特定的数据库和 SQL 语法绑定。
*   低层 SQL 代码嵌入到业务逻辑中。
*   难以缓存数据。

ORM 框架试图解决这些不匹配问题，并充当弥合这些差距的中间人。ORM 所需的最重要特性是前三点；其余的是附加优势。

*   数据库模型与对象模型之间没有不匹配。ORM 解决了这种不匹配。
*   ORM 通过同步内存中对象模型与关系数据库的状态来帮助进行状态管理。
*   ORM 通过使用纯面向对象领域模型并将模型与数据库同步来帮助进行建模。

根据我多年来构建许多系统的经验，如果你正在设计一个使用面向对象领域模型且数据存储是关系型的项目，那么 ORM 是唯一更好的选择。

如果你不特别关注面向对象设计和领域模型，则无需使用 ORM。你可以选择任何其他有助于从 Java 将数据持久化到数据库的技术。

如果你总是考虑表和行，而不考虑领域对象，那么不要浪费时间在 ORM 上。最好使用其他可以帮助从 Java 将数据持久化到 RDMS 的技术。

但是，如果你不使用 ORM，你将错过很多，并且最终会编写大量样板代码。因此，即使是较小的项目，也请继续使用 ORM 并适当地建模你的业务对象。没有理由不使用 ORM，因为几乎所有 ORM 实现都已成熟，并且可以在数据访问和更新方面提供更广泛的选择。

第 7 章 访问数据库

**关键术语**

面向对象领域模型中实体之间可能的关系包括：

*   一对一
*   一对多
*   多对一
*   多对多

**一对多**

订单（Order）与订单项（LineItems）之间的关系。一个订单可能包含多个订单项。所以这是一对多的一个例子。如果`LineItem`没有引用`Order`的属性，则它是单向关联。

**多对一**

从`LineItem`到`Order`的关系是多对一的一个例子。

**多对多**

这可以通过学生和课程之间的关联轻松解释。一个学生可以注册多门课程，一门课程可以由多个学生参加。因此，这是多对多的一个例子。

**关系方向**

这些关系可以是单向的或双向的。单向意味着你只能从父级访问子级。双向意味着可以从两端遍历父级和子级。也就是说，每个实体都有一个引用另一个实体的关系字段。

第 7 章 访问数据库

双向关系有一个拥有方（owning side）和一个反向方（inverse side），而单向关系只有拥有方。

**关系的拥有方决定了对数据库中关系的更新。** 更容易理解的方法是，在双向关系的情况下，包含表外键的实体是拥有者，并负责维护关系。

例如，`Order`包含指向订单项的外键，因此它是关系的拥有者。

双向关系应遵循以下规则：

*   双向关系的反向方必须通过使用`@OneToOne`、`@OneToMany`或`@ManyToMany`注解的`mappedBy`元素来引用其拥有方。`mappedBy`元素指定了关系中作为拥有者的实体中的属性或字段。
*   多对一双向关系的多方不得定义`mappedBy`元素。多方始终是关系的拥有方。
*   对于一对一的双向关系，拥有方对应于包含相应外键的一方。
*   对于多对多的双向关系，任何一方都可以是拥有方。

包含外键的实体是拥有者，子实体是反向方。子实体应包含`mappedBy`注解。当实体通过关系链接时，指定适当的级联设置也很重要。例如，订单项是订单的一部分；如果订单被删除，订单项也应该被删除。

这称为级联删除关系。我希望你现在已经快速掌握了使用 ORM 的知识。

第 7 章 访问数据库

**配置 JPA**

Java 持久化 API（JPA）要求数据源应可通过 JNDI 访问。你需要做的第一件事是通过更改`conf/application.conf`文件中的数据源配置，使数据源可通过 JNDI 访问。通过在配置中指定 JNDI 名称，可以使数据源通过 JNDI 可用。

你将使用 h2 内存数据库作为示例。目标是保存用户对一本书的评论，并使用 JPA 将其持久化。

打开`application.conf`文件并添加以下条目：



