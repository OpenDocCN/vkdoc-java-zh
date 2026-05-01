# @ConfigProperty 注解

`@ConfigProperty` 注解可用于通过 CDI 将配置值注入 Java 字段或方法参数，如下所示：

```
@Inject
@ConfigProperty(name="my.url")
private URL myURL;
```

`@ConfigProperty` 注解可以包含 `defaultValue`，当在底层 `Config` 中未找到配置属性时，将使用该默认值来配置字段：

```
@Inject
@ConfigProperty(name="my.url", defaultValue="http://localhost/")
private URL myURL;
```

如果未设置 `defaultValue` 且未找到任何属性，应用程序将抛出 `DeploymentException`，因为它无法被正确配置。

如果某个配置属性可能不存在，可以使用 `Optional`，如下代码块所示：

```
@Inject
@ConfigProperty(name="my.url")
private Optional<URL> someUrl; // 如果找不到属性 `my.url`，则会被设置为 Optional.empty
```

读取配置后，我们需要提供配置源，这将在下一节中介绍。

