# 为附加器 X 设置布局
log4j.appender.X.layout=org.apache.log4j.PatternLayout
log4j.appender.X.layout.conversionPattern=%m%n
```

| **![结束示例](img/_1.gif)** |

|  |

上述配置将根日志记录器的级别定义为 DEBUG，并指定要使用的附加器为 `X`。接下来，我们将附加器 `X` 定义为引用 `org.apache.log4j.ConsoleAppender` 对象，并将附加器的布局指定为 `org.apache.log4j.PatternLayout`。`PatternLayout` 还需要提供一个转换模式或格式。在本例中，我们提供了转换模式 `%m%n`，这意味着日志消息打印后会跟一个换行符。可以定义更复杂和更具描述性的转换模式，我们将在第 6 章中详细讨论。

|  | 注意  | 在转换模式中，`%m` 代表消息字符串，`%n` 代表换行符。我们将在第 6 章中了解更多关于这些转换模式的内容。 |

更复杂的配置可以为特定的日志记录器附加多个附加器。每个附加器又可以有不同布局，并且该布局可以关联一个转换模式。清单 5-2 是一个更复杂配置文件的示例。

清单 5-2：复杂的 log4j 配置文件

| **![开始示例](img/_1.gif)** |

```
