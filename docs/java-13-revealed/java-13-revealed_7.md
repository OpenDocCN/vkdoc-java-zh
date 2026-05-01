# 7. 类数据共享

在本章中，你将学习

*   什么是类数据共享（CDS）
*   什么是应用程序类数据共享（AppCDS）
*   如何在你的应用程序中使用 CDS 和 AppCDS
*   JDK12 中的默认 CDS 归档是什么，以及 Java 应用程序如何自动使用它
*   JDK13 中的动态 CDS 归档是什么
*   如何为你的应用程序生成和使用动态 CDS 归档

当 JVM 启动时，它通常会从 Java 核心库加载数千个类。在启动时加载如此多的类需要时间。如果你启动多个 JVM，每个 JVM 都会执行相同的任务——在启动时加载相同的数千个系统类副本。这有两个缺点：

*   JVM 启动速度变慢。
*   每个 JVM 都会将相同的系统类副本加载到内存中，从而增加了运行时内存占用。

类数据共享（CDS）——在 JDK5 中引入——是一项 HotSpot JVM 特性，它允许预处理系统类并在 JVM 之间共享它们，从而减少启动时间和运行时内存占用。CDS 允许共享由引导类加载器加载的 Java 核心类。

应用程序类数据共享（AppCDS）——在 JDK10 中引入——扩展了 CDS 特性，允许在 JVM 之间共享类路径上的应用程序类。从 JDK10 开始，所有类型的类加载器（引导类加载器、平台类加载器、应用程序类加载器和自定义类加载器）都可以使用 AppCDS 加载类。JDK13 引入了动态 CDS 归档，这使得使用 AppCDS 变得更加简单。

我将在后续章节中详细解释 CDS 和 AppCDS。

### 提示

AppCDS 在 Oracle JDK 8 和 9 中可用，但需要商业许可。在 JDK 10 中，AppCDS 在 OpenJDK 中可用。

## 类数据共享

类数据共享（CDS）在 JDK5 中引入。JRE 安装程序会加载常用的核心 Java 类到内存中，并将加载的类转储到一个名为*共享归档*的文件中。共享归档包含以预处理格式存储的类，以便后续进行内存映射。共享归档是位于 `JAVA_HOME` 目录下的 `classes.jsa` 文件。在类 UNIX 平台上，共享归档存储在 `JAVA_HOME/lib/[arch]/server/classes.jsa`。在 Windows 平台上，共享归档存储在 `JAVA_HOME\bin\server\classes.jsa`。如果你在机器上找不到共享归档，可以随时生成它。有时在对系统类进行更改后，你需要重新生成它。稍后我将向你展示如何生成共享归档。

当 JVM 启动时，它会查找共享归档并对共享归档进行内存映射。当 JVM 需要加载一个类时，它会首先尝试从内存中的共享归档加载该类，而不是从磁盘加载，从而减少了类加载时间。如果另一个 JVM 启动，该 JVM 会首先检查共享归档数据是否已被另一个 JVM 加载到内存中，并重用内存中共享归档中的相同类，从而进一步减少第二个及后续 JVM 的启动时间，并通过在内存中共享同一共享归档中的相同类数据来显著减少内存占用。

CDS 支持 G1、串行、并行和 ParallelOldGC 垃圾回收器。共享 Java 堆对象特性（CDS 的一部分）仅在 64 位非 Windows 平台上支持 G1 垃圾回收器。

### 创建共享归档

你可以使用带有 `-XShare:dump` 选项的 `java` 命令来创建共享归档，如下所示：

```
C:\Java13Revealed>java -Xshare:dump
```

该命令会在 `JAVA_HOME` 目录的子目录中创建一个共享归档（`classes.jsa` 文件），如上一节所述。该命令在创建共享归档时会打印信息性消息。

### 提示

如果你使用的是 JDK12 或更高版本，则默认 CDS 归档已作为 JDK 的一部分提供，你无需运行此命令。运行此命令将重新生成默认 CDS 归档。

### 使用共享归档

在本节中，我将向你展示在运行 Java 应用程序时如何使用共享归档。本章中的所有示例程序都在 `jdojo.cds` 模块中，该模块在清单 7-1 中定义。

```
// module-info.java
module jdojo.cds {
    exports com.jdojo.cds;
}
清单 7-1
一个名为 jdojo.cds 的模块
```

让我们创建一个简单的 Java 类，你将运行它来测试 CDS 特性。清单 7-2 包含一个名为 `HelloCDS` 的简单类的代码。它向标准输出打印一条消息。

```
// HelloCDS.java
package com.jdojo.cds;
public class HelloCDS {
    public static void main(String[] args) {
        System.out.println("Hello, CDS and AppCDS");
    }
}
清单 7-2
一个 HelloCDS 类
```

源代码中包含这些类，这些类是在 JDK13 中使用 `--enable-preview` 选项编译的，因为源代码使用了预览特性，例如 `switch` 表达式和文本块。如果你想在其他 JDK 上进行实验，则需要自行编译和打包本章的代码。编译后的模块代码保存在 `build\modules\jdojo.cds` 目录中。模块 JAR 保存在 `dist\jdojo.cds.jar` 文件中。我假设你正在下载源代码的目录中运行这些命令，并且 `JAVA_HOME\bin` 目录已添加到 PATH 环境变量中。

使用以下命令编译源代码：

```
C:\Java13revealed>javac -d build\modules\jdojo.cds src\jdojo.cds\classes\module-info.java src\jdojo.cds\classes\com\jdojo\cds\HelloCDS.java
```

使用以下命令将源代码打包到 `jdojo.cds.jar` 文件中。请注意命令中的最后一个字符，它是一个点（`.`）。

```
C:\Java13revealed>jar --verbose --create --file dist\jdojo.cds.jar -C build\modules\jdojo.cds .
```

在运行应用程序时，可以使用以下三个选项之一来使用共享归档：

*   `-Xshare:on`
*   `-Xshare:off`
*   `-Xshare:auto`

`-Xshare:on` 选项要求使用共享类数据。如果无法使用共享类数据，此选项会导致应用程序启动失败。此选项仅用于测试目的。如果共享归档无法共享，有时可能会失败。如果由于所需地址空间不可用或类路径无法映射而导致内存映射不可能，则可能发生失败。请勿在生产环境中使用此选项。

`-Xshare:off` 选项不会尝试使用共享归档。

`-Xshare:auto` 选项会尽可能使用共享归档。如果无法使用共享归档，此选项不会导致失败。建议使用 `-Xshare:auto` 选项。



### 提示

从 JDK11 开始，服务器端 VM 默认启用 `-Xshare:auto` 选项。

以下命令尝试使用 JDK10 运行 `HelloCDS` 类——一次使用 `-Xshare:off`，一次使用 `-Xshare:on`：

```
C:\Java13Revealed>java -Xshare:off --module-path dist\jdojo.cds.jar --module jdojo.cds/com.jdojo.cds.HelloCDS
Hello, CDS and AppCDS
C:\Java13Revealed>java -Xshare:on --module-path dist\jdojo.cds.jar --module jdojo.cds/com.jdojo.cds.HelloCDS
An error has occurred while processing the shared archive file.
Specified shared archive not found.
Error occurred during initialization of VM
Unable to use shared archive.
```

当你使用 `-Xshare:on` 时，收到一个错误，提示找不到共享归档。在 OpenJDK10 和 OpenJDK11 中，你需要使用 `-Xshare:dump` 选项来生成共享归档。从 OpenJDK12 开始，构建 JDK 镜像时会自动生成共享归档。如果你在前一节中已经生成了共享归档，那么这条命令本应成功运行。以下是使用 JDK10 生成共享归档并重新运行之前命令的方法：

```
C:\Java13Revealed>java -Xshare:dump
...
C:\Java13Revealed>java -Xshare:on --module-path dist\jdojo.cds.jar --module jdojo.cds/com.jdojo.cds.HelloCDS
Hello, CDS and AppCDS
```

当共享归档被禁用和启用时，你在输出中看不到任何区别。然而，第二次——当共享归档启用时——JVM 启动得更快。如何证明这一点？

在类 UNIX 操作系统上，`time` 命令用于测量进程运行时间。在 Windows 上，你需要使用 `Measure-Command` PowerShell 命令。所有命令均使用 JDK10。以下是在 Linux 上的结果：

```
[/home/ksharan/Java13Revealed] $ time java -Xshare:off --module-path dist/jdojo.cds.jar --module jdojo.cds/com.jdojo.cds.HelloCDS
Hello, CDS and AppCDS
real 0m0.215s
user 0m0.124s
sys 0m0.034s
[/home/ksharan/Java13Revealed] $ time java -Xshare:on --module-path dist/jdojo.cds.jar --module jdojo.cds/com.jdojo.cds.HelloCDS
Hello, CDS and AppCDS
real 0m0.108s
user 0m0.098s
sys 0m0.023s
```

以下是在 Windows 上使用 PowerShell（而非 Windows 命令提示符）的结果：

```
PS C:\Java13revealed> Measure-Command {java -Xshare:off --module-path dist\jdojo.cds.jar --module jdojo.cds/com.jdojo.cd
s.HelloCDS | Write-Host}
Hello, CDS and AppCDS
...
TotalMilliseconds : 281.1476
PS C:\Java13revealed> Measure-Command {java -Xshare:on --module-path dist\jdojo.cds.jar --module jdojo.cds/com.jdojo.cds
.HelloCDS | Write-Host }
Hello, CDS and AppCDS
...
TotalMilliseconds : 180.3489
```

注意第二次运行程序所花费的时间，比第一次要少。在 Linux 上，应用程序运行速度大约快了 49%，而在 Windows 上，大约快了 36%。你得到的结果可能略有不同。

### 了解已加载类的来源

当 CDS 启用时，核心系统类从共享归档中加载。你可以使用 `-Xlog:class+load=info` 选项打印已加载类的来源。以下命令在 JDK10 上使用此选项——一次在 CDS 启用时，一次在 CDS 禁用时：

```
C:\Java13Revealed>java -Xshare:off -Xlog:class+load=info --module-path dist\jdojo.cds.jar --module jdojo.cds/com.jdojo.cds.HelloCDS
[0.011s][info][class,load] opened: C:\java10\lib\modules
[0.021s][info][class,load] java.lang.Object source: jrt:/java.base
[0.022s][info][class,load] java.io.Serializable source: jrt:/java.base
[0.022s][info][class,load] java.lang.String source: jrt:/java.base
...
[0.202s][info][class,load] com.jdojo.cds.HelloCDS source: file:/C:/Java13Revealed/dist/jdojo.cds.jar
...
Hello, CDS and AppCDS
...
C:\Java13Revealed>java -Xshare:on -Xlog:class+load=info --module-path dist\jdojo.cds.jar --module jdojo.cds/com.jdojo.cds.HelloCDS
[0.012s][info][class,load] opened: C:\java10\lib\modules
[0.024s][info][class,load] java.lang.Object source: shared objects file
[0.024s][info][class,load] java.io.Serializable source: shared objects file
[0.024s][info][class,load] java.lang.String source: shared objects file
...
[0.150s][info][class,load] com.jdojo.cds.HelloCDS source: file:/C:/Java13Revealed/dist/jdojo.cds.jar
...
Hello, CDS and AppCDS
...
```

当 CDS 禁用时，诸如 `Object` 和 `String` 类等系统类的来源被列为 `jrt:/java.base`。这意味着这些类是从磁盘上的 `C:\java10\lib\modules` 文件（即 JRE 运行时镜像）加载的。当 CDS 启用时，相同类的来源被列为“shared objects file”，这意味着它们是从共享归档加载的。请注意，应用程序类 `HelloCDS` 在两种情况下都是从磁盘加载的，因为 CDS 只允许共享系统类数据。要共享应用程序类，你需要使用 AppCDS，这将在下一节中解释。

## 应用程序类数据共享 (AppCDS)

在 JDK10 之前，类数据的共享仅适用于由引导类加载器加载的核心 Java 类。JDK10 扩展了 CDS 功能以共享应用程序类。在 JDK10 中，平台类加载器、应用程序类加载器和自定义类加载器也可以从共享归档中加载类。

在 JDK10 中，你需要将 `-XX:+UseAppCDS` 选项与 `java` 命令一起使用才能使用 AppCDS。在 JDK11 及更高版本中，此选项已废弃。

要共享应用程序类数据，你需要创建自己的共享归档，其中将包含应用程序类以及核心 Java 类。当你运行应用程序时，需要使用 `-XX:SharedArchiveFile` 选项指定你的共享归档文件路径。你的共享归档将包含 `jdojo.cds.jar` 文件中的所有类，该文件位于本书源代码的 `dist` 目录中。提供的源代码是使用 JDK13 编译的。如果你使用的是较早的 JDK，则需要重新编译并重新创建 `jdojo.cds.jar` 文件。

### 提示

Java 10 仅支持对从类路径（而非模块路径）加载的类使用 AppCDS。对模块路径的支持在 JDK11 及更高版本中可用。

使用 AppCDS 涉及三个步骤：

*   为要包含在共享归档中的类创建类列表

*   生成共享归档

*   在运行应用程序时使用共享归档

以下各节将引导你完成使用 AppCDS 的步骤。我将使用 JDK10 和 JDK11 展示相同的步骤。你可以选择任意一个 JDK，并按照该 JDK 的说明进行操作。



### 创建类列表

首先，你需要创建一个类列表，其中包含你想要纳入 AppCDS 共享归档的类。你可以使用 `-XX:DumpLoadedClassList` 选项将 JVM 加载的系统类列表转储到一个文件中。`-XX:DumpLoadedClassList` 选项仅包含由引导类加载器加载的类。请确保使用 `-XX:+UseAppCDS` 选项来包含由应用程序和平台类加载器加载的类。你需要使用 `-Xshare:off` 选项运行 `java` 命令来生成类列表。

以下命令会创建一个 `jdojoclasses.lst` 文件，其中包含加载到 JVM 中的所有类的列表。这是一个文本文件。你可以在你选择的文本编辑器中打开并编辑它。以下是用于 JDK10 的命令：

```
C:\Java13Revealed>java -Xshare:off -XX:+UseAppCDS -XX:DumpLoadedClassList=jdojoclasses.lst --class-path dist\jdojo.cds.jar com.jdojo.cds.HelloCDS
Hello, CDS and AppCDS
```

在 JDK11 及更高版本中，你无需在 `java` 命令中包含 `-XX:+UseAppCDS` 选项。你也可以使用模块路径通过以下命令生成类列表：

```
C:\Java13Revealed>java -Xshare:off -XX:DumpLoadedClassList=jdojoclasses.lst --module-path dist\jdojo.cds.jar --module jdojo.cds/com.jdojo.cds.HelloCDS
Hello, CDS and AppCDS
```

打开 `jdojoclasses.lst` 文件查看其中的类。你可以找到一行包含以下条目：`com/jdojo/cds/HelloCDS`。该条目表明，当你使用 `jdojoclasses.lst` 文件创建共享归档时，`HelloCDS` 类将成为共享归档的一部分。你将在下一节中使用此文件。

### 为 AppCDS 生成共享归档

让我们在 `java` 命令中使用 `-Xshare:dump` 和 `-XX:SharedArchiveFile` 选项，根据 `jdojoclasses.lst` 文件中的类列表创建一个共享归档。此步骤会创建一个共享归档，在你的机器上它将是一个 `jdojo.jsa` 文件。该命令会将信息性消息打印到标准输出（此处未显示）。以下是在 JDK10 中为 AppCDS 生成共享归档的命令：

```
C:\Java13Revealed>java -Xshare:dump -XX:+UseAppCDS -XX:SharedClassListFile=jdojoclasses.lst -XX:SharedArchiveFile=jdojo.jsa --class-path dist\jdojo.cds.jar
```

以下是在 JDK11 及更高版本中为 AppCDS 生成共享归档的命令：

```
C:\Java13Revealed>java -Xshare:dump -XX:SharedClassListFile=jdojoclasses.lst -XX:SharedArchiveFile=jdojo.jsa --module-path dist\jdojo.cds.jar --module jdojo.cds/com.jdojo.cds.HelloCDS
```

你将在下一节中使用这个共享归档（即 `jdojo.jsa` 文件）来运行 `HelloCDS` 类。

### 使用 AppCDS 运行应用程序

是时候看看你的 AppCDS 共享归档实际运行的效果了。现在当你运行 `HelloCDS` 类时，你需要指定要使用的 `jdojo.jsa` 共享归档。以下是用于 JDK10 的命令：

```
C:\Java13Revealed>java -Xshare:on -XX:+UseAppCDS -XX:SharedArchiveFile=jdojo.jsa --class-path dist\jdojo.cds.jar com.jdojo.cds.HelloCDS
Hello, CDS and AppCDS
```

以下是用于 JDK11 及更高版本的命令：

```
C:\Java13Revealed>java -Xshare:on -XX:SharedArchiveFile=jdojo.jsa --module-path dist\jdojo.cds.jar --module jdojo.cds/com.jdojo.cds.HelloCDS
Hello, CDS and AppCDS
```

自 JDK 11 起，`-Xshare:auto` 是默认选项，因此从上述命令中移除 `-Xshare:on` 也能正常工作。回想一下，`-Xshare:auto` 与 `-Xshare:on` 的工作方式相同，区别在于如果 CDS 无法使用，前者会禁用 CDS 并继续运行，而后者会导致应用程序启动失败。

注意上述命令中使用了以下三个选项。`-Xshare:on` 选项启用 CDS。`-XX:+UseAppCDS` 选项启用 AppCDS，表明你想要使用 AppCDS，这在 JDK11 或更高版本中是不需要的。`-XX:SharedArchiveFile=jdojo.jsa` 选项指定要使用的共享归档。输出中没有什么特别之处。启动时间更短，并且多个 JVM 在运行时也会共享应用程序类。

使用 `-Xlog:class+load=info` 选项来验证你的应用程序类 `HelloCDS` 是否从共享归档中加载。以下是用于 JDK10 的命令：

```
C:\Java13Revealed>java -Xshare:on -XX:+UseAppCDS -XX:SharedArchiveFile=jdojo.jsa -Xlog:class+load=info --class-path dist\jdojo.cds.jar com.jdojo.cds.HelloCDS
[0.011s][info][class,load] opened: C:\java10\lib\modules
[0.037s][info][class,load] java.lang.Object source: shared objects file
[0.038s][info][class,load] java.io.Serializable source: shared objects file
[0.038s][info][class,load] java.lang.String source: shared objects file
...
[0.103s][info][class,load] com.jdojo.cds.HelloCDS source: shared objects file
...
Hello, CDS and AppCDS
...
```

以下是用于 JDK11 及更高版本的命令：

```
C:\Java13Revealed>java -Xshare:on -XX:SharedArchiveFile=jdojo.jsa --module-path dist\jdojo.cds.jar -Xlog:class+load=info --module jdojo.cds/com.jdojo.cds.HelloCDS
[0.012s][info][class,load] opened: C:\java11\lib\modules
[0.042s][info][class,load] java.lang.Object source: shared objects file
[0.042s][info][class,load] java.io.Serializable source: shared objects file
[0.042s][info][class,load] java.lang.String source: shared objects file
...
[0.141s][info][class,load] com.jdojo.cds.HelloCDS source: shared objects file
...
Hello, CDS and AppCDS
...
```

注意，在输出中，`com.jdojo.cds.HelloCDS` 类的来源是“shared objects file”，这表明 `HelloCDS` 类是从共享归档中加载的。



### 类路径与模块路径规则

让我们在启用 AppCDS 并使用 JDK10 的两个不同类路径下，重新运行 `HelloCDS` 类：

```
C:\Java13Revealed>java -Xshare:on -XX:+UseAppCDS -XX:SharedArchiveFile=jdojo.jsa --class-path build\modules;dist\jdojo.cds.jar com.jdojo.cds.HelloCDS
处理共享归档文件时发生错误。
共享类路径不匹配（提示：启用 -Xlog:class+path=info 诊断失败原因）
初始化虚拟机时发生错误
无法使用共享归档。
C:\Java13Revealed>java -Xshare:on -XX:+UseAppCDS -XX:SharedArchiveFile=jdojo.jsa --class-path dist\jdojo.cds.jar;build\modules com.jdojo.cds.HelloCDS
Hello, CDS and AppCDS
```

回想一下，您之前通过指定类路径为 `dist\jdojo.cds.jar` 创建了 `jdojo.jsa` 共享归档。当您将类路径指定为 `build\modules;dist\jdojo.cds.jar` 时，收到了一个错误，提示共享类路径不匹配。

共享归档会记录其创建时使用的类路径。当您启用共享归档来运行应用程序时，必须满足以下条件之一，否则会报错：

*   启动 JVM 时使用的类路径与创建共享归档时使用的类路径相同。

*   启动 JVM 时使用的类路径包含创建共享归档时使用的类路径，并且后者作为前缀。

在此示例中，共享归档是使用类路径 `dist\jdojo.cds.jar` 创建的。当您将 `dist\jdojo.cds.jar` 作为类路径的唯一部分或第一部分使用时，应用程序可以正常运行。在前面的示例中，当您遇到错误时，您将 `dist\jdojo.cds.jar` 指定为了类路径的最后一部分。要查看出错时 JVM 正在查找的详细信息，请使用 `-Xlog:class+path=info` 选项运行命令，如下所示：

```
C:\Java13Revealed>java -Xshare:on -XX:+UseAppCDS -XX:SharedArchiveFile=jdojo.jsa -Xlog:class+path=info --class-path build\modules;dist\jdojo.cds.jar com.jdojo.cds.HelloCDS
[0.010s][info][class,path] 引导加载器类路径=D:\jdk-10\lib\modules
[0.012s][info][class,path] 已打开: C:\java10\lib\modules
[0.022s][info][class,path] 类型=BOOT
[0.023s][info][class,path] 期望的 BOOT 路径=C:\java10\lib\modules
[0.024s][info][class,path] 正常
[0.025s][info][class,path] 类型=APP
[0.026s][info][class,path] 期望的 -Djava.class.path=dist\jdojo.cds.jar
[0.027s][info][class,path]
[0.027s][info][class,path] [APP 类路径不匹配，实际: -Djava.class.path=build\modules;dist\jdojo.cds.jar
处理共享归档文件时发生错误。
共享类路径不匹配（提示：启用 -Xlog:class+path=info 诊断失败原因）
初始化虚拟机时发生错误
无法使用共享归档。
```

JDK11 为 AppCDS 共享归档增加了以下支持：

*   支持类路径和模块路径的任何有效组合。

*   模块路径中的非空目录会导致致命错误。换句话说，您只能在模块路径中包含模块化 JAR。

*   与类路径不同，没有要求转储时的模块路径必须等于或作为运行时模块路径的前缀。

*   如果在归档生成后更新了模块路径中的现有 JAR，则归档将失效。

*   从模块路径中移除 JAR 不会使共享归档失效。已移除 JAR 中的归档类在运行时不会被使用。

## 默认 CDS 归档

JDK12 为 64 位平台上的原生构建增加了对默认 CDS 归档的支持。JDK 映像包含一个默认的 CDS 归档。它被命名为 `classes.jsa`。在类 Unix 操作系统上，它保存在 `JAVA_HOME/lib/server` 目录中；在 Windows 上，保存在 `JAVA_HOME/bin/server` 目录中。`JAVA_HOME/lib/classlist` 文件包含了默认 CDS 归档中的系统类列表。您可以在文本编辑器中打开 `classlist` 文件，查看默认共享归档中包含哪些类。

要在 JDK12 及更高版本中利用默认 CDS，您无需执行任何额外操作。也就是说，您无需运行 `-Xshare:dump`，也无需使用 `-Xshare:auto` 或 `-Xshare:on` 来运行您的应用程序。`-Xshare:dump` 的工作在构建 JDK 映像时就已经完成了。当您运行应用程序时，CDS 归档会被自动使用，因为在 JDK 11 及更高版本中，`-Xshare:auto` 是服务器 VM 的默认设置。

让我们使用 `-Xlog:class+load=info` 选项在 JDK12 下运行 `HelloCDS` 类，这样输出可以显示每个类是从哪里加载的：

```
C:\Java13Revealed>java --module-path dist\jdojo.cds.jar -Xlog:class+load=info --module jdojo.cds/com.jdojo.cds.HelloCDS
[0.012s][info][class,load] 已打开: C:\java12\lib\modules
[0.023s][info][class,load] java.lang.Object 来源: 共享对象文件
[0.023s][info][class,load] java.io.Serializable 来源: 共享对象文件
[0.023s][info][class,load] java.lang.String 来源: 共享对象文件
...
[0.131s][info][class,load] com.jdojo.cds.HelloCDS 来源: file:/C:/Java13Revealed/dist/jdojo.cds.jar
...
Hello, CDS and AppCDS
...
```

输出显示，在您未指定任何额外选项的情况下，默认的 CDS 归档已被使用。系统类是从“共享对象文件”加载的，即默认的 CDS 归档。请注意 `HelloCDS` 类的位置，它是从 `jdojo.cds.jar` 文件加载的，而不是从共享归档加载的。这是因为默认 CDS 仅适用于系统类，不适用于应用程序类。如果您想从共享归档加载应用程序类，则需要使用上一节中描述的 AppCDS。JDK13 引入了一种使用动态 CDS 归档来使用 AppCDS 的新方法，我将在下一节中进行解释。



## 动态 CDS 归档

使用 CDS 和 AppCDS 可以显著缩短应用程序的启动时间，同时还能大幅减少运行时内存占用。JDK12 中引入的默认 CDS 归档使得 CDS 归档的使用变得透明。然而，在 JDK12 中使用 AppCDS 仍需执行三步流程：

*   创建类列表。
*   转储共享归档。
*   使用共享归档运行应用程序。

最终目标是让 AppCDS 的使用变得完全透明，用户无需任何操作即可利用共享归档。不过，截至 JDK13，我们尚未实现这一目标。JDK13 极大地简化了生成共享归档以使用 AppCDS 的方式，使其成为一个简单的两步流程：

*   使用 `-XX:ArchiveClassesAtExit=<文件名>` 参数运行应用程序，其中 `<文件名>` 是共享归档文件的名称。应用程序退出时会生成一个共享归档。如果应用程序异常退出，则不会生成共享归档。
*   使用 `-XX:SharedArchiveFile=<文件名>` 选项运行应用程序，以使用该共享归档进行 AppCDS。

让我们使用 JDK13 的以下命令为我们的 `HelloCDS` 应用程序创建一个共享归档。如果运行此命令时遇到错误，请参考以下文本进行故障排除：

```
C:\Java13Revealed>java -XX:ArchiveClassesAtExit=jdojo.jsa --module-path dist\jdojo.cds.jar --module jdojo.cds/com.jdojo.cds.HelloCDS
Hello, CDS and AppCDS
[0.137s][warning][cds] Skipping java/util/stream/FindOps$FindSink$OfRef$$Lambda$6: Unsafe anonymous class
...
```

在源代码中，我使用 `--enable-preview` 选项编译了所有类。如果你没有通过重新编译源代码（不使用 `--enable-preview` 选项）来重新创建 `dist\jdojo.cds.jar` 文件，你将收到以下错误信息：

```
Error: Unable to load main class com.jdojo.cds.HelloCDS in module jdojo.cds
java.lang.UnsupportedClassVersionError: Preview features are not enabled for com/jdojo/cds/HelloCDS (class file version 57.65535). Try running with '--enable-preview'
...
```

要修复此错误，你需要使用 `--enable-preview` 选项运行 `HelloCDS` 类，如下所示：

```
C:\Java13Revealed>java --enable-preview -XX:ArchiveClassesAtExit=jdojo.jsa --module-path dist\jdojo.cds.jar --module jdojo.cds/com.jdojo.cds.HelloCDS
```

此命令创建的动态 CDS 归档（顶层归档）是基于随 JDK 打包的默认 CDS 归档（基础层归档）创建的。顶层归档依赖于基础层归档。如果无法使用顶层归档，它将被禁用，仅使用基础层归档。如果无法使用基础层归档，则顶层归档也会被禁用。

顶层归档（或动态 CDS 归档）包含所有已加载的应用程序类和默认 CDS 归档（基础层归档）中不存在的库类。

### 提示

存在于类路径或模块路径上的 JAR 文件中，但在执行期间未被加载的类不会被动态归档。应用程序执行期间创建的 Java 堆对象也不会被动态归档。

让我们使用动态归档运行 `HelloCDS` 类。我使用了 `-Xlog:class+load=info` 选项，这样你可以在输出中看到 `HelloCDS` 类是从动态归档加载的。如果你使用的是本书源代码附带的 `dist\jdojo.cds.jar` 文件，请在以下命令中使用 `--enable-preview` 选项：

```
C:\Java13Revealed>java -XX:SharedArchiveFile=jdojo.jsa --module-path dist\jdojo.cds.jar -Xlog:class+load=info --module jdojo.cds/com.jdojo.cds.HelloCDS
[0.011s][info][class,load] opened: C:\java13\lib\modules
[0.023s][info][class,load] java.lang.Object source: shared objects file
[0.023s][info][class,load] java.io.Serializable source: shared objects file
[0.023s][info][class,load] java.lang.String source: shared objects file
...
[0.145s][info][class,load] com.jdojo.cds.HelloCDS source: shared objects file (top)
...
Hello, CDS and AppCDS
...
```

请注意输出中 `HelloCDS` 类的位置 `"shared objects file (top)"` 中使用了 `"(top)"`。这表明 `HelloCDS` 类是从顶层共享归档（即动态 CDS 归档）加载的。系统类的位置中不包含 `"top"` 字样，这意味着它们是从基础层（默认 CDS 归档）加载的。

敬请期待未来 JDK 版本中对动态 CDS 归档的更多改进。

## 总结

类数据共享（CDS）是 JDK5 中引入的一项 JVM 特性，它允许在 JVM 之间共享类，从而减少启动时间和运行时内存占用。CDS 允许共享由引导类加载器加载的 Java 核心类。

JDK10 中的应用程序类数据共享（AppCDS）扩展了 CDS 特性，允许在 JVM 之间共享类路径上的应用程序类。从 JDK10 开始，所有类型的类加载器（引导类加载器、平台类加载器、应用程序类加载器和自定义类加载器）都可以从 AppCDS 加载类。AppCDS 在 Oracle JDK 8 和 9 中可用，但需要商业许可。在 JDK10 中，AppCDS 在 OpenJDK 中可用。在 JDK10 中，你只能为类路径上的类创建 AppCDS 共享归档。JDK11 及更高版本允许包含来自模块路径和类路径的类。

当你为 AppCDS 使用共享归档时，用于创建共享归档的类路径必须与启用 AppCDS 启动应用程序时使用的类路径相同，或者是其前缀。否则，会发生类路径不匹配错误，应用程序将无法启动。

JDK12 为 64 位平台上的原生构建增加了对默认 CDS 归档的支持。JDK 镜像包含一个默认的 CDS 归档。它被命名为 `classes.jsa`。在类 Unix 操作系统上，它存储在 `JAVA_HOME/lib/server` 目录中；在 Windows 上，存储在 `JAVA_HOME/bin/server` 目录中。`JAVA_HOME/lib/classlist` 文件包含默认 CDS 归档中的系统类列表。要在 JDK12 及更高版本中利用默认 CDS，你无需执行任何操作。

JDK13 极大地简化了生成共享归档以使用 AppCDS 的方式。首先，使用 `-XX:ArchiveClassesAtExit=<文件名>` 参数运行应用程序，其中 `<文件名>` 是共享归档文件的名称。应用程序退出时会生成一个共享归档。如果应用程序异常退出，则不会生成共享归档。其次，使用 `-XX:SharedArchiveFile=<文件名>` 选项运行应用程序，以将共享归档与 AppCDS 一起使用。

动态 CDS 归档（顶层归档）是基于随 JDK 打包的默认 CDS 归档（基础层归档）创建的。顶层归档依赖于基础层归档。如果无法使用顶层归档，它将被禁用，仅使用基础层归档。如果无法使用基础层归档，则顶层归档也会被禁用。

顶层归档包含所有已加载的应用程序类和默认 CDS 归档中不存在的库类。存在于类路径或模块路径上的 JAR 文件中，但在执行期间未被加载的类不会被动态归档。应用程序执行期间创建的 Java 堆对象也不会被动态归档。



