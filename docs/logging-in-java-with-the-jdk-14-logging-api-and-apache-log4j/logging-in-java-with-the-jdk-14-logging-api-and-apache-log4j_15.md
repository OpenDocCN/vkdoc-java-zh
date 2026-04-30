# 定义名为 CONSOLE 的附加器
log4j.appender.CONSOLE=org.apache.log4j.ConsoleAppender
log4j.appender.CONSOLE.conversionPattern=%m%n
```

| **![结束示例](img/_1.gif)** |

|  |

此配置文件将根日志记录器定义为具有 DEBUG 级别，并为其附加了两个名为 `CONSOLE` 和 `FILE` 的附加器。我们定义了一个名为 `com.foo` 的自定义日志记录器，并定义了代表该自定义日志记录器的 Java 类文件。然后，我们为自定义日志记录器附加了一个名为 `FILE` 的附加器。附加器 `FILE` 被定义为 `org.apache.log4j.FileAppender`。`FILE` 附加器写入位于 `user.home` 系统路径下名为 "log.out" 的文件。需要注意的是，log4j 支持 UNIX 风格的变量替换，例如 `${variableName}`。定义的变量名被视为键，并首先在系统属性中搜索。如果未找到，log4j 框架随后会在正在解析的属性文件中查找该变量的值。然后，`CONSOLE` 附加器被分配给 `org.apache.log4j.ConsoleAppender`，定义的转换模式是 `%m%n`，这意味着打印的日志消息后面会跟一个换行符。

### XML 风格配置

使用 log4j，还可以在 XML 文件中定义配置参数，并在启动时将该文件传递给应用程序以配置不同的日志组件。XML 配置遵循一个名为 "log4j.dtd" 的文档类型定义 (DTD)。配置参数和值以标签格式描述。例如，清单 5-1 中列出的配置信息可以用 XML 格式定义如下：

```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE log4j:configuration SYSTEM "log4j.dtd">
<log4j:configuration>
  <root>
    <priority value ="debug" />
    <appender-ref ref="X"/>
  </root>
  <appender name="X" class="org.apache.log4j.ConsoleAppender">
    <param name="conversionPattern" value="%m%n"/>
  </appender>
</log4j:configuration>
```

XML 风格配置和属性风格配置在配置 log4j 方面都非常有效。两者都是灵活且优秀的配置风格。然而，log4j 中的一些组件，如 `Filter`、`AsyncAppender` 等，似乎只能通过 XML 风格配置进行配置。看起来 log4j 正在从属性风格配置向 XML 风格配置演进，并逐渐将 XML 风格配置作为标准。就目前而言，我们需要掌握这两种配置风格。



### 默认初始化

通常，log4j 不会对其运行环境做任何假设。因此，配置 log4j 是应用程序开发者的责任。配置 log4j 本质上是指定日志记录器、附加器、布局等。`LogManager` 类通过一个类级别的 `static` 初始化块，在启动时仅执行一次初始化操作。默认的初始化操作主要包括以下步骤：

1.  `LogManager` 类会查找系统属性 `log4j.configuration`。

2.  如果未定义 `log4j.configuration` 属性，则它会尝试在应用程序类路径中查找名为 `log4j.properties` 的资源。

3.  然后，它会尝试将已定义的配置资源字符串转换为 `URL` 对象。如果资源字符串无法转换为有效的 `java.net.URL` 对象，则会抛出 `java.net.MalformedURLException` 异常。

4.  如果找不到任何资源，则初始化过程终止。

5.  如果找到了资源，并且它是一个包含键值格式配置信息的普通 Java 属性样式文件，则使用 `org.apache.log4j.PropertyConfigurator` 类来解析该配置文件。如果配置文件是 XML 文件，则使用 `org.apache.log4j.xml.DOMConfigurator` 类来解析 XML 文件并初始化日志记录对象。

也可以避免使用像 `LogManager` 这样的默认初始化类，并编写自己的配置类。自定义配置类可以在应用程序启动时作为 `log4j.configurationClass` 指定给 Java 运行时。默认情况下，任何自定义配置类都应实现 `org.apache.log4j.spi.Configurator` 接口。

例如，我们可以使用以下命令分别将配置文件和配置类传递给 Java 运行时：

```
java -Dlog4j.configuration=config file yourApp
java -Dlog4j.configurationClass=config class yourApp
```

#### 创建配置文件

现在，我们将了解如何编写一个小程序，该程序将借助 `Logger` 对象打印非常基本的日志信息。我们将按照清单 5-3 中的定义创建自己的配置属性文件，将其命名为 "log4j.properties"，并将其作为系统属性传递给 Java 运行时。

清单 5-3: log4j.properties

| **![开始示例](img/_1.gif)** |

```
