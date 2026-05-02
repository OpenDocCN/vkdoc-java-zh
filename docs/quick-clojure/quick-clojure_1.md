# 1. 什么是 Clojure？

Clojure¹ 是一门函数式、共生且同像性的编程语言。

好吧，作为开场白，我敢肯定你会觉得这有点拗口。那么，让我们稍微拆解一下这句话，重点关注“函数式”、“共生”和“同像性”这几个词，以便理解它们的含义。

如果下面的描述听起来像学术废话，请不要担心；随着我们阅读本书的各章节，我们会更详细地介绍这些概念，你会意识到它们实际上在 Clojure 中有着非常实用的用途。

*   **函数式**：函数是一等公民，并且不鼓励改变状态。
*   **共生**：该语言旨在宿主环境之上运行。
*   **同像性**：“代码即数据”——这有助于实现用于重写语言的宏系统。

注意

Clojure 也支持可选类型标注，² 尽管该特性不在本书的讨论范围内。

通常，在阅读关于 Clojure 的资料时，你会经常看到这些术语（函数式、共生、同像性），因此更好地熟悉它们以及人们在 Clojure 语言语境下使用这些术语时的含义会很有帮助。

Clojure 支持的主要平台是 JVM³（Java 虚拟机）。但 Clojure 也支持任何能够执行 JavaScript 引擎的平台，例如网页浏览器，这通过该语言的一个分支——ClojureScript⁴ 来实现。ClojureScript 是一个将 Clojure 代码转换为 JavaScript 代码的编译器。

Clojure 也能与公共语言运行时（CLR）配合使用，后者是微软 .NET 框架的执行引擎。

注意

在本书中，我们只专注于 Clojure 编程语言的 JVM 实现。

Clojure 是一种 Lisp。⁵ Lisp 是一种最初于 1958 年设计的编程语言。它具有非常独特的语法，使其从其他现代编程语言中脱颖而出。像 Clojure 这样的语言建立在 Lisp 的结构基础之上（我们将在下一章中了解更多）。Lisp 语法优雅而简洁，因此赋予了我们极强的表达能力。如果你不熟悉 Lisp 语法是什么样子，别担心，我们会在后续内容中看到相关示例。

如果你等不及了，那么下面清单 1-1 中的代码片段至少能让你对 Clojure 的样子有个概念（尽管在目前这个阶段，如果你不熟悉 Lisp 和/或 Clojure，它显然没什么意义）：

```
(def base-credentials {:client-config {:protocol "http"}})
(defn cred [{:keys [:Host :HostPort]}]
(assoc base-credentials :endpoint (str Host ":" HostPort)))
(cred {:Host "localhost" :HostPort 8080})
清单 1-1.
Clojure/Lisp 语法演示
```

构成 Clojure 编程生态系统的有趣方面有很多，虽然本书会涵盖大部分真正重要的内容，但值得说明的是，我并不打算包含每一个可能的特性或细节，因为那样会分散本书的重点，对初学者来说也不那么易于接受。

Clojure 有许多复杂的活动部件，因此本书的目的是帮助读者快速上手 Clojure，并能够尝试该语言一些最有趣的特性，而不会被过多的实现细节所困扰。

## 为什么你应该关注？

Clojure 很有趣。不，真的！

但更实际的是，得益于该语言对不可变性的支持、众多的并发机制以及非常强大的核心函数库（再次强调，如果这些词对你来说毫无意义，别担心，我们会在后续章节中涵盖所有这些细节），它使得解决复杂问题的方案变得轻而易举。

你甚至可以通过一种称为“宏”的机制来改变 Clojure 编程语言本身，以满足你的需求。宏是一个非常强大的特性，我真希望它也能在我使用的其他编程语言中可用。如果你想添加语言本身不原生支持的新类型流程控制，没问题；你可以用 Clojure 代码动态地自己编写！我们稍后会看到它的实际应用。

关于 Clojure，一个经常被引用的事实是，其创造者 Rich Hickey 在开始实际构建和实现 Clojure 这项艰巨任务之前，花了两年时间进行语言设计。

这听起来可能像是一个无意义的“趣闻”，但当你开始编写 Clojure 代码后，你很快就会开始理解为什么开发者喜欢反复提及设计这门语言花了多长时间。这意味着 Clojure 能够回顾性地从其他编程语言中挑选所有优点，同时过滤掉之前糟糕的设计决策，最终为你提供一门能够解决许多其他有争议的设计问题的新编程语言。

## 名字的由来？

Clojure 这个词及其来源是 Clojurists 的典型讨论点。不过，考虑到 Rich Hickey（Clojure 的作者）早已确认这个名字是以下内容的组合，我不确定为什么还要讨论：

*   C#
*   Lisp
*   Java

(C)(L)o(J)ure

Clojure 的发音（用作者自己的话说）：

> 完全像 closure，其中 s/j 的发音是 azure、pleasure 等词中的 zh 音。——Rich Hickey（Google Groups 讨论⁶）



## 开始入门

我们首先需要一个 REPL（REPL 是“读取-求值-输出-循环”的缩写）。实际上，REPL 是一种让你执行任意代码片段并查看结果的方式，是一种“交互式开发”的形式。随着学习的深入，你会发现 Clojure 的工作流程与 REPL 的概念紧密结合，因此我们将从这里开始。

你会发现大多数其他语言也都有 REPL：

*   Node
*   Ruby
*   PHP
*   Python

注意

本书大部分时间都将使用 REPL。它初始启动可能较慢，但我们只需执行一次。

Leiningen——直到今天我仍记不清它的正确发音——是 Clojure 的完整项目自动化和构建工具。我将在后续章节深入介绍它的功能，但目前你只需知道它内置了一个我们可以（并且将会）使用的 REPL。请参考 Leiningen 网站⁷获取最新的安装说明。

再次强调，为了能够使用 Leiningen REPL，本书将全程在终端 shell 中进行操作。

对我而言，在 Mac 上，我使用 Homebrew⁸ 包管理器，因为我认为这是安装 Leiningen 最简单的方式。安装 Homebrew 后，你可以使用清单 1-2 中的命令安装 Leiningen。

```
brew install leiningen
清单 1-2.
通过 Homebrew 安装 Leiningen
```

安装完成后，要启动 REPL，请运行命令 `lein repl`。你现在应该会看到类似清单 1-3 的输出。

```
Retrieving org/clojure/tools.nrepl/0.2.12/tools.nrepl-0.2.12.pom from central
Retrieving org/clojure/pom.contrib/0.1.2/pom.contrib-0.1.2.pom from central
Retrieving org/sonatype/oss/oss-parent/7/oss-parent-7.pom from central
Retrieving clojure-complete/clojure-complete/0.2.4/clojure-complete-0.2.4.pom from clojars
Retrieving org/clojure/clojure/1.8.0/clojure-1.8.0.pom from central
Retrieving org/clojure/tools.nrepl/0.2.12/tools.nrepl-0.2.12.jar from central
Retrieving org/clojure/clojure/1.8.0/clojure-1.8.0.jar from central
Retrieving clojure-complete/clojure-complete/0.2.4/clojure-complete-0.2.4.jar from clojars
nREPL server started on port 55415 on host 127.0.0.1 - nrepl://127.0.0.1:55415
REPL-y 0.3.7, nREPL 0.2.12
Clojure 1.8.0
Java HotSpot(TM) 64-Bit Server VM 1.8.0_92-b14
Docs: (doc function-name-here)
(find-doc "part-of-name-here")
Source: (source function-name-here)
Javadoc: (javadoc java-object-or-class-here)
Exit: Control+D or (exit) or (quit)
Results: Stored in vars *1, *2, *3, an exception in *e
user=>
清单 1-3.
启动 Leiningen REPL 的输出
```

从清单 1-3 中我们可以看到，Leiningen 为我们下载了一些依赖项（例如 Clojure 语言版本 1.8.0）。但你也应该注意输出的最后一行：`user=>`。

最后一行表明你的代码将在 `user` 命名空间中运行。我们将在后续章节介绍命名空间的工作原理，但目前只需知道 Clojure 允许你将代码隔离到不同的命名空间中，以帮助隔离特定功能（就像你将文件拆分到不同的文件夹中一样）。

另外值得一提的是，如果你已经安装了 Java，则无需下载/安装 Leiningen 即可访问 REPL。首先，你需要下载并解压 Clojure。在终端 shell 中，执行清单 1-4 所示的命令。

```
java -cp clojure-1.8.0.jar clojure.main
清单 1-4.
直接利用 Java 启动 REPL
```

清单 1-4 演示了如何直接使用 Java 命令启动一个 Clojure REPL 界面，该界面与 Leiningen 提供的类似。但当你赶时间时，也有一些在线测试工具：

*   [`​www.​tryclj.​com/​`](http://www.tryclj.com/)
*   [`​himera.​herokuapp.​com/​index.​html`](http://himera.herokuapp.com/index.html) (ClojureScript)

现在我们已经进入了 Leiningen REPL 环境，让我们输入一些代码看看会发生什么。尝试输入清单 1-5 中的代码，看看输出是否符合你的预期（我知道我们还没学习 Clojure 语法，但我希望在我解释之前，代码本身能足够清晰地让你对结果有个模糊的概念）：

```
(+ 2 2)
清单 1-5.
执行一些基本的 Clojure 代码
```

一旦你在 REPL 中输入清单 1-5 中的代码并按下回车键，你应该会看到这行代码执行的结果。如果你正确输入了代码，你会看到结果是 4。

在整本书中，我会鼓励你通过在 REPL 中输入不同的代码示例来尝试它们，因为这是实验和学习这门语言的最佳方式。

注意

Clojure 代码中的注释用分号 `;` 表示，Clojure 中的一个常见约定是使用两个分号。

好了，这是一个不错且简单的代码片段供你尝试。但这段代码实际上代表什么呢？嗯，在下一章中，我们将开始学习 Clojure 的基本语法，并初步了解它的一些数据结构。但在那之前，让我们先回顾最后一个重要主题来结束本章……



## 文档

如果你在任何时候想查找某个函数，最快的方法是在 REPL 中直接使用 `doc` 函数。

例如，如果我想提醒自己如何使用 Clojure 中的 `inc` 函数来递增数字，我会进入 REPL 并执行清单 1-6 中的代码。

```
(doc inc)
清单 1-6.
查找 inc 函数的文档
```

这会返回给我清单 1-7 中所示的输出。

```
clojure.core/inc
([x])
返回一个比 num 大一的数字。不会自动提升
long 类型，溢出时会抛出异常。另请参阅：inc'
nil
清单 1-7.
inc 函数的文档
```

从这个输出中，我们首先可以看到该函数所在的命名空间（`clojure.core/inc`）。我们将在后面的章节中详细讨论命名空间，但现在值得注意的一点是，Clojure 有一个 `core` 命名空间，它提供了一组标准化的函数供你使用。

我们还可以看到 `inc` 函数接受一个参数（`[x]` 表示的就是这个意思），并且我们还可以阅读提供的描述，以更好地理解该函数的上下文（即它的用途是什么）。

Clojure 的“命名空间”是一种将代码划分到不同文件中的方式。Clojure 中的主要命名空间是 `clojure.core` 命名空间。如果你需要查看不属于 `clojure.core` 的函数的文档，那么你需要在 REPL 中使用 `doc` 函数时包含该命名空间。例如，如果你想查找位于 `clojure.string` 命名空间中的 `blank?` 函数，那么在 REPL 中你应该执行 `(doc clojure.string/blank?)`。

对于 Java 文档（有时你需要这样做，因为 Clojure 偶尔会依赖 Java 库），你需要使用 `javadoc` 函数而不是 `doc`。例如，如果我们想查找 Java 中的 `Formatter` 类（它位于 Java 的 `util` 命名空间中），我们会执行你在清单 1-8 中看到的代码。

```
(javadoc java.util.Formatter)
清单 1-8.
查找 Java 类的文档
```

执行清单 1-8 中的代码会在你的网页浏览器中打开相关的 Java 文档，而不是在终端中显示结果。这不如查找 Clojure 特定功能那么方便，但在编写 Clojure 代码时，你很少需要查找 Java（也许你会在代码中查看堆栈跟踪，并对所引用的类感兴趣时才会这样做）。

## 总结

至此，我们对 Clojure 的背景以及如何在 REPL 中执行代码有了一些了解。在下一章中，我们将开始探索 Clojure 提供的一些数据结构。这些结构帮助我们使用 Clojure 语言实现更多实用功能。

脚注 1

[`http://clojure.org/`](http://clojure.org/)

  2

[`https://github.com/clojure/core.typed`](https://github.com/clojure/core.typed)

  3

[`http://en.wikipedia.org/wiki/Java_virtual_machine`](http://en.wikipedia.org/wiki/Java_virtual_machine)

  4

[`https://github.com/clojure/clojurescript`](https://github.com/clojure/clojurescript)

  5

[`http://en.wikipedia.org/wiki/Lisp_%28programming_language%29`](http://en.wikipedia.org/wiki/Lisp_%28programming_language%29)

  6

Google Groups 讨论 [`https://goo.gl/zs7UxO`](https://goo.gl/zs7UxO)

  7

[`http://leiningen.org/`](http://leiningen.org/)

  8

[`http://brew.sh/`](http://brew.sh/)

