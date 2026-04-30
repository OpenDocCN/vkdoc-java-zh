# 30. NoSQL

SQL 数据库在将现实世界的关系映射到数据存储方面做得很好。然而，开发一个大型关系结构并非易事，因为在项目后期或生产阶段，更改关系、添加或移动表列都是耗时的任务。你必须检查数据库条目关系是否仍然有效并正确关联到数据库访问代码，并检查所有客户端是否能够应对更新的数据库结构。出于这些原因，在项目开始时需要投入大量精力来创建一个无懈可击的数据库关系模型。
为了简化结构化数据模型的更改，你必须放宽甚至放弃严格的数据库表关系。此类数据库管理系统在数据库结构频繁变化的现代使用场景中，最近获得了相当大的关注。它们提供了 Java 客户端可用的 API。它们通常被称为“NoSQL”数据库。这是“Not-Only-SQL”的缩写，因为通常仍然可以使用类似 SQL 的传统方式访问数据，而无模式地访问数据库条目是处理表条目的首选方式。
目前还没有最终确定的 Jakarta EE 10 标准来描述对 NoSQL 数据库的访问（参见 [`https://jakarta.ee/specifications/nosql/`](https://jakarta.ee/specifications/nosql/) 了解正在进行的工作）。尽管如此，鉴于 NoSQL 数据库如今的重要性，本章简要介绍两种 NoSQL DBMS——CouchDB 和 MongoDB。

## 从 Jakarta EE 应用程序使用 CouchDB

CouchDB 提供了一个 REST API，你可以使用 `java.net.http` 包中的 `HttpClient` 从 Java 访问该 API。假设你有一个 CouchDB 在本地端口 5984 上运行，并且有一个管理员用户 `admin`，密码为 `PASSWD`，你首先定义几个辅助函数：

```
import java.net.Authenticator;
import java.net.PasswordAuthentication;
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;
import java.net.http.HttpResponse.BodyHandlers;
import java.nio.charset.StandardCharsets;
import java.time.Duration;
import java.util.Base64;
import java.util.function.BiFunction;
import java.util.function.Function;
...
final String SERVER = "127.0.0.1:5984";
final String USER = "admin";
final String PASSWD = "PASSWD";
var auth = new Authenticator() {
protected PasswordAuthentication
getPasswordAuthentication() {
return new PasswordAuthentication(USER,
PASSWD.toCharArray());
}
};
var encodedAuth = Base64.getEncoder()
.encodeToString((USER + ":" + PASSWD)
.getBytes(StandardCharsets.UTF_8));
Function get = (url) -> {
return HttpRequest.newBuilder()
.uri(URI.create("http://" + SERVER  + "/" + url))
.timeout(Duration.ofSeconds(10))
.header("Authorization", "Basic " + encodedAuth)
.GET().build();
};
Function delete = (url) -> {
return HttpRequest.newBuilder()
.uri(URI.create("http://" + SERVER  + "/" + url))
.timeout(Duration.ofSeconds(10))
.header("Authorization", "Basic " + encodedAuth)
.DELETE().build();
};
BiFunction post =
(url,body) -> {
return HttpRequest.newBuilder()
.uri(URI.create("http://" + SERVER + "/" + url))
.timeout(Duration.ofSeconds(10))
.header("Authorization", "Basic " + encodedAuth)
.POST(HttpRequest.BodyPublishers.ofString(body))
.build();
};
BiFunction put = (url,body) -> {
return HttpRequest.newBuilder()
.uri(URI.create("http://" + SERVER + "/" + url))
.timeout(Duration.ofSeconds(10))
.header("Authorization", "Basic " + encodedAuth)
.PUT(HttpRequest.BodyPublishers.ofString(body))
.build();
};
HttpClient client = HttpClient.newBuilder()
.version(HttpClient.Version.HTTP_1_1)
.followRedirects(HttpClient.Redirect.NORMAL)
.connectTimeout(Duration.ofSeconds(10))
.authenticator(auth)
.build();
Function> invoke =
(req) -> {
try {
return client.send(req, BodyHandlers.ofString());
} catch (Exception e) {
e.printStackTrace(System.err);
return null;
}
};
```

为简单起见，此示例使用了 lambda 表达式，但辅助函数可以很容易地转换为类方法。

现在很容易向 CouchDB 实例发送请求。以下代码片段中故意未使用搜索语句，因为对于 CouchDB，需要创建可搜索索引和/或安装搜索插件，这超出了这个简短示例的范围。

```
// ----- CouchDB 信息
var request1 = get.apply("");
var response1 = invoke.apply(request1);
System.out.println(response1.statusCode() +"\n"
+ response1.body());
// ----- 创建数据库
var request2 = put.apply("mydb","");
var response2 = invoke.apply(request2);
System.out.println(response2.statusCode() + "\n"
+ response2.body());
// ----- 创建条目
var request3 = put.apply("mydb/001",
"{ \"name\":\"John\", \"age\":42 }");
var response3 = invoke.apply(request3);
System.out.println(response3.statusCode() + "\n"
+ response3.body());
// ----- 删除数据库
var request99 = delete.apply("mydb");
var response99 = invoke.apply(request99);
System.out.println(response99.statusCode() + "\n"
+ response99.body());
```

## 从 Jakarta EE 应用程序使用 MongoDB

MongoDB 有一个专用的 Java 驱动程序。将以下依赖项添加到你的项目中（这是针对 Gradle 的）：

```
...
dependencies {
...
implementation 'org.mongodb:' +
'mongodb-driver-sync:4.5.1'
implementation 'org.slf4j:' +
'slf4j-simple:1.8.0-beta4'
implementation 'org.slf4j:' +
'slf4j-api:1.8.0-beta4'
}
```

对于 Maven 依赖项，请在 `pom.xml` 文件中写入以下内容：

```
...

...

org.mongodb
mongodb-driver-sync
4.5.1

org.slf4j
slf4j-api
1.8.0-beta4

org.slf4j
slf4j-simple
1.8.0-beta4

```

然后，你可以在代码中使用驱动程序 API 中的类。请注意，MongoDB 会在首次使用时自动创建数据库和集合（MongoDB 中的表称为“集合”），如果它们不存在的话。因此，你无需显式创建它们。

```
...
import org.bson.Document;
import org.bson.types.ObjectId;
import com.mongodb.client.MongoClient;
import com.mongodb.client.MongoClients;
import com.mongodb.client.MongoCollection;
import com.mongodb.client.MongoDatabase;
public class MongoMain {
public static void main(String[] args) {
MongoClient client = MongoClients.create();
// 访问数据库：
MongoDatabase database = client.
getDatabase("my_database");
// 访问集合（表）：
MongoCollection persons =
database.getCollection("persons");
// 创建一个文档（条目）：
Document p = new Document("name", "John")
.append("cars", new Document("Tesla", 2019));
// 保存它：
ObjectId id = persons.insertOne(p)
.getInsertedId().asObjectId().getValue();
// 搜索文档（条目）：
Document john = persons.find(
new Document("name", "John")).first();
System.out.println(john.toJson());
}
}
```

这适用于在本地主机上运行并使用标准端口 27017 的 MongoDB 实例。对于不同的地址或端口，请查看 `MongoClients` 的其他 `create( ... )` 方法。有关更多详细信息，请参阅 [`https://docs.mongodb.com/drivers/java/sync/current/`](https://docs.mongodb.com/drivers/java/sync/current/) 和 [`https://mongodb.github.io/mongo-java-driver/`](https://mongodb.github.io/mongo-java-driver/)。

