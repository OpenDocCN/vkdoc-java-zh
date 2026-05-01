# 6. 平台日志记录 API 与服务

日志记录是应用程序开发的一个重要方面。Java 平台上有许多可用的日志记录框架，包括流行的选择，如 Apache Log4j 2 ( [`https://logging.apache.org/log4j/2.x/`](https://logging.apache.org/log4j/2.x/) ) 和 Logback ( [`https://logback.qos.ch/`](https://logback.qos.ch/) )。SLF4J 通常用作不同日志记录实现的门面。Java 也有自己的日志记录实现，即 `java.util.logging` API，它是在 Java 1.4 中添加的。尽管 `java.util.logging` 是 Java 标准库使用的内置日志记录解决方案，但它并不十分流行。大多数 Java 应用程序仍然使用外部日志记录框架。JDK 本身以及在其上运行的应用程序可能使用不同的日志记录框架。如果应用程序错误实际上与 Java 标准库相关（尽管这种错误不太可能发生），那么两个独立的日志记录框架将使错误诊断变得更加困难。在你使用两个独立日志记录框架的情况下，你还需要配置这两个日志记录框架，并且它们的配置很可能存在重复。

在 Java 9 中，现在可以通过使用 `java.lang.System.LoggerFinder` 类，为 JDK 和应用程序使用相同的日志记录框架。`LoggerFinder` 负责管理 JDK 使用的日志记录器。JVM 有一个单一的系统级 `LoggerFinder` 实例，该实例通过服务提供者机制使用 `ServiceLoader` 加载。如果未找到 `LoggerFinder` 提供者，当 `java.logging` 模块存在时，默认的 `LoggerFinder` 实现将使用 `java.util.logging` 作为日志记录框架。如果 `java.logging` 模块不存在，则默认的 `LoggerFinder` 实现会使用 `System.err` 将日志消息输出到控制台。

`LoggerFinder` 的静态方法 `getLoggerFinder()` 返回 JVM 中的单一 `LoggerFinder` 实例。`LoggerFinder` 的子类需要实现抽象方法 `System.Logger getLogger(String name, Module module)`，该方法为给定模块返回 `java.lang.System.Logger` 接口的日志记录器实例。`System.Logger` 有不同的方法来记录不同级别的消息。`LoggerFinder` 还有方法 `System.Logger getLocalizedLogger(String name, ResourceBundle bundle, Module module)`，用于为给定模块返回一个可本地化的日志记录器实例。

`System.Logger` 是应用程序用于记录消息的主要类。表 6-1 展示了 `System.Logger` 的方法。

表 6-1. `System.Logger` 的方法

| 方法 | 描述 |
| --- | --- |
| `String getName()` | 返回此日志记录器的名称。 |
| `boolean isLoggable(System.Logger.Level level)` | 检查此日志记录器是否会记录给定级别的日志消息。 |
| `void log(System.Logger.Level level, String msg)` | 记录一条给定级别的消息。 |
| `void log(System.Logger.Level level, Supplier<String> msgSupplier)` | 记录一条由给定供应者函数生成的给定级别的消息。仅当消息将被记录时才会调用供应者函数。 |
| `void log(System.Logger.Level level, Object obj)` | 通过调用对象的 `toString()` 方法来记录一条给定级别的消息。 |
| `void log(System.Logger.Level level, String msg, Throwable thrown)` | 记录一条给定级别的消息，并附带一个给定的可抛出对象。 |
| `void log(System.Logger.Level level, Supplier<String> msgSupplier, Throwable thrown)` | 记录一条由给定供应者函数生成的给定级别的消息，并附带一个给定的可抛出对象。 |
| `void log(System.Logger.Level level, String format, Object… params)` | 记录一条从格式字符串和可选参数列表生成的消息。该消息使用 `MessageFormat` 中指定的格式。 |
| `void log(System.Logger.Level level, ResourceBundle bundle, String msg, Throwable thrown)` | 记录一条给定级别的本地化消息，并附带一个给定的可抛出对象。 |
| `void log(System.Logger.Level level, ResourceBundle bundle, String format, Object… params)` | 记录一条从格式字符串和可选参数列表生成的本地化消息。 |

`System.Logger` 在 `System.Logger.Level` 中定义了用于日志记录级别的自有枚举。这些级别可以轻松映射到其他日志记录框架中的日志记录级别。这些日志记录级别是 `ALL`、`TRACE`、`DEBUG`、`INFO`、`WARNING`、`ERROR` 和 `OFF`。

## 默认的 `LoggerFinder` 实现

让我们首先仔细看看默认的 `LoggerFinder` 实现。

`java.base` 模块中的 `jdk.internal.logger.LoggerFinderLoader` 类负责加载 `LoggerFinder` 实现。它首先尝试使用 `ServiceLoader` 来加载 `LoggerFinder` 的实现。如果不存在提供者，它会再次使用 `ServiceLoader` 来加载 `jdk.internal.logger.DefaultLoggerFinder` 类的实现。如果仍然找不到任何提供者，它会使用 `DefaultLoggerFinder` 的一个新实例。`DefaultLoggerFinder` 本身是一个日志记录实现，它使用 `jdk.internal.logger.SimpleConsoleLogger` 类通过 `System.err` 将日志消息写入控制台。

`java.logging` 模块中的 `sun.util.logging.internal.LoggingProviderImpl` 类是 `DefaultLoggerFinder` 的一个实现，它将日志记录请求委托给由 `java.util.logging` 创建的基础日志记录器；请参见清单 6-1 中 `java.logging` 的模块声明。

```
module java.logging {
    exports java.util.logging;
    provides jdk.internal.logger.DefaultLoggerFinder with
        sun.util.logging.internal.LoggingProviderImpl;
}
清单 6-1. java.logging 的模块声明
```



## 创建自定义 `LoggerFinder` 实现

现在你可以创建自己的 `LoggerFinder` 实现了。由于 `LoggerFinder` 可以更改系统的默认日志实现，因此当安装了 `SecurityManager` 时，`LoggerFinder` 的实现应首先检查 `loggerFinder` 权限。

这里我将使用 SLF4J 与 Logback 作为日志框架，来演示如何创建自定义的 `LoggerFinder`。在清单 6-2 中，类 `SLF4JLoggerFinder` 就是 `LoggerFinder` 的实现。方法 `checkPermission()` 用于检查所需的权限。在方法 `getLogger()` 中，我使用 SLF4J 的方法 `LoggerFactory.getLogger()` 来创建一个新的 `org.slf4j.Logger` 实例。日志记录器的名称由模块名称和提供的名称共同构成。`getLogger()` 的返回值是类 `SLF4JLoggerWrapper` 的一个实例，如清单 6-3 所示。

```
public class SLF4JLoggerFinder extends System.LoggerFinder {
private static final RuntimePermission LOGGERFINDER_PERMISSION =
new RuntimePermission("loggerFinder");
public SLF4JLoggerFinder() {
this.checkPermission();
}
private void checkPermission() {
final SecurityManager sm = System.getSecurityManager();
if (sm != null) {
sm.checkPermission(LOGGERFINDER_PERMISSION);
}
}
@Override
public System.Logger getLogger(final String name, final Module module) {
checkPermission();
final Logger logger = LoggerFactory
.getLogger(String.format("%s/%s", module.getName(), name));
return new SLF4JLoggerWrapper(logger);
}
}
清单 6-2.
SLF4JLoggerFinder
```

类 `SLF4JLoggerWrapper` 实现了接口 `System.Logger`。在方法 `isLoggable()` 中，它将检查日志级别是否应被记录的任务委托给了被包装的 `org.slf4j.Logger` 实例中的不同方法。例如，如果级别是 `DEBUG`，则委托给方法 `isDebugEnabled()`。`org.slf4j.Logger` 中的日志方法（例如 `trace()` 和 `debug()`）接受的参数与 `System.Logger` 中的方法 `log()` 不同，因此我需要为本地化和消息格式创建适配器。方法 `localizedMessage()` 返回从 `ResourceBundle` 中获取的本地化消息。方法 `doLog()` 仅接收纯文本消息，并根据级别将其委托给 `org.slf4j.Logger` 中的不同日志方法。在 `SLF4JLoggerWrapper` 的方法 `log()` 中，首先准备纯文本消息，然后将其传递给 `doLog()` 进行记录。

```
public class SLF4JLoggerWrapper implements System.Logger {
private final Logger logger;
public SLF4JLoggerWrapper(final Logger logger) {
this.logger = logger;
}
@Override
public String getName() {
return this.logger.getName();
}
@Override
public boolean isLoggable(final Level level) {
switch (level) {
case ALL:
case TRACE:
return this.logger.isTraceEnabled();
case DEBUG:
return this.logger.isDebugEnabled();
case INFO:
return this.logger.isInfoEnabled();
case WARNING:
return this.logger.isWarnEnabled();
case ERROR:
return this.logger.isErrorEnabled();
default:
return false;
}
}
@Override
public void log(final Level level, final ResourceBundle bundle,
final String msg, final Throwable thrown) {
this.doLog(level, localizedMessage(bundle, msg), thrown);
}
@Override
public void log(final Level level, final ResourceBundle bundle,
final String format, final Object... params) {
this.doLog(level,
String.format(localizedMessage(bundle, format), params),
null);
}
private void doLog(final Level level,
final String msg,
final Throwable thrown) {
switch (level) {
case ALL:
case TRACE:
this.logger.trace(msg, thrown);
break;
case DEBUG:
this.logger.debug(msg, thrown);
break;
case INFO:
this.logger.info(msg, thrown);
break;
case WARNING:
this.logger.warn(msg, thrown);
break;
case ERROR:
this.logger.error(msg, thrown);
break;
}
}
private String localizedMessage(final ResourceBundle resourceBundle,
final String msg) {
if (resourceBundle != null) {
try {
return resourceBundle.getString(msg);
} catch (final MissingResourceException e) {
return msg;
}
}
return msg;
}
}
清单 6-3.
SLF4JLoggerWrapper
```

清单 6-4 中该模块的 `module-info.java` 文件使用 `provides` 来声明 `System.LoggerFinder` 的提供者。

```
module feature.logging {
requires transitive slf4j.api;
provides java.lang.System.LoggerFinder
with io.vividcode.feature9.logging.SLF4JLoggerFinder;
}
清单 6-4.
模块声明
```

现在我可以使用日志服务来记录消息。我使用方法 `System.getLogger()` 创建一个日志记录器，然后使用方法 `log()` 来记录消息。这里我使用了一些 Java 标准类，以观察应用程序日志和平台日志是如何结合在一起的。

```
public class Main {
private static final System.Logger LOGGER = System.getLogger("Main");
public static void main(final String[] args) {
LOGGER.log(Level.INFO, "Run!");
try {
new URL("https://google.com").openConnection().connect();
} catch (final IOException e) {
LOGGER.log(Level.WARNING, "Failed to connect", e);
}
}
}
清单 6-5.
使用日志服务
```

清单 6-6 展示了日志。在输出中，你可以同时看到应用程序日志和 JDK 日志。这里我配置了 Logback 以输出所有消息。

```
2017-09-20 21:23:56,202 |  INFO [      main] feature.runtime/Main : Run!
2017-09-20 21:23:56,448 | TRACE [      main] .h.HttpURLConnection : ProxySelector Request for https://google.com/
2017-09-20 21:23:56,450 | TRACE [      main] .h.HttpURLConnection : Looking for HttpClient for URL https://google.com and proxy value of DIRECT
2017-09-20 21:23:56,451 | TRACE [      main] .h.HttpURLConnection : Creating new HttpsClient with url:https://google.com and proxy:DIRECT with connect timeout:-1
2017-09-20 21:23:56,516 | TRACE [      main] .h.HttpURLConnection : Proxy used: DIRECT
清单 6-6.
日志消息
```

## 小结

借助新的平台日志 API 和服务，你现在可以为 JDK 本身和应用程序使用统一的日志实现。这使得错误诊断变得更加容易。在下一章中，我们将讨论响应式流。

