# 4. WORKSPACE 文件功能

在上一章中，我们创建并运行了第一个 Bazel 项目，重点基本放在“让它跑起来”所需的最小内容上。在那一章过程中，我们使用了两个具有特殊名称的文件：`BUILD`，以及（程度要小得多的）`WORKSPACE`。虽然 `BUILD` 文件的用途非常明显，但我们（有意地）没有深入 `WORKSPACE` 文件。本章中，我们将探索 `WORKSPACE` 文件更丰富的一组功能。

### 注意

本章将对 `WORKSPACE` 文件做一个高层次概览；不过，要真正开始体会它的实际工作方式，完成练习至关重要。

## WORKSPACE 文件

在上一章中，我们让 `WORKSPACE` 文件保持完全空白。对于那个特定示例来说，我们不需要添加任何其他内容，因为我们只使用了 Bazel 开箱即用提供的全部功能。

具体来说，上一个练习有两个明显特征：

*   所有代码都位于单个本地仓库中。

*   所需的规则和构建目标都来自 Bazel 的开箱即用能力。

但在实际中，这种组合通常对大多数项目并不可行。你*一定*会需要依赖额外功能，而且很可能还会在项目中使用其他语言或其他类型的构建目标。`WORKSPACE` 文件正是为项目所需功能和规则搭建基础舞台的地方。

## 向 WORKSPACE 添加新规则

如前所述，Bazel 开箱即用支持若干构建规则（Rules）。例如，它提供了定义 C++、Java、Python 等语言如何构建、编译、链接等的规则。此外，一个原生 Bazel 项目还定义了一些实用规则，用于规定项目中的资源（例如数据文件）应如何打包和引用。

Bazel 最强大的方面之一，就是能够通过添加新规则来扩展其能力。通过向项目的 workspace 添加新规则，我们可以加入远程依赖获取能力、增加新语言支持等等。

值得注意的是，Bazel 自带了一些规则，但它们默认不会自动加载。这使你作为项目创建者，能够显式控制希望在项目中可用的规则集合。

我们将用来加载新规则的基本命令是 Bazel 内置的 `load`。

### 注意

`load` 命令会同时在 `WORKSPACE` 和 `BUILD` 文件中使用。正如你可能已经猜到的那样，我们最终也会用它把新类型的功能显式引入到 `BUILD` 文件中。

使用 `load` 的基本结构如下：

```
load("//local/path/to/my:file.bzl", "symbol_to_load")
```

这会让 `load` 拉取本地路径中的文件，并将指定符号加载到本地环境中；当它写在 `WORKSPACE` 文件中时，就是把该符号加载到 `WORKSPACE` 的本地环境中。

作为 `load` 的一个实际示例，我们将用它把 `http_archive` 规则引入 `WORKSPACE`。让我们创建一个新的项目目录。不过这次我们稍微“偷个懒”，复制上一章的成果。在复制之前，我们首先需要清理已有的构建输出，以避免意外把它们一并拷过去：

```
$ cd chapter_03
chapter_03$ bazel clean
chapter_03$ cd ..
$ cp -r chapter_03 chapter_04
$ cd chapter_04
```

在上一章中，我们让 `WORKSPACE` 文件保持完全空白，并且完全依赖内置规则。现在，让我们开始向 `WORKSPACE` 文件加入一点新功能。

用你喜欢的编辑器打开 `WORKSPACE` 文件，并写入以下内容。

```
load("@bazel_tools//tools/build_defs/repo:http.bzl", "http_archive")
Listing 4-1
A simple load command
```

将该文件保存到磁盘。

恭喜！你刚刚向 `WORKSPACE` 文件添加了一些功能。当然，这个功能此刻实际上还没有做任何事（我们会在下一节讲到）。

### 注意

细心的读者会再次注意到一种新文件类型：`.bzl`。虽然这超出了本书范围，但你只需知道：`.bzl` 文件用于为 Bazel 定义规则（例如构建规则），并让我们能够扩展 Bazel 的能力（例如添加新语言）。



### 深入解析加载路径

如果你仔细查看前一个示例中的加载路径，可能会注意到一件有趣的事：那个特定路径在你的文件系统中并不存在。那么，它是从哪里来的呢？

路径中的第一个元素是 `@bazel_tools`。`@` 用于向 Bazel 表明你正在从一个特定的 Bazel 仓库加载，该仓库名为 `bazel_tools`。`bazel_tools` 右侧下方的文件路径，则指定了*该仓库内部*某个文件的具体路径。

这是一个重要细节，因为它很快就会变得非常关键。随着你的项目开始引用其他 Bazel 仓库中的功能，你会使用名称来区分这些仓库。这样你就可以为项目所需的构建目标创建绝对路径。

到目前为止，你还没有引入任何外部仓库，那么 `bazel_tools` 实际上是从哪里来的？`bazel_tools` 仓库很特殊，（某种意义上）是“开箱即用”的。

这很关键，因为它自带一些重要功能，其中尤其重要的一项就是能够引入其他仓库。可以把它看作一个引导（bootstrapping）仓库：你只要安装了 Bazel 并创建了 `WORKSPACE`，就会获得它。

### 查找 bazel_tools 仓库

如果你想稍微探索一下，可以在项目中找到 `bazel_tools` 仓库的位置。不过首先，先清理我们现有的项目：

```
chapter_04$ bazel clean
```

现在，如果你尝试在当前项目中定位 `bazel_tools` 仓库，可能会一无所获：

```
chapter_04$ ls -1
WORKSPACE
src
third_party
```

请注意，这里只有我们之前创建的目录和文件；`clean` 命令已经清除了所有构建产物、依赖、输出等内容。此时，这个 Bazel 项目基本上是“未触碰”的状态；实际上还没有执行任何 Bazel 命令。Bazel 的目标是在任意时刻都不下载超出所需的内容；因此，除非绝对必要，它甚至不会把 `bazel_tools` 下载到某个 `WORKSPACE` 中。

现在，让我们重新运行上一章中一个有效的构建命令：

```
chapter_04$ bazel build src:LibraryExampleTest
chapter_04$ ls -1
WORKSPACE
bazel-bin
bazel-chapter_04
bazel-out
bazel-testlogs
src
third_party
```

这一次，我们要更深入地查看生成的文件，尤其是 `bazel-chapter_04`：

```
chapter_04$ cd bazel-chapter_04
chapter_04/bazel-chapter_04$ ls -1
bazel-out
external
src
third_party
chapter_04$ cd external
chapter_04/bazel-chapter_04/external$ ls -1
bazel_tools
(other directories may be here)
```

恭喜！你已经找到了这个仓库。值得注意的是，它所在的位置揭示了 Bazel 的几个重要特性。

### 注意

对于好奇的读者，如果你继续浏览 bazel_tools 目录，会在其中发现 tools/build_defs/repo 目录。你之前加载的 http.bzl 文件就在这里。

首先，你的每个项目都旨在成为各自明确的事实来源。并不存在一个跨所有项目共享的中心位置来放置通用的 `bazel_tools` 仓库；每个项目都应拥有自己版本的仓库（当然，这并不妨碍 Bazel 在幕后通过文件链接共享仓库来做一些优化）。

其次，除非*绝对需要*，否则 Bazel 不会下载依赖。我们会在本章稍后再次讨论这一点；不过即使你在 `WORKSPACE` 文件中新增了外部依赖，如果你从未使用这些依赖中的任何内容，*Bazel 也不会下载它们。* 这正体现了“把一切都显式声明”这一理念的核心：Bazel 因此能够进行一些很酷的优化。

### 同时加载多个规则

在离开这一节之前，值得了解的是：同一个文件中可以包含多个规则。虽然你可以执行多条 *load* 命令来引入所需功能，但你也可以一次性获取所有必需的符号。

其格式如下：

```
load("//local/path/to/my:file.bzl", "symbol_to_load_1", "symbol_to_load_2", "symbol_to_load_3")
```

也就是说，你只需在 load 命令后追加任意多个想从某个文件中加载的符号即可。一个实际示例如下：

```
load("@bazel_tools//tools/build_defs/repo:http.bzl", "http_archive", "http_file")
Listing 4-2
A load command that pulls in multiple symbols
```

这会将 *http_archive* 和 *http_file* 两个符号都加载到你的 workspace 中。

## 引用其他依赖

在上一章中，我们显式下载了 JUnit 库并将其直接加入项目中。这与 Bazel 偏好的模型（即 monorepo）非常契合。

不过，Bazel 还提供了几种不同方式来引用其他外部依赖。这带来了额外的灵活性，使你可以在不显式纳入依赖源码的情况下扩展项目。

在 `WORKSPACE` 文件中，有一些规则可用于拉取项目外部的依赖。实践中，其中两个最常见（你会在各种 Bazel 项目中看到）的是：

*   `http_archive`

*   `git_repository`

请注意，虽然这些规则在早期版本的 Bazel 中曾是开箱即用的，但现在你需要显式加载它们，才能在项目中使用。

这些规则都用于获取远程 Bazel 仓库，并将其包含的 targets 作为你项目可用的依赖。



### http_archive

`http_archive`用于在给定压缩文件路径的情况下，引用并获取一个压缩的 Bazel 仓库。压缩仓库获取完成后，会被解压，其中包含的规则、目标等内容就可以在你的项目中使用。

`http_archive`最基础、最精简的形式如下。

```
http_archive (
name = "foo",
urls = ["http://my_favorite_url.com/path/to/archive.zip"],
)
Listing 4-3
Example http_archive
```

我们来稍微拆解一下前面的代码。上述规则指定从位置*http://my_favorite_url.com/path/to/archive.zip*获取仓库。假设该过程成功，归档文件将被获取、下载并解压（如果之前尚未完成），从而使其内容可供使用。

前面我们讨论过，为了使用该仓库中的任何功能，需要使用标签`bazel_tools`。类似地，为了使用我们新仓库`foo`中的任何功能，我们需要使用标签`@foo`。

为了让这些内容更具体一些，我们来看一个真实的`http_archive`示例。请将以下内容添加到你的`chapter_04 WORKSPACE`文件中。

```
load("@bazel_tools//tools/build_defs/repo:http.bzl", "http_archive")
http_archive (
name = "io_bazel_rules_go",
urls = ["https://github.com/bazelbuild/rules_go/releases/download/v0.19.5/rules_go-v0.19.5.tar.gz"],
)
Listing 4-4
http_archive for Go language rules
```

保存你的`WORKSPACE`文件。

正如你所想，这会拉取 Go 语言规则的压缩仓库并将其解压，使该仓库的目标可供使用。正如我们之前探讨的，这个仓库最终会出现在`chapter_04/bazel-chapter_04/external`目录中（需要注意的是，它不会立刻出现在那里，原因我们在本章前文已经讨论过）。

为了使用仓库中的任何功能，我们需要确保正确指定相应功能。为说明这一点，请将以下内容添加到`WORKSPACE`文件中，放在`http_archive`指令下面。

```
load("@io_bazel_rules_go//go:deps.bzl", "go_rules_dependencies", "go_register_toolchains")
Listing 4-5
Retrieving functionality for Go
```

将其保存到你的`WORKSPACE`文件中。

你应该会注意到，你需要指定`@io_bazel_rules_go`，才能形成正确路径并访问底层功能。

### git_repository

`http_archive`侧重于获取 Bazel 仓库的压缩归档（无论其是否属于某个 SCM 系统），而`git_repository`用于克隆一个 git 仓库，并在指定的提交（或标签）上检出。

同样地，我们先从一个最简示例开始。注意，我们需要像`http_archive`一样，显式加载`git_repository`规则。

```
load("@bazel_tools//tools/build_defs/repo:git.bzl", "git_repository")
git_repository(
name = "foo",
remote = "http://my_favorite_url.com/path/to/repo.git",
commit = "some_commit_hash_to_check_out_repo",
)
Listing 4-6
Loading and using the git_repository
```

在我们拆解过`http_archive`之后，这里有些特性看起来非常相似。在这个例子中，`name`作用完全一致，作为仓库的消歧标签。与`http_archive`的`urls`参数类似，`remote`指定我们想克隆的 Git 仓库路径（例如位于[GitHub.com](http://github.com)之类的平台）。唯一的主要差异是`commit`，用于指定实际要获取的仓库版本。

#### 获取一个 Git 仓库

再次说明，我们用一个真实示例来演示。请将以下内容添加到你的`WORKSPACE`文件中。

```
load("@bazel_tools//tools/build_defs/repo:git.bzl", "git_repository")
git_repository(
name = "io_bazel_rules_go",
remote = "https://github.com/bazelbuild/rules_go.git",
commit = "f5cfc31d4e8de28bf19d0fb1da2ab8f4be0d2cde",
)
Listing 4-7
Retrieving the repository for Go
```

### 注意

这里使用的具体提交哈希仅在本文写作时是最新的；你可能需要到该仓库中查看更新的版本。

在将这段内容保存到你的`WORKSPACE`文件之前，强烈建议你先注释掉相同请求的`http_archive`版本。否则，`http_archive`版本和`git_repository`版本会出现同名。Bazel 会通过取文件中最后一个定义来消歧决定谁“生效”；不过为了清晰起见，你不应在`WORKSPACE`文件的依赖中引入这种歧义。

保存你的`WORKSPACE`文件。

#### 关于 git_repository 的补充说明

尽管`git_repository`会将远程 git 仓库克隆到你的 Bazel 项目中，但这并不意味着你可以像操作普通 git 仓库那样与之交互。也就是说，你不能进入包含该 Git 仓库的目录（例如`bazel-chapter_04/external/<name of git repository>`）并开始执行一套常规 git 操作（例如`commit`、`push`等）。考虑到仓库所在位置，这一点其实也容易理解：所有`bazel-*`目录都是临时性的。它们都可以通过一次`bazel clean`被移除，这很容易清除任何本地创建的更改。

如果你想编辑外部 git 仓库并让变更反映到项目中，一种方式是：单独克隆该仓库，完成并提交你的更改，然后*再*更新项目中的提交哈希，使其匹配新创建的提交。诚然，这可能不是最顺滑的工作流；但请记住，Bazel 始终强调可复现性。对依赖进行显式跟踪，正是 Bazel 强大能力的关键之一。

还有另一种使用`git_repository`的方法：不使用提交哈希，而是引用该提交对应的标签。示例如下。

```
git_repository(
name = "foo",
remote = "http://my_favorite_url.com/path/to/repo.git",
tag = ""
)
Listing 4-8
Using the tag instead of the commit hash
```

这样你就不再锁定到某个特定提交哈希，而是跟踪某个标签；如果你对 Git 仓库进行了更新（并随后更新该标签），你的项目将获得该标签对应的版本。

除了 tag 和 commit 之外，你还可以使用“branch”来引用 Git 仓库的特定分支。

### 说明

你必须在“tag”“commit”或“branch”中选择其一来指向某个特定代码版本；不能同时使用多个。

虽然这会让外部仓库的使用更方便，但相比提交哈希，这种方式提供的保证会**弱得多**。它在开发过程中可能很方便，但在实践中也可能引发问题，因为你依赖的是一种可变的“浮动版本”代码。

### http_archive 与 git_repository 对比

`http_archive`和`git_repository`都是用于引用外部 Bazel 仓库的工具；但这就引出了一个问题：“两者都能用时，我应该用哪一个？”例如，GitHub 同时提供 git 仓库（显然）和归档文件。

默认情况下，Bazel 的建议是优先使用`http_archive`。这很合理，因为它能提供最强的可复现性保证（即给定版本的归档是静态的）。下载并解压归档也比克隆仓库更快。此外，它还省去了为构建项目安装 git 的需求。对于那些预期版本变化较慢的依赖，这尤其是个好主意。



### 注意

严格来说，即使是 http_archive 的 URL，其内容*也可能*发生变化。为了增强可复现性并确保检索到正确文件的保证，还有*另一个*属性 `sha256`，它包含要检索的归档文件预期的 SHA-256。虽然这里为了简洁省略了该字段，但在实际开发中，你应该设置这个字段，以确保构建的密封性（hermeticity）。

另一方面，如果某个归档不可用，或者你*需要*与变化非常快的外部依赖协作，那么 `git_repository` 可能更有意义得多（尤其是考虑到前文提到的可配合 git tag 使用的能力）。

最后补充一点，Bazel 项目倾向于单体化（monolithic），因此一个可考虑的方向是*避免*使用外部依赖，而是将必要代码直接纳入你的项目。这样并非总是可行或方便，但它*确实*能为你的构建提供最强保证。

### 引入一门新语言

虽然 Bazel 开箱即用支持多种语言，但我们仍需要能够按项目需要添加更多语言。如果你还没猜到，我们将为项目添加 Go 支持。

让我们利用一路学到的一些工具。请确保你的 `WORKSPACE` 文件中包含以下内容。

```
load("@bazel_tools//tools/build_defs/repo:http.bzl", "http_archive")
http_archive (
name = "io_bazel_rules_go",
urls = ["https://github.com/bazelbuild/rules_go/releases/download/v0.19.5/rules_go-v0.19.5.tar.gz"],
)
load("@io_bazel_rules_go//go:deps.bzl", "go_rules_dependencies", "go_register_toolchains")
go_rules_dependencies()
go_register_toolchains()
Listing 4-9
Loading the Go language rules
```

保存 `WORKSPACE` 文件。

该示例中的大部分内容你之前都见过，例外是最后两行。最后两行会调用已加载的规则，为你的项目配置 Go 语言。

### 注意

与 Bazel 中许多结构和模式不同，上面这两行不应被视为适用于所有语言的规范示例。每种新语言都有自己的一组（一个或多个）初始化规则，因此函数名每次都可能略有不同。

既然 Go 规则已经配置好，让我们用它们创建一个新目标。和之前一样，我们先从一个基础示例开始。

```
chapter_04$ cd src
chapter_04/src$ touch hello_world.go
```

让我们创建一个简单的 Go 程序。

```
package main
import "fmt"
func main() {
fmt.Println("Hello, World!")
}
Listing 4-10
Hello World in Go
```

将其保存为 `hello_world.go`。

现在，打开 `src` 目录中的 `BUILD` 文件，以便我们创建 Go 目标。不过，在真正创建目标之前，我们需要显式加载 Go 规则。

将以下内容添加到 `BUILD` 文件中。

```
load("@io_bazel_rules_go//go:def.bzl", "go_binary")
Listing 4-11
Loading the Go language rules
```

请注意，我们使用标签 `@io_bazel_rules_go` 来进行加载，以便正确引用规则所需的包。保存之前，再添加新的 Go 目标。

```
go_binary(
name = "hello_world_go",
srcs = ["hello_world.go"],
)
Listing 4-12
Create the go_binary target
```

保存到 `BUILD` 文件。现在我们应该能够实际构建并运行该目标：

```
chapter_04/src$ bazel run :hello_world_go
INFO: Analysed target //src:go_hello_world (23 packages loaded, 6239 targets configured).
INFO: Found 1 target...
Target //src:go_hello_world up-to-date:
bazel-bin/src/darwin_amd64_stripped/go_hello_world
INFO: Elapsed time: 3.221s, Critical Path: 1.08s
INFO: 6 processes: 6 darwin-sandbox.
INFO: Build completed successfully, 10 total actions
INFO: Build completed successfully, 10 total actions
Hello, World!
```

恭喜！你现在已经添加了一门新语言，并为其创建了一个目标！

### 定位 Go 语言规则仓库

前面我们查找过 `bazel_tools` 仓库，它只会在我们执行了实际需要它的构建后才出现。以同样的方式，你现在也应该有一个 Go 语言规则仓库。我们快速确认一下：

```
chapter_04/src$ cd ../bazel-chapter_04/external
chapter_04/bazel-chapter_04/external$ ls -1
bazel_tools
io_bazel_rules_go
(possible other repos)
```

如前所述，Bazel 只会拉取构建你所请求目标所需的依赖。如果你先执行一次*clean*，然后构建一个非 Go 目标（例如之前的 Java 目标），你会发现 `io_bazel_rules_go` **不会**存在于你的 `bazel-chapter_04/external` 目录中，尽管这两个目标都在同一个 `BUILD` 文件里。

### 练习——再添加一门语言

在关于 `WORKSPACE` 文件的这一节里，我们一直在积累将新语言添加到项目中的知识。现在你已经具备了这些工具，接下来应继续探索向项目中添加更多语言。

访问 [`https://github.com/bazelbuild`](https://github.com/bazelbuild)，查看他们提供的各类 rules 包。需要注意的是，虽然那里会有许多常见语言，但有些规则集可能不在 bazelbuild 组织下。如果你没有找到心仪的语言，还可以在 Awesome Bazel（[`https://awesomebazel.com`](https://awesomebazel.com)）上找到更大的列表。选择你喜欢的语言，配置你的 `WORKSPACE` 文件，并为该语言创建一个构建目标。

