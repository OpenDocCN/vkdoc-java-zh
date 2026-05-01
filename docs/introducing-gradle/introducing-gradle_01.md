# 1. 入门指南

电子补充材料 本章的在线版本（doi:[10.​1007/​978-1-4842-1031-4_​1](http://dx.doi.org/10.1007/978-1-4842-1031-4_1)）包含补充材料，仅供授权用户使用。

传统软件开发通常涉及编写代码、编译代码、运行测试以及打包最终部署或分发的归档文件。随着软件项目日益复杂，诸如运行静态代码分析、条件性资源包含以及执行安全扫描等额外步骤，已成为主流构建与部署流程的一部分。构建自动化工具允许你自动化这些步骤，从而有助于使构建过程可重复且可预测。在本书中，我们将讨论并探索 Gradle（一款流行的构建自动化工具）的特性。

Gradle（[`http://gradle.org/`](http://gradle.org/)）是 Java 构建项目自动化工具家族的最新成员。它基于 Apache 许可证 2.0 开源发布，首个版本（0.7）于 2009 年发布，随后在 2014 年发布了 2.0 版本。在撰写本书时，Gradle 的当前版本为 2.7。Gradle 借鉴了 Ant 和 Maven 等其他现有构建工具的经验教训，因此获得了广泛采用。多个知名项目，如 Android、Spring Framework 和 Hibernate，已将其构建系统迁移至 Gradle。让我们来看看 Gradle 受欢迎的一些原因。

## 声明式依赖管理

大多数 Java 项目依赖其他项目和开源框架才能正常运行。手动下载这些依赖项并在项目中使用时跟踪其版本可能非常繁琐。更复杂的是，这些依赖项可能还有自己的依赖项（称为传递依赖项），需要解析并下载。

Gradle 提供了一种便捷的方式来声明项目依赖项。然后它会自动下载这些依赖项（以及传递依赖项），并允许你在项目中使用它们。这极大地简化了项目依赖管理。需要注意的是，你只需告诉 Gradle **需要什么**，而无需关心**如何实现**。

## 声明式构建

Gradle 使用基于 Groovy（[`http://groovy-lang.org/`](http://groovy-lang.org/)）的领域特定语言（DSL——参见[`https://docs.gradle.org/current/dsl/`](https://docs.gradle.org/current/dsl/)）来声明构建。该 DSL 提供了一组语言元素，可以轻松组合成简单且意图明确的构建脚本。

## 约定优于配置

Gradle 为 Java、Groovy、Web、Scala、Android 和 OSGi 项目提供了合理的默认值和约定。例如，Gradle 建议 Java 项目的所有生产源代码位于 `src\main\java` 文件夹下。同样，它也对测试代码和资源的位置提出了建议。此外，对于 Java 项目，默认任务会自动配置，用于编译代码、运行测试以及生成 JAR 工件。

遵循这些约定可以使构建脚本非常简洁。然而，你并不局限于遵循这些约定。由于 Gradle 的 DSL 基于 Groovy，因此很容易编写 Groovy 代码来调整和偏离这些约定。

## 增量构建

复杂项目常常会遇到构建速度慢的问题，因为构建工具会尝试“清理”并重新构建所有内容。Gradle 通过提供增量构建来解决这个问题，如果任务的输入和输出都没有变化，则会跳过该任务的执行。例如，`JavaCompile` 任务以一组 Java 源文件作为输入，并生成一组类文件。Gradle 利用此信息检查源文件是否已更改，如果未检测到更改，则该任务会被跳过。

## Gradle Wrapper

Gradle Wrapper 在 Windows 环境中是一个批处理文件（`gradlew.bat`），在 Linux/Mac 环境中是一个 shell 脚本。运行时，包装器脚本会在机器上下载并安装一份全新的 Gradle 运行时，然后执行 Gradle 构建。Gradle Wrapper 使得快速搭建新的持续集成（CI）服务器变得容易，这些服务器无需任何额外配置即可运行构建。包装器还使得分发代码和与他人协作变得简单，因为接收者可以轻松地构建它。

## 插件

Gradle 使得通过插件来增强和自定义其功能变得容易。插件是可分发的组件，封装了可重用的构建和任务逻辑。使用插件，可以支持其他语言、创建新任务、修改现有任务功能，并通过添加新关键字来扩展构建语言。使用 Gradle，你可以轻松创建自己的插件，从而能够集成特定于你组织的任务和工作流。

## 开源

Gradle 是开源的，下载和使用完全免费。它附带丰富的在线文档，并得到活跃社区的支持。此外，Gradle Inc. 为 Gradle 生态系统提供咨询和商业支持。

## Gradle 的替代方案

在深入探讨 Gradle 之前，先了解一下它的几个替代方案——Ant + Ivy 和 Apache Maven。

### Ant + Ivy

Apache Ant（[`http://ant.apache.org`](http://ant.apache.org/)）是一款流行的开源脚本化构建工具。它于 2000 年发布，是 Java 生态系统中首个“现代”构建工具。该框架基于 Java，并使用可扩展标记语言（XML）进行配置。Ant 的默认配置文件是 `build.xml` 文件。

使用 Ant 通常涉及定义任务和目标。顾名思义，Ant 任务是需要完成的一个工作单元。典型任务包括创建目录、运行测试、编译源代码、构建 Web 应用程序归档（WAR）文件等。目标只是一组任务。一个目标可以依赖于其他目标。这种依赖关系允许你对目标执行进行排序。清单 1-1 展示了一个简单的 `build.xml` 文件，其中包含一个名为 `compile` 的目标。`compile` 目标包含两个 `echo` 任务和一个 `javac` 任务。

**清单 1-1. 示例 Ant build.xml 文件**

`<project name="Sample Build File" default="compile" basedir=".">`

     `<target name="compile" description="Compile Source Code">`

       `<echo message="Starting Code Compilation"/>`

       `<javac srcdir="src" destdir="dist"/>`

       `<echo message="Completed Code Compilation"/>`

     `</target>`

`</project>`

Ant 不会对你的项目施加任何约定或限制，以其极高的灵活性而闻名。然而，这种灵活性有时会导致 `build.xml` 文件变得复杂、难以理解和维护。

Apache Ivy（[`http://ant.apache.org/ivy/`](http://ant.apache.org/ivy/)）提供了自动化依赖管理，使 Ant 的使用更加愉快。使用 Ivy，你可以在名为 `ivy.xml` 的 XML 文件中声明依赖项，如清单 1-2 所示。将 Ivy 与 Ant 集成涉及在 `build.xml` 文件中声明新目标以检索和解析依赖项。

**清单 1-2. 示例 Ivy 清单**

`<ivy-module version="2.0">`

     `<info organisation="com.apress" module="gswm-ivy" />`

     `<dependencies>`

       `<dependency org="org.apache.logging.log4j" name="log4j-api" rev="2.0.2" />`

     `</dependencies>`

`</ivy-module>`



### Maven

Apache Maven 是当前 Java 生态系统中**最流行**的构建自动化/项目管理工具。Maven 于 2004 年发布，旨在解决 Ant 用户面临的诸多问题。它高度遵循“约定优于配置”原则，并为项目引入了标准目录结构。它还引入了声明式依赖管理，并自动从外部仓库下载所需的依赖。

Maven 使用 XML 来提供项目、依赖元数据和构建配置。这些信息在项目根目录下定义的 `pom.xml` 文件中提供。清单 1-3 展示了一个 Java 项目的 `pom.xml` 文件示例。

**清单 1-3\. 示例 pom.xml 文件**

`<project ExternalRef"> http://maven.apache.org/POM/4.0.0` `"`

`xmlns:xsi="` [`http://www.w3.org/2001/XMLSchema-instance`](http://www.w3.org/2001/XMLSchema-instance) `"`

    `xsi:schemaLocation="` [`http://maven.apache.org/POM/4.0.0`](http://maven.apache.org/POM/4.0.0)

[`http://maven.apache.org/xsd/maven-4.0.0.xsd`](http://maven.apache.org/xsd/maven-4.0.0.xsd) `">`

    `<modelVersion>4.0.0</modelVersion>`

    `<groupId>com.apress</groupId>`

    `<artifactId>test-service</artifactId>`

    `<version>1.0.0-SNAPSHOT</version>`

    `<dependencies>`

        `<dependency>`

            `<groupId>junit</groupId>`

            `<artifactId>junit</artifactId>`

            `<version>3.8.1</version>`

            `<scope>test</scope>`

        `</dependency>`

    `</dependencies>`

`</project>`

在这个 `pom.xml` 文件中，`groupId`、`artifactId` 和 `version` 元素提供了关于正在构建的项目的信息。`dependencies` 元素指明了项目所依赖的工件（本例中为 `JUnit`）。

Maven 的“约定优于配置”虽然有用，但有时可能过于僵化，难以偏离约定。Maven 对 XML 的使用表现力不强，并且在声明复杂的构建逻辑时可能限制过多。

注意

要了解更多关于 Maven 的信息，请参考 Balaji Varanasi 和 Sudha Belida 所著的《Introducing Maven》（Apress，2014 年）([`http://www.apress.com/9781484208427`](http://www.apress.com/9781484208427)）。

## 总结

Gradle 极大地简化了构建过程并实现了构建管理的自动化。本章对 Gradle 进行了简要介绍，并阐述了采用它的主要原因。你还了解了 Gradle 的同类工具——Ant + Ivy 和 Maven。

在下一章中，你将学习启动并运行 Gradle 所需的设置。

