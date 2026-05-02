# 2. 安装 Scala

## 入门指南

有几种方法可以开始使用 Scala。

1.  通过解释器交互式运行 Scala。  
2.  将较短的程序作为 shell 脚本运行。  
3.  使用 `scalac` 编译器编译程序。



## Scala 解释器

在使用集成开发环境（IDE）之前，熟悉一下 Scala 解释器（即 REPL）可能是值得的。

下载最新的 Scala 二进制文件（来自 [`http://scala-lang.org/downloads`](http://scala-lang.org/downloads)）并解压归档文件。假设你已经安装了 Java，就可以直接从命令提示符或终端窗口开始使用解释器。要启动解释器，请导航到解压后的文件夹并输入¹

```
bin/scala
```

你将看到 Scala 提示符。

```
scala> _
```

你可以输入命令后按 `enter` 键，解释器会计算表达式并打印结果。它在一个循环中读取、计算并打印，因此被称为 REPL。

如果你输入 `42*4` 并按 `enter` 键，REPL 会计算输入并显示结果。

```
scala> 42*4
res0: Int = 168
```

在这种情况下，结果被赋值给一个名为 `res0` 的变量。你可以继续使用这个变量，例如，计算 `res0` 的一半。

```
scala> res0 / 2     res1: Int = 84
```

新的结果被赋值给 `res1`。

注意 REPL 还会显示结果的类型：`res0` 和 `res1` 都是整数（`Int`）。Scala 根据值推断出了类型。

如果你将 `res1` 添加到字符串的末尾，没问题；新的结果对象是一个字符串。

```
scala> "Hello Prisoner " + res1  res2: String = Hello Prisoner 84
```

要退出 REPL，请输入

```
:quit
```

REPL 是一个非常实用的工具，可以让你无需费力创建常规项目文件就能试验 Scala。它非常有用，以至于社区早在 2013 年就提供了一个 Java REPL²。有趣的是，Oracle 也紧随其后，在 2017 年的 Java 9 中引入了名为 JShell 的官方 Java REPL。

## Scala 脚本

Scala 的创造者最初试图推广在 Unix shell 脚本中使用 Scala。作为 Unix 环境下 Perl、Groovy 或 bash 脚本的竞争者，它并没有真正流行起来，但如果你愿意，可以创建一个 shell 脚本来封装 Scala。

```
1   #!/bin/sh
2   exec scala "$0" "$@"
3   !#
4   object HelloWorld {
5     def main(args: Array[String]) {
6       println("Hello, " + args.toList)
7     }
8   }
9   HelloWorld.main(args)
```

不必担心语法或脚本的功能（尽管我相信你已经有了相当清晰的概念）。需要注意的重要一点是，一些 Scala 代码被嵌入到了一个 shell 脚本中，而最后一行是运行命令。

你可以将其保存为一个 `.sh` 文件——例如 `hello.sh`——并像这样执行它：

```
./hello.sh World!
```

第 2 行的 `exec` 命令会生成一个进程来调用 `scala` 并传入参数；第一个参数是脚本文件名本身（`hello.sh`），第二个参数是要传递给脚本的参数。整个过程等同于像这样运行 Scala，并将一个 shell 脚本作为参数传入：

```
scala hello.sh World!
```

## `scalac`

如果你愿意，也可以使用 Scala 编译器来编译 `.scala` 文件。

`scalac` 编译器的工作方式与 `javac` 类似。它生成可以直接在 JVM 上执行的 Java 字节码。你可以使用 `scala` 命令来运行生成的字节码。不过，和 Java 一样，你不太可能想通过命令行来构建应用程序。

所有主流的 IDE 都支持 Scala 项目，因此你更可能继续使用你最喜欢的 IDE。我们不打算详细讨论如何在每个主流 IDE 中设置 Scala 项目；如果你熟悉在 IDE 中创建 Java 项目，那么过程会非常相似。

不过，作为参考，这里提供一些入门起点。

*   你可以使用 Maven 和 `maven-scala-plugin` 创建引导项目。
*   安装 `scala` 插件（可在 JetBrains 仓库中找到）后，你可以直接在 IntelliJ IDEA 中创建新的 Scala 项目。
*   类似地，安装 Scala IDE 插件后，你可以直接在 Eclipse 中创建新的 Scala 项目。Typesafe 创建了这个插件，可以从常规的更新站点获取。你也可以直接从 scala-lang 或 scala-ide.org 网站下载捆绑包。
*   你可以使用 SBT 并创建一个构建文件来编译和运行你的项目。SBT 代表 Simple Build Tool（简单构建工具），类似于 Ant 或 Maven，但专为 Scala 世界设计。
*   SBT 也有适用于 Eclipse 和 IDEA 的插件，因此你可以直接在 IDE 中使用它来创建和管理 IDE 项目文件。

脚注 1

如果你不想进入安装文件夹来运行 REPL，可以将 `bin` 文件夹添加到你的路径中。

  2

[`http://www.javarepl.com/`](http://www.javarepl.com/)

