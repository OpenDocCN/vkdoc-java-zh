# Open Liberty

IBM 是开源项目 Open Liberty 的赞助商，该项目实现了 Eclipse MicroProfile 规范。Open Liberty 是 IBM WebSphere Liberty 应用服务器的上游开源项目。Open Liberty 是一个能够生成 uberjar 的应用服务器，该 uberjar 包含你的应用程序以及内嵌的 Open Liberty 服务器。要运行这个 uberjar，你需要输入以下命令：

```
$ java -jar <可执行 JAR 文件>
```

此命令会将 JAR 文件解压到你的用户临时目录中，然后从该目录执行应用程序。

请确保 JAR 文件的路径中没有空格，否则启动过程将失败。

生成的 uberjar 只能包含 `server.xml` 文件中定义的功能所对应的应用服务器功能子集。要使用这组最小功能构建 uberjar，你需要在运行 Maven 时使用 `minify-runnable-package` 配置文件。

Open Liberty 的文档非常详尽，包含了丰富的指南和参考文档。

你可以在 [`openliberty.io/docs/`](https://openliberty.io/docs/) 找到 Open Liberty 的文档。

在他们的文档中，有一个专门介绍 MicroProfile 指南的部分，提供了文档完善的教程。

