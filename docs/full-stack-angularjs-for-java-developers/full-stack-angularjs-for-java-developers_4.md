# 4. 使用 Spring Security 保护你的 RESTful API

在本章中，你将保护你的 REST API，使得只有经过身份验证和授权的用户才能调用 REST API 并执行不同的 CRUD 操作。你将使用 Spring Security 来保护你的 RESTful 服务。首先，我将介绍 Spring Security 并解释所需的 Maven 依赖项。然后，你将学习如何在 UserRegistrationSystem 应用程序中实现 Spring Security 来保护你的 REST API。你还将了解用于设置用户凭据及其角色的内存和数据库配置。

## 介绍 Spring Security

安全是指保护资源免受未经身份验证和未授权用户的访问，并允许特定（经过身份验证和授权）用户访问这些受保护资源的过程。安全不同于防火墙、入侵检测、JVM 安全或其他任何东西。Spring Security 主要面向基于 Spring 的应用程序。

Spring Security 框架最初以 Acegi Security 框架起步，后来被 Spring 采纳为一个子项目。它已成为保护使用 Spring 框架开发的应用程序的事实标准。Spring Security 在 HTTP 请求方法的调用级别支持身份验证和授权。

### 身份验证与授权

身份验证和授权是 Spring Security 提供的两个主要操作（参见图 4-1）。

![A436970_1_En_4_Fig1_HTML.jpg](img/A436970_1_En_4_Fig1_HTML.jpg)

图 4-1.

身份验证与授权

身份验证是验证用户是否为其所声称身份的过程。身份验证通过识别和核实来完成。

授权是允许经过身份验证的用户访问资源的过程。换句话说，它为经过身份验证的用户提供访问控制。让我们来看一个 UserRegistrationSystem 应用程序中的示例：用户 `USER` 可以执行用户注册、更新用户详情以及获取用户列表，而只有用户 `ADMIN` 拥有删除用户的额外权限。授予客户端的访问权限将决定该应用程序的访问规则。

Spring Security 支持基于 URL 的安全性，并通过过滤器实现。Spring Security 还支持方法级安全性，只有授权用户才能调用授予他们的特定方法。

### 介绍基本认证

传统的身份验证方法，例如使用登录页面和会话标识，都依赖于需要人工交互的基于 Web 的客户端。当涉及到与 REST 客户端（甚至可能不是基于 Web 的应用程序）通信时，你需要考虑基本认证提供的解决方案。

基本认证是一种标准的 HTTP 标头（`Authorization`），它在每个请求中发送 Base64 编码的凭据。Base64 并未以任何方式进行加密或哈希处理；换句话说，用户名和密码以明文格式编码。

凭据字符串包含格式为 `username:password` 的用户名和密码。清单 4-1 展示了用于准备该标头的算法。

```
String plain_Client_Credentials="user:password";
String base64_ClientCredentials =
new String(Base64.encodeBase64(plain_Client_Credentials.getBytes()));
HttpHeaders headers = getHeaders();
headers.add("Authorization", "Basic " + base64_ClientCredentials);
清单 4-1.
准备 HTTP 标头的代码
```

清单 4-1 生成了标头 `Authorization : Basic dXNlcjpwYXNzd29yZA==`，该标头将随每个 HTTP 请求一起发送。

基本认证是保护 RESTful API 的最简单技术之一，因为它不需要 Cookie、会话标识符，甚至不需要登录页面。

### BasicAuthenticationFilter

`BasicAuthenticationFilter` 对象负责处理任何包含 HTTP 请求标头 `Authorization` 且认证方案为基本认证以及 Base64 编码的 `username:password` 令牌的 HTTP 请求。

如果身份验证成功，Spring 中的 `BasicAuthenticationFilter` 负责将结果（`Authentication` 对象）放入 `SecurityContextHolder` 中。

## 在 RESTful 服务上启用 Spring Security

在本节中，你将了解名为 Security 的 Spring Boot 启动器，然后你将使用它来保护你的 RESTful API。

### 什么是 Spring Boot Security 启动器？

Spring Boot 的目标是简化应用程序开发。与 Spring Boot 的每个其他特性一样，通过添加匹配的启动器 POM，名为 Security 的 Spring Boot 启动器会为开发者创建基本配置设置，包括 HTTP 基本认证和一个 `AuthenticationManager` bean，该 bean 在基于 Spring Boot 构建应用程序时带有一个内存中的默认用户。

现在，你将在你的 UserRegistrationSystem 应用程序中配置 Spring Security。要将 Spring Security 添加到你的 Spring Boot 应用程序，你需要在 Maven 的 `pom.xml` 文件中添加 Spring Security 依赖项。



### 使用 Spring Security 依赖更新 pom.xml 文件

清单 4-2 展示了需要在 UserRegistrationSystem 应用的 `pom.xml` 中添加的 Spring Security 依赖，该依赖将处理所有与 Spring Security 相关的必要依赖。

```
org.springframework.boot
spring-boot-starter-security

清单 4-2.
Spring Security 依赖
```

图 4-2 展示了在 UserRegistrationSystem Spring Boot 应用的 Maven 依赖中添加的依赖项。

![A436970_1_En_4_Fig2_HTML.jpg](img/A436970_1_En_4_Fig2_HTML.jpg)

图 4-2.

Spring Security 依赖

一旦将 Spring Security 依赖添加到 Maven 的 `pom.xml` 文件中，你的整个应用（除公共静态资源，如 CSS 文件、JavaScript 文件等之外的所有资源）都将通过 HTTP 基本认证得到保护，并且会为你的应用创建一个包含内存默认用户的 `AuthenticationManager` Bean。

默认用户名为 `user`，当你将 UserRegistrationSystem 应用作为 Spring Boot 应用运行时，密码会生成在 STS IDE 的控制台日志中。启动 Spring Boot 应用后，你将在日志中看到生成的默认用户密码，如下所示：

```
Using default security password: 72baa813-dab9-47bf-8329-78987485785b
```

图 4-3 展示了 STS 控制台中的日志，你可以在此看到 Spring Boot 应用的默认安全密码。

![A436970_1_En_4_Fig3_HTML.jpg](img/A436970_1_En_4_Fig3_HTML.jpg)

图 4-3.

日志中生成的默认安全密码

此处生成的密码是随机的，每次启动 Spring Boot 应用时都会改变，而用户名则始终相同（此处为 `user`）。

添加 Spring Security 依赖后启动 Spring Boot 应用时，控制台会打印日志，如图 4-4 所示。

![A436970_1_En_4_Fig4_HTML.jpg](img/A436970_1_En_4_Fig4_HTML.jpg)

图 4-4.

带有映射过滤器信息的日志

让我们来理解自动配置背后发生的魔法。`Mapping filter: 'springSecurityFilterChain' to: [/*]` 部分表明，默认情况下 Spring Security 已对应用中的所有 URL 启用。

让我们通过启动 Postman 并尝试调用一个 REST API 来测试你的应用：访问 `http://localhost:8080/api/user/` 以获取 UserRegistrationSystem 中的用户列表。一旦访问此 URL，你将收到状态码为 401 且错误信息为 `Unauthorized` 的响应，表明认证失败，如清单 4-3 所示。

```
{
"timestamp": 1503461733809,
"status": 401,
"error": "Unauthorized",
"message": "Full authentication is required to access this resource",
"path": "/api/user/"
}
清单 4-3.
未授权错误 JSON 响应
```

图 4-5 展示了 Postman 中状态码为 401、错误信息为 `Unauthorized` 的未授权错误消息。

![A436970_1_En_4_Fig5_HTML.jpg](img/A436970_1_En_4_Fig5_HTML.jpg)

图 4-5.

未授权错误消息

当你尝试从浏览器访问相同的 URL 时，会弹出一个提示“需要身份验证”的对话框，如图 4-6 所示。

![A436970_1_En_4_Fig6_HTML.jpg](img/A436970_1_En_4_Fig6_HTML.jpg)

图 4-6.

浏览器中弹出的身份验证要求

你必须在请求中提供用户名和密码才能获得授权。因此，你需要从 STS 控制台复制生成的默认用户密码，然后提供用户名 `user` 和该密码，以便在访问 RESTful API 时对用户进行身份验证。

启动 Postman，点击“Authorization”选项卡，然后将类型设置为“Basic Auth”。提供用户名和密码，点击“Update Request”，这样请求头中就会获得授权值。最后，访问 `http://localhost:8080/api/user/`，如图 4-7 所示。由于 UserRegistrationSystem 应用中的用户列表为空，响应将显示一个空列表。

![A436970_1_En_4_Fig7_HTML.jpg](img/A436970_1_En_4_Fig7_HTML.jpg)

图 4-7.

从 Postman 传递用户凭据

每次重启应用时都从日志中复制密码，这几乎不切实际。你可以通过在 `application.properties` 文件中设置一些属性来自定义此默认凭据，接下来你将学习如何操作。

### 覆盖 Spring Security 默认值

通过之前的设置，每次重启 Spring Boot 应用时，你都必须查找生成的默认用户密码，然后复制并粘贴该密码以在访问 RESTful API 时对用户进行身份验证，这几乎不切实际。

Spring Boot 允许开发者通过在 `application.properties` 文件中指定安全属性来轻松覆盖安全默认值（用户名、密码和角色），该文件位于 UserRegistrationSystem 应用的 `src\main\resources\` 文件夹中，如图 4-8 所示。

![A436970_1_En_4_Fig8_HTML.jpg](img/A436970_1_En_4_Fig8_HTML.jpg)

图 4-8.

在 application.properties 文件中覆盖安全属性

重启 UserRegistrationSystem 应用并观察 STS 控制台中的日志，如图 4-6 所示。你可以清楚地看到，这次 Spring Boot 应用没有生成任何默认用户密码。

让我们通过启动 Postman 并传递用户凭据来测试这一点，如清单 4-4 所示。

```
security.user.name = user
security.user.password = password
security.user.role = ADMIN
清单 4-4.
安全属性

在从 Postman 传递请求头中的用户凭据时，你可以点击 Postman 中的“Update Request”来更新 HTTP 请求头中的授权值，如图 4-9 所示。

![A436970_1_En_4_Fig9_HTML.jpg](img/A436970_1_En_4_Fig9_HTML.jpg)

图 4-9.

使用新配置的用户名和密码调用受保护的 REST API

到目前为止，你已看到仅通过添加 `spring-boot-starter-security` 依赖就发生了许多神奇的事情。

在实际用例中，你需要配置多个具有不同凭据和角色的用户来访问你的应用。此外，还需要自定义有权访问 REST API 的用户。为了实现这一点，你需要引入一些 Java 配置。

## 自定义用户身份验证

用户在获准访问受保护资源之前，需要先进行身份验证和授权。在 Spring Security 中，身份验证提供者负责对用户进行身份验证，验证成功后，用户才能访问系统中的受保护资源。

Spring Security 支持多种用户身份验证方式，例如使用内存定义进行用户身份验证，以及针对存储用户详细信息的用户存储库（关系数据库）进行身份验证。让我们通过配置这些用户和角色来自定义用户身份验证：

*   你将拥有两个角色：`USER` 和 `ADMIN`。
*   你将创建一个 `USER` 角色，凭据为 `user`/`password`。
*   你还将创建一个 `ADMIN` 角色，凭据为 `admin`/`password`。

在你的 UserRegistrationSystem 应用中，你将向特定用户角色提供对不同 REST 端点的访问权限，如表 4-1 所示。

表 4-1.

API 与用户角色的映射

| 用户角色 | HTTP 方法 | API 端点 |
| --- | --- | --- |
| `USER` | `GET` | `/api/user/` |
| `USER` | `POST` | `/api/user/` |
| `USER` | `PUT` | `/api/user/{id}` |
| `ADMIN` | `DELETE` | `/api/user/{id}` |



### 使用内存定义进行身份验证自定义

如果您的应用程序只有少数用户，您可以在 Spring 配置文件中定义他们的详细信息，而不是从某个持久化引擎中提取用户信息，这样用户详细信息就会加载到应用程序的内存中。让我们创建一个 `SpringSecurityConfiguration_InMemory.java` 文件，如代码清单 4-5 所示。

```
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Configuration;
import org.springframework.http.HttpMethod;
import org.springframework.security.config.annotation.authentication.builders.AuthenticationManagerBuilder;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
@Configuration
@Order(SecurityProperties.ACCESS_OVERRIDE_ORDER)
public class SpringSecurityConfiguration_InMemory extends WebSecurityConfigurerAdapter {
@Autowired
protected void configureGlobal(AuthenticationManagerBuilder auth)
throws Exception {                auth.inMemoryAuthentication().withUser("user").password("password")
.roles("USER");                auth.inMemoryAuthentication().withUser("admin").password("password")
.roles("USER", "ADMIN");
}
@Override
protected void configure(HttpSecurity http) throws Exception {
http
.httpBasic().and()
.authorizeRequests()
.antMatchers(HttpMethod.GET, "/api/user/")
.hasRole("USER")
.antMatchers(HttpMethod.POST, "/api/user/")
.hasRole("USER")
.antMatchers(HttpMethod.PUT, "/api/user/**")                          .hasRole("USER")
.antMatchers(HttpMethod.DELETE, "/api/user/**")
.hasRole("ADMIN")
.and()
.csrf()
.disable();
}
}
代码清单 4-5.
在内存中自定义用户身份验证
```

在上一个代码片段中，您创建了一个名为 `SpringSecurityConfiguration_InMemory` 的类，并使用 `@Configuration` 注解标记了该类，这是一个 Spring 注解，使该类成为一个配置类。您让该类继承了 `WebSecurityConfigurerAdapter` 类，这允许您配置 Spring Security 并覆盖应用程序的默认方法。

您通过创建两个用户（`user` 和 `admin`）及其角色（`USER` 和 `ADMIN`）和密码来配置身份验证。`admin` 用户同时拥有 `USER` 和 `ADMIN` 角色，而 `user` 用户只有 `USER` 角色。为了配置内存身份验证，您使用了 `configureGlobal` 方法。您使用 `@Autowired` 注解标记了此方法。此方法有一个 `AuthenticationManagerBuilder` 类型的参数。

接下来，您覆盖了第二个方法 `configure`，它接受参数 `HttpSecurity`。在 `configure` 方法中，您通过将角色映射到 URL 来配置授权。您使用了 HTTP 基本身份验证来验证每个请求。在此 `configure` 方法内部，您使用了 `anyMatchers` 方法将 URL 模式和 `HttpMethod` 映射到特定角色；例如，任何匹配 `/api/user/**` 并执行 `DELETE` 操作的 URL 都应具有 `ADMIN` 角色，其余操作可以具有 `USER` 角色。您还禁用了跨站请求伪造，以限制任何最终用户执行非预期的操作。

#### 运行 UserRegistrationSystem 应用程序

一旦您重新启动 UserRegistrationSystem 应用程序，您就可以通过启动 Postman 并使用用户凭据调用 API 来测试新实现的代码。

如图 4-10 所示，启动 Postman 在您的 UserRegistrationSystem 应用程序中注册一个新用户。在 Postman 中，选择 HTTP `POST` 方法并输入 URL `http://localhost:8080/api/user/`。点击 Authorization 选项卡，并为 Basic Auth 选择 TYPE。输入用户名 `user` 和密码 `password`，然后点击 Update Request 按钮，这将使用 `Authorization` 值更新 HTTP 标头。然后在请求体中提供 JSON 数据（如您在第 2 章中所见），最后点击 Send 按钮。

![A436970_1_En_4_Fig10_HTML.jpg](img/A436970_1_En_4_Fig10_HTML.jpg)

图 4-10.
通过传递用户凭据创建新用户

如果一切顺利，这将在您的 UserRegistrationSystem 应用程序中注册一个新用户。

您可以调用另一个端点来获取用户列表。输入用户凭据为 `user`/`password`，如图 4-11 所示。

![A436970_1_En_4_Fig11_HTML.jpg](img/A436970_1_En_4_Fig11_HTML.jpg)

图 4-11.
传递用户凭据后获取用户列表

类似地，您可以通过调用另一个端点并传递用户凭据 `user`/`password` 来更新用户详细信息，如图 4-12 所示。

![A436970_1_En_4_Fig12_HTML.jpg](img/A436970_1_En_4_Fig12_HTML.jpg)

图 4-12.
通过传递用户凭据更新用户详细信息

因此，您现在已使用用户凭据 `user`/`password` 在您的 Spring Boot 应用程序中执行了创建、读取和更新操作。您也可以使用 `admin` 用户的凭据 `admin`/`password` 执行这些操作，因为前面的端点可以被 `USER` 和 `ADMIN` 两种角色访问。

现在，如果您尝试使用 `USER` 角色，通过传递凭据 `user`/`password` 来调用应用程序中的另一个端点执行删除操作，会发生什么？由于您已将此操作限制为除 `ADMIN` 之外的所有其他用户角色，它应该会抛出错误消息“Access is denied”，状态码为 403 `Forbidden`，如图 4-13 所示。

![A436970_1_En_4_Fig13_HTML.jpg](img/A436970_1_En_4_Fig13_HTML.jpg)

图 4-13.
对未经授权的用户拒绝访问。

让我们通过传递 `admin` 凭据来调用此 API 以执行删除操作，如图 4-14 所示。

![A436970_1_En_4_Fig14_HTML.jpg](img/A436970_1_En_4_Fig14_HTML.jpg)

图 4-14.
使用 ADMIN 角色执行删除操作

由于您使用了 `ADMIN` 的凭据来调用此端点，操作成功，并且由于此操作从 UserRegistrationSystem 中删除了一个用户，响应返回了一个空列表。

到目前为止，您已经使用内存定义执行了身份验证自定义。接下来，让我们通过针对数据库进行身份验证自定义，将这一实践提升到新的水平。



### 针对关系型数据库的身份验证自定义

尽管你已经使用内存身份验证自定义了 Spring Security，但你希望针对数据库进行身份验证来自定义安全行为，以便使用应用程序的用户，而不是 Spring Boot 的通用用户。让我们通过创建和更新用户，为你的 UserRegistrationSystem 应用程序设置针对数据库进行身份验证自定义所需的基础设施。

首先，创建一个 `UserInfo` 领域对象，如清单 4-6 所示。这个实体类代表一个 UserRegistrationSystem 用户。`UserInfo` 实体类包含诸如 ID（`userid`）、用户名、密码、`isEnabled` 和角色等属性。你使用 JPA 和 Hibernate 注解来标注这个类。

```
package com.apress.ravi.dto;
import javax.persistence.Column;
import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.Id;
import javax.persistence.Table;
import org.hibernate.validator.constraints.NotEmpty;
@Entity
@Table(name = "users")
public class UserInfo {
@Id
@GeneratedValue
@Column(name = "userid")
private Long id;
@Column(name = "username")
@NotEmpty
private String username;
@Column(name = "password")
@NotEmpty
private String password;
@Column(name = "enabled")
private boolean isEnabled;
@Column(name = "role")
private String role;
public String getUsername() {
return username;
}
public void setUsername(String username) {
this.username = username;
}
public String getPassword() {
return password;
}
public void setPassword(String password) {
this.password = password;
}
public boolean isEnabled() {
return isEnabled;
}
public void setEnabled(boolean isEnabled) {
this.isEnabled = isEnabled;
}
public String getRole() {
return role;
}
public void setRole(String role) {
this.role = role;
}
}
清单 4-6.
UserInfo 类
```

由于你将把 UserRegistrationSystem 的用户存储在数据库中，因此需要一个 `UserInfoJpaRepository` 接口来对 `UserInfo` 实体执行 CRUD 操作。清单 4-7 展示了扩展了 `JpaRepository` 的 `UserInfoJpaRepository` 接口。除了 `JpaRepository` 提供的默认方法外，`UserInfoJpaRepository` 接口还包含一个名为 `findByUsername` 的自定义查找方法，该方法以用户名作为参数。Spring Data JPA 将提供一个运行时实现，允许 `findByUsername` 方法根据传入的用户名参数返回一个用户。

```
package com.apress.ravi.repository;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;
import com.apress.ravi.dto.UserInfo;
@Repository
public interface UserInfoJpaRepository extends JpaRepository {
public UserInfo findByUsername(String username);
}
清单 4-7.
UserInfoJpaRepository 接口

为简单起见，我生成了一些测试用户，如清单 4-8 所示。我在 UserRegistrationSystem 项目的 `src\main\resources` 文件夹中创建了一个 `import.sql` 文件，并将这些 SQL 语句复制到了该文件的末尾。当应用程序启动时，Hibernate 将用这些测试用户更新 `users` 表，并使它们可供应用程序使用。

```
INSERT INTO users (username, password, enabled, role) VALUES ('user', 'password', true, 'USER');
INSERT INTO users (username, password, enabled, role) VALUES ('admin', 'password', true, 'ADMIN');
INSERT INTO users (username, password, enabled, role) VALUES ('ravi', 'password', true, 'USER');
清单 4-8.
import.sql 文件中的测试用户数据
```

请注意，生成的测试用户的密码是明文，因此不需要对密码进行加密。

#### UserDetailsService 实现

在 Spring Security 中，`UserDetailsService` 用于从后端（例如数据库）返回用户信息，该信息会在身份验证过程中与提交的用户凭据进行比较。清单 4-9 展示了你的 UserRegistrationSystem 应用程序的 `UserDetailsService` 实现。

```
package com.apress.ravi.Service;
import java.util.ArrayList;
import java.util.Collection;
import java.util.List;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.security.core.GrantedAuthority;
import org.springframework.security.core.authority.AuthorityUtils;
import org.springframework.security.core.userdetails.UserDetails;
import org.springframework.security.core.userdetails.UserDetailsService;
import org.springframework.security.core.userdetails.UsernameNotFoundException;
import org.springframework.stereotype.Service;
import com.apress.ravi.dto.UserInfo;
import com.apress.ravi.repository.UserInfoJpaRepository;
@Service
public class UserInfoDetailsService implements UserDetailsService {
@Autowired
private UserInfoJpaRepository userInfoJpaRepository;
@Override
public UserDetails loadUserByUsername(String username)
throws UsernameNotFoundException {
UserInfo user = userInfoJpaRepository.findByUsername(username);
if (user == null) {
throw new UsernameNotFoundException(
"Opps! user not found with user-name: " + username);
}
return new org.springframework.security.core.userdetails.User(
user.getUsername(), user.getPassword(),
getAuthorities(user));
}
private Collection getAuthorities(UserInfo user) {
List authorities = new ArrayList();
authorities = AuthorityUtils.createAuthorityList(user.getRole());
return authorities;
}
}
清单 4-9.
UserRegistrationSystem 的 UserDetailsService 实现
```

`UserInfoDetailsService` 类自动装配了 `UserInfoJpaRepository`，以便从数据库中检索 `UserInfo` 详细信息。该类重写了 `loadUserByUsername` 方法，该方法返回一个 `UserDetails` 类型的实例。此方法首先检查从数据库检索到的用户是否为空，如果为空，则抛出一个带有适当消息的 `UsernameNotFoundException` 异常。如果检索到的用户不为空，则此方法创建一个 `org.springframework.security.core.userdetails.User` 实例，并使用从数据库返回的用户数据填充它。



#### 自定义 Spring Security 并保护 URI

现在，你将通过创建一个带有 `@Configuration` 和 `@EnableWebSecurity` 注解的 `SpringSecurityConfiguration_Database` 配置类，来自定义 Spring Security 的默认行为并保护一个 URI。该配置类继承了 `org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter` 类，该类提供了一个辅助方法来配置 Spring Security。清单 4-10 展示了包含 UserRegistrationSystem 应用程序 Spring Security 配置的 `SpringSecurityConfiguration` 数据库。

```
package com.apress.ravi.Security;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.authentication.builders.AuthenticationManagerBuilder;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
import org.springframework.security.config.http.SessionCreationPolicy;
import com.apress.ravi.Service.UserInfoDetailsService;
@Configuration
@EnableWebSecurity
public class SpringSecurityConfiguration_Database
extends WebSecurityConfigurerAdapter {
@Autowired
private UserInfoDetailsService userInfoDetailsService;
@Override
protected void configure(
AuthenticationManagerBuilder authenticationManagerBuilder)
throws Exception {
authenticationManagerBuilder
.userDetailsService(userInfoDetailsService);
}
@Override
protected void configure(HttpSecurity http) throws Exception {
http.sessionManagement()
.sessionCreationPolicy(SessionCreationPolicy.STATELESS)
.and()
.authorizeRequests()
.antMatchers("/api/user/**")
.authenticated()
.and()
.httpBasic()
.realmName("User Registration System")
.and()
.csrf()
.disable();
}
}
清单 4-10.
UserRegistrationSystem 的安全配置
```

`SpringSecurityConfiguration_Database` 配置类自动装配了 `UserInfoDetailsService` bean。该配置类重写了 `WebSecurityConfigurerAdapter` 的 `configure` 方法，该方法以 `AuthenticationManagerBuilder` 作为参数。`AuthenticationManagerBuilder` 是一个辅助类，它实现了构建器模式，并提供了一种组装 `AuthenticationManager` 的方法。你在此方法中使用了 `AuthenticationManagerBuilder` 来添加 `UserInfoDetailsService` 实例。

此外，你还在 `SpringSecurityConfiguration` 数据库配置类中重写了 `WebSecurityConfigurerAdapter` 的另一个 `configure` 方法，该方法有助于配置 HTTP 基本认证以使用 UserRegistrationSystem 用户。此配置保护所有端点，并要求进行身份验证才能访问资源。该方法以 `HttpSecurity` 作为参数，允许你指定哪些 URI 应该被保护或不受保护。该方法的实现首先请求 Spring Security 不要创建 HTTP 会话，也不要将已登录用户的 `SecurityContext` 值存储在会话中，这是通过使用 `SessionCreationPolicy.STATELESS` 创建策略实现的。然后，你使用了 `antMatchers` 来提供一个 Ant 风格的 URI 表达式，你希望 Spring Security 使用 `authenticated` 方法来保护该表达式，该方法允许经过身份验证的用户访问相应的端点。最后，你启用了 HTTP 基本认证，并将领域名称设置为 User Registration System。

此外，为了简单起见，你禁用了 HTTP 方法的 CSRF。CSRF 是一种安全漏洞，恶意网站会强制用户在当前已认证的另一个网站上执行非预期的命令。默认情况下，Spring Security 会启用 CSRF 保护，并且强烈建议在用户通过浏览器提交请求时使用。对于非浏览器客户端使用的服务（REST），可以禁用 CSRF。

#### 方法级安全

方法级安全是保护 URI 访问的另一种方式。有时需要确保只有具有管理员权限（拥有 `admin` 角色）的用户才能删除 UserRegistrationSystem 中的注册用户，这可以通过对方法实施细粒度的安全控制来实现。你将在 `deleteUser` 方法上应用 Spring Security 的方法级安全。

要启用 Spring 的方法级安全，请使用 `org.springframework.security.config.annotation.method.configuration.EnableGlobalMethodSecurity` 注解来标注 `SpringSecurityConfiguration_Database` 配置类，如清单 4-11 所示。

```
package com.apress.ravi.Security;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.authentication.builders.AuthenticationManagerBuilder;
import org.springframework.security.config.annotation.method.configuration.EnableGlobalMethodSecurity;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
import org.springframework.security.config.http.SessionCreationPolicy;
import com.apress.ravi.chapter2.Service.UserInfoDetailsService;
@Configuration
@EnableWebSecurity
@EnableGlobalMethodSecurity(prePostEnabled = true)
public class SpringSecurityConfiguration_Database
extends WebSecurityConfigurerAdapter {
//...code
}
清单 4-11.
添加 EnableGlobalMethodSecurity 注解
```

Spring Security 支持丰富的方法级授权注解以及类级授权注解。你在 `EnableGlobalMethodSecurity` 中将 `prePostEnabled` 标志设置为 `true`，这将启用 Spring Security 注解来执行方法调用前后的授权检查。因此，使用 `@PreAuthorize` 注解来标注 `UserRegistrationRestController` 的 `deleteUser` 方法，如清单 4-12 所示。

```
import org.springframework.security.access.prepost.PreAuthorize;
@DeleteMapping("/{id}")
@PreAuthorize("hasAuthority('ADMIN')")
public ResponseEntity deleteUser(@PathVariable("id") final Long id) {
//...code
}
清单 4-12.
添加 PreAuthorize 注解
```

`@PreAuthorize` 注解仅允许授权用户调用 `deleteUser` 方法。`hasAuthority` 检查登录用户是否拥有 `ADMIN` 权限，并让 Spring Security 做出决定。

重新启动 UserRegistrationSystem 应用程序，并启动 Postman 对端点 `http://localhost:8080/api/user/1` 执行 `DELETE` 操作。将 Postman 中的授权类型设置为 Basic Auth，并输入用户名为 `user`、密码为 `password` 的凭据，该用户拥有 `USER` 角色。由于 `user` 没有管理权限，将返回一条未经授权的响应，消息为“Access is denied”，如图 4-15 所示。

![A436970_1_En_4_Fig15_HTML.jpg](img/A436970_1_En_4_Fig15_HTML.jpg)

图 4-15.

未经授权的删除响应

## 总结

在本章中，你了解了 Spring Security 以及身份验证和授权的概念。你通过实现基本认证，在 RESTful 服务上启用了 Spring Security。然后，你使用内存数据库和针对数据库的方式自定义了身份验证。在下一章中，你将探索如何使用 AngularJS 消费受保护的 RESTful API。



