# Helidon

Oracle 公司是开源 Helidon 项目的赞助商，该项目实现了 Eclipse MicroProfile 规范。Helidon 是一组 Java 库，使开发者能够编写微服务。它利用了 Netty（一个非阻塞 I/O 客户端服务器框架）。Helidon 是一个应用程序组装器，因为它会生成应用程序 JAR。构建好应用程序 JAR 后，你可以使用以下命令执行它：

```
$ java -jar <可执行 JAR 文件>
```

Helidon 有两种版本：SE 和 MP。Helidon SE 是所有 Helidon 库提供的函数式编程风格，它提供了一个名为 MicroFramework 的微服务框架。Helidon MP 实现了微服务的 MicroProfile 规范，并且构建在 Helidon 库之上。虽然没有示例项目生成器工具，但 Helidon 提供了丰富且详尽的文档手册。

Helidon 的文档可以在 [`helidon.io/docs/latest/#/about/01_overview`](https://helidon.io/docs/latest/#/about/01_overview) 找到。

Helidon SE 提供了一个 WebServer，这是一个用于创建 Web 应用程序的异步和响应式 API。Helidon MP 提供了一个封装了 Helidon WebServer 的 MicroProfile 服务器实现。

