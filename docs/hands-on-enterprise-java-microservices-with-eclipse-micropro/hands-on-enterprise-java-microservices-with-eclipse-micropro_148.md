# 如何 quark 一个生成的 MicroProfile 项目

在我们开始介绍如何 *quark* 一个由 MicroProfile Starter 生成的 MicroProfile 项目的步骤之前，我们首先需要确保在你的环境中安装、定义并配置了 `GRAALVM_HOME`。为此，请遵循以下步骤：

1.  访问 `https://github.com/oracle/graal/releases` 并为你的操作系统下载最新版本的 GraalVM。

2.  将下载的文件解压到你选择的子目录中。顺便说一下，解压会为 GraalVM 创建一个子目录，例如 `/Users/[你的主目录]/graalvm-ce-1.0.0-rc13`：

```
$ cd $HOME
$ tar -xzf graalvm-ce-1.0.0-rc16-macos-amd64.tar.gz
```

3.  打开一个终端窗口，创建一个名为 `GRAALVM_HOME` 的环境变量，例如：

```
$ export GRAALVM_HOME=/Users/[你的主目录]/graalvm-ce-1.0.0-rc13/Contents/Home
```

现在我们已经安装了 GraalVM，我们可以继续介绍如何使用 MicroProfile Starter 来 *quark* 一个生成的 MicroProfile 项目的步骤：

1.  首先，将浏览器指向 [`start.microprofile.io`](https://start.microprofile.io)，然后选择 Thorntail 作为 MicroProfile 服务器。

你也可以利用以下步骤来 *quark* 任何现有的 Java 应用程序。

如果你不记得如何操作，请转到第 2 章，*治理与贡献*，并按照 *MicroProfile Starter 快速浏览* 部分中的说明操作，直到第 5 步，此时 `demo.zip` 文件会下载到你本地的 `Downloads` 目录。

2.  使用你喜欢的解压工具，展开 MicroProfile Starter 在你本地 `Downloads` 目录下生成的 `demo.zip` 文件。如果你的 `demo.zip` 文件没有自动展开，以下是执行此操作的命令（假设是 Linux；对于 Windows，请使用等效命令）：

```
$ cd $HOME/Downloads
$ unzip demo.zip
```

这将创建一个 `demo` 子目录，其下包含一个完整的目录树结构，其中包含使用 Maven 构建和运行 Thorntail 示例 MicroProfile 项目所需的所有源文件。

3.  与其在 `demo` 子目录中进行更改，不如在 `demo` 子目录旁边创建第二个名为 `Qproj4MP` 的目录，如下所示：

```
$ mkdir $HOME/Downloads/Qproj4MP
```

这应该在 `Downloads` 目录中，与你现有的 `demo` 子目录同级，创建一个名为 `Qproj4MP` 的子目录。

4.  将你的目录更改为 `Qproj4MP`，并通过输入以下命令创建一个空的 Quarkus 项目：

```
$ cd $HOME/Downloads/Qproj4MP
$ mvn io.quarkus:quarkus-maven-plugin:0.12.0:create \
 -DprojectGroupId=com.example \
 -DprojectArtifactId=demo \
 -Dextensions="smallrye-health, smallrye-metrics, smallrye-openapi, smallrye-fault-tolerance, smallrye-jwt, resteasy, resteasy-jsonb, arc"
```

5.  在 `Qproj4MP` 目录中，删除 `src` 子目录，并通过输入以下命令将其替换为 Thorntail 示例 MicroProfile 项目中的 `src` 子目录：

```
$ cd $HOME/Downloads/Qproj4MP  # 确保你在 Qproj4MP 子目录中
$ rm -rf ./src
$ cp -pR $HOME/Downloads/demo/src .
```

6.  Quarkus 和 Thorntail 对某些配置和 Web 应用相关文件应放置的位置有不同的期望。因此，为了让 Quarkus 满意，让我们通过输入以下命令来复制一些文件：

```
$ cd $HOME/Downloads/Qproj4MP # 确保你在 Qproj4MP 子目录中
$ mkdir src/main/resources/META-INF/resources
$ cp /Users/csaavedr/Downloads/demo/src/main/webapp/index.html src/main/resources/META-INF/resources
$ cp -p src/main/resources/META-INF/microprofile-config.properties src/main/resources/application.properties
```

我们本可以将这些文件从其原始位置移动过来，但在此示例中我们选择只复制它们。



7.  由 MicroProfile Starter 生成的 Thorntail 示例 MicroProfile 项目，其 `src` 子目录内容已复制到 `Qproj4MP` 中，该项目使用了一个名为 `bouncycastle` 的安全库。原因是生成的代码包含一个 MicroProfile JWT 传播规范的示例，该规范允许你在微服务之间传播安全信息。因此，我们还需要在 Quarkus 项目的 POM 文件中添加两个依赖项，一个用于 `bouncycastle`，另一个用于 `nimbusds`。

在 MicroProfile Starter 的下一个 sprint 版本中，`bouncycastle` 依赖项将从 Thorntail 服务器代码生成中移除。

要添加这些依赖项，请编辑 `$HOME/Downloads/Qproj4MP` 目录下的 `pom.xml` 文件，并在 `<dependencies>` 部分输入以下代码块：

```
 <dependency>
 <groupId>org.bouncycastle</groupId>
 <artifactId>bcpkix-jdk15on</artifactId>
 <version>1.53</version>
 <scope>test</scope>
 </dependency>
 <dependency>
 <groupId>com.nimbusds</groupId>
 <artifactId>nimbus-jose-jwt</artifactId>
 <version>6.7</version>
 <scope>test</scope>
 </dependency>
```

现在，我们已经准备好编译这个“Quarkus 化”的 MicroProfile 项目了。

8.  除了支持构建在 OpenJDK 上运行的 Java 项目外，Quarkus 还支持将 Java 项目一直编译到机器码。输入以下命令，将“Quarkus 化”的示例项目编译为原生代码：

```
$ cd $HOME/Downloads/Qproj4MP # 确保你在 Qproj4MP 子目录中
$ ./mvnw package -Pnative
```

9.  要运行该应用程序，请输入以下命令：

```
$./target/demo-1.0-SNAPSHOT-runner
```

要测试该应用程序，请按照第 2 章（*治理与贡献*）中 *MicroProfile Starter 快速入门* 部分从第 10 步开始的说明进行操作。

10.  如果你想在开发模式下运行这个“Quarkus 化”的项目，请先停止正在运行的进程，然后输入以下命令：

```
$ cd $HOME/Downloads/Qproj4MP # 确保你在 Qproj4MP 子目录中
$ ./mvnw compile quarkus:dev
```

此时，你可以使用你选择的 IDE（例如 Visual Studio Code 或 Eclipse IDE）打开该项目，并开始修改源代码。Quarkus 支持热重载，这意味着，一旦你对源代码进行了任何更改，Quarkus 就会在后台重建并重新部署你的应用程序，以便你可以立即查看并测试更改的效果。此外，如果你在源代码中犯了语法错误，Quarkus 会将有意义的错误信息传播到 Web 应用程序，以帮助你修复错误，从而提高你的工作效率。

11.  如果你想生成一个可执行的应用程序 JAR，请输入以下命令：

```
$ cd $HOME/Downloads/Qproj4MP # 确保你在 Qproj4MP 子目录中
$ ./mvn clean package
```

12.  要运行可执行的应用程序 JAR，请输入以下命令：

```
$ java -jar target/demo-1.0-SNAPSHOT-runner.jar
```

在应用程序 JAR 旁边会创建一个 `lib` 目录，其中包含运行所需的库文件。

我们已经向你展示了“Quarkus 化”一个由 MicroProfile Starter 生成的 MicroProfile 项目的步骤。虽然这些步骤适用于一个特定的生成项目，但你可以使用相同的指令来“Quarkus 化”一个现有的 Java 应用程序或微服务，以便利用 Quarkus 提供的优势，例如低内存消耗、快速启动时间，以及将 Java 代码编译为原生代码，从而使其能够在容器、云和函数即服务环境中高效运行。无论你使用哪种 MicroProfile 实现，MicroProfile 为最终用户提供的一个巨大好处是互操作性。这意味着你可以使用不同 MicroProfile 实现的微服务来设计应用程序，这正是下一节的主题。

