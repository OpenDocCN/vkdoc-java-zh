# 自定义 ConfigSource 实现

可以在您的应用程序中提供额外的配置源，这些配置源将由 MicroProfile Config 实现自动添加。

您需要定义一个 `org.eclipse.microprofile.config.spi.ConfigSource` 的实现，并为其添加一个 Java `ServiceLoader` 配置，然后将该文件作为 `META-INF/services/``org.eclipse.microprofile.config.spi.ConfigSource` 放入您的应用程序归档文件中。供您参考，以下是一个环境 `ConfigSource` 实现的定义示例：

```
package io.packt.sample.config;import java.io.Serializable;import java.util.Collections;import java.util.Map;import org.eclipse.microprofile.config.spi.ConfigSource;public class EnvConfigSource ...
```

