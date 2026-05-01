# 编程模型

你可以通过使用 MicroProfile OpenAPI 编程模型，借助 Java POJO（普通旧式 Java 对象）提供 OpenAPI 元素。完整的模型集在 `org.eclipse.microprofile.openapi.models` 包中描述。你可以在 [`github.com/eclipse/microprofile-open-api/tree/master/api/src/main/java/org/eclipse/microprofile/openapi/models`](https://github.com/eclipse/microprofile-open-api/tree/master/api/src/main/java/org/eclipse/microprofile/openapi/models) 阅读更多相关信息。

你可以使用 `OASFactory` 创建 OpenAPI 树。请参考以下代码块作为示例：

```
OASFactory.createObject(Info.class).title("Weather")
          .description("Weather APIs").version("1.0.0");
```

要引导 OpenAPI 模型树，你可以使用 `OASModelReader` 接口。然后，你可以创建此接口的实现，并使用 `mp.openapi.model.reader` 配置键进行注册。

以下是在 `META-INF/microprofile-config.properties` 中其定义的大致示例：

```
mp.openapi.model.reader=com.mypackage.MyModelReader
```

与静态文件类似，模型读取器可用于提供完整或部分的模型树。要提供完整的 OpenAPI 模型树，应将 `mp.openapi.scan.disable` 配置设置为 `true`。否则，将假定为部分模型。

