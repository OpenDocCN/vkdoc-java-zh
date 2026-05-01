# 第 6 章. 在 Raspberry Pi 上运行 JavaFX 应用程序

欢迎来到 **IoT**（**物联网**）世界。毫无疑问，你经常听到这个术语。物联网近来已成为热门话题，而且理由充分。一些估计显示，目前连接的小型设备数量约为 90 亿；预计到 2020 年将跃升至 240 亿。虽然预测数字各不相同，但评估结果是一致的：从绝对数量来看，物联网将使以往任何计算模型都相形见绌。

与物联网世界紧密相关的是 Raspberry Pi——一款由 Raspberry Pi 基金会设计的信用卡大小的微型计算机，用于实验和教育。

关于 Raspberry Pi，你需要知道的是，它不过是一台小型计算机。功耗小、体积小、内存小，最重要的是成本低。它的一切都很小，但它仍然是一台计算机，并且使用 Linux。

Java 从发明之初就是为物联网而生的。Java 的创建有着清晰的愿景：控制小型设备，如电视机顶盒。随着物联网的爆发，Java 回归了它的本源。

你可能会认为，作为富客户端开发平台的 JavaFX 会错过物联网的盛宴——但事实并非如此！根据 Oracle 技术网络上的 *JavaFX 概述* 页面：

> *“它旨在提供一个轻量级、硬件加速的 Java UI 平台”*

这句话揭示了图形丰富且强大的 JavaFX 的关键：硬件加速；幸运的是，Raspberry Pi 配备了强大的 GPU。

在本章中，我们将学习：

*   购买、准备和配置 Raspberry Pi
*   为 JavaFX 8 准备 Raspberry Pi
*   远程连接到 Raspberry Pi
*   在 Raspberry Pi 上安装和配置 Java SE 8
*   在 Raspberry Pi 上开发和运行 JavaFX 8 应用程序
*   将 NetBeans 与 Raspberry Pi 结合使用

兴奋吗？需要找点乐子！没错，让我们直接深入，与我们的 Raspberry Pi 一起玩耍吧。

### 注意

自 2015 年 1 月发布 JDK 8u33 ARM 版本以来，Oracle 已从 ARM 发行版中移除了 JavaFX Embedded。请参见 [`www.oracle.com/technetwork/java/javase/jdk-8u33-arm-relnotes-2406696.html#CACHGFJC`](http://www.oracle.com/technetwork/java/javase/jdk-8u33-arm-relnotes-2406696.html#CACHGFJC) 和 [`jaxenter.com/jdk-arm-without-javafx-end-javafx-embedded-114212.html`](http://jaxenter.com/jdk-arm-without-javafx-end-javafx-embedded-114212.html)。

JavaFX Embedded 的代码已交给开源项目 OpenJFX ([`wiki.openjdk.java.net/display/OpenJFX/Main`](https://wiki.openjdk.java.net/display/OpenJFX/Main))。建议正在寻找 JavaFX Embedded 替代方案的开发者积极参与该项目。

在本章中，我们将学习几种克服此问题的方法。

