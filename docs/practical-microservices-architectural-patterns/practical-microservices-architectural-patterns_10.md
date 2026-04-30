# 脚注

7. Spring Boot

在上一章中，你了解了消息传递。你使用 Spring 配置了消息发送者和监听器，并在 Spring 中显式地连接了 Bean 和所需的基础设施。到目前为止，一切顺利。那么，如何能让各方面变得更简单呢？你将在本章中了解 Spring Boot，它采用了一种对 Spring 平台和许多第三方 Java 库的“约定优于配置”的观点，从而可以轻松、直接地创建独立的、生产级的、基于 Spring 的应用程序，并且可以“直接运行”。由于 Boot 遵循了开发人员在使用 Spring 和其他第三方库时一直利用的许多约定和使用模式，因此大多数 Spring Boot 应用程序只需要很少的 Spring 配置。你将使用 Spring Boot 来开发与本章讨论相关的几个示例。再次强调，目的不是详细涵盖 Spring Boot；这超出了本章的范围。熟悉 Java 或 Spring 的读者访问 Spring Boot 主页应该可以毫无困难地入门。我将花时间讨论讨论所必需的最基本工具，以免偏离本书的意图，即解释实用的微服务。然而，当你开始查看后续章节中的代码示例时，你应该熟悉基本的构建块代码结构。

你将在本章中学习以下内容：

*   如何使用 Spring Initializer 创建 Boot 项目模板
*   如何使用 cURL 访问一个针对 MongoDB 执行 CRUD 操作的 Boot 示例
*   HATEOAS 和 HAL 简介，以及如何使用`RestTemplate`访问数据
*   如何开发一个端到端的 Boot 示例，用于对 REST 执行 CRUD 操作



使用 Spring Boot 和 MongoDB 执行数据操作
多语言持久化是微服务架构中的一个关键方面。虽然你仍然需要具备本地和/或分布式事务能力的 ACID 兼容数据存储，但仔细分析会发现，大多数数据存储和检索操作并不需要这些总是要付出代价的严格能力。如今，许多 NoSQL 数据库被设计为在大型集群上运行，并能处理海量数据；它们还能高效地响应流量呈指数级增长的读取请求，同时性能基本保持线性。因此，在单个应用程序中同时满足两种（SQL 和 NoSQL）数据存储需求是很自然的，而这正是多语言持久化发挥作用的地方。在微服务上下文中，你可以根据单个微服务或单个大型企业应用程序的组件使用数据的方式，选择使用多种数据存储技术。当你需要以文档格式存储数据时，MongoDB 是最佳选择，因此你将在本书的其余章节中研究一个 MongoDB 示例。

在本节中，你将实现一个“Hello World”类型的 Spring Boot 应用程序。开始开发 Spring Boot 应用程序的方法不止一种，主要方法有：

*   使用 Spring Boot 命令行界面（CLI）作为命令行工具^(¹⁰)
*   使用 Eclipse 和 Spring Tool Suite (STS) 等 IDE 来提供 Spring Boot 支持^(¹¹)
*   使用 Spring Initializer 项目

在本节中，你将采用最后一种方法。

使用 Spring Initializer 创建 Spring Boot 项目模板
你将在此处使用 Spring Initializer 创建一个 Spring Boot 项目。Spring Initializer 是基于 Eclipse 的 Spring Tool Set (STS) 项目向导的直接替代品，它提供了一个在线 Web UI 来配置和生成不同风格的 Spring Boot 项目。使用 Spring Initializer，你可以通过在线界面生成一个项目，然后可以将其导入任何 IDE。

目标是快速开发一个应用程序，帮助你实现以下目标：

*   了解 Spring Boot
*   了解 MongoDB
*   了解使用 Spring Data 进行基本的 CRUD 操作

Spring Initializer 项目位于 [`http://start.spring.io`](http://start.spring.io)。

你需要填写详细信息，例如它是否是 Maven 项目、是否需要为 Boot 项目使用 Java 以及 Spring Boot 版本。其他详细信息包括 group 和 artifact ID，如图 7-1 所示。接下来，单击“Switch to go to the full version”链接，该链接位于“Generate Project”按钮下方。在这里，你可以选择 MongoDB 和 Rest Repositories 作为依赖项。你还需要确保 Java 版本为 8，并且包类型选择为 JAR。选择依赖项后，你可以点击“Generate Project”按钮。这将生成一个 Maven 项目。该项目可以作为 ZIP 文件下载到浏览器的默认下载目录中。

![../images/477630_1_En_7_Chapter/477630_1_En_7_Fig1_HTML.jpg](img/477630_1_En_7_Fig1_HTML.jpg)

图 7-1
使用 Spring Boot Initializer 生成 Spring Boot 模板

Spring Boot 提供了一组 Starter Poms 或 gradle 构建文件，你可以使用它们来添加所需的依赖项。根据其类路径中的库，Spring Boot 将通过自动配置自动配置所需的类。例如，要与数据库交互，如果类路径中有 Spring Data 库，它会自动设置与数据库的连接以及所需的 Data Source 类。

现在，你可以解压该文件并将其保存到文件系统中你选择的目录中。同样，一个没有空格的文件路径将使你在开发应用程序时避免许多不可预见的错误。现在，你可以使用你喜欢的 IDE 继续应用程序开发。

1.  如果你使用 Eclipse，现在可以转到“File”菜单并单击“Import”。
2.  导航到 Maven ➤ Existing Maven Projects，然后单击“Next”。
3.  单击“Root Directory”旁边的“Browse”选项，然后选择你之前解压的文件夹。
4.  单击“Finish”按钮。这会将生成的 Maven 项目加载到 Eclipse Project Explorer 中。

假设你熟悉 Eclipse，我将不在此花费时间或篇幅解释 Eclipse 的各个方面。相反，让我们详细地看一下应用程序开发。
首先，查看创建 Spring Boot 项目时自动生成的 `pom.xml`。这里解释了主要的三个元素。

Spring Boot Starter Parent
通常，你的 Maven `pom.xml` 文件将继承自 spring-boot-starter-parent 项目，并声明对一个或多个“starter”的依赖。在图 7-1 中，你可以看到两个这样的 starter：MongoDB 和 Rest Repositories。

清单 7-1 提到了你的 POM 继承层次结构，这是一个物料清单（BOM），用于管理项目所需的不同库和版本。

```
org.springframework.boot
spring-boot-starter-parent
1.5.4.RELEASE

清单 7-1
Spring Boot Starter Parent (ch07\ch07-01\pom.xml)
```

Spring Boot Dependencies

接下来，如果你可以查看依赖项部分，你会看到 POM 文件只提到了三个依赖项，如清单 7-2 所示。

```

org.springframework.boot
spring-boot-starter-data-mongodb

org.springframework.boot
spring-boot-starter-data-rest

org.springframework.boot
spring-boot-starter-test
test

清单 7-2
Spring Boot Dependencies
```

这些依赖项解释如下：

*   `spring-boot-starter-data-mongodb`：Spring Data MongoDB 项目帮助你与 MongoDB 文档数据库集成。Spring Data MongoDB 的主要功能领域是一个以 POJO 为中心的模型，用于与 MongoDB 集合交互以执行 CRUD 操作，并轻松编写存储库风格的数据访问层。
*   `spring-boot-starter-data-rest`：Spring Data REST 构建在 Spring 的 MVC 之上。Spring Data REST 创建了一组 Spring MVC 控制器和其他所需的 bean，以提供 RESTful 前端。然后，这些组件会传递性地链接到 Spring Data JPA 后端。使用 Spring Boot，这一切都会为你自动配置。
*   `spring-boot-starter-test`：此 starter 导入了 Spring Boot 测试模块、JUnit、AssertJ、Hamcrest 以及许多其他有用的库，这些库将帮助你测试应用程序。

Spring Boot Maven Plugin

接下来，让我们看看 Spring Boot Maven 插件。参见清单 7-3。

```

org.springframework.boot
spring-boot-maven-plugin

清单 7-3
Spring Boot Maven Plugin
```

清单 7-3 中的 Spring Boot Maven 插件有助于创建单个可执行 JAR。单个可执行 JAR 是一个自包含的可执行 JAR 文件，你可以在生产环境中运行。在第 3 章的“微服务是自包含的”部分，你看到了对自包含和自足的可执行包的要求。Java 没有提供任何标准方法来加载 JAR 文件内包含的 JAR 文件。在这里，你可以使用 uber JAR。一个 uber JAR 简单地将所有 JAR 中的所有类打包到一个归档文件中。但是，当需要同一库的不同版本或文件名相同时，这有时会遇到问题。因此，Spring Boot 采用了一种不同的方法，直接嵌套 JAR，而 Spring Boot Maven 插件则负责所有必要的准备工作。



设计与编码领域实体

为了本节示例的需要，我们将保持领域实体非常简单。该实体是一个典型的可在电商网站上架的商品。多个商品归属于一个商品类别，因此在网站上，你可以先展示商品类别，在线用户可以通过浏览商品类别来查看所选类别下的所有可用商品。图 7-2 展示了领域实体的设计。这是一个基础设计，将帮助你管理商品类别与商品之间的关系，以满足在所选商品类别下列出所有可用商品的需求。

![../images/477630_1_En_7_Chapter/477630_1_En_7_Fig2_HTML.jpg](img/477630_1_En_7_Fig2_HTML.jpg)

图 7-2
商品/商品类别领域模型

清单 7-4 展示了实现此功能所需的代码。

```
public class Product {
@Id
private String id;
private String name;
private String code;;
private String title;
private String description;
private String imgUrl;
private Double price;
private String productCategoryName;
}
public class ProductCategory {
@Id
private String id;
private String name;
private String title;
private String description;
private String imgUrl;
}
清单 7-4
商品/商品类别领域模型 (ch07\ch07-01\src\main\java\com\acme\ch07\ex01\product\model\Product.java)
```

清单 7-4 还揭示了一个配置为自动生成的 `id` 对象，因此你无需手动处理它。

编码仓库

仓库是一个接口，它允许你执行涉及 `Product` 和 `ProductCategory` 对象的各种操作。它通过扩展 Spring MongoDB 项目 API 中定义的 MongoRepository 接口来获得这些操作。MongoRepository 提供的基本功能包括：

*   支持对特定类型的仓库进行通用的 CRUD 操作
*   使用分页和排序抽象来检索实体的方法

参见清单 7-5。

```
@RepositoryRestResource(collectionResourceRel = "products", path = "products")
public interface ProductRepository extends MongoRepository  {
public List findByProductCategoryName(
@Param("productCategory") String  productCatagoryName);
}
@RepositoryRestResource(collectionResourceRel = "categories",
path = "categories")
public interface ProductCategoryRepository extends
MongoRepository  {
}
清单 7-5
商品和商品类别 Mongo 仓库 (ch07\ch07-01\src\main\java\com\acme\ch07\ex01\product\repository\ProductRepository.java)
```

这里你定义了一个自定义查询，用于根据 `Category` 检索 `Product` 对象列表。你可以向上述仓库接口定义更多抽象的实用方法，从而免费获得它们的实现，但为了满足你的目的，我将其限制为清单 7-5 所示。在运行时，Spring Data REST 会自动创建上述接口的实现。然后，它将使用 `@RepositoryRestResource` 注解来指示 Spring MVC 在 `/products` 和 `/categories` 路径下创建 RESTful 端点。

编码启动应用程序可执行文件

如果需要，你可以修改 `EcomProductMicroserviceApplication.java` 文件。清单 7-6 显示了之前生成的默认代码。这是一个简单的 Java 类，带有一个 `main()` 方法，因此它可以作为一个独立的 Java 应用程序运行，并使用嵌入式 Tomcat Servlet 容器作为 HTTP 运行时。

```
@SpringBootApplication
public class EcomProductMicroserviceApplication {
public static void main(String[] args) {
SpringApplication.run(EcomProductMicroserviceApplication.class,
args);
}
}
清单 7-6
Spring Boot 应用程序 (ch07\ch07-01\src\main\java\com\acme\ch07\ex01\product\EcomProductMicroserviceApplication.java)
```



虽然 `main()` 方法使用 Spring Boot 的 `SpringApplication.run()` 方法来启动应用程序，但 Spring Boot 内部会启动 Spring Data JPA，为 `ProductRepository` 和 `ProductCategoryRepository` 创建具体的实现类，并配置其使用 JPA 与后端数据库集成。

## 构建并打包 Spring Boot 应用程序

Spring Boot 的风格是通过创建一个完全自包含的可执行 JAR 文件来构建和打包示例，该文件可以在生产环境中运行。正如第 3 章“微服务是自包含的”一节中所解释的，可执行 JAR 也称为 fat JAR，是一种归档文件，其中包含你编译后的类以及代码运行所需的所有其他 JAR 依赖项，这些依赖项在你解析 POM 依赖时会被解析。`ch07-01\pom.xml` 包含了构建和运行示例所需的 Maven 脚本。请确保 MongoDB 已启动并正在运行。你可能需要参考附录 A 来开始使用 MongoDB。

你首先需要将可执行文件构建并打包成一个单独的 JAR 文件：

```
cd ch07-01
D:\binil\gold\pack03\ch07\ch07-01>build
D:\binil\gold\pack03\ch07\ch07-01>mvn clean package -Dmaven.test.skip=true
```

这将在 `target` 文件夹中生成可执行 JAR。需要注意的是，当你执行上述 Maven 脚本时，默认情况下它也会执行测试用例，因此你通过跳过测试来禁用了它。

你可能需要注意以下两个文件，你可以使用它们来自定义许多运行时参数，在你的示例中，这些文件在 `samples` 文件夹中保持空白：

```
ch07-01\src\main\resources\application.properties
ch07-01\src\test\resources\test.properties
```

此外，为了加快你的开发周期，当你只想编译所有 Java 文件（包括测试类）时，你可以使用以下命令：

```
cd ch07-01
ch07-01>mvn clean test-compile
```

如果你想打包你的可执行工件，但不想构建或执行测试类，你可以使用以下命令：

```
cd ch07-01
ch07-01>mvn clean package -Dmaven.test.skip=true
```

## 运行并测试 Spring Boot 应用程序

在 mongo shell 已连接到正在运行的 mongod 实例的 Windows 命令提示符中，你可能需要先清理任何需要的集合：

```
> show collections
product
productCategory
> db.productCategory.drop()
true
> db.product.drop()
true
> show collections
>
```

现在，你可以通过多种方式运行 Spring Boot 应用程序。最直接的方式是通过以下命令执行 JAR 文件：

```
cd ch07-01
D:\binil\gold\pack03\ch07\ch07-01>run
D:\binil\gold\pack03\ch07\ch07-01>rem java -jar .\target\Ecom-Product-Microservice-0.0.1-SNAPSHOT.jar
D:\binil\gold\pack03\ch07\ch07-01>mvn spring-boot:run
```

一旦应用程序启动，它将在 mongodb 中创建所需的集合：

```
> show collections
product
productCategory
>
```

现在你可以测试应用程序了。你可以使用任何你喜欢的 REST 客户端。你可以使用 cURL 工具快速对服务器执行命令。请参考附录 D 了解如何使用 cURL 执行命令的概述。现在你可以查看服务器提供了什么；参见清单 7-7。

```
$ curl http://localhost:8080
{
"_links": {
"categories": {
"href": "http://localhost:8080/categories{?page,size,sort}",
"templated": true
},
"products": {
"href": "http://localhost:8080/products{?page,size,sort}",
"templated": true
},
"profile": {
"href": "http://localhost:8080/profile"
}
}
}
清单 7-7
使用 cURL 的 GET HTTP 请求
```

请参考附录 D 查看更全面的 cURL 命令集，这些命令在本书的其余部分会非常有用。图 7-3 展示了如何在基于 Unix 的环境中使用 cURL 调用基本的 REST 操作。

![../images/477630_1_En_7_Chapter/477630_1_En_7_Fig3_HTML.jpg](img/477630_1_En_7_Fig3_HTML.jpg)

图 7-3
使用 cURL 的 GET HTTP 请求

清单 7-7 显示了一个位于 `http://localhost:8080/products` 的 products 链接。它有几个选项，例如 `?page`、`?size` 和 `?sort`。还有其他链接，你可以检索并理解它们。

Spring Data REST 使用超文本应用程序语言 (HAL) 格式进行 JSON 输出。它很灵活，并提供了一种简单方便的方式来提供与所提供数据相邻的链接。HAL 将使你的 API 可探索，并且其文档易于从 REST API 本身中发现。测试这些链接的最简单方法是使用 Postman 工具。你可能想使用 Postman 探索以下链接：

```
http://localhost:8080/profile
http://localhost:8080/profile/products
http://localhost:8080/profile/categories
```

图 7-4 展示了在 Postman 中对链接 `http://localhost:8080/profile/products` 执行 GET 操作时的样子。

![../images/477630_1_En_7_Chapter/477630_1_En_7_Fig4_HTML.jpg](img/477630_1_En_7_Fig4_HTML.jpg)

图 7-4
使用 Postman 的 GET HTTP 请求

或者，你现在可以使用 Maven 执行 Junit 测试。测试位于名为 `ch07-01\src\test\java\com\acme\ch07\ex01\product` 的文件夹中。测试直接访问 Mongo 存储库并执行数据操作。参见清单 7-8。

```
public class ProductRepositoryTest {
@Autowired
private ProductRepository productRepository;
@Before
public void setUp(){
productRepository.deleteAll();
}
@Test
public void testAddProduct(){
productRepository.save(createProject());
assertTrue("successfully saved",true);
}
@Test
public void testFindAllProducts(){
productRepository.save(createProject());
List productList = productRepository.findAll();
assertTrue(productList.size()>0);
}
@Test
public void testProductByProductCategory(){
Product product = createProject();
productRepository.save(product);
List productList =
productRepository.findByProductCategoryName(
product.getProductCategoryName());
assertTrue(productList.size()>0);
}
}
清单 7-8
Junit 测试 Mongo 存储库 (ch07\ch07-01\src\test\java\com\acme\ch07\ex01\product\ProductRepositoryTest.java)
```

要执行测试，假设你的 Spring Boot 应用程序已经启动，请执行以下命令：

```
cd ch07-01
D:\binil\gold\pack03\ch07\ch07-01>test
D:\binil\gold\pack03\ch07\ch07-01>mvn test
```

## 使用 Spring HAL 浏览器、HATEOAS 进行开发

超媒体驱动的 REST 接口的一个非常方便的方面是，你可以使用 cURL（或你正在使用的任何 REST 客户端）发现所有 RESTful 端点。如果每个响应还提供了用户下一步可以操作的选项信息，则无需在方法调用（查询）之前与客户交换正式的合同或接口文档。HAL 格式提供了一种一致且简单的方法来在你的 API 中的资源之间建立超链接。HAL 将使你的 REST API 可探索，并且其文档易于从 API 本身中发现。这将使你的 API 在手动和机器可读模式下都更易于使用，因此对客户端开发人员更具吸引力。HATEOAS（将超文本作为应用程序状态的引擎）是一种 REST 模式，其中导航链接作为响应负载的一部分提供。然后，客户端可以确定状态并遵循作为该状态一部分提供的转换 URL。

大多数主流编程语言都有可用的开源库支持 HAL，因此采用 HAL 的 API 可以轻松地在多个平台和编程语言中提供和使用。它非常简单，你可以像处理任何其他 JSON 一样处理它。



HAL 浏览器
HAL 浏览器是一款基于 HAL 规范处理 HAL+JSON 数据的实用应用程序。它是一个使用 HAL 驱动的 JavaScript 的 Web 应用。你可以将其指向任何符合 HAL 规范的 Spring Data REST API，并利用它来浏览应用和遍历链接资源。

要让你的应用启用 HAL 浏览器，你只需在 Spring Initializer 中，除了图 7-1 中提到的依赖之外，再包含“Rest Repositories HAL Browser”依赖即可。因此，你的 `pom.xml` 将包含清单 7-9 中所示的额外依赖。

```xml
<dependency>
    <groupId>org.springframework.data</groupId>
    <artifactId>spring-data-rest-hal-browser</artifactId>
</dependency>
```

清单 7-9
Spring REST HAL 浏览器的 Maven 依赖 (ch07\ch07-02\pom.xml)

请注意，`pom.xml` 中还有一些配置细节，但这些额外的行都是用于配置 Java 简单日志门面 (SL4J) 的，它充当常用日志框架（如 Java Util Logging、Log4J、Log4J2、Logback 等）的门面。当你针对 SL4J API 编写日志代码时，你可以灵活地在部署时插入你想要的日志框架，这是通过一个中间桥接/适配器层实现的。还有一个“Exclude”配置用于排除内置的 Logback 依赖，因为如果类路径中存在 Logback，Spring Boot 默认会选择并使用它。

由于存放在 `ch07\ch07-02` 文件夹中的示例应用与你之前在 `ch07\ch07-01` 中看到的代码类似，我在此不再赘述。请确保 MongoDB 已启动并运行。你可能需要参考附录 A 来开始使用 MongoDB。

你可以按如下方式构建并运行应用：

```
cd ch07\ch07-02
D:\binil\gold\pack03\ch07\ch07-02>build
D:\binil\gold\pack03\ch07\ch07-02>mvn clean package -Dmaven.test.skip=true
cd ch07\ch07-02
D:\binil\gold\pack03\ch07\ch07-02>run
D:\binil\gold\pack03\ch07\ch07-02>mvn spring-boot:run
```

`spring-data-rest-hal-browser` 依赖会自动配置，当你访问应用的根 URI 时，HAL 浏览器就会被提供。你可以将浏览器指向

```
http://localhost:8080
```

这将会被重定向到图 7-5 中所示的 URL。

REST 端点有 GET 和非 GET 操作，你可以点击 GET 按钮并导航查看某个集合，或者点击非 GET 选项来对集合进行修改。这是通过启用 HAL 浏览器自动实现的，它可以从响应中读取链接，并将其显示在浏览器左侧的列表中；浏览器右侧则显示来自响应的详细信息，包括头部和主体（一个 HAL 文档）。

![../images/477630_1_En_7_Chapter/477630_1_En_7_Fig5_HTML.jpg](img/477630_1_En_7_Fig5_HTML.jpg)

图 7-5
Spring Data REST HAL 浏览器

HAL 浏览器与 URI 模板进行交互。你可以通过问号图标，点击“Products”或“Categories”旁边的某个 GET 方法。如果你选择导航到它，将会弹出一个展开对话框，如图 7-6 所示。

![../images/477630_1_En_7_Chapter/477630_1_En_7_Fig6_HTML.jpg](img/477630_1_En_7_Fig6_HTML.jpg)

图 7-6
展开 HAL URI 模板的 GET 方法

你可以不输入任何内容直接点击“Follow URI”按钮；如果这样，变量将被忽略，你将收到服务器返回的集合中的所有元素。类似地，你也可以点击上面的非 GET 按钮，这将弹出一个对话框，如图 7-7 所示。默认情况下，它显示的是 POST 方法。这个字段可以调整为 PUT 或 PATCH 资源。头部默认已正确填写，用于提交一个新的 JSON 文档。你可能会注意到，这些字段是根据资源的元数据自动提供的。这是由 Spring Data REST 自动生成的。

![../images/477630_1_En_7_Chapter/477630_1_En_7_Fig7_HTML.jpg](img/477630_1_En_7_Fig7_HTML.jpg)

图 7-7
展开 HAL URI 模板的非 GET 方法

使用 RestTemplate 测试 HAL REST 端点

你现在可以使用 Maven 执行 Junit 测试。测试代码位于 `ch07-02\src\test\java\com\acme\ch07\ex02\product` 文件夹中。本章上一节的测试直接访问了 Mongo 仓库并执行了数据操作；然而，本节的测试将访问由 Spring Data REST 自动生成的 REST API。清单 7-10 到 7-12 检查了使用 RestTemplate 访问 HAL REST 端点执行数据操作所需的代码。清单 7-10 展示了如何通过调用 HTTP POST 方法在服务器上创建新资源。它还展示了如何调用 HTTP DELETE 方法，你可以在测试初始化设置中执行此操作。

```java
public class ProductHalRestTemplateTest {
    private static String PRODUCT_SERVICE_URL =
        "http://127.0.0.1:8080/products";
    @Before
    public void setUp(){
        deleteAllProducts();
    }
    @Test
    public void testPostProduct(){
        try{
            Product productNew1 = createProduct("1");
            Product productNew2 = createProduct("2");
            RestTemplate restTemplate = restTemplate();
            Product productRetreived1 = restTemplate.postForObject(
                PRODUCT_SERVICE_URL, productNew1, Product.class);
            Product productRetreived2 = restTemplate.postForObject(
                PRODUCT_SERVICE_URL, productNew2, Product.class);
            assertTrue("successfully saved",true);
        }catch(Exception ex){
            assertTrue("successfully failed",true);
        }
    }
    public void deleteAllProducts(){
        RestTemplate restTemplate = restTemplate();
        List productList = getAllProducts();
        productList.forEach(item->restTemplate.delete(
            PRODUCT_SERVICE_URL + "/" + item.getId())
        );
    }
    private RestTemplate restTemplate() {
        ObjectMapper mapper = new ObjectMapper();
        mapper.configure(DeserializationFeature.FAIL_ON_UNKNOWN_PROPERTIES,
            false);
        mapper.registerModule(new Jackson2HalModule ());
        MappingJackson2HttpMessageConverter converter =
            new MappingJackson2HttpMessageConverter();
        converter.setSupportedMediaTypes(
            MediaType.parseMediaTypes("application/hal+json"));
        converter.setObjectMapper(mapper);
        return new RestTemplate(Arrays.asList(converter));
    }
}
```
清单 7-10
使用 RestTemplate 测试 HAL REST 端点 (ch07\ch07-02\src\test\java\com\acme\ch07\ex02\product\ProductHalRestTemplateTest.java)

你可以使用 HTTP GET 方法查询新创建的资源，如清单 7-11 所示。



```
public class ProductHalRestTemplateTest {
@Test
public void testGetAllProducts(){
testPostProduct();
List productList = getAllProducts();
assertTrue(productList.size() > 0);
}
public List getAllProducts(){
RestTemplate restTemplate = restTemplate();
ParameterizedTypeReference> responseTypeRef =
new ParameterizedTypeReference>() {
};
ResponseEntity> responseEntity =
restTemplate.exchange(PRODUCT_SERVICE_URL, HttpMethod.GET,
(HttpEntity) null, responseTypeRef);
PagedResources resources = responseEntity.getBody();
Collection products = resources.getContent();
List productList = new ArrayList(products);
return productList;
}
}
清单 7-11
使用 RestTemplate 测试 HAL GET REST 端点
```

清单 7-12 展示了如何通过 RestTemplate 调用 HTTP PUT 来修改服务器中的资源。

```
public class ProductHalRestTemplateTest {
@Test
public void testPutProduct(){
try{
Product productNew3 = createProduct("3");
RestTemplate restTemplate = restTemplate();
Product productRetreived3 = restTemplate.postForObject(
PRODUCT_SERVICE_URL, productNew3, Product.class);
productRetreived3.setPrice(productRetreived3.getPrice() * 2);
restTemplate.put( PRODUCT_SERVICE_URL + "/" +
productRetreived3.getId(), productRetreived3, Product.class);
Product productAgainRetreived =
restTemplate.getForObject(PRODUCT_SERVICE_URL + "/" +
productRetreived3.getId(), Product.class);
assertTrue("successfully saved",true);
}catch(Exception ex){
assertTrue("successfully failed",true);
}
}
}
清单 7-12
使用 RestTemplate 测试 HAL PUT 端点
```

要执行测试，假设你的 Spring Boot 应用程序已经启动，请执行以下命令：

```
cd ch07-02
D:\binil\gold\pack03\ch07\ch07-02>test
D:\binil\gold\pack03\ch07\ch07-02>mvn test
```

开发一个 RESTful Web 服务
REST（表述性状态转移）是一种用于设计分布式系统的架构风格，由 Roy Fielding 在 2000 年的博士论文中提出。REST 是一组应用于资源的通用约束；然而，它通常与 HTTP 相关联。本节将介绍 REST 概念，你将看到一个完整的 REST 提供者以及一个使用 Spring Boot 的 REST 消费者的完整实现。

REST 基础
REST 推荐一组约束，例如无状态、具有客户端/服务器关系、提供统一接口、请求-响应周期是幂等的等等。REST 并非严格与 HTTP 相关，但它最常与 HTTP 关联。

以下总结了 REST 的原则：

*   资源暴露 URI，这些 URI 易于理解且具有类似目录的结构，以便它们可以被发现、链接和遍历。
*   表示层以 JSON 或 XML 作为线缆级格式传输，以表示数据对象和属性。
*   操作或消息显式地使用 HTTP 方法（例如 GET、POST、PUT 和 DELETE）。
*   操作是无状态的，因此你在请求之间不会在服务器上存储任何客户端上下文。客户端仅持有会话状态，这样当客户端数量增加时，服务器的可扩展性不会因大量状态数据而受到影响。

使用 REST 还有其他优点。一个主要优点是像缓存和代理这样的中间件可以提供缓存命中，从而除非必要，否则不会给实际的资源服务器带来压力。缓存服务器是一种专门的代理服务器，它将帮助你提高 Web 性能。Web 性能可以在客户端、服务器端甚至其他中间件之间得到改善。遵循 REST HTTP 标准和约定将帮助你利用这些优化，由于 HTTP 是一个标准，并且许多设备都提供了配置这些优化的功能，因此你可以免费获得这些优化。

用于 CRUD 资源的 HTTP 方法

以下是可用于实现 REST 的主要 HTTP 方法：

*   GET：GET 是最简单的 HTTP 操作，其目的是从服务器检索资源。200 OK 是成功操作的状态码。所有 GET 操作都应该是幂等的，这意味着无论你用相同的参数重复操作多少次，资源的状态都不应发生任何变化。
*   POST^(¹²)：HTTP POST 为客户端提供了一种向服务器发送信息的选项。POST 推荐用于创建新资源（尽管它也可以更新现有资源）。
*   PUT：HTTP PUT 也为客户端提供了一种向服务器发送信息的选项；然而，根据 HTTP 规范，PUT 的使用语义与 POST 略有不同。使用 PUT，你可以向服务器发送一个“新对象”，以将其放置或定位在由 URI 表示的服务器位置，因此其意图应该是用新资源替换现有资源。就单个客户端而言，PUT 请求是幂等的。如果在 PUT 请求中只提供了数据元素的子集，其余部分将被替换为空或 null。
*   DELETE：DELETE 为客户端提供了一种从服务器移除资源的选项。URI 标识要删除的资源。资源可能不必立即删除；相反，它可以通过后台的异步或长时间运行的操作来完成。

在 Spring Boot 中开发一个 REST 控制器

你将使用前面章节中描述的相同 Product 实体来构建一个功能完整的 REST 服务，以执行 CRUD 操作。Spring 构建 RESTful Web 服务的方法是使用控制器来处理 HTTP 请求。清单 7-13 到 7-16 展示了使用 Spring 构建基于 HATEOAS 的 REST 控制器的完整代码。清单 7-13 展示了如何检索具有特定 ID 的产品。`@RequestMapping` 注解确保对 `/products/{id}` 的 HTTP 请求被映射到 `getProduct()` 方法。

```
@RestController
public class ProductRestController {
@Autowired
private ProductRepository productRepository;
@RequestMapping(value = "/products/{id}", method = RequestMethod.GET,
produces = MediaType.APPLICATION_JSON_VALUE)
public ResponseEntity> getProduct(
@PathVariable("id") String id) {
Product product = productRepository.findOne(id);
if (product == null) {
return new ResponseEntity>(HttpStatus.NOT_FOUND);
}
Resource productRes = new Resource(product,
linkTo(methodOn(ProductRestController.class).getProduct(
product.getId())).withSelfRel());
return new ResponseEntity>(productRes,
HttpStatus.OK);
}
@RequestMapping(value = "/products", method = RequestMethod.GET,
produces = {MediaType.APPLICATION_JSON_VALUE})
public ResponseEntity>> getAllProducts() {
List products = productRepository.findAll();
Link links[] = {linkTo(methodOn(ProductRestController.class).
getAllProducts()).withSelfRel(),linkTo(methodOn(
ProductRestController.class).getAllProducts()).withRel(
"getAllProducts")};
if(products.isEmpty()){
return new ResponseEntity>>(
HttpStatus.NOT_FOUND);
}
List> list=new ArrayList> ();
for(Product product:products){
list.add(new Resource(product,
linkTo(methodOn(ProductRestController.class).getProduct(
product.getId())).withSelfRel()));
}
Resources> productRes =
new Resources>(list, links) ;
return new ResponseEntity>>(
productRes, HttpStatus.OK);
}
}
清单 7-13
启用 HATEOAS 的 REST 控制器（含 GET 操作）(ch07\ch07-03\src\main\java\com\acme\ch07\ex03\product\controller\ProductRestController.java)
```



REST 组件通过 `@RestController` 注解进行标识，而 `ProductRestController` 则处理针对 Product 实体的基于 HTTP 的 CRUD 请求。方法体的实现使用提供的 id 检索产品实体，并返回一个新的 `Product` 对象。此 `Product` 对象的数据将直接以 JSON 格式写入 HTTP 响应。清单 7-13 还展示了一个批量检索操作，用于检索所有 `Product` 资源。

传统的 MVC 控制器与上述 RESTful Web 服务控制器之间的一个关键区别在于 HTTP 响应体的创建方式。这个 RESTful Web 服务控制器并非依赖视图技术来执行问候数据到 HTML 的服务器端渲染，而是简单地填充并返回一个 `Greeting` 对象。该对象数据将直接以 JSON 格式写入 HTTP 响应。

这段代码使用了 Spring 4 的 `@RestController` 注解，该注解将类完全标记为控制器，其中每个方法都返回一个领域对象，而不是像传统 MVC 控制器那样返回视图。它是 `@Controller` 和 `@ResponseBody` 组合在一起的简写形式。

为了将 `Product` 对象转换为 JSON，Spring 使用了 HTTP 消息转换器支持。你无需手动执行此转换，因为类路径上存在 Jackson 2，所以 Spring 的 `MappingJackson2HttpMessageConverter` 会被自动选中，用于将 `Product` 实例转换为 JSON。

让我们看看其他操作。清单 7-14 展示了如何创建新资源。

```
@RestController
public class ProductRestController {
@RequestMapping(value = "/products", method = RequestMethod.POST)
public ResponseEntity> postProduct(@RequestBody Product
product,    UriComponentsBuilder ucBuilder) {
List products =
productRepository.findByCode(product.getCode());
if (products.size() > 0) {
LOGGER.debug("A Product with code {} already exist",
product.getCode());
return new ResponseEntity>(HttpStatus.CONFLICT);
}
Product newProduct = productRepository.save(product);
HttpHeaders headers = new HttpHeaders();
headers.setLocation(ucBuilder.path("/products/{id}").buildAndExpand(
product.getId()).toUri());
Resource productRes =new Resource(newProduct,
linkTo(methodOn(ProductRestController.class).getProduct(
newProduct.getId())).withSelfRel());
return new ResponseEntity>(productRes,
headers, HttpStatus.OK);
}
}
清单 7-14
支持 HATEOAS 的 REST 控制器（包含 POST 操作）
```

你可以使用清单 7-15 中所示的代码片段对服务器端资源进行更改。

```
@RestController
public class ProductRestController {
@RequestMapping(value = "/products/{id}", method = RequestMethod.PUT)
public ResponseEntity> updateProduct(@PathVariable("id")
String id, @RequestBody Product product) {
Product currentProduct = productRepository.findOne(id);
if (currentProduct == null) {
return new ResponseEntity>(HttpStatus.NOT_FOUND);
}
currentProduct.setName(product.getName());
currentProduct.setCode(product.getCode());
currentProduct.setTitle(product.getTitle());
currentProduct.setDescription(product.getDescription());
currentProduct.setImgUrl(product.getImgUrl());
currentProduct.setPrice(product.getPrice());
currentProduct.setProductCategoryName(
product.getProductCategoryName());
Product newProduct = productRepository.save(currentProduct);
Resource productRes = new Resource(newProduct,
linkTo(methodOn(ProductRestController.class).getProduct(
newProduct.getId())).withSelfRel());
return new ResponseEntity>(productRes,
HttpStatus.OK);
}
}
清单 7-15
支持 HATEOAS 的 REST 控制器（包含 PUT 操作）
```

最后，DELETE 方法可以从服务器删除资源。清单 7-16 展示了如何删除具有特定 ID 的产品以及如何删除所有产品资源。

```
@RestController
public class ProductRestController {
@RequestMapping(value = "/products/{id}", method = RequestMethod.DELETE)
public ResponseEntity deleteProduct(
@PathVariable("id") String id) {
Product product = productRepository.findOne(id);
if (product == null) {
return new ResponseEntity(HttpStatus.NOT_FOUND);
}
productRepository.delete(id);
return new ResponseEntity(HttpStatus.NO_CONTENT);
}
@RequestMapping(value = "/products", method = RequestMethod.DELETE)
public ResponseEntity deleteAllProducts() {
productRepository.deleteAll();
return new ResponseEntity(HttpStatus.NO_CONTENT);
}
}
清单 7-16
支持 HATEOAS 的 REST 控制器（包含 DELETE 操作）
```

在第一部分中，你了解了如何编写 Spring Boot 应用程序。现在，你将构建并打包该应用程序。

由于存放在 `ch07\ch07-02` 文件夹中的示例应用程序与你之前在 `ch07\ch07-01` 和 `ch07\ch07-02` 中看到的代码类似，因此我在此不再赘述。请确保 MongoDB 已启动并正在运行。你可能需要参考附录 A 来开始使用 MongoDB。

你可以按如下方式构建和运行应用程序：

```
cd ch07\ch07-03
D:\binil\gold\pack03\ch07\ch07-03>build
D:\binil\gold\pack03\ch07\ch07-03>mvn clean package -Dmaven.test.skip=true
cd ch07\ch07-02
D:\binil\gold\pack03\ch07\ch07-02>run
D:\binil\gold\pack03\ch07\ch07-02>mvn spring-boot:run
```

使用 RestTemplate 测试 REST 控制器

一旦应用程序启动，你就可以对其进行测试。你可以使用任何你喜欢的 REST 客户端。清单 7-17 展示了如何使用 `RestTemplate` 编写一个 Java 客户端来消费 HATEOAS 服务。`RestTemplate` 是 Spring 用于同步客户端 HTTP 调用的核心类。`RestTemplate` 简化了与 HTTP 服务器的通信，并强制执行 REST 原则。`RestTemplate` 处理底层 HTTP 连接，让应用程序代码负责提供 URL（可能包含模板变量）并提取响应。默认情况下，`RestTemplate` 依赖 JDK 设施来建立 HTTP 连接；但是，你可以切换使用不同的 HTTP 库，例如 Apache HttpComponents、Netty 或 OkHttp。



```
public class ProductControllerRestTemplateTest {
private static String PRODUCT_SERVICE_URL =
"http://localhost:8080/products";
@Test
public void testPostProduct(){
Product productNew1 = createProduct("1");
RestTemplate restTemplate = restTemplate();
Product productRetreived1 = restTemplate.postForObject(
PRODUCT_SERVICE_URL,       productNew1, Product.class);
}
@Test
public void testGetAProduct(){
Product productNew2 = createProduct("2");
RestTemplate restTemplate = restTemplate();
Product productRetreived2 = restTemplate.postForObject(
PRODUCT_SERVICE_URL, productNew2, Product.class);
String uri = PRODUCT_SERVICE_URL + "/" + productRetreived2.getId();
Product productRetreivedAgain2 = restTemplate.getForObject(uri,
Product.class);
}
@Test
public void testPutProduct(){
Product productNew3 = createProduct("3");
RestTemplate restTemplate = restTemplate();
Product productRetreived3 = restTemplate.postForObject(
PRODUCT_SERVICE_URL, productNew3, Product.class);
productRetreived3.setPrice(productRetreived3.getPrice() * 2);
restTemplate.put( PRODUCT_SERVICE_URL + "/" +
productRetreived3.getId(), productRetreived3, Product.class);
Product productAgainRetreived3 =
restTemplate.getForObject(PRODUCT_SERVICE_URL + "/" +
productRetreived3.getId(), Product.class);
}
@Test
public void testDeleteAProduct(){
Product productNew4 = createProduct("4");
RestTemplate restTemplate = restTemplate();
Product productRetreived4 = restTemplate.postForObject(
PRODUCT_SERVICE_URL, productNew4, Product.class);
restTemplate.delete(PRODUCT_SERVICE_URL + "/" +
productRetreived4.getId());
}
@Test
public void testDeleteAllProducts(){
RestTemplate restTemplate = restTemplate();
Product productNew5 = createProduct("5");
Product productRetreived5 = restTemplate.postForObject(
PRODUCT_SERVICE_URL, productNew5, Product.class);
Product productNew6 = createProduct("6");
Product productRetreived6 = restTemplate.postForObject(
PRODUCT_SERVICE_URL, productNew6, Product.class);
restTemplate.delete(PRODUCT_SERVICE_URL);
}
private List getAllProducts(){
RestTemplate restTemplate = restTemplate();
ParameterizedTypeReference> responseTypeRef =
new ParameterizedTypeReference>() {
};
ResponseEntity> responseEntity =
restTemplate.exchange(PRODUCT_SERVICE_URL, HttpMethod.GET,
(HttpEntity) null, responseTypeRef);
PagedResources resources = responseEntity.getBody();
Collection products = resources.getContent();
List productList = new ArrayList(products);
return productList;
}
private RestTemplate restTemplate() {
ObjectMapper mapper = new ObjectMapper();
mapper.configure(DeserializationFeature.FAIL_ON_UNKNOWN_PROPERTIES,
false);
mapper.registerModule(new Jackson2HalModule());
MappingJackson2HttpMessageConverter converter =
new MappingJackson2HttpMessageConverter();
converter.setSupportedMediaTypes(MediaType.parseMediaTypes(
"application/hal+json, application/json"));
converter.setObjectMapper(mapper);
return new RestTemplate(Arrays.asList(converter));
}
}
清单 7-17
使用 RestTemplate 消费 HATEOAS 服务的客户端代码（ch07\ch07-03\src\test\java\com\acme\ch07\ex03\product\ProductControllerRestTemplateTest.java）

要执行测试，假设你的 Spring Boot 应用程序已经启动，请执行以下命令：

```
cd ch07-03
D:\binil\gold\pack03\ch07\ch07-03>test
D:\binil\gold\pack03\ch07\ch07-03>mvn test
```

总结
在本章中，你学习了 Spring Boot，以及它如何利用对 Spring 平台和许多其他第三方 Java 库的独到见解，提供一种简单且脚本化程度更低的方式来完成任务，通过约定来利用代码和打包。接着，你实践了 MongoDB，编写代码对自定义实体或资源执行 CRUD 操作。你使用了 Postman 和 Curl 进行简单测试。后续章节的代码还介绍了 HATEOAS 和 HAL 的概念。然后，你开发了一个完整的 REST 控制器示例，以便在服务器端有一个可以放置复杂业务逻辑以及自定义规则和验证（如果需要）的地方。在接下来的章节中，将基于本章的示例构建更多代码示例。此外，后续大部分章节都利用了 Spring Boot，因此本章是你让代码在其余章节中运行的基础。在 Spring Boot 之后，下一个需要了解的框架是 Spring Cloud，它为分布式微服务生态系统中的常见模式提供了实现。你将在下一章中学习这些内容。

脚注

8.  Spring Cloud

在第 7 章中，你了解了 Spring Boot。你探索了几个使用 Spring Boot 构建的示例，因此现在你已经拥有了构建企业级应用程序（无论是中等规模还是大规模）的基础模块。在本章中，你将研究 Spring 的下一个不可或缺的部分，即 Spring Cloud，它本身构建在 Spring Boot 之上。在分布式微服务生态系统中，有一组常见的模式可以帮助你将核心服务集成为一个松散耦合的连续体，而 Spring Cloud 提供了许多强大的工具来增强 Spring Boot 应用程序的行为，以实现这些模式。你只会学习保持后续章节讨论继续进行所需的主要和关键模块，同样会借助具体的代码示例。本章中的示例是在前面示例的基础上逐步构建的，因此请不要跳过任何部分，否则你可能无法顺利运行后续示例。

在本章中，你将学习以下内容：

*   用于进行 HTTP API 调用的 Feign 客户端

*   Hystrix，用于优雅降级的断路器

*   Hystrix 仪表盘，显示系统中断路器的图形概览

*   Ribbon，客户端负载均衡器

*   Eureka，基于 REST 的注册服务

*   Zuul，API 网关，它是你的微服务的前端控制器

*   配置服务器，用于外部化版本控制和管理微服务的配置参数

用于微服务架构的 Spring Cloud
第 4 章中的“微服务架构”部分讨论了当你从传统架构迁移到基于微服务的架构时需要处理的主要新问题。由于微服务架构中的反转效应，许多应用程序内部的问题现在转移到了（微）服务间层面，并且随着微服务数量的不断增长，相关的外部架构复杂性也随之固有增长。

Spring Cloud 为微服务生态系统中典型的用例和所需的扩展机制提供了良好的开箱即用支持。这里列出其中几个：

*   分布式/版本化配置

*   服务注册与发现

*   路由与负载均衡

*   服务间调用

*   断路器

*   分布式消息传递

与 Spring Boot 类似，Spring Cloud 采用了一种非常声明式的方法，通常只需在类路径中包含一个依赖项和/或一个注解，就能获得大量功能。现在让我们来了解其中的一些。



Spring Cloud 中 Feign 客户端的使用
Feign 客户端是一个受 Retrofit、JAXRS-2.0 和 WebSocket 等库启发的 Java 到 HTTP 的客户端绑定器。Feign 旨在通过将绑定分母统一泛化到 HTTP API（无论 REST API 的成熟度如何，即 API 的 RESTful 程度），来降低调用 HTTP API 的复杂性。Feign 通过将注解处理成模板化请求，使 HTTP API 调用变得简单。在发送请求之前，参数会以直接的方式应用到这些模板中。然而，Feign 仅支持基于文本的 API。尽管如此，Feign 极大地简化了系统方面的工作，例如重放请求、单元测试等。在本节中，你将学习如何调用 HTTP API。

设计一个 Feign 客户端场景

从本节开始，以及本章中 Spring Cloud 的所有其他示例，你将使用一个简单但具有代表性的真实用例。你将首先研究用例的设计，然后进一步学习。图 8-1 展示了一个简单的场景，其中有两个独立的应用程序，或者用我们的术语来说，是两个独立的微服务：Product Web 和 Product Server。

![../images/477630_1_En_8_Chapter/477630_1_En_8_Fig1_HTML.jpg](img/477630_1_En_8_Fig1_HTML.jpg)

图 8-1
一个 Feign 客户端
使用场景

让我们逐一探讨这些组件：

*   **Product Server**：Product Server 是一个功能完备的微服务应用程序，实现了部分 CRUD 操作。该微服务使用 Mongo DB 来写入和读取与产品实体相关的数据。因此，该微服务封装了针对数据库执行典型 CRUD 操作所需的所有组件，并将功能作为基于 REST 的服务暴露出来，包括：

    *   **ProductRestController**：暴露选定 CRUD 操作的 REST 控制器
    *   **Product**：`Entity` 类，即你要持久化到数据库的内容
    *   **ProductRepository**：执行所有 Mongo DB 操作管道的 Mongo 仓库
    *   **EcomProductMicroserviceApplication**：基于 Spring Boot 的 `Application` 类

*   **Product Web**：Product Web 被设计为另一个功能完备的微服务应用程序。然而，这次该微服务应用程序不执行任何 CRUD 操作；相反，它充当面向外部世界或客户端的门面，将所有调用委托给 Product Server 微服务。因此，Product Web 也拥有之前为 Product Server 列出的所有组件；唯一的区别是没有仓库组件，因为它不进行任何数据库交互。但是，由于 Product Web 将所有传入的 REST 调用委托给 Product Server 微服务，你必须为此创建代码，而正是出于这个目的，你将利用 Feign 客户端，而不是手动编写所有管道代码，我将在接下来解释这一点。

*   **ProductService**：ProductService 是列出所有选定用于暴露的方法的接口，因此 Product Server 和 Product Web 都履行此接口中指定的契约。

*   **ProductServiceProxy**：该组件是另一个扩展了 ProductService 的抽象接口，因此它获得了 Product Server 和 Product Web 接口所期望的所有方法。此外，ProductServiceProxy 是用于注解 Feign 客户端基本配置的接口。在实际意义上，这可以被视为服务器端功能的客户端代理。

*   **Spring Cloud Feign 客户端**：Spring 的 Feign 客户端是 Spring Cloud 即时实现的实际实现。正是通过这个代理实现，来自 Product Web 的调用被委托给 Product Server。

上述交互如图 8-2 所示。



![../images/477630_1_En_8_Chapter/477630_1_En_8_Fig2_HTML.jpg](img/477630_1_En_8_Fig2_HTML.jpg)

图 8-2
通过 Feign 客户端进行调用委托

在此，来自浏览器的请求将首先到达 Product Web 微服务。Product Web 微服务随后会借助 `ProductServiceProxy` 将调用委托给 Product Server 微服务。Product Server 微服务将从数据库中检索数据并提供响应，该响应将通过调用堆栈返回给浏览器。

使用 Feign 客户端的代码

由于 Feign 是一个声明式 HTTP 客户端，只需在 Maven POM 中引入 `spring-cloud-starter-feign` 依赖，并添加一个 `@EnableFeignClients` 注解，即可获得一个功能完备、配置合理且开箱即用的 HTTP 客户端。请访问 `pom.xml` 查看 Feign 客户端依赖的显式声明。参见清单 8-1。

```
org.springframework.cloud
spring-cloud-starter-feign

清单 8-1
Maven 中的 Feign 客户端依赖 (ch08\ch08-01\ProductWeb\pom.xml)
```

将 Feign 添加到类路径后，只需再添加一个注解，即可使用默认配置属性使一切正常运行。这一点在清单 8-2 中有所说明。

```
import org.springframework.cloud.netflix.feign.EnableFeignClients;
@EnableFeignClients(basePackageClasses = ProductServiceProxy.class)
@ComponentScan(basePackageClasses = ProductServiceProxy.class)
@CrossOrigin
@RestController
public class ProductRestController implements ProductService{
private ProductServiceProxy productServiceProxy;
@Autowired
public ProductRestController(ProductServiceProxy productServiceProxy){
this.productServiceProxy = productServiceProxy;
}
@RequestMapping(value = "/productsweb", method = RequestMethod.GET ,
produces = {MediaType.APPLICATION_JSON_VALUE})
public ResponseEntity>> getAllProducts() {
return productServiceProxy.getAllProducts();
}
@RequestMapping(value = "/productsweb/{productId}", method =
RequestMethod.GET, produces = MediaType.APPLICATION_JSON_VALUE)
public ResponseEntity> getProduct(
@PathVariable("productId") String productId) {
return productServiceProxy.getProduct(productId);
}
}
清单 8-2
启用 Feign 客户端 (ch08\ch08-01\ProductWeb\src\main\java\com\acme\ecom\product\controller\ProductRestController.java)
```

在清单 8-2 的 Feign 客户端代理中，`ProductServiceProxy` 被注入到代码中。创建这个 REST 代理客户端非常简单，大多数情况下，你只需要创建一个接口并添加一些注解。Spring Cloud 环境会在运行时创建实现，并使调用委托生效。清单 8-3 展示了代码中的 `ProductServiceProxy`。

```
import org.springframework.cloud.netflix.feign.FeignClient;
@FeignClient(name="product-proxy", url = "http://localhost:8080")
public interface ProductServiceProxy extends ProductService{
}
清单 8-3
Feign REST 客户端 (ch08\ch08-01\ProductWeb\src\main\java\com\acme\ecom\product\client\ProductServiceProxy.java)
```

我不会解释 Product Server 微服务的代码，因为它与我在第 7 章“开发 RESTful Web 服务”部分中已经解释过的示例非常相似。

构建并测试 Feign 客户端
演示 Feign 客户端所需的完整代码位于 `ch08-01` 文件夹中。
请确保 MongoDB 已启动并正在运行。你可能需要参考附录 A 来开始使用 MongoDB。

首先，构建并打包 Product Server 微服务的可执行文件，然后启动服务器。这里提供了一个实用脚本，你可以在 `ch08\ch08-01\ProductServer\make.bat` 文件夹中轻松执行：

```
cd ch08\ch08-01\ProductServer
D:\binil\gold\pack03\ch08\ch08-01\ProductServer>make
D:\binil\gold\pack03\ch08\ch08-01\ProductServer>mvn -Dmaven.test.skip=true clean package
```



您可以通过多种方式运行 Spring Boot 应用程序。最直接的方式是通过以下命令执行 JAR 文件：

```
D:\binil\gold\pack03\ch08\ch08-01\ProductServer>run
D:\binil\gold\pack03\ch08\ch08-01\ProductServer>java -jar -Dserver.port=8080 ./target/Ecom-Product-Microservice-0.0.1-SNAPSHOT.jar
```

这些命令将在 8080 端口启动 Product Server。请注意，位于以下位置的初始化组件会在启动时向 MongoDB 注入一些 Product 实例，这将便于您稍后演示应用程序：

```
ch08\ch08-01\ProductServer\src\main\java\com\acme\ecom\productInitializationComponent.java
```

接下来，构建并打包 Product Web 微服务的可执行文件，然后启动服务器：

```
cd ch08\ch08-01\ProductWeb
D:\binil\gold\pack03\ch08\ch08-01\ProductWeb>make
D:\binil\gold\pack03\ch08\ch08-01\ProductWeb>mvn -Dmaven.test.skip=true clean package
D:\binil\gold\pack03\ch08\ch08-01\ProductWeb>run
D:\binil\gold\pack03\ch08\ch08-01\ProductWeb>java -jar -Dserver.port=8081 .\target\Ecom-Product-Microservice-0.0.1-SNAPSHOT.jar
```

上述命令将在 8081 端口启动 Product Web。

以下位置提供了一个快速工具来测试应用程序：

```
ch08\ch08-01\ProductWeb\src\main\resources\product.html
```

建议在 Chrome 浏览器中打开此 HTML 工具。加载完成后，浏览器客户端将向监听 8081 端口的 Product Web 发送请求，Product Web 会通过 Feign 客户端代理，将调用委托给监听 8080 端口的 Product Server。

如果一切顺利，浏览器小部件将填充来自后端数据库的数据（图 8-3）。当您刷新屏幕时，浏览器将首先访问 Product Web 微服务。然后 Product Web 微服务会调用 Product Server 微服务，后者再查询 MongoDB 并返回结果。此演示的显著特点是，您创建了两个不同的微服务，然后其中一个微服务使用 Feign 客户端调用了另一个微服务。这是微服务之间通过 HTTP 调用进行同步通信的方式之一。

![../images/477630_1_En_8_Chapter/477630_1_En_8_Fig3_HTML.jpg](img/477630_1_En_8_Fig3_HTML.jpg)

图 8-3
测试 Feign 客户端

Hystrix 降级
有时，某个微服务、某个数据库或任何资源可能正忙，无法在预期的 SLA 内做出响应。在这种情况下，调用方服务不能无休止地等待依赖服务，因此需要有备用策略。您将使用 Hystrix 断路器降级到备用策略来处理这种情况。
当对服务或资源的方法调用失败时，断路器可以优雅地降级功能。使用断路器模式，可以在依赖服务失败时允许微服务继续运行。这将防止故障级联，从而为失败的服务提供足够的恢复时间。

设计 Hystrix 降级场景

您将扩展上一节中的示例（如图 8-1 所示）。图 8-4 展示了一个简单的场景，其中有三个独立的应用程序，或者用我们的术语来说，三个独立的微服务：Product Web、Product Server 1 和 Product Server 2。

![../images/477630_1_En_8_Chapter/477630_1_En_8_Fig4_HTML.jpg](img/477630_1_En_8_Fig4_HTML.jpg)

图 8-4
Hystrix 降级使用场景

示例场景的主要组件包括：

*   Product Server 1

*   Product Server 2

*   Product Web

*   Product Service

*   ProductServiceProxy

*   ProductAlternateServiceComponent

*   ProductAlternateServiceProxy

*   Spring Cloud Feign 客户端

上述许多组件的解释与前面“Feign 客户端”部分提供的相同，因此此处不再赘述。但是，我将解释新组件。

*   **ProductAlternateServiceComponent**：Product Web 被设计为将所有传入的 REST 调用委托给 Product Server 1 微服务。但是，如果 Product Server 1 微服务因某种原因宕机或无响应，您可以设计一个 Hystrix 回调，指向名为 ProductAlternateServiceComponent 的降级组件。在真实场景中，这可以是一个本地缓存，您可以从中向客户端提供备用数据。然而，在您的示例中，您已经了解了如何使用 Feign 客户端将调用代理到远程服务。因此，您将拥有一个 ProductAlternateServiceProxy 组件，调用可以被代理到该组件。

*   **Product Server 2**：Product Server 2 是一个功能完备的微服务应用程序，复制了 Product Server 1 中实现的 CRUD 操作。该微服务同样使用 MongoDB 来读写与产品实体相关的数据。该微服务充当备用微服务实现，如果 Product Server 1 无响应，它可以处理请求。

*   **ProductAlternateServiceProxy**：ProductAlternateServiceProxy 组件是另一个扩展 Product Service 的抽象接口，因此它继承了 Product Server 和 Product Web 接口所需的所有方法。您使用 Feign 客户端的基本配置来注解此接口，以便所有调用都可以被代理到 Product Server 2 微服务。

您将组件交互分为两部分，如图 8-5 和图 8-6 所示。在图 8-5 中，您可以看到来自客户端浏览器的查询命中了 Product Web，即示例中的微服务 1。然后 Product Web 微服务将查询代理到微服务 2，即 Product Server 1。如果 Product Server 1 正在运行，则示例执行将在向客户端返回响应数据后结束，因此此示例的行为将与之前“Feign 客户端”部分中的示例完全相同。相反，您可以选择不启动 Product Server 1，或者如果它已经启动，您可以将其关闭。在这种情况下，Hystrix 将重试到配置的降级服务。

![../images/477630_1_En_8_Chapter/477630_1_En_8_Fig5_HTML.jpg](img/477630_1_En_8_Fig5_HTML.jpg)

图 8-5
Hystrix 检测到服务不可用

假设降级微服务 Product Server 2 已启动，查询将重试以命中此微服务，并且执行将通过图 8-6 所示的正常流程进行。

![../images/477630_1_En_8_Chapter/477630_1_En_8_Fig6_HTML.jpg](img/477630_1_En_8_Fig6_HTML.jpg)

图 8-6
Hystrix 重试备用服务

编写 Hystrix 降级场景代码

您已经了解了如何在 POM 中声明 Feign 客户端。类似地，您可以通过在 POM 中再次声明 Hystrix 依赖项来引入它。本节的所有代码示例都放在文件夹 `ch08\ch08-02` 中。请访问 `pom.xml` 查看 Hystrix 依赖项的显式声明。请参阅清单 8-4。

```
org.springframework.cloud
spring-cloud-starter-hystrix

清单 8-4
Spring Cloud Hystrix 依赖项 (ch08\ch08-02\ProductWeb\pom.xml)
```

接下来，您需要指定 Hystrix 降级。这在 ProductServiceProxy 中指定；请参阅清单 8-5。



```
@FeignClient(name="product-proxy", url = "http://localhost:8080",
fallback = ProductAlternateServerComponent.class)
public interface ProductServiceProxy extends ProductService{
}
代码清单 8-5
Hystrix 降级声明 (ch08\ch08-02\ProductWeb\src\main\java\com\acme\ecom\product\client\ProductServiceProxy.java)
```

此处，你已指示 Hystrix 将 `ProductAlternateServerComponent` 视为降级时重试的组件。现在，让我们看看代码清单 8-6 中提到的 `ProductAlternateServerComponent` 代码。

```
@EnableFeignClients(basePackageClasses = ProductAlternateServiceProxy.class)
@ComponentScan(basePackageClasses = ProductAlternateServiceProxy.class)
@Component
public class ProductAlternateServerComponent implements ProductServiceProxy{
private ProductAlternateServiceProxy productAlternateServiceProxy;
@Autowired
public ProductAlternateServerComponent(
ProductAlternateServiceProxy productAlternateServiceProxy){
this.productAlternateServiceProxy = productAlternateServiceProxy;
}
@Override
public ResponseEntity>> getAllProducts() {
return productAlternateServiceProxy.getAllProducts();
}
@Override
public ResponseEntity> getProduct(
@PathVariable("productId") String productId) {
return productAlternateServiceProxy.getProduct(productId);
}
}
代码清单 8-6
Hystrix 降级实现 (ch08\ch08-02\ProductWeb\src\main/java/com\acme\ecom\product\component\ ProductAlternateServerComponent.java)
```

一种方法是让 `ProductAlternateServerComponent` 以某种本地缓存数据作为降级响应。但是，由于你指定了一个备用微服务作为降级服务，你可以在此处利用它。你还利用了 Feign 客户端将调用委托给 Product Server 2 微服务。对于这个 Feign 客户端，你将使用代码清单 8-7 中的 `ProductAlternateServiceProxy`。

```
@FeignClient(name="product-alternate-proxy", url = "http://localhost:8079")
public interface ProductAlternateServiceProxy extends ProductService{
}
代码清单 8-7
指向备用微服务的 Feign 客户端代理 (ch08\ch08-02\ProductWeb\src\main\java\com\acme\ecom\product\client\ ProductAlternateServiceProxy.java)
```

这里，你期望 Product Server 2 微服务在 `http://localhost:8079` 上可用，它将作为 Product Server 1 微服务的降级服务，而 Product Server 1 预计在 `http://localhost:8080` 上可用。

你现在需要启用 Hystrix 并配置其超时间隔，这可以在以下文件中完成：

```
ch08\ch08-02\ProductWeb\src\main\resources\application.properties:
feign.hystrix.enabled=true
hystrix.command.default.execution.isolation.thread.timeoutInMilliseconds=2000
```

其余代码与你之前在“Feign 客户端”部分看到的类似，因此此处不再重复解释。相反，让我们执行代码。

**构建并测试 Hystrix 降级场景**

演示 Hystrix 降级所需的完整代码保存在 `ch08-02` 文件夹中。确保 MongoDB 已启动并正在运行。你可能需要参考附录 A 来开始使用 MongoDB。

首先，构建并打包 Product Server 1 微服务的可执行文件，然后启动服务器。一个易于执行的实用脚本位于 `ch08\ch08-02\ProductServer\make.bat` 文件夹中。

```
cd ch08\ch08-02\ProductServer
D:\binil\gold\pack03\ch08\ch08-02\ProductServer>make
D:\binil\gold\pack03\ch08\ch08-02\ProductServer>mvn -Dmaven.test.skip=true clean package
```

你可以通过多种方式运行 Spring Boot 应用程序。最直接的方式是通过以下命令执行 JAR 文件：

```
cd ch08\ch08-02\ProductServer
D:\binil\gold\pack03\ch08\ch08-02\ProductServer>run
D:\binil\gold\pack03\ch08\ch08-02\ProductServer>java -jar -Dserver.port=8080 .\target\Ecom-Product-Microservice-0.0.1-SNAPSHOT.jar
```

这些命令将在端口 8080 上启动 Product Server。现在，构建并打包 Product Server 2 微服务的可执行文件，并在一个新的命令窗口中启动服务器：

```
cd ch08\ch08-02\ProductServerAlternate
D:\binil\gold\pack03\ch08\ch08-02\ProductServerAlternate>make
D:\binil\gold\pack03\ch08\ch08-02\ProductServerAlternate>mvn -Dmaven.test.skip=true clean package
D:\binil\gold\pack03\ch08\ch08-02\ProductServerAlternate>run
D:\binil\gold\pack03\ch08\ch08-02\ProductServerAlternate>java -jar -Dserver.port=8079 .\target\Ecom-Product-Microservice-0.0.1-SNAPSHOT.jar
```

接下来，构建并打包 Product Web 微服务的可执行文件，然后启动服务器：

```
cd ch08\ch08-02\ProductWeb
D:\binil\gold\pack03\ch08\ch08-02\ProductWeb>make
D:\binil\gold\pack03\ch08\ch08-02\ProductWeb>mvn -Dmaven.test.skip=true clean package
D:\binil\gold\pack03\ch08\ch08-02\ProductWeb>run
D:\binil\gold\pack03\ch08\ch08-02\ProductWeb>java -jar -Dserver.port=8081 .\target\Ecom-Product-Microservice-0.0.1-SNAPSHOT.jar
```

这些命令将在端口 8081 上启动 Product Web。你可以在 Chrome 浏览器中打开这个 HTML 实用工具：

```
ch08\ch08-02\ProductWeb\src\main\resources\product.html
```

加载后，浏览器客户端将向监听端口 8081 的 Product Web 发送请求，Product Web 将通过 Feign 客户端代理将调用委托给监听端口 8080 的 Product Server 1。

重复刷新浏览器，请求仍会指向 Product Server 1，你可以通过查看其命令窗口中的日志来验证。为了演示 Hystrix 降级，只需关闭 Product Server 1 并刷新浏览器。这次你将看到请求到达 Product Alternate Server。你还可以在 Product Web 微服务控制台中看到相应的日志，如代码清单 8-8 所示。

```
D:\binil\gold\pack03\ch08\ch08-02\ProductWeb>run
D:\binil\gold\pack03\ch08\ch08-02\ProductWeb>java -jar -Dserver.port=8081 .\target\Ecom-Product-Microservice-0.0.1-SNAPSHOT.jar
.   ____          _            __ _ _
/\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
\\/  ___)| |_)| | | | | || (_| |  ) ) ) )
'  |____| .__|_| |_|_| |_\__, | / / / /
=========|_|==============|___/=/_/_/_/
:: Spring Boot ::        (v1.5.4.RELEASE)
2019-02-21 16:53:59 INFO  org.springframework.boot.SpringApplication.logStartupProfileInfo:593 - No active profile set, falling back to default profiles: default
2019-02-21 16:54:10 INFO  com.acme.ecom.product.InitializationComponent.init:61- Start
2019-02-21 16:54:10 DEBUG com.acme.ecom.product.InitializationComponent.init:63- Doing Nothing...
2019-02-21 16:54:10 INFO  com.acme.ecom.product.InitializationComponent.init:65- End
2019-02-21 16:54:13 INFO  org.springframework.boot.StartupInfoLogger.logStarted:57 - Started EcomProductMicroserviceApplication in 25.369 econds (JVM running for 30.007)
2019-02-21 16:56:41 INFO  com.acme.ecom.product.controller.ProductRestController.getAllProducts:84 - Delegating...
2019-02-21 16:56:57 INFO  com.acme.ecom.product.controller.ProductRestController.getAllProducts:84 - Delegating...
2019-02-21 16:56:58 INFO  com.acme.ecom.product.component.ProductAlternateServerComponent.getAllProducts:78 - Delegating...
代码清单 8-8
Hystrix 降级实战
```

**Hystrix 仪表盘**

Hystrix 仪表盘可用于提供系统中断路器的图形概览。在典型的微服务架构中，会有多个微服务进程，在这种情况下，你需要监控应用程序中的多个断路器。为此，你可以使用 Turbine，但此处不进行解释。在本节中，你将了解如何启用 Hystrix 仪表盘。

**重新设计 Hystrix 降级方法**



现在，我们将对“Hystrix 回退”一节中的 Feign 和 Hystrix 设计稍作修改。这样做的目的是，首先演示定义 Hystrix 回退的另一种方式，然后演示 Hystrix 仪表盘。重构后的设计如图 8-7 所示。

![../images/477630_1_En_8_Chapter/477630_1_En_8_Fig7_HTML.jpg](img/477630_1_En_8_Fig7_HTML.jpg)

**图 8-7**
Hystrix 回退的重构设计

在重构后的设计中，所有 Feign 客户端的交互都已从 Controller 移到了 Component 类中。因此，Product Server Component 现在依赖于两个代理，这两个代理将在运行时注入 Feign 客户端代理。

**编写 Hystrix 的新设计**

本节的所有代码示例都放在 `ch08\ch08-03` 文件夹中。Hystrix 仪表盘应用程序是另一个 Spring Boot 应用程序。要启用它，你需要遵循几个步骤。请访问 `pom.xml`，查看清单 8-9 中显式提及的 Hystrix 仪表盘依赖项。

```
org.springframework.cloud
spring-cloud-starter-hystrix-dashboard

org.springframework.boot
spring-boot-starter-actuator

清单 8-9
Hystrix 仪表盘依赖项 (ch08\ch08-03\ProductWeb\pom.xml)
```

第一个依赖项需要通过使用 `@EnableHystrixDashboard` 注解 `@Configuration` 来启用，而后一个依赖项会自动在你的 Web 应用程序微服务中启用所需的指标。

然后，你需要将 `@EnableHystrixDashboard` 添加到你的 Spring Boot `main` 类中，如清单 8-10 所示。

```
@SpringBootApplication
@EnableCircuitBreaker
@EnableHystrixDashboard
@Configuration
public class EcomProductMicroserviceApplication {
public static void main(String[] args) {
SpringApplication.run(EcomProductMicroserviceApplication.class, args);
}
}
清单 8-10
启用 Hystrix 仪表盘 (ch08\ch08-03\ProductWeb\src\main\java\com\acme\ecom\product\EcomProductMicroserviceApplication.java)
```

REST Controller 将所有调用委托给 ProductServerComponent，如清单 8-11 所示。

```
@RestController
public class ProductRestController implements ProductService{
private ProductServerComponent productServerComponent;
@Autowired
public ProductRestController(ProductServerComponent
productServerComponent){
this.productServerComponent = productServerComponent;
}
@RequestMapping(value = "/productsweb", method = RequestMethod.GET ,
produces = {MediaType.APPLICATION_JSON_VALUE})
public ResponseEntity>> getAllProducts() {
return productServerComponent.getAllProducts();
}
@RequestMapping(value = "/productsweb/{productId}",
method = RequestMethod.GET,
produces = MediaType.APPLICATION_JSON_VALUE)
public ResponseEntity> getProduct(
@PathVariable("productId") String productId) {
return productServerComponent.getProduct(productId);
}
}
清单 8-11
REST Controller (ch08\ch08-03\ProductWeb\src\main\java\com\acme\ecom\product\controller\ProductRestController.java)
```

如清单 8-11 所示，ProductRestController 简单地将调用委托给 ProductServerComponent。在 ProductServerComponent 中，你将方法标记为 HystrixCommand，并为每个 HystrixCommand 定义回退方法，如清单 8-12 所示。

```
import com.netflix.hystrix.contrib.javanica.annotation.HystrixCommand;
@EnableFeignClients(basePackageClasses = {ProductServiceProxy.class,
ProductAlternateServiceProxy.class})
@ComponentScan(basePackageClasses = {ProductServiceProxy.class,
ProductAlternateServiceProxy.class})
public class ProductServerComponent implements ProductService{
private ProductServiceProxy productServiceProxy;
private ProductAlternateServiceProxy productAlternateServiceProxy;
@HystrixCommand(fallbackMethod = "getAllTheProducts")
public ResponseEntity>> getAllProducts() {
return productServiceProxy.getAllProducts();
}
@HystrixCommand(fallbackMethod = "getTheProduct")
public ResponseEntity> getProduct(
@PathVariable("productId") String productId) {
return productServiceProxy.getProduct(productId);
}
public ResponseEntity>> getAllTheProducts() {
return productAlternateServiceProxy.getAllProducts();
}
public ResponseEntity> getTheProduct(
@PathVariable("productId") String productId) {
return productAlternateServiceProxy.getProduct(productId);
}
}
清单 8-12
Hystrix 命令 (ch08\ch08-03\ProductWeb\src\main\java\com\acme\ecom\product\component\ProductServerComponent.java)
```

如你所见，如果 Product Server 1 宕机，对 `productServiceProxy.getAllProducts()` 的调用将会失败，并且由于你已将 `getAllTheProducts()` 声明为回退方法，因此将通过对 productAlternateServiceProxy 的调用来进行重试。在发生错误（对 productServiceProxy 服务的调用失败或超时）或电路断开时快速失败的情况下，Hystrix 会使用回退方法。

**构建并测试 Hystrix 回退场景**

演示 Hystrix 回退所需的完整代码保存在 `ch08-03` 文件夹中。确保 MongoDB 已启动并正在运行。然后，你可以按以下顺序构建、打包和运行这三个微服务：

```
cd ch08\ch08-03\ProductServer
D:\binil\gold\pack03\ch08\ch08-03\ProductServer>make
D:\binil\gold\pack03\ch08\ch08-03\ProductServer>mvn -Dmaven.test.skip=true clean package
D:\binil\gold\pack03\ch08\ch08-03\ProductServer>run
D:\binil\gold\pack03\ch08\ch08-03\ProductServer>java -jar -Dserver.port=8080 .\target\Ecom-Product-Microservice-0.0.1-SNAPSHOT.jar
cd ch08\ch08-03\ProductServerAlternate
D:\binil\gold\pack03\ch08\ch08-03\ProductServerAlternate>make
D:\binil\gold\pack03\ch08\ch08-03\ProductServerAlternate>mvn -Dmaven.test.skip=true clean package
D:\binil\gold\pack03\ch08\ch08-03\ProductServerAlternate>run
D:\binil\gold\pack03\ch08\ch08-03\ProductServerAlternate>java -jar -Dserver.port=8079 .\target\Ecom-Product-Microservice-0.0.1-SNAPSHOT.jar
cd ch08\ch08-03\ProductWeb
D:\binil\gold\pack03\ch08\ch08-03\ProductWeb>make
D:\binil\gold\pack03\ch08\ch08-03\ProductWeb>mvn -Dmaven.test.skip=true clean package
D:\binil\gold\pack03\ch08\ch08-03\ProductWeb>run
D:\binil\gold\pack03\ch08\ch08-03\ProductWeb>java -jar -Dserver.port=8081 .\target\Ecom-Product-Microservice-0.0.1-SNAPSHOT.jar
```

最后一个命令将在端口 8081 上启动 Product Web。你可以打开这个 HTML 工具，最好在 Chrome 浏览器中打开：

```
ch08\ch08-03\ProductWeb\src\main\resources\product.html
```

加载后，浏览器客户端将向监听在 8081 端口的 Product Web 发起请求，Product Web 将通过 Feign 客户端代理，将调用委托给监听在 8080 端口的 Product Server 1。重复刷新浏览器也会将请求指向 Product Server 1，你可以通过查看其命令窗口中发生的日志来验证这一点。为了演示 Hystrix 回退，只需关闭 Product Server 1 并刷新浏览器。这次你将看到请求到达 Product Server 2。你也可以在 Product Server 微服务控制台中看到相应的日志。

**检查 Hystrix 仪表盘**

Hystrix 仪表盘（图 8-8）在你的环境中可通过以下 URL 访问：

![../images/477630_1_En_8_Chapter/477630_1_En_8_Fig8_HTML.jpg](img/477630_1_En_8_Fig8_HTML.jpg)



图 8-8
Hystrix 仪表盘

```
http://localhost:8081/hystrix
```

Hystrix 仪表盘会要求输入 Hystrix 流的 URL。每个启用了 Hystrix 的应用程序都会生成一个流，其中持续写入所有断路器的状态。该 URL 的格式为 `http://application-node:port/hystrix.stream`，在你的场景中，URL 是 `http://localhost:8081/hystrix.stream`。该流可以通过仪表盘进行监控，如图 8-9 所示。

![../images/477630_1_En_8_Chapter/477630_1_En_8_Fig9_HTML.jpg](img/477630_1_En_8_Fig9_HTML.jpg)

图 8-9
Hystrix 仪表盘中的流监控

Ribbon：客户端负载均衡器
在了解了 Feign 客户端和 Hystrix 降级机制之后，现在是时候介绍 Spring Cloud 中的下一个重要组件了：Ribbon。Ribbon 是一个客户端负载均衡器，它让你能够对 HTTP 和 TCP 客户端的行为进行额外控制。Feign 已经使用了 Ribbon，因此理解 Ribbon 至关重要。
负载均衡将传入的流量分发到两个或多个微服务之间。它使你能够在微服务应用中实现容错。负载均衡旨在优化资源使用、最大化吞吐量、最小化响应时间，并避免任何单个微服务过载。使用同一服务的多个微服务实例进行负载均衡，而不是使用单个实例，可能会以冗余为代价来提高可靠性和可用性。
Ribbon 提供了多种配置选项，例如连接超时、重试、重试算法（指数退避、有界退避）、负载均衡、容错、支持多种协议（HTTP、TCP、UDP）、支持异步和响应式模型、缓存以及批处理。

设计一个 Ribbon 客户端场景

你将使用与 Feign 客户端部分相同的设计。但是，你将增强 Feign 客户端，使其在内部利用 Ribbon。该设计如图 8-10 所示。

![../images/477630_1_En_8_Chapter/477630_1_En_8_Fig10_HTML.jpg](img/477630_1_En_8_Fig10_HTML.jpg)

图 8-10
使用 Ribbon 的设计

你将使用与本章前面部分相同的 Product Server 微服务模块。在前面的部分中，你还使用了 Product Server Alternate 微服务模块；但是，在本节中，你将只使用一个模块，即 Product Server 微服务模块，但将运行它的两个实例。这一点很重要，因为我还想演示，只要你处理好了其他设计方面（如无状态性、幂等性等），一个微服务可以实例化多个（更确切地说是复制）以实现水平扩展。

使用 Ribbon 客户端的代码

本节的所有代码示例都放在 `ch08\ch08-04` 文件夹中。请访问 `pom.xml` 查看显式声明的 Ribbon 依赖项。参见代码清单 8-13。

```
org.springframework.cloud
spring-cloud-starter-ribbon

代码清单 8-13
Ribbon 依赖项 (ch08\ch08-04\ProductWeb\pom.xml)
```

Ribbon 的核心概念是命名客户端。每个负载均衡器都是一个组件集合的一部分，这些组件协同工作以按需联系远程服务器，并且该集合有一个你作为应用程序开发者赋予它的名称（例如，使用 `@FeignClient` 注解），如代码清单 8-14 所示。

```
@FeignClient(name="product-proxy")
public interface ProductServiceProxy extends ProductService{
}
代码清单 8-14
Ribbon 中的命名客户端 (ch08\ch08-04\ProductWeb\src\main\java\com\acme\ecom\product\client\ProductServiceProxy.java)
```

现在你需要为 Ribbon 提供配置值，这将在 `ch08\ch08-04\ProductWeb\src\main\resources\application.properties` 中完成：

```
product-proxy.ribbon.listOfServers=localhost:8080,localhost:8081
```

就是这样，这将完成所有必需的底层连接工作。

构建并测试 Ribbon 客户端

演示 Ribbon 所需的完整代码保存在 `ch08-04` 文件夹内。确保 MongoDB 已启动并正在运行。然后，你可以按以下顺序构建、打包并运行这三个微服务：

```
cd ch08\ch08-04\ProductServer
D:\binil\gold\pack03\ch08\ch08-04\ProductServer>make
D:\binil\gold\pack03\ch08\ch08-04\ProductServer>mvn -Dmaven.test.skip=true clean package
D:\binil\gold\pack03\ch08\ch08-04\ProductServer>run1
D:\binil\gold\pack03\ch08\ch08-04\ProductServer>java -jar -Dserver.port=8080 .\target\Ecom-Product-Microservice-0.0.1-SNAPSHOT.jar
cd ch08\ch08-04\ProductServer
D:\binil\gold\pack03\ch08\ch08-04\ProductServer>run2
D:\binil\gold\pack03\ch08\ch08-04\ProductServer>java -jar -Dserver.port=8081 .\target\Ecom-Product-Microservice-0.0.1-SNAPSHOT.jar
cd ch08\ch08-03\ProductWeb
D:\binil\gold\pack03\ch08\ch08-04\ProductWeb>make
D:\binil\gold\pack03\ch08\ch08-04\ProductWeb>mvn -Dmaven.test.skip=true clean package
D:\binil\gold\pack03\ch08\ch08-04\ProductWeb>run
D:\binil\gold\pack03\ch08\ch08-04\ProductWeb>java -jar -Dserver.port=8082 .\target\Ecom-Product-Microservice-0.0.1-SNAPSHOT.jar
```

最后一条命令将在端口 8082 上启动 Product Web。你应该已经注意到，Product Server 微服务的两个实例正在端口 8080 和 8081 上运行。这就解释了为什么你使用了以下配置：

```
product-proxy.ribbon.listOfServers=localhost:8080,localhost:8081
```

现在，你可以在 Chrome 浏览器中打开这个 HTML 工具：

```
ch08\ch08-04\ProductWeb\src\main\resources\product.html
```

浏览器客户端在加载自身时，会向监听在端口 8082 的 Product Web 发起一个请求，该请求将通过 Feign 客户端代理，将调用委托给 Product Server 微服务的任意一个实例。
重复刷新浏览器，将会在 Product Web 微服务向 Product Server 微服务的两个实例之间进行负载均衡，你可以通过查看 Product Server 微服务命令窗口中发生的日志来验证这一点。如果你关闭其中一个 Product Server 微服务并刷新浏览器，你将看到请求将始终发送到正在运行的另一个 Product Server 实例。如果你重新启动之前关闭的 Product Server 微服务，Ribbon 负载均衡将再次生效，在 Product Web 微服务向 Product Server 微服务的两个实例之间交替进行调用。

Eureka：服务注册中心
Eureka 是一个基于 REST 的注册中心服务，主要用于公有云部署，尤其是在 AWS 云中。使用 Eureka，你可以定位微服务以实现负载均衡和故障转移。Eureka 是 Spring Cloud 的服务注册中心，它充当了微服务的电话簿。每个微服务都会向服务注册中心自我注册，告知注册中心它的位置（主机、节点名称、端口等）以及可能还有其他服务特定的元数据。Eureka 包含两部分：Eureka Server 和一个客户端组件 Eureka Client，后者使得与注册中心服务的交互更加容易。该客户端还内置了一个负载均衡器，可以执行基本的轮询负载均衡。

像 Eureka 这样的服务注册中心还有另一个特性，它为服务消费者提供了一层或多层间接寻址。这意味着服务消费者可以使用逻辑服务名称进行注册中心查找，并且这个逻辑服务名称可能映射到不同的实际部署服务，其物理地址条目与上述逻辑名称相对应。这使服务消费者与物理部署环境中实际服务细节或任何其他变化隔离开来。在“Ribbon”部分解释的示例中，你可能已经注意到服务 URL 的硬编码值，例如：

```
localhost:8080,localhost:8081
```



这种做法并不理想。DNS（域名系统）可用于解决类似问题；然而，在企业应用中面对数百个微服务时，DNS 相对笨重。此外，你也不希望将微服务的服务器细节暴露在外围或 DMZ（非军事区）之外。在基于微服务的企业应用中，服务消费者通常是其他微服务。因此，这些微服务需要询问诸如服务拓扑（“是否有可用的‘产品服务’？如果有，在哪里？”）和服务能力（“你能处理 A、B 和 C 吗？”）之类的问题。这正是 Eureka 和 Consul 等服务注册中心发挥作用的地方。Eureka 对通信协议或方法没有任何限制，因此你可以使用 Eureka 来采用 thrift、http(s) 或任何其他 RPC 机制等协议。

当已有许多软件和硬件负载均衡器（包括 AWS Elastic Load Balancer 和 AWS Route 53）时，你可能仍会怀疑是否需要 Eureka。AWS Elastic Load Balancer 用于暴露边缘服务，这些服务通常连接最终用户的网络流量，而 Eureka 则满足中间层负载均衡的需求，或者用我们的术语来说，是微服务注册和负载均衡。Route 53 类似于 DNS 服务，你可以在其中托管 DNS 记录，甚至适用于非 AWS 数据中心，因此它带有传统基于 DNS 的负载均衡解决方案的相关缺点：你的流量仍可能被路由到不健康甚至不存在的服务器，这在 AWS 为自动扩展配置的公共云部署中非常典型。由于你应该倾向于让微服务无状态（非粘性），Eureka 提供了更好的可扩展性模型，因为你的微服务能够抵御负载均衡器（Eureka）的故障——关于可用服务器的信息会缓存在客户端。这确实需要少量内存，但这种增强的弹性并不会过多地影响整体资源考量。

设计一个启用 Eureka 的场景

你将通过引入几个新组件来修改设计，如图 8-11 所示。这里你用 `RestTemplate` 替换了 `ProductServiceProxy`。这意味着 Product Web 微服务现在将使用 `RestTemplate` 来消费 Product Server 微服务的任何实例。`RestTemplate` 简化了与 HTTP 服务器的通信，第 7 章的“开发 RESTful Web 服务”部分已经解释了如何使用 `RestTemplate` 与 `RestController` 通信。你将在此处进行扩展。使用 `RestTemplate` 的另一个意图是，当 `RestTemplate` 在类路径上并且定义了 `LoadBalancerClient` bean 时，你可以通过自动配置的 `RestTemplate` 间接使用 Ribbon，从而在之前的 Ribbon 示例基础上进行构建。

![../images/477630_1_En_8_Chapter/477630_1_En_8_Fig11_HTML.jpg](img/477630_1_En_8_Fig11_HTML.jpg)

图 8-11
使用 Eureka 的设计

通过启用 Eureka 注册中心，所有微服务在启动时都会自行注册到注册中心。此外，在之前的示例中，你看到了如何通过多次实例化同一个 Product Server 微服务来实现冗余。你将遵循相同的模式来处理 Eureka，以便在服务注册中心级别也实现冗余。

使用 Eureka 的代码

本节的所有代码示例都放在文件夹 `ch08\ch08-05` 中。访问 `pom.xml` 可以看到显式引用了 Eureka Server 依赖项。参见清单 8-15。

```
org.springframework.cloud
spring-cloud-starter-eureka-server

清单 8-15
Eureka 依赖项 (ch08\ch08-05\Eureka\pom.xml)
```

接下来，你需要 Spring Cloud 的 `@EnableEurekaServer` 来启动一个注册中心，其他微服务可以与之通信。这是在常规的 Spring Boot 应用程序 `main` 类中完成的，只需添加一个注解即可启用服务注册中心。参见清单 8-16。

```
@EnableEurekaServer
@SpringBootApplication
public class EurekaRegistryApplication {
public static void main(String[] args) {
SpringApplication.run(EurekaRegistryApplication.class, args);
}
}
清单 8-16
启用 Eureka Server (ch08\ch08-05\Eureka\src\main\java\com\acme\ecom\infra\EurekaRegistryApplication.java)
```

当 Eureka 启动时，它会尝试注册自身，因此你需要禁用它；参见清单 8-17。

```
spring.application.name=eureka-registry
server.port=8761
eureka.client.registerWithEureka=false
eureka.client.fetchRegistry=true
eureka.client.server.waitTimeInMsWhenSyncEmpty=0
eureka.client.serviceUrl.defaultZone=http://localhost:8761/eureka/,http://localhost:8762/eureka/
eureka.server.enableSelfPreservation=false
清单 8-17
Eureka 配置 (ch08\ch08-05\Eureka\src\main\resources\application.properties)
```

如果你想使用这个单一的属性文件启动多个 Eureka 注册中心实例，你需要至少覆盖前两个参数。在以下两个脚本中执行此操作：

```
ch08\ch08-05\Eureka\run1.bat
java -jar -Dserver.port=8761 -Dspring.application.name=eureka-registry1 .\target\Ecom-Product-Microservice-0.0.1-SNAPSHOT.jar
ch08\ch08-05\Eureka\run2.bat
java -jar -Dserver.port=8762 -Dspring.application.name=eureka-registry2 .\target\Ecom-Product-Microservice-0.0.1-SNAPSHOT.jar
```

由于你处理的是基于 HAL 的 JSON 数据，因此需要配置要在 Product Web 微服务中使用的 REST 模板，以调用 Product Server 微服务中的调用，从而处理 HAL 格式的数据。参见清单 8-18。

```
@Configuration
public class RestTemplateConfiguration{
@LoadBalanced
@Bean
public RestTemplate restTemplate() {
return new RestTemplate(getRequiredMessageConvertors());
}
private List<HttpMessageConverter getRequiredMessageConvertors(){
ObjectMapper mapper = new ObjectMapper();
mapper.configure(DeserializationFeature.FAIL_ON_UNKNOWN_PROPERTIES,
false);
mapper.registerModule(new Jackson2HalModule());
MappingJackson2HttpMessageConverter converter =
new MappingJackson2HttpMessageConverter();
converter.setSupportedMediaTypes(MediaType.parseMediaTypes(
"application/hal+json, application/json"));
converter.setObjectMapper(mapper);
return Arrays.asList(converter);
}
}
清单 8-18
处理 HAL JSON 格式的 Rest Template 配置 (ch08\ch08-05\ProductWeb\src\main\java\com\acme\ecom\product\controller\RestTemplateConfiguration.java)
```

Product Server 微服务和 Product Web 微服务必须向注册中心注册自身，并使用 Spring Cloud DiscoveryClient 抽象来查询注册中心以获取自己的主机和端口。你可以使用 `@EnableEurekaClient` 来激活 Netflix Eureka DiscoveryClient 实现。`EnableEurekaClient` 是一个便利注解，供客户端启用 Eureka 发现配置（具体而言）。此注解会开启发现功能，并让自动配置在可用时找到 Eureka 类。参见清单 8-19。

```
@SpringBootApplication
@EnableEurekaClient
public class EcomProductMicroserviceApplication {
public static void main(String[] args) {
SpringApplication.run(EcomProductMicroserviceApplication.class, args);
}
}
清单 8-19
启用 Eureka Client (ch08\ch08-05\ProductServer\src\main\java\com\acme\ecom\product\EcomProductMicroserviceApplication.java)
```

访问 `pom.xml` 可以看到显式引用了 Eureka 依赖项。参见清单 8-20。

```
org.springframework.cloud
spring-cloud-starter-eureka



**清单 8-20**
Eureka 依赖项 (ch08\ch08-05\ProductServer\pom.xml)
```

你在第 7 章的“开发 RESTful Web 服务”一节中，已经看到过使用 `ProductRepository` 从数据库检索产品数据的 `ProductController` 代码，因此这里不再解释产品服务器微服务中的 `ProductController`。但是，你需要检查产品 Web 微服务所使用的 `ProductController` 的完整代码，因为这段代码演示了如何完全拆解一个基于 HATEOAS 的 HTTP 响应，然后重新组装它，以创建一个基于 HATEOAS 的 HTTP 响应并返回。清单 8-21 展示了 `getAllProducts()` 的代码。

```
@CrossOrigin
@RestController
public class ProductRestController implements ProductService{
@Autowired
RestTemplate restTemplate;
private static String PRODUCT_SERVICE_URL =
"http://product-service/products";
@Autowired
public ProductRestController(RestTemplate restTemplate){
this.restTemplate = restTemplate;
}
@RequestMapping(value = "/productsweb",
method = RequestMethod.GET,
produces = {MediaType.APPLICATION_JSON_VALUE})
public ResponseEntity>> getAllProducts() {
ParameterizedTypeReference>
responseTypeRef =
new ParameterizedTypeReference>() {};
ResponseEntity> responseEntity =
restTemplate.exchange(PRODUCT_SERVICE_URL, HttpMethod.GET,
(HttpEntity) null, responseTypeRef);
PagedResources resources = responseEntity.getBody();
Collection products = resources.getContent();
List productList = new ArrayList(products);
Link links[] =
{linkTo(methodOn(ProductRestController.class).getAllProducts()).
withSelfRel(),linkTo(methodOn(ProductRestController.class).
getAllProducts()).withRel("getAllProducts")};
if(products.isEmpty()){
return new ResponseEntity>>(
HttpStatus.NOT_FOUND);
}
List> list = new ArrayList> ();
for(Product product:products){
list.add(new Resource(product,
linkTo(methodOn(ProductRestController.class).
getProduct(product.getId())).withSelfRel()));
}
Resources> productResponse =
new Resources>(list, links) ;
return new ResponseEntity>>(
productResponse, HttpStatus.OK);
}
}
**清单 8-21**
拆解与组装 getAllProducts 的 HAL JSON 数据 (ch08\ch08-05\ProductWeb\src\main\java\com\acme\ecom\product\controller\ProductRestController.java)
```

清单 8-21 展示了如何拆解你逐个检索到的 Product 实体，进一步将它们丰富为 HAL 格式的数据，并组装起来以响应客户端。清单 8-22 展示了单个产品检索场景的类似代码。

```
public class ProductRestController implements ProductService{
@RequestMapping(value = "/productsweb/{id}", method =
RequestMethod.GET, produces = MediaType.APPLICATION_JSON_VALUE)
public ResponseEntity> getProduct(
@PathVariable("id") String id) {
Product product = restTemplate.getForObject(
PRODUCT_SERVICE_URL + "/" + id, Product.class);
if (product == null) {
return new ResponseEntity>(
HttpStatus.NOT_FOUND);
}
Resource productResponse = new Resource(
product, linkTo(methodOn(ProductRestController.class).
getProduct(product.getId())).withSelfRel());
return new ResponseEntity>(
productResponse, HttpStatus.OK);
}
}
**清单 8-22**
拆解与组装 getProduct 的 HAL JSON 数据
```

其余代码与你之前在示例中看到的或多或少相同，因此不再赘述。

**构建并测试 Eureka 示例**
演示 Eureka 注册中心所需的完整代码位于 `ch08-05` 文件夹中。确保 MongoDB 已启动并运行。然后，你可以按以下顺序构建、打包并运行不同的微服务：

首先启动 Eureka 注册中心微服务：

```
cd ch08\ch08-05\Eureka
D:\binil\gold\pack03\ch08\ch08-05\Eureka>make
D:\binil\gold\pack03\ch08\ch08-05\Eureka>mvn -Dmaven.test.skip=true clean package
D:\binil\gold\pack03\ch08\ch08-05\Eureka>run1
D:\binil\gold\pack03\ch08\ch08-05\Eureka>java -jar -Dserver.port=8761 -Dspring.application.name=eureka-registry1 .\target\Ecom-Product-Microservice-0.0.1-SNAPSHOT.jar
```

当第一个 Eureka 实例启动时，控制台会显示一个堆栈跟踪错误，因为注册中心找不到要连接的副本节点。在生产环境中，你通常需要运行多个注册中心实例。出于示例目的，你将再启动一个 Eureka 实例。

```
D:\binil\gold\pack03\ch08\ch08-05\Eureka>run2
D:\binil\gold\pack03\ch08\ch08-05\Eureka>java -jar -Dserver.port=8762 -Dspring.application.name=eureka-registry2 .\target\Ecom-Product-Microservice-0.0.1-SNAPSHOT.jar
```

注意，在 Eureka 控制台窗口中，两个实例会相互同步。你可能需要等待几秒钟才能观察到这种同步。另外请注意，这种延迟在本示例演示的不同阶段都会出现，因为注册中心的注册、查找和同步都存在一些延迟（你可以通过调整配置来控制这些延迟）。

既然注册中心已经设置完毕，接下来可以启动应用微服务：

```
cd ch08\ch08-05\ProductServer
D:\binil\gold\pack03\ch08\ch08-05\ProductServer>make
D:\binil\gold\pack03\ch08\ch08-05/ProductServer>mvn -Dmaven.test.skip=true clean package
D:/binil/gold/pack03/ch08/ch08-05/ProductServer>run1
D:/binil/gold/pack03/ch08/ch08-05/ProductServer>java -Dserver.port=8080 -Dspring.application.name=product-service -Deureka.client.serviceUrl.defaultZone=http://localhost:8761/eureka/,http://localhost:8762/eureka/ -jar ./target/Ecom-Product-Microservice-0.0.1-SNAPSHOT.jar
```

清单 8-23 展示了当你启动微服务时，Eureka 控制台中应该出现的情况。



```
D:\binil\gold\pack03\ch08\ch08-05\Eureka>run1
D:\binil\gold\pack03\ch08\ch08-05\Eureka>java -jar -Dserver.port=8761 -Dspring.application.name=eureka-registry1 .\target\Ecom-Product-Microservice-0.0.1-SNAPSHOT.jar
2019-02-21 19:09:38.836  INFO 16672 --- [           main] s.c.a.AnnotationConfigApplicationContext : Refreshing org.springframework.context.annotation.AnnotationConfigApplicationContext@28ba21f3: startup date [Thu Feb 21 19:09:38 IST 2019]; root of context hierarchy
.   ____          _            __ _ _
/\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
\\/  ___)| |_)| | | | | || (_| |  ) ) ) )
'  |____| .__|_| |_|_| |_\__, | / / / /
=========|_|==============|___/=/_/_/_/
:: Spring Boot ::        (v1.5.4.RELEASE)
2019-02-21 19:10:59.615  INFO 16672 --- [freshExecutor-0] com.netflix.discovery.DiscoveryClient    : Getting all instance registry info from the eureka server
2019-02-21 19:10:59.633  INFO 16672 --- [freshExecutor-0] com.netflix.discovery.DiscoveryClient    : The response status is 200
2019-02-21 19:10:59.646  INFO 16672 --- [a-EvictionTimer] c.n.e.registry.AbstractInstanceRegistry  : Running the evict task with compensationTime 0ms
2019-02-21 19:11:08.318  INFO 16672 --- [nio-8761-exec-4] c.n.e.registry.AbstractInstanceRegistry  : Registered instance PRODUCT-SERVICE/tiger:product-service:8080 with status UP (replication=true)
2019-02-21 19:11:29.675  INFO 16672 --- [freshExecutor-0] com.netflix.discovery.DiscoveryClient    : The response status is 200
2019-02-21 19:11:41.187  INFO 16672 --- [nio-8761-exec-8] c.n.e.registry.AbstractInstanceRegistry  : Registered instance PRODUCT-SERVICE/tiger:product-service:8081 with status UP (replication=true)
2019-02-21 19:11:59.647  INFO 16672 --- [a-EvictionTimer] c.n.e.registry.AbstractInstanceRegistry  : Running the evict task with compensationTime 0ms
cd ch08\ch08-05\ProductServer
D:\binil\gold\pack03\ch08\ch08-05\ProductServer>run2
D:\binil\gold\pack03\ch08\ch08-05\ProductServer>java -Dserver.port=8081 -Dspring.application.name=product-service -Deureka.client.serviceUrl.defaultZone=http://localhost:8761/eureka/,http://localhost:8762/eureka/ -jar .\target\Ecom-Product-Microservice-0.0.1-SNAPSHOT.jar
清单 8-23
微服务向 Eureka 注册
```

请注意，与 Eureka 实例的情况不同，你启动的所有 Product Server 微服务实例都使用了相同的应用名称。这是为了向注册中心明确表示，这两个实例实际上指向的是同一个微服务。这将有助于 Eureka 在收到对该微服务的查找请求时，路由到其中任意一个实例。

接下来，启动 Product Web 微服务：

```
cd ch08\ch08-05\ProductWeb
D:\binil\gold\pack03\ch08\ch08-05\ProductWeb>make
D:\binil\gold\pack03\ch08\ch08-05\ProductWeb>mvn -Dmaven.test.skip=true clean package
D:\binil\gold/pack03/ch08/ch08-05/ProductWeb>run
D:/binil/gold/pack03/ch08/ch08-05/ProductWeb>java -Dserver.port=8082 -Dspring.application.name=product-web -Deureka.client.serviceUrl.defaultZone=http://localhost:8761/eureka/,http://localhost:8762/eureka/ -jar ./target/Ecom-Product-Microservice-0.0.1-SNAPSHOT.jar
```

最后一条命令将在端口 8082 上启动 Product Web。

你可以通过 Web 浏览器访问以下运行注册服务的 URL 来查看 Eureka 控制台：

```
http://localhost:8761/
http://localhost:8762/
```

参见图 8-12。

![../images/477630_1_En_8_Chapter/477630_1_En_8_Fig12_HTML.jpg](img/477630_1_En_8_Fig12_HTML.jpg)

图 8-12
Eureka 控制台

现在，你最好在 Chrome 浏览器中打开这个 HTML 工具：`ch08\ch08-05\ProductWeb\src\main\resources\product.html`。浏览器客户端在加载时，会向监听在 8082 端口的 Product Web 发送一个请求，该请求将委托给 Product Server 微服务的任意一个实例。重复刷新浏览器，Product Web 微服务对 Product Server 微服务实例的请求将实现负载均衡，你可以通过查看 Product Server 微服务命令窗口中发生的日志来验证这一点。如果你关闭其中一个 Product Server 微服务并刷新浏览器，你会看到请求将始终发送到另一个正在运行的 Product Server 实例。这是因为你在 RestTemplate 上使用了 `@LoadBalanced` 注解，该注解用于从 Product Web 微服务向 Product Server 微服务发送请求，如清单 8-18 所示。如果你重新启动之前关闭的 Product Server 微服务，Ribbon 负载均衡将再次生效，在 Product Web 微服务对 Product Server 微服务的两个实例之间交替发送调用。类似地，你也可以通过关闭其中一个 Eureka 微服务来进行测试。Eureka 客户端被设计为能够处理 Eureka 集群中一个或多个 Eureka 对等节点的故障。由于 Eureka 客户端本地缓存了注册信息，即使所有可用的 Eureka 对等节点都宕机，它们也能相当良好地运行。

引导你的引导服务器
现在你应该明白，在基于微服务的企业应用中，每个服务实例都可以向 Eureka 注册中心注册，Eureka 注册中心实例本身也是如此。但有一个例外，那就是 API 网关，你将在下一节中看到；在此之前，请忽略这个例外。我已经说过，Eureka 注册中心是任何感兴趣的服务消费者的服务目录，因此，如果服务消费者能够通过它想要消费的服务名称找到注册中心，注册中心就会提供如何访问该服务的信息（主机、端口等）。那么关键问题是，任何服务消费者最初是如何访问注册中心的？换句话说，Eureka 注册中心将帮助任何微服务进行引导并自我宣告。但是，Eureka 注册中心本身如何宣告自己呢？引导服务器如何引导自身？！
你需要一组标准的、易于识别的 Eureka 服务器地址。一种机制是使用内部 DNS 或类似服务。然而，这种固定的（IP）地址仅在传统的局域网环境中有效。一旦我们谈到在 AWS 这样的公有云中部署，情况就变了。在像 AWS 这样的云环境中，实例来来去去。这意味着你不能用标准的主机名或 IP 地址来固定 Eureka 服务器。必须使用像 AWS EC2 弹性 IP 地址这样的机制。弹性 IP 地址是专为动态云计算设计的静态 IPv4 地址。使用弹性 IP 地址，你可以通过快速将地址重新映射到账户中的另一个实例来掩盖微服务的故障。集群中的每个 Eureka 服务器都需要一个弹性 IP。一旦你用弹性 IP 地址列表配置了 Eureka 服务器，Eureka 服务器就会处理查找未使用的弹性 IP 并在启动时将其绑定到自身的麻烦。



Zuul，API 网关
任何服务都需要通过地址来访问——更准确地说，是通过一个众所周知的地址。像 [`www.google.com`](http://www.google.com) 或 [`www.apple.com`](http://www.apple.com) 这样的地址就是例子。同样，当你托管一个企业应用时，它也需要有一个地址，具体来说就是一个主页 URL。在浏览器中输入这样的 URL，会将你的请求引导至网站中的着陆资源。通常，这些名称或众所周知的 URL 会通过 DNS 解析，并指向一个物理 IP 或一组物理 IP 的组合。当出于可扩展性原因在集群或服务器场中部署应用时，你还需要一个负载均衡器，它将充当反向代理，请求首先到达这里，然后被负载均衡到服务器场中的任何一个实例。Apache HTTP 服务器和 F5 就是类似的设备。AWS ELB（弹性负载均衡器）是一种针对在 AWS 云场景中暴露给最终用户流量的 Web 服务的负载均衡解决方案。在所有这些情况下，一旦你公布了公开暴露的 IP，从那时起，企业应用的内部细节（包括网络和部署细节）就对外界隐藏了。这是一种推荐模式，在你的微服务应用中也会遵循同样的做法。

引导 URL
在上一节中，你看到每个微服务在启动时都可以向 Eureka 注册中心进行自我注册，这样消费者就可以通过注册中心被引导到这些服务。为了发现注册中心的位置，你将使用众所周知且固定的地址来访问注册中心。这些注册中心只包含指向内部微服务的路由映射。外部可访问的资源和地址通常不会在注册中心中建立索引；相反，你需要通过其他方式暴露它们。DNS 就是其中一种机制。但你也知道，来自这些可公开访问资源的后续查询或请求（例如来自主页和其他页面的许多 AJAX 调用）需要访问这些微服务。你需要一种机制来实现这一点。这就是 API 网关发挥作用的地方。一组微服务可以通过 API 网关从外围暴露给客户端，而其余你不想暴露的微服务则可以继续保持不暴露状态。因此，你的 API 网关将成为那些客户端用来访问公开暴露的微服务的 URL 的引导机制。

图 8-13 描述了如何在本地部署中设置 API 网关。第 4 章的“网格应用和服务架构”部分讨论了网格应用和服务架构，我将扩展 MASA 的概念以容纳 API 网关。在每个企业应用中，API 网关处需要处理许多问题，安全性就是其中之一。所有这些问题都可以在 API 网关处解决，因为网关将充当企业应用架构的单一前门。

![../images/477630_1_En_8_Chapter/477630_1_En_8_Fig13_HTML.jpg](img/477630_1_En_8_Fig13_HTML.jpg)

图 8-13
Zuul，本地部署的 API 网关

使用 API 网关时，需要注意以下几点：

*   只有公共服务才绑定到 API 网关。这意味着所有需要从企业 DMZ 外部寻址的微服务 API 都将绑定到 API 网关。
*   对于不一定需要从外部访问的微服务，则无需在 API 网关处暴露它们。

在图 8-13 中，你可以看到所有微服务都在 Eureka 中注册。然后，所有需要从外部访问的微服务也都绑定到了 API 网关。然而，不一定需要从外部访问的微服务（例如微服务 2）则没有绑定到 API 网关。下一个值得注意的方面是，API 网关本身应该可以从外部寻址。所有应用也是如此，因为应用需要能够被客户端设备直接访问。在图 8-13 中，你可以看到应用域，我们通常将 DMZ 外部可寻址和可访问的所有组件放置在此域中。通常，此域由 Web 应用、移动应用、面向物联网设备的代理等组成。将应用和 API 网关注册到合适的外部命名服务使这成为可能，而 DNS 是实现此目的的一种方式。任何此类可公开访问的资源都将在公共 DNS 中注册，因此它们的地址将通过 DNS 查找来解析。成功的 DNS 查找将为客户端设备提供资源的实际 URL，以便客户端设备可以请求该资源并获取响应，然后进行渲染。如图 8-14 所示。

![../images/477630_1_En_8_Chapter/477630_1_En_8_Fig14_HTML.jpg](img/477630_1_En_8_Fig14_HTML.jpg)

图 8-14
引导微服务应用的 URL

一旦你收到应用内容（在传统 Web 应用上下文中是主页）并在设备上渲染后，从那时起，客户端设备到服务器的所有其余请求都可以路由到一个或多个上下文根 URL。这意味着来自客户端设备应用的所有后续请求都将命中 API 网关，如图 8-15 所示。这在图中用标签进行了标记。由于 API 网关也是公开暴露的，因此通过像 DNS 这样的命名服务来解析其 IP 是合理的，或者客户端应用本身也可以记住 API 网关的 IP 并直接访问（标签 7）。API 网关将首先执行注册中心查找（标签 9），然后将请求路由到目标微服务（标签 10）。Zuul 默认使用 Ribbon 通过服务发现（例如在你的场景中是 Eureka）来定位要转发请求的实例。因此，如果你的服务在 Eureka 中注册，Zuul 也可以处理实例查找并继续转发负载均衡的请求。

让我们将场景再扩展一层。假设微服务 1 必须内部调用微服务 2 以访问某些功能。图 8-13 显示微服务 2 没有绑定到 API 网关，因为它是一个始终内部可访问的微服务；然而，它也在 Eureka 中注册了一个名称。然后，微服务 1 可以使用 Feign 客户端或类似机制执行 Eureka 查找，并执行基于 Ribbon 的负载均衡，以便将调用路由到微服务 2（标签 12）。

![../images/477630_1_En_8_Chapter/477630_1_En_8_Fig15_HTML.jpg](img/477630_1_En_8_Fig15_HTML.jpg)

图 8-15
从客户端应用到微服务的请求

你需要认识到，这里解释的部署架构是典型的架构，并且可以根据你的企业需求有多种变体。

设计一个启用 Zuul 的场景
你将通过引入 Zuul API 网关来修改图 8-13 中的设计；修改后的设计如图 8-16 所示。流程如上一节所述；但是，这里我不会介绍 DNS 等的所有复杂性。让我们来看看这个流程。



客户端设备（在您的场景中即浏览器）将用于渲染 `product.html`（标签 1）。当 `product.html` 加载到浏览器后，它会向 Product Web 微服务发起请求。在本示例中，为简化起见，您不会将 Product Web 微服务绑定到 API 网关，而仅将 Product Server 微服务绑定到 API 网关。因此，客户端设备到 Product Web 微服务的请求是直接访问，不经过 API 网关（标签 3）。现在，Product Web 微服务需要将请求委托给 Product Server 微服务，为此，它将通过 API 网关路由请求（标签 4）。API 网关将执行注册表查找（标签 5），并将托管 Product Server 微服务的服务器详细信息返回给 Product Web 微服务中的 Ribbon 客户端。Product Web 微服务获取此信息后，将请求负载均衡到 Product Server 微服务的任一实例（标签 6），因为您将托管两个 Product Server 微服务实例。

![../images/477630_1_En_8_Chapter/477630_1_En_8_Fig16_HTML.jpg](img/477630_1_En_8_Fig16_HTML.jpg)

**图 8-16**
使用 Zuul 的设计

**使用 Zuul 的代码**

本节的所有代码示例均位于 `ch08\ch08-06` 文件夹中。请访问 `pom.xml` 查看 Zuul 依赖项的显式声明。参见代码清单 8-24。

```
org.springframework.cloud
spring-cloud-starter-zuul

org.springframework.cloud
spring-cloud-starter-eureka

代码清单 8-24
Zuul 依赖项 (ch08\ch08-06\ProductApiZuul\pom.xml)
```

请注意，Zuul 本身就是一个微服务，因此它需要像其他普通微服务一样向注册表注册自己。因此，Zuul 也需要一个 Eureka 依赖项，如代码清单 8-25 所示。

接下来，您需要 Spring Cloud 的 `@EnableZuulProxy` 来启动一个 API 网关，其他微服务可以绑定到该网关。这是在常规的 Spring Boot 应用程序主类中完成的，只需添加一个注解即可启用 Zuul 网关。

```
@EnableZuulProxy
@EnableDiscoveryClient
@SpringBootApplication
public class ProductServerApiApplication {
public static void main(String[] args) {
SpringApplication.run(ProductServerApiApplication.class, args);
}
}
代码清单 8-25
启用 Zuul (ch08\ch08-06\ProductApiZuul\src\main\java\com\acme\ecom\infra\ProductServerApiApplication.java)
```

现在，您需要在 API 网关处定义路由，这将在 `application.yml` 中完成。参见代码清单 8-26。

```
spring:
application:
name: product-service-api
server:
port: 8082
zuul:
routes:
product-api:
path: /api/**
service-id: product-service
eureka:
client:
serviceUrl:
defaultZone: http://localhost:8761/eureka/
hystrix:
command:
default:
execution:
isolation:
thread:
timeoutInMilliseconds: 2000
代码清单 8-26
在 Zuul 网关中定义路由 (ch08\ch08-06\ProductApiZuul\src\main\resources\application.yml)
```

现在，Zuul 将以名称 `product-service-api` 注册到 Eureka。此外，任何到达 Zuul 的、模式为 `/api/**` 的 URL 都将被路由到 `product-service`。

Product Server 微服务的代码与您在“使用 Eureka 的代码”部分中看到的代码类似，不同之处在于您移除了 MongoDB 依赖项，而是使用简单的内存表示来存储产品数据，以使示例更简单。然而，Product Web 微服务的代码需要解释，因为正是在这里，您查找 Zuul API 网关以将请求委托给 Product Server 微服务。参见代码清单 8-27。

```
@RestController
public class ProductRestController implements ProductService{
@Autowired
RestTemplate restTemplate;
private static String PRODUCT_SERVICE_URL =
"http://product-service-api/api/products";
@Autowired
public ProductRestController(RestTemplate restTemplate){
this.restTemplate = restTemplate;
}
@RequestMapping(value = "/productsweb",
method = RequestMethod.GET ,
produces = {MediaType.APPLICATION_JSON_VALUE})
public ResponseEntity>> getAllProducts() {
ResponseEntity> responseEntity =
restTemplate.exchange(PRODUCT_SERVICE_URL, HttpMethod.GET,
(HttpEntity) null, responseTypeRef);
// 其他代码在此处...
}
}
代码清单 8-27
使用 API 网关调用其他微服务 (ch08\ch08-06\ProductWeb\src\main\java\com\acme\ecom\product\controller\ProductRestController.java)
```

所有其他代码与您在“使用 Eureka 的代码”部分中看到的类似；但变化在于 URL。URL [ `http://product-service-api/api/products` ](https://www.product-service-api/api/products) 中的 `product-service-api` 部分指向 Zuul API 网关的地址，因此调用将首先到达网关。在 API 网关中，您已配置任何到达 Zuul 的、模式为 `/api/**` 的 URL 都将被路由到 `product-service`，因此上述完整 URL 将被转换为 Product Server 微服务中的 products 端点。

**构建并测试 Zuul 示例**

演示 Eureka 注册表所需的完整代码位于 `ch08-06` 文件夹中。此示例不需要 MongoDB。您可以按以下顺序构建、打包和运行不同的微服务。首先启动 Eureka 注册表微服务：

```
cd ch08\ch08-06\Eureka
D:\binil\gold\pack03\ch08\ch08-06\Eureka>make
D:\binil\gold\pack03\ch08\ch08-06\Eureka>mvn -Dmaven.test.skip=true clean package
D:\binil\gold\pack03\ch08\ch08-06\Eureka>run
D:\binil\gold\pack03\ch08\ch08-06\Eureka>java -jar -Dserver.port=8761 -Dspring.application.name=eureka-registry -Deureka.client.registerWithEureka=false -Deureka.client.fetchRegistry=true -Deureka.client.server.waitTimeInMsWhenSyncEmpty=0 -Deureka.client.serviceUrl.defaultZone=http://localhost:8761/eureka/ -Deureka.server.enableSelfPreservation=false .\target\Ecom-Product-Microservice-0.0.1-SNAPSHOT.jar
```

接下来启动 Zuul API 网关：

```
D:\binil\gold\pack03\ch08\ch08-06\ProductApiZuul>make
D:\binil\gold\pack03\ch08\ch08-06\ProductApiZuul>mvn -Dmaven.test.skip=true clean package
D:\binil\gold\pack03\ch08\ch08-06\ProductApiZuul>run
D:\binil\gold\pack03\ch08\ch08-06\ProductApiZuul>java -Dserver.port=8082 -Dspring.application.name=product-service-api -Deureka.client.serviceUrl.defaultZone=http://localhost:8761/eureka/ -jar ./target/Ecom-Product-Microservice-0.0.1-SNAPSHOT.jar
```

由于注册表和 API 网关现已设置完毕，接下来您可以启动应用程序微服务：

```
cd ch08\ch08-06\ProductServer
D:\binil\gold\pack03\ch08\ch08-06\ProductServer>make
D:\binil\gold\pack03\ch08\ch08-06\ProductServer>mvn -Dmaven.test.skip=true clean package
D:\binil\gold\pack03\ch08\ch08-06\ProductServer>run1
D:\binil\gold\pack03\ch08\ch08-06\ProductServer>java -Dserver.port=8080 -Dspring.application.name=product-service -Deureka.client.serviceUrl.defaultZone=http://localhost:8761/eureka/ -jar .\target\Ecom-Product-Microservice-0.0.1-SNAPSHOT.jar
cd ch08\ch08-06\ProductServer
D:\binil\gold\pack03\ch08\ch08-06\ProductServer>run2
D:\binil\gold\pack03\ch08\ch08-06\ProductServer>java -Dserver.port=8081 -Dspring.application.name=product-service -Deureka.client.serviceUrl.defaultZone=http://localhost:8761/eureka/ -jar .\target\Ecom-Product-Microservice-0.0.1-SNAPSHOT.jar
```

接下来，启动 Product Web 微服务：



```
cd ch08\ch08-06\ProductWeb
D:\binil\gold\pack03\ch08\ch08-06\ProductWeb>make
D:\binil\gold\pack03\ch08\ch08-06\ProductWeb>mvn -Dmaven.test.skip=true clean package
D:\binil\gold\pack03\ch08\ch08-06\ProductWeb>run
D:\binil\gold\pack03\ch08\ch08-06\ProductWeb>java -Dserver.port=8084 -Dspring.application.name=product-web -Deureka.client.serviceUrl.defaultZone=http://localhost:8761/eureka/ -jar .\target\Ecom-Product-Microservice-0.0.1-SNAPSHOT.jar
```

最后一条命令会在 8084 端口启动 Product Web。

你可以通过 Web 浏览器访问以下 URL 来查看 Eureka 控制台，该 URL 指向运行注册服务的地址：`http://localhost:8761/`。
参见图 8-17。

![../images/477630_1_En_8_Chapter/477630_1_En_8_Fig17_HTML.jpg](img/477630_1_En_8_Fig17_HTML.jpg)

图 8-17
Eureka
控制台

现在，你可以打开这个 HTML 工具，最好在 Chrome 浏览器中打开：
`ch08\ch08-06\ProductWeb\src\main\resources\product.html`。
加载完成后，浏览器客户端会向监听在 8084 端口的 Product Web 发起请求，Product Web 会将调用委托给 Product Server 微服务的任意实例。Product Web 微服务现在需要将请求委托给 Product Server 微服务，为此，它将通过 API 网关路由请求。
API 网关将执行注册表查找，并将托管 Product Server 微服务的服务器详细信息返回给 Product Web 微服务中的 Ribbon 客户端。Product Web 微服务获取此信息，并将请求负载均衡到 Product Server 微服务的任意一个实例。

配置服务器
外部化配置参数是微服务展现的一个关键特性。这对于应用程序参数和基础设施参数都是如此。当存在许多微服务时，在外部维护配置参数是一项重要的任务，而 Spring Cloud 的 Config Server 在此处为你提供了帮助。
Spring Config Server 在版本控制的仓库（如 SVN 或 Git）中维护配置参数。同样，仓库可以是本地的或远程的，并且可以采用单节点或多节点配置以实现高可用性。由于配置参数可以进行版本控制，然后由生产服务器直接引用，因此可以完全避免因错误值等导致的运行时错误场景。

设计一个配置场景

你将在示例中引入 Config Server，而无需对其他微服务进行太多更改。在你的设计中，你将仅对 Product Server 微服务使用 Config Server，尽管它可以用于所有其他微服务。如图 8-18 所示，Product Server 微服务利用 Spring Cloud Config Server 来管理其一个配置参数的值。

![../images/477630_1_En_8_Chapter/477630_1_En_8_Fig18_HTML.jpg](img/477630_1_En_8_Fig18_HTML.jpg)

图 8-18
Config Server 场景的设计

使用 Config Server 的代码

本节的所有代码示例都在文件夹 `ch08/ch08-07` 中。访问 `pom.xml` 可以看到对 Config Server 依赖的显式引用。参见清单 8-28。

```
org.springframework.cloud
spring-cloud-config-server

清单 8-28
Config Server 依赖 (ch08\ch08-07\ConfigServer\pom.xml)
```

接下来，你需要将 `@EnableConfigServer` 添加到 `Application` 类中，如清单 8-29 所示。

```
@SpringBootApplication
@EnableConfigServer
public class EcomProductConfigApplication {
public static void main(String[] args) {
SpringApplication.run(EcomProductConfigApplication.class, args);
}
}
清单 8-29
启用 Config Server (ch08\ch08-07\ConfigServer\src\main\java\com\acme\ecom\product\EcomProductConfigApplication.java)
```

接下来，配置 Config Server 本身以指向 GIT URL。参见清单 8-30。

```
server:
port: 8888
spring:
cloud:
config:
server:
git:
uri: file://D:/binil/gold/pack03/ch08/ch08-07/ConfigServer/config-repo
清单 8-30
Spring Cloud Config Server 配置 (ch08\ch08-07\ConfigServer\src\main\resources\bootstrap.yml)
```

这里，由于端口 8888 是 Config Server 的默认端口，即使没有显式指定 server.port，Config Server 也会绑定到 8888。接下来，你提供 GIT URL。你需要将所有不同微服务的配置文件放置在此仓库中。

接下来，你需要让你的微服务能够访问 Config Server。访问清单 8-31 中显示的 Maven 依赖。

```
org.springframework.boot
spring-boot-starter-actuator

org.springframework.cloud
spring-cloud-starter-config

清单 8-31
Config Server 客户端 (ch08\ch08-07\ProductServer\pom.xml)
```

Product Server 微服务将充当 Config Server 的客户端。这是通过 spring-cloud-starter-config 启用的。你还需要注意 actuator 依赖，当 Config Server 中的参数发生更改时，需要它来刷新配置参数。接下来，检查微服务配置文件，如清单 8-32 所示。

```
server:
port: 8081
spring:
application:
name: productservice
cloud:
config:
server:
uri: http://localhost:8888
management:
security:
enabled: false
清单 8-32
Product Server 微服务配置 (ch08\ch08-07\ProductServer\src\main\resources\application.yml)
```

重要的配置参数是

```
spring.application.name=productservice
spring.cloud.config.uri=http://localhost:8888
```

这里，productservice 是赋予 Product Server 微服务的逻辑名称，它也将被视为其服务 ID。Config Server 将在仓库中查找 `productservice.yml` 以解析任何应用程序配置参数。参见清单 8-33。

```
page:
size: 3
清单 8-33
Product Server 配置参数 (ch08\ch08-07\ConfigServer\config-repo\productservice.yml)
```

你将在下一节中看到清单 8-33 中配置参数的相关性。现在，你可以继续查看清单 8-34 中显示的 Product Server 微服务代码。

```
@RestController
@RefreshScope
public class ProductRestController {
@Value("${page.size}")
private int size = 0;
// 其他代码在此处...
}
清单 8-34
Config Server 客户端 (ch08\ch08-07\ProductServer\src\main\java\com\acme\ecom\product\controller\ProductRestController.java)
```

为了演示参数的集中配置及其更改的传播，你引入了一个特定于应用程序的参数，名为 `size`，它通过名称 `page.size` 从外部化配置中引用。在应用程序代码中，你使用此参数来控制当客户端访问产品时返回的产品行数。

构建并测试 Config Server
演示 Config Server 所需的完整代码位于文件夹 `ch08-07` 中。
此示例也不需要 MongoDB。你可以按以下顺序构建、打包和运行不同的微服务。

首先启动 Config Server 微服务：

```
cd ch08\ch08-07\ConfigServer
D:\binil\gold\pack03\ch08\ch08-07\ConfigServer>make
D:\binil\gold\pack03\ch08\ch08-07\ConfigServer>mvn -Dmaven.test.skip=true clean package
D:\binil\gold\pack03\ch08\ch08-07\ConfigServer>run
D:\binil\gold\pack03\ch08\ch08-07\ConfigServer>java -Dserver.port=8888 -Dspring.application.name=productservice -jar .\target\Ecom-Product-Microservice-0.0.1-SNAPSHOT.jar
```



一旦配置服务器启动，清单 8-33 中列出的 `productservice.yml` 配置参数便可通过输入 `http://localhost:8888/productservice/default` 进行查看。

URL 的第一部分是微服务名称。在你的案例中，微服务名称是 `productservice`。微服务名称是赋予应用程序的逻辑名称，它通过 Spring Boot（产品服务器）应用程序的 `application.yml` 中的 [ `spring.application.name` ](http://spring.application.name) 属性来设置。每个应用程序都应具有唯一的名称。配置服务器将使用此名称来解析并从配置服务器仓库中获取相应的 `.yml` 或 `.properties` 文件。应用程序名称也被称为服务 ID。请参见图 8-19。

![../images/477630_1_En_8_Chapter/477630_1_En_8_Fig19_HTML.jpg](img/477630_1_En_8_Fig19_HTML.jpg)

图 8-19
查看配置服务器

接下来启动应用程序微服务：

```
cd ch08\ch08-07\ProductServer
D:\binil\gold\pack03\ch08\ch08-07\ProductServer>make
D:\binil\gold\pack03\ch08\ch08-07\ProductServer>mvn -Dmaven.test.skip=true clean package
D:\binil\gold\pack03\ch08\ch08-07\ProductServer>run
D:\binil\gold\pack03\ch08\ch08-07\ProductServer>java -Dserver.port=8081 -Dspring.application.name=productservice -jar .\target\Ecom-Product-Microservice-0.0.1-SNAPSHOT.jar
cd ch08\ch08-07\ProductWeb
D:\binil\gold\pack03\ch08\ch08-07\ProductWeb>make
D:\binil\gold\pack03\ch08\ch08-07\ProductWeb>mvn -Dmaven.test.skip=true clean package
D:\binil\gold\pack03\ch08\ch08-07\ProductWeb>run
D:\binil\gold\pack03\ch08\ch08-07\ProductWeb>java -Dserver.port=8080 -Dspring.application.name=productweb -jar .\target\Ecom-Product-Microservice-0.0.1-SNAPSHOT.jar
```

打开此 HTML 工具，最好在 Chrome 浏览器中打开：`ch08\ch08-07\ProductWeb\src\main\resources\product.html`。浏览器客户端在加载自身时，会向监听 8080 端口的 Product Web 发起请求，Product Web 会将调用委托给 Product Server 微服务。你可以在客户端浏览器中看到与 `productservice.yml` 中配置数量相同的 Product 行。

作为下一步，更改 `productservice.yml` 文件中 `page.size` 的值（从 `3` 改为 `2` 或 `4`）并保存文件。你应该能够通过在浏览器中输入配置服务器 URL `http://localhost:8888/productservice/default` 来查看此更改。

现在你可以刷新浏览器客户端。但是，此更改可能不会反映在 Product Server 微服务中。为了强制 Product Server 微服务重新加载配置参数，请调用 Product Server 微服务的 `/refresh` 端点。这是执行器的刷新端点。以下命令将向 `/refresh` 端点发送一个空的 POST 请求：

```
curl –d {} localhost:8081/refresh
```

或者，你也可以使用 Postman 发送一个空的 POST 请求。如果你再次刷新浏览器客户端，你将看到更改已生效。`/refresh` 端点将刷新 Product Server 微服务本地缓存的配置参数，并从配置服务器重新加载^(¹³)新值。

总结
在本章中，你探索了 Spring Cloud 的主要构建模块。由于 Spring Cloud 提供了简单、易用且对 Spring 友好的 API，并且它建立在 Spring 的“约定优于配置”方法之上，因此 Spring Cloud 默认了所有配置，帮助你快速上手。在后续章节中，许多 Spring Cloud 功能将被用于构建你的电子商务示例应用程序，因此本章提供的基础知识对于你连接多个微服务并可视化整体架构至关重要。正如你可能已经推测到的，许多 Spring Cloud 组件从一开始就考虑到了故障安全和高度可用的特性。随着微服务场景下外部架构复杂性的增加，这一点尤为重要。在下一章中，你将进行端到端分析，探讨各种关键的高可用性方面和特性。

脚注

9. 高可用性与微服务

高可用性（HA）描述了软件服务可用的时间段，以及这些系统响应用户请求所需的时间。消除基础设施中可能导致服务中断的单点故障是设计高可用性系统的关键。在每一层和每个阶段复制或设计冗余组件对于实现端到端的高可用性至关重要。第 4 章的“外部架构视角”部分讨论了微服务架构的外部架构问题。上一章中的各个部分和示例为你提供了具体的解决方案，以解决第 4 章中提到的部分问题。你了解了如何启动一个微服务的多个实例。在上一章中，你还启动了多个 Eureka 实例。在本章中，你将进一步探讨高可用性的一般细节，特别是微服务的高可用性。你还将看到一个全面的示例，演示微服务架构的端到端高可用性。

在本章中，你将学习以下内容：

*   定义和衡量高可用性

*   从 DNS 查找到存储备份，在每一层分解高可用性

*   在微服务背景下重新审视高可用性方面

*   使用微服务和 Spring Cloud 实现高可用性的代码演示

高可用性
高可用性规划涉及识别业务连续性所必需的服务。应识别构成每个服务的组件，并列出这些系统可能的故障点。应为每个组件建立故障容忍基线，并设计故障转移策略。

衡量高可用性
软件可用性以年度正常运行时间的百分比表示。以几乎无法实现的 100% 可用性理想状态为基准，最高级别的服务可用性目标被认为是“五个九”，即 99.999% 的可用性。

停机时间（通常以每年中断分钟数表示）是表示可用性的另一种方式。表 9-1 显示了不同可用性级别的比较。

表 9-1
可用性级别

**可用性** | **不可用性** | **五个九** | **每年停机时间** |

| --- | --- | --- | --- | --- | --- | --- | --- | --- |

0.9 | 0.1 | 1 | 36 天 |

0.99 | 0.01 | 2 | 87.7 小时 |

0.999 | 0.001 | 3 | 8 小时 46 分钟 |

0.9999 | 0.0001 | 4 | 52.5 分钟 |

0.99999 | 0.00001 | 5 | 5 分钟 16 秒 |

0.999999 | 0.000001 | 6 | 32 秒 |



我不会解释这些级别，也不会探讨达到这些可用性级别的具体方法，因为我们已经描述和实践它们数十年了，并且有专门的书籍^(¹⁴)和参考资料^(¹⁵)进行说明。我们的讨论将只提供足够的背景知识，以便您将其与本次微服务高可用性（HA）的讨论联系起来。

**设定高可用性基线**

高可用性基线可以根据两个参数来定义：

*   **恢复时间目标（RTO）**：业务在系统不可用的情况下能够正常运行的时间长度。
*   **恢复点目标（RPO）**：系统恢复后，数据可能丢失的时间跨度。

基于 RTO 和 RPO 基线目标，并进一步根据可用预算、可访问的技术、业务关键性以及可用的技术专长，软件组件的设计需要达到所需的可用性级别。

**分解高可用性**
如果您的基础设施由负载均衡路由器后面的两个相同且冗余的 Web 服务器组成，那么来自客户端设备的流量可以平均分配到这些 Web 服务器之间。但是，如果其中一台服务器宕机，负载均衡器会将所有后续流量重定向到仍在线的服务器。这是一个简单的机制，如果仔细扩展，它可以用于为不同元素设计高可用性。互联网、内部网络、网络设备和软件组件都是端到端高可用性链中的基本环节。您将了解我们讨论所需的基本环节，这样本章末尾演示的概念和示例对您来说才有意义。同时，在本章中，我将在本地或私有数据中心的背景下解释相关方面。在第 16 章中，当我描述高级高可用性模式时，我将在公有云的背景下进行解释，以便您对如何在私有云和公有云背景下看待高可用性有一个更广阔的视角。

**DNS 冗余**
第 8 章中的“引导 URL”一节介绍了微服务架构中的域名系统（DNS）。由于 DNS 通过将人类友好的计算机主机名转换为 IP 地址来充当互联网的电话簿，因此它充当了大多数通过互联网进行的软件相关操作的引导机制。从这个点开始理解可用性是有意义的。这意味着您需要理解 DNS 冗余的机制。

DNS 中断可能由配置错误、基础设施故障或 DDoS 攻击引起。DNS 冗余是针对此类场景的故障安全解决方案或备份机制。如果 DNS 名称服务器因请求过多而过载，或者由于上述任何原因而宕机或响应缓慢，这会在 DNS 网络中造成延迟，使解析过程变慢，最终导致大多数用户无法访问 DNS 服务器。您可以通过增加可用名称服务器的数量或选择范围，为单服务器架构引入 DNS 冗余，以便有更多的名称服务器响应 DNS 查询。图 9-1 显示了一个典型的冗余 DNS 设置，其中每个 DNS 服务器都有能力充当响应 DNS 查询的主服务器。

![../images/477630_1_En_9_Chapter/477630_1_En_9_Fig1_HTML.jpg](img/477630_1_En_9_Fig1_HTML.jpg)

**图 9-1**
DNS 冗余

现在让我们更详细地探讨这一点；见图 9-2。在设置 DNS 冗余时，服务器可以设置为主-从模式，或者主-主模式。^(¹⁶) 维护多个 DNS 提供商并保持它们同步是一个复杂的过程，但它能确保更高的可用性，同时不损害 DNS 提供商支持的先进 DNS 功能。在为您的多 DNS 网络评估多个 DNS 提供商时，请考虑提供商的全球存在点（PoP），并确保这些位于不同网络上的提供商可以通过 API 无缝集成。

![../images/477630_1_En_9_Chapter/477630_1_En_9_Fig2_HTML.jpg](img/477630_1_En_9_Fig2_HTML.jpg)

**图 9-2**
DNS 冗余的工作原理

**DNS 负载均衡**
一旦 DNS 冗余得到处理，您需要考虑 DNS 负载均衡。DNS 负载均衡器会审查一个服务器列表，并依次向每个服务器发送一个连接。当 DNS 发生变更时，必须在 ISP（互联网服务提供商）层面进行记录。ISP 仅在每个 TTL（生存时间）周期内刷新一次其 DNS 缓存。这意味着在缓存更新之前，ISP 不知道发生的任何变更，并继续将流量路由到错误的服务器。

除了本地负载均衡，您还可以选择全局负载均衡。全局 DNS 负载均衡将流量分布到多个数据中心，以提高性能和可用性；然而，这同样取决于您跨地域的托管策略。如果您的托管是跨地域的“双活”模式，您可以选择全局负载均衡。有些解决方案可以监控任意时刻地理上分散站点之间的连接质量，以及实际请求者的地理位置，并据此路由流量。见图 9-3。

![../images/477630_1_En_9_Chapter/477630_1_En_9_Fig3_HTML.jpg](img/477630_1_En_9_Fig3_HTML.jpg)

**图 9-3**
全局服务器负载均衡

**ISP 冗余**
ISP 为您提供从您的企业到互联网的管道（或连接）（图 9-4）。如果这个管道或路径中断，将影响您的网络连接，进而影响您的企业应用程序可用性。这正是 ISP 冗余可以为您提供帮助的地方。

![../images/477630_1_En_9_Chapter/477630_1_En_9_Fig4_HTML.jpg](img/477630_1_En_9_Fig4_HTML.jpg)

**图 9-4**
企业到 ISP 的连接



边界网关协议（BGP）是实现互联网连接冗余的关键协议之一。当你的网络连接到多个互联网服务提供商（ISP）时，这被称为多宿主连接。多宿主连接^(¹⁷)提供了冗余和网络优化功能。它会选择能够为资源提供最佳可用路径的 ISP。让我们从高层次来详细了解其细节。

你需要从互联网上的自治系统（AS）开始理解。自治系统是一个由连接的互联网协议（IP）路由前缀组成的集合，由一个或多个网络运营商代表单个管理实体或域进行控制，并向互联网呈现统一且明确的路由策略。ISP 必须拥有官方注册的自治系统编号（ASN）。一个 ISP 可能支持多个自治系统；然而，互联网只看到该 ISP 的路由策略。ASN 由互联网号码分配局（IANA）按块分配给区域互联网注册管理机构（RIR）。然后，相应的 RIR 在其指定区域内将 AS 编号分配给各个实体。

自治系统内的网络使用内部网关协议（IGP）相互通信路由信息。一个自治系统使用边界网关协议与其他自治系统共享路由信息。未来，BGP 预计将被 OSI 域间路由协议（IDRP）取代。

运营商中立的数据中心由多个 ISP 提供服务。拥有两个 ISP 时，你可以从彼此获得足够的带宽，以便在其中一个 ISP 出现故障时，仍能处理你的流量而不会出现任何速度下降。拥有三个或更多 ISP 时，你需要在以下两种能力之间做出选择：一个 ISP 故障时仍能无降速运行，以及除一个 ISP 外其余全部故障时仍能保持运行。

图 9-5 展示了连接到多个 ISP 的示意图。请记住，该示意图是典型情况；可能存在多种替代方案。冗余路径、交换机、接入端口和路由器将确保，如果任何路径或组件发生故障，仍然存在一条不同的可用路径。

![../images/477630_1_En_9_Chapter/477630_1_En_9_Fig5_HTML.jpg](img/477630_1_En_9_Fig5_HTML.jpg)

图 9-5
通过多个本地路由器实现到 ISP 的多宿主负载分担

**应用架构冗余**

如前文所述，面向互联网的边界路由器直接与 ISP 建立对等连接。用于防范蠕虫、病毒、拒绝服务（DoS）流量等的入侵防御设备，以及网络安全设备（WSA）等设备，都位于网络的这一部分。防火墙通过实施和执行有状态防火墙规则以及应用层检查，来保护通过边缘的功能。DMZ 为基于 HTTP 的应用程序和电子商务应用提供主要安全保护。

在图 9-6 中，你可以看到路由器和交换机交叉连接以提供冗余路径。类似地，应用层交换机为跨 Web 服务器的负载分担和故障切换提供支持。再次注意，此处的图示是典型情况，会根据企业需求而有所不同。

![../images/477630_1_En_9_Chapter/477630_1_En_9_Fig6_HTML.jpg](img/477630_1_En_9_Fig6_HTML.jpg)

图 9-6
高可用性应用部署架构

部署在 DMZ 中的面向公众的服务显示为单台服务器；然而，它们也可以冗余部署。数据存储层中的核心服务器同样是冗余的。

**数据与存储冗余**

数据存储在块级存储阵列（如 SAN，即存储区域网络）或基于文件的阵列（也称为 NAS 或网络附加存储阵列）上。它们提供不同级别的冗余，因此如果阵列中的单个组件（无论是磁盘驱动器、控制器、SAN 或 LAN 接入端口等）发生故障，都不会完全破坏设置，用户仍然可以访问数据。基于 x86 架构的服务器硬件通常配置有到 SAN 和以太网网络的冗余连接。到异构设备的此类连接通过使用双端口适配器或使用多个（单端口）适配器来实现。适配器的选择取决于服务器中的可用空间（即 I/O 扩展插槽的数量）以及所需的硬件冗余级别。简而言之，基础设施的设置方式使其为网络和存储资源使用了冗余路径、交换机和接入端口。

图 9-7 揭示了路径是冗余的，并且不允许存在单点故障。如果以太网或 SAN 路径之一发生故障，仍然可以通过一条幸存的路径保持连接。在这种故障情况下，故障切换将由某种类型的多路径或故障切换软件触发。

![../images/477630_1_En_9_Chapter/477630_1_En_9_Fig7_HTML.jpg](img/477630_1_En_9_Fig7_HTML.jpg)

图 9-7
非融合存储网络

操作系统将拥有 NIC（网络接口卡）驱动程序，这些驱动程序允许它们与多个网卡关联，从而能够跨两条路径实现网络流量的负载均衡。如果物理网卡、线缆或其他网络组件发生故障，相同的驱动程序也会从故障中进行故障切换。
此外，存储阵列供应商提供位于操作系统和存储适配器之间的软件，该软件可以跨可用的主机总线适配器（HBA）进行负载均衡，如图 9-7 所示。当 HBA、路径或设备发生故障时，将发生到冗余组件的故障切换。

从图 9-7 中，你可以想象需要如此多的网络端口、存储端口、适配器和线缆来实现冗余。拥有如此多此类组件的大型数据中心将增加数据中心的建设以及运营成本。这就是融合网络发挥作用的地方，SAN 和以太网流量可以在单一类型的线缆上运行。FCoE（以太网光纤通道）就是一个例子，它使用支持 FCoE 的适配器，这些适配器可以同时传输以太网和光纤通道流量。图 9-8 展示了这一改进。

![../images/477630_1_En_9_Chapter/477630_1_En_9_Fig8_HTML.jpg](img/477630_1_En_9_Fig8_HTML.jpg)

图 9-8
融合存储网络

图 9-8 清楚地表明，要实现与图 9-7 中相同或更好的冗余和故障切换能力，你需要的网络端口、存储端口、适配器和线缆更少。



图 9-9 展示了如何跨地域复制数据中心。图 9-9 中所示的 SAN1 和 SAN2 也可以部署在遥远的地理位置。这样，其中一个站点可以被视为灾难恢复站点。供应商解决方案可以提供同步和异步复制功能，以及块级增量复制和精细快照功能。结合了光纤通道和 IP 特性的 IP 光纤通道（FCIP）用于远距离连接分布式 SAN。FCIP 封装光纤通道并通过 TCP 套接字传输，因此它是一种隧道协议，因为它通过 IP 网络在地理上分离的 SAN 之间建立透明的点对点连接。FCIP 依赖 TCP/IP 服务，通过局域网、城域网或广域网建立远程 SAN 之间的连接。

![../images/477630_1_En_9_Chapter/477630_1_En_9_Fig9_HTML.jpg](img/477630_1_En_9_Fig9_HTML.jpg)

图 9-9
SAN 复制

最后但同样重要的是，来自存储层的已存储字节会被归档到非持久性介质中，并可传输到辅助站点或灾难恢复站点，这同样可以由多个服务提供商完成，以提高可靠性。

高可用微服务

现在，您可以扩展前面“应用架构冗余”部分中解释的应用部署架构，以适应前面解释的网格应用和服务架构。如图 9-10 所示。请注意，在 MASA 风格中，应用是从客户端设备执行的。它们可以是 Web 应用、原生移动应用、物联网应用等。但是，将它们表示在防火墙后面的核心数据中心内也并无不妥，大多数具有服务器端执行能力的 Web 应用都是这种情况。如果应用是移动应用，通常它们由应用商店（如 Playstore 等）表示。此外，如果应用没有任何服务器端执行能力，而仅在客户端设备中执行，那么它们也可以托管在 DMZ 或更靠近边界的位置，以便于分发到客户端设备。在这种情况下，安全问题和其他类似问题将由 API 层进一步拦截，您已在上一章中了解过 API 层。

![../images/477630_1_En_9_Chapter/477630_1_En_9_Fig10_HTML.jpg](img/477630_1_En_9_Fig10_HTML.jpg)

图 9-10
MASA 风格部署的高可用性

图 9-10 中需要注意的另一点是，应用和服务可以冗余托管，尤其是在它们被设计为微服务时。如果这些概念难以消化，请不要担心；在本章末尾的工作示例中，一切都会得到澄清。

高可用 Spring Cloud 微服务演示
上一节从部署角度解释了微服务架构如何融入高可用参考架构模型。在本节中，您将看到一个真实场景，您将在其中模拟从微服务角度已经讨论过的 HA 的许多方面。您将仅模拟微服务场景所必需的 HA 构建块；但是，您不会尝试演示本章前面解释的基础设施级 HA 的其他通用原则，因为它们已经过充分验证，即使应用于微服务上下文，也并无不同。

设计高可用微服务场景

为了演示微服务 HA 场景，您将使用在第 8 章中使用的 Eureka 和 Zuul 组件。您将设计的场景如图 9-11 所示。

![../images/477630_1_En_9_Chapter/477630_1_En_9_Fig11_HTML.jpg](img/477630_1_En_9_Fig11_HTML.jpg)

图 9-11
演示微服务的高可用性

您拥有 Zuul 和 Eureka 各两个实例。接下来，您将应用组件划分为三个不同的微服务：

*   **Web 应用 μ 服务**：这是 MASA 风格中的应用组件。在您的示例中，它仅托管一个 HTML 页面，因此一旦浏览器从服务器加载此 HTML，之后从 HTML 应用到服务器端的所有交互都将通过 API 网关进行。

*   **产品 Web 服务器 μ 服务**：您模拟此微服务接收来自防火墙外部客户端的所有请求，因此此组件不会执行任何主要的数据操作。相反，它会将调用委托给产品数据服务器 μ 服务。

*   **产品数据服务器 μ 服务**：在此 μ 服务中，您将模拟 CRUD 操作。为保持演示简单，您不会针对任何数据库执行实际的 CRUD 操作。相反，您将使用内存数据存储来模拟它们。

上述所有微服务也将被实例化两次，以便在任何时间点都具有冗余。
接下来，您将使用一个 Nginx 服务器实例来模拟反向代理。此反向代理将配置为负载均衡模式，以便它可以对下一层组件进行负载均衡。

关于 Nginx 代理配置，您需要注意两个方面：

*   **Web 应用 μ 服务**：Web 应用 μ 服务是必须首先下载到客户端设备的应用。因此，应用的引导地址必须将此应用下载并渲染到客户端浏览器中。简而言之，此组件必须能够被客户端设备直接寻址和访问，因此 Nginx 会将来自客户端的请求直接反向代理到此 μ 服务。

*   **Zuul API 网关**：一旦 Web 应用 μ 服务在客户端设备中渲染完成，此后该应用对服务器的任何后续请求都必须仅通过 API 网关进行。因此，Zuul API 网关实例也应在 Nginx 反向代理中进行配置。

我已从演示中移除了 DNS、防火墙等，因为它们的配置与典型部署并无不同，因此您的网络架构师将在这方面为您提供帮助。

演示高可用微服务的代码

本节的所有代码示例都在文件夹 `ch09\ch09-01` 中。请注意，您是在上一章示例的基础上构建的，特别是第 8 章中的 Eureka 和 Zuul 示例，因此在尝试运行此示例之前，您应该先了解这些示例的概况。但是，即使您跳过了这些示例，您仍然应该能够运行并理解此示例。见图 9-12。

![../images/477630_1_En_9_Chapter/477630_1_En_9_Fig12_HTML.jpg](img/477630_1_En_9_Fig12_HTML.jpg)

图 9-12
演示微服务高可用性的组件

此示例中的大多数组件已在第 8 章中解释过，因此我将避免重复。相反，此处将描述本节中代码相对于第 8 章代码的任何增量更改。

您将首先查看 Nginx 反向代理设置。配置在清单 9-1 的 Nginx 配置文件中提及。



```
http {
upstream myapp1 {
server localhost:8082;
server localhost:8083;
}
upstream myapp2 {
server localhost:9001;
server localhost:9002;
}
server {
listen       8090;
server_name  tiger;
location / {
proxy_pass http://myapp2;
}
}
server {
listen       8090;
server_name  localhost;
location / {
proxy_pass http://myapp1;
}
}
}
代码清单 9-1
用于演示高可用性的 Nginx 配置 (ch09\ch09-01\ConfNginx\nginx.conf)
```

注意
你需要将代码清单 9-1 中的 `tiger` 替换为你自己主机的名称。

该文件通常位于 Nginx 发行版中。你需要编辑 `nginx.conf` 文件，以在 Nginx 发行版中反映上述更改，我在我的机器上找到该文件的位置是：`nginx-1.13.5\conf`。你也可以参考附录 C 来熟悉 Nginx。

使用不同名称来反映不同站点的正确方法是使用本地 DNS 服务器，然后在 DNS 中创建一些 CNAME 记录，并将它们指向 Nginx 配置。不过，这里你将使用一个简单的技巧来快速运行。我的 Windows 机器的主机名是 `tiger`，而 `localhost` 也指向这台机器，因此我可以在 Nginx 配置中使用这两个不同的名称来配置两个不同的反向代理设置。

因此，`http://myapp1` 和 `http://myapp2` 是两个反向代理设置，其中 `http://myapp1` 指向 API 网关，而 `http://myapp2` 指向 Web 应用服务器集群，可以从该集群获取应用的默认主页。

你需要在启动 API 网关和 Web 应用服务器之后才启动 Nginx，这样 Nginx 的日志中就不会出现任何错误。

由于你需要启动更多实例，让我们再次审视设计，但这次重点关注已配置的不同端口和微服务名称，这样当我解释相关代码部分时，你会更容易理解。这些细节已在图 9-13 中标注。

![../images/477630_1_En_9_Chapter/477630_1_En_9_Fig13_HTML.jpg](img/477630_1_En_9_Fig13_HTML.jpg)

图 9-13
用于高可用性演示的微服务名称和端口

Eureka 的配置与第 8 章中解释的相比没有变化。在 Zuul API 网关中，唯一的区别在于应用配置；请参见代码清单 9-2。

```
spring:
application:
name: product-service-api
zuul:
routes:
product-api:
path: /myapp1/api/∗∗
service-id: product-service
product-webapi:
path: /myapp1/web/∗∗
service-id: product-web
代码清单 9-2
Zuul API 网关路由配置 (ch09\ch09-01\ProductApiZuul\src\main\resources\application.yml)
```

该配置不言自明。它表明 API 网关的应用名称为 product-service-api。此外，任何符合 `/myapp1/api/**` 模式的 URL 都将被路由到 product-service 微服务，而任何符合 `/myapp1/web/**` 模式的 URL 都将被路由到 product-web 微服务。

Product Data Server 微服务不言自明，因为它与你第 8 章“使用 Zuul 的代码”部分中遇到的 Product Server 微服务类似。同样，Product Web Server 微服务与你第 8 章“编写 Hystrix 新设计代码”部分中遇到的 ProductWeb 微服务类似，因此这里不再赘述。

然而，Web App 微服务需要稍作解释，因为这是你第一次使用此类微服务的结构。Web App 微服务是一等微服务；但其目的是仅为客户端设备提供静态内容。在你的示例中，内容是 HTML 文件和 JavaScript，它们在客户端设备中渲染。因此，作为第一步，你需要将微服务建模为 Spring MVC Web 应用程序，并为此使用 spring-boot-starter-web 依赖项；请参见代码清单 9-3。

```
org.springframework.boot
spring-boot-starter-web

代码清单 9-3
用于 MVC 的 Spring Boot 依赖项 (ch09\ch09-01\WebApp\pom.xml)
```

spring-boot-starter-web 是用于构建 Web（包括 RESTful）应用程序的启动器，它使用 Spring MVC，并将 Tomcat 作为默认嵌入式容器。spring-boot-starter-web 通过 spring-boot-starter-tomcat 引入了 Tomcat，但也可以通过更改配置来使用 spring-boot-starter-jetty 和 spring-boot-starter-undertow。

接下来，Web App 微服务只有一个 Java 类，如代码清单 9-4 所示。

```
@SpringBootApplication
@EnableEurekaClient
public class EcomProductHomeMicroserviceApplication {
public static void main(String[] args) {
SpringApplication.run(EcomProductHomeMicroserviceApplication.class,
args);
}
}
代码清单 9-4
Web App 微服务应用 (ch09\ch09-01\WebApp\src\main\java\com\acme\ecom\product\EcomProductHomeMicroserviceApplication.java)
```

该应用的其他工件放置在文件夹 ch09\ch09-01\WebApp\src\main\resources 中。它们是基于 Angular JS 的 Web 应用所需的文件。我不会深入探讨或解释这个应用，因为本书的范围不是深入 Angular 或 Web 端编程，而仅关注微服务方面。因此，这里我只提及两个文件。

```
ch09\ch09-01\WebApp\src\main\resources\static\index.html
```

这是客户端设备在浏览器中输入引导 URL（`http://tiger:8090`）后将收到的欢迎页面。Nginx 会将其解析到 myapp2，然后 myapp2 会将请求负载均衡到代码清单 9-5 中所示的任何一个监听套接字。

```
upstream myapp2 {
server localhost:9001;
server localhost:9002;
}
代码清单 9-5
反向代理配置
```

如果你回头参考图 9-13，可以回想起端口 9001 和 9002 托管了 Web App 微服务的冗余实例。现在你需要理解，当 HTML 被下载并在客户端设备中渲染时，相关的脚本也会被下载到设备中，因此下一个感兴趣的文件也会被下载到设备中，并在设备上执行；请参见代码清单 9-6。

```
App.factory('ProductService', ['$http', '$q', function($http, $q){
return {
getApplication: function() {
return $http.get('http://localhost:8090/myapp1/web/productsweb/')
.then(
function(response){
return response.data._embedded.products;
},
function(errResponse){
console.error('Error while fetching application');
return $q.reject(errResponse);
}
);
}
};
}]);
代码清单 9-6
Web App 联系 API 网关 (ch09\ch09-01\WebApp\src\main\resources\static\js\service\product_service.js)
```

这里需要注意的主要方面是 URL `http://localhost:8090/myapp1/web/productsweb/`，它将被解析为：

*   `http://localhost:8090/myapp1/`：
    Nginx 会将其解析为

```
    upstream myapp1 {
    server localhost:8082;
    server localhost:8083;
    }
    ```

端口 8082 和 8083 托管了 API 网关的冗余实例。

*   接下来，`/web/**`
    将由 API 网关解析到 product-web 微服务：

```
    zuul:
    routes:
    product-webapi:
    path: /myapp1/web/∗∗
    service-id: product-web
    ```



*   最后，`/productsweb/` 是 product-web μ Service 的 RestController 方法所在之处：

```
    @RequestMapping(value = "/productsweb", method = RequestMethod.GET ,
    produces = {MediaType.APPLICATION_JSON_VALUE})
    public ResponseEntity>> getAllProducts() {
    return productServiceProxy.getAllProducts();
    }
    ```

最后，product-web μ Service 必须将请求委托给 product-service μ Service。这发生在 product-web μ Service 的 RestController 中，如清单 9-7 所示。

```
@EnableFeignClients(basePackageClasses = ProductServiceProxy.class)
@CrossOrigin
@RestController
public class ProductRestController implements ProductService{
private ProductServiceProxy productServiceProxy;
@Autowired
public ProductRestController(ProductServiceProxy productServiceProxy){
this.productServiceProxy = productServiceProxy;
}
@RequestMapping(value = "/productsweb", method = RequestMethod.GET ,
produces = {MediaType.APPLICATION_JSON_VALUE})
public ResponseEntity>> getAllProducts() {
return productServiceProxy.getAllProducts();
}
}
清单 9-7
Product Web 微服务 RestController (ch09\ch09-01\ProductWebServer\src\main\java\com\acme\ecom\product\controller\ProductRestController.java)
```

实际的路由发生在 Feign 客户端代理中，如清单 9-8 所示。

```
@FeignClient("product-service")
public interface ProductServiceProxy extends ProductService{
}
清单 9-8
Feign 客户端代理 (ch09\ch09-01\ProductWebServer\src\main\java\com\acme\ecom\product\client\ProductServiceProxy.java)
```

product-service 最终将由 Eureka 解析为 Product Data Service μ Service，至此整个流程结束。

构建并测试微服务的高可用性
演示 Eureka 注册中心所需的完整代码保存在 `ch09\ch09-01` 文件夹中。此示例不需要 MongoDB。您可以按以下顺序构建、打包和运行不同的微服务。

首先，启动 Eureka 微服务的两个实例：

```
cd ch09\ch09-01\Eureka
D:\binil\gold\pack03\ch09\ch09-01\Eureka>make
D:\binil\gold\pack03\ch09\ch09-01\Eureka>mvn -Dmaven.test.skip=true clean package
D:\binil\gold\pack03\ch09\ch09-01\Eureka>run1
D:\binil\gold\pack03\ch09\ch09-01\Eureka>java -jar -Dserver.port=8761 -Dspring.application.name=eureka-registry1 -Deureka.client.registerWithEureka=false -Deureka.client.fetchRegistry=true -Deureka.client.server.waitTimeInMsWhenSyncEmpty=0-Deureka.client.serviceUrl.defaultZone=http://localhost:8761/eureka/,http://localhost:8762/eureka/ -Deureka.server.enableSelfPreservation=false .\target\Ecom-Product-Microservice-0.0.1-SNAPSHOT.jar
cd ch09\ch09-01\Eureka
D:\binil\gold\pack03\ch09\ch09-01\Eureka>run2
D:\binil\gold\pack03\ch09\ch09-01\Eureka>java -jar -Dserver.port=8762 -Dspring.application.name=eureka-registry2 -Deureka.client.registerWithEureka=false -Deureka.client.fetchRegistry=true -Deureka.client.server.waitTimeInMsWhenSyncEmpty=0-Deureka.client.serviceUrl.defaultZone=http://localhost:8761/eureka/,http://localhost:8762/eureka/ -Deureka.server.enableSelfPreservation=false .\target\Ecom-Product-Microservice-0.0.1-SNAPSHOT.jar
```

这里需要注意几点：

*   通过配置 `eureka.client.registerWithEureka=false`，显式禁用 Eureka 向自身注册。

*   每个 Eureka 实例通过查看 `eureka.client.serviceUrl.defaultZone` 中的值来了解其对等节点。

*   与同一微服务的其他冗余实例不同，您为每个 Eureka 实例赋予了不同的名称。

接下来，启动 Zuul API 网关的两个实例：

```
cd ch09\ch09-01\ProductApiZuul
D:\binil\gold\pack03\ch09\ch09-01\ProductApiZuul>make
D:\binil\gold\pack03\ch09\ch09-01\ProductApiZuul>mvn -Dmaven.test.skip=true clean package
D:\binil\gold\pack03\ch09\ch09-01\ProductApiZuul>run1
D:\binil\gold\pack03\ch09\ch09-01\ProductApiZuul>java -Dserver.port=8082 -Dspring.application.name=product-service-api -Deureka.client.serviceUrl.defaultZone=http://localhost:8761/eureka/,http://localhost:8762/eureka/ -jar .\target\Ecom-Product-Microservice-0.0.1-SNAPSHOT.jar
cd ch09\ch09-01\ProductApiZuul
D:\binil\gold\pack03\ch09\ch09-01\ProductApiZuul>run2
D:\binil\gold\pack03\ch09\ch09-01\ProductApiZuul>java -Dserver.port=8083 -Dspring.application.name=product-service-api -Deureka.client.serviceUrl.defaultZone=http://localhost:8761/eureka/,http://localhost:8762/eureka/ -jar .\target\Ecom-Product-Microservice-0.0.1-SNAPSHOT.jar
```

稍后您会看到 Zuul 实例本身会向 Eureka 注册。
由于 Eureka 注册中心和 Zuul API 网关都已设置完毕，现在可以逐个启动应用程序微服务。

首先，启动 Product Data Server μ Service 的两个实例：

```
cd ch09\ch09-01\ProductDataServer
D:\binil\gold\pack03\ch09\ch09-01\ProductDataServer>make
D:\binil\gold\pack03\ch09\ch09-01\ProductDataServer>mvn -Dmaven.test.skip=true clean package
D:\binil\gold\pack03\ch09\ch09-01\ProductDataServer>run1
D:\binil\gold\pack03\ch09\ch09-01\ProductDataServer>java -Dserver.port=8080 -Dspring.application.name=product-service -Deureka.client.serviceUrl.defaultZone=http://localhost:8761/eureka/,http://localhost:8762/eureka/ -jar .\target\Ecom-Product-Microservice-0.0.1-SNAPSHOT.jar
cd ch09\ch09-01\ProductDataServer
D:\binil\gold\pack03\ch09\ch09-01\ProductDataServer>run2
D:\binil\gold\pack03\ch09\ch09-01\ProductDataServer>java -Dserver.port=8081 -Dspring.application.name=product-service -Deureka.client.serviceUrl.defaultZone=http://localhost:8761/eureka/,http://localhost:8762/eureka/ -jar .\target\Ecom-Product-Microservice-0.0.1-SNAPSHOT.jar
```

接下来，启动 Product Web μ Service 的两个实例：

```
cd ch09\ch09-01\ProductWebServer
D:\binil\gold\pack03\ch09\ch09-01\ProductWebServer>make
D:\binil\gold\pack03\ch09\ch09-01\ProductWebServer>mvn -Dmaven.test.skip=true clean package
D:\binil\gold\pack03\ch09\ch09-01\ProductWebServer>run1
D:\binil\gold\pack03\ch09\ch09-01\ProductWebServer>java -Dserver.port=8084 -Dspring.application.name=product-web -Deureka.client.serviceUrl.defaultZone=http://localhost:8761/eureka/,http://localhost:8762/eureka/ -jar .\target\Ecom-Product-Microservice-0.0.1-SNAPSHOT.jar
cd ch09\ch09-01\ProductWebServer
D:\binil\gold\pack03\ch09\ch09-01\ProductWebServer>run2
D:\binil\gold\pack03\ch09\ch09-01\ProductWebServer>java -Dserver.port=8085 -Dspring.application.name=product-web -Deureka.client.serviceUrl.defaultZone=http://localhost:8761/eureka/,http://localhost:8762/eureka/ -jar .\target\Ecom-Product-Microservice-0.0.1-SNAPSHOT.jar
```

最后，启动 Web App μ Service 的两个实例：

```
cd ch09\ch09-01\WebApp
D:\binil\gold\pack03\ch09\ch09-01\WebApp>make
D:\binil\gold\pack03\ch09\ch09-01\WebApp>mvn -Dmaven.test.skip=true clean package
D:\binil\gold\pack03\ch09\ch09-01\WebApp>run1
D:\binil\gold\pack03\ch09\ch09-01\WebApp>java -Dserver.port=9001 -Dspring.application.name=product-home -Deureka.client.serviceUrl.defaultZone=http://localhost:8761/eureka/,http://localhost:8762/eureka/ -jar .\target\Ecom-Product-Microservice-0.0.1-SNAPSHOT.jar
cd ch09\ch09-01\WebApp
D:\binil\gold\pack03\ch09\ch09-01\WebApp>run2
D:\binil\gold\pack03\ch09\ch09-01\WebApp>java -Dserver.port=9002 -Dspring.application.name=product-home -Deureka.client.serviceUrl.defaultZone=http://localhost:8761/eureka/,http://localhost:8762/eureka/ -jar .\target\Ecom-Product-Microservice-0.0.1-SNAPSHOT.jar
```

至此，所有微服务应该都已启动。您可以在 Eureka 控制台进行检查：

```
http://localhost:8761 或 http://localhost:87612
```



见图 9-14。

![../images/477630_1_En_9_Chapter/477630_1_En_9_Fig14_HTML.jpg](img/477630_1_En_9_Fig14_HTML.jpg)

图 9-14
Eureka 控制台中的高可用微服务

可以看到，您已经注册了对外可用的 μService。换句话说，Web 应用 μService (product-home) 已注册到 Eureka 中，尽管这不是必需的，并且您也没有从注册表中对其进行查找。此外，甚至 API 网关也出现在 Eureka 的注册列表中。这同样不是强制性的。不过，您可以根据具体需求进行这些变通。

最后一步，您应该启动 Nginx 反向代理。假设您已完成所需的配置，可以通过以下命令启动它：

```
cd D:\Applns\nginx\nginx-1.13.5
D:\Applns\nginx\nginx-1.13.5>nginx
```

一切就绪。现在，您可以打开浏览器（最好使用 Chrome），输入以下 URL 来端到端地测试应用流程：

```
http://tiger:8090/
```

如果一切顺利，从标签 1 到标签 6 的端到端流程（如图 9-13 所示）将被执行，获取数据并将数据渲染到网页中。如果页面未能成功渲染数据，您可以使用提供的按钮刷新屏幕，或者尝试使用新的浏览器重试。
您可以通过关闭其中一个微服务，使用浏览器测试应用，然后重新启动之前关闭的微服务，并再次使用浏览器进行测试，来专门测试每个微服务的高可用性。
需要注意的是，这个端到端演示涉及多个动态组件。如果您通过“猴子测试”来验证高可用性场景，请务必给微服务足够的时间来恢复，并让它们有足够的时间相互同步，包括与注册中心的同步。
如果您想重新测试数据加载，GUI 中还提供了一个“刷新”按钮。

总结
链条的强度取决于其最薄弱的环节，这句谚语同样适用于高可用性的许多方面。我从一个宏观视角，阐述了高可用性和冗余在端到端部署的每个阶段是如何体现的，从数据产生的客户端浏览器开始，到 Web 和应用服务器，再到数据管理和数据存储层，最后到归档或灾难恢复部分。在第 16 章中，您还将研究改进高可用性的高级构造和模式，旨在确保用户创建的每一个字节的完整性，都能按照端到端链中预期的数据一致性规则得以保留，无论您是遵循具有严格 ACID^(¹⁸) 合规性的传统单体架构，还是采用具有宽松 ACID 合规性的新一代微服务架构。虽然配置高可用性及相关方面需要在处理节点和网络路径上实现冗余，但与单体架构相比，基于微服务的架构可能会增加网络跳数或线程及进程级上下文切换的总数。因此，应尽一切努力解决单个微服务以及整个微服务应用的性能问题。您将在下一章中探讨这一点。

脚注

10. 微服务性能

当您从传统的三层或 n 层架构迁移到微服务架构时，一个主要的可观察特征是微服务进程之间互连数量的增加。以前完全在单个进程内完成的计算，现在可能会被拆分成许多跨进程的微计算。这增加了进程间通信的数量、上下文切换的次数、涉及的 I/O 操作数量等。因此，更详细地审视这些方面是很有意义的。在本章中，您将探讨微服务中需要考虑的几个性能方面，具体包括：

*   同步 HTTP 与异步 HTTP
*   用于异步 HTTP 的 Servlet 3.0 规范
*   演示浏览器与微服务之间异步 HTTP 的代码
*   演示微服务之间异步 HTTP 的代码
*   Google Protocol Buffer
*   演示在微服务之间使用 Google Protocol Buffer 的代码

外部架构间的通信

第 4 章的“外部架构视角”部分介绍了微服务的外部架构。在那里，您看到了当您转向基于微服务的架构时，典型单体架构的内部复杂性是如何“浮出水面”的。一个明显的变化是，单个或少数几个计算进程被拆分成更多类似但计算密集度较低的进程。借助图 10-1 可以更好地解释这一变化。

![../images/477630_1_En_10_Chapter/477630_1_En_10_Fig1_HTML.jpg](img/477630_1_En_10_Fig1_HTML.jpg)

图 10-1
单体架构与微服务架构的边界

在传统的单体架构中，构成服务实现的多个组件在单个容器进程中执行。这些组件之间的方法调用或消息传递是“本地”或“进程内”的。这将允许进程（无论是容器管理的进程还是简单的 Java 应用程序进程）做出许多假设和优化，并完成执行。然而，当您将同一个服务从单体架构重新架构为微服务架构时，实现会被拆分并部署到多个进程中。如图 10-1 所示。与进程内的通信相比，进程间通信是重量级的。它们涉及更多的上下文切换、网络等待和 I/O 操作、数据编组和解组等。这增加了微服务架构外部架构空间的复杂性。
此外，每个微服务都涉及监听传入流量的套接字监听器。因此，与传统的单体架构相比，微服务架构涉及更多的套接字监听器、用于处理传入流量的相关线程等。深入研究这些精选关注点中的几个细节，并分析解决它们的方案，将使您对整个领域有一个相当清晰的认识。

异步 HTTP
您在第 6 章中简要了解了分布式消息传递，同时也认识到了微服务之间同步和异步通信风格的区别。您了解到消息传递是微服务进行异步通信的一流技术，并通过示例看到了如何关联许多异步消息传输，以便将它们编织成单个端到端请求/响应周期的组成部分。然而，第 8 章中的所有示例都使用 HTTP 协议进行微服务之间的相互通信。在本节中，您将更仔细地审视这些通信的性质。



HTTP 的弊端与丑陋之处
如前所述，第 8 章中的所有示例均使用 HTTP 协议进行微服务间的相互通信。如果再次审视这些交互，你会发现其中大部分是同步 HTTP 调用。仔细观察这些通信过程会发现，从消费端微服务到提供端微服务服务器的传入请求会占用一个 Servlet 连接，并在向消费端微服务发送响应之前，对远程服务执行阻塞调用。这种方式虽然可行，但如果同时存在大量此类并发客户端，则无法有效扩展。同步 HTTP 最适合用户使用客户端应用程序获取即时反馈或响应的场景；然而，在服务器端处理时，情况并非如此。

异步 HTTP 处理的 API
像 Apache Tomcat 这类应用服务器中的典型容器，通常为每个客户端请求使用一个服务器线程。在负载增加的情况下，这要求容器拥有大量线程来处理所有客户端请求。这限制了可扩展性，可能因内存耗尽或容器线程池耗尽而引发问题。从 Servlet 3.0 规范开始，Java EE 为 Servlet 和过滤器增加了异步处理支持。当 Servlet 或过滤器在处理请求时遇到潜在的阻塞操作，可以将该操作分配给异步执行上下文，并立即将与请求关联的线程返回给容器，而无需等待生成响应。阻塞操作稍后可以在某个不同线程的异步执行上下文中完成，该线程可以生成响应或将请求分派给另一个 Servlet。`javax.servlet.AsyncContext` 类就是提供在服务方法内执行异步处理所需功能的 API。服务方法中 `HttpServletRequest` 对象的 `startAsync()` 方法会将请求置于异步模式，并确保即使在退出服务方法后也不会提交响应。你必须在阻塞操作完成后的异步上下文中生成响应，或者将请求分派给另一个 Servlet。

这种编程模型很直接。让我们先看看传统 `Controller` 类编程模型的伪代码，来理解其中的区别。请参见代码清单 10-1。

```
@RestController
public class ProductRestController{
@RequestMapping(value = "/products", method = RequestMethod.GET ,
produces = {MediaType.APPLICATION_JSON_VALUE})
public List getAllProducts() {
//return "List of All Products";
}
}
代码清单 10-1
同步控制器的伪代码
```

当你使用 Spring 时，控制器方法可以返回一个 `java.util.concurrent.Callable` 来异步完成处理。然后 Spring MVC 会借助 TaskExecutor 在单独的线程中调用该 `Callable`。TaskExecutor 是 Spring 对异步执行和任务调度的抽象。重写后的伪代码如代码清单 10-2 所示。

```
@RestController
public class ProductRestController{
@RequestMapping(value = "/products", method = RequestMethod.GET ,
produces = {MediaType.APPLICATION_JSON_VALUE})
public Callable> getAllProducts() {
return new Callable>() {
public Object call() throws Exception {
// ...
// return "List of All Products";
}
};
}
}
代码清单 10-2
使用 Callable 的异步控制器伪代码
```

Spring 3.2 引入了 `org.springframework.web.context.request.async.DeferredResult`，它同样可以由 `Controller` 类返回。`DeferredResult` 为使用 `Callable` 进行异步请求处理提供了一种替代方案。`Callable` 是代表应用程序并发执行的，而使用 `DeferredResult`，应用程序可以从其选择的线程中产生结果。在这里，处理可以在 Spring MVC 未知的线程中进行，类似于需要从消息通道等获取响应的情况。因此，当你使用 `DeferredResult` 时，即使离开了控制器处理方法，请求处理也尚未完成。相反，Spring MVC（利用 Servlet 3.0 的能力）会保持响应，并保持空闲的 HTTP 连接。即使 HTTP 工作线程不再使用，HTTP 连接仍然保持打开状态。在稍后的某个时间点，其他某个线程会通过为 `DeferredResult` 赋值来解析它。Spring MVC 会立即捕获此事件并向客户端发送响应，从而完成请求处理。

使用 `DeferredResult` 的异步场景伪代码如代码清单 10-3 所示。

```
@RestController
public class ProductRestController{
@RequestMapping(value = "/products", method = RequestMethod.GET ,
produces = {MediaType.APPLICATION_JSON_VALUE})
public DeferredResult> getAllProducts() {
DeferredResult> deferredResult =
new DeferredResult> ();
// 将 deferredResult 添加到内部内存持有者中
// 比如一个队列或映射...
return deferredResult;
}
// 在某个其他线程中...
deferredResult.setResult(data);
// 从内部内存持有者中移除 deferredResult
}
代码清单 10-3
使用 DeferredResult 的异步控制器伪代码
```

Java 8 引入了 `java.util.concurrent.CompletableFuture`，它可以与 Spring 一起使用来创建异步端点，这些端点同样可以释放请求线程以执行其他任务。在这种情况下，`DeferredResult.setResult` 可以简单地替换为 `CompletableFuture.complete`。`CompletableFuture` 是一个 `java.util.concurrent.Future`。`Future` 表示异步计算的结果，而 `CompletableFuture` 是一个可以显式完成（设置其值和状态）的 `Future`，并且可以用作 `CompletionStage`，支持在其完成时触发的依赖函数和操作。`CompletableFuture` 使得处理复杂的异步编程更加容易。它甚至允许你组合和级联异步调用，并且还提供了静态实用方法 `runAsync` 和 `supplyAsync` 来抽象掉手动创建线程的过程。请参见代码清单 10-4。

```
@RestController
public class ProductRestController{
@RequestMapping(value = "/products", method = RequestMethod.GET,
produces = {MediaType.APPLICATION_JSON_VALUE})
public CompletableFuture> getAllProducts() {
Future> future =
new CompletableFuture>();
return CompletableFuture.supplyAsync(() -> "in the background");
}
}
代码清单 10-4
使用 CompletableFuture 的异步控制器伪代码
```

设计一个演示微服务间异步 HTTP 的场景
你将使用与之前示例相同的组件，但进行精简。因此，这里的示例将包含三个主要组件：一个基于 HTML 的客户端应用程序和两个微服务，如图 10-2 所示。我已移除了 HATEOAS 和所有数据仓库的复杂性，以便你可以专注于异步 HTTP 本身。一旦你理解了设计及其实现所用到的主要组件，就应该能够将类似的模式应用于其他复杂的业务场景。


好的，作为一名高级文档工程师和翻译员，我将严格遵循您提供的注意事项和示例格式，将给定的英文文本翻译成中文。


在所示的设计中，客户端应用使用 HTTP 协议与 Product Web 微服务通信；但是，您希望客户端使用异步 HTTP 模型来实现。同样，Product Web 微服务也使用 HTTP 协议与 Product Server 微服务通信，并且这种微服务间的通信是使用异步 HTTP 模型设计的。

![../images/477630_1_En_10_Chapter/477630_1_En_10_Fig2_HTML.jpg](img/477630_1_En_10_Fig2_HTML.jpg)

图 10-2
在 Spring Boot 中设计一个异步 HTTP 场景

让我们通过参考代码片段来更详细地了解这个设计，以便概念更加清晰。

在 Spring Boot 中使用异步 HTTP 的代码

本节的所有代码示例都在文件夹 `ch10\ch10-01` 中。访问 `pom.xml` 可以看到为您的 Product Server 微服务显式添加了 `spring-boot-starter-web` 依赖。请参阅清单 10-5。

```
org.springframework.boot
spring-boot-starter-web

清单 10-5
将 spring-boot-starter-web 添加到 Spring Boot (ch10\ch10-01\ProductServer\pom.xml)
```

`spring-boot-starter-web` 将为 Product Server 微服务添加 Tomcat 和 Spring MVC。

接下来，查看清单 10-6 中显示的 Boot 主应用程序类。

```
@SpringBootApplication
@EnableAsync
public class EcomProductMicroserviceApplication {
public static void main(String[] args) {
SpringApplication.run(EcomProductMicroserviceApplication.class, args);
}
}
清单 10-6
启用异步的 Spring Boot 应用程序 (ch10\ch10-01\ProductServer\src\main\java\com\acme\ecom\product\EcomProductMicroserviceApplication.java)
```

`@EnableAsync` 注解开启了 Spring 在后台线程池中运行 `@Async` 方法的能力。这个类还自定义了支持线程池的 `Executor`。默认情况下，使用的是 `SimpleAsyncTaskExecutor`。`SimpleAsyncTaskExecutor` 不会重用线程。尽管它支持通过 `concurrencyLimit` bean 属性限制并发线程，但默认情况下并发线程数是不受限制的。在严肃的应用中，您应该考虑使用线程池化的 `TaskExecutor` 实现。

接下来，查看实现异步处理逻辑的 Service 组件，如清单 10-7 所示。

```
@Service
public class ProductService{
@Async
public CompletableFuture> getAllProducts()
throws InterruptedException{
List products = getAllTheProducts();
if (products.size() == 0) {
LOGGER.debug("No Products Retreived from the repository");
}
LOGGER.debug(Thread.currentThread().toString());
Thread.sleep(2000L); // 延迟以模拟一个长时间运行的任务
return CompletableFuture.completedFuture(products);
}
}
清单 10-7
实现异步处理的 Service 组件 (ch10\ch10-01\ProductServer\src\main\java\com\acme\ecom\product\service\ProductService.java)
```

该类被标记为 `@Service` 注解，使其成为 Spring 组件扫描的候选对象，以便检测并将其添加到应用上下文中。接下来，`getAllProducts` 方法被标记为 Spring 的 `@Async` 注解，表明它将在单独的线程上运行。该方法的返回类型是 `CompletableFuture<List<Product>>` 而不是 `<List<Product>>`，这是任何异步服务的要求。这段代码使用 `completedFuture` 方法返回一个 `CompletableFuture` 实例，该实例已经使用从仓库查询到的结果集合（一个预计需要花费相当长时间才能完成的处理逻辑）完成了。

现在，查看 Product Server 微服务的 Rest Controller，如清单 10-8 所示。

```
@RestController
public class ProductRestController {
private final ProductService productService;
@Autowired
public ProductRestController(ProductService productService) {
this.productService = productService;
}
@RequestMapping(value = "/products", method = RequestMethod.GET,
produces = {MediaType.APPLICATION_JSON_VALUE})
public CompletableFuture> getAllProducts()
throws InterruptedException, ExecutionException{
LOGGER.debug(Thread.currentThread().toString());
return productService.getAllProducts()
.thenApply(products -> {
products.forEach(item->LOGGER.debug(item.toString()));
LOGGER.debug(Thread.currentThread().toString());
return products;
});
}
}
清单 10-8
促进异步的 Product Server Rest Controller (ch10\ch10-01\ProductServer\src\main\java\com\acme\ecom\product\controller\ProductRestController.java)
```

请注意，`productService.getAllProducts()` 返回的 `CompletableFuture` 响应允许您手动控制 future 返回的时刻，并在此过程中转换输出。然后，您借助 `thenApply()` 将这个简单的 `CompletableFuture` 转换为您需要的格式，`thenApply()` 允许您记录一些关于当前线程的数据，以确保执行确实是异步发生的，即完成工作的线程不是启动工作的线程。然后，Rest Controller 再次将产品集合作为另一个 `CompletableFuture<List<Product>>` 返回，以便 Spring MVC 也以异步方式执行 HTTP 方法，从而使其成为一个巧妙实现的微服务！请记住，这是一个独立的微服务；接下来您将看到一个依赖于此独立微服务的微服务（如图所示）。

这个依赖的微服务是 Product Web 微服务。主要代码位于 `Controller` 类中，如清单 10-9 所示。

```
@RestController
public class ProductRestController{
private final AsyncRestTemplate asyncRestTemplate =
new AsyncRestTemplate();
private static String PRODUCT_SERVICE_URL =
"http://localhost:8080/products/";
@RequestMapping(value = "/productsweb", method = RequestMethod.GET,
produces = {MediaType.APPLICATION_JSON_VALUE})
public DeferredResult> getAllProducts() {
LOGGER.debug(Thread.currentThread().toString());
DeferredResult> deferredResult = new DeferredResult();
ParameterizedTypeReference> responseTypeRef =
new ParameterizedTypeReference>() {};
ListenableFuture>> entity =
asyncRestTemplate.exchange(PRODUCT_SERVICE_URL,
HttpMethod.GET, (HttpEntity) null, responseTypeRef);
entity.addCallback(new
ListenableFutureCallback>>() {
@Override
public void onFailure(Throwable ex) {
LOGGER.debug(Thread.currentThread().toString());
LOGGER.error(ex.getMessage());
}
@Override
public void onSuccess(ResponseEntity> result) {
List products = result.getBody();
products.forEach(item->LOGGER.debug(item.toString()));
LOGGER.debug(Thread.currentThread().toString());
deferredResult.setResult(products);
}
});
LOGGER.debug(Thread.currentThread().toString());
return deferredResult;
}
}
清单 10-9
在另一个微服务上调用异步 HTTP 的微服务 (ch10\ch10-01\ProductWeb\src\main\java\com\acme\ecom\product\controller\ProductRestController.java)
```



这里的 `PRODUCT_SERVICE_URL` 指的是独立的微服务。Product Web 微服务需要调用 Product Server 微服务。您已经将 Product Server 微服务的方法设为异步。现在，让我们也将 Product Web 微服务的方法设为异步，以便托管这些微服务的所有容器能够通过为运行时模型实现异步特性，更好地利用容器资源。但这还没完；您还需要以异步模式调用 Product Server 微服务，这样微服务的实现以及微服务间的通信都将采用智能的异步模式！

`org.springframework.web.client.AsyncRestTemplate` 是 Spring 用于异步客户端 HTTP 访问的核心类。它公开的方法与 `RestTemplate` 类似；不过，它返回的是 `ListenableFuture` 包装器，而不是具体的结果。默认情况下，`AsyncRestTemplate` 依赖标准的 JDK 工具来建立 HTTP 连接。同样，您也可以通过使用接受 `AsyncClientHttpRequestFactory` 的构造函数，切换到其他 HTTP 库，例如 Apache HttpComponents、Netty 或 OkHttp。`AsyncRestTemplate` 为您提供了一个 `ListenableFuture`，因此容器线程不会等待接收响应；相反，由于您使用的是 `AsyncRestTemplate`，它会继续执行下一步处理。`ListenableFuture` 能够接受完成回调，因此您可以添加失败和成功场景的回调。如果成功，您将结果设置到 `DeferredResult` 的主体中。由于 Spring MVC 通过空闲的 HTTP 连接保持响应，一旦您将结果设置到 `DeferredResult`，客户端就会收到它。

`product_service.js` 包含了 Angular JS 客户端用于调用 Product Web 微服务的代码；请参见清单 10-10。

```
App.factory('ProductService', ['$http', '$q', function($http, $q){
return {
getApplication: function() {
return $http.get('http://localhost:8081/productsweb/')
.then(
function(response){
return response.data;
},
function(errResponse){
console.error('Error while fetching application');
return $q.reject(errResponse);
}
);
}
};
}]);
清单 10-10
Web 客户端调用异步 HTTP (ch10\ch10-01\ProductWeb\src\main\resources\static\js\service\product_service.js)
```

构建并测试微服务间的异步 HTTP
演示微服务间异步 HTTP 所需的完整代码位于文件夹 `ch10\ch10-01` 中。此示例不需要 MongoDB。您可以按以下顺序构建、打包和运行不同的微服务。

首先启动 Product Server 微服务：

```
cd ch10\ch10-01\ProductServer
D:\binil\gold\pack03\ch10\ch10-01\ProductServer>make
D:\binil\gold\pack03\ch10\ch10-01\ProductServer>mvn -Dmaven.test.skip=true clean package
D:\binil\gold\pack03\ch10\ch10-01\ProductServer>run
D:\binil\gold\pack03\ch10\ch10-01\ProductServer>java -jar -Dserver.port=8080 .\target\Ecom-Product-Microservice-0.0.1-SNAPSHOT.jar
```

请参考附录 D 了解 cURL 和 Postman 的概述。您可以使用这些工具中的任何一种，通过指向 `http://localhost:8080/products/` 来测试上述微服务。

接下来，启动 Product Web 微服务：

```
cd ch10\ch10-01\ProductWeb
D:\binil\gold\pack03\ch10\ch10-01\ProductWeb>make
D:\binil\gold\pack03\ch10\ch10-01\ProductWeb>mvn -Dmaven.test.skip=true clean package
D:\binil\gold\pack03\ch10\ch10-01\ProductWeb>run
D:\binil\gold\pack03\ch10\ch10-01\ProductWeb>java -jar -Dserver.port=8081 .\target\Ecom-Product-Microservice-0.0.1-SNAPSHOT.jar
```

您可以再次使用 cURL 或 Postman，通过指向 `http://localhost:8081/productsweb/` 来测试 Product Web 微服务。

或者，您也可以打开这个 HTML 工具（最好在 Chrome 浏览器中打开）：

```
ch10\ch10-01\ProductWeb\src\main\resources\product.html.
```

浏览器客户端在加载自身时，会向监听 8081 端口的 Product Web 发送一个请求，该请求将委托给监听 8080 端口的 Product Server 微服务。到目前为止，就请求路由而言，一切正常。实际上，客户端与 Product Web 微服务之间的 HTTP 通道，以及 Product Web 微服务与 Product Server 微服务之间的 HTTP 通道，都保持打开和空闲状态，并且两个微服务中的容器线程将返回，以便可用于上下文切换。因此，您的 Postman 客户端或 HTML 工具客户端将一直等待空闲的 HTTP 连接。在服务器端，处理发生在不同的线程中，一旦结果可用，它们就会被写回到打开的 HTTP 连接中。

通过仔细观察微服务控制台的日志，可以理解线程的动态。清单 10-11 显示了被调用微服务（ProductServer）的控制台输出。

```
D:\binil\gold\pack03\ch10\ch10-01\ProductServer>run
D:\binil\gold\pack03\ch10\ch10-01\ProductServer>java -jar -Dserver.port=8080 .\target\Ecom-Product-Microservice-0.0.1-SNAPSHOT.jar
.   ____          _            __ _ _
/\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
\\/  ___)| |_)| | | | | || (_| |  ) ) ) )
'  |____| .__|_| |_|_| |_\__, | / / / /
=========|_|==============|___/=/_/_/_/
:: Spring Boot ::        (v1.5.4.RELEASE)
2019-02-23 10:11:33 INFO  StartupInfoLogger.logStarting:48 - Starting
2019-02-23 10:11:40 INFO  StartupInfoLogger.logStarted:57 - Started EcomProductMicroserviceApplication in 8.231 seconds (JVM running for 9.827)
2019-02-23 10:13:34 INFO  ProductRestController.getAllProducts:94 - Start
2019-02-23 10:13:34 DEBUG ProductRestController.getAllProducts:95 - Thread[http-nio-8080-exec-10,5,main]
2019-02-23 10:13:34 INFO  ProductService.getAllProducts:78 - Start
2019-02-23 10:13:34 DEBUG ProductService.getAllProducts:79 - Fetching all the products from the repository
2019-02-23 10:13:34 INFO  ProductService.getAllTheProducts:109 - Start
2019-02-23 10:13:34 INFO  ProductService.getAllTheProducts:136 - Ending...
2019-02-23 10:13:34 DEBUG ProductService.getAllProducts:86 - Thread[SimpleAsyncTaskExecutor-1,5,main]
2019-02-23 10:13:34 INFO  ProductService.getAllProducts:87 - Ending
2019-02-23 10:13:36 DEBUG ProductRestController.lambda$null$1:100 - Product [productId=1, ...]
2019-02-23 10:13:36 DEBUG ProductRestController.lambda$null$1:100 - Product [productId=2, ...]
2019-02-23 10:13:36 DEBUG ProductRestController.lambda$getAllProducts$2:101 - Thread[SimpleAsyncTaskExecutor-1,5,main]
清单 10-11
ProductServer（被调用）微服务的控制台日志
```

清单 10-11 显示 `ProductRestController` 在 `Thread[http-nio-8080-exec-10,5,main]` 的上下文中执行，`ProductService` 也是如此。在 `ProductService` 中，就在 `ProductService.getAllTheProducts:136 - Ending...` 日志发生后，以下两行代码释放了正在执行的线程：

```
Thread.sleep(2000L); // 延迟以模拟长时间运行的任务
return CompletableFuture.completedFuture(products);
```

您需要区分上述两行代码，我将解释它们：

1.  第一行只是制造一个延迟，以便调用者（下面复制的 ProductWeb 微服务中 `ProductRestController` 的代码行）会感受到来自被调用微服务的感知延迟：

```
    ListenableFuture>> entity =
    asyncRestTemplate.exchange(PRODUCT_SERVICE_URL, HttpMethod.GET,
    (HttpEntity) null, responseTypeRef);
    ```

2.  第二行代码实际上返回了正在执行的线程，因此 `Thread[http-nio-8080-exec-10,5,main]` 将被返回到嵌入式 Web 容器的 HTTP 池中。



当 `CompletableFuture.completedFuture(products)` 恢复执行时，它将在某个其他线程上下文中运行（在您的示例中为 `Thread[SimpleAsyncTaskExecutor-1,5,main]`，如清单 10-11 所示）。

类似地，清单 10-12 展示了被调用的（ProductServer）微服务的控制台输出。

```
D:\binil\gold\pack03\ch10\ch10-01\ProductWeb>run
D:\binil\gold\pack03\ch10\ch10-01\ProductWeb>java -jar -Dserver.port=8081 .\target\Ecom-Product-Microservice-0.0.1-SNAPSHOT.jar
.   ____          _            __ _ _
/\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
\\/  ___)| |_)| | | | | || (_| |  ) ) ) )
'  |____| .__|_| |_|_| |_\__, | / / / /
=========|_|==============|___/=/_/_/_/
:: Spring Boot ::        (v1.5.4.RELEASE)
2019-02-23 10:12:33 DEBUG StartupInfoLogger.logStarting:51 - Running with Spring Boot v1.5.4.RELEASE, Spring v4.3.9.RELEASE
2019-02-23 10:12:38 INFO  StartupInfoLogger.logStarted:57 - Started EcomProductMicroserviceApplication in 6.804 seconds (JVM running for 8.494)
2019-02-23 10:13:34 INFO  ProductRestController.getAllProducts:87 - Start
2019-02-23 10:13:34 DEBUG ProductRestController.getAllProducts:88 - Thread[http-nio-8081-exec-7,5,main]
2019-02-23 10:13:34 DEBUG ProductRestController.getAllProducts:113 - Thread[http-nio-8081-exec-7,5,main]
2019-02-23 10:13:37 DEBUG ProductRestController$2.lambda$onSuccess$0:107 - Product [productId=1, ...]
2019-02-23 10:13:37 DEBUG ProductRestController$2.lambda$onSuccess$0:107 - Product [productId=2, ...]
2019-02-23 10:13:37 DEBUG ProductRestController$2.onSuccess:108 - Thread[SimpleAsyncTaskExecutor-1,5,main]
清单 10-12
ProductWeb（调用方）微服务的控制台日志
```

清单 10-11 和 10-12 证实了后台处理发生在 `SimpleAsyncTaskExecutor` 的线程中，而格式为 `http-nio-*.*` 的容器线程已经完成了事务的请求部分，并被归还到线程池。这是一种强大的方式，用于桥接 RESTful 接口的同步、阻塞范式与服务器端执行的异步、非阻塞处理。

Spring Boot 微服务之间的 Google Protocol Buffer
上一节讨论了如何有效利用微服务的服务器资源，如 HTTP 连接和线程。同样重要的是微服务间通信中其他可能的优化。由于微服务跨越进程边界，微服务之间发送的数据量至关重要。这归结为跨微服务的数据结构编组和解组。在本节中，您将通过示例来探讨这一点。

Protocol Buffer
Protocol Buffer 是 Google 用于编组和解组结构化数据的平台中立且语言中立的机制。您需要定义希望数据如何结构化以使用 Protocol Buffer，然后可以在多个平台和语言中使用生成的源代码，轻松地从各种数据流中写入和读取结构化数据。使用 Protocol Buffer 的一个好处是，您甚至可以更新数据结构，而不会破坏已部署的、针对“旧”格式编译的应用程序代码。这对于构建能够适应或演进以适应未来需求变化的应用程序架构尤为重要。

现在，您将探讨使用 Protocol Buffer 的动态过程。首先，您需要在 `.proto` 文件中指定希望序列化的信息如何结构化。在此过程中，您将使用 Protocol Buffer 消息类型。每个 Protocol Buffer 消息都是一个信息的逻辑分组，包含一系列名称-值对。清单 10-13 是一个 `.proto` 文件的示例，它定义了一个包含 `Product` 信息的消息，您在所有之前的示例中都使用了它。

```
message Product {
string productId = 1;
string name = 2;
string code = 3;
string title = 4;
string description = 5;
string imgUrl = 6;
double price = 7;
string productCategoryName = 8;
}
清单 10-13
示例 .proto 文件
```

每个消息类型（在示例中为 `Product` 消息）都有一个或多个唯一编号的字段，每个字段都有一个名称和一个值类型，其中值类型可以是数字（整数或浮点数）、布尔值、字符串或原始字节。您的 Protocol Buffer 消息也可以是其他 Protocol Buffer 消息类型，如清单 10-14 中定义的容器消息所示，用于保存 `Product` 实例的集合。

```
message Products {
repeated Product product = 1;
}
清单 10-14
用于其他 .proto 类型集合的 .proto 文件
```

您可以指定字段为可选、必需和/或重复，如 `Products` 消息所示。

一旦定义了消息，您就可以针对您选择的应用程序编程语言，在您的 `.proto` 文件上运行 Protocol Buffer 编译器，以生成实体类。这些实体类为每个字段提供简单的访问器（如 `code()` 和 `set_code()`），以及将整个结构序列化/解析为原始字节的方法。在您的场景中，如果您选择的语言是 Java，对上述示例运行编译器将生成一个名为 `Product` 的类。您可以在应用程序中使用这个实体类来编组和解组 `Product` Protocol Buffer 消息。您的代码将类似于清单 10-15 所示。

```
Product product =
Product.newBuilder().setProductId(id)
.setName("Kamsung D3")
.setCode("KAMSUNG-TRIOS")
.setTitle("Kamsung Trios 12 inch , black , 12 px ....")
.setDescription("Kamsung Trios 12 inch with Touch")
.setImgUrl("kamsung.jpg")
.setPrice(12000.00)
.setProductCategoryName("Mobile").build();
// 写入
FileOutputStream output = new FileOutputStream("D:/Product.ser");
product.writeTo(output);
output.close();
// 读取
Product productFromFile = Product.parseFrom(new FileInputStream("D:/Product.ser"));
清单 10-15
编组和解组 .proto 类型
```

现在，您可以向消息格式添加新字段，而不会破坏向后兼容性；任何旧的程序二进制文件在解析时会忽略任何新字段。因此，通过使用 Protocol Buffer 作为数据格式，您可以扩展消息协议，而无需担心破坏现有代码。

Google 关于 Protocol Buffer 的文档列出了使用 Protocol Buffer 的优势如下：

*   更简单
*   体积小 3 到 10 倍
*   速度快 20 到 100 倍
*   歧义更少
*   生成的数据访问类在编程上更易于使用

第二点和第三点意义重大，尤其是在您希望设计微服务之间大量“闲聊”式交互时，而在任何严肃的企业级应用中，这些交互的数量又非常庞大。
现在，让我们看一个完整的示例，以解释 Protocol Buffer 在微服务之间的使用。

演示微服务之间 Protocol Buffer 的场景



您将修改之前演示中使用的同一个示例，以利用 Protocol Buffer。您将使用第 8 章中相同组件的精简版本。因此，此处的示例将由三个主要组件构成：一个基于 HTML 的客户端应用和两个微服务，如图 10-3 所示。同样，我移除了 HATEOAS 和数据仓库的所有复杂性，以便您可以专注于 Protocol Buffer 的使用。

![../images/477630_1_En_10_Chapter/477630_1_En_10_Fig3_HTML.jpg](img/477630_1_En_10_Fig3_HTML.jpg)

图 10-3
在微服务之间使用 Protocol Buffer

在您的示例设计中，您使用了一个 `.proto` 文件，在其中指定了您想要使用的实体。下一步是针对应用程序的编程语言（在您的案例中是 Java），在您的 `.proto` 文件上运行 Protocol Buffer 编译器，以生成类似的类。现在，依赖微服务和独立微服务都可以根据生成的实体类进行编程，从而使使用 protocol buffer 进行微服务间通信成为一个直接的步骤。

在 Spring Boot 中使用 Protocol Buffer 的代码

本节的所有代码示例都在文件夹 `ch08\ch10-02` 中。您将首先查看独立微服务（Product Server 微服务）的代码。请访问 `pom.xml` 以查看 Product Server 微服务中显式提及的 protobuf-java 和 protobuf-java-format 依赖项。请参阅清单 10-16。

```

3.1.0
1.4

com.google.protobuf
protobuf-java
${protobuf-java.version}

com.googlecode.protobuf-java-format
protobuf-java-format
${protobuf-java-format.version}

org.springframework.boot
spring-boot-starter-web

org.springframework.boot
spring-boot-maven-plugin

com.github.os72
protoc-jar-maven-plugin
3.1.0

generate-sources

run

3.1.0
true

src/main/resources

src/main/protobuf

清单 10-16
向 Maven 构建添加 Protocol Buffer 编译器和运行时 (ch10\ch10-02\ProductServer\pom.xml)
```

`protoc-jar-maven-plugin` 使用多平台可执行文件 protoc-jar 执行 protobuf 代码生成。在构建时，此 Maven 插件会检测平台并执行相应的 protoc 二进制文件，以便它可以使用 protoc-jar 嵌入式 protoc 编译器编译 `.proto` 文件，从而在主要平台（Linux、Mac/OSX 和 Windows）之间提供一定的可移植性。在上面的 `pom.xml` 中，编译器在主周期中编译 `.proto` 文件，并将生成的文件放入 target/generated-sources，包括 google.protobuf 标准类型，以及所需的任何其他导入。
protobuf-java Maven 工件提供了在 Java 中序列化生成的消息对象所需的 Java API。因此，它充当 Protocol Buffer 的运行时。
请注意，编译器版本应与 Java API 版本相同。在您的示例中，您使用的是 3.1.0 版本。
protobuf-java-format Maven 工件提供了基于 Google 的 protobuf Message 的不同格式的序列化和反序列化。它允许将默认（字节数组）输出覆盖为基于文本的格式，例如 XML、JSON 和 HTML。

现在，查看清单 10-17 中显示的 Protocol Buffer 类型声明。

```
syntax = "proto3";
package binildas;
option java_package = "com.acme.ecom.product.model";
option java_outer_classname = "ECom";
message Products {
repeated Product product = 1;
}
message Product {
string productId = 1;
string name = 2;
string code = 3;
string title = 4;
string description = 5;
string imgUrl = 6;
double price = 7;
string productCategoryName = 8;
}
清单 10-17
product.proto 中的类型声明 (ch10\ch10-02\ProductServer\src\main\resources\product.proto)
```

由于您使用的是 protocol buffer 编译器和 protocol buffer 语言运行时的版本 3，因此 `.proto` 文件必须以 `syntax = “proto3”` 声明开头。如果使用编译器版本 2，则此声明将被省略。
`.proto` 文件接下来应该有一个包声明，这有助于防止不同项目之间不同类型声明的命名冲突。在 Java 中，除非您像此处一样显式指定了 `java_package`，否则此包名称也用作 Java 包。

接下来是两个 Java 特定的选项：`java_package` 和 `java_outer_classname`。
`java_package` 选项指定生成的类应位于哪个 Java 包中。如果您没有显式指定，它将与包声明给出的包名称匹配。`java_outer_classname` 选项定义了容器类的名称，该容器类应包含类型定义文件中生成的所有类。

接下来，您为要序列化的每个数据结构添加一个消息，然后为消息中的每个字段指定一个名称和类型。消息是一个包含一组类型化字段的聚合体。许多标准的简单数据类型可用作字段类型，包括 bool、int32、float、double 和 string。您还可以将其他消息类型添加为字段类型；在您的示例中，`Products` 消息包含 Product 消息。`= 1`、`= 2`、`= 3` 等是每个元素上的标记，用于标识该字段在二进制编码中使用的唯一“标签”。

字段可以使用以下修饰符之一进行注释：

*   **required**：表示必须提供该字段的值；否则，该消息将被视为“未初始化”。

*   **optional**：表示该字段可以设置，也可以不设置。如果未设置可选字段的值，则使用默认值，例如数值类型为 0，字符串为空字符串，布尔值为 false 等。

*   **repeated**：表示该字段可以重复任意次数（包括零次）。重复值的顺序将在 protocol buffer 中保留。

接下来，查看清单 10-18 中的 Product Server 控制器。

```
package com.acme.ecom.product.controller;
import com.acme.ecom.product.model.ECom.Product;
import com.acme.ecom.product.model.ECom.Products;
@RestController
public class ProductRestController {
@RequestMapping(value = "/products", method = RequestMethod.GET)
public  Products getAllProducts() {
List products = getAllTheProducts();
if(products.isEmpty()){
LOGGER.debug("No products retreived from repository");
}
products.forEach(item->LOGGER.debug(item.toString()));
Products productsParent =
Products.newBuilder().addAllProduct(products).build();
return productsParent;
}
}
清单 10-18
用于发出 Protocol Buffer 的 Product Rest 控制器 (ch10\ch10-02\ProductServer\src\main\java\com\acme\ecom\product\controller\ProductRestController.java)
```

Rest 控制器没有明显的重大区别，除了您需要导入在外部容器类 `ECom` 中定义的 Products 和 Product Java 类型。

您现在需要使用所需的库来装备微服务运行时，以序列化和反序列化为 Protocol Buffer 格式。Spring 的 `ProtobufHttpMessageConverter` 可以方便地在内部利用 Google 的库。此转换器默认支持 `application/x-protobuf` 和 `text/plain`，并使用了官方的 com.google.protobuf:protobuf-java 库。需要注意的是，如果类路径上有正确的附加库，也可以支持其他格式。您将在配置类中实例化此 bean；请参阅清单 10-19。



```
import org.springframework.http.converter.protobuf.ProtobufHttpMessageConverter;
@Configuration
public class ProductRestControllerConfiguration{
@Bean
ProtobufHttpMessageConverter protobufHttpMessageConverter() {
return new ProtobufHttpMessageConverter();
}
}
清单 10-19
配置 Protocol Buffer 消息转换器 (ch10\ch10-02\ProductServer\src\main\java\com\acme\ecom\product\controller\ProductRestControllerConfiguration.java)
```

以上就是 Product Server 微服务的全部内容。接下来，你将看到
Product Web 微服务的代码。之前为 Product Server 微服务展示的所有代码片段以及我给出的解释仍然有效，为简洁起见，此处不再重复；相反，我将解释任何额外的需求。

Product Web 微服务必须将请求委托给 Product Server 微服务。为了使这种委托生效，你需要在配置类中注册一个 `RestTemplate` 类型的 bean。这个 `RestTemplate` 会注入另一个 `ProtobufHttpMessageConverter` 类型的 bean，它将帮助自动转换接收到的 protocol buffer 消息。如清单 10-20 所示。

```
@Configuration
public class ProductRestControllerConfiguration{
@Bean
RestTemplate restTemplate(ProtobufHttpMessageConverter hmc) {
return new RestTemplate(Arrays.asList(hmc));
}
@Bean
ProtobufHttpMessageConverter protobufHttpMessageConverter() {
return new ProtobufHttpMessageConverter();
}
}
清单 10-20
用于调用 Protocol Buffer 的 Rest Template (ch10\ch10-02\ProductWeb\src\main\java\com\acme\ecom\product\controller\ProductRestControllerConfiguration.java)
```

接下来，Product Web 微服务的 Rest Controller 将利用上述 `RestTemplate` 将调用委托给 Product Server 微服务，如清单 10-21 所示。

```
@RestController
public class ProductRestController{
@Autowired
RestTemplate restTemplate;
private static String PRODUCT_SERVICE_URL =
"http://localhost:8081/products/";
@Autowired
public ProductRestController(RestTemplate restTemplate){
this.restTemplate = restTemplate;
}
@RequestMapping(value = "/productsweb", method = RequestMethod.GET,
produces = {MediaType.APPLICATION_JSON_VALUE})
public  Products getAllProducts() {
Products products = restTemplate.getForObject(PRODUCT_SERVICE_URL,
Products.class);
List productsList = products.getProductList();
Products productsParent =
Products.newBuilder().addAllProduct(productsList).build();
return productsParent;
}
}
清单 10-21
将调用委托给输出 Protocol Buffer 的微服务的 Rest Controller (ch10\ch10-02\ProductWeb\src\main\java\com\acme\ecom\product\controller\ProductRestController.java)
```

构建并测试微服务间的 Protocol Buffer
一旦你在 `.proto` 文件中定义了消息结构，就需要一个 protoc 编译器来将这些语言无关的内容转换为 Java 代码。请按照 Protocol Buffer 仓库（ [`https://github.com/google/protobuf`](https://github.com/google/protobuf) ）中的说明来获取合适的编译器版本。
或者，你也可以通过搜索 `com.google.protobuf:protoc` 构件，从 Maven 中央仓库下载预构建的二进制编译器，然后为你的平台选择合适的版本。

接下来，你需要运行编译器，指定搜索 `.proto` 文件的路径、生成代码的目标目录以及 `.proto` 文件的绝对路径。在你的例子中，命令如下：

```
D:\Applns\Google\ProtocolBuffer\protoc-3.4.0-win32\bin\protoc --proto_path .\src\main\resources --java_out .\src\main\java .\src\main\resources\product.proto
```

因为你想要生成 Java 类，所以使用了 `--java_out` 选项；不过，其他支持的语言也提供了类似的选项。
上述步骤很方便，但要将它们无缝集成到 Maven 构建中并不直接。这就是为什么你在清单 10-16 的 `pom.xml` 中使用了 `protoc-jar-maven-plugin` 插件；它使用跨平台的可执行文件 `protoc-jar` 执行 protobuf 代码生成。因此，构建和运行示例无需手动步骤。让我们开始吧。
演示使用 Protocol Buffer 进行微服务间通信所需的完整代码保存在 `ch10\ch10-02` 文件夹中。此示例不需要 MongoDB。你可以按以下顺序构建、打包和运行不同的微服务。

首先启动 Product Server 微服务：

```
cd ch10\ch10-02\ProductServer
D:\binil\gold\pack03\ch10\ch10-02\ProductServer>make
D:\binil\gold\pack03\ch10\ch10-02\ProductServer>rem D:\Applns\Google\ProtocolBuffer\protoc-3.4.0-win32\bin\protoc --proto_path .\src\main\resources --java_out .\src\main\java .\src\main\resources\product.proto
D:\binil\gold\pack03\ch10\ch10-02\ProductServer>mvn -Dmaven.test.skip=true clean package
D:\binil\gold\pack03\ch10\ch10-02\ProductServer>run
D:\binil\gold\pack03\ch10\ch10-02\ProductServer>java -jar -Dserver.port=8080 .\target\Ecom-Product-Microservice-0.0.1-SNAPSHOT.jar
```

上述命令将在端口 8080 上启动 Product Server 微服务。你还想检查通过线路传输的消息格式，因此使用 Apache TCPMon 作为代理。请参阅附录 E 以了解如何将 TCPMon 作为代理启动。

```
cd D:\Applns\apache\TCPMon\tcpmon-1.0-bin\build
D:\Applns\apache\TCPMon\tcpmon-1.0-bin\build>tcpmon 8081 127.0.0.1 8080
```

这将启动 TCPMon，以便所有到达运行 TCPMon 的主机（在你的例子中是 localhost）上端口 8081 的请求，都将被代理到上述情况中主机 127.0.0.1 的端口 8080。见图 10-4。

![../images/477630_1_En_10_Chapter/477630_1_En_10_Fig4_HTML.jpg](img/477630_1_En_10_Fig4_HTML.jpg)

图 10-4
设置为监视请求和响应的 TCPMon 代理

接下来，启动 Product Web 微服务：

```
cd ch10\ch10-02\ProductWeb
D:\binil\gold\pack03\ch10\ch10-02\ProductWeb>make
D:\binil\gold\pack03\ch10\ch10-02\ProductWeb>mvn -Dmaven.test.skip=true clean package
D:\binil\gold\pack03\ch10\ch10-02\ProductWeb>run
D:\binil\gold\pack03\ch10\ch10-02\ProductWeb>java -Dserver.port=8082 -jar .\target\Ecom-Product-Microservice-0.0.1-SNAPSHOT.jar
```

现在，你可以通过指向 URL 来检索基于 HTML 的客户端应用程序来测试应用程序，最好使用 Chrome 浏览器：

```
http://localhost:8082/
```

浏览器客户端在加载时，会向监听在端口 8082 上的 Product Web 发起一个请求，该请求将委托给监听在端口 8081 上的 Apache TCPMon 代理。这个到达运行 TCPMon 的主机（在你的例子中是 localhost）上端口 8081 的请求，将被代理到 localhost 的端口 8080，也就是 Product Server 微服务监听的地方。如果一切按计划进行，你应该能够在网页上看到列出的产品。如果你检查 TCPMon 代理控制台，你应该能够验证两个微服务之间的通信在握手基于 HTTP 的 REST 调用时，使用了 Protocol Buffer 作为线路级协议。

使用 Protocol Buffer 的影响
让我们检查一下在微服务间通信中使用 Protocol Buffer 时的响应内容长度。为了进行比较，你将查看三种场景。

Protocol Buffer 编码



上一节的示例演示了如何使用 Protocol Buffer 进行微服务间通信。你可以观察 TCPMon 代理控制台，应该能够验证两个微服务之间的通信使用了 Protocol Buffer。它还会显示响应内容长度为 265（这是一个代表性数值）。请参见图 10-5。

![../images/477630_1_En_10_Chapter/477630_1_En_10_Fig5_HTML.jpg](img/477630_1_En_10_Fig5_HTML.jpg)

图 10-5 使用 TCPMon 检查 Protocol Buffer 编码

XML 编码

你可以使用 Chrome 直接访问 TCPMon 代理，这样客户端在内容协商期间就不会显式请求 Protocol Buffer，只需使用 URL `http://localhost:8081/products/`。这将显示 XML 格式的响应；请参见图 10-6。

![../images/477630_1_En_10_Chapter/477630_1_En_10_Fig6_HTML.jpg](img/477630_1_En_10_Fig6_HTML.jpg)

图 10-6 使用 TCPMon 检查 XML 编码

你可以使用任何工具检查内容大小（我在图 10-7 中使用了 Microsoft Word）。

![../images/477630_1_En_10_Chapter/477630_1_En_10_Fig7_HTML.jpg](img/477630_1_En_10_Fig7_HTML.jpg)

图 10-7 使用 XML 编码时的内容长度

JSON 编码

强制微服务调用之间使用 JSON 编码的一种简单方法是稍微修改 Product Server 微服务中的应用程序代码。请参见代码清单 10-22。

```
@RequestMapping(value = "/products", method = RequestMethod.GET,
produces = {MediaType.APPLICATION_JSON_VALUE})
//@RequestMapping(value = "/products", method = RequestMethod.GET)
public  Products getAllProducts() {
List products = getAllTheProducts();
Products productsParent =     Products.newBuilder().addAllProduct(products).build();
return productsParent;
}
代码清单 10-22 强制使用 JSON 编码 (ch10\ch10-02\ProductServer\src\main\java\com\acme\ecom\product\controller\ProductRestController.java)
```

现在，你可以重新构建并重启 Product Server 微服务，然后通过访问 `http://localhost:8082/` 重新运行客户端应用程序。

如果你检查 TCPMon 代理控制台，应该能够看到在这种情况下选择了 JSON 编码，如图 10-8 所示。

![../images/477630_1_En_10_Chapter/477630_1_En_10_Fig8_HTML.jpg](img/477630_1_En_10_Fig8_HTML.jpg)

图 10-8 使用 TCPMon 检查 JSON 编码

你可以再次使用任何工具检查内容大小（参见图 10-9）。

![../images/477630_1_En_10_Chapter/477630_1_En_10_Fig9_HTML.jpg](img/477630_1_En_10_Fig9_HTML.jpg)

图 10-9 使用 JSON 编码时的内容长度

现在，你可以比较这三种场景，了解在微服务间通信使用 Protocol Buffer 时响应大小的变化。

总结
微服务就像一把双刃剑：它们提供了额外的杠杆作用，但应谨慎使用。这主要是由于第 4 章讨论的架构反转。你学习了两种可以引入微服务架构的重构过程，以更大程度地影响性能。还有更多的优化方法；不过，我将它们限制在本章讨论的两种方法之内。本书剩余部分的大部分内容将讨论基于事件的微服务，其中微服务间通信的异步特性默认通过消息传递而非 HTTP 来利用。你将在下一章开始深入了解这些方面。

11. 事件、最终一致性与
一致性

与同事握手，你会立即感受到同样的握手反馈。与伴侣拥抱或亲吻也是如此——你也会立即得到同样的回应（假设你伴侣的心情不会招来一记耳光作为回应！）。如果在上述任何场景中，你的伴侣不在你身边，但你仍然想向他或她传递类似的问候，该怎么办？你可以通过当今可用的任何数字媒体发送电子邮件或消息。你的伴侣（迟早）会收到你的消息，并能够回复你。最终的效果是，即使你的伴侣不在附近，甚至在你发起问候的确切时刻没有听到你的声音，你迟早也会得到回应的问候。这些场景和问候也典型地适用于微服务应用程序。一旦你根据领域边界将应用程序拆分为可以独立于其他微服务进行部署、启动和运行的微服务，这也意味着这些微服务应该有能力继续提供功能，而不管其对应的微服务如何。这就是必须讨论事件驱动架构（EDA）重要性的地方。在本章中，你将了解 EDA 的一般概念以及一些选定的关注点。最后，你将能够理解事件和 EDA，并更好地决定是希望在微服务之间使用异步事件还是同步请求/响应风格的交互。

在本章中，你将学习以下内容：

*   EDA 简介

*   EDA 的不同风格

*   EDA 在微服务架构演进中的相关性

*   EDA 在使不同微服务最终一致中的作用

*   规模立方体与分区计算活动的需求

*   CAP 定理与 CAP 金字塔

*   BASE 事务

事件驱动架构
事件驱动架构是一种软件架构模式，它促进事件的产生、检测、消费和响应。一个事件驱动系统通常由事件发射器（或代理）、事件消费者（或接收器）以及事件传播所通过的事件通道组成。让我们详细了解这些组件。

事件
事件是特定系统或领域内发生或出现的事情；它是该领域中已经发生或被认为已经发生的事情。
事件可以定义为状态的变化。例如，当订单发货时，订单的状态从“待处理”变为“已发货”。电子商务微服务架构可以将此状态变化视为一个事件，其发生可以通知给架构内的其他微服务，甚至微服务边界之外的其他组件。从形式上讲，被产生、发布、传播、检测或消费的是（通常是异步的）消息，称为事件通知，而不是事件本身。如果是这样，事件就是触发消息发出的状态变化。事件不会传播；它们只是发生。然而，术语“事件”和“事件通知”通常互换使用，以表示通知消息本身。



EDA 组件
基于 EDA 的应用程序通常由事件发射器（源或代理）、事件消费者（接收器）和事件通道组成。事件发射器负责生成、检测、收集和传输事件。当事件发射器执行此操作时，它不知道事件的消费者，甚至不知道是否存在任何消费者，即使存在，也不知道事件如何、何时或在何处被用于进一步处理。

事件接收器负责在感知到感兴趣的事件时立即做出反应。接收器提供的反应可能是完整的，也可能是部分的。如果反应是部分的，那么同一事件可能也会引起其他接收器的兴趣，并且它们可能会补充该反应。

事件通道
是事件从事件源传输到事件接收器的管道。将事件正确分发到正确接收器所需的信息仅存在于事件通道内部。然而，
这种具有分发知识的事件通道在寻址方面相当僵化，在物理层面上，它们通常由消息队列表示。换句话说，这种事件通道的物理实现可以基于传统组件，例如使用点对点通信的面向消息的中间件，通常由消息队列表示。

图 11-1 展示了
通常利用消息队列的点对点事件处理方式。当你使用队列时，
事件源和接收器是紧密耦合的，如图 11-1 所示。更好的方法是使用主题来表示这些管道。在这里，事件通道几乎不包含关于将事件分发到何处的信息；相反，由事件接收器自行连接到感兴趣的事件通道，并在有感兴趣的事件可用时得到通知。

![../images/477630_1_En_11_Chapter/477630_1_En_11_Fig1_HTML.jpg](img/477630_1_En_11_Fig1_HTML.jpg)

图 11-1.
点对点
EDA

后一种方法在微服务应用的可扩展性方面更加灵活，稍后你将看到详细信息。图 11-2 展示了事件源和接收器相对于消息管道（此处为消息主题）的关系相对灵活。

![../images/477630_1_En_11_Chapter/477630_1_En_11_Fig2_HTML.jpg](img/477630_1_En_11_Fig2_HTML.jpg)

图 11-2.
发布-订阅
EDA

在图 11-2 中，队列已被主题取代。^(¹⁹) 消息主题提供了一种发布-订阅消息模式，其中消息的发送者（称为发布者）不会将消息直接编程发送给特定的接收者（称为订阅者），而是将发布的消息分类，而不知道可能存在哪些订阅者。同样，订阅者表达对一个或多个类别的兴趣，然后接收感兴趣的消息，而不知道存在哪些发布者。这提供了在运行时动态地从架构中添加和/或移除源（发布者）和接收器（订阅者）的选项，如图 11-3 所示。

![../images/477630_1_En_11_Chapter/477630_1_En_11_Fig3_HTML.jpg](img/477630_1_En_11_Fig3_HTML.jpg)

图 11-3.
灵活的
发布-订阅 EDA

如图 11-3 所示，在遵循发布-订阅模式时，管道本身是开放且持续的，这意味着它可以在未来运行时容纳更多的接收器。

微服务与事件驱动架构
在讨论了 EDA 的点对点和发布-订阅模式之后，现在让我们来看看在设计采用灵活发布-订阅模式的微服务时，你将面临的一些问题。

微服务的演进
大多数软件应用程序在构建之初都考虑了一组需求，然后在几年内自然演进。在应用程序最终退役之前，会持续多年地纳入变更请求和新需求。传统或单体应用程序需要在源代码层面进行修改以适应这些变化，然后整个应用程序必须进行回归测试、性能验证并重新部署。这也不可避免地会导致停机。

图 11-4 展示了一个使用发布-订阅微服务模式的电子商务应用。

![../images/477630_1_En_11_Chapter/477630_1_En_11_Fig4_HTML.jpg](img/477630_1_En_11_Fig4_HTML.jpg)

图 11-4.
发布-订阅微服务

参考图 11-4，微服务之间的交互如下：

1.  订单服务创建一个处于待处理状态的订单，并发布一个 OrderCreated 事件。
2.  库存服务接收该事件，并尝试根据该订单更新商品库存。然后，它发布一个 InventoryUpdated 事件或一个 NotEnoughInventory 事件。
3.  如果发生 InventoryUpdated 事件，则订单状态必须从待处理更改为已确认，这将再次触发一个 OrderConfirmed 事件。
4.  配送服务接收 OrderConfirmed 事件，并安排产品配送。一旦安排完成且商品已发货，它会触发一个 OrderShipped 事件。

到目前为止，一切顺利，但假设该电子商务公司希望提升客户体验，作为其中的一部分，IT 部门希望增强应用程序，通过电子邮件和其他渠道向客户发送即时通知。解决方案是引入一个名为通知服务的新微服务。
由于你基于发布-订阅模式设计了应用程序，你应该能够在不对应用程序造成任何停机的情况下引入新的微服务。换句话说，你应该开发、测试、基准测试性能、建立通知微服务的基线，并将其“热”部署到现有的应用程序基础设施中。一旦部署完成，通知微服务将开始消费 OrderConfirmed 事件，以即时通知客户其订单已确认。类似地，它也可能开始消费 OrderShipped 事件，以即时通知客户其订单已发货。如图 11-5 所示。

![../images/477630_1_En_11_Chapter/477630_1_En_11_Fig5_HTML.jpg](img/477630_1_En_11_Fig5_HTML.jpg)

图 11-5.
微服务应用的
演进

你所看到的是无缝演进。你无需触碰或干扰任何现有的微服务；然而，你可以挂接或附加越来越多的微服务，以显著改变应用程序的行为或功能！现在你应该能更好地理解第 3 章“可扩展微服务”一节中提到的蜂巢类比，该类比在图 11-6 中再次呈现。

![../images/477630_1_En_11_Chapter/477630_1_En_11_Fig6_HTML.jpg](img/477630_1_En_11_Fig6_HTML.jpg)

图 11-6.
微服务蜂巢类比



图 11-6 展示了蜜蜂如何建造蜂巢。它从建造第一个蜂房开始，然后不断向蜂巢中添加越来越多的蜂房。因此，蜂巢会随着时间的推移而生长。建造蜂房所使用的材料可能并不相同；它们会根据建造该蜂房时可获得的材料而有所差异。然而，每个新蜂房都能与整个蜂巢完美融合，并且整个蜂巢持续发挥其功能而不受干扰。类似地，你可以构建像上述通知微服务这样的新功能，该服务将发送基于电子邮件和短信的订单状态通知，从而无缝地将这一增强功能添加到应用程序中，且不会对现有（微服务）应用程序造成任何停机时间。^(²⁰)

最终一致性与微服务

现在，让我们来探讨微服务中一些有趣的复杂细节。单体应用通常使用单个关系型数据库。使用关系型数据库的一个关键优势在于，你的应用程序可以利用 ACID 事务。图 11-7 描绘了一个典型的单体应用。

![../images/477630_1_En_11_Chapter/477630_1_En_11_Fig7_HTML.jpg](img/477630_1_En_11_Fig7_HTML.jpg)

图 11-7. 一个电商单体应用

你领域中的多个实体可以由单个关系型数据库中一个模式内的不同表来表示。在当前示例中，`Order`、`Item` 和 `Inventory` 就是这样的实体。客户会将多个商品添加到购物车，最后通过单击一个按钮进行结账。此时，`Order` 表中会创建一行新记录来表示一个新订单。以该订单 ID 作为外键，会在 Items 表中创建与购物车中商品数量相同的行。此外，对于 `Item` 表中创建的每一行，`Inventory` 表中相应行的数量值必须递减。如果你使用单个关系型数据库，上述所有操作都可以以符合 ACID 的方式全部提交或全部回滚。

图 11-8 展示了借助微服务实现的相同业务用例。假设订单和库存是两个不同的微服务。现在，让我们分析一个复杂场景：两个用户试图大致同时地对同一个实体进行操作。这类场景在业务应用中很典型，这里列举了其中几个：

![../images/477630_1_En_11_Chapter/477630_1_En_11_Fig8_HTML.jpg](img/477630_1_En_11_Fig8_HTML.jpg)

图 11-8. 微服务竞态条件

*   航空公司订票应用中的最后一个座位
*   酒店预订门户中的最后一个房间
*   电商应用中某特定产品的最后一件商品

假设身处地球两端的乔和安都对一款较旧版本的 MacBook Pro 感兴趣，该产品在电商网站上正以大幅折扣出售。同时假设该商品在电商网站上仅剩一件待售。乔和安都进行了搜索、查看，并最终将该商品添加到了各自的购物车。现在假设他们俩大致同时点击了“确认购买”按钮（图 11-8 中的标签 1 和 2）。一个基于 EDA 的微服务架构（如图 11-8 所示）将允许订单微服务代表乔和安各自创建订单，并发出 OrderCreated 事件。两个订单的状态现在都是“新建”。你可以进一步假设有两个 OrderCreated 事件大致同时被提交（标签 3 和 4）。库存微服务实例将消费这些 OrderCreated 事件；同样，假设此事件消费是并发进行的。由于这两个 OrderCreated 事件都指向同一件商品，即使微服务尝试并发处理这些 OrderCreated 事件，在库存数据库管理层，也只会允许代表其中一个 OrderCreated 事件的库存更新继续进行，因此库存将减少到零，并发出一个 InventoryUpdated 事件（标签 5）。代表另一个 OrderCreated 事件进行处理的库存微服务无法成功，因为库存不足，所以它将发出一个 NotEnoughInventory 事件。订单微服务将同时消费 OrderCreated 事件和 NotEnoughInventory 事件。订单微服务在消费 OrderCreated 事件时，会将相应订单的状态更改为“已确认”；然而，在消费 NotEnoughInventory 事件时，它会将相应订单的状态从“新建”更改为“已取消”或“无法履行”等。这里解释的场景是完全正常且可接受的，那么问题出在哪里呢？仔细审视会发现，在一段时间内，代表乔和安创建的两个订单都处于“新建”或“已创建”状态。此时，两个用户都收到订单正在被处理的反馈；然而，其中一人稍后会感到失望。系统向用户提供了一个“虚假的”快照，而非“事实的”快照。（不过，没有买到商品的人会获得全额退款。）

如果你回头看看图 11-7 中描述的架构，你会意识到，如果乔和安试图购买那最后一件商品，那么对于其中一人，事务会成功，而对于另一人，整个事务会被原子性地回滚，所有步骤（要么全部成功，要么全部回滚）作为一个整体单元，因此在任何时刻系统都是一致的，并且不会出现后来的失望。换句话说，要么是即时的失望，要么是即时的满意；不存在最终失望或最终满意的可能性。现在关键问题来了：当你的架构从图 11-7 所示变为图 11-8 所示时，这种最终性对于业务来说是否可以接受？

微服务与 CAP 定理

这个讨论引出了分布式计算中另一个有趣的约束，即 CAP 定理，本节将在微服务的背景下对其进行探讨。

可伸缩立方体



高事务性系统必须在单位时间内处理数百万个请求，因此这类系统的每秒事务处理量（TPS）非常高。当我们讨论**Web 规模架构**时尤其如此。通过提升处理请求节点的计算能力，我们可以增加吞吐量；这被称为**垂直扩展**。然而，存在一个极限，超过该极限后，在同一节点上增加计算能力将无法带来成比例的性能提升，如图 11-9 所示。

![../images/477630_1_En_11_Chapter/477630_1_En_11_Fig9_HTML.jpg](img/477630_1_En_11_Fig9_HTML.jpg)

**图 11-9. 垂直扩展**

通过**水平扩展**技术可以进一步提升吞吐量。在 Web 规模下运行软件需要分发软件运行时的多个副本。这也意味着数据集的多个副本分布在许多节点上。

![../images/477630_1_En_11_Chapter/477630_1_En_11_Fig10_HTML.jpg](img/477630_1_En_11_Fig10_HTML.jpg)

**图 11-10. 扩展立方体**

我们来谈谈**扩展立方体**，它涉及三维扩展。图 11-10 描绘了三维扩展，其中每个轴代表在不同层面扩展你的应用程序：

*   **X 轴（水平扩展）**：在此，你复制同一个微服务的多个实例，并使用负载均衡器来分发流量。

*   **Y 轴（功能分解）**：微服务是 Y 轴扩展的一种方法，你根据功能拆分应用程序组件。

*   **Z 轴（数据分区）**：在此，每个微服务或功能可以根据某些标准进一步拆分。一个 Web 规模的应用可以分发流量，例如，所有姓氏以 A 到 G 开头的请求发送到服务器集群 X，H 到 O 发送到服务器集群 Y，P 到 Z 发送到服务器集群 Z。这种技术称为**名称分片**。

每种扩展方法都必须解决数据一致性问题。在企业应用中，你有**主数据**（或查找数据）和**事务数据**。主数据或多或少是静态的，而事务数据则随着事务不断改变状态。X 轴、Y 轴和 Z 轴扩展依赖于网络来让分布式服务共享数据。因此，你可以在每个轴上分发数据和服务的多个副本以增加可扩展性，或者让多个节点托管服务以提高可用性。一旦你拥有多个节点，就需要网络进行互联，因此网络提供了**分区容错性**。此外，如果你拥有数据或代码（服务运行时）的多个副本，就需要解决数据一致性问题。我将在下一节对此进行更详细的描述。

**CAP 定理**

CAP 定理描述了三个相互依赖的架构属性：

*   **一致性**

*   **可用性**

*   **分区容错性**

该定理指出，你最多只能保证这三个属性中的两个。如果你增加或提升其中一个属性，就必须以牺牲另一个（或两个）属性为代价。换句话说，你可以拥有一个高可用、一致的系统，但其分区容错性会很低；或者你可以拥有一个高可用、可分区的系统，但很可能需要放弃一致性。

图 11-11 以图形方式描绘了 CAP 定理。它表明，你可以从 C、A 和 P 中选择任意两个，但无法同时实现所有三个。让我们更好地理解这意味着什么。首先，我们来看 CAP 定理中 C、A 和 P 的定义。

![../images/477630_1_En_11_Chapter/477630_1_En_11_Fig11_HTML.jpg](img/477630_1_En_11_Fig11_HTML.jpg)

**图 11-11. CAP 定理**

*   **一致性**：所有微服务节点对数据拥有相同的视图。

*   **可用性**：每个微服务在任何时候都可以读取或写入任何其他微服务。

*   **分区容错性**：即使微服务之间存在物理网络分区，整个微服务应用程序也能正常工作。

像 Oracle 这样的关系型数据库在提供一致性方面表现出色，因为它们专注于 ACID 属性，并通过提供对事务隔离的精细控制来实现一致性。因此，在之前的电子商务示例中，当客户 Joe 确认购买某产品的最后一件库存商品，并同时将库存减少一件时，任何中间状态都对客户 Ann 隔离，Ann 必须等待几毫秒或几微秒，直到 RDBMS 表或行锁被释放，数据存储达到一致状态。
可用性意味着就事务而言，服务能够完全正常运行。在企业应用中，如果某个服务实例宕机，会使用集群感知的代理或客户端存根回退到冗余实例。Oracle 的**真正应用集群（RAC）** 在此值得关注，因为它坚定地承诺同时提供磁盘上数据的一致性和运行时进程中 DBMS 服务的可用性。一旦你能够使用第 9 章“数据或存储冗余”一节中提到的技术将数据位传输到磁盘级别，你就可以确信你的数据位足够持久。然而，该节中提到的技术会增加形成分区的可能性！
当你使用 SQL 客户端并在运行 Oracle 数据库的同一节点上发出“Select for Update”^((21)) 或类似命令时，你的节点充当一种**原子处理器**，要么正常工作，要么不工作（即，如果崩溃了，它不可用，但也不会导致数据不一致）。当你开始为了可扩展性或可用性而将处理逻辑与数据分离并分布到不同节点时，就存在形成分区的风险。例如，当网络交换机故障、网线被拔出或电源故障导致你的其他节点不可用时，就会发生分区。
在设计微服务以应对 CAP 定理带来的限制时，必须进行架构权衡。如果你必须防止网络故障，你可以设计没有分区的系统。为此，你需要将与某个事务相关的所有内容放在一台机器上，或者放在一个原子故障单元（如单个机架）中。啊，这听起来熟悉吗？我们是在谈论“一个微服务”吗？你已经看到，微服务根据定义是一个独立可部署、自包含的单元，它拥有并管理自己的数据！接下来还有一个注意事项：即使是一个机架也可能发生部分故障。此外，微服务本身也需要扩展，这意味着你需要考虑冗余。而且，如果单个微服务的水平层必须最佳部署以满足 SLA，那么分区的需求就会再次出现。是的，你的设计思路在兜圈子，必须考虑你的应用程序特性、业务上下文以及许多其他技术和文化因素，才能在上述循环连续体中确定你的微服务架构的最佳平衡点。

**BASE（基本可用、软状态、最终一致性）系统**



在探讨了 CAP 定理，并假设你已经熟悉常见且完全一致的 ACID 事务之后，让我们来看看 ACID 的替代方案 BASE。BASE 是 **B**asically Available（基本可用）、**S**oft state（软状态）、**E**ventually consistent（最终一致性）的缩写，它与 ACID 原则截然相反。ACID 基于悲观假设，并在每次操作结束时强制保证一致性；而 BASE 则基于乐观假设，并接受数据库一致性会处于一种动态变化的状态，其程度需符合所考虑的业务事务的可接受水平。BASE 设计鼓励以如下方式设计系统：

*   如果应用程序的一部分宕机或运行不正常，应用程序的其他一些功能仍应能正常工作，或者
*   如果客户数据库的少数节点故障仅影响了该特定主机上 30% 的客户，那么其余客户仍应能够使用该系统。
*   等等。

如果你为系统或服务的协调定义如下一组属性，那么上述所有或大部分特性都可以现实地实现：

*   系统在支持部分故障时是**基本可用**的，这比整个系统完全故障更可取。
*   系统的状态是“软”的，这意味着即使在没有进一步更新的情况下，它也可能随时间变化，因为一些过去的更改尚未应用，它们“仍在传输中”，源自其他分区。
*   如果系统不再有新的更新，系统最终将变得**一致**。

CAP 金字塔

CAP 金字塔是一个倒置的四面体^(²²)，以其顶点为支点，相对的底面保持与支点平面（地面）平行，如图 11-12 所示。

![../images/477630_1_En_11_Chapter/477630_1_En_11_Fig12_HTML.jpg](img/477630_1_En_11_Fig12_HTML.jpg)

图 11-12. 处于不稳定平衡状态的 CAP 金字塔

如果我们假设 CAP 金字塔顶面每个角（分别代表一致性、可用性和分区容错性）到支点平面的高度，那么在平衡状态下，这三个特性之间将始终存在一种平衡，这种状态就是 BASE 状态。在 BASE 状态下，这三个特性既不会太低，也不会太高；相反，它们处于一个标称水平，以保持 CAP 金字塔处于不稳定平衡状态。^(²³)
CAP 定理指出，你可以从一致性、可用性和分区容错性中选择任意两个，但无法同时实现所有三个。

在图 11-13 中，金字塔被倾斜，使得靠近倾斜点的角更接近支点平面，而倾斜点对面的角则远离支点平面。

![../images/477630_1_En_11_Chapter/477630_1_En_11_Fig13_HTML.jpg](img/477630_1_En_11_Fig13_HTML.jpg)

图 11-13. 被扰动的 CAP 金字塔

如图 11-13 所示，在不改变支点的情况下，你一次最多可以尝试增加任意两个特性。这里我尝试以提高原子性和分区容错性（标签“y > x”）为代价，降低一致性（标签 x）。

当你扰动 BASE 状态的不稳定平衡时，以下任意两个特性都可以按你的意愿得到改善：

*   系统可以从基本可用性改变为五个九的高可用性。
*   系统的状态是“非软”的，这意味着它不会随时间变化，因为所有更新都是原子性应用的，并且预计不会有“仍在传输中”的更新。
*   系统始终是一致的，因为预计不会有新的更新。

现在，你应该能够将此与微服务架构的核心设计原则联系起来。例如，即使你的订单微服务运行不正常，客户仍应能够搜索和浏览产品列表和产品详情页面。因此，当 BASE 通过利用多个分区的微服务来允许（部分）可用性时，就需要识别出可以放宽一致性的机会。诚然，从用户体验的角度来看，这种一致性的放宽无法完全隐藏。更明确地说，当你的订单微服务和库存微服务被分区时，有可能即使在库存中最后一件商品已被确认 Joe 的购买订单后，Ann 仍可能将该商品添加到她的购物车并确认结账。她最终才会知道这件特定商品已无库存。从微服务设计的角度来看，这些是暂时的不一致，最终会变得一致，因此 Ann 暂时的“不悦”最终也会转化为“满意”，因为系统要么会在付款前通知她该商品已不可用，要么在最坏的情况下（即已为不可用商品付款），将会启动退款流程。
在实际进行系统设计和实现时，还有更多复杂之处，但让我们先将其搁置，直到第 13 章关于事务的内容中再讨论。

总结
架构关乎权衡，而在微服务架构中，明智的权衡尤为重要。你在第 4 章中看到了微服务上下文中的架构反转，这已将外部架构的复杂性提升到了指数级。这使得网络变得不可避免，因此，实现分区容错性的技术必须通过设计内置于架构中，而不是事后才考虑。分区容错性的程度会影响应用程序的可用性和一致性，而在这三个特性之间决定平衡是设计微服务应用程序的关键。在第 5 章中，你在 CQRS 模式中分离了应用程序的命令和查询部分，这是在架构中引入分区的一个很好的例子。现在是时候通过一些实际代码来解释这些概念了，请转到第 12 章。

脚注

12. 用于 CQRS 架构的 Axon



在第 11 章中，你学习了事件和事件驱动架构，并了解了任何基于 EDA 的应用程序可以运行的理论和约束。微服务架构天生就引入了分区，因此实现分区容错的具体机制至关重要。在本章中，你将借助 Axon 来实现这一点。Axon 是一个轻量级框架，可帮助你遵循 CQRS 模式开发应用程序。Axon 框架采用 Apache 2 许可证授权，因此你可以在任何应用程序中免费使用该框架。不过，如果你需要围绕 Axon 的专业服务，则由总部位于阿姆斯特丹的 AxonIQ B.V.提供。

构建基于 CQRS 的系统并非必须使用 Axon，使用 Axon 构建系统也并非必须采用 CQRS 模式。然而，任何新框架都是你应用程序栈中的额外一层债务，因此你在应用程序中使用 Axon 的主要原因之一，可能是你渴望在构建应用程序时遵循 CQRS 模式。再次强调，CQRS 并非你设计微服务架构的必备前提；但是，CQRS 为解决分布式企业软件系统的可扩展性问题提供了杠杆，因此 Axon 和微服务是相辅相成的。

你在第 7 章和第 8 章中已经深入学习了 Spring Boot 和 Spring Cloud，在本章使用 Axon 时，你将大量利用这两者。这种 CQRS 与 Spring Cloud 的结合，将让你同时拥有来自两个世界的最强大工具。

在本章中，你将完成以下内容：

*   认识 Axon，Java CQRS 框架
*   理解 Axon 的构建模块
*   构建并运行一个入门级的 CQRS 示例
*   构建并运行一个完全分布式的 CQRS 微服务示例

介绍 Axon，CQRS 框架
在开源社区和商业世界中，有相当多的框架允许你设计基于 CQRS 的解决方案，涵盖 Java 和.NET 平台。我选择 Axon 是因为其开源许可证的免费性质，以及它与其他主流语言和平台（包括 Java 和 Spring）的集成选项。

在本章中，你将看到同时使用 Axon 和 Spring Boot 的工作示例。在介绍示例时，我还会涵盖基本概念和 API。建议你参考 Axon 文档以详细了解框架的用法，因为我不会在此尝试复制 Axon 文档。相反，我将通过示例带你了解核心概念，以便你能够快速上手。

对于这些示例，你将使用两个不同版本的 Axon。你首先会使用 Axon 2.4.1。在这里，你需要通过注解或使用 XML 配置来显式地连接组件。此外，Axon 2.4.1 示例将利用 Spring Boot 的 1.3.5.RELEASE 版本。在本书后面，你还会看到使用 Spring Boot 1.5.3.RELEASE 版本的 Axon 3.0.5 示例。在 Axon 3.0.5 中，可以避免许多显式连接，因为 Axon 使用 Spring 应用上下文来定位构建模块的具体实现，并为那些不存在的模块提供默认实现。尽管这会让开发人员的生活更轻松，但如果你不是经验丰富的 Axon 开发人员，你可能无法理解底层发生了什么。在 Axon 2.4.1 中，大部分连接是显式的，因此在代码中作为注解或在配置中作为 XML 可见，这样你可以更好地理解底层发生的情况。因此，你将首先使用 Axon 的旧版本（2.4.1）^(²⁴)来开始示例。不过，我也会通过提供相同版本的示例，为你提供一条通往后续 Axon 版本^(²⁵)的简易路线图。

什么是 Axon？
Axon 框架帮助开发人员应用 CQRS 架构模式，并构建模块化的、基于 SOA 的解决方案。传统的 SOA 架构解决了软件架构中的一些主流问题；然而，Axon 填补了过去二十年使用 SOA 过程中发现的空白。Axon 提供了最重要构建模块的实现，例如聚合、仓库和事件总线（事件的调度机制）。聚合负责将实体分组到父子层次结构中，并明确规定聚合根的责任，以在数据一致性和同步方面管理实体树的生命周期。事件总线帮助你省去创建、维护和跟踪应用程序不同部分之间关联 ID 的所有繁琐细节。Axon 保证将事件传递给正确的事件监听器，并并行以及按预期顺序处理它们。此外，Axon 使用 Java 注解支持，这意味着你可以在不将代码绑定到 Axon 特定逻辑的情况下构建聚合和事件监听器。

在哪里可以使用 Axon？
Axon 是免费且开源的，其标语是*The Jargon CQRS*（你的许多同行仍在互联网上搜索以理解其含义），这足以长期保持管理层的关注，那么为什么不在你构建的每个新应用程序中使用它呢？不幸的是，与传统的点对点和同步架构风格相比，CQRS 并不简单直接。在架构设计之初就需要在一定程度上预见未来的复杂性；此外，与传统的 SOA，甚至与没有 CQRS 的微服务相比，你的设计必须容纳和演化更多场景、状态和流程的组合。然而，如果你理解这一点并尝试去做，那么付出的努力将以多种方式得到回报。以下是一些 CQRS 更有意义的场景：

*   **可扩展架构**：典型的应用程序在设计时都会考虑其生命周期，比如 10 年、20 年甚至 50 年（如果你还在用汇编或大型机编程的话）；然而，在这段时间内，许多此类应用程序很可能会通过增加更多功能来进行扩展。今天构建的应用程序也是如此，它们被部署到云和容器中。但是，业务连续性至关重要，无论是功能方面的扩展还是系统质量（QoS）方面的扩展，都必须在不对运行系统造成任何中断的情况下实现。你能做到吗？这种期望是现实的还是仍然是一个神话？嗯，答案不仅仅取决于可用的技术和框架。你还需要将它们与你的架构思想结合起来，并搁置 DevOps 等其他方面的所有进步。



*   **高读写比**：回顾第 5 章关于浏览-预订比率的讨论。一个系统在将一次写入事务转化之前，必须容纳成百上千次读取事务。在当今的企业软件范式中，尤其是在云原生场景下，将操作服务质量（QoS）同时应用于读取和写入事务将是灾难性的，因为每一个 CPU 周期、每一比特的存储或传输都会增加成本。你的读取事务是**领先**事务，而写入事务是**滞后**事务。前者将“意图”转化为“购买决策”，后者则将“库存”转化为“已实现利润”。两者都很重要，但关键性不同。因功能或系统问题导致的写入事务失败代价高昂，而读取事务失败的影响则较小，因为它可以重试或忽略；与失败的写入事务相比，其价值损失要低得多。用于查询的数据源应与用于命令执行的数据源采用不同的调优方式，并且应针对快速查询进行优化，采用资源上的宽松锁；然而，对于写入事务，你的数据源应确保严格的 ACID 合规性，这样一旦旅客成功购买了飞机上的最后一个座位，你就有所有手段来尊重这笔购买，从而无需事后向该旅客发送一封遗憾邮件，告知他由于某种原因该座位不可用，因此将为他办理退款！

*   **单一事实源，多种视图**：“资源”最初在万维网上被定义为通过 URL 标识的文档或文件。然而，如今它们有了更通用和抽象的定义，涵盖了在网络上可以以任何方式被识别、命名、寻址或处理的任何事物或任何实体。在 RESTful Web 服务中，对资源 URI 的请求将产生一个响应，该响应可以是 CSV、XML、HTML、JSON 或其他格式。应用程序中的相同数据可能以多种方式呈现，有些针对快速搜索进行了优化，有些处于缓存状态以实现最大响应速度，还有一些则以预定义报告的形式呈现，如 Excel 或 PDF 文件。这就是以多种视图存在的单一事实源在应用层面的现实。CQRS 明确地解决了这一需求，并且还帮助你在发生变更时，通过引发事件的形式，使不同的视图与单一事实源保持同步。

*   **具有多样化交互的应用程序**：某些应用程序的用户群与系统的交互程度各不相同。例如，一个电子商务应用程序拥有终端用户或客户进行零售购买，这是用户群的一部分，数量庞大；而且，这些客户期望毫秒级的响应时间。另一部分是管理库存的后台店员，其数量有限，如果软件偶尔运行缓慢或卡顿，他们会感到困扰。第三部分是与供应链连接的 B2B 接口，其数量更少，表现为少数已知的 IP 地址，如果接口中断几分钟，他们也会受到影响，因为之后他们必须重试。根据交互方的类型以及代表这些方进行的交易的整体价值和关键性，同一应用程序内的软件服务可能必须表现出不同级别的访问权限、安全性、性能等。微服务架构将在这方面为你提供帮助，而采用 CQRS 将为你提供额外的优势，以应对这些异构类型的交互。

*   **具有自治接口的应用程序**：物联网（IoT）和万维物联网（WoT）是当前的软件范式，它们给软件架构师带来了一系列新的挑战。使用命令和事件对应用程序 API 进行严格定义，使得与这些物和设备的接口集成变得更加容易。任何接口都可以发送命令或监听主机接口生成的事件。

以上列表仅提供了 CQRS 能够增值的问题特征的一般性列表；它绝非详尽无遗。

运行 Axon 所需的条件
Axon 框架是基于 Java 6 构建和测试的，因此这基本上是其唯一的要求。你可以使用 Gradle 或 Maven 进行构建，也可以使用任何你喜欢的 IDE。由于 Axon 本身不创建任何连接或线程，因此在应用服务器上运行是安全的。Axon 通过使用执行器（executors）来抽象所有异步行为，这些执行器将任务提交与每个任务如何运行的机制解耦。因此，你甚至可以轻松地传入一个由容器管理的线程池。如果你不使用应用服务器（例如 Tomcat、Jetty 或独立应用程序），你可以使用`Executors`类或 Spring 框架来创建和配置线程池。
现在，你将看到一些具体的示例，并了解 Axon 代码的实际应用。

在同一个 JVM 中处理命令和事件
先从简单的事情开始，所以让我们把 Axon 的第一个示例保持简单。然而，我无法让它更简单了。为了介绍 Axon，你必须首先理解一个 Axon 方式的简单场景，然后了解 Axon 中每个步骤的细节。

示例场景

第一个 Axon 示例来自一个电子商务应用程序的单个微服务。领域中有两个实体：订单和产品。用户可以购买产品，这将创建一个新订单。当新订单创建时，产品库存将减少。仅此而已。图 12-1 描绘了带有完整流程标注的架构。

![../images/477630_1_En_12_Chapter/477630_1_En_12_Fig1_HTML.jpg](img/477630_1_En_12_Fig1_HTML.jpg)

图 12-1. 在同一个 JVM 中处理 Axon 命令和事件

第 5 章中的“基于事件的 CQRS 设计元模型”一节展示了一个基于 CQRS 架构的元模型。图 12-1 是第 5 章中图 5-5 所表示的抽象元模型的一个具体实例。

现在让我们探索端到端的流程，该流程在图中标注如下：

1.  当你在浏览器中点击“立即订购一个！”按钮时，一个 REST/JSON 请求会命中订单控制器。



2.  订单控制器将请求解释为写入请求，创建新的订单命令并将其发送到命令网关，命令网关随后将该命令分派到命令总线。

3.  命令总线接收命令并将其路由到订单命令处理器。

4.  订单命令处理器检索对应的产品并扣减库存。

5.  扣减产品库存后，通过向产品实体发送更新来在产品仓库中生效更改，该更改将持久化到写入数据库。

6.  此仓库更改会向事件总线触发一个库存更新事件，事件总线随后将事件分派给所有感兴趣的事件监听器。

7.  产品事件处理器已订阅库存更新事件，因此它会收到库存更新的详细信息通知。

8.  基于库存更新事件中提到的详细信息，产品事件处理器对产品的读取模型进行更改。

9.  在第 4 步中，你看到订单命令处理器扣减了产品库存。它还根据收到的订单命令中提到的详细信息创建了一个新订单。

10. 创建新订单时，通过插入新的订单实体在订单仓库中生效，该实体随后将持久化到写入数据库。

11. 此仓库插入操作会向事件总线触发一个订单创建事件，事件总线随后将事件分派给所有感兴趣的事件监听器。

12. 订单事件处理器已订阅订单创建事件，因此它会收到新创建订单的详细信息通知。

13. 基于订单创建事件中提到的详细信息，订单事件处理器对订单的读取模型进行更改。

14. 浏览器
    可以自行刷新，以通过查询实体的读取模型来保持视图更新。

编写示例场景

本节的所有代码
示例都在文件夹 `ch12\ch12-01` 中。访问
`pom.xml` 可以
看到 Axon 依赖的显式声明。参见清单 12-1。

```
org.axonframework
axon-core
2.4.1

清单 12-1
Axon Maven 依赖 (ch12\ch12-01\Ax2-Commands-Events-Same-JVM\pom.xml)
```

第一个 Axon 示例是一个一流的 Spring Boot 应用，因此
`main` 应用
类中没有什么特别之处；你仍然可以查看清单 12-2 中的代码。

```
@SpringBootApplication
public class EcomApplication {
public static void main(String[] args) {
SpringApplication.run(EcomApplication.class, args);
}
}
清单 12-2
Spring Boot 应用类 (ch12\ch12-01\Ax2-Commands-Events-Same-JVM\src\main\java\com\acme\ecom\EcomApplication.java)
```

接下来是这个示例中最重要的类，
你将在其中设置所有 Axon 组件；参见清单 12-3。

```
@Configuration
public class EcomAppConfiguration {
@PersistenceContext
private EntityManager entityManager;
@Bean
public SimpleCommandBus commandBus() {
return new SimpleCommandBus();
}
@Bean
public SimpleEventBus eventBus() {
return new SimpleEventBus();
}
@Bean
AnnotationCommandHandlerBeanPostProcessor
annotationCommandHandlerBeanPostProcessor() {
AnnotationCommandHandlerBeanPostProcessor handler =
new AnnotationCommandHandlerBeanPostProcessor();
handler.setCommandBus(commandBus());
return handler;
}
@Bean
AnnotationEventListenerBeanPostProcessor
annotationEventListenerBeanPostProcessor() {
AnnotationEventListenerBeanPostProcessor listener =
new AnnotationEventListenerBeanPostProcessor();
listener.setEventBus(eventBus());
return listener;
}
@Bean
public DefaultCommandGateway commandGateway() {
return new DefaultCommandGateway(commandBus());
}
@Bean
@Qualifier("productRepository")
public GenericJpaRepository productJpaRepository() {
SimpleEntityManagerProvider entityManagerProvider =
new SimpleEntityManagerProvider(entityManager);
GenericJpaRepository genericJpaRepository =
new GenericJpaRepository(entityManagerProvider, Product.class);
genericJpaRepository.setEventBus(eventBus());
return genericJpaRepository;
}
@Bean
@Qualifier("orderRepository")
public GenericJpaRepository orderJpaRepository() {
SimpleEntityManagerProvider entityManagerProvider =
new SimpleEntityManagerProvider(entityManager);
GenericJpaRepository genericJpaRepository =
new GenericJpaRepository(entityManagerProvider, Order.class);
genericJpaRepository.setEventBus(eventBus());
return genericJpaRepository;
}
}
清单 12-3
Spring Axon 配置 (ch12\ch12-01\Ax2-Commands-Events-Same-JVM\src\main\java\com\acme\ecom\EcomAppConfiguration.java)
```



现在我将解释 Axon 特有的组件。SimpleCommandBus 会在分派命令的同一线程中直接处理命令。命令处理完成后，修改后的聚合会被保存，并且任何生成的事件都会在同一线程中发布。

聚合 是一个始终保持一致状态的实体或实体组。聚合根 是聚合树顶层的对象，负责维护这种一致状态。例如，一个订单聚合可能包含订单项和送货地址等实体。为了保持整个聚合的一致状态，更改此订单的地址应通过订单聚合进行，而不是直接访问地址实体。

SimpleCommandBus 为每个发布的命令维护一个工作单元（UoW）的概念。由于所有命令处理都在同一线程中完成，此实现仅限于单个 JVM 的边界内。

工作单元 是对聚合进行的一组修改。命令的执行通常发生在工作单元的范围内。当命令处理程序执行完毕时，UoW 会被提交，所有操作都会最终确定。这意味着任何存储库都会收到其聚合状态变更的通知，并且计划发布的事件会被发送到事件总线。这是一种便捷的分组方式，有助于防止在处理完多个聚合之前发布单个事件。在后台，它允许存储库在整个事务中管理资源，例如锁。例如，锁只会在工作单元被提交或回滚时释放。

工作单元不能替代传统的 ACID 风格事务。它只是一个缓冲区域，用于分组和暂存更改。当工作单元被提交时，所有暂存的更改都会被提交；但是，此提交不是原子性的。这意味着如果提交失败，可能部分更改已被持久化，而其他更改则没有。因此，Axon 的最佳实践规定，一个命令应始终只包含一个操作。如果你的工作单元中有更多操作，则应考虑为工作单元的提交附加一个事务，关于这一点你可以参考 Axon 文档。

SimpleEventBus，顾名思义，是 EventBus 接口的一个非常基础的实现，是一种用于将事件分派给已订阅事件监听器的机制。当事件分派是同步且本地（即在单个 JVM 中）进行时，它表现良好。它管理已订阅的 EventListener，并将所有传入的事件转发给所有已订阅的监听器。EventListener 必须显式地向事件总线注册，才能接收事件。注册过程是线程安全的，监听器可以随时注册和注销事件。如果 EventListener 抛出异常，事件分派将停止，并且异常会传播给发布事件的组件。

AnnotationCommandHandlerBeanPostProcessor 会自动将包含命令处理程序方法（即使用 `@CommandHandler` 注解的方法）的类注册到命令总线。

*   此 Bean 后处理器会扫描应用程序上下文，查找所有包含带有 `@CommandHandler` 注解方法的 Bean。
*   所有此类命令处理程序都会被自动识别并订阅到命令总线。

AnnotationEventListenerBeanPostProcessor 会自动将应用程序上下文中包含事件处理程序方法（即使用 `@EventHandler` 注解的方法）的类注册，并自动将它们连接到事件总线。

*   此 Bean 后处理器会扫描应用程序上下文，查找所有包含带有 `@EventHandler` 注解方法的 Bean。
*   所有此类事件监听器都会被自动识别并订阅到事件总线。

命令网关是命令分派机制的一个便捷接口。使用网关来分派命令是可选的；然而，这通常是最简单的方式。CommandGateway 是接口，DefaultCommandGateway 是 Axon 提供的默认实现。网关提供了多种方法，允许你发送命令并等待结果，可以是同步、带超时或异步的。

GenericJpaRepository 是一个存储库实现，可以存储与 JPA 兼容的聚合。它配置了一个 EntityManager 来管理实际的持久化，以及指定存储在存储库中的聚合实际类型的类，在你的例子中是 `Product` 和 `Order`。所有带有 JPA 注解的聚合必须实现 AggregateRoot 并具有正确的 JPA 注解。存储库可以配置锁定方案。存储库将始终在底层持久化存储中强制使用乐观锁。可以为存储库配置一个可选的锁，在这种情况下，它将作为此乐观锁的补充。一个存储库实例上的锁不会与其他存储库实例共享。当请求此存储库持久化对聚合的更改时，它还会刷新 EntityManager，在此期间它将强制检查数据库约束和乐观锁。现在让我们看看你的 AggregateRoot 实体；参见清单 12-4。

```
import org.axonframework.domain.AbstractAggregateRoot;
@Entity
@Table(name="ECOM_ORDER")
@Data
@EqualsAndHashCode(exclude = { "id" })
public class Order extends AbstractAggregateRoot {
@Id
private Integer id;
@Column(name="PRICE")
private Double price;
@Column(name="NUMBER")
private Integer number;
@Column(name="ORDER_STATUS")
@Enumerated(EnumType.STRING)
private OrderStatusEnum orderStatus; ;
@ManyToOne(fetch=FetchType.LAZY)
@JoinColumn(name="PRODUCT_ID")
private Product product;
@Override
public Integer getIdentifier() {
return id;
}
public Order(Integer id, Double price, Integer number,
OrderStatusEnum orderStatus, Product product) {
super();
this.id = id;
this.price = price;
this.number = number;
this.orderStatus = orderStatus;
this.product = product;
registerEvent(new OrderCreatedEvent(id, price, number,
product.getDescription(), orderStatus.toString()));
}
}
清单 12-4
订单聚合实体 (ch12\ch12-01\Ax2-Commands-Events-Same-JVM\src\main\java\com\acme\ecom\order\model\Order.java)
```

`Order` 继承了 AbstractAggregateRoot，这是 AggregateRoot 接口的一个非常基础的实现。它提供了跟踪所有未提交事件的机制，并且还根据生成的事件数量维护一个版本号，该版本号可用于提交期间的验证。此外，`Order` 实体还使用 `registerEvent` 方法注册在保存聚合时要发布的事件。接下来，你将看到清单 12-5 中的 `Product` 聚合。

```
@Entity
@Table(name="ECOM_PRODUCT")
@Data
@EqualsAndHashCode(exclude = { "id" })
public class Product extends AbstractAggregateRoot {
@Id
private Integer id;
@Column(name="PRICE")
private Double price;
@Column(name="STOCK")
private Integer stock;
@Column(name="DESCRIPTION")
private String description;
@Override
public Integer getIdentifier() {
return id;
}
public void depreciateStock(int count) {
if(this.stock >= count){
this.stock = this.stock - count;
registerEvent(new StockUpdatedEvent(id, stock));
}else{
throw new RuntimeException("Out of stock");
}
}
}
清单 12-5
产品聚合实体 (ch12\ch12-01\Ax2-Commands-Events-Same-JVM\src\main\java\com\acme\ecom\product\model\Product.java)
```

`Product` 聚合在结构和功能上与 `Order` 聚合相似，因此此处不再重复说明。



接下来，你将了解命令（Commands）。它们是直接的 DTO 类，如清单 12-6 所示。

```
import lombok.Data;
@Data
public class NewOrderCommand {
private final Double price;
private final Integer number;
private final Integer productId;
}
清单 12-6
新订单命令 (ch12\ch12-01\Ax2-Commands-Events-Same-JVM\src\main\java\com\acme\ecom\order\api\command\NewOrderCommand.java)
```

一个好的实践是让命令通过其名称来表达意图。因此，你可以通过 Java 类名来推断需要执行的操作，而命令对象的字段则应提供基于该命令执行操作所需的信息。

现在，你将看到上述命令的创建过程。当你在浏览器中点击“立即下单！”按钮时，一个 REST/JSON 请求会到达订单控制器，如清单 12-7 所示。

```
@RestController
public class OrderController {
@Autowired
private DataSource dataSource;
@Autowired
private CommandGateway commandGateway;
@RequestMapping(value = "/orders", method = RequestMethod.POST)
@Transactional
public ResponseEntity addNewOrder(@RequestBody OrderDTO orderDTO) {
commandGateway.sendAndWait(new NewOrderCommand(orderDTO.getPrice(),
orderDTO.getNumber(), orderDTO.getProductId()));
return new ResponseEntity( HttpStatus.OK);
}
}
清单 12-7
订单 REST 控制器 (ch12\ch12-01\Ax2-Commands-Events-Same-JVM\src\main\java\com\acme\ecom\web\controller\OrderController.java)
```

`Controller` 方法将创建一个新命令，并将其发送到命令总线，然后等待其执行。执行结果在可用时返回。此 `sendAndWait` 方法将无限期阻塞，直到有结果可用或当前线程被中断。如果线程被中断，该方法返回 null。如果在处理过程中发生异常，该异常会被包装在 `CommandExecutionException` 中。

清单 12-8 展示了命令处理器。

```
@Component
public class OrderCommandHandler {
@Autowired
@Qualifier("orderRepository")
private Repository orderRepository;
@Autowired
@Qualifier("productRepository")
private Repository productRepository;
@CommandHandler
public void handle(NewOrderCommand newOrderCommand){
Product product =
productRepository.load(newOrderCommand.getProductId());
product.depreciateStock(newOrderCommand.getNumber());
Order order = new Order(new Random().nextInt(),
newOrderCommand.getPrice(),
newOrderCommand.getNumber(), OrderStatusEnum.NEW, product);
orderRepository.add(order);
}
}
清单 12-8
Axon 订单命令处理器 (ch12\ch12-01\Ax2-Commands-Events-Same-JVM\ src\main\java\com\acme\ecom\order\commandhandler\OrderCommandHandler.java)
```

`@CommandHandler` 注解会将 `OrderCommandHandler` 上的 `handle` 方法标记为命令处理器。被注解方法的第一个参数是该方法要处理的命令。可选地，命令处理器可以指定第二个参数，类型为 `UnitOfWork`。如果提供了该参数，则会传入当前活跃的工作单元。一种类型的命令只能由一种类型的命令处理器处理。因此，一个订单命令处理器实例将处理新订单命令，当这样的命令到达时，它会从仓库中检索相应的领域对象（在你的场景中是 `Product` 聚合），并执行其上的方法来改变状态（在你的场景中是减少库存并创建新订单）。这些聚合通常包含实际的业务逻辑，因此负责维护自身的不变性。聚合的状态变化会导致领域事件的生成。在上述代码中，产品库存减少和新订单的创建都会生成事件。领域事件和聚合共同构成了领域模型。

现在，你将查看生成的事件的代码。事件是简单的数据封装 POJO 类。参见清单 12-9 和清单 12-10。

```
public class StockUpdatedEvent {
private final Integer id;
private final Integer stock;
}
清单 12-10
库存更新事件 (ch12\ch12-01\Ax2-Commands-Events-Same-JVM\ src\main\java\com\acme\ecom\product\api\event\StockUpdatedEvent.java)
```

```
public class OrderCreatedEvent {
private final Integer id;
private final Double price;
private final Integer number;
private final String productDescription;
private final String orderStatus;
}
清单 12-9
订单创建事件 (ch12\ch12-01\Ax2-Commands-Events-Same-JVM\ src\main\java\com\acme\ecom\order\api\event\OrderCreatedEvent.java)
```

现在来看能够处理这些事件的事件处理器。如果你还记得，对于命令处理，一种类型的命令只能由一种类型的命令处理器处理。然而，对于事件处理，一种类型的事件可以由零个或多个类型的事件处理器处理。这类似于使用消息主题的发布-订阅模式，其中一条消息可以被多个订阅者消费。稍后会详细介绍；现在让我们看看清单 12-11 中事件处理器的代码。

```
@Component
public class OrderEventHandler {
@Autowired
DataSource dataSource;
@EventHandler
public void handleOrderCreatedEvent(OrderCreatedEvent event) {
JdbcTemplate jdbcTemplate = new JdbcTemplate(dataSource);
jdbcTemplate.update("INSERT INTO ecom_order_view VALUES(?,?,?,?,?)",
new Object[]{event.getId(),
event.getPrice(), event.getNumber(), event.getProductDescription(),
event.getOrderStatus()});
}
}
清单 12-11
订单事件处理器 (ch12\ch12-01\Ax2-Commands-Events-Same-JVM\ src\main\java\com\acme\ecom\order\eventhandler\OrderEventHandler.java)
```

订单事件处理器在新订单创建后接收订单创建事件。因此，剩下的唯一任务就是更新订单读取数据库，上述代码完成了这项工作。另一个事件处理器是产品事件处理器，如清单 12-12 所示。

```
@Component
public class ProductEventHandler {
@Autowired
DataSource dataSource;
@EventHandler
public void handleProductStockUpdatedEvent(StockUpdatedEvent event) {
JdbcTemplate jdbcTemplate = new JdbcTemplate(dataSource);
jdbcTemplate.update("UPDATE ecom_product_view SET stock=? WHERE ID=?",
new Object[]{event.getStock(), event.getId()});
}
}
清单 12-12
产品事件处理器 (ch12\ch12-01\Ax2-Commands-Events-Same-JVM\src\main\java\com\acme\ecom\product\eventhandler\ProductEventHandler.java)
```

产品事件处理器会将产品库存水平的变化更新到产品读取数据库。通过这种方式，每次新订单的创建都会改变订单和产品的写入数据库，并且所有这些更改最终也会传播到读取数据库。

构建并测试示例场景

演示这个简单 Axon 示例所需的完整代码位于 `ch12\ch12-01` 文件夹中。首先，更新配置文件以适配你的环境：

```
ch12\ch12-01\Ax2-Commands-Events-Same-JVM\src\main\resources\application.properties
spring.datasource.url=jdbc:mysql://localhost/ecom01
spring.datasource.username=root
spring.datasource.password=rootpassword
```

确保 MySQL 已启动并正在运行。你可能需要参考附录 H 来开始使用 MySQL。

```
首先启动 MySQL 服务器。
D:\Applns\MySQL\mysql-8.0.14-winx64\bin>mysqld --console
现在打开 MySQL 提示符
D:\Applns\MySQL\mysql-8.0.14-winx64\bin>mysql -u root –p
mysql> use ecom01;
数据库已更改
mysql>
```

为了从干净的表格开始，删除示例中你需要的任何名称的表格：

```
mysql> drop table ecom_order;
mysql> drop table ecom_product;
mysql> drop table ecom_order_view;
mysql> drop table ecom_product_view;
```



现在，当微服务启动时，`ecom_order` 和 `ecom_product` 表将被创建。但是，你需要显式地创建读数据库（Read DB）表：

```
mysql> create table ecom_product_view(id INT , price DOUBLE, stock INT ,description VARCHAR(255));
mysql> create table ecom_order_view(id INT , price DOUBLE, number INT ,description VARCHAR(225),status VARCHAR(50));
```

现在，你将构建并打包 Axon 微服务的可执行文件，然后启动服务器。由于你在安装 MySQL 服务器时使用了 `mysql-8.0.14-winx64.zip` 归档文件（参见附录 H），因此你需要在 Maven 构建脚本 `pom.xml` 中使用 `mysql-connector-java` Maven 工件的 8.0.14 版本。在文件夹 `ch12\ch12-01\Ax2-Commands-Events-Same-JVM\make.bat` 中提供了一个实用脚本：

```
cd D:\binil\gold\pack03\ch12\ch12-01\Ax2-Commands-Events-Same-JVM
D:\binil\gold\pack03\ch12\ch12-01\Ax2-Commands-Events-Same-JVM>make
D:\binil\gold\pack03\ch12\ch12-01\Ax2-Commands-Events-Same-JVM>mvn clean install
```

现在，你可以通过多种方式运行 Axon Spring Boot 应用程序。最直接的方式是通过以下命令执行 JAR 文件：

```
D:\binil\gold\pack03\ch12\ch12-01\Ax2-Commands-Events-Same-JVM>run
D:\binil\gold\pack03\ch12\ch12-01\Ax2-Commands-Events-Same-JVM>mvn spring-boot:run
```

这将在 8080 端口启动 Axon Spring Boot 服务器。此时 `ecom_order` 和 `ecom_product` 表应该已经创建好了。你需要用一些初始数据预填充 `ecom_product` 和 `ecom_product_view` 表：

```
mysql> insert into ecom_product(id,description,price,stock,version) values(1,'Shirts',100,5,0);
mysql> insert into ecom_product(id,description,price,stock,version) values(2,'Pants',100,5,0);
mysql> insert into ecom_product(id,description,price,stock,version) values(3,'T-Shirt',100,5,0);
mysql> insert into ecom_product(id,description,price,stock,version) values(4,'Shoes',100,5,0);
mysql> insert into ecom_product_view(id,description,price,stock) values(1,'Shirts',100,5);
mysql> insert into ecom_product_view(id,description,price,stock) values(2,'Pants',100,5);
mysql> insert into ecom_product_view(id,description,price,stock) values(3,'T-Shirt',100,5);
mysql> insert into ecom_product_view(id,description,price,stock) values(4,'Shoes',100,5);
```

你可以使用浏览器（推荐 Chrome）并访问 `http://localhost:8080/`。现在，你可以通过点击图 12-2 中的任意一个按钮来测试示例。

![../images/477630_1_En_12_Chapter/477630_1_En_12_Fig2_HTML.jpg](img/477630_1_En_12_Fig2_HTML.jpg)

图 12-2.
在同一个 JVM 中测试 Axon 命令和事件处理

当你点击时，订单写数据库（Order Write DB）中会创建一个新订单。最终，订单读数据库（Order Read DB）将用这个新订单进行更新。同时，产品写数据库（Product Write DB）中对应的产品库存将减少一个。这个变更最终也会在产品读数据库（Product Read DB）中更新。该变更反映在图 12-3 中。

![../images/477630_1_En_12_Chapter/477630_1_En_12_Fig3_HTML.jpg](img/477630_1_En_12_Fig3_HTML.jpg)

图 12-3.
产品视图与产品写入数据同步

分布式命令和事件处理
企业级应用程序将处理过程分布到多个计算节点和多个进程中，以充分利用多核处理器的全部能力，并实现最大程度的并行性，从而提高整体性能。微服务架构也不例外，在本示例中，你将把不同的 Axon 组件完全分布到多个 JVM 中。由于该示例是跨进程分布的，你实际上也可以将它们分布到多个节点上；但为了简单起见，你将在单个节点（在我的例子中是我的个人笔记本电脑）上执行每个主要 Axon 组件的示例。

示例场景

你使用的是与上一节相同的场景。虽然之前的示例是作为一个单一的微服务实现的，但在本示例中，你将把相同的功能拆分为四个不同的微服务，并且还将额外引入另一个微服务。这第五个新的微服务是事件处理器审计微服务（Event Handler Audit microservice）。事件处理器审计微服务有一个事件处理器 `AuditEventHandler`，它也订阅了订单创建事件。因此，你现在有两个微服务（事件处理器审计微服务和事件处理核心微服务），它们拥有两种事件处理器类型，都对同一个订单创建事件感兴趣！图 12-4 展示了该设计。

![../images/477630_1_En_12_Chapter/477630_1_En_12_Fig4_HTML.jpg](img/477630_1_En_12_Fig4_HTML.jpg)

图 12-4.
Axon 中分布式命令和事件处理的设计

第 5 章中的“使用事件进行命令查询分离”一节展示了基于事件的 CQRS 架构的元模型。图 12-4 是第 5 章中图 [5-6](https://doi.org/10.1007/978-1-4842-4501-9_5Fig#6) 所表示的抽象元模型的具体体现。

该架构中的命令和事件流也与之前的示例类似；但存在一些细微的差别。让我们重新审视一下端到端的流程，该流程在图中标注如下：

1.  当你在浏览器中点击“立即订购一件！”按钮时，一个 REST/JSON 请求会命中订单控制器。

2.  创建命令 REST 控制器微服务中的订单控制器将该请求解释为写入请求，并创建一个新的订单命令，然后将其发送到命令网关，命令网关再将命令分派到分布式命令总线。

3.  分布式命令总线在跨 JVM 的命令总线实现之间形成一座桥梁。每个 JVM 上的分布式命令总线被称为一个段（segment）。

4.  命令总线接收命令并将其路由到订单命令处理器，这两者都存在于处理命令和创建事件微服务中。

5.  订单命令处理器检索对应的产品并减少库存。

6.  在减少产品库存后，通过向产品实体发送更新来影响产品仓库中的变更，该更新将持久化到写数据库（Write DB）中。

7.  此仓库变更会向事件总线触发一个库存更新事件，事件总线再将事件分派给所有感兴趣的事件监听器。

8.  事件处理核心微服务中的产品事件处理器已订阅了库存更新事件，因此它会收到库存更新详情的通知。

9.  根据库存更新事件中提到的详细信息，产品事件处理器对产品的读模型进行更改。

10. 在第 5 步中，订单命令处理器减少了产品库存。与此同时，它还根据收到的新的订单命令中的详细信息创建了一个新订单。

11. 当创建一个新订单时，通过插入一个新的订单实体来影响订单仓库，该实体随后会持久化到写数据库（Write DB）中。

12. 此仓库插入操作会向事件总线触发一个订单创建事件，事件总线再将事件分派给所有感兴趣的事件监听器。有两个感兴趣的事件监听器，它们都位于外部（不同进程或节点）的微服务中，即事件处理核心微服务和事件处理器审计微服务。

13. 事件处理核心微服务中的订单事件处理器已订阅了订单创建事件，因此它会收到新创建订单的详细信息通知。



14.  根据订单创建事件中提及的详细信息，订单事件处理器会对订单的读取模型进行修改，在此场景中即插入一条新记录。

15.  事件处理器审计微服务中的审计事件处理器也已订阅了订单创建事件，因此它会收到新创建订单的详细信息通知。

16.  根据订单创建事件中提及的详细信息，审计事件处理器会对审计的读取模型进行修改。

17.  浏览器可以自行刷新，以通过查询实体的读取模型来保持视图更新。

18.  用户可以通过浏览器查看变更。

**编写示例场景代码**
演示简单 Axon 示例所需的完整代码位于文件夹 `ch12\ch12-02` 中。需要编写五个微服务，我们将逐一进行介绍。

**微服务 1：01-Ecom-web**
该微服务是一个典型的 Spring Boot Web 应用程序，不包含任何 Axon 组件，因此此处不再赘述。

**微服务 2：02-Ecom-CreateCommandRestController**

请查看 `pom.xml` 文件以了解 axon-distributed-commandbus 依赖项；参见代码清单 12-13。

```
org.axonframework
axon-distributed-commandbus
2.4.1

代码清单 12-13
Axon 分布式命令总线 Maven 依赖项 (ch12\ch12-02\Ax2-Commands-Multi-Event-Handler-Distributed\02-Ecom-CreateCommandRestController\pom.xml)
```

此示例的大部分代码与上一个示例相似，因此此处不再重复；相反，我将解释所有新代码以及相对于上一个示例的代码变更。此示例最重要的类是在其中设置所有分布式 Axon 组件的类，如代码清单 12-14 所示。

```
@Configuration
public class EcomAppConfiguration {
@Bean
public XStreamSerializer xstreamSerializer() {
return new XStreamSerializer();
}
@Bean
@Qualifier("distributedCommandGateway")
public CommandGatewayFactoryBean
commandGatewayFactoryBean() {
CommandGatewayFactoryBean factory =
new CommandGatewayFactoryBean();
factory.setCommandBus(distributedCommandBus());
return factory;
}
@Bean
public JGroupsConnector getJGroupConnector() {
JGroupsConnector connector = new JGroupsConnector(
new JChannel("udp_config.xml"), "myCluster",
localSegment(), xstreamSerializer());
connector.connect(100);
return connector;
return null;
}
@Bean
@Qualifier("distributedCommandBus")
public DistributedCommandBus distributedCommandBus() {
DistributedCommandBus distributedCommandBus =
new DistributedCommandBus(getJGroupConnector());
return distributedCommandBus;
}
@Bean
@Qualifier("localCommandBus")
public SimpleCommandBus localSegment() {
SimpleCommandBus simpleCommandBus = new SimpleCommandBus();
simpleCommandBus.setDispatchInterceptors(Arrays.asList(
new BeanValidationInterceptor()));
return simpleCommandBus;
}
}
代码清单 12-14
分布式命令总线配置 (ch12\ch12-02\Ax2-Commands-Multi-Event-Handler-Distributed\02-Ecom-CreateCommandRestController\src\main\java\com\acme\ecom\EcomAppConfiguration.java)
```

`DistributedCommandBus` 在多个 JVM 的命令总线实现之间形成了一座桥梁。在您的场景中，您希望在 02-Ecom-CreateCommandRestController 微服务和 03-Ecom-HandleCommandAndCreateEvent 微服务之间建立桥梁，因为前者微服务生成的命令需要由后者微服务处理。因此，每个 JVM 上的 `DistributedCommandBus` 实例被称为段，并在代码中限定名称为 `localCommandBus`，如图 12-5 所示。

![../images/477630_1_En_12_Chapter/477630_1_En_12_Fig5_HTML.jpg](img/477630_1_En_12_Fig5_HTML.jpg)

图 12-5.
Axon 中的分布式命令与事件处理

`DistributedCommandBus` 依赖于 `CommandBusConnector`，后者实现了 JVM 之间的通信协议。在您的场景中，您使用了 `JGroupsConnector`，它利用 JGroups 来发现并连接到网络中的其他 `JGroupsConnector`。根据所提供的通道配置，此实现支持动态发现和添加新成员。当成员断开连接时，其处理部分会由剩余成员分担。这种节点、JVM 或微服务的动态添加和移除对于您软件所需的可扩展性至关重要，这在本章第一节中已讨论过。
就 Ecom-CreateCommandRestController 微服务的代码而言，除了此配置外没有其他更改，但您可以参考上一个示例中的相应章节，以了解该微服务其余代码的解释。

**微服务 3：03-Ecom-HandleCommandAndCreateEvent**
该微服务必须处理由 02-Ecom-CreateCommandRestController 微服务创建并通过远程 JVM 的 `DistributedCommandBus` 到达的任何命令。此外，该微服务还会创建事件，这些事件应由远程 JVM 或外部微服务中的分布式事件处理器消费。因此，为了容纳所有这些连接器和路由，该微服务的配置会稍微复杂一些。

请查看 `EcomAppConfiguration.java` 文件以了解主要配置，如代码清单 12-15 所示。

```
@Configuration
public class EcomAppConfiguration {
@PersistenceContext
private EntityManager entityManager;
@Qualifier("transactionManager")
@Autowired
protected PlatformTransactionManager txManager;
@Bean
@Qualifier("distributedCommandGateway")
public CommandGatewayFactoryBean
commandGatewayFactoryBean() {
CommandGatewayFactoryBean factory =
new CommandGatewayFactoryBean();
factory.setCommandBus(distributedCommandBus());
return factory;
}
@Bean
@Qualifier("localCommandGateway")
public CommandGatewayFactoryBean
localCommandGatewayFactoryBean() {
CommandGatewayFactoryBean factory =
new CommandGatewayFactoryBean();
factory.setCommandBus(localSegment());
return factory;
}
@Bean
public JGroupsConnector getJGroupConnector() {
JGroupsConnector connector = new JGroupsConnector(
new JChannel("udp_config.xml"), "myCluster",
localSegment(), xstreamSerializer());
connector.connect(100);
return connector;
}
@Bean
@Qualifier("distributedCommandBus")
public DistributedCommandBus distributedCommandBus() {
DistributedCommandBus distributedCommandBus =
new DistributedCommandBus(getJGroupConnector());
return distributedCommandBus;
}
@Bean
@Qualifier("localCommandBus")
public SimpleCommandBus localSegment() {
SimpleCommandBus simpleCommandBus = new SimpleCommandBus();
SpringTransactionManager transcationMgr =
new SpringTransactionManager(txManager);
simpleCommandBus.setTransactionManager(transcationMgr);
simpleCommandBus.setDispatchInterceptors(Arrays.asList
new BeanValidationInterceptor()));
return simpleCommandBus;
}
@Bean
public AnnotationCommandHandlerBeanPostProcessor
annotationCommandHandlerBeanPostProcessor() {
AnnotationCommandHandlerBeanPostProcessor processor =
new AnnotationCommandHandlerBeanPostProcessor();
processor.setCommandBus(distributedCommandBus());
return processor;
}
}
代码清单 12-15
分布式命令总线配置 (请访问 ch12\ch12-02\Ax2-Commands-Multi-Event-Handler-Distributed\03-Ecom-HandleCommandAndCreateEvent\src\main\java\com\acme\ecom\EcomAppConfiguration.java)
```

您已经了解了代码清单 12-14 中分布式命令总线配置的解释，因此我现在将解释代码清单 12-15 中的分布式命令处理基础设施。接下来，让我们继续讨论事件基础设施；参见代码清单 12-16。


```markdown

```
@Configuration
public class EcomAppConfiguration {
@PersistenceContext
private EntityManager entityManager;
@Qualifier("transactionManager")
@Autowired
protected PlatformTransactionManager txManager;
@Bean
public ConnectionFactory connectionFactory() {
CachingConnectionFactory connectionFactory =
new CachingConnectionFactory();
connectionFactory.setAddresses(rabbitMQAddress);
connectionFactory.setUsername(rabbitMQUser);
connectionFactory.setPassword(rabbitMQPassword);
connectionFactory.setVirtualHost(rabbitMQVhost);
connectionFactory.setConnectionTimeout(500000);
connectionFactory.setRequestedHeartBeat(20);
return connectionFactory;
}
@Bean
public FanoutExchange eventBusExchange() {
return new FanoutExchange(rabbitMQExchange, true, false);
}
@Bean
public Queue eventBusQueue() {
return new Queue(rabbitMQQueue, true, false, false);
}
@Bean
public Binding binding() {
return BindingBuilder.bind(eventBusQueue()).to(eventBusExchange());
}
@Bean
public EventBus eventBus() {
ClusteringEventBus clusteringEventBus = new ClusteringEventBus(
new DefaultClusterSelector(simpleCluster()), terminal());
return clusteringEventBus;
}
@Bean
public DefaultAMQPMessageConverter defaultAMQPMessageConverter() {
return new DefaultAMQPMessageConverter(xstreamSerializer());
}
@Bean
ListenerContainerLifecycleManager listenerContainerLifecycleManager() {
ListenerContainerLifecycleManager listenerContainerLifecycleManager =
new ListenerContainerLifecycleManager();
listenerContainerLifecycleManager.setConnectionFactory(
connectionFactory());
return listenerContainerLifecycleManager;
}
@Bean
public AnnotationEventListenerBeanPostProcessor
annotationEventListenerBeanPostProcessor() {
AnnotationEventListenerBeanPostProcessor processor =
new AnnotationEventListenerBeanPostProcessor();
processor.setEventBus(eventBus());
return processor;
}
@Bean
public EventBusTerminal terminal() {
SpringAMQPTerminal terminal = new SpringAMQPTerminal();
terminal.setConnectionFactory(connectionFactory());
terminal.setSerializer(xstreamSerializer());
terminal.setExchangeName(rabbitMQExchange);
terminal.setListenerContainerLifecycleManager(
listenerContainerLifecycleManager());
terminal.setDurable(true);
terminal.setTransactional(true);
return terminal;
}
SimpleCluster simpleCluster() {
SimpleCluster simpleCluster = new SimpleCluster(rabbitMQQueue);
return simpleCluster;
}
}
清单 12-16
集群事件总线配置
```

`ClusteringEventBus` 将事件分发到不同机器上的事件处理器。一个 `ClusteringEventBus` 必须配置一个 `ClusterSelector`，用于为每个已注册的 `EventListener` 选择一个 `Cluster` 实例，以及一个 `EventBusTerminal`，负责将事件分发到每个相关的集群。

在上述代码中，你使用 `DefaultClusterSelector` 作为 `ClusterSelector`，它始终选择同一个集群。该实现可以作为其他集群选择器的委托，用于处理不属于特定集群的事件监听器。`DefaultClusterSelector` 又使用了一个简单的 `Cluster` 实现，当事件发布时，它会调用集群中的每个成员。下一个重要的组件是 `EventBusTerminal`，它是一种连接事件总线集群的机制。终端负责将已发布的事件传递给事件总线中所有可用的集群（无论是本地还是远程）。终端通常绑定到单个事件总线实例，但可能知道存在多个实例，以便在这些事件总线之间形成桥梁。你使用了一个 `SpringAMQPTerminal`，它是 `EventBusTerminal` 的一个实现，使用兼容 AMQP 0.9 的消息代理来分发事件消息。这是因为你使用了兼容 AMQP 的 Rabbit MQ 作为消息队列提供者，它为分布式事件通道提供了骨干支持。所有外发消息都被发送到配置的交换机。该终端不会在内部分发事件，因为它依赖每个集群监听自己的 AMQP 队列。同样，如代码所示，终端配置了 `ConnectionFactory`，用于提供发送消息的连接和通道。虽然这些内容有点复杂难以理解，但好在 Axon 抽象了大部分复杂性，你可以将整个设置可视化为图 12-6 所示。

![../images/477630_1_En_12_Chapter/477630_1_En_12_Fig6_HTML.jpg](img/477630_1_En_12_Fig6_HTML.jpg)

图 12-6.
集群事件总线中的分布式事件处理

如果你理解了前面的示例，那么这个微服务中绝对没有任何新内容。

**微服务 4：04-Ecom-EventHandleCore**
同样，如果你已经理解了前面的示例，那么这个微服务中也没有任何新内容，因此我将不进行代码讲解。正如微服务名称所暗示的，它包含两个主要的事件处理器：`OrderEventHandler` 和 `ProductEventHandler`，其功能和代码片段与前面的示例类似。

**微服务 5：05-Ecom-EventHandlerAudit**
这是一个新的微服务。其目的是演示基于 Axon 的 CQRS 架构的可扩展性：将来，如果你有一个新功能想要订阅现有事件并添加到应用程序的现有整体功能中，你应该能够在现有运行应用程序不停机的情况下做到这一点。

清单 12-17 展示了新添加的事件处理器的代码，该处理器期望被通知之前已处理的事件本身。

```
@Component
public class AuditEventHandler {
@Autowired
DataSource dataSource;
@EventHandler
public void handleOrderCreatedEvent(OrderCreatedEvent event) {
JdbcTemplate jdbcTemplate = new JdbcTemplate(dataSource);
jdbcTemplate.update("INSERT INTO ecom_order_audit VALUES(?,?,?)",
new Object[]{event.getId(),
event.getOrderStatus(), new Date()});
}
}
清单 12-17
新引入微服务的事件处理器（ch12\ch12-02\Ax2-Commands-Multi-Event-Handler-Distributed\05-Ecom-EventHandlerAudit\src\main\java\com\acme\ecom\order\eventhandler\AuditEventHandler.java）
```

**构建并测试示例场景**

作为第一步，你需要启动 RabbitMQ 服务器。你可能需要参考附录 B 来开始使用 RabbitMQ 服务器。

```

```markdown

```
D:\Applns\RabbitMQ\rabbitmq_server-3.6.3\sbin>D:\Applns\RabbitMQ\rabbitmq_server-3.6.3\sbin\rabbitmq-server.bat
```

确保 MySQL 已启动并正在运行。你可以参考附录 H 来开始使用 MySQL。

若要使用干净的表格，请删除本示例中你需要的所有同名表格：

```
mysql> drop table ecom_order;
mysql> drop table ecom_product;
mysql> drop table ecom_order_view;
mysql> drop table ecom_product_view;
mysql> drop table ecom_order_audit;
```

新建所需的表格。现在，`ecom_order` 表和 `ecom_product` 表将在微服务启动时自动创建。但是，你需要显式地创建读数据库（Read DB）表格和审计（Audit）表格：

```
mysql>create table ecom_product_view(id INT , price DOUBLE, stock INT ,description VARCHAR(255));
mysql>create table ecom_order_view(id INT , price DOUBLE, number INT ,description VARCHAR(225),status VARCHAR(50));
mysql>create table ecom_order_audit(id INT ,status VARCHAR(50),date TIMESTAMP);
```

本示例包含一个 Maven 模块，其中包含了所有其他微服务使用的公共类。因此，需要先构建该模块：

```
cd D:\binil\gold\pack03\ch12\ch12-02\Ax2-Commands-Multi-Event-Handler-Distributed\06-Ecom-common
D:\binil\gold\pack03\ch12\ch12-02\Ax2-Commands-Multi-Event-Handler-Distributed\06-Ecom-common>make
D:\binil\gold\pack03\ch12\ch12-02\Ax2-Commands-Multi-Event-Handler-Distributed\06-Ecom-common>mvn clean install
```

接下来，需要构建并运行五个微服务。我们将逐一进行。

微服务 1：01-Ecom-web

首先，根据你的环境更新配置文件：

```
ch12\ch12-02\Ax2-Commands-Multi-Event-Handler-Distributed\01-Ecom-web\src\main\resources\application.properties
server.port=8080
```

建议你不要在此处做任何更改。

现在，构建并打包 Ecom-web 微服务的可执行文件，并启动服务器。在 `ch12\ch12-02\Ax2-Commands-Multi-Event-Handler-Distributed\01-Ecom-web\make.bat` 中有一个实用脚本：

```
cd D:\binil\gold\pack03\ch12\ch12-02\Ax2-Commands-Multi-Event-Handler-Distributed\01-Ecom-web
D:\binil\gold\pack03\ch12\ch12-02\Ax2-Commands-Multi-Event-Handler-Distributed\01-Ecom-web>make
D:\binil\gold\pack03\ch12\ch12-02\Ax2-Commands-Multi-Event-Handler-Distributed\01-Ecom-web>mvn clean install
```

你可以通过多种方式运行 Spring Boot 应用程序。最直接的方式是通过以下命令执行 JAR 文件：

```
D:\binil\gold\pack03\ch12\ch12-02\Ax2-Commands-Multi-Event-Handler-Distributed\01-Ecom-web>run
D:\binil\gold\pack03\ch12\ch12-02\Ax2-Commands-Multi-Event-Handler-Distributed\01-Ecom-web>java -jar .\target\01-Ecom-web-0.0.1-SNAPSHOT.jar
```

这将在端口 8080 上启动 01-Ecom-web Spring Boot 服务器。

微服务 2：
02-Ecom-CreateCommandRestController
JGroups 配置文件位于 `ch12\ch12-02\Ax2-Commands-Multi-Event-Handler-Distributed\02-Ecom-CreateCommandRestController\src\main\resources\udp_config.xml`。不过，暂时不必过多担心此文件的内容。

根据你的环境更新配置文件：

```
ch12\ch12-02\Ax2-Commands-Multi-Event-Handler-Distributed\02-Ecom-CreateCommandRestController\src\main\resources\application.properties
server.port=8081
spring.datasource.url=jdbc:mysql://localhost/ecom01
spring.datasource.username=root
spring.datasource.password=rootpassword
```

现在，构建微服务：

```
cd D:\binil\gold\pack03\ch12\ch12-02\Ax2-Commands-Multi-Event-Handler-Distributed\02-Ecom-CreateCommandRestController
D:\binil\gold\pack03\ch12\ch12-02\Ax2-Commands-Multi-Event-Handler-Distributed\02-Ecom-CreateCommandRestController>make
D:\binil\gold\pack03\ch12\ch12-02\Ax2-Commands-Multi-Event-Handler-Distributed\02-Ecom-CreateCommandRestController>mvn clean install
```

你可以通过多种方式运行 02-Ecom-CreateCommandRestController Axon Spring Boot 应用程序。最直接的方式是通过以下命令执行 JAR 文件：

```
D:\binil\gold\pack03\ch12\ch12-02\Ax2-Commands-Multi-Event-Handler-Distributed\02-Ecom-CreateCommandRestController>run
D:\binil\gold\pack03\ch12\ch12-02\Ax2-Commands-Multi-Event-Handler-Distributed\02-Ecom-CreateCommandRestController>java -jar .\target\02-Ecom-CreateCommandRestController-0.0.1-SNAPSHOT.jar
```

这将在端口 8081 上启动 02-Ecom-CreateCommandRestController Axon Spring Boot 服务器。

微服务 3：
03-Ecom-HandleCommandAndCreateEvent

根据你的环境更新配置文件：

```
ch12\ch12-02\Ax2-Commands-Multi-Event-Handler-Distributed\03-Ecom-HandleCommandAndCreateEvent\src\main\resources\application.properties
server.port=8082
spring.datasource.url=jdbc:mysql://localhost/ecom01
spring.datasource.username=root
spring.datasource.password=rootpassword
ecom.amqp.rabbit.address= 127.0.0.1:5672
ecom.amqp.rabbit.username= guest
ecom.amqp.rabbit.password= guest
ecom.amqp.rabbit.vhost=/
ecom.amqp.rabbit.exchange=Ecom-02
ecom.amqp.rabbit.queue=Ecom-createcommand
cd D:\binil\gold\pack03\ch12\ch12-02\Ax2-Commands-Multi-Event-Handler-Distributed\03-Ecom-HandleCommandAndCreateEvent
D:\binil\gold\pack03\ch12\ch12-02\Ax2-Commands-Multi-Event-Handler-Distributed\03-Ecom-HandleCommandAndCreateEvent>make
D:\binil\gold\pack03\ch12\ch12-02\Ax2-Commands-Multi-Event-Handler-Distributed\03-Ecom-HandleCommandAndCreateEvent>mvn clean install
```

你可以通过多种方式运行 03-Ecom-HandleCommandAndCreateEvent Axon Spring Boot 应用程序。最直接的方式是通过以下命令执行 JAR 文件：

```
D:\binil\gold\pack03\ch12\ch12-02\Ax2-Commands-Multi-Event-Handler-Distributed\03-Ecom-HandleCommandAndCreateEvent>run
D:\binil\gold\pack03\ch12\ch12-02\Ax2-Commands-Multi-Event-Handler-Distributed\03-Ecom-HandleCommandAndCreateEvent>java -jar .\target\03-Ecom-HandleCommandAndCreateEvent-0.0.1-SNAPSHOT.jar
```

这将在端口 8082 上启动 02-Ecom-CreateCommandRestController Axon Spring Boot 服务器。

微服务 4：04-Ecom-EventHandleCore

根据你的环境更新配置文件：

```
ch12\ch12-02\Ax2-Commands-Multi-Event-Handler-Distributed\04-Ecom-EventHandleCore\src\main\resources\application.properties
server.port=8083
spring.datasource.url=jdbc:mysql://localhost/ecom01
spring.datasource.username=root
spring.datasource.password=rootpassword
ecom.amqp.rabbit.address= 127.0.0.1:5672
ecom.amqp.rabbit.username= guest
ecom.amqp.rabbit.password= guest
ecom.amqp.rabbit.vhost=/
ecom.amqp.rabbit.exchange=Ecom-02
ecom.amqp.rabbit.queue=Ecom-event-core
cd D:\binil\gold\pack03\ch12\ch12-02\Ax2-Commands-Multi-Event-Handler-Distributed\04-Ecom-EventHandleCore
D:\binil\gold\pack03\ch12\ch12-02\Ax2-Commands-Multi-Event-Handler-Distributed\04-Ecom-EventHandleCore>make
D:\binil\gold\pack03\ch12\ch12-02\Ax2-Commands-Multi-Event-Handler-Distributed\04-Ecom-EventHandleCore>mvn clean install
```

你可以通过多种方式运行 04-Ecom-EventHandleCore Axon Spring Boot 应用程序。最直接的方式是通过以下命令执行 JAR 文件：

```
D:\binil\gold\pack03\ch12\ch12-02\Ax2-Commands-Multi-Event-Handler-Distributed\04-Ecom-EventHandleCore>run
D:\binil\gold\pack03\ch12\ch12-02\Ax2-Commands-Multi-Event-Handler-Distributed\04-Ecom-EventHandleCore>java -jar .\target\04-Ecom-EventHandleCore-0.0.1-SNAPSHOT.jar
```

这将在端口 8083 上启动 04-Ecom-EventHandleCore Axon Spring Boot 服务器。

微服务 5：05-Ecom-EventHandlerAudit

这是本示例中要启动的最后一个微服务。根据你的环境更新配置文件：

```


```
ch12\ch12-02\Ax2-Commands-Multi-Event-Handler-Distributed\05-Ecom-EventHandlerAudit\src\main\resources\application.properties
server.port=8084
spring.datasource.url=jdbc:mysql://localhost/ecom01
spring.datasource.username=root
spring.datasource.password=rootpassword
ecom.amqp.rabbit.address= 127.0.0.1:5672
ecom.amqp.rabbit.username= guest
ecom.amqp.rabbit.password= guest
ecom.amqp.rabbit.vhost=/
ecom.amqp.rabbit.exchange=Ecom-02
ecom.amqp.rabbit.queue=Ecom-event-history
cd D:\binil\gold\pack03\ch12\ch12-02\Ax2-Commands-Multi-Event-Handler-Distributed\05-Ecom-EventHandlerAudit
D:\binil\gold\pack03\ch12\ch12-02\Ax2-Commands-Multi-Event-Handler-Distributed\05-Ecom-EventHandlerAudit>make
D:\binil\gold\pack03\ch12\ch12-02\Ax2-Commands-Multi-Event-Handler-Distributed\05-Ecom-EventHandlerAudit>mvn clean install
```

你可以通过多种方式再次运行 05-Ecom-EventHandlerAudit Axon Spring Boot 应用程序。最直接的方式是通过以下命令执行 JAR 文件：

```
D:\binil\gold\pack03\ch12\ch12-02\Ax2-Commands-Multi-Event-Handler-Distributed\05-Ecom-EventHandlerAudit>run
D:\binil\gold\pack03\ch12\ch12-02\Ax2-Commands-Multi-Event-Handler-Distributed\05-Ecom-EventHandlerAudit>java -Dserver.port=8084 -Dspring.application.name=product-audit-01 -jar .\target\05-Ecom-EventHandlerAudit-0.0.1-SNAPSHOT.jar
```

这将在 8084 端口启动 05-Ecom-EventHandlerAudit Axon Spring Boot 服务器。

你需要用一些初始数据预填充 `ecom_product` 和 `ecom_product_view` 表：

```
mysql> insert into ecom_product(id,description,price,stock,version) values(1,'Shirts',100,5,0);
mysql> insert into ecom_product(id,description,price,stock,version) values(2,'Pants',100,5,0);
mysql> insert into ecom_product(id,description,price,stock,version) values(3,'T-Shirt',100,5,0);
mysql> insert into ecom_product(id,description,price,stock,version) values(4,'Shoes',100,5,0);
mysql> insert into ecom_product_view(id,description,price,stock) values(1,'Shirts',100,5);
mysql> insert into ecom_product_view(id,description,price,stock) values(2,'Pants',100,5);
mysql> insert into ecom_product_view(id,description,price,stock) values(3,'T-Shirt',100,5);
mysql> insert into ecom_product_view(id,description,price,stock) values(4,'Shoes',100,5);
```

你可以使用浏览器（推荐 Chrome）并访问 `http://localhost:8080/`。

现在，你可以通过点击图 12-7 中的任意按钮来测试示例。点击后，订单写入数据库中会创建一个新订单。最终，订单读取数据库也会更新此新订单。同时，产品写入数据库中相应的产品库存会减一。此更改最终也会更新到产品读取数据库中。

![../images/477630_1_En_12_Chapter/477630_1_En_12_Fig7_HTML.jpg](img/477630_1_En_12_Fig7_HTML.jpg)

图 12-7.
测试分布式命令和事件处理示例

这里还有另一个需要注意的方面。你在本例中添加的新微服务负责审计日志条目，因此审计读取数据库会更新记录。当你刷新屏幕时，更改会反映在图 12-8 中。（如果更改未反映，你可以关闭浏览器会话，打开一个新的浏览器实例，然后重试上述 URL。同样，本书示例中提供的 GUI 可能存在一些小问题，因为本书的重点是微服务，而不是其 UI 的完美性。）

![../images/477630_1_En_12_Chapter/477630_1_En_12_Fig8_HTML.png](img/477630_1_En_12_Fig8_HTML.png)

图 12-8.
产品视图与产品写入数据同步

与上一节示例的不同之处在于，在当前示例中，同一个事件 `OrderCreatedEvent` 被两个事件处理器消费。这展示了架构的可扩展性。为了更清楚地说明，你可以创建第六个微服务，为同一个事件添加一个新的事件处理器，如果你能在不干扰已有五个微服务的情况下启动它，那么从下一个命令开始，该事件也将被新附加的第六个微服务消费！

总结
通过执行本章中的示例，你在本书中达到了一个重要的里程碑，因为这些是实际运行的代码，它们解释了许多本书迄今为止讨论的概念。你看到了 CQRS 应用程序在熟悉的 Spring Boot 框架中的样子，也看到了如何将其分布到多个微服务中，以及如何通过消息中间件使用事件与其他微服务通信。到目前为止，一切顺利；然而，你现在已经将“撒旦”^((26))引入了你的架构：分区。通过将第一个示例中的单个 JVM CQRS 应用程序分区为第二个示例中的多个微服务，你现在需要解决你在第 11 章中通过 CAP 定理探讨的一致性方面的问题。为了进一步详细讨论这一点，你首先应该了解微服务上下文中一致性的基础，因此请继续阅读第 13 章。

脚注

13. 分布式事务

在上一章中，你了解了如何使用 Axon 框架实现 CQRS 模式的不同场景。在本章中，你将重新审视 Axon，以应对微服务上下文中的更多场景，包括执行长时间运行的计算处理。为了有效地做到这一点，让我们从一些基础知识开始讨论，包括事务。简单形式的事务是实体之间的值变更或交换。涉及的参与方可以是单个实体或多个实体。当涉及的参与方是单个实体时，该事务通常是本地事务。如果涉及多个参与方，则根据所涉及实体的性质和位置，该事务可以归类为本地事务或分布式事务。
事务可能涉及单个步骤或多个步骤。无论是单步还是多步，事务都是一个包含这些单独步骤的单一工作单元。为了成功完成一个事务，所涉及的每个单独步骤都必须成功。如果任何单独步骤失败，事务应撤销该事务中已执行的其他步骤的所有影响。
有了这个前言，你将深入了解事务的细节，特别是分布式事务。我只会触及事务的基本要点；我会花更多时间讨论实际影响方面，尤其是在微服务的上下文中。更深入地详述事务需要一本完整的书，因此这里的讨论将仅限于围绕微服务以及一些可用于构建可靠微服务的实用工具。

在本章中，你将学习

*   计算机网络的不确定性特征

*   一般的事务方面

*   分布式事务与本地事务

*   ACID 与 BASE 事务

*   一个使用多个资源管理器演示分布式事务的完整示例



两位将军悖论
在计算机网络领域（特别是涉及传输控制协议时），文献表明 TCP 无法保证端点之间（完全）的状态一致性。这一点通过**两位将军悖论**（亦称两位将军问题、两军问题或协同攻击问题）得以阐明。这是一个思想实验，揭示了在通过不可靠链路通信以协调行动时存在的陷阱及相关设计挑战。

在任意通信故障面前，两位将军悖论是无解的；然而，该问题为任何分布式一致性协议设定了现实期望的基础。因此，要理解这个问题，你需要理解其中涉及的相关不确定性，这将为深入学习事务提供一个完美的类比。

阐述两位将军悖论

本节将通过经典示例场景来阐述两位将军悖论。图 13-1 展示了两支军队，分别由不同的将军率领，他们正联合准备进攻图中所示的设防城市。两支军队都驻扎在城市附近，但各自位于自己的山谷中。两位将军所在的两座山丘之间隔着第三座山谷，两位将军沟通的唯一方式就是派遣信使穿越这座山谷。第三座山谷被城市的守军占据，因此任何穿越该山谷的信使都有可能被俘。

![../images/477630_1_En_13_Chapter/477630_1_En_13_Fig1_HTML.jpg](img/477630_1_En_13_Fig1_HTML.jpg)

图 13-1 阐述两位将军悖论

两位将军已同意发动进攻；然而，他们尚未就进攻时间达成一致。只有当两支军队同时进攻城市时，攻击才会成功；单独进攻的军队将必死无疑。因此，将军们必须相互沟通，以决定进攻时间，并同意在该时间联合进攻；每位将军还必须知道，另一位将军知道他们已经共同同意了进攻计划。消息接收的确认信息可能与原始消息本身一样容易丢失，因此需要可能无限多的消息序列才能达成（近乎）共识。两位将军将永远无法确定他们最后派出的信使是否成功抵达。

解决方案探讨
计算机网络是分布式系统的支柱；然而，任何网络的可靠性都必须经过检验。分布式系统需要解决软件系统的可扩展性和灵活性，因此网络是必要之恶。方案应当接受网络的不确定性，而不是试图消除它；相反，应该将其缓解到可接受的程度。

在两位将军悖论中，你可以想到许多可接受的机制，例如：

*   第一位将军可以派出 50 名信使，预计所有 50 名信使都被俘的概率很低。这位将军将固定进攻时间，以便为这 50 名信使中的全部、部分或多数能够抵达另一位将军留出足够时间。然后，无论情况如何，他都会在约定的时间发起进攻，而第二位将军只要收到任何一条消息，也会发起进攻。

*   第二种方法是，第一位将军发送一连串消息，第二位将军对收到的每条消息发送确认，随着收到消息数量的增加，每位将军的信心也随之增强。

*   在第三种方法中，第一位将军可以在每条消息上做标记，标明这是 n 条消息中的第 1 条、第 2 条、第 3 条，等等。这样，第二位将军可以估算信道的可靠性，并发送适当数量的回复消息，以确保至少有一条消息被接收的概率很高。如果网络高度可靠，那么一条消息就足够了，额外的消息也无济于事；如果网络不可靠，最后一条消息丢失的可能性与第一条消息一样大。

作为将军的微服务
当你将一个“从此幸福地生活在一起”的单体应用状态，拆分为“从此幸福地分离生活”的状态时，你就将一支军队从一个将军分成了多个将军。每个微服务都有自己的将军，或者其本身就是一个将军，并且可能需要不止一位将军来协调任何有用的功能。通过采用多语言架构，每个微服务都将拥有自己的资源，无论是数据库还是文件存储。网络是这些微服务进行协调和通信的唯一途径。跨微服务对资源所做的更改的影响必须保持一致。这种一致性取决于你选择哪种模式或协议来协调和传达更改命令以及确认信息，这些确认信息充当了在微服务之间实施该更改的协议。



TCP/IP，将军之间的峡谷
互联网协议（IP）并不可靠。数据包可能延迟、丢失、重复，或到达顺序与原始意图不符。传输控制协议在 IP 之上增加了一个更可靠的层。TCP 可以重传丢失的数据包、消除重复数据包，并按照发送方意图的顺序组装数据包。然而，虽然 TCP 可以隐藏丢包、重复或乱序问题，但它可能无法消除网络中的延迟，因为网络是分组交换的。传统的固定电话网络极其可靠，音频帧延迟和通话中断非常罕见。这是因为在通话双方的整个路由上，网络会为你分配固定且有保障的带宽；这被称为电路交换。然而，数据中心网络和互联网并非电路交换。它们的主干网——以太网和 IP——是分组交换协议，会遭受排队问题，这可能导致网络中无上限的延迟。

每个微服务都有自己的内存空间用于计算，以及用于持久化和存储的资源，一个微服务无法访问另一个微服务的内存或资源（除非通过网络，通过暴露的 API 向另一个微服务发出请求）。互联网以及数据中心内部的大多数网络（通常是以太网）都具有上述特性，而这些网络成为了微服务进行协调和通信的唯一途径。在这种网络中，一个微服务可以向另一个微服务发送消息，但网络无法保证消息何时到达，甚至是否能够到达。如果你发送一个请求并期望得到响应，中间可能会出现许多问题。

发送方微服务甚至无法判断请求或消息是否已送达。次优方案是让接收方微服务发送一条响应消息，但这条消息本身也可能丢失或延迟。在异步网络中，这些问题难以区分。当一个微服务向另一个微服务发送请求但未收到响应时，不可能知道原因。处理此类问题的典型方法是使用超时机制：一段时间后，你放弃等待并假设响应永远不会到达。然而，当超时发生时，你仍然不知道远程微服务是否收到了你的请求；并且如果请求仍在某处排队，它仍有可能被送达给接收方微服务，即使发送方微服务已经放弃了之前的请求并可能已重新发送了请求，在这种情况下，将会触发一个重复的请求。针对此问题的高级解决方案在参考文献中有所论述。^(²⁷)

事务
事务帮助你控制对同一数据集的访问权限，无论是读取还是写入。处于变化状态的数据可能不一致，因此当状态从“变更中”转变为“已提交”或“已回滚”时，其他读取和写入操作应等待。现在，你将了解与事务相关的细节。

事务核心的硬件指令集
对于单个数据库节点上的事务，原子性是在存储层面实现的。当事务提交时，数据库会使事务的写入持久化（通常通过预写式日志^(²⁸)），随后将一条提交记录追加到磁盘上的日志中。因此，处理该写入操作的磁盘驱动器控制器在确认写入是否成功方面起着关键作用。

一旦提交，事务便无法撤销。你不能在事务提交后改变主意并追溯性地撤销它。这是因为，一旦数据被提交，它可能对其他事务可见，从而其他事务可能已经开始依赖该数据。

事务中的 ACID
为了符合规范，事务应具备 ACID（原子性、一致性、隔离性和持久性）属性。让我们逐一探讨。

*   **原子性：** 事务的结果，如果是提交，则事务的所有写入作为一个工作单元被持久化。如果是中止，则事务的所有写入作为一个工作单元被回滚（即撤销或丢弃）。如果你回顾上一节，我描述了正是某个特定磁盘驱动器的单个控制器位于核心，负责做出提交或回滚的决定，你需要认识到，无论是写入单个字^(²⁹)还是多个字（取决于 32 位或 64 位），正是硬件层面提供的支持，帮助你将它们组合成一个工作单元的写入。

*   **一致性：** 该属性保证，无论事务是提交还是回滚，事务都会使数据保持一致性状态。要理解“一致性状态”的含义，它指的是遵守数据库的约束或规则。通常，业务规则约束是通过数据库完整性约束来建模的。因此，维护一致性是资源管理器和应用程序双方的共同努力。应用程序确保键、参照完整性等方面的一致性，而事务管理器则确保事务的原子性、隔离性和持久性。例如，如果你的事务是将航班上的最后一个座位分配给两位同时预订该航班的旅客中的一位，那么如果该座位从空闲座位库存中移除，并分配给且仅分配给其中一位旅客，同时向另一位尝试同时预订的旅客显示该座位已不可分配或不可再分配，则该事务是一致的。

*   **隔离性：** 隔离性属性保证一个事务中正在进行的更改不会影响另一个事务访问的数据。这种隔离可以控制到更精细的级别，“事务隔离机制”一节将对此进行描述。

*   **持久性：** 该属性要求，当事务提交时，事务对数据所做的任何更改都必须被永久记录。当事务提交时，数据库会使事务的写入持久化（通常通过预写式日志），随后将一条提交记录追加到磁盘上的日志中。如果数据库在此过程中崩溃，则数据库重启后可以从日志中恢复该事务。如果提交记录在崩溃前已写入磁盘，则该事务被视为已提交。否则，该事务的所有写入都将被回滚。因此，磁盘完成写入提交记录的时刻是决定提交的唯一关键点；在此之前，仍有可能中止（由于崩溃），但在此之后，事务即被提交（即使数据库随后立即崩溃）。因此，是处理写入操作的磁盘驱动器控制器使得提交具有原子性。话虽如此，如果在此过程中发生错误或崩溃，上述日志可用于重建或重新创建数据更改。

事务模型
事务模型指的是在封闭事务的上下文中，如何组织单个事务的协调。你将在此了解主要的事务模型：



*   **扁平事务：** 在扁平事务中，当某个步骤失败时，整个事务都会回滚。扁平事务会依次完成每个步骤，然后才进入下一步。每个步骤按顺序访问相应的资源。当事务使用锁时，它一次只能等待一个对象。

*   **嵌套事务：** 在嵌套事务中，原子事务被嵌入到其他事务中。顶层事务可以开启子事务，每个子事务又可以开启更下一级的子事务，这种嵌套可以持续进行。单个嵌入事务的效果不会影响其父事务。当父事务中止时，其所有子事务也会中止。然而，当子事务中止时，父事务可以决定是否中止。在嵌套事务中，一个子事务可以是另一个嵌套事务，也可以是一个扁平事务。

*   **链式事务：** 在链式事务中，每个事务都依赖于前一个事务的结果和资源。链式事务也称为串行事务。其显著特征是，当一个事务提交时，它的资源（如游标）会被保留，并可供链中的下一个事务使用。因此，链内的事务可以看到链中前一个提交的结果；但是，链外的事务无法看到或更改链内事务正在影响的数据。如果链中某个事务失败，只有该事务会被回滚，而之前已提交的事务则不会。

*   **Saga：** Saga 与嵌套事务类似；然而，其中的每个事务都有一个对应的补偿事务。如果 Saga 中的任何一个事务失败，则会调用之前成功运行的每个事务的补偿事务。

EJB 与 Spring 中的事务属性

Spring 和 EJB 都允许用户自由选择编程式或声明式事务管理。对于编程式事务管理，你需要针对 JDBC 和 JTA API 进行编码。使用声明式方法，你可以将事务控制外部化到配置文件中。此外，你需要从可用的事务属性中进行选择，以获得所需的行为。EJB 规范定义了六个基本的事务属性。随后，Spring 为所有六个事务属性提供了对应的实现。事实上，Spring 拥有更多：

*   **PROPAGATION_REQUIRED（EJB 中的 REQUIRED）：** 支持当前事务；如果不存在则创建一个新事务。
*   **PROPAGATION_REQUIRES_NEW（EJB 中的 REQUIRES_NEW）：** 创建一个新事务；如果存在当前事务则将其挂起。
*   **PROPAGATION_NOT_SUPPORTED（EJB 中的 NOT_SUPPORTED）：** 以非事务方式执行；如果存在当前事务则将其挂起。
*   **PROPAGATION_SUPPORTS（EJB 中的 SUPPORTS）：** 支持当前事务；如果不存在则以非事务方式执行。
*   **PROPAGATION_MANDATORY（EJB 中的 MANDATORY）：** 支持当前事务；如果不存在则抛出异常。
*   **PROPAGATION_NEVER（EJB 中的 NEVER）：** 以非事务方式执行；如果存在事务则抛出异常。
*   **PROPAGATION_NESTED（EJB 中无对应项）：** 如果存在当前事务，则在嵌套事务内执行；否则行为类似于 PROPAGATION_REQUIRED。EJB 中没有类似的功能。嵌套事务的实际创建仅适用于特定的事务管理器。开箱即用的情况下，这仅适用于在 JDBC 3.0 驱动上工作的 JDBC DataSourceTransactionManager。一些 JTA 提供者也可能支持嵌套事务。

事务隔离机制

事务隔离指的是，当多个并发事务（通常针对同一数据集）发生时，保护一个事务免受其他事务影响。事务管理器依赖两种机制来实现事务隔离：

*   **锁定：** 锁定控制事务对特定数据集的访问。读锁是非独占的，允许多个事务并发读取数据。然而，写锁是独占锁，只允许单个事务更新数据。
*   **序列化：** 当多个事务并发发生时，序列化是一种保证其效果如同顺序执行（而非并发执行）的机制。锁定是强制序列化的一种手段。

事务隔离级别

所使用的序列化和锁的类型，以及它们受影响的程度，决定了事务执行时的隔离级别。Java 企业版在 `java.sql.Connection` 接口中定义了以下类型的隔离级别：

*   **TRANSACTION_NONE：** 指示不支持事务。
*   **TRANSACTION_READ_COMMITTED：** 指示防止脏读；但可能发生不可重复读和幻读^(³⁰)。
*   **TRANSACTION_READ_UNCOMMITTED：** 指示可能发生脏读、不可重复读和幻读。此级别允许一个事务更改的行，在该行任何更改提交之前，被另一个事务读取（脏读）。如果任何更改被回滚，第二个事务将检索到无效的行。
*   **TRANSACTION_REPEATABLE_READ：** 指示防止脏读和不可重复读；但可能发生幻读。
*   **TRANSACTION_SERIALIZABLE：** 指示防止脏读、不可重复读和幻读。此级别禁止以下情况：一个事务读取满足 WHERE 条件的所有行，第二个事务插入一个满足该 WHERE 条件的行，然后第一个事务重新读取相同条件，在第二次读取时检索到额外的幻影行。

序列化和锁的强制执行是宽松还是严格，取决于应用程序在数据陈旧性方面愿意容忍的并发类型。下一节将解释这个概念。

事务并发

当多个并发事务（通常针对同一数据集）发生时，一个事务受其他事务影响的程度，可以通过应用程序愿意容忍的并发级别来控制。级别如下：

*   **脏读：** 当一个事务读取了另一个事务已写入但尚未提交的数据时，就会发生脏读。
*   **不可重复读：** 如果一个事务读取数据，并且在同一个事务中重新读取相同数据时得到不同的结果，这就是不可重复读。
*   **幻读：** 当单个事务多次读取同一数据集时，另一个事务插入并添加了额外的数据，就会发生幻读。

事务隔离控制方法

严格的事务隔离级别可能导致性能不佳的场景，在这种情况下，有两种通用的锁定方法：



*   **乐观锁：** 乐观锁采取一种务实的方法，鼓励客户端保持“乐观”，认为数据在使用期间不会发生变化，并允许它们同时访问相同的数据。如果任何特定客户端的事务想要更新，当且仅当最初提供给客户端的数据与数据库中当前数据相同时，该更新才会被提交。这种方法对于热点数据不太理想，因为比较操作会频繁失败。

*   **悲观锁：** 悲观锁可能使用信号量^(³¹)或之前讨论过的任何事务机制。每次读取都需要一个读锁，每次写入都需要一个写锁。读锁不是排他性的，而写锁是排他性的。当你执行类似以下查询时，可能会获得一个典型的读锁：

```
    SELECT ∗ FROM QUOTES_TABLE WHERE QUOTES_ID=5 FOR UPDATE;
    ```

只有当数据使用完成后，这两个锁才会被释放。只要没有其他事务持有写锁，就可以将读锁授予请求的事务。对于要更新的事务，它需要一个写锁，并且在事务持有写锁期间，当且仅当其他事务不需要读锁时，才允许它们读取数据。此外，如果一个事务持有写锁，它将不允许其他事务读取其正在更改的数据，直到这些更改被提交。

企业事务类别
事务根据其使用上下文的不同而有不同的含义。它可能出现在单体系统内的多个操作上下文中，也可能出现在多个单体或多个微服务的上下文中，或者出现在具有共同（或冲突）利益的多个企业的上下文中。你将在此上下文中简要了解不同的事务类别。

ACID 事务

ACID 事务与原子事务相关，其中多个步骤或操作被执行，其意图是要么全部成功，要么由于一个或多个步骤的失败而全部回滚到之前的状态。本章前面部分的大部分讨论都涉及 ACID 事务，因此已经做了足够的解释。现在，你将了解 ACID 事务处理（TP）系统的架构。图 13-2 描绘了一个由多个资源管理器组成的典型架构。

![../images/477630_1_En_13_Chapter/477630_1_En_13_Fig2_HTML.jpg](img/477630_1_En_13_Fig2_HTML.jpg)

图 13-2
具有多个资源的分布式 ACID 事务

BASE = 切分后的 ACID
ACID 是唯一能保证数据一致性的风格。然而，如果分布式系统使用 ACID 事务，那么所有地方都必须以锁步模式进行。这要求所有组件都可用，并且还会增加数据的锁定时间。BASE 是一种架构风格，它通过将（整体的、理想的）ACID 事务切分成更小的片段（每个片段本身仍然是 ACID 的）来放宽这些要求。
消息传递用于使更改在整个系统中传播，每个更改理想情况下由一个 ACID 事务处理。这些消息是异步的而非同步的，这意味着在 BASE 系统中，第一个和最后一个 ACID 事务之间存在延迟。
这就是为什么这种风格表现出“最终一致性”：必须等待所有消息在系统中传播并在适当的地方被应用。第 11 章的“微服务的最终一致性”部分对此进行了详细讨论。
现在，我将通过逐步缩小 ACID 事务的范围，分步介绍 BASE。

BASE 事务

图 13-3 展示了一个包含四个不同微服务的 BASE 架构。每个微服务仍然具有 ACID 属性（独立于系统中的其他微服务）。

![../images/477630_1_En_13_Chapter/477630_1_En_13_Fig3_HTML.jpg](img/477630_1_En_13_Fig3_HTML.jpg)

图 13-3
BASE 架构，其中每个微服务通过 XA 事务仍然具有 ACID 保证

这是一个以 BASE 风格运行的微服务生态系统架构，它以 ACID 风格发送或接收消息。你将在后面的章节（“分布式事务示例”部分下的“设计示例场景”小节）中了解每个微服务的功能；不过，目前请先关注这里描绘的技术组件。之所以称为 BASE，是因为通过 ActiveMQ，你不需要知道也不关心消息的去向或来源；报价处理和报价结算之间看到的只是队列（而经典的 ACID 架构会为报价结算和报价处理创建一个大的 ACID 事务）。你使用 JTA/XA 来确保消息被精确处理一次，因为你不想丢失消息（或得到重复消息）。即使发生中间崩溃，也能保证从队列接收的消息能够到达数据库。即使发生中间崩溃，也能保证发送的消息的领域对象状态在数据库中得到更新。
对于需要强数据一致性保证的系统来说，这是一个很好的解决方案。在其他系统中，强数据一致性并非那么重要，你可以进一步放宽要求——通过在 BASE 架构中使用更细粒度的 ACID 事务范围。
当你过渡到微服务时，你希望避免系统范围的分布式事务。然而，与单体场景相比，你的实际数据分布得更广，因此对分布式事务的需求非常真实。正是在这种背景下，我们讨论用最终一致性替代即时一致性。第 11 章的“微服务的最终一致性”部分对此进行了详细讨论。

宽松的 BASE 事务
在某些情况下，你不太关心消息丢失或重复，并且你的 ACID 事务范围可以限制在单个资源管理器内，例如仅限纯本地事务。这提供了更高的性能，但也有代价：到目前为止，BASE 系统具有最终一致性，但这种架构默认不再保证这一点。
正如下一章将展示的，为了获得一定程度的 consistency，应用程序开发人员需要做更多的工作；在某些情况下，最终一致性甚至可能无法实现，尤其是在消息丢失的情况下。

图 13-4 展示了一个在宽松 BASE 事务中包含多个资源管理器的典型架构。

![../images/477630_1_En_13_Chapter/477630_1_En_13_Fig4_HTML.jpg](img/477630_1_En_13_Fig4_HTML.jpg)

图 13-4
具有多个资源的分布式、宽松 BASE 事务



微服务资源分布在各个节点上。你希望避免分布式事务，因为跨节点锁定资源会阻碍系统的可扩展性。此外，你可以接受最终一致性，但也不介意无法完美实现它。如果是这样，图 13-4 展示了如何遵循宽松的 BASE 模型。应用程序要么直接与一个或多个相关的资源管理器交互，要么更典型地，通过某个负载均衡器或网关进行交互，该网关将传入请求分发到一组具有对称职责和能力的资源管理器池中。请求由一个或多个资源管理器处理，随后变更会传播到一个或多个其他资源管理器。所涉及的资源管理器数量取决于分片模型（详见第 11 章的“伸缩立方体”部分）和所选的系统配置，范围从单个节点（单副本）到节点仲裁组，理论上甚至可以是给定数据记录的所有副本。

Axon 提供了许多功能，你可以利用这些功能跨资源管理器展开 BASE 事务，而无需使用分布式事务管理器或事务协调器。换句话说，在 BASE 事务中不存在分布式事务管理器或事务协调器；尽管如此，事务仍然可以跨多个资源管理器传播，以达到某种程度的最终一致性。图 13-5 描绘了这一场景，展示了 Axon 中实现此功能的关键组件。

![../images/477630_1_En_13_Chapter/477630_1_En_13_Fig5_HTML.jpg](img/477630_1_En_13_Fig5_HTML.jpg)

图 13-5
支持 BASE 事务的 Axon 组件

你将查看典型的宽松 BASE 事务步骤，请参考图 13-5。来自浏览器的写入请求将以 HTTP PUT 的形式到达 HTTP 服务器或负载均衡器。

1.  请求被路由到 API 网关。

2.  API 网关将此请求路由到 Axon 应用程序的第一个接触点，即（前端）REST 控制器。

3.  REST 控制器在解包请求后，将其解释为对实体变更的请求，并遵循 CQRS 模式创建一个命令。此命令被发送到分布式命令总线，从而启动一个本地事务。

4.  分布式命令总线将此命令传输到包含相应命令处理程序（资源管理器 3）的节点。

5.  命令处理程序借助写入数据库中的存储库，实现对实体的写入操作。

6.  上述存储库的变更还会触发一个事件（第 12 章“同一 JVM 中的命令与事件处理”部分下的“设计示例场景”一节提供了详细说明），此事件被发送到集群事件总线。

7.  在图 13-5 中，你可以看到两个不同的事件处理程序（资源管理器 1 和资源管理器 2）对先前的事件感兴趣，并且它们也可能执行各自所需的任何状态变更。

8.  你可以看到资源管理器 2 实际上是在根据系统中的任何有趣变更来同步自身（读取数据库）。

9.  任何后续的查询（HTTP GET）也会看到这些变更。最终，所有人都能看到这些变更。

ACID 与 BASE
这种比较就像比较苹果和橙子：它们并非旨在相互替代，并且在服务于各自目的方面同样出色（或糟糕）。这是一个说起来容易的论断；然而，任何 CTO 级别的讨论都不能仅凭这种直白的解释就结束。话虽如此，理解这种区别是“掌握微服务”的关键。如果你理解了这种差异，那么本书的主要意图就达到了！
BASE 事务是微服务架构的基础，我们希望在此架构中远离 ACID 事务。然而，它们服务于不同的目的。争论孰优孰劣意义不大，因为两者解决的是正交的关注点——这也是 CAP 定理之所以重要的原因。但话说回来，你是否想完全抛弃 ACID 事务？这就像问，作为战斗机的机长，你是应该直接控制弹射座椅按钮，还是即使无法直接触及，但（几乎）确信你的副驾驶会在你下令时为你激活它，你也能接受？在前一种情况下，你非常确定当突然需要时，你可以凭自己的意愿瞬间弹射逃生。在后一种情况下，你几乎确信你仍然能够逃生；然而，对于逃生是瞬间完成还是会延迟，存在一定程度的怀疑——延迟可能是由于你的副驾驶需要时间执行你的命令，或者不幸的是，你的副驾驶无法移动自己的肌肉来采取行动！重申一下，前者类似于 ACID 事务，它相对更具确定性，而后者类似于 BASE 事务，它（可能）最终会发生。
我们仍然需要 ACID，即使并非总是如此。
现在让我们来谈谈 BASE。如果你仍然在适当的层级使用 ACID（如图 13-3 所示），那么事情就简单了。另一方面，更宽松的 BASE 方法并不会让你的生活更轻松；事实上，宽松的 BASE 会给你带来比 ACID 复杂得多的问题。
话虽如此，理解和领会 BASE 事务中涉及的复杂性的一种方法是先理解 ACID 事务本身，因此你将在接下来的章节中进行学习。

重新审视分布式事务
在查看具体示例之前，你需要理解一些概念，这些概念将为你要探索的示例设定上下文。

本地事务
如果你使用一个典型的资源管理器，通常该单个资源会被限制在单个主机或节点上（尽管这并非强制要求）。局限于这种单个资源的操作就是本地事务，它们只影响一个事务性资源。在单个节点内，非确定性操作较少（或者出于实际考虑，为零），因此发送到单个节点的命令必须被认为是确定性的，并且在发生任何灾难时，存在本地恢复机制。这些资源拥有自己的事务性 API，事务的概念通常表现为会话的概念，会话可以封装一个工作单元，并带有界定 API，用于告知资源何时应将缓冲的工作提交到底层资源。因此，从开发者的角度来看，你在本地事务中并非通过事务进行管理，而只是通过“连接”进行管理。



`java.sql.Connection` 接口是一种事务性资源，可以封装一个数据库。默认情况下，`Connection` 对象处于自动提交模式，这意味着它在执行每条语句后会自动提交更改。如果禁用了自动提交模式，则必须显式调用 `commit` 方法来提交更改；否则，数据库更改将不会保存。当有多个语句时，最好将几个相关的语句收集到一个批处理中，然后要么全部提交，要么全部不提交。实现方法是先将 `Connection` 的 `setAutoCommit()` 方法设置为 `false`，然后在批处理结束时显式调用 `Connection.commit()` 或 `Connection.rollback()`。请参见清单 13-1。

```
try{
connection.setAutoCommit(false);
Statement statement = conn.createStatement();
String insertString = "insert into " +  dbName + ".SHIPPING VALUES ("
+ orderId + ", 'NEW')";
statement.executeUpdate(insertString);
String updateString = "update " + dbName + ".ORDERS set STATUS =
'PROCESSED' where ORDER_ID = " + orderId;
statement.executeUpdate(updateString);
connection.commit();
}catch(SQLException se){
connection.rollback();
}
清单 13-1
一个本地事务示例
```

在清单 13-1 中，这两个语句将一起提交或一起回滚。然而，这是一个本地事务，而不是分布式事务。

**分布式事务**

通常，分布式事务存在的场景必须跨越至少两个资源管理器。数据库、消息队列、事务处理（TP）监控器（如 IBM 的 CICS、BEA 的 Tuxedo、SAP Java Connector 和 Siebel Systems）是常见的事务性资源，并且通常，如果事务必须是分布式的，它必须跨越其中几个资源。分布式事务可以被视为一个原子操作，必须在分布在多个不同物理位置的多个参与资源之间进行同步（或提供 ACID 属性）。

像 Oracle Weblogic 和 IBM Websphere 这样的 Java EE 应用服务器原生支持 JTA，并且还有第三方独立的 JTA 实现，例如：

*   **JOTM：** JOTM 是一个用 Java 实现的开源事务管理器。它支持多种事务模型和规范，为使用各种中间件平台（J2EE、CORBA、Web Services、OSGi）的客户端提供事务支持。

*   **Narayana：** Narayana，以前称为 JBossTS 和 Arjuna Transaction Service，附带一个非常健壮的实现，支持 JTA 和 JTS API。它支持三种扩展事务模型：嵌套顶层事务、嵌套事务和基于 Saga 的补偿模型。此外，它还支持 Web 服务和 RESTful 事务。需要手动与 Spring 框架集成，但它提供了与 Spring Boot 的原生集成。

*   **Atomikos TransactionsEssentials：** Atomikos TransactionsEssentials 是一个生产级质量的实现，除了 JTA API 之外，还支持恢复和一些特殊功能。它提供了开箱即用的 Spring 集成，并支持数据库和 JMS 资源的连接池。

*   **Bitronix JTA：** Bitronix 声称其事务恢复能力与某些商业产品相当甚至更好。Bitronix 还提供了开箱即用的连接池和会话池。

**Java 中的分布式事务**

跨越多个资源的事务协调由 opengroup 的 X/Open 标准规定。Java 通过提供两个接口来支持 X/Open 标准：JTA（Java 事务 API）和 JTS（Java 事务服务）。如图 13-2 所示，应用程序开发人员使用 JTA 与事务管理器通信。由于资源可以由遵循不同平台和编程语言的多个供应商提供，如果所有这些资源必须协调，它们必须再次遵守 X/Open 标准。这里，遵循 CORBA OTS（对象事务服务）的 JTS 提供了与分布式资源共存的不同事务管理器之间所需的互操作性。我已经讨论了分布式事务中的“分布式特性”。两阶段提交协议用于跨多个资源管理器协调事务。

**使用 MySQL、ActiveMQ、Derby 和 Atomikos 的分布式事务示例**

解决许多数据一致性问题的简单方法，尤其是在涉及多个资源管理器时，是使用 XA 或分布式事务，即使在 BASE 中也是如此，如图 13-3 所示。然而，如果您想过渡到更宽松的 BASE 微服务，设计机制必须具有更高的容错性，因为您的 XA 事务管理器不存在，而它们本应完成所有繁重的工作。您将借助一个示例来了解主要的一致性关注场景。同样，我本可以在示例中直接演示关注场景而不使用分布式事务；但是，我将采用相反的方式，使用 XA 事务来说明完美场景，并通过进一步缩小 ACID 范围来模拟各种故障条件，因为这样更容易让您（读者）理解各个方面。XA 事务允许我们通过“滥用”事务属性来使事务范围比正常情况下更小。这意味着我可以重用相同的代码来逐步说明宽松 BASE 的异常情况。

**示例场景**

如图 13-3 所示，该示例并不简单，因此需要稍作解释。示例场景是一个简单的股票交易处理系统。以下是其构建模块：

1.  股票交易的新报价可以推送到 Broker Web 微服务。新报价被插入到 MySQL 数据库中的 `Quotes` 表中，状态为“New”。

2.  Quotes 处理器任务是一个 quartz 调度任务，它轮询 MySQL 数据库中的 `Quotes` 表，查找任何状态为“New”的新报价。

3.  当找到状态为“New”的新报价时，它会在事务中调用 broker 服务的 `processNewQuote` 方法，并将新报价的唯一标识符传递给 `Quotes` 表。

4.  broker 服务使用其他事务性服务（拍卖服务和股票订单服务），并且两者的执行必须是原子的。

5.  拍卖服务通过在一个事务内将报价状态更改为“Confirmed”来确认收到的报价。

6.  股票订单服务根据新报价中包含的信息创建一条 JMS 消息，并将其发送到 ActiveMQ 队列以进行报价结算，同样在上述（5）的事务内进行。

7.  结算监听器服务正在监听 ActiveMQ 队列，以获取任何新的已确认报价。所有到达 ActiveMQ 队列的已确认报价都会在事务内被结算监听器服务的 `onMessage` 方法拾取。

8.  结算监听器服务调用报价对账服务进行报价对账，同样在上述（7）的事务内进行。



9.  报价对账服务需要根据上述第（7）条交易，将已交易股票的价值与卖方和买方各自的用户账户进行对账。

10. Broker Web 微服务和 Settlement Web 微服务只是实用工具，用于提供 `Quotes` 表和 `User` 表上操作数据的仪表盘视图，以呈现实时状态。

了解了示例场景的高级业务逻辑后，现在让我们关注架构中的其他基础设施方面。报价处理微服务必须对 MySQL 和 ActiveMQ 这两个资源执行原子操作。这里需要一个分布式事务管理器。同样，报价结算微服务也必须对 Derby 和 ActiveMQ 这两个资源执行原子操作。这里也需要一个分布式事务管理器。

**编写示例场景代码**

演示分布式事务示例所需的完整代码位于文件夹 `ch13\ch13-01` 中。需要编写四个微服务。我们将逐一查看它们。

**微服务 1：报价处理 (Broker-MySQL-ActiveMQ)**

请访问 `pom.xml` 查看 Atomikos 分布式事务管理器依赖项。参见清单 13-2。

```
javax.transaction
jta
1.1

com.atomikos
transactions
3.9.3

com.atomikos
transactions-hibernate3
3.9.3

com.atomikos
transactions-api
3.9.3

com.atomikos
transactions-jms
3.9.3

com.atomikos
transactions-jdbc
3.9.3

com.atomikos
transactions-jta
3.9.3

org.apache.activemq
activemq-core
5.7.0

mysql
mysql-connector-java
8.0.14

清单 13-2
使用 MySQL 和 ActiveMQ 的分布式事务示例的 Maven 依赖项 (ch13\ch13-01\XA-TX-Distributed\Broker-MySQL-ActiveMQ\pom.xml)
```

当你详细查看配置时，这些库依赖项会变得更加清晰。在此之前，让我们先检查架构中的主要组件。

你首先会看到报价处理器任务，这是一个 Quartz 调度器，它会在配置的间隔时间超时，从而在固定的时间间隔触发 `processNewQuotes()` 方法。参见清单 13-3。

```
public class QuotesProcessorTask {
@Autowired
@Qualifier("brokerServiceRequired_TX")
BrokerService brokerServiceRequired_TX;
public void processNewQuotes() {
List newQuotes = brokerServiceRequired_TX.findNewQuotes();
newQuotes.forEach(item->{
if(((QuoteDTO) item).getStatus().equals(Quote.NEW)){
brokerServiceRequired_TX.processNewQuote(
((QuoteDTO) item).getId());
}
});
}
}
清单 13-3
处理新报价的定时任务 (ch13\ch13-01\XA-TX-Distributed\Broker-MySQL-ActiveMQ\src\main\java\com\acme\ecom\schedule\QuotesProcessorTask.java)
```

如果找到“新”报价，调度器会为每个新报价调用一次 broker 服务的 `processNewQuote()` 方法，并传入找到的新报价的 ID。请注意，此方法调用发生在一个事务内，其配置你很快就会看到。接下来，你将在清单 13-4 中查看 broker 服务。

```
public class BrokerServiceImpl implements BrokerService{
private static volatile boolean flipFlop = false;
@Autowired
@Qualifier("auctionServiceRequired_TX")
AuctionService auctionServiceRequired_TX;
@Autowired
@Qualifier("stockOrderServiceRequired_TX")
StockOrderService stockOrderServiceRequired_TX;
@Autowired
@Qualifier("auctionServiceRequiresNew_TX")
AuctionService auctionServiceRequiresNew_TX;
@Autowired
@Qualifier("stockOrderServiceRequiresNew_TX")
StockOrderService stockOrderServiceRequiresNew_TX;
private static synchronized void flipFlop() throws QuotesBaseException{
if(flipFlop){
flipFlop = false;
}
else{
flipFlop = true;
}
if(flipFlop){
throw new QuotesBaseException("Explicitly thrown by Broker
Application to Roll Back!");
}
}
@Override
public List findNewQuotes(){
List newQuotes = auctionServiceRequired_TX.findNewQuotes();
return newQuotes;
}
@Override
public void processNewQuote(Long id)throws QuotesBaseException{
Optional quoteQueried =
auctionServiceRequired_TX.findQuoteById(id);
QuoteDTO quoteDTO = (QuoteDTO) quoteQueried.get();
Integer testCase = quoteDTO.getTest();
If((testCase == 1)  || (testCase == 5) || (testCase == 6)
|| (testCase == 7)){
auctionServiceRequired_TX.confirmQuote(quoteDTO);
stockOrderServiceRequired_TX.sendOrderMessage(quoteDTO);
}
else if(testCase == 2){
auctionServiceRequired_TX.confirmQuote(quoteDTO);
stockOrderServiceRequired_TX.sendOrderMessage(quoteDTO);
flipFlop();
}
else if(testCase == 3){
auctionServiceRequired_TX.confirmQuote(quoteDTO);
try{
stockOrderServiceRequiresNew_TX.sendOrderMessage(quoteDTO);
}
catch(QuotesMessageRollbackException
quotesMessageRollbackException){
LOGGER.error(quotesMessageRollbackException.getMessage());
}
}
else if(testCase == 4){
try{
auctionServiceRequiresNew_TX.confirmQuote(quoteDTO);
}
catch(QuotesConfirmRollbackException
quotesConfirmRollbackException){
LOGGER.error(quotesConfirmRollbackException.getMessage());
}
stockOrderServiceRequired_TX.sendOrderMessage(quoteDTO);
}
else if(testCase == 8){
try{
auctionServiceRequiresNew_TX.confirmQuote(quoteDTO);
// PROPAGATION_REQUIRES_NEW Because, during next time out
// of QuotesProcessorTask we shouldn't fetch this quote
}
catch(QuotesConfirmRollbackException
quotesConfirmRollbackException){
LOGGER.error(quotesConfirmRollbackException.getMessage());
}
stockOrderServiceRequired_TX.sendOrderMessage(quoteDTO);
}
else{
LOGGER.debug("Undefined Test Case");
}
}
}
清单 13-4
协调新报价处理的 Broker 服务 (ch13\ch13-01\XA-TX-Distributed\Broker-MySQL-ActiveMQ\src\main\java\com\acme\ecom\service\BrokerServiceImpl.java)
```



你在 broker 服务中自动配置了对拍卖服务和股票订单服务的引用。每个服务都有两个引用；这只是一个用于安排各种测试场景（你将进行验证）的临时方案。为了清晰起见，你拥有两个拍卖服务的引用，名称分别为 `auctionServiceRequired_TX` 和 `auctionServiceRequiresNew_TX`。顾名思义，`auctionServiceRequired_TX` 中的写入方法在 `PROPAGATION_REQUIRED` 上下文中执行，而 `auctionServiceRequiresNew_TX` 中的写入方法则在 `PROPAGATION_REQUIRES_NEW` 上下文中执行。其余代码检查传入的参数，你在此参数中附带了一个指示器（一个整数值），用于标识你正在执行哪个测试场景（测试用例）。总体策略是：如果你希望在父服务和子服务之间传播事务，则使用 `PROPAGATION_REQUIRED`；在你显式生成错误条件的地方，则使用 `PROPAGATION_REQUIRES_NEW`，以便将错误限制在相应服务方法的上下文内，并且以受控的方式（通过使用 try-catch）进行处理，从而保证其余执行流程能够继续进行；再次说明，这是为了便于演示而设置的测试装置。

你需要意识到，在实际的生产场景中，你并不需要这些测试场景的安排；因此，如果你希望跨服务实现原子操作，只需使用 `PROPAGATION_REQUIRED` 配置所有内容，即仅使用 `(testCase == 1)` 这一场景。

在 broker 服务中还定义了一个名为 `flipFlop()` ^(³²) 的实用方法。此方法在两次连续执行之间翻转操作中错误模拟的创建状态，因此，如果你在一次方法执行中模拟了错误，那么下一次执行相同方法时就不会模拟错误。通过这种方式，如果你执行了一个测试用例来验证某个错误场景，你也能看到在相同流程没有该错误场景执行时的不变量情况。如果你想查看消息消费是否在第一次执行时失败，以便在尝试重新消费时，下一次执行能够无故障进行，那么这个方法就很有用；这样，你就能轻松测试并可视化结果。暂时先把它放在这里；当你执行测试时，它会变得更清晰。

接下来，查看清单 13-5 中所示的拍卖服务。

```
@Service
public class AuctionServiceImpl implements AuctionService{
@Autowired
private QuoteRepository quoteRepository;
@Override
public QuoteDTO confirmQuote(QuoteDTO quoteDTO)
throws QuotesConfirmRollbackException{
Integer testCase = quoteDTO.getTest();
Optional quoteQueried = quoteRepository.findById(quoteDTO.getId());
Quote quote = null;
Quote quoteSaved = null;
if(quoteQueried.isPresent()){
quote = (Quote) quoteQueried.get();
quote.setStatus(Quote.CONFIRMED);
quote.setUpdatedAt(new Date());
quoteSaved = quoteRepository.save(quote);
}
if(testCase == 4){
flipFlop();
}
return getQuoteDTOFromQuote((Quote) quoteQueriedAgain.get());
}
}
清单 13-5
将新报价确认到 MySQL 数据库的拍卖服务 (ch13\ch13-01\XA-TX-Distributed\Broker-MySQL-ActiveMQ\src\main\java\com\acme\ecom\service\AuctionServiceImpl.java)
```

拍卖服务将新报价的状态更改为“已确认”。

broker 服务在调用拍卖服务后，将调用股票订单服务。股票订单服务是一个 JMS 消息发送器，如清单 13-6 所示。

```
public class StockOrderServiceImpl implements StockOrderService{
private JmsTemplate jmsTemplate;
public void setJmsTemplate(JmsTemplate jmsTemplate) {
this.jmsTemplate = jmsTemplate;
}
public void sendOrderMessage(final QuoteDTO quoteDTO)
throws QuotesMessageRollbackException{
Integer testCase = quoteDTO.getTest();
jmsTemplate.send(new MessageCreator() {
public Message createMessage(Session session) throws JMSException {
return session.createObjectMessage(quoteDTO);
}
});
if(testCase == 3){
throw new QuotesMessageRollbackException(
"Explicitly thrown by Message Sender to Roll Back!");
}
}
}
清单 13-6
针对已确认报价向 ActiveMQ 发送消息的股票订单服务 (ch13\ch13-01\XA-TX-Distributed\Broker-MySQL-ActiveMQ\src\main\java\com\acme\ecom\messaging\StockOrderServiceImpl.java)
```

为了代码的完整性，清单 13-7 展示了 `Quote` 模型类。

```
@Entity
@Table(name = "quote")
@Data
@EqualsAndHashCode(exclude = { "id" })
public class Quote{
public static final String NEW = "New";
public static final String CONFIRMED = "Confirmed";
@Id
@GeneratedValue(strategy = GenerationType.AUTO)
private Long id;
@NotBlank
@Column(name = "symbol", nullable = false, updatable = false)
private String symbol;
@Column(name = "sellerid", nullable = false, updatable = false)
private Long sellerId;
@Column(name = "buyerid", nullable = false, updatable = false)
private Long buyerId;
@Column(name = "amount", nullable = false, updatable = false)
private Float amount;
@Column(name = "status", nullable = false, updatable = true)
private String status;
@Column(name = "test", nullable = true, updatable = true)
private Integer test;
@Column(name = "delay", nullable = true, updatable = true)
private Integer delay = 0;
@Column(name = "createdat", nullable = true, updatable = false)
@Temporal(TemporalType.TIMESTAMP)
private Date createdAt;
@Column(name = "updatedat", nullable = true, updatable = true)
@Temporal(TemporalType.TIMESTAMP)
private Date updatedAt;
}
清单 13-7
报价实体 (ch13\ch13-01\XA-TX-Distributed\Broker-MySQL-ActiveMQ\src\main\java\com\acme\ecom\model\quote\Quote.java)
```

以上就是该示例的所有主要 Java 文件。将上述类与 Atomikos XA 事务管理器进行连接的主要配置在文件 `spring-sender-mysql.xml` 中完成，如清单 13-8 所示。

```

PROPAGATION_REQUIRED,
-QuotesMessageRollbackException, +QuotesNoRollbackException

PROPAGATION_REQUIRES_NEW,
-QuotesMessageRollbackException, +QuotesNoRollbackException

PROPAGATION_REQUIRED,
-QuotesConfirmRollbackException, +QuotesNoRollbackException

PROPAGATION_SUPPORTS, readOnly

PROPAGATION_REQUIRES_NEW,
-QuotesConfirmRollbackException,
+QuotesNoRollbackException
PROPAGATION_SUPPORTS, readOnly

PROPAGATION_REQUIRED,
-QuotesBaseException, +QuotesNoRollbackException

PROPAGATION_SUPPORTS, readOnly

清单 13-8
使用 MySQL、ActiveMQ 和 Atomikos 的分布式事务示例的 Spring 连接配置 (ch13\ch13-01\XA-TX-Distributed\Broker-MySQL-ActiveMQ\src\main\resources\spring-sender-mysql.xml)
```



你在之前的“分布式事务再探讨”部分中提到了本地事务，而 Spring 的 `PlatformTransactionManager` 支持 JDBC、JMS、AMQP、Hibernate、JPA、JDO 以及许多其他使用场景中的本地事务。当你想使用 XA 事务时，可以使用 `org.springframework.transaction.jta.JtaTransactionManager`。`JtaTransactionManager` 是 JTA 的 `PlatformTransactionManager` 实现，它将工作委托给后端的 JTA 提供者。这通常用于委托给 Java EE 服务器的事务协调器，例如 Oracle WebLogicJtaTransactionManager 或 IBM WebSphereUowTransactionManager，但也可以配置为使用嵌入在应用程序中的本地 JTA 提供者，例如 Atomikos。嵌入式事务管理器让你无需使用完整的应用服务器；在独立的 JVM 应用程序中，你仍然可以获得与应用服务器提供的 XA 事务支持类似的大部分功能。

在当前示例中，有两个资源管理器中的操作需要具有事务性：

*   `AuctionService.confirmQuote()`

*   `StockOrderService.sendOrderMessage()`

第一个操作使用 JPA 仓库将实体的变更状态合并到底层仓库，并随后合并到持久化存储中。第二个操作使用 JMS 将消息发送到 ActiveMQ 队列。你希望使上述两个操作具有原子性，即要么两者都成功，要么两者都回滚。为了简化控制，你将上述两个事务性方法封装在第三个方法中：

*   `BrokerService.processNewQuote()`

你现在利用 `org.springframework.transaction.interceptor.` `TransactionProxyFactoryBean`，它旨在覆盖声明式事务划分的典型用例，通过使用事务代理包装单例目标对象，代理目标对象实现的所有接口。这是在 XML 配置中完成的。然后，你还需要为所有三个方法配置所需的事务类型：

*   `AuctionService.confirmQuote()`

*   `<prop key="confirm*">PROPAGATION_REQUIRED</prop>`

*   `StockOrderService.sendOrderMessage()`

*   `<prop key="sendOrderMessage*">PROPAGATION_REQUIRED</prop>`

*   `BrokerService.processNewQuote()`

*   `<prop key="process*">PROPAGATION_REQUIRED</prop>`

此外，向 Spring 框架的事务基础设施指示事务工作应被回滚的推荐方法是从当前在事务上下文中执行的代码中抛出异常。Spring 框架的事务基础设施代码会在未处理的异常沿调用栈向上冒泡时捕获它，并将该事务标记为回滚。这就是连接业务组件所需做的全部工作。现在，如果上述三个方法中的任何一个出现错误，它将回滚所有三个业务方法在参与 XA 事务的底层资源管理器中所影响的状态变更。

你现在可以专注于基础设施的搭建了。需要配置两个资源：JMS 资源和 JDBC 资源。XA JMS 资源请参见清单 13-9。

```

清单 13-9
分布式事务示例中 XA JMS 资源的 Spring 配置 ActiveMQ Atomikos (ch13\ch13-01\XA-TX-Distributed\Broker-MySQL-ActiveMQ\src\main\resources\spring-sender-mysql.xml)
```

`StockOrderService` 使用的 `JmsTemplate` 使用了 `com.atomikos.jms.AtomikosConnectionFactoryBean`，这是 Atomikos 的 JMS 1.1 连接工厂，用于支持 JTA 的 JMS 操作。你可以使用此类的实例使 JMS 参与 JTA 事务，而无需自己处理底层的 XA 调用。你可以通过 `localTransactionMode` 属性设置是否需要本地事务，该属性默认为 false。使用本地事务时，不会进行 XA 登记；相反，应用程序应执行会话级别的 JMS 提交或回滚。请注意，此功能还需要你的 JMS 提供者（在你的案例中是 ActiveMQ）的支持。`xaConnectionFactory` 属性是封装了连接到 ActiveMQ 代理所需基础设施的基本连接工厂。在你的案例中，该 bean 是 `ActiveMQXAConnectionFactory` 类型的一个实例，这是一个特殊的连接工厂类，当你希望连接到支持 XA 事务的 ActiveMQ 代理时必须使用它。

XA JDBC 资源请参见清单 13-10。

```

JDBC-1

xads1

jdbc:mysql://localhost:3306/ecom01

true

root
rootpassword

true

com.acme.ecom.AtomikosJtaPlatform

JTA

清单 13-10
使用 MySQL 和 Atomikos 的分布式事务示例中 XA JDBC 资源的 Spring 配置 (ch13\ch13-01\XA-TX-Distributed\Broker-MySQL-ActiveMQ\src\main\resources\spring-sender-mysql.xml)
```

如果你想使用支持 Atomikos JTA 的连接池，可以使用 `com.atomikos.jdbc.` `AtomikosDataSourceBean` 类的一个实例。你需要构造一个实例并设置所需的属性，生成的 bean 将自动向事务服务注册并参与活动事务。通过从此类获取的连接执行的所有 SQL 都将参与 JTA 事务。从 Connector/J 5.0.0 开始，`javax.sql.XADataSource` 接口通过 `com.mysql.jdbc.jdbc2.optional.MysqlXADataSource` 类实现，该类在与 MySQL 服务器 5.0 及更高版本结合使用时支持 XA 分布式事务。你将 `MysqlXADataSource` 的一个实例设置为 `AtomikosDataSourceBean` 类的 `xaDataSource` 属性。
接下来，`LocalContainerEntityManagerFactoryBean` 是 Spring 的 FactoryBean，它创建一个 JPA EntityManagerFactory，然后可以通过依赖注入将其传递给基于 JPA 的 DAO。从 Spring 3.1 开始，不再需要 `persistence.xml`，`LocalContainerEntityManagerFactoryBean` 支持一个 `packagesToScan` 属性，可以在其中指定要扫描 `@Entity` 类的包。

为了让 Hibernate 知道如何参与 Atomikos 事务，之前你必须设置一个属性，即 `hibernate.transaction.manager_lookup_class`。但是，Hibernate 4.3 移除了长期弃用的 TransactionManagerLookup。Hibernate4 默认不知道如何与 Atomikos 协同工作。JTA 提供者必须实现 `org.hibernate.engine.transaction.jta.platform.spi.JtaPlatform`，以从 Spring 配置的 `JtaTransactionManager` 实现中解析 JTA UserTransaction 和 TransactionManager。Hibernate 中已经提供了一个 JTA Platform 的抽象实现，即 `org.hibernate.engine.transaction.jta.platform.internal.AbstractJtaPlatform`。使用它使得为 Atomikos 编写 JTA Platform 变得轻而易举。你需要实现这个类，并显式设置 `hibernate.transaction.jta.platform` 属性。请参见清单 13-11。



```
import javax.transaction.TransactionManager;
import javax.transaction.UserTransaction;
import org.hibernate.engine.transaction.jta.platform.internal.AbstractJtaPlatform;
import org.springframework.transaction.jta.JtaTransactionManager;
@SuppressWarnings("serial")
public class AtomikosJtaPlatform extends AbstractJtaPlatform {
static TransactionManager transactionManager;
static UserTransaction userTransaction;
@Override
protected TransactionManager locateTransactionManager() {
Assert.notNull(transactionManager,
"TransactionManager has not been setted");
return transactionManager;
}
@Override
protected UserTransaction locateUserTransaction() {
Assert.notNull(userTransaction, "UserTransaction has not been setted");
return userTransaction;
}
public void setJtaTransactionManager(JtaTransactionManager
jtaTransactionManager) {
transactionManager = jtaTransactionManager.getTransactionManager();
userTransaction = jtaTransactionManager.getUserTransaction();
}
public void setTransactionManager(TransactionManager transactionManager) {
this.transactionManager = transactionManager;
}
public void setUserTransaction(UserTransaction userTransaction) {
this.userTransaction = userTransaction;
}
}
清单 13-11
解析 JTA UserTransaction 和 TransactionManager（ch13\ch13-01\XA-TX-Distributed\Broker-MySQL-ActiveMQ\src\main\java\com\acme\ecom\AtomikosJtaPlatform.java）
```

在了解了整体配置之后，我们再来详细看看基础设施配置，请参考清单 13-9 和清单 13-10。
你需要将 `LocalContainerEntityManagerFactoryBean` 的 `dataSource` 属性设置为一个 JDBC 数据源，JPA 持久化提供程序将使用该数据源来访问数据库。此处传入的 DataSource 将作为 `nonJtaDataSource` 用于传递给 PersistenceProvider 的 PersistenceUnitInfo。请注意，此变体通常也适用于 JTA 事务管理；如果不适用，请考虑改用显式属性 `jtaDataSource`。你可以使用前面解释过的 `AtomikosDataSourceBean` 来设置此属性。

接下来，你需要一个服务提供者接口实现，它允许你将特定于供应商的行为插入到 Spring 的 EntityManagerFactory 创建器中。`HibernateJpaVendorAdapter` 是 Hibernate EntityManager 的一个实现。它支持检测带注解的包以及其他功能。Spring Data 的 JPA 模块包含一个自定义命名空间 `<jpa:repositories>`，该命名空间允许定义仓库 bean。它还包含某些特定于 JPA 的特性和元素属性。通常，JPA 仓库可以使用 `repositories` 元素来设置。在此示例中，Spring 被指示扫描 `com.acme.ecom.repository.quote` 及其所有子包，以查找扩展了 `Repository` 或其子接口的接口。对于找到的每个接口，基础设施会注册特定于持久化技术的 FactoryBean，以创建适当的代理来处理查询方法的调用。每个 bean 都以从接口名称派生的 bean 名称注册，因此你的 `QuoteRepository` 接口将以 `quoteRepository` 名称注册。`entity-manager-factory-ref` 属性将有助于显式地将 EntityManagerFactory 与 `repositories` 元素检测到的仓库进行关联。当应用程序中使用多个 EntityManagerFactory bean 时，通常会使用此属性。如果未显式配置，Spring 将自动查找在 ApplicationContext 中配置的单个 EntityManagerFactory。

至此，两个 XA 资源的配置完成。接下来，你必须配置 XA 事务管理器。

你需要 `org.springframework.transaction.jta.JtaTransactionManager`，它是 `PlatformTransactionManager` 的一个实现。你需要提供 `javax.transaction.TransactionManager` 和 `javax.transaction.UserTransaction` 的实现来创建一个 `JtaTransactionManager`。

`javax.transaction.UserTransaction` 接口为应用程序提供了以编程方式控制事务边界的能力。此接口可由 Java 客户端程序或企业级 Java Bean 使用。`UserTransaction.begin()` 方法启动一个全局事务，并将该事务与调用线程关联。事务与线程的关联由事务管理器透明地管理。因此，`UserTransaction` 是面向用户的 API。

`com.atomikos.icatch.jta.UserTransactionManager` 是 `javax.transaction.TransactionManager` 的一个简单、零配置的实现。独立的 Java 应用程序可以使用此类的实例来获取事务管理器的句柄，并在首次使用时自动启动或恢复事务服务。`com.atomikos.icatch.jta.UserTransactionImp` 是 Atomikos 提供的 `javax.transaction.UserTransaction` 实现，你可以将其用于独立的 Java 应用程序。此类同样会在首次使用时自动启动并恢复事务服务。

`org.springframework.transaction.jta.JtaTransactionManager` 是 JTA 的 PlatformTransactionManager 实现，它将工作委托给后端 JTA 提供程序。它通常用于委托给 Java EE 服务器的事务协调器，但在你的案例中，你配置的是嵌入在应用程序中的 Atomikos JTA 提供程序。



微服务 2：Broker-Web

该微服务结构简单，包含 REST 控制器。您可以通过向此微服务发送 HTTP 请求来调用测试用例。您还可以持续观察仪表盘，它将为您提供 `Quotes` 表的视图，以便您直观地看到测试效果。图 13-6 展示了如何与 Broker Web 微服务进行各种交互。

![../images/477630_1_En_13_Chapter/477630_1_En_13_Fig6_HTML.jpg](img/477630_1_En_13_Fig6_HTML.jpg)

图 13-6 Broker Web 控制台

微服务 3：报价结算 (Settlement-ActiveMQ-Derby)

查看 `pom.xml` 文件以了解 Atomikos 分布式事务管理器依赖项。您在微服务 2（Broker-MySQL-ActiveMQ 微服务）中已经拥有所有已描述的依赖项。但是，如架构图所示，您正在使用 Derby 数据库代替 MySQL 进行下游的报价结算。我不会重复您已经见过的依赖项；清单 13-12 仅显示了 Derby 客户端的依赖项。

```xml
<dependency>
    <groupId>org.apache.derby</groupId>
    <artifactId>derbyclient</artifactId>
    <version>10.14.1.0</version>
</dependency>
```

清单 13-12 Derby 客户端的 Maven 依赖项 (ch13\ch13-01\XA-TX-Distributed\Settlement-ActiveMQ-Derby\pom.xml)

我使用 Derby 数据库代替 MySQL 的意图是让您（读者）熟悉如何将 XA 事务与另一个符合 XA 标准的资源（Derby 数据库）结合使用，以便在后续练习中，您拥有所有工具，可以自行尝试在单个 XA 事务中跨两个符合 XA 标准的数据库进行 XA 事务。但您不会在本文中尝试该练习，因为它超出了我们的讨论范围。

当新的报价消息到达 ActiveMQ 队列时，结算监听器会立即将其拾取。结算监听器是一个 JMS 监听器，它在一个事务中从队列消费消息。请参见清单 13-13。

```java
public class SettlementListener implements MessageListener {
    @Autowired
    @Qualifier("quotesReconcileServiceRequired_TX")
    QuotesReconcileService quotesReconcileServiceRequired_TX;
    @Autowired
    @Qualifier("quotesReconcileServiceRequiresNew_TX")
    QuotesReconcileService quotesReconcileServiceRequiresNew_TX;
    public void onMessage(Message message) {
        try {
            reconcile((QuoteDTO) objectMessage.getObject());
        }
        catch (JMSException e) {
            throw new RuntimeException(e);
        }
        catch (QuotesBaseException e) {
            throw new RuntimeException(e);
        }
    }
    private void reconcile(QuoteDTO quoteDTO)throws QuotesBaseException{
        Integer testCase = quoteDTO.getTest();
        if(testCase.equals(1) || testCase.equals(2) || testCase.equals(4) ||
           testCase.equals(8)){
            quotesReconcileServiceRequired_TX.reconcile(quoteDTO);
        }
        else if(testCase.equals(5)){
            try{
                quotesReconcileServiceRequiresNew_TX.reconcile(quoteDTO);
            }
            catch(QuotesBaseException quotesBaseException){
                LOGGER.error(quotesBaseException.getMessage());
            }
            flipFlop();
        }
        else if(testCase.equals(6)){
            try{
                quotesReconcileServiceRequiresNew_TX.reconcile(quoteDTO);
            }
            catch(QuotesBaseException quotesBaseException){
                LOGGER.error(quotesBaseException.getMessage());
            }
        }
        else if(testCase.equals(7)){
            try{
                quotesReconcileServiceRequired_TX.reconcile(quoteDTO);
            }
            catch(QuotesBaseException quotesBaseException){
                LOGGER.error(quotesBaseException.getMessage());
            }
            flipFlop();
        }
        else{
            LOGGER.debug("Undefined Test Case");
        }
    }
}
```

清单 13-13 编排消息消费和报价结算的事务性消息监听器 (ch13\ch13-01\XA-TX-Distributed\Settlement-ActiveMQ-Derby\src\main\java\com\acme\ecom\messaging\SettlementListener.java)

消费消息后，结算监听器将调用 `QuotesReconcileService.reconcile(QuoteDTO)` 方法来对账和结算报价，这同样在一个事务中完成。请参见清单 13-14。

```java
public class QuotesReconcileServiceImpl implements QuotesReconcileService{
    @Autowired
    private UserRepository userRepository;
    @Override
    public void reconcile(QuoteDTO quoteDTO)throws QuotesBaseException{
        Integer testCase = quoteDTO.getTest();
        Optional<User> sellerQueried =
            userRepository.findById(quoteDTO.getSellerId());
        Optional<User> buyerQueried  =
            userRepository.findById(quoteDTO.getBuyerId());
        User seller = null;
        User buyer = null;
        User sellerSaved = null;
        User buyerSaved = null;
        Date updatedDate = null;
        seller = (User) sellerQueried.get();
        buyer  = (User) buyerQueried.get();
        updatedDate = new Date();
        seller.setAmountSold(seller.getAmountSold() + quoteDTO.getAmount());
        seller.setUpdatedAt(updatedDate);
        seller.setLastQuoteAt(quoteDTO.getCreatedAt());
        sellerSaved = userRepository.save(seller);
        buyer.setAmountBought(buyer.getAmountBought() + quoteDTO.getAmount());
        buyer.setUpdatedAt(updatedDate);
        buyer.setLastQuoteAt(quoteDTO.getCreatedAt());
        buyerSaved = userRepository.save(buyer);
        if(testCase.equals(6)){
            throw new QuotesReconcileRollbackException(
                "Explicitly thrown by Reconcile Application to Roll Back!");
        }
        if(testCase.equals(7)){
            flipFlop();
        }
    }
}
```

清单 13-14 执行结算对账的报价对账服务 (ch13\ch13-01\XA-TX-Distributed\Settlement-ActiveMQ-Derby\src\main\java\com\acme\ecom\service\QuotesReconcileServiceImpl.java)

在对账过程中，您分别更新卖方和买方的 `amountSold` 和 `amountBought` 属性，因此这两个属性代表了如代码清单 13-14 所示的运行总计。为了讨论的完整性，清单 13-15 展示了 User 实体。

```java
@Entity
@Table(name = "stockuser")
@Data
@EqualsAndHashCode(exclude = { "id" })
public class User {
    @Id
    @Column(name = "id", nullable = false, updatable = false)
    private Long id;
    @Column(name = "name", nullable = false, updatable = false)
    private String name;
    @Column(name = "amountsold", nullable = true, updatable = true)
    private Double amountSold;
    @Column(name = "amountbought", nullable = true, updatable = true)
    private Double amountBought;
    @Column(name = "lastquoteat", nullable = true, updatable = true)
    @Temporal(TemporalType.TIMESTAMP)
    private Date lastQuoteAt;
    @Column(name = "createdat", nullable = false, updatable = false)
    @Temporal(TemporalType.TIMESTAMP)
    private Date createdAt;
    @Column(name = "updatedat", nullable = false, updatable = true)
    @Temporal(TemporalType.TIMESTAMP)
    private Date updatedAt;
}
```

清单 13-15 User 实体 (ch13\ch13-01\XA-TX-Distributed\Settlement-ActiveMQ-Derby\src\main\java\com\acme\ecom\model\user\User.java)

以上就是该微服务的主要 Java 文件。将上述类与 Atomikos XA 事务管理器连接的主要配置在文件 `spring-listener-derbyl.xml` 中完成，如清单 13-16 所示。

```xml
<bean id="dataSource"
    class="com.atomikos.jdbc.AtomikosDataSourceBean"
    init-method="init" destroy-method="close">
    <property name="uniqueResourceName" value="JDBC-2"/>
    <property name="xaDataSourceClassName"
        value="org.apache.derby.jdbc.EmbeddedXADataSource"/>
    <property name="xaProperties">
        <props>
            <prop key="databaseName">D:/Applns/apache/Derby/derbydb/exampledb</prop>
            <prop key="createDatabase">create</prop>
        </props>
    </property>
    <property name="poolSize" value="5"/>
    <property name="testQuery" value="VALUES 1"/>
</bean>

<bean id="jmsConnectionFactory"
    class="com.atomikos.jms.AtomikosConnectionFactoryBean"
    init-method="init" destroy-method="close">
    <property name="uniqueResourceName" value="localhost"/>
    <property name="xaConnectionFactoryClassName"
        value="org.apache.activemq.ActiveMQXAConnectionFactory"/>
    <property name="xaProperties">
        <props>
            <prop key="brokerURL">tcp://localhost:61616</prop>
        </props>
    </property>
    <property name="maxPoolSize" value="5"/>
</bean>

<bean id="transactionManager"
    class="org.springframework.transaction.jta.JtaTransactionManager">
    <property name="transactionManager">
        <bean class="com.atomikos.icatch.jta.UserTransactionManager"
            init-method="init" destroy-method="close">
            <property name="forceShutdown" value="true"/>
        </bean>
    </property>
    <property name="userTransaction">
        <bean class="com.atomikos.icatch.jta.UserTransactionImp"/>
    </property>
</bean>

<tx:annotation-driven transaction-manager="transactionManager"/>

<bean id="quotesReconcileServiceRequired_TX"
    class="com.acme.ecom.service.QuotesReconcileServiceImpl">
    <property name="userRepository" ref="userRepository"/>
    <aop:scoped-proxy/>
</bean>

<bean id="quotesReconcileServiceRequiresNew_TX"
    class="com.acme.ecom.service.QuotesReconcileServiceImpl">
    <property name="userRepository" ref="userRepository"/>
    <aop:scoped-proxy/>
</bean>

<tx:advice id="txAdviceRequired" transaction-manager="transactionManager">
    <tx:attributes>
        <tx:method name="reconcile" propagation="REQUIRED"
            rollback-for="QuotesReconcileRollbackException"
            no-rollback-for="QuotesNoRollbackException"/>
    </tx:attributes>
</tx:advice>

<tx:advice id="txAdviceRequiresNew" transaction-manager="transactionManager">
    <tx:attributes>
        <tx:method name="reconcile" propagation="REQUIRES_NEW"
            rollback-for="QuotesReconcileRollbackException"
            no-rollback-for="QuotesNoRollbackException"/>
    </tx:attributes>
</tx:advice>

<aop:config>
    <aop:pointcut id="reconcileOperation"
        expression="execution(* com.acme.ecom.service.QuotesReconcileService.reconcile(..))"/>
    <aop:advisor advice-ref="txAdviceRequired"
        pointcut-ref="reconcileOperation"/>
</aop:config>
```

清单 13-16 使用 Derby、ActiveMQ 和 Atomikos 的分布式事务示例的 Spring 配置 (ch13\ch13-01\XA-TX-Distributed\Settlement-ActiveMQ-Derby\src\main\resources\spring-listener-derbyl.xml)



除了 `DefaultMessageListenerContainer` 之外，我已经解释了上述所有配置。`DefaultMessageListenerContainer` 是一种消息监听器容器变体，它使用纯 JMS 客户端 API，特别是通过循环调用 `MessageConsumer.receive()` 来实现。当与 XA 事务管理器注册时，这种方式还允许对消息进行事务性接收。它被设计为既能在原生 JMS 环境中工作，也能在 Java EE 环境中工作。你首先需要设置标准的 JMS 消息监听器，即结算监听器，以便消息能够被 `SettlementListener.onMessage()` 拾取。

微服务 4：结算-Web

这个微服务同样很直接，它包含 REST 控制器。你也可以持续观察仪表盘，它会为你提供 `User` 表的视图，以便你能直观地看到测试效果。你还会使用这个微服务在系统中创建一些初始用户用于测试目的。图 13-7 展示了如何与结算 Web 微服务进行各种交互。

![../images/477630_1_En_13_Chapter/477630_1_En_13_Fig7_HTML.jpg](img/477630_1_En_13_Fig7_HTML.jpg)

图 13-7 结算 Web 控制台

有一个 junit `Test` 类，它简单地将 bean 定义加载到 `spring-sender-mysql.xml` 中，并将主测试应用程序置于休眠模式，以便调度器能够超时，并在预定义的时间间隔内检查是否有任何新的报价被接收。参见清单 13-17。

```
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations="classpath:spring-sender-mysql.xml")
public class BrokerServiceTest {
@Autowired
@Qualifier("brokerServiceRequired_TX")
BrokerService brokerService;
@Test
public void testSubmitQuote() throws Exception{
Thread.sleep(1000 ∗ 60 ∗ 60);
}
}
清单 13-17 报价处理器端的主测试类 (ch13\ch13-01\XA-TX-Distributed\Broker-MySQL-ActiveMQ\src\test\java\com\acme\ecom\test\BrokerServiceTest.java)
```

类似地，还有另一个 junit `Test` 类，它简单地将 bean 定义加载到 `spring-listener-derby.xml` 中，并将主测试应用程序置于休眠模式，以便 JMS 监听器能够持续监听 ActiveMQ 中的任何传入消息。参见清单 13-18。

```
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations="classpath:spring-listener-derby.xml")
public class SettlementListenerServiceTest {
@Test
public void testSettleQuote() throws Exception{
Thread.sleep(1000 ∗ 60 ∗ 60);
}
}
清单 13-18 报价结算端的另一个测试类 (ch13\ch13-01\XA-TX-Distributed\Settlement-ActiveMQ-Derby\src\test\java\com\acme\ecom\test\SettlementListenerServiceTest.java)
```

构建并测试示例的快乐路径
作为第一步，你需要启动 ActiveMQ 服务器。你可能需要参考附录 F 来开始使用 ActiveMQ。

你需要配置一个队列，它将作为上游和下游处理之间的桥梁。清单 13-19 给出了可以在 `activemq.xml` 中完成的队列配置。

```

清单 13-19 ActiveMQ 队列配置 (D:\Applns\apache\ActiveMQ\apache-activemq-5.13.3\conf\activemq.xml)
```

现在你可以启动 ActiveMQ 服务器：

```
cd D:\Applns\apache\ActiveMQ\apache-activemq-5.13.3\bin
D:\Applns\apache\ActiveMQ\apache-activemq-5.13.3\bin>activemq start
```

接下来，确保 MySQL 已启动并正在运行。你可能需要参考附录 H 来开始使用 MySQL。

```
D:\Applns\MySQL\mysql-5.7.14-winx64\bin>mysqld --console
```

现在打开一个 MySQL 提示符：

```
D:\Applns\MySQL\mysql-5.7.14-winx64\bin>mysql -u root –p
mysql> use ecom01;
Database changed
mysql>
```

为了从干净的表格开始，删除任何你示例中想要使用的名称的表格：

```
mysql> drop table quote;
```

接下来，使用此示例所需的模式创建表格：



```
mysql> create table quote (id BIGINT PRIMARY KEY AUTO_INCREMENT, symbol VARCHAR(4), sellerid BIGINT, buyerid BIGINT, amount FLOAT, status VARCHAR(9), test INTEGER, delay INTEGER, createdat DATETIME, updatedat DATETIME);
```

接下来，请确保 Derby 数据库已启动并以网络模式运行。
你可能需要参考附录 G 来开始使用 Derby。

```
D:\Applns\apache\Derby\db-derby-10.14.1.0-bin\bin>startNetworkServer
```

你也可以使用 Derby 的 `ij` 工具来创建数据库 `exampledb`，并通过嵌入式驱动使用以下命令打开一个到已创建数据库的连接：

```
D:\Applns\apache\Derby\derbydb>ij
ij> connect 'jdbc:derby://localhost:1527/D:/Applns/apache/Derby/derbydb/exampledb;create=false';
```

注意
你正在从一个与 Derby 安装目录不同的基础位置使用 `ij` 工具，假设你的数据库位于与 Derby 安装目录不同的位置，这是一种最佳实践。
关于如何创建新数据库的更多详细信息，请参考附录 G。

这里你同样将从清空表开始。删除任何你示例中想要使用的名称的表：

```
ij> drop table stockuser;
```

接下来，使用此示例所需的模式创建表：

```
ij> create table stockuser (id bigint not null, amountbought double, amountsold double, createdat timestamp not null, lastquoteat timestamp, name varchar(10) not null, updatedat timestamp not null, primary key (id));
ij> CREATE SEQUENCE hibernate_sequence START WITH 1 INCREMENT BY 1;
```

至此，构建和运行示例所需的基础设施就完成了。
接下来，需要构建并运行四个微服务。我们将逐一进行。

微服务 1：报价处理
微服务

请参阅清单 13-9 来调整 ActiveMQ 特定的配置，该配置位于：

```
ch13\ch13-01\XA-TX-Distributed\Broker-MySQL-ActiveMQ\src\main\resources\spring-sender-mysql.xml
```

此外，你还需要调整 MySQL 特定的配置：

```

jdbc:mysql://localhost:3306/ecom01

root
rootpassword

```

现在，构建并打包报价处理微服务的可执行文件，并启动定时处理器。在文件夹 `ch13\ch13-01\XA-TX-Distributed\Broker-MySQL-ActiveMQ\make.bat` 中提供了一个实用脚本，你可以轻松执行：

```
cd D:\binil\gold\pack03\ch13\ch13-01\XA-TX-Distributed\Broker-MySQL-ActiveMQ
D:\binil\gold\pack03\ch13\ch13-01\XA-TX-Distributed\Broker-MySQL-ActiveMQ>make
D:\binil\gold\pack03\ch13\ch13-01\XA-TX-Distributed\Broker-MySQL-ActiveMQ>mvn -Dmaven.test.skip=true clean install
```

现在，可以使用提供的脚本运行 JUnit 测试：

```
D:\binil\gold\pack03\ch13\ch13-01\XA-TX-Distributed\Broker-MySQL-ActiveMQ>run
D:\binil\gold\pack03\ch13\ch13-01\XA-TX-Distributed\Broker-MySQL-ActiveMQ>mvn -Dtest=BrokerServiceTest#testSubmitQuote test
```

微服务 2：经纪人 Web
微服务

首先，你需要更新配置文件以适应你的环境：

```
ch13\ch13-01\XA-TX-Distributed\Broker-Web\src\main\resources\application.properties
server.port=8080
spring.datasource.url = jdbc:mysql://localhost:3306/ecom01?autoReconnect=true&useUnicode=true&characterEncoding=UTF-8&allowMultiQueries=true&useSSL=false
spring.datasource.username = root
spring.datasource.password = rootpassword
spring.jpa.properties.hibernate.dialect = org.hibernate.dialect.MySQL5Dialect
spring.jpa.hibernate.ddl-auto = update
spring.freemarker.cache=false
```

现在，你可以构建并打包经纪人 Web 微服务的可执行文件，并启动服务器。在文件夹 `ch13\ch13-01\XA-TX-Distributed\Broker-Web\make.bat` 中提供了一个实用脚本：

```
cd D:\binil\gold\pack03\ch13\ch13-01\XA-TX-Distributed\Broker-Web
D:\binil\gold\pack03\ch13\ch13-01\XA-TX-Distributed\Broker-Web>make
D:\binil\gold\pack03\ch13\ch13-01\XA-TX-Distributed\Broker-Web>mvn -Dmaven.test.skip=true clean package
```

你可以通过多种方式运行 Spring Boot 应用程序。最直接的方式是通过以下命令执行 JAR 文件：

```
D:\binil\gold\pack03\ch13\ch13-01\XA-TX-Distributed\Broker-Web>run
D:\binil\gold\pack03\ch13\ch13-01\XA-TX-Distributed\Broker-Web>java -jar -Dserver.port=8080 .\target\quotes-web-1.0.0.jar
```

这将在 8080 端口启动经纪人 Web Spring Boot 服务器。
你可以使用浏览器（推荐 Chrome）并指向以下 URL，以持续监控所有新传入报价的处理情况：`http://localhost:8080/`。

微服务 3：报价结算
微服务

请参阅清单 13-16 来调整 ActiveMQ 特定的配置，该配置位于：

```
ch13\ch13-01\XA-TX-Distributed\Settlement-ActiveMQ-Derby\src\main\resources\spring-listener-derby.xml
```

此外，你还需要调整 Derby 特定的配置：

```

D:/Applns/apache/Derby/derbydb/exampledb
localhost

```

现在，构建并打包报价结算微服务的可执行文件，并启动消息监听器。在文件夹 `ch13\ch13-01\XA-TX-Distributed\Settlement-ActiveMQ-Derby\make.bat` 中提供了一个实用脚本：

```
cd D:\binil\gold\pack03\ch13\ch13-01\XA-TX-Distributed\Settlement-ActiveMQ-Derby
D:\binil\gold\pack03\ch13\ch13-01\XA-TX-Distributed\Settlement-ActiveMQ-Derby>make
D:\binil\gold\pack03\ch13\ch13-01\XA-TX-Distributed\Settlement-ActiveMQ-Derby>mvn -Dmaven.test.skip=true clean install
```

现在，可以使用提供的脚本运行 JUnit 测试：

```
D:\binil\gold\pack03\ch13\ch13-01\XA-TX-Distributed\Settlement-ActiveMQ-Derby>run
D:\binil\gold\pack03\ch13\ch13-01\XA-TX-Distributed\Settlement-ActiveMQ-Derby>mvn -Dtest=SettlementListenerServiceTest#testSettleQuote test
```

微服务 4：结算 Web
微服务

首先，你需要更新配置文件以适应你的环境：

```
ch13\ch13-01\XA-TX-Distributed\Settlement-Web\src\main\resources\application.properties
server.port=8081
spring.datasource.url=jdbc:derby://localhost:1527/D:/Applns/apache/Derby/derbydb/exampledb;create=false
spring.datasource.initialize=false
spring.datasource.driver-class-name=org.apache.derby.jdbc.ClientDriver
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.DerbyTenSevenDialect
spring.freemarker.cache=false
```

现在，构建并打包结算 Web 微服务的可执行文件，并启动服务器。在文件夹 `ch13\ch13-01\XA-TX-Distributed\Settlement-Web\make.bat` 中提供了一个实用脚本：

```
cd D:\binil\gold\pack03\ch13\ch13-01\XA-TX-Distributed\Settlement-Web
D:\binil\gold\pack03\ch13\ch13-01\XA-TX-Distributed\Settlement-Web>make
D:\binil\gold\pack03\ch13\ch13-01\XA-TX-Distributed\Settlement-Web>mvn -Dmaven.test.skip=true clean package
```

你可以通过多种方式运行 Spring Boot 应用程序。最直接的方式是通过以下命令执行 JAR 文件：

```
D:\binil\gold\pack03\ch13\ch13-01\XA-TX-Distributed\Settlement-Web>run
D:\binil\gold\pack03\ch13\ch13-01\XA-TX-Distributed\Settlement-Web>java -jar -Dserver.port=8081 .\target\user-web-1.0.0.jar
```

这将在 8081 端口启动结算 Web Spring Boot 服务器。
你可以使用浏览器（推荐 Chrome）并指向以下 URL，以持续监控用户的运行账户状态以及每次新传入报价结算时账户余额的变化情况：`http://localhost:8081/`。

总共有八个测试用例，你将逐一执行以验证该示例。请参见图 13-8。

![../images/477630_1_En_13_Chapter/477630_1_En_13_Fig8_HTML.jpg](img/477630_1_En_13_Fig8_HTML.jpg)

图 13-8
测试用例



严格来说，只有三个测试用例能够验证该示例，它们分别是第一个、第二个和第七个测试用例。所有其他测试用例要么通过模拟各种故障条件，要么通过调整应用于服务方法的事务语义，来使该示例失效。我这样做是为了让你理解分布式事务环境中可能出现的错误场景。在本节中，你将查看第一个测试用例，它演示了端到端流程中严格符合 ACID 的事务。这意味着资源中的所有状态变更要么全部被批准，要么全部被回滚。其余的测试用例将在后续章节中逐一执行。

作为第一步，你将在系统中创建两个测试用户。打开 Postman 并创建两个测试用户，如图所示（见图 13-9）：

![../images/477630_1_En_13_Chapter/477630_1_En_13_Fig9_HTML.jpg](img/477630_1_En_13_Fig9_HTML.jpg)

图 13-9
创建测试用户

```
http://localhost:8081/api/users
方法: POST; 请求体: 原始 JSON
{ "id" : 11, "name" : "Sam", "amountSold" : 1000.0, "amountBought" : 1000.0 }
{ "id" : 21, "name" : "Joe", "amountSold" : 5000.0, "amountBought" : 5000.0 }
```

测试用户创建完成后，它们将立即在结算控制台中可见，如图 13-10 所示。

![../images/477630_1_En_13_Chapter/477630_1_En_13_Fig10_HTML.jpg](img/477630_1_En_13_Fig10_HTML.jpg)

图 13-10
测试用户控制台

要启动第一个测试用例，请打开 Postman 并创建一个新的报价，如图 13-11 所示：

![../images/477630_1_En_13_Chapter/477630_1_En_13_Fig11_HTML.jpg](img/477630_1_En_13_Fig11_HTML.jpg)

图 13-11
创建新报价

```
http://localhost:8080/api/quotes
方法: POST; 请求体: 原始 JSON
{ "symbol" : "AAPL", "sellerId" : 11, "buyerId" : 21, "amount" : 100, "test" : 1, "delay" : 2 }
```

持续观察经纪人 Web 服务和结算 Web 服务的控制台。当创建新报价时，如图 13-11 所示，它会立即反映在经纪人 Web 控制台中，如图 13-12 所示。

![../images/477630_1_En_13_Chapter/477630_1_En_13_Fig12_HTML.jpg](img/477630_1_En_13_Fig12_HTML.jpg)

图 13-12
测试用例 1 的新报价已到达

几分钟内，报价将在上游微服务中处理，随后在下游微服务中结算。仔细观察可以发现，经纪人 Web 控制台中报价的状态从“新建”变为“已确认”，并且用户在结算 Web 控制台中的运行余额将根据报价金额发生变化，两者均显示在图 13-13 中。

![../images/477630_1_En_13_Chapter/477630_1_En_13_Fig13_HTML.jpg](img/477630_1_En_13_Fig13_HTML.jpg)

图 13-13
测试用例 1 控制台

你还应注意图 13-13 中的另一个方面，这将在后面的“测试消息乱序接收场景”部分的测试用例 8 中相关。每当相应用户账户更新时，上游系统中最新的报价创建时间会更新为用户的最后报价时间。

由于你已经在前面章节中详细查看了代码的每个细节，因此不再深入探讨底层发生了什么。相反，你现在将专注于事务语义。

![../images/477630_1_En_13_Chapter/477630_1_En_13_Fig14_HTML.jpg](img/477630_1_En_13_Fig14_HTML.jpg)

图 13-14
测试用例 1 的事务语义

如图 13-14 所示，你将 `TX_REQUIRED` 事务语义应用于所有服务方法。这意味着资源中的所有状态变更要么全部执行，要么全部回滚。在当前测试用例中，你没有模拟任何错误或故障条件，因此所有事务都成功了。
在后续的测试用例中，你将模拟错误条件，以便更好地理解企业场景中可能的故障情况。透彻理解所有这些可能的故障情况将使你获得更深入的洞察，这在设计“真正的微服务”时非常必要，因为你希望远离分布式事务！

测试事务回滚场景

要测试事务回滚测试用例，请再次打开 Postman 并创建另一个新报价，如图 13-11 所示：

```
http://localhost:8080/api/quotes
方法: POST; 请求体: 原始 JSON
{ "symbol" : "AMZN", "sellerId" : 11, "buyerId" : 21, "amount" : 200, "test" : 2, "delay" : 2 }
```

在图 13-3 中，上游处理和下游处理是分开的。这两个域之间没有严格的“超级事务协调器”。如果将事务语义应用于图 13-3，你会得到图 13-15。因此，在一个域内（仅上游或仅下游），你可以决定跨多个资源进行协调事务，而当涉及跨这些域的协调时，你可能需要考虑 BASE 方法。

![../images/477630_1_En_13_Chapter/477630_1_En_13_Fig15_HTML.jpg](img/477630_1_En_13_Fig15_HTML.jpg)

图 13-15
测试用例 2 的事务语义

如图 13-15 所示，`TX_REQUIRED` 事务语义已应用于所有服务方法。这意味着资源中的所有状态变更要么全部执行，要么全部回滚。
在第一次执行过程中，当报价处理器任务拾取新报价进行处理时，你在经纪人服务中模拟了一个错误。这在图 13-15 中用 X 标记表示。因此，即使拍卖服务和股票订单服务中的相应事务已准备好提交（如 ✓ 标记所示），它们也会被回滚，因为两者都是在模拟错误的经纪人服务的父事务上下文中调用的。这在图 13-16 的报价处理微服务控制台中显示。

此外，由于在第一次执行中事务被回滚，新报价消息向 ActiveMQ 的传递未被提交，因此报价结算微服务中不会发生任何操作（见图 13-16）。为简洁起见，这在图 13-15 的第一次执行中用空白圆圈表示。

![../images/477630_1_En_13_Chapter/477630_1_En_13_Fig16_HTML.jpg](img/477630_1_En_13_Fig16_HTML.jpg)

图 13-16
测试用例 2 第一次执行处理

等待报价处理器任务在下一个计划触发器中再次拾取之前的报价（因为状态仍为“新建”），如图 13-17 所示。

![../images/477630_1_En_13_Chapter/477630_1_En_13_Fig17_HTML.jpg](img/477630_1_En_13_Fig17_HTML.jpg)

图 13-17
测试用例 2 的新报价已到达



在执行报价处理器任务的第二轮时，它再次拾取对应于测试用例 2 的新报价进行处理。在第二轮中，你不模拟任何错误条件，如图 13-15 中的✓符号所示。因此，拍卖服务和股票订单服务中准备提交的事务（如✓符号所示）被提交，因为这两个服务都是在经纪人服务的父事务上下文中被调用的，而该父事务也被提交。由于向 ActiveMQ 发送的新报价消息被提交，报价结算微服务将收到用于结算的报价消息。如图 13-15 中第二轮结算监听服务和记录用户事务服务的✓符号所示，报价结算微服务内的完整事务将被提交。这反映在图 13-18 的控制台中。

![../images/477630_1_En_13_Chapter/477630_1_En_13_Fig18_HTML.jpg](img/477630_1_En_13_Fig18_HTML.jpg)

图 13-18
测试用例 2 控制台

**注意**
作为读者，如果你严格按照本章提到的测试执行步骤操作，你将很容易根据此处提供的解释和截图直观地看到效果并理解。因此，强烈建议你完全按照指示操作。此外，这种设置会自动使部分测试用例的第一轮失败，第二轮成功。这种机制的设计是为了在测试执行期间最大限度地减少读者的干预。然而，测试夹具仅在你使用单个测试客户端（Postman 浏览器客户端）执行测试用例时才能正常工作。因此，请不要使用并发测试客户端对示例进行（猴子）测试。待你熟悉了按描述执行测试并能理解所提供的解释后，你就可以开始调整代码和测试夹具，以测试你自己设想的更多场景。

**模拟宽松的 BASE 异常**
到目前为止，你实现了一个具有合理程度 ACID 事务的 BASE 系统，从而确保最终一致性得以保持。现在，让我们滥用事务属性，人为地进一步“削减”ACID 事务的范围，这将使你进入以下宽松的 BASE 情况。

**测试消息丢失场景**

要测试消息丢失测试用例，请再次打开 Postman 并创建另一个新报价：

```
http://localhost:8080/api/quotes
方法: POST; 请求体: 原始 JSON
{ "symbol" : "GOOG", "sellerId" : 11, "buyerId" : 21, "amount" : 300, "test" : 3, "delay" : 2 }
```

图 13-19 显示了测试用例 3 的新报价。其状态为“新建”。结算后，报价金额 300 应被添加到图中所示的买方和卖方各自的运行余额中。

![../images/477630_1_En_13_Chapter/477630_1_En_13_Fig19_HTML.jpg](img/477630_1_En_13_Fig19_HTML.jpg)

图 13-19
测试用例 3 初始控制台

现在让我们看看事务语义。见图 13-20。

![../images/477630_1_En_13_Chapter/477630_1_En_13_Fig20_HTML.jpg](img/477630_1_En_13_Fig20_HTML.jpg)

图 13-20
测试用例 3 事务语义

这里，报价处理器任务拾取新报价进行处理。你在股票订单服务中模拟一个错误。这在图 13-19 中用 X 符号标记。拍卖服务中的对等事务必须成功，因此用✓符号标记。经纪人服务中的父事务也需要成功。这意味着股票订单服务中的错误不应影响经纪人服务中的父事务或拍卖服务中的对等事务，因此你为股票订单服务设置了`TX_REQUIRES_NEW`语义。最终结果是该报价将被标记为“已确认”；然而，向 ActiveMQ 发送的新报价消息将失败。由于报价已被标记为“已确认”，报价处理器任务在下一个计划触发时不会再次拾取此报价进行处理。最终结果是此报价将永远不会在下游的报价结算微服务中得到结算，如图 13-21 所示！消息丢失了。

![../images/477630_1_En_13_Chapter/477630_1_En_13_Fig21_HTML.jpg](img/477630_1_En_13_Fig21_HTML.jpg)

图 13-21
测试用例 3 完成时控制台

**测试重复消息发送场景**

要验证重复消息发送测试用例，请再次打开 Postman 并创建另一个新报价：

```
http://localhost:8080/api/quotes
方法: POST; 请求体: 原始 JSON
{ "symbol" : "NFLX", "sellerId" : 11, "buyerId" : 21, "amount" : 400, "test" : 4, "delay" : 2 }
```

图 13-22 显示了测试用例 4 的新报价。其状态为“新建”。结算后，报价金额 400 应被添加到图中所示的买方和卖方各自的运行余额中。

![../images/477630_1_En_13_Chapter/477630_1_En_13_Fig22_HTML.jpg](img/477630_1_En_13_Fig22_HTML.jpg)

图 13-22
测试用例 4 初始控制台

现在让我们看看图 13-23 中的事务语义。

![../images/477630_1_En_13_Chapter/477630_1_En_13_Fig23_HTML.jpg](img/477630_1_En_13_Fig23_HTML.jpg)

图 13-23
测试用例 4 事务语义

这里，报价处理器任务拾取新报价进行处理。你在拍卖服务中模拟一个错误。这在图 13-23 中用 X 符号标记。股票订单服务中的对等事务必须成功，因此用✓符号标记。经纪人服务中的父事务也需要成功。这意味着拍卖服务中的错误不应影响经纪人服务中的父事务或股票订单服务中的对等事务，因此你为拍卖服务设置了`TX_REQUIRES_NEW`语义。最终结果是标记为“已确认”的报价状态将被回滚；然而，向 ActiveMQ 发送的新报价消息将成功。

由于向 ActiveMQ 发送的新报价消息被提交，报价结算微服务将收到用于结算的报价消息。如图 13-23 中第一轮结算监听服务和记录用户事务服务的✓符号所示，报价结算微服务内的完整事务将被提交。这反映在图 13-24 的控制台中。

![../images/477630_1_En_13_Chapter/477630_1_En_13_Fig24_HTML.jpg](img/477630_1_En_13_Fig24_HTML.jpg)

图 13-24
测试用例 4 控制台，中间视图



令人震惊的是，你可以看到报价结算已经发生，并且 400 的报价金额被分别添加到买方和卖方的相应运行余额中，如图 13-24 所示；然而，在上游系统中，该报价尚未被标记为“已确认”。这是因为将报价标记为“已确认”的拍卖服务发生了回滚！

其结果是，下一次报价处理器任务会再次拾取该新报价进行处理。在你的测试执行中，这次你不会模拟拍卖服务中的错误。因此，该报价的端到端处理会第二次重复执行，图 13-25 反映了这一结果！

![../images/477630_1_En_13_Chapter/477630_1_En_13_Fig25_HTML.jpg](img/477630_1_En_13_Fig25_HTML.jpg)

图 13-25
测试用例 4 完成时的控制台

这并非预期结果。重复的消息导致了报价的重复结算！

测试重复消息消费场景

要测试重复消息消费测试用例，请再次使用 Postman 并创建另一个新报价：

```
http://localhost:8080/api/quotes
METHOD: POST; BODY: Raw JSON
{ "symbol" : "TSLA", "sellerId" : 11, "buyerId" : 21, "amount" : 500, "test" : 5, "delay" : 2 }
```

图 13-26 显示了测试用例 5 的新报价。其状态为“新建”。结算时，报价金额 500 应被添加到买方和卖方的相应运行余额中，如图所示。

![../images/477630_1_En_13_Chapter/477630_1_En_13_Fig26_HTML.jpg](img/477630_1_En_13_Fig26_HTML.jpg)

图 13-26
测试用例 5 控制台，初始状态

现在让我们看一下图 13-27 中的事务语义。

![../images/477630_1_En_13_Chapter/477630_1_En_13_Fig27_HTML.jpg](img/477630_1_En_13_Fig27_HTML.jpg)

图 13-27
测试用例 5 的事务语义

这里，在任何上游处理中都没有模拟错误，因此报价状态将从“新建”变为“已确认”，并且由于新报价消息成功投递到 ActiveMQ，报价结算微服务将收到该报价消息进行结算。结算监听服务将接收该消息，随后调用报价对账服务进行结算。结算过程中，买方和卖方的运行余额会被更新。然而，一旦控制权从报价对账服务返回，就会模拟一个错误，如图 13-27 中结算监听服务第一次处理时的 X 标记所示。

这里需要注意几点：

*   报价对账服务执行的结算是在`TX_REQUIRES_NEW`事务上下文中完成的，因此发生在外部或父事务（结算监听服务的事务）中的错误不会影响报价对账服务的事务，所以结算的效果被提交了。

*   尽管结算监听服务已经从 ActiveMQ 读取了消息，但处于`TX_REQUIRED`事务上下文中的消息监听方法不会提交从 ActiveMQ 读取的消息。因此，对于 ActiveMQ 来说，消息投递并未成功，所以它会尝试再次投递该消息。

在消息投递重试期间（即消息监听器第二次处理期间），你不会模拟任何错误，这由图 13-27 中两个服务的标记（✓）表示。因此，结算监听服务和报价对账服务的事务都将成功并提交。然而，存在一个更大的问题：报价将再次被结算，这将导致买方账户和卖方账户分别被重复增加和重复扣除。见图 13-28。

![../images/477630_1_En_13_Chapter/477630_1_En_13_Fig28_HTML.jpg](img/477630_1_En_13_Fig28_HTML.jpg)

图 13-28
测试用例 5 的事务语义

测试消息已消费、处理失败场景

要测试消息已消费、处理失败测试用例，请再次使用 Postman 并创建另一个新报价：

```
http://localhost:8080/api/quotes
METHOD: POST; BODY: Raw JSON
{ "symbol" : "MSFT", "sellerId" : 11, "buyerId" : 21, "amount" : 600, "test" : 6, "delay" : 2 }
```

图 13-29 显示了测试用例 6 的新报价。其状态为“新建”。结算时，报价金额 600 应被添加到买方和卖方的相应运行余额中，如图所示。

![../images/477630_1_En_13_Chapter/477630_1_En_13_Fig29_HTML.jpg](img/477630_1_En_13_Fig29_HTML.jpg)

图 13-29
测试用例 6 控制台，初始状态

现在让我们看一下图 13-30 所示的事务语义。

![../images/477630_1_En_13_Chapter/477630_1_En_13_Fig30_HTML.jpg](img/477630_1_En_13_Fig30_HTML.jpg)

图 13-30
测试用例 6 的事务语义

这里同样，在任何上游处理中都没有模拟错误，因此报价状态将从“新建”变为“已确认”，并且由于新报价消息成功投递到 ActiveMQ，报价结算微服务将收到该报价消息进行结算。结算监听服务将接收该消息，随后调用报价对账服务进行结算。结算过程中，它会尝试更新买方和卖方的运行余额。然而，在报价对账服务事务内部模拟了一个错误，如图 13-30 中的 X 标记所示。由于报价对账服务事务配置了`TX_REQUIRES_NEW`语义，结算将被回滚。由于消息消费成功，最终结果是该报价在下游系统中永远保持未结算状态！见图 13-31。

![../images/477630_1_En_13_Chapter/477630_1_En_13_Fig31_HTML.jpg](img/477630_1_En_13_Fig31_HTML.jpg)

图 13-31
测试用例 6 控制台，完成状态

测试消息重新投递场景
JMS 不保证消息投递的顺序，因此先发送的消息可能在后发送的消息之后到达。无论你是否使用 XA 事务，这一点都成立。

要测试消息重新投递测试用例，请再次使用 Postman 并创建另一个新报价：

```
http://localhost:8080/api/quotes
METHOD: POST; BODY: Raw JSON
{ "symbol" : "ORCL", "sellerId" : 11, "buyerId" : 21, "amount" : 700, "test" : 7, "delay" : 2 }
```

图 13-32 显示了测试用例 7 的新报价。其状态为“新建”。结算时，报价金额 700 应被添加到买方和卖方的相应运行余额中，如图所示。

![../images/477630_1_En_13_Chapter/477630_1_En_13_Fig32_HTML.jpg](img/477630_1_En_13_Fig32_HTML.jpg)

图 13-32
测试用例 7 控制台，初始状态

现在让我们看一下图 13-33 所示的事务语义。



![../images/477630_1_En_13_Chapter/477630_1_En_13_Fig33_HTML.jpg](img/477630_1_En_13_Fig33_HTML.jpg)

**图 13-33**
测试用例 7 事务语义

在此用例中，任何上游处理均未模拟错误，因此报价状态将从“新建”变为“已确认”。并且由于新报价消息成功投递至 ActiveMQ，报价结算微服务将接收该报价消息进行结算。结算监听服务将接收消息，随后调用报价对账服务进行结算。结算过程中，会尝试更新买方和卖方的运行余额。然而，在第一次处理中，报价对账服务事务内模拟了一个错误，如图 13-33 中 X 标记所示。此外，当控制权从报价对账服务返回后，结算监听服务内又模拟了另一个错误，如图 13-33 中结算监听服务第一次处理时的 X 标记所示。

尽管结算监听服务已从 ActiveMQ 读取消息，但处于 TX_REQUIRED 事务上下文中的消息监听方法不会提交从 ActiveMQ 读取的消息。因此，对于 ActiveMQ 而言，消息投递未成功，它将尝试重新投递消息。在重试期间（即消息监听器第二次处理时），不模拟任何错误，两个服务中均以 ✓ 标记表示。因此，结算监听服务和报价对账服务的事务都将成功并提交。其效果是，报价将在第二次处理时正确结算。见图 13-34。

![../images/477630_1_En_13_Chapter/477630_1_En_13_Fig34_HTML.jpg](img/477630_1_En_13_Fig34_HTML.jpg)

**图 13-34**
测试用例 7 控制台，完成

**常见消息传递陷阱**
在本节中，我将演示一些常见的消息传递陷阱，这些陷阱在分布式场景中，如果系统设计不周，就可能发生。

**测试消息乱序到达场景**
测试此场景稍显棘手，因为您需要根据测试装置协调每个操作的时间。我将根据我的测试装置配置进行说明，如果您未对示例配置进行任何更改，这些配置同样适用于您。
您需要发送一个新的报价进行上游处理，但希望稍微延迟处理。当此报价等待延迟结束时，您还需要再触发一个新的报价进行上游处理，该报价将几乎无延迟地直接处理完毕。结果，后创建的报价必须先到达下游系统进行进一步处理，而最初创建的报价随后到达。通过这种方式，您需要模拟消息在下游系统乱序到达的情况。
在开始为此测试用例发送请求之前，请完整阅读本节剩余部分一次，以理解您需要在心理上做的准备，以便按照指示发送新报价。然后，再次回到此处的解释点，重新阅读，并继续实际测试。

图 13-35 显示了此测试用例的事务语义。

![../images/477630_1_En_13_Chapter/477630_1_En_13_Fig35_HTML.jpg](img/477630_1_En_13_Fig35_HTML.jpg)

**图 13-35**
测试用例 8 事务语义

如图 13-35 所示，您不模拟任何错误场景；而是仅模拟消息乱序到达的情况。

要测试此测试用例，再次打开 Postman 并触发一个新的报价：

```
http://localhost:8080/api/quotes
方法: POST; 请求体: 原始 JSON
{ "symbol" : "QCOM", "sellerId" : 11, "buyerId" : 21, "amount" : 800, "test" : 8, "delay" : 90}
```

您应该注意，您在测试请求负载中附带了一个 90 秒的延迟，如图 13-36 所示。

![../images/477630_1_En_13_Chapter/477630_1_En_13_Fig36_HTML.jpg](img/477630_1_En_13_Fig36_HTML.jpg)

**图 13-36**
测试用例 8 控制台，创建第一个报价

持续观察上游微服务控制台（命令屏幕）。当报价处理器任务超时时，它将获取这个新报价，将状态从“新建”更改为“已确认”，但在完成端到端事务之前，它将休眠您提供的 90 秒延迟；如图 13-37 所示。

![../images/477630_1_En_13_Chapter/477630_1_En_13_Fig37_HTML.jpg](img/477630_1_En_13_Fig37_HTML.jpg)

**图 13-37**
测试用例 8 控制台，显示延迟

这 90 秒很重要，因为报价处理器任务的下一次超时将在接下来的 60 秒内发生，并且在该超时期间，它不应获取此报价，因为它已处于处理状态（报价状态不是“新建”）。

诀窍来了。在报价处理器任务获取报价进行处理并进入您提供的 90 秒延迟休眠后（10 到 20 秒内），您需要触发另一个新报价（理想情况下，您有另一个 Postman 会话并已准备好数据），如下所示：

```
http://localhost:8080/api/quotes
方法: POST; 请求体: 原始 JSON
{ "symbol" : "GILD", "sellerId" : 11, "buyerId" : 21, "amount" : 900, "test" : 8, "delay" : 2 }
```

这第二个新报价如图 13-38 所示。仅此而已，您这边的测试执行步骤就结束了。

![../images/477630_1_En_13_Chapter/477630_1_En_13_Fig38_HTML.jpg](img/477630_1_En_13_Fig38_HTML.jpg)

**图 13-38**
测试用例 8 控制台，创建第二个报价

当报价处理器任务下次超时时，它将仅获取这第二个新报价。这是因为，即使第一个报价仍在上游系统中处理，该报价的状态已更新为“已确认”，如图 13-38 所示。这通过将拍卖服务的事务标记为 TX_REQUIRES_NEW 来体现，如图 13-35 所示（另一个用于演示目的的技巧）。

对于第二个新报价，处理延迟可以忽略不计，它完成上游处理后，通过 ActiveMQ，到达下游系统进行进一步处理。您期望第一个报价仍在上游系统的处理阶段。这样，消息在下游系统中就乱序了。这将如图 13-39 所示进行结算。

![../images/477630_1_En_13_Chapter/477630_1_En_13_Fig39_HTML.jpg](img/477630_1_En_13_Fig39_HTML.jpg)

**图 13-39**
测试用例 8 控制台，第二个报价先结算

持续观察浏览器控制台。很快，您将看到下游系统中用户的运行余额发生变化。这是第一个报价的效果，其上游处理被延迟，因此它乱序到达下游系统并最终完成结算，如图 13-40 所示。

![../images/477630_1_En_13_Chapter/477630_1_En_13_Fig40_HTML.jpg](img/477630_1_En_13_Fig40_HTML.jpg)

**图 13-40**
测试用例 8 控制台，第一个报价第二个结算



在之前的“构建并测试示例的快乐路径”一节中，你了解到，每当用户账户更新时，上游系统中最新的报价创建时间会作为最后报价时间被更新。按照这个逻辑，图 13-40 中的报价 ID 9 是最新创建的报价，因此该报价的创建时间戳应该是该用户的最后报价时间。然而，由于报价 ID 8 和报价 ID 9 是无序到达下游系统的，这条数据属性规则就出错了！

总结
事务是任何企业级应用动态运行的基石。本地事务是好的；然而，跨分区域的分布式事务则并非善类。当你从基于单体架构转向基于微服务的架构时，你仍然需要事务；但是，对于跨分区的域，你最好使用 BASE 事务来代替 ACID 事务，并在分区或域内保持 ACID 或 XA 兼容的事务。在本章中，你看到了分布式企业系统中可能出现的许多错误场景，并且你也看到了如果使用 ACID 事务，这些错误场景本可以如何避免。当你从单体架构转向微服务架构时，上述错误场景并不会减少；相反，由于微服务架构的分布程度成倍增加，此类错误场景的数量会大幅增加。在下一章中，你将看到在架构分布式系统时可以用来防范此类错误场景的技术，所以请继续愉快地阅读吧！

脚注

14. 事务与微服务

第 13 章广泛地涵盖了分布式事务。你现在已经清楚地理解了分布式事务，以便能明智地使用它们。需要明确的是，在微服务世界中，你应尽可能避免跨分区和跨域使用分布式事务。我并不是说在微服务架构中应该完全弃用分布式事务，因为有些领域你需要通过牺牲松耦合来换取可靠性，从而使用它们；然而，其精髓在于，当涉及跨分区和跨域时，你可以在不实际使用分布式事务的情况下完成大部分设计。你将在本章中详细了解如何做到这一点^(³³)。本章中的示例是第 13 章示例的延续，因此你可能需要在尝试运行本章示例之前先阅读并理解第 13 章。

你将在本章中探讨以下几个方面：

*   跨微服务分布数据的影响
*   区分全局事务和本地事务
*   增强第 13 章的示例，使其具有容错能力
*   探索可在实际微服务工作中使用的进一步设计重构

分区与微服务
最大化一致性的最佳方法是避免分区。没有分区意味着你有一个单一的软件进程处理所有处理工作。时间已经证明，软件进程存在局限性，并且垂直扩展在达到一定限度后无法线性增加，更不用说指数级增加了；这一点在第 11 章的“扩展立方体”一节中讨论过。正是在这种背景下，人们使用水平扩展方法来提高可扩展性。一旦你将处理逻辑拆分到多个软件进程中，你就引入了分区的可能性，并且分区越多，你能实现的一致性就越低。微服务也不例外。当你将单体应用拆分为基于微服务的架构时，你增加了节点或软件进程的数量，你的处理逻辑被拆分到这些节点或进程上，并且这些处理逻辑的协调必须通过网络来完成。

网络或进程边界带来了所有相关的复杂性和不确定性，你再也无法确定端到端的软件处理将如何以及何时完成。正是在这种背景下，你一直在利用两阶段提交协议来减少不确定性。

作为两阶段提交协议的一部分执行请求的软件进程需要能够相互通信，以便在事务提交时协调它们的操作。在两阶段提交协议的第一阶段，协调器（通常是发起事务的进程）会询问所有参与者是否准备好提交；在第二阶段，协调器指示它们提交（或中止）事务。如果某个参与者资源管理器能够提交其负责的那部分事务，那么一旦它已将所做的更改（对资源）及其状态记录到永久存储中，它就会同意，因此也就准备好提交了。因此，两阶段提交协议包括一个投票阶段和一个完成阶段。所有这些额外的步骤都增加了主要的处理指令，因此两阶段提交协议相对昂贵，因为它们完成所需的时间比本地事务更长。

即使是两阶段提交协议也并非完全没有错误。在某些情况下，在两阶段提交协议即将结束时，在所有参与者都同意提交之后，某些资源可能出现故障，导致提交无法完成。然而，该协议在设计上可以容忍此类故障（服务器崩溃、网络故障或消息丢失），并且保证最终能够完成，无论是否需要人工干预，尽管无法指定完成的时间限制。

微服务与分布式数据

典型的单体应用将所有或大部分数据集中存放在单个数据库中，这为应用在一致性方面提供了简单性和许多控制选项。例如，查看第 13 章中引入的两个数据表，如下面的图 14-1 所示。

![../images/477630_1_En_14_Chapter/477630_1_En_14_Fig1_HTML.jpg](img/477630_1_En_14_Fig1_HTML.jpg)

图 14-1
用户余额表

图 14-1 展示了`User Balance`表，它是用户运行余额的聚合视图。图 14-2 展示了`Quotes`表，它维护着交易历史记录。

![../images/477630_1_En_14_Chapter/477630_1_En_14_Fig2_HTML.jpg](img/477630_1_En_14_Fig2_HTML.jpg)

图 14-2
报价表



你知道，当创建新报价时，其状态为“新建”。当报价被处理并结算后，其状态会更新为“已确认”，同时报价的价值会累加到`User Balance`表中买方和卖方的运行余额中。这两个操作必须是原子性的，如果系统是单体架构，你可以在本地事务范围内实现操作的原子性。参见清单 14-1。

```
开始本地事务
更新报价状态
增加或减少用户余额
结束本地事务
清单 14-1
单体应用中的事务
```

当你按照微服务设计原则将处理过程拆分为多个微服务时，每个微服务将管理自己的数据。这意味着，报价处理微服务将拥有并管理`Quotes`表，而报价结算微服务将拥有并管理`User Balance`表。如果使用两阶段提交的全局事务，操作将如清单 14-2 所示。

```
开始全局事务
更新报价状态
增加或减少用户余额
结束全局事务
清单 14-2
微服务中的全局事务
```

如果你不对清单 14-2 中描述的操作使用两阶段提交事务，那么这两个操作将必须由两个微服务分别完成，这意味着这两个操作之间的原子性必须放宽。参见清单 14-3。

```
开始本地事务
更新报价状态
结束本地事务
开始本地事务
增加或减少用户余额
结束本地事务
清单 14-3
微服务中的本地事务
```

存在一种可能性：当报价状态从“新建”更改为“已确认”后，紧接着发生错误，导致`User Balance`表中对买方和卖方的报价价值增减操作未能执行，从而错过了报价结算！参见清单 14-4。

```
开始本地事务
更新报价状态
结束本地事务
开始本地事务
增加或减少用户余额
结束本地事务 !错误
清单 14-4
微服务本地事务错误场景
```

这种状态是否可被应用程序接受，取决于上述`User Balance`表中的操作被遗漏所产生的影响。即使`User Balance`表中的数据更新被遗漏，相同的信息已经在`Quotes`表中以不同形式进行了追踪。在这个例子中，`User Balance`表中的数据应被视为一种汇总视图，它是对每个用户聚合总额的一种缓存。它的存在是为了更快地查询系统中每个用户的交易余额。如果应用程序认为这些用户余额仅仅是估算值，其正确值始终可以通过`Quotes`表中的数据进行验证，那么前面描述的错误——遗漏了在`User Balance`表中对买方和卖方增加报价价值——是可以接受的。在这种情况下，`User Balance`表中的数据并不要求 100%准确。
第 13 章中的“测试消息丢失场景”部分模拟了类似的错误场景：报价状态从“新建”更改为“已确认”成功，但由于上游微服务发送给下游微服务的消息未能成功送达，导致下游结算被遗漏。该章引入了消息中间件作为两个微服务之间的桥梁，使微服务之间实现松耦合。然而，当你这样解耦微服务时，又引入了另一个难题：管理一个微服务与消息中间件之间的一致性。当你的应用程序中有许多微服务时，这类场景很常见。你将在后面的“全局资源与本地资源”部分探讨消息中间件所涉及的复杂性；不过，我们暂时先搁置队列的问题。

幂等操作与微服务
处理清单 14-4 中错误场景的一个简单方法是反转事务的顺序。参见清单 14-5。

注意
这种事务顺序的反转在此特定场景下有效；对于你的情况，可能需要采用不同的方法。

```
开始本地事务
增加或减少用户余额
结束本地事务
开始本地事务
更新报价状态
结束本地事务
清单 14-5
反转后的微服务本地事务场景
```

通过如上所述反转事务的顺序，你可以确保只有在用户余额更新成功时，才尝试更改报价状态。通过这种方式，你可以确信：如果报价处理通过将报价状态从“新建”更改为“已确认”而完成，那么报价肯定已经通过在`User Balance`表中进行相应更改而完成了结算。

这时出现了一个新场景。如果在报价已在`User Balance`表中结算后，报价处理失败怎么办？此时用户余额已更新，但报价仍保持“新建”状态，如清单 14-6 所示。

```
开始本地事务
增加或减少用户余额
结束本地事务
开始本地事务
更新报价状态
结束本地事务 !错误
清单 14-6
反转后的微服务本地事务错误场景
```



如果是这样，当报价处理器任务（一个定时调度器）下次超时时，由于报价状态仍为“New”，它将再次尝试处理同一报价。这会导致用户余额中出现重复交易。第 13 章中的“测试重复消息发送场景”和“测试重复消息消费场景”小节模拟了此类错误场景。在第一种情况下，报价状态从“New”更改为“Confirmed”在第一次处理时未成功，因此向下游结算微服务发送了重复消息。在第二种情况下，模拟了重复消息消费场景，导致用户余额中出现重复交易。

只要交易是幂等的，重复交易的影响就可以被消除。幂等操作是指可以重复应用而不会产生任何副作用的操作。加法和减法不是幂等的，这从其操作本身的特性即可看出。在单线程环境中，仅设置值的更新操作是幂等的；然而，在存在并发的多线程环境中，即使是此类更新操作也不是幂等的。这是因为作为此类更新操作前提条件的上下文可能已被另一个并发线程更改。或者，此类更新的应用顺序可能与期望的顺序不符。你需要有处理这些情况的机制，本章后面的增强示例部分将对此进行探讨。总的来说，幂等消费者的实现可能很棘手，如参考文献^(³⁴)所示。

全局资源与本地资源
第 13 章讨论了 ACID 事务，图[13-2](https://doi.org/10.1007/978-1-4842-4501-9_13Fig#2)展示了一个包含两个独立资源的典型场景：一个数据库资源和一个消息中间件资源。消息中间件资源的队列是存储移动中实体状态（临时存储在节点或进程中）的一等公民。当你将单体报价应用拆分为两个微服务（报价处理微服务和报价结算微服务）时，它们之间需要一种通信机制。如果你倾向于为此通信采用响应式或基于事件的方法，而不是同步风格的 REST 调用，那么消息中间件资源是不错的选择，这正是图[13-2](https://doi.org/10.1007/978-1-4842-4501-9_13Fig#2)所展示的。然而，该图描绘了一个完全分布式的架构，其中所有资源管理器甚至事务管理器都全局分布在网络中，因此，如果你必须应用 ACID 风格的两阶段提交操作，则需要完整的全局事务。

如果队列需要具备容错能力，则它们必须是持久化的。一个队列要实现持久化，需要有合适的持久化机制作为支撑，通常由数据库本身提供。因此，如果我们重新绘制图[13-2](https://doi.org/10.1007/978-1-4842-4501-9_13Fig#2)来表示参与全局事务的持久化队列，它将如图 14-3 所示。

![../images/477630_1_En_14_Chapter/477630_1_En_14_Fig3_HTML.jpg](img/477630_1_En_14_Fig3_HTML.jpg)

图 14-3
使用分布式资源的分布式 ACID 事务

图 14-3 保留了图[13-2](https://doi.org/10.1007/978-1-4842-4501-9_13Fig#2)中的资源管理器 1 和资源管理器 2，但它为 JMS 资源（资源管理器 2）展示了一个由资源管理器 3 表示的后备存储。然后，你可以将微服务架构表示为类似于图 14-4 所示。

![../images/477630_1_En_14_Chapter/477630_1_En_14_Fig4_HTML.jpg](img/477630_1_En_14_Fig4_HTML.jpg)

图 14-4
报价微服务架构

同样典型的是，中间件供应商会同时提供数据库解决方案和消息中间件解决方案。如果是这样，你可以对图 14-3 所示的架构进行一项优化，如图 14-5 所示。

![../images/477630_1_En_14_Chapter/477630_1_En_14_Fig5_HTML.jpg](img/477630_1_En_14_Fig5_HTML.jpg)

图 14-5
部分优化的分布式 ACID 事务

许多主要的中间件厂商（包括但不限于微软、IBM 和 Oracle）都在同一技术栈内提供解决方案。然而，仍然存在一些问题。只有将微服务中的操作和消息队列纳入单个事务中，才能解决前面小节讨论的问题。如果这样做，由于队列的后备持久化数据库和微服务使用的数据库是不同的资源，因此需要全局两阶段事务。

你将进一步优化图 14-5 中的架构以解决上述问题；见图 14-6。

![../images/477630_1_En_14_Chapter/477630_1_En_14_Fig6_HTML.jpg](img/477630_1_En_14_Fig6_HTML.jpg)

图 14-6
优化为本地事务的分布式事务

该技术是确保后备持久化与数据库位于同一资源上，如图 14-6 所示。请注意，图 14-6 中事务的标签已从 XA-JTA 更改为 TX-Local。此外，当避免了分区时，你可以最小化甚至避免在网络层面发生的通信。这一点也在图 14-6 中展示。与图 14-3 或图 14-5 相比，事务管理器、资源管理器和后备资源都位于同一分区或节点中。如果它们都来自同一供应商，那么就有通过紧密集成选项进行优化的可能性，例如绕过已发布的 API，通过已发布甚至未发布的 SPI（服务提供者接口）进行优化调用。

现在，让我们看看在这种资源共享设置下可能实现的微服务架构。图 14-7 中显示的架构选项无疑是对图 14-4 的改进；然而，仔细观察会发现，你并没有完全避免使用全局两阶段事务的需求。你的队列资源可以与任一微服务的应用事务数据库资源共享，但不能同时与两者共享。因此，对于另一个微服务和该队列，仍然存在两阶段提交事务的问题。

![../images/477630_1_En_14_Chapter/477630_1_En_14_Fig7_HTML.jpg](img/477630_1_En_14_Fig7_HTML.jpg)

图 14-7
微服务架构选项



这里需要根据所涉及的业务进行架构权衡。或许选项 1 更优，因为你希望将任意数量的新报价限流后交给报价处理微服务处理。通过在同一个事务中，将报价状态从“新建”改为“已确认”来持久化消息，同时更新新报价，便已安全捕获了更新外部微服务中`User Balance`表所需的信息。该事务仅在一个数据库实例上执行，因此通过将事务限制在本地，可以实现最大吞吐量。如果你假设有用户与报价处理微服务交互，一旦事务完成，需要向其设备或浏览器返回响应，那么这种设计也是可取的（尽管第 13 章的示例中没有用户等待，而是使用定时任务来处理报价）。

如果你为架构选择了选项 1，那么当你从队列中取出消息交由报价结算微服务进行结算时，仍然会遇到两阶段事务问题。如果你将报价结算视为纯粹的后台流程，那么在此处继续使用两阶段提交事务的一个理由是：你最大化面向客户微服务的吞吐量和可用性，同时以牺牲后台微服务的相对较低响应性为代价。类似的情况也适用于处理“昂贵”或“高后果”事务的场景，例如金融领域，你仍然希望继续使用两阶段提交事务，因为其收益大于成本。

同样，如图 14-7 所示，在选项 2 中，设计被重构了。然而，这仅仅表明你并未完全避免对两阶段提交事务的需求；相反，它将这种需求从下游转移到了上游！

分布式事务示例：向更少 ACID 重构

在“幂等操作与微服务”部分，你看到了企业系统中因部分失败而导致的问题。当你放宽 ACID 约束时，需要有机制来处理以下场景，包括但不限于：

*   部分失败
*   消息丢失
*   重复消息
*   重试尝试
*   消息重新投递
*   消息乱序接收

如你所知，上述所有场景在第 13 章中都已涵盖。最终目标是根据所涉及用例的性质和上下文，在分布式事务和本地事务之间做出选择。但图 14-7 说明，如果你的应用程序是单体应用，你可以仅使用本地事务。一旦你将单体应用拆分为微服务，就会在架构中引入分区，此时就需要进行权衡。图 14-7 中的选项 1 是首选方案，它避免在需要高响应性的微服务（例如与人类用户交互的微服务）中使用分布式事务，而在其他微服务中保留某种形式的分布式事务，这些微服务是你证明需要使用分布式事务的微服务架构部分，因为它们需要相对较低的响应性，即你的后台微服务。

到目前为止，一切顺利。但如果分布式事务或两阶段事务在你的微服务中根本不可接受呢？这个问题能否解决？让我们重新审视第 13 章中的一个示例。

走向宽松的 BASE：为处理重复和乱序消息而重新设计

这里的目标不是立即弃用分布式事务。相反，你将放宽第 13 章中的设计，模拟故障场景，并看看你是否能够克服第 13 章中看到的缺点。一旦你理解了这种增强设计，你将更好地掌握设计更具弹性和容错性的分布式系统的基础知识。一旦你掌握了这些基础知识，设计容错微服务就只是一个架构创意的问题了。

我在此引入的主要设计变更是在下游系统中创建一个名为`QuotesTX`的新表，如图 14-8 所示。

![../images/477630_1_En_14_Chapter/477630_1_En_14_Fig8_HTML.jpg](img/477630_1_En_14_Fig8_HTML.jpg)

图 14-8
报价事务表

你需要一种方法来跟踪通过消息中间件接收的哪些报价已结算，哪些仍未处理。对`QuotesTX`表的事务已由系统中一个名为 StockTransaction 的新实体处理，并且对该实体的所有操作都由一个新的事务性组件服务 QuotesTransactionService 负责。增强后的架构如图 14-9 所示。

![../images/477630_1_En_14_Chapter/477630_1_En_14_Fig9_HTML.jpg](img/477630_1_En_14_Fig9_HTML.jpg)

图 14-9
宽松 XA 事务示例的架构

解释示例中新构建块的宽松设计业务流如下：

1.  股票交易的新报价可以推送到 Broker Web 微服务。新报价将被插入到 MySQL 数据库中的`Quotes`表中，状态为“新建”。

2.  Quotes Processor Task 是一个 Quartz 定时任务，它会轮询 MySQL 数据库中的`Quotes`表，查找状态为“新建”的报价。

3.  当找到状态为“NEW”的报价时，它会调用 broker 服务的`processNewQuote`方法，该方法始终在一个事务中执行，并传递`Quotes`表中新报价的唯一标识符。

4.  broker 服务使用另外两个事务性服务：auction 服务和 stock order 服务。这两个服务的执行必须是原子性的。

5.  auction 服务通过在一个事务中将报价状态更改为“已确认”来确认收到的报价。

6.  stock order 服务根据新报价中包含的信息创建一条 JMS 消息，并将其发送到 ActiveMQ 队列以进行报价结算，同样在一个事务中完成。

7.  settlement listener 服务监听 ActiveMQ 队列，查找任何新的已确认报价。所有到达 ActiveMQ 队列的已确认报价都会在事务中被 settlement listener 服务的 onMessage 方法拾取。

8.  settlement listener 服务调用 quotes transaction 服务，以记录系统收到的所有报价。为了确保无论封闭父事务中的对等事务如何处理，该记录操作都能发生，此记录操作的事务语义为 REQUIRES_NEW。此记录操作在发现重复条目时不会引发任何错误。

9.  quotes transaction 服务尝试安全地记录下游微服务通过消息桥接收到的所有报价事务。

10. settlement listener 服务使用另外两个事务性服务：quotes reconcile 服务和 quotes transaction 服务（再次使用），这两个服务的执行必须是原子性的。



11. 报价对账服务需要在一笔交易中，将所交易股票的价值对账到卖方和买方各自的用户账户中。

12. 同时，报价交易服务通过将`QuotesTX`表中报价交易条目的状态从“新建”更改为“已结算”，来将其标记为已结算。

13. Broker Web 微服务和 Settlement Web 微服务只是提供仪表盘视图的工具，用于查看`Quotes`表、`User`表和`QuotesTX`表中的运营数据，以便为您提供实时视图。

关于新设计，还需要进行一些讨论。在新设计中，`StockTransaction`实体仅仅是微服务接收到的消息副本。在消息传递和分布式系统中，最佳实践是记录进出（子）系统的内容，以便日后需要追踪交易过程中发生的事件时，能够将实体状态与时间相关的操作关联起来。这样做的一个缺点是，由于数据重复，会占用更多的存储空间。然而，你需要回顾微服务的另一个基本原则：一个微服务应拥有其提供服务所需的一切，而无需任何外部依赖。这样一来，在某些场景下，就必须在微服务中复制所需数据并进行缓存。这也是第 12 章介绍的 CQRS 架构带来的影响之一：当一个微服务拥有某个实体的“写”治理权时，可能还有许多其他微服务对“读”模型感兴趣，此时也需要将读取的数据复制并附加到这些感兴趣的微服务上，以便它们能够独立运行，无需外部依赖。

另一个方面是，`StockTX`表也是下游微服务识别通过消息中间件传入的重复消息的一种机制。这是通过使用事务 ID 模式实现的。在这里，`StockTX`表还会记录发送方微服务随消息负载一起发送的事务 ID。发送方和接收方微服务必须就一个通用模式达成一致，即这些事务 ID 是唯一的，任何两条不同的消息都不会拥有相同的事务 ID。如果是这样，接收方微服务就可以将此事务 ID 与消息一起记录。这样一来，每当接收方微服务收到一条消息时，它就可以检查是否为重复消息，如果是，则可以静默忽略。

微服务的边界级操作应与内部处理隔离，并采用更具容错性的设计模式。这是因为微服务边界依赖于系统资源，如网络、I/O 等，这些资源更容易出错。因此，当下游微服务从消息网络接收到消息时，你的目标应该是使“记录新消息”的操作独立于后续的内部微服务处理，因为内部微服务处理仅由该微服务自身控制（不涉及前述的外部资源），并且在出错时可以重试。在新设计中，通过将新消息记录操作的事务语义设置为`REQUIRES_NEW`来实现这一点，这样无论同级事务或父事务的结果如何，该操作都会成功。

**编写弹性示例场景**

用于演示具有增强弹性以应对宽松 BASE 异常的分布式事务示例的完整代码位于文件夹`ch14\ch14-01`中。

上游微服务的代码与你在第 13 章中看到的相比没有重大变化，因此此处不再重复代码。我在下游微服务中引入了新代码并对现有代码进行了更新，因此我将在此处全部进行说明。主要变化在报价结算下游微服务中。

**微服务 3：报价结算 (Settlement-ActiveMQ-Derby)**

当一条新的报价消息到达 ActiveMQ 队列时，结算监听器会立即将其拾取。结算监听器的代码与你在第 13 章中看到的有所不同，请查看代码清单 14-7。

```java
public class SettlementListener implements MessageListener {
@Autowired
@Qualifier("quotesTransactionServiceRequired_TX")
QuotesTransactionService quotesTransactionServiceRequired_TX;
@Autowired
@Qualifier("quotesTransactionServiceRequiresNew_TX")
QuotesTransactionService quotesTransactionServiceRequiresNew_TX;
@Autowired
@Qualifier("quotesReconcileServiceRequired_TX")
QuotesReconcileService quotesReconcileServiceRequired_TX;
@Autowired
@Qualifier("quotesReconcileServiceRequiresNew_TX")
QuotesReconcileService quotesReconcileServiceRequiresNew_TX;
public void onMessage(Message message) {
QuoteDTO quoteDTO = null;
try {
quoteDTO = (QuoteDTO) ((ObjectMessage) message). getObject();
}
catch (JMSException e) {
throw new RuntimeException(e);
}
quotesTransactionServiceRequiresNew_TX.insertUniqueNoErrorOnDuplicate(
quoteDTO);
try {
reconcile(quoteDTO);
}
catch (QuotesBaseException e) {
throw new RuntimeException(e);
}
}
}
```
代码清单 14-7 结算监听器编排器 (ch14\ch14-01\XA-TX-Resilient\Settlement-ActiveMQ-Derby\src\main\java\com\acme\ecom\messaging\SettlementListener.java)

如代码清单 14-7 所示，在消费消息后，结算监听器会立即尝试记录由持久化资源支持的传入报价消息。为了使此记录操作不受封闭父事务内同级事务中任何其他处理结果（成功或失败）的影响，此记录操作的事务语义设置为`REQUIRES_NEW`。此记录操作在发现重复条目时不会引发任何错误。接下来，监听器会检查是否已经结算了与收到的报价类似的报价。这种情况发生在重复消息的情况下。请参见代码清单 14-8。



```
public class SettlementListener implements MessageListener {
private void reconcile(QuoteDTO quoteDTO)throws QuotesBaseException{
User seller = null;
User buyer = null;
StockTransaction stockTransaction = null;
Integer testCase = quoteDTO.getTest();
if(!quotesTransactionServiceRequired_TX.isNotSettled(
quoteDTO.getId())){
LOGGER.debug("Quote will not be attempted to be settled again");
return;
}
if(testCase.equals(1) || testCase.equals(2) ||
testCase.equals(4) || testCase.equals(8)){
try{
quotesReconcileServiceRequired_TX.reconcile(quoteDTO);
quotesTransactionServiceRequired_TX.markSettled(
quoteDTO.getId());
}
catch(QuotesBaseException quotesBaseException){
LOGGER.error(quotesBaseException.getMessage());
}
}
else if(testCase.equals(5)){
try{
quotesReconcileServiceRequiresNew_TX.reconcile(quoteDTO);
quotesTransactionServiceRequiresNew_TX.markSettled(
quoteDTO.getId());
}
catch(QuotesBaseException quotesBaseException){
LOGGER.error(quotesBaseException.getMessage());
}
flipFlop();
}
else if(testCase.equals(6)){
try{
quotesReconcileServiceRequiresNew_TX.reconcile(quoteDTO);
quotesTransactionServiceRequired_TX.markSettled(
quoteDTO.getId());
}
catch(QuotesBaseException quotesBaseException){
LOGGER.error(quotesBaseException.getMessage());
}
}
else if(testCase.equals(7)){
try{
quotesReconcileServiceRequired_TX.reconcile(quoteDTO);
quotesTransactionServiceRequired_TX.markSettled(
quoteDTO.getId());
}
catch(QuotesBaseException quotesBaseException){
LOGGER.error(quotesBaseException.getMessage());
}
flipFlop();
}
else{
LOGGER.debug("Undefined Test Case");
}
}
}
清单 14-8
结算监听器对账 (ch14\ch14-01\XA-TX-Resilient\Settlement-ActiveMQ-Derby\src\main\java\com\acme\ecom\messaging\SettlementListener.java)
```

如果该报价不是重复的，将尝试进行对账，如清单 14-8 所示。作为最后一步，监听器会将 `StockTX` 表中的股票交易标记为“已结算”，这样，如果后续有相同报价的重复消息到达，监听器就不会再次结算该报价。清单 14-9 展示了该股票交易。

```
@Entity
@Table(name = "stocktx")
@Data
@EqualsAndHashCode(exclude = { "id" })
public class StockTransaction {
public static final String NEW = "New";
public static final String SETTLED = "Settled";
@Id
@GeneratedValue(strategy = GenerationType.AUTO)
private Long id;
@Column(name = "stocksymbol", nullable = false, updatable = false)
private String stockSymbol;
@Column(name = "sellerid", nullable = false, updatable = false)
private Long sellerId;
@Column(name = "buyerid", nullable = false, updatable = false)
private Long buyerId;
@Column(name = "amount", nullable = false, updatable = false)
private Float amount;
@Column(name = "quoteid", nullable = false, updatable = false, unique=true)
private Long quoteId;
@Column(name = "status", nullable = false, updatable = true)
private String status;
@Column(name = "quotecreated", nullable = false, updatable = false)
@Temporal(TemporalType.TIMESTAMP)
private Date quoteCreated;
@Column(name = "test", nullable = true, updatable = true)
private Integer test;
@Column(name = "createdat", nullable = false, updatable = false)
@Temporal(TemporalType.TIMESTAMP)
private Date createdAt;
@Column(name = "updatedat", nullable = true, updatable = true)
@Temporal(TemporalType.TIMESTAMP)
private Date updatedAt;
}
清单 14-9
股票交易实体 (ch14\ch14-01\XA-TX-Resilient\Settlement-ActiveMQ-Derby\src\main\java\com\acme\ecom\model\trade\StockTransaction.java)
```

清单 14-9 中 StockTransaction 实体的字段是为了在后续章节中演示所需的测试用例而选择的。接下来，你将看到负责管理股票交易实体生命周期的 QuotesTransactionService，如清单 14-10 所示。

```
public class QuotesTransactionServiceImpl implements QuotesTransactionService{
@Autowired
private StockTransactionRepository stockTransactionRepository;
@Override
public boolean insertUniqueNoErrorOnDuplicate(QuoteDTO quoteDTO){
StockTransaction stockTransaction = null;
StockTransaction stockTransactionSaved = null;
boolean inserted = false;
Optional stockTransactionQueried =
stockTransactionRepository.findOptionalByQuoteId(quoteDTO.getId());
if(!stockTransactionQueried.isPresent()){
stockTransaction = convertQuoteToStockTransaction(quoteDTO);
stockTransaction.setStatus(StockTransaction.NEW);
Date now = new Date();
stockTransaction.setCreatedAt(now);
stockTransaction.setUpdatedAt(now);
stockTransactionSaved =
stockTransactionRepository.save(stockTransaction);
inserted = true;
}
else{
LOGGER.debug("Stock Transaction with quoteId : {} exist;
Cannot insert duplicate", quoteDTO.getId());
// Do Nothing - we know this is a duplicate message
}
return inserted;
}
}
清单 14-10
报价交易服务 (ch14\ch14-01\XA-TX-Resilient\Settlement-ActiveMQ-Derby\src\main\java\com\acme\ecom\service\QuotesTransactionServiceImpl.java)
```

清单 14-10 中值得注意的一点是，你以静默方式忽略了重复消息。此外，`isNotSettled` 和 `markSettled` 这两个实用方法也在清单 14-11 中展示。

```
public class QuotesTransactionServiceImpl implements QuotesTransactionService{
@Autowired
private StockTransactionRepository stockTransactionRepository;
@Override
public void markSettled(Long quoteId){
StockTransaction stockTransaction = null;
StockTransaction stockTransactionSaved = null;
Optional stockTransactionQueried =
stockTransactionRepository.findOptionalByQuoteId(quoteId);
if(stockTransactionQueried.isPresent()){
stockTransaction =
(StockTransaction) stockTransactionQueried.get();
stockTransaction.setStatus(StockTransaction.SETTLED);
stockTransaction.setUpdatedAt(new Date());
stockTransactionSaved =
stockTransactionRepository.save(stockTransaction);
}
else{
LOGGER.debug("Stock Transaction cannot be tracked");
}
}
@Override
public boolean isNotSettled(Long quoteId){
boolean isNotSettled = true;
StockTransaction stockTransaction = null;
Optional stockTransactionQueried =
stockTransactionRepository.findOptionalByQuoteId(quoteId);
if(stockTransactionQueried.isPresent()){
stockTransaction =
(StockTransaction) stockTransactionQueried.get();
if(stockTransaction.getStatus().equals(StockTransaction.SETTLED)){
isNotSettled = false;
}
}
return isNotSettled;
}
public Optional findOptionalByQuoteId(Long quoteId){
return stockTransactionRepository.findOptionalByQuoteId(quoteId);
}
}
清单 14-11
报价交易服务 (ch14\ch14-01\XA-TX-Resilient\Settlement-ActiveMQ-Derby\src\main\java\com\acme\ecom\service\QuotesTransactionServiceImpl.java)
```

报价对账服务也已在第 13 章的基础上进行了更新，以处理乱序到达的消息。请查看清单 14-12 中的代码，了解处理乱序消息的方法。



```
public class QuotesReconcileServiceImpl implements QuotesReconcileService{
@Autowired
private UserRepository userRepository;
@Override
public void reconcile(QuoteDTO quoteDTO)throws QuotesBaseException{
Integer testCase = quoteDTO.getTest();
Optional sellerQueried =
userRepository.findById(quoteDTO.getSellerId());
Optional buyerQueried  =
userRepository.findById(quoteDTO.getBuyerId());
User seller = null;
User buyer = null;
User sellerSaved = null;
User buyerSaved = null;
Date updatedDate = null;
if(sellerQueried.isPresent() && buyerQueried.isPresent()){
seller = (User) sellerQueried.get();
buyer  = (User) buyerQueried.get();
updatedDate = new Date();
seller.setAmountSold(seller.getAmountSold() +
quoteDTO.getAmount());
seller.setUpdatedAt(updatedDate);
if(quoteDTO.getCreatedAt().after(seller.getLastQuoteAt())){
seller.setLastQuoteAt(quoteDTO.getCreatedAt());
}
sellerSaved = userRepository.save(seller);
buyer.setAmountBought(buyer.getAmountBought() +
quoteDTO.getAmount());
buyer.setUpdatedAt(updatedDate);
if(quoteDTO.getCreatedAt().after(buyer.getLastQuoteAt())){
buyer.setLastQuoteAt(quoteDTO.getCreatedAt());
}
buyerSaved = userRepository.save(buyer);
if(testCase.equals(6)){
throw new QuotesReconcileRollbackException("Explicitly thrown
by Reconcile Application to Roll Back!");
}
if(testCase.equals(7)){
flipFlop();
}
}
else{
LOGGER.debug("Reconciliation Not Done");
}
}
}
清单 14-12
增强版报价对账服务代码 (ch14\ch14-01\XA-TX-Resilient\Settlement-ActiveMQ-Derby\src\main\java\com\acme\ecom\service\QuotesReconcileServiceImpl.java)
```

报价对账服务有一个辅助方法用于检索用户详细信息，如清单 14-13 所示。

```
public class QuotesReconcileServiceImpl implements QuotesReconcileService{
@Autowired
private UserRepository userRepository;
@Override
public Optional findUserById(Long id){
return userRepository.findById(id);
}
}
清单 14-13
增强版报价对账服务辅助方法
```

处理乱序到达的报价消息的方式是使用清单 14-14 中的检查逻辑。

```
if(quoteDTO.getCreatedAt().after(seller.getLastQuoteAt())){
seller.setLastQuoteAt(quoteDTO.getCreatedAt());
}
if(quoteDTO.getCreatedAt().after(buyer.getLastQuoteAt())){
buyer.setLastQuoteAt(quoteDTO.getCreatedAt());
}
清单 14-14
处理乱序到达的消息
```

这些检查仅在以下情况下更新买方或卖方的最后报价时间戳：上游微服务记录的原始报价消息的相应创建时间戳晚于该买方或卖方当前的最后报价时间戳。

构建并测试重复消息发送场景

由于此示例是第 13 章示例的延续，我不会详细解释该场景。相反，您的目标是理解如何管理重复消息和乱序消息，以便了解如何在微服务架构中更好地设计应对方案。因此，您将详细研究三个测试用例，如图 14-10 所示。

![../images/477630_1_En_14_Chapter/477630_1_En_14_Fig10_HTML.jpg](img/477630_1_En_14_Fig10_HTML.jpg)

图 14-10
XA 事务弹性测试用例

您关注的测试用例是：

*   测试用例 4：发送重复消息
*   测试用例 5：重复消费消息
*   测试用例 8：消息乱序到达

您将在本节中查看测试用例 4，并在后续两节中查看其他两个测试用例。
您需要执行与第 13 章中类似的步骤；但是，这里有一些额外的步骤。因此，此处将再次说明运行示例的完整步骤。此外，作为运行这些测试用例的前提条件，我假设您已具备运行第 13 章中所述测试场景的经验。
第一步，您需要启动 ActiveMQ 服务器。您可能需要参考附录 F 来开始使用 ActiveMQ 服务器。

您将配置一个队列，作为上游和下游处理之间的桥梁。清单 14-15 给出了队列的配置，该配置可以在 `activemq.xml` 中完成。

```

清单 14-15
ActiveMQ 队列配置 (D:\Applns\apache\ActiveMQ\apache-activemq-5.13.3\conf\activemq.xml)
```

现在您可以启动 ActiveMQ 服务器：

```
cd D:\Applns\apache\ActiveMQ\apache-activemq-5.13.3\bin
D:\Applns\apache\ActiveMQ\apache-activemq-5.13.3\bin>activemq start
```

接下来，确保 MySQL 已启动并正在运行。您可能需要参考附录 H 来开始使用 MySQL。

```
首先启动 MySQL 服务器
D:\Applns\MySQL\mysql-5.7.14-winx64\bin>mysqld --console
现在打开 MySQL 提示符
D:\Applns\MySQL\mysql-5.7.14-winx64\bin>mysql -u root –p
mysql> use ecom01;
Database changed
mysql>
```

为了从干净的表格开始，删除您示例中想要使用的任何名称的表格：

```
mysql> drop table quote;
```

接下来，使用此示例所需的模式创建表格：

```
mysql> create table quote (id BIGINT PRIMARY KEY AUTO_INCREMENT, symbol VARCHAR(4), sellerid BIGINT, buyerid BIGINT, amount FLOAT, status VARCHAR(9), test INTEGER, delay INTEGER, createdat DATETIME, updatedat DATETIME);
```

接下来，确保 Derby 数据库以网络模式启动并运行。您可能需要参考附录 G 来开始使用 Derby。

```
D:\Applns\apache\Derby\db-derby-10.14.1.0-bin\bin>startNetworkServer
```

您也可以使用 Derby ij 工具创建名为 `exampledb` 的数据库，并通过以下命令使用嵌入式驱动程序打开与已创建数据库的连接：

```
D:\Applns\apache\Derby\derbydb>ij
ij> connect 'jdbc:derby://localhost:1527/D:/Applns/apache/Derby/derbydb/exampledb;create=false';
```

注意
您从与 Derby 安装目录不同的基础位置使用 ij 工具，假设您的数据库位于与 Derby 安装目录不同的位置，这是一种最佳实践。有关如何创建新数据库的更多详细信息，请参考附录 G。

这里您再次从干净的表格开始。删除您示例中想要使用的任何名称的表格：

```
ij> drop table stockuser;
ij> drop table stocktx;
```

接下来，使用此示例所需的模式创建表格：

```
ij> create table stockuser (id bigint not null, amountbought double, amountsold double, createdat timestamp not null, lastquoteat timestamp, name varchar(255) not null, updatedat timestamp not null, primary key (id));
ij> create table stocktx (id bigint generated by default as identity, amount float not null, buyerid bigint not null, createdat timestamp not null, quotecreated timestamp not null, quoteid bigint not null, sellerid bigint not null, status varchar(255) not null, stocksymbol varchar(255) not null, test integer, updatedat timestamp, primary key (id));
ij> alter table stocktx add constraint stocktx_001 unique (quoteid);
ij> CREATE SEQUENCE hibernate_sequence START WITH 1 INCREMENT BY 1;
```

至此，构建和运行示例所需的基础设施已完成。
对于此示例，需要构建并运行四个微服务。您将逐一完成。

微服务 1：报价处理微服务

调整 `spring-sender-mysql.xml` 中特定于 ActiveMQ 的配置：



```
ch14\ch14-01\XA-TX-Resilient\Broker-MySQL-ActiveMQ\src\main\resources\spring-sender-mysql.xml

```

此外，调整 MySQL 专属配置：

```

jdbc:mysql://localhost:3306/ecom01

true

root
rootpassword

```

现在，您将构建并打包报价处理微服务的可执行文件，并启动定时处理器。文件夹 `ch14\ch14-01\XA-TX-Resilient\Broker-MySQL-ActiveMQ\make.bat` 中有一个实用脚本：

```
cd D:\binil\gold\pack03\ch14\ch14-01\XA-TX-Resilient\Broker-MySQL-ActiveMQ
D:\binil\gold\pack03\ch14\ch14-01\XA-TX-Resilient\Broker-MySQL-ActiveMQ>make
D:\binil\gold\pack03\ch14\ch14-01\XA-TX-Resilient\Broker-MySQL-ActiveMQ>mvn -Dmaven.test.skip=true clean install
```

现在，可以使用提供的脚本运行 JUnit 测试：

```
D:\binil\gold\pack03\ch14\ch14-01\XA-TX-Resilient\Broker-MySQL-ActiveMQ>run
D:\binil\gold\pack03\ch14\ch14-01\XA-TX-Resilient\Broker-MySQL-ActiveMQ>mvn -Dtest=BrokerServiceTest#testSubmitQuote test
```

微服务 2：Broker Web 微服务

首先，更新配置文件以适配您的环境：

```
ch14\ch14-01\XA-TX-Resilient\Broker-Web\src\main\resources\application.properties
server.port=8080
spring.datasource.url = jdbc:mysql://localhost:3306/oauth2?autoReconnect=true&useUnicode=true&characterEncoding=UTF-8&allowMultiQueries=true&useSSL=false
spring.datasource.username = root
spring.datasource.password = rootpassword
spring.jpa.properties.hibernate.dialect = org.hibernate.dialect.MySQL5Dialect
spring.jpa.hibernate.ddl-auto = update
spring.freemarker.cache=false
```

我建议您不要在此处做任何更改。

现在构建并打包 Broker Web 微服务的可执行文件，并启动服务器。文件夹 `ch14\ch14-01\XA-TX-Resilient\Broker-Web\make.bat` 中有一个实用脚本：

```
cd D:\binil\gold\pack03\ch14\ch14-01\XA-TX-Resilient\Broker-Web
D:\binil\gold\pack03\ch14\ch14-01\XA-TX-Resilient\Broker-Web>make
D:\binil\gold\pack03\ch14\ch14-01\XA-TX-Resilient\Broker-Web>mvn -Dmaven.test.skip=true clean package
```

您可以通过多种方式运行 Spring Boot 应用程序。最直接的方式是通过以下命令执行 JAR 文件：

```
D:\binil\gold\pack03\ch14\ch14-01\XA-TX-Resilient\Broker-Web>run
D:\binil\gold\pack03\ch14\ch14-01\XA-TX-Resilient\Broker-Web>java -jar -Dserver.port=8080 .\target\quotes-web-1.0.0.jar
```

这将在端口 8080 上启动 Broker Web Spring Boot 服务器。您可以使用浏览器（推荐 Chrome）并访问此 URL 来持续监控所有新传入报价的处理情况：`http://localhost:8080/`。

微服务 3：报价结算微服务

调整以下文件中的 ActiveMQ 专属配置：

```
ch13\ch13-01\XA-TX-Distributed\Settlement-ActiveMQ-Derby\src\main\resources\spring-listener-derby.xml

```

此外，您还需要调整 Derby 专属配置：

```

D:/Applns/apache/Derby/derbydb/exampledb
localhost

```

现在构建并打包报价结算微服务的可执行文件，并启动消息监听器。文件夹 `ch14\ch14-01\XA-TX-Resilient\Settlement-ActiveMQ-Derby\make.bat` 中提供了一个实用脚本：

```
cd D:\binil\gold\pack03\ch14\ch14-01\XA-TX-Resilient\Settlement-ActiveMQ-Derby
D:\binil\gold\pack03\ch14\ch14-01\XA-TX-Resilient\Settlement-ActiveMQ-Derby>make
D:\binil\gold\pack03\ch14\ch14-01\XA-TX-Resilient\Settlement-ActiveMQ-Derby>mvn -Dmaven.test.skip=true clean install
```

现在，可以使用提供的脚本运行 JUnit 测试：

```
D:\binil\gold\pack03\ch14\ch14-01\XA-TX-Resilient\Settlement-ActiveMQ-Derby>run
D:\binil\gold\pack03\ch14\ch14-01\XA-TX-Resilient\Settlement-ActiveMQ-Derby>mvn -Dtest=SettlementListenerServiceTest#testSettleQuote test
```

微服务 4：Settlement Web 微服务

首先，更新配置文件以适配您的环境：

```
ch14\ch14-01\XA-TX-Resilient\Settlement-Web\src\main\resources\application.properties
server.port=8081
spring.datasource.url=jdbc:derby://localhost:1527/D:/Applns/apache/Derby/derbydb/exampledb;create=false
spring.datasource.initialize=false
spring.datasource.driver-class-name=org.apache.derby.jdbc.ClientDriver
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.DerbyTenSevenDialect
spring.freemarker.cache=false
```

我建议您不要在此处做任何更改。

现在构建并打包 Settlement Web 微服务的可执行文件，并启动服务器。文件夹 `ch13\ch13-01\XA-TX-Distributed\Settlement-Web\make.bat` 中提供了一个实用脚本：

```
cd D:\binil\gold\pack03\ch14\ch14-01\XA-TX-Resilient\Settlement-Web
D:\binil\gold\pack03\ch14\ch14-01\XA-TX-Resilient\Settlement-Web>make
D:\binil\gold\pack03\ch14\ch14-01\XA-TX-Resilient\Settlement-Web>mvn -Dmaven.test.skip=true clean package
```

您可以通过多种方式运行 Spring Boot 应用程序。最直接的方式是通过以下命令执行 JAR 文件：

```
D:\binil\gold\pack03\ch14\ch14-01\XA-TX-Resilient\Settlement-Web>run
D:\binil\gold\pack03\ch14\ch14-01\XA-TX-Resilient\Settlement-Web>java -jar -Dserver.port=8081 .\target\user-web-1.0.0.jar
```

这将在端口 8081 上启动 Settlement Web Spring Boot 服务器。您可以使用浏览器（推荐 Chrome）并访问此 URL 来持续监控用户的运行账户状态，以及每当新传入的报价被结算时账户余额的变化情况：`http://localhost:8081/`。请注意，同一个控制台也会显示新引入的表——`Transactions` 表。

在开始执行任何测试用例之前，您需要在系统中创建两个测试用户。打开 Postman 并创建如下所示的两个测试用户：

```
http://localhost:8081/api/users
METHOD: POST; BODY: Raw JSON
{ "id" : 11, "name" : "Sam", "amountSold" : 1000.0, "amountBought" : 1000.0 }
{ "id" : 21, "name" : "Joe", "amountSold" : 5000.0, "amountBought" : 5000.0 }
```

如前所述，目的是执行测试用例 4；但是，为了便于关联第 13 章中测试用例的执行，您将从测试用例 1 开始执行所有测试用例。

要测试第一个测试用例，请打开 Postman 并创建一个新报价，如下所示：

```
http://localhost:8080/api/quotes
METHOD: POST; BODY: Raw JSON
{ "symbol" : "AAPL", "sellerId" : 11, "buyerId" : 21, "amount" : 100, "test" : 1, "delay" : 2 }
```

持续观察所有三个控制台。确认测试用例 1 完成后，再次打开 Postman 并创建另一个新报价来执行第二个测试用例，如下所示：

```
{ "symbol" : "AMZN", "sellerId" : 11, "buyerId" : 21, "amount" : 200, "test" : 2, "delay" : 2 }
```

测试用例 2 完全处理完毕后，执行第三个测试用例：

```
{ "symbol" : "GOOG", "sellerId" : 11, "buyerId" : 21, "amount" : 300, "test" : 3, "delay" : 2 }
```

如果您完全按照步骤操作，在测试用例 3 完成后，您的控制台将如图 14-11 和图 14-12 所示。

![../images/477630_1_En_14_Chapter/477630_1_En_14_Fig12_HTML.jpg](img/477630_1_En_14_Fig12_HTML.jpg)

图 14-12
股票交易表，测试用例 4 之前

![../images/477630_1_En_14_Chapter/477630_1_En_14_Fig11_HTML.jpg](img/477630_1_En_14_Fig11_HTML.jpg)

图 14-11
报价和用户表，测试用例 4 之前



您可能已经注意到，在图 14-12 中，尽管上游微服务处理了三条新的报价，但 `Stock Transaction` 表中只记录了两笔交易。这是因为测试用例 3 的报价消息在上游丢失了，从未到达下游微服务。出于同样的原因，测试用例 3 中 300 的报价金额也未被添加到买方和卖方的余额中，如图 14-11 所示。

现在，让我们执行您打算进行的测试用例，并验证弹性设计的效果。为了测试“重复消息发送”测试用例，再次打开 Postman 并创建另一条新报价，如下所示：

```
http://localhost:8080/api/quotes
METHOD: POST; BODY: Raw JSON
{ "symbol" : "NFLX", "sellerId" : 11, "buyerId" : 21, "amount" : 400, "test" : 4, "delay" : 2 }
```

图 14-13 展示了测试用例 4 的新报价。其状态为“新建”。结算后，报价金额 400 应被添加到买方和卖方各自的运行余额中，如图 14-11 所示。

![../images/477630_1_En_14_Chapter/477630_1_En_14_Fig13_HTML.jpg](img/477630_1_En_14_Fig13_HTML.jpg)

图 14-13
测试用例 4 控制台，初始状态

关于您正在执行的测试场景的详细说明，可参考第 13 章中的“测试重复消息发送场景”一节。在此，您模拟上游微服务发送重复的报价消息。在该节中，您看到报价被结算了两次，这是不期望的。

在本节执行的测试用例中，您模拟上游微服务发送重复的报价消息。该模拟通过成功发送消息，并使报价状态从“新建”更新为“已确认”的操作失败来实现。如图 14-14 所示。此时，报价金额已在下游微服务进行报价结算时被添加。

![../images/477630_1_En_14_Chapter/477630_1_En_14_Fig14_HTML.jpg](img/477630_1_En_14_Fig14_HTML.jpg)

图 14-14
测试用例 4 控制台，中间状态

您也可以查看图 14-15 中的 `Stock Transaction` 控制台，并看到测试用例 4 的报价结算已被记录。

在上游微服务的报价处理器任务的下一次执行中，它将再次拾取测试用例 4 的同一报价进行处理。这次，您也会向下游微服务发送一条报价消息；但是，在将报价状态从“新建”更新为“已确认”时，您不模拟错误。当此报价消息作为重复消息到达下游微服务时，由于解决方案的增强设计，下游微服务现在足够智能，能够通过查看 `Stock Transaction` 表（图 14-15）来识别此报价消息是重复的，因此不会进行结算。所有这些都显示在图 14-16 中。

![../images/477630_1_En_14_Chapter/477630_1_En_14_Fig16_HTML.jpg](img/477630_1_En_14_Fig16_HTML.jpg)

图 14-16
测试用例 4 控制台，完成状态

![../images/477630_1_En_14_Chapter/477630_1_En_14_Fig15_HTML.jpg](img/477630_1_En_14_Fig15_HTML.jpg)

图 14-15
测试用例 4 期间的 `Stock Transaction` 表

测试重复消息消费场景

为了测试“重复消息消费”测试用例，再次打开 Postman 并创建另一条新报价，如下所示（见图 14-17）：

![../images/477630_1_En_14_Chapter/477630_1_En_14_Fig17_HTML.jpg](img/477630_1_En_14_Fig17_HTML.jpg)

图 14-17
测试用例 5 控制台，初始状态

```
http://localhost:8080/api/quotes
METHOD: POST; BODY: Raw JSON
{ "symbol" : "TSLA", "sellerId" : 11, "buyerId" : 21, "amount" : 500, "test" : 5, "delay" : 2 }
```

在此测试用例中，上游微服务一切正常。您在下游微服务结算报价消息，然后模拟下游微服务的消息监听器在读取消息时出错。此时的状态如图 14-18 所示。

![../images/477630_1_En_14_Chapter/477630_1_En_14_Fig18_HTML.jpg](img/477630_1_En_14_Fig18_HTML.jpg)

图 14-18
测试用例 5 控制台，中间状态和完成状态

报价消息的结算也被下游微服务记录在 `Stock Transaction` 表中，如图 14-19 所示。

![../images/477630_1_En_14_Chapter/477630_1_En_14_Fig19_HTML.jpg](img/477630_1_En_14_Fig19_HTML.jpg)

图 14-19
测试用例 5 期间的 `Stock Transaction` 表

因为您模拟了下游微服务的消息监听器在读取消息时出错，消息代理将重新向该下游微服务投递该消息。但是，当此报价消息作为重复消息到达下游微服务时，由于解决方案的增强设计，下游微服务现在足够智能，能够通过查看 `Stock Transaction` 表（图 14-19）来识别此报价消息是重复的，因此不会再次结算；用户余额的最终状态保持如图 14-18 所示。

*“如果消费者使用消息来简单地写入（且从不覆盖）数据库中的某个值，那么多次收到一条消息与恰好收到一次消息没有区别”。* ^(³⁵)

这对于某些应用来说是成立的，但并非对所有应用都成立。至少，它要求应用相应地解释传入的消息。关于上述与微服务相关的消息传递声明，还有许多其他方面；然而，我们将把它们留待另一本关于此主题的教科书来讨论，因为相关的方面非常多。

测试消息乱序到达场景

您现在已经执行到测试用例 5。在执行测试用例 8 以模拟消息乱序到达之前，您将执行测试用例 6 和测试用例 7，但我不再进一步解释，因为我不想展示任何超出您在第 13 章相应章节中已看到的内容。

对于测试用例 6，打开 Postman 并创建一条新报价，如下所示：

```
http://localhost:8080/api/quotes
METHOD: POST; BODY: Raw JSON
{ "symbol" : "MSFT", "sellerId" : 11, "buyerId" : 21, "amount" : 600, "test" : 6, "delay" : 2 }
```

持续观察所有三个控制台。一旦确认测试用例 6 完成，再次打开 Postman 并创建另一条新报价来执行测试用例 7，如下所示：

```
{ "symbol" : "ORCL", "sellerId" : 11, "buyerId" : 21, "amount" : 700, "test" : 7, "delay" : 2 }
```

如果您完全按照指示操作，在测试用例 7 完成时，您的控制台将如图 14-20 和图 14-21 所示。

![../images/477630_1_En_14_Chapter/477630_1_En_14_Fig21_HTML.jpg](img/477630_1_En_14_Fig21_HTML.jpg)

图 14-21
`Stock Transaction` 表，测试用例 8 之前

![../images/477630_1_En_14_Chapter/477630_1_En_14_Fig20_HTML.jpg](img/477630_1_En_14_Fig20_HTML.jpg)

图 14-20
`Quotes` 和 `User` 表，测试用例 8 之前



现在实体已处于可执行测试用例 8 的状态，这是本节要考察的实际测试用例。此场景的测试稍显棘手，因为你需要根据测试装置协调每个操作的时机。我将基于我的测试装置配置进行说明，如果你没有对示例配置做任何修改，这些配置同样适用于你。

你需要向上游处理系统发送一个新的报价单，但希望延迟一段时间再处理。当这个报价单等待延迟结束时，你还想再向上游处理系统发送另一个新报价单，这个报价单将几乎无延迟地直接完成处理。这样一来，后创建的报价单会先到达下游系统进行后续处理，而最初创建的报价单则随后到达。通过这种方式，你将模拟消息在下游系统乱序到达的情况。

在开始为此测试用例发送请求之前，请完整阅读本节内容一次，以便在心理上做好按照指示发送新报价单的准备。然后回到此处继续执行实际测试。

再次打开 Postman 并发送一个新报价单，如下所示：

```
http://localhost:8080/api/quotes
方法：POST；请求体：原始 JSON
```

请注意，你在测试请求负载中附加了 90 秒的延迟，如图 14-22 所示。

![../images/477630_1_En_14_Chapter/477630_1_En_14_Fig22_HTML.jpg](img/477630_1_En_14_Fig22_HTML.jpg)

图 14-22
测试用例 8 控制台，创建第一个报价单

```
{ "symbol" : "QCOM", "sellerId" : 11, "buyerId" : 21, "amount" : 800, "test" : 8, "delay" : 90}
```

持续观察上游微服务控制台（命令提示符控制台），当报价处理器任务超时时，它将获取这个新报价单并将状态从“新建”更改为“已确认”，但在完成端到端事务之前，它会按照你提供的 90 秒延迟进入休眠状态。见图 14-23。

![../images/477630_1_En_14_Chapter/477630_1_En_14_Fig23_HTML.jpg](img/477630_1_En_14_Fig23_HTML.jpg)

图 14-23
测试用例 8 控制台，处理第一个报价单

这 90 秒具有重要意义，因为报价处理器任务的下一次超时将在接下来的 60 秒内发生，而在那次超时时，它不应获取此报价单，因为它已处于处理状态（报价单状态不是“新建”）。

关键点来了：一旦（在 10 到 20 秒内）报价处理器任务获取报价单进行处理并进入你提供的 90 秒延迟休眠状态，你需要立即发送另一个新报价单（理想情况下，你已准备好另一个 Postman 会话并填充好数据），如下所示：

```
http://localhost:8080/api/quotes
方法：POST；请求体：原始 JSON
{ "symbol" : "GILD", "sellerId" : 11, "buyerId" : 21, "amount" : 900, "test" : 8, "delay" : 2 }
```

这第二个新报价单如图 14-24 所示。仅此而已，测试执行步骤到此结束。

![../images/477630_1_En_14_Chapter/477630_1_En_14_Fig24_HTML.jpg](img/477630_1_En_14_Fig24_HTML.jpg)

图 14-24
测试用例 8 控制台，创建第二个报价单

当报价处理器任务下次超时时，它将仅获取这第二个新报价单。这是因为尽管第一个报价单仍在上游系统中处理，但其状态已更新为“已确认”，如图 14-24 所示。这是通过将上游微服务中拍卖服务的事务标记为`TX_REQUIRES_NEW`来实现的。

对于第二个新报价单，处理延迟可以忽略不计，它完成上游处理后，通过 ActiveMQ 到达下游系统进行后续处理。你预期第一个报价单仍在上游系统的处理阶段。这样，消息在下游系统中就出现了乱序。这种情况将按图 14-25 所示得到解决。

![../images/477630_1_En_14_Chapter/477630_1_En_14_Fig25_HTML.jpg](img/477630_1_En_14_Fig25_HTML.jpg)

图 14-25
测试用例 8 控制台，第二个报价单先结算

图 14-26 也证实，在测试用例 8 期间第二个创建的报价单确实被记录在`Stock Transaction`表中，并且该报价单处于已结算状态。

![../images/477630_1_En_14_Chapter/477630_1_En_14_Fig26_HTML.jpg](img/477630_1_En_14_Fig26_HTML.jpg)

图 14-26
测试用例 8 期间的 Stock Transaction 表

持续观察浏览器控制台。很快你将看到下游系统中用户的运行余额发生变化。这是第一个报价单的效果，其上游处理被延迟，导致它乱序到达下游系统并最终完成结算，如图 14-27 所示。

![../images/477630_1_En_14_Chapter/477630_1_En_14_Fig27_HTML.jpg](img/477630_1_En_14_Fig27_HTML.jpg)

图 14-27
测试用例 8 控制台，第一个报价单作为第二个结算

在第 13 章的“构建并测试示例的 Happy Flow”部分，你了解到每当用户账户更新时，上游系统中最新的报价单创建时间会被更新为最后报价时间。在此处的演示中，用户账户的最后报价时间并未根据第一个创建的报价单（最后到达）而改变。这是正确的，但在第 13 章中你未能实现这一点。由于解决方案的增强设计，下游微服务现在足够智能，能够通过详细检查到达的报价消息来理解，即使对于乱序到达的消息也能满足要求。总的来说，无论你使用何种事务范式（是否采用宽松的 BASE），当消息顺序很重要时，你都应该始终添加代码和设计来处理乱序消息。

图 14-28 显示了`Stock Transaction`表，该表也反映了从原始报价单创建顺序角度来看消息乱序的记录情况。

![../images/477630_1_En_14_Chapter/477630_1_En_14_Fig28_HTML.jpg](img/477630_1_En_14_Fig28_HTML.jpg)

图 14-28
测试用例 8 后的 Stock Transaction 表

选择事务选项
你在“全局与本地资源”一节中得出结论，论证了如果你为架构选择选项 1，那么当从队列中取出消息供报价结算微服务进行结算时，你仍然面临两阶段事务问题。如果你将报价结算视为纯粹的后台处理过程，那么继续使用两阶段事务的一个理由是，你最大化面向客户微服务的吞吐量和可用性，同时以相对较低的后台微服务响应性作为权衡。

但是，如果在微服务架构的某些部分，两阶段提交事务永远不可接受呢？即使你像前面讨论的那样，证明在特定用例中使用它们是合理的，两阶段提交事务也伴随着一系列影响。以下是其中几点：

*   两阶段提交事务协调器是单点故障。你需要分布式和复制的日志来保持状态，并保持协调器无状态以解决此问题。^(³⁶)



*   两阶段提交事务与本地事务相比，与资源的往返通信次数大约多出两倍，显得过于“啰嗦”。
*   资源锁的持有时间稍长，这在某些情况下会阻碍可扩展性。
*   最后但同样重要的是，两阶段提交事务并不能解决所有问题。当出现问题时，例如数据库崩溃后需要恢复，你可能仍然需要人工干预。为了避免这种情况，通常更倾向于采用某种形式的热故障转移架构。

你需要根据具体情况，在两阶段事务和本地事务之间做出明智的选择。对于你未来开发的许多微服务架构来说，情况都将如此。为了使场景更具体，假设图 14-7 中所示的**架构选项 1**比选项 2 更可接受。如果是这样，那么在需要时，如何进一步避免报价结算微服务与 ActiveMQ 消息代理之间的两阶段事务，同时仍然保证数据的完整性？让我们在下一节打开这个潘多拉魔盒。

消息队列、窥探与客户端确认
假设你想采用图 14-7 中所示的**架构选项 1**，并且仍然希望避免报价结算微服务与 ActiveMQ 消息代理之间的两阶段提交事务。当你避免使用两阶段提交事务时，如果数据库操作和消息队列操作由分区资源支持，那么它们就无法原子性地执行。如果它们无法原子性地执行，你就不希望自动封装从队列中读取消息和确认读取消息的操作；相反，应该由客户端程序或客户端代码来控制。JMS 规范支持两种消息传递类型：持久性和非持久性。如果你希望消息在传递给代理后始终可供消息消费者使用，即使该消费者在消息发送时未运行，也应使用持久性消息。当消息被消费者消费并确认后，它通常会从代理的消息存储中删除。

`Javax.jms.Session`可以被指定为事务性的。当事务性时，会话支持单一事务序列。每个事务将一组消息发送和一组消息接收组合成一个原子工作单元。实际上，事务将会话的输入消息流和输出消息流组织成一系列原子单元。当事务提交时，其原子输入单元被确认，其关联的原子输出单元被发送。如果执行了事务回滚，则事务的已发送消息将被销毁，并且会话的输入将自动恢复。

注意
与 XA 事务不同，JMS 事务仅关注单个连接的 JMS 消息。回滚或提交不会扩展到单个连接之外。

在本章以及第 13 章的示例中，你使用了以下配置：

或者，可以将`SESSION_TRANSACTED`值作为参数传递给`Connection`对象上的`createSession(int sessionMode)`方法，以指定会话应使用本地事务。使用时，它会通过`Session.commit()`来汇总确认。

```
Session session = connection.createSession(true, Session.SESSION_TRANSACTED);
```

通过指定`false`和`AUTO_ACKNOWLEDGE`，你可以创建一个非事务性会话，并且会话会自动确认消息。当消息成功从`receive()`方法或`onMessage()`方法返回时，它会自动被确认。如果在执行`receive()`方法或`onMessage()`方法时发生故障，消息会自动重新投递。JMS 提供者管理消息重新投递，并保证一次性投递语义。但是，如果应用程序在`receive()`返回之后、消息被处理之前崩溃，消息仍然可能丢失。

```
Session session = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);
```

如果你创建接收者的会话，并将`false`指定为`createSession()`的第一个参数，将`Session.CLIENT_ACKNOWLEDGE`指定为其第二个参数，则你将在客户端确认模式下创建会话。这会创建一个非事务性会话，并且需要显式调用`Message`类的`acknowledge()`方法来确认消息。通过在已消费的消息上调用`acknowledge()`，客户端会确认该消息投递到的会话所消费的所有消息。客户端可以在消费每条消息时单独确认，也可以选择按应用程序定义的组来确认消息（通过调用组中最后接收到的消息的`acknowledge()`方法，从而确认该会话消费的所有消息）。已接收但未确认的消息可能会被重新投递，这是一把双刃剑：既是福音也是诅咒。说它是福音，是因为如果未确认，你可以确保消息会在稍后的某个时间点被重新投递。同时，它也是诅咒，因为如果你的应用程序设计没有处理重复消息的机制，就会导致重复处理，从而产生不利影响。

让我们进一步探讨这个方面。参见清单 14-16。

```
开始 两阶段事务
开始 ActiveMQ 事务-01
从 ActiveMQ 读取消息
开始 Derby 事务-01
在 Derby 中记录收到的报价
结束 Derby 事务-01
开始 Derby 事务-02
在 Derby 中协调用户余额
更改报价状态
结束 Derby 事务-02
结束 ActiveMQ 事务-01
结束 两阶段事务
清单 14-16
两阶段事务内的 ActiveMQ 和 Derby
```

你在本章前面的示例下游处理中看到的从 ActiveMQ 队列消费消息以及在 Derby 数据库中的数据库操作，可以像清单 14-16 那样用伪代码表示。如果你移除两阶段事务，同样的操作可以重写为清单 14-17。

```
从 ActiveMQ 读取消息
开始 Derby 事务-01
在 Derby 中记录收到的报价
结束 Derby 事务-01
开始 Derby 事务-02
在 Derby 中协调用户余额
更改报价状态
结束 Derby 事务-02
确认从 ActiveMQ 读取的消息
清单 14-17
无两阶段事务的 ActiveMQ 和 Derby
```

这等同于清单 14-18。

```
从 ActiveMQ 窥探消息
开始 Derby 事务-01
在 Derby 中记录收到的报价
结束 Derby 事务-01
开始 Derby 事务-02
在 Derby 中协调用户余额
更改报价状态
结束 Derby 事务-02
从 ActiveMQ 移除消息
清单 14-18
使用本地事务的 ActiveMQ 和 Derby
```



清单 14-18 中的伪代码依赖于以下能力：在不移除消息的情况下窥探队列中的消息，以及在成功处理后从队列中移除消息。如有必要，这可以通过两个或更多独立事务来完成，如伪代码所示：一个事务处理消息队列，另一个事务在用户数据库中记录报价消息，第三个事务则用于协调用户余额并更改报价状态。用户余额协调和报价状态更改是原子性提交的；然而，由于这两个操作发生在同一个本地资源中，因此不需要两阶段事务。除非数据库操作成功提交，否则队列操作不会提交。数据库操作期间的部分失败将导致队列操作保持未提交状态，这样消息迟早会被重新投递，并重新开始处理。该算法现在支持部分失败，同时仍能提供事务性保证。这种算法或方法是在不使用分布式两阶段提交事务的情况下，在分布式微服务之间实现最终一致性的一种尝试。此处提供的示例代码可以增强，以实现上述方法的多种变体；这留作练习，供您动手实践代码。请注意，这需要实现“幂等消费者模式”，而正确实现该模式颇具难度。^(³⁷) 除了本章中的基本探索性测试用例之外，还需要进行额外的测试，以便考虑到所有可能的故障模式。

总结
和我的许多同事一样，我也曾热爱事务。自 1999 年起，我就开始使用声明式事务管理，当时我们很兴奋，因为我们是首批下载 *Sun 的 J2EE 服务器参考实现（含 EJB）*^(³⁸) *(Enterprise Java Beans) 容器* 的人之一，用于为一家欧洲航空公司实施优化其客运预订系统运营的专家系统。从那时起已经过去了 20 年，我们仍然热爱事务，无论是本地事务还是分布式事务。然而，关键在于，分布式事务和本地事务之间的选择必须根据所涉及微服务的上下文、功能和关键性，逐案进行。在并非强制需要的情况下，设计利用分布式事务管理器的系统成本高昂，因为它可能会增加运营成本，并略微影响性能。在本章中，您看到了一些可用于设计必须最终一致的容错微服务的技术，尽管我并未解决所有可能的异常情况。在下一章中，您将扩展所学知识，研究其他一些场景，包括但不限于长时间运行的事务。

脚注

15. 为微服务优化的事务

虽然第 13 章介绍了带有工作代码的分布式事务，但您在第 14 章中以增强的设计重新审视了这些示例，旨在理解在为 BASE 事务支持设计架构时的细节和关注点。到目前为止一切顺利，但问题在于，是否有一种方法可以抽象您在这两章中看到的细节，同时仍然在微服务架构中达到所需的数据一致性水平。您将在本章中详细探讨这一点。本章中的示例并非第 13 章和第 14 章中示例的延续，因此如果您急于理解本章中的示例，可以跳过那两章。但是，您将在本章中再次利用 Axon 框架，因此强烈建议您先阅读第 12 章以了解 Axon 的入门知识，这样您就能在讨论中熟悉编程范式。

您将在本章中探讨以下内容：

*    Saga 简介
*    分布式 Saga 与微服务
*    使用 Axon 2 的完整 Saga 示例

微服务事务的 Saga
第 13 章中“事务模型”一节介绍了 Saga 的概念，在本节中，您将详细探讨 Saga。



Saga 详解
Saga 的概念最早于 1987 年在一篇论文中提出，并在微服务背景下逐渐流行起来。Saga 与嵌套事务类似。在嵌套事务中，原子事务被嵌入到其他事务中。而在 Saga 中，每个事务都有一个对应的补偿事务。如果 Saga 中的任何一个事务失败，则会调用之前成功运行的每个事务的补偿事务，以抵消之前成功事务的影响。

Saga 通常用于对工作流中涉及的**长时间运行的事务**进行建模。不建议使用两阶段事务来控制长时间运行的事务，因为长时间锁定资源是绝对不可取的。在微服务背景下，即使不涉及长时间运行的事务，Saga 也变得相关，原因在于我们希望拒绝两阶段提交事务。当无法使用两阶段提交事务，并且您仍希望对多步骤事务的每个阶段进行控制时，Saga 将为您提供帮助。

即使在微服务演进之前，Saga 的概念就已经非常流行，并且从最真实的意义上讲，许多现实世界的问题都是通过 Saga 来解决的。跨位置和/或信任边界维护一致性很难通过经典的 ACID 模型来处理，而许多现实场景都涉及这些复杂性。将资金从一个银行的账户转移到另一个银行的账户是一个经典示例，没有理由假设这些银行的软件系统彼此靠近或在一起。当您进行此类资金转账时，可以从日常经验中体会到这一点。即使资金转账近乎实时完成，以至于在各个方面看起来都是瞬间完成的，但所涉及的事务很可能并非通过两阶段提交事务执行，而是通过某种 Saga 来执行。这意味着，当您进行资金转账时，相关的借记和贷记在大多数情况下会作为两个关联的步骤发生，通过某种非两阶段事务协调器的方式进行协调，以便所有涉及的步骤要么全部发生，要么最终全部回滚。

另一个经典示例是预订航班、酒店和租车。单个企业拥有所有这些资源的库存可能性很小，但最终用户很可能希望在一个事务中预订其中一项或多项资源，因为对他来说，已确认的酒店预订加上未确认的航班预订用处不大。这里的另一个方面是，如果您查看此类情况下的资源管理器或资源所有者，他们可能位于信任边界之外，并且在许多情况下，他们可能是相互竞争的企业。我目前所在的公司致力于为旅游和酒店行业构建软件系统，我可以联想到我们软件设计中的许多实例，在这些实例中，类似的事务可以并且只能被设计为最终一致性。

图 15-1 展示了一个状态图，说明了在 Saga 中，各个事务如何逐步推进。

![../images/477630_1_En_15_Chapter/477630_1_En_15_Fig1_HTML.jpg](img/477630_1_En_15_Fig1_HTML.jpg)

图 15-1
说明 Saga 的状态图

如图 15-1 所示，在 Saga 中，如果其中一个事务失败，则会执行每个成功运行的事务的补偿事务。这由 **Saga 事务协调器** 进行协调。

分布式 Saga
上一节描述的 Saga 概念构建起来相当简单，因为许多工作流和业务流程模型与符号（BPMN）框架都是使用相同的类比构建的，即编排长时间运行的事务。如果要在微服务环境中协调这一点，则涉及协调多个去中心化的节点和进程，这并非易事。

您将首先从概念层面了解分布式 Saga 所需的各种组件。见图 15-2。

*   **事务和补偿事务：**

在微服务上下文中，Saga 事务最好称为请求（HTTP）或事件（命令和事件）；然而，为了讨论的简便，我们将保留“事务”这一术语。

分布式 Saga 是事务的集合：

```
    T1, T2, ..., Tn
    ```

每个事务都有一个补偿事务：

```
    C1, C2, ..., Cn
    ```

补偿事务 (Cn) 将在语义上撤销先前完成的对应事务 (Tn)。

分布式 Saga 保证要么发生

```
    T1, T2, ..., Tn
    ```

要么发生

```
    T1, T2, ..., Ti, Ci, ..., C2, C1
    ```

因此，如图 15-1 所示，如果其中一个事务失败，则会执行每个成功运行的事务的补偿事务。

![../images/477630_1_En_15_Chapter/477630_1_En_15_Fig2_HTML.jpg](img/477630_1_En_15_Fig2_HTML.jpg)

图 15-2
分布式 Saga 示意图

*   **Saga 日志：**

Saga 日志是一个分布式日志，用于在给定 Saga 执行期间持久化每个事务/操作的状态。Saga 日志包含各种状态变更操作，例如开始 Saga、结束 Saga、中止 Saga、开始 Ti、结束 Ti、开始 Ci 和结束 Ci。Saga 日志通常使用分布式日志实现，并且通常使用诸如 Kafka 之类的系统进行实现。

*   **Saga 执行协调器 (SEC)：**

SEC 编排整个逻辑，并负责 Saga 的执行。给定 Saga 中的所有步骤都记录在 Saga 日志中，SEC 写入、读取并解释 Saga 日志中的记录。SEC 具有以下子组件：

*   **控制总线：** 控制总线有效地管理一个分布在多个节点和进程之间的消息传递系统。

*   **流程管理器：** 流程管理器维护序列的状态，并根据中间结果确定下一个处理步骤。

*   **消息关联：** 发送方将其收到的响应与其最初发送的请求关联起来。消息的 MessageID 和 CorrelationID 属性用于关联请求和响应消息。因此，关联标识符是一个唯一标识符，指示此回复是针对哪个请求消息的。

*   **消息序列：** 这是为了保持按原始意图发送的消息的顺序。

*   **保证交付：** 通过保证交付，系统使用内置的数据存储来持久化消息。安装消息传递系统的每个节点都有自己的数据存储，以便消息可以本地存储。当发送方发送消息时，在消息安全地存储在发送方的数据存储中之前，发送操作不会成功完成。随后，在消息成功转发并存储到下一个数据存储之前，不会将其从一个数据存储中删除。通过这种方式，一旦发送方成功发送消息，该消息将始终存储在至少一台计算机的磁盘上，直到成功交付给接收方并得到接收方的确认。



SEC 并非一个集中控制整个执行过程的特殊流程。  
它作为一个集中式运行时运行，但该运行时是“哑”的，执行逻辑被保存在 SEC 外部的分布式 Saga 日志中。因此，当某个 SEC 崩溃时，可以启动另一个 SEC 实例，通过查看 Saga 日志来继续执行。这要求确保 SEC 始终保持运行状态。一旦 SEC 发生故障，应基于相同的分布式 Saga 日志启动一个新的 SEC 进程。该分布式 Saga 日志本身也是分布式且经过复制的，因此不存在单点数据丢失的风险。

在了解了分布式 Saga 所需的各个组件之后，现在来看一下分布式 Saga 是如何执行的。请参见图 15-3。

![../images/477630_1_En_15_Chapter/477630_1_En_15_Fig3_HTML.jpg](img/477630_1_En_15_Fig3_HTML.jpg)

图 15-3  
作为有向无环图的分布式 Saga

分布式 Saga 是一个有向无环图（DAG），SEC 的任务就是执行这个 DAG。有向无环图是一个没有有向环的有限有向图^(³⁹)。也就是说，它由有限数量的顶点和边（也称为弧）组成，每条边从一个顶点指向另一个顶点，并且不存在从某个顶点 (v) 出发，沿着一致方向的边序列，最终又回到 v 的情况。等价地，DAG 是一个具有拓扑排序的有向图，即顶点的一个序列，使得每条边都从序列中较早的顶点指向较晚的顶点。

参考图 15-2，一旦旅行服务收到预订请求，SEC 就会通过向 Saga 日志写入一条包含“开始 Saga”指令以及处理该 Saga 所需的其他元数据来启动一个 Saga。一旦该记录被持久化提交到日志中，SEC 就可以继续执行下一条指令。然后，根据图 15-3 中的 Saga DAG，SEC 可以选择执行航班、酒店或出租车预订事务中的任意一个（因为这三个事务可以并行处理）。假设首先执行航班事务。在这种情况下，SEC 会向 Saga 日志记录一条“开始航班事务”的消息。然后，SEC 执行预订航班操作。一旦 SEC 收到航班服务的响应，它就会提交“结束航班事务”消息以及来自航班服务的响应（在 Saga 后续阶段可能需要用到）。重复执行相同的步骤，直到成功完成对航班、酒店和出租车预订服务这三个操作。最后，由于所有操作都已成功完成，SEC 会将“结束 Saga”消息提交到 Saga 日志中。这是一个成功的 Saga 执行过程，如图 15-4 所示。

![../images/477630_1_En_15_Chapter/477630_1_En_15_Fig4_HTML.jpg](img/477630_1_En_15_Fig4_HTML.jpg)

图 15-4  
成功分布式 Saga 的执行步骤

图 15-5 展示了一个 Saga 失败的情况。

![../images/477630_1_En_15_Chapter/477630_1_En_15_Fig5_HTML.jpg](img/477630_1_En_15_Fig5_HTML.jpg)

图 15-5  
失败分布式 Saga 的执行步骤

这与前面讨论的步骤相同，但在本例中，出租车预订过程失败了（例如，指定日期没有可用出租车）。由于检测到某个子事务（即出租车预订）失败，需要回滚到目前为止已执行的所有其他子事务。因此，在 Saga 日志中可以找到“开始出租车预订”日志（Start T3），但现在出租车预订已经失败。此时，必须遍历到目前为止已执行的 Saga 的逆 DAG，并将该 Saga 标记为失败。

现在来看一下分布式 Saga 的一些有趣方面。前面提到，SEC 可以选择执行航班、酒店或出租车预订事务中的任意一个（因为这三个事务可以并行处理）。这意味着 Saga 事务可以并行执行，并且 Saga 日志可能是交错记录的，如图 15-6 所示。

![../images/477630_1_En_15_Chapter/477630_1_En_15_Fig6_HTML.jpg](img/477630_1_En_15_Fig6_HTML.jpg)

图 15-6  
并行执行事务的分布式 Saga

这意味着 SEC 不应依赖 Saga 日志中单个日志条目的顺序。应重新审视第 14 章中“测试乱序接收消息场景”一节的讨论，其中介绍了处理此类场景的技术。

接下来，对于分布式 Saga 中的每个事务，消息传递语义应如下：

*   事务：最多一次
*   补偿事务：至少一次

现在应重新阅读第 14 章中“构建并测试重复发送消息场景”和“测试重复消费消息场景”两节。通过适当结合这两节中描述的技术，可以构思如何将“最多一次”和“至少一次”语义整合到整体解决方案中。使用相同的技术，还应处理表 15-1 中列出的分布式 Saga 事务所需的额外特性。

表 15-1  
分布式 Saga 事务特性

特性 | 事务 (T) | 补偿事务 (C)
--- | --- | ---
幂等性 | 是 | 是
可中止 | 是 | `×`
可交换 | 不适用 | 是

关于表 15-1 中描述的补偿事务所需的可交换特性，需要稍作讨论。请参考图 15-7。

![../images/477630_1_En_15_Chapter/477630_1_En_15_Fig7_HTML.jpg](img/477630_1_En_15_Fig7_HTML.jpg)

图 15-7  
分布式 Saga 中的消息延迟

这里出现了两个 Start T1 事务。这是因为如果 SEC 在定义的超时时间内未收到 Start T1 和 End T1，它可以重放 Start T1。这个延迟到达的 Start T1 可能会在补偿事务（Start C1, End C1）成功执行之后才到达待执行的服务，在这种情况下，不应尝试执行这个后来才到达的 Start T1。因此，在语义上：

```
T1 -> C1
```

或

```
C1 -> T1
```

两者应具有相同的效果。
还有更多类似的复杂性，但到现在为止应该已经掌握了要点。理想情况下，一个框架应该为你抽象掉所有这些复杂性。尽管在撰写本文时，我还没有找到一个完整的框架来执行我们之前的讨论，但我希望这样的框架最终会出现，不过我不再进一步讨论，因为此类讨论不在本书的范围内。

在结束这个话题之前，让我们在 Axon CQRS 和基于事件的微服务背景下，看一下图 15-2 的一个可能的设计替代方案，如图 15-8 所示。

![../images/477630_1_En_15_Chapter/477630_1_En_15_Fig8_HTML.jpg](img/477630_1_En_15_Fig8_HTML.jpg)

图 15-8  
事件驱动 CQRS 上下文中的分布式 Saga 示意图

请注意，图 15-8 所示的设计仅用于说明目的，在下一节中将会看到，Axon CQRS 有一个类似的 Saga 实现，但不一定与所示设计完全一致。

使用 Axon 的 Saga 示例代码



你将扩展第 12 章中的示例代码，该代码是一个电子商务应用的一部分。你的领域中有两个实体：订单和产品。用户可以购买产品，这将创建一个新订单。当新订单创建时，订单状态将处于“新建”状态，产品库存将减少。创建新订单还会在 `Audit` 表中添加一条记录。示例应用如图 15-9 所示。

![../images/477630_1_En_15_Chapter/477630_1_En_15_Fig9_HTML.jpg](img/477630_1_En_15_Fig9_HTML.jpg)

图 15-9
Saga 示例应用

设计示例 Saga 场景

此示例与第 12 章中“分布式命令与事件处理”一节的示例类似，不同之处在于 Handle Command and Create Event 微服务中添加了一个 saga 事务管理器。共有五个微服务。Event Handle Core 微服务有一个事件处理器 OrderEventHandler，而 Event Handler Audit 微服务有一个事件处理器 AuditEventHandler。两者都订阅了订单创建事件。因此，你有两个微服务（Event Handler Audit 微服务和 Event Handle Core 微服务）包含两种事件处理器类型，它们都对同一个订单创建事件感兴趣。此外，Handle Command and Create Event 微服务有一个 saga，即 OrderProcessSaga，它也对订单创建事件感兴趣。图 15-10 展示了该设计。

![../images/477630_1_En_15_Chapter/477630_1_En_15_Fig10_HTML.jpg](img/477630_1_En_15_Fig10_HTML.jpg)

图 15-10
为新订单启动 Saga

当你订购新产品时，端到端流程中架构内的命令和事件流如下所示：

![../images/477630_1_En_15_Chapter/477630_1_En_15_Fig11_HTML.jpg](img/477630_1_En_15_Fig11_HTML.jpg)

图 15-11
新订单已创建

1.  当你在浏览器中点击“立即订购！”按钮时，一个 REST/JSON 请求会到达订单控制器。
2.  Create Command Rest Controller 微服务中的订单控制器将该请求解释为写入请求，因此创建一个新订单命令并将其发送到命令网关，命令网关随后将该命令分发到分布式命令总线。
3.  分布式命令总线在跨 JVM 的命令总线实现之间形成一座桥梁。每个 JVM 上的分布式命令总线被称为一个段。
4.  命令总线接收命令并将其路由到 Handle Command and Create Event 微服务中的订单命令处理器。
5.  订单命令处理器检索对应的产品并减少库存。
6.  减少产品库存后，通过向产品实体发送更新来在产品仓库中生效更改，该更新会持久化到写入数据库。
7.  此仓库更改会向事件总线触发一个库存更新事件，事件总线随后将事件分发给所有感兴趣的事件监听器。
8.  Event Handle Core 微服务中的产品事件处理器已订阅库存更新事件，因此它会收到库存更新详情的通知。
9.  根据库存更新事件中提到的详细信息，产品事件处理器对产品的读模型进行更改。
10. 在第 5 步中，订单命令处理器减少了产品库存。它还根据收到的新订单命令中提到的详细信息创建一个新订单。
11. 当新订单创建时，通过插入一个新订单实体在订单仓库中生效，该实体随后被持久化到写入数据库，新订单的状态为“新建”，如图 15-11 所示。
12. 此仓库插入操作会向事件总线触发一个订单创建事件，事件总线随后将事件分发给所有感兴趣的事件监听器。在两个外部微服务（Event Handle Core 微服务和 Event Handler Audit 微服务）中有两个感兴趣的事件监听器。此外，订单处理 saga 也对订单创建事件感兴趣。
13. 订单处理 saga 在收到订单创建事件后启动一个新的 saga。
14. Event Handle Core 微服务中的订单事件处理器已订阅订单创建事件，因此它会收到新订单创建详情的通知。
15. 根据订单创建事件中提到的详细信息，订单事件处理器对订单的读模型进行更改，在本例中插入一行新数据。
16. Event Handler Audit 微服务中的审计事件处理器也已订阅订单创建事件，因此它会收到新订单创建详情的通知。
17. 根据订单创建事件中提到的详细信息，审计事件处理器在审计的读模型中添加一条新记录。
18. 浏览器可以自行刷新，以通过查询实体的读模型来保持视图更新。

Saga 事件处理器中的事件处理与常规事件监听器类似，正如你在第 12 章的代码清单 12-11 中所见。方法和参数解析的相同规则在此同样适用；但有一个区别。虽然处理所有传入事件的事件监听器只有一个实例，但 saga 可能存在多个实例，每个实例只对针对不同实体实例的 saga 事件感兴趣。为了明确说明，管理 ID 为 x 的订单事务的 saga 不会对针对 ID 为 y 的订单的事件感兴趣，反之亦然。之前启动的新 saga 将存在于内存中，并由持久化支持。如图 15-11 所示，订单工作流可以从“新建”状态进展到“已取消”或“已确认”状态。你将分别研究它们。

首先，你将看到 saga 因订单确认而从“新建”状态转换到“结束”状态。见图 15-12。

![../images/477630_1_En_15_Chapter/477630_1_En_15_Fig12_HTML.jpg](img/477630_1_En_15_Fig12_HTML.jpg)

图 15-12
订单确认后结束 Saga

当你点击确认新订单时，端到端流程中架构内的命令和事件流如下：

1.  当你在浏览器中点击“确认”按钮以确认一个已创建的订单时，一个 REST/JSON 请求会到达订单控制器。
2.  Create Command Rest Controller 微服务中的订单控制器将该请求解释为写入请求，并创建一个订单状态更新命令，将其发送到命令网关，命令网关随后将该命令分发到分布式命令总线。
3.  分布式命令总线在跨 JVM 的命令总线实现之间形成一座桥梁。每个 JVM 上的分布式命令总线被称为一个段。
4.  命令总线接收命令并将其路由到 Handle Command and Create Event 微服务中的订单命令处理器。
5.  订单命令处理器检索对应的订单，并将状态从“新建”更改为“已确认”。
6.  更改订单状态后，通过向订单实体发送更新来在订单仓库中生效更改，该更新会持久化到写入数据库。
7.  此仓库更改会向事件总线触发一个订单已确认事件，事件总线随后将事件分发给所有感兴趣的事件监听器。



8.  事件处理核心微服务中的订单事件处理器已订阅订单确认事件，因此它会收到状态更新的详细信息通知。

9.  根据订单确认事件中提到的详细信息，订单事件处理器会对订单的读取模型进行修改。

10. 如前所述，Saga 也对订单确认事件感兴趣。收到订单确认事件后，Saga 将转换到“结束 Saga”状态。

11. 事件处理审计微服务中的审计事件处理器也已订阅订单确认事件，因此它也会收到新订单创建的详细信息通知。

12. 根据订单确认事件中提到的详细信息，审计事件处理器将在审计的读取模型中添加一条新记录。

13. 浏览器可以自行刷新，通过查询实体的读取模型来保持视图更新。

最后，您将看到 Saga 因取消已创建的订单而从“新建”状态转换到“结束”状态。见图
15-13。

![../images/477630_1_En_15_Chapter/477630_1_En_15_Fig13_HTML.jpg](img/477630_1_En_15_Fig13_HTML.jpg)

图 15-13
订单取消后的结束 Saga

当您点击取消已创建的订单时，架构中端到端流程的命令和事件流如下：

1.  当您在浏览器中点击“取消”按钮以取消已创建的订单时，一个 REST/JSON 请求会到达订单控制器。

2.  创建命令 REST 控制器微服务中的订单控制器将该请求解释为写入请求，创建订单状态更新命令，并将其发送到命令网关，命令网关随后将该命令分发到分布式命令总线。

3.  分布式命令总线在跨 JVM 的命令总线实现之间形成一座桥梁。每个 JVM 上的分布式命令总线称为一个段。

4.  命令总线接收命令并将其路由到位于处理命令和创建事件微服务中的订单命令处理器。

5.  订单命令处理器检索相应的订单，并将订单状态从“新建”更改为“已取消”。

6.  更改订单状态后，通过向订单实体发送更新来在订单仓库中生效更改，该更新将持久化到写入数据库中。

7.  此仓库更改将向事件总线触发一个订单取消事件，事件总线随后将事件分发给所有感兴趣的事件监听器。

8.  事件处理核心微服务中的订单事件处理器已订阅订单取消事件，因此它会收到状态更新的详细信息通知。

9.  根据订单取消事件中提到的详细信息，订单事件处理器将对订单的读取模型进行修改。

10. 如前所述，Saga 也对订单取消事件感兴趣。收到订单取消事件后，Saga 将转换到“结束 Saga”状态。

11. 由于取消订单而转换到结束状态时，Saga 还会向分布式命令总线输出一个新的产品库存还原命令。

12. 处理命令和创建事件微服务中的产品命令处理器检索相应的产品，并根据已取消订单中订购的商品数量还原库存数量。

13. 还原产品库存后，通过向产品实体发送更新来在产品仓库中生效更改，该更新将持久化到写入数据库中。

14. 此仓库更改将向事件总线触发一个库存更新事件，事件总线随后将事件分发给所有感兴趣的事件监听器。

15. 事件处理核心微服务中的产品事件处理器已订阅库存更新事件，因此它会收到库存更新的详细信息通知。

16. 根据库存更新事件中提到的详细信息，产品事件处理器将对产品的读取模型进行修改。

17. 事件处理审计微服务中的审计事件处理器也已订阅订单取消事件，因此它会收到订单取消的详细信息通知。

18. 根据订单取消事件中提到的详细信息，审计事件处理器将在审计的读取模型中添加一条新记录。

19. 浏览器可以自行刷新，通过查询实体的读取模型来保持视图更新。

编写示例 Saga 场景
演示简单 Axon 示例所需的完整代码位于
`ch15\ch15-01`。由于
此示例是第 12 章“分布式命令和事件处理”一节中示例的扩展，我将仅解释新添加或更改的代码，因此请参考该节以获取其余代码的解释。需要编写五个微服务，您将逐一查看它们。

微服务 1：01-Ecom-web
此微服务是一个典型的 Spring Boot Web 应用程序，不包含任何 Axon 组件，因此我将不再进一步讨论。

微服务 2：
02-Ecom-CreateCommandRestController

您将从订单控制器开始，该控制器处理用于创建新订单以及确认或取消订单的 HTTP 请求。请参见清单 15-1。

```
@RestController
public class OrderController {
@Autowired
private DataSource dataSource;
@Autowired
@Qualifier("distributedCommandGateway")
private CommandGateway commandGateway;
@RequestMapping(value = "/orders", method = RequestMethod.GET,
produces = { MediaType.APPLICATION_JSON_VALUE })
public ResponseEntity> getAllOrders() {
JdbcTemplate jdbcTemplate = new JdbcTemplate(dataSource);
List queryResult = jdbcTemplate.query(
"SELECT ∗ from ecom_order_view order by id", (rs, rowNum) ->
{return new OrderDTO(rs.getInt("id"), rs.getDouble("price"),
rs.getInt("number"), rs.getString("description"),
rs.getString("status"));
});
return new ResponseEntity(queryResult, HttpStatus.OK);
}
@RequestMapping(value = "/orders", method = RequestMethod.POST)
@Transactional
public ResponseEntity addNewOrder(@RequestBody OrderDTO orderDTO)
{
commandGateway.sendAndWait(new NewOrderCommand(orderDTO.getPrice(),
orderDTO.getNumber(), orderDTO.getProductId()));
return new ResponseEntity( HttpStatus.OK);
}
@RequestMapping(value = "/orders", method = RequestMethod.PUT)
@Transactional
public ResponseEntity updateOrder(@RequestBody OrderStatusUpdateDTO
orderStatusUpdateDTO) {
commandGateway.sendAndWait(
new OrderStatusUpdateCommand(orderStatusUpdateDTO.getOrderId(),
orderStatusUpdateDTO.getOrderStatus()));
return new ResponseEntity( HttpStatus.OK);
}
}
清单 15-1
订单控制器 (ch15\ch15-01\Ax2-Saga\02-Ecom-CreateCommandRestController\src\main\java\com\acme\ecom\web\controller\OrderController.java)
```

主要关注的方法有：

*   `addNewOrder`：这是一个 HTTP POST 方法，用于创建新订单。因此，它还会向分布式命令总线发送一个新订单命令。

*   `updateOrder`：此 HTTP PUT 方法处理订单的确认和取消。会触发一个订单状态更新命令来处理订单状态的更新。

其他主要的类是 `ProductController` 和
`OrderAuditController`；
两者都是简单的 REST 控制器，为各自的视图模型提供视图。

微服务 3：
03-Ecom-HandleCommandAndCreateEvent



该微服务需要处理由 `02-Ecom-CreateCommandRestController` 微服务创建、并通过分布式命令总线从远程 JVM 传输而来的所有命令。此外，该微服务还会创建事件，这些事件应由远程 JVM 或外部微服务中的分布式事件处理器消费。清单 15-2 首先展示了订单命令处理器。

```
@Component
public class OrderCommandHandler {
@Autowired
@Qualifier("orderRepository")
private Repository orderRepository;
@Autowired
@Qualifier("productRepository")
private Repository productRepository;
@CommandHandler
public void handleNewOrder(NewOrderCommand newOrderCommand){
Product product =
productRepository.load(newOrderCommand.getProductId());
product.depreciateStock(newOrderCommand.getNumber());
Integer id = new Random().nextInt();
Order order = new Order(id, newOrderCommand.getPrice(),
newOrderCommand.getNumber(), OrderStatusEnum.NEW, product);
orderRepository.add(order);
newOrderCommand.getNumber(), product.getId()});
}
@CommandHandler
public void handleUpdateOrder(
OrderStatusUpdateCommand orderStatusUpdateCommand){
Order order =
orderRepository.load(orderStatusUpdateCommand.getOrderId());
order.updateOrderStatus(orderStatusUpdateCommand.getOrderStatus());
orderStatusUpdateCommand.getOrderStatus()});
}
}
清单 15-2
订单命令处理器 (ch15\ch15-01\Ax2-Saga\03-Ecom-HandleCommandAndCreateEvent\src\main\java\com\acme\ecom\order\commandhandler\OrderCommandHandler.java)
```

接下来，清单 15-3 展示了产品命令处理器。

```
@Component
public class ProductCommandHandler {
@Autowired
@Qualifier("productRepository")
private Repository productRepository;
@CommandHandler
public void handleNewOrder(ProductStockRevertCommand
productStockRevertCommand){
Product product =
productRepository.load(productStockRevertCommand.getProductId());
product.revertStock(productStockRevertCommand.getCount());
}
}
清单 15-3
产品命令处理器 (ch15\ch15-01\Ax2-Saga\03-Ecom-HandleCommandAndCreateEvent\ src\main\java\com\acme\ecom\product\commandhandler\ProductCommandHandler.java)
```

订单命令处理器和产品命令处理器的作用不言自明。因此，接下来你将看到本例中下一个也是最重要的代码——Saga，如清单 15-4 所示。

```
public class OrderProcessSaga extends AbstractAnnotatedSaga {
private static final long serialVersionUID = -7209131793034337691L;
private Integer orderId;
private Integer productId;
private Integer count;
@Autowired
@Qualifier("distributedCommandGateway")
private transient CommandGateway commandGateway;
@StartSaga
@SagaEventHandler(associationProperty = "orderId")
public void handleOrderCreationEvent(OrderCreatedEvent orderCreatedEvent) {
orderId = orderCreatedEvent.getOrderId();
productId = orderCreatedEvent.getProductId();
count = orderCreatedEvent.getNumber();
}
@SagaEventHandler(associationProperty = "orderId")
@EndSaga
public void handleOrderCanceledEvent(
OrderCancelledEvent orderCancelledEvent) {
// 这是补偿命令
commandGateway.send(new ProductStockRevertCommand(productId, count));
}
@SagaEventHandler(associationProperty = "orderId")
@EndSaga
public void handleOrderConfirmationEvent(
OrderConfirmedEvent orderConfirmedEvent) {
// 无需操作
}
public CommandGateway getCommandGateway() {
return commandGateway;
}
public void setCommandGateway(CommandGateway commandGateway) {
this.commandGateway = commandGateway;
}
public Integer getProductId() {
return productId;
}
public void setProductId(Integer productId) {
this.productId = productId;
}
public Integer getCount() {
return count;
}
public void setCount(Integer count) {
this.count = count;
}
public Integer getOrderId() {
return orderId;
}
public void setOrderId(Integer orderId) {
this.orderId = orderId;
}
}
清单 15-4
订单处理 Saga (ch15\ch15-01\Ax2-Saga\03-Ecom-HandleCommandAndCreateEvent\src\main\java\com\acme\ecom\saga\OrderProcessSaga.java)
```

单个 Saga 实例负责管理单个 Saga 事务。因此，如果有两个订单，你将拥有上述 Saga 的两个实例。所以，只有在系统中找不到合适的现有 Saga（同一类型）时，才会启动一个新的 Saga。

你需要指明 Saga 生命周期的开始和结束。抽象注解 Saga 允许你使用 `@SagaEventHandler` 注解来标注事件处理器。如果处理器方法标志着事务的开始，你需要在该方法上添加另一个注解：`@StartSaga`。当发布匹配的事件时（在你的场景中是订单创建事件），该注解会创建一个新的 Saga 并调用其事件处理器方法。在你的场景中，有两种情况可以使 Saga 进入结束状态：当拦截到订单确认事件时，或者当拦截到订单取消事件时。如果某个特定事件始终标志着 Saga 生命周期的结束，请在 Saga 上为该事件的事件处理器标注 `@EndSaga`，在你的场景中，即处理订单取消事件和处理订单确认事件的事件处理器。Saga 的生命周期将在处理器调用结束后终止。

Saga 事件处理器注解表明被注解的方法是 Saga 实例的事件处理器方法。对于每个事件，只会调用一个被注解的方法。该方法按以下优先级顺序解析：

*   首先，搜索实际类（运行时）的事件处理器方法。
*   如果找到某个方法的参数可以分配给领域事件，则将其标记为符合条件。
*   在评估完一个类之后（但在任何父类之前），将调用最具体的事件处理器方法。这意味着，如果类 X 的事件处理器和类 Y 的事件处理器都符合条件，并且 Y 是 X 的子类，那么将选择参数类型为 Y 的方法。
*   如果在实际类中未找到任何方法，则评估其父类。
*   如果仍未找到任何方法，则事件监听器将忽略该事件。



Axon 不会将全部事件发布给系统中所有的 Saga 实例，而是仅发布包含 Saga 已关联属性的那些事件。这是通过关联值（association values）来实现的。一个关联值由一个键（key）和一个值（value）组成。键代表所使用的标识符类型，例如上面代码中的 `orderId`。值则代表订单 ID 的对应值。`@SagaEventHandler` 注解有两个属性，其中 `associationProperty` 是最重要的一个。这是传入事件上用于查找关联 Saga 的属性名称。关联值的键就是该属性的名称，值则是该属性 getter 方法返回的值。

微服务 4：04-Ecom-EventHandleCore

正如该微服务的名称所暗示，它包含两个主要的事件处理器：`OrderEventHandler` 和 `ProductEventHandler`。它们的功能是响应以下事件，分别更新订单（Order）和产品（Product）的视图：

*   `OrderCreatedEvent`

*   `OrderConfirmedEvent`

*   `OrderCancelledEvent`

*   `StockUpdatedEvent`

清单 15-5 展示了更新订单视图的订单事件处理器代码。

```
@Component
public class OrderEventHandler {
@Autowired
DataSource dataSource;
@EventHandler
public void handleOrderCreatedEvent(OrderCreatedEvent event) {
JdbcTemplate jdbcTemplate = new JdbcTemplate(dataSource);
jdbcTemplate.update("INSERT INTO ecom_order_view VALUES(?,?,?,?,?)",
new Object[]{event.getOrderId(), event.getPrice(),
event.getNumber(), event.getProductDescription(), NEW});
}
@EventHandler
public void handleOrderConfirmedEvent(OrderConfirmedEvent event) {
JdbcTemplate jdbcTemplate = new JdbcTemplate(dataSource);
jdbcTemplate.update("UPDATE ecom_order_view SET status=?
WHERE id =?", new Object[]{CONFIRMED, event.getOrderId()});
}
@EventHandler
public void handleOrderCancelledEvent(OrderCancelledEvent event) {
JdbcTemplate jdbcTemplate = new JdbcTemplate(dataSource);
jdbcTemplate.update("UPDATE ecom_order_view SET status=?
WHERE id =?", new Object[]{CANCELLED, event.getOrderId()});
}
}
清单 15-5
订单事件处理器 (ch15\ch15-01\Ax2-Saga\04-Ecom-EventHandleCore\src\main\java\com\acme\ecom\order\eventhandler\OrderEventHandler.java)
```

清单 15-6 展示了更新产品视图的产品事件处理器代码。

```
@Component
public class ProductEventHandler {
@Autowired
DataSource dataSource;
@EventHandler
public void handleProductStockUpdatedEvent(StockUpdatedEvent event) {
JdbcTemplate jdbcTemplate = new JdbcTemplate(dataSource);
jdbcTemplate.update("UPDATE ecom_product_view SET stock=?
WHERE ID=?", new Object[]{event.getStock(), event.getId()});
}
}
清单 15-6
产品事件处理器 (ch15\ch15-01\Ax2-Saga\04-Ecom-EventHandleCore\src\main\java\com\acme\ecom\product\eventhandler\ProductEventHandler.java)
```

微服务 5：05-Ecom-EventHandlerAudit

该微服务对针对领域实体发生的所有主要事件进行审计记录。清单 15-7 展示了审计事件处理器。

```
@Component
public class AuditEventHandler {
@Autowired
DataSource dataSource;
@EventHandler
public void handleOrderCreatedEvent(OrderCreatedEvent event) {
JdbcTemplate jdbcTemplate = new JdbcTemplate(dataSource);
jdbcTemplate.update("INSERT INTO ecom_order_audit VALUES(?,?,?)",
new Object[]{event.getOrderId(), event.getOrderStatus(),
new Date()});
}
@EventHandler
public void handleOrderConfirmedEvent(OrderConfirmedEvent event) {
JdbcTemplate jdbcTemplate = new JdbcTemplate(dataSource);
jdbcTemplate.update("INSERT INTO ecom_order_audit VALUES(?,?,?)",
new Object[]{event.getOrderId(), CONFIRMED, new Date()});
}
@EventHandler
public void handleOrderCancelledEvent(OrderCancelledEvent event) {
JdbcTemplate jdbcTemplate = new JdbcTemplate(dataSource);
jdbcTemplate.update("INSERT INTO ecom_order_audit VALUES(?,?,?)",
new Object[]{event.getOrderId(), CANCELLED, new Date()});
}
}
清单 15-7
审计事件处理器 (ch15\ch15-01\Ax2-Saga\05-Ecom-EventHandlerAudit\src\main\java\com\acme\ecom\order\eventhandler\AuditEventHandler.java)
```

审计事件处理器会创建审计行，并在订单状态发生变化时更新它们。以上就是所有主要的类。还有一些其他的类，比如 `Command`、`Event` 和 `Data Transfer` 类，它们都很简单直接，因此这里不再赘述。

构建并测试 Saga 示例

第一步，你需要启动 MongoDB。你可能需要参考附录 A 来开始使用 MongoDB。

```
D:\Applns\MongoDB\Server\3.2.6\bin>mongod.exe --dbpath D:\Applns\MongoDB\Server\3.2.6\data
```

接下来，你需要启动 RabbitMQ 服务器。你可能需要参考附录 B 来开始使用 RabbitMQ 服务器。

```
D:\Applns\RabbitMQ\rabbitmq_server-3.6.3\sbin>D:\Applns\RabbitMQ\rabbitmq_server-3.6.3\sbin\rabbitmq-server.bat
```

确保 MySQL 已启动并正在运行。你可能需要参考附录 H 来开始使用 MySQL。

```
启动 MySQL 服务器
cd D:\Applns\MySQL\mysql-8.0.14-winx64\bin
D:\Applns\MySQL\mysql-8.0.14-winx64\bin>mysqld --console
现在打开 MySQL 提示符
D:\Applns\MySQL\mysql-8.0.14-winx64\bin>mysql -u root -p
mysql> use ecom01;
数据库已更改
mysql>
```

为了从干净的表格开始，请删除示例中你想要使用的任何名称的表格：

```
mysql> drop table ecom_order;
mysql> drop table ecom_product;
mysql> drop table ecom_order_view;
mysql> drop table ecom_product_view;
mysql> drop table ecom_order_audit;
```

重新创建所需的表格：

```
mysql>create table ecom_order (id integer not null, last_event_sequence_number bigint, version bigint, number integer, order_status varchar(255), price double precision, product_id integer, primary key (id)) ENGINE=InnoDB
mysql>create table ecom_product (id integer not null, last_event_sequence_number bigint, version bigint, description varchar(255), price double precision, stock integer, primary key (id)) ENGINE=InnoDB
mysql>alter table ecom_order add constraint FK_f3rnd79i90twafllfhpo1sihi foreign key (product_id) references ecom_product (id)
mysql>create table ecom_product_view(id INT , price DOUBLE, stock INT ,description VARCHAR(255));
mysql>create table ecom_order_view(id INT , price DOUBLE, number INT ,description VARCHAR(225),status VARCHAR(50));
mysql>create table ecom_order_audit(id INT ,status VARCHAR(50),date TIMESTAMP);
```

该示例有一个 Maven 模块，其中包含所有其他微服务使用的公共类。因此，必须首先构建它：

```
cd D:\binil\gold\pack03\ch15\ch15-01\Ax2-Saga\06-Ecom-common
D:\binil\gold\pack03\ch15\ch15-01\Ax2-Saga\06-Ecom-common>make
D:\binil\gold\pack03\ch15\ch15-01\Ax2-Saga\06-Ecom-common>mvn clean install
```

接下来，有五个微服务需要构建并运行。你将逐一完成。

微服务 1：01-Ecom-web

首先，更新配置文件以适配你的环境：

```
ch15\ch15-01\Ax2-Saga\01-Ecom-web\src\main\resources\application.properties
server.port=8080
```

注意
我建议你不要在此处做任何更改。



现在构建并打包 Ecom-web 微服务的可执行文件，然后启动服务器。在文件夹 `ch15\ch15-01\Ax2-Saga\01-Ecom-web\make.bat` 中有一个实用脚本。

```
cd D:\binil\gold\pack03\ch15\ch15-01\Ax2-Saga\01-Ecom-web
D:\binil\gold\pack03\ch15\ch15-01\Ax2-Saga\01-Ecom-web>make
D:\binil\gold\pack03\ch15\ch15-01\Ax2-Saga\01-Ecom-web>mvn clean install
```

现在运行 Spring Boot 应用程序。最直接的方式是通过以下命令执行 JAR 文件：

```
D:\binil\gold\pack03\ch15\ch15-01\Ax2-Saga\01-Ecom-web>run
D:\binil\gold\pack03\ch15\ch15-01\Ax2-Saga\01-Ecom-web>java -jar .\target\01-Ecom-web-0.0.1-SNAPSHOT.jar
```

这将在 8080 端口启动 01-Ecom-web Spring Boot 服务器。

微服务 2：
02-Ecom-CreateCommandRestController

JGroups 配置在以下文件中提供：

```
ch15\ch15-01\Ax2-Saga\02-Ecom-CreateCommandRestController\src\main\resources\udp_config.xml
```

不过，我们现在不必过多关注这个文件的内容。

更新下一个配置文件以适配你的环境：

```
ch15\ch15-01\Ax2-Saga\02-Ecom-CreateCommandRestController\src\main\resources\application.properties
server.port=8081
spring.datasource.url=jdbc:mysql://localhost/ecom01
spring.datasource.username=root
spring.datasource.password=rootpassword
cd D:\binil\gold\pack03\ch15\ch15-01\Ax2-Saga\02-Ecom-CreateCommandRestController
D:\binil\gold\pack03\ch15\ch15-01\Ax2-Saga\02-Ecom-CreateCommandRestController>make
D:\binil\gold\pack03\ch15\ch15-01\Ax2-Saga\02-Ecom-CreateCommandRestController>mvn clean install
```

现在你可以运行 02-Ecom-CreateCommandRestController Axon Spring Boot 应用程序，同样有多种方式。最直接的方式是通过以下命令执行 JAR 文件：

```
D:\binil\gold\pack03\ch15\ch15-01\Ax2-Saga\02-Ecom-CreateCommandRestController>run
D:\binil\gold\pack03\ch15\ch15-01\Ax2-Saga\02-Ecom-CreateCommandRestController>java -Dserver.port=8081 -Dlog4j.configurationFile=log4j2-spring.xml -jar .\target\02-Ecom-CreateCommandRestController-0.0.1-SNAPSHOT.jar
```

这将在 8081 端口启动 02-Ecom-CreateCommandRestController Axon Spring Boot 服务器。

微服务 3：
03-Ecom-HandleCommandAndCreateEvent

更新配置文件以适配你的环境，参见清单 15-8：

```
spring.data.mongodb.uri=mongodb://localhost:27017/test
server.port=8082
spring.datasource.url=jdbc:mysql://localhost/ecom01
spring.datasource.username=root
spring.datasource.password=rootpassword
ecom.amqp.rabbit.address= 127.0.0.1:5672
ecom.amqp.rabbit.username= guest
ecom.amqp.rabbit.password= guest
ecom.amqp.rabbit.vhost=/
ecom.amqp.rabbit.exchange=Ecom-02
ecom.amqp.rabbit.queue=Ecom-createcommand_01
清单 15-8
微服务 3 配置参数
```

```
ch12\ch12-02\Ax2-Commands-Multi-Event-Handler-Distributed\03-Ecom-HandleCommandAndCreateEvent\src\main\resources\application.properties
```

注意
MongoDB URL 被微服务用于持久化 saga。即使你提供了 `test` 数据库的完整路径，微服务也只会在一个名为 `axonframework` 的不同数据库中创建所有与 saga 相关的集合。

```
cd D:\binil\gold\pack03\ch15\ch15-01\Ax2-Saga\03-Ecom-HandleCommandAndCreateEvent
D:\binil\gold\pack03\ch15\ch15-01\Ax2-Saga\03-Ecom-HandleCommandAndCreateEvent>make
D:\binil\gold\pack03\ch15\ch15-01\Ax2-Saga\03-Ecom-HandleCommandAndCreateEvent>mvn clean install
```

你可以运行 03-Ecom-HandleCommandAndCreateEvent Axon Spring Boot 应用程序，同样有多种方式。最直接的方式是通过以下命令执行 JAR 文件：

```
D:\binil\gold\pack03\ch15\ch15-01\Ax2-Saga\03-Ecom-HandleCommandAndCreateEvent>run
D:\binil\gold\pack03\ch15\ch15-01\Ax2-Saga\03-Ecom-HandleCommandAndCreateEvent>java -Dserver.port=8082 -Dlog4j.configurationFile=log4j2-spring.xml -jar .\target\03-Ecom-HandleCommandAndSaga-0.0.1-SNAPSHOT.jar
```

这将在 8082 端口启动 02-Ecom-CreateCommandRestController Axon Spring Boot 服务器。

微服务 4：04-Ecom-EventHandleCore

更新配置文件以适配你的环境，参见清单 15-9：

```
server.port=8083
spring.datasource.url=jdbc:mysql://localhost/ecom01
spring.datasource.username=root
spring.datasource.password=rootpassword
ecom.amqp.rabbit.address= 127.0.0.1:5672
ecom.amqp.rabbit.username= guest
ecom.amqp.rabbit.password= guest
ecom.amqp.rabbit.vhost=/
ecom.amqp.rabbit.exchange=Ecom-02
ecom.amqp.rabbit.queue=Ecom-event-core_01
cd D:\binil\gold\pack03\ch15\ch15-01\Ax2-Saga\04-Ecom-EventHandleCore
D:\binil\gold\pack03\ch15\ch15-01\Ax2-Saga\04-Ecom-EventHandleCore>make
D:\binil\gold\pack03\ch15\ch15-01\Ax2-Saga\04-Ecom-EventHandleCore>mvn clean install
清单 15-9
微服务 4 配置参数
```

```
ch15\ch15-01\Ax2-Saga\04-Ecom-EventHandleCore\src\main\resources\application.properties
```

现在你可以运行 04-Ecom-EventHandleCore Axon Spring Boot 应用程序。最直接的方式是通过以下命令执行 JAR 文件：

```
D:\binil\gold\pack03\ch15\ch15-01\Ax2-Saga\04-Ecom-EventHandleCore>run
D:\binil\gold\pack03\ch15\ch15-01\Ax2-Saga\04-Ecom-EventHandleCore>java -Dserver.port=8083 -jar .\target\04-Ecom-EventHandlerCore-0.0.1-SNAPSHOT.jar
```

这将在 8083 端口启动 04-Ecom-EventHandleCore Axon Spring Boot 服务器。

微服务 5：05-Ecom-EventHandlerAudit

这是本例中你要启动的最后一个微服务。更新配置文件以适配你的环境，参见清单 15-10：

```
server.port=8084
spring.datasource.url=jdbc:mysql://localhost/ecom01
spring.datasource.username=root
spring.datasource.password=rootpassword
ecom.amqp.rabbit.address= 127.0.0.1:5672
ecom.amqp.rabbit.username= guest
ecom.amqp.rabbit.password= guest
ecom.amqp.rabbit.vhost=/
ecom.amqp.rabbit.exchange=Ecom-02
ecom.amqp.rabbit.queue=Ecom-event-history_01
cd D:\binil\gold\pack03\ch15\ch15-01\Ax2-Saga\05-Ecom-EventHandlerAudit
D:\binil\gold\pack03\ch15\ch15-01\Ax2-Saga\05-Ecom-EventHandlerAudit>make
D:\binil\gold\pack03\ch15\ch15-01\Ax2-Saga\05-Ecom-EventHandlerAudit>mvn clean install
清单 15-10
微服务 5 配置参数
```

```
ch15\ch15-01\Ax2-Saga\05-Ecom-EventHandlerAudit\src\main\resources\application.properties
```

现在你可以运行 05-Ecom-EventHandlerAudit Axon Spring Boot 应用程序，同样有多种方式。最直接的方式是通过以下命令执行 JAR 文件：

```
D:\binil\gold\pack03\ch15\ch15-01\Ax2-Saga\05-Ecom-EventHandlerAudit>run
D:\binil\gold\pack03\ch15\ch15-01\Ax2-Saga\05-Ecom-EventHandlerAudit>java -Dserver.port=8084 -Dspring.application.name=product-audit-01 -jar .\target\05-Ecom-EventHandlerHistory-0.0.1-SNAPSHOT.jar
```

这将在 8084 端口启动 05-Ecom-EventHandlerAudit Axon Spring Boot 服务器。

用一些初始数据预填充 `ecom_product` 和 `ecom_product_view` 表：

```
mysql> insert into ecom_product(id,description,price,stock,version) values(1,'Shirts',100,5,0);
mysql> insert into ecom_product(id,description,price,stock,version) values(2,'Pants',100,5,0);
mysql> insert into ecom_product(id,description,price,stock,version) values(3,'T-Shirt',100,5,0);
mysql> insert into ecom_product(id,description,price,stock,version) values(4,'Shoes',100,5,0);
mysql> insert into ecom_product_view(id,description,price,stock) values(1,'Shirts',100,5);
mysql> insert into ecom_product_view(id,description,price,stock) values(2,'Pants',100,5);
mysql> insert into ecom_product_view(id,description,price,stock) values(3,'T-Shirt',100,5);
mysql> insert into ecom_product_view(id,description,price,stock) values(4,'Shoes',100,5);
```



您可以使用浏览器（推荐 Chrome）并访问 `http://localhost:8080/`。
这将显示如图 15-9 所示的界面。您可以通过点击图 15-9 中任意产品的“立即下单”按钮来测试示例。点击后，订单写入数据库中会创建一个新订单。最终，订单读取数据库也会更新此新订单。同时，产品写入数据库中相应产品的库存会减少一件。此变更最终也会更新到产品读取数据库中。审计读取数据库也会更新记录。当您刷新界面时，变更结果如图 15-11 所示。

当您创建第一个订单时，一个 Saga 流程便会启动。要验证这一点，您可以使用控制台检查 MongoDB 中的集合，如代码清单 15-11 所示。

```
D:\Applns\MongoDB\Server\3.2.6\bin>D:\Applns\MongoDB\Server\3.2.6\bin\mongo
MongoDB shell version: 3.2.6
connecting to: test
> show dbs
ecom   0.000GB
local  0.000GB
test   0.000GB
> show dbs
axonframework  0.000GB
ecom           0.000GB
local          0.000GB
test           0.000GB
> use axonframework
switched to db axonframework
> show collections
sagas
> db.sagas.find()
{ "_id" : ObjectId("5c7f689a75c90e3adeb78318"), "sagaType" : "com.acme.ecom.saga.OrderProcessSaga", "sagaIdentifier" : "3aeffc45-4ddd-419b-ab00-d2e32914c9ce", "serializedSaga" : BinData(0,"rO0ABXNyACNjb20uYWNtZS5lY29tLnNhZ2EuT3JkZXJQcm9jZXNzU2FnYZv0BNRxNqJlAgADTAAFY291bnR0ABNMamF2YS9sYW5nL0ludGVnZXI7TAAHb3JkZXJJZHEAfgABTAAJcHJvZHVjdElkcQB+AAF4cgA3b3JnLmF4b25mcmFtZXdvcmsuc2FnYS5hbm5vdGF0aW9uLkFic3RyYWN0QW5ub3RhdGVkU2FnYSEZTtg8hhaCAgADWgAIaXNBY3RpdmVMABFhc3NvY2lhdGlvblZhbHVlc3QAKkxvcmcvYXhvbmZyYW1ld29yay9zYWdhL0Fzc29jaWF0aW9uVmFsdWVzO0wACmlkZW50aWZpZXJ0ABJMamF2YS9sYW5nL1N0cmluZzt4cAFzcgA3b3JnLmF4b25mcmFtZXdvcmsuc2FnYS5hbm5vdGF0aW9uLkFzc29jaWF0aW9uVmFsdWVzSW1wbHLSJtX04PrCAgABTAAGdmFsdWVzdAAPTGphdmEvdXRpbC9TZXQ7eHBzcgAoamF2YS51dGlsLmNvbmN1cnJlbnQuQ29weU9uV3JpdGVBcnJheVNldEu90JKQFWnXAgABTAACYWx0ACtMamF2YS91dGlsL2NvbmN1cnJlbnQvQ29weU9uV3JpdGVBcnJheUxpc3Q7eHBzcgApamF2YS51dGlsLmNvbmN1cnJlbnQuQ29weU9uV3JpdGVBcnJheUxpc3R4XZ/VRquQwwMAAHhwdwQAAAACc3IAJ29yZy5heG9uZnJhbWV3b3JrLnNhZ2EuQXNzb2NpYXRpb25WYWx1ZTGYTDwHQAy9AgACTAALcHJvcGVydHlLZXlxAH4ABEwADXByb3BlcnR5VmFsdWVxAH4ABHhwdAAOc2FnYUlkZW50aWZpZXJ0ACQzYWVmZmM0NS00ZGRkLTQxOWItYWIwMC1kMmUzMjkxNGM5Y2VzcQB+AA50AAdvcmRlcklkdAAJNTEwMzc0MTg1eHEAfgARc3IAEWphdmEubGFuZy5JbnRlZ2VyEuKgpPeBhzgCAAFJAAV2YWx1ZXhyABBqYXZhLmxhbmcuTnVtYmVyhqyVHQuU4IsCAAB4cAAAAAFzcQB+ABUea7Epc3EAfgAVAAAAAQ=="), "associations" : [ { "key": "sagaIdentifier", "value" : "3aeffc45-4ddd-419b-ab00-d2e32914c9ce" }, { "key": "orderId", "value" : "510374185" } ] }
代码清单 15-11
持久化在 MongoDB 集合中的 Saga
```

> **注意**
> 如果微服务未按预期方式响应，您可能需要清空 RabbitMQ 队列。这可以通过关闭 RabbitMQ、删除数据文件夹，然后重新启动 RabbitMQ 轻松完成。相关详情请参阅附录 B。

（如果变更未生效，请关闭浏览器会话，打开一个新的浏览器实例，然后重试上述 URL。）

再创建两个订单，以便执行所有测试用例。刷新界面后，您将看到所有新订单以及订单的审计日志，如图 15-14 所示。

![../images/477630_1_En_15_Chapter/477630_1_En_15_Fig14_HTML.jpg](img/477630_1_En_15_Fig14_HTML.jpg)

图 15-14
创建了三个新订单

如果您点击某个订单的“取消”按钮，该订单将被取消，并且 Saga 流程结束。不仅如此，由于订单被取消，对应产品的库存数量会加回一件。取消操作会生成自己的审计日志。再次刷新界面，您将看到如图 15-15 所示的变更。

![../images/477630_1_En_15_Chapter/477630_1_En_15_Fig15_HTML.jpg](img/477630_1_En_15_Fig15_HTML.jpg)

图 15-15
订单已取消

如果您点击某个订单的“确认”按钮，该订单将被确认，并且 Saga 流程再次结束。由于订单已确认，您在订购时减少的产品库存将保持不变。确认操作会生成自己的审计日志。再次刷新界面，您将看到如图 15-16 所示的变更。

![../images/477630_1_En_15_Chapter/477630_1_En_15_Fig16_HTML.jpg](img/477630_1_En_15_Fig16_HTML.jpg)

图 15-16
订单已确认

最后，请注意状态仍为“新建”的订单。如果您对此订单不进行任何进一步操作，它将保持该状态，这是由于 Saga 的持久性特性，它也将保持该状态。这演示了一个长时间运行的工作流，在您的案例中即 Saga。

**总结**
虽然第 13 章和第 14 章详细介绍了事务，但本章专门在微服务的上下文中介绍了事务。您学习了 Saga，以及如何通过将给定事务分组为一系列子事务和相应的补偿事务来利用分布式 Saga。Saga 中的所有事务要么全部成功完成，要么在发生故障时回滚补偿事务。您还看到了如何使用 Axon 2 框架实现 Saga 的完整工作代码。在了解了事务之后，接下来您将在第 16 章中重新审视高可用性和可扩展性，重点放在 CQRS 设计上。

**脚注**

16. **高级高可用性与可扩展性**

在第 9 章中，您了解了软件架构中影响高可用性（HA）的各种要素，其中大部分要素既适用于微服务架构，也适用于单体架构。在那里，您了解了私有云或本地部署基础设施的各种组件，并简要了解了 HA 的各个方面。在本章中，您将深入探讨对设计基于 CQRS 的系统有重大影响的特定关注点。为了使内容简单易懂并与您已知或正在使用的概念相关联，我将在大多数人都熟悉的 Oracle 数据库的上下文中审视这些关注点。一旦您理解了这些概念，就很容易将这些知识扩展到任何其他供应商的解决方案或其他免费开源解决方案。有趣的是，您将在 Oracle 的上下文中审视许多这些概念，以确保仅在绝对必要时才利用此类解决方案，这再次引出了一个核心观点：在许多情况下，存在替代且可能更便宜的解决方案。

您将在本章中探讨以下内容：

*   用于解决日益增长的可扩展性需求的架构模板
*   使用 Axon 2 CQRS 演示 HA 和可扩展性设计的代码示例
*   读取可扩展性与写入可扩展性
*   允许实体并发修改的技术
*   使用 Axon 2 CQRS 演示实体并发修改的代码示例



以 Oracle 数据库为参考的高可用性与可扩展性模板

如前所述，您将使用 Oracle 数据库作为解决方案来阐释各种高可用性（HA）模板。在此过程中，您还将使用公有云，即您案例中的亚马逊云服务（AWS），作为部署环境。这是因为，您很快就会发现，这些模板存在一些注意事项，我将在涉及公有云时进行说明。您将把应用架构视为一个生命周期，该周期的每个阶段都代表着从初始到架构成熟并实现规模经济与效率运营，再到需要以指数级或无限级进行网络规模扩展的各个成长阶段。

简单初始架构

这是任何分布式或微服务架构的最简单形式，仅适用于为个人使用或小型初创企业提供应用服务。其基本形式由一个提供应用服务的中间层组成，该中间层连接到一个单一的、简单的数据库实例以提供持久化服务。图 16-1 展示了其在公有云亚马逊云服务中部署时的样貌。

![../images/477630_1_En_16_Chapter/477630_1_En_16_Fig1_HTML.jpg](img/477630_1_En_16_Fig1_HTML.jpg)

图 16-1
简单初始架构

这里有一个中间层或微服务的单一实例，以及一个数据库服务器的单一实例。此架构的可扩展性由底层硬件容量决定，在您的案例中，这取决于所选 EC2 实例的等级。亚马逊弹性计算云（Amazon EC2）是一项在云中提供安全、可调整计算容量的 Web 服务。它与大多数 AWS 服务（如亚马逊简单存储服务（Amazon S3）、亚马逊关系数据库服务（Amazon RDS）和亚马逊虚拟私有云（Amazon VPC））集成，为跨广泛应用程序的计算、查询处理和云存储提供基础设施解决方案。

随着与应用交互的用户数量增长，或者当此架构需要服务的并发事务数量增加时，横向扩展的能力完全取决于您可选择的可用 EC2 实例的容量。这是有限的，并且这种可扩展性限制是此类架构设计方法固有的。

关于图 16-1，有几个组件需要快速介绍，因为它们将在后续讨论中被引用：

*   **虚拟私有云（VPC）**：一个逻辑隔离的虚拟网络，跨越整个 AWS 区域，您的 EC2 实例在其中启动。VPC 主要涉及实现以下功能：

    *   将您的 AWS 资源与其他账户的资源隔离
    *   路由进出您的实例的网络流量
    *   保护您的实例免受网络入侵和其他漏洞攻击

*   **AWS 区域和可用区**：AWS 云基础设施围绕区域和可用区（AZ）构建。AWS 区域提供多个物理上分离且隔离的可用区，这些可用区通过低延迟、高吞吐量和高度冗余的网络连接。这些可用区提供了比传统单数据中心基础设施或多数据中心基础设施更高度可用、容错且可扩展的基础设施。AWS 区域与其他 AWS 区域完全隔离。AWS 云目前在全球 18 个地理区域和一个本地区域内拥有 54 个可用区。图 16-1 中描绘了一个 AWS 区域，而可用区将在下一节相关部分中展示。

简单横向扩展架构

上一节描述的简单初始架构是最低限度的，也是麻烦最少的。如果情况允许，您可能希望采用这种极简架构，因为它能让您省心。然而，如果您的业务在此架构下取得成功，这在某种意义上意味着您将需要一个增强型架构来实现可扩展性，因为不断增长的业务绝不可能由上述极简架构来服务。简单横向扩展架构如图 16-2 所示。

![../images/477630_1_En_16_Chapter/477630_1_En_16_Fig2_HTML.jpg](img/477630_1_En_16_Fig2_HTML.jpg)

图 16-2
简单横向扩展架构



这种简单的横向扩展架构其实并不简单，但与你接下来要看到的架构相比，它确实更简单，因此得名。简单的横向扩展架构利用了区域、可用区和自动扩展组。每个区域是 AWS 云中的一个独立地理区域。每个区域包含多个隔离的位置，称为可用区。

Amazon EC2 提供了将实例和数据等资源部署到多个位置的能力。如果你将所有实例都托管在单个位置，一旦该位置受到严重影响，所有实例都将不可用。AWS 区域和可用区在此处能为你提供帮助。每个区域完全独立。每个可用区也是隔离的，但同一区域内的可用区通过低延迟链路相互连接。当你启动一个 EC2 实例时，你可以选择一个可用区，或者让 AWS 为你选择。如果你将 EC2 实例分布到多个可用区，并且其中一个实例发生故障，同时你的应用程序设计得当，那么另一个可用区中的实例仍然可以处理请求。

自动扩展可确保你有适当数量的 EC2 实例来应对应用程序的负载。你可以创建 EC2 实例的集合，称为自动扩展组。你可以为自动扩展组指定最小实例数，自动扩展会确保你的组永远不会低于此规模。你还可以指定每个自动扩展组中的最大实例数，自动扩展会确保你的组永远不会超过此规模。当你指定所需容量时，自动扩展会确保你的组在启动时拥有这么多实例。如果你指定了扩展策略，那么自动扩展可以根据应用程序的需求动态启动或终止实例。

图 16-1 中的微服务实例必须进行复制以提高可扩展性。回顾图 16-2，同一个微服务的多个实例可以在一个可用区中实例化。为了应对某个可用区宕机的情况，可以在另一个可用区中实例化更多同一微服务的实例。为了处理微服务的动态扩展，你可以设计一个自动扩展组。这个自动扩展组可以跨可用区，这样你既能获得动态可扩展性，也能实现区域冗余。

当你的计算节点像上面那样分布时，你还需要一种机制来分配你的工作负载，而弹性负载均衡器 (ELB) 可以自动将传入的应用程序流量分配到多个目标，例如 Amazon EC2 实例、容器和 IP 地址。ELB 可以处理单个可用区或跨多个可用区的应用程序流量的变化负载。ELB 有三种类型：

*   应用程序负载均衡器：ALB 最适合对 HTTP 和 HTTPS 流量进行负载均衡，在 OSI 第 7 层（应用层）的单个请求级别运行。
*   网络负载均衡器：NLB 对需要极致性能的 TCP 流量进行负载均衡，在 OSI 第 4 层（传输层）的连接级别运行。
*   传统负载均衡器：CLB 适用于在 EC2-Classic 网络中构建的应用程序。它提供跨多个 Amazon EC2 实例的基本负载均衡，并在请求级别和连接级别运行。

另一个需要讨论的方面是全局分发数据的需求。这可能有多种原因。一个原因是用户群分布在全球各地，因此用户应被引导到靠近其地理位置的微服务实例，以获得低延迟。另一个原因可能是为了遵守数据隐私要求，例如 GDPR^(⁴⁰)（通用数据保护条例）。当你希望利用跨区域的微服务实例时，可以在此处利用 AWS 区域。这在图 16-2 中也以非展开形式进行了描绘。可以使用 AWS Route 53 进行区域流量路由或基于延迟的流量路由。Amazon Route 53 是一种高可用且可扩展的云域名系统 (DNS) Web 服务。Route 53 使你可以通过多种路由类型轻松地全局管理流量，包括基于延迟的路由、地理 DNS、地理邻近路由和加权轮询——所有这些都可以与 DNS 故障转移结合使用。

图 16-2 中的简单横向扩展架构是对图 16-1 中简单初始架构的改进；然而，仔细观察图 16-2 会发现其他问题和限制。首先，跨区域的数据是分散的；它们不会自动同步。也许这正符合迫使你将应用程序分布到不同区域的原因，例如隐私。但是，让我们专注于一个区域内部。即使你可以设计拥有许多实例的微服务，你的后端数据存储仍然是一个单一实例，这是你的单一事实来源。这个单一的数据库实例仍然会存在前面讨论过的可扩展性问题。你需要进一步改进，这将在下一部分中看到。

解决数据库瓶颈的架构
你希望对简单横向扩展架构进行的改进也需要解决数据库实例的可扩展性问题。Oracle 真正应用集群 (RAC) 是一种具有共享缓存架构的集群数据库，它克服了传统无共享和共享磁盘方法的局限性，为数据库提供了高可扩展性和高可用性。在 RAC 环境中，数据库本身在服务器池中共享，这意味着如果服务器池中的任何服务器发生故障，数据库将继续在幸存的服务器上运行，从而避免了任何单点故障。由于存在一个服务器池，集群数据库服务器的组合内存容量和处理能力也提供了高可扩展性。RAC 是一种具有共享数据库存储的主动-主动分布式架构。共享存储在实现自动故障转移、无数据丢失、100% 数据一致性以及防止应用程序停机方面发挥着核心作用。

在像 AWS 这样的公有云中，存在一个问题。截至撰写本文时，Amazon Web Services 目前不支持在 Amazon EC2 或 Amazon RDS 上原生运行 Oracle RAC。Oracle RAC 有以下基础设施要求，而这些要求在 AWS 中无法直接获得：

*   集群中所有节点均可访问的共享高性能存储
*   集群中所有节点之间支持多播的网络
*   用于不同类型流量的独立网络：客户端、集群互连和存储

但好消息是，有第三方提供商提供的解决方案允许你使用 AWS 原生组件（例如 Amazon EC2、Amazon Elastic Block Store (Amazon EBS) 和 Amazon VPC）在 AWS 上运行 Oracle RAC。图 16-3 描绘了一种可能的架构方法，该方法利用 Oracle RAC 来改进简单的横向扩展架构。

![../images/477630_1_En_16_Chapter/477630_1_En_16_Fig3_HTML.jpg](img/477630_1_En_16_Fig3_HTML.jpg)



图 16-3  
解决数据库瓶颈的架构  

参考图 16-3 所示的架构，建议采用两节点或三节点集群以提高可靠性。四节点及以上的集群也可用于实现额外的高可用性或性能提升。包含多个两节点或三节点数据库集群的四节点及以上网格基础架构集群也是可行的。集群中的节点可以位于同一个可用区，也可以跨可用区分布。  

大多数 AWS 区域仅限三个可用区。图 16-3 展示了三个 RAC 节点。将仲裁节点与 RAC 节点部署在同一可用区，仍能实现大部分预期的高可用性功能。这种三节点集群能够容忍任意两个节点或任意一个可用区的故障，而不会导致数据库停机。然而，若同时丢失两个可用区，则会导致数据库停机。在采用本地 NVMe SSD^(⁴¹)而非 EBS（弹性块存储）卷的配置中，可使用高冗余 ASM^(⁴²)（自动存储管理）磁盘组来提供额外的数据保护层。此时，第三个节点将配置为配备 NVMe SSD 或 EBS 卷的存储节点，而非仲裁节点。将实例部署在不同可用区可消除节点同时故障的风险，除非发生影响区域内多个数据中心设施的罕见灾难事件。  

完成此类集群搭建后，下一步是将负载分发至 RAC 集群。该架构利用了 Oracle Clusterware、ASM 和数据库内置的高可用性功能。如图 16-3 所示，来自可用区内微服务实例的数据库调用，也可路由至跨可用区的数据库实例池。其权衡在于节点间网络延迟较高且网络带宽相对较低。是否还有进一步优化的空间？  

**独立读写扩展性以提升效率**  

在上一节基于 RAC 的架构中，我讨论了如何通过跨多个可用区扩展 RAC 来增加冗余。当您这样做时，还需要在架构上管理节点间较高的网络延迟和相对较低的带宽。图 16-4 所示的架构变体提供了其中一种方案。  

![../images/477630_1_En_16_Chapter/477630_1_En_16_Fig4_HTML.jpg](img/477630_1_En_16_Fig4_HTML.jpg)  

图 16-4  
提升读取扩展性的数据架构  

在许多场景中，读取请求或查询的数量远超应用程序需要处理的总写入请求数。您已在第 12 章探讨了“浏览与预订比率”这一概念。图 16-4 中的架构选项采用了主数据库节点和辅助（备用）数据库节点的概念。它利用 Oracle Data Guard 复制^(⁴³)（ODGR）和 Oracle Active Data Guard 复制（OADGR）来实现主备节点概念。通过 Oracle Active Data Guard 实现跨多个可用区的负载分发。使用 Active Data Guard 进行复制，允许将复制的备用实例用于只读负载，从而实现负载分发。这在图 16-4 中显而易见：所有读取请求均路由至各自可用区内的备用节点。通过这种方式，主节点不会因跨可用区的微服务读取请求而负载过重。相反，所有写入请求现在均可路由至主节点。如图所示，即使来自其他可用区的写入请求也可路由至主节点。此外，如果主实例发生故障，该架构会迅速切换到同一可用区内的第二个实例，确保数据库持续可用并防止数据丢失。如果主可用区发生故障，或主可用区内的主实例和辅助实例同时故障，该架构会切换到辅助可用区内的第三个实例，从而保持数据库可用并防止数据丢失。使用 Active Data Guard 进行复制，允许第二和第三个实例同时用于只读工作负载。备用数据库是 Oracle 主生产数据库的事务一致性副本，最初由主数据库的备份副本创建。一旦创建并配置好备用数据库，Oracle Active Data Guard 会自动通过将主数据库的重做数据传输到备用系统来维护备用数据库，并在备用系统上应用重做数据。主数据库与备用数据库之间的复制可配置为同步或异步模式。  

Oracle Data Guard 不支持设置只读副本，因为物理备用数据库无法在从主数据库归档事务的同时打开用于读取——它只能在托管恢复模式或只读模式下运行，无法同时处于两种模式。基于 Oracle Data Guard 构建的 Oracle Active Data Guard 是一个允许设置只读副本的选项。除前述功能外，Oracle Active Data Guard 还允许对备用数据库进行只读访问，同时通过从主数据库归档事务来保持其数据更新。这使得您可以在备用实例上运行读取查询和报表，并从备用实例执行备份。在此类架构中，通常通过使用 EBS 快照到 Amazon S3，或使用 Oracle 恢复管理器^(⁴⁴)（RMAN）和 Oracle 安全备份云模块（OSB）来执行数据备份。  

**面向 Web 规模架构的分片**  



成功将写入可扩展性与读取可扩展性的管理分离后，你仍不能松懈。迟早你会开始感到，分离出的读取节点，或者如果你的业务过于成功（如同当今许多企业），甚至写入节点本身也会带来限制——这往往是由于其必须处理的交易量或管理的数据量过大所致。当企业在全球范围内运营，并将其应用程序开放给全球客户使用时，情况尤其如此。许多社交网络应用和消息应用都属于此类，它们需要达到网络规模。“分而治之”是核心法则，而分片（sharding）便是其中一种方法。分片可以应用于服务层或数据层。图 16-5 所示的架构展示了这一概念。

![../images/477630_1_En_16_Chapter/477630_1_En_16_Fig5_HTML.jpg](img/477630_1_En_16_Fig5_HTML.jpg)

图 16-5
提升读取可扩展性的数据架构

分片架构的概念是，你将拥有许多节点，所有或部分节点也会拥有冗余数据，然后每个节点将拥有唯一的令牌（token），你利用这些令牌将整个数据划分开来，以便对数据库进行写入和读取操作。

参考图 16-5，我将用一个类比来说明。假设你的应用程序类似于我们今天使用的流行社交网络应用。你知道系统中有太多用户，并且有太多用户操作需要在你的应用程序中管理。如果我的两个女儿，安和莉亚，以及其他数百万用户都是该应用的用户，那么设计令牌的一个简单方法是按字母顺序，根据用户名字的首字母将用户分配到不同的节点。有专门的节点或节点集群来处理名字以 A-H 开头的用户的请求和数据，同样，另一组节点或节点集群处理名字以 I-Q 开头的用户，以此类推。其假设是，交易和数据在这些节点或节点集群之间的分布大致均衡，你只需一次性管理单个节点或单个节点集群内的可扩展性。你可以设计你的应用程序未来需要多少个这样的节点或集群，并相应地设计节点（或分区）的数量以及令牌。你也可以使用任何其他模式来设计你的分片拓扑结构；但核心理念是相同的。你可以对微服务层和数据层都进行分片，在这种情况下，你的 ELB（弹性负载均衡器）需要具备一定的智能，以便将请求路由到适当的微服务实例。

**架构虽好，但我们仍需更优、更简的方案**

在了解了 Oracle 数据库作为数据库解决方案所提供的不同选项后，让我们考虑一下其他替代方案！你之前看到的所有解决方案模板都是为了解决手头的不同问题，是否使用它们是一个架构决策，需要综合考虑多方面因素，包括企业的长期技术战略。在本书中，我一直在谈论多语言（数据）架构的重要性，以及每个微服务完全管理自身数据的重要性。基于此，你也希望考虑那些能够提供类似可扩展性和可用性等级，但又不过度依赖任何特定供应商昂贵技术栈的架构。

微服务架构本身就是一种将系统按功能分解为多个独立部分的方法，以便你能更好地独立管理每个部分的可用性和可扩展性。然后，你可以通过分片来进一步管理数据量。无论是否使用分片，你还可以考虑将读取与写入分离，第 12 章介绍的 CQRS（命令查询职责分离）模式非常适合这一点。正确原则的组合将帮助你实现网络规模。

**Axon 中的高可用与可扩展 CQRS 示例**

抛开上一节中定义高可用性（HA）模板的各种供应商产品，我们将回到第 12 章介绍的 Axon CQRS。在那里，你看到了一个将不同 Axon 组件完全分布到多个 JVM 中的可运行代码。由于该示例是跨进程分布的，你实际上也可以将它们分布到多个节点上；不过，为了简单起见，我们在示例中将每个 Axon 处理器都放在单个节点上演示。在当前示例中，你将把它们分布到 Axon 组件的多个实例中，以验证如何为基于 Axon 的 CQRS 架构带来冗余，进而实现高可用性和可扩展性。

**设计示例场景**

你将沿用第 12 章“分布式命令与事件处理”一节中的相同设计，因此我不再重复所有解释。你的领域中有两个实体：订单和产品。用户可以购买产品，这将创建一个新订单。当新订单创建时，产品库存将减少。仅此而已。请重新阅读该节，以了解你将在本示例中执行的业务场景架构的详细概述。

你将扩展第 12 章示例的部署架构，以引入冗余。你可能需要参考第 9 章中“设计微服务高可用性场景”一节，你已在那里演示了 Spring Cloud 中的高可用性概念。在当前示例中，你将遵循类似的部署拓扑结构；但是，为了将示例的整体复杂性控制在特定范围内，并专注于讨论要点——即 Axon CQRS 组件本身的高可用性和可扩展性——你将省略当前示例中的 Spring Cloud 组件，部署架构将如图 16-6 所示。

![../images/477630_1_En_16_Chapter/477630_1_En_16_Fig6_HTML.jpg](img/477630_1_En_16_Fig6_HTML.jpg)

图 16-6
Axon CQRS 的高可用性演示

当前部署架构与第 12 章相应节中示例的主要区别在于，托管命令创建者、命令处理器、事件创建者和事件处理器的微服务被实例化了多次。因此，部署生态系统具有以下特征：

*   存在不止一种类型的事件处理器对同一类事件感兴趣。



*   同一类命令处理器和事件处理器存在多个实例。

在图 16-6 中，由于 Web 应用只有一个实例，因此你没有通过 Nginx 将请求路由到该 Web 应用，但这并不妨碍你实例化更多 Web 应用并通过 Nginx 进行反向代理请求。其目的是单独验证 Axon 组件的高可用性，因此你特意没有复制 Web 应用，再次是为了控制演示的复杂度。然而，负责通过拦截 HTTP 请求来创建命令的 REST 控制器微服务被实例化了两次，并且请求通过 Nginx 反向代理进行路由。这将有助于将来自 Web 浏览器的请求交替路由到这两个 REST 控制器微服务实例。通过这样做，你让两个实例都参与创建命令，并将它们分发到分布式命令总线，该总线将通过 JGroups 连接器进一步传播到应用程序。尽管图 16-6 中显示的所有其他微服务也通过嵌入式 HTTP 连接器监听 HTTP 流量，但它们与应用程序其他组件的通信是通过 JGroups 或 RabbitMQ 通道进行的，涉及创建和消费命令以及事件。

**编写示例场景**

演示简单 Axon 示例所需的完整代码位于文件夹 `ch16\ch16-01` 中。该代码与第 12 章的代码没有太大区别，因此我不再重复。

**构建并测试高可用性场景**

第一步，启动 RabbitMQ 服务器。你可能需要参考附录 B 来开始使用 RabbitMQ 服务器。

```
D:\Applns\RabbitMQ\rabbitmq_server-3.6.3\sbin>D:\Applns\RabbitMQ\rabbitmq_server-3.6.3\sbin\rabbitmq-server.bat
```

确保 MySQL 已启动并正在运行。你可能需要参考附录 H 来开始使用 MySQL。

```
首先启动 MySQL 服务器
D:\Applns\MySQL\mysql-8.0.14-winx64\bin>mysqld --console
现在打开 MySQL 提示符
D:\Applns\MySQL\mysql-8.0.14-winx64\bin>mysql -u root –p
mysql> use ecom01;
数据库已更改
mysql>
```

为了从干净的表格开始，请删除示例中你想要的任何名称的表格：

```
mysql> drop table ecom_order;
mysql> drop table ecom_product;
mysql> drop table ecom_order_view;
mysql> drop table ecom_product_view;
mysql> drop table ecom_order_audit;
```

现在，如果 `ecom_order` 和 `ecom_product` 表尚不存在，它们将在微服务启动时被创建。但是，你需要显式创建读数据库表和审计表。在你的情况下，你将创建所有必需的表格。

```
mysql> create table ecom_order (id integer not null, last_event_sequence_number bigint, version bigint, number integer, order_status varchar(255), price double precision, product_id integer, primary key (id)) ENGINE=InnoDB;
mysql> create table ecom_product (id integer not null, last_event_sequence_number bigint, version bigint, description varchar(255), price double precision, stock integer, primary key (id)) ENGINE=InnoDB;
mysql> alter table ecom_order add constraint FK_f3rnd79i90twafllfhpo1sihi foreign key (product_id) references ecom_product (id);
mysql> create table ecom_product_view(id INT , price DOUBLE, stock INT ,description VARCHAR(255));
mysql> create table ecom_order_view(id INT , price DOUBLE, number INT ,description VARCHAR(225),status VARCHAR(50));
mysql> create table ecom_order_audit(id INT ,status VARCHAR(50),date TIMESTAMP);
```

该示例有一个 Maven 模块，其中包含所有其他微服务使用的公共类。因此，必须首先构建它：

```
cd D:\binil\gold\pack03\ch16\ch16-01\Ax2-Multi-Command-Multi-Event-Handler-Instance\06-Ecom-common
D:\binil\gold\pack03\ch16\ch16-01\Ax2-Multi-Command-Multi-Event-Handler-Instance\06-Ecom-common>make
D:\binil\gold\pack03\ch16\ch16-01\Ax2-Multi-Command-Multi-Event-Handler-Instance\06-Ecom-common>mvn clean install
```

接下来，有五个微服务需要构建并运行，因此你将逐一进行。

**微服务 1：01-Ecom-web**

首先，更新配置文件以适应你的环境：

```
ch16\ch16-01\Ax2-Multi-Command-Multi-Event-Handler-Instance\01-Ecom-web\src\main\resources\application.properties
server.port=8080
```

注意
此处不要做任何更改。

现在构建并打包 Ecom-web 微服务的可执行文件，并启动服务器。在 `ch16\ch16-01\Ax2-Multi-Command-Multi-Event-Handler-Instance\01-Ecom-web\make.bat` 处提供了一个实用脚本。

```
cd D:\binil\gold\pack03\ch16\ch16-01\Ax2-Multi-Command-Multi-Event-Handler-Instance\01-Ecom-web
D:\binil\gold\pack03\ch16\ch16-01\Ax2-Multi-Command-Multi-Event-Handler-Instance\01-Ecom-web>make
D:\binil\gold\pack03\ch16\ch16-01\Ax2-Multi-Command-Multi-Event-Handler-Instance\01-Ecom-web>mvn clean install
```

你可以通过多种方式运行 Spring Boot 应用程序。直接的方式是通过以下命令执行 JAR 文件：

```
D:\binil\gold\pack03\ch16\ch16-01\Ax2-Multi-Command-Multi-Event-Handler-Instance\01-Ecom-web>run
D:\binil\gold\pack03\ch16\ch16-01\Ax2-Multi-Command-Multi-Event-Handler-Instance\01-Ecom-web>java -Dserver.port=8080 -jar .\target\01-Ecom-web-0.0.1-SNAPSHOT.jar
```

这将在端口 8080 上启动 01-Ecom-web Spring Boot 服务器。

**微服务 2：02-Ecom-CreateCommandRestController**

JGroups 配置在以下文件中提供：

```
ch16\ch16-01\Ax2-Multi-Command-Multi-Event-Handler-Instance\02-Ecom-CreateCommandRestController\src\main\resources\udp_config.xml
```

但是，我们现在不必过多担心此文件的内容。

更新配置文件以适应你的环境：

```
ch16\ch16-01\Ax2-Multi-Command-Multi-Event-Handler-Instance\02-Ecom-CreateCommandRestController\src\main\resources\application.properties
server.port=8082
spring.datasource.url=jdbc:mysql://localhost/ecom01
spring.datasource.username=root
spring.datasource.password=rootpassword
cd D:\binil\gold\pack03\ch16\ch16-01\Ax2-Multi-Command-Multi-Event-Handler-Instance\02-Ecom-CreateCommandRestController
D:\binil\gold\pack03\ch16\ch16-01\Ax2-Multi-Command-Multi-Event-Handler-Instance\02-Ecom-CreateCommandRestController>make
D:\binil\gold\pack03\ch16\ch16-01\Ax2-Multi-Command-Multi-Event-Handler-Instance\02-Ecom-CreateCommandRestController>mvn clean install
```

你可以通过多种方式运行 02-Ecom-CreateCommandRestController Axon Spring Boot 应用程序。直接的方式是通过以下命令执行 JAR 文件：

```
D:\binil\gold\pack03\ch16\ch16-01\Ax2-Multi-Command-Multi-Event-Handler-Instance\02-Ecom-CreateCommandRestController>run1
D:\binil\gold\pack03\ch16\ch16-01\Ax2-Multi-Command-Multi-Event-Handler-Instance\02-Ecom-CreateCommandRestController>java -Dserver.port=8082 -jar .\target\02-Ecom-CreateCommandRestController-0.0.1-SNAPSHOT.jar
```

这将在端口 8082 上启动 02-Ecom-CreateCommandRestController Axon Spring Boot 服务器。

为同一个微服务启动另一个实例克隆：

```
cd D:\binil\gold\pack03\ch16\ch16-01\Ax2-Multi-Command-Multi-Event-Handler-Instance\02-Ecom-CreateCommandRestController
D:\binil\gold\pack03\ch16\ch16-01\Ax2-Multi-Command-Multi-Event-Handler-Instance\02-Ecom-CreateCommandRestController>run2
D:\binil\gold\pack03\ch16\ch16-01\Ax2-Multi-Command-Multi-Event-Handler-Instance\02-Ecom-CreateCommandRestController>java -Dserver.port=8083 -jar .\target\02-Ecom-CreateCommandRestController-0.0.1-SNAPSHOT.jar
```

这将在端口 8083 上启动 02-Ecom-CreateCommandRestController Axon Spring Boot 服务器的另一个实例。



微服务 3：
03-Ecom-HandleCommandAndCreateEvent

根据你的环境更新配置文件；参见清单
16-1。

```
server.port=8084
spring.datasource.url=jdbc:mysql://localhost/ecom01
spring.datasource.username=root
spring.datasource.password=rootpassword
ecom.amqp.rabbit.address= 127.0.0.1:5672
ecom.amqp.rabbit.username= guest
ecom.amqp.rabbit.password= guest
ecom.amqp.rabbit.vhost=/
ecom.amqp.rabbit.exchange=Ecom-02
ecom.amqp.rabbit.queue=Ecom-createcommand_03
cd D:\binil\gold\pack03\ch16\ch16-01\Ax2-Multi-Command-Multi-Event-Handler-Instance\03-Ecom-HandleCommandAndCreateEvent
D:\binil\gold\pack03\ch16\ch16-01\Ax2-Multi-Command-Multi-Event-Handler-Instance\03-Ecom-HandleCommandAndCreateEvent>make
D:\binil\gold\pack03\ch16\ch16-01\Ax2-Multi-Command-Multi-Event-Handler-Instance\03-Ecom-HandleCommandAndCreateEvent>mvn clean install
清单 16-1
微服务 3 配置 (ch16\ch16-01\Ax2-Multi-Command-Multi-Event-Handler-Instance\03-Ecom-HandleCommandAndCreateEvent\src\main\resources\application.properties)
```

你可以通过多种方式运行 03-Ecom-HandleCommandAndCreateEvent Axon Spring Boot 应用程序的两个实例。最直接的方式是通过以下命令执行 JAR 文件：

```
D:\binil\gold\pack03\ch16\ch16-01\Ax2-Multi-Command-Multi-Event-Handler-Instance\03-Ecom-HandleCommandAndCreateEvent>run1
D:\binil\gold\pack03\ch16\ch16-01\Ax2-Multi-Command-Multi-Event-Handler-Instance\03-Ecom-HandleCommandAndCreateEvent>java -Dserver.port=8084 -jar .\target\03-Ecom-HandleCommandAndCreateEvent-0.0.1-SNAPSHOT.jar
```

这将在端口 8084 上启动 02-Ecom-CreateCommandRestController Axon Spring Boot 服务器。

```
cd D:\binil\gold\pack03\ch16\ch16-01\Ax2-Multi-Command-Multi-Event-Handler-Instance\03-Ecom-HandleCommandAndCreateEvent
D:\binil\gold\pack03\ch16\ch16-01\Ax2-Multi-Command-Multi-Event-Handler-Instance\03-Ecom-HandleCommandAndCreateEvent>run2
D:\binil\gold\pack03\ch16\ch16-01\Ax2-Multi-Command-Multi-Event-Handler-Instance\03-Ecom-HandleCommandAndCreateEvent>java -Dserver.port=8085 -jar .\target\03-Ecom-HandleCommandAndCreateEvent-0.0.1-SNAPSHOT.jar
```

这将在端口 8085 上启动 02-Ecom-CreateCommandRestController Axon Spring Boot 服务器。

微服务 4：04-Ecom-EventHandleCore

根据你的环境更新配置文件，如清单 16-2 所示。

```
server.port=8086
spring.datasource.url=jdbc:mysql://localhost/ecom01
spring.datasource.username=root
spring.datasource.password=rootpassword
ecom.amqp.rabbit.address= 127.0.0.1:5672
ecom.amqp.rabbit.username= guest
ecom.amqp.rabbit.password= guest
ecom.amqp.rabbit.vhost=/
ecom.amqp.rabbit.exchange=Ecom-02
ecom.amqp.rabbit.queue=Ecom-event-core_03
cd D:\binil\gold\pack03\ch16\ch16-01\Ax2-Multi-Command-Multi-Event-Handler-Instance\04-Ecom-EventHandleCore
D:\binil\gold\pack03\ch16\ch16-01\Ax2-Multi-Command-Multi-Event-Handler-Instance\04-Ecom-EventHandleCore>make
D:\binil\gold\pack03\ch16\ch16-01\Ax2-Multi-Command-Multi-Event-Handler-Instance\04-Ecom-EventHandleCore>mvn clean install
清单 16-2
微服务 4 配置 (ch16\ch16-01\Ax2-Multi-Command-Multi-Event-Handler-Instance\04-Ecom-EventHandleCore\src\main\resources\application.properties)
```

你可以再次通过多种方式运行 04-Ecom-EventHandleCore Axon Spring Boot 应用程序的两个实例。最直接的方式是通过以下命令执行 JAR 文件：

```
D:\binil\gold\pack03\ch16\ch16-01\Ax2-Multi-Command-Multi-Event-Handler-Instance\04-Ecom-EventHandleCore>run1
D:\binil\gold\pack03\ch16\ch16-01\Ax2-Multi-Command-Multi-Event-Handler-Instance\04-Ecom-EventHandleCore>java -Dserver.port=8086 -jar .\target\04-Ecom-EventHandleCore-0.0.1-SNAPSHOT.jar
```

这将在端口 8086 上启动 04-Ecom-EventHandleCore Axon Spring Boot 服务器。

```
cd D:\binil\gold\pack03\ch16\ch16-01\Ax2-Multi-Command-Multi-Event-Handler-Instance\04-Ecom-EventHandleCore
D:\binil\gold\pack03\ch16\ch16-01\Ax2-Multi-Command-Multi-Event-Handler-Instance\04-Ecom-EventHandleCore>run2
D:\binil\gold\pack03\ch16\ch16-01\Ax2-Multi-Command-Multi-Event-Handler-Instance\04-Ecom-EventHandleCore>java -Dserver.port=8087 -jar .\target\04-Ecom-EventHandleCore-0.0.1-SNAPSHOT.jar
```

这将在端口 8087 上启动 04-Ecom-EventHandleCore Axon Spring Boot 服务器。

微服务 5：05-Ecom-EventHandlerAudit

这是本示例中你要启动的最后一个微服务。根据你的环境更新配置文件，如清单 16-3 所示。

```
server.port=8088
spring.datasource.url=jdbc:mysql://localhost/ecom01
spring.datasource.username=root
spring.datasource.password=rootpassword
ecom.amqp.rabbit.address= 127.0.0.1:5672
ecom.amqp.rabbit.username= guest
ecom.amqp.rabbit.password= guest
ecom.amqp.rabbit.vhost=/
ecom.amqp.rabbit.exchange=Ecom-02
ecom.amqp.rabbit.queue=Ecom-event-history_03
cd D:\binil\gold\pack03\ch16\ch16-01\Ax2-Multi-Command-Multi-Event-Handler-Instance\05-Ecom-EventHandlerAudit
D:\binil\gold\pack03\ch16\ch16-01\Ax2-Multi-Command-Multi-Event-Handler-Instance\05-Ecom-EventHandlerAudit>make
D:\binil\gold\pack03\ch16\ch16-01\Ax2-Multi-Command-Multi-Event-Handler-Instance\05-Ecom-EventHandlerAudit>mvn clean install
清单 16-3
微服务 5 配置参数 (ch16\ch16-01\Ax2-Multi-Command-Multi-Event-Handler-Instance\05-Ecom-EventHandlerAudit\src\main\resources\application.properties)
```

你可以通过多种方式运行 05-Ecom-EventHandlerAudit Axon Spring Boot 应用程序的两个实例。最直接的方式是通过以下命令执行 JAR 文件：

```
D:\binil\gold\pack03\ch16\ch16-01\Ax2-Multi-Command-Multi-Event-Handler-Instance\05-Ecom-EventHandlerAudit>run1
D:\binil\gold\pack03\ch16\ch16-01\Ax2-Multi-Command-Multi-Event-Handler-Instance\05-Ecom-EventHandlerAudit>java -Dserver.port=8088 -Dspring.application.name=product-audit-01 -jar .\target\05-Ecom-EventHandlerAudit-0.0.1-SNAPSHOT.jar
```

这将在端口 8088 上启动 05-Ecom-EventHandlerAudit Axon Spring Boot 服务器。

```
cd D:\binil\gold\pack03\ch16\ch16-01\Ax2-Multi-Command-Multi-Event-Handler-Instance\05-Ecom-EventHandlerAudit
D:\binil\gold\pack03\ch16\ch16-01\Ax2-Multi-Command-Multi-Event-Handler-Instance\05-Ecom-EventHandlerAudit>run2
D:\binil\gold\pack03\ch16\ch16-01\Ax2-Multi-Command-Multi-Event-Handler-Instance\05-Ecom-EventHandlerAudit>java -Dserver.port=8089 -Dspring.application.name=product-audit-02 -jar .\target\05-Ecom-EventHandlerAudit-0.0.1-SNAPSHOT.jar
```

这将在端口 8089 上启动 05-Ecom-EventHandlerAudit Axon Spring Boot 服务器。
至此，所有微服务启动完毕。

现在，用一些初始数据预填充 `ecom_product` 和 `ecom_product_view` 表：

```
mysql> insert into ecom_product(id,description,price,stock,version) values(1,'Shirts',100,5,0);
mysql> insert into ecom_product(id,description,price,stock,version) values(2,'Pants',100,5,0);
mysql> insert into ecom_product(id,description,price,stock,version) values(3,'T-Shirt',100,5,0);
mysql> insert into ecom_product(id,description,price,stock,version) values(4,'Shoes',100,5,0);
mysql> insert into ecom_product_view(id,description,price,stock) values(1,'Shirts',100,5);
mysql> insert into ecom_product_view(id,description,price,stock) values(2,'Pants',100,5);
mysql> insert into ecom_product_view(id,description,price,stock) values(3,'T-Shirt',100,5);
mysql> insert into ecom_product_view(id,description,price,stock) values(4,'Shoes',100,5);
```

接下来，你需要将 Nginx 配置为反向代理。将 `ch16\ch16-01\Ax2-Multi-Command-Multi-Event-Handler-Instance\ConfNginx\nginx.conf` 的相关部分应用到你的 Nginx 配置中；参见清单 16-4。



```
http {
upstream myapp1 {
server localhost:8082;
server localhost:8083;
}
server {
listen       8081;
server_name  localhost;
location / {
proxy_pass http://myapp1;
root   html;
index  index.html index.htm;
}
}
}
清单 16-4
Nginx 配置参数 (D:\Applns\nginx\nginx-1.13.5\conf\nginx.conf)
```

你可能需要参考附录 C 来了解如何开始使用 Nginx。

完成上述配置更改后，启动 Nginx：

```
cd D:\Applns\nginx\nginx-1.13.5
D:\Applns\nginx\nginx-1.13.5>nginx
```

你可以使用浏览器（推荐 Chrome）并访问 `http://localhost:8080/.`
如果更改未生效，你可以关闭浏览器会话，然后打开一个新的浏览器实例，并尝试上述 URL。
你可以按照第 12 章“分布式命令与事件处理”一节中“构建并测试示例场景”子节之前描述的步骤来测试示例。在测试示例时，如果你持续观察微服务实例的控制台窗口，可以验证 HTTP 请求将交替命中 CreateCommandRestController 微服务的两个实例，因为 Nginx 会将请求反向代理到这两个实例。类似地，命令处理和事件处理将在各自微服务的两个实例之间交替进行。你也可以暂时关闭任何一个微服务实例，在此期间，如果你持续发送请求进行测试，你会发现相应的处理始终在另一个存活的微服务实例中进行。之后，如果你重新启动之前关闭的实例，你会看到处理再次均匀地分布在两个实例之间。

在 AXON CQRS 中扩展写入节点的聚合根实体
上一节演示了如何通过添加更多节点来处理命令和事件，从而在 Axon 中扩展 CQRS 架构。现在让我们探讨扩展写入节点或命令处理节点时涉及的注意事项。

并发修改同一实体的克隆
在“独立读写可扩展性以提高效率”一节中，你看到了基于 Oracle 的架构模板，其中有一个单一的写入节点或主数据库用于处理所有写入操作，以及多个读取节点或辅助数据库用于管理查询。你知道采用这种架构的原因：管理高读写比或大多数业务应用的类似特性，其中读取事务的数量远高于写入事务的数量。

当你在数据持久化层之上添加 CQRS 层时，你已经将命令和查询处理划分到与数据持久化层分离的节点或进程中。此外，当你将处理命令和查询的微服务复制到多个实例时，典型的架构如图 16-7 所示。

![../images/477630_1_En_16_Chapter/477630_1_En_16_Fig7_HTML.jpg](img/477630_1_En_16_Fig7_HTML.jpg)

图 16-7
扩展后的 CQRS 架构

参考图 16-7，扩展事件处理器是直接的，因为所有的事件处理器都会被通知，并且在大多数情况下，它们都可以采取任何操作，例如刷新物化视图或缓存。如果你观察命令处理器的扩展场景，可能会更复杂。图 16-8 描述了一个场景，其中有许多托管命令处理器的微服务实例。

![../images/477630_1_En_16_Chapter/477630_1_En_16_Fig8_HTML.jpg](img/477630_1_En_16_Fig8_HTML.jpg)

图 16-8
扩展后的命令处理器

实际上，图 16-8 是图 16-7 中命令处理部分的分解视图。假设 Ann 和 Ria 都试图通过一个全球在线旅行社（OTA）网站预订一个航班座位。该旅行社销售单一航空公司多个航班的座位库存。该 OTA 也销售多家航空公司的航班座位库存。如果你查看航班座位的预订请求，它们是对应用程序的写入操作，如果采用 CQRS 模式，这些操作将被建模为命令和命令处理器。
由于 OTA 在全球运营，假设每个时间段发生的预订总数相当可观，因此完全有理由拥有多个包含负责发起预订的命令处理器的微服务实例。这就是图 16-8 所表示的内容。该图是对实际库存和预订系统的过度简化视图，但足以解释我们本节试图讨论和解决的核心概念。
当来自多个最终用户的预订某航空公司最后一个座位的请求几乎同时到达时，场景变得复杂。如果忽略分片、粘性会话等其他方面，你可以假设，来自多个最终用户的预订某航空公司最后一个座位的 HTTP 请求，几乎同时命中同一个包含负责发起预订的命令处理器的微服务的不同实例。这些不同的命令处理器实例将首先尝试从持久化存储（即单一数据库）加载实体，在你的场景中，这个实体就是飞机上的最后一个座位。当这种情况发生时，关于持久化存储中最后一个座位的单一事实来源会进入多个进程空间，即命令处理器微服务的不同实例。如果底层硬件是多核架构，你现在可以想象一个场景，即处理预订请求几乎同时在同一个最后座位的不同克隆上进行。你应该如何处理这种情况？

乐观锁
乐观锁假设多个事务可以互不影响地完成，因此这些事务可以在不锁定它们所影响的数据资源的情况下进行。然而，在提交之前，每个事务都会验证没有其他事务修改过它的数据。如果验证发生冲突，则提交的事务会回滚。
乐观锁通常用于跨越多个数据库事务的长事务或会话。但同样的策略也可以用于短事务。你可以在实体中存储版本信息，这样如果同一个实体被多个会话更新，后续的提交尝试会被告知冲突，并且不会覆盖先前会话的工作。这种方法保证了一定的隔离性，但扩展性良好，并且在读多写少的场景下尤其有效。Hibernate 提供了两种不同的机制来存储版本信息：专用的版本号或时间戳。

对于这个示例，你使用 Hibernate 作为持久化服务提供者。因此，在同一个实体上有多个事务同时进行的情况下，Hibernate 将允许其中一个事务提交（通常是先完成的那个），而另一个事务将收到一个 `org.hibernate.StaleObjectStateException` 异常，并且该异常将携带关于导致失败的特定实体实例的信息。这里提供了一个示例：

```
Caused by: org.hibernate.StaleObjectStateException: Row was updated or deleted by another transaction (or unsaved-value mapping was incorrect) : [com.axon.concurrency.user.model.User#1]
```



当持久化提供程序收到此类异常时，会判定发生了乐观锁冲突，并抛出 `javax.persistence.OptimisticLockException`。此异常可能在 API 调用、刷新或提交时抛出。发生这种情况时，当前事务（如果处于活动状态）将被标记为回滚。

Axon 中的冲突检测与解决
明确变更含义的主要优势之一在于能够更精确地检测冲突变更。通常，当两个用户几乎同时对同一数据进行操作时，就会发生这些冲突变更。参考图 16-8，如果 Ann 和 Ria 都查看数据的特定版本，并认为他们最后查看的座位状态是“可预订”，那么他们俩都可能决定对该数据进行更改。他们都会发送类似“在此聚合的 X 版本上执行该操作”的命令，其中 X 是应用更改的聚合的预期版本；至少，这是 Ann 和 Ria 所期望的。在这种情况下，其中一人的更改将实际应用于预期版本。当系统即将代表另一用户应用更改时，应用更改的预期版本已经改变，因此该特定用户的更改将不会被应用。
`org.axonframework.domain.AggregateRoot<I>` 是一个接口，为表示聚合根的实体定义了契约，其中 `I` 表示此聚合标识符的类型。AggregateRoot 提供了一个名为 `Long getVersion()` 的方法。
`AggregateRoot.getVersion()` 返回聚合的当前版本号，如果聚合是新创建的，则返回 null。此版本必须反映应用更改的聚合的版本号。每次修改聚合并存储在存储库中时，版本号必须至少增加 1。此版本号可用于乐观锁策略以及检测冲突的并发修改。
通常，此聚合上最后一个已提交事件的序列号被用作版本号。
为方便起见，Axon 还提供了 `org.axonframework.domain.AbstractAggregateRoot<I>`，这是 AggregateRoot 接口的一个非常基础的实现。它提供了跟踪未提交事件的机制，并根据生成的事件数维护版本号。在大多数情况下，您只需从该抽象类扩展您的聚合根实体即可。

演示 Axon 中乐观锁的示例
CQRS
在本节中，您将看到一个演示 Axon 乐观锁策略的工作示例。您将搭建一个类似于图 16-8 所示的环境；但是，您不会部署在云中。相反，您将在自己的 PC 上运行，并且只运行两个微服务实例，因为如果可以演示两个微服务的工作情况，则可以假定在涉及两个以上微服务实例的场景中也能正常工作。

设计示例场景

您将模拟两个用户同时尝试修改同一实体。为此，您将拥有一个简单的 Axon 微服务，包含一个 REST 控制器和一个命令处理器。REST 控制器可以接受 POST 和 PUT 请求来创建和修改 User 实体的记录。见图 16-9。

![../images/477630_1_En_16_Chapter/477630_1_En_16_Fig9_HTML.jpg](img/477630_1_En_16_Fig9_HTML.jpg)

图 16-9
测试乐观锁的示例场景设计

您需要调整“修改”操作的处理方式，以模拟并发修改场景。为此，您将使用 `Thread.sleep()` 方法引入延迟，以便在第一个修改操作处于休眠状态时，您有足够的时间启动第二个修改请求。

编写示例场景代码
代码很直接，但为了示例的完整性，我将在此展示。本节的所有代码示例都在文件夹 `ch16\ch16-02\Ax2-Cuncurrency-Test` 中。

首先，您将查看 User 实体类。见清单 16-5。

```
import org.axonframework.domain.AbstractAggregateRoot;
@Entity
@Table(name = "USER")
public class User extends AbstractAggregateRoot {
@Id
private Long id;
@Column(name="USER_NAME")
private String userName;
@Column(name="AGE")
private Integer age;
}
清单 16-5
被并发修改的 User 实体 (ch16\ch16-02\Ax2-Cuncurrency-Test\src\main\java\com\axon\concurrency\user\model\User.java)
```

您扩展了 AbstractAggregateRoot 来创建这个基本的聚合实体，以便测试并发修改。

REST 控制器同样简单，包含创建和修改 User 实体的方法。见清单 16-6。

```
@RestController
public class UserController {
@Autowired
private CommandGateway commandGateway;
@RequestMapping(method = RequestMethod.POST,path="/user/create")
public void createNewUser(@RequestBody UserDTO userDTO){
UserCreationCommand command = new UserCreationCommand(userDTO.getId(),
userDTO.getUserName(), userDTO.getAge());
try{
commandGateway.sendAndWait(command);
}
catch(RuntimeException runtimeException){
LOGGER.error(runtimeException.getMessage());
}
}
@RequestMapping(method = RequestMethod.PUT,path="/user/update")
public void updateUser(@RequestBody UserDTO userDTO){
UserModificationCommand command = new UserModificationCommand(
userDTO.getId(), userDTO.getUserName(), userDTO.getAge());
try{
commandGateway.sendAndWait(command);
}
catch(RuntimeException runtimeException){
LOGGER.error(runtimeException.getMessage());
}
}
}
清单 16-6
创建命令的 REST 控制器 (ch16\ch16-02\Ax2-Cuncurrency-Test\src\main\java\com\axon\concurrency\user\web\UserController.java)
```

您创建并发送一个 UserCreationCommand 用于创建新用户。您还创建并发送一个 UserModificationCommand 用于用户修改请求。UserCreationCommand 和 UserModificationCommand 都由 UserCommandHandler 处理。见清单 16-7。

```
@Component
public class UserCommandHandler {
@Autowired
@Qualifier(value = "userRepository")
private Repository userRepository;
@CommandHandler
public void handleUserCreationCommand(UserCreationCommand
userCreationCommand) {
User user = new User(userCreationCommand.getId(),
userCreationCommand.getUserName(), userCreationCommand.getAge());
userRepository.add(user);
}
@CommandHandler
public void handleUserModifyCommand(UserModificationCommand
userModificationCommand) {
User user = userRepository.load(userModificationCommand.getId());
user.setAge(userModificationCommand.getAge());
user.setUserName(userModificationCommand.getUserName());
Long seconds = 60L;
LOGGER.debug("Thread Sleeping for {} seconds", seconds);
try{
Thread.sleep(1000 ∗ seconds);
}catch(Exception exception){
LOGGER.error(exception.getMessage());
}
}
}
清单 16-7
Axon 命令处理器 (ch16\ch16-02\Ax2-Cuncurrency-Test\src\main\java\com\axon\concurrency\user\command\handler\UserCommandHandler.java)
```

主要代码到此结束。

构建并测试示例场景

第一步，您需要启动 MySQL 服务器。您可能需要参考附录 H 来开始使用 MySQL 服务器。

```
D:\Applns\MySQL\mysql-8.0.14-winx64\bin>mysqld --console
```

现在打开一个 MySQL 提示符：

```
D:\Applns\MySQL\mysql-8.0.14-winx64\bin>mysql -u root –p
mysql> use ecom01;
Database changed
mysql>
```



首先清理数据表，删除与样本同名的所有表：

```
mysql> drop table user;
```

根据你的环境更新配置文件，参见清单 16-8。

```
server.port: 8080
spring.datasource.url=jdbc:mysql://localhost/ecom01
spring.datasource.username=root
spring.datasource.password=rootpassword
清单 16-8
MySQL 配置 (ch16\ch16-02\Ax2-Cuncurrency-Test\src\main\resources\application.properties)
```

注意
请勿在此处进行任何修改。

接下来，你将构建并打包并发测试微服务的可执行文件，并启动两个服务器实例。在文件夹 `ch16\ch16-02\Ax2-Cuncurrency-Test\make.bat` 中有一个实用脚本。

```
cd D:\binil\gold\pack03\ch16\ch16-02\Ax2-Cuncurrency-Test
D:\binil\gold\pack03\ch16\ch16-02\Ax2-Cuncurrency-Test>make
D:\binil\gold\pack03\ch16\ch16-02\Ax2-Cuncurrency-Test>mvn clean install
```

运行 Spring Boot 应用程序有多种方式。最直接的方式是通过以下命令执行 JAR 文件：

```
D:\binil\gold\pack03\ch16\ch16-02\Ax2-Cuncurrency-Test>run1
D:\binil\gold\pack03\ch16\ch16-02\Ax2-Cuncurrency-Test>java -Dserver.port=8080 -jar .\target\Axon-Concurrency-test-0.0.1-SNAPSHOT.jar
```

这将在端口 8080 上启动命令处理器的第一个实例。同时，它还会在数据库中为本示例创建所需的 `User` 表。你可能需要留意清单 16-9 中 Axon 为管理 AggregateRoot 实体而创建的额外字段 `last_event_sequence_number` 和 `version`。

```
mysql> desc user;
+----------------------------+--------------+------+-----+---------+-------+
| 字段                       | 类型         | 空   | 键   | 默认值  | 额外   |
+----------------------------+--------------+------+-----+---------+-------+
| id                         | bigint(20)   | NO   | PRI | NULL    |       |
| last_event_sequence_number | bigint(20)   | YES  |     | NULL    |       |
| version                    | bigint(20)   | YES  |     | NULL    |       |
| age                        | int(11)      | YES  |     | NULL    |       |
| user_name                  | varchar(255) | YES  |     | NULL    |       |
+----------------------------+--------------+------+-----+---------+-------+
5 行记录 (0.04 秒)
mysql>
清单 16-9
AggregateRoot 实体的 User 表
```

你至少需要再启动一个相同微服务的实例，以便在同一个 Axon Aggregate 的不同副本上模拟并发修改请求。请在另一个窗口中执行此操作。

```
cd D:\binil\gold\pack03\ch16\ch16-02\Ax2-Cuncurrency-Test
D:\binil\gold\pack03\ch16\ch16-02\Ax2-Cuncurrency-Test>run2
D:\binil\gold\pack03\ch16\ch16-02\Ax2-Cuncurrency-Test>java -Dserver.port=8081 -jar .\target\Axon-Concurrency-test-0.0.1-SNAPSHOT.jar
```

这将在端口 8081 上启动命令处理器的第二个实例。

你需要在域中创建一个 User 实体。打开 Postman，发送一个 HTTP POST 请求，以 JSON 格式发送 User 数据来创建新用户，如图 16-10 所示。你可以参考附录 D 了解更多关于 Postman 的信息。

![../images/477630_1_En_16_Chapter/477630_1_En_16_Fig10_HTML.jpg](img/477630_1_En_16_Fig10_HTML.jpg)

图 16-10
创建新用户

```
HTTP POST: http://127.0.0.1:8080/user/create
请求体: JSON(application/json)
{ "id" : 1, "userName" : "TestUser-01", "age" : 11 }
```

清单 16-10 显示了微服务控制台输出用户创建的日志。

```
D:\binil\gold\pack03\ch16\ch16-02\Ax2-Cuncurrency-Test>run1
D:\binil\gold\pack03\ch16\ch16-02\Ax2-Cuncurrency-Test>java -Dserver.port=8080 -jar .\target\Axon-Concurrency-test-0.0.1-SNAPSHOT.jar
.   ____          _            __ _ _
/\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
\\/  ___)| |_)| | | | | || (_| |  ) ) ) )
'  |____| .__|_| |_|_| |_\__, | / / / /
=========|_|==============|___/=/_/_/_/
:: Spring Boot ::        (v1.3.5.RELEASE)
...
2019-03-08 13:53:39 INFO  org.springframework.boot.StartupInfoLogger.logStarted:57 - Started AxonApplication in 36.376 seconds (JVM running for 39.958)
...
2019-03-08 13:56:32 INFO  com.axon.concurrency.user.web.UserController.createNewUser:67 - Start
2019-03-08 13:56:32 INFO  com.axon.concurrency.user.command.handler.UserCommandHandler.handleUserCreationCommand:69 - Start
2019-03-08 13:56:32 DEBUG com.axon.concurrency.user.command.handler.UserCommandHandler.handleUserCreationCommand:72 - New User Created : ID : 1; UserName : TestUser-01; Age : 11; Version : null
2019-03-08 13:56:32 INFO  com.axon.concurrency.user.command.handler.UserCommandHandler.handleUserCreationCommand:73 - End
Hibernate: insert into user (last_event_sequence_number, version, age, user_name, id) values (?, ?, ?, ?, ?)
2019-03-08 13:56:32 INFO  com.axon.concurrency.user.web.UserController.createNewUser:76 - End
清单 16-10
微服务控制台显示用户创建
```

你可以通过 MySQL 命令窗口查询 MySQL 数据库，确认 User 实体已创建，如清单 16-11 所示。

```
mysql> select ∗ from user;
+----+----------------------------+---------+------+-------------+
| id | last_event_sequence_number | version | age  | user_name   |
+----+----------------------------+---------+------+-------------+
|  1 |                       NULL |       0 |   11 | TestUser-01 |
+----+----------------------------+---------+------+-------------+
1 行记录 (0.00 秒)
mysql>
清单 16-11
检查新创建的用户
```

现在，你应该尝试从多个客户端（大致同时）修改上面创建的用户。用户修改处理当前配置了 60 秒的延迟。这意味着，在你发出第一个修改请求后，你需要在接下来的 60 秒内（更稳妥地说，是在接下来的 60 秒减去一个微小时间差内）再发出另一个修改请求。

打开 Postman，发送一个 HTTP PUT 请求，以 JSON 格式发送 User 数据来更新之前创建的用户，如图 16-11 所示。

![../images/477630_1_En_16_Chapter/477630_1_En_16_Fig11_HTML.jpg](img/477630_1_En_16_Fig11_HTML.jpg)

图 16-11
从第一个客户端更新用户

```
HTTP PUT http://127.0.0.1:8080/user/update
请求体 Raw JSON(application/json)
{ "id" : 1, "userName" : "TestUser-02", "age" : 12 }
```

不要耽误太多时间，再发送一个 HTTP PUT 请求，以 JSON 格式发送 User 数据来更新之前创建的用户，但使用略有不同的数据，以便你能够区分上述两个操作所导致的更新，如图 16-12 所示。

![../images/477630_1_En_16_Chapter/477630_1_En_16_Fig12_HTML.jpg](img/477630_1_En_16_Fig12_HTML.jpg)

图 16-12
从第二个客户端更新用户

```
HTTP PUT http://127.0.0.1:8081/user/update
请求体 Raw JSON(application/json)
{ "id" : 1, "userName" : "TestUser-03", "age" : 13 }
```

测试到此完成。如果你检查微服务控制台，可以看到两个客户端都处于 20 秒的休眠模式。清单 16-12 显示了微服务 1 的控制台，该服务受到了图 16-11 中 PUT 方法的影响。



```
D:\binil\gold\pack03\ch16\ch16-02\Ax2-Cuncurrency-Test>run1
D:\binil\gold\pack03\ch16\ch16-02\Ax2-Cuncurrency-Test>java -Dserver.port=8080 -jar .\target\Axon-Concurrency-test-0.0.1-SNAPSHOT.jar
.   ____          _            __ _ _
/\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
\\/  ___)| |_)| | | | | || (_| |  ) ) ) )
'  |____| .__|_| |_|_| |_\__, | / / / /
=========|_|==============|___/=/_/_/_/
:: Spring Boot ::        (v1.3.5.RELEASE)
...
2019-03-08 13:53:39 INFO  org.springframework.boot.StartupInfoLogger.logStarted:57 - Started AxonApplication in 36.376 seconds (JVM running for 39.958)
...
2019-03-08 13:56:32 INFO  com.axon.concurrency.user.web.UserController.createNewUser:67 - Start
2019-03-08 13:56:32 INFO  com.axon.concurrency.user.command.handler.UserCommandHandler.handleUserCreationCommand:69 - Start
2019-03-08 13:56:32 DEBUG com.axon.concurrency.user.command.handler.UserCommandHandler.handleUserCreationCommand:72 - New User Created : ID : 1; UserName : TestUser-01; Age : 11; Version : null
2019-03-08 13:56:32 INFO  com.axon.concurrency.user.command.handler.UserCommandHandler.handleUserCreationCommand:73 - End
Hibernate: insert into user (last_event_sequence_number, version, age, user_name, id) values (?, ?, ?, ?, ?)
2019-03-08 13:56:32 INFO  com.axon.concurrency.user.web.UserController.createNewUser:76 - End
2019-03-08 14:00:56 INFO  com.axon.concurrency.user.web.UserController.updateUser:82 - Start
2019-03-08 14:00:56 INFO  com.axon.concurrency.user.command.handler.UserCommandHandler.handleUserModifyCommand:79 - Start
Hibernate: select user0_.id as id1_0_0_, user0_.last_event_sequence_number as last_eve2_0_0_, user0_.version as version3_0_0_, user0_.age as age4_0_0_, user0_.user_name as user_nam5_0_0_ from user user0_ where user0_.id=?
2019-03-08 14:00:56 DEBUG com.axon.concurrency.user.command.handler.UserCommandHandler.handleUserModifyCommand:82 - User Found : ID : 1; UserName : TestUser-01; Age : 11; Version : 0; versionInitial : 0
2019-03-08 14:00:56 DEBUG com.axon.concurrency.user.command.handler.UserCommandHandler.handleUserModifyCommand:85 - User Modified to : ID : 1; UserName : TestUser-02; Age : 12; Version : 0
2019-03-08 14:00:56 DEBUG com.axon.concurrency.user.command.handler.UserCommandHandler.handleUserModifyCommand:88 - User Queried : ID : 1; UserName : TestUser-02; Age : 12; Version : 0; versionQueried : 0 .
2019-03-08 14:00:56 DEBUG com.axon.concurrency.user.command.handler.UserCommandHandler.handleUserModifyCommand:90 - Thread Sleeping for 60 seconds
清单 16-12
微服务 1 在休眠模式下的更新操作
```

清单 16-13 展示了受图 16-12 中 PUT 方法影响的微服务 2 的控制台输出。

```
D:\binil\gold\pack03\ch16\ch16-02\Ax2-Cuncurrency-Test>run2
D:\binil\gold\pack03\ch16\ch16-02\Ax2-Cuncurrency-Test>java -Dserver.port=8081 -jar .\target\Axon-Concurrency-test-0.0.1-SNAPSHOT.jar
.   ____          _            __ _ _
/\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
\\/  ___)| |_)| | | | | || (_| |  ) ) ) )
'  |____| .__|_| |_|_| |_\__, | / / / /
=========|_|==============|___/=/_/_/_/
:: Spring Boot ::        (v1.3.5.RELEASE)
...
2019-03-08 13:53:39 INFO  org.springframework.boot.StartupInfoLogger.logStarted:57 - Started AxonApplication in 29.656 seconds (JVM running for 31.918)
...
2019-03-08 14:01:01 INFO  com.axon.concurrency.user.web.UserController.updateUser:82 - Start
2019-03-08 14:01:01 INFO  com.axon.concurrency.user.command.handler.UserCommandHandler.handleUserModifyCommand:79 - Start
Hibernate: select user0_.id as id1_0_0_, user0_.last_event_sequence_number as last_eve2_0_0_, user0_.version as version3_0_0_, user0_.age as age4_0_0_, user0_.user_name as user_nam5_0_0_ from user user0_ where user0_.id=?
2019-03-08 14:01:01 DEBUG com.axon.concurrency.user.command.handler.UserCommandHandler.handleUserModifyCommand:82 - User Found : ID : 1; UserName : TestUser-01; Age : 11; Version : 0; versionInitial : 0
2019-03-08 14:01:01 DEBUG com.axon.concurrency.user.command.handler.UserCommandHandler.handleUserModifyCommand:85 - User Modified to : ID : 1; UserName : TestUser-03; Age : 13; Version : 0
2019-03-08 14:01:01 DEBUG com.axon.concurrency.user.command.handler.UserCommandHandler.handleUserModifyCommand:88 - User Queried : ID : 1; UserName : TestUser-03; Age : 13; Version : 0; versionQueried : 0
2019-03-08 14:01:01 DEBUG com.axon.concurrency.user.command.handler.UserCommandHandler.handleUserModifyCommand:90 - Thread Sleeping for 60 seconds
清单 16-13
微服务 2 在休眠模式下的更新操作
```

仔细查看清单 16-12 和清单 16-13，你会发现两个客户端都尝试使用不同的值来执行更新操作。
现在请回顾第 13 章中“事务隔离控制方法”一节的内容，我在其中定义了乐观锁。乐观锁采用一种务实的方法，鼓励客户端保持“乐观”，认为数据在使用过程中不会发生变化，从而允许它们并发访问同一数据。等待两个处于休眠模式的相应更新处理被唤醒、完成更新并尝试提交事务。你会看到其中一个客户端成功完成了更新。

清单 16-14 展示了微服务 1 的控制台输出，该服务最先被唤醒（因为它最先进入休眠状态），并成功提交了更新。

```
D:\binil\gold\pack03\ch16\ch16-02\Ax2-Cuncurrency-Test>run1
D:\binil\gold\pack03\ch16\ch16-02\Ax2-Cuncurrency-Test>java -Dserver.port=8080 -jar .\target\Axon-Concurrency-test-0.0.1-SNAPSHOT.jar
.   ____          _            __ _ _
/\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
\\/  ___)| |_)| | | | | || (_| |  ) ) ) )
'  |____| .__|_| |_|_| |_\__, | / / / /
=========|_|==============|___/=/_/_/_/
:: Spring Boot ::        (v1.3.5.RELEASE)
...
2019-03-08 13:53:39 INFO  org.springframework.boot.StartupInfoLogger.logStarted:57 - Started AxonApplication in 36.376 seconds (JVM running for 39.958)
...
2019-03-08 14:00:56 DEBUG com.axon.concurrency.user.command.handler.UserCommandHandler.handleUserModifyCommand:90 - Thread Sleeping for 60 seconds
2019-03-08 14:01:57 DEBUG com.axon.concurrency.user.command.handler.UserCommandHandler.handleUserModifyCommand:98 - User Queried Again : ID : 1; UserName : TestUser-02; Age : 12; Version : 0; versionQueriedAgain : 0
2019-03-08 14:01:57 INFO  com.axon.concurrency.user.command.handler.UserCommandHandler.handleUserModifyCommand:99 - End
Hibernate: update user set last_event_sequence_number=?, version=?, age=?, user_name=? where id=? and version=?
2019-03-08 14:01:57 INFO  com.axon.concurrency.user.web.UserController.updateUser:91 - End
清单 16-14
微服务 1 的更新操作已提交
```

清单 16-15 展示了微服务 2 的控制台输出，该服务第二个被唤醒（因为它第二个进入休眠状态），但未能成功提交更新。它抛出了一个异常，如清单 16-15 所示。这是因为在乐观并发访问中，如果代表某个特定客户端的事务想要执行更新，那么只有当最初提供给客户端的数据与数据库中当前的数据相同时，该更新才会被提交。



```
D:\binil\gold\pack03\ch16\ch16-02\Ax2-Cuncurrency-Test>run2
D:\binil\gold\pack03\ch16\ch16-02\Ax2-Cuncurrency-Test>java -Dserver.port=8081 -jar .\target\Axon-Concurrency-test-0.0.1-SNAPSHOT.jar
.   ____          _            __ _ _
/\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
\\/  ___)| |_)| | | | | || (_| |  ) ) ) )
'  |____| .__|_| |_|_| |_\__, | / / / /
=========|_|==============|___/=/_/_/_/
:: Spring Boot ::        (v1.3.5.RELEASE)
...
2019-03-08 14:01:01 DEBUG com.axon.concurrency.user.command.handler.UserCommandHandler.handleUserModifyCommand:90 - Thread Sleeping for 60 seconds
2019-03-08 14:02:01 DEBUG com.axon.concurrency.user.command.handler.UserCommandHandler.handleUserModifyCommand:98 - User Queried Again : ID : 1; UserName : TestUser-03; Age : 13; Version : 0; versionQueriedAgain : 0
2019-03-08 14:02:01 INFO  com.axon.concurrency.user.command.handler.UserCommandHandler.handleUserModifyCommand:99 - End
Hibernate: update user set last_event_sequence_number=?, version=?, age=?, user_name=? where id=? and version=?
2019-03-08 14:02:01 ERROR com.axon.concurrency.user.web.UserController.updateUser:88 - Command execution resulted in a checked exception that was not declared on the gateway
org.axonframework.commandhandling.CommandExecutionException: Command execution resulted in a checked exception that was not declared on the gateway
at org.axonframework.commandhandling.gateway.GatewayProxyFactory$WrapNonDeclaredCheckedExceptions.invoke(GatewayProxyFactory.java:524)
...
at java.lang.Thread.run(Unknown Source)
Caused by: javax.persistence.OptimisticLockException: Row was updated or deleted by another transaction (or unsaved-value mapping was incorrect) : [com.axon.concurrency.user.model.User#1]
...
at org.axonframework.repository.GenericJpaRepository.doSaveWithLock(GenericJpaRepository.java:78)
at org.axonframework.repository.LockingRepository.doSave(LockingRepository.java:128)
at org.axonframework.repository.AbstractRepository$SimpleSaveAggregateCallback.save(AbstractRepository.java:183)
at org.axonframework.unitofwork.DefaultUnitOfWork$AggregateEntry.saveAggregate(DefaultUnitOfWork.java:322) .
...
Caused by: org.hibernate.StaleObjectStateException: Row was updated or deleted by another transaction (or unsaved-value mapping was incorrect) : [com.axon.concurrency.user.model.User#1]
...
at org.hibernate.internal.SessionImpl.flush(SessionImpl.java:1258)
at org.hibernate.jpa.spi.AbstractEntityManagerImpl.flush(AbstractEntityManagerImpl.java:1335)
... 84 more
2019-03-08 14:02:01 INFO  com.axon.concurrency.user.web.UserController.updateUser:91 - End
清单 16-15
微服务 2 的更新操作已回滚
```

如果你查询数据库，可以验证只有成功的那一个客户端尝试的更新生效了，如清单 16-16 所示。

```
mysql> select ∗ from user;
+----+----------------------------+---------+------+-------------+
| id | last_event_sequence_number | version | age  | user_name   |
+----+----------------------------+---------+------+-------------+
|  1 |                       NULL |       1 |   12 | TestUser-02 |
+----+----------------------------+---------+------+-------------+
1 row in set (0.00 sec)
mysql> .
清单 16-16
仅一个客户端更新成功
```

总结
为高可用性和可扩展性进行规划既是一门艺术，也是一项工程活动。针对特定场景，可能存在不止一种合适的设计，而选择正确的设计并兼顾未来需求是一项具有挑战性的任务。本章涵盖了一些技术，从简单的 HA 设计场景到更高级、更复杂的场景。在此过程中，你还重新审视了 CQRS 模式，并剖析了独立扩展读写节点的核心方面。虽然扩展读节点相对直接，但扩展写节点则不然，因为在更改实体状态时需要处理并发问题。然而，没有什么是不可能的，你看到了利用你在第 14 章学到的**事务控制**基本原则来扩展写节点的实际代码。至此，你在本书的旅程中又达到了一个里程碑。积累到目前为止获得的所有知识，现在是时候研究更严肃的代码了，你将在下一章中看到完整的端到端企业级微服务应用程序。休息一下。当你准备好时，开始下一章。

脚注

17. Axon CQRS 微服务
电子商务

到目前为止，你已经学习了来自多个框架的精选且最低限度的技术细节，包括但不限于 Spring Boot、Spring Cloud 和 Axon CQRS。现在让我们看看它们如何协同工作，以便你能够架构和构建真实世界的应用程序。为此，你将设计和构建一个基本的电子商务网站。
正如你现在可以体会到的，与传统的或单体架构相比，微服务架构并不简单或直接，因此即使是你将在本章中构建的小型电子商务网站也并非微不足道，需要你投入时间和精力，以便理解所有关键方面。为了帮助你实现这一目标，我不会重复你在前几章中已经看到和感受到的任何方面，例如高可用性、高可扩展性等。这意味着你将只使用微服务的单个实例进行操作。然而，这并不限制你通过实例化同一微服务的更多实例来以自己的方式操作示例。
其次，由于本章篇幅较长，我将只介绍几个关键和基本的流程。我省略了其他方面，例如支付集成等。尽管没有任何限制阻止你扩展代码以添加更多功能使其可用于生产环境（这本身就是微服务的目标之一），但本章示例的当前范围是极简的，我建议你首先按照本章规定的方式测试示例，以便你对底层机制有相当好的理解。你可以在后续的测试轮次中进行猴子测试，使其失败。

你将在本章中探讨以下内容：

*   电子商务应用程序的传统单体架构
*   同一电子商务应用程序在使用微服务架构时的样子
*   使用 CQRS 为微服务电子商务应用程序选定的主要用例的详细设计
*   微服务电子商务应用程序的完整代码
*   如何配置和运行电子商务应用程序的所有微服务
*   如何执行上述选定的用例

注意
本章使用 Axon 2 构建示例。在第 19 章中，我们将使用更高版本的 Axon 重构同一个应用程序。



重新审视传统电子商务应用
让我们从在线电子商务网站的基本需求开始讨论。在了解这些需求后，你将看到，如果采用传统或单体架构进行设计，基本构建模块的典型逻辑架构会是什么样子。在下一节中，你将了解如果改用基于微服务的方法重新设计架构，需要进行哪些更改。

电子商务业务概述
示例电子商务企业名为 ACME Shop。ACME 拥有在线业务，客户或最终用户可以在该网站上进行在线购物。ACME 的后台员工也通过同一个网站来处理客户订单。
ACME Shop 专门销售时尚配饰，并将其产品组合归类到不同的类别下，称为产品类别。在每个产品类别下，ACME Shop 会列出待售的产品集。当（匿名）用户访问 ACME 主页时，所有产品类别都会被列出，并且默认情况下，其中一个类别下的产品会被展开并进一步列出。
用户可以点击某个产品来查看产品详情。产品详情还会显示可用库存。用户可以进一步指定要购买所选产品的数量，并将其添加到购物车。一旦添加到购物车，它就会显示在网站的用户购物车图标中。用户可以选择其购物车并进行结算。下一步，用户可以确认购买。
要确认购买，用户必须登录。如果用户之前创建过用户档案，他/她可以使用这些凭据登录。新用户可以点击“新用户”链接来新建用户档案。创建档案后，用户可以再次选择购物车进行结算。他这次可以登录并确认购买。
购买成功后，将创建一个订单。订单创建后，可以在相应子菜单下的“您的订单”链接中查看。
在订单被履行之前，用户可以取消订单。
ACME 后台员工使用超级凭据登录，与同一个网站交互以处理客户订单。一旦用户创建了订单，下一步就是后台员工发货。订单发货后，用户无法取消订单。相反，用户可以在其“订单”下的“进行中”子菜单中查看“已发货状态”。
订单履行流程的下一阶段是订单交付。ACME 后台员工必须进入“待交付”子菜单并执行操作。根据你是想模拟成功交付还是失败情况，有两个操作：“已交付”或“交付失败”。

重新审视电子商务架构

上述业务应用的典型单体架构如图 17-1 所示。

![../images/477630_1_En_17_Chapter/477630_1_En_17_Fig1_HTML.jpg](img/477630_1_En_17_Fig1_HTML.jpg)

图 17-1
单体电子商务逻辑架构

该 Web 应用通常是一个单一的 Web 归档（`.war`）文件。来自 Web 应用的组件向中间层可用的服务发送请求。中间层的逻辑架构描绘了多个模块，这些模块与 Web 交互，并且通常使用请求-响应风格的同步通信相互交互。

图 17-2 试图描绘单体架构中组件之间的相互依赖关系。请注意，图 17-1 和图 17-2 是代表性的，并非详尽无遗，仅用于说明组件之间的紧密耦合。

![../images/477630_1_En_17_Chapter/477630_1_En_17_Fig2_HTML.jpg](img/477630_1_En_17_Fig2_HTML.jpg)

图 17-2
单体电子商务组件依赖关系

电子商务微服务应用
在前面的章节中，我已经描述了基于微服务架构的必要性，因此这里不再重复。相反，让我们重新审视上一节中描述的架构，看看需要改变什么。
很明显，在新架构中，你需要类似或增强的功能，包括功能性和非功能性。同时，你希望减少上一节中描述的架构的不良影响，那么让我们开始吧。

基于微服务的电子商务架构的逻辑架构

重构时首先要考虑的是将功能分离到微服务中，如图 17-3 所示。

![../images/477630_1_En_17_Chapter/477630_1_En_17_Fig3_HTML.jpg](img/477630_1_En_17_Fig3_HTML.jpg)

图 17-3
电子商务微服务依赖关系

微服务之间几乎没有或完全没有直接依赖关系；相反，微服务通过基于事件的基础设施相互通信。所有这些微服务都将部署在独立的进程中，并且可能部署在不同的物理节点上，也可能不部署。

BASE 中的 ACID

再次比较图 17-2 和图 17-3 中的架构。仔细观察会发现两个值得注意的方面：

*   **复制的读模型**：你可以看到库存数据被复制并保存在多个微服务级别。这是为了处理宿主微服务不拥有的数据的查询需求，这正是在第 12 章的 CQRS 模式中详细讨论的内容。为了明确起见，当显示产品详情时，屏幕上也需要显示可用库存信息，这可以通过维护在产品微服务级别的库存物化视图来完成。

*   **增强一致性的领域亲和性**：图 17-2 将订单和库存描绘为两个独立的领域。然而，在图 17-3 的重新设计中，你可以看到这两个领域实体被整合在一起，并维护在同一个空间（进程或节点）中。原因是希望在订单和库存领域实体之间实现更强的数据一致性。

虽然你希望在这两个实体之间实现类似 ACID 的一致性，但希望避免跨节点的分布式事务。但这里有一个警告：无论出于何种原因，你试图将单体拆分为微服务，同样的力量现在又施加了约束，要求以一致性为代价将它们重新合并！虽然你不想将所有东西都合并回去以获得零分区的强一致性，但让我们暂时保留订单和库存，并接受“BASE 中的 ACID”这一场景。
别担心；在第 19 章中，当你研究 Axon 3 时，你将看到一个进一步重构的设计，其中具有真正的 BASE 事务特性。

电子商务微服务的设计

电子商务应用的业务领域对象模型（BDOM）或公共信息模型（CIM）如图 17-4 所示。

![../images/477630_1_En_17_Chapter/477630_1_En_17_Fig4_HTML.jpg](img/477630_1_En_17_Fig4_HTML.jpg)

图 17-4
电子商务领域对象模型



尽管你需要基于微服务架构设计电商应用，但业务领域对象模型（BDOM）仍遵循传统的 UML 类图风格。因此，上方的电商类图所展示的依赖关系，在确定微服务边界时可能无法一一对应。相反，当你基于领域驱动设计确定微服务边界时，此处展示的部分依赖关系将跨越微服务边界被拆分，在这种情况下，某些微服务必须维护由外部微服务所拥有的数据的物化视图。

**基于微服务的电商架构的技术架构**

在了解了构建基于微服务的电商应用所采用方法背后的原理后，你可以得出技术架构的初步视图，如图 17-5 所示。

![../images/477630_1_En_17_Chapter/477630_1_En_17_Fig5_HTML.jpg](img/477630_1_En_17_Fig5_HTML.jpg)

图 17-5
电商微服务技术架构

你正试图移除所有直接的微服务间依赖关系，并通过**事件驱动架构**来引导所有微服务间的通信。为此，你使用了 Axon 的集群事件总线。你利用多语言持久化来存储数据，以便可以根据各个微服务使用数据的方式选择多种数据存储技术。对于 Shipping 微服务和 Delivery 微服务，你未使用任何持久化来简化应用，因此它们在图 17-5 中被模糊处理了。

**设计与编码电商微服务应用**

在进入设计与编码之前，让我们明确你将理解此示例的边界。本书的范围仅限于中间件微服务上下文中的微服务。为了构建一个完整的电商微服务应用工作示例，你还需要基于 Web 框架开发基于 HTML 的屏幕。Web 应用的所有这些元素都包含在一个未在图 17-5 中显示的微服务中；不过，我将在后续章节中介绍该微服务。对构建 HTML 屏幕及相关 Web 技术的详细审查或解释不在本书的讨论范围之内。建议读者参考其他教科书以获取关于如何构建 Web 应用的详细解释。然而，示例中提供的用于构建 Web 应用的代码是简单的，因此其所需部分应该是不言自明的，你可以浏览它们。

排除 Web 应用，还有另外七个应用微服务，如图 17-5 所示。同样，我不会列出微服务中的每个 Java 文件；相反，我将逐一介绍重要的用例，并解释该用例所需的服务器端或微服务代码。

应用的完整代码位于`folder ch17\ch17-01\Ax2-Ecom-No-Security`。

**列出产品类别和产品**

默认选中类别下的产品类别和产品将显示在 ACME Shop 主页上。清单 17-1 展示了这部分代码。

```
@RepositoryRestResource(collectionResourceRel = "categories", path = "categories")
public interface ProductCategoryRepository extends MongoRepository  {
}
清单 17-1
ProductCategoryRepository (ch17\ch17-01\Ax2-Ecom-No-Security\Ecom-product\src\main\java\com\acme\ecom\product\repository\ ProductCategoryRepository.java)
```

该仓库是一个接口，允许你执行涉及 ProductCategory 对象的各种操作。它通过扩展 Mongo Repository（一个特定于 Mongo 的 Repository 接口，该接口又扩展了 Spring Data Commons 中定义的 PagingAndSortingRepository 接口）来获得这些操作。在运行时，Spring Data REST 将自动创建此接口的实现。然后，它将使用`@` `RepositoryRestResource`注解来指示 Spring MVC 在`/categories`创建 RESTful 端点：

`product_service.js`
按如下方式检索所有产品类别：

```
http.get(sessionStorage.getItem('apiUrl')+'/product/categories/')
```

注意
Web 应用的所有代码都在文件夹`ch17\ch17-01\Ax2-Ecom-No-Security\Ecom-web\src\main\resources\static`中。

接下来，你应该能够使用清单 17-2 中的代码检索某个产品类别下的所有产品。

```
@RestController
public class ProductRestController {
@RequestMapping(value = "/productsByCategory", method = RequestMethod.GET,
produces = { MediaType.APPLICATION_JSON_VALUE })
public ResponseEntity>>getAllProductsByCategory
(@RequestParam("category") String category) {
List products =
productRepository.findByProductCategoryName(category);
Link links[] = { };
if (products.isEmpty()) {
return new ResponseEntity>>(
HttpStatus.NOT_FOUND);
}
List> list = new ArrayList>();
addLinksToProduct(products, list);
Resources> productRes =
new Resources>(list, links);
return new ResponseEntity>>(
productRes, HttpStatus.OK);
}
}
清单 17-2
productsByCategory (ch17\ch17-01\Ax2-Ecom-No-Security\Ecom-product\src\main\java\com\acme\ecom\product\controller\ProductRestController.java)
```

`product_service.js`
按如下方式检索某个产品类别下列出的所有产品：

```
http.get(sessionStorage.getItem('apiUrl')+'/product/productsByCategory?category='+category)
```

**列出与库存数据融合的产品详情**

下一个主要用例是当用户点击特定产品以列出产品详情时检索信息。可以使用清单 17-3 中的代码检索产品详情。

```
@RestController
public class ProductRestController {
@RequestMapping(value = "/products/{id}", method = RequestMethod.GET,
produces = MediaType.APPLICATION_JSON_VALUE)
public ResponseEntity> getProduct(
@PathVariable("id") String id) {
Product product = productRepository.findOne(id);
if (product == null) {
return new ResponseEntity>(HttpStatus.NOT_FOUND);
}
Resource productRes = new Resource(
product,new Link[]{linkTo(methodOn(ProductRestController.class).
getProduct(product.getId())).withSelfRel(), linkTo(
ProductRestController.class).slash("productImg").slash(
product.getImgUrl()).withRel("imgUrl")});
return new ResponseEntity>(productRes,
HttpStatus.OK);
}
}
清单 17-3
获取产品详情 (ch17\ch17-01\Ax2-Ecom-No-Security\Ecom-product\src\main\java\com\acme\ecom\product\controller\ProductRestController.java)
```

`product_service.js`
按如下方式检索某个产品类别下列出的所有产品：

```
http.get(sessionStorage.getItem('apiUrl')+'/product/products/'+id)
```



产品详情应与库存信息整合，以便用户判断能否将该产品的任意数量添加到购物车。图 17-5 所示的架构表明，库存信息仅存在于“核心”微服务中，而该微服务位于产品微服务之外。
你可以做出一个架构决策，允许浏览器通过请求产品微服务来获取产品详情，然后请求核心微服务来获取该特定产品的库存信息，从而在客户端层进行数据整合。
但微服务的原则指出，微服务应自给自足且独立。这意味着，如果你考虑产品微服务和核心微服务，每个微服务都应能独立运行和提供服务，无论另一个微服务是否正常运行。如果核心微服务宕机，ACME 在线商店仍应能以功能缩减的方式运行，允许用户访问主页、浏览产品类别、列出某类别下的产品、查看所选产品的详情（或许也包括库存信息！）、选择并添加商品到购物车等。或许只有创建订单功能会在核心微服务重新启动并运行后才能正常工作。因此，你将采用一种不同的架构方法，允许产品微服务维护一个库存的物化视图，这是一个只读副本。

要从这个只读副本中检索产品的库存详情，你需要使用库存仓库，如清单 17-4 所示。

```
@RepositoryRestResource(collectionResourceRel = "inventory",
path = "inventory")
public interface InventoryRepository extends
MongoRepository  {
public Inventory findByInventoryId(
@Param("inventoryId") Long  inventoryId);
public Inventory findBySku(@Param("sku") String  sku);
}
product_service.js 检索某产品类别下列出的所有产品，如下所示：
http.get(sessionStorage.getItem('apiUrl')+'/product/inventory/search/findBySku?sku='+productCode)
清单 17-4
库存只读仓库 (ch17\ch17-01\Ax2-Ecom-No-Security\Ecom-product\src\main\java\com\acme\ecom\product\inventory\repository\InventoryRepository.java)
```

现在，从上述两个 API 源和客户端层整合数据就很简单了。

**将产品添加到购物车**
这是一个直接的实现，将选定的待订购商品添加到 `org.springframework.cache.CacheManager` 中，然后再检索出来，因此此处不展示代码片段。代码位于文件夹 `ch17\ch17-01\Ax2-Ecom-No-Security\Ecom-cart` 中。

**创建新订单**

从现在开始要探索的用例并非简单，因为微服务之间的控制流有多个方向。如前所述，你不会使用任何同步风格的微服务间通信；相反，你将利用 Axon CQRS 事件驱动基础设施来促进端到端的控制流。你将使用类似于图 17-6 所示的表示方式，以便更容易理解用例执行过程中的控制流和状态变化。

![../images/477630_1_En_17_Chapter/477630_1_En_17_Fig6_HTML.jpg](img/477630_1_En_17_Fig6_HTML.jpg)

图 17-6
创建新订单

参考图 17-6，流程已标注并说明如下。

1.  `order_service.js`
    通过 HTTP 发送 REST JSON 请求以创建新订单：

```
    $http.post(sessionStorage.getItem('apiUrl')+'/core/order/',orderDTO)
    ```

此请求将到达 API 网关。

2.  API 网关通过查看服务注册表来解析 URL，并将请求定向到 Ecom-core 微服务中的订单 REST 控制器。

3.  订单 REST 控制器创建一个新的 `OrderCreateCommand`，并将其发送到命令网关并等待。参见清单 17-5。

```
    @RestController
    @RequestMapping("/order")
    public class OrderController {
    @Autowired
    private CommandGateway commandGateway;
    @RequestMapping(method = RequestMethod.POST)
    @Transactional
    @ResponseBody
    public OrderCreationStatus createOrder(@RequestBody OrderDTO orderDTO) {
    OrderCreateCommand orderCommand = new OrderCreateCommand(
    orderDTO.getUserId(), orderDTO.getLineItems());
    commandGateway.sendAndWait(orderCommand);
    return OrderCreationStatus.SUCCESS;
    }
    }
    清单 17-5
    用于创建新订单的订单控制器 (ch17\ch17-01\Ax2-Ecom-No-Security\Ecom-core\src\main\java\com\acme\ecom\core\web\OrderController.java)
    ```

4.  命令网关将 `OrderCreateCommand` 传递到命令总线。

5.  `OrderCommandHandler` 从命令总线消费 `OrderCreateCommand`。参见清单 17-6。

```
    @Component
    public class OrderCommandHandler {
    @Autowired
    @Qualifier(value = "orderRepository")
    private Repository orderRepository;
    @CommandHandler
    public void handleNewOrder(OrderCreateCommand orderCreatedCommand) {
    Order order = new Order(Long.valueOf(new Random().nextInt()));
    order.setOrderDate(new Date());
    order.setOrderStatus(OrderStatus.PAID);
    order.setUserId(orderCreatedCommand.getUserId());
    double total = 0;
    if (orderCreatedCommand.getLineItems() != null) {
    for (LineItemDTO lineItemDto :
    orderCreatedCommand.getLineItems()) {
    if (lineItemDto.getInventoryId() != null) {
    LineItem lineItem = new LineItem(new Random().nextLong(),
    lineItemDto.getProductId(), lineItemDto.getQuantity(),
    lineItemDto.getPrice(), lineItemDto.getInventoryId());
    total = total + lineItemDto.getPrice();
    order.addLineItem(lineItem);
    Inventory inventory = inventoryRepository.load(lineItemDto.getInventoryId());
    inventory.updateProductStock(lineItemDto.getQuantity(),
    ProductStockOperation.DEPRECIATE);
    }
    }
    }
    order.setTotal(total);
    order.notifyOrderCreation();
    orderRepository.add(order);
    }
    }
    清单 17-6
    OrderCommandHandler 的 handleNewOrder 方法 (ch17\ch17-01\Ax2-Ecom-No-Security\Ecom-core\src\main\java\com\acme\ecom\core\order\command\handler\OrderCommandHandler.java)
    ```

命令处理过程是原子性的，因此它要么完全处理，要么完全不处理。Axon 框架使用工作单元（UoW）来跟踪命令处理器执行的操作。命令处理器完成后，Axon 会尝试提交在 UoW 中注册的操作。这包括将修改后的聚合存储到各自的仓库中，并在事件总线上发布事件。然而，UoW 并不能替代事务。UoW 仅确保在命令处理器成功执行后，对聚合所做的更改被存储。如果在存储某个聚合时发生错误，已存储的任何聚合都不会回滚。你可以将事务绑定到 UoW。像 `SimpleCommandBus` 和 `DisruptorCommandBus` 这样的 `CommandBus` 实现允许你配置一个事务管理器。该事务管理器用于创建事务，并绑定到用于管理命令处理过程的 UoW。如清单 17-7 所示。



```
    @Configuration
    @RefreshScope
    public class EcomCoreAppConfiguration {
    @Bean
    public SimpleCommandBus commandBus() {
    SimpleCommandBus simpleCommandBus = new SimpleCommandBus();
    simpleCommandBus.setDispatchInterceptors(
    Arrays.asList(new BeanValidationInterceptor()));
    SpringTransactionManager transcationMgr =
    new SpringTransactionManager(txManager);
    simpleCommandBus.setTransactionManager(transcationMgr);
    return simpleCommandBus;
    }
    }
    清单 17-7
    将事务绑定到工作单元 (ch17\ch17-01\Ax2-Ecom-No-Security\Ecom-core\src\main\java\com\acme\ecom\core\EcomCoreAppConfiguration.java)
    ```

此处，`SpringTransactionManager` 是一个 `TransactionManager` 实现，它使用 `PlatformTransactionManager` 作为底层事务管理器。

请注意，你在单个事务中使用了两个聚合实体（库存和订单），但由于这两个聚合实体由同一节点上的同一资源管理器管理，因此不涉及分布式事务。

6.  `OrderCommandHandler` 首先在库存 AXON 聚合中扣减可用库存。请参见清单 17-8。

```
    @Entity
    @Table(name = "INVENTORY")
    public class Inventory extends AbstractAggregateRoot {
    public void updateProductStock(Integer count,
    ProductStockOperation stockoperation) {
    if (stockoperation.equals(ProductStockOperation.DEPRECIATE)) {
    if (this.quantity - count >= 0) {
    this.quantity = this.quantity - count;
    } else {
    throw new OutOfStockException(this.id);
    }
    } else {
    this.quantity = this.quantity + count;
    }
    registerEvent(new InventoryUpdateEvent(id, sku, quantity));
    }
    }
    清单 17-8
    库存聚合 updateProductStock (ch17\ch17-01\Ax2-Ecom-No-Security\Ecom-core\src\main\java\com\acme\ecom\core\inventory\model\Inventory.java)
    ```

当你查看产品列表页面或产品详情页面时，看到的库存数据来自 Ecom-product 微服务中库存物化视图的缓存数据。然而，当你创建订单时，所需库存会与可用库存进行验证，这是针对库存聚合实体（即库存的写模型）进行的。类似场景在许多业务流程中很常见，例如销售酒店房间和航班座位。当你开始搜索酒店房间或航班座位时，房间或座位的初始可用性通常是“近乎准确”的近似值，可能来自缓存或中间供需网关。当实际预订完成时，总会对请求的座位或房间数量与单一事实来源（即写模型）中的实际可用性进行即时或最终的验证。

7.  在扣减库存时，库存聚合会向集群事件总线注册一个 `InventoryUpdateEvent`。

8.  `OrderCommandHandler` 在第 5 步消费 `OrderCreateCommand` 时，在成功扣减库存后还会创建一个新订单。请参见清单 17-9。

```
    @Entity(name="order")
    @Table(name = "CUSTOMER_ORDER")
    public class Order extends AbstractAggregateRoot {
    @OneToMany(cascade = CascadeType.ALL, mappedBy="order", orphanRemoval=true)
    private Set lineItems;
    public void addLineItem(LineItem lineItem){
    if(this.lineItems==null){
    this.lineItems=new HashSet();
    }
    lineItem.setOrder(this);
    this.lineItems.add(lineItem);
    }
    public void notifyOrderCreation() {
    List lineItemDTOs = new ArrayList();
    for (LineItem lineItem : lineItems) {
    lineItemDTOs.add(new LineItemDTO(lineItem.getProduct(),
    lineItem.getPrice(), lineItem.getQuantity(),
    lineItem.getInventoryId()));
    }
    registerEvent(new OrderCreatedEvent(id, userId, orderStatus.name(),
    total, orderDate, lineItemDTOs));
    }
    }
    清单 17-9
    订单 notifyOrderCreation (ch17\ch17-01\Ax2-Ecom-No-Security\Ecom-core\src\main\java\com\acme\ecom\core\order\model\Order.java)
    ```

9.  一旦订单聚合收到新订单创建的通知，它就会向集群事件总线注册一个 `OrderCreatedEvent`。

10. 对于每个新的 `OrderCreatedEvent`，都会实例化一个新的订单处理 Saga 实例，如清单 17-10 所示。

```
    public class OrderProcessSaga extends AbstractAnnotatedSaga {
    private Long orderId;
    @StartSaga
    @SagaEventHandler(associationProperty = "orderId")
    public void handleOrderCreationEvent(OrderCreatedEvent orderCreatedEvent) {
    this.orderId = orderCreatedEvent.getOrderId();
    }
    }
    清单 17-10
    在新订单创建时启动 Saga (ch17\ch17-01\Ax2-Ecom-No-Security\Ecom-core\src\main\java\com\acme\ecom\core\order\saga\handler\OrderProcessSaga.java)
    ```

11. 在第 7 步扣减库存时，库存聚合会向集群事件总线注册一个 `InventoryUpdateEvent`。此 `InventoryUpdateEvent` 由 Ecom-product 微服务中的库存事件处理器消费。

12. Ecom-product 微服务中的 `InventoryEventHandler` 会刷新其自身的库存读模型物化视图，如清单 17-11 所示。

```
    @Component
    public class InventoryEventHandler {
    @Autowired
    private InventoryRepository inventoryRepository;
    @EventHandler
    public void handleInventoryUpdates(InventoryUpdateEvent event,
    Message eventMessage, @Timestamp DateTime moment) {
    Inventory inventory =
    inventoryRepository.findByInventoryId(event.getId());
    if(inventory == null){
    inventory=new Inventory();
    inventory.setInventoryId(event.getId());
    inventory.setSku(event.getSku());
    }else{
    logger.debug("Inventory  existing - updating...............");
    }
    inventory.setQuantity(event.getQuantity());
    inventoryRepository.save(inventory);
    }
    }
    清单 17-11
    产品 handleInventoryUpdates (ch17\ch17-01\Ax2-Ecom-No-Security\Ecom-product\src\main\java\com\acme\ecom\product\inventory\event\handler\InventoryEventHandler.java)
    ```

13. 在第 9 步中，当订单聚合收到新订单创建的通知时，它会向集群事件总线注册一个 `OrderCreatedEvent`。此 `OrderCreatedEvent` 由 Ecom-history 微服务中的订单历史事件处理器消费。

14. Ecom-history 微服务中的订单历史事件处理器会在 `Order History` 表中创建一条新记录。请参见清单 17-12。



```
    @Component
    public class OrderHistoryEventHandler {
    @Autowired
    private OrderHistoryRepository orderHistoryRepository;
    @EventHandler
    public void handleOrderCreationEvent(OrderCreatedEvent event,
    Message eventMessage, @Timestamp DateTime moment) {
    Order order =new Order();
    order.setOrderId(event.getOrderId());
    order.setUserId(event.getUserId());
    order.setCreationDate(new Date());
    order.setOrderStatus(event.getOrderStatus());
    List lineItems=new ArrayList();
    if(event.getLineItems()!=null){
    for(LineItemDTO lineItemDTO:event.getLineItems()){
    LineItem lineItem=new LineItem();
    lineItem.setProductId(lineItemDTO.getProductId());
    lineItem.setPrice(lineItemDTO.getPrice());
    lineItem.setQuantity(lineItemDTO.getQuantity());
    lineItems.add(lineItem);
    }
    }
    order.setLineItems(lineItems);
    orderHistoryRepository.save(order);
    }
    }
    清单 17-12
    handleOrderCreationEvent (ch17\ch17-01\Ax2-Ecom-No-Security\Ecom-history\src\main\java\com\acme\ecom\order\history\event\handler\OrderHistoryEventHandler.java)
    ```

稍后你还会看到，每当 Ecom-core 微服务中订单聚合的状态发生变化时，该历史记录表中的数据也会同步更新。如有需要，BI 或分析功能可以基于这些记录执行，因为它们位于与 Ecom-core 微服务中订单聚合相独立的微服务中，因此不会影响订单聚合的性能。
至此，“创建新订单”流程完成。稍后你将在“测试电子商务微服务用例”章节下的“创建新订单”小节中查看实际用例的执行过程。

取消订单

如果新创建的订单尚未进入履行流程，用户可以将其取消。图 17-7 描述了取消命令发起后，不同实体所经历的各种状态。

![../images/477630_1_En_17_Chapter/477630_1_En_17_Fig7_HTML.jpg](img/477630_1_En_17_Fig7_HTML.jpg)

图 17-7
取消订单

参考图 17-7，由 Web 应用发送 REST JSON 请求以取消订单的端到端流程可总结如下：

1.  order_service.js 通过 HTTP 发送 REST JSON 请求以取消订单：

```
    $http.delete(sessionStorage.getItem('apiUrl')+'/core/order/'+orderId)
    ```

该请求会到达 API 网关。

2.  API 网关通过查询服务注册表来解析 URL，并将请求定向到 Ecom-core 微服务中的 Order REST Controller。

3.  Order REST Controller 创建一个新的 OrderCancelCommand 并将其发送到命令网关。参见清单 17-13。

```
    @RestController
    @RequestMapping("/order")
    public class OrderController {
    @RequestMapping(value = "{orderId}", method = RequestMethod.DELETE)
    @Transactional
    @ResponseBody
    public void cancelOrder(@PathVariable Long orderId) {
    OrderCancelCommand orderCommand = new OrderCancelCommand(orderId);
    commandGateway.send(orderCommand);
    }
    }
    清单 17-13
    Order Controller cancelOrder (ch17\ch17-01\Ax2-Ecom-No-Security\Ecom-core\src\main\java\com\acme\ecom\core\web\OrderController.java)
    ```

4.  命令网关将 OrderCancelCommand 投递到命令总线。

5.  OrderCommandHandler 从命令总线消费 OrderCreateCommand。参见清单 17-14。

```
    @Component
    public class OrderCommandHandler {
    @CommandHandler
    public void handleOrderCancel(OrderCancelCommand orderCancelCommand) {
    Order order = orderRepository.load(orderCancelCommand.getOrderId());
    order.cancelOrder();
    rollbackInventory(order);
    }
    private void rollbackInventory(Order order){
    for(LineItem lineItem:order.getLineItems()){
    Inventory inventory =
    inventoryRepository.load(lineItem.getInventoryId());
    inventory.updateProductStock(lineItem.getQuantity(),
    ProductStockOperation.ADD);
    }
    }
    }
    清单 17-14
    OrderCommandHandler handleOrderCancel (ch17\ch17-01\Ax2-Ecom-No-Security\Ecom-core\src\main\java\com\acme\ecom\core\order\command\handler\OrderCommandHandler.java)
    ```

6.  OrderCommandHandler 从仓库中检索相应的订单并调用 cancelOrder。选定的订单聚合将其状态更改为 OrderStatus.CANCELLED。参见清单 17-15。

```
    @Entity(name="order")
    @Table(name = "CUSTOMER_ORDER")
    public class Order extends AbstractAggregateRoot {
    public void cancelOrder(){
    this.orderStatus=OrderStatus.CANCELLED;
    registerEvent(new OrderUpdatedEvent(this.id, orderStatus.name(),
    new Date(),null));
    registerEvent(new OrderCancelledEvent(this.id));
    }
    }
    清单 17-15
    Order Aggregate cancelOrder (ch17\ch17-01\Ax2-Ecom-No-Security\Ecom-core\src\main\java\com\acme\ecom\core\order\model\Order.java)
    ```

7.  订单聚合将 OrderUpdatedEvent 发送到集群事件总线。

8.  订单聚合还会将 OrderCancelledEvent 发送到集群事件总线。

9.  作为第 5 步的结果，OrderCommandHandler 还会检索与该订单关联的所有产品的库存项，并调用 updateProductStock。其目的是回滚库存以抵消取消订单的影响，从而使这些库存可以再次用于销售。清单 17-14 中的 rollbackInventory 描述了这一点。

参见清单 17-8 以了解库存聚合实体中的 updateProductStock 代码。

10. 在恢复库存时，每个库存聚合都会向集群事件总线注册一个 InventoryUpdateEvent。

11. 在第 8 步中注册的 OrderCancelledEvent 由订单流程 Saga 消费。参见清单 17-16。

```
    public class OrderProcessSaga extends AbstractAnnotatedSaga {
    private Long orderId;
    @EndSaga
    @SagaEventHandler(associationProperty = "orderId")
    public void handleOrderCanceledEvent(
    OrderCancelledEvent orderCancelledEvent) {
    // 不做任何操作
    }
    }
    清单 17-16
    OrderProcessSaga handleOrderCanceledEvent (ch17\ch17-01\Ax2-Ecom-No-Security\Ecom-core\src\main\java\com\acme\ecom\core\order\saga\handler\OrderProcessSaga.java)
    ```

Saga 的生命周期在调用 handleOrderCanceledEvent 处理器后结束，因为你已在该 Saga 的事件处理器上使用了 `@` `EndSaga` 注解。

让 Saga 在事件发生时采取行动很容易，就像你这里的 OrderCancelledEvent 事件一样。但是，如果你希望 Saga 在很长时间内没有任何事情发生时执行某些操作，该怎么办？在这种情况下，会使用截止时间。虽然信用卡支付的确认应在几秒内完成，但像你这里的在线订单，可能需要几小时或几天。你可以使用 EventScheduler 来调度一个事件以遵守截止时间。Axon 提供了两种 EventScheduler 实现：SimpleEventScheduler（纯 Java 实现）和 QuartzEventScheduler（使用 Quartz 2 作为底层调度机制）。我在本例中没有使用它们；这留给读者作为练习。



12.  在步骤 10 中恢复库存时，库存聚合体向集群事件总线注册一个`InventoryUpdateEvent`。该`InventoryUpdateEvent`由 Ecom-product 微服务中的`InventoryEventHandler`消费。

13.  Ecom-product 微服务中的`InventoryEventHandler`会刷新其自身的库存读取模型物化视图。清单 17-11 展示了执行此操作的`handleInventoryUpdates`代码。

14.  在步骤 7 中，当订单聚合体收到订单取消通知时，它会向集群事件总线注册一个`OrderUpdatedEvent`。该`OrderUpdatedEvent`由 Ecom-history 微服务中的 Order History Event Handler 消费。

15.  Ecom-history 微服务中的 Order History Event Handler 会更新`Order History`表中对应的 Order 记录。参见清单 17-17。

```
    @Component
    public class OrderHistoryEventHandler {
    @EventHandler
    public void handleOrderUpdatedEvent(OrderUpdatedEvent event,
    Message eventMessage, @Timestamp DateTime moment) {
    Order order =orderHistoryRepository.findByOrderId(event.getOrderId());
    order.setOrderStatus(event.getOrderStatus());
    if(event.getOrderStatus().equals("SHIPPED")){
    order.setShippedDate(event.getDate());
    }else if(event.getOrderStatus().equals("DELIVERED")){
    order.setDeliveredDate(event.getDate());
    }else if(event.getOrderStatus().equals("CANCELLED")){
    order.setCancelledDate(event.getDate());
    }else if(event.getOrderStatus().equals("DELIVERY_FAILED")){
    order.setDeliveryFailReason(event.getDescription());
    }
    orderHistoryRepository.save(order);
    }
    }
    清单 17-17
    OrderHistoryEventHandler handleOrderUpdatedEvent (ch17\ch17-01\Ax2-Ecom-No-Security\Ecom-history\src\main\java\com\acme\ecom\order\history\event\handler\OrderHistoryEventHandler.java)
    ```

至此，“取消订单”流程完成，你将在后续章节中查看实际用例的执行情况。

**发货已接收订单**

在“创建新订单”小节中，你了解了创建新订单的流程。所有已创建的新订单都将可供后台管理员领取并触发履行处理。订单履行的第一步是触发发货命令。图 17-8 展示了发货步骤中各实体的状态变化。

![../images/477630_1_En_17_Chapter/477630_1_En_17_Fig8_HTML.jpg](img/477630_1_En_17_Fig8_HTML.jpg)

图 17-8
发货订单

参考图 17-8，后台管理员使用 Web 应用通过 REST JSON 请求发货订单的端到端流程可总结如下：

1.  `order_service.js`
    通过 HTTP 发送 REST JSON 请求以发货新创建的订单：

```
    $http.post(sessionStorage.getItem('apiUrl')+'/shipping/shipping',shippingDTO)
    ```

该请求会命中 API 网关。

2.  API 网关通过查询服务注册表来解析 URL，并将请求路由到 Ecom-shipping 微服务中的 Order Shipping REST Controller。

3.  Order Shipping REST Controller 创建一个新的`OrderShippingCommand`并将其发送到命令网关。参见清单 17-18。

```
    @RestController
    @RequestMapping("/shipping")
    public class OrderShippingController {
    @Autowired
    private CommandGateway commandGateway;
    @RequestMapping(method = RequestMethod.POST)
    @ResponseBody
    public void shipOrder(@RequestBody ShippingDTO shippingDTO) {
    OrderShippingCommand orderCommand =
    new OrderShippingCommand(shippingDTO.getOrderId());
    commandGateway.send(orderCommand);
    }
    }
    清单 17-18
    OrderShippingController shipOrder (ch17\ch17-01\Ax2-Ecom-No-Security\Ecom-shipping\src\main\java\com\acme\ecom\shipping\web\OrderShippingController.java)
    ```

4.  命令网关将`OrderShippingCommand`投递到命令总线。

5.  `OrderShippingCommandHandler`从命令总线消费`OrderShippingCommand`。参见清单 17-19。

```
    @Component
    public class OrderShippingCommandHandler {
    @Autowired
    private EventBus eventBus;
    @CommandHandler
    public void handleOrderShipping(
    OrderShippingCommand orderShippingCommand) {
    eventBus.publish(asEventMessage(new OrderShippedEvent(
    orderShippingCommand.getOrderId(), new Date())));
    }
    }
    清单 17-19
    OrderShippingCommandHandler handleOrderShipping (ch17\ch17-01\Ax2-Ecom-No-Security\Ecom-shipping\src\main\java\com\acme\ecom\shipping\command\handler\ OrderShippingCommandHandler.java)
    ```

6.  `OrderShippingCommandHandler`创建一个新的`OrderShippedEvent`并将其发送到集群事件总线。

7.  当`OrderShippedEvent`出现在集群事件总线中时，`OrderProcessSaga`的`handleOrderShippedEvent` SagaEventHandler 会拾取它。

8.  `OrderProcessSaga`中的`handleOrderShippedEvent`类似于 Axon 中的普通事件处理器。它不会启动或结束现有 Saga；相反，它需要单独执行某个操作，即在你的场景中向命令网关发送一个新的`OrderUpdateCommand`。参见清单 17-20。

```
    public class OrderProcessSaga extends AbstractAnnotatedSaga {
    private Long orderId;
    @SagaEventHandler(associationProperty = "orderId")
    public void handleOrderShippedEvent(OrderShippedEvent orderShippedEvent) {
    commandGateway.send(new OrderUpdateCommand(
    orderShippedEvent.getOrderId(),OrderStatus.SHIPPED));
    }
    }
    清单 17-20
    OrderProcessSaga handleOrderShippedEvent (ch17\ch17-01\Ax2-Ecom-No-Security\Ecom-core\src\main\java\com\acme\ecom\core\order\saga\handler\OrderProcessSaga.java)
    ```

9.  命令网关将`OrderUpdateCommand`投递到命令总线。

10.  `OrderCommandHandler`对`OrderUpdateCommand`感兴趣。参见清单 17-21。

```
    @Component
    public class OrderCommandHandler {
    @Autowired
    @Qualifier(value = "orderRepository")
    private Repository orderRepository;
    @CommandHandler
    public void handleOrderUpdate(OrderUpdateCommand orderUpdateCommand) {
    Order order = orderRepository.load(orderUpdateCommand.getOrderId());
    order.updateOrderStatus(orderUpdateCommand.getOrderStatus());
    }
    }
    清单 17-21
    OrderCommandHandler handleOrderUpdate (ch17\ch17-01\Ax2-Ecom-No-Security\Ecom-core\src\main\java\com\acme\ecom\core\order\command\handler\OrderCommandHandler.java)
    ```

11.  `handleOrderUpdate`首先检索对应的订单，然后调用`updateOrderStatus`，如清单 17-22 所示。



```
    @Entity(name="order")
    @Table(name = "CUSTOMER_ORDER")
    public class Order extends AbstractAggregateRoot {
    @Id
    private Long id;
    @Enumerated(EnumType.STRING)
    @Column(name="ORDER_STATUS")
    private OrderStatus orderStatus;
    public void updateOrderStatus(OrderStatus orderStatus){
    this.orderStatus=orderStatus;
    registerEvent(new OrderUpdatedEvent(this.id, orderStatus.name(),
    new Date(), null));
    }
    }
    清单 17-22
    updateOrderStatus (ch17\ch17-01\Ax2-Ecom-No-Security\Ecom-core\src\main\java\com\acme\ecom\core\order\model\Order.java)
    ```

12.  订单聚合体向集群事件总线发送一个 `OrderUpdatedEvent`。

13.  `OrderUpdatedEvent` 被 Ecom-history 微服务中的订单历史事件处理器消费。

14.  Ecom-history 微服务中的订单历史事件处理器更新 `Order History` 表中对应的订单记录。

清单 17-17 展示了订单历史事件处理器处理订单更新的代码。至此，“发货订单”流程完成，稍后你将看到实际用例的执行过程。

交付已发货订单

你刚刚看到了新订单的发货流程。所有已发货的订单将可供后台管理员进一步拣选并触发履行周期的最后一步——订单交付。图 17-9 展示了交付步骤中各实体的状态变化。

![../images/477630_1_En_17_Chapter/477630_1_En_17_Fig9_HTML.jpg](img/477630_1_En_17_Fig9_HTML.jpg)

图 17-9
订单交付成功

参考图 17-9，后台管理员使用 Web 应用通过 REST JSON 请求发货的端到端流程可总结如下：

1.  `order_service.js`
    通过 HTTP 发送一个 REST JSON 请求，以交付一个已发货的订单：

```
    $http.post(sessionStorage.getItem('apiUrl')+'/delivery/delivery',deliveryDTO)
    ```

该请求命中 API 网关。

2.  API 网关通过查询服务注册表来解析 URL，并将请求定向到 Ecom-delivery 微服务中的订单交付 REST 控制器。

3.  订单交付 REST 控制器创建一个新的 `OrderDeliveryCommand` 并将其发送到命令网关。参见清单 17-23。

```
    @RestController
    @RequestMapping("/delivery")
    public class OrderDeliverController {
    @Autowired
    private CommandGateway commandGateway;
    @RequestMapping(method = RequestMethod.POST)
    @ResponseBody
    public void deliverOrder(@RequestBody DeliveryDTO deliveryDTO) {
    OrderDeliveryCommand orderCommand =
    new OrderDeliveryCommand(deliveryDTO.getOrderId(),
    deliveryDTO.isDelivered(),deliveryDTO.getReasonForFailure());
    commandGateway.send(orderCommand);
    }
    }
    清单 17-23
    OrderDeliverController deliverOrder (ch17\ch17-01\Ax2-Ecom-No-Security\Ecom-delivery\src\main\java\com\acme\ecom\delivery\web\OrderDeliverController.java)
    ```

4.  命令网关将 `OrderDeliveryCommand` 投递到命令总线。

5.  `OrderDeliveryCommandHandler` 从命令总线消费 `OrderDeliveryCommand`。参见清单 17-24。

```
    @Component
    public class OrderDeliveryCommandHandler {
    @Autowired
    private EventBus eventBus;
    @CommandHandler
    public void handleOrderDelivery(
    OrderDeliveryCommand orderDeliveryCommand) {
    if(orderDeliveryCommand.isDelivered()){
    eventBus.publish(asEventMessage(
    new OrderDeliveredEvent(orderDeliveryCommand.getOrderId(),
    new Date())));
    }else{
    eventBus.publish(asEventMessage(
    new OrderDeliveryFailedEvent(
    orderDeliveryCommand.getOrderId(),
    orderDeliveryCommand.getReasonForFailure())));
    }
    }
    }
    清单 17-24
    OrderDeliveryCommandHandler handleOrderDelivery (ch17\ch17-01\Ax2-Ecom-No-Security\Ecom-delivery\src\main\java\com\acme\ecom\delivery\command\handler\OrderDeliveryCommandHandler.java)
    ```

6.  在当前流程中，当后台管理员点击“已交付”按钮时，检查 `if(orderDeliveryCommand.isDelivered())` 为真，因此一个新的 `OrderDeliveredEvent` 被发送到集群事件总线。

7.  步骤 6 中注册的 `OrderDeliveredEvent` 被订单流程 Saga 消费。

8.  在调用 `handleOrderCanceledEvent` 处理器后，Saga 的生命周期结束，因为你已在该 Saga 的事件处理器上标注了 `@` `EndSaga` `。` 参见清单 17-25。

```
    public class OrderProcessSaga extends AbstractAnnotatedSaga {
    private Long orderId;
    @EndSaga
    @SagaEventHandler(associationProperty = "orderId")
    public void handleOrderDeliveredEvent(OrderDeliveredEvent
    orderDeliveredEvent) {
    commandGateway.send(new OrderUpdateCommand(
    orderDeliveredEvent.getOrderId(),OrderStatus.DELIVERED));
    }
    }
    清单 17-25
    OrderProcessSaga handleOrderDeliveredEvent (ch17\ch17-01\Ax2-Ecom-No-Security\Ecom-core\src\main\java\com\acme\ecom\core\order\saga\handler\OrderProcessSaga.java)
    ```

9.  Saga 向命令网关发送一个 `OrderUpdateCommand`。

10. 命令网关将 `OrderUpdateCommand` 投递到命令总线。

11. 订单命令处理器拾取 `OrderUpdateCommand` 进行进一步处理。

    请参考清单 17-21 查看订单命令处理器处理 `OrderUpdateCommand` 的代码演练。

12. `handleOrderUpdate` 首先检索对应的订单并调用 `updateOrderStatus`。

13. 订单聚合体向集群事件总线发送一个 `OrderUpdatedEvent`。清单 17-22 展示了这段代码。

14. `OrderUpdatedEvent` 被 Ecom-history 微服务中的订单历史事件处理器消费。

15. Ecom-history 微服务中的订单历史事件处理器更新 `Order History` 表中对应的订单记录。

清单 17-17 展示了订单历史事件处理器处理订单更新的代码。至此，“交付已发货订单”流程完成，稍后你将看到实际用例的执行过程。

已发货订单的交付失败

示例应用程序提供了一种模拟已发货订单交付失败的机制。此事件可能由多种原因引起，例如无法找到送货地址、客户拒收货物、货物运输途中遭遇自然灾害等。无论何种情况，一旦货物退回仓库，就无需将该订单中的产品保持打包状态；相反，可以将产品释放回库存（当然，如果检查合格）以便再次销售。当模拟此类场景时，你将在图 17-10 中看到流程和状态变化。

![../images/477630_1_En_17_Chapter/477630_1_En_17_Fig10_HTML.jpg](img/477630_1_En_17_Fig10_HTML.jpg)

图 17-10
订单交付失败



参考图 17-10，通过后台管理员使用 Web 应用发送 REST JSON 请求来模拟已发货订单配送失败的端到端流程可总结如下：

1.  `order_service.js`
    通过 HTTP 发送一个 REST JSON 请求，以配送一个已发货的订单，具体如下：

```
    $http.post(sessionStorage.getItem('apiUrl')+'/delivery/delivery',deliveryDTO)
    ```

此请求会命中 API 网关。

2.  API 网关通过查询服务注册表来解析 URL，并将请求路由至 Ecom-delivery 微服务中的 Order Deliver REST 控制器。

3.  Order Deliver REST 控制器创建一个新的 OrderDeliveryCommand，并将其发送至命令网关。

请参考清单 17-21 查看相关代码。

4.  命令网关将 OrderDeliveryCommand 投递至命令总线。

5.  OrderDeliveryCommandHandler 从命令总线消费 OrderDeliveryCommand。

请参考清单 17-24 查看相关代码。

6.  在当前流程中，当后台管理员点击“配送失败”按钮时，`if(orderDeliveryCommand.isDelivered())` 检查结果为 false，因此会向集群事件总线发送一个新的 OrderDeliveryFailedEvent。

7.  步骤 6 中注册的 OrderDeliveryFailedEvent 由订单流程 Saga 消费。

8.  在调用 handleOrderCanceledEvent 处理器后，Saga 的生命周期结束，因为你已使用 `@EndSaga` 对该 Saga 上的事件处理器进行了注解，如清单 17-26 所示。

```
    public class OrderProcessSaga extends AbstractAnnotatedSaga {
    private Long orderId;
    @EndSaga
    @SagaEventHandler(associationProperty = "orderId")
    public void handleOrderDeliveryFailureEvent(
    OrderDeliveryFailedEvent orderDeliveryFailedEvent) {
    commandGateway.send(new OrderDeliveryFailureRollbackCommand(
    orderDeliveryFailedEvent.getOrderId(),
    orderDeliveryFailedEvent.getFailureReason()));
    }
    }
    清单 17-26
    OrderProcessSaga 处理 OrderDeliveryFailureEvent (ch17\ch17-01\Ax2-Ecom-No-Security\Ecom-core\src\main\java\com\acme\ecom\core\order\saga\handler\OrderProcessSaga.java)
    ```

9.  订单流程 Saga 向命令网关发送一个 OrderDeliveryFailureRollbackCommand。

10. 命令网关将 OrderDeliveryFailureRollbackCommand 投递至命令总线。

11. Order 命令处理器拾取 OrderDeliveryFailureRollbackCommand 进行处理。参见清单 17-27。

```
    @Component
    public class OrderCommandHandler {
    @Autowired
    @Qualifier(value = "inventoryRepository")
    private Repository inventoryRepository;
    @Autowired
    @Qualifier(value = "orderRepository")
    private Repository orderRepository;
    @CommandHandler
    public void handleOrderDeliveryFailure(OrderDeliveryFailureRollbackCommand
    orderDeliveryFailureRollbackCommand) {
    Order order = orderRepository.load(
    orderDeliveryFailureRollbackCommand.getOrderId());
    order.updateOrderStatus(OrderStatus.DELIVERY_FAILED);
    rollbackInventory(order);
    }
    private void rollbackInventory(Order order){
    for(LineItem lineItem:order.getLineItems()){
    Inventory inventory =
    inventoryRepository.load(lineItem.getInventoryId());
    inventory.updateProductStock(lineItem.getQuantity(),
    ProductStockOperation.ADD);
    }
    }
    }
    清单 17-27
    OrderCommandHandler 处理 OrderDeliveryFailure (ch17\ch17-01\Ax2-Ecom-No-Security\Ecom-core\src\main\java\com\acme\ecom\core\order\command\handler\OrderCommandHandler.java)
    ```

12. Order 命令处理器从仓库加载相应的订单聚合实体，并调用订单的 `updateOrderStatus` 方法，将状态更改为“配送失败”。

请参见清单 17-22 中关于调用订单的 `updateOrderStatus` 方法以更改状态的代码讲解。

13. 订单聚合向集群事件总线发送一个 OrderUpdatedEvent。

14. 在步骤 11 中，Order 命令处理器还检索了与该订单关联的所有产品的库存项，并调用了 `updateProductStock`。其目的是回退库存以应对订单配送失败的影响，从而使这些库存可以再次用于销售。清单 17-14 中的 rollbackInventory 描述了这一点。

15. 在回退库存的同时，每个库存聚合都会向集群事件总线注册一个 InventoryUpdateEvent。

16. 步骤 15 中生成的 InventoryUpdateEvent 由 Ecom-product 微服务中的 Inventory 事件处理器消费。

17. Ecom-product 微服务中的 InventoryEventHandler 刷新其自身的库存读取模型物化视图。

清单 17-11 展示了执行此操作的 handleInventoryUpdates 代码。

18. OrderUpdatedEvent 由 Ecom-history 微服务中的 Order History 事件处理器消费。

19. Ecom-history 微服务中的 Order History 事件处理器更新 `Order History` 表中订单的相应记录。

清单 17-17 展示了 Order History 事件处理器处理订单更新的代码。
至此，“已发货订单配送失败”流程的模拟完成，稍后你将看到实际用例的执行。

检索订单历史视图

订单历史用于渲染特定用户的所有订单，并且可按状态进行过滤。这是通过 Order History Mongo 仓库实现的。有趣的是，Order History 微服务中没有 REST 控制器；而是使用了 `@` `RepositoryRestResource`。参见清单 17-28。

```
@RepositoryRestResource(collectionResourceRel = "orderHistory",
path = "orderHistory")
public interface OrderHistoryRepository extends
MongoRepository  {
@RestResource(exported = false)
public Order findByOrderId(@Param("orderId") Long orderId);
public List findByUserId(@Param("userId") String userId);
public List findByOrderStatus(
@Param("orderStatus") String orderStatus);
@Override
public Order findOne(String id);
@Override
@RestResource(exported = false)
public Page findAll(Pageable pageable);
}
清单 17-28
OrderHistoryRepository (ch17\ch17-01\Ax2-Ecom-No-Security\Ecom-history\src\main\java\com\acme\ecom\order\history\repository\OrderHistoryRepository.java)
```

在运行时，Spring Data REST 会自动创建上述接口的实现。然后它会使用 `@RepositoryRestResource` 注解来指示 Spring MVC 在 `/orderHistory` 路径下创建 RESTful 端点，其详细信息你已在第 7 章的“使用 Spring Boot 和 MongoDB 执行数据操作”一节中看到。

设计与编码电子商务微服务基础设施
除了上一节中的业务微服务之外，示例应用程序中还包含一组基础设施微服务。这些微服务的代码库直接易懂，其中大部分已在第 8 章的“Spring Cloud”一节中介绍过，因此此处不再详述。不过，你将看到它们如何在整体架构中协同工作。



配置服务器
配置服务器微服务为所有其他业务微服务和基础设施微服务托管配置参数。因此，所有其他微服务的启动都需要配置服务器微服务处于健康的运行状态。您可以通过指向该微服务，从单一位置找到所有其他微服务的配置文件。在生产环境中，此位置将指向一个 GIT 仓库，以便配置文件本身能够进行版本控制。

图 17-11 反映了上述依赖关系，标记为 1。

![../images/477630_1_En_17_Chapter/477630_1_En_17_Fig11_HTML.jpg](img/477630_1_En_17_Fig11_HTML.jpg)

图 17-11
配置服务器与服务注册中心

配置服务器微服务的代码很简单，如代码清单 17-29 所示。

```
@SpringBootApplication
@EnableConfigServer
public class EcomConfigApplication {
public static void main(String[] args) {
SpringApplication.run(EcomConfigApplication.class, args);
}
}
代码清单 17-29
配置服务器 (ch17\ch17-01\Ax2-Ecom-No-Security\Ecom-config\src\main\java\com\acme\ecom\config\EcomConfigApplication.java)
```

服务注册中心
您使用 Eureka 服务器作为服务注册中心。所有业务微服务在启动时都会向服务注册中心注册自己，此注册过程在图 17-11 中标记为 2。

服务注册中心微服务的代码同样很简单，如代码清单 17-30 所示。

```
@SpringBootApplication
@EnableEurekaServer
public class EcomServiceRegisterApplication {
public static void main(String[] args) {
SpringApplication.run(EcomServiceRegisterApplication.class, args);
}
}
代码清单 17-30
服务注册中心 (ch17\ch17-01\Ax2-Ecom-No-Security\Ecom-registry\src\main\java\com\acme\ecom\registry\ EcomServiceRegisterApplication.java)
```

API 网关
您启用 Zuul 作为 API 网关。来自 Web 应用的任何请求都只会到达 API 网关，由 API 网关负责通过联系服务注册中心来解析请求目标，并将请求路由到相应的服务。请参见代码清单 17-31。

```
@SpringBootApplication
@EnableZuulProxy
@EnableDiscoveryClient
@EnableCircuitBreaker
@EnableHystrix
@EnableHystrixDashboard
@EnableFeignClients
public class EcomApiGatewayApplication {
public static void main(String[] args) {
SpringApplication.run(EcomApiGatewayApplication.class, args);
}
@Bean
public CorsFilter corsFilter() {
UrlBasedCorsConfigurationSource source =
new UrlBasedCorsConfigurationSource();
CorsConfiguration config = new CorsConfiguration();
config.setAllowCredentials(true);
config.addAllowedOrigin("∗");
config.addAllowedHeader("∗");
config.addAllowedMethod("OPTIONS");
config.addAllowedMethod("HEAD");
config.addAllowedMethod("GET");
config.addAllowedMethod("PUT");
config.addAllowedMethod("POST");
config.addAllowedMethod("DELETE");
config.addAllowedMethod("PATCH");
source.registerCorsConfiguration("/∗∗", config);
return new CorsFilter(source);
}
}
代码清单 17-31
Zuul API 网关 (ch17\ch17-01\Ax2-Ecom-No-Security\Ecom-gateway\src\main\java\com\acme\ecom\gateway\EcomApiGatewayApplication.java)
```

图 17-12 描绘了 Web 应用与服务器端微服务的各种交互。

![../images/477630_1_En_17_Chapter/477630_1_En_17_Fig12_HTML.jpg](img/477630_1_En_17_Fig12_HTML.jpg)

图 17-12
API 网关

参照图 17-12，Web 应用与服务器端微服务的各种交互如下：

1.  Web 应用可直接从外部网络或互联网访问。一旦在浏览器中输入主页 URL，Web 应用将被下载到浏览器中。



2.  来自 Web 应用的所有后续请求都会到达 API 网关。

3.  当来自 Web 应用的请求到达 API 网关时，网关会联系服务注册中心，以解析微服务的 IP、端口和服务名称。

4.  随后，该请求会被路由到相应的微服务。

表 17-1 提供了微服务示例应用的应用程序和基础设施组件的完整视图。当您想要理解前面讨论的各种用例的依赖关系和流程时，这将非常方便。

表 17-1
应用程序和基础设施组件的完整视图

序号 |
 μ ? |
 微服务 |
 组件 |
 构造型 |
 方法 |
 参数 |
 备注 |

| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |

1 |
 μ |
 Ecom-web |
 配置控制器 |
 REST 控制器 |
 getAPIGatewayURL |
   |
   |

2 |
 μ |
 Ecom-user |
 用户控制器 |
 REST 控制器 |
 postCustomer |
   |
   |

3 |
 validateCredential |
   |
   |

4 |
 客户服务 |
 服务 |
 saveCustomer |
   |
   |

5 |
 findCustomer |
   |
   |

6 |
 用户仓库 |
 CRUD 仓库 |
 findByUserId |
   |
   |

7 |
 在线用户
仓库 |
 CRUD 仓库 |
 findByScreenName |
   |
   |

8 |
 μ |
 Ecom-product |
 产品 REST
控制器 |
 REST 控制器 |
 updateProduct |
   |
   |

9 |
 postProduct |
   |
   |

10 |
 getProduct |
   |
   |

11 |
 getAllProducts |
   |
   |

12 |
 deleteProduct |
   |
   |

13 |
 deleteAllProducts |
   |
   |

14 |
 getAllProductsByCategory |
   |
   |

15 |
 getAllProductsByName |
   |
   |

16 |
 产品仓库 |
 Mongo 仓库 |
   |
   |
   |

17 |
 产品类别
仓库 |
 Mongo 仓库 |
   |
   |
   |

18 |
 库存事件
处理器 |
 事件处理器 |
 handleInventoryUpdates |
 库存更新
事件 |
   |

19 |
 μ |
 Ecom-cart |
 客户购物车
应用控制器 |
 REST 控制器 |
 getCustomerCartInfo |
   |
   |

20 |
 客户购物车
控制器 |
 REST 控制器 |
 updateCustomerCartToCache |
   |
   |

21 |
 getCustomerCartFromCache |
   |
   |

22 |
 购物车缓存
服务 |
 服务 |
 updateUserCartInCache |
   |
   |

23 |
 getUserCartFromCache |
   |
   |

24 |
 μ |
 Ecom-core |
 订单控制器 |
 REST 控制器 |
 createOrder |
   |
   |

25 |
 cancelOrder |
   |
   |

26 |
 订单处理
Saga |
 Saga 事件
处理器 |
 handleOrderCreationEvent |
 订单已创建
事件 |
 启动 Saga |

27 |
 handleOrderShippedEvent |
 订单已发货
事件 |
   |

28 |
 handleOrderCanceledEvent |
 订单已取消
事件 |
 结束 Saga |

29 |
 handleOrderDeliveredEvent |
 订单已送达
事件 |
 结束 Saga |

30 |
 handleOrderDeliveryFailureEvent |
 订单配送
失败事件 |
 结束 Saga |

31 |
 μ |
 Ecom-core |
 订单命令
处理器 |
 命令处理器 |
 handleNewOrder |
 订单创建
命令 |
   |

32 |
 handleOrderUpdate |
 订单更新
命令 |
   |

33 |
 handleOrderCancel |
 订单取消
命令 |
   |

34 |
 handleOrderDeliveryFailure |
 订单配送
失败回滚
命令 |
   |

35 |
 库存创建
命令处理器 |
 命令处理器 |
 handleInventoryCreation |
 库存创建
命令 |
   |

36 |
 订单 |
 聚合根 |
 notifyOrderCreation |
   |
   |

37 |
 updateOrderStatus |
   |
   |

38 |
 cancelOrder |
   |
   |

39 |
 notifyOrderFailure |
   |
   |

40 |
 库存 |
 聚合根 |
 updateProductStock |
   |
   |

41 |
 μ |
 Ecom-shipping |
 订单配送
控制器 |
 REST 控制器 |
 shipOrder |
   |
   |

42 |
 订单配送
命令处理器 |
 命令处理器 |
 handleOrderShipping |
 订单配送
命令 |
   |

43 |
 μ |
 Ecom-delivery |
 订单交付
控制器 |
 REST 控制器 |
 handleOrder |
   |
   |

44 |
 订单交付
命令处理器 |
 命令处理器 |
 handleOrderDelivery |
 订单交付
命令 |
   |

45 |
 μ |
 Ecom-history |
 订单历史
事件处理器 |
 事件处理器 |
 handleOrderCreationEvent |
 订单已创建
事件 |
   |

46 |
 handleOrderUpdatedEvent |
 订单已更新
事件 |
   |



47 |
 μ |
 Ecom-security |
   |
 Infra |
   |
   |
   |

48 |
 μ |
 Ecom-gateway |
   |
 Infra |
   |
   |
   |

49 |
 μ |
 Ecom-registry |
   |
 Infra |
   |
   |
   |

50 |
 μ |
 Ecom-config |
   |
 Infra |
   |
   |
   |

51 |
 × |
 Ecom-common |
   |
 Common Libraries |
   |
   |
   |

52 |
 `×` |
 config-repo |
   |
 Config Files |
   |
   |
   |

53 |
 × |
 Delivery-setup  |
   |
 Utility Folder |
   |
   |
   |

配置电子商务微服务
应用程序

您需要在微服务中进行必要的配置更改。我们将逐一完成。

1.  微服务 1：Ecom-config。参见清单 17-32。

```
spring:
cloud:
config:
server:
git:
uri: file://D:/binil/gold/pack03/ch17/ch17-01/Ax2-Ecom-No-Security/config-repo
清单 17-32
Ecom-config 微服务配置 (ch17\ch17-01\Ax2-Ecom-No-Security\Ecom-config\src\main\resources\application.yml)
```

注意
请根据您的环境进行必要的更改。确保配置 URI 正确无误。您需要稍后在其他服务器之前启动配置服务器。此外，所有其他微服务的配置参数检索将取决于此配置服务器微服务的正确配置。

1.  微服务 2：Ecom-registry。参见清单 17-33。

```
eureka:
instance:
hostname: localhost
client:
registerWithEureka: false
fetchRegistry: false
serviceUrl.defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/
清单 17-33
Ecom-registry 微服务配置 (ch17\ch17-01\Ax2-Ecom-No-Security\config-repo\ecom-registry.yml)
```

注意
此处无需进行任何更改。

1.  微服务 3：Ecom-gateway。参见清单 17-34。

```
zuul:
routes:
ecom-core:
path: /core/∗∗
service-id: ecom-core
ecom-user:
path: /customer/∗∗
service-id: ecom-user
ecom-cart:
path: /cart/∗∗
service-id: ecom-cart
ecom-product:
path: /product/∗∗
service-id: ecom-product
ecom-history:
path: /orderhistory/∗∗
service-id: ecom-history
ecom-delivery:
path: /delivery/∗∗
service-id: ecom-delivery
ecom-shipping:
path: /shipping/∗∗
service-id: ecom-shipping
eureka:
client:
serviceUrl:
defaultZone: http://localhost:8761/eureka/
清单 17-34
Ecom-gateway 微服务配置 (ch17\ch17-01\Ax2-Ecom-No-Security\config-repo\ecom-gateway.yml)
```

注意
此处无需进行任何更改。

1.  微服务 4：Ecom-cart。参见清单 17-35。

```
eureka:
client:
serviceUrl:
defaultZone: http://localhost:8761/eureka/
清单 17-35
Ecom-cart 微服务配置 (ch17\ch17-01\Ax2-Ecom-No-Security\config-repo\ecom-cart.yml)
```

注意
此处无需进行任何更改。

1.  微服务 5：Ecom-core。参见清单 17-36。

```
spring:
data:
mongodb:
uri: mongodb://localhost:27017/ecom
datasource:
url: jdbc:mysql://localhost/ecom
username: root
password: rootpassword
driver-class-name: com.mysql.jdbc.Driver
jpa:
database-platform: org.hibernate.dialect.MySQL5InnoDBDialect
eureka:
client:
serviceUrl:
defaultZone: http://localhost:8761/eureka/
ecom:
amqp:
rabbit:
address: localhost:5672
username: guest
password: guest
vhost: /
exchange: Ecom-exchange
queue: Ecom-core-queue
清单 17-36
Ecom-core 微服务配置 (ch17\ch17-01\Ax2-Ecom-No-Security\config-repo\ecom-core.yml)
```

注意
请根据您的环境进行必要的更改。

1.  微服务 6：Ecom-delivery。参见清单 17-37。

```
eureka:
client:
serviceUrl:
defaultZone: http://localhost:8761/eureka/
ecom:
amqp:
rabbit:
address: localhost:5672
username: guest
password: guest
vhost: /
exchange: Ecom-exchange
queue: Ecom-delivery-queue
清单 17-37
Ecom-delivery 微服务配置 (ch17\ch17-01\Ax2-Ecom-No-Security\config-repo\ecom-delivery.yml)
```

注意
请根据您的环境进行必要的更改。

1.  微服务 7：Ecom-history。参见清单 17-38。



```
spring:
data:
mongodb:
uri:mongodb://localhost:27017/ecom
eureka:
client:
serviceUrl:
defaultZone: http://localhost:8761/eureka/
ecom:
amqp:
rabbit:
address: localhost:5672
username: guest
password: guest
vhost: /
exchange: Ecom-exchange
queue: Ecom-order-histo-queue
清单 17-38
Ecom-history 微服务配置 (ch17\ch17-01\Ax2-Ecom-No-Security\config-repo\ecom-history.yml)
```

注意
根据你的环境进行必要的更改。

1.  微服务 8：Ecom-product。请参见清单 17-39。

```
spring:
data:
mongodb:
uri: mongodb://localhost:27017/ecom
eureka:
client:
serviceUrl:
defaultZone: http://localhost:8761/eureka/
ecom:
amqp:
rabbit:
address: localhost:5672
username: guest
password: guest
vhost: /
exchange: Ecom-exchange
queue: Ecom-product-queue
product.img.location: D:/binil/gold/pack03/ch17/ch17-01/Ax2-Ecom-No-Security/Ecom-xtra-setup/productImg/
清单 17-39
Ecom-product 微服务配置 (ch17\ch17-01\Ax2-Ecom-No-Security\config-repo\ecom-product.yml)
```

注意
根据你的环境进行必要的更改。

1.  微服务 9：Ecom-shipping。请参见清单 17-40。

```
eureka:
client:
serviceUrl:
defaultZone: http://localhost:8761/eureka/
ecom:
amqp:
rabbit:
address: localhost:5672
username: guest
password: guest
vhost: /
exchange: Ecom-exchange
queue: Ecom-shipping-queue
清单 17-40
Ecom-shipping 微服务配置 (ch17\ch17-01\Ax2-Ecom-No-Security\config-repo\ecom-shipping.yml)
```

注意
根据你的环境进行必要的更改。

1.  微服务 10：Ecom-user。请参见清单 17-41。

```
spring:
datasource:
url: jdbc:mysql://localhost/ecom
username: root
password: rootpassword
eureka:
client:
serviceUrl:
defaultZone: http://localhost:8761/eureka/
清单 17-41
Ecom-user 微服务配置 (ch17\ch17-01\Ax2-Ecom-No-Security\config-repo\ecom-user.yml)
```

注意
根据你的环境进行必要的更改。

1.  微服务 11：Ecom-web。请参见清单 17-42。

```
spring:
application:
name: ecom-web
server:
port: 8080
ecom:
apigateway:
url: http://localhost:9000
清单 17-42
Ecom-web (ch17\ch17-01\Ax2-Ecom-No-Security\Ecom-web\src\main\resources\application.yml)
```

Web 应用将使用上述配置的 API 网关 URL 将所有请求路由到微服务。此处无需进行任何更改。

为电子商务微服务应用设置环境基础设施

接下来，你需要设置一些基础设施来运行示例。让我们也逐一完成。

1.  设置 RabbitMQ 服务器。

作为第一步，你需要启动 RabbitMQ 服务器。你可能需要参考附录 B 来开始使用 RabbitMQ 服务器。

```
    D:\Applns\RabbitMQ\rabbitmq_server-3.6.3\sbin>D:\Applns\RabbitMQ\rabbitmq_server-3.6.3\sbin\rabbitmq-server.bat
    ```

2.  设置 MongoDB 服务器。

接下来，确保 MongoDB 已启动并正在运行。你可能需要参考附录 A 来开始使用 MongoDB。

```
    D:\Applns\MongoDB\Server\3.2.6\bin\mongod.exe --dbpath D:\Applns\MongoDB\Server\3.2.6\data
    ```

现在，你可以通过另一个 Windows 命令提示符连接到上述启动的 MongoDB 服务器，并执行程序：

```
    D:\Applns\MongoDB\Server\3.2.6\bin>D:\Applns\MongoDB\Server\3.2.6\bin\mongo
    > use ecom
    switched to db ecom
    > db.getName()
    ecom
    ```

同样，一种为示例准备干净集合和数据的简单方法是直接删除数据库，然后在创建所需集合时重新创建它：

```
    > db.dropDatabase();
    > show collections
    ```

现在你将创建所需的集合。为此，在另一个 Windows 命令提示符中，你需要使用以下 `mongoimport` 工具：

```
    cd D:\Applns\MongoDB\Server\3.2.6\bin
    ```

首先，创建 `ecom` 数据库，并将产品类别数据导入到名为 `productCategory` 的集合中：

```
    D:\Applns\MongoDB\Server\3.2.6\bin>mongoimport --db ecom --jsonArray --collection productCategory --file D://binil/gold/shuffle/pack02/ch17/ch17-01/Ax2-Ecom-No-Security/Ecom-xtra-setup/productCategory.json
    2018-05-25T12:11:12.587+0530    connected to: localhost
    2018-05-25T12:11:13.032+0530    imported 6 documents
    ```

接下来，对产品和库存集合执行相同操作：

```
    D:\Applns\MongoDB\Server\3.2.6\bin>mongoimport --db ecom --jsonArray --collection product --file D://binil/gold/shuffle/pack02/ch17/ch17-01/Ax2-Ecom-No-Security/Ecom-xtra-setup/products.json
    2018-05-25T15:58:44.679+0530    connected to: localhost
    2018-05-25T15:58:44.913+0530    imported 34 documents
    D:\Applns\MongoDB\Server\3.2.6\bin>mongoimport --db ecom --jsonArray --collection inventory --file D://binil/gold/shuffle/pack02/ch17/ch17-01/Ax2-Ecom-No-Security/Ecom-xtra-setup/inventory.json
    2018-05-25T15:59:47.156+0530    connected to: localhost
    2018-05-25T15:59:47.502+0530    imported 34 documents
    D:\Applns\MongoDB\Server\3.2.6\bin>
    ```

你可以像这样列出新创建的集合：

```
    > show collections
    inventory
    product
    productCategory
    >
    ```

3.  设置 MySQL 服务器。

确保 MySQL 已启动并正在运行。你可能需要参考附录 H 来开始使用 MySQL。

首先，启动 MySQL 服务器：

```
    D:\Applns\MySQL\mysql-5.7.14-winx64\bin>mysqld --console
    ```

现在打开一个 MySQL 提示符：

```
    D:\Applns\MySQL\mysql-5.7.14-winx64\bin>mysql -u root –p
    mysql> SHOW DATABASES;
    mysql> use ecom;
    Database changed
    ```

为了从干净的表格开始，让我们删除当前示例中使用的所有表格（如果存在），然后在微服务启动期间自动重新创建它们：

```
    mysql> drop table inventory;
    mysql> drop table line_item;
    mysql> drop table customer_order;
    mysql> drop table user_info;
    mysql> drop table address;
    mysql> drop table user_role;
    mysql> drop table user_credential;
    ```

构建并运行电子商务微服务应用
你已经看到该应用中有 11 个微服务，因此建议你使用 IDE（最好是 Eclipse）来更好地管理构建和运行的复杂性。

你需要首先将 11 个微服务项目设置为 Eclipse 项目。首先进入以下根文件夹：

```
cd ch17\ch17-01\Ax2-Ecom-No-Security
```

要从你的 POM 生成 Eclipse 项目文件，请执行以下命令：

```
mvn eclipse:eclipse
```

Maven Eclipse 插件用于生成 Eclipse IDE 文件（`∗.classpath`、`∗.project`、`∗.wtpmodules` 和 `.settings` 文件夹），以供项目使用。如果你的 Maven 项目有依赖项，Eclipse 类路径将与当前的 Maven 依赖项列表以及任何传递性 Maven 依赖项同步。
示例应用的 Maven 项目由多个聚合项目组成，这些项目共享一个共同的根 POM，其中一些聚合项目相互依赖。`eclipse:eclipse` 目标会将 Eclipse 中的每个依赖项目配置为 Eclipse 项目依赖项，而不是 Eclipse jar 依赖项。通过这样做，项目 A 中的代码更改将立即可用于项目 B（假设项目 B 定义了与项目 A 的依赖关系）。

接下来，你必须将项目导入到你的 Eclipse 工作区中（从菜单栏选择“文件” ➤ “导入” ➤ “将现有项目导入工作区”）。请注意，项目（目录）不应位于你的工作区中，因为 Eclipse 可能会遇到问题，特别是如果你想将 Eclipse 用作 SCM 客户端时。

1.  在 Eclipse 项目资源管理器中，导入现有项目。

2.  选择 Maven ➤ 现有 Maven 项目 ➤ 下一步。

3.  在“选择 Maven 项目”窗口中，单击“浏览”。



4.  在“选择根文件夹”中，选择 `D:\binil\gold\pack03\ch17\ch17-01\Ax2-Ecom-No-Security`，然后点击“确定”，如图 17-13 所示。

![../images/477630_1_En_17_Chapter/477630_1_En_17_Fig13_HTML.jpg](img/477630_1_En_17_Fig13_HTML.jpg)

图 17-13
导入电商微服务 Maven 项目

5.  确认导入完成后（通过观察 Eclipse 状态栏），在 Java EE 透视图中，选中所有项目（Ecom 项目除外）。

6.  右键单击，选择 Maven ➤ 更新项目。

7.  在“更新 Maven 项目”窗口中，点击“确定”。

如果一切顺利，所有项目都应构建成功，且无任何错误。您可以忽略任何警告，并继续逐个启动服务器。要启动服务器，您需要按照以下顺序启动前三个微服务：

```
Ecom-config
Ecom-registry
Ecom-gateway
```

对于其余微服务，您可以按任意顺序启动它们。这种灵活性也体现了另外两个方面：

*   **微服务的独立性**：微服务之间绝对零依赖。这与旧的 CORBA、RMI、IIOP 和 SOAP 服务范式不同，在那些范式中，依赖的微服务需要独立微服务的存根。

*   **应用程序的部分故障**：即使您没有启动所有微服务，Web 应用程序也能通过部分使用可用服务来正常运行。

要启动一个微服务，请在 Eclipse 中选择该项目，右键单击并选择“运行方式” > “Java 应用程序”。见图 17-14。

![../images/477630_1_En_17_Chapter/477630_1_En_17_Fig14_HTML.jpg](img/477630_1_En_17_Fig14_HTML.jpg)

图 17-14
将每个微服务作为 Java 应用程序运行

在下一个窗口中，选择您打算运行的包含 main 方法的 Java 类。您可以输入“Ecom∗”来列出包含 main 方法的 Java 类，然后点击“确定”，如图 17-15 所示。

![../images/477630_1_En_17_Chapter/477630_1_En_17_Fig15_HTML.jpg](img/477630_1_En_17_Fig15_HTML.jpg)

图 17-15
将每个微服务的主类作为应用程序运行

请耐心等待，直到微服务完全初始化。通常，Eclipse 中的控制台日志会指示初始化完成，如图 17-16 所示。

![../images/477630_1_En_17_Chapter/477630_1_En_17_Fig16_HTML.jpg](img/477630_1_En_17_Fig16_HTML.jpg)

图 17-16
在 Eclipse IDE 中启动的微服务

您可以按照上述步骤为每个微服务启动并运行它们。当您启动微服务时，它会在 MySQL 中自动创建所需的表。然后，您还需要用一些初始数据填充选定的表，如下所示：

*   插入库存数据：

```
    insert into inventory(id,last_event_sequence_number, version,quantity, sku) values(1,0,0,10,'LEE');
    insert into inventory(id,last_event_sequence_number, version,quantity, sku) values(2,0,0,10,'ACTION');
    insert into inventory(id,last_event_sequence_number, version,quantity, sku) values(3,0,0,10,'ADDIDAS');
    insert into inventory(id,last_event_sequence_number, version,quantity, sku) values(4,0,0,10,'NIKE');
    insert into inventory(id,last_event_sequence_number, version,quantity, sku) values(5,0,0,10,'LEE_1');
    insert into inventory(id,last_event_sequence_number, version,quantity, sku) values(6,0,0,10,'ACTION_1');
    insert into inventory(id,last_event_sequence_number, version,quantity, sku) values(7,0,0,10,'LAXME');
    insert into inventory(id,last_event_sequence_number, version,quantity, sku) values(8,0,0,10,'LAXME13');
    insert into inventory(id,last_event_sequence_number, version,quantity, sku) values(9,0,0,10,'ADDIDAS_W');
    insert into inventory(id,last_event_sequence_number, version,quantity, sku) values(10,0,0,10,'WEDGES');
    insert into inventory(id,last_event_sequence_number, version,quantity, sku) values(11,0,0,10,'LEE_2');
    insert into inventory(id,last_event_sequence_number, version,quantity, sku) values(12,0,0,10,'LEE_3');
    insert into inventory(id,last_event_sequence_number, version,quantity, sku) values(13,0,0,10,'RAYBAN1');
    insert into inventory(id,last_event_sequence_number, version,quantity, sku) values(14,0,0,10,'RAYBAN2');
    insert into inventory(id,last_event_sequence_number, version,quantity, sku) values(15,0,0,10,'RAYBAN3');
    insert into inventory(id,last_event_sequence_number, version,quantity, sku) values(16,0,0,10,'FASTTRACK1');
    insert into inventory(id,last_event_sequence_number, version,quantity, sku) values(17,0,0,10,'FASTTRACK2') ;
    insert into inventory(id,last_event_sequence_number, version,quantity, sku) values(18,0,0,10,'FASTTRACK3');
    insert into inventory(id,last_event_sequence_number, version,quantity, sku) values(19,0,0,10,'ARROW1');
    insert into inventory(id,last_event_sequence_number, version,quantity, sku) values(20,0,0,10,'ARROW2');
    insert into inventory(id,last_event_sequence_number, version,quantity, sku) values(21,0,0,10,'ARROW3');
    insert into inventory(id,last_event_sequence_number, version,quantity, sku) values(22,0,0,10,'POLO1');
    insert into inventory(id,last_event_sequence_number, version,quantity, sku) values(23,0,0,10,'POLO2');
    insert into inventory(id,last_event_sequence_number, version,quantity, sku) values(24,0,0,10,'POLO3');
    insert into inventory(id,last_event_sequence_number, version,quantity, sku) values(25,0,0,10,'DON1');
    insert into inventory(id,last_event_sequence_number, version,quantity, sku) values(26,0,0,10,'DON2');
    insert into inventory(id,last_event_sequence_number, version,quantity, sku) values(27,0,0,10,'DON3');
    insert into inventory(id,last_event_sequence_number, version,quantity, sku) values(28,0,0,10,'DON4');
    insert into inventory(id,last_event_sequence_number, version,quantity, sku) values(29,0,0,10,'X-COTTEN1');
    insert into inventory(id,last_event_sequence_number, version,quantity, sku) values(30,0,0,10,'X-COTTEN2');
    insert into inventory(id,last_event_sequence_number, version,quantity, sku) values(31,0,0,10,'X-COTTEN3');
    insert into inventory(id,last_event_sequence_number, version,quantity, sku) values(32,0,0,10,'X-COTTEN4');
    insert into inventory(id,last_event_sequence_number, version,quantity, sku) values(33,0,0,10,'X-COTTEN5');
    insert into inventory(id,last_event_sequence_number, version,quantity, sku) values(34,0,0,10,'X-COTTEN6') ;
    ```

*   插入后台管理员：



```
    insert into user_credential (id,active,password,user_id) values (1,1,'admin','admin');
    insert into user_role (id,role,user_id) values (1,'ROLE_ADMIN',1);
    insert into user_role (id,role,user_id) values (2,'CUSTOMER_READ',1);
    insert into user_role (id,role,user_id) values (3,'PRODUCT_WRITE',1);
    insert into user_role (id,role,user_id) values (4,'ORDER_READ',1);
    insert into user_role (id,role,user_id) values (5,'PRODUCT_WRITE',1);
    insert into user_role (id,role,user_id) values (6,'ORDER_WRITE',1);
    insert into user_info (id,email,first_name,last_name,phone,user_id) values(1,'admin@admin','admin','admin',903766787,'admin');
    ```

**注意**
在您阅读到第 18 章之前，您不会用到此处添加的角色。

**测试电子商务微服务用例**
您可以使用您偏好的浏览器来测试该应用程序。您可以打开两个浏览器窗口，一个用于普通客户或最终用户，另一个用于后台管理员。

输入此 URL 将显示如图 17-17 所示的最终用户界面：`http://localhost:8080/`。
（如果完整内容未如图 17-17 所示加载，请刷新浏览器）。

![../images/477630_1_En_17_Chapter/477630_1_En_17_Fig17_HTML.jpg](img/477630_1_En_17_Fig17_HTML.jpg)

**图 17-17**
最终用户主页

要调出后台管理员界面，请输入此 URL：`http://localhost:8080/admin.html`。
见图 17-18。

![../images/477630_1_En_17_Chapter/477630_1_En_17_Fig18_HTML.jpg](img/477630_1_En_17_Fig18_HTML.jpg)

**图 17-18**
后台管理员登录页面

管理员界面会要求输入凭据进行登录。您可以使用以下凭据：

```
用户名：admin
密码：admin
```

它们将打开如图 17-19 所示的管理员窗口。

![../images/477630_1_En_17_Chapter/477630_1_En_17_Fig19_HTML.jpg](img/477630_1_En_17_Fig19_HTML.jpg)

**图 17-19**
后台管理员主页

现在，您可以通过执行几个用例来测试该应用程序。您可能需要使用浏览器的“刷新”按钮显式刷新屏幕，以查看您操作的效果。

**查看产品类别和产品详情**

从图 17-17 所示的产品列表中，选择一个产品以查看产品详情和可用库存。您可以选择要购买的数量，如图 17-20 所示。

![../images/477630_1_En_17_Chapter/477630_1_En_17_Fig20_HTML.jpg](img/477630_1_En_17_Fig20_HTML.jpg)

**图 17-20**
选择要订购的商品数量

**添加到购物车**

下一步，您可以将选定的产品及其数量添加到购物车，如图 17-21 所示。

![../images/477630_1_En_17_Chapter/477630_1_En_17_Fig21_HTML.jpg](img/477630_1_En_17_Fig21_HTML.jpg)

**图 17-21**
将商品添加到购物车

您将能够在页面顶部的购物车图标中看到新添加的商品。您可以点击购物车图标来显示购物车，如图 17-22 所示。

![../images/477630_1_En_17_Chapter/477630_1_En_17_Fig22_HTML.jpg](img/477630_1_En_17_Fig22_HTML.jpg)

**图 17-22**
购物车中已有待结账的商品

**创建用户资料**

点击图 17-22 中的“立即订购”按钮继续下单。系统会要求用户登录，如图 17-23 所示。

![../images/477630_1_En_17_Chapter/477630_1_En_17_Fig23_HTML.jpg](img/477630_1_En_17_Fig23_HTML.jpg)

**图 17-23**
用户登录提示

由于这是您首次登录，您需要先创建一个登录资料，然后才能登录。因此，请点击“新用户？”链接，这将带您进入如图 17-24 所示的用户资料创建页面。

![../images/477630_1_En_17_Chapter/477630_1_En_17_Fig24_HTML.jpg](img/477630_1_En_17_Fig24_HTML.jpg)

**图 17-24**
创建用户资料

填写表单以创建用户资料，如图 17-24 所示，然后点击“提交”。现在您可以登录应用程序（如有需要，可点击购物车图标显示购物车），如图 17-25 所示。

![../images/477630_1_En_17_Chapter/477630_1_En_17_Fig25_HTML.jpg](img/477630_1_En_17_Fig25_HTML.jpg)

**图 17-25**
用户登录

**创建新订单**

登录后，您可以再次点击购物车图标查看您的购物车，如图 17-22 所示，然后点击“立即订购”按钮继续下单。在下一个窗口（如图 17-26 所示）中，您可以查看订单详情，然后点击“继续结账”按钮确认订单。

![../images/477630_1_En_17_Chapter/477630_1_En_17_Fig26_HTML.jpg](img/477630_1_En_17_Fig26_HTML.jpg)

**图 17-26**
确认创建新订单

如果订单创建成功，它将在下一个窗口中显示，如图 17-27 所示。

![../images/477630_1_En_17_Chapter/477630_1_En_17_Fig27_HTML.jpg](img/477630_1_En_17_Fig27_HTML.jpg)

**图 17-27**
新订单已创建

您可以通过点击“进行中”菜单（刷新浏览器屏幕以反映更改）来查看新创建的订单，如图 17-28 所示。

![../images/477630_1_En_17_Chapter/477630_1_En_17_Fig28_HTML.jpg](img/477630_1_En_17_Fig28_HTML.jpg)

**图 17-28**
进行中的订单

**发货新订单**

同时，新创建的订单将可供后台管理员触发履行流程。点击“待发货”菜单（刷新浏览器屏幕以反映更改）以查看所有新创建的订单，如图 17-29 所示。

![../images/477630_1_En_17_Chapter/477630_1_En_17_Fig29_HTML.jpg](img/477630_1_En_17_Fig29_HTML.jpg)

**图 17-29**
后台管理员确认发货

后台管理员可以通过点击图 17-29 中的“已发货”按钮来模拟发货步骤。一旦发货，订单将进入履行的下一阶段；刷新浏览器以查看，如图 17-30 所示。

![../images/477630_1_En_17_Chapter/477630_1_En_17_Fig30_HTML.jpg](img/477630_1_En_17_Fig30_HTML.jpg)

**图 17-30**
订单已发货，后续订单配送进行中

一旦发货，客户也将能够看到订单状态的变化，如图 17-31 所示。

![../images/477630_1_En_17_Chapter/477630_1_En_17_Fig31_HTML.jpg](img/477630_1_En_17_Fig31_HTML.jpg)

**图 17-31**
客户订单已发货

**成功配送已发货的订单**

接下来，后台管理员需要模拟履行的下一步。他需要点击图 17-30 中的“已送达”按钮。订单的成功配送将完成订单处理工作流。在此阶段，客户将能够在“已完成”选项卡中查看其订单状态，如图 17-32 所示。

![../images/477630_1_En_17_Chapter/477630_1_En_17_Fig32_HTML.jpg](img/477630_1_En_17_Fig32_HTML.jpg)

**图 17-32**
客户订单已完成



图 17-32
客户订单已履行

当客户下单时，所选产品的库存会减少，并且这一变化会反映在相应产品的详情页上，如图 17-33 所示。

![../images/477630_1_En_17_Chapter/477630_1_En_17_Fig33_HTML.jpg](img/477630_1_En_17_Fig33_HTML.jpg)

图 17-33
产品库存减少

已发货订单配送失败

现在，你将测试“配送失败”场景。创建另一个订单，如图 17-34 所示。

![../images/477630_1_En_17_Chapter/477630_1_En_17_Fig34_HTML.jpg](img/477630_1_En_17_Fig34_HTML.jpg)

图 17-34
创建第二个订单

接下来，使用图 17-29 所示的后台管理界面模拟发货。发货成功后，订单将进入下一步处理，如图 17-35 所示。

![../images/477630_1_En_17_Chapter/477630_1_En_17_Fig35_HTML.jpg](img/477630_1_En_17_Fig35_HTML.jpg)

图 17-35
第二个订单准备配送

这次，通过点击图 17-35 中所示的“配送失败”按钮来模拟配送失败。当配送失败时，这一情况将再次反映在最终用户界面上，如图 17-36 所示。

![../images/477630_1_En_17_Chapter/477630_1_En_17_Fig36_HTML.jpg](img/477630_1_En_17_Fig36_HTML.jpg)

图 17-36
第二个客户订单失败

这次，如果你查看相应的产品详情页，你会看到库存已更新，与失败订单对应的商品数量将可以再次下单。

取消新订单

为了测试最后一个用例，创建第三个订单，如图 17-37 所示。

![../images/477630_1_En_17_Chapter/477630_1_En_17_Fig37_HTML.jpg](img/477630_1_En_17_Fig37_HTML.jpg)

图 17-37
创建第三个订单

新订单将可供后台管理员进行进一步处理，如图 17-38 所示。

![../images/477630_1_En_17_Chapter/477630_1_En_17_Fig38_HTML.jpg](img/477630_1_En_17_Fig38_HTML.jpg)

图 17-38
第三个订单等待处理

然而，这次让后台管理员不做任何操作。如果最终用户检查他刚订购的产品的库存，他会发现库存已经减少，如图 17-39 所示。

![../images/477630_1_En_17_Chapter/477630_1_En_17_Fig39_HTML.jpg](img/477630_1_En_17_Fig39_HTML.jpg)

图 17-39
库存减少

现在，最终用户必须通过点击订单旁边的“取消订单”按钮来取消他刚创建的订单，如图 17-28 所示。一旦取消，最终用户可以在“您的订单”下的“已取消”标签页中看到该订单，如图 17-40 所示。

![../images/477630_1_En_17_Chapter/477630_1_En_17_Fig40_HTML.jpg](img/477630_1_En_17_Fig40_HTML.jpg)

图 17-40
已取消的订单

恢复库存

订单一旦被取消，如果你查看相应的产品详情页，你会看到库存已恢复，与取消订单对应的商品数量将可以再次下单，如图 17-41 所示。

![../images/477630_1_En_17_Chapter/477630_1_En_17_Fig41_HTML.jpg](img/477630_1_En_17_Fig41_HTML.jpg)

图 17-41
因取消而恢复库存

总结

在本章中，你在基于 CQRS 的微服务之旅中取得了很大进展。现在，你已经掌握了所有基本工具，可以使用事件驱动型微服务构建任何规模的企业级应用程序。但是，有两点需要注意：

*   你尚未在你的电子商务微服务应用程序中实现安全性（身份验证和授权）。

*   你将两个领域实体放在一起以保持强数据一致性；否则，它们本应被分离到各自独立的微服务中。

你将在接下来的两章中探讨这些方面。

18. 微服务安全

在第 17 章的电子商务微服务应用程序中，你应该已经注意到，有一些 API 方法，尤其是产品类别和产品服务中的那些方法，任何人都可以自由访问；但是，要访问所有其他微服务，你需要以已在应用程序中创建用户配置文件的后台管理员或客户身份登录。到目前为止，一切顺利，但即使需要登录，这些微服务的安全性又如何呢？
与所有服务集中部署的传统单体架构截然不同，在微服务架构中，有许多节点或进程来分布服务。即使你将 API 网关视为一个单一的、集中的前端控制器，所有请求最初都由其拦截，API 网关也必须通过内部网络将请求路由到相应的微服务。同样，微服务间的通信也通过同一个内部网络进行。
即使你已确保网关访问的安全，你的数据也无法免受内部攻击。实现安全性的一种方法是为所有服务共享用户凭证数据，并在访问前在每个服务上对用户进行身份验证。尽管这实际上是一种可行的方法，但在多个地方分发凭证是一个奇怪的想法。你应该有一个更好的方法。
你将在本章中探讨微服务安全的问题。你还将为第 17 章的示例构建安全功能，以便你了解这在真实场景中如何运作。

你将在本章中探讨以下内容：

*   OAuth 2.0

*   OAuth 2.0 上下文中的客户端类型

*   OAuth 2.0 中的授权码授权类型

*   JSON Web 令牌 (JWT)

*   为微服务电子商务应用程序启用安全性

OAuth 2.0 与微服务
借助 Spring Security 及其对 OAuth 2.0 的支持，你拥有锁定 API 网关和后端资源服务器所需的一切。你可以对其进行设置，以自动将访问令牌从一个微服务传播到另一个微服务，确保整个过程安全且加密。我不会详细介绍 OAuth 2.0，但会介绍所需的最基本方面，以便你能够为示例微服务应用程序启用安全性。

OAuth 2.0
互联网工程任务组 (IETF) 的请求评论 (RFC) 第 6749 号将 OAuth 2.0 定义为一个授权框架，它使第三方应用程序能够获得对 HTTP 服务的有限访问权限，要么代表资源所有者，通过协调资源所有者和 HTTP 服务之间的批准交互，要么允许第三方应用程序代表其自身获得访问权限。OAuth 2.0 规范取代并废弃了 OAuth 1.0 协议。

OAuth 2.0 角色

OAuth 定义了四个角色，它们是 OAuth 交互的核心：

*   **资源所有者**：任何能够授予对受保护资源访问权限的实体。当资源所有者是人类时，它被称为最终用户。资源所有者可以授予访问权限，并且此授权可用于后续和重复的资源访问。



*   **资源服务器**：托管受保护资源的服务器，能够接受并使用访问令牌响应受保护资源请求。在你的微服务场景中，如果每个微服务拥有受保护资源，它们都可能成为资源服务器。

*   **客户端**：这是一个代表资源所有者并经过其授权，发起受保护资源请求的应用程序。客户端在发起请求时，可能代表最终用户（该用户可能是也可能不是资源所有者）进行操作。“客户端”一词并不暗示任何特定的实现特性（例如，应用程序是在服务器、桌面还是其他设备上执行）。

*   **授权服务器**：在成功验证资源所有者身份并获得授权后，向客户端颁发访问令牌的服务器。授权服务器可以与资源服务器是同一台服务器，也可以是独立的实体。单个授权服务器可以颁发被多个资源服务器接受的访问令牌。

OAuth 2.0 客户端与用户代理

尽管 OAuth 只规定了四种角色，但你还应注意另一个实体，这在许多情况下（例如使用基于浏览器的应用程序时）很典型：

*   **用户代理**：用户代理通常是最终用户或资源所有者与客户端程序交互的界面。有时，用户代理和客户端可以是同一个实体。

要理解最后一点，你需要探索几种典型的架构，并理解各个客户端程序对外部世界的暴露程度差异，这将在下一节中进行。

可信客户端与不可信客户端
OAuth 客户端可分为两类：**可信客户端**或**不可信客户端**。客户端属于可信还是不可信类别，取决于客户端是否具备安全存储和传输信息的能力。通过重新审视下面描述的选定架构模型，可以阐明这一概念。

经典三层单体架构

三层单体架构包含表示层、中间层和数据库层。经典的**三层架构是一种多页面架构（MPA）**，这意味着每当用户界面需要显示数据或将数据提交回服务器时，它都必须向服务器请求一个新页面，然后在 Web 浏览器中渲染该页面。Java Server Pages (JSP) 技术用于创建 MPA Web 应用；然而，还有许多其他技术可用于构建 MPA。图 18-1 描述了一个三层单体 MPA 架构。

![../images/477630_1_En_18_Chapter/477630_1_En_18_Fig1_HTML.jpg](img/477630_1_En_18_Fig1_HTML.jpg)

图 18-1. 经典三层单体架构

三层单体 MPA 的典型交互流程如下：

1.  最终用户在浏览器中输入 URL。

2.  表示层组件（通常是一个 JSP 页面）生成所需的 HTML 标签，并将其发送回浏览器，浏览器渲染 HTML 内容。

3.  浏览器中的用户点击和操作被表示层服务器拦截，服务器需要生成下一个页面。

4.  在此阶段，如果页面需要来自任何受保护资源的数据，它需要联系安全服务器或授权服务器，进行用户身份验证和授权。

5.  一旦通过身份验证和授权，表示层服务器将联系业务层以检索任何后端数据，将其嵌入到生成的 HTML 内容中，并将内容推送回浏览器，浏览器再次渲染内容。

这里值得注意的一点是，在步骤 3 结束时，必须协调用户身份验证和授权，其结果被发送到表示层服务器，服务器可以缓存或存储与该用户（会话）关联的身份验证和授权步骤的状态。此后，对于浏览器中最终用户的每一次后续点击和操作，如果需要受保护资源，表示层服务器可以检索与该请求关联的用户（会话）的身份验证和授权步骤状态，并做出决策或将请求路由到其他后端服务器。缓存或存储在表示层服务器中的身份验证和授权步骤状态是安全的，因为它存储在**非军事区（DMZ）**内。

带有 HTTP 前端的经典三层单体架构

在一种略有不同的架构中，你只会在 DMZ 中放置一个 HTTP 服务器，而图 18-1 中显示在 DMZ 内的所有其他组件都将被移到内部网络边界内。在这里，缓存或存储在表示层服务器中的身份验证和授权步骤状态更加安全。图 18-2 描述了这种部署设置。

![../images/477630_1_En_18_Chapter/477630_1_En_18_Fig2_HTML.jpg](img/477630_1_En_18_Fig2_HTML.jpg)

图 18-2. 带有 HTTP 前端的经典三层单体架构

在这种架构中，包括 HTML 模板在内的所有静态内容都可以由 HTTP 服务器提供，而所有动态 Web 内容和业务层中的受保护资源则以下述方式访问（如图 18-2 所示）：

1.  最终用户在浏览器中输入 URL。

2.  表示层组件（通常是一个 JSP 页面）将生成所需的 HTML 标签，并将其发送回浏览器，浏览器渲染 HTML 内容。包括 HTML 模板在内的所有静态内容都可以由 HTTP 服务器提供。

3.  浏览器中的用户点击和操作再次被表示层服务器拦截，服务器需要生成下一个页面。

4.  在此阶段，如果页面需要来自任何受保护资源的数据，它首先需要联系安全服务器或授权服务器，进行用户身份验证和授权。

5.  一旦通过身份验证和授权，表示层服务器将联系业务层以检索任何后端数据，将其嵌入到生成的 HTML 内容中，并将内容推送回浏览器，浏览器再次渲染内容。

带有 API 网关的微服务架构

图 18-3 描述了基于 API 网关的微服务架构。你在前面的章节中看到过许多变体；但此处展示它是为了强调安全流程。

![../images/477630_1_En_18_Chapter/477630_1_En_18_Fig3_HTML.jpg](img/477630_1_En_18_Fig3_HTML.jpg)

图 18-3. 微服务 API 架构

微服务架构通常使用**单页面架构（SPA）**，这本质上是 MPA + AJAX 设计模式的演进。在 SPA 中，只有外壳页面在服务器上生成，其余 UI 由浏览器 JavaScript 代码渲染。SPA 分别请求标记和数据，并直接在浏览器中渲染页面。整体流程总结如下：

1.  最终用户在浏览器中输入 URL。

2.  表示层组件（通常是一个 SPA Web 应用）被下载到浏览器中，并渲染第一页或默认页面。SPA 资源（HTML+CSS+脚本）在应用程序的整个生命周期中仅加载一次。此外，只有数据在前后端之间传输。

3.  浏览器中的进一步用户点击和操作被 API 网关拦截。



4.  在此阶段，如果请求需要访问任何受保护资源的数据，API 网关将首先需要联系安全服务器或授权服务器，用户身份验证和授权在此处进行。

5.  一旦通过身份验证和授权，API 网关便会联系微服务以检索任何后端数据，并仅将数据作为响应推送回浏览器，浏览器随后会重新渲染内容。

再次强调，在 API 网关处的步骤 3 结束时，必须编排用户身份验证和授权，并将最终结果发送给 API 网关，以便其缓存或存储与该用户关联的身份验证和授权步骤的状态。此后，对于来自浏览器的最终用户的每一次后续点击和操作，当请求到达 API 网关时，如果它需要受保护资源，API 网关可以检索与该请求关联的用户身份验证和授权步骤的状态，并做出决策或将请求路由到其他后端资源服务器。缓存或存储在 API 网关中的身份验证和授权步骤的状态是安全的，因为它存储在 DMZ 内，并受到企业在安全和访问控制方面实施的严格管控。

**无服务器的客户端应用**

另一种可能的情况是，完整的 Web 应用（或 SPA）一旦下载完成，就会完全在浏览器内部继续执行，在这种情况下，就没有表示层或业务层。简而言之，浏览器中的 Web 应用没有对应的服务器端。可能存在这样一种场景：Web 应用仍然需要访问某些受保护资源；不过，这次是从第三方访问，如图 18-4 所示。

![../images/477630_1_En_18_Chapter/477630_1_En_18_Fig4_HTML.jpg](img/477630_1_En_18_Fig4_HTML.jpg)

**图 18-4.** 无服务器的 Web 应用

部署选项还有更多变体，但我们还是坚持之前解释过的场景。

**授权码授权类型**

OAuth 定义了多种授权类型；不过，我将把讨论范围限定在授权码授权类型，因为它适用于那些在与授权服务器进行身份验证时能够对其客户端凭据保密的 OAuth 客户端。对于微服务场景，API 网关是在安全服务器上实现的“客户端”。作为一种基于重定向的流程，此 OAuth 客户端必须能够与资源所有者的用户代理进行交互，并且还必须能够通过来自授权服务器的重定向来接收传入请求。

让我们结合你将在下一节中访问的示例，来看一下授权码授权类型的流程序列。见图 18-5。

![../images/477630_1_En_18_Chapter/477630_1_En_18_Fig5_HTML.jpg](img/477630_1_En_18_Fig5_HTML.jpg)

**图 18-5.** OAuth 授权码授权类型

参考图 18-5，在访问受保护资源时，端到端授权流程的典型交互序列可以总结如下：

1.  最终用户在浏览器中输入 URL。

2.  表示层组件（通常是一个 SPA Web 应用）被下载到浏览器中，并渲染第一页或默认页面。SPA 资源（HTML+CSS+脚本）在应用的整个生命周期中仅加载一次。此后，仅来回传输数据。

3.  来自浏览器的进一步用户点击和操作被 API 网关拦截。API 网关是 OAuth 客户端。由于 API 网关是一个安全的服务器端微服务，它可以在与授权服务器进行身份验证时对其客户端凭据保密。

4.  在此阶段，如果请求需要访问任何受保护资源的数据，API 网关会通过将资源所有者的用户代理引导至授权端点来启动流程。在此过程中，OAuth 客户端会包含其客户端标识符、请求的范围、本地状态以及重定向 URI。一旦允许或拒绝访问受保护资源，授权服务器就会将用户代理发送回客户端指定的重定向 URI。

5.  授权服务器通常要求用户首先进行身份验证。此步骤用于断言声称自己是资源所有者的人是否确实是实际的资源所有者，以及他们被允许将哪些权利委托给客户端。授权服务器可以与资源服务器是同一台服务器，也可以是独立的实体。

6.  授权服务器通过用户代理对资源所有者进行身份验证。用户的身份验证直接在用户（通过用户代理）和授权服务器之间进行，因此客户端永远无法看到。

7.  身份验证可以通过查询内部身份验证服务器来完成。OAuth 不规定任何特定的身份验证技术，授权服务器可以自由选择方法，例如用户名/密码对、加密证书、安全令牌、单点登录或任何其他可能性。

8.  一旦资源所有者授予访问权限，OAuth 客户端就会使用步骤 4 中提供的重定向 URI 将用户代理重定向回 OAuth 客户端。重定向 URI 还包含一个授权码以及 OAuth 客户端先前提供的任何本地状态。

9.  现在客户端拥有了授权码，它可以将其发送回授权服务器的令牌端点。OAuth 客户端还会包含用于获取授权码以进行验证的重定向 URI。

10. 授权服务器验证客户端凭据和授权码。服务器还确保接收到的重定向 URI 与步骤 3 中用于重定向客户端的 URI 匹配。如果授权码有效、之前未被使用过，并且发出此请求的客户端与发出原始请求的客户端相同，则授权服务器会生成并返回一个新的访问令牌给客户端。单个授权服务器可以颁发被多个资源服务器接受的访问令牌。

11. 拿到令牌后，客户端可以将令牌出示给资源服务器（受保护资源）。

12. 资源服务器可以向授权服务器的令牌验证端点进行验证。

13. 如果上一步中出示的令牌有效，资源服务器可以将受保护资源返回给客户端。

14. 客户端可以将此受保护资源作为对步骤 3 中用户点击的响应返回给 SPA Web 应用。

**用于可扩展 API 调用的令牌**

在了解了典型授权码类型 OAuth 流程的动态之后，你现在应该从微服务架构的角度来看待此安全方案的某些方面，特别是令牌本身的可扩展性和安全性。



会话 ID
传统上，身份验证一直作为一种有状态操作来执行。当用户输入其凭据时，服务器会生成一个唯一的会话 ID，将其存储在服务器端，同时也将其返回给用户。在每次需要访问受保护资源的请求中，此会话 ID 会作为请求头传递，服务器将始终根据服务器端的实际用户凭据或服务器端维护的“活动会话”缓存来验证该会话 ID。对于单体应用来说，这近乎是一个完美的解决方案，因为在每个请求-响应周期中，你只需要进行一次这样的验证。

对于微服务架构而言，这可能存在局限性。为几乎每个操作都从中央存储获取数据可能会很麻烦，尤其是当单个用户端的请求-响应周期在内部跨越一个图谱或多次微服务间调用时。即使单个用户端的请求-响应周期在内部由单个微服务处理，对于一个高事务处理系统，为所有 API 方法进行用户身份验证和授权数据库（或缓存）查找及验证的累积过程也是繁琐的，因此我们需要更智能的设计机制。

你应该注意到会话 ID 的一个优点：会话 ID 是不透明的，这意味着第三方无法从会话 ID 中提取出易于破译的数据。会话 ID 与其可推断数据之间的关联完全在服务器端完成。

令牌
令牌是使基于会话 ID 的安全设计更智能的一种替代方案。令牌不仅仅是一个标识符，其中还包含有意义且可推断的数据。除了会话 ID 之外，令牌还可以包含用户凭据中不太敏感的部分，例如用户名。通过这种方式，用户或服务请求者的详细信息已经存在于请求头中，因此微服务无需再对数据库或缓存进行另一次 I/O（输入/输出）来解释请求者。现在你需要关注授权部分：谁有权访问哪些受保护资源，以及一旦访问，该用户可以对资源执行哪些操作。因此，你还可以将用户可用的操作类型信息包含在令牌中。例如，你可以添加一个名为 `scope` 的字段，其值可以是 `user`（代表普通终端用户的权限）和 `admin`（拥有执行关键后台操作的额外权限）。如果是这样，当用户随请求提供令牌以执行某项操作（例如调用 API 端点）时，处理该端点并保护受保护资源的服务可以验证令牌，在其中查找正确的“作用域”，并授权（或拒绝）该用户执行该操作。

JSON Web 令牌 (JWT)
你可以创建自己的令牌以获得上一节所述的便利性，也可以采用业界已有的标准，即 JSON Web 令牌 (JWT)。

JWT 有三个不同的部分，它们经过 URL 编码以便传输：

*   **头部**：头部包含令牌的元数据，至少包含签名类型和/或加密算法。

*   **声明**：声明包含你想要签名的任何信息。

*   **JSON Web 签名 (JWS)**：头部和声明使用头部中指定的算法进行数字签名。

JWT 将一系列声明编码在一个 JSON 对象中。其中一些声明具有特定含义，而另一些则留给用户自行解释。你常见的一些声明包括：

*   签发者 (iss)
*   主题 (sub)
*   受众 (aud)
*   过期时间 (exp)
*   生效时间 (nbf)
*   签发时间 (iat)
*   JWT ID (jti)

使用像 JWT 这样的通用数据格式可以轻松地与现有解决方案和库实现互操作，这样你就可以针对常见的、反复出现的问题使用经过验证和测试的模式。

一个典型的 JWT 负载如下所示：

```
{
"sub": "1234567890",
"name": "John Doe",
"iat": 1516239022
}
```

服务器提供的此类令牌将由每个客户端应用程序随请求一起发送，很多时候浏览器或用户代理就是此客户端。这些数据因此容易受到中间人攻击。需要明确的是，拦截到此令牌的人可以尝试使用相同的令牌重放请求。应该有一种方法来防止这种情况。

让我们看看上面列出的三个声明。

*   **过期时间 (exp)**：令牌将被视为过期的 UNIX 时间戳，如果未提供 iat，则此为必需项。
*   **签发时间 (iat)**：令牌创建时的 UNIX 时间戳，如果未提供 exp，则此为必需项。
*   **JWT ID (jti)**：一个唯一的令牌字符串，实际上是一个随机数，每个请求必须唯一，不能为空字符串。

这些时间戳信息有助于限制令牌的有效性，以防止重放攻击。在一种方案中，服务器可以选择不接受在当前时间之前超过 x 分钟发送的消息。这会引发客户端和服务器机器之间的时钟偏差问题，因此你可能需要在服务器中构建一些容错机制，通过在时间窗口两侧各填充 n 分钟来弥补这一差异，以应对不同机器上时钟的潜在差异，前提是你已经处理好了不同服务器之间的时钟同步问题。

上述随机数（作为 jti 值）有助于防止重放攻击。随机数只是你创建的一组任意字节，用于帮助防止重放攻击。每个请求必须具有唯一的负载。即使请求失败（HTTP 状态码 400-599）或错误地通过 HTTP 而非 HTTPS 发送，jti 值也不能重复使用。对于每个请求，建议生成一个全局唯一标识符 (GUID) 或一个足够随机的字符串，以确保在“x 分钟”的时间跨度内没有两个请求具有相同的 jti 值。

由于 JWT 由每个客户端应用程序随请求一起发送，很多时候浏览器或用户代理就是此客户端，因此这些数据也容易受到恶意篡改。它可能被篡改，以添加用户本不应拥有的权限声明。因此，令牌本身应该是防篡改的。

当 API 身份验证方案在其请求的 HTTP 授权头中使用 JWT 时，每个请求的授权头都将设置为 Bearer 方案。以下是一个示例：

```
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c
```

在这里，头部和声明是经过 base64 编码以便传输的 JSON。头部、声明和签名使用句点字符 (.) 连接在一起，从而为 JWT 提供了如下结构：

```
[Base64Encoded(HEADER)] . [Base64Encoded (PAYLOAD)] . [encoded(SIGNATURE)]
```

假设以上所有问题都已处理妥当，那么如何验证一个符合上述所有要求的有效令牌最初是从正确的合作伙伴端点发送的呢？如果 JWT 没有签名，则不可信。JWT 允许使用 JSON Web 签名 (JWS) 规范采用不同的签名和加密算法。这些算法在 JWT 头部中指定。大多数情况下，JWT 通常不加密，仅签名。但是，如果你的令牌包含敏感数据，那么使用 JSON Web 加密 (JWE) 对其进行加密是一个选择。一种典型的加密方案使用已签名的 JWT 作为加密的负载。这被称为嵌套 JWT。使用相同的密钥进行加密和验证也是可以接受的。到此为止，你已经初步了解了 JWT。其中还有更多的复杂细节和漏洞，但这足以让你理解下面的示例。



为示例微服务应用设计安全性
现在，您将保护第 17 章中的微服务应用。第 17 章的示例只实现了非常基础的身份验证。这就是为什么您能够从示例应用的主页开始浏览初始页面，直到可以将商品添加到购物车；但是，要允许执行下一步的结账操作，您必须已经登录，或者如果您在应用中已有账户则直接登录，或者如果您在应用中还没有现成的账户，则需要先创建一个账户再登录。同样，后台管理员要在应用中执行此功能，也必须登录。

然而，登录只是安全保护过程的一部分。对于一个真正需要妥善保护的微服务应用，您还需要处理授权部分。如果没有适当的授权，即使您的 Web 应用功能通过身份验证受到了限制，各个微服务 API 本身也并不安全。因此，如果同一网络中的某人能够访问某个微服务，他们就可以执行功能。当然，这可以被允许，但必须经过所需的授权。这正是您要在示例应用中实现的功能。

微服务的安全架构

请参考第 17 章中的图 17-11，了解示例微服务应用的基本架构。现在，让我们添加必要的额外组件来增强它，以实现示例应用的端到端安全。见图 18-6。

![../images/477630_1_En_18_Chapter/477630_1_En_18_Fig6_HTML.jpg](img/477630_1_En_18_Fig6_HTML.jpg)

图 18-6.
微服务电商应用的安全架构

观察图 17-11 和图 18-6 所示架构之间的差异。后者展示了一个新的安全微服务。这个安全微服务是一个 OAuth 授权服务器。由于每个应用微服务都必须根据部署的授权方案保护自己的数据，因此存在多个资源服务器。

现在，您应该回顾一下图 18-5，其中您看到了初始授权码类型流程中的 OAuth 交互。同样的设计也将应用于您示例微服务应用的增强安全架构中。浏览器中 Web 应用的所有请求将首先被 API 网关拦截。API 网关负责处理微服务的安全性。API 网关将首先检查传入的 REST 请求是否具有针对受保护资源的现有访问令牌。参考图 18-6，

*   如果访问令牌不存在，则意味着该请求是前面“授权码授予类型”小节中描述的 OAuth 握手初始循环的一部分。如果是这样，网关将检查请求是否指向授权服务器，如果是，则会同时添加 `client_id` 和 `client_secret`。OAuth 客户端凭据（`client_id` 和 `client_secret`）由网关安全地保存在服务器端，网关将它们添加到请求头中并转发请求。如前所述，OAuth 握手将在最后完成，并提供一个访问令牌作为响应头。

*   相反，如果访问令牌已存在，它将把令牌添加到请求头中，并将请求转发给受保护资源。

非对称签名和引用令牌
在前面“JSON Web 令牌”小节中，您了解了签署 JWT 的必要性。有两种签名方法：使用对称密钥或非对称密钥。在对称场景中，您使用相同的密钥来加密和解密数字签名；而在非对称场景中，您使用私钥和公钥的组合。

使用令牌时面临的另一个问题是在复杂授权场景中令牌的长度。JWT 的大小很可能会增长到一定程度，以至于作为请求头传递时会遇到技术障碍。此外，即使您可以对 JWT 进行签名和加密，它仍然是“敏感信息”，最好不要让这些信息在您的 DMZ 之外传播。这里我们考虑一下引用令牌的概念。引用令牌是对实际 JWT 的引用，您只需将引用令牌传递到 DMZ（或 OAuth 客户端）之外。

在保护示例微服务应用时，您将同时利用上述两种方法。见图 18-7。

![../images/477630_1_En_18_Chapter/477630_1_En_18_Fig7_HTML.jpg](img/477630_1_En_18_Fig7_HTML.jpg)

图 18-7.
非对称加密和引用令牌

回顾前面标题为“授权码授予类型”的小节，在第 4 步中，您假设用户对受保护资源的请求已经到达 API 网关。现在，参考图 18-7，

1.  用户对受保护资源的请求到达 API 网关。
2.  授权服务器在完成所有基本的身份验证和授权检查与验证后，首先会创建一个 JWT。此 JWT 使用非对称密钥进行签名和加密。非对称密钥有一个私钥的概念，该私钥仅授权服务器可以访问，以及其对应的公钥，授权服务器会将其自由分发给任何需要它的人，在您的情况下是分发给所有资源服务器。
3.  签名并加密后的 JWT 被返回给 API 网关。
4.  API 网关不会直接将 JWT 返回给用户或浏览器，而是将 JWT 映射到一个新的引用令牌，可能存储在本地缓存或本地存储中。
5.  API 网关仅将引用令牌返回给用户或浏览器。
6.  对于 Web 应用中任何后续需要访问服务器端受保护资源的用户操作，引用令牌也会作为请求头传递，并再次被 API 网关拦截。
7.  API 网关检索与引用令牌对应的实际 JWT。
8.  API 网关将 JWT 附加到请求中，然后将请求转发给资源服务器。

如图 18-7 所示，资源服务器现在可以使用其拥有的公钥副本来验证收到的 JWT 的真实性，并进一步验证请求是否具有所需的权限，然后接受或拒绝该请求。

将安全性编码到微服务电商应用中
本节是您在第 17 章中看到的代码的延续。建议您在继续学习本章的示例应用之前，先通读第 17 章。我将逐步添加并解释为此示例建立基于 OAuth 的安全性所需的关键代码。演示为示例电商微服务应用建立 OAuth 安全性所需的完整代码位于文件夹 `ch18\ch18-01\Ax2-Ecom-With-Security` 中。

使 Web 应用能够处理安全性



通过运行第 17 章的应用程序，您已经体验到，在无需实际登录系统的情况下，即可浏览 Web 应用中的许多界面。然而，某些操作要求用户必须登录，具体如下：

*   在结账时确认订单之前，Web 应用会要求您登录。请参见代码清单 18-1。

*   如果您显式点击屏幕中上部的“Hello, My Orders”链接，Web 应用会要求您登录。请参见代码清单 18-2。

```

立即订购

代码清单 18-1
Web 应用中的“立即订购” (ch18\ch18-01\Ax2-Ecom-With-Security\Ecom-web\src\main\resources\static\cart.html)
```

```

Hello {{screenName}} ,您的订单

代码清单 18-2
主页上的“您的订单” (ch18\ch18-01\Ax2-Ecom-With-Security\Ecom-web\src\main\resources\static\index.html)
```

上述两个操作都将由代码清单 18-3 中的代码进行路由。

```
$scope.doOrder = function() {
if(sessionStorage.getItem('loggedUser')==null){
$location.url('/user');
}else{
$location.url('/order');
}
};
代码清单 18-3
路由 doOrder 操作 (ch18\ch18-01\Ax2-Ecom-With-Security\Ecom-web\src\main\resources\static\js\controller\application_controller.js)
```

代码清单 18-3 显示，如果用户尚未登录，系统将提示其进入登录界面。

代码清单 18-4 展示了其他核心代码部分。

```
ecomApp.config(['$routeProvider', function($routeProvider) {
$routeProvider.
when('/order', {
templateUrl: '/order.html',
//controller: 'ShowOrdersController'
}).
when('/user', {
templateUrl: '/user.html',
//controller: 'ShowOrdersController'
}).
when('/newUser', {
templateUrl: '/newUser.html',
//controller: 'ShowOrdersController'
}).
otherwise({
templateUrl: '/productSearch.html'
});
}]).config(['$httpProvider', function($httpProvider) {
$httpProvider.defaults.withCredentials = true;
$httpProvider.interceptors.push('AuthInterceptor');
}]);
ecomApp.factory('AuthInterceptor', function ($window, $q) {
return {
request: function(config) {
config.headers = config.headers || {};
if (sessionStorage.getItem('xtoken')) {
config.headers['x-token'] = sessionStorage.getItem('xtoken');
}
return config || $q.when(config);
},
response: function(response) {
if (response.status === 401) {
alert(response.status);
}
return response || $q.when(response);
}
};
});
代码清单 18-4
路由映射 (ch18\ch18-01\Ax2-Ecom-With-Security\Ecom-web\src\main\resources\static\js\app.js)
```

如果您使用标准的 `$http` 服务来访问远程 API，只要服务器配置为允许来自您域的 HTTP 请求，并且您不需要存储 cookie，它就能正常工作。但对于包括您的应用在内的许多应用程序，您还需要设置和存储 cookie 信息，尤其是用于登录。默认情况下，大多数浏览器不允许这样做，而 `withCredentials` 是您可以在底层 XMLHttpRequest (AJAX) 对象上设置的一个标志。在 Angular JS 中，您可以通过以下方式配置 `$http` 请求，为所有请求设置此标志：

```
$httpProvider.defaults.withCredentials = true;
```

下一步，您需要注入一个 `AuthInterceptor`。每当浏览器发出请求时，都会调用 `AuthInterceptor` 代码。它会检索存储在会话存储中的引用令牌，并将其附加到请求头中。

`user.html` 将允许用户登录应用程序。如果用户是新用户，他可以选择点击“新用户？”按钮来创建新的用户资料。即使您创建了新用户资料，随后也会被引导至用户登录页面。请参见代码清单 18-5。

```

新用户？

提交

代码清单 18-5
User.html (ch18\ch18-01\Ax2-Ecom-With-Security\Ecom-web\src\main\resources\static\user.html)
```

`loginCtrl.doLogin()` 首先调用 `UserService` 来验证尝试登录的用户。如果验证成功，它将加载该用户的购物车，并重定向到 `/order`，以便预填充订单小部件并显示给用户。请参见代码清单 18-6。

```
ecomApp.controller('LoginController',
['$scope','$location','UserService','CartService',
function($scope,$location,UserService,CartService) {
loginCtrl.doLogin =function(){
UserService.validateUser($scope.userCredentialDTO)
.then(
function(data) {
sessionStorage.setItem('loggedUser',
JSON.stringify(data));
sessionStorage.setItem('sessionUser',data.userId);
$scope.screenName=data.firstName+ '  '+data.lastName;
$scope.$emit('userModified', $scope.screenName);
if($scope.myCart!=null){
$scope.myCart.userId=data.userId;
}
loginCtrl.loadCart();
$location.url('/order');
}
);
}
}]);
代码清单 18-6
user_controller.js (ch18\ch18-01\Ax2-Ecom-With-Security\Ecom-web\src\main\resources\static\js\controller\user_controller.js)
```

代码清单 18-7 展示了 `UserService.validateUser` 的实现。

```
'use strict';
ecomApp.factory('UserService', ['$http','$httpParamSerializer',
'$q','$cookies', function($http, $httpParamSerializer,$q,$cookies){
return {
validateUser: function(userCredentialDTO){
var data = {
grant_type:"password",
username: userCredentialDTO.userName,
password: userCredentialDTO.password,
client_id: "ecom_app"
};
var encodedData = $httpParamSerializer(data);
var authUrl=sessionStorage.getItem('apiUrl')+
"/security/oauth/token"
var req = {
method: 'POST',
url: authUrl,
headers: {
"Content-type": "application/x-www-form-urlencoded;
charset=utf-8"
},
data: encodedData
};
return $http(req)
.then(
function(response){
var token=(response.headers()['x-token']);
sessionStorage.setItem("xtoken", token);
return $http.get(sessionStorage.getItem('apiUrl')+
'/customer/customer')
.then(
function(response){
return response.data;
},
function(errResponse){
console.error('Error while getting user info'+
errResponse);
return $q.reject(errResponse);
}
);
},
function(errResponse){
console.error('Error while validating user'+
errResponse);
return $q.reject(errResponse);
}
);
}
};
}]);
代码清单 18-7
Web 应用端的用户认证 (ch18\ch18-01\Ax2-Ecom-With-Security\Ecom-web\src\main\resources\static\js\service\user_service.js)
```

当用户想要登录时，请求调用会在用户会话期间通过上述函数一次。您在此处执行两项操作：

1.  首先，您调用认证流程，将用户凭据和 `client_id` 作为请求参数提供。

2.  如果上述认证请求成功，您首先从响应中检索引用令牌并将其缓存在会话存储中，以便在需要访问任何服务器端受保护资源时，可以将其与请求参数一起提供。下一步，您调用客户端点来检索已登录用户的完整详细信息。

以上信息足以让您理解在 Web 应用端实现安全性的关键流程。

Zuul：作为 OAuth 客户端的 API 网关

安全性通过 Zuul 过滤器进行处理。您对 Zuul 同时使用了前置过滤器和后置过滤器。在 Zuul 将请求委托给实际资源服务器之前，会调用 Zuul 前置过滤器。请参见代码清单 18-8。



```
@Component
public class AuthenticationPreFilter extends ZuulFilter {
@Override
public String filterType() {
return "pre";
}
@Override
public int filterOrder() {
return 1;
}
@Override
public boolean shouldFilter() {
return true;
}
@Autowired
private CacheManager cacheManager;
@Override
public Object run() {
RequestContext ctx = RequestContext.getCurrentContext();
HttpServletRequest request = ctx.getRequest();
request.getRequestURL().toString()));
if(request.getRequestURL().indexOf("/oauth/token") > 0 &&
request.getParameter("grant_type") != null &&  (
StringUtils.equals(request.getParameter("grant_type"),
"password"))){
ctx.addZuulRequestHeader("authorization", "Basic " +
new String(Base64.getEncoder().
encode("ecom_app:ecom".getBytes())));
ctx.addZuulRequestHeader("content-type",
"application/x-www-form-urlencoded; charset=utf-8");
}else{
final String xToken = request.getHeader("x-token");
if(StringUtils.isNotEmpty(xToken)){
ValueWrapper value =
cacheManager.getCache("AUTH_CACHE").get(xToken);
if(value != null){
TokenDTO tokenDTO=(TokenDTO) value.get();
ctx.addZuulRequestHeader("Authorization", "Bearer  " +
tokenDTO.getAccess_token());
}
}
}
return null;
}
}
清单 18-8
Zuul 前置过滤器 (ch18\ch18-01\Ax2-Ecom-With-Security\Ecom-gateway\src\main\java\com\acme\ecom\gateway\zuul\auth\filter\AuthenticationPreFilter.java)
```

在 Zuul 前置过滤器中，可能存在三种主要场景：

*   如果请求是用于身份验证，Zuul 前置过滤器会添加一个头部来表示基本认证。在此过程中，它还会将 `client_id` 和 `client_secret` 一同添加到头部。请注意，`client_secret` 是只有 Zuul API 网关知道的参数，因为 Zuul API 网关就是 OAuth 客户端。

*   如果请求不是用于身份验证，则可能存在两种情况。第一种情况是用户尚未登录。在这种情况下，Zuul 前置过滤器将不执行任何操作。

*   请求不是用于身份验证的第二种情况是用户已经通过身份验证。在这种情况下，Zuul 前置过滤器假定请求头中存在一个引用令牌，并使用该令牌在其本地 EHCache 中查找实际的 JWT。目前，可以假设每当 API 网关首次收到用户会话的 JWT 时，它会先将其缓存到 EHCache 中，并与一个引用令牌关联，而返回给用户的正是这个引用令牌。检索到的 JWT 随后会被添加到请求头中，以实现 Bearer 授权。

Zuul 后置过滤器在 Zuul 从实际资源服务器接收到响应后被调用。清单 18-9 展示了你的 Zuul 后置过滤器。

```
@Component
public class AuthenticationPostFilter extends ZuulFilter {
public static final String MAGIC_KEY = "obfuscate";
@Autowired
private CacheManager cacheManager;
@Override
public String filterType() {
return "post";
}
@Override
public int filterOrder() {
return 1;
}
@Override
public boolean shouldFilter() {
RequestContext ctx = RequestContext.getCurrentContext();
HttpServletRequest request = ctx.getRequest();
return request.getParameter("grant_type") != null &&
(StringUtils.equals(request.getParameter("grant_type"),
"password"));
}
@Override
public Object run() {
RequestContext ctx = RequestContext.getCurrentContext();
HttpServletRequest request = ctx.getRequest();
if (request.getRequestURL().indexOf("/oauth/token") > 0 ) {
HttpServletResponse response = ctx.getResponse();
if (response.getStatus() == HttpServletResponse.SC_OK) {
final String responseData = CharStreams.toString(
new InputStreamReader(responseDataStream,"UTF-8"));
TokenDTO tokenDTO = new ObjectMapper().readValue(
responseData, TokenDTO.class);
long expiryTime = System.currentTimeMillis() +
((Integer.valueOf(tokenDTO.getExpires_in()) - 1) ∗ 1000);
String refToken = expiryTime + ":" + createReferenceToken(
request.getParameter("username"), expiryTime);
ctx.addZuulResponseHeader(
"Access-Control-Expose-Headers", "x-token");
ctx.addZuulResponseHeader("x-token", refToken);
cacheManager.getCache("AUTH_CACHE").put(
refToken, tokenDTO);
}
}
return null;
}
private  String createReferenceToken(String username, long expires){
StringBuilder signatureBuilder = new StringBuilder();
signatureBuilder.append(username);
signatureBuilder.append(":");
signatureBuilder.append(expires);
signatureBuilder.append(":");
signatureBuilder.append(MAGIC_KEY);
MessageDigest digest;
try {
digest = MessageDigest.getInstance("MD5");
} catch (NoSuchAlgorithmException e) {
throw new IllegalStateException("MD5 algorithm not available!");
}
return new String(Hex.encode(digest.digest(
signatureBuilder.toString().getBytes())));
}
}
清单 18-9
Zuul 后置过滤器 (ch18\ch18-01\Ax2-Ecom-With-Security\Ecom-gateway\src\main\java\com\acme\ecom\gateway\zuul\auth\filter\AuthenticationPostFilter.java)
```

Zuul 后置过滤器仅在身份验证流程中被调用。Zuul 后置过滤器的主要工作是，每当它从授权服务器首次收到用户会话的 JWT 时，会先将其缓存到 EHCache 中并与一个引用令牌关联，然后将此引用令牌作为响应头返回。

授权服务器
授权服务器构成了 OAuth 流程的核心，它将负责身份验证和授权两个方面。

对于身份验证，你使用 Spring Security 的 Web 基础设施，这是一组标准的 Servlet 过滤器。以下是 Spring 对术语的简要定义：

*   **主体（Principal）**：执行操作的用户、设备或系统。
*   **身份验证（Authentication）**：验证主体凭据是否有效的过程。
*   **授权（Authorization）**：决定是否允许主体执行某项操作。
*   **安全项（Secured item）**：受保护的资源。

你必须启用清单 18-10 中所示的安全配置类，才能使用你为验证和授权用户而定义的 JDBC 数据源。



```
@Configuration
class WebSecurityConfig extends WebSecurityConfigurerAdapter {
@Autowired
private JdbcTemplate jdbcTemplate;
@Override
@Bean
public AuthenticationManager    authenticationManagerBean()
throws Exception {
return super.authenticationManagerBean();
}
@Override
protected void configure(HttpSecurity http) throws Exception {
http
.csrf().disable()
.exceptionHandling()
.authenticationEntryPoint((request, response, authException) ->
response.sendError(HttpServletResponse.SC_UNAUTHORIZED))
.and()
.authorizeRequests()
.antMatchers("/∗∗").authenticated()
.and()
.httpBasic();
}
@Override
protected void configure(AuthenticationManagerBuilder auth)
throws Exception {
auth.jdbcAuthentication().dataSource(jdbcTemplate.getDataSource())
.usersByUsernameQuery("select user_id, password, active from
user_credential where user_id=?")
.authoritiesByUsernameQuery(
"select user.user_id, role.role from user_credential user
join  user_role role on user.id = role.user_id and
user.user_id =?");
}
}
清单 18-10
Spring Security 配置 (ch18\ch18-01\Ax2-Ecom-With-Security\Ecom-security\src\main\java\com\acme\ecom\security\oauth\WebSecurityConfig.java)
```

Java 配置方法会创建一个名为 `springSecurityFilterChain`（`DelegatingFilterProxy` 和 `FilterChainProxy`）的 Servlet 过滤器，该过滤器负责应用程序内的所有安全事宜（保护应用程序 URL、验证提交的用户名和密码、重定向到登录表单等）。

您将 `authenticationManagerBean` 声明为一个 Bean，稍后它将被注入到 Spring OAuth 安全配置中。

`WebSecurityConfigurerAdapter` 在 `configure(HttpSecurity http)` 方法中提供了默认配置，因此所有用户访问 Web 应用程序的任何 URL 都需要进行身份验证。但您可以像清单 18-10 所示那样重写此方法。

首先，配置 `HttpSecurity` 以定义哪些资源必须受保护、已授权、未授权、不受保护、登录页面、注销页面、访问被拒页面等。这里需要注意的一点是配置的顺序。特定于某些页面或 URL 的配置必须放在前面，然后是大多数 URL 通用的配置。

如果身份验证失败，您将收到预期的 401 Unauthorized 响应，随后是身份验证质询。在普通网页上下文中，浏览器会解释此质询，并通过一个简单的对话框提示您输入凭据。在浏览器环境中，登录页面不再是硬性要求，因为所有浏览器都支持基本身份验证，并且可以使用标准对话框提示用户输入凭据。此外，Spring Security 默认提供的 `BasicAuthenticationEntryPoint` 会为 401 Unauthorized 响应向客户端返回一个完整页面。这种错误的 HTML 表示形式在浏览器中也能很好地呈现，但对于像 REST API 或 Zuul API 网关这样可能更倾向于 JSON 表示形式的场景来说，它并不适用。因此，您应该通过保留 HTTP Basic 但将 401 质询更改为“Basic”以外的内容来阻止身份验证对话框。您可以在 `HttpSecurity` 配置回调中使用一行代码实现 `AuthenticationEntryPoint`：

```
authenticationEntryPoint((request, response, authException) ->
response.sendError(HttpServletResponse.SC_UNAUTHORIZED))
```

接下来是身份验证的配置。对于身份验证，Spring Security 提供了多种方法：

*   DAO 身份验证提供者是 Spring 的默认实现

Spring 在此期望提供一个 `org.springframework.security.core.userdetails.UserDetailsService` 实现来提供凭据和权限。

*   内置：内存（属性）、JDBC（数据库）、LDAP

*   自定义

*   自定义身份验证提供者

示例：在使用单点登录时获取预认证的用户详细信息

*   CAS、TAM、SiteMinder

您将使用 Java 配置中的 JDBC 身份验证，在其中可以自定义默认查询。清单 18-10 展示了如何接入 JDBC 身份验证。您需要通过提供一个 `DataSource` 来配置它。由于您使用的是自定义表，因此还需要提供选择查询来获取用户详细信息及其角色。您为 `AuthenticationManagerBuilder` 设置了两个查询，一个用于 `usersByUsernameQuery` 中的身份验证，另一个用于 `authoritiesByUserNameQuery` 中的授权。

*   `users-by-username-query`：此查询将选择用户的用户名、密码和启用状态属性，并根据您的自定义表将用户名作为参数。在实际实现中，搜索可能区分大小写或不区分大小写，具体取决于实现实例的配置方式。

*   `authorities-by-username-query`：此查询将选择用户名和用户角色，并根据您的自定义表将用户名作为参数。

身份验证处理完毕后，您就可以开始配置负责管理访问令牌的授权服务器了。Spring Security OAuth2 处理授权。要配置和启用 OAuth 2.0 授权服务器，您必须在清单 18-11 中使用 `@EnableAuthorizationServer` 注解。

```
@Configuration
@EnableAuthorizationServer
public class OAuth2Configuration extends AuthorizationServerConfigurerAdapter {
@Autowired
@Qualifier("authenticationManagerBean")
private AuthenticationManager authenticationManager;
@Override
public void configure(ClientDetailsServiceConfigurer clients)
throws Exception {
clients.inMemory()
.withClient("ecom_app")
.secret("ecom")
.scopes("PRODUCT","CUSTOMER","ORDER_HISTO",
"CORE","SHIPPING","DELIVERY")
.autoApprove(true)
.authorities("PRODUCT_READ", "PRODUCT_WRITE","CUSTOMER_READ",
"ORDER_READ","ORDER_WRITE")
.authorizedGrantTypes("implicit","refresh_token",
"password", "authorization_code");
}
@Override
public void configure(AuthorizationServerEndpointsConfigurer endpoints)
throws Exception {
endpoints.tokenStore(tokenStore()).tokenEnhancer(jwtTokenEnhancer()).
authenticationManager(authenticationManager);
}
@Bean
public TokenStore tokenStore() {
return new JwtTokenStore(jwtTokenEnhancer());
}
@Bean
protected JwtAccessTokenConverter jwtTokenEnhancer() {
KeyStoreKeyFactory keyStoreKeyFactory = new KeyStoreKeyFactory(
new ClassPathResource("jwt.jks"), "mySecretKey".toCharArray());
JwtAccessTokenConverter converter = new JwtAccessTokenConverter();
converter.setKeyPair(keyStoreKeyFactory.getKeyPair("jwt"));
return converter;
}
}
清单 18-11
Spring OAuth 安全配置 (ch18\ch18-01\Ax2-Ecom-With-Security\Ecom-security\src\main\java\com\acme\ecom\security\oauth\OAuth2Configuration.java)
```

您通过添加 `@EnableAuthorizationServer` 注解并使用 Spring 的 `AuthorizationServerConfigurerAdapter` 实现 `AuthorizationServerConfigurer` 来配置 OAuth 2.0 授权服务器。您重写了空的 `configure()` 方法。

`AuthorizationServerConfigurerAdapter` 依赖于几个可以重写以设置配置的方法。主要方法有：

*   `void configure(AuthorizationServerSecurityConfigurer security)`：您可以配置授权服务器的安全性，这实际上意味着 `/oauth/token` 端点。令牌端点（`/oauth/token`）将自动使用客户端凭据的 HTTP 基本身份验证进行保护。`/oauth/authorize` 端点也需要安全，但这是一个普通的面向用户的端点，其安全方式应与 UI 的其余部分相同。在您的情况下，您保持其原样。



*   `void configure(ClientDetailsServiceConfigurer clients)`: 该方法用于配置 `ClientDetailsService`（提供 OAuth2 客户端详细信息的服务），例如声明各个客户端及其属性。请注意，除非向 `configure` 方法提供了 `AuthenticationManager`，否则密码授权模式不会被启用（即使某些客户端被允许使用）。必须至少声明一个客户端或一个完整自定义的 `ClientDetailsService`，否则服务器将无法启动。您已注册了另一个客户端，并授权了“implicit”、“password”、“authorization_code”和“refresh_token”授权类型。为了使用“password”授权类型，您需要注入并使用 `AuthenticationManager` bean。该认证管理器将代表 Web 安全用户。

*   `void configure(AuthorizationServerEndpointsConfigurer endpoints)`: 您可以在此处配置授权服务器端点的非安全特性，例如令牌存储、令牌自定义、用户审批和授权类型。默认情况下，您无需执行任何操作，除非您需要密码授权，在这种情况下，您需要提供一个认证管理器。

默认情况下，Spring Security 会提供 UUID 格式的 `access_token` 和 `refresh_token`，然后由资源服务器进行验证。如果您的服务有大量请求，这可能会使授权服务器成为瓶颈。相反，根据之前关于令牌和 JWT 的讨论，您可以让授权服务器签发包含验证用户所需全部信息的签名 JWT。为此，您需要 `JwtTokenStore` 和 `JwtAccessTokenConverter`。您可以使用一个自定义的 `JwtAccessTokenConverter`，它将使用预生成的证书进行 JWT 签名。为此，您必须使用您的 `keyPair` 配置并配置一个自定义的 `JwtAccessTokenConverter`，如清单 18-11 所示。`JwtAccessTokenConverter` 在 JWT 编码的令牌值与 OAuth 认证信息之间进行双向转换。当令牌被授予时，它还充当 `TokenEnhancer`（一个在访问令牌被 `AuthorizationServerTokenServices` 实现存储之前对其进行增强的策略类）。`TokenStore` 代表存储 OAuth 访问令牌的存储机制。Spring 支持不同类型的令牌存储，例如 `InMemoryTokenStore`、`JDBCTokenStore`、`JwtTokenStore` 等。在您的场景中，您使用 `JwtTokenStore`。JWT 版本的存储（`JwtTokenStore`）将所有关于授权的数据编码到令牌本身中，并且不持久化任何数据。一旦您创建了 `JwtTokenStore` 和 `JwtAccessTokenConverter` bean，就可以在 `OAuth2Configuration` 类中将它们连接到 `AuthorizationServerEndpointsConfigurer`。

资源服务器

接下来，您可以使用 `@EnableGlobalMethodSecurity` 注解在任何 `@Configuration` 实例上为您的资源服务器启用基于注解的安全性。这将启用 Spring 方法级安全性。请参见清单 18-12。

```
@Configuration
@EnableGlobalMethodSecurity(prePostEnabled = true)
public class GlobalMethodSecurityConfiguration   {
}
清单 18-12
EnableGlobalMethodSecurity (ch18\ch18-01\Ax2-Ecom-With-Security\Ecom-core\src\main\java\com\acme\ecom\core\security\GlobalMethodSecurityConfiguration.java)
```

`@EnableGlobalMethodSecurity` 可以接受几个参数：

*   `prePostEnabled`：决定是否启用 Spring Security 的前置和后置注解（`@PreAuthorize`、`@PostAuthorize` 等）。
*   `secureEnabled`：决定是否启用 Spring Security 的 `@Secured` 注解。
*   `jsr250Enabled`：决定是否启用 JSR-250 注解（`@RolesAllowed` 等）。

对于任何接口或类，您应该在同一个应用程序中只启用一种类型的注解；否则，行为将无法明确定义。如果发现两个注解适用于同一个特定方法，则只会应用其中一个。

您使用 `prePostEnabled = true`，并且 Spring 的 `@PreAuthorize`/`@PostAuthorize` 注解是应用方法级安全性的首选方式。它们支持 Spring 表达式语言并提供基于表达式的访问控制。让我们看看它们是如何在这里被利用的：

*   `@PreAuthorize`：适用于在进入方法之前验证授权。`@PreAuthorize` 可以考虑登录用户的角色/权限、传递给方法的参数等。
*   `@PostAuthorize`：可以在方法执行后检查授权。它适用于验证返回值的授权；然而，它并不常用。Spring EL 提供了一个 `returnObject`，可以在表达式语言中访问，它反映了从方法返回的实际对象。

接下来，您需要配置资源服务器。`@EnableResourceServer` 是 OAuth2 资源服务器的一个便捷注解，它启用了一个 Spring Security 过滤器，该过滤器通过传入的 OAuth2 令牌对请求进行身份验证。这启用了一个名为 `OAuth2AuthenticationProcessingFilter` 的 Spring Security 过滤器，它使用传入的 OAuth2 令牌对请求进行身份验证。您应该添加此注解，并提供一个类型为 `ResourceServerConfigurer` 的 `@Bean`（例如通过 `ResourceServerConfigurerAdapter`），用于指定资源的详细信息（URL 路径和资源 ID）。您创建了一个名为 `CoreResourceServerConfiguration` 的 bean，它扩展了 `ResourceServerConfigurerAdapter` 并重写了 `configure(HttpSecurity http)` 方法以启用 Ecom-core 微服务的安全性。请参见清单 18-13。

```
@Configuration
@EnableResourceServer
public class CoreResourceServerConfiguration extends ResourceServerConfigurerAdapter{
@Autowired
TokenStore tokenStore;
@Autowired
JwtAccessTokenConverter tokenConverter;
@Override
public void configure(HttpSecurity http) throws Exception {
http
.csrf().disable()
.authorizeRequests()
.anyRequest().authenticated()
.antMatchers(HttpMethod.POST).hasAuthority("ORDER_WRITE")
.antMatchers(HttpMethod.DELETE).hasAuthority("ORDER_WRITE");
}
@Override
public void configure(ResourceServerSecurityConfigurer resources)
throws Exception {
resources.resourceId("CORE").tokenStore(tokenStore);
}
}
清单 18-13
启用资源服务器 (ch18\ch18-01\Ax2-Ecom-With-Security\Ecom-core\src\main\java\com\acme\ecom\core\security\CoreResourceServerConfiguration.java)
```

在 `configure` 方法中，您使用传入的 `HttpSecurity` 实例来保护 POST 和 DELETE 方法，并且只允许具有 `ORDER_WRITE` 权限的已认证用户访问资源。然后，您使用 `ResourceServerSecurityConfigurer` 类创建一个名为 `CORE` 的资源 ID。此资源 ID 的名称必须与授权服务器中使用的名称匹配。

接下来，您需要从加密的 JWT 中读取值。正如您已经看到的，`JwtAccessTokenConverter` 有助于在 JWT 编码的令牌值和 OAuth 认证信息之间进行双向转换。由于您使用非对称加密，并且令牌已经由授权服务器使用私钥加密，因此您现在将使用公钥对进行解密和读取。请参见清单 18-14。


```markdown

```
@Configuration
public class JwtConfiguration {
@Autowired
JwtAccessTokenConverter jwtAccessTokenConverter;
@Bean
@Qualifier("tokenStore")
public TokenStore tokenStore() {
return new JwtTokenStore(jwtAccessTokenConverter);
}
@Bean
protected JwtAccessTokenConverter jwtTokenEnhancer() {
JwtAccessTokenConverter converter =  new JwtAccessTokenConverter();
Resource resource = new ClassPathResource("public.cert");
String publicKey = null;
try {
publicKey = new String(FileCopyUtils.copyToByteArray(
resource.getInputStream()));
} catch (IOException e) {
throw new RuntimeException(e);
}
converter.setVerifierKey(publicKey);
return converter;
}
}
清单 18-14
令牌解密 (ch18\ch18-01\Ax2-Ecom-With-Security\Ecom-core\src\main\java\com\acme\ecom\core\security\JwtConfiguration.java)
```

至此，资源服务器的设置就完成了。

**注意**
证书和密钥库包含在代码示例的下载文件中。此处不涉及它们的创建过程，因为可以按照任何相关标准文献来完成。此外，示例应用中其他资源服务器（微服务）的代码不再赘述，因为它们遵循了与您刚才看到的 Ecom-core 微服务类似的设置。

**设置电子商务微服务应用**
在本节中，您将配置、构建并运行电子商务微服务应用，并执行各种测试用例。

**配置应用**
您需要在微服务中进行必要的配置更改。需要进行的更改与第 17 章中说明的类似。

**设置环境基础设施**
接下来，您需要设置一些基础设施来运行示例。这同样与第 17 章中说明的类似。

**构建并运行应用**
该应用包含 12 个微服务。构建和运行应用的方法也与第 17 章中说明的类似。

在第 17 章中，您需要按以下顺序启动前三个微服务的服务器：

```
Ecom-config
Ecom-registry
Ecom-gateway
```

我建议您接着启动以下两个微服务：

```
Ecom-security
Ecom-user
```

对于其余的微服务，您可以按任意顺序启动它们。

**测试应用**
您可以在 Chrome 浏览器中测试该应用。同样，您可以参考第 17 章中描述的步骤来端到端地测试整个应用。

**总结**
您刚刚全面探索了 OAuth 2.0，并深入研究了与微服务上下文相关的选定授权类型的特定方面。您还将身份验证和授权集成到了微服务电子商务应用中。这是一个重大的进步，因为安全性是任何企业级应用的首要关注点。在前几章的讨论中，剩下的内容是需要深入探讨将订单和库存领域紧密联系在一起的必要性，您将在下一章中进行此操作。

**19. Axon 微服务与 BASE 事务**

您在第 12 章中看到了 Axon 2 以及相关的运行示例。正如该章所述，本书将使用多个版本的 Axon。在第 12 章中，您使用了 Axon 2.4.1。它需要通过注解或使用 XML 配置来显式地装配组件。在第 12 章中特意使用 Axon 2 并基于它演示示例，是因为我希望让 Bean 的装配过程显式化，以便作为首次接触 CQRS 概念和首次使用 Axon 框架的读者，您能够清晰地理解这些概念。
在本章中，您将查看 Axon 后续版本中的示例，您将使用 Spring 5.0.7.RELEASE、Spring Boot 2.0.3.RELEASE、Axon 3.3.2 或更高版本。在 Axon 3 中，可以避免许多显式的 Bean 装配，因为 Axon 使用 Spring 应用上下文来定位构建块的具体实现，并为缺失的构建块提供默认实现。Axon 3 对 Axon 的内部工作机制进行了彻底改革，这主要是由于 Java 7 和 8 中的许多新特性。明确地说，如果您添加几个启动器依赖项，就足够了；Axon Spring Boot 组件将根据项目中拥有的其他依赖项为您设置一个基本的基础设施。例如，如果您有 JPA，那么 Axon 将自动为您配置所有基于 JPA 的组件。如果您在类上标记了 `@Aggregate` 或 `@Saga`，Axon 将自动配置操作这些聚合和 Saga 所需的组件。所有这些都将使您的工作变得更加轻松，您将在示例中很快看到这一点。
此外，如果您回想一下第 17 章“BASE 中的 ACID”部分的讨论，您将订单和库存聚合放在了一起。通过在这种情况下使用单个资源管理器（MySQL 持久化引擎），您避免了分布式事务。但是，您仍然使用了 ACID 风格的事务。
让我们看看，如果有必要，是否可以避免使用 ACID 风格的事务。

**注意**
在阅读本章时，您可能需要参考 Axon 3 参考文档^(⁴⁵) 和 API 文档^(⁴⁶)。

在本章中，您将执行以下操作：

*   通过查看代码，了解 Axon 3 与 Axon 2 的不同之处
*   将第 12 章中单 JVM 示例的 Axon 2 命令和事件处理迁移到 Axon 3
*   将第 12 章中分布式命令和事件处理示例的 Axon 2 迁移到 Axon 3
*   将第 17 章和第 18 章中的微服务电子商务应用迁移到 Axon 3
*   移除第 17 章和第 18 章中跨多个聚合使用的 ACID 风格事务
*   了解 Axon 4，以便在完成本书最后一章后，能够顺利地进行自己的探索

让我们直接开始动手实践 Axon 3。

**使用 Axon 3 在同一个 JVM 中进行命令和事件处理**
您将把第 12 章中“在同一个 JVM 中进行命令和事件处理”部分的示例从 Axon 2 迁移到 Axon 3。在此过程中，我将重点介绍主要的变化。

**设计示例场景**
由于您是在同一个示例的基础上进行构建，因此对于这个 Axon 3 示例，您将遵循第 12 章中解释的相同设计。请参考图 [12-1](https://doi.org/10.1007/978-1-4842-4501-9_12_Fig#1)。

**编写示例场景代码**
本节的所有代码示例都位于文件夹 `ch19\ch19-01` 中。请查看 `pom.xml` 以了解 Axon 依赖项的显式声明。请参见清单 19-1。

```


```
org.axonframework
axon-spring-boot-starter
3.3.2

清单 19-1
Axon 3 Maven 依赖项 (ch19\ch19-01\Ax3-Commands-Events-Same-JVM\pom.xml)
```

本示例中最重要的类，用于设置所有 Axon 组件，是 `EcomAppConfiguration`。
请参见清单 19-2。

```
@Configuration
public class EcomAppConfiguration {
@PersistenceContext
private EntityManager entityManager;
@Bean
@Qualifier("productRepository")
public GenericJpaRepository productJpaRepository(
EventBus eventBus) {
SimpleEntityManagerProvider entityManagerProvider =
new SimpleEntityManagerProvider(entityManager);
GenericJpaRepository genericJpaRepository =
new GenericJpaRepository(entityManagerProvider,
Product.class, eventBus, ((String id) -> Integer.valueOf(id)));
return genericJpaRepository;
}
@Bean
@Qualifier("orderRepository")
public GenericJpaRepository orderJpaRepository(EventBus eventBus) {
SimpleEntityManagerProvider entityManagerProvider =
new SimpleEntityManagerProvider(entityManager);
GenericJpaRepository genericJpaRepository =
new GenericJpaRepository(entityManagerProvider,
Order.class, eventBus, ((String id) -> Integer.valueOf(id)));
return genericJpaRepository;
}
}
清单 19-2
EcomAppConfiguration (ch19\ch19-01\Ax3-Commands-Events-Same-JVM\src\main\java\com\acme\ecom\EcomAppConfiguration.java)
```

将此代码与第 12 章清单 [12-3](https://doi.org/10.1007/978-1-4842-4501-9_12_Fig#PC3) 中的 `EcomAppConfiguration.java` 代码进行比较。您会发现第 12 章中的所有显式配置都已消失；然而，在当前示例中，您从基于 Axon 的架构中获得了相同的效用。

代码库中的下一个主要更改是针对 `OrderCommandHandler` 的；请参见清单 19-3。

```
@Component
public class OrderCommandHandler {
@Autowired
@Qualifier("orderRepository")
private Repository orderRepository;
@Autowired
@Qualifier("productRepository")
private Repository productRepository;
@CommandHandler
public void handle(NewOrderCommand newOrderCommand) throws Exception{
Aggregate  productAggregate =
productRepository.load(newOrderCommand.getProductId().toString());
Product product =  productAggregate.invoke(
(Product p)->{p.depreciateStock(newOrderCommand.getNumber());
return p;}
);
orderRepository.newInstance(()->new Order(new Random().nextInt(),
newOrderCommand.getPrice(), newOrderCommand.getNumber(),
OrderStatusEnum.NEW, product));
}
}
清单 19-3
OrderCommandHandler (ch19\ch19-01\Ax3-Commands-Events-Same-JVM\src\main\java\com\acme\ecom\order\commandhandler\OrderCommandHandler.java)
```

命令处理器检索订单领域对象，该对象是一个聚合，来自一个存储 JPA 注解聚合的通用仓库实现，并在其上执行方法以更改其状态。如果将清单 19-3 中 `OrderCommandHandler` 的代码与第 12 章清单 [12-8](https://doi.org/10.1007/978-1-4842-4501-9_12_Fig#PC8) 中的代码进行比较，即使功能上执行的是相同操作，您也能体会到细微的差别。

接下来，订单聚合包含管理订单的实际业务逻辑，因此负责维护不变条件。订单聚合的状态变化会导致领域事件的生成，如清单 19-4 所示。领域事件和聚合共同构成了领域模型。

```
import  static org.axonframework.commandhandling.model.AggregateLifecycle.apply;
@Entity
@Table(name="ECOM_ORDER")
public class Order{
private static final long serialVersionUID = 1L;
@Id
private Integer id;
@Column(name="PRICE")
private Double price;
@Column(name="NUMBER")
private Integer number;
@Column(name="ORDER_STATUS")
@Enumerated(EnumType.STRING)
private OrderStatusEnum orderStatus; ;
@ManyToOne(fetch=FetchType.LAZY)
@JoinColumn(name="PRODUCT_ID")
private Product product;
public Order(Integer id, Double price, Integer number,
OrderStatusEnum orderStatus, Product product){
super();
this.id = id;
this.price = price;
this.number = number;
this.orderStatus = orderStatus;
this.product = product;
apply(new OrderCreatedEvent(id, price, number,
product.getDescription(), orderStatus.toString()));
}
public Order() {
}
}
清单 19-4
订单实体 (ch19\ch19-01\Ax3-Commands-Events-Same-JVM\src\main\java\com\acme\ecom\order\model\Order.java)
```

将清单 19-4 中订单聚合的代码与第 12 章清单 [12-4](https://doi.org/10.1007/978-1-4842-4501-9_12_Fig#PC4) 中的代码进行比较。Axon 2 要求订单和产品聚合都继承 `AbstractAggregateRoot`，这是 `AggregateRoot` 接口的一个非常基础的实现，而在 Axon 3 中，您的聚合不需要继承该基类。`AggregateRoot` 必须声明一个包含聚合标识符的字段。该标识符最晚必须在发布第一个事件时初始化，并且该标识符字段必须使用 `@AggregateIdentifier` 注解进行标注。但是，如果您使用 JPA 并且在聚合上使用了 JPA 注解，Axon 也可以使用 JPA 提供的 `@Id` 注解。
聚合使用 `AggregateLifecycle.apply()` 方法来注册要发布的事件。应用事件意味着它们会立即被应用（发布）到聚合，并计划发布给其他事件处理器。
所有其他类中的代码与第 12 章“同一 JVM 中的命令和事件处理”部分下的“编写示例场景代码”小节中列出的代码保持一致，因此此处不再重复。

构建并测试示例场景
演示简单 Axon 示例所需的完整代码位于 `ch19\ch19-01` 中。要构建并运行此示例，请参考第 12 章“同一 JVM 中的命令和事件处理”部分中“构建并测试示例场景”小节提供的说明。

过程中唯一的区别在于从清理表开始的步骤。在当前示例中，您希望删除您示例中所需名称的任何表，可能多达七个：

```
drop table association_value_entry;
drop table domain_event_entry;
drop table ecom_order;
drop table ecom_product;
drop table saga_entry;
drop table snapshot_event_entry;
drop table token_entry;
drop table ecom_order_view;
drop table ecom_product_view;
```

在第 12 章的相应示例中，您只删除了四个表，区别到此为止。对于示例构建和运行的其余部分，只需遵循第 12 章的说明即可。

使用 Axon 进行分布式命令和事件处理

在这里，您将再次重构第 12 章“分布式命令和事件处理”部分中的相同设计，使其适用于 Axon 3。
不过，我将引入一个小改动，用 Spring Cloud 替换 JGroups 连接器。



使用 Spring Cloud 设计分布式命令总线
如前所述，请参考第 [12](https://doi.org/10.1007/978-1-4842-4501-9_12Fig#4) 章中图 [12-4](https://doi.org/10.1007/978-1-4842-4501-9_12Fig#4) 所示的设计。您应该已经熟悉了其中的分布式命令总线。与其他命令总线实现不同，分布式命令总线根本不会调用任何处理器。它所做的只是在不同 JVM 上的命令总线实现之间形成一个“桥梁”。

分布式命令总线由两个组件组成：一个命令总线连接器和一个命令路由器。命令总线连接器实现了微服务各个 JVM 之间的通信协议，而命令路由器则为每个传入的命令选择一个目标。路由器根据由路由策略计算出的路由键，决定命令应被路由到分布式命令总线的哪个段。这确保了只要运行时段的数目和配置没有变化，具有相同路由键的两个命令将始终被路由到同一个段。

在第 [12](https://doi.org/10.1007/978-1-4842-4501-9_12Fig#4) 章的设计中，您使用了 `JGroupsConnector`，它使用 JGroups 作为底层的发现和调度机制。分布式命令总线中与 JGroups 相关的组件位于 `axon-distributed-commandbus-jgroups` 模块中。由于 JGroups 既处理微服务节点的发现，也处理它们之间的通信，因此 `JGroupsConnector` 同时充当了命令总线连接器和命令路由器。还有另一种使用 Spring Cloud 实现命令总线之间互连和路由的方法。您可以为此使用 Eureka 或 Consul。由于您已经从第 [8](https://doi.org/10.1007/978-1-4842-4501-9_12Fig#4) 章的“Eureka，服务注册中心”一节了解了 Eureka 的工作原理，您将在重构后的 Axon 3 设计中使用它。

当您使用 Spring Cloud 时，Spring Cloud 命令路由器会利用 Spring Cloud 特有的 `ServiceInstance.Metadata` 字段来推断并告知系统中所有微服务节点其消息路由信息。因此，为了与 Axon 结合使用，所选的 Spring Cloud 实现应支持 `ServiceInstance.Metadata` 字段的使用。如果所需的 Spring Cloud 实现不支持修改 `ServiceInstance.Metadata`（例如 Consul），还有其他替代方法，此处不再赘述。

使用 Spring Cloud Eureka 最简单的方法是使用 Axon 分布式命令总线模块的 Spring Cloud 启动器。一旦包含了启动器，只需在应用程序上下文中添加一个属性即可启用分布式命令总线：

```
axon.distributed.enabled=true
```

下一个独立于连接器类型使用的设置是：

```
axon.distributed.load-factor=100
```

通过这样做，当应用程序上下文中同时存在命令路由器和命令总线连接器时，Axon 将默认配置一个分布式命令总线。Spring Cloud 连接器设置是 Spring Cloud 命令路由器和 Spring HTTP 命令总线连接器的组合；它们分别充当分布式命令总线的命令路由器和命令总线连接器。Spring Cloud 命令路由器使用 Spring Cloud 的 Discovery Clients 来传播其命令消息路由信息，并发现其他 Axon 微服务节点并检索它们的消息路由信息。它通过利用 `ServiceInstance.Metadata` 来存储消息路由信息来实现这一点。出于性能原因，任何通过 Discovery Client 系统发现的、不包含任何所需消息路由信息字段的其他节点将被列入黑名单，从而避免对该节点执行不必要的额外检查。

Spring HTTP 命令总线连接器会将给定的命令发送到被分配处理具有给定路由键消息的节点。发送方不期望收到回复，因此如果 `send` 方法抛出异常，则可以保证命令的目标节点未收到该命令。如果方法正常返回，则连接器的具体实现定义了交付保证。由于连接器基于给定的路由键路由命令，使用相同的路由键将导致命令被发送到同一个成员。此外，每条消息必须恰好发送给一个成员。

在使用 Spring Cloud 命令路由器时，您需要处理的另一个方面是，您的 Spring 应用程序应启用心跳事件。Spring Cloud 命令路由器实现利用您的 Spring Cloud 微服务发布的心跳事件来检查其对所有其他节点的了解是否是最新的。因此，如果禁用了心跳事件，集群内的微服务将无法感知整个设置，从而给正确的命令路由带来问题。

因此，图 [12-5](https://doi.org/10.1007/978-1-4842-4501-9_12Fig#5) 变成了图 [19-1](https://doi.org/10.1007/978-1-4842-4501-9_12Fig#5)。

![../images/477630_1_En_19_Chapter/477630_1_En_19_Fig1_HTML.jpg](img/477630_1_En_19_Fig1_HTML.jpg)

图 19-1.
使用 Spring Cloud 的分布式命令总线

接下来您将查看代码。我将指出任何细微的差别。

编写示例场景的代码
演示分布式 Axon 示例所需的完整代码位于 `ch19\ch19-02` 中。在第 [12](https://doi.org/10.1007/978-1-4842-4501-9_12Fig#5) 章的示例代码中有五个微服务。您将添加一个新的微服务用于基于 Eureka 的服务注册中心。

微服务 1：00-Ecom-registry
该微服务是一个典型的 Spring Cloud Eureka 微服务应用程序，不包含任何 Axon 组件，您已经在之前的许多示例中看到过，因此不再赘述。

微服务 2：01-Ecom-web
该微服务是一个典型的 Spring Boot Web 应用程序，同样不包含任何 Axon 组件，因此不再赘述。

微服务 3：02-Ecom-CreateCommandRestController

请查看 `pom.xml` 以了解 `axon-distributed-commandbus` 依赖项。参见清单 [19-5](https://doi.org/10.1007/978-1-4842-4501-9_12Fig#5)。

```

org.springframework.boot
spring-boot-starter-parent
2.0.3.RELEASE

5.0.7.RELEASE
3.3.2

org.axonframework
axon-spring-boot-starter
${axonVersion}

org.axonframework
axon-distributed-commandbus-springcloud
${axonVersion}

org.springframework.cloud
spring-cloud-starter-netflix-eureka-client

org.springframework.cloud
spring-cloud-dependencies
Finchley.RELEASE
pom
import

清单 19-5
分布式命令总线 Maven 依赖项 (ch19\ch19-02\Ax3-Commands-Multi-Event-Handler-Distributed\02-Ecom-CreateCommandRestController\pom.xml)
```

您使用了 `axon-distributed-commandbus-springcloud` 模块，该模块包含分布式命令总线中与 Spring Cloud 连接器相关的组件。



该微服务的下一个主要更新位于主应用类中。请参见代码清单 19-6。

```
@SpringBootApplication
@EnableDiscoveryClient
public class EcomCreateCommandAndRestApplication {
public static void main(String[] args) {
SpringApplication.run(EcomCreateCommandAndRestApplication.class, args);
}
}
代码清单 19-6
Ecom-CreateCommandRestController 主类 (ch19\ch19-02\Ax3-Commands-Multi-Event-Handler-Distributed\02-Ecom-CreateCommandRestController\src\main\java\com\acme\ecom\EcomCreateCommandAndRestApplication.java)
```

必须通过使用 `@EnableDiscoveryClient` 注解你的 Spring Boot 应用来创建一个 Spring Cloud 命令路由器。完成后，它将在你的类路径上查找 Spring Cloud 的实现。

代码中还有其他一些变化。让我们看看代码清单 19-7 中 Axon 组件的配置。

```
@Configuration
public class EcomAppConfiguration {
}
代码清单 19-7
Ecom-CreateCommandRestController 配置 (ch19\ch19-02\Ax3-Commands-Multi-Event-Handler-Distributed\02-Ecom-CreateCommandRestController\src\main\java\com\acme\ecom\EcomAppConfiguration.java)
```

如你所见，与代码清单 [12-15](https://doi.org/10.1007/978-1-4842-4501-9_12_Fig#PC22) 和代码清单 [12-16](https://doi.org/10.1007/978-1-4842-4501-9_12_Fig#PC23) 中的代码相比，代码清单 19-7 完全不包含配置 Axon 3 组件所需的显式配置。这一切都由 Spring 自动装配完成！

微服务 4：
03-Ecom-HandleCommandAndCreateEvent

该微服务必须处理由 02-Ecom-CreateCommandRestController 微服务创建并通过分布式命令总线从远程 JVM 到达的任何命令。与第 12 章中的代码相比，该微服务代码的唯一变化是通过使用 `@EnableDiscoveryClient` 注解 Spring Boot 应用来引入 Spring Cloud 命令路由器，如上一个微服务所示，因此我将直接在代码清单 19-8 中展示代码，不做详细解释。

```
@EnableDiscoveryClient
@SpringBootApplication
public class EcomHandleCommandAndCreateEventApplication {
public static void main(String[] args) {
SpringApplication.run(
EcomHandleCommandAndCreateEventApplication.class, args);
}
}
代码清单 19-8
主应用类 (ch19\ch19-02\Ax3-Commands-Multi-Event-Handler-Distributed\03-Ecom-HandleCommandAndCreateEvent\src\main\java\com\acme\ecom\EcomHandleCommandAndCreateEventApplication.java)
```

微服务 5：04-Ecom-EventHandleCore
同样，由于你已经学习了第 12 章中的相应示例，该微服务中没有任何新内容。因此，我将不进行详细的代码讲解。正如微服务名称所示，它包含两个主要事件处理器：OrderEventHandler 和 ProductEventHandler，其功能和代码片段与本章前面的示例类似。但是，这里需要提一下一个小更新。

正如你已经看到的，所有包含 `@EventHandler` 注解方法的单例 Spring Bean 组件都将订阅事件处理器，以接收发布到事件总线的事件消息。事件处理器也可以在 application.properties 中进行配置。

```
axon.eventhandling.processors.name.mode=tracking
axon.eventhandling.processors.name.source=eventBus
```

这里的 source 属性指的是实现 SubscribableMessageSource 或 StreamableMessageSource 的 Bean 的名称，该 Bean 将用作所选处理器的事件源，默认为应用上下文中定义的事件总线或事件存储。接下来，你可以标记使用所谓的跟踪处理器来异步处理事件。该处理器将跟踪事件存储（或任何其他支持跟踪的源）中的事件。

要配置处理器切换到跟踪模式，你需要执行以下操作：

1.  首先，覆盖处理组的名称。默认情况下，处理组（以及代表它处理事件的处理器）的名称是分配给它的事件处理器的包名。你可以通过放置 `@ProcessingGroup` 来覆盖它。

2.  接下来，在 application.properties 中，将消息处理器配置为跟踪模式：

```
    axon.eventhandling.processors.messages.mode=tracking.
    ```

这里，messages 部分是处理器的名称。检查 application.properties 以查看此内容。请参见代码清单 19-9。

```
axon.eventhandling.processors[ECOM_EVENT_PROCESSOR].mode=tracking
axon.eventhandling.processors[ECOM_EVENT_PROCESSOR].source=eventBus
代码清单 19-9
Ecom-EventHandleCore 配置 (ch19\ch19-02\Ax3-Commands-Multi-Event-Handler-Distributed\04-Ecom-EventHandleCore\src\main\resources\application.properties)
```

然后，你附加处理组以使其能够处理正确的事件，如代码清单 19-10 所示。

```
@Component
@ProcessingGroup("ECOM_EVENT_PROCESSOR")
public class OrderEventHandler {
@EventHandler
public void handleOrderCreatedEvent(OrderCreatedEvent event) {
//代码写在这里
}
}
代码清单 19-10
订单事件处理器 (ch19\ch19-02\Ax3-Commands-Multi-Event-Handler-Distributed\04-Ecom-EventHandleCore\src\main\java\com\acme\ecom\order\eventhandler\OrderEventHandler.java)
```

类似地，你附加处理组以使其能够处理与库存相关的事件，如代码清单 19-11 所示。

```
@Component
@ProcessingGroup("ECOM_EVENT_PROCESSOR")
public class ProductEventHandler {
@EventHandler
public void handleProductStockUpdatedEvent(StockUpdatedEvent event) {
//代码写在这里
}
}
代码清单 19-11
产品事件处理器 (ch19\ch19-02\Ax3-Commands-Multi-Event-Handler-Distributed\04-Ecom-EventHandleCore\src\main\java\com\acme\ecom\product\eventhandler\ProductEventHandler.java)
```

微服务 6：05-Ecom-EventHandlerAudit
该微服务的功能和代码与你已经在第 12 章中看到的 EventHandlerAudit 微服务相同。唯一的变化与刚刚描述的微服务中附加处理组的方式类似，因此我将不再重复该信息。

构建并测试示例场景
按照第 12 章中“分布式命令和事件处理”部分下的“构建并测试示例场景”子部分的说明来构建和测试该示例。此示例的唯一区别是你多了一个微服务，即 Eureka 注册中心。你应该首先构建并运行 Eureka 注册中心微服务。然后按照第 12 章中描述的步骤进行操作。



Axon 3 CQRS 微服务电商与 BASE 事务
在本节中，你的主要目标是将你在第 17 章和第 18 章构建的电商示例应用从 Axon 2 迁移到 Axon 3。此外还有一个次要目标，我现在就来定义它。

如果你还记得第 17 章“BASE 中的 ACID”一节中的讨论，当时你将订单和库存聚合放在了一起。这在现实场景中可能发生，也可能不会发生。订单或预订实体由销售渠道和最终用户活动驱动，而库存或存货实体则由最终用户和供应链活动驱动。因此，负责管理这些实体的应用程序、系统或微服务可能并非同一个，在许多情况下，它们会是独立的微服务。通过在第 17 章和第 18 章的示例中使用单一资源管理器（MySQL 持久化引擎），你避免了分布式事务。然而，你仍然使用了 ACID 风格的事务，因为这两个实体都紧密地保存在单个微服务中，并且你利用了 ACID 风格的本地事务。但如果你想将它们分开呢？

移除订单与库存领域之间的 ACID 束缚

你首先将回顾第 17 章的代码，其中你在两个聚合根实体（订单和库存）上执行了 ACID 风格的事务。第 17 章中的清单 [17-6](https://doi.org/10.1007/978-1-4842-4501-9_17_Fig#PC10) 在此处作为清单 19-12 重复列出。

```
@Component
public class OrderCommandHandler {
@Autowired
@Qualifier(value = "orderRepository")
private Repository orderRepository;
@CommandHandler
public void handleNewOrder(OrderCreateCommand orderCreatedCommand) {
Order order = new Order(Long.valueOf(new Random().nextInt()));
order.setOrderDate(new Date());
order.setOrderStatus(OrderStatus.PAID);
order.setUserId(orderCreatedCommand.getUserId());
double total = 0;
if (orderCreatedCommand.getLineItems() != null) {
for (LineItemDTO lineItemDto :
orderCreatedCommand.getLineItems()) {
if (lineItemDto.getInventoryId() != null) {
LineItem lineItem = new LineItem(new Random().nextLong(),
lineItemDto.getProductId(), lineItemDto.getQuantity(),
lineItemDto.getPrice(), lineItemDto.getInventoryId());
total = total + lineItemDto.getPrice();
order.addLineItem(lineItem);
Inventory inventory =
inventoryRepository.load(lineItemDto.getInventoryId());
inventory.updateProductStock(lineItemDto.getQuantity(),
ProductStockOperation.DEPRECIATE);
}
}
}
order.setTotal(total);
order.notifyOrderCreation();
orderRepository.add(order);
}
}
清单 19-12
OrderCommandHandler handleNewOrder (ch17\ch17-01\Ax2-Ecom-No-Security\Ecom-core\src\main\java\com\acme\ecom\core\order\command\handler\OrderCommandHandler.java)
```

你在第 12 章中了解到，工作单元（UoW）是对聚合进行的一组修改。命令的执行通常发生在 UoW 的范围内。当清单 19-12 中的命令处理方法执行完毕时，UoW 被提交，所有操作最终确定。这意味着任何仓库都会收到其聚合状态变更的通知，并且计划发布的事件会被发送到事件总线。这是一种便捷的分组方式，有助于防止在处理多个聚合之前发布单个事件。在后台，它允许仓库在整个事务中管理资源，例如锁。例如，锁只有在 UoW 被提交或回滚时才会被释放。
UoW 并不能替代传统的 ACID 风格事务。它仅仅是一个缓冲区，用于对变更进行分组和暂存。当 UoW 被提交时，所有暂存的变更才会被提交；然而，这种提交并非原子性的。这意味着如果提交失败，可能部分变更已被持久化，而其他变更则没有。Axon 最佳实践规定，应遵循一条命令始终只包含一个操作的最佳实践。
Axon 2 中的 UoW 允许一条命令在多个聚合上执行逻辑。UoW 在管理工作单元（由命令处理器完成）方面扮演着更核心的角色。UoW 当前会跟踪由仓库加载的聚合，并在 UoW 提交时持久化它们。

如果你的 UoW 中包含多个操作或多个聚合，那么你应该考虑将事务附加到 UoW 的提交上。在第 17 章中，你在清单 [17-7](https://doi.org/10.1007/978-1-4842-4501-9_17_Fig#PC11) 中这样做了，具体代码在此处作为清单 19-13 重复列出。

```
@Configuration
@RefreshScope
public class EcomCoreAppConfiguration {
@Autowired
@Qualifier("transactionManager")
protected PlatformTransactionManager txManager;
@Bean
public SimpleCommandBus commandBus() {
SimpleCommandBus simpleCommandBus = new SimpleCommandBus();
simpleCommandBus.setDispatchInterceptors(
Arrays.asList(new BeanValidationInterceptor()));
SpringTransactionManager transcationMgr =
new SpringTransactionManager(txManager);
simpleCommandBus.setTransactionManager(transcationMgr);
return simpleCommandBus;
}
}
清单 19-13
将事务绑定到工作单元 (ch17\ch17-01\Ax2-Ecom-No-Security\Ecom-core\src\main\java\com\acme\ecom\core\EcomCoreAppConfiguration.java)
```

这里，`SpringTransactionManager` 是一个 `TransactionManager` 实现，它使用 `PlatformTransactionManager` 作为底层事务管理器。
这里你在单个事务中使用了两个聚合实体（库存和订单），但由于这两个聚合实体由同一个资源管理器管理，并且都在单个节点上，因此不涉及分布式事务。
可以将事务绑定到 UoW。许多命令总线实现，例如 `SimpleCommandBus` 和 `DisruptorCommandBus`，允许你配置一个事务管理器。然后，该事务管理器将用于创建事务，并将其绑定到用于管理命令处理过程的 UoW。这样，命令处理过程就可以被视为一个原子过程；它要么被完全处理，要么完全不处理。Axon 框架使用 UoW 来跟踪命令处理器执行的操作。当命令处理器完成时，Axon 将尝试提交在 UoW 中注册的操作。当 UoW 绑定到事务时，它将确保绑定的事务在正确的时间点被提交。
接下来，我们转向 Axon 3，这里有一些不同之处。



注意
在第 12 章的“分布式命令与事件处理”示例中，你配置了事务管理器，而在同一示例的 Axon 3 迁移版本中，你并未配置或使用任何显式的事务管理器。

如果你在事务性命令处理器中处理多个聚合根，Axon 3 会报错。这意味着清单 19-12 和清单 19-13 的组合在 Axon 3 中难以正常工作。让我们对电商微服务 Ecom-core 的设计稍作重构。

清单 19-14 展示了以分布式方式连接命令总线和事件总线所需的最低配置。在代码中，你为命令总线附加了一个事务管理器，并为事件队列附加了一个 Saga 事件处理器。

```
@Configuration
public class EcomCoreAppConfiguration {
@Value("${axon.amqp.exchange}")
private String rabbitMQExchange;
@Value("${axon.amqp.queue}")
private String rabbitMQQueue;
@Bean
public FanoutExchange eventBusExchange() {
return new FanoutExchange(rabbitMQExchange, true, false);
}
@Bean
public Queue eventBusQueue() {
return new Queue(rabbitMQQueue, true, false, false);
}
@Bean
public Binding binding() {
return BindingBuilder.bind(eventBusQueue()).to(eventBusExchange());
}
@Bean
public SpringAMQPMessageSource ecomCoreEventQueue(
AMQPMessageConverter       messageConverter) {
return new SpringAMQPMessageSource(messageConverter) {
@RabbitListener(queues = "${axon.amqp.queue}")
@Override
@Transactional
public void onMessage(Message message, Channel channel) {
super.onMessage(message, channel);
}
};
}
@Bean
public CommandBus commandBus(org.axonframework.common.transaction.
TransactionManager transactionManager) {
SimpleCommandBus commandBus = new AsynchronousCommandBus();
commandBus.registerHandlerInterceptor(
new TransactionManagingInterceptor(transactionManager));
return commandBus;
}
@Bean("orderSagaConfiguration")
public SagaConfiguration orderSagaConfiguration() {
return SagaConfiguration.trackingSagaManager(
OrderProcessSaga.class,"ECOM_CORE_EVENT_PROCESSOR");
}
}
清单 19-14
EcomCoreAppConfiguration (ch19\ch19-03\Ax3-Ecom-With-Security\Ecom-core\src\main\java\com\acme\ecom\core\EcomCoreAppConfiguration.java)
```

清单 19-15 展示了你在清单 19-14 中看到的参数值的配置。

```
axon:
amqp:
exchange: Ecom-axon3-exchange
queue: Ecom-core-queue
eventhandling:
processors:
ECOM_CORE_EVENT_PROCESSOR:
source: ecomCoreEventQueue
清单 19-15
ecom-core.yml (ch19\ch19-03\Ax3-Ecom-With-Security\config-repo\ecom-core.yml)
```

接下来，让我们看看需要对清单 19-12 中 OrderCommandHandler 的代码进行的主要重构。你将 OrderCommandHandler 重写为清单 19-16 所示。

```
@Component
public class OrderCommandHandler {
@Autowired
@Qualifier(value = "orderRepository")
private GenericJpaRepository orderRepository;
@Autowired
private CommandGateway commandGateway;
@Transactional
@CommandHandler
public void handleNewOrder(OrderCreateCommand orderCreatedCommand
)throws Exception {
Long orderId = Long.valueOf(new Random().nextInt());
Order order = new Order(orderId);
order.setOrderDate(new Date());
order.setOrderStatus(OrderStatus.PAID);
order.setUserId(orderCreatedCommand.getUserId());
double total = 0;
if (orderCreatedCommand.getLineItems() != null) {
for (LineItemDTO lineItemDto :
orderCreatedCommand.getLineItems()) {
if (lineItemDto.getInventoryId() != null) {
LineItem lineItem =
new LineItem(new Random().nextLong(),
lineItemDto.getProductId(),
lineItemDto.getQuantity(), lineItemDto.getPrice(),
lineItemDto.getInventoryId());
total = total + lineItemDto.getPrice();
order.addLineItem(lineItem);
}
}
}
order.setTotal(total);
Aggregate orderAggregate =  orderRepository.newInstance(
()->order);
orderAggregate.invoke((Order orderSaved) ->{
orderSaved.notifyOrderCreation();
return orderSaved;});
}
}
清单 19-16
为 Axon 3 重构后的订单命令处理器 (ch19\ch19-03\Ax3-Ecom-With-Security\Ecom-core\src\main\java\com\acme\ecom\core\order\command\handler\OrderCommandHandler.java)
```

你从 OrderCommandHandler 中移除了所有与库存相关的操作。相反，你执行了创建完整订单及其所有订单项所需的所有验证和计算，如清单 19-16 所示。与库存相关的操作被移到了另一个处理器 InventoryCommandHandler 中，如清单 19-17 所示。

```
@Component
public class InventoryCommandHandler {
@Autowired
@Qualifier(value = "inventoryRepository")
private GenericJpaRepository inventoryRepository;
public void setInventoryRepository(GenericJpaRepository
inventoryRepository) {
this.inventoryRepository = inventoryRepository;
}
@CommandHandler
@Transactional
public void handleInventoryCreation(InventoryCreateCommad
inventoryCreatedCommand)  {
Integer id = new Random().nextInt();
try{
inventoryRepository.newInstance(()->new Inventory(Long.valueOf(id),
inventoryCreatedCommand.getSku(),
inventoryCreatedCommand.getQuantity()));
}catch(Exception ex){
LOGGER.error("InventoryCommandHandler error ", ex);
}
}
@CommandHandler
@Transactional
public void handleInventoryUpdation(InventoryUpdateCommand
inventoryUpdateCommand)  {
Aggregate  inventoryAggregate =
inventoryRepository.load(inventoryUpdateCommand.getLineItemDTO().
getInventoryId().toString());
inventoryAggregate.invoke((Inventory inventory) ->
{inventory.updateProductStock(
inventoryUpdateCommand.getLineItemDTO().getQuantity(),
inventoryUpdateCommand.getProductStockOperation());
return inventory;
});
}
}
清单 19-17
为 Axon 3 重构后的库存命令处理器 (ch19\ch19-03\Ax3-Ecom-With-Security\Ecom-core\src\main\java\com\acme\ecom\core\inventory\command\handler\InventoryCommandHandler.java)
```

在清单 19-16 的 OrderCommandHandler 和清单 19-17 的 InventoryCommandHandler 中，你需要注意到一个方面：所有处理器方法都在事务上下文中执行。然而，这里没有分布式事务或两阶段提交操作，也没有涉及多个聚合根的 ACID 事务。只有局限于单个聚合根实体的本地事务。



到目前为止，一切顺利。现在让我们进入业务编排部分。库存折旧和创建新订单的编排工作，现在将从清单 19-12 的 `OrderCommandHandler` 转移到清单 19-18 的 `OrderController` REST 控制器中。其核心要点是，对这两个实体的操作要么全部成功，要么所有操作都必须进行补偿回滚！

```java
@RestController
@RequestMapping("/order")
public class OrderController {
@Autowired
private CommandGateway commandGateway;
@RequestMapping(method = RequestMethod.POST)
@ResponseBody
public OrderCreationStatus createOrder(@RequestBody OrderDTO orderDTO) {
OrderCreationStatus orderCreationStatus = null;
//步骤 1：折旧产品库存.............
List inventoryUpdatedList = new ArrayList();
try {
for (LineItemDTO lineItemDTO : orderDTO.getLineItems()) {
commandGateway.sendAndWait(new InventoryUpdateCommand(
lineItemDTO, ProductStockOperation.DEPRECIATE));
inventoryUpdatedList.add(lineItemDTO);
}
} catch (Exception ex) {
Throwable e = ex.getCause();
if (e instanceof OutOfStockException) {
LOGGER.error("由于缺货，创建新订单时出错", e);
orderCreationStatus = OrderCreationStatus.OUT_OF_STOCK;
} else {
LOGGER.error("更新库存时出现未知错误", e);
orderCreationStatus = OrderCreationStatus.FAILED;
}
//步骤 2：任何异常，回滚已更新的库存
for (LineItemDTO lineItemDTO : inventoryUpdatedList) {
commandGateway.send(new InventoryUpdateCommand(
lineItemDTO, ProductStockOperation.ADD));
}
return orderCreationStatus;
}
//步骤 3：创建新订单
try {
OrderCreateCommand orderCommand =
new OrderCreateCommand(orderDTO.getUserId(),
orderDTO.getLineItems());
commandGateway.sendAndWait(orderCommand);
orderCreationStatus = OrderCreationStatus.SUCCESS;
} catch (Exception ex) {
LOGGER.error("创建新订单时出现未知错误", ex);
orderCreationStatus = OrderCreationStatus.FAILED;
// 步骤 4：任何异常，回滚所有已更新的库存
for (LineItemDTO lineItemDTO : orderDTO.getLineItems()) {
commandGateway.send(new InventoryUpdateCommand(lineItemDTO,
ProductStockOperation.ADD));
}
}
return orderCreationStatus;
}
@RequestMapping(value = "{orderId}", method = RequestMethod.DELETE)
@ResponseBody
public void cancelOrder(@PathVariable Long orderId) {
OrderCancelCommand orderCommand = new OrderCancelCommand(orderId);
commandGateway.send(orderCommand);
}
}
清单 19-18
Order REST 控制器 (ch19\ch19-03\Ax3-Ecom-With-Security\Ecom-core\src\main\java\com\acme\ecom\core\web\OrderController.java)
```

在清单 19-18 中，你首先需要注意的一点是，HTTP POST 方法 `createOrder` 并不在任何类型的事务之内。这正是你执行库存折旧和创建新订单编排的地方。这意味着，虽然聚合根实体操作发生在清单 19-16 中 `OrderCommandHandler` 的命令处理器操作和清单 19-17 中 `InventoryCommandHandler` 的本地事务内，但它们的编排是在没有任何封闭事务上下文的情况下完成的，如清单 19-18 所示。这意味着异常和错误可能发生在任何一行代码（即指令或操作）执行时（或之后），在这种情况下，到目前为止已执行的操作也必须进行补偿回滚！

清单 19-18 显示，在步骤 1 中，你根据 Order POST 请求中的行项目数量，对每个行项目执行 `sendAndWait` 发送 `InventoryUpdateCommand`。`sendAndWait` 将一个 `InventoryUpdateCommand` 分派到命令网关，以便该命令能被清单 19-17 中的 `InventoryCommandHandler` 拾取。此外，由于 `OrderController` 和 `InventoryCommandHandler` 位于同一个微服务（或分区）中，并且 `sendAndWait` 是一个同步阻塞调用，因此如果 `sendAndWait` 成功，你可以确信预期的操作已确定执行。对于所有 `sendAndWait` 成功的行项目，你将它们记录在 `inventoryUpdatedList` 中，以便在需要时稍后执行补偿操作。如果你能够成功地为订单的所有行项目执行 `sendAndWait` 发送 `InventoryUpdateCommand`，那么你将进入步骤 3，开始创建订单。换句话说，在继续创建订单之前，你要确保有足够的库存，并且折旧了所有必需的库存。

在检查库存时，任何行项目都可能因库存不足或其他故障而发生错误。无论原因如何，在这种情况下都无法完成整个订单，因此你需要对已为该订单生效的任何库存变更进行补偿。这在清单 19-18 中标记为步骤 2。

如果步骤 1 完全成功且没有任何错误，则意味着你已有足够的库存来履行订单，因此你开始执行步骤 3。在这里，你再次创建一个 `OrderCreateCommand` 并通过 `sendAndWait` 发送到命令网关。清单 19-16 中处理新订单创建的 `OrderCommandHandler` 同样位于同一个微服务中，因此成功的 `sendAndWait` 足以确定订单创建成功。此阶段的异常也意味着你需要对已为该订单生效的任何库存变更进行补偿。这在清单 19-18 中标记为步骤 4。

这是一个 BASE 事务的示例；然而，整体事务并非万无一失。这意味着，如果在执行 `createOrder` HTTP REST POST 方法期间发生微服务崩溃，`createOrder` 中的其余指令将不会被执行；极有可能出现以下状态：

*   订单中对应行项目的库存已更新；但订单未创建，且库存未恢复。

*   订单中所有行项目对应的库存已更新；但订单未创建，且库存未恢复。

*   订单中所有行项目对应的库存已更新；但订单未创建，且仅恢复了部分行项目对应的库存。



系统可能无法最终达到一致性，因为将系统带回最终一致状态所需的“引力”随着微服务的崩溃而消失了！更明确地说，将系统恢复到语义一致状态所需的基本状态已经丢失，因为你没有使微服务栈的状态持久化。

**将订单和库存领域划分为独立的微服务**

你已看到，清单 19-12 中 `OrderCommandHandler` 的 `handleNewOrder` 处理过程，相当于对订单和库存实体执行了一次 ACID 风格的操作。然而，在本章的重构中，你将代码转换为了清单 19-18 中 `OrderController` 的 `createOrder` HTTP POST 方法。这里的处理过程相当于 BASE 风格，但没有任何固有的故障转移特性。在采用了跨订单和库存聚合根实体的 BASE 风格事务后，订单和库存领域现在已成为彼此领域独立的“一等公民”，因此如果需要，它们也可以被分离到各自的微服务中。

你不会在此处实现图 19-2 所示的重构。相反，这将是后续的练习。下一小节将提供更多相关提示。

![../images/477630_1_En_19_Chapter/477630_1_En_19_Fig2_HTML.jpg](img/477630_1_En_19_Fig2_HTML.jpg)

**图 19-2.** 为 Axon 3 重构的微服务电商架构

**用于确定性最终一致性的 Saga**

在采用了跨订单和库存聚合根实体的 BASE 风格事务后，订单和库存领域现在可以成为独立的微服务。然而，有一个问题！一旦你将订单和库存领域分离到独立的微服务中，以下组件将不再位于同一位置：

*   清单 19-16 中所示的 `OrderCommandHandler`
*   清单 19-17 中所示的 `InventoryCommandHandler`
*   清单 19-18 中所示的 `OrderController`

如果是这样，向一个或多个命令处理器发送的 `sendAndWait` 将跨网络分区执行，因此其确定性特性将丢失。如果你继续按照清单 19-17 中 `InventoryCommandHandler` 的方式建模 BASE 事务，那么它失败的可能性将超出我已解释的范围。

手动建模 BASE 事务的方式在这里不是一个好主意。由于以下特性，你需要在此处使用 Saga 事务：

*   Saga 是长生命周期的。
*   Saga 是持久化的。即使微服务崩溃，Saga 也可以被恢复。
*   Saga 可以建模复杂的补偿场景。
*   Saga 的事务或补偿可以被重放或重试。

无论最终一致性在时间线上处于哪个点，尚未结束的 Saga 总是倾向于将系统（带）回到一致状态。
当订单和库存领域被分离到不同的微服务时，你可以很好地使用 Saga 来建模多行商品订单的创建。Saga 可以跟踪每个库存消耗的状态，一旦满足条件，就继续创建新订单，然后结束 Saga。在这个例子中，我没有这样做，因为我不想让演示过于复杂，因为它已经包含了一个 Saga。

**配置电商微服务应用**

请遵循第 18 章中“配置电商微服务应用”和“设置环境基础设施”部分的说明。

**构建并运行电商微服务应用**

同样，你应该遵循第 18 章中“构建并运行安全的电商微服务应用”部分的说明。在了解了 Axon 3 之后，让我们在下一节看看 Axon 4。

**使用 Axon 4 在同一个 JVM 中进行命令和事件处理**

在本节中，你将把第 12 章“在同一个 JVM 中进行命令和事件处理”部分讨论的代码迁移到 Axon 4。迁移后的代码库位于文件夹 `ch19\ch19-04` 中。在阅读代码时，你可能需要参考 Axon 4 参考文档^(⁴⁷) 和 Axon 4 API 文档^(⁴⁸)。
实际上，这段代码是对本章前面“使用 Axon 3 在同一个 JVM 中进行命令和事件处理”部分中看到的代码的重构。要构建并执行该示例，请遵循那里提到的说明，但有一个额外的步骤需要你作为第一步来完成，那就是设置 Axon 服务器。

Axon 服务器通过了解正在交换的不同类型的消息（例如从一个服务发送到一个或多个其他服务的事件、发送到一个服务执行某项操作的命令等）来补充 Axon 框架。你可以从 [`​download.​axoniq.​io/​axonserver/​AxonServer.​zip`](https://download.axoniq.io/axonserver/AxonServer.zip) 下载并解压 Axon 服务器库。

```
cd D:\binil\Study\Sites\axon\AxonQuickStart-4.0.3_2019-02-19\AxonServer
java -jar axonserver-4.0.3.jar
```

这将启动 Axon 服务器。随后，请遵循本章前面“使用 Axon 3 在同一个 JVM 中进行命令和事件处理”部分中提到的说明。

**使用 Axon 进行分布式命令和事件处理**

在本节中，你将把第 12 章“分布式命令和事件处理”部分讨论的代码迁移到 Axon 4。迁移后的代码库位于文件夹 `ch19\ch19-05` 中。
实际上，这段代码是对本章前面“使用 Axon 3 进行分布式命令和事件处理”部分中看到的代码的重构。要构建并执行该示例，请遵循此处提到的说明，但有一个额外的步骤需要你作为第一步来完成，那就是设置 Axon 服务器。
请遵循上一节的说明来启动 Axon 服务器。一旦 Axon 服务器启动，请遵循本章前面“使用 Axon 3 进行分布式命令和事件处理”部分中提到的说明。

**总结**

每一次结束都是新的开始。你已经读完了这本书；然而，你的微服务之旅才刚刚开始。我们揭开了围绕架构选择的一些困惑，并深入研究了包括数据一致性在内的几个关注点的每一个细节，这些在你日常采用微服务架构时可能会遇到。我希望你喜欢这本书的许多部分，就像我喜欢花时间深入代码细节来解释它们一样。我试图平衡理论量和代码量，让你对几乎所有解释过的概念都有动手实践的感觉。最后但同样重要的是，我还试图通过本章涵盖 Axon CQRS 框架的最新版本。框架会来来去去，规范会不断演进，因为“唯一不变的就是变化。”然而，本书讨论的架构基础仍然具有相关性和有效性——至少这是我在 25 年 IT 职业生涯中所经历的。继续前进，保持快速步伐。

**脚注**

安装、配置和运行 MongoDB



MongoDB 是一款开源文档数据库，其服务器遵循自由软件基金会发布的 GNU AGPL v3.0 许可协议，具备高性能、高可用性和自动扩展能力。您可以从 [`www.mongodb.com/`](http://www.mongodb.com/) 下载 MongoDB。

在本节中，您将了解以下内容：

*   MongoDB 简介
*   如何下载、安装和配置 MongoDB
*   如何启动 MongoDB 服务器
*   如何连接 MongoDB 服务器
*   如何对 MongoDB 服务器执行基本操作
*   如何安装和使用 MongoDB 的 UI 工具 Robomongo

MongoDB 简介
MongoDB 是一种文档数据库，其记录是结构化的文档。由于文档是结构化的，因此它由字段和值对组成的数据结构。MongoDB 文档遵循类似于 JSON（JavaScript 对象表示法）对象的结构。作为字段的值，MongoDB 文档可以包含其他文档、数组和/或其他文档的数组。

MongoDB 文档

以下是一个 MongoDB 文档示例，以便您了解其结构：

```
{
name: "bob",
class: 2,
division: "B",
subjects: ["english", "science", "maths"]
}
```

由于 MongoDB 文档可以包含其他文档和其他文档的数组，因此其数据结构是可扩展的。此外，MongoDB 文档与编程语言中的数据类型是同义词。

安装和配置 MongoDB
在本节中，您将下载 MongoDB 社区版。您还将安装和配置它，然后测试安装。

下载 MongoDB 社区版
您可以在 [`www.mongodb.com/download-center`](http://www.mongodb.com/download-center) 下载 MongoDB。详细的安装说明也可以在 [`https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/`](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/) 找到。MongoDB 安装文件可以在 [`www.mongodb.org/downloads`](http://www.mongodb.org/downloads) 找到。

您必须拥有 Windows Server 2008 R2、Windows Vista 或更高版本才能安装 MongoDB 社区版。`.msi` 安装程序包含 MongoDB 所需的所有软件依赖项，并且如果您的机器上安装了旧版本的 MongoDB，它也会自动升级。参见图 A-1。

![../images/477630_1_En_20_Chapter/477630_1_En_20_Fig1_HTML.jpg](img/477630_1_En_20_Fig1_HTML.jpg)

图 A-1
MongoDB 社区服务器安装程序

安装和配置 MongoDB

下载 MongoDB 安装文件后，在 Windows 资源管理器中找到下载的 MongoDB `.msi` 文件，该文件通常位于默认的 `Downloads` 文件夹中。双击 `.msi` 文件后，将出现一组屏幕，引导您完成安装过程。尽管您可以选择任何位置，例如

```
C:\Program Files\MongoDB\Server\3.2\
```

但我明确避免在路径中包含空格的位置，例如 `Program Files` 中的空格。因此，在我的 Windows 机器上，我使用以下位置：

![../images/477630_1_En_20_Chapter/477630_1_En_20_Fig2_HTML.jpg](img/477630_1_En_20_Fig2_HTML.jpg)

图 A-2
MongoDB 安装文件夹

```
D:\Applns\MongoDB\Server\3.2.6
```

MongoDB 服务器需要一个数据目录来存储所有数据（图 A-2）。您可以从您选择的任何文件夹运行 MongoDB。MongoDB 的默认数据目录路径是启动 MongoDB 的驱动器上的绝对路径 `\data\db`。您可以通过在 Windows 命令提示符中运行以下命令来创建此文件夹：

```
md D:\Applns\MongoDB\Server\3.2.6\data
```

开始使用 MongoDB
在本节中，您将连接到已安装的 MongoDB 服务器并执行基本数据操作。

启动并连接 MongoDB

您现在可以使用以下命令启动 MongoDB：

```
D:\Applns\MongoDB\Server\3.2.6\bin\mongod.exe --dbpath D:\Applns\MongoDB\Server\3.2.6\data
```



清单 A-1 展示了如何启动 MongoDB 服务器。

```
D:\Applns\MongoDB\Server\3.2.6\bin>D:\Applns\MongoDB\Server\3.2.6\bin\mongod.exe --dbpath D:\Applns\MongoDB\Server\3.2.6\data
2019-03-02T19:37:34.958+0530 I CONTROL  [initandlisten] MongoDB starting : pid=7632 port=27017 dbpath=D:\Applns\MongoDB\Server\3.2.6\data 64-bit host=tiger
2019-03-02T19:37:34.959+0530 I CONTROL  [initandlisten] targetMinOS: Windows 7/Windows Server 2008 R2
2019-03-02T19:37:34.959+0530 I CONTROL  [initandlisten] db version v3.2.6
2019-03-02T19:37:34.959+0530 I CONTROL  [initandlisten] git version: 05552b562c7a0b3143a729aaa0838e558dc49b25
2019-03-02T19:37:34.960+0530 I CONTROL  [initandlisten] OpenSSL version: OpenSSL 1.0.1p-fips 9 Jul 2015
2019-03-02T19:37:34.960+0530 I CONTROL  [initandlisten] allocator: tcmalloc
2019-03-02T19:37:34.960+0530 I CONTROL  [initandlisten] modules: none
2019-03-02T19:37:34.960+0530 I CONTROL  [initandlisten] build environment:
2019-03-02T19:37:34.960+0530 I CONTROL  [initandlisten]     distmod: 2008plus-ssl
2019-03-02T19:37:34.960+0530 I CONTROL  [initandlisten]     distarch: x86_64
2019-03-02T19:37:34.960+0530 I CONTROL  [initandlisten]     target_arch: x86_64
2019-03-02T19:37:34.960+0530 I CONTROL  [initandlisten] options: { storage: { dbPath: "D:\Applns\MongoDB\Server\3.2.6\data" } }
2019-03-02T19:37:34.967+0530 I -        [initandlisten] Detected data files in D:\Applns\MongoDB\Server\3.2.6\data created by the 'wiredTiger' storage engine, so setting the active storage engine to 'wiredTiger'.
2019-03-02T19:37:34.968+0530 I STORAGE  [initandlisten] wiredtiger_open config:create,cache_size=8G,session_max=20000,eviction=(threads_max=4),config_base=false,statistics=(fast),log=(enabled=true,archive=true,path=journal,compressor=snappy),file_manager=(close_idle_time=100000),checkpoint=(wait=60,log_size=2GB),statistics_log=(wait=0),
2019-03-02T19:37:36.552+0530 I NETWORK  [HostnameCanonicalizationWorker] Starting hostname canonicalization worker
2019-03-02T19:37:36.552+0530 I FTDC     [initandlisten] Initializing full-time diagnostic data capture with directory 'D:/Applns/MongoDB/Server/3.2.6/data/diagnostic.data'
2019-03-02T19:37:36.583+0530 I NETWORK  [initandlisten] waiting for connectionson port 27017
清单 A-1
启动 MongoDB 服务器
```

如果你的路径包含空格，请将整个路径用双引号括起来，如下所示：

```
D:\Applns\MongoDB\Server\3.2.6\bin>D:\Applns\MongoDB\Server\3.2.6\bin\mongod.exe --dbpath "D:\Path with space\MongoDB\Server\3.2.6\data"
```

现在，你可以使用另一个 Windows 命令提示符并执行以下程序来连接到上述已启动的 MongoDB 服务器：

```
D:\Applns\MongoDB\Server\3.2.6\bin>D:\Applns\MongoDB\Server\3.2.6\bin\mongo
```

清单 A-2 展示了如何连接到 MongoDB。

```
D:\Applns\MongoDB\Server\3.2.6\bin>D:\Applns\MongoDB\Server\3.2.6\bin\mongo
MongoDB shell version: 3.2.6
connecting to: test
>
清单 A-2
连接到 MongoDB 服务器
```

使用 MongoDB 执行基本数据操作

在 mongo shell 连接到正在运行的 mongod 实例的 Windows 命令提示符中，你可以使用以下命令查询当前连接的数据库：

```
db.getName()
```

此命令将显示所有数据库：

```
showdbs
```

你可以使用以下命令切换到 test 数据库：

```
use test
```

然后，你可以使用以下命令列出当前数据库中所有可用的集合：

```
show collections
```

清单 A-3 展示了如何连接到 MongoDB 中的特定数据库。

```
D:\Applns\MongoDB\Server\3.2.6\bin>D:\Applns\MongoDB\Server\3.2.6\bin\mongo
MongoDB shell version: 3.2.6
connecting to: test
> db.getName()
test
> use test
switched to db test
> show collections
product
productCategory
>
清单 A-3
在 MongoDB 中选择一个数据库
```

你可以使用以下命令向 `students` 集合中插入一个新文档：

```
db.students.insert(
{
name: "bob",
class: 2,
division: "B",
subjects: ["english", "science", "maths"]
}
)
```

你可以使用以下命令查询刚刚从 `students` 集合中插入的文档（另请参见清单 A-4）：

```
> db.students.insert(
... {
... name: "bob",
... class: 2,
... division: "B",
... subjects: ["english", "science", "maths"]
... }
... )
WriteResult({ "nInserted" : 1 })
> db.students.find()
{ "_id" : ObjectId("5c7a9071f2a95d2c70e35476"), "name" : "bob", "class" : 2, "di
vision" : "B", "subjects" : [ "english", "science", "maths" ] }
>
清单 A-4
向 MongoDB 集合中插入文档并查询
```

```
db.students.find()
```

现在，你可以使用以下命令更新刚刚插入的、`name` 字段值为 `bob` 的 `students` 集合中的文档：

```
db.students.update(
{ "name" : "bob" },
{
$set: { "division": "C" }
}
)
```

你可以使用以下命令查询刚刚更新的文档：

```
db.students.find()
```

清单 A-5 展示了如何更新 MongoDB 集合中的文档。

```
> db.students.update(
... { "name" : "bob" },
... {
... $set: { "division": "C" }
... }
... )
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })
> db.students.find()
{ "_id" : ObjectId("5c7a9071f2a95d2c70e35476"), "name" : "bob", "class" : 2, "di
vision" : "C", "subjects" : [ "english", "science", "maths" ] }
>
清单 A-5
更新 MongoDB 集合中的文档
```

请注意，`division` 字段的值已从 `B` 更改为 `C`。

现在，你可以使用以下命令删除 `students` 集合中 `name` 字段值为 `bob` 的文档（另请参见清单 A-6）：

```
> db.students.remove( { "name": "bob" } )
WriteResult({ "nRemoved" : 1 })
> db.students.find()
>
清单 A-6
从 MongoDB 集合中删除文档
```

```
db.students.remove( { "name": "bob" } )
```

你可以使用以下命令从数据库中删除整个集合（另请参见清单 A-7）：

```
> show collections
product
productCategory
students
> db.students.drop()
true
> show collections
product
productCategory
>
清单 A-7
从 MongoDB 中删除集合
```

```
db.collection.drop()
```

`db.dropDatabase()` 将删除你当前连接的数据库。

Robomongo，一个 MongoDB 的图形用户界面
Robomongo^(⁴⁹) 是一个以 mongo shell 为核心的跨平台 MongoDB 管理工具。它将实际的 mongo shell 嵌入到选项卡式界面中。它既提供对 shell 命令行的访问，也提供图形用户界面。

下载并安装 Robomongo 社区版

Robomongo 社区版可在 [`https://robomongo.org/download`](https://robomongo.org/download) 下载。参见图 A-3。

![../images/477630_1_En_20_Chapter/477630_1_En_20_Fig3_HTML.jpg](img/477630_1_En_20_Fig3_HTML.jpg)

图 A-3
下载 Robomongo

建议你下载适用于 Windows 64 位的便携版本，并将压缩包解压到你方便的位置。在我的机器上，位置是

```
D:\Applns\Robomongo\robomongo-0.9.0-rc9
```

使用 Robomongo 启动并连接到 MongoDB

你可以执行此文件来启动图形用户界面：

```
D:\Applns\Robomongo\robomongo-0.9.0-rc9\Robomongo.exe
```

图形用户界面启动后，你可以通过选择图 A-4 中所示的“连接”按钮来连接到 MongoDB 服务器。

![../images/477630_1_En_20_Chapter/477630_1_En_20_Fig4_HTML.jpg](img/477630_1_En_20_Fig4_HTML.jpg)

图 A-4
使用 Robomongo 连接到 MongoDB

使用 MongoDB 执行基本数据操作

浏览器窗格允许你选择 MongoDB 中所需的集合，右键单击会提供许多选项来执行基本数据操作。参见图 A-5。



![../images/477630_1_En_20_Chapter/477630_1_En_20_Fig5_HTML.jpg](img/477630_1_En_20_Fig5_HTML.jpg)

**图 A-5** 使用 Robomongo 浏览 MongoDB 集合

**总结**
本附录引导您完成了 MongoDB 的安装和使用。您将在本书的示例中大量使用 MongoDB。当您需要对 MongoDB 执行操作时，请参考本附录。

**脚注**

**安装、配置和运行 RabbitMQ 集群**

RabbitMQ 是一个开源消息中间件，可以在所有主流操作系统上运行，并支持多种平台的开发工具。作为一个消息代理，它允许您发送和接收消息，并且您的消息可以安全地保存在一个地方，直到被消费者接收。RabbitMQ 可从 [`www.rabbitmq.com/`](http://www.rabbitmq.com/) 下载。

您将在本附录中探索以下内容：

*   如何下载和安装 Erlang（安装 RabbitMQ 的先决条件）
*   如何安装和启动 RabbitMQ 服务器
*   如何启用 RabbitMQ 管理插件并查看 RabbitMQ 管理控制台
*   如何设置包含两个节点的 RabbitMQ 集群
*   如何在 RabbitMQ 集群中关闭和启动单个节点

**RabbitMQ 简介**

消息代理的主要区别特征在于其能够解耦消息生产者和消息消费者。因此，生产者和消费者可以按照自己的节奏和便利性发布和消费消息。RabbitMQ 的一些特性包括：

*   **可靠性：** RabbitMQ 在性能与可靠性之间进行权衡。可靠性特性包括但不限于消息持久化、投递确认、发布者确认和高可用性。
*   **路由：** RabbitMQ 在消息到达队列之前，通过交换机进行路由。有几种内置的交换机类型可用于典型的路由逻辑。您还可以将交换机绑定在一起，甚至编写自己的交换机类型作为插件，以实现更复杂的路由。

**设置 RabbitMQ**
在本节中，您将下载并安装 Erlang，安装和配置 RabbitMQ，并测试安装。

**下载并安装 Erlang**
Erlang 是一种编程语言，通常用于构建大规模实时和高可用性系统。Erlang 的运行时内置了对并发、分布式和容错的支持。RabbitMQ 需要在机器上安装 Erlang，因此您将在本节中安装并设置 Erlang。
Erlang 可从 [`www.erlang.org/downloads`](http://www.erlang.org/downloads) 下载。

运行 Erlang 安装文件。尽管您可以选择任何位置，例如：

```
C:\Program Files\erlx.x.x\
```

我明确避免路径中包含空格的位置，例如 `Program Files` 中的空格。因此，在我的 Windows 机器上，我使用以下位置：

```
D:\Applns\Erlang\erl8.0-otp_win64_19.0
```

将 `ERLANG_HOME` 设置为您实际放置 Erlang 安装的位置。在我的机器上是 `D:\Applns\Erlang\erl8.0-otp_win64_19.0\erl8.0`。RabbitMQ 启动文件期望执行 `%ERLANG_HOME%\bin\erl.exe`：

```
set ERLANG_HOME=D:\Applns\Erlang\erl8.0-otp_win64_19.0\erl8.0
```

**安装并配置 RabbitMQ**
RabbitMQ 可从 [`www.rabbitmq.com/download.html`](http://www.rabbitmq.com/download.html) 下载。详细的安装说明也可在 [`www.rabbitmq.com/install-windows-manual.html`](http://www.rabbitmq.com/install-windows-manual.html) 找到。
下载名为 `rabbitmq-server-windows-3.6.9.zip` 的 RabbitMQ 安装文件。
从 zip 文件中，将名为 `rabbitmq_server-3.6.9` 的文件夹解压到 `C:\Program Files\RabbitMQ`（或适合应用程序文件的其他位置）。

我明确避免路径中包含空格的位置，例如 `Program Files` 中的空格。因此，在我的 Windows 机器上，我使用以下位置：

```
D:\Applns\RabbitMQ\rabbitmq_server-3.6.9
```

默认情况下，RabbitMQ 日志和 Mnesia 数据库存储在用户的应用程序数据目录中，在我的机器上是 `C:\Users\binil\AppData\Roaming\RabbitMQ`。
如果您必须将 RabbitMQ 作为手动安装的 Windows 服务运行，您还需要同步 Erlang cookie；换句话说，服务帐户和运行 `rabbitmqctl.bat` 的用户所使用的 Erlang 安全 cookie 必须同步，`rabbitmqctl.bat` 才能正常工作。这可以通过确保 Erlang cookie 文件包含相同的字符串来实现。为此，请复制 `.erlang.cookie` 文件（在我的机器上位于 Windows 目录 `C:\WINDOWS\.erlang.cookie`），并替换用户的 `.erlang.cookie`，该文件位于用户的主目录中（在我的机器上是 `C:\Users\binil\.erlang.cookie`）。

**开始使用 RabbitMQ**
在本节中，您将启动并连接到已安装的 RabbitMQ 服务器，并执行基本的数据操作。

**启用管理插件**

Rabbitmq-management 插件公开了一个基于 HTTP 的 API，用于管理和监控 RabbitMQ 服务器。它还包含一个基于浏览器的 UI 工具，可以执行基本的管理操作。您需要首先启用 RabbitMQ 发行版中包含的管理插件：

```
D:\Applns\RabbitMQ\rabbitmq_server-3.6.3\sbin>D:\Applns\RabbitMQ\rabbitmq_server-3.6.3\sbin\rabbitmq-plugins enable rabbitmq_management
```

启动 RabbitMQ（在下一节中说明）后，您可以在默认 URL [`http://server-name:15672/`](https://www.server-name:15672/) 查看 Web UI。

**启动 RabbitMQ 服务器**

您现在可以使用以下命令启动 RabbitMQ 服务器：

```
D:\Applns\RabbitMQ\rabbitmq_server-3.6.3\sbin>D:\Applns\RabbitMQ\rabbitmq_server-3.6.3\sbin\rabbitmq-server.bat
```

清单 B-1 显示了结果。

```
D:\Applns\RabbitMQ\rabbitmq_server-3.6.3\sbin>D:\Applns\RabbitMQ\rabbitmq_server-3.6.3\sbin\rabbitmq-server.bat
RabbitMQ 3.6.3\. Copyright (C) 2007-2016 Pivotal Software, Inc.
##  ##      Licensed under the MPL.  See http://www.rabbitmq.com/
##  ##
##########  Logs: C:/Users/binil/AppData/Roaming/RabbitMQ/log/RABBIT~1.LOG
######  ##        C:/Users/binil/AppData/Roaming/RabbitMQ/log/RABBIT~2.LOG
##########
Starting broker...
completed with 6 plugins.
清单 B-1 启动 RabbitMQ 服务器
```

**查看 RabbitMQ 管理控制台**

您现在可以使用您喜欢的 Web 浏览器连接到上面启动的 RabbitMQ 服务器，并查看 RabbitMQ 管理控制台（参见图 B-1）：

![../images/477630_1_En_21_Chapter/477630_1_En_21_Fig1_HTML.jpg](img/477630_1_En_21_Fig1_HTML.jpg)

**图 B-1.** RabbitMQ 管理控制台登录

```
http://127.0.0.1:15672
```

您可以使用默认凭据登录管理控制台：

```
用户名: guest
密码: guest
```

参见图 B-2。

![../images/477630_1_En_21_Chapter/477630_1_En_21_Fig2_HTML.jpg](img/477630_1_En_21_Fig2_HTML.jpg)

**图 B-2.** RabbitMQ 管理控制台

**设置 RabbitMQ 集群**
RabbitMQ 集群是一个或多个 Erlang 节点的分组，每个节点运行 RabbitMQ 应用程序实例，并共享用户、虚拟主机、队列、交换机、绑定和其他运行时参数。

**配置 RabbitMQ 集群**

这里说明如何快速设置一个双节点 RabbitMQ 集群。您可能希望在 RabbitMQ 安装的 `sbin` 文件夹中创建两个脚本，如下所示：

```
D:\Applns\RabbitMQ\rabbitmq_server-3.6.3\sbin\rabbitmq-server1.bat
D:\Applns\RabbitMQ\rabbitmq_server-3.6.3\sbin\rabbitmq-server2.bat
```

每个脚本的内容如下：

```
D:\Applns\RabbitMQ\rabbitmq_server-3.6.3\sbin\rabbitmq-server1.bat
set RABBITMQ_NODE_PORT=5672
set RABBITMQ_NODENAME=rabbit1
set RABBITMQ_SERVICE_NAME=rabbit1
set RABBITMQ_SERVER_START_ARGS=-rabbitmq_management listener [{port,15672}]
REM rabbitmq-server -detached
rabbitmq-server
```



上述脚本将启动一个节点名为 rabbit1 的 RabbitMQ 实例。以下脚本将启动一个节点名为 rabbit2 的 RabbitMQ 实例：

```
D:\Applns\RabbitMQ\rabbitmq_server-3.6.3\sbin\rabbitmq-server2.bat
set RABBITMQ_NODE_PORT=5673
set RABBITMQ_NODENAME=rabbit2
set RABBITMQ_SERVICE_NAME=rabbit2
set RABBITMQ_SERVER_START_ARGS=-rabbitmq_management listener [{port,15673}]
REM call rabbitmq-server -detached
rabbitmq-server
```

现在，你需要停止 rabbit2，让它加入另一个正在运行的节点 rabbit1 所在的集群，然后重新启动 rabbit2。以下脚本将精确完成此操作：

```
D:\Applns\RabbitMQ\rabbitmq_server-3.6.3\sbin\rabbitmq-cluster2.bat
call rabbitmqctl -n rabbit2 stop_app
call rabbitmqctl -n rabbit2 join_cluster rabbit1@tiger
call rabbitmqctl -n rabbit2 start_app
call rabbitmqctl -n rabbit1 set_policy ha-all "^.∗" "{""ha-mode"":""all""}"
```

注意
你可能需要将上述脚本中的主机名 `tiger` 替换为你自己的主机名。

启动 RabbitMQ 集群

假设你只创建了上一节中提到的必要脚本，并且尚未启动任何服务器，那么你现在可以通过在不同的命令窗口中按相同顺序执行以下命令来启动 RabbitMQ 集群：

```
cd D:\Applns\RabbitMQ\rabbitmq_server-3.6.3\sbin
D:\Applns\RabbitMQ\rabbitmq_server-3.6.3\sbin>rabbitmq-server1
```

清单 B-2 显示了结果。

```
D:\Applns\RabbitMQ\rabbitmq_server-3.6.3\sbin>rabbitmq-server1
D:\Applns\RabbitMQ\rabbitmq_server-3.6.3\sbin>set RABBITMQ_NODE_PORT=5672
D:\Applns\RabbitMQ\rabbitmq_server-3.6.3\sbin>set RABBITMQ_NODENAME=rabbit1
D:\Applns\RabbitMQ\rabbitmq_server-3.6.3\sbin>set RABBITMQ_SERVICE_NAME=rabbit1
D:\Applns\RabbitMQ\rabbitmq_server-3.6.3\sbin>set RABBITMQ_SERVER_START_ARGS=-rabbitmq_management listener [{port,15672}]
D:\Applns\RabbitMQ\rabbitmq_server-3.6.3\sbin>REM rabbitmq-server -detached
D:\Applns\RabbitMQ\rabbitmq_server-3.6.3\sbin>rabbitmq-server
RabbitMQ 3.6.3\. Copyright (C) 2007-2016 Pivotal Software, Inc.
##  ##      Licensed under the MPL.  See http://www.rabbitmq.com/
##  ##
##########  Logs: C:/Users/binil/AppData/Roaming/RabbitMQ/log/rabbit1.log
######  ##        C:/Users/binil/AppData/Roaming/RabbitMQ/log/RABBIT~3.LOG
##########
Starting broker...
completed with 6 plugins.
清单 B-2
启动 RabbitMQ 集群的节点 1
```

可以通过将浏览器指向 [`http://127.0.0.1:15672/`](http://127.0.0.1:15672/) 来查看名为 rabbit1 的实例的管理控制台。参见图 B-3。

![../images/477630_1_En_21_Chapter/477630_1_En_21_Fig3_HTML.jpg](img/477630_1_En_21_Fig3_HTML.jpg)

图 B-3.
RabbitMQ 节点 1 的管理控制台

在另一个命令窗口中，执行以下命令：

```
cd D:\Applns\RabbitMQ\rabbitmq_server-3.6.3\sbin
D:\Applns\RabbitMQ\rabbitmq_server-3.6.3\sbin>rabbitmq-server2
```

清单 B-3 显示了结果。

```
D:\Applns\RabbitMQ\rabbitmq_server-3.6.3\sbin>rabbitmq-server2
D:\Applns\RabbitMQ\rabbitmq_server-3.6.3\sbin>set RABBITMQ_NODE_PORT=5673
D:\Applns\RabbitMQ\rabbitmq_server-3.6.3\sbin>set RABBITMQ_NODENAME=rabbit2
D:\Applns\RabbitMQ\rabbitmq_server-3.6.3\sbin>set RABBITMQ_SERVICE_NAME=rabbit2
D:\Applns\RabbitMQ\rabbitmq_server-3.6.3\sbin>set RABBITMQ_SERVER_START_ARGS=-rabbitmq_management listener [{port,15673}]
D:\Applns\RabbitMQ\rabbitmq_server-3.6.3\sbin>REM call rabbitmq-server -detached
D:\Applns\RabbitMQ\rabbitmq_server-3.6.3\sbin>rabbitmq-server
RabbitMQ 3.6.3\. Copyright (C) 2007-2016 Pivotal Software, Inc.
##  ##      Licensed under the MPL.  See http://www.rabbitmq.com/
##  ##
##########  Logs: C:/Users/binil/AppData/Roaming/RabbitMQ/log/RABBIT~4.LOG
######  ##        C:/Users/binil/AppData/Roaming/RabbitMQ/log/RA7871~1.LOG
##########
Starting broker...
completed with 6 plugins.
清单 B-3
启动 RabbitMQ 集群的节点 2
```

可以通过将浏览器指向 [`http://127.0.0.1:15673/`](http://127.0.0.1:15673/) 来查看名为 rabbit2 的实例的管理控制台。参见图 B-4。

![../images/477630_1_En_21_Chapter/477630_1_En_21_Fig4_HTML.jpg](img/477630_1_En_21_Fig4_HTML.jpg)

图 B-4.
RabbitMQ 节点 2 的管理控制台

请注意，这些实例所需的数据创建在我机器上的路径 `C:\Users\binil\AppData\Roaming\RabbitMQ\db` 中。参见图 B-5。

![../images/477630_1_En_21_Chapter/477630_1_En_21_Fig5_HTML.jpg](img/477630_1_En_21_Fig5_HTML.jpg)

图 B-5.
RabbitMQ 实例数据文件夹

现在，你需要让 rabbit2 以集群模式加入 rabbit1。为此，请在第三个命令窗口中执行以下脚本：

```
cd D:\Applns\RabbitMQ\rabbitmq_server-3.6.3\sbin
D:\Applns\RabbitMQ\rabbitmq_server-3.6.3\sbin>rabbitmq-cluster2
```

清单 B-4 显示了结果。

```
D:\Applns\RabbitMQ\rabbitmq_server-3.6.3\sbin>rabbitmq-cluster2
D:\Applns\RabbitMQ\rabbitmq_server-3.6.3\sbin>call rabbitmqctl -n rabbit2 stop_app
Stopping node rabbit2@tiger ...
Clustering node rabbit2@tiger with rabbit1@tiger ...
Starting node rabbit2@tiger ...
Setting policy "ha-all" for pattern "^^.∗" to "{\"ha-mode\":\"all\"}" with priority "0" ...
D:\Applns\RabbitMQ\rabbitmq_server-3.6.3\sbin>
清单 B-4
创建一个双节点 RabbitMQ 集群
```

一旦 `rabbitmq-cluster2` 命令完全执行完毕，你可以在第二个窗口中看到 rabbit2 节点重新启动并加入集群；参见清单 B-5。

```
D:\Applns\RabbitMQ\rabbitmq_server-3.6.3\sbin>set RABBITMQ_SERVICE_NAME=rabbit2
D:\Applns\RabbitMQ\rabbitmq_server-3.6.3\sbin>set RABBITMQ_SERVER_START_ARGS=-rabbitmq_management listener [{port,15673}]
D:\Applns\RabbitMQ\rabbitmq_server-3.6.3\sbin>REM call rabbitmq-server -detached
D:\Applns\RabbitMQ\rabbitmq_server-3.6.3\sbin>rabbitmq-server
RabbitMQ 3.6.3\. Copyright (C) 2007-2016 Pivotal Software, Inc.
##  ##      Licensed under the MPL.  See http://www.rabbitmq.com/
##  ##
##########  Logs: C:/Users/binil/AppData/Roaming/RabbitMQ/log/RABBIT~4.LOG
######  ##        C:/Users/binil/AppData/Roaming/RabbitMQ/log/RA7871~1.LOG
##########
Starting broker...
completed with 6 plugins.
RabbitMQ 3.6.3\. Copyright (C) 2007-2016 Pivotal Software, Inc.
##  ##      Licensed under the MPL.  See http://www.rabbitmq.com/
##  ##
##########  Logs: C:/Users/binil/AppData/Roaming/RabbitMQ/log/RABBIT~4.LOG
######  ##        C:/Users/binil/AppData/Roaming/RabbitMQ/log/RA7871~1.LOG
##########
Starting broker...
completed with 6 plugins.
清单 B-5
RabbitMQ 节点加入集群
```

一旦看到上述输出，集群就已就绪，你可以打开两个节点的管理控制台，验证它们是否已作为集群的活动参与者加入。

可以通过将浏览器指向 [`http://127.0.0.1:15672/`](http://127.0.0.1:15672/) 来查看名为 rabbit1 的实例的管理控制台。参见图 B-6。

![../images/477630_1_En_21_Chapter/477630_1_En_21_Fig6_HTML.jpg](img/477630_1_En_21_Fig6_HTML.jpg)

图 B-6.
RabbitMQ 集群节点 1 的管理控制台

你也可以通过将浏览器指向 [`http://127.0.0.1:15673/`](http://127.0.0.1:15673/) 来打开名为 rabbit2 的实例的管理控制台。参见图 B-7。

![../images/477630_1_En_21_Chapter/477630_1_En_21_Fig7_HTML.jpg](img/477630_1_En_21_Fig7_HTML.jpg)

图 B-7.
RabbitMQ 集群节点 2 的管理控制台

这意味着你的 RabbitMQ 集群已启动并正在运行。



重启 RabbitMQ 集群
当整个集群宕机时，最后一个关闭的节点必须成为第一个启动的节点。如果未能如此操作，节点将等待 30 秒，等待最后一个磁盘节点重新上线，之后便会失败。你可以参考 RabbitMQ 在线文档以获取更多详细信息。

要重启集群中的某个节点（rabbit2），你可以使用之前见过的名为 `rabbitmq-cluster2.bat` 的脚本。要重启 rabbit1，请使用 `rabbitmq-cluster1.bat`：

```
D:\Applns\RabbitMQ\rabbitmq_server-3.6.3\sbin\rabbitmq-cluster1.bat
call rabbitmqctl -n rabbit1 stop_app
call rabbitmqctl -n rabbit1 join_cluster rabbit2@tiger
call rabbitmqctl -n rabbit1 start_app
call rabbitmqctl -n rabbit2 set_policy ha-all "^.∗" "{""ha-mode"":""all""}"
```

如果在启动和重启集群时搞乱了配置，你可能需要前往 RabbitMQ 的数据文件夹，删除所有内容，然后按照正确的顺序重新尝试启动和重启实例及集群。

从客户端连接到 RabbitMQ 集群

客户端可以连接到集群内的任意单个节点。如果该节点发生故障而集群其余部分幸免于难，那么客户端应注意到连接已关闭，并应显式地重新连接到集群中某个幸存的成员。我不建议将主机名或 IP 地址硬编码到客户端应用程序中，因为这是一种不灵活的方法。相反，推荐的方法是采用以下任何一种抽象方式：

*   使用一个 TTL 配置非常短的动态 DNS 服务。

*   使用一个普通的 TCP 负载均衡器。

*   使用某种通过 pacemaker 或类似技术实现的移动 IP。

请参考附录 C，其中我解释了如何使用 Nginx 设置 TCP 负载均衡器。如果你在 RabbitMQ 集群和客户端程序之间使用了这样的 TCP 负载均衡器，那么当集群中某个节点发生故障而集群其余部分幸免于难时，客户端无需注意到连接已关闭，并且会被隐式地路由到集群中任何幸存的成员。

总结
RabbitMQ 是一个可靠且持久的消息队列服务器，可以在集群中配置为具有多个节点的高可用性模式。我在本书的许多代码演示中都使用了 RabbitMQ。在某些代码演示中，我仅将 RabbitMQ 用作单节点服务器；然而，在所有这些场景中，RabbitMQ 都可以配置在集群中，以测试示例。

安装、配置和运行 Nginx 反向代理

Nginx 是一个 HTTP 和反向代理服务器，它也可以充当邮件代理服务器或通用的 TCP/UDP 代理服务器。基本的 HTTP 服务器功能包括提供静态文件和索引文件。Nginx 还支持 keep-alive 和流水线连接。TCP/UDP 代理服务器功能包括 TCP 和 UDP 的通用代理，以及负载均衡和容错。请访问 [`https://nginx.org`](https://nginx.org) 获取更多信息。

我在本附录中涵盖以下内容：

*   如何安装并开始使用 Nginx

*   如何将 Nginx 设置为 HTTP 反向代理

*   如何将 Nginx 设置为 TCP 反向代理

安装 Nginx
在本节中，你将下载、安装和配置 Nginx，并测试安装。Nginx 可在 [`https://nginx.org/en/download.html`](https://nginx.org/en/download.html) 下载。

先将安装归档文件保存到硬盘上的合适位置，然后将归档文件解压到你选择的位置。尽管你可以选择任何位置，例如

```
C:\Program Files\nginx\
```

但我明确避免使用路径中包含空格的位置，例如 `Program Files` 中的空格。因此，在我的 Windows 机器上，我使用以下位置：

```
D:\Applns\nginx\nginx-1.10.1
```

开始使用 Nginx

你现在可以使用以下命令启动 Nginx 服务器，如图 C-1 所示：

![../images/477630_1_En_22_Chapter/477630_1_En_22_Fig1_HTML.jpg](img/477630_1_En_22_Fig1_HTML.jpg)

图 C-1. 启动 Nginx 服务器

```
D:\Applns\nginx\nginx-1.10.1>nginx
```

一旦 Nginx 启动，可以通过使用 `-s` 参数调用可执行文件来控制它，例如

```
nginx -s flag
```

其中 `flag` 可以是以下之一：

*   Stop：强制关闭

*   Quit：优雅关闭

*   Reload：重新加载配置文件

*   Reopen：重新打开日志文件

开始将 Nginx 用作反向代理
Nginx 经常被用来设置为反向代理服务器。当设置为反向代理服务器时，Nginx 接收请求，将其传递给被代理的服务器，从被代理的服务器检索响应，并将其发送回客户端。
Nginx 中的配置在位于 `conf` 路径下的 `nginx.conf` 文件中生效。

配置 HTTP 反向代理

可以在 Nginx 中设置 HTTP 反向代理，以代理使用 HTTP 协议的请求。用于设置 HTTP 代理的 `nginx.conf` 中的相关部分如清单 C-1 所示。

```
http {
upstream myapp1 {
server localhost:8081;
server localhost:8082;
}
server {
listen       8080;
server_name  localhost;
location / {
proxy_pass http://myapp1;
}
}
清单 C-1
Nginx HTTP 反向代理配置
```

现在，类似于 URL 模式 `http://localhost:8080` 的 HTTP 请求将以负载均衡的方式被代理到以下两个 URL：

```
http://localhost:8081
http://localhost:80802
```

配置 TCP 反向代理

可以在 Nginx 中设置 TCP 反向代理，以代理使用 TCP 协议的请求。用于设置 TCP 代理的 `nginx.conf` 中的相关部分如清单 C-2 所示。

```
stream {
upstream myapp1 {
server localhost:5672;
server localhost:5673;
}
server {
listen     5671;
proxy_pass myapp1;
}
}
清单 C-2
Nginx TCP 反向代理配置
```

总结
Nginx 的反向代理设置在将外部请求负载均衡到微服务时非常有用。因此，我在本书中使用 Nginx 来演示许多场景。

cURL 和 Postman

cURL 是一个使用 URL 语法获取或发送文件的命令行工具。cURL 支持一系列常见的互联网协议，如 HTTP、HTTPS、FTP、FTPS、SCP、SFTP、TFTP 和 LDAP，其中 HTTP 是我们在本书中讨论的重点。
Postman 是一个基于 UI 的应用程序，可以更轻松地执行所有 HTTP 操作。我也会介绍这个应用程序。
第 7 章中的“使用 Spring Boot 和 MongoDB 执行数据操作”一节介绍了一个示例 Spring Boot 应用程序。你在那里使用了 Spring Data REST 来自动创建 MongoDB 仓库接口的实现。然后 Spring Boot 指示 Spring MVC 在 `/products` 和 `/categories` 处创建 RESTful 端点。我将假设此示例已构建并正在运行，以便你可以使用此示例执行 cURL 和 Postman 的基本操作。

我将在本附录中涵盖以下内容：

*   如何将 cURL 用于 HTTP 操作

*   如何将 Postman 用于 HTTP 操作

用于 HTTP 的 cURL 操作

基本的 cURL 操作涉及在命令行输入 `curl`，后跟要检索的输出的 URL。例如，要检索 `microservices.io` 主页，请输入

```
curl www.microservices.io
```

cURL 默认将其从 URL 检索到的输出显示到系统指定的标准输出，通常是终端窗口（在 Mac 上）。因此，在大多数系统上，运行上述命令将在终端窗口中显示 [`www.microservices.io`](http://www.microservices.io) 的源代码网页。

HTTP GET 检索实体

你可以使用 cURL 通过发出清单 D-1 所示的命令来初步了解服务器 API 端点提供的内容。


```markdown
```
binils-MacBook-Pro:~ mike$ curl http://localhost:8080
{
"_links": {
"categories": {
"href": "http://localhost:8080/categories{?page,size,sort}",
"templated": true
},
"products": {
"href": "http://localhost:8080/products{?page,size,sort}",
"templated": true
},
"profile": {
"href": "http://localhost:8080/profile"
}
}
}
清单 D-1
使用 cURL 进行 HTTP GET 请求
```

响应表明存在一个位于 `http://localhost:8080/products` 的 products 链接。
它包含一些选项，例如 `?page`、`?size` 和 `?sort`。你可以检查
其他 URL，例如

```
binils-MacBook-Pro:~ mike$ curl http://localhost:8080/ profile
binils-MacBook-Pro:~ mike$ curl http://localhost:8080/products/search
```

如果后端数据库中有相应的数据可用，你
还可以执行类似的自定义查询

```
binils-MacBook-Pro:~ mike$ curl http://localhost:8080/products/search/findByProductCategoryName?productCategory=Mobile
```

HTTP POST 创建实体

现在你可以创建一些产品实例，如清单 D-2 所示。

```
binils-MacBook-Pro:~ mike$ curl -i -X POST -H "Content-Type:application/json" -d '{"name":"Giomi", "code":"GIOME-KL", "title":"Giome 10 inch gold", "imgUrl":"giome.jpg", "price":11000.0, "productCategoryName":"Mobile"}' http://localhost:8080/products
HTTP/1.1 201
Location: http://localhost:8080/products/595ce0f073ed92061ca85665
Content-Type: application/hal+json;charset=UTF-8
Transfer-Encoding: chunked
Date: Wed, 05 Jul 2017 12:52:00 GMT
{
"name" : "Giomi",
"code" : "GIOME-KL",
"title" : "Giome 10 inch gold",
"description" : null,
"imgUrl" : "giome.jpg",
"price" : 11000.0,
"productCategoryName" : "Mobile",
"_links" : {
"self" : {
"href" : "http://localhost:8080/products/595ce0f073ed92061ca85665"
},
"product" : {
"href" : "http://localhost:8080/products/595ce0f073ed92061ca85665"
}
}
}
清单 D-2
使用 cURL 进行 HTTP POST 请求
```

这里

*   `-i`
    指示你需要查看包含响应头的响应消息。新创建的人的 URI 也会显示出来。

*   `-X POST`
    表示这是一个用于创建新条目的 POST 请求。

*   `-H "Content-Type:application/json"` 指明了内容类型，因此
    应用程序期望请求体包含一个 JSON 对象。

*   `-d '{"name":"Giomi", "code":"GIOME-KL", "title":"Giome 10 inch gold", "imgUrl":"giome.jpg", "price":11000.0, "productCategoryName":"Mobile"}'` 是正在发送的数据，此处为 JSON
    格式。

如果你在跨平台（从 Windows 到 Linux 或 Mac 等）复制这些命令时遇到问题，你可能需要直接在终端窗口中仔细手动输入它们。另请注意，实体 ID `595c937473ed9208488808b5` 是由系统自动创建并返回的。使用这个相同的 ID，你现在可以使用 `curl` 命令来检索上述新创建的实体，如清单 D-3 所示。

```
binils-MacBook-Pro:~ mike$ curl http://localhost:8080/products/595ce0f073ed92061ca85665
{
"name" : "Giomi",
"code" : "GIOME-KL",
"title" : "Giome 10 inch gold",
"description" : null,
"imgUrl" : "giome.jpg",
"price" : 11000.0,
"productCategoryName" : "Mobile",
"_links" : {
"self" : {
"href" : "http://localhost:8080/products/595ce0f073ed92061ca85665"
},
"product" : {
"href" : "http://localhost:8080/products/595ce0f073ed92061ca85665"
}
}
}
清单 D-3
使用 cURL 查看 HTTP POST 的效果
```

HTTP PUT 替换实体
如果你想替换整个组，请使用 HTTP PUT。服务器可能会选择创建一个新组并丢弃旧组，因此 ID 可能保持不变。但客户端应根据服务器的响应假设他得到了一个全新的项目。

对于 PUT 请求，客户端应始终发送整个资源，包含创建新项目所需的所有数据，如清单 D-4 所示。

```
binils-MacBook-Pro:~ mike$ curl -i -X PUT -H "Content-Type:application/json" -d '{"name":"Giomi-New", "code":"GIOME-KL-NEW", "title":"Giome New 10 inch gold", "imgUrl":"giomenew.jpg", "price":15000.0, "productCategoryName":"Mobile"}' http://localhost:8080/products/595ce0f073ed92061ca85665
HTTP/1.1 200
Location: http://localhost:8080/products/595ce0f073ed92061ca85665
Content-Type: application/hal+json;charset=UTF-8
Transfer-Encoding: chunked
Date: Wed, 05 Jul 2017 12:53:27 GMT
{
"name" : "Giomi-New",
"code" : "GIOME-KL-NEW",
"title" : "Giome New 10 inch gold",
"description" : null,
"imgUrl" : "giomenew.jpg",
"price" : 15000.0,
"productCategoryName" : "Mobile",
"_links" : {
"self" : {
"href" : "http://localhost:8080/products/595ce0f073ed92061ca85665"
},
"product" : {
"href" : "http://localhost:8080/products/595ce0f073ed92061ca85665"
}
}
}
清单 D-4
使用 cURL 进行 HTTP PUT 请求
```

你可以通过清单 D-5 中的代码查询并查看你刚才所做的更改。

```
binils-MacBook-Pro:~ mike$ curl http://localhost:8080/products
{
"_embedded" : {
"products" : [ {
"name" : "Giomi-New",
"code" : "GIOME-KL-NEW",
"title" : "Giome New 10 inch gold",
"description" : null,
"imgUrl" : "giomenew.jpg",
"price" : 15000.0,
"productCategoryName" : "Mobile",
"_links" : {
"self" : {
"href" : "http://localhost:8080/products/595ce0f073ed92061ca85665"
},
"product" : {
"href" : "http://localhost:8080/products/595ce0f073ed92061ca85665"
}
}
} ]
},
"_links" : {
"self" : {
"href" : "http://localhost:8080/products{?page,size,sort}",
"templated" : true
},
"profile" : {
"href" : "http://localhost:8080/profile/products"
},
"search" : {
"href" : "http://localhost:8080/products/search"
}
},
"page" : {
"size" : 20,
"totalElements" : 1,
"totalPages" : 1,
"number" : 0
}
}
清单 D-5
使用 cURL 查看 HTTP PUT 的效果
```

HTTP PATCH 修改实体

如果你想更新一个属性，比如 `status`（一个可以设置的组属性），请使用 PATCH。像 `status` 这样的属性通常适合限制在白名单值中，但在清单 D-6 中，你对 `price` 属性使用了 PATCH。

```
binils-MacBook-Pro:~ mike$ curl -i -X PATCH -H "Content-Type:application/json" -d '{"price":15000.50}' http://localhost:8080/products/595ce0f073ed92061ca85665
HTTP/1.1 200
Content-Type: application/hal+json;charset=UTF-8
Transfer-Encoding: chunked
Date: Wed, 05 Jul 2017 12:54:49 GMT
{
"name" : "Giomi-New",
"code" : "GIOME-KL-NEW",
"title" : "Giome New 10 inch gold",
"description" : null,
"imgUrl" : "giomenew.jpg",
"price" : 15000.5,
"productCategoryName" : "Mobile",
"_links" : {
"self" : {
"href" : "http://localhost:8080/products/595ce0f073ed92061ca85665"
},
"product" : {
"href" : "http://localhost:8080/products/595ce0f073ed92061ca85665"
}
}
}
清单 D-6
使用 cURL 进行 HTTP PATCH 请求
```

HTTP DELETE 删除实体

你可以使用 DELETE 命令删除同一个实体，如清单 D-7 所示。

```
binils-MacBook-Pro:~ mike$ curl -i -X DELETE http://localhost:8080/products/595ce0f073ed92061ca85665
HTTP/1.1 204
Date: Wed, 05 Jul 2017 12:55:40 GMT
清单 D-7
使用 cURL 进行 HTTP DELETE 请求
```

另一次查询将确认该实体确实已被删除！请参阅
清单 D-8。

```
binils-MacBook-Pro:~ mike$ curl http://localhost:8080/products
{
"_embedded" : {
"products" : [ ]
},
"_links" : {
"self" : {
"href" : "http://localhost:8080/products{?page,size,sort}",
"templated" : true
},
"profile" : {
"href" : "http://localhost:8080/profile/products"
},
"search" : {
"href" : "http://localhost:8080/products/search"
}
},
"page" : {
"size" : 20,
"totalElements" : 0,
"totalPages" : 0,
"number" : 0
}
}
清单 D-8
使用 cURL 查看 HTTP PUT 的效果
```
```


用于 HTTP 操作的 Postman
Postman 是一款用于与 HTTP API 交互的 Google Chrome 应用。它提供了一个友好的图形用户界面，用于构建请求和读取响应。该应用可从 [`www.getpostman.com/`](http://www.getpostman.com/) 获取。截至撰写本文时，Postman 应用支持 Mac、Windows 和 Linux 平台。

使用 Postman 执行 HTTP GET 以检索实体
安装应用后，您将需要测试所有针对它的 HTTP 操作。这里我再次参考第 7 章，其中我介绍了一个示例 Spring Boot 应用程序。我还参考了图 [7-4](https://doi.org/10.1007/978-1-4842-4501-9_7Fig#4)，该图展示了使用 Postman 对链接 `http://localhost:8080/profile/products` 执行 GET 操作的过程。

总结
cURL 和 Postman 都是用于快速与 REST 端点交互并执行基本 HTTP 操作的优秀工具。在运行本书中的示例时，您将大量使用它们。

Apache TCPMon

Apache TCPMon 是一个实用工具，允许查看和重新发送流量，尤其是 HTTP 流量消息。它作为一个调试工具非常有用，您可以借此查看客户端和服务器之间，甚至两个微服务之间通过网络传输的内容。它最初是 Axis1 的一部分，现在已独立成为一个项目。请访问 [`https://ws.apache.org/tcpmon/`](https://ws.apache.org/tcpmon/) 获取更多信息。

我在本附录中涵盖以下内容：

*   如何安装和启动 TCPMon

*   如何将 TCPMon 设置为代理

安装 TCPMon
在本节中，您将下载、安装和配置 TCPMon，并测试安装。TCPMon 可在 [`https://ws.apache.org/tcpmon/download.cgi`](https://ws.apache.org/tcpmon/download.cgi) 下载。

首先将安装存档文件保存到硬盘上的合适位置，然后将存档解压到您选择的位置。尽管您可以选择任何位置，例如

```
C:\Program Files\tcpmon\
```

但我明确避免路径中包含空格的位置，例如 `Program Files` 中的空格。因此，在我的 Windows 机器上，我使用以下位置：

```
D:\Applns\apache\TCPMon\tcpmon-1.0-bin
```

开始使用 TCPMon

您可以通过进入 TCPMon 解压文件夹的 `build` 文件夹来启动 TCPMon（如图 E-1 所示）：

![../images/477630_1_En_24_Chapter/477630_1_En_24_Fig1_HTML.jpg](img/477630_1_En_24_Fig1_HTML.jpg)

图 E-1
启动 TCPMon

```
cd D:\Applns\apache\TCPMon\tcpmon-1.0-bin\build
D:\Applns\apache\TCPMon\tcpmon-1.0-bin\build>tcpmon
```

将 TCPMon 设置为代理
TCPMon 可以充当代理。要将 TCPMon 设置为代理模式，只需从单选按钮中选择代理选项，并配置调用要代理到的目标主机和端口。

或者，您可以在启动 TCPMon 时提供配置参数。使用模式如下：

```
D:\Applns\apache\TCPMon\tcpmon-1.0-bin\build>tcpmon [listenPort targetHost targetPort]
```

如果您像下面这样启动，您将看到如图 E-2 所示的屏幕：

![../images/477630_1_En_24_Chapter/477630_1_En_24_Fig2_HTML.jpg](img/477630_1_En_24_Fig2_HTML.jpg)

图 E-2.
配置为代理的 TCPMon

```
D:\Applns\apache\TCPMon\tcpmon-1.0-bin\build>tcpmon 8081 127.0.0.1 8080
D:\Applns\apache\TCPMon\tcpmon-1.0-bin\build>java -cp ./tcpmon-1.0.jar org.apache.ws.commons.tcpmon.TCPMon 8081 127.0.0.1 8080
```

在上述情况下，所有到达运行 TCPMon 的主机上 8081 端口的请求都将被代理到主机 127.0.0.1 的 8080 端口。

总结
TCPMon 是一个用于检查 HTTP 请求和响应的优秀工具。您可以将其安装在您的机器上并保持就绪状态，以便轻松地通过 TCPMon 代理 HTTP 请求和响应，并检查消息内容。

ActiveMQ

Apache ActiveMQ 是一个开源的消息传递和集成模式服务器。ActiveMQ 速度很快，支持多种跨语言客户端和协议，并附带易于使用的企业集成模式和许多高级功能。ActiveMQ 完全支持 JMS 1.1 和 J2EE 1.4，并根据 Apache 2.0 许可证发布。ActiveMQ 可从 [`http://activemq.apache.org/`](http://activemq.apache.org/) 下载。

ActiveMQ 的主要区别特征在于它支持多种跨语言客户端和协议，包括 Java、C、C++、C#、Ruby、Perl、Python 和 PHP。ActiveMQ 支持以下协议（以及其他协议）：

*   OpenWire，用于 Java、C、C++ 和 C# 的高性能客户端
*   Stomp 支持，使得可以用 C、Ruby、Perl、Python、PHP、ActionScript/Flash 和 Smalltalk 轻松编写客户端，以便与 ActiveMQ 以及任何其他流行的消息代理通信
*   AMQP v1.0 支持
*   MQTT v3.1 支持，允许在物联网环境中进行连接

安装和配置 ActiveMQ
ActiveMQ 可在 [`http://activemq.apache.org/download.html`](http://activemq.apache.org/download.html) 下载。详细的安装说明也可在 [`http://activemq.apache.org/getting-started.html#GettingStarted-InstallationProcedureforWindows`](http://activemq.apache.org/getting-started.html%2523GettingStarted-InstallationProcedureforWindows) 获取。
下载名为 `apache-activemq-5.13.3-bin.zip` 的 ActiveMQ 安装文件。
从 zip 文件中，将名为 `apache-activemq-5.13.3` 的文件夹解压到 `C:\Program Files\ActiveMQ`（或应用程序文件合适的其他位置）。

我明确避免路径中包含空格的位置，例如 `Program Files` 中的空格。因此，在我的 Windows 机器上，我使用以下位置：

```
D:\Applns\apache\ActiveMQ\apache-activemq-5.13.3
```

开始使用 ActiveMQ

您可以通过以下命令启动 ActiveMQ 服务器：

```
cd D:\Applns\apache\ActiveMQ\apache-activemq-5.13.3\bin
D:\Applns\apache\ActiveMQ\apache-activemq-5.13.3\bin>activemq start
```

清单 F-1 展示了 ActiveMQ 的启动过程。



```
D:\Applns\apache\ActiveMQ\apache-activemq-5.13.3\bin>activemq start
Java Runtime: Oracle Corporation 1.8.0_45 D:\Applns\oracle\jdk\jdk1.8.0_45\jre
...
ACTIVEMQ_HOME: D:\Applns\apache\ActiveMQ\apache-activemq-5.13.3\bin\..
ACTIVEMQ_BASE: D:\Applns\apache\ActiveMQ\apache-activemq-5.13.3\bin\..
ACTIVEMQ_CONF: D:\Applns\apache\ActiveMQ\apache-activemq-5.13.3\bin\..\conf
ACTIVEMQ_DATA: D:\Applns\apache\ActiveMQ\apache-activemq-5.13.3\bin\..\data
Loading message broker from: xbean:activemq.xml
INFO | Refreshing org.apache.activemq.xbean.XBeanBrokerFactory$1@62ee68d8: startup date [Wed Mar 06 13:06:42 IST 2019]; root of context hierarchy
INFO | Using Persistence Adapter: KahaDBPersistenceAdapter[D:\Applns\apache\ActiveMQ\apache-activemq-5.13.3\bin\..\data\kahadb]
INFO | KahaDB is version 6
INFO | Recovering from the journal @4:13937439
INFO | Recovery replayed 28377 operations from the journal in 1.604 seconds.
INFO | PListStore:[D:\Applns\apache\ActiveMQ\apache-activemq-5.13.3\bin\..\data\localhost\tmp_storage] started
INFO | Apache ActiveMQ 5.13.3 (localhost, ID:tiger-61203-1551857807769-0:1) isstarting
INFO | Listening for connections at: tcp://tiger:61616?maximumConnections=1000&wireFormat.maxFrameSize=104857600
INFO | Connector openwire started
INFO | Listening for connections at: amqp://tiger:5672?maximumConnections=1000&wireFormat.maxFrameSize=104857600
INFO | Connector amqp started
INFO | Listening for connections at: stomp://tiger:61613?maximumConnections=1000&wireFormat.maxFrameSize=104857600
INFO | Connector stomp started
INFO | Listening for connections at: mqtt://tiger:1883?maximumConnections=1000&wireFormat.maxFrameSize=104857600
INFO | Connector ws started
INFO | Apache ActiveMQ 5.13.3 (localhost, ID:tiger-61203-1551857807769-0:1) started
INFO | For help or more information please see: http://activemq.apache.org
INFO | No Spring WebApplicationInitializer types detected on classpath
INFO | ActiveMQ WebConsole available at http://0.0.0.0:8161/
INFO | ActiveMQ Jolokia REST API available at http://0.0.0.0:8161/api/jolokia/
INFO | Initializing Spring FrameworkServlet 'dispatcher'
INFO | No Spring WebApplicationInitializer types detected on classpath
INFO | jolokia-agent: Using policy access restrictor classpath:/jolokia-access.xml
清单 F-1
启动 ActiveMQ 服务器
```

工作目录会相对于当前目录创建。为了在正确的位置创建工作目录，必须从其主目录/安装目录启动 ActiveMQ。

ActiveMQ 服务器将在默认端口 61616 上启动。你可以通过另一个窗口运行 `netstat` 并搜索端口 61616 来验证这一点。在 Windows 控制台中，输入

```
netstat -an|find "61616"
```

参见清单 F-2。

```
Microsoft Windows [版本 10.0.17134.345]
(c) 2018 Microsoft Corporation。保留所有权利。
C:\Windows\System32>netstat -an|find "61616"
TCP    0.0.0.0:61616          0.0.0.0:0              LISTENING
TCP    [::]:61616             [::]:0                 LISTENING
C:\Windows\System32>
清单 F-2
验证 ActiveMQ 监听端口
```

在 Unix 命令 shell 中，输入

```
netstat -nl|grep 61616
```

查看 ActiveMQ 管理控制台
现在，你可以使用你喜欢的网络浏览器连接到上述已启动的 ActiveMQ 服务器：
`http://127.0.0.1:8161/admin/`

你可以使用以下默认凭据登录管理控制台：

```
用户名: admin
密码: admin
```

参见图 F-1。

![../images/477630_1_En_25_Chapter/477630_1_En_25_Fig1_HTML.jpg](img/477630_1_En_25_Fig1_HTML.jpg)

图 F-1
ActiveMQ 管理控制台

配置 ActiveMQ
ActiveMQ 安装包含一个名为 `activemq.xml` 的文件，可用于配置 ActiveMQ 服务器。该文件通常位于 `D:\Applns\apache\ActiveMQ\apache-activemq-5.13.3\conf`。

在 ActiveMQ 中，无需显式设置/配置你将在应用程序中使用的队列。当你尝试从任何队列或主题发布或订阅时，相应的队列或主题将在运行时自动静默创建。但是，如果出于任何原因你想预先指定队列和主题，可以在配置文件中进行。例如，当你希望将目标放入 JNDI，以便应用程序无需知道实际的物理队列/主题名称即可将其取出时，可以这样做。清单 F-1 提供了一个队列的示例配置，图 F-2 显示了结果。

![../images/477630_1_En_25_Chapter/477630_1_En_25_Fig2_HTML.jpg](img/477630_1_En_25_Fig2_HTML.jpg)

图 F-2
配置 ActiveMQ

```

清单 F-3
在 ActiveMQ 中配置一个队列
```

总结
本附录涵盖了 Apache ActiveMQ（开源消息传递和集成模式服务器）的基本安装和设置。我在几个章节的示例中使用了 ActiveMQ，因此在阅读这些示例时，请参考本附录。

Derby

Apache Derby 是一个完全用 Java 实现的开源关系数据库，遵循 Apache 许可证。Derby 遵循 Java、JDBC 和 SQL 标准，并支持通过 Derby Network Client JDBC 驱动程序和 Derby Network Server 进行客户端/服务器模式操作。请访问 [`https://db.apache.org/derby/`](https://db.apache.org/derby/) 获取更多信息。

在本附录中，我将介绍 Derby 的以下内容：

*   如何安装和配置 Derby 数据库

*   如何在网络模式下启动和停止 Derby

*   如何在 Derby 中创建新数据库

*   如何在 Derby 数据库中执行基本的表创建和表操作命令

安装和配置 Derby
在本节中，你将下载、安装和配置 Derby，并测试安装。Derby 可在 [`https://db.apache.org/derby/derby_downloads.html`](https://db.apache.org/derby/derby_downloads.html) 下载。由于我的机器上装有 Java 8，我选择了 Derby 的 10.14.1.0 版本，该版本可在 [`https://db.apache.org/derby/releases/release-10.14.1.0.cgi`](https://db.apache.org/derby/releases/release-10.14.1.0.cgi) 获取。

首先将安装归档文件保存到硬盘上的合适位置，然后将归档文件解压到你选择的位置。尽管你可以选择任何位置，例如

```
C:\Program Files\Derby\
```

但我明确避免使用路径中包含空格的目录，例如 `Program Files` 中的空格。因此，在我的 Windows 机器上，我使用以下位置：

```
D:\Applns\apache\Derby\db-derby-10.14.1.0-bin
```

将 `DERBY_HOME` 环境变量设置为你解压 Derby 发行版的位置。例如，如果你将 Derby 解压到 `D:\Applns\apache\Derby\` 目录，则将环境变量设置为

```
set DERBY_HOME=D:\Applns\apache\Derby\db-derby-10.14.1.0-bin
```

如果你愿意，还可以将 `DERBY_HOME/bin` 目录添加到 `PATH` 环境变量中，以便从任何目录运行 Derby 脚本。

在网络模式下启动和停止 Derby 服务器
应用程序可以使用熟悉的客户端/服务器模式访问 Derby 数据库。这是通过一个嵌入 Derby 并处理来自应用程序（包括在同一台机器或远程机器上不同 JVM 中运行的应用程序）的数据库请求的框架实现的。Derby Network Server 嵌入 Derby 并管理来自网络客户端的请求。

要轻松启动 Derby Network Server，你必须首先将工作目录更改为 `DERBY_HOME` 内的 `bin` 文件夹（如图 G-1 所示）：

![../images/477630_1_En_26_Chapter/477630_1_En_26_Fig1_HTML.jpg](img/477630_1_En_26_Fig1_HTML.jpg)

图 G-1
在网络模式下启动 Derby 服务器



```
cd D:\Applns\apache\Derby\db-derby-10.14.1.0-bin
D:\Applns\apache\Derby\db-derby-10.14.1.0-bin\bin>startNetworkServer
```

这将在端口 1527 上启动网络服务器，并回显一些启动信息。当网络服务器处理连接请求时，更多信息将继续输出到同一窗口。由于网络服务器也会启动 Derby，你将在启动网络服务器的目录中找到 `derby.log` 错误日志。

为了维护数据库的完整性，始终建议在使用后正确关闭网络服务器。你可以通过以下命令轻松完成此操作：

```
cd D:\Applns\apache\Derby\db-derby-10.14.1.0-bin
D:\Applns\apache\Derby\db-derby-10.14.1.0-bin\bin>stopNetworkServer
```

**创建一个新的 Derby 数据库**

最好在与 Derby 安装位置不同的目录中创建和维护你的实际数据库。在我的机器上，我创建了一个父目录来存放我所有的 Derby 数据库：

```
D:\Applns\apache\Derby\derbydb
```

将当前目录切换到该文件夹：

```
cd D:\Applns\apache\Derby\derbydb
```

现在，你可以使用 Derby 嵌入式驱动程序来创建并连接到 `sampledb` 数据库。可以使用 Derby `ij` 工具来创建并加载 Derby 数据库。如果你已将 `DERBY_HOME/bin` 目录包含在 `PATH` 环境变量中，请键入：

```
ij
```

如果你尚未将 `DERBY_HOME/bin` 目录包含在 `PATH` 环境变量中，请键入：

```
java -jar %DERBY_HOME%\lib\derbyrun.jar ij
```

在我的机器上，我使用了以下命令：

```
D:\Applns\apache\Derby\derbydb>ij
ij version 10.14
ij>
```

现在，你可以使用以下命令创建名为 `sampledb` 的数据库，并使用嵌入式驱动程序打开与该数据库的连接：

```
connect 'jdbc:derby://localhost:1527/D:/Applns/apache/Derby/derbydb/sampledb;create=true';
```

参见图 G-2。

![../images/477630_1_En_26_Chapter/477630_1_En_26_Fig2_HTML.jpg](img/477630_1_En_26_Fig2_HTML.jpg)

**图 G-2** 以网络模式启动 Derby 服务器

上述命令提示符也将连接到新创建的数据库 `sampledb`。使用以下命令退出此窗口：

```
exit;
```

退出后，你可以再次使用 Derby 嵌入式驱动程序，通过以下代码重新连接到之前创建的数据库：

```
D:\Applns\apache\Derby\ derbydb>ij
ij version 10.14
ij>connect 'jdbc:derby://localhost:1527/D:/Applns/apache/Derby/derbydb/sampledb;create=false';
```

以下是一些有用的命令：

*   `SHOW CONNECTIONS`：如果没有连接，`SHOW CONNECTIONS` 命令会返回“No connections available.”。否则，此命令会显示连接名称列表以及用于连接它们的 URL。如果存在当前活动连接，其名称后会标有 ∗。

*   `SHOW SCHEMAS` 显示当前连接中的所有模式。

*   `SHOW TABLES` 显示当前模式中的所有表。如果指定了 `IN schemaName`，则显示给定模式中的表。

参见图 G-3。

![../images/477630_1_En_26_Chapter/477630_1_En_26_Fig3_HTML.jpg](img/477630_1_En_26_Fig3_HTML.jpg)

**图 G-3** 使用 Derby 嵌入式驱动程序 `ij` 与数据库交互

**在 Derby 数据库中执行基本命令**

现在，你可以使用以下 SQL 命令测试你的数据库：

```
CREATE TABLE SIMPLETABLE (ID INT PRIMARY KEY, NAME VARCHAR(12));
DESCRIBE SIMPLETABLE;
INSERT INTO SIMPLETABLE VALUES (50,'FIFTY'),(60,'SIXTY'),(70,'SEVENTY');
SELECT ∗ FROM SIMPLETABLE;
DELETE FROM SIMPLETABLE;
DROP TABLE SIMPLETABLE;
```

参见图 G-4。

![../images/477630_1_En_26_Chapter/477630_1_En_26_Fig4_HTML.jpg](img/477630_1_En_26_Fig4_HTML.jpg)

**图 G-4** 使用 `ij` 对 Derby DB 执行基本 SQL 操作

**总结**
Derby 是一个具有强一致性特征的关系型数据库，这就是我在本书中使用 Derby 来演示分布式事务场景的原因。

**MySQL**

MySQL 是一个流行的开源 SQL 数据库管理系统，由 Oracle 公司开发、分发和支持。MySQL 管理结构化的数据集合。MySQL 数据库帮助你添加、访问和处理存储在数据库中的数据。MySQL 将数据存储在单独的表中。数据库结构被组织成针对速度优化的物理文件。逻辑模型包含数据库、表、视图、行和列等对象，提供了一个灵活的编程环境。“MySQL”中的 SQL 部分代表“结构化查询语言”，这是用于访问数据库的最常见的标准化语言。MySQL 软件使用 GPL（GNU 通用公共）许可证，是开源软件。你可以在 [`www.mysql.com/`](http://www.mysql.com/) 获取有关 MySQL 的更多详细信息。

在本附录中，我将介绍 MySQL 的以下内容：

*   如何下载和安装 MySQL

*   如何为 MySQL 初始化数据目录

*   如何启动 MySQL 服务器并连接到服务器

*   如何测试与 MySQL 服务器的连接并断开与服务器的连接

*   如何创建数据库并执行基本的表操作

*   如何向数据库添加用户帐户

**安装 MySQL**
在本节中，你将下载、安装和配置 MySQL，并测试安装。MySQL 可在 [`https://dev.mysql.com/downloads/mysql/`](https://dev.mysql.com/downloads/mysql/) 下载。

对于 Microsoft Windows，MySQL 仅适用于 64 位操作系统。你可以下载以下存档文件：

```
mysql-8.0.14-winx64.zip
```

将上述存档文件解压到所需的安装目录。尽管你可以选择任何位置，例如：

```
C:\Program Files\MySQL\
```

但我明确避免路径中包含空格的位置，例如 `Program Files` 中的空格。因此，在我的 Windows 机器上，我使用以下位置：

```
D:\Applns\MySQL\mysql-8.0.14-winx64
```

详细的安装说明也可在 [`https://dev.mysql.com/doc/refman/8.0/en/windows-install-archive.html`](https://dev.mysql.com/doc/refman/8.0/en/windows-install-archive.html) 找到。

**初始化数据目录**

由于你使用 noinstall 包安装了 MySQL，因此不包含数据目录。要初始化数据目录，请从解压存档的主文件夹中调用 `mysqld`，如下所示：

```
cd D:\Applns\MySQL\mysql-8.0.14-winx64
D:\Applns\MySQL\mysql-8.0.14-winx64>D:\Applns\MySQL\mysql-8.0.14-winx64\bin\mysqld --initialize --console
2019-01-22T13:26:05.124628Z 0 [System] [MY-013169] [Server] D:\Applns\MySQL\mysql-8.0.14-winx64\bin\mysqld (mysqld 8.0.14) initializing of server in progress as process 14464
2019-01-22T13:26:54.151969Z 5 [Note] [MY-010454] [Server] A temporary password is generated for root@localhost: S9wdszB#
```

初始化数据目录的详细说明也可在 [`https://dev.mysql.com/doc/refman/8.0/en/data-directory-initialization-mysqld.html`](https://dev.mysql.com/doc/refman/8.0/en/data-directory-initialization-mysqld.html) 找到。由于你使用了 `--initialize` 进行“默认安全”安装（即包括生成随机初始 root 密码），该密码被标记为已过期，你需要选择一个新密码。但是，你可能需要记下上述密码，以便能够使用客户端连接到服务器，然后选择新密码。

**启动 MySQL 服务器**

要启动服务器，请输入以下命令：



```
D:\Applns\MySQL\mysql-8.0.14-winx64\bin>mysqld --console
2019-01-22T13:34:14.562420Z 0 [System] [MY-010116] [Server] D:\Applns\MySQL\mysql-8.0.14-winx64\bin\mysqld.exe (mysqld 8.0.14) starting as process 22172
2019-01-22T13:34:21.355370Z 0 [Warning] [MY-010068] [Server] CA certificate ca.pem is self signed.
2019-01-22T13:34:21.605828Z 0 [System] [MY-010931] [Server] D:\Applns\MySQL\mysql-8.0.14-winx64\bin\mysqld.exe: ready for connections. Version: '8.0.14'  socket: “  port: 3306  MySQL Community Server - GPL.
2019-01-22T13:34:21.870413Z 0 [System] [MY-011323] [Server] X Plugin ready for connections. Bind-address: '::' port: 33060
```

服务器会持续将后续生成的诊断输出写入控制台。你可以打开一个新的控制台窗口，在其中运行客户端程序。

首次启动服务器的详细说明，请参阅 [`https://dev.mysql.com/doc/refman/8.0/en/windows-server-first-start.html`](https://dev.mysql.com/doc/refman/8.0/en/windows-server-first-start.html) 。

连接到 MySQL 服务器

由于你使用了 `--initialize` 来初始化数据目录，因此你需要使用服务器在初始化序列期间生成的随机密码，以 root 身份连接到服务器：

```
D:\Applns\MySQL\mysql-8.0.14-winx64\bin>mysql -u root -p
Enter password: ∗∗∗∗∗∗∗∗∗∗∗∗
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 8
Server version: 8.0.14
Copyright (c) 2000, 2019, Oracle and/or its affiliates. All rights reserved.
```

Oracle 是 Oracle 公司和/或其附属公司的注册商标。其他名称可能是其各自所有者的商标。

```
Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
mysql>
```

接下来，你可以分配一个新的 root 密码：

```
mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY 'rootpassword';
Query OK, 0 rows affected (0.25 sec)
mysql>
```

当你从 Java 程序连接到 MySQL 服务器时，可能需要根据构建脚本 `pom.xml` 中使用的服务器版本，编辑 `mysql-connector-java` Maven 构件的版本：

```

8.0.14

mysql
mysql-connector-java
${mysqljcon.version}

```

测试 MySQL 服务器安装

你可以通过执行以下命令来测试 MySQL 服务器是否正常工作：

```
D:\Applns\MySQL\mysql-8.0.14-winx64\bin>mysqlshow -u root -p
Enter password: ∗∗∗∗∗∗∗∗∗∗∗∗
+------------------------------+
|     Databases                |
+------------------------------+
| information_schema   |
| mysql          |
| performance_schema |
| sys                          |
+------------------------------+
D:\Applns\MySQL\mysql-8.0.14-winx64\bin>
```

断开与 MySQL 服务器的连接

如果你正在使用 mysql 客户端连接到 MySQL 服务器，可以通过以下代码断开连接：

```
mysql> quit
Bye
D:\Applns\MySQL\mysql-8.0.14-winx64\bin>
```

创建并选择数据库

你可以按照“连接到 MySQL 服务器”部分所述，使用新提供的密码再次连接到数据库。连接后，你可以像这样查询当前连接的用户和数据库：

```
mysql> SELECT USER();
+----------------------+
| USER()               |
+----------------------+
| root@localhost |
+----------------------+
1 row in set (0.00 sec)
mysql> SELECT DATABASE();
+-----------------+
| DATABASE() |
+-----------------+
| NULL            |
+-----------------+
1 row in set (0.00 sec)
```

如果你尚未选择任何数据库，结果将为 NULL。

你可以使用以下代码创建一个新数据库：

```
mysql> create database ecom01;
Query OK, 1 row affected (0.16 sec)
mysql>
```

你可以使用以下代码查看新创建的数据库：

```
mysql> show databases;
+------------------------------+
| Database                     |
+------------------------------+
| ecom01                       |
| information_schema   |
| mysql                        |
| performance_schema |
| sys   |
+------------------------------+
5 rows in set (0.00 sec)
mysql>
```



首先，你需要选择新创建的数据库，操作如下：

```
mysql> use ecom01;
Database changed
mysql> SELECT DATABASE();
+-----------------+
| DATABASE() |
+-----------------+
| ecom01        |
+-----------------+
1 row in set (0.00 sec)
mysql>
```

创建表

选择数据库后，你就可以在该数据库中创建表了：

```
mysql> CREATE TABLE pet (name VARCHAR(20), owner VARCHAR(20),
-> species VARCHAR(20), sex CHAR(1), birth DATE, death DATE);
Query OK, 0 rows affected (0.63 sec)
mysql>
```

接下来，你可以查看新创建数据库的模式：

```
mysql> desc pet;
+---------+-------------+------+-------+----------+--------+
| Field   | Type        | Null | Key   | Default| Extra |
+---------+-------------+------+-------+----------+--------+
| name    | varchar(20) | YES  |       | NULL     |        |
| owner   | varchar(20) | YES  |       | NULL     |        |
| species | varchar(20) | YES  |       | NULL     |        |
| sex     | char(1)     | YES  |       | NULL     |        |
| birth   | date        | YES  |       | NULL     |        |
| death   | date        | YES  |       | NULL     |        |
+---------+-------------+------+-------+----------+--------+
6 rows in set (0.01 sec)
mysql>
```

向 MySQL 服务器添加用户账户

你可以添加新用户来访问数据库服务器并授予权限，操作如下：

```
mysql> CREATE USER 'tutorialuser'@'localhost' IDENTIFIED BY 'tutorialmy5ql';
Query OK, 0 rows affected (0.07 sec)
mysql> GRANT ALL PRIVILEGES ON ∗.∗ TO 'tutorialuser'@'localhost' WITH GRANT OPTION;
Query OK, 0 rows affected (0.03 sec)
mysql> CREATE USER 'tutorialuser'@'%' IDENTIFIED BY 'tutorialmy5ql';
Query OK, 0 rows affected (0.00 sec)
mysql> GRANT ALL PRIVILEGES ON ∗.∗ TO 'tutorialuser'@'%' WITH GRANT OPTION;
Query OK, 0 rows affected (0.00 sec)
https://dev.mysql.com/doc/refman/8.0/en/adding-users.html
```

停止 MySQL 服务器

要停止服务器，请输入以下命令：

```
D:\Applns\MySQL\mysql-8.0.14-winx64\bin>mysqladmin -u root -p shutdown
Enter password: ∗∗∗∗∗∗∗∗∗∗∗∗
D:\Applns\MySQL\mysql-8.0.14-winx64\bin>
```

总结
MySQL 是一个关系型数据库，可用于需要强一致性的操作，例如 ACID 事务中的操作。我在本书中使用 MySQL 来演示许多示例。

索引

A

ACID 事务

*与* BASE

ActiveMQ

配置

安装

管理控制台

队列

服务器

启动

ActiveMQXAConnectionFactory

聚合

聚合根

AggregateRoot.getVersion() 方法

Amazon Elastic Compute Cloud (Amazon EC2)

Amazon Web Services (AWS)

AMQP

Apache TCPMon

安装

代理设置

启动

API 网关

应用和基础设施组件

应用架构

应用架构冗余

应用负载均衡器 (ALB)

异步网络

异步 HTTP 方法

原子性

原子性、一致性、隔离性、持久性 (ACID)

Atomikos

AtomikosDataSourceBean

审计事件处理器

AuthenticationEntryPoint

AuthenticationManagerBuilder

授权码授权类型

OAuth

交互序列

授权服务器

AuthenticationEntryPoint

@EnableAuthorizationServer 注解

HttpSecurity

JwtTokenStore

OAuth 安全配置

Spring 安全配置

AuthorizationServerConfigurerAdapter

AUTO_ACKNOWLEDGE

自动存储管理 (ASM)

自治系统编号 (ASN)

自动缩放

可用区 (AZs)

AWS 区域

Axon

聚合

JVM 中的命令和事件处理

AbstractAggregateRoot

ACID

聚合

AggregateRoot

构建和测试

代码示例

CommandGateway

CommandHandler

DefaultCommandGateway

事件总线

事件处理器

锁

元模型

乐观锁

工作单元

分布式命令和事件处理

集群事件总线

代码示例

设计

Ecom-EventHandleAudit

Ecom-EventHandleCore

SpringAMQPTerminal

测试

事件总线

执行器

可扩展架构

look-to-book

读写比

仓库

单一事实来源

线程池

Axon 3

Axon 3 CQRS 微服务

ACID 风格

Axon 4

电子商务

handleNewOrder 处理

JVM

Saga

Axon 4

Axon CQRS

Axon-distributed-commandbus-springcloud

B

BASE 架构

BASE 事务

Axon 组件



HTTP PUT

微服务架构

宽松的

基本可用、软状态、最终一致性（BASE）系统

物料清单（BOM）

Bitronix

边界网关协议（BGP）

代理 Web 微服务

业务领域对象模型（BDOM）

业务流程模型与标记法（BPMN）框架

C

CAP 金字塔

CAP 定理

ACID 事务

架构属性

原子处理器

可用性

轴扩展

BASE 系统

基本可用

CAP 金字塔

一致性

数据分区

水平扩展

分区容错性

RAC

关系型数据库

扩展立方体

扩展方法

三轴扩展

权衡

垂直扩展

Web 规模架构

链式事务

断路器

传统负载均衡器（CLB）

CLIENT_ACKNOWLEDGE

客户端-服务器架构

客户端套接字

基于云的架构

API 网关

云服务模型

容器

Docker

IaaS

PaaS

SaaS

虚拟化

组件

CSP

服务粒度

层

以技术为中心的方法

*对比* 以领域为中心

Web 规模 IT

云服务提供商（CSP）

集群事件总线

ClusteringEventBus

命令与事件处理，Axon 3

构建与测试

设计

Ecom-CreateCommandRestController

Ecom-EventHandleCore

Ecom-EventHandlerAudit

Ecom-HandleCommandAndCreateEvent

Ecom-registry

Ecom-web

命令与事件处理，JVM

构建与测试

代码

设计

命令总线连接器

命令处理器

命令查询职责分离（CQRS）模式

架构

基于微服务

命令

并发读取

并发写入

基于事件的架构

Axon

使用事件的命令查询

元模型

横向扩展

扩展面向消息的微服务

事件

元模型

读写事务

用于……的数据库节点

横向扩展

用于……的模式

读取模型

只读副本

横向扩展

写入模型

命令路由器

通用信息模型（CIM）

计算机网络

分布式系统

IP

TCP

配置服务器

构建与测试

配置参数

受控仓库

依赖

设计

@EnableConfigServer

GIT URL

Maven 依赖

微服务配置

Product Server 微服务

spring-cloud-config-server

spring-cloud-starter-config

Connection.rollback()

一致性

基于容器的虚拟化

协同攻击问题

CORBA

创建、读取、更新和删除（CRUD）

cURL

D

数据库管理员（DBA）

数据一致性

非军事区（DMZ）

Derby

客户端/服务器模式

创建

数据库

定义

安装

网络模式

设计安全性

微服务架构

非对称加密

引用令牌

Web 应用功能

有向无环图（DAG）

灾难恢复（DR）

DisruptorCommandBus

分布式 ACID 事务

分布式命令总线

分布式计算架构

应用

典型

典型部署

网络集成拓扑（*参见* 网络架构）

可扩展性困境

应用单体

应用状态

模块间依赖

模块化方法

软件

应用层

应用层

系统（*参见* 系统架构）

分布式数据

全局事务

本地事务

单体应用

报价表

用户余额表

分布式消息传递

异步

阻塞

集群

并发

关联标识符

依赖

管道

生产者和消费者

客户端套接字

并发

上下文切换

关联

correlationId 属性

CPU

执行器池

网络服务器

网络服务器监听

操作系统资源

资源共享

服务器套接字

同步

RabbitMQ 集群

高可用性

多节点

Nginx

nginx.conf

TCP 负载均衡器

TCP 反向代理

RabbitMQ 消息代理

Ant 构建与运行

com.rabbitmq.client.Consumer

com.rabbitmq.client.DefaultConsumer

Maven 构建与运行

接收者

发送者

弹性

异步

集群

消息通道

消息持久化

消息存储

运行特性

发布

弹性或可用的微服务

可扩展性

订阅

返回地址

单线程执行

Spring AMQP

特性

消息监听器

消息生产者

RabbitTemplate

运行 RabbitMQ 示例

同步通信

分布式 Saga

分布式事务

Bitronix

边界级别操作

代理 Web 微服务

幂等操作

在 Java 中

JOTM

本地（*参见* 本地事务）

消息传递陷阱

Narayana

乱序消息

报价处理（Broker-MySQL-ActiveMQ）

Atomikos

拍卖服务

代理服务

flipFlop()



JTA UserTransaction 和 TransactionManager

Maven 依赖

PlatformTransactionManager

PROPAGATION_REQUIRED

PROPAGATION_REQUIRES_NEW

Quartz 调度器

报价实体

定时任务

Spring 装配

股票订单服务

WebSphereUowTransactionManager

XA JDBC 资源，Spring 装配

XA JMS 资源，Spring 装配

报价结算（Settlement-ActiveMQ-Derby）

Atomikos

Maven 依赖，Derby 客户端

报价对账服务

Spring 装配

事务性消息监听器

用户实体

结算 Web 微服务

股票交易处理系统

StockTransaction 实体

XA 事务，架构

分而治之

DNS 查找

领域实体

域名系统（DNS）

重复消息消费测试用例

重复消息，发送场景

ActiveMQ 队列配置

经纪人 Web 微服务

报价处理微服务

报价结算微服务

结算 Web 微服务

测试用例

XA 事务

持久性

E

电子商务

业务

类图

微服务（*参见* 电子商务微服务）

单体架构

电子商务微服务

BASE 中的 ACID

架构

BDOM/CIM

构建与运行

方面

后台管理

Eclipse IDE

插入库存数据

Java 应用程序

主类

Maven 项目

CacheManager

取消订单

购物车微服务配置

配置微服务配置

核心微服务配置

配送微服务配置

Eclipse IDE 文件

网关微服务配置

获取产品详情

历史微服务配置

基础设施设置

库存读取仓库

逻辑架构

新订单，创建

订单配送

订单配送失败

ProductCategoryRepository

产品微服务配置

注册中心微服务配置

发货订单

物流微服务配置

技术架构

用户微服务配置

电子商务微服务应用，代码安全

授权服务器

基础设施设置

资源服务器

设置

Web 应用（*参见* Web 应用，代码安全）

Zuul 过滤器

EcomProductMicroserviceApplication

弹性块存储（EBS）

弹性 IP

弹性负载均衡器（ELB）

电子稳定程序（ESP）

空的 configure() 方法

@EnableAsync 注解

@EnableDiscoveryClient

@EnableEurekaClient

@EnableEurekaServer

@EnableHystrixDashboard

@EnableZuulProxy

EndSaga

企业服务总线（ESB）

企业事务类别

ACID

BASE

Erlang

Eureka 控制台

Eureka，服务注册中心

引导服务器

构建与测试

配置

依赖

设计

DMZ

DNS

@EnableEurekaClient

@EnableEurekaServer

getAllProducts()

getProducts()

HAL JSON 格式

@LoadBalanced

查找

基于事件的 CQRS 架构

基于事件的基础设施

基于事件的微服务

事件总线

事件通道

事件驱动架构（EDA）

ACID

异步

通道

定义，事件

发射器

灵活

灵活的发布-订阅

明智的权衡

微服务（*参见* 微服务与 EDA）

点对点

发布-订阅

队列

软件架构模式

紧耦合

事件驱动的基础设施

事件发射器

最终一致性与微服务

业务应用

电子商务单体应用

Mac Book Pro

单体应用

NotEnoughInventory 事件

OrderCreated 事件

Order、Item 和 Inventory

竞态条件

F

Fat JAR

Feign 客户端

构建与测试

组件

依赖

设计

@EnableFeignClients

@FeignClient

ProductServiceProxy

spring-cloud-starter-feign

@FeignClient 注解

以太网光纤通道（FCoE）

IP 光纤通道（FCIP）

扁平事务

FlipFlop

G

getJMSCorrelationID()

GET（订阅）

全局 *vs.* 本地资源

架构选项

分布式 ACID 事务

分布式资源

部分优化

本地事务

单体报价应用

队列参与

报价微服务架构

全局两阶段事务

全局唯一标识符（GUID）

H

HAL 浏览器

高可用场景，构建与测试

Ecom-EventHandleCore

Ecom-EventHandlerAudit

Ecom-HandleCommandAndCreateEvent

Ecom-web

RabbitMQ 服务器

HibernateJpaVendorAdapter

hibernate.transaction.jta.platform

hibernate.transaction.manager_lookup_class

高可用性（HA）

基线

分解

融合网络

数据中心

部署架构

DMZ

DNS 负载均衡

DNS 冗余

以太网流量

故障转移



全局负载均衡

ISP 冗余

微服务

复制

存储区域网络（SAN）

测量

微服务

应用组件

构建与测试

CRUD

Eureka

eureka.client.serviceUrl.defaultZone

在 Eureka 控制台中

FeignClient

Hystrix

MASA 风格部署

MVC

Nginx

注册中心

反向代理

spring-boot-starter-web

Web 应用

Zuul

高浏览预订比

主机总线适配器（HBA）

HTTP DELETE

HTTP GET

HTTP PATCH

HTTP POST

HTTP PUT

HTTP 反向代理

中心辐射型架构

超文本应用语言（HAL）

超文本作为应用状态引擎（HATEOAS）

Hystrix 仪表盘

构建与测试

依赖

设计

@EnableCircuitBreaker

@EnableHystrixDashboard

@HystrixCommand

ProductServerComponent

流监控

Hystrix 降级

构建与测试

断路器

组件

依赖

设计

ProductAlternateServerComponent

ProductAlternateServiceProxy

ProductServiceProxy

服务等级协议（SLA）

I

幂等操作

基础设施即服务（IaaS）

集成开发环境（IDE）

微服务间通信

互联网号码分配局（IANA）

互联网工程任务组（IETF）

互联网协议（IP）

架构反转（IoA）

隔离

J、K

JavaScript 对象表示法（JSON）

Java 服务器页面（JSP）技术

Java 事务 API（JTA）

Java 事务服务（JTS）

java.util.concurrent.Callable

java.util.concurrent.Future

javax.servlet.AsyncContext 类

javax.transaction.UserTransaction 接口

JOTM

JSON Web 加密（JWE）

JSON Web 签名（JWS）

JSON Web 令牌（JWT）

JtaTransactionManager

JwtAccessTokenConverter

JwtTokenStore

L

LoadBalancerClient

localTransactionMode 属性

本地事务

自动提交

Connection.commit()

java.sql.Connection 接口

会话

工作单元

锁定

Logback

Log4J2

Long getVersion()方法

消息丢失测试用例

M

基于大型机的架构

MappingJackson2HttpMessageConverter

Maven Eclipse 插件

网格应用与服务架构（MASA）

消息代理

消息已消费、处理失败测试用例

面向消息的微服务

消息乱序接收场景

报价创建

报价处理

报价与用户表

股票交易表

测试

消息重新投递测试用例

消息传递

元模型

聚合实体

命令网关

命令处理器

命令

控制器

CQRS 元模型

领域实体

事件总线

事件处理器

事件

事件存储

仓库

微单体

微服务

架构

异步 HTTP

Angular JS 客户端

AsyncRestTemplate 类

Boot 主应用类

Callable

CompletableFuture

控制台日志

控制器类

cURL 与 Postman

DeferredResult

设计

ListenableFuture

ProductRestController

REST 控制器

可伸缩性

服务器线程

服务组件

Servlet 连接

SimpleAsyncTaskExecutor

spring-boot-starter-web 依赖

Spring MVC

线程动态

CAP 定理（*参见* CAP 定理）

独立模块

模块间通信

单体边界

协议缓冲区

Apache TCPMon 代理

向后兼容性

设计

Google 文档

java_outer_classname 类

编组与解组

消息转换器

产品服务器控制器

protobuf-java-format

protoc-jar-maven-plugin

proto 文件

RestTemplate

面向服务架构（SOA）

同步 HTTP

TCPMon 代理

JSON 编码

协议缓冲区编码

协议 XML 编码

XML 编码

微服务与事件驱动架构（EDA）

设计

最终一致性（*参见* 最终一致性与微服务）

演进

蜂巢类比

交互

通知服务

OrderShipped 事件

发布-订阅微服务模式

传统/单体应用

微服务架构（MSA）

数字业务

数字应用上下文

企业解决方案模式（ESP）

MASA

内部架构视角

架构反转（IoA）

MASA

符号表示

外部架构视角

基于微服务的架构

故障安全微服务

蜂巢类比

HTTP 服务器服务

微单体

n 层分布式架构

可伸缩性困境

到 SOA

模块化单体

应用边界

IDE

模块间通信

非功能需求（NFR）

可伸缩性

服务层应用

SLA

技术约束

MongoDB

文档

安装文件

操作

Robomongo

服务器

Mongo 仓库

单体应用

单体风格架构

多页面架构（MPA）

多线程环境

MySQL

连接服务器

创建与选择

创建表

数据目录

定义



disconnect

installation

start the server

stop

testing

user accounts

MysqlXADataSource 类

N

嵌套事务

网络架构

企业消息总线拓扑

ESB

中心辐射型

点对点

网络集成拓扑

网络接口卡 (NIC)

网络负载均衡器 (NLB)

Nginx

定义

安装

启动

非功能性需求 (NFR)

不可重复读

非易失性内存快速通道 (NVMe)

N 层架构

O

OAuth 2.0

授权服务器

HTTP 服务

资源所有者

资源服务器

*与* 用户代理

OAuth2AuthenticationProcessingFilter

对象关系映射 (ORM)

对象事务服务 (OTS)

在线旅行社 (OTA) 网站

操作系统级虚拟化

乐观锁

构建与测试

代码

设计

Oracle Active Data Guard 复制 (OADGR)

Oracle Data Guard 复制 (ODGR)

Oracle 真正应用集群 (RAC)

Oracle 恢复管理器 (RMAN)

Oracle 安全备份云模块 (OSB)

org.axonframework.domain.AbstractAggregateRoot<I>

org.axonframework.domain.AggregateRoot<I>

org.hibernate.StaleObjectStateException

P

PagingAndSortingRepository

模式

CQRS（*参见* 命令查询职责分离 (CQRS)）

查询预订比

在线事务

读取事务

传统 *与* 基于 CQRS 的软件系统

写入

*与* 读取事务

横向扩展

悲观锁

平台即服务 (PaaS)

PlatformTransactionManager

点对点架构

点对点 EDA

多语言

Postman

processNewQuotes()

ProductAlternateServiceComponent

ProductAlternateServiceProxy

ProductRestController

PROPAGATION_MANDATORY

PROPAGATION_NESTED

PROPAGATION_NEVER

PROPAGATION_NOT_SUPPORTED

PROPAGATION_REQUIRED

PROPAGATION_REQUIRES_NEW

PROPAGATION_SUPPORTS

PUT（发布）

Q

QuartzEventScheduler

报价处理微服务

报价结算

监听器编排器

监听器对账

微服务

对账服务代码

对账服务辅助方法

股票交易实体

交易服务

R

RabbitMQ

集群

配置

动态 DNS

重启

启动节点

TCP 负载均衡器

配置

Erlang

安装

管理控制台

rabbitmq_management 插件

可靠性

路由

服务器

rabbitmq_management

RabbitMQ 消息代理

RabbitTemplate

读锁

只读副本

读取副本

读取事务

读取 *与* 写入可扩展性

真正应用集群 (RAC)

恢复点目标 (RPO)

恢复时间目标 (RTO)

区域互联网注册机构 (RIR)

关系数据库管理系统 (RDBMS)

复制的读取模型

RepositoryRestResource

表述性状态转移 (REST)

定义

HAL 浏览器

基于 HATEOS 的控制器

DELETE 方法

GET 操作

JSON

POST 操作

PUT 操作

@RequestMapping

@RestController

HTTP 方法

DELETE

GET

POST

PUT

Jackson2HalModule

Logback 依赖

原则

REST 客户端

RestTemplate

Spring Data REST

测试 HAL REST 端点

@RequestMapping 注解

资源服务器

GlobalMethodSecurity

OAuth2

令牌解密

ResourceServerConfigurerAdapter

基于 REST 的注册中心

RestTemplate

反向代理服务器

基于 Ribbon 的负载均衡

Ribbon，客户端负载均衡器

构建与测试

依赖

设计

@FeignClient 注解

Robomongo

Route 53

runAsync() 方法

S

SaaS 成熟度模型

Saga

应用

代码示例

Ecom-CreateCommandRestController

Ecom-EventHandleAudit

Ecom-EventHandleCore

Ecom-HandleCommandAndCreateEvent

Ecom-web

补偿事务

设计

分布式

组件

DAG

在事件驱动 CQRS 上下文中

执行

日志

消息延迟

SEC

事务特性

事务并行

长事务

测试

五个微服务

MongoDB

MySQL

RabbitMQ 服务器

事务协调器

两阶段提交事务

SagaEventHandler

Saga 执行协调器 (SEC)

Saga 事务协调器

可扩展的 API 调用

JWT

会话 ID

令牌

可扩展架构

分而治之

无状态设计

扩展立方体

扩展聚合根实体

安全

API 网关

授权码授权类型

OAuth 2.0

受信任客户端/非受信任客户端

信号量

序列化

服务器套接字

@Service 注解

服务等级协议 (SLA)

面向服务的架构 (SOA)

服务提供者接口 (SPI)

服务注册中心

SESSION_TRANSACTED 值

setAutoCommit() 方法

setJMSCorrelationID

结算 Web 微服务



下游微服务

用户表和报价表

Spring Boot 应用程序

股票交易表

上游微服务

分片

SimpleAsyncTaskExecutor

SimpleCommandBus

SimpleEventBus

SimpleEventScheduler

简单初始架构

简单日志门面（SL4J）

简单水平扩展架构

单页应用程序（SPA）

单页架构（SPA）

单线程环境

软件即服务（SaaS）

固态硬盘（SSD）

SpringApplication.run() 方法

Spring Boot

应用程序

创建 Spring Initializer

依赖

生成模板

Maven 插件

spring-boot-starter-data-mongodb

spring-boot-starter-data-rest

spring-boot-starter-parent 项目

cURL

领域模型

胖 JAR

JPA

MongoRepository 接口

@RepositoryRestResource

REST 客户端

测试应用程序

GET HTTP 请求，cURL

GET HTTP 请求，Postman

Junit 测试

Spring Boot Maven 插件

spring-boot-starter-web 依赖

Spring Cloud

微服务生态系统

命令路由器

spring-cloud-config-server

spring-cloud-starter-config

spring-cloud-starter-eureka

spring-cloud-starter-eureka-server

spring-cloud-starter-feign 依赖

spring-cloud-starter-hystrix

spring-cloud-starter-hystrix-dashboard

spring-cloud-starter-ribbon

Spring Data REST

Spring HTTP 命令总线连接器

SpringSecurityFilterChain

SpringTransactionManager

startAsync() 方法

StockTransaction

存储区域网络（SAN）

supplyAsync 方法

系统架构

客户端-服务器

基于大型机

N 层

三层

T

TaskExecutor

TCP/IP 网络

TCP 负载均衡器

TCP 反向代理

以技术为中心的方法

thenApply() 方法

Thread.sleep() 方法

事务属性

事务 ID 模式

事务隔离

TransactionManager

TransactionManagerLookup

事务模型

事务处理（TP）

TransactionProxyFactoryBean

事务回滚测试用例

事务

ACID

acknowledge()

并发级别

CRUD

Derby，本地事务

Derby，两阶段事务

分布式数据

幂等操作

隔离级别

TRANSACTION_NONE

TRANSACTION_READ_COMMITTED

TRANSACTION_READ_UNCOMMITTED

TRANSACTION_REPEATABLE_READ

TRANSACTION_SERIALIZABLE

消息队列操作

分区

窥探能力

用例

预写日志

传输控制协议（TCP）

受信任 *与* 不受信任的客户端

API 架构

三层单体架构

HTTP 服务器

用户会话

Web 应用

两军问题，*参见* 两将军悖论

两将军悖论

任意通信故障

图解

微服务作为将军

解决方案

TCP/IP

两阶段提交协议

典型应用架构

典型部署架构

U

uber JAR

工作单元（UoW）

usersByUsernameQuery

UserTransactionImp

UserTransactionManager

用例，电子商务

添加商品到购物车

后台管理首页

后台管理登录页面

取消订单

配送失败

终端用户首页

创建新订单

订单配送

产品类别

回滚库存

发货订单

用户资料

创建

登录提示

登录

V

垂直扩展

虚拟化

虚拟私有云（VPC）

W

Web 应用，代码安全

doOrder 操作，路由

首页

已登录

路由映射

侧边用户认证

user_controller.js

user.html

WebLogicJtaTransactionManager

WebSecurityConfigurerAdapter

WebSphereUowTransactionManager

写锁

写事务

X, Y

XADataSource 接口

Z

Zuul，API 网关

AWS ELB

引导 URL

构建与测试

依赖

设计

DMZ

DNS

@EnableDiscoveryClient

@EnableZuulProxy

Eureka 控制台

hystrix

负载均衡

微服务

产品服务微服务

注册表查找

Ribbon

路由

spring-cloud-starter-zuul

```
```