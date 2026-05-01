# Thorntail

Red Hat 是开源项目 Thorntail 的赞助商，该项目实现了 Eclipse MicroProfile 规范。Thorntail 是一个应用程序组装器，它只打包应用程序所需的服务器运行时组件，并创建一个可运行的 JAR（即 uberjar），你可以通过执行以下命令来运行它：

```
$ java -jar <可执行 JAR 文件>
```

Thorntail 不仅兼容 MicroProfile，还可以在你的应用程序中包含 MicroProfile 之外的功能。它引入了“分片”（fraction）的概念，分片是一个特定的库，包含你想要集成到应用程序中的功能。分片作为依赖项被包含在你的应用程序的 Maven POM 文件中。除了 MicroProfile 之外...

