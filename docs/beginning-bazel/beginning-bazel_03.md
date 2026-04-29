# 3. 你的第一个 Bazel 项目

现在你已经下载并配置好了 Bazel，真正有趣的部分开始了。我们将先从一个小项目入手，然后在此基础上逐步构建（双关一下）。

在本章结束时，你将成功运行你的第一个 Bazel 项目，并能够构建和测试代码。

## 设置你的工作区

在添加任何代码之前，我们通过在指定目录中创建一个 `WORKSPACE` 文件来建立一个新的 Bazel 项目。

为你的项目创建目录，并创建一个空的 `WORKSPACE` 文件：

```
$ mkdir chapter_03 (or )
$ cd chapter_03 (or )
chapter_03$ touch WORKSPACE
```

`WORKSPACE` 文件的位置应始终位于 Bazel 项目的根目录。在你的 Bazel 项目中，所有路径都会相对于 `WORKSPACE` 文件进行解析。随着你在不同目录中创建各种构建目标，你将能够以相对于 `WORKSPACE` 文件的方式引用它们。

不过，这只是 `WORKSPACE` 文件强大能力的冰山一角。后续我们将看到如何使用 `WORKSPACE` 文件来：

*   向你的工作区添加新的远程代码仓库（之后你可以引用它们）

*   添加用于编译不同语言的新规则

不过目前，仅一个空的 `WORKSPACE` 文件就已经足够我们开展很多工作，因此我们将从这里开始。



## 添加源代码

虽然 `WORKSPACE` 文件定义了 Bazel 项目的根目录，但你还需要定义一个源代码目录（也可能是多个源代码目录）来放置代码。这样做的一个原因是代码组织，因为你通常希望具备某种结构。

不过，至少还有一个很重要的原因：Bazel 会在与你的 `WORKSPACE` 目录相同的位置创建新的子目录。稍后我们会详细介绍这些目录，因为它们与 Bazel 构建过程中产出的构建产物有关。

为你的源代码创建一个目录：

```
chapter_03$ mkdir src (or your favorite directory name)
```

### 注意

在考虑目录名称时，不要使用以下名称之一：

*   `bazel-bin`

*   `bazel-out`

*   `bazel-testlogs`

*   `bazel-chapter_03` (or `bazel-<name of your directory>`)

    如果你还没猜到，这些就是 Bazel 会创建的特殊目录。创建一个与这些目录同名的目录就是在自找麻烦，所以请通过选择不同的名称（例如 `src`）来省去很多头痛。另外，尽管上述目录对应的是当前版本 Bazel 的命名，也建议避免任何符合 “bazel-*” 模式的目录名。

### Hello World，Java 风格

开箱即用（在撰写本文时），Bazel 无需额外配置即可支持 C++、Java 和 Python。开始时，我们将创建一个（稍作修改的）Java 版 Hello World。（别担心，我们后面会更复杂；这只是起步。）

在你偏好的代码编辑器中，创建文件 `HelloWorld.java` 并写入以下内容。

```
public class HelloWorld {
public static void main(String[] args) {
System.out.println("Hello, World!);
}
}
Listing 3-1
A simple Java program
```

将该文件保存到磁盘上的 `src` 目录下。

### 指定 BUILD 目标

代码创建完成后，我们现在可以把注意力转向实际构建所需的 Bazel 基础组件。

在你的 `src` 目录中，创建文件 `BUILD`，写入并保存以下内容。

```
java_binary(
name = "HelloWorld",
srcs = ["HelloWorld.java"],
)
Listing 3-2
Your first BUILD file
```

在这个示例中，`HelloWorld` 是一个*构建目标*；也就是说，`HelloWorld` 是一个可以被识别并构建的单元。

### 构建你的目标

定义好要构建的内容后，我们现在就可以真正开始构建了。不过在此之前，先回到根目录，也就是包含 `WORKSPACE` 文件的那个目录。先列出其中内容：

```
chapter_03$ ls
WORKSPACE
src (or your favorite directory name)
```

另外，再确认一下 `src` 目录中的内容。如下：

```
chapter_03$ ls src
BUILD    HelloWorld.java
```

这就是 Bazel 项目的干净状态，此时还没有任何内容被构建。接下来我们来改变这一点。

#### 构建二进制文件

要构建你的第一个项目，请在命令行中运行：

```
chapter_03$ bazel build src:HelloWorld
```

把 `bazel` 命令的参数稍作拆解：

*   `build`
    *   这表示你要构建/编译/组装给定目标。

*   `src`
    *   这表示包含所需构建目标的目录。

    *   在这个示例中，目录层级比较浅；不过你可以（并且将会）指定任意数量的有效目录路径来补充这个参数。

*   `:HelloWorld`
    *   这是 `src` 目录中的实际构建目标。

    *   在 Bazel 中，一个目录可以有一个或多个可构建目标。

假设一切按计划进行，你的输出应类似如下：

```
INFO: Analysed target //src:HelloWorld (19 packages loaded, 550 targets configured).
INFO: Found 1 target...
Target //src:HelloWorld up-to-date:
bazel-bin/src/HelloWorld.jar
bazel-bin/src/HelloWorld
INFO: Elapsed time: 0.144s, Critical Path: 0.00s
INFO: 0 processes.
INFO: Build completed successfully, 1 total action.
```

#### 运行二进制文件

构建好可执行文件后，你可以使用以下命令运行它：

```
chapter_03$ bazel-bin/src/HelloWorld
Hello, World!
```

不过，在实际开发中，你不会想要不断在“先构建可执行文件”与“再直接执行二进制文件”之间来回切换。好在你不需要这么做；Bazel 提供了直接构建并运行可执行文件的能力。

类似于你最初构建可执行文件的方式，你可以通过以下命令直接运行它：

```
chapter_03% bazel run src:HelloWorld
INFO: Analysed target //src:HelloWorld (0 packages loaded, 0 targets configured).
INFO: Found 1 target...
Target //src:HelloWorld up-to-date:
bazel-bin/src/HelloWorld.jar
bazel-bin/src/HelloWorld
INFO: Elapsed time: 0.217s, Critical Path: 0.07s
INFO: 1 process: 1 worker.
INFO: Build completed successfully, 2 total actions
INFO: Build completed successfully, 2 total actions
Hello, World!
```

### 说明

在两次运行中都值得注意的一点是关于 (*X* packages loaded, *Y* targets configured) 的那一行。它大致反映了项目缓存状态。在第一个示例中，这两个值都非 0，说明为了生成目标，需要在依赖项上执行一些工作。在第二个示例中，这两个值都为 0，说明构建应当是完全命中缓存的。只有当发生变化时，Bazel 才会加载包和目标，并智能地仅重建必要部分。



### 创建和使用依赖

创建单个二进制文件是可以的；然而，这在开发中显然并不实用。在实践中，我们希望将程序拆分为更细粒度的组件。细粒度组件有很多优点，包括更易共享、更易测试、构建更快，以及更容易优化构建过程。

在这个特定案例中，我们能从原始示例中抽离出的内容并不多，因此我们来添加一些新功能。

在你的 `src` 目录中，新建文件 `IntMultiplier.java`，并添加以下代码。

```
public class IntMultiplier {
private int a;
private int b;
public IntMultiplier(int a, int b) {
this.a = a;
this.b = b;
}
public int GetProduct() {
return a * b;
}
}
Listing 3-3
IntMultiplier.java
```

暂时不要向 `BUILD` 文件中添加任何内容；我们将先尝试把新类加入我们的二进制目标中。

```
public class HelloWorld {
public static void main(String[] args) {
System.out.println("Hello, World!");
IntMultiplier im = new IntMultiplier(3, 4);
System.out.println(im.GetProduct());
}
}
Listing 3-4
Adding IntMultiplier to HelloWorld.java
```

现在我们再次尝试运行 HelloWorld 的构建：

```
chapter_03% bazel run src:HelloWorld
INFO: Analysed target //src:HelloWorld (0 packages loaded, 0 targets configured).
INFO: Found 1 target...
ERROR: /Users/pj/Dropbox/Books/Beginning_Bazel/code_samples/chapter_03/src/BUILD:6:1: Building src/HelloWorld.jar (1 source file) failed (Exit 1)
src/HelloWorld.java:5: error: cannot find symbol
IntMultiplier im = new IntMultiplier(3, 4);
^
symbol:   class IntMultiplier
location: class HelloWorld
src/HelloWorld.java:5: error: cannot find symbol
IntMultiplier im = new IntMultiplier(3, 4);
^
symbol:   class IntMultiplier
location: class HelloWorld
Target //src:HelloWorld failed to build
Use --verbose_failures to see the command lines of failed build steps.
INFO: Elapsed time: 0.246s, Critical Path: 0.10s
INFO: 0 processes.
FAILED: Build did NOT complete successfully
```

在这个例子中，构建失败是因为无法找到 `IntMultiplier`*。* 这体现了 Bazel 最重要的特性之一：构建中没有任何隐式行为；你需要显式指定所有内容，包括全部依赖。Bazel 不会自动神奇地查找同目录、同包等位置中的内容。

我们可以通过以下两种方式之一解决这个问题：

*   将新的源文件添加到二进制目标中。

*   创建一个新的库，并让二进制目标依赖它。

我们将两种方法都进行探索。

#### 将 IntMulitplier.java 添加到 java_binary

在这种情况下，我们可以直接把 `IntMultiplier.java` 作为 `HelloWorld` 构建目标的另一个源码文件添加进去。

```
java_binary(
name = "HelloWorld",
srcs = [
"HelloWorld.java",
"IntMultiplier.java",
],
)
Listing 3-5
Adding to the HelloWorld srcs
```

现在，让我们尝试重新运行 `HelloWorld`：

```
chapter_03% bazel run src:HelloWorld
INFO: Analysed target //src:HelloWorld (0 packages loaded, 0 targets configured).
INFO: Found 1 target...
Target //src:HelloWorld up-to-date:
bazel-bin/src/HelloWorld.jar
bazel-bin/src/HelloWorld
INFO: Elapsed time: 0.141s, Critical Path: 0.00s
INFO: 0 processes.
INFO: Build completed successfully, 1 total action
INFO: Build completed successfully, 1 total action
Hello, World!

```

通过显式列出该目标所依赖的文件，`HelloWorld` 就能够成功构建并运行。

不过，虽然这个方案可行，但仍然不是最优；按目前这种方式，`IntMultiplier` 本可以很容易在其他地方复用，但现在它被锁定在 `HelloWorld` 二进制目标内部。

#### 创建 java_library 依赖

与其把文件直接添加到 `HelloWorld` 构建目标，不如创建一个完全独立的依赖。这一次，我们不再创建 `java_binary` 构建目标，而是引入一种新的构建目标类型：`java_library`。

顾名思义，`java_library` 构建目标用于封装一组可共享的 Java 功能。创建后，其他构建目标（包括其他 `java_library` 构建目标）就可以依赖这个 `java_library`。

```
java_library(
name = "LibraryExample",
srcs = ["IntMultiplier.java"],
)
Listing 3-6
Creating the java_library dependency
```

创建了新的构建目标后，我们先直接构建它：

```
chapter_03% bazel build src:LibraryExample
INFO: Analysed target //src:LibraryExample (1 packages loaded, 2 targets configured).
INFO: Found 1 target...
Target //src:LibraryExample up-to-date:
bazel-bin/src/libLibraryExample.jar
INFO: Elapsed time: 0.203s, Critical Path: 0.06s
INFO: 1 process: 1 worker.
INFO: Build completed successfully, 2 total actions
```

不过，正如预期且与 `HelloWorld` 示例不同，我们不能运行这个构建目标。尝试运行会得到如下错误：

```
chapter_03% bazel run src:LibraryExample
ERROR: Cannot run target //src:LibraryExample: Not executable
INFO: Elapsed time: 0.097s
INFO: 0 processes.
FAILED: Build did NOT complete successfully (0 packages loaded)
FAILED: Build did NOT complete successfully (0 packages loaded)
```

#### 依赖构建目标

现在我们已经创建了该构建目标，接下来让 `HelloWorld` 依赖这个目标。

```
java_binary(
name = "HelloWorld",
srcs = [ "HelloWorld.java"],
deps = [":LibraryExample"],
)
Listing 3-7
Adding a dependency to HelloWorld
```

现在让我们重新运行 `HelloWorld`：

```
chapter_03% bazel run src:HelloWorld
INFO: Analysed target //src:HelloWorld (1 packages loaded, 4 targets configured).
INFO: Found 1 target...
Target //src:HelloWorld up-to-date:
bazel-bin/src/HelloWorld.jar
bazel-bin/src/HelloWorld
INFO: Elapsed time: 0.955s, Critical Path: 0.78s
INFO: 3 processes: 1 darwin-sandbox, 2 worker.
INFO: Build completed successfully, 7 total actions
INFO: Build completed successfully, 7 total actions
Hello, World!

```

在 Bazel 中，二进制目标与库目标的这种模式是跨语言通用的一对结构。一般来说，你创建的 `<insert language>_binary` 目标数量会相对较少；它们对应你希望生成的可执行文件数量。相对地，你创建的 `<insert language>_library` 构建目标数量通常会相对较多。

### 测试你的构建目标

创建更小构建单元的一个主要优势是它们更容易测试。既然我们已经创建了一些模块化功能，接下来就设置一个测试来验证这些功能。

#### 配置测试依赖

在创建测试之前，我们首先需要配置一些必需的依赖。

在项目根目录下，新建目录 `third_party`，并在其中创建两个子目录：`hamcrest` 和 `junit`：

```
chapter_03$ mkdir third_party
chapter_03$ mkdir third_party/hamcrest
chapter_03$ mkdir third_party/junit
```

根据以下网站的说明 [`https://github.com/junit-team/junit4/wiki/download-and-install`](https://github.com/junit-team/junit4/wiki/download-and-install) 下载以下 jar 包：

*   `hamcrest-core-1.3.jar`

*   `junit-4.12.jar`

将这些 jar 包移动到 `third_party` 下各自对应的目录中。为了使用这些 jar 包，我们将使用另一种构建目标类型：`java_import`*。*

让我们新建一个 `BUILD` 文件来容纳 `java_import` 构建目标。

```
package(default_visibility = ["//visibility:public"])
java_import(
name = "junit4",
jars = [
"hamcrest/hamcrest-core-1.3.jar",
"junit/junit-4.12.jar",
]
)
Listing 3-8
BUILD file for third_party targets
```



### 注意

细心的读者会注意到，我们在 `BUILD` 文件中加入了一个新的指令 `package`。我们将在后续章节进一步深入这一点，用来控制构建目标对其他目标的可见性。现在只需要知道，这个指令使得此 `BUILD` 文件中包含的目标可以被任何其他 `BUILD` 文件中的 `BUILD` 目标可见。

将 `BUILD` 文件保存到 `third_party` 目录。你可以通过运行以下命令来测试其是否配置正确：

```
chapter_03$ bazel build third_party:junit4
INFO: Analysed target //third_party:junit4 (2 packages loaded, 25 targets configured).
INFO: Found 1 target...
Target //third_party:junit4 up-to-date (nothing to build)
INFO: Elapsed time: 0.157s, Critical Path: 0.00s
INFO: 0 processes.
INFO: Build completed successfully, 1 total action
```

#### 创建 java_test 构建目标

现在让我们使用 `java_test` 构建目标为我们的功能创建一个测试。

```
import static org.junit.Assert.assertEquals;
import org.junit.Test;
public class IntMultiplierTest {
@Test
public void testIntMultiplier() throws Exception {
IntMultiplier im = new IntMultiplier(3, 4);
assertEquals(12, im.GetProduct());
}
}
Listing 3-9
IntMultiplierTest.java
```

保存到 `src/IntMultiplierTest.java`。

现在让我们向 `BUILD` 文件添加一个新的构建目标。

```
java_test(
name = "LibraryExampleTest",
srcs = ["IntMultiplierTest.java"],
deps = [
":LibraryExample",
"//third_party:junit4",
],
test_class = "IntMultiplierTest",
)
Listing 3-10
Adding java_test to BUILD
```

运行你刚创建的测试：

```
chapter_03$ bazel test src:LibraryExampleTest
INFO: Build options --collect_code_coverage, --instrumentation_filter, and --test_timeout have changed, discarding analysis cache.
INFO: Analysed target //src:LibraryExampleTest (0 packages loaded, 617 targets configured).
INFO: Found 1 test target...
Target //src:LibraryExampleTest up-to-date:
bazel-bin/src/LibraryExampleTest.jar
bazel-bin/src/LibraryExampleTest
INFO: Elapsed time: 2.710s, Critical Path: 2.40s
INFO: 3 processes: 1 darwin-sandbox, 2 worker.
INFO: Build completed successfully, 7 total actions
//src:LibraryExampleTestPASSED in 0.4s
Executed 1 out of 1 test: 1 test passes.
INFO: Build completed successfully, 7 total actions
```

不出所料，测试通过了。

为了验证一下，我们再添加一个测试用例。这次先故意创建一个失败的测试，看看会发生什么。

```
public class IntMultiplierTest {
@Test
public void testIntMultiplier() throws Exception {
IntMultiplier im = new IntMultiplier(3, 4);
assertEquals(12, im.GetProduct());
}
@Test
public void testIntMultiplier_Failure() throws Exception {
IntMultiplier im = new IntMultiplier(4, 5);
assertEquals(21, im.GetProduct());
}
}
Listing 3-11
Add a failing test
```

保存文件并重新执行测试：

```
chapter_03$ bazel test src:LibraryExampleTest
INFO: Analysed target //src:LibraryExampleTest (20 packages loaded, 617 targets configured).
INFO: Found 1 test target...
FAIL: //src:LibraryExampleTest (see /execroot/__main__/bazel-out/darwin-fastbuild/testlogs/src/LibraryExampleTest/test.log)
Target //src:LibraryExampleTest up-to-date:
bazel-bin/src/LibraryExampleTest.jar
bazel-bin/src/LibraryExampleTest
INFO: Elapsed time: 13.038s, Critical Path: 2.91s
INFO: 3 processes: 1 darwin-sandbox, 2 worker.
INFO: Build completed, 1 test FAILED, 7 total actions
//src:LibraryExampleTestFAILED in 0.3s
/execroot/__main__/bazel-out/darwin-fastbuild/testlogs/src/LibraryExampleTest/test.log
```

从这个（相当明显的）失败中，Bazel 会将信息输出到前面提到的 `test.log` 文件。打开该文件会看到如下内容。

```
There was 1 failure:
1) testIntMultiplier_Failure(IntMultiplierTest)
java.lang.AssertionError: expected: but was:
Listing 3-12
Failure found in test.log file
```

请注意，实际输出可能会详细得多，但上述代码已足以让我们诊断并修复问题。让我们修正这个问题并重新运行测试。

```
public class IntMultiplierTest {
...
@Test
public void testIntMultiplier_Failure() throws Exception {
IntMultiplier im = new IntMultiplier(4, 5);
assertEquals(20, im.GetProduct());
}
}
Listing 3-13
Correcting the failing test
```

重新运行测试：

```
chapter_03$ bazel test src:LibraryExampleTest
INFO: Analysed target //src:LibraryExampleTest (0 packages loaded, 0 targets configured).
INFO: Found 1 test target...
Target //src:LibraryExampleTest up-to-date:
bazel-bin/src/LibraryExampleTest.jar
bazel-bin/src/LibraryExampleTest
INFO: Elapsed time: 0.717s, Critical Path: 0.56s
INFO: 2 processes: 1 darwin-sandbox, 1 worker.
INFO: Build completed successfully, 3 total actions
//src:LibraryExampleTestPASSED in 0.3s
```

## 构建（并清理）整个世界

在结束之前，我们再来看几个 Bazel 核心功能。

### 构建所有内容（在某个目录中）

在前面的示例中，我们是逐个构建各个构建目标的。对于开发单个组件来说这没问题，但显然这不是一个可扩展的流程。

Bazel 内置了可同时构建多种目标的功能。例如，不必逐个构建 `src` 目录中的构建目标，我们可以通过运行以下命令让 Bazel 一次性全部构建：

```
chapter_03$ bazel build src:all
INFO: Analysed 3 targets (20 packages loaded, 619 targets configured).
INFO: Found 3 targets...
INFO: Elapsed time: 8.892s, Critical Path: 5.87s
INFO: 9 processes: 6 darwin-sandbox, 3 worker.
INFO: Build completed successfully, 16 total actions
```

在这里，`:all` 并不是某个具体的构建目标；它是一个元目标，用来告诉 Bazel 构建给定包（即目录）中的所有构建目标。

同样地，我们也可以让 Bazel 构建 `third_party` 目录中的所有内容：

```
chapter_03$ bazel build third_party:all
INFO: Analysed target //third_party:junit4 (13 packages loaded, 520 targets configured).
INFO: Found 1 target...
Target //third_party:junit4 up-to-date (nothing to build)
INFO: Elapsed time: 1.467s, Critical Path: 0.00s
INFO: 0 processes.
INFO: Build completed successfully, 1 total action
```

`:all` 目标不仅可用于构建，也可用于所有 Bazel 命令（例如 `bazel test <insert target>`）。

这可能已经很明显了，但不要把任何构建目标命名为“all”。这只会导致混淆。

### 构建所有内容（当前目录及其下级目录）

再次说明，前面的 `build all` 命令在处理特定目录时很好用；但是如果要以这种方式为所有目录构建，就会再次变得繁琐。幸运的是，Bazel 再次提供了另一个命令来帮助我们。

在你的工作区根目录运行以下命令：

```
chapter_03$ bazel build ...
INFO: Analysed 4 targets (20 packages loaded, 620 targets configured).
INFO: Found 4 targets...
INFO: Elapsed time: 7.234s, Critical Path: 5.78s
INFO: 9 processes: 6 darwin-sandbox, 3 worker.
INFO: Build completed successfully, 16 total actions
```

这一次，`"..."` 元目标是在告诉 Bazel 构建当前目录以及该目录下的所有内容。当在工作区根级别执行时，它会构建你工作区中的*所有*内容。以这种方式构建时请谨慎，尽管在更新本地仓库后，这很可能是开启一天工作的好方式。

与 `:all` 元目标类似，`"..."` 元目标同样适用于其他 Bazel 命令（例如 `test`）。

另外，你也可以将“...”限定到特定目录。比如，你可以使用“bazel build src/...”来构建 `src` 目录下的所有内容。



### （几乎）全部清理干净

尽管 Bazel 在管理依赖方面表现非常出色，但你可能会在某个时刻需要彻底清空一切并重新开始。在 Bazel 中，清理操作非常简单：

```
chapter_03$ bazel clean
INFO: Starting clean.
```

就是这么简单。如果你在根目录下快速执行一次 `ls`，你会注意到所有 `bazel-*` 目录都不见了；所有输出、缓存等内容都已被移除。当然，在你下一次使用 Bazel 命令构建目标时，它们还会再次出现。

## 最后的话

恭喜你！你刚刚已经创建并连接好了第一组 Bazel 目标，涵盖了一系列不同功能：

*   `java_binary`
    *   表示并创建一个 Java 可执行文件

*   `java_library`
    *   封装可共享的 Java 功能模块

*   `java_import`
    *   将一个或多个现有 jar 文件封装为可被依赖的单元

*   `java_test`
    *   创建用于验证 `java_library` 预期行为的测试

即便只是 Bazel 构建目标中的这一小部分子集，你也已经具备了创建、组织、测试和运行 Java 程序的足够能力。

值得注意的是，本章专门聚焦于 Java 目标，以便说明 Bazel 的功能。不过，`{language}_binary`、`{language}_library` 和 `{language}_test` 这种模式，会在 Bazel（及其扩展）支持的各种语言中反复出现并变得熟悉。

例如：

*   C/C++（Bazel 内置支持）
    *   `cc_binary`、`cc_library`、`cc_test`

*   Python（Bazel 内置支持）
    *   `py_binary`、`py_library`、`py_test`

*   Go（由外部规则提供）
    *   `go_binary`、`go_library`、`go_test`

当然，Bazel 支持的每种语言也可能会有一些语言特定的构造（例如 `java_import`）；不过，即使在这些情况下，仍然存在大量在所有构建目标类型中基本通用的特性（例如名称、可见性、依赖关系等）。

在接下来的章节中，我们将减少对某一特定语言的关注，进一步深入 Bazel 本身的一些结构性元素，主要围绕 `BUILD` 和 `WORKSPACE files.` 展开。

### 练习——Python Helloworld

在本章中，我们始终只专注于创建 Java 目标。然而，开箱即用的 Bazel 具备面向 Java、Python 和 C++ 的构建能力。既然你已经完成了 Java 的 HelloWorld 练习，现在请用 Python 再实现一遍。

由于 Bazel 的一个标志性特征是可同时处理多种语言，你可以在同一个 `BUILD` 文件中，像 Java 目标那样创建一组类似的 `HelloWorld` Python 目标。就实际情况而言，你在真实项目中不太可能这样做；不过，这确实能够体现 Bazel 在同一 `BUILD` 文件中跨语言处理多个目标的能力。你的 Python 可执行程序最终会落在一个 py_binary 构建目标中。

最后，你还可以在独立的 `py_library` 构建目标中创建类似的 `IntMultiplier` 功能，并在对应的 `py_test` 构建目标中编写一组测试。与 Java 不同，Python 是“自带电池”的语言，内置了自己的单元测试框架，因此无需像 Java 那样再创建类似 `junit4` 的构建目标。

