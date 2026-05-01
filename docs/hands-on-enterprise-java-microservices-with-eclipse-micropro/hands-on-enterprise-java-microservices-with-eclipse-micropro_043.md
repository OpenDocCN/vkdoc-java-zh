# 使用转换器进行高级配置

MicroProfile Config 将从其 `ConfigSource` 中读取 Java `String` 对象。但是，它提供了将 `String` 对象*转换*为应用程序中更具体类型的工具。

例如，我们之前描述的 `myUrl` 字段是一个 `URL` 对象。相应的属性 `my.url` 被读取为 `String` 对象，然后在注入之前转换为 `URL` 对象。

如果应用程序使用 `Config` 对象，MicroProfile Config 实现也会将 `String` 对象转换为作为 `getValue` 和 `getOptionalValue` 方法第二个参数传入的类型。这种转换可以使用不同的转换器类型完成：内置转换器、自动转换器和自定义转换器。我们现在将详细讨论它们。

