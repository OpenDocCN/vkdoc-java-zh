# 7. Jakarta EE 与 Spring 框架

一本关于 Jakarta EE 的书如果不讨论 Spring 框架，那将是不完整的。那么，什么是 Spring 框架？

Spring 框架是 JVM 上另一种运行在 Servlet 容器上的服务器端软件开发平台。它过去是基于 Java 的，但现在你可以使用其他基于 JVM 的语言（如 Kotlin）来开发 Spring 应用程序。Spring 框架的出现是因为开发者在早期版本的 Java EE（当时称为 J2EE）中遇到了诸多挫折。

Rod Johnson^(⁷) 启动了该项目，旨在提供一个比 J2EE 更对开发者友好、更少晦涩难懂的替代平台。最初，Spring 框架只是一个控制反转（依赖注入）框架。它很快变得非常流行，并逐渐发展成为 Jakarta EE 的全栈替代方案。Spring 框架的知识产权归 Pivotal Inc. 所有。

然而，在 Spring 发展的同时，开发者开始遇到一些痛点，这些痛点给他们带来了很多对该平台的挫败感^(⁸)。在 Spring 大受欢迎的同时，Java EE 也在根据收到的反馈不断演进。

Java EE 7 的发布标志着该平台历史上的一个里程碑，因为它在开发者生产力方面几乎赶上了 Spring 框架，同时成功避免了 Spring 的陷阱。Java EE 的发布深受 Spring 框架的影响。

如今，Spring 作为一个平台，拥有 Spring Boot 框架，我称其为“Spring 的小型 Java EE”。Spring Boot 受到了 Java EE 的“约定优于配置”和“明智默认值”理念的影响。这两个平台相互影响深远。

作为一名 Java 开发者，你可以选择使用 Jakarta EE 或 Spring 框架。对我来说，两者没有孰优孰劣之分。两个平台各有其优势和劣势。自然，作为一本 Jakarta EE 书籍的作者，我选择 Jakarta EE，但这仅仅是因为我对它作为 Eclipse 基金会社区项目的未来感到兴奋，我觉得使用它很高效，并且我发现它易于学习和教授。

你应该根据平台对你的技术价值、你维护应用程序的难易程度、你对向后兼容性的重视程度、你开发团队的现有技能以及其他类似指标来选择平台。我对于整个 Spring 与 Java EE 之争的看法是，这根本不值得。它们都是优秀的平台，迎合了不同类型的开发者。Spring 框架背后的公司 Pivotal 现在已是 Jakarta EE 社区的成员。

Spring 框架与 Java EE 可以重新表述为 Spring 框架*和* Jakarta EE。作为开发者，你应该选择适合你具体情况的平台。既然你正在阅读这本书，我相信你已经决定要选择哪一个了。

脚注 1   2

