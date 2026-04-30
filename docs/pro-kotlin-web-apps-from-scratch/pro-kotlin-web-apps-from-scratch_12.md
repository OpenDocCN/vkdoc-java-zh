# 14. 使用 Spring Security 的企业级身份验证

在本章中，你将学习如何弥合新旧之间的差距，并将 Spring Security（[*https://spring.io/projects/spring-security*](https://spring.io/projects/spring-security)）添加到你的基于 Ktor 的 Web 应用中。

如果你的组织已经广泛使用 Spring Security，你可能已经拥有大量的基础设施，要改变它们需要付出打破康威定律的巨大努力。相反，你可以将 Spring Security 添加到你的现代 Kotlin Web 应用中，并利用你组织现有的插件和专业知识。

请注意，Spring Security 并非为组合而设计。Spring Security 会包装你的整个 Web 应用，如果你已经设置了其他身份验证方式，例如第 9 章中基于 cookie 的身份验证或第三方系统（如 Keycloak（[*www.keycloak.org/*](http://www.keycloak.org/)）），它们将会发生冲突并相互干扰。

## 准备你的 Web 应用

为了能够在你的 Web 应用中使用 Spring Security，你需要首先完成一些步骤。简而言之，你的 Web 应用需要基于 Servlet 规范。然后，你将 Spring Security 添加为一个 Servlet 过滤器，这是使用 Spring Security 的主要方式。



### 配置嵌入式 Jetty

到目前为止，你一直使用 Ktor 嵌入式服务器和 Netty 来运行你的 Web 应用。为了完全控制配置并添加对 Servlet 的支持，你需要进行切换。你将不再使用 Ktor 的 `embeddedServer`，而是从头开始启动一个嵌入式 Jetty 服务器，将其配置为使用 Servlet 规范，并将你的 Ktor Web 应用作为 Servlet 附加到其上。

首先，你需要将必要的依赖项添加到你的 *build.gradle.kts* 文件中：

```
implementation("io.ktor:ktor-server-servlet:2.1.2")
implementation("org.eclipse.jetty:jetty-server:9.4.49.v20220914")
implementation("org.eclipse.jetty:jetty-servlet:9.4.49.v20220914")
```

你将使用 Jetty 作为实际的服务器，并使用 `ktor-server-servlet` 将你现有的 Ktor 应用映射到 Jetty Servlet 配置。

Jetty 的最新版本是 11。你使用 v9 最新版本的原因是，Jetty 的后续版本在 Java 平台上使用了新的 `jakarta.*` 命名空间作为标准库 API。由于许可和专利问题，Java 社区正在逐步弃用 `javax.*` 命名的包。但就目前而言，你必须坚持使用 `javax` 命名空间及其兼容版本，因为最新版本的 Spring Security 仍然使用 `javax` 命名空间。当 Spring 6 发布时，你可以升级所有这些版本，因为 Spring 5 使用 `javax.*`，而 Spring 6 使用新的 `jakarta.*` 命名空间。

这个新配置与你现有的配置完全分离，因此你将在一个新文件中编写所有代码。你不会在现有的 *Main.kt* 中添加更多内容，而是创建一个新的 *MainSpringSecurity.kt*，所有基于 Servlet 和 Jetty 的新代码都将放在其中。清单 14-1 展示了启动并运行 Jetty 并将其配置为 Servlet 模式所需的完整 `main()` 函数。

```
import org.eclipse.jetty.server.HttpConnectionFactory
import org.eclipse.jetty.server.Server
import org.eclipse.jetty.server.ServerConnector
import org.eclipse.jetty.servlet.ListenerHolder
import org.eclipse.jetty.servlet.ServletContextHandler
private val log = LoggerFactory.getLogger(
"kotlinbook.MainSpringSecurity"
)
fun main() {
val appConfig = createAppConfig(
System.getenv("KOTLINBOOK_ENV") ?: "local"
)
val server = Server()
val connector = ServerConnector(
server,
HttpConnectionFactory()
)
connector.port = appConfig.httpPort
server.addConnector(connector)
server.handler = ServletContextHandler(
ServletContextHandler.SESSIONS
).apply {
contextPath = "/"
resourceBase = System.getProperty("java.io.tmpdir")
servletContext.setAttribute("appConfig", appConfig)
servletHandler.addListener(
ListenerHolder(BootstrapWebApp::class.java)
)
}
server.start()
server.join()
}
清单 14-1
启动并配置 Jetty 以托管 Servlet
```

Jetty 是一个功能齐全的 Java 平台服务器，它实现了大量 API，并且极其灵活。与 Ktor 中的 `embeddedServer` 不同，Jetty 也没有太多默认设置，因此你必须显式指定所有内容才能使其启动并运行。

此配置的主要部分是 Jetty 服务器的 handler 属性，它被设置为一个 `ServletContextHandler`。你传递给它的 `ServletContextHandler.SESSIONS` 标志启用了 Servlet 会话，这是使 Spring Security 正常工作所必需的。handler 需要一个上下文路径，这是你的 Web 应用可访问的基本路径。它还需要一个资源目录才能工作，该目录指向系统默认的临时目录，以便操作系统自动清理 Jetty 创建的任何文件。Servlet 上下文（所有单独的 Servlet 和过滤器都可以访问它）可以访问应用程序配置，这将在以后派上用场。最后，设置了一个监听器，这是一个在 Jetty 完成启动后由 Jetty 服务器执行的类。你将在此类中配置和初始化用于你的 Web 应用和 Spring Security 本身的实际 Servlet。

如果你现在输入此代码，`BootstrapWebApp` 将会出现编译错误。该类尚不存在，因此你接下来将编写它。

### 初始化 Servlet

既然 Jetty 已配置为 Servlet 模式，你需要初始化你的 Servlet。Servlet 初始化是 Java 平台上的一项古老的 Jedi 大师级技艺。你为 Servlet 栈编写的初始化代码将不知道 Jetty 的存在，任何支持 Servlet 的环境都可以执行它——甚至是像 Apache Tomcat ([*https://tomcat.apache.org/*](https://tomcat.apache.org/)) 和 IBM WebSphere ([*www.ibm.com/products/websphere-application-server*](http://www.ibm.com/products/websphere-application-server)) 这样的老式服务器容器。

你已经在清单 14-1 中输入了初始化器类的名称 `BootstrapWebApp`。清单 14-2 展示了如何正确创建和设置此类。

```
import javax.servlet.annotation.WebListener
import javax.servlet.ServletContextEvent
import javax.servlet.ServletContextListener
@WebListener
class BootstrapWebApp : ServletContextListener {
override fun contextInitialized(sce: ServletContextEvent) {
val ctx = sce.servletContext
}
override fun contextDestroyed(sce: ServletContextEvent) {
}
}
清单 14-2
Servlet 初始化的基本框架
```

这个初始化器目前还没有做任何工作。你将在本章后面在此处添加更多内容。现在，尝试运行你的主类，并检查一切是否编译正确。如果你做的一切正确，基于 Servlet 的 Jetty 服务器应该可以正常启动，并根据你的配置文件监听正确的端口。请注意，如果你的现有主函数已在后台运行，则应将其停止，因为 Jetty 服务器将尝试使用相同的端口（因为它使用相同的配置文件和相同的配置加载代码）。

提示

`BootstrapWebApp` 上的 `WebListener` 注解不是必需的，删除它后一切也能正常工作。但是，添加它意味着你可以在老式的容器型 Java 服务器上构建和运行你的 Web 应用，它会识别 `WebListener` 注解并挂载你的 Servlet，而无需运行你的 `main()` 函数！

你需要设置的所有内容都将在 `BootstrapWebApp` 类的 `contextInitialized` 函数中完成。

稍后你将在 `contextInitialized` 中添加更多内容。现在，你只需要提取配置并获取数据源。清单 14-3 展示了如何执行此操作。

```
override fun contextInitialized(sce: ServletContextEvent) {
val ctx = sce.servletContext
log.debug("Extracting config")
val appConfig = ctx.getAttribute(
"appConfig"
) as WebappConfig
log.debug("Setting up data source")
val dataSource = createAndMigrateDataSource(appConfig)
清单 14-3
在 contextInitialized 内部访问配置和数据源
```

`"appConfig"` 属性来自清单 14-1，你在其中创建了 Servlet 上下文并将 `"appConfig"` 属性分配给了你的 `WebappConfig` 实例。这意味着你可以从 `contextInitialized` 内部的 Servlet 上下文中访问它。`getAttribute` 的类型是平台类型 `Object!`，因此你需要将其转换为 `WebappConfig`。然后，就像你在主要的 Ktor 设置和测试中所做的那样，创建一个数据源。



### 将 Ktor 添加到你的 Servlet 中

现在你已经拥有了一个包含配置和数据源等所有基础功能的 Servlet，下一步就是将你的 Ktor Web 应用连接到这个 Servlet。

由于 Ktor Servlet 包的构建方式，需要一些手动操作。有一个内置的 Servlet 用于挂载 Ktor Web 应用，即 `io.ktor.server.servlet.ServletApplicationEngine`，这就是你将要用到的那个。但这个 Servlet 类不够灵活，默认情况下，它会基于加载一些默认配置文件并在内部进行连接，尝试创建自己的 Ktor 应用实例。你需要能够以编程方式传递你使用第 2 章中的 `createKtorApplication` 创建的 Ktor 应用实例。清单 14-4 展示了如何进行全部设置。

```
log.debug("Setting up Ktor servlet environment")
val appEngineEnvironment = applicationEngineEnvironment {
module {
createKtorApplication(appConfig, dataSource)
}
}
val appEnginePipeline = defaultEnginePipeline(
appEngineEnvironment
)
BaseApplicationResponse.setupSendPipeline(
appEnginePipeline.sendPipeline
)
appEngineEnvironment.monitor.subscribe(
ApplicationStarting
) {
it.receivePipeline.merge(appEnginePipeline.receivePipeline)
it.sendPipeline.merge(appEnginePipeline.sendPipeline)
it.receivePipeline.installDefaultTransformations()
it.sendPipeline.installDefaultTransformations()
}
ctx.setAttribute(
ServletApplicationEngine
.ApplicationEngineEnvironmentAttributeKey,
appEngineEnvironment
)
log.debug("Setting up Ktor servlet")
ctx.addServlet(
"ktorServlet",
ServletApplicationEngine::class.java
).apply {
addMapping("/")
}
清单 14-4
在 contextInitialized 中将 Ktor 设置为 Servlet
```

这段初始化代码大部分基于 Ktor 内置 `ServletApplicationEngine` 中已有的初始化代码，Ktor 会在其中尝试创建自己的 Ktor 应用实例。`appEngineEnvironment` 和 `appEnginePipeline` 是 `ServletApplicationEngine` 所需的内部对象。此外，还需要为 `ApplicationStarting` 事件进行一些必要的连接，并且 `ServletApplicationEngine` Servlet 期望能够从 Servlet 上下文的一个特定命名属性中获取 `appEngineEnvironment` 实例。

希望未来版本的 Ktor Servlet 包能让你更轻松地以编程方式传递自己的 Ktor 应用实例。不过，目前你仍然需要上述的连接代码才能使一切正常工作。

当所有设置完成后，你可以使用标准的 Servlet API 来添加一个 Servlet 并添加映射。

现在，你已经成功搭建了基于 Servlet 的 Ktor Web 应用！尝试启动应用，你应该会看到所有 Web 处理器都能正常响应，就像你运行旧的 `main` 函数（使用 `embeddedServer` 和 Netty 启动 Ktor）时一样。

## 使用 Spring Security

现在你已经让基础功能运行起来了，你的 Ktor 应用作为 Servlet 运行在 Jetty 上。下一步是将 Spring Security 添加到你的 Servlet 栈中。

### 设置 Servlet 过滤器

你需要做的第一件事是将所需的依赖项添加到 *build.gradle.kts* 中：

```
implementation("org.springframework.security:spring-security-web:5.7.3")
implementation("org.springframework.security:spring-security-config:5.7.3")
```

接下来，你需要在你的 Servlet 环境中连接 Spring Security。为此，你需要三样东西：一个角色层次结构、一个 Spring Security Web 应用上下文，以及一个映射到你的 Servlet 的 Spring Security 过滤器。清单 14-5 展示了如何进行全部设置。

```
log.debug("Setting up Spring Security")
val roleHierarchy = """
ROLE_ADMIN > ROLE_USER
"""
val wac = object : AbstractRefreshableWebApplicationContext() {
override fun loadBeanDefinitions(
beanFactory: DefaultListableBeanFactory
) {
beanFactory.registerSingleton(
"dataSource",
dataSource
)
beanFactory.registerSingleton(
"rememberMeKey",
"asdf"
)
beanFactory.registerSingleton(
"roleHierarchy",
RoleHierarchyImpl().apply {
setHierarchy(roleHierarchy)
}
)
AnnotatedBeanDefinitionReader(beanFactory)
.register(WebappSecurityConfig::class.java)
}
}
wac.servletContext = ctx
ctx.addFilter(
"springSecurityFilterChain",
DelegatingFilterProxy("springSecurityFilterChain", wac)
).apply {
addMappingForServletNames(null, false, "ktorServlet")
}
清单 14-5
在 contextInitialized 中设置 Spring Security
```

你可以在没有角色层次结构的情况下设置 Spring Security，但很可能你需要多个角色，所以最好立即进行设置。

Web 应用上下文是一个依赖注入引擎，你可以在其中以编程方式注册单例 Bean，即对象的硬编码实例，这些实例将被依赖注入到你的 Spring Security 配置中，以便稍后在 Spring Security 中配置身份验证和过滤时使用。

`"rememberMeKey"` 是 Spring Security 用于创建会话 Cookie 的密钥。与其硬编码为 `"asdf"`，你应该将其存储为配置属性。到目前为止，你已经创建了几个配置属性，因此你已经知道如何添加更多属性。继续更新你的配置文件、配置数据类和配置加载器代码，将 `"rememberMeKey"` 存储在那里吧！



### 配置 Spring Security

下一步是实现 `WebappSecurityConfig` 类，该类已在清单 14-5 中作为 bean 添加到 Spring Security Web 应用上下文中。

请注意，关于如何配置和设置 Spring Security 所有复杂细节的详细教程不在本书的讨论范围之内。为此，我建议你参考众多在线文章和其他相关书籍。本书的目标仅仅是向你展示如何入门。

类名对于 Spring Security 来说并不重要，你可以随意命名这个配置类。它之所以能被识别为 Spring Security 配置类，是因为你将添加的注解。清单 14-6 展示了该类的空骨架实现。

```
import org.springframework.context.annotation.Configuration
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity
import org.springframework.beans.factory.annotation.Autowired
import org.springframework.context.annotation.Bean
import org.springframework.security.core.userdetails.UserDetailsService
import org.springframework.security.core.userdetails.User
import org.springframework.security.config.annotation.web.builders.HttpSecurity
import org.springframework.security.web.SecurityFilterChain
@Configuration
@EnableWebSecurity
open class WebappSecurityConfig {
@Autowired
lateinit var dataSource: DataSource
@Autowired
lateinit var roleHierarchy: RoleHierarchy
@Autowired
lateinit var rememberMeKey: String
@Autowired
lateinit var userDetailsService: UserDetailsService
@Bean
open fun userDetailsService() =
UserDetailsService { userName ->
User(userName, "{noop}", listOf())
}
@Bean
open fun filterChain(
http: HttpSecurity
): SecurityFilterChain {
return http.build()
}
}
清单 14-6
一个空的 Spring Security 配置类
```

注解 `@Configuration` 和 `@EnableWebSecurity` 会触发 Spring Security 以及清单 14-5 中的 `DelegatingFilterProxy`，从而在你的 Servlet 链中启用和配置 Spring Security。

`@Autowired` 注解意味着 Spring Security 会根据属性名称及其类型，将 Web 应用上下文（清单 14-5 中）的对象实例（bean）分配给这些属性。

稍后，你将扩展 `filterChain`，使其包含 Spring Security 中用户和过滤部分的实际配置。

`UserDetailsService` 是 Spring Security 设置中必需的部分。目前，你只需用它来创建一个密码为空（Spring Security 会将 `"{noop}"` 字符串解释为不尝试进行任何与密码解码相关的复杂操作）且角色列表为空的虚拟用户。

### 认证用户

为了对用户进行认证，你需要使用一个认证提供者来配置 Spring Security 过滤器链。

认证提供者是 Spring Security 要求你处理登录凭据、检查用户是否存在以及验证你收到的凭据（密码）是否正确的地方。

认证设置在你于清单 14-6 中创建的、当前为空的 `filterChain` 函数内部。清单 14-7 展示了如何进行设置。

```
http.authenticationProvider(object : AuthenticationProvider {
override fun authenticate(
auth: Authentication
): Authentication? {
val username = auth.principal as String
val password = auth.credentials as String
val userId = sessionOf(dataSource).use { dbSess ->
authenticateUser(dbSess, username, password)
}
if (userId != null) {
return UsernamePasswordAuthenticationToken(
username,
password,
listOf(SimpleGrantedAuthority("ROLE_USER"))
)
}
if (username == "quentin" && password == "test") {
return UsernamePasswordAuthenticationToken(
username,
password,
listOf(SimpleGrantedAuthority("ROLE_ADMIN"))
)
}
return null
}
override fun supports(authentication: Class) =
authentication ==
UsernamePasswordAuthenticationToken::class.java
})
清单 14-7
在 WebappSecurityConfig 的 filterChain 方法中添加一个用于认证用户的认证提供者
```

这段代码使用了第 9 章的 `authenticateUser` 函数，根据你现有的设置来认证用户。Spring Security 通过 `principal` 和 `credentials` 属性提供用户名和密码，并期望你的 `authenticate` 函数在未找到与这些凭据匹配的用户时返回 `null`，如果找到有效用户，则返回 `org.springframework.security.core.Authentication` 的一个实例。

为了演示，代码中还包含了一个硬编码的管理员用户示例，其用户名为 `"quentin"`，密码为 `"test"`。出于显而易见的原因，你不应该在实际的 Web 应用中添加此类内容。但这提供了一个示例，说明你可以用 Spring Security 做什么，以及如何创建具有不同角色的用户。

### 过滤请求

目前，你的 Spring Security 过滤器不会过滤任何请求。为此，你需要设置一个过滤器链。

过滤器链是一组规则，Spring Security 使用它来确定一个请求是否需要认证，以及如果存在已认证的用户，该用户是否具有正确的访问级别。

过滤器链是使用一个编程式构建器 DSL 设置的，该 DSL 可在你的 Spring Security 配置类的 `filterChain` 内部的 `HttpSecurity` 实例 HTTP 上使用。清单 14-8 展示了你可以用它做的一些事情的示例。

```
http
.authorizeRequests()
.expressionHandler(
DefaultWebSecurityExpressionHandler().apply {
setRoleHierarchy(roleHierarchy)
}
)
.antMatchers("/login").permitAll()
.antMatchers("/coroutine_test").permitAll()
.antMatchers("/admin/**").hasRole("ADMIN")
.antMatchers("/**").hasRole("USER")
.anyRequest().authenticated()
.and().formLogin()
.and()
.rememberMe()
.key(rememberMeKey)
.rememberMeServices(
TokenBasedRememberMeServices(
rememberMeKey,
userDetailsService
).apply {
setCookieName("REMEMBER_ME_KOTLINBOOK")
}
)
.and()
.logout()
.logoutRequestMatcher(
AntPathRequestMatcher("/logout")
)
清单 14-8
使用 filterChain 内部的构建器 DSL 过滤请求
```

设置过滤的方法有很多种，你需要更改具体的配置来满足你的特定需求。这作为一些可能性的示例，并且对于大多数 Web 应用来说是一个很好的起点。

登录页面被设置为不需要认证，以避免无限重定向循环。所有以 */admin* 开头的页面都要求用户具有管理员角色。否则，过滤器要求对所有页面进行认证。还设置了默认的 `formLogin()`，它使用 Spring Security 默认的登录 GUI。它带有一个样式化的登录表单和一些默认的错误消息，是一种无需自己实现登录表单即可快速上手并演示工作设置的好方法。然后，设置了 `logout`，以便当用户访问路径 */logout* 时，Spring Security 会注销该用户并清除会话。

再次强调，本书并非 Spring Security 的全面教程。其目的是让 Spring Security 设置好并准备就绪，与你从头开始编写的、基于库的 Web 应用协同工作。



### 访问已登录用户

当加载需要登录的 Web 处理器时，能够提取当前已登录用户的信息也很有用，这样你就可以在网页上显示用户名，并在数据库中使用用户 ID 作为各种查询的输入。

在 Spring Security 中，有两种主要方式可以访问当前已登录用户。

第一种是使用线程局部变量可访问的形式：`SecurityContextHolder.getContext().authentication`。如果你从 Web 处理器线程访问它，它是全局可用的。这是一种访问当前已登录用户的便捷方式，但在 Kotlin 的世界中会有点问题。例如，如果你不小心，你的代码可能会因为你在协程中访问它而突然运行在另一个线程中。而且，如果你尝试将 Servlet 和 Servlet 过滤器设置为异步，`SecurityContextHolder` 也会失效，因为异步 Servlet 并不局限于每个请求使用单个线程。

第二种是在 Servlet 请求本身上访问用户主体。这种方式不依赖线程局部变量，本质上更具函数式和数据驱动特性。你的 Servlet 栈处理的每个请求都会获得一个 Servlet 请求实例，你可以随意将其传递到任何地方。

不过，使用 `javaSecurityPrincipal` 有一个问题。在我撰写本章时，Ktor 2.2.0 尚未发布。在早期版本的 Ktor 中存在一个 bug，导致 `javaSecurityPrincipal` 无法正常工作，总是返回 `null`。因此，目前你只能使用非异步 Servlet，并通过线程局部的 `SecurityContextHolder.getContext().authentication` 来访问会话。

我建议你查看 Ktor 的问题跟踪器，网址为 [*https://youtrack.jetbrains.com/issue/KTOR-4784*](https://youtrack.jetbrains.com/issue/KTOR-4784)，看看该问题是否已解决并发布。该问题由本人于 2022 年 8 月 22 日（恰好也是本人的生日）报告，并在仅仅两天后的 8 月 24 日由 Ktor 团队修复。耶，Ktor 团队！

