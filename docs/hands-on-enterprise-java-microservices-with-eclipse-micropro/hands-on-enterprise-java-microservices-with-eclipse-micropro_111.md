# 配置

MicroProfile OpenAPI 规范利用 MicroProfile 配置规范来配置其参数和值。例如，为了注入配置值，MicroProfile OpenAPI 可以使用默认和自定义的 ConfigSource。

有关 ConfigSource 的更多信息，可以访问 [`github.com/eclipse/microprofile-config/blob/master/spec/src/main/asciidoc/configsources.asciidoc`](https://github.com/eclipse/microprofile-config/blob/master/spec/src/main/asciidoc/configsources.asciidoc)。

有许多可配置项。下表包含了其中一部分：

| **配置项** | **描述** |
| --- | --- |
| `mp.openapi.scan.di sable` | 用于禁用注解扫描的配置属性。默认值为 `false`。 |
| `mp.openapi.servers` | 用于指定全局服务器列表的配置属性，这些服务器... |

