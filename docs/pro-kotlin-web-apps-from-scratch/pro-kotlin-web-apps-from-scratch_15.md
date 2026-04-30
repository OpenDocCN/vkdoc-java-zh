# 16. Kotlin 技巧集锦

在前面的章节中，你通过实现一个生产级 Web 应用，已经学到了许多巧妙的 Kotlin 技巧。在本章中，你将学习一些我尚未提及的、更巧妙的 Kotlin 技巧。

这些技巧中，许多对库作者来说比 Web 应用构建者更有用。事实上，大多数库很可能都会用到本章提到的大部分概念。但了解这些概念仍然是有益的。你最终可能会需要调试库代码，而了解你所看到的内容总是好的。而且，世事难料！也许当你在为自己的 Web 应用编写工具函数和辅助函数时，会发现本章中的 Kotlin 技巧大有用武之地。

## 委托（by lazy）

惰性委托允许你延迟对值和属性的昂贵计算，但访问它们时，却如同访问其他普通属性一样。

在你本书构建的 Web 应用中，并没有哪些初始化过于急切、能从惰性初始化中受益的昂贵资源。对于 Web 应用后端来说，拥有有状态组件并不常见，因为状态通常存在于数据库和队列中。因此，为了演示惰性初始化，我们将看一个假设的例子。

惰性委托的一个用例是那些计算昂贵资源且结果不会随时间变化的方法。与其写成：

```
class Foo(val myInput: String) {
fun getExpensiveThing() =
useLotsOfCpu(myInput)
}
```

不如将其改写为惰性委托：

```
class Foo(val myInput: String) {
val expensiveThing: String by lazy {
useLotsOfCpu(myInput)
}
}
```

使用 `by lazy` 进行委托的好处在于，lambda 表达式只会在第一次调用时执行一次，而方法则会在每次调用时都执行耗 CPU 的代码。

你还可以控制 Kotlin 如何管理来自多个线程对惰性属性的并行访问。你可以在三种线程安全模式中选择：

*   `NONE`：线程之间无同步。如果多个线程在属性尚未初始化时同时尝试访问它，其行为是未定义的。
*   `PUBLICATION`：允许多个计算并行运行，但会使用第一个成功完成的计算结果。此后的进一步调用不会导致计算再次执行。
*   `SYNCHRONIZED`：通过锁保护计算，确保其只运行一次。同时发生的调用会等待第一个调用完成。

默认模式是 `SYNCHRONIZED`。

委托是 Kotlin 中一个深奥的主题，你可以用它做很多事情，不仅仅是惰性初始化。例如，你可以将类委托给一个对象实例，从而有效地从一个实例继承类，而不是进行子类化。你可以在官方 Kotlin 文档中阅读更多相关内容：[*https://kotlinlang.org/docs/delegated-properties.xhtml*](https://kotlinlang.org/docs/delegated-properties.xhtml)。

## 内联函数

你可以通过创建*内联函数*，使 Kotlin 中的函数实现零开销。

内联函数还有一个好处，即它们不会以任何方式改变调用上下文。一个很好的例子是你在本书中使用的 SQL 库 Kotliquery 中的 `transaction` 函数。事实上，正是笔者向 Kotliquery 提交的、在第 6 章中提到的拉取请求（[*https://github.com/seratch/kotliquery/pull/57*](https://github.com/seratch/kotliquery/pull/57)），将 `transaction` 函数改成了内联函数。清单 16-1 展示了该函数实现的大致轮廓。

```
fun  Session.transaction(
block: (TransactionalSession) -> A
): A {
try {
connection.begin()
return block(TransactionalSession(connection)).also {
connection.commit()
}
} catch (e: Exception) {
connection.rollback()
throw e
}
}
清单 16-1
Kotliquery 中 transaction 函数实现的大致轮廓
```

这段代码运行良好，但有一个问题：事务块内部的代码会破坏协程上下文。Kotlin 无法静态地知道一个函数如何使用你传递给它的 lambda。因此，Kotlin 不能假设一个函数会内联地调用与其声明上下文相同的 lambda。以下代码会出错：

```
runBlocking {
dbSess.transaction { txSess ->
delay(1)
txSess.run(...)
}
}
```

这个问题的解决方案是将函数标记为内联。这样 Kotlin 就知道该函数也会内联地调用 lambda。可以编写不内联调用 lambda 的内联函数，但如果出现这种情况，你需要显式地用 `noinline` 或 `crossinline` 标记 lambda。

通过在 Kotliquery 的 `transaction` 函数中添加 `inline` 关键字（`inline fun <A> Session.transaction(...`），代码就能正常运行，因为 Kotlin 编译器会像处理 lambda 外部的代码一样，处理传递给 `transaction` 的 lambda 中的代码。

内置的作用域函数，如 `apply`、`with`、`also` 等，都是内联函数。这就是为什么它们也能在任何上下文中工作，并且不会因为表达性或性能而带来任何编译时或运行时的开销。

## 具体化泛型

具体化泛型（或具体化类型参数）解决了 Java 平台上一个常见的陷阱：泛型类型仅在编译时存在。这通常被称为*类型擦除*，在编译后的字节码中不会留下泛型类型的任何痕迹。因此，如果你有一个 `List<String>`，所有字节码和 Java 运行时只知道它是一个 `List`。检查其元素是否为字符串的类型检查，是在编译器生成最终输出之前的编译阶段完成的。

Kotlin 无法神奇地绕过 Java 平台的限制。但 Kotlin 确实有一个解决方案。关键在于内联函数，并且你只能在内联函数中使用具体化泛型。

编译器会从编译输出中移除（*内联*）内联函数。Kotlin 编译器会将函数体复制到你代码中调用它的每个位置。这意味着它可以用调用函数时传递的实际类型来替换泛型类型。因此，对于内联函数来说，具体化的泛型类型只是一个快捷方式，用于将那段代码手动复制到所有调用它的地方，并用调用点使用的实际类型替换泛型。

例如，假设你想编写一个辅助函数，它接收一个 JSON 字符串并将其序列化为正确的类型。清单 16-2 展示了你可以如何尝试实现它。

```
fun  serializeJson(json: String): T =
Gson().fromJson(json, T::class.java)
清单 16-2
尝试为 JSON 序列化编写辅助函数
```

然而，这段代码无法编译。这是因为类型擦除。当这段代码运行时，`T` 的类型信息就丢失了。因此，`serializeJson` 无法知道 `T` 的类型是什么。

但是，如果你将其编写为一个带有具体化泛型的内联函数，编译器就拥有了使其工作所需的一切信息。清单 16-3 展示了其工作原理。

```
inline fun  serializeJson(json: String): T =
Gson().fromJson(json, T::class.java)
清单 16-3
使用具体化泛型为 JSON 序列化编写辅助函数
```

代码是相同的，除了 `inline` 和 `reified` 关键字。由于编译器内联了该函数，它就知道泛型的类型是什么，因为 Kotlin 在编译时（而非运行时）解析内联函数的类型。



## 契约

你是否曾好奇 Kotlin 是如何做到这一点的？

```
val foo: MyFoo? = createFoo()
assertNotNull(foo)
foo.doSomething()
```

如果你仔细审视这段代码，会发现一些奇怪的地方。`foo` 的类型是 `MyFoo?`，这意味着它可以是 `null`。但你仍然可以在不检查 `foo` 是否为 null 的情况下调用 `foo.doSomething()`。在可空类型上调用方法本应因空安全而引发编译错误！这是什么魔法？

这是因为 `assertNotNull` 实现了一个*契约*。

这是没有契约的 `assertNotNull` 的样子：

```
fun <T> assertNotNull(
    actual: T?,
    message: String? = null
): T {
    asserter.assertNotNull(message, actual)
    return actual!!
}
```

这段代码运行良好，并返回一个非空类型。如果值为 `null`，你会从非空断言运算符（`!!`）得到一个 `NullPointerException`。但这并没有解开谜团。我们没有使用 `assertNotNull` 的返回值，所以即使 `assertNotNull` 在传入 null 时抛出异常并返回非空类型，对我们的程序来说也无关紧要。

`assertNotNull` 的实际实现如下：

```
fun <T> assertNotNull(
    actual: T?,
    message: String? = null
): T {
    contract { returns() implies (actual != null) }
    asserter.assertNotNull(message, actual)
    return actual!!
}
```

注意新增的 `contract` 这一行。契约是一种告诉类型系统那些它无法自行推断、但却是真实陈述的方式。从技术上讲，可以创建一个在实现中不成立的契约。但如果正确使用，契约是对类型系统的强大补充，它使得第三方库（例如 `kotlin.test` 中的断言库）能够扩展内置的类型检查。

这里使用的特定契约表明：如果函数返回了一个值（即没有抛出异常），则意味着参数 `actual` 是非空的。然后，Kotlin 类型系统就可以将类型 `MyFoo?` 智能转换为 `MyFoo`。你已经见过在 if 语句中通过检查 null 来进行智能转换，而契约允许第三方函数实现同样的效果。

请注意，契约是一个实验性 API。这意味着它将来可能会发生变化，并且你必须使用 `@OptIn(ExperimentalStdlibApi::class)` 显式选择启用才能使用它们。这种选择启用机制是为了防止你意外使用不稳定的 API，这些 API 可能会在未来的 Kotlin 版本中发生变更，从而确保你的代码面向未来。

## 远不止于此

Kotlin 语言充满了各种技巧和构造，旨在让你的开发生活更美好。那么，为什么这一章这么短呢？

我努力将尽可能多的 Kotlin 技巧融入本书的其他章节中。本章只包含我认为对 Web 应用开发者来说重要或至少有趣的内容，而这些内容我未能在关于构建实际 Web 应用功能的章节中涵盖。

在第 3 章中，你学习了数据类、作用域函数、可空性与空安全以及 Elvis 运算符。在第 4 章中，你学习了密封类、函数类型、解构和扩展函数。在第 5 章中，你学习了函数引用以及可关闭对象上的 `use` 作用域函数。在第 6 章中，你学习了可空类型的智能转换、伴生对象以及普通 Kotlin 函数中的泛型。在第 7 章中，你学习了不安全转换运算符和内联函数。在第 8 章中，你几乎学习了关于协程用法和内部机制的所有知识。在第 9 章中，你学习了安全调用运算符、运算符重载、DSL、对象表达式以及扩展函数和默认接收者的优先级规则的一些关键细节。在第 13 章中，你学习了使用 `lateinit` 实现空安全以及使用 lambda 代替对象字面量。

所有这些技巧都是本章的绝佳候选内容。但我已经介绍过它们了，所以没有必要在此重复。

如果你想深入探索 Kotlin 提供的所有功能，请查阅 Tim Lavers 的著作 *Learn to Program with Kotlin*（Apress, 2021）、官方 Kotlin 文档（[*https://kotlinlang.org/docs/home.xhtml*](https://kotlinlang.org/docs/home.xhtml)）以及你在互联网上能找到的众多课程、资源和书籍。

Kotlin 语言有很多方面，还有很多我在这本书中没有提及的语言特性。但是，当你学习从头开始构建专业的 Kotlin Web 应用时，重点应该放在那些有助于交付实际价值的语言特性上。而我相信这正是本书所实现的。



索引 A 抽象 累加器 也作为函数 API 调用 避免自动序列化 内部 *vs.* 外部 API 常规路由 解析输入 验证和提取输入 API 密钥 app.conf app.css <appender> appEngineEnvironment AppLayout 模板 Application 插件 ApplicationCall ApplicationCall().-> Unit ApplicationContext 类 app-local.conf app-production.conf Arrow assertEquals(2, users.size) assertNotNull assertNull async() 认证拦截器 authenticateUser 函数 认证 cookies JWT 插件 提供者 auth-session AutoCloseable 自动补全 自动数据序列化 await() AWS Aurora Serverless AWS Lambda AWS API Gateway 云提供商 控制台 创建函数 部署到 AWS Lambda 编辑运行时设置 执行结果 GetTestHelloWorld handleCoroutineTest handleRequest handleUserEmailSearch 输入参数 JAR 文件 kotlinbook.GetTestHelloWorld kotlinbookTestFunction Ktor 处理器 MainServerless.kt 映射 WebResponse RequestHandler RequestHandler 接口 serverlessWebResponse 函数 shadowJar Gradle 任务 测试 从 Azul Zulu 上传 B 向后兼容的迁移 Bcrypt bcryptHasher Beans 黑盒测试 蓝/绿环境 BootstrapWebApp 类 build.gradle.kts 业务逻辑 ByteArray ByteBuffer C call 参数 call.receiveParameters() call.receiveText() call.respondHtml call.respondHtmlTemplate call.sessions 不区分大小写的标头 键 多种大小写 Pair set-cookie cause 参数 challenge 块 检入的配置文件 类文件 *org/slf4j/impl/StaticLoggerBinder.class* 类层次结构 Gradle clean 任务 close() 方法 componentN() 函数 ConfigFactory.load() config.getBoolean(“httpPort”) config.getInt(“httpPort”) config.getInt(“http proot”) config.httpPort config.httpProot Config 对象 ConfigurationFactory 类 配置 尽早失败且类型安全 Kotlin 空安全 Kotlin 平台类型 let 块 加载配置 数据类 存储配置 Web 应用 数据类 配置文件 默认值 环境 特性 读取值 结构 连接池 连接 URL 构造函数参数 容器 ContentType 实例 contextInitialized 函数 Continuation 挂起函数 契约 cont.resume(Unit) 便捷函数 标头 参数 函数重载 header 函数 header(String, List<String>) header(String, String) 标头值 List<String> 映射和列表 WebResponse 基于 Cookie 的认证 基于 Cookie 的会话存储 cookieEncryptionKey Cookies cookieSigningKey cookieSigningKey (String) copyResponse 协程内部 与 arrow 比较 Java 平台实现 Kotlin *vs.* KotlinX 底层延续 协程与阻塞调用 上下文/函数 delay() 外部服务 Ktor 处理器 并行化服务调用 挂起 线程问题 Web 应用准备 coroutineScope createAndMigrateDataSource() createAppConfig createContinuation 函数 createDataSource() createKtorApplication() createStatement() createUser 跨域资源共享 (CORS) CSS 文件 ctx.setResponseCode() D 数据库连接 数据库密码 数据库模式 CREATE TABLE 语句 创建 Flyway Flyway 迁移 迁移库 DataSource dbSavePoint 函数 dbSavePoint lambda dbSess 参数 解耦 Web 处理器 HTTP 响应抽象 Ktor 特定库 defaultPage 默认值 覆盖 Deferred<T> delay() 函数 DelegatingFilterProxy 委托 DELETE 语句 Dependencies 块 部署设置 解构赋值 不同的默认值 Dispatchers.IO 上下文 Dockerfile Docker 镜像 领域特定语言 (DSL) 双感叹号 E Eclipse IDE Either 实例 Elvis 运算符 embeddedServer() 空实现 环境特定配置文件 EnvOrSystemPropertyPreprocessor 异常处理器块 异常处理 executeQuery() 执行时间 扩展函数 参数 函数类型 lambda 面向对象编程 接收者类型 String webResponse F FactoryBean 类 尽早失败 虚假服务实现 回退配置 filterChain 函数 金融交易数据 FlowOrHeadingContent Flyway Flyway 迁移文件 flyway_schema_history 表 fold 函数 forEach() formLogin() 框架约定 fromRow <form> 标签 “全功能” 测试 全栈解决方案 函数式数据驱动核心 函数 header(String, List<String>) 函数引用 返回函数的函数 空骨架版本 handler lambda webResponse 需要 函数 start() G getAwsLambdaResponse getColumnName getConnection() GET /login getObject() getOrDefault getRandomBytes(16) getRandomBytes(32) GetTestHelloWorld 处理器函数 getUser GitHub GraalVM Gradle Gradle 控制面板 Gradle 依赖 Gradle 设置 ./gradlew application Gson 库 H h1 函数 H2 haltHere handleCoroutineTest() 函数 handleCreateOrder 函数 handleGetFoo 函数 handler lambda 函数 处理失败的迁移 生产环境失败 本地失败 手动执行迁移 重新运行 headerName headers() 函数 硬编码的 createUser Header(String, List<String) header(String, String) HeaderValue 辅助函数 六边形架构 高质量日志记录 错误消息 Java 平台 生产级 Web 应用 HikariCP HikariCP 连接池 HOCON 文件格式 Hoplite 环境变量 JVM 目标版本错误 加载配置文件 屏蔽配置值 Typesafe Config hrefLang HTML 生成 添加自定义标签 DSL 能力 kotlinx.xhtml DSL Ktor HTML DSL 运算符重载 HtmlWebResponse 抽象 扩展函数 优先级 实现 使用 HTTP API 响应 httpPort HTTP 响应抽象 不区分大小写的标头 便捷函数 标头 解构赋值 fold 函数 多种响应类型 表示 HTTP 响应 HTTP 响应 HTTP 服务器 HttpStatusCode HttpStatusCode.fromValue I 惯用 Java 不可变属性 index.xhtml 文件 初始化时间 初始化时间 *vs.* 执行时间 内联函数 INSERT 语句 INSERT INTO 语句 insert(pageBody) 即时重载 集成开发环境 (IDE) IntelliJ IDEA 自动补全功能 build.gradle.kts 规范 IDE 下载与使用 JDK Kotlin 项目创建 进度条 io.ktor.server.application.Application io.ktor.server.netty.EngineMain.main() isDevMode isSingleton() it.component1() it.component2() it.getBoolean(“useFileSystemAssets”) J Java 归档 (JAR) 文件 Java 开发工具包 (JDK) java.io.Closeable java.lang.AutoCloseable Java 方法 config.getInt(“...”) Java 方法 config.getString(“...”) java.nio.ByteBuffer Java 平台兼容代码 JavaScript JavaScript 框架 Next.js javaSecurityPrincipal javax.* 命名包 JDBC DataSource 接口 JetBrains Jetty Jooby 认证 表单 JWTs Ktor 映射 WebResponse 响应请求 提供静态资源 启动服务器 Jooby joobyWebResponse JSON JsonListWebResponse JsonMapWebResponse JSON 序列化 JSON 序列化器 JsonWebResponse JSON Web 令牌 (JWTs) jUnit 5 避免泄漏测试 行业标准库 kotlin.test 使测试通过 运行失败测试 TDD 编写失败测试 编写 Web 应用测试 jvmTarget K Koin Kooby Kotest Kotlin 数据类 函数式编程 语言特性 元编程 Web 应用创建 kotlinbook KOTLINBOOK_ENV KOTLINBOOK_HTTP_PORT kotlinbook.Main Kotlin 代码 Kotlin 社区 kotlin.coroutines kotlin.coroutines API Kotlin 数据验证库 Kotlin Hello, World! 命名约定 使用 Gradle 运行代码 使用 IntelliJ IDEA 运行代码 编写代码 Kotlin Native Kotlin 空安全 Kotlin 平台类型 Kotlin 程序员 Kotlin 项目创建 IntelliJ IDEA 内置向导 下载 Gradle 构建系统 骨架 GUI JDK 安装 JDK 规范 判断 LTS 版本 新建项目对话框 Kotlin 特定依赖注入 kotlin.test Kotlin 技巧 契约 内联函数 惰性委托 具体化泛型 kotlin.UninitializedPropertyAccessException kotlinx.coroutines kotlinx.xhtml DSL kotlinx.serialization Kotliquery Kotliquery 函数 sessionOf Kotliquery 会话 Ktor Ktor API Ktor call API Ktor 显示 Ktor 嵌入式服务器 Ktor HTML DSL Ktor HTTP 客户端 KtorJsonWebResponse Ktor 路由映射 Ktor 的 embeddedServer() ktor-server-servlet Ktor servlet 包 L Lambda 语言原生库 lateinit lateinit var lateinit var unmigratedDataSource 惰性委托 泄漏测试 内存数据库 使用相对断言 testCreateAnotherUser() testCreateUser user_t 表 使用事务 left() 函数 let 块 库边界与层次 文档 Flyway 数据库迁移 框架 Java Kotlin Ktor 语言 语言原生库 语言 流行度 稳定性 权衡 X 方式 基于库的 Web 应用 <link> 标签 Liquibase Lisp 语言 list() listUsers() load() 方法 loadConfigOrThrow 本地开发环境 logback.xml 配置文件 logback.xml 配置文件 Logback XML 配置文件 已登录用户 LoggerFactory 类 日志配置 第三方代码 XML 配置文件 日志配置 Web 应用启动 环境变量 格式化输出 屏蔽秘密 编写代码 日志实现 /login 登录表单 认证用户 配置会话 cookie 登录 登出 新的扩展函数 使用认证保护路由 会话类型 日志级别 /logout 长时间运行的操作 长期支持 (LTS) 底层 Kotlin 延续 M “魔法” XML 配置文件 main() 函数 Main.kt 文件 MainServerlessKt MainServerlessKt.getDataSource() 管理模式变更 添加额外模式 添加非空列 向后兼容的迁移 异常 手动执行迁移 mapFromRow() 函数 Map 标头 mapOf() 映射 JsonWebResponse 映射 TextWebResponse 映射 *vs.* 数据类 数据类 映射 传递映射 传递单个属性 原始查询数据 类型安全的数据类 metaData 对象 (it) metaData.columnCount 元编程 方法论 前端测试 真实数据库写入 *vs.* 模拟 测试 单元 *vs.* 集成测试 migrateDataSource. getObjectType() migrateDataSource 函数 MigratedDataSourceFactoryBean 类 迁移本地失败 迁移与 H2 迁移脚本 生产环境中的迁移 Monadic 函数式编程 多个构造函数参数 多个同时事务 myapp.homeless myFancyCoroutineFunction() myTag 函数 MyThing.myVal N 命名参数 原生应用 认证 cookies JWT 执行认证 基于 Web 的 SPA 嵌套事务 非空类型 NOT NULL NULL 可空类型 NullPointerException O 官方 Kotlin 文档 伴生对象 操作系统环境 操作系统环境变量 运算符重载 Oracle DB OutgoingContent 实现 P 打包为自包含 JAR 文件 参见自包含 JAR 文件 生产构建 Docker 镜像 部署 运行 Docker 镜像 pageBody pageTitle Pair 并行化服务调用 添加依赖 添加到 Ktor 处理竞态条件 执行 PascalCase 传递单个属性 password_hash 密码哈希 passwordText passwordText.toByteArray(Charsets.UTF_8) 个人身份信息 (PII) /ping Placeholder<BODY> 平台类型 plugins 块 插件 流行度 位置 *vs.* 命名参数 PostgreSQL POST /login 预生成服务器端 HTML 先验假设 private val dataSource “production” 生产级 Kotlin 开发 面向公众的 API pushState 浏览器 API Q 从 Web 处理器查询 避免长时间连接 创建辅助函数 框架与架构 查询设置 安装 Kotliquery JDBC API Kotlin 库与框架 映射库 映射结果 SQL 数据库 类型安全映射 查询 SQL 数据库 执行 映射 *vs.* 数据类 设置 事务 Web 处理器 queryOperation R random() /random_number randomNumberRequest randomNumberRequest.await() rawConfig README 文件 真实世界 Web 应用 registerShutdownHook() 规范 具体化泛型 相对断言 rememberMeKey 可重复迁移 respondHtmlLayout returnGeneratedKey reverseRequest right() 函数 rollback() <root> 日志记录器 路由函数 Kotliquery Row 对象 row.anyOrNull(it) runApp() runBlocking S SameSite ScheduledThreadPoolExecutor 模式迁移库 SecurityContextHolder 种子数据 任意包 可重复迁移 Web 应用 自包含 JAR 文件 执行 胖 JAR Gradle 运行 ./gradlew shadowJar shadowJar Gradle 任务 Shadow 插件 安装 第三方依赖 uberjars sendEmailToUserA sendEmailToUserB 无服务器环境 AWS Lambda 参见 AWS Lambda 分离的 Web 处理器 Node.js 性能改进 冷启动 GraalVM init 块 初始化时间 初始化时间 *vs.* 执行时间 Java 运行时标志 Kotlin/JS Kotlin Native 惰性加载 MainServerlessKt 迁移与 H2 UserEmailSearch 处理器类 分离的 Web 处理器 Ktor 结构 serverlessWebResponse 函数 服务器端会话存储 提供静态文件 ServletApplicationEngine ServletContextHandler sessionOf Sessions 插件 Set-Cookie setSavePoint setUpKtorCookieSecurity setUpKtorJwtSecurity shadowJar Gradle 任务 Shadow 插件 single() 单表达式函数 singlePageApplication 单页应用 (SPA) 认证用户 与 API 一起托管 单独托管 使用 CORS 单个测试函数 SLF4J /something_else 复杂函数式编程 Spek Kotest 多平台 多种测试定义格式 运行单个测试 跳过测试 结构 Spek 测试 测试 Spring Boot Spring Context ApplicationContext 类 Clojure 创建 创建数据源 扩展使用 初始化 Kotlin 中的 lateinit 基于库的 Web 应用 main() 函数 设置 Spring Framework 启动 Web 应用 基于 ThreadPoolExecutor 的数据处理 Spring Framework Spring Security 访问已登录用户 认证用户 配置 过滤器 过滤器设置 Servlet 过滤器 Web 应用 将 Ktor 添加到 Servlet 初始化 Servlet 设置 嵌入式 Jetty *.sql 后缀* SQL 数据库 数据库模式 处理失败的迁移 种子数据 SQL 数据库连接 连接池 连接池设置 创建 数据库驱动 安装 异常 H2 设置 更新 WebappConfig SQL 查询执行 额外操作 创建会话 插入行 多行 位置 *vs.* 命名参数 单行 UPDATE 和 DELETE 语句 static 路由 statusCode StatusPages 存储密码 存储秘密 版本控制 styleLink 调用 suspend 挂起 T 标签接口 Template<HTML> testDataSource 测试驱动开发 (TDD) 好处 getUser 函数 实现代码 测试 编写失败测试 testTx testUserPasswordSalting testVerifyUserPassword TextWebResponse app.conf 配置文件 第三方依赖 第三方 JSON 序列化器 this.pageBody 基于 ThreadPoolExecutor 的数据处理 Thread.sleep() toString() 方法 权衡 传统 Web 应用开发 添加登录表单 添加 webResponse CSS 和资源 HTML 生成 模式与组织 可复用布局 用户 安全 事务函数 TransactionalSession 事务 业务逻辑 创建 描述 嵌套事务 在 Web 处理器中 transform 函数 txSess txSess.connection.rollback() 类型擦除 Typesafe Config 类型安全的事务性业务逻辑 类型安全 U 一元加 underscore_case unmigratedDataSource unsafe 函数 update 函数 UPDATE 语句 updateAndReturnGeneratedKey() 函数 更新迁移 更新可重复迁移 use() useFileSystemAssets UserDetailsService UserEmailSearch 处理器类 User.fromRow(...) User 接口渲染逻辑 UserSession 数据类 用户安全 bcrypt 密码哈希 UserTests.kt useSecureCookie (Boolean) V val 语句 validate 块 ValidationError ValidationError 数据类 值语义 val useFileSystemAssets: Boolean 冗长实现 Visual Studio Code V2 迁移 V3 迁移 V4 迁移 W WebappConfig WebappConfig 数据类 WebappConfig 对象 WebappConfig(4207) Web 应用环境 定义 Web 应用上下文 WebappSecurityConfig 类 基于 Web 的包装器 Web 处理器 环境 HTTP 动词和 URL 路径 语言特性 真实世界 Web 应用 WebResponse 抽象函数 代码库 copy 函数 handler() lambda 标头 标头值 实例 Ktor API Ktor 处理 statusCode 子类 更新 WebResponse 抽象 WebResponse API WebResponse 业务逻辑 WebResponse 数据类 WebResponseDb WebResponse 处理器 WebResponseTx 函数 Web 服务器 Hello, World! 添加 Ktor 库 选择 Ktor Lambda 命名参数 运行应用 提取单独函数 启动 Web 服务器 手动 withContext withFallback() 编写 Web 应用测试 基础 设置 失败测试 使测试通过 X, Y, Z XML 配置文件
