# 11. Groovy 的精彩之处

本章简要介绍了 Groovy 生态系统中的各种实用框架。其中一些将在后续章节中更详细地描述。



## Web 与 UI 框架

以下这些 Web 和用户界面框架要么构建于 Groovy 之上，要么支持 Groovy。

### Grails^(²⁵)

一个受 Ruby on Rails 启发的 Web 框架。它拥有至少 800 个插件，并采用“约定优于配置”的方法。

### Micronaut^(²⁶)

一个较新的框架，用于构建微服务、无服务器应用或命令行应用，具有内存占用小、启动速度快的特点，并采用编译时注解处理。由 Grails 背后的同一团队构建。

### Griffon^(²⁷)

一个用于构建 Swing 用户界面的框架。它的命令行界面与 Grails 非常相似。例如，`create-app cool -archetype=jumpstart`。

### Vert.x^(²⁸)

一个用于异步应用开发的框架。严格来说它不是一个 Groovy 项目，但你可以使用它。它目前是 Eclipse 基金会的一个项目。^(²⁹)

### Ratpack^(³⁰)

一个用于 JVM 上异步 Web 应用和 Web 服务（微服务）的工具包。构建于 Netty 之上。

## 云计算框架

在当今的编程世界中，云计算已成为主流需求。以下是针对两个流行云平台的两个有用的 Groovy 框架。

### Gaelyk^(³¹)

对 GAE（Google App Engine）的一个抽象；它拥有一个新兴的插件系统。

### Caelyf^(³²)

一个基于 Apache 2 许可的 CloudFoundry 框架。它与 gaelyk 类似。

## 构建框架

没有构建框架，任何项目都是不完整的。Gradle 在过去几年中变得越来越流行，尤其是在构建 Java 项目方面。它也是 Android 和 Grails 项目的默认构建框架。

### Gradle^(³³)

它使用基于 Groovy 的 DSL 来构建项目（也增加了基于 Kotlin 的 DSL），并使用 `build.gradle` 作为主要的构建文件。它拥有非常庞大的插件生态系统，可用于构建任何类型的应用。

### Gant^(³⁴)

类似于 Groovy 中的 Ant。它目前处于维护模式。过去曾被 Grails 和 Griffon 使用。

## 测试框架/代码分析

测试对于任何项目都极其重要，而代码分析有时对大型项目很有用。我们将在后面的章节中介绍其中一个名为 Spock 的框架。

### Spock^(³⁵)

一个非常出色的、基于 Groovy 的 DSL 测试框架，已经存在多年。它能够使用数据表作为测试输入，内置模拟类和接口的能力，以及许多其他功能。

### Codenarc^(³⁶)

Groovy 的静态代码分析工具，自 2009 年以来就已存在。它拥有 Grails 和 Griffon 的插件。

## 并发

如前所述，并发对于高效应用充分利用硬件至关重要。

### GPars^(³⁷)

一个用于 Groovy 的多线程框架。它提供了 fork/join 抽象、Actor、Dataflow、STM 等功能。

### RxGroovy^(³⁸)

这是 RxJava 的一个 Groovy 适配器，RxJava 是一个使用 JVM 上的可观察序列来组合异步和基于事件的程序的库。不过，它已不再是必需的——你可以直接使用 RxJava 库。

## 其他

这些是社区创建的其他工具，用于管理已安装的编程框架和工具，以及创建新项目。

### SDKMAN^(³⁹)

最初称为 Groovy enVironment Manager（GVM）。现在称为 SDKMAN（软件开发工具包管理器），非常酷。它允许你管理多个 Groovy 和非 Groovy SDK 的多个版本，包括 Groovy 本身。

### Lazybones^(⁴⁰)

一个简单的项目创建工具，使用打包的项目模板。可以通过 sdkman 安装。它创建启动各种类型项目所需的基本文件。例如，它有一个 Ratpack 模板。

### GrooCSS^(⁴¹)

一个基于 Groovy 的 DSL，用于创建 CSS。它与 LESS 非常相似，但拥有 Groovy 所能提供的一切。

脚注 1   2   3   4   5   6   7   8   9   10   11   12   13   14   15   16   17

