# 里程碑

1.  封面
2.  目录
3.  正文

ISBN 978-1-4842-5369-4 e-ISBN 978-1-4842-5370-0 [`doi.org/10.1007/978-1-4842-5370-0`](https://doi.org/10.1007/978-1-4842-5370-0) © Ioannis Kostaras, Constantin Drabo, Josh Juneau, Sven Reimers, Mario Schröder, Geertjan Wielenga 2020 本作品受版权保护。出版商保留所有权利，无论是涉及材料的全部或部分，特别是翻译、重印、重用插图、朗诵、广播、以缩微胶片或任何其他物理方式复制、传输或信息存储与检索、电子改编、计算机软件，或采用目前已知或未来开发的类似或不同方法进行复制。本书中可能出现商标名称、标识和图像。我们仅在编辑风格中使用这些名称、标识和图像，以维护商标所有者的权益，无意侵犯商标权。本书中使用的商品名称、商标、服务标志及类似术语，即使未明确标识，也不应被视为对其是否受专有权利保护的立场表达。尽管本书中的建议和信息在出版时被认为是真实准确的，但作者、编辑和出版商均不对可能存在的任何错误或遗漏承担法律责任。出版商对本书内容不作任何明示或暗示的担保。本书通过 Springer Science+Business Media New York 在全球图书贸易中发行，地址：233 Spring Street, 6th Floor, New York, NY 10013。电话：1-800-SPRINGER，传真：(201) 348-4505，电子邮件：orders-ny@springer-sbm.com，或访问 www.springeronline.com。Apress Media, LLC 是一家加利福尼亚有限责任公司，其唯一成员（所有者）是 Springer Science + Business Media Finance Inc (SSBM Finance Inc)。SSBM Finance Inc 是一家特拉华州公司。

*谨以此书献给我们默默支持我们写作冒险的家人。具体而言：*

*Ioannis Kostaras：*

*献给我亲爱的妻子 Katerina；我可爱的幼子 Nikolaos-Ioannis；以及我的父母 Nikolaos 和 Zinovia*

*Constantin Drabo：*

*我将本书献给我亲爱的母亲、我的兄弟 Emmanuel 和妹妹 Denise、我的配偶 Mariam 以及我的幼子 Johann。最后，献给这些特别的人：Geertjan Wielenga 和 Malo Sadouanouan*

*Josh Juneau：*

*献给我的妻子 Angela；以及我的孩子们，Katie、Jake、Matt、Zach 和 Luke*

*Sven Reimers：*

*献给我挚爱的妻子 Isabelle*

*Mario Schröder：*

*献给我的妻子 Fridah*

*Geertjan Wielenga：*

*献给我的妻子 Hermine*

引言

*Pro Apache NetBeans* 详细概述了 NetBeans IDE 的最新功能，例如对 JShell、Jigsaw 模块系统和局部变量类型推断的支持，重点介绍了这个新版本为开发者带来的变化。它还描述了 NetBeans 平台（即构建 NetBeans IDE 的框架）的新特性。

本书是一本实用的操作指南，提供了许多循序渐进的教程，帮助您充分利用最新 Java（及其他）软件平台的功能，并为在项目中使用 NetBeans IDE 打下坚实基础。本书由 Apache 社区成员编写，他们一直使用该 IDE，并积极贡献和开发 Apache NetBeans 这一开源项目。

*Pro Apache NetBeans* 由三部分组成。第一部分描述了如何使用 IDE 以及它为支持最新 Java 版本带来的新特性。第二部分描述了如何通过创建插件或使用富客户端平台编写自己的独立应用程序来扩展 NetBeans。第三部分描述了如何为继续开发您最喜爱的 IDE 做出贡献，成为推动该工具集未来发展的开源团队的一员。

更详细地说，第 1 章“什么是 NetBeans”对 NetBeans 进行了简要介绍。它解释了 NetBeans 是什么、不是什么、它的历史，一直到它的新家——Apache 软件基金会，并成为其第二大项目。它还介绍了 NetBeans 支持的多种编程语言和工具，并向您介绍了本书其余部分将详细解释的许多其他内容。

第 2 章“NetBeans 入门”概述了 NetBeans IDE，将帮助新用户熟悉其用户界面、各种窗口，并向您介绍如何构建您的第一个 Java 项目。它还提供了有用的技巧和提示，帮助您提高工作效率。

第 3 章概述了 Apache NetBeans IDE 自 9.0 版本以来为 Java 9-12 版本及其他编程语言提供的新特性。它描述了对 JShell、Jigsaw 模块系统、HttpClient 和局部变量类型推断的支持，以及对 PHP 的改进。

第 4 章描述了如何开发桌面应用程序。这些是独立应用程序，通常本地运行在您的机器上，无需 Web 或应用服务器。本章解释了开发 Java GUI 桌面应用程序的三种方法：使用抽象窗口工具包 (AWT)、Swing 或 JavaFX。NetBeans 提供了一个非常强大的可视化编辑器，您可以通过将组件拖放到窗体（面板）上来构建 GUI。本章还指导您完成开发自己的桌面应用程序的步骤。

第 5 章描述了如何构建企业（或 Web）应用程序。与桌面应用程序相反，这些应用程序需要 Web 或应用服务器来提供 HTML 页面。自 11.0 版本起，Apache NetBeans 已包含对 Java EE 的支持。对 Maven Web 应用程序和 Java EE 技术（如 Enterprise JavaBeans (EJB)、上下文和依赖注入 (CDI) 以及 JavaServer Faces (JSF)）的全面支持，使开发者能够方便地使用自动完成、代码片段和轻松的语法识别。完成本章后，您将对 Apache NetBeans 为全栈 Java EE 开发提供的便利性有基本的了解。

第 6 章“调试和分析应用程序”详细解释了 Apache NetBeans 强大的调试器和分析器。如果调试能让您的代码正确，那么分析将使其运行得更快。本章解释了诸如断点、调试多线程代码、CPU 和内存分析、快照等内容。

本书的第二部分描述了 NetBeans 平台（或富客户端平台），即构建 NetBeans IDE 的框架或库。掌握这些知识，您可以更好地理解 NetBeans 源代码，从而修复错误，或通过编写自己的插件来扩展它。或者，您可以使用它来开发自己的桌面应用程序，比使用 Swing 更快。

第 7 章“掌握核心平台”向读者介绍了 NetBeans 平台的核心 API，即 NetBeans 模块系统、FileSystem 和 Lookups。NetBeans 提供了自己的模块系统来实现应用程序的模块化，并支持使用 OSGi。本章提供了与 Java 模块系统 Jigsaw 的比较。NetBeans 还提供了自己的库来访问文件系统。提供了与 Java 7 引入的 NIO.2 的比较。最后，Lookups 是构建松散耦合应用程序的非常强大的工具。还提供了与 Java 6 `ServiceLoader` 的比较。

第 8 章“掌握用户界面”向读者教授 NetBeans 平台的 GUI 组件，即窗口系统、动作系统、节点系统、资源管理器视图和属性表，所有这些都减少了开发强大 UI 所需的时间。

在第 9 章中，读者通过将一个桌面应用程序移植到 NetBeans RCP 来应用所学知识。开发分三步进行：(1) 构建用户界面的“静态”视觉原型；(2) 构建应用程序的“动态”原型，编写用户界面事件和相关的业务逻辑，并根据需要创建自定义 UI 组件；(3) 编写持久化逻辑。

第 10 章“学习平台的扩展功能”描述了 NetBeans 平台提供的许多其他 API，例如 Dialogs API、Visual Library 和 Palette、状态栏和通知、进度条、QuickSearch 和输出窗口等。

第 11 章教您如何通过为 NetBeans 编写插件来扩展它。您将逐步学习如何为 Hyperledger 开发一个插件，Hyperledger 是由 Linux 基金会托管的、旨在推进区块链技术的开源项目。

第三部分概述了 NetBeans 的新家——Apache 基金会；其支持开源项目的基础设施，例如用于问题跟踪、测试、文档等的各种工具；并为您提供了成为 Apache NetBeans 贡献者所需的所有信息。

第 12 章描述了 Apache NetBeans 的流程。在简要介绍历史之后，它解释了源代码如何从 Oracle 迁移到 Apache，作为孵化器项目在 Apache 下的各种 NetBeans 版本，以及如何毕业成为 Apache 顶级项目。

第 13 章描述了 Apache 基础设施以及 Apache 软件基金会为其项目提供的用于问题/错误报告、维基、网站等的各种工具。

第 14 章描述了 NetCAT 计划。***Net*** Beans IDE***C*** ommunity***A*** cceptance***T*** esting（NetBeans IDE 社区验收测试）计划对产品质量至关重要。一个典型的 NetCAT 计划大约需要两个月的活动。本章解释了每个 NetBeans 版本在发布前是如何由社区测试的。您可以轻松参与此计划并执行测试，从而帮助改进您最喜爱的 IDE。

第 15 章概述了 NetBeans 源代码、其架构、各种模块及其依赖关系。它描述了如何下载和构建 NetBeans 源代码，将其加载到 NetBeans IDE 中并进行调试。最后，本章鼓励您通过修复错误并将更改提交到基线来为 NetBeans 源代码做出贡献。

致谢

编写一本书就像踏上一场冒险。即使你制定了计划，你也不知道它会持续多久，风浪会有多大，以及你将探索到什么样的新大陆。就像奥德修斯一样，我们追寻我们的伊萨卡，道路并不总是一帆风顺——它充满了冒险，确实令人兴奋——但最终是值得的。

这本书是我们进入一个我们都热爱并继续使用的 IDE 的冒险。因此，首先，我们要感谢并致谢布拉格大学的这个小团队，他们提出了创建 NetBeans 的奇妙想法，感谢其不知疲倦的产品经理 Geertjan Wielenga，以及所有这些年来以各种方式做出贡献，使这个 IDE 成为我们所有人必需品的人们。

我们还要感谢 Apress 团队在一本多作者社区书籍中的耐心和支持，这本书的管理和协调并不总是那么容易，感谢他们坚持不懈的评论，不放过任何细节，并仔细审查草稿的每一句话。然而，可能仍然存在的任何错误均由作者负责。

最后但同样重要的是，我们要感谢我们的家人在我们冒险期间的包容，当我们花费许多小时在电脑屏幕前，尽力解释您将在本书中找到的众多 NetBeans 主题，以及编写能为读者带来更多价值的示例程序时。



### 关于作者与技术审校者

### 关于作者

### 关于技术审校者

