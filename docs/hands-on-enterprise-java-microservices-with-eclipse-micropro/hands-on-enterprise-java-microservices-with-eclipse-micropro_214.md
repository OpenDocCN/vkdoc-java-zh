# 第 2 章

1.  MicroProfile 社区的主要沟通渠道是他们的 Google 群组，名为 *Eclipse MicroProfile*。您可以通过发送电子邮件至 `microprofile@googlegroups.com` 来发布消息。另一种让您的声音被听到的好方法是参加每两周一次的 MicroProfile Hangout 电话会议。请查看 MicroProfile Google 日历以了解确切的日期、时间和会议信息来加入。
2.  MicroProfile Hangout 电话会议作为一个论坛，讨论与会者提出的议题并做出决策，内容涵盖从子项目状态和发布内容到发布日期和子项目创建批准等各个方面。

3.  一个子项目（MicroProfile 规范）的负责人或一组负责人通常是所涉主题的领域专家，并被指定为其推动者。需要注意的一个重要方面是，工作组（或子项目）的负责人并不能单方面塑造或决定规范的演进，也不能决定包含或不包含哪些功能。他们对于与其规范相关的决策没有否决权或最终决定权。通过分享想法、专业知识、过往经验、对现有技术的分析以及最佳实践，工作组将提出他们所能做到的最佳提案。
4.  在社区通过 MicroProfile Google 群组和/或 MicroProfile Hangout 电话会议对新想法进行充分讨论，并确定值得进一步辩论之后，社区决定为这个新想法创建一个工作组，并指定一位或一组通常是该主题领域专家的负责人作为其推动者。工作组将建立每周或每两周一次的例会，并录入 MicroProfile Google 日历。任何人都可以参加这些会议，但通常会有一些核心人员作为领域专家参与这些电话会议。经过几次会议后，工作组将决定是否应将新功能提交到 MicroProfile Hangout 电话会议，以讨论其成为 MicroProfile 子项目的提案。在 MicroProfile Hangout 电话会议上，子项目提案可能会被拒绝或接受。接受一个子项目意味着它有效地满足了某种需求，从而丰富了规范，使其朝着为微服务架构优化企业级 Java 的目标迈进。正是在这一刻，一个子项目成为了官方的 MicroProfile API。一旦子项目成为 MicroProfile API，就会决定它应该是一个独立于总括项目的子项目，还是包含在 MicroProfile 发布总括项目中的子项目。
5.  Eclipse MicroProfile 遵循一个有时间限制的快速增量发布计划，该计划是公开的，并列在 Eclipse Foundation MicroProfile 项目页面上。主要的 Eclipse MicroProfile 发布，例如从 1.x 到 2.x，包含对 MicroProfile API 的重大更新，可能会引入破坏性变更。次要发布，即小版本发布，包含小的 API 更新或在预定发布日期前完成的新 API。目前，MicroProfile 社区的发布窗口是每年二月、六月和十一月，用于次要和/或主要发布。

6.  沙盒仓库是一个 GitHub 仓库，用于孵化最终将变成独立仓库的想法和代码示例，从而为新的规范做出贡献。任何人都可以发起拉取请求并使用沙盒来试验新想法，以及共享代码和文档，这些可以作为社区 Google 群组、MicroProfile Hangout 电话会议或工作组会议讨论的一部分。
7.  在 MicroProfile 总括/平台发布之外发布子项目的原因是，它让社区和最终用户有机会使用和测试新技术，从而在实际应用中证明其价值，然后再考虑将其纳入总括项目。MicroProfile 项目鼓励新的子项目规范在考虑纳入总括/平台发布之前，至少先在总括项目之外发布一个版本。
8.  MicroProfile Starter 是一个示例源代码生成器，其目标是通过在 Maven 项目中生成可工作的示例代码，帮助开发者快速上手使用和利用社区驱动的企业级 Java 微服务开源规范 Eclipse MicroProfile 的功能。

