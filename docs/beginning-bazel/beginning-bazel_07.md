# 7. 代码组织与 Bazel

在过去几章中，示例始终将所有代码放在单一目录里。虽然这对演示很方便，但在实际中并不可行。此外，示例也经常偏离某些语言中既有（甚至被强制执行）的目录和包结构模式（例如 Java、Go 等）。在本章中，我们将修正前几章在组织结构上的不足，并演示 Bazel 在分层目录结构中工作的相关能力。

### 注意

我们在这里创建的目录结构将在本书剩余部分中持续使用。

## 准备工作

这里的大部分工作都是对上一章内容的重组。大体上，我们不会创建任何新功能。为了加快进度，我们先把上一章的内容复制到一个新目录。在此之前，我们还要先清理上一章产生的构建产物：

```
$ cd chapter_06
chapter_06$ bazel clean
chapter_06$ ls
WORKSPACE      src
```

清理完之前缓存的文件后，现在把上一章的内容复制过来：

```
chapter_06$ cd ..
$ cp -rf chapter_06 chapter_07
$ cd chapter_07
chapter_07 $ ls
WORKSPACE      src
```

如果你愿意，可以通过对上一章某个目标执行 `build` 或 `run` 命令来确认一切按预期工作。如果你这样做了，也请再执行一次 `clean` 命令；虽然这并非绝对必要，但它有助于在进行重组任务时保持顶层目录整洁。

## 分离 Protocol Buffers

重组代码有很多方式（例如按语言、按客户端与服务端等）；不过，除了 Bazel 天然倾向 monorepo 之外，本章在这件事上并没有给出任何强烈主张（也就是说，按项目实际情况来做）。

不过，这里一个明显可行的拆分是：把那些包含 Protocol Buffers 各类 `BUILD` 目标和定义的构建目标相关文件抽离出来。客户端和服务端都会引用它们，*而且*它们本身也属于独立语言，因此可以很容易地从当前混合目录中拆分出去。

首先，创建一个顶层目录（`proto`），并将 Protocol Buffer 定义从当前位置移动过去：

```
chapter_07$ mkdir proto
chapter_07$ mv src/transmission_object.proto proto/
```

接下来，我们会创建一个新的 `BUILD` 文件，用于存放所有 protobuf 构建目标：

```
load("@io_bazel_rules_go//proto:def.bzl", "go_proto_library")
proto_library(
name = "transmission_object_proto",
srcs = ["transmission_object.proto"],
)
java_proto_library(
name = "transmission_object_java_proto",
deps = [":transmission_object_proto"],
)
go_proto_library(
name = "transmission_object_go_proto",
proto = ":transmission_object_proto",
importpath = "transmission_object"
)
Listing 7-1
Protobuf-only BUILD file
```

```
chapter_07$ cd proto/
chapter_07/proto$ touch BUILD
```

将上述内容保存到 `proto/BUILD`。

现在，从 `src/BUILD` 中删除对应目标（例如 `src:transmission_object_proto` 等）。这会暂时导致该 `BUILD` 文件中的其他构建目标无法构建，但很快就会修复。

最后，验证在新目录中的目标是否构建正确：

```
chapter_07/proto$ bazel build :all
INFO: Analysed 3 targets (23 packages loaded, 6994 targets configured).
INFO: Found 3 targets...
INFO: Elapsed time: 0.993s, Critical Path: 0.35s
INFO: 4 processes: 3 darwin-sandbox, 1 worker.
INFO: Build completed successfully, 5 total actions
```



## 引用当前包之外的构建目标

前面的示例利用了它们位于同一目录/包中的便利性，从而可以轻松指定 BUILD 目标之间的依赖关系。由于所有内容都在同一个位置，每个示例的构建目标都可以指定本地构建目标（或引用外部依赖）。

当我们将 Protocol Buffer 构建目标拆分到单独的包中时，就破坏了现有目标。尝试构建现有目标之一会导致失败：

```
chapter_07$ bazel build src:echo_client
ERROR: chapter_07/ src/BUILD:5:12: in deps attribute
of java_binary rule //src:echo_client: target '//src:transmission_object_java_proto' does not exist
ERROR: Analysis of target '//src:echo_client' failed; build aborted: Analysis of target '//src:echo_client' failed; build aborted
INFO: Elapsed time: 0.117s
INFO: 0 processes.
FAILED: Build did NOT complete successfully (1 packages loaded, 2 targets configured)
```

让我们修正各个 BUILD 目标，以便能够正确引用新创建的非本地 Protocol Buffer 依赖。打开 `src/BUILD` 文件（加粗处为变更）。

```
java_binary(
name = "echo_client",
srcs = ["EchoClient.java"],
main_class = "EchoClient",
deps = ["//proto:transmission_object_java_proto"],
)
load("@io_bazel_rules_go//go:def.bzl", "go_binary")
go_binary(
name = "echo_server",
srcs = ["echo_server.go"],
deps = [
"//proto:transmission_object_go_proto",
"@com_github_golang_protobuf//proto:go_default_library",
],
)
Listing 7-2
Updating the src/BUILD dependencies
```

保存对 `src/BUILD` 的修改。

Bazel 的一个特性是：除了允许本地依赖之外，所有依赖引用都是相对于某个 `WORKSPACE` 的绝对路径*。* 在前面的示例中，当我们指定对新建 protobuf 目标的依赖时，是相对于 `WORKSPACE` 根目录来写的（由 `//` 表示）。这一点很重要：依赖并不是使用相对于当前 `BUILD` 文件的路径来指定的。虽然这看起来可能是一项繁琐的要求，但它源于 Bazel 一贯强调的“所有内容都应显式声明”的理念。

如前所示，我们也可以引用被拉入 `WORKSPACE` 的依赖（例如我们的 Go 规则），方法是在依赖名称前使用 `@` 符号；这会告知 Bazel 该依赖并非 `WORKSPACE` 本地依赖。

然而，尽管我们已经正确引用了新创建的目标，仍然会遇到另一个问题。为说明这一点，让我们再运行一次构建：

```
chapter_07$ bazel build src:echo_client
ERROR: /chapter_07/src/BUILD:1:1: in java_binary rule //src:echo_client: target '//proto:transmission_object_java_proto' is not visible from target '//src:echo_client'. Check the visibility declaration of the former target if you think the dependency is legitimate
ERROR: Analysis of target '//src:echo_client' failed; build aborted: Analysis of target '//src:echo_client' failed; build aborted
INFO: Elapsed time: 0.215s
INFO: 0 processes.
FAILED: Build did NOT complete successfully (6 packages loaded, 390 targets configured)
```

我们将在下一节中修复这个问题。

## 目标可见性

许多面向对象语言（例如 Java、C++、Objective-C 等）都有对象成员变量和函数的*可见性*概念。通常，这会以*接口*（即面向外部的公共 API）和*实现*（即实际执行工作的代码）来描述。为了实现*关注点分离*，这种划分在无数编程书籍中被反复强调。理论上，这使得实现可以在不影响功能使用方的情况下进行变更；因为它们都遵循同一套接口。

在许多语言中，可见性不一定是二元选择；可以将可见性限定为某种特定范围（例如，仅父类子类可见的成员、仅同一 Java 包可见的成员等）。这在“哪些部分应可见、对谁可见”方面提供了灵活性。

Bazel 提供了强大的目标可见性机制，使项目架构师能够决定哪些内容应该可见、对谁可见。值得注意的是，这是一个*与语言无关*的特性；无论语言本身是否实现了某种成员可见性形式，任何构建目标都可以利用这一 Bazel 特性。这使我们能够确定代码中的哪些部分应被视为可供“公开”使用（即*接口*），哪些部分应保留为私有（即*实现*）。

在本书迄今为止的内容中，我们一直忽略了可见性的概念。这之所以可行，是因为我们的所有代码都位于同一目录和 `BUILD` 文件中（即同一个 Bazel 包内）；同一包中的所有目标彼此自动可见。此外，如果没有额外声明，同一包中的所有目标默认对任何外部目标都*不可见*。也就是说，除非我们显式声明其可见性，否则某个目标*不能被其自身包外部依赖*。

虽然这看起来可能是一项繁琐的要求，但它实际上是 Bazel 最强大的特性之一：作为代码作者，你可以决定如何最好地组织代码以便构建，*以及*如何最好地组织代码供使用方调用。这两者不必是同一件事。

由于我们*确实*希望包外部的使用方能够使用我们的代码，因此需要指定可见性。实现方式主要有两种：（1）包级别；（2）目标级别。我们将同时探索这两种方式。

### 包可见性

如前所述，默认情况下，同一包内的所有目标都不能作为依赖被包外其他目标使用。我们可以采用的最简单方式是让*所有*目标可见。

打开 `proto/BUILD` 文件并添加以下指令。

```
package(default_visibility = ["//visibility:public"])
load("@io_bazel_rules_go//proto:def.bzl", "go_proto_library")
proto_library(
name = "transmission_object_proto",
srcs = ["transmission_object.proto"],
)
java_proto_library(
name = "transmission_object_java_proto",
deps = [":transmission_object_proto"],
)
go_proto_library(
name = "transmission_object_go_proto",
proto = ":transmission_object_proto",
importpath = "transmission_object"
)
Listing 7-3
Making all build targets visible
```

保存到 `proto/BUILD`。

现在，让我们再次尝试构建 `src:echo_client`：

```
chapter_07$ bazel build src:echo_client
INFO: Analysed target //src:echo_client (1 packages loaded, 4 targets configured).
INFO: Found 1 target...
Target //src:echo_client up-to-date:
bazel-bin/src/echo_client.jar
bazel-bin/src/echo_client
INFO: Elapsed time: 3.221s, Critical Path: 2.83s
INFO: 2 processes: 1 darwin-sandbox, 1 worker.
INFO: Build completed successfully, 6 total actions
```

现在我们已经更新了依赖的可见性，构建会按预期工作（`src:echo_server` 也应当可以工作，不过这里留给读者作为练习）。



### 说明

再次强调，细心的读者会注意到，我们在 *BUILD* 文件中又引入了一个新函数（`package`）。`package` 函数的作用是将相同的元数据应用到给定包中的所有目标。在这个例子里，我们仅用它来修改可见性。

#### 路径特定可见性

上一节的解决方案解决了让 `echo_client` 和 `echo_server` 目标能够构建这一紧迫问题。  
但是，把包中的*每一个*目标都设为可见，这种方案至少可以说是有些过于粗放。这种“全有或全无”的方式并不利于良好的代码组织；从极端来看，它只比把所有东西都放在同一个地方稍微好一点。

幸运的是，我们可以做得更好。Bazel 支持显式指定目标可见性的路径。在这里，我们将访问限制为仅限 *src* 包。

打开 `proto/BUILD` 文件，并修改我们的可见性配置。

```
package(default_visibility = ["//src:__pkg__"])
load("@io_bazel_rules_go//proto:def.bzl", "go_proto_library")

Listing 7-4
Restricting the visibility to a specific package
```

保存 `proto/BUILD` 文件。

再次确认你可以同时构建 `src:echo_client` 和 `src:echo_server`。现在你已经将可见性缩小到*仅*限于严格位于 `src` 包内的目标。

### 说明

尽管 `//src:__pkg__` 这一配置允许在 `src` 包内访问任意 `proto` 目标，但该配置**不会**自动包含 `src` 的任何*子包*。  
也就是说，如果你有一个类似 `//src/client` 的包，那么 *proto* 目标对 `//src/client` 内的目标依然是**不可见**的。

这可以通过将可见性配置从 `__pkg__` 改为 `__subpackages__` 轻松解决。这表示某个依赖不仅对特定包可见，也对其下的所有子包可见。

### 单个目标可见性

在上一节中，我们是在包级别指定可见性。虽然这通常是个不错的起点，但它仍然呼应了前面“全有或全无”的问题；我们仍在对给定包内的所有目标统一声明可见性。Bazel 再次提供了更细粒度的能力。

单个目标可以自行声明其可见性；也就是说，每个单独目标都可以指定哪些其他包可以依赖它。这也包括某个单独目标直接声明 `visibility:public` 的情况。

打开 `proto/BUILD` 文件；我们将修改各个目标的可见性。

```
#package(default_visibility = ["//src: :__pkg__"])
load("@io_bazel_rules_go//proto:def.bzl", "go_proto_library")
proto_library(
name = "transmission_object_proto",
srcs = ["transmission_object.proto"],
)
java_proto_library(
name = "transmission_object_java_proto",
deps = [":transmission_object_proto"],
visibility = ["//src:__pkg__"],
)
go_proto_library(
name = "transmission_object_go_proto",
proto = ":transmission_object_proto",
importpath = "transmission_object",
)
Listing 7-5
Specifying visibility at the build target level
```

保存 `proto/BUILD`。现在，再次验证我们的 `src:echo_client` 仍可构建：

```
chapter_07$ bazel build src:echo_client
INFO: Analysed target //src:echo_client (1 packages loaded, 4 targets configured).
INFO: Found 1 target...
Target //src:echo_client up-to-date:
bazel-bin/src/echo_client.jar
bazel-bin/src/echo_client
INFO: Elapsed time: 0.207s, Critical Path: 0.00s
INFO: 0 processes.
INFO: Build completed successfully, 1 total action
```

不过，当你尝试构建 *src:echo_server* 时会得到不同结果：

```
chapter_07$ bazel build src:echo_server
ERROR: chapter_07/src/BUILD:10:1: in go_binary rule //src:echo_server: target '//proto:transmission_object_go_proto' is not visible from target '//src:echo_server'. Check the visibility declaration of the former target if you think the dependency is legitimate
ERROR: Analysis of target '//src:echo_server' failed; build aborted: Analysis of target '//src:echo_server' failed; build aborted
INFO: Elapsed time: 0.109s
INFO: 0 processes.
FAILED: Build did NOT complete successfully (0 packages loaded, 1 target configured)
```

这里，我们移除了包级别“让所有目标对 *src* 可见”的指令。取而代之的是，我们仅让 *transmission_object_java_proto* 目标（只被 *echo_client* 需要）对 *src* 包可见。于是，*echo_server* 所需的 *transmission_object_go_proto* 又再次变为不可见。

显然，我们可以轻松修复它。重新打开 *proto/BUILD* 文件，并为 *transmission_object_go_proto* 增加可见性配置。

```
#package(default_visibility = ["//src:echo_client"])
load("@io_bazel_rules_go//proto:def.bzl", "go_proto_library")
proto_library(
name = "transmission_object_proto",
srcs = ["transmission_object.proto"],
)
java_proto_library(
name = "transmission_object_java_proto",
deps = [":transmission_object_proto"],
visibility = ["//src:__pkg__"],
)
go_proto_library(
name = "transmission_object_go_proto",
proto = ":transmission_object_proto",
importpath = "transmission_object",
visibility = ["//src:__pkg__"],
)
Listing 7-6
Fixing the visibility for transmission_object_go_proto
```

将文件保存到 *proto/BUILD*，并重试构建 *src:echo_server*：

```
chapter_07$ bazel build src:echo_server
INFO: Analysed target //src:echo_server (1 packages loaded, 4 targets configured).
INFO: Found 1 target...
Target //src:echo_server up-to-date:
bazel-bin/src/darwin_amd64_stripped/echo_server
INFO: Elapsed time: 0.227s, Critical Path: 0.01s
INFO: 0 processes.
INFO: Build completed successfully, 1 total action
```

修复依赖可见性后，所有构建现在又都恢复正常了。

### 混合使用包级与目标级可见性

我们已经演示了两种表达可见性的方式（即 *package* 和*单个*目标），还需要说明它们如何相互作用。

简而言之，包级可见性配置会作为包内所有目标的*默认值*。而单个目标上的可见性配置则是该目标可见性的*最终*值。  
也就是说，包级与目标级之间不会尝试合并；始终是替换关系。

虽然这看起来有些严格，但要记住一个关键点：Bazel 追求的是显式性；在可见性规则之间进行复杂的隐式合并并不符合这一目标。是的，这可能意味着你在制定某些非常细致的规则时需要多写一些配置；但显式定义胜过一时的便利。

Bazel 通过 `package_group.` 结构提供了一种缓解这种冗长性的策略。`package_group` 允许你将元数据（例如可见性规则）应用到一组包上。这在“给单个目标设置可见性”和“要求整个包统一可见性策略”之间提供了很好的折中。

## 分离客户端与服务端代码

在将 protobuf 代码拆分到独立包之后，我们也将把客户端和服务端代码分别拆分到各自独立的包中。



### 拆分 Echo 服务器代码

首先，让我们为 `echo_server` 创建一个目录。出于一些原因（这些原因在后文以及后续章节中会更明显），我们将为 `echo_server` 创建一个子目录，并将对应代码移动到该目录中：

```
chapter_07$ mkdir -p server/echo_server
chapter_07$ mv src/echo_server.go server/echo_server/echo_server.go
```

现在让我们创建一个 `server/echo_server/BUILD` 文件。我们只需从原始 `src/BUILD` 文件中复制先前针对 `echo_server` 构建目标的定义。

```
load("@io_bazel_rules_go//go:def.bzl", "go_binary")
go_binary(
name = "echo_server",
srcs = ["echo_server.go"],
deps = [
"//proto:transmission_object_go_proto",
"@com_github_golang_protobuf//proto:go_default_library",
],
)
Listing 7-7
Creating the server/echo_server/BUILD file
```

将该文件保存为 `server/echo_server/BUILD`。尝试构建它只会重新引入我们之前看到的 proto 可见性问题。让我们先更新必要目标的可见性规则。

打开 `proto/BUILD` 文件并进行如下修改。

```
load("@io_bazel_rules_go//proto:def.bzl", "go_proto_library")
proto_library(
name = "transmission_object_proto",
srcs = ["transmission_object.proto"],
)
java_proto_library(
name = "transmission_object_java_proto",
deps = [":transmission_object_proto"],
visibility = ["//src:__pkg__"],
)
go_proto_library(
name = "transmission_object_go_proto",
proto = ":transmission_object_proto",
importpath = "transmission_object",
visibility = ["//server/echo_server:__pkg__"],
)
Listing 7-8
Updating the visibility package for transmission_object_go_proto
```

将更改保存到 `proto/BUILD`。现在，我们应该能够成功构建新创建的 `server/echo_server:echo_server` 目标：

```
chapter_07$ bazel build server/echo_server:echo_server
INFO: Analysed target //server/echo_server:echo_server (2 packages loaded, 5 targets configured).
INFO: Found 1 target...
Target //server/echo_server:echo_server up-to-date:
bazel-bin/server/echo_server/darwin_amd64_stripped/echo_server
INFO: Elapsed time: 0.896s, Critical Path: 0.56s
INFO: 2 processes: 2 darwin-sandbox.
INFO: Build completed successfully, 5 total actions
```

#### 省略构建目标名

需要注意的一点是，我们在构建 `echo_server` 的路径中出现了重复；具体来说，我们看到了两次 “echo_server”：

```
chapter_07$ bazel build server/echo_server:echo_server
```

Bazel 提供的一项便利是：当构建目标与其所在包同名时，可以省略该目标名。也就是说，下面的调用在功能上是等价的：

```
chapter_07$ bazel build server/echo_server
INFO: Analysed target //server/echo_server:echo_server (1 packages loaded, 2 targets configured).
INFO: Found 1 target...
Target //server/echo_server:echo_server up-to-date:
bazel-bin/server/echo_server/darwin_amd64_stripped/echo_server
INFO: Elapsed time: 0.217s, Critical Path: 0.00s
INFO: 0 processes.
INFO: Build completed successfully, 1 total action
```

尽管此时你可能会想对 Bazel 提出质疑，但这种便利实际上提供了一个非常强大的约定：如果某个目标与其所在包同名，那么它就可以被视为该包对外暴露的“公共”目标（即 *接口*）。这使得外部包在判断应依赖哪个（或哪些）目标时，预期更加统一、简单。

值得注意的是，这一约定并不是 Bazel 的*强制要求*，但它的存在非常有力，在创建和分析构建依赖树时可以显著降低认知负担。

### 拆分 Echo 客户端代码

在处理完服务端之后，我们终于也将注意力转向客户端。在这种情况下，我们会创建一个略有不同的目录/包结构；这是为了后续章节做准备。和之前一样，我们将把相应代码移动到子目录中：

```
chapter_07$ mkdir -p client/echo_client/command_line
chapter_07$ mv src/EchoClient.java client/echo_client/command_line/EchoClient.java
```

现在我们需要创建对应的 `BUILD` 文件；同样，我们最终只需复制之前该目标的定义。

```
java_binary(
name = "command_line",
srcs = ["EchoClient.java"],
main_class = "EchoClient",
deps = ["//proto:transmission_object_java_proto"],
)
Listing 7-9
Creating the client/echo_client/command_line/BUILD file
```

将修改保存到 `client/echo_client/command_line/BUILD` 文件。再次说明，我们需要更新 `proto/BUILD` 中相应目标的可见性；否则，我们的 `echo_client` 目标将再次构建失败。

打开 `proto/BUILD`，并对 `transmission_object_java_proto` 做如下修改。

```
load("@io_bazel_rules_go//proto:def.bzl", "go_proto_library")
proto_library(
name = "transmission_object_proto",
srcs = ["transmission_object.proto"],
)
java_proto_library(
name = "transmission_object_java_proto",
deps = [":transmission_object_proto"],
visibility = ["//client/echo_client:__subpackages__"],
)
go_proto_library(
name = "transmission_object_go_proto",
proto = ":transmission_object_proto",
importpath = "transmission_object",
visibility = ["//server/echo_server:__pkg__"],
)
Listing 7-10
Updating the visibility for transmission_object_java_proto
```

将文件保存到 `proto/BUILD`。

### 注意

你可能已经注意到，我们为 `transmission_object_java_proto` 与其 Go 对应项设置了略有不同的可见性规则。具体来说，Go 版本非常明确地只面向 `echo_server` 包，而 Java 版本则允许更广泛的潜在包集合（即 `echo_client` 包下的所有内容）。这同样是为了后续章节提前做准备。

完成可见性更新后，让我们验证目标仍能按预期构建（并利用前面提到的目标名省略）：

```
chapter_07$ bazel build client/echo_client/command_line
INFO: Analysed target //client/echo_client/command_line:command_line (1 packages loaded, 2 targets configured).
INFO: Found 1 target...
Target //client/echo_client/command_line:command_line up-to-date:
bazel-bin/client/echo_client/command_line/command_line.jar
bazel-bin/client/echo_client/command_line/command_line
INFO: Elapsed time: 0.192s, Critical Path: 0.01s
INFO: 0 processes.
INFO: Build completed successfully, 1 total action
```

### 清理

由于原始目录中的内容基本都已移走，现在我们可以将其删除：

```
chapter_07$ rm -rf src
```

作为一次合理性检查，我们可以通过一个全量构建命令再次确认一切都能按预期构建：

```
chapter_07$ bazel build ...
INFO: Analysed 5 targets (0 packages loaded, 0 targets configured).
INFO: Found 5 targets...
INFO: Elapsed time: 0.188s, Critical Path: 0.01s
INFO: 0 processes.
INFO: Build completed successfully, 1 total action
```

由于我们并没有更改任何实际代码，只是移动了代码并创建/重配了 BUILD 文件和目标；因此所有功能都应与之前一致。验证过程留给读者作为练习。



## 最后的话

在上一章中，你使用 Bazel 的工具将代码重组为一种（更）可扩展的开发结构。尽管仍然是玩具示例，但内容已经足够让你开始使用 Bazel 的各种构件，把代码结构打造成易于理解、可扩展且可控的形式。

虽然本章中完成的是一种特定的组织方式，但绝不应将其视为唯一标准。例如，Protocol Buffer 及其派生的语言特定目标的位置和可见性可以调整（例如，将语言特定的 proto 目标放到更接近其实际使用位置的地方）。另一个例子是切换为以语言为中心的目录/包结构。

没有“正确”的唯一答案；每种可能性都有其权衡。无论如何，Bazel 都能灵活支持最适合你需求的代码组织方式，同时提供有助于长期维护该结构的工具。

