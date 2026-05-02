# 26. 使用 Maven 插件将单元测试与集成测试分离

还记得第 23 章中提到的不同类型的测试具有不同的反馈级别（因此性能也不同）吗？现在让我们在实践中验证这一点。

打开一个终端窗口，进入 `ebook-chat` 目录，并执行以下命令：

```
$ mvn test
```

这将执行 `UnitTestsSuite.java` 类，即所有单元测试。请注意执行这些单元测试的速度有多快。

现在执行以下命令：

```
$ mvn verify
```

这将同时执行 `UnitTestsSuite.java` 和 `IntegrationTestsSuite.java`。请注意集成测试运行所需的时间要长得多。

![A453568_1_En_26_Figa_HTML.jpg](img/A453568_1_En_26_Figa_HTML.jpg) 只有当你按照第 2 章的步骤操作后，此操作才会生效。请注意，集成测试将启动 Cassandra、Redis、MySQL 和 RabbitMQ 的 Docker 容器，因此当你执行 `mvn verify` 命令时，你的机器上不能运行这些容器中的任何一个，否则会导致端口冲突。

![A453568_1_En_26_Figb_HTML.jpg](img/A453568_1_En_26_Figb_HTML.jpg) 你也可以通过调用 `mvn integration-test` 命令来运行集成测试。

在某些情况下，将单元测试与集成测试分开运行可能是个好主意。为了在编写代码时获得更快的反馈（例如，当某些东西崩溃时），你可以根据需要多次运行单元测试，而无需“浪费时间”等待集成测试。（只是在提交代码之前，别忘了至少同时运行一次单元测试和集成测试。）

## 26.1 Maven Surefire 插件

当使用 Apache Maven 管理应用程序构建生命周期时，你可以使用插件来自定义行为。

![A453568_1_En_26_Figc_HTML.jpg](img/A453568_1_En_26_Figc_HTML.jpg) Apache Maven 是可扩展的，因此如果需要，你甚至可以创建自己的 Maven 插件¹。

你可以在构建生命周期的 Maven 测试阶段使用 Maven Surefire 插件² 来执行应用程序的单元测试。以下是聊天应用 `pom.xml` 文件中使用的插件配置：

```
org.apache.maven.plugins
maven-surefire-plugin

**/UnitTestsSuite.java

```

请注意，配置非常简单。它只包含了 `UnitTestsSuite.java` JUnit 测试套件，以便在执行 `maven test` 命令时运行所有单元测试。单元测试的配置就这些！

## 26.2 Maven Failsafe 插件

Maven Failsafe 插件³ 旨在管理集成测试。以下是其配置：

```
org.apache.maven.plugins
maven-failsafe-plugin

**/UnitTestsSuite.java
**/IntegrationTestsSuite.java

```

类似地，它包含了 `UnitTestsSuite.java` 和 `IntegrationTestsSuite.java` JUnit 测试套件，以便在执行 `mvn integration-test` 或 `mvn verify` 命令时运行所有单元测试和集成测试。

脚注 1

[`https://maven.apache.org/plugin-developers/index.html`](https://maven.apache.org/plugin-developers/index.html)

  2

[`http://maven.apache.org/surefire/maven-surefire-plugin/`](http://maven.apache.org/surefire/maven-surefire-plugin/)

  3

[`http://maven.apache.org/surefire/maven-failsafe-plugin/`](http://maven.apache.org/surefire/maven-failsafe-plugin/)

