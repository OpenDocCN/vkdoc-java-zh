# 为什么要把 JavaFX 移植到移动环境？

为什么要把 JavaFX 移植到移动环境？它难道不是**一次编写，随处运行**（**WORA**）吗？这是一个非常好的问题。任何 Java 应用程序都遵循 WORA 范式，但它依赖于一个非常关键的抽象软件来运行，这个软件被称为**Java 虚拟机**（**JVM**）。

JVM 负责将编译后的字节码（*.class 文件*）转换为特定机器能够理解和运行的平台指令，从而使你的应用程序得以运行。因此，你会看到针对不同硬件（Intel、AMD、SPARC 或 ARM）和平台（Windows、Mac、Linux 或 Solaris）的不同版本的 JRE 或**JDK**。

在桌面、Web 或嵌入式设备上，你必须先安装**Java 运行时环境**（**JRE**）才能运行 Java 应用程序。但对于移动设备，你会发现你只需从商店下载应用程序，安装它，然后直接运行，无需任何额外软件。此外，一些封闭平台不允许安装 JVM。

为了获得更好的最终用户体验，运行 JavaFX 应用程序与运行其他针对 Android 或 iOS 的应用程序之间不应有任何区别。

因此，我们应该拥有一个自包含（应用程序加 JVM）的 JavaFX 应用程序，它可以在移动设备上运行。除了能够与 Android 底层 API 交互以控制设备功能外，它在 Google Play 商店中也会与其他应用程序一样被对待。

我们应该感谢社区推出了这样的移植 SDK，填补了这一空白，使我们能够使用 RoboVM（[`www.robovm.org/`](http://www.robovm.org/)）的移植在 iOS 上创建和运行 JavaFX 应用程序，并使用 JavaFXPorts（[`javafxports.org/`](http://javafxports.org/)）的移植在 Android 上创建和运行 JavaFX 应用程序。

自 2015 年 2 月起，这些项目背后的公司之间达成了一项协议，现在一个名为`jfxmobile-plugin`的单一插件允许我们从同一个代码库为桌面、Android 和 iOS 三个平台构建应用程序。

此外，一家名为**Gluon**的新公司为**NetBeans**提供了一个免费插件（[`gluonhq.com/products/tools/ide-plugins/`](http://gluonhq.com/products/tools/ide-plugins/)），该插件可以创建一个包含基于`jfxmobile-plugin`构建应用程序所需一切的项目。

### 注意

但请记住，这一切都在不断发展，实际情况可能与本文所述有所不同。

## 工作原理

用于 iOS 移植的 RoboVM 和用于 Android 移植的 JavaFXPorts 都包含了所有必需的库，以便轻松地将你的 JavaFX 8 应用程序与所需的运行时环境打包在一起。

当使用 RoboVM for iOS 打包你的 JavaFX 应用程序（生成`.ipa`包文件）时，你所有的 JavaFX 应用程序都会被转换为**Objective-C**（目前是**Swift**）应用程序。

当使用 JavaFXPorts for Android 打包你的 JavaFX 应用程序（生成`.apk`包文件）时，应用程序会被转换为运行在**Dalvik**虚拟机之上的 Android 包。

这些 SDK 包含大量原生代码，这些代码在注入到你的 JavaFX 应用程序内部后，会被移植到 iOS 和 Android，以提高应用程序性能。

使用这些 SDK，我们可以将应用程序打包成适合提交到应用商店的格式（iOS 为`.ipa`，Android 为`.apk`）。

## 谁在维护它？

别担心——将 JavaFX 移植到 Android 和 iOS 有大规模的免费支持，也有商业支持。

### 注意

对于免费和商业支持，RoboVM 和 JavaFXPorts 社区都使用这个 Google 群组：

[`groups.google.com/forum/#!forum/javafxports`](https://groups.google.com/forum/#!forum/javafxports)

免费和商业支持主要来自社区中的人以及积极参与这两个项目的人。他们也鼓励更多的第三方参与进来。

对于 iOS，RoboVM 为开发者提供了不同的方案；你可以在[`robovm.com/pricing/`](http://robovm.com/pricing/)查看。

而对于 Android，**LodgON**公司提供 JavaFX-Android 集成的支持，作为其 JavaFX 移植支持的一部分（[`www.lodgon.com/dali/page/JavaFX_Consulting`](http://www.lodgon.com/dali/page/JavaFX_Consulting)）。



