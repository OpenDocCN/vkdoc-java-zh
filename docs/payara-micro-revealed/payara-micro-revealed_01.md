# 1. Jakarta EE、MicroProfile、Payara 与云

Payara 是一个符合 Jakarta EE 和 MicroProfile 规范的运行时；这两者都是规定服务器端和基于云的应用程序标准的规范。在本章中，我们将简要介绍服务器端 Java 和 Payara 的历史，以提供一些背景，说明我们是如何走到今天的。

## 服务器端 Java 简史

Java 语言于 1995 年由 Sun Microsystems 发布给世界，并迅速风靡全球。最初，Java 的杀手级特性是 Java 小程序，这是一种允许丰富的“类桌面”应用程序在 Web 浏览器中运行的技术。

Sun Microsystems 开始将 Java 语言扩展到桌面应用程序之外，于 1999 年将该语言分为三个版本；这三个版本分别称为 J2SE（Java 2 标准版）、J2ME（Java 2 微型版）和 J2EE（Java 2 企业版）。

1999 年 J2EE 的推出取得了巨大成功。Sun Microsystems 创建了一个其他供应商可以实施的标准，对 J2EE 的需求激增，大量供应商创建了实施该标准的产品。其中一些产品至今仍然存在，例如 Red Hat JBoss、Oracle WebLogic 和 IBM WebSphere 应用服务器。

## J2EE

J2EE 取得了巨大成功；它提供了以前是 Java 语言一部分的 API，例如用于在服务器端生成标记（通常是 HTML）以在客户端（通常是 Web 浏览器）上显示的 Servlet。J2EE 引入了几个变得非常流行的新 API 和技术，例如 Java 服务器页面（JSP），它使得在服务器端生成标记比使用 Servlet 更容易，以及企业 JavaBeans（EJB），它自动化了许多企业需求，例如事务和安全性。

J2EE 是如此成功，以至于尽管出现了竞争性的服务器端 Java 技术，例如流行的 Spring 框架，但术语 J2EE 已成为一个通用术语，意为“服务器端 Java”。招聘信息中要求具备 J2EE 专业知识的情况并不少见，而所涉职位实际上只需要很少（如果有的话）的 J2EE 知识，反而侧重于竞争性的 Java 技术。

时至今日，在招聘信息甚至与服务器端 Java 相关的非正式对话中，仍然不难发现术语 J2EE，尽管 J2EE 已经过时多年，早在 2006 年就被 Java EE 取代。

## Java EE

尽管广受欢迎，但 J2EE 却因复杂和难以使用而闻名。因此，一些竞争性技术开始涌现，声称提供与 J2EE 同等的功能，同时更易于使用。当时一些著名的 J2EE 竞争对手包括 Ruby on Rails 和 Spring 框架。

作为对这些竞争技术的回应，Sun Microsystems 于 2006 年推出了 Java EE（Java 企业版）。Java EE 引入了大量使用注解进行应用程序配置的方式，极大地简化了配置和开发服务器端 Java 应用程序的任务。

Java EE 发布后不久，Oracle 公司收购了 Sun Microsystems；因此，Oracle 成为 Java 语言的新管理者，并由此成为 Java EE 的管理者。

与之前的 J2EE 一样，Java EE 应用程序最初旨在部署到应用服务器上，应用服务器是一种企业软件，负责处理应用程序的许多“管道”工作，使应用程序开发人员能够专注于实现业务逻辑。这些应用服务器往往资源密集，需要大量的计算资源，如 RAM 和 CPU。

在 Java EE 发布后的某个时间点，基于云的部署开始流行起来，这在很大程度上得益于 Amazon Web Services 的普及。公司不再需要在现场托管服务器，而是可以从云提供商那里租用计算能力，从而免去了公司采购和维护自己服务器的负担。这些云提供商根据客户实际使用的资源（CPU、内存、带宽等）收费，在许多情况下大大降低了客户的成本。

就其本质而言，为云开发的应用程序不应是资源密集型的。随着云部署的日益流行，传统的 Java EE 应用程序及其庞大、资源密集的应用服务器开始失去人气。Java EE 实现供应商通过开发更适合云部署的轻量级版本来应对。

除了开发轻量级版本的产品外，许多应用服务器供应商开始合作，创建一个专门针对云部署的新标准；这一努力的结果是 MicroProfile 的诞生。MicroProfile 1.0 于 2016 年在旧金山举行的 JavaOne 大会上正式发布。




## MicroProfile

与之前的 Java EE 类似，MicroProfile 是一个规范（可以理解为一份文档），拥有多种实现。MicroProfile 为部署到云环境时常用的 API 提供了规范，例如应用配置、健康检查、请求追踪、指标、容错、文档和安全。

MicroProfile 还与 Jakarta EE（Java EE 的继任者，见下一节）共享一些 API，即 Jakarta RESTful Web 服务（原名 JAX-RS）、Jakarta 上下文和依赖注入（CDI）、Jakarta JSON 绑定和 Jakarta JSON 处理。

不出所料，Jakarta RESTful Web 服务是用于开发 RESTful Web 服务的 API。使用此 API 开发 RESTful Web 服务非常简单，大部分工作只需添加几个简单的注解即可完成。

Jakarta 上下文和依赖注入（CDI）是一个用于将依赖注入到应用程序代码中的 API。它还为由 CDI 管理的 Java Bean 提供了作用域（例如请求、会话、应用等）。

Jakarta JSON 绑定和 JSON 处理 API 用于处理 JavaScript 对象表示法（JSON）格式的数据，这是目前处理微服务和 Web 服务时最常用的格式。Jakarta JSON 绑定是一个高级 API，在后台完成大量工作，而 Jakarta JSON 处理是一个较低级别的 API，为应用程序开发人员提供了更多控制权。

## Jakarta EE

2017 年，Oracle 将 Java EE 捐赠给了 Eclipse 基金会；此时，该技术更名为 Jakarta EE。随着捐赠给 Eclipse 基金会，不再有单一实体控制该标准；相反，几个平等的合作伙伴公司共同为其做出贡献；甚至与任何 Jakarta EE 供应商无关的个人贡献者也可以加入基金会并为该标准做出贡献。

尽管近年来界限变得模糊，但 Jakarta EE 主要被认为是使用单体应用服务器进行传统本地应用部署的规范。另一方面，MicroProfile 被认为是针对轻量级云部署的规范。

尽管云部署近年来变得非常流行，但它并非万能药；并非所有应用都适合为云开发。

此外，在云部署流行之前开发的许多应用程序，迁移到新架构的成本过高或不切实际。

大多数流行的 Jakarta EE 实现都提供了其产品的轻量级版本（例如 Payara Micro），这使得将 Jakarta EE 应用程序迁移到云的任务变得更加容易，甚至简化了专门为云开发新的 Jakarta EE 兼容应用程序的任务。

## Payara

Payara 最初是 GlassFish 应用服务器的一个分支。对于不了解的读者，GlassFish 曾经是 Oracle（以及之前的 Sun Microsystems）的一个开源 Java EE 实现。GlassFish 曾是 Java EE 的参考实现。多年来，GlassFish 一直提供商业支持；然而，在某个时间点，支持被停止了，尽管 GlassFish 仍然可以免费下载。

许多组织要求为他们生产环境中使用的任何软件提供支持合同；GlassFish 商业支持的停止让许多公司争相寻找替代品。

总部位于英国的 C2B2 Consulting 看到了机会，并创建了 Payara 作为 GlassFish 的一个分支。由于基于 GlassFish，为 GlassFish 开发的应用程序可以无缝迁移到 Payara。

C2B2 Consulting 一直在维护 Payara 并添加 GlassFish 原本不具备的额外功能，包括将 Payara 分为两个版本：“Payara Server”，这是一个传统的 Jakarta EE 应用服务器，以及“Payara Micro”，旨在部署到云环境。

Payara 是完全开源的；根据通用开发和分发许可证（CDDL）1.1 版，需要商业支持的用户可以获得。

在本书中，我们将重点介绍 Payara Micro，并着重于 Payara 对 MicroProfile API 的实现。

