# 12. 使用 TestContainers 进行测试

测试是应用开发的重要方面。无论是开发单体应用还是微服务应用，测试都必须且应该成为整个应用开发周期的核心部分。尽管测试被普遍认为很重要，但并没有一种“正确”的测试方法。所选择的测试种类、类型和模式大多取决于应用和领域。

很多时候，应用开发平台会决定应用开发者可用的测试选项。在以 Java SE 为基础的 Jakarta EE 平台上，有大量可用的测试选项。选择可用测试选项的最优组合，通常取决于应用领域。本章将探讨如何使用久经考验的 JUnit 和 TestContainers 库来测试 Jakarta EE 应用。

本章首先探讨一些基本的测试理论，然后介绍如何对 Jakarta EE 组件进行单元测试，最后展示如何使用 TestContainers 编写集成测试。^(¹¹⁷) 到本章结束时，您将很好地理解如何为您的企业应用设计和编写测试。

## 应用测试理论

测试可以定义为通过结合不同类型的测试来确立应用的整体正确性和可靠性的过程。“整体”一词很重要，因为测试并不能保证应用没有缺陷。它只能揭示应用中可能存在的缺陷。测试大致分为两类：手动测试和自动化测试。在这两类测试下，可以运行不同类型的测试。有些测试类型跨越了这两个类别。以下是应用测试的一般类型列表。

### 测试类型

#### 单元测试

单元测试是一种自动化测试，它单独测试单个组件，以验证该组件是否按预期工作。单元测试是任何测试套件中最原子的测试。根据被测试的特定组件，其依赖项可能会被模拟，以便能够单独对该组件进行单元测试。单元测试通常运行速度非常快，因为它们具有隔离性。

#### 集成测试

集成测试用于断言应用中共同执行特定功能的不同组件是否正常工作。集成测试跨越多个组件或微服务。例如，一个用于断言成功创建钱包的集成测试，可以先断言用户的创建，然后再断言钱包的创建。在这种情况下，将实际调用这两个模块。集成测试运行起来往往更耗时，因为它们可能跨越多个组件，并且很可能包含多次网络调用。集成测试也是自动化的。

#### 功能测试

功能测试侧重于应用的业务方面。功能测试通常会验证被测操作的结果。功能测试和集成测试之间差别很小，因为这两种测试都会导致调用应用的不同组件。在某些组织中，功能测试和集成测试是同一回事。然而，从技术上讲，功能测试断言操作的输出，以验证其是否等于预设的期望值。而集成测试通常检查组件是否正常工作。功能测试可以是手动的，也可以是自动化的。

#### 性能测试

性能测试检查应用在不同工作负载下的表现。性能测试有助于发现瓶颈，因为它们会衡量应用的速度、站点的可靠性以及组件的可扩展性等指标。这类测试有助于根据收集到的测试数据优化应用。性能测试是自动化的。

#### 冒烟测试

冒烟测试主要是一种手动测试，用于检查应用的基本功能是否按预期工作。冒烟测试通常在新构建或发布应用之后进行，并先于其他类型的检查。

#### 其他测试

还有其他测试，如端到端测试和验收测试，它们是集成测试和功能测试的不同变体。最终，每个组织都会根据其领域混合使用不同的测试类型和模式。前面列举的测试类型绝非所有现有测试类型的详尽列表。但这些是跨所有类型组织中最常见的测试类型。

### 测试原则

尽管软件应用测试非常重要，但并没有一个通用的标准来确定什么是良好的测试实践。现有的众多编程语言和应用开发框架意味着测试是一种高度异质化的活动，因平台和应用而异。美国软件测试资格委员会（ASTQB）确定了七项原则^(¹¹⁸)，可以帮助指导创建有效的测试体系。这些原则如下。



#### 测试能证明缺陷的存在，而非其不存在

测试有助于发现应用程序中的错误。然而，测试无法保证应用程序完全没有错误。测试套件中的所有测试都通过，并不意味着不存在尚未发现的缺陷。尽管如此，拥有广泛的测试可以在一定程度上合理保证应用程序在已测试的条件下不会发生故障。

#### 穷尽测试是不可能的

企业级应用程序非常庞大且复杂。不可能进行覆盖应用程序所有可能排列组合的测试。试图进行任何此类活动都将代价高昂。应对不同的核心功能进行分析，并将测试工作集中在关键领域。

#### 尽早测试可节省时间和成本

对新发布的应用程序进行快速冒烟测试，有助于发现那些原本需要运行整个测试套件才能揭露的致命错误。这也同样适用于尽可能实施测试驱动开发。

#### 缺陷具有集群性

应用程序中少数几个组件将负责大部分的错误。因此，测试应重点关注这些导致大部分错误的组件。这本质上是将帕累托原则应用于应用程序组件的测试。

#### 警惕农药原理

长期重复使用同一套测试用例，最终可能无法捕获新的缺陷。这一原理类似于长期在同一区域使用农药会导致杀虫效果下降。测试需要更新和重构，引入新的输入数据和排列组合，以保持其识别应用程序缺陷的有效性。

#### 测试具有上下文相关性

测试是一项高度依赖上下文的活动。应用于商业应用程序的测试，与应用于航空航天计算机控制系统的测试会有所不同。在借鉴经验创建测试系统时，牢记这一点非常重要。

#### 没有错误是一种谬误

正如第一条原则所阐述的，测试并不能保证没有错误。因此，如果测试没有发现任何缺陷就认为应用程序没有错误，这是一种错误的观念。此外，识别并修复缺陷并不能保证系统不会发生故障。

## Jakarta EE 中的测试

Jakarta EE 作为一个应用程序开发平台，本身并没有自己的测试规范或 API。然而，由于它构建在 Java SE 之上，Java SE 上可用的各种测试库和框架都可以用来测试 Jakarta EE 应用程序。但是，测试 Jakarta EE 应用程序也面临一些挑战。

一个现代的 Jakarta EE 应用程序，就像本书到目前为止所讨论的那样，大量使用了 Jakarta 上下文和依赖注入 API。几乎所有组件都由某个运行时或其他机制管理。例如，一个 CDI bean 可能声明了对另一个 bean 的依赖，而后者又依赖于一个 JPA 管理的工件，如 `EntityManager`。

### 对 Jakarta EE 组件进行单元测试

对 Jakarta EE 组件进行单元测试可以通过对被测组件进行少量重构来实现。使用字段注入的被测组件，可以重构为其依赖项使用构造器注入。这样，被测组件的依赖项可以在应用程序运行时由 CDI 传入，而在测试期间则通过其构造器手动实例化，传入其依赖项的模拟实例。

#### JUnit

JUnit 是一个用于对测试工件进行断言的 Java 测试库。它有许多用于设置和销毁测试数据的回调方法。它还有一个扩展 API，其他测试库在此基础上构建，以扩展不同目的的测试。JUnit 可以说是企业级 Java 中最流行的测试库。

#### Mockito

Mockito 是一个用于在单元测试中创建模拟对象的 Java 库。一个依赖于其他组件的被测组件，其依赖项将被模拟。模拟允许在模拟对象上返回可预测的值，并随后进行断言。

为了测试 jwallet 的 `WalletService` CDI bean，我们首先对其进行重构，使其对其两个 CDI 管理的应用程序组件使用构造器注入。这样，在单元测试中，`WalletService` 可以轻松地传入模拟对象。清单 12-1 展示了重构后的 `WalletService`。

```
@Action
@NoArgsConstructor
public class WalletService {
WalletRepository walletRepository;
TransactionRepository transactionRepository;
RateService rateService;
@Inject
public WalletService(WalletRepository wallet,
TransactionRepository txn,
RateService rate) {
this.walletRepository = wallet;
this.transactionRepository = txn;
this.rateService = rate;
}
}
清单 12-1
重构后的 WalletService
```

`WalletService` 依赖于 `WalletRepository`、`TransactionRepository` 和 `RateService`。它已被重构为使用构造器注入，带有一个标注了 `@Inject` 的构造器，传入这三个依赖项并用它们初始化类字段。通过这个简单的重构，`WalletService` 现在可以进行单元测试了。清单 12-2 展示了 `WalletService` 的单元测试类 `WalletServiceTest`。

```
@ExtendWith(MockitoExtension.class)
public class WalletServiceTest {
@Mock
WalletRepository walletRepository;
WalletService walletService;
static String walletCurrency;
static BigDecimal balance;
@BeforeAll
public static void initAll() {
walletCurrency = "GHS";
balance = new BigDecimal("50");
}
@BeforeEach
void init() {
walletService = new WalletService(walletRepository, null, null);
when(walletRepository.findById(anyLong())).thenAnswer(i -> {
Wallet wallet = new Wallet();
wallet.setId(i.getArgument(0));
wallet.setCurrency(walletCurrency);
wallet.setBalance(balance);
return wallet;
});
}
@Test
void shouldFindWallet() {
BalanceResponse response = walletService.getBalance(1L);
assertNotNull(response);
assertEquals(1L, response.getData().getWalletId());
assertEquals(balance, response.getData().getBalance());
assertEquals(walletCurrency, response.getData().getCurrency());
}
}
清单 12-2
WalletServiceTest
```

该类使用 `@ExtendWith` 注解，并传入了 `MockitoExtension.class`。这将 `MockitoExtension` 注册到 JUnit 运行时。`@Mock` 注解用于模拟 `WalletRepository`。`@BeforeAll` 回调方法设置了一些预期数据，这些数据应该是被测组件（本例中为待测试的 `WalletService` 方法）返回的。`WalletService` 对象被实例化，传入了模拟的 `WalletRepository`，另外两个参数传入了 `null`。我们同样可以模拟 `WalletService` 构造器的另外两个参数。

调用了静态的 Mockito `when` 方法来模拟对 `WalletRepository.findById` 方法的调用。这个模拟只是捕获传入的 ID，并用它构造一个 `Wallet` 对象。现在，我们已经准备好一切，可以对 `WalletService` 的 `getBalance` 方法进行单元测试了。

`shouldFindWallet` 测试方法调用了 `WalletService` 上的 `getBalance` 方法，然后使用 JUnit 的断言方法来断言预期数据。这通常是在 Jakarta EE 中为应用程序组件编写单元测试的方式。请注意，除了对被测组件进行少量重构以使用构造器注入来初始化依赖字段外，并没有其他显著的不同。

这种测试速度非常快，可以在 CI/CD 流水线上多次运行。需要注意的是，`WalletServiceTest` 是作为一个与 Jakarta EE 无关的 POJO 进行测试的。它的方法被测试并断言其行为符合预期。你应该尽可能地对应用程序的核心组件进行单元测试。



### Jakarta EE 中的集成测试

与单元测试不同，集成测试需要不同的组件之间相互通信。这要求在测试上下文中有一个正在运行的应用程序实例。Jakarta EE 集成测试，根据应用程序的不同，需要调用应用程序的一个入口点，该入口点随后会调用所有相关的组成单元。这个入口点通常是一个 REST 端点。

在 Jakarta EE 中，在测试上下文中部署一个正在运行的应用程序一直有些繁琐。像 Arquillian 这样的框架在这方面提供了帮助。但即便如此，设置起来也很困难，并且需要为各种兼容的运行时环境进行大量的底层配置。随着越来越多的应用程序被设计为优先考虑云原生，容器的使用意味着只要特定环境支持容器，就可以在不同的上下文中复制该环境。

TestContainers 是一个用于在测试环境中将应用程序作为 Docker 容器进行部署的库。由于 jwallet 被打包为 Docker 容器，因此为集成测试生成一个运行中的实例只需要一个 `docker-compose` 文件。TestContainers 库将使用这个 `docker-compose` 文件在测试中创建一个正在运行的应用程序实例。有了正在运行的应用程序实例，我们就可以继续向应用程序发起 REST 调用。

使用 TestContainers，测试中运行的应用程序与已部署的应用程序之间没有区别，因为它们都是从相同的 Docker 容器创建的。TestContainers 为不同的技术栈提供了不同的模块。`DockerComposeContainer` 模块用于从 `docker-compose` 文件运行容器。清单 12-3 展示了如何设置 `DockerComposeContainer`，以便从钱包模块的 `docker-compose` 文件创建容器。

```
try (DockerComposeContainer composeContainer = new DockerComposeContainer(new File("docker-compose.yaml")).withExposedService("wallet", 3001)) {
composeContainer.start();
walletServiceUrl = String.format(walletServiceUrlTemplate,
composeContainer.getServiceHost("wallet", 3001),
composeContainer.getServicePort("wallet", 3001));
client = ClientBuilder.newClient();
target = client.target(walletServiceUrl);
}
清单 12-3
DockerComposeContainer
```

`DockerComposeContainer` 在一个 `try-with-resources` 块中被实例化，以便它可以被自动关闭（它是 `AutoClosable` 的）。它接收 `docker-compose` 文件、要暴露的服务以及端口。在实例上调用 `start` 方法。调用 `getServiceHost` 和 `getServicePort` 方法来获取在运行容器中创建的服务的 host 和 port。然后将这些信息传递给 Jakarta REST Client 对象的 `target` 方法。通过这样的设置，我们就拥有了一个完整的钱包模块运行实例，可以对其发起 REST 调用，并对响应执行测试断言。清单 12-4 展示了 `WalletResourceIT` 类中的 `createWallet` JUnit 测试。

```
class WalletResourceIT extends AbstractWalletIT {
@Test
void createWallet() {
CreateWalletRequest createWalletRequest = new CreateWalletRequest();
createWalletRequest.setCurrency("USD");
BalanceResponse balanceResponse = target.path("wallets").request()
.post(Entity.json(createWalletRequest), BalanceResponse.class);
assertEquals("0", balanceResponse.getResponse().getResponseCode());
assertEquals("OK", balanceResponse.getResponse().getResponseMessage());
assertEquals(createWalletRequest.getCurrency(), balanceResponse.getData().getCurrency());
assertEquals(BigDecimal.ZERO, balanceResponse.getData().getBalance());
}
}
清单 12-4
createWalletTest
```

`WalletResourceIT` 类继承了 `AbstractWalletIT`，后者包含了 TestContainers 的实例化代码。`createWallet` 方法是一个 JUnit 测试，它向钱包资源发起一个 REST 调用来创建一个钱包，然后对返回的数据进行一些断言。与单元测试不同，这个测试可能需要一些时间，因为钱包创建过程涉及对数据库的调用。仅用几行代码，我们就拥有了一个完全容器化的应用程序运行时环境，可以从中发起调用来创建集成测试。我们强烈建议你在 Jakarta EE 测试中了解一下 TestContainers。

## 总结

本章从软件应用程序测试的理论开始，然后探讨了如何为 Jakarta EE 应用程序创建单元测试和集成 JUnit 测试。创建单元测试的过程包括通过模拟其感兴趣的依赖项来隔离待测试的组件。我们研究了如何使用 TestContainers 创建一个完全容器化的 Jakarta EE 运行时环境来创建集成测试。

正如本章所述，测试是一项非常依赖领域的活动，需要业务分析来确定使用哪些测试类型的组合，以及测试资源应该集中在何处。本章向你展示了可用于创建两种最流行测试类型（单元测试和集成测试）的最高效、最直接的方法。

脚注 1   2

