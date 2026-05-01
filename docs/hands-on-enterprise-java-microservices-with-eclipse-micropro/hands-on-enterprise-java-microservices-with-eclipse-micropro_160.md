# Web Shell 命令

接下来，在您要求打开的 Web Shell 终端窗口中，将此项目克隆到您的计算机，`cd` 到 `web` 子目录，然后运行以下命令以开发模式执行 Web 应用程序：

```
mvn clean package
```

构建完成后，要运行 Web 子项目 JAR，请输入以下命令：

```
java -jar target/sample-web-runner.jar
```

应用程序启动并运行后，将您的浏览器指向 Web 应用程序 [`localhost:8080/index.html`](http://localhost:8080/index.html)。在下一节中，我们将详细介绍 Web 应用程序。

