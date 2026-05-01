# 使用 RoboVM 在 iOS 上运行 JavaFX

**RoboVM** 是连接 Java 和 Objective-C 的桥梁。使用它，开发要在 iOS 设备上运行的 JavaFX 8 应用程序变得很容易，因为 **RoboVM** 项目的最终目标是在不牺牲开发者体验或应用用户体验的情况下解决这个问题。

正如我们在上一章关于 Android 的内容中所看到的，使用 *JavaFXPorts* 生成 APK 是一项相对容易的任务，因为 Android 基于 Java 和 **Dalvik 虚拟机**。

相反，iOS 没有用于 Java 的虚拟机，并且不允许动态加载原生库。

因此需要另一种方法。RoboVM 开源项目试图通过使用*提前*编译器将 Java 字节码转换为原生 ARM 或 x86 机器码，从而在 Java 和 Objective-C 之间建立一座桥梁，为 Java 开发者弥合这一差距。

## 特性

让我们来了解一下 RoboVM 的特性：

*   将 Java 和其他 JVM 语言（如 Scala、Clojure 和 Groovy）引入 iOS 设备
*   提前将 Java 字节码转换为机器码，以便直接在 CPU 上快速执行，没有任何开销
*   主要目标是 iOS 和 ARM 处理器（32 位和 64 位），但也支持在 x86 CPU（32 位和 64 位）上运行的 Mac OS X 和 Linux
*   不对开发者可访问的 Java 平台功能施加任何限制，例如反射或文件 I/O
*   支持标准 JAR 文件，让开发者能够复用庞大的第三方 Java 库生态系统
*   通过 Java 到 Objective-C 的桥接提供对完整原生 iOS API 的访问，从而能够开发具有真正原生 UI 和完全硬件访问权限的应用
*   与最流行的工具集成，如 NetBeans、Eclipse、Intellij IDEA、Maven 和 Gradle
*   已为 App Store 做好准备，商店中已有数百个应用



## 局限性

主要由于 iOS 平台的限制，使用 RoboVM 时存在一些局限性：

*   不支持在运行时加载自定义字节码。构成应用的所有类文件必须在开发机器上编译时可用。
*   桌面或服务器上常用的 Java 原生接口技术通常从动态库加载原生代码，但 Apple 不允许在 iOS 应用中附带自定义动态库。RoboVM 支持一种基于静态库的 JNI 变体。
*   另一个重大局限是 RoboVM 是一个正在开发中的 alpha 阶段项目，尚不建议用于生产环境。

### 注意

RoboVM 完全支持反射。

## 工作原理

如第 4 章 *为 Android 开发 JavaFX 应用程序* 所述，自 2015 年 2 月起，RoboVM 和 JavaFXPorts 背后的公司达成了一项协议，现在一个名为 `jfxmobile-plugin` 的单一插件允许我们从同一代码库为三个平台（桌面、Android 和 iOS）构建应用程序。

JavaFXMobile 插件为你的 Java 应用添加了许多任务，使你能够创建可提交至 Apple Store 的 .ipa 包。

Android 主要使用 Java 作为主要开发语言，因此很容易将你的 JavaFX 8 代码与之合并。在 iOS 上，内部情况则完全不同——但使用了类似的 Gradle 命令。

该插件将下载并安装 RoboVM 编译器，并使用 RoboVM 编译器命令在 `build/javafxports/ios` 目录中创建 iOS 应用程序。

