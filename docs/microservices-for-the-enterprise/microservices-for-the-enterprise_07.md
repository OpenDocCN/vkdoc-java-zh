# 7. 集成微服务

微服务架构提倡将软件应用程序构建为一组独立的服务。当我们需要实现一个特定的业务用例时，通常需要多个微服务之间的通信和协调。因此，集成微服务并构建服务间通信已成为实现微服务架构最具挑战性的任务之一。

在现有的大多数书籍和其他资源中，微服务集成的概念很少被讨论，或者以一种非常抽象的方式解释。因此，在本章中，我们将深入探讨集成微服务的关键挑战、集成微服务的模式，以及我们可以利用的框架和语言。



## 为何必须集成微服务

微服务旨在处理特定的细粒度业务功能。因此，当您使用微服务构建软件应用时，必须在这些服务之间建立通信结构。正如第 1 章“微服务的理由”中所讨论的，当您使用 SOA 时，您会构建一组服务（Web 服务），并通过一个称为企业服务总线（ESB）的中央总线来集成它们。

如图 7-1 所示，在基于 ESB 的方法中，您将业务逻辑以及各种网络通信功能（如弹性通信（断路器与超时）和服务质量方面）都构建为这个中央总线的一部分（我们将在本章后半部分详细讨论这些能力）。

![../images/461146_1_En_7_Chapter/461146_1_En_7_Fig1_HTML.jpg](img/461146_1_En_7_Fig1_HTML.jpg)

图 7-1

在 SOA 中使用 ESB 集成服务

当您使用 ESB 进行服务集成时，会得到一组紧密耦合到中央 ESB 运行时的虚拟服务。随着 ESB 层因业务和网络通信逻辑而不断膨胀，它在大多数企业中会变成一个巨大的单体应用。这意味着它具备我们在第 1 章中讨论过的单体应用的所有局限性。

当您转向微服务架构时，您仍然需要集成微服务来构建有意义的业务用例。微服务架构中有几个重要需求使得微服务集成变得至关重要。

*   *微服务组合*：从现有微服务中创建复合服务，并将其作为业务功能暴露给消费者，是微服务架构中最常见的用例之一。组合可以通过同步通信（主动）或异步（响应式）通信模式来构建。

*   *构建弹性服务间通信*：所有微服务调用都发生在网络上，并且容易失败。因此，在进行服务间调用时，我们需要实现稳定性和弹性模式。

*   *细粒度服务与 API*：大多数微服务过于细粒度，无法直接作为业务功能/API 发布给消费者。

*   *棕地企业中的微服务*：企业应用中的微服务需要与现有遗留系统、专有系统（例如 ERP 系统）、数据库和 Web API（例如 Salesforce）进行集成。

微服务架构倾向于采用一种替代集中式 ESB 的方法，即所谓的*智能端点和哑管道*。我们上面讨论的所有需求也需要在微服务中实现。让我们更仔细地看看智能端点和哑管道的概念。

## 智能端点和哑管道

采用*智能端点和哑管道*方法时，当我们集成微服务时，不应使用集中式的单体 ESB 架构，而应采用一种完全去中心化的方式，让服务通过哑消息传递基础设施进行通信。所有智能都存在于端点（服务和消费者）中，而中间消息传递通道不包含任何业务或网络通信逻辑。因此，如图 7-2 所示，微服务的集成由另一组微服务负责处理。它们负责集成逻辑以及调用这些服务所需的网络通信。这些服务可能使用不同的技术构建，并且与 ESB 不同，每个集成服务都是自治的。

![../images/461146_1_En_7_Chapter/461146_1_En_7_Fig2_HTML.jpg](img/461146_1_En_7_Fig2_HTML.jpg)

图 7-2

智能端点和哑管道：所有智能都存在于端点（服务）层面，而它们通过哑消息传递基础设施进行通信

尽管这种方法看起来比传统的集中式 ESB 优雅得多，但开发人员仍需应对若干复杂性。首先，我们必须清楚理解，这种方法并没有消除 ESB 方法中业务或网络通信的任何复杂性。这意味着您需要将服务集成逻辑所需的所有能力都作为服务的一部分来实现。例如，微服务-1 应包含来自`订单处理`和`购物车`微服务的多种数据类型的组合，以及调用这些服务所需的弹性通信（如断路器、故障转移等）。它还必须包含您需要的任何其他横切能力（如安全性和可观测性）。此外，如果您使用多语言微服务技术，那么对于诸如弹性通信等通用功能，您很可能需要使用多种技术重复相同的实现。

在选择实现技术时，将这些微服务集成需求纳入考量至关重要。我们将在本章后半部分深入探讨这些需求的具体细节以及满足这些需求的技术。但在此之前，有必要讨论一些您应该避免的、与微服务集成相关的常见反模式。

## 微服务集成的反模式

在集成微服务时，有几种反模式需要我们注意。这些模式大多是由于集成微服务的复杂性，以及试图在微服务架构中复制集中式 ESB 所提供的同一套功能而产生的。



### 用于微服务集成的单体 API 网关

一种常见的反模式是使用 API 网关作为服务集成（或组合）层，向消费者暴露业务服务。例如，假设你开发了多个微服务，而你想要暴露的业务功能需要多个服务之间进行某种协作（或编排）。你需要构建的是一个复合微服务，它能够与多个下游服务通信，并暴露复合功能。在许多微服务实现中，我们将集成逻辑作为 API 网关的一部分来开发，而 API 网关或多或少是一个单体组件。现有的微服务实现中有许多真实案例。例如，图 7-3 展示了 Netflix API 网关^(⁹⁶)最初是如何实现的。

![../images/461146_1_En_7_Chapter/461146_1_En_7_Fig3_HTML.jpg](img/461146_1_En_7_Fig3_HTML.jpg)

图 7-3

Netflix API 网关：服务集成在 API 网关层完成，多个 API 属于单体 API 网关层的一部分

Netflix 可能是目前最流行且最成功的微服务实现。Netflix 通过 Netflix API 层暴露其内部服务。他们这样描述 Netflix API 的功能：

> *Netflix API 是 Netflix 微服务生态系统的“前门”。当请求从设备端到达时，API 提供编排调用所有构建响应所需服务的逻辑。它从后端服务收集所需的任何信息，按需要的顺序进行，根据需要格式化并过滤数据，然后返回响应。因此，Netflix API 的核心是一个编排服务，它通过组合微服务提供的细粒度功能来暴露粗粒度的 API。*

你可以清楚地观察到，在这种场景下，编排层（一个单体组件）包含了相当大一部分业务逻辑。这导致了我们在前几章讨论过的与单体应用相关的众多权衡（例如，无故障隔离、无法独立扩展、所有权问题等）。

Netflix 已经认识到这种方法的缺点，并为完全相同的场景引入了一种新的架构，即采用分离的 API 网关层，该层不再是单体结构。如图 7-4 所示，在 API 网关层，每个组合服务都作为一个独立的实体实现。

![../images/461146_1_En_7_Chapter/461146_1_En_7_Fig4_HTML.jpg](img/461146_1_En_7_Fig4_HTML.jpg)

图 7-4

具有独立 API 的 Netflix API 网关，用于集成微服务

这种方法与引入一个不属于单体运行时的集成服务，并将 API 网关相关功能作为服务运行时的一部分来实施，几乎完全相同。他们还尝试了另一种替代方案，即尽可能保持 API 网关的“哑”特性，并引入一个复合服务，让 API 网关仅充当透传运行时。

从这个用例中得出的关键结论是，你不应该将 API 网关用作放置业务逻辑的单体运行时。服务集成或组合逻辑必须是另一个微服务的一部分（无论是在 API 网关层还是在服务层）。

### 使用 ESB 集成微服务

有些微服务实现通过使用 ESB 作为运行时来实现服务集成，从而将 ESB 带回了微服务架构。在大多数情况下，ESB 被部署在容器中，以服务于特定用例的服务集成。然而，ESB 有其固有的局限性，例如过于庞大而无法作为容器运行，由于基于配置的集成而对开发者不够友好等。事实上，确实有一些 ESB 供应商试图推广这种模式，但这是你在集成微服务时应该避免的。（也存在对容器友好且轻量级的 ESB 版本，可用于独立集成微服务，这比使用中心化的 ESB 要好得多。）

### 使用同质化技术构建所有微服务

我们之前讨论过，智能端点和哑管道字面意思就是，所有我们开箱即用从 ESB 获得的酷炫功能现在都必须作为服务逻辑的一部分来实现。在开发微服务时，我们需要考虑到并非所有微服务都是相似的。有些服务会更侧重于业务逻辑和计算，而有些服务则更关注服务间通信和网络调用。如果我们坚持使用单一的同质化技术集来构建所有这些微服务，那么我们将不得不投入更多精力来构建集成微服务的核心组件，而不是专注于服务的业务逻辑。例如，服务集成通常需要服务发现和弹性通信（如断路器）。有些框架或编程语言开箱即用地提供了这些能力，而有些则没有。因此，你的架构应该足够灵活，以便为工作选择合适的技术。

## 组织微服务

根据交互方式识别不同类型的微服务，并使用最合适的技术来构建它们，是构建成功微服务架构的关键。如果我们更仔细地审视微服务实现，我们可以识别出不同类型的服务，并将其归类为几个不同的类别。基于服务功能和粒度，我们可以识别出以下服务类别。

### 核心服务

有些微服务是细粒度的、自包含的（没有外部服务依赖），并且主要由业务逻辑组成，几乎没有或完全没有网络通信逻辑。鉴于这些服务没有显著的网络通信功能，你可以自由选择任何能够满足服务业务逻辑的服务实现技术。此外，这些服务可能拥有自己的私有数据库，用于构建业务逻辑。这类微服务可以归类为*核心*或*原子*微服务。



### 集成服务

核心微服务通常过于细粒度，无法直接映射到业务功能。任何实际的业务能力都需要多个微服务之间的交互或组合。这种交互或组合以*集成服务*或复合服务的形式实现。这些服务通常需要在服务层面自身支持企业服务总线（ESB）的相当一部分功能，例如路由、转换、编排、弹性和稳定性模式等。

集成服务提供复合业务功能，彼此独立，并包含业务逻辑（路由、调用哪些服务、如何进行数据类型映射等）和网络通信逻辑（通过多种协议进行服务间通信，以及断路器之类的弹性行为）。此外，它们可能有也可能没有与服务业务功能相关的私有数据库。这些服务可以桥接其他遗留和专有系统（例如，ERP 系统）、外部 Web API（例如，Salesforce）、共享数据库等（通常被称为防腐层）。

为构建集成微服务选择恰当的服务开发技术至关重要。由于网络通信是集成服务的关键部分，您应选择最合适的技术来实现这些服务。在本章后半部分，我们将讨论适合构建这些服务的技术和框架。

### API 服务

您将使用 API 服务或边缘服务，将选定的部分复合服务甚至一些核心服务作为托管 API 暴露出来。这些服务是一种特殊类型的集成服务，应用了基本的路由能力、API 版本管理、API 安全、限流、货币化、API 组合等。

在大多数微服务实现中，API 服务是作为单体 API 网关运行时的一部分实现的，这违反了核心微服务架构概念。然而，现在大多数 API 网关解决方案正朝着微网关能力发展，您可以在独立且轻量级的运行时上部署 API 服务，同时进行集中管理。在实现方面，其需求与集成服务非常相似，并且我们需要一些额外的特性。我们将在第 10 章“API、事件和流”中更广泛地讨论 API 服务和 API 管理。

既然我们已经很好地理解了不同类型的微服务，接下来让我们讨论一些常用的微服务集成模式。

## 微服务集成模式

我们已经在不同的微服务类别中找到了接缝，现在是时候看看它们如何在实际应用中被使用了。在集成微服务时，我们可以识别出几种集成模式。让我们详细讨论它们，并分析其优缺点以及何时使用这些模式。

### 主动组合或编排

微服务集成可以通过这样一种方式实现：一个给定的（集成）微服务主动调用其他几个服务（可以是核心服务或复合服务）。业务逻辑和网络通信作为集成服务的一部分构建。集成微服务应通过其执行的组合来形成业务功能。例如，如图 7-5 所示，`microservice-1`同步调用`microservice-4`和`microservice-5`。`microservice-1`提供的业务能力是`microservice-4`和`microservice-5`能力的组合。此外，我们开发的集成服务可以通过 API 网关层作为 API 暴露出来。

![../images/461146_1_En_7_Chapter/461146_1_En_7_Fig5_HTML.jpg](img/461146_1_En_7_Fig5_HTML.jpg)

图 7-5

微服务的主动组合：一个给定的集成微服务调用多个微服务并形成一种业务能力

这里的关键概念是，通过主动组合，我们创建了一个依赖于其他一组微服务的集成服务。如果你考虑我们在前几章讨论的理论方面，这似乎违反了微服务原则。但是，如果不依赖其他服务和系统，几乎不可能构建任何有用的东西。这里真正重要的是理解这些服务之间的边界，并清晰地定义其能力。

当我们需要在集中式服务中控制服务集成，并且依赖服务之间的通信是同步时，通常会使用主动组合。一旦你为集成服务清晰地定义了业务能力，其业务逻辑就驻留在单个服务中。这使得管理和维护变得相当容易。

### 注意

同步通信并不意味着实现基于阻塞通信模型。我们可以在完全非阻塞实现的基础上构建同步通信，在这种实现中，线程不会在给定的请求-响应交互上被阻塞。我们可以利用非阻塞编程模型来实现这种同步通信模式。

如果你有异步或事件驱动的用例，这种方法可能不是最佳选择。服务之间的依赖关系可能对某些业务用例造成问题。即使你使用非阻塞技术来实现同步通信，请求也会受限于所有依赖服务的延迟。例如，如果调用了一个给定的集成服务，它将受限于所有依赖服务产生的延迟总和。

### 响应式组合或编排

采用响应式通信风格，我们没有同步调用其他服务的服务。相反，服务之间的所有交互都使用异步事件驱动的通信风格实现。例如，如图 7-6 所示，微服务与消费者应用程序之间的通信是通过事件驱动的异步消息传递完成的。因此，我们需要使用事件总线作为消息传递骨干。事件总线是一个哑消息传递基础设施，所有逻辑都驻留在服务层面。

![../images/461146_1_En_7_Chapter/461146_1_En_7_Fig6_HTML.jpg](img/461146_1_En_7_Fig6_HTML.jpg)

图 7-6

采用异步事件驱动通信的响应式组合

如第 3 章“服务间通信”所述，通信可以是基于队列的（单个消费者）或发布-订阅（多个消费者）。根据你的需求，你可以使用 Kafka、RabbitMQ 或 ActiveMQ 等作为事件总线。

响应式组合使微服务天生具有自治性。由于我们没有包含集中式组合逻辑的服务，这些微服务彼此不依赖。它们仅在给定事件发生时被激活，然后处理消息，并在结果发布到事件总线后完成工作。



### 注意

事件流处理或复杂事件处理可被视为处理事件流的更强大方式。此处我们仅讨论了基于事件的消息传递。我们将在第 10 章详细讨论事件流处理。

这种方法的主要权衡，例如通信的复杂性以及业务逻辑不集中在单一服务中，使得系统极难理解。由于我们使用了事件总线/消息总线，我们编写的所有服务都必须具备发布和订阅事件总线的能力。此外，如果没有跨所有服务的全面可观测性，就很难理解响应式组合所实现的交互和业务逻辑。

### 主动与响应式组合的混合

主动和响应式组合风格各有其优缺点。根据大多数务实微服务实施的经验，某些场景下主动组合最为合适，而另一些场景下响应式组合则不可或缺。我们的建议是根据微服务集成用例，采用这两种方法的混合模式。例如，图 7-7 展示了这两种风格的混合。

![../images/461146_1_En_7_Chapter/461146_1_En_7_Fig7_HTML.jpg](img/461146_1_En_7_Fig7_HTML.jpg)

图 7-7

主动与响应式微服务集成的混合

通常，有些服务会以完全同步的 API 形式暴露给消费者。此类 API 调用将触发多个其他微服务调用，其中部分调用可以/应该采用响应式方法进行。正如第 3 章所讨论的，在零售业务用例中，订单下达和处理等场景采用响应式风格实现会更为优雅。因此，您需要根据业务用例来选择要使用的风格。

对于大多数企业级微服务集成而言，混合组合通常更为务实。

### 防腐层

您可以在企业中引入微服务架构，即使其部分子系统仍基于单体架构。我们可以在微服务与单体子系统之间构建一个外观层或适配器层。在第 2 章“设计微服务”中，我们讨论了防腐层，它允许您独立开发微服务组件或现有单体应用。构建在这两种不同架构风格之上的应用可以通过防腐层进行交互。单体部分和微服务部分所使用的技术和标准可能截然不同。这就是在构建微服务集成时通常需要构建防腐层的原因。

例如，在我们于图 7-7 中讨论的混合组合用例中，`microservice-3` 将微服务子系统与专有、遗留及外部 Web API（这些都属于单体子系统的一部分）集成在一起。该服务是防腐层的一部分。通常，用于构建集成微服务的技术也可用于构建防腐层的服务。

### 绞杀者外观

在企业微服务的背景下，您经常需要处理现有的非微服务子系统。通过引入微服务架构，您将逐步替换大部分现有子系统。然而，这并非一蹴而就之事。*绞杀者模式*提出了一种方法，帮助您通过逐步用新的微服务替换特定功能模块，来增量迁移非微服务子系统。如果您正在为企业引入微服务，您将构建一个绞杀者外观，有选择性地在现代系统与遗留系统之间路由流量。随着时间的推移，您将用新的微服务完全替换遗留系统，并移除绞杀者层。

## 集成服务的关键需求

至此，您已充分理解集成微服务和微服务集成模式的重要性。让我们深入探讨可用于实现这些模式的技术。但在深入之前，明确构建集成微服务的具体需求将大有裨益。

### 网络通信抽象

正如我们在第 3 章中详细讨论的，微服务间通信对于构建基于微服务的应用至关重要。服务是自治的，它们交互并形成业务功能的唯一方式就是通过服务间通信。因此，对于集成服务，我们必须支持不同的通信模式，如同步和异步通信以及相关的网络协议。

在实践中，对于同步通信，RESTful 服务被广泛使用，因此对 RESTful 服务和 HTTP 1.1 的原生支持至关重要。此外，许多服务实现框架现在利用 HTTP2 作为默认通信协议，以受益于 HTTP2 引入的所有新功能。

在同步通信的背景下，gRPC 服务正在普及，大多数微服务实现将其用作内部微服务通信的事实标准。鉴于 gRPC 和协议缓冲区^(⁹⁷)适用于多语言微服务实现，它们天然满足了使用不同语言构建的微服务的大部分服务间通信需求。

异步服务集成主要围绕基于队列的通信（单一接收者）构建，AMQP 等技术在实践中相当常用。对于发布-订阅（事件驱动的多接收者通信），Kafka 已成为服务间通信的事实标准。

到目前为止，我们讨论的内容涵盖了微服务间通信常用的标准和最新技术。那么，我们如何与企业微服务生态系统中的遗留或专有系统通信呢？事实上，微服务实现技术也必须满足这些遗留和专有集成的用例。例如，如果您在企业中使用 ERP 系统，不与其交互就无法构建有用的应用。因此，如有必要，微服务必须能够与此类遗留或专有系统通信。这引导我们思考，微服务实现技术应能处理 ESB 所支持的任何网络通信协议。

> *您在集中式 ESB 中开发的功能现在必须在您的集成微服务中实现。微服务实现技术应满足 ESB 提供的所有能力。*

除了基本的网络通信协议，微服务通常还需要与 Web API 集成，例如 Twitter、Salesforce、Google Docs、PayPal、Twilio 等。虽然有提供网络可访问 API 的 SaaS 应用，但大多数集成产品（如 ESB）都提供了高级抽象，使您能够以最少的工作量集成这些系统。最终，集成微服务实现技术需要具备一定程度的抽象来集成此类 Web API。（例如，用于访问 Twitter API、PayPal API 等 Web API 的库或连接器。）



### 弹性模式

如第 2 章所述，分布式计算的关键谬误之一便是网络是可靠的。微服务间的通信或集成微服务必须始终关注在不可靠网络上进行微服务通信的问题。

> *网络从此刻到永远，都将不可靠。——迈克尔·尼加德，《发布它！》*

迈克尔·尼加德在其著作《发布它！》中讨论了多种与在不可靠网络上进行应用间通信相关的模式。在本章中，我们将更深入地审视这些模式的行为，试图通过真实世界的用例来理解它们，并探讨一些实现细节。

#### 超时

当我们使用同步服务间通信时，一个服务（调用方）发送请求并等待及时响应。超时就是在调用方服务层面决定何时停止等待响应。当你使用特定协议（例如 HTTP）调用另一个服务或系统时，可以指定一个超时时间，如果达到该超时时间，你可以定义特定的逻辑来处理此类事件。

务必牢记，超时是应用层面的事情，不应与协议层面的任何类似实现相混淆。例如，当某个集成微服务调用微服务 A 和 B 时，该集成微服务可以为服务 A 和 B 分别定义超时值。超时有助于服务隔离故障。另一个系统或服务的异常行为或故障不必成为你服务的问题。当你在调用外部服务时设置超时，并拥有处理此类事件的特定逻辑，这将更容易地隔离故障并优雅地处理故障。

#### 断路器

当你调用外部服务或系统时，它们可能因各种错误而失败。在这种情况下，你可能希望用一个对象来包装该调用，该对象能监控并防止对系统造成进一步损害。断路器就是这样的包装对象，我们可以在调用外部服务和系统时使用它们。使用断路器背后的主要思想是，如果服务调用失败并达到某个阈值，那么断路器包装器会阻止对外部服务的任何进一步调用。相反，它会立即从断路器返回一个错误。图 7-8 展示了断路器在闭合和断开状态下的行为。

![../images/461146_1_En_7_Chapter/461146_1_En_7_Fig8_HTML.jpg](img/461146_1_En_7_Fig8_HTML.jpg)

**图 7-8** 断路器的行为。当电路闭合时，断路器包装对象允许服务调用通过并到达外部服务。当电路断开时，它阻止对外部服务的调用并立即返回。

当发生调用失败时，断路器会保持该状态并更新阈值计数，并根据阈值计数或失败计数的频率来断开电路。当电路断开时，对外部服务的实际调用被阻止，断路器生成一个错误并立即返回。

当电路处于断开状态一段时间后，我们可以通过在一个合适的间隔后再次尝试服务调用（针对新请求），并在成功时重置断路器，来应用一种自复位行为。这个时间间隔被称为电路复位超时。通过这种行为，我们可以在断路器中识别出三种不同的状态，如图 7-9 所示。

![../images/461146_1_En_7_Chapter/461146_1_En_7_Fig9_HTML.jpg](img/461146_1_En_7_Fig9_HTML.jpg)

**图 7-9** 断路器的状态

我们可以认为，当达到复位超时时，电路状态变为半开状态，此时断路器允许对到达微服务的新请求再次调用外部服务。如果调用成功，断路器将再次变为闭合状态，否则变为断开状态。

按设计，断路器是一种在系统性能不佳或发生故障时（而不是让整个系统崩溃）降低系统性能的机制。它将防止对系统造成任何进一步损害或级联故障。我们可以使用各种退避机制、超时、复位间隔以及错误码（这些错误码应直接导致电路进入断开状态，或应被忽略）来调整断路器。这些行为通过不同的断路器实现，在不同复杂度的层面上得以实现。务必牢记，断路器行为与特定基于微服务的应用的业务需求有着密切关系。

#### 快速失败

在快速失败模式中，关键目标是尽快检测到故障。它建立在这样一个概念之上：一个失败响应远胜于一个缓慢的失败响应。因此，在服务间通信的早期阶段检测故障是一个重要因素。我们可以在服务间通信的不同阶段检测故障。在某些情况下，仅通过查看请求/消息的内容，我们就能判断该请求是无效的。在其他情况下，我们可以检查系统资源（例如线程池、连接、套接字限制和数据库）以及请求生命周期中下游组件的状态。

快速失败与超时相结合，将有助于我们开发稳定且响应迅速的基于微服务的应用。

#### 隔板

隔板是一种对应用进行分区隔离的机制，使得某个分区中发生的错误仅局限于该分区。它不会导致整个系统进入不稳定状态；只有那个分区会失败。在微服务的核心设计原则中，隔板模式被广泛使用。当我们设计微服务时，我们有意将相似的操作分组到一个微服务中，并将独立的业务功能实现为单独的微服务。因此，微服务被独立部署在不同的运行时（虚拟机或容器）上，这意味着某个特定功能的故障不会影响其他功能。

然而，如果出于某种原因，你不得不在单个服务内部实现两个或更多业务功能，你需要采取预防措施来对服务进行分区，以便特定业务操作集合的故障不会影响其余操作。通常，建议识别出此类独立操作，并在可能的情况下将其转换为微服务。但是，如果你无法将它们拆分为服务，则有一些技术可以在单个服务/应用内部实现隔板。例如，我们可以拥有专用的资源（如线程池、存储或数据库）来处理服务的不同分区。



#### 负载均衡与故障转移

负载均衡和故障转移背后的核心理念相当简单。负载均衡用于将负载分发到多个微服务实例，而故障转移则用于在某个服务发生故障时，将请求重新路由到备用服务。在传统的中间件实现（如 ESB）中，这些功能也作为服务逻辑的一部分来实现。然而，随着容器和容器管理系统（如 Kubernetes）的进步，这些功能中的大部分现已内置于部署生态系统本身。此外，大多数云基础设施供应商，例如亚马逊云服务（AWS）、谷歌云和 Azure，都将其作为基础设施即服务（IaaS）产品的一部分提供。我们将在第 8 章“部署和运行微服务”中详细讨论容器和 Kubernetes。

### 主动或响应式组合

正如我们在微服务集成模式部分所讨论的，构建主动或响应式服务组合对于任何实际的微服务实现都至关重要。因此，微服务集成技术应支持构建主动和响应式组合。在实现层面，这意味着能够通过不同协议调用服务，使用诸如断路器之类的支持组件，并创建复合业务逻辑。对于主动组合，支持同步服务调用（在带有回调的非阻塞线程之上实现）非常重要。对于响应式组合，则需要支持发布-订阅和基于队列的消息传递等消息传递风格，并与 Kafka 或 RabbitMQ 等消息主干无缝集成。此外，需要在服务层面实现不同的消息交换模式——并且需要能够混合和匹配这些模式。例如，入站请求可能是一个异步消息，而外部（出站）服务调用是同步的。因此，我们应该能够混合和匹配这些消息交换模式。

### 数据格式

在构建组合时，我们必须处理来自不同数据格式的组合。例如，一个给定的微服务会通过一种数据格式（针对入站请求）暴露，同时它会调用其他服务（出站），而这些服务可能使用不同的数据格式。当我们创建这些服务的组合时，必须在这些数据格式之间进行类型匹配，并以类型安全的方式实现我们的服务。因此，我们使用的服务实现技术应该关注所有不同的数据格式，并提供一种便捷的方式来处理这些格式。JSON、Avro、CSV、XML、协议缓冲区等数据格式在实践中被广泛使用。

### 容器原生与 DevOps 就绪

我们使用的微服务开发技术应该是云原生和容器原生的。这同样适用于集成服务。在构建集成服务时，您使用的开发技术必须是云原生和容器原生的。当我们讨论微服务集成的反模式时，强烈不鼓励使用 ESB 来集成微服务。其背后的主要原因是，几乎所有的 ESB 技术都不是云原生或容器原生的。

一项技术要成为云原生或容器原生，其运行时应在几秒（或更短）内启动，内存占用、CPU 消耗和所需存储空间必须极低。因此，在选择微服务集成技术时，我们应考虑所有这些方面。

除了运行时的容器原生特性外，集成微服务开发技术还必须关注与容器和容器管理系统（如 Kubernetes）的原生集成。这意味着，您开发的应用程序或服务可以多么轻松地创建出容器。在您的服务开发技术中支持配置和创建容器相关工件，将极大地提高微服务开发过程的敏捷性。我们将在第 8 章中详细介绍容器、Docker 和 Kubernetes。

### 集成服务的治理

我们在第 6 章“微服务治理”中涵盖了微服务的治理方面。当我们构建微服务集成时，某些治理方面（如可观测性）极其关键。例如，让我们重新审视之前讨论过的混合组合场景（见图 7-10）。

![../images/461146_1_En_7_Chapter/461146_1_En_7_Fig10_HTML.jpg](img/461146_1_En_7_Fig10_HTML.jpg)

图 7-10

复杂的微服务集成必须具有可观测性

图 7-10 显示，您可以清晰地看到微服务之间的所有交互，并且可以清楚地了解所有业务用例。但是，请思考一下这些交互在操作层面看起来是怎样的。如果我们没有适当的可观测性机制，我们将无法获得这种视图。作为集成服务开发技术的一部分，我们需要一种无缝的方式来集成现有的可观测性工具，以获取您的集成服务的指标、追踪、日志、服务可视化和告警。

### 无状态、有状态或长时间运行的服务

微服务设计倾向于无状态的不可变服务，大多数用例都可以通过使用此类无状态服务来实现。然而，还有许多用例，例如业务流程、工作流等，需要有状态和长时间运行的服务。在传统的集成中间件背景下，这些需求是在 ESB 或业务流程解决方案层面实现和支持的。对于微服务，这些需求必须从头开始构建，因此在集成微服务层面原生支持这些能力将非常有用。

构建工作流、业务流程以及使用 SAGA（在第 5 章“数据管理”中讨论）进行分布式事务的能力，是有状态和长时间运行服务的一个关键需求。

## 构建集成服务的技术

从本章到目前为止的讨论中，应该清楚，没有一种银弹技术可以用来构建微服务。存在不同类型的微服务，每种微服务都解决一组截然不同的需求。因此，为了实现这些微服务，我们需要利用多语言微服务开发技术。在本节中，我们将讨论一些最常用的微服务开发技术；那些更适合构建微服务组合或集成微服务的技术。

有些微服务框架构建在通用编程语言（如 Java）之上，并通过不同的技术提供抽象来满足微服务组合的需求。另一方面，集成框架并非专门针对构建微服务（而是旨在解决通用的企业集成需求），但它们仍然可以用于集成微服务。此外，某些编程语言开箱即用地满足了此类微服务集成需求。

让我们仔细看看一些在实践中常用的微服务开发框架、集成框架和通用编程语言。



### 注意

如果在构建或运行本书提供的示例时遇到任何问题，请参考 Git 仓库中对应章节下的 README 文件：[`https://github.com/microservices-for-enterprise/samples.git`](https://github.com/microservices-for-enterprise/samples.git)。我们将更新 Git 仓库中的示例及相应的 README 文件，以反映与本书所用工具、库和框架相关的任何变更。

### Spring Boot

Spring Boot 是一个基于 Spring 平台构建的微服务框架，它通过对 Spring 平台和第三方库采取一种**约定优于配置**的观点，让你能以最少的麻烦快速上手。Spring Boot 使得创建独立的、生产级的、基于 Spring 的应用程序变得简单，你*只需运行*即可。你可以编写大多数 Spring Boot 微服务，而无需或只需极少的 Spring 配置。Spring Boot 通过提供以下能力，力求让微服务的实现变得微不足道：

*   创建独立的 Spring 应用程序。
*   直接嵌入 Tomcat、Jetty 或 Undertow（无需部署 WAR 文件）。
*   提供约定优于配置的 *starter* POM 来简化你的 Maven 配置。
*   尽可能自动配置 Spring。
*   提供生产就绪的功能，如指标、健康检查和外部化配置。
*   绝对没有代码生成，也不需要 XML 配置。

让我们讨论一下 Spring Boot 提供的、对于集成微服务至关重要的一些关键特性。

#### RESTful 服务

你可以使用 Spring Boot 的服务开发特性来构建组合微服务。例如，你可以使用 Spring Boot 构建一个简单的 RESTful 服务，如下所示。

假设你需要构建一个 HTTP RESTful 服务，该服务处理针对 `/greeting` 端点的 `GET` 请求，并且可选地在查询字符串中包含一个 name 参数。`GET` 请求应返回一个 `200 OK` 响应，其主体中包含一个表示问候语的 JSON 负载。作为第一步，为了对问候语进行建模，你需要如下定义一个问候语的表示形式。

```
package com.apress.ch07;
public class Greeting {
private final long id;
private final String content;
public Greeting(long id, String content) {
this.id = id;
this.content = content;
}
public long getId() {
return id;
}
public String getContent() {
return content;
}
}
```

然后，你可以创建用于处理问候请求的服务和资源。在 Spring 构建 RESTful Web 服务的方法中，我们有一个控制器来处理 HTTP 请求。这些组件通过 `@RestController` 注解很容易被识别，而 `GreetingController` 类通过返回 `Greeting` 类的新实例来处理针对 `/greeting` 端点（上下文）的 `GET` 请求：

```
package com.apress.ch07;
import java.util.concurrent.atomic.AtomicLong;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;
@RestController
public class GreetingController {
private static final String template = "Hello, %s!";
private final AtomicLong counter = new AtomicLong();
@RequestMapping("/greeting")
public Greeting greeting(@RequestParam(value="name",
defaultValue="World") String name) {
return new Greeting(counter.incrementAndGet(),
String.format(template, name));
}
}
```

由于响应格式是一个 POJO，它会被显式转换为 JSON。如果你想控制这一点，可以在请求映射级别使用 `@GetMapping(path = "/hello", produces=MediaType.APPLICATION_JSON_VALUE)` 来实现。

你可以从我们 `ch07/sample01` 中的示例中尝试这个例子。

#### 网络通信抽象

Spring Boot 通过众多抽象来支持通过网络消费和生成数据，如下所述。

##### HTTP

你可以使用 Spring REST 模板来消费 RESTful 服务。这里我们指定了需要将响应转换成的 POJO。`getForObject` 通过对 URL 执行 `GET` 操作来检索表示形式。响应（如果有）会被转换并返回。

```
RestTemplate restTemplate = new RestTemplate();
Quote quote = restTemplate.getForObject("http://gturnquist-quoters.cfapps.io/api/random", Quote.class);
log.info(quote.toString());
```

类似地，`RestTemplate` 也支持其他 HTTP 动词，如 `POST`、`PUT` 和 `DELETE`。在创建暴露 RESTful 服务的服务时，你可以使用 Spring 对嵌入 Tomcat Servlet 容器作为 HTTP 运行时的支持，而不是部署到外部实例。你可以从我们 `ch07/sample02` 中的示例中尝试这个例子。

##### JMS

Spring Boot 还提供了与其他网络通信协议和系统集成的抽象。例如，你可以创建一个通过 JMS 消费消息的服务，如下所示：

```
@JmsListener(destination = "mailbox", containerFactory = "myFactory")
public void receiveMessage(Email email) {
System.out.println("Received ");
}
```

`JmsListener` 注解定义了此方法应监听的 `Destination` 的名称，并且对 `JmsListenerContainerFactory` 的引用用于创建底层的消息监听器容器。除非你需要自定义容器的构建方式，否则不必向 `containerFactory` 属性传递值，因为 Spring Boot 会在必要时注册一个默认工厂。

消息可以使用 `JMSTemplates` 来生成。

```
public class JmsQueueSender {
private JmsTemplate jmsTemplate;
private Queue queue;
public void setConnectionFactory(ConnectionFactory cf) {
this.jmsTemplate = new JmsTemplate(cf);
}
public void setQueue(Queue queue) {
this.queue = queue;
}
public void simpleSend() {
this.jmsTemplate.send(this.queue, new MessageCreator() {
public Message createMessage(Session session) throws JMSException {
return session.createTextMessage("hello queue world");
}
});
}
}
```

`JmsTemplate` 包含许多发送消息的便捷方法。有些 send 方法使用 `javax.jms.Destination` 对象指定目标，有些则使用字符串指定目标以用于 JNDI 查找。你可以从我们 `ch07/sample03` 中的示例中尝试这个例子。

##### 数据库/JDBC

为了通过 JDBC 将你的微服务与数据库集成，Spring 提供了一个名为 `JdbcTemplate` 的模板类，它使得使用 SQL 关系数据库和 JDBC 变得容易。大多数通用的 JDBC 代码充满了资源获取、连接管理、异常处理和常规错误检查，这些与代码本应实现的目标完全无关。`JdbcTemplate` 为你处理了所有这些。

```
jdbcTemplate.query(
"SELECT id, first_name, last_name FROM customers WHERE first_name = ?", new Object[] { "Josh" },
(rs, rowNum) -> new Customer(rs.getLong("id"), rs.getString("first_name"), rs.getString("last_name"))
).forEach(customer -> log.info(customer.toString()));
```

你可以从我们 `ch07/sample04` 中的示例中尝试这个例子。除了我们提到的，Spring 还提供了与众多其他网络协议集成的能力。

##### Web API：Twitter

与诸如 Twitter 之类的 Web API 集成是 Spring Boot 专用于每个 Web API 的库所支持的功能。例如，从你的 Spring Boot 微服务连接并发布推文非常简单。你只需要初始化 `TwitterTemplate` 并调用其所需操作即可。你可以从我们 `ch07/sample05` 中的示例中尝试这个例子。

```
Twitter twitter = new TwitterTemplate(consumerKey, consumerSecret);
twitter.timelineOperations().updateStatus("Microservices for Enterprise.!")
```

如你所见，Spring Boot 提供了最全面的能力集之一，用于将你的微服务与其他系统和 API 集成。



#### 弹性模式

Spring Boot 利用 Netflix Hystrix 等库来实现具有弹性的微服务通信。Spring Cloud Netflix Hystrix 实现会查找任何使用 `@HystrixCommand` 注解标记的方法，并将该方法封装在连接到断路器的代理中，以便 Hystrix 对其进行监控。例如，在以下代码片段中，调用外部 RESTful 服务的方法使用了 `@HystrixCommand` 注解进行标记。

```
package hello;
import com.netflix.hystrix.contrib.javanica.annotation.HystrixCommand;
import org.springframework.stereotype.Service;
import org.springframework.web.client.RestTemplate;
import java.net.URI;
@Service
public class BookService {
private final RestTemplate restTemplate;
public BookService(RestTemplate rest) {
this.restTemplate = rest;
}
@HystrixCommand(fallbackMethod = "reliable")
public String readingList() {
URI uri = URI.create("http://localhost:8090/recommended");
return this.restTemplate.getForObject(uri, String.class);
}
public String reliable() {
return "Microservices for Enterprise (APress)";
}
}
```

我们将 `@HystrixCommand` 应用到了原始的 `readingList()` 方法上。这里我们还有一个新方法，叫做 `reliable()`。`@HystrixCommand` 注解将 `reliable` 指定为其 `fallbackMethod`，因此，如果由于某种原因，Hystrix 断开了 `readingList()` 的电路，我们将有一个默认结果可以显示。你可以从我们 `ch07/sample06` 中的示例中尝试这个例子。

#### 数据格式

Spring Boot 允许你编写微服务，使其能够主要通过使用 Jackson^(⁹⁸) 数据处理工具来生成、消费和转换多种数据格式。

```
// Java 对象转 JSON
ObjectMapper objectMapper = new ObjectMapper();
Car car = new Car("yellow", "renault");
objectMapper.writeValue(new File("target/car.json"), car);
// JSON 转 Java 对象
String json = "{ \"color\" : \"Black\", \"type\" : \"BMW\" }";
Car car = objectMapper.readValue(json, Car.class);
```

Jackson 为多种数据类型提供了一套全面的数据处理能力，包括旗舰级的流式 JSON 解析器/生成器库、匹配的数据绑定库（POJO 与 JSON 之间的转换），以及用于处理以 Avro、BSON、CBOR、CSV、Smile、(Java) Properties、Protobuf、XML 或 YAML 编码的数据的附加数据格式模块。它甚至提供了大量的数据格式模块来支持广泛使用的数据类型，例如 Guava、Joda、Pcollections 等等。你可以从我们 `ch07/sample07` 中的示例中尝试这个例子。

#### 可观测性

你可以为你的 Spring Boot 微服务应用启用指标、日志记录和分布式追踪。只需对你的微服务应用进行最少的更改，即可使你的 Spring Boot 集成微服务变得可观测。当我们在第 13 章“可观测性”中深入探讨可观测性概念时，我们将详细讨论这些能力。

### Dropwizard

Dropwizard 是另一个流行的微服务开发框架。Dropwizard 的主要目标是为生产就绪的 Web 应用所需的一切提供高性能、可靠的实现。由于这些功能被提取到一个可重用的库中，你的应用保持精简和专注，从而缩短了上市时间并减少了维护负担。

Dropwizard 使用 Jetty HTTP 库将调优后的 HTTP 服务器直接嵌入到你的项目中。Jersey 用作 RESTful Web 应用开发引擎，而 Jackson 则处理数据格式。Dropwizard 默认还捆绑了其他几个库。然而，与 Spring Boot 不同，对于某些需要与多种网络协议和 Web API 集成的微服务集成场景，它开箱即用提供的功能集是有限的。

### Apache Camel 和 Spring Integration

*Apache Camel* 是一个传统的集成框架，旨在满足集中式集成/ESB 的需求。Apache Camel 集成框架的关键目标是以一种简单易用的机制，以较小的占用空间和开销，并以可嵌入到现有微服务中的方式，轻松实现企业集成模式（EIP），例如基于内容的路由、转换、协议切换、分散-收集等。

鉴于其领域特定语言（DSL）能力可以处理 Java、Scala 等多种语言，以及其轻量级集成框架的特性，Apache Camel 可以解决相当多的微服务用例。Apache Camel 拥有能够通过几乎所有流行的网络协议、Web API 和系统来消费和生成消息的组件。你可以构建一个基于 Camel 的自包含运行时，该运行时可以在容器上运行。（事实上，目前正在努力构建一个原生运行在 Kubernetes 上的容器原生运行时，称为 Camel-K）。例如，在下面的示例中，你可以找到一个涉及多个 EIP 的集成用例的 Camel DSL。

```
public void configure() {
from("direct:cafe")
.split().method("orderSplitter")
.to("direct:drink");
from("direct:drink").recipientList().method("drinkRouter");
from("seda:coldDrinks?concurrentConsumers=2")
.to("bean:barista?method=prepareColdDrink")
.to("direct:deliveries");
from("seda:hotDrinks?concurrentConsumers=3")
.to("bean:barista?method=prepareHotDrink")
.to("direct:deliveries");
from("direct:deliveries")
.aggregate(new CafeAggregationStrategy())
.method("waiter", "checkOrder").completionTimeout(5 * 1000L)
.to("bean:waiter?method=prepareDelivery")
.to("bean:waiter?method=deliverCafes");
}
```

此外，Apache Camel 提供了与 Spring Boot 的无缝集成，这形成了一个强大的组合，有助于微服务集成。你可以从我们 `ch07/sample08` 中的示例中尝试这个例子。

*Spring Integration* 与 Apache Camel 非常相似，它扩展了 Spring 编程模型以支持众所周知的 EIP。Spring Integration 支持在基于 Spring 的应用中进行轻量级消息传递，并通过声明式适配器支持与外部系统的集成。这些适配器在 Spring 对远程处理、消息传递和调度的支持之上提供了更高层次的抽象。Spring Integration 的主要目标是为构建企业集成解决方案提供一个简单的模型，同时保持关注点分离，这对于生成可维护、可测试的代码至关重要。

以下代码是一个基于 Spring Integration 的用例的 DSL 示例，与我们之前看到的 Camel 示例类似。

```
@MessagingGateway
public interface Cafe {
@Gateway(requestChannel = "orders.input")
void placeOrder(Order order);
}
private AtomicInteger hotDrinkCounter = new AtomicInteger();
private AtomicInteger coldDrinkCounter = new AtomicInteger();
@Bean(name = PollerMetadata.DEFAULT_POLLER)
public PollerMetadata poller() {
return Pollers.fixedDelay(1000).get();
}
@Bean
public IntegrationFlow orders() {
return f -> f
.split(Order.class, Order::getItems)
.channel(c -> c.executor(Executors.newCachedThreadPool()))
.route(OrderItem::isIced, mapping -> mapping
.subFlowMapping("true", sf -> sf
.channel(c -> c.queue(10))
.publishSubscribeChannel(c -> c
.subscribe(s ->
s.handle(m -> sleepUninterruptibly(1, TimeUnit.SECONDS)))
...
```

如果你比较和对比 Camel 与 Spring Integration，你可能会发现 Spring Integration DSL 暴露了较低层次的 EIP（例如，通道、网关等），而 Camel DSL 则更侧重于高层次集成抽象。

无论是使用 Camel 还是 Spring Integration，你都可以基于定义良好的 DSL 来构建你的微服务集成。但是，请记住，你会受到此 DSL 的限制，并且在 DSL 之上构建真正的编程逻辑时，你将需要进行大量的调整。



此外，这两种 DSL 在相当复杂的集成场景中可能会变得非常笨重。有人可能会认为，对于微服务集成，我们可以完全省略 EIP 的使用，而是从头开始将其作为服务代码的一部分来实现。因此，如果你的用例需要使用大部分现有的 EIP 和连接到各种系统的连接器，那么 Camel 或 Spring Integration 是一个不错的选择。

### Vert.x

Eclipse Vert.x 是一个事件驱动、非阻塞、响应式且支持多语言的软件开发工具包，你可以用它来构建微服务并进行集成。Vert.x 不是一个限制性的框架（一个无主见的工具包），它不会强制你以某种特定方式编写应用程序。你可以将 Vert.x 与多种语言一起使用，包括 Java、JavaScript、Groovy、Ruby、Ceylon、Scala 和 Kotlin。

Vert.x 为微服务集成提供了丰富的功能集。它有几个关键组件，每个组件都解决一组特定的需求。Vert.x 核心提供了相当底层的一组功能来处理 HTTP，对于某些应用程序来说，这已经足够了。然而，对于深度利用 RESTful 服务概念的微服务，你将需要 Vert.x Web 组件。Vert.x-Web 构建在 Vert.x 核心之上，为更轻松地构建 Web 应用程序提供了更丰富的功能集。

```
HttpServer server = vertx.createHttpServer();
Router router = Router.router(vertx);
router.route().handler(routingContext -> {
// This handler will be called for every request
HttpServerResponse response = routingContext.response();
response.putHeader("content-type", "text/plain");
// Write to the response and end it
response.end("Hello World from Vert.x-Web!");
});
server.requestHandler(router::accept).listen(8080);
```

我们创建一个 HTTP 服务器和一个路由器。完成后，我们创建一个没有匹配条件的简单路由，因此它将匹配到达服务器的所有请求。然后我们为该路由指定一个处理器。该处理器将被调用以处理到达服务器的所有请求。你可以进一步添加路由逻辑，用于捕获路径参数和 HTTP 方法等。（你可以从我们 `ch07/sample09` 中的示例尝试此示例。）

```
Route route = router.route(HttpMethod.POST, "/catalogue/products/:producttype/:productid/");
route.handler(routingContext -> {
String productType = routingContext.request().getParam("producttype");
String productID = routingContext.request().getParam("productid");
// Do something with them...
});
```

客户端代码也很简单，因为 Vert.x 提供了相当多的抽象来与客户端交互。（你可以从我们 `ch07/sample10` 中的示例尝试此示例。）

```
WebClient client = WebClient.create(vertx);
client
.post(8080, "myserver.mycompany.com", "/some-uri")
.sendJsonObject(new JsonObject()
.put("firstName", "Dale")
.put("lastName", "Cooper"), ar -> {
if (ar.succeeded()) {
// Ok
}
});
```

Vert.x-Web API 契约带来了两个功能来帮助你开发 API：HTTP 请求验证，以及支持自动请求验证的 OpenAPI 3。Vert.x 还提供了不同的异步客户端，用于从你的微服务访问各种数据存储。（你可以从我们 `ch07/sample11` 中的示例尝试此示例。）

```
SQLClient client = JDBCClient.createNonShared(vertx, config);
client.getConnection(res -> {
if (res.succeeded()) {
SQLConnection connection = res.result();
connection.query("SELECT * FROM some_table", res2 -> {
if (res2.succeeded()) {
ResultSet rs = res2.result();
// Do something with results
connection.close();
}
});
} else {
// Failed to get connection - deal with it
}
});
```

类似地，我们还可以使用 Vert.x 将你的服务连接到 Redis、MongoDB、MySQL 等更多系统。对于微服务集成，诸如断路器之类的弹性服务间通信能力也作为 Vert.x 的一部分包含在内。（你可以从我们 `ch07/sample12` 中的示例尝试此示例。）

```
CircuitBreaker breaker = CircuitBreaker.create("my-circuit-breaker", vertx,
new CircuitBreakerOptions().setMaxFailures(5).setTimeout(2000)
);
breaker.execute(future -> {
vertx.createHttpClient().getNow(8080, "localhost", "/", response -> {
if (response.statusCode() != 200) {
future.fail("HTTP error");
} else {
response
.exceptionHandler(future::fail)
.bodyHandler(buffer -> {
future.complete(buffer.toString());
});
}
});
}).setHandler(ar -> {
// Do something with the result
});
```

Vert.x 的集成能力还包括 gRPC、Kafka、基于 RabbitMQ 的 AMQP、MQTT、STOMP、认证与授权、服务发现等等。除了功能组件之外，所有与生态系统相关的能力——例如测试、集群、DevOps、与 Docker 集成、通过指标和健康检查实现的可观测性等——绝对使 Vert.x 成为最全面的微服务和集成框架之一。



### Akka

Akka 是一组用于设计跨处理器核心和网络的可扩展、弹性系统的开源库。Akka 完全基于 Actor 模型，这是一种并发计算的数学模型，将*Actor*视为并发计算的通用原语。Actor 在接收到消息后，可以做出本地决策、创建更多 Actor、发送更多消息，并决定如何响应下一条接收到的消息。Actor 可以修改自身的私有状态，但只能通过消息相互影响；这避免了对任何锁的需求。

Akka 旨在为你的微服务提供多线程行为，而无需使用诸如原子操作或锁之类的底层并发结构——让你无需考虑内存可见性问题，实现系统及其组件之间的透明远程通信，以及一个集群化、高可用性的架构，该架构具有弹性，可按需伸缩。

你可以利用 Akka HTTP 模块来实现基于 HTTP 的服务，它在 Akka-actor 和 Akka-stream 之上提供了一个完整的服务器端和客户端 HTTP 栈。它不是一个 Web 框架，而是一个更通用的工具包，用于提供和使用基于 HTTP 的服务。

在 Akka HTTP 之上，Akka 提供了一种 DSL 来描述 HTTP *路由*及其处理方式。每条路由由一个或多个级别的指令组成，这些指令将范围缩小到处理一种特定类型的请求。

例如，一条路由可能首先匹配请求的路径，仅当找到与 `/order` 的匹配时，然后将其范围缩小到仅处理 HTTP `GET` 请求，最后用一个字符串字面量完成这些请求，该字符串将作为 HTTP `OK` 响应，并以字符串作为响应体发送回去。使用 Route DSL 创建的*路由*随后绑定到一个端口以开始服务 HTTP 请求。通过使用 Jackson，Akka-http 可以支持 JSON。

在以下用例中，我们有两条独立的 Akka 路由。第一条路由查询一个异步数据库，并将 `CompletionStage<Optional<Item>>` 结果编组为 JSON 响应。第二条路由将传入请求中的 `Order` 解组，将其保存到数据库，并在完成后回复 `OK`。（你可以从我们的示例 `ch07/sample13` 中尝试此示例。）

```
public class JacksonExampleTest extends AllDirectives {
public static void main(String[] args) throws Exception {
ActorSystem system = ActorSystem.create("routes");
final Http http = Http.get(system);
final ActorMaterializer materializer = ActorMaterializer.create(system);
JacksonExampleTest app = new JacksonExampleTest();
final Flow routeFlow = app.createRoute().flow(system, materializer);
final CompletionStage binding = http.bindAndHandle(routeFlow,
ConnectHttp.toHost("localhost", 8080), materializer);
binding
.thenCompose(ServerBinding::unbind) // 触发从端口解绑
.thenAccept(unbound -> system.terminate()); // 然后关闭系统
}
private CompletionStage> fetchItem(long itemId) {
return CompletableFuture.completedFuture(Optional.of(new Item("foo", itemId)));
}
private CompletionStage saveOrder(final Order order) {
return CompletableFuture.completedFuture(Done.getInstance());
}
private Route createRoute() {
return route(
get(() ->
pathPrefix("item", () ->
path(longSegment(), (Long id) -> {
final CompletionStage> futureMaybeItem = fetchItem(id);
return onSuccess(futureMaybeItem, maybeItem ->
maybeItem.map(item -> completeOK(item, Jackson.marshaller()))
.orElseGet(() -> complete(StatusCodes.NOT_FOUND, "Not Found"))
);
}))),
post(() ->
path("create-order", () ->
entity(Jackson.unmarshaller(Order.class), order -> {
CompletionStage futureSaved = saveOrder(order);
return onSuccess(futureSaved, done ->
complete("order created")
);
})))
);
}
}
```

Akka 通过 Alpakka 计划满足微服务和其他类型集成的特定需求。Alpakka 实现了基于 Akka 的各种 Akka Streams 连接器、集成模式和数据转换的集成，用于集成用例。Alpakka 提供了众多连接器，例如 HTTP、Kafka、File、AMQP、JMS、CSV、Web API（AWS S3、GCP pub-sub 和 Slack）、MongoDB 等。

在以下示例中，你可以找到一个使用 Alpakka 编写的 AMQP 生产者和消费者示例。（你可以从我们的示例 `ch07/sample14` 中尝试此示例。）

```
// AMQP 生产者
final Sink> amqpSink = AmqpSink.createSimple(
AmqpSinkSettings.create(connectionProvider)
.withRoutingKey(queueName)
.withDeclarations(queueDeclaration)
);
// AMQP 消费者
final Integer bufferSize = 10;
final Source amqpSource = AmqpSource.atMostOnceSource(
NamedQueueSourceSettings.create(
connectionProvider,
queueName
).withDeclarations(queueDeclaration),
bufferSize
);
```

这里，AmqpSink 是一个工厂方法的集合，有助于创建 Sink 和 Source，使你能够从 AMQP 获取消息。

### Node、Go、Rust 和 Python

Node.js 是一个开源、跨平台的 JavaScript 运行时环境，用于在服务器端执行 JavaScript 代码。Node.js 开箱即用地支持构建 RESTful 服务，你可以基于完全非阻塞的 I/O 模型构建服务，该模型利用了事件循环。（当 Node.js 启动时，它会初始化事件循环，处理提供的输入脚本——该脚本可能会进行异步 API 调用、调度定时器或调用 `process.nextTick()`——然后开始处理事件循环。）

以下代码展示了一个使用 Node.js 构建的简单 `Echo` 服务。（你可以从我们的示例 `ch07/sample15` 中尝试此示例。）

```
const http = require('http');
http.createServer((request, response) => {
if (request.method === 'POST' && request.url === '/echo') {
let body = [];
request.on('data', (chunk) => {
body.push(chunk);
}).on('end', () => {
body = Buffer.concat(body).toString();
response.end(body);
});
} else {
response.statusCode = 404;
response.end();
}
}).listen(8080);
```

除了标准功能集之外，Node.js 还拥有多样化的生态系统，允许你将基于 Node.js 的微服务与几乎所有其他网络协议、数据库、Web API 和其他系统集成。

Node.js 之上构建了多个框架，例如 Restify，这是一个 Web 服务框架，专为构建语义正确的 RESTful Web 服务而优化，可用于大规模生产环境。同样，NPM（NPM 是 Node.js 包的包管理器）上有大量可用于 Node.js 的库和包。例如，你可以找到用于 Kafka 集成（kafka-node）、AMQP（node-amqp）、断路器以及大多数流行可观测性工具的检测库的 NPM 包。

Go^(⁹⁹) 也常用于微服务开发，并提供了丰富的网络通信包。

类似地，其他编程语言，如 Rust 和 Python，也提供了不少开箱即用的能力来构建微服务和集成微服务。对于 Rust，我们有 Rocket，这是一个 Web 框架，可以轻松编写快速的 Web 应用程序，而无需牺牲灵活性或类型安全性。Rust 生态系统组件解决了 Rust 应用程序与大多数其他网络协议、数据、Web API 和其他系统的集成问题。然而，一些开发者声称 Rust 过于底层，不适合作为微服务开发语言。因此，我们建议你在完全采用之前，先用一些用例尝试一下。

同样，Python 拥有广泛的社区和可用于生产环境的框架，例如 Flask，用于微服务开发和集成。



### Ballerina

Ballerina^(¹⁰⁰) 是一种新兴的集成技术，它被构建为一种编程语言，旨在填补集成产品与通用编程语言之间的空白，使开发者能够轻松编写程序，以类型安全且具有弹性的方式集成和编排跨分布式微服务和端点。

在本书撰写时，Ballerina 的版本为 0.981。大部分编程结构已最终确定，但部分内容仍可能发生变化，且该语言尚未在微服务社区中得到广泛采用。

### 免责声明

本书的作者参与了 Ballerina 的设计和开发。我们致力于保持本书内容的技术中立性和供应商中立性，因此不会将 Ballerina 与其他类似技术进行比较或对比。我们强烈建议读者在对其用例和实现这些用例的潜在技术进行彻底评估后，选择最合适的技术。

Ballerina 中的代码和图形语法都借鉴了独立方如何通过序列图中的交互进行通信的方式。

在图形语法中，Ballerina 将客户端、工作者和远程系统表示为序列图中的不同角色。例如，如图 7-11 所示，客户端/调用者、服务、工作者和其他外部端点之间的交互可以使用序列图来表示。每个端点表示为序列图中的一个角色，而动作则表示为这些角色之间的交互。

![../images/461146_1_En_7_Chapter/461146_1_En_7_Fig11_HTML.jpg](img/461146_1_En_7_Fig11_HTML.jpg)

图 7-11

Ballerina 图形语法和源代码语法基于序列图隐喻

在代码中，远程端点通过提供类型安全动作的端点进行接口连接，工作者的逻辑则作为资源或函数内的顺序代码编写。您可以定义一个服务并将其绑定到服务器端点（例如，HTTP 服务器端点可以监听给定的 HTTP 端口）。每个服务包含一个或多个资源，我们为这些资源编写与工作者相关的顺序代码，并由专用工作者线程运行。

以下代码片段展示了一个简单的 HTTP 服务，它接受 HTTP `GET` 请求，然后调用另一个外部服务来检索某些信息并将其发送回客户端。（您可以从 `ch07/sample16` 中的示例尝试此操作。）

```
import ballerina/http;
import ballerina/io;
endpoint http:Listener listener {
port:9090
};
@http:ServiceConfig {
basePath:"/time"
}
service timeInfo bind listener {
@http:ResourceConfig {
methods:["GET"],
path:"/"
}
getTime (endpoint caller, http:Request req) {
endpoint http:Client timeServiceEP {
url:"http://localhost:9095"
};
http:Response response = check
timeServiceEP -> get("/localtime");
json time = check response.getJsonPayload();
json payload = {
source: "Ballerina",
time: time
};
response.setJsonPayload(untaint payload);
_ = caller -> respond(response);
}
}
```

#### 网络感知抽象

Ballerina 专为集成不同的服务、系统和数据而设计。因此，Ballerina 提供了原生的网络感知结构，这些结构为通过不同网络协议与端点进行交互提供了抽象。Ballerina 为大多数标准网络通信协议提供了开箱即用的支持。

```
endpoint http:Client timeServiceEP {
url:"http://localhost:9095"
};
...
http:Response response = check
timeServiceEP -> get("/localtime");
endpoint mysql:Client testDB {
host: "localhost",
port: 3306,
name: "testdb",
username: "root",
password: "root",
poolOptions: { maximumPoolSize: 5 },
dbOptions: { useSSL: false }
};
...
var selectRet = testDB->select("SELECT * FROM student", ());
// Kafka 生产者端点
endpoint kafka:SimpleProducer kafkaProducer {
bootstrapServers: "localhost:9092",
clientID:"basic-producer",
acks:"all",
noRetries:3
};
...
// 生成消息并将其发布到 Kafka 主题
kafkaProducer->send(serializedMsg, "product-price", partition = 0);
```

与前面的情况类似，您可以利用服务器连接器通过那些协议接收消息，并将它们绑定到打算消费这些消息的服务。通过给定协议消费消息的大部分实现细节对开发者来说是透明的。

```
// 服务器端点配置。
endpoint grpc:Listener ep {
host:"localhost",
port:9090
};
// 绑定到服务器端点的 gRPC 服务。
service SamplegRPCService bind ep {
// 一个接受字符串消息的资源。
receiveMessage (endpoint caller, string name) {
// 打印接收到的消息。        foreach record in records {
blob serializedMsg = record.value;
// 将序列化消息转换为字符串消息
string msg = serializedMsg.toString("UTF-8");
log:printInfo("New message received from the product admin");
...
endpoint jms:SimpleQueueReceiver consumer {
initialContextFactory:"bmbInitialContextFactory",
providerUrl:"amqp://admin:admin@carbon/carbon"
+ "?brokerlist='tcp://localhost:5672'",
acknowledgementMode:"AUTO_ACKNOWLEDGE",
queueName:"MyQueue"
};
service jmsListener bind consumer {
onMessage(endpoint consumer, jms:Message message) {
match (message.getTextMessageContent()) {
string messageText => log:printInfo("Message : " + messageText);
```

#### 弹性且安全的集成

您使用 Ballerina 编写的集成微服务天生具有弹性。您可以以弹性且类型安全的方式调用外部端点。

例如，当您调用一个可能不可靠的外部端点时，您可以通过为您正在使用的特定协议设置断路器来规避此类交互。这就像向您的客户端端点代码传递几个额外参数一样简单。

```
// 断路器示例
endpoint http:Client backendClientEP {
circuitBreaker: {
rollingWindow: { // 故障计算窗口
timeWindowMillis:10000,
bucketSizeMillis:2000
},
failureThreshold:0.2, // 触发断路器断开的故障百分比阈值
resetTimeMillis:10000, // 断路器从断开状态到半开状态所需的时间
statusCodes:[400, 404, 500] // 被视为故障的 HTTP 状态码
}
url: "http://localhost:8080",
timeoutMillis:2000,
};
```

按照设计，您编写的 Ballerina 代码不需要特定的工具来检查漏洞或最佳实践。例如，构建分布式系统时的一个常见问题是，不能信任通过网络传输的数据不会包含注入攻击。Ballerina 假设所有通过网络传输的数据都是受污染的。编译时检查会阻止需要未受污染数据的代码访问受污染数据。Ballerina 将此类功能作为语言的内置结构提供，从而强制程序员编写安全的代码。



#### 数据格式

Ballerina 拥有一个结构化的类型系统，包含基本类型、记录、对象、元组和联合类型。这种类型安全的模型在赋值时融入了类型推断，并为连接器、逻辑和网络绑定负载提供了众多编译时完整性检查。

集成服务和系统的代码常常需要处理复杂的分布式错误。Ballerina 具有基于联合类型的错误处理能力。联合类型能够显式地捕获语义，而无需开发者创建不必要的*包装器*类型。当你决定将错误返回给调用者时，可以使用 `check` 操作符。

例如，当你通过消息接收到 JSON 数据时，可以将其转换为你在逻辑中定义的类型。然后，通过处理针对该联合类型编写的 `match` 子句中可能出现的错误，你可以安全地转换这两种类型。

```
// 这是 Ballerina 中一个简单的结构化对象定义
// 它可以自动映射为 JSON，并再次映射回来
type Payment {
string name,
string cardnumber,
int month,
int year,
int cvc;
};
...
json payload = check request.getJsonPayload();
// 下一行展示了将 JSON 类型安全地解析为对象
Payment|error p = payload;
match p {
Payment x => {
io:println(x);
res.statusCode = 200;
// 返回已创建的 JSON
res.setJsonPayload(check x);
}
error e => {
res.statusCode = 400 ;
// 如果 JSON 解析失败，则返回错误消息
res.setStringPayload(e.message);
}
_ = caller -> respond (res);
```

#### 可观测性

监控、日志记录和分布式追踪是揭示 Ballerina 代码内部状态以提供可观测性的关键方法。Ballerina 提供了开箱即用的能力，可与 Prometheus、Grafana、Jaeger 和 Elastic Stack 等可观测性工具协同工作，且只需极少的配置。

### 工作流引擎解决方案

我们以专门为需要工作流（即可能需要人工交互的长时间运行的有状态流程）的微服务设计的技术来结束对微服务集成技术的讨论。在微服务架构中构建工作流是集成需求的一个特例。有不少新的和现有的解决方案正在演变为微服务工作流领域。Zeebe、Netflix Conductor、Apache NiFi、AWS Step Functions、Spring Cloud Data Flow 和 Microsoft Logic Apps 都是很好的例子。

*Zeebe*^(¹⁰¹) 支持使用可视化工作流（由 Camunda 开发，Camunda 是一个流行的开源业务流程模型和符号——BPMN——解决方案）对工作节点和微服务进行有状态编排。它允许用户使用 BPMN 2.0 或 YAML 以可视化方式定义编排流程。Zeebe 确保流程一旦启动，就会完全执行，并在失败时重试步骤。在此过程中，Zeebe 会维护完整的审计日志，以便监控和跟踪流程的进展。Zeebe 是一个大数据系统，能够随着交易量的增长无缝扩展。（你可以从我们 `ch07/sample17` 的示例中尝试一个 Zeebe 示例。）

*Netflix Conductor*^(¹⁰²) 是一个开源工作流引擎，它使用 JSON DSL 来定义工作流。Conductor 允许创建复杂的流程/业务流，其中单个任务由微服务实现。

*Apache NiFi*^(¹⁰³) 是一个传统的集成框架，支持强大且可扩展的有向图，用于数据路由、转换和系统中介逻辑。

## 服务网格的兴起

我们在本章中已经看到，微服务必须与其他微服务、数据、Web API 和其他系统协同工作。由于我们不使用集中式 ESB 作为总线来连接所有这些服务和系统，因此服务间通信现在成为了服务开发者的一部分。尽管许多微服务框架解决了大部分此类需求，但对于服务开发者来说，处理集成微服务的所有需求仍然是一项艰巨的任务。

为了克服这个问题，架构师们认识到，某些服务间通信功能可以被视为通用特性，并且服务代码可以独立于它们。服务网格的核心概念是识别出诸如断路器、超时、基本路由、服务发现、安全通信、可观测性等通用网络通信功能，并在一个称为*边车*的组件中实现它们，该组件与你开发的微服务一起运行。这些边车由一个称为*控制平面*的中央管理层控制。

随着服务网格的出现，微服务开发者在多语言服务开发技术方面获得了更多自由，并且他们较少关注服务间通信。他们可以更多地专注于所开发服务的业务能力。

我们将在第 9 章“服务网格”中详细讨论服务网格，并深入探讨一些现有的服务网格实现。

## 总结

在本章中，我们深入分析了微服务集成的挑战。由于 ESB 的缺失，我们在开发服务时需要践行智能端点和哑客户端理念。通过这种方法，ESB 的大部分能力现在都需要在我们开发的微服务中得到支持。我们确定了一些常用的微服务集成模式：主动组合/编排、响应式组合/编排以及混合方法。坚持采用混合方法来集成微服务，并根据你的用例选择集成模式，是更为务实的做法。

为了促进微服务集成，微服务开发框架需要具备一系列独特的能力，例如内置的网络通信抽象、对弹性模式的支持、对数据类型的原生支持、管理集成微服务的能力、云原生和容器原生特性等。针对这些需求，我们详细讨论了一些关键的微服务实现技术。随着服务网格的兴起，一些微服务集成需求可以被卸载到一个分布式网络通信抽象上，该抽象作为边车与每个服务一起执行，并由集中式控制平面控制。在下一章中，我们将讨论如何部署和运行微服务。

脚注 1   2   3   4   5   6   7   8



