# 从 MicroProfile Config API 读取配置

MicroProfile Config 规范定义了两个对象来读取配置参数的值：

*   `Config`对象，用于以编程方式访问配置值
*   `@ConfigProperty`注解，用于使用**上下文与依赖注入**（**CDI**）注入配置值

让我们详细讨论它们。



