# 11. 使用 JSON Web 令牌进行安全认证

传统上，Web 应用的安全性是通过在 HTTP 会话中存储已登录用户的数据来实现的。然而，由于微服务天生是无状态的，这种方法并不适用于微服务架构。为了解决安全问题，MicroProfile 提供了对 JSON Web 令牌（JWT）的支持。

JSON Web 令牌是一种用于在微服务中实现无状态安全机制的机制。

JWT 是一种基于 JSON 的文本格式，用于在系统之间交换信息。JWT 是一个开放标准，在 RFC 7519 中进行了规定。JWT 的信息封装在*声明*中，这些声明本质上是键值对。MicroProfile 要求 JWT 必须使用 RSA-SHA256 算法进行数字签名。

JWT 可以包含任意声明，所有声明都是可选的；不过，MicroProfile JWT 规范要求包含特定的声明。

| **JWT 声明** | **描述** |
| --- | --- |
| iss | 令牌签发者 |
| at | 令牌签发时间，以长整型值表示，代表从 UTC 时间 1970 年 1 月 1 日到签发时间之间的秒数 |
| exp | 令牌过期时间，以长整型值表示，代表从 UTC 时间 1970 年 1 月 1 日到过期时间之间的秒数 |
| upn | 已登录主体的用户名 |

除了必需的声明外，MicroProfile JWT 规范还建议 JWT 中包含以下声明：

| **JWT 声明** | **描述** |
| --- | --- |
| jti | JWT 的唯一标识符 |
| aud | 标识 JWT 的目标接收者，对受众值的解释因应用而异 |
| groups | 用户所属的任何安全组（也称为角色） |

## 获取令牌

JWT 通常从某种身份服务器获取；当部署到云端时，大多数云提供商都提供了一种获取 JWT 以在其服务器上使用的方法。如果在内部部署应用，JWT 通常通过内部部署的身份服务器工具获取，例如，像开源的 Keycloak 工具，或者通过商业身份服务器（如 Okta 或类似产品）获取。

出于测试和学习目的，这些工具有点大材小用；幸运的是，有一位多产的 Java 顾问 Adam Bien 开发了一个开源工具，名为 *jwtenizr*，我们可以用它来快速轻松地生成用于测试的 JWT。

可以从 [*http://jwtenizr.sh/*](http://jwtenizr.sh/) 下载 jwtenizr；它是一个自包含的可执行 JAR 文件；我们可以通过在命令行中执行以下命令来生成一个令牌：

```
java -jar jwtenizr.jar
```

上述命令将生成类似于以下的输出：

```
使用 java -Dverbose -jar jwtenizr.jar [可选：为生成的 curl 提供 URI] 启用详细输出
生成的令牌 token.jwt 包含从 jwt-token.json 加载的信息
调整 groups[] 以配置角色，并在 jwt-token.json 中更改 upn 以更改主体，然后重新执行 JWTenizr
jwt-token.json 中的 iss 必须与 microprofile-config.properties 中的 mp.jwt.verify.issuer 对应
将 microprofile-config.properties 复制到您的 WAR/src/main/resources/META-INF
使用以下命令进行测试：
curl -i -H'Authorization: Bearer eyJraWQiOiJqd3Qua2V5IiwidHlwIjoiSldUIiwiYWxnIjoiUlMyNTYifQ.eyJzdWIiOiJkdWtlIiwidXBuIjoiZHVrZSIsImF1dGhfdGltZSI6MTYzOTQwODk4NywiaXNzIjoiYWlyaGFja3MiLCJncm91cHMiOlsiY2hpZWYiLCJoYWNrZXIiXSwiZXhwIjoxNjM5NDA5OTg3LCJpYXQiOjE2Mzk0MDg5ODcsImp0aSI6IjQyIn0.GnSwzNSO7EfKRD-ANqYhaVQuY8HIRdp29r-8K3lqpjCb2bByRmkdqTl9HPW4ePfqe0K7wpr_1Nfir12CPSb9e4i1PWw-qZxf9pPpEIeLRVB-_8p_n6FaTCkt4uSB12fcBMaLh2PZGOAnK5uWjAY1V-noD-KxmX_BsZnhSOssMa-0FnmevXlYPcRcbauVUxlTOACzFwdxpcRSq_Ms9QMh_5TctQZtq59VTnpOnfOZBIHd3eS0zB6AHqqNNtioPW8syX6iZxweSQkBX0pmYBaQxC2iUExhUOTmzEnDeBjaJ5EcdvAZ0-98w2GIqnZrftBlQvtWt25MKmNIl_NAtmBKZQ' http://localhost:8080
[10:23:07:548]JWT 已生成
```

请注意，该工具的输出包含如何使用 *curl* 调用安全服务的示例用法，这在测试我们的服务时非常方便。

jwtenizr 会生成多个文件：

*   **jwtenizr-config.json**：一个 JSON 文件，我们可以用它来配置 jwtenizr，可用于自定义 JWT 签发者和生成的示例 MicroProfile 配置文件的位置；它将在后续执行 jwtenizr 时被读取。

*   **jwt-token.json**：生成的 JWT 的明文版本。

*   **microprofile-config.properties**：示例的 microprofile-config.properties 文件，我们可以用它来配置我们的安全应用。

*   **token.jwt**：生成的 JWT 的 Base 64 编码版本。

尽管 JWT 的可配置性相当高，但其默认设置足以满足我们的需求。默认情况下，jwtenizr 会为一个名为“duke”的用户生成一个令牌，该用户属于“chief”和“hacker”组，由“airhacks”签发，并在令牌创建后的 16 分 40 秒（1000 秒）后过期。

## 使用 JSON Web 令牌保护微服务

保护我们的 RESTful Web 服务主要通过几个简单的 MicroProfile Config 属性和一些注解来完成。

### MicroProfile Config JWT 属性

如前所述，JSON Web 令牌是经过数字签名的；这是通过使用签发者的私钥加密令牌来实现的。要验证令牌的有效性，我们需要使用签发者的公钥对其进行解密。

MicroProfile JWT 提供了一个标准的配置属性，我们可以用它来指定公钥；该属性的名称是 `mp.jwt.verify.publickey`；其值必须是 Base 64 编码的公钥；该值通常由我们使用的任何身份服务提供。在我们的示例中，该值出现在 jwtenizr 生成的示例 MicroProfile 配置文件中。

或者，我们可以将公钥放在一个文本文件中，然后将该文本文件发布到 URL 或将其放在 JAR 文件内部；在一个典型的 Maven 项目中，包含公钥的文件将放在 *src/main/resources* 或其子目录下。

例如，如果我们将密钥放在 *src/main/resources/keys/publicKey.pem* 下，那么 `mp.jwt.verify.publickey` 属性的值将是 `/keys/publicKey.pem`；相反，如果我们将公钥直接放在 *src/main/resources* 中，那么相应的属性值将是 `/publicKey.pem`。

除了指定公钥，我们还需要指定令牌的签发者；这在我们使用的 JWT 的 *iss* 声明中指定，并且可以从我们用于身份验证的明文 JSON Web 令牌中获取。jwtenizr 使用 *airhacks* 作为其默认签发者；因此，我们必须相应地配置我们的应用。jwtenizr 方便地生成了一个我们可以使用的 `microprofile-config.properties` 文件；它应该类似于以下内容：

```
#由 jwtenizr 生成
#Mon Dec 13 18:05:17 EST 2021
mp.jwt.verify.publickey=MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEArOmNhZNAb2UXrMQ+TOp4hOCX4/QN1lC7DJW9Sw8PRZF85SrxKe2rAt8u0aaPg8KZdYsmAfxJU7ZsILHFV7cT9ixYyZcIz556CpluhQmJiVlBEDi6lX9IIbhXsfeaCXPATd+0fe0Yg4CLtfGeJjZQQOK8yabqsRemhQ84s/alCfWeWm7zp0DHe0PH+2kNkLVeSg4cigAzakDiW9JYNs5+7XzqPujYpNOjJqCltDfPkz0c0bqIOMKr7cm0G+WTQIqXxI46y1vUTYCdH+irJuJF8FPlL84Rd1NYrRtCLslFhqfLhSELYRWu7lyXsH89QSAjFTmY1ofzmWdawPYkIIQJrwIDAQAB
mp.jwt.verify.issuer=airhacks
```

将该文件放在我们 Maven 项目的 *src/main/resources/META-INF* 目录下，即可满足我们的 JWT 配置需求。



### MicroProfile JWT 注解

每个 JAX-RS 应用都需要一个继承自 `javax.ws.rs.core.Application` 的类实例；通常，我们会用 `@ApplicationPath` 注解该类，以指定应用中所有资源 URI 的基 URI。在保护应用时，我们需要用 `@LoginConfig` 注解该类，如下例所示。

```
package com.ensode.jwtdemo;
import javax.inject.Singleton;
import javax.ws.rs.ApplicationPath;
import javax.ws.rs.core.Application;
import org.eclipse.microprofile.auth.LoginConfig;
@Singleton
@ApplicationPath("webresources")
@LoginConfig(authMethod = "MP-JWT")
public class ApplicationConfig extends Application {
}
```

如示例所示，我们需要将 `@LoginConfig` 的 `authMethod` 属性设置为 `"MP-JWT"`；这会告知 MicroProfile 运行时（Payara Micro）我们的应用正在使用 JWT 进行身份验证。

此外，我们的应用类需要通过 `javax.inject.Singleton` 注解变为单例；否则将导致客户端无法正确进行身份验证。

一旦我们指定了使用 JWT 进行身份验证，就需要指定哪些角色可以访问受保护端点；这通过 `@RolesAllowed` 注解完成，如下例所示：

```
package com.ensode.jwtdemo;
import javax.annotation.security.RolesAllowed;
// 其他导入已省略
@RequestScoped
@Path("jwtdemo")
public class JwtDemoResource {
@GET
@Produces(MediaType.TEXT_PLAIN)
@RolesAllowed({"chief"})
public String secured() {
return "受保护端点访问成功\n";
}
}
```

`@RolesAllowed` 注解可用于类级别，此时它将应用于该类中的所有端点；也可用于方法级别，此时仅应用于被注解的方法。其值必须是一个字符串数组，包含允许访问该端点的有效角色；这些角色取自 JWT 中的 *groups* 声明。在我们的示例中，我们使用 jwtenizr 生成令牌，默认情况下，它会创建一个包含以下 groups/roles 的令牌："chief" 和 "hacker"；在我们的示例中，只有拥有 "chief" 角色的用户才能访问该端点。

为了使 `@RolesAllowed` 注解按预期工作，我们需要通过 `@RequestScoped` 注解将 JAX-RS 类转换为 CDI 请求作用域 bean，如示例所示。

我们可以使用 curl 通过将 Base 64 编码的令牌作为 HTTP 头发送来测试受保护端点，例如：

```
curl -H'Authorization: Bearer eyJraWQiOiJqd3Qua2V5IiwidHlwIjoiSldUIiwiYWxnIjoiUlMyNTYifQ.eyJzdWIiOiJkdWtlIiwidXBuIjoiZHVrZSIsImF1dGhfdGltZSI6MTYzOTQzOTIxNCwiaXNzIjoiYWlyaGFja3MiLCJncm91cHMiOlsiY2hpZWYiLCJoYWNrZXIiXSwiZXhwIjoxNjM5NDQwMjE0LCJpYXQiOjE2Mzk0MzkyMTQsImp0aSI6IjQyIn0.gMwd042R8L_pxmj97j9Bzsz8Vg5cZCSZ_uiSJwqnBD5i811Wq_4l9_DQ-LdRGQwgORKrN3emVfkxOiiWnTSSfEIOKDktuCiJuhlcojeaTvpACaFtnYLI5fZ6hCkurj7TEnrJwVqxJVo_dH6AS45UR2Q3z688oBBSb5_i5gtw9g_84nkWp70FkAjDusz-jLWeebOWAEk6NRv6fN_ZAP1JJcz9WoNordMeXgVe2tFmLz8_yDY_ba-0x1F9vEXBalHz4AwJaAYtW97mvshdnLnKkP48mQGoO4X9Ps6eRCG1viTuTmcHRTACt9osb7wssWIDenhuumz_lI9P9sLVFfj29Q' http://localhost:8080/jwt-demo/webresources/jwtdemo
```

上述 *curl* 命令将成功调用我们的端点；如果从命令中移除 `-H` 选项，则请求将失败，并返回 HTTP 状态码 401：未授权。

要通过 MicroProfile REST 客户端 API 授权请求，我们也需要发送 Base 64 编码的令牌；我们将在下一节中介绍如何执行此操作。

## 使用 MicroProfile REST 客户端 API 调用受保护微服务

为了通过 MicroProfile REST 客户端 API 调用受保护的 RESTful Web 服务，我们需要编写一个接口，并像往常一样使用 `@RegisterRestClient` 注解；但是，在这种情况下，与实现端点的代码相比，客户端接口中的方法签名和注解存在一些细微差异。

```
package com.ensode.jwtclientdemo;
import javax.ws.rs.HeaderParam;
import javax.ws.rs.core.HttpHeaders;
// 其他导入已省略
@RegisterRestClient
@Path("jwtdemo")
public interface JwtDemoResourceClient {
@GET
@Produces(MediaType.TEXT_PLAIN)
public String secured(@HeaderParam(HttpHeaders.AUTHORIZATION)
String authorizationHeader);
}
```

首先要注意的是，我们不应在客户端接口上使用 `@RolesAllowed` 注解；这会导致客户端本身受到保护，这不是我们示例中想要的效果；我们在这里的目标是从一个未受保护的端点调用一个受保护的端点；为此，我们需要将 JWT 令牌作为 HTTP 头发送。

我们可以通过使用 `@HeaderParam` 注解一个 `String` 参数来发送 HTTP 头；该注解的 `value` 属性指定了头名称；由于 "Authorization" 是一个标准的 HTTP 头名称，它在 `HttpHeaders` 类中被定义为 `AUTHORIZATION` 常量。

请注意，方法签名与 JAX-RS 服务代码中的相应签名并不完全匹配；端点没有定义 `authorizationHeader` 参数。

我们像往常一样使用 REST 客户端接口，通过 `@Inject` 和 `@RestClient` 注解注入它，然后将 JWT 令牌作为参数传递，该参数将作为 HTTP 头处理，这要归功于 `@HeaderParam` 接口。

```
package com.ensode.jwtclientdemo;
// 导入已省略
@ApplicationScoped
@Path("jwtclient")
public class JwtClientResource {
@Inject
@RestClient
private JwtDemoResourceClient jwtDemoResourceClient;
@Inject
@ConfigProperty(name = "ensode.jwt.header.string")
private String jwtHeaderString;
@GET
public String accessSecuredEndpoint() {
jwtDemoResourceClient.secured(
"Bearer ".concat(jwtHeaderString));
return "受保护端点访问成功";
}
}
```

我们可以将 JWT 令牌作为简单字符串获取；在我们的示例中，我们将其设置为 MicroProfile Config 属性并从中检索；然后我们调用客户端方法，将 JWT 令牌作为参数传递；为了使身份验证按预期工作，我们需要在令牌文本前加上单词 "Bearer"，我们在调用客户端接口上的方法时就是这样做的。

为了完整和清晰起见，以下是用于存储 JWT 文本的属性文件：

```
com.ensode.jwtclientdemo.JwtDemoResourceClient/mp-rest/url=http://localhost:8080/jwt-demo/webresources
ensode.jwt.header.string=eyJraWQiOiJqd3Qua2V5IiwidHlwIjoiSldUIiwiYWxnIjoiUlMyNTYifQ.eyJzdWIiOiJkdWtlIiwidXBuIjoiZHVrZSIsImF1dGhfdGltZSI6MTYzOTQ4ODc4OCwiaXNzIjoiYWlyaGFja3MiLCJncm91cHMiOlsiY2hpZWYiLCJoYWNrZXIiXSwiZXhwIjoxNjM5NDg5Nzg4LCJpYXQiOjE2Mzk0ODg3ODgsImp0aSI6IjQyIn0.C9_ejgLALOo8IVHZ6P6fqoTrVIjrmaqJ_gexHrEzwx0jWExDwLmKLIWKcOHWytKPrW0k1ok8cKUj55rKWG8Vp1-gGhIptWeGF2ckSKu5fCOaoBerJn6x8uDJVeURV8-ABHcoJFSePQx3EEGmoLEb2_EqgBvU4didp-MyUwyyIKiDIuOSu_n-mFq_yB9WpZZiY4lyNS9ewt_TDUmIgteJ2VMsmZaHp3n_RygV0S4FPXhOUHRhKOfLLUorvJzGYsT7dgjbnY6kyh4dgAXWrGnX4kaf0ZFyVXCxsmuvks9Ixltv6krqWe_j-0ZkYMzevgnXbvc3MA9LJWQMhauwtGfw1g
```

请注意，JWT 令牌文本被指定为 `ensode.jwt.header.string` 属性的值，我们在代码中通过 `@ConfigProperty` 注解检索它，并将其传递给客户端接口。



## 从令牌中获取信息

我们可以将 `org.eclipse.microprofile.jwt.JsonWebToken` 的实例注入到 JAX-RS 资源中；该类提供了许多 getter 方法，可用于从访问页面所使用的令牌中提取信息。

```
package com.ensode.jwtdemo;
//其他导入已省略
import org.eclipse.microprofile.jwt.JsonWebToken;
@RequestScoped
@Path("jwtdemo")
public class JwtDemoResource {
private static final Logger LOGGER = Logger.getLogger(JwtDemoResource.class.getName());
@Inject
private JsonWebToken jsonWebToken;
@GET
@Produces(MediaType.TEXT_PLAIN)
@RolesAllowed({"chief"})
public String secured() {
LOGGER.log(Level.INFO, String.format("受众: %s",
jsonWebToken.getAudience()));
LOGGER.log(Level.INFO, String.format("过期时间: %s",
jsonWebToken.getExpirationTime()));
LOGGER.log(Level.INFO, String.format("组: %s",
jsonWebToken.getGroups()));
LOGGER.log(Level.INFO, String.format("签发时间: %s",
jsonWebToken.getIssuedAtTime()));
LOGGER.log(Level.INFO, String.format("签发者: %s",
jsonWebToken.getIssuer()));
LOGGER.log(Level.INFO, String.format("名称: %s",
jsonWebToken.getName()));
LOGGER.log(Level.INFO, String.format("原始令牌: %s",
jsonWebToken.getRawToken()));
LOGGER.log(Level.INFO, String.format("主题: %s",
jsonWebToken.getSubject()));
LOGGER.log(Level.INFO, String.format("令牌 ID: %s",
jsonWebToken.getTokenID()));
return "安全端点访问成功\n";
}
}
```

下表列出了所有可用于从令牌中提取信息的 getter 方法，以及提取信息所对应的声明。

| Getter 方法 | JWT 声明 | 描述 |
| --- | --- | --- |
| `getAudience()` | aud | 目标受众 |
| `getExpirationTime()` | exp | 过期时间，以自 1970 年 1 月 1 日 UTC 以来的秒数表示 |
| `getGroups()` | groups | 用户所属的组（或角色） |
| `getIssuedAtTime()` | iat | 令牌签发时间，以自 1970 年 1 月 1 日 UTC 以来的秒数表示 |
| `getIssuer()` | iss | 签发令牌的组织 |
| `getName()` | upn | 唯一主体名称（用户名） |
| `getRawToken()` | 无 | 代表令牌的 Base 64 编码字符串（我们作为 HTTP 授权标头传递的值） |
| `getSubject()` | sub | 用户的附加标识符，可用于存储用户的全名（例如，“张三”） |
| `getTokenId()` | jti | JSON 令牌 ID |

上述所有 getter 方法都对应 MicroProfile JWT 规范要求或推荐的声明；如果任何推荐的声明不存在，getter 将返回 `null`。然而，一个 JWT 令牌可能包含任意数量的声明，其中许多声明我们没有对应的 getter 方法。

如果我们事先知道想要检索其值的声明名称，可以通过调用 `JsonWebToken` 实例上的 `claim()` 方法来实现；例如，如果我们知道令牌中有一个名为“foo”的声明，可以通过调用 `jsonWebToken.claim("foo")` 来获取其值。

有时我们可能无法事先知道令牌中所有的声明名称；为了处理这种情况，`JsonWebToken` 类提供了一个 `claimNames()` 方法，该方法返回一个包含令牌中所有声明名称的字符串集合；我们可以使用这个集合来获取令牌中所有声明的值；以下示例说明了如何做到这一点：

```
package com.ensode.jwtdemo;
//导入已省略
@RequestScoped
@Path("jwtdemo")
public class JwtDemoResource {
private static final Logger LOGGER = Logger.getLogger(JwtDemoResource.class.getName());
@Inject
private JsonWebToken jsonWebToken;
@GET
@Produces(MediaType.TEXT_PLAIN)
@RolesAllowed({"chief"})
public String secured() {
Set claimNames = jsonWebToken.getClaimNames();
LOGGER.log(Level.INFO, "--- 开始令牌声明");
claimNames.forEach(claimName -> LOGGER.log(Level.INFO,
String.format("%s: %s", claimName,
jsonWebToken.claim(claimName).orElse(""))));
LOGGER.log(Level.INFO, "--- 结束令牌声明");
return "安全端点访问成功\n";
}
}
```

在这个示例中，我们获取了一个包含所有声明名称的 `Set<String>`，然后遍历该集合并提取每个声明的值，最后将输出发送到 Payara Micro 的输出。回想一下，`JsonWebToken.claim()` 返回一个 `Optional`；在我们的示例中，如果 `Optional` 恰好为空，则使用空字符串 (`""`) 作为对应的值。

## 总结

在本章中，我们介绍了如何使用 JSON Web 令牌来保护我们的 RESTful Web 服务。

我们介绍了如何获取令牌以便用于保护应用程序；然后解释了如何配置 RESTful Web 服务以要求进行身份验证。

此外，我们还介绍了如何安全地调用受保护的 RESTful Web 服务。

最后，我们介绍了如何在代码中从 JSON Web 令牌中提取信息。

