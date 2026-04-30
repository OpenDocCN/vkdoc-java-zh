# 13. 使用 Spring Context 进行设置、拆卸与依赖注入

在本章中，你将学习如何使用 Spring Context 来管理 Web 应用中的资源，并通过依赖注入将组件连接在一起。

如果你是 Kotlin 新手，以下是本章示例中你将看到的一些语言特性：

*   使用 `lateinit` 实现空安全
*   使用 lambda 代替对象字面量

同样在本章中，你将学习以下关于使用 Spring Context 的知识：

*   以编程方式创建和配置 Spring Context——无需 XML！
*   设置懒加载 Bean
*   初始化 Spring Context 以尽早失败
*   在关闭时优雅地清理所有资源

在本章中，你将学习如何使用依赖注入（而非显式初始化）来编写 Web 应用中的资源，使用 Spring Context——这个流行且无处不在的 Java 依赖注入库。

## 为什么选择 Spring 和依赖注入？

有几个 Kotlin 专用的依赖注入库可供你考虑使用，例如 Koin（[*https://insert-koin.io/*](https://insert-koin.io/)）。但在本章中，你将学习如何直接使用 Java 库 Spring Context。

Spring Context 是 Spring 框架（[*https://spring.io/projects/spring-framework*](https://spring.io/projects/spring-framework)）的一部分，它既是传统 Spring MVC 应用的核心组件，也是更现代的 Spring Boot 应用的核心组件。将 Spring Context 作为一个库来使用并不常见，但我在真实的生产级 Web 应用中多次这样做过，正是因为它对于团队中其他更熟悉框架思维的开发者来说很熟悉。事实上，Spring Context 是 Spring 框架的核心，以至于如果你谷歌搜索 Spring Context，得到的所有链接都是 Spring 框架本身的文档。

Spring Context 的主要接口是 `ApplicationContext` 类，你将使用它来手动配置 Spring Bean，它也是 Spring Boot（[*https://spring.io/projects/spring-boot*](https://spring.io/projects/spring-boot)）中 Spring 放置其所有自动配置 Bean 的地方。

如果你的应用有很多有状态的移动部件，例如队列消费者或基于内存中 `ThreadPoolExecutor` 的数据处理等，那么让 Spring Context 自动按正确顺序完成所有资源的设置和拆卸工作会很方便。事实上，在框架几乎不存在的 Clojure 语言社区中，人们流行使用 Mount（[*https://github.com/tolitius/mount*](https://github.com/tolitius/mount)）或 Component（[*https://github.com/stuartsierra/component*](https://github.com/stuartsierra/component)）正是为了这个目的。

本书选择 Spring Context 的主要原因是为了弥合传统 Spring 框架世界与这里使用的基于库的方法之间的差距。Spring 框架是一个庞大的系统，但本质上由设计良好、边界清晰的库组成。Spring Context 就是这样一个库，并且很容易以类似库的方式使用它，这非常适合本书——没有隐式的魔法，除非你调用方法或函数来触发事情，否则什么都不会发生。

将 Spring Context 置于基于库的 Web 应用的核心，将使代码的设置和感觉对具有 Spring 框架经验的人来说很熟悉。事实上，你可以使用这种方法将整个遗留的 Spring 框架应用嵌入到基于库的 Kotlin 应用中。我在真实世界的项目中做过这件事。我曾合作过的一个客户对 Java 有严重的过敏反应，没有人愿意碰一个旧的 Spring 框架代码库。为了帮助解决这个问题，我移除了 Java 代码中所有 Spring Web 特定的部分（在一个原本庞大的代码库中，这只有几行代码），创建了一个 Clojure 应用（Kotlin 也同样适用），从 Clojure 中连接了 Spring Context（在这种情况下是 `AnnotationConfigApplicationContext`，以支持注解），然后加载了整个 Java 应用并将其嵌入到基于库的 Clojure 应用中。这样，应用就可以通过移除部分 Java 代码并用 Clojure 重写来逐步重写，而无需对整个 Java 代码库进行彻底的全面重写。并且，由于应用的主入口点是熟悉的 Clojure 代码，Java 过敏症很快就消失了。



## 设置 Spring 上下文

首先需要的是 Spring Context 本身。将其作为依赖添加到 *build.gradle.kts* 中：

```
implementation("org.springframework:spring-context:5.3.23")
```

你将使用 Spring Context 来初始化你的 Web 应用。为了避免与你现有的代码冲突，你将创建一个新的主文件 *MainSpringContext.kt*，将 Spring 初始化与 *Main.kt* 中现有的初始化分离开来。Spring Context 将根据其检测到的组件之间的依赖关系图，按正确的顺序初始化 Web 应用的组件和资源。因此，你不再需要 *Main.kt* 中已有的任何“手动”初始化代码。

Spring Context 的核心是应用上下文对象。这通常是通过 XML 配置文件创建的资源。但在这里，你将通过编程方式设置它。清单 13-1 展示了如何启动并运行基础功能。

```
fun createApplicationContext(appConfig: WebappConfig) =
StaticApplicationContext().apply {
beanFactory.registerSingleton("appConfig", appConfig)
refresh()
registerShutdownHook()
}
清单 13-1
创建并初始化 Spring 上下文
```

这段简短的代码片段展示了 Spring Context 中的一些重要概念。

首先，是 *bean* 的概念。bean 是 Spring 在依赖注入领域中对对象实例的称呼。你将 `"appConfig"` bean 定义为“单例 bean”，这是 Spring 的术语，指的是你提供 bean 的实例，而不是告诉 Spring 如何创建该实例。你已经有一个方便的函数来创建应用配置的实例，因此无需将其包装在 Spring Context 中。

你还在上下文上调用了两个重要方法。`refresh()` 会立即实例化你定义的所有 bean，而不是稍后延迟实例化。稍后，你将在上下文中初始化资源，并且最好让设置在启动时失败，而不是启动正常，但直到你开始调用 Web 应用时（可能是在几秒或几分钟后）才失败。

`registerShutdownHook()` 将设置 Spring Context 对象，以便当 Java 运行时收到关闭命令时，Spring Context 会尝试优雅地关闭。它还会以与启动相反的顺序关闭组件，这样依赖其他组件的组件就不会因为其某个依赖项在父组件关闭之前关闭而失败。

## 添加数据源

下一步是在 Spring Context 中初始化你的 Web 应用资源。你将从初始化数据源开始。

当你使用 Spring Context 设置数据源时，需要解决两个问题：创建实际的数据源本身，并确保它在 Spring Context 中的其他 bean 调用它之前完成迁移。

创建数据源本身很简单，只需指定类及其属性即可，如清单 13-2 所示。

```
registerBean(
"unmigratedDataSource",
HikariDataSource::class.java,
BeanDefinitionCustomizer { bd ->
bd.propertyValues.apply {
add("jdbcUrl", appConfig.dbUrl)
add("username", appConfig.dbUser)
add("password", appConfig.dbPassword)
}
}
)
清单 13-2
在 createApplicationContext 内部创建数据源
```

在这里，你以传统方式注册了一个 bean。它不是单例，因此你告诉 Spring Context 如何创建你提供的类的实例以及所创建实例的各种属性。在本例中，你指定将 `jdbcUrl`、`username` 和 `password` 属性设置为 `WebappConfig` 中的相应值。

如果你向 Spring Context 请求一个 `"unmigratedDataSource"` 的实例，你将得到顾名思义的东西——一个原始的 HikariCP 数据源，你可以用它来查询数据库。但在你开始执行查询之前，它不会运行任何待处理的 Flyway ([*https://flywaydb.org/*](https://flywaydb.org/)) 数据库迁移。清单 13-3 展示了如何设置一个额外的 bean 来同时运行你的迁移。

```
class MigratedDataSourceFactoryBean : FactoryBean {
lateinit var unmigratedDataSource: DataSource
override fun getObject() =
unmigratedDataSource.also(::migrateDataSource)
override fun getObjectType() =
DataSource::class.java
override fun isSingleton() =
true
}
// 在 createApplicationContext 内部：
registerBean(
"dataSource",
MigratedDataSourceFactoryBean::class.java,
BeanDefinitionCustomizer { bd ->
bd.propertyValues.apply {
add(
"unmigratedDataSource",
RuntimeBeanReference("unmigratedDataSource")
)
}
}
)
清单 13-3
一个运行迁移的 FactoryBean 类及其 bean 声明
```

这里发生了几个重要的事情。

首先是 `MigratedDataSourceFactoryBean` 类本身。创建这个类的原因是为了有一个地方来调用运行 Flyway 数据库迁移的代码。这个类的实现声明了 Spring Context 创建 bean 实例所需的一切。`getObject()` 被设置为执行 `migrateDataSource` 的实际调用。`getObjectType()` 告诉 Spring 你的 bean 创建的是哪个类的实例。`isSingleton()` 被设置为 `true`，这样 Spring 就不会创建超过一个此 bean 的实例，而是在整个 Web 应用中重用同一个数据源。

当你注册 `"dataSource"` bean 时，你使用了 `MigratedDataSourceFactoryBean` 类，并将一个 `RuntimeBeanReference` 传递给 `"unmigratedDataSource"` 属性。运行时 bean 引用允许 Spring Context 延迟创建 bean 实例，并让 Spring Context 完全控制其管理的对象的初始化顺序。如果你在注册 `"dataSource"` 的 bean 声明时以某种方式获得了 `"unmigratedDataSource"` 对象的完整实例，那么 Spring Context 将不得不创建并完全初始化该对象的一个实例。运行时 bean 引用只是一个命名引用，它允许 Spring Context 稍后根据其判断创建实际对象。



## Kotlin 中的 `lateinit`

清单 13-3 最有趣的地方在于 `lateinit` 这个关键字。

Spring Context 初始化对象的方式有一个大问题：它使用属性而非构造函数参数。当你手动配置 bean 实例化时，从技术上讲，可以将 bean 引用作为构造函数参数传递。但这需要额外的工作，并且一旦你尝试使用像 `@Autowired` 这样的注解（这是添加到 bean 属性上的注解），这种方式就会失效。

其后果是，Spring Context 设置的所有属性都必须是可空且可变的。如果你将属性声明为 `val String myProp`，除非编译器能保证 `myProp` 由该类的构造函数设置，否则 Kotlin 将无法编译该类，这意味着 Kotlin 可以强制执行其空安全保证。因此，你不得不将所有属性声明为 `var String? myProp = null`，这样属性初始值为 `null`，之后当 Spring Context 遍历你的对象图并初始化所有内容时，再将其设置为一个 `String` 值。

这意味着每次访问 `myProp` 时，你都必须检查它是否为 `null`，或者使用非空断言运算符（`!!`）强制将其转换为非空类型。

这正是 `lateinit` 要解决的问题。

对于那些由于技术原因在类实例化时必须初始化为 `null`，但你知道在能够使用该类实例之前它会被设置为非空值的代码，你可以将属性声明为 `lateinit var`。这允许你覆盖 Kotlin 的编译时检查，并告诉类型系统：“我保证在使用它时，它会是非空值。”

在清单 13-3 中，你将未迁移数据源的引用声明为 `lateinit var unmigratedDataSource: DataSource`。当 Spring Context 在你的 `MigratedDataSourceFactoryBean` 上调用 `getObject()` 时，Spring Context 已经根据你的 bean 声明将 `unmigratedDataSource` 设置为适当的值。换句话说，在 Spring Context 状态机的内部，它绝不会在初始化某个 bean 的所有依赖项之前调用该 bean 上的方法。因此，从实际角度来看，当你的代码运行时，`unmigratedDataSource` 绝不可能是 `null`，所以在 Spring Context 作为 bean 装配过程一部分而初始化的属性上使用 `lateinit` 是安全的。

此外，如果你尝试访问一个尚未设置的 `lateinit var`，Kotlin 会立即抛出一个 `kotlin.UninitializedPropertyAccessException`。这样你就不会得到 `null` 返回值，也不会意外地将空值传递到系统的其他部分。

这是 Kotlin 类型系统实用性的一个例子，也是我在整个 Kotlin 语言中最喜欢的特性之一。Kotlin 在处理可能为 `null` 的值时非常明确，但它仍然允许你成为一个负责任的程序员，即使在技术上某个值可能为 `null` 的场景下，也能依赖 Kotlin 的空安全保证。

## 启动你的 Web 应用

你在 `createApplicationContext` 中的 Spring Context 初始化已经准备就绪，因此下一步就是使用它来启动你的实际 Web 应用。

初始化过程与你已经在 *Main.kt* 中拥有的类似。清单 13-4 展示了如何使用你的 Spring Context 启动 Web 应用。

```
fun main() {
log.debug("Starting application...")
val env = System.getenv("KOTLINBOOK_ENV") ?: "local"
log.debug("Application runs in the environment ${env}")
val config = createAppConfig(env)
log.debug("Creating app context")
val ctx = createApplicationContext(config)
log.debug("Getting data source")
val dataSource =
ctx.getBean("dataSource", DataSource::class.java)
embeddedServer(Netty, port = config.httpPort) {
createKtorApplication(config, dataSource)
}.start(wait = true)
}
清单 13-4
MainSpringContext.kt 中的 main() 函数
```

你仍然手动创建 `WebappConfig`。你可以设置 `createApplicationConfig` 使其创建自己的 `WebappConfig`，但能够完全控制你的 Spring Context 将使用哪个确切的数据库 URL 等是很方便的。

`createKtorApplication` 需要普通的数据源才能工作，因此你从 Spring Context 的 `ctx` 对象中提取 `"dataSource"` bean。因为你配置了 `"dataSource"` bean 来同时运行你的迁移，所以你不必显式运行迁移，因为 Spring Context 已经运行了它们。

## 扩展用法

Spring Context 的扩展用法呢？例如，你所有的 Web 处理程序现在都像以前一样工作，通过以纯函数式风格将它们使用的参数作为参数传入。

你可以像这样将 Web 处理程序编写为一个 bean：

```
beanFactory.registerSingleton(
"springContextTestHandler",
webResponse {
TextWebResponse("Hello from Spring!")
}
)
```

然后，你可以像这样为该处理程序添加一个路由映射：

```
get(
"/spring_context_test",
ctx.getBean("springContextTestHandler") as
PipelineInterceptor
)
```

这个解决方案的问题在于，除了将 lambda 包装在 Spring Context 中而不是直接插入 lambda 之外，你并没有真正增加任何额外价值。没有办法将依赖项注入到 lambda 中。

你可以走创建工厂持有者 bean 和其他名词密集型构造的路线，但我倾向于远离这类事情。依赖注入为资源的初始化和销毁增加了价值，但对于 Web 路由处理函数的实现来说，依赖注入增加的唯一价值就是为向函数传递参数这个简单的任务增加了一些额外的仪式感。

