# Config 对象

`org.eclipse.microprofile.config.Config` 接口是在 Java 应用程序中检索配置的入口点。

有两种方式可以获取 `Config` 实例：

1.  第一种（也是推荐的方式）是使用 CDI 将其注入到代码中：

```
@Inject
private Config config;
```

2.  第二种方式是调用静态方法 `org.eclipse.microprofile.config.ConfigProvider#getConfig()` 来获取 `Config` 实例：

```
Config config = ConfigProvider.getConfig();
```

`Config` 接口提供了两种检索属性的方法：

*   `getValue(String propertyName, Class propertyType)`：如果配置中不存在该属性，此方法会抛出运行时异常。此方法仅应用于**强制**配置项……

