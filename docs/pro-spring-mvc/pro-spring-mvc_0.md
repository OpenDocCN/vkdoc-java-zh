

![](img/Cover.jpg)

![封面](img/Cover.jpg)![标题](img/Title.jpg)

**精通 Spring MVC**

版权所有 © 2012，作者：Marten Deinum 与 Koen Serneels，以及 Colin Yates、Seth Ladd 和 Christophe Vanfleteren

本作品受版权保护。出版商保留所有权利，无论是涉及材料的全部或部分，特别是翻译、重印、重用插图、朗诵、广播、微缩胶片复制或任何其他物理方式的复制权，以及传输或信息存储与检索、电子改编、计算机软件，或现在已知或以后开发的类似或不同方法的权利。此法律保留的例外情况包括：与评论或学术分析相关的简短摘录，或专门为输入计算机系统并执行而提供的材料，仅供作品购买者独家使用。仅可根据出版商所在地现行版权法的规定复制本出版物或其部分内容，且必须始终从 Springer 获得使用许可。使用许可可通过 Copyright Clearance Center 的 RightsLink 获取。违反者将根据相应的版权法被起诉。

国际标准书号-13（平装）：978-1-4302-4155-3

国际标准书号-13（电子版）：978-1-4302-4156-0

本书中可能出现商标名称、标识和图像。我们并非在每次出现商标名称、标识或图像时都使用商标符号，而是仅以编辑方式使用这些名称、标识和图像，以利于商标所有者，且无意侵犯商标权。

本出版物中使用的商品名称、商标、服务标志及类似术语，即使未明确标识，也不应被视为对其是否受专有权利保护的表达意见。

尽管本书中的建议和信息在出版时被认为是真实准确的，但作者、编辑和出版商均不对可能存在的任何错误或遗漏承担法律责任。出版商对本书所包含的材料不作任何明示或暗示的保证。

总裁与出版人：Paul Manning
首席编辑：Steve Anglin
技术审阅人：Manuel Jordan
编辑委员会：Steve Anglin, Ewan Buckingham, Gary Cornell, Louise Corrigan, Morgan Ertel, Jonathan Gennick, Jonathan Hassell, Robert Hutchinson, Michelle Lowman, James Markham, Matthew Moodie, Jeff Olson, Jeffrey Pepper, Douglas Pundick, Ben Renow-Clarke, Dominic Shakeshaft, Gwenan Spearing, Matt Wade, Tom Welsh
协调编辑：Jennifer L. Blackwell 和 Stephen Moles
文字编辑：Patrick Meader 和 James Compton
排版：Bytheway Publishing Services
索引编制：SPi Global
插图：SPi Global
封面设计：Anna Ishchenko

本书通过 Springer Science+Business Media New York 在全球图书贸易中发行，地址：233 Spring Street, 6th Floor, New York, NY 10013。电话：1-800-SPRINGER，传真：(201) 348-4505，电子邮件：`orders-ny@springer-sbm.com`，或访问 [`www.springeronline.com`](http://www.springeronline.com)。

有关翻译信息，请发送电子邮件至 `rights@apress.com`，或访问 [`www.apress.com`](http://www.apress.com)。

Apress 和 friends of ED 的书籍可批量购买用于学术、企业或促销用途。大多数图书也提供电子书版本和许可证。有关更多信息，请参考我们的特别批量销售–电子书许可网页：[`www.apress.com/bulk-sales`](http://www.apress.com/bulk-sales)。

作者在本文中引用的任何源代码或其他补充材料，读者可在 [`www.apress.com`](http://www.apress.com) 获取。有关如何找到本书源代码的详细信息，请访问 [`www.apress.com/source-code`](http://www.apress.com/source-code)。

*献给我的妻子，感谢你的爱与无尽的支持。*

*–Marten Deinum*

*献给 Sonja Korte，我生命中的挚爱，感谢你的耐心与支持。你几乎让我相信了咖啡杯会自动续杯。献给爸爸妈妈，感谢你们一直在我身边，并为我买了一台 80386 而不是那台 8 位的 NES。*

*–Koen Serneels*

## 目录概览

![图片](img/square.jpg) 前言

![图片](img/square.jpg) 关于作者

![图片](img/square.jpg) 关于技术审阅人

![图片](img/square.jpg) 致谢

![图片](img/square.jpg) 引言

![图片](img/square.jpg) 第 1 章：配置 Spring 开发环境

![图片](img/square.jpg) 第 2 章：Spring 框架基础

![图片](img/square.jpg) 第 3 章：Web 应用程序架构

![图片](img/square.jpg) 第 4 章：Spring MVC 架构

![图片](img/square.jpg) 第 5 章：实现控制器

![图片](img/square.jpg) 第 6 章：实现控制器——进阶

![图片](img/square.jpg) 第 7 章：REST 与 AJAX

![图片](img/square.jpg) 第 8 章：解析与实现视图

![图片](img/square.jpg) 第 9 章：测试 Spring MVC 应用程序

![图片](img/square.jpg) 第 10 章：Spring Web Flow

![图片](img/square.jpg) 第 11 章：使用 Spring Web Flow 构建应用程序

![图片](img/square.jpg) 第 12 章：高级 Spring Web Flow

![图片](img/square.jpg) 第 13 章：Spring Security

![图片](img/square.jpg) 附录 A：Cloud Foundry：部署到云端

![图片](img/square.jpg) 索引



## 目录

![图片](img/square.jpg) 前言

![图片](img/square.jpg) 关于作者

![图片](img/square.jpg) 关于技术审校者

![图片](img/square.jpg) 致谢

![图片](img/square.jpg) 引言

![图片](img/square.jpg) 第 1 章：配置 Spring 开发环境

前提条件

Java 开发工具包

Servlet 容器

集成开发环境

示例应用程序

书店示例应用程序

构建系统

构建示例应用程序

部署示例应用程序

SpringSource Tool Suite (STS)

为 Gradle 项目配置 STS

将示例导入 STS

在 SpringSource vFabric tc Server 上运行应用程序

编辑应用程序

小结

![图片](img/square.jpg) 第 2 章：Spring 框架基础

Spring 框架

依赖注入

ApplicationContext

资源加载

组件扫描

作用域

配置文件

启用特性

面向切面编程

Web 应用程序

小结

![图片](img/square.jpg) 第 3 章：Web 应用程序架构

MVC 模式

应用分层

关注点分离

Spring MVC 应用层

领域层

用户界面层

Web 层

服务层

数据访问层

条条大路通罗马

小结

![图片](img/square.jpg) 第 4 章：Spring MVC 架构

DispatcherServlet 请求处理工作流

工作流

请求处理总结

DispatcherServlet

引导 DispatcherServlet

配置 DispatcherServlet

Spring MVC 组件

HandlerMapping

HandlerAdapter

MultipartResolver

LocaleResolver

ThemeResolver

HandlerExceptionResolver

RequestToViewNameTranslator

ViewResolver

FlashMapManager

小结

![图片](img/square.jpg) 第 5 章：实现控制器

控制器简介

基于接口的控制器

基于注解的控制器

配置视图控制器

请求处理方法

支持的方法参数类型

支持的方法参数注解

支持的方法返回值

编写基于注解的控制器

一个简单的登录控制器

图书搜索页面



书籍详情页

数据绑定

自定义数据绑定

按控制器自定义

模型属性

类型转换

验证模型属性

国际化

消息源

区域解析器

区域变更拦截器

总结

![图片](img/square.jpg) 第 6 章：实现控制器——进阶

使用作用域 Bean

向购物车添加商品

实现结账功能

横切关注点

拦截器

异常处理

简单映射异常解析器

扩展 Spring @MVC

扩展 RequestMappingHandlerMapping

扩展 RequestMappingHandlerAdapter

使用 RequestDataValueProcessor

总结

![图片](img/square.jpg) 第 7 章：REST 与 AJAX

表述性状态转移（REST）

识别资源

处理资源

异步 JavaScript 与 XML（AJAX）

为我们的应用添加 AJAX

结合 AJAX 与 REST

渐进增强

处理文件上传

配置

文件上传的请求处理方法

异常处理

总结

![图片](img/square.jpg) 第 8 章：解析与实现视图

视图解析器与视图

视图解析器

Bean 名称视图解析器

XML 视图解析器

资源包视图解析器

基于 URL 的视图解析器

内部资源视图解析器

XSLT 视图解析器

内容协商视图解析器

实现自定义视图解析器

视图技术

Java 服务器页面

JavaServer Faces

Tiles

Velocity 与 FreeMarker

PDF

Excel

XML 与 JSON

JasperReports

总结

![图片](img/square.jpg) 第 9 章：测试 Spring MVC 应用

测试简介

为什么我要费心编写测试？

在项目中推广测试

不同类型的测试

设置基本单元测试

测试代码覆盖率

使用 Spring 的测试支持

设置集成测试

测试 JpaBookRepository

使用模拟对象

什么是模拟对象？

测试 AccountService

测试 MVC 逻辑

使用 Spring 模拟对象

Spring MVC 测试简介

自动化前端测试

使用 Selenium 进行前端测试

编写 Selenium 测试



使用 Selenium IDE

通过 Gradle 运行前端测试

总结

![图片](img/square.jpg) 第 10 章：Spring Web Flow

为何使用 Web Flow

流程概念

细粒度作用域

自动状态管理

请求同步

Post Redirect Get (PRG)

受控导航

何时避免使用 Web Flow

流程的基本要素

流程

视图状态

状态转换

评估动作

表达式

配置

依赖项

Web Flow 配置

整合 Spring MVC 与 Spring Web Flow

构建你的第一个流程

创建首页

实现创建订单流程

概述

总结

![图片](img/square.jpg) 第 11 章：使用 Spring Web Flow 构建应用程序

重要的 Web Flow 概念

流程定义

不同的 Web Flow 作用域

隐式对象

增强书店应用

选择类别

选择图书与配送选项

使用 JSR 303 注解进行表单验证

设置变量与访问作用域

流程变量

从视图访问作用域变量

以编程方式访问作用域

控制动作执行

<on-start>

<on-end>

<on-entry>

<on-exit>

<on-render>

控制动作执行：子元素

全局转换

子流程

进一步增强书店应用

将认证实现为子流程

决策状态

动作状态

处理结果事件

概述

子流程输入/输出映射

将订单流程创建为子流程

结束状态

总结

![图片](img/square.jpg) 第 12 章：高级 Spring Web Flow

继承

流程继承

状态继承

Web Flow 配置自定义

执行与对话快照

更改表达式解析器

Web Flow 1 迁移

异常处理

On Exception 转换

自定义异常处理器

显式表单绑定

Web Flow AJAX 支持

为 AJAX 配置 Web Flow

准备视图

调整流程

使用 Spring JS 和 JQuery 为视图添加 AJAX

流程执行监听器

编写流程执行监听器

流程执行监听器方法

流程管理的持久化上下文

从数据库到视图

延长持久化上下文

应用流程管理的持久化上下文

重构订单概览

总结

![图片](img/square.jpg) 第 13 章：Spring Security

安全简介

什么是应用程序安全？

通用安全原则

我们将涵盖的内容

准备示例应用程序

保护我们的书店

添加正确的依赖项

启用 Spring Security

定义要保护的资源

配置对资源的访问

配置认证

整合所有配置

完整的安全配置

连接到数据库

以正确的方式保护我们的流程

为流程添加访问属性

配置 SecurityFlowExecutionListener

传输安全

本地化

基于角色的访问控制

授权访问

在页面中使用标签库

在代码中使用注解

总结

![图片](img/square.jpg) 附录 A：Cloud Foundry：部署到云端

云计算

Cloud Foundry

部署我们的应用程序

安装 Cloud Foundry 插件

进行一些调整

部署

配置服务

它是如何工作的？

其他配置选项

本地部署

使用 Cloud Foundry 进行调试

总结

![图片](img/square.jpg) 索引



## 前言

我至今仍记得 2004 年初次接触 Spring 框架时的情景。当时我大量使用 J2EE 和 Struts，在构建 Java 企业应用时，这些技术的有效运用让我遇到了诸多困难。Spring 并没有试图完全摒弃 J2EE，而是通过提供大量最佳实践实现，以及一个将所有组件粘合在一起的控制反转容器，极大地简化并提升了 J2EE 的使用效率。这确实是一个令人振奋的方案！

虽然 Spring 通常不会试图通过提供与 J2EE 现有组件或其他成熟框架（尤其是 Hibernate）竞争的解决方案来重新发明轮子，但有一个重要的例外：Web 应用程序开发。Spring 自带了自己的 Web 框架——Spring MVC，这是一个功能完备的 Web 应用框架，可以直接作为 Struts 等框架的替代方案。当时，我看到了通过为 Spring MVC 添加一个名为 Spring Web Flow 的页面流组件来增强基于 Spring 的 Web 应用开发的进一步机会。Spring MVC 和 Spring Web Flow 正是本书的核心主题。

Spring MVC 的设计受益于从早期框架中汲取的经验教训。灵活性和长期生产力是其核心设计目标。设计良好的 Spring MVC 应用能够成长和变化，同时保持可管理和可维护性。然而，并非一切尽善尽美。许多人发现采用 Spring，特别是 Spring MVC 的初始学习曲线过于陡峭。Ruby on Rails 等新框架应运而生，它们专注于短期生产力，让开发者能够非常轻松地快速上手并运行。Spring 开发者社区认识到了这一不足，而 Spring MVC 最近的 3.0 和 3.1 版本在很大程度上解决了这个问题：正如本书将展示的那样，大量使用约定优于配置和注解使得该框架比以往任何时候都更容易使用！这一切在保持 100%向后兼容性的同时得以实现，这充分证明了 Spring MVC 的灵活性和设计质量。您现在拥有一个两全其美的框架：既易于上手，其支撑的底层原则又能带来长期回报。Spring Web Flow 目前也正在经历类似的演变。

自 2004 年以来，Spring MVC 和 Spring Web Flow 的受欢迎程度稳步持续增长。它们现在已是成熟且完善的 Java 开发框架。如果您在这些框架之上构建 Java Web 应用程序，可以放心，您正在一个非常坚实的基础上进行构建！

在本书中，Marten 和 Koen 采用了一种实践性的方法来介绍 Spring MVC 和 Spring Web Flow。当然，他们会帮助您搭建高效的生产开发环境，并指导您开始使用 Spring MVC 和 Web Flow 进行开发。但他们并未止步于此。我由衷赞赏他们不仅涵盖了技术细节，还花时间解释了许多底层概念，从而为您（读者）带来更深入的理解。此外，本书还涵盖了其他重要主题，例如实施安全约束以及确保您的 Web 应用程序经过充分测试。阅读完本书后，您将能够很好地基于 Spring 技术开发真实的 Web 应用程序。

我很高兴看到，时至今日，人们仍在受益于 Spring 以及我在 2004 年为其添加的那个小组件。我向所有渴望学习 Spring MVC 和 Spring Web Flow 的人强烈推荐这本书，并赞扬作者们在帮助开发者学习这些激动人心的 Spring 技术方面所做的出色工作。

Erwin Vervaet

Spring Web Flow 项目创始人

## 关于作者

![图片](img/marten_deinum.jpg) ![图片](img/square.jpg) **Marten Deinum** 是 Conspect 公司的一名 Java/软件顾问。他为大小公司开发并设计了软件架构，主要使用 Java。他是一位热心的开源软件用户，也是 Spring 框架的长期粉丝、用户和倡导者。他曾担任过软件工程师、开发主管、教练以及 Java 和 Spring 培训师等职位。在不工作或不在 Spring 框架论坛上回答问题的时候，他会在水中为铁人三项训练，或者在水下潜水或为他人做向导。

![图片](img/koen_serneels.jpg) ![图片](img/square.jpg) **Koen Serneels** 是一名高级 JAVA 软件工程师，拥有多项 IBM、Cisco 和 Oracle 认证。十多年来，他使用 Java(EE)、Spring、Spring MVC、Web Flow、JSF 和 Hibernate 开发企业解决方案。他对系统架构与集成、数据建模、关系数据库、安全和网络有着浓厚的兴趣。Koen 的职业生涯始于比利时联邦政府，他开发了具有遗留系统集成功能的高事务性 Java 应用程序。目前，他受雇于惠普公司，担任比利时弗拉芒政府的 Java 软件顾问和技术负责人。他还在比利时鲁汶的 Groep T 工程学院教授一门名为“使用 Java 进行软件开发”的研究生课程。

![图片](img/square.jpg) **Colin Yates** 是一位专攻基于 Web 开发的 J2EE 首席架构师。过去三年，他一直是自由顾问，曾在多种（既有结构化的也有混乱的）环境中工作。自 1997 年获得软件工程学位以来，他担任过多个职位，包括开发主管、首席系统工程师、导师和专业培训师。他的主要技能包括指导他人、将复杂问题架构为可管理的解决方案，以及优化开发流程。

Colin 于 2003 年 1 月由他的导师 Peter Den Haan 和 David Hewitt 首次引入 Spring 框架，此后便一直专注于该领域。

![图片](img/square.jpg) **Seth Ladd** 是一名软件工程师和专业 Spring 框架培训师及导师，专精于面向对象和可测试的 Web 应用程序。他 17 岁时创办了自己的公司来构建网站，但现在更喜欢拥有一份正式的工作。Seth 目前受雇于 Camber Corporation，曾为 NEC、罗切斯特理工学院、Brivo Systems 和国家信息联盟构建并部署系统。他使用 Java 和 C 语言为服务器和远程连接的嵌入式设备设计并开发了企业应用程序。他乐于演讲和教学，经常在本地 Java 用户组和企业开发者大会上担任演讲者。Seth 非常感激能与妻子一起在夏威夷凯鲁瓦生活和工作。

![图片](img/square.jpg) **Christophe Vanfleteren** 是一名软件工程师，自 2001 年以来一直从事 Java 技术工作。在 EDS / HP 工作期间，他主要参与比利时弗拉芒政府的项目，包括一些大规模的事件驱动型应用，例如跟踪 2006 年弗拉芒选举的系统，以及向 600 万人征收多种不同税种的弗拉芒税务平台。他能够处理技术栈的各个层面，从高事务性代码到基于 JavaScript 的前端。自 2010 年起，Christophe 成为一名独立承包商。



## 关于技术审校者

![图片](img/manuel_ordan_elera.jpg) ![图片](img/square.jpg) **曼努埃尔·乔丹·埃莱拉**是一位自学成才的开发者与研究员，他热衷于学习新技术用于个人实验，并创造新的集成方案。

曼努埃尔曾荣获 2010 年 Springy 奖——社区冠军。在有限的闲暇时间里，他阅读《圣经》并用吉他创作音乐。曼努埃尔是 Spring 社区论坛的高级成员，在论坛中他以`dr_pompeii`之名广为人知。

曼努埃尔担任以下书籍的技术审校者（均由 Apress 出版）：

*《Pro SpringSource dm Server》，作者：Gary Mak、Daniel Rubio（2009 年）*
*《Spring Enterprise Recipes》，作者：Gary Mak、Josh Long（2009 年）*
*《Spring Recipes》，作者：Gary Mak、Daniel Rubio、Josh Long（第二版，2010 年）*
*《Pro Spring Integration》，作者：Mark Liu 博士、Mario Gray、Andy Chan、Josh Long（2011 年）*
*《Pro Spring Batch》，作者：Michael T. Minella（2011 年）*
*《Pro Spring 3》，作者：Clarence Ho、Rob Harrop（2012 年）*

您可以通过他的博客[`http://manueljordan.wordpress.com/`](http://manueljordan.wordpress.com/)阅读并联系他，也可以在 Twitter 上关注他，账号为`@dr_pompeii`。

## 引言

欢迎阅读《Pro Spring MVC with Web Flow》第一版；这是第一本完全专注于使用 Spring Framework 3.1 生态系统进行 Web 开发的 Pro Spring 系列书籍。

### 本书将教会你什么

本书将教你使用 Spring Framework 3.1 版本构建企业级 Web 应用程序所需的一切知识。主题包括但不限于：

*   Spring MVC 组件的构建模块
*   配置开发环境
*   为基于 Spring 的应用程序提供 Web 前端
*   测试 Web 前端的实用方法
*   部署到本地 Web 服务器和远程云端部署平台
*   Spring Web Flow 简介
*   如何使用 Spring Web Flow 构建应用程序

阅读本书后，你将熟悉 Spring MVC 工具包，并能够从零开始构建自己的 Web 应用程序，或为现有应用程序提供新的 Web 界面。

通常，框架的强大功能往往通过简单的示例来展示；但只有在实际场景中使用框架时，其局限性才会显现。本书旨在展示 Spring MVC 如何解决 Web 开发者面临的棘手问题（以及简单问题！），例如在多页面复杂用例中管理状态，或为不同用户提供同一资源的多种视图。

由于本书探讨的实际问题颇具挑战性，有时 Spring MVC 提供的解决方案可能并不如人们期望的那样简单。本书不会回避这些问题，也不会吝于提供如何正确行事的实用建议。

### 本书适合谁阅读？

本书适合熟悉 Spring 并希望深入理解 Spring MVC 的读者。虽然主要面向 Spring MVC 新手，但其中包含的信息甚至会让经验丰富的 MVC 专家感到惊喜！

典型的读者将是那些对核心 Spring 框架有一定了解（例如阅读过《Pro Spring》），并希望更详细研究 Spring MVC 的 Web 开发者。

如果你不熟悉 Spring，也完全可以继续阅读；特别是第 2 章。但如果你觉得内容不够充分，可以参考 Spring 参考指南^(1)或《Pro Spring 3》（Apress，2012 年）。

Spring 的奠基之作《Expert One-on-One J2EE Design and Development》（Wrox，2002 年）^(2)，由 Rod Johnson（Spring 框架的“之父”）撰写，虽然年代久远，但依然充满智慧且具有参考价值。

### 如何阅读本书

本书将引导读者经历设计、实现和部署 Java Web 应用程序的完整思维过程。章节顺序遵循开发生命周期的时序。在章节中，我们将使用一个示例应用程序来阐述所讨论的主题。

每一章都会通过引入一个新概念或新功能来解决一个实际问题，并以此升级示例应用程序。

![图片](img/square.jpg) **注意** 作者们始终面临一个两难困境：是先展示答案再提出问题，还是先提出问题再展示答案？第一种方法可能让读者信息过载，而第二种方法对于已经熟悉主题的读者来说可能显得拖沓且缓慢。作者们相信，本书的节奏对于不熟悉 Spring MVC 的读者来说是合适的；更有经验的读者可以跳过某些章节。例如，如果你已经配置好开发环境并熟悉 Spring，可以快速浏览第 1 章获取示例应用程序，然后直接跳到第 3 章。

### 示例应用程序

虽然示例无法成为真正的企业级应用程序（因为我们需要将本书控制在 1 万页以内；）），但它足够真实，能够突出实际项目中面临的典型设计挑战。

示例是一个支持 Web 功能的书店网站。它为用户提供以下功能：

*   匿名用户可以浏览网站。
*   登录用户可以提交一本或多本书的订单。

__________

¹ [`http://static.springsource.org/spring/docs/current/spring-framework-reference/html/`](http://static.springsource.org/spring/docs/current/spring-framework-reference/html/)

² [`http://www.wrox.com/WileyCDA/WroxTitle/productCd-0764543857.html`](http://www.wrox.com/WileyCDA/WroxTitle/productCd-0764543857.html)

*   登录用户可以查看自己的历史订单。
*   作者可以管理书籍。
*   管理员可以管理分类。

在本书的后续章节中，每一章都会为示例应用程序增加一个新功能。在示例中实现这一点的最简单方式是为每一章创建一个新项目。

### 示例代码

我们需要解释一下本书中使用的编码和引用风格。你会发现有些代码清单是完整的，可以直接从书中复制到开发环境中。在其他代码清单中，我们省略了前面清单中已提及的某些导入或方法；我们已用`// Imports omitted`或`// Methods omitted`标记。大多数代码清单都附有引用，指向可找到这些方法或导入的代码清单。这种处理方式是为了提高可读性，因为有些代码清单只包含几行新代码，而另一些则会跨越多页。

另外需要说明的是，我们没有为常见类（如`java.lang.String`或`java.util.HashMap`）包含完整的包名（大多数`java.util`包也未包含），因为 Java 开发者通常知道这些类的位置。此外，我们只在首次重要引用（主标题之后）时提及完整的类名；例如，首次标识为`org.springframework.web.servlet.DispatcherServlet`的包，在后续引用中将简称为`DispatcherServlet`。这同样是为了提高可读性，但我们认为有必要向您展示类的位置，并相信这是一种可接受的做法。



### 本书结构

本书由一系列章节组成，每章解释框架的一部分或如何使用某项技术。前四章理论性较强，用于阐述一些更通用的概念及其在 Spring MVC 中的应用或工作原理（本书 Spring Web Flow 部分的开头也是如此；第 10 章也或多或少偏理论）。

在介绍了 Spring MVC 背后的概念之后，随着我们开始开发一个应用程序，其余章节将采用更实际、更动手操作的方式。

各章节如下：

*   第 1 章，“配置 Spring 开发环境”。本章介绍了开发环境的先决条件，你可以使用该环境来开发示例应用程序。本章还解释了示例应用程序（一个在线书店）的结构和目的。
*   第 2 章，“Spring 框架基础”，全面概述了 Spring 框架的基本构建块。它介绍了依赖注入（DI）和控制反转（IoC）的概念。如果你不熟悉 Spring，特别推荐阅读本章。
*   第 3 章，“Web 应用程序架构”。在本章中，我们将稍微绕道，解释 Web 应用程序架构。我们将解释（通常）构成 Web 应用程序的不同层，并解释模型-视图-控制器三元组。
*   第 4 章，“Spring MVC 架构”。本章是处理 Spring MVC 的第一个“深入”章节。它精确定义了什么是 MVC，Web 应用程序通常如何结构化或分层，并深入探讨了 Spring MVC 引擎的核心：强大的 `DispatcherServlet`。
*   第 5 章，“实现控制器”。此时，你可能已经准备好说：“给我看代码！”在本章中，我们将利用前几章的知识开始编写控制器；我们还将更深入地了解 Spring @MVC 的内部机制。最后，我们将启动示例应用程序。令人兴奋！
*   第 6 章，“实现控制器 – 高级”。每个应用程序在许多不同地方都需要相同的行为，以及一些并非核心应用程序一部分但需要嵌入某处的行为。本章将介绍面向切面编程（AOP）以及 Spring MVC 如何轻松解决一些常见的 Web 问题。我们还将进一步探索 Spring MVC 的内部机制，并解释如何扩展现有基础设施以及如何根据我们的需求进行定制。
*   第 7 章，“REST 和 AJAX”。现在我们的书店正在起步，我们想为应用程序添加一些精巧的行为，并且希望将我们的控制器暴露为 REST Web 服务，以便其他人能够与我们集成。为此，我们将探索 REST 和 AJAX，并将这些技术应用到我们的应用程序中。
*   第 8 章，“解析和实现视图”。第 8 章 更深入地探讨了视图在 Spring MVC 中是如何解析的，并基于你目前学到的信息进行构建。在本章中，你将重新审视 `ViewResolver` 基础设施，并在重用相同基础设施为同一模型提供不同呈现方式时，开始看到 MVC 架构的强大之处。
*   第 9 章，“测试 Spring MVC 应用程序”。既然完成某些任务的渴望已得到一定满足，并且我们已经编写了一些代码，现在是时候关注测试了。本章解释了*如何*测试 Spring MVC 应用程序，但同样关键的是*测试什么*。本章将深入讨论不同的测试策略，包括如何确保只测试你需要测试的内容。你还将有机会对 HTML 进行测试驱动！
*   第 10 章，“Spring Web Flow”。到目前为止，所有的页面交互都非常简单；每个用例只有一两个页面。现在，经理想要引入一些非平凡的用例。第 10 章 介绍了 Spring Web Flow——Spring MVC 的一个伴侣组件，它提供了一些非常精巧的功能来管理与客户端的 Web 会话。
*   第 11 章，“使用 Spring Web Flow 构建应用程序”。继前一章介绍 Spring Web Flow 及其帮助解决的问题之后，第 11 章将实践探索如何使用 Spring Web Flow 构建应用程序。
*   第 12 章，“高级 Spring Web Flow”，基于第 11 章获得的信息，演示了如何更深入地挖掘 Spring Web Flow 并发现一些真正的宝藏。
*   第 13 章，“Spring Security”，展示了如何通过使用 Spring 工具箱中另一个成熟的工具 Spring Security，将邋遢的黑客拒之我们的 Web 应用程序门外。
*   附录 A，“Cloud Foundry——部署到云端”。在本附录中，我们解释了将应用程序部署到云端（特别是 Cloud Foundry）所需的步骤，因为它与我们选择的开发环境无缝集成。

### Spring 生态系统的鸟瞰图

因此，在进一步深入之前，让我们先初步了解 Spring MVC 及其在现有 Spring 生态系统中的位置。

第一个好消息是 Spring MVC *就是* Spring。你可以使用现有的强大 Spring 容器来配置 Spring MVC。在 Spring MVC 中定义的 Bean 与其他任何 Bean 完全一样。

来自 [`http://static.springsource.org/spring/docs/3.1.0.M2/spring-framework-reference/html/overview.html`](http://static.springsource.org/spring/docs/3.1.0.M2/spring-framework-reference/html/overview.html) 的以下图片非常有帮助：

![Image](img/pxxvii.jpg)

如你所见——Spring MVC 由 Spring 的其余部分驱动！

### 继续前进！

现在你已经了解了本书的风格和目的，让我们不要再浪费时间，立即为你设置好开发环境。我们将在第 1 章中见。

### 联系作者

如有关于本书的疑问或建议，可以通过 `marten@deinum.biz` 联系 Marten Deinum。他的博客位于 [`http://mdeinum.wordpress.com`](http://mdeinum.wordpress.com)。如果你发现错误、想提问或想讨论与本书相关的任何其他内容，请随时联系他。

如有关于本书的任何事宜，可以通过 `koen.serneels@error.be` 联系 Koen Serneels。如果你有建议、意见或问题，或发现不准确之处，请随时给他留言。他的博客位于 [`http://www.error.be`](http://www.error.be)。

## 第 1 章

## 配置 Spring 开发环境

在你真正开始 Spring MVC 之旅之前，需要确保正确设置好开发环境。本章将首先引导你完成此过程。接着，将提供一些关于本书附带的示例书店应用程序的详细信息。但在深入探讨开发环境或示例应用程序的细节之前，本章将概述你环境的一般先决条件。

本书附带的示例应用程序用于解释 Spring MVC 和一般 MVC 的概念。它并非旨在成为一个功能完备、可直接投入生产的应用程序；也不打算用作 Java 或完整的 Spring Framework 应用程序。该应用程序的主要目的是帮助解释和表达本书中使用的 Spring MVC 概念。



### 前提条件

要构建示例应用程序，你需要安装 Java 开发工具包（JDK）；并且为了（独立）部署，你需要一个支持 Servlet 规范 3.0 版本的 Servlet 容器（我们选择使用 Tomcat 7）。为了简化开发，你需要使用集成开发环境（IDE）；在本书中，我们（作者）选择使用 SpringSource Tool Suite（STS）。表 1-1 列出了编写本书时使用的产品和版本。所选产品的开发仍在继续，因此在你阅读本书时，可能会有更新的版本可用。不过，代码中没有任何内容不适用于或不能与软件的更新版本配合使用。

![Image](img/tab_1_1.jpg)

#### Java 开发工具包

第一个要素是 Java 开发工具包。JDK7 最近已发布；然而，JDK6 似乎仍然被更广泛地使用，尤其是在企业中。使用 JDK7 运行示例应用程序没有已知问题，因此那些想要“走在技术前沿”的人可以自由选择。那些希望获得最流畅体验的人应该选择 JDK6。

![Image](img/square.jpg) **注意** 你可能已经安装了 Java 运行时环境；但是，你仍然需要 Java 开发版（JDK）。JRE 仅用于运行 Java 应用程序，而 JDK 用于编译 Java 代码。

#### Servlet 容器

要运行 Java Web 应用程序，你需要一个 Web 容器。示例应用程序使用了 Servlet 3.0 特性；因此，你需要一个支持 Servlet 3.0 的 Web 容器。Tomcat（[`http://tomcat.apache.org/`](http://tomcat.apache.org/)）是一个极好的选择，并且广泛应用于生产系统。示例文件中没有任何 Tomcat 特定的内容，因此你可以自由使用 Jetty、JBoss、Glassfish 或任何其他现代 Web 容器或应用服务器。

![Image](img/square.jpg) **提示** STS 包含其自有的 Tomcat 版本，称为 *tc-server*（[`www.vmware.com/products/vfabric-tcserver/`](http://www.vmware.com/products/vfabric-tcserver/)）。这是一个基于 Tomcat 构建的优秀产品，为开发和生产环境都提供了价值。

#### 集成开发环境

虽然 Spring 本身不需要特定的开发环境（除了 Java 开发工具包之外），但强烈建议熟悉一个好的 IDE。

鉴于 Spring 在企业级 Java 开发中的普遍性，在主要的 IDE（如 Eclipse（[`www.eclipse.org`](http://www.eclipse.org)）和 IntelliJ IDEA（[`www.jetbrains.com/idea/`](http://www.jetbrains.com/idea/)））中都可以找到对其出色的支持。SpringSource 也投入了大量精力，提供了基于 Eclipse 的自有发行版，称为 SpringSource Tool Suite（STS）。虽然“原版”Eclipse 已经很出色，但在开发基于 Spring 的应用程序时，STS 提供了更高层次的优化。

### 示例应用程序

本节将深入探讨你将在本书中构建的示例应用程序。例如，你将了解为示例应用程序选择并使用的构建系统。接下来，你将学习如何将示例应用程序导入 STS，并迈出部署应用程序第一部分的第一步。

示例应用程序的主要目的是展示 Spring MVC 的特性和可能性。虽然 Spring MVC 是 Spring 框架的一部分，但此应用程序并不旨在全面、深入地涵盖 Spring 框架的所有特性。如果你想阅读并了解更多关于 Spring 框架的内容，我们建议查阅 *Pro Spring 3*（Apress，2012 年）。

#### 一个书店示例应用程序

将在本书中构建和使用的示例应用程序是一个简单的书店。你将学习如何添加搜索、显示和购买书籍的功能。要购买书籍，应用程序的用户需要在应用程序关联的书店拥有一个账户，因此还需要一些账户注册和登录页面。示例应用程序在本书中将基本保持不变。然而，在 Spring Web Flow 章节中，你将看到一些重要的差异，这些差异主要是为了说明 Spring Web Flow 的某些功能。

你可以在本书的源代码中找到示例应用程序的代码。请继续下载并解压它。请注意，顶层目录包含多个名为 `chapterX-bookstore` 的文件夹。随着你阅读本书的进度，你将不断向示例应用程序添加新功能。在某些情况下，你将重新审视前面章节的代码。为了简洁和清晰，作者决定将每章的进展分离到单独的项目中；要查看第 X 章的相关代码，只需加载 `chapterX-bookstore` 文件夹中的项目即可。

除了每章的目录外，你还可以找到两个在所有章节之间共享的项目（参见图 1-1）。这些项目名为 `bookstore-shared` 和 `bookstore-web-resources`，它们与所有项目共享。一个项目包含共享的业务层，而另一个项目包含图像、样式表和其他类似文件。

![Image](img/9781430241553_Fig01-01.jpg)

***图 1-1.** 书店组件概览*

![Image](img/square.jpg) **注意** `chapterX-bookstore` 中的项目包含第 X 章的解决方案。

虽然这种方法确实有缺点，即需要你向 STS 加载许多项目（每章一个），但它也意味着你可以准确地看到应用程序的进展——如果你想自己解决第 7 章中的问题，那么复制第 6 章的项目并开始工作，如果遇到困难，可以将第 7 章作为参考点。

为方便起见，相关章节的示例应用程序的根目录（即本章的 `chapter1-bookstore`）将被称为 `SAMPLE_ROOT`。`SAMPLE_ROOT` 显然会因章节而异。

作者试图创建一个现实但简单的示例应用程序；为此，我们尝试使用当今的技术。例如，我们使用 JPA 2.0 作为持久层，并依赖 Hibernate 4.1 作为我们的具体实现。对于网页，主要使用的技术是 Java 服务器页面（JSP）。关于 JSP 的全面教程不在本书的讨论范围之内；然而，有许多关于此主题的好书可用，例如 *Pro JSP 2*（Apress，2005 年）。你还可以找到大量关于此技术的其他资源，包括 Oracle 自己的教程，网址为 [`http://download.oracle.com/javaee/5/tutorial/doc/bnagx.html`](http://download.oracle.com/javaee/5/tutorial/doc/bnagx.html)。

![Image](img/square.jpg) **注意** 细心的读者可能注意到了教程 URL 中的 JEE5 部分。JEE6 版本的教程涵盖了 JSF 但没有涵盖 JSP；因此，这里引用了 JEE5 教程。



#### 构建系统

关于如何构建应用程序的话题，几十年来一直饱受争论、狂热甚至十足的愚蠢行为困扰。我们本可以走捷径，通过完全使用 SpringSource Tool Suite (STS) 来构建和部署，从而避免这场讨论（我们将在本章后面讨论 STS）。然而，作者认为这样做对您是一种伤害。STS 足以管理开发人员的工作流程，但对于运维管理方面却不太够用。通常，应用程序使用一种机制（IDE）构建，而使用完全不同的机制部署到生产环境。这常常是挫败感的来源，并导致了著名的“在我机器上能跑”综合症。

考虑到这一点，我们强烈认为，确保应用程序在尽可能接近生产环境的环境中运行是开发人员的责任，*包括构建机制本身！* 您将看到复杂的构建工具如何使这成为可能。本书中您将使用的构建工具是 Gradle ([`http://gradle.org`](http://gradle.org)`)`。这是一个相当新但崭露头角的*项目自动化*工具（项目自动化是*构建工具*的花哨说法），它有着强大的血统，并且已经建立了非常好的声誉。对构建工具的全面讨论超出了本书的范围；不过，可以这么说，Gradle 结合了 Maven“约定优于配置”方法的强大功能与 Groovy 的简洁性。

![Image](img/square.jpg) **注意** Gradle 项目赢得了 2010 年 Springy 奖的最具创新产品/项目奖^(1)，并且是 2011 年 JAX 创新奖的决赛入围者。

____________

¹ [`http://www.springsource.org/node/2871`](http://www.springsource.org/node/2871)

Gradle 基础设施由 `build.gradle` 脚本（这有点类似于 Maven 的 `pom.xml`）和一个可选的 `settings.gradle` 文件驱动；两者都可以在 `SAMPLE_ROOT` 目录中找到。

![Image](img/square.jpg) **提示** 如果您更喜欢使用 Maven，请注意 Gradle 完全能够生成 Maven POM（项目对象模型）。更多信息，请阅读 [`http://gradle.org/maven_plugin.html`](http://gradle.org/maven_plugin.html) 上的“Maven POM 生成”。

##### 依赖管理

Java 的优势在于其生态系统。丰富的高质量库是弥补该语言冗长和痛苦的代价。然而，它最大的优势，一如既往，也是它最大的弱点：中小型项目拥有超过 50 个库的依赖图并不罕见。这种库的泛滥因正确使用模块化而变得更加严重！

依赖的问题在于依赖本身也有依赖，这些依赖还有依赖，如此循环……嗯，您可能明白我的意思了。有直接依赖（项目的依赖）和传递依赖（直接依赖所需的依赖）。然而，这还不是唯一的问题，因为还存在所谓的*版本地狱*。如果有多个依赖，并且这些依赖都依赖于库 x 但版本不同，这就带来了额外的挑战。但是，等等，还有更多！您不想坐在电脑前谷歌搜索所有缺失的（传递）依赖，下载它们，重新部署您的应用程序，*然后*发现还有另一个缺失的依赖（或者可能是错误的依赖版本）。

依赖管理在这里可以帮助您。例如，它帮助您下载和发现您需要的东西，并尝试解决所有版本冲突。在这种情况下，您可以让计算机为您完成繁重的工作，而您可以专注于手头的任务，即解决业务问题——在本书的例子中是卖书（而不是深夜寻找依赖）。

![Image](img/square.jpg) **注意** Gradle 的依赖管理基础设施与某种程度上行业标准的 Maven 仓库兼容——并且它在解决依赖方面有出色的支持。

##### Gradle 构建文件

如前所述，Gradle 不仅仅是一个依赖管理工具。它也是一个构建工具；因此，它可以为您做很多事情。例如，如果您打开 `build.gradle` 文件，您会看到所有子项目所需的依赖。Gradle 还可以帮助您构建（编译、测试和创建 war 归档）您的应用程序，并且可以使用 Groovy 语言轻松修改或影响您构建应用程序的方式。为了帮助构建应用程序，有一个插件可以将应用程序部署到嵌入式 Tomcat 实例。这个实例反过来可以用于运行集成测试（参见第 9 章）。

这些功能只是 Gradle 所能提供功能的开始。如果您对更复杂的构建感兴趣，请尝试阅读 Gradle 参考指南^(2)，或者查看 Spring 框架^(4) 使用的 Gradle 构建^(3)（是的，连 SpringSource 也在使用 Gradle）。

#### 构建示例应用程序

要构建示例，请 `cd` 进入 `SAMPLE_ROOT` 目录并执行 `gradlew` 脚本。假设您使用的是 Unix 系统，这看起来会像图 1-2 所示。

![Image](img/9781430241553_Fig01-02.jpg)

***图 1-2.** 初始构建输出*

您会注意到 `gradlew` 脚本做的第一件事就是下载 Gradle 发行版本身！这就是为什么没有“安装 gradle”这一步——它是自安装的。

![Image](img/square.jpg) **注意** 我们建议将 `gradlew` 脚本检入源代码控制。持续集成（CI）服务器以及（其他开发人员）都使用此脚本。升级到新版本的 Gradle 就像更新 `gradlew` 脚本一样简单，该脚本在执行时会静默地自我升级。

____________

² [`http://www.gradle.org/docs/current/userguide/userguide.html`](http://www.gradle.org/docs/current/userguide/userguide.html)

³ [`https://github.com/SpringSource/spring-build-gradle`](https://github.com/SpringSource/spring-build-gradle)

⁴ [`https://github.com/SpringSource/spring-framework`](https://github.com/SpringSource/spring-framework)

![Image](img/square.jpg) **注意** 说到升级；在撰写本文时，v1.0 尚未发布，因此本书中的代码依赖于最新的里程碑版本：里程碑 9。您的输出可能略有不同。

在 Gradle 下载自身之后，您应该通过执行 `../gradlew build` 来构建示例应用程序（参见图 1-3）。

![Image](img/9781430241553_Fig01-03.jpg)

***图 1-3.** 构建示例应用程序输出*

注意 Gradle 如何下载示例依赖并经历编译、测试和打包示例应用程序的生命周期。与 Maven 类似，Gradle 包含一个 JAR 的本地缓存（您主目录中的 `.gradle` 目录）。后续执行会快得多，因为不需要网络访问（更多信息请参见 [`http://gradle.org/documentation`](http://gradle.org/documentation)）。

![Image](img/square.jpg) **提示** 要获取所有可执行任务的列表，请键入 `gradlew tasks`。

假设一切顺利，输出应该是一个位于 `build/libs` 目录中的 `chapter1-bookstore-1.0.0.war` 文件。



#### 部署示例应用

下一步是将应用部署到 Web 容器中。Tomcat (`http://tomcat.apache.org/`) 是一个绝佳的选择，因此你可以下载并安装它。如前所述，示例文件中没有任何 Tomcat 特有的内容，因此你也可以自由使用 Jetty、JBoss、Glassfish 或任何其他现代 Web 容器。

![Image](img/square.jpg) **注意** 为了减少你需要学习的新内容——如果你对其他 Web 容器更熟悉，那么现在坚持使用你所了解的工具可能更有意义。

现在，将 WAR 文件从 `SAMPLE_ROOT/build/libs/chapter1-bookstore-1.0.0.war` 复制到 `TOMCAT_ROOT/webapps`。如果 Tomcat 尚未启动，则通过执行 `TOMCAT_ROOT/bin/startup.sh run`（Windows 系统为 `.bat`）来启动它。

现在打开你最喜欢的 Web 浏览器，并导航至 [`http://localhost:8080/chapter1-bookstore-1.0.0`](http://localhost:8080/chapter1-bookstore-1.0.0)。如果一切顺利，你应该会看到 图 1-4 中的界面。

![Image](img/9781430241553_Fig01-04.jpg)

***图 1-4.** 书店主页*

什么！你就只能做到这样吗？这个界面看起来糟透了！当然，你可以做得更好 :)。

然而，这个示例应用的重点是从*非常*简单开始并逐步构建，因此没有任何多余的*花哨内容*干扰细节。别担心；随着你的深入，这个应用会变得**好得多**。

![Image](img/square.jpg) **注意** `chapter1-bookstore-1.0.0` 并不是最好的名称；首先，它读起来就不太顺口。Tomcat 中的默认命名策略是将*上下文路径*设置为 WAR 文件的名称。例如，如果 WAR 文件的名称为 `bookstore.war`，那么上下文路径就是 `/bookstore`。

恭喜！你已经完成了关键但经常被忽视的一步：确保你的应用可以在 IDE 之外构建和运行。下一步是安装 STS，并实际查看你刚刚出色部署的示例。

在继续之前，请确保按下 `Control-C` 关闭 Tomcat。

### SpringSource Tool Suite (STS)

正如我们之前所解释的，Spring 并不要求特定的开发环境，但我们确实建议你考虑一个好的 IDE。像 Eclipse 和 IntelliJ IDEA 这样的主流 IDE 是极好的选择，但基于 Eclipse 的 SpringSource Tool Suite (STS) 为开发基于 Spring 的应用提供了额外的好处。本书将使用 STS。

#### 为 Gradle 项目配置 STS

要安装 Gradle 支持，请点击“安装扩展”。第一次执行此操作时，STS 会下载扩展列表，这可能需要几秒钟。最终，“安装扩展”页面将准备就绪（参见 图 1-5）。

![Image](img/9781430241553_Fig01-05.jpg)

***图 1-5.** STS 中的“扩展”选项卡*

接下来，搜索“gradle”；图 1-6 显示了搜索结果。

![Image](img/9781430241553_Fig01-06.jpg)

***图 1-6.** “gradle”搜索结果*

现在选中“Gradle 支持”旁边的复选框，然后点击右下角的“安装”。查看提示并点击“下一步”以打开“安装”对话框（参见 图 1-7）。

![Image](img/9781430241553_Fig01-07.jpg)

***图 1-7.** “安装”对话框*

点击“下一步”进入“安装详情”对话框，再次点击“下一步”进入许可协议页面。最后，阅读许可协议（谁会读这些？）并点击“完成”。Gradle 插件现在应该开始下载和安装。安装完成后，系统会要求你重新启动——点击“是”。如前所述，典型的 Java 应用可能具有庞大的依赖关系图。Gradle 已经处理了这个问题，如果为了向 STS 注册而必须手动解析依赖关系，那将非常痛苦。幸运的是，STS 实际上能够理解 Gradle 项目（就像它理解 Maven 项目一样）。

#### 将示例导入 STS

既然 STS 知道如何导航 Gradle 项目，下一步就是将项目导入 STS。为此，选择“文件”![Image](img/U003.jpg)“导入”（即从“文件”菜单中选择“导入”选项）。这将打开“导入”对话框。选择“Gradle”![Image](img/U003.jpg)“Gradle 项目”（参见 图 1-8）。最后，点击“下一步”（参见 图 1-9）。

![Image](img/9781430241553_Fig01-08.jpg)

***图 1-8.** STS 导入对话框*

![Image](img/9781430241553_Fig01-09.jpg)

***图 1-9.** 导入 Gradle 项目对话框*

现在点击“浏览”并导航到包含示例项目的目录。接下来，点击“构建模型”。

![Image](img/square.jpg) **注意** STS 会下载其自己的 Gradle 版本，并且如果你跳过了之前通过命令行构建示例应用的步骤，STS 还会下载应用的依赖项。这可能需要几分钟时间。

一旦 Gradle 构建了项目结构的内部表示（即项目及其所有子项目），它将允许你选择要导入的项目（参见 图 1-10）。

![Image](img/9781430241553_Fig01-10.jpg)

***图 1-10.** 可以导入的 Gradle 项目*

你可以选择根项目“bookstore”，或者点击“全选”以导入所有项目。之后，点击“完成”指示 STS 实际导入你的项目。这可能需要一些时间，因为 STS 正在下载所有缺失的依赖项并编译所有项目。不过，这些项目最终应该会出现在 STS 中（参见 图 1-11）。

![Image](img/9781430241553_Fig01-11.jpg)

***图 1-11.** 导入项目后的 STS*

![Image](img/square.jpg) **提示** 我们建议你关闭当前不使用的项目。为此，选择要关闭的项目，右键单击，然后选择菜单选项“关闭项目”。你可以通过双击项目来重新打开它。

#### 在 SpringSource vFabric tc Server 上运行应用

在运行应用之前，你必须告诉 STS 要在哪个 Web 容器上运行该应用。你有两个选择：使用你之前下载的 Tomcat 安装，或者使用预构建的 VMware vFabric tc Server。

![Image](img/square.jpg) **注意** 注册 Tomcat 非常简单：右键单击右下角的“服务器”部分，选择“新建”![Image](img/U003.jpg)“服务器”，展开菜单至“Apache/Tomcat v7.0 服务器”，点击“下一步”，浏览到 Tomcat 的安装根目录，点击“打开”，然后点击“确定”。

要在服务器上运行应用，请右键单击项目并选择“运行方式”![Image](img/U003.jpg)“在服务器上运行”（参见 图 1-12）。

![Image](img/9781430241553_Fig01-12.jpg)

***图 1-12.** 选择“在服务器上运行”菜单选项*

选择“在服务器上运行”应打开“在服务器上运行”对话框（参见 图 1-13）。

![Image](img/9781430241553_Fig01-13.jpg)

***图 1-13.** “在服务器上运行”对话框*

选择“VMware vFabric tc Server Developer Edition v2.6”条目（或者 Tomcat，如果你决定使用它，则会显示）。接下来，确保选中“运行此项目时始终使用此服务器”，然后点击“完成”。然后，应用应该会被构建并部署到 tc Server 上。启动后，欢迎页面应该会加载（参见 图 1-14）。

![Image](img/square.jpg) **注意** 你可能会注意到，tc Server 启动所需的时间比普通 Tomcat 长得多，因为它需要初始化各种子组件。

![Image](img/9781430241553_Fig01-14.jpg)

***图 1-14.** 正在运行的应用*



#### 编辑应用程序

恭喜你走到这一步！这一切值得吗？作者们认为是值得的。为了展示 STS 的强大功能，我们来做一些编辑。目前不必担心细节，这些将在后面详细解释。现在，你只需专注于使用 STS。

打开 `IndexController`（使用 Control-Shift-T 组合键，然后输入 **IndexController**，或者使用树形导航找到它），并导航到第 12 行（参见图 1-15）。

![Image](img/9781430241553_Fig01-15.jpg)

***图 1-15.** IndexController 代码*

现在，替换（或修改）IndexController 选项卡上的代码，如代码清单 1-1 所示。

***代码清单 1-1.** 包含模型属性的 IndexController*

`package com.apress.prospringmvc.bookstore.web;`

`import org.springframework.stereotype.Controller;`
`import org.springframework.web.bind.annotation.RequestMapping;`
`import org.springframework.web.servlet.ModelAndView;`

`@Controller`
`public class IndexController {`

`    @RequestMapping(value = "/index.htm")`
`    public ModelAndView indexPage() {`
`        ModelAndView mav = new ModelAndView("/WEB-INF/views/index.jsp");`
`        mav.addObject("theModelKey", "Spring says HI!");`
`        return mav;`
`    }`
`}`

接下来，使用 Control-Shift-R 组合键打开资源，然后输入 **index.jsp** 来打开 `index.jsp`（参见图 1-16）。

![Image](img/9781430241553_Fig01-16.jpg)

***图 1-16.** index.jsp 的内容*

在段落标签（`<p>`）下方，输入 **`<br/><p>The model says ${theModelKey}</p>`**。然后，保存更改，并注意新更改会自动重新加载到控制台选项卡中。通过点击网页上的双箭头图标（从左数第四个图标）刷新网页，以查看更改（参见图 1-17）。

![Image](img/9781430241553_Fig01-17.jpg)

***图 1-17.** 更新后的网页*

太棒了！这里无需重新部署，因为 STS 会自动部署你所做的更改。

### 本章小结

在本章中，你学习了构建和运行示例书店应用程序所需的先决条件。你还了解了示例应用程序所使用的支持技术（即 Hibernate/JPA 2.0 和 JSP）。

接下来，你学习了 Gradle，这是用于开发示例应用程序的构建系统；你还了解了它将如何帮助你进行项目开发。

为了编写和调试本书的代码，最好使用 IDE（集成开发环境）。作者选择使用 STS（SpringSource Tool Suite），安装了 Gradle 插件，然后使用该插件导入并构建了示例项目。该项目随后被部署到嵌入式 tc Server 实例中。

你还为你的 MVC 应用程序编写了第一段 Java 代码，稍微修改了本章的基本代码，并利用它探索了嵌入式服务器的自动重新部署功能。

在下一章中，随着你对 Spring MVC 内部工作原理的更深入理解，你将对该应用程序进行改进。

## 第 2 章

## Spring 框架基础

Spring 框架源于 Rod Johnson 为《Expert One-on-One J2EE Design and Development》（Wrox, 2002）一书编写的代码。该框架结合了业界 Java 企业版（JEE）开发的最佳实践，并与同类最佳第三方框架进行了集成。如果你需要尚不存在的集成方式，它还提供了简单的扩展点来编写你自己的集成。该框架的设计始终以开发人员生产力为核心，使得使用现有（有时是繁琐的）Java 和 JEE API 变得更加容易。

在我们开始探索 Spring MVC 和 Spring Web Flow 之前，我们将快速回顾一下 Spring Core（以前称为 Spring 框架）的知识。Spring 现在早已是 Java 企业软件开发的事实标准。它向我们中的许多人介绍了诸如*依赖注入*、*面向切面编程*（AOP）以及使用*普通 Java 对象*（POJO）进行编程等概念。

在本章中，我们将介绍依赖注入和 AOP。具体来说，我们将介绍 Spring 框架如何帮助我们实现依赖注入，以及如何利用编程来发挥我们的优势。为了能够做到这里提到的事情，我们将探索控制反转（IoC）容器：*应用程序上下文*。

我们在这里只涉及 Spring 框架必要的基础知识。如果你想了解更深入的信息，我们推荐优秀的 Spring 参考指南（[`www.springsource.org`](http://www.springsource.org)）或诸如《Pro Spring 3》（Apress, 2012）或《Spring Recipes, 第 2 版》（Apress, 2011）等书籍。

让我们先快速浏览一下 Spring 框架及其组成模块。

![Image](img/square.jpg) **提示**  你可以在 `chapter2-samples` 项目中找到本章的示例代码。示例的不同部分包含一个带有 `main` 方法的类，你可以运行它来执行代码。

### Spring 框架

在引言中，我们提到 Spring 框架源于 Rod Johnson 为《Expert One-on-One J2EE Design and Development》一书编写的代码。这本书旨在解释 JEE 中的一些复杂性以及如何克服它们。尽管 JEE 中的许多复杂性和问题已在较新的 JEE 规范（尤其是自 JEE 6 以来）中得到解决，但 Spring 框架仍然非常流行。

它之所以仍然流行，是因为其构建应用程序的简单（而非过于简化！）方法。它还针对不同类型的技术（无论是数据访问还是消息传递基础设施）提供了一致的编程模型。该框架允许开发人员针对离散的问题，并专门为其构建解决方案。

该框架由几个模块（参见图 2-1）组成，这些模块协同工作并相互构建。我们可以相当自由地挑选我们想要使用的模块。

![Image](img/9781430241553_Fig02-01.jpg)

***图 2-1.** Spring 框架概览*

图 2-1 中的所有模块都表示为 jar 文件，如果我们需要特定技术，可以将它们包含在类路径中。在表 2-1 中，我们列出了 Spring 3.1 附带的所有模块，并简要描述了每个模块的内容，以及可能用于依赖管理的任何工件名称。实际 jar 文件的名称可能因获取模块的方式而异。可下载的发行版包含以下形式的 jar：`org.springframework.[module-name]-[version].jar`。来自 Maven 仓库的 jar 使用以下形式：`spring-[artifact].jar`。参见表 2-1。

![Image](img/tab_2_1.jpg)

![Image](img/tab_2_1a.jpg)

大多数模块都依赖于 Spring 框架中的其他模块。`core` 模块是此规则的例外。图 2-2 概述了常用模块及其对其他模块的依赖关系。请注意，`instrumentation`、`aspect` 和 `test` 模块未出现在图中；这是因为它们的依赖关系取决于项目以及使用了哪些其他模块。Spring 框架只有一个必需的依赖项：`commons-logging`，一个日志抽象框架。其他依赖项根据项目的需求而有所不同。

![Image](img/9781430241553_Fig02-02.jpg)

***图 2-2.** Spring 框架模块依赖关系*



### 依赖注入

依赖注入（DI）的概念——即对象在构造时被赋予其依赖项——是 Spring 框架的基石之一。你可能也听说过*控制反转*（IoC）^(1)。IoC 是一个更宽泛、更通用的概念，可以通过不同方式实现。IoC 让开发者能够解耦，并专注于企业应用中某个特定部分的重要事项，而无需考虑系统中其他部分的功能。面向接口编程是思考解耦的一种方式。

几乎每个企业应用都由多个需要协同工作的组件构成。在 Java 企业级开发的早期，我们简单地将所有构建这些对象（以及这些对象所需的对象）的逻辑都放在构造函数中（参见清单 2-1）。乍一看，这种方法并无不妥；然而，随着时间的推移，对象构建变得缓慢，并且对象拥有了许多本不该拥有的知识（参见单一职责原则^(2)）。这些类变得难以维护，并且进行单元测试和/或集成测试也相当困难。

***清单 2-1.** 具有硬编码依赖关系的 MoneyTransferService 实现*

`package com.apress.prospringmvc.moneytransfer.simple;`

`import java.math.BigDecimal;`

`import com.apress.prospringmvc.moneytransfer.domain.Account;`
`import com.apress.prospringmvc.moneytransfer.domain.MoneyTransferTransaction;`
`import com.apress.prospringmvc.moneytransfer.domain.Transaction;`
`import com.apress.prospringmvc.moneytransfer.repository.AccountRepository;`
`import com.apress.prospringmvc.moneytransfer.repository.MapBasedAccountRepository;`
`import com.apress.prospringmvc.moneytransfer.repository.MapBasedTransactionRepository;`
`import com.apress.prospringmvc.moneytransfer.repository.TransactionRepository;`
`import com.apress.prospringmvc.moneytransfer.service.MoneyTransferService;`

`public class SimpleMoneyTransferServiceImpl implements MoneyTransferService {`

`    private AccountRepository accountRepository = new MapBasedAccountRepository();`
`    private TransactionRepository transactionRepository = new MapBasedTransactionRepository();`

`    public SimpleMoneyTransferServiceImpl() {`
`        super();`
`        ((MapBasedAccountRepository) this.accountRepository).initialize();`

`    }`

`    @Override`
`    public Transaction transfer(String source, String target, BigDecimal amount) {`
`        Account src = this.accountRepository.find(source);`
`        Account dst = this.accountRepository.find(target);`

`        src.credit(amount);`
`        dst.debit(amount);`

`        MoneyTransferTransaction transaction = new MoneyTransferTransaction(src, dst, amount);`
`        this.transactionRepository.store(transaction);`
`        return transaction;`
`    }`
`}`

____________

¹ [`http://www.martinfowler.com/articles/injection.html`](http://www.martinfowler.com/articles/injection.html)

² [`http://www.objectmentor.com/resources/articles/srp.pdf`](http://www.objectmentor.com/resources/articles/srp.pdf)

清单 2-1 中的类面向接口编程，但它仍然需要了解接口的具体实现，仅仅是为了进行对象构建。通过解耦构建逻辑（协作对象）来应用 IoC，可以使应用程序更易于维护并提高可测试性。有七种方法可以解耦这种依赖构建逻辑：

1.  工厂模式
2.  服务定位器模式
3.  依赖注入
    1.  基于构造函数
    2.  基于 Setter 方法
    3.  基于接口
    4.  注解驱动
4.  上下文查找

当使用工厂模式、服务定位器模式或上下文查找时，需要依赖项的类仍然需要了解如何获取这些依赖项。这可以使事情更易于维护，但测试可能仍然困难。清单 2-2 展示了来自 JNDI（Java 命名和目录接口）的上下文查找。构造函数代码需要知道如何进行查找并处理异常。

***清单 2-2.** 具有上下文查找的 MoneyTransferService 实现*

`package com.apress.prospringmvc.moneytransfer.jndi;`

`import javax.naming.InitialContext;`
`import javax.naming.NamingException;`

`//其他 import 语句已省略。`
`public class JndiMoneyTransferServiceImpl implements MoneyTransferService {`

`    private AccountRepository accountRepository;`
`    private TransactionRepository transactionRepository;`

**`    public JndiMoneyTransferServiceImpl() {`**
**`        try {`**
**`            InitialContext context = new InitialContext();`**
**`            this.accountRepository = (AccountRepository) context.lookup("accountRepository");`**
**`            this.transactionRepository = (TransactionRepository) context.lookup`**
**`("transactionRepository");`**
**`        } catch (NamingException e) {`**
**`            throw new IllegalStateException(e);`**
**`        }`**
**`    }`**

`    //transfer 方法已省略，与清单 2-1 相同`
`}`

上述代码并不特别清晰；例如，想象一下如果存在来自不同上下文的多个依赖项。代码会很快变得混乱，并且越来越难以（甚至不可能）进行单元测试（更多关于测试的信息，请参见第 9 章）。

为了解决对象构造函数中的构建/查找逻辑问题，我们可以使用依赖注入。我们只需将对象工作所需的依赖项传递给对象。这使我们的代码清晰、解耦且易于测试（参见清单 2-3）。依赖注入是一个过程，对象在其中指定它们所依赖的依赖项。IoC 容器使用该规范；当它构造一个对象时，也会注入其依赖项。这样，我们的代码更清晰，不再让类承担构建逻辑的负担。它更易于维护，也更易于进行单元测试和/或集成测试。测试更容易，因为我们可以注入一个桩（stub）或模拟（mock）对象来验证我们对象的行为。

***清单 2-3**. 基于构造函数依赖注入的 MoneyTransferService 实现*

`package com.apress.prospringmvc.moneytransfer.constructor;`

`// import 语句已省略`

`public class MoneyTransferServiceImpl implements MoneyTransferService {`

`    private AccountRepository accountRepository;`
`    private TransactionRepository transactionRepository;`

**`    public MoneyTransferServiceImpl(AccountRepository accountRepository,`**
**`                                    TransactionRepository transactionRepository) {`**
**`        super();`**
**`        this.accountRepository = accountRepository;`**
**`        this.transactionRepository = transactionRepository;`**
**`    }`**
`//transfer 方法已省略，与清单 2-1 相同`
`}`

顾名思义，基于构造函数的依赖注入使用构造函数将依赖项注入到对象中。清单 2-3 使用了基于构造函数的依赖注入。它有一个构造函数，接受两个对象作为参数：`com.apress.prospringmvc.moneytransfer.repository .AccountRepository` 和 `com.apress.prospringmvc.moneytransfer.repository.TransactionRepository`。当我们构造 `com.apress.prospringmvc.moneytransfer.constructor .MoneyTransferServiceImpl` 的实例时，需要向其提供所需的依赖项。



基于 Setter 的依赖注入使用 *setter* 方法来注入依赖项。JavaBeans 规范定义了 `setter` 和 `getter` 方法。如果我们有一个名为 `setAccountService` 的方法，那么我们就设置了一个名为 `accountService` 的属性。属性名是通过方法名去掉“set”并将首字母小写来创建的（完整规范可在 JavaBeans 规范中找到^(3)）。清单 2-4 展示了一个基于 Setter 的依赖注入示例。

***清单 2-4**. 一个使用基于 Setter 的依赖注入的 MoneyTransferService 实现*

`package com.apress.prospringmvc.moneytransfer.setter;`

`// 导入已省略`

`public class MoneyTransferServiceImpl implements MoneyTransferService {`

`    private AccountRepository accountRepository;`
`    private TransactionRepository transactionRepository;`

**`    public void setAccountRepository(AccountRepository accountRepository) {`**
**`        this.accountRepository = accountRepository;`**
**`    }`**

**`    public void setTransactionRepository(TransactionRepository transactionRepository) {`**
**`        this.transactionRepository = transactionRepository;`**
**`    }`**
`// 转账方法已省略，与清单 2-1 相同`
`}`

最后，还有基于注解的依赖注入（参见清单 2-5）。要使其工作，我们不需要指定构造函数参数或 setter 方法来设置依赖项。我们首先定义一个可以持有依赖项的类级别字段。接着，我们在该字段上放置一个注解，以表达我们希望将该依赖项注入到对象中的意图。Spring 接受几种不同的注解：`@Autowired`、`@Resource` 和 `@Inject`。所有这些注解的工作方式大致相同。深入解释这些注解之间的差异不在本书的讨论范围内，因此如果您想了解更多，我们建议您参考 Spring 参考指南或 *Pro Spring 3*（Apress，2012）。它们之间的主要区别在于，`@Autowired` 注解来自 Spring 框架，而 `@Resource` 和 `@Inject` 是 Java 标准注解。

____________

³ [`http://download.oracle.com/otn-pub/jcp/7224-javabeans-1.01-fr-spec-oth-JSpec/beans.101.pdf`](http://download.oracle.com/otn-pub/jcp/7224-javabeans-1.01-fr-spec-oth-JSpec/beans.101.pdf)

***清单 2-5**. 一个使用基于注解的依赖注入的 MoneyTransferService 实现*

`package com.apress.prospringmvc.moneytransfer.annotation;`

`import org.springframework.beans.factory.annotation.Autowired;`

`// 其他导入已省略`

`public class MoneyTransferServiceImpl implements MoneyTransferService {`

`    @Autowired`
`    private AccountRepository accountRepository;`

`    @Autowired`
`    private TransactionRepository transactionRepository;`

`    // 转账方法已省略，与清单 2.1 相同`
`}`

![Image](img/square.jpg) **注意** `@Autowired` 和 `@Inject` 可以放置在方法和构造函数上以表达依赖注入配置，即使在有多个参数的情况下也是如此！

请注意，Spring 框架不支持基于接口的依赖注入。这意味着我们需要为某个接口指定要注入的具体实现。

![Image](img/square.jpg) **注意**  Google Guice^(4) 开箱即用地支持基于接口的注入。

总结一下，我们使用依赖注入的原因如下：

1.  更清晰的代码
2.  解耦的代码
3.  更简单的代码测试

前两个原因使我们的代码更易于维护。代码更易于测试这一事实应该使我们能够编写单元测试来验证对象的行为——从而验证我们的应用程序。

在了解了三种不同的依赖注入方法之后，您可能会想知道应该使用哪一种。这或多或少是一个哲学讨论，但这里有一个我们可以使用的经验法则：如果某个依赖项对于对象正常运行是强制性的，我们应该使用基于构造函数或基于注解的依赖注入。如果我们使用基于注解的依赖注入，而依赖项未被注入，我们将在应用程序启动时收到异常。这是因为，在使用基于注解的依赖注入时，默认情况下依赖项是强制性的。鉴于 Java 和 JEE 的最新发展和新增功能，本书作者认为基于注解的方法是最佳选择。`javax.annotation.Resource` 注解被添加到 Java 以及 JSR-330（Java 依赖注入）中，这相当清楚地表明这是 Java 社区流程（JCP）所设想的未来。

____________

⁴ [`http://code.google.com/p/google-guice/`](http://code.google.com/p/google-guice/)

在本书中，我们将尽可能使用基于 Java 的配置方法。如果基于 Java 的配置选项不可行或不可用，我们将回退到基于 XML 的配置。我们在介绍 Web Flow 时就是这种情况。在撰写本文时，Web Flow 尚未更新以受益于基于 Java 的配置方法。



### 应用上下文

为了在 Spring 中实现依赖注入，我们需要一个*应用上下文*。在 Spring 中，这是 `org.springframework.context.ApplicationContext` 接口的一个实例。应用上下文负责管理其中定义的 bean，还支持更复杂的功能，例如对其中的 bean 应用 AOP。

`ApplicationContext` 接口可以通过多种方式进行配置。最广为人知的方式是使用一个或多个 XML 文件；不过，我们也可以使用属性文件或 Java 类，甚至可以混合搭配不同的配置方法。通常，最好坚持使用单一方法，这样更容易理解和弄清你的配置是什么，也无需在 Java、XML 和属性文件之间来回查找。

Spring 提供了几种不同的 `ApplicationContext` 实现（参见图 2-3）。每种实现都提供相同的功能，但在加载应用上下文配置的方式上有所不同。图 2-3 还向我们展示了 `org.springframework.web.context.WebApplicationContext` 接口，它是 `ApplicationContext` 接口的一个专门版本，用于 Web 环境。

![Image](img/9781430241553_Fig02-03.jpg)

***图 2-3.** 各种 ApplicationContext 实现（简化版）*

如前所述，不同的实现具有不同的配置机制（即 XML 或 Java）。表 2-2 显示了默认配置选项，并指明了资源加载位置。

![Image](img/tab_2_2.jpg)

让我们来看一个基于 Java 的配置文件：`com.apress.prospringmvc.moneytransfer.annotation.ApplicationContextConfiguration` 类（参见清单 2-6）。该类中使用了两个注解：`org.springframework.context.annotation.Configuration` 和 `org.springframework.context.annotation.Bean`。前者将我们的类标记为配置文件，后者则指示该方法的返回值将用作创建 bean 的工厂。默认情况下，bean 的名称就是方法名。在清单 2-6 中，我们有三个 bean，分别命名为 `accountRepository`、`transactionRepository` 和 `moneyTransferService`。我们也可以通过设置 `Bean` 注解的 `name` 属性来显式指定 bean 名称。

***清单 2-6.** ApplicationContextConfiguration 配置文件*

`package com.apress.prospringmvc.moneytransfer.annotation;`

`import org.springframework.context.annotation.Bean;`
`import org.springframework.context.annotation.Configuration;`

`import com.apress.prospringmvc.moneytransfer.repository.AccountRepository;`
`import com.apress.prospringmvc.moneytransfer.repository.MapBasedAccountRepository;`
`import com.apress.prospringmvc.moneytransfer.repository.MapBasedTransactionRepository;`
`import com.apress.prospringmvc.moneytransfer.repository.TransactionRepository;`
`import com.apress.prospringmvc.moneytransfer.service.MoneyTransferService;`

**`@Configuration`**
`public class ApplicationContextConfiguration {`

**`    @Bean`**
`    public AccountRepository accountRepository() {`
`        return new MapBasedAccountRepository();`
`    }`

**`    @Bean`**
`    public TransactionRepository transactionRepository() {`
`        return new MapBasedTransactionRepository();`
`    }`

**`    @Bean`**
`    public MoneyTransferService moneyTransferService() {`
`        return new MoneyTransferServiceImpl();`
`    }`
`}`

![Image](img/square.jpg) **注意** 配置类可以是 `abstract` 的，但不能是 `final` 的。为了解析该类，Spring 会创建一个配置类的动态子类。

仅仅有一个带有 `Configuration` 注解的类是不够的，我们还需要一些东西来引导我们的应用上下文。我们用它来启动应用程序。在示例项目中，这由 `MoneyTransferSpring` 类负责（参见清单 2-7）。该类通过创建 `org.springframework.context.annotation.AnnotationConfigApplicationContext` 的实例，并将包含我们配置的类（参见清单 2-6）传递给它，从而引导我们的配置。对于基于 XML 的方法，过程类似，它会从类路径加载 XML 文件。两者的结果相同。我们同时包含了这两种方法，以突出基于 XML 和基于 Java 的配置之间的差异；然而，最终应用程序及其执行结果没有任何变化。

***清单 2-7.** MoneyTransferSpring 类*

`package com.apress.prospringmvc.moneytransfer.annotation;`

`import java.math.BigDecimal;`

`import org.slf4j.Logger;`
`import org.slf4j.LoggerFactory;`
`import org.springframework.context.ApplicationContext;`
`import org.springframework.context.annotation.AnnotationConfigApplicationContext;`
`import org.springframework.context.support.ClassPathXmlApplicationContext;`

`import com.apress.prospringmvc.ApplicationContextLogger;`
`import com.apress.prospringmvc.moneytransfer.domain.Transaction;`
`import com.apress.prospringmvc.moneytransfer.service.MoneyTransferService;`

`public class MoneyTransferSpring {`

`    private static final Logger logger = LoggerFactory.getLogger(MoneyTransferSpring.class);`

`public static void main(String[] args) {`

`        ApplicationContext ctx1 = new AnnotationConfigApplicationContext`![Image](img/U002.jpg)
`(ApplicationContextConfiguration.class);`
`        transfer(ctx1);`

`        ApplicationContext ctx2 = new ClassPathXmlApplicationContext(`
`                "/com/apress/prospringmvc/moneytransfer/annotation/application-context.xml");`
`        transfer(ctx2);`

`        ApplicationContextLogger.log(ctx1);`
`        ApplicationContextLogger.log(ctx2);`
`    }`

`    private static void transfer(ApplicationContext ctx) {`
`        MoneyTransferService service = ctx.getBean("moneyTransferService",`![Image](img/U002.jpg)
` MoneyTransferService.class);`
`        Transaction transaction = service.transfer("123456", "654321", new`![Image](img/U002.jpg)
` BigDecimal("250.00"));`
`        logger.info("转账完成: {}", transaction);`
`    }`
`}`

最后，请注意应用上下文可以形成层级结构。我们可以有一个应用上下文作为另一个上下文的父上下文（参见图 2-4）。一个应用上下文只能有一个父上下文，但可以有多个子上下文。子上下文可以访问父上下文中定义的 bean；但父上下文中的 bean 不能访问子上下文中的 bean。例如，如果我们在父上下文中启用了事务，这不会应用于子上下文（请参阅本章后面的“启用特性”部分）。

![Image](img/9781430241553_Fig02-04.jpg)

***图 2-4.** ApplicationContext 层级结构*

此功能允许我们将应用程序 bean（例如服务、仓库和基础设施）与 Web bean（例如请求处理器和视图）分离开来。这种分离非常有用。例如，假设多个 servlet 需要重用相同的应用程序 bean。我们无需为每个 servlet 重新创建它们，只需简单地重用已有的实例即可。例如，当一个 servlet 处理 Web UI，而另一个 servlet 处理 Web 服务时，就可能出现这种情况。



#### 资源加载

表 2-2 概述了不同的 `ApplicationContext` 实现及其默认的资源加载机制。但这并不意味着我们只能从默认位置加载资源。我们还可以通过添加适当的前缀，从特定位置加载资源（参见表 2-3）。

![图片](img/tab_2_3.jpg)

代码清单 2-7 展示了一个引导代码，它使用了默认从类路径加载资源的应用上下文实现。我们也可以使用默认从文件系统加载资源，但在使用适当前缀时从类路径加载资源的实现（参见代码清单 2-8）。

***代码清单 2-8.** 在 MoneyTransferSpringFileSystem 中使用前缀加载资源*

`package com.apress.prospringmvc.moneytransfer.annotation;`

`import org.springframework.context.support.FileSystemXmlApplicationContext;`

`// 其他导入已省略，参见代码清单 2-7`

`public class MoneyTransferSpringFileSystem {`

`private static final Logger` *`logger`* `= LoggerFactory.`*`getLogger`*![图片](img/U002.jpg)
`(MoneyTransferSpringFileSystem.class);`

`public static void main(String[] args) {`

`        ApplicationContext ctx1 = new AnnotationConfigApplicationContext(                                     ApplicationContextConfiguration.class);`
*`transfer`*`(ctx1);`

**`        ApplicationContext ctx2 = new FileSystemXmlApplicationContext(`**
**`                "classpath:/com/apress/prospringmvc/moneytransfer/annotation`**![图片](img/U002.jpg)
**`/application-context.xml");`**
*`transfer`*`(ctx2);`

`        ApplicationContextLogger.`*`log`*`(ctx1);`
`        ApplicationContextLogger.`*`log`*`(ctx2);`
`    }`
`  // transfer 方法已省略，参见代码清单 2-7`
`}`

除了能够指定从何处加载文件，我们还可以使用 Ant 风格的正则表达式来指定加载哪些文件。Ant 风格的正则表达式是一种包含 `**` 和/或 `*` 字符的资源位置。`*` 字符表示“当前层级”或“单个层级”，而 `**` 字符表示“当前层级及其所有子层级”。表 2-4 展示了一些示例。此技术仅适用于处理类路径或文件系统中的文件资源；不适用于 Web 资源或包名。

![图片](img/tab_2_4.jpg)

#### 组件扫描

Spring 还提供了一种称为*组件扫描*的功能。简而言之，此功能使 Spring 能够扫描类路径，查找带有 `org.springframework.stereotype.Component` 注解（或其专用注解，如 `org.springframework.stereotype.Service`、`org.springframework.stereotype.Repository`、`org.springframework.stereotype.Controller` 或 `org.springframework.context.annotation.Configuration`）的类。如果我们想启用组件扫描，需要指示应用上下文执行此操作。`org.springframework.context.annotation.ComponentScan` 注解使我们能够实现这一点。该注解需要放在配置类上以启用组件扫描。代码清单 2-9 展示了修改后的配置类。

***代码清单 2-9.** 使用 ApplicationContextConfiguration 实现组件扫描*

`package com.apress.prospringmvc.moneytransfer.scanning;`

`import org.springframework.context.annotation.ComponentScan;`
`import org.springframework.context.annotation.Configuration;`

`@Configuration`
**`@ComponentScan(basePackages = {`**
**`        "com.apress.prospringmvc.moneytransfer.scanning",`**
**`        "com.apress.prospringmvc.moneytransfer.repository" })`**
`public class ApplicationContextConfiguration {}`

查看代码清单 2-9 可以发现，该类没有更多内容，只有两个注解。一个注解表示该类用于配置，另一个注解则启用组件扫描。组件扫描注解配置了一个要扫描的包。

![图片](img/square.jpg) **注意** 不指定包或使用过于宽泛的包（如 com.apress）来扫描整个类路径被认为是不良实践。这可能导致扫描大部分甚至所有类，从而严重影响应用程序的启动时间。

如果我们使用了组件扫描，并且找到了带有 `Configuration` 注解的类，那么该类将被用于向上下文中添加更多 Bean。通常，这些 Bean 是无法自动创建的，例如数据源或 JMS 配置。

#### 作用域

默认情况下，Spring 应用上下文中的所有 Bean 都是*单例*的。顾名思义，每个 Bean 只有一个实例，并用于整个应用程序。这通常不会带来问题，因为我们的服务和仓库不持有状态；它们只是执行特定操作并（可选地）返回一个值。

然而，如果我们想在 Bean 内部保持状态，单例就会带来问题。我们正在开发一个希望吸引成千上万用户的 Web 应用程序。如果只有一个 Bean 实例，并且所有这些用户都在操作同一个实例，那么用户将看到并修改彼此的数据，或来自多个用户组合的数据。显然，这不是我们想要的。幸运的是，Spring 为 Bean 提供了多种作用域，我们可以利用这些作用域（参见表 2-5）。

![图片](img/tab_2_5.jpg)



#### 配置文件（Profiles）

Spring 在 3.1 版本中引入了配置文件（profiles）的概念。配置文件使得为不同环境创建不同的应用程序配置变得简单。例如，我们可以为本地环境、测试环境以及部署到 CloudFoundry 的环境创建独立的配置文件。这些环境中的每一个都需要一些特定于环境的配置或 Bean。可以想到数据库配置、消息传递解决方案，以及针对测试环境的某些 Bean 的桩（stubs）。

要启用一个配置文件，我们需要告诉应用程序上下文哪些配置文件是激活的。要激活特定的配置文件，我们需要设置一个名为 `spring.profiles.active` 的系统属性（在 Web 环境中，这可以是一个 Servlet 初始化参数或 Web 上下文参数）。这是一个逗号分隔的字符串，包含激活的配置文件的名称。如果我们现在添加一些（此处为静态内部）类（参见清单 2-10），并为其添加 `org.springframework.context.annotation.Configuration` 和 `org.springframework.context.annotation.Profile` 注解，那么只有与某个激活的配置文件相匹配的类才会被处理。所有其他类都将被忽略。

***清单 2-10.** 带有配置文件的 ApplicationContextConfiguration*

`package com.apress.prospringmvc.moneytransfer.annotation.profiles;`

`import org.springframework.context.annotation.Bean;`
`import org.springframework.context.annotation.Configuration;`
`import org.springframework.context.annotation.Profile;`

`import com.apress.prospringmvc.moneytransfer.annotation.MoneyTransferServiceImpl;`
`import com.apress.prospringmvc.moneytransfer.repository.AccountRepository;`
`import com.apress.prospringmvc.moneytransfer.repository.MapBasedAccountRepository;`
`import com.apress.prospringmvc.moneytransfer.repository.MapBasedTransactionRepository;`
`import com.apress.prospringmvc.moneytransfer.repository.TransactionRepository;`
`import com.apress.prospringmvc.moneytransfer.service.MoneyTransferService;`

`@Configuration`
`public class ApplicationContextConfiguration {`

`    @Bean`
`    public AccountRepository accountRepository() {`
`        return new MapBasedAccountRepository();`
`    }`

`    @Bean`
`    public MoneyTransferService moneyTransferService() {`
`        return new MoneyTransferServiceImpl();`
`    }`

**`    @Configuration`**
**`    @Profile(value = "test")`**
**`    public static class TestContextConfiguration {`**
**`        @Bean`**
**`        public TransactionRepository transactionRepository() {`**
**`            return new StubTransactionRepository();`**
**`        }`**
**`    }`**

**`    @Configuration`**
**`    @Profile(value = "local")`**
**`    public static class LocalContextConfiguration {`**

**`        @Bean`**
**`        public TransactionRepository transactionRepository() {`**
**`            return new MapBasedTransactionRepository();`**
**`        }`**
**`    }`**
`}`

清单 2-11 展示了一些示例引导代码。通常，我们不会在引导代码中设置激活的配置文件。相反，我们会使用系统变量的组合来设置我们的环境。这使我们能够保持应用程序不变，同时仍然可以灵活地更改运行时配置。

***清单 2-11.** 带有配置文件的 MoneyTransferSpring*

`package com.apress.prospringmvc.moneytransfer.annotation.profiles;`

`import java.math.BigDecimal;`

`import org.slf4j.Logger;`
`import org.slf4j.LoggerFactory;`
`import org.springframework.context.ApplicationContext;`
`import org.springframework.context.annotation.AnnotationConfigApplicationContext;`
`import org.springframework.context.support.ClassPathXmlApplicationContext;`

`import com.apress.prospringmvc.ApplicationContextLogger;`
`import com.apress.prospringmvc.moneytransfer.domain.Transaction;`
`import com.apress.prospringmvc.moneytransfer.service.MoneyTransferService;`

`public class MoneyTransferSpring {`

`private static final Logger` *`logger`* `= LoggerFactory.`*`getLogger`*`(MoneyTransferSpring.class);`

`    public static void main(String[] args) {`

**`        System.setProperty("spring.profiles.active", "test");`**

`        AnnotationConfigApplicationContext ctx1 = new AnnotationConfigApplicationContext(`
`                ApplicationContextConfiguration.class);`
*`transfer`*`(ctx1);`
`        ApplicationContextLogger.`*`log`*`(ctx1);`

`        ApplicationContext ctx2 = new ClassPathXmlApplicationContext(`
`                "/com/apress/prospringmvc/moneytransfer/annotation/profiles`![Image](img/U002.jpg)
`/application-context.xml");`
*`transfer`*`(ctx2);`

`        ApplicationContextLogger.`*`log`*`(ctx2);`

**`        System.setProperty("spring.profiles.active", "local");`**
`        AnnotationConfigApplicationContext ctx3 = new AnnotationConfigApplicationContext(`
`                ApplicationContextConfiguration.class);`
*`transfer`*`(ctx3);`
`        ApplicationContextLogger.`*`log`*`(ctx3);`

`        ApplicationContext ctx4 = new ClassPathXmlApplicationContext(`
`                "/com/apress/prospringmvc/moneytransfer/annotation/profiles`![Image](img/U002.jpg)
`/application-context.xml");`
*`transfer`*`(ctx4);`
`        ApplicationContextLogger.`*`log`*`(ctx4);`

`    }`

`    private static void transfer(ApplicationContext ctx) {`
`        MoneyTransferService service = ctx.getBean("moneyTransferService",`![Image](img/U002.jpg)
` MoneyTransferService.class);`
`        Transaction transaction = service.transfer("123456", "654321", new`![Image](img/U002.jpg)
` BigDecimal("250.00"));`
*`logger`*`.info("Money Transfered: {}", transaction);`
`    }`
`}`

你可能会想，我们到底为什么要使用配置文件。一个原因是它允许灵活的配置。这意味着我们的整个配置都处于版本控制之下，并且位于同一个源代码中，而不是分散在不同的服务器、工作站等地方。当然，我们仍然可以加载包含某些属性（如用户名和密码）的额外文件。如果公司的安全策略不允许我们将这些属性放入版本控制，这会很有用。在介绍测试和部署到云时，我们将广泛使用配置文件，因为这两项任务需要为数据源提供不同的配置。

#### 启用特性

Spring 框架提供的灵活性远不止依赖注入；它还提供了许多我们可以启用的不同特性。我们可以使用注解来启用这些特性（参见表 2-6）。请注意，我们不会使用此表中提到的所有注解；但是，我们的示例应用程序将使用事务，并且我们将使用一些 AOP。本书的大部分内容是关于 `org.springframework.web.servlet.config.annotation.EnableWebMvc` 注解所提供的特性。

![Image](img/tab_2_6.jpg)

![Image](img/tab_2_6a.jpg)

![Image](img/square.jpg) **注意**  有关这些特性的更多信息，我们建议您查阅不同注解的 Java 文档，以及专门的参考指南章节。



#### 面向切面编程

为了启用表 2-4 中列出的特性，Spring 使用了面向切面编程（AOP）。AOP 是另一种思考软件结构的方式。它使你能够将事务管理或性能日志记录等跨越多种类型和对象的功能（横切关注点）模块化。在 AOP 中，有几个重要的概念需要牢记（参见表 2-7）。

![图片](img/tab_2_7.jpg)

现在让我们来看看事务管理，以及 Spring 如何使用 AOP 在方法周围应用事务。事务通知或拦截器是 `org.springframework.transaction.interceptor.TransactionInterceptor`。该通知被放置在带有 `org.springframework.transaction.annotation.Transactional` 注解的方法周围。为此，Spring 会在实际对象周围创建一个包装器，这被称为*代理*（参见图 2-5）。代理就像一个封装对象，但它允许添加（动态）行为（在此例中为方法的事务性）。

![图片](img/9781430241553_Fig02-05.jpg)

***图 2-5.** 代理方法调用*

`org.springframework.transaction.annotation.EnableTransactionManagement` 注解会注册包含切入点（作用于 `org.springframework.transaction.annotation.Transactional` 注解）的 Bean。此时，拦截器已准备好供我们使用。其他用于启用特性的注解工作方式类似；它们注册 Bean 以启用所需特性，包括大多数特性所需的 AOP（以及由此产生的代理创建）。

### Web 应用程序

那么，上述所有技术如何应用于 Web 应用程序呢？例如，应用程序上下文扮演什么角色？还有所有其他提到的东西呢？

在开发 Web 应用程序时，我们拥有实际的业务逻辑（例如，我们的服务、仓库和基础设施信息），以及基于 Web 的 Bean。这些东西应该被分离，因此我们需要拥有多个应用程序上下文，并在它们之间建立关系。

我们还需要引导应用程序的代码，否则什么都不会发生。在本章的示例中，我们使用了一个带有 `main` 方法的 `MoneyTransferSpring` 类来启动应用程序上下文。这在 Web 环境中是无法做到的。Spring 提供了两个可以引导应用程序的组件：`org.springframework.web.servlet.DispatcherServlet` 和 `org.springframework.web.context.ContextLoaderListener`。这两个组件都会引导和配置一个应用程序上下文。

让我们看一下配置 `DispatcherServlet` 的类。这是 `com.apress.prospringmvc.bookstore.web.BookstoreWebApplicationInitializer` 类（参见清单 2-12）。这个类会被我们的 Servlet 3.0 容器检测到，并用于初始化我们的应用程序（有关此主题的更多信息，请参见第 3 章）。我们创建 `DispatcherServlet` 并将 `org.springframework.web.context.support.AnnotationConfigWebApplicationContext` 传递给它。接下来，我们将 Servlet 映射到所有内容（“/”），并告诉它在启动时加载。

***清单 2-12.** BookstoreWebApplicationInitializer 类*

`package com.apress.prospringmvc.bookstore.web;`

`import javax.servlet.ServletContext;`
`import javax.servlet.ServletException;`
`import javax.servlet.ServletRegistration;`

`import org.springframework.web.WebApplicationInitializer;`
`import org.springframework.web.context.WebApplicationContext;`
`import org.springframework.web.context.support.AnnotationConfigWebApplicationContext;`
`import org.springframework.web.servlet.DispatcherServlet;`

`import com.apress.prospringmvc.bookstore.web.config.WebMvcContextConfiguration;`

`public class BookstoreWebApplicationInitializer implements WebApplicationInitializer {`

`    @Override`
`    public void onStartup(final ServletContext servletContext) throws ServletException {`
`        registerDispatcherServlet(servletContext);`
`    }`

**`    private void registerDispatcherServlet(final ServletContext servletContext) {`**
**`        WebApplicationContext dispatcherContext = createContext`![图片](img/U002.jpg)**
**`(WebMvcContextConfiguration.class);`**
**`        DispatcherServlet dispatcherServlet = new DispatcherServlet(dispatcherContext);`**
**`        ServletRegistration.Dynamic dispatcher = servletContext.addServlet`![图片](img/U002.jpg)**
**`("dispatcher", dispatcherServlet);`**
**`        dispatcher.setLoadOnStartup(1);`**
**`        dispatcher.addMapping("/");`**
**`    }`**

`private WebApplicationContext createContext(final Class<?>... annotatedClasses) {`
`        AnnotationConfigWebApplicationContext context = new`![图片](img/U002.jpg)
` AnnotationConfigWebApplicationContext();`
`        context.register(annotatedClasses);`
`        context.getEnvironment().setActiveProfiles("local");`
`        return context;`
`    }`
`}`

让我们通过添加一个 `ContextLoaderListener` 类来让事情变得更有趣一些，这样我们就可以拥有一个父上下文和一个子上下文（参见清单 2-13）。新注册的监听器将使用 `com.apress.prospringmvc.bookstore.config.InfrastructureContextConfiguration`（参见清单 2-14）来确定要加载哪些 Bean。已经配置好的 `DispatcherServlet` 会自动检测由 `ContextLoaderListener` 加载的应用程序上下文。

***清单 2-13.** 对 BookstoreWebApplicationInitializer 类的修改*

`package com.apress.prospringmvc.bookstore.web;`



`import org.springframework.web.context.ContextLoaderListener;`
`import com.apress.prospringmvc.bookstore.config.InfrastructureContextConfiguration;`

`// 其他导入已省略，详见清单 2-12`

`public class BookstoreWebApplicationInitializer implements WebApplicationInitializer {`

`    @Override`
`    public void onStartup(final ServletContext servletContext) throws ServletException {`
**`        registerListener(servletContext);`**
`        registerDispatcherServlet(servletContext);`
`    }`

`// registerDispatcherServlet 方法已省略，详见清单 2-12`

`    private void registerListener(final ServletContext servletContext) {`
`        AnnotationConfigWebApplicationContext rootContext =`![Image](img/U002.jpg)
` createContext(InfrastructureContextConfiguration.class);`
`        servletContext.addListener(new ContextLoaderListener(rootContext));`
`    }`

`private AnnotationConfigWebApplicationContext createContext(final Class<?>...`![Image](img/U002.jpg)
` annotatedClasses) {`
`        AnnotationConfigWebApplicationContext context = new`![Image](img/U002.jpg)
` AnnotationConfigWebApplicationContext();`
`        context.getEnvironment().setActiveProfiles("local");`
`        context.register(annotatedClasses);`
`        return context;`
`    }`

`}`

清单 2-14 是我们的主应用上下文。它包含了服务和仓库的配置。该清单还展示了我们的 JPA 实体管理器，包括其基于注解的事务支持。

***清单 2-14.** InfrastructureContextConfiguration 源文件*

`package com.apress.prospringmvc.bookstore.config;`

`import javax.persistence.EntityManagerFactory;`
`import javax.sql.DataSource;`

`import org.springframework.beans.factory.FactoryBean;`
`import org.springframework.beans.factory.annotation.Autowired;`
`import org.springframework.context.annotation.Bean;`
`import org.springframework.context.annotation.ComponentScan;`
`import org.springframework.context.annotation.Configuration;`
`import org.springframework.jdbc.datasource.embedded.EmbeddedDatabaseBuilder;`
`import org.springframework.jdbc.datasource.embedded.EmbeddedDatabaseType;`
`import org.springframework.orm.jpa.JpaTransactionManager;`
`import org.springframework.orm.jpa.JpaVendorAdapter;`
`import org.springframework.orm.jpa.LocalContainerEntityManagerFactoryBean;`
`import org.springframework.orm.jpa.vendor.HibernateJpaVendorAdapter;`
`import org.springframework.transaction.PlatformTransactionManager;`
`import org.springframework.transaction.annotation.EnableTransactionManagement;`

`@Configuration`
`@EnableTransactionManagement`
`@ComponentScan(basePackages = {       "com.apress.prospringmvc.bookstore.service",`
`        "com.apress.prospringmvc.bookstore.repository",         "com.apress.prospringmvc.bookstore.domain.support" })`
`public class InfrastructureContextConfiguration {`

`    @Autowired`
`    private DataSource dataSource;`

`    @Autowired`
`    private EntityManagerFactory entityManagerFactory;`

`    @Bean`
`    public FactoryBean<EntityManagerFactory> entityManagerFactory() {`
`        LocalContainerEntityManagerFactoryBean emfb;         emfb = new LocalContainerEntityManagerFactoryBean();`
`        emfb.setDataSource(this.dataSource);`
`        emfb.setJpaVendorAdapter(jpaVendorAdapter());`
`        return emfb;`
`    }`

`    @Bean`
`    public JpaVendorAdapter jpaVendorAdapter() {`
`        return new HibernateJpaVendorAdapter();`
`    }`

`    @Bean`
`    public PlatformTransactionManager transactionManager() {`
`        JpaTransactionManager txManager = new JpaTransactionManager();`
`        txManager.setEntityManagerFactory(this.entityManagerFactory);`
`        txManager.setDataSource(this.dataSource);`
`        return txManager;`
`    }`

`    @Bean`
`    public DataSource dataSource() {`
`        EmbeddedDatabaseBuilder builder = new EmbeddedDatabaseBuilder();`
`        builder.setType(EmbeddedDatabaseType.H2);`
`        return builder.build();`
`    }`
`}`

### 本章小结

在本章中，我们介绍了 Spring Core 的基础知识。我们回顾了依赖注入的概念，并简要介绍了三种不同的依赖注入方式。此外，我们还涵盖了基于构造器、基于 Setter 和基于注解的依赖注入。

接着，我们进入了 Spring 的世界，深入研究了 `org.springframework.context.ApplicationContext`，包括它在应用程序中所扮演的角色。我们还解释了不同类型的应用上下文（例如，基于 XML 或 Java 的），以及它们各自的资源加载方式。在我们的 Web 环境中，我们通过实现 `org.springframework.web.context.WebApplicationContext` 接口，使用了一种专门的应用上下文版本。我们还介绍了默认情况下，应用上下文中的 Bean 是如何以单例作用域存在的。幸运的是，Spring 为我们提供了额外的作用域，例如 request、session、globalSession、prototype、application 和 thread。

为了能够在不同环境中使用不同的配置，Spring 还提供了 Profile。我们简要说明了如何启用 Profile 以及如何使用它们。在测试示例应用程序（见第 9 章）以及将其部署到 Cloud Foundry（见附录 A）时，我们将使用 Profile。

我们还深入探讨了 Spring 需要哪些启用注解来开启特定功能。这些注解会在应用上下文中注册额外的 Bean，从而启用所需的功能。这些功能大多依赖于 AOP 的启用（例如，实现声明式事务管理）。Spring 会创建代理，将 AOP 应用于注册在应用上下文中的 Bean。

在下一章中，我们将探讨 MVC Web 应用程序的架构、不同的层次，以及它们在应用程序中所扮演的角色。

## 第 3 章

## Web 应用程序架构

在我们开始探索 Spring MVC 的内部机制之前，首先需要理解 Web 应用程序的不同层次。我们将从简要介绍 MVC 模式开始，包括它是什么以及为什么应该使用它。我们还将介绍 Spring 框架提供的一些接口和类，用于表达 MVC 模式的不同部分。

在回顾了 MVC 模式之后，我们将逐一介绍 Web 应用程序中的不同层次，并了解每个层次在应用程序中扮演的角色。我们还将探讨 Spring 框架如何在各个层次中为我们提供帮助，以及如何利用它来发挥我们的优势。


### MVC 模式

模型-视图-控制器模式（MVC 模式）最早由 Trygve Reenskaug 在施乐公司从事 Smalltalk 开发时提出。当时，该模式主要面向桌面应用程序。该模式将表示层划分为不同类型的组件，每个组件都有其自身的职责。视图利用模型来渲染自身。基于用户的操作，视图会触发控制器，控制器进而更新模型。然后模型会通知视图进行（重新）渲染（参见图 3-1）。

![Image](img/9781430241553_Fig03-01.jpg)

***图 3-1.** MVC 模式*

MVC 模式的核心在于关注点分离。正如我们之前提到的，每个组件都有其特定的角色（参见表 3-1）。关注点分离在表示层中非常重要，因为它有助于我们保持各个组件的清晰整洁。这样，我们就不会让实际的视图承担业务逻辑、导航逻辑和模型数据的负担。遵循这种方法可以使一切保持良好分离，从而使我们的应用程序更易于维护和测试。

![Image](img/tab_3_1.jpg)

MVC 模式的经典实现（如图 3-1 所示）涉及用户触发一个操作。这会促使控制器更新模型，模型进而将更改推送给视图。然后视图使用来自模型的更新数据进行自我更新。这是 MVC 模式的理想实现，例如，它在基于 Swing 的桌面应用程序中运行良好。然而，由于 HTTP 协议的特性，这种方法在 Web 环境中并不可行。对于 Web 应用程序，用户通常通过发出请求来启动操作。这会促使应用程序更新并渲染视图，然后将视图发送回用户。这意味着我们在 Web 环境中需要一种略有不同的方法。我们不是将更改推送给视图，而是需要从服务器拉取更改。

这种方法看起来相当可行，但在 Web 应用程序中应用起来并不像人们想象的那么直接。Web（或 HTTP）本质上是无状态的，因此保持模型的存在可能相当困难。对于 Web 而言，MVC 模式被实现为 *Model 2* 架构（参见图 3-2）^(1)。原始模式（*Model 1* 如图 3-1 所示）与修改后模式的区别在于，后者引入了一个*前端控制器*，用于将传入的请求分派给其他控制器。这些控制器处理传入的请求，返回模型，并选择视图。

____________

¹ [`http://java.sun.com/blueprints/guidelines/designing_enterprise_applications_2e/web-tier/webtier5.html`](http://java.sun.com/blueprints/guidelines/designing_enterprise_applications_2e/web-tier/webtier5.html)

![Image](img/9781430241553_Fig03-02.jpg)

***图 3-2.** Model 2 MVC 模式*

前端控制器是处理传入请求的组件。首先，它将请求委托给一个合适的控制器。当该控制器完成处理并更新模型后，前端控制器将根据结果决定渲染哪个视图。在大多数情况下，这个前端控制器被实现为一个 `javax.servlet.Servlet` servlet（例如，Struts 中的 `ActionServlet` 或 JSF 中的 `FacesServlet`）。在 Spring MVC 中，这个前端控制器是 `org.springframework.web.servlet.DispatcherServlet`。

### 应用程序分层

在引言中，我们提到一个应用程序由多个层组成（参见图 4-3）。我们倾向于将层视为应用程序的一个关注领域。因此，我们也使用分层来实现关注点分离。例如，视图不应该承担业务逻辑或数据访问逻辑，因为这些是不同的关注点，通常位于不同的层中。

层应被视为概念上的边界，但它们不必在物理上相互隔离（在不同的虚拟机中）。对于 Web 应用程序，这些层通常运行在同一个虚拟机内。Rod Johnson 的著作 *Expert One-on-One J2EE Design and Development*（Wrox, 2002）对应用程序的分布和扩展进行了很好的讨论。

![Image](img/9781430241553_Fig03-03.jpg)

***图 3-3.** 典型的应用程序分层*

图 3-3 是 Spring MVC 应用程序各层的高度概括视图。这种分层在许多应用程序中都能看到。数据访问位于应用程序的底部，表示层位于顶部，而服务层（实际的业务逻辑）位于中间。在本章中，我们将探讨这种架构以及一切是如何组织的。表 3-2 提供了对不同层的简要描述。

![Image](img/tab_3_2.jpg)

层之间的通信是自上而下的。服务层可以访问数据访问层，但数据访问层不能访问服务层。如果你发现应用程序中出现了这种循环依赖，请退后几步，重新考虑你的设计。循环依赖（或自下而上的依赖）几乎总是设计不良的标志，会导致复杂性增加，并使应用程序更难维护。

![Image](img/square.jpg) **注意** 有时，人们也可能遇到术语 *tier*。许多人将 tier 和 layer 互换使用；然而，在讨论应用程序架构或其部署时，区分这两个术语会有所帮助。我们倾向于使用 *layer* 来表示应用程序中的概念层，而 *tier* 则表示在部署时各层在不同机器上的物理分离。从 *layers* 的角度思考有助于软件开发人员，而从 *tiers* 的角度思考则有助于系统管理员。

尽管图 3-3 给出了 Web 应用程序各层的总体概览，但我们还可以进一步细分。在一个典型的 Web 应用程序中，我们可以识别出五个概念层（参见图 3-4）。我们可以将表示层拆分为 Web 层和用户界面层，但应用程序还包括一个领域层（请参阅本章后面的“Spring MVC 应用程序层”部分）。通常，领域层横切所有层，因为它从数据访问层到用户界面层都被广泛使用。

![Image](img/9781430241553_Fig03-04.jpg)

***图 3-4.** Web MVC 应用程序层*

![Image](img/square.jpg) **注意** 分层架构并非唯一的应用程序架构；然而，它是 Web 应用程序中最常遇到的架构。

如果我们查看示例应用程序，图 3-4 中所示的架构在包结构中得到了明确体现。这些包可以在 *bookstore-shared* 项目中找到（参见图 3-5）。主要的包包括：

*   `com.apress.prospringmvc.bookstore.domain`：领域层
*   `com.apress.prospringmvc.bookstore.service`：服务层
*   `com.apress.prospringmvc.bookstore.repository`：数据访问层

其他包是 Web 层的支持包，而 `com.apress.prospringmvc.bookstore.config` 包包含根应用程序上下文的配置类。用户界面层和 Web 层将在本书中介绍，这些层将位于 `com.apress.prospringmvc.bookstore.web` 包中，并在需要时以 Java Server Pages 的形式存在于用户界面中。

![Image](img/9781430241553_Fig03-05.jpg)

***图 3-5.** Bookstore 包结构概览*



### 关注点分离

正如我们在第 2 章中提到的，清晰的关注点分离非常重要。观察图 3-4 中的架构，可以看到关注点分离体现在各个层级中。将关注点分离到不同层级有助于我们实现清晰的设计，以及灵活且可测试的应用程序。

创建或识别层级可能比较困难。一个经验法则是：如果一个层级与其他层级有过多依赖关系，我们可能需要引入一个包含所有这些依赖关系的新层级。另一方面，如果我们在不同层级中看到同一个层级，我们可能需要重新考虑这个层级，并将其作为应用程序的一个切面。在这种情况下，我们可以使用 Spring 框架的 AOP 功能在运行时应用这些切面（参见第 2 章）。

层级之间的耦合——例如，服务层需要与数据访问层通信——是通过定义清晰的接口来实现的。定义接口并面向接口编程可以减少对具体实现的实际耦合。这种降低的耦合度和复杂度使得应用程序更易于测试和维护。使用接口的另一个好处是，Spring 可以使用 JDK 动态代理^(2)来创建代理并应用 AOP。

![图片](img/square.jpg) **注意** Spring 也可以对基于类的代理应用 AOP；但这需要在类路径中包含`cglib`库（[`http://cglib.sourceforge.net`](http://cglib.sourceforge.net)）。

关键在于：应用程序中的分层有助于实现更易于维护和测试的应用程序。清晰的关注点分离也能带来良好的应用程序架构。

### Spring MVC 应用程序层级

您可能想知道所有层级如何融入 Spring MVC 应用程序，以及这些不同的层级如何帮助我们构建 Spring MVC 应用程序。在本节中，我们将探讨图 3-4 中描绘的五个层级。我们将特别关注不同层级所扮演的角色以及每个层级应包含的内容。

#### 领域层

领域是应用程序中最重要的层级。它是我们正在解决的业务问题的代码表示，并包含我们领域的业务规则。这些规则可能会检查我们是否有足够的资金从账户中转账，或者确保字段是唯一的（例如，我们系统中的用户名）。

一种流行的确定领域模型的技术是使用用例描述中的名词作为领域对象（例如，`Account`或`Transaction`）。这些对象既包含状态（例如，`Account`的`username`），也包含行为（例如，`Account`上的`credit`方法）。这些方法通常比服务层中的方法粒度更细。例如，在第 2 章的转账示例中，`com.apress.prospringmvc.moneytransfer.domain.Account`对象具有`debit`和`credit`方法。`credit`方法包含一些业务逻辑，用于检查我们账户中是否有足够的资金来转账。

____________

² [`http://docs.oracle.com/javase/6/docs/technotes/guides/reflection/proxy.html`](http://docs.oracle.com/javase/6/docs/technotes/guides/reflection/proxy.html)

在第 2 章中，`com.apress.prospringmvc.moneytransfer.service.MoneyTransferService`的实现使用了这些辅助方法来实现一个用例（在示例中，它将资金从一个账户转移到另一个账户）。这不应与贫血领域模型^(3)混淆，在贫血领域模型中，我们的领域对象只持有状态而没有行为。

通常，您的领域模型不需要注入依赖；但这样做仍然是可能的。例如，可以使用 Spring 框架和 AspectJ 在我们的领域对象中启用依赖注入。在这种情况下，我们需要为领域类添加`org.springframework.beans.factory.annotation.Configurable`注解。接下来，我们需要设置加载时织入或编译时织入，然后依赖就会被注入。有关此主题的更多详细信息，请参阅 Spring 参考指南^(4)。

![图片](img/square.jpg) **注意** 这是 Spring ROO^(5)中使用的设置。它采用了 Eric Evans^(6)所倡导的*富领域模型*。

#### 用户界面层

用户界面层向用户呈现应用程序。该层将服务器生成的响应转换为用户客户端请求的类型。例如，Web 浏览器可能会请求 HTML 文档，Web 服务可能需要 XML 文档，而其他客户端可能请求 PDF 或 Excel 文档。

我们将表示层分为用户界面层和 Web 层，因为尽管存在多种不同的视图技术，但我们希望尽可能多地重用代码。我们的目标是只重新实现用户界面。目前有许多不同的视图技术，包括 JSP(X)、JSF、Velocity 和 Freemarker 等。在理想情况下，我们应该能够在不更改应用程序后端的情况下切换用户界面。

Spring MVC 帮助我们实现用户界面与系统其他部分的隔离。在 Spring 中，视图由一个接口表示：`org.springframework.web.servlet.View`。该接口负责将用户操作的结果（模型）转换为用户请求的响应类型。`View`接口是通用的，不依赖于特定的视图技术。对于每种支持的视图技术，Spring 框架本身或视图技术本身都会提供相应的实现。开箱即用，Spring 支持以下视图技术：

*   JSP
*   PDF
*   Excel

____________

³ [`http://martinfowler.com/bliki/AnemicDomainModel.html`](http://martinfowler.com/bliki/AnemicDomainModel.html)

⁴ [`http://static.springsource.org/spring/docs/3.1.x/spring-frameworkreference/html/aop.html#aop-atconfigurable`](http://static.springsource.org/spring/docs/3.1.x/spring-frameworkreference/html/aop.html#aop-atconfigurable)

⁵ [`http://www.springsource.org/spring-roo`](http://www.springsource.org/spring-roo)

⁶ [`http://domaindrivendesign.org/`](http://domaindrivendesign.org/)

*   Freemarker
*   Velocity
*   JasperReports
*   Tiles 2
*   XML（编组、XSLT 或纯文本）
*   JSON

用户界面通常依赖于领域层。有时，直接暴露和渲染领域模型会很方便。当我们开始在应用程序中使用表单时，这一点尤其有用。例如，这让我们可以直接处理领域对象，而不是增加一个额外的间接层。有些人认为这会在层之间造成不必要或不希望的耦合。然而，仅仅为了将领域与视图解耦而创建另一个层会导致不必要的复杂性和重复。无论如何，重要的是要记住，Spring MVC 并不要求我们直接将领域模型暴露给视图——是否这样做完全取决于我们自己。



#### Web 层

Web 层有两项职责。第一项职责是引导用户在 Web 应用程序中进行操作。第二项职责是作为服务层与 HTTP 之间的集成层。

引导用户在网站中导航，可以简单到将 URL 映射到视图，也可以复杂到像 Spring Web Flow 那样提供完整的页面流解决方案。导航通常仅绑定在 Web 层，领域层或服务层中不应包含任何导航逻辑。

作为集成层，Web 层应尽可能精简。它应该负责将传入的 HTTP 请求转换为服务层能够处理的内容，然后将服务层返回的结果（如果有）转换为用户界面的响应。Web 层不应包含任何业务逻辑——那是服务层的唯一职责。

Web 层还包含 Cookie、HTTP 头，以及可能的 HTTP 会话。Web 层有责任以一致且透明的方式管理所有这些内容。不同的 HTTP 元素绝不应渗透到我们的服务层中。如果渗透进去，整个服务层（以及我们的应用程序）就会与 Web 环境绑定。这样做会使应用程序更难以维护和测试。保持服务层的纯净，也使我们能够为不同的渠道复用相同的服务。例如，它使我们能够添加 Web 服务或基于 JMS 的解决方案。Web 层应被视为连接服务层并将其暴露给最终用户的客户端或代理。

在 Java Web 开发的早期，这一层主要由 Servlet 或 Java Server Pages 实现。Servlet 负责处理请求并将其转换为服务层能够理解的内容。通常情况下，Servlet 会直接将所需的 HTML 写回客户端。这种实现方式很快就变得难以维护和测试。几年后，Model 2 MVC 模式出现，我们终于拥有了先进的 Web MVC 能力。

像 Spring MVC、Struts、JSF 和 Tapestry 这样的框架为这种模式提供了不同的实现，它们的工作方式也大相径庭。然而，我们可以识别出两种主要的 Web 层实现类型：请求/响应框架（例如 Struts 和 Spring MVC）和基于组件的框架（例如 JSF 和 Tapestry）。请求/响应框架操作于 `javax.servlet.ServletRequest` 和 `javax.servlet.ServletResponse` 对象。因此，它们操作 Servlet API 这一事实并未真正对用户隐藏。基于组件的框架则提供了一种完全不同的编程模型。它们试图对程序员隐藏 Servlet API，并提供一种基于组件的编程模型。使用基于组件的框架感觉很像在使用 Swing 桌面应用程序。

两种方法各有优缺点。Spring MVC 相当强大，并且在两者之间取得了良好的平衡。它可以隐藏用户正在使用 Servlet API 这一事实；然而，在需要时，访问该 API（以及其他内容）也非常容易。

Web 层依赖于领域层和服务层。在大多数情况下，您需要将传入的请求转换为领域对象，并调用服务层的方法对该领域对象执行某些操作（例如，更新客户或创建订单）。Spring MVC 可以轻松地将传入的请求映射到对象，并且我们可以使用依赖注入来访问服务层。

在 Spring MVC 中，Web 层由 `org.springframework.web.servlet.mvc.Controller` 接口或带有 `org.springframework.stereotype.Controller` 注解的类表示。基于接口的方法历史悠久，自 Spring 框架诞生以来就一直存在；然而，它现在被认为已经过时了。尽管如此，它对于简单的用例仍然有用，并且 Spring 提供了一些开箱即用的便捷实现。新的基于注解的方法比原始的基于接口的方法更强大、更灵活。本书主要关注基于注解的方法。

在执行控制器之后，基础设施（有关此主题的更多信息，请参见第 4 章）期望存在一个 `org.springframework.web.servlet.ModelAndView` 类的实例。该类包含了模型（以 `org.springframework.ui.ModelMap` 的形式）和要渲染的视图。这个视图可以是一个实际的 `org.springframework.web.servlet.View` 实现，也可以是一个视图的名称。

![Image](img/square.jpg) **注意** 不要在实现了 `Controller` 接口的类上使用 `@Controller` 注解。这两者的处理方式不同，混合使用这两种策略可能会导致意想不到且不期望的结果！



#### 服务层

服务层在应用程序架构中是一个非常重要的层次。它可以被视为我们应用的核心，因为它将系统的功能（即用例）暴露给用户。它通过提供一个粗粒度的 API（如表 3-2 所述）来实现这一点。代码清单 3-1 描述了一个粗粒度的服务接口。

***代码清单 3-1.** 一个粗粒度的服务接口*

`package com.apress.prospringmvc.bookstore.service;`

`import com.apress.prospringmvc.bookstore.domain.Account;`

`public interface AccountService {`

`    Account save(Account account);`

`    Account login(String username, String password) throws AuthenticationException;`

`    Account getAccount(String username);`
`}`

该代码清单被认为是粗粒度的，因为它只需要客户端进行一次简单的方法调用即可完成一个用例。这与代码清单 3-2（细粒度的服务方法）中的代码形成对比，后者需要多次调用才能执行一个用例。

***代码清单 3-2.** 一个细粒度的服务接口*

`package com.apress.prospringmvc.bookstore.service;`

`import com.apress.prospringmvc.bookstore.domain.Account;`

`public interface AccountService {`

`    Account save(Account account);`

`    Account getAccount(String username);`

`    void checkPassword(Account account, String password);`

`    void updateLastLogin(Account account);`

`}`

如果可能的话，我们不应该通过调用一系列方法来执行系统功能。事实上，我们应该尽可能地将用户与数据访问和 POJO 交互隔离开来。在理想情况下，一个粗粒度的函数应该代表一个要么成功要么失败的单一工作单元。用户可以使用不同的客户端（例如，Web 应用程序、Web 服务或桌面应用程序）；然而，这些客户端应该执行相同的业务逻辑。因此，服务层应该是我们实际系统（即业务逻辑）的单一入口点。

在服务层拥有单一入口点和粗粒度方法的额外好处是，我们可以简单地在这一层应用事务和安全性。我们不必将安全性和事务性要求强加给应用程序的不同客户端。现在，这已成为系统核心的一部分，并且通常通过使用 AOP 来应用。

在基于 Web 的环境中，我们可能有多个用户同时操作服务。服务必须是无状态的，因此将服务设为单例是一个好做法。在领域模型中，应尽可能保持状态。保持服务层无状态还有一个额外的好处：它也使该层成为线程安全的。

将服务层保持为单一入口点、保持该层无状态，并在该层应用事务和安全性，使我们能够利用 Spring 框架的其他特性将服务层暴露给不同的客户端。例如，我们可以使用配置轻松地通过 RMI 或 JMS 暴露我们的服务层。有关 Spring 框架远程处理支持的更多信息，我们推荐阅读《Pro Spring 3》（Apress，2012 年）或在线 Spring 参考指南（[`www.springsource.org/documentation`](http://www.springsource.org/documentation)）。

在我们的书店示例应用程序中，`com.apress.prospringmvc.bookstore.service.BookstoreService` 接口（见代码清单 3-3）充当了我们服务层的接口（还有其他几个接口，但这是最重要的一个）。该接口包含几个粗粒度的方法。在大多数情况下，执行一个用例只需要一次方法调用（例如，`createOrder`）。

***代码清单 3-3.** BookstoreService 接口*

`package com.apress.prospringmvc.bookstore.service;`

`import java.util.List;`

`import com.apress.prospringmvc.bookstore.domain.Account;`
`import com.apress.prospringmvc.bookstore.domain.Book;`
`import com.apress.prospringmvc.bookstore.domain.BookSearchCriteria;`
`import com.apress.prospringmvc.bookstore.domain.Cart;`
`import com.apress.prospringmvc.bookstore.domain.Category;`
`import com.apress.prospringmvc.bookstore.domain.Order;`

`public interface BookstoreService {`

`    List<Book> findBooksByCategory(Category category);`

`    Book findBook(long id);`

`    Order findOrder(long id);`

`    List<Book> findRandomBooks();`

`    List<Order> findOrdersForAccount(Account account);`

`    Order store(Order order);`

`    List<Book> findBooks(BookSearchCriteria bookSearchCriteria);`

`    Order createOrder(Cart cart, Account account);`

`    List<Category> findAllCategories();`
`}`

如代码清单 3-3 所示，服务层依赖于领域层来执行业务逻辑。然而，它也依赖于数据访问层来从底层数据存储中存储和检索数据。服务层可以作为连接一个或多个领域对象以执行业务功能的粘合剂。服务层应该协调它需要哪些领域对象以及它们如何相互交互。

Spring 框架没有提供帮助我们实现服务层的接口；但这并不令人意外。服务层是构成我们应用程序的部分；事实上，它是为我们的应用程序专门定制的。尽管如此，Spring 框架可以帮助我们构建架构和编程模型。我们可以使用依赖注入并应用切面来驱动我们的事务。所有这些都对我们的编程模型产生了积极的影响。



#### 数据访问层

数据访问层负责与底层持久化机制进行交互。该层知道如何从数据存储中存储和检索对象。它以一种服务层无需了解底层使用何种数据存储的方式来完成此操作。（数据存储可以是数据库，也可以是文件系统中的平面文件。）

创建独立数据访问层的原因有几个。首先，我们不想让服务层承担了解我们使用何种数据存储（或多种数据存储）的负担；我们希望以透明的方式处理持久化。在我们的示例应用中，我们使用内存数据库和 JPA（Java 持久化 API）来存储数据。现在设想一下，我们的 `com.apress.prospringmvc.bookstore.domain.Account` 并非来自数据库，而是来自 Active Directory 服务。我们可以简单地创建一个知道如何处理 Active Directory 的接口新实现——所有这些都无需更改我们的服务层。理论上，我们可以相当轻松地替换实现；例如，我们可以从 JDBC 切换到 Hibernate，而无需更改服务层。这种情况不太可能发生，但拥有这种能力是件好事。

这种方法最重要的原因是它简化了应用的测试。通常，数据访问速度较慢，因此让我们的测试尽可能快地运行非常重要。独立的数据访问层使得为我们的数据访问层创建桩（stub）或模拟（mock）实现变得相当容易。

Spring 框架对数据访问层提供了出色的支持。例如，它提供了一种一致且透明的方式来处理各种不同的数据访问框架（例如 JDBC、JPA、Hibernate、iBATIS 和 JDO）。对于这些技术中的每一种，Spring 都为以下能力提供了广泛支持：

*   事务管理
*   资源处理
*   异常转换

对于其支持的每种技术，事务管理都是透明的。有一个事务管理器负责处理事务，它甚至支持 JTA（Java 事务 API），从而支持分布式或全局事务。这种出色的事务支持意味着事务管理器也可以为我们管理资源。我们不再需要担心数据库连接或文件句柄可能未被关闭；这一切都由我们处理。受支持的实现可以在 `org.springframework.jdbc` 和 `org.springframework.orm` 包中找到。

![图片](img/square.jpg) **提示** Spring Data 项目（[`www.springsource.org/spring-data`](http://www.springsource.org/spring-data)）提供了与多种技术的更深层次集成。对于某些用例，它甚至可以消除我们编写自己的数据访问对象（DAO）或仓库实现的需要。

Spring 框架在其数据访问支持中包含了另一个强大的特性：异常转换。Spring 为其所有支持的技术提供了广泛的异常转换支持。此特性将特定于技术的异常转换为 `org.springframework.dao.DataAccessException` 的子类。对于基于数据库的技术，它甚至可以考虑到数据库供应商、版本以及从数据库接收的错误代码。该异常层次结构继承自 `RuntimeException`；因此，它不必被捕获，因为它不是受检异常。有关数据访问支持的更多信息，请参阅 *Pro Spring 3*（Apress，2012）或在线 Spring 参考指南（[`www.springsource.org/documentation`](http://www.springsource.org/documentation)）。

清单 3-4 展示了一个数据访问对象或仓库可能的样子。请注意，该接口没有引用或提及我们使用的任何数据访问技术（我们在示例应用中使用 JPA）。此外，服务层不关心数据如何或在哪里持久化；它只想知道如何存储或检索数据。

***清单 3-4.** 一个示例 AccountRepository*

`package com.apress.prospringmvc.bookstore.repository;`

`import com.apress.prospringmvc.bookstore.domain.Account;`

`public interface AccountRepository {`

`    Account findByUsername(String username);`

`    Account findById(long id);`

`    Account save(Account account);`
`}`

### 条条大路通罗马

如前所述，这里讨论的架构并非唯一的应用架构。哪种架构最适合给定的应用，取决于应用的规模、开发团队的经验以及应用的生命周期。团队越大，或者应用的生命周期越长，拥有清晰分层架构的重要性就越高。

一个从单个静态页面开始的 Web 应用可能不需要任何架构。然而，随着应用的增长，我们越来越不能试图将所有内容都塞进那个单一页面，因为那样会使应用难以维护或理解，更不用说测试了。

随着应用在规模和寿命上的增长，我们需要重构其设计，并牢记每个层或组件应该具有单一职责。如果我们检测到某些关注点应该位于不同的层，或者触及多个组件，那么我们应该将其转换为应用的一个切面（横切关注点），并使用 AOP 将此切面应用到代码中。

在决定如何构建我们的层时，我们应该尝试为我们的系统识别出一个清晰的 API（通过 Java 接口暴露）。为我们的系统考虑 API 会促使我们思考我们的设计，以及一个有用且可用的 API。通常，如果一个 API 难以使用，那么它也难以测试和维护。因此，一个清晰的 API 很重要。此外，在不同层之间使用接口允许各层被独立构建和测试。这在较大的开发团队（或由多个较小团队组成的团队）中可能是一个巨大的优势。它使我们能够专注于我们正在处理的功能，而不是底层或更高层的组件。

在设计和构建应用时，使用良好的面向对象实践和模式来解决问题也很重要。例如，我们应该利用多态和继承的优势，并且应该使用 AOP 来应用系统范围的关注点。Spring 框架还可以帮助我们在运行时将应用组件装配在一起。总的来说，本章描述的特性和方法可以帮助我们保持代码整洁，并为我们的应用实现最佳架构。



### 本章小结

本章我们介绍了 MVC 模式，包括其起源以及它解决了哪些问题。我们还简要讨论了 MVC 模式的三个组成部分：模型、视图和控制器。接着，我们谈到了 Model 2 MVC 模式，以及使用前端控制器如何使其区别于 Model 1 MVC 模式。在 Spring MVC 中，这个前端控制器就是`org.springframework.web.servlet.DispatcherServlet`。

随后，我们简要概述了通用的 Web 应用程序架构。我们指出了 Web 应用程序中通常存在的五个不同层次：领域层、用户界面层、Web 层、服务层和数据访问层。所有这些层次在我们的应用程序中都扮演着重要角色，我们既讨论了这些角色是什么，也讨论了它们如何协同工作。我们还介绍了 Spring 如何在应用程序的不同层次中为我们提供帮助。

本章的主要收获在于，MVC 模式中的不同层次和组件可以帮助我们分离不同的关注点。每个层次都应该有单一的职责，无论是处理业务逻辑，还是作为 HTTP 世界与服务层之间的粘合剂。关注点分离有助于我们实现清晰的架构并创建可维护的代码。最后，清晰的层次划分使得测试我们的应用程序变得更加容易。

在下一章中，我们将深入探讨 Spring MVC。具体来说，我们将探索`DispatcherServlet`这个 Servlet，包括它的工作原理以及如何配置它。我们还将更仔细地研究本章中描述的不同组件在 Spring MVC 应用程序中是如何工作的。

## 第 4 章

## Spring MVC 架构

在本章中，你将深入 Spring MVC 的内部机制，仔细研究`org.springframework.web.servlet.DispatcherServlet`。你将首先了解该 Servlet 如何处理传入的请求，以及如何识别在请求处理过程中起作用的组件。在识别出这些组件之后，你将更深入地了解这些组件的角色和功能，以及这些组件的不同实现。你还将学习如何配置`org.springframework.web.servlet.DispatcherServlet`，部分内容将通过检查默认配置来实现。

### DispatcherServlet 请求处理工作流

在上一章中，你了解了前端控制器在 Model 2 MVC 模式中扮演的重要角色。前端控制器负责将传入的请求分派给正确的处理器，并准备响应，以便将其渲染为用户希望看到的内容。在 Spring MVC 中，前端控制器的角色由`org.springframework.web.servlet.DispatcherServlet`扮演。该 Servlet 使用多个组件来履行其职责。所有这些组件都以接口的形式表示，并且每个接口都有一个或多个可用的实现。下一节将探讨这些组件在请求处理工作流中的一般角色。后续章节将介绍这些接口的不同实现。

![图片](img/square.jpg) **注意** 我们特意使用了术语*处理器*。`DispatcherServlet`非常灵活且可定制，它能够处理的处理器类型不仅限于`org.springframework.web.servlet.mvc.Controller`的实现或带有`org.springframework.stereotype.Controller`注解的类。

#### 工作流

请求处理工作流的高级概览如图 4-1 所示。

![图片](img/9781430241553_Fig04-01.jpg)

***图 4-1.** 请求处理工作流*

在前面的章节中，你了解了关注点分离的重要性。在 Spring 框架中，同样的规则也得到了应用。许多支持组件在设计时都考虑到了可扩展性和关注点分离，并以接口的形式呈现。尽管图 4-1 中的高级概览是正确的，但幕后还有更多事情发生。图 4-2 展示了请求处理工作流更完整的视图。

![图片](img/9781430241553_Fig04-02.jpg)

***图 4-2.** 请求处理工作流*

图 4-2 提供了`DispatcherServlet`内部请求处理工作流的全局概览。以下各节将放大介绍此流程中的不同步骤。

##### 准备请求

在`DispatcherServlet`开始分派和处理请求之前，它首先会对请求进行一些准备和预处理工作。该 Servlet 首先使用`org.springframework.web.servlet.LocaleResolver`确定并暴露当前请求的`java.util.Locale`。接着，它在`org.springframework.web.context.request.RequestContextHolder`中准备并暴露当前请求。这使得框架代码可以轻松访问当前请求，而无需将其四处传递。

接下来，该 Servlet 将构建一个所谓的`org.springframework.web.servlet.FlashMap`。它通过调用`org.springframework.web.servlet.FlashMapManager`来实现，后者将尝试解析输入的`FlashMap`。此映射包含显式存储在上一个请求中的属性。通常，这在重定向到下一页时使用。此主题将在第 5 章中深入讨论。

接着，会检查传入的请求是否为 multipart HTTP 请求（这在执行文件上传时使用）。如果是，则通过将其传递给`org.springframework.web.multipart.MultipartResolver`，将请求包装在`org.springframework.web.multipart.MultipartHttpServletRequest`中。在此之后，请求就准备好被分派到正确的处理器了。图 4-3 展示了请求处理工作流第一部分的流程图。

![图片](img/9781430241553_Fig04-03.jpg)

***图 4-3.** 请求处理流程的开始*

##### 确定 HandlerExecutionChain

分派请求涉及多个组件（见图 4-4）。当请求准备好进行分派时，`DispatcherServlet`将咨询一个或多个`org.springframework.web.servlet.HandlerMapping`实现，以确定哪个处理器可以处理该请求。如果未找到处理器，则会向客户端发送 HTTP 404 响应。`HandlerMapping`返回一个`org.springframework.web.servlet.HandlerExecutionChain`（你将在下一节中了解更多信息）。当处理器确定后，该 Servlet 将尝试找到一个`org.springframework.web.servlet.HandlerAdapter`来实际执行找到的处理器。如果找不到合适的`HandlerAdapter`，则会抛出`javax.servlet.ServletException`。

![图片](img/9781430241553_Fig04-04.jpg)

***图 4-4.** 分派请求*



##### 执行 HandlerExecutionChain

为了处理请求，`DispatcherServlet` 使用 `HandlerExecutionChain` 来确定要执行的内容。该类持有对需要调用的实际处理程序的引用；同时，它（可选地）还引用了在处理器执行之前（`preHandle` 方法）和之后（`postHandle` 方法）执行的 `org.springframework.web.servlet.HandlerInterceptor` 实现。这些拦截器可用于应用横切功能（有关此主题的更多信息，请参见第 6 章）。如果代码成功执行，拦截器将再次被调用；最后，在需要时，视图会被渲染（参见图 4-5）。

![Image](img/9781430241553_Fig04-05.jpg)

***图 4-5.** 处理请求*

处理器的执行被委托给上一步中选定的 `HandlerAdapter`。它知道如何执行选定的处理器，以及如何将响应转换为 `org.springframework.web.servlet.ModelAndView`。如果返回的 `ModelAndView` 中没有视图，则会咨询 `org.springframework.web.servlet.RequestToViewNameTranslator`，根据传入的请求生成一个视图名称。

##### 处理异常

当在请求处理过程中抛出异常时，`DispatcherServlet` 将咨询已配置的 `org.springframework.web.servlet.HandlerExceptionResolver` 来处理抛出的异常。该解析器可以将异常转换为向用户显示的视图。例如，如果出现与数据库错误相关的异常，你可以显示一个指示数据库宕机的页面。如果异常未被解决，它将被重新抛出并由 Servlet 容器处理，这通常会导致 HTTP 500 响应码（内部服务器错误）。图 4-6 展示了请求处理工作流的这一部分。

![Image](img/9781430241553_Fig04-06.jpg)

***图 4-6.** 异常处理*

##### 渲染视图

如果在请求处理工作流中已选定了一个视图，`DispatcherServlet` 首先检查它是否是一个视图引用（如果视图是 `java.lang.String` 类型，则属于这种情况）。如果是，则会咨询已配置的 `org.springframework.web.servlet.ViewResolver`，将视图引用解析为实际的 `org.springframework.web.servlet.View` 实现。如果没有视图且无法解析，则会抛出 `javax.servlet.ServletException`。图 4-7 展示了视图渲染过程。

![Image](img/9781430241553_Fig04-07.jpg)

***图 4-7.** 视图渲染过程*

##### 完成处理

每个传入的请求都会经过请求处理流程的这一步，无论是否存在异常。如果 `HandlerExecutionChain` 可用，则会调用拦截器的 `afterCompletion` 方法。只有那些 `preHandle` 方法成功调用的拦截器，其 `afterCompletion` 方法才会被调用。接下来，这些拦截器会按照其 `preHandle` 方法被调用的相反顺序执行。这模仿了 Servlet 过滤器中的行为，即第一个被调用的过滤器也是最后一个被调用的。

最后，`DispatcherServlet` 使用 Spring 框架中的事件机制来触发一个 `org.springframework.web.context.support.RequestHandledEvent`（参见图 4-8）。你可以创建并配置一个 `org.springframework.context.ApplicationListener` 来接收并记录这些事件。

![Image](img/9781430241553_Fig04-08.jpg)

***图 4-8.** 完成处理*

#### 请求处理总结

`DispatcherServlet` 是使用 Spring MVC 处理请求的关键组件。它也非常灵活且可配置。这种灵活性源于该 Servlet 使用了大量不同的组件来履行其职责，并且这些组件都以接口形式表达。表 4-1 概述了请求处理工作流中涉及的所有主要组件类型。

![Image](img/tab_4_1.jpg)

![Image](img/tab_4_1a.jpg)

在接下来的章节中，你将看到如何配置 `DispatcherServlet`。你还将更深入地了解各种组件的不同实现。

### DispatcherServlet

与任何 Servlet 一样，`org.springframework.web.servlet.DispatcherServlet` 需要被配置，以便 Web 容器能够启动并映射该 Servlet。这样，它才能处理请求。配置 `DispatcherServlet` 是一个两步过程。首先，你需要告诉容器加载一个 Servlet 并将其映射到一个或多个 URL 模式。

启动后，该 Servlet 使用创建的 `org.springframework.web.context .WebApplicationContext` 来配置自身。Servlet 会尝试从该应用上下文中检测所需的组件，如果未找到，则在大多数情况下会使用某种默认值。

#### 启动 DispatcherServlet

Servlet 3.0 规范引入了多种配置和注册 Servlet 的选项：

*   选项 1：使用 `web.xml`（参见清单 4-1）。
*   选项 2：使用 `web-fragment.xml`（参见清单 4-2）。
*   选项 3：使用 `javax.servlet.ServletContainerInitializer`（参见清单 4-3）。
*   选项 4：示例应用程序使用 Spring 3.1，因此你可以通过实现 `org.springframework.web.WebApplicationInitializer` 接口获得第四个选项。

Dispatcher Servlet 需要一个 `WebApplicationContext`，该上下文应包含所有使 Dispatcher Servlet 能够自行配置的 Bean。默认情况下，Dispatcher Servlet 会创建一个 `org.springframework.web.context.support.XmlWebApplicationContext`。接下来各节中的所有示例都会加载 `org.springframework.web.servlet.DispatcherServlet` 并将其映射到所有传入请求（/*）。所有这些配置都会导致 Servlet 的运行时设置相同。只是实现该设置的机制不同。本书的其余部分将使用选项 4 来配置示例应用程序。

![Image](img/square.jpg) **注意** `org.springframework.web.context.WebApplicationContext` 是 `org.springframework.context.ApplicationContext` 的一个专门扩展，在 Web 环境中是必需的（更多信息请参见第 2 章）。

你在本书中构建的示例应用程序将尽可能使用选项 4 来配置环境和应用程序。尽管如此，你仍将学习配置 Servlet 的所有四种选项的基本设置。



##### 使用 web.xml

`web.xml` 文件自 Servlet 规范诞生之初就已存在。它是一个 XML 文件，包含启动 Servlet、监听器和/或过滤器所需的所有配置。清单 4-1 展示了启动 `DispatcherServlet` 所需的最小 `web.xml` 配置。`web.xml` 文件必须位于 Web 应用程序的 `WEB-INF` 目录中（这是 Servlet 规范规定的）。

***清单 4-1.** web.xml 配置（Servlet 3.0）*

`<web-app version="3.0"`

`        xsi:schemaLocation=http://java.sun.com/xml/ns/javaee`
`                           http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd`
`        metadata-complete="true">`

`    <servlet>`
`        <servlet-name>bookstore</servlet-name>`
`        <servlet-class>`
`            org.springframework.web.servlet.DispatcherServlet`
`        </servlet-class>`
`        <load-on-startup>1</load-on-startup>`
`    </servlet>`

`    <servlet-mapping>`
`        <servlet-name>bookstore</servlet-name>`
`        <url-pattern>/*</url-pattern>`
`    </servlet-mapping>`
`</web-app>`

![Image](img/square.jpg) **注意** 默认情况下，Dispatcher Servlet 会从 `WEB-INF` 目录加载名为 `[servletname]-servlet.xml` 的文件。

`web-app` 元素中的 `metadata-complete` 属性指示 Servlet 容器不要扫描类路径以查找 `javax.servlet.ServletContainerInitializer` 的实现；也不会扫描 `web-fragment.xml` 文件。将此属性添加到 `web.xml` 中可以显著缩短启动时间，因为扫描类路径在大型应用程序中可能会花费相当长的时间。

##### 使用 web-fragment.xml

`web-fragment.xml` 特性自 Servlet 规范 3.0 版本起可用，它允许对 Web 应用程序进行更加模块化的配置。`web-fragment.xml` 必须位于 jar 文件的 META-INF 目录中。它不会被检测到位于 Web 应用程序的 META-INF 中；它必须位于 jar 文件中。`web-fragment.xml` 可以包含与 `web.xml` 相同的元素（参见清单 4-2）。

这种方法的好处是，每个打包为 jar 文件的模块都可以为 Web 应用程序的配置做出贡献。这也可以被视为一个缺点，因为现在你将配置分散到了代码库中，这在大型项目中可能会带来麻烦。

***清单 4-2.** web-fragment.xml 配置（Servlet 3.0）*

`<web-fragment version="3.0"`

`  xsi:schemaLocation="http://java.sun.com/xml/ns/javaee`
`                      http://java.sun.com/xml/ns/javaee/web-fragment_3_0.xsd">`

`    <servlet>`
`        <servlet-name>bookstore</servlet-name>`
`        <servlet-class>`
`            org.springframework.web.servlet.DispatcherServlet`
`        </servlet-class>`
`        <load-on-startup>1</load-on-startup>`
`    </servlet>`

`    <servlet-mapping>`
`        <servlet-name>bookstore</servlet-name>`
`        <url-pattern>/*</url-pattern>`
`    </servlet-mapping>`
`</web-fragment>`

##### 使用 ServletContainerInitializer

Servlet 规范 3.0 版本引入的另一个特性是，可以选择使用基于 Java 的方法来配置 Web 环境（参见清单 4-3）。兼容 Servlet 3.0 的容器会扫描类路径，查找实现了 `javax.servlet.ServletContainerInitializer` 接口的类，并调用这些类上的 `onStartup` 方法。通过在这些类上添加 `javax.servlet.annotation.HandlesTypes` 注解，你还可以获得需要进一步配置 Web 应用程序的类（这是允许使用第四种选项 `org.springframework.web.WebApplicationInitializer` 的机制）。

与 `web.fragments` 类似，`ServletContainerInitializer` 允许对 Web 应用程序进行模块化配置，但现在是基于 Java 的方式。使用 Java 可以带来使用 Java 语言而非 XML 的所有额外好处。此时，你拥有强类型，可以影响 Servlet 的构建，并且有更简单的方法来配置 Servlet（在 XML 文件中，这是通过添加 `init-param` 和/或 `context-param` 元素来完成的）。

***清单 4-3.** 基于 Java 的配置*

`package com.apress.prospringmvc.bookstore.web;`

`import java.util.Set;`

`// javax.servlet imports omitted.`

`import org.springframework.web.servlet.DispatcherServlet;`

`public class BookstoreServletContainerInitializer`
`implements ServletContainerInitializer {`

`  @Override`
`  public void onStartup(Set<Class<?>> classes, ServletContext servletContext)`
`  throws ServletException {`
`      ServletRegistration.Dynamic registration;`
`      registration = servletContext.addServlet("dispatcher", DispatcherServlet.class);`
`      registration.setLoadOnStartup(1);`
`      registration.addMapping("/*");`
`  }`
`}`

##### 使用 WebApplicationInitializer

现在，是时候看看使用 Spring 时配置应用程序的第四种选项了。Spring 已经提供了一个 `ServletContainerInitializer` 实现，即 `org.springframework.web.SpringServletContainerInitializer`，它让事情变得稍微简单一些（参见清单 4-4）。Spring 框架提供的这个实现会检测并实例化所有 `org.springframework.web.WebApplicationInitializer` 的实例，并调用这些实例的 `onStartup` 方法。

***清单 4-4.** WebApplicationInitializer 配置*

`package com.apress.prospringmvc.bookstore.web;`

`// javax.servlet imports omitted`

`import org.springframework.web.WebApplicationInitializer;`
`import org.springframework.web.servlet.DispatcherServlet;`

`public class BookstoreWebApplicationInitializer implements WebApplicationInitializer {`

`  @Override`
`  public void onStartup(ServletContext servletContext) throws ServletException {`
`      ServletRegistration.Dynamic  registration`
`      registration = servletContext.addServlet("dispatcher",`![Image](img/U002.jpg)
` DispatcherServlet.class);`
`      registration.addMapping("/*");`
`      registration.setLoadOnStartup(1);`
`  }`
`}`

![Image](img/square.jpg) **警告** 使用此功能可能会严重影响应用程序的启动时间！首先，Servlet 容器需要扫描类路径以查找所有 `javax.servlet.ServletContainerInitializer` 的实现。其次，类路径会被扫描以查找 `org.springframework.web.WebApplicationInitializer` 的实现。在大型应用程序中，这种扫描可能会花费一些时间。

#### 配置 DispatcherServlet

配置 `org.springframework.web.servlet.DispatcherServlet` 是一个两步过程。第一步是通过直接在 Dispatcher Servlet 上设置属性（声明）来配置 Servlet 的行为。第二步是配置应用程序上下文中的组件（初始化）。

Dispatcher Servlet 为组件提供了许多默认设置。这使你不必为基本行为进行大量配置，并且你可以根据需要覆盖和扩展配置。除了 Dispatcher Servlet 的默认配置之外，Spring @MVC 也有一个默认配置，可以通过使用 `org.springframework.web.servlet.config.annotation.EnableWebMvc` 注解来启用（参见第 2 章中的“启用特性”部分）。

##### Dispatcher Servlet 属性

Dispatcher Servlet 有许多可以设置的属性。所有这些属性都有 `setter` 方法，并且都可以通过编程方式设置，或者在 XML 配置中通过包含 Servlet 初始化参数来设置。表 4-2 列出并描述了 Dispatcher Servlet 上可用的属性。

![Image](img/tab_4_2.jpg)

![Image](img/tab_4_2a.jpg)

![Image](img/tab_4_2b.jpg)



##### 应用上下文

`org.springframework.web.servlet.DispatcherServlet` 需要一个 `org.springframework.web.context.WebApplicationContext` 来使用所需的组件进行自我配置。你可以让 Servlet 自行构建一个，或者使用构造函数传入一个应用上下文。在基于 XML 的配置文件中，使用的是第一种方式（因为无法构建应用上下文）。在基于 Java 的配置中，则使用第二种方式。

在示例应用中，`com.apress.prospringmvc.bookstore.web.BookstoreWebApplicationInitializer` 是引导应用程序的类。要启用基于 Java 的配置，你需要指示 Servlet 使用基于 Java 的应用上下文（默认是基于 XML 的），并将配置类传递给它。你将使用 `org.springframework.web.context.support.AnnotationConfigWebApplicationContext` 类来设置应用程序并配置 Servlet。更改部分在代码清单 4-5 中以粗体突出显示。

***代码清单 4-5.** 带有 ApplicationContext 的 BookstoreWebApplicationInitializer*

`package com.apress.prospringmvc.bookstore.web;`

`// javax.servlet 导入已省略。`

`import org.springframework.web.WebApplicationInitializer;`
`import org.springframework.web.context.WebApplicationContext;`
`import org.springframework.web.context.support`
`       .AnnotationConfigWebApplicationContext;`
`import org.springframework.web.servlet.DispatcherServlet;`

`import com.apress.prospringmvc.bookstore.web.config.WebMvcContextConfiguration;`

`public class BookstoreWebApplicationInitializer`![Image](img/U002.jpg)
` implements WebApplicationInitializer {`

`  @Override`
`  public void onStartup(final ServletContext servletContext)`
`  throws ServletException {`
`      registerDispatcherServlet(servletContext);`
`    }`

`  private void registerDispatcherServlet(final ServletContext servletContext) {`
**`      WebApplicationContext dispatcherContext = createContext`![Image](img/U002.jpg)**
**`(WebMvcContextConfiguration.class);`**
**`      DispatcherServlet dispatcherServlet = new DispatcherServlet(dispatcherContext);`**
`      ServletRegistration.Dynamic dispatcher;`
`      dispatcher = servletContext.addServlet("dispatcher", dispatcherServlet);`
`      dispatcher.setLoadOnStartup(1);`
`      dispatcher.addMapping("/");`
`  }`

**`  private WebApplicationContext createContext(final Class<?>... annotatedClasses) {`**
**`      AnnotationConfigWebApplicationContext context`**
**`      context = new AnnotationConfigWebApplicationContext();`**
**`      context.register(annotatedClasses);`**
**`      return context;`**
**`  }`**
**`}`**

代码清单 4-5 展示了如何构造 `org.springframework.web.servlet.DispatcherServlet` 并为其传入一个应用上下文。这是配置 Servlet 最基本的方式。

第 2 章 介绍了配置文件（profile）的概念。要选择一个配置文件，你可以包含一个 Servlet 初始化参数（参见第 2 章）；然而，为了更动态化，你可以使用 `org.springframework.context.ApplicationContextInitializer`。此类初始化器用于在应用上下文加载所有 bean 之前对其进行初始化。

这在 Web 应用中非常有用，当你想要配置或设置要使用的配置文件时（更多信息请再次参见第 2 章）。例如，你可能需要设置一个自定义的系统属性。或者，你可以通过读取文件系统中的某个文件来检测配置文件，甚至可以根据操作系统来选择配置文件。你拥有几乎无限的选择。

在附录 A 中，你将学习如何将应用程序部署到 CloudFoundry。CloudFoundry 也提供了一个包含 `ApplicationContextInitializer` 的 API。该实现会检测到它正在云上运行，并激活一个名为 `cloud` 的配置文件（参见代码清单 4-6）。

***代码清单 4-6.** CloudApplicationContextInitializer*

`package org.cloudfoundry.reconfiguration.spring;`

`// 其他导入已省略`

`import org.cloudfoundry.runtime.env.CloudEnvironment;`
`import org.springframework.context.ApplicationContextInitializer;`
`import org.springframework.context.ConfigurableApplicationContext;`
`import org.springframework.core.Ordered;`
`import org.springframework.core.env.ConfigurableEnvironment;`
`import org.springframework.core.env.EnumerablePropertySource;`
`import org.springframework.core.env.PropertiesPropertySource;`
`import org.springframework.core.env.PropertySource;`

`public final class CloudApplicationContextInitializer`
`implements ApplicationContextInitializer<ConfigurableApplicationContext>,`
`           Ordered {`

`    private static final Log logger =`
`            LogFactory.getLog(CloudApplicationContextInitializer.class);`

`    private static final int DEFAULT_ORDER = 0;`

`    private ConfigurableEnvironment springEnvironment;`
`    private CloudEnvironment cloudFoundryEnvironment;`

`    public CloudApplicationContextInitializer() {`
`        cloudFoundryEnvironment = new CloudEnvironment();`
`    }`

`    @Override`
`    public void initialize(ConfigurableApplicationContext applicationContext) {`
`        if (!cloudFoundryEnvironment.isCloudFoundry()) {`
`            logger.info("未在 Cloud Foundry 上运行。")`
`            return;`
`        }`
`        try {`
`            logger.info("正在为 Cloud Foundry 初始化 Spring 环境");`
`            springEnvironment = applicationContext.getEnvironment();`
`            addPropertySource(buildPropertySource());`
**`            addActiveProfile("cloud");`**
`        } catch (Throwable t) {`
*`// 确保安全`*
`            logger.error("初始化时出现意外异常: "`
`                         + t.getMessage(), t);`
`        }`
`    }`
`    // 其他方法已省略`
`}`

##### 组件解析

当 Servlet 配置完成后，它将从 Servlet 容器接收一个初始化请求。当 Servlet 初始化时，它会使用如图 4-9 所示的逻辑来检测所需的组件。某些组件按类型检测，而其他组件则按名称检测。对于可按类型检测的组件，你可以指定（参见表 4-2）不希望这样做。在这种情况下，组件将通过一个众所周知的名称来检测。表 4-3 列出了参与请求处理的不同组件以及用于检测它们的 bean 名称。该表还指出了 Dispatcher Servlet 是否自动检测多个实例（当“是”可以被禁用时，则按表中指定的名称检测单个 bean）。

![Image](img/9781430241553_Fig04-09.jpg)

***图 4-9.** DispatcherServlet 的组件发现过程*

![Image](img/tab_4_3.jpg)

##### Dispatcher Servlet 的默认配置

你可能会觉得处理请求所涉及的所有组件有点让人不知所措。你甚至可能想知道是否需要显式配置所有这些组件。幸运的是，Spring MVC 有一些合理的默认值，在很多情况下，这些默认值已经足够——或者至少足以让你入门。正如你在表 4-4 中所见，Dispatcher Servlet 有相当多的默认设置。你可以在下一节中找到关于不同实现的更多信息。

![Image](img/tab_4_4.jpg)

![Image](img/tab_4_4a.jpg)



##### Spring @MVC 默认配置

第 2 章 介绍了 Spring 的 @Enable 注解。其中一个注解是 `org.springframework.web.servlet.config.annotation.EnableWebMvc.`。该注解启用了强大且灵活的基于注解的请求处理组件，并覆盖了调度 Servlet 的一些默认设置。实际配置可以在 `org.springframework.web.servlet.config.annotation.WebMvcConfigurationSupport` 类中找到。这是使用该注解时注册的配置。表 4-5 展示了启用 @MVC 时的默认配置，并突出了 @MVC 与调度 Servlet 之间的差异。

![Image](img/tab_4_5.jpg)

这些差异可能看起来不大，但新注册的组件提供的初始默认值与调度 Servlet 提供的默认值相比有显著不同。本质区别在于：Spring 的 @MVC 组件使您能够编写更灵活的请求处理方法（参见第 5 章和第 6 章）。

### Spring MVC 组件

在前面的章节中，您了解了请求处理工作流及其使用的组件。您还学习了如何配置 `org.springframework.web.servlet.DispatcherServlet`。在本节中，您将更深入地了解请求处理工作流中涉及的所有组件。例如，您将探索不同组件的 API，并查看 Spring 框架附带了哪些实现。

#### HandlerMapping

`HandlerMapping` 的职责是确定将传入请求分派给哪个处理器。您可用于映射传入请求的一个标准是 URL；然而，实现（参见图 4-10）可以自由选择使用什么标准来确定映射。

`org.springframework.web.servlet.HandlerMapping` 的 API 包含一个单一方法（参见清单 4-7）。`DispatcherServlet` 调用此方法来确定 `org.springframework.web .servlet.HandlerExecutionChain`。可以配置多个处理器映射。Servlet 将按顺序调用不同的处理器映射，直到其中一个返回非 `null` 值。

***清单 4-7.** HandlerMapping API*

`package org.springframework.web.servlet;`

`import javax.servlet.http.HttpServletRequest;`
`public interface HandlerMapping {`

`    HandlerExecutionChain getHandler(HttpServletRequest request)`
`    throws Exception;`

`}` ![Image](img/9781430241553_Fig04-10.jpg)

***图 4-10.** HandlerMapping 实现*

Spring MVC 开箱即用地提供了六种不同的实现。其中大多数基于 URL 映射。其中两种实现提供了更复杂的映射策略，您稍后将了解。然而，在查看不同的实现之前，更仔细地研究 URL 及其重要部分可能会有所帮助。

一个请求 URL 由几个部分组成。让我们看一下 [`http://www.example.org/bookstore/app/home`](http://www.example.org/bookstore/app/home) 这个 URL 并对其进行剖析。一个 URL 由四个部分组成（参见图 4-11）：

1.  服务器的主机名
2.  应用程序的名称（如果是根应用程序，则为空）
3.  Servlet 映射的名称（在示例应用程序中，它映射到 /）
4.  Servlet 内部的路径

![Image](img/9781430241553_Fig04-11.jpg)

***图 4-11.** URL 映射*

默认情况下，所有提供的处理器映射实现都使用 Servlet 内部相对于 Servlet 上下文的路径（Servlet 上下文相对路径）来解析处理器。将 `alwaysUseFullPath` 属性设置为 `true` 可以改变此行为。此时会包含 Servlet 映射，这将（对于手头的示例）导致使用 */app/home* 来解析请求处理器；否则，将使用 */home*。

所有实现共有的最后一个特性是可以配置默认处理器。这是通过设置 `defaultHandler` 属性来完成的。当无法为传入请求找到处理器时，它将始终映射到默认处理器。这是可选的，应谨慎使用，尤其是在链接多个处理器映射时。只有最后一个处理器映射应指定默认处理器，否则链会中断。

##### BeanNameUrlHandlerMapping

`org.springframework.web.servlet.handler.BeanNameUrlHandlerMapping` 是调度 Servlet 使用的默认策略之一。此实现将任何名称以 / 开头的 bean 视为潜在的请求处理器。一个 bean 可以有多个名称，名称也可以包含通配符，用 * 表示。

此实现使用 Ant 风格的正则表达式将传入请求的 URL 与 bean 的名称进行匹配。它遵循以下算法：

1.  尝试精确匹配；如果找到，则退出。
2.  搜索所有已注册的路径以寻找匹配项；最具体的匹配项获胜。
3.  如果未找到匹配项，则返回映射到 /* 的处理器或默认处理器（如果已配置）。

![Image](img/square.jpg) **注意** bean 的 `name` 与 `id` 不同。Id 由 XML 规范定义，不能包含 / 等特殊字符。这意味着您需要使用 bean 的 name。您可以通过在 `org.springframework.context.annotation.Bean` 注解上设置 `name` 属性来为 bean 提供名称。一个 bean 可以有多个名称，名称可以写成 Ant 风格的正则表达式。

清单 4-8 展示了如何使用 bean 名称并将其映射到 `/index.htm` URL。在示例应用程序中，您现在可以使用 [`http://localhost:8080/chapter4-bookstore/index.htm`](http://localhost:8080/chapter4-bookstore/index.htm) 来调用此控制器。

***清单 4-8.** BeanNameUrlHandlerMapping 示例配置*

`package com.apress.prospringmvc.bookstore.web.config;`

`import java.util.Properties;`

`import org.springframework.context.annotation.Bean;`
`import org.springframework.context.annotation.Configuration;`

`import com.apress.prospringmvc.bookstore.web.IndexController;`

`@Configuration`
`public class WebMvcContextConfiguration {`

**`    @Bean(name = { "/index.htm" })`**
`    public IndexController indexController() {`
`        return new IndexController();`
`    }`
`}`



##### SimpleUrlHandlerMapping

此实现需要显式配置，与 `org.springframework.web.servlet.handler.BeanNameUrlHandlerMapping` 不同，它不会自动检测映射。清单 4-9 展示了一个示例配置。同样，你将控制器映射到 `/index.htm`。

***清单 4-9.** SimpleUrlHandlerMapping 示例配置*

`package com.apress.prospringmvc.bookstore.web.config;`

`// 其他导入已省略，参见清单 4-8`

`import org.springframework.web.servlet.HandlerMapping;`
`import org.springframework.web.servlet.handler.SimpleUrlHandlerMapping;`

`@Configuration`
`public class WebMvcContextConfiguration {`

`    @Bean`
`    public IndexController indexController() {`
`        return new IndexController();`
`    }`

`    @Bean`
`    public HandlerMapping simpleUrlHandlerMapping() {`
`        SimpleUrlHandlerMapping urlMapping = new SimpleUrlHandlerMapping();`
`        Properties mappings = new Properties();`
**`        mappings.put("/index.htm", "indexController");`**
`        urlMapping.setMappings(mappings);`
`        return urlMapping;`
`    }`
`}`

你需要显式配置 `SimpleUrlHandlerMapping` 并向其传递映射（参见粗体代码）。你将 `/index.htm` URL 映射到名为 `indexController` 的控制器。如果你有很多控制器，此配置会显著增长。这种方法的优点在于，你可以将所有映射集中在一个位置。

##### ControllerBeanNameHandlerMapping

此实现的工作方式与 `org.springframework.web.servlet.handler .BeanNameUrlHandlerMapping` 类似，但有一个重大区别：它不要求 bean 名称以 / 开头。此映射会检测应用程序上下文中的所有控制器，获取它们的名称，并在前面加上 /。它还可以选择性地为生成的 URL 映射添加后缀。清单 4-10 展示了如何使用此处理器映射将控制器映射到 `/index.htm`。

***清单 4-10.** ControllerBeanNameHandlerMapping 示例配置*

`package com.apress.prospringmvc.bookstore.web.config;`

`// 其他导入已省略，参见清单 4-8`

`import org.springframework.web.servlet.HandlerMapping;`
`import org.springframework.web.servlet.mvc.support.ControllerBeanNameHandlerMapping;`

`@Configuration`
`public class WebMvcContextConfiguration {`

**`  @Bean(name = "index")`**
`  public IndexController indexController() {`
`      return new IndexController();`
`  }`

`  @Bean`
`  public HandlerMapping controllerBeanNameHandlerMapping() {`
`      ControllerBeanNameHandlerMapping mapping;`
`      mapping = new ControllerBeanNameHandlerMapping();`
**`      mapping.setUrlSuffix(".htm");`**
`      return mapping;`
`  }`
`}`

##### ControllerClassNameHandlerMapping

此实现会检测应用程序上下文中的所有控制器，并使用类的简单名称来创建 URL 映射。对于 `com.apress.prospringmvc.bookstore.web.IndexController`，它将创建 URL `/index`。它会获取类的简单名称，去掉 Controller 部分，并将剩余部分转换为小写（除非另有配置）。清单 4-11 展示了此实现的一个示例配置，它将把控制器映射到 `/index` URL。此映射实现不支持后缀（例如 `.htm`）。

***清单 4-11.** ControllerClassNameHandlerMapping 示例配置*

`package com.apress.prospringmvc.bookstore.web.config;`

`// 其他导入已省略，参见清单 4-8`

`import org.springframework.web.servlet.HandlerMapping;`
`import org.springframework.web.servlet.mvc.support.ControllerClassNameHandlerMapping;`

`@Configuration`
`public class WebMvcContextConfiguration {`

`    @Bean`
`    public IndexController indexController() {`
`        return new IndexController();`
`    }`

`    @Bean`
`    public HandlerMapping controllerClassNameHandlerMapping() {`
`        return new ControllerClassNameHandlerMapping();`
`    }`
`}`

如果你的控制器遵循某些命名约定，并且它们直接映射到 URL，那么此映射实现会非常方便。这种方法可以节省大量配置。

##### DefaultAnnotationHandlerMapping 和 RequestMappingHandlerMapping

DefaultAnnotationHandlerMapping 和 RequestMappingHandlerMapping 实现是更复杂的实现。两者都非常强大，并且都使用注解来检测映射。此外，两者都会检测应用程序上下文中请求处理 bean 上的 `org.springframework.web.bind.annotation.RequestMapping` 注解。此注解可以放在类级别和/或方法级别。要将 `com.apress.prospringmvc.bookstore.web.IndexController` 映射到 `/index.htm`，你需要添加该注解。清单 4-12 是控制器，清单 4-13 展示了示例配置。

***清单 4-12.** 带有 RequestMapping 的 IndexController*

`package com.apress.prospringmvc.bookstore.web;`

`import org.springframework.stereotype.Controller;`
`import org.springframework.web.bind.annotation.RequestMapping;`
`import org.springframework.web.servlet.ModelAndView;`

`@Controller`
`public class IndexController {`

`    @RequestMapping(value = "/index.htm")`
`    public ModelAndView indexPage() {`
`        return new ModelAndView("/WEB-INF/views/index.jsp");`
`    }`
`}`

***清单 4-13.** 基于注解的示例配置*

`package com.apress.prospringmvc.bookstore.web.config;`

`// 其他导入已省略，参见清单 4-8`

`@Configuration`
`public class WebMvcContextConfiguration {`

`    @Bean`
`    public IndexController indexController() {`
`        return new IndexController();`
`    }`
`}`

`DefaultAnnotationHandlerMapping` 是 dispatcher servlet 注册的默认映射之一。如果你想使用 `RequestMappingHandlerMapping`，则需要显式配置它，或者使用 `org.springframework.web.servlet.config.annotation.EnableWebMvc` 注解。`RequestMappingHandlerMapping` 实现比默认注册的映射更强大、更灵活，并且允许更灵活的配置和映射方法。这种方法的可扩展性和灵活性将在第 6 章中介绍。



#### HandlerAdapter

`org.springframework.web.servlet.HandlerAdapter` 是调度器 Servlet 与所选处理器之间的粘合剂。它将实际的执行逻辑从调度器 Servlet 中剥离，从而使调度器 Servlet 具有无限的可扩展性。可以将此组件视为 Servlet 与实际处理器实现之间的桥梁。清单 4-14 展示了 `HandlerAdapter` 的 API。

***清单 4-14.** HandlerAdapter API*

`package org.springframework.web.servlet;`

`import javax.servlet.http.HttpServletRequest;`
`import javax.servlet.http.HttpServletResponse;`

`public interface HandlerAdapter {`

`    boolean supports(Object handler);`

`    ModelAndView handle(HttpServletRequest request,`
`                        HttpServletResponse response,`
`                        Object handler) throws Exception;`

`    long getLastModified(HttpServletRequest request, Object handler);`

`}`

如清单 4-14 所示，该 API 包含几个方法。调度器 Servlet 会调用上下文中每个处理器的 `supports` 方法，以确定哪个 `HandlerAdapter` 能够执行所选处理器。如果处理器适配器能够执行该处理器，则会调用 `handle` 方法来实际执行所选处理器。处理器的执行可能会返回一个 `org.springframework.web.servlet.ModelAndView` 对象。然而，某些实现始终返回 `null`，表示响应已发送给客户端。

如果传入的请求是 `GET` 或 `HEAD` 请求，则会调用 `getLastModified` 方法来确定底层资源最后被修改的时间（`-1` 表示内容始终重新生成）。结果会作为 `Last-Modified` 请求头发送回客户端，并与 `If-Modified-Since` 请求头进行比较。如果资源有修改，则会重新生成内容并再次发送给客户端；否则，会向客户端返回 HTTP 响应码 304（未修改）。当调度器 Servlet 提供静态资源服务时，此功能尤其有用，可以节省带宽。

Spring MVC 开箱即用地提供了五个 `HandlerAdapter` 的实现（参见图 4-12）。

![Image](img/9781430241553_Fig04-12.jpg)

***图 4-12.** HandlerAdapter 实现*

##### HttpRequestHandlerAdapter

`org.springframework.web.servlet.mvc.HttpRequestHandlerAdapter` 知道如何执行 `org.springframework.web.HttpRequestHandler` 实例。此处理器适配器主要用于 Spring Remoting 以支持某些 HTTP 远程处理选项。不过，您也会用到 `org.springframework.web.HttpRequestHandler` 接口的两个实现。一个用于提供静态资源服务，另一个用于将传入请求转发到 Servlet 容器的默认 Servlet（有关此实现的更多信息，请参见第 5 章）。

##### SimpleControllerHandlerAdapter

`org.springframework.web.servlet.mvc.SimpleControllerHandlerAdapter` 知道如何执行 `org.springframework.web.servlet.mvc.Controller` 实现。它返回从控制器实例的 `handleRequest` 方法返回的 `org.springframework .web.servlet.ModelAndView` 对象。

##### SimpleServletHandlerAdapter

在应用程序上下文中配置 `javax.servlet.Servlet` 实例并将其置于调度器 Servlet 之后可能会很方便。为了能够执行这些 Servlet，您需要 `org.springframework.web.servlet.handler.SimpleServletHandlerAdapter`。它知道如何执行 `javax.servlet.Servlet`，并且始终返回 `null`，因为它期望 Servlet 自行处理响应。

##### AnnotationMethodHandlerAdapter

`org.springframework.web.servlet.mvc.annotation.AnnotationMethodHandlerAdapter` 用于执行带有 `org.springframework.web.bind.annotation.RequestMapping` 注解的方法。它会转换方法参数，并让您轻松访问请求参数。方法的返回值会被转换或添加到由此处理器适配器内部创建的 `org.springframework.web.servlet.ModelAndView` 中。整个映射和转换过程相当严格；不过，存在一些扩展点，允许您自定义或添加自己的类型处理。

##### RequestMappingHandlerAdapter

`org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter` 用于执行带有 `org.springframework.web.bind.annotation.RequestMapping` 注解的方法。它会转换方法参数，并让您轻松访问请求参数。方法的返回值会被转换或添加到由此处理器适配器内部创建的 `org.springframework.web.servlet.ModelAndView` 中。整个绑定和转换过程具有很高的可配置性和灵活性。从 `org.springframework.web.servlet.mvc.annotation .AnnotationMethodHandlerAdapter` 中汲取的许多经验教训都已融入其中；其可能性将在第 5 章和第 6 章中阐述。

#### MultipartResolver

`org.springframework.web.multipart.MultipartResolver` 策略接口用于判断传入请求是否为多部分文件请求（用于文件上传）；如果是，它会将传入请求包装在 `org.springframework.web.multipart.MultipartHttpServletRequest` 中。然后，可以使用包装后的请求轻松访问表单中的底层多部分文件。文件上传将在第 7 章中说明。清单 4-15 展示了 `MultipartResolver` 的 API。

***清单 4-15.** MultipartResolver API*

`package org.springframework.web.multipart;`

`import javax.servlet.http.HttpServletRequest;`

`public interface MultipartResolver {`

`    boolean isMultipart(HttpServletRequest request);`

`    MultipartHttpServletRequest resolveMultipart(HttpServletRequest request)`
`    throws MultipartException;`

`    void cleanupMultipart(MultipartHttpServletRequest request);`
`}`

`org.springframework.web.multipart.MultipartResolver` 的三个方法在请求的准备和清理过程中被调用。`isMultipart` 方法被调用来判断传入请求是否确实是多部分请求。如果是，则调用 `resolveMultipart` 方法，该方法将原始请求包装在 `MultipartHttpServletRequest` 中。最后，当请求处理完毕后，会调用 `cleanupMultipart` 方法来清理所有已使用的资源。图 4-13 展示了 `MultipartResolver` 的两个开箱即用的实现。

![Image](img/9781430241553_Fig04-13.jpg)

***图 4-13.** MultipartResolver 实现*

##### CommonsMultipartResolver

`org.springframework.web.multipart.commons.CommonsMultipartResolver` 使用 Commons `fileupload` 库^(1) 来处理多部分文件。它使得可以轻松配置 Commons `fileupload` 库的多个方面。

##### StandardServletMultipartResolver

Servlet 3.0 规范引入了一种处理多部分表单的标准方式。`org.springframework.web.multipart.support.StandardServletMultipartResolver` 仅作为此标准方法的包装器，以便以透明的方式暴露它。



#### LocaleResolver

`org.springframework.web.servlet.LocaleResolver` 策略接口用于确定渲染页面时应使用哪个 `java.util.Locale`。在大多数情况下，它用于解析应用程序中的验证消息或标签。不同的实现方式如图 4-14 所示，并在以下小节中描述。

____________

¹ [`http://commons.apache.org/fileupload/`](http://commons.apache.org/fileupload/)

![Image](img/9781430241553_Fig04-14.jpg)

***图 4-14.** LocaleResolver 实现*

清单 4-16 展示了 `org.springframework.web.servlet.LocaleResolver` 的 API。

***清单 4-16.** LocaleResolver API*

`package org.springframework.web.servlet;`

`import java.util.Locale;`

`import javax.servlet.http.HttpServletRequest;`
`import javax.servlet.http.HttpServletResponse;`

`public interface LocaleResolver {`

`    Locale resolveLocale(HttpServletRequest request);`
`    void setLocale(HttpServletRequest request,`
`                   HttpServletResponse response,`
`                   Locale locale);`

`}`

该 API 包含两个方法，每个方法在存储和检索当前 `java.util.Locale` 时都发挥作用。当您想要更改当前 locale 时，会调用 `setLocale` 方法。如果实现不支持此操作，则会抛出 `java.lang.UnsupportedOperationException`。Spring 框架（通常内部）使用 `resolveLocale` 方法来解析当前 locale。

##### AcceptHeaderLocaleResolver

`org.springframework.web.servlet.i18n.AcceptHeaderLocaleResolver` 简单地委托给当前 `javax.servlet.HttpServletRequest` 的 `getLocale` 方法。它使用 `Accept-Language` HTTP 头来确定语言。客户端设置此标头值；此解析器不支持更改 locale。

##### CookieLocaleResolver

顾名思义，`org.springframework.web.servlet.i18n.CookieLocaleResolver` 使用 `javax.servlet.http.Cookie` 来存储要使用的 locale。这在您希望应用程序尽可能无状态的情况下特别有用。实际值存储在客户端，并在每次请求时发送给您。此解析器允许更改 locale（您可以在第 6 章中找到更多信息）。此解析器还允许您配置 cookie 的名称和要使用的默认 locale。如果无法为当前请求确定值（即，既没有 cookie 也没有默认 locale），则此解析器会回退到请求的 locale（请参阅 `AcceptHeaderLocaleResolver`）。

##### FixedLocaleResolver

`org.springframework.web.servlet.i18n.FixedLocaleResolver` 是 `org.springframework.web.servlet.LocaleResolver` 最基本的实现。它允许您配置在整个应用程序中使用的 locale。此配置是固定的；因此，无法更改。

##### SessionLocaleResolver

`org.springframework.web.servlet.i18n.SessionLocaleResolver` 实现使用 `javax.servlet.http.HttpSession` 来存储 locale 的值。可以配置属性的名称以及默认 locale。如果无法为当前请求确定值（即，会话中既没有存储值也没有默认 locale），则它会回退到请求的 locale（请参阅 `AcceptHeaderLocaleResolver`）。此解析器还允许您更改 locale（更多信息请参见第 6 章）。

#### ThemeResolver

`org.springframework.web.servlet.ThemeResolver` 策略接口用于确定渲染页面时应使用哪个主题。有几种实现；这些如图 4-15 所示，并在以下小节中解释。如何应用主题化在第 8 章中解释。如果无法解析主题名称，则此解析器使用硬编码的默认 `theme`。

![Image](img/9781430241553_Fig04-15.jpg)

***图 4-15.** ThemeResolver 实现*

清单 4-17 展示了 `org.springframework.web.servlet.ThemeResolver` 的 API，它与 `org.springframework.web.servlet.LocaleResolver` API 非常相似。

***清单 4-17.** ThemeResolver API*

`package org.springframework.web.servlet;`

`import javax.servlet.http.HttpServletRequest;`
`import javax.servlet.http.HttpServletResponse;`

`public interface ThemeResolver {`
`    String resolveThemeName(HttpServletRequest request);`

`    void setThemeName(HttpServletRequest request,`
`                      HttpServletResponse response,`
`                      String themeName);`

`}`

当您想要更改当前主题时，会调用 `setThemeName` 方法。如果不支持更改主题，则会抛出 `java.lang.UnsupportedOperationException`。当 Spring 框架需要解析当前主题时，会调用 `resolveThemeName` 方法。这主要通过使用主题 `jsp` 标签来完成。

##### CookieThemeResolver

顾名思义，`org.springframework.web.servlet.theme.CookieThemeResolver` 使用 `javax.servlet.http.Cookie` 来存储要使用的主题。这在您希望应用程序尽可能无状态的情况下特别有用。实际值存储在客户端，并将在每次请求时发送给您。此解析器允许更改主题；您可以在第 6 章和第 8 章中找到更多信息。此解析器还允许您配置 cookie 的名称和要使用的主题 locale。

##### FixedThemeResolver

`org.springframework.web.servlet.theme.FixedThemeResolver` 是 `org.springframework.web.servlet.ThemeResolver` 最基本的实现。它允许您配置在整个应用程序中使用的主题。此配置是固定的；因此，无法更改。

##### SessionThemeResolver

`org.springframework.web.servlet.theme.SessionThemeResolver` 使用 `javax.servlet.http .HttpSession` 来存储主题的值。可以配置属性的名称以及默认主题。



#### HandlerExceptionResolver

在大多数情况下，您希望控制如何处理请求处理过程中发生的异常。为此，您可以使用 `HandlerExceptionResolver`。该 API（参见清单 4-18）包含一个单一方法，该方法由调度器 servlet 检测到的 `org.springframework.web.servlet.HandlerExceptionResolver` 调用。解析器可以选择自行处理异常，或者返回一个包含要渲染的视图和模型（通常包含抛出的异常）的 `org.springframework.web.servlet.ModelAndView`。

***清单 4-18.** HandlerExceptionResolver API*

`package org.springframework.web.servlet;`

`import javax.servlet.http.HttpServletRequest;`
`import javax.servlet.http.HttpServletResponse;`

`public interface HandlerExceptionResolver {`

`    ModelAndView resolveException(HttpServletRequest request,`
`                                  HttpServletResponse response,`
`                                  Object handler,`
`                                  Exception ex);`

`}`

图 4-16 展示了 Spring 框架提供的不同实现。每个实现的工作方式略有不同，其配置方式也各不相同（更多信息请参见第 6 章）。

![Image](img/9781430241553_Fig04-16.jpg)

***图 4-16.** HandlerExceptionResolver 实现*

`org.springframework.web.servlet.handler.HandlerExceptionResolverComposite` 是 Spring MVC 内部使用的一个实现。它用于将多个 `org.springframework.web.servlet.HandlerExceptionResolver` 实现链接在一起。此解析器不提供实际的实现或附加功能；相反，它仅充当多个实现（当配置了多个实现时）的包装器。

#### RequestToViewNameTranslator

当处理器未返回视图实现或视图名称，且未自行向客户端发送响应时，`org.springframework.web.servlet.RequestToViewNameTranslator` 会尝试根据传入请求确定视图名称。默认实现（参见图 4-17）`org.springframework.web.servlet.view.DefaultRequestToViewNameTranslator` 会简单地获取 URL，去除后缀和上下文路径，然后将剩余部分用作视图名称（例如，[`http://localhost:8080/bookstore/admin/index.html`](http://localhost:8080/bookstore/admin/index.html) 变为 `admin/index`）。您可以在第 8 章中找到有关视图的更多信息。

![Image](img/9781430241553_Fig04-17.jpg)

***图 4-17.** RequestToViewNameTranslator 层次结构*

`RequestToViewNameTranslator` API 如清单 4-19 所示。

***清单 4-19.** RequestToViewNameTranslator API*

`package org.springframework.web.servlet;`

`import javax.servlet.http.HttpServletRequest;`

`public interface RequestToViewNameTranslator {`

`    String getViewName(HttpServletRequest request) throws Exception;`

`}`

#### ViewResolver

Spring MVC 提供了一种非常灵活的视图解析机制。它简单地获取从处理器返回的视图名称，并尝试将其解析为实际的视图实现（如果未返回具体的 `org.springframework.web.servlet.View`）。实际的实现可以是 JSP，但同样也可以是 Excel 电子表格或 PDF 文件。有关视图解析的更多信息，请参阅第 8 章。

此 API（参见清单 4-20）非常简单，仅包含一个方法。该方法接收视图名称和当前选定的区域设置（另请参阅 `LocaleResolver`）。这可用于解析为实际的 `View` 实现。当配置了多个 `org.springframework.web.servlet.ViewResolver` 时，调度器 servlet 会依次调用它们，直到其中一个返回要渲染的 `View`。

***清单 4-20.** ViewResolver API*

`package org.springframework.web.servlet;`

`import java.util.Locale;`

`public interface ViewResolver {`

`    View resolveViewName(String viewName, Locale locale) throws Exception;`

`}`

`ViewResolver` 的实现如图 4-18 所示。Spring 开箱即用地提供了多种实现（更多信息请参见第 8 章）。

![Image](img/9781430241553_Fig04-18.jpg)

***图 4-18.** ViewResolver 实现*

#### FlashMapManager

`org.springframework.web.servlet.FlashMapManager` 用于在 Spring MVC 应用程序中启用 flash “作用域”。您可以使用此机制将属性放入 flash 映射中，这些属性在重定向后被检索（flash 映射在一次请求/响应周期中存活）。视图渲染后，flash 映射会被清除。Spring 提供了一个单一实现 `org.springframework.web.servlet.support.SessionFlashMapManager`（参见图 4-19）。

![Image](img/9781430241553_Fig04-19.jpg)

***图 4-19.** FlashMapManager 层次结构*

清单 4-21 展示了 FlashMapManager 的 API。

![Image](img/square.jpg) **注意** 此处提到的 flash “作用域”与 Spring Web Flow 中使用的“flashscope”无关。

***清单 4-21.** FlashMapManager API*

`package org.springframework.web.servlet;`

`import javax.servlet.http.HttpServletRequest;`
`import javax.servlet.http.HttpServletResponse;`

`public interface FlashMapManager {`

`    FlashMap retrieveAndUpdate(HttpServletRequest request,`
`                               HttpServletResponse response);`

`    void saveOutputFlashMap(FlashMap flashMap,`
`                            HttpServletRequest request,`
`                            HttpServletResponse response);`
`}`

### 总结

本章首先探讨了请求处理工作流，确定了在此过程中起作用的组件。`DispatcherServlet` 可被视为 Spring MVC 中的主要组件，它扮演着最关键的**前端控制器**角色。Spring MVC 中的 MVC 模式非常明确：您拥有一个 `Model`、一个 `View` 以及一个 `Controller`（处理器）。处理器处理请求、填充模型并选择要渲染的视图。

在处理请求时，`DispatcherServlet` 使用许多不同的组件来发挥其作用。最重要的组件是 `HandlerMapping` 和 `HandlerAdapter`；这些组件分别是用于映射和处理请求的核心组件。要应用横切关注点，您可以使用 `HandlerInterceptor`。处理请求后，需要渲染视图。处理器可以返回一个 `View` 或要渲染的视图名称。在后一种情况下，此名称会传递给 `ViewResolver` 以解析为实际的视图实现。

此外，还提供了对 flash 作用域变量的基本支持。为此，引入了 `FlashMapManager` 的概念。有时，请求处理不会按您期望的方式进行。例如，您可能会遇到异常。为了处理这些异常，您可以使用 `HandlerExceptionResolver`。在此过程中起作用的最后两个组件是 `LocaleResolver` 和 `ThemeResolver`。它们共同在您的应用程序中实现了国际化和主题化。

本章还提到了调度器 servlet 包含一些默认配置的组件，并将这些默认组件与 Spring @MVC 的默认组件进行了对比，后者可以通过使用 `@EnableWebMvc` 注解来启用。我们还探讨了这两种方法的不同默认组件。

接下来的章节将解释如何构建控制器来处理请求，并更深入地探讨如何配置 Spring MVC。

## 第 5 章



## 实现控制器

控制器在 Web 应用中扮演着至关重要的角色：它们执行实际的请求、准备模型并选择要渲染的视图。与调度器 Servlet 协同工作时，控制器在请求处理流程中也发挥着关键作用。控制器是核心应用与应用 Web 界面之间的粘合剂。在本章中，我们将探讨两种不同的控制器方法，并介绍 Spring 框架提供的开箱即用实现。

本章还将介绍请求处理的支持组件。例如，我们将涵盖表单提交以及如何应用国际化（I18N）。

### 控制器简介

控制器是负责响应用户操作的组件。该操作可以是表单提交、点击链接或仅访问页面。控制器选择或更新视图所需的数据。它还选择要渲染的视图名称，或者可以直接渲染视图本身。使用 Spring MVC，我们在编写控制器时有两种选择。我们可以实现一个接口，或者在类上添加注解。接口是 `org.springframework.web.servlet.mvc.Controller`，注解是 `org.springframework.stereotype.Controller`。本书主要关注基于注解的方法（即 Spring @MVC）来编写控制器。不过，我们认为仍有必要提及基于接口的方法。

尽管两种方法都能实现控制器，但它们之间存在两个主要区别。第一个区别在于灵活性，第二个区别在于 URL 到控制器的映射。基于注解的控制器允许非常灵活的方法签名，而基于接口的方法则必须在接口上实现预定义的方法。获取其他有用的协作者会更加困难（但并非不可能！）。

对于基于接口的方法，我们必须显式地将 URL 外部映射到这些控制器；通常，这种方法会与 `org.springframework.web.servlet.handler.SimpleUrlHandlerMapping` 结合使用，以便将所有 URL 集中在一个位置。将所有 URL 集中在一个位置是基于接口的方法相对于基于注解的方法的一个优势。基于注解的方法的映射分散在整个代码库中，这使得查看哪个 URL 映射到哪个请求处理方法变得更加困难。基于注解的控制器的优势在于，当你打开一个控制器时，你可以看到它映射到了哪些 URL。

在本节中，我们将展示如何编写这两种类型的控制器，以及如何配置基本的视图控制器。

#### 基于接口的控制器

要编写基于接口的控制器，我们需要创建一个实现 `Controller` 接口的类。清单 5-1 展示了该接口的 API。实现此接口时，我们必须实现 `handleRequest` 方法。该方法需要返回一个 `org.springframework.web.servlet.ModelAndView` 对象，如果控制器自行处理响应，则返回 `null`。

***清单 5-1.** Controller 接口*

`package org.springframework.web.servlet.mvc;`

`import javax.servlet.http.HttpServletRequest;`
`import javax.servlet.http.HttpServletResponse;`

`import org.springframework.web.servlet.ModelAndView;`

`public interface Controller {`
`    ModelAndView handleRequest(HttpServletRequest request,`
`                               HttpServletResponse response) throws Exception;`
`}`

让我们看一个小例子。如果我们以 `com.apress.prospringmvc.bookstore.web.IndexController` 为例，并从中创建一个基于接口的控制器，它将类似于清单 5-2 所示。我们实现了 `handleRequest` 方法，并返回一个带有视图名称的 `ModelAndView` 实例。

***清单 5-2**. 基于接口的 IndexController*

`package com.apress.prospringmvc.bookstore.web;`
`// 省略 javax.servlet 导入`
`import org.springframework.web.servlet.ModelAndView;`
`import org.springframework.web.servlet.mvc.Controller;`

`public class IndexController implements Controller {`

`    @Override`
`    public ModelAndView handleRequest(HttpServletRequest request,`
`                                      HttpServletResponse response)`
`    throws Exception {`
`        return new ModelAndView("index");`
`    }`
`}`

除了编写此控制器外，我们还需要配置一个 `org.springframework.web.servlet.HandlerMapping` 实例，将 `/index.htm` 映射到此控制器（更多信息请参见第 3 章）。我们还需要确保注册了一个 `org.springframework.web.servlet.mvc.SimpleControllerHandlerAdapter` 来执行基于接口的控制器（默认情况下已注册）。

这里给出的示例相当简单。现在想象一个具有某些页面流程的控制器。在这种情况下，我们需要检查请求是 `GET` 还是 `POST`；基于此，我们需要执行不同的控制器逻辑。对于大型控制器来说，这可能会变得相当繁琐。

表 5-1 显示了框架附带的 `Controller` 实现。其中许多已弃用（自 Spring 3.0 起），或者可以被视为已弃用，转而支持更新的基于注解的控制器。请查看每个控制器的描述以了解弃用说明。

![图片](img/tab_5_1.jpg)

![图片](img/tab_5_1a.jpg)

#### 基于注解的控制器

要编写基于注解的控制器，我们需要编写一个类，并在该类上添加 `Controller` 注解。此外，我们需要在类、方法或两者上添加 `org.springframework.web.bind.annotation.RequestMapping` 注解。清单 5-3 展示了我们 `IndexController` 的基于注解的方法。

***清单 5-3.** 基于注解的 IndexController*

`package com.apress.prospringmvc.bookstore.web;`

`import org.springframework.stereotype.Controller;`
`import org.springframework.web.bind.annotation.RequestMapping;`
`import org.springframework.web.servlet.ModelAndView;`

`@Controller`
`public class IndexController {`

`    @RequestMapping(value = "/index.htm")`
`    public ModelAndView indexPage() {`
`        return new ModelAndView("index");`
`    }`
`}`

该控制器包含一个带有 `RequestMapping` 注解的方法，并指定它应映射到 `/index.htm` URL，这就是请求处理方法。该方法没有必需的参数，我们可以返回任何我们想要的内容；目前，我们想要返回一个 `ModelAndView`。

映射位于控制器定义中，我们需要一个 `HandlerMapping` 实例来解释这些映射。这里有两个实现可以帮助我们：`org.springframework.web.servlet.mvc.annotation.DefaultAnnotationHandlerMapping` 和 `org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerMapping`。第一个是由 `org.springframework.web.servlet.DispatcherServlet` 注册的默认实现之一。第二个是由 Spring @MVC（我们通过 `org.springframework.web.servlet.config.EnableWebMvc` 注解启用）注册的默认实现之一。我们将使用 Spring @MVC 默认实现，因为它比 `DefaultAnnotationHandlerMapping` 更强大、更灵活。我们将在本书中看到这种强大和灵活性。



#### 配置视图控制器

到目前为止，我们编写的两个控制器示例被称为*视图控制器*。它们不选择数据，而只选择要渲染的视图名称。如果我们的应用程序规模较大，包含更多此类视图，那么维护和编写这些控制器将变得相当繁琐。Spring MVC 可以在这方面帮助我们，使我们能够简单地将一个 `org.springframework.web.servlet.mvc.ParameterizableViewController` 添加到配置中，并对其进行相应配置。我们需要配置一个实例，使其返回 `index` 作为视图名称，并将其映射到 `/index.htm` URL。清单 5-4 展示了实现此功能所需添加的内容。

***清单 5-4.** 一个 ParameterizableViewController 配置*

`package com.apress.prospringmvc.bookstore.web.config;`

`import org.springframework.web.servlet.mvc.ParameterizableViewController;`
`import org.springframework.web.servlet.config.annotation.WebMvcConfigurerAdapter;`

`// 其他导入已省略`

`@Configuration`
`@EnableWebMvc`
`@ComponentScan(basePackages = { "com.apress.prospringmvc.bookstore.web" })`
`public class WebMvcContextConfiguration extends WebMvcConfigurerAdapter {`

`    // 其他方法已省略`

**`@Bean(name = "/index.htm")`**
`    public Controller index() {`
`        ParameterizableViewController index;`
`        index = new ParameterizableViewController();`
**`index.setViewName("index");`**
`        return index;`
`    }`
`}`

那么它是如何工作的呢？我们创建控制器，设置要返回的视图名称，然后显式地给它命名为 `/index.htm`（参见高亮部分）。这种显式命名使得 `org.springframework.web.servlet.handler.BeanNameUrlHandlerMapping` 能够识别我们的控制器并将其映射到该 URL。然而，如果这种方法需要大幅扩展，那么我们将需要创建相当多的此类方法。同样，Spring MVC 可以在这里帮助我们。由于我们已经启用了新的 Spring MVC 配置，我们可以利用它来发挥优势。我们可以重写 `addViewControllers` 方法（`org.springframework.web.servlet.config.annotation .WebMvcConfigurerAdapter` 的方法之一），并简单地将我们的视图名称注册到特定的 URL。清单 5-5 展示了如何做到这一点。

***清单 5-5.** 一个 ViewController 配置*

`package com.apress.prospringmvc.bookstore.web.config;`

`import org.springframework.web.servlet.config.annotation.ViewControllerRegistry;`
`import org.springframework.web.servlet.config.annotation.WebMvcConfigurerAdapter;`

`// 其他导入已省略`

`@Configuration`
`@EnableWebMvc`
`@ComponentScan(basePackages = { "com.apress.prospringmvc.bookstore.web" })`
`public class WebMvcContextConfiguration extends WebMvcConfigurerAdapter {`

`  // 其他方法已省略`
`    @Override`
`    public void addViewControllers(final ViewControllerRegistry registry) {`
`        registry.addViewController("/index.htm").setViewName("index");`
`    }`
`}`

结果是相同的。一个 `ParameterizableViewController` 被创建并映射到 `/index.htm` URL（参见图 5-1）。然而，第二种方法比第一种更简单，使用起来也更不繁琐。

![Image](img/9781430241553_Fig05-01.jpg)

***图 5-1.** 索引页面*

### 请求处理方法

编写请求处理方法可能是一个挑战。例如，如何将一个方法映射到传入的请求？这里可能涉及多个因素，包括 URL、使用的 HTTP 方法（例如 `GET` 或 `POST`^(1)）、参数或 HTTP 头^(2) 的可用性，甚至是要生成的请求内容类型或内容类型（例如 XML、JSON 或 HTML）。所有这些都会影响选择哪个方法来处理请求。

编写请求处理方法的第一步是在方法上添加 `org.springframework.web.bind.annotation.RequestMapping` 注解。这个映射会被 `org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerMapping` 检测到，以创建传入 URL 到正确方法的映射（有关处理器映射的更多信息，请参见第 4 章中的“Spring MVC 组件”部分）。接下来，我们需要指定要执行指定处理器的 Web 请求。

该注解可以放在类型（控制器）和方法级别上。我们可以使用类型级别的注解进行粗粒度映射（例如 URL），然后使用方法级别的注解进一步指定何时执行该方法（例如 `GET` 或 `POST` 请求）。

表 5-2 显示了我们可以为 `RequestMapping` 注解设置哪些属性，以及它们如何影响映射。

![Image](img/tab_5_2.jpg)

![Image](img/tab_5_2a.jpg)

________________

¹ [`http://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html`](http://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)

² [`http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html`](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html)

在表 5-3 中，有几个示例映射也展示了类级别和方法级别匹配的效果。如前所述，类上的 `RequestMapping` 注解适用于控制器中的*所有*方法。这种机制可用于在类级别进行粗粒度映射，在方法级别进行更细粒度的映射。

![Image](img/tab_5_3.jpg)

![Image](img/tab_5_3a.jpg)

________________

³ [`http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html`](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html)

#### 支持的方法参数类型

一个请求处理方法可以有多种方法参数和返回值。表 5-4 中提到的大多数参数可以按任意顺序使用。但有一个例外：`org.springframework.validation.BindingResult` 参数。该参数必须跟在用于将请求参数绑定到其上的模型对象之后。

![Image](img/tab_5_4.jpg)

![Image](img/tab_5_4a.jpg)

![Image](img/tab_5_4b.jpg)

##### RedirectAttributes

`org.springframework.web.servlet.mvc.support.RedirectAttributes` 值得比表 5-4 中显示的更详细的解释。使用 `RedirectAttributes`，可以精确声明重定向所需的属性。默认情况下，执行重定向时会暴露所有模型属性。由于重定向总是导致 `GET` 请求，所有原始模型属性（或原始类型的集合/数组）都将被编码为请求参数。然而，在使用注解的控制器中，模型中还存在一些不需要暴露且不受我们控制的对象（如路径变量和其他隐式值）。

`RedirectAttributes` 可以在这里帮助我们。当它被用作方法参数并且正在发出重定向时，只有添加到 `RedirectAttributes` 实例中的属性才会被添加到 URL 中。

除了指定编码在 URL 中的属性之外，还可以指定所谓的闪存属性。*闪存属性*是在重定向之前存储，并在重定向之后检索并作为模型属性可用的属性。这是通过使用配置的 `org.springframework.web.servlet.FlashMapManager` 来完成的。使用闪存属性对于无法编码的对象（非原始对象）或保持 URL 整洁非常有用。



##### UriComponentsBuilder

`UriComponentsBuilder` 提供了一种构建和编码 URI 的机制。它可以接受一个 URL 模式，并替换或扩展其中的变量。这可以用于相对或绝对 URL。与需要自行考虑编码参数或进行字符串拼接的情况相比，这种机制在创建 URL 时特别有用。该组件为我们统一处理了所有这些事项。代码清单 5-6 中的代码创建了 `/book/detail/42` 这个 URL。

***代码清单 5-6.** UriComponentsBuilder 示例代码*

`UriComponentsBuilder.fromPath("/book/detail/{bookId}");`
`.build();`
`.expand("42")`
`.encode()`

给出的示例相当简单；然而，我们可以指定更多变量（例如 `bookId`）并替换它们（例如指定 `port` 或 `host`）。还有一个 `ServletUriComponentsBuilder` 子类，我们可以用它来操作当前请求。例如，我们可能用它来替换路径变量，以及请求参数。

#### 支持的方法参数注解

除了显式支持的类型（如上一节所述），还有一些注解可以用来标注我们的方法参数（参见表 5-5）。其中一些注解也可以与表 5-4 中提到的方法参数类型一起使用。在这种情况下，它们用于指定请求、Cookie、请求头或响应中属性的名称，以及该参数是否为必需的。

所有参数值都通过类型转换转换为参数类型。类型转换系统使用 `org.springframework.core.convert.converter.Converter` 或 `PropertyEditor` 将 `String` 类型转换为实际类型。

![Image](img/tab_5_5.jpg)

所有这些不同的方法参数类型和注解使我们能够编写非常灵活的请求处理方法。然而，我们可以通过扩展框架来扩展此机制。解析这些方法参数类型是由 `org.springframework.web.method.support.HandlerMethodArgumentResolver` 的各种实现完成的。代码清单 5-7 展示了该接口。如果需要，我们可以创建此接口的自定义实现，并将其注册到框架中。你可以在第 7 章中找到更多相关信息。

***代码清单 5-7.** HandlerMethodArgumentResolver 接口*

`package org.springframework.web.method.support;`

`import org.springframework.core.MethodParameter;`
`import org.springframework.web.bind.WebDataBinder;`
`import org.springframework.web.bind.support.WebDataBinderFactory;`
`import org.springframework.web.context.request.NativeWebRequest;`

`public interface HandlerMethodArgumentResolver {`

`    boolean supportsParameter(MethodParameter parameter);`

`    Object resolveArgument(MethodParameter parameter,`
`                           ModelAndViewContainer mavContainer,`
`                           NativeWebRequest webRequest,`
`                           WebDataBinderFactory binderFactory)`
`                               throws Exception;`
`}`

________________

⁴ [`http://en.wikipedia.org/wiki/List_of_HTTP_header_fields`](http://en.wikipedia.org/wiki/List_of_HTTP_header_fields)

让我们仔细看看我们可以使用的所有不同注解类型。所有这些注解都有一些我们可以设置的属性，这些属性可能有默认值，也可能是必需的。

表 5-5 中提到的所有注解都有一个 `value` 属性。此 `value` 属性指的是要使用的对象的名称（其具体作用取决于注解）。如果未填写此值，则回退为使用方法参数的名称。此回退仅在类使用调试信息编译时才可用。^(5) 使用 `ModelAttribute` 注解时是一个例外。它不会使用方法参数的名称，而是从参数类型推断名称，使用简单的类名作为参数名称。如果类型是数组或集合，则通过添加 `List` 使其变为复数形式。如果我们使用 `com.apress.prospringmvc.bookstore.domain.Book` 作为参数，那么名称将是 `book`；如果是数组或集合，则变为 `bookList`。

##### RequestParam

`RequestParam` 注解可以放在请求处理方法的任何参数上。当存在时，它用于从请求中检索参数。当放在 `Map` 上时，根据是否设置了 `name` 属性，会有一些特殊处理。如果设置了名称，则检索该值并将其转换为 `Map`。对于转换（更多信息请参阅本章的“数据绑定”部分），如果未指定名称，则所有请求参数都将作为键/值对添加到映射中。

![Image](img/tab_5_6.jpg)

________________

⁵ [`http://docs.oracle.com/javase/6/docs/technotes/tools/windows/javac.html`](http://docs.oracle.com/javase/6/docs/technotes/tools/windows/javac.html)

##### RequestHeader

`RequestHeader` 注解可以放在任何方法参数上。它用于将方法参数绑定到请求头。当放在 `Map` 上时，所有可用的请求头都将作为键/值对放入映射中。如果放在其他类型的参数上，则使用 `org.springframework.core.convert.converter.Converter` 或 `PropertyEditor` 将该值转换为该类型（更多信息请参阅“数据绑定”部分）。

![Image](img/tab_5_7.jpg)

##### RequestBody

`RequestBody` 注解用于标记我们想要绑定到 Web 请求正文的方法参数。通过查找并调用 `org.springframework.http.converter.HttpMessageConverter`，将正文转换为方法参数类型。此转换器根据请求的内容类型进行选择。如果未找到转换器，则会抛出 `org.springframework.web.HttpMediaTypeNotSupportedException`。默认情况下，这会导致向客户端发送状态码为 415 (SC_UNSUPPORTED_MEDIA_TYPE) 的响应。

可选地，方法参数还可以使用 `javax.validation.Valid` 或 `org.springframework.validation.annotation.Validated` 进行注解，以对创建的对象强制执行验证。有关验证的更多信息，请参阅本章后面的“模型属性验证”部分。

##### RequestPart

当 `RequestPart` 注解放在类型为 `javax.servlet.http.Part`、`org.springframework.web.multipart.MultipartFile`（或后者的集合或数组）的方法参数上时，我们将获得该文件（或文件组）的内容注入。如果放在任何其他参数类型上，则内容会通过 `org.springframework.http.converter.HttpMessageConverter` 进行处理，该转换器针对文件检测到的内容类型。如果未找到合适的转换器，则会抛出 `org.springframework.web.HttpMediaTypeNotSupportedException`。

![Image](img/tab_5_8.jpg)

##### ModelAttribute

`ModelAttribute` 注解可以放在方法参数上，也可以放在方法上。当放在方法参数上时，它用于将此参数绑定到模型对象。当放在方法上时，该方法用于构造模型对象，并且该方法将在调用请求处理方法之前被调用。这类方法可用于创建要在表单中编辑的对象，或提供表单自身渲染所需的数据。（更多信息请参阅“数据绑定”部分。）

![Image](img/tab_5_9.jpg)



##### PathVariable

`PathVariable`注解可与路径变量结合使用。路径变量可用于 URL 模式中，将 URL 绑定到一个变量上。在 URL 映射中，路径变量表示为`{`*`name`*`}`。如果我们使用`/book/{isbn}/image`这样的 URL 映射，那么`isbn`将作为一个路径变量可用。

![Image](img/tab_5_10.jpg)

##### CookieValue

`CookieValue`注解可以放置在请求处理方法中的任何参数上。当存在该注解时，它用于检索一个 cookie。当放置在类型为`javax.servlet.http.Cookie`的参数上时，我们会获取完整的 cookie。否则，cookie 的值会被转换为参数类型。

![Image](img/tab_5_11.jpg)

#### 支持的方法返回值

除了所有不同的方法参数类型外，请求处理方法还可以具有多种不同的返回值之一。表 5-12 列出了请求处理方法默认支持的方法返回值及其处理方式。

![Image](img/tab_5_12.jpg)

![Image](img/tab_5_12a.jpg)

当返回一个任意对象且没有`ModelAttribute`注解时，框架会尝试确定一个名称，用作该对象在模型中的名称。它基本上会取类的简单名称（不含包名的类名），并将首字母小写。例如，我们的`com.apress.prospringmvc.bookstore.domain.Book`类的名称变为`book`。当返回类型是集合或数组时，它会变成类的简单名称，并加上`List`后缀。因此，一个`Book`对象的集合会变成`bookList`。

当我们使用`Model`或`ModelMap`添加对象而没有显式名称时，也会应用相同的逻辑。这样做还有一个好处，即可以使用特定对象，而不是普通的`Map`来访问底层的隐式模型。

尽管支持的返回值列表已经相当广泛，但我们仍可以利用框架的灵活性和可扩展性来创建自己的处理器。方法的返回值由`org.springframework.web.method.support.HandlerMethodReturnValueHandler`接口的实现来处理（见清单 5-8）。

***清单 5-8.** HandlerMethodReturnValueHandler 接口*

`package org.springframework.web.method.support;`

`import org.springframework.core.MethodParameter;`
`import org.springframework.web.context.request.NativeWebRequest;`

`public interface HandlerMethodReturnValueHandler {`

`    boolean supportsReturnType(MethodParameter returnType);`

`    void handleReturnValue(Object returnValue,`
`                           MethodParameter returnType,`
`                           ModelAndViewContainer mavContainer,`
`                           NativeWebRequest webRequest) throws Exception;`
`}`

### 编写基于注解的控制器

让我们将目前学到的部分理论应用到我们的控制器中。例如，我们页面上所有的菜单选项都会导致 404 错误，这表明页面无法找到。

在本节中，我们将为应用程序添加一些控制器和视图。我们将从创建一个简单的登录控制器开始，该控制器使用请求和请求参数进行操作。接下来，我们将添加一个使用对象的图书搜索页面。最后，我们将构建一个检索并显示图书详细信息的控制器来结束本节。

#### 一个简单的登录控制器

在开始编写控制器之前，我们需要有一个登录页面。在`WEB-INF/views`目录中，我们创建一个名为`login.jsp`的文件。最终的结构应类似于图 5-2 所示。

![Image](img/9781430241553_Fig05-02.jpg)

***图 5-2.** 添加 login.jsp 后的目录结构*

登录页面需要一些内容，如清单 5-9 所示。

***清单 5-9.** 登录页面，Login.jsp*

`<%@taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core"%>`
`<%@taglib prefix="spring" uri="http://www.springframework.org/tags" %>`

`<c:if test="${exception ne null}">`
`    <div class="error">${exception.message}</div>`
`</c:if>`
`<form action="<c:url value="/login"/>" method="post">`
`    <fieldset>`
`        <legend>登录</legend>`
`        <table>`
`        <tr>`
`            <td>用户名</td>`
`            <td>`
`                <input type="text" id="username" name="username"`
`                        placeholder="用户名"/></td>`
`        </tr>`
`        <tr>`
`            <td>密码</td>`
`            <td>`
`                <input type="password" id="password" name="password"`
`                       placeholder="密码"/></td>`
`        </tr>`
`        <tr><td colspan="2" align="center">`
`            <button id="login">登录</button>`
`        </td></tr>`
`        </table>`
`    </fieldset>`
`</form>`

除了页面之外，我们还需要有一个控制器并将其映射到`/login`。让我们创建`com.apress.prospringmvc.bookstore.web.controller.LoginController`，并先让它渲染我们的页面（见清单 5-10）。

***清单 5-10**. 初始的 LoginController*

`package com.apress.prospringmvc.bookstore.web.controller;`

`import org.springframework.stereotype.Controller;`
`import org.springframework.web.bind.annotation.RequestMapping;`
`import org.springframework.web.bind.annotation.RequestMethod;`

`@Controller`
`@RequestMapping(value = "/login")`
`public class LoginController {`

`    @RequestMapping(method = RequestMethod.GET)`
`    public String login() {`
`        return "login";`
`    }`
`}`

在应用程序重新部署后，我们点击“登录”按钮，应该会看到一个类似图 5-3 所示的页面。

![Image](img/9781430241553_Fig05-03.jpg)

***图 5-3.** 登录页面*

如果我们现在输入用户名和密码（jd/secret）并按下“登录”按钮，我们会看到一个错误页面（错误代码 405），提示该方法（`POST`）不受支持。这是正确的，因为我们的控制器还没有处理`POST`请求的方法。那么，让我们为控制器添加一个实际处理登录的方法。清单 5-11 显示了修改后的控制器。

***清单 5-11**. 修改后的 LoginController*

`package com.apress.prospringmvc.bookstore.web.controller;`

`// 其他导入已省略，见``清单 5-10`

`import org.springframework.beans.factory.annotation.Autowired;`

`import com.apress.prospringmvc.bookstore.domain.Account;`
`import com.apress.prospringmvc.bookstore.service.AccountService;`
`import com.apress.prospringmvc.bookstore.service.AuthenticationException;`

`@Controller`
`@RequestMapping(value = "/login")`
`public class LoginController {`

`    public static final String ACCOUNT_ATTRIBUTE = "account";`

`    @Autowired`
`    private AccountService accountService;`

`    @RequestMapping(method = RequestMethod.GET)`
`    public String login() {`
`        return "login";`
`    }`



`    @RequestMapping(method = RequestMethod.POST)`
`    public String handleLogin(HttpServletRequest request, HttpSession session)`
`    throws AuthenticationException {`
`        try {`
`            String username = request.getParameter("username");`
`            String password = request.getParameter("password");`
`            Account account = this.accountService.login(username, password);`
`            session.setAttribute(ACCOUNT_ATTRIBUTE, account);`
`            return "redirect:/index.htm";`
`        } catch (AuthenticationException ae) {`
`            request.setAttribute("exception", ae);`
`            return "login";`
`        }`
`    }`
`}`

在继续之前，让我们深入探讨一下 `handleLogin` 方法的工作原理。从请求中获取 `username` 和 `password` 参数，然后使用它们调用 `AccountService` 上的 `login` 方法。如果提供了正确的凭据，我们会获得一个用户的 `Account` 实例（将其存储在会话中），然后重定向到索引页面。如果凭据不正确，服务会抛出 `AuthenticationException`，目前由控制器处理。该异常被存储为 `request` 属性，并将用户返回到登录页面。

尽管当前控制器能够完成其工作，但我们仍然直接操作 `HttpServletRequest`。这是一种相当繁琐（但有时是必要的）的方法；然而，我们通常希望避免这种情况，并使用灵活的方法签名来简化控制器。考虑到这一点，让我们修改控制器，限制直接访问请求的使用（参见清单 5-12）。

***清单 5-12.** 使用 RequestParam 的 LoginController*

`package com.apress.prospringmvc.bookstore.web.controller;`

`import org.springframework.web.bind.annotation.RequestParam;`

`// 其他导入已省略，参见` `清单 5-11`

`@Controller`
`@RequestMapping(value = "/login")`
`public class LoginController {`

`    // 其他方法已省略`

`    @RequestMapping(method = RequestMethod.POST)`
`    public String handleLogin(@RequestParam String username,`
`                              @RequestParam String password,`
`                              HttpServletRequest request,`
`                              HttpSession session)`
`            throws AuthenticationException {`
`        try {`
`            Account account = this.accountService.login(username, password);`
`            session.setAttribute(ACCOUNT_ATTRIBUTE, account);`
`            return "redirect:/index.htm";`
`        } catch (AuthenticationException ae) {`
`            request.setAttribute("exception", ae);`
`            return "login";`
`        }`
`    }`
`}`

使用 `RequestParam` 注解简化了我们的控制器。然而，我们的异常处理要求我们仍然需要访问请求。这将在下一章实现异常处理时发生变化。

这种方法仍然有一个缺点，那就是我们缺乏对浏览器“返回”按钮的支持。如果我们返回上一页，会弹出一个提示框，询问是否要重新提交表单。在 `POST`^(6) 请求之后进行重定向是一种常见的方法；这样，我们可以解决重复提交的问题。在 Spring 中，我们可以通过使用 `RedirectAttributes` 来解决这个问题。清单 5-13 以粗体突出显示了我们对控制器所做的最终修改。

***清单 5-13.** 使用 RedirectAttributes 的 LoginController*

`package com.apress.prospringmvc.bookstore.web.controller;`

`// 其他导入已省略，参见` `清单 5-11`

`import org.springframework.web.servlet.mvc.support.RedirectAttributes;`

`@Controller`
`@RequestMapping(value = "/login")`
`public class LoginController {`

`    // 其他方法已省略`

`    @RequestMapping(method = RequestMethod.POST)`
`    public String handleLogin(@RequestParam String username,`![Image](img/U002.jpg)
`                              @RequestParam String password,`![Image](img/U002.jpg)
**`RedirectAttributes redirect,`![Image](img/U002.jpg)**
`HttpSession session)` ![Image](img/U002.jpg)
`    throws AuthenticationException {`
`        try {`
`            Account account = this.accountService.login(username, password);`
`            session.setAttribute(ACCOUNT_ATTRIBUTE, account);`
`            return "redirect:/index.htm";`
`        } catch (AuthenticationException ae) {`
**`redirect.addFlashAttribute("exception", ae);`**
**`return "redirect:/login";`**
`        }`
`    }`
`}`

________________

⁶ [`www.theserverside.com/news/1365146/Redirect-After-Post`](http://www.theserverside.com/news/1365146/Redirect-After-Post)

当应用程序重新部署并登录时，输入错误的用户名/密码组合仍然会引发错误消息；但是，当我们按下“返回”按钮时，表单提交的弹出提示框就消失了。

到目前为止，我们所做的一切都相当底层。我们的解决方案包括直接处理请求和/或响应，或者通过 `org.springframework.web.bind.annotation.RequestParam` 进行一定程度的抽象。然而，我们使用的是面向对象的编程语言，在可能的情况下，我们希望使用对象进行操作。我们将在下一节中探讨这一点。



#### 图书搜索页面

我们经营一家书店，想要销售图书。但目前，我们的 Web 应用中没有任何功能允许用户搜索甚至查看图书列表。让我们通过创建一个图书搜索页面来解决这个问题，这样应用的用户就可以搜索图书了。

首先，在 `/WEB-INF/views` 目录下创建一个 `book` 目录。在该目录中，创建一个名为 `search.jsp` 的文件。这个文件是我们的搜索表单，它也将显示搜索结果。该文件的代码见清单 5-14。

***清单 5-14.** 搜索页面表单*

`<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core"%>`

`<form method="GET" action="<c:url value="/book/search"/>">`
`    <fieldset>`
`        <legend>搜索条件</legend>`
`        <table>`
`            <tr>`
`                <td><label for="title">书名</label></td>`
`                <td><input id="title" name="title" /></td>`
`            </tr>`
`        </table>`
`    </fieldset>`
`    <button id="search">搜索</button>`
`</form>`

`<c:if test="${not empty bookList}">`
`    <table>`
`        <tr><th>书名</th><th>描述</th><th>价格</th></tr>`
`        <c:forEach items="${bookList}" var="book">`
`            <tr>`
`                <td>${book.title}</td>`
`                <td>${book.description}</td>`
`                <td>${book.price}</td>`
`            </tr>`
`        </c:forEach>`
`    </table>`
`</c:if>`

该页面由一个表单组成，表单中有一个字段用于填写（部分）书名，用于搜索图书。当有结果时，我们会向用户显示一个包含结果的表格。现在我们已经有了页面，还需要一个能够处理请求的控制器。清单 5-15 展示了初始的 `com.apress.prospringmvc.bookstore.web.controller.BookSearchController`。

***清单 5-15.** 包含搜索功能的 BookSearchController*

`package com.apress.prospringmvc.bookstore.web.controller;`

`import org.springframework.beans.factory.annotation.Autowired;`
`import org.springframework.stereotype.Controller;`
`import org.springframework.ui.Model;`
`import org.springframework.web.bind.annotation.RequestMapping;`
`import org.springframework.web.bind.annotation.RequestMethod;`

`import com.apress.prospringmvc.bookstore.domain.BookSearchCriteria;`
`import com.apress.prospringmvc.bookstore.service.BookstoreService;`

`import javax.servlet.http.HttpServletRequest`

`@Controller`
`public class BookSearchController {`

`    @Autowired`
`    private BookstoreService bookstoreService;`

`    @RequestMapping(value = "/book/search", method = RequestMethod.GET)`
`    public String list(Model model, HttpServletRequest request) {`
`        BookSearchCriteria criteria = new BookSearchCriteria();`
`        criteria.setTitle(request.getParameter("title");`
`        model.addAttribute(this.bookstoreService.findBooks(criteria));`
`        return "book/search";`
`    }`
`}`

控制器将响应 URL；从请求中获取 title 参数（这是页面中字段的名称，如清单 5-13 所示）；最后，执行搜索。搜索结果被放入模型中。初始状态下，它会显示所有图书；然而，一旦输入了书名，它就会根据该书名限制搜索结果（参见图 5-4）。

![Image](img/9781430241553_Fig05-04.jpg)

***图 5-4.** 显示搜索结果的图书搜索页面*

如前所述，在大多数情况下，直接使用 `HttpServletRequest` 并非必要。让我们通过将 `com.apress.prospringmvc.bookstore.domain.BookSearchCriteria` 放入方法参数列表来简化搜索方法（参见清单 5-16）。

***清单 5-16.** 以 BookSearchCriteria 作为方法参数的 BookSearchController*

`package com.apress.prospringmvc.bookstore.web.controller;`

`import org.springframework.web.bind.annotation.RequestParam;`

`// 其他导入已省略，参见` `清单 5-15`

`@Controller`
`public class BookSearchController {`

`    @Autowired`
`    private BookstoreService bookstoreService;`

`    @RequestMapping(value = "/book/search", method = RequestMethod.GET)`
`public String list(Model model,` **`BookSearchCriteria criteria`**`) {`
`        model.addAttribute(this.bookstoreService.findBooks(criteria));`
`        return "book/search";`
`    }`
`}`

在 Spring MVC 中，这就是我们所说的*数据绑定*。为了启用数据绑定，我们需要修改 `com.apress.prospring.bookstore.web.controller.BookSearchController`，使其使用方法参数，而不是直接使用 `request`（参见清单 5-14）。或者，它可以使用 `RequestParam` 来检索参数并将其设置到对象上。这将强制 Spring 对 `criteria` 方法参数进行数据绑定。这样做会将所有与对象属性同名的请求参数映射到该对象（即，请求参数 `title` 将被映射到属性 `title`）。使用数据绑定将大大简化我们的控制器（你可以在本章的“数据绑定”部分找到更深入的信息）。

我们甚至可以做得更好！除了返回 `String`，我们还可以返回其他内容。例如，让我们修改控制器以返回一个图书集合。这个集合会以 `bookList` 的名称添加到模型中，正如本章前面所述。清单 5-16 展示了这个控制器，但是我们如何选择要渲染的视图呢？它并没有明确指定。在第 4 章中，我们提到如果没有明确指定要渲染的视图，`org.springframework.web.servlet.RequestToViewNameTranslator` 就会介入。我们在这里看到了这个机制的工作原理。它获取 URL（`http://[server]:[port]/chapter5-bookstore/book/search`）；剥离服务器、端口和应用名称；移除后缀（如果有）；然后使用剩余的 `book/search` 作为要渲染的视图名称（这正是我们一直在返回的内容）。

***清单 5-17.** BookSearchController 的替代版本*

`package com.apress.prospringmvc.bookstore.web.controller;`

`// 其他导入已省略，参见` `清单 5-15`

`@Controller`
`public class BookSearchController {`

`    @Autowired`
`    private BookstoreService bookstoreService;`

`    @RequestMapping(value = "/book/search", method = RequestMethod.GET)`
`public` **`Collection<Book>`** `list(BookSearchCriteria criteria ) {`
**`return this.bookstoreService.findBooks(criteria);`**
`    }`
`}`



#### 图书详情页面

现在让我们为搜索页面添加更多功能。例如，将图书标题设为链接，点击后可导航至图书详情页面，该页面会显示图书图片及相关信息。我们首先修改 `search.jsp` 并添加链接（参见清单 5-18）。

***清单 5-18.** 修改后的搜索页面*

`<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core"%>`

`<form method="POST" action="<c:url value="/book/search"/>">`
`    <fieldset>`
`        <legend>搜索条件</legend>`
`        <table>`
`            <tr>`
`                <td><label for="title">标题</label></td>`
`                <td><input id="title" name="title"/></td>`
`            </tr>`
`        </table>`
`    </fieldset>`
`    <button id="search">搜索</button>`
`</form>`

`<c:if test="${not empty bookList}">`
`    <table>`
`        <tr><th>标题</th><th>描述</th><th>价格</th></tr>`
`        <c:forEach items="${bookList}" var="book">`
`            <tr>`
`                <td>`**`<a href="<c:url value="/book/detail/${book.id}"/>">${book.title}</a>`**`</td>`
`                <td>${book.description}</td>`
`                <td>${book.price}</td>`
`            </tr>`
`        </c:forEach>`
`    </table>`
`</c:if>`

高亮行是我们对此页面所做的唯一修改。至此，我们已根据图书的 `id` 生成了一个 URL，因此应得到类似 `/book/detail/4` 的 URL，用于显示 id 为 4 的图书详情。接下来，我们创建一个控制器来响应此 URL 并从 URL 中提取 id（参见清单 5-19）。

***清单 5-19.** BookDetailController*

`package com.apress.prospringmvc.bookstore.web.controller;`

`import org.springframework.beans.factory.annotation.Autowired;`
`import org.springframework.stereotype.Controller;`
`import org.springframework.ui.Model;`
`import org.springframework.web.bind.annotation.PathVariable;`
`import org.springframework.web.bind.annotation.RequestMapping;`

`import com.apress.prospringmvc.bookstore.domain.Book;`
`import com.apress.prospringmvc.bookstore.service.BookstoreService;`

`@Controller`
`public class BookDetailController {`

`    @Autowired`
`    private BookstoreService bookstoreService;`

`    @RequestMapping(value = "/book/detail/`**`{bookId}`**`")`
`    public String details`**`(@PathVariable("bookId") long bookId`**`, Model model) {`
`        Book book = this.bookstoreService.findBook(bookId);`
`        model.addAttribute(book);`
`        return "book/detail";`
`    }`
`}`

高亮代码实现了 id 的提取功能。这里使用了 `org.springframework.web.bind.annotation.PathVariable` 注解。URL 映射中包含 `{bookId}` 部分，这告诉 Spring MVC 将该部分 URL 绑定到名为 `bookId` 的路径变量。然后我们可以使用该注解再次获取路径变量。除了控制器之外，我们还需要一个 JSP 页面来显示详情。清单 5-20 中的代码在 `book` 目录下创建了一个 `detail.jsp` 文件。

***清单 5-20.** 图书详情页面*

`<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core"%>`

`<c:url value="/resources/images/books/${book.isbn}/book_front_cover.png" var="bookImage"/>`
`<img src="${bookImage}" align="left" alt="${book.title}" width="250"/>`

`<table>`
`    <tr><td>标题</td><td>${book.title}</td></tr>`
`    <tr><td>描述</td><td>${book.description}</td></tr>`
`    <tr><td>作者</td><td>${book.author}</td></tr>`
`    <tr><td>年份</td><td>${book.year}</td></tr>`
`    <tr><td>ISBN</td><td>${book.isbn}</td></tr>`
`    <tr><td>价格</td><td>${book.price}</td></tr>`
`</table>`

重新部署后，如果我们点击搜索页面中的某个链接，就会看到一个详情页面，其中显示图书图片及相关信息（参见图 5-5）。

![Image](img/9781430241553_Fig05-05.jpg)

***图 5-5.** 图书详情页面*

### 数据绑定

在本节中，我们将探讨使用数据绑定的好处和可能性，包括如何配置和扩展它。不过，我们首先会解释数据绑定的基础知识。清单 5-21 展示了我们的 `com.apress.prospringmvc.bookstore.domain.BookSearchCriteria` JavaBean。它是一个简单的对象，包含两个属性：`title` 和 `category`。

***清单 5-21.** BookSearchCriteria JavaBean*

`package com.apress.prospringmvc.bookstore.domain;`

`public class BookSearchCriteria {`

`    private String title;`
`    private Category category;`

`    public String getTitle() {`
`        return this.title;`
`    }`

`    public void setTitle(String title) {`
`        this.title = title;`
`    }`
`    public void setCategory(Category category) {`
`        this.category = category;`
`    }`

`    public Category getCategory() {`
`        return this.category;`
`    }`
`}`

假设我们收到以下请求：[`http://localhost:8080/chapter5-bookstore/book/search?title=Agile`](http://localhost:8080/chapter5-bookstore/book/search?title=Agile)。在这种情况下，`title` 属性会接收值 `Agile`。在幕后，Spring 会调用我们 JavaBean 上的 `setTitle` 方法，该方法被指定为控制器中 `list` 方法的一个参数。如果请求中存在名为 `category` 的参数，那么 Spring 会调用 `setCategory` 方法；不过，它会首先尝试将该参数（始终是 `String` 类型）转换为 `com.apress.prospring.bookstore.domain.Category` JavaBean。

然而，数据绑定并不仅限于简单的 setter 方法。我们还可以绑定到嵌套属性，甚至绑定到索引集合，如映射、数组和列表。当参数名包含*点号*（.）时，就会发生嵌套绑定；例如，`address.street=Somewhere` 会导致调用 `getAddress().setStreet("Somewhere")`。

要绑定到索引集合，我们必须使用方括号表示法，并在其中包含索引。当使用映射时，该索引不必是数字。例如，`list[2].name` 会将 name 属性绑定到列表中的第三个元素。类似地，`map['foo'].name` 会将 name 属性绑定到映射中键 `foo` 对应的值。

#### 自定义数据绑定

我们有两种方式来自定义数据绑定的行为：全局方式或每个控制器单独配置。当然，我们也可以将这两种策略结合起来：先进行全局设置，然后针对每个控制器进行微调。



##### 全局自定义

要实现全局数据绑定，我们需要创建一个实现了 `org.springframework.web.bind.support.WebBindingInitializer` 接口的类。Spring MVC 提供了该接口的一个可配置实现，即 `org.springframework.web.bind.support.ConfigurableWebBindingInitializer`。该接口的实例必须注册到处理器映射实现中，以便能够被使用。在创建了 `org.springframework.web.bind.WebDataBinder` 的实例后，会调用 `org.springframework.web.bind.support.WebBindingInitializer` 的 `initBinder` 方法。

提供的实现允许我们设置一些属性。当某个属性未设置时，它会使用 `org.springframework.web.bind.WebDataBinder` 指定的默认值。如果我们想要指定更多属性，扩展默认实现并添加所需行为是相当容易的。这里可以设置与控制器中相同的属性（参见表 5-13）。

![Image](img/tab_5_13.jpg)

当我们想要扩展 Spring @MVC 提供的配置时，需要进行一些扩展和重写。这是因为默认配置已经配置了一个 `org.springframework.web.bind.support.ConfigurableWebBindingInitializer`；然而，它并没有将其暴露为一个 Bean。相反，我们需要扩展 `org.springframework.web.servlet.config.annotation.WebMvcConfigurationSupport` 并重写 `requestMappingHandlerAdapter` 方法（参见清单 5-22）。

***清单 5-22.** 重用 ConfigurableWebBindingInitializer*

`public class WebMvcContextConfiguration` **`extends WebMvcConfigurationSupport`** `{`
**`    @Override`**
**`    @Bean`**
**`    public RequestMappingHandlerAdapter requestMappingHandlerAdapter() {`**
**`        RequestMappingHandlerAdapter adapter = super.requestMappingHandlerAdapter();`**
**`        ConfigurableWebBindingInitializer webBindingInitializer;`![Image](img/U002.jpg)**
**`webBindingInitializer = (ConfigurableWebBindingInitializer)` ![Image](img/U002.jpg)**
**`adapter.getWebBindingInitializer();`**
**`        webBindingInitializer.setDirectFieldAccess(true);`**
**`        //执行其他重新配置`**
**`        return adapter;`**
**`    }`**
`}`

#### 每个控制器的自定义

对于每个控制器的选项，我们必须在控制器中实现一个方法，并在该方法上添加 `org.springframework.web.bind.annotation.InitBinder` 注解。该方法必须没有返回值（void），并且至少有一个 `org.springframework.web.bind.WebDataBinder` 作为方法参数。该方法*可以*拥有与请求处理方法相同的参数。但是，它*不能*包含带有 `org.springframework.web.bind.annotation.ModelAttribute` 注解的方法参数。这是因为模型在绑定之后才可用；而在此方法中，我们将要配置如何进行绑定。

`org.springframework.web.bind.annotation.InitBinder` 注解有一个名为 `value` 的单一属性，它可以接受此初始化绑定器方法将要应用的模型属性名称或请求参数名称。默认情况下，它应用于所有模型属性和请求参数。

要自定义绑定，我们需要配置我们的 `org.springframework.web.bind.WebDataBinder`。该对象有几个我们可以使用的配置选项（setter 方法），如表 5-14 所示。

![Image](img/tab_5_14.jpg)

![Image](img/tab_5_14a.jpg)

除了设置这些属性之外，我们还可以告诉 `org.springframework.web.bind.WebDataBinder` 使用 Bean 属性访问（默认）或直接字段访问。这可以通过分别调用 `initBeanPropertyAccess` 或 `initDirectFieldAccess` 方法来设置属性访问或直接字段访问。直接字段访问的优势在于，我们不必为每个想要用于绑定的字段编写 getter/setter 方法。清单 5-23 展示了一个初始化绑定器方法的示例。

***清单 5-23.** 一个初始化绑定器方法示例*

`package com.apress.prospringmvc.bookstore.web.controller;`

`//省略导入`

`@Controller`
`@RequestMapping(value = "/customer")`
`public class RegistrationController {`

`    // 省略其他方法`

`    @InitBinder`
`    public void initBinder(WebDataBinder binder) {`
`        binder.initDirectFieldAccess();`
`        binder.setDisallowedFields("id");`
`        binder.setRequiredFields("username", "password", "emailAddress");`
`    }`

`}`

#### ModelAttributes

为了充分利用数据绑定，我们必须使用模型属性。此外，我们应该使用这些模型属性之一作为表单字段所绑定的对象。在我们的 `com.apress.prospringmvc.bookstore.web.controller.BookSearchController` 中，我们添加了一个对象作为方法参数，Spring 将其用作绑定请求参数的对象。然而，我们可以对对象以及如何创建对象拥有更多控制。为此，我们可以使用 `org.springframework.web.bind.annotation.ModelAttribute` 注解。该注解既可以放在方法上，也可以放在方法参数上。

##### 在方法上使用 ModelAttribute

我们可以在方法上使用 `ModelAttribute` 注解来创建一个用于表单的对象（例如，在编辑或更新时），或者获取引用数据（即渲染表单所需的数据，如类别列表）。让我们修改控制器，向模型添加一个类别列表和一个 `com.apress.prospring.bookstore.domain.BookSearchCriteria` 对象的实例（参见清单 5-24）。

![Image](img/square.jpg) **注意** 当 `ModelAttribute` 注解放在方法上时，该方法将在请求处理方法被调用之前被调用！

***清单 5-24.** 带有 ModelAttribute 方法的 BookSearchController*

`package com.apress.prospringmvc.bookstore.web.controller;`

`// 省略其他导入。`

`import org.springframework.web.bind.annotation.ModelAttribute;`

`@Controller`
`public class BookSearchController {`

`    @Autowired`
`    private BookstoreService bookstoreService;`

`    @ModelAttribute`
`    public BookSearchCriteria criteria() {`
`        return new BookSearchCriteria();`
`    }`

`    @ModelAttribute("categories")`
`    public List<Category> getCategories() {`
`        return this.bookstoreService.findAllCategories();`
`    }`

`    @RequestMapping(value = "/book/search", method = { RequestMethod.`*`GET`* `})`
`    public Collection<Book> list(BookSearchCriteria criteria) {`
`        return this.bookstoreService.findBooks(criteria);`
`    }`
`}`

带有 `ModelAttribute` 注解的方法在方法参数类型方面与请求处理方法具有相同的灵活性。当然，它们不应操作响应，并且不能包含带有 `ModelAttribute` 注解的方法参数。我们也可以让方法返回 void；但是，那样我们需要包含一个 `org.springframework.ui.Model`、`org.springframework.ui.ModelMap` 或 `Map` 作为方法参数，并显式地将其值添加到模型中。

该注解也可以放在请求处理方法上，表示该方法的返回值将用作模型属性。视图的名称则根据请求，使用已配置的 `org.springframework.web.servlet.RequestToViewNameTranslator` 来推导得出。



##### 在方法参数上使用 @ModelAttribute

当在方法参数上使用该注解时，会从模型中查找该参数。如果未找到，则使用默认构造函数创建参数类型的实例。清单 5-25 展示了我们的 `com.apress.prospring.bookstore.web.controller.BookSearchController` 使用该注解的情况。

***清单 5-25.** 在方法参数上使用 @ModelAttribute 注解的 BookSearchController*

`package com.apress.prospringmvc.bookstore.web.controller;`

`// 导入部分已省略，详见` `清单 5-22`

`@Controller`
`public class BookSearchController {`

`// 方法已省略，详见` `清单 5-22`

`    @RequestMapping(value = "/book/search", method = { RequestMethod.GET })`
`    public Collection<Book> list`**`(@ModelAttribute("bookSearchCriteria")`** `BookSearchCriteria`![Image](img/U002.jpg)
` criteria) {`
`        return this.bookstoreService.findBooks(criteria);`
`    }`
`}`

##### 使用 SessionAttributes

在请求之间将模型属性存储在会话中可能非常有用。例如，假设我们需要编辑一条客户记录。第一个请求从数据库中获取该客户。然后在应用程序中对其进行编辑，并将更改提交回来并应用到该客户。如果我们不将客户存储在会话中，则必须再次从数据库中检索该客户记录。这可能很不方便。

在 Spring @MVC 中，你可以告诉框架将某些模型属性存储在会话中。为此，你可以使用 `org.springframework.web.bind.annotation.SessionAttributes` 注解（参见表 5-15）。你应该使用此注解将模型属性存储在会话中，以便它们能够跨越多个 HTTP 请求而存在。但是，你不应该使用此注解将某些内容存储在会话中，然后使用 `javax.servlet.http.HttpSession` 来检索它。会话属性也只能在同一个控制器内使用，因此你不应将它们用作在控制器之间传递对象的传输工具。如果你需要类似的功能，我们建议你使用 Spring Web Flow（参见第 10 章至第 12 章）。

![Image](img/tab_5_15.jpg)

当使用 `org.springframework.web.bind.annotation.SessionAttributes` 注解将模型属性存储在会话中时，我们还需要告诉框架何时移除这些属性。为此，我们需要使用 `org.springframework.web.bind.support.SessionStatus` 接口（参见清单 5-26）。当我们使用完这些属性后，需要调用该接口上的 `setComplete` 方法。要访问该接口，我们可以简单地将其作为方法参数包含进来（参见表 5-4）。

***清单 5-26.** SessionStatus 接口*

`package org.springframework.web.bind.support;`

`public interface SessionStatus {`

`    void setComplete();`

`    boolean isComplete();`

`}`

##### 表单标签库

为了能够使用框架提供的所有数据绑定功能，我们还需要使用标签库来编写表单。Spring MVC 提供了两个标签库。第一个是通用标签库（参见表 5-16），第二个是用于简化页面中表单编写的标签库（参见表 5-17）。我们可以使用通用标签库来编写表单（这是 Spring Framework 2.0 之前的工作方式）。这是一种非常强大的方法，但也相当繁琐（尽管在表单标签库不足以应对的极端情况下，它仍然可以作为后备方案使用）。

![Image](img/tab_5_16.jpg)

![Image](img/tab_5_16a.jpg)

![Image](img/tab_5_17.jpg)

![Image](img/tab_5_17a.jpg)

当使用表单标签库时，我们需要指定要将表单元素绑定到哪个模型属性。我们通过在表单元素上指定 `path` 属性来实现这一点。我们可以在各种表单标签上设置多个属性，但表 5-18 显示了主要属性。对于使用项目集合的表单标签（例如，select、checkboxes 和 radiobuttons），还有一些额外的共享属性（参见表 5-19）。

![Image](img/tab_5_18.jpg)

![Image](img/tab_5_19.jpg)

我们可以将此逻辑应用于我们的订单 JSP 页面。该页面仍将具有相同的功能，但它将充分利用 Spring MVC 的数据绑定功能（参见清单 5-27）。在这种情况下，我们只需将普通的 input 元素标签替换为表单元素标签，然后提供要绑定的路径。当然，我们还需要在 JSP 顶部添加 `taglib` 声明。

***清单 5-27.** 使用表单标签的订单 JSP*

`<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core"%>`
**`<%@ taglib prefix="form" uri="http://www.springframework.org/tags/form" %>`**
**`<form:form method="GET" modelAttribute="bookSearchCriteria">`**
`    <fieldset>`
`        <legend>搜索条件</legend>`
`        <table>`
`            <tr>`
`                <td`**`><form:label path="title">`**`标题</form:label></td>`
`                <td`**`><form:input path="title"/>`**`</td>`
`            </tr>`
`        </table>`
`    </fieldset>`
`    <button id="search">搜索</button>`
`</form:form>`

`<c:if test="${not empty bookList}">`
`    <table>`
`        <tr><th>标题</th><th>描述</th><th>价格</th></tr>`
`        <c:forEach items="${bookList}" var="book">`
`            <tr>`
`                <td><a href="<c:url value="/book/detail/${book.id}"/>">${book.title}</a></td>`
`                <td>${book.description}</td>`
`                <td>${book.price}</td>`
`            </tr>`
`        </c:forEach>`
`    </table>`
`</c:if>`

如果我们此时重新部署并执行新的搜索，我们会看到我们的标题字段保留了之前输入的值（参见图 5-6）。这是由于我们结合使用了数据绑定和表单标签。

![Image](img/9781430241553_Fig05-06.jpg)

***图 5-6.** 标题字段保持已填写状态*

现在是时候通过添加一个下拉框（HTML select）来让事情变得更有趣一些了，以便除了标题之外，还可以选择一个要搜索的类别。我们的模型中已经有这些类别（参见清单 5-23）。我们只想添加一个下拉框并将其绑定到类别的 `id` 字段（参见清单 5-28）。我们添加一个 `select` 标签，并告诉它哪个模型属性包含要渲染的 `items`。我们还指定了每个项目要显示的 `value` 和 `label`。`value` 绑定到用于表单的模型属性。

***清单 5-28.** 带类别选择的搜索页面*

`<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core"%>`
`<%@ taglib prefix="form" uri="http://www.springframework.org/tags/form" %>`
`<form:form method="GET" modelAttribute="bookSearchCriteria">`
`  <fieldset>`
`    <legend>搜索条件</legend>`
`    <table>`
`      <tr>`
`        <td><form:label path="title">标题</form:label></td>`
`        <td><form:input path="title"/></td>`
`      </tr>`
**`<tr>`**
**`<td><form:label path="category.id">类别</form:label></td>`**
**`<td>`**
**`<form:select path="category.id" items="${categories}" itemValue="id"`![Image](img/U002.jpg)**
**`itemLabel="name"/>`**
**`</td>`**
**`</tr>`**
`        </table>`
`    </fieldset>`
`    <button id="search">搜索</button>`
`</form:form>`

`// 结果表格已省略`



#### 类型转换

数据绑定中的一个重要部分是类型转换。当我们收到请求时，我们唯一拥有的是`String`实例。然而，在现实世界中，我们使用许多不同的对象类型，而不仅仅是文本表示。因此，我们希望将这些`String`实例转换为我们可以使用的东西，这就是类型转换的作用。在 Spring MVC 中，有三种方法可以进行类型转换：

*   属性编辑器（Property Editors）
*   转换器（Converters）
*   格式化器（Formatters）

属性编辑器是进行类型转换的旧方式，而转换器和格式化器是进行类型转换的新方式。转换器和格式化器更加灵活；因此，它们也比属性编辑器更强大。此外，依赖属性编辑器还会引入整个`java.beans`包，包括其所有支持类，而这些在 Web 环境中我们并不需要。

##### 属性编辑器

自 Spring 框架诞生以来，对属性编辑器的支持就一直是其一部分。要使用这种类型转换，我们创建一个`PropertyEditor`实现（通常通过继承`PropertyEditorSupport`）。属性编辑器接收一个`String`并将其转换为强类型对象——反之亦然。Spring 提供了几个开箱即用的实现来完成此操作（参见表 5-20）。

![Image](img/tab_5_20.jpg)

![Image](img/tab_5_20a.jpg)

##### 转换器

Spring 3 中的转换器 API 是一个通用的类型转换系统。在 Spring 容器中，该系统用作属性编辑器的替代方案，用于将 bean 属性值的字符串转换为所需的属性类型。在我们的应用程序中，只要需要进行类型转换，我们也可以利用这个 API。转换器系统是一个强类型转换系统，并使用泛型来强制执行这一点。

有四种不同的接口可用于实现转换器，所有这些接口都可以在`org.springframework.core.convert.converter`包中找到：

*   `Converter`
*   `ConverterFactory`
*   `GenericConverter`
*   `ConditionalGenericConverter`

让我们探讨这四种不同的 API。

清单 5-29 展示了 Converter API，它非常直接。它有一个单一的`convert`方法，该方法接收一个`source`参数并将其转换为目标类型。源类型和目标类型由泛型类型参数`S`和`T`表示。

***清单 5-29.** Converter API*

`package org.springframework.core.convert.converter;`

`public interface Converter<S, T> {`

`    T convert(S source);`
`}`

清单 5-30 展示了`ConverterFactory` API，当您需要为整个类层次结构提供转换逻辑时，它非常有用。为此，我们可以将`S`参数化为我们要转换的类型（源），并将`R`参数化为我们要转换到的基类型。然后，我们可以在此工厂的实现内部创建适当的转换器。

***清单 5-30.** ConverterFactory API*

`package org.springframework.core.convert.converter;`

`public interface ConverterFactory<S, R> {`

`    <T extends R> Converter<S, T> getConverter(Class<T> targetType);`
`}`

当我们需要更复杂的转换逻辑时，我们可以使用`org.springframework.core.convert.converter.GenericConverter`（参见清单 5-31）。它比之前的转换器类型更灵活，但强类型化程度较低。它支持在多个源类型和目标类型之间进行转换。在转换过程中，我们可以访问源类型和目标类型的描述，这对于复杂的转换逻辑非常有用。这也允许通过注解驱动类型转换（即，我们可以在运行时解析注解以确定需要做什么）。

***清单 5-31.** GenericConverter API*

`package org.springframework.core.convert.converter;`

`import org.springframework.core.convert.TypeDescriptor;`
`import org.springframework.util.Assert;`

`import java.util.Set;`

`public interface GenericConverter {`

`    Set<ConvertiblePair> getConvertibleTypes();`

`    Object convert(Object source,`
`                   TypeDescriptor sourceType,`
`                   TypeDescriptor targetType);`

`}`

这种类型转换逻辑的一个例子是将数组转换为集合的转换器。转换器会首先检查正在转换的元素类型，以便我们可以对不同的元素应用额外的转换逻辑。

清单 5-32 展示了`GenericConverter`的一个特化版本，它允许我们指定一个条件来决定何时执行转换。例如，我们可以创建一个转换器，它使用`BigDecimal`的某个`valueOf`方法来转换一个值，但这只有在我们可以使用给定的`sourceType`实际调用该方法时才有用。

***清单 5-32.** ConditionalGenericConverter API*

`package org.springframework.core.convert.converter;`

`import org.springframework.core.convert.TypeDescriptor;`

`    boolean matches(TypeDescriptor sourceType, TypeDescriptor targetType);`

`}`

转换器在`org.springframework.core.convert.ConversionService`接口（参见清单 5-33）背后执行；该接口的典型实现也实现了`org.springframework.core.convert.converter.ConverterRegistry`接口，这使得可以轻松注册额外的转换器。当使用 Spring @MVC 时，有一个预配置的`org.springframework.format.support.DefaultFormattingConversionService`实例（它也允许执行和注册格式化器）。

***清单 5-33.** ConversionService API*

`package org.springframework.core.convert;`

`public interface ConversionService {`

`    boolean canConvert(Class<?> sourceType, Class<?> targetType);`

`    boolean canConvert(TypeDescriptor sourceType, TypeDescriptor targetType);`

`    <T> T convert(Object source, Class<T> targetType);`

`    Object convert(Object source, TypeDescriptor sourceType, TypeDescriptor targetType);`
`}`



##### 格式化器

Converter API 是一个通用类型转换系统。它是强类型的，并且可以将任何对象类型转换为另一种对象类型（如果有可用的转换器）。然而，在我们的 Web 环境中并不需要这个功能，因为我们只处理 `String` 对象。另一方面，我们可能希望将对象以 `String` 的形式呈现给客户端，甚至可能希望以本地化的方式来实现。这正是 Formatter API 发挥作用的地方（参见清单 5-34）。它提供了一种简单且健壮的机制，用于将 `String` 转换为强类型对象。它是属性编辑器的一种替代方案，但更轻量（例如，它不依赖于 `java.beans` 包）且更灵活（例如，它可以访问 `Locale` 以处理本地化内容）。

***清单 5-34.** Formatter API*

`package org.springframework.format;`

`public interface Formatter<T> extends Printer<T>, Parser<T> {`
`}`

`import java.util.Locale`

`public interface Printer<T> {`
`    String print(T object, Locale locale);`
`}`

`import java.util.Locale`
`import java.text.ParseException;`

`public interface Parser<T> {`
`    T parse(String text, Locale locale) throws ParseException;`
`}`

要创建一个格式化器，我们需要实现 `org.springframework.format.Formatter` 接口，并将类型 `T` 指定为我们想要转换的类型。例如，假设我们有一个格式化器，可以将 `Date` 实例转换为文本，反之亦然。我们会将 `T` 指定为 `Date`，并使用 `Locale` 来确定执行转换时要使用的具体日期格式（参见清单 5-35）。

***清单 5-35.** 示例 DateFormatter*

`package com.apress.prospringmvc.bookstore.formatter;`

`// java.text 和 java.util 的导入已省略`

`import org.springframework.format.Formatter;`
`import org.springframework.util.StringUtils;`

`public class DateFormatter implements Formatter<Date> {`

`    private String format;`

`    @Override`
`    public String print(Date object, Locale locale) {`
`        return getDateFormat(locale).format(object);`
`    }`

`    @Override`
`    public Date parse(String text, Locale locale) throws ParseException {`
`        return getDateFormat(locale).parse(text);`
`    }`

`    private DateFormat getDateFormat(Locale locale) {`
`        if (StringUtils.hasText(this.format)) {`
`            return new SimpleDateFormat(this.format, locale);`
`        } else {`
`            return SimpleDateFormat.getDateInstance(SimpleDateFormat.MEDIUM, locale);`
`        }`
`    }`

`    public void setFormat(String format) {`
`        this.format = format;`
`    }`
`}`

格式化器也可以由注解驱动，而不是由字段类型驱动。如果我们想将一个格式化器绑定到一个注解，我们必须实现 `org.springframework.format.AnnotationFormatterFactory`（参见清单 5-36）。

***清单 5-36.** AnnotationFormatterFactory*

`package org.springframework.format;`

`public interface AnnotationFormatterFactory<A extends Annotation> {`

`    Set<Class<?>> getFieldTypes();`

`    Printer<?> getPrinter(A annotation, Class<?> fieldType);`

`    Parser<?> getParser(A annotation, Class<?> fieldType);`
`}`

我们需要用我们想要关联的注解类型来参数化 `A`。`getPrinter` 和 `getParser` 方法应分别返回一个 `org.springframework.format.Printer` 和一个 `org.springframework.format.Parser`。然后我们可以使用它们来从注解类型进行转换或转换到注解类型。假设我们有一个 `com.apress.prospringmvc.bookstore.formatter.DateFormat` 注解，可以用来设置 `date` 字段的格式。那么我们可以实现如清单 5-37 所示的工厂。

***清单 5-37.** DateFormatAnnotationFormatterFactory*

`package com.apress.prospringmvc.bookstore.formatter;`

`import java.util.Date;`
`import java.util.HashSet;`
`import java.util.Set;`

`import org.springframework.format.AnnotationFormatterFactory;`
`import org.springframework.format.Parser;`
`import org.springframework.format.Printer;`

`public class DateFormatAnnotationFormatterFactory implements`![Image](img/U002.jpg)
` AnnotationFormatterFactory<DateFormat> {`

`    @Override`
`    public Set<Class<?>> getFieldTypes() {`
`        Set<Class<?>> types = new HashSet<Class<?>>(1);`
`        types.add(Date.class);`
`        return types;`
`    }`

`    @Override`
`    public Printer<?> getPrinter(DateFormat annotation, Class<?> fieldType) {`
`        return createFormatter(annotation);`
`    }`

`    @Override`
`    public Parser<?> getParser(DateFormat annotation, Class<?> fieldType) {`
`        return createFormatter(annotation);`
`    }`

`    private DateFormatter createFormatter(DateFormat annotation) {`
`        DateFormatter formatter = new DateFormatter();`
`        formatter.setFormat(annotation.format());`
`        return formatter;`
`    }`
`}`



##### 配置类型转换

如果要在 Spring MVC 中使用 `org.springframework.core.convert.converter.Converter` 或 `org.springframework.format.Formatter`，则需要添加一些配置。`org.springframework.web.servlet.config.annotation.WebMvcConfigurerAdapter` 为此提供了一个方法。可以重写 `addFormatters` 方法来注册额外的转换器和/或格式化器。该方法接受一个 `org.springframework.format.FormatterRegistry`（参见清单 5-38）作为参数，并可用于注册额外的转换器和/或格式化器（`FormatterRegistry` 扩展了 `org.springframework.core.convert.converter.ConverterRegistry`，后者为 `Converter` 实现提供了相同的功能）。

***清单 5-38.** FormatterRegistry 接口*

`package org.springframework.format;`

`import java.lang.annotation.Annotation;`

`import org.springframework.core.convert.converter.ConverterRegistry;`

`public interface FormatterRegistry extends ConverterRegistry {`

`    void addFormatter(Formatter<?> formatter);`
`    void addFormatterForFieldType(Class<?> fieldType, Formatter<?> formatter);`
`    void addFormatterForFieldType(Class<?> fieldType, Printer<?> printer, Parser<?> parser);`
`    void addFormatterForFieldAnnotation(AnnotationFormatterFactory<? extends Annotation>`![Image](img/U002.jpg)
` annotationFormatterFactory);`

`}`

为了将 `String` 转换为 `com.apress.prospringmvc.bookstore.domain.Category`，我们将实现一个 `org.springframework.core.convert.converter.GenericConverter`（参见清单 5-39），并在配置中注册它（参见清单 5-40）。`com.apress.prospringmvc.bookstore.converter.StringToEntityConverter` 接收一个 `String` 作为源，并将其转换为可配置的实体类型。然后，它使用 `javax.persistence.EntityManager` 从数据库加载记录。

***清单 5-39.** StringToEntityConverter*

`package com.apress.prospringmvc.bookstore.converter;`

`import java.util.HashSet;`
`import java.util.Set;`

`import javax.persistence.EntityManager;`
`import javax.persistence.PersistenceContext;`

`import org.apache.commons.lang3.StringUtils;`
`import org.apache.commons.lang3.reflect.FieldUtils;`
`import org.springframework.core.convert.TypeDescriptor;`
`import org.springframework.core.convert.converter.GenericConverter;`

`public class StringToEntityConverter implements GenericConverter {`

`    private static final String ID_FIELD = "id";`

`    private final Class<?> clazz;`

`    @PersistenceContext`
`    private EntityManager em;`

`    public StringToEntityConverter(Class<?> clazz) {`
`        super();`
`        this.clazz = clazz;`
`    }`

`    @Override`
`    public Set<ConvertiblePair> getConvertibleTypes() {`
`        Set<ConvertiblePair> types = new HashSet<GenericConverter.ConvertiblePair>();`
`        types.add(new ConvertiblePair(String.class, this.clazz));`
`        types.add(new ConvertiblePair(this.clazz, String.class));`
`        return types;`
`    }`

`    @Override`
`    public Object convert(Object source, TypeDescriptor sourceType,`
`                          TypeDescriptortargetType) {`
`        if (String.class.equals(sourceType.getType())) {`
`            if (StringUtils.isBlank((String) source)) {`
`                return null;`
`            }`
`            Long id = Long.parseLong((String) source);`
`            return this.em.find(this.clazz, id);`
`        } else if (this.clazz.equals(sourceType.getType())) {`
`            try {`
`                if (source == null) {`
`                    return "";`
`                } else {`
`                    return FieldUtils.readField(source, ID_FIELD, true).toString();`
`                }`
`            } catch (IllegalAccessException e) {`
`            }`
`        }`
`        throw new IllegalArgumentException("Cannot convert " + source + " into a suitable`![Image](img/U002.jpg)
` type!");`
`    }`
`}`

***清单 5-40.** CategoryConverter 配置*

`package com.apress.prospringmvc.bookstore.web.config;`

`... [import 已省略]`

`@Configuration`
`@EnableWebMvc`
`@ComponentScan(basePackages = { "com.apress.prospringmvc.bookstore.web" })`
`public class WebMvcContextConfiguration extends WebMvcConfigurerAdapter {`

`    …`

**`@Bean`**
**`public StringToEntityConverter categoryConverter() {`**
**`return new StringToEntityConverter(Category.class);`**
**`}`**

**`@Override`**
**`public void addFormatters(final FormatterRegistry registry) {`**
**`registry.addConverter(categoryConverter());`**
**`registry.addFormatter(new DateFormatter("dd-MM-yyyy"));`**
**`}`**
**`...`**
`}`

除了类别转换之外，我们还需要进行日期转换。因此，清单 5-38 中还包含一个带有日期转换模式的 `org.springframework.format.datetime.DateFormatter`。



##### 使用类型转换

现在我们已经介绍了类型转换，接下来看看它的实际应用。我们将创建一个用户注册页面，用于输入 `com.apress.prospringmvc.bookstore.domain.Account` 对象的详细信息。首先，我们需要在 `WEB-INF/views` 下创建一个网页。接着，需要创建一个 `customer` 目录，并在其中放置一个 `register.jsp` 文件。由于该页面中不同字段存在大量重复内容，因此这里仅包含代码清单 5-41 的部分内容。

***代码清单 5-41.** 注册页面*

`<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core"%>`
`<%@ taglib prefix="spring" uri="http://www.springframework.org/tags" %>`
`<%@ taglib prefix="form" uri="http://www.springframework.org/tags/form" %>`

`<form:form method="POST" modelAttribute="account">`
`    <fieldset>`
`        <legend>个人信息</legend>`
`        <table>`
`            <tr>`
`                <td>`
`                     <form:label path="firstName" cssErrorClass="error">`
`                          名字`
`                     </form:label>`
`                  </td>`
`                  <td><form:input path="firstName" /></td>`
`                  <td><form:errors path="firstName"/></td>`
`             </tr>`
`             // 其他 Account 字段已省略`
`        </table>`
`    <fieldset>`
`        <legend>用户信息</legend>`
`        <table>`
`            <tr>`
`                <td><form:label path="username" cssErrorClass="error">`
`                         用户名`
`                    </form:label></td>`
`                <td><form:input path="username"/></td>`
`                <td><form:errors path="username"/></td>`
`            </tr>`
`            // 密码和电子邮件地址字段已省略。`
`         </table>`
`    </fieldset>`
`    <button id="save”>保存</button>`
`</form:form>`

我们还需要一个控制器，因此将创建 `com.apress.prospringmvc.bookstore.web.controller.RegistrationController`。在这个控制器中，我们将使用一些数据绑定特性。首先，我们将禁止提交 `id` 字段（以防止有人编辑其他用户）。其次，我们将根据当前的 `Locale` 预选用户的国家。代码清单 5-42 展示了我们的控制器。

***代码清单 5-42.** RegistrationController*

`package com.apress.prospringmvc.bookstore.web.controller;`

`import java.util.Locale;`
`import java.util.Map;`
`import java.util.TreeMap;`

`import org.springframework.beans.factory.annotation.Autowired;`
`import org.springframework.stereotype.Controller;`
`import org.springframework.validation.BindingResult;`
`import org.springframework.web.bind.WebDataBinder;`
`import org.springframework.web.bind.annotation.InitBinder;`
`import org.springframework.web.bind.annotation.ModelAttribute;`
`import org.springframework.web.bind.annotation.RequestMapping;`
`import org.springframework.web.bind.annotation.RequestMethod;`

`import com.apress.prospringmvc.bookstore.domain.Account;`
`import com.apress.prospringmvc.bookstore.service.AccountService;`

`@Controller`
`@RequestMapping("/customer/register")`
`public class RegistrationController {`

`    @Autowired`
`    private AccountService accountService;`

`    @ModelAttribute("countries")`
`    public Map<String, String> countries(Locale currentLocale) {`
`        Map<String, String> countries = new TreeMap<String, String>();`
`        for (Locale locale : Locale.getAvailableLocales()) {`
`            countries.put(locale.getCountry(), locale.getDisplayCountry(currentLocale));`
`        }`
`        return countries;`
`    }`

`    @InitBinder`
`    public void initBinder(WebDataBinder binder) {`
`        binder.setDisallowedFields("id");`
`        binder.setRequiredFields("username","password","emailAddress");`
`    }`

`    @RequestMapping(method = RequestMethod.GET)`
`    @ModelAttribute`
`    public Account register(Locale currentLocale) {`
`        Account account = new Account();`
`        account.getAddress().setCountry(currentLocale.getCountry());`
`        return account;`
`    }`

`    @RequestMapping(method = { RequestMethod.POST, RequestMethod.PUT })`
`public String handleRegistration(@ModelAttribute Account account,` **`BindingResult result`**`) {`
**`if (result.hasErrors()) {`**
**`return "customer/register";`**
**`}`**
`        this.accountService.save(account);`
`        return "redirect:/customer/account/" + account.getId();`
`    }`
`}`

这个控制器包含了很多内容。例如，`initBinder` 方法配置了我们的绑定。它禁止设置 `id` 属性，并设置了一些必填字段。我们还有一个方法，通过将 JDK 中所有可用的国家添加到模型中来准备模型。最后，我们有两个请求处理方法，一个用于 `GET` 请求（进入页面时的初始请求），另一个用于提交表单时的 `POST`/`PUT` 请求。请注意模型属性旁边的 `org.springframework.validation.BindingResult` 属性。我们可以用它来检测错误；并基于此重新显示原始页面。还要记住 JSP 中的 `error` 标签，它们用于显示字段或对象的错误消息（我们将在后续章节中更深入地介绍这一点）。当应用程序重新部署后，我们点击 `注册` 链接，应该会看到如图 5-7 所示的页面。

![Image](img/9781430241553_Fig05-07.jpg)

***图 5-7.** 账户注册页面*

如果我们现在输入一个无效的日期；将用户名、密码和电子邮件地址字段留空；然后提交表单；同一页面会重新显示并附带一些错误消息（见图 5-8）。

![Image](img/9781430241553_Fig05-08.jpg)

***图 5-8.** 显示一些错误的账户注册页面*

这些错误消息是由 Spring MVC 的数据绑定功能生成的。在本章后面，我们将看到如何影响显示的消息。目前，我们先保持它们不变。如果我们填写正确的信息并点击保存，我们将被重定向到一个账户页面（我们已经为该页面提供了基本的控制器和实现）。



#### 验证模型属性

我们之前已经多次提到过验证。我们也多次引用了 `org.springframework.validation` 包。使用 Spring 框架的验证抽象来验证我们的模型属性相当容易。验证并不局限于 Web 层；它关乎对象的验证。因此，验证也可以在 Web 层之外使用；事实上，它可以在任何地方使用。

验证的主要抽象是 `org.springframework.validation.Validator` 接口。该接口有两个回调方法。`supports` 方法用于确定验证器实例是否可以验证该对象。`validate` 方法用于实际验证对象（参见 清单 5-43）。

***清单 5-43.** Validator 接口*

`package org.springframework.validation;`

`public interface Validator {`

`    boolean supports(Class<?> clazz);`

`    void validate(Object target, Errors errors);`
`}`

调用 `supports` 方法是为了检查验证器是否可以验证当前对象类型。如果返回 `true`，框架将调用 `validate` 方法，并传入要验证的对象以及 `org.springframework.validation.Errors` 接口实现的一个实例。在进行绑定时，这将是 `org.springframework.validation.BindingResult` 的一个实现。在进行验证时，最好包含一个 `Errors` 或 `BindingResult`（后者扩展了 `Errors`）方法属性。这样，我们就可以处理存在绑定或验证错误的情况。如果不是这种情况，则会抛出 `org.springframework.validation.BindException`。

使用 Spring @MVC 时，我们有两种触发验证的选项。第一种是将验证器注入到我们的控制器中，然后自行调用验证器上的 `validate` 方法。第二种是向我们的方法属性添加 `javax.validation.Valid`（JSR-303）或 `org.springframework.validation.annotation.Validated` 注解。Spring 框架的注解比 `javax.validation` 包中的注解更强大。Spring 注解使我们能够指定*提示*；当与 JSR-303 验证器（例如 `hibernate-validation`）结合使用时，可用于指定验证组。

验证和绑定错误会导致消息代码注册到 `Errors` 实例中。通常，仅向用户显示错误代码信息量不大，因此需要将代码解析为消息。这就是 `org.springframework.context.MessageSource` 发挥作用的地方。错误代码作为消息代码传递给配置的消息源，并用于检索消息。如果我们没有配置消息源，将会看到一个友好的堆栈跟踪，指示找不到代码 x 的消息。因此，在继续之前，让我们配置 清单 5-44 中所示的 `MessageSource`。

***清单 5-44.** MessageSource 配置*

`package com.apress.prospringmvc.bookstore.web.config;`

`import org.springframework.context.support.ResourceBundleMessageSource;`
`// 其他导入已省略`

`@Configuration`
`@EnableWebMvc`
`@ComponentScan(basePackages = { "com.apress.prospringmvc.bookstore.web" })`
`public class WebMvcContextConfiguration extends WebMvcConfigurerAdapter {`

`@Bean`
`    public MessageSource messageSource() {`
`        ResourceBundleMessageSource messageSource;`
`        messageSource = new ResourceBundleMessageSource();`
`        messageSource.setBasename("messages");`
`        messageSource.setUseCodeAsDefaultMessage(true);`
`        return messageSource;`
`    }`
`    // 其他方法已省略`
`}`

我们配置了一个消息源，然后将其配置为加载一个基本名称为 messages 的资源包（我们将在本章后面的“国际化”部分了解更多信息）。当找不到消息时，我们将代码本身作为消息返回。这在开发期间特别有用，因为我们可以快速查看资源包中缺少哪些消息代码。

让我们为 `com.apress.prospringmvc.bookstore.domain.Account` 类实现验证。我们想要验证一个账户是否有效；为此，我们需要用户名、密码和一个有效的电子邮件地址。为了能够处理发货，我们还需要地址、城市和国家。没有这些信息，账户就是无效的。现在让我们看看如何利用验证框架来为我们服务。



###### 实现自定义验证器

首先，我们将实现自己的验证器。在本例中，我们将创建一个 `com.apress.prospringmvc.bookstore.validation.AccountValidator`（见清单 5-45），并使用 init-binder 方法对其进行配置。

***清单 5-45.** AccountValidator 实现*

`package com.apress.prospringmvc.bookstore.validation;`

`import java.util.regex.Pattern;`

`import org.springframework.validation.Errors;`
`import org.springframework.validation.ValidationUtils;`
`import org.springframework.validation.Validator;`

`import com.apress.prospringmvc.bookstore.domain.Account;`

`public class AccountValidator implements Validator {`

`    private static final String EMAIL_PATTERN =`
`    "^[_A-Za-z0-9-]+(\\.[_A-Za-z0-9-]+)*@"`
`    +"[A-Za-z0-9]+(\\.[A-Za-z0-9]+)*(\\.[A-Za-z]{2,})$";`

`    @Override`
`    public boolean supports(Class<?> clazz) {`
`        return (Account.class).isAssignableFrom(clazz);`
`    }`

`    @Override`
`    public void validate(Object target, Errors errors) {`
`        ValidationUtils.rejectIfEmpty(errors, "username",`
`                                  "required", new Object[] {"Username"});`
`        ValidationUtils.rejectIfEmpty(errors, "password",`
`                                  "required", new Object[] {"Password"});`
`        ValidationUtils.rejectIfEmpty(errors, "emailAddress",`
`                                  "required", new Object[] {"Email Address"});`
`        ValidationUtils.rejectIfEmpty(errors, "address.street",`
`                                  "required", new Object[] {"Street"});`
`        ValidationUtils.rejectIfEmpty(errors, "address.city",`
`                                  "required", new Object[] {"City"});`
`        ValidationUtils.rejectIfEmpty(errors, "address.country",`
`                                  "required", new Object[] {"Country"});`

`        if (!errors.hasFieldErrors("emailAddress")) {`
`            Account account = (Account) target;`
`            String email = account.getEmailAddress();`
`            if (!emai.matches(EMAIL_PATTERN)) {`
`                errors.rejectValue("emailAddress", "invalid");`
`            }`
`        }`
`    }`
`}`

![Image](img/square.jpg) **注意** 在 `org.springframework.web.bind.WebDataBinder` 上指定 `requiredFields` 会产生与 `ValidationUtils.rejectIfEmptyOrWhiteSpace` 相同的验证逻辑。但在我们的例子中，所有验证逻辑都集中在一处，而不是分散在两处。

此验证器实现将检查字段是否不为 `null` 且非空。如果字段为空，它将为给定字段注册一个错误。该错误是一个消息代码集合，该集合由 `org.springframework.validation.MessageCodesResolver` 实现确定。默认实现 `org.springframework.validation.DefaultMessageCodesResolver` 将解析为四种不同的代码（见表 5-21）。表中的顺序也是错误代码解析为相应消息的顺序。

![Image](img/tab_5_21.jpg)

此验证的最后一步是，我们需要配置验证器，并告知控制器在提交时验证我们的模型属性。在清单 5-46 中，我们展示了修改后的订单控制器。我们只想在表单最终提交时触发验证。

***清单 5-46**. 带验证功能的 RegistrationController*

`package com.apress.prospringmvc.bookstore.web.controller;`

`import com.apress.prospringmvc.bookstore.domain.AccountValidator;`

`import javax.validation.Valid;`
`// 其他导入已省略`

`@Controller`
`@RequestMapping("/customer/register")`
`public class RegistrationController {`

`    @InitBinder`
`    public void initBinder(WebDataBinder binder) {`
`        binder.setDisallowedFields("id");`
**`binder.setValidator(new AccountValidator());`**
`    }`

`    @RequestMapping(method = { RequestMethod.`*`POST`*`, RequestMethod.`*`PUT`* `})`
`    public String handleRegistration`**`(@Valid`** `@ModelAttribute Account account, BindingResult`![Image](img/U002.jpg)
` result) {`
`        if (result.hasErrors()) {`
`            return "customer/register";`
`        }`
`        this.accountService.save(account);`
`        return "redirect:/customer/account/" + account.getId();`
`    }`

`    // 其他方法已省略`
`}`

如果重新部署后提交了非法值，我们将看到一些错误代码，如图 5-9 所示。

![Image](img/9781430241553_Fig05-09.jpg)

***图 5-9.** 显示错误代码的注册页面*

###### 使用 JSR-303 验证

除了实现我们自己的验证器，我们还可以使用 JSR-303 注解来添加验证。为此，我们只需使用 JSR-303 注解来标注 `com.apress.prospringmvc.bookstore.domain.Account` 对象（见清单 5-47），然后保留 `javax.validation.Valid` 注解即可。使用这些注解时，使用的错误代码与我们自定义验证器中使用的略有不同（见表 5-22）。但是，注册页面无需更改，因此与之前保持一致。在我们的 init-binder 方法中，我们不需要设置验证器，因为支持 JSR-303 的验证器会被自动检测到（示例项目使用了 Hibernate 的验证器）。

***清单 5-47.** 带有 JSR-303 注解的 Account*

`package com.apress.prospringmvc.bookstore.domain;`

`import java.io.Serializable;`
`import java.util.ArrayList;`
`import java.util.Date;`
`import java.util.List;`

`import javax.persistence.CascadeType;`
`import javax.persistence.Embedded;`
`import javax.persistence.Entity;`
`import javax.persistence.GeneratedValue;`
`import javax.persistence.GenerationType;`
`import javax.persistence.Id;`
`import javax.persistence.ManyToMany;`
`import javax.validation.Valid;`

`import org.hibernate.validator.constraints.Email;`
`import org.hibernate.validator.constraints.NotEmpty;`

`@Entity`
`public class Account implements Serializable {`

`    @Id`
`    @GeneratedValue(strategy = GenerationType.AUTO)`
`    private Long id;`

`    private String firstName;`
`    private String lastName;`

`    private Date dateOfBirth;`

`    @Embedded`
`    @Valid`
`    private Address address = new Address();`

`    @NotEmpty`
`    @Email`
`    private String emailAddress;`
`    @NotEmpty`
`    private String username;`
`    @NotEmpty`
`    private String password;`

`    // getter 和 setter 已省略`
`}`

![Image](img/square.jpg) **注意** `NotEmpty` 和 `Email` 注解来自 `org.hibernate.validator.constraints` 包；因此，此包不是标准的 JSR-303 注解，而是其扩展。

![Image](img/tab_5_22.jpg)

使用 JSR-303 注解时，如果我们提交带有无效值的表单，会得到如图 5-10 所示的结果。如我们所见，显示的是消息而不是代码。这是如何实现的？我们使用的验证器实现附带了一些默认消息。如果我们愿意，可以通过在资源包中指定表 5-22 中的某个代码来覆盖这些消息（见下一节）。

![Image](img/9781430241553_Fig05-10.jpg)

***图 5-10.** 显示错误消息的注册页面*



### 国际化

要实现国际化，我们需要配置不同的组件，使其能够根据用户的语言（区域设置）解析消息。例如，`org.springframework.context.MessageSource` 允许我们根据消息代码和区域设置来解析消息。为了能够解析区域设置，我们还需要一个 `org.springframework.web.servlet.LocaleResolver`。最后，为了能够更改区域设置，我们还需要配置一个 `org.springframework.web.servlet.i18n.LocaleChangeInterceptor`（下一章将更深入地介绍拦截器）。

#### 消息源

消息源是根据代码和区域设置实际解析消息的组件。Spring 提供了 `org.springframework.context.MessageSource` 接口的几种实现。其中两种实现是我们可以直接使用的，而其他实现则只是委托给另一个消息源。

Spring 框架提供的两种实现位于 `org.springframework.context.support` 包中。表 5-23 简要描述了它们。

![Image](img/tab_5_23.jpg)

这两种 Bean 的配置方式大致相同。我们需要一个名为 `messageSource` 的 Bean。选择哪种实现并不重要。例如，我们甚至可以创建自己的实现，使用数据库来加载消息。

清单 5-48 中的配置配置了一个 `org.springframework.context.support.ReloadableResourceBundleMessageSource`，它从类路径加载一个名为 `messages.properties` 的文件。它还会尝试为当前用于解析消息的 `Locale` 加载 `messages_[locale].properties` 文件。

***清单 5-48.** WebMvcContext 中的 MessageSource 配置*

`package com.apress.prospringmvc.bookstore.web.config;`

`import org.springframework.context.support.ReloadableResourceBundleMessageSource;`

`// 其他导入已省略`

`@Configuration`
`@EnableWebMvc`
`@ComponentScan(basePackages = { "com.apress.prospringmvc.bookstore.web" })`
`public class WebMvcContextConfiguration extends WebMvcConfigurerAdapter {`

`    @Bean`
`    public MessageSource messageSource() {`
`        ReloadableResourceBundleMessageSource messageSource;`
`        messageSource = new ReloadableResourceBundleMessageSource();`
`        messageSource.setBasename("classpath:/messages");`
`        messageSource.setUseCodeAsDefaultMessage(true);`
`        return messageSource;`
`    }`
`}`

以下代码片段展示了加载的两个属性文件（实际上是资源包）。`messages.properties`（见清单 5-49）文件中的消息被视为默认值，并且可以在特定语言的 `messages_nl.properties` 文件（见清单 5-50）中被覆盖。

***清单 5-49.** messages.properties 片段*

`home.title=Welcome`

`invalid.account.emailaddress=Invalid email address.`
`required=Field {0} is required.`

***清单 5-50.** messages_nl.properties*

`home.title=Welkom`

`invalid.account.emailaddress=Ongeldig emailadres.`
`required=Veld {0} is verplicht.`

#### LocaleResolver

为了使消息源正常工作，我们还需要配置一个 `org.springframework.web.servlet.LocaleResolver`（可以在 `org.springframework.web.servlet.i18n` 包中找到）。Spring 提供了几种不同的实现，可以简化我们的工作。区域设置解析器是一种用于检测要使用哪个 `Locale` 的策略。不同的实现使用不同的方式来解析区域设置（见表 5-24）。

![Image](img/tab_5_24.jpg)

#### LocaleChangeInterceptor

如果我们希望用户能够更改区域设置，则需要配置一个 `org.springframework.web.servlet.i18n.LocaleChangeInterceptor`（见清单 5-51）。此拦截器检查当前传入的请求，并检查请求中是否存在名为 `locale` 的参数。如果存在，拦截器将使用先前配置的区域设置解析器来更改当前用户的 `Locale`。参数名称是可配置的。

***清单 5-51.** 完整的国际化配置*

`package com.apress.prospringmvc.bookstore.web.config;`

`import org.springframework.context.MessageSource;`
`import org.springframework.context.support.ReloadableResourceBundleMessageSource;`
`import org.springframework.web.servlet.HandlerInterceptor;`
`import org.springframework.web.servlet.LocaleResolver;`
`import org.springframework.web.servlet.config.annotation.InterceptorRegistry;`
`import org.springframework.web.servlet.config.annotation.ResourceHandlerRegistry;`
`import org.springframework.web.servlet.i18n.CookieLocaleResolver;`
`import org.springframework.web.servlet.i18n.LocaleChangeInterceptor;`

`// 其他导入已省略`

`@Configuration`
`@EnableWebMvc`
`@ComponentScan(basePackages = { "com.apress.prospringmvc.bookstore.web" })`
`public class WebMvcContextConfiguration extends WebMvcConfigurerAdapter {`

`    @Override`
`    public void addInterceptors(InterceptorRegistry registry) {`
`        registry.addInterceptor(localeChangeInterceptor());`
`    }`

`    @Bean`
`    public HandlerInterceptor localeChangeInterceptor() {`
`        LocaleChangeInterceptor localeChangeInterceptor;`
`        localeChangeInterceptor = new LocaleChangeInterceptor();`
`        localeChangeInterceptor.setParamName("lang");`
`        return localeChangeInterceptor;`
`    }`

`    @Bean`
`    public LocaleResolver localeResolver() {`
`        return new CookieLocaleResolver();`
`    }`

`    @Bean`
`    public MessageSource messageSource() {`
`        ReloadableResourceBundleMessageSource messageSource;`
`        messageSource = new ReloadableResourceBundleMessageSource();`
`        messageSource.setBasename("classpath:/messages");`
`        messageSource.setUseCodeAsDefaultMessage(true);`
`        return messageSource;`
`    }`
`}`

![Image](img/square.jpg) **注意** 通常，将 `LocaleChangeInterceptor` 作为第一个拦截器之一是一个好主意。如果出现问题，我们希望以正确的语言通知用户。

如果我们重新部署应用程序，切换语言时应该会得到本地化的错误消息（当然，这仅在我们向资源包中添加了适当的错误代码时才有效）。然而，`MessageSource` 的用途不仅限于错误消息；我们还可以使用 `MessageSource` 从资源包中检索标签、标题、错误消息等。为此，我们可以使用 `message` 标签。清单 5-52 显示了一个修改后的图书搜索页面，该页面使用 `message` 标签来填充标签、标题和页眉。如果我们切换语言，应该会得到本地化的消息（见图 5-11 和 5-12）。

***清单 5-52.** 使用 Message 标签的图书搜索页面*

`<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core"%>`
`<%@ taglib prefix="spring" uri="http://www.springframework.org/tags" %>`
`<%@ taglib prefix="form" uri="http://www.springframework.org/tags/form" %>`



`<form:form method="GET" modelAttribute="bookSearchCriteria">`
`    <fieldset>`
`        <legend><spring:message code="book.searchcriteria"/></legend>`
`        <table>`
`            <tr>`
`                <td><form:label path="title">`
`                    <spring:message code="book.title" />`
`                </form:label></td>`
`                <td><form:input path="title" /></td>`
`            </tr>`
`            <tr>`
`                <td><form:label path="category">`
`                    <spring:message code="book.category" />`
`                </form:label></td>`
`                <td>`
`                <form:select path="category" items="${categories}"`
`                             itemValue="id" itemLabel="name"/>`
`                </td>`
`            </tr>`
`        </table>`
`    </fieldset>`
`    <button id="search"><spring:message code="button.search"/></button>`
`</form:form>`

`<c:if test="${not empty bookList}">`
`    <table>`
`        <tr>`
`            <th><spring:message code="book.title"/></th>`
`            <th><spring:message code="book.description"/></th>`
`            <th><spring:message code="book.price" /></th>`
`        </tr>`
`        <c:forEach items="${bookList}" var="book">`
`            <tr>`
`                <td>`
`         <a href="<c:url value="/book/detail/${book.id}"/>">${book.title}</a>`
`                </td>`
`                <td>${book.description}</td>`
`                <td>${book.price}</td>`
`            </tr>`
`        </c:forEach>`
`    </table>`
`</c:if>` ![Image](img/9781430241553_Fig05-11.jpg)

***图 5-11.** 英文版图书搜索页面*

![Image](img/9781430241553_Fig05-12.jpg)

***图 5-12.** 荷兰语版图书搜索页面*

### 本章小结

本章涵盖了编写控制器和处理表单所需的所有内容。我们首先探讨了 `RequestMapping` 注解，以及如何使用它将请求映射到处理请求的方法。我们还探讨了灵活的方法签名，并介绍了开箱即用支持的方法参数类型和返回值。

接着，我们深入实践，开始编写控制器并修改现有代码。我们还引入了表单对象，并介绍了如何将属性绑定到字段。此外，我们解释了数据绑定，探讨了 Spring 的类型转换系统，以及如何使用它进行对象间的转换。我们还编写了自己的 `Converter` 实现，用于将文本转换为 `Category` 对象。

除了类型转换，我们还探讨了验证。有两种验证方式：我们可以创建自己的 `Validator` 接口实现，或者在我们想要验证的对象上使用 JSR-303 注解。启用验证可以通过 `Valid` 或 `Validated` 注解来完成。

为了更轻松地将特定字段绑定到表单对象的属性，Spring 提供了表单标签库，帮助我们编写 HTML 表单。该库还帮助我们向用户显示绑定和验证错误。

最后，我们介绍了如何在网页上实现国际化，以及如何将验证和错误代码转换为适当的消息以显示给最终用户。

在下一章中，我们将探讨 Spring MVC 的一些更高级的特性。在此过程中，我们将了解如何进一步扩展和自定义现有基础设施。

## 第 6 章

## 实现控制器——高级篇

在本章中，我们将探讨 Spring MVC 的一些更高级的部分，然后了解如何深入框架本身进行扩展，以满足我们的需求。

我们将首先研究作用域 Bean，以及如何利用它们为我们带来优势。接下来，我们将探讨如何为应用程序添加通用功能（即所谓的横切关注点）。为此，我们将研究拦截器，包括如何创建它们以及如何将它们接入我们的应用程序。

无论我们的应用程序多么健壮或设计得多么周全，总会有应用程序行为不符合预期的时候（例如，有人可能绊到连接数据库服务器的线缆），这会导致应用程序中出现异常。通常，我们希望防止用户看到那些往往令人费解的堆栈跟踪信息；为此，我们将探讨 Spring MVC 中的异常处理机制。

在涵盖所有这些主题之后，我们将深入 Spring @MVC 的内部机制，探讨一些我们可以扩展的 API；然后，我们将使用这些扩展的 API 来增强框架的功能。

### 使用作用域 Bean

在第 2 章中，我们介绍了 Spring 框架支持的 Bean 的不同作用域。表 6-1 再次列出了它们。在本节中，我们将利用作用域来为我们服务。具体来说，我们将通过一个实际示例来演示如何利用作用域 Bean 创建一个在线购物车（`com.apress.prospringmvc.bookstore.domain.Cart`）。

![Image](img/tab_6_1.jpg)

![Image](img/tab_6_1a.jpg)

我们已经使用过 `singleton` 作用域——这是 Spring 框架中 Bean 创建的默认作用域。`org.springframework.context.annotation.Scope` 注解用于指定 Bean 的作用域；其属性列在表 6-2 中。

该注解可以用作类型级别或方法级别的注解。当您将 Scope 用作类型级别注解时，该类型的所有 Bean 都将具有该注解指定的作用域。当您将其用作方法级别注解时，由该注解方法创建的 Bean 将具有该注解指定的作用域。您必须将其放在带有 `org.springframework.context.annotation.Bean` 注解的方法上。

![Image](img/tab_6_2.jpg)



#### 将商品添加到购物车

在本节中，我们将迈出第一步，真正让网站访客能够从我们的书店购买图书。具体来说，我们将实现允许将图书添加到购物车的逻辑。为此，我们首先需要定义一个会话作用域的 `Cart` Bean。

清单 6-1 展示了如何定义一个具有 `session` 作用域的 Bean（即我们的购物车）。这个 Bean 可以像框架中的其他任何 Bean 一样被注入到其他 Bean 中。Spring 将负责管理 Bean 生命周期的复杂性。Bean 的生命周期取决于其作用域（参见表 6-1）。例如，一个 `singleton` 作用域的 Bean（默认情况）与应用程序上下文的生命周期绑定，而一个 `session` 作用域的 Bean 则与 `javax.servlet.http.HttpSession` 对象的生命周期绑定。

***清单 6-1.** 购物车会话作用域 Bean*

`package com.apress.prospringmvc.bookstore.web.config;`

`//其他导入已省略`

`import org.springframework.context.annotation.Scope;`
`import org.springframework.context.annotation.ScopedProxyMode;`
`import com.apress.prospringmvc.bookstore.domain.Cart;`

`@Configuration`
`@EnableWebMvc`
`@ComponentScan(basePackages = { "com.apress.prospringmvc.bookstore.web" })`
`public class WebMvcContextConfiguration extends WebMvcConfigurerAdapter {`

`    //其他方法已省略`
`    @Bean`
**`@Scope(value = "session", proxyMode = ScopedProxyMode.TARGET_CLASS)`**
`    public Cart cart() {`
`        return new Cart();`
`    }`

`}`

在这个例子中，我们使用注解声明了一个 Bean，并使用了 `session` 作用域。我们希望使用基于类的代理（`com.apress.prospringmvc.bookstore.domain.Cart` 没有实现接口，因此我们需要基于类的代理）。现在，我们可以简单地将这个 Bean 注入到其他 Bean 中，并像使用其他任何 Bean 一样使用它。让我们创建一个使用此 Bean 的控制器，即 `com.apress.prospringmvc.bookstore.web.controller.CartController`（参见清单 6-2）。

![Image](img/square.jpg) **注意** 为了能够使用基于类的代理，Spring 需要一个名为 CGLIB 的额外库。^(1)

________________

¹ [`http://cglib.sourceforge.net/`](http://cglib.sourceforge.net/)

***清单 6-2.** CartController Bean*

`package com.apress.prospringmvc.bookstore.web.controller;`

`import org.slf4j.Logger;`
`import org.slf4j.LoggerFactory;`
`import org.springframework.beans.factory.annotation.Autowired;`
`import org.springframework.stereotype.Controller;`
`import org.springframework.web.bind.annotation.PathVariable;`
`import org.springframework.web.bind.annotation.RequestHeader;`
`import org.springframework.web.bind.annotation.RequestMapping;`

`import com.apress.prospringmvc.bookstore.domain.Book;`
`import com.apress.prospringmvc.bookstore.domain.Cart;`
`import com.apress.prospringmvc.bookstore.service.BookstoreService;`

`@Controller`
`public class CartController {`

`    private Logger logger = LoggerFactory.`*`getLogger`*`(CartController.class);`

**`@Autowired`**
**`private Cart cart;`**

`    @Autowired`
`    private BookstoreService bookstoreService;`

`    @RequestMapping("/cart/add/{bookId}")`
`    public String addToCart(@PathVariable("bookId") long bookId,`
`                            @RequestHeader("referer") String referer) {`
`        Book book = this.bookstoreService.findBook(bookId);`
`        this.cart.addBook(book);`
`        this.logger.info("Cart: {}", this.cart);`
`        return "redirect:" + referer;`
`    }`

`}`

在这个例子中，我们简单地自动装配了 `session` 作用域的 Bean `cart`，就像我们对其他任何 Bean 所做的那样。`addToCart` 方法包含了将图书添加到购物车的逻辑。添加图书后，我们会重定向到我们之前所在的页面（`referer` 请求头）。

这个控制器映射到了 URL `/cart/add/{bookId}`；但是，目前没有任何东西会调用我们的控制器，因为我们没有任何指向该 URL 的内容。让我们修改图书搜索页面并添加一个链接，这样我们就可以将图书添加到购物车中（参见清单 6-3）。以粗体突出显示的部分展示了所做的更改。

***清单 6-3.** 带有“添加到购物车”链接的图书搜索页面*

`<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core"%>`
`<%@ taglib prefix="spring" uri="http://www.springframework.org/tags" %>`
`<%@ taglib prefix="form" uri="http://www.springframework.org/tags/form" %>`

`// 搜索表单已省略`

`<c:if test="${not empty bookList}">`
`    <table>`
`        <tr>`
`            <th><spring:message code="book.title"/></th>`
`            <th><spring:message code="book.description"/></th>`
`            <th><spring:message code="book.price" /></th>`
**`<th></th>`**
`        </tr>`
`        <c:forEach items="${bookList}" var="book">`
`            <tr>`
`                <td>`
`                    <a href="<c:url value="/book/detail/${book.id}"/>">`
`                       ${book.title}`
`                    </a>`
`                </td>`
`                <td>${book.description}</td>`
`                <td>${book.price}</td>`
**`<td>`**
**`<a href="<c:url value="/cart/add/${book.id}"/>">`**
**`<spring:message code="book.addtocart"/>`**
**`</a>`**
**`</td>`**
`            </tr>`
`        </c:forEach>`
`    </table>`
`</c:if>`

重新部署我们的应用程序后，图书页面上应该会出现一个“添加到购物车”链接（参见图 6-1）。如果我们点击该链接，应该会停留在图书页面。不过，我们确实向购物车中添加了一些东西。如果我们切换到日志输出（参见图 6-2），可以看到购物车中确实有内容。

![Image](img/9781430241553_Fig06-01.jpg)

***图 6-1.** 带有“添加到购物车”链接的图书搜索页面*

![Image](img/9781430241553_Fig06-02.jpg)

***图 6-2.** 显示已添加到购物车中图书的购物车日志*



#### 实现结账

为了完成订购流程，我们将让顾客有机会结算购物车。这将在数据库中创建一个实际的 `com.apress.prospringmvc.bookstore.domain.Order` 对象和条目。结账功能结合了我们在上一章和上一节中介绍的许多内容。控制器是 `com.apress.prospringmvc.bookstore.web.controller.CheckoutController`（参见清单 6-4），其中包含大量逻辑。`checkout.jsp` 文件是包含我们界面的 JSP 文件；它位于 `/WEB-INF/views/cart` 目录下。

***清单 6-4.** CheckoutController*

`package com.apress.prospringmvc.bookstore.web.controller;`

`//其他导入已省略`
`import com.apress.prospringmvc.bookstore.validation.OrderValidator;`

`@Controller`
`@SessionAttributes(types = { Order.class })`
`@RequestMapping("/cart/checkout")`
`public class CheckoutController {`

`    private final Logger logger;`
`    logger  = LoggerFactory.getLogger(CheckoutController.class);`

`    @Autowired`
`    private Cart cart;`

`    @Autowired`
`    private BookstoreService bookstoreService;`

`    @ModelAttribute("countries")`
`    public Map<String, String> countries(Locale currentLocale) {`
`        Map<String, String> countries = new TreeMap<String, String>();`
`        for (Locale locale : Locale.getAvailableLocales()) {`
`            countries.put(`
`                       locale.getCountry(),`
`                       locale.getDisplayCountry(currentLocale));`
`        }`
`        return countries;`
`    }`

`    @RequestMapping(method = RequestMethod.GET)`
`    public void show(HttpSession session, Model model) {`
`        Account account = (Account)`
`        session.getAttribute(LoginController.ACCOUNT_ATTRIBUTE);`
`        Order order = this.bookstoreService.createOrder(this.cart, account);`
`        model.addAttribute(order);`
`    }`

`    @RequestMapping(method = RequestMethod.POST, params = "order")`
`    public String checkout(SessionStatus status,`
`                           @Validated @ModelAttribute Order order,`
`                           BindingResult errors) {`
`        if (errors.hasErrors()) {`
`            return "cart/checkout";`
`        } else {`
`            this.bookstoreService.store(order);`
`            status.setComplete(); //从会话中移除订单`
`            this.cart.clear(); // 清空购物车`
`            return "redirect:/index.htm";`
`        }`
`    }`

`    @RequestMapping(method = RequestMethod.POST, params = "update")`
`    public String update(@ModelAttribute Order order) {`
`        order.updateOrderDetails();`
`        return "cart/checkout";`
`    }`

`    @InitBinder`
`    public void initBinder(WebDataBinder binder) {`
`        binder.setValidator(new OrderValidator());`
`    }`

`}`

当我们点击结账时，控制器上调用的第一个方法是 `show` 方法，它获取我们的购物车，并使用存储在会话中的 `Account` 创建一个订单，并将其添加到模型中。由于使用了 `SessionAttributes`，订单在请求之间存储在会话中。完成后，将呈现结账页面（参见图 6-3）。

![图片](img/9781430241553_Fig06-03.jpg)

***图 6-3.** 结账页面*

当表单填写完毕后，顾客可以执行两个操作：他可以按下“下单”按钮或“更新”按钮。当按下“更新”按钮时，会调用 `update` 方法。该方法提交表单，然后更新订单（并重新计算总价）。当按下“下单”按钮时，订单被提交，然后由 `com.apress.prospringmvc.bookstore.validation.OrderValidator` 进行验证。如果出现任何错误，页面会重新显示，并向顾客展示错误信息。有趣的部分发生在没有错误时。首先，订单被存储到数据库中。当我们处理完订单后，需要将其从会话中移除，我们通过调用 `org.springframework.web.bind.support.SessionStatus` 对象上的 `setComplete` 方法来实现这一点（参见第 5 章的“支持的方法参数类型”部分）。最后，在重定向回首页之前，我们需要清空购物车。我们这样做是为了让顾客可以向购物车中添加新书。由于我们不能简单地替换 `session` 作用域的对象，我们需要调用一个方法来清空它。如果我们用一个新实例替换购物车，基本上会破坏作用域代理对象。

### 横切关注点

在开发企业应用时，我们经常面临或挑战横切关注点。这些是影响许多对象和动作的关注点。横切关注点的示例包括事务管理和安全性，以及为每个传入的 Web 请求公开通用数据等操作。

通常，使用传统的面向对象方法在我们的代码库中实现这些关注点是很困难的。如果我们以传统方式实现它们，会导致代码重复和难以维护的代码。对于我们的通用对象，我们可以使用面向切面编程（AOP）来处理这些横切关注点；然而，当涉及到将其应用于请求时，我们需要一种略有不同的方法。

Spring MVC 为我们提供了两种实现横切关注点的方法。第一种方法使用*拦截器*来实现通用逻辑，而第二种方法依赖于异常处理。在本节中，我们将探讨在 Web 应用程序中应用横切关注点的这两种技术。



#### 拦截器

拦截器之于请求处理器，就如同过滤器之于 Servlet。根据 Servlet 规范^(2)，过滤器是一段可重用的代码，能够转换 HTTP 请求、响应及头部信息的内容。过滤器可以修改或适配针对某个资源的请求，也可以修改或适配来自某个资源的响应。过滤的示例包括身份验证、审计和加密。

过滤器和拦截器都实现了通用功能（横切关注点），以应用于所有（或选定的）传入 HTTP 请求。过滤器比拦截器更强大，因为它们可以替换（或包装）传入的请求/响应，而拦截器无法做到这一点。另一方面，拦截器比过滤器拥有更多的生命周期方法（参见表 6-3）。

![图片](img/tab_6_3.jpg)

Spring MVC 有两种不同的拦截器策略：

*   `org.springframework.web.servlet.HandlerInterceptor`（参见清单 6-5）
*   `org.springframework.web.context.request.WebRequestInterceptor`（参见清单 6-6）

________________

² 参见 Servlet 规范 3.0，第 6 章。

***清单 6-5.** HandlerInterceptor 接口*

`package org.springframework.web.servlet;`

`import javax.servlet.http.HttpServletRequest;`
`import javax.servlet.http.HttpServletResponse;`

`public interface HandlerInterceptor {`

`  boolean preHandle(HttpServletRequest request,`
`                    HttpServletResponse response,`
`                    Object handler) throws Exception;`

`  void postHandle(HttpServletRequest request, HttpServletResponse response,`
`                  Object handler, ModelAndView modelAndView) throws Exception;`

`  void afterCompletion(HttpServletRequest request,`
`                       HttpServletResponse response,`
`                       Object handler,`
`                       Exception ex) throws Exception;`
`}`

***清单 6-6.** WebRequestInterceptor 接口*

`package org.springframework.web.context.request;`

`import org.springframework.ui.ModelMap;`

`public interface WebRequestInterceptor {`

`  void preHandle(WebRequest request) throws Exception;`

`  void postHandle(WebRequest request, ModelMap model) throws Exception;`

`  void afterCompletion(WebRequest request, Exception ex) throws Exception;`
`}`

与 Spring 框架中的常见情况一样，这两种策略都表示为接口，我们可以为其提供实现。这两种策略的主要区别在于，`WebRequestInterceptor` 与底层技术无关。它可以在 JSF 或 Servlet 环境中使用，而无需更改实现。而 `HandlerInterceptor` 只能在 Servlet 环境中使用。`HandlerInterceptor` 的一个优点是，我们可以用它来阻止处理器的调用。我们通过从 `preHandle` 方法返回 `false` 来实现这一点。

##### 配置拦截器

要使用拦截器，我们需要在配置中进行配置。配置拦截器包含两个步骤：

1.  我们需要配置拦截器本身。
2.  我们需要将其连接到我们的处理器。

将拦截器连接到我们的处理器有两种方式。可以同时使用这两种方法，但我们不推荐这样做。首先，我们可以在配置中显式地将拦截器添加到处理器映射中。其次，我们可以使用 `org.springframework.web.servlet .config.annotation.InterceptorRegistry` 来添加拦截器。

通常，更推荐使用 `InterceptorRegistry` 来添加拦截器，仅仅因为这是一种非常便捷的添加方式。而且，限制拦截器匹配哪些 URL 也非常容易（我们将在关于 `InterceptorRegistry` 的部分进行说明）。

###### 显式配置带有拦截器的 HandlerMapping

要将拦截器注册到处理器映射中，我们首先需要获取相关的处理器映射。为此，我们需要显式地添加它们，或者扩展 Spring 基类以获取对它们的引用（参见清单 6-7）。接下来，我们只需将所有拦截器添加到这些实例中。当使用多个处理器映射时，这可能会相当繁琐，尤其是当我们只想将拦截器应用于某些特定的 URL 时。

***清单 6-7.** 带有拦截器的显式 HandlerMapping 配置示例*

`package com.apress.prospringmvc.bookstore.web.config;`

`import org.springframework.context.annotation.ComponentScan;`
`import org.springframework.context.annotation.Configuration;`
`import org.springframework.web.servlet.config.annotation.EnableWebMvc;`
`import org.springframework.web.servlet.config.annotation.WebMvcConfigurationSupport;`
`import org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerMapping;`

`@Configuration`
`@EnableWebMvc`
`@ComponentScan(basePackages = { "com.apress.prospringmvc.bookstore.web" })`
`public class WebMvcContextConfiguration extends WebMvcConfigurationSupport {`

`    @Override`
`    public RequestMappingHandlerMapping requestMappingHandlerMapping() {`
`        RequestMappingHandlerMapping handlerMapping;`
`        handlerMapping = super.requestMappingHandlerMapping();`
`        handlerMapping.setInterceptors(getAllInterceptors());`
`        return handlerMapping`
`    }`
`}`



###### 使用 InterceptorRegistry

注册拦截器的一种更强大且灵活的方式是使用 `org.springframework.web.servlet.config.annotation.InterceptorRegistry`。添加到该注册表中的拦截器会被添加到所有已配置的处理器映射中。此外，通过这种方式，可以非常轻松地实现对特定 URL 的映射。要获取该注册表，我们需要在配置 Web 资源的配置类上实现 `org.springframework.web.servlet.config.annotation.WebMvcConfigurer` 接口。该接口包含多个回调方法，这些方法会在 Spring @MVC 配置期间被调用。

![Image](img/square.jpg) **提示** 框架提供了一个 `org.springframework.web.servlet.config.annotation.WebMvcConfigurerAdapter` 类，我们可以继承它。这样我们就不必实现 `org.springframework.web.servlet.config.annotation.WebMvcConfigurer` 接口中的所有方法。

我们也可以继承 `org.springframework.web.servlet.config.annotation.WebMvcConfigurationSupport`，从而可以简单地添加到默认配置中。这样我们就可以省略 `org.springframework.web.servlet.config.annotation.EnableWebMvc` 注解。

`InterceptorRegistry` 有两个方法（每种拦截器类型对应一个），我们可以用它们来添加拦截器（参见清单 6-8）。这两个方法都返回一个 `org.springframework.web.servlet.config.annotation.InterceptorRegistration` 实例，我们可以用它来微调拦截器的映射。我们可以使用 Ant 风格的路径模式^(3)来为已注册的拦截器配置细粒度的映射。如果不提供模式，该拦截器将应用于所有传入的请求。

***清单 6-8.** InterceptorRegistry 接口*

`package org.springframework.web.servlet.config.annotation;`

`import java.util.ArrayList;`
`import java.util.List;`

`import org.springframework.web.context.request.WebRequestInterceptor;`
`import org.springframework.web.servlet.HandlerInterceptor;`
`import org.springframework.web.servlet.handler.WebRequestHandlerInterceptorAdapter;`

`public class InterceptorRegistry {`

`      public InterceptorRegistration addInterceptor(`
`                HandlerInterceptor interceptor) { .. }`

`    public InterceptorRegistration addWebRequestInterceptor(`
`                WebRequestInterceptor interceptor) { .. }`
`}`

________________

³ 关于 Ant 风格表达式的信息，请参见第 3 章。

清单 6-9 展示了我们当前的配置。此时，我们已经配置了一个用于更改区域设置的拦截器，并且该拦截器被应用于所有传入的请求（我们没有指定要匹配的 URL 模式）。接下来，我们配置该拦截器，并使用 `addInterceptor` 方法将其添加到注册表中。框架将负责处理将拦截器注册到已配置的处理器映射中的其他细节。

***清单 6-9.** 使用 InterceptorRegistry 添加拦截器*

`package com.apress.prospringmvc.bookstore.web.config;`

`//其他导入已省略`

`import org.springframework.web.servlet.HandlerInterceptor;`
`import org.springframework.web.servlet.config.annotation.InterceptorRegistry;`

`@Configuration`
`@EnableWebMvc`
`@ComponentScan(basePackages = { "com.apress.prospringmvc.bookstore.web" })`
`public class WebMvcContextConfiguration extends WebMvcConfigurerAdapter {`

`    @Override`
`    public void addInterceptors(InterceptorRegistry registry) {`
`        registry.addInterceptor(localeChangeInterceptor());`
`    }`

`    @Bean`
`    public HandlerInterceptor localeChangeInterceptor() {`
`        LocaleChangeInterceptor localeChangeInterceptor;`
`        localeChangeInterceptor = new LocaleChangeInterceptor();`
`        localeChangeInterceptor.setParamName("lang");`
`        return localeChangeInterceptor;`
`    }`
`    //… 其他方法已省略`

`}`

清单 6-10 展示了一段代码片段，我们将映射从所有 URL 更改为仅以 `/customer` 开头的 URL。

***清单 6-10.** 将拦截器限制到特定 URL*

`package com.apress.prospringmvc.bookstore.web.config;`
`//导入已省略`
`@Configuration`
`@EnableWebMvc`
`@ComponentScan(basePackages = { "com.apress.prospringmvc.bookstore.web" })`
`public class WebMvcContextConfiguration extends WebMvcConfigurerAdapter {`

`    @Override`
`    public void addInterceptors(InterceptorRegistry registry) {`
`        InterceptorRegistration registration;`
`        registration = registry.addInterceptor(localeChangeInterceptor());`
**`registation.addPathPatterns("/customers/**");`**
`    }`
`    //其他方法已省略`
`}`

##### 实现拦截器

到目前为止，我们已经介绍了不同类型的拦截器以及如何注册它们，以便它们能够被使用。现在，让我们为我们的商店实现拦截器。我们将实现两个不同的拦截器。第一个拦截器会向我们的模型添加一些常用数据，以便我们可以将其展示给用户。第二个拦截器将解决一个安全需求：我们希望只有注册用户才能访问账户和结账页面。



###### 实现 WebRequestInterceptor

在本节中，我们将实现一个 `org.springframework.web.context.request.WebRequestInterceptor`。观察 图 6-4 中的网页，可以看到一个名为“随机图书”的区域。到目前为止，网页上的这个区域一直为空。现在，我们将创建一个拦截器，向模型中添加一些随机图书。为此，我们将实现 `postHandle` 方法（见 清单 6-11）。

![Image](img/square.jpg) **注意** 在真实的网店中，我们可能会将这个区域称为“新书上架”或“为您推荐”。

![Image](img/9781430241553_Fig06-04.jpg)

***图 6-4.** 未列出随机图书的欢迎页面*

***清单 6-11.** CommonDataInterceptor*

`package com.apress.prospringmvc.bookstore.web.interceptor;`

`import org.springframework.beans.factory.annotation.Autowired;`
`import org.springframework.ui.ModelMap;`
`import org.springframework.web.context.request.WebRequest;`
`import org.springframework.web.context.request.WebRequestInterceptor;`

`import com.apress.prospringmvc.bookstore.service.BookstoreService;`

`public class CommonDataInterceptor implements WebRequestInterceptor {`

`    @Autowired`
`    private BookstoreService bookstoreService;`

`    @Override`
`    public void preHandle(WebRequest request) throws Exception {`
`    }`

`    @Override`
`    public void postHandle(WebRequest request, ModelMap model)`
`    throws Exception {`
`        if (model != null) {`
**`model.addAttribute("randomBooks", this.bookstoreService.findRandomBooks());`**
`        }`
`    }`

`    @Override`
`    public void afterCompletion(WebRequest request, Exception ex)`
`    throws Exception {`
`    }`

`}`

`postHandle` 方法向模型添加了一些随机图书，但仅在模型可用时执行。这就是我们的代码中包含 `null` 检查的原因。当我们执行 AJAX 请求或自行编写响应时，模型可能为 `null`。

为了让拦截器应用于传入请求，我们需要注册它。该拦截器需要对每个传入请求都进行调用，因此不需要太多额外配置（见 清单 6-12 中高亮行）。

***清单 6-12.** CommonDataInterceptor 配置*

`package com.apress.prospringmvc.bookstore.web.config;`

`import org.springframework.web.context.request.WebRequestInterceptor;`
`import org.springframework.web.servlet.config.annotation.InterceptorRegistry;`
`com.apress.prospringmvc.bookstore.web.interceptor.CommonDataInterceptor;`

`// 其他导入已省略`

`@Configuration`
`@EnableWebMvc`
`@ComponentScan(basePackages = { "com.apress.prospringmvc.bookstore.web" })`
`public class WebMvcContextConfiguration extends WebMvcConfigurerAdapter {`

`    @Override`
`    public void addInterceptors(InterceptorRegistry registry) {`
`        registry.addInterceptor(localeChangeInterceptor());`
**`registry.addWebRequestInterceptor(commonDataInterceptor());`**
`    }`

`    @Bean`
`    public WebRequestInterceptor commonDataInterceptor() {`
`        return new CommonDataInterceptor();`
`    }`
`    // 其他方法已省略`
`}`

现在，当我们重新部署应用程序并访问页面时，应该会在页面的“随机图书”区域看到显示的随机图书（见 图 6-5）。（模板中用于选择随机图书的逻辑如 清单 6-13 所示。）

![Image](img/9781430241553_Fig06-05.jpg)

***图 6-5.** “随机图书”区域包含书名的欢迎页面*

***清单 6-13.** 模板中的随机图书区域*

`<div class=`*`"right_box"`*`>`

`<div class="title">`
`  <span class="title_icon">`
`    <img src="<c:url value="/resources/images/bullet4.gif"/>" alt="" title="" />`
`  </span>`
`  <spring:message code="main.title.randombooks"/>`
`</div>`
`<c:forEach items="${randomBooks}" var="book">`
`  <div class="new_prod_box">`
`    <c:url value="/book/detail/${book.id}" var="bookUrl" />`
`    <a href="${bookUrl}">${book.title}</a>`
`    <div class="new_prod_img">`
`      <c:url value="/resources/images/books/${book.isbn}/book_front_cover.png"`![Image](img/U002.jpg)
` var="bookImage"/>`
`      <a href="${bookUrl}">`
`        <img src="${bookImage}" alt="${book.title}" title="${book.title}"`![Image](img/U002.jpg)
` class="thumb" border="0" width="100px"/>`
`      </a>`
`    </div>`
`  </div>`
`</c:forEach>`
`</div>`



###### 实现 HandlerInterceptor

目前，我们的账户页面并不安全。例如，有人只需更改 URL 中的 id 就能查看其他账户的内容。让我们使用拦截器方法来为页面增加安全性。我们将创建一个拦截器，用于检查用户是否已登录（即我们的账户信息是否存在于 HTTP 会话中）。如果未登录，它将抛出一个 `com.apress.prospringmvc.bookstore.service.AuthenticationException`（参见清单 6-14）。我们还会将原始 URL 存储在一个会话属性中；这样，在用户登录后，我们可以将其重定向到他想访问的 URL。

***清单 6-14.** SecurityHandlerInterceptor*

`package com.apress.prospringmvc.bookstore.web.interceptor;`
`// javax.servlet imports omitted`
`import org.springframework.web.servlet.HandlerInterceptor;`
`import org.springframework.web.servlet.handler.HandlerInterceptorAdapter;`
`import org.springframework.web.util.WebUtils;`

`import com.apress.prospringmvc.bookstore.domain.Account;`
`import com.apress.prospringmvc.bookstore.service.AuthenticationException;`
`import com.apress.prospringmvc.bookstore.web.controller.LoginController;`

`public class SecurityHandlerInterceptor extends HandlerInterceptorAdapter {`

`    @Override`
`    public boolean preHandle(HttpServletRequest request,`
`                             HttpServletResponse response,`
`                             Object handler) throws Exception {`
`        Account account= (Account) WebUtils.getSessionAttribute(`
`                                request, LoginController.ACCOUNT_ATTRIBUTE);`
`        if (account == null) {`
`            //Retrieve and store the original URL.`
`            String url = request.getRequestURL().toString();`
`            WebUtils.setSessionAttribute(`
`                         request, LoginController.REQUESTED_URL, url);`
`            throw new AuthenticationException(`
`                 "Authentication required.", "authentication.required");`
`        }`
`        return true;`
`    }`

`}`

对于这个拦截器，我们的配置会稍微复杂一些，因为我们需要将其映射到特定的 URL（参见清单 6-15 中高亮的部分）。

***清单 6-15.** SecurityHandlerInterceptor 配置*

`package com.apress.prospringmvc.bookstore.web.config;`

`import com.apress.prospringmvc.bookstore.web.interceptor.SecurityHandlerInterceptor;`

`//Other imports omitted`

`@Configuration`
`@EnableWebMvc`
`@ComponentScan(basePackages = { "com.apress.prospringmvc.bookstore.web" })`
`public class WebMvcContextConfiguration extends WebMvcConfigurerAdapter {`

`    @Override`
`    public void addInterceptors(InterceptorRegistry registry) {`
`        registry.addInterceptor(localeChangeInterceptor());`
`        registry.addWebRequestInterceptor(commonDataInterceptor());`
**`registry.addInterceptor(new SecurityHandlerInterceptor()).`**
**`addPathPatterns("/customer/account*", "/cart/checkout");`**
`    }`

`    // Other methods omitted`
`}`

最后，我们还需要对 `com.apress.prospringmvc.bookstore.web.controller.AccountController` 进行修改。目前，我们期望 URL 中包含一个 id。但是，我们将不再从数据库中检索账户，而是从会话中恢复它。清单 6-16 展示了必要的修改。

***清单 6-16.** AccountController*

`package com.apress.prospringmvc.bookstore.web.controller;`

`// Imports omitted`

`@Controller`
`@RequestMapping("/customer/account")`
`@SessionAttributes(types = Account.class)`
`public class AccountController {`

`    //Fields and other methods omitted`

**`@RequestMapping(method = RequestMethod.GET`**`)`
`    public String index(Model model, HttpSession session) {`
**`Account account = (Account) session.getAttribute(LoginController.ACCOUNT_ATTRIBUTE);`**
`        model.addAttribute(account);`
`        model.addAttribute("orders", this.orderRepository.findByAccount(account));`
`        return "customer/account";`
`    }`

**`@RequestMapping(method = { RequestMethod.POST, RequestMethod.PUT })`**
`    public String update(@ModelAttribute Account account) {`
`        this.accountRepository.save(account);`
`        return "redirect:/customer/account";`
`    }`
`}`

当我们重新部署应用程序并点击菜单栏中的“账户”时，会看到一个错误页面（参见图 6-6）。我们使用默认的异常处理机制向客户端发送一个错误代码，以便浏览器能够据此做出响应。在下一节中，我们将更详细地介绍异常处理。

![Image](img/9781430241553_Fig06-06.jpg)

***图 6-6.** 点击受保护链接后显示的 403 错误页面*

虽然我们已经保护了资源，但如果能在用户需要登录才能查看请求页面时，向用户显示登录页面并附带一条提示信息，那会更好。这正是我们将在下一节中要做的事情。

#### 异常处理

如第 4 章所述，当请求处理过程中发生异常时，Spring 会尝试处理该异常。为了提供一种通用的异常处理方式，Spring 使用了另一种策略，该策略可以通过实现 `org.springframework.web.servlet.HandlerExceptionResolver` 接口来利用。

`org.springframework.web.servlet.HandlerExceptionResolver` 为调度器 servlet 提供了一个回调方法（参见清单 6-17）。当请求处理工作流中发生异常时，会调用此方法。该方法可以返回一个 `org.springframework.web.servlet.ModelAndView`，也可以选择自行处理该 `Exception`。

***清单 6-17.** HandlerExceptionResolver 接口*

`package org.springframeowork.web.servlet;`

`import javax.servlet.http.HttpServletRequest;`
`import javax.servlet.http.HttpServletResponse;`

`public interface HandlerExceptionResolver {`

`  ModelAndView resolveException(HttpServletRequest request,`
`                                HttpServletResponse response,`
`                                Object handler,`
`                                Exception ex);`
`}`

默认情况下，调度器 servlet 会在应用程序上下文中查找所有类型为 `org.springframework.web.servlet.HandlerExceptionResolver` 的 bean（参见第 4 章中的“配置 DispatcherServlet”部分）。当检测到多个解析器时，调度器 servlet 会依次咨询每个解析器，直到返回一个视图名称或写入响应。如果异常无法处理，则会重新抛出异常，以便 servlet 容器进行处理。servlet 容器将使用其配置中的错误页面配置，或者直接将异常传播给用户。（在大多数情况下，您会在屏幕上看到一个带有堆栈跟踪信息的 500 错误。）

Spring MVC 提供了 `org.springframework.web.servlet.HandlerExceptionResolver` 接口的多个实现，如图 6-6 所示。请注意，每个实现的工作方式都不同。表 6-4 简要概述了不同实现的工作方式。

![Image](img/9781430241553_Fig06-07.jpg)

***图 6-7.** HandlerExceptionResolver 层次结构*

![Image](img/tab_6_4.jpg)

如图 6-7 中的类图所示，所有用于解析异常的可用的实现都扩展了 `org.springframework.web.servlet.handler.AbstractHandlerExceptionResolver`。这是一个方便的父类，为所有实现提供了通用特性和配置选项。表 6-5 列出并简要描述了其通用属性。

![Image](img/tab_6_5.jpg)



##### DefaultHandlerExceptionResolver

`DefaultHandlerExceptionResolver` 实现始终返回一个空的 `ModelAndView`，并向客户端发送 HTTP 响应码。在表 6-6 中，我们可以看到异常与 HTTP 响应码及描述之间的映射关系。

![Image](img/tab_6_6.jpg)

##### ResponseStatusExceptionResolver

`ResponseStatusExceptionResolver` 会检查抛出的异常是否带有 `org.springframework.web.bind.annotation.ResponseStatus` 注解（参见清单 6-18）。如果是，它将处理该异常，将注解中的 HTTP 响应码发送给客户端，然后返回一个空的 `ModelAndView` 以表明异常已被处理。如果该注解不存在，则直接返回 `null`，表示异常未被处理。

***清单 6-18.** 处理 AuthenticationException*

`package com.apress.prospringmvc.bookstore.service;`

`import org.springframework.http.HttpStatus;`
`import org.springframework.web.bind.annotation.ResponseStatus;`

**`@ResponseStatus(value = HttpStatus.FORBIDDEN)`**
`public class AuthenticationException extends Exception {`

`    private String code;`

`    public AuthenticationException(String message, String code) {`
`        super(message);`
`        this.code = code;`
`    }`

`    public String getCode() {`
`        return this.code;`
`    }`
`}`

当我们抛出此异常时，`org.springframework.web.servlet.mvc.annotation.ResponseStatusExceptionResolver` 会检测到它已被 `org.springframework.web.servlet.bind.annotation.ResponseStatus` 注解标注。该注解有两个属性可用于指定信息（参见表 6-7）。

![Image](img/tab_6_7.jpg)

![Image](img/square.jpg) **注意** 这是我们用来让框架处理 `com.apress.prospringmvc.bookstore.service.AuthenticationException` 的机制。

#### SimpleMappingExceptionResolver

`SimpleMappingExceptionResolver` 可以配置为将特定异常转换为视图。例如，我们可以将（部分）异常类名映射到某个视图。这里之所以说“部分”，是因为匹配是基于类名而非具体类型进行的。匹配通过简单的子字符串机制完成；因此，不支持通配符（ant 风格的正则表达式）。

清单 6-19 展示了 `SimpleMappingExceptionResolver` 的配置。它被配置为将 `AuthenticationException` 映射到名为 `login` 的视图。我们还设置了一个与 `login` 视图一起发送的 HTTP 响应码。

***清单 6-19.** SimpleMappingExceptionResolver 配置*

`package com.apress.prospringmvc.bookstore.web.config;`

`import org.springframework.web.servlet.HandlerExceptionResolver;`
`import org.springframework.web.servlet.handler.SimpleMappingExceptionResolver;`

`// 导入省略`

`@Configuration`
`@EnableWebMvc`
`@ComponentScan(basePackages = { "com.apress.prospringmvc.bookstore.web" })`
`public class WebMvcContextConfiguration extends WebMvcConfigurerAdapter {`

`    @Override`
`    public void configureHandlerExceptionResolvers(`
`                    List<HandlerExceptionResolver> exceptionResolvers) {`
`        exceptionResolvers.add(simpleMappingExceptionResolver());`
`    }`

`    @Bean`
`    public SimpleMappingExceptionResolver simpleMappingExceptionResolver() {`
`        SimpleMappingExceptionResolver exceptionResolver;`
`        exceptionResolver = new SimpleMappingExceptionResolver();`
`        Properties mappings = new Properties();`
`        mappings.setProperty("AuthenticationException", "login");`

`        Properties statusCodes = new Properties();`
`        mappings.setProperty(`
`                    "login",`
`                    String.valueOf(HttpServletResponse.SC_UNAUTHORIZED));`

`        exceptionResolver.setExceptionMappings(mappings);`
`        exceptionResolver.setStatusCodes(statusCodes);`
`        return exceptionResolver;`
`    }`
`    // 其他方法省略`
`}`

匹配是基于类名而非具体类型进行的。如果抛出的异常的类名与指定模式匹配，则将使用相应的视图名称。该模式不支持通配符；它仅仅是匹配类名的子字符串。我们需要谨慎选择模式。例如，`Exception` 将匹配几乎所有抛出的异常（因为大多数异常的类名中都包含 `Exception`）。同样，`DataAccessException` 将或多或少匹配 Spring 中所有用于数据访问的异常。

我们需要做最后一项调整：即修改我们的 `com.apress.prospringmvc.bookstore.web.controller.LoginController`。目前，控制器内部存在异常处理逻辑；但可以将其移除，因为 `AuthenticationException` 将由我们刚刚配置的 `HandlerExceptionResolver` 处理（改进后的控制器见清单 6-20）。

***清单 6-20.** 改进后的 Login 控制器*

`package com.apress.prospringmvc.bookstore.web.controller;`
`// 导入省略`

`@Controller`
`@RequestMapping(value = "/login")`
`public class LoginController {`

`    public static final String ACCOUNT_ATTRIBUTE = "account";`
`    public static final String REQUESTED_URL = "REQUESTED_URL";`

`    @Autowired`
`    private AccountService accountService;`

`    @RequestMapping(method = RequestMethod.GET)`
`    public void login() {}`

`    @RequestMapping(method = RequestMethod.POST)`
`    public String handleLogin(@RequestParam String username,`
`                              @RequestParam String password,`
`                              HttpSession session)`
`    throws AuthenticationException {`
`        Account account = this.accountService.login(username, password);`
`        session.setAttribute(ACCOUNT_ATTRIBUTE, account);`
`        String url = (String) session.getAttribute(REQUESTED_URL);`
`        session.removeAttribute(REQUESTED_URL); // 移除该属性`
`        if (StringUtils.hasText(url) && !url.contains("login")) {`
`            return "redirect:" + url;`
`        } else {`
`            return "redirect:/index.htm";`
`        }`
`    }`

`}`

重新部署后，如果我们点击菜单栏上的“Account”，将会看到一个登录页面（参见图 6-8）。

![Image](img/9781430241553_Fig06-08.jpg)

***图 6-8.** 登录页面*



##### ExceptionHandlerExceptionResolver 与 AnnotationMethodHandlerExceptionResolver

`AnnotationMethodHandlerExceptionResolver` 和 `ExceptionHandlerExceptionResolver` 都会在当前控制器中查找带有 `org.springframework.web.bind.annotations.ExceptionHandler` 注解的方法。这也是它们的缺点，因为这对异常解析器仅作用于当前指定控制器中的方法。如果使用这种技术，就无法为整个应用程序定义全局异常处理器。

异常处理方法与控制器方法非常相似（如第 5 章所述）；它们可以使用相同的方法参数和返回值。异常处理方法使用相同的基础设施来检测返回类型和方法参数类型。不过，这些方法有一个额外的特性：我们还可以传入抛出的异常；具体来说，我们可以指定一个类型为 `Exception`（或其子类）的参数。

清单 6-21 中的方法将处理其所在控制器中抛出的所有异常。它会向客户端返回错误代码 500，并附带给定的原因。这是我们可以编写的最基本的异常处理方法。如前所述，我们可以在方法签名中使用多个参数。这既适用于方法参数，也适用于方法返回类型。（概述请参见前一章的表 5-3 和表 5-4。）

***清单 6-21.** 基本异常处理方法示例*

`@ExceptionHandler()`
`@ResponseStatus(`
`    value=HttpStatus.INTERNAL_SERVER_ERROR,`
`    reason="Exception while handling request.")`
`public void handleException() {}`

清单 6-22 展示了一个更详细的示例。当发生 `org.springframework.dao.DataAccessException` 时，它会尽可能多地向模型中填充信息。之后，将渲染名为 `db-error` 的视图。

***清单 6-22.** 高级异常处理方法示例*

`@ExceptionHandler`
`public ModelAndView handleIOException(DataAccessException ex,`
`                                      Principal principal,`
`                                      WebRequest request) {`
`    ModelAndView mav = new ModelAndView("db-error");`
`    mav.addObject("username", principal.getName());`
`    mav.addAllObjects(request.getParameterMap());`

`    for(Iterator<String> names = request.getHeaderNames(); names.hasNext(); ) {`
`        String name =  names.next();`
`        String[] value = request.getHeaderValues(name);`
`        mav.addObject(name, value);`
`    }`
`    return mav;`
`}`

### 扩展 Spring @MVC

在前面的章节中，我们解释了 Spring MVC 的工作原理以及如何编写控制器。然而，有时框架提供的开箱即用支持可能不够用，我们想要更改或添加框架的行为。总的来说，Spring 框架由于其构建方式而非常灵活。它使用了大量的策略和委托，这也是我们可以用来扩展或修改框架行为的方式。在本节中，我们将深入探讨请求映射、请求处理和表单渲染的内部机制。最后，我们将介绍如何扩展这些功能。

#### 扩展 RequestMappingHandlerMapping

为了将传入的请求映射到控制器方法，Spring 使用了处理器映射。在我们的用例中，我们一直使用的是 `org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerMapping`，并且我们已经多次提到它非常灵活。为了基于带有 `org.springframework.web.bind.annotation.RequestMapping` 注解的方法来匹配请求，处理器映射会查询多个 `org.springframework.web.servlet.mvc.condition.RequestCondition` 实现（参见图 6-8）。

![图片](img/9781430241553_Fig06-09.jpg)

***图 6-9.** RequestCondition 类图*

如图所示，`org.springframework.web.bind.annotation.RequestMapping` 注解的每个属性（即 `consumes`、`headers`、`methods`、`params`、`produces` 和 `value`；更多详情请参见表 5-2）都有一个对应的实现。`RequestConditionHolder` 和 `RequestMappingInfo` 是框架内部使用的两个实现。

要创建一个实现，我们需要两样东西。首先，我们需要实现该接口（API 见清单 6-23）。其次，我们需要扩展 `org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerMapping`。这个类包含两个回调方法，它们充当自定义请求方法的工厂方法（见清单 6-24）。`getCustomTypeCondition` 方法被调用来创建匹配类型级别条件的实例，而 `getCustomMethodCondition` 方法用于方法级别的条件。

***清单 6-23.** RequestCondition API*

`package org.springframework.web.servlet.mvc.condition;`

`import javax.servlet.http.HttpServletRequest;`

`import org.springframework.web.bind.annotation.RequestMapping;`

`public interface RequestCondition<T> {`

`    T combine(T other);`

`    T getMatchingCondition(HttpServletRequest request);`

`    int compareTo(T other, HttpServletRequest request);`
`}`

***清单 6-24.** RequestMappingHandlerMapping*

`package org.springframework.web.servlet.mvc.method.annotation;`

`import java.lang.reflect.Method;`

`import org.springframework.core.annotation.AnnotationUtils;`
`import org.springframework.stereotype.Controller;`
`import org.springframework.web.bind.annotation.RequestMapping;`
`import org.springframework.web.servlet.mvc.condition.ConsumesRequestCondition;`
`import org.springframework.web.servlet.mvc.condition.HeadersRequestCondition;`
`import org.springframework.web.servlet.mvc.condition.ParamsRequestCondition;`
`import org.springframework.web.servlet.mvc.condition.PatternsRequestCondition;`
`import org.springframework.web.servlet.mvc.condition.ProducesRequestCondition;`
`import org.springframework.web.servlet.mvc.condition.RequestCondition;`
`import org.springframework.web.servlet.mvc.condition.RequestMethodsRequestCondition;`
`import org.springframework.web.servlet.mvc.method.RequestMappingInfo;`
`import org.springframework.web.servlet.mvc.method.RequestMappingInfoHandlerMapping;`

`public class RequestMappingHandlerMapping`
`extends RequestMappingInfoHandlerMapping {`

`    // 其他方法已省略。`

`    protected RequestCondition<?> getCustomMethodCondition(Method method) {`
`        return null;`
`    }`

`    protected RequestCondition<?> getCustomTypeCondition(Class<?> handlerType) {`
`        return null;`
`    }`
`}`

#### 扩展 RequestMappingHandlerAdapter

与 `RequestMappingHandlerMapping` 类似，`RequestMappingHandlerAdapter` 也使用了几种不同的策略来完成其工作。为了确定向方法参数注入什么内容，适配器会查询多个 `org.springframework.web.method.support.HandlerMethodArgumentResolver` 实现。对于返回类型，它会查询已注册的 `org.springframework.web.method.support.HandlerMethodReturnValueHandler` 实现。



##### HandlerMethodArgumentResolver

`RequestMappingHandlerAdapter` 使用 `HandlerMethodArgumentResolver` 来确定方法参数的具体内容。对于每种受支持的方法参数类型或注解，几乎都有对应的实现（参见第 5 章的“受支持的方法参数类型”一节）。该 API 相当简单，如清单 6-25 所示。

***清单 6-25.** HandlerMethodArgumentResolver API*

`package org.springframework.web.method.support;`

`import org.springframework.core.MethodParameter;`
`import org.springframework.web.bind.WebDataBinder;`
`import org.springframework.web.bind.support.WebDataBinderFactory;`
`import org.springframework.web.context.request.NativeWebRequest;`

`public interface HandlerMethodArgumentResolver {`

`    boolean supportsParameter(MethodParameter parameter);`

`    Object resolveArgument(MethodParameter parameter,`
`                           ModelAndViewContainer mavContainer,`
`                           NativeWebRequest webRequest,`
`                           WebDataBinderFactory binderFactory)`
`       throws Exception;`

`}`

每个已注册的 `HandlerMethodArgumentResolver` 都会调用 `supportsParameter` 方法。返回 `true` 的那个解析器将用于检测或创建该方法参数的实际值。我们通过调用 `resolveArgument` 方法来实现这一点。

##### HandlerMethodReturnValueHandler

`HandlerMethodReturnValueHandler` 与 `HandlerMethodArgumentResolver` 类似，但有一个重要区别。顾名思义，`HandlerMethodReturnValueHandler` 处理的是方法的返回值。对于每种受支持的返回值类型或注解，都有对应的实现（参见第 5 章的“受支持的返回值”一节）。该 API 同样非常简单，如清单 6-26 所示。

***清单 6-26.** HandlerMethodReturnValueHandler*

`package org.springframework.web.method.support;`

`import org.springframework.core.MethodParameter;`
`import org.springframework.web.context.request.NativeWebRequest;`

`public interface HandlerMethodReturnValueHandler {`

`    boolean supportsReturnType(MethodParameter returnType);`

`    void handleReturnValue(Object returnValue,`
`                           MethodParameter returnType,`
`                           ModelAndViewContainer mavContainer,`
`                           NativeWebRequest webRequest)`
`    throws Exception;`

`}`

每个已注册的 `HandlerMethodReturnValueHandler` 的 `supportsReturnType` 方法都会被调用，并传入方法的返回类型。返回 `true` 的那个处理器将用于实际处理返回值。这是通过调用 `handleReturnValue` 方法完成的。

##### 实现你自己的处理器

我们可以利用 `RequestMappingHandlerAdapter` 所使用的策略来为我们服务。例如，我们想要一种简单的方法来在 `javax.servlet.http.HttpSession` 中存储和检索对象。为此，我们首先需要一个注解来标记方法参数或返回类型，表明我们希望从 `HttpSession` 中检索或放入其中。清单 6-27 描述了我们将要使用的注解。

***清单 6-27.** SessionAttribute 注解*

`package com.apress.prospringmvc.bookstore.web.method.support;`

`import java.lang.annotation.Documented;`
`import java.lang.annotation.ElementType;`
`import java.lang.annotation.Retention;`
`import java.lang.annotation.RetentionPolicy;`
`import java.lang.annotation.Target;`

`@Target({ ElementType.PARAMETER, ElementType.METHOD })`
`@Retention(RetentionPolicy.RUNTIME)`
`@Documented`
`public @interface SessionAttribute {`

`    String value() default "";`

`    boolean required() default true;`

`    boolean exposeAsModelAttribute() default false;`
`}`

然而，仅仅添加一个注解本身并没有太大帮助，因为我们还需要一个使用该注解的类。由于我们希望能够在 `HttpSession` 中检索和存储数据，我们将创建一个同时实现 `HandlerMethodReturnValueHandler` 和 `HandlerMethodArgumentResolver` 接口的类（参见清单 6-28）。

***清单 6-28.** SessionAttributeProcessor*

`package com.apress.prospringmvc.bookstore.web.method.support;`

`import org.springframework.core.MethodParameter;`
`import org.springframework.web.bind.MissingServletRequestParameterException;`
`import org.springframework.web.bind.support.WebDataBinderFactory;`
`import org.springframework.web.context.request.NativeWebRequest;`
`import org.springframework.web.context.request.WebRequest;`
`import org.springframework.web.method.support.HandlerMethodArgumentResolver;`
`import org.springframework.web.method.support.HandlerMethodReturnValueHandler;`
`import org.springframework.web.method.support.ModelAndViewContainer;`

`public class SessionAttributeProcessor implements`![Image](img/U002.jpg)
`HandlerMethodReturnValueHandler, HandlerMethodArgumentResolver {`

`    @Override`
`    public boolean supportsReturnType(MethodParameter returnType) {`
`        return returnType.getMethodAnnotation(SessionAttribute.class) != null;`
`    }`

`    @Override`
`    public void handleReturnValue(Object returnValue,`
`                                  MethodParameter returnType,`
`                                  ModelAndViewContainer mavContainer,`
`                                  NativeWebRequest webRequest)`
`    throws Exception {`

`        SessionAttribute annotation;`
`        annotation = returnType.getMethodAnnotation(SessionAttribute.class);`
`        webRequest.setAttribute(annotation.value(), returnValue,`![Image](img/U002.jpg)
`WebRequest.SCOPE_SESSION);`
`        exposeModelAttribute(annotation, returnValue, mavContainer);`
`    }`

`    @Override`
`    public boolean supportsParameter(MethodParameter parameter) {`
`        return parameter.hasParameterAnnotation(SessionAttribute.class);`
`    }`

`    private void exposeModelAttribute(SessionAttribute annotation,`
`                                      Object value,`
`                                      ModelAndViewContainer mavContainer) {`
`        if (annotation.exposeAsModelAttribute()) {`
`            mavContainer.addAttribute(annotation.value(), value);`
`        }`
`    }`

`    @Override`
`    public Object resolveArgument(MethodParameter parameter,`
`                                  ModelAndViewContainer mavContainer,`
`                                  NativeWebRequest webRequest,`
`                                  WebDataBinderFactory binderFactory)`
`    throws Exception {`

`        SessionAttribute annotation;`
`        annotation = parameter.getParameterAnnotation(SessionAttribute.class);`


好的，作为高级文档工程师和翻译员，我将严格遵循您提供的注意事项和示例格式，对给定的英文文本进行翻译。


`        Object value = webRequest.getAttribute(`
`                        annotation.value(), WebRequest.SCOPE_SESSION);`
`        if (value == null && annotation.required()) {`
`            throw new MissingServletRequestParameterException(`
`                        annotation.value(),`
`                        parameter.getParameterType().getName());`
`        }`
`        exposeModelAttribute(annotation, value, mavContainer);`
`        return value;`
`    }`
`}`

在使用处理器之前，我们需要对其进行配置。为此，我们需要修改配置类。具体来说，我们需要将处理器添加为一个 bean，并让环境感知到这个 bean 的存在（参见清单 6-29）。

***清单 6-29.** 修改后的 WebMvcContextConfiguration*

`package com.apress.prospringmvc.bookstore.web.config;`

`com.apress.prospringmvc.bookstore.web.method.support.SessionAttributeProcessor;`

`// 其他导入已省略`

`@Configuration`
`@EnableWebMvc`
`@ComponentScan(basePackages = { "com.apress.prospringmvc.bookstore.web" })`
`public class WebMvcContextConfiguration extends WebMvcConfigurerAdapter {`

`    @Bean`
`    public SessionAttributeProcessor sessionAttributeProcessor() {`
`        return new SessionAttributeProcessor();`
`    }`

`    @Override`
`    public void addArgumentResolvers(`
`                    List<HandlerMethodArgumentResolver> argumentResolvers) {`
`        argumentResolvers.add(sessionAttributeProcessor());`
`    }`

`    @Override`
`    public void addReturnValueHandlers(`
`               List<HandlerMethodReturnValueHandler> returnValueHandlers) {`
`        returnValueHandlers.add(sessionAttributeProcessor());`
`    }`
`    // 其他方法已省略`
`}`

现在我们已经配置好了处理器，终于可以开始使用它了。让我们从修改账户控制器开始，如清单 6-30 所示。我们移除了直接访问会话以及将账户添加到模型的需求，现在这一切都由处理器处理。粗体字反映了这些变化；它仅仅是方法参数上的一个注解。至此，我们不再需要直接访问 HTTP 会话。

***清单 6-30.** 修改后的 AccountController*

`package com.apress.prospringmvc.bookstore.web.controller;`

`// 导入已省略`

`@Controller`
`@RequestMapping("/customer/account")`
`@SessionAttributes(types = Account.class)`
`public class AccountController {`

`    @RequestMapping(method = RequestMethod.GET)`
`    public String index(`
`            Model model,`
**`@SessionAttribute(value =`**![Image](img/U002.jpg)
**`LoginController.ACCOUNT_ATTRIBUTE, exposeAsModelAttribute = true) Account account) {`**
`        model.addAttribute("orders", this.orderRepository.findByAccount(account));`
`        return "customer/account";`
`    }`
`    // 其他方法已省略`
`}`

如果我们现在重新启动应用程序并点击“账户”（登录后），就会看到我们的账户页面（参见图 6-10）。

![Image](img/9781430241553_Fig05-10.jpg)

***图 6-10.** 账户页面*

#### 使用 RequestDataValueProcessor

`org.springframework.web.servlet.support.RequestDataValueProcessor` 是一个可选组件，我们可以用它来在渲染请求参数值或发出重定向前检查或修改这些值。

我们可以将此组件用作解决方案的一部分^(4)，以提供数据完整性、机密性以及防范跨站请求伪造（CSRF）。^(5) 我们还可以用它来自动向所有表单和 URL 添加隐藏字段。

`RequestDataValueProcessor` API 包含四个方法（参见清单 6-31）。

________________

⁴ [`http://www.hdiv.org`](http://www.hdiv.org)

⁵ [`http://www.owasp.org`](http://www.owasp.org)

***清单 6-31.** RequestDataValueProcessor API*

`package org.springframework.web.servlet.support;`

`import java.util.Map;`

`import javax.servlet.http.HttpServletRequest;`

`public interface RequestDataValueProcessor {`

`    String processAction(HttpServletRequest request, String action);`

`    String processFormFieldValue(HttpServletRequest request, String name,`
`                                 String value, String type);`

`    Map<String, String> getExtraHiddenFields(HttpServletRequest request);`

`    String processUrl(HttpServletRequest request, String url);`
`}`

我们可以使用这个接口来做一些有趣的事情。例如，我们可以在控制器（或拦截器）中为不可编辑的字段（如 `id`）创建一个校验和，然后检查这个校验和，看看是否有字段被篡改。

`processUrl` 方法由 `Url` 标签使用；在重定向时，我们可以用它来编码或向 URL 添加额外参数，以保护我们的 URL（例如，我们可以添加一个校验和来检查参数的有效性）。

框架没有提供默认实例。因此，需要为我们的应用程序定制一个实现（HDIV 网站有一个插件，可以保护网站免受一系列漏洞的攻击）。要配置 `RequestDataValueProcessor`，我们需要将其添加到应用程序上下文中，然后以名称 `requestDataValueProcessor` 注册它。这是框架用来检测已注册实例的名称。

### 总结

在本章中，我们介绍了一些可用于构建 Web 应用程序的更高级技术。例如，我们首先了解了作用域 bean 以及如何利用它们。为此，我们在示例应用程序中实现了一个购物车。

有时，我们需要跨大量类或 URL 重用代码或执行代码。这些所谓的横切关注点可以通过使用面向切面编程来解决；然而，在 Web 应用程序中，这并不总是最佳选择。在 Spring MVC 中，我们可以使用拦截器和高级异常处理策略来处理这些横切关注点。例如，我们可以使用拦截器为大量控制器执行一段代码。在配置这些拦截器时，我们可以指定是映射到所有控制器，还是仅根据 URL 映射到某些控制器。

尽管我们都努力使应用程序尽可能健壮，但总有可能出现问题。当问题发生时，我们希望能够优雅地处理它们。例如，当我们需要用户凭据时，我们可能希望向用户显示错误页面或登录页面。为此，我们深入研究了 Spring MVC 内部的异常处理策略。

接着，我们更深入地探讨了 Spring MVC 的基础设施类，并研究了在需要时如何扩展框架。我们还解释了如何通过指定额外的请求条件来扩展请求匹配。接下来，我们解释（并演示）了如何编写一个处理器来处理请求处理方法的方法参数类型和返回值。

最后，我们以对请求数据值处理器的简要介绍结束了本章，涵盖了如何使用它来防范 CSRF 并提供数据完整性。

## 第 7 章



## REST 与 AJAX

到目前为止，我们一直在构建相当经典的 Web 应用程序：向服务器发送请求，服务器处理请求，然后我们渲染结果并展示给客户端。然而，在过去的十年里，我们构建 Web 应用程序的方式发生了巨大变化。现在我们有了 JavaScript 和 JSON/XML，它们支持基于 AJAX 的 Web 应用程序，并且将越来越多的行为推送到客户端，包括验证、渲染部分屏幕等。

本章首先介绍 REST^(1)（表述性状态转移），这是一种概念或架构风格，它影响了我们作为开发者对 Web 资源的思考方式以及应如何处理它们。接下来，我们将讨论 AJAX，并将其与 REST 结合考虑。

本章的第二部分涵盖文件上传。你将看到如何使用 Spring 框架进行文件上传，以及如何在控制器中处理此任务。但在深入探讨之前，让我们先看看 REST。

### 表述性状态转移 (REST)

本节简要解释 REST 的主题。该概念本质上包含两个部分：首先是资源以及我们如何标识它们，然后是我们操作或使用这些资源的方式。REST 于 2000 年由 Roy Thomas Fielding 在一篇题为《*架构风格与基于网络的软件架构设计*》的论文中提出。^(2) 它描述了如何使用 HTTP 协议以及该协议提供的特性来操作资源。

#### 标识资源

在第 4 章中，我们简要讨论了 URL（统一资源定位符）的组成部分。对于 REST 而言，这并没有真正改变；然而，URL 很重要，因为它指向一个唯一的资源。表 7-1 给出了一些资源位置的示例。

_________

¹ [`www.ics.uci.edu/~fielding/pubs/dissertation/rest_arch_style.htm`](http://www.ics.uci.edu/~fielding/pubs/dissertation/rest_arch_style.htm)

² [`www.ics.uci.edu/~fielding/pubs/dissertation/top.htm`](http://www.ics.uci.edu/~fielding/pubs/dissertation/top.htm)

![Image](img/9781430241553_tab07-01.jpg)

在 REST 中，一切都关乎资源的*表述*，因此 URL/URI 非常重要。它为我们提供了实际资源（网页、网页上的图片、mp3 文件等）的位置。我们在 Web 浏览器中看到的并非实际资源，而是该资源的一种表述。下一节将解释如何使用此资源位置来操作（修改、删除等）该资源。

#### 操作资源

HTTP 协议指定了几种方法（HTTP 方法^(3)) 来处理来自我们应用程序的信息。表 7-2 给出了这些方法的概述。

![Image](img/9781430241553_tab07-02.jpg)

_________

³ [`www.w3.org/Protocols/rfc2616/rfc2616-sec9.html`](http://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)

在上一节“标识资源”中，我们提到了 URL 如何指向一个资源。如果我们将 REST 与表 7-1 中的资源结合起来，我们实际上可以操作它们，如表 7-3 所示。

![Image](img/9781430241553_tab07-03.jpg)

HTTP 方法的列表比大多数 Web 浏览器支持的要多。通常，它们只支持 `GET` 和 `POST` 方法，而不支持其他已标识的方法。为了能够在经典的 Web 应用程序中使用不同的方法，我们需要使用一种变通方法；为此，Spring MVC 提供了 `HiddenHttpMethodFilter`。

##### HiddenHttpMethodFilter

`org.springframework.web.filter.HiddenHttpMethodFilter` 会将一个 `POST` 请求转换为另一种指定类型的请求。它使用一个请求参数来确定传入请求应使用哪种方法。默认情况下，它使用名为 `_method` 的请求参数；但是，此名称可以配置。通过确保存在该请求参数，可以将 `POST` 请求“转换”为 `PUT` 或 `DELETE` 请求；然后该请求将被包装在一个 `HttpMethodRequestWrapper`（它是该过滤器的一个内部类）中。`GET` 请求会按原样处理，不会转换为其他类型的请求。这是因为 `GET` 请求与其他类型不同，其所有参数都编码在 URL 中。相比之下，`POST` 和 `PUT` 请求的参数则编码在请求体中。

让我们为我们的 Web 应用程序配置 `HiddenHttpMethodFilter`。打开 `com.apress.prospringmvc.bookstore.web.BookstoreWebApplicationInitializer`，让我们添加一个方法来添加我们的过滤器。清单 7-1 显示了所需的修改。

***清单 7-1.** 包含 HiddenHttpMethodFilter 的 BookstoreWebApplicationInitializer*

`package com.apress.prospringmvc.bookstore.web;`

`import java.util.EnumSet;`

`// javax.servlet imports omitted`

`import org.springframework.web.WebApplicationInitializer;`
`import org.springframework.web.filter.HiddenHttpMethodFilter;`

`public class BookstoreWebApplicationInitializer implements WebApplicationInitializer {`

`    private static final String DISPATCHER_SERVLET_NAME = "dispatcher";`

`    @Override`
`    public void onStartup(ServletContext servletContext) throws ServletException {`
`        registerListener(servletContext);`
`        registerDispatcherServlet(servletContext);`
**`registerHiddenHttpMethodFilter(servletContext);`**
`    }`

`    private void registerHiddenHttpMethodFilter(ServletContext servletContext) {`
`        FilterRegistration.Dynamic registration;`
`        registration = servletContext.addFilter("hiddenHttpMethodFilter",`
`                                 HiddenHttpMethodFilter.class);`
`        registration.addMappingForServletNames(`
`                 EnumSet.of(DispatcherType.REQUEST, DispatcherType.FORWARD),`
`                 false,`
`                 DISPATCHER_SERVLET_NAME);`
`    }`
`    // Other methods omitted`
`}`

现在我们已经配置了过滤器，需要修改我们的账户页面。打开 `account.jsp` 文件，确保有一个名为 `_method`、值为 `PUT` 的隐藏字段。清单 7-2 显示了页面的开头；如你所见，表单的开头定义了此隐藏字段。

***清单 7-2.** Account.jsp 头部*

`<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core"%>`
`<%@ taglib prefix="spring" uri="http://www.springframework.org/tags" %>`
`<%@ taglib prefix="form" uri="http://www.springframework.org/tags/form" %>`

`<form:form method="POST" modelAttribute="account" id="accountForm">`
**`<input type="hidden" name="_method" value="PUT" />`**
`    <fieldset>`
`        <legend><spring:message code="account.personal"/></legend>`

`// Remainder of page omitted`

当页面提交时，`HiddenHttpMethodFilter` 将执行其工作，并将我们的 `POST` 请求转换为 `PUT` 请求。控制器在编写时已经考虑到了这一点；请参见清单 7-3 中的更新方法。这里我们对 `POST` 和 `PUT` 复用了相同的方法，但我们也可以分别处理它们。

***清单 7-3.** AccountController 更新方法*

`package com.apress.prospringmvc.bookstore.web.controller;`

`//Imports omitted`

`@Controller`
`@RequestMapping("/customer/account")`
`@SessionAttributes(types = Account.class)`
`public class AccountController {`



`@RequestMapping(method = { RequestMethod.POST,` **`RequestMethod.PUT`** `})`
`    public String update(@ModelAttribute Account account) {`
`        this.accountRepository.save(account);`
`        return "redirect:/customer/account";`
`    }`
`    // 其他方法已省略`
`}`

使用过滤器仍然是一种在浏览器和普通表单中实现 REST 的变通方法，如果我们选择为网站采用**渐进增强**或**优雅降级**策略，这会很有用。渐进增强意味着在确保基础页面按预期工作后，再为页面添加丰富的交互行为。优雅降级则相反——我们先开发一个功能丰富的网站，然后确保即使某些特性不可用，整个网站仍能正常工作。

### 异步 JavaScript 与 XML（AJAX）

AJAX 这个术语由 Jesse James Garrett^(4) 于 2005 年提出^(5)。AJAX 本身并非一项技术，而是一系列技术的集合，它们协同工作，为我们的 Web 应用创造丰富的用户体验。AJAX 包含以下技术：

> *   基于标准的展示层（使用 HTML 和 CSS）
> *   动态显示与交互（使用文档对象模型 DOM）
> *   数据交换与处理（使用 XML 或 JSON）
> *   使用 `XMLHttpRequest` 进行异步数据检索
> *   使用 JavaScript 将所有技术整合在一起

尽管该缩写代表异步 JavaScript 与 XML，但在客户端与服务器之间传递数据时，它通常与 JavaScript 对象表示法（JSON）一起使用。

由于 AJAX 已经使用了数年，目前已有相当多的 JavaScript 框架可以更轻松地创建丰富的用户体验。对于 Spring MVC 而言，选择哪个 JavaScript 库并不重要，而且讨论市面上众多的 JavaScript 库也超出了本书的范围。在我们的示例中，我们使用 jQuery^(6)，因为在撰写本书时，它是最广泛使用的库之一。要使用 jQuery，我们需要加载包含该库的 JavaScript 文件。为此，我们修改 `template.jsp` 文件以引入 jQuery（见清单 7-4）。

***清单 7-4.** 修改后的 template.jsp 头部*

`<!DOCTYPE HTML>`
`<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core"%>`
`<%@ taglib prefix="spring" uri="http://www.springframework.org/tags" %>`
`<%@ taglib prefix="tiles" uri="http://tiles.apache.org/tags-tiles" %>`

`<html>`
`<head>`
`    <meta charset="utf-8">`
`    <c:set var="titleKey">`
`        <tiles:getAsString name="title" />`
`    </c:set>`
`    <title>Bookstore | <spring:message code="${titleKey}" text="您的书籍之家"/></title>`
`    <link rel="stylesheet" type="text/css" href="<c:url value="/resources/css/style.css"/>" >`
**`<script src="<c:url value="/resources/jquery/jquery-1.7.1.min.js"/>"></script>`**
`</head>`
`// 主体部分已省略`
`</html>`

这会将 jQuery JavaScript 库添加到我们所有的页面中；然而，仅此而已并不会产生太多效果。我们还需要向页面添加逻辑。在接下来的章节中，我们将为示例应用添加 AJAX 行为。我们将从一个简单的表单提交开始，并在此过程中探索 Spring MVC 提供的与 AJAX 协作的功能，以及它如何帮助我们构建 REST 应用。

_________

⁴ [`http://www.adaptivepath.com/ideas/ajax-new-approach-web-applications`](http://www.adaptivepath.com/ideas/ajax-new-approach-web-applications)

⁵ [`http://www.jjg.net`](http://www.jjg.net)

⁶ [`http://www.jquery.org`](http://www.jquery.org)

#### 为应用添加 AJAX

得益于 Spring MVC 的灵活性，为我们的应用添加 AJAX 行为并将其与 Spring MVC 良好集成变得相当容易。在本节中，我们将了解如何将表单提交改为基于 AJAX 的表单提交（包括使用和不使用 JSON 的情况）。然而，表单提交并非 AJAX 的唯一用途；它仅用于我们的示例应用，同样也可以实现自动补全字段、自动字段/表单验证等功能。

##### 返回 HTML 结果的 AJAX 表单提交

让我们看看图书搜索页面，并将其改造成一个更具动态性的网页。我们将从把普通表单提交改为 AJAX 表单提交开始。打开 `search.jsp` 文件，并在表单之后或页面底部添加如清单 7-5 所示的脚本。

***清单 7-5.** 带 AJAX 表单提交的图书搜索页面*

`<script>`
`$('#bookSearchForm').submit(function(evt){`
`    evt.preventDefault();`
`    formData = $('#bookSearchForm').serialize();`
`    $.ajax({`
`        url: $('#bookSearchForm').action,`
`        type: 'GET',`
`        data: formData`
`    });`
`});`
`</script>`

此脚本替代了实际的表单提交。它首先阻止实际提交的发生，然后构建一个 AJAX 请求，将数据传递给服务器。如果我们重新部署应用，导航到图书搜索页面并点击提交，看起来什么也没发生。至少我们在屏幕上看不到任何变化。如果我们调试应用，可以看到请求到达服务器并执行了搜索。那么，为什么结果没有被渲染出来呢？

在本节开头，我们提到 AJAX 是一系列技术的集合，其中之一就是使用 `XMLHttpRequest` 进行异步数据检索。这也是我们当前问题所在。我们向服务器发送了一个请求，但没有包含任何处理服务器响应的代码。清单 7-6 展示了修改后的脚本（见高亮部分），用于实际渲染返回的页面。

***清单 7-6.** 带成功处理器的图书搜索页面*

`<script>`
`$('#bookSearchForm').submit(function(evt){`
`    evt.preventDefault();`
`    formData = $('#bookSearchForm').serialize();`
`    $.ajax({`
`        url: $('#bookSearchForm').action,`
`        type: 'GET',`
`        data: formData,`
**`success: function(html) {`**
**`resultTable = $('#bookSearchResults', html);`**
**`$('#bookSearchResults').html(resultTable);`**
**`}`**
`    });`
`});`
`</script>`

我们为此脚本添加了成功处理器，它的作用是渲染从服务器接收到的结果。这个结果就是正常情况下会渲染的整个页面。我们选择包含结果的表格，并用检测到的表格替换屏幕上的当前表格。如果重新部署应用并执行搜索，页面将再次正常工作。



##### 使用 JSON 结果的 AJAX 表单提交

上一节展示了一个基础的 AJAX 表单提交，我们从中获取了 HTML 响应。虽然这种方法可行，但效率不高。我们将数据发送到服务器，然后像正常渲染一样获取完整的页面。更好的方式是，我们只需返回需要渲染的数据，并在客户端进行处理。为此，我们需要对 JavaScript 进行一些修改，同时也要扩展服务器端。我们需要一个额外的方法来向客户端返回 JSON 编码的数据（参见清单 7-7）。

***清单 7-7.** 包含 JSON 生成方法的 BookSearch 控制器*

`package com.apress.prospringmvc.bookstore.web.controller;`

`import org.springframework.http.MediaType;`
`import org.springframework.web.bind.annotation.ResponseBody;`

`// 其他导入已省略`

`@Controller`
`public class BookSearchController {`

`    // 其他方法已省略`

`    @RequestMapping(value = "/book/search",`
`                    method = ( RequestMethod.GET ),`
**`produces = MediaType.APPLICATION_JSON_VALUE )`**
`public` **`@ResponseBody`** `Collection<Book> listJSON(`
`        @ModelAttribute("bookSearchCriteria") BookSearchCriteria criteria) {`
`        return this.bookstoreService.findBooks(criteria);`
`    }`
`}`

该方法与同一控制器上的原始 `list` 方法功能相同；但有两个重要的区别，已高亮显示。首先，当传入的请求指定要接收 JSON 时（通过设置 Accept 头，如第 4 章所述），此方法会被调用。其次，我们使用 `org.springframework.web.bind.annotation.ResponseBody` 注解来指示 Spring MVC 将返回值作为响应的主体（参见第 5 章中的“支持的方法参数注解”一节）。返回值通过 `org.springframework.httpconverter.HttpMessageConverter` 进行转换。

![图片](img/square.jpg) **提示** 当在类路径上发现 Jackson Java JSON 处理器^(7) 时，Spring MVC 会自动注册 `org.springframework.http.converter.json.MappingJacksonHttpMessageConverter`。

除了控制器，我们还需要稍微修改 JavaScript，以指定我们希望从服务器接收 JSON。由于我们将接收 JSON，因此还需要使用 JSON 来替换结果表格的内容。在清单 7-8 中，你可以看到 `search.jsp` 文件的结果。

***清单 7-8.** 包含 JSON 成功处理器的图书搜索页面*

`<script>`
`$('#bookSearchForm').submit(function(evt){`
`    evt.preventDefault();`
`    formData = $('#bookSearchForm').serialize();`
`    $.ajax({`
`        url: $('#bookSearchForm').action,`
`        type: 'GET',`
`        dataType: 'json',`
`        data: formData,`
`        success: function(data) {`
`            var content = '';`
`            var books = data;`
`            var baseDetailUrl = <c:url value="/book/detail/"/>;`
`            var baseAddCartUrl = <c:url value="/cart/add/" />;`
`            for (var i = 0; i<books.length; i++) {`
`                content += '<tr>';`
`                content += '<td><a href="'`
`                           + baseDetailUrl + books[i].id+'">'`
`                           + books[i].title+'</a</td>';`
`                content += '<td>'+books[i].description+'</td>';`
`                content += '<td>'+books[i].price+'</td>';`
`                content += '<td><a href="'+ baseAddCartUrl +books[i].id+'">`
`                            <spring:message code="book.addtocart"/>`
`                            </a></td></tr>';`
`            }`
`            $('#bookSearchResults tbody').html(content);`
`        }`
`    });    `
`});`
`</script>`

_________

⁷ [`http://jackson.codehaus.org/`](http://jackson.codehaus.org/)

当应用程序重新部署并执行搜索时，我们的新方法将被调用，JSON 将返回给客户端。客户端将使用 JSON 对象创建一个新的表格主体，并在创建完成后替换当前的表格主体。

##### 发送和接收 JSON

既可以向服务器发送 JSON，也可以从服务器接收 JSON。发送 JSON 的优点是它非常紧凑，因此比 XML 发送和处理（客户端和服务器端）更快。一个缺点可能是，你需要一些手动编码来准备要发送到服务器的 JSON，尤其是在重用现有对象时（正如我们在示例中看到的）。

为了实现这一点，我们需要修改客户端 JavaScript，并对请求处理方法进行一些更改。控制器需要知道我们使用的不是普通的模型属性，而是希望为 `BookSearchCriteria` 使用 JSON。为此，我们将使用 `RequestBody` 注解来标注方法参数；它与 `ResponseBody` 类似，但用于传入请求。清单 7-9 高亮显示了需要对控制器进行的更改。

***清单 7-9.** 包含 RequestBody 注解的 BookSearchController*

`package com.apress.prospringmvc.bookstore.web.controller;`

`import org.springframework.web.bind.annotation.RequestBody;`

`//其他导入已省略`

`@Controller`
`public class BookSearchController {`

`// 其他方法已省略`

`@RequestMapping(value = "/book/search",`
`method = {` **`RequestMethod.POST`** `},`
`                produces = MediaType.APPLICATION_JSON_VALUE)`
`    public @ResponseBody Collection<Book> listJSON(`
**`@RequestBody`** `BookSearchCriteria criteria) {`
`        return this.bookstoreService.findBooks(criteria);`
`    }`
`}`

注意从 `GET` 请求到 `POST` 请求的更改；这是必需的，因为我们使用了 `RequestBody` 注解。顾名思义，该注解作用于请求的主体，但 `GET` 请求通常将数据编码在 URL 中，而不是主体中。

![图片](img/square.jpg) **注意** 当使用 `RequestBody` 和 `ResponseBody` 注解时，Spring MVC 不会使用类型转换系统将 `id` 转换为实际对象（如我们的类别）。这是因为在 REST 中，表示/构建资源所需的一切都应该是请求的一部分。

修改控制器后，我们还需要再次修改 JavaScript。我们需要将表单中的数据转换为可以发送到服务器的 JSON 字符串。清单 7-10 显示了需要更改的内容。

***清单 7-10.** 包含 JSON 表单提交的图书搜索页面*

`<script>`
`$('#bookSearchForm').submit(function(evt){`
`    evt.preventDefault();`
**`var title = $('#title').val();`**
**`var category = $('#category').val();`**
**`var json = { "title" : title, "category" : { "id" : category}};`**

`    $.ajax({`
`        url: $('#bookSearchForm').action,`
**`type: 'POST',`**
`        dataType: 'json',`
`        contentType: 'application/json',`
**`data: JSON.stringify(json),`**
`        success: function(books) {`
`            var content = '';`
`            var baseDetailUrl = '<c:url value="/book/detail/"/>';`
`            var baseAddCartUrl = '<c:url value="/cart/add/" />';`
`            for (var i = 0; i<books.length; i++) {`
`                content += '<tr>';`
`                content += '<td><a href="'+ baseDetailUrl + books[i].id+'">'`
`                           +books[i].title+'</a></td>';`
`                content += '<td>'+books[i].description+'</td>';`
`                content += '<td>'+books[i].price+'</td>';`
`                content += '<td><a href="'+ baseAddCartUrl +books[i].id+'">`
`                            <spring:message code="book.addtocart"/>`
`                            </a></td></tr>';`
`            }`
`            $('#bookSearchResults tbody').html(content);`
`        }`
`    });`
`});`
`</script>`



第一个高亮块是将表单数据转换为 JSON 对象。第二个高亮行是类型。我们需要将其改为 `POST`。之所以需要这样做，是因为内容就是请求体，而 `GET` 请求没有请求体，它会将所有内容编码到 URL 中。最后一个高亮行是将 JSON 对象转换为可以发送到服务器的 JSON 字符串。其余部分保持不变。

如果重新部署应用程序并执行搜索，搜索结果将再次显示给用户。

![图片](img/square.jpg) **注意** jQuery 拥有插件架构，并且市面上有一些插件可以简化表单到 JSON 的转换。我们选择不使用插件，因为那样会过于关注插件本身。

#### 结合 AJAX 和 REST

我们简要介绍了 REST，也涉及了 AJAX 的主题，但我们是分别介绍每个主题的。然而，将两者结合起来也非常容易。在 REST 部分，我们将账户更新表单改为了一个使用 `PUT` 请求提交的表单，但这只是使用 `POST` 进行的模拟。使用我们所用的 JavaScript 库，实际上可以创建一个真正的 `PUT` 请求，而不是一个被当作 `PUT` 请求使用的 `POST` 请求。

为了能够发起并处理 `PUT` 请求，我们需要做两件事：表单必须通过 AJAX 以 `PUT` 请求的方式提交，并且我们需要准备好服务器以处理 `PUT` 请求。`POST` 和 `PUT` 请求之间存在一些差异。一个主要区别是 `POST` 请求必须提供表单数据（这是规范要求的），但对于 `PUT` 请求则并非如此。Spring 提供了一个 `org.springframework.web.filter.HttpPutFormContentFilter`，可以帮助我们解决这个问题。

当检测到内容类型为 `application/x-www-form-urlencoded` 的 `PUT` 请求时，该过滤器会生效。它会解析传入请求的请求体（委托给 `org.springframework.http.converter.xml.XmlAwareFormHttpMessageConverter`），结果是一个参数映射，可以像普通表单参数一样使用。清单 7-11 展示了如何注册该过滤器。

***清单 7-11.** 注册 HttpPutFormContentFilter*

`package com.apress.prospringmvc.bookstore.web;`

`import org.springframework.web.filter.HttpPutFormContentFilter;`
`// 其他导入已省略。`

`public class BookstoreWebApplicationInitializer implements WebApplicationInitializer {`

`    private static final String DISPATCHER_SERVLET_NAME = "dispatcher";`

`    @Override`
`    public void onStartup(ServletContext servletContext)`
`    throws ServletException {`
`        registerListener(servletContext);`
`        registerDispatcherServlet(servletContext);`
**`registerHttPutFormContentFilter(servletContext);`**
`        registerHiddenHttpMethodFilter(servletContext);`

`    }`

`    private void registerHttPutFormContentFilter(`
`           ServletContext servletContext) {`
`        FilterRegistration.Dynamic registration;`
`        registration = servletContext.addFilter(`
`                           "httpPutFormContentFilter",`
`                            HttpPutFormContentFilter.class);`
`        registration.addMappingForServletNames(`
`            EnumSet.of(DispatcherType.REQUEST, DispatcherType.FORWARD),`
`            false,`
`            DISPATCHER_SERVLET_NAME);`
`    }`
`    // 其他方法已省略`
`}`

接下来，我们还需要在 `account.jsp` 文件中添加一些 JavaScript。这与我们最初添加到图书搜索页面的脚本非常相似，但有一个主要区别——我们现在使用 `PUT` 而不是 `GET`。请参见清单 7-12，了解在表单之后或页面末尾添加的 JavaScript。控制器方法（参见清单 7-3）保持不变，因为它对于控制器来说仍然是一个 `PUT` 请求。

***清单 7-12.** 账户页面 PUT Ajax 表单提交*

`<script>`
`$('#accountForm').submit(function(evt){`
`    evt.preventDefault();`
`    formData = $('#accountForm').serialize();`
`    $.ajax({`
`        url: $('#accountForm').action,`
`        type: 'PUT',`
`        data: formData`
`    });`
`});`
`</script>`

#### 渐进增强

我们应用 AJAX 功能的方式是一种称为*渐进增强*的技术。这意味着先构建一个功能完整的简单网页，然后使用 JavaScript 为页面添加动态和丰富的交互行为。

相反的方法也是可行的；这种技术称为*优雅降级*，这意味着我们从一个包含所有所需行为的页面开始，然后根据浏览器提供的功能，缩减所使用的丰富交互行为。

如今的趋势是使用渐进增强，因为通常来说，它更容易构建和维护。它还有一个优点，即我们可以根据连接到我们应用程序的设备的能力进行增强（iPhone 与运行 Internet Explorer 9 的 Windows 7 PC 具有不同的功能）。

### 处理文件上传

HTTP 文件上传或基于 HTML 表单的文件上传在 RFC 1867^(8) 中定义。在向表单添加类型为 `file` 的 HTML 输入字段并将编码设置为 `multipart/form-data` 后，浏览器可以将文本和/或二进制文件作为 `POST` 请求的一部分发送到服务器。

为了能够处理文件上传，我们首先需要注册一个 `org.springframework.web.multipart.MultipartResolver`。开箱即用，Spring 提供了两种处理文件上传的方式。第一种是 Servlet 3.0 规范中描述的多部分支持，第二种是使用 Apache 的 Commons FileUpload^(9) 项目提供的功能。

Spring 框架提供了两种实现：`org.springframework.web.multipart.support.StandardServletMultipartResolver` 和 `org.springframework.web.multipart.commons.CommonsMultipartResolver`。第一种实现只能在启用了多部分支持的 Servlet 3.0 环境中使用，第二种则使用 commons-fileupload。

为了实际处理文件上传，我们需要修改控制器。这些修改大多与所使用的文件上传技术无关。Spring 提供了几种处理文件上传的抽象。我们可以编写一个请求处理方法，该方法接受类型为 `org.springframework.web.multipart.MultipartFile`（或集合）的参数；我们也可以使用 `org.springframework.web.multipart.MultipartHttpServletRequest` 并自行检索文件。当我们在 Servlet 3.0 环境中并使用多部分解析支持时，我们还可以使用 `javax.servlet.http.Part` 接口来获取文件。

指示对文件上传执行操作的最终方法是用 `org.springframework.web.bind.annotation.RequestPart` 注解方法参数（参见第 4 章）。当将其放在上述任何其他类型上时，Spring 会使用类型转换系统来转换文件的内容。

我们将首先讨论两种不同策略的配置。之后，我们将研究如何在控制器内部处理文件上传。



#### 配置

启用文件上传的第一步是配置我们的环境。由于 Spring 提供了两种开箱即用的技术，每种技术都需要一组不同的配置项。我们将先介绍 Servlet 3.0 的多部分支持，然后再介绍 commons-fileupload。

选择哪一种取决于具体需求。如果应用程序严格运行在 Servlet 3.0 环境中，则应使用 Servlet 规范指定的标准多部分支持。如果应用程序还需要在旧版容器中运行，则应选择 commons-fileupload 支持。

_________

⁸ [`http://www.ietf.org/rfc/rfc1867.txt`](http://www.ietf.org/rfc/rfc1867.txt)

⁹ [`http://commons.apache.org/fileupload/`](http://commons.apache.org/fileupload/)

##### 配置 Servlet 3.0 文件上传

在 `org.springframework.web.servlet.DispatcherServlet` 上进行多部分解析的第一步是，根据我们的配置，在 `XML` 配置中添加一个 `multipart-config` 部分，或者在 `org.springframework.web.WebApplicationInitializer` 实现中包含一个 `javax.servlet.MultipartConfigElement`（参见清单 7-13）。

***清单 7-13.** 包含 MultipartConfigElement 的 BookstoreWebApplicationInitializer*

`package com.apress.prospringmvc.bookstore.web;`

`import javax.servlet.MultipartConfigElement;`

`//其他导入已省略`

`public class BookstoreWebApplicationInitializer implements WebApplicationInitializer {`

`    private static final String DISPATCHER_SERVLET_NAME = "dispatcher";`

**`private static final long MAX_FILE_UPLOAD_SIZE = 1024 * 1024 * 5;`**
**`private static final int FILE_SIZE_THRESHOLD = 1024 * 1024;`**
**`private static final long MAX_REQUEST_SIZE = -1L;`**

`    private void registerDispatcherServlet(ServletContext servletContext) {`
`        AnnotationConfigWebApplicationContext dispatcherContext;`
`        dispatcherContext = createContext(WebMvcContextConfiguration.class);`
`        ServletRegistration.Dynamic dispatcher;`
`        dispatcher = servletContext.addServlet(DISPATCHER_SERVLET_NAME,`
`                new DispatcherServlet(dispatcherContext));`
`        dispatcher.setLoadOnStartup(1);`
`        dispatcher.addMapping("/");`

**`dispatcher.setMultipartConfig(`**
**`new MultipartConfigElement(null,`**
**`MAX_FILE_UPLOAD_SIZE, MAX_REQUEST_SIZE, FILE_SIZE_THRESHOLD));`**
`    }`
`    // 其他方法已省略`
`}`

当 Servlet 配置为进行多部分解析后，我们需要将 `StandardServletMultipartResolver` 添加到我们的配置中（清单 7-14）。对于多部分解析器，无需再进行其他配置；这些配置已通过 `MultipartConfigElement` 完成。

***清单 7-14.** 包含 StandardServletMultipartResolver 的 WebMvcContextConfiguration*

`package com.apress.prospringmvc.bookstore.web.config;`

`import org.springframework.web.multipart.MultipartResolver;`
`import org.springframework.web.multipart.support.StandardServletMultipartResolver;`

`// 其他导入已省略`

`@Configuration`
`@EnableWebMvc`
`@ComponentScan(basePackages = { "com.apress.prospringmvc.bookstore.web" })`
`public class WebMvcContextConfiguration extends WebMvcConfigurerAdapter {`

`    // 其他方法已省略    `

`    @Bean`
`    public MultipartResolver multipartResolver() {`
`        return new StandardServletMultipartResolver();`
`    }`
`}`

##### 配置 Apache Commons File Uploading

在旧版 Servlet 容器中，或者为了更便携的文件上传方式，我们可以在 Spring MVC 中使用 commons file upload 支持。这只需要注册一个 `CommonsMultipartResolver` 即可启用文件上传（清单 7-15）。我们不再需要在 Servlet 上进行多部分解析，因此需要在多部分解析器上配置最大文件大小等参数。

***清单 7-15.** 包含 CommonsMultipartResolver 的 WebMvcContextConfiguration*

`package com.apress.prospringmvc.bookstore.web.config;`

`import org.springframework.web.multipart.MultipartResolver;`
`import org.springframework.web.multipart.commons.CommonsMultipartResolver;`

`// 其他导入已省略`

`@Configuration`
`@EnableWebMvc`
`@ComponentScan(basePackages = { "com.apress.prospringmvc.bookstore.web" })`
`public class WebMvcContextConfiguration extends WebMvcConfigurerAdapter {`

**`private static final long MAX_FILE_UPLOAD_SIZE = 1024 * 1024 * 5; //5 Mb 文件大小限制`**
**`private static final int FILE_SIZE_THRESHOLD = 1024 * 1024; // 超过 1Mb 后开始`![Image](img/U002.jpg)**
**`将文件写入磁盘`**

`    // 其他方法已省略`

**`    @Bean`** **`public MultipartResolver multipartResolver() {`**
**`CommonsMultipartResolver multipartResolver = new CommonsMultipartResolver();`**
**`multipartResolver.setMaxInMemorySize(FILE_SIZE_THRESHOLD);`**
**`multipartResolver.setMaxUploadSize(MAX_FILE_UPLOAD_SIZE);`**
**`return multipartResolver;`**
**`}`**
`}`

#### 文件上传的请求处理方法

除了配置上传功能，我们还需要一个包含可提交文件的表单页面。为此，我们需要创建一个编码设置为 `multipart/form-data` 的表单（清单 7-16）。即使我们更换不同的可用技术，这个表单也不会改变；唯一改变的是处理上传的方式。当添加一个类型为 file 的输入元素时，为其指定一个名称非常重要，尤其是在进行单文件上传时。这个名称也用于从请求中检索文件。

***清单 7-16.** 账户页面的上传订单表单*

`<form id="orderForm" action="<c:url value="/order/upload"/>" method="POST"`![Image](img/U002.jpg)
` enctype="multipart/form-data">`
`    <fieldset>`
`        <legend>上传订单</legend>`
`        <input type="file" placeholder="选择文件" id="order" name="order"/>`
`        <button id="upload"><spring:message code="button.upload"/></button>`
`    </fieldset>`
`</form>`

我们将此表单添加到 `account.jsp` 文件中，紧跟在已有的表单之后。当我们现在渲染账户页面时，它将看起来像图 7-1。

![Image](img/9781430241553_Fig07-01.jpg)

***图 7-1.** 带有文件上传功能的账户页面*

在接下来的章节中，我们将探讨在控制器中处理文件上传的不同方法。大多数方法在这两种不同的文件上传技术之间是可移植的；然而，最后一种方法仅在使用 Servlet 3.0 多部分支持时才可用。当文件上传时，每种不同的请求处理方法都会产生相同的输出；它将打印上传文件的名称和文件大小，如图 7-2 所示。

![Image](img/9781430241553_Fig07-02.jpg)

***图 7-2.** 示例文件上传输出*



##### 编写包含多部分文件的请求处理方法

在编写请求处理方法时，若需实现文件上传并利用 Spring 的多部分文件抽象，我们需要创建一个方法，为其添加注解，并确保其包含一个`MultipartFile`类型的参数。当有多个同名文件上传时，我们也可以接收一个文件集合而非单个元素。清单 7-17 展示了一个控制器，其方法可使用此技术处理文件上传。

***清单 7-17.** 使用 MultipartFile 的 UploadOrderController*

`package com.apress.prospringmvc.bookstore.web.controller;`

`// 其他导入已省略`
`import org.springframework.web.multipart.MultipartFile;`

`@Controller`
`public class UploadOrderController {`

`    private Logger logger;`
`    logger = LoggerFactory.`*`getLogger`*`(UploadOrderController.class);`

`    @RequestMapping(value = "/order/upload", method = RequestMethod.`*`POST`*`)`
`    public String handleUpload(MultipartFile order) {`
`        logFile(order.getOriginalFilename(), order.getSize());`
`        return "redirect:/customer/account";`
`    }`

`    private void logFile(String name, long size) {`
`        this.logger.info("Received order: {}, size {}", name, size);`
`    }`
`}`

##### 使用 MultipartHttpServletRequest 处理文件上传

除了直接访问文件，也可以使用`MultipartHttpServletRequest`来获取多部分文件（清单 7-18）。该接口定义了一些用于访问文件的方法。用于访问多部分文件的方法在其父接口`org.springframework.web.multipart.MultipartRequest`中定义。

***清单 7-18.** 使用 MultipartHttpServletRequest 的 UploadOrderController*

`package com.apress.prospringmvc.bookstore.web.controller;`

`// 其他导入已省略`
`import org.springframework.web.multipart.MultipartFile;`
`import org.springframework.web.multipart.MultipartHttpServletRequest;`

`@Controller`
`public class UploadOrderController {`

`    private Logger logger;`
`    logger = LoggerFactory.`*`getLogger`*`(UploadOrderController.class);`

`    @RequestMapping(value = "/order/upload", method = RequestMethod.`*`POST`*`)`
`    public String handleUpload(MultipartHttpServletRequest request) {`
`        Map<String, MultipartFile> files = request.getFileMap();`
`        for (MultipartFile file : files.values()) {`
`            logFile(file.getOriginalFilename(), file.getSize());`
`        }`
`        return "redirect:/customer/account";`
`    }`

`    private void logFile(String name, long size) {`
`        this.logger.info("Received order: {}, size {}", name, size);`
`    }`
`}`

##### 使用表单对象处理上传

我们也可以将上传作为表单对象（模型属性）的一部分，而不是直接处理上传。如果上传是包含更多字段的表单的一部分（例如我们的客户账户页面，其中包含图片），这会很方便。为此，我们需要创建一个可作为表单对象的类，其中包含一个`MultipartFile`类型的属性（清单 7-19）。

***清单 7-19.** UploadOrderForm*

`package com.apress.prospringmvc.bookstore.web;`

`import org.springframework.web.multipart.MultipartFile;`

`public class UploadOrderForm {`

`    private MultipartFile order;`

`    public MultipartFile getOrder() {`
`        return this.order;`
`    }`

`    public void setOrder(final MultipartFile order) {`
`        this.order = order;`
`    }`
`}`

我们需要修改控制器，将表单作为方法参数（参见清单 7-20）。

***清单 7-20.** 使用 UploadOrderForm 的 UploadOrderController*

`package com.apress.prospringmvc.bookstore.web.controller;`

`// 其他导入已省略`
`import com.apress.prospringmvc.bookstore.web.UploadOrderForm;`

`@Controller`
`public class UploadOrderController {`

`    private Logger logger;`
`    logger = LoggerFactory.`*`getLogger`*`(UploadOrderController.class);`

`    @RequestMapping(value = "/order/upload", method = RequestMethod.`*`POST`*`)`
`    public String handleUpload(UploadOrderForm form) {`
`        logFile(form.getOrder().getOriginalFilename(), form.getOrder().getSize());`
`        return "redirect:/customer/account";`
`    }`

`    private void logFile(String name, long size) {`
`        this.logger.info("Received order: {}, size {}", name, size);`
`    }`
`}`

##### 使用 Servlet 3.0 编写请求处理方法

在严格的 Servlet 3.0 环境中，我们可以使用标准接口`javax.servlet.http.Part`来访问上传的文件。我们只需创建一个以`Part`为参数的方法（参见清单 7-21）。我们需要创建一个方法，为其添加注解，并提供一个方法参数。此技术仅适用于 Servlet 3.0 环境，在这方面，其可移植性不如使用`MultipartFile`参数。

***清单 7-21.** 使用 Part 的 UploadOrderController*

`package com.apress.prospringmvc.bookstore.web.controller;`

`// 其他导入已省略`
`import javax.servlet.http.Part;`

`@Controller`
`public class UploadOrderController {`

`    private Logger logger;`
`    logger = LoggerFactory.`*`getLogger`*`(UploadOrderController.class);`

`    @RequestMapping(value = "/order/upload", method = RequestMethod.`*`POST`*`)`
`    public String handleUpload(final Part order) {`
`        logFile(order.getName(), order.getSize());`
`        return "redirect:/customer/account";`
`    }`

`    private void logFile(String name, long size) {`
`        this.logger.info("Received order: {}, size {}", name, size);`
`    }`
`}`

#### 异常处理

文件上传也可能失败。文件可能过大（超过配置的最大文件大小），或者磁盘空间已满。失败的原因有很多。如果可能，我们希望处理这些错误，并向用户显示一个友好的错误页面。我们可以使用异常处理（如第 6 章所述）来为我们处理异常并显示友好的错误页面。当发生异常时，多部分支持将抛出`org.springframework.web.multipart.MultipartException`，我们可以利用此异常来显示错误页面。

![图片](img/square.jpg) **注意** 在此特定情况下，`CommonsMultipartResolver`会抛出更具体的`MaxUploadSizeExceededException`，否则它将抛出`MultipartException`。

### 总结

本章涵盖了 Roy Thomas Fielding 所阐述的表征状态转移（REST），并且您了解了如何配置 Spring MVC 以支持 REST 所使用的不同方法。我们讨论了`HiddenHttpMethodFilter`的配置及其使用场景。

接下来，我们简要介绍了异步 JavaScript 和 XML（AJAX），以及如何在客户端使用它并让控制器响应这些请求。尽管 AJAX 最初是关于 XML 的，但如今它主要涉及 JSON；我们通过使用`RequestBody`和`ResponseBody`注解探索了 Spring MVC 提供的 JSON 特性。

将 AJAX 和 REST 结合起来也非常有用，它允许为我们的应用程序创建基于 REST 的 API。然而，为了便于处理`PUT`请求，我们需要配置`HttpPutFormContentFilter`，该过滤器使`PUT`请求体内容可作为请求参数使用。

本章的最后部分探讨了如何将文件上传到我们的应用程序。为此，我们研究了 Servlet 3.0 多部分支持和 commons-fileupload 支持所需的配置，然后探讨了编写能够处理文件上传的控制器的不同方法。

## 第 8 章



## 解析与实现视图

到目前为止，我们主要使用 Java 服务器页面（JSP）作为视图技术；然而，Spring MVC 提供了一种非常强大且灵活的机制来解析和实现视图。我们在第 4 章中简要介绍了视图解析机制；在本章中，我们将更深入地研究不同的 `ViewResolver` 实现，并了解如何创建和使用我们自己的实现。

我们还将了解 Spring MVC 开箱即用支持哪些视图技术，并创建一些自定义实现。不过，在深入探讨内部机制之前，让我们先回顾一下视图渲染流程和 API。

### 视图解析器与视图

在第 4 章中，我们讨论了调度器 servlet 的请求处理工作流程。解析和渲染视图是该流程的一部分。在图 8-1 中，你可以看到视图渲染流程（有关描述，请参见第 4 章中的“渲染视图”部分）。

![图片](img/9781430241553_Fig08-01.jpg)

***图 8-1.** 视图渲染流程*

控制器可以返回一个 `org.springframework.web.servlet.View` 实现或一个视图引用（视图名称）。在后一种情况下，会查询已配置的 `ViewResolver`，以将引用转换为具体的实现。当实现可用时，将指示其进行渲染；否则，会抛出 `javax.servlet.ServletException` 异常。

`ViewResolver`（见清单 8-1）只有一个方法，用于解析为 `View`。

***清单 8-1.** ViewResolver API*

`package org.springframework.web.servlet;`

`import java.util.Locale;`

`public interface ViewResolver {`

`    View resolveViewName(String viewName, Locale locale) throws Exception;`

`}`

当视图被选中后，调度器 servlet 将调用视图实例上的 `render` 方法（见清单 8-2）。`getContentType` 方法用于确定内容的类型。该值用于设置响应的内容类型；`org.springframework.web.servlet.view.ContentNegotiatingViewResolver` 也使用它来确定最佳匹配的视图（有关此视图解析器的更多信息，请参见后续章节）。

***清单 8-2.** View API*

`package org.springframework.web.servlet;`

`import java.util.Map;`

`import javax.servlet.http.HttpServletRequest;`
`import javax.servlet.http.HttpServletResponse;`

`public interface View {`

`    String getContentType();`

`    void render(Map<String, ?> model,`
`                HttpServletRequest request,`
`                HttpServletResponse response) throws Exception;`

`}`

### 视图解析器

在第 4 章中，我们展示了不同 `ViewResolver` 实现的层次结构。让我们更仔细地研究一下通用的可用实现、它们的工作原理以及如何配置它们。图 8-2 再次展示了不同的实现。特定于特定视图技术的实现将在“视图技术”部分中解释。

![图片](img/9781430241553_Fig08-02.jpg)

***图 8-2.** ViewResolver 层次结构*

#### BeanNameViewResolver

`org.springframework.web.servlet.view.BeanNameViewResolver` 是可用的最基本实现，也是默认配置的实现之一。它获取视图的名称，并在 `org.springframework.context.ApplicationContext` 中查找是否存在具有该名称的 `View`。如果存在，解析器将返回它；否则，返回 `null`。此视图解析器在小型应用程序中很有用；然而，它有一个很大的缺点，即每个视图都需要在应用程序上下文中进行配置。它有一个可配置的属性，即被调用的顺序（见表 8-1）。

![图片](img/9781430241553_tab08-01.jpg)

在清单 8-3 中，你可以看到我们需要如何配置以及配置什么，才能让我们的首页由此视图解析器提供和解析。我们显式配置了 `BeanNameViewResolver`；仅当调度器 servlet 的默认配置不足时才需要这样做。我们还需要添加一个 `View` 实例，由于我们使用的是支持 JSTL 的 JSP，因此我们返回一个 `org.springframework.web.servlet.view.JstlView`。

***清单 8-3.** BeanNameViewResolver 配置*

`package com.apress.prospringmvc.bookstore.web.config;`

`// 其他导入已省略`
`import org.springframework.web.servlet.view.BeanNameViewResolver;`
`import org.springframework.web.servlet.view.JstlView;`

`@Configuration`
`public class ViewConfiguration {`

`    @Bean`
`    public ViewResolver viewResolver() {`
`        BeanNameViewResolver viewResolver = new BeanNameViewResolver();`
`        viewResolver.setOrder(1);`
`        return viewResolver;`
`    }`

`    @Bean`
`    public View index() {`
`        JstlView view = new JstlView();`
`        view.setUrl("/WEB-INF/views/index.jsp");`
`        return view;`
`    }`
`}`

#### XmlViewResolver

`org.springframework.web.servlet.view.XmlViewResolver` 与 `BeanNameViewResolver` 非常相似，区别在于它需要一个额外的 Spring XML 配置文件来保存所有视图。默认情况下，它会加载名为 `/WEB-INF/views.xml` 的文件；但是，这可以进行配置。表 8-2 显示了可用的属性。

![图片](img/9781430241553_tab08-02.jpg)

清单 8-4 展示了 `XmlViewResolver` 的配置。清单 8-5 展示了用于配置首页视图的 `/WEB-INF/views.xml` 文件。

***清单 8-4.** XmlViewResolver 配置*

`package com.apress.prospringmvc.bookstore.web.config;`

`// 其他导入已省略`
`import org.springframework.web.servlet.view.XmlViewResolver;`

`@Configuration`
`public class ViewConfiguration {`

`    @Bean`
`    public ViewResolver viewResolver() {`
`        XmlViewResolver viewResolver = new XmlViewResolver();`
`        viewResolver.setOrder(1);`
`        return viewResolver;`
`    }`
`}`

***清单 8-5.** 示例 views.xml*

`<?xml version="1.0" encoding="UTF-8"?>`
`<beans`

`    xsi:schemaLocation="http://www.springframework.org/schema/beans`
`       http://www.springframework.org/schema/beans/spring-beans.xsd">`

`    <bean id="index" class="org.springframework.web.servlet.view.JstlView">`
`        <property name="url" value="/WEB-INF/views/index.jsp"/>`
`    </bean>`
`</beans>`



#### ResourceBundleViewResolver

该实现与 `XmlViewResolver` 非常相似，它使用属性文件而非 XML 文件来保存视图配置。`org.springframework.web.servlet.view.ResourceBundleViewResolver` 的优势在于，我们可以为不同语言提供不同视图。这是因为该解析器使用 Java 中的标准资源包机制来加载配置文件。表 8-3 列出了该实现的属性。

![Image](img/9781430241553_tab08-03.jpg)

清单 8-6 展示了 `ResourceBundleViewResolver` 的示例配置。在清单 8-7 中，我们可以看到正在使用的 `views.properties` 文件。

***清单 8-6.** ResourceBundleViewResolver 配置*

`package com.apress.prospringmvc.bookstore.web.config;`

`// 其他导入已省略`
`import org.springframework.web.servlet.view.ResourceBundleViewResolver;`

`@Configuration`
`public class ViewConfiguration {`

`    @Bean`
`    public ViewResolver viewResolver() {`
`        ResourceBunldeViewResolver viewResolver;`
`        viewResolver = new ResourceBundleViewResolver();`
`        viewResolver.setOrder(1);`
`        return viewResolver;`
`    }`
`}`

***清单 8-7.** 示例 views.properties*

**`index.(class)`**`=org.springframework.web.servlet.view.JstlView`
**`index.url=`**`/WEB-INF/views/index.jsp`

#### UrlBasedViewResolver

`org.springframework.web.servlet.view.UrlBasedViewResolver` 期望视图名称直接映射到 URL。它可以选择性地通过为视图名称添加前缀和/或后缀来修改 URL。通常，该类作为不同视图技术（如 JSP 和基于模板的视图技术，参见本章后面的“视图技术”一节）的基类。在表 8-4 中，我们可以看到此类视图解析器的属性。

![Image](img/9781430241553_tab08-04.jpg)

_________

¹ [`http://www.iana.org/assignments/media-types/index.html`](http://www.iana.org/assignments/media-types/index.html)

在清单 8-8 中，我们看到了此视图解析器的示例配置。我们需要指定视图类（必需），并且通常还需要添加前缀和/或后缀，以生成指向实际视图实现的 URL。其优势在于，我们可以为视图使用命名约定，而无需在配置中显式指定视图（例如，我们之前使用 `BeanNameViewResolver` 时就是这样做的）。

***清单 8-8.** UrlBasedViewResolver 配置*

`package com.apress.prospringmvc.bookstore.web.config;`

`// 其他导入已省略`
`import org.springframework.web.servlet.view.JstlView;`
`import org.springframework.web.servlet.view.UrlBasedViewResolver;`

`@Configuration`
`public class ViewConfiguration {`

`    @Bean`
`    public ViewResolver viewResolver() {`
`        UrlBasedViewResolver viewResolver = new UrlBasedViewResolver();`
`        viewResolver.setOrder(1);`
`        viewResolver.setPrefix("/WEB-INF/views/");`
`        viewResolver.setSuffix(".jsp");`
`        viewResolver.setViewClass(JstlView.class);`
`        return viewResolver;`
`    }`
`}`

#### InternalResourceViewResolver

作为 `UrlBasedViewResolver` 的扩展，这是一个便捷的子类，它预配置了视图类，并添加了一些额外支持，以便将 bean 包含在模型中，使其在视图中可用于渲染。清单 8-9 展示了 `org.springframework.web.servlet.view.InternalResourceViewResolver` 的示例配置。其结果与清单 8-8 基本相同。

***清单 8-9.** InternalResourceViewResolver 配置*

`package com.apress.prospringmvc.bookstore.web.config;`

`// 其他导入已省略`
`import org.springframework.web.servlet.view.InternalResourceViewResolver;`

`@Configuration`
`public class ViewConfiguration {`

`    @Bean`
`    public ViewResolver viewResolver() {`
`        InternalResourceViewResolver viewResolver;`
`        viewResolver = new InternalResourceViewResolver();`
`        viewResolver.setOrder(1);`
`        viewResolver.setPrefix("/WEB-INF/views/");`
`        viewResolver.setSuffix(".jsp");`
`        return viewResolver;`
`    }`
`}`

#### XsltViewResolver

`org.springframework.web.servlet.web.view.xslt.XsltViewResolver` 可用于将视图名称解析为 XSLT 样式表，从而将模型转换为可供用户查看的内容。要使用此视图解析器和视图，我们需要一个 XSLT 模板来将模型转换为视图。返回的视图（`org.springframework.web.servlet.view.xslt.XsltView` 的实例）将检测要渲染的模型对象。它支持以下类型：

> *   `javax.xml.transform.Source`
> *   `org.w3c.dom.Document`
> *   `org.w3c.dom.Node`
> *   `java.io.Reader`
> *   `java.io.InputStream`
> *   `org.springframework.core.io.Resource`

`org.springframework.web.servlet.view.xslt.XsltView` 将获取支持的类型，并使用 XSLT 样式表将其转换为我们想要的形式。尽管此机制可能很强大，但作者认为，它不应用于为 Web 应用程序创建视图层。通常，从控制器返回 XML（或 JSON）并在客户端使用 JavaScript 直接处理更为简单。

#### ContentNegotiatingViewResolver

`org.springframework.web.servlet.view.ContentNegotiatingViewResolver` 是一个非常特殊的视图解析器：它可以根据名称和内容类型解析视图。它的工作方式是首先确定请求的内容类型，通过检查文件扩展名、检查 Accept 头或检查请求参数来实现（这是可配置的；参见表 8-5）。确定内容类型后，解析器会咨询所有已配置的视图解析器，按名称收集候选视图。最后，它通过检查请求的内容类型是否受支持，来选择最佳匹配的视图。表 8-5 显示了此视图解析器的可配置属性。

![Image](img/9781430241553_tab08-05.jpg)

![Image](img/square.jpg) **注意** 当使用多个视图解析器时，`ContentNegotiatingViewResolver` 必须具有最高优先级才能正常运行。默认情况下已设置此优先级，但如果您更改了 `order`，请记住这一点。

#### 实现你自己的 ViewResolver

本节说明如何实现我们自己的视图解析器。我们将创建一个简单的实现，该实现从已配置视图的映射中解析视图名称。

实现你自己的视图相当简单；只需创建一个类，让它实现 `ViewResolver` 接口（参见清单 8-1），并提供必要的实现。清单 8-10 展示了我们的 `com.apress.prospringmvc.bookstore.web.view.SimpleConfigurableViewResolver`。

***清单 8-10.** SimpleConfigurableViewResolver*

`package com.apress.prospringmvc.bookstore.web.view;`

`// 其他导入已省略`
`import org.springframework.web.servlet.View;`
`import org.springframework.web.servlet.ViewResolver;`

`public class SimpleConfigurableViewResolver implements ViewResolver {`

`    private Map<String, ? extends View> views = new HashMap<String, View>();`

`    @Override`
`    public View resolveViewName(String viewName, Locale locale)`
`    throws Exception {`
`        return this.views.get(viewName);`
`    }`

`    public void setViews(Map<String, ? extends View> views) {`
`        this.views = views;`
`    }`
`}`

我们将在下一节中需要使用 PDF 和 Excel 视图时使用此实现。



### 视图技术

Spring MVC 支持多种不同的技术，如果某项技术未得到原生支持，你通常可以通过实现 `org.springframework.web.servlet.view.View` 接口或扩展某个已提供的 `View` 类来自行添加支持。本节将讨论几种视图技术，并介绍 Spring MVC 如何为它们提供支持。其中一些技术得到了广泛支持，而另一些则支持较少。图 8-3 展示了 `View` 类的层次结构，从中我们可以看到部分受支持的视图技术。对于某些技术，我们需要指定特定的 `ViewResolver` 才能工作，而其他技术则能与已配置的视图解析器协同工作。

![Image](img/9781430241553_Fig08-03.jpg)

***图 8-3.** 视图层次结构*

本节接下来的部分将简要介绍一些受支持的视图技术，展示其支持类以及如何配置 Spring 以使用这些特定技术。这里不会深入介绍所有受支持的视图技术；对于此处提到的大多数技术，都有专门的书籍可供参考。

![Image](img/square.jpg) **注意** 在本节的大多数示例中，`TilesViewResolver` 的 order 值被设置为 2。这是为了确保它在正确的时机执行，尤其是在使用 `ContentNegotiatingViewResolver` 时，后者应在 `TilesViewResolver` 之前执行（这是默认行为）。

#### Java Server Pages

到目前为止，我们一直在应用程序中使用 Java Server Pages。Spring 对其提供了出色的支持，包括其自身的标签库（参见第 5 章和第 6 章）。Spring 提供了支持类和集成类，通常与 `org.springframework.web.servlet.view.InternalResourceViewResolver` 配合使用，以启用 JSTL 支持，并与 Sun 提供的默认格式化和函数库集成。

#### JavaServer Faces

总体而言，Spring MVC 对 JavaServer Faces (JSF) 的支持非常有限。如果你想使用 JSF，建议将 Spring Web Flow 与 JSF 结合作为视图层；这种方法能提供丰富的开发环境。Spring 对 JSF 的支持主要体现在我们可以重用应用上下文中的 bean。我们通过定义一个自定义的 `javax.faces.el.VariableResolver`（JSF 1.1）或 `javax.el.ELResolver`（JSF 1.2+）来实现这一点，以便在 JSF 环境中访问 Spring bean。

将 Spring 集成到 JSF 环境中最简单的方法是配置 `org.springframework.web.jsf.DelegatingVariableResolver`，它会在所使用的 JSF 实现的默认解析器中进行查找。如果某个名称无法解析，该解析器会委托给根应用上下文进行查找。这使得我们可以在 JSF 中重用服务和其他通用组件。清单 8-11 展示了应用程序中 JSF 部分的配置。该配置位于 `faces-context.xml` 文件中。

***清单 8-11.** DelegatingVariableResolver 示例配置 (faces-context.xml)*

`<faces-config>`
`    <application>`
`        <variable-resolver>`
`        org.springframework.web.jsf.DelegatingVariableResolver`
`        </variable-resolver>`
`    </application>`
`</faces-config>`

此外，还有 `org.springframework.web.jsf.SpringBeanVariableResolver`，其工作方式类似；不过，它会首先查询根应用上下文，然后再查询 JSF 实现的默认解析器。当在 JSF 中使用来自应用上下文的限定作用域 bean 时，这非常有用。

对于 JSF 1.2+ 环境，我们还可以配置 `org.springframework.web.jsf.el.SpringBeanFacesELResolver`，它首先在根应用上下文中进行查找，然后在所使用的 JSF 实现的默认解析器中查找。清单 8-12 展示了此 bean 的示例配置。

***清单 8-12.** SpringBeanFacesELResolver 示例配置 (faces-context.xml)*

`<faces-config>`
`    <application>`
`        <el-resolver>`
`        org.springframework.web.jsf.el.SpringBeanFacesELResolver`
`        </el-resolver>`
`    </application>`
`</faces-config>`

如果我们有一些自定义的 JSF 组件，并且需要访问当前的应用上下文，可以使用 `org.springframework.web.jsf.FacesContextUtils`。这个工具类提供了两个便捷方法，用于获取应用上下文并从中检索 bean，进而可以在自定义组件中使用这些 bean。

#### Tiles

Apache Tiles^(2) 是一个强大的页面组合框架，它允许你使用不同的页面组件（即 *tiles*）来组合页面。这些页面组件可以被重用，并配置到不同的页面布局中。最初，它是作为 JSP 组合框架设计的；然而，它也可以用于组合基于 Velocity 和 Freemarker 的视图。

要开始使用 Tiles，我们必须为其配置并引导引擎。接下来，我们还需要配置视图解析器，使其能够返回基于 Tiles 的视图。最后，我们需要指定页面组合方式并添加不同的模板（tiles）。

我们需要将 `org.springframework.web.servlet.view.tiles2.TilesConfigurer` 添加到配置中。除此之外，我们还需要专门的 `org.springframework.web.servlet.view.tiles2.TilesViewResolver`。最后，我们还需要指定页面组合方式并添加模板。

##### 配置 Tiles

在开始使用 Tiles 支持之前，我们需要引导 Tiles。为此，Spring 提供了 `org.springframework.web.servlet.view.tiles2.TilesConfigurer` 类。接下来，我们还需要配置一个视图解析器来与 Tiles 协同工作，为此我们可以使用 `org.springframework.web.servlet.view.tiles2.TilesViewResolver`。清单 8-13 展示了 Tiles 最基本的配置。

***清单 8-13.** Tiles 的视图配置*

`package com.apress.prospringmvc.bookstore.web.config;`

`// 其他导入已省略`
`import org.springframework.web.servlet.view.tiles2.TilesConfigurer;`
`import org.springframework.web.servlet.view.tiles2.TilesViewResolver;`

`@Configuration`
`public class ViewConfiguration {`

`    @Bean`
`    public TilesConfigurer tilesConfigurer() {`
`        return new TilesConfigurer();`
`    }`

`    @Bean`
`    public TilesViewResolver tilesViewResolver() {`
`        TilesViewResolver tilesViewResolver = new TilesViewResolver();`
`        tilesViewResolver.setOrder(2);`
`        return tilesViewResolver;`
`    }`
`}`

_________

² [`http://tiles.apache.org/`](http://tiles.apache.org/)

默认情况下，`TilesConfigurer` 会从 WEB-INF 目录加载一个名为 `tiles.xml` 的文件；该文件包含页面定义。在查看定义文件之前，让我们先看看 表 8-6 中配置器的属性。

![Image](img/9781430241553_tab08-06.jpg)

`TilesViewResolver` 没有需要设置的额外属性；它具有与 `UrlBasedViewResolver` 相同的属性集。它是一个便捷的子类，会自动配置要返回的正确视图类型。对于 Tiles，我们需要创建 `org.springframework.web.servlet.view.tiles2.TilesView` 的实例。



##### 配置与创建模板

Tiles 需要一个或多个文件来定义页面；这些文件被称为*定义文件*。`TilesConfigurer` 默认加载的文件是 `/WEB-INF/tiles.xml`（参见清单 8-14）。

***清单 8-14.** Tiles 定义*

`<?xml version="1.0" encoding="UTF-8" ?>`
`<!DOCTYPE tiles-definitions PUBLIC`
`    "-//Apache Software Foundation//DTD Tiles Configuration 2.1//EN"`
`    "http://tiles.apache.org/dtds/tiles-config_2_1.dtd">`

`<tiles-definitions>`

`    <definition name="template" template="/WEB-INF/templates/template.jsp">`
`        <put-attribute name="header" value="/WEB-INF/templates/header.jsp"/>`
`        <put-attribute name="footer" value="/WEB-INF/templates/footer.jsp"/>`
`    </definition>`

`    <definition name="*" extends="template">`
`        <put-attribute name="title" value="{1}.title" />`
`        <put-attribute name="body" value="/WEB-INF/views/{1}.jsp" />`
`    </definition>`

`    <definition name="*/*" extends="template">`
`        <put-attribute name="title" value="{1}.{2}/title" />`
`        <put-attribute name="body" value="/WEB-INF/views/{1}/{2}.jsp" />`
`    </definition>`

`</tiles-definitions>`

我们创建了三个定义：名为 `template` 的定义是通用布局配置，其他定义扩展了此通用布局（并可覆盖预定义的属性）。需要时我们可以添加更多定义。为了让 Spring 选择正确的定义，我们的定义名称必须与视图名称匹配（或像示例中那样使用通配符 `*`）。我们的模板页面（`template.jsp`）由三个 tile（`header`、`footer` 和 `body`）组成，并且需要一个包含消息键的 `title` 属性，以便我们可以使用消息源（参见第 5 章关于国际化的讨论）来解析实际的标题。清单 8-15 展示了用于通用布局的 `template.jsp`。清单 8-16 展示了用作欢迎页面主体的 `index.jsp`。图 8-4 展示了最终生成的页面。[Au: OK? CE]

***清单 8-15.** 模板 JSP*

`<!DOCTYPE HTML>`
`<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core"%>`
`<%@ taglib prefix="spring" uri="http://www.springframework.org/tags" %>`
`<%@ taglib prefix="tiles" uri="http://tiles.apache.org/tags-tiles" %>`

`<html>`
`<head>`
`    <meta charset="utf-8">`
**`<c:set var="titleKey">`**
**`<tiles:getAsString name="title" />`**
**`</c:set>`**
`    <title>`
`Bookstore |` **`<spring:message code="${titleKey}" text="Your Home in Books"/>`**
`    </title>`
`    <link rel="stylesheet" type="text/css" href="<c:url value="/resources/css/style.css"/>" >`
`</head>`
`<body>`
`    <div id="wrap">`
`        <tiles:insertAttribute name="header"/>`
`       <div class="center_content">`
`        <div class="left_content">`
`            <h1><spring:message code="${titleKey}" text="${titleKey}" /></h1>`
`            <tiles:insertAttribute name="body" />`
`        </div><!--end of left content-->`

`        <div class="right_content">`

`             <div class="right_box">`

`                <div class="title"><span class="title_icon"><img src="<c:url value="/resources/images/bullet4.gif"/>" alt="" title="" /></span><spring:message code="main.title.randombooks"/></div>`
`                    <c:forEach items="${randomBooks}" var="book">`
`                        <div class="new_prod_box">`
`                            <c:url value="/book/${book.id}" var="bookUrl" />`
`                            <a href="${bookUrl}">${book.title}</a>`
`                            <div class="new_prod_img">`
`                            <c:url value="/book/${book.isbn}/image" var="bookImage"/>`
`                            <a href="${bookUrl}"><img src="${bookImage}" alt="${book.title}"`
`title="${book.title}" class="thumb" border="0" width="100px"/></a>`
`                            </div>`
`                        </div>`
`                    </c:forEach>`
`             </div>`

`        </div><!--end of right content-->`
`       <div class="clear"></div>`
`       </div><!--end of center content-->`

`       <tiles:insertAttribute name="footer" />`
`    </div>`
`</body>`
`</html>`

![Image](img/square.jpg) **提示** 高亮显示的代码基于 `tiles.xml` 中 `title` 属性的内容设置了一个变量。这样，我们可以在配置中指定一个键，并使用 Spring 的消息标签来解析实际的值。

***清单 8-16.** 用作内容的索引 JSP*

`<p>欢迎光临书店</p>` ![Image](img/9781430241553_Fig08-04.jpg)

***图 8-4.** 生成的欢迎页面*

#### Velocity 与 FreeMarker

Velocity^(3) 和 FreeMarker^(4) 都是用 Java 编写并用于 Java 的模板框架。你可以使用它们（以及其他工具）来创建 HTML 页面的模板。它们是基于文本的模板引擎，并且都广泛应用于各种模板解决方案的应用程序中。

_________

³ [`http://velocity.apache.org`](http://velocity.apache.org)

⁴ [`http://www.freemarker.org`](http://www.freemarker.org)

Velocity 和 FreeMarker 模板不像 JSP 那样被编译成 Java 代码。它们在运行时由其模板引擎解释执行；这很像我们之前讨论的 XSLT 处理过程。有人可能会认为这种解释执行而非编译的方式会导致应用程序性能下降，但事实往往并非如此。这两个引擎都对解释后的模板进行了大量缓存，这使得它们相当快速。

使用模板方法而非 JSP 的另一个优点是，在 JSP 中你可能会忍不住将 Java 代码放入其中。虽然可以将 Java 代码放入页面，但这并不是一种可取的方法。它通常会导致页面难以维护和修改。

缺点是我们需要为应用程序添加一些额外的配置。我们首先需要配置所选用的模板引擎，然后需要为该模板引擎配置视图解析。



##### 配置模板引擎

Spring 框架对 Velocity 和 FreeMarker 都提供了广泛支持，并且有一些辅助类可以更轻松地配置这些引擎。对于 Velocity，有 `org.springframework.web.servlet.view.velocity.VelocityConfigurer`（表 8-7）；对于 FreeMarker，有 `org.springframework.web.servlet.view.freemarker.FreeMarkerConfigurer`（表 8-8）。它们各自用于配置相应的模板引擎。

![Image](img/9781430241553_tab08-07.jpg)

![Image](img/9781430241553_tab08-08.jpg)

这些表中最重要的属性是设置模板加载位置的属性。对于 Velocity，它是 `resourceLoaderPath`；对于 FreeMarker，它是 `templateLoaderPath`。最佳实践是让 Web 客户端无法访问这些路径，可以通过将它们放在 `WEB-INF` 目录下来实现。

![Image](img/square.jpg) **注意** 还有 `org.springframework.beans.factory.FactoryBean` 可用于配置相应的模板引擎。这些工厂 Bean 用于引导引擎，使其作为非 Web 模板（如电子邮件）使用。

除了设置不同的引擎，我们还需要配置一个视图解析器来解析到正确的视图实现。Spring 提供了 `org.springframework.web.servlet.view.velocity.VelocityViewResolver` 或 `org.springframework.web.servlet.view.velocity.VelocityLayoutViewResolver`。对于 FreeMarker，有 `org.springframework.web.servlet.view.freemarker.FreemarkerViewResolver`。并不强制要求使用这些专门的视图解析器；一个经过充分配置的 `InternalResourceViewResolver` 也可以胜任。然而，使用这些专门的视图解析器能让我们的生活更轻松。清单 8-17 和 8-18 展示了 Velocity 和 FreeMarker 的配置示例。

***清单 8-17.** Velocity 配置*

`package com.apress.prospringmvc.bookstore.web.config;`

`// 其他导入已省略`
`import org.springframework.web.servlet.view.velocity.VelocityConfigurer;`
`import org.springframework.web.servlet.view.velocity.VelocityViewResolver;`

`@Configuration`
`public class ViewConfiguration {`

`    @Bean`
`    public VelocityConfigurer velocityConfigurer() {`
`        VelocityConfigurer velocityConfigurer = new VelocityConfigurer();`
`        velocityConfigurer.setResourceLoaderPath("WEB-INF/velocity");`
`        return velocityConfigurer;`
`    }`

`    @Bean`
`    public ViewResolver velocityViewResolver() {`
`        VelocityViewResolver viewResolver = new VelocityViewResolver();`
`        viewResolver.setSuffix(".vm");`
`        return viewResolver;`
`    }`
`}`

***清单 8-18.** FreeMarker 配置*

`package com.apress.prospringmvc.bookstore.web.config;`

`// 其他导入已省略`
`import org.springframework.web.servlet.view.freemarker.FreeMarkerConfigurer;`
`import org.springframework.web.servlet.view.freemarker.FreeMarkerViewResolver;`

`@Configuration`
`public class ViewConfiguration {`

`    @Bean`
`    public FreeMarkerConfigurer freeMarkerConfigurer() {`
`        FreeMarkerConfigurer freeMarkerConfigurer;`
`        freeMarkerConfigurer = new FreeMarkerConfigurer();`
`        freeMarkerConfigurer.setTemplateLoaderPath("WEB-INF/freemarker");`
`        return freeMarkerConfigurer;`
`    }`

`    @Bean`
`    public ViewResolver freeMarkerViewResolver() {`
`        FreeMarkerViewResolver viewResolver = new FreeMarkerViewResolver();`
`        viewResolver.setSuffix(".ftl");`
`        return viewResolver;`
`    }`
`}`

现在，当控制器返回 `index` 作为视图名称时，对于 Velocity，将从 `WEB-INF/velocity/index.vm` 加载模板；对于 FreeMarker，则变为 `WEB-INF/freemarker/index.ftl`。`resourceLoaderPath`/`templateLoaderPath` 会作为前缀添加到视图名称之前。这两个视图解析器还允许设置一个额外的前缀（继承自 `UrlBasedViewResolver`）。表 8-9 描述了视图解析器的不同属性。

![Image](img/9781430241553_tab08-09.jpg)

##### 模板语言

现在我们已经配置好了环境，还需要编写一个模板来向用户展示页面。这两种模板语言非常相似。清单 8-19 和 8-20 分别展示了用 Velocity 和 FreeMarker 编写的图书搜索页面。

***清单 8-19.** Velocity 模板*

`<!DOCTYPE HTML>`
`<html>`
`<head>`
`<title>图书搜索</title>`
`</head>`
`<body>`
`<h1>#springMessage("book.title")</h1>`

`<p>`

`    <form method="POST">`
`        <fieldset>`
`            <legend>#springMessage("book.searchcriteria")</legend>`
`            <table>`
`                <tr><td>#springMessage("book.title")</td><td>#springFormInput`![Image](img/U002.jpg)
`("searchCriteria.title" "")</td></tr>`
`                <tr><td>#springMessage("book.category")</td><td>#springFormSingleSelect`![Image](img/U002.jpg)
`("searchCriteria.category" $categories "")</td></tr>`
`            </table>`
`        </fieldset>`
`        <button id="search">#springMessage("button.search")</button>`
`    </form>`

`    #if ($bookList.empty eq false)`
`        <table>`
`            <tr><th>#springMessage("book.title")</th><th>#springMessage`![Image](img/U002.jpg)
`("book.description")</th><th>#springMessage("book.price")</th></tr>`
`            #foreach($book in $bookList)`
`                <tr><td>${book.title}</td><td>${book.description}</td><td>$`![Image](img/U002.jpg)
`{book.price}</td><td><a href="#springUrl("/cart/add/${book.id}")">#springMessage`![Image](img/U002.jpg)
`("book.addtocart")</a></tr>`
`            #end`
`        </table>`
`    #end`
`</p>`

`</body>`
`</html>`

***清单 8-20.** FreeMarker 模板*

`<#ftl>`
`<#import "/spring.ftl" as spring />`
`<!DOCTYPE HTML>`
`<html>`
`<head>`
`<title>图书搜索</title>`
`</head>`
`<body>`
`<h1><@spring.message code="book.title" /></h1>`

`<p>`

`    <form method="POST">`
`        <fieldset>`
`            <legend><@spring.message code="book.searchcriteria" /></legend>`
`            <table>`
`                <tr><td><@spring.message code="book.title" /></td><td><@spring.formInput`![Image](img/U002.jpg)
`"searchCriteria.title" /></td></tr>`
`                <tr><td><@spring.message code="book.category"`![Image](img/U002.jpg)
`/></td><td><@spring.formSingleSelect "searchCriteria.category", categories, "" /></td></tr>`
`            </table>`
`        </fieldset>`
`        <button id="search"><@spring.message code="book.search" /></button>`
`    </form>`

`    <#if bookList?has_content>`
`        <table>`
`            <tr><th><@spring.message code="book.title"/></th><th><@spring.message`![Image](img/U002.jpg)
`code="book.description"/></th><th><@spring.message code="book.price" /></th></tr>`
`            <#list bookList as book>`
`                <tr><td>${book.title}</td><td>${book.description}</td><td>$`![Image](img/U002.jpg)
`{book.price}</td><td><a href="<@spring.url "/cart/add/${book.id}"/>"><@spring.message`![Image](img/U002.jpg)
`code="book.addtocart"/></a></tr>`
`            </#list>`
`        </table>`
`    </#if>`
`</p>`

`</body>`
`</html>`

查看这些模板，我们可以看到它们与我们在前几章编写的 JSP 类似。Velocity（清单 8-19）和 FreeMarker（清单 8-20）模板也提供了可用的标签库（在清单中绑定到 Spring）。这两个库提供的支持与 JSP 的 Spring 表单标签库大致相同。表 8-10 概述了 Velocity 和 FreeMarker 的不同标签，或者更准确地说是宏库。

![Image](img/9781430241553_tab08-10.jpg)

上述任何宏的参数都具有一致的含义：



> *   **path：** 要绑定的字段名称（例如，“`searchCriteria.title`”）。
> *   **options：** 一个映射，包含输入字段中所有可供选择的值。该映射的键代表将从表单 POST 回来并绑定到命令对象的值。键对应的值用作向用户显示的标签。通常，此类映射由控制器作为参考数据提供。可以根据所需行为使用任何 Map 实现。
> *   **separator：** 当多个选项作为离散元素（单选按钮或复选框）可用时，用于在列表中分隔每个选项的字符序列（例如，`<br>`）。
> *   **attributes：** 要包含在 HTML 标签本身中的任意标签或文本的附加字符串。此字符串由宏原样输出。例如，在文本区域字段中，你可以提供属性，如 '`rows="5" cols="60"`'，或者传递样式信息，如 '`style="border:1px solid silver"`'。
> *   **classOrStyle：** 对于 showErrors 宏，这是包裹每个错误的 span 标签将使用的 CSS 类的名称。如果未提供任何信息（或值为空），则错误将被包裹在 `<b></b>` 标签中。

![Image](img/square.jpg) **提示** 表中标记为 (*) 的两个宏存在于 FreeMarker 中；然而，它们实际上并非必需，因为你可以使用普通的 `formInput` 宏，并将 `hidden` 或 `password` 指定为 `fieldType` 参数的值。

FreeMarker 和 Velocity 宏之间的区别在于，使用 FreeMarker 时，你可以像在 JSP 中一样指定要使用的库。这在 Velocity 宏中是不可能的，因为它们是全局的，并且包含在每个页面中（并且可访问）。对于 FreeMarker，我们需要使用 `import` 指令来指定库（参见 清单 8-20 顶部）。

##### 日期和数字

Velocity 有几个有用的工具可用于格式化日期和数字。Spring 也可以与这些工具集成，以便它们可以使用当前选定的区域设置。要启用此支持，我们需要设置 `dateToolAttribute` 和/或 `numberToolAttribute`。设置这些属性会将工具暴露给模型，以便它们可以在视图模板中使用。

#### PDF

Spring 使用 iText 库^(5) 来添加 PDF 支持。要启用此功能，我们需要编写自己的视图实现，为此我们需要扩展 `org.springframework.web.servlet.view.document.AbstractPdfView`。当我们扩展此类时，必须实现 `buildPdfDocument` 方法。

我们将创建一个 PDF，用于在我们的账户页面上概览其中一个订单。清单 8-21 显示了视图实现。

***清单 8-21.** 创建 PDF 的视图实现*

`package com.apress.prospringmvc.bookstore.web.view;`

`// 其他导入已省略`
`import org.springframework.web.servlet.view.document.AbstractPdfView;`

`import com.apress.prospringmvc.bookstore.domain.Order;`
`import com.apress.prospringmvc.bookstore.domain.OrderDetail;`
`import com.lowagie.text.Document;`
`import com.lowagie.text.Paragraph;`
`import com.lowagie.text.Table;`
`import com.lowagie.text.pdf.PdfWriter;`

`public class OrderPdfView extends AbstractPdfView {`

`    @Override`
`    protected void buildPdfDocument(Map<String, Object> model,`
`                                    Document document,`
`                                    PdfWriter writer,`
`                                    HttpServletRequest request,`
`                                    HttpServletResponse response)`
`    throws Exception {`

`        Order order = (Order) model.get("order");`

`        document.addTitle("订单 :" + order.getId());`
`        document.add(new Paragraph("订单日期: " + order.getOrderDate()));`
`        document.add(new Paragraph("交货日期: " + order.getDeliveryDate()));`

`        Table orderDetails = new Table(4);`
`        orderDetails.addCell("标题");`
`        orderDetails.addCell("价格");`
`        orderDetails.addCell("数量");`
`        orderDetails.addCell("总计");`

`        for (OrderDetail detail : order.getOrderDetails()) {`
`            orderDetails.addCell(detail.getBook().getTitle());`
`            orderDetails.addCell(detail.getBook().getPrice().toString());`
`            orderDetails.addCell(String.valueOf(detail.getQuantity()));`
`            orderDetails.addCell(detail.getPrice().toString());`
`        }`

`        document.add(orderDetails);`

`    }`

`}`

_________

⁵ [`http://itextpdf.com/`](http://itextpdf.com/)

接下来，让我们将 `org.springframework.web.servlet.view.ContentNegotiatingViewResolver` 添加到我们的视图配置中。我们这样做是为了让我们的订单页面能够以 HTML 或 PDF 格式呈现，并且我们不想更改 `com.apress.prospringmvc.bookstore.web.controller.OrderController`，因为它已经在做我们想要的事情，即将选定的订单添加到模型中。清单 8-22 显示了更改后的 `com.apress.prospringmvc.bookstore.web.config.ViewConfiguration`。这也是我们将开始使用自定义视图解析器的地方（参见关于实现你自己的视图解析器的讨论）。

***清单 8-22.** 使用 ContentNegotiatingViewResolver 的 ViewConfiguration*

`package com.apress.prospringmvc.bookstore.web.config;`

`// 其他导入已省略`
`import org.springframework.web.servlet.view.ContentNegotiatingViewResolver;`
`import org.springframework.web.servlet.view.document.AbstractPdfView;`

`import com.apress.prospringmvc.bookstore.web.view.OrderPdfView;`
`import com.apress.prospringmvc.bookstore.web.view`
`       .SimpleConfigurableViewResolver;`

`@Configuration`
`public class ViewConfiguration {`

`    @Bean`
`    public ContentNegotiatingViewResolver contentNegotiatingViewResolver() {`
`        ContentNegotiatingViewResolver viewResolver;`
`        viewResolver = new ContentNegotiatingViewResolver();`
`        List<ViewResolver> viewResolvers = new ArrayList<ViewResolver>();`
`        viewResolvers.add(pdfViewResolver());`
`        viewResolver.setViewResolvers(viewResolvers);`
`        return viewResolver;`
`    }`

`    @Bean`
`    public ViewResolver pdfViewResolver() {`
`        SimpleConfigurableViewResolver viewResolver;`
`        viewResolver = new SimpleConfigurableViewResolver();`
`        Map<String, AbstractPdfView> views;`
`        views = new HashMap<String, AbstractPdfView>();`
`        views.put("order", new OrderPdfView());`
`        viewResolver.setViews(views);`
`        return viewResolver;`
`    }`
`    // 其他方法已省略，参见清单 8-13`
`}`

更改后的配置包含我们自己的视图解析器，我们用它来解析 `com.apress.prospringmvc.bookstore.web.view.OrderPdfView`。我们这样做是因为我们还希望能够解析 Excel 文档的订单视图（参见“Excel”部分）。

完成这些更改后，我们需要重新部署我们的应用程序。如果我们登录并导航到我们的账户页面，现在可以点击 PDF 链接，实际获得一个 PDF 文件，而不是 HTML 版本。图 8-5 显示了点击 PDF 链接的结果。

![Image](img/9781430241553_Fig08-05.jpg)

***图 8-5.** 生成的 PDF*

尽管这种方法非常灵活，但其缺点是我们需要为每个想要的 PDF 编写 PDF 构建代码。如果我们有一些复杂的 PDF 或需要应用某种样式，这将非常繁琐且难以维护。在这种情况下，考虑使用像 JasperReports 这样的解决方案可能是值得的（参见“JasperReports”部分）。



#### Excel

Spring 有两种渲染 Excel 文档的方式。第一种是使用 JExcel 库^(6)，另一种是使用 Apache POI 库^(7)。这两种方法都需要我们实现一个视图（就像处理 PDF 一样）；为此，我们需要继承 `org.springframework.web.servlet.view.document.AbstractJExcelView` 或 `org.springframework.web.servlet.view.document.AbstractExcelView`。这两个类分别对应 JExcel 和 Apache POI。这两种实现都隐藏了设置过程，并允许加载和处理模板 xls 文件；我们需要添加视图特定的渲染逻辑。为此，我们需要实现 `buildExcelDocument` 方法。代码清单 8-23 展示了一个将订单导出为 Excel 文档的 JExcel 示例。

_________

⁶ [`http://jexcelapi.sourceforge.net/`](http://jexcelapi.sourceforge.net/)

⁷ [`http://poi.apache.org/`](http://poi.apache.org/)

***代码清单 8-23.** OrderExcelView*

`package com.apress.prospringmvc.bookstore.web.view;`

`// 其他导入已省略`
`import jxl.write.DateTime;`
`import jxl.write.Label;`
`import jxl.write.WritableSheet;`
`import jxl.write.WritableWorkbook;`

`import org.springframework.web.servlet.view.document.AbstractJExcelView;`

`import com.apress.prospringmvc.bookstore.domain.Order;`
`import com.apress.prospringmvc.bookstore.domain.OrderDetail;`

`public class OrderExcelView extends AbstractJExcelView {`

`    @Override`
`    protected void buildExcelDocument(Map<String, Object> model,`
`                                      WritableWorkbook workbook,`
`                                      HttpServletRequest request,`
`                                      HttpServletResponse response)`
`    throws Exception {`
`        Order order = (Order) model.get("order");`

`        WritableSheet sheet = workbook.createSheet("Order", 0);`
`        sheet.addCell(new Label(1, 1, "Order :" + order.getId()));`
`        sheet.addCell(new Label(1, 2, "Order Date"));`
`        sheet.addCell(new DateTime(2, 2, order.getOrderDate()));`
`        sheet.addCell(new Label(1, 2, "Delivery Date"));`
`        sheet.addCell(new DateTime(2, 2, order.getDeliveryDate()));`

`        sheet.addCell(new Label(1, 4, "Title"));`
`        sheet.addCell(new Label(2, 4, "Price"));`
`        sheet.addCell(new Label(3, 4, "#"));`
`        sheet.addCell(new Label(4, 4, "Total"));`

`        int row = 4;`
`        for (OrderDetail detail : order.getOrderDetails()) {`
`            row++;`
`            sheet.addCell(new Label(1, row, detail.getBook().getTitle()));`
`            sheet.addCell(new jxl.write.Number(2, row, detail.getBook().getPrice()`![Image](img/U002.jpg)
`.doubleValue()));`
`            sheet.addCell(new jxl.write.Number(3, row, detail.getQuantity()));`
`            sheet.addCell(new jxl.write.Number(2, row, detail.getPrice().doubleValue()));`
`        }`
`    }`

`}`

除了视图之外，我们还需要添加一个视图解析器。在我们的示例应用中，我们将像处理 PDF 视图一样，将其添加到 `ViewConfiguration` 类中。我们将添加另一个自定义实现的实例（参见代码清单 8-24），并让 `ContentNegotiatingViewResolver` 决定如何处理。

***代码清单 8-24.** 包含 OrderExcelView 的 ViewConfiguration*

`package com.apress.prospringmvc.bookstore.web.config;`

`// 其他导入已省略，参见代码清单 8-22`
`import org.springframework.web.servlet.view.document.AbstractJExcelView;`
`import org.springframework.web.servlet.view.document.AbstractPdfView;`

`import com.apress.prospringmvc.bookstore.web.view.OrderExcelView;`

`@Configuration`
`public class ViewConfiguration {`

`    @Bean`
`    public ContentNegotiatingViewResolver contentNegotiatingViewResolver() {`
`        ContentNegotiatingViewResolver viewResolver;`
`        viewResolver = new ContentNegotiatingViewResolver();`
`        List<ViewResolver> viewResolvers = new ArrayList<ViewResolver>();`
`        viewResolvers.add(pdfViewResolver());`
**`viewResolvers.add(xlsViewResolver());`**
`        viewResolver.setViewResolvers(viewResolvers);`
`        return viewResolver;`
`    }`

`    @Bean`
`    public ViewResolver xlsViewResolver() {`
`        SimpleConfigurableViewResolver viewResolver;`
`        viewResolver = new SimpleConfigurableViewResolver();`
`        Map<String, AbstractJExcelView> views;`
`        views = new HashMap<String, AbstractJExcelView>();`
`        views.put("order", new OrderExcelView());`
`        viewResolver.setViews(views);`
`        return viewResolver;`
`    }`
`    // 其他方法已省略，参见代码清单 8-22`
`}`

但是，等等，我们的应用会不会因为多个视图实现都解析到 `order` 视图名称而出错？特殊的视图解析器 `ContentNegotiatingViewResolver` 将在这里帮助我们。它会确定哪个已解析的视图与请求的内容类型最匹配。因此，无需更改控制器，只需添加一些配置（和视图实现），我们就可以区分提供的是哪个视图。

要测试，请点击 xls 链接，系统将下载一个 Excel 文档供您查看。



#### XML 与 JSON

Spring MVC 还提供了另一种向客户端提供 XML 或 JSON 的方式。我们可以利用 `ContentNegotiatingViewResolver` 的优势。Spring 提供了两个特殊的视图实现，分别用于将对象转换为 XML 或 JSON，即 `org.springframework.web.servlet.view.xml.MarshallingView` 和 `org.springframework.web.servlet.view.json.MappingJacksonJsonView`。基于 XML 的视图使用 Spring 的 XML 支持将模型编组为 XML。JSON 视图则使用 Jackson 库。我们可以非常轻松地配置视图解析器，使其也能向客户端暴露 XML 和/或 JSON。我们只需为 XML 和 JSON 添加默认视图（也可以像处理 PDF 和 Excel 文档那样，添加额外的视图解析器）。清单 8-25 展示了修改后的配置（请参见高亮部分）。

***清单 8-25.** 用于 XML 和 JSON 的 ViewConfiguration*

`package com.apress.prospringmvc.bookstore.web.config;`

`// 其他导入已省略`

`import org.springframework.oxm.Marshaller;`
`import org.springframework.oxm.xstream.XStreamMarshaller;`
`import org.springframework.web.servlet.view.json.MappingJacksonJsonView;`
`import org.springframework.web.servlet.view.xml.MarshallingView;`

`import com.apress.prospringmvc.bookstore.web.view.OrderExcelView;`
`import com.apress.prospringmvc.bookstore.web.view.OrderPdfView;`

`@Configuration`
`public class ViewConfiguration {`

`    @Bean`
`    public ContentNegotiatingViewResolver contentNegotiatingViewResolver() {`
`        ContentNegotiatingViewResolver viewResolver;`
`        viewResolver = new ContentNegotiatingViewResolver();`
`        List<ViewResolver> viewResolvers = new ArrayList<ViewResolver>();`
`        viewResolvers.add(pdfViewResolver());`
`        viewResolvers.add(xlsViewResolver());`
`        viewResolver.setViewResolvers(viewResolvers);`
**`List<View> defaultViews = new ArrayList<View>();`**
**`defaultViews.add(jsonOrderView());`**
**`defaultViews.add(xmlOrderView());`**
**`viewResolver.setDefaultViews(defaultViews);`**
`        return viewResolver;`
`    }`

**`@Bean`**
**`public MappingJacksonJsonView jsonOrderView() {`**
**`MappingJacksonJsonView jsonView = new MappingJacksonJsonView();`**
**`jsonView.setModelKey("order");`**
**`return jsonView;`**
**`}`**

**`@Bean`**
**`public MarshallingView xmlOrderView() {`**
**`MarshallingView xmlView = new MarshallingView(marshaller());`**
**`xmlView.setModelKey("order");`**
**`return xmlView;`**
**`}`**

**`@Bean`**
**`public Marshaller marshaller() {`**
**`return new XStreamMarshaller();`**
**`}`**

`    // 其他方法已省略，请参见清单 8-22 和 8-24`
`}`

要使 XML 正常工作，我们还需要配置一个 `org.springframework.oxm.Marshaller` 实现。这里我们选择使用 XStream^(8) 库，因为它快速且易于使用。若要使用其他解决方案，只需配置相应的编组器即可。有关编组和 XML 的更多信息，请参阅 Spring 参考指南。

![Image](img/square.jpg) **注意** 当将这些类型的视图与 ORM 实现（如我们的示例中）一起使用时，可能会遇到诸如延迟加载或由于集合被初始化而加载半个数据库的问题。

现在，如果我们将浏览器中的 URL 改为以 .json 或 .xml 结尾，我们将获得订单的 JSON 或 XML 表示形式（图 8-6 是 JSON 示例）。现在，我们有五种不同的方式来查看订单（HTML、PDF、Excel、JSON 和 XML），而无需修改控制器，只需更改配置即可。

![Image](img/9781430241553_Fig08-06.jpg)

***图 8-6.** 订单的 JSON 表示形式*

_________

⁸ [`http://xstream.codehaus.org`](http://xstream.codehaus.org)

#### JasperReports

Spring MVC 支持名为 JasperReports 的报表引擎。^(9) 该报表引擎使用 XML 格式的报表设计，并可以基于该 XML 输出不同格式。有一个名为 iReport Designer 的强大报表设计器，可用于创建报表设计。也可以使用 XML 或文本编辑器来创建设计，但不推荐这样做，因为 XML 非常冗长。

Spring 通过一个视图解析器和六种不同的视图实现来支持 JasperReports。这个视图解析器不出意外地是 `org.springframework.web.servlet.view.jasperreports.JasperReportsViewResolver`。视图实现可以在同一个包中找到。它们是：

> *   `JasperReportsCsvView`
> *   `JasperReportsHtmlView`
> *   `JasperReportsPdfView`
> *   `JasperReportsXlsView`
> *   `ConfigurableJasperReportsView`
> *   `JasperReportsMultiFormatView`

前四种实现生成预定义的格式，分别是 CSV、HTML、PDF 或 Excel。对于第五种，你可以配置要导出的格式；它允许设置用于生成报表的特定 `net.sf.jasperreports.engine.JRExporter` 实现。最后一种视图实现充当了前四种视图的包装器，或者可以配置特定的实现。它将一种格式映射到特定的视图实现。表 8-11 列出了默认映射。

![Image](img/9781430241553_tab08-11.jpg)

要使用其中一种视图，我们需要添加视图并配置报表设计的 URL。这可以是一个 XML 文件（.jrxml 文件）或编译后的形式（.jasper 文件）。无论哪种方式，出于性能原因，内部都需要报表的编译版本。对于生产环境，预先编译报表可能是明智之举。清单 8-26 展示了基于 JasperReports 的订单 PDF 视图的示例配置。

_________

⁹ [`http://jasperforge.org/projects/jasperreports`](http://jasperforge.org/projects/jasperreports)

***清单 8-26.** JasperReports 视图配置*

`package com.apress.prospringmvc.bookstore.web.config;`

`// 其他导入已省略`

`import org.springframework.web.servlet.view.jasperreports`
`       .JasperReportsPdfView;`

`@Configuration`
`public class ViewConfiguration {`

`    @Bean`
`    public View jasperReportsView() {`
`        JasperReportsPdfView orderPdfView = new JasperReportsPdfView();`
`        orderPdfView.setUrl("/WEB-INF/jasper/order.jrxml");`
`        return orderPdfView;`
`    }`
`  // 其他方法已省略，请参见清单 8-18`
`}`

特殊的 `org.springframework.web.servlet.view.jasperreports.JasperReportsMultiFormatView` 需要将要渲染的格式放入模型中。默认情况下，模型中应该有一个名为 `format` 的键，用于告知要渲染哪种格式。使用 `ContentNegotiatingViewResolver` 来解析正确的视图实现更加灵活且侵入性更小。这允许你在不修改控制器的情况下配置视图。而多格式视图需要修改我们的控制器，并且我们需要在其中添加逻辑来确定要渲染的内容。



##### 填写报告

与之前的 PDF 和 Excel 示例类似，如果能向用户展示一些内容，那将非常不错。与其他视图实现一样，Jasper Reports 的视图实现也使用模型中的数据进行渲染。不过，Jasper 的做法与其他视图技术略有不同。

如果我们的模型包含单个属性，并且该属性是 `net.sf.jasperreports.engine.JRDataSource`、`net.sf.jasperreports.engine.JRDataSourceProvider` 或 `javax.sql.DataSource`，那么它将直接使用（如果是 `DataSource`，则需要在报告中包含查询语句）。如果该属性是 `Collection` 或数组，它将被转换为可用的 `JRDataSource` 实现。

如果我们的模型包含多个属性，则需要指定要使用哪个属性。我们通过设置视图的 `reportDataKey` 属性来实现这一点。如果不这样做，可能会导致意外结果，因为系统会使用检测到的第一个 `JRDataSource`、`Collection` 或数组来填充报告。

报告需要 `net.sf.jasperreports.engine.JRDataSource` 来检索数据并渲染报告。JasperReports 为不同的数据源提供了多种实现，范围从数据库到 Excel 文档。

### 总结

本章涵盖了 Spring MVC 的视图部分。我们通过介绍几个通用的 `ViewResolver` 实现来探讨视图解析。我们还介绍了 Spring MVC 支持的几种视图技术，并解释了如何配置 Spring 来使用它们。我们从 JSP 开始，简要介绍了 JSF 以及如何将 Spring 集成到 JSF 应用程序中。接着，我们研究了几种模板解决方案，具体来说，涵盖了 Tiles、Velocity 和 FreeMarker。

在基于 Web 的视图之后，我们研究了不同的视图技术，例如如何在不更改控制器的情况下创建 PDF 和 Excel，只需添加一个 `ContentNegotiatingViewResolver` 和相应的 `View` 实现即可。

在上一章中，我们介绍了 JSON，而在本章中，我们探讨了另一种将模型（部分）暴露为 JSON 或 XML 的方法。最后，我们研究了 JasperReports，用于基于模板语言创建报告。

本章需要掌握的一个重要内容是控制器逻辑与视图逻辑的分离（这一点通过我们订单的不同表现形式得到了证明）。这展示了应用关注点分离的强大之处以及它带来的灵活性。

我们可能永远不会在单个应用程序中使用所有这些技术。很可能仅限于两到三种不同的技术（用于我们的页面，可能还会创建 PDF 或 Excel 文件）。但拥有更改或简单地为应用程序添加新视图层的灵活性，这非常好。

## 第 9 章

## 测试 Spring MVC 应用程序

在大多数项目中，测试都是一个难点。我们都知道测试很重要，但我们也知道测试需要时间，并且编写好的测试很困难。测试还需要代码库中有足够的基础设施。

测试也不是很吸引人。它不涉及制作具有炫酷效果的闪亮 UI，也不需要你做出出色的设计（不要误会，测试需要*良好*的设计，但它大多是重复性的，不像生产代码那样引人入胜）。也许最大的挑战是，在编写测试时，你的项目似乎没有进展。感觉像是在浪费时间，而本可以用来构建功能。因此，测试有时会被“遗忘”，或者承诺稍后再编写测试。

尽管有大量的书籍，甚至互联网上有更多的参考资料，但我们不打算讨论方法论之类的东西。在本章中，我们首先要提醒你测试为何如此重要，并且我们还将尝试定义本章中将如何开展测试。

在代码示例中，我们将向你介绍一种使用 Spring 测试支持进行测试的方法，这种方法既吸引人又易于处理。我们将展示测试应用程序的不同方法以及如何构建这些测试。

本章的标题是“测试 Spring MVC 应用程序”，这是广义上的意思，因为我们将在应用程序的每个层面讨论测试。我们将从简单的部分开始，即如何使用 Spring 的测试支持测试我们的后端模块：存储库和服务。我们还将向你展示如何以及何时使用模拟对象。之后，我们将讨论如何测试 MVC 控制器，最后对我们的页面执行完整的浏览器测试。

### 测试介绍

在深入代码之前，我们想确保你了解编写测试为何如此有益。我们还将探讨一些重要的事情，你可以通过这些事让测试在你的项目中变得（并保持）有吸引力。当编写测试成为一种痛苦时，可以肯定测试质量很差，甚至更糟，根本没有编写测试。为了结束我们的介绍，我们将研究不同类型的测试。我们将解释它们之间的区别，并更深入地研究本章将要涵盖的测试类型。那么，让我们先来看看为什么你应该投入时间编写测试。



#### 为什么我要费心编写测试？

测试应该成为你日常开发流程中自然而然的反应，就像编写功能、交付构建、修复错误、跟踪问题等一样。

我们知道测试需要时间。编写好的测试用例有时可能比编写实现实际业务逻辑的代码更复杂。因此，你应该尽一切努力让你项目中的测试过程尽可能顺畅和简单。这是在项目中成功进行测试的首要条件。如果编写测试太麻烦，或者测试从未自动运行，人们就会找借口不去编写它们。测试应该被视为项目中的“一等公民”。

我们都有这种感受：修改了一些代码后，你如何确保代码真的能工作？有些人可能会争辩说，根据你的经验、背景和技能，这一事实本身并不需要测试来证明。也许是这样，但你修改的影响呢？你的代码可能运行良好，但你知道它不会破坏任何依赖它的其他代码吗？另外，你有没有考虑过在你之后需要修改你代码的人？他们需要一些保证，确保他们所做的修改是没问题的，并且没有破坏其他任何东西。

没有测试，你的实现可能会变成图 9-1 所示的样子，而你甚至可能还没意识到：

![图片](img/9781430241553_Fig09-01.jpg)

***图 9-1.** 没有测试时的实现变形*

这张图代表了需要实现的需求和实际的实现。在 Jaroslav Tulach 的著作 *《实用 API 设计》*^(1) 中，这被称为“变形虫效应”——诚然，是在不同的上下文中，但这个想法在这里也适用。如果我们都是完美的开发者，编写 100% 正确的代码，那么实现就会和需求一样是规整的矩形。不幸的是，事实并非如此，实现会发生变形。你也可以称之为“有缺陷的”，或者就所要求的内容而言是“不符合的”。

![图片](img/square.jpg) **注意** 这里我们将需求视为绝对真理，因为它们是用直线绘制成矩形形状。在实践中，这也并非如此。需求本身也会包含超出实际要求的误差范围。但从开发者的角度来看，需求就是需求，代码应该 100% 地符合它们（如果不是，应该首先修改需求）。

那么，如果有一个集成在项目中的“机器人超级开发者”，每次你的实现将要变形时它都会告诉你，这样你就可以阻止变形，那岂不是太棒了？

变形可能以不同的方式潜入你的代码。你可能仅仅因为重构了应用程序中的某些内容，或者更改了某些功能就导致了变形。即使执行了最小的更改，你也可以免费召唤这个机器人开发者，让它检查所有依赖项并分析你工作的所有影响。几秒钟后，机器人开发者可能会说：“你做得很好，我检查了所有代码，一切似乎都正常！”或者机器人可能会说：“注意，你在那个类的第 x 行破坏了一条业务规则！”在这两种情况下，你都会非常高兴。在第一种情况下，你会体验到“舒适温暖的感觉”。在第二种情况下，你会感谢机器人指出了潜在的 bug。在这种情况下，你会继续修改你的代码（或你的测试），使其尽可能保持矩形形状。

_________

¹ Jaroslav Tulach, *《实用 API 设计：Java 框架架构师自白》* (Apress 2008)

这个机器人开发者是真实存在的，它就隐藏在你的测试中。但你必须一点一点地构建它。每一次你能编写一个测试用例，你都应该去写。每一次你编写一个测试用例，你都应该充分利用它，并且像对待你正在测试的代码一样重视它。测试用例是投资，它们守护着你项目的未来。

当你拥有一个合适的测试套件（一组测试）后，你就可以确保每次修改后，你的代码仍然符合这个套件。这个套件是根据需求形成的，这意味着每个测试都测试了需求的一部分。如果你把所有测试加起来，理想情况下你就测试了项目的所有需求（参见图 9-2）。

![图片](img/9781430241553_Fig09-02.jpg)

***图 9-2.** 添加测试套件以保持代码与需求一致*

在这张图中，我们添加了一个测试套件，试图将我们的测试映射到我们的需求。正如你所看到的，测试套件本身也不是一个完美的矩形，但它比严重变形的实现要接近得多。测试套件不可能是完美的，因为它永远无法真正测试所有东西，并且它本身也可能包含错误。然而，将这个套件应用于这个变形的实现，会产生许多失败，你可以修复这些失败，直到你的实现越来越接近你的需求。最重要的是，在修复代码使其紧密符合需求之后，你的测试套件将充当代码的监控器。每当出现新的变形时，你都会收到警报，以便采取适当的措施。

至于你是在实际生产代码之前还是之后编写测试，这取决于你。这个讨论超出了本章的范围。但可以说，如果你在编写代码之后编写测试，最好将时间间隔保持得尽可能短。在图 9-2 所示的模型中，我们正在测试的实现应该被视为你项目中非常小的一部分。它可能代表一个单独的类，甚至是一个单独的方法，你应该在编写代码后尽快为其编写单元测试。



#### 在项目中推广测试

如果编写测试过于困难，或者为了测试某个特定模块而需要对代码进行大量重构，那么可以肯定的是，没有人会去编写测试。许多开发者将测试视为一项必须完成的额外任务。在本节中，我们想提供一些在项目中推广测试的技巧，让编写测试变得更容易、更有吸引力。项目本身应尽可能为编写测试提供支持。本节内容当然无法面面俱到，但我们希望它能让你了解一些更重要的方面。

> **目标：** 在项目中，你可以做的第一件事就是设定一个测试目标；例如，以测试覆盖生产代码的高百分比为目标，就是一个值得努力的好目标。EclEmma^(2) 是一个非常优秀的插件，可以帮你实现这一点。我们将在下一节编写第一个测试后展示它的输出结果。Gradle 和 Maven 也有相应的插件，可以自动运行 Emma^(3)。你甚至可以设定构建成功所需的最低覆盖率目标。代码覆盖率可以在心理上发挥作用，让开发者有一个目标：尽可能提高被覆盖代码的百分比。这是一种“游戏化”的形式；它通过激励我们争取尽可能高的覆盖率，来帮助我们尽最大努力编写测试。但是，请注意，数量并不比质量更重要，因此不应忽视质量方面。编写一个达到 100% 覆盖率的测试并不难，但这并不意味着代码本身得到了良好的测试。代码测试覆盖率只是一个用于感受测试质量的最低限度指标。
> 
> **基础设施：** 我们指的是项目中的框架和结构。没有良好的基础设施，测试会变得很痛苦。你应该开始搭建一个良好的平台来编写测试。如前所述，编写测试是一项投资，这项投资应该得到很好的保护。测试就像你汽车的全面保险。它需要花钱，而且不会给你带来任何直接好处。但如果你的车出了任何问题，它就会支付费用，而且，当这种情况发生时，你会庆幸自己买了这份保险！测试也是如此（只不过事故每天都会发生，而且一旦你构建了测试，使用它们就是免费的）。一旦你开始重构或更改应用程序，它们就会开始产生回报。
> 
> **测试的重要性：** 测试有时比它们所测试的代码更重要，因为它们是代码正常工作的证明。没有测试，代码就毫无意义，因为其行为是一种无法验证的状态。即使你非常确定代码能工作，你可能也不知道其影响。其他人依赖于你的代码，可能会突然注意到行为的变化，从而破坏你的项目。如果每个组件都经过了充分的测试，那么在运行测试后，问题就会立即显现出来。
> 
> **理解：** 你应该理解不同级别的测试，以及何时在何种情况下应用哪种类型的测试。你可以通过前端测试来测试整个应用程序，但这将是一个糟糕的主意。你还应该知道测试应该在何处停止。你不需要测试 Spring、Hibernate 或你正在使用的任何库。它们有自己的测试集。

_________

² [`http://www.eclemma.org/`](http://www.eclemma.org/)

³ [`http://docs.codehaus.org/display/GRADLE/Plugins#Plugins-Emmaplugin`](http://docs.codehaus.org/display/GRADLE/Plugins#Plugins-Emmaplugin)

> **环境：** 你的测试（包括单元测试和集成测试）应该持续运行。你应该有一台专用机器，在每次提交时构建你的项目。对于 CI（持续集成）系统，我们建议你了解一下 Jenkins^(4)（在因法律原因更名之前，它被称为 Hudson）。它设置起来非常简单，并且运行得非常好。它会从你的版本控制系统（VCS）中检出代码并进行构建。如果你的项目可以使用构建工具（如 Gradle、Ant、Maven 等）构建，那么只需几分钟就能安排一个自动化构建。此外，由于你将使用此类工具来自动化构建，你的测试应该与环境无关。它们应该能够在你的智能手机上运行，而无需任何资源或环境依赖。测试应该能够在任何地方运行。测试还应该易于运行，最好只需一个命令，无需任何形式的配置或设置。

![Image](img/square.jpg) **注意** 验证项目测试套件的最佳方法是：找一台能够运行 Java 的随机设备，从你的 VCS 执行检出，禁用所有网络接口，然后输入一个命令来执行构建并运行你的测试。应该没有失败，并且你的测试覆盖率应能清晰地表明项目的主要部分都经过了测试。还有一个名为 sonar^(5) 的优秀工具（开源），它正是做这件事的：从 VCS 检出你的代码，并对其应用各种度量指标，包括测试覆盖率。我们强烈建议你尝试一下，因为它设置起来非常简单，并且对于保障代码质量的好处是巨大的。



#### 不同类型的测试

测试有多种类型。我们需要明确决定覆盖哪一层级的测试，最重要的是给出清晰的定义。本章将探讨单元测试、集成测试和系统测试。首先，我们将概述不同类型的测试并加以说明。这份列表并非详尽无遗，但能让你了解我们将覆盖的测试层级。

> *   **单元测试：** 单元测试的基本定义是“用于检查独立软件模块正确性的离散测试条件”。单元测试在软件模块的最低可能层级运行，最好是类中的单个方法，且不依赖任何外部资源（如资源文件）。我们将在下一小节中更详细地探讨单元测试。
> *   **集成测试：** 与单元测试不同，集成测试的范围更广，涉及测试应用程序的多个层级。我们将在“集成测试”部分更详细地探讨集成测试。

_________

⁴ [`http://jenkins-ci.org/`](http://jenkins-ci.org/)

⁵ [`http://www.sonarsource.com/`](http://www.sonarsource.com/)

> *   **系统测试：** 类似于集成测试，但使用真实资源。这些资源可以是实际生产资源的缩减版本，但重要的是环境设置和拓扑结构要尽可能匹配。这将确保项目能顺利迁移到其他环境。测试的编写也不依赖于应用程序内部构建方式的任何知识；它们将待测应用程序视为一个黑盒。使用真实浏览器进行的前端测试也是系统测试的一种形式。
> *   **用户验收测试：** 验收测试是由用户通过与应用程序界面交互来执行的测试。这是一种手动测试，在软件的某个部分实现时进行。尽早并频繁地进行验收测试是件好事；然而，单元测试和集成测试应该在问题到达用户之前就定位出大部分问题。因此，验收测试不应被视为单元测试和集成测试的替代品。
> *   **性能测试：** 理想情况下，应在尽可能接近应用程序最终部署的生产环境的镜像上进行。这里的*环境*指所有方面——硬件和软件，也包括依赖项，如数据库（含数据）、外部服务等。性能测试旨在检测应用程序在正常负载下的吞吐量。对于 Web 应用程序，它可以是在预测用户负载下请求的平均响应时间。性能测试的结果可以验证应用程序是否能够满足拟定的服务等级协议（SLA）。
> *   **压力测试：** 理想情况下需要与性能测试相同的环境。但这里我们让应用程序承受异常高的负载（或压力）。它旨在检测薄弱环节，例如开始出现故障的事务，或正常操作下不会出现的其他故障。总的来说，它能让我们了解当超出正常负载边界时，整个环境的稳定性和健壮性。

如前所述，我们将只讨论单元测试、集成测试和系统测试。对于系统测试，我们将仅限于自动化前端测试。这三个层级应占你整个（自动化）测试集的 90%或更多。它们将证明你的代码能正常工作，并且在添加功能或重构代码时仍能保持正常工作。这些测试能够自动运行，且在任何地方都能运行。在这一层级，Spring 的测试支持也将证明其最为有用。接下来，让我们更深入地了解单元测试。

##### 单元测试

单元测试在代码的最低可能层级伴随代码编写，并在开发阶段完成。它可以有多种形式，从验证领域对象行为的基础测试，到验证业务服务行为的更复杂测试。最重要的是，单元测试是系统无关的，并且不依赖任何资源。如果一段代码依赖于某个资源，那么该资源会以某种方式被“模拟”。我们将在后续章节讨论模拟对象（或称 mock，它是模拟器的一种形式），但目前只需理解 mock 是资源的代码替代品即可。一个好的单元测试应满足的最重要标准可列举如下。一个好的单元测试应做到以下几点：

> *   **运行速度快：** 单元测试必须运行极快。如果需要等待数据库连接、外部服务器进程或解析大文件，其效用将很快受限。测试应能提供即时反馈和即时满足感。
> *   **零外部配置：** 单元测试不得需要任何外部配置文件，即使是简单的文本文件也不行。测试的配置必须由测试框架本身通过调用代码来提供和设置。目的是最大限度地减少测试的运行时间，并消除外部依赖（这些依赖可能随时间变化，与测试不同步）。测试用例的条件应在测试框架中表达，从而创建更易读的测试条件。
> *   **独立于其他测试运行：** 单元测试必须能够完全隔离地运行。换句话说，单元测试不能依赖于在其之前或之后运行的其他测试。每个测试都是一个独立的单元。事实上，测试类中的每个测试方法都应是独立的，不依赖于其他方法，也不依赖于测试方法按特定顺序运行。
> *   **零外部资源依赖：** 单元测试不得依赖任何外部资源，例如数据库连接或 Web 服务。这些资源不仅会拖慢测试速度，而且它们不受测试控制，因此无法保证其处于正确的测试状态。
> *   **不改变外部状态：** 单元测试不得留下任何它曾运行过的痕迹。单元测试被设计为可重复的，因此它们必须自行清理。显然，当测试不依赖外部资源（这些资源通常更难清理或恢复）时，这一点更容易做到。
> *   **测试尽可能小的代码单元：** 单元测试必须测试尽可能小的代码单元，以便隔离被测代码。在面向对象编程中，这个单元通常是对象或类的一个方法。编写单元测试时，让一个方法独立于其他方法进行测试，可以减少可能包含潜在错误的代码行数。



##### 集成测试

集成测试伴随代码编写，在开发阶段完成，与单元测试类似。但与单元测试不同的是，集成测试跨越多个层级，并尽可能多地使用各种资源。在我们的实践中，集成测试的设计原则是绝不依赖“真实”系统资源，从而避免测试对特定环境产生依赖。你的测试应该能够在任何时间、任何地点运行。

违反这一原则的示例可能是使用预填充的外部数据库。在这种情况下，你只能在能够访问该数据库的环境中运行测试。如果数据库宕机或出现问题，你将无法运行测试。此外，所有用户都应拥有各自独立的数据库表，否则多个用户将无法同时运行测试而不相互干扰。当你在远程开发且没有 VPN 连接时，你将无法连接到数据库，从而无法运行测试。所有这些都会让你的工作变得比本应更复杂，并可能迅速打击你编写完善测试的积极性。

我们的集成测试将使用这些资源的内存等效物：内存数据库、内存目录服务器、内存浏览器等。它们允许测试应用程序多个层级的“集成”，而不会受到环境的影响。如果在某个节点无法使用此类资源，则会像单元测试一样使用模拟对象。

集成测试不能替代使用真实资源的系统测试。但它会尽可能模拟目标环境，从而减少在执行实际系统测试或用户验收测试时出现的问题。真实的系统测试通常资源消耗更大且更难自动化。如果你创建了可靠的集成测试框架，将在后续测试阶段受益，因为许多问题已经通过集成测试被检测并解决。

需要牢记的重点是，这些测试都伴随项目进行。它们由开发人员在开发阶段创建，并以自动化方式运行。如果任何测试失败，你的项目就处于“损坏”状态。同时，不要忘记，不依赖实际资源和用户的集成测试，能够让你在项目生命周期早期高效地锁定大量基础性问题。这使得后续的系统测试和用户验收测试更加顺畅和轻松。使用（在内存中运行或不产生依赖的）资源，总是比使用静态模拟器或模拟对象更好。

关于内存资源的最后一点说明：你的项目可能使用了仅在特定数据库管理系统上运行的专用查询，因此无法在其他数据库上执行。也可能存在相关语句根本不被支持的情况。在大多数情况下，这些专用查询仅占代码库的极小部分（对于一般项目而言）。更好的做法是仅为这些特定情况创建依赖资源的（系统）测试，而不是让整个测试集都依赖真实资源。

此外，如果你使用了特定数据库管理系统的特性或无法在其他资源上模拟的复杂查询逻辑，你确定你的 Java 代码是放置它的正确位置吗？数据库对象（如物化视图、存储过程或函数）是否是更好的解决方案？这一点会引发架构层面的讨论，但值得深思，它不仅会影响你的整体架构和可重用性，还会影响（Java）项目的可测试性。

![图片](img/square.jpg) **注意** 单元测试与集成测试之间并非总是存在明确界限，尽管本讨论强调了存在明显分界线的观点。有时你只需要测试业务算法，而不想被“如何获取数据”所困扰。我们会将其称为单元测试。在另一种情况下，你需要确保 HQL 或 JPQL 查询能转换为可执行的 SQL 并执行所需操作。这通常会在内存数据库上执行，因此我们称之为集成测试。



### 设置基本单元测试

在本节中，我们将解释如何在不涉及 Spring 的情况下，使用 JUnit 框架⁶ 设置一个基本的单元测试。我们将通过注解来介绍一些特定的 JUnit 特性。如果你已经熟悉 JUnit，⁶ 可以快速浏览本节内容。

_________

⁶ [`http://www.junit.org/`](http://www.junit.org/)

![Image](img/square.jpg) **注意** 根据经验，我们知道大多数开发者至少都熟悉 JUnit。由于我们想明确地向你展示 Spring 测试框架，我们选择将其与 JUnit 结合使用。不过，我们也建议你看看 TestNG。^(7) 虽然它与 JUnit 在源代码上不兼容，但它们在表面上看起来很像。如果你了解 JUnit，使用 TestNG 会非常轻松。然而，TestNG 在测试排序、表达依赖关系以及管理大量测试方面拥有明显更多的特性。TestNG 也比 JUnit 更灵活。此外，还有一个 STS（Eclipse）插件（以及用于 Gradle 和 Maven 的构建插件）。请参阅这份 IBM^(8) 资源，了解这两个框架之间的良好对比，并看看哪个更适合你和你的项目。

我们的基本设置首先是在测试类路径（`src/test/java`）下创建一个 Java 类（它将作为我们的测试类）。我们将使用 JUnit4，它通过注解来表示特定功能。我们在生产类路径（`src/main/java`）下创建一个 `DummyDao` 类，它将作为我们待测试的类（参见清单 9-1）。`DummyDao` 将对一个虚拟数据库（在我们的例子中是一个简单的 `ArrayList`）执行操作。`DummyDao` 类有三个操作：从数据库中删除一个条目、向数据库中添加一个条目，以及在数据库中搜索包含给定查询词的条目。

***清单 9-1.** 一个表示 DAO（数据访问对象）的简单类，我们将对其进行单元测试*

`package com.apress.prospringmvc.bookstore.dao;`

`public class DummyDao {`

`    private List<String> database;`

`    public DummyDao(List<String> database) {`
`        this.database = database;`
`    }`

`    public void delete(String element) {`
`        database.remove(element);`
`    }`

`    public void add(String element) {`
`        database.add(element);`
`    }`

`    public List<String> find(String queryWord) {`
`        List<String> result = new ArrayList<String>();`
`        for (String element : database) {`
`            if (element.contains(queryWord)) {`
`                result.add(element);`
`            }`
`        }`
`        return result;`
`    }`
`}`

_________

⁷ [`http://testng.org/doc/index.html`](http://testng.org/doc/index.html)

⁸ [`http://www.ibm.com/developerworks/java/library/j-cq08296/`](http://www.ibm.com/developerworks/java/library/j-cq08296/)

接下来，我们为每个方法添加测试（参见清单 9-2）。

***清单 9-2.** DatabaseDao 的测试用例*

`package com.apress.prospringmvc.bookstore.basic;`

`import static junit.framework.Assert.assertEquals;`
`import static junit.framework.Assert.assertTrue;`

`import org.junit.Before;`
`import org.junit.Test;`

`import com.apress.prospringmvc.bookstore.dao.DummyDao;`

`public class BasicUnitTest {`

`    private List<String> database;`
`    private DummyDao dummyDao;`

`    @Before`
`    public void dataSetup() {`
`        database = new ArrayList<String>();`
`        for (int i = 0; i < 20; i++) {`
`            database.add("test" + i);`
`        }`
`        dummyDao = new DummyDao(database);`
`    }`

`    @Test`
`    public void testDeleteQuery() {`
`        assertEquals(1, dummyDao.find("test0").size());`
`        dummyDao.delete("test0");`
`        assertEquals(19, database.size());`
`        assertEquals(0, dummyDao.find("test0").size());`
`    }`

`    @Test`
`    public void testAddQuery() {`
`        assertEquals(0, dummyDao.find("test20").size());`
`        dummyDao.add("test20");`
`        assertEquals(21, database.size());`
`        assertEquals(1, dummyDao.find("test20").size());`
`    }`

`    @Test`
`    public void testFindQuery() {`
`        List<String> results = dummyDao.find("2");`
`assertEquals(2, results.size());`
`     for (String result : results) {`
`        assertTrue(result.equals("test2") || result.equals("test12"));`

`@Test` 注解将一个方法标记为测试方法。该方法将被 JUnit 识别并执行。在这个测试方法内部，你将编写代码来测试实际的生产代码。测试类中其他没有特定注解的方法则不会被处理。`@Before` 注解会在每个测试方法之前运行。它将确保我们的数据库填充了初始数据。如果某个 `@Test` 方法更改了数据，我们将确保数据重置为初始状态。重要的是，每个方法都应作为独立的测试运行。你不应该在 `@Test` 方法之间建立依赖关系，否则你的测试代码会变得脆弱且难以快速理解。此外，你无法控制 `@Test` 方法的执行顺序。

STS 自带了一个 JUnit 插件。一旦你至少有一个 `@Test` 方法，就可以右键单击该类并选择“Run As”。额外的运行选项“JUnit Test”会自动出现。点击它即可运行测试。

`assertTrue` 和 `assertEquals` 方法是通过静态导入从 `junit.framework.Assert` 类中引入的（如清单 9-2 顶部所示）。这些方法允许你验证行为，如果行为不符合预期，则让测试停止。这只是一点语法糖，能让你的代码读起来更流畅。通常你不需要很多这样的方法。有了 `assertTrue` 和 `assertEquals`，你就能应对大部分情况了。

以 `testDeleteQuery` 方法为例，我们首先执行删除查询来删除数据库中的一个条目。接下来，我们检查数据库是否确实少了一个条目。如果不是这种情况，`assertEquals` 就会失败，并且该特定的测试方法会被标记为失败，如图 9-3 所示。

![Image](img/9781430241553_Fig09-03.jpg)

***图 9-3.** JUnit 测试失败*

如果所有测试都成功，你会看到一个绿色条，如图 9-4 所示。

![Image](img/9781430241553_Fig09-04.jpg)

***图 9-4.** JUnit 测试成功*

除了 `@Before` 方法，还有 `@After`。`@After` 方法会在每个测试方法之后运行，即使该方法抛出异常或某个断言失败。还有 `@BeforeClass` 和 `@AfterClass`，它们分别在整个测试类的第一个测试方法开始之前和最后一个测试方法结束之后执行一次。执行顺序如图 9-5 所示。

![Image](img/9781430241553_Fig09-05.jpg)

***图 9-5.** JUnit 注解执行顺序*

`@BeforeClass`、`@Before`、`@After` 和 `@AfterClass` 当然都是可选的注解（这就是为什么它们用虚线框表示）。例如，如果你没有指定 `@Before`，JUnit 会直接开始执行你的测试方法，然后，如果你指定了 `@After`，则执行 `@After` 方法。如果你没有指定，则运行下一个测试方法。别忘了，`@Before` 和 `@After` 会在*每个*测试方法之前和之后运行。`@BeforeClass` 和 `@AfterClass` 只运行一次，分别在测试开始和结束时。JUnit 还有一些额外的特性，比如 Suites 和 Categories，但它们超出了本章的范围。你可以在 JUnit 网站^(9) 和 JavaDoc^(10) 中找到更多信息。

至此，我们快速介绍了 JUnit 以及如何编写一个简单的测试。这对于本章剩余部分来说已经足够了。



### 测试代码覆盖率

使用 STS 插件 EclEmma，你可以概览测试所覆盖的代码。操作非常简单：进入 Spring 仪表盘，点击视图底部的扩展选项卡，在搜索框中输入 **eclemma**，然后安装 EclEmma 插件（参见图 9-6）。

_________

⁹ [`http://www.junit.org/`](http://www.junit.org/)

¹⁰ [`http://kentbeck.github.com/junit/javadoc/latest/`](http://kentbeck.github.com/junit/javadoc/latest/)

![Image](img/9781430241553_Fig09-06.jpg)

***图 9-6.** 安装 EclEmma*

安装插件后，你可以右键点击测试，然后选择“覆盖率作为 JUnit 测试”。结果将显示在一个单独的面板中，如图 9-7 所示。如果该面板没有出现，你可以手动打开它：在 STS 菜单栏中选择“窗口”，然后选择“显示视图”->“其他”。接着在出现的文本框中输入 **coverage**，列表中会出现一个结果：其下带有“覆盖率”的“Java”。选择“覆盖率”，然后点击“确定”。

![Image](img/9781430241553_Fig09-07.jpg)

***图 9-7.** 运行 EclEmma 后的代码覆盖率*

我们可以清楚地看到，我们的 `DummyDao` 类已经达到了 100% 的测试覆盖率，这意味着每一行代码都被我们的测试用例执行过。根据你测试的类类型，并非总能达到 100% 的覆盖率，但这应该是你为每个测试努力追求的目标。请注意，在某些情况下，达到 100% 覆盖率并不可行，或者根本没有必要。例如，如果你的对象有多个 get/set 方法（由你的 IDE 生成），那么测试这些方法就有点多余，即使这会导致无法达到 100% 的覆盖率。但是，也不要过早放弃。如果因为代码依赖于资源而导致覆盖率下降，你可以使用内存资源或模拟对象，我们将在接下来的章节中讨论。

执行覆盖率分析后，你还可以双击你测试过的实际生产类：在我们的例子中是 `DummyDao`。EclEmma 会指示哪些行已被（或未被）测试。在这次运行中，所有内容都会显示为绿色，因为每一行代码都已被测试。我们还进行了第二次运行，移除了测试方法中对 `DummyDao` 的 `find` 方法的所有引用。我们将两次运行的结果并排放置在图 9-8 中。

![Image](img/9781430241553_Fig09-08.jpg)

***图 9-8.** EclEmma 显示已测试（绿色）和未测试（红色）的代码行*

左侧你可以看到第一次运行中 100% 的代码覆盖率（全部为绿色）。右侧则是我们移除了测试中对 `find` 方法所有引用的运行结果。`find` 方法会显示为红色，因为它没有被我们的测试执行到。某些行左侧的菱形图标表示循环是否针对列表中的每个元素都执行了一次。例如，如果我们在 `if` 之后添加一个 `break` 语句，循环将只运行一次，那么第一个菱形会显示为黄色，表示该分支未被完全覆盖。第二个菱形也是如此，它覆盖了 `if` 语句。如果该语句未被执行，菱形也会显示为橙色。

### 使用 Spring 的测试支持

在上一节中，你了解了如何使用 JUnit 创建基本的单元测试。这是一个非常独立的测试，不依赖于 Spring 或其他资源等任何依赖项，正如单元测试应有的样子。在本节中，我们将介绍 Spring 的测试支持，以创建实际在测试中一起使用这些依赖项的集成测试。

Spring 的测试支持主要涉及将 Spring 集成到你的测试中。通过几个注解，你可以加载一个 Spring 上下文，并使其可用于你的单元测试。这允许你直接在测试中注入依赖项，例如，使用自动装配。

#### 设置我们的集成测试

接下来，我们将设置一个集成测试，用于测试我们的 `com.apress.prospringmvc.bookstore.repository.JpaBookRepository`。我们首先在测试类路径中创建一个普通类（参见清单 9-3）。理想情况下，你应该将测试放在与要测试的类相同的包中，但放在测试类路径而不是生产类路径（即 `src/main/java`）下。因此，我们将在 `src/test/java` 类路径下创建一个 `com.apress.prospringmvc.bookstore.repository.JpaBookRepositoryTest`。

***清单 9-3.** 测试 JpaBookRepository 的集成测试的开头，位于 src/test/java*

`package com.apress.prospringmvc.bookstore.repository;`

`public class JpaBookRepositoryTest {`
`}`

为了启用 Spring 测试上下文，我们告诉 JUnit 它必须引导 Spring，方法是在类顶部添加以下注解，如清单 9-4 所示。

***清单 9-4.** 添加运行器以启用 Spring 的测试支持*

`package com.apress.prospringmvc.bookstore.repository;`

`import org.junit.runner.RunWith;`
`import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;`

`@RunWith(SpringJUnit4ClassRunner.class)`
`public class JpaBookRepositoryTest {`
`}`

`SpringJUnit4ClassRunner` 是 JUnit 的一个 Spring 特定集成类。JUnit 会检查那些带有 `@RunWith` 注解的类。当你指示要将一个类作为 JUnit 测试运行时，该测试类会作为参数传递给 JUnit 运行时（在我们的例子中，这是由 IDE 完成的）。然后 JUnit 会扫描 `@RunWith` 注解。在我们的例子中，它将执行 `org.springframework.test.context.junit4.SpringJUnit4ClassRunner`。这个类将通过使用 Spring 的 `org.springframework.test.context.TestContextManager` 和 `org.springframework.test.context.TestContext` 来启动 Spring 的测试支持。这将启用所有 Spring 功能，例如加载你的应用程序上下文（基于 `@ContextConfiguration`，我们将在后面看到）、启用自动装配以及执行 `org.springframework.test.context.TestExecutionListener` 的实现。后者是特殊的监听器，可以在测试类初始化之前、运行测试类之前以及运行测试类中的测试方法之前执行。它们可以使用 `TestExecutionListener` 注解进行注册。默认注册了三个监听器：

> *   `org.springframework.test.context.support.DependencyInjectionTestExecutionListener`
> *   `org.springframework.test.context.support.DirtiesContextTestExecutionListener`
> *   `org.springframework.test.context.transaction.TransactionalTestExecutionListener`

就我们这里的目的而言，只有最后一个监听器是相关的，因为它将管理我们测试中的事务；我们稍后会讨论这一点。

最后，我们必须指明要为我们的测试类加载哪个（测试）上下文。此时你有两个选择：

> *   一种方法是加载一个或多个生产上下文。这些是你的应用程序实际使用的上下文。如果你想区分测试运行和实际应用程序运行，可以使用 Spring 配置文件。
> *   你也可以选择仅为你的测试设置特定的上下文。这样你就可以精确指定你需要哪些资源。

每种方法都有其优缺点，因此你应该根据你的应用程序以及你要测试的内容来权衡，以确定哪种方法最佳。你的 Spring 配置（无论是注解类还是 XML 配置文件）越模块化，就越容易将这两种方法结合起来。当你有一个列出你的仓库的 Spring 配置，但有一个单独的数据源配置时，你可以在测试中加载仓库的生产配置，同时为数据源组合一个单独的测试上下文（在这种情况下，该上下文将指向一个内存数据库）。


当你确定要加载哪些上下文后，可以在类上使用 `@ContextConfiguration` 注解来指定它们。使用此注解时，你可以通过 `locations` 属性（接收一个 XML 配置文件数组）指定上下文 XML 文件，或者通过 `classes` 属性（接收一个 `@Configuration` 类数组）来加载上下文。你*不能*同时使用两者。如果你想混合使用 XML 和类，则必须从其中一方加载另一方。这意味着，如果你想使用 XML，但同时需要加载 `@Configuration` 类，则必须从某个 XML 上下文文件中加载它们。如果你在使用 `@Configuration` 类，则必须在其中一个类中使用 `@ImportResource` 来加载所需的 XML 配置文件。

我们将使用生产环境的上下文类，因此我们将使用 `classes` 属性（参见清单 9-5）。

***清单 9-5.** 添加用于加载 Spring 配置的上下文配置*

`package com.apress.prospringmvc.bookstore.repository;`

`import org.junit.runner.RunWith;`
`import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;`
`import com.apress.prospringmvc.bookstore.config.`![Image](img/U002.jpg)
`       InfrastructureContextConfiguration;`
`import com.apress.prospringmvc.bookstore.config.TestDataContextConfiguration;`
`import org.springframework.test.context.ContextConfiguration;`
**`@RunWith(SpringJUnit4ClassRunner.class)`**
**`@ContextConfiguration(classes = { InfrastructureContextConfiguration.class,`![Image](img/U002.jpg)**
**`TestDataContextConfiguration.class })`**
`public class JpaBookRepositoryTest {`

`}`

仅此而已。当你以 JUnit 测试方式运行时，得益于 Spring 测试支持（其本身由 `SpringJUnit4ClassRunner` 初始启动），Spring 上下文将被启动。在我们的例子中，这意味着所有仓库和服务都被加载到上下文中，并且一个内存数据库被启动并预填充了数据。

在本节中，我们只测试 `JpaBookstoreRepository`。但还有很多其他类需要测试。我们将为每个类创建相应的测试，并应用相同的注解来为每个测试类加载上下文。

![Image](img/square.jpg) **注意** 与其为每个测试类独立重复注解配置，不如将其移动到一个公共超类中，让每个测试类继承它。这样就能避免在每个测试类中重复相同的注解配置。JUnit 注解（如我们之前看到的 `@Before`、`@After` 等）也是如此。然而，为了让每个类自身完整，我们重复了配置。此外，你不应仅仅为了避免重复注解配置而创建超类。只有当这些类有更多共同点时，才这样做。例如，为了测试你的仓库，你可以设置一个超类，它包含注解配置并设置一个对所有仓库测试都有用的最小数据集。创建仅包含注解配置的超类，很快就会导致这些类过度膨胀，并使你的代码变得不够清晰。

由于这是一个小型项目，上下文几乎可以瞬间加载。对于较大的项目，这可能会花费更多时间。为了确保上下文不会为每个类独立加载，Spring 测试框架提供了 Spring 应用上下文的一致加载和缓存功能。对已加载上下文的缓存支持非常重要，因为启动时间可能成为一个问题——这并非 Spring 本身的问题，而是因为 Spring 容器实例化的对象需要时间。例如，一个包含许多 Hibernate 映射文件的项目，由于需要解析每个映射文件，加载可能需要几秒钟。如果上下文只加载一次，额外 5 秒的加载时间不会很明显。但如果它为每个测试单独加载（例如，你有 200 个测试），那么 5 秒的额外加载时间意味着你的测试集将毫无理由地多运行 16 分钟。如果你的测试运行缓慢，就会开始拖累你的基础设施，这可能导致人们找借口不写测试，因为“它们运行得太慢了”。保持测试集精简高效非常重要，这样运行测试集所花费的时间就能尽可能少。

默认情况下，一旦配置好的 `ApplicationContext` 被加载，它就会为每个测试重用。因此，设置成本（每个测试套件）只产生一次，后续的测试执行会快得多。在此上下文中，术语*测试套件*指在同一个 JVM 中运行的所有测试。然而，当你逐个从 STS 运行测试时，这意味着应用上下文将为每个测试单独启动。解决方案（使用 JUnit）是构建一个套件，或者使用你的构建工具（在我们的例子中是 Gradle）来运行它们。在这种情况下，应用上下文将只为所有运行的测试加载一次。使用 Gradle 构建 chapter9 示例项目将自动运行测试。你首先进入 chapter9 示例目录，然后发出以下 Gradle 命令来启动构建：

`$ gradle build`

输出将类似于这样：

`:bookstore-shared:compileJava UP-TO-DATE`

***`-- 为简洁起见，省略了进一步的 Gradle 输出 --`***

`:chapter9-bookstore:testClasses`
**`:chapter9-bookstore:test`**
`22:30:31.392 [Thread-15] INFO  o.s.c.s.GenericApplicationContext - Closing`
`org.springframework.context.support.GenericApplicationContext@39518cc: startup date [Thu Mar`
`29 22:30:28 CEST 2012]; root of context hierarchy`
`22:30:31.397 [Thread-15] INFO  o.s.b.f.s.DefaultListableBeanFactory – Destroyi`

***`-- 为简洁起见，省略了进一步的 Gradle 输出 --`***

`22:30:31.401 [Thread-15] INFO  o.s.o.j.LocalContainerEntityManagerFactoryBean - Closing JPA`
`EntityManagerFactory for persistence unit 'bookstore'`

`:chapter9-bookstore:check`
`:chapter9-bookstore:build`

`BUILD SUCCESSFUL`

`Total time: 17.033 secs`

在上面的例子中，所有测试都由 Gradle 运行，因此共享一个应用上下文实例。在极少数情况下，如果某个测试破坏了应用上下文（例如，通过修改 bean 定义或应用对象的状态）并需要重新加载，可以将 Spring 的测试支持配置为在执行下一个测试之前重新加载配置并重建应用上下文。后者可以通过在修改应用上下文的类甚至测试方法上放置 `@DirtiesContext` 注解来实现。下一个测试类（或测试方法）将获得一个刷新后的应用上下文。如果需要，你也可以直接注入 `ApplicationContext`（参见清单 9-6）。

***清单 9-6.** 如果需要，应用上下文可以被自动装配。*

`@Autowired`
`Private ApplicationContext ApplicationContext`

由于我们启用了 Spring 的测试支持，我们现在为我们的测试用例启用了 Spring，并使用了提供的配置。所有 Spring 特性现在都可以在我们的测试中使用。例如，我们可以直接在测试中自动装配 Spring 配置中加载的任何 bean。我们的测试类现在已完全具备 Spring 感知能力。



##### 在测试内部管理事务

由于我们将编写一个集成测试，该测试会（通过我们正在测试的仓库）访问内存数据库，因此我们需要在测试用例内部管理事务。在生产代码中，事务由服务层管理，而非仓库本身。仓库参与由服务启动的事务。因此，在这种情况下，我们需要测试用例来启动事务，因为我们将直接测试仓库。我们将通过在测试类上添加 `@Transactional` 注解来实现这一点，使每个测试方法首先启动一个事务。

当找到此类注解时，自动注册的 `TransactionalTestExecutionListener` 将确保在测试方法结束时默认执行事务*回滚*。正如本章前面提到的，`TransactionalTestExecutionListener` 是 Spring 测试支持注册的默认监听器之一。为了让您快速了解：这些监听器（以及所有其他可能注册的 `TestExecutionListener`）会在测试方法执行之前（和之后）被调用。这个特定的监听器会在测试方法结束后自动将事务标记为回滚。请记住，这是 Spring 测试支持特有的功能。当您以正常方式启动应用程序上下文时，这些监听器并不存在。因此，在正常的生产场景中，如果没有发生异常，事务将会提交。

回滚实际上正是您想要的行为。这意味着 CUD 操作（创建、更新和删除）不会修改我们数据库中的数据。允许它们修改数据是最不可取的方法，因为这可能会在测试之间创建数据依赖关系。当测试被修改，或者由于某种原因测试的运行顺序被改变时，某些测试可能会突然开始失败，因为它们依赖于另一个尚未运行的测试所添加的数据。这里的最佳模式是在方法级别插入您需要的数据，然后让事务回滚。这样数据就不会持久化到数据库中，也不会留下任何痕迹。

![图片](img/square.jpg) **注意** 在测试仓库时，您可能需要一些默认的数据设置，这些数据可用于每个测试方法。与其为每个测试方法重复相同的数据设置，不如安全地在 `@Before` 注解的方法中创建这些数据。该方法会在每个测试方法之前自动运行。它也会在与测试方法本身相同的事务中运行，因此插入、修改或删除的数据将像正在执行的测试方法一样被回滚。

这种在每个测试方法结束时自动回滚事务的方法也有一个缺点。当使用 ORM（对象关系映射）时，回滚对于读取操作非常有效，因为所有查询都已发送到数据库，为您提供了完整的覆盖率。但是，当使用像 Hibernate 这样的 ORM（直接使用或通过 JPA 使用）时，CUD 操作可能不会被刷新。Hibernate 使用写后缓存。它会在认为时机成熟时，或者至少在事务提交时，将语句刷新到数据库。因此，当您调用了触发更新或插入语句的方法时，该语句在回滚事务时可能永远不会被刷新到数据库。在这种情况下，您永远无法确定该查询是否真的在数据库上执行了。您有两个选项来处理这个问题：

> *   无论如何都让事务提交。您可以通过在测试方法上添加 `@Rollback(false)` 来实现。然后，您可以创建一个拆卸方法（`@After`），用于删除您插入的数据。
> *   在测试方法结束之前，调用 `EntityManager`（在 JPA 的情况下）上的 `flush` 方法，让它将所有未处理的语句刷新到数据库。请记住，事务将会回滚，因此更改不会持久化。但它们已被数据库接收并验证，因此您可以确保它们能够正常工作。

选择这两种方法中的哪一种取决于个人偏好；两者都会产生相同的结果。第一种方法要求您在拆卸方法中编写一些额外的代码。第二种方法更自动化，但需要对您使用的 ORM 有更深入的了解。对于第二种解决方案，您也可以将 `flush` 放在 `@After` 方法中，也许是从父类继承而来的。

正如我们之前提到的，集成测试并不能提供 100% 的保证。首先，我们使用的是与您的实际目标环境不同的 RDBMS。当回滚事务而不是提交它们时，您增加了实际提交时可能出现问题的可能性。然而，当应用前面讨论的任何策略时，这种情况发生的可能性非常小。如果您刷新了，所有语句都已被数据库处理，因此通常没有理由提交会失败。尽管在非常特定的场景中可能仍然会出现问题，但这种情况发生的几率非常小。最大的优点是您的测试保持独立，并且当您添加另一个本身完全正常工作的测试时，您永远不必弄清楚为什么突然有 10 个测试开始失败。您的测试集也将是资源无关的，并且可以在任何地方运行。很明显，这些都是主要的优势。

![图片](img/square.jpg) **注意** 如您所知，大型代码库在设计、API、一致性等方面难以管理。不要忘记，您的测试代码量甚至可能超过您的实际生产代码。善待它们，它们也会善待您。



#### 测试 JpaBookRepository

现在我们已经拥有了构建测试所需的所有要素。对于事务策略，我们将选择第二种方案。也就是说，我们依赖基础设施来启动和回滚事务，并在执行 CUD 操作的测试方法中手动调用 flush。

接下来，我们继续为 `BookRepository` 接口暴露的每个方法定义一个测试方法并进行测试。在示例代码中，我们将测试范围限定为 `BookRepository` 的两个 `read` 方法和一个 `create` 方法。其他方法的测试方式与此相同，不会展示任何新内容。我们还将创建一个 setup 方法（使用 `@Before` 注解），用于加载每个测试方法所需的公共数据。首先，让我们查看清单 9-7 中的测试代码。

***清单 9-7.** src/test/java 中的完整测试类*

`package com.apress.prospringmvc.bookstore.repository;`

`import static junit.framework.Assert.assertEquals;`

`import javax.persistence.EntityManager;`
`import javax.persistence.PersistenceContext;`

`import org.junit.After;`
`import org.junit.Before;`
`import org.junit.Test;`
`import org.junit.runner.RunWith;`
`import org.springframework.beans.factory.annotation.Autowired;`
`import org.springframework.test.annotation.Rollback;`
`import org.springframework.test.context.ContextConfiguration;`
`import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;`
`import org.springframework.transaction.annotation.Transactional;`

`import com.apress.prospringmvc.bookstore.config.`
`       InfrastructureContextConfiguration;`![Image](img/U002.jpg)
`import com.apress.prospringmvc.bookstore.config.TestDataContextConfiguration;`
`import com.apress.prospringmvc.bookstore.domain.Book;`
`import com.apress.prospringmvc.bookstore.domain.Category;`
`import com.apress.prospringmvc.bookstore.domain.support.BookBuilder;`
`import com.apress.prospringmvc.bookstore.domain.support.CategoryBuilder;`
`import com.apress.prospringmvc.bookstore.domain.support.EntityBuilder.`
`       EntityBuilderManager;`![Image](img/U002.jpg)

`@RunWith(SpringJUnit4ClassRunner.class)`
`@ContextConfiguration(classes = { InfrastructureContextConfiguration.class, TestDataContextConfiguration.class })`
**`@Transactional`**
`public class JpaBookRepositoryTest {`

`    @Autowired`
`    private BookRepository bookRepository;`
`    @Autowired`
`    private BookBuilder bookBuilder;`
`    @Autowired`
`    private CategoryBuilder categoryBuilder;`
`    @PersistenceContext`
`    private EntityManager entityManager;`

`    private Book book;`
`    private Category category;`

`    @Before`
`    public void setupData() {`
`        category = categoryBuilder.name("Evolution").build();`
`        book = bookBuilder.description("Richard Dawkins' brilliant`
`               reformulation of the theory of natural selection")`
`               .author("Richard Dawkins").title("The Selfish Gene:`![Image](img/U002.jpg)
`                         30th Anniversary Edition")`
`               .isbn("9780199291151")`
`               .category(category).build();`
`    }`

`    @Test`
`    public void testFindById() {`
`        Book book = bookRepository.findById(this.book.getId());`
`        assertEquals(this.book.getAuthor(), book.getAuthor());`
`        assertEquals(this.book.getDescription(), book.getDescription());`
`        assertEquals(this.book.getIsbn(), book.getIsbn());`
`    }`

`    @Test`
`    public void testFindByCategory() {`
`        List<Book> books = bookRepository.findByCategory(category);`
`        assertEquals(1, books.size());`

`        for (Book book : books) {`
`            assertEquals(this.book.getCategory().getId(), category.getId());`
`            assertEquals(this.book.getAuthor(), book.getAuthor());`
`            assertEquals(this.book.getDescription(), book.getDescription());`
`            assertEquals(this.book.getIsbn(), book.getIsbn());`
`        }`
`    }`

`    @Test`
`    public void testStoreBook() {`
`        Book book = bookBuilder.description("Something").author("JohnDoe").title("John Doe's`![Image](img/U002.jpg)
`life")`
`                .isbn("1234567890123").category(category).build();`
`        bookRepository.storeBook(book);`

**`// 显式刷新，确保所有遗留的 CUD 查询在回滚前发送到数据库`**
**`entityManager.flush();`**
`    }`
`}`

在这段代码中，我们默认将测试设置为事务性的。对于 `read` 方法，我们使用前面讨论过的 JUnit `@Before` 方法插入额外数据。我们使用 Builder 模式构建数据，默认情况下，这些数据通过注入到构建器中的 `EntityManager` 实例进行插入。当测试方法结束时，由于事务回滚，这些数据也会被移除。

![Image](img/square.jpg) **注意** 我们之前提到过，在测试执行 CUD 功能的方法时，应该执行一次 flush，以确保所有语句都被刷新到数据库。在测试只读方法时，我们首先使用 JPA 将数据插入数据库。那么这些插入语句的刷新呢？实际上，我们并不关心。ORM 会根据需要自行刷新它们，以确保我们的读取操作基于最新数据。它可能会延迟插入 category 和 book，但一旦它看到我们按 ID 查询书籍的查询语句，就会先将 book 和 category 刷新到数据库。关键在于，我们并没有测试任何 CUD 操作。ORM 如何在内部管理延迟写入，以确保我们的读取操作看到最新数据，这并非我们关注的重点。

在测试创建新图书记录的方法中，我们在测试方法结束前显式执行了 flush。这样我们就能确保 `insert` 语句（由仓库 `storeBook` 方法中的查询生成）已发送到数据库（这可能是唯一的语句）。之后，测试方法结束，事务被回滚。

### 使用 Mock 对象

在本节中，我们将介绍如何使用 Mock 对象。在创建单元测试时，你需要确保测试尽可能隔离，不依赖外部资源。而在集成测试中，你则要尽可能多地使用资源，只要你能保持控制，并且这些资源不会使测试依赖于特定资源或环境即可。对于那些无法做到这一点的情况，Mock 对象将为你提供帮助。让我们先来看看 Mock 对象究竟是什么。



#### 什么是模拟对象？

正如引言中所述，在某些情况下，你希望隔离某个特定算法进行单元测试。该算法可能依赖于一个为其提供数据的仓库，而你不想费心去设置测试数据。你希望将测试重点完全放在算法本身。模拟对象在这里是一个很好的解决方案，因为它允许你插入一个真实资源的替代品，并预先为其编程设定数据。每当算法调用模拟对象时，它就能返回你在测试设置中为其编程设定的数据。

模拟对象还有其他好处。模拟对象是智能的。例如，它可以被设计成仅当方法调用的参数匹配某个特定值时才会返回数据。模拟对象还可以被验证。当测试完成时，你可以要求模拟对象检查所有你为其编程设定的方法是否确实以预期的参数被调用了。如果没有，它就会让你的测试失败。模拟对象的另一个用例是针对那些没有简单内存替代方案的资源；例如，当你依赖一个遗留系统时。

模拟对象属于所谓的“测试替身”范畴。测试替身有不同的种类，但其中只有一种是真正的模拟对象。马丁·福勒在他的文章《Mocks Aren't Stubs》^(11)中区分了不同的测试替身并恰当地命名了它们。让我们来看看：

> *   **哑元。** 哑元通常是给定接口的真实实现。例如，我们可以构建一个 `DummyBookRepository`。哑元的方法体大多是空的。如果其签名要求它们返回一个值，那么通常返回“null”。哑元对于构建你要测试的对象很有用，这些对象对哑元有强制性的依赖。但所讨论的这个依赖关系并不用于测试给定的功能。
> *   **伪造。** 伪造是真实资源的工作替代品，例如内存数据库或 Web 服务的内存实现。很明显，内存伪造相比任何外部依赖都具有真正的优势，因为外部依赖会再次打破我们的规则——即我们的测试应该能够在任何地方运行，同时，一旦伪造被替换为真实资源，它仍能提供高水平的保证，确保你的代码能正常工作。
> *   **桩。** 桩是替换对象，当它们的方法被调用时，能够返回预先编程设定的值。它们通常不会响应其被编程设定值之外的值。它们捕获状态的方式非常基础，并且验证过程大多不允许你进行行为验证。这些有时也被称为“模拟器”。
> *   **模拟。** 与桩相比，模拟对象在训练（设置预编程数据）和验证阶段增加了额外的功能。模拟对象不仅允许你验证某个给定方法是否被调用，还能验证它是否以期望的参数被调用。这通常是内建在模拟框架中的，不需要你自己构建（与桩不同）。

根据我们的经验，所有这些测试替身在项目中都有其特定的位置。虽然所有测试替身实际上都可以被模拟对象替代，但你或许不应该这样做。模拟对象是在测试中即时构建的，为其编程设定数据，期望特定的行为，并在测试结束前进行验证。哑元是位于项目代码中的一个真实类。除了 `com.apress.prospringmvc.bookstore.repository.JpaBookRepository` 之外，可能还存在一个“`DummyBookRepository`”。除了存在之外，它不应该做任何事情。其优势在于，你可以在测试中使用 Spring 配置文件或选择性上下文加载来装配其中一个。这可能会使你的代码库更整洁，测试更易于理解。此外，当你在真正需要哑元的地方使用了模拟对象时，你会向阅读你代码的其他开发者发出错误的信号。他们会看到一个既未编程设定也未经验证的模拟对象，因此会认为这是一个开发者错误。使用哑元，你清楚地表明该依赖关系在你正在执行的测试范围内不相关，但你正在测试的对象对其有强制性依赖，因此你给了它一个哑元实现。

_________

¹¹ [`http://martinfowler.com/articles/mocksArentStubs.html`](http://martinfowler.com/articles/mocksArentStubs.html)

在某些时候，你想要构建一个真正的集成测试。在上一节中，我们针对一个内存数据库测试了我们的 `JpaBookRepository`。尽管我们可以模拟 `EntityManager`，但不这样做带来了巨大的价值；它告诉我们，我们的 JPQL 查询被翻译成了实际正确的 SQL，并且可以被 RDBMS 执行。用模拟对象替换它会从我们的测试中削减关键内容，并降低其有效性。

桩可能只在特殊的集成测试中有用，即当某个给定资源（例如遗留系统）没有现成的伪造可用时。此时，桩可以作为“带数据的哑元”被装配进来。

最后一点，仔细考虑你将使用哪种测试替身。在编写单元测试时，首先考虑使用哑元。如果你真的需要对依赖项进行状态和行为验证，考虑使用模拟对象。如果你在进行集成测试，尽可能多地考虑使用内存伪造。如果你从另一个接口进行集成测试（例如，使用内存浏览器进行前端测试），考虑使用桩。

在此讨论中，我们将仅限于为我们的单元测试使用和创建模拟对象。在下一节中，我们将深入代码，测试我们的 `AccountService`，同时为其 `AccountRepository` 依赖项使用模拟对象。



#### 测试 AccountService

我们的 `com.apress.prospringmvc.bookstore.service.AccountService` 有两个方法：`save` 方法，用于将新的 `com.apress.prospringmvc.bookstore.domain.Account` 保存到数据库；以及 `login` 方法，该方法接收用户名和密码作为参数并验证凭据。如果凭据正确，则返回属于该用户的 `Account`。它通过从数据库中加载用户名匹配的 `Account` 来实现这一点。之后，它会尝试验证密码。密码是经过 SHA-256 哈希处理的。因此，该服务首先根据密码参数创建一个哈希值，然后将其与从数据库中检索到的值（我们只存储密码的哈希值）进行比较。

我们将编写一个单元测试来测试 `login` 方法。为此，我们将为 `com.apress.prospringmvc.bookstore.repository.AccountRepository`（这是用于根据用户名从数据库加载 `Account` 的仓库）创建一个模拟对象。我们将创建一个测试方法来测试成功登录，并创建另一个执行负面测试的方法，在该方法中我们故意提供错误的凭据，这应该会导致产生异常。让我们看看 清单 9-8 中测试的第一个版本。

***清单 9-8.** 在 src/test/java 中设置 AccountServiceTest*

`package com.apress.prospringmvc.bookstore.service;`

`// 其他导入已省略，参见清单 9-7`

`import static junit.framework.Assert.assertEquals;`

`import org.mockito.Mockito;`
`import org.springframework.context.annotation.Bean;`
`import org.springframework.context.annotation.Configuration;`

`import com.apress.prospringmvc.bookstore.domain.Account;`
`import com.apress.prospringmvc.bookstore.domain.support.AccountBuilder;`
`import com.apress.prospringmvc.bookstore.repository.AccountRepository;`

`@RunWith(SpringJUnit4ClassRunner.class)`
`@ContextConfiguration`
`public class AccountServiceTest {`

`    @Autowired`
`    private AccountService accountService;`
`    @Autowired`
`    private AccountRepository accountRepository;`

`    @Test(expected = AuthenticationException.class)`
`    public void testLoginFailure() throws AuthenticationException {`
`        accountService.login("john", "fail");`
`    }`

`    @Test()`
`    public void testLoginSuccess() throws AuthenticationException {`
`        Account account = accountService.login("john", "secret");`
`        assertEquals("John", account.getFirstName());`
`        assertEquals("Doe", account.getLastName());`
`    }  `

`    @Configuration`
`    static class AccountServiceTestContextConfiguration {`

`        @Bean`
`        public AccountService accountService() {`
`            return new AccountServiceImpl();`
`        }`

`        @Bean`
`        public AccountRepository accountRepository() {`
`return` **`Mockito.mock(AccountRepository.class);`**
`        }`
`    }`
`}`

Mockito^(12) 是一个我们将使用的先进的模拟框架。它很灵活，并且拥有非常直观的 API。在本节中，我们将让您更熟悉如何使用 Mockito 来满足您的所有模拟需求。加粗的那一行代码就是使用 Mockito 创建模拟对象所需的全部内容。您传入一个接口或类，Mockito 将返回一个尚未编程的模拟对象。

进一步发生的是，当添加 `@ContextConfiguration` 注解时，Spring 会自动扫描该类范围内的 `@Configuration` 类，并找到我们的 `AccountServiceTestContextConfiguration`。或者，您也可以直接在 `@ContextConfiguration` 注解上使用 `classes` 属性指定类，或使用 `locations` 属性指定 XML 文件。在 `AccountServiceTestContextConfiguration` 配置类中，我们创建了两个 Bean：第一个是 `AuthenticationService`，第二个是 `AccountRepository`。对于第二个 Bean，我们使用了 Mockito 来创建一个模拟对象。Spring 现在会在创建上下文时自动将 `AccountRepository` 注入到 `AuthenticationService` 中（请记住，Spring 将注入我们模拟版本的 `AccountRepository`）。

此时测试将无法运行，因为我们还没有对模拟对象进行编程。当 `AuthenticationService` 的 `login` 方法调用我们的 `AccountRepository` 模拟对象上的方法时，它将返回 null 值。因此，下一步就是对我们模拟对象进行编程，如 清单 9-9 所示。

![Image](img/square.jpg) **注意** `testLoginFailure`（所谓的“负面”测试）使用 JUnit 特性来验证确实抛出了异常：`@Test(expected = AuthenticationException.class)`。在这种情况下，我们要求实际抛出 `AuthenticationException`。如果没有抛出，则测试失败。

***清单 9-9.** 对模拟对象进行编程*

`@Before`
`public void setup() {`
`    Account account = new AccountBuilder() {`
`        {`
`            address("Herve", "4650", "Rue de la gare", "1", null,`![Image](img/U002.jpg) `"Belgium");`
`            credentials("john", "secret");`
`            name("John", "Doe");`
`        }`
`    }.build(true);`

`    Mockito.when(accountRepository.findByUsername("john"))`
`           .thenReturn(account);`
`}`

我们首先创建了模拟对象需要返回的数据。在我们的例子中，它是一个 `Account` 实例。清单 9-9 的最后一行代码对模拟对象进行了编程。每当模拟对象接收到对 `findByUsername` 方法且参数为 `"john"` 的调用时，它将返回该 account。如果它接收到其他参数的调用（或未编程的其他方法），则返回 null。此逻辑被放在一个 `@Before` 方法中，因为我们将在每次测试方法调用后重置模拟对象。在新的测试执行开始之前，模拟对象将被重新编程。

_________

¹² [`http://code.google.com/p/mockito/`](http://code.google.com/p/mockito/)

最后，在 清单 9-10 中，我们有 `@After` 方法，它将验证模拟对象的状态。它将检查我们编程的模拟对象方法是否确实被调用了。这里我们将严格检查该方法被调用了一次（恰好一次）。之后，我们重置模拟对象，使其忘记其编程数据和验证状态。然后它可以被重新编程（这将在执行下一个方法之前的 `@Before` 方法中进行）。

***清单 9-10.** 验证模拟对象的状态并重置它*

`@After`
`public void verify() {`
`    Mockito.verify(accountRepository,`
`                   VerificationModeFactory.times(1))`![Image](img/U002.jpg)
`                   .findByUsername(Mockito.anyString());`

`    // 这里允许：使用容器注入的模拟对象  `
`    Mockito.reset(accountRepository);`
`}`



第一行验证了 `findByUsername` 方法恰好被调用了一次。如果未被调用，`verify` 方法会抛出错误，导致测试失败。第二行我们重置了模拟对象。你可能会注意到 `reset` 方法上方的注释。重置模拟对象通常是不良实践。通常，你会在单个测试方法的范围内创建模拟对象：创建它、编程设定行为、验证调用，然后结束。在下一个测试方法中，你会创建模拟对象的新实例。然而，在我们的场景中，`AccountRepository` 是通过 Spring 自动注入到 `AccountService` 中的。当我们创建模拟对象的新实例时，需要找到一种干净的方式将其注入到 `AccountService` 中。是的，另一种选择是为 `AccountService` 添加一个额外的（`AccountRepository`）setter 方法，这样我们就可以通过 setter 来设置 `AccountRepository`。这取决于个人偏好。无论如何，通过这种方式重置模拟对象并没有违反任何规则。只需确保不要在测试中途重置模拟对象即可。例如，如果出现这种情况，你可能应该创建两个测试方法。

![Image](img/square.jpg) **注意** 你也可以像使用 JUnit 那样，对 Mockito 的方法使用静态导入。这实际上是一种推荐做法，因为它能使代码保持整洁和可读。在代码示例中，我们特意保留了类名可见，以明确表明这是 Mockito 对象而非 JUnit 对象（如果你刚接触 Mockito 或模拟对象，这一点可能不太明显）。

清单 9-11 展示了完整的测试用例。

***清单 9-11.** 完整的测试用例*

`package com.apress.prospringmvc.bookstore.service;`

`// 其他导入已省略，参见清单 9-7`

`import static junit.framework.Assert.assertEquals;`

`import org.mockito.Mockito;`
`import org.mockito.internal.verification.VerificationModeFactory;`
`import org.springframework.beans.factory.annotation.Autowired;`
`import org.springframework.context.annotation.Bean;`
`import org.springframework.context.annotation.Configuration;`

`@RunWith(SpringJUnit4ClassRunner.class)`
`@ContextConfiguration`
`public class AccountServiceTest {`

`    @Autowired`
`    private AccountService accountService;`
`    @Autowired`
`    private AccountRepository accountRepository;`

`    @Before`
`    public void setup() {`
`        Account account = new AccountBuilder() {`
`            {`
`                address("Herve", "4650", "Rue de la gare", "1", null,`![Image](img/U002.jpg) `"Belgium");`
`                credentials("john", "secret");`
`                name("John", "Doe");`
`            }`
`        }.build(true);`
`        Mockito.when(accountRepository.findByUsername("john"))`
`               .thenReturn(account);`
`    }`

`    @After`
`    public void verify() {`
`        Mockito.verify(accountRepository,`![Image](img/U002.jpg)
`                        VerificationModeFactory.times(1))                         .findByUsername(Mockito.anyString());`
`        // 此处允许：使用容器注入的模拟对象`
`        Mockito.reset(accountRepository);`
`    }`

`    @Test(expected = AuthenticationException.class)`
`    public void testLoginFailure() throws AuthenticationException {`
`        accountService.login("john", "fail");`
`    }`

`    @Test()`
`    public void testLoginSuccess() throws AuthenticationException {`
`        Account account = accountService.login("john", "secret");`
`        assertEquals("John", account.getFirstName());`
`        assertEquals("Doe", account.getLastName());`
`    }`

`    @Configuration`
`    static class AccountServiceTestContextConfiguration {`

`        @Bean`
`        public AccountService accountService() {`
`            return new AccountServiceImpl();`
`        }`

`        @Bean`
`        public AccountRepository accountRepository() {`
`            return Mockito.mock(AccountRepository.class);`
`        }`
`    }`
`}`

至此，我们完成了使用模拟的 `AccountRepository` 对 `AccountService` 的测试。我们希望已经向你展示了使用 Mockito 设置和验证模拟对象是多么简单，以及它们在隔离待测试逻辑方面是多么有用。还有许多其他功能可以帮助你设置所需的模拟环境。这些内容超出了本章的范围，因此我们建议你访问 Mockito 网站，这是一个很好的起点。

### 测试你的 MVC 逻辑

如图 9-9 所示，Spring MVC 由我们的视图层（JSP）、控制器（Spring 内部代码）、模型以及我们的应用控制器组成。重要的是，我们首先要决定这些组件中哪些应该实际测试，哪些不应该。

要测试 JSP，你需要一个正在运行的 Servlet 容器。这将是系统测试的一部分，我们将在本章后面的“自动化前端测试”部分进行测试。我们不需要测试控制器，因为这是 Spring 框架的内部代码。我们之前已经解释了如何使用单元测试和集成测试来测试领域层，因此这些已经测试过了。唯一剩下需要测试的就是我们的应用控制器。

![Image](img/9781430241553_Fig09-09.jpg)

***图 9-9.** MVC 架构中的组件*

`com.apress.prospringmvc.bookstore.web.controller.LoginController` 是我们将要测试的应用控制器，更具体地说是 `handleLogin` 方法。我们的首要建议是尽可能限制应用控制器内部的逻辑。例如，当你有大量的验证逻辑时，请将这些代码放在单独的类中。这样，它们就成为了独立的组件，无需依赖应用控制器即可轻松测试。如果你始终如一地应用这条规则，你将能够使用纯单元测试测试前端 90%（或更多）的代码。这样，应用控制器将仅作为控制器框架与这些独立（易于测试）的类以及你的模型之间的薄薄一层。


#### 使用 Spring Mock 对象

当需要测试应用程序控制器时，可以使用 Spring 提供的辅助工具：`org.springframework.test.web.ModelAndViewAssert` 结合 `MockHttpServletRequest`、`MockHttpServletResponse` 和 `MockHttpSession`（所有这些都位于 `org.springframework.mock.web` 中）。虽然它们被命名为“Mock”，但根据我们对术语的定义，它们更像是桩（stub）。例如，你可以通过 `MockHttpServletRequest` 向 `MockHttpSession` 添加属性，并在之后检索它们（`MockHttpServletRequest` 内部使用 `MockHttpSession` 作为其会话桩）。根据你设置或执行的操作，某些 `MockHttpServletRequest` 方法在调用时可能会有不同的反应（它还包含一些内部逻辑，以尽可能模拟 `HttpServletRequest`）。我们将首先展示 代码清单 9-12 中的 handleLogin 方法，以便你轻松理解我们正在测试的内容。

***代码清单 9-12.** LoginController 中的 handleLogin 方法*

`@RequestMapping(method = RequestMethod.POST)`
`public String handleLogin(@RequestParam String username, @RequestParam String`![Image](img/U002.jpg)
`                          password, HttpSession session)`![Image](img/U002.jpg)
`                          throws AuthenticationException {`
`    Account account = this.accountService.login(username, password);`
`    session.setAttribute(ACCOUNT_ATTRIBUTE, account);`
`    String url = (String) session.getAttribute(REQUESTED_URL);`
`    session.removeAttribute(REQUESTED_URL); // 移除该属性`

`    if (StringUtils.hasText(url) && !url.contains("login")) {`![Image](img/U002.jpg)
`        // 防止登录页面出现循环。`
`        return "redirect:" + url;`
`    } else {`
`        return "redirect:/index.htm";`
`    }`
`}`

代码清单 9-12 中的方法负责处理来自登录页面的登录操作。它接受从登录表单提交的用户名和密码。然后，它将使用 `AccountService` 对用户进行身份验证，并将结果（一个 Account 对象）以常量 `ACCOUNT_ATTRIBUTE` 作为键放入 HTTP 会话中。你可以在第 6 章的“实现 HandlerInterceptor”部分看到定义的 `SecurityHandlerInterceptor`。

接下来，我们展示测试用例（参见代码清单 9-13），然后讨论一些特定的代码片段。

***代码清单 9-13.** LoginController 的 handleLogin 方法测试*

`package com.apress.prospringmvc.bookstore.web.controller;`

`// 其他导入已省略，参见代码清单 9-7`

`import static junit.framework.Assert.assertEquals;`
`import static junit.framework.Assert.assertNotNull;`
`import static junit.framework.Assert.assertNull;`

`import org.mockito.Mockito;`
`import org.mockito.internal.verification.VerificationModeFactory;`
`import org.springframework.beans.factory.annotation.Autowired;`
`import org.springframework.context.annotation.Bean;`
`import org.springframework.context.annotation.Configuration;`
`import org.springframework.mock.web.MockHttpServletRequest;`

`import com.apress.prospringmvc.bookstore.service.AccountService;`
`import com.apress.prospringmvc.bookstore.service.AuthenticationException;`
`import com.apress.prospringmvc.bookstore.web.interceptor.`![Image](img/U002.jpg)
`       SecurityHandlerInterceptor;`

`@RunWith(SpringJUnit4ClassRunner.class)`
`@ContextConfiguration`
`public class LoginControllerTest {`

`    @Autowired`
`    private LoginController loginController;`
`    @Autowired`
`    private AccountService accountService;`

`    @Before`
`    public void setup() throws AuthenticationException {`
`        Account account = new AccountBuilder() {`
`            {`
`address("Herve", "4650", "Rue de la station", "1", null,` ![Image](img/U002.jpg)
`                        "Belgium");`
`                credentials("john", "secret");`
`                name("John", "Doe");`
`            }`
`        }.build(true);`

`Mockito.when(accountService.login("john",` ![Image](img/U002.jpg)
`                     "secret")).thenReturn(account);`
`    }`

`    @After`
`    public void verify() throws AuthenticationException {`
`        Mockito.verify(accountService,`![Image](img/U002.jpg)
`         VerificationModeFactory.times(3)).login("john", "secret");`
`        Mockito.reset();`
`    }`

`    @Test`
`    public void testHandleLogin() throws AuthenticationException {`

**`MockHttpServletRequest mockHttpServletRequest =` ![Image](img/U002.jpg)**
**`new MockHttpServletRequest();`**
**`mockHttpServletRequest.getSession().setAttribute(`![Image](img/U002.jpg)**
**`SecurityHandlerInterceptor.REQUESTED_URL, "someUrl");`**

`String view = loginController.handleLogin("john", "secret",` ![Image](img/U002.jpg)
`           mockHttpServletRequest);`

**`Account account = (Account)`![Image](img/U002.jpg)**
**`mockHttpServletRequest.getSession().getAttribute(`![Image](img/U002.jpg)**
**`SecurityHandlerInterceptor.ACCOUNT_ATTRIBUTE);`**

`        assertNotNull(account);`
`        assertEquals("John", account.getFirstName());`
`        assertEquals("Doe", account.getLastName());`
`        assertNull(mockHttpServletRequest.getSession()`![Image](img/U002.jpg)
`                   .getAttribute(SecurityHandlerInterceptor.REQUESTED_URL));`
`        assertEquals("redirect:someUrl", view);`

`        // 测试不同的视图选择`
`        mockHttpServletRequest = new MockHttpServletRequest();`
`view = loginController.handleLogin("john", "secret",` ![Image](img/U002.jpg)
`               mockHttpServletRequest);`
`        assertEquals("redirect:/index.htm", view);`

`        mockHttpServletRequest = new MockHttpServletRequest();`
`        mockHttpServletRequest.getSession().setAttribute(`![Image](img/U002.jpg)
`           SecurityHandlerInterceptor.REQUESTED_URL, "abclogindef");`
`view = loginController.handleLogin("john", "secret",` ![Image](img/U002.jpg)
`           mockHttpServletRequest);`
`        assertEquals("redirect:/index.htm", view);`
`    }`

`    @Configuration`
`    static class LoginControllerTestConfiguration {`

`        @Bean`
`        public AccountService accountService() {`
`            return Mockito.mock(AccountService.class);`
`        }`

`        @Bean`
`        public LoginController loginController() {`
`            return new LoginController();`
`        }`
`    }`
`}`

如你所见，我们使用模拟（mocking）来模拟 `AccountService`，方式与测试 `AccountService` 时模拟 `AccountRepository` 相同，因此这应该已经是你熟悉的内容。唯一值得注意的新元素是使用了 `MockHttpServletRequest`。首先，我们使用 `MockHttpServletRequest` 设置一个会话属性，`LoginController` 将使用该属性来决定重定向到哪个视图：

`mockHttpServletRequest.getSession().setAttribute(SecurityHandlerInterceptor.REQUESTED_URL,`![Image](img/U002.jpg)
` "someUrl");`

这实际上是用户在重定向到登录页面之前所请求的 URL。得益于这个桩（stub），`LoginController` 能够从 `MockHttpSession`（通过 `MockHttpServletRequest`）检索到这个值并执行其逻辑，我们稍后将通过以下代码进行验证：

`assertEquals("redirect:someUrl", view);`

当用户成功通过身份验证后，`LoginController` 还会在会话中放置一个属性。这是一个以 `Account` 对象为值的 `account` 属性。这里我们验证确实如此：

`Account account = (Account) mockHttpServletRequest.getSession().getAttribute(`![Image](img/U002.jpg)
`                SecurityHandlerInterceptor.ACCOUNT_ATTRIBUTE);`



在最后两个测试中（注释下方：`// Test the different view selection choices`），我们测试了当 `MockHttpServletRequest` 上未设置初始 `REQUESTED_URL` 时，代码的反应。第一个测试仅测试了完全没有值的情况。最后一个测试测试了包含 `login` 的值。在这两种情况下，我们都验证了渲染的视图是索引（首页）页面。

#### 引入 Spring MVC Test

测试应用程序控制器的另一种选择是使用全新的 Spring MVC `test` 模块。^(13) 在撰写本文时，它尚未与 Spring 集成，但计划包含在 Spring 3.2 中。不过，已经有一个可用的 *snapshot* 版本，相当稳定（1.0.0 快照版）。

在继续之前，我们必须警告您，依赖快照版本（尤其是在没有先前发布版本的情况下）会带来一些风险，因为事情仍可能发生变化。因此，我们不会深入细节，仅介绍其思路和基本设置。事情可能会在不同地方发生变化，导致深入的内容在模块发布到 Spring 之前就过时了。然而，我们在此展示的思路应该保持稳定，并且在后续版本中仍然适用。

话虽如此，为了能够使用此模块，我们在第 9 章示例的 `build.gradle` 中添加了依赖项：

`testCompile 'org.springframework:spring-test-mvc:1.0.0.BUILD-SNAPSHOT'`

由于这是一个快照依赖项，我们还将快照仓库添加到了依赖管理配置的仓库列表中。我们在示例目录根目录下的 `build.gradle` 中完成了此操作：

`repositories {`
`    mavenRepo url: 'http://maven.springframework.org/release'`
**`mavenRepo url: 'http://maven.springframework.org/snapshot'`**
`    mavenCentral()`
`    mavenRepo url: 'http://maven.springframework.org/milestone'`
`    mavenRepo url: 'https://repository.jboss.org/nexus/content/repositories/releases/'`![Image](img/U002.jpg)
`    mavenRepo url: 'http://download.java.net/maven/glassfish/org/glassfish/'`
`}`

_________

¹³ [`https://github.com/SpringSource/spring-test-mvc`](https://github.com/SpringSource/spring-test-mvc)

Spring MVC `test` 旨在使您的应用程序控制器更易于测试，扩展了我们在上一节中使用的 `MockHttpServletRequest`、`MockHttpServletResponse` 和 `MockHttpSession` 辅助工具背后的理念。它试图更好地模拟 Spring MVC，而无需启动实际的 Servlet 容器。例如；使用 Spring MVC test，您将能够验证在使用 Tiles 作为视图解析器时，实际选择了哪些 JSP 进行渲染。这是以前不可能做到的事情，只能在运行时（可能使用前端测试）进行验证。我们能够使用单元测试或集成测试测试得越多，效果就越好。

我们将创建的示例将展示如何使用基础知识。第一步是引导一个 `org.springframework.test.web.server.MockMvc`。此对象初始化 MVC 基础设施，并将成为您进入 API 的主要入口点。您可以通过不同的方式初始化它：

> *   从一个或多个 MVC 注解配置类
> *   从一个或多个 Spring XML 文件
> *   使用 `WebApplicationContext`
> *   单独的 `@Controller` 类

前三种可能性是托管模式。在这些情况下，会启动一个应用程序上下文。处理您向其发起的请求所需的控制器将从应用程序上下文中查找。此外，MVC 环境将像在运行时一样进行配置，具有相同的视图解析器、资源处理器、拦截器等，就像您在配置中指定的一样。

最后一种可能性是所谓的独立模式。在此模式下，您直接提供应用程序控制器，并实例化最少的 Spring MVC 基础设施组件集——仅处理注册的应用程序控制器请求所需的那些组件。基础设施组件集与 MVC 命名空间或 MVC Java 配置提供的非常相似。需要注意的是，实际的 Spring MVC 配置将不会被测试（因为它从未被加载）。

首选的初始化方法是加载整个 Spring MVC 配置的方法。但是，如果您为每个测试都实例化 `MockMvc`，代价可能会很高。首选方式是使用第三种方法，即使用 `WebApplicationContext`，它将结合两方面的优点。`WebApplicationContext` 被注入到测试中。配置由 Spring 的测试支持照常加载。对于配置（在 `classes` 属性中提供），您将使用 Spring MVC 注解配置类。可能如下所示：

`@RunWith(SpringJUnit4ClassRunner.class)`
`@ContextConfiguration(classes = { ....})`
`public class ApplicationControllerTest {`

`  @Autowired`
`  private WebApplicationContext wac;`

这将加载您使用 `classes` 属性指定的 `@Configuration` 类，并启动一个 `WebApplicationContext`。然后，您将能够从注入的 `WebApplicationContext` 构建一个 `MockMvc` 对象，这将是廉价的。Spring 的测试支持将缓存其加载的上下文的初始化，这些上下文通过 `@ContextConfiguration` 指示，可能仅为整个测试套件加载一个实例。然而，目前 `WebApplicationContext` 支持尚不可用，尽管它将在 Spring 3.2 中开始提供。

在您初始化 `MockMvc` 对象（以某种方式）之后，您可以开始向其发送请求。它将使用实际的 Spring MVC 逻辑处理这些请求，并调用您的控制器。最后，您能够验证结果。在清单 9-14 中，我们使用 Spring MVC `test` 重写了 `LoginControllerTest`。为简单起见，我们以独立模式构建了 `MockMvc` 对象。让我们先看一下代码。

***清单 9-14.** 使用 Spring MVC test 重写 LoginController 测试*

`package com.apress.prospringmvc.bookstore.web.controller;`

`// 其他导入已省略，参见清单 9-7 和 9-13`

`import static org.springframework.test.web.server.request.`![Image](img/U002.jpg)
`              MockMvcRequestBuilders.post;`
`import static org.springframework.test.web.server.result.`![Image](img/U002.jpg)
`              MockMvcResultMatchers.redirectedUrl;`
`import static org.springframework.test.web.server.result.`![Image](img/U002.jpg)
`              MockMvcResultMatchers.request;`
`import static org.springframework.test.web.server.result.`![Image](img/U002.jpg)
`              MockMvcResultMatchers.status;`

`import org.springframework.test.web.server.MockMvc;`
`import org.springframework.test.web.server.setup.MockMvcBuilders;`

`@RunWith(SpringJUnit4ClassRunner.class)`
`@ContextConfiguration`
`public class SpringMvcTestLoginControllerTest {`

`    @Autowired`
`    private LoginController loginController;`

`    @Autowired`
`    private AccountService accountService;`

`    private Account account;`

`    @Before`
`    public void setup() throws Exception {`
`        account = new AccountBuilder() {`
`            {`
`                address("Herve", "4650", "Rue de la station", "1", null,`![Image](img/U002.jpg)
`                        "Belgium");`
`                credentials("john", "secret");`
`                name("John", "Doe");`
`            }`
`        }.build(true);`

`        Mockito.when(accountService.login("john",`![Image](img/U002.jpg)
`                     "secret")).thenReturn(account);`
`    }`



`    @Test`
`    public void testHandleLogin() throws Exception {`
`        MockMvc mockMvc = MockMvcBuilders.standaloneSetup(loginController)`![Image](img/U002.jpg)
`                          .build();`
`        mockMvc.perform(post("/login")`![Image](img/U002.jpg)
`               .param("username", "john")`
`               .param("password", "secret"))`
`               .andExpect(status().isOk())`
`               .andExpect(request().sessionAttribute(`![Image](img/U002.jpg)
`                       SecurityHandlerInterceptor.ACCOUNT_ATTRIBUTE, account))`
`               .andExpect(redirectedUrl("/index.htm"));`
`    }`

`    @After`
`    public void verify() throws AuthenticationException {`
`        Mockito.verify(accountService, VerificationModeFactory.times(1))`![Image](img/U002.jpg)
`               .login("john", "secret");`
`        Mockito.reset();`
`    }`

`    @Configuration`
`    static class LoginControllerTestConfiguration {`

`        @Bean`
`        public AccountService accountService() {`
`            return Mockito.mock(AccountService.class);`
`        }`

`        @Bean`
`        public LoginController loginController() {`
`            return new LoginController();`
`        }`
`    }`
`}`

初始设置保持不变，与 `LoginControllerTest` 中的设置相同。例如，我们仍然使用 Mockito 来模拟 `AccountService`，并且仍然通过 `LoginControllerTestConfiguration` 类使用本地 Spring 配置来注入 `LoginController`。

我们做的第一件事是以独立模式构建 MockMvc 对象。我们通过将 `LoginController` 作为构造函数参数提供给它来实现这一点：

`MockMvc mockMvc = MockMvcBuilders.standaloneSetup(loginController)`![Image](img/U002.jpg)
`                          .build();`

`LoginController` 的测试相当直接，因此独立模式加载的所有默认设置就足够了。但是，如果需要，你可以在调用 `build` 之前应用自定义设置。例如，可以像这样设置自定义视图解析器：

`MockMvc mockMvc = MockMvcBuilders`
`                  .standaloneSetup(loginController)`
`                  .setViewResolvers(viewResolver)`
`                  .build();`

当上下文初始化后，我们可以让它处理一个请求。我们的 `LoginController` 只接受 HTTP `post` 请求，因此我们构建一个带有两个参数的 `post` 请求：

`mockMvc.perform(post("/login")`
`    .param("username", "john")`
`    .param("password", "secret"))`

诸如 `post` 和 `param` 之类的方法是通过类顶部的静态导入引入的（参见清单 9-14）。这里我们特意保留了这一点，以向你展示应用此策略后语法变得多么简洁。不过，刚开始时，需要一些时间来适应各种可用的方法。最后，我们验证结果。我们检查 HTTP 状态码是否为 200（`isOk`），账户是否已在会话中设置，以及我们是否被重定向到 index.htm 页面：

`.andExpect(status().isOk())`
`.andExpect(request().sessionAttribute(`![Image](img/U002.jpg)
`        SecurityHandlerInterceptor.ACCOUNT_ATTRIBUTE, account))`
`.andExpect(redirectedUrl("/index.htm"));`

这看起来相当不错。你无需为 `request`、`response` 和 `session` 创建单独的模拟对象，因为这一切都由 Spring MVC `test` 模块处理。如果你将此与我们之前的测试用例 `LoginControllerTest` 进行比较，你会立即发现这段代码看起来好得多，更简洁，而且最重要的是，它测试了包括 Spring MVC 注解在内的整个控制器。

顺便提一下，在托管模式下构建一个 `MockMvc`，使其实际加载基于 `@Configuration` 类的配置，并没有太大不同，看起来如下：

`MockMvc mockMvc = MockMvcBuilders`
`.annotationConfigSetup(WebMvcContextConfiguration.class,` ![Image](img/U002.jpg)
`InfrastructureContextConfiguration.class,` ![Image](img/U002.jpg)
`                                     TestDataContextConfiguration.class,            `![Image](img/U002.jpg)
`                                     LoginControllerTestConfiguration.class)`
`               .configureWebAppRootDir("src/main/webapp", false)`
`               .build();`

至此，我们对 Spring MVC `test` 的简短介绍就结束了。我们希望我们向你展示的东西，能成为你以最少工作量测试 Spring MVC 应用控制器的强大工具。我们期待它能够完全集成到 Spring 中！

### 自动化前端测试

自动化前端测试是单元测试拼图中的最后一块。它帮助我们测试最后一个未经测试的组件——我们的视图。JSP 可以像所有其他组件一样，以自动化方式进行测试。前端测试有多种形式。例如，你可以在不同的浏览器上测试你的 Web 应用，以检查外观和感觉是否相似。你还可以测试应用的性能、渲染时间和可用性。所有这些测试可能都在其他测试阶段使用外部工具进行。

我们这里追求的测试是确保我们所有的页面都能渲染，并且它们提供的所有功能都能正常工作。仅此而已。如果你已经很好地测试了所有之前的组件，这应该是轻而易举的事。请确保你已：

> *   为前端测试设置了*良好*的基础设施。这需要一个正在运行的容器、数据设置，以及可能针对某些资源和功能的模拟或桩。这些测试也需要（像任何单元测试一样）通过你的持续集成系统自动运行，这意味着它们应该全部集成到你的构建系统中。
>     
>     
>     
>     
>     这并不像看起来那么容易，因为你有一个小问题：你需要启动一个可以运行你的应用的 Servlet 容器（参见图 9-10）。Gradle（和 Maven）有一些插件，比如 Cargo，^(14)，它们可以在运行这些测试之前启动一个容器并部署你的应用。
>     
>     ![Image](img/9781430241553_Fig09-10.jpg)
>     
>     ***图 9-10**. 针对 Tomcat 运行前端测试*
>     
>     
> *   设计好了你的视图。通常，你在设计视图时已经手动测试过它们。因此，通常当每一层都经过良好测试时，前端测试应该只会发现一些小错误。
>     
>     
>     
>     
>     最重要的是，它们强化了你的“测试工具”。即使你忘记在服务中测试某些内容，它也可能在你的某个前端测试中得到测试。此外，如果以后有人重构了你的视图，你仍然拥有测试工具来确保你的页面仍然正常工作。

为了设置前端测试，我们选择了 Selenium。^(15) 它可以在两种模式下运行：使用像 Firefox 这样的真实浏览器，以及使用 HtmlUnit（一种内存浏览器）。

HtmlUnit 能够运行 JavaScript 和处理 AJAX。这一切都可以完全在内存中运行，并且没有任何外部依赖，因此它可以被视为一种集成测试。



#### 使用 Selenium 进行前端测试

我们将使用的当前 Selenium 版本是 Selenium 2.x（此后将 Selenium 2.x 简称为“Selenium”）。Selenium 利用了所谓的 WebDriver 项目。这使得 Selenium 能够直接对所选浏览器运行测试，而无需额外软件。之前的版本（Selenium 1.x）则不同，它要求在测试用例和浏览器之间运行一个所谓的 Selenium RC 服务器。你可以在图 9-11 中看到这一点。不过，Selenium RC 服务器仍然可以与当前版本的 Selenium 结合使用。为了解释这一点，我们首先来看一下 Selenium 1.x 的工作原理。

_________

¹⁴ [`http://cargo.codehaus.org/`](http://cargo.codehaus.org/)

¹⁵ [`http://seleniumhq.org/`](http://seleniumhq.org/)

![Image](img/9781430241553_Fig09-11.jpg)

***图 9-11.** 带有 Selenium RC 服务器设置的 Selenium 1.x*

在 Selenium 1.x 中，测试用例与 Selenium RC 服务器之间的通信通过网络进行（你的测试命令被转换为发送到 Selenium RC 服务器的 HTTP 请求）。Selenium RC 服务器可能运行在与实际测试运行不同的机器上。然后，Selenium RC 服务器将通过 HTTP 接收到的命令转换为 JavaScript 调用，并将其注入目标浏览器。这是一个复杂的过程，但对用户来说是透明的。虽然这种设置已被视为过时，但了解其工作原理可能仍然有趣。你可以在 Selenium 文档页面^(16)上阅读更多相关信息。

Selenium 现在提供了一种不同的方法，即使用 WebDriver，它通过浏览器的原生 API 直接控制浏览器。因此，它不再需要像 Selenium 1.x 使用的 Selenium RC 那样复杂的（内部）设置。使用 Selenium，你可以直接控制浏览器，而无需 Selenium RC 服务器。你只需根据要使用的浏览器，通过 API 选择合适的驱动程序即可。

但是，你仍然可以使用 RC 服务器。在这种情况下，RC 服务器也会使用 WebDriver。你的测试不再直接使用 WebDriver 控制本地机器上的浏览器，而是指示运行在另一台机器上的 RC 服务器使用 WebDriver 与本地浏览器通信。Selenium RC 服务器设置与 Selenium 1.x 的主要区别在于，Selenium RC 服务器做的事情远不止这些；它还注入 JavaScript 并充当代理，而使用 WebDriver 时这些已不再需要。它仅作为一个“远程 JVM”，使你能够控制远程机器上的浏览器。

_________

¹⁶ [`http://seleniumhq.org/docs/05_selenium_rc.html#selenium-rc-architecture`](http://seleniumhq.org/docs/05_selenium_rc.html#selenium-rc-architecture)

![Image](img/square.jpg) **提示** 使用 Selenium RC 服务器特别适用于那些你想在机器 A 上运行测试但使用机器 B 浏览器的情况。例如，你可以在你的 CI（一个 Unix 类型机器）上运行测试。你想针对 IE 7、8 和 9 运行前端测试。为此，你可以设置三台虚拟机，每台安装 Windows 和其中一种浏览器，并运行一个 Selenium RC 服务器。然后你可以运行测试套件三次。第一次使用运行 IE 7 的虚拟机的 IP 地址，第二次使用运行 IE 8 的虚拟机的 IP 地址，依此类推。你可以创建 Spring 测试配置文件，根据运行测试的配置文件动态注入不同的 IP 地址。

#### 编写 Selenium 测试

现在我们已经了解了一些关于 Selenium 及其前身 Selenium 1.x 的理论，让我们看看如何实际使用它。在开始之前，我们必须在 `build.gradle` 中添加其依赖项。我们将把它们添加到 chapter9-bookstore 示例的 gradle 构建文件中。

`testCompile 'org.seleniumhq.selenium:selenium-java:2.20.0'`
`testCompile 'org.seleniumhq.selenium:selenium-firefox-driver:2.20.0'`
`testCompile 'org.seleniumhq.selenium:selenium-htmlunit-driver:2.20.0'`

我们添加了核心依赖项（`selenium-java`）以及 Firefox 和 HtmlUnit 的 WebDriver 依赖项。其他驱动程序^(17)目前包括 `selenium-android-driver`、`selenium-chrome-driver`、`selenium-ie-driver`、`selenium-iphone-driver` 和 `selenium-remote-driver`。接下来，让我们看看清单 9-15 中针对 LoginController 的 Selenium 测试。

***清单 9-15.** 使用 Selenium 测试登录序列*

`package com.apress.prospringmvc.bookstore.web.frontend;`

`// 其他导入已省略，参见清单 9-7`

`import static org.junit.Assert.assertEquals;`

`import org.openqa.selenium.By;`
`import org.openqa.selenium.WebDriver;`
`import org.openqa.selenium.firefox.FirefoxDriver;`

`public class SeleniumLoginFrontendTest {`

`    private WebDriver browser;`

`    @Before`
`    public void setup() {`
`        browser = new FirefoxDriver();`
`    }`

`    @Test`
`    public void startTest() {`
`        browser.get("http://localhost:8080/chapter9-bookstore/");`

`        browser.findElement(By.id("login")).click();`

`        // 如果找不到元素将抛出异常`
`        browser.findElement(By.id("username")).sendKeys("jd");`
`        browser.findElement(By.id("password")).sendKeys("secret");`

`        browser.findElement(By.id("loginButton")).click();`
`        browser.findElement(By.id("account")).click();`

`        assertEquals("John", browser.findElement(`![Image](img/U002.jpg)
`                             By.id("firstName")).getAttribute("value"));`
`    }`

`    @After`
`    public void tearDown() {`
`        browser.close();`
`    }`
`}`

_________

¹⁷ [`http://repo1.maven.org/maven2/org/seleniumhq/selenium/`](http://repo1.maven.org/maven2/org/seleniumhq/selenium/)

在 `setup` 方法中，我们启动了 `browser`，它是在测试用例中控制浏览器的实际 API。这里我们通过 FirefoxDriver 使用 Firefox：

`browser = new FirefoxDriver();`

如果我们想切换到内存浏览器（HtmlUnit），所有代码将保持不变。我们只需将 `FirefoxDriver` 替换为 `HtmlUnitDriver`：

`browser = new HtmlUnitDriver();`

运行此代码将针对内存浏览器执行测试。像这样运行测试时，不会有浏览器弹出。要了解支持哪些浏览器及其特性，最佳资源是 Selenium 文档。^(18)

_________

¹⁸ [`http://seleniumhq.org/docs/03_webdriver.html#introducing-webdriver-s-drivers`](http://seleniumhq.org/docs/03_webdriver.html#introducing-webdriver-s-drivers)

![Image](img/square.jpg) **注意** 如果你想将 Selenium RC 服务器与 WebDriver 一起使用，可以使用 `org.openqa.selenium.remote.RemoteWebDriver`。在目标机器上，你需要运行 Selenium RC 服务器才能使其工作。你可以使用 `org.openqa.selenium.remote.DesiredCapabilities` 类进一步配置驱动程序。例如：`browser = new RemoteWebDriver(new URL("remoteHostRunningSeleniumRC:4444"), DesiredCapabilities.firefox());` 这告诉 Selenium 我们将使用 `RemoteWebDriver`，并且它应该连接到运行在主机 `remoteHostRunningSeleniumRC` 的 4444 端口上的 Selenium RC 服务器。它还告诉远程实例，它应该针对 Firefox 运行测试（然后将在远程机器上启动 Firefox）。



为了继续概述测试类，我们现在将进入实际的测试方法 `startTest`。测试方法的第一行告诉浏览器要打开哪个 URL：

`browser.get("http://localhost:8080/chapter9-bookstore/");`

接下来的几行代码负责导航（点击链接、输入文本等）。当我们点击链接或按钮时，我们使用的是 ID。不过，你也可以使用更高级的选择器，比如 CSS 和 XPath。你可以在 Selenium 参考指南中找到更多相关信息。^(19)

#### 使用 Selenium IDE

回顾清单 9-15，你会发现使用浏览器进行导航需要查看 JSP 源代码并检查你想要使用的元素的标识符。例如，要点击一个按钮，你必须知道它的 ID，然后编写这行代码：

`browser.findElement(By.id("login")).click();`

在这个例子中，我们点击了一个 ID 为 `"login"` 的按钮。我们的场景非常简短，但对于更长的场景来说，这可能会变得耗时且相当乏味。幸运的是，有一个名为 Selenium IDE 的强大工具，它可以为你生成这些代码。它不仅能生成 Java 代码，还能生成其他语言的代码，我们稍后会看到。Selenium IDE 是 Firefox 的一个插件。根据 Selenium 官网的说法，^(20) Selenium IDE 目前仅在 Firefox 中运行。要下载 Selenium IDE 插件，请访问 Selenium 官网^(21)并按照说明操作。你基本上只需点击下载链接，Firefox 就会询问你是否要安装该软件，如图 9-12 所示。

_________

¹⁹ [`http://seleniumhq.org/docs/03_webdriver.html#locating-ui-elements-webelements`](http://seleniumhq.org/docs/03_webdriver.html#locating-ui-elements-webelements)

²⁰ [`http://seleniumhq.org/about/platforms.html#browsers`](http://seleniumhq.org/about/platforms.html#browsers)

²¹ [`http://seleniumhq.org/download/`](http://seleniumhq.org/download/)

![Image](img/9781430241553_Fig09-12.jpg)

***图 9-12.** 为 Firefox 安装 Selenium IDE*

点击“允许”后，你会看到如图 9-13 所示的界面，请求你的安装许可。

![Image](img/9781430241553_Fig09-13.jpg)

***图 9-13.** 为 Firefox 安装 Selenium IDE*

安装完成后，你需要重启 Firefox。然后，你可以通过“工具”菜单选择 Selenium IDE 来打开它。之后，应该会出现一个单独的窗口，如图 9-14 所示。

![Image](img/9781430241553_Fig09-14.jpg)

***图 9-14.** Selenium IDE 主窗口*

Selenium IDE 会检查你在 Firefox 页面上的交互操作，并“记录”所有事件，例如点击按钮、输入文本等。要进行设置，你需要在“基础 URL”文本字段中添加你想要记录的应用程序（参见图 9-15）。接下来，切换回 Firefox 并导航到你想要捕获场景的应用程序。在我们的例子中，我们将 Firefox 导航到 [`http://localhost:8080/chapter9-bookstore`](http://localhost:8080/chapter9-bookstore)。当你进入 chapter9-bookstore 应用程序的主页后，切换回 Selenium IDE 窗口并点击“录制”按钮，该按钮是右上方、基础 URL 输入框下方的一个红色圆形按钮（它看起来像一个“指示灯”，但实际上是一个可以点击的按钮）。点击它之后，Selenium 将进入录制模式。现在你可以回到浏览器并执行一个测试场景。我们将执行与测试中相同的操作。因此，我们将点击“登录”链接，输入用户名和密码，点击“登录”，然后点击“账户”链接。之后，你可以回到 Selenium IDE 窗口，你会看到“表格”选项卡中出现了几行记录，如图 9-15 所示。

![Image](img/9781430241553_Fig09-15.jpg)

***图 9-15.** Selenium 录制一个场景*

你可以再次点击“录制”按钮来禁用录制。现在，你可以将捕获的交互导出为特定语言的 Selenium 命令。支持的语言包括 Ruby、C#、Python，当然还有 Java。首先，我们选择要导出的语言；我们将选择 JUnit 4 with WebDriver，如图 9-16 所示。

![Image](img/9781430241553_Fig09-16.jpg)

***图 9-16.** 选择导出场景的语言*

现在，通过选中表格列表中的所有行，右键点击并选择“复制”，来复制所有行，如图 9-17 所示。

![Image](img/9781430241553_Fig09-17.jpg)

***图 9-17**. 将场景复制到剪贴板*

复制后，你可以直接将其粘贴到你的测试类文件中。该场景将立即转换为所需的格式，如清单 9-16 所示。

***清单 9-16.** 由 Selenium IDE 生成的 Selenium WebDriver JUnit 风格场景*

`driver.get(baseUrl + "/chapter9-bookstore/index.htm");`
`driver.findElement(By.id("login")).click();`
`driver.findElement(By.id("username")).clear();`
`driver.findElement(By.id("username")).sendKeys("jd");`
`driver.findElement(By.id("password")).clear();`
`driver.findElement(By.id("password")).sendKeys("secret");`
`driver.findElement(By.id("loginButton")).click();`
`driver.findElement(By.id("account")).click();`

这几乎与我们在示例中自己编写的代码相同。唯一的区别是，Selenium 在向字段添加文本之前，会显式地清除这些字段。

除了使用插件从录制的场景生成代码外，你还可以使用插件重放场景。更多信息，我们建议访问 Selenium IDE 文档页面。^(22)



### 通过 Gradle 运行前端测试

除了常规的单元测试和集成测试，我们还希望通过构建工具来运行前端测试。当我们使用 Gradle 执行常规构建（命令为 `gradle build`）时，前端测试并不会运行。为此，我们创建了一个名为 `systemTest` 的独立任务，专门用于运行前端测试。`systemTest` 命令利用 Cargo 插件将应用程序部署到 Tomcat，随后会运行所有以 `FrontendTest` 结尾的测试。`systemTest` 任务在根目录的 `build.gradle` 文件中定义如下：

`task systemTest(type: Test) {`
`    include '**/*FrontendTest.*'`
`    dependsOn(war)`
`    doFirst {`
`        cargoStartLocal.execute()`
`    }`
`}`

`systemTest` 任务包含了所有以 `FrontendTest` 结尾的类。它依赖于 `war` 任务，这意味着 Gradle 会先构建项目的 war 包。最后，任务会执行 `cargoStartLocal`，该任务之所以可用，是因为我们启用了 Cargo 插件。此任务会启动 Tomcat 并部署由 `war` 任务预先生成的 war 文件。

为了将这些 `FrontendTest` 排除在常规的 Gradle 测试生命周期之外，我们对 `test` 任务进行了扩展，专门排除了这些文件：

`test {`
`    exclude '**/*FrontendTest.*'`
`}`

运行 `FrontendTest` 非常简单：打开命令行界面，导航至第 9 章的示例目录，然后输入：

_________

²² [`http://seleniumhq.org/docs/02_selenium_ide.html`](http://seleniumhq.org/docs/02_selenium_ide.html)

`gradle systemTest`

为了使系统测试正常运行，你的系统上需要安装 Firefox。由于应用程序需要部署到 Tomcat，测试会花费一些时间。在测试过程中，你会看到 Firefox 打开和关闭，这是 Selenium 测试在运行。最后，你应该会看到“构建成功”的确认信息。如果你进入第 9 章目录下的 `build/reports/test` 文件夹，并打开 `index.html`，就可以看到测试报告（参见图 9-18），其中显示两个测试均已运行。

![图片](img/9781430241553_Fig09-18.jpg)

***图 9-18.** 测试结果报告*

![图片](img/square.jpg) **注意** 有报告指出，当前版本的 Selenium 与最新版本的 Firefox（撰写本文时为版本 12）存在兼容性问题。然而，该代码是在 Firefox 仍为版本 11 时开发和测试的。如果你在运行系统测试时遇到问题，请使用 Firefox 11，或查看 Selenium 网站以获取修复此问题的新版本（预计很快会发布）。

### 本章小结

在本章中，我们认识到认真思考如何在项目中处理测试是非常重要的。显然，一个项目需要充分的测试才能得以成长并保持可维护性。你的测试应与实际生产代码同等重要。我们介绍了一些指导原则：确保在正确的层级进行测试，以及如何保证测试在你的项目中保持吸引力，因为这是确保测试得到充分编写的关键要素之一。

我们还解释了不同层级的测试。我们从基本的单元测试开始，接着介绍了 Spring 的测试支持，并利用它编写了集成测试，通过内存数据库测试了我们的仓库。在 MVC 方面，我们展示了如何使用 Spring 的测试支持和全新的 Spring MVC 测试来测试应用程序控制器。有了这两个工具，你现在可以进一步提高整体代码覆盖率。

最后，我们介绍了一个完整的前端测试，它测试了我们的视图，同时也间接测试了其背后的所有逻辑。我们向你展示了使用真实浏览器（Selenium）创建系统测试的可能性，但也可以将其配置为使用内存浏览器（HtmlUnit），从而使其成为集成测试。

我们希望这里介绍的理念和技术能帮助你编写更多、更好的测试。剩下的唯一事情就是去实践这些测试策略！

## 第 10 章

## Spring Web Flow

在构建可扩展、易于开发和维护的 Web 应用程序方面，Spring Web Flow 2（下文简称 Web Flow）是一个重要的组成部分。在接下来的三章中，我们将带你熟悉 Web Flow 及其优缺点；我们还将讨论为什么、何时以及最重要的是如何使用它。在本章中，我们首先解释什么是 Web Flow 以及它能为你做什么。之后，我们将讨论一些基本的 Web Flow 元素，这些是你开始构建第一个流程之前需要理解的。

贯穿 Web Flow 各章的主线是一个示例应用程序，我们将随着这些章节的推进逐步创建它。在本章中，我们将首先说明如何将一个普通的 MVC 示例通过少量配置转变为支持 Web Flow 的应用程序。接下来，我们将逐步为书店示例应用程序添加 Web Flow 功能，并沿途详细解释每个特性。我们特别留意确保你在阅读这些章节时无需手头有示例应用程序。所有相关代码都会展示并加以说明。

在本章中，我们将仅限于创建一个流程：创建（图书）订单流程。第 11 章和第 12 章将更详细地介绍该应用程序。在第 11 章中，我们将扩展示例应用程序的功能，并介绍一些更详细的配置选项。在第 12 章中，我们将让你熟悉一些更高级的方面，例如使用 AJAX、流程管理的持久化等。

如果你已经有一些 Web Flow 经验，可以快速浏览本章，然后进入下一章。如果你是 Web Flow 新手，或者想复习一下，那么这里就是最佳起点！

### 为什么选择 Web Flow

在 Web Flow 各章中，我们将从讨论 Web Flow 2.x 系列开始。我们始终将版本 1 的工具称为 *Web Flow 1*。否则，所有讨论的特性均与 Web Flow 2.x（撰写本文时为版本 2.3.1）相关。

你可能想知道为什么需要费心阅读关于 Web Flow 的内容。你尤其可能想知道为什么需要学习又一个框架。因为老实说：Java 领域非常广阔，Web 框架的选择极其丰富（你显然已经选择了 Spring MVC——很好！）。然而，在这些框架中，仍然有一些附加功能可以让你的工作更轻松。

我们说更轻松？嗯，是的。我们确实是这个意思。读完这三章后，我们将让你相信，使用 Web Flow 可以使 Web 应用程序的管理变得更加容易。让我们先从一些理论开始，这样我们就能说明 Web Flow 能为你做什么。希望这能让你产生足够的兴趣，读完所有三章 Web Flow 内容！



#### 流程概念

尽管 Web Flow 提供了众多特性和优势，但其核心始终围绕着“流程”（难怪它也被称为 Web *Flow*）。在深入探讨之前，我们先退一步，了解什么是流程以及它的设计模型。这将极大地帮助你理解 Web Flow 的目标以及它如何为你提供帮助。

流程是大多数应用程序中自然存在的事物（参见图 10-1）。一个基本的例子是向导。想象一个网店订单向导，它要求你按照几个步骤来下订单。这些步骤就构成了一个流程。你从第一步开始，填写一些身份信息，例如你的名和姓，然后点击“下一步”按钮。这会带你进入第二步，询问你将订单运送到哪里。第三步也是最后一步，允许你输入配送选项：运输类型、你是否希望包裹在某个日期之前送达（如果包裹在那之前未备齐，可能会分多次发货），等等。如果这个向导设计得不错，它应该允许你在步骤之间前后导航，并记住你在流程中为该特定步骤输入的状态（数据）。每个步骤都是流程中的一小部分。它们通过某种导航形式连接在一起。有时你还可以选择跳过某个步骤。在流程结束时，你可以完成你的流程。在我们的例子中，这将处理你的订单，并确保你的产品尽快送达。

![图片](img/9781430241553_Fig10-01.jpg)

***图 10-1.** 应用程序中的基本流程*

Web Flow 的全部意义就在于创建这些流程，你需要通过管理不同流程步骤之间的状态、表达导航等方式来实现。到目前为止，我们已经讨论了流程的核心方面；然而，流程还有一些其他巧妙的功能。我们接下来将讨论这些。

#### 细粒度作用域

Web Flow 的第一个主要优势是它增加了额外的作用域。毫无疑问，你已经注意到 Servlet 规范声明了三个作用域，你的应用程序可以在其中保留状态。

作用域不过是一个映射表，你可以在其中存储*键值对*，根据作用域类型的不同，这些键值对会存储一段确定的时间。Servlet 规范定义了三个作用域：应用程序（application）、会话（session）和请求（request）。

在表 10-1 中，我们将简要总结不同的作用域，并解释你应该如何使用它们。尽管对于任何做过 Java Web 开发的人来说，这都是一个非常基础的话题，但我们建议你快速浏览一下，以免遗漏任何内容。

![图片](img/9781430241553_tab10-01.jpg)

你可能会想，这与 Web Flow 有什么关系。让我们看一个使用我们书店 Web 应用程序的例子。就像任何在线商店一样，我们有一个订单和结账流程。在这个流程的第一步，用户可以选择一个类别。类别可以是 IT、科幻、惊悚等。在下一步中，可以从之前选择的类别中选择书籍。最后，用户将能够选择一些选项，例如配送详情。然而，每个步骤都在单独的页面上。在这个例子中，我们将有三个页面。当用户确认订单时，我们需要找到这些信息（我们在前三个页面中收集的），并据此创建订单。我们需要在这些不同步骤之间的某个地方存储这些数据；问题是我们应该把这些数据存储在哪里？让我们看看一些选项：

*   **应用程序作用域：** 显然，我们不希望用户 x 的信息对用户 y 可见。这不仅是不希望的，还会造成安全漏洞，使得一个用户的敏感数据可能对其他用户可见。
*   **请求作用域：** 这也是一个糟糕的选择，因为你无法跨多个页面存储数据；请求作用域在每次页面渲染后都会被销毁。你可以通过添加隐藏字段来将数据附带在页面上，但这通常是一个坏主意。虽然这种解决方案将数据卸载到客户端（在某些情况下可能被视为优点），但它也可能引发安全漏洞（如果你有一个内部状态，不应该对客户端可见，但又需要保留，该怎么办？），而且你必须在代码中手动管理它。简而言之，请求作用域的生命周期太短，无法优雅地满足我们的需求。
*   **会话作用域：** 好吧，我们别无选择！虽然这个作用域通常是这三个选项中最好的，但它也有一些缺点。我们将在接下来的段落中讨论这些缺点，让你了解使用它时会面临的问题。最后，我们将研究 Web Flow 如何帮助你克服这个作用域的缺点。

将会话数据存储的问题归结为清理和管理。例如，考虑一下当用户完成订单并在应用程序中做其他事情时，我们的数据会发生什么。这些数据会无用地停留在会话中，直到会话超时吗？

是的，会这样。有很多机制可以帮助你从会话中移除数据，但没有一个是保证有效的。这可能是一个大胆的说法；但是，请记住，用户可以随心所欲：点击后退按钮、跳出你的用例、在你的应用程序中手动输入另一个 URL，等等。

另外，你打算如何在会话映射表中命名你的键：`myData`？你确定之前没有其他人使用过这个键吗？会话可以被你的 Web 应用程序中的任何组件使用，因此你必须开始为你的键添加命名空间（例如，`page.x.data`）。



那么，会话中到底该存储些什么呢？它肯定有它的用处！考虑这个例子：用户登录后，你可能希望维护一个包含已验证用户数据的安全上下文。应用程序中的任何地方都可能需要这些信息——甚至*每个*页面都可能需要。例如，你可能希望将一些个人信息（如用户的姓名）放在每个页面自动显示的页眉区域中。这类信息理应与用户的会话同生共死；在这种情况下，会话显然是存储此类上下文数据的正确选择。*上下文数据*指的是与会话或应用程序本身紧密耦合的数据；它的使用范围比应用程序中的单个流程或用例更广。

但在这个例子中，我们讨论的是普通的用户数据（通过表单为应用程序中的特定流程捕获的数据），这与上下文数据本质上是不同的。用户会浏览多个屏幕，并在每个屏幕中输入数据。你不仅需要当前视图的用户数据，还需要后续视图的数据。

当然，你可能会疑惑为什么我们要对在会话中保留数据超过必要时间这件事如此小题大做。嗯，你的会话应该尽可能保持精简；表 10-2 列出了一些更重要的原因（尽管可能还有其他原因）。

![图片](img/9781430241553_tab10-02.jpg)

到目前为止，我们已经看到存储状态并不像看起来那么简单。Servlet 规范提供的范围并没有给我们太多选择；在大多数情况下，这会导致我们过度使用会话。随着应用程序的持续增长，这可能会在应用程序生命周期的后期成为一个问题。

Web Flow 通过引入五个新的作用域来解决这个问题：*conversation*、*flow*、*view*、*flash* 和 *request*（参见图 10-2）。

我们不会在这里详细讨论它们；但请注意，它们位于会话和请求作用域之间。例如，放在 flow 作用域中的内容比请求存活得更久，但不如会话作用域长。Flash 作用域可能比请求存活得更久，但不如 flow 作用域长。此外，这些作用域的内容会自动为您管理，比标准的会话维护更有保障。我们将在接下来的章节中讨论其工作原理。

![图片](img/9781430241553_Fig10-02.jpg)

***图 10-2.** Web Flow 的额外作用域*

这些额外的作用域存储在会话作用域中。然而，Web Flow 机制是专门设计的，以确保您不必担心这些额外作用域的创建和清理。这就是您开始受益于使用 Web Flow 自动状态管理的地方。

#### 自动状态管理

每当 Web Flow 在您的流程中执行一个操作时，它都会将您在不同 Web Flow 作用域上使用的状态存储在一个状态快照中。虽然我们还没有介绍任何 Web Flow 术语，但一个流程由不同的*操作*组成。例如，一个操作可以是显示一个页面，或者在应用程序控制器上执行逻辑。放置在不同 Web Flow 作用域上的数据会自动为您管理和版本化。每次 Web Flow 从执行请求的操作返回时，它都会创建您数据的快照，包括其所有内部*状态*信息。

您可以将这个过程比作拍照。在返回浏览器之前，Web Flow 会拍摄数据的快照并存储起来，这意味着如果需要，您以后可以回溯到它。例如，您可以使用浏览器的“后退”按钮返回到之前的状态。重要的是要认识到，这种*快照*功能是内置于 Web Flow 中的，并且可以自动使用。您可以配置应存储多少个快照。

除了存储与流程相关的所有数据的快照外，Web Flow 还可以同时维护多个流程或*执行实例*（参见图 10-3）。执行实例是一个有点抽象的概念；最好的理解方式是将其视为流程及其包含的所有元素的运行时变体。在这种情况下，流程指的是定义本身（即组成流程的 XML）；而执行实例指的是流程启动后的内存表示。这个执行实例包括流程的所有状态，以及可能由它启动的所有子流程（我们将在下一章解释子流程）。Web Flow 会为您保留不同的流程执行实例。例如，假设您启动了一个涉及流程 x 的执行实例，然后启动了一个涉及流程 y 的执行实例（即，在启动流程 x 后，您在某个点放弃它，转而启动一个完全独立的流程 y）。您仍然可以通过使用浏览器的“后退”按钮返回到流程 x 的执行实例。每个流程执行实例都会保留自己的一组状态快照。类似于您可以为流程配置最大快照数量，您也可以配置最大活动执行实例数量。我们将在第 12 章中更详细地讨论这两个设置。

![图片](img/9781430241553_Fig10-03.jpg)

***图 10-3.** Web Flow 状态管理机制*



#### 请求同步

在使用 Spring MVC（或任何其他基于请求的 Web 框架）设计 Web 应用程序时，迟早你会发现自己缺少一些其他重要功能。例如，你是否考虑过，如果用户在一个图书订单表单上点击两次“提交”按钮会发生什么？理论上，这没有充分的理由发生，因为点击按钮会立即将你带到下一页。当然，这只是理论上的。实际上，这可能会带来一定的延迟，具体取决于你的应用程序响应速度（快或慢）。用户往往缺乏耐心；如果几秒钟内没有任何反应，用户甚至可能怀疑第一次点击是否成功（也许他没点准？）。因此，在你意识到之前，你就不得不处理来自同一用户和同一页面的重复提交。

重复提交后会发生什么，很大程度上取决于运气。也许你的表单提交在某种程度上是*幂等*的^(1)？在这种情况下，幂等意味着多次执行操作，而不会从业务流程角度受到任何不良副作用的影响。仅仅因为用户双击了“提交”按钮就重复创建（并随后交付）同一订单，这显然是一种不良副作用。虽然幂等性通常与只读操作相关，但有时表单提交也可以是幂等的，具体取决于你的设计方式。例如，在创建账户时，数据库表的 `username` 列上可能有一些唯一约束或主键约束。如果表单被提交两次，第二次提交会因*重复键*而失败，不会对你的系统造成损害。不过，用户可能会看到一个错误页面。这显然是你想避免的，但至少你不会在数据库中创建重复的用户。在这个例子中，是表的唯一约束救了你；但运气稍差一点，情况可能正好相反，重复提交表单很容易触发不良副作用。这最终可能导致数据损坏或系统中的其他不一致问题。

因此，你通常希望设计你的应用程序，使得表单无法被提交两次；或者，至少确保不会产生任何不良副作用。虽然一行简单的 JavaScript 代码似乎可以解决这个问题（例如，你可以在第一次点击后禁用按钮），但请记住，这只能降低表单被提交两次的可能性。禁用按钮远非理想或完整的解决方案。例如，以下场景仍然可能发生：

____________

¹ [`http://en.wikipedia.org/wiki/Idempotence`](http://en.wikipedia.org/wiki/Idempotence)（计算机科学含义）

*   点击“提交”按钮后，用户按下 F5 键；这将导致表单被重新提交。（浏览器会警告用户，但仍然可能提交两次表单。）
*   用户可以使用像 cURL 这样简单的工具模拟多线程 HTTP 请求，轻松绕过你的 JavaScript。^(2) 或者，用户可以直接在浏览器中禁用 JavaScript；这甚至不需要是专业开发者就能做到！

这里的要点是，你需要一种成熟的机制来序列化对特定表单提交的访问（当然，是针对特定会话），这样你的应用程序逻辑就不必处理重复提交（即，你不想编写特定的代码来处理这个问题）。

Spring Web Flow 通过序列化对流程执行的访问来解决这个问题。获取 HTTP 会话 ID 后，你将获得一个*流程执行键*。流程执行键之于 Web Flow，就如同 HTTP 会话 ID 之于你的 Web 容器。每个流程执行都会获得一个唯一的 ID。Web Flow 将使用此 ID 来标识用户当前参与的流程执行。但它也会使用此 ID 来序列化对流程执行的访问（即，它将防止两个请求同时操作同一个流程执行）。但别担心：这也是为你自动管理的，你甚至不会注意到它。

每当表单在你的流程中被提交两次时，Web Flow 保证在给定 HTTP 会话中，对于该流程执行，一次只有一个请求会被执行。它通过序列化流程执行键来实现这一点。第二个请求会保持挂起状态，因为流程执行被第一个请求锁定了。它将在第一个请求处理完毕后最终被执行，除非第一个请求结束了该流程。在这种情况下，第二个请求将简单地重新启动流程，因为流程已经结束（参见图 10-4）。

____________

²[`http://curl.haxx.se/`](http://curl.haxx.se/) 一个非常简洁易用的基于命令行的工具，使用 URL 语法（http、https、smtp 等）传输数据。

![图片](img/9781430241553_Fig10-04.jpg)

***图 10-4.** Web Flow 同步对同一流程执行的多个请求*

在图 10-4 中，顶部的矩形代表处理两个请求（来自同一浏览器会话的重复提交）的流程执行。只允许一个请求通过进行处理（`request1`），而第二个请求（`request2`）被流程执行阻塞（其他请求也会被阻塞）。在处理完 `request1` 后，在请求结束之前，它触发了一个结束状态（我们稍后会讨论）。结束状态会终止整个流程执行。在中间的矩形中，你可以看到第一个请求处理完毕后的流程执行。锁被释放，因此第二个请求有机会进入流程执行。然而，该执行已经被销毁；在底部的矩形中，你可以看到一个新的流程执行被启动。换句话说，第二个请求会将用户带回流程的入口点，而不是重新执行该操作。

这意味着在流程执行内部仍然可能发生重复提交。Web Flow 序列化了对流程执行的访问，但只要流程尚未结束（如前一段所述），第二个请求仍会执行与第一个请求完全相同的逻辑，你仍然会遇到重复提交。通常，你会在结束流程之前执行你的流程事务。在图书订购流程中，我们会经历不同的步骤，每个步骤构成订单的一部分。如果用户在此过程中双击，不会发生什么坏事（最坏的情况是，一本书被添加了两次）。但当用户最终确定订单时，我们会执行创建订单的事务，并将所有内容提交到数据存储。当第一个请求返回时，流程已经结束。这意味着第二个请求不会触发第二次创建订单的事务，而只是重新启动流程。



![Image](img/square.jpg) **注意** 你可能会问，基于令牌（流程执行键）同步访问是否会影响性能。答案是不会。同步发生在同一 HTTP 会话内的流程执行中。换句话说，它序列化了单个客户端发送到同一流程执行的操作的访问。例如，客户端的重复提交会被序列化，因为重复提交会在同一 HTTP 会话内并行向同一流程执行发起多个请求。然而，如果两个不同的客户端（因此是两个不同的会话和不同的流程执行）恰好同时按下同一个“提交”按钮，则不会发生序列化，所有操作都会并行执行。

现在让我们回到 F5（刷新）按钮引发的问题。即使没有双击，意外刷新已提交的表单也会触发同样的效果。它不会被检测为双击（只有一个请求，按顺序跟在第一个请求之后）。针对这个问题，Web Flow 提供了“Post Redirect Get (PRG)”模式。这个名称虽然复杂，但模式本身非常容易理解，它解决了“意外”重新提交的问题。

#### Post Redirect Get (PRG)

通过应用 PRG，Web Flow 会对请求进行两阶段处理。在第一阶段，从浏览器接收 HTTP 请求。首先，触发应用程序逻辑（如果有的话）的调用。接着，在开始第二阶段之前，Web Flow 向浏览器发出重定向，第二阶段负责渲染并返回视图。这使得浏览器忘记表单，并发出一个（幂等的）GET 请求来获取视图。之后按 F5 只会重新发送最后一次 GET 请求。因此，它仅重新执行第二阶段，即渲染并返回视图（参见图 10-5）。

![Image](img/9781430241553_Fig10-05.jpg)

***图 10-5.** PRG（Post Redirect Get）模式*

起初，你触发一个正常的表单提交。请记住：如果你重复提交，得益于 Web Flow 的序列化机制，每次只会处理一个提交。另一个提交会在第一个提交完成后被处理。如果第一个请求结束了流程，第二个请求则会简单地重新启动流程。

当请求（一个 HTTP POST）进入 Web 容器时，它会执行请求的逻辑。Web Flow 不会直接渲染视图并发送 HTTP 200（+ 视图数据），而是会向客户端浏览器发送一个 302 重定向。此时，流程执行被“暂停”，状态被内部存储起来。请求之间存储状态的机制完全由 Web Flow 处理。

浏览器会跟随重定向，向 Web 容器发出一个 HTTP GET 请求。在这里，Web Flow 再次接手请求，并让视图被渲染。视图最终被发送回客户端浏览器并显示。请记住：最后的动作不是 POST，而是 GET。如果用户按下 F5，会执行（幂等的）GET 请求，不会产生任何副作用；它仅仅返回最后一个视图，而不会（重复）再次处理相同的逻辑。因此，没有机会意外重新提交表单。你可以免费获得所有这些功能。酷！

#### 受控导航

到目前为止，你主要使用 Spring MVC 来管理应用程序中的导航。例如，使用 `org.springframework.web.servlet.ModelAndView` 可以让你指定下一个要渲染的视图。在流程中工作时，Web Flow 将接管导航控制权。它实际上会从你的控制器中抽离出来，并在你的流程中进行建模。你还将能够触发应用程序控制器上的操作，或直接从流程内部执行逻辑。

当我们谈到应用程序控制器时，在使用 Web Flow 时是否还需要它们？答案取决于具体场景。你的流程实际上已经成为了应用程序控制器。它控制导航，并可以通过表达式语言执行逻辑。你可以直接从流程内部调用一个外观（或者服务，如果你喜欢的话）。然而，大多数情况下，使用一个普通的 Java 对象（POJO）作为应用程序控制器并无坏处。Java 仍然比冗长的表达式和 XML 更易读。对于只需一行 Java 代码的单个方法就能解决的琐碎事情，仅靠你的流程就足够了。如果你需要更多逻辑，并觉得有必要开始编写代码，那么最好创建一个单独的 POJO 应用程序控制器（用 @Controller 注解），并让你的流程调用其方法。

![Image](img/square.jpg) **注意** 你可能想知道，在使用 Web Flow 时，应用程序控制器上是否仍然需要 @`Controller` 注解。需要了解的是，当应用这些注解时，相关类会成为一个 Spring 管理的 Bean。这意味着你可以直接从流程中引用这些应用程序控制器。从注解角度来看，你可以使用 @Controller 或 @Component 来实现。@Controller 只是 @Component 的一种特化。换句话说，两者作用相同——@Controller 只是额外表明该类是一个应用程序控制器（位于前端的东西）。即使你的应用程序控制器仅由流程使用，而可能不被 Spring MVC 使用，继续使用 @Controller 可能也是一个好主意。归根结底，应用程序控制器是你流程的扩展（流程本身也可以被视为一个应用程序控制器）。



#### 何时应避免使用 Web Flow

与所有技术一样，使用 Web Flow 也有其利弊。我们已经向你介绍了 Web Flow 能为应用程序带来的一些优势。现在，让我们看看在哪些情况下可能不适合使用 Web Flow。

幸运的是，我们认为只有三个理由需要避免使用 Web Flow。首先，（显然）当你的 Web 应用程序框架不支持 Web Flow 时，应避免使用它。在这种情况下，我们建议寻找其他解决方案。但由于我们使用的是 Spring MVC，这不成问题。（顺便提一下，Web Flow 也支持 JSF 和 portlet，即 JSR-168^(3) 环境。）

其次，Spring MVC 已经提供了一些（基本的）对构建向导（即共享状态的顺序视图）的支持，因此可以说 Spring MVC 基础设施可用于处理简单的、相互依赖的页面。另一方面，引入 Web Flow 相当容易（我们将会看到）。Web Flow 会添加大量功能和其他可能性，这些功能在你不需要时不会造成困扰，但在你需要时随时可用。与几乎所有 Spring 技术一样，Web Flow 是非侵入式的。

第三，在应用程序中没有流程的地方，Web Flow 作用不大。如果所有内容都包含在单个页面中，那么 Web Flow 只会带来额外开销。这个页面仍然可以由普通的 Spring MVC 控制器支持，并且无需感知 Web Flow。

因此，除了这些情况以及它需要学习另一个框架/概念这一事实外，我们认为在应用程序中引入 Web Flow 几乎没有缺点。如果你在某些特定且非常简单的用例中不需要它，并且可以使用 Spring MVC 控制器，那就这样做。我们的 Web Flow 设置仍然允许你在同一个应用程序中同时使用 Spring MVC 和 Web Flow，从而让你能够针对每种情况选择最合适的方案。

![Image](img/square.jpg) **注意** 我们讨论的基本 Spring MVC 流程功能是通过一个特定的 Spring MVC 控制器支持的：`org.springframework.web.servlet.mvc.AbstractWizardFormController`。这个类现已弃用，你应该改用标准的 `@Controller` 注解。你可以结合 `@SessionAttributes` 和 `org.springframework.web.bind.support.SessionStatus` 来构建一个流程，并在处理结束时结束该流程。虽然这仍然是一个有效的替代方案，但有些繁琐。请注意，这种工作方式非常有限，远不及 Web Flow 提供的额外能力。我们的建议是，仅在你需要为应用程序的极小部分构建非常离散且小块的流程逻辑时，才将此方法作为一个小帮手使用。对于所有其他需求，请省去麻烦，考虑使用 Web Flow。

### 流程的基本要素

在本节中，我们将探讨构建所谓流程所需的基本构建块。我们将从一个简单的流程示例开始，然后解释该流程包含的不同元素。我们不会深入探讨每个元素的所有可能选项，而是只关注基础知识。在下一章中，我们将修订此处介绍的某些元素（并涵盖一些新元素），并更详细地介绍如何使用这些元素。因此，如果某些功能未在此处描述，请不要惊慌——我们会在后面讲到。

____________

³ [`http://jcp.org/aboutJava/communityprocess/final/jsr168/index.html`](http://jcp.org/aboutJava/communityprocess/final/jsr168/index.html)

#### 流程

流程是你将与之交互的主要组件。它是一个由你编写并由 Web Flow 解释的 XML 文档。流程定义了应用程序中可能的步骤，并为每个步骤定义了以下内容：

*   应渲染哪个视图以及视图之间的交互
*   哪些操作是可行的，以及是将执行委托给应用程序控制器，还是直接在流程中执行表达式

每个流程定义都是一个 XML 文档，因此应符合 XML 语法标准。你需要从 XML 声明开始，然后是流程根元素（见清单 10-1）。

***清单 10-1.** 流程根元素声明*

`<flow xmlns=http://www.springframework.org/schema/webflow`

`      xsi:schemaLocation="`
`        http://www.springframework.org/schema/webflow`
`        http://www.springframework.org/schema/webflow/spring-webflow-2.0.xsd">`

流程定义有一个 XML Schema，它会告诉你哪些元素/属性允许在哪个位置出现。该 Schema 还会提供一个简短摘要，说明每个（基本）元素/属性的用途。

在图 10-6 中，我们可以看到流程定义 XML Schema 的可视化表示。^(4) 该 Schema 让我们能够概览流程定义可以包含的元素和属性。

![Image](img/square.jpg) **注意** 根据你对 XML Schema 的熟悉程度，你可能知道 Schema 可视化仅展示了流程的顶级元素。大多数元素都有自己的属性，但这些属性并未在此列出（你可以在深入查看 Schema 时看到它们）。当我们在本章或下一章讨论这些元素时，我们会列出并解释这些元素的属性。此外，某些元素可以包含嵌套元素，但这些嵌套元素不能作为顶级元素存在（因此未在此列出）。例如，`<set>` 元素可以嵌套在 `<on-start>` 元素中，但不能用作顶级元素。在讨论某个元素时，我们也会介绍该元素所支持的不同可能性。

____________

⁴[`http://www.springframework.org/schema/webflow/spring-webflow-2.0.xsd`](http://www.springframework.org/schema/webflow/spring-webflow-2.0.xsd)

![Image](img/9781430241553_Fig10-06.jpg)

***图 10-6.** 流程定义 XML Schema 可视化*

在图 10-6 中，右侧的 flow 是顶级元素。每个流程定义都应以 `<flow>` 元素开头。顶部区域（以 `start-state` 开头）包含可在流程根元素内使用的三个属性。

在底部区域（以 `attribute` 开头），你可以看到可在流程定义内部使用的元素。它们的顺序很重要，你应该遵循此处列出的顺序。例如，你不能在 `<on-start>` 元素之后放置 `<var>` 元素。

![Image](img/square.jpg) **注意** 只有五种状态在流程定义中是不区分位置的。这是因为它们通过特殊的选择结构连接在一起。所有其他元素都是区分位置的，并且应按照图 10-6 所示的顺序出现。

中间列（例如 `[0..*]`）显示了元素的多重性。它表示该元素在单个流程定义中可以出现的次数。左边的数字是最小值，右边的数字（或表示无限的星号）是最大值。例如，`[0..*]` 表示该元素是可选的，并且可以出现无限次。现在看看 `<var>` 元素。它的多重性（`[0..1]`）意味着该元素是可选的，但对于给定的流程定义，它只能出现一次（例如 `<on-start>` 元素）。



不同的状态（以 `<action-state>` 开头，以 `<end-state>` 结尾）通过一个选择（choice）连接起来。通常，在一个选择中，列出的元素只能出现一个。例如，通常你只能有一个 `<view-state>` 或一个 `<action-state>`，但不能同时存在。然而，如果你仔细观察选择图标，会发现选择符号具有多重性（`0..*`）。这意味着状态是可选的；但同时，它也表明选择中的每个元素都可以出现无限次。因此，你可以定义的状态数量没有限制。此外，由于选择的存在，状态是非位置性的（这可能是引入选择作为连接方式的主要原因）。例如，你可以有一个 `<view-state>`（出现一次或多次）后跟一个 `<action-state>`（出现一次或多次），或者反过来。这两种方式都是有效的。

回顾一下清单 10-1，你会发现我们将 Web Flow 本身声明为默认命名空间。这意味着我们不必为每个元素使用前缀。每个元素或属性都会自动根据与默认命名空间关联的模式进行验证。最后，我们还指定了模式位置。IDE 使用模式位置来加载模式、验证 XML 并提供命令行补全功能。请注意，我们使用的是 Web Flow 2.0 的模式。

在后续的 Web Flow 讲解中，我们将在代码清单中省略流程根元素及其命名空间声明。这样可以使内容保持紧凑且更易读。只有当这些信息相关时，我们才会展示，例如在介绍属于流程根元素的属性时。请注意，即使在给定的清单中没有显示流程根元素及其命名空间声明，它们仍然存在于每个流程的开头，并且是强制性的。

现在，请查看清单 10-2 中的流程定义。

***清单 10-2.** 一个流程示例*

`<flow`

`      xsi:schemaLocation="`
`        http://www.springframework.org/schema/webflow`
`        http://www.springframework.org/schema/webflow/spring-webflow-2.0.xsd">`

`    <on-start>`
`<evaluate` ![图片](img/U002.jpg)
`            expression="orderController.initializeForm()"`![图片](img/U002.jpg)
`            result="flowScope.orderForm"/>`
`<evaluate` ![图片](img/U002.jpg)
`            expression="orderController.initializeSelectableCategories()"`![图片](img/U002.jpg)
`            result="flowScope.selectableCategories"/>`
`    </on-start>`

`    <view-state id="selectCategory" view="selectCategory" model="orderForm">`
`        <transition on="next" to="selectBooks" >`
`<evaluate` ![图片](img/U002.jpg)
`   expression="orderController.initializeSelectableBooks(flowScope.orderForm)"`![图片](img/U002.jpg)
`   result="flowScope.selectableBooks" />`
`        </transition>`

`        <transition on="cancel" to="end" />`
`    </view-state>`

`    ...`

`    <end-state id="end" view="redirect:/index.htm"/>`
`</flow>`

在这个例子中，我们创建了一个简单的流程定义。它不是一个完整的定义（因此末尾有省略号 `...`）；但我们希望以此温和地介绍流程定义的样子。

我们还想指出，在使用 STS 时，你的 IDE 中会提供 Web Flow 支持。你还可以以图形方式显示流程 XML。一旦双击流程 XML 文件，内容面板将打开，通常会显示 XML 视图。在此面板的底部，你会注意到一些选项卡。在“源”选项卡旁边，你应该还能找到“流程”和“流程图”选项卡。“流程”选项卡以列表形式提供流程中元素的概览（参见图 10-7）。

![图片](img/9781430241553_Fig10-07.jpg)

***图 10-7.** “流程”选项卡，提供流程组件的概览*

“流程图”选项卡以图形方式显示元素及其之间的不同关系（参见图 10-8）。你还可以使用左侧的调色板添加新元素并组合它们。

![图片](img/9781430241553_Fig10-08.jpg)

***图 10-8.** “流程图”选项卡，提供流程的图形概览*

调色板中的所有信息都会直接反映在源代码中，反之亦然。如果你从调色板添加一个组件，相应的 XML 将被添加到源代码中。如果你直接在 XML 中添加一个元素，然后返回“流程图”（或图形）视图，新元素也会出现在那里。

目前，只需理解当此流程启动时，它最终会进入 `selectCategory` 视图状态。此视图状态将渲染 `selectCategory` 视图（该视图将由 Tiles^(5) 配置解析），并使用 `orderForm` 键下可用的表单。

我们使用一个启动动作（on start action）来实例化模型，并在进入视图状态之前将其分配给流程作用域。我们将在后续章节中讨论 evaluate 和 on start 动作。目前，只需知道 on start 动作在 Web Flow 进入其第一个状态之前执行，并且它将执行 `com.apress.prospringmvc.bookstore.web.controller.OrderController` 上的 `initializeForm` 方法。这将创建一个已初始化的模型并返回它。然后该模型将被放入流程作用域。模型名称随后可以在视图中的 `<spring:form modelAttribute="orderForm"…/>` 标签中使用。这将在提交时将表单中的字段绑定到指定的表单。Web Flow 将负责参数绑定，以及转换和表单验证（我们将在后面看到）。请注意，`modelAttribute` 必须已经存在于某个 Web Flow 作用域中。

____________

⁵我们在第 8 章中向你介绍了 Tiles，因此我们假设你已经对这个模板框架有了基本的了解。如果你跳过了前面的内容直接阅读本章关于 Web Flow 的内容，但不知道 Tiles 是如何工作的，那么我们建议你在继续阅读本章其余部分之前，先阅读 Tiles 的介绍。

![图片](img/square.jpg) **注意** `OrderController` 只是一个在应用程序上下文中定义的 Spring Bean。在我们的例子中，通过使用注解；应用 `@Controller` 注解，这样该类将被类路径扫描发现并成为 Spring Bean。

同样值得注意的是，在视图状态中可以触发的可能转换。当视图被渲染时，可以执行 `next` 或 `cancel` 转换。`next` 转换将在控制器上执行一个方法，然后转发到下一个状态（这可以是视图状态或 Web Flow 支持的任何其他状态，我们将在后面看到）。`cancel` 转换将导航到结束状态。我们将在下一章详细讨论结束状态；目前，只需知道转换到结束状态会终止流程即可。

流程定义根元素（flow）允许你指定一些额外的属性，这些属性是可选的。对于一个简单的流程，跳过指定它们是可以的；然而，一旦你有了更复杂的流程布局或想要更多的自定义，这些属性可能会派上用场（参见表 10-3）。此根元素允许你覆盖默认的起始状态、从另一个现有流程继承，以及将你的流程标记为抽象。我们将在第 12 章的“应用继承”部分详细讨论后两种情况。

![图片](img/9781430241553_tab10-03.jpg)



#### 视图状态

`<view-state>` 元素是每个流程中的基本元素。正如我们将看到的，还有其他四个 `*-state` 元素。视图状态可能是这些元素中最重要的一个，因此我们将首先对其进行描述。

视图状态模拟了一个从进入该状态时开始渲染的视图。视图状态可以作为流程的起点进入，也可以通过该流程内其他状态的转换事件进入。当视图状态渲染视图时，执行过程会暂停（此时用户正在浏览器上查看渲染后的视图）。然后，用户可以触发该视图状态内允许的转换以继续处理。

____________

⁶ 我们接下来将详细讨论视图状态。其他状态包括决策状态、动作状态、子流程状态和结束状态。我们在此有意省略了它们，因为构建基本流程并不需要它们。

在清单 10-2 中，描述了两种这样的转换：`next` 和 `cancel`。每当用户触发此类动作（通过点击页面上的按钮或链接）时，流程会恢复并执行给定的动作。

视图状态通过其 `id` 属性进行标识。你可以使用该属性从其他状态引用此视图状态。在同一流程定义中，所有状态的 id 必须是唯一的。`view` 属性指示此视图状态将渲染哪个视图。`model` 属性标识存在于任何 Web Flow 作用域中的对象名称，该对象将用于绑定接收到的参数。这些参数是表单提交中的请求参数部分。表 10-4 列出了这些属性及其描述和用途。

![图片](img/9781430241553_tab10-04.jpg)

____________

⁷ 如需更多详细信息，请参阅 [`jira.springsource.org/browse/SWF-1518`](https://jira.springsource.org/browse/SWF-1518)。

图 10-9 中还列出了其他一些属性，我们此时不会讨论它们，因为它们超出了本章基础内容的范围。构建基本流程也不需要它们。为了方便起见，如果你正在阅读本文作为参考，或者想提前了解，`parent`、`popup` 和 `history` 属性将分别在第 12 章的“继承”、“重新设计订单概览”和“执行与会话快照”部分中讨论。

![图片](img/9781430241553_Fig10-09.jpg)

***图 10-9.** `<view-state>` 元素的 XML 模式概览*

`attribute` 元素对于每个状态都是通用的，也可以应用于流程根元素。你可能永远不会用到这个元素，但为了完整性，我们在此进行介绍。例如，`attribute` 元素用于向工具提供元数据。

![图片](img/square.jpg) **注意** 当属性也是 XML 语法的一部分时（例如 `id` 或 `view` 属性），你会看到一个名为“attribute”的元素。这一切使得这个属性名称变得不必要的令人困惑！

可视化流程的工具可以读取这些 `attribute` 元素，并使用其内容来显示附加信息。该元素具有三个属性：`name`、`value` 和 `type`。`name` 和 `value` 属性可以是任何内容。`type` 属性用于将值（否则被视为字符串数据类型）转换为特定类型（如果需要）。

在其余元素中（参见图 10-9），我们将在本章中仅讨论 `<transition>` 元素。请查阅目录或索引以查找其余元素的解释，因为它们分散在接下来的几章中。

#### 状态转换

转换字面意思是指从当前状态到流程内相同或不同状态的状态转换。在某些情况下，流程可能会在执行实际转换之前执行一个动作。你可以在清单 10-2 的 `selectCategory` 视图状态中看到这一点，其中有两个转换。转换由 `on` 属性中定义的字面量触发。你基本上是通过一种特定的命名约定来提供一个请求参数，其中嵌套了 `on` 字面量。在接下来的“选择类别”部分中，当你构建第一个页面时，你将看到如何在视图中定义它。

![图片](img/square.jpg) **注意** Web Flow 内部使用事件工作。当你在 `on` 属性中指定一个字面量时，它会从中创建一个匹配的事件。每当有请求进来触发转换时，它会匹配该事件。这目前还不是很重要，因为你将在视图和 `on` 属性中指定一个字面量。第 11 章的“使用应用程序控制器进行表单验证”和“处理结果事件”部分将介绍这与事件的关联。除了字面量，你还可以在 `on` 和 `to` 属性中使用表达式。

目标状态可以是五种可能的状态类型中的任何一种，你可以通过 `to` 属性引用它们。其值是你希望转换到的其他状态的 id。请记住，你也可以转换到相同的状态（在我们的例子中是相同的视图状态）。省略 `to` 属性会产生相同的效果，尽管它不会触发真正的转换。在这种情况下，视图状态不会被显式地重新进入。当我们下一章讨论 `on entry` 和 `on exit` 动作时，这一点将变得重要。目前，可以安全地假设这两种替代方案会产生相同的结果；然而，不指定 `to` 属性是此处最合乎逻辑的做法。请注意，`cancel` 转换会转换到另一个具有 `end` id 的状态（即结束状态）。这是其他四个特殊状态之一，我们稍后会看到。

表 10-5 列出了 `transition` 元素的各种属性。

![图片](img/9781430241553_tab10-05.jpg)

#### 评估动作

评估动作使你的流程能够与其他组件交互，例如 Spring MVC 控制器（或任何其他 Java 类，只要它们是 Spring Bean）。评估动作接受一个表达式，该表达式在评估发生时执行。评估动作必须是另一个元素的子元素，不能独立存在。它作为流程中触发的某个特定*动作*的一部分执行。在示例中，我们将评估动作嵌套在视图状态内的转换中以及启动动作中。正如我们将看到的，除了转换和启动动作之外，你还可以将评估动作与其他元素结合使用。^(8) 无论 `evaluate` 动作在哪里使用，其目标保持不变：评估一个执行方法、操作某些对象状态并可能返回结果的表达式。

当表达式返回一个值时，你可以使用 `result` 属性将返回值绑定到给定作用域中的变量。评估动作的属性列在表 10-6 中。

![图片](img/9781430241553_tab10-06.jpg)



#### 表达式

在阅读了关于评估动作和表达式的内容后，你可能会想：“表达式”？很好。但那是哪种表达式语言呢？是 Unified EL？OGNL？SpEL？还是其他什么？实际上，这次 Spring 的开发人员让事情变得简单了。默认情况下，Web Flow 使用 Spring EL (SpEL) 作为其表达式语言。如果你以前用过 Spring，你可能对 SpEL 很熟悉，并且明白这能让整个体系更加一致。现在，你可以在 Web Flow 中以与在 Spring 应用程序中相同的方式使用 SpEL。

如果你不熟悉 SpEL，可以在 Spring 参考文档中找到很好的概述。^(9) 对于 Web Flow 章节，只需要对这种表达式语言有非常基本的了解，我们只会用它来调用方法和传递参数。我们将快速介绍你需要了解的关于 SpEL 的一切，以便理解它在 Web Flow 章节中的用法。

____________

⁸ 参见第 11 章：在“执行动作概述”部分介绍了 on start、on entry、on render、on exit 和 on end，在“动作状态”部分介绍了动作状态。

⁹ [`http://static.springsource.org`](http://static.springsource.org)`/spring/docs/3.1.0.RELEASE/spring-frameworkreference/htmlsingle/spring-framework-reference.html#new-feature-el`

首先，SpEL 与 Unified EL 类似，但它有一些额外的功能，比如方法调用和字符串模板函数。使用 SpEL，我们可以在运行时操作我们的对象图。要访问 bean 的属性，我们引用该 bean 并简单地指定我们想要获取的属性。表达式是一个以 `#{` 开头、以 `}` 结尾的字符串。Spring 会检测到该表达式并使用它来解析值。

要访问一个属性，我们只需编写 `#{bean.property}`，这告诉 Spring 在名为 `bean` 的 bean 上执行 `getProperty()` 方法。*点号* (`.`) 是分隔符，第一部分用于查找名为 `bean` 的对象，第二部分是根据 JavaBeans 规范指定的属性名称（因此你不需要指定 `get` 或 `set`）。

在这种情况下，bean 名称是 Web Flow 可用的任何作用域中的一个键（我们将在第 11 章的“不同的 Web Flow 作用域”部分讨论这些）。默认情况下，Web Flow 会扫描所有可用的作用域，以查找具有匹配键的 bean。如果未找到匹配项，则会接着尝试应用程序上下文，以解析具有匹配 bean 标识符的 bean。

![图片](img/square.jpg) **注意** 应用程序上下文（通常）存储你的控制器、服务、仓库等。这些是无状态（并作为单例运行）的对象类型。你的真实状态，例如包含用户数据的表单，则存储在任何可用的作用域上，无论是 Web Flow 提供的作用域还是实际的 Servlet 提供的作用域。

除了访问属性，你还可以调用方法并传递参数。其工作方式相同。例如，看看 Web Flow 中评估动作的这个表达式：

`<evaluate expression = "someService.someMethod(someObject)"/>`

这将执行 `someMethod`——`someService` 的一个方法，并将 `someObject` 作为参数传递。同样，表达式解析器默认会扫描所有作用域以查找服务对象和参数（包括应用程序上下文）。你还可以使用隐式对象来表示作用域或其他直接可用的特殊对象。虽然这些与表达式语言没有严格关系，但它们可以被视为始终可用的*别名*。同样，我们将在第 11 章中介绍隐式对象。

在清单 10-2 的早期示例中，我们调用了 `orderController` 的 `initializeForm()` 方法：

`<on-start>`
`    <evaluate`
`        expression="orderController.initializeForm()"`![图片](img/U002.jpg)
`        result="flowScope.orderForm"/>`
`    <evaluate`
`        expression="orderController.initializeSelectableCategories()"`![图片](img/U002.jpg)
`        result="flowScope.selectableCategories"/>`
`</on-start>`

在执行表达式时，`orderController` 在应用程序上下文中被找到。该方法返回的结果是将在整个流程中使用的完全初始化的模型。该结果被绑定到流程作用域。

查看前面的表达式时，你可能会注意到我们省略了表达式分隔符（`#{}`）。放置分隔符的原因是为了向基础设施明确你正在定义一个表达式。否则，它如何区分普通的字面值和表达式呢？然而，在评估动作的情况下，有一个显式的 `expression` 属性。我们只期望那里有一个表达式，因此不需要使用分隔符。在这种情况下，甚至*禁止*使用表达式分隔符。我们认为这有点奇怪，但事实就是如此。

在你创建模板表达式或在既用于字面值又用于表达式的属性（例如，视图状态的 `view` 属性）中使用表达式的情况下，你必须使用分隔符来标明表达式部分的位置。例如，考虑这个片段：

`<view-state view="myView#{someVariable}"/>`

这里我们标明 `#{someVariable}` 是表达式部分。如果我们改为放置 `myViewSomeVariable`，那么整个值将被解释为字面值。

![图片](img/square.jpg) **注意** 你可能是一位 Web Flow 1 的用户，并且更喜欢 OGNL（该版本的默认表达式语言）。或者，你可能正在从 Web Flow 2 的早期版本升级，其中 Unified EL 是默认的表达式语言。在第 12 章中，我们将说明如何将默认表达式语言更改为 Unified EL 或 OGNL。但是，我们鼓励你使用 SpEL（默认值），并且只有在有充分理由的情况下才偏离此选项。

### 配置

现在你已经了解了基本流程及其主要元素的样子，是时候进行一些实际操作了。我们应该做的第一件事是配置我们选择的 Web 应用程序框架（Spring MVC）。接下来，我们将配置 Web Flow 并解释 Web Flow 如何与 Spring MVC 集成。

除了 Spring MVC 之外，Web Flow 还对以下内容有特殊的集成支持：

*   Spring Portlet MVC
*   JSF2

这里不讨论这些选项，但重要的是要告诉你，Web Flow 并不仅仅绑定到 Spring MVC。这是一件好事，因为你在这里学到的知识可能对使用其他 Web 技术的项目有用。

![图片](img/square.jpg) **注意** 这里解释的配置和示例代码片段可以在 `chapter10-bookstore` 示例部分找到。在本书的开头，你将找到关于如何在 STS IDE 中导入此项目或如何直接从嵌入式 Web 容器运行它的详细信息。要理解配置，并不需要了解我们正在构建的应用程序的来龙去脉——这就是我们首先讨论此设置的原因。这里我们首先关注通用的 Spring MVC/Web Flow 配置。正如“欢迎”部分所解释的，我们特别注意尽可能完整地说明本书中的代码，以便你无需访问示例应用程序就能理解正在发生的事情。



#### 依赖项

首先，你需要将 Spring Web Flow 添加到构建和部署路径中。使用 Gradle 时（如我们的示例所示），只需在 `build.gradle` 中声明对以下构件的依赖即可：

`compile "org.springframework.webflow:spring-webflow:$springSwfVersion"`
`compile "org.springframework.webflow:spring-js:$springSwfVersion"`
`compile "org.springframework.webflow:spring-binding:$springSwfVersion"`

你可以在 `chapter10-bookstore` 示例中的 `build.gradle` 文件中找到这段代码。该依赖是在项目级别声明的（而非根目录的 `build.gradle`）。这样做的原因是，本书之前的示例项目并未使用 Web Flow。如果在根目录的 `build.gradle` 文件中声明依赖，将隐式地意味着所有这些项目都包含不必要的 Web Flow 依赖。通过仅在 Web Flow 示例的 `build.gradle` 中专门声明 Web Flow 依赖，我们只在准备讨论 Web Flow 主题时才引入该依赖，从而限制了它的范围。版本变量维护在 `/samples` 目录根目录下的 `build.gradle` 中。这是一种管理核心库版本的简洁方式。对于单体结构项目（其中每个子项目都应使用给定库的相同版本）来说，这种方法非常有效。

这将提供开始使用 Web Flow 所需的所有依赖项（包括传递依赖项）。当然，你还需要本书前面章节介绍的 Spring 和 Spring MVC 依赖项。在撰写本文时，最新的 Web Flow 2 版本是 2.3.1。请查看 SpringSource Web Flow^(10) 网站，以确保在开始在你的应用程序中使用 Web Flow 时没有更新的版本可用。

#### Web Flow 配置

这里我们将首先解释 Web Flow 特定配置所需的内容。它由引导 Web Flow 所需的组件组成。我们将首先描述 Web Flow 本身需要什么。例如，我们将介绍如何配置流程执行器和流程注册表。流程执行器是实际运行我们流程的 Web Flow 引擎，而流程注册表将查找并解析我们的流程定义。我们还将了解可用于自定义配置的流程构建器服务。最后，我们需要一个独立的配置片段，将 Web Flow 配置链接到 Spring MVC 框架中。这个“粘合”配置将在“Spring MVC 粘合配置”部分中描述。

##### 核心配置

通过使用 Spring 所谓的 *命名空间配置*，核心 Web Flow 配置得以简化。这意味着你将使用 Spring Web Flow 命名空间中的组件来配置 Web Flow（参见清单 10-3）。其背后的机制将创建引导它所需的所有 bean。

____________

¹⁰[`http://www.springsource.org/webflow`](http://www.springsource.org/webflow)

所有这些都在一个 Spring XML 配置文件中进行配置。目前，还没有注解替代方案。Spring 配置文件看起来是标准的；但在本例中，我们添加了一个额外的命名空间，其中包含 Web Flow 特定的配置元素和属性。此外，该命名空间带有 `webflow` 前缀；我们将使用此前缀来引用 Web Flow 配置命名空间中的元素。

***清单 10-3.** src/main/resources/spring/webflow-config.xml 和 Web Flow 配置命名空间*

`<beans xmlns=http://www.springframework.org/schema/beans`

`    xsi:schemaLocation="http://www.springframework.org/schema/beans`
`        http://www.springframework.org/schema/beans/spring-beans-3.1.xsd`
`        http://www.springframework.org/schema/webflow-config`
`        http://www.springframework.org/schema/webflow-config/spring-webflow-config.xsd">`

![Image](img/square.jpg) **注意** 目前，还没有用于配置 Web Flow 的注解。然而，命名空间配置方法使配置变得相当直接。只需几行 XML，你就可以设置好 Web Flow 配置。

让 Web Flow 执行其工作的两个核心组件是 `org.springframework.webflow.executor.FlowExecutor` 和 `org.springframework.webflow.definition.registry.FlowDefinitionRegistry`。第一个组件是 Web 应用程序框架驱动流程执行的主要入口点。清单 10-4 展示了如何配置流程执行器。

***清单 10-4.** 配置流程执行器*

`    <webflow:flow-executor id="flowExecutor"/>`

可选地，你可以指定 `flow-registry` 属性，该属性默认为 id 为 `flowRegistry` 的 bean。这意味着，如果你遵循该 bean 命名约定，则无需指定此属性。如果你将 `FlowDefinitionRegistry` 命名为其他名称（例如 `myFlowRegistry`），则必须显式指定它，如清单 10-5 所示。你希望这样做的原因只有两个：

*   存在一个名称冲突的 bean。换句话说，已经有一个 bean 以 `flowRegistry` 作为其标识。
*   你没有使用命名空间配置，而是创建了一个自定义的流程注册表 bean，并为其指定了一个特定名称。例如，你可以创建一个从数据库读取流程的流程注册表，并为其指定 id `databaseReadingFlowRegistry`。在这种情况下，你可以决定给它一个不同的名称以明确其意图。

在我们的示例代码中，我们将使用默认值，并且不指定 `flow-registry` 属性。

***清单 10-5.** 配置非标准流程注册表：myFlowRegistry*

`<webflow:flow-executor id="flowExecutor" flow-registry="myFlowRegistry"/>`

流程执行器中唯一值得注意的子元素是 `<webflow:flow-execution-repository>`，它允许你自定义流程执行器的内部结构。我们将在第 12 章的“Web Flow 配置自定义”部分深入讨论这一点。

`FlowDefinitionRegistry` 负责查找和读取你创建的流程。大致来说，你有两个选项：

*   使用 `<webflow:flow-location path="" id="">` 子元素手动定义每个流程。
*   指定一个模式。Web Flow 将自动扫描流程，并在找到它们时将其添加到注册表中。

在示例中，我们选择了第二个选项（参见清单 10-6）。



***清单 10-6.** 配置流程注册表以自动扫描流程定义*

`<webflow:flow-registry base-path="/WEB-INF/view">`
`    <webflow:flow-location-pattern value="/**/*-flow.xml" />`
`</webflow:flow-registry>`

设置 `base-path` 意味着注册表将从 `/WEB-INF/view` 及其所有子目录开始查找流程。我们选择将流程与视图放在一起。不过，如果你希望将流程分开存储，也可以将 `base-path` 的值更改为其他目录。

`base-path` 有助于保持流程 ID 的简洁。流程 ID（即用于启动流程的 ID）是流程在 Web 应用程序中的位置，后跟文件名（但不包含 .xml 扩展名）。因此，位于 `/WEB-INF/view` 中的 `x.xml` 流程将像这样启动：[`http://host:port/contextRoot/x`](http://host:port/contextRoot/x)。然而，位于 `WEB-INF/view/`subdir`/y.xml` 中的 `y.xml` 流程将使用此 URL 启动：[`http://host:port/contextRoot/`](http://host:port/contextRoot/)subdir。

![Image](img/square.jpg) **注意** 最后一个 URL 并非笔误；末尾并没有缺少“y”。当流程不位于根目录时，Web Flow 会将子目录累积为流程 ID 的一部分，并在最后一个子目录处停止。在这种情况下，流程本身的名称*不*属于流程 ID。例如，位于 `WEB-INF/subdir1/subdir2/someFlow.xml` 的流程将使用此 URL 启动：[`http://host:port/contextRoot/subdir1/subdir2`](http://host:port/contextRoot/subdir1/subdir2)。虽然这听起来可能有些奇怪，但 Web Flow 鼓励你为*每个*流程创建一个唯一的子目录。建议你将流程与页面等其他工件放在一起。从这个角度来看，这样做确实是有道理的。

我们将把流程与视图存储在一起。允许将流程与视图分开存储的选项将在第 12 章的“Web Flow 配置自定义”部分讨论。

##### 流程构建器服务

通过流程构建器服务，你可以进一步配置和自定义流程注册表。（实际上，它是 flow-registry 的一个属性，正如你将在本章后面看到的那样。）仅当你需要非默认设置时才需要指定此项。由于我们有一个特殊需求——即使用 Tiles 作为模板框架——我们需要配置一个自定义的流程构建器服务（参见清单 10-7）。

***清单 10-7.** 配置流程构建器服务*

`<webflow:flow-builder-services id="flowBuilderServices"` **`view-factory-`**![Image](img/U002.jpg)
**`"creator`**`="mvcViewFactoryCreator` **`development`**`="true"/>"`

`<bean id="mvcViewFactoryCreator" class="org.springframework.webflow.mvc`![Image](img/U002.jpg)
`.builder.MvcViewFactoryCreator">`
`    <property name="viewResolvers">`
`        <list>`
`            <ref bean="tilesViewResolver"/>`
`        </list>`
`    </property>`
`</bean>`

让我们看看当前重要的两个属性（参见表 10-7）。第一个是 `view-factory-creator`，第二个是 `development`。

![Image](img/9781430241553_tab10-07.jpg)

指定流程构建器服务后，你可以将其挂接到流程注册表：

`<webflow:flow-registry base-path="/WEB-INF/view"` **`flow-builder-services="flowBuilderServices">`**
`    <webflow:flow-location-pattern value="/**/*-flow.xml" />`
`</webflow:flow-registry>`

流程构建器服务也可用于自定义其他内容。例如，你可以配置 Web Flow 以执行 JSR 303 验证（也称为 Bean 验证）。这意味着，如果你使用 `javax.constraints.validation` 包中的注解（例如 `@Min`、`@Max` 等）对模型进行注解，那么验证将会执行，并且会为模型中的该路径创建错误消息。验证将在第 11 章的“选择书籍和配送选项”部分中更详细地讨论。

执行验证后，你可以配置自定义类型转换和格式化。这些主题分别在第 11 章的“类型转换”和“类型格式化”部分中讨论。

最后，流程构建器服务还允许你切换表达式解析器。这在第 12 章的“Web Flow 配置自定义”部分中有更详细的讨论。



#### 粘合 Spring MVC 与 Spring Web Flow

我们的 Spring MVC 配置采用默认设置，其设置方式与前几章完全相同。此外，还有一个基础的 `com.apress.prospringmvc.bookstore.web.config.WebMvcContextConfiguration` 类，其开头如下：

`package com.apress.prospringmvc.bookstore.web.config;`

`import org.springframework.context.annotation.Configuration;`
`import org.springframework.web.servlet.config.annotation.EnableWebMvc;`
`import org.springframework.context.annotation.ComponentScan;`
`//为简洁起见，省略了其他导入`

`@Configuration`
`@EnableWebMvc`
`@ComponentScan(basePackages = { "com.apress.prospringmvc.bookstore.web" })`
`public class WebMvcContextConfiguration extends WebMvcConfigurationSupport{`

在该类内部，我们为以下内容添加了 Bean：

*   `本地化（messagesource）`
*   `标准视图解析器`
*   `Tiles 视图解析器`

我们还拥有 `com.apress.prospringmvc.bookstore.web.interceptor.CommonDataHandlerInterceptor`，用于支持右侧面板中的随机图书功能。然而，与前几章相比，这并非新内容（在第 6 章的“配置拦截器”一节中已有说明）。同样，还有 `org.springframework.web.servlet.i18n.LocaleChangeInterceptor`，用于在页面上动态切换语言（在第 5 章的“LocaleChangeInterceptor”一节中已有说明）。我们不再进一步讨论此类，因为它与前几章完全相同。

在本章后续部分，我们将使用与 Spring MVC 定义和使用的相同的 Tiles 视图解析器来配置 Web Flow。关键在于，我们仍然可以按原样使用 Spring MVC，而无需添加 Web Flow 功能。在这种情况下，Spring MVC 将正常工作，并且应该具备解析其视图的能力。

在普通的 Spring MVC 和 Web Flow 之间进行选择，发生在 Spring MVC 的主入口点：`org.springframework.web.servlet.DispatcherServlet`。通过使用 `org.springframework.web.servlet.HandlerAdapter` 映射，Spring MVC 能够将请求转发到其自身的某个 `HandlerAdapter`，或转发到 Web Flow 的 `org.springframework.webflow.mvc.servlet.FlowHandlerAdapter`（参见图 10-8）。

此机制是专门构建的，以便 Spring MVC 的 `DispatcherServlet` 仍然是您的单一入口点。根据请求的不同进行区分，并将请求转发到 Web Flow 或 Spring MVC 本身（后者可能是一个资源，甚至是对控制器的调用）。对于 Web Flow，有一个 `org.springframework.webflow.mvc.servlet.FlowHandlerMapping` 注册到 `DispatcherServlet`。此 `FlowHandlerMapping` 将检测 URL 是否指向现有的流程 ID。如果是，则该请求被视为 Web Flow 请求；否则，下一个 `org.springframework.web.servlet.HandlerMapping` 将尝试识别请求类型。

![Image](img/9781430241553_Fig10-10.jpg)

***图 10-10.** Spring MVC 的可配置 HandlerAdapter 机制，用于转发请求*

您可以看到 `DispatcherServlet` 使用 `HandlerAdapter` 和 `HandlerMapping` 将请求发送到特定部分——在我们的案例中，是发送到 Web Flow 或 Spring MVC。这两个组件（即 `FlowHandlerMapping` 和 `FlowHandlerAdapter`）需要在 Web Flow 能够从 `DispatcherServlet` 接收请求之前进行注册。

我们在 `com.apress.prospringmvc.bookstore.web.config.WebflowContextConfiguration` 中声明了这些组件。此类执行两项操作：

*   它使用 `@ImportResource("classpath:/spring/webflow-config.xml")` 注解将 `webflow-config.xml`（如上一节所述）导入到 ApplicationContext 中。
*   它定义并配置了两个具有以下 ID 的 Bean：`flowHandlerAdapter` 和 `flowHandlerMapping`。

在清单 10-8 中，我们展示了 `WebflowContextConfiguration` 的内容。此清单将所有组件整合在一起，并有效地充当了 Web Flow 和 Spring MVC 之间的粘合剂。

***清单 10-8.** 添加 FlowHandlerAdapter 和 FlowHandlerMapping Bean*

`package com.apress.prospringmvc.bookstore.web.config;`

`import org.springframework.web.servlet.DispatcherServlet;`
`import org.springframework.web.servlet.i18n.LocaleChangeInterceptor;`
`import org.springframework.webflow.definition.registry.FlowDefinitionRegistry;`
`import org.springframework.webflow.executor.FlowExecutor;`
`import org.springframework.webflow.mvc.servlet.FlowHandlerAdapter;`
`import org.springframework.webflow.mvc.servlet.FlowHandlerMapping;`
`import com.apress.prospringmvc.bookstore.web.interceptor.`![Image](img/U002.jpg)
`         CommonDataHandlerInterceptor;`
`//为简洁起见，省略了其他导入`

`@Configuration`
`@ImportResource("classpath:/spring/webflow-config.xml")`
`public class WebflowContextConfiguration {`

`    @Autowired`
`    private FlowExecutor flowExecutor;`
`    @Autowired`
`    private FlowDefinitionRegistry flowRegistry;`
`    @Autowired`
`    private CommonDataHandlerInterceptor commonDataHandlerInterceptor;`
`    @Autowired`
`    private LocaleChangeInterceptor localeChangeInterceptor;`

`    @Bean`
`    public FlowHandlerAdapter flowHandlerAdapter() {`
`        FlowHandlerAdapter flowHandlerAdapter = new FlowHandlerAdapter();`
`        flowHandlerAdapter.setFlowExecutor(flowExecutor);`
`        return flowHandlerAdapter;`
`    }`

`    @Bean`
`    public FlowHandlerMapping flowHandlerMapping() {`
`        FlowHandlerMapping flowHandlerMapping = new FlowHandlerMapping();`
`        flowHandlerMapping.setInterceptors(new Object[] { commonDataHandlerInterceptor,`![Image](img/U002.jpg)
` localeChangeInterceptor });`
`        flowHandlerMapping.setFlowRegistry(flowRegistry);`
`        return flowHandlerMapping;`
`    }`
`  }`
`}`

![Image](img/square.jpg) **注意** 当我们讨论 `WebMvcContextConfiguration` 时，我们说过它声明了两个拦截器，就像在前几章 Spring MVC 中所做的那样。这些拦截器是用于随机图书的 `CommonDataHandlerInterceptor`，以及用于支持动态语言切换的 `LocaleChangeInterceptor`。在 `WebflowContextConfiguration` 中，我们重复了此映射，将相同的拦截器引用传递给 `FlowHandlerMapping`。这是因为这两个系统是相互分离的。一旦 Web Flow 处理请求，它便不知道添加到 Spring MVC 处理程序映射中的拦截器，反之亦然。如果您没有在此处添加这些拦截器，那么从您启动流程的那一刻起，更改语言或显示随机图书的功能将不再有效。

在前面的章节中，我们介绍了配置 Web Flow 的不同配置细节。我们还研究并解释了如何将 Web Flow 与 Spring MVC 集成。在下一节中，我们将重点构建一个使用 Web Flow 的可运行示例。



### 构建你的第一个流程

在本节及后续内容中，我们将使用 Web Flow 构建书店示例应用的一小部分。我们已经了解了构成流程的一些重要元素。现在，我们将利用它们构建一个完整的可运行流程。为了让您熟悉构建第一个流程所需的基本模块，我们将逐步构建它，并详细描述每个部分。最后，我们将展示完整的组装流程。接下来的章节将包含大量代码片段，因此如果您在阅读过程中感到困惑，不妨先查看“概述”部分中列出的完整流程。

我们将实现“创建订单”用例。该用例允许用户订购图书。第一个屏幕显示一个用于选择类别的下拉列表，而下一个屏幕允许用户选择属于该选定类别的图书。可以将一本或多本不同的图书添加到列表中。接着，用户可以输入订单详细信息，例如交货日期。当用户确认后，订单即生成。用户还可以在不同阶段取消该流程。此流程包含以下操作/页面：

1.  用户从导航栏中选择“购买图书”链接。
2.  必须选择一个类别（页面 1）。
3.  必须显示该类别下的图书（页面 2）。
4.  用户可以选择一本或多本不同的图书，并将其添加到已选图书列表中。
5.  用户可以输入一些订单详细信息，例如交货日期（页面 3）。
6.  订单被提交。

请参见图 10-11 中创建订单的流程图；表 10-8 解释了流程步骤。

![Image](img/9781430241553_Fig10-11.jpg)

***图 10-11.** 示例应用实现的流程*

![Image](img/9781430241553_tab10-08.jpg)

我们可以总结如下：

*   在此示例应用中，我们不执行任何形式的身份验证。但由于需要将订单关联到用户，每个用户在进入 Web 应用时都会自动通过身份验证。示例中已为您完成了此操作。我们在此不讨论这一点，因为暂时将这部分流程从您的控制中剥离。我们将在下一章进行解释。
*   订单生成后，用户将被送回首页。

![Image](img/square.jpg) **注意** 我们在此 Spring Web Flow 示例中用于选择图书和创建订单的方法，与之前讨论的 Spring MVC 方法有所不同。我们特意省略了购物车，并将图书选择和订单流程转变为更基于流程的方法。这在解释不同的 Web Flow 元素时，更能满足我们的需求。

#### 创建首页

在开始构建流程之前，我们首先要介绍用户如何进入应用。换句话说，我们想回顾一下用户在输入书店 URL 时看到的内容。

首先显示的页面是所谓的首页。它由中间没有内容（内容区域）的模板组成。此页面允许用户从导航菜单中选择一个操作。让我们快速了解一下这是如何实现的。

控制器 `com.apress.prospringmvc.bookstore.web.controller.MainController` 是一个普通的 Spring MVC 控制器，通过 `@Controller` 注解定义为 Spring Bean。该控制器的目标很简单，就是渲染首页，其物理文件是 `main.jsp`。控制器使用 `@RequestMapping("index.htm")` 来实现这一点。该控制器定义在清单 10-9 中。

***清单 10-9.** 主控制器*

`package com.apress.prospringmvc.bookstore.web.controller;`

`import org.springframework.stereotype.Controller;`
`import org.springframework.web.bind.annotation.RequestMapping;`
`import org.springframework.web.servlet.ModelAndView;`

`@Controller`
`public class MainController {`
`    @RequestMapping("index.htm")`
`    public ModelAndView main() {`
`        ModelAndView mov = new ModelAndView();`
`        mov.setViewName("main");`
`        return mov;`
`    }`
`}`

视图通过 Tiles 渲染。如果您打开位于 `/WEB-INF/tiles/tiles-configuration.xml` 的 `tiles-configuration.xml`，您会看到 `main` 视图继承了 `fullTemplate`，并将 `main.jsp` 设置为内容面板（参见清单 10-10）。

***清单 10-10.** Tiles 配置*

`<definition name="main" extends="fullTemplate">`
`    <put-attribute name="content" value="/WEB-INF/view/main.jsp"/>`
`</definition>`

要访问应用并显示此首页，您只需在浏览器中指向 [`http://localhost:8080/chapter10-bookstore/`](http://localhost:8080/chapter10-bookstore/)。得益于 `index.jsp`（它会被自动识别为 Web 应用的欢迎文件），欢迎屏幕会显示出来。这个 `index.jsp` 会在内部转发到 `index.htm` 请求映射，`MainController` 正在监听该映射，从而触发视图渲染过程。请注意，欢迎文件必须是一个物理文件。它不能是一个由 Spring MVC 解析的虚构视图名称（例如我们的 `index.htm`）。为了实现这一点，我们在 `webapp` 文件夹下直接放置了一个 `index.jsp` 文件，如清单 10-11 所示。该文件夹可直接被浏览器访问。Tomcat（或 tc Server）默认会在此处查找 `index.jsp` 欢迎文件。请记住，`index.jsp` 位于我们的 `WEB-INF` 目录之外（在 STS Java 视图中，它位于 `/webapp` 目录下），因此外部客户端可以直接访问它。这与我们其他视图的 JSP 不同，它们位于 `WEB-INF/view` 下，外部客户端无法直接访问。

***清单 10-11.** 转发到内部 Tiles 组合主视图的 index.jsp*

`<!DOCTYPE HTML>`

`<%@ page pageEncoding="UTF-8" contentType="text/html; charset=UTF-8"%>`

`<jsp:forward page="/index.htm" />`

![Image](img/square.jpg) **注意** 您可以通过首先进入工作区服务器主目录来检查欢迎文件的默认设置。可以通过双击“Servers”项目（在 Java 或 Java EE 透视图下），然后双击服务器实例（默认名为“VMware vFabric tc Server Developer Edition v2.6-config”）来找到主目录。在其中，您可以打开 `web.xml`。此文件中的条目是将在该服务器实例上部署的每个 Web 应用的默认设置。在文件末尾，您会找到 `welcome-file-list`，它列出了默认被识别为欢迎文件的所有文件（及其扩展名）。

#### 实现创建订单流程

*要根据所需的用例实现我们的流程，我们需要三个主要元素：*

*   一个 `<on-start>` 元素，用于初始化我们的模型和可选择的类别
*   三个视图状态，每个页面一个
    *   每个视图状态中的转换，用于导航返回/取消/前进
    *   一些评估操作
    *   准备我们的可选图书列表
    *   将图书添加到已选图书列表
    *   清空已选图书列表
    *   在最后一步创建我们的订单
*   一个结束状态，用于终止我们的流程

我们决定开始逐页构建示例。在接下来的章节中，我们将构建这三个页面（按照图 10-5 所示的顺序）以及所需的流程组件。让我们从构建允许用户选择类别的页面开始。



##### 选择类别

首先，我们需要初始化模型并获取可供用户选择的类别。该模型是 `com.apress.prospringmvc.bookstore.web.controller.OrderForm`。在整个流程中，我们将使用此模型来收集用户的输入。类别是流程第一步所必需的，它们会以下拉列表的形式呈现给用户，供其选择。这些类别需要先从数据库中检索出来。

![Image](img/square.jpg) **注意** 我们在此构建的流程是 `createOrders-flow.xml`，它位于 `WEB-INF/view/createOrders/` 目录下。我们即将创建的页面以及流程文件也将放置在此目录中。

在本章前面部分，我们介绍了 `<on-start>` 元素，它允许你在流程初始启动时执行逻辑。该逻辑会在流程进入任何其他状态之前执行。该动作应出现在任何其他状态之前，并且在流程中只能出现一次。这对于我们的初始化工作来说非常完美（参见代码清单 10-12）。

***代码清单 10-12.** 创建订单流程中的 <on-start> 元素*

`<on-start>`
`    <evaluate expression="orderController.initializeForm()"`![Image](img/U002.jpg)
`        result="flowScope.orderForm" />`
`    <evaluate expression="orderController.initializeSelectableCategories()"`![Image](img/U002.jpg)
`        result="flowScope.selectableCategories"/>`
`</on-start>`

第一个求值动作将初始化我们的表单并设置一些初始值（此代码在 `com.apress.prospringmvc.bookstore.web.controller.OrderController` 上执行）。它调用了代码清单 10-13 中所示的方法。初始化后的 `OrderForm` 随后被设置到流程作用域中。`flowScope` 是一个用于访问流程作用域的隐式变量。（作用域将在下一章的“不同 Web 流程作用域”部分进行解释；同样，你可以在下一章的“隐式变量”部分了解隐式变量）。

***代码清单 10-13.** 从 <on-start> 元素调用的、用于初始化表单的方法*

`public OrderForm initializeForm() {`
`    OrderForm orderForm = new OrderForm();`
`    orderForm.setQuantity(1);`
`    orderForm.setOrderDate(simpleDateFormat.format(new Date()));`
`    return orderForm;`
`}`

我们可以在一个 on-start 元素中指定多个求值动作，这些动作将按顺序执行。如果其中一个求值动作导致异常，则处理过程停止，并且不会发生任何状态转换。第二个求值动作会将一个可选类别的映射（`com.apress.prospringmvc.bookstore.domain.Category`）放入流程作用域。为简单起见，我们使用了一个键类型为 `Long`（每个类别记录的代理键）、值类型为 `String`（类别名称）的映射，如代码清单 10-14 所示。稍后我们将看到如何直接使用 Category 对象，而不是先将它们转换为字符串。

***代码清单 10-14.** 从 on-start 调用的、用于初始化类别的方法*

`public Map<Long, String> initializeSelectableCategories() {`
`    Map<Long, String> selectableCategories = new HashMap<Long, String>();`
`    for (Category category : categoryService.findAll()) {`
`        selectableCategories.put(category.getId(), category.getName());`
`    }`
`    return selectableCategories;`
`}`

![Image](img/square.jpg) **注意** 我们明确地将类别与 `OrderForm` 分开存储。我们本可以将它们保存在表单本身中，但为了不让表单过于臃肿，我们将其分开了。

接下来，我们将创建视图状态，如代码清单 10-15 所示。

***代码清单 10-15.** selectCategory 视图状态*

`<view-state id="selectCategory" view="selectCategory" model="orderForm">`
`    <transition on="next" to="selectBooks">`
`    <evaluate expression="orderController.initializeSelectableBooks(flowScope.orderForm)"`![Image](img/U002.jpg)
` result="flowScope.selectableBooks"/>`
`    </transition>`
`    <transition on="cancel" to="end" />`
`</view-state>`

此视图状态将渲染 `selectCategory` 视图。这是 `tiles-configuration.xml` 中的一个定义条目，它扩展自完整模板（参见代码清单 10-16）。

***代码清单 10-16.** 选择类别视图的 Tiles 配置*

`<definition name="selectCategory" extends="fullTemplate">`
`    <put-attribute name="content" value="/WEB-`![Image](img/U002.jpg)
`      INF/view/createOrders/selectCategory.jsp"/>`
`</definition>`

视图状态将使用我们之前在 `<on-start>` 元素中初始化的 `orderForm`（如 `model` 属性所示）。Web Flow 会自动将每个请求参数绑定到指定的表单。正如我们将在第 11 章的“显式绑定”部分中看到的，这可以被覆盖。请记住：我们尽可能保持此示例的简单性；因此，我们暂时不考虑显式绑定、验证和转换。我们将在后续章节中讨论这些问题。

我们的视图需要某种控制组件来触发 `next` 或 `cancel` 转换。`next` 转换将把我们带到下一个视图状态。我们已经将其命名为 `selectBooks`。然而，在进行转换之前，我们需要准备所选类别中可用的书籍列表。我们通过调用 `OrderController` 上的一个方法来实现这一点。该方法将使用所选类别来加载该类别下可用的书籍。结果是一个与类别映射结构相同的映射；它也被设置到流程作用域中（参见代码清单 10-17）。

***代码清单 10-17.** 可选书籍列表的初始化器*

`public Map<Long, String> initializeSelectableBooks(OrderForm orderForm) {`
`    orderForm.getSelectedBooks().clear();`
`    orderForm.resetSelectedBooks();`

`    Map<Long, String> selectableBooks = new HashMap<Long, String>();`
`    for (Book book : bookstoreService.findBooksByCategory(categoryService.findById`![Image](img/U002.jpg)
`(orderForm.getCategoryId()))) {`
`        selectableBooks.put(book.getId(), book.getTitle());`
`    }`
`    return selectableBooks;`
`}`

将呈现给用户并驱动前述视图状态中转换的页面位于 `WEB-INF/view/createOrders/selectCategory.jsp`，如代码清单 10-18 所示。

***代码清单 10-18.** 选择类别页面的实际内容*

`<form:form modelAttribute="orderForm" action="${flowExecutionUrl}">`
`    <table style="width: 100%">`
`        <tr>`
`             <td width="30%">`
`                 <spring:message code="label.page.category"/></td>`
`             <td>`
`<form:select path="categoryId"` ![Image](img/U002.jpg)
`                     items="${selectableCategories}"/>`
`             </td>`
`        </tr>`
`    </table>`

`    <div align="right" style="margin-bottom: 20px; margin-top: 10px" >`
`        <button type="submit" id="return" name="_eventId_cancel">`
`            <spring:message code="button.cancel"/>`
`        </button>`
`    <button type="submit" id="continue" name="_eventId_next">`
`            <spring:message code="button.next"/>`
`        </button>`
`    </div>`
`</form:form>`



请注意，我们使用的是 Spring MVC 表单。如果我们后续需要为特定输入字段绑定错误信息，这将非常有用。我们的 action（`${flowExecutionUrl}`）是一个特殊的隐式对象，它指向当前的流程执行。这相当于当前 URL 加上流程执行键。您应将其视为当前运行流程的回发 URL。在最终渲染的 HTML 中，它可能如下所示：

`action="/chapter10-bookstore/createOrders?execution=e4s1"`

流程执行键将允许 Web Flow 在返回响应（即暂停流程之前）后，重新加载之前保存的状态。

`<form:select>` 元素是 Spring MVC 标签库中用于渲染 HTML 选择列表的基本标签。它可以与 Map 完美配合；在这种情况下，它会将 Map 的键作为 HTML option 元素的值（即作为表单提交的一部分发送的文本），并将 Map 的值作为 HTML option 的内容（即列表中显示的文本）。

最后，还有两个按钮。这些是驱动流程转换的控制组件。如果您查看其名称，会发现它们以转换的 `on` 值结尾，并带有 `_eventId_` 前缀。Web Flow 正是通过这种方式来识别要执行哪个转换。提交表单时，按钮的名称会随请求一起发送。其模式为 `_eventId_<`*`on_transition`*`>`。

哦，等等；还有一件事要做。如图 10-10 的主页所示，我们的想法是通过一个 `Buy books` 链接来启动流程。我们还需要在导航栏中配置此链接以启动流程。

要找到这个链接，我们必须打开 `header.jsp`，该文件位于 `WEB-INF/templates/` 目录下。请记住，我们使用 Tiles 进行模板化。一个页面由页眉、内容和页脚面板组成。菜单作为 `header.jsp` 的一部分进行渲染（参见代码清单 10-19）。

***代码清单 10-19** 导航栏中用于启动创建订单流程的链接*

`<li>`
`    <spring:url value="/createOrders" var="createOrder" />`
`    <a href="${createOrder}">Buy books</a>`
`</li>`

在我们的例子中，流程文件位于 `(WEB-INF/view)/createOrders/createOrders-flow.xml` 目录下，以及流程中使用的页面文件也在此目录下。创建的 URL 只是在基础 URL 后附加了 `"/createOrders"`。正如我们在“核心配置”部分所见，`WEB-INF/view` 目录下的流程可以直接通过其所在的目录结构进行访问。

![Image](img/square.jpg) **注意** 您可能会疑惑，为什么我们不直接将 `<spring:url>` 嵌入到 `href` 属性中，而是使用一个变量。我们本可以这样做；虽然这种写法稍微复杂一些，但也更具可读性。我们就说这是个人风格和喜好的问题吧。

至此，您应该可以测试应用程序了。打开浏览器，访问 [`http://localhost:8080/chapter10-bookstore/`](http://localhost:8080/chapter10-bookstore/)。在主页上，您现在可以点击 `Buy books` 链接。这样做应该会显示如图 10-12 所示的视图。

![Image](img/9781430241553_Fig10-12.jpg)

***图 10-12.** 在浏览器中渲染的类别选择页面*

点击“下一步”按钮会产生错误，因为我们尚未实现下一个视图状态。但是，点击“取消”按钮应该会将我们带回主页。这是通过转换到结束状态来实现的。目前，只需知道结束状态会简单地结束流程执行，并最终重定向到我们配置的视图即可。如前所述，我们将在第 11 章中详细研究 `<end-state>` 元素。

至此，选择类别的部分就介绍完了，接下来我们继续介绍选择和添加书籍。

##### 选择和添加书籍

接下来，我们需要构建一个页面，向用户展示之前所选类别下的书籍列表。用户应该能够将选中的书籍添加到订单列表中，并且应该有一个输入字段来指定用户想要添加的书籍数量。

在我们之前的视图状态中，在转换到我们将要构建的视图状态之前，我们已经将书籍加载到了流程作用域下的 `selectableBooks` 键中——所以这部分工作已经完成。剩下的工作就是由我们来指定视图状态，并定义所有可用的转换，以处理用户选择书籍并将其添加到列表这一用例部分。在本节中，我们将继续描述实现此功能所需的功能。接下来，我们将放置实际的转换代码，以便您了解其具体形式。最后，我们将给出整个视图状态的概览。

让我们从实现一个能让我们返回上一页的功能开始（参见代码清单 10-20）。

***代码清单 10-20.** 转换到上一个视图状态*

`<transition on="previous" to="selectCategory" />`

`to` 属性是上一个视图状态的 `id`。因此，当请求参数中包含 `_eventId_previous` 时，Web Flow 会自动将我们带回上一个视图状态。上一个视图及其数据将会被显示。

我们应该能够从可用书籍列表中选择一本书，然后将该书添加到存储在表单中的已选书籍列表中（参见代码清单 10-21）。

***代码清单 10-21.** 将选中的书籍添加到已选书籍列表的转换*

`<transition on="add" >`
`    <evaluate expression="orderController.addBook(flowScope.orderForm)" />`
`</transition>`

上述代码片段将调用 `OrderController` 中代码清单 10-22 所示的方法。

***代码清单 10-22.** 执行控制器代码以将书籍添加到已选书籍列表*

`public void addBook(OrderForm orderForm) {`
`    Book book = bookstoreService.findBook(orderForm.getBookId());`
`    if (orderForm.getSelectedBooks().containsKey(book)) {`
`orderForm.getSelectedBooks().put(book,` ![Image](img/U002.jpg)
`        orderForm.getSelectedBooks().get(book) + orderForm.getQuantity());`
`    } else {`
`        orderForm.getSelectedBooks().put(book, orderForm.getQuantity());`
`    }`
`}`

我们将使用 OrderForm 来获取选中的书籍 `id`。当表单提交时（即执行 `add` 转换时），Web Flow 会将其绑定到表单上。我们检索到该书，并将其连同指定的数量一起添加到已选书籍的 Map 中。

另外，请注意此转换没有 `to` 属性。正如我们之前所见，此属性是可选的。当未指定时，将再次渲染相同的视图状态。这正是我们想要的；添加一本书后，我们希望停留在同一页面，并向用户显示新书已添加到列表中。

一旦某些书籍被添加到列表中，用户可能想要重新开始，从而清除当前已选的书籍。代码清单 10-23 显示了转换，代码清单 10-24 显示了清除列表的代码。

***代码清单 10-23.** 清除已选书籍列表的转换*

`<transition on="reset" to="selectBooks">`
`    <evaluate expression="orderForm.resetSelectedBooks()" />`
`</transition>`

***代码清单 10-24.** 实际清除列表的代码*

`public void resetSelectedBooks() {`
`    selectedBooks.clear();`
`}`

与之前的视图状态一样，用户必须能够通过点击“取消”按钮来退出当前的订单流程（参见代码清单 10-25）。

***代码清单 10-25.** 取消当前订单并进入结束状态*

`<transition on="cancel" to="end" />`



用户还应能够前进到最终页面（参见清单 10-27）。

***清单 10-26.** 前进到下一个视图以选择配送选项*

`<transition on="next" to="selectDeliveryOptions" />`

最后，清单 10-28 展示了我们的视图状态将如何呈现。

***清单 10-27.** selectBooks 视图状态概览*

`<view-state id="selectBooks" view="selectBooks" model="orderForm">`

`   <transition on="previous" to="selectCategory"/>`

`    <transition on="add" >`
`        <evaluate expression="orderController.addBook(flowScope.orderForm)" />`
`    </transition>`

`    <transition on="next" to="selectDeliveryOptions" />`

`    <transition on="reset" to="selectBooks">`
`        <evaluate expression="orderForm.resetSelectedBooks()" />`
`    </transition>`

`    <transition on="cancel" to="end" />`
`</view-state>`

在清单 10-28 中，你可以看到与 `selectBooks` 视图对应的 Tiles 定义。

***清单 10-28.** 选择书籍视图的 Tiles 定义*

`<definition name="selectBooks" extends="fullTemplate">`
`    <put-attribute name="content" value="/WEB-INF/view/createOrders/selectBooks.jsp"/><`
`/definition>`

接下来，我们将创建页面（参见清单 10-29），该页面由三部分组成。你可以在 `/WEB-INF/view/createOrders/selectBooks.jsp` 找到此页面。第一部分允许你选择书籍、输入数量并将其添加到已选书籍列表中。你也可以清空列表。通过点击“添加”和“重置”按钮来调用此功能，这些按钮将分别触发 `add` 和 `reset` 转换。页面的第二部分是列表本身；它显示当前已选书籍及其数量。最后，页面的第三部分显示用于驱动其余转换的按钮：`previous`、`cancel` 和 `next`。

***清单 10-29.** 已选书籍页面*

`<form:form modelAttribute="orderForm" action="`**`${flowExecutionUrl}`**`">`
`    <table style="width: 100%">`
`        <tr style="height: 10px;"/>`
`        <tr>`
`            <td>`
`                <spring:message code="label.page.books.select.book" />`
`            </td>`
`            <td>`
**`<form:select path="bookId" items="${selectableBooks}"/>`**
`            </td>`
`        </tr>`
`        <tr>`
`            <td>`
`                <spring:message code="label.page.books.select.quantity"/>`
`            </td>`
`            <td>`
`                <form:input path="quantity" />`
`            </td>`
`        </tr>`
`        <tr height="10px"/>`
`        <tr align="right">`
`            <td colspan="2">`
`                <button type="submit" id="add" name="_`**`eventId_add`**`">`
`                    <spring:message code="label.page.books.add.book"/>`
`                </button>`
`                <button type="submit" id="cancel" name="_`**`eventId_reset`**`">`
`                    <spring:message code="button.reset"/>`
`                </button>`
`            </td>`
`        </tr>`
`    </table>`
`    <p/>`
`    <h3><spring:message code="label.page.books.selected.books"/></h3>`
`    <div style="margin-top: 10px; margin-bottom: 10px;">`
`        <table style="width: 100%;" rules="groups">`
`            <thead>`
`                <tr>`
`                    <th width="80%" align="left">`
`                        <spring:message code="label.page.books.book.name"/>`
`                    </th>`
`                    <th width="20%" align="left">`
`                    <spring:message code="label.page.books.book.quantity"/>`
`                    </th>`
`                </tr>`
`            </thead>`
`            <tbody>`
`                <tr height="10px"/>`
**`<c:forEach items="${orderForm.selectedBooks}" var="book">`**
`                        <tr>`
`                            <td>${book.key.title}</td>`
`                            <td>${book.value}</td>`
`                        </tr>`
`                    </c:forEach>`
`                <tr height="20px"/>`
`            </tbody>`
`        </table>`
`    </div>`
`    <div align="right" style="margin-bottom: 20px;" >`
`        <button type="submit" id="previous" name="_`**`eventId_previous`**`">`
`            <spring:message code="button.previous"/>`
`        </button>`
`        <button type="submit" id="previous" name="_`**`eventId_cancel`**`">`
`            <spring:message code="button.cancel"/>`
`        </button>`
`        <button type="submit" id="next" name="_`**`eventId_next`**`">`
`            <spring:message code="button.next"/>`
`        </button>`
`    </div>`
`</form:form>`

关于上述清单，有几点关键内容需要注意，特别是以粗体显示的部分：

*   **${flowExecutionUrl}**：当表单提交时，它最终会在回发 URL 中呈现，以指向我们的流程执行。
*   **<form:select path="bookId" items="${selectableBooks}"/>:** 这将把我们的 `selectableBooks` 列表渲染为一个 HTML 下拉列表。
*   **eventId_add 和 eventId_reset:** 点击时，它们将分别触发 `add` 和 `reset` 转换。
*   **<c:forEach items="${orderForm.selectedBooks}" var="book">:** 这是一个标准的 JSTL `forEach` 标签，它将遍历我们可用的书籍，并在 HTML 表格中渲染它们。
*   **eventId_previous、eventId_cancel 和 eventId_next:** 点击时，它们将分别触发 `previous`、`cancel` 和 `next` 转换。

实现清单 10-30 后，我们重新启动流程，选择类别，然后点击“下一步”按钮。如果我们添加 *敏捷软件开发：原则、模式与实践* 这本书 11 次，我们将看到如图 10-13 所示的视图。

![Image](img/9781430241553_Fig10-13.jpg)

***图 10-13.** 浏览器渲染的选择书籍视图*



##### 指定订单详情并创建订单

接下来是我们实现流程的最后一部分。我们将显示订单日期（在初始化表单时已设置为当前日期）；并在其下方为用户提供一个输入字段，用于输入期望的交货日期。这是三个页面中的最后一个；此时，用户应能够返回上一页、取消订单或完成订单。当用户点击“下单！”按钮时，我们将使用表单中存储的信息创建订单，然后将订单存储到数据库中。

清单 10-30 展示了实现我们视图状态的代码。

***清单 10-30.** 选择交货选项的视图状态*

`<view-state id="selectDeliveryOptions" view="selectDeliveryOptions"`
`    model="orderForm">`
`    <transition on="previous" to="selectBooks" />`
`    <transition on="finish" to="end">`
`        <evaluate expression="orderController.placeOrder(flowScope.orderForm,`![Image](img/U002.jpg)
`           externalContext.sessionMap.authenticatedAccount)"/>`
`    </transition>`

`    <transition on="cancel" to="end" />`
`</view-state>`

第一个转换并不新鲜；它将我们带回上一个视图状态。第二个转换完成订单并转换到结束状态，我们稍后会看到。为了创建订单，我们将 `com.apress.prospringmvc.bookstore.domain.Account`（领域对象属于 `bookstore-shared` 模块）和 `OrderForm` 传递给控制器上的 `placeOrder` 方法。目前无需担心 `Account`；只需知道当用户打开 Web 应用程序时，它会自动设置到会话作用域中。请记住，对于此示例，我们尽可能保持一切简单，因此用户是自动登录的（始终使用相同的硬编码用户）。在下一章中，我们将自己实现身份验证，以此来说明一些新的 Web Flow 概念。

Account 标识当前登录并下订单的用户。订单将添加到该用户名下。如您所见，我们使用了另一个隐式对象 `externalContext`——这次是为了访问 `javax.servlet.Servlet` 会话作用域并从中检索 Account。会话作用域是存储身份验证等信息的理想位置，这些信息确实与会话的生命周期绑定。

`placeOrder` 方法将使用 `com.apress.prospringmvc.bookstore.domain.support` `.OrderBuilder` 来开始创建新的 `com.apress.prospringmvc.bookstore.domain.Order`（请参阅清单 10-32）。接下来，它将遍历选定的书籍，并将每一本书添加到我们的订单中。我们使用书籍 ID 从数据库中检索 `com.apress.prospringmvc.bookstore.domain.Book` 对象。

***清单 10-31.** 用于下单的控制器方法*

`public void placeOrder(final OrderForm orderForm, final Account account) throws`![Image](img/U002.jpg)
` ParseException {`
`    OrderBuilder orderBuilder = new OrderBuilder() {`
`        {`
`            deliveryDate(simpleDateFormat.parse(orderForm.getDeliveryDate()));`
`            account(account);`
`            orderDate(simpleDateFormat.parse(orderForm.getOrderDate()));`
`        }`
`    };`

`    for (Entry<Book, Integer> selectedBook : orderForm.getSelectedBooks().entrySet()) {`
`        orderBuilder.addBook(selectedBook.getKey(), selectedBook.getValue());`
`    }`

`    bookstoreService.store(orderBuilder.build(true));`
`}`

订单创建后，流程转换到结束状态，所有其他状态将被清理。然后用户被重定向到主页。我们将在下一章详细讨论结束状态。目前，只需知道它将结束我们当前的流程并执行所有清理工作即可（请参阅清单 10-33）。

***清单 10-32.** 结束我们的流程并重定向到主页*

`<end-state id="end" view="redirect:/index.htm"/>`

在清单 10-34 中，我们展示了定义 `selectDeliveryOptions` 视图的 Tiles 配置。

***清单 10-33.** 选择交货选项的 Tiles 定义*

`<definition name="selectDeliveryOptions" extends="fullTemplate">`
`    <put-attribute name="content" value="/WEB-INF/view/createOrders/`![Image](img/U002.jpg)
`selectDeliveryOptions.jsp"/>`
`</definition>`

清单 10-35 展示了选择交货选项页面，该页面位于 `/WEB-INF/view/createOrders/selectDeliveryOptions.jsp`。

***清单 10-34.** 选择交货选项页面*

`<form:form modelAttribute="orderForm" action="${flowExecutionUrl}">`
`    <table style="width: 100%">`
`        <tr>`
`            <td>`
`                <spring:message`
`                code="label.page.selectdeliveryoptions.select.order.date"/>`
`            </td>`
`            <td>`
`                <form:input path="orderDate" disabled="true" />`
`            </td>`
`        </tr>`
`        <tr>`
`            <td>`
`                <spring:message`
`                code="label.page.selectdeliveryoptions.select.delivery.date"/>`
`            </td>`
`            <td>`
`                <form:input path="deliveryDate" />`

**`<script type="text/javascript">`**
**`Spring.addDecoration(new Spring.ElementDecoration({`**
**`elementId : "deliveryDate",`**
**`widgetType : "dijit.form.DateTextBox",`**
**`widgetAttrs : { datePattern : "MM-dd-yyyy",`**
**`required : true }}));`**
**`</script>`**
`            </td>`
`        </tr>`
`    </table>`

`    <div align="right" style="margin-bottom: 20px; margin-top: 10px" >`
`        <button type="submit" id="previous" name="_eventId_previous">`
`            <spring:message code="button.previous"/>`
`        </button>`
`        <button type="submit" id="cancel" name="_eventId_cancel">`
`            <spring:message code="button.cancel"/>`
`        </button>`

`        <button type="submit" id="finish" name="_eventId_finish">`
`            <spring:message code="label.page.selectdeliveryoptions.order"/>`
`        </button>`
`    </div>`
`</form:form>`

请注意以粗体突出显示的代码。我们使用了一点 Spring JS（Spring JavaScript，基于 DOJO）来渲染日期选择器，正如您在图 10-14 的视图中看到的那样。`addDecoration` 装饰了一个现有的 HTML 元素——在本例中，其 id 为 `deliveryDate`。我们指定了小部件的类型（日期选择器），并为其提供了一些属性，例如所需的格式。这是一种客户端验证形式。在下一章中，我们还将添加匹配的服务器端验证。在第 12 章讨论 Ajax 时，我们还将讨论更多 JavaScript 选项。

![Image](img/9781430241553_Fig10-14.jpg)

***图 10-14.** 选择交货选项——我们的交货日期*

如果您想验证订单是否已创建，可以单击菜单栏中的“订单概览”链接。您会看到现在有了第六个订单，即我们在此过程中创建的订单。请注意，其他五个订单是在应用程序启动时由 `com.apress.prospringmvc.bookstore.domain.support.InitialDataSetup` 自动插入的。

![Image](img/square.jpg) **注意** 您会注意到，在不输入（必填的）交货日期的情况下单击“下单！”按钮会引发一个难看的服务器端异常。在此流程中，我们没有应用验证或任何此类功能。我们在字段上应用的装饰（日期选择器）会指示该字段是必填的；然而，不填写交货日期并不能阻止用户单击提交按钮，从而在没有交货日期的情况下提交表单。在接下来的章节中，我们将研究如何改进这一点，并显示适当的验证消息。


#### 概述

在前面的章节中，我们逐步完成了构建第一个流程的各个步骤。我们介绍了流程的每个部分，并描述了所需的其他元素，例如 JSP 页面和 tiles 配置。在结束之前，你可以在清单 10-35 中找到我们迄今为止构建的流程的完整概述。

***清单 10-35.** 第一个流程的概述：createOrders-flow.xml*

`<flow`

`    xsi:schemaLocation=`![Image](img/U002.jpg)
`        "http://www.springframework.org/schema/webflow`
`         http://www.springframework.org/schema/webflow/spring-webflow-2.0.xsd">`

`    <on-start>`
`        <evaluate expression="orderController.initializeForm()" result="flowScope.orderForm"/>`
`<evaluate expression="orderController.initializeSelectableCategories()"` ![Image](img/U002.jpg)
`                         result="flowScope.selectableCategories"/>`
`    </on-start>`

`    <view-state id="selectCategory" view="selectCategory" model="orderForm">`
`        <transition on="next" to="selectBooks" >`
`            <evaluate expression="orderController.initializeSelectableBooks`![Image](img/U002.jpg)
`(flowScope.orderForm)"                              result="flowScope.selectableBooks" />`
`        </transition>`

`        <transition on="cancel" to="end" />`
`    </view-state>`

`    <view-state id="selectBooks" view="selectBooks" model="orderForm" >`
`        <transition on="previous" to="selectCategory" />`

`            <transition on="add" >`
`                <evaluate expression="orderController.addBook(flowScope.orderForm)" />`
`            </transition>`

`            <transition on="next" to="selectDeliveryOptions" />`
`            <transition on="reset" to="selectBooks">`
`                <evaluate expression="orderForm.resetSelectedBooks()" />`
`            </transition>`

`            <transition on="cancel" to="end" />`
`    </view-state>`

`    <view-state id="selectDeliveryOptions" view="selectDeliveryOptions" model="orderForm">`
`        <transition on="previous" to="selectBooks" />`

`        <transition on="finish" to="end">`
`<evaluate expression="orderController.placeOrder(flowScope.orderForm,` ![Image](img/U002.jpg)
`                                      externalContext.sessionMap. authenticatedAccount)"/>`
`        </transition>`

`        <transition on="cancel" to="end" />`
`        </view-state>`

`    <end-state id="end" view="redirect:/index.htm"/>`
`</flow>`

### 本章小结

在本章中，我们首先从一些理论入手，探讨了 Web Flow 为何以及如何简化你的 Web 应用程序开发。我们还介绍了 Web Flow 的一些更重要的特性，例如其扩展的作用域机制、自动状态保存，以及它如何通过使用 Post Redirect Get 和请求令牌同步来帮助保护 Web 应用程序状态的完整性。

接下来，我们学习了如何配置 Web Flow，包括如何管理其特定的配置细节。我们还学习了如何将 Web Flow 与 Spring MVC 整合在一起。接着，我们介绍了在应用程序中实现基本流程所需的大部分 Web Flow 基础知识。最后，我们通过一个构建基本图书订购流程的实时代码示例，展示了 Web Flow 的一些特性。

现在，我们准备进入下一章，届时我们将更深入地探讨 Web Flow 的一些尚未涉及的特性，并利用这些特性将我们的示例应用程序转变为一个真正的 Web Flow 应用程序。

## 第 11 章

## 使用 Spring Web Flow 构建应用程序

在上一章关于 Spring Web Flow 的内容中，我们向你介绍了 Web Flow 的主要特性及其能为你做什么。我们还介绍了基本 Web Flow 配置的样子，以及它如何与 Spring MVC 集成。我们看到了几乎每个流程都包含的一些基本元素，例如视图状态和评估动作。最后，我们将书店转换为了一个支持基本 Web Flow 的应用程序。

在本章中，我们将继续探讨 Web Flow 的更多特性。例如，我们将看到将书店转变为实际应用程序所需的所有特性。我们将从上一章结束时的书店状态开始，并在此过程中逐步对其进行重构。本章的总体方法是一种混合方式；我们将首先向你介绍一些新特性，并详细解释它们。接着，我们将通过重构现有应用程序或使用这些特性来修改书店，看看如何应用这些概念。

我们选择这种方法，是为了便于继续参考目录。在使用 Web Flow 时，你可能希望回溯到某些功能并阅读其所有细节。如果我们将所有内容混合在一起，这将变得困难。我们将分两个阶段处理示例，以使一切更易于理解。

### 重要的 Web Flow 概念

既然你已经了解了 Web Flow 的一些基础知识，我们想解释一些特定的新 Web Flow 概念和术语，并进一步阐述第 10 章中引入的一些概念。具体来说，我们将扩展第 10 章中引入的“流程”定义。我们还将更深入地研究作用域及其工作原理，然后介绍并定义可用于 Web Flow 表达式的隐式对象。

#### 流程定义

在一个流程定义中，我们可以有五种状态，其中两种我们已经见过：视图状态和结束状态。其余的状态是动作状态、决策状态和子流程状态。我们将在本章中更详细地介绍这些状态。然而，在本节中，我们想对子流程做一个简短介绍，因为在我们完全介绍“子流程”部分之前，会先提到它们。子流程的概念非常简单。例如，子流程的结构和设置与其他流程没有太大区别；它在其自己的流程定义 XML 中定义，并且有自己的流程 ID。

有时，一个给定的流程是从另一个流程启动的，而不是通过直接引用它在顶层启动。我们将这样的流程称为*子流程*。这个过程在两个流程之间创建了一种父子关系。正如我们稍后将看到的，这种关系意味着这两个流程可以使用 `conversation` 作用域共享状态，以及将数据从一个流程映射到另一个流程。它也是一种模块化技术，因为它可以帮助你构建可重用的组件。

另一个驱动流程的重要元素是*事件*。用户通过将表单内容提交到 URL 来发出事件信号，该 URL 包含作为路径的流程 ID 和作为参数之一的流程执行键。其他参数可以是输入组件的值，例如文本字段、选择框、复选框等。Web Flow 将使用提交中的流程 ID 和执行键来恢复流程执行，并从提交的参数中解析出事件。请记住，在我们的示例中，事件被封装在 Submit 按钮的名称中，格式为：`_eventId_event`。解析事件后，Web Flow 将使用它来调用转换，该转换具有与我们提交的事件匹配的 `on` 属性。然后，它将被转发到由转换的 `to` 属性指示的状态。

转换将流程从一种状态带到同一流程的另一种状态。在你可以返回视图之前，你必须要么转换到另一个视图状态（可能经过其他状态），要么转换到一个结束状态。正如我们将看到的，结束状态也可以选择指定一个视图。该视图在流程执行终止后呈现。



#### 不同的 Web Flow 作用域

我们在前一章已经提到，Web Flow 在架构中引入了许多不同的作用域（确切地说是五个）：`conversation`、`flow`、`view`、`flash` 和 `request`（参见第 10 章中的“细粒度作用域”一节）。每个作用域都有其明确定义的生命周期。

![Image](img/square.jpg) **注意** 你放入 `conversation`、`flow`、`view` 或 `flash` 作用域的每个对象都应实现 `java.io.Serializable` 接口（对于 `request` 作用域，则无此要求）。这是因为 Web Flow 会在请求之间存储这些作用域的状态。默认实现依赖于 Java 序列化机制。另请注意，这是递归的；你的对象所引用的每个对象也必须是可序列化的。如果你不希望保留某些对象，可以在字段上使用 Java 的 `transient` 关键字。这样，该字段将被排除在序列化之外。当流程状态恢复时，这些字段将解析为 `null`。

接下来，我们将从最短的生命周期到最长的生命周期，详细解释这五个作用域。让我们从 `request` 作用域开始。

##### Request 作用域

图 11-1 展示了两个 HTTP 请求进入视图状态：HTTP 请求 1 和 HTTP 请求 2。虽然它们显示在同一张图中，但应被理解为顺序发生的。首先，HTTP 请求 1 进入并被处理，然后返回响应。其次，HTTP 请求 2 进入，并针对该请求重复此循环。当第一个请求进入时，`request` 作用域 1 被启动。可能会触发一个事件，并执行一次转移。任何执行的操作都可以访问该 `request` 作用域。当处理完成时，`request` 作用域 1 被销毁，并返回 HTTP 请求 1 的响应。当下一个请求（HTTP 请求 2）到来时，会创建一个*新的* `request` 作用域（请求作用域 2）。

![Image](img/9781430241553_Fig11-01.jpg)

***图 11-1.** 说明 request 作用域*

![Image](img/square.jpg) **注意** 我们想指出，从现在开始，在“作用域图”中，花括号表示该时刻可用的作用域。某个作用域的创建和清理由花括号本身表示；在花括号的顶部和底部之间，该作用域对任何试图使用它的执行过程都是可用的。

`request` 作用域与流程执行中的单个请求相关联。进入流程执行的每一个请求都将从一个新的、空的 `request` 作用域开始。

认识到 `request` 作用域本身并不与某个流程执行绑定，这一点很重要。因此，执行子流程的父流程（反之亦然）始终看到的是同一个 `request` 作用域。你可以使用 `request` 作用域来存储仅在处理当前请求时才需要的对象。创建或加载成本较低的对象也可以放入 `request` 作用域，因为它们可以在每次需要时重新创建或重新加载。

例如，如果你需要加载一个结果列表，你可以通过一个 `<on-render>` 动作来加载它们（我们稍后会解释）。`<on-render>` 在视图渲染之前执行。然后，结果可以非常短暂地存储在 `request` 作用域中，直到视图使用该信息进行渲染，然后被清除。

Servlet `request` 作用域和 Web Flow `request` 作用域之间有很大的相似性。主要区别在于，后者是 Web Flow 组件本地的。它是 Web Flow 内部使用的一个内部构件，不会暴露给外部组件（例如，Servlet）。

![Image](img/square.jpg) **注意** 在 Web Flow 中包含 `request` 作用域是一个设计决策，目的是对标准的 Servlet `request` 作用域进行抽象。设计者也将 Web Flow 定位为其他平台（这些平台可能没有像 Servlet 规范那样的 `request` 作用域概念）。对于作为开发者的你来说，除了 Web Flow 的 `request` 作用域更容易从例如流程内的表达式中访问之外，没有真正的区别。

##### Flash 作用域

你可以将 `flash` 作用域视为一个更大的 `request` 作用域，它在“后置-重定向-获取”模式中存活两个请求，直到视图最终被渲染。具有 `flash` 作用域的属性会一直保留，直到视图被渲染，然后被清除。

将对象存储在 `flash` 作用域中，对于视图中需要但其他地方都不需要的对象特别有用。使用 `flash` 作用域的一个很好的例子是存储要在视图上渲染的消息，比如错误或警告消息。当动作执行时，它们可以由你的应用程序控制器或流程中的验证逻辑放入 `flash` 作用域。`request` 作用域是不够的，因为模型验证发生在 PRG 模式的第一个请求中。因此，当视图被渲染时，放入 `request` 作用域的消息将消失。`flash` 作用域为这类用例提供了一个优雅的解决方案。

图 11-2 通过展示一个跨越转移 t1 中的请求 1 和请求 2 的 `flash` 作用域来说明这一点。从视图被渲染的那一刻起，现有的 `flash` 作用域就被清除了。这意味着，如果你刷新视图，消息将会消失，这通常是你对错误和警告消息这类信息所期望的行为。

![Image](img/9781430241553_Fig11-02.jpg)

***图 11-2**. 展示 flash 作用域*

因为 `flash` 作用域是一个扩展的 `request` 作用域，所以它也对所使用的任何子流程可用。例如，当视图状态内的一个转移委托给一个子流程，然后该子流程渲染视图时，`flash` 作用域也将对该子流程内发生的执行过程可用。然而，在视图被渲染之后，对该子流程的任何后续请求都将在它们自己的 `flash` 作用域中运行。

##### View 作用域

`view` 作用域在视图状态进入时被分配，并在视图状态退出时被销毁。`view` 作用域只能从视图状态内部引用。在图 11-3 中，你可以看到，对于每个新的视图状态，都有一个可用的新 `view` 作用域。

![Image](img/9781430241553_Fig11-03.jpg)

***图 11-3**. 展示 view 作用域*

你可以使用 `view` 作用域来存储仅在给定视图中需要的数据。例如，当你有一个带分页功能的数据表时，你可以加载结果并将它们放入 `view` 作用域。通过使用视图状态内的转移（例如，“下一页”和“上一页”转移），你可以每次滚动浏览列表，显示结果集的特定部分。从视图状态退出的那一刻起（例如，在数据表中选择一个结果后），`view` 作用域被销毁，资源被释放。

##### Flow 作用域

`flow` 作用域的范围是一个流程会话。你可以在图 11-4 中看到这一点，其中父流程的执行有一个 `flow` 作用域，子流程的流程会话也有一个（子流程与父流程在同一个流程执行中运行，但在不同的流程会话中）。这意味着父流程放置在 `flow` 作用域中的属性将无法从子流程的 `flow` 作用域中访问。

![Image](img/9781430241553_Fig11-04.jpg)

***图 11-4.** flow 作用域*

你可以使用 `flow` 作用域来存储那些在整个流程会话期间都应可用，但仅限该会话使用，而不被同一流程执行中启动的其他流程会话（即子流程）使用的对象。

`flow` 作用域中的对象大多与应用程序及其实现的功能相关（例如，由流程操作的领域对象实例）。



##### 对话作用域

`conversation`（对话）作用域的范围是整个对话或执行过程（参见图 11-5）。术语“对话”可能有些误导性，但这只是一个实现细节（尤其在 Web Flow 2 中），对本讨论并不重要。尽管我们不得不使用这个术语，但你可以将 `conversation` 作用域理解为在整个流程执行过程中维护状态的作用域。我们认为“`execution`（执行）作用域”会是更好的命名。`conversation` 作用域中的属性对对话（或执行）中包含的每个流程会话都可用。因此，`conversation` 作用域也对所有子流程可用。

![图片](img/9781430241553_Fig11-05.jpg)

***图 11-5.** 对话作用域*

与 `flow` 作用域一样，`conversation` 作用域主要存储与应用相关的对象。如果这些对象应对每个流程会话（针对该流程及其子流程）都可用，而不是仅针对单个流程会话，则应使用 `conversation` 作用域。如果你的流程不使用子流程，则 `conversation` 和 `flow` 作用域的生命周期可以视为相等。请注意，我们并不是说这两个作用域“变得相等”；它们仍然是两个物理上分离的作用域，因此在 `flow` 作用域上设置的内容*不会*在 `conversation` 作用域上可见。但在这种情况下，它们在“逻辑上”是相等的，因为它们具有相同的生命周期，而生命周期通常是区分不同流程的属性。

由于它们的生命周期相同，有人可能会认为选择 `flow` 作用域或 `conversation` 作用域是等价的。然而，默认情况下，选择 `flow` 作用域而非 `conversation` 作用域更为合理。这是因为后续可能会引入子流程（即当你的流程扩展时），而子流程将自动能够访问 `conversation` 作用域上的数据。正如我们稍后在介绍子流程时将会看到的，最佳实践是思考子流程需要哪些数据，并仅暴露适量的数据。

在图 11-5 中，你可以看到，对于流程 x，`conversation` 和 `flow` 作用域是相等的，因为没有子流程存在。你还可以看到，为流程 y 创建了一个新的 `conversation` 作用域（和 `flow` 作用域）。同样，对于流程 y，会为子流程创建一个不同的 `flow` 作用域。然而，`conversation` 作用域对父流程和子流程仍然都是可访问的。如前所述，子流程可见的 `flow` 作用域是一个新的作用域，子流程无法访问父流程的 `flow` 作用域。

![图片](img/square.jpg) **注意** 在 `flow` 或 `conversation` 作用域中存储大型对象时要小心。这些作用域的生命周期相对较长，这意味着其中存储的数据需要长时间维护。作为在 `flow` 或 `conversation` 作用域中存储大型对象的替代方案，可以考虑为每个请求重新加载这些对象，并将其存储在 `request` 作用域中。

#### 隐式对象

就像在 JSP（脚本片段）和 EL 中有隐式对象一样，Web Flow 也定义了一些隐式对象。隐式对象是对某些常用功能的快捷访问（例如，HTTP 请求参数）。虽然你可以通过 `javax.servlet.http.HttpServletRequest` 访问请求参数，但 EL 和 Web Flow 都包含一个“快捷方式”，可以直接访问请求参数映射。在 EL 中，这个隐式对象称为 `param`；在 Web Flow 表达式中，你可以使用 `requestParameters`。

除了与数据相关的隐式对象外，还有对关键框架对象的引用。例如，如果你在 Web Flow 表达式中需要 `javax.servlet.ServletContext`，可以在表达式中引用隐式变量 `externalContext`。`externalContext` 变量将间接暴露 `javax.servlet.ServletContext` 以及其他与上下文相关的信息。在这种情况下，表达式将变为：

`"#{externalContext.nativeContext}"`

![图片](img/square.jpg) **注意** 你也可以在视图（JSP）中从 EL 使用这些隐式对象。这些对象由 Web Flow 绑定到 Servlet 的 `request` 作用域。例如，使用类似 `#{flowExecutionUrl}` 的内容将解析为绑定到 Servlet 的 `request` 作用域的 `flowExecutionUrl` 隐式对象。

表 11-1 概述了可在 Web Flow 表达式中使用的每个隐式对象。

![图片](img/9781430241553_tab11-01.jpg)

![图片](img/9781430241553_tab11-01a.jpg)

### 增强书店应用

在前面的章节中，我们较为详细地介绍了 Web Flow 的通用术语。在深入代码之前，我们需要先介绍这些概念。现在我们已经介绍了它们，接下来将探讨特定的 Web Flow 特性。我们将通过代码示例进行说明，并沿途解释。

在上一章中，我们首次尝试为订购图书引入了一个基本流程。我们使用了一些基本的 Web Flow 组件，展示了使用 Web Flow 转换给定用例是多么容易。现在，我们将继续为示例应用程序添加功能并进行改进，同时沿途说明和解释各种 Web Flow 特性。例如，我们在上一章中留下的示例应用程序还算不上一个完整的应用程序。很多东西仍然缺失，仅举几例：

*   *没有验证*：当没有选择图书时，不应允许下订单。交货日期也是必填项，如果用户未指定此日期，网站应触发异常。
*   *没有转换*：我们在表单中将 `Category` 和 `Book` 映射为 `Long`，使用了这些对象的标识符。如果我们能直接从领域模型映射到 `Category` 或 `Book`，那将更加优雅。

在接下来的章节中，我们将解决这些及其他不足之处，逐步重构我们的示例应用程序，以展示如何使用 Web Flow 实现一些真实世界的用例。我们将从头开始，修改流程中的第一步：选择图书类别。

#### 选择类别

为了加载可选择的类别列表，我们使用了一个启动时动作。该动作在进入视图状态以渲染所选类别视图之前，将我们的类别加载到 `flow` 作用域中。正如我们在“不同的 Web Flow 作用域”部分已经解释过的，这并不是存储此信息的理想位置。可选择的类别仅在所选类别视图的作用域内需要。我们将看到存在更好的替代方案。

此外，我们在表单中使用类别的 id 进行映射，而不是将其映射为领域对象。接下来的章节将展示我们如何改变这一点。



##### 关于启动

我们仍将使用 `on-start` 来初始化 `OrderForm`。请记住，当流程会话启动时，`on-start` 会自动执行。即使你在顶层流程元素中定义了特定的 `<start-state>` 属性，Web Flow 在启动流程时也始终会执行 `on-start` 中定义的动作，并按顺序依次执行每个动作。由于我们不会在此处加载类别，因此删除了相关代码，只保留表单的初始化（参见清单 11-1）。

***清单 11-1.** 初始化订单表单的 `<on-start>` 动作*

`<on-start>`
`    <evaluate expression="orderController.initializeForm()"`
`              result="flowScope.orderForm"/>`
`</on-start>`

初始化表单的代码保持不变。有一个小细节值得注意：在 `com.apress.prospringmvc.bookstore.web.controller.OrderForm` 类的先前版本中，我们刻意将所有表单元素保持简单。例如，选中的 `com.apress.prospringmvc.bookstore.domain.Book` 和 `com.apress.prospringmvc.bookstore.domain.Category` 对象本身并未存储，而只存储了它们的 ID，订单日期和交货日期则存储为 `java.lang.String` 类型。从现在开始，我们将使用常规数据类型，并将转换和格式化工作交给 Web Flow 和 Spring MVC。因此，我们将把交货日期和订单日期改为 `java.util.Date` 类型，而非 `java.lang.String`。这改变了我们对 `OrderForm` 的初始化方式；在清单 11-2 中，你可以看到我们现在将这些信息存储为 `Date` 类型，而不是 `String` 类型。

***清单 11-2.** 使用日期类型初始化 OrderForm 实例*

`public OrderForm initializeForm() {`
`    OrderForm orderForm = new OrderForm();`
`    orderForm.setQuantity(1);`
**`orderForm.setOrderDate(new Date());`**
`    return orderForm;`
`}`

##### 关于渲染

既然我们已经移除了类别（`Category`）的初始化，就必须寻找另一种解决方案。解决方案在于使用 `on-render` 动作和 `request` 作用域。在视图即将渲染之前加载类别，并在渲染后立即移除它们，这将非常有益。

`on-render` 动作正好可以实现这一点。`on-render` 中指定的每个动作都会在视图状态渲染之前立即执行。请记住：当 Web Flow 使用 PRG 模式时，视图状态会在第二个请求时渲染。第一个请求执行我们的控制器并选择要渲染的视图。当第二个请求（GET 请求）到来时，我们的 `on-render` 动作就会执行。然后我们加载类别，并将它们放入 `request` 作用域（参见第 11 章的“请求作用域”部分中的图 11-1）。接下来要渲染的视图将能够访问这些类别并渲染它们（参见清单 11-3）。

***清单 11-3.** 使用 on-render 加载类别*

`<view-state id="selectCategory" view="selectCategory" model="orderForm">`
**`<on-render>`**
**`<evaluate`**
**`expression="orderController.initializeSelectableCategories()"`**
**`result="requestScope.selectableCategories"/>`**
**`</on-render>`**
`    <transition on="next" to="selectBooks" >`
`        <evaluate expression="orderController.`![Image](img/U002.jpg)
`                              initializeSelectableBooks(flowScope.orderForm)"    `
`                  result="flowScope.selectableBooks" />`
`    </transition>`
`    <transition on="cancel" to="end" />`
`</view-state>`

基本上，我们将 evaluate 表达式从 `on-start` 动作移到了 `on-render` 动作，并选择了 `requestScope` 而不是 `flowScope`。

现有用于检索类别的代码版本会选取所有类别，并将它们放入一个以 ID 为键、名称为值的映射中（参见清单 11-4）。

***清单 11-4.** 加载类别的现有方式*

`public Map<Long, String> initializeSelectableCategories() {`
`    Map<Long, String> selectableCategories = new HashMap<Long, String>();`
`    for (Category category : categoryService.findAll()) {`
`        selectableCategories.put(category.getId(), category.getName());`
`    }`
`    return selectableCategories;`
`}`

现在，我们将通过直接返回一个包含领域实体 `Category` 类型的 `List` 来使这段代码更自然（参见清单 11-5）。稍后，我们将探讨转换是如何进行的。

***清单 11-5.** 类别现在按原样加载*

`public List<Category> initializeSelectableCategories() {`
`    return categoryService.findAll();`
`}`

![Image](img/square.jpg)  **注意**  我们的 `initialize` 方法被重构为一行代码；一个不错的替代方案是直接从流程中调用 `categoryService.findAll()`。

在我们的页面中，将不再有 `Map`，而是有一个 `Category` 对象的列表。我们需要修改 `<form:select>`，以便将名称显示为标签，将 ID 显示为值。我们仍然需要 ID（作为 select 的值）以便稍后执行转换（参见清单 11-6）。

***清单 11-6.** 带有额外 itemLabel 和 itemValue 的表单选择框*

`<form:select path="category" items="${selectableCategories}"` **`itemLabel="name" itemValue="id"/>`**

![Image](img/square.jpg)  **注意**  类别和书籍的实例是缓存的理想候选。然而，我们不会将它们缓存在任何 Servlet 或 Web Flow 作用域中（这些作用域最终会存在于用户的会话中，并为每个用户重复存储值），而是使用 ORM 的二级缓存将它们缓存在后端。正如你将看到的，`Book` 和 `Category` 实体上都有 `@Cacheable` 和 `@Cache` 注解。当对它们执行查询时，我们也会显式地缓存这些查询。ORM 被配置为使用 Ehcache 作为缓存实现。这可以将冗余对象从用户会话中卸载，并将它们缓存在后端，供整个应用程序使用。如果你启用 Hibernate（我们的 JPA 实现）调试，你会看到，在服务器启动后，类别仅从数据库中获取一次。在下一次服务器重启或缓存过期之前，它们不会被再次获取。



##### 类型转换

现在我们有了一个可选分类列表，这些分类实际上是领域实体对象。在绑定时，`category` 的 id 需要转换为 `Category` 类型，因为这是我们在 `OrderForm` 对象上指定的类型。

***清单 11-7.** OrderForm 中的 Category 现在是我们的实体类，而不再是 Long 类型*

`private Category category;`

要启用类型转换，我们必须回到 Web Flow 配置中。我们已经指定了一个流程构建器服务。请记住，流程构建器服务允许我们自定义某些 Web Flow 行为。我们在上一章的“Web Flow 配置”部分介绍了流程构建器的可能性。现在我们将看到如何使用流程构建器服务来指定自定义转换服务。清单 11-8 显示了我们当前的配置；该文件可以在 `src/main/resources/spring/webflow-config.xml` 中找到。

***清单 11-8.** 当前的流程构建器服务配置*

`<webflow:flow-builder-services id="flowBuilderServices" view-factory`![Image](img/U002.jpg)
`-creator="mvcViewFactoryCreator" development="true"/>`

解释转换服务有点麻烦，因为我们觉得它比实际应该的要复杂得多。转换服务能够执行类型转换。例如，如果你的模型有一个 `java.lang.Integer` 类型的字段，执行从请求参数到模型绑定的代码就必须进行转换。请求参数总是作为 `String` 接收，因此必须有人将 `java.lang.String` 转换为 `java.lang.Integer`。幸运的是，许多基本转换器已经存在，你可能甚至没有注意到转换的发生。

到目前为止，Web Flow 拥有自己的类型转换机制。Spring MVC 使用一个非常相似但不兼容的类型转换系统，因此为 Spring MVC 编写的自定义转换器理论上需要为 Web Flow 重写。幸运的是，现在有一个适配器，允许你将 Spring MVC 特定的转换器插入到 Web Flow 中。

![Image](img/square.jpg)  **注意**  Web Flow 建议你使用标准的 Spring MVC 转换机制，并且不再以“Web Flow 方式”编写转换器。你可以根据它们的包来识别这些转换器。你应该使用的转换器机制来自 `org.springframework.core.convert` 包，*而不是* Web Flow 的 `org.springframework.binding.convert` 包。后者仅由 Web Flow 使用，并且对于编写新的自定义转换器来说，它应被视为已弃用。

在我们的案例中，我们必须编写一个能够将 `String`（表示选择列表中的数值）转换为 `Category` 对象的转换器。让我们从配置转换服务开始（参见清单 11-9），该配置位于 `src/main/resources/spring/webflow-config.xml` 中。

***清单 11-9.** 定义转换服务*

`<bean id="conversionService"`
` class="org.springframework.binding.convert.service.DefaultConversionService">`
`    <constructor-arg ref="mvcConversionService"/>`
`</bean>`

这段代码基本上创建了一个 Web Flow 的 `DefaultConversionService`。默认转换服务注册了所有默认转换器，这些转换器能够转换所有原始类型，例如 `Integer`、`Long` 等。然而，它也包括像 `java.math.BigDecimal` 和 `java.util.Date` 这样的类型。转换服务还为你提供了一个钩子来注册你自己的转换器，或者注册另一个转换服务作为委托转换服务。用于注册委托的钩子被用来传递核心的 Spring MVC 转换服务。`DefaultConversionService` 明确支持“新样式”的委托转换服务。每当要求转换服务转换给定类型时，它会先查看自己是否能处理该转换（可能使用现有的 Web Flow 转换器）。如果不能，它会检查 Spring MVC 提供的转换服务中可用的转换器。

需要说明的是，Spring MVC 转换服务（`org.springframework.format.support.DefaultFormattingConversionService`）是由 Spring 自动为我们配置的；它是在 `WebMvcContextConfiguration` 中使用 `@EnableWebMvc` 注解时自动创建的 bean 之一。创建的 Spring bean 的 id 为 `mvcConversionService`。接下来，我们可以将对 `conversionService` 的引用传递给流程构建器服务（参见清单 11-10）。你可以在 `src/main/resources/spring/webflow-config.xml` 中找到此服务。这完成了 Web Flow 和 Spring MVC 之间关于转换和格式化的集成。现在双方已连接，并将作为一个大的转换或格式化服务运行。

***清单 11-10.** 带有自定义转换服务的流程构建器服务*

`<webflow:flow-builder-services id="flowBuilderServices" view-factory`![Image](img/U002.jpg)
`-creator="mvcViewFactoryCreator"` **`conversion-service="conversionService"`** `development="true"/>`

最后，我们仍然需要注册我们的自定义转换器。在第 5 章（参见“配置类型转换”部分）中，我们介绍了通用的 `com.apress.prospringmvc.bookstore.converter``.StringToEntityConverter`。我们将重用这个转换器，它将把 `java.lang.String`（在本例中代表我们的 id）转换为目标实体，即我们的 `Category` 领域对象（参见清单 11-11）。你可以在 `com.apress.prospringmvc.bookstore.web.config.WebMvcContextConfiguration` 中找到这个 bean。

***清单 11-11.** 定义 Category 转换器*

`@Bean`
`public StringToEntityConverter categoryConverter() {`
`    return new StringToEntityConverter(Category.class);`
`}`

正如我们已经看到的，MVC 和 Web Flow 的转换/格式化服务是关联的。我们将使用 Spring 核心服务来添加自定义转换器，这些转换器随后将对 Web Flow 可用。在清单 11-12 中，我们将把转换器添加到 Spring MVC 的 `org.springframework.format.FormatterRegistry` 中。`addFormatters` 方法由 Spring 自动调用，并提供 `FormatterRegistry`。这在第 5 章的“配置类型转换”部分有详细解释。

***清单 11-12.** 在 WebMvcContextConfiguration 中将转换器添加到注册表*

`@Override`
`protected void addFormatters(FormatterRegistry registry) {`
`    registry.addConverter(categoryConverter());`
`}`

![Image](img/square.jpg)  **注意**  如果你仔细观察，你会注意到我们正在向 `FormatterRegistry` 添加一个转换器。正如你将看到的，格式化是转换的一种特化。`FormatterRegistry` 扩展了 `org.springframework.core.convert.converter.ConverterRegistry`，因此你可以同时向注册表添加转换器和格式化器。提醒一下，转换和格式化是核心 Spring MVC 的一部分，并在第 5 章（参见“转换器”和“格式化器”部分）中有详细解释。



##### 类型格式化

格式化器不过是 `org.springframework.core.convert.converter.Converter` 的一种特化实现。在内部，格式化器会被转换为 `org.springframework.core.convert.converter.GenericConverter`¹。这其实不难理解：格式化器是一种单向转换器，你可以在其中定义特定对象在视图中渲染时的可视化规则。

通过在 Web Flow 中启用转换服务并将其与 Spring 转换服务关联，我们实际上间接通过 Spring MVC 转换服务启用了格式化功能。任何在该服务中注册的格式化器也都能被 Web Flow 使用。

我们需要自定义配送日期和订单日期的格式化方式。由于日期格式化默认可用，我们无需注册特定的转换器或格式化器。但为了使用特定的日期格式，我们在 `OrderForm` 的字段定义中添加了 Spring 默认的日期时间格式化注解。这将引导格式化流程（参见清单 11-13）。

____________

¹ 你可以在 `org.springframework.format.support.FormattingConversionService` 的 `addFormatterForFieldType` 方法中看到这一点。

***清单 11-13.** 在 OrderForm 中格式化配送日期和订单日期*

**`@DateTimeFormat(pattern = "MM-dd-yyyy")`**
`private Date deliveryDate;`
**`@DateTimeFormat(pattern = "MM-dd-yyyy")`**
`private Date orderDate;`

#### 选择书籍与配送选项

我们需要对选择书籍的视图状态重复上一部分的重构操作。在上一章中，我们在转换到 `selectBooks` 视图状态后，于 `next` 转换中使用了 evaluate 动作，如清单 11-14 所示。

***清单 11-14.** 旧方法：使用 Evaluate 动作初始化书籍*

`<transition on="next" to="selectBooks" >`
`    <evaluate`
`    expression="orderController.initializeSelectableBooks(flowScope.orderForm)"`
`    result="flowScope.selectableBooks" />`
`</transition>`

我们将移除上述 `evaluate` 动作，并用相同的 on render 机制替代它（参见清单 11-15）。

***清单 11-15.** 使用 on render 初始化可选书籍*

`<view-state id="selectBooks" view="selectBooks" model="orderForm" >`
`    <on-render>`
`        <evaluate expression="orderController.`![Image](img/U002.jpg)
`initializeSelectableBooks(flowScope.orderForm)"` ![Image](img/U002.jpg)
`                  result="flowScope.selectableBooks" />`
`    </on-render>`
`    <transition on="previous" to="selectCategory" />`
`    <transition on="add">`
`        <evaluate expression="orderController.addBook(flowScope.orderForm)" />`
`    </transition>`
`    <transition on="next" to="selectDeliveryOptions" />`
`    <transition on="reset" to="selectBooks">`
`        <evaluate expression="orderForm.resetSelectedBooks()" />`
`    </transition>`
`    <transition on="cancel" to="end" />`
`</view-state>`

这使得前一个 `selectCategory` 视图状态中 `on next` 转换里的 evaluate 动作变得多余。

同样地，我们修改了选择列表 `selectBooks.jsp`，将标题渲染为项目标签，将 ID 渲染为项目值，因为现在可选书籍存储的是 `Book` 领域实体（参见清单 11-16）。你可以在 `src/main/webapp/WEB-INF/view/createOrders/selectBooks.jsp` 中找到该选择书籍文件。

***清单 11-16.** 为书籍表单的选择框添加 itemLabel 和 itemValue*

`<form:select path="book items="${selectableBooks}" itemLabel="title" itemValue="id"/>`

最后，我们还需要为 `Book` 领域实体注册一个转换器。其流程与 `Category` 完全相同。这里我们使用通用的 `com.apress.prospringmvc.bookstore.converter.StringToEntityConverter`。只需在 Spring MVC 配置类（位于 `com.apress.prospringmvc.bookstore.web.config.WebMvcContextConfiguration.java`）中注册带有正确参数化的转换器即可，如清单 11-17 所示。

***清单 11-17.** 定义书籍转换器*

`@Bean`
`public StringToEntityConverter bookConverter() {`
`    return new StringToEntityConverter(Book.class);`
`}`

接下来，我们可以继续针对应用程序的选择书籍和选择配送选项进行具体重构。这涉及添加验证功能，以便用户在提交包含无效或缺失数据的表单时收到通知。我们将看到执行验证大致有三种方式：使用 JSR 303 注解、使用 `Web Flow` 可识别的表单（或独立类）中的验证器方法，以及在应用程序控制器本身中应用验证。



#### 使用 JSR 303 注解进行表单验证

您可以配置 Web Flow 来执行 JSR 303 验证（即 Bean 验证）。例如，如果您使用 `javax.constraints.validation` 中的注解（如 `@Min` 或 `@Max`）对模型进行注解，那么验证将会执行，并且会为模型中的该路径创建错误消息。要实现这一点，您必须向流程构建器服务提供一个 `org.springframework.validation.beanvalidation.LocalValidatorFactoryBean`。

清单 11-18 定义了启用 JSR-303 验证的工厂 Bean。您可以在 `src/main/resources/spring/webflow-config.xml` 中找到这个 Bean。

***清单 11-18.** 定义 Bean 验证器工厂 Bean*

`<bean id="validator" class="org.springframework.validation.beanvalidation`![Image](img/U002.jpg)
`.LocalValidatorFactoryBean" />`

接下来，我们可以完成流程构建器服务的配置，如清单 11-19 所示。您可以在 `src/main/resources/spring/webflow-config.xml` 中找到此配置文件。

***清单 11-19.** 启用 Bean 验证*

`<webflow:flow-builder-services id="flowBuilderServices" view-factory-creator=`![Image](img/U002.jpg)
`"mvcViewFactoryCreator" conversion-service="conversionService" validator="validator"`![Image](img/U002.jpg)
` development="true" />`

一切设置完毕后，我们可以简单地在表单上应用注解（参见清单 11-20），该表单位于 `com.apress.prospringmvc.bookstore.web.controller.OrderForm` 中。目前，我们仅对 `quantity` 字段使用验证。这是用户在向订单添加书籍时可以更改的字段。默认值为 `1`，但用户也可以直接向订单添加多本书。

***清单 11-20.** 添加验证注解*

`package com.apress.prospringmvc.bookstore.web.controller;`

`import javax.validation.constraints.Max;`
`import javax.validation.constraints.Min;`
`import javax.validation.constraints.NotNull;`

`@NotNull`
`@Min(1)`
`@Max(999)`
`private Integer quantity;`

我们也可以对其他字段使用 Bean 验证。然而，这样做会有两个问题：

*   首先，验证 API 擅长验证单个字段，但我们还需要验证字段的组合。例如，稍后我们需要检查输入的配送日期是否在订单日期之后。验证 API 具有分组功能来验证组内的字段，但如果不编写自定义 Bean 验证器，就无法指示更复杂的关系。不幸的是，这不是 Bean 验证开箱即用支持的功能。我们将使用另外两种验证方式之一（稍后介绍）来完成此操作。
*   其次，这种验证设置的问题在于整个表单作为一个整体被验证，但我们在流程的每一步中只使用它的一部分。基本上，这意味着当在第一页设置 `Category` 时，所有其他的验证注解也会被触发。如果我们在 `Book` 实例字段上方放置一个 `@NotNull` 注解，那么当我们选择一个 `Category` 时，就会收到一个错误，提示未选择 `Book`。一种解决方案是将表单拆分为多个较小的表单。但目前，我们将以不同的方式验证其他字段。另外，请注意 `quantity` 字段不存在此问题，因为我们将其值初始化为 `1`。因此，在选择 `Category` 时，`quantity` 字段会被验证，但由于其值为 `1`，验证会通过。稍后，当用户在 `select books` 页面中选择数量时，如果用户指定了不允许的值，验证器将触发错误。

![Image](img/square.jpg)  **注意**  对于 JSR303 验证，您需要在编译类路径中包含验证 API（其中包含 `javax.constraints.validation` 注解）。但在运行时，我们还需要一个提供者。Tomcat 没有自带提供者，因此我们使用 Hibernate Validator（它也是 JSR 303 的 RI，即参考实现）。^(2) 我们只需将这些验证器放在类路径上——但这就是我们需要做的全部，因为我们之前已经使用 `LocalValidatorFactoryBean` 配置了 Web Flow。为了定义依赖关系，我们将其添加到项目的根 Gradle 文件（示例根目录中的 `build.gradle`）中：

`compile "org.hibernate:hibernate-validator:$hibernateValidatorVersion"`
`compile "javax.validation:validation-api:1.0.0.GA"`

##### 使用 Web Flow 验证器方法和类进行表单验证

第二种执行验证的方式是在我们的模型中实现一个形式为“`validate`*`State`*”的方法。这种类型的验证完全通过遵循命名约定被 Web Flow 检测到。单词“validate”是必需的，而“*state*”是一个占位符，需要替换为应应用验证的状态的 ID。Web Flow 会在回发到定义的状态时执行验证。因此，验证不会在视图状态首次渲染时执行，而是在发生回发时执行（例如，提交表单时）。

在清单 11-21 的示例中，当我们转换到 `selectCategory` 状态时，验证被触发。我们检查触发转换的事件是否为 `next` 事件。如果用户点击取消按钮，那么我们就不希望执行验证。`org.springframework.binding.validation.ValidationContext` 由 Web Flow 自动注入。如果我们添加一个错误代码，那么该代码将使用已注册的 `org.springframework.context.MessageSource` 自动解析（此代码可以在 `com.apress.prospringmvc.bookstore.web.controller.OrderForm` 中找到）。

***清单 11-21.** 验证类别*

`public void validateSelectCategory(ValidationContext context) {`
`    if (context.getUserEvent().equals("next")) {`
`        MessageContext messages = context.getMessageContext();`
`        if (category == null) {`
`messages.addMessage(new` ![Image](img/U002.jpg)
`            MessageBuilder().error().source("category")`![Image](img/U002.jpg)
`            .code("error.page.category.required").build());`
`        }`
`    }`
`}`

此方法的一个变体是将验证逻辑与模型分开存储在一个 `validator` 类中（参见清单 11-22）。为此，您必须遵循更多关于类名和 `validation` 方法的命名约定。该类应命名为 `modelnameValidator`，而方法应命名为 `validateState`。在我们的例子中，类名将变为 `OrderFormValidator`，方法将与之前在 `validateSelectShop` 中使用的方法相同。对于外部验证器，您还将获得模型作为 `validate` 方法的参数，并且可选地获得 Spring MVC 的 `Errors` 对象。

____________

² [`download.oracle.com/otndocs/jcp/bean_validation-1.0-fr-oth-JSpec`](http://download.oracle.com/otndocs/jcp/bean_validation-1.0-fr-oth-JSpec)/

***清单 11-22.** 独立的验证器类*

`@Component`
`public class OrderModelValidator{`
`    public void validateSelectShop(OrderForm orderForm, ValidationContext context,`![Image](img/U002.jpg)
` Errors errors) {`

最后的要求是该 Bean 应注册为 Spring Bean。在像这样的注解驱动模型中，您可以简单地在类定义上方添加 `@Component`，并确保该类会被组件扫描所捕获。

![Image](img/square.jpg)  **注意**  在示例应用程序中，我们选择将验证与模型存储在一起，因此您不会找到外部的 `OrderFormValidator`。但欢迎您进行实验，尝试将验证逻辑从 `OrderForm` 移到一个单独的验证器中。



##### 使用应用控制器进行表单验证

最终的验证方法在由 Web Flow 表达式调用的控制器方法内执行自定义验证（见清单 11-23）。你可以在 `com.apress.prospringmvc.bookstore.web.controller.OrderController` 的 `validateDeliveryDate` 方法中看到这样一个示例，该方法在“选择配送选项”页面最终确定订单之前被调用（见清单 11-24）。

***清单 11-23.** 在应用控制器上调用验证方法*

`<view-state id="selectDeliveryOptions" view="selectDeliveryOptions" model="orderForm">`
`    <transition on="previous" to="selectBooks" />`
`    <transition on="finish" to="end">`
**`<evaluate expression="orderController.validateDeliveryDate`![Image](img/U002.jpg)**
**`(flowScope.orderForm, messageContext)"/>`**
`        <evaluate expression="orderController.placeOrder(flowScope.orderForm,`
`              externalContext.sessionMap.authenticatedAccount)"/>`![Image](img/U002.jpg)
`    </transition>`

`    <transition on="cancel" to="end" />`
`</view-state>`

***清单 11-24.** OrderController 上的验证方法*

`public Event validateDeliveryDate(OrderForm orderForm, MessageContext`
`     messageContext) {`
`    if (orderForm.getDeliveryDate() == null){`
`        MessageBuilder errorMessageBuilder = new MessageBuilder().error();`
`        errorMessageBuilder.source("deliveryDate");`
`            errorMessageBuilder.code("error.page.selectdeliveryoptions.`![Image](img/U002.jpg)`
`                deliverydate.required");`
`        messageContext.addMessage(errorMessageBuilder.build());`
`        return new EventFactorySupport().error(this);`
`    }`

`if (orderForm.getDeliveryDate().before(`
`        DateUtils.truncate(orderForm.getOrderDate(), Calendar.DAY_OF_MONTH))){`
`        MessageBuilder errorMessageBuilder = new MessageBuilder().error();`
`        errorMessageBuilder.source("deliveryDate");`
`        errorMessageBuilder.code("error.page.selectdeliveryoptions.`![Image](img/U002.jpg)`
`                                  deliverydate.in.past");`
`        messageContext.addMessage(errorMessageBuilder.build());`
`        return new EventFactorySupport().error(this);`
`    }`
`    return new EventFactorySupport().success(this);`
`}`

当用户在输入配送选项后点击“完成”按钮（触发 `finish` 转换）时，用于验证配送日期的评估动作会被执行。该调用发生在创建实际订单的调用之前。我们传递了表单和 `messageContext`（一个 Web Flow 隐式对象）。在其中，如上述代码片段所示，当验证失败时，我们使用消息上下文添加一条自定义消息。

你可能已经注意到，该方法返回了 `org.springframework.webflow.execution.Event`。这与我们之前在使用评估表达式时调用的其他方法形成对比。例如，`com.apress.prospringmvc.bookstore.web.controller.OrderController` 中的 `addBook` 方法仅返回 `void`。

当执行一个表达式时，其结果（如果还不是一个 `Event`）会在内部被转换为一个 `Event`。这些对象驱动转换，指示评估是成功还是失败（或任何其他基于 `java.lang.String` 的结果）。此外，这些对象还可以选择性地将对象作为属性与之关联。

当你不需要触发结果条件时，就没有必要使用 `Event` 作为返回值，你可以直接返回值（或直接返回 `void`）。Web Flow 会为你将其转换为一个成功的 `Event`。

然而，在这种情况下，我们需要区分验证成功和验证失败。两者将呈现不同的结果。在第一种情况下，我们希望继续订单创建过程；在第二种情况下，我们希望再次渲染“选择配送选项”页面以显示错误消息，并让用户重试。我们通过事件显式地发出此信号。请注意，我们可以为成功的 `Event` 附加一个结果，然后可以使用评估动作的 `result` 属性来绑定该结果。我们将在本章后面的“处理结果事件”部分讨论这一点。



##### 显示验证消息

最后，在通过目前介绍的三种不同方法之一添加验证消息后，我们仍需将其展示给用户。为此，我们可以使用 Spring MVC 标准的 `<form:errors>` 标签来实现以下两种操作之一。首先，我们可以为模型中的特定路径渲染消息。或者，我们也可以通过为 `path` 属性指定通配符来渲染所有消息。

重要的是要认识到，所有验证方法生成的消息都可以通过这种方式显示。无论消息是通过 Bean 验证、Web Flow 表单验证，还是在应用控制器中使用 `org.springframework.binding.message.MessageContext` 添加的，最终都会通过 `<form:errors>` 标签显示出来。此时，我们将重构页面中的所有输入组件，在其后添加 `<form:errors path="<表单字段名称>"/>`。这样，当某个字段验证失败时，我们就能显示相应的验证消息。

我们将以选择配送选项页面（见清单 11-25）为例进行演示，该页面位于 `src/main/webapp/WEB-INF/view/createOrders/selectDeliveryOptions.jsp` 文件中。（另外两个页面重复了完全相同的过程，因此我们不再赘述。）

***清单 11-25.** 重构选择配送选项页面以显示验证消息*

`<form:form modelAttribute="orderForm" action="${flowExecutionUrl}">`
`    <table style="width: 100%">`
`        <tr>`
`            <td>`
`                <spring:message`![Image](img/U002.jpg)
`code="label.page.selectdeliveryoptions.select.order.date"/>`
`            </td>`
`            <td>`
`                <form:input path="orderDate" disabled="true" />`
`            </td>`
`        </tr>`
`        <tr>`
`            <td>`
`                <spring:message`![Image](img/U002.jpg)
`code="label.page.selectdeliveryoptions.select.delivery.date" />`
`            </td>`
`            <td>`
`                <form:input path="deliveryDate" />`
**`<span style="margin-left: 5px">`**
**`<form:errors path="deliveryDate" cssClass="error"/>`**
**`</span>`**
`        …`

如果我们不选择配送日期就继续操作，错误消息将显示在该字段旁边（见图 11-6）。

![Image](img/9781430241553_Fig11-06.jpg)

***图 11-6.** 当配送日期留空时显示验证消息*

我们自定义错误消息的本地化部分来自应用程序中包含的默认资源包。这一点已在 Spring MVC 章节中解释过。例如，图 11-6 中显示的消息的键，在 `OrderController` 类的 `validateDeliverydate` 方法中被使用。它作为“错误代码”被添加到创建的错误消息中。为了回顾一下，让我们看看构建错误消息的方法片段：

`MessageBuilder errorMessageBuilder = new MessageBuilder().error();`
`errorMessageBuilder.source("deliveryDate");`
`errorMessageBuilder.code("`**`error.page.selectdeliveryoptions.deliverydate.required"`**`);`

粗体显示的消息代码在基础名称为 `messages.properties` 的资源包中定义。该资源包以该代码为键，以翻译文本（你可以在图 11-6 中看到）为值。

![Image](img/square.jpg)  **注意** 资源包物理上位于共享项目 `bookstore-web-resources` 中。其中包含一个英语资源包（`messages_en.properties`）和一个荷兰语资源包（`messages_nl.properties`）。你可以通过点击导航栏右侧的国旗图标动态切换语言。

对于 Bean 验证消息，存在一个默认加载的标准资源包。你可以在自己的资源包中覆盖这些消息。默认资源包是验证 API 实现的一部分。在我们的案例中，这是 RI（即 Hibernate 验证器）。如果我们打开 Hibernate 验证器的 JAR 文件，就会看到消息包（见图 11-7）。

![Image](img/9781430241553_Fig11-07.jpg)

***图 11-7.** 包含资源包的 Hibernate 验证器 JAR 文件*

打开默认消息包 `ValidationMessages.properties`，将显示清单 11-26 中针对 `@Min` 和 `@Max` 注解的翻译（我们在 `com.apress.prospringmvc.bookstore.web.controller.OrderForm` 的 `quantity` 属性上使用了这些注解）。

***清单 11-26.** 默认的验证 API 消息*

`javax.validation.constraints.Max.message= 必须小于或等于 {value}`
`javax.validation.constraints.Min.message= 必须大于或等于 {value}`

你可以通过在应用程序的类路径中指定自己的 `ValidationMessages.properties`（可带语言/国家扩展名）来覆盖这些消息。



##### 概述

在结束首次重构之前，我们想总结一下到目前为止所做的各项工作。这将为您提供流程以及所涉及的 `OrderController` 和 `OrderForm` 的完整代码概览。

我们首先使用 `<on-render>` 动作加载了类别和图书。接着，我们将表单中的图书和类别引用替换为实际的实体对象。为此，我们必须创建并注册两个自定义转换器。最后，我们为流程添加了验证功能，这样就不再可能因为未输入必填字段的值而触发应用程序错误。我们还向您展示了实现验证的三种方式。

清单 11-27 展示了我们目前的 `createOrders-flow`；该文件位于 `src/main/webapp/WEB-INF/view/createOrders/createOrders-flow.xml`。

***清单 11-27.** 首次重构后的 createOrders-flow 文件*

`<on-start>`
`    <evaluate expression="orderController.initializeForm()"`![Image](img/U002.jpg)
`     result="flowScope.orderForm"/>`
`</on-start>`
`<view-state id="selectCategory" view="selectCategory" model="orderForm">`
`    <on-render>`
`        <evaluate`
`            expression="orderController.initializeSelectableCategories()"`![Image](img/U002.jpg)
`            result="requestScope.selectableCategories"/>`
`    </on-render>`
`    <transition on="next" to="selectBooks"/>`
`    <transition on="cancel" to="end" />`
`</view-state>`
`<view-state id="selectBooks" view="selectBooks" model="orderForm" >`
`    <on-render>`
`        <evaluate`
`           expression="orderController.initializeSelectableBooks`![Image](img/U002.jpg)
`           (flowScope.orderForm)" result="requestScope.selectableBooks" />`
`    </on-render>`
`    <transition on="previous" to="selectCategory" />`

`    <transition on="add" >`
`        <evaluate expression="orderController.addBook(flowScope.orderForm)" />`
`    </transition>`
`    <transition on="next" to="selectDeliveryOptions" />`
`    <transition on="reset" to="selectBooks">`
`        <evaluate expression="orderForm.resetSelectedBooks()" />`
`    </transition>`
`    <transition on="cancel" to="end" />`
`</view-state>`
`<view-state id="selectDeliveryOptions" view="selectDeliveryOptions" model="orderForm">`
`    <transition on="previous" to="selectBooks" />`
`    <transition on="finish" to="end">`
`<evaluate` ![Image](img/U002.jpg)
`expression="orderController.validateDeliveryDate(flowScope.orderForm,` ![Image](img/U002.jpg)
`         messageContext)"/>`
`<evaluate expression="orderController.placeOrder(flowScope.orderForm,` ![Image](img/U002.jpg)
`         externalContext.sessionMap.authenticatedAccount)"/>`
`    </transition>`
`    <transition on="cancel" to="end" />`
`</view-state>`
`<end-state id="end" view="redirect:/index.htm"/>`

类似地，清单 11-28 展示了我们的 `OrderForm`，该文件位于 `com.apress.prospringmvc.bookstore.web.controller.OrderForm`。

***清单 11-28.** 首次重构后的 OrderForm*

`package com.apress.prospringmvc.bookstore.web.controller;`

`import javax.validation.constraints.Max;`
`import javax.validation.constraints.Min;`
`import javax.validation.constraints.NotNull;`

`import org.springframework.binding.message.MessageBuilder;`
`import org.springframework.binding.message.MessageContext;`
`import org.springframework.binding.validation.ValidationContext;`
`import org.springframework.format.annotation.DateTimeFormat;`

`import com.apress.prospringmvc.bookstore.domain.Book;`
`import com.apress.prospringmvc.bookstore.domain.Category;`
`import com.apress.prospringmvc.bookstore.domain.Order;`

`public class OrderForm implements Serializable {`
`    private Map<Book, Integer> books = new HashMap<Book, Integer>();`
`    private Book book;`

`    @NotNull`
`    @Min(1)`
`    @Max(999)`
`    private Integer quantity;`
`    private Category category;`
`    @DateTimeFormat(pattern = "MM-dd-yyyy")`
`    private Date deliveryDate;`
`    @DateTimeFormat(pattern = "MM-dd-yyyy")`
`    private Date orderDate;`
`// ---- 由 Web Flow 根据约定触发的表单验证方法，参见参考文档 5.10 节：验证模型`
`    public void validateSelectCategory(ValidationContext context) {`
`        if (context.getUserEvent().equals("next")) {`
`            MessageContext messages = context.getMessageContext();`
`            if (category == null) {`
`messages.addMessage(new` ![Image](img/U002.jpg)
`                MessageBuilder().error().source("category")`![Image](img/U002.jpg)
`                .code("error.page.category.required").build());`
`            }`
`        }`
`    }`
`    public void validateSelectBooks(ValidationContext context) {`
`        if (context.getUserEvent().equals("next")) {`
`            MessageContext messages = context.getMessageContext();`
`            if (books.isEmpty()) {`
`messages.addMessage(new` ![Image](img/U002.jpg)
`                MessageBuilder().error().source("books")`![Image](img/U002.jpg)
`                .code("error.page.books.required").build());`
`            }`
`        }`
`    }`
`    public void resetSelectedBooks() {`
`        books.clear();`
`    }`
`…<为简洁起见，省略了 getter 和 setter 方法>…`

而清单 11-29 展示了我们的 `OrderController`，该文件位于 `com.apress.prospringmvc.bookstore.web.controller.OrderController`。

***清单 11-29.** 首次重构后的 OrderController*

`package com.apress.prospringmvc.bookstore.web.controller;`

`import javax.servlet.http.HttpSession;`

`import org.apache.commons.lang3.time.DateUtils;`
`import org.springframework.beans.factory.annotation.Autowired;`
`import org.springframework.binding.message.MessageBuilder;`
`import org.springframework.binding.message.MessageContext;`
`import org.springframework.stereotype.Controller;`
`import org.springframework.web.bind.annotation.RequestMapping;`
`import org.springframework.web.servlet.ModelAndView;`
`import org.springframework.webflow.action.EventFactorySupport;`
`import org.springframework.webflow.execution.Event;`
`import org.springframework.webflow.execution.RequestContextHolder;`

`import com.apress.prospringmvc.bookstore.domain.Account;`
`import com.apress.prospringmvc.bookstore.domain.Book;`
`import com.apress.prospringmvc.bookstore.domain.Category;`
`import com.apress.prospringmvc.bookstore.domain.Order;`
`import com.apress.prospringmvc.bookstore.domain.support.OrderBuilder;`
`import com.apress.prospringmvc.bookstore.service.BookstoreService;`
`import com.apress.prospringmvc.bookstore.service.CategoryService;`

`@Controller`
`public class OrderController {`

`    @Autowired`
`    private BookstoreService bookstoreService;`

`    @Autowired`
`    private CategoryService categoryService;`

`    @RequestMapping("ordersOverview.htm")`
`    public ModelAndView retrieveOrders(HttpSession httpSession) {`
`List<Order> orders = bookstoreService.findOrdersForAccount((Account)` ![Image](img/U002.jpg)
`            httpSession.getAttribute(`![Image](img/U002.jpg)
`            AuthenticationController.AUTHENTICATED_ACCOUNT_KEY));`

`        ModelAndView mov = new ModelAndView();`
`        mov.setViewName("ordersOverview");`
`        mov.getModel().put("orders", orders);`

`        return mov;`
`    }`

`    public OrderForm initializeForm() {`
`        OrderForm orderForm = new OrderForm();`
`        orderForm.setQuantity(1);`
`        orderForm.setOrderDate(new Date());`
`        return orderForm;`
`    }`

`    public List<Category> initializeSelectableCategories() {`
`        return categoryService.findAll();`
`    }`



`    public List<Book> initializeSelectableBooks(OrderForm orderForm) {`
`        return bookstoreService.findBooksByCategory(orderForm.getCategory());`
`    }`

`    public void addBook(OrderForm orderForm) {`
`        Book book = orderForm.getBook();`
`        if (orderForm.getBooks().containsKey(book)) {`
`orderForm.getBooks().put(book, orderForm.getBooks().get(book) +` ![Image](img/U002.jpg)
`            orderForm.getQuantity());`
`        } else {`
`            orderForm.getBooks().put(book, orderForm.getQuantity());`
`        }`
`    }`

`    public Long placeOrder(final Account account, final OrderForm orderForm) {`
`        Order order = new OrderBuilder() {`
`            {`
`                addBooks(orderForm.getBooks());`
`                deliveryDate(orderForm.getDeliveryDate());`
`                orderDate(orderForm.getOrderDate()).account(account);`
`            }`

`        }.build(true);`

`        return bookstoreService.store(order).getId();`
`    }`

`public Event validateDeliveryDate(OrderForm orderForm, MessageContext` ![Image](img/U002.jpg)
`                                                           messageContext) {`
`        if (orderForm.getDeliveryDate() == null) {`
`            MessageBuilder errorMessageBuilder = new MessageBuilder().error();`
`            errorMessageBuilder.source("deliveryDate");`
`            errorMessageBuilder.code("error.page.selectdeliveryoptions.`![Image](img/U002.jpg)
`                                      deliverydate.required");`
`            messageContext.addMessage(errorMessageBuilder.build());`
`            return new EventFactorySupport().error(this);`
`        }`

`        if (orderForm.getDeliveryDate().`
`                before(DateUtils.truncate(orderForm.getOrderDate(),`![Image](img/U002.jpg)
`                Calendar.DAY_OF_MONTH))) {`
`            MessageBuilder errorMessageBuilder = new MessageBuilder().error();`
`            errorMessageBuilder.source("deliveryDate");`
`            errorMessageBuilder.code("error.page.selectdeliveryoptions.`![Image](img/U002.jpg)
`                                      deliverydate.in.past");`
`            messageContext.addMessage(errorMessageBuilder.build());`
`            return new EventFactorySupport().error(this);`
`        }`
`        return new EventFactorySupport().success(this);`
`    }`
`}`

### 设置变量和访问作用域

好了，我们已经完成了第一次重构。在进一步深入之前，我们想先向你介绍一些其他的 Web Flow 特性。在本节中，我们将介绍如何使用流变量以及如何通过隐式对象访问不同的 Web Flow 作用域。

作用域被视为一个 `java.util.Map`，因此你只需传递一个键来从作用域中读取值，以及传递一个键和值来在作用域上设置内容。表达式语言使得读取作用域变得简单直接。例如，如果你想从 `flow` 作用域中读取键为 `myKey` 的值，并将其传递给一个应用控制器，可以这样做：

`expression="myApplicationController.someMethod(flowscope.myKey)"`

设置变量可以使用 `<set>` 元素。该元素在所有五种状态（我们将在后续章节中介绍）中都是有效的，但决策状态除外。该元素也可以在转换中使用。清单 11-30 中的示例通过一个流启动动作，在 `flow` 作用域上使用键 `myKey` 设置了一个字面值 `someValue`。我们本可以使用其他作用域来存放该值（例如 `conversationScope` 或 `requestScope`）；然而，并非所有作用域都合理或允许。例如，`view` 作用域会报错，因为我们尚未处于视图状态，因此不存在 `view` 作用域。最后，如果指定一个字面值，则必须将其放在单引号内。`value` 属性默认期望一个表达式，这意味着 `value` 属性的值会被自动解释为一个表达式（即，不需要将其放在 `#{}` 之间）。

***清单 11-30.** 在 Flow 作用域上设置一个字面值*

`<on-start>`
`    <set name="flowScope.myKey" value="'someValue'"/>`
`</on-start>`

除了 `<set>` 元素，你还可以在 evaluate 动作中使用表达式语言来设置变量。清单 11-31 展示了如何将执行应用控制器方法返回的结果存储到 `flow` 作用域中。该表达式包含了要执行的逻辑（在此例中，是在 `myApplicationController` bean 上执行 `someMethod` 方法），将使用键 `myKey` 从 `flow` 作用域中检索到的值作为参数传递，并将结果以键 `someMethodResult` 存储在 `flow` 作用域中。

***清单 11-31.** 带参数调用应用控制器方法并将结果存储在 flow 作用域上*

`<evaluate expression="myApplicationController.someMethod(flowScope.myKey)"`![Image](img/U002.jpg)
`results="flowScope.someMethodResult"/>`

#### 流变量

一个流可以声明一个或多个实例变量。这些变量在流启动时分配，在流结束时销毁。声明它们的语法如下所示：

`<var name="orderForm" class="com.apress.prospringmvc.bookstore.web.OrderForm"/>`

`name` 是创建的实例在 `flow` 作用域中可用的键，而 `class` 属性是要实例化的类的完全限定类名。Web Flow 使用该类的默认（或无参）构造函数创建一个实例。

请注意，Web Flow 会从创建的实例中创建一个 Spring 管理的 bean。这样做时，Spring 会完全初始化这个实例化的类，应用所有适用的 `BeanPostProcessors`。该实例最终会作为 Spring bean 注册到应用上下文中。这意味着你可以在该类中使用例如 `@Autowired` 注解。在初始化期间，这些依赖关系将由 Spring 注入。

![Image](img/square.jpg) **注意**  “不同的 Web Flow 作用域”一节解释了你在 `class` 属性中指定的类应该实现 `java.io.Serializable`，因为创建的对象会被放入 `flow` 作用域。这也适用于未标记为 `transient` 的引用对象。然而，对不持有状态的对象（例如，服务、外观、DAO 等）的引用应声明为 `transient`。Java 的 `transient` 关键字将这些对象排除在 Java 序列化过程之外。此类对象不需要序列化，因为它们不包含任何状态。如果这些实例是自动装配的 bean（使用 `@Autowired`），那么 Spring 会确保在流恢复时重新连接它们。

`<var>` 元素主要用于在流启动前创建类的实例，并且这些实例在该流执行的作用域内使用。你只能在流定义的顶部使用此元素。相比之下，`<set>` 元素更适合在已有实例之间设置或移动值。例如，如果你想将给定的请求参数值放到一个搜索条件对象上，可以使用 `<set>` 元素。在 `name` 属性中，你可以引用代表搜索条件对象的键。在 `value` 属性中，你可以引用 `requestParameters` 隐式对象，然后通过该对象访问所需名称的参数（例如 `requestParameters.myParam`）。



#### 从视图中访问作用域变量

如我们所见，每个 Web Flow 作用域都有一个隐式对象可用，你可以在流程内的表达式中使用它来引用该作用域。正如我们之前所说，Web Flow 使用 Spring 表达式语言（Spring EL）。Web Flow 修改了 Spring EL 上下文，从而使这些隐式对象可用。

从我们进入视图（在我们的例子中是 JSP）的那一刻起，我们将间接使用统一表达式语言（Unified EL）。接下来，Web Flow 会通过 Servlet `request` 作用域使这些隐式对象可用。例如，在我们的 JSP 中，我们仍然可以使用 `#{flowExecutionUrl}`。Unified EL 会扫描每个 Servlet `scope`，并在 Servlet `request` 作用域中找到 `flowExecutionUrl` 键。然而，与作用域相关的隐式对象（例如 `flowScope` 和 `flashScope`）并不可用。此时，您可能会想知道如何从视图中访问您放在任何 Web Flow 作用域上的信息。

Web Flow 所做的是，在渲染视图之前，将每个 Web Flow 作用域中的每个对象直接放到 Servlet `request` 作用域上。这意味着两件事：

*   您可以直接从 Servlet `request` 作用域访问流程中任何 Web Flow 作用域的任何对象。例如，如果您在流程的 `flow` 作用域中有一个键为 `x` 的对象，您可以在 JSP 中使用表达式 `#{x}` 来访问它（如前所述，Unified EL 会扫描不同的 Servlet 作用域，并从 Servlet `request` 作用域解析它）。同样，如果您在 `conversation` 作用域中有一个变量 `y`，您也可以用相同的方式访问它，因为它也被放到了 Servlet `request` 作用域上：`#{y}`。
*   您必须小心处理键的名称。如果您在 `flow` 和 `conversation` 作用域中都有一个键为 `x` 的值，那么 Servlet `request` 作用域中只会存在其中一个（另一个会被覆盖）。通常，这不会造成问题，因为在不同的作用域中使用相同的键意义不大。

#### 以编程方式访问作用域

除了使用隐式对象从表达式中访问作用域之外，我们还可以在代码中使用 `org.springframework.webflow.execution.RequestContext` 对象来访问每个 Web Flow 作用域。该对象为每个作用域都提供了一个 getter 方法。同样，我们可以使用 `org.springframework.webflow.context.ExternalContext` 对象来访问 Servlet 作用域。我们可以通过使用表达式注入来获取对 `RequestContext` 或 `ExternalContext` 对象的引用。例如，`flowRequestContext` 和 `externalContext` 都是隐式对象，分别引用 `RequestContext` 和 `ExternalContext` 接口。

如果我们有一个 evaluate 动作，我们可以使用如下表达式：

`<evaluate expression="myApplicationController.someMethod(flowRequestContext)"/>`

`someMethod` 方法接受 `RequestContext` 作为参数（参见清单 11-32）。

***清单 11-32.** 使用表达式注入 RequestContext*

`import org.springframework.webflow.execution.RequestContext;`

`public void someMethod(RequestContext requestContext) {`
`    requestContext.getConversationScope();`
`}`

还有两个线程绑定的持有者类，可以让您即时访问 `RequestContext` 和 `ExternalContext`：

*   `RequestContextHolder.getRequestContext()`——参见清单 11-33
*   `ExternalContextHolder.getExternalContext()`——参见清单 11-34

***清单 11-33.** 访问 Web Flow 作用域*

`import org.springframework.webflow.execution.RequestContext;`
`import org.springframework.webflow.execution.RequestContextHolder;`

`RequestContext requestContext = RequestContextHolder.getRequestContext();`
`requestContext.getFlowScope();`
`requestContext.getFlashScope();`
`…`

***清单 11-34.** 访问 Servlet 作用域*

`import javax.servlet.http.HttpServletRequest;`
`import javax.servlet.http.HttpSession;`

`import org.springframework.webflow.context.ExternalContext;`
`import org.springframework.webflow.context.ExternalContextHolder;`

`ExternalContext externalContext = ExternalContextHolder.getExternalContext();`

`//分别访问 Servlet session 映射和 request 映射的快捷方式`
`externalContext.getSessionMap();`
`externalContext.getRequestMap();`

`//我们也可以检索 HttpServletRequest 对象本身`
`HttpServletRequest httpServletRequest`![Image](img/U002.jpg)
`  =(HttpServletRequest)externalContext.getNativeRequest();`

### 控制动作执行

我们已经看到了控制 Web Flow 动作执行的三种方式：在转换时、在启动时和在渲染时。然而，还有其他执行这些动作的方式，所有这些方式都有一些特定的规则，用于决定它们何时执行其包含的动作。这些条件将在接下来的章节中通过详细概述进行总结。

这些元素中的每一个还可以包含三个子元素；这些将在“控制动作执行：子元素”部分中描述。

#### <on-start>

`<on-start>` 元素在流程启动时执行一个或多个动作。此元素包含的动作只会执行一次。当指定多个动作时，下一个动作的执行将取决于前一个动作的成功与否（如果某个动作失败，则跳过下一个动作并抛出错误）。动作将按照它们在流程中定义的顺序执行。

整个流程只能有一个 `<on-start>`。如果一个流程有一个或多个带有 `<on-start>` 元素的父流程，则这些内容会在合并后的流程中合并为一个 `<on-start>`（我们将在第 12 章中进一步讨论流程继承）。

此动作对于执行流程执行/会话的整体初始化非常有用。在我们的示例中，我们使用它来用一些默认值初始化表单。

#### <on-end>

`<on-end>` 元素是 `<on-start>` 的对应物，适用相同的规则。此处定义的动作是流程结束前最后执行的动作。如果流程的结束状态有一个 `<on-entry>`（见下文），那么该 `<on-entry>` 会在 `<on-end>` 执行之前执行。

如果流程在到达结束状态之前抛出错误，则不会执行 `<on-end>`。不要将 `<on-end>` 与 Java 的 `finally` 块混淆。不能保证 `<on-end>` 一定会被执行。例如，假设一个给定的流程进入某个视图状态，并向用户的浏览器渲染一个视图。此时，流程执行（即存在于 Web 容器中的那个）被暂停。用户现在可以决定触发一个新事件来继续与该流程执行进行对话。然而，用户也可以决定导航到另一个页面（或另一个流程），使当前流程执行保持原样。当达到最大并发执行数时，Web Flow 将开始清理最旧的流程执行。如果我们给定的流程执行是最旧的，它将被清理；但在这种情况下，`<on-end>` 元素不会被触发。它仅在流程执行正常结束时（在到达结束状态后）被触发。

此元素在具有多个结束状态的流程中特别有用，当流程结束时需要执行某些逻辑。如果没有此元素，则逻辑需要为每个结束状态重复，但现在可以将其放在一个单一的 on end 中。这将确保无论哪个不同的结束状态终止了流程，所需的逻辑都会被执行。



#### <on-entry>

`<on-entry>` 元素可应用于全部五种状态：视图、操作、决策、子流程和结束状态。每次进入状态时，都会执行由 `<on-entry>` 元素指定的操作。

然而，这仅在状态被*显式*进入时才成立。例如：当你在一个转换（嵌套在视图状态中）中省略了 `to` 属性时，你会停留在同一个视图状态（即，该视图状态将被重新渲染）。但是，在这种情况下，`<on-entry>` 不会被触发。必须有一个到某个状态的显式转换才能执行 `<on-entry>`。

换句话说，你需要指定 `to` 属性来触发 `<on-entry>`（或 `<on-exit>`，我们将在下一节中看到）。`to` 属性所引用的状态并不重要；无论它是定义转换的同一个状态，还是完全不同的另一个状态，这一点都成立。例如，在视图状态的情况下，如果一个转换返回到同一个视图状态（显式指定了 `to` 属性），那么 `<on-entry>` 将再次被执行。

如果我们在 `createOrders-flow` 的 `selectBooks` 视图状态中放置一个 `<on-entry>`（参见清单 11-15），那么每次我们触发 `reset` 转换时它都会被执行。然而，当我们触发 `add` 转换时，它*不会*被执行，因为该转换没有显式地转换回 `selectBooks` 视图状态（它的转换中没有 `to` 属性）。

#### <on-exit>

每当退出一个状态时，都会执行 `<on-exit>` 元素。此元素可应用于以下状态：视图、操作、决策和子流程。但是，它不能应用于结束状态。

每次退出状态时，都会执行由 `<on-exit>` 指定的操作。这也适用于转换到同一状态的情况；在这种情况下，如果指定了 `to` 属性，则会退出该状态并再次进入（适用于 `<on-entry>` 元素的相同规则也适用于 `<on-exit>` 元素）。现在让我们扩展之前给出的 `<on-entry>` 示例。如果我们在 `createOrders-flow` 的 `selectBooks` 视图状态中添加一个 `<on-exit>`（参见清单 11-15），那么执行 `reset` 转换将会触发 `<on-exit>`。在这种情况下，我们将退出 `selectBooks` 视图状态以再次进入它。

#### <on-render>

`<on-render>` 元素仅适用于五种状态中的一种：视图状态。正如第 10 章中所讨论的，Web Flow 在处理请求时使用两阶段方法（Post Redirect Get）。转换中的表达式在 POST 阶段执行。`<on-render>` 元素在视图渲染之前，即 GET 阶段执行。

当处理大型结果集时，我们可以通过在视图渲染之前将它们加载到 `request` 作用域中来利用此行为。我们在本章中已经看到了这一点，当时我们介绍了重构示例应用程序的第一阶段（请参阅本章前面的“On Render”部分）。

#### 控制操作执行：子元素

前面描述的每个元素都支持三个子元素：

*   evaluate
*   set
*   render

您已经熟悉 evaluate 操作。它接受一个（默认情况下是 SpEL）表达式并执行它。可选地，您可以使用 `result` 属性将结果存储到具有特定键的作用域上，并且如果您要求结果属于特定类型，则可以指定结果类型（使用 `result-type` 属性）。如果是这样，将尝试进行转换；如果没有适用的转换器，则会抛出转换异常。表达式可以直接返回结果对象，或者根据需要将其包装在 `org.springframework.webflow.execution.Event` 类中以指示不同的结果状态。我们将在“使用结果事件”部分进一步讨论事件。

正如本章前面的“设置变量和访问作用域”部分所解释的，`<set>` 元素允许您在给定的作用域上设置某些内容。您可以指定一个名称和值，它们分别是变量将使用的键和绑定到该键的值。该值可以是一个表达式。可选地，您可以要求结果属于特定类型。如果值不是所需的类型，将尝试进行转换；如果没有适用的转换器，则会抛出转换异常。

render 元素有些特殊。它支持一个 `fragments` 属性，该属性接受要渲染的*片段*的 id。但什么是片段呢？要使用这些特性，您需要 Tiles。在配置 Tiles 时，您定义了填充模板的各个部分。片段指的是此类属性或定义的名称。它允许您部分渲染页面组合的一部分。当一个页面由多个部分组成（每个部分由一个 `tiles` 属性标识）时，您可以在片段中命名您特别想要重新渲染的部分。页面的所有其他内容保持不变。这允许在浏览器中进行部分页面更新。要实现这一点，需要在服务器端和客户端进行一些特殊配置。此外，它仅适用于 Ajax 请求。当执行普通请求时，指定 `<render>` 操作将不起作用。我们将在第 12 章的“Web Flow AJAX 支持”部分进一步讨论这一点。

### 全局转换

正如我们所看到的，转换可以将用户带到另一个视图，或者它可能只是执行一个操作并重新渲染当前视图。在下一章中，我们还将看到转换如何在处理 Ajax 事件时请求渲染视图的某些部分（称为“片段”）。到目前为止，我们只在状态中看到了转换。这些转换是特定于该状态的。要触发它们，您必须处于该特定状态。

除了在特定状态中定义的转换之外，流程还可以拥有*全局转换*。当一个事件发生时，如果 Web Flow 在当前流程状态中找不到任何匹配的转换，它将尝试查找匹配的全局转换。全局转换必须在流程的末尾定义。请参阅第 10 章中的流程结构图（参见图 10-1）以了解其在流程中的确切位置。

您可能会发现某些转换在多个视图状态中是重复的。使用全局转换，您现在可以在一个地方定义它们。之后，每个状态都能够执行该转换，就好像它直接是该给定状态的一部分一样。最常见的例子是一个终止流程的“取消”按钮，但它可能出现在流程的每个步骤中。

要定义全局转换，只需将转换取出并嵌套在 `<global-transitions>` 元素内，如清单 11-35 所示。

***清单 11-35.** 定义全局转换*

`<global-transitions>`
`     <transition on="cancel" to="endCancel" />`
`</global-transitions>`



### 子流程

子流程为您提供了构建流程的另一种方式。事实证明，Web Flow（我们至今尚未真正讨论过）的一大优势在于其对组合性和模块化的支持。

*组合性*允许您组合不同的流程定义，以构建更完整的定义。为此，您可以重用现有的定义，从而避免代码重复。在下一章关于高级 Web Flow 的内容中，您将看到如何通过继承在实践中运用这一特性。

如果您为*模块化*而设计，您将能够将流程的某些部分视为独立的模块。这同样促进了重用，因为您可以从应用程序的其他部分引用这些模块，而无需重复代码。在本节中，我们将探讨子流程，这是一种 Web Flow 技术，可帮助您实现此类重用。

与任何面向对象语言一样，您需要某种设计。这种设计会花费您一些额外的精力，但此特性将成为您项目中一个（非常）重要的方面。例如，它将决定您的应用程序在不久的将来的可管理性。

虽然在 Java 中，我们可以将所有内容写在一个类甚至一个方法中，但我们都知道，这会在不到一天的时间内使项目变得无法运作。因此，我们尝试应用良好的设计原则，因为我们知道从长远来看会从中受益。这将使我们的项目更易于测试，代码更易于阅读。它还将提高我们代码的质量，并使其更易于扩展，而无需花费数周时间进行重构。

这一原则同样适用于流程。流程不包含真正的 Java 代码，但它们确实替代（或容纳）了您的应用程序控制器，而控制器是流程内部逻辑的重要组成部分。良好的流程设计使其易于阅读、易于重构，最重要的是，可重用。

![Image](img/square.jpg) **注意**  Web Flow 1 包含一个名为*内联流程*的特性，即内联于或嵌入在另一个顶级流程中的流程。此类流程无需单独向 Web Flow 注册（您必须在 `<inline-flow>` 流程元素内自行提供流程 ID）。将内联流程与私有的 Java 嵌套类（或私有的内部类，如果您喜欢的话）进行比较是最佳方式。Web Flow 2 不再支持内联流程。如果您必须迁移此类流程，建议的操作是简单地将它们移动到顶级流程定义中，并在流程注册表中使用流程 ID 进行注册。该流程 ID 应与 `<inline-flow>` 元素的 `id` 属性相同。或者，您可以使用流程定义更新工具为您完成此操作。我们将在第 12 章的“Web Flow 1 迁移”部分解释如何使用此工具。

子流程与我们已介绍过的普通流程并无不同。它更像是一个概念。正如我们将在下一节继续重构示例应用程序时看到的那样，调用子流程需要一个特殊的状态，并且需要考虑在父流程和子流程之间传递状态。包含子流程的流程被称为*父*流程。子流程有时也被称为*子*流程。

简而言之：子流程使您能够将包含在一个流程（flow-x）中的部分流程移动到另一个顶级流程（flow-y）。接下来，您可以在 flow-x 中使用 `<subflow-state>` 元素从 flow-x 指向 flow-y；这使您能够执行已移动到 flow-y 的部分。这种重构不会影响您的流程（或整个应用程序）的行为，但会对您的流程设计产生影响。

通过这样做，您促进了重用。当您有一段在多个流程中都需要使用的流程逻辑时，您可以提取该逻辑，将其放入另一个顶级流程中，然后在其他流程中委托给该流程作为子流程。

您可以将此特性与 Java 中的*委托*进行比较。当不同类中有执行相同操作的逻辑时，您将其提取出来，放入一个单独的类中，然后从您提取逻辑的类中调用它。您可以将相同的模式应用于流程；只需提取公共部分并将其放入一个单独的流程中。当需要更改时，您只需更改一次，而不是在每个流程中分别更改。

如前所述，理解子流程与普通流程并无不同这一点很重要。实际上，您并非直接创建子流程；您只是创建一个普通流程。您无法通过查看一个流程来判断它是否是子流程。子流程应作为普通流程注册，并且适用相同的规则。

在图 11-8 中，flow-x 和 flow-y 都是普通流程。两者都可以像任何普通流程一样启动。但是，flow-y 也作为子流程包含在 flow-x 中。因此，从 flow-x 的角度来看，flow-y 被视为一个子流程。

![Image](img/9781430241553_Fig11-08.jpg)

***图 11-8.** Flow-x 作为顶级流程启动，并调用另一个流程（flow-y），该流程成为其子流程。*

当您在流程中包含一个子流程时，该流程本身也可以委托给一个子流程，依此类推。对于子流程可以存在的层级数量，没有硬性限制。

![Image](img/square.jpg) **注意**  正如我们所看到的，子流程在其自身的流程定义 XML 中被定义为一个普通的顶级流程。当一个流程被包含在另一个流程中时，我们才称其为子流程。然而，因为它是一个普通流程，您仍然可以像启动任何其他普通顶级流程一样启动它。但是，在大多数情况下，您会专门设计一个流程用作子流程；因此，在没有父流程上下文的情况下直接启动它可能意义不大。尽管如此，也存在一些场景，其中某个流程从应用程序的一部分作为顶级流程启动，同时又作为子流程包含在另一部分中，这可能是合理的。

### 进一步增强书店应用

我们将使用前几节讨论的元素继续我们的重构工作。到目前为止，我们还没有为应用程序添加任何安全性。在上一章中，我们解释说，一旦您进入应用程序，您就会自动登录。我们这样做是为了简化事情。但是，我们现在将用传统的登录页面替换登录过程。我们还将要求用户在下订单之前先登录。虽然我们将在接下来的专门章节（第 13 章）中讨论安全性，但在本节中，我们将通过创建自己的自制安全机制来初步了解安全性。我们将用它来说明流程的几个方面，包括子流程以及尚未介绍的其他状态。

到目前为止，用户在使用应用程序时会自动以“jd”身份登录；这是通过 `com.apress.prospringmvc.bookstore.web.AuthenticationSessionListener` 在后台发生的。这是一个 `javax.servlet.http.HttpSessionListener`，它会在新的 `javax.servlet.http.HttpSession` 启动时以编程方式对用户“jd”进行身份验证。如果您愿意，可以查看其实现，但这对于我们这里的解释并不重要。在前面的示例中，需要它才能向用户添加订单（您必须登录才能添加订单和查看现有订单）。

在本章的示例中，我们移除了该监听器，因为我们将实现自己的安全机制。让我们回顾一下图 11-9 中显示的修改后的流程。图中编号的步骤在表 11-2 中进行了描述。

![Image](img/9781430241553_Fig11-09.jpg)

***图 11-9.** 我们将重构的书店应用程序中修订后的流程*

![Image](img/9781430241553_tab11-02.jpg)



#### 将身份验证实现为子流程

我们的第一个操作是实现一个流程，该流程能够从页面捕获用户名和密码并验证其真实性。为此，我们需要以下内容：

*   一个用于捕获用户名和密码的页面和表单，即我们的登录页面
*   一个用于验证用户名和密码并将用户标记为已验证的应用控制器方法
*   一个流程，它将管理导航并充当登录页面与应用控制器之间的粘合剂。我们将把这个流程作为子流程包含进来。该流程必须能够：

    *   在启动后渲染登录页面
    *   通过调用应用控制器上的相应方法来执行身份验证
    *   指示身份验证已成功。如果身份验证失败，我们将停留在子流程内，显示一条消息，并给用户另一次机会输入正确的凭据
    *   指示我们希望返回上一页，以防用户决定此时不下订单

我们首先从流程开始。清单 11-36 显示了流程定义。我们将流程命名为 `authentication-flow.xml`，并将其放在以下目录中：`src/main/webapp/WEB-INF/view/authentication`。稍后我们将看到如何将此流程作为子流程包含进来。

***清单 11-36.** 身份验证流程*

`<on-start>`
`    <evaluate expression="authenticationController.initializeForm()"`
`        result="flowScope.authenticationForm" />`
`</on-start>`
`<view-state id="authenticate" view="login" model="authenticationForm">`
`    <transition on="authenticate" to="authenticationOk" >`
`        <evaluate`
`                expression="authenticationController.authenticate(`![Image](img/U002.jpg)
`flowScope.authenticationForm, externalContext,` ![Image](img/U002.jpg)
`                messageContext)"/>`
`    </transition>`
`    <transition on="previous" to="previous" bind="false"/>`
`</view-state>`
`<end-state id="previous"/>`
`<end-state id="authenticationOk"/>`

该子流程看起来与其他任何流程一样。启动时，它将调用 `AuthenticationController` 上的 `initializeForm`，该方法会将 `com.apress.prospringmvc.bookstore.web.controller.AuthenticationForm` 的一个新实例放入流程作用域。此表单将用于捕获从登录页面提交的用户名和密码。

`public AuthenticationForm initializeForm() {`
`    return new AuthenticationForm();`
`}`

接下来，它将进入视图状态，最终渲染登录页面。

当执行身份验证转换时，它将调用 `com.apress.prospringmvc.bookstore.web.controller.AuthenticationController` 上的 `authenticate` 方法。我们将在本节稍后讨论此方法。

最后，有两个结束状态，用于让子流程的调用者区分是返回上一屏幕还是继续处理（因为身份验证成功）。如果身份验证失败，我们将停留在子流程内，显示一条消息，表明用户提供了错误的凭据，并应在修改凭据后再次尝试登录。

接下来，我们在与流程相同的文件夹中构建登录页面（`login.jsp`）；因此，它将位于 `src/main/webapp/WEB-INF-view/authentication/login.jsp`（参见清单 11-37）。

***清单 11-37.** 位于 authentication/login.jsp 的身份验证页面*

`<spring:url value="/authenticate.htm" var="authenticate" />`
`<form:form modelAttribute="authenticationForm" action="${flowExecutionUrl !=` ![Image](img/U002.jpg)
`           null ? flowExecutionUrl  : 'authenticate.htm'}" method="POST">`
`    <table style="width: 100%">`
`        <tr>`
`            <td>`
`                <spring:message code="label.page.login.username" />`
`            </td>`
`            <td><form:input  path="username" disabled="false" />`
`                <form:errors cssClass="error" path="username"/>`
`            </td>`
`            <td>`
`                <spring:message code="label.page.login.password" />`
`            </td>`
`            <td>`
`                <form:password showPassword="false" path="password" />`
`                <form:errors cssClass="error" path="password"/>`
`            </td>`
`        </tr>`
`        <tr>`
`            <td colspan="4">`
`                <form:errors cssClass="error"/>`
`            </td>`
`        </tr>`
`    </table>`
`    <div align="right" style="margin-bottom: 20px; margin-top: 10px" >`
`        <button type="submit" id="previous" name="_eventId_previous">`
`            <spring:message code="label.page.login.previous"/>`
`        </button>`
`        <button type="submit" id="login" name="_eventId_authenticate">`
`            <spring:message code="label.page.login.login"/>`
`        </button>`
`    </div>`
`</form:form>`

首先要注意的是表单 `action` 属性的值：

`${flowExecutionUrl != null ? flowExecutionUrl  : 'authenticate.htm'}`

我们在应用程序的两个地方引用了身份验证页面。第一次引用发生在直接点击导航栏中的 `login` 链接时。点击导航栏中的 `login` 链接时，我们会导航到 `chapter11-bookstore/login.htm`，该页面会渲染 `webapp/view/authentication/login.jsp` 页面（如清单 11-38 所示）。在这种情况下，我们不会启动流程，而是使用普通的 Spring MVC 直接导航到登录页面。也不需要流程，因为在这种场景下，身份验证过程仅包含单个页面。如果用户想直接从主页登录，这很有用。

由于没有活动流程，因此表单的 `action` 属性使用了条件运算符的 `else` 部分：当点击登录按钮（填写用户名和密码后）时，表单将提交到 `authenticate.htm`。在这种情况下，将执行 `AuthenticationController` 的第一个 `@RequestMapping`，如清单 11-38 所示。执行此应用控制器方法将检索绑定到 `AuthenticationForm` 的已提交用户名和密码，并对用户进行身份验证。

***清单 11-38.** 使用 Spring MVC 导航并执行身份验证*

`// ----- Spring MVC 逻辑`

`    public AuthenticationForm initializeForm() {`
`        return new AuthenticationForm();`
`    }`

`    @RequestMapping("login.htm")`
`    public ModelAndView authentication() {`
`        ModelAndView mov = new ModelAndView();`

`        mov.setViewName("login");`
`        mov.addObject("authenticationForm", initializeForm());`
`        return mov;`
`    }`

`    @RequestMapping(value = "authenticate.htm", method = RequestMethod.POST)`
`public ModelAndView authentication(@ModelAttribute AuthenticationForm` ![Image](img/U002.jpg)
`authenticationForm, Errors errors, ModelAndView mov,` ![Image](img/U002.jpg)
`        HttpSession httpSession) {`
`        try {`
`            authenticate(authenticationForm, httpSession);`
`            mov.addObject("authenticationOk", "true");`
`            mov.addObject("username", authenticationForm.getUsername());`
`            mov.setViewName("main");`
`        } catch (AuthenticationException authenticationException) {`
`            errors.reject(LOGIN_FAILED_KEY);`
`            mov.setViewName("login");`
`        }`



`        return mov;`
`    }`
`    @RequestMapping(value = "authenticate.htm", params = "_eventId_previous",`![Image](img/U002.jpg)
`                     method = RequestMethod.POST)`
`    public ModelAndView previous(ModelAndView mov) {`
`        mov.setViewName("main");`
`        return mov;`
`    }`

我们引用认证页面的第二个位置出现在 `authentication-flow` 中，通过执行 authenticate 转换来实现，正如我们在清单 11-36 中已经看到的那样。稍后我们将看到，我们将 `authentication-flow` 作为 `createOrders-flow` 的一个子流程包含进来。当用户想要创建订单但尚未通过认证时，她会被首先重定向到登录页面。

当在活动流程中执行登录时，条件运算符的 `then` 部分会被执行（如清单 11-37 所示），表单的 `action` 属性将使用 `flowExecutionUrl` 作为 `callback URL` 重定向回我们的 `authentication-flow`。为了判断我们的认证页面是直接从 Spring MVC 加载的，还是从 `authentication-flow` 加载的，我们会检查是否存在 `flowExecutionUrl`。如果存在，则说明它是从 `authentication-flow` 内部加载的（因此我们会回发到 `flowExecutionUrl`）；否则，说明我们的认证页面是直接从 Spring MVC 加载的，我们会回发到 `authenticate.htm`。

在清单 11-38 中需要注意的第二点是，有两个输入字段（`username` 和 `password`）绑定到了 `authenticationForm`。如清单 11-39 所示，我们有 `<form:form modelAttribute="authenticationForm"…>`，这表明我们使用的表单是 `authenticationForm`。接下来，我们在该表单中为每个输入文本声明路径：

` <form:input path="username"… >`

`authenticationForm` 既用于 Spring MVC 的 `AuthenticationController`，也用于我们的 `authentication-flow`。后者可以在清单 11-35 的 `<view-state>` 中看到：

`<view-state id="authenticate" view="login"` **`model="authenticationForm"`**`>`

最后，我们还有两个按钮：login 和 previous。第一个按钮启动认证过程，而第二个按钮将用户返回到上一页。当点击 login 按钮时，如果页面是由 Spring MVC 加载的，表单会直接提交给认证方法。执行的方法带有注解，如清单 11-38 所示：

`@RequestMapping(value = "authenticate.htm", method = RequestMethod.POST)`

如果点击了 previous 按钮，则会执行清单 11-40 中的最后一个请求映射：

`@RequestMapping(value = "authenticate.htm", params = "_eventId_previous",`![Image](img/U002.jpg)
`                method = RequestMethod.POST)`

这种区分是基于 `_eventId_previous` 参数是否存在。如果存在，则会调用最后一个 `@RequestMapping`（因为它具有 `params = "_eventId_previous"` 属性），并渲染主页面。

如果认证页面是由 Web Flow 加载的，按钮名称中的 `eventId` 将决定执行哪个转换。当点击 `name _eventId_authenticate` 的按钮时，将执行 `authenticate` 转换。如果点击 `name _eventId_previous` 的按钮，则将执行 `previous` 转换。清单 11-39 展示了位于 `com.apress.prospringmvc.bookstore.web.AuthenticationForm` 的认证表单，它保存了用户名和密码。

***清单 11-39.** AuthenticationForm*

`public class AuthenticationForm implements Serializable {`
`    private String username;`
`    private String password;`

`    public String getUsername() {`
`        return username;`
`    }`
`    public void setUsername(String username) {`
`        this.username = username;`
`    }`
`    public String getPassword() {`
`        return password;`
`    }`
`    public void setPassword(String password) {`
`        this.password = password;`
`    }`
`}`

由于 Spring MVC 有一个启动认证过程的入口点（即 `AuthenticationController` 中的第二个 `@RequestMapping` 方法——参见清单 11-38），我们的 `authentication-flow` 也有一个单独的入口点。当点击 login 按钮并执行 `authenticate` 转换时，将执行此方法。这个转换是清单 11-40 中看到的第一个方法。第二个方法是一个共享方法，既用于 Spring MVC 入口点，也用于 Web Flow 入口点。

***清单 11-40.** AuthenticationController 中用于 Web Flow 的 authenticate 方法*

`// ---- POJO 逻辑`
`public Event authenticate(AuthenticationForm authenticationForm,`
`                          MvcExternalContext externalContext,`
`                          MessageContext messageContext) {`
`    try {`
`        authenticate(authenticationForm, ((HttpServletRequest)`
`        externalContext.getNativeRequest()).getSession());`
`    } catch (AuthenticationException authenticationException) {`
`            messageContext.addMessage(new`
`            MessageBuilder().error().code(LOGIN_FAILED_KEY).build());`
`            return new EventFactorySupport().error(this);`
`    }`
`    return new EventFactorySupport().success(this);`
`}`

`// ---- 辅助方法`
`private void authenticate(AuthenticationForm authenticationForm, HttpSession`
`                          httpSession) throws AuthenticationException {`
`    Account account = accountService.login(authenticationForm.getUsername(),`
`                       authenticationForm.getPassword());`
`    httpSession.setAttribute(AUTHENTICATED_ACCOUNT_KEY, account);`
`}`

现在一切就绪，我们可以将认证流程作为子流程包含到 `createOrders-flow` 中。我们可以使用 `<subflow-state>` 元素来实现这一点。图 11-10 展示了该元素可能具有的不同属性和子元素。

![Image](img/9781430241553_Fig11-10.jpg)

***图 11-10.** 子流程状态的不同属性和元素*

您将在本书的不同地方了解到这些元素中的许多内容。诸如 `<on-entry>` 和 `<on-exit>` 之类的通用元素将在“控制动作执行”部分讨论；`<transition>` 的含义与在视图状态中引入时相同；`<exception-handler>` 将在第 12 章 中深入讨论；`<secured>` 将在第 13 章 中介绍；输入/输出将在本章后面介绍；`parent` 属性将在第 12 章 讨论继承时介绍；`subflow-attribute-mapper` 将在本章后面查看子流程输入/输出映射时介绍。

我们为调用认证流程而声明的子流程状态可以在清单 11-41 中看到。

***清单 11-41.** 声明子流程*

`<subflow-state id="authenticate" subflow="authentication">`
`    <transition on="authenticationOk" to="placeOrder"/>`
`    <transition on="previous" to="selectDeliveryOptions"/>`
`</subflow-state>`

`subflow` 属性引用已注册流程的 `id`。如前所述，子流程是一个普通的流程，因此您在此处指定的 `id` 将是您在应用程序中将其作为顶级流程启动时使用的 `id`。



`<subflow-state>` 元素的 `transition` 属性上的 `attribute` 应与子流程的某个结束状态 `id` 相匹配。这样，子流程就能根据其执行的是哪个结束状态，告知父流程应转换到哪个状态。如果子流程以 `id` 为 `previous` 的结束状态终止，那么父流程（即我们的 `createOrders-flow`）将转换回 `selectDeliveryOptions`。然而，如果我们的子流程以 `id` 为 `authenticationOk` 的结束状态终止，那么我们将继续执行到 `placeOrder` 状态。

我们仍然缺少将所有内容连接起来所需的全部要素。在我们的 `createOrders-flow` 中，我们需要决定是否需要进入子流程。如果我们的用户已经通过身份验证，则无需执行子流程。我们需要一个决策状态来帮助我们。

#### 决策状态

决策状态可以包含任意数量的 `if` 元素，这些元素定义了一个 if-then-else 结构。`if` 元素的 `test` 属性定义了条件，而 `then` 和 `else` 属性则标识了目标状态。当流程进入决策状态时，第一个匹配的 `if` 将用于转换到下一个状态。这意味着所有其他 `if` 元素将不会被处理。决策状态还可以使用表达式将决策标准委托给 Java 应用程序代码。您可以在图 11-11 中看到决策状态的不同元素和属性（包括它们的顺序）。

![Image](img/9781430241553_Fig11-11.jpg)

***图 11-11.** 决策状态的不同属性和元素*

在我们的示例中，我们将在 `createOrders-flow` 中使用一个决策状态来检查用户是否已通过身份验证。如果用户未通过身份验证，我们将进入身份验证子流程，该子流程将呈现登录屏幕。如果用户之前已通过身份验证，我们可以立即继续创建订单。

![Image](img/square.jpg) **注意**  请记住，身份验证机制是一个自创的、基于会话的模型。Web Flow 与 Spring Security 有安全集成，我们将在第 13 章中看到。通常不需要自己构建这样的模型。然而，我们选择不引入 Spring Security 来分散您的注意力，因为现在我们希望您专注于本章讨论的核心 Web Flow 项目。当通过身份验证时，模型只需在 `session` 作用域上放置一个令牌。

您可以在清单 11-42 中看到决策状态的实现。

***清单 11-42.** 登录决策状态*

`<decision-state id="loginCheck">`
`<if test="externalContext.sessionMap.contains('authenticatedCustomer') ==` ![Image](img/U002.jpg)
`              true" then="placeOrder" else="authenticate"/>`
`</decision-state>`

如果提供了多个 `if` 条件，则它们会逐一被评估。如果没有一个条件评估为 `true`，则会抛出 `org.springframework.webflow.engine.NoMatchingTransitionException` 异常。您可以实现一个 `if` 条件链；但是请注意，任何定义了 `else` 子句的 `if` 条件，根据定义，都会评估为 `true`。这意味着剩余的 `if` 条件都不会被评估。因此，如果您有多个 `if` 元素，那么只有最后一个 `if` 元素拥有 `else` 才有意义。如果前面的某个 `if` 有 `else`，那么后续的 `if` 元素将永远不会被评估。仔细想想，这是有道理的；如果一个 `if` 也有一个 `else` 条件，那么要么执行 `then`，要么执行 `else`。因此，所有定义在该 `if` 条件之后的 `if` 元素都会被跳过。

在我们向您展示本章所做工作的概述之前，我们想执行最后一次重构——针对创建订单流程。该流程现在作为 `finish` 转换的一个评估动作嵌入其中。我们将用一个动作状态来替换它。

#### 动作状态

动作状态执行一个动作作为其活动。图 11-12 展示了它的各种属性和元素。在 XML 流程定义中，动作状态使用 `<action-state>` 元素定义，而要执行的动作则使用嵌套的 `<evaluate>` 元素定义。动作状态通常用于与后端服务交互，无论是直接交互还是通过控制器。动作状态会自动执行，并且不会暂停流程执行。视图状态会暂停流程执行（视图被渲染，用户花时间与视图交互），但动作状态会触发事件，导致转换到另一个状态。

![Image](img/9781430241553_Fig11-12.jpg)

***图 11-12.** 动作状态的不同属性和元素*

在接下来的示例中，当订单成功创建时，我们转换到结束状态。我们也可以响应“错误”事件，并转换到错误页面。

![Image](img/square.jpg) **注意**  没有单一的规则来确定是否需要动作状态。如果存在多个您可以响应的结果，或者处理过程更复杂（例如，执行多个动作时），您可能应该使用动作状态。

为了重构这一点，我们只需将评估表达式移动到一个 `<action-state>` 中，并相应地调整转换。清单 11-43 中显示的代码变成了清单 11-44 中显示的动作状态代码。此代码可以在 `src/main/webapp/WEB-INF/view/createOrders/createOrders-flow.xml` 中找到。

***清单 11-43.** 将评估动作重构为动作状态*

`<transition on="finish" to="end">`
`    <evaluate expression="orderController.placeOrder(flowScope.orderForm,`
`     externalContext.sessionMap.authenticatedAccount)"/>`
`</transition>`

***清单 11-44.** 用于下单的动作状态*

`<action-state id="placeOrder">`
`    <evaluate expression="orderController.placeOrder(externalContext.`![Image](img/U002.jpg)
`sessionMap.authenticatedAccount,` ![Image](img/U002.jpg)
`                          flowScope.orderForm)"`
`              result="flowScope.orderId"/>`
`    <transition on="success" to="end"/>`
`</action-state>`

`authenticatedAccount` 变量是从 `session` 作用域中查找的。它是在用户使用 `AuthenticationController` 登录时被放置在那里的。您可以在清单 11-40 的最后一个方法中看到这一点。`OrderForm` 是在 `createOrder-flow` 开头的 `on start` 元素中创建的。它被放置在流程作用域上。我们不需要特别指定作用域，因为表达式语言会自动在所有 Web Flow 作用域中查找（即，不指定隐式的 `flowScope` 对象而直接指定 `orderForm` 也是正确的，并且会产生相同的结果）。



#### 处理结果事件

如我们所见，可以使用 evaluate 动作来调用应用程序控制器上的方法。当从动作状态或决策状态调用时，这些方法的返回值可用于驱动状态转换。由于转换由事件触发，因此必须先将方法返回值映射到 `Event` 对象。

例如，在 `AuthenticationController` 的 `authenticate` 方法中，我们创建了由应用程序控制器直接返回的所谓事件：

`return new` **`EventFactorySupport().error(this);`**
`return new` **`EventFactorySupport().success(this);`**

在这种情况下，动作的结果事件成为转换决策的主要依据。当与控制器或 POJO 动作结合使用时，这允许你调用应用程序代码中的一个方法，该方法返回一个单一值，该值可用作路由决策的基础。

如果你不返回 `org.springframework.webflow.execution.Event`，Web Flow 将根据一组规则自动将方法返回值适配为适当的结果事件。这些规则很简单，并在表 11-3 中进行了说明。

![图片](img/9781430241553_tab11-03.jpg)

在我们的动作状态中，`placeOrder()` 方法返回了一个 `Long` 类型的对象。这导致 Web Flow 创建了一个带有 `success` 标识的事件——因此，我们的转换条件为 `on="success"`。

每当你返回一个值时，无论是带有属性的显式事件还是任何其他对象（Web Flow 会为你创建一个 `Event`），你都可以使用评估表达式的 `result` 属性（参见清单 11-43 的示例）来绑定该值。如果事件的标识与动作状态的任何转换都不匹配，则会抛出 `NoMatchingTransitionException` 异常。

然而，如果你在转换中嵌套了一个 evaluate 动作，你只能决定该转换是允许继续还是中止。你可以通过返回一个成功或错误事件来实现这一点，无论是直接返回，还是让 Web Flow 为你映射。

如果你不返回显式的成功或错误事件，那么 Web Flow 将把你的返回值映射到一个 `Event`，如表 11-3 所述。对于 `String`、`Enum` 或 `Boolean` 类型，该值必须为 `success`、`yes` 或 `true` 才能被解释为成功的 `Event`。在所有其他情况下，转换将被中止，并重新渲染最后一个视图状态。

![图片](img/square.jpg) **注意**  在 `AuthenticationController` 中，我们选择在应用程序代码本身中生成 Event，以说明如何创建此类事件。在那个特定情况下（结果要么是 OK 要么是不 OK，即身份验证要么成功要么失败），使用 `Boolean` 同样也是一个很好的选择。

#### 概述

应用了之前所有的重构之后，清单 11-45 展示了我们的 `createOrders-flow` 现在的样子。你可以在 `src/main/webapp/WEB-INF/view/createOrders/createOrders-flow.xml` 文件中找到此文件。

***清单 11-45.** 最终重构后的 createOrders-flow*

`<on-start>`
`    <evaluate expression="orderController.initializeForm()"`
`        result="flowScope.orderForm" />`
`</on-start>`
`<view-state id="selectCategory" view="selectCategory" model="orderForm">`
`    <on-render>`
`        <evaluate`![图片](img/U002.jpg)
`          expression="orderController.initializeSelectableCategories()"`![图片](img/U002.jpg)
`          result="requestScope.selectableCategories" />`
`    </on-render>`
`    <transition on="next" to="selectBooks" />`
`    <transition on="cancel" to="end" />`
`</view-state>`
`<view-state id="selectBooks" view="selectBooks" model="orderForm">`
`    <on-render>`
`        <evaluate expression="orderController.`![图片](img/U002.jpg)
`                  initializeSelectableBooks(flowScope.orderForm)"`![图片](img/U002.jpg)
`                  result="flowScope.selectableBooks" />`
`    </on-render>`
`    <transition on="previous" to="selectCategory" />`
`    <transition on="add">`
`        <evaluate expression="orderController.addBook(flowScope.orderForm)" />`
`    </transition>`
`    <transition on="next" to="selectDeliveryOptions" />`
`    <transition on="reset" to="selectBooks">`
`        <evaluate expression="orderForm.resetSelectedBooks()" />`
`    </transition>`
`    <transition on="cancel" to="end" />`
`</view-state>`
`<view-state id="selectDeliveryOptions" view="selectDeliveryOptions" model="orderForm">`
`    <transition on="previous" to="selectBooks" />`

`    <transition on="finish" to="loginCheck">`
`<evaluate` ![图片](img/U002.jpg)
`         expression="orderController.validateDeliveryDate(flowScope.orderForm,`![图片](img/U002.jpg)
`         messageContext)"/>`
`    </transition>`

`    <transition on="cancel" to="end" />`
`</view-state>`
`<decision-state id="loginCheck">`
`    <if test="externalContext.sessionMap.contains('authenticatedAccount')`![图片](img/U002.jpg)
`              == true" then="placeOrder" else="authenticate" />`
`</decision-state>`
`<subflow-state id="authenticate" subflow="authentication-flow">`
`    <transition on="authenticationOk" to="placeOrder" />`
`    <transition on="previous" to="selectDeliveryOptions" />`
`</subflow-state>`
`<action-state id="placeOrder">`
`    <evaluate expression="orderController.placeOrder(externalContext.sessionMap`![图片](img/U002.jpg)
`.authenticatedAccount, flowScope.orderForm)"`![图片](img/U002.jpg)
`     result="flowScope.orderId"/>`
`    <transition on="success" to="endOrderOk"/>`
`</action-state>`
`<end-state id="end" view="redirect:/index.htm" />`



### 子流程输入/输出映射

正如我们在“不同 Web 流程作用域”一节中所见，每个流程会话都有自己的 `flow` 作用域。我们还说明了子流程也是如此。在作用域层面，子流程与普通流程并无区别，它们在自己的流程会话中运行——因此，它们拥有自己的 `flow` 作用域。只有 `conversation` 作用域在多个流程会话之间共享。那么，如果你需要在子流程内部访问来自父流程的数据，该怎么办？当然，你可以将其放入 `conversation` 作用域，但这有一些缺点。

子流程最好被视为一个具有特定输入和输出契约的黑盒。仅仅为了便于数据访问而将所有内容放入 `conversation` 作用域会破坏封装性，并可能导致脆弱的流程设计。一个更好的选择是将数据带入子流程的 `flow` 作用域——但仅限于子流程所需的数据。Web Flow 提供了一些特殊功能来为子流程定义输入和输出契约。这样，你就能确切知道它需要什么数据作为输入，以及它会返回什么数据。

你可以通过向流程添加*输入映射*来定义流程的输入契约。输入映射负责将所有在流程启动时提供的输入映射到其 `flow` 作用域中。你还可以通过*输出映射*来定义输出契约。输出映射定义了执行流程后预期会返回的值，并且这些值可以映射回父流程的 `flow` 作用域。

在继续之前，你应该了解 Web Flow 如何处理输入和输出属性。它为输入和输出分别分配了一个独立的映射。这个映射是独立的，仅用于在来自父流程的属性与子流程的 `flow` 作用域之间建立过渡。当流程启动时，它会收集信息放入输入映射。这些信息可能来自 URL 或 `<subflow-state>` 的输入。你可以在启动子流程中使用 `<input>` 元素来访问输入映射。

这里，流程-x 向子流程（流程-y）暴露了一个名为 `varX` 的属性。这是通过在子流程状态中指定 input 元素来实现的。这使得 Web Flow 能够从流程-x 的 `flow` 作用域中获取键 `varX`，然后将该键/值对放入输入映射。当流程-y 启动时，它会在输入映射中查找 `varX` 属性，并将其放入流程-y 的 `flow` 作用域。

在图 11-13 中，你还可以看到输入映射中填充了来自 URL 的值。这是你拥有的第二个选项。除了使用 `<subflow-state>` 的 `<input>` 元素传递值之外，Web Flow 还会读取你的 URL，并将所有可用的请求参数放入输入映射。这样做的原因是，它允许你轻松地将一个流程既作为独立流程使用，也作为子流程使用。

![图片](img/9781430241553_Fig11-13.jpg)

***图 11-13.** 子流程输入/输出映射*

![图片](img/square.jpg) **注意**  输出映射与输入映射镜像对应，适用相同的原则和规则。不过，我们现在有两个 `<output>` 元素，而不是两个 `<input>` 元素。一个位于父流程的 `<subflow-state>` 中，另一个位于子流程本身中。此外，还有一个输出映射，用于将值从子流程映射回父流程（如图 11-13 所示）。

除了使用 `<input>` 和 `<output>` 元素之外，你还可以指定一个自定义的 `subflow-attribute-mapper`。这是 `<subflow-state>` 元素的一个属性。它允许使用 Java 类进行自定义映射，而不是在流程内部执行映射。该属性接受一个对 `org.springframework.webflow.engine.SubflowAttributeMapper` 接口实现的 bean 引用。这个类有两个方法：

`public MutableAttributeMap createSubflowInput(RequestContext context);`
`public void mapSubflowOutput(AttributeMap output, RequestContext context);`

使用这种方法时，你可以在自定义的 `SubflowAttributeMapper` 实现中自行指定映射。这相当少见；通常，标准的输入和输出元素足以映射你所需的任何内容。然而，如果在映射数据时需要访问某些资源，这种方法将帮助你实现这一点，因为你可以完全控制映射过程。



### 将订单流程创建为子流程

到目前为止，我们已经创建了一个用于执行身份验证的子流程。接下来，我们将进一步重构 `createOrders-flow`，并将实际*下达*订单的部分提取到一个子流程中。请记住，我们之前已将其重构为 `createOrders-flow` 中的一个动作状态（参见清单 11-46）。

***清单 11-46.** 回顾用于下单的动作状态*

`<action-state id="placeOrder">`
`    <evaluate`
`      expression="orderController.placeOrder(`![Image](img/U002.jpg)
`      externalContext.sessionMap.authenticatedAccount, flowScope.orderForm)"`![Image](img/U002.jpg)
`      result="flowScope.orderId"/>`
`    <transition on="success" to="endOrderOk"/>`
`</action-state>`

接下来，我们将把这个动作状态移动到一个名为 `placeOrders-flow.xml` 的子流程中。该流程将放置在此目录中：`src/main/webapp/WEB-INF/view/placeOrders/`。为了使动作状态在子流程中正常工作，我们需要传递我们的订单表单。该订单表单存储在父流程的 `flow` 作用域中；并且，正如我们已经看到的，子流程将拥有自己的 `flow` 作用域。因此，我们需要将订单表单从父流程的 `flow` 作用域映射到子流程的 `flow` 作用域。账户信息不需要映射，因为我们将账户信息放在了 Servlet 的 `session` 作用域中。这个作用域在整个流程中都是可访问的（甚至在 Web Flow 之外，例如在 Servlet 或 JSP 中，因为它是一个 JEE 作用域）。一旦子流程完成，我们还需要将订单 ID 发送回父流程。我们的子流程将如清单 11-47 所示；此代码可以在 `placeOrders/placeOrders-flow.xml` 中找到。

***清单 11-47.** 创建流程*

`<input name="orderForm" required="true"` ![Image](img/U002.jpg)
`        type="com.apress.prospringmvc.bookstore.web.OrderForm"/>`

`<action-state id="placeOrder">`
`    <evaluate expression="orderController.placeOrder(externalContext`![Image](img/U002.jpg)
`.sessionMap.authenticatedAccount, orderForm)"`
`      result="flowScope.orderId"/>`

`    <transition on="success" to="endOrderOk"/>`
`</action-state>`

`<end-state id="endOrderOk">`
`    <output name="orderId" />`
`</end-state>`

首先，我们将 `orderForm` 从输入映射映射到子流程的 `flow` 作用域。我们还声明该输入是必需的，并声明它属于特定类型。`required` 和 `type` 属性都是可选的。`type` 只是一个额外的检查，以确保输入的类型正确。当输入不是指定的类型时，将尝试进行类型转换。如果转换失败，则会引发异常。如果缺少必需的输入，则在启动子流程时会引发异常（`FlowInputMappingException`）。

在结束状态中，我们使用 `output` 元素将结果（在本例中为订单 ID）映射回父流程。如果你有多个结束状态，并且它们都将相同的结果输出回父流程，那么你也可以从每个结束状态中移除 `output` 元素，并将 `output` 元素放在最后一个结束状态的下方。这样，你就不必为每个结束状态重复 `output` 元素。无论哪个结束状态终止了子流程，输出都会被映射。清单 11-48 展示了如何声明一个单一的 `output` 元素，当任何结束状态终止子流程时，该元素会将声明的变量映射到父流程。

***清单 11-48.** 声明单一输出元素*

`<end-state id="endstateOne"/>`
`<end-state id="endstateTwo"/>`
`<output name="varX"/>`

最后，清单 11-49 展示了父流程中的子流程状态；这将声明要执行哪个子流程，并由 `subflow` 属性指示。该值是我们要作为子流程运行的流程在 Web Flow 中映射的 ID。这并非新内容——我们在之前的“将身份验证实现为子流程”部分已经介绍过。

***清单 11-49.** 父流程中的子流程状态*

`<subflow-state id="placeOrder" subflow="placeOrders">`
`    <input name="orderForm"/>`
`    <output name="orderId"/>`

`    <transition on="endOrderOk" to="endOrderOk"/>`
`</subflow-state>`

在子流程状态内部，我们可以看到输入映射。这是我们将提供给子流程的值。这里，我们引用了 `orderForm` 变量。

输出映射是输入映射的逆过程；换句话说，这是子流程返回的值，我们将把它绑定到父流程内的一个变量。对于输出映射，我们选择了变量名 `orderId`，默认情况下它将被放入 `flow` 作用域。输出值（订单 ID）将被我们的结束状态使用，结束状态将用它来传递流程结束时将要渲染的目标 URL（我们将在下一节讨论结束状态）。转换也不是新内容——我们在“将身份验证实现为子流程”部分已经介绍过。当子流程终止时，它将触发此转换，因为 `on` 属性与子流程的结束状态 ID 匹配，并将我们带到父流程的结束状态。

![Image](img/square.jpg) **警告**  你不能在 `input` 或 `output` 元素中指定作用域。例如，指定 `input name="flowScope.orderForm"` 将会引发异常。默认情况下，你在 `input` 元素中引用的值是从 `flow` 作用域解析的，并且值会以你在 `output` 元素中指定的名称放回到 `flow` 作用域。这是一个合理的限制，这一点尚有争议。事实是，`flow` 作用域是唯一用于输入/输出映射的逻辑作用域，因为 `conversation`、`flash` 和 `request` 作用域是与子流程共享的。请注意，`view` 作用域不共享，但一旦你的流程离开视图状态，它就会被清除。

![Image](img/square.jpg) **注意**  一个元素（`<input>`）同时出现在 `<subflow-state>` 和子流程本身中，这看起来可能很奇怪。理解这一点最好的方式是，在 `<subflow-state>` 元素中，你定义了要从给定值/表达式中映射*到* *输入*映射的键。在作为子流程启动的流程的 `<input>` 元素中，`<input>` 元素说明了要从*输入*映射中取出哪些键并放入子流程的 `flow` 作用域（`<output>` 元素也是同理，它映射到输出映射）。



### 结束状态

结束状态会终止当前的活动流会话。图 11-14 展示了该状态的属性和元素。

![图片](img/9781430241553_Fig11-14.jpg)

***图 11-14.** 结束状态的不同属性和元素*

代码清单 11-50 展示了一个终止活动流会话的结束状态示例。你可以在 `src/main/webapp/WEB-INF/view/createOrders/createOrders-flow.xml` 文件中找到这段代码。如果被终止的流会话是当前正在执行的流的根流会话，那么整个流执行过程将会结束。当该会话是一个子流会话时，处理过程将在父流中恢复。

***代码清单 11-50.** 终止活动流会话的结束状态*

`<end-state id="endOrderOk" view="redirect:/ordersOverview.htm?orderOk=`![图片](img/U002.jpg)
`true&amp;orderId={orderId}"/>`
`<end-state id="end" view="redirect:/index.htm"/>`

![图片](img/square.jpg) **注意**  在代码清单 11-50 中，我们使用了一个 XML 实体（`&amp;`）来转义 `&` 字符。这是强制性的，因为某些字符（例如 `"`、`<`、`>` 和 `&`）在 XML 中不允许直接出现，因为它们具有特殊含义，因此需要进行转义。

一个流可以有任意数量的结束状态。一个流没有结束状态的情况也并不少见，例如当你有一个重复性的流程（如搜索，通常有一个“再次搜索”按钮）时。拥有多个结束状态也很常见——每个结束状态对应流的一个逻辑结果。如示例代码所示，我们有一个结束状态用于处理用户想要取消订单流的情况。如果发生这种情况，我们会将用户重定向到主页。我们还有一个结束状态，一旦订单成功创建，就会将用户重定向到订单概览页面。

结束状态可以选择性地引用一个视图。这样的视图可用于确认流处理已成功结束；这通常被称为*确认视图*。只有当结束状态终止了整个流执行时，该结束状态引用的视图才会被渲染。如果结束状态结束的是一个子流会话，那么视图的选择就由恢复执行的父流负责。

在“子流”部分，我们还看到结束状态可以有一个输出元素。这允许结束状态将数据映射回父流。你可以在代码清单 11-47 中看到实际应用。

![图片](img/square.jpg) **警告**  我们已经知道 Web Flow 使用了 PRG 模式。然而，结束状态有点例外。当触发结束状态时，Web Flow 会在处理 PRG 模式中的第一个请求（POST 请求）后终止流执行。它还会在处理第一个请求后渲染并发送回视图——但是，它不会像通常那样发送重定向。如果发出了正常的重定向，PRG 模式中的第二个请求（GET）会触发流执行重启，因为流执行在处理第一个请求（同样是 POST）后已经被终止了。这意味着，如果你在到达结束状态后刷新页面，你将重新启动该流。为了避免这种情况，最好让结束状态重定向到一个*稳定的*（可能是外部的）URL。示例代码通过一些 `redirect` 语法实现了这一点。对于应用程序上下文之外的外部重定向，你可以使用 `externalRedirect` 属性。在这种情况下，将不再有对流执行的传入请求，因为新渲染的 URL 与该流不再有任何关系。

### 本章小结

完成本章后，你应该对大多数 Web Flow 特性及其应用方法有了扎实的理解。你应该能够在自己的应用程序中开始使用 Web Flow，并在遇到疑问或需要重温 Web Flow 行为的具体细节时，随时参考本章和第 10 章。

在本章中，我们展示了如何使用 Web Flow 解决实际用例。这些用例包括转换、格式化和验证。在此过程中，你详细了解了如何在不同类型的验证中进行选择。本章还讨论了子流，它允许你构建更易于维护和可重用的流。例如，我们涵盖了所有不同的作用域，以及如何使用它们。我们还回顾了所有的动作状态，并介绍了如何在我们的示例应用程序中使用它们。

现在，你可以继续学习下一章，也是最后一章关于 Web Flow 的内容，在那里你将探索 Web Flow 提供的一些更高级的功能。

## 第 12 章

## 高级 Spring Web Flow

欢迎来到关于 Spring Web Flow 的最后一章。在前几章中，我们让你熟悉了 Web Flow 的基础知识，例如如何配置 Web Flow、如何将其与 Spring MVC 集成，以及如何设置一个基本流。我们还解释了不同的元素，例如动作状态、模型绑定、验证、应用程序控制器（以及如何调用它们）、表达式等等。

我们在前几章中的方法是逐步向你介绍其中包含的概念，并随着章节的推进在这些概念的基础上进行构建。在第 10 章中，我们从 Web Flow 的基本高级视图开始，引入了一个简单的用例，然后将其构建为一个示例应用程序，以说明使用 Web Flow 的不同方面。

在第 11 章中，我们通过进一步重构和用新的 Web Flow 功能构建示例应用程序来扩展这些内容。

本章将继续构建在前几章涵盖的主题之上；然而，本章中的主题耦合度较低。每个主题或多或少都应被视为一个独立的主题，讨论其自身的 Web Flow 特性。

我们将继续使用上一章结束时的示例应用程序，并进行修改以支持我们在此讨论的功能。一个功能可以是全新的，也可以是对我们在前一章中看到的内容的扩展。我们特意这样划分章节，既是为了让前一章尽可能易于消化，也是为了让你能够带着所需的 Web Flow 知识开始本章的学习，从而直接切入某个特定功能。

在读完这最后一章关于 Web Flow 的内容后，你应该对几乎所有最重要的 Web Flow 特性有透彻的理解，包括如何在实践中以及何时使用它们。

### 继承

在上一章中，我们向你介绍了子流，这将帮助你构建和重用你的流。但还有另一个 Web Flow 特性可以帮助你实现这种结构和重用：继承。*Web Flow 提供了两种形式的继承：*

*   流继承
*   状态继承

在上一章中，我们还看到流根元素上有一个特殊的属性，你可以用它来声明流继承（`parent` 属性）。在本章中，我们将演示该功能。除了流之外，继承也可以单独应用于五种不同的状态（即 `view`、`action`、`decision`、`subflow` 和 `end` 状态）。在接下来的章节中，我们将详细讨论这些模块化特性中的每一个。同样，我们将基于前几章构建的示例应用程序来构建这些示例。



#### 流程继承

你可以让一个流程继承由另一个或一组流程定义的元素。通过使用 `parent` 属性来标记每个包含你想要继承元素的流程，即可声明这种继承关系。一个常见的用例是让父流程定义全局转换和异常处理器。该流程应被视为父流程与当前（子）流程的合并。与 Java 不同，你*可以*从多个父流程继承。只需用逗号分隔每个流程；顺序无关紧要（参见清单 12-1）。

***清单 12-1** 流程继承允许一个流程拥有多个父流程*

`<flow parent="global-initialization-flow, global-actions-flow"/>`

![Image](img/square.jpg) **提示** 假设你有多个流程共享大量通用功能。在这种情况下，你可以将通用功能从各个流程中移出，放入一个*父流程*中。然后，你可以让每个子流程继承该父流程。然而，如果你这样定义，父流程将像其他任何流程一样是一个普通流程（它需要有一个 id，并在 Web Flow 中注册等）。这也意味着你将能够启动父流程，因为它是一个独立的流程。但在这种情况下这样做没有意义，因为父流程必须始终与某个子流程结合使用。应该启动的是子流程，而不是它们共有的父流程。请注意，你可以将父流程声明为 `abstract`。这将告诉 Web Flow 禁止启动该流程的执行（无论是顶层流程还是通过子流程）。但是，如果从这样的流程继承，子流程可以正常作为顶层流程或子流程启动。你可以通过将流程根元素上的 `abstract` 属性设置为 `true` 来实现这一点。

Web Flow 将从子流程及其所有父流程创建一个*合并后*的流程。重复元素的规则分为*可合并*和*不可合并*元素。如果元素相似，可合并元素将始终尝试合并在一起。父流程或子流程中的不可合并元素将原封不动地保留在最终流程中；它们不会作为合并过程的一部分被修改。

当满足以下条件时，元素被视为可合并：

*   它们的关键属性匹配：如果不匹配，则归入不可合并类别，并按原样保留在合并后的流程中。关键属性是 Web Flow 识别为用于可合并性匹配的元素属性。换句话说，当父流程和子流程中存在相同的元素类型，并且该属性的值匹配时，该元素被视为可合并。
*   它们被标记为 `始终合并`（参见表 12-1）：在这种情况下，可合并元素无论如何都会被合并。此时，没有需要首先匹配的*关键*属性。

例如，清单 12-2 展示了一个虚构的流程，它有一个包含两个转换的 `view` 状态。关注继承方面，`view` 状态渲染哪个视图并不重要，每个转换转向哪个状态也不重要。

***清单 12-2.** 一个具有单个视图状态和两个转换的流程*

`<flow`

`      xsi:schemaLocation="http://www.springframework.org/schema/webflow`
`        http://www.springframework.org/schema/webflow/spring-webflow-2.0.xsd">`

`    <view-state id="someViewState" view="someView" >`
`        <transition on="x" to="someState"/>`
`        <transition on="y" to="someOtherState"/>`
`    </view-state>`
`</flow>`

如果我们将其拆分为两个流程，例如一个父流程和一个子流程，那么它将如清单 12-3 和 12-4 所示。

***清单 12-3.** 父流程，包含两个转换中的一个*

`<flow`

`      xsi:schemaLocation="http://www.springframework.org/schema/webflow`
`        http://www.springframework.org/schema/webflow/spring-webflow-2.0.xsd"`
**`abstract="true">`**

`    <view-state id="someViewState" view="someView" >`
`        <transition on="x" to="someState"/>`
`    </view-state>`
`</flow>`

***清单 12-4** 子流程，包含另一个转换并继承自父流程*

`<flow`

`      xsi:schemaLocation="http://www.springframework.org/schema/webflow`
`        http://www.springframework.org/schema/webflow/spring-webflow-2.0.xsd"`
**`parent="parent-flow"`**`>`

`    <view-state id="someViewState" view="someView" >`
`        <transition on="y" to="someOtherState"/>`
`    </view-state>`
`</flow>`

清单 12-3 中列出的父流程不能直接启动，因为它被声明为 `abstract`。清单 12-4 中列出的子流程可以直接启动。由于子流程继承自父流程，Web Flow 将扫描可合并元素。它会发现，在父流程和子流程中，都存在一个 id 为 `someViewState` 的 `view` 状态。现在它将合并 `view` 状态，组合其内部的所有元素。这将产生一个看起来与原始流程相同的合并后流程（参见清单 12-5）。

***清单 12-5.** 合并后的流程*

`<flow`

`      xsi:schemaLocation="http://www.springframework.org/schema/webflow`
`        http://www.springframework.org/schema/webflow/spring-webflow-2.0.xsd">`

`    <view-state id="someViewState" view="myView" >`
`        <transition on="x" to="someState"/>`
`        <transition on="y" to="someOtherState"/>`
`    </view-state>`
`</flow>`

这个合并后的流程在物理上并不存在；相反，它是在 Web Flow 启动并扫描你的流程定义时在内存中生成的。物理上，你只有父流程和子流程。在这个例子中，`view` 状态是一个可合并元素，因为它具有相同的 id，而 id 是 Web Flow 决定合并该元素的 `view` 状态的关键属性。如果 id 值不同，那么 Web Flow 会将父流程中的元素原封不动地复制并粘贴到合并后的流程中。

在表 12-1 中，你可以看到不同的元素及其关键属性。如果子流程和父流程中都存在一个具有相同关键属性的元素，则该元素将被标记为合并。否则，该元素将完全按原样放入合并后的流程。如果一个元素的关键属性是 `始终合并`，这意味着不需要匹配特定属性，并且父流程和子流程之间相同类型的元素始终合并。

![Image](img/tab_12_1.jpg)

![Image](img/tab_12_1a.jpg)

以下是不可合并元素：`bean-import`、`evaluate`、`exception-handler`、`persistence-context`、`render`、`set` 和 `var`。如前所述，它们会原封不动地成为合并后流程的一部分，并从父流程整体复制粘贴到合并后的流程中。

在表 12-1 中未标记为关键属性，并且在父流程和子流程的元素上都有指定的属性，将被子流程覆盖。例如，考虑 `<view-state>` 元素的 view 属性。当父流程包含 `<view-state id="x" view="a">` 且子流程包含 `<view-state id="x" view="b">` 时，该元素符合合并条件，因为 `view` 状态的 id 匹配。因此，每个子元素都将从父流程继承；但在这种情况下，view 属性在父流程和子流程中定义的值不同。合并后的流程在渲染时将导航到视图 `"b"`。



对于在父流程和子流程中指定了相同标识符的不可合并元素，同样适用此规则：子流程的值优先。例如，父流程有以下 var 元素：`<var name="x" value="com.apress.prospringmvc.bookstore.**ClassX**"/>`。子流程也有一个同名但值不同的 var 元素：`<var name="x" value="com.apress.prospringmvc.bookstore.**ClassY**"/>`。通用的覆盖规则在此适用，名为 `x` 的变量将拥有类型为 `com.apress.prospringmvc.bookstore.**ClassY**` 的对象值，即子流程中的值。如果我们将子流程中的 `<var>` 元素移除，那么将只剩下一个名为 `x` 的 var 元素（即父流程中的那个）。在这种情况下，变量 `x` 将变成类型为 `ClassX` 的对象。

![Image](img/square.jpg) **警告** 父流程中外部资源的路径应为绝对路径。当两个流程合并时，相对路径将会失效，除非父流程和子流程位于同一目录。合并后，父流程中的所有相对路径都将变为相对于子流程的路径。在我们的示例中，这问题不大，因为我们不直接处理资源。我们的视图是字面量，与 Tiles 配置中的定义名称相匹配。但是，如果你没有使用 Tiles，而是直接在流程中引用 JSP，那么你可能会遇到这样的情况（假设示例）：一个位于 `WEB-INF\views\parent\parent-flow.xml` 的 `parent-flow` 流程，它有一个单一的 `view` 状态：

`<view-state id="someViewState" view="thePage.jsp"/>`

在这种情况下，`thePage.jsp` 与流程位于同一位置（`WEB-INF\views\parent\thePage.jsp`）。因此，它是一个指向资源的相对指针。如果另一个目录（例如 `WEB-INF\views\child\child-flow.xml`）中的子流程现在要继承此流程，那么来自 `someViewState` 的 `view` 状态的 `thePage.jsp` 资源将尝试从子流程的位置 `WEB-INF\views\child\thePage.jsp` 解析——当然，该位置并不存在。在这种情况下，你应该通过指定完整路径来使资源路径变为绝对路径。与其在父流程中指定 `view="thePage.jsp"`，不如指定以下路径：

`view="WEB-INF\views\parent\thePage.jsp"`

#### 状态继承

除了在 flow 元素上指定 `parent` 属性外，你还可以在各个状态上指定 `parent` 属性。如果你希望对父流程中的哪些元素实际被继承进行更精细的控制，这会很有用。这仅在未在 flow 级别应用继承时才有意义；换句话说，仅当你未在 flow 元素上指定 `parent` 属性，而仅在你希望继承的那些状态上指定它时才有意义：

*   与流程继承不同，你只能从一个状态继承。
*   你继承的父状态应为同一类型。例如，`view` 状态不能继承自 `action` 状态。
*   基本上，适用于流程继承的相同规则也适用于此处。唯一的区别是粒度级别更细，因为你现在可以指示需要继承哪些元素。唯一显著的区别是，状态始终与指定的父状态合并。对于流程继承，元素是否具有相同的 key 属性很重要，因此它可以被合并或原样保留。由于我们在此直接指定状态，因此无论何种情况，它始终会被合并。
*   你必须指定要继承的状态所在的流程。格式如下：`parent="parent-flow#parent-state"`

![Image](img/square.jpg) **注意** 你可能会想，如果父流程和子流程中存在同名的 `view` 状态，并且它们应用了状态继承，会发生什么情况。例如，在子流程中，假设有一个这样的 `view` 状态：`<view-state id="viewStateX" view="someView" parent="parent#viewStateX">`。同时，父流程也有一个名为 `viewStateX` 的 `view` 状态。如前所述，这无关紧要。状态始终会被合并。在这种情况下，父流程中定义的 `viewStateX` 的每个属性和元素都会与子流程中的 `viewStateX` 合并。如果父流程和子流程都提供了相同的属性或具有相同标识符的元素，则保留子流程的版本。

### Web Flow 配置自定义

在第一章中，我们向你介绍了核心的 Web Flow 配置。接下来，我们将了解如何对此配置进行一些调整，这些调整可能对你的应用程序性能以及/或者流程在应用程序中的管理方式很重要。

我们还看到 Web Flow 默认使用 SpEL，但并非必须如此。我们将讨论的第二个自定义项解释了如何将默认表达式语言更改为其他语言。



#### 执行与会话快照

我们已经配置了 `org.springframework.webflow.executor.FlowExecutor`，但未指定任何特定选项。对于我们的基本设置，开箱即用的配置已经足够：

`    <webflow:flow-executor id="flowExecutor"/>`

`flow-executor` 中唯一值得注意的子元素是 **<webflow:flow-execution-repository>**，它允许你自定义流程执行器的内部机制。它有两个重要的属性：`max-execution-snapshots` 和 `max-executions`。我们将在清单 12-6 中演示如何定义它们，并在下一段中进行描述。

***清单 12-6.** 自定义流程注册表配置*

`    <webflow:flow-executor id="flowExecutor" flow-registry="flowRegistry" >`
`<webflow:flow-execution-repository` **`max-execution-snapshots="30"`![Image](img/U002.jpg)**
**`max-executions="5"/>`**
`    </webflow:flow-executor>`

`max-execution-snapshots` 和 `max-executions` 属性允许你精细调整在“过去”中可以回溯多远。在上面的代码示例中，我们将它们设置为默认值。因此，将 `max-execution-snapshots` 指定为 `30`，将 `max-executions` 指定为 `5` 与完全不指定它们的效果相同，因为这些是在未设置其他值时的默认值。我们在代码片段中输入这些值是为了演示如何使用这些属性，并突出显示它们的默认值。

在第 10 章中，我们提到，每次向流程执行发起请求时，都会在返回响应之前创建其状态的快照（参见第 10 章的图 10-2 及其对 Web Flow 状态管理机制的描述）。`max-execution-snapshots` 属性映射到流程执行中可以存储多少个这样的快照。如果将此值设置为 `0`，则无法在流程执行中返回到先前的状态。实际上，你的“后退”按钮会看起来像失效了一样。值为 `-1` 表示“无限制”——你可能永远不想使用这个值，因为你希望将用户会话的大小控制在合理范围内。

`max-executions` 属性映射到给定会话中可以同时激活多少个并发流程执行。每当你启动一个新的流程执行（并且没有显式结束前一个流程执行时），你仍然可以返回到前一个流程执行——它会与其所有状态一起被存储，具体取决于 `max-execution-snapshots` 属性的值。因此，清单 12-3 中的配置意味着你可以回溯到前五个流程执行；在每个流程中，你可以浏览最近的 30 个 `view` 状态。同样，值为 `0` 会完全禁用此功能，而 `-1` 则将数量设置为无限（记住：你可能不想使用那个值）。

对于 `max-execution-snapshots` 和 `max-executions` 这两个属性，每当达到配置的最大值时，Web Flow 将分别移除最旧的快照或最旧的执行。因此，如果我们将 `max-executions` 设置为 `2`，那么在启动第三个执行后，Web Flow 会简单地清理并移除第一个启动的执行。`max-execution-snapshots` 也遵循同样的原则。

![Image](img/square.jpg) **注意** 这些设置始终适用于用户的会话，而不是整个应用程序。因此，如果我们将 `max-conversations` 设置为 `5`，这意味着每个用户（以及每个会话）最多可以同时拥有五个活跃的会话（如果启动了第六个会话，最旧的会话将被 Web Flow 清理并移除）。

![Image](img/square.jpg) **警告** 我们现在就告诉你，你应该考虑这些默认值。你可能希望调整它们以满足你的需求，特别是当你的流程中有很多 `view` 状态，并且在流程中存储了大量状态时——在这种情况下，这些默认值会很快开始占用大量内存（因此，你可能希望降低这些数值）。对于内存密集型应用程序（或内存受限的环境），将 `max-execution-snapshots` 设置为 `5`，将 `max-executions` 设置为 `1` 是完全合理的设置。此外，大多数应用程序不需要能够回溯超过五个状态或超过一个先前的流程执行。如果你完全不需要这种状态“保存/恢复”快照功能，那么将两个值都设置为 `0` 以禁用它。

#### 更改表达式解析器

在上一章中，我们了解了如何使用 `<webflow:flow-builder-services>` 来自定义行为。我们尚未讨论的一个功能是 `expression-parser` 属性。此属性允许你插入另一个支持其他类型表达式语言的解析器。

默认情况下，Web Flow 使用 SpEL 作为其表达式语言。根据你的背景或偏好，你可能希望将表达式语言切换为其他语言。

更改表达式解析器的另一个好理由可能是你正在从 Web Flow 1 迁移。在下一节中，你将看到有一个工具可以帮助你将 Web Flow 1 的流程迁移到 Web Flow 2 语法。但是，此迁移不会更改你的表达式。将 OGNL 设置为默认表达式语言可以帮助你逐步升级 Web Flow。

事实证明，切换表达式语言只需在 `expression-parser` 属性中配置正确的类即可（参见清单 12-7）。

***清单 12-7.** 更改表达式解析器*

`<webflow:flow-builder-services id="flowBuilderServices" view-factory-`
`creator="mvcViewFactoryCreator" conversion-service="conversionService" validator="validator"`
`development="true"` **`expression-parser=""`**`/>`

在清单 12-7 中，我们将 `expression-parser` 属性留空了。例如，指定以下类 `org.springframework.webflow.expression.el.WebFlowELExpressionParser` 将允许你使用统一表达式语言（EL），就像在 JSF 中使用的那样。如果你改用 `org.springframework.webflow.expression.WebFlowOgnlExpressionParser` 类，那么 OGNL 将被启用为表达式语言（这是 Web Flow 1 中的默认设置）。



### Web Flow 1 迁移

如果您没有使用过 Web Flow 1，或者您不参与 Web Flow 1 项目，那么本节可能对您不感兴趣。但是，我们想确保您了解迁移的可能性，以防您恰好是 Web Flow 1 的用户，或者您遇到了一个想要迁移的使用 Web Flow 1 的项目。即使它不适用于您，您也可以快速浏览本节，因为它非常简短，并且将来可能会对您有所帮助。

尽管 Web Flow 2 进行了大量重构，但从用户的角度来看，它在概念上仍然非常类似于 Web Flow 1（这是一件好事）。话虽如此，它确实增加了一些新功能，重构了一些古怪之处，并实现了一些内部优化。Web Flow 2 的流程定义语法也与 Web Flow 1 非常相似；但是，它*不*向后兼容。`<inline-flow>` 元素就是一个例子。这个特性在 Web Flow 2 中被直接移除了，并且没有替代品。幸运的是，Web Flow 2 确实为您提供了一个工具，可以转换您的 Web Flow 1 流程，以便它们可以在 Web Flow 2 中使用（该工具随 Web Flow 2 一起提供）。

假设您有清单 12-8 中所示的 Web Flow 1 流程，并且您希望将此流程迁移到不再支持 `<inline-flow>` 的 Web Flow 2。

***清单 12-8.** 将要转换为 Web Flow 2 流程的 Web Flow 1 流程*

`<?xml version="1.0" encoding="UTF-8"?>`
`<flow`

`      xsi:schemaLocation="`
`        http://www.springframework.org/schema/webflow`
`        http://www.springframework.org/schema/webflow/spring-webflow-1.0.xsd">`

`    <var name="someVar" scope="flow"/>`

`    <start-state idref="loadDetail"/>`

`    <action-state id="loadDetail"/>`

`    <inline-flow id="testInlineFlow">`
`        <flow/>`
`    </inline-flow>`
`</flow>`

要运行流程定义更新工具，您需要在其类路径中包含以下 jar 包：主要的 Web Flow jar 包，如果您手动下载，其名称为 `org.springframework.webflow-2.3.0.RELEASE.jar`；Spring 核心 jar 包，如果您手动下载，其名称为 `org.springframework.core-3.1.0.RELEASE.jar`；以及一个 XSLT 处理器。当运行 Java6 或更高版本时，JRE 默认附带的 XSLT 处理器就足够了，因此您不需要任何额外的 jar 包。如果您确实遇到任何问题，建议您下载 Saxon（家庭版）。在这种情况下，您只需将 jar 包（撰写本文时为 `saxon9he.jar`）放在与 Spring 核心和 Spring Web Flow jar 包相同的目录中即可。

如前所述，您将 Spring 核心和 Web Flow 依赖项与清单 12-8 中的流程放在一个目录中；在本例中，该目录是 `/tmp/convert`。如果您列出目录内容，它应该如下所示：

`/tmp/convert$ ls -l`
`org.springframework.core-3.1.1.RELEASE.jar`
`org.springframework.webflow-2.3.0.RELEASE.jar`
`web-flow-1-flow.xml`

接下来，启动转换器：

`java -cp *: org.springframework.webflow.upgrade.WebFlowUpgrader web-flow-1-flow.xml`

输出显示在控制台上，不会存储在文件中。如果需要，您可以使用控制台工具将输出*管道*到文件中。上述命令生成的输出是转换为 Web Flow 2 流程语法的等效流程定义（参见清单 12-9）。

***清单 12-9.** 转换后的 Web Flow 2 流程*

`<?xml version="1.0" encoding="UTF-8"?>`
`<flow xmlns="http://www.springframework.org/schema/webflow`
`      xmlns:xsi=http://www.w3.org/2001/XMLSchema-instance`
`      xsi:schemaLocation="`
`         http://www.springframework.org/schema/webflow`
`         http://www.springframework.org/schema/webflow/spring-webflow-2.0.xsd"`
`      start-state="loadDetail">`
`    <var name="someVar"/>`
`    <action-state id="loadDetail"/>`

`    <inline-flow>`
`    <!-- WARNING: inline-flow is no longer supported.  Create a new top level`
`         flow.`
`     -->`
`       <flow xsi:schemaLocation="http://www.springframework.org/schema/webflow`
`       http://www.springframework.org/schema/webflow/spring-webflow-2.0.xsd">`
`      </flow>`
`    </inline-flow>`
`</flow>`

在前面的代码片段中，您可以看到 `<start-state>` 元素已作为属性移动到顶级流程元素中。`<start-state>` 元素在 Web Flow 2 中不再作为单独的元素存在。此外，`<inline-flow>` 元素被提取为一个单独的流程，并附带一条警告，提示您应将其添加为顶级流程。

![Image](img/square.jpg) **注意**  虽然此工具将帮助您转换 Web Flow 1 流程，但请注意，关于作用域的定义（特别是 `flash` 作用域）在 Web Flow 2 中已更改。此外，转换还需要更改其他方面，例如流程执行监听器 API（这些更改将在后续章节中介绍）。从 Web Flow 1 迁移到 Web Flow 2 并不像仅仅运行此工具那么简单。您仍然需要检查流程和部分代码，以确定是否需要额外的修改。但是，此工具至少会让您走上正轨。最后，根据您拥有的 Web Flow 1 流程数量，并假设您的 Web Flow 1 流程使用 OGNL（可能性很大，因为这是默认设置），将 Web Flow 2 中的表达式语言也切换为 OGNL 可能是个好主意。这样，您至少可以免于更改流程中的每个表达式。

### 异常处理

在使用 Web Flow 时，有三种主要方式可以处理异常。我们已经（间接地）看到了第一种方式。在这种情况下，我们让应用程序控制器处理异常。在 `com.apress.prospringmvc.bookstore.web.controller.AuthenticationController` 的 `authenticate` 方法中，您可以看到，如果在向消息上下文添加消息后身份验证失败，我们会返回另一个事件类型（`error` 类型）（参见清单 12-10）。

***清单 12-10.** AuthenticationController 的 authenticate 方法*

`public Event authenticate(AuthenticationForm authenticationForm,`
`                          MvcExternalContext externalContext,`
`                          MessageContext messageContext) {`
`    try {`
`        authenticate(authenticationForm, ((HttpServletRequest)`
`        externalContext.getNativeRequest()).getSession());`
`    } catch (AuthenticationException authenticationException) {`
`        messageContext.addMessage(new`
`        MessageBuilder().error().code(LOGIN_FAILED_KEY).build());`
**`return new EventFactorySupport().error(this);`**
**`}`**
`}`

流程可以对此事件类型做出反应。在发生错误事件的情况下，流程将执行其他操作（例如，再次渲染同一个视图，以便显示错误消息），而不是像事件成功时那样继续执行。在应用程序控制器中捕获异常并使用特定事件来控制结果是处理异常的首选方式。在某种程度上，这可能是个人偏好问题；然而，当使用应用程序控制器时，这种方法使得异常处理也在方法本身内部执行（而不是委托给流程）更加明显。

但是，您还可以使用另外两种机制来处理异常，这两种机制都涉及在流程内部处理异常：

*   异常转换属性（on exception transition attribute）
*   通过 `<exception-handler>` 元素实现的自定义异常处理器



#### 关于异常转移

如果使用异常转移，可以将完全限定的异常类指定为属性的值。当该状态中的其他执行之一触发了该异常或其任何子类时，转移将自动触发。对于给定的转移，只能指定 `on` 或 `on-exception`，不能同时指定两者。例如，在身份验证流程中，可以像代码清单 12-11 那样添加一个异常转移。

***代码清单 12-11.** 带有异常转移的视图状态*

`<view-state id="authenticate" view="login" model="authenticationForm">`
`    <transition on="authenticate" to="authenticationOk">`
`        <evaluate expression="authenticationController.authenticate`![Image](img/U002.jpg)
`(flowScope.authenticationForm,` ![Image](img/U002.jpg)
`                              externalContext, messageContext)"/>`
`     </transition>`

`     <transition on="previous" to="previous"/>`

`     <transition on-exception="com.apress.prospringmvc.bookstore.`![Image](img/U002.jpg)
`                               service.AuthenticationException"`![Image](img/U002.jpg)
`                               to="authenticate"/>`
`</view-state>`

当在流程内部使用异常转移处理异常时，我们需要在 `AuthenticationController` 中重新抛出 `com.apress.prospringmvc.bookstore.AuthenticationException`，而不是捕获它并返回一个错误事件。在这种情况下，方法的返回值是 `void`。

如果没有发生异常，则应用程序控制器方法的执行会正常完成，且不返回结果值。Web Flow 会自动将其转换为一个成功事件，执行将照常继续。如果抛出了 `AuthenticationException` 错误，则会执行异常转移。在这种情况下，`to` 属性会转移到同一个视图状态并渲染同一个视图。这使我们能够显示由应用程序控制器的 `authenticate` 方法添加的错误消息。

#### 自定义异常处理器

可以使用 `<exception-handler bean="">` 元素将流程执行异常处理器附加到流程或状态上。当流程执行中发生异常时，Spring Web Flow 将首先尝试附加到流程当前状态的异常处理器。如果没有处理器处理该异常，则会尝试附加到流程本身的异常处理器。如果异常未被任何可用的异常处理器处理，它将被重新抛出。

该元素的 `bean` 属性标识了 Spring 应用程序上下文中可用的 `org.springframework.webflow.engine.FlowExecutionExceptionHandler` Bean（参见代码清单 12-12）。你可以将任意数量的异常处理器附加到流程或其任何状态上。

***代码清单 12-12.** FlowExcecutionExceptionHandler 接口*

`package org.springframework.webflow.engine;`

`import org.springframework.webflow.engine.support.`
`       TransitionExecutingFlowExecutionExceptionHandler;`
`import org.springframework.webflow.execution.FlowExecutionException;`

`public interface FlowExecutionExceptionHandler {`

`    public boolean canHandle(FlowExecutionException exception);`
`    public void handle(FlowExecutionException exception, RequestControlContext`![Image](img/U002.jpg)
`    context);`
`}`

第一个方法以 `org.springframework.webflow.execution.FlowExecutionException` 作为参数。Web Flow 将流程执行中抛出的异常包装在 `FlowExecutionException` 中。你可以通过调用其 `getCause` 方法来访问原始抛出的异常。实际类型也可能是更具体的 Web Flow 异常的不同子类之一（例如，`org.springframework.webflow.execution.ActionExecutionException`，它是 `FlowExecutionException` 的子类）。在这个 `canHandle` 方法中，你可以决定是否希望针对该异常类型调用异常处理器。如果 `canHandle` 方法对给定的异常类型返回 `true`，则会调用第二个方法 `handle` 让你处理该异常。然而，如果你查看 `handle` 方法的返回类型，你会发现处理异常并返回 `void` 并非易事，其中涉及更多复杂性。

异常转移只是将导航委托给 Web Flow（即，在发生特定异常时，导航到给定状态），而自定义异常处理器则需要在处理异常后自行驱动导航。

如果实现不正确，`FlowExecutionExceptionHandler` 可能会使流程执行处于无效状态，这可能导致流程执行无法使用或使其未来行为不确定。例如，你在 `flow` 级别定义了一个 `FlowExecutionExceptionHandler`。当在流程会话启动时（例如，在 `<on-start>` 元素中）抛出异常时，该异常会被路由到异常处理器。异常处理器必须在处理异常后将流程转换到其起始状态。处理器不应简单地返回——从而让流程没有设置当前状态——因为这将使流程执行处于不确定状态。这种额外的复杂性是你在决定使用 `FlowExecutionExceptionHandler` 而非异常转移属性时必须权衡的。前者让你对异常处理过程有更多控制，但也带来了更多复杂性。后者为你处理一切，但你对异常处理过程没有任何程序化控制。



![Image](img/square.jpg) **提示** 使用自定义异常处理器时，建议采用支持类 `org.springframework.webflow.engine.support.TransitionExecutingFlowExecutionExceptionHandler`。使用该支持类可以确保异常处理完成后导航按预期进行。此类可通过配置一个异常映射表来指定要处理的异常，以及处理异常后应执行的目标状态。默认情况下，该类还会将异常暴露在 `flash` 作用域中。你可以重写 `handle` 方法以实现自定义异常处理。在这种情况下，建议在方法末尾调用 `super.handle`，以便异常仍能暴露在 `flash` 作用域中，并执行到目标状态的转换。

你可以通过一个或多个映射来配置 `TransitionExecutingFlowExecutionExceptionHandler`，每个映射以异常类作为键，目标状态 ID 作为值。这两个值构成异常映射。每当处理器作用域内抛出异常时，`TransitionExecutingFlowExecutionExceptionHandler` 会通过比较所抛出异常的异常类与初始配置映射中的异常类，检查是否存在对应映射。如果存在映射，则会调用 `exposeException()` 方法，最终流程将转换到该映射指定的目标状态。

在查找异常时，`TransitionExecutingFlowExecutionExceptionHandler` 不仅会考虑 `FlowExecutionException`（或其任何子类），还会考虑其异常原因链。原因链中的异常也会匹配其超类的映射。例如，一个包装了 `java.io.FileNotFoundException` 的 `FlowExecutionException` 实例，会匹配 `java.io.IOException` 的映射，因为后者是 `FileNotFoundException` 的超类。

`TransitionExecutingFlowExecutionExceptionHandler` 可以像普通 Spring Bean 一样进行配置，如清单 12-13 所示。

***清单 12-13** 配置 `TransitionExecutingFlowExecutionExceptionHandler`*

`@Bean`
`public TransitionExecutingFlowExecutionExceptionHandler`![Image](img/U002.jpg)
`                                           ioFlowExecutionExceptionHandler() {`
`    TransitionExecutingFlowExecutionExceptionHandler`![Image](img/U002.jpg)
`    handler = new TransitionExecutingFlowExecutionExceptionHandler();`

`    handler.add(IOException.class, "targetState");`
`    return handler;`
`}`

然后可以将 `ioFlowExecutionExceptionHandler` 配置为 `<exception-handler>` 元素的 bean 属性：

`<exception-handler attribute="ioFlowExecutionExceptionHandler"/>`

我们已经看到，Web Flow 提供了三种处理异常的方式：

*   在应用程序控制器内部。通过捕获异常并向 `Messagecontext` 添加相应消息来实现。
*   在流程内部，通过指定转换的 on exception 属性来实现。
*   在流程内部，通过指定使用 `FlowExecutionExceptionHandler` 的 `<exception-handler>` 元素来实现。该元素可以在五个状态之一中以细粒度方式声明，也可以在整个流程中以粗粒度方式声明（在这种情况下，`<exception-handler>` 元素是 flow 元素的直接同级元素）。

问题在于：每种技术最适合在何时使用？当应用程序控制器与流程结合使用时，第一种方法可能最为自然。它让你能直接控制异常处理过程，并且与代码保持耦合。

然而，如果你直接从流程内部执行服务或外观（而不使用任何应用程序控制器），那么你只能通过流程功能来响应异常。在这种情况下，使用第二种方法（即使用 on exception 属性）将是处理应用程序异常的首选方式。

最后一种方法，即使用 `FlowExecutionExceptionHandler`，更适用于全局异常行为。例如，假设你想在每次出现异常时记录日志。你可以通过在 `flow` 级别声明一个 `FlowExecutionExceptionHandler` 来处理。



### 显式表单绑定

在第 11 章中，我们看到当指定模型时，随表单提交的请求参数会被绑定到该模型。当你指定模型时，这一过程会自动发生，并且你无需为特定转换显式关闭绑定。

绑定之后，如果模型包含验证方法或存在自定义验证器，模型就会得到验证，正如我们在前一章的验证部分所看到的那样。

有时，这种自动绑定可能过于粗粒度。如果你希望对绑定的请求参数有更多控制，可以指定一个绑定器，如代码清单 12-14 所示。例如，假设你的模型并非每个属性都在页面上使用。你可能有一些对应用程序来说是私有的属性，并且永远不会在视图上渲染（例如，用于操作模型的辅助方法）。由于 Web Flow 会自动尝试映射每个请求参数，用户可能会通过为这些属性提交值（通过操纵请求 URL 和参数）来滥用此功能。这种被利用的可能性很小，因为用户必须知道属性的名称，而且该属性名称在这些页面中从未使用过。但这仍然可能是你安全中的一个潜在漏洞。如果你的模型包含“应用程序私有”且绝不应通过请求参数填充的属性，那么你可以使用一个绑定器，该绑定器仅显式列出那些允许与传入请求参数绑定的属性。

***代码清单 12-14** 指定基于每个属性的绑定*

`<view-state id="authenticate" view="login" model="authenticationForm">`
`    <binder>`
`        <binding property="username" required="true"/>`
`        <binding property="password" required="true"/>`
`    </binder>`

`    <transition on="authenticate" to="authenticationOk" >`
`        <evaluate expression="authenticationController.authenticate(`![Image](img/U002.jpg)
`             flowScope.authenticationForm, externalContext, messageContext)"/>`
`</transition>`

`    <transition on="previous" to="previous" bind="false"/>`
`</view-state>`

在上述代码中，我们显式地将请求参数中的用户名和密码绑定到了模型。我们还将它们标记为必填项；当值缺失时，会添加一条错误消息。我们稍后将讨论如何本地化这些消息。

还有第三个属性：`converter`。你可以用它来指向一个命名的转换器。这适用于较旧的 Spring Web Flow 转换机制，但不应再使用。

在第 11 章中，我们解释过，我们是从 `org.springframework.`**`core`**`.convert.converter` 包创建转换器，而不是从 `org.springframework.`**`binding`**`.convert.converter` 包。Spring 核心转换服务不支持命名转换器。因此，使用命名转换器意味着你需要将其显式注册到 Web Flow 自定义转换服务中，但它不能在 Spring MVC 中直接（通过命名）使用。不鼓励这种工作方式。

推荐的方法是创建一个 `org.springframework.core.convert.converter`.`Converter`，然后将其注册为一个 Spring Bean，并通过 `org.springframework.format.support.FormattingConversionServiceFactoryBean` 为 Spring Web Flow 注册。这将导致自动选择正确的转换器。

最后，请注意我们在 `previous` 转换上指定了 `bind="false"`。`previous` 转换是通过表单中的普通提交按钮触发的。唯一的区别是事件 ID。如果没有显式禁用绑定，我们可能会要求必须填写用户名和密码才能返回。

![Image](img/square.jpg) **警告** 你可能还记得第 11 章中，示例应用程序中也有一个“上一步”按钮。在那个例子中，我们通过在模型验证方法中检查状态以编程方式处理了这个问题：

`if (context.getUserEvent().equals("next")) {`
`…`

在这种情况下，更好的解决方案是指定 `binding="false"`。如果你在流程中重命名了一个转换，模型中的代码就会出错。因此，对于简单情况，最好将流程绑定属性设置为 `false`。尽管如此，在某些需要根据状态有条件地执行特定验证的情况下，编程式解决方案可能更方便。

我们将用户名和密码都标记为必填项，但它们如何本地化为错误消息呢？为了让必填消息显示出来，我们必须声明消息键。Web Flow 遵循预定义的结构来尝试查找属于缺失字段的键：

*   `required`
*   `<`*`字段名`*`>.required`
*   `<`*`模型名`*`>.<`*`字段名`*`>.required`

在我们的消息包（`messages_xx.properties`）中，我们使用了第三种选项：

`authenticationForm.password.required=密码是必填项`
`authenticationForm.username.required=用户名是必填项`

另一种可能性是使用 `required` 和一个占位符。第一个占位符包含字段名：

`required= {0}: 是必填项`

最后，你需要在 JSP 中声明一个标准的 `<form:errors>`，并指定字段的路径，以便为不同字段显示错误：

`<form:errors cssClass="error" path="password"/>`
`<form:errors cssClass="error" path="password"/>`

![Image](img/square.jpg) **注意** 除了 `required` 错误类型之外，还有其他类型可以使用，例如 `typeMismatch`。当无法执行适当的类型转换时，会添加此类型的错误。你可以通过查看 `org.springframework.binding.mapping.MappingResult` 的实现来找到其他错误类型。该实现的 `getCode()` 方法返回错误类型。存在并可能引发的错误类型如下：`required`、`propertyNotFound`、`evaluationException` 和 `typeMismatch`。你可以按照刚才讨论的相同方式为这些错误定义消息。



### Web Flow AJAX 支持

Web Flow 在服务器端和客户端均支持 AJAX。服务器端通过 `<render>` 元素支持选择性局部渲染，可以在该元素中指定一个或多个需要渲染的片段。

在客户端，有一个可用的 JavaScript 库，它能自动解释局部响应并执行局部页面刷新。

JavaScript 部分基于 Dojo 框架，并被打包为 Spring JS（Spring JavaScript）。它作为 Web Flow 的一部分，但作为一个独立的构件进行打包。这意味着你必须在构建系统中添加一个单独的依赖才能获取此构件，因为它并不随主 Web Flow 依赖直接提供。在我们的示例项目 `build.gradle` 中，我们显式定义了此依赖（参见清单 12-15）。

***清单 12-15** 通过 Gradle 在 Spring JS 上定义额外依赖*

`compile "org.springframework.webflow:spring-js:$springSwfVersion"`

此依赖包含 AJAX `ViewResolver` 和 AJAX Tiles 视图。你还会将 JS 资源作为传递依赖获取。此依赖包含 JavaScript 依赖。约定是将它们放在 `META-INF/web-resources` 下（参见图 12-1）。

![图片](img/9781430241553_Fig12-01.jpg)

***图 12-1.** Spring JS 资源的目录结构，展示了一个中心 web-resources 文件夹，所有构件都打包在其中*

我们的示例应用程序也遵循相同的约定。这减少了查找资源的位置数量，并让我们只需在 Spring MVC 配置 `com.apress.prospringmvc.bookstore.web.config.WebMvcContextConfiguration` 中定义一个资源处理器即可：

`public void addResourceHandlers(ResourceHandlerRegistry registry) {`
`  registry.addResourceHandler("/resources/**/*")`![图片](img/U002.jpg)
`          .addResourceLocations("classpath:/META-INF/web-resources/");`
`}`

在我们可以使用 AJAX 功能之前，需要设置不同的元素。本节将总结这些元素，概述我们需要为示例应用程序支持 AJAX 局部渲染而处理的内容：

*   我们需要启用带有 AJAX Tiles 视图的特殊 AJAX `ViewResolver`。此 `ViewResolver` 内置了检测 AJAX 请求的特殊支持。
*   我们需要重构一个页面，以便通过 AJAX 调用重新渲染其一部分。我们还需要使用将要重新渲染的独立片段来调整 `tiles-config.xml`。
*   我们需要使用 `<render>` 元素调整流程，并指定要渲染的片段。
*   最后，我们需要在页面中使用 Spring JS 来发起 AJAX 请求，并让它局部刷新页面中与该片段匹配的部分。

#### 为 AJAX 配置 Web Flow

让我们首先在 `WebMvcContextConfiguration` 类中将标准的 `UrlBasedViewResolver` 替换为经过 AJAX 装饰的 `org.springframework.js.ajax.AjaxUrlBasedViewResolver`（参见清单 12-16）。`org.springframework.web.servlet.view.tiles2.TilesView` 也被替换为 `org.springframework.webflow.mvc.view.FlowAjaxTilesView`。

***清单 12-16** 在 WebMvcContextConfiguration 中配置 AjaxUrlBasedViewResolver 以支持 Ajax*

`@Bean`
`public ViewResolver tilesViewResolver() {`
`    UrlBasedViewResolver urlBasedViewResolver = new AjaxUrlBasedViewResolver();`
`    urlBasedViewResolver.setOrder(1);`
`    urlBasedViewResolver.setViewClass(FlowAjaxTilesView.class);`
`    return urlBasedViewResolver;`
`}`

在前面的示例中，我们简单地将现有的视图解析器替换为经过 Ajax 装饰的版本。当检测到非 AJAX 视图时，其行为与普通的基于 URL 的视图解析器相同。

#### 准备视图

现在我们已经为 AJAX 配置了 Web Flow，我们将通过使用 AJAX 调用局部重新渲染部分内容（而不是触发完整的页面刷新）来更改视图。在本节中，我们将了解需要做哪些修改来支持这一点。

我们还需要修改 Tiles 配置，但我们将单独处理，以保持内容分离并使其更易于理解。

我们将从 `selectBooks.jsp` 视图开始，其中有一个表单允许我们将书籍添加到订单中。一旦添加了书籍，它们会立即显示在所选书籍下方的表格中。这将是一个局部渲染的良好候选，如图 12-2 所示。我们向表格中添加了三本不同的书籍：*Effective Java*、*Refactoring: Improving the Design of Existing Code* 和 *Practical API Design: Confessions of a Java Framework Architect*。

![图片](img/9781430241553_Fig12-02.jpg)

***图 12-2.** 选择书籍*

我们要做的是从 `selectBooks.jsp` 视图中提取表格部分，并将其放入一个名为 `showSelectedBooks.jsp` 的独立视图中，该视图位于 `WEB-INF/view/createOrders/showSelectedBooks.jsp`（参见清单 12-17）。

***清单 12-17** showSelectedBooks.jsp 视图*

`<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core"%>`
`<%@ taglib prefix="spring" uri="http://www.springframework.org/tags"%>`
`<%@ taglib prefix="form" uri="http://www.springframework.org/tags/form"%>`
`<%@ taglib prefix="fn" uri="http://java.sun.com/jsp/jstl/functions"%>`

`<div id="selectedBooks" style="margin-top: 10px; margin-bottom: 10px;">`
`    <table style="width: 100%;" rules="groups">`
`        <thead>`
`            <tr>`
`                <th width="80%" align="left">`
`                    <spring:message code="label.page.books.book.name"/>`
`                </th>`
`                <th width="20%" align="left">`
`                    <spring:message code="label.page.books.book.quantity"/>`
`                </th>`
`            </tr>`
`        </thead>`
`        <tbody>`
`            <tr height="10px" />`
`                <c:forEach items="${orderForm.books}" var="book">`
`                    <tr>`
`                        <td>${book.key.title}</td>`
`                        <td>${book.value}</td>`
`                    </tr>`
`                </c:forEach>`
`            <tr height="20px" />`
`        </tbody>`
`    </table>`
`</div>`

![图片](img/square.jpg) **注意**  需要指出的是，我们显式地为顶层 `<div>` 元素命名了一个 `id` 属性。在我们的例子中，我们将其命名为 `selectedBooks`。在接下来的章节中，我们将看到它用于标识页面中应由传入的 AJAX 响应替换的部分。AJAX 响应将包含渲染后的局部 `showSelectedBooks.jsp`，如清单 12-17 所示。通过检查第一个元素，Spring JS 知道要替换实际页面上的哪段 HTML。在这种情况下，它将查找 `id` 为 `selectedBooks` 的 `<div>`，并将其替换为通过 AJAX 响应传入的版本。

接下来，在 `selectBooks.jsp` 视图中，我们将添加一个 Tiles 占位符，用于插入来自 `showSelectBooks.jsp` 的内容（参见清单 12-18）。到目前为止，我们仍然只是在操作 Tiles，并没有做任何与 Web Flow AJAX 相关的特殊操作。到目前为止，这个示例可以看作是一个普通的重构，我们将一部分内容提取到一个单独的视图中（例如，为了提高可读性、便于维护或重用）。

***清单 12-18** createOrders/selectBooks.jsp 中的 Tiles 属性*

`<h3>`
`    <spring:message code="label.page.books.selected.books"/>`
`</h3>`
`<tiles:insertAttribute name="selectedBooks"/>`



**现在** `<tiles:insertAttribute>` **所在的位置**，原本是我们已移至 `showSelectedBooks.jsp` 的 JSP 代码。在 Tiles 用实际内容替换此占位符之前，我们必须告知它这个新属性。

我们还显式地为表单指定了 `id` 属性 `selectBookForm`；稍后在应用 AJAX 时将会用到它。为方便起见，我们在清单 12-19 中提供了应用了这些修改后的 `WEB-INF/view/createOrders/selectBooks.jsp` 页面。

***清单 12-19：** 修改后的 selectBooks.jsp*

`<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core"%>`
`<%@ taglib prefix="spring" uri="http://www.springframework.org/tags" %>`
`<%@ taglib prefix="form" uri="http://www.springframework.org/tags/form" %>`
`<%@ taglib prefix="fn" uri="http://java.sun.com/jsp/jstl/functions" %>`
`<%@ taglib prefix="tiles" uri="http://tiles.apache.org/tags-tiles" %>`

`<form:form` **`id="selectBookForm"`** `modelAttribute="orderForm" action="${flowExecutionUrl}">`
`    <table style="width: 100%">`
`        <tr>`
`            <td colspan="2">`
`                <form:errors path="books" cssClass="error"/>`
`            </td>`
`        </tr>`
`        <tr style="height: 10px;"/>`
`            <tr>`
`                <td>`
`                    <spring:message code="label.page.books.select.book" />`
`                </td>`
`                <td>`
`                    <form:select path="book" items="${selectableBooks}"`
`                                 itemLabel="title" itemValue="id"/>`
`                </td>`
`            </tr>`
`            <tr>`
`                <td>`
`                    <spring:message code="label.page.books.select.quantity"/>`
`                </td>`
`                <td>`
`                    <form:input path="quantity" />`
`                    <span style="margin-left: 5px">`
`                        <form:errors path="quantity" cssClass="error"/>`
`                    </span>`
`                </td>`
`            </tr>`
`            <tr height="10px"/>`
`            <tr align="right">`
`            <td colspan="2">`
`                <button type="submit" id="add" name="_eventId_add">`
`                    <spring:message code="label.page.books.add.book"/>`
`                </button>`
`            </td>`
`        </tr>`
`    </table>`

`    <p/>`
`    <h3>`
`        <spring:message code="label.page.books.selected.books"/>`
`    </h3>`
**`<tiles:insertAttribute name="selectedBooks"/>`**

`    <div align="right" style="margin-bottom: 20px;" >`
`        <button type="submit" id="previous" name="_eventId_previous">`
`            <spring:message code="button.previous"/>`
`        </button>`
`        <button type="submit" id="previous" name="_eventId_cancel">`
`            <spring:message code="button.cancel"/>`
`        </button>`
`        <button type="submit" id="next" name="_eventId_next">`
`            <spring:message code="button.next"/>`
`        </button>`
`    </div>`
`</form:form>`

##### 配置 Tiles

对于 `selectBooks` 视图，我们仅使用了清单 12-20 所示的配置。

***清单 12-20** 当前 selectBooks.jsp 的 Tiles 配置*

`<definition name="selectBooks" extends="fullTemplate">`
`<put-attribute name="content"` ![Image](img/U002.jpg)
`           value="/WEB-INF/view/createOrders/selectBooks.jsp"/>`
`</definition>`

这段代码告诉 Tiles 应渲染完整模板，并用 `selectBooks.jsp` 视图替换 Tiles 占位符的内容。我们现在想要扩展此配置，因为此时 `selectBooks` 本身也是一个模板。

让我们首先创建一个新模板，将 `selectBooks` 标识为模板。此模板将被包含在原始的 selectBooks 定义中。为说明这一点，我们将为其指定一个*内部*名称：`show.selected.books`。如清单 12-21 所示，该定义以 `selectBooks` 视图作为模板，并指示它用新创建的视图 `/WEB-INF/view/createOrders/showSelectedBooks.jsp` 替换 `selectedBooks` Tiles 占位符。

***清单 12-21** 以 selectBooks.jsp 为模板的 showSelectedBooks.jsp 的新 Tiles 定义*

`<definition name="show.selected.books"`![Image](img/U002.jpg)
`template="/WEB-INF/view/createOrders/selectBooks.jsp">`
`    <put-attribute name="selectedBooks"`![Image](img/U002.jpg)
`     value="/WEB-INF/view/createOrders/showSelectedBooks.jsp"/>`
`</definition>`

最后，我们需要将此模板与主模板的 content 属性关联起来。这实际上与我们之前的原始配置相同；但不再指向实际的视图，而是指向我们刚刚创建的定义：`show.selected.books`（参见清单 12-22）。

***清单 12-22.** 使用先前定义的 show.selected.books 作为 content 的适配后的 selectBooks Tiles 定义*

`<definition name="selectBooks" extends="fullTemplate">`
`    <put-attribute name="content" value="show.selected.books"/>`
`</definition>`

图 12-3 展示了我们新的分层设计。到目前为止，我们一直使用主模板（`fullTemplate`），它直接用一个页面（例如 `WEB-INF/view/createOrders/selectBooks.jsp`）填充其内容。然而，在该页面中，我们也包含了一个 Tiles 占位符。通过我们刚刚创建的配置，我们还可以制作第二个模板来渲染 `selectBooks.jsp`，但将其 content 属性替换为 `showSelectedBooks.jsp` 页面。

![Image](img/9781430241553_Fig12-03.jpg)

***图 12-3.** 最终渲染的 selectBooks 页面，包含所有组合*

让我们总结一下到目前为止所做的工作：这两个 Tiles 定义共同构成了我们的新配置。如果渲染 selectBooks 视图，将使用模板 `fullTemplate`，并且 tiles 内容占位符将被替换为 `show.select.books`。后者引用另一个 Tiles 定义，该定义进而渲染 `/WEB-INF/view/createOrders/selectBooks.jsp`，并将 Tiles `selectedBooks` 占位符替换为 `/WEB-INF/view/createOrders/showSelectedBooks.jsp` 视图。

![Image](img/square.jpg) **注意** `show.selected.books` 定义是组合的一部分，我们永远不会直接渲染它，因为我们仍然需要将其包含在从 `fullTemplate` 扩展的定义中（在我们的例子中是 `selectBooks` 定义）。如果我们直接渲染 `show.selected.books`，我们会看到包含 `showSelectedBooks.jsp` 的 `selectBooks.jsp` 页面；但是，我们仍然会缺少页眉和页脚，因为它们尚未插入到主模板中。由于我们正在渲染的顶层定义仍然称为 `selectBooks`（我们只是更改了其内部组合），因此我们的控制器或流程中无需更改任何内容，视图名称也保持不变。顺便提一下，还有一种更短的表示法来定义这种组合，它使用内联形式。这将产生相同的结果，但在语法上更优雅一些。我们将在本章后面的“流程管理持久化上下文”部分重构 Tiles 配置时说明这一点。

有了这些代码，我们就可以测试重构后的应用程序了。它应该产生与我们将“显示已选书籍”结果表移动到不同视图之前完全相同的结果；换句话说，渲染的合并视图应与之前渲染的视图相同。



#### 调整流程

如前所述，我们必须明确指示哪些部分需要重新渲染。我们可以通过 `<render>` 元素来实现，该元素接受一个 `fragments` 属性（参见清单 12-23）。此属性将指向要渲染的 Tiles 属性。我们还可以通过逗号分隔来指定多个片段，并且允许使用表达式（这些表达式必须求值为 Tiles 属性名称，就像其字面等价物一样）。

如果我们以 `createOrders-flow` 为例，可以在 `add` 转换中添加 render 元素。render 元素仅对重新渲染同一视图的转换有意义，因此我们可以标记 Tiles 属性 `selectedBooks` 进行渲染。这意味着当触发部分渲染时，`<put-attribute name="selectedBooks" value="/WEB-INF/view/showSelectedBooks.jsp"/>` 将被重新渲染。

***清单 12-23** 在 createOders/createOrders-flow.xml 中渲染片段*

`<view-state id="selectBooks" view="selectBooks" model="orderForm" >`
`    <on-render>`
`        <evaluate expression="orderController.`![Image](img/U002.jpg)
`                  initializeSelectableBooks(orderForm)"`
`                  result="flashScope.selectableBooks" />`![Image](img/U002.jpg)
`    </on-render>`

`    <transition on="add" >`
`        <evaluate expression="orderController.addBook(flowScope.orderForm)" />`
**`        <render fragments="selectedBooks"/>`**
`    </transition>`

`    <transition on="next" to="selectDeliveryOptions" />`

`    <transition on="reset" to="selectBooks">`
`        <evaluate expression="orderForm.resetSelectedBooks()" />`
`    </transition>`

`    <transition on="previous" to="selectCategory" />`

`    <transition on="cancel" to="end" />`

`</view-state>`

#### 使用 Spring JS 和 JQuery 为视图添加 AJAX

最后，我们需要从浏览器实际发起一个异步调用。这样做将请求部分页面更新，从而更新 DOM 中的特定部分。不幸的是，由于不同浏览器对此功能的非标准支持，使用 XMLHttpRequest API（利用异步支持的浏览器 API）会有些问题。

幸运的是，我们拥有一些非常优秀的 JavaScript 框架，它们为我们应用开发者隐藏了所有这些繁琐的细节。我们将讨论两种方案。第一种使用 Spring JS，由于我们依赖它，因此已经具备。第二种依赖于 JQuery，我们需要为此下载一个单独的库。让我们首先演示如何使用 Spring JS 实现客户端 AJAX。

##### 使用 Spring JS

在此示例中，当用户按下“添加”按钮时，我们将使用 Spring JS 发起一个 AJAX 调用（参见清单 12-24）。“添加”按钮是一个普通的 HTML 提交按钮，它将保持不变。

***清单 12-24** 保持不变的提交按钮*

`<button type="submit" id="add" name="_eventId_add">`
`    <spring:message code="label.page.books.add.book"/>`
`</button>`

Spring JS 的一个核心概念是对现有 DOM 节点应用装饰。此技术用于渐进式增强网页，使其在禁用 JavaScript 或 JavaScript 本身有问题的浏览器中仍能正常工作。`addDecoration` 方法用于应用这些装饰，我们将使用此方法为已存在的、id 为 `add` 的提交按钮添加一个 Ajax 装饰。

为了能够使用 AJAX 库，我们必须在页面中包含对它们的引用。由于我们使用模板，这非常容易；只需将这些引用添加到模板的 HTML `head` 部分即可。在示例应用中打开 `template.jsp` 时可以看到这一点（参见清单 12-25）。

***清单 12-25:** 启用 Ajax 所需的额外 JavaScript 导入*

`<spring:url value="/resources/dojo/dojo.js" var="dojo"/>`
`<script type="text/javascript" src="${dojo}"></script>`
`<spring:url value="/resources/spring/Spring.js" var="springJs"/>`
`<script type="text/javascript" src="${springJs}"></script>`
`<spring:url value="/resources/spring/Spring-Dojo.js" var="springDojo"/>`
`<script type="text/javascript" src="${springDojo}"></script>`

![Image](img/square.jpg) **注意** 假定 script 元素是非空的。如果你使用（通常语法等价的）“/>”来闭合此类元素，脚本将不会被加载。这就是为什么它们被显式地用 `</script>` 闭合。这与 Spring JS 无关；而是 HTML 的一个特性。

接下来，我们必须用 AJAX 处理器装饰按钮。我们可以通过将清单 12-26 中的脚本添加到 `selectBooks` 视图来实现，该视图位于 `WEB-INF/view/createOders/selectBooks.jsp` 的底部。

***清单 12-26** JavaScript 装饰，将在添加按钮上启用 AJAX*

`<script type="text/javascript">`
`    dojo.addOnLoad(function() {`
`        Spring.addDecoration(new Spring.AjaxEventDecoration({`
`        elementId : "add",`
`        event : "onclick",`
`        formId: "selectBookForm"`
`        }));`
`    });`
`</script>`

关于上述清单，有几点关键事项值得注意：

*   `elementId` 是要应用装饰的 HTML 元素的 id。在我们的例子中，它是 id 为 `add` 的提交按钮。
*   event 是 HTML 元素的触发事件，用于调用装饰。在我们的例子中，它是添加按钮的 `onclick` 事件。
*   `formId` 必须与包含所有需要随 Ajax 请求一起提交的信息的表单匹配。我们在清单 12-19 中将此 id 添加到了 `form` 元素中。
*   `dojo.addOnLoad()` 方法将确保在所有元素加载完成后添加装饰。对于此类装饰，此方法可能不是必需的，但除非你有充分的理由不使用，否则将其视为最佳实践。顺便说一句，`<script>` 元素放置在哪里并不重要。我们选择将其放在底部，但它可以出现在文件中的任何位置。

该装饰将提交表单，导致所有输入被传输到服务器。由于 AJAX 装饰，这是以异步方式发生的。它将从按钮中获取名称作为要触发的事件 id（该 id 也会随参数一起传输）。该请求实际上是一个普通的表单提交。图 12-4 显示了如果你使用 Firebug 检查请求时将会看到的内容。



![Image](img/9781430241553_Fig12-04.jpg)

***图 12-4.** Firebug 捕获请求并显示请求参数*

在服务器端，`AjaxUrlBasedViewResolver` 将该请求识别为 AJAX 请求。当流程恢复时，它会根据请求参数中的事件 ID 发现我们想要执行 `add` 转换。它还会发现流程定义中指定了一个渲染元素。有了这些信息，它就有足够的能力继续选择应该渲染的视图部分。

![Image](img/square.jpg) **注意** 如果你仔细查看图 12-4，你会发现在 `Parameters` 列下有一个以粗体列出的 `ajaxSource` POST 参数（它是第二个参数）。这一点值得注意，因为我们的视图中并没有定义这样的组件。这个参数是由 Spring JS 添加的。它允许 Web Flow 将请求识别为 AJAX 请求。你可以在 `org.springframework.js.ajax.SpringJavascriptAjaxHandler` 中看到这一点：

`protected boolean isAjaxRequestInternal(HttpServletRequest request, HttpServletResponse` ![Image](img/U002.jpg)
`response) {`
`String acceptHeader = request.getHeader("Accept");`
`String ajaxParam = request.getParameter(AJAX_SOURCE_PARAM);`
`if (AJAX_ACCEPT_CONTENT_TYPE.equals(acceptHeader) || StringUtils.hasText(ajaxParam)) {`
`    return true;`
`} else {`
`    return false;`
`    }`
`}`

Web Flow 完成请求处理后，浏览器收到的响应是一个部分响应，其中仅包含来自 `showSelectBook` 视图的数据。我们可以使用 Firebug 来验证这一点（参见图 12-5）。

![Image](img/9781430241553_Fig12-05.jpg)

***图 12-5.** Firebug 捕获部分渲染的响应*

如你所见，结果仅包含整个页面的一部分；即，仅包含 `showSelectedBooks.jsp` 中定义的部分。接收部分视图更新的 JavaScript 现在会尝试匹配一个与更新中类型和 ID 相同的元素（这就是我们显式命名 `<div>` 元素 ID 为 `selectBooks` 的原因）。然后，它会用接收到的更新替换该内容。

就是这样！如果你现在点击“添加”按钮，你会注意到浏览器不再执行普通的表单提交或页面刷新。例如，在 Safari 中，你将不再看到浏览器状态栏中在点击添加按钮时弹出的提交表单进度条。现在，按下添加按钮时发生的视图更新将带来更流畅的体验，因为只有包含所选书籍的表格被更新，而不是整个页面。

##### 使用 JQuery

除了使用 Spring JS，客户端部分也可以轻松地被其他 JavaScript 库替代。例如，你可能更熟悉 JQuery（或 ProtoType 或其他库）。只要强制性的额外请求参数 `_eventId_xxx` 和 `ajaxSource` 存在，这些库将产生相同的效果。这些参数由 Spring JS 自动添加，但在使用其他 JavaScript 库时需要手动添加。

例如，如果你将清单 12-27 中的 Spring JS 脚本替换为 JQuery 脚本，你会发现这也能毫无问题地工作。要创建该脚本，我们需要下载最新版本的 JQuery（撰写本文时为版本 1.7.1）并将其添加到 HTML 的 `head` 部分。

***清单 12-27** 在 WEB-INF/templates/header.jsp 中额外导入 JQuery*

`<spring:url value="/resources/jquery/jquery-1.7.1.min.js" var="jquery" />`
`<script type="text/javascript" src="${jquery}"></script>`

接下来，我们可以在 `WEB-INF/view/createOrders/selectBooks.jsp` 中将 Spring JS 替换为等效的 JQuery，如清单 12-28 所示。

***清单 12-28** 用 JQuery 替换 Spring JS JavaScript*

`<script type="text/javascript">`

`    $(function () {`
`        var submit = $('#add');`

`            submit.click(function() {`
`                var form = $('#selectBookForm');`
`                var event = submit.attr('name');`
`                var data = form.serialize()+ '&amp;' + event + '=' + event +`![Image](img/U002.jpg)
`                           '&amp;ajaxSource=' + submit.attr('id');`

`                $.ajax({`
`                    type: "POST",`
`                    dataType: 'text',`
`                    url: form.attr( 'action' ),`
`                    data: data,`
`                    success : function(result) {`
`                        $('#selectedBooks').replaceWith(result);`
`                    }`
`                });`
`            return false;`
`        }):`
`    });`
`</script>`

如你所见，JQuery 替代方案需要更多代码。我们或许可以使其更简短一些，但 Spring JS 的优势在于它是专门为此任务优化的。然而，如果你习惯使用 JQuery，你可能会发现这是一个更好的选择。

该脚本手动构建每一步。我们首先为添加按钮（即用于添加新书的提交按钮）添加一个点击事件监听器。该事件监听器在所有内容加载完成后绑定。

你还可以看到，当我们在 `data` 变量中组合请求数据时，我们序列化表单以添加所有输入元素，并手动添加参数 `ajaxSource` 和 `_eventId_xxx`。

![Image](img/square.jpg) **注意** `$(#xxx)` 是一个 JQuery 选择器，它根据对象的 ID 在 DOM 中查找对象。`$(function () {})` 是 `$(document).ready(function(){})` 的简写形式。它与 `dojo.addOnLoad(function(){}.` 功能相同。

接下来，我们组装请求，并标识提交按钮和表单。我们序列化表单，并手动附加所需的参数。这些参数基于提交按钮的 name 和 id。提交的 URL 也取自表单。使用此 URL，我们现在可以执行 POST Ajax 调用，并且 URL 和参数与 Spring JS 发送的相同。作为响应，我们从服务器获取部分渲染的视图。剩下唯一要做的事情就是用 HTML 响应内容替换整个目标对象。

必须说明的是：使用 JQuery 时，你还应该注意处理服务器返回的错误。使用 Spring JS 时，你会自动获得错误检测支持：错误会很好地显示在弹出窗口中。此外，还可以直接在弹出窗口中显示渲染的内容。



### 流程执行监听器

在执行流程时，Web Flow 会在实际执行转换或调用应用程序控制器上的方法之前经历不同的步骤。例如，我们已经看到，在视图被渲染并发送回客户端后，流程执行会暂停。同样，当现有流程执行收到新请求时，需要恢复流程执行。暂停和恢复流程执行只是 Web Flow 将执行的步骤中的两个，但还有更多步骤。我们说这些步骤是 Web Flow *生命周期*的一部分，它由 Web Flow 在执行请求时所经历的（值得注意的）步骤组成。Web Flow 允许我们配置一个 `org.springframework.webflow.execution.FlowExecutionListener` 的实现，当任何这些特殊的生命周期步骤发生时，该实现将接收回调。

从这些生命周期步骤获取回调通常仅对更多与基础设施相关的代码（例如，横切关注点）有用。例如，您可以使用 `FlowExecutionListener` 来应用特殊的安全检查。或者，您可以使用它来记录访问日志。`FlowExecutionListener` 提供的功能在某种意义上与 Aspects 提供的功能非常相似，因为它对实际目标来说是透明的，无论这些回调是否发生。

`FlowExecutionListener` 绝不能替代 Aspects，后者仍然更强大。然而，Aspects 也更复杂。您应该评估流程执行监听器方法是否足以完成您想要做的工作。如果不够，您仍然可以回退到 AOP。

在接下来的部分中，我们将介绍如何编写我们自定义的 `FlowExecutionListener`。最后，我们将逐一介绍 `FlowExecutionListener` 中的所有方法，并对每个方法进行详细解释。

#### 编写流程执行监听器

编写流程执行监听器就像实现 `FlowExecutionListener` 接口一样简单。该接口定义了许多回调方法，这些方法将在流程执行生命周期过程中发生某些事情时被调用。Spring Web Flow 还提供了一个 `org.springframework.webflow.execution.FlowExecutionListenerAdapter` 作为便利类，它使用空方法体实现了 `FlowExecutionListener` 接口定义的所有方法。您也可以继承 `FlowExecutionListenerAdapter` 并仅覆盖您在特定时间需要的方法。

![图片](img/square.jpg) **注意** 流程执行监听器绝不使用 AOP 或任何字节码操作。它是内置于 Web Flow 子系统中的一个钩子，用于在流程执行期间为您提供某些事件的回调。这类似于 Web 容器回调已注册的 `HttpSessionListener` 以通知您会话创建或销毁。

流程执行监听器通过流程执行器声明（参见清单 12-29）。它们的顺序很重要；如果定义了多个流程执行监听器，它们的回调方法将按照监听器在 `<webflow:flow-execution-listeners>` 元素中定义的顺序被调用。定义在最上面的流程执行监听器首先被调用；紧随其后的监听器其次被调用；依此类推。

***清单 12-29** 配置流程执行监听器*

`<webflow:flow-executor id="flowExecutor" flow-registry="flowRegistry">`
**`    <webflow:flow-execution-listeners>`**
**`        <webflow:listener ref="loggerListener" />`**
**`        <webflow:listener ref="securityListener" />`**
**`    </webflow:flow-execution-listeners>`**
`</webflow:flow-executor>`

您还可以在 `<webflow:listener>` 上指定 `criteria` 属性，其值为逗号分隔的流程 ID 列表。这将使给定的监听器仅对指定的流程生效。如果将其留空，则该监听器对所有流程生效。这相当于指定一个星号 (*)，如果您不指定 `criteria` 属性，这就是它的默认值。流程 ID 和星号是该属性唯一允许的值（参见清单 12-30）。

***清单 12-30** 仅为特定流程 ID 配置流程执行监听器*

`<webflow:flow-executor id="flowExecutor" flow-registry="flowRegistry">`
**`    <webflow:flow-execution-listeners>`**
**`        <webflow:listener ref="loggerListener" criteria="myFlowId" />`**
**`        <webflow:listener ref="securityListener" />`**
**`    </webflow:flow-execution-listeners>`**
`</webflow:flow-executor>`



#### 流程执行监听器方法

`FlowExecutionListener` 接口定义了许多方法，可以在 Web Flow 执行（或即将执行）处理流程执行请求的不同步骤时通知您。例如，您可以在恢复或暂停执行、触发事件、执行操作等时获得回调。当回调方法过多且文档记录不佳时，它们会变得令人不悦。如果回调方法太少，您无法使用监听器捕获所需信息，使用起来也会令人不悦。在本小节中，您将找到每个回调方法的完整说明，以便您能够为特定任务做出正确选择。

我们不建议您尝试记住所有这些方法；最好快速浏览一遍，以便了解有哪些可能性。当您需要此类功能时，可以返回本节，验证哪个回调最符合您的需求。需要牢记的是：某些执行监听器方法非常相似，但也存在微妙的差异。您应始终确保进行充分测试，不要假设某个给定的监听器方法会按照您认为的方式工作。

让我们来看看 `FlowExecutionListener` 接口定义的方法。（请注意，为了更好的可读性，我们省略了返回类型，它始终是 `void`。）流程执行监听器方法不能返回任何内容；如果它要更改某些内容，应通过提供给相关回调方法的参数来实现。

**`requestSubmitted(RequestContext context)`**
**`requestProcessed(RequestContext context)`**

`requestSubmitted` 在请求进入流程执行的开头被调用，而 `requestProcessed` 在请求结束时被调用。`requestSubmitted` 是所有 `FlowExecutionListener` 回调方法中第一个被调用的方法，而 `requestProcessed` 是最后一个被调用的方法。如果您想在处理生命周期的早期执行某些对整个请求执行有影响的操作，可以使用这些回调。请注意，当启动一个新的流程执行时，`requestSubmitted` 回调会在流程执行实际启动*之前*被调用（此时还没有活跃的流程）。`requestProcessed` 也有类似的情况：当流程执行终止时（因为它到达了一个 `end` 状态），在调用 `requestProcessed` 时，流程已经终止。您可以通过使用 `context.getFlowExecutionContext().isActive()` 来测试流程执行是否处于活跃状态。

**`sessionCreating(RequestContext context, FlowDefinition definition)`**

`sessionCreating` 被调用来指示即将创建一个新的流程执行。可以通过从此方法中抛出任何类型的运行时异常来否决新流程执行的创建。

**`sessionStarting(RequestContext context, FlowSession session, MutableAttributeMap input)`**

`sessionStarting` 在新的流程执行创建之后、但在该新流程执行实际启动之前被调用。在启动子流程的情况下，流程执行尚未启动，因此父流程执行仍然处于活跃状态。`input` 参数是将要传递给衍生流程的输入映射，该映射由子流程状态的 `<input>` 映射准备或由外部提供。监听器也可以操作该输入。监听器可以通过抛出任何类型的运行时异常来否决新启动的流程执行会话的启动。

**`sessionStarted(RequestContext context, FlowSession session)`**

`sessionStarted` 在新的流程执行已初始化并开始处理之后被调用。启动行为已经执行（例如，流程的 `<on-start>` 或所执行的第一个状态的 `<on-entry>`）。如果您想在每次流程会话成功启动并初始化后执行某些操作，此回调非常有用。

**`eventSignaled(RequestContext context, Event event)`**

`eventSignaled` 在流程执行中每次发出事件信号时被调用。该事件可以是用户外部发出的事件，也可以是内部发出的事件（例如，由应用程序控制器发出）。此方法在实际进行转换之前被调用。

**`transitionExecuting(RequestContext context, TransitionDefinition transition)`**

`transitionExecuting` 在匹配到转换时、但在转换发生之前被调用。将此方法与 `eventSignaled` 进行比较是很有意义的；在这种情况下，转换的进展更进一步。在此阶段，您已经知道转换的源状态和（将成为的）目标状态，而在 `eventSignaled` 中，您只知道“某些事情”将要发生。

**`stateEntering(RequestContext context, StateDefinition state) throws EnterStateVetoException`**

`stateEntering` 指示流程执行即将进入一个给定的状态。此方法将在匹配到转换之后、但在进入该状态之前被调用（例如，此时尚未执行任何进入动作）。请注意，监听器可以通过抛出 `org.springframework.webflow.execution.EnterStateVetoException` 来否决进入该状态。

**`stateEntered(RequestContext context, StateDefinition previousState, StateDefinition state)`**

`stateEntered` 通知监听器流程已成功从一个状态转换到下一个状态。给定状态的所有进入逻辑现已执行完毕，并且该状态已完全初始化。

**`viewRendering(RequestContext context, View view, StateDefinition viewState)`**

`viewRendering` 在视图选择已经发生、并且信息已从触发的 `view` 状态中检索出来时被调用。这发生在任何渲染动作执行之前。

**`viewRendered(RequestContext context, View view, StateDefinition viewState);`**

`viewRendered` 在视图完全渲染完毕、并准备好发送回客户端之后被调用。

**`paused(RequestContext context)`**

`paused` 将在活跃的流程会话暂停、并且视图已渲染之后被调用。请注意，如果流程执行结束（即，它经过了一个 `end` 状态），则不会调用此方法。在这种情况下，将改为调用 `sessionEnding` 和 `sessionEnded`。

**`resuming(RequestContext context)`**

`resuming` 回调通知监听器一个暂停的流程执行已被恢复，并且即将继续执行。

**`sessionEnding(RequestContext context, FlowSession session, String outcome, MutableAttributeMap output)`**

`sessionEnding` 在流程到达一个 `end` 状态并即将结束流程执行时被调用。在此回调方法被调用时，流程执行仍然处于活跃状态。可能的 `<on-entry>` 动作（例如，那些出现在 `end` 状态上的动作）已经执行完毕。如果该流程是一个子流程，并且使用 `<output>` 映射输出了任何内容，则也可以通过 `output` 参数获取。这允许监听器检查或操作返回给父流程的输出。`outcome` 参数与结束会话所达到的结果相匹配；这将是终止的 `<end-state>` 的 id。

**`sessionEnded(RequestContext context, FlowSession session, String outcome, AttributeMap output)`**

`sessionEnded` 在 `sessionEnding` 之后、流程执行完全结束时被调用。当此方法被调用时，流程执行不再处于活跃状态。如果所讨论的流程是一个子流程，并且它将数据映射回父流程，那么此时不能再操作流程输出映射。此外，如果父流程已经重新激活，但尚未恢复，那么 `session.isRoot()` 将指示会话是为子流程结束的（在这种情况下，`isRoot` 将为 `false`）还是为父流程结束的（`isRoot` 将为 `true`）。

**`exceptionThrown(RequestContext context, FlowExecutionException exception)`**



在活跃流程执行期间处理请求时，若发生异常，将调用 `exceptionThrown` 回调方法。如果配置了流程异常处理器，它们将在该方法被调用后执行。

### 流程托管持久化上下文

通过本节，我们将介绍 Web Flow 的最后一个特性。该特性允许你将 JPA 持久化上下文扩展到表示层，并使其在整个流程执行期间保持持久化。

虽然从 Web Flow 的角度看，这是一个简单的特性，但要完全理解何时或为何使用它则稍显复杂。在本节中，我们将详细探讨它实际能为你做什么，并通过示例应用程序的部分内容来解释和说明其工作原理。

此特性并非纯粹的 Web Flow 或 Web 应用相关特性。我们在 Web Flow 章节中看到的所有其他内容都与我们在表示层如何操作有关。然而，此特性则始于后端的数据库访问层（DAL）。

正如你在第 1 章中所了解的，我们使用 ORM 来管理持久化。为了保持供应商中立，我们选择使用 JPA 作为 ORM API，并采用 Hibernate 作为 JPA 的提供者。通过这样做，我们也可以让应用程序与其他 JPA 提供者（例如 EclipseLink）一起运行。你可以在 Repository 中通过导入的包来区分这一点：我们使用 `javax.persistence` 包中的类，这是 Java API 中 Java 持久化 API 的一部分。

JPA 既可以在 JEE 托管上下文中使用，也可以在其外部使用。我们是在 JEE 托管上下文之外使用它，这在事务管理和其他配置方面需要一些手动干预。得益于 Spring，我们可以模拟一个托管环境，因此与在托管环境中使用 JPA 几乎没有区别。

![Image](img/square.jpg) **注意** 虽然我们在托管环境（Tomcat）中运行，但我们实际指的是一个完全托管/兼容的 JEE 应用服务器，无论是否包含 EJB 配置文件。例如，Tomcat 仅实现了 JEE 服务器的部分 Web 配置文件，从 JPA 的角度来看，它不能被视为托管环境。在 Tomcat 中，至少从 JPA 的角度来看，你的应用程序必须自行处理事务管理，并且 JPA 将像在普通 SE 环境中一样进行配置。

#### 从数据库到视图

首先，我们来解释一下到目前为止示例应用程序中数据访问的处理方式。如第 1 章所述，我们配置 Spring 使用 JPA，并使用 Hibernate 作为 ORM 实现。我们的应用程序遵循典型的简单三层架构：表示层、业务（逻辑）层和数据访问层。每当我们的应用程序控制器需要某些东西时，它们会与 Service 通信。Service 使用领域模型和 Repository 从关系数据库中检索数据。

从数据库检索到的对象称为*实体*。实体是领域模型的一部分，反映了由数据库支持的数据。这样的实体由 JPA 管理；更准确地说，是由持久化上下文管理。ORM 可能提供的特性之一是*懒加载*。此特性允许你仅检索少量数据，并在你请求时（自动）获取额外数据。例如，在检索 `Order` 时，我们可以懒加载 `Customer`。如果在处理过程中不需要 `Customer`，则不会加载任何 `Customer` 数据。我们加载的数据越少越好。

然而，此特性仅在实体由持久化上下文管理时才有效。通常，当事务结束时，持久化上下文会关闭。在事务之外，实体变为*游离态*。调用一个方法来加载在事务活动期间未加载的额外数据将导致抛出异常（对于 Hibernate，你会看到著名的 `org.hibernate.LazyInitializationException`）。

然而，在我们的示例应用程序中，我们一直使用 `org.springframework.orm.jpa.support.OpenEntityManagerInViewFilter`。这意味着我们的业务事务被提交（或回滚），但持久化上下文保持打开状态。这也意味着我们的实体保持托管状态，我们仍然可以让它们从表示层（以及从我们的视图中）加载数据。但是，我们应该小心不要在表示层执行更改或创建操作，因为在使用实体时不再有跨越的事务。每次交互都将在其自己的事务中运行；这与我们的业务逻辑层不同，在业务逻辑层中，整个 Service 方法调用发生在一个大事务中。

你可以在图 12-6 中看到这一点。我们模拟了一个源自表示层的方法调用，该方法调用进入业务逻辑层，并在途中启动了一个事务。在业务逻辑层中，我们被允许进行修改，因为我们所做的所有修改都包含在全局事务中。当结果返回给表示层时，事务结束，要么提交要么回滚。我们可以看到实体在所有层中都处于托管状态。然而，在业务逻辑层之外，不再有事务。尽管如此，得益于 `OpenEntityManagerInViewFilter`，在渲染视图阶段，持久化上下文会为读取操作保持打开状态。

![Image](img/9781430241553_Fig12-06.jpg)

***图 12-6.** 事务边界位于业务服务层*

虽然这效果很好，但问题在于，一旦视图被渲染，`OpenEntityManagerInViewFilter` 会在将视图返回给浏览器之前关闭持久化上下文。这意味着，如果你将一个实体放入（例如）`flow` 作用域，它将变为游离态。如果你需要在多个请求中使用该实体加载关联，这将成为一个问题，因为这会再次触发 `LazyInitializationException`。



为了更好地解释这一点，假设我们在用户屏幕上显示一个订单。为此，我们使用服务从数据库加载`Order`实体。`Order`实体存储在`flow`作用域中，并在视图渲染时被检索出来，以显示订单的实际内容。此外，我们可能还会显示用户账户的一些信息，这需要从视图内部执行额外的查询来检索`Account`实体。这一切都很好，因为所有操作都在同一个请求中发生——我们的持久化上下文仍然处于打开状态，实体也仍处于受管状态。视图渲染完成后，流程会话暂停，视图被发送到浏览器。但在视图被发送回之前，`OpenEntityManagerInViewFilter`已经关闭了持久化上下文。到目前为止，这没有问题，因为无论如何我们的视图已经渲染完毕。然而，假设订单视图允许用户点击某个订单以显示其详细信息（例如，订单中包含的产品）。在这种情况下，用户点击详情按钮后，会发起一个新请求，流程会话恢复。当视图尝试从`Order`实体（该实体从`flow`作用域中检索）获取`OrderDetail`实体时，将会收到一个`LazyInitializationException`异常。这很奇怪，因为当请求进来时，`OpenEntityManagerInViewFilter`已经打开了一个新的持久化上下文。虽然确实如此，但它不会重新关联那些在上一个请求结束时已变为游离状态的实体。

这基本上意味着，虽然持久化上下文被扩展到了表示层，但它无法跨越多个请求存活。一旦请求被处理完毕（并且视图已渲染完成），持久化上下文就会关闭。当实体存储在某个作用域中时，当下一个请求进来并尝试操作它们时，这些实体将处于游离状态。

#### 延长持久化上下文

Web Flow 允许你将持久化上下文的连续性延长到多个请求中。这被称为**流程管理持久化上下文（FMPC）**模式。它是我们已经在使用的**开放实体管理器视图模式（OEMIV）**的扩展（该模式由`OpenEntityManagerInViewFilter`实现）。正如我们在上一节中解释的，持久化上下文在请求进来时打开，但在视图渲染后销毁。在 OEMIV 模式中，持久化上下文不会跨多个请求保留；而在 FMPC 模式中，持久化上下文会被存储在流程会话中，供下一个请求使用。

这种模式的好处在于，我们可以在表示层中随意访问任何需要的数据，因为在流程的整个执行期间，我们都有一个活跃的持久化上下文。

使用 FMPC 模式时，我们也不再需要显式声明业务事务。在我们的流程中，只有一个地方需要事务，那就是最后一步：当我们实际下单时。在中间步骤中，我们只执行读取访问，这实际上不需要事务；或者最多需要一个只读事务（如果我们检索不同类型的实体，并希望保持一定程度的一致性）。

在图 12-7 中，我们可以看到 Web Flow 如何在启动或恢复流程会话之前，确保持久化上下文被绑定到`flow`作用域并从其中解绑。这将确保我们能够从表示层的流程不同步骤中访问数据库，即使是那些从`flow`作用域中检索到的实体。

![图片](img/9781430241553_Fig12-07.jpg)

***图 12-7.** Web Flow 自动绑定和解绑持久化上下文*

在这种方法中，持久化上下文在流程会话开始时打开，并保持打开状态直到会话结束。该模式应仅用于只读操作，并在流程结束时以单个创建/更新事务结束，我们稍后将进行说明。

当你进行需要数据的调用时（例如，集合或属性的延迟初始化），会生成一个单独的事务，并检索数据。这意味着你不需要在服务上添加任何`@Transactional`注解。如果需要读取一致性，你可以这样做；但建议将事务设为只读。你可能做出的任何修改都不会被刷新，直到流程结束时执行更新事务。



#### 应用流程托管持久化上下文

我们将重新设计 `createOrders-flow`，以利用托管持久化上下文。首先，在单个流程基础上启用持久化上下文延长功能。具体做法是在 `createOrders-flow` 流程的开头使用 `<persistence-context/>` 元素。

我们需要启用一个特殊的流程执行监听器（参见清单 12-31）。该监听器会在流程启动、结束、暂停或恢复时（至少针对我们启用了 `<persistence-context/>` 元素的每个流程）绑定和解绑持久化上下文。

***清单 12-31** 注册用于处理流程托管持久化上下文的流程执行监听器*

`<webflow:flow-executor id="flowExecutor" flow-registry="flowRegistry">`
`    <webflow:flow-execution-repository max-executions="3" max-execution-snapshots="10" />`
`        <webflow:flow-execution-listeners>`
**`            <webflow:listener ref="jpaFlowExecutionListener"/>`**
`        </webflow:flow-execution-listeners>`
`   </webflow:flow-executor>`

**`<bean id="jpaFlowExecutionListener"` ![Image](img/U002.jpg)**
**`           class="org.springframework.webflow.persistence.JpaFlowExecutionListener">`**
**`    <constructor-arg ref="entityManagerFactory" />`**
**`    <constructor-arg ref="transactionManager" />`**
**`</bean>`**

该监听器需要访问 `javax.persistence.EntityManagerFactory`（用于创建 `javax.persistence.EntityManager`，也称为持久化上下文）以及事务管理器。事务管理器用于在流程结束时创建事务。此时，实体管理器会与事务结合，以便你的修改能够以全部成功或全部失败的方式刷新到数据库。你可以在清单 12-32 中看到这一点，该清单取自 `JpaFlowExecutionListener`。

***清单 12-32** JpaFlowExecutionListener，在结束流程会话时将 EntityManager 与事务结合*

`public void sessionEnding(RequestContext context, FlowSession session,`
`                          String outcome, MutableAttributeMap output) {`![Image](img/U002.jpg)
`    if (isParentPersistenceContext(session)) {`
`        return;`
`    }`

`    if (isPersistenceContext(session.getDefinition())) {`
`        final EntityManager em = getEntityManager(session);`
`Boolean commitStatus =` ![Image](img/U002.jpg)
`            session.getState().getAttributes().getBoolean("commit");`

`        if (Boolean.TRUE.equals(commitStatus)) {`
`            transactionTemplate.execute(`![Image](img/U002.jpg)
`                new TransactionCallbackWithoutResult() {`
`                    protected void doInTransactionWithoutResult(`![Image](img/U002.jpg)
`                                                   TransactionStatus status) {`
`                        em.joinTransaction();`
`                }`
`            });`
`        }`
`     unbind(em);`
`     em.close();`
`    }`
`}`

该监听器还会检测子流程，因此当子流程结束时不会执行事务。创建/更新事务仅在顶层流程结束时执行。

接下来，我们需要指明哪个 `end` 状态会触发提交（参见清单 12-33）。

***清单 12-33** 触发提交的结束状态*

`<end-state id="endOrderOk"` ![Image](img/U002.jpg)
`view="redirect:/ordersOverview.html?orderOk=true&amp;orderId={orderId}"` **`commit="true"`**`/>`

还剩一件事要做。到目前为止，我们一直使用 `org.springframework.orm.jpa.support.OpenEntityManagerInViewFilter`，它是在我们的 `com.apress.prospringmvc.bookstore.web.config.BookstoreWebApplicationInitializer` 中配置的。`OpenEntityManagerInViewFilter` 和 `JpaFlowExecutionListener` 不能很好地协同工作。仔细想想，这很合理，因为它们都试图延长持久化上下文；但只有我们的 `JpaFlowExecutionListener` 会将其延长得更久一些。因此，我们需要禁用 `OpenEntityManagerInViewFilter`。在清单 12-34 中，你可以看到我们已将其注释掉，这样它就不会被加载。现在我们将完全依赖 `JpaFlowExecutionListener` 来延长持久化上下文。

***清单 12-34** 在 BookstoreWebApplicationInitializer 中禁用 OpenEntityManagerInViewFilter*

`@Override`
`public void onStartup(ServletContext servletContext) throws ServletException {`
`    registerListener(servletContext);`
`    registerDispatcherServlet(servletContext);`
`    // 我们改用 JpaFlowExecutionListener`
`    //registerOpenEntityManagerInViewFilter(servletContext);`
`}`

这就是我们需要做的全部工作。现在发生的情况是，在流程执行期间，我们的实体保持托管状态。当流程结束时，我们指示 Web Flow 进行提交。这意味着它将启动一个事务，并让 EntityManager 加入该事务，从而将修改操作（即创建、更新和删除）刷新到数据库。这基本上意味着我们服务上的 `createOrder` 方法不再需要 `@Transactional` 注解，因为事务现在由 Web Flow 管理。

![Image](img/square.jpg) **注意** 当在同一流程中使用断开连接的事务时（例如，流程的每一步执行多个读取事务，最后执行一个修改事务），建议配置乐观锁。在我们的案例中，这并不那么重要，因为我们是在插入新数据。然而，如果我们想要更新之前在流程的某个步骤中读取的数据（因此属于不同的事务），这就变得非常重要，因为当我们执行修改事务时，该数据可能已被更新。如果没有乐观锁，我们可能会在不知情的情况下覆盖其他用户的更改。



#### 重构订单概览页面

上一节展示了我们可以使用 Web Flow 来管理事务，并且它能在流程结束时创建一个提交事务，将所有更改推送至数据库。

最后，我们想说明的是，对于存储在作用域中的实体，其持久化上下文在视图中也是可用的。通常情况下这是不可能的，因为请求结束后持久化上下文就不再可用。

`订单概览`页面显示了客户所下的订单。我们将添加一个额外的弹出窗口来显示订单详情。在领域模型中，一个`订单`包含零个或多个`订单详情`的列表。目前，订单概览由 Spring MVC 控制器支持，不受流程管理。在清单 12-35 中，我们将开始为订单概览构建一个流程。你可以在 `WEB-INF/view/ordersOverview/ordersOverview-flow.xml` 中找到此代码。

![Image](img/square.jpg) **注意** 在清单 12-35 中，我们为一个并非真正“流程”的东西构建了流程；它只是一个带有详情页的概览页面。我们纯粹是为了示例应用程序而创建了一个流程，以便演示流程管理的持久化上下文。在实际应用中，将其建模为流程可能并不会带来好处。

***清单 12-35** 新创建的 ordersOverview-flow*

**`<persistence-context/>`**

`<view-state id="ordersOverview" view="ordersOverview">`
`    <on-render>`
`        <evaluate expression="orderController.retrieveOrders(`
`externalContext.sessionMap.authenticatedAccount)"` ![Image](img/U002.jpg)
`                  result="viewScope.orders" />`![Image](img/U002.jpg)
`    </on-render>`
`    <transition on="showOrderDetail" to="orderDetail">`
`        <set name="flashScope.order"`![Image](img/U002.jpg)
`             value="orders[requestParameters.index]">`
`        </set>`
`    </transition>`
`</view-state>`

`<view-state id="orderDetail" view="orderDetail" popup="true">`
`    <on-render>`
`        <render fragments="content"></render>`
`    </on-render>`
`</view-state>`

上述清单的以下几个方面值得注意：

*   我们添加了 `<persistence-context/>`，这将为此流程启用流程管理的持久化上下文（我们在 `createOrders-flow` 中也做了同样的事情）。
*   我们还使用 `<set>` 动作将选定的 `订单` 设置到 `flash` 作用域中。所选订单的索引从请求参数中获取：`orders[requestParameters.index]`。
*   第一个 `视图` 状态渲染 `ordersOverview` 视图，该视图渲染 `ordersOverview.jsp`。
*   第二个 `视图` 状态渲染 `orderDetail` 视图，但只会部分渲染它。由于 popup 属性设置为 `true`，这将与 Spring 的客户端 JavaScript 结合，在弹出窗口中渲染内容。

在清单 12-36 中，你可以看到 `orderDetail.jsp`，它只是一个完整页面的片段，因为它将作为弹出窗口渲染。

***清单 12-36** 将渲染订单详情的 orderDetail.jsp*

`<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core"%>`

`<h2>订单详情</h2>`
`<div style="width: 550px; height: 100px; margin-top: 15px;">`
`    <table style="width: 100%;">`
`        <thead>`
`            <tr>`
`                <th align="left">书名</th>`
`                <th align="left">书籍描述</th>`
`                <th width="60px" align="left">书籍价格</th>`
`            </tr>`
`        </thead>`
`        <tbody>`
`            <tr height="10px" />`
`            <c:forEach items="${order.orderDetails}" var="orderDetail" varStatus="status">`
`                <tr>`
`                    <td>${orderDetail.book.title}</td>`
`                    <td>${orderDetail.book.description}</td>`
`                    <td>${orderDetail.book.price}</td>`
`                </tr>`
`            </c:forEach>`
`            <tr height="20px" />`
`        </tbody>`
`    </table>`
`</div>`

此流程的 Tiles 配置很简单（参见清单 12-37）。

***清单 12-37** orderDetail.jsp 的 Tiles 定义*

`<definition name="orderDetail" extends="contentOnlyTemplate">`
`    <put-attribute name="content" value="/WEB-INF/view/ordersOverview/orderDetail.jsp"/>`
`</definition>`

在上面的清单中，我们继承了 `contentOnlyTemplate`，因为内容将在弹出窗口中渲染，所以我们不需要属于 `fullTemplate` 一部分的页眉和页脚。

在 `ordersOverview.jsp` 中，我们仍然需要添加链接，该链接将发起 Ajax 调用并打开弹出窗口（参见清单 12-38）。重要的是，此链接必须嵌套在表单中。我们还将通过隐藏字段提交索引（我们从 JSTL 提供的 `varStatus` 对象中获取）。这将使我们能够确定要查看详情的订单（正如我们在流程中看到的，它使用索引请求参数来确定列表中的选定 `订单` 对象）。

***清单 12-38** ordersOverview.jsp 中将触发订单的链接*

`<c:forEach items="${orders}" var="order" varStatus="status">`
`    <tr>`
`        <td>${order.id}</td>`
`        <td>${fn:length(order.orderDetails)}</td>`
`        <td><spring:eval expression="order.orderDate" /></td>`
`        <td>${order.totalOrderPrice}</td>`
`        <td>`
`<form:form id="orderDetailForm_${status.index}"` ![Image](img/U002.jpg) `action="${flowExecutionUrl}">`
`                <input type="hidden" name="index" value="${status.index}" />`
`<a name="_eventId_showOrderDetail"` ![Image](img/U002.jpg) `id="orderDetailLink_${status.index}" href="#">查看</a>`
`             </form:form>`
`        </td>`
`    </tr>`
`</c:forEach>`

最后，我们将使用 Ajax 装饰来装饰链接。因为我们需要链接和表单，并且每种都有多个版本，所以我们使用 dojo 来查询所有 id 以 `orderDetailLink_` 开头的链接。从中我们可以解析出索引。有了这些信息，我们还可以组合出要绑定的锚点 id 以及表单。我们还启用了弹出功能，这样 Spring JS 就知道在弹出窗口内渲染响应。所有这些都在清单 12-39 中完成。

![Image](img/square.jpg) **注意** 为了启用表单以弹出窗口形式显示，我们在渲染弹出内容的 `视图` 状态上启用了 popup 属性。

***清单 12-39** 将装饰按钮并触发 AJAX 请求以显示将在弹出窗口中显示的订单详情的 JavaScript*

`dojo.query("[id^='orderDetailLink_']").forEach(function(element) {`
`    var index = element.id.substr(element.id.lastIndexOf('_')+1,element.id.length);`
`    Spring.addDecoration(new Spring.AjaxEventDecoration({`
`        elementId : element.id,`
`        event : "onclick",`
`        formId : "orderDetailForm_"+index,`
`        popup : "true"`
`    }));`
`});`



完成最终修改后，我们可以运行应用程序并进入`Orders`概览页面。具体操作是点击导航栏中的“Orders overview”。图 12-8 展示了用户 ID 为`jd`的客户的订单。

![Image](img/9781430241553_Fig12-08.jpg)

***图 12-8.** 新的订单概览页面，显示了订单列表以及查看详情的链接*

在上述代码中，`Order`实体存储在`view`作用域中。`OrderDetails`页面（在详情视图中需要用到）的`Book`实体尚未初始化。最后，当用户点击`View`链接时，详情信息将在弹出窗口中打开（参见图 12-9）。

![Image](img/9781430241553_Fig12-09.jpg)

***图 12-9.** 点击查看链接会弹出一个窗口，显示订单详情*

Web Flow 将选中的`Order`实体从`view`作用域移到了`flash`作用域。接着，我们指示 Web Flow 渲染`orderDetail`的`view`状态。该`view`状态显示了`Book`实体的信息。在正常情况下，此时我们会收到一个`LazyInitializationException`，因为`Book`实体尚未加载。然而，得益于我们流管理的持久化上下文，这些实体仍然处于被管理状态，额外的数据得以加载。我们成功获取了详情，没有抛出任何异常。

为了证明这确实有效，我们切换回`OpenEntityManagerInViewFilter`并禁用了`JpaFlowExecutionListener`。我们还在`WebMvcContextConfiguration`和`WebflowContextConfiguration`中禁用了`CommonDataHandlerInterceptor`。`CommonDataHandlerInterceptor`负责加载随机书籍。如果启用，它会在我们的持久化上下文中静默加载书籍，这样我们就无法进行客观的测试。最后，我们还需要将`OrderDetail`实体中的`Book @ManyToOne`关联标记为懒加载：

`@ManyToOne(optional = false, fetch=FetchType.LAZY)`
`private Book book;`

这是必需的，因为 JPA（默认情况下）会立即加载多对一关联。如果我们不明确告诉 JPA 懒加载该关联，那么在加载概览页面时，`Book`实体就已经被加载了。概览页面间接加载了`OrderDetail`集合，因为它要显示订单中的书籍数量。这是通过调用`OrderDetails`集合的`size`方法实现的；因此，这个关联必须被初始化。这会隐式触发`Book`实体的加载，因为当`OrderDetail`关联被加载时，它会立即被获取。在将`Book`关联明确设置为懒加载后，我们可以点击`view`链接查看详情。如图 12-9 所示，我们反而得到了一个异常。

![Image](img/9781430241553_Fig12-10.jpg)

***图 12-10.** 如果不使用流管理的持久化上下文，加载书籍详情时会触发异常。*

如果我们继续向下滚动堆栈跟踪，会发现异常实际上是由`LazyInitializationException`引起的（参见图 12-11）。

![Image](img/9781430241553_Fig12-11.jpg)

***图 12-11.** 异常的原因是`LazyInitializationException`*

### 总结

至此，关于 Web Flow 的最后一章就结束了。我们现在已经涵盖了 Web Flow 提供的所有值得注意的特性，甚至还向您展示了如何使用它们。

在本章中，我们对前几章介绍的一些功能进行了更深入和高级的探讨。例如，我们展示了如何通过实现流组合和模块化的扩展来应用流继承。我们还展示了如何在流中执行显式表单绑定以及处理异常（而不是在应用程序控制器中处理它们）。

然后，我们更深入地探讨了执行 Web Flow 操作。例如，我们讨论了一些强大的新特性，如流执行监听器、使用 Web Flow 处理 Ajax 请求，以及最后提到的流管理的持久化上下文。

在这些 Web Flow 章节中，有一件重要的事情我们没有讨论：安全性。Web Flow 可以无缝地集成到 Spring Security 中。我们认为最好将安全性作为一个独立的章节来讨论。我们的下一章（也是最后一章）将介绍 Spring MVC 的 Spring Security（这将是我们讨论的起点）；在此过程中，我们将向您展示 Web Flow 如何融入这个安全设置。

## 第 13 章

## Spring Security

众所周知，安全性对任何应用程序都至关重要。对于暴露在互联网上的 Web 应用程序来说尤其如此。面对外部威胁是显而易见的，处理这些威胁将是您开发安全解决方案的主要工作。然而，开发人员面临的挑战不仅仅是外部威胁。

由于安全性不增加功能，它常常被忽视，有时甚至在时间和资源上难以证明其合理性。在紧迫的截止日期和满足所有功能需求的双重压力下，安全性往往被遗忘或实现得很糟糕。当然，从“功能性”的角度来看待安全性是错误的。

应该从编写测试或重构代码的相同角度来看待安全性。测试不会直接增加新功能的价值，但它确实能改进现有功能，并使得未来的功能添加或错误修复不会破坏项目的稳定性。重构也是如此。作为一名优秀的开发人员，您每天都在重构代码，无论改动多么微小，都是为了改进应用程序的性能和稳定性。安全性应该被视为更加重要。即使没有特别要求您考虑安全性，您也应该意识到应用程序面临的威胁，并确保您交付的产品尽可能安全。

另一方面，请不要忘记，整个“安全”过程并不完全掌握在您手中。安全性跨越多个层面。如果您在应用程序层面保护了您的应用程序，但基础设施团队却在未打任何补丁的、使用了四年的 Web 容器以及类似的操作系统上运行您的应用程序，那么无论您的应用程序多么安全，您都可能会遇到不速之客。对于组织来说，理解安全性的重要性以及影响安全性的所有因素至关重要。

从组织的角度来看，这不仅仅是一个技术问题。意识和认知也非常重要。即使存在最微小的可利用异常，客户或其他用户也可能认为应用程序不安全。根据您应用程序的可见性级别，发现此类异常（无论多么微不足道）都可能引发抗议风暴，并给组织带来负面宣传。这甚至可能比攻击者利用攻击本身造成的损害更严重！

在本章中，我们将向您展示如何履行作为应用程序开发人员的职责，并在应用程序层面使您的应用程序尽可能安全。我们将通过向您介绍 Spring Security 来实现这一点，这是一个最先进的、为 Spring 3.1 准备好的安全扩展。在本章中，我们将向您展示如何在几个小时内保护您的应用程序！我们将从一个基本的安全配置开始，该配置只需几行 XML 就能保护您的应用程序。接着，我们将展示如何使用数据库作为用户和凭据的存储。我们还将研究如何将安全性集成到 Spring Web Flow 中。最后，我们将讨论在 JSP 页面和应用程序控制器中使用 Spring MVC 执行授权检查的不同选项。



### 安全简介

在开始讨论 Spring Security 能为你做什么以及我们如何应用它来保护应用程序之前，我们首先需要对安全性做一些介绍。重要的是，我们首先要设定 Spring Security 将运行的环境。在本节的第一部分，我们将概述应用程序安全，这是我们即将处理的安全类型。接下来，我们将探讨一些通用的安全原则和术语，这些对于理解本章的其余内容至关重要。

最后，我们将对本节涵盖的主题进行更详细的总结，以便你确切了解后续内容。为了尽可能全面，我们将涉及 Spring Security 的不同方面。

#### 什么是应用程序安全？

从宏观角度来看，应用程序安全就是控制对应用程序功能的访问。例如，在我们的书店中，每个用户都应该能够查看我们正在销售的书籍。但并非每个用户都应该能够在未满足某些条件（例如创建账户并登录）的情况下创建订单。如果我们决定进一步扩展书店，增加一些类似 CMS 的功能，比如编辑或向系统添加新书，那么我们希望只有特定用户才能进行修改。通过应用程序级别的安全，你将能够指定应用程序中的哪些资源受到限制，哪些资源是公开可用的。对于受限资源，你还可以进一步区分；能够访问受限功能 A 的用户，可能不被允许访问受限功能 B。

根据应用程序的上下文，这套规则可能从非常复杂到不存在。在前一种情况下，你可能需要详细的角色、复杂的认证机制等；而在后一种情况下，你可能正在构建一个只有只读内容且对所有人公开的网站，那么应用程序安全可能就不是你关心的问题了。

![图片](img/square.jpg) **注意** 正如本章引言所述，安全跨越多个层面，而不仅仅是你的应用程序层。这就是为什么这里明确讨论的是“应用程序安全”。无论你的应用程序需要何种级别的应用程序安全，应用程序所处的环境都需要是安全的。你不应害怕向你的网络或基础设施团队询问他们正在采取哪些措施来确保应用程序所在环境的安全。你可能已经熟悉的一个表述很好地总结了这一点：“你的安全性取决于其最薄弱的环节。”

#### 通用安全原则

我们将从介绍一些通用安全原则开始，这些原则在你开始保护应用程序时将变得非常重要：

> *   **标识：** 根据特定身份（例如用户名、令牌或证书）来识别用户。
> *   **认证：** 验证所提供身份的过程。对于用户名而言，这就像检查提供的密码一样简单。
> *   **授权：** 确定经过认证的用户被允许使用哪些功能的过程。

如果你需要提供应用程序安全，首先必须考虑如何*标识*你的用户。标识将允许你识别想要访问受保护资源的用户。这是应用安全的第一步。标识可以很简单，比如一个用户名。它也可以是一个令牌，甚至是颁发给个人或组织的 x.509 证书。

接下来，你必须验证用户提供的身份。这个验证身份的过程称为*认证*：检查标识的真实性。

对于用户名，真实性可以通过要求输入密码并将输入内容与该用户存储的密码进行比较来验证。

最后，如果用户的身份通过认证，该用户就被允许访问应用程序的受限区域。此时，你可能希望对经过认证的用户进行区分。换句话说，你希望给予经过认证的用户某些特权。假设你有一个网络商店。在订购商品之前，用户必须经过认证，因为你希望知道谁下了哪个订单，你需要访问送货/联系地址，或者可能需要访问存储的支付凭证（如信用卡号）。然而，很明显，一个作为应用程序用户通过认证的普通客户不应该被允许添加或更改产品。允许或禁止经过认证的用户使用某些功能称为*授权*。

Spring Security 将特别关注认证和授权部分。同时请注意，认证方法在某种程度上与用户必须提供的标识类型相关。



#### 本章内容概述

Spring Security（原名 Acegi Security）已成为保护应用程序安全的重要组件。它通过提供身份验证和授权方案，尤其有助于保障应用安全。它能帮助您集成多种身份验证系统，例如简单的数据库、目录服务（提供 LDAP 接口，用于访问如 OpenLDAP 或 Active Directory 等）。同时，它也能帮助您管理授权。本章将介绍如何配置 Spring Security 来处理身份验证和授权两部分内容。

![图片](img/square.jpg) **注意** Spring Security 通常具有较低的侵入性。这使得我们可以在应用程序实现的后期阶段添加安全功能。虽然可能需要一些重构，但通常改动非常小。这也是我们将本章放在最后的原因之一。

我们将首先让您熟悉基本的安全配置。我们将配置 Spring Security，使进入应用程序的每个请求都经过安全配置。我们会看到，这种配置基于一种过滤器机制，请求在到达目标资源之前必须通过该机制。我们还将向您展示，定义哪些资源需要保护、哪些资源可以保持公开（例如登录页面、图片等资源）是多么简单。

在身份验证部分，我们将使用用户名和密码。我们将使用一个包含典型用户名和密码字段的登录表单。除了创建页面（包含登录表单）本身之外，我们还将看到，只需一些 XML 配置即可让 Spring Security 处理这一切。（相比之下，在前面的章节中，我们自行编写了身份验证机制。）为简单起见，此处的身份验证将基于基本的内存数据存储。稍后我们将看到如何轻松地将其更改为数据库存储。

在使用 Web Flow 的应用程序中，大量的应用控制器逻辑和视图选择很可能已迁移到流程中。Web Flow 支持直接保护流程。我们将了解如何将 Web Flow 集成到安全机制中，从而使您的应用程序从上到下都安全。

配置完身份验证后，我们将了解如何进一步保护登录和订单处理过程。在此之前，所有数据都通过纯 HTTP 传输。我们将看到，添加传输安全只需进行配置即可。

接下来，我们还将讨论本地化。Spring Security 支持异常消息的本地化，默认情况下这些消息是英文的。我们将了解需要配置哪些内容以支持其他语言环境。

我们还将通过实现基于角色的访问模型来扩展安全性。这将为您的用户授予权限，您之后可以在需要访问特定资源时使用这些权限来检查授权。

Spring Security 的授权可以通过编程方式（通过代码）或声明方式应用。声明式方法允许您使用注解、表达式或 XML 形式的元数据来断言授权。我们将介绍如何在以下元素中应用 Spring Security 的授权：

> *   视图：能够显示哪些组件对给定用户可见
> *   控制器
> *   Web Flow 流程
> *   服务层

我们将涵盖声明式安全（使用注解）和编程式安全，并详细展示如何在视图、应用控制器和流程中应用这两种方式。最后，我们将介绍如何在 JSP 页面中使用 Spring Security 标签库。

![图片](img/square.jpg) **注意** 供您参考，Spring Security 也可以以相同的声明方式应用于后端（主要是服务层），就像应用于应用控制器一样。不过，本章不会涉及后端中的 Spring Security。


### 准备示例应用程序

我们将复用示例中最新、最完善的版本，该版本来自第 12 章。此应用程序包含 Spring MVC 功能、流程、应用控制器以及服务层，是应用安全机制的理想候选。

在当前版本中，我们已经应用了一套自行设计的安全机制。用户无需任何限制即可进入网站浏览图书，也可以不受限制地将图书添加到购物车。然而，未经身份验证的用户无法完成最终结账，也无法查看自己的订单。用户必须先通过用户名和密码登录，才能执行这些操作。

为此，我们提供了一个登录表单，用于获取凭据并将其与数据库中存储的信息进行比对。一旦用户通过身份验证，我们会在用户的会话中放置一个令牌（即`com.apress.prospringmvc.bookstore.domain.Account`领域对象），以表明身份验证成功。（如需回顾，可参见第 11 章中“将身份验证实现为子流程”一节；清单 11-39 演示了通过 Spring MVC 进行身份验证，清单 11-41 则展示了通过 Web Flow 进行身份验证。）正如您将看到的，我们可以完全通过配置 Spring Security 来实现这一切，而无需自行构建安全机制。

尽管我们自行设计的方案在当时避免了一些额外的复杂性，并且表面上似乎也能正常工作，但仔细审视后，仍会发现一些严重缺陷。

首先，控制器或流程上并未应用任何安全措施。例如，让我们回到之前的示例（第 12 章），其中我们仍在使用自制的安全实现。在未通过身份验证的情况下，尝试直接在浏览器中输入以下 URL 来启动订单概览流程，以打开图 13-1 所示的页面：

[`http://localhost:8080/chapter12-bookstore/ordersOverview`](http://localhost:8080/chapter12-bookstore/ordersOverview)

![图片](img/9781430241553_Fig13-01.jpg)

***图 13-1.** 直接启动流程可绕过安全机制。*

您可以看到我们并未登录，因为导航栏中没有显示“订单概览”。因此，即使未认证用户无法访问此链接，他们只需在浏览器地址栏中复制/粘贴流程名称，页面便会加载。这无疑是安全漏洞……

在这个特定案例中，并未造成实际损害，甚至没有抛出任何异常。我们很幸运；唯一发生的事情是显示了一个空订单页面。服务层/仓库层会尝试从账户“null”加载订单，结果返回一个空列表，因此什么也不会显示。然而，情况本可能更糟。如果我们在开发仓库层时不够谨慎，当用户名为 null 时可能忽略了“用户”这一筛选条件；那样的话，结果将是检索*所有*客户的*所有*订单！

显然，这是一个严重的安全隐患，因此我们必须确保我们的安全机制是真正的安全，不能再通过简单地在浏览器地址栏中输入正确的 URL 来绕过。

其次，也无法对已认证用户进行区分，因此无法限制访问权限。一旦用户通过身份验证，就能访问所有受限制的功能，这种方法在大多数情况下过于粗粒度（大多数应用程序都支持不同级别的授权）。

我们提出这一点，只是为了向您说明，自行开发安全机制绝非易事。幸运的是，Spring Security 将为我们解决这些问题。

在首次尝试中，我们将向您介绍 Spring Security 的基础知识，并展示设置一个基本安全方案是多么简单，而且该方案看起来已经非常专业。它将实现以下功能：

> *   确保无论用户尝试何种操作，只要未通过身份验证，就无法访问任何受限制的功能。
>     *   因此，简单的 URL 技巧将不再有效。
>     *   即使是通过身份验证的用户，也无法访问未定义明确访问规则的页面或流程。
> *   使用基于表单的身份验证，向用户显示一个登录页面，用户可通过用户名和密码字段输入凭据。
> *   当用户尝试直接访问受保护资源时，自动将其重定向到登录页面，并在身份验证成功后将其重定向回该资源。
> *   使用 Spring Security 处理身份验证部分，而无需自行编写此类代码。

在逐步为应用程序添加安全功能的过程中，我们将解释 Spring Security 的部分架构和内部原理。随后，在接下来的章节中，我们将扩展这一基本方案，进一步讨论授权以及如何在应用程序中应用它。我们将通过添加分类和图书管理功能来扩展应用程序，这些功能仅对部分已认证用户开放。

### 保护我们的书店

为了演示基本安全方案，我们将将其应用于示例应用程序。在本节中，我们将向您展示如何：

> *   将 Spring Security 配置为应用程序的巨型包装器，使所有传入请求都经过 Spring Security 处理
> *   配置我们的登录页面
> *   将某些资源标记为仅限已认证用户访问
> *   启用自动重定向功能：当未认证用户访问受保护资源时，自动跳转至登录页面，并在认证成功后重定向回原资源
> *   使用 Spring Security 自带的用户内存数据存储
> *   使用加盐哈希安全存储密码



#### 添加正确的依赖项

在使用 Spring Security 之前，您必须声明正确的依赖项集合。与 Web Flow 或 Spring MVC 类似，Spring Security 是一个模块，您必须为其声明特定的依赖项才能使用它。方便的是，Spring Security 本身又进一步划分为多个子模块。这样做的好处是，您的项目可以只依赖它实际需要的模块；不多不少。这有助于保持依赖树整洁，因为不会将未使用的模块（这些模块本身也有依赖项）拉入您的项目。我们将要使用的模块（以及您可能始终用于保护 Web 应用程序的模块）列在表 13-1 中。

![Image](img/9781430241553_Tab13-01.jpg)

在示例项目中，您会发现这些模块已转换为 `build.gradle` 中的依赖项条目（请参见清单 13-1）。

***清单 13-1.** Gradle 中的依赖项*

`compile "org.springframework.security:spring-security-core:$springSecurityVersion"`
`compile "org.springframework.security:spring-security-web:$springSecurityVersion"`
`compile "org.springframework.security:spring-security-config:$springSecurityVersion"`
`compile "org.springframework.security:spring-security-taglibs:$springSecurityVersion"`

由于 Spring Security 是一个核心依赖项，我们使用了一个变量 `$springSecurityVersion` 来表示其版本。该变量的值可以在根 Gradle 文件中找到。（在撰写本文时，其版本为 3.1.0.RELEASE。）

![Image](img/square.jpg) **注意** 您已经看到 Spring Security 被划分为多个模块。如清单 13-1 所示，我们所依赖的模块只是其中的一部分，因为我们不会使用 Spring Security 提供的所有功能。如果您需要其他功能（例如 LDAP），则必须将其作为额外模块包含在依赖项列表中。这些模块的列表可以在 Spring Security 参考文档的依赖项部分找到^(1)。在这里，您将看到每个模块及其自身的依赖项一起列出。模块自身的依赖项仅作为参考信息；从您向某个 Spring Security 模块添加依赖项的那一刻起，它们就会被您的构建工具/依赖管理系统自动拉取。

__________

¹ [`http://static.springsource.org/spring-security/site/docs/3.1.x/reference/appendixdependencies.html`](http://static.springsource.org/spring-security/site/docs/3.1.x/reference/appendixdependencies.html)

#### 启用 Spring Security

在保护任何内容之前，我们需要启用 Spring Security。其核心概念是，它将捕获进入我们系统的每个请求，并决定该请求是否可以通过。支持此机制的起点是一个标准的 JEE Servlet 过滤器。

Spring Security 使用 Spring Core 的 `org.springframework.web.filter.DelegatingFilterProxy`（它是标准 `javax.servlet.Filter` 的一个实现），该过滤器将被配置为拦截每个请求。此过滤器将作为一个钩子，将捕获到的请求委托给 Spring Security。过滤器的配置方式与 Spring MVC 的 Dispatcher Servlet 相同，都在我们的 `com.apress.prospringmvc.bookstore.web.BookstoreWebApplicationInitializer` 中进行。我们可以利用 JEE 动态 Servlet API 来添加此过滤器，如清单 13-2 所示。

***清单 13-2.** 在 BookstoreWebApplicationInitializer 中注册 Spring Security 过滤器*

`private void registerSpringSecurityFilterChain(ServletContext servletContext){`
`FilterRegistration.Dynamic springSecurityFilterChain =` ![Image](img/U002.jpg)
`servletContext.addFilter(BeanIds.SPRING_SECURITY_FILTER_CHAIN, new` ![Image](img/U002.jpg)
`      DelegatingFilterProxy());`
`    springSecurityFilterChain.addMappingForUrlPatterns(null, false, "/*");`
`}`

`addFilter` 的第一个参数是我们将赋予过滤器的名称。在这种情况下，我们添加的过滤器名称为 `springSecurityFilterChain`。我们并非随意选择名称，实际上引用了一个包含这些预定义名称作为常量的 Spring 类（因此常量 `org.springframework.security.config.BeanIds.SPRING_SECURITY_FILTER_CHAIN` 的值为 `springSecurityFilterChain`*)*。稍后当我们介绍 Spring 端时，将解释为什么此过滤器的名称很重要。第二个参数是过滤器实例本身。

另请注意，过滤器 `DelegatingFilterProxy` 是一个“全捕获”过滤器。查看过滤器模式“/*”，它将捕获任何请求（请参见图 13-2）！因此，没有任何请求可以在不被此过滤器捕获的情况下进入我们的应用程序。

![Image](img/9781430241553_Fig13-02.jpg)

***图 13-2.** 过滤器捕获所有请求。*

`addMappingForUrlPatterns` 的第一个参数（dispatcherTypes）指示何时应调用过滤器。默认值（当提供 null 时）是对所有来自 Servlet 容器“外部”的请求调用它。您也可以指定，例如，当通过内部 include 或 forward 语句访问资源时，应调用过滤器。在这种情况下，您必须提供一个 `java.util.EnumSet`，其中包含一个或多个 `javax.servlet.DispatcherType` 类型的枚举。将此值保留为 null 就足够了，因为您的资源受到外部保护，并且即使在执行 include 或 forward 时，您也会使用安全标签（我们稍后将介绍）检查页面上的授权。然而，在某些情况下，在执行此类操作时再次经过安全过滤器链可能是可取的。



`addMappingForUrlPatterns` 的第二个参数（即 `isMatchAfter`）用于声明当多个过滤器匹配同一 URL 模式（或同一 Servlet）时的执行顺序；其值可为 `true` 或 `false`。在这种情况下，过滤器通常按照它们在 `web.xml` 中出现的顺序执行，或者在使用 API 时（如我们当前的做法）按照它们在代码中添加的顺序执行。如果你为 `isMatchAfter` 提供 `true` 值，该过滤器将在匹配该 URL 模式（或 Servlet）的过滤器链中最后被调用。如果多个过滤器匹配同一 URL 模式（或 Servlet）且均将此标志设为 `true`，则按照它们定义的顺序执行（但它们仍会在所有其他过滤器之后匹配）。若此标志设为 `false`，则情况相反，该过滤器将在映射到同一 URL（或 Servlet）的其他过滤器之前被调用。由于我们控制着所有注册的过滤器，只需更改注册顺序即可自行调整顺序。因此无需考虑此属性，我们为所有其他过滤器注册选择一个统一的值（本例中为 false）。在这种情况下，过滤器的定义顺序将决定它们的调用顺序。

![图片](img/square.jpg) **注意** Tomcat（撰写本文时为 7.0.26 版本）似乎存在一个问题，该标志的解释方式与预期相反。因此，如果你将 `Filter` 的 `isMatchAfter` 设为 true，它将在同一 URL 模式（或 Servlet）的所有其他过滤器之前匹配。我们已就此问题在 Apache Tomcat 开发邮件列表^(2)上发布了消息。

现在我们已经了解了所有请求如何被过滤器捕获，接下来将看到请求如何进入 Spring Security 机制。正如配置的过滤器类名（**`Delegating`**`FilterProxy`）所示，它显然会将传入的请求委托到某处。这个“某处”正是 Spring Security 的核心，并在我们的 Spring 配置中进行配置。

为此我们创建了一个独立的 Spring 配置文件，名为 `spring-security.xml`，位于 `src/main/resources/spring/` 目录下。该配置片段将配置“Spring 主安全配置”，如图 13-2 所示。

为此，我们首先需要配置 Spring Security 命名空间，该命名空间将包含设置主配置所需的所有构件（见清单 13-3）。

***清单 13-3.** Spring 安全配置文件的 XML 头部*

`<?xml version="1.0" encoding="UTF-8"?>`
`<beans`


好的，作为一名高级文档工程师和翻译员，我将严格遵循您提供的注意事项和示例，将给定的英文文本翻译成中文。


`` `       xsi:schemaLocation="` `         http://www.springframework.org/schema/beans` `         http://www.springframework.org/schema/beans/spring-beans-3.1.xsd` **`http://www.springframework.org/schema/security`** **`         http://www.springframework.org/schema/security/spring-security-`**![Image](img/U002.jpg) **`         3.1.xsd"`**`>` 每个安全特性都将使用命名空间前缀“security”。    ![Image](img/square.jpg) **注意** 你也可以将 Spring Security 命名空间设为默认命名空间。这里我们选择将核心 Spring beans 功能的命名空间设为默认，这也是我们所有其他 Spring 配置文件的默认设置。不过，在这种情况下，交换它们也是可行的，因为这是一个专门用于 Spring Security 的配置文件，你将使用更多的 Spring Security 元素，而不是 bean 命名空间的元素。在这种情况下，你可以去掉“security”前缀，因为所有安全元素都可以在默认命名空间中访问，无需任何前缀。当安全配置很多时，这可以使你的 XML 看起来更简洁。    __________    ² [`http://markmail.org/message/5q7gpipxdiqxsu6p?q=+list:org.apache.tomcat.dev`](http://markmail.org/message/5q7gpipxdiqxsu6p?q=+list:org.apache.tomcat.dev)    接下来，我们需要定义 `DelegatingFilterProxy` 将委托给的“delegate”。这通过 `<security:http>` 元素完成，如清单 13-4 所示。    ***清单 13-4:** Spring Security 命名空间过滤器链配置*  `<security:http>` `     <security:form-login login-page="/public/authentication/login.htm" default-target-`![Image](img/U002.jpg) `url="/public/main.htm"/>` `</security:http>`  这个元素的作用是创建一个过滤器链，并将其以默认的 bean ID `springSecurityFilterChain` 注册到应用上下文中。`DelegatingFilterProxy` 将使用过滤器的名称来查找这个 bean，并将控制权委托给它。如果你将过滤器命名为 `someFilter`，它将查找 bean ID 为 `someFilter` 的过滤器链，这将导致找不到 bean。在继续之前，我们需要解释一下 Spring Security 过滤器链是什么以及它将为我们做什么。    过滤器链是捕获请求和处理待办事项的核心组件。Spring Security 主配置中的几乎所有内容都由过滤器链中的一个过滤器处理。你可以随意插入或移除过滤器，每个过滤器都有特定的工作（参见图 13-3）。  ![Image](img/9781430241553_Fig13-03.jpg)  ***图 13-3.** 过滤器委托给已配置的应用过滤器链*    在清单 13-4 中，我们隐式配置了一个元素，如果用户在未认证的情况下访问受保护的资源，该元素会将用户发送到 `login.htm`（由 `login-page` 属性指定）页面。当用户登录时，过滤器会自动将用户重定向回请求的受保护资源。或者，如果用户之前没有请求过安全页面，而是直接通过登录页面登录，则用户可能会被重定向到 `main.htm`，该页面会渲染主页（由 `default-target-url` 属性定义）。在这种情况下，我们还会发送一个参数，指示认证成功。相关的主页面将检测到这一点，并显示一条消息，表明认证过程成功。这个元素会导致一个过滤器被添加到过滤器链中，以处理此问题。    虽然你可以插入自己的自定义过滤器，但许多现有的过滤器已经可供你使用。讨论所有这些过滤器超出了本书的范围，但你可以在 Spring Security 参考手册^(3) 中找到关于它们的更多信息。    为了方便起见，我们在表 13-2 中列出了完整的列表。这将让你了解哪些功能已经可供你使用。如果你定义了过滤器，bean 别名对于引用该过滤器非常重要。例如，如果你添加了自己的自定义过滤器，你将能够将它们放置在链中。可以使用 `<security:custom-filter />` 元素定义自定义过滤器，例如 `<security:custom-filter ref="myCustomFilter"/>`。通过显式引用这些过滤器，你可以将它们放置在特定过滤器之前或之后。例如，如果你想将 `myCustomFilter` 放置在 `FORM_LOGIN_FILTER` 之后，可以将其定义为  ` <security:custom-filter ref="myCustomFilter" after="FORM_LOGIN_FILTER"/>`  命名空间元素或属性是触发在链中添加过滤器的配置元素。表中的过滤器按照你定义它们时在链中的顺序列出。    ![Image](img/9781430241553_Tab13-02.jpg)    ![Image](img/9781430241553_Tab13-02a.jpg)    __________    ³ [`http://static.springsource.org/spring-security/site/docs/3.1.x/reference/springsecuritysingle.html#filter-stack`](http://static.springsource.org/spring-security/site/docs/3.1.x/reference/springsecuritysingle.html#filter-stack)    事实证明，我们在清单 13-4 中已经通过添加以下内容配置了一个过滤器：  `<security:form-login login-page="/public/authentication/login.htm" default-target-`![Image](img/U002.jpg) `url="/public/main.htm"/>`  如果你查看表 13-2，你会看到 `http/form-login` 配置了 `UsernamePasswordAuthenticationFilter`。此过滤器将利用前面描述的将用户重定向到登录页面或索引页面的功能。顾名思义，它还涵盖了其他功能，我们将在后面讨论。    #### 定义要保护的资源    接下来，我们必须告诉 Spring Security 应该保护哪些资源。这再次通过隐式地向过滤器链添加一个过滤器来完成。我们不声明新的过滤器，而是使用 Spring Security 提供的现有过滤器。我们将使用安全命名空间中的一个元素：`<security:intercept-url>`。如果你查看表 13-2，你会看到这个元素（`intercept-url`）匹配 `ChannelProcessingFilter`，并且将是 `http` 元素的子元素。当我们指定 `<security:intercept-url>` 时，Spring Security 会自动将此过滤器添加到链中。如果输入的资源的模式与请求的资源匹配，该过滤器将执行认证检查。    此时，我们混合了流程和页面，后者由 Spring MVC 控制器直接控制。为了完整性，我们将在此列出它们。对于每个资源，我们将指明它应该是公开访问还是私有访问（仅限经过认证的访问）。让我们从表 13-3 中的流程开始。    ![Image](img/9781430241553_Tab13-03.jpg)    接下来，在表 13-4 中，我们将列出在 Spring MVC 控制器中存在 `@RequestMapping` 的 URL。我们再次定义它们是应该公开访问还是仅在认证后才能访问。    ![Image](img/9781430241553_Tab13-04.jpg)    为了组织需要公开或认证访问的页面，我们将它们分组到 `WEB-INF/view/public` 和 `WEB-INF/view/secured` 下。    #### 配置对资源的访问    清单 13-5 显示了将上述访问规则转换为我们的 XML 配置，并将其添加到我们在清单 13-4 中已经构建的基础配置中。    ***清单 13-5.** 基于我们想要显式配置的请求 URI 添加 intercept-url 规则*  `<security:http use-expressions="true" >` `<security:form-login login-page="/public/authentication/login.htm"` ![Image](img/U002.jpg) `    default-target-url="/public/main.htm"`![Image](img/U002.jpg) `    authentication-failure-url="/public/authentication/login.htm"  />`  `    <security:intercept-url pattern="/index.jsp" access="permitAll" />`  `    <security:intercept-url pattern="/public/**" access="permitAll" />` `    <security:intercept-url pattern="/secured/**" access="fullyAuthenticated"/>`  `    <security:intercept-url pattern="/**" access="denyAll" />` `</security:http>`  此清单应从上到下阅读。这意味着你应该首先指定更具体的规则，然后是更通用的规则。如果资源与某个模式匹配（无论是直接匹配还是通过模式中指定的通配符匹配），则需要 `access` 属性指定的认证级别。如果满足该条件，则授予访问权限；否则拒绝访问。    首先，我们声明 `index.jsp`（我们的欢迎文件）对所有人开放。接下来，我们定义了 `/secured/**` 和 `/public/**` 匹配模式。默认情况下，使用 ant^(**4**) 风格的匹配模式。每个以 `/secured` 开头的 URL 都要求用户在授予访问权限之前进行完全认证。以 `/public` 开头的 URL 可以被任何人访问。我们稍后将讨论 `access` 属性以及这些值的来源。    回想一下，我们的流程与页面放在一起。要启动 `ordersOverviewflow`，你需要完全认证，因为该流程的路径是 `secured/ordersOverview`。因此，我们不需要指定额外的规则，因为对 `/secured` 下任何内容的访问都将被我们的 `/secured/**` 规则捕获。    但是，如果你将流程存储在单独的位置，例如 `WEB-INF/flows` 下，并且你使用 `<webflow:flow-registry>` 的 `base-path` 属性将 `WEB-INF/flows` 设为基础路径，那么你必须遵循相同的约定。也就是说，你必须将受保护的流程放在 `flows/secured` 下，将公共流程放在 `flows/public` 下。如果你不这样做，例如将所有流程放在 `/flows` 下，你将不得不为每个流程添加单独的 `<security:intercept-url>` 规则。    如果我们请求访问 `/createOrders-flow`，我们不需要认证即可访问。但是，如果我们尝试访问 `/placeOrders-flow`，我们首先需要进行认证。如果我们未认证，我们将被重定向到登录页面。登录成功后，Spring Security 会将我们带回我们最初请求的受保护资源（在本例中为 `/placeOrders-flow`）。    你可能还注意到，我们在 `<security:http>` 元素中添加了一个名为 `use-expressions` 的属性，其值为 `true`。我们还需要解释 `fullyAuthenticated`、`permitAll` 和 `denyAll` 这些值突然从何而来。在此之前，我们必须更详细地解释 Spring Security 的工作原理。    每当 `<security:intercept-url>` 匹配到传入的请求 URL 时，Spring Security 需要知道应该检查哪些条件以授予或拒绝访问。这些条件由一个所谓的 `org.springframework.security.access.AccessDecisionManager` 检查。`AccessDecisionManager` 又拥有一个或多个 `org.springframework.security.access.AccessDecisionVoter` 实例。每个投票者将评估访问是被授予、弃权还是拒绝。这由 `AccessDecisionVoter` 的 `vote` 方法的返回值指示。该返回值是一个原始的 `int`，可以是以下常量之一：`AccessDecisionVoter.ACCESS_ABSTAIN,` `AccessDecisionVoter.ACCESS_DENIED`, 或 `AccessDecisionVoter.ACCESS_GRANTED`    __________    ⁴ [`http://static.springsource.org/spring/docs/3.1.0.RELEASE/javadocapi/org/springframework/util/AntPathMatcher.html`](http://static.springsource.org/spring/docs/3.1.0.RELEASE/javadocapi/org/springframework/util/AntPathMatcher.html)    `AccessDecisionManager` 接口有三种实现。我们将使用 `org.springframework.security.access.vote.AffirmativeBased`。如果至少有一个投票者返回 `ACCESS_GRANTED`，这个 `AccessDecisionManager` 将允许访问。另外两个是 `org.springframework.security.access.vote.ConsensusBased`^(**5**) 和 `org.springframework.security.access.vote.UnanimousBased`。^(6) 更多详细信息请参见它们各自的 JavaDoc。    `http` 元素的 `access-decision-manager-ref` 属性是引用 `AccessDecisionManager` 的方式。但是，当定义 `use-expressions` 属性时，会自动为我们添加一个 `AffirmativeBased` 的 `AccessDecisionManager`。因此，我们无需自己配置 `AccessDecisionManager`，因此我们可以省略 `access-decision-manager-ref` 属性。使用的 `AccessDecisionVoter` 是 `org.springframework.security.web.access.expression.WebExpressionVoter`，它将帮助解析你可以在 `access` 属性中使用的 (SpEL) 表达式。    至此，我们已经配置了 Spring Security，以便它拦截每个请求并扫描我们的 `<security:intercept-url>` 条目列表。如果请求的资源与任何列出的条目匹配（无论是直接匹配还是通过通配符匹配），则会检查 access 属性以确定需要做什么。如果 access 属性是 `permitAll`，则始终授予访问权限。如果 access 属性是 `fullyAuthenticated`，则用户必须经过认证才能被允许访问。如果不是这种情况，用户将自动重定向到登录页面。登录后，用户将被重定向回受保护的资源。    最后，如果请求的资源与任何条目都不匹配，则默认情况下使用 `denyAll` 的 `access` 属性拒绝访问。默认情况下，这将呈现服务器的默认“403 访问被拒绝”页面。因此，访问一个随机的 URL 将显示此页面。即使用户登录后，访问一个随机的 URL 仍然会触发“访问被拒绝”页面，因为没有先前的规则明确*授予*访问权限。所有未显式列出的资源现在默认情况下都禁止任何用户访问。    ![Image](img/square.jpg) **提示** 在配置对资源的访问时，以拒绝所有规则结尾总是明智的。如果请求资源的访问要求未列出，则直接拒绝访问。当用户访问我们列出的资源之外的资源时，用户的浏览器上将显示一个“403 访问被拒绝”页面。这确保了未显式配置的资源不会被意外访问。       ![Image](img/square.jpg) **注意** 在资源列表中，我们提到对资源的访问应该是公开的。为此，我们更改了 `WebMvcContextConfiguration` 中定义的资源持有者的映射。我们在 URL 前添加了 `/public` 前缀，以使我们的资源无需认证即可公开访问：  `public void addResourceHandlers(ResourceHandlerRegistry registry) {` `registry.addResourceHandler("/public/resources/**/*").` `    addResourceLocations("classpath:/META-INF/web-resources/");` `}`  __________    ⁵ [`http://static.springsource.org/springsecurity/site/docs/3.1.x/apidocs/org/springframework/security/access/vote/ConsensusBased.html`](http://static.springsource.org/springsecurity/site/docs/3.1.x/apidocs/org/springframework/security/access/vote/ConsensusBased.html)    我们相应地重构了页面中的所有 URL 映射，使其以 /public 为前缀。    ##### 表达式方法和字面量概述    我们仍然需要解释 `permitAll`、`fullyAuthenticated` 和 `denyAll` 从何而来。这些都是 Spring EL 表达式，它们在一个特殊的、具有 Spring Security 意识的上下文中进行评估。在正常的 Spring EL 环境中，评估 `denyAll` 会触发异常。但是，在此范围内，你的表达式可以隐式使用额外的方法和属性。    虽然我们无法在此涵盖所有可能性，但表 13-5 快速概述了在使用表达式时可以在 `access` 属性中使用的方法，而表 13-6 总结了属性。    ![Image](img/9781430241553_Tab13-05.jpg)    ![Image](img/square.jpg) **注意** 这些方法可以在父类 `org.springframework.security.access.expression.SecurityExpressionRoot` 及其子类 `org.springframework.security.web.access.expression.WebSecurityExpressionRoot` 中找到。你会看到父类中还有表 13-5 未涵盖的其他方法。我们没有包含它们，因为它们是别名；例如，`hasAuthority` 和 `hasAnyAuthority` 分别是 `hasRole` 和 `hasAnyRole` 的别名。我们还省略了 `hasPermission` 方法，这些方法专门用于结合后置和前置过滤的基于方法的安全性。    __________    ⁶ [`http://en.wikipedia.org/wiki/CIDR_notation`](http://en.wikipedia.org/wiki/CIDR_notation)    表 13-6 中的方法也可以作为属性访问。它们提供对返回 `Object` 的 getter 方法的访问，例如 `principal`。principal 只是一个 `Object`。大多数情况下，它是 `org.springframework.security.core.UserDetails` 接口的一个实例，我们将在后面讨论。或者，它们为返回布尔值的 getter 方法提供快捷方式（这些属性以 `is` 开头，而不是 `get`）。例如，方法 `boolean isFullyAuthenticated()` 可以直接作为属性 `isFullyAuthenticated` 调用。结果与方法语法完全相同，但省去了你输入括号的麻烦。    ![Image](img/9781430241553_Tab13-06.jpg)    请记住，无论你使用的是方法还是属性，表达式都是 Spring EL 表达式。你将能够使用逻辑运算符，例如 `and`、`or` 等。例如 `access="hasRole('ROLE_X')` **`and`** `hasRole('ROLE_Y')`    #### 配置认证    当用户被重定向到登录页面时，应用程序会要求他们输入用户名和密码组合才能继续。Spring Security 将接收此信息，但需要验证用户是否存在以及提供的密码是否与该用户匹配。    为此，我们需要某种用户存储来保存能够访问应用程序的用户（和密码）。为了简单起见，我们将使用一个内存数据存储，我们将完全在配置中指定用户和密码。稍后我们将看到如何将其替换为数据库存储。    我们需要配置一个 `org.springframework.security.authentication.AuthenticationManager` 来执行此操作。Spring Security 提供了一个命名空间构造（`<security:authentication-manager>`），与 `org.springframework.security.access.AccessDecisionManager` 不同，它将使所需的编码简短且容易。使用此构造，我们不必显式地连接此认证管理器；Spring 会自动检测它。    为了使认证管理器能够完成其工作，它需要引用一个认证提供者。这是连接到包含用户详细信息的存储的桥梁。在这种情况下，我们不会引用外部提供者，而是使用内存用户服务，它被声明为 `<security:user-service>`，并接受一个 `<security:user>` 元素列表，每个元素代表我们系统中的一个用户（参见清单 13-6）。一个用户拥有用户名（此处缩写为 `name`）、密码和逗号分隔的权限列表。这些权限目前并不重要。稍后我们将展示如何使用它们。    ***清单 13-6.** 使用内存用户数据存储的认证管理器配置*  `<security:authentication-manager>` `    <security:authentication-provider>` `        <security:user-service >` `            <security:user name="jd"            ` `                password="5238377ba2eac049901b54004ee9e03db62c0ab0b48133a4a162ab3aedfc809f"`![Image](img/U002.jpg) `                authorities="ROLE_CUSTOMER"/>` `        </security:user-service>` `    </security:authentication-provider>` `</security:authentication-manager>`  重要的是，这段代码还不完整。如你所见，我们有一个奇怪且很长的密码。这被称为*单向哈希密码*。单向哈希是一种加密函数，它接受纯文本作为输入，并生成另一个看起来奇怪的字符串作为输出，称为哈希（或摘要）。其总体思路是，哈希无法（在不花费数十年计算时间的情况下）被逆向工程回原始的纯文本形式。为了比较这些密码，需要获取用户输入的密码，使用哈希函数对其进行哈希处理，然后将输出与存储在数据存储中的哈希进行比较。如果哈希匹配，则密码相同。    在这种情况下，我们使用了输出大小为 256 位的 SHA2 哈希算法（即 SHA-256）。唯一的问题是，Spring 现在期望用户输入哈希值 `"5238377ba2eac049901b54004ee9e03db62c0ab0b48133a4a162ab3aedfc809f"` 作为密码，而不是可读的密码。因为 Spring 不知道我们存储的实际上是一个哈希，它认为这个长字符串是用户必须输入的实际密码。    剩下要做的就是配置 Spring，让它获取用户输入的真正纯文本密码，使用选定的哈希算法对其进行哈希处理，然后将输出的哈希与我们提供的密码字段中的哈希值进行比较。在清单 13-7 中，我们通过指定 `<security:password-encoder>` 来实现这一点。需要配置的内容不多。我们只需说明我们使用的是 SHA-256 位（实际上是 SHA 版本 2）。还有一个奇怪的子元素，我们稍后会讨论。有了这个密码编码器，Spring Security 就知道我们的密码是经过哈希处理的，并且它应该首先对用户输入的密码进行哈希处理，然后再将值与数据存储中的值进行比较。    ***清单 13-7.** 添加密码编码器*  `<security:authentication-manager>` `    <security:authentication-provider>` `        <security:password-encoder hash="sha-256">` `            <security:salt-source user-property="username"/>` `        </security:password-encoder>` `        <security:user-service >` `<security:user name="jd"` ![Image](img/U002.jpg) `                password="5238377ba2eac049901b54004ee9e03db62c0ab0b48133a4a162ab3aedfc809f"`![Image](img/U002.jpg) `                authorities="ROLE_CUSTOMER"/>` `        </security:user-service>` `    </security:authentication-provider>` `</security:authentication-manager>`  ![Image](img/square.jpg) **注意** 为什么我们在仅仅进行基本设置时，要通过引入哈希密码而不是纯文本密码来使事情变得更复杂？我们这样做是有充分理由的。永远不要以纯文本形式存储密码。即使在临时（或基本）解决方案中也不行。一位智者曾告诉我们，“在实践中，临时解决方案等同于将永远存在的生产就绪解决方案。”问问你自己；你开发过多少次临时解决方案，而它至今仍在使用？此外，在使用 Spring Security 或 Java 时，没有充分的理由不对密码进行哈希处理。使用 Apache Commons Codec，^(7) 你可以用一行代码创建一个哈希。永远不要存储纯文本密码！此外，如果有人进行审计并发现密码是纯文本的，你将会遇到麻烦。这是任何安全应用程序的第一步。    你可能想知道，我们最初是如何生成哈希的？我们可以编写一个小型 Java 工具来为我们完成这项工作，使用 Apache Commons Codec：  `String hashedPassword =` ![Image](img/U002.jpg) `org.apache.commons.codec.digest.DigestUtils.sha256Hex(plainTextPassword);`  或者，我们可以用谷歌搜索一个在线网站来做到这一点。网上也有很多免费工具可以生成任何类型的哈希。有一点需要注意，使用在线生成器时要小心，因为你将传递真实的密码。对于测试目的，这没问题，但对于真实场景，请考虑自己编写一个工具或下载一个知名工具**^(8)**。    我们进行哈希处理的密码是“secret”。如果你验证这一点，你会注意到哈希并不完全匹配。在这种情况下，我们使用的是“加盐哈希”，它只不过是在密码后面附加了在认证用户时可以找到的额外数据。例如，它可以是用户的名字。在这种情况下，我们选择了最简单的元素：用户名。    __________    ⁷ [`http://commons.apache.org/codec/`](http://commons.apache.org/codec/)    ⁸ [`http://www.slavasoft.com/hashcalc/`](http://www.slavasoft.com/hashcalc/) `(仅限 Windows - 免费软件)` [`http://sourceforge.net/projects/hash-calculator/files/hash-calculator/`](http://sourceforge.net/projects/hash-calculator/files/hash-calculator/) `(Java - 开源)`    盐的目标是使摘要更加唯一。如果两个用户使用相同的密码，将生成相同的哈希。当使用盐时，哈希会不同，因为它们是使用另一个后缀（在本例中是他们的用户名）计算的。    拥有相同的哈希本身并无害处，因为它们（实际上）无法被逆向工程。但是，你正在给出一个免费的提示：查看哈希的人可能会注意到多个用户具有相同的哈希，从而隐式地知道他们也具有相同的密码。通过添加盐，你消除了最后一个提示，并且还使密码更长。密码越长，哈希抵抗攻击的能力就越强。    Spring 使用以下方案计算加盐哈希：  `plaint_text_password{salt}`  在我们的例子中，我们必须将哈希计算为 `secret{jd}`，其中 `jd` 是我们用户的用户名。如果你现在尝试使用此输入字符串重新生成哈希，你会注意到它将与上面显示的哈希匹配。我们将按照清单 13-8 所示进行配置。    ***清单 13-8.** 使用属性作为盐的密码编码器*  `<security:password-encoder hash="sha-256">` **`<security:salt-source user-property="username"/>`** `</security:password-encoder>`  我们现在已经使用一种哈希类型配置了密码编码器。在内部，我们为其提供了一个盐源，该盐源具有一个值为 `“username”` 的 `user-property`。让我们更详细地看看 Spring Security 将如何处理这个问题：    > 1.  在应用哈希函数之前，首先解析我们的盐。盐是从 `UserDetails` 对象（我们稍后将介绍）中检索的。应该从这个 `Object` 中使用的属性由 `user-property` 属性（`“username”`）的值指示。从 `UserDetails` `Object` 中检索 `username` 属性的值。该值将是我们的盐。然后，盐被添加到密码的末尾，放在大括号之间。因此，当我们的密码是 `secret` 并且 `UserDetails` 对象的 username 属性是 `jd` 时，我们的密码字符串现在将是 `secret{jd}.` > 2.  使用 SHA 256 位对密码字符串 `secret{jd`} 进行哈希处理。 > 3.  最后，将哈希函数的结果与从用户数据存储中检索到的结果进行比较。    #### 整合所有内容    在测试我们的应用程序之前，我们仍然需要对页面进行一些修改。在之前的应用程序中，我们的页面是为我们自己创建的自定义认证方案而调整的。特别是，登录页面和登录后页面需要进行一些修改才能与 Spring Security 协同工作。    首先，我们将处理登录页面。Spring Security 将根据我们提供的用户名和密码执行认证。我们将通过我们的登录页面来执行此操作，该页面包含用户名和密码字段。但是 Spring 需要知道这些项目，所以我们将看看如何处理它。我们还将看到如何显示登录可能产生的错误消息。    接下来，我们将对主页进行一个小修改，以指示认证是否成功。以前，如果用户使用登录链接登录，他们只能推断登录一定成功了，因为没有错误。我们将通过添加一条消息来说明认证成功来对此进行一些增强。    最后，我们将更改注销，使其也由 Spring Security 处理。    ##### 更改登录页面    正如我们之前解释的，我们隐式配置了一个过滤器（`UsernamePasswordAuthenticationFilter`），它将执行到登录页面和登录后页面的重定向。除此之外，此过滤器还将监听一个预配置的 URL，并查找预配置的用户名和密码请求参数。这些默认键值如下：    > *   用户名请求参数名称：`j_username` > *   密码请求参数名称：`j_password` > *   请求 URL：`/j_spring_security_check`    ![Image](img/square.jpg) **注意** 你可以通过指定 *`username-parameter`*、*`password-parameter`* 和 *`login-processing-url`* 属性来更改 `<security:form-login>` 元素上的所有这三个值。我们提到这个选项是因为在你自己的 Spring 项目中可能值得考虑，尽管在本章中我们将保留这些值为默认值。请求 URL 将在 HTML 表单的 action 中。任何检查你 HTML 源代码的人都会知道你正在使用 Spring Security。虽然“通过模糊实现安全”本身可能会给人一种虚假的安全感，但如果应用在控制良好且安全的环境中（就像我们正在构建的），它被认为是一个加分项，因为你移除了另一个提示。在这种情况下，它是你用来保护应用程序的版本或软件产品。    `login.jsp` 现在看起来像清单 13-9。    ***清单 13-9.** 调整后的 WEB-INF/view/public/authentication/login.jsp，以集成 Spring Security 基于表单的登录过滤器。*  `<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core"%>` `<%@ taglib prefix="spring" uri="http://www.springframework.org/tags"%>` `<%@ taglib prefix="form" uri="http://www.springframework.org/tags/form"%>`  `    <spring:url value="`**`/j_spring_security_check`**`" var="`**`login`**`" />` `        <form action="`**`${login}`**`" method="POST">` `            <table style="width: 100%">` `                <tr>` `                    <td>` `                        <spring:message code="label.page.login.username" />` `                    </td>` `                    <td>` `<input type="text"` **`name="j_username"`**`/>` `                    </td>` `                    <td>` `                        <spring:message code="label.page.login.password" />` `                    </td>` `                    <td>` `<input type="password"` **`name="j_password"`** `/>` `                    </td>` `                </tr>` `                <tr>` `                    <td colspan="4">`  `                            <c:if test="${not empty param.authenticationNok}">` `                                <font color="red">` `<spring:message code="label.login.failed"` ![Image](img/U002.jpg)`                                                                               arguments="${SPRING_SECURITY_LAST_EXCEPTION.message}" />` `                                </font>` `                            </c:if>` `                        </td>` `                    </tr>` `                </table>`  `                <div align="right" style="margin-bottom: 20px;                      margin-top: 10px" >` `                    <button type="submit" id="login">` `                        <spring:message code="label.page.login.login"/>` `                    </button>` `                </div>` `        </form>` `</jsp:root>`  如你所见，我们确保密码和用户名的输入字段具有所需的名称，以便 Spring 能够拾取该参数。我们还将表单提交到默认的 Spring Security 认证 URL。    你可能还会注意到，我们添加了一个部分来显示登录错误：  `<c:if test="${not empty param.authenticationNok}">` `    <font color="red">` `        <spring:message code="label.login.failed"`![Image](img/U002.jpg) `            arguments="${SPRING_SECURITY_LAST_EXCEPTION.message}" />` `     </font>` `</c:if>`  当用户提供错误的凭据时，Spring Security 会将异常以键 `SPRING_SECURITY_LAST_EXCEPTION` 存储在会话中。我们唯一需要做的就是显示它。该消息可以本地化，也可以更改。我们将在本章的“本地化”部分进一步讨论。为了检测到新的登录失败，我们检查参数 `authenticationNok`* 是否存在。*    这个参数是我们添加的。当 Spring Security 检测到登录失败时，它可以重定向到指定的 URL。这样做时，你可以传递额外的参数。为了进行此重定向，我们需要修改 `<security:form-login>`，如清单 13-10 所示。    ***清单 13-10.** 认证失败时使用参数进行转发*  `<security:form-login login-page="/public/authentication/login.htm"` ![Image](img/U002.jpg) `    authentication-failure-url="`**`/public/authentication/login.htm?`**![Image](img/U002.jpg) **`                                  authenticationNok=1`**`"` `</security:form-login>`  认证失败时，我们会重定向回登录页面，但这次会附加参数 `authenticationNok`，这将使消息出现。我们为参数提供一个值 1，只是为了让它有一个值，这样我们就可以轻松测试此参数是否存在。然而，该值并不重要。这完成了对登录页面的修改；现在我们将继续处理主页。    ![Image](img/square.jpg) **注意** 我们还从之前的示例中删除了 `authentication-flow.xml`，因为登录现在由 Spring Security 基础设施处理。这也意味着我们更改了 `header.jsp` 中的锚点，使其指向 `/public/authentication/login.htm`，而不是启动（不再存在的）认证流程。    ##### 更改主页    当认证成功时，我们会重定向到主页。在这种情况下，我们想显示一条消息，表明认证成功。为此，我们将向 `default-target-url` 属性中指定的 URL 添加一个参数（参见清单 13-11）。    ***清单 13-11.** 配置 default-target-url，带有一个额外参数，Spring Security 将在成功认证后将用户重定向到该 URL*  `<security:form-login login-page="/public/authentication/login.htm"` **`default-target-`**![Image](img/U002.jpg) **`url="/public/main.htm?authenticationOk=1"`** `authentication-failure-`![Image](img/U002.jpg) `url="/public/authentication/login.htm?authenticationNok=1"  />`  在清单 13-12 中，我们展示了主页的相关部分（`main.jsp`，位于 `WEB-INF/view/public/`），该部分将在登录成功时向用户显示消息。显示消息的代码以粗体显示。    ***清单 13-12.** 调整主页以在登录成功时显示适当的消息*  `<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core"%>` `<%@ taglib prefix="spring" uri="http://www.springframework.org/tags"%>` `<%@ taglib prefix="form" uri="http://www.springframework.org/tags/form"%>` `<%@ taglib prefix="fn" uri="http://java.sun.com/jsp/jstl/functions"%>` `<%@ taglib prefix="sec"`![Image](img/U002.jpg) `        uri="http://www.springframework.org/security/tags"%>` `<sec:authorize access="fullyAuthenticated">` `    <c:if test="${not empty param.authenticationOk}">` `        <div id="authenticationOk"`![Image](img/U002.jpg) `            style="color: green; display: block; margin-left: 15px;` `            margin-bottom: 10px;">` `            <table>` `                <tr>` `                    <td>` `                        <ul style="list-style-type: disc">` `                            <li>` `                                <h3>` `                                   <sec:authentication` `                                    property="principal.username"`![Image](img/U002.jpg) `                                    var="username" scope="request"/>`![Image](img/U002.jpg)  **`<spring:message`** **`                                      code="label.page.main.authentication.ok"`**![Image](img/U002.jpg) **`                                      arguments="${username}" />`**![Image](img/U002.jpg) `                                </h3>` `                            </li>` `                        </ul>` `                    </td>` `                </tr>` `            </table>` `        </div>` `    </c:if>` `</sec:authorize>`  `<script>` `    dojo.addOnLoad(function(){` `        function fadeIt() {` `            if(dojo.byId("authenticationOk")){` `                dojo.style("authenticationOk", "opacity", "1");` `                var fadeOutArgs = {node: "authenticationOk", duration: 15000};` `                dojo.fadeOut(fadeOutArgs).play();` `            }` `        }` `    fadeIt();` `    });` `</script`  如果有人手动附加此参数（`authenticationOk=1`），这不会产生副作用，消息也不会显示。在主页上，使用安全标签库（`<sec:authorize access="fullyAuthenticated">`）在显示消息的代码周围有一个认证检查。    我们仅在用户已经过认证时才检查 `authenticationOk` 参数。如果我们随后看到此参数存在，我们会显示一条消息，以便用户知道认证成功。此认证策略确保消息只显示一次。如果经过认证的用户使用“主页”链接或任何其他方式返回欢迎页面，我们已确保消息不会第二次显示。    我们将在后面的“授权访问”部分详细讨论 Spring Security 标签库。    ##### 注销    到目前为止，注销功能是在我们的 `com.apress.prospringmvc.bookstore.web.controller.AuthenticationController` 中实现的。让我们重复一下代码片段以刷新我们的记忆。  `@RequestMapping(value = "/logout", method = RequestMethod.GET)` `public String logout(HttpSession session) {` `    session.invalidate();` `    return "redirect:/index.htm";` `}`  当用户单击注销链接时，它将定向到 `/logout`，这将调用控制器方法 `logout`，并在 HTTP 会话上调用 `invalidate`。之后，用户被重定向到主页。虽然这并不复杂，但它不应再成为我们应用程序代码的一部分。Spring Security 也可以处理这个问题。我们可以安全地删除 `AuthenticationController` 中的代码。    之后，我们将配置 Spring Security 来接管此任务。我们将通过配置 `LogoutFilter` 来实现。在之前的表 13-2 中，我们概述了可能的过滤器；`LogoutFilter` 就是其中之一。此过滤器在表中具有以下条目：    ![Image](img/9781430241553_Tab13-02b.jpg)    此过滤器可以声明为 `http` 元素内的一个元素（`<security:logout>`）。此配置的代码如清单 13-13 所示。    ***清单 13-13.** 在 src/main/resources/spring/spring-security.xml 中配置注销功能*  `<security:logout logout-success-url="/public/main.htm" logout-url="/logout" invalidate-`![Image](img/U002.jpg) `session="true"/>`  `logout-success-url` 属性指示 Spring Security 在注销成功时应重定向到的页面。在我们的例子中，这是主页。第二个属性 `logout-url` 指示过滤器应监听的 URL。在我们的例子中，这是 `/logout`。每当调用 `/logout` 时，`LogoutFilter` 将被调用，用户将注销。最后一个属性指示是否也应使 HTTP 会话失效。如果你指定 `false`，则只会清除 `org.springframework.security.core.context.SecurityContext`，而不会使 HTTP 会话失效。我们稍后将介绍这个类；现在只需知道，从你通过认证的那一刻起，认证信息就可以通过 `org.springframework.security.core.context.SecurityContext` 访问。你也可以选择指示清除整个 HTTP 会话。虽然这不是强制性的，但在几乎所有情况下，同时清除会话是最安全的。如果你不指定 `invalidate-session` 属性，使会话失效是默认行为。我们在这里显式指定它，以便任何阅读我们安全配置的人都能理解会话肯定会被清除（不是我们怀疑默认值是否有效，而是为了给出明确的指示，并非每个人都了解默认值）。    我们的 `header.jsp` 中的注销链接本身保持不变，如清单 13-14 所示。    ***清单 13-14.** WEB-INF/templates/header.jsp 中的注销链接*  `<spring:url value="/logout" var="logout" />` `<a href="${logout}">` `    <spring:message code="nav.logout"/>` `</a>`  此链接现在指向 `/logout`。唯一缺少的是，此链接只应在用户实际登录时显示。我们将在介绍授权标签时看到如何处理这个问题。我们的注销控制现在已完全正常运行。    #### 完整的安全配置    在清单 13-15 中，你将找到我们为本部分构建的完整安全配置（位于 `src/main/resources/spring/spring-security.xml`）。尽管我们包含了许多功能，但配置部分仍然相当易于理解，正如你将看到的。    ***清单 13-15.** 基本安全设置的完整概述*  `<security:http use-expressions="true" >` `    <security:form-login login-page="/public/authentication/login.htm"    `![Image](img/U002.jpg) `default-target-url="/public/main.htm?authenticationOk=1"` ![Image](img/U002.jpg) `        authentication-failure-url="/public/authentication/login.htm`![Image](img/U002.jpg) `                                   ?authenticationNok=1"/>`  `    <security:intercept-url pattern="/index.jsp" access="permitAll" />`  `    <security:intercept-url pattern="/public/**" access="permitAll" />` `    <security:intercept-url pattern="/secured/**"` `    access="fullyAuthenticated"/>`  `    <security:intercept-url pattern="/**" access="denyAll" />` `<security:logout logout-success-url="/public/main.htm"` ![Image](img/U002.jpg) `              logout-url="/logout" invalidate-session="true"/>` `</security:http>`  `<security:authentication-manager>` `    <security:authentication-provider>` `        <security:password-encoder hash="sha-256">` `            <security:salt-source user-property="username"/>` `        </security:password-encoder>` `    <security:user-service >` `<security:user name="jd"` ![Image](img/U002.jpg) `password="5238377ba2eac049901b54004ee9e03db62c0ab0b48133a4a162ab3aedfc809f"`![Image](img/U002.jpg) `            authorities="ROLE_CUSTOMER"/>` `        </security:user-service>` `    </security:authentication-provider>` `</security:authentication-manager>`  结合前两节解释的登录和主页修改，这保护了我们整个应用程序。我们现在能够通过表单登录进行登录。认证通过 Spring Security 安全地处理。我们所有的资源现在都是安全的；有些需要用户认证后才能访问，有些则是公开可访问的。未显式列出的资源默认拒绝访问。    让我们继续，看看我们还能做什么！    ### 转向数据库    到目前为止，我们已将用户信息存储在配置本身中。通过使用 `<security:user-service>`，Spring Security 创建了一个内存数据存储，该存储将使用我们指定的数据进行填充。在我们当前的应用程序中，用户实际上存储在数据库中，并建模为 `org.springframework.security.authentication.dao.Account` 的实例。最好调整我们的配置，以便我们可以从现有的数据库表中加载用户帐户。事实证明，这是一个非常简单的步骤。    通过定义 `<security:authentication-provider>`，Spring 注册了一个 `org.springframework.security.authentication.dao.DaoAuthenticationProvider`。这个类将处理所有底层细节。对我们来说重要的是，这个类委托给一个所谓的 `org.springframework.security.core.userdetails.UserDetailsService` 类，该类负责从任何合适的位置加载用户信息。在我们之前的实现中，这是从内存数据存储加载的。现在我们将提供我们自己的实现。这是一个非常容易实现的接口，因为它只有一个方法：  `UserDetails loadUserByUsername(String username) throws UsernameNotFoundException;`  ![Image](img/square.jpg) **注意** UserDetailsService 不负责认证。那是认证提供者的工作。在我们的例子中，这将是 Spring Security 的 `DaoAuthenticationProvider`。`UserDetailsService` 的目标是访问包含用户信息以及可能的凭据的数据存储（数据库、目录服务器或可能的文件）。它应该将这些信息从该数据存储的内部结构映射到预定义的标准对象（`org.springframework.security.core.userdetails.UserDetails`），并将其返回给提供者，以便提供者可以继续其工作。    基本上，实现应该从其配置确定的任何位置加载用户信息，并将该信息转换为 `UserDetails` 对象。正如我们稍后将看到的，`UserDetails` 对象是你在请求安全信息时将使用的主要对象。该实现如清单 13-16 所示。    ***清单 13-16.** 实现自定义的 UserDetailsService*  `package com.apress.prospringmvc.bookstore.web.security;`  `import org.apache.commons.lang3.StringUtils;` `import org.springframework.beans.factory.annotation.Autowired;` `import org.springframework.security.core.GrantedAuthority;` `import org.springframework.security.core.authority.SimpleGrantedAuthority;` `import org.springframework.security.core.userdetails.UserDetails;` `import org.springframework.security.core.userdetails.UserDetailsService;` `import org.springframework.security.core.userdetails.UsernameNotFoundException;` `import org.springframework.stereotype.Component;` `import com.apress.prospringmvc.bookstore.domain.Account;` `import com.apress.prospringmvc.bookstore.domain.Permission;` `import com.apress.prospringmvc.bookstore.domain.Role;` `import com.apress.prospringmvc.bookstore.service.AccountService;`  `@Component` `public class BookstoreUserDetailsService implements UserDetailsService {`  `@Autowired` `private CustomerService customerService;`  `    @Override` `    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {` `        return new BookstoreUserDetails(customerService.getCustomer(username));` `    }` `}`  我们使用 `CustomerService` 来加载 `Customer`，并将其传递给我们的自定义 `UserDetails` 对象。该对象的实现如清单 13-17 所示。    ***清单 13-17.** 实现自定义的 UserDetails*  `package com.apress.prospringmvc.bookstore.web.security;`  `// 导入省略，参见清单 13-16`  `public class BookstoreUserDetails implements UserDetails {`  `    private Customer customer;` `private List<GrantedAuthority> authorities = new` ![Image](img/U002.jpg) `        ArrayList<GrantedAuthority>();`  `    public BookstoreUserDetails(Customer customer) {` `        this.customer = customer;` `    }`  `    // 省略类实例变量的 getter 和 setter 方法` `}`  有用于用户名、密码和我们的 `Customer` 对象的 getter。目前，我们为 `GrantedAuthority` 属性返回一个空列表。我们稍后将看到这如何融入我们的应用程序。所有其他方法都是 `UserDetails` 的特性，例如帐户过期等。我们不会使用这些特性，因此所有方法都返回 `true`。一旦用户通过认证，关于该用户的信息将存储在 `org.springframework.security.core.context.SecurityContext` 的实现中。`org.springframework.security.core.context.SecurityContextHolder` 类是一个 `java.lang.ThreadLocal`，^(9) 将提供对 SecurityContext 的访问。`SecurityContext` 本身只有一个用于 `org.springframework.security.core.Authentication Object` 的 getter（和 setter）。这意味着你将能够通过调用以下代码在你的 Web 应用程序中的任何位置访问 `Authentication Object`    __________    ⁹ [`http://docs.oracle.com/javase/6/docs/api/java/lang/ThreadLocal.html`](http://docs.oracle.com/javase/6/docs/api/java/lang/ThreadLocal.html)  `SecurityContextHolder.getContext().getAuthentication();`  在 `Authentication` 对象上，我们特别对 `getPrincipal` 方法感兴趣。这将返回关于当前已认证用户的详细信息。在上一节中，我们向你介绍了 `UserDetails`，嗯，这就是 `getPrincipal` 返回的内容，一个 `UserDetails` 实现。在我们的例子中，它将是一个自定义的 `UserDetails`，即我们的 `BookstoreUserDetails`。    ![Image](img/square.jpg) **注意** 如果你查看 API，你会看到 `getPrincipal` 返回 `Object`。因此，它也可以返回除 `UserDetails` 实现之外的其他类型。如果你定义了自己的自定义 `org.springframework.security.authentication.AuthenticationProvider`，你可以自由地构建自己的认证对象。在这种情况下，`getPrincipal` 的返回值可以是任何你希望的主体类型。    ### 以正确的方式保护我们的流程    我们到目前为止建立的设置仍然像保护普通请求映射一样保护我们的流程。虽然这似乎可行，但实际上并非如此。它只适用于来自外部访问流程的请求。如果一个流程想要启动一个子流程，该请求已经在安全过滤器之后，不会被捕获（参见图 13-4）。  ![Image](img/9781430241553_Fig13-04.jpg)  ***图 13-4.** 使用过滤器链将流程作为请求 URI 进行保护是不够的。从父流程启动子流程将绕过此机制。*    如果你还记得请求映射，`placeOrders-flow`（位于 `WEB-INF/view/secured/placeOrders/`）只能由经过认证的用户访问。`createOrders-flow`（位于 `WEB-INF/view/public/createOrders/`）是公开可用的。这里发生的情况是，当请求进来以转换到 `placeOrder` 时，不会触发认证（因为它仍然是 `createOrders-flow`）。`placeOrder` 是 `createOrders-flow` 中的一个子流程状态。为了刷新我们的记忆，你可以从下面的 `createOrders-flow` 代码片段中看到，该代码片段从流程转换到 `placeOrder` 的那一刻起启动子流程：  `<subflow-state id="placeOrder" subflow="secured/placeOrders">` `    <input name="orderForm"/>`  `    <output name="orderId"/>` `        <transition on="endOrderOk" to="endOrderOk"/>` `</subflow-state>`  当 Web Flow 进行内部状态转换到子流程时，它实际上会执行，而不会将用户重定向到登录页面。我们真正想要的是 Web Flow 与 Spring Security 的集成，以便在执行流程时涉及 Spring Security。    #### 向流程添加访问属性    在这里，我们将看到为了调用 Spring Security for Web Flow 需要做什么。当你这样做时，你的流程间通信也将得到保护。    为了保护你的流程，你可以在流程定义的顶部添加以下元素：  `<secured attributes="…"/>`  `attributes` 属性的用法与 Spring Security 配置中 `<security:intercept-url>` 的 `access` 属性相同。你可以使用它来放置在此流程可以执行之前必须有效的表达式。    如果你回到“*配置要保护的资源*”部分，你会看到以下两个流程需要认证：    > *   `placeOrders-flow` > *   `ordersOverview-flow`    第三个流程 `createOrders-flow`（位于 `WEB-INF/view/public/createOrders/`）是公开的，不需要任何认证即可执行。我们将 `fullyAuthenticated` 条件添加到 `placeOrders-flow.xml`（位于 `WEB-INF/view/secured/placeOrders/`）和 `ordersOverview-flow.xml`（位于 `secured/ordersOverview/`），如清单 13-18 和 13-19 所示。    ***清单 13-18.** 将 fullyAuthenticated 授权检查作为顶级元素添加到 ordersOverview-flow.xml 和 placeOrders-flow.xml*  `<flow`  `      xsi:schemaLocation="http://www.springframework.org/schema/webflow` `                           http://www.springframework.org/schema/webflow/                            spring-webflow-2.0.xsd">`  `<secured attributes="fullyAuthenticated" />` `...`  ***清单 13-19.** 将 permitAll 检查作为顶级元素添加到 createOrders-flow.xml*  `<flow`  `        xsi:schemaLocation="http://www.springframework.org/schema/webflow` `                           http://www.springframework.org/schema/webflow/                            spring-webflow-2.0.xsd">`  `<secured attributes="permitAll" />` `...`  `<secured>` 元素还有第二个属性，名为 `match`。在使用 SpEL 时，此属性不重要。但是，如果不使用 SpEL，你会受到更多限制。例如，无法定义“`or”或` “`and”` 关系。稍后我们将介绍角色的概念，但你应该知道，你可以根据是否存在一个或多个角色来允许或拒绝执行流程。使用 SpEL，如果你希望仅在两个角色都存在时才执行流程，则只需使用 `and` 运算符；如果其中一个角色就足够了，则使用 `or` 运算符。如果不使用 SpEL，则必须使用 `match` 属性，该属性允许你在 `any`（如果请求的角色之一存在，则执行流程）和 `all`（必须存在 `attributes` 属性中的所有角色才能执行流程）之间切换。    ![Image](img/square.jpg) **注意** `createOrders-flow` 和 `ordersOverview-flow` 上的流程安全配置有点多余，因为它们都是顶级流程。`<security:intercept-url pattern="/public/**" access="permitAll" />` 和 `<security:intercept-url pattern="/secured/**" access="fullyAuthenticated"/>` 已经在我们进一步操作之前检查了我们是否具有适当的认证。但是，流程中的 `<secured>` 元素可用于执行更细粒度的访问控制或授权。虽然过滤器配置可能允许用户通过（例如，用户已成功认证），但你仍然可以检查仅当用户具有特定角色时才授予访问权限；例如，`<secured attribute="hasRole(ROLE_SOMEROLE)"/>`。角色将在“基于角色的访问控制”部分讨论。    #### 配置 SecurityFlowExecutionListener    `org.springframework.webflow.security.SecurityFlowExecutionListener` 类将启用 Web Flow 和 Spring Security 之间的集成。为了使我们之前讨论的 `<security>` 元素执行任何操作，需要它。这个类是我们在第 12 章 中看到的 Web Flow `org.springframework.webflow.execution.FlowExecutionListener` 的一个实现。    通常，这应该是一个两段的章节，因为你通常只需定义一个 bean（`SecurityFlowExecutionListener`），并使用 `<webflow:flow-execution-listeners>` 元素将其绑定到 `webflow-config.xml` 中的流程执行器。事实证明，当使用 Spring Security 启用 SpEL 的安全表达式时，`SecurityFlowExecutionListener` 目前存在一个问题。当你阅读本文时，这个问题可能已经解决；如果是这样，那么你可以像往常一样继续（只需像我们在清单 13-21 中看到的那样配置 `SecurityFlowExecutionListener`）。如果问题^(10) 尚未解决，那么你有两个选择：    > *   按原样使用 `SecurityFlowExecutionListener`，但在这种情况下，你不能在流程的 `<security>` 元素中使用 SpEL。你将不得不回退到主要使用角色。 > *   使用我们的自定义 `com.apress.prospringmvc.bookstore.web.security.BookstoreSecurityFlowExecutionListener`，它应该可以作为解决方法。使用这种方法，你仍然可以使用 SpEL。    为了保持一致性，我们在示例中采用了第二种解决方案。    要配置 `BookstoreSecurityFlowExecutionListener`，我们打开 `src/main/resources/spring/webflow-config.xml` 并定义一个 Spring bean，如清单 13-20 所示。    ***清单 13-20:** 定义 `SecurityFlowExecutionListener`*  `<bean id="securityFlowExecutionListener"` ![Image](img/U002.jpg) `class="com.apress.prospringmvc.bookstore.web.security.BookstoreSecurityFlowExecutionListener`![Image](img/U002.jpg) `"/>`  接下来，我们使用流程执行器配置监听器（参见清单 13-21）。    ***清单 13-21:** 将 `SecurityFlowExecutionListener` 与流程执行器集成*  `<webflow:flow-executor id="flowExecutor" flow-registry="flowRegistry" >` `    <webflow:flow-execution-repository max-executions="3" max-execution-snapshots="10" />` **`<webflow:flow-execution-listeners>`** **`        <webflow:listener ref="securityFlowExecutionListener"/>`** **`    </webflow:flow-execution-listeners>`** `</webflow:flow-executor>`  就是这样！如果你想配置默认的 `SecurityFlowExecutionListener`，其方式完全相同，只是 `securityFlowExecutionListener` bean 的类将是 `org.springframework.webflow.security.SecurityFlowExecutionListener`，而不是 `com.apress.prospringmvc.bookstore.web.security.BookstoreSecurityFlowExecutionListener`。    ![Image](img/square.jpg) **注意** 我们没有将 `BookstoreSecurityFlowExecutionListener` 代码包含在本节中，因为那会离题太远。如果你到了需要这个的地步，并且问题尚未修复，我们建议你阅读该问题（参见脚注中的链接）并同时查看源代码。这将是理解我们应用的修复的最佳方式。    __________    ¹⁰ [`https://jira.springsource.org/browse/SWF-1508`](https://jira.springsource.org/browse/SWF-1508)    ### 传输安全    目前，我们的安全设置中仍然存在一个主要的安全漏洞。当我们的用户登录时，他们提供用户名和密码凭据，这些凭据将由 Spring Security 进行认证。然而，这些凭据是通过浏览器使用 HTTP（一种纯文本协议）发送到 Web 服务器的。任何捕获数据的人都能够以明文形式看到用户名和密码。为了说明这一点，我们启动了 Wireshark**^(11)** 并让它监听环回适配器。接下来，我们在应用程序中执行了一次登录，图 13-5 显示了结果。  ![Image](img/9781430241553_Fig13-05.jpg)  ***图 13-5.** HTTP 会话的网络捕获，显示通过表单登录时明文凭据*    这看起来非常不安全。为了改变这种行为，我们将在用户登录时切换到 SSL（安全套接字层，现在称为 TLS，即传输层安全，是其继任者，但为了保持一致，我们将其称为 SSL）。从那时起，所有内容都将通过 SSL 提供。    在继续之前，你必须准备你的 tomcat 或 tomcat STS 实例，以添加一个额外的 SSL 连接器。深入探讨 SSL 协议或 PKI（公钥基础设施）的内部原理超出了本书的范围，但为了使其工作，你需要为 tomcat 提供一个密钥库，其中包含一个私钥/公钥对以及该公钥的证书。通常，证书部分由第三方证书颁发机构 (CA) 生成。幸运的是，你可以自签名这样的证书，这对于测试或封闭的生产环境来说是理想的。    作为一个小插曲，我们想分享一些关于自签名证书的内容，因为有时人们对它们存在误解。使用自签名证书的 PKI 与使用官方 CA 签名证书具有完全相同的加密级别。因此，你的 SSL 不会更不安全；加密强度、过程以及所有依赖它们的东西都提供完全相同的保证。    __________    ¹¹ [`http://www.wireshark.org/`](http://www.wireshark.org/)    区别在于信任。使用自签名证书，你的用户必须显式接受该证书，并且浏览器会给他们一个警告，提示无法验证身份。如何呈现取决于浏览器，但使用 Safari，他们会看到如图 13-6 所示的页面。  ![Image](img/9781430241553_Fig13-06.jpg)  ***图 13-6.** 浏览器确认信任我们的自签名证书*    为了仅信任当前会话的证书，你可以单击“继续”。如果你想信任未来会话的证书，则必须单击“显示证书”。这样做将显示如图 13-7 所示的对话框。如果你随后选中“连接到 localhost 时始终信任 Bookstore”，该证书将被存储。  ![Image](img/9781430241553_Fig13-07.jpg)  ***图 13-7.** 接受自签名证书*    单击“继续”后，该证书将被信任（仅限此会话或未来会话，具体取决于你是否在图 13-7 中选中了复选框），你将能够通过 SSL 继续。很明显，这种信任仅适用于内部测试。    对于真正的 CA 签名证书，CA 在真正颁发证书之前已经向证书持有者索取了凭据。例如，我们永远无法从授权的 CA 获得 google.com 的服务器证书，因为我们无法证明我们拥有该域或对其有任何授权。    客户端的浏览器信任库（其中包含授权根 CA 的列表）默认也不会信任我们的自签名证书，因为它无法由任何已知且受信任的 CA 验证。我必须显式地接受其信任。    因此，对于生产网站，自签名证书将是一个坏主意，因为用户无法验证证书背后的身份是否真的是它所声称的（你的服务器）；没有第三方验证身份并给予任何批准。但是，如果你在有限的用户集（封闭生产）之间发送数据，并且各方相互信任，那么向各方发送自签名证书是完全可行的。每一方都知道你的身份（你亲自通过 U 盘提供，或从你的个人帐户通过电子邮件发送）是与该证书相关联的身份（身份可以是个人、服务器、域或任何东西）。这样，你可以轻松节省由 CA 签署公钥的成本。    要生成自签名证书，我们可以使用一个名为 `keytool` 的 Java 工具。要使用它，请执行以下步骤：    > 1.  打开命令行提示符并输入以下命令（确保 JDK 在你的路径上可用）：`keytool -genkey -alias tomcat -keystore keystore.jks` > 2.  输入 **tomcat** 作为密码，并在提示重新输入时再次输入：`Enter keystore password:` >     `Re-enter new password:` > 3.  按如下方式完成提示：`What is your first and last name?` >     `  [Unknown]:  `**`Bookstore`** >     `What is the name of your organizational unit?` >     `  [Unknown]:  `**`Bookstore`** >     `What is the name of your organization?` >     `  [Unknown]:  `**`Bookstore`** >     `What is the name of your City or Locality?` >     `  [Unknown]:  `**`Brussels`** >     `What is the name of your State or Province?` >     `  [Unknown]:  `**`Brussel`**`s` >     `What is the two-letter country code for this unit?` >     `  [Unknown]:  `**`BE`** >     `Is CN=Bookstore, OU=Bookstore, O=Bookstore, L=Brussels, ST=Brussels, C=BE correct?` >     `[no]:  `**`yes`** > 4.  不输入任何内容，直接按 Enter。提示符将返回，你的自签名密钥已在密钥库中准备就绪。`Enter key password for <tomcat>` >     `        (RETURN if same as keystore password):`    将密钥库放在你的 tomcat 主目录 `/conf` 下，如果你使用 STS，则是 `spring-insight-instance/conf/`。    接下来打开 server.xml（`spring-insight-instance/conf/server.xml`）并配置 SSL 连接器，如清单 13-22 所示。如果你想了解此处所示配置的完整说明，请参阅 Tomcat 操作指南^(12) 中的“编辑 Tomcat 配置文件”部分。    __________    ¹² [`http://tomcat.apache.org/tomcat-7.0-doc/ssl-howto.html`](http://tomcat.apache.org/tomcat-7.0-doc/ssl-howto.html)    ***清单 13-22.** 向 tomcat 的 server.xml 添加 SSL 连接器*  `<Connector executor="tomcatThreadPool" port="${bio.https.port}"` ![Image](img/U002.jpg) `protocol="org.apache.coyote.http11.Http11Protocol"`![Image](img/U002.jpg) `connectionTimeout="20000" redirectPort="${bio.https.port}" acceptCount="100"` ![Image](img/U002.jpg) `maxKeepAliveRequests="15"`![Image](img/U002.jpg) `keystoreFile="${catalina.base}/conf/keystore.jks" keystorePass="tomcat" keyAlias="tomcat"`![Image](img/U002.jpg) `SSLEnabled="true" scheme="https" secure="true"/>`  在此示例中，我们依赖于 STS 实例。像 `${bio.https.port}` 这样的变量是预定义的。如果你使用的是 Tomcat，你可以将它们替换为 8443，这将是我们的默认 SSL 端口。    之后，你可以启动 Tomcat。在启动之前，不要忘记在 STS 中清理服务器。在修改服务器配置中的任何内容后，你必须“清理”以便复制最新的文件（在服务器视图中右键单击服务器实例，然后单击“清理”）。    此时，验证你的服务器是否启动并且没有抛出任何错误。如果配置有任何问题，Tomcat 几乎会在启动后立即显示错误。    最后要做的是告诉 Spring Security 我们想要为哪些映射使用 SSL（参见清单 13-23）。我们将为以下内容使用 SSL    > *   我们的登录页面 > *   购书流程    ***清单 13-23.** 为特定请求 URI 配置安全传输*  `<security:intercept-url pattern="/index.jsp" access="permitAll" />` `<security:intercept-url pattern="/public/authentication/**" access="permitAll"` **`requires-`**![Image](img/U002.jpg) **`channel="https"`** `/>` `<security:intercept-url pattern="/public/createOrders/**" access="permitAll"` **`requires-`**![Image](img/U002.jpg) **`channel="https"`** `/>`  `<security:intercept-url pattern="/public/**" access="permitAll" />`  `<security:intercept-url pattern="/secured/**" access="fullyAuthenticated"` **`requires-`**![Image](img/U002.jpg) **`channel="https"`** `/>`  `<security:intercept-url pattern="/**" access="denyAll" />`  `requires-channel` 将自动将我们的协议从 HTTP 更改为 HTTPS（即基于 SSL 的 HTTP）。为了说明这一点，我们将访问我们的主页（参见图 13-8）。  ![Image](img/9781430241553_Fig13-08.jpg)  ***图 13-8.** 登录前，我们仍在 HTTP 上。*    接下来，我们单击导航栏中的“登录”链接，结果如图 13-9 所示。  ![Image](img/9781430241553_Fig13-09.jpg)  ***图 13-9.** 单击“登录”导航链接后，我们自动切换到 HTTPS。*    现在当我们登录时，我们的凭据被安全地传输。如果我们查看证书，我们将看到使用 `keytool` 生成时输入的数据。我们还将看到没有信任，因为它不是由存储在我们浏览器信任库中的受信任 CA 生成的。图 13-10 显示了我们之前创建的自签名证书。它不被我们的浏览器（在本例中为 Safari）信任。  ![Image](img/9781430241553_Fig13-10.jpg)  ***图 13-10.** 由 tomcat 颁发的证书信息*    ![Image](img/square.jpg) **注意** 当你第一次运行示例应用程序时，转到登录页面将自动触发切换到 SSL。在这种情况下，你将收到如图 13-6 和图 13-7 所示的自签名证书警告。因此，在你的浏览器通过 SSL 继续访问登录页面之前，你必须首先接受自签名证书。    当我们现在检查网络上的内容时，在配置 SSL 之后，我们会看到初始请求是对登录页面的普通 HTTP GET 请求。然而，Spring Security 将发送一个重定向（HTTP 302）回浏览器，指示协议更改。你可以在图 13-11 中突出显示的两行中看到这一点。第一行是 HTTP 302 重定向。第二行是响应的正文内容，其中你可以看到它正在请求我们的浏览器访问 `https://localhost....` 接下来不再是普通的 HTTP，而是 SSL。在 Wireshark 中，它被标记为 TCP。数据包的内容是加密的，密码无法再通过查看数据包来截获。  ![Image](img/9781430241553_Fig13-11.jpg)  ***图 13-11.** 所有提交的信息现在都通过 SSL 加密*    这结束了我们对传输层安全的讨论。你已经看到我们如何仅通过更改 Spring Security 配置就从 HTTP 切换到 HTTPS。    ### 本地化    Spring Security 支持异常消息的本地化。如果你的应用程序是为英语用户设计的，则无需执行任何操作，因为默认情况下所有安全消息都是英文的。    如果你需要支持其他语言环境，你应该知道所有异常消息都可以本地化，包括与认证失败和访问被拒绝（授权失败）相关的消息。面向开发人员或系统部署人员的异常和日志消息（包括不正确的属性、接口契约违规、使用不正确的构造函数、启动时验证、调试级别日志记录）不会被本地化，而是在 Spring Security 的代码中硬编码为英文。    资源包位于 `org/springframework/security/` 下，基本名称为 `messages.properties`，可以在我们的 `WebMvcContextConfiguration` 中进行配置（参见清单 13-24）。我们已经为我们的本地资源包（`messages.properties`）配置了一个 `org.springframework.context.MessageSource`；在这里，我们将扩展配置并传递第二个资源包，该资源包由包格式的位置（`org.springframework.security.messag`es）后跟基本名称标识，基本名称是不带国家或语言后缀且不带“properties”扩展名的文件名。    ***清单 13-24.** 添加用于本地化 Spring Security 消息的额外包*  `@Bean` `public MessageSource messageSource() {` `ResourceBundleMessageSource messageSource = new` ![Image](img/U002.jpg) `        ResourceBundleMessageSource();` `messageSource.setBasenames(new String[] { "messages",` ![Image](img/U002.jpg) `        "`**`org.springframework.security.messages`**`" });` `    messageSource.setUseCodeAsDefaultMessage(true);` `    return messageSource;` `}`  Spring Security 依赖 Spring 的本地化支持来查找适当的消息。为了使其工作，你必须确保传入请求的语言环境存储在 Spring 的 `org.springframework.context.i18n.LocaleContextHolder` 中。Spring MVC 的 `org.springframework.web.servlet.DispatcherServlet` 会自动为你的应用程序执行此操作，但由于 Spring Security 的过滤器在 `DispatcherServlet` 之前被调用，因此需要在调用 Spring Security 之前设置 `LocaleContextHolder` 以包含正确的语言环境。我们通过配置一个 Spring `javax.servlet.ServletContextListener` 实现，即 `org.springframework.web.context.request.RequestContextListener` 来实现这一点。此监听器将在任何过滤器被调用之前被调用。    `RequestContextListener` 在 `BookstoreWebApplicationInitializer` 中配置，如清单 13-25 所示。    ***清单 13-25:** 来自 BookstoreWebApplicationInitializer 的代码，配置 RequestContextListener*  `@Override` `public void onStartup(ServletContext servletContext) throws ServletException {` `    registerListener(servletContext);` `    registerDispatcherServlet(servletContext);` `    // 我们改用 JpaFlowExecutionListener，但为 Spring MVC 提供的页面启用它` `    registerSpringSecurityFilterChain(servletContext);` `    registerOpenEntityManagerInViewFilter(servletContext);` `}`  `private void registerListener(ServletContext servletContext) {` `AnnotationConfigWebApplicationContext rootContext =` ![Image](img/U002.jpg) `        createContext(configurationClasses);` `    servletContext.addListener(new ContextLoaderListener(rootContext));` `    servletContext.addListener(new RequestContextListener());` `}`  ### 基于角色的访问控制    基于角色的访问控制 (RBAC) 对于拥有大量用户且其日常工作中扮演的角色高度多样化的组织来说，是一种有益的访问控制模型。RBAC 是专门为支持这种结构而建模的。你在 RBAC 中的角色实际上与你在组织中的角色相当；很可能与开发有关。权限是授予具有该角色的人的权利。    虽然这种详细程度通常只对较大的组织有意义，但其基本实现并不十分困难。正如我们将看到的，我们可以轻松地扩展我们的领域模型，以最小的努力支持基本的 RBAC。    但为了澄清事实，对于像我们的书店这样简单的东西来说，这肯定是过度设计了。即使它成为一个受欢迎的商店，用户的多样性也会非常低；他们都将做几乎相同的事情。因此，我们不会真正从 RBAC 系统中受益。但我们将演示它，只是为了展示 Spring Security 的可能性。    我们将支持的模型归结为图 13-12 中所示的关系。  ![Image](img/9781430241553_Fig13-12.jpg)  ***图 13-12.** RBAC 模型*    一个用户（在我们的领域中称为“`Account`”）拥有一个或多个角色。一个角色可以拥有一个或多个权限。我们定义的角色将取决于我们的应用程序的重要性。角色可以被视为一组权限的全局化。    在我们的书店中，角色可能是“customer”或“administrator”，权限可能是“create order”或“add books”。权限通常比角色更细粒度。    我们决定在安全上下文中同时加载角色和权限。检查角色可能对粗粒度的检查有意义。例如；这个导航链接应该显示吗？这可能取决于给定用户是否拥有给定角色。但是，更深入地说，可能存在两个不同的角色授予对相同功能的访问权限。但在功能内部，只允许某些操作，具体取决于特定的权限。因此，在这里检查特定权限的存在对我们有益。正如我们将看到的，使用 Spring Security，我们可以轻松地检查两者。    例如，假设一家公司有一个页面，显示员工享有的各种福利。这可能包含有关医疗计划、餐券或团体促销的信息。只有内部员工才允许访问此信息；承包商和其他类型的外部员工不享有此福利，应被排除在访问此信息之外。    因此，我们可以选择首先对角色进行粗粒度检查。这将阻止承包商甚至看到此信息的链接。但是，在页面本身内部，我们希望区分普通员工（只能读取/查询信息）和管理员（能够修改信息）。在这里，我们将继续检查实际的权限。作为员工，我们只有读取额外福利的权限，而管理员（同时也是员工）将拥有额外的*权限*来修改信息。    整个故事对于 Spring Security 来说并不那么有趣。它只想知道一个“字符串”列表，你将使用它来检查是否存在。这个列表包含角色、权限还是外星人，并不重要。角色和权限的划分只是为了管理应用程序的人员，以便可以灵活地处理访问，而无需更改应用程序的代码。    角色或权限将被转换为 Spring Security 的 `GrantedAuthorities`。我们的领域模型有特定的实体来支持这一点。首先是熟悉的 `Account` 实体，如清单 13-26 所示。以粗体显示的是它与 Order 的多对多关联。`Role` 实体如清单 13-27 所示，它与 `Permission` 的多对多关联以粗体显示。最后，我们有 `Permission` 实体，如清单 13-28 所示。    ***清单 13-26.** Account，与 Role 具有多对多关联*  `package com.apress.prospringmvc.bookstore.domain;`  `import java.io.Serializable;`  `import javax.persistence.CascadeType;` `import javax.persistence.Embedded;` `import javax.persistence.Entity;` `import javax.persistence.GeneratedValue;` `import javax.persistence.GenerationType;` `import javax.persistence.Id;` `import javax.persistence.ManyToMany;` `import javax.validation.Valid;`  `import org.hibernate.validator.constraints.Email;` `import org.hibernate.validator.constraints.NotEmpty;`  `@Entity` `public class Account implements Serializable {`  `    @Id` `    @GeneratedValue(strategy = GenerationType.AUTO)` `    private Long id;`  `    private String firstName;` `    private String lastName;`  `    private Date dateOfBirth;`  `    @Embedded` `    @Valid` `    private Address address = new Address();`  `    @NotEmpty` `    @Email` `    private String emailAddress;` `    @NotEmpty` `    private String username;` `    @NotEmpty` `    private String password;`  **`    @ManyToMany(cascade = CascadeType.ALL)`** **`    private List<Role> roles = new ArrayList<Role>();`**  `    // 省略类实例变量的 getter 和 setter 方法` `}`  ***清单 13-27.** Role 实体，它与 Permission 具有多对多关联*  `package com.apress.prospringmvc.bookstore.domain;`  `import java.io.Serializable;` `import java.util.ArrayList;` `import java.util.List;`  `import javax.persistence.CascadeType;` `import javax.persistence.Entity;` `import javax.persistence.GeneratedValue;` `import javax.persistence.GenerationType;` `import javax.persistence.Id;` `import javax.persistence.ManyToMany;` `import javax.persistence.Table;` `import javax.persistence.UniqueConstraint;`  `@Entity` `@Table(uniqueConstraints = { @UniqueConstraint(columnNames = { "role" }) })` `public class Role implements Serializable {`  `    @Id` `    @GeneratedValue(strategy = GenerationType.AUTO)` `    private Long id;`  `    private String role;`  **`@ManyToMany(cascade = CascadeType.ALL)`** **`    private List<Permission> permissions = new ArrayList<Permission>();`**  `    Role() {` `        // 供 ORM 使用` `    }`  `    public Role(String role) {` `        this.role = role;` `    }`  `    // 省略类实例变量的 getter 和 setter 方法` `}`  ***清单 13-28.** Permission 实体*  `package com.apress.prospringmvc.bookstore.domain;`  `import java.io.Serializable;`  `import javax.persistence.Entity;` `import javax.persistence.GeneratedValue;` `import javax.persistence.GenerationType;` `import javax.persistence.Id;` `import javax.persistence.Table;` `import javax.persistence.UniqueConstraint;`  `@Entity` `@Table(uniqueConstraints = { @UniqueConstraint(columnNames = { "permission" }) })` `public class Permission implements Serializable {`  `    @Id` `    @GeneratedValue(strategy = GenerationType.AUTO)` `    private Long id;`  `    private String permission;`  `    Permission() {` `        // 供 ORM 使用` `    }`  `    public Permission(String permission) {` `        this.permission = permission;` `    }`  `    // 省略类实例变量的 getter 和 setter 方法` `}`  这个设置非常简单。一个 Account 拥有多个角色，每个角色拥有多个权限。正如我们所说：一个 `Account` 可以有多个角色，一个角色可以有多个权限。    最后，我们需要用这些角色和权限填充我们的安全主体。回想一下，我们创建了一个 Spring Security `UserDetailsService` 的自定义实现（`com.apress.prospringmvc.bookstore.web.security.BookstoreUserDetailsService`）。我们现在将扩展它以加载已授予权限的列表，如清单 13-29 所示。    ***清单 13-29.** 扩展 `BookstoreUserDetailsService` 功能，将角色和权限加载到已授予权限中*  `package com.apress.prospringmvc.bookstore.web.security;`  `import org.apache.commons.lang3.StringUtils;` `import org.springframework.beans.factory.annotation.Autowired;` `import org.springframework.security.core.GrantedAuthority;` `import org.springframework.security.core.authority.SimpleGrantedAuthority;` `import org.springframework.security.core.userdetails.UserDetails;` `import org.springframework.security.core.userdetails.UserDetailsService;` `import org.springframework.security.core.userdetails.UsernameNotFoundException;` `import org.springframework.stereotype.Component;`  `import com.apress.prospringmvc.bookstore.domain.Account;` `import com.apress.prospringmvc.bookstore.domain.Permission;` `import com.apress.prospringmvc.bookstore.domain.Role;`  `@Component` `public class BookstoreUserDetailsService implements UserDetailsService {`  `    @Autowired` `    private AccountService accountService;`  `    @Override` `    public UserDetails loadUserByUsername(String username) throws         UsernameNotFoundException {` `        if (StringUtils.isBlank(username)) {` `            throw new UsernameNotFoundException("Username was empty");` `        }`  `        Account account = accountService.getAccount(username);`  `        if (account == null) {` `            throw new UsernameNotFoundException("Username not found");` `        }`  `List<GrantedAuthority> grantedAuthorities = new` ![Image](img/U002.jpg) `            ArrayList<GrantedAuthority>();`  `        for (Role role : account.getRoles()) {` **`grantedAuthorities.add(new  `**![Image](img/U002.jpg) **`                  SimpleGrantedAuthority(role.getRole()));`** `            for (Permission permission : role.getPermissions()) {` **`grantedAuthorities.add(new`**![Image](img/U002.jpg) **`                     SimpleGrantedAuthority(permission.getPermission()));`** `            }` `        }` `return new BookstoreUserDetails(accountService.getAccount(username),` ![Image](img/U002.jpg) `               grantedAuthorities);` `    }` `}`  首先，我们检查用户名是否不为 null 或空白。如果是，我们抛出一个 `org.springframework.security.core.userdetails.UsernameNotFoundException`。接下来，我们检索给定用户名的 `Account`。当找不到匹配的 `Account` 时，我们再次抛出 `UsernameNotFoundException`。最后，我们将所有链接到 `Account` 的 `Role` 以及链接到每个 `Role` 的所有 `Permission` 合并，并将它们添加到类型为 `GrantedAuthority` 的列表中。我们将每个 `Role` 或每个 `Permission` 包装在一个 `SimpleGrantedAuthority` 中，它是接口 `GrantedAuthority` 的一个实现。该类只接受一个字符串作为构造函数值，并提供一个 getter 以便稍后检索它。你可以在此处看到这一点（为简洁起见，我们省略了 equals、hashCode 和 toString 实现）：  `package org.springframework.security.core.authority;`  `import org.springframework.security.core.GrantedAuthority;` `import org.springframework.security.core.SpringSecurityCoreVersion;` `import org.springframework.util.Assert;`  `public final class SimpleGrantedAuthority implements GrantedAuthority {`  `    public SimpleGrantedAuthority(String role) {` `Assert.hasText(role, "A granted authority textual representation is` ![Image](img/U002.jpg) `            required");` `        this.role = role;` `    }`  `    public String getAuthority() {` `        return role;` `    }` `}`  我们的主体（`BookstoreUserDetails`）现在填充了一个 `GrantedAuthority` 列表，其中每个项目代表一个角色或一个权限。在下一节中，我们将看到如何基于这些权限来限制访问。    ### 授权访问    到目前为止，你已经了解了如何通过使所有资源仅对经过认证的用户可访问（除非另有指定）来保护我们的应用程序。你了解了如何通过要求用户在登录页面上输入用户名和密码来识别我们的用户。我们还展示了如何配置 Spring Security 以便它可以认证给定的凭据。在上一节中，我们向你介绍了 RBAC，这导致填充了我们的 `com.apress.prospringmvc.bookstore.web.security.BookstoreUserDetails` 角色和权限，这些角色和权限与用户的 `Account` 相关联。    我们的安全设置现在几乎准备好了，因为所有的部分都已就位。剩下要做的唯一一件事就是基于我们现在在 `org.springframework.security.core.context.SecurityContext` 中为经过认证的用户拥有的信息来执行授权。或者更确切地说，我们需要根据与用户 `Account` 关联的角色和权限来指示允许用户使用哪些功能。    在以下部分中，我们将看到如何控制页面中的授权。我们将看到 Spring Security 自带了自己的标签库，该库提供了特殊的标签，可以根据某些角色或权限的存在，直接在 JSP 页面中禁用或启用功能。    #### 在页面中使用标签库    Spring Security 带有一个专用的标签库，你可以使用它来有条件地渲染某些页面组件。用例总是相同的：根据权限集确定是否应向当前登录的用户显示某个组件或页面的一部分。    为了使用这些标签，我们必须在页面中声明相应的标签库。我们通过添加一个额外的 JSP 声明来实现：  `<%@ taglib prefix="spring" uri="http://www.springframework.org/tags"%>.`  在以下部分中，我们将说明最重要的标签以及如何使用它们。    ##### authorize 标签    此标签用于根据一个或多个授权检查来确定是否应评估其内容。在我们的例子中，我们将以与 `flow` 安全属性或拦截 URL 的 `access` 属性完全相同的方式使用它。使用 SpEL 表达式，我们将验证当前经过认证的用户是否具有给定的角色，以便让内容被评估。    在第一个代码片段中，我们只会在经过认证的用户具有角色 `ROLE_AUTHOR` 时显示内容。在第二个片段中，只有当经过认证的用户具有角色 `PERM_ADD_BOOK` 时，才会显示内容。  `<sec:authorize access="hasRole('ROLE_AUTHOR')">` `…content…` `</sec:authorize>`  或者  `<sec:authorize access="hasRole('PERM_ADD_BOOK')">` `…content…` `</sec:authorize>`  为了说明这个重要标签的用法，我们创建了一个单独的页面 `manageBooks.jsp`（位于 `WEB-IN/view/secured/manageBooks`）。此页面将允许用户向系统添加新书和新类别。为了使用此功能，用户必须经过认证并具有角色 `ADMIN` 或 `AUTHOR`。但是，对于这两个功能（添加新类别和书籍），需要两个单独的权限。要添加书籍，你应该拥有 `PERM_ADD_BOOKS`，要添加新类别，你应该拥有 `PERM_ADD_CATEGORIES`。    为了演示此页面，我们在 `com.apress.prospringmvc.bookstore.domain.support.InitialDataSetup` 类中创建了两个新用户，如清单 13-30 所示。    ***清单 13-30:** 向我们的初始数据设置添加额外用户*  `private Permission permissionAddCategories = new Permission("PERM_ADD_`![Image](img/U002.jpg) `                                             CATEGORIES");` `private Permission permissionAddBooks = new Permission("PERM_ADD_BOOKS");` `private Permission permissionCreateOrders = new Permission("PERM_CREATE_`![Image](img/U002.jpg) `                                             ORDER");`  `private Role roleCustomer = new Role("ROLE_CUSTOMER");` `private Role roleAdmin = new Role("ROLE_ADMIN");` `private Role roleAuthor = new Role("ROLE_AUTHOR");`  `InitialDataSetup.this.johnDoe = new AccountBuilder() {` `    {` `        address("Brussels", "1000", "Nieuwstraat", "1", "A", "BE");` `        email("foo@test.com");` `        credentials("jd", "secret");` `        name("John", "Doe");` `roleWithPermissions(InitialDataSetup.this.roleCustomer,` ![Image](img/U002.jpg) `                            InitialDataSetup.this.permissionCreateOrders);` `    }` `}.build();`  `new AccountBuilder() {` `    {` `        address("Antwerp", "2000", "Meir", "1", "A", "BE");` `        email("bar@test.com");` `        credentials("admin", "secret");` `        name("Super", "User");` `        roleWithPermissions(InitialDataSetup.this.roleAdmin,`![Image](img/U002.jpg) `                            InitialDataSetup.this.permissionAddBooks,`![Image](img/U002.jpg) `                            InitialDataSetup.this.permissionAddCategories);` `    }` `}.build();`  `new AccountBuilder() {` `    {` `        address("Gent", "9000", "Abdijlaan", "1", "A", "BE");` `        email("baz@test.com");` `        credentials("author", "secret");` `        name("Some", "Author");` `        roleWithPermissions(InitialDataSetup.this.roleAuthor,`![Image](img/U002.jpg) `                            InitialDataSetup.this.permissionAddBooks);` `    }` `}.build();`  总结一下，我们有用户 `admin`，角色为 `ROLE_ADMIN`，权限为 `PERM_ADD_BOOKS` 和 `PERM_ADD_CATEGORIES`。接下来，我们有用户 `author`，角色为 `ROLE_AUTHOR`，只有一个权限 `PERM_ADD_BOOKS`。`author` 用户只能向现有类别添加书籍，但不能创建新类别（只有 `admin` 用户可以）。    接下来，在清单 13-31 中，我们在导航菜单中添加了一个新链接，并使用 `authorize` 标签应用了正确的安全设置。请注意，我们允许 `ROLE_ADMIN` 和 `ROLE_AUTHOR` 访问该功能。两个角色的用户都应该能够管理书籍，但在页面内部，我们将根据他们的权限进一步限制访问。我们接下来会看到这一点。    ***清单 13-31.** 在导航栏中使用 authorize 标签，仅向具有角色 `ROLE_ADMIN` 或 `ROLE_AUTHOR` 的用户显示“管理书籍”链接*  **`<sec:authorize access="hasRole('ROLE_ADMIN') or hasRole('ROLE_AUTHOR') ">`** `    <li>` `<spring:url value="/secured/manageBooks/manageBooks.htm"` ![Image](img/U002.jpg) `                    var="manageBooks" />` `        <a href="${manageBooks}">` `            <spring:message code="nav.manageBooks"/>` `        </a>` `    </li>` **`</sec:authorize>`**  很明显，普通客户（比如我们的用户 `jd`）将根本无法看到“管理书籍”链接。只有作者和管理员才能看到。    接下来，我们将修改 `/WEB-INF/view/secured/manageBooks/manageBooks.jsp` 页面中的管理书籍功能（参见清单 13-32）。在这里，我们将根据特定权限进一步控制授权。    ***清单 13-32.** 更细粒度的权限检查*  `<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core"%>` `<%@ taglib prefix="spring" uri="http://www.springframework.org/tags" %>` `<%@ taglib prefix="form" uri="http://www.springframework.org/tags/form" %>` `<%@ taglib prefix="fn" uri="http://java.sun.com/jsp/jstl/functions" %>` `<%@ taglib prefix="tiles" uri="http://tiles.apache.org/tags-tiles" %>` `<%@ taglib prefix="sec" uri="http://www.springframework.org/security/tags" %>`  `<c:if test="${not empty actionSuccess}">` `    <div id="actionSuccess" style="color: green; display:` `             block; margin-bottom: 10px;">` `        <table>` `            <tr>` `                <td>` `                    <ul style="list-style-type: disc">` `                        <li>` `                            <h3>` `                                <spring:message code="label.page.managebooks.`![Image](img/U002.jpg) `                                                     ${actionSuccess}.added"/>` `                            </h3>` `                        </li>` `                    </ul>` `                </td>` `            </tr>` `        </table>` `    </div>` `</c:if>`  **`<sec:authorize access="hasRole('PERM_ADD_CATEGORIES')">`** `    <spring:url value="/secured/addCategory.htm" var="addCategory"/>` `    <form:form  action="${addCategory}" commandName="manageCategoryForm">` `        <table style="width: 100%">` `            <tr>` `                <td width="30%">` `                    <spring:message code="label.page.managebooks.`![Image](img/U002.jpg) `                                          category.add"/>` `                </td>` `                <td>` `                    <input type="text" name="category" path="category"/>` `                    <form:errors path="category" cssClass="error"/>` `                    <form:errors/>` `                </td>` `            </tr>` `        </table>` `        <div align="right" style="margin-bottom: 20px; margin-top: 10px" >` `            <button type="submit" id="add">` `                <spring:message code="label.page.managebooks.add.category"/>` `            </button>` `            <button type="reset" id="clear">` `                <spring:message code="label.page.managebooks.clear.category"/>` `            </button>` `        </div>` `    </form:form>` `</sec:authorize>`  **`<sec:authorize access="hasRole('PERM_ADD_BOOKS')">`** `    <spring:url value="/secured/addBooks.htm" var="addBook"/>` `    <form:form modelAttribute="manageBookForm" action="${addBook}"` `               method="POST">` `        <table style="width: 100%">` `            <tr>` `                <td width="30%">` `                    <spring:message code="label.page.managebooks.`![Image](img/U002.jpg) `                                          book.category"/>` `                </td>` `                <td>` `                    <form:select path="category" items="${manageBookForm.`![Image](img/U002.jpg) `selectableCategories}" itemLabel="name"` ![Image](img/U002.jpg) `                                 itemValue="id"/>` `                    <form:errors path="category" cssClass="error"/>` `                </td>` `            </tr>`  `            <%-- 为简洁起见，省略其余行 --%>`  `        </table>` `        <div align="right" style="margin-bottom: 20px; margin-top: 10px" >` `            <button type="submit" id="add">` `                <spring:message code="label.page.managebooks.book.add"/>` `            </button>` `            <button type="reset" id="clear">` `                <spring:message code="label.page.managebooks.book.clear"/>` `            </button>` `        </div>` `    </form:form>` `</sec:authorize>`  `<script>` `    dojo.addOnLoad(function(){` `        function fadeIt() {` `            if(dojo.byId("actionSuccess")){` `                dojo.style("actionSuccess", "opacity", "1");` `                var fadeOutArgs = {node: "actionSuccess", duration: 15000};` `                dojo.fadeOut(fadeOutArgs).play();` `            }` `        }` `    fadeIt();` `    });` `</script>`  第一个 `if` 块和最后一个 JavaScript 块用于在成功添加 `Category` 或 `Book` 时显示消息。此 JavaScript 代码与我们用于在用户登录或下订单时显示消息的代码相同。在后一种情况下，用户被重定向到 `ordersOverview` 页面，并弹出一条消息，其中包含用户名和新下订单的订单 ID。在所有情况下，JavaScript 都会让显示的消息慢慢消失。    第一个 `authorize` 标签（以粗体显示）确保你必须拥有添加类别的权限（`PERM_ADD_CATEGORIES`）。在我们的例子中，作者将无法执行此操作。此“添加类别”部分将不会向具有适当授权的角色的用户显示；在我们的例子中，用户 author 将看不到此部分。    第二个 `authorize` 标签要求拥有添加书籍的权限（`PERM_ADD_BOOKS`）。作者和管理员都能够执行此操作。为了说明这一点，当我们使用用户“jd”登录时，管理书籍的菜单选项甚至不会显示，如图 13-13 所示。  ![Image](img/9781430241553_Fig13-13.jpg)  ***图 13-13.** 使用用户“jd”登录不会显示“管理书籍”菜单选项。*    当我们以作者身份登录时，会显示“管理书籍”菜单选项。当我们单击它时，我们会看到我们只能添加书籍，如图 13-14 所示。  ![Image](img/9781430241553_Fig13-14.jpg)  ***图 13-14.** 使用用户“author”登录会显示“管理书籍”菜单选项，但只允许我们添加书籍。*    最后，当我们以管理员身份登录时，“管理书籍”链接也会显示在导航栏中，但现在我们被允许添加类别和书籍，如图 13-15 所示。  ![Image](img/9781430241553_Fig13-15.jpg)  ***图 13-15.** 使用用户“admin”登录会显示“管理书籍”菜单选项，并允许我们添加书籍和类别。*    在我们结束本节之前，还有最后一件事想展示。在之前的“注销”部分中，我们说过我们还需要进行一项额外的更改。菜单中的“注销”按钮只应在用户已登录时显示，而“登录”按钮应隐藏（它只应在用户尚未认证时显示）。既然我们已经了解了 authorize 标签，这只是在 `header.jsp` 中添加另一个标签的问题。在清单 13-33 中，我们展示了 `header.jsp` 的一个摘录，其中将此标签应用于“注销”和“登录”菜单链接（以粗体显示）。    ***清单 13-33.** 在 WEB-INF/templates/header.jsp 中为登录和注销链接添加授权标签*  `<ul>`  `    <%-- 为简洁起见，省略其他导航栏链接 --%>`  **`    <sec:authorize access="! authenticated">`** `        <li>` `            <spring:url value="/public/authentication/login.htm" var="login"             />` `            <a href="${login}">` `                <spring:message code="nav.login"/>` `            </a>` `        </li>` **`    </sec:authorize>`** `    <sec:authorize access="hasRole('ROLE_ADMIN') or hasRole('ROLE_AUTHOR') ">` `        <li>` `<spring:url value="/secured/manageBooks/manageBooks.htm"` ![Image](img/U002.jpg) `                        var="manageBooks" />` `            <a href="${manageBooks}">` `                <spring:message code="nav.manageBooks"/>` `            </a>` `        </li>` `    </sec:authorize>` **`    <sec:authorize access="fullyAuthenticated">`** `        <li>` `            <spring:url value="/logout" var="logout" />` `            <a href="${logout}">` `                <spring:message code="nav.logout"/>` `            </a>` `        </li>` **`    </sec:authorize>`** `<ul>`  第一个以粗体显示的 `authorize` 标签仅在我们尚未认证时渲染其内容（登录链接）。第二个以粗体显示的 authorize 标签仅在我们已授权时渲染其内容（注销链接）。    ##### authentication 标签    此标签允许访问存储在安全上下文中的当前 `org.springframework.security.core.Authentication` 对象。它可以直接在 JSP 中渲染对象的任何属性。例如，你可以使用它来显示来自主体的信息，在我们的例子中是 `BookstoreUserDetails` 对象（参见清单 13-34）。    ***清单 13-34.** 渲染用户名的 authentication 标签*  `<sec:authentication property="principal.username"/>`  这将显示当前登录用户的用户名。    我们将此代码应用于我们的主页，位于 `WEB-INF/view/public/main.jsp`，如前面的清单 13-12 所示。我们已经配置 Spring Security 在登录成功时将我们转发到主页。我们还添加了一个特殊参数（`authenticationOk`）来指示这一点。主页将显示一条消息，表明登录成功，包括用户名。我们从清单 13-12 中提取了该片段，并在清单 13-35 中为您重复。在这里，我们使用 `var` 和 `scope` 属性将结果（来自 `property` 属性指示的属性）与给定的变量名（`username`）绑定到指示的作用域（Servlet 请求作用域）    ***清单 13-35.** 将用户名存储在请求作用域中的 authentication 标签*  `<h3>` **`    <sec:authentication property="principal.username" var="username"`**![Image](img/U002.jpg) **`                         scope="request"/>`**  `<spring:message code="label.page.main.authentication.ok"` ![Image](img/U002.jpg) `                     arguments="`**`${username}`**`" />` `</h3>`  你可以在图 13-16 中看到结果，我们以用户 `jd` 身份登录。底部的 JavaScript 用于让消息慢慢消失，就像我们在其他地方显示通知消息时一样。  ![Image](img/9781430241553_Fig13-16.jpg)  ***图 13-16.** 登录后显示的消息*    #### 在代码中使用注解    正如我们将看到的，仅使用授权标签保护页面是不够的。如果用户知道或猜到直接链接，隐藏链接（这是授权标签的主要任务）将无济于事。在这种情况下，用户仍然可以访问（假设你没有为该页面放置带有适当授权检查的特定 `intercept-url`）。    在本节中，我们将看到你也可以使用注解来保护你的应用程序控制器方法和后端服务。这样，你可以确保如果用户绕过了前端安全，用户将在下一级被阻止。在后端服务中应用安全措施，如果你有不同的前端层使用相同的后端，也很有用。    到目前为止，我们已经看到了三个可以放置特定授权检查点的地方：    > *   在请求的资源上，使用 `intercept-url` > *   在你的流程上 > *   对于更细粒度的方法，在页面本身中使用 authorize 标签    这给我们留下了一个空白。考虑到我们最后的示例，我们在 `manageBooks.jsp` 页面上添加了特定的权限检查。只有拥有 `PERM_ADD_BOOKS` 权限的用户才能添加书籍，只有拥有 `PERM_ADD_CATEGORIES` 权限的用户才能添加类别。但是，如果具有作者角色（不能添加类别）的用户模拟一个添加新类别的 HTTP POST 请求，会发生什么？    这仍然是我们系统中的一个潜在弱点。虽然页面上的授权标签只隐藏了一个组件，但监听请求的控制器仍然存在。因此，任何知道正确 URL 和请求参数的人仍然可以直接调用控制器。    除了为每个页面添加具有细粒度授权控制（指定角色和/或权限）的 `intercept-url` 之外，你还可以使用方法特定的注解，如清单 13-36 所示。    ***清单 13-36.** 在应用程序控制器中使用方法安全*  `@RequestMapping("secured/addCategory.htm")` **`@PreAuthorize("hasRole('PERM_ADD_CATEGORIES')")`** `public ModelAndView addCategory(`  通过在控制器请求映射上添加 `org.springframework.security.access.prepost.PreAuthorize` 注解来添加书籍，我们实现了一个额外的检查。自己试试——将 `manageBooks.jsp` 中的 `<sec:authorize access="hasRole('PERM_ADD_CATEGORIES')">` 注释掉，并使用用户 `author` 登录。因为标签被注释掉了，所以添加类别的组件会显示出来。如果你尝试添加一个，你会得到著名的“403 访问被拒绝”页面。    在 Spring Security 处理这些注解之前，你需要启用方法安全。我们在 `src/main/resources/spring-security.xml` 中执行了此操作，如清单 13-37 所示。    ***清单 13-37.** 启用方法级安全*  `<security:global-method-security pre-post-annotations="enabled"/>`  接下来，我们还将保护 `addBooks` 方法，就像我们对 `addCategory` 方法所做的那样，如清单 13-38 所示。    ***清单 13-38.** 在应用程序控制器中使用方法安全*  `@RequestMapping("secured/addBooks.htm")` **`@PreAuthorize("hasRole('PERM_ADD_BOOKS')")`** `public ModelAndView addBooks(`  为了使整个链安全，你也可以在你的服务层上添加这些注解。一旦你这样做了，就没有人能够绕过你的安全并调用当前主体不允许的函数。    最后，我们想就安全异常说几句。通常，你的用户在应用程序的正常导航过程中不应遇到此类异常。我们使用授权标签屏蔽了页面中的所有链接；换句话说，如果用户无权访问某个链接（或按钮），它根本就不会显示。但是，如果用户开始摆弄 URL，仍然有可能访问到被禁止的 URL。在这种情况下，用户将遇到应用程序控制器或后端上的安全注解，并将显示 403 访问被拒绝消息。如果你想自定义此行为，你有两个选择：    > *   你可以在 web.xml 中使用 JEE Servlet `<error-page>`，在出现 `<error-code>`（在本例中为 403）时转发到某个页面。 > *   你可以使用 Spring Security 的 `org.springframework.security.web.access.ExceptionTranslationFilter`，它可以处理 `org.springframework.security.access.AccessDeniedException` 和 `org.springframework.security.core.AuthenticationException`。其主要目的是在用户尚未认证时将其转发到登录页面，或者在当前用户权限不足时将其转发到预定义的错误页面。为了方便起见，此过滤器已通过使用 Spring Security 命名空间配置（<`security:http`>）注册。实际上，`http` 元素的直接属性之一是 `access-denied-page="…"`，它允许你指定在发生此类安全相关异常时转发到的页面。当整个 Spring Security 配置构建时，此属性将直接提供给 `ExceptionTranslationFilter`。    ### 总结    总的来说，安全是一个非常复杂和广泛的领域。但是，在本章中，我们已经向你展示了，对于一个普通的 Web 应用程序来说，基本的安全措施不必很复杂。相反，我们展示了如何使用一小段 XML 和过滤器配置来保护你的页面，以便只有那些显式列出的页面才能被访问。    后来，我们展示了使用自定义 `UserDetailsService` 进行自定义也并不复杂，但它允许你将用户存储适配到你可能拥有的任何现有数据库结构。最后，我们展示了如何基于授予主体的权限在你的页面、流程和控制器中执行授权。    总之，我们希望你已经了解到，只需付出最少的努力，你的应用程序就可以得到保护。有了像 Spring Security 这样出色的工具，再也没有理由不在你的应用程序中构建基本的安全措施了。    编写本章的目的是让你了解各种可能性，并帮助你快速入门。你的环境可能有特定的安全要求。在这种情况下，我们希望本章能激发你的兴趣，去阅读更多关于 Spring Security 及其如何满足特定需求的详细资料。    ## 附录 A    ## Cloud Foundry：部署到云端    在本附录中，我们将讨论如何让我们的应用程序在与我们迄今为止运行的环境不同的环境中运行。我们将不再运行在任何普通的旧 Tomcat 实例上，而是在*云端*运行它。但在我们开始之前，我们将先简单谈谈云的含义。    ### 云计算    你可能无法避免在专业媒体上看到云计算的冲击。但你可能仍然不清楚云计算涉及什么。我们将尝试简要概述云计算是什么。    很难给出云计算的单一定义，因为它对不同的人意味着不同的东西。一个更广泛的定义是这样的：    > *云计算是一种分布式计算模型，由三层组成——基础设施、平台和服务——它能够实现对可配置计算资源（例如，网络、服务器、存储、应用程序和服务）的共享池进行无处不在、便捷、按需的网络访问。*    以 Amazon S3^(1) 为例：Amazon 允许你使用基于 Web 的 API 将数据存储在其服务器上。你根据上传或下载该数据所使用的带宽向 Amazon 付费。在任何时候，你都不需要拥有自己的存储基础设施。    目前使用几种云服务模型：    *   *基础设施即服务 (IaaS)*：将计算基础设施作为服务交付。此类基础设施的示例包括虚拟计算机、存储、网络基础设施等。这更像是系统管理员而非开发人员的领域。Amazon 和 Rackspace 是两个最知名的 IaaS 提供商。 *   *平台即服务 (PaaS)*：提供超越基础设施的服务。这里，重点是开发环境、应用程序服务和应用程序部署机制。对实际基础设施本身的控制主要由服务提供商处理。PaaS 方法的一个很好的例子是 Google App Engine 和 Cloud Foundry。 *   *软件即服务 (SaaS)*：将完整的解决方案作为服务提供，消除了在客户自己的计算机上安装和运行应用程序的需要，并简化了维护和支持。将此与传统的软件交付模型进行对比，在传统模型中，最终用户在每个桌面上安装软件，自己管理升级等。在这种模型中，用户只需访问一个网站并开始在线使用应用程序。Google Docs 是 SaaS 的一个很好的例子。    __________________    ¹ [`http://aws.amazon.com/es/s3/`](http://aws.amazon.com/es/s3/)    ### Cloud Foundry    在本附录中，我们将使用 Cloud Foundry 作为我们选择的 PaaS 来部署我们的书店应用程序。但是什么是 Cloud Foundry，我们为什么要使用它？    Cloud Foundry 这样描述自己：    > *[Cloud Foundry 是] 一个开放的平台即服务，提供云、开发者框架和应用程序服务的选择。由 VMware 发起，得到广泛的行业支持，Cloud Foundry 使构建、测试、部署和扩展应用程序更快、更容易。它是一个开源项目，可通过各种私有云发行版和公共云实例获得，包括 CloudFoundry.com。^(2)*    这意味着什么？这意味着 Cloud Foundry 不是一个专有的、特定于供应商的 PaaS，而是一个真正开放的平台，由多个个人和组织共同开发。例如，ActiveState 提供 Stackato，^(3) 它是 Cloud Foundry 的扩展，提供 Python、PHP 和 Perl 支持。Ubuntu 也正在其 Ubuntu Cloud^(4) 旗帜下致力于支持 Cloud Foundry。    这也意味着我们必须更明确地定义一些事情：    *   *Cloud Foundry* 是开源项目的名称。你可以在 github 上找到它的代码；^(5) 其网站位于 [`www.cloudfoundry.org`](http://www.cloudfoundry.org)。 *   *Cloudfoundry.com* 是 VMWare 托管的 Cloud Foundry 实例，允许你将应用程序部署在 VMWare 拥有的基础设施上。    在本附录的过程中，我们不区分 Cloud Foundry 平台和 CloudFoundry.com 托管服务。    __________________    ² [`http://www.cloudfoundry.com`](http://www.cloudfoundry.com)    ³ [`http://www.activestate.com/stackato`](http://www.activestate.com/stackato)    ⁴ [`http://cloud.ubuntu.com`](http://cloud.ubuntu.com)    ⁵ [`https://github.com/cloudfoundry/`](https://github.com/cloudfoundry/)    既然我们已经了解了 Cloud Foundry 是什么，为什么我们作为 Java/Spring 开发者要关心它？为什么我们应该使用它而不是任何其他 PaaS？原因之一：Cloud Foundry 是一个开放的、多供应商的平台，这一事实非常好，因为它帮助我们避免供应商锁定的危险和相关成本。    另一个原因：它还支持多种语言，包括 Ruby、Java、Scala 和 NodeJs；并且它提供许多服务，从关系型存储（如 MySQL 和 PostgreSQL）到队列（如 RabbitMQ），再到 NoSQL 存储（如 MongoDB 和 Redis）。另一个很大的优势是它与 Spring 集成得非常好。这些因素结合在一起，使 Cloud Foundry 成为一个非常有趣的平台。    CloudFoundry.com，即 VMWare 托管的 Cloud Foundry 版本，在撰写本文时仍处于测试阶段。尽管如此，你可以通过访问 [`https://my.cloudfoundry.com/signup`](https://my.cloudfoundry.com/signup) 并输入电子邮件地址来注册。阅读并同意服务条款后，你可以单击“请求邀请”按钮。你将收到一封确认注册请求的电子邮件。稍后（可能需要几个小时甚至一天），你将收到一封电子邮件，其中包含实际部署到 CloudFoundry.com 所需的凭据。注册所需做的就是这些。    ### 部署我们的应用程序    在本节中，我们将描述如何将示例应用程序部署到 Cloud Foundry。我们将详细描述每个步骤，并解释我们必须进行的修改，以使一切按预期工作。如果你想自己执行此处描述的操作，我们假设你已经阅读了关于如何设置环境的初始章节。我们还假设你拥有 STS 或安装了 STS 插件的 Eclipse 实例。    #### 安装 Cloud Foundry 插件    尽管 Cloud Foundry 提供了几种工具来部署你的应用程序，但当你初次接触时，最快上手的方法是使用 Eclipse 插件。另一种方法是使用 VMC 命令行应用程序，它作为 Ruby gem 实现。高级用户无法避免使用 VMC 命令行；但就本附录而言，我们将使用 Eclipse 插件。    ![Image](img/square.jpg) **注意** RubyGems^(6) 是 Ruby 的包管理器。它可以与 Ubuntu 的 apt-get 或 Red Hat 的 yum 相媲美。*gem* 是一个打包的 Ruby 应用程序或库。你可以在 [`http://start.cloudfoundry.com/tools/vmc/installing-vmc.html`](http://start.cloudfoundry.com/tools/vmc/installing-vmc.html) 找到有关 VMC 安装的更多信息。如果你有 Ubuntu，你也可以直接使用 `apt-get install cloudfoundry-client` 安装命令行客户端。    我们将从安装 Cloud Foundry Eclipse 插件开始。转到 STS 插件仪表板（可以在菜单 ![Image](img/U003.jpg) 帮助 ![Image](img/U003.jpg) 仪表板下找到），单击窗口底部的“扩展”选项卡，然后在搜索栏中搜索 *cloud foundry*。选中找到的扩展，然后单击右下角的“安装”按钮，如图 A-1 所示。    __________________    ⁶ [`http://rubygems.org`](http://rubygems.org)  ![Image](img/9781430241553_App-01.jpg)  ***图 A-1.** 查找 Cloud Foundry 插件*    你将看到正常的插件安装窗口，要求你接受或拒绝许可协议。单击“下一步”和“确定”按钮，直到出现要求你重新启动 Eclipse 的对话框，然后选择“立即重新启动”以重新启动它。    既然我们已经安装了插件，我们可以继续下一步：将我们的应用程序部署到 Cloud Foundry。    #### 进行一些调整    Spring 和 Cloud Foundry 背后的团队一直致力于使在 Cloud Foundry 上部署 Spring 应用程序变得非常容易。一个明确的目标是，在大多数情况下，无需更改一行配置或代码即可部署 Spring 应用程序。    不幸的是，这对我们的应用程序来说并非完全正确，因为如果不进行一些更改，它将无法部署。主要原因是，在撰写本文时，Cloud Foundry 仅提供 Tomcat 6 实例（对 Tomcat 7 的支持已在路线图中）。另一个驱动更改的因素是 Cloud Foundry 中设置 SSL 的方式，这对我们配置 Spring Security 的方式有一些影响。    ##### 切换到 Tomcat 6    Tomcat 6 是一个 Java EE5 Servlet 容器，因此它不实现 Servlet 3 API。我们到目前为止使用的本地服务器实例是 Tomcat 7，它符合 Java EE 6。我们的示例应用程序明确依赖于 Java EE 6——特别是 Servlet 3 API。    到目前为止，我们一直在 `BookstoreWebApplicationInitializer` 中引导 Spring 应用程序上下文，它是 `org.springframework.web.WebApplicationInitializer` 的一个实现。`WebApplicationInitializer` 机制使我们能够长时间不使用 `web.xml` 文件；但是，它依赖于 Servlet 3 实现才能工作。    由于 Cloud Foundry 使用 Tomcat 6，我们必须回退到 Servlet 2.5 环境，并且需要创建一个 `web.xml` 文件，在其中重新创建我们在 `BookstoreWebApplicationInitializer` 中所做的事情。为此，我们需要一个 `ApplicationContext`、一个 `DispatcherServlet`、一个 `OpenEntityManagerInViewFilter` 以及配置 Spring Security 的过滤器。    通过添加 `ContextLoaderListener`，我们可以确保 `ApplicationContext` 将被启动（参见清单 A-1）。    ***清单 A-1.** 在 web.xml 文件中添加 ContextLoaderListener*  `<!—` `    tells the ContextLoaderListener where to look for applicationContext` `    xml files` `-->` `<context-param>` `    <param-name>contextConfigLocation</param-name>` `    <param-value>classpath:/spring/context.xml</param-value>` `</context-param>`  `<!-- the Listener will be called by the servlet container,` `     which will then boot a Spring ApplicationContext for us -->` `<listener>` `    <listener-class>` `        org.springframework.web.context.ContextLoaderListener` `    </listener-class>` `</listener>`  我们还需要通过使用 servlet 标签来声明一个 `DispatcherServlet`，如清单 A-2 所示。    ***清单 A-2.** 在 web.xml 文件中添加 DispatcherServlet*  `<servlet>` `  <servlet-name>dispatcher</servlet-name>` `  <servlet-class>` `    org.springframework.web.servlet.DispatcherServlet` `  </servlet-class>` `  <init-param>` `    <param-name>contextClass</param-name>` `    <!-- note that we’re specifying the type of ApplicationContext here.` `         We’re using an AnnotationConfigWebApplicationContext and configure it` `         to use the classes we specified in  contextConfigLocation as` `         configuration classes.` `      -->` `    <param-value>` `      org.springframework.web.context.support.` `AnnotationConfigWebApplicationContext` `    </param-value>` `  </init-param>` `  <init-param>` `    <param-name>contextConfigLocation</param-name>` `      <param-value>` `      com.apress.prospringmvc.bookstore.web.config.WebflowContextConfiguration` `      com.apress.prospringmvc.bookstore.web.config.WebMvcContextConfiguration` `      </param-value>` `  </init-param>` `  <load-on-startup>1</load-on-startup>` `</servlet>`  `<servlet-mapping>` `    <servlet-name>dispatcher</servlet-name>` `    <url-pattern>/</url-pattern>` `</servlet-mapping>`  我们正在创建两个应用程序上下文，将后端配置（例如，数据源、服务、存储库等）与前端配置（例如，Spring MVC、Spring Web Flow、@Controllers 等）分开。    `ContextLoaderListener` 将负责引导后端应用程序上下文。`DispatcherServlet` 将引导其自己的前端上下文。当 `DispatcherServlet` 引导其自己的上下文时，它将找到我们使用 `ContextLoaderListener` 引导的后端 `ApplicationContext`。它将此上下文设置为其自己的 `application context` 的父上下文。我们现在有两个应用程序上下文而不是一个，这一事实对我们没有实际影响。你只需要知道根上下文（由 `ContextLoaderListener` 引导）将无法访问由 dispatcher servlet 应用程序上下文引导的 bean，因为子上下文可以访问父上下文中的 bean，但反之则不行。    我们现在也使用两种类型的应用程序上下文类。对于后端，我们使用 `XmlWebApplicationContext`，它将从标准的 XML 应用程序上下文文件启动。`XmlWebApplicationContext` 的使用没有在 `web.xml` 中明确提及，因为 `ContextLoaderListener` 默认使用此类。在该上下文文件中，我们导入 `spring-security.xml` 并使用 `<context:annotation-config/>` 和 `<context:component-scan/>` 加载带有 @Configuration 注解的类。    然而，对于 `DispatcherServlet`，我们直接使用 `AnnotationConfigWebApplicationContext`，并为其提供前端的带有 @Configuration 注解的配置类。    你可能想知道为什么我们没有在两种情况下都使用 `AnnotationConfigWebApplicationContext`。例如，我们可以使用 @ImportResource 注解从 @Configuration 类中加载 `spring-security.xml`，就像我们在 `WebflowContextConfiguration` 中使用 `webflow-config.xml` 一样：  `@ImportResource("classpath:/spring/webflow-config.xml")`  原因：在部署期间，Cloud Foundry 将检查我们的 `web.xml`，并在我们的 `contextConfigLocation` 文件末尾添加其自己的 Spring 上下文 XML 文件，以供 `ContextLoaderListener` 加载。Cloud Foundry Spring 配置将执行一些魔法，例如自动将我们的数据源替换为内部的 Cloud Foundry 数据源，我们稍后将讨论这一点。问题是，当使用 `AnnotationConfigWebApplicationContext` 时，我们不能在 `contextConfigLocation` 中混合使用注解和 XML 文件。因此，当为根上下文使用 `AnnotationConfigWebApplicationContext` 时，我们的 @Configuration 类将被加载，但 Spring XML 文件（稍后由 Cloud Foundry 添加）将被忽略。希望这个限制将来会消失，这样我们就可以使用我们想要的任何类型的应用程序上下文实现。    最后，我们需要配置过滤器，如清单 A-3 所示。    ***清单 A-3.** 向 web.xml 文件添加过滤器*  `<filter>` `    <filter-name>openEntityManagerInViewFilter</filter-name>` `    <filter-class>` `        org.springframework.orm.jpa.support.OpenEntityManagerInViewFilter` `    </filter-class>` `</filter>`  `<filter-mapping>` `    <filter-name>openEntityManagerInViewFilter</filter-name>` `    <url-pattern>/*</url-pattern>` `</filter-mapping>`  `<filter>` `    <filter-name>springSecurityFilterChain</filter-name>` `    <filter-class>` `        org.springframework.web.filter.DelegatingFilterProxy` `    </filter-class>` `</filter>`  `<filter-mapping>` `    <filter-name>springSecurityFilterChain</filter-name>` `    <url-pattern>/*</url-pattern>` `</filter-mapping>`  我们可以在清单 A-4 中看到我们在 `contextConfigLocation` 中指定的 `spring/context.xml`。    ***清单 A-4.** context.xml 文件*  `<?xml version="1.0" encoding="UTF-8"?>` `<beans`   `       xsi:schemaLocation="` `         http://www.springframework.org/schema/beans` `         http://www.springframework.org/schema/beans/spring-beans-3.1.xsd` `         http://www.springframework.org/schema/context` `         http://www.springframework.org/schema/context/spring-context-3.1.xsd">`  `    <context:annotation-config/>` `    <context:component-scan  ` `        base-package="com.apress.prospringmvc.bookstore.config,` `        com.apress.prospringmvc.bookstore.web.security"/>` `    <import resource="classpath:/spring/spring-security.xml"/>` `</beans>`  在此上下文文件中，我们告诉 Spring 启用注解配置，并在 `com.apress.prospringmvc.bookstore.config` 和 `com.apress.prospringmvc.bookstore.web.security` 包中查找组件。因为 @Configuration 类也是 @Components，所以它们也会被拾取。    我们现在已经完成了为在 Tomcat 6 下运行而准备应用程序所需的所有更改。接下来，我们将看看 Spring Security 所需的更改。    ![Image](img/square.jpg) **注意**  当你阅读本文时，刚才讨论的更改很可能不再需要。对 Tomcat 7 的支持已在路线图中，并且在你阅读本文时可能已经实现。这也说明了在 PaaS 上运行的一个缺点：你无法控制底层基础设施。例如，如果你现在就需要在 Tomcat 7 上运行，该怎么办？    ##### Spring Security 配置    我们还没有完成更改。由于在第 13 章中启用了 Spring Security 中的 SSL，Spring Security 现在会在检测到我们通过 HTTP 连接时，将我们重定向到 HTTPS 连接。Cloud Foundry 支持 SSL，但在我们的应用程序前面有一个代理。此代理将终止 SSL 连接。但是，代理和运行我们应用程序的 Cloud Foundry Tomcat 实例之间的连接是普通的 HTTP。图 A-2 有点简化，但它足够接近从开发者的角度说明情况。  ![Image](img/9781430241553_App-02.jpg)  ***图 A-2.** 代理后面的应用程序*    在到达我们的应用程序之前，代理会添加一些标头，这些标头是处理 SSL 连接的结果。它将告诉我们使用了哪个密码，以及如果执行双向 SSL，则客户端证书。如果我们只使用“普通”SSL 而没有客户端证书，最后一个标头也会存在，但其值将为“无值”。    然而，Spring Security 通过调用 `(Http)ServletRequest` 上的 `isSecure()` 来检测 SSL 连接。这将返回 `false`，因为代理和应用程序之间的连接类型是 HTTP。然后 Spring Security 将向浏览器发送一个重定向，要求切换到 SSL，这将导致无限循环。    解决方法是使用自定义过滤器包装 `(Http)ServletRequest`。我们将重写 `isSecure()` 方法，以便在考虑连接是否安全时也检查 HTTP 标头。如果原始实现决定连接不安全，那么我们将检查 Cloud Foundry 代理添加的这些特定 SSL 标头是否存在，然后做出最终决定。清单 A-5 显示了过滤器代码，它除了使用包装的 `(Http)ServletRequest` 调用 `chain.doFilter` 之外，什么也不做。对我们来说更有趣的部分是包含的 `CloudFoundryHttpServletRequestWrapper` 静态类，它充当我们的包装器。    它首先会调用超类的 `isSecure()`。如果默认行为表明我们的请求是安全的，那么我们将保持一切不变并原样返回值（`true`）。当超类实现返回 `false` 时，我们尝试从 `HttpServletRequest` 中检索 `sslclientcipher` 和 `sslclientcertstatus` 标头。如果两者都存在且具有非空值，我们将认为该请求是安全请求（至少，我们将假设用户的浏览器直到 Cloud Foundry 代理都使用了 SSL），并将返回 `true`。    ***清单 A-5.** 包装 HttpRequest 的过滤器*  `public class CloudFoundryHttpServletRequestWrappingFilter extends GenericFilterBean {`  `    @Override` `    public void doFilter(ServletRequest request, ServletResponse response,` `                       FilterChain chain) throws IOException, ServletException {` `        chain.doFilter(new CloudFoundryHttpServletRequestWrapper((HttpServletRequest)` `request), response);` `    }`  `    static class CloudFoundryHttpServletRequestWrapper extends HttpServletRequestWrapper {`  `        private static final String[] TLS_HEADERS = new String[]` `        {"sslclientcipher", "sslclientcertstatus"};`  `        public CloudFoundryHttpServletRequestWrapper(HttpServletRequest httpServletRequest) {` `            super(httpServletRequest);` `        }`  `        @Override` `        public boolean isSecure() {` `            boolean isSecure = super.isSecure();` `            if (!isSecure) {` `                boolean isActuallySecure = true;` `                for (String header : TLS_HEADERS) {` `isActuallySecure = isActuallySecure && StringUtils.isNotBlank` ![Image](img/U002.jpg) `(getHeader(header));` `                }` `                isSecure = isActuallySecure;` `            }` `            return isSecure;` `        }` `    }` `}`  重写 `isSecure()` 方法可能看起来有点激烈。但是，JavaDoc 指出，如果连接被认为是安全的，它应该返回 `true`。它不绑定到特定协议，因此永远不应假设当 `isSecure` 返回 `true` 时，协议一定是 HTTPS。它也可以是任何其他提供“安全”保证的协议。此外，鉴于这种实现是最不具侵入性的，并且在升级 Spring 或 Spring Security 时最不可能出现问题，我们更喜欢这种解决方案。Cloud Foundry 也在不久的将来致力于更好的 SSL 支持，可能允许 SSL 一直延伸到我们的服务器。^(7)    我们还需要告诉 Spring Security 在 `/spring/spring-security.xml` 中使用我们的额外过滤器（参见清单 A-6）。    ***清单 A-6.** 将过滤器添加到 spring-security.xml 文件*  `<security:http use-expressions="true" >` `    <security:custom-filter` `      ref="cloudFoundryHttpServletRequestWrappingFilter"` `      before="FIRST"/>`  `    <security:form-login login-page="/public/authentication/login.htm"` `       default-target-url="/public/main.htm?authenticationOk=1"` `       authentication-failure-url="/public/authentication/login.htm?authenticationNok=1"  />`  `    <security:intercept-url pattern="/index.jsp" access="permitAll" />` `    <security:intercept-url pattern="/public/authentication/**"` `                            access="permitAll" requires-channel="https" />` `    <security:intercept-url pattern="/public/createOrders/**"` `                            access="permitAll" requires-channel="https" />` `    <security:intercept-url pattern="/public/**" access="permitAll" />`  `    <security:intercept-url pattern="/secured/**"` `                            access="fullyAuthenticated"` `                            requires-channel="https" />`  `    <security:intercept-url pattern="/**" access="denyAll" />` `</security:http>`  `<bean id="cloudFoundryHttpServletRequestWrappingFilter"` `class="com.apress.prospringmvc.bookstore.web.security.` ![Image](img/U002.jpg) `CloudFoundryHttpServletRequestWrappingFilter"/>`  现在我们已经完成了所有这些，我们应该能够将我们的应用程序部署到 Tomcat 6——因此也能部署到 Cloud Foundry——所以现在让我们尝试这样做。    #### 部署    使用插件部署到 Cloud Foundry 的过程与部署到 Tomcat 实例非常相似。按照以下步骤创建要部署到的服务器：    __________________    ⁷ [`http://support.cloudfoundry.com/entries/20672161-ssl-support-to-server-instance`](http://support.cloudfoundry.com/entries/20672161-ssl-support-to-server-instance)    1.  在“服务器”面板中，右键单击并选择“新建” ![Image](img/U003.jpg) “服务器”（参见图 A-3）。          ![Image](img/9781430241553_App-03.jpg)          ***图 A-3.** 创建新服务器*           2.  选择 Cloud Foundry，它在 VMWare 节点下。你可以将所有其他选项保留为默认值。 3.  单击“下一步”，你将看到一个对话框，要求你输入注册 Cloud Foundry 时使用的帐户信息（参见图 A-4）。如果你还没有这样做，你仍然可以单击“CloudFoundry.com 注册”按钮，这将带你到网站。          ![Image](img/9781430241553_App-04.jpg)          ***图 A-4.** 配置你的 CloudFoundry.com 帐户*           4.  此时，有点怀疑并单击“验证帐户”按钮也无妨。它将检查你是否可以连接到 Cloud Foundry 以及你的凭据是否正确。单击“完成”，下一个对话框将询问你要部署哪些应用程序。 5.  在左侧列表（可用资源）中，你可以选择 `appendixA-bookstore`，然后单击“添加” ![Image](img/U003.jpg) 按钮。这会将应用程序移动到“已配置资源”列表，并将其标记为准备部署（这就像将你的应用程序标记为部署到普通的 Tomcat 实例一样）。接下来，单击“完成”。 6.  这将带你进入“应用程序详细信息”对话框，询问你正在部署哪种类型的应用程序。选择“Spring”，如图 A-5 所示，然后单击“完成”。          ![Image](img/9781430241553_App-05.jpg)          ***图 A-5.** 选择应用程序类型和名称*           7.  你会再看到一个对话框，要求你提供一个 URL 来部署你的应用程序（参见图 A-6）。默认情况下，URL 将是 *`name-of-the-app`*`.cloudfoundry.com`。你需要选择另一个 URL，因为 `appendixA-bookstore` 已经被保留。你还可以通过更改“内存预留”的值来选择要为应用程序分配的内存量。          ![Image](img/9781430241553_App-06.jpg)          ***图 A-6.** 为我们的应用程序选择 URL*           8.  现在单击“完成”。我们保持“部署时启动应用程序”复选框处于选中状态，因此应用程序将开始部署和启动。（你也可以稍后通过手动单击“应用程序”选项卡上 Cloud Foundry 面板中的“启动”按钮来启动应用程序。）    部署完成后，你可以将浏览器指向你之前配置的 URL（在我们的例子中是 [`http://appendixa-bookstore.cloudfoundry.com`](http://appendixa-bookstore.cloudfoundry.com)，但对于你的情况是 [`http://`*`the-name-you-just-chose`*`.cloudfoundry.com`](http://the-name-you-just-chose.cloudfoundry.com)），然后看到你的应用程序在云端运行（参见图 A-7）！  ![Image](img/9781430241553_App-07.jpg)  ***图 A-7.** 我们的应用程序在云端运行*    #### 配置服务    我们现在可能已经让应用程序运行了，但仍然缺少一些东西：应用程序仍然使用其嵌入的 H2 实例，因此每当我们重新启动应用程序时，数据将再次丢失。因此，让我们使用 Cloud Foundry 提供给我们的持久化选项之一。我们使用 JPA 进行持久化，因此我们的选择将限于关系数据库 MySQL 和 PostgreSQL：    1.  双击“服务器”面板中的“VMWare Cloud Foundry”服务器。你将看到“概述”面板，你可以在其中更改你的帐户详细信息（参见图 A-8）。          ![Image](img/9781430241553_App-08.jpg)          ***图 A-8.** Cloud Foundry 概述面板*           2.  单击底部的“应用程序”选项卡，你将看到已部署的实际应用程序，如图 A-9 所示。单击“服务”面板旁边的“添加服务”按钮。          ![Image](img/9781430241553_App-09.jpg)          ***图 A-9.** 添加服务*           3.  选择一个 MySQL 数据库服务并为其命名（参见图 A-10）。          ![Image](img/9781430241553_App-10.jpg)          ***图 A-10.** 添加 MySQL 服务*           4.  单击“完成”，你将返回到“应用程序”选项卡，你将在其中看到 MySQL 现在是一个可用的服务。（请注意，在撰写本文时，插件中存在一个错误，导致“服务”面板在刚刚添加服务后不显示该服务。重新启动 Eclipse 可以解决这个问题。） 5.  现在选择你已部署的应用程序，并将 MySQL 服务从“服务”面板拖到“应用程序服务”面板（参见图 A-11）。    ![Image](img/square.jpg) **注意**  你可以使用 `vmc services` 命令查看可用的服务（及其版本）。  ![Image](img/9781430241553_App-11.jpg)  ***图 A-11.** 将 MySQL 服务添加到我们的应用程序*    配置 MySQL 数据库只需这些。现在单击“更新并重新启动”按钮，然后使用浏览器导航到应用程序。你下的订单现在应该能够在重启后保留。    #### 它是如何工作的？    你可能想知道，我们如何能够将数据保存在 MySQL 数据库中，因为我们从未在应用程序中配置过另一个数据源。事实上，我们所做的就是告诉 Cloud Foundry 我们想要使用 MySQL 作为应用程序服务。我们从未更改 `com.apress.prospringmvc.bookstore.config.InfrastructureContextConfiguration` 类中的数据源配置，但我们似乎仍然能够连接到 Cloud Foundry 上的 MySQL。    这背后的魔法是由 Spring 所谓的*自动重新配置*来解释的（更多信息请参见 Spring 博客上的博文^(8)）。    还记得我们在部署时可以选择应用程序类型吗？我们选择了 Spring，这使 Cloud Foundry 和 Spring 能够施展它们的魔法。发生的情况是，在部署过程中，额外的配置被添加到你的 `web.xml` 中，这些配置将一些额外的 Spring bean 注册到你的 `ApplicationContext` 中。（这正是我们之前解释的需要使用 XML 配置的原因。）其中一个 bean 是 `org.cloudfoundry.reconfiguration.CloudAutoStagingBeanFactoryPostProcessor`^(9)，它将查找代表服务的 bean（例如，`javax.sql.DataSource`、`org.springframework.data.mongodb.MongoDBDFactory` 等的实例），这些 bean 可以被其他了解绑定到你的应用程序的应用程序服务的云感知 bean 替换。    但是 Hibernate 方言呢？之前我们使用的是 H2，现在我们使用的是 MySQL——但我们不必告诉 Hibernate 任何关于此事的信息。事实证明，最近的 Hibernate 版本有一个方便的特性，可以从实际连接中检测数据库类型，因此不再需要显式配置要使用的方言。    ![Image](img/square.jpg) **注意**  这有其缺点：如果你的数据库在应用程序启动时未启动或无法访问，Hibernate 将在运行时失败。    #### 其他配置选项    自动重新配置并不是配置应用程序的唯一方法。另一种更显式的可能性是使用云配置命名空间。使用该命名空间，我们可以在 XML 中配置数据源，如清单 A-7 所示。    __________________    ⁸ [`http://blog.springsource.org/2011/11/04/using-cloud-foundry-services-with-spring-part-2-autoreconfiguration/`](http://blog.springsource.org/2011/11/04/using-cloud-foundry-services-with-spring-part-2-autoreconfiguration/)    ⁹ [`https://github.com/cloudfoundry/vcap-java/tree/master/auto-reconfiguration/src/main/java/org/cloudfoundry/reconfiguration`](https://github.com/cloudfoundry/vcap-java/tree/master/auto-reconfiguration/src/main/java/org/cloudfoundry/reconfiguration)    ***清单 A-7.** 使用云命名空间*  `<?xml version="1.0" encoding="UTF-8"?>` `<beans`    `       xsi:schemaLocation="http://www.springframework.org/schema/beans` `         http://www.springframework.org/schema/beans/spring-beans-3.1.xsd` `         http://www.springframework.org/schema/context` `         http://www.springframework.org/schema/context/spring-context-3.1.xsd` `         http://schema.cloudfoundry.org/spring` `         http://schema.cloudfoundry.org/spring/cloudfoundry-spring-0.8.xsd"` `profile="cloud">`  `    <cloud:data-source id="dataSource" />` `</beans>`  在 beans 元素上设置 `profile="cloud"` 属性意味着只有在“cloud”配置文件处于活动状态时，才会使用该元素内定义的 bean。`cloud:datasource` 元素是一个“普通”数据源，表示绑定到此应用程序的关系数据库应用程序服务。    将此与 Cloud Foundry 自动将 *cloud* 设置为活动应用程序配置文件的事实相结合，我们可以使用该配置文件进行特定于云的配置。如果我们想同时使用多个相同类型的云服务（例如，MySQL 和 PostgreSQL 服务），这种显式配置甚至是必需的。    如果我们不希望 Spring 使用自动重新配置，我们总是可以使用 *war* 应用程序类型。如果我们使用该类型部署，我们的应用程序将不会以任何方式被触及。    #### 本地部署    到目前为止，我们已经部署到了 cloudfoundry.com 上的实际云基础设施，该基础设施由 VMWare 托管。但是 CloudFoundry.com 提供了另一种解决方案，一种不需要我们总是连接到远程系统的解决方案。它被称为 *MicroCloud*，它作为虚拟机（一个 VMWare 镜像）分发，我们可以在本地运行（例如，在免费的 VMWare Player 上）。    你可以从 [`https://my.cloudfoundry.com/micro`](https://my.cloudfoundry.com/micro) 下载 MicroCloud（需要有效的 CloudFoundry.com 帐户才能下载）。在同一网站上，你还可以找到有关如何配置 MicroCloud 的说明。完成后，部署过程与在 CloudFoundry.com 上部署完全相同；你只需使用不同的目标。    ### 使用 Cloud Foundry 进行调试    事情并不总是按计划进行，有时你需要调试你的应用程序。    虽然你始终可以在自己的 Tomcat 实例上部署时调试应用程序，但 MicroCloud 也支持使用 Java 调试器连接。为此，你可以使用“调试”按钮而不是“启动”按钮来启动应用程序。一旦应用程序以调试模式启动，你就可以像调试任何其他本地启动的应用程序一样调试它。    另一种了解在云端运行时发生了什么的方法是利用日志文件。你可以使用 STS 中的“远程系统”视图访问它们，你可以通过“窗口” ![Image](img/U003.jpg) “显示视图” ![Image](img/U003.jpg) “其他...”找到该视图，然后搜索“远程系统”。你可以浏览远程目录结构并下载日志文件以查看其内容（参见图 A-12）。  ![Image](img/9781430241553_App-12.jpg)  ***图 A-12.** 远程系统视图*    如果你想远程访问你的服务（例如，你想检查当前数据），Cloud Foundry 提供了 Caldecott gem，它允许你创建一个隧道^(10) 连接到它们。例如，你可以使用 `vmc tunnel mysql --port 12345` 这样的命令来隧道连接到本地端口 12345 上的远程 MySQL。现在只需将你的 MySQL 或 JDBC 客户端指向该端口即可访问数据。你可以在 [`http://blog.cloudfoundry.com/post/12928974099/now-you-can-tunnel-into-any-cloud-foundry-data-service`](http://blog.cloudfoundry.com/post/12928974099/now-you-can-tunnel-into-any-cloud-foundry-data-service) 找到有关 Caldecott 的更多信息。    __________________    ¹⁰ [`http://bit.ly/akrcc1`](http://bit.ly/akrcc1)    ### 总结    本附录涵盖了 Cloud Foundry 以及如何将你的应用程序部署到其上。    有几件事将 Cloud Foundry 与其他 PaaS 提供商区分开来：一是 Cloud Foundry 的开源性质，另一个是其与 Spring 框架的出色集成。虽然 Cloud Foundry 为大多数应用程序承诺了无缝的部署模型，允许你按原样部署应用程序，但在书店应用程序的情况下，你必须进行一些调整。这些调整是必需的，主要是因为您使用的 Tomcat 版本比 Cloud Foundry 当前提供的版本更新。    Cloud Foundry 目前仍处于测试阶段，因此它正在快速发展，新的特性和服务仍在添加中。虽然你可能偶尔会遇到一些粗糙的边缘，但它绝对值得关注，看看它未来如何发展。    ## 索引    ### ![Image](img/square.jpg) A    AccountRepository, 296, 298, 299    Acegi 安全。*参见* Spring 安全    动作执行控制    <on-end> 元素, 404    <on-entry> 元素, 404–405    <on-exit> 元素, 405    <on-render> 元素, 405    <on-start> 元素, 404    子元素, 405–406    基于注解的控制器    书籍详情页面, 136    BookDetailController, 134–135    detail.jsp, 135    修改后的搜索页面, 134    URL 映射, 135    书籍搜索页面    BookSearchController 替代版本, 133    带有 BookSearchCriteria 的 BookSearchController, 131–133    criteria 方法参数, 133    数据绑定, 133    表单代码, 130    RequestParam, 133    搜索结果, 132    search.jsp 文件, 130    DefaultAnnotationHandlerMapping, 110    HandlerMapping, 110    IndexController, 110    登录控制器    AuthenticationException, 128    handleLogin 方法, 128    HttpServletRequest, 128    初始 LoginController, 126–127    登录页面, 127    login.jsp, 125–126    修改后的 LoginController, 127–128    RedirectAttributes, 129–130    RequestParam, 128–129    用户名和密码参数, 128    ModelAndView, 110    RequestMapping 注解, 110    AnnotationFormatterFactory, 154    应用程序安全, 478    应用程序测试    变形效应, 274    自动化前端测试    基础设施设置, 310    小故障, 310    性能、渲染时间和可用性, 309    Selenium (*参见* Selenium)    Servlet 容器, 310    测试工具, 310    Tomcat, 310    代码覆盖率, 284–286    环境, 277    通过 Gradle 进行前端测试, 318–319    目标, 276    没有测试的实现变形, 274    重要性, 276    基础设施, 276    集成测试 (*参见* 集成测试)    模拟对象 (*参见* 模拟对象)    性能测试, 278    机器人开发者, 274, 275    Spring 的测试支持    集成测试 (*参见* 集成测试)    JpaBookRepository, 292–294    压力测试, 278    系统测试, 278    测试工具, 275    理解, 276    单元测试, 278–279    用户验收测试, 278    VCS, 277    assertEquals 方法, 283    assertTrue 方法, 283    异步 JavaScript (AJAX)    AJAX (*续*.)    帐户页面 PUT Ajax 表单提交, 227    优雅降级, 227    HTML, 221–222    JSON    BookSearch 控制器, 222    org.springframework.web.bind.annotation.ResponseBody, 223    search.jsp 文件., 223, 224    发送和接收, 224–226    渐进增强., 227    PUT 请求, 226    template.jsp 文件, 220    authenticatedAccount 变量, 419    认证过程, 478, 479    AuthenticationController, 411–413, 420    AuthenticationException, 128    授权, 479    ### ![Image](img/square.jpg) B    BookId, 135    BookRepository 接口, 292    书店增强    动作状态, 417–419    认证    action 属性, 412    属性和元素, 415    AuthenticationController, 411, 414–415    authentication-flow, 411, 413    authenticationForm, 413, 414    eventId, 414    flowExecutionUrl, 413    实现步骤, 410    initializeForm, 411    页面, 411–412    @RequestMapping, 412    Spring MVC, 412–413    subflow 属性, 416    书籍选择和配送选项, 387–388    类别选择    渲染时, 382–384    启动时, 382    类型转换, 384–386    类型格式化, 386–387    createOrders-flow, 420–421    决策状态, 416–417    表单验证    应用程序控制器, 391–392    createOrders-flow 文件, 396    JSR 303 注解, 388–390    消息, 393–395    OrderController, 395, 398–400    OrderForm, 395, 397–398    Web Flow 验证器方法和类, 390–391    javax.servlet.http.HttpSession, 408    无转换, 381    无验证, 381    结果事件, 419–420    修订后的流程, 409    示例流程重构步骤, 410    安全机制, 408    ### ![Image](img/square.jpg) C    CategoryConverter 配置, 157    证书颁发机构 (CA), 510, 511    子流程, 407    云计算, 535, 536    Cloud Foundry    调试, 552–553    部署    添加服务按钮, 548    应用程序详细信息对话框, 546    自动重新配置, 551    CloudFoundry.com 帐户配置, 545    MicroCloud, 552    MySQL 数据库服务, 549, 550    命名空间, 551, 552    创建新服务器, 545    概述面板, 547, 548    插件, 537–538    Spring Security 配置 (*参见* Spring Security, 配置)    Tomcat 6 (*参见* Tomcat 6)    URL, 546    基于集合的标签, 146    ConditionalGenericConverter API, 152    ConfigurableWebBindingInitializer, 137–139    确认视图, 426    @ContextConfiguration, 306    持续集成 (CI) 系统, 277    控制器    基于注解的控制器 (*参见* 基于注解的控制器)    配置视图控制器, 111–112    数据绑定 (*参见* 数据绑定)    定义, 107    实现, 109–110    基于接口的控制器, 108    国际化 (*参见* 国际化)    请求处理方法 (*参见* 请求处理方法)    会话作用域, 378    ConversionService API, 152    ConverterFactory API, 151    CookieValue 注解, 123    CreateOrders-flow, 416    ### ![Image](img/square.jpg) D    数据访问对象 (DAO), 281–282    数据绑定, 133    BookSearchCriteria JavaBean, 136–137    全局自定义, 137–139    ModelAttribute    表单标签库 (*参见* 表单标签库)    在方法参数上, 142–143    在方法上, 141–142    SessionAttributes, 143–144    Spring 标签库, 144    嵌套绑定, 137    每个控制器的自定义, 139–141    setCategory 方法, 137    setTitle 方法, 137    类型转换 (*参见* 类型转换)    验证, 模型属性    AccountValidator 实现, 164–165    绑定错误, 163    错误代码, 字段错误, 165    JSR-303, 167–170    MessageSource 配置, 163–164    带有错误代码的注册页面, 167    RegistrationController, 166    requiredFields, 165    supports 方法, 163    验证器接口, 163    DateFormatAnnotationFormatterFactory, 154–155    DateFormatter, 153–154    DefaultHandlerExceptionResolver, 200    DispatcherServlet    引导    ServletContainerInitializer, 76–77    Servlet 3.0 规范, 74    web.xml, 75–76    WebApplicationInitializer, 77–78    web-fragment.xml 配置, 76    配置    应用程序上下文, 80–83    组件解析, 83–85    默认配置, 85–86    属性, 78–80    Spring @MVC 默认值, 86–87    DummyDao 类, 285–286    ### ![Image](img/square.jpg) E    EclEmma, 284–286    EntityManager, 291    事件, 374    执行作用域, 378    ExternalRedirect 属性, 426    ### ![Image](img/square.jpg) F    文件上传    配置    Apache, 230–231    异常处理, 235    请求处理方法 (*参见* 请求处理方法)    Servlet 3.0, 228–230    控制器修改, 228    实现, 228    注册, 228    Flash 属性, 118    Flash 作用域, 376–377    流程作用域, 377–379    流程变量, 401    flowExecutionUrl 隐式对象, 379    FlowInputMappingException, 424    表单标签库, 145    订单 JSP, 147    path 属性, 146    带有类别的搜索页面, 148    共享表单标签属性, 146    标题字段, 147    Formatter API, 152–153    FormatterRegistry 接口, 155–156    ### ![Image](img/square.jpg) G    GenericConverter API, 151–152    全局转换, 406    ### ![Image](img/square.jpg) H    Handlelogin 方法, 128, 301–304    HandlerExceptionResolver, 198    处理器执行链    确定, 68, 69    异常处理, 71    执行, 69–71    Hibernate 验证器 JAR 文件, 394, 395    主页, 500–501    HTTP 协议    HiddenHttpMethodFilter    帐户.jsp 标题, 219    AccountController 更新方法, 219    BookstoreWebApplicationInitializer, 218    方法, 216    URL 指向, 217    HttpPutFormContentFilter, 226, 227    ### ![Image](img/square.jpg) I    Init-binder 方法, 141    内联流程, 407    集成测试, 277, 279–280    注解配置, 289    ApplicationContext, 289, 290    架构和可重用性, 280    @Configuration 类, 288    上下文配置添加, 288    @ContextConfiguration 注解, 288    @DirtiesContext 注解, 290    DMBS, 280    Gradle 命令, 289–290    HQL/JPQL 查询, 280    内存等效物, 279    JpaBookRepository, 在 src/test/java 中, 287    监听器, 287    “真实”系统资源, 279    运行器添加, 287    Spring 配置, 288    SpringJUnit4ClassRunner, 287    测试套件, 289    可测试性, 280    TestExecutionListener 注解, 287    事务管理, 290–292    拦截器    回调, 186    HandlerInterceptor    AccountController, 196–197    配置, 196    403 错误页面, 197    接口, 187    SecurityHandlerInterceptor, 195    HandlerMapping 配置, 188    interceptorregistry, 189–191    WebRequestInterceptor    CommonDataInterceptor, 192, 193    postHandle 方法, 193    随机书籍部分, 193–195    欢迎页面, 192    WebRequestInterceptor 接口, 187    基于接口的控制器, 108    国际化    LocaleChangeInterceptor    荷兰语书籍搜索页面, 176    英语书籍搜索页面, 175    带有消息标签的书籍搜索页面, 174–175    配置, 173    LocaleResolver, 172    消息源, 170–172    控制反转 (IoC), 29    ### ![Image](img/square.jpg) J, K    java.io.Serializable, 401    Java 的 transient, 401    javax.servlet.http.HttpServletRequest, 379    ### ![Image](img/square.jpg) L    LocaleChangeInterceptor    荷兰语书籍搜索页面, 176    英语书籍搜索页面, 175    带有消息标签的书籍搜索页面, 174–175    配置, 173    LocalValidatorFactoryBean, 390    登录页面, 498–500    LoginController, 302–304    LoginControllerTest, 307–309    ### ![Image](img/square.jpg) M    Match 属性, 508    模拟对象    AccountService    @After 方法, 299    @Before 方法, 299    AuthenticationService, 298    完整测试用例, 299–301    ContextConfiguration 注解, 298    findByUsername 方法, 298, 299    login 方法, 296    Mockito 方法, 299    编程, 298    save 方法, 296    src/test/java, 297    testLoginFailure, 298    验证, 299    虚拟对象, 295, 296    伪造对象, 295    JpaBookRepository, 296    MVC 逻辑    handleLogin 方法, 302–304    MockHttpServletRequest, 304–305    RDBMS, 296    桩, 295, 296    测试替身, 295    验证阶段, 295    MockHttpServletRequest, 304–306    MockHttpServletResponse, 306    MockHttpSession, 306    模型-视图-控制器 (MVC), 51    横切关注点    异常处理, 197–201    SimpleMappingExceptionResolver, 201–204    组件, 301    DispatcherServlet (*参见* DispatcherServlet)    FlashMapManager, 104, 105    HandlerAdapter    AnnotationMethodHandlerAdapter, 96    HandlerAdapter API, 94    HttpRequestHandlerAdapter, 95    实现, 95    RequestMappingHandlerAdapter, 96    SimpleControllerHandlerAdapter, 95    SimpleServletHandlerAdapter, 96    HandlerExceptionResolver    API, 101    实现, 102    ViewResolver, 103, 104    HandlerMapping    BeanNameUrlHandlerMapping, 90–91    ControllerBeanNameHandlerMapping, 92    ControllerClassNameHandlerMapping, 92–93    DefaultAnnotationHandlerMapping, 93–94    HandlerMapping API, 88    实现, 89    RequestMappingHandlerMapping, 93–94    SimpleUrlHandlerMapping, 91–92    URL 映射, 90    JSP, 301    LocaleResolver    AcceptHeaderLocaleResolve, 99    API, 98    CookieLocaleResolver, 99    FixedLocaleResolver, 99    实现, 98    SessionLocaleResolver, 99    MultipartResolver    API, 96–97    实现, 97    库, 97    StandardServletMultipartResolver, 97    org.springframework.web.servlet    afterCompletion 方法, 72, 73    处理器执行链 (*参见* 处理器执行链)    请求处理流程, 67–68    请求处理总结, 73–74    请求处理工作流, 66    视图渲染过程, 71–72    RequestDataValueProcessor, 212–213    RequestMappingHandlerAdapter    帐户页面, 212    accountcontroller, 211    HandlerMethodArgumentResolver, 207    HandlerMethodReturnValueHandler, 207    修改后的 WebMvcContextConfiguration, 210–211    SessionAttribute 注解, 208    SessionAttributeProcessor, 209, 210    RequestMappingHandlerMapping, 205–206    作用域    添加到购物车链接, 180–182    注解属性, 178    CartController Bean, 180    CheckoutController, 183–185    会话, 178, 179    单例作用域, 178    Spring 模拟对象    handleLogin 方法, 302–304    MockHttpServletRequest, 304–305    Spring MVC 测试    @Configuration 类, 306    build.gradle, 305    配置, 306    LoginControllerTest, 307–309    MVC, Spring MVC 测试 (*续*.)    MVC 基础设施, 306    快照, 305    WebApplicationContext, 306    ThemeResolver    API, 100    CookieThemeResolver, 101    FixedThemeResolver, 101    实现, 100    SessionThemeResolver, 101    ViewNameTranslator, 102–103    ModelAndView, 108    ModelAttribute 注解, 120, 122, 124    表单标签库, 145    订单 JSP, 147    path 属性, 146    带有类别的搜索页面, 148    共享表单标签属性, 146    标题字段, 147    在方法参数上, 142–143    在方法上, 141–142    请求处理方法, 122    Spring 标签库, 144    ModelnameValidator 类, 391    ### ![Image](img/square.jpg) N    NoMatchingTransitionException, 419    ### ![Image](img/square.jpg) O    对象关系映射 (ORM), 291, 294    OrderController 类, 392, 394    OrderFormValidator 类, 391    org.springframework.validation.BindingResult 属性, 160    org.springframework.webflow.execution.RequestContext 对象, 402    ### ![Image](img/square.jpg) P, Q    Param, 379    ParameterizableViewController, 111    父流程, 407    PathVariable 注解, 122    性能测试, 278    PlaceOrder() 方法, 419    Post Redirect Get (PRG) 惯用法, 426    ### ![Image](img/square.jpg) R    RedirectAttributes, 118    RegistrationController, 158–160    表述性状态转移 (REST)    HTTP 协议 (*参见* HTTP 协议)    PUT 请求, 226    资源标识, 215–216    请求作用域, 374–376    RequestBody 注解, 121    请求处理方法    帐户页面, 231    属性, 113–114    粗粒度映射, 113    多部分文件, 232    MultipartHttpServletRequest, 233    示例文件上传输出, 232    示例映射, 114–115    Servlet 3.0, 234    支持的方法参数注解, 119    CookieValue, 123    HandlerMethodArgumentResolver 接口, 119–120    ModelAttribute 注解, 120, 122    PathVariable, 122    RequestBody, 121    RequestHeader, 121    RequestParam, 120    RequestPart, 121–122    支持的方法参数类型, 115–117    RedirectAttributes, 118    UriComponentsBuilder, 118    支持的方法返回值, 123–125    UploadOrderForm, 233–234    RequestHeader 注解, 121    RequestParam 注解, 120    RequestParameters 隐式对象, 401    RequestPart 注解, 121–122    ResponseStatusExceptionResolver, 200, 201    基于角色的访问控制 (RBAC)    Account, 518, 521    BookstoreUserDetailsService, 521, 522    领域模型, 518    功能, 518    GrantedAuthority, 522    信息访问, 518    与权限的多对多关联, 520    与角色的多对多关联, 519    权限实体, 520    ### ![Image](img/square.jpg) S    安全套接字层 (SSL), 510    安全过程, 477    SecurityHandlerInterceptor, 302    Selenium    IDE    复制到剪贴板, 317    用于 Firefox, 314–315    JSP 源, 314    语言选择, 317    登录, 314    主窗口, 316    场景录制, 316    WebDriver JUnit 风格, 318    RC 服务器, 310–312    测试编写, 312–314    WebDriver 项目, 310    Servlet 请求作用域, 379, 402    SessionAttributes, 143–144    <set> 元素, 401    共享表单标签属性, 146    SimpleMappingExceptionResolver    AnnotationMethodHandlerExceptionResolver, 204    配置, 201, 202    ExceptionHandlerExceptionResolver, 204    改进的登录控制器, 202–203    登录页面, 203    片段, 172    Spring 开发环境    先决条件    集成开发环境, 2    Java 开发工具包, 2    Servlet 容器, 2    软件版本和下载站点, 1    示例应用程序    书店, 3–4    部署, 8–9    ../gradlew build, 7–8    gradlew 脚本, 6–7    STS (*参见* SpringSource Tool Suite)    Spring 表达式语言 (Spring EL), 402    Spring 框架    应用程序上下文    配置文件, 36    默认配置选项, 35    层次结构, 38    MoneyTransferSpring 类, 29–30    org.springframework.web.context.WebApplicationContext 接口, 34, 35    面向切面编程, 45–47    组件扫描, 40–41    依赖注入    基于注解的依赖注入, 33    基于构造函数的依赖注入, 31    上下文查找, 30, 31    硬编码依赖, 29–30    IoC, 29    基于 setter 的依赖注入, 32    启用特性, 44–45    模块依赖, 26–28    配置文件, 41–44    资源加载, 38–40    作用域, 41    Web 应用程序, 47–50    Spring 安全, 477    访问授权    “添加类别”, 528    管理员用户登录, 529    认证标签, 530–531    授权标签, 导航栏, 525    代码注解, 531–532    更细粒度的权限检查, 526–527    信息, 523    初始数据设置, 524, 525    JavaScript 代码, 527    登录和注销, 529    管理书籍菜单选项, 528    manageBooks.jsp 页面, 524    标签库, 页面, 523    认证和授权方案, 479    基本安全方案, 481, 482    书店    访问配置, 资源, 490–492    addMappingForUrlPatterns, 485    应用程序过滤器链配置, 487    认证管理器, 495    完整安全配置, 503, 504    核心概念, 484    表达式方法和字面量, 493–494    过滤器, 484    主页, 500–501    注销, 502–503    登录页面, 498–500    多个过滤器, 485    Spring 安全, 书店 (*续*.)    命名空间元素/属性, 488, 489    命名空间过滤器链配置, 487    单向哈希密码, 495    密码编码器, 496, 497    资源, 489, 490    正确的依赖, 482–483    加盐哈希, 496, 497    Spring 类, 常量, 484    xml 头部, 486    配置, 479    chain.doFilter, 542, 543    (Http)ServletRequest, 542    isSecure(), 542    spring-security.xml 文件, 544    数据库    getPrincipal 方法, 506    内存数据存储, 504    SecurityContext, 505    UserDetails 对象, 504, 505    声明式方法, 480    流程绕过安全, 481    流程安全    属性添加, 507–508    请求映射, 506    SecurityFlowExecutionListener, 508–509    URI, 过滤器链, 506    本地化, 516–517    消息本地化, 480    RBAC (*参见* 基于角色的访问控制)    Spring MVC, 480    传输安全    浏览器确认, 511    CA, 510, 511    HTTP, 510, 513, 514    HTTPS, 514    信息加密, 515, 516    keytool, 512    自签名证书, 511    SSL 连接器, 510, 513    Tomcat 证书, 514, 515    URI, 513    Web Flow, 480    Spring Web Flow    访问作用域    externalContext, 402    flowRequestContext, 402    RequestContext, 403    Servlet 请求作用域, 402, 403    Spring EL, 402    Unified EL, 402    动作执行控制    <on-end> 元素, 404    <on-entry> 元素, 404–405    <on-exit> 元素, 405    <on-render> 元素, 405    <on-start> 元素, 404    子元素, 405–406    书店增强 (*参见* 书店增强)    结束状态, 425–426    流程定义, 373–374    流程变量, 401    全局转换, 406    隐式对象, 379–381    java.util.Map, 400    作用域    对话, 378–379    Flash, 376–377    流程, 377–378, 400, 401    Java transient, 374    java.io.Serializable, 374    请求, 374–376    类型, 374    视图, 377    子流程    子流程, 407    组合, 406    内联流程, 407    输入/输出映射, 421–423    模块化, 407    *vs*. 普通流程, 407    普通顶级流程, 408    订单处理, 423–425    父流程, 407    value 属性, 400    SpringSource Tool Suite (STS)    依赖管理, 5    扩展选项卡, 10    Gradle 搜索结果, 11    index.jsp, 21    IndexController 代码, 19, 20    安装对话框, 12    模型属性, 20, 21    示例导入, 12–16    SpringSource vFabric tc Server, 17–19    网页更新, 21, 22    压力测试, 278    StringToEntityConverter, 156–157    子流程, 373    子流程, 407    组合, 406    内联流程, 407    输入/输出映射, 421–423    模块化, 407    *vs*. 普通流程, 407    普通顶级流程, 408    订单处理, 423–425    父流程, 407    系统测试, 278    ### ![Image](img/square.jpg) T    @Test 方法, 283    TestNG, 281    Tomcat 6    AnnotationConfigWebApplicationContext, 540    context.xml 文件, 541    ContextLoaderListener, 539    DispatcherServlet, 539    过滤器配置, 541    XmlWebApplicationContext, 540    @Transactional 注解, 290    TransactionalTestExecutionListener, 290    传输层安全 (TSL)。*参见* 安全套接字层 (SSL)    类型转换    帐户注册页面, 161    带有错误的帐户注册页面, 162    配置    CategoryConverter 配置, 157    FormatterRegistry 接口, 155–156    StringToEntityConverter, 156–157    转换器    ConditionalGenericConverter API, 152    ConversionService API, 152    ConverterFactory API, 151    GenericConverter API, 151–152    接口, 150    格式化器    AnnotationFormatterFactory, 154    DateFormatAnnotationFormatterFactory, 154–155    Formatter API, 152–153    示例 DateFormatter, 153–154    org.springframework.validation.BindingResult 属性, 160    属性编辑器, 149–150    register.jsp 文件, 158    注册页面, 158    RegistrationController, 158–160    String 实例, 148    ### ![Image](img/square.jpg) U    统一表达式语言 (Unified EL), 402    单元测试    标准, 278–279    定义, 277    JUnit    @After 方法, 284    注解执行顺序, 284    assertTrue 和 assertEquals 方法, 283    @Before 注解, 283    DAO, 281–282    DatabaseDao, 282–283    测试失败, 283    测试成功, 283    TestNG, 281    @Test 注解, 283    @Test 方法, 283    UriComponentsBuilder, 118    用户验收测试, 278    ### ![Image](img/square.jpg) V    ValidateDeliverydate 方法, 391, 394    ValidateSelectShop, 391    ValidationMessages.properties, 395    <var> 元素, 401    版本控制系统 (VCS), 277    视图解析器    BeanNameViewResolver, 239–240    ContentNegotiatingViewResolver, 246–247    getContentType 方法, 238    层次结构, 238, 239    实现, 247    InternalResourceViewResolver, 245    org.springframework.web.servlet, 238    render 方法, 238    ResourceBundleViewResolver, 241–243    UrlBasedViewResolver, 243–245    视图渲染过程, 237    XmlViewResolver, 240–241    XsltViewResolver, 245    视图作用域, 377    视图技术    Excel    OrderExcelView, 265–266    ViewConfiguration 类, 267    视图技术 (*续*.)    Freemarker    配置, 257    configurer 属性, 256    宏, 261, 262    模板语言, 259–260    templateLoaderPath, 256    WEB-INF/freemarker/index.ftl, 258    层次结构, 248    JasperReports    配置, 271    多格式视图默认映射, 270    报表填充, 271    Java Server Pages, 249    JavaServer Faces, 249–250    PDF    配置, ContentNegotiatingViewResolve, 264–265    创建, 263–264    生成的 PDF, 265    Tiles    配置, 250–251    定义文件, 252    索引 JSP, 254    模板 JSP, 252–253    欢迎页面, 254    Velocity    配置, 257    configurer 属性, 255    日期和数字, 262    宏, 261, 262    resourceLoaderPath, 256    模板语言, 259–260    WEB-INF/velocity/index.vm, 258    XML 和 JSON, 268–269    ### ![Image](img/square.jpg) W, X, Y, Z    Web 应用程序架构    应用程序分层, 53–55    数据访问层, 61–63    领域层, 56–57    MVC 模式, 51–53    关注点分离, 56    用户界面层, 57–58    Web 层, 58–59    Web Flow    AJAX 支持    添加按钮, Spring JS, 454    addDecoration 方法, Spring JS, 454    书籍选择, 448    配置, 447    createOrders/selectBooks.jsp, 449    通过 Gradle 对 Spring JS 的额外依赖, 445    Firebug 捕获, 455–457    JavaScript 装饰, Spring JS, 455    JavaScript 依赖, 445    JQuery, 457–459    修改后的 selectBooks.jsp, 449–451    showSelectedBooks.jsp 视图, 448    Tiles 配置, 451–453    避免, 332    书店示例应用程序    帐户, 367    实际页面, 359    粗体类型, 365    浏览器渲染视图, 366    购买书籍链接, 360    控制器代码执行, 362    控制器方法, 367    配送选项选择, 363    配送选项 - 我们的配送日期, 370    _eventId_<*on_transition*>, 360    流程步骤说明, 354    <form:select> 元素, 360    header.jsp, 360    主页创建, 355–356, 368    http://localhost:8080/chapter10-bookstore/., 361    实现, 356    <on-start> 元素, 357, 358    概述, 371–372    页面创建, 364–365    上一个视图状态, 362    选择配送选项页面, 368    可选书籍, 359, 362    selectCategory 视图状态, 358    字符串, 358    Tiles 配置, 359    Tiles 定义, 364    视图状态, 选择配送选项, 367    配置    .apress.prospringmvc.bookstore.web.config, 350    自动扫描流程定义, 348    依赖, 346    流程构建器服务, 349–350    流程执行器, 347    流程注册表, 348    FlowHandlerAdapter 和 FlowHandlerMapping Bean, 351, 352    .prospringmvc.bookstore.web.interceptor, 350    src/main/resources/spring/webflow-config.xml 和 Web Flow 配置命名空间, 347    受控导航, 331    异常处理    authenticationcontroller, 439    自定义异常处理器, 440–442    异常转换, 439    显式表单绑定    转换器包, 443    消息键, 444    基于每个属性的绑定, 443    首选方法, 443    细粒度作用域, 323    自动状态管理, 326–327    书店 Web 应用程序, 323    上下文数据, 324    HTTP 会话, 325    myData, 324    用法, 323    流程概念, 322    流程执行监听器    配置, 460    eventSignaled, 461    exceptionThrown, 463    FlowExecutionListener 接口, 459    paused, 462    requestSubmitted(RequestContext context, 461    resuming, 462    sessionCreating, 461    sessionEnding, 462    sessionStarting, 461    stateEntering, 462    transitionExecuting, 461    viewRendering, 462    流程继承    子流程, 431    可合并和不可合并的元素, 430    合并后的流程, 432    parent 属性, 430    父流程, 431    单个视图状态, 431    流程管理的持久化上下文    绑定和解绑, 466    数据库到视图, 463–465    结束状态, 467    JpaFlowExecutionListener,, 467    LazyInitializationException, 475    新订单概览页面, 472    OpenEntityManagerInViewFilter, [468](#Chapter12.html#page_468