# 提供配置源

配置源由 `ConfigSource` 接口表示。除非您想提供应用程序中使用的 MicroProfile 实现所不提供的配置源，否则无需实现此接口。

如果在多个配置源中找到某个属性，`Config` 将返回具有最高 `ordinal` 值的 `ConfigSource` 接口中的值。

对 `ConfigSource` 进行排序很重要，因为除了 MicroProfile Config 实现提供的默认 `ConfigSource` 接口之外，用户还可以提供自定义的 `ConfigSource` 接口。

