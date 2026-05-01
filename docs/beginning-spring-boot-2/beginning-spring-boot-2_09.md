# 9. 使用 MongoDB

MongoDB 是最流行的面向文档的 NoSQL 数据库之一。Spring Data MongoDB 提供了对 MongoDB 的支持，其编程模型与 Spring Data JPA 类似，都基于一致的 Spring 框架。Spring Boot 为 Spring Data Mongo 提供了一个启动器，通过实现其自动配置机制，使其更易于使用。

本章将讨论 MongoDB，包括如何在 Windows、MacOS 和 Linux 等各种平台上安装 MongoDB 服务器。本章将解释如何从 Mongo Shell 执行各种数据库操作。然后，你将探索如何通过使用 Spring Boot 的 `spring-boot-starter-data-mongodb` 启动器来使用 Spring Data Mongo 的功能。

## 认识 MongoDB

MongoDB 是一个开源的面向文档的 NoSQL 数据库。MongoDB 使用一种名为 BSON 的文档存储和数据交换格式，它提供了类 JSON 文档的二进制表示形式。MongoDB 将文档存储在集合中。集合类似于关系数据库中的表。然而，与表不同的是，集合不要求其文档具有相同的模式。参见清单 9-1。

```
{
"_id" : ObjectId("13f345492b7c8eb21818bd09"),
"title" : "在 SpringBoot 应用中使用 MongoDB",
"url": "http://localhost:8080/blog/working-with-mongodb",
"created_on" : ISODate("2016-10-01T00:00:00Z"),
"tags" : ["NoSQL","MongoDB","SpringBoot"],
"content" : "此处为长篇博客文章内容",
"comments": [
{
"name" : "John",
"email": "john@gmail.com",
"message": "很好的教程",
"created_on" : ISODate("2016-10-01T00:00:00Z"),
},
{
"name" : "Remo",
"email": "remo@gmail.com",
"message": "感谢这个教程",
"created_on" : ISODate("2016-10-04T00:00:00Z"),
}
]
}
清单 9-1.
MongoDB 集合中的示例博客文章文档
```

## 安装 MongoDB

MongoDB 支持多种平台，包括 Windows、Linux 和 MacOS 操作系统。要获取完整的支持平台列表，请参见 [`https://docs.mongodb.com/manual/installation/#supported-platforms`](https://docs.mongodb.com/manual/installation/#supported-platforms) 。

注意

本书使用 MongoDB Community Server。

### 在 Windows 上安装 MongoDB

从 [`https://www.mongodb.com/download-center`](https://www.mongodb.com/download-center) 下载最新版本的 MongoDB。运行 MSI 安装程序并选择一个安装目录，例如 `C:\Apps`。在这种情况下，MongoDB 服务器将安装在 `C:\Apps\MongoDB\Server\3.4`。

MongoDB 需要一个数据目录来存储其文件。在 Windows 上，MongoDB 数据文件夹的默认位置是 `C:\data\db`。你可以创建数据目录结构 `C:\data\db`，或者将数据目录的自定义位置作为参数传递，如下所示：

```
C:\Apps\MongoDB\Server\3.4\bin>mongod.exe --dbpath " C:\Apps\MongoDB\Server\3.4\data"
```

运行此命令后，MongoDB 应在默认端口 `27017` 上启动。与其每次都运行此命令并传递所有配置选项，不如创建一个配置文件和一个批处理脚本来启动 MongoDB。

例如，你可以在 `C:\Apps\MongoDB\Server\3.4` 目录中创建 `mongod.cfg`，其内容如清单 9-2 所示。

```
systemLog:
destination: file
path: C:/Apps/MongoDB/Server/3.4/logs/mongo.log
storage:
dbPath: C:/Apps/MongoDB/Server/3.4/data/db
net:
bindIp: 127.0.0.1
port: 27017
清单 9-2.
名为 mongod.cfg 的 MongoDB 服务器配置文件
```

在 `C:\Apps\MongoDB\Server\3.4` 目录中创建 `start-mongo.bat`，内容如下：

```
bin\mongod.exe --config C:\Apps\MongoDB\Server\3.4\mongod.cfg
```

现在，你可以简单地按如下方式启动 MongoDB 服务器：

```
C:\Apps\MongoDB\Server\3.4>start-mongo.bat
```

注意

在启动 MongoDB 之前，请确保数据目录和日志目录已经创建。

你可以在 [`https://docs.mongodb.com/manual/reference/configuration-options/`](https://docs.mongodb.com/manual/reference/configuration-options/) 了解更多关于 MongoDB 服务器配置选项的信息。

### 在 MacOS 上安装 MongoDB

在 MacOS 上安装 MongoDB 服务器最简单的方法是使用 Homebrew。关于如何安装 Homebrew，请参见 [`https://brew.sh/`](https://brew.sh/) 。安装 Homebrew 后，你需要更新 Homebrew 的包数据库并安装 MongoDB 包，如下所示：

```
> brew update
> brew install mongodb
```

在 MacOS 上，默认数据目录是 `/data/db`。请确保数据目录具有读写权限。你可以在不指定 `dbpath` 的情况下启动 Mongo 服务器，这意味着它将使用默认数据目录，或者你也可以传递一个自定义的数据目录位置，如下所示：

```
> mongod --dbpath /Users/username/mongodb/data
```

有关在 MacOS 上安装 MongoDB 的其他选项，请参见 [`https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/`](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/) 。

### 在 Linux 上安装 MongoDB

MongoDB 为大多数流行的 Linux 发行版提供了软件包。关于特定 Linux 发行版的安装说明，请参考 [`https://docs.mongodb.com/manual/administration/install-on-linux/`](https://docs.mongodb.com/manual/administration/install-on-linux/) 。



## 使用 Mongo Shell 快速上手 MongoDB

MongoDB 服务器启动后，你可以通过启动 Mongo Shell 来连接 MongoDB 服务器。打开另一个命令提示符窗口，运行 `C:\Apps\MongoDB\Server\3.4\bin\mongo.exe`。如果连接服务器成功，它将打印 MongoDB Shell 版本和消息 `connecting to: test`。

```
C:\Apps\MongoDB\Server\3.4\bin> mongo.exe
MongoDB shell version: 3.2.1
connecting to: test
Welcome to the MongoDB shell.
For interactive help, type "help".
For more comprehensive documentation, see
http://docs.mongodb.org/
Questions? Try the support group
http://groups.google.com/group/mongodb-user
>
```

你可以使用 `show dbs` 命令来检查可用数据库的列表：

```
> show dbs
```

默认情况下，你连接的是名为 `test` 的默认数据库。你可以使用 `use db_name` 命令切换到另一个数据库。

```
> use users
switched to db users
```

你可以按如下方式向 `users` 集合中插入一个用户文档：

```
> db.users.insert({"username": "siva","password": "secret" })
```

如果该集合不存在，它会被自动创建。

你可以查询数据库以返回所有文档，如下所示：

```
> db.users.find()
```

你也可以对查询应用过滤器，如下所示：

```
> db.users.find({"name": "siva" })
```

你还可以获取文档的数量，如下所示：

```
> db.users.count()
```

注意

深入介绍 MongoDB 超出了本书的范围。请访问官方文档：[`https://docs.mongodb.com/manual/`](https://docs.mongodb.com/manual/) 以了解更多关于 MongoDB 的信息。

## 介绍 Spring Data MongoDB

Spring Data 项目系列提供了 Spring Data Mongo 模块，该模块支持执行 CRUD（创建、读取、更新、删除）操作以及基于方法名称的动态查询，这与 Spring Data JPA 类似。

Spring Data MongoDB 模块提供了 `MongoTemplate`，它在 MongoDB Java 驱动 API `com.mongodb.Mongo` 之上提供了更高层次的抽象。

如果你没有使用 Spring Boot，你需要使用 JavaConfig 配置 MongoDB 组件，如清单 9-3 所示。

```
@Configuration
public class MongoConfiguration
{
@Bean
public MongoDbFactory mongoDbFactory() throws Exception {
return new SimpleMongoDbFactory(new Mongo(), "database");
}
@Bean
public MongoTemplate mongoTemplate() throws Exception {
return new MongoTemplate(mongoDbFactory());
}
}
Listing 9-3.
使用 JavaConfig 配置 MongoDB 组件
```

Spring Boot 提供了 `spring-boot-starter-data-mongodb` 启动器，通过自动配置 MongoDB 组件来轻松使用 MongoDB，无需手动配置。

```
org.springframework.boot
spring-boot-starter-data-mongodb

```

通过添加 `spring-boot-starter-data-mongodb` 依赖，Spring Boot 将自动配置 `MongoClient`、`MongoDbFactory`、`MongoTemplate` 等，并连接到 `mongodb://localhost/test` 的本地 MongoDB 服务器。

你可以通过在 `application.properties` 文件中配置 `spring.data.mongodb.uri` 属性来自定义 `mongodb` 服务器 URL。

```
spring.data.mongodb.uri=mongodb://remoteserver:27017/blog
```

你也可以使用 `MongoTemplate` 来保存、更新和删除领域对象，这些操作内部负责将领域对象映射到 MongoDB 中的文档。

首先，按如下方式创建 `User` 类：

```
public class User
{
private String id;
private String name;
private String email;
private boolean disabled;
//setters and getters
}
```

然后，你可以注入 `MongoTemplate` 并对 MongoDB 执行各种操作，如清单 9-4 所示。

```
@Service
public class MongoUserService
{
@Autowired
private MongoTemplate mongoTemplate;
public List getUsers()
{
return mongoTemplate.findAll(User.class, "users");
}
public User getUser(String id)
{
Query query = Query.query(Criteria.where("id").is(id));
return mongoTemplate.findOne(query, User.class);
}
public void createUser(User user)
{
mongoTemplate.save(user, "users");
}
}
Listing 9-4.
使用 MongoTemplate 执行数据库操作
```

除了使用 `MongoTemplate`，你还可以创建扩展了 `MongoRepository` 接口的仓库，这些仓库开箱即用地提供 CRUD 操作。

你可以使用 `@Document` 注解将领域对象映射到特定的 MongoDB 集合名称。

```
@Document(collection="users")
public class User
{
@Id
private String id;
private String name;
private String email;
private boolean disabled;
//setters and getters
}
```

默认情况下，MongoDB 会生成一个名为 `_id` 的 `ObjectId` 主键。但是，你可以通过简单地使用 `@Id` 注解，将任何现有属性映射为主键。

通过扩展 `MongoRepository` 接口来创建 `UserRepository` 接口，如清单 9-5 所示。

```
public interface UserRepository extends MongoRepository
{
}
Listing 9-5.
UserRepository.java
```

现在，你可以按如下方式执行各种 CRUD 操作：

```
List users = userRepository.findAll();
Optional user1 = userRepository.findById("1");
User user2 = new User("2", "Robert", "robert@gmail.com");
User savedUser = userRepository.save(user2);
// 根据主键删除用户
userRepository.deleteById("1");
// 根据对象删除用户
userRepository.delete(user);
// 获取 users 集合中用户文档的总数
userRepository.count();
```

你也可以执行类似于 Spring Data JPA 仓库的排序和分页操作。

```
Sort sort = new Sort(Direction.ASC, "name");
List users = userRepository.findAll(sort);
```

你还可以使用 `@Query` 来表达查询条件，如下所示：

```
@Query("{ 'name' : ?0 }")
User findByUserName(String name);
```



## 使用嵌入式 Mongo 进行测试

使用嵌入式 MongoDB 进行测试会更加方便，尤其是在持续集成环境中。

你可以使用以下嵌入式 Mongo（[`https://github.com/flapdoodle-oss/de.flapdoodle.embed.mongo`](https://github.com/flapdoodle-oss/de.flapdoodle.embed.mongo)）依赖，Spring Boot 可以自动配置它，这样你无需搭建实际的 MongoDB 服务器即可运行测试。

```
de.flapdoodle.embed
de.flapdoodle.embed.mongo

```

你还可以通过实现 `CommandLineRunner` 接口来初始化示例数据，该接口会在应用程序启动时执行 `public void run(String… args)` 方法，如清单 9-6 所示。

```
@SpringBootApplication
public class SpringbootMongodbDemoApplication implements CommandLineRunner
{
@Autowired
private UserRepository userRepository;
public static void main(String[] args)
{
SpringApplication.run(SpringbootMongodbDemoApplication.class, args);
}
@Override
public void run(String... args) throws Exception {
userRepository.save(new User("1", "Robert","robert@gmail.com"));
userRepository.save(new User("2", "Dan","dan@gmail.com"));
}
}
清单 9-6.
SpringbootMongodbDemoApplication.java
```

创建 JUnit 测试来测试 `UserRepository` 上的基本 CRUD 操作，如清单 9-7 所示。

```
@RunWith(SpringRunner.class)
public class SpringbootMongodbDemoApplicationTests
{
@Autowired
private UserRepository userRepository;
@Test
public void findAllUsers()  {
List users = userRepository.findAll();
assertNotNull(users);
assertTrue(!users.isEmpty());
}
@Test
public void findUserById()  {
User user = userRepository.findById("1").get();
assertNotNull(user);
}
@Test
public void createUser() {
User user = new User("3", "Joseph", "joseph@gmail.com");
User savedUser = userRepository.save(user);
User newUser = userRepository.findById(savedUser.getId()).get();
assertEquals("Joseph", newUser.getName());
assertEquals("joseph@gmail.com", newUser.getEmail());
}
}
清单 9-7.
SpringbootMongodbDemoApplicationTests.java
```

要了解更多关于 Spring Data Mongo 特性的信息，请参阅 Spring Data MongoDB 参考文档：[`http://docs.spring.io/spring-data/data-mongo/docs/current/reference/html`](http://docs.spring.io/spring-data/data-mongo/docs/current/reference/html)。

## 本章小结

本章介绍了如何安装和使用 MongoDB 服务器，并探讨了如何在 Spring Boot 应用程序中使用 Spring Data MongoDB。下一章，你将开始使用 Spring Boot 和 Spring MVC 开发 Web 应用程序。

