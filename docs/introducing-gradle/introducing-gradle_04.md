# 4. 理解 Gradle 构建

Gradle 的核心是两个基本构建块——项目和任务。一个 Gradle 构建由一个或多个项目组成，每个项目包含一个或多个任务。本章将回顾这两个构建块。你将回顾这些构建块，并了解与构建相关的生命周期阶段。



## 项目

在 Gradle 中，项目是一个抽象概念，代表需要构建的产物。例如，一个 Gradle 项目可以是一个需要打包成 JAR 文件的 Java 应用程序。Gradle 项目也可以代表一项需要完成的工作。例如，可以使用 Gradle 项目来部署一个应用程序。

对于构建中的每个项目，Gradle 都会创建一个 `org.gradle.api.Project` 实例，并将其与构建脚本关联。这使得构建脚本能够在运行时使用 Project 的 API 来访问属性和自定义构建行为（例如，通过创建新任务或跳过现有任务）。表 4-1 列出了 Project API 中一些常用的属性和方法。

表 4-1.

常用的 Project API 属性和方法

| 属性/方法 | 描述 |
| --- | --- |
| `name` | 项目名称，可通过 `settings.gradle` 文件更改。默认为项目目录名称。 |
| `description` | 项目描述。通常在 `build.gradle` 文件中设置。 |
| `version` | 项目的版本。 |
| `parent` | 返回父项目（如果存在）。 |
| `group` | 用户定义的标识符，表示负责该项目的组或组织。例如，`org.hibernate` 或 `org.springframework`。 |
| `task` | 用于创建新任务的重载方法。 |
| `dependencies` | 配置项目的依赖项。 |
| `repositories` | 配置项目的仓库。 |
| `defaultTasks` | 配置要为项目运行的默认任务名称。 |

要访问 Project API 的属性/方法，可以使用 Gradle 与构建文件关联的隐式变量 `project`。清单 4-1 展示了一个位于 `chapter4` 文件夹中、名为 `project-explicit.gradle` 的 Gradle 构建文件。该构建文件包含设置 Project 的 description 并将其打印到控制台的代码。Gradle 将使用名称 `chapter4` 作为它为此构建创建的项目对象。

清单 4-1\. project-explicit.gradle 示例

`project.description = "Hello World Project"`

`println "Project Description ${project.description}"`

请注意，此新构建文件的名称并未使用 `build.gradle`。Gradle 默认会查找 `build.gradle` 文件来运行构建。也可以创建具有不同名称的构建文件。要使用这些文件触发构建，需要使用命令行选项 `-b` 或 `--build-file`，后跟文件名。在本章中，你将处理大量示例，并会创建单独的构建文件，然后使用 `-b` 选项运行它们。运行 `project-explicit.gradle` 构建文件将产生以下输出。

`chapter` `4` `>gradle -b project-explicit.gradle`

`Project Description Hello World Project`

Gradle 还允许你在访问 Project API 的方法或属性时省略项目变量名：

`description = "Hello World Project"`

`println "Project Description ${description}"`

可以使用 `ext {}` 闭包内的语法 `<<name>> =<< value>>` 或使用快捷方式 `ext.<<name>>=<<value>>` 来定义额外的项目级属性。清单 4-2 展示了一个名为 `project-add-properties.gradle` 的新构建文件中定义的两个额外属性——`environment` 和 `outputLang`。

清单 4-2\. project-add-properties.gradle 示例

`description = "Hello World Project"`

`println "Project Description ${description}"`

`ext {`

`outputLang = "English"`

`}`

`ext.environment = "local"`

`println "Output Language: ${project.outputLang}"`

`println "Environment: ${environment}"`

运行此脚本将产生以下输出。由于这些属性是在项目级别定义的，你可以直接访问它们的值，或使用 `project` 前缀。

`chapter` `4` `>gradle -b project-add-properties.gradle`

`Project Description Hello World Project`

`Output Language: English`

`Environment: local`

## 任务

Gradle 项目由一个或多个执行构建步骤的任务组成。任务执行诸如编译 Java 源代码并生成类或清理目标文件夹等操作。任务也可以依赖于其他任务。例如，运行测试用例的任务依赖于编译 Java 代码的任务。对应于构建文件中的每个任务，Gradle 都会创建一个 `org.gradle.api.Task` 实例。默认情况下，它将是 `org.gradle.api.DefaultTask` 类。表 4-2 展示了一些常用的与任务相关的 API 属性和方法。

表 4-2.

常用的与任务相关的 API 属性和方法

| 属性/方法 | 描述 |
| --- | --- |
| `name` | 任务名称。 |
| `description` | 任务描述。 |
| `group` | 任务所属的任务组。任务组提供了一种对相关任务进行逻辑分组的方法。 |
| `enabled` | 决定任务是启用还是禁用。 |
| `dependson` | 配置任务依赖关系。 |
| `doFirst` | 在任务操作列表的开头添加一个操作。 |
| `doLast` | 在任务操作列表的末尾添加一个操作。 |
| `onlyIf` | 仅当传入的闭包返回 true 时才运行任务。 |

在接下来的章节中，你将使用这些属性和方法来创建任务、添加操作以及声明任务之间的依赖关系。



### 创建任务

在最简单的层面上，创建一个任务只需要任务名称：

`task displayProperties`

尽管这个任务完全有效，但它实际上什么也不做。为了让任务执行有意义的操作，你需要为其添加一些动作。Gradle 提供了 `doFirst` 和 `doLast` 方法来将动作与任务关联。顾名思义，添加到 `doFirst` 方法的动作会位于待执行动作列表的开头。添加到 `doLast` 方法的动作则位于动作列表的末尾。清单 4-3 展示了更新后的 `displayProperties` 任务及其添加的动作。

**清单 4-3.** 带动作的 displayProperties

`description = "Sample Description for Project"`

`task displayProperties {`

`description = "Task to display properties"`

`ext {`

`taskProp1 = "Prop Val"`

`}`

`doFirst {`

`println "Project Description: $project.description"`

`println "Task name $name"`

`println "Task Description: $description"`

`}`

`doLast {`

`println "Task Property 1: $taskProp1"`

`}`

`}`

清单 4-3 更新了项目对象的 `description` 属性，然后在任务的闭包内更新了任务的 `description` 属性。Gradle 允许你使用 `ext` 闭包在任务级别声明额外的属性。跨任务和其他配置元素共享的属性通常被声明为项目属性。例如，依赖工件的版本号通常被声明为项目属性（更多内容见第 6 章）。特定于某个任务的属性则被声明为任务属性。在清单 4-3 中，使用了 `ext` 闭包来声明一个名为 `taskProp1` 的任务属性。代码还为 `doFirst` 方法添加了三个动作，为 `doLast` 方法添加了一个动作。在 `doFirst` 方法中，`$project` 变量将项目对象的描述打印到控制台。由于 `name` 和 `description` 是任务属性，你可以直接访问它们来打印其值。

创建一个名为 `display-properties.gradle` 的新构建文件，并复制清单 4-3 的内容。执行 `displayProperties` 任务，你应该会看到以下输出。如你所见，`doFirst` 内的动作先被执行，然后是 `doLast` 块内的动作。

`\chapter` `4` `>gradle -b display-properties.gradle displayProperties`

`:displayProperties`

`Project Description: Sample Description for Project`

`Task name displayProperties`

`Task Description: Task to display properties`

`Task Property 1: Prop Val`

`BUILD SUCCESSFUL`

`Total time: 4.046 secs`

**提示**

在命令行执行任务时，不必指定完整的任务名称。驼峰式命名的任务可以通过缩写每个单词来运行。例如，`displayProperties` 任务可以使用以下快捷方式运行：`gradle -b display-properties.gradle dP`。

为了让 Gradle 执行任务，你需要将任务名称作为 Gradle 命令的一部分传入，正如你在之前的执行中所见。Gradle 提供了一种方法，可以使用 `defaultTasks` 方法定义一个或多个默认任务，当没有提供其他任务时可以运行这些默认任务。清单 4-4 展示了如何使用 `defaultTasks` 方法将 `displayProperties` 定义为默认任务。

**清单 4-4.** 带默认任务的 displayProperties

`description = "Sample Description for Project"`

`defaultTasks 'displayProperties'`

`task displayProperties {`

`description = "Task to display properties"`

`// 为简洁起见，内容已移除`

`}`

一旦你用 `defaultTasks` 代码更新了 `display-properties.gradle` 文件，你就可以使用 `gradle -b display-properties.gradle` 命令触发任务执行。

### 任务依赖

非平凡的构建通常包含多个任务，并且一个任务通常需要其依赖的另一个任务完成后才能运行。例如，在运行打包任务来组装工件之前，你必须先运行编译源代码的任务。同样，在编译任务运行之前，它可能需要运行一个任务来清理输出文件夹。Gradle 提供了几种声明此类任务依赖关系的方法。

清单 4-5 展示了 `task-dependencies.gradle` 构建文件，其中包含三个任务——`clean`、`compile` 和 `package`。这个例子采用了两种声明依赖关系的方法。`dependsOn` 方法使 `compile` 任务依赖于 `clean` 任务。要使 `package` 任务依赖于 `compile`，你需要使用任务的 `dependsOn` 属性。在 Gradle 中，`package` 是一个保留字，要将其用作任务名称，你必须将其作为参数传递给 `task` 方法。另外，请注意使用 `<<` 运算符作为 `doLast` 方法的快捷方式。

**清单 4-5.** task-dependencies.gradle

`task clean << {`

`println 'Executing clean task'`

`}`

`task compile << {`

`println 'Executing compile task'`

`}`

`compile.dependsOn 'clean'`

`task ("package", dependsOn : 'compile') << {`

`println 'Executing package task'`

`}`

运行 `package` 任务，你会看到 `clean` 和 `compile` 任务在运行 `package` 任务之前被执行：

`\chapter` `4` `>gradle -b task-dependencies.gradle package`

`:clean`

`Executing clean task`

`:compile`

`Executing compile task`

`:package`

`Executing package task`

`BUILD SUCCESSFUL`

为了确定任务需要运行的顺序，Gradle 会创建一个有向无环图（DAG）。每个要执行的任务都成为图中的一个节点。任务之间的依赖关系用于创建节点之间的边。图 4-1 展示了清单 4-5 中任务的 DAG 表示。

![A978-1-4842-1031-4_4_Fig1_HTML.gif](img/A978-1-4842-1031-4_4_Fig1_HTML.gif)

**图 4-1.** Gradle 的任务 DAG 表示

DAG 不包含有向环。换句话说，不可能从一个节点出发，沿着一条边序列，再回到同一个节点。因此，Gradle 永远不会执行一个已经执行过的任务。

考虑清单 4-6 中的场景，其中任务 A 和任务 B 都依赖于任务 C。

**清单 4-6.** task-dependencies2.gradle

`task A << { println 'In task A' }`

`task B << { println 'In task B' }`

`task C << { println 'In task C' }`

`A.dependsOn 'C'`

`B.dependsOn 'C'`

由于 Gradle 为任务执行创建了 DAG，当任务 A 和 B 运行时，Gradle 只运行任务 C 一次。创建一个包含清单 4-6 内容的 `task-dependencies2.gradle` 文件，并运行以下命令来验证这一点：

`\chapter` `4` `>gradle -b task-dependencies2.gradle A B`

`:C`

`In task C`

`:A`

`In task A`

`:B`

`In task B`

考虑另一个不同的场景，其中任务 A 依赖于任务 B 和任务 C，如清单 4-7 所示。

**清单 4-7.** task-dependencies3.gradle

`task A << { println 'In task A' }`

`task B << { println 'In task B' }`

`task C << { println 'In task C' }`

`A.dependsOn 'B'`

`A.dependsOn 'C'`

当任务 A 运行时，`dependsOn` 确保 B 和 C 在执行 A 之前运行。创建一个包含清单 4-7 内容的 `task-dependencies3.gradle` 文件并执行任务 A。你应该会看到以下输出：

`\chapter` `4` `>gradle -b task-dependencies3.gradle A`

`:B`

`In task B`

`:C`

`In task C`

`:A`

`In task A`



`dependsOn` 并不能保证依赖任务的执行顺序。例如，在清单 4-7 中，无法保证任务 B 会在任务 C 之前执行，反之亦然。有时你可能需要确保 B 在 C 之前运行。为此，Gradle 提供了 `mustRunAfter` 方法。清单 4-8 展示了在前一个清单的基础上添加了 `mustRunAfter` 的修改版本。

**清单 4-8. task-dependencies4.gradle**

`task A << { println 'In task A' }`

`task B << { println 'In task B' }`

`task C << { println 'In task C' }`

`A.dependsOn 'B'`

`A.dependsOn 'C'`

`B.mustRunAfter 'C'`

创建一个包含清单 4-8 内容的 `task-dependencies4.gradle` 文件，并运行任务 A。你应该会看到任务按照 C、B、A 的顺序执行，如下所示：

`\chapter` `4` `>gradle -b task-dependencies4.gradle A`

`:C`

`In task C`

`:B`

`In task B`

`:A`

`In task A`

### 跳过任务

有时你可能希望跳过某个任务的执行。例如，你可能只想在开发环境中运行某些任务，而在测试和生产环境中跳过它们。为此，Gradle 提供了 `onlyIf` 方法，该方法接受一个闭包。如果闭包返回 `false`，则跳过该任务的执行。

清单 4-9 展示了一个仅在非生产环境中运行的任务。`onlyIf` 闭包检查一个名为 `env` 的属性是否存在，并验证其值是否等于 `Prod`。

**清单 4-9. skip-task.gradle**

`task skipInProd {`

    `onlyIf {`

        `!project.hasProperty('env') || project.env != "Prod"`

    `}`

    `doLast {`

        `println 'Task execution complete'`

    `}`

`}`

你可以使用命令行参数 `-Pname=value` 将外部属性传递给 Gradle 构建脚本。将清单 4-9 的内容复制到 `skip-task.gradle` 文件中，并使用命令行参数 `-Penv=Prod` 执行 `skipInProd` 任务。你应该会看到任务被跳过执行，如下所示：

`\chapter` `4` `>gradle -b skip-task.gradle skipInProd -Penv=Prod`

`:skipInProd SKIPPED`

`BUILD SUCCESSFUL`

`Total time: 2.82 secs`

Gradle 提供了另一种通过任务的 `enabled` 属性来跳过任务的技术。默认情况下，`enabled` 属性被设置为 `true`。但是，可以通过编程方式切换其值，从而影响任务的执行。清单 4-10 展示了使用 `enabled` 属性重写的 `skipInProd` 任务。

**清单 4-10. 使用 enabled 属性跳过任务**

`task skipInProd {`

    `enabled = !project.hasProperty('env') || project.env != "Prod"`

    `doLast {`

        `println 'Task execution complete'`

    `}`

`}`

### Gradle 任务类型

在本章前面部分，你已经了解到 Gradle 会为构建文件中找到的每个任务创建一个 `org.gradle.api.Task` 实例。默认情况下，这些任务是 `org.gradle.api.DefaultTask` 的实例，因此继承了 `DefaultTask` 的属性和方法。Gradle 提供了几种开箱即用的任务类型，你的任务可以扩展这些类型，从而获得额外的功能，例如创建 ZIP 归档或执行 Java 程序的能力。本节将介绍其中一些开箱即用的任务类型。

#### Zip

`Zip` 任务允许你从一组文件/目录创建 ZIP 归档。以下是一个 `Zip` 任务的示例：

`task archiveTask (type : Zip) {`

    `archiveName  'images.zip'`

    `from 'images'`

    `into "$buildDir/dist"`

`}`

`archiveTask` 从 `images` 目录读取文件/文件夹，创建一个名为 `images.zip` 的 ZIP 文件，并将其放置在 `build/dist` 文件夹中。

#### Copy

`Copy` 任务允许你将文件从一个或多个源复制到目标目录。以下是一个 `Copy` 任务的示例：

`task copyTask(type : Copy) {`

    `from 'src/config/dev.cert'`

    `from 'src/db'`

    `into 'dist'`

`}`

`copyTask` 会将一个名为 `dev.cert` 的单个文件以及 `src/db` 文件夹中的所有文件复制到 `dist` 文件夹中。

#### Exec

`Exec` 任务允许你执行一个命令行进程。示例如下：

`task showWindowsVersion(type:Exec) {`

  `commandLine 'cmd', 'ver'`

`}`

在 Windows 机器上，此任务会查找 Windows 操作系统版本。`commandLine` 属性需要完整的命令行，包括可执行文件和参数。但是，你需要将命令字符串按空格分割，并以逗号分隔的形式提供分割后的项。因此，命令 `cmd ver` 被分割并作为 `cmd, ver` 提供。

#### Delete

`Delete` 任务允许你删除文件或文件夹。以下任务删除了两个文件夹——`libs` 和 `docs`。

`task cleanTask (type : Delete) {`

    `delete 'build/libs', 'build/docs'`

`}`

## 构建生命周期

每个 Gradle 构建都会经历三个不同的生命周期阶段：

初始化阶段：在此阶段，Gradle 识别所有需要处理的 `build.gradle` 文件。对于单项目构建，只会识别一个构建文件。对于多项目构建（更多内容请参见第 7 章），Gradle 会尝试定位与这些项目相关的所有可能的构建文件。一旦 Gradle 识别出参与构建的项目，它将为每个项目创建一个对应的 `org.gradle.api.Project` 实例。   配置阶段：在此阶段，所有已识别项目的构建脚本都会被执行。请务必记住，此阶段不会执行任何实际的任务。相反，Gradle 会为每个项目构建一个任务对象的 DAG（有向无环图）。Gradle 还会执行所有任务的配置部分，包括被禁用的任务。   执行阶段：在此阶段，Gradle 识别出需要执行的任务，并按正确的顺序运行它们。  

为了更好地理解构建生命周期，请考虑清单 4-11 中所示的 `build-phases.gradle` 构建脚本。该脚本包含两个任务——`noActionTask` 和 `withActionTask`。这两个任务都直接在任务的闭包中包含 Groovy 代码。此外，`withActionTask` 在 `doLast` 方法内部也有代码。

**清单 4-11. build-phases.gradle**

`task noActionTask {`

    `description = "A task with no actions"`

    `println "I am in the configuration block of $name"`

`}`

`task withActionTask {`

    `println "I am in the configuration block of $name"`

    `doLast {`

        `println "performing an action in $name"`

    `}`

`}`

在配置阶段，Gradle 会执行直接在任务闭包中指定的代码。配置阶段完成后，Gradle 会在执行阶段执行 `doFirst` 和 `doLast` 内部的操作。例如，运行一个列出所有任务的命令会执行初始化和配置阶段，产生以下输出：

`\chapter` `4` `>gradle -q -b build-phases.gradle tasks`

`I am in the configuration block of noActionTask`

`I am in the configuration block of withActionTask`

---------------output truncated---------------------

运行 `withActionTask` 将导致执行两个配置块以及 `doLast` 方法中的操作。以下是产生的输出：

`\chapter` `4` `>gradle -q -b build-phases.gradle wAT`

`I am a in the configuration block of noActionTask`

`I am a in the configuration block of withActionTask`

`performing an action in withActionTask`



## 总结

在本章中，你学习了 Gradle 构建的两个基本组成部分：项目和任务。你了解到一个任务可以包含一个或多个在 `doFirst` 或 `doLast` 代码块中定义的操作。任务可以依赖于其他任务，Gradle 会创建一个有向无环图（DAG）来确保任务按正确顺序执行，同时也确保每个任务只执行一次。你了解了两种用于执行或跳过任务的技术——`onlyIf` 和 `enable`。最后，你回顾了 Gradle 构建的三个阶段。

在下一章中，你将使用 Gradle 构建 Java 和 Web 项目。你还将学习 Gradle 插件，并构建一个自定义插件。

