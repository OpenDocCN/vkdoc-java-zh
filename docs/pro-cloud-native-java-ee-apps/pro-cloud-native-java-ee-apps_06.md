# 6. 使用 Jakarta EE REST API 实现 REST

表述性状态转移（REST）架构是一种用于现代应用的无状态、基于 HTTP 的通信标准。该架构最初由 Roy Fielding 博士在其博士论文中提出^(¹⁰⁶)，它已成为在不同语言和框架编写的应用之间实现简单、无状态、可预测、快速且基于表述的通信的事实标准。例如，使用 Python 的 Django 框架编写的应用可以拥有 REST 资源，这些资源可以被另一个使用 Java 和 Jakarta EE 编写的应用消费。同样，Jakarta EE 应用也可以创建资源，供 C# 应用消费。

消费 REST 资源的客户端并不总是外部应用。一个由微服务集合构成的单一应用，也会使用 REST 架构作为不同服务之间相互通信的方式。作为一种与平台无关的通信手段，HTTP 协议允许 REST 资源的实现对客户端完全透明。

Jakarta RESTful Web Services 规范 ^(¹⁰⁷) 是在该平台上创建 REST Web 服务的 Jakarta EE 标准。该规范定义了一系列接口和注解，用于结合其他 Jakarta EE 规范（如 Jakarta Contexts and Dependency Injection）创建复杂的 Web 服务。

本章将向您介绍 Jakarta RESTful Web Services 规范。您将学习如何使用该规范中非常简单的 API 结构创建不同类型的 Web 资源，以及它如何与其他 Jakarta EE 规范集成，形成一个用于开发现代应用的统一平台。

本章还将介绍 Eclipse MicroProfile REST 客户端，用于以类型安全的方式消费 REST 资源。学完本章后，您将掌握在 Jakarta EE 上创建 Web 服务以及使用 Eclipse MicroProfile Client 的坚实基础。

## 建模 REST 服务

REST Web 服务之所以被称为资源，是因为它们代表了应用资源。所代表的实际资源是特定于领域的。设计 REST Web 服务以向消费客户端暴露资源，需要根据可以对给定资源执行的操作来设计 Web 资源。由于 REST 架构基于 HTTP，资源被暴露为 HTTP 路径。确定可以对资源执行哪些操作，可以明确这些操作可以在哪些 HTTP 路径上暴露。

一旦确定了操作集及其对应的 HTTP 路径，就需要考虑应用数据格式。由于 HTTP 与语言无关，它可以用于以多种格式暴露资源，包括 JSON、XML 和纯文本。这一点很重要，因为不同的客户端可能希望以不同的格式获取相同的资源。然而，资源格式的问题正变得越来越不重要，因为 JSON 格式已成为 Web 的通用语言。因此，它通常是 HTTP 上暴露资源的默认格式。尽管如此，资源格式的问题仍然值得考虑，特别是当资源暴露给不同的客户端时。

在考虑了应用路径和数据格式之后，下一步是将 HTTP 方法^(¹⁰⁸)映射到已识别的路径。这一步需要将正确的 HTTP 方法映射到可以对相关资源执行的每个相应操作。

### 用户资源

让我们为上一章关于 Jakarta Persistence 中详细讨论的 User 实体建模一个 REST 资源。对于我们的 User 资源，我们希望允许客户端创建、更新、按 ID 查找用户、获取所有用户、搜索用户、查找具有特定总转换阈值的所有用户，最后删除用户。表 6-1 显示了映射到 HTTP 路径及其对应 HTTP 方法的各种操作。

表 6-1

显示了 UserResource 的可能映射

| 操作 | HTTP 路径 | 返回类型 | HTTP 方法 |
| --- | --- | --- | --- |
| 创建用户 | /users | 创建的用户 | POST |
| 更新用户 | /users | 更新后的用户 | PUT |
| 按 ID 查找用户 | /users/<userId> | 用户或异常 | GET |
| 获取所有用户 | /users | 用户列表 | GET |
| 搜索用户 | /users/search | 用户列表 | GET |
| 按转换阈值查找用户 | /users/<threshold> | 用户列表 | GET |
| 删除用户 | /users | 空 | DELETE |

该表显示了可以对用户资源执行的操作与相应的 HTTP 路径和方法之间的映射。命名资源方法需要遵循通用的良好 REST 实践。^(¹⁰⁹) 由于资源是通过 HTTP 标准（其本身与语言或平台无关）消费的，因此以直观、简单且一致的方式命名资源非常重要。表 6-1 将作为本章剩余部分的讨论背景。

## Jakarta REST 资源

为了便于应用维护，REST 资源应以层次结构的方式组织。通常，应用会有一个基础路径，所有其他资源路径都相对于该路径进行访问。例如，来自 [`https://api.apilayer.com/currency_data`](https://api.apilayer.com/currency_data) 的货币转换 API 将所有货币转换资源方法都托管在相对于 currency_data 路径的位置。这可以称为 apilayer.com 货币转换 API 的根应用路径或根资源。

### 根资源

在 Jakarta REST 中，根资源被定义为 `jakarta.ws.rs.core.Application` 类的子类。清单 6-1 显示了 jwallet 应用的根资源声明。

```
@ApplicationPath("api")
public class RootResourceConfiguration extends Application {
}
清单 6-1
显示了根资源声明
```

根资源类被声明为 `Application` 类的子类。此类包含三个方法，可以重写它们以显式注册资源类、扩展和属性。大多数情况下，您不需要像清单 6-1 那样重写这些方法。

该类使用 `@ApplicationPath` 注解，并传递值 `"api"`。此注解定义了应用的根资源。应用中的所有资源都将通过基础路径或根路径 `/api` 进行访问。



### Jakarta REST 资源

Jakarta REST 中的 REST 资源被定义为一个 Java 类。该类将拥有一个与 HTTP 路径匹配的路径。然后，该类就被视为托管在该路径上。清单 6-2 介绍了 `UserResource`，用于实现表 6-1 中确定的各个资源。

```
@Consumes(MediaType.APPLICATION_JSON)
@Produces(MediaType.APPLICATION_JSON)
@Path("users")
public class UserResource {
@Inject
UserService userService;
}
清单 6-2
展示了第一个 UserResource 类
```

`UserResource` 类是一个带有多个 Jakarta REST 注解的 POJO。这些注解将这个 POJO 转换为一个 REST HTTP 资源，尽管我们尚未添加任何资源方法。`@Consumes` 和 `@Produces` 注解共同决定了资源类中的方法可以消费和产生的多用途互联网邮件扩展（MIME^(¹¹⁰)）媒体类型。由于 JSON 是 Web 和微服务应用程序中最流行的数据交换格式，因此常量 `MediaType.APPLICATION_JSON` 被传递给这两个注解。此常量是一个值为 `application/json` 的字符串。

`@Path` 注解被传递了值 `"users"`，指定该类托管在路径 `/api/users` 上。此注解接受一个值，该值表示被注解对象将托管在的 HTTP 资源路径。请记住，根路径或基本路径是通过 `RootResourceConfiguration` 上的 `@ApplicationPath` 设置为 `"api"` 的，如清单 6-1 所示。清单 6-2 中展示的声明在路径 `/api/users` 上创建了一个资源，当客户端访问该路径时，将导致 Jakarta REST 运行时实例化一个 `UserResource` 实例来处理该请求。

运行时能够将 `/users` 映射到托管在基本路径 `/api` 下，因为在 `RootResourceConfiguration` 中，`getClasses` 方法没有被重写。这意味着，默认情况下，所有资源类都将被扫描并作为子资源添加到这个根资源下。

### HTTP 方法

有了资源类，我们就可以开始实现表 6-1 中所示的 HTTP 方法了。Jakarta REST 中的资源方法是一个 Java 方法，它带有任何必要的注解，这些注解指明了该 Java 方法响应的 HTTP 方法。有七个注解映射到用于创建资源方法的相应 HTTP 方法。它们是 `@POST`、`@GET`、`@PUT`、`@DELETE`、`@PATCH`、`@HEAD` 和 `@OPTIONS`。

#### @POST

资源类方法上的 `@POST` 注解表示该方法响应 HTTP POST 请求。清单 6-3 展示了 `UserResource` 类中带有 `@POST` 注解的 `createUser` 方法。

```
@Consumes(MediaType.APPLICATION_JSON)
@Produces(MediaType.APPLICATION_JSON)
@Path("users")
public class UserResource {
@Inject
UserService userService;
@POST
public UserResponse createUser(CreateUserRequest createUserRequest) {
return userService.createUser(createUserRequest);
}
}
清单 6-3
展示了 @POST 方法
```

`createUser` 方法带有 `@POST` 注解，意味着它响应 HTTP POST 请求。在没有显式 `@Path` 注解的情况下，此方法将托管在 `/api/users` 路径上。对该路径的 POST 请求将导致运行时调用 `UserResource` 类实例上的 `createUser` 方法。到目前为止，一切都是带有少量注解的纯 Java。该方法消费一个强类型的 Java 类 `CreateUserRequest`。

类级别的 `@Consumes` 注解意味着 `createUser` 方法消费 JSON。所谓消费 JSON，是指客户端将发布该方法所消费的 Java 类型的 JSON 表示形式，在本例中为 `CreateUserRequest`。当运行时将客户端请求匹配到此方法时，请求体（本例中为 JSON 字符串）将被传递给一个称为消息体读取器的构件。

##### 消息体读取器

消息体读取器是参数化接口 `jakarta.ws.rs.ext.MessageBodyReader<T>` 的一个实例，它接收一个 `MediaType` 的 `InputStream` 并将其转换为 `T`，其中 `T` 是某种 Java 类型。每个消息体读取器都与一个单一的 `MediaType` 相关联，它可以将其 `InputStream` 转换为 Java 类型。运行时附带了许多用于流行 `MediaType` 的消息体读取器。除非您想自定义转换过程，否则无需提供 `MessageBodyReader` 的实例。

##### 消息体写入器

消息体写入器与消息体读取器相反，它接收一个 Java 类型并将其转换为与其关联的 `MediaType` 所表示的类型。消息体写入器接收 Java 类型实例并将其写入返回给客户端的输出流。与消息体读取器类似，通常只有在需要对过程进行一些自定义时，您才会提供自己的自定义写入器实现。`createUser` 方法返回的 `UserResponse` 对象同样会被转换为 JSON 并写入发送给客户端的响应输出流。

清单 6-4 中所示的 HTTP 请求将由自动配置的消息体读取器自动映射到 `CreateUserRequest` Java 类型。

```
POST http://localhost:3003/account/api/users
Content-Type: application/json
{
"firstName":"John",
"lastName":"Jakes",
"email":"a@b.com",
"userRegion":"AFRICA",
"birthDay":"08-02-1975",
"password":"_myStrong_pswd&^_I"
}
清单 6-4
展示了一个示例 POST 请求
```

请求体中的 JSON 将被映射到清单 6-5 中所示的 `CreateUserRequest`。

```
public class CreateUserRequest {
private String firstName;
private String lastName;
private String email;
private LocalDate birthDay;
private UserRegion userRegion;
private String password;
}
清单 6-5
展示了 CreateUserRequest
```

资源方法的实际实现完全是业务特定的。REST 运行时会将请求体转换为方法参数并交给您处理。如清单 6-3 中所声明的，如果客户端没有传递方法体或传递了空字符串，则方法参数将为 `null`。这意味着您必须对传递的值进行空值检查。为了防止这种情况，REST 规范与 Jakarta Bean Validation 规范集成，以允许声明式验证。



#### 验证资源

正如上一章关于使用 Jakarta Persistence 持久化数据所讨论的，Bean Validation API 是一组注解和接口，可用于将值约束为仅满足应用程序要求的值。为了防止客户端在创建用户时传递 null 或无效值，清单 6-6 展示了带有一些验证约束的 createUser 方法。

```
@Consumes(MediaType.APPLICATION_JSON)
@Produces(MediaType.APPLICATION_JSON)
@Path("users")
public class UserResource {
@Inject
UserService userService;
@POST
public UserResponse createUser(@NotNull @Valid  CreateUserRequest createUserRequest) {
return userService.createUser(createUserRequest);
}
}
清单 6-6
展示了 Bean 验证
```

这两个约束注解 `@NotNull` 和 `@Valid` 将导致方法参数被验证。它首先会验证转换后的 POST 请求体不为 null。然后，它会对转换后的对象进行递归验证。在这种情况下，`CreateUserRequest` 上的所有约束验证都将被验证。只有在没有验证异常时，方法体才会被执行。如果因违反约束而导致验证异常，REST 运行时会向客户端发送 HTTP 400（错误请求）。`CreateUserRequest` 及其验证约束如清单 6-7 所示。

```
public class CreateUserRequest {
@NotEmpty
private String firstName;
@NotEmpty
private String lastName;
@NotEmpty
@Email
private String email;
@NotNull
@Past
private LocalDate birthDay;
private UserRegion userRegion;
@NotEmpty
@Size(min = 8, max = 30)
private String password;
}
清单 6-7
展示了更新后的 CreateUserRequest
```

由于 `createUser` 方法没有像其类那样使用 `@Path` 注解显式声明任何资源路径，因此当向父路径 `/users`（由类上的 `@Path` 注解声明）发送 POST 请求时，运行时将知道调用它。在一个没有显式 `@Path` 注解的类中，每种 HTTP 方法只允许存在一个。如果我们在同一个 `UserResource` 类中声明另一个没有 `@Path` 注解的 `@POST` 方法，这将是一个错误。

向 `/users` 路径发送一个包含有效数据的 POST 请求示例，如清单 6-4 所示，将返回清单 6-8 中所示的 JSON 数据。

```
http://localhost:3003/account/api/users
HTTP/1.1 200 OK
Content-Type: application/json
Content-Language: en-US
Content-Length: 114
Date: Fri, 26 Aug 2022 17:08:13 GMT
{
"data": {
"firstName": "John",
"lastName": "Jakes",
"userId": 5
},
"response": {
"responseCode": "0",
"responseMessage": "OK"
}
}
清单 6-8
展示了成功调用 /users 资源
```

由 Jakarta REST 运行时生成的响应头包含有关响应的一些元数据。它告诉我们使用的 HTTP 版本、响应代码（在此次成功调用中为 200）。响应体中返回的 JSON 是 `createUser` 方法返回的 `UserResponse` 对象的表示。清单 6-9 展示了 `UserResponse` 对象。

```
public class UserResponse extends Response {
public static UserResponse of(UserResponseData data) {
UserResponse userResponse = new UserResponse();
userResponse.setData(data);
return userResponse;
}
@Getter
@Setter
public static class UserResponseData {
private Long userId;
private String firstName;
private String lastName;
private String username;
}
}
清单 6-9
展示了 UserResponse 类
```

使用清单 6-10 中所示的请求调用 `/users` 资源方法将导致 HTTP 响应代码 400，因为数据不正确。由于请求对象正在使用 `@Valid` 和 `@NotNull` 注解进行验证，运行时将自动验证请求并返回适当的响应。

```
POST http://localhost:3003/account/api/users
Content-Type: application/json
{
"firstName":"John",
"lastName":"Jakes",
"userRegion":"AFRICA",
"birthDay":"08-02-1975",
"password":"_myStrong_pswd&^_I"
}
清单 6-10
使用错误数据调用 /users
```

请求数据缺少 `email` 字段，该字段在 `CreateUserRequest` 中被注解为 `@NotEmpty`。上述调用导致如清单 6-11 所示的响应。

```
http://localhost:3003/account/api/users
HTTP/1.1 400 Bad Request
Content-Type: text/plain;charset=UTF-8
validation-exception: true
Content-Language: en-US
Content-Length: 60
Connection: Close
Date: Fri, 26 Aug 2022 17:35:41 GMT
清单 6-11
响应数据
```

这是自动完成的，只需要你使用相关的 Jakarta Bean Validation 注解来注解你的字段。POST 方法不一定需要返回数据。`createUser` 方法也可以返回 void。资源方法返回什么是一个业务领域决策，需要根据具体情况进行判断。

#### @GET

创建一个响应 HTTP GET 请求的方法需要用一个 `@GET` 注解来注解一个 Java 方法。在表 6-1 列出的资源方法列表中，有四个 GET 请求。其中三个返回用户集合。当不同的资源方法具有如此相似的返回类型时，这表明需要重构端点以简化它们。

再次查看表 6-1，搜索用户和按总转换次数筛选用户的端点可以合并为一个。因此，搜索端点支持按总货币转换次数筛选或搜索用户。这样，搜索端点可以扩展，以便你可以基于谓词（如大于或等于、小于或等于等）按转换次数进行筛选。采用这种新方法，我们最终得到三个 GET 请求：一个用于按 ID 获取用户，一个用于获取所有用户，另一个用于搜索。清单 6-12 展示了为 `UserResource` 构建 GET 请求的首次尝试。

```
@Consumes(MediaType.APPLICATION_JSON)
@Produces(MediaType.APPLICATION_JSON)
@Path("users")
public class UserResource{
@Inject
UserService userService;
@GET
@Path("{userId}")
public UserResponse getUser(@PathParam("userId") Long userId) {
return userService.getUser(userId);
}
@GET
@Path("search")
public List searchUsers(@NotNull @BeanParam UserSearchRequest userSearchRequest) {
return userService.searchUsers(userSearchRequest);
}
@GET
public List getAllUsers() {
return userService.getAllUsers();
}
}
清单 6-12
用户的 GET 请求
```



##### @PathParam

`getUser` 方法接受 `java.lang.Long` 类型参数。该参数使用 `@PathParam` 注解进行标注。传递给 `@PathParam` 注解的值与传递给该方法 `@Path` 注解的值相同。不过，`@Path` 注解的值被花括号括了起来。这是在 Jakarta REST 中捕获资源路径或路径段中变量的一种方式。`{userId}` 是一个变量，在运行时将被替换为实际传递的值。`@PathParam` 会获取该值并将其注入到 `userId` 方法参数中。路径 `/api/users/1` 将与此方法匹配。

路径参数不必只有一个。资源路径可以有 `/{param1}/{param2}/{param3}` 或任何类似的组合。请记住，如果你两次使用同一个变量，则最后一次匹配的结果会生效。因此，如果你有 `/{same}/{same}`，并且使用 `@PathParam("same")` 注入变量，那么对于路径 `/foo/bazz`，所有 `@PathParam("same")` 实例都将被注入值 `bazz`。在创建资源路径时请牢记这一点。`@Path` 注解还支持使用正则表达式来限制可以作为路径传递的字符集。方法参数还可以通过 `@NotNull` 进一步约束。

与 POST 方法类似，返回类型将自动编组为 JSON 表示形式，并以此形式返回给客户端。由于该类使用了 `@Produces("application/json")` 注解，所有返回类型都将自动转换为 JSON。

##### @BeanParam 和 @QueryParam

`searchUsers` 资源方法位于 `/api/users/search` 路径下。此方法接受一个 `UserSearchRequest` 对象，并返回一个 `UserResponses` 列表。该方法参数使用 `@BeanParam` 注解，该注解用于将资源参数聚合到被注解的对象中。清单 6-13 展示了被注解的 `UserSearchRequest` 类。

```
public class UserSearchRequest implements Serializable {
@QueryParam("firstName")
private String firstName;
@QueryParam("lastName")
private String lastName;
@QueryParam("totalConversions")
private BigDecimal totalConversions;
@QueryParam("conversionFilter")
private ConversionFilter conversionFilter = ConversionFilter.EQ;
@QueryParam("userType")
private UserType userType;
public enum ConversionFilter {
GT,
GTE,
LT,
LTE,
EQ,
NEQ
}
}
清单 6-13
UserSearchRequest
```

`UserSearchRequest` 类的字段使用了 `@QueryParam` 注解。此注解将传递的 HTTP 查询参数名称的值绑定到被注解的字段。它也可以像 `@PathParam` 一样用于方法参数。如清单 6-13 中所用，当方法参数使用 `@BeanParam` 注解时，运行时将获取搜索资源的所有查询参数，并将它们注入到该类的一个实例中，然后将该实例传递给方法。

例如，HTTP GET 请求 `/api/users/search?firstName=Joe&conversionFilter=GTE` 将导致创建一个 `UserSearchRequest` 实例，并将 `firstName` 字段设置为 `Joe`，`conversionFilter` 字段设置为 `GTE`。`@BeanParam` 是一个用于创建搜索类型资源的非常强大的注解，因为你可以将所有可能的查询参数抽象到一个对象中，就像我们在 `searchUsers` 方法中所做的那样，而不必使用许多方法参数。

在简单情况下，这个搜索资源应该足够了。但是，当返回一个列表时，数据集可能非常大，与其一次性全部返回，不如对客户端和服务器都更高效地进行分页。分页要求客户端能够传递一个限制（即每页返回的最大数据量）和一个偏移量。返回的信息还应包含关于搜索数据集的一些元数据，例如集合中的元素总数。清单 6-14 展示了更新后的 `UserSearchRequest`，它为客户提供了分页选项。

```
public class UserSearchRequest implements Serializable {
@QueryParam("firstName")
private String firstName;
@QueryParam("lastName")
private String lastName;
@QueryParam("totalConversions")
private BigDecimal totalConversions;
@QueryParam("conversionFilter")
private ConversionFilter conversionFilter = ConversionFilter.EQ;
@QueryParam("userType")
private UserType userType;
@QueryParam("limit")
@DefaultValue("150")
@Positive
private Integer limit;
@QueryParam("offset")
@DefaultValue("0")
private Integer offset;
public enum ConversionFilter {
GT,
GTE,
LT,
LTE,
EQ,
NEQ
}
}
清单 6-14
更新后的 UserSearchRequest
```



##### @DefaultValue

`limit` 和 `offset` 字段也是查询参数，我们期望从请求中获取它们并注入到相应字段中。两个字段上的 `@DefaultValue` 在客户端未设置任何显式值时，会设置一个默认值。这意味着，如果没有将 `limit` 和 `offset` 值作为查询参数传递，实例化的类实例会将 `limit` 字段设置为 150，`offset` 字段设置为 0。`limit` 字段还标注了 `@Positive`，这是一个 Bean 验证约束，用于将该字段的可能值限制为正整数。

我们重构了 `UserSearchRequest`，但完全没有改动资源方法中的任何内容。这就是我们建议在处理查询参数时使用 `@BeanParam` 注解的原因。创建分页选项后，我们现在可以重构返回类型，使其也包含一些客户端在创建有意义的分页请求时使用的元数据。清单 6-15 展示了一个带有元数据的新参数化类 `SearchResult`。

```
public class SearchResult implements Serializable {
private List searchResult = new ArrayList();
private Long totalSize;
private Long offset;
private Long limit;
}
清单 6-15
SearchResult
```

`SearchResult` 接受一个类型，并将该类型绑定到 `searchResult` 列表。该列表将包含实际的搜索结果。`totalSize` 字段指匹配搜索请求的总大小。`offset` 和 `limit` 仅返回客户端传递的值（如果有的话）。利用这个新结构，我们可以重构 `searchUsers` 资源方法，使其返回一个类型为 `UserResponse` 的 `SearchResult` 对象。清单 6-16 展示了更新后的资源方法。

```
@GET
@Path("search")
public SearchResult searchUsers(@NotNull @BeanParam UserSearchRequest userSearchRequest) {
return userService.searchUsers(userSearchRequest);
}
清单 6-16
更新后的 searchUsers 资源方法
```

清单 6-17 展示了 `UserRepository` 中使用 Jakarta Persistence 规范的 Criteria API 的完整搜索实现。正如前一章所讨论的，Criteria API 更适合此类应用搜索场景。

```
public SearchResult searchUsers(final UserSearchRequest searchRequest) {
CriteriaBuilder criteriaBuilder = em.getCriteriaBuilder();
CriteriaQuery query = criteriaBuilder.createQuery(User.class);
Root from = query.from(User.class);
final Set predicateSet = new HashSet();
if (searchRequest.getFirstName() != null) {
Predicate firstName = criteriaBuilder.like(from.get("firstName"), "%" + searchRequest.getFirstName() + "%");
predicateSet.add(firstName);
}
if (searchRequest.getLastName() != null) {
Predicate lastName = criteriaBuilder.like(from.get("lastName"), "%" + searchRequest.getLastName() + "%");
predicateSet.add(lastName);
}
if (searchRequest.getTotalConversions() != null) {
if (searchRequest.getConversionFilter() == UserSearchRequest.ConversionFilter.EQ) {
predicateSet.add(criteriaBuilder.equal(from.get("totalConversions"),
searchRequest.getTotalConversions()));
}
if (searchRequest.getConversionFilter() == UserSearchRequest.ConversionFilter.GT) {
predicateSet.add(criteriaBuilder.greaterThan(from.get("totalConversions"),
searchRequest.getTotalConversions()));
}
if (searchRequest.getConversionFilter() == UserSearchRequest.ConversionFilter.GTE) {
predicateSet.add(criteriaBuilder.greaterThanOrEqualTo(from.get("totalConversions"),
searchRequest.getTotalConversions()));
}
if (searchRequest.getConversionFilter() == UserSearchRequest.ConversionFilter.LT) {
predicateSet.add(criteriaBuilder.lessThan(from.get("totalConversions"),
searchRequest.getTotalConversions()));
}
if (searchRequest.getConversionFilter() == UserSearchRequest.ConversionFilter.LTE) {
predicateSet.add(criteriaBuilder.lessThanOrEqualTo(from.get("totalConversions"),
searchRequest.getTotalConversions()));
}
if (searchRequest.getConversionFilter() == UserSearchRequest.ConversionFilter.NEQ) {
predicateSet.add(criteriaBuilder.notEqual(from.get("totalConversions"),
searchRequest.getTotalConversions()));
}
}
if (searchRequest.getUserType() != null) {
Predicate userType = criteriaBuilder.equal(from.get("userType"), searchRequest.getUserType());
predicateSet.add(userType);
}
CriteriaQuery where = query.select(from).where(predicateSet.toArray(new Predicate[] {}));
TypedQuery typedQuery = em.createQuery(where);
if (searchRequest.getOffset() != null) {
typedQuery = typedQuery.setFirstResult(searchRequest.getOffset());
}
if (searchRequest.getLimit() != null) {
typedQuery = typedQuery.setMaxResults(searchRequest.getLimit());
}
List response =
typedQuery.getResultList().stream().map(UserService::toUserResponse).collect(Collectors.toList());
SearchResult searchResult = new SearchResult();
searchResult.setSearchResult(response);
CriteriaQuery countQuery = criteriaBuilder.createQuery(Long.class);
Root userCountRoot = countQuery.from(User.class);
countQuery.select(criteriaBuilder.countDistinct(userCountRoot.get("id")))
.where(predicateSet.toArray(new Predicate[] {}));
Long count = em.createQuery(countQuery).getSingleResult();
searchResult.setTotalSize(count);
searchResult.setLimit(Long.valueOf(searchRequest.getLimit()));
searchResult.setOffset(searchResult.getOffset());
return searchResult;
}
清单 6-17
使用 Criteria API 的搜索用户实现
```

正如前一章所讨论的，Criteria API 可能非常冗长且难以处理。然而，它是处理这类数据库查询的最佳工具。通常，你会将搜索实现查询分解为更小的方法。此处为了便于参考，将它们作为一个整体呈现。

实现了包含分页功能的搜索方法后，我们需要对最后一个 GET 方法做出架构决策。该方法仅返回系统中的所有用户。但我们真的需要它吗？由于搜索资源方法也返回类似的结果，我们可以决定移除最后一个 GET 方法，因为它几乎是多余的。你可以搜索用户集合，这将返回一个列表。拥有一个返回所有用户的端点也需要分页以获得更好的性能。由于这已在 `searchUsers` 端点中实现，从架构角度来看，我们可以省略最终的 GET 方法。



#### @PUT

@PUT 方法用于创建或更新资源。通常的模式是使用 @POST 进行创建，使用 @PUT 进行更新。我们将遵循这一约定。因此，@PUT 用于更新用户。清单 6-18 展示了 UserResource 类中的 @PUT 方法。

```
@Consumes(MediaType.APPLICATION_JSON)
@Produces(MediaType.APPLICATION_JSON)
@Path("users")
public class UserResource {
@Inject
UserService userService;
@PUT
public UserResponse updateUser(@NotNull UpdateUserRequest updateUserRequest) {
return       userService.updateUser(updateUserRequest);
}
}
清单 6-18
@PUT 方法
```

由于此方法没有显式声明任何 @Path 参数，它将托管在根路径 /users 下。对 /api/users 的 HTTP PUT 请求将匹配到此方法。方法参数使用了 @NotNull 注解，因为我们不希望客户端传递 null。但实际的 UpdateUserRequest 并未声明任何约束验证。清单 6-19 展示了 UpdateUserRequest。

```
public class UpdateUserRequest implements Serializable {
private Long userId;
private String firstName;
private String lastName;
@JsonbDateFormat("dd-MM-yyyy")
private LocalDate birthDay;
private CreateUserRequest.UserRegion userRegion;
}
清单 6-19
UpdateUserRequest
```

该类仅允许更新用户的选定字段。这是因为在应用程序开发中，你经常会遇到一些实体字段会影响应用程序的其他许多部分。例如，用户电子邮件可能链接到用户拥有的其他工件。允许临时更新此类字段可能会导致数据损坏。这些核心字段通常通过更复杂的方式更新，例如通过使用业务流程。这样，该字段被使用的所有其他位置都会一次性得到更新。这就是为什么从架构和业务角度来看，UpdateUserRequest 只允许更新选定字段。

birthDay 字段上的 @JsonbDateFormat 注解用于设置传入值的日期格式。此注解来自 Jakarta JSON Binding 规范^(¹¹¹)，这是一个用于在 JSON 和 Java 之间进行转换的 API。该规范是将响应类型编组为 JSON 的默认底层提供程序。该注解接受与 java.time 包中的 DateTimeFormatter 相同的日期格式。

#### @DELETE

@DELETE 方法用于创建一个响应 HTTP DELETE 方法的资源方法。与更新某些字段类似，删除资源是一个非常严肃的操作，必须以可控且经过深思熟虑的方式进行。在企业应用程序中，删除资源需要一个业务流程，以确保被删除资源所使用到的所有位置都得到妥善处理。然而，为了完整性起见，清单 6-20 展示了一个 @DELETE 方法。

```
@DELETE
public void deleteUser(@NotNull @Positive Long userId) {
userService.removeUser(userId);
}
清单 6-20
@DELETE 方法
```

### 响应对象

到目前为止看到的资源方法都直接返回 Java 类型，然后这些类型被编组为 JSON。可以使用 `jakarta.ws.rs.core.Response` 对象向客户端提供自定义元数据。清单 6-21 展示了重构后的 createUser 资源方法，它返回一个包含新创建实体路径的自定义 Response 对象。

```
@POST
public Response createUser(@NotNull @Valid CreateUserRequest createUserRequest, @Context UriInfo uriInfo) {
UserResponse user = userService.createUser(createUserRequest);
URI uri = uriInfo.getAbsolutePathBuilder()
.path(user.getData().getUserId().toString())
.build();
return Response.created(uri).entity(user).build();
}
清单 6-21
返回 Response 对象
```

createUser 方法现在接受两个参数。第二个参数是 UriInfo，通过使用 @Context 注解注入到变量 uriInfo 中。此注解用于将 Jakarta REST 工件注入到字段和方法参数中。UriInfo 接口包含用于获取当前请求 URI 信息的方法。createUser 实现首先保存传入的用户，然后构建指向新创建用户位置的路径。

回想一下表 6-1，我们将通过 ID 获取用户的路径设置为 /api/users/{userId}。URI info 实例调用 getAbsolutePathBuilder 方法，该方法返回当前资源类的完整路径，然后将新创建的用户 ID 附加到该路径上。清单 6-22 展示了当发出如清单 6-4 所示的 POST 请求时返回的响应。

```
http://localhost:3003/account/api/users
HTTP/1.1 201 Created
Content-Type: application/json
Location: http://localhost:3003/account/api/users/1
Content-Language: en-US
Content-Length: 114
Date: Sat, 27 Aug 2022 00:07:22 GMT
{
"data": {
"firstName": "John",
"lastName": "Jakes",
"userId": 1
},
"response": {
"responseCode": "0",
"responseMessage": "OK"
}
}
清单 6-22
返回的自定义 Response 对象
```

Location 头字段被设置为指向新创建用户的完整 URI。然后客户端可以导航该路径，从资源类加载新创建的用户。同样值得注意的是，在清单 6-21 中，当 createUser 资源方法返回 UserResponse 时，HTTP 响应码是 200。但在清单 6-22 中，当返回 Response 对象时，HTTP 响应码是 201。Response 类可用于创建返回给客户端的各种形式的自定义响应数据。



## 异常映射器

异常映射是 Jakarta REST 中用于将异常映射到客户端响应的一种机制。业务异常是向客户端发出信号，表明应用程序的正常执行出现了问题。向客户端传达异常消息的方式是通过使用异常映射器。当在清单 6-10 中发出 POST 请求以创建用户时，服务器以 HTTP 状态码 400 响应，告知客户端请求有误。这条消息帮助不大。更好的体验是准确告知客户端传递的数据具体哪里有问题。这可以通过使用异常映射器来实现。

异常映射器是 `jakarta.ws.rs.ext.ExceptionMapper` 接口的一个实现。该接口接受一个 `Throwable` 类型，并在 `toResponse` 方法中将其映射到一个 `Response` 对象。清单 6-23 展示了一个用于向客户端返回验证约束违反消息的异常映射器。

```
@Provider
public class ConstraintViolationExceptionMapper implements ExceptionMapper<ConstraintViolationException> {
    @Override
    public Response toResponse(ConstraintViolationException exception) {
        StringBuilder builder = new StringBuilder();
        for (ConstraintViolation<?> cv : exception.getConstraintViolations()) {
            String path = cv.getPropertyPath().toString().split("\\.")[2];
            builder.append(path).append(" - ")
                   .append(cv.getMessage()).append("\n");
        }
        return  Response.status(Response.Status.BAD_REQUEST)
                .entity(builder.toString())
                .build();
    }
}
清单 6-23
异常映射器
```

`ConstraintViolationExceptionMapper` 将 `jakarta.validation.ConstraintViolationException` 异常映射到一个 `Response` 对象。当发生约束违反时，会抛出 `ConstraintViolationException`，其中包含一组发生的约束。每个约束对象都包含发生违反的字段名称。`toResponse` 方法将这些信息映射成一个字符串，并将其设置为 `Response` 的实体。类上的 `@jakarta.ws.rs.ext.Provider` 注解将其注册为一个 Jakarta REST 构件，运行时将自动扫描并注册它。清单 6-24 展示了向 `createUser` 资源方法发送格式错误数据的调用响应。

```
HTTP/1.1 400 Bad Request
Content-Type: application/json
Content-Language: en-US
Content-Length: 55
Connection: Close
Date: Sat, 27 Aug 2022 00:46:22 GMT
lastName - must not be empty
email - must not be empty
清单 6-24
映射后的响应
```

该请求省略了 `CreateUserRequest` 对象的 `email` 和 `lastName` 字段。这导致了验证异常的发生，并且 `ConstraintViolationException` 通过我们注册的异常映射器被映射给了客户端。响应体显示了每个违反的字段以及相应的帮助信息。

## Jakarta REST 客户端

Jakarta REST 附带了一个用于发起 REST 调用的 HTTP 客户端。这个客户端是一个非常简单的结构，围绕 `jakarta.ws.rs.client.WebTarget` 接口展开。`WebTarget` 通过其 URI 定位一个资源。通过调用 `jakarta.ws.rs.client.ClientBuilder` 类返回的 `jakarta.ws.rs.client.Client` 实例上的静态 `target` 方法，可以获得一个 `WebTarget`。清单 6-25 展示了在 account 模块的测试类中构建一个 `Client` 并使用它来定位资源以获取 `WebTarget` 的过程。

```
public class AbstractAccountIT {
    protected static final WebTarget target;
    protected static final Client client;
    static {
        client = ClientBuilder.newClient();
        target = client.target("http://localhost:3003/account/api");
    }
    @AfterAll
    protected static void cleanUp() {
        if (client != null) {
            client.close();
        }
    }
}
清单 6-25
获取一个 Client
```

`ClientBuilder.newClient` 方法返回一个 `Client`。`Client` 是重量级对象，因此不应创建超过必要数量的实例。在前面的例子中，返回的实例被赋值给静态变量 `client`。然后调用 `target` 方法，传入要定位的完整基础资源路径，返回一个 `WebTarget` 实例，该实例被赋值给 `target` 变量。实例化是在一个静态块中完成的，因为 `WebTarget` 和 `Client` 实例是静态的。仅此初始化就足以在所有调用中使用 `target`。

一切完成后，应关闭 `Client` 以防止资源泄漏。根据 API 文档，“`Client` 是管理客户端通信基础设施的重量级对象。`Client` 实例的初始化和销毁可能是一项相当昂贵的操作。”因此，我们在 JUnit 测试套件中带有 `@AfterAll` 注解的 `cleanUp` 方法中，在所有测试运行完毕后关闭 `Client` 实例。

初始化 `WebTarget` 后，可以通过指定目标资源的相对路径来发起 HTTP REST 调用。清单 6-26 分别展示了向 `/users` 和 `/users/{userId}` 资源路径发起 POST 和 GET 请求。

```
@Test
void createUser() {
    UserResponse createdUser = target.path("users")
            .request().post(Entity.json(createUserRequest), UserResponse.class);
    assertEquals("0", createdUser.getResponse().getResponseCode());
    assertEquals("OK", createdUser.getResponse().getResponseMessage());
    // get created user
    UserResponse userResponse = target.path("users/{userId}")
            .resolveTemplate("userId", createdUser.getData().getUserId()).request()
            .get(UserResponse.class);
    assertEquals("0", userResponse.getResponse().getResponseCode());
    assertEquals("OK", userResponse.getResponse().getResponseMessage());
}
清单 6-26
使用客户端发起调用
```

`WebTarget` 上的 `path` 方法返回另一个 `WebTarget` 实例，该实例解析为传递给 `client.target` 调用的原始路径（在清单 6-25 中）和这个新路径的组合。因此，清单 6-26 中的 `target.path("users")` 调用将返回的 `WebTarget` 解析为 `http://localhost:3003/account/api/users`。然后，在隐式返回的 `WebTarget` 实例上调用 `request` 方法，返回一个 `jakarta.ws.rs.client.SyncInvoker` 实例。该接口包含所有可以调用的 HTTP 方法，用于发起实际的请求。

清单 6-26 中调用的 `post` 方法传入了一个 `CreateUserRequest` 实例。该实例使用 `jakarta.ws.rs.client.Entity` 类上的静态 `json` 方法转换为 JSON。传递给 `post` 方法的第二个参数是返回的响应应被转换成的类类型。根据表 6-1 中显示的资源路径，对 `/api/users` 资源的 POST 请求会返回创建的用户。`post` 调用的响应直接赋值给一个 `UserResponse` 变量，因为运行时会自动读取返回的实体并将其从 JSON 转换为具体的 Java 类型。

第二个调用是对 `/api/users/{userId}` 端点的 GET 调用。该端点接受一个路径参数。`path` 方法传入包含变量名的原始路径，然后通过 `resolveTemplate` 方法将其解析为用户的 ID。该方法接受要解析的变量名和要解析成的值。然后调用 `get` 方法发起 HTTP GET 请求。返回的响应直接赋值给一个 Java 类型，因为 `get` 方法传入了一个类类型，响应的主体应被转换为该类型。

Jakarta REST 客户端是一个功能丰富的 API，用于向 Web 资源发起各种形式的 HTTP 调用。你可以链式调用 `path` 方法并解析变量来创建复杂的资源路径。你也可以获取调用返回的原始 `jakarta.ws.rs.core.Response` 对象，以便在代码中使用。



## MicroProfile REST 客户端

Eclipse MicroProfile REST 客户端规范^(¹¹²)提供了另一种以更类型安全的方式进行 REST 调用的方法。尽管上一节介绍的 Jakarta REST 客户端能够完成任务，但有时会显得冗长且笨拙。MicroProfile REST 客户端是另一种在 Java 中消费 HTTP 资源的方式，无需处理创建客户端对象的底层工作。清单 6-27 展示了一个完整的 MicroProfile REST 客户端声明，用于对外调用 apilayer.com 的货币转换服务。

```
@RegisterRestClient(baseUri = BASE_URI)
@ClientHeaderParam(name = "apikey", value = "INSERT_API_KEY_FROM_SERVICE")
@Path("exchangerates_data")
public interface ApiLayer {
String NAME = "ApiLayer";
String BASE_URI = "https://api.apilayer.com";
@GET
@Path("convert")
ApiLayerConvertResponse convertCurrency(@BeanParam ApiLayerConvertRequest apiLayerConvertRequest);
@GET
@Path("list")
JsonObject getCurrencySymbols();
}
清单 6-27
MicroProfile 客户端
```

创建 REST 客户端需要描述要访问的资源。`ApiLayer` 接口声明了两个 HTTP GET 方法。第一个方法接受一个 `ApiLayerConvertRequest` 对象，该对象使用 `@BeanParam` 注解，其中包含了 apilayer.com 货币服务所需的查询参数。这两个方法分别返回 `ApiLayerConvertResponse` 和 `JsonObject`。第一个返回类型是特定于应用的对象，第二个 `JsonObject` 是 Jakarta JSON Processing API 的一部分。

该接口使用 `@RegisterRestClient(baseUri = BASE_URI)` 注解。这个注解是一个 MicroProfile REST 客户端原型，将此接口标记为针对 `baseUri` 的 REST 客户端。使用此客户端的所有调用都将相对于 `baseUri` 传递的值进行，在此示例中为 [`https://api.apilayer.com`](https://api.apilayer.com)。`@ClientHeaderParam(name = “apikey”, value = “API_KEY”)` 用于向出站请求添加 HTTP 头信息。由于在接口级别使用，通过此客户端发出的每个出站请求都将包含来自此注解的键值对作为头条目。对于前面的示例，每个请求的请求头中都将包含 `apiKey=“YOUR_API_KEY_FROM_API_LAYER”`。

`@Path` 注解用于指定相对路径，在此客户端上调用的方法将相对于该路径进行。因此，在此客户端实例上调用的所有方法都将相对于 [`https://api.apilayer.com/exchangerates_data`](https://api.apilayer.com/exchangerates_data) 进行。第一个 GET 方法将针对 [`https://api.apilayer.com/exchangerates_data/convert`](https://api.apilayer.com/exchangerates_data) 调用，第二个方法将针对 [`https://api.apilayer.com/exchangerates_data/list`](https://api.apilayer.com/exchangerates_data) 调用。声明好客户端后，使用它就像使用 `@Inject` 和 MicroProfile 特定的限定符 `@RestClient` 注入它一样简单。清单 6-28 展示了在 `RateService` bean 中使用 `ApiLayer` REST 客户端的情况。

```
@ApplicationScoped
public class RateService {
@Inject
@RestClient
ApiLayer apiLayer;
private ConvertCurrencyResponse convert(final ConvertCurrencyRequest currencyRequest) {
ApiLayerConvertRequest request = new ApiLayerConvertRequest();
request.setFromCurrency(currencyRequest.getSourceCurrency());
request.setToCurrency(currencyRequest.getTargetCurrency());
request.setAmount(currencyRequest.getAmount().toPlainString());
ApiLayerConvertResponse response = apiLayer.convertCurrency(request);
ConvertCurrencyResponseData data = new ConvertCurrencyResponseData();
data.setCurrency(currencyRequest.getTargetCurrency());
data.setAmount(response.getResult());
data.setSource(ApiLayer.NAME);
return ConvertCurrencyResponse.of(data);
}
}
清单 6-28
REST 客户端的使用
```

要使用 REST 客户端，我们使用 `@Inject` 注入它，并使用 MicroProfile REST 客户端 API 中的 `@RestClient` CDI 限定符对其进行限定。接口的实现将在运行时由底层的 REST 客户端实现提供。`convert` 方法像调用任何其他 Java 实例一样，在注入的客户端上调用 `convertCurrency` 方法。该实现将负责创建实际的 HTTP 请求、调用目标资源、获取响应并将其转换为声明的 Java 类型。`ApiLayerConvertRequest` 和 `ApiLayerConvertResponse` 类分别如清单 6-29 和 6-30 所示。

```
public class ApiLayerConvertResponse {
private boolean success;
private Info info;
private LocalDate date;
private BigDecimal result;
@Getter
@Setter
public static class Info {
private Long timestamp;
private BigDecimal rate;
}
}
清单 6-30
ApiLayerConvertResponse
```

```
public class ApiLayerConvertRequest {
@QueryParam("from")
private String fromCurrency;
@QueryParam("to")
private String toCurrency;
@QueryParam("amount")
private String amount;
}
清单 6-29
ApiLayerConvertRequest
```

`ApiLayerConvertResponse` 是端点返回的 JSON 的 Java 表示。REST 客户端运行时负责自动将返回的 JSON 编组为 Java 表示。清单 6-31 显示了由 [`https://api.apilayer.com/exchangerates_data/convert`](https://api.apilayer.com/exchangerates_data) 端点返回的示例 JSON。

```
{
"date": "2005-01-01",
"historical": true,
"info": {
"quote": 0.51961,
"timestamp": 1104623999
},
"query": {
"amount": 10,
"from": "USD",
"to": "GBP"
},
"result": 5.1961,
"success": true
}
清单 6-31
示例返回数据
```

`ApiLayer` 接口中的 `getCurrencySymbols` 端点返回一个 `JsonObject`。对于此端点，我们希望直接获取 JSON 数据以进行进一步处理。当在客户端实例上调用该方法时，REST 客户端运行时会将响应的 JSON 字符串表示转换为 `JsonObject`。MicroProfile REST 客户端 API 是在 Jakarta EE 应用程序中消费 REST 资源的一种强大、直观且类型安全得多的方式。

## 总结

本章探讨了使用 Jakarta REST API 创建 RESTful Web 服务。我们研究了将领域资源建模为 HTTP 资源路径，接着使用 API 中的各种构造来创建这些资源，并学习了如何使用客户端 API 进行 REST 调用。我们还研究了 Eclipse MicroProfile REST 客户端，并看到了使用该 API 消费 REST 资源是多么简单。您可以在相应的 Jakarta REST 和 Eclipse MicroProfile REST 客户端规范中找到更多高级主题。它们都使用易于理解的语言编写，应作为您掌握高级用法的权威知识来源。

脚注 1   2   3   4   5   6   7



