# 33. 账户处理

Michael Müller^(1 )

(1)德国，北莱茵-威斯特法伦州，布吕尔

如果您还记得第 32 章的自定义领域，我当时将 `obtainPermittedGroups` 方法留空，并承诺稍后描述。为了对用户进行身份验证，自定义领域需要检查用户的凭据，这些凭据存储在账户对象中。或者用数据库的术语来说，存储在账户表中。如果我们向领域添加一个数据访问类，我们也需要在 Alumni 中访问该账户。

考虑到 DRY 原则（*不要重复自己*），这就提出了一个问题：是否可能不在两个不同的地方使用相同的代码？我们希望只编写一次访问账户的代码，然后在不同的地方重用。我们是应该编写一个可供 Alumni 和自定义领域使用的库？还是应该让自定义领域调用 Alumni 的相应数据访问方法？这两种解决方案都有其缺点，尤其是后者，它强制领域了解 Alumni。是否有可能独立运行该账户对象？

好消息是：是的，可以。



## 微服务

创建、检索和删除用户账户，以及设置其状态和登录功能，将由一个独立于 Alumni 和自定义领域运行的小型应用程序处理。通常，这样的单元被称为*服务*。你可以独立于其他单元来开发和部署此类服务。在 Web 开发领域，我们通常使用两种服务：

*   使用 SOAP 通信的 WS* 服务
*   基于 REST 架构风格的服务

SOAP（[www.w3.org/TR/soap/](http://www.w3.org/TR/soap/)）是 WS* 系列中 Web 服务通信的协议标准。通常 SOAP 通过 HTTP 进行隧道传输。*SOAP* 最初代表简单对象访问协议，但如今仅使用其缩写。目前已有更简单的协议可用。

我们之所以谈论 WS* 系列，是因为这类服务使用 Web 服务描述语言（WSDL）。（你可以在 [www.w3schools.com/xml/xml:wsdl.asp](http://www.w3schools.com/xml/xml:wsdl.asp) 阅读更多关于 WSDL 的内容。）当 Web 服务在 21 世纪初与面向服务的架构（SOA）一起流行时，它们通常与一种注册表结合使用，该注册表可用于查找服务目录。从使用 WS* 服务的应用程序角度来看，它可以被视为一种远程方法调用。调用此类服务就像调用本地方法一样简单。我们可以传递参数并接收结果对象。就个人而言，我经常使用 WS* 服务，但在 Alumni 项目中，我选择遵循类似 REST 的风格，这种风格通常更轻量级。

如今，我们谈论的是微服务。人们常常误解这个术语。*微服务*并不是一个“小”服务。它是一个提供一组完整、高内聚相关功能的应用程序。其功能通过公共接口提供，并可通过网络访问。微服务是独立的；它不需要任何支持组件（例如为 WS* 服务设计的注册表）的开销。

这个定义将微服务限制为既不是特殊协议，也不是特殊架构。如果我们省略支持基础设施，WS* 服务也可以充当微服务。

另一种流行的服务是遵循 REST 架构风格的服务。当然，还有更多类型的服务，但我在本章中讨论的这两种是 Web 开发领域中最重要的。

###### 注意

顺便提一下，*REST* 是 *REpresentational State Transfer*（表述性状态转移）的缩写，由 Roy Thomas Fielding 在其论文“架构风格与基于网络的软件架构设计”中描述，你可以在 [www.ics.uci.edu/~fielding/pubs/dissertation/top.htm](http://www.ics.uci.edu/%7Efielding/pubs/dissertation/top.htm) 阅读该论文。

在 REST 中，资源由标识符表示。使用 HTTP 作为传输介质，资源由 URI 标识。对此类资源的操作与 HTTP 方法（如 POST、GET、PUT 或 DELETE）相关联。通常，POST 用于创建对象，GET 用于读取，PUT 用于更新，DELETE 用于删除。因此，所有众所周知的 CRUD 操作都可以得到支持。

有关 REST 和 HTTP 方法的更多信息，你可以阅读“为 RESTful 服务使用 HTTP 方法”（[www.restapitutorial.com/lessons/httpmethods.html](http://www.restapitutorial.com/lessons/httpmethods.html)）或“RESTFUL API 教程”（[`restfulapi.net/http-methods/`](https://restfulapi.net/http-methods/)）。还有许多其他文章和优秀书籍可供参考。讨论 RESTful 服务的所有基础知识远远超出了本书的范围。

假设我们要处理客户。可以使用 GET 方法通过 <baseAddress>/customers 访问客户列表。使用 POST 方法向同一地址传递客户数据将创建一个新客户并返回其 ID。对 <baseAddress>/customers/<id>（其中 <id> 代表相应的 ID）执行 GET 操作将检索该客户，而对同一地址使用 DELETE 方法则会将其删除。

Java EE 包含用于 RESTful Web 服务的 Java API（JAX-RS）。当前版本 Java EE 8 包含 JAX-RS 2.1，该版本已被定义为 JSR370（[`jcp.org/en/jsr/detail?id=370`](https://jcp.org/en/jsr/detail%3Fid=370)）。

对于 Alumni，账户信息的访问由基于 JAX-RS 的微服务处理。

## 账户服务

许多较旧的 JSF 应用程序（包括我早期的项目）倾向于变得庞大。编译和部署不断增长的应用程序需要越来越长的时间。这样的单体应用变得更难维护，因此最好使用一组较小的部分来组成整个应用程序。我们可以使用*门户组件*（参见 JSR 362 的门户组件规范，网址为 [`jcp.org/en/jsr/detail?id=362`](https://jcp.org/en/jsr/detail%3Fid=362)）集成几个独立的 JSF 应用程序。或者，我们可以设计几个 JSF 应用程序，通过共享会话来无缝交互。或者，我们可以调用服务。

让我们看看 *Alumni* 如何处理其账户。访问其 API 的地址将是 http://<server:port>/AccountService/Accounts。Alumni 可以使用 POST 方法调用此地址，并传递一些账户数据来创建一个新账户。与前面提到的客户示例不同，我插入了一个额外的 id 子路径来访问特定账户（希望 RESTful 纯粹主义者不会责怪我）。这使我能够为启用登录和禁用账户等特定功能添加其他子分支。



###### 账户 API 文档

一个账户可能处于以下四种不同状态之一：

*   新建

*   活跃

*   非活跃

*   已注销

创建账户时，初始状态会被设置为*新建*。如果用户在创建后的三天内激活其账户，状态将变为*活跃*；否则，该账户将被移除（从数据库中删除）。

当且仅当账户处于活跃状态时，用户才可以：

*   登录其账户

*   更新账户信息

*   删除其账户

DELETE 操作可以将账户从数据库中完全移除，或者将账户状态设置为*已注销*并移除标识用户身份的关键信息（姓名、电子邮件等）。

此行为取决于与该账户关联的数据。如果某些数据需要作为系统内的信息保留，那么该账户将以匿名形式保留在已注销状态。否则，它将从数据库中被完全删除。

当且仅当账户处于活跃状态时，管理员可以将其设置为*非活跃*。这样做可能是为了临时禁用某个账户，例如在调查滥用账户的案件期间。之后，管理员可以再将状态切换回活跃。

基于此类原因，账户服务（AlumniAccount）提供了以下 API：

```
AlumniAccount/api/account
```

*   POST：创建一个账户。

*   如果数据库中存在具有给定电子邮件的账户，则返回 409 冲突。

*   如果账户创建成功，则返回 201 已创建。如果给定的 loginName 在数据库中已存在，系统会将其更改为一个唯一的 loginName。

```
AlumniAccount/api/account/{id}
```

*   GET：检索账户。

```
AlumniAccount/api/account/{id}
```

*   PUT：更新账户。

```
AlumniAccount/api/account/{id}
```

*   DELETE：删除账户（要么匿名化并设置为已注销，要么从数据库中完全移除）。

```
AlumniAccount/api/account/activate/{id}
```

*   PUT：将账户状态设置为活跃。

```
AlumniAccount/api/account/login/{loginName}/{password}
```

*   GET：如果允许用户登录，则返回一个组列表。

尽管 AccountService 是一个独立的 Web 应用程序，但其项目对象模型（POM）派生自 Alumni。

NetBeans 原生支持创建 RESTful 服务。您可以选择从模式、数据库或实体创建新的 RESTful Web 服务。例如，如果您想从实体类创建，NetBeans 会为数据访问以及服务及其配置创建一个类。这通常是您进一步开发的一个良好起点。

要配置 REST 服务，我们需要一个派生自 Application 的类。使用 @ApplicationPath 注解并指定您的 REST API 的路径。此路径将附加到应用程序的上下文路径之后。我简单地使用了 api。

在我们的类中，我们需要重写 getClasses 方法，该方法返回我们想要为服务暴露的所有类。如果您使用 NetBeans 创建 REST 服务，配置类会包含一个 addRestResourceClasses 方法来添加服务类。不要修改此方法，因为它是自动生成的，并且如果您添加或删除 REST 服务类，NetBeans 会对其进行管理。请参见清单 33-1。

###### 清单 33-1 REST 服务配置

```
01   @javax.ws.rs.ApplicationPath("api")
02   public class ApplicationConfig extends Application {

04     @Override
05     public Set<Class<?>> getClasses() {
06       Set<Class<?>> resources = new java.util.HashSet<>();
07       addRestResourceClasses(resources);
08       return resources;
09    }

11     private void addRestResourceClasses(Set<Class<?>> resources) {
12       resources.add(de.muellerbruehl.alumniaccount.rest.AccountService.class);
13     }

15   }
```

在该清单中，我们只添加了一个服务类。让我们在清单 33-2 中查看它。

###### 清单 33-2 AccountService（节选）

```
01  @Path("/account")
02  public class AccountService {

04    @Context private UriInfo context;

06    @POST
07    @Consumes(MediaType.APPLICATION_JSON)
08    @Produces(MediaType.APPLICATION_JSON)
09    public Response createAccount(Account account) {
10      try {
11        AccountFacade.getInstance().createAccount(account);
12        URI path = context.getAbsolutePath().resolve(account.getId());
13        return Response.created(path).entity(account.getAccessKey()).build();
14      } catch (IllegalArgumentException ex) {
15        return Response.status(Status.CONFLICT).build();
16      } catch (Exception ex) {
17        return Response.notAcceptable(null).build();
18      }
19    }

21    @GET
22    @Path("{id}")
23    @Produces(MediaType.APPLICATION_JSON)
24    public Account getAccount(@PathParam("id") String id) {
25      Account account = AccountFacade.getInstance().findAccount(id);
26      return account;
27    }
28    ...
29  }
```

在第 1 行，有一个 Path 注解。这声明了 API 内的相对路径。完整路径由协议、服务器、端口、上下文路径、配置的 API 路径以及最后的账户资源相对路径组合而成。因此，在本地机器上访问 AccountService 的完整路径将是 http://localhost:8082/AlumniAccount/api/account。您可能想知道为什么端口与通常的 8080 不同。我将此服务运行在它自己的应用服务器上。（是的，可以将其 Docker 化，但这超出了本书的范围。）

节选部分包含两个方法：createAccount 通过 API 接收一个账户并将其传递给 AccountFacade，后者执行一些检查并持久化该账户。在第 4 行，我们注入了一个 UriInfo 对象。我们将用它来为成功创建时的响应创建一个路径。在此响应中，我们返回新创建的账户 id。

如果新创建的（尚不存在的）账户带有一个在账户表中已存在的电子邮件，则会抛出 IllegalArgumentException。一旦我们在此处捕获到它（第 14 行），我们就返回 HTTP 错误代码 409（冲突）。在其他任何情况下，我们返回一个不可接受的状态。

当客户端调用此方法时，它不需要像服务器那样传递一个 Account 类的对象。客户端账户的所有数据都会被转换为其 JSON 表示形式。在服务器端，数据被转换为服务器 Account 类中的一个对象。这种转换之所以有效，是因为两个类使用了相同的 getter 和 setter 名称。尽管客户端账户的密码以明文形式（用户输入的那样）传入，但服务器端的密码在其 setter 方法中被哈希处理。这是因为我们只想在数据库中存储加密后的密码。

为了在传输过程中保护密码，我们需要在浏览器和应用服务器之间使用安全的传输协议，例如 TLS。如果我们假设应用服务器和 AccountService 都位于防火墙后的同一网络中，那么我们就不需要保护这两台服务器之间的传输。这就是为什么该服务可以通过非安全的（http:）URI 访问。

第二个方法 getAccount 接受一个 id 并从数据库中检索相应的账户。请看第 22 行：这里的路径被花括号包围。这意味着“期望一个变量 id 作为 URI 的一部分”。在方法签名中，我们引用路径的这一部分来将其值赋给参数。因为我们用 @GET 注解了此方法，所以它只对 HTTP GET 方法做出响应。

现在，如果我们保持路径不变但更改 HTTP 方法，我们就可以实现一个 DELETE 方法，如清单 33-3 所示。



###### 清单 33-3 AccountService（DELETE 方法节选）

```
01     @DELETE
02     @Path("{id}")
03     public void delete(@PathParam("id") String id) {
04       AccountFacade.getInstance().deleteAccount(id);
05     }
```

如果我们需要多个变量，只需将其添加到路径中即可。对于登录功能，相对路径包含字面量 `login` 以及用于登录名和密码的两个变量。如果用户能够通过身份验证，登录方法会返回该用户所属的所有用户组。这正是我们的自定义领域（realm）所需处理的内容。请参见清单 33-4。

###### 清单 33-4 AccountService（登录方法节选）

```
01     @GET
02     @Produces(MediaType.APPLICATION_JSON)
03     @Path("login/{loginName}/{password}")
04    public String login(@PathParam("loginName") String loginName,
05            @PathParam("password") String password) {
06      try {
07        Account account = AccountFacade.getInstance()
08                .findAccountByName(loginName);
09        if (!account.checkPassword(password)) {
10          return "";
11        }
12        return AccountFacade.getInstance().retrieveRoles(account.getId());
13      } catch (IllegalArgumentException ex) {
14        LOGGER.log(Level.INFO, ex.getMessage());
15      }
16      return "";
17    } 
```

## 测试

测试 Web 应用可能是一项挑战。通常，开发者会借助单元测试框架来测试代码单元，但 Web 应用的许多功能依赖于其他模块或其他 Java EE 技术。我们可以模拟（mock）掉应用的某些部分，以隔离待测试的单元。

有时我们无法模拟任何内容。如果我们需要容器提供的特性，那么使用简单的测试框架编写测试就会变得困难。除了用于自动化浏览器的 Selenium，我们还可以使用 Arquillian（[`arquillian.org`](http://arquillian.org)）。结合 ShrinkWrap，只有测试所需的应用部分会被打包成一个 Web 归档文件。Arquillian 会启动一个内嵌的应用服务器，并运行精简后的系统来执行测试。这种方法可以与单元测试框架结合使用。

幸运的是，测试一个独立的微服务并不需要如此大的工作量。除了对单个类进行单元测试，我们还可以编写一个小型 REST 客户端，并使用测试框架来调用我们服务的方法。实际上，这并非单元测试——而是集成测试。

清单 33-5 展示了我为 AccountService 编写的测试的一个简短节选。

###### 清单 33-5 AccountService 的集成测试（节选）

```
01     @Test
02     public void createAndDeleteAccount() {
03       AccountClient client = new AccountClient();
04       Account account = createAccount();
05       Response postJson = client.createAccount(account);
06       assertEquals(201, postJson.getStatus());

08       String path = postJson.getLocation().getPath();
09       String id = path.substring(path.lastIndexOf("/") + 1);
10       assertEquals("testclient", client.getAccount(id).getLoginName());
11       client.deleteAccount(id);
12       assertEquals(null, client.getAccount(id));
13     }
```

## 总结

Alumni 和自定义领域（realm）都使用了账户管理功能。为了避免领域对应用的依赖，我们将账户管理功能独立出来，形成自己的应用。这种专注于单一业务场景的应用被称为微服务。

Java EE 原生支持两种不同类型的 Web 服务：

*   WS* 服务

*   RESTful 服务

我们基于 JAX-RS 功能，将 AccountService 实现为一个独立的 RESTful 服务。JAX-RS 支持轻松访问客户端通过 HTTP 请求发送的信息。我们可以通过定义路径模式从 URI 中提取信息，或者访问通过头部（header）发送的数据——例如，POST 数据。

为了测试此服务，我们创建了一个合适的客户端，并使用单元测试框架来调用服务方法。

© Michael Müller 2018

Michael Müller, Practical JSF in Java EE 8 , `doi.org/10.1007/978-1-4842-3030-5_34`

