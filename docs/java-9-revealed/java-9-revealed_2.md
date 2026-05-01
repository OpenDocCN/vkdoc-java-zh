# 货币种类数量：225

Log4jLoggerFinder.getLogger(): [name=javax.management.mbeanserver, module=java.management]

Log4jLoggerFinder.getLogger(): [name=javax.management.misc, module=java.management]

Log4jLoggerFinder.getLogger(): [name=Log4jLogger, module=com.jdojo.logger]

`main()` 方法尝试获取可用货币符号列表并打印其数量。这样做的目的是什么？我稍后会解释其用途。目前，它只是 `java.util.Currency` 类中的一个方法调用。

第 19 章 ■ 平台与 JVM 日志记录

尽管你不应该使用日志记录器来记录应用程序消息，但出于测试目的，你可以这样做。你可以使用 `System.getLogger()` 方法获取平台日志记录器的引用，并开始记录消息。`main()` 方法中的以下代码片段实现了这一点：

```java
Logger logger = System.getLogger("Log4jLogger");

logger.log(TRACE, "Entering application.");

logger.log(ERROR, "An unknown error occurred.");

logger.log(INFO, "FYI");

logger.log(TRACE, "Exiting application.");
```

■ **提示** 在 JDK 9 中，`System` 类包含两个可用于获取平台日志记录器引用的静态方法。这两个方法是 `getLogger(String name)` 和 `getLogger(String name, ResourceBundle bundle)`。两个方法都返回 `System.Logger` 接口的实例。

输出中没有显示这四条消息。它们去哪了？回想一下，你配置了 Log4j 将消息记录到当前目录下名为 `logs/platform.log` 的文件中。当前目录取决于你如何运行 `PlatformLoggerTest` 类。如果你从 NetBeans 运行它，项目目录（即 `C:\Java9Revealed\com.jdojo.logger`）就是当前目录。如果你从命令提示符运行，则由你控制当前目录。假设你是在 NetBeans 内部运行此类，你会在 `C:\Java9Revealed\com.jdojo.logger\logs\platform.log` 找到一个文件。其内容如清单 19-6 所示。

***清单 19-6.*** logs/platform.log 文件的内容

```
2017-02-09 09:58:34,644 ERROR com.jdojo.logger.Log4jLogger [main] An unknown error occurred.

2017-02-09 09:58:34,646 INFO com.jdojo.logger.Log4jLogger [main] FYI
```

■ **注意** 每次运行 `PlatformLoggerTest` 类时，log4j 都会将消息追加到 `logs\platform.log` 文件中。本讨论的其余部分仅显示该程序一次运行的输出。你可以在运行程序前清除日志文件的内容，或者直接删除日志文件，每次运行程序时它都会被重新创建。

日志文件表明只记录了两条消息（ERROR 和 INFO），而丢弃了 TRACE 消息。这与 Log4j 配置文件中的日志记录器级别设置有关，如清单 19-2 所示。

你已在日志记录器中启用了 INFO 级别日志记录：

```xml
<Loggers>
    <Root level="info">
        <AppenderRef ref="Async"/>
    </Root>
</Loggers>
```

每个日志记录器级别都有一个严重程度，它是一个整数。如果你启用了级别为 x 的日志记录器，它将记录所有级别严重程度大于或等于 x 的消息。表 19-1 显示了 `System.Logger.Level` 枚举定义的所有级别名称及其关联的严重程度。请注意，在你的示例中，你使用的是 Log4j 日志记录器级别，而不是 `System.Logger.Level` 枚举定义的级别。但是，Log4j 定义的级别的相对值与此表中显示的*顺序*相同。

第 19 章 ■ 平台与 JVM 日志记录

***表 19-1.** System.Logger.Level 枚举定义的名称和严重级别*

| **名称** | **严重程度** |
| :--- | :--- |
| ALL | Integer.MIN_VALUE |
| TRACE | |
| DEBUG | |
| INFO | |
| WARNING | |
| ERROR | |
| OFF | Integer.MAX_VALUE |

如果你将日志记录器级别设置为 INFO，日志记录器将记录所有 INFO、WARNING 和 ERROR 级别的消息。如果你想记录所有级别的消息，可以在清单 19-2 所示的 Log4j 配置文件中使用 `trace` 或 `all` 作为级别值。

请注意，在输出中，平台类通过调用 `Log4jLoggerFinder` 类的 `getLogger()` 方法请求了三次日志记录器。前两次请求是由 `javax.management` 模块发出的。第三次请求出现在输出中，是因为你从 `PlatformLoggerTest` 类的 `main()` 方法请求了一个日志记录器。

你在日志中看到了自己的消息，但没有看到任何来自 JDK 类的消息。我相信你一定很好奇，想看到日志中来自 JDK 类的消息。毕竟，我一开始举这个例子就是为了向你展示这一点。你如何知道哪些 JDK 类会向平台日志记录器记录消息，以及如何让它们记录消息？没有简单直接的方法可以知道这一点。通过查看 JDK 的源代码并找到用于记录平台消息的 `sun.util.logging.PlatformLogger` 类的引用，我发现 `javax.management` 模块记录了 TRACE 级别的消息。要查看这些消息，你需要将 Log4j 日志记录器的级别设置为 `trace`，然后重新运行 `PlatformLoggerTest` 类。这将在日志文件中记录大量消息。

让我们回到 `PlatformLoggerTest` 类中使用 `Currency` 类的问题上。我使用它是为了向你展示 JDK 类（在本例中是 `java.util.Currency` 类）记录的消息。当你请求所有货币的列表时，JDK 会读取其内置的货币列表以及位于 `JAVA_HOME\lib` 目录下的自定义货币属性文件。在本例中，你使用 JDK 运行此示例，因此 `JAVA_HOME` 指的是 `JDK_HOME`。创建一个文本文件，内容如清单 19-7 所示。

该文件的路径将是 `JDK_HOME\lib\currency.properties`。请注意，该文件只包含一个单词 `ABadCurrencyFile`。使用任何一个单词都可以。

***清单 19-7.*** JDK_HOME\lib\currency.properties 文件的内容

```
ABadCurrencyFile
```

`Currency` 类尝试将 `currency.properties` 文件作为 Java 属性文件加载，该文件应包含 `name=value` 对。此文件不是有效的属性文件。当 `Currency` 类尝试加载它时，会抛出异常，并且该类会向平台日志记录器记录一条错误消息。现在你知道你创建了一个无效的货币文件，因此你可以通过 JDK 类看到平台日志记录器的实际运行情况。

第 19 章 ■ 平台与 JVM 日志记录

让我们再次运行 `PlatformLoggerTest` 类，得到以下输出：

```
Log4jLoggerFinder.getLogger(): [name=javax.management.mbeanserver, module=java.management]

Log4jLoggerFinder.getLogger(): [name=javax.management.misc, module=java.management]

Log4jLoggerFinder.getLogger(): [name=java.util.Currency, module=java.base]
```



