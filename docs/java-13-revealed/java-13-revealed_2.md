# 2. Java 版本命名方案

在本章中，你将学习

*   关于新旧 Java 发布模型
*   JDK 9 之前的 JDK 版本命名方案是什么
*   JDK 9 中的新 JDK 版本命名方案及其在 JDK 10 中的更新
*   如何使用 `Runtime.Version` 类解析 JDK 版本字符串
*   使用 `java` 命令的不同选项打印 Java 版本字符串时使用的格式



## Java 发布模型

在 2017 年 9 月发布的 Java 9 之前，Java 采用功能驱动的发布模型，每两年发布一个包含新功能的版本。在这种模型中，每个功能版本都包含一个或多个主要特性。有时，新功能版本（例如 Java 8 和 Java 9）会延迟数月甚至一年以上，因为主要特性的开发尚未完成。这种发布模型有一个主要缺点：小功能必须等待很长时间才能提供给开发者使用。

Java 已切换到一种新的基于时间的发布模型，该模型将包含：

*   每 6 个月发布一个功能版本
*   每 3 个月发布一个更新版本
*   每 3 年发布一个长期支持（LTS）版本

在新的基于时间的发布模型中，从 2018 年 3 月开始，你将每 6 个月获得一个新功能版本，例如 2018 年 3 月的 Java 10、2018 年 9 月的 Java 11、2019 年 3 月的 Java 12、2019 年 9 月的 Java 13，以此类推。一个功能版本可能包含对 Java 语言、JVM 或 API 的小型和/或大型新增内容。新功能的开发是一个持续的过程，在该功能完全开发完成之前，它不会与任何未来的功能版本绑定。一旦新功能完全开发完成，它就会被合并到即将发布的功能版本中。这样，新功能就不会延迟任何即将发布的版本。

从 2018 年 4 月开始，你将在 4 月、7 月、10 月和 1 月每 3 个月获得一个新的更新版本。更新版本将仅包含针对较新版本的安全修复和错误修复。

从 2018 年 9 月开始，你将获得一个 LTS 功能版本，该版本将通过更新获得至少 3 年的支持。

## 什么是版本字符串？

Java 的每个版本都由一个唯一的字符串标识，该字符串被称为*版本字符串*。例如，版本字符串 "1.8"、"9" 和 "10" 标识了三个不同的 Java 版本。Java 版本字符串之所以重要，有以下几个原因。如果你正在开发一个 Java 应用程序，你会希望根据 Java 支持的功能、你所在组织提供的支持以及你的供应商支持该 Java 版本的时间长短来选择一个 Java 版本。在 Java 9 之前，Java 使用的版本字符串类似于 "1.7"、"1.8.0_162" 等。Java 9 引入了一种新的版本字符串方案，而 Java 10 扩展了 Java 9 中引入的版本字符串方案。后续章节将为你概述所有版本字符串方案，以及如何在命令行中打印它们并在程序中使用它们。

## 旧版版本字符串方案

在 JDK 9 之前，版本字符串通常采用以下形式：

```
$n.$feature.$maintenance_$update-$identifier
```

其中：

*   `$n` 始终为 1。
*   `$feature` 是一个从 1 开始顺序递增的整数，表示包含新功能的功能版本。
*   `$maintenance` 是一个表示维护版本的整数，包含以工程为重点的错误修复。对于功能版本，它始终为零。
*   `$update` 是一个两位数的整数，如果需要，前面会补零。它表示以客户为中心的错误修复。`$update` 不是按顺序编号的。
*   `$identifier` 用于标识产品正式发布（GA）之前的里程碑，例如 "ea" 表示早期访问构建，beta 表示测试版，构建编号等。对于正式发布版本，标识符不是版本字符串的一部分。

以下是 JDK 9 之前的一些 JDK 版本字符串示例：

*   "1.8.0" 是一个正式发布的功能版本。
*   "1.8.0_162" 是功能版本 "1.8.0" 的一个更新版本（更新编号为 162）。
*   "1.8.0_162-b12" 是功能版本 "1.8.0" 的一个更新版本（更新编号为 162），构建标识符为 "b12"。

在 JDK 9 之前，JDK 版本命名方案对开发者来说不够直观，程序也不容易解析。查看两个 JDK 版本，你无法分辨它们之间的细微差别。很难回答一个简单的问题：哪个版本包含最新的所有安全修复，"1.7.0_55" 还是 "1.7.0_60"？答案并非你猜测的显而易见的那个——"1.7.0_60"。两个版本包含相同的安全修复。名为 *JDK 8 Update 66*、*1.8.0_66* 和 *JDK 8u66* 的版本之间有什么区别？它们代表同一个版本。在你能够说出版本字符串中包含的详细信息之前，有必要详细了解版本命名方案。例如，在 JDK 1.5.0 的初始版本之后，更新版本号中 20 的倍数表示与奇数更新号不同类型的更新。本节中的文本并未提供足够的信息让你完全理解 JDK 版本字符串方案。有关 JDK 9 之前 JDK 版本字符串方案的更多详细信息，请参考以下网页：

*   [`www.oracle.com/technetwork/java/javase/versioning-naming-139433.html`](http://www.oracle.com/technetwork/java/javase/versioning-naming-139433.html)
*   [`www.oracle.com/technetwork/java/javase/overview/jdk-version-number-scheme-1918258.html`](http://www.oracle.com/technetwork/java/javase/overview/jdk-version-number-scheme-1918258.html)

## 新版版本字符串方案

JDK 9 试图标准化 JDK 版本命名方案，使其易于人类理解、易于程序解析，并遵循行业标准的版本命名方案。

JDK 9 添加了一个名为 `Runtime.Version` 的静态嵌套类，它表示 Java SE 平台实现的版本字符串。该类可用于表示、解析、验证和比较版本字符串。

一个*版本字符串*按顺序包含以下四个元素。只有第一个元素是必需的：

*   版本号（`$vnum`）
*   预发布信息（`$pre`）
*   构建信息（`$build`）
*   附加信息（`$opt`）

以下正则表达式定义了版本字符串的格式：

```
$vnum(-$pre)?(\+($build)?(-$opt)?)?
```

一个*短版本字符串*由一个版本号组成，可选地后跟预发布信息：

```
$vnum(-$pre)?
```

你可以拥有像 `"9"` 和 `"10"` 这样仅包含功能版本号的短版本字符串，也可以拥有像 `"10.0.1.2-ea+40-20180201.07.36am"` 这样包含版本字符串所有部分的长版本字符串。我编造了这个长版本字符串，以向你展示版本字符串的所有元素。通常，预发布版本（版本字符串中包含 -ea）没有更新和补丁版本。后续章节将详细解释版本字符串的所有部分及其子部分。



### 版本号

版本号是由句点分隔的元素序列。其长度可以是任意的。格式如下：

```
^[1-9][0-9]*(((\.0)*\.[1-9][0-9]*)*)*$
```

版本号可由一至四个或更多元素组成，具体如下。第五个及后续元素没有特定含义；JDK 供应商可用它们来标识供应商特定的补丁。版本号的格式如下：

```
$feature.$interim.$update.$patch(.$addtionalInfo)
```

`$feature` 元素在 JDK 9 中被称为 `$major`。它是一个功能发布计数器，每发布一个功能版本就增加 1。功能版本代表 JDK 发布的主要版本，并包含新功能。例如，JDK 10 的功能版本是 10，JDK 11 的功能版本是 11。当功能编号增加时，版本号中的所有其他部分都会被移除。例如，如果你有一个版本号 10.0.2.1，当功能计数器从 10 增加到 11 时，新的版本号将是 11。采用 6 个月发布模式后，`$feature` 将从 2018 年 3 月开始，每年 3 月和 9 月增加 1。因此，2018 年 3 月的版本是 JDK 10，2018 年 9 月的版本是 JDK 11，2019 年 3 月的版本是 JDK 12，依此类推。

`$interim` 元素在 JDK 9 中被称为 `$minor`。它代表一个非功能版本，包含兼容的错误修复和增强。临时版本不会包含任何不兼容的更改、功能移除或对标准 API 的任何更改。`$interim` 的值（如果存在）始终为零，因为在 6 个月发布模式下不会有临时版本。未来，发布模式可能会改变，开始包含临时版本，届时将使用 `$interim`。

`$update` 元素在 JDK 9 中被称为 `$security`。对于修复安全漏洞、回归问题以及较新功能中错误的兼容性更新版本，该值会增加。

`$patch` 元素代表一个紧急补丁版本，包含针对关键问题的修复。

以下规则适用于版本号：

*   所有元素必须是无符号整数。

*   只有 `$feature` 是必需的。

*   版本号的元素不能包含前导零。例如，JDK 10 的第二个更新版本是 10.0.2，而不是 10.0.02。

*   尾部元素不能为零。也就是说，你不能有一个版本号如 10.0.0.0。它可以是 10、10.0.1、10.0.0.1 等。

*   当一个元素增加时，所有后续元素都会被移除。假设当前 JDK 版本是 10.0.1.4。如果更新计数器从 1 增加到 2，补丁计数器 4 将被重置为零，因此被移除，版本号变为 10.0.2。如果功能计数器增加到 11，所有其他计数器将被移除，版本号将从 10.0.1.4 变为 11。

*   两个版本号通过从左到右比较各自的计数器进行比较。例如，10.0.4.1 大于 10.0.3.5，但小于 10.0.4.2。

### 预发布信息

版本字符串中的 `$pre` 元素是一个预发布标识符，例如 `ea` 表示早期访问版本，`snapshot` 表示预发布快照，`internal` 表示开发者内部构建。它是可选的。如果存在，则以连字符（`-`）为前缀，并且必须是字母数字，匹配正则表达式 `([a-zA-Z0-9]+)`。版本字符串 "10-ea" 包含版本号 10 和预发布标识符 `ea`。

### 构建信息

版本字符串中的 `$build` 元素是一个构建编号，每次推广构建时递增。它是可选的。当版本号的任何部分递增时，它会被重置为 1。如果存在，则以加号（+）为前缀，并且必须匹配正则表达式 `(0|[1-9][0-9]*)`。版本字符串 "10-ea+40" 包含构建编号 40。

### 附加信息

版本字符串中的 `$opt` 元素包含额外的构建信息，例如内部构建的日期和时间。它是可选的。它是字母数字，可以包含连字符和句点。如果存在，则以连字符（`-`）为前缀，并且必须匹配正则表达式 `([-a-zA-Z0-9\.]+)`。如果 `$build` 不存在，你需要用加号后跟连字符（`+-`）作为 `$opt` 的前缀。例如，在 "10-ea+40-2018-02-19" 中，`$build` 是 40，`$opt` 是 "2018-02-19"；在 "9+-123" 中，`$pre` 和 `$build` 不存在，`$opt` 是 123。版本字符串 "10-ea+40-20180219.07.36am" 在其附加信息元素中嵌入了版本的日期和时间。

## 解析新旧版本字符串

在 JDK 9 之前，JDK 版本要么是包含新功能和非安全修复的有限更新版本，要么是仅包含安全漏洞修复的关键补丁更新。版本字符串包括版本号（包含更新编号和构建编号）。有限更新版本以 20 的倍数编号。关键补丁更新使用奇数，这些奇数是通过将先前有限更新版本加上 5 的倍数计算得出，并在需要时加 1 以保持结果为奇数。一个例子是 1.8.0_31-b13，这是 JDK 主版本 8 的更新 31。其构建编号是 13。请注意，在 JDK 9 之前，版本字符串总是以 1 开头。

你现有的用于解析版本字符串以获取 JDK 版本功能版本的代码，在 JDK 9 及更高版本中可能会失败，具体取决于其使用的逻辑。例如，如果逻辑通过跳过第一个元素（在 JDK 9 之前始终为 1）来查找第二个元素处的功能版本，则该逻辑将失败。例如，如果它从 1.8.0 返回 8，那么现在它将从 10.0.1.0 返回 0，而你可能期望的是 10。

## 系统属性的变更

在 JDK 9 中，包含 JDK 版本字符串的系统属性返回的值已更改。表 2-1 列出了这些系统属性及其值的格式。表中的 `$vstr`、`$vnum` 和 `$pre` 分别表示版本字符串、版本号和预发布信息。

表 2-1

JDK 9 中系统属性及其变更后的值

| 系统属性名称 | 值 |
| --- | --- |
| `java.version` | `$vnum(\-$pre)?` |
| `java.runtime.version` | `$vstr` |
| `java.vm.version` | `$vstr` |
| `java.specification.version` | `$vnum` |
| `java.vm.specification.version` | `$vnum` |

JDK 10 新增了两个系统属性：

*   `java.version.date`

*   `java.vendor.version`

`java.version.date` 系统属性包含当前 JDK 版本的 GA 日期，格式为 ISO-8601 `YYYY-MM-DD`。对于早期访问版本，这是该版本计划的 GA 日期。此系统属性可让你了解你的 JDK 有多旧。当你使用 `"java --version"` 和其他命令打印 JDK 版本时，也会打印其值。

`java.vendor.version` 系统属性是供应商特定的产品版本字符串。它是可选的，由 JDK 供应商分配。如果设置了该值，则必须匹配正则表达式 `\p{Graph}+`，该表达式允许一个或多个字母数字和标点符号。在开放 JDK 中，它被设置为 `$year.$month`，表示 JDK 版本的年份和月份。例如，在 JDK 10 中，它被设置为 18.3，表示 2018 年 3 月发布的 JDK。当你使用 `"java --version"` 和其他命令打印 JDK 版本时，也会打印其值。



## 使用 Runtime.Version 类

JDK 9 新增了一个名为 `Runtime.Version` 的静态嵌套类，其实例代表版本字符串。该类没有公共构造方法。你可以通过以下两种方式获取该类的实例：

*   使用 `Runtime` 类的静态 `version()` 方法
*   使用 `Runtime.Version` 类的静态 `parse(String vstr)` 方法

`Runtime` 类的静态 `version()` 方法返回一个 `Runtime.Version`，它代表 JRE 的版本。以下代码片段打印了当前 JRE 的版本，表明我的 JRE 是功能版本 13 的早期访问构建版本 29：

```
Runtime.Version version = Runtime.version();
System.out.println(version);
13-ea+29
```

你可以使用 `Runtime.Version` 类的 `parse()` 方法来解析版本字符串并获取一个 `Runtime.Version`。如果指定的字符串不是有效的版本字符串，`parse()` 方法会抛出 `IllegalArgumentException`：

```
// 解析版本字符串 "12.0.1+12"
Runtime.Version version = Runtime.Version.parse("12.0.1+12");
System.out.println(version);
12.0.1+12
```

`Runtime.Version` 类中的以下方法返回版本字符串的元素。方法名称直观，足以猜测它们返回的元素类型：

*   `int feature()`
*   `int interim()`
*   `int update()`
*   `int patch()`
*   `Optional<String> pre()`
*   `Optional<Integer> build()`
*   `Optional<String> optional()`

**提示** `Runtime.Version` 类中的 `major()`、`minor()` 和 `security()` 方法已在 JDK 10 中弃用。请改用该类的 `feature()`、`interim()` 和 `update()` 方法。

请注意，对于可选元素 `$pre`、`$build` 和 `$opt`，返回类型是 `Optional`。对于可选的 `$interim`、`$update` 和 `$patch` 元素，返回类型是 `int`，而不是 `Optional`，如果它们在版本字符串中不存在，则返回零。

版本字符串中的版本号在第四个元素之后可能包含附加信息。`Runtime.Version` 类不包含直接获取附加信息的方法。它包含一个 `version()` 方法，该方法返回一个 `List<Integer>`，其中列表包含版本号的所有元素。列表中的前四个元素依次是 `$feature`、`$interim`、`$update` 和 `$patch`。其余元素是附加的版本号信息。该列表至少包含一个元素，即功能版本号（`$feature`）。

`Runtime.Version` 类包含用于比较两个版本字符串的顺序和相等性的方法。你可以选择是否包含可选的构建信息（`$opt`）进行比较。这些比较方法如下：

*   `int compareTo(Runtime.Version v)`
*   `int compareToIgnoreOptional(Runtime.Version v)`
*   `boolean equals(Object v)`
*   `boolean equalsIgnoreOptional(Object v)`

表达式 `v1.compareTo(v2)` 将返回一个负整数、零或正整数，具体取决于 `v1` 是小于、等于还是大于 `v2`。`compareToIgnoreOptional()` 方法的工作方式与 `compareTo()` 方法相同，区别在于它在比较时会忽略可选的构建信息。`equals()` 和 `equalsIgnoreOptional()` 方法分别在有和没有可选构建信息的情况下比较两个版本字符串是否相等。

哪个版本字符串代表最新的构建版本，`10.0.1.1` 还是 `10.0.1.1-ea`？第一个不包含预发布元素，而第二个包含，因此第一个是最新的构建版本。哪个版本字符串代表最新的构建版本，`10.0.1.1` 还是 `10.0.1.2-ea`？这次，第二个代表最新的构建版本，因为版本号 `10.0.1.1` 被认为小于 `10.0.1.2`。当版本号更大时，版本字符串中的其他元素不再进行比较。以下是比较两个版本字符串的详细规则：

*   比较按以下顺序进行：`$vnum`、`$pre`、`$build` 和 `$opt`。
*   比较从版本号的每个元素开始。如果版本号中的任何部分缺失，则假定为零。从功能版本号开始，如果第一个版本字符串中的任何元素大于第二个版本字符串中的对应元素，则第一个版本字符串更大。例如，10.0.1.1 小于 11。
*   如果两个版本字符串的版本号相同，则比较预发布元素。带有预发布元素的版本字符串总是小于不带预发布元素的版本字符串。如果预发布元素仅由数字组成，则进行数值比较；否则，按字典顺序进行比较。数字预发布元素被认为小于非数字预发布元素。
*   没有构建号的版本总是小于有构建号的版本。如果两个版本字符串都有构建号，则按数值进行比较。
*   带有附加信息元素（`$opt`）的版本被认为大于不带 `$opt` 的版本。否则，按字典顺序比较 `$opt` 的值。

本章中的程序位于 `jdojo.version.scheme` 模块中，如清单 2-1 所示。

```
// module-info.java
module jdojo.version.scheme {
    exports com.jdojo.version.scheme;
}
清单 2-1
一个名为 jdojo.version.scheme 的模块
```

清单 2-2 包含一个完整的程序，演示如何使用 `Runtime.Version` 类提取版本字符串的所有部分。在打印当前 JRE 版本详细信息的第一个部分，你可能会得到不同的输出。



```
// VersionTest.java
package com.jdojo.version.scheme;
import java.lang.Runtime.Version;
import java.util.stream.Collectors;
import static java.lang.System.out;
public class VersionTest {
public static void main(String[] args) {
// 打印当前 JRE 的版本字符串详情
Version jreVersion = Runtime.version();
out.println("当前 JRE 版本: " + jreVersion);
printVersionDetails(jreVersion.toString());
// 准备一些版本字符串
String[] versionStrings = {
"10", "10.1", "10.0.1.2", "10.0.2.3.4", "10.0.0",
"10.1.2-ea+153", "10+132", "10-ea+24-2018-01-23", "10+-123",
"10.0.1-ea+132-2018-01-28.10.56.45am"};
// 解析每个版本字符串并显示其组成部分
for (String vstr : versionStrings) {
printVersionDetails(vstr);
}
}
public static void printVersionDetails(String vstr) {
try {
Version version = Version.parse(vstr);
// 获取版本号部分中从第 5 个元素开始的附加版本号元素
String vnumAdditionalInfo = version.version()
.stream()
.skip(4)
.map(n -> n.toString())
.collect(Collectors.joining("."));
out.printf("版本字符串=%s%n", vstr);
out.printf("主版本=%d, 次版本=%d, 更新=%d, 补丁=%d,"
+ " 附加信息=%s,"
+ " 预发布=%s, 构建=%s, 可选信息=%s %n%n",
version.feature(),
version.interim(),
version.update(),
version.patch(),
vnumAdditionalInfo,
version.pre().orElse(""),
version.build().isPresent()
? version.build().get().toString()
: "",
version.optional().orElse(""));
} catch (Exception e) {
out.printf("%s%n%n", e.getMessage());
}
}
}
当前 JRE 版本: 13-ea+29
版本字符串=13-ea+29
主版本=13, 次版本=0, 更新=0, 补丁=0, 附加信息=, 预发布=ea, 构建=29, 可选信息=
版本字符串=10
主版本=10, 次版本=0, 更新=0, 补丁=0, 附加信息=, 预发布=, 构建=, 可选信息=
版本字符串=10.1
主版本=10, 次版本=1, 更新=0, 补丁=0, 附加信息=, 预发布=, 构建=, 可选信息=
版本字符串=10.0.1.2
主版本=10, 次版本=0, 更新=1, 补丁=2, 附加信息=, 预发布=, 构建=, 可选信息=
版本字符串=10.0.2.3.4
主版本=10, 次版本=0, 更新=2, 补丁=3, 附加信息=4, 预发布=, 构建=, 可选信息=
无效的版本字符串: '10.0.0'
版本字符串=10.1.2-ea+153
主版本=10, 次版本=1, 更新=2, 补丁=0, 附加信息=, 预发布=ea, 构建=153, 可选信息=
版本字符串=10+132
主版本=10, 次版本=0, 更新=0, 补丁=0, 附加信息=, 预发布=, 构建=132, 可选信息=
版本字符串=10-ea+24-2018-01-23
主版本=10, 次版本=0, 更新=0, 补丁=0, 附加信息=, 预发布=ea, 构建=24, 可选信息=2018-01-23
版本字符串=10+-123
主版本=10, 次版本=0, 更新=0, 补丁=0, 附加信息=, 预发布=, 构建=, 可选信息=123
版本字符串=10.0.1-ea+132-2018-01-28.10.56.45am
主版本=10, 次版本=0, 更新=1, 补丁=0, 附加信息=, 预发布=ea, 构建=132, 可选信息=2018-01-28.10.56.45am
清单 2-2
一个演示如何使用 `Runtime.Version` 类处理版本字符串的 `VersionTest` 类
```

## 打印 Java 版本字符串

你可以使用表 2-2 中的某个选项配合 `java` 命令来打印 Java 产品版本字符串。输出结果会打印到标准输出或标准错误流。

表 2-2

用于打印产品版本的 java 命令选项

| 选项 | 描述 |
| --- | --- |
| `-version` | 将产品版本打印到错误流并退出。 |
| `--version` | 将产品版本打印到输出流并退出。 |
| `-showversion` | 将产品版本打印到错误流并继续执行。 |
| `--show-version` | 将产品版本打印到输出流并继续执行。 |
| `-fullversion` | 将产品版本打印到错误流并退出。 |
| `--full-version` | 将产品版本打印到输出流并退出。 |

这些选项在三个方面有所不同：输出格式、输出目标位置以及输出后的行为。我稍后会展示它们的输出格式。如果某个选项是“继续执行”，意味着它会将输出打印到目标位置，然后继续执行命令的其他部分（如果有的话）。例如，你可以使用 `-showversion` 和 `--show-version` 选项来打印产品版本并运行一个类：

```
java --show-version --module-path dist --module test/com.jdojo.Test
```

以下是每个选项的输出格式列表，其中 `\u0020` 代表空格字符的 Unicode 值。在格式说明符中：

*   诸如 `"openjdk version"` 的自由格式文本在 Open JDK 中会原样打印。当你使用 Oracle JDK 时，`"openjdk version"` 会被替换为 `"java version"`。

*   如果版本字符串中 `$opt` 部分的前三个字符是 `"LTS"`，则 `${LTS}` 会扩展为 `"\u0020LTS"`。

*   如果定义了 `java.vendor.version` 系统属性，则 `${JVV}` 会扩展为 `"\u0020${java.vendor.version}"`。

```
C:\> java -version
openjdk version \"${java.version}\" ${java.version.date}${LTS}
${java.runtime.name}${JVV} (build ${java.runtime.version})
${java.vm.name}${JVV} (build ${java.vm.version}, ${java.vm.info})
C:\> java --version
openjdk ${java.version} ${java.version.date}${LTS}
${java.runtime.name}${JVV} (build ${java.runtime.version})
${java.vm.name}${JVV} (build ${java.vm.version}, ${java.vm.info})
C:\> java -showversion 
openjdk version \"${java.version}\" ${java.version.date}${LTS}
${java.runtime.name}${JVV} (build ${java.runtime.version})
${java.vm.name}${JVV} (build ${java.vm.version}, ${java.vm.info})
[如果使用了其他选项，则输出其他选项的结果...]
C:\> java --show-version 
openjdk ${java.version} ${java.version.date}${LTS}
${java.runtime.name}${JVV} (build ${java.runtime.version})
${java.vm.name}${JVV} (build ${java.vm.version}, ${java.vm.info})
[如果使用了其他选项，则输出其他选项的结果...]
C:\> java -fullversion
openjdk full version \"${java.runtime.version}\"
C:\> java --full-version
openjdk ${java.runtime.version}
```

以下是在 Java 13 早期访问构建版本 29 中使用 `java` 命令的 `--version` 选项的输出。你可能会得到不同的输出，这取决于你使用的 JRE 版本。

```
C:\>java --version
openjdk 13-ea 2019-09-17
OpenJDK Runtime Environment (build 13-ea+29)
OpenJDK 64-Bit Server VM (build 13-ea+29, mixed mode, sharing)
```



## 摘要

Java 已采用一种新的基于时间的发布模式，即每 6 个月发布一个特性版本，每 3 个月发布一个更新版本，每 3 年发布一个长期支持（LTS）版本。新的特性版本将于每年 3 月和 9 月发布，从 2018 年 3 月的 Java 10 开始。更新版本将于每年 1 月、4 月、7 月和 10 月发布。LTS 版本将从 2018 年 9 月起每 3 年发布一次。

JDK 9 中改进了 JDK 不直观的版本编号方案。一个 JDK 版本字符串按顺序包含以下四个元素，其中只有第一个是必需的：版本号、预发布信息、构建信息和附加信息。正则表达式“`$vnum(-$pre)?(\+($build)?(-$opt)?)?"` 定义了版本字符串的格式。在 JDK 9 中，`$vnum` 由四部分组成：`$major.$minor.$security(.$additionalInfo)`。从 JDK 10 开始，`$vnum` 由五部分组成：`$feature.$interim.$update.$patch(.$addtionalInfo)`。JDK 10 中 `$vnum` 的 `$feature`、`$interim`、`$update` 元素在 JDK 9 中分别被称为 `$major`、`$minor` 和 `$security`。

短版本字符串仅包含前两个元素——一个版本号，可选地后跟预发布信息。你可以拥有像“10”这样仅包含主版本号的短版本字符串，也可以拥有像“10.0.1.1-ea+54-20180210.07.36am”这样包含所有元素的完整版本字符串。

JDK 9 添加了一个名为 `Runtime.Version` 的静态嵌套类，其实例代表 JDK 版本字符串。该类没有公共构造函数。你可以使用 `Runtime` 类的 `version()` 静态方法获取 JRE 的当前版本。该方法返回一个 `Runtime.Version`。你可以使用 `Runtime.Version` 类的 `parse(String vstr)` 静态方法来解析版本字符串，从而获得一个 `Runtime.Version`。如果版本字符串为 `null` 或无效，`parse()` 方法可能会抛出运行时异常。该类包含多个方法来获取版本字符串的不同部分。

JDK 10 新增了两个系统属性，名为 `java.version.date` 和 `java.vendor.version`。`java.version.date` 系统属性包含当前 JDK 版本的通用可用性（GA）日期，格式为 ISO-8601 `YYYY-MM-DD`。`java.vendor.version` 系统属性是供应商特定的产品版本字符串，它是可选的，由 JDK 供应商分配。

`Runtime.Version` 类包含多个方法来比较和检索版本字符串的不同部分。例如，你可以使用 `feature()` 方法获取版本字符串的 `$feature` 元素。`Runtime.Version` 类中的 `major()`、`minor()` 和 `security()` 方法已在 JDK 10 中弃用；请改用该类的 `feature()`、`interim()` 和 `update()` 方法。

