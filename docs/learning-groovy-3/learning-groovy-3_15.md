# 12. Gradle

Gradle 是一个用于 JVM 的构建工具，它使用基于 Groovy 的 DSL 来构建项目。Gradle 网站对其描述如下：

*   Gradle 将 Ant 的强大和灵活性与 Maven 的依赖管理和约定相结合，形成了一种更有效的构建方式。由 Groovy DSL 驱动并充满创新，Gradle 通过合理的默认值提供了一种声明式的方式来描述各种类型的构建。

*   —gradle.org (2015)^(⁴²)

## 项目与任务

每个 Gradle 构建由一个或多个项目组成，每个项目由任务组成。Gradle 构建的核心是 `build.gradle` 文件（称为*构建脚本*）。

要尝试一下，请访问 [`www.gradle.org`](http://www.gradle.org) 并安装 Gradle。然后创建你自己的 `build.gradle` 文件。

任务可以通过编写 `task` 后跟任务名称，再跟一个闭包来定义。使用带有闭包的 `doLast` 方法来执行任意代码。例如：

```
1   task upper { doLast {
2           String someString = 'test'
3           println "Original: $someString"
4           println "Uppercase: " + someString.toUpperCase()
5   }}
```

任务可以包含任何 Groovy 代码。

与 Ant 非常相似，一个任务可以依赖于其他任务，这意味着这些任务需要在该任务之前运行。你只需调用 `dependsOn`，并将任意数量的任务名称作为参数传入。例如：

```
1   task buildApp {
2           dependsOn clean, installApp, processAssets
3   }
```

你也可以使用以下替代语法：

```
1   task buildApp(dependsOn: [clean, installApp, processAssets])
```

### ![../images/426440_2_En_12_Chapter/426440_2_En_12_Figa_HTML.gif](img/426440_2_En_12_Figa_HTML.gif)提示

如果你的任务包含你希望任务运行的实际代码，请使用 `doLast` 方法。你是在向任务添加一个闭包，而不是配置任务。否则，Gradle 总是在第一遍运行时执行代码，而不仅仅是在你调用任务时。

定义好任务后，你可以通过在命令行中调用 `gradle <task_name>` 来运行它们。有一些内置任务。例如，要列出所有可用任务，请调用以下命令：

```
1   gradle tasks
```

## 插件

Gradle 核心内置的功能很少，但它拥有强大的插件，使其非常灵活。一个插件可以执行以下一项或多项操作：

*   向项目添加任务（例如，编译和测试）。

*   使用有用的默认值预配置添加的任务。

*   向项目添加依赖配置。

*   通过扩展向现有类型添加新的属性和方法。

我们将专注于构建基于 Groovy 的项目，因此我们将使用 Groovy 插件（但是，Gradle 并不局限于 Groovy 项目！）：

```
1   apply plugin: 'groovy'
```

该插件使用 Maven 的约定。例如，它期望你的主源代码位于 `src/main/groovy` 下，测试源代码位于 `src/test/groovy` 下。



## 配置任务

添加了一些插件后，您可能希望根据自己的目的配置任务的某些属性。

例如，您可能希望为 Gradle 的 `wrapper` 任务指定一个 Gradle 版本：

```
1   task wrap(type: Wrapper) {
2       gradleVersion = '5.4.1'
3   }
```

可用的属性取决于您正在配置的任务。在前面的例子中，我们正在配置 Gradle 的 Wrapper 任务以使用 Gradle 版本 `5.4.1`。运行此任务将创建一个 `"gradlew"` 脚本文件，该文件可以代替 `gradle` 运行，这样无论当前机器上安装了什么版本，都会运行相同版本的 Gradle。这对于确保每次构建都以相同方式运行非常有用。

## 额外配置

要在 Gradle 构建文件中提供额外属性，请使用 `ext` 方法。您可以在闭包内定义任意值，这些值将在整个项目中可用。

您也可以应用来自其他 Gradle 构建文件的属性。例如：

```
1   ext {
2       apply from: 'props/another.gradle'
3       myVersion = '1.2.3'
4   }
```

在此闭包内定义的属性可以在您的任务中或定义依赖项时使用。

## 依赖项

每个 Java 项目往往依赖于许多开源项目来构建。Gradle 构建在 Maven 之上，因此您可以使用简单的 DSL 轻松包含依赖项，如下例所示：

```
1   apply plugin: 'java'

3   sourceCompatibility = 1.11

5   repositories {
6           mavenLocal()
7           mavenCentral()
8   }

10      dependencies {
11              compile 'com.google.guava:guava:27.1-jre'
12              compile 'org.groocss:groocss:1.0-M2'
13              testCompile group: 'junit', name: 'junit', version: '5.+'
14              testCompile "org.mockito:mockito-core:2.28.2"
15      }
```

此构建脚本使用 `sourceCompatibility` 将 Java 源代码版本定义为 `1.11`（在编译期间使用）。接下来，它告诉 Maven 首先使用本地仓库（`mavenLocal`），然后使用 Maven 中央仓库。

在 `dependencies` 块中，此构建脚本为 `compile` 范围定义了两个依赖项，为 `testCompile` 范围定义了两个依赖项。`testCompile` 范围内的 JAR 包仅由测试使用，不会包含在任何最终产品中。

JUnit 的那一行展示了定义依赖项的更详细风格。它还使用 `+` 指定版本为 5.0 或更高。

您也可以通过调用 `maven` 并提供一个提供适当参数（至少一个 URL）的闭包来指定您自己的 Maven 仓库。例如（在 `repositories` 部分）：

```
1   maven {url "https://oss.sonatype.org/content/repositories/snapshots/" }
2   maven { url = "$nexus/content/groups/public"
3           credentials {
4                   username 'deployment'
5                   password deploymentPassword
6           }
7   }
```

第二个示例演示了使用安全的 Maven 仓库。它还演示了使用变量 `nexus` 和 `deploymentPassword`，这些变量可以（可能应该）存储在 `gradle.properties` 文件中。

## Gradle 属性

`gradle.properties` 文件允许您指定 Gradle 属性以及构建脚本可用的其他属性。例如，您可以指定 JVM 参数以及是否要使用 Gradle 守护进程（它在后台运行并加速后续的 Gradle 构建；这是默认设置）：

```
1   org.gradle.daemon=true
2   org.gradle.jvmargs=-Xms128m -Xmx512m
```

您也可以指定不想保留在版本控制系统中的构建特定值（例如 Nexus 凭据）。例如，您可以将以下内容添加到本地的 `gradle.properties` 文件中：

```
nexusUser=user
nexusPassword=changeme123
```

现在，`nexusUser` 和 `nexusPassword` 都将是 Gradle 构建文件中的可用属性。

## 多项目构建

多项目构建可以包含任意数量的子项目，这些子项目一起构建。每个子项目应放在一个顶层目录下的子目录中，每个子目录的名称就是子项目的名称。

多项目构建中的各个构建可以相互依赖。您可以使用以下语法表达对任意数量子项目的依赖：

```
1   dependencies {
2     compile project(':subproject1')
3     compile project(':subproject2')
4   }
```

多项目构建的顶层 `build.gradle` 文件应类似于以下内容：

```
1   allprojects {
2     apply plugin: "groovy"
3   }
4   project(":subproject1") {
5     dependencies {}
6   }
7   project(":subproject2") {
8     dependencies {}
9   }
```

您可以通过创建一个目录并在其中放置一个 `build.gradle` 文件来创建子项目。例如（在类 Unix 系统上）：

```
mkdir subproject1
touch subproject1/build.gradle
mkdir subproject2
touch subproject2/build.gradle
```

最后，您应该创建一个名为 `settings.gradle` 的文件，该文件调用 `include` 并传入子项目列表。例如，对于上述子项目，在 `settings.gradle` 中使用以下内容：

```
include 'subproject1', 'subproject2'
```

## 文件操作

由于 Gradle 在实际执行任务之前会先评估任务，因此通常不应直接使用 `java.util.File` 来定义文件或目录。相反，Gradle 提供了许多内置方法和任务。例如，您应该对单个文件或目录使用 `file` 方法，并使用 `files` 方法来定义文件或目录的集合。

```
1   outputDir = file("libs/x86")
```

还有一个 `fileTree` 方法用于递归列出目录中的文件。例如，您可以通过以下方式依赖于 `lib` 目录下的所有文件：

```
1   dependencies {
2           compile fileTree('lib')
3   }
```

要将文件从一个位置复制到另一个位置，请使用 `Copy` 任务。这是一个很好的例子：

```
1   task copyImages(type: Copy) {
2           from 'assets'
3           into 'build/images'
4           include '**/*.jpg'
5           exclude '**/*test*'
6   }
```

此任务会将所有以 `.jpg` 结尾的图像从 `assets` 目录复制到 `build/images` 目录，排除任何包含单词 `test` 的文件。

## 探索

请记住，您还可以使用 `.properties` 轻松列出 Groovy 中对象的属性。这可以帮助您在运行时探索可用的 Gradle 属性。例如：

```
1   task testIt {
2           doLast { println sourceSets.main.properties }
3   }
```

### 完全 Groovy

请记住，Groovy 的所有优点在 Gradle 中都可使用。例如，以下单行代码使用“星点”表示为两个任务设置编码选项为 `UTF-8`（当您的代码包含非 ASCII 字符时，这很有用）：

```
1   [compileJava, compileTestJava]*.options*.encoding = 'UTF-8'
```

### ![../images/426440_2_En_12_Chapter/426440_2_En_12_Figb_HTML.gif](img/426440_2_En_12_Figb_HTML.gif)练习

通过制作自己的 `build.gradle` 并尝试本章中的所有内容来探索 Gradle。

## 总结

本章向您介绍了关于 Gradle 的以下内容：

*   如何创建任务

*   如何使用插件

*   如何指定依赖项

*   `gradle.properties` 是什么

*   如何创建多项目构建

*   使用内置方法进行文件操作

### ![../images/426440_2_En_12_Chapter/426440_2_En_12_Figc_HTML.gif](img/426440_2_En_12_Figc_HTML.gif)在线文档

Gradle 在 [`www.gradle.org`](http://www.gradle.org) 上提供了庞大的在线用户指南。^(⁴³)

脚注 1   2



