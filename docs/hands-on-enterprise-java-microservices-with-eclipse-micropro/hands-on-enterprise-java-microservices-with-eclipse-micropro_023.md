# MicroProfile 价值主张

对于信赖企业级 Java 来运行其生产工作负载的客户而言，Eclipse MicroProfile 提供了一套**供应商中立**的企业级 Java 微服务规范。它使客户能够通过更高的敏捷性和可扩展性、更快的上市时间、更高的开发效率、更轻松的调试和维护，以及持续集成与持续部署，更好地满足业务需求。

客户使用 Eclipse MicroProfile 所获得的好处，与使用微服务本身带来的好处是一致的。总体而言，根据备受尊敬的软件开发者、作家和演讲者 Martin Fowler 的观点，微服务提供了以下好处（[`martinfowler.com/articles/microservice-trade-offs.html`](https://martinfowler.com/articles/microservice-trade-offs.html)）：

*   **强模块边界**：微服务强化了模块化结构，这对于较大的团队尤其重要。
*   **独立部署**：简单的服务更容易部署，并且由于它们是自治的，当出现问题时，它们不太可能导致系统故障。
*   **技术多样性**：使用微服务，您可以混合使用多种语言、开发框架和数据存储技术。

除了微服务的通用好处之外，Eclipse MicroProfile 还特别提供了以下优势：

*   **社区协作的好处**：Eclipse MicroProfile 是一个由社区运营的开源项目。没有任何单一供应商控制或决定该规范的演进和成熟。
*   **实现选择的自由**：许多供应商已将 Eclipse MicroProfile 作为其软件栈的一部分来实现，客户可以选择最适合其环境的实现。
*   **更快的演进**：由于 Eclipse MicroProfile 是一个创新项目，新的和改进的功能会通过限时发布的版本频繁交付。这使得开发者和客户能够随时掌握这些功能，并尽早开始在其项目中利用这些更新。
*   **基于数十年的经验**：不仅该规范的领域专家带来了丰富的经验、专业知识和智慧，而且 Eclipse MicroProfile 还利用了其构建所基于的 Java EE API 中经过市场检验和生产验证的能力，为开发者提供了成熟度。
*   **对企业级 Java 的熟悉性**：Eclipse MicroProfile 建立在熟悉的企业级 Java 结构之上，使企业级 Java 开发者易于采用。
*   **无需重新培训**：您现有的企业级 Java 开发者会发现 Eclipse MicroProfile 是他们专业知识的自然延伸。几乎没有学习曲线。他们将能够利用自己的技能。
*   **互操作性**：不同的 MicroProfile 实现之间可以互操作，每个实现都为用户提供了自由，可以在一个应用程序中选择一个或组合多个 MicroProfile 实现。
*   **多种使用 API 的方式**：Eclipse MicroProfile API 提供了易于使用的接口，例如基于 CDI 的接口、编程式接口、命令行接口和基于文件（基于配置）的接口。
*   **一套完整的工件**：每个 API 都包含一个**测试兼容性工具包**（**TCK**）、Javadoc、可供下载的 PDF 文档、API Maven 工件坐标、Git 标签以及下载文件（规范和源代码）。
*   许多其他特定于每个 API 的好处。这些将在本书的每个 Eclipse MicroProfile 子项目章节中讨论。

