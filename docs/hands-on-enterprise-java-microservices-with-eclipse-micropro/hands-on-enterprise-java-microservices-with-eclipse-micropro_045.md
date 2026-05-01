# 自动转换器

MicroProfile Config 还定义了*自动转换器*。如果对于给定的 Java 类型没有已知的转换器，它将尝试使用以下三种不同方法之一将 `String` 对象转换为该类型：

*   Java 类型具有一个接受 `String` 参数的公共构造函数。
*   它有一个 `public static valueOf(String)` 方法。
*   它有一个 `public static parse(String)` 方法。

这就是 `my.url` 属性从 `String` 转换为 `URL` 的方式，因为 `java.net.URL` 类型具有 `public URL(String)` 构造函数。

