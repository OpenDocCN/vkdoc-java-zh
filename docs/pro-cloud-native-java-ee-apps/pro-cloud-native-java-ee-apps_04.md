# 4. 使用 CDI 管理依赖

每个重要的应用都由协同工作以实现应用目标的不同组件构成。这些组件之间存在着错综复杂的依赖关系。手动管理这种复杂的相互依赖关系可能是一项非常艰巨的任务。Jakarta EE 平台提供了 Jakarta Contexts and Dependency Injection API，您可以使用它来自动化管理应用组件依赖的过程。

您可以将 CDI API 视为一个黑盒，您向其发起调用，然后“神奇地”获得具有明确定义的生命周期和其他附加功能的应用组件实例。Jakarta CDI 类似于餐厅的厨房。大多数时候，您只需走进餐厅，浏览菜单，下单，稍等片刻，您的订单就会准备好，并连同餐具和其他附属物品一起端上桌。CDI 运行时与此非常相似，只是您的“订单”是即时送达的。

使用 CDI，您的各种应用组件声明其依赖关系，并由运行时管理所创建实例的创建、注入和销毁。您反转了依赖管理的控制权。依赖注入实际上是应用开发中控制反转^(⁸⁵)模式的一种形式。在本章以及本书的其余部分，我们松散地使用术语“组件”来指代可以封装在 Java 类中的任何应用片段。

在本章结束时，您将学习 CDI API 的 Bean 管理（我们将广泛了解什么是 Bean）、生产者、拦截器和事件机制如何帮助您编写可维护、松散耦合且能在可预见的未来持续运行的应用。您还将了解这些构造如何以最小的代价透明地让您实现久经考验的设计模式。

## Jakarta EE 10 中的 CDI 概述

Jakarta EE 10 附带的最新 CDI 规范版本是 CDI 4.0。^(⁸⁶) 此版本将核心 CDI API 拆分为 Lite 和 Full。CDI Lite 旨在更受限的环境中运行，并包含原始功能的一个子集。CDI Full 包含 Lite 以及先前 Jakarta EE 版本中核心 CDI 的所有其他功能。本章及本书其余部分重点介绍 CDI Full，即完整 Jakarta EE 10 版本所附带的内容。

### CDI 的关键特性

CDI API 提供了一组特性，有助于开发松散耦合的复杂企业应用。拥有松散耦合的应用意味着应用的各个组件不会彼此刚性绑定。这样，就可以轻松地用一个组件替换另一个组件，而无需大量重写应用。以下是 CDI API 可用于帮助您实现这一目标的核心特性。

#### 类型安全的依赖解析机制

CDI 使用 Java 类型来解析依赖关系。与其他基于字符串的依赖注入实现不同，CDI 通过元数据和 Java Bean 的组合，使用 Java 类型来解析您声明的依赖关系。这是通过一个复杂的解析机制实现的，该机制允许您在开发或部署时选择依赖关系。

#### 明确定义的生命周期上下文

CDI 允许您将有状态对象绑定到明确定义、可扩展的生命周期上下文。您从运行时请求的每个 Bean 实例都具有可预测的生命周期，并包含您可以挂钩的回调。

#### 与 Web 层的集成

通过与 Jakarta 统一表达式语言 (EL) 的集成，可以从 Jakarta Server Faces 页面直接访问 Jakarta Enterprise Beans 等上下文对象。

#### 拦截器

CDI 拦截器允许您通过类型安全的拦截器绑定将拦截器与 Java 对象关联起来。您可以使用这些拦截器来实现与任何给定 Bean 的核心功能正交的特性。

#### 事件

CDI 事件是一种强大的、类型安全的方式，允许应用中完全独立的组件通过使用事件通知以完全解耦的方式进行通信。一个组件可以触发一个事件，该事件的任何已注册监听器都将被 CDI 运行时通知或调用，并自动传递必要的事件对象。

#### 用于创建扩展的 SPI

CDI 提供了一个服务提供者接口，用于创建与容器集成的可移植扩展。CDI 可移植扩展的一个例子是 Apache DeltaSpike^(⁸⁷) 项目。

这些是 CDI 为 Jakarta EE 平台上的企业应用开发提供的主要特性。本章的其余部分将详细介绍如何在应用开发中利用这些特性。让我们首先了解如何在 Jakarta EE 应用中激活 CDI。

## CDI 激活

### Bean 发现模式

要理解 CDI 如何在应用中激活，我们首先需要讨论 Bean 发现模式的概念。Bean 发现模式决定了 CDI 运行时如何处理应用中的潜在 CDI 组件。有以下三种 Bean 发现模式。

#### None

此模式告诉 CDI 运行时在应用启动期间不要扫描任何 Bean。将 Bean 发现模式设置为此值实际上是在应用中禁用了 CDI。您几乎永远不会使用此选项。



#### 注解模式

注解模式的 bean 发现机制告知 CDI 运行时仅扫描带有 CDI bean 定义注解的组件。Bean 定义注解是 CDI 的构造，可用于以某种方式定义 bean。在注解模式的 bean 发现机制下，使用这些 CDI 构造定义的 bean 是隐式 bean。在没有显式设置或 CDI 配置文件的情况下，注解模式的 bean 发现机制是默认选项。这意味着，如果你没有显式设置 bean 发现模式，CDI 将仅扫描应用程序中的隐式 bean。

#### 全部模式

全部模式的 bean 发现机制告知 CDI 运行时扫描应用程序中的所有 bean，无论该 bean 是否带有显式的 CDI bean 定义注解。此模式显式启用 CDI。将 bean 发现模式设置为全部意味着你可以注入一个没有显式 CDI 注解的 POJO，运行时仍然能够为你创建实例。本书的参考应用程序将使用此 bean 发现模式。本章及本书后续章节的所有讨论均假设显式地将 bean 发现模式设置为全部。

### beans.xml 文件

CDI 的主要配置文件是 `beans.xml` 文件。它是一个简单的文件，可用于显式设置 bean 发现模式并注册其他 CDI 组件。清单 4-1 展示了一个典型的 beans 文件示例。

```

清单 4-1
jwallet 中使用的 beans.xml 文件
```

清单 4-1 展示了使用新的 4.0 模式文件的 `beans.xml` 文件。bean 发现模式值设置为 `all`。通过此 `beans.xml` 文件，我们在 jwallet 中显式启用了 CDI，并且通过将发现模式的值设置为 `all`，我们应用程序中的每个 bean/POJO 都会自动成为 CDI bean，并将在应用程序启动时被扫描。

## CDI 容器

“容器”一词通常与控制反转相关联。它指的是负责根据依赖方的请求创建、注入和销毁依赖实例的核心软件组件。在 Jakarta EE 平台上，CDI 容器是负责管理应用程序中各种不同组件之间关系的运行时。应用程序不同部分之间的依赖关系可能会变得非常复杂且难以手动管理。

CDI 容器负责在启动时扫描和验证你的应用程序，以确保没有违反 CDI 规则。违反 CDI 构造将阻止应用程序启动，因为 CDI 容器在扫描并发现违规时会抛出错误。由于容器的这种启动时扫描和验证，你很少会遇到运行时 CDI 错误。

CDI 容器还负责创建 bean 的上下文实例，为这些创建的实例添加相关的 CDI 增强功能，将它们分配到相关的上下文中，并最终在它们所绑定的上下文被销毁时销毁这些实例。你可以将 CDI 容器视为一个黑盒，它只在响应你的请求时出现，并在不需要时消失。作为开发者，你通常不需要过多考虑 CDI 容器，只需知道它在后台随时准备响应你的请求即可。

## Bean 与上下文实例

### 什么是 CDI Bean？

Bean 是 CDI 中最原子的组件。CDI 中的 bean 是任何具有无参构造函数或至少一个可注入构造函数的 Java 类。它可以选择性地使用一个或多个 CDI 注解进行显式注解。CDI bean 的少数限制是它不能是内部类或抽象类。CDI bean 与 JavaBean 没有太大区别。^(⁸⁸) 事实上，任何 JavaBean 都会自动成为 CDI bean。从技术上讲，这种 bean 被称为托管 bean——所谓“托管”，是指此类 bean 实例的创建和销毁完全由 CDI 容器管理。

另一种技术类型的 CDI bean 是会话 bean。会话 bean 是使用 EJB^(⁸⁹) 规范中任何 bean 定义元数据（即 `@Stateless`、`@Stateful` 和 `@Singleton`）进行注解的组件。任何使用这些注解之一进行注解的 bean，除了是 EJB 之外，还会自动成为 CDI bean，并拥有两个规范提供的特性和服务。我们将在本章后面的部分讨论 EJB。

你在 bean 类中实现应用程序逻辑，并可以选择使用一个或多个 CDI 元数据为其添加 CDI 特性。然后，你可以在其他组件（这些组件也是 bean）中声明对 bean 的依赖。清单 4-2 展示了一个 CDI bean 的示例。

```
public class WalletService {
}
清单 4-2
展示 WalletService 作为一个 CDI bean
```

清单 4-2 展示了一个名为 `WalletService` 的简单 POJO。这个类是一个完全有效的 CDI bean，可以注入到其他 bean 中。尽管 `WalletService` 还没有任何 CDI 属性，但由于我们在 `beans.xml` 文件中将 bean 发现模式设置为 `all`，它仍然会在启动时被容器扫描。我们可以通过使用一些 CDI 注解为 `WalletService` bean 赋予一些属性，从而显式地为其添加 CDI 特性。清单 4-3 展示了通过注解添加了一些 CDI 属性的 `WalletService`。

```
@RequestScoped
@Transactional
public class WalletService {
}
清单 4-3
展示带有 CDI 属性的 WalletService
```

清单 4-3 展示了被赋予了一些 CDI 属性的 `WalletService` bean。`@RequestScoped` 注解告诉 CDI 容器该 bean 的实例应置于何种上下文中。`@Transactional` 注解是一个拦截器，它将自动为在 `WalletService` 实例上调用的方法划分事务边界。你可以赋予 bean 的 CDI 属性集包括：限定符、作用域、替代方案和名称（我们将在本章中讨论这些）。可以通过使用 `@Inject` 注解标注一个 `WalletService` 类型的字段，将此 bean 注入到其他 bean 中，如清单 4-4 所示。

```
public class WalletResourceImpl implements WalletResource {
@Inject
WalletService walletService;
}
清单 4-4
展示对 WalletService CDI bean 的依赖声明
```

清单 4-4 展示了将 `WalletService` bean 注入到 `WalletResourceImpl` 组件中。`walletService` 变量的字段类型是 `WalletService`，这引出了一个问题：容器如何解析用于解析注入点的类型。



#### CDI Bean 类型

注入点是指任何期望由 CDI 容器实例化的字段或参数。例如，清单 4-4 中标注了 `@Inject` 的 `walletService` 字段就是一个注入点。CDI 容器会将 `WalletService` 类型的上下文实例注入到变量 `walletService` 中。但 CDI 容器如何知道对于任意给定的注入点，应该将变量解析为哪种类型呢？显然，我们需要一种方式来告知运行时环境我们 CDI Bean 的类型，以便它能正确地将每个可注入字段解析为其对应的类型。

从技术上讲，CDI Bean 是任何实现了 `jakarta.enterprise.inject.spi.Bean<T>`^(⁹⁰) 接口的 Java 类。该接口代表一个 Bean，并定义了 CDI 容器管理 Bean 实例所需的一切。`Bean` 接口本身扩展了 `jakarta.enterprise.inject.spi.BeanAttributes<T>`^(⁹¹) 接口。该接口包含以下方法：

*   `getName()` – 获取 Bean 的 EL 名称（如果 Bean 上使用了 `@Named` 注解）
*   `getQualifiers()` – 获取 Bean 的限定符集合
*   `getTypes()` – 获取 Bean 的类型集合
*   `getScope()` – 获取 Bean 的作用域
*   `getStereotypes()` – 获取 Bean 的原型集合
*   `isAlternative()` – 布尔方法，用于判断 Bean 是否为备选 Bean

这些方法返回相关信息，帮助 CDI 容器将注入点解析为正确的 Bean 类型。此外，`Bean` 接口还包含 `create()` 和 `destroy()` 方法，容器会调用它们来分别创建和销毁 Bean 实例。在实践中，你无需显式实现 `Bean` 接口。CDI 实现可以代表你，从你的 Bean 生成 `Bean<T>` 接口的实现。通过为你的 Bean 实现 `Bean<T>` 接口，CDI 容器可以通过其类型、类型与限定符的组合，以及通过名称（在表达式语言中）来解析 Bean 类型。

##### 按类型解析

任何 CDI Bean 的类型包括：该 Bean 的所有超类（直至 `java.lang.Object`）、所有直接或间接实现的接口，以及该类本身。以我们的 `WalletService` Bean 为例，它的类型将是 `java.lang.Object` 和 `WalletService`，因为它除了 `Object` 之外没有其他超类，也没有实现任何接口。然而，`WalletResourceImpl`（清单 4-4）有三种类型：它是 `Object`、`WalletResource` 和 `WalletResourceImpl`。

##### 按名称和限定符解析

CDI 中的限定符是一种标记或限定 CDI 组件的方式，以便 CDI 容器能进一步识别它们。如清单 4-4 所示，`WalletResourceImpl.java` 类实现了 `WalletResource` 接口。这意味着我们可以有一个类型为 `WalletResource` 的注入点，如清单 4-5 所示。

```
public class HelloResourceImpl implements HelloResource {
@Inject
WalletResource walletResource;
}
清单 4-5
展示通过接口类型注入字段
```

清单 4-5 展示了 `HelloResourceImpl` 在变量 `walletResource` 上声明了对 `WalletResource` 的依赖。`WalletResource` 是一个由 `WalletResourceImpl` 实现的接口。由于接口可以被多个类实现，清单 4-5 中声明的注入点 `walletResource` 需要被限定，因为 CDI 容器在运行时无法知道对于任何给定类型为 `WalletResource` 的注入点，应该注入哪个 `WalletResource` 实现。我们稍后讨论 CDI 限定符时会再回到这一点。

##### 按名称解析

前两种 Bean 解析机制，即按类型解析以及按类型和限定符解析，都是类型安全的。第三种解析 Bean 的方式是按名称解析。你可以在基于字符串的环境中（如 Jakarta EE 平台的表达式语言^(⁹²)）访问给定 Bean 的实例。通过在 Bean 上标注内置限定符 `@Named`，可以在 EL 中访问时解析该 Bean。清单 4-6 展示了标注了 `@Named` 的 `WalletService`。

```
@Named
@RequestScoped
@Transactional
public class WalletService {
}
清单 4-6
展示使用 @Named 在 EL 中解析 Bean
```

清单 4-6 展示了标注了 `@Named` 限定符的 `WalletService`。有了这个限定符，`WalletService` 就可以通过 EL 表达式访问。默认情况下，`WalletService` 可以通过 EL 表达式中自动可用的变量 `walletService` 来访问，例如：`#{walletService.someMethod()}`。当以这种方式访问时，CDI 容器会负责创建实例并将其注入到变量中。

无论使用哪种机制来解析 Bean 实例，CDI 容器都会为给定的字段类型创建正确的实例。由 CDI 容器为满足注入点而创建的 Bean 实例，其技术术语是**上下文实例**，这引出了一个问题：什么是上下文实例？

### CDI Bean 与上下文实例

在上一节中，我们看到 Bean 是一个 Java 类，它可选地关联了 CDI 元数据，并可用于满足注入点。因此，Bean 是用于满足应用程序中依赖请求的实例来源。由 CDI 容器为满足注入点而创建的 Bean 实例，在技术上被称为**上下文实例**。

我们在上一节中看到的 `Bean<T>` 接口也扩展了 `Contextual<T>` 接口。`Bean<T>` 接口的 `create` 和 `destroy` 方法正是继承自 `Contextual<T>`^(⁹³) 接口。从任何实现了 `Bean<T>` 接口的 Bean 创建的实例被称为上下文类型。由于 CDI 运行时通常会代表你创建 `Bean<T>` 接口的实现，最终你会得到注入到注入点中的上下文实例。

当你通过在注入点上使用 `@Inject` 注解向 CDI 容器请求 Bean 实例时，容器必须确保返回给你的上下文实例与正确的作用域相关联。Bean 实例总是与一个作用域相关联。你可以显式定义 Bean 应该关联到哪个作用域。如果你没有为某个给定的 Bean 显式定义作用域，容器会将该 Bean 的上下文实例与默认作用域关联起来。



## 作用域与上下文

CDI 创建的 bean 实例与作用域相关联。你可以将 CDI 作用域视为一个定义良好的生命周期，bean 实例在其中存在。当某个作用域被销毁时，该作用域关联的所有 bean 实例也会随之销毁。不同作用域的 bean 可以相互注入。CDI 容器负责确保将正确的 bean 注入到每个依赖注入点。

例如，我们有一个 SecurityManager，希望在整个应用程序生命周期（从启动到关闭）中只存在一个实例。这个 SecurityManager 控制器依赖于另一个控制器 UserService，后者作用于每个 HTTP 请求。清单 4-7 展示了 UserService 控制器。

```
@RequestScoped
@Transactional
public class UserService {
@Inject
UserRepository userRepository;
}
清单 4-7
展示 UserService 控制器
```

清单 4-7 展示了 UserService 类。它使用了 `@RequestScoped` 注解。这是内置作用域之一（稍后会详细介绍各种作用域）。该 bean 声明了对 UserRepository 的依赖，而 UserRepository 也是一个 RequestScoped bean。清单 4-8 展示了 SecurityManager 控制器。

```
@ApplicationScoped
public class SecurityManager {
@Inject
UserService userService;
}
清单 4-8
展示 SecurityManager 控制器
```

清单 4-8 展示了 SecurityManager 控制器及其对 UserService 控制器的依赖声明。请注意，SecurityManager 使用了 `@ApplicationScoped` 注解。这也是内置作用域之一。尽管 UserService 和 SecurityManager 关联了不同的作用域，但 CDI 容器会确保 SecurityManager 的所有客户端看到不同的 userService 上下文对象实例。这是通过使用 bean 代理的概念来实现的。

### Bean 代理

清单 4-8 中 SecurityManager 控制器里的 userService 引用，并非直接指向 `Contextual#create()` 调用返回的上下文实例，而是一个客户端代理对象。这个客户端代理对象实现了 bean 的部分或全部 bean 类型，并将对其的方法调用委托给目标上下文对象。为什么需要这种间接性呢？

回到我们的 SecurityManager 控制器，这个类在应用程序的整个生命周期中只有一个实例。然而，它声明了对一个作用于每个 HTTP 请求的 bean 的依赖。这意味着 SecurityManager 的每个客户端都应该注入不同的 UserService bean 实例。这种情况要求容器创建一个对 UserService 上下文实例的间接引用。容器需要保证，当任何有效的、对属于某个作用域的 bean 的注入引用被调用时，该调用始终由被注入 bean 的当前实例处理。

容器还需要管理可能的循环依赖。实现所有这些目标最清晰、最透明的方式就是使用客户端代理对象。这样，无论你的 bean 之间是什么依赖关系，都能保证在正确的作用域中获得正确的上下文实例。

### 内置作用域

CDI 提供了多个内置作用域，以满足大多数应用程序的常见需求。如果需要，你也可以创建自己的作用域。但为了构建一个内聚的平台，并为其他 API 提供统一的构建基础，CDI 规范总共提供了五个内置作用域。它们是 `@ApplicationScoped`、`@RequestScoped`、`@SessionScoped`、`@ConversationScoped` 和 `@Dependent` 伪作用域。

#### @ApplicationScoped

bean 上的 `@ApplicationScoped` 注解使其成为应用程序作用域的单例。这意味着，对于任何正在运行的应用程序实例的整个生命周期，只会创建该 `@ApplicationScoped` bean 的一个上下文实例。清单 4-8 中展示的 SecurityManager 控制器就是一个应用程序作用域的 bean。应用程序作用域本身在容器启动时创建。然而，该 bean 的第一个也是唯一的上下文实例，将在首次对代理调用方法时创建。创建之后，无论容器创建了多少个 SecurityManager bean 代理，所有方法调用都将在整个应用程序中被委托给同一个 bean 实例。

无论 SecurityManager 被注入到其他什么作用域中，所有方法调用最终都将由同一个 bean 实例处理。当应用程序在关闭期间销毁应用程序作用域时，该实例将被容器销毁。

#### @RequestScoped

bean 上的 `@RequestScoped` 注解将该 bean 与外部用户请求关联起来，这些请求可以来自 Servlet 请求、Web 服务请求或远程 EJB 请求。当 CDI 事件（我们将在后续章节讨论 CDI 事件）被分派给异步观察者方法时，容器也会创建一个请求作用域。

对于 Servlet 请求，当在 Servlet 或 Servlet 过滤器上分别调用 `service()` 或 `doFilter()` 方法时，CDI 容器会创建一个请求作用域。对于 Web 服务，无论是通过 JAX-RS 还是 JAX-WS，容器都会为每次 Web 服务调用创建一个请求作用域。在 EJB 处理远程 EJB 调用期间，也会为每次远程 EJB 调用创建一个请求。当 CDI 容器将事件分派给该特定事件的异步方法观察者时，也会创建一个请求作用域。对于所有这些调用，请求作用域将在调用开始时创建，并在调用结束时销毁。

清单 4-7 中展示的 UserService 控制器通过 `@RequestScoped` 注解作用于请求上下文。对于对 UserResourceImpl 资源的每次 Web 服务调用，都会创建一个新的请求作用域，并创建一个新的 UserService 上下文实例与该作用域关联，因为 UserResourceImpl 声明了对 UserService 控制器的依赖，如清单 4-9 所示。

```
@Consumes(MediaType.APPLICATION_JSON)
@Produces(MediaType.APPLICATION_JSON)
@Path("users")
public class UserResourceImpl implements UserResource {
@Inject
UserService userService;
}
清单 4-9
展示 UserResourceImpl 及其对 UserService 控制器的依赖声明
```

清单 4-9 展示了 `@RequestScoped` 的 UserService 控制器被注入到 UserResourceImpl REST Web 服务类中。对于在 UserServiceResourceImpl 类上调用的每个 REST 方法，都会创建一个新的请求作用域，并将 UserServiceResourceImpl 和 UserService 上下文实例与该作用域关联。方法调用返回后，该作用域及其关联的上下文实例都将被销毁。



#### @SessionScoped

会话作用域可以通过 `@SessionScoped` 注解与 Bean 关联。该作用域绑定到单个用户会话，并在同一 HTTP 请求中的所有 Servlet 请求之间共享。会话作用域在任何 Servlet 的 `service` 方法、任何 Servlet 过滤器的 `doFilter()` 方法以及容器调用任何 `ServletRequestListener`、`AsyncListener` 或 `HttpSessionListener` 时处于活动状态。当会话超时时，该作用域被销毁。但在会话被销毁之前，容器会确保该会话没有未调用的 `HttpSessionListeners`。这确保了当仍有监听器附加到会话时，会话不会被销毁。清单 4-10 展示了在 jwallet 的 `UserSession` 控制器中使用 `@SessionScoped` 注解。

```
@SessionScoped
public class UserSession implements Serializable {
@Inject
SecurityManager securityManager;
}
清单 4-10
展示 @SessionScoped 的使用
```

清单 4-10 展示了 `UserSession` 控制器。它声明了对 `SecurityManager` 的依赖。回想清单 4-8，`SecurityManager` 是一个 `@ApplicationScoped` Bean。`UserSession` 是我们看到的第一个实现了 `Serializable` 接口的 Bean。为什么？因为 Bean 上的 `@SessionScoped` 注解使 Bean 具备钝化能力。这意味着在运行时，当 CDI 容器确定存在空闲的会话作用域 Bean 实例时，它可以临时将这些 Bean 实例的状态转移到辅助存储中。实际上，容器会钝化或保存空闲实例的状态。`Serializable` 接口正是为此标记 Bean。此外，会话作用域 Bean 中的所有字段都应该是可序列化的或瞬态的。

#### @ConversationScoped

会话作用域是一种特殊的作用域，用于保存客户端和服务器之间的“会话”。它比请求作用域长，但比会话或应用程序作用域短。你可以通过一个跨多个页面的注册表单示例来理解会话。每个页面都是浏览器和服务器之间的一次会话，一旦你到达最后一页并点击提交按钮，会话就终止了。CDI 提供了内置的 `Conversation`^(⁹⁴) Bean，用于管理会话作用域 Bean 中的会话状态。清单 4-11 展示了一个会话作用域的 `SignupManager`。

```
@ConversationScoped
public class SignupManager implements Serializable {
@Inject
Conversation conversation;
public void beginSignup() {
beginConversation();
}
public void submit() {
endConversation();
}
private void beginConversation() {
if (conversation.isTransient()) {
conversation.begin();
}
}
private void endConversation() {
if (!conversation.isTransient()) {
conversation.end();
}
}
}
清单 4-11
展示 SignupManager 作为会话作用域 Bean
```

清单 4-11 展示了我们的注册 Bean。它也实现了 `Serializable` 接口，因为与会话作用域 Bean 类似，它可能会被容器钝化。同样，会话作用域 Bean 中的所有字段都应该是可钝化的或瞬态的。默认情况下，`@ConversationScoped` Bean 处于瞬态模式。也就是说，会话作用域 Bean 的上下文实例很可能随时被容器销毁，因为它处于瞬态。你必须显式地将一个会话作用域 Bean 启动为一个会话。实现这一点的方法是使用 `Conversation` 接口上的方法。

清单 4-11 展示了 `SignupManager` 通过一个该类型的注入点声明了对 `Conversation` Bean 的依赖。容器会为我们创建一个 `Conversation` 接口的实例。`beginSignup()` 方法首先调用 `beginConversation()` 方法。`beginConversation()` 方法首先通过调用 `Conversation#isTransient()` 来检查 Bean 是否处于瞬态模式。

如果 Bean 当前不处于任何会话状态，或者从技术上讲，不处于长期运行的会话中，则此方法返回 `true`。为什么要进行此检查？因为如果我们在一个已经处于长期运行会话中的 Bean 上调用 `Conversation#begin()` 方法，容器会抛出 `IllegalStateException`。`beginConversation()` 方法仅在该检查返回 `true` 时，通过调用 `Conversation#begin()` 方法来启动长期运行的会话。在此调用之后，Bean 被置入长期运行的会话或会话作用域中。此作用域将超越单个请求，但比会话作用域短。

当注册完成并准备提交时，`submit()` 方法首先调用 `endConversation()` 方法。此方法对 Bean 进行反向检查，以查看它是否处于长期运行的会话中，然后调用 `Conversation#end()` 方法。这也是因为如果你在一个非长期运行的 `Conversation` 上调用 `Conversation#end()`，容器会抛出 `IllegalStateException`。

`Conversation#begin` 方法还可以选择接受一个字符串参数作为会话 ID。此 ID 将与该方法启动的会话相关联。如果没有显式指定 ID，容器会为你生成一个。你可以通过调用 `Conversation#getId()` 方法获取与当前长期运行会话关联的 ID。如果会话处于瞬态，则返回 ID 或 `null`。你还可以分别通过 `Conversation#setTimeout()` 和 `Conversation#getTimeout()` 方法以毫秒为单位设置和获取会话的超时时间。

#### @Dependent 伪作用域

`@Dependent` 是一个伪作用域，它依赖于其使用位置。当没有定义显式作用域时，它是 Bean 被置入的默认作用域。之所以称为依赖作用域，是因为 `@Dependent` 作用域的 Bean 会采用或继承它被注入到的 Bean 的作用域。依赖作用域的 Bean 有两个显著特征，即：

*   被注入的 Bean 实例永远不会在多个注入点之间共享。
*   注入到容器正在创建的对象中的任何 Bean 实例都绑定到该创建对象的生命周期。

实际上，每次请求一个依赖作用域的 Bean 都会产生一个新的 Bean。并且这个依赖 Bean 实例的实际作用域将是它被注入到的任何 Bean 的作用域。我们将在本书后续章节介绍其他 CDI 结构时，进一步讨论依赖作用域。

到目前为止，我们已经讨论了 Bean 及其可能的作用域。CDI 的核心是在这些不同作用域的 Bean 之间管理依赖关系。在 CDI 中使用依赖管理魔杖的“入口点”是在注入点使用 `@Inject` 注解。

## 注入点

CDI 中的注入点是你请求上下文实例的地方。也就是说，你在特定位置使用 `@Inject` 注解标注一个类型，然后 CDI 容器会为你实例化该特定变量。CDI 中有三个主要的注入点，如下所示。



### 字段注入点

字段注入点是我们目前已经看到的方式。它只需在类中为给定 bean 类型的字段添加 `@Inject` 注解。在代码清单 4-12 中，`Conversation` 类型的字段 `conversation` 被标注了 `@Inject`。正如我们已经讨论过的，容器会负责将注解字段类型的正确上下文实例注入到变量 `conversation` 中。容器有效地保证了我们在运行时调用 `conversation` 变量上的方法时不会出现 `NullPointerException`。字段注入可以说是最流行，也是我们使用 CDI 请求上下文实例的首选方式。

### 构造器注入

CDI 中可用的第二种注入点是构造器注入点。还记得我们将 CDI bean 定义为任何具有无参构造器的 Java 类，或者至少有一个 CDI 可注入构造器的 bean 吗？构造器注入是指你使用 `@Inject` 注解 bean 的构造器，并将 CDI 可以实例化的 bean 作为参数传入。代码清单 4-12 展示了重构后的 `SignupManager` 对话作用域 bean，它使用构造器注入来实例化 `conversation` 字段。

```
@ConversationScoped
public class SignupManager implements Serializable {
    Conversation conversation;
    @Inject
    public SignupManager(final Conversation conversation) {
        this.conversation = conversation;
    }
}
代码清单 4-12
展示使用构造器注入点的 SignupManager
```

代码清单 4-12 展示了 `SignupManager` bean 使用构造器注入来实例化 `conversation` 字段。这个构造器注入将由容器自动调用。即使我们的 `SignupManager` 没有无参构造器，由于使用了构造器注入，它仍然是一个完全有效的 CDI bean。

需要注意的是，使用构造器注入时，传递给构造器的所有参数都必须是 CDI bean，或者至少是容器知道如何实例化的 bean（例如，通过生产者方法，我们将在后面的章节中讨论）。如果任何一个构造器参数不是有效的 CDI bean，应用程序将无法通过 CDI 启动验证。此外，每个 bean 只允许有一个构造器注入。你仍然可以拥有一个无参构造器和其他构造器。但是，拥有多个构造器注入将导致应用程序在启动时出错。

构造器注入的一个很好的用途是用于测试。当你需要为控制器编写单元测试时（例如，使用 Mockito^(⁹⁵)），你可以使用构造器注入在运行时初始化你的 bean 字段。在你的测试中，你仍然可以像处理任何 POJO 构造器一样，手动调用同一个构造器，并手动传入各种参数。

### 方法注入

方法注入点是指一个方法被标注了 `@Inject`。与构造器注入类似，该方法的所有参数都必须是 CDI bean，或者至少容器必须知道如何初始化它们。方法注入被称为初始化器方法。容器会自动调用这些方法，类似于构造器注入。

以上就是 CDI 中的三种主要注入点。然而，除此之外，还有另外两种注入点，其传入的参数是由 CDI 实例化的，无需显式的 `@Inject` 注解。它们是生产者方法和观察者方法。我们将在后面的章节中介绍这两种结构。

## Jakarta 企业 Bean

Jakarta 企业 Bean，以前称为 Enterprise JavaBeans 或 EJB，是一个早于 CDI 规范的规范。它是一个面向组件的规范，用于创建实现业务逻辑的 bean。它有自己的依赖解析机制。使用 `@Stateless`、`@Stateful` 和 `@Singleton` 中的任何一个注解标注一个 Java 类，就会使该类成为一个默认具有事务性、池化和安全性的组件。

CDI 的出现以及在 Jakarta EE 平台上需要一个核心的控制反转实现，意味着 CDI 必须重新实现 EJB 中的许多特性。然而，这两个规范已经协调一致，使得 CDI 成为创建业务层组件的单一核心入口点。尽管 CDI 是创建业务服务的首选方式，但 Jakarta 企业 Bean 仍然被广泛使用，并且在可预见的未来仍将如此。

所有 Jakarta 企业 Bean 自动成为 CDI bean。使用 Jakarta 企业 Bean 定义注解标注一个 bean，会使该 bean 同时成为一个 CDI bean 和一个 Jakarta 企业 Bean，并拥有这两个规范的所有特性。从 CDI 的角度来看，Jakarta 企业 Bean 被称为会话 bean。代码清单 4-13 展示了我们的 `SecurityManager`，现在多了两个注解。

```
@ApplicationScoped
@Singleton
@Startup
public class SecurityManager {
    @Inject
    UserService userService;
    public void saveUser(final CreateUserRequest request) {
        userService.createUser(request);
    }
}
代码清单 4-13
展示带有 EJB 注解的 SecurityManager
```

代码清单 4-13 展示了 `SecurityManager` bean 被标注了两个 Jakarta 企业 Bean 注解——`@Singleton` 和 `@Startup`。`@Singleton` 注解使这个 bean 成为一个单例 Jakarta 企业 Bean。`@Singleton` 注解与 `@ApplicationScoped` 注解在某种意义上没有太大区别，两者都导致在应用程序运行的生命周期内只存在该 bean 的一个实例。

`@Startup` 注解标记 `SecurityManager` 为立即实例化。这意味着我们的 bean 实例将在应用程序启动序列期间被创建。`@Singleton` 注解还默认使在 `SecurityManager` 上下文实例上调用的每个方法都具有事务性。对于每个方法调用，如果不存在事务，容器将加入或创建事务。

你可以使用立即实例化的 Jakarta 企业 Bean 单例，在你的应用程序所有其他组件准备就绪之前，执行某种形式的提前内务处理。例如，你需要在应用程序启动时准备好某种形式的数据，但你需要从外部服务加载这些数据。在这种情况下，你可以立即实例化一个单例并执行该调用。

如你所见，来自两个 API 规范的元数据彼此完全兼容。然而，在全新开发的应用程序中，你很可能几乎只使用 CDI。但是，仍然有大量现有代码仍在使用 Jakarta 企业 Bean 元数据。将此类代码维护或迁移到 CDI 应该相当简单，因为这两个规范可以很好地协同工作。我们将在关于 Jakarta 持久化的章节中，在深入讨论事务和事务边界时，再回到 Jakarta 企业 Bean。现在，让我们通过查看限定符来继续我们的 CDI 讨论。



## 限定符

限定符是一种向 CDI 容器提供提示的方式，用于指示在满足特定依赖注入点时应该使用哪个确切的 Bean 类型。在清单 4-5 中，我们将 `WalletResource` 注入到了 `HelloResourceImpl` 中。清单 4-14 展示了 `WalletResource` 接口。

```
@RegisterRestClient(baseUri = BASE_URI)
@Consumes(MediaType.APPLICATION_JSON)
@Produces(MediaType.APPLICATION_JSON)
@Path("wallets")
public interface WalletResource {
String BASE_URI = "http://wallet:3001/wallet/api";
}
清单 4-14
展示 WalletResource 接口
```

清单 4-14 展示了 `WalletResource` 接口。这是一个 REST 端点声明，它使用了 Eclipse MicroProfile REST 客户端 API 中的 `@RegisterRestClient()` 注解（本书后面会有专门章节介绍此内容），来为此 Jakarta REST 资源自动创建一个 REST 客户端。

如清单 4-5 所示，我们通过其接口将此 Bean 注入到了 `HelloResourceImpl` 中。尝试启动应用程序将会失败，并显示错误信息：“对于类型 `WalletResource`，使用限定符 `@Default` 存在歧义的依赖关系。”这是因为 `WalletResource` 接口在应用程序中有两个实现。一个是属于我们应用程序代码的 `WalletResourceImpl`。另一个是由 Eclipse MicroProfile REST 客户端自动生成的 REST 客户端。

由于该接口存在多个可能的实现，CDI 容器需要确切地知道在运行时应该使用哪个具体实现，以满足在 `HelloResourceImpl` 中声明的注入点。而且，由于我们没有指定任何限定符，默认的限定符 `@Default` 不足以告知容器应该使用哪个实现，因此在应用程序启动验证期间，运行时会抛出错误并终止应用程序启动。

为了解决这个问题，让我们为 `WalletResource` 接口声明一个限定符。清单 4-15 展示了 `WalletResource` 的限定符声明。

```
@Qualifier
@Retention(RUNTIME)
@Target({FIELD, TYPE, METHOD, PARAMETER})
public @interface Wallet {
}
清单 4-15
展示 @Wallet 限定符声明
```

清单 4-15 展示了 `@Wallet` 限定符声明。CDI 中的限定符是一个普通的 Java 注解，它带有来自 CDI API 的 `@Qualifier` 注解。`@Wallet` 限定符将 `@Target` 值设置为字段、类型、方法和参数。这意味着它可以用于字段、类、方法或参数上。声明了注解之后，我们现在需要将限定符与一个具体类型关联起来。在这个例子中，我们希望将 `@Wallet` 限定符与我们的 `WalletResource` 实现关联起来。清单 4-16 展示了在 `WalletResourceImpl` 上使用 `@Wallet` 限定符。

```
@Consumes(MediaType.APPLICATION_JSON)
@Produces(MediaType.APPLICATION_JSON)
@Path("wallets")
@Wallet
public class WalletResourceImpl implements WalletResource {
@Inject
WalletService walletService;
}
清单 4-16
展示在 WalletResourceImpl 上使用 @Wallet
```

清单 4-16 展示了与 `WalletResourceImpl` 关联的 `@Wallet` 限定符。有了这个设置，我们现在就有了一种机制，可以告诉 CDI 容器，对于由 `WalletResource` 接口类型声明的注入点，应该注入 `WalletResource` 的哪个确切实现。清单 4-17 展示了 `HelloResourceImpl` 中使用 `@Wallet` 限定符限定的 `WalletResource` 注入点。

```
@Path("hello")
public class HelloResourceImpl implements HelloResource {
@Inject
@Wallet
WalletResource walletResource;
}
清单 4-17
展示在 HelloResourceImpl 中使用 @Wallet 限定符限定 walletResource 字段
```

清单 4-17 向我们展示了使用 `@Wallet` 限定符来限定 `walletResource` 字段。有了这些设置，我们就是在告诉容器，将具体类型 `WalletResourceImpl` 的一个上下文实例注入到 `HelloResourceImpl` 中的 `walletResource` 字段。完成此构造后，应用程序应该能够正常启动，并且不会抛出任何验证错误。

### @Default 限定符

在本节前面，我们看到了当 `walletResource` 字段存在歧义的依赖关系解析时，容器抛出的错误信息。该信息显示为：“对于类型 `WalletResource`，使用限定符 `@Default` 存在歧义的依赖关系。”该信息中提到的 `@Default` 限定符是一个内置限定符，在没有任何显式限定符的情况下，每个 Bean 都默认拥有它。只要某个 Bean 没有声明或关联任何限定符，运行时就会隐式地将其注解在每个 Bean 类型上。`@Default` 实际上就是默认限定符。

### 其他内置限定符

Jakarta EE 平台以及 Eclipse MicroProfile 项目附带了许多内置限定符。在“按名称解析”一节中，我们看到了 `@Named` 限定符。此限定符接受一个可选的字符串值，该值成为 Bean 在表达式语言中的名称。`@Named` 是一个内置限定符。

另一个内置限定符是 `@RestClient` 限定符。在清单 4-14 中，我们看到 `WalletResource` 声明带有注解 `@RegisterRestClient(baseUri = BASE_URI)`。此注解告诉 Eclipse MicroProfile REST 客户端在运行时为我们创建此 Bean 的一个实现。如何获取这个已创建的 Bean 呢？清单 4-18 再次展示了 `HelloResourceImpl`，但这次，它额外注入了相同 `WalletResource` 类型但带有不同限定符的实例。

```
@Path("hello")
public class HelloResourceImpl implements HelloResource {
@Inject
@Wallet
WalletResource walletResource;
@Inject
@RestClient
WalletResource restClient;
}
清单 4-18
展示使用来自 Eclipse MicroProfile 项目的内置限定符来限定 WalletResource
```

清单 4-18 展示了另一个相同 `WalletResource` 类型的字段声明，该字段使用了来自 Eclipse MicroProfile 项目的 `@RestClient` 限定符进行限定。`restClient` 变量将在运行时被注入一个由 MicroProfile 生成的 `WalletResource` 实现。`@RestClient` 注解将为我们提供一个上下文实例，该实例实现了接口的所有方法，并且可以直接使用。值得注意的是，`walletResource` 和 `restClient` 字段都是相同的类型——`WalletResource`。这两个字段都是通过它们的接口定义的。在没有限定符的情况下，容器将不知道如何处理这种歧义的依赖关系声明。

CDI 中的限定符构造是一种看似简单但功能强大的机制，它可以帮助您轻松编写非常清晰、可读且可测试的代码。我们将在本章和本书的其余部分继续讨论和使用限定符。接下来，让我们看看 CDI 中的生产者概念。到目前为止，我们看到的所有 Bean 都是我们以某种方式从头声明的——要么是接口，要么是具体的 Java 类。然而，有时您会发现自己需要将不属于您代码的 Bean 转换为应用程序中的 CDI Bean。



## CDI 生产者

CDI 中的生产者是一种将 Bean 或不属于应用程序代码的 Java 类型转换为可在应用程序中使用的 CDI Bean 的方式。我们将 CDI Bean 定义为任何具有无参构造器或至少有一个使用 `@Inject` 注解的构造器的 Java 类。然而，应用程序中可能需要某些 Java 类型，但它们不一定是有效的 CDI Bean。例如，清单 4-19 展示了 `BaseRepository` Bean 中一个类型为 `EntityManager` 的字段注入点。

```
public abstract class BaseRepository {
@Inject
EntityManager em;
}
清单 4-19
展示尝试将平台类注入到应用程序定义的 Bean BaseRepository 中
```

清单 4-19 展示了 `BaseRepository` Bean 中一个类型为 `EntityManager` 的字段注入点声明。尝试部署该应用程序将会失败，并显示错误信息：“在注入点 @Inject com.example.jwallet.core.control.BaseRepository.em 处，对于带有限定符 @Default 的类型 EntityManager 存在未满足的依赖关系”。CDI 容器报错指出该字段类型存在未满足的依赖关系。这意味着 CDI 运行时不知道如何实例化 `EntityManager` 类的上下文实例。

`EntityManager` 是一个 Jakarta EE 平台类。它是一个由 Jakarta Persistence 运行时提供实现的接口。像我们这样将其用作注入点类型是行不通的，因为 CDI 无法实例化它。为了解决这个问题，我们可以使用生产者。借助 CDI 生产者，你可以告诉容器某个给定的 Bean 类型是在哪里“生产”的，这样每当有对该 Bean 的上下文实例的请求时，容器就会去你的生产者那里获取实例。CDI 生产者有两种类型：生产者字段和生产者方法。

### 生产者字段

生产者字段指的是从另一个 Bean 的字段中“生产”CDI Bean。清单 4-20 展示了一个生产者字段，它使用 CDI 的 `@Produces` 注解来“生产”`EntityManager` 实例。

```
public class ProducerFactory {
@Produces
@PersistenceContext
EntityManager entityManager;
}
清单 4-20
展示 EntityManager 生产者字段
```

清单 4-20 展示了一个用于 `EntityManager` 类型的生产者字段。`ProducerFactory` Bean 有一个类型为 `EntityManager` 的字段，该字段使用了 `@PersistenceContext` 和 `@Produces` 注解。`@PersistenceContext` 是 Jakarta Persistence API 中的一个注解，用于注入 `EntityManager` 实例。然而，由于这个注解不是 CDI 注解，因此返回的实例并非 CDI Bean。

再次使用 `@Produces` 注解该字段，可以将 `@PersistenceContext` 注解返回的 `EntityManager` 实例转换为一个 CDI Bean。实际上，我们是在使用 `@Produces` 注解将 Jakarta Persistence 创建的 `EntityManager` 实例转换为一个 CDI Bean，并使其具备所有 CDI 服务。当 CDI 容器遇到 `BaseRepository` Bean（清单 4-19）中类型为 `EntityManager` 的字段注入点时，它就知道应该到这个字段来获取上下文实例。

生产者并不局限于生产普通的上下文实例。你可以显式地设置一个作用域，使其与生产的上下文实例相关联。如果没有显式指定作用域，则默认应用 `@Dependent` 作用域。在清单 4-20 的示例中，生产的 `EntityManager` 上下文实例将处于 `@Dependent` 作用域。然而，为生产者字段设置显式作用域有一个注意事项。如果生产者字段在运行时被访问时包含 `null` 值，并且它具有除 `@Dependent` 之外的任何其他作用域，那么容器将抛出 `IllegalProductException`。生产者字段可以是 CDI 管理的 Bean 类中的静态或非静态的默认访问、受保护、公共或私有字段。

#### 限定生产者字段

你也可以使用限定符来限定生产者字段。假设 jwallet 使用了两个数据库——Apache Derby 和 Postgres。你可以有两个生产者字段，每个都用特定的限定符进行限定，这样你就可以注入并限定注入点。在关于限定符的章节中，我们创建了 `@Wallet` 限定符作为一个简单的无参注解。然而，限定符可以接受参数。与其创建两个限定符来限定为每个数据库生产的 `EntityManager`，不如创建一个接受参数的单一限定符。在清单 4-21 中，我们创建了 `@Database` 限定符，它接受一个类型为 `DB` 的参数。

```
@Qualifier
@Retention(RUNTIME)
@Target({FIELD, TYPE, METHOD, PARAMETER})
public @interface Database {
DB value();
enum DB {
DERBY,
POSTGRES
}
}
清单 4-21
展示 @Database 限定符定义
```

清单 4-21 展示了 `@Database` 限定符，它有一个类型为 `DB` 的 `value()` 字段，用于向限定符传递参数。`DB` 是在限定符类中定义的一个枚举，包含两个字段：`DERBY` 和 `POSTGRES`。清单 4-22 展示了如何使用 `@Database` 限定符来限定两个 `EntityManager` 生产者字段。

```
public class ProducerFactory {
@Produces
@Database(DB.DERBY)
@PersistenceContext(unitName = "jwallet-derby")
EntityManager derby;
@Produces
@Database(DB.POSTGRES)
@PersistenceContext(unitName = "jwallet-postgres")
EntityManager postgres;
}
清单 4-22
展示使用 @Database 限定符来限定生产者字段
```

清单 4-22 展示了 `ProducerFactory` Bean，它有两个生产者字段，类型都是 `EntityManager`。然而，这两个字段都使用了 `@Database` 限定符进行限定。`derby` 字段生产者由 `@Database(DERBY)` 限定符限定，而 `postgres` 字段由 `@Database(POSTGRES)` 限定符限定。

这两个 `EntityManager` 字段关联到两个不同的持久化上下文，如传递给 `@PersistenceContext` 注解的 `unitName` 参数的值所示（Jakarta Persistence 将在另一章中讨论）。现在我们有了两个相同类型的字段，它们生产相同类型但具有不同限定符的实例。清单 4-23 展示了在 `BaseRepository` 中使用 derby `EntityManager` 生产者字段的情况。

```
public abstract class BaseRepository {
@Inject
@Database(DB.DERBY)
EntityManager em;
}
清单 4-23
展示 derby 生产者字段的使用
```

清单 4-23 展示了一个类型为 `EntityManager` 的注入点，该注入点使用了 `@Database(DERBY)` 限定符进行限定。在运行时，容器将使用 `ProducerFactory` Bean 中 `derby` 字段生产的上下文实例来填充 `BaseRepository` Bean 中的 `em` 字段。通过这种方式，即使 `EntityManager` Bean 不属于应用程序代码，通过 CDI 生产者字段结构，我们仍然能够将其转换为一个 CDI Bean。



### 生产者方法

生产者方法与生产者字段类似，它们都能生成上下文实例。然而，生产者方法基于其返回类型来生成上下文实例。一个被 `@Produces` 注解的方法将生成其返回类型的实例。生产者方法也可以接受参数。但是，传递给生产者方法的所有参数都是注入点，并由容器负责处理，因为生产者方法是由容器自动调用的。清单 4-24 展示了一个生成 `java.util.logging.Logger` 对象的生产者方法。

```
public class ProducerFactory {
@Produces
@Database(DB.DERBY)
@PersistenceContext(unitName = "jwallet-derby")
EntityManager derby;
@Produces
@Database(DB.POSTGRES)
@PersistenceContext(unitName = "jwallet-postgres")
EntityManager postgres;
@Produces
public Logger getLog(final InjectionPoint injectionPoint) {
return       Logger.getLogger(injectionPoint.getMember().getDeclaringClass().getName());
}
}
清单 4-24
展示生产者方法生成 Logger 对象
```

清单 4-24 展示了 `ProducerFactory` Bean，这次它包含了生产者方法 `getLog()`。该方法返回一个 `java.util.logging.Logger` 对象。它还接受一个 `InjectionPoint` 类型的参数。该参数将由 CDI 容器注入（这是方法注入的一个例子）。`InjectionPoint` 是一个 CDI 组件，包含关于注入点的元数据。`getLog` 方法的实现通过调用 `InjectionPoint#getMember#getDeclaringClass#getName()` 将生成的 Logger 的名称设置为它被注入到的类。有了这个生产者方法，注入一个 Logger 实例就变得非常简单，只需在 `Logger` 类型的字段上使用 `@Inject` 注解即可，如清单 4-25 所示。

```
public abstract class BaseRepository {
@Inject
@Database(DERBY)
EntityManager em;
@Inject
Logger logger;
}
清单 4-25
展示将 Logger 注入到 BaseRepository Bean 中
```

清单 4-25 展示了 `BaseRepository`，它增加了一个 `Logger` 类型的字段注入。在运行时，CDI 容器会查询 `getLog` 生产者方法以获取上下文实例来满足这个注入点。由于我们没有在 `ProducerFactory` Bean 的生产者方法和字段上设置显式的作用域，因此生成的 `EntityManager` 和 `Logger` 的上下文实例将被分配给 `@Dependent` 伪作用域。

通常，你会使用生产者方法来实例化那些需要某种自定义初始化（可能无法在 Bean 的构造函数中完成）的 Bean。例如，你可能需要调用外部 Web 服务来初始化某个 Bean。在这种情况下，你可以使用生产者方法将你手动实例化的 Bean 实例转换为 CDI 管理的对象。这是因为你手动实例化的 Bean 不受 CDI 运行时的管理。生产者方法的访问修饰符无关紧要。它也可以是静态的或非静态的。通过使用 CDI 生产者构造，我们将另一个不属于应用程序代码的类转换成了 CDI Bean。

### CDI 生产者 – 注意事项

在使用 CDI 生产者时，有几点需要注意。以下情况会在应用程序启动时导致 CDI 定义错误：

*   你不能使用 `@Inject` 注解生产者方法或字段。
*   生产者方法的参数不能使用 `@Disposes`、`@Observes` 或 `@ObservesAsync` 注解。
*   拦截器不能定义生产者方法或字段。

## 原型

在应用程序开发中，尤其是使用 CDI 时，你经常会发现为不同目的创建的 Bean 需要相同的一组通用元数据。对于任何非平凡的应用程序，你最终可能会在 Bean 上重复大量相同的元数据。当你需要为所有这些 Bean 添加功能时，这会变得相当繁琐。

例如，清单 4-26 和 4-27 展示了 `WalletService` 和 `UserService` Bean 及其 CDI 元数据。

```
@RequestScoped
@Transactional
public class UserService {
}
清单 4-27
展示 UserService Bean 及其元数据。
```

```
@RequestScoped
@Transactional
public class WalletService {
}
清单 4-26
展示 WalletService Bean 及其元数据
```

清单 4-26 和 4-27 都展示了两个不同的 CDI Bean 具有完全相同的 CDI 元数据。两者都是请求作用域且支持事务的。尽管这两个 Bean 将执行不同的功能，但它们确实需要完全相同的一组 CDI 服务。如果我们想将一个拦截器（下一节将详细介绍拦截器）与这两个 Bean 关联起来，我们就必须在两个 Bean 上重复拦截器注解。显然，一遍又一遍地重复相同的元数据并不是编写代码的理想方式。

CDI 规范提供了原型构造，用于将常见的架构模式组合成一个单一的、通用的注解。因此，对于清单 4-26 和 4-27 中展示的 Bean，原型允许我们将它们集中在一个通用注解下，而不是重复相同的 CDI 注解。在清单 4-28 中，我们创建了一个 `@Action` 原型，它同时具有 `@RequestScoped` 和 `@Transactional` 特性。

```
@Stereotype
@RequestScoped
@Transactional
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE)
public @interface Action {
}
清单 4-28
展示 Action 原型
```

清单 4-28 展示了 `@Action` 原型的声明。一个 CDI 原型是一个被 `@Stereotype` 注解的 Java 注解。对于 `@Action` 原型，我们声明它是 `@RequestScoped` 和 `@Transactional` 的。该原型的 `@Target` 也被设置为 `TYPE`，因为我们只想将此原型与 Bean 关联，而不是与方法、字段或参数关联。所有使用此原型注解的 Bean 将自动成为请求作用域并支持事务，就像这些 Bean 被显式地使用这些注解进行注解一样。声明了 `@Action` 原型后，我们现在可以重构 `UserService` 和 `WalletService` Bean 来使用它。清单 4-29 展示了重构后使用 `@Action` 原型的 `WalletService`。

```
@Action
public class UserService {
}
清单 4-30
展示使用 @Action 原型的 UserService
```

```
@Action
public class WalletService {
}
清单 4-29
展示使用 @Action 原型的 WalletService
```

清单 4-29 和 4-30 展示了重构后使用 `@Action` 原型的两个 CDI Bean。这意味着这两个 Bean 仍然是请求作用域且支持事务的。但是，那些样板式的声明性元数据已经被抽象到一个原型中。如果我们需要将所有控制器 Bean 与一个拦截器关联起来，我们只需将拦截器与原型关联，我们的 Bean 就会自动被拦截。



Bean 可以通过声明自己的作用域来覆盖与某个原型关联的作用域。Bean 声明的作用域会覆盖其原型关联的作用域。例如，如果 `WalletService` 除了原型之外还显式声明了另一个作用域，那么 `WalletService` 声明的作用域将优先于原型声明的作用域。除了内置的 `@Named` 限定符之外，你不能将限定符与原型关联。将原型与除 `@Named` 之外的限定符关联会导致 CDI 定义错误。你还可以将原型与其他原型关联。这样做时，第二个原型会继承第一个原型的所有元数据。

## 生命周期回调

CDI Bean 的上下文实例由容器完全管理——从创建到销毁。然而，有时你可能希望在 Bean 实例化后立即执行一些准备工作，或者在其销毁前执行一些操作。在这两种情况下，你都可以通过钩入生命周期回调来完成这些任务。这些生命周期回调会在适当的时候由容器自动为你调用。

### @PostConstruct

要在 Bean 实例化后立即执行一些自定义的准备工作，你可以使用 `@PostConstruct` 注解一个方法。该方法必须返回 `void` 且不接受任何参数（拦截器除外）。它可以是 public、private 或包私有方法，且不能是静态的。被注解的方法将在 Bean 实例化且其所有依赖项都已正确实例化后，由容器立即调用。清单 4-31 展示了 `SecurityManager` 中的一个 postconstruct 生命周期回调方法。

```
@ApplicationScoped
@Singleton
@Startup
public class SecurityManager {
@Inject
UserService userService;
@Inject
UserSession userSession;
@PostConstruct
private void init() {
//执行准备工作。
}
}
清单 4-31
展示 SecurityManager 中的 @PostConstruct 方法
```

清单 4-31 展示了 `SecurityManager` 中一个使用 `@PostConstruct` 注解的生命周期回调方法。该方法将在 Bean 的上下文实例投入使用之前，但在其依赖项初始化之后由容器调用。在此示例中，该方法仅在 `userService` 和 `userSession` Bean 被容器完全初始化之后才会被调用。由于此 Bean 是应用作用域的，因此 `init` 生命周期回调方法只会被调用一次——即在 Bean 首次创建且即将投入使用之前。或者更技术地说，在第一个代理方法被委托给 `SecurityManager` 的（唯一）实例之前。

### @PreDestroy

`@PreDestroy` 与 `@PostConstruct` 相反。当声明了该方法的 Bean 实例即将被容器销毁时，容器会调用使用 `@PreDestroy` 注解的方法。`@PreDestroy` 通常用于释放 Bean 实例可能持有的资源，例如文件。`@PostConstruct` 回调方法的限制同样适用于 `@PreDestroy` 回调方法。

## CDI 拦截器

在应用程序开发中，你经常会发现某些 Bean 需要一些与其核心功能无关的正交服务。例如，一个应用程序可能需要对某些方法的调用进行某种形式的自定义日志记录。日志记录是一个横切关注点，因为可能有多个 Bean 都需要它。

在我们的 Bean 方法中添加大量的日志消息可能会很快变得非常令人不悦且重复，因为几乎所有日志记录都具有相同的格式和消息，可能只是方法名有所变化。如果我们能够将此类横切关注点抽象到其他结构中——即允许在不修改 Bean 代码的情况下向 Bean 添加功能的结构——那将会优雅得多。

面向切面编程（AOP）^(⁹⁶) 是一种范式，旨在解决将横切功能抽象为简单且可定制的结构（或切面）的问题，这些结构可以根据需要启用或禁用。CDI 规范为你提供了拦截器的概念，以便在你的应用程序中实现 AOP。CDI 扩展了 Jakarta Interceptors 规范。

在讨论原型时，你已经看到了你的第一个拦截器。清单 4-28 中展示的 `@Action` 原型带有 `@Transactional` 注解。`@Transactional` 是 JTA^(⁹⁷) 规范提供的一个内置拦截器，用于拦截对被注解的 Bean 或方法的调用，并控制这些方法的事务。

实现 CDI 拦截器包含两个部分。第一部分是拦截器绑定注解。这个注解是拦截器的客户端将使用的。例如，`@Transactional` 注解是事务拦截器的一部分。另一部分是将该注解与实现拦截器方法的 Bean 关联起来。

### 拦截器注解

对于 jwallet，我们有一个需求：当某些方法被调用时，记录当前登录用户的名称、日期和时间。你需要使用拦截器绑定注解来注解那些需要被记录日志的选定方法。拦截器绑定类则负责实现记录规定信息的实际过程。对于我们的日志记录拦截器需求，清单 4-32 展示了拦截器绑定注解。

```
@Inherited
@Target({TYPE, METHOD})
@Retention(RUNTIME)
@InterceptorBinding
public @interface Logged {
}
清单 4-32
展示日志记录拦截器绑定注解
```

清单 4-32 展示了 `@Logged` 拦截器绑定注解。这是一个普通的 Java 注解，带有来自 Jakarta Interceptors 规范的 `@InterceptorBinding` 注解。有了这个注解，我们现在需要将其与一个将执行注解实际工作的 Bean 关联起来。清单 4-33 展示了拦截器绑定 Bean `LoggingBean` 如何实现该拦截器。



### 拦截器 Bean

```
@Interceptor
@Logged
@Priority(Interceptor.Priority.APPLICATION)
public class LoggingBean {
@Inject
Logger logger;
@AroundInvoke
//@AroundConstruct
public Object logMethod(final InvocationContext invocationContext) throws Exception {
logger.log(Level.FINE,
() -> String.format("Method %s invoked at %s",   invocationContext.getMethod().getName(),
LocalDateTime.now(ZoneOffset.UTC)));
return invocationContext.proceed();
}
}
清单 4-33
展示 LoggingBean 拦截器类
```

清单 4-33 展示了 LoggingBean 拦截器类。它分别被注解为 `@Interceptor` 和 `@Logged`。这两个注解使该类成为一个拦截器 Bean，并将 `@Logged` 注解与之关联。这意味着无论 `@Logged` 注解在何处使用，拦截期间要执行的实际工作都将由 LoggingBean 类中的一个方法完成。它还带有 `@Priority(Interceptor.Priority.APPLICATION)` 注解。这就是在 Jakarta EE 中激活拦截器并为其排序的方式。`@Priority` 接受一个 `int` 值；该 `int` 值越小，注解的优先级越高。

`Interceptor.Priority.APPLICATION` 是一个常量，其 `int` 值为 2000。如果另一个拦截器将其 `@Priority` 值设置为更小的 `int` 值，那么该拦截器在调用顺序上将优先于 `@Logged` 拦截器。过去，拦截器需要在 XML 配置文件中注册。但这种情况在 Java EE 7 中发生了变化。现在，你使用 `@Priority` 注解来激活拦截器并为其排序。

然后，LoggingBean 注入由我们的生产者方法生成的 Logger 类，并声明一个名为 `logMethod` 的方法，该方法接受一个参数——`InvocationContext`。当容器调用该方法执行拦截时，CDI 容器会注入此参数。在运行时，容器会向该方法提供一个 `InvocationContext` 接口的实现。该接口提供关于被拦截调用的上下文信息，并且还包含控制调用链行为的方法。对于日志记录，会调用 `InvocationContext#getMethod#getName` 方法来获取被拦截方法的名称。

`logMethod` 方法带有 `@AroundInvoke` 注解。此注解告诉容器我们希望拦截器在何时生效。顾名思义，`@AroundInvoke` 意味着拦截器应在带注解的方法被调用之前被调度执行。因此，就在容器调用任何带有 `@Logged` 注解的方法之前，`logMethod` 将被自动调用来执行拦截功能。该方法可以调用它所拦截的目标方法能够调用的任何服务。

另一个可用于拦截 Bean 的注解是 `@AroundConstruct`。此注解会使容器在目标 Bean 的构造函数被调用之前，调用被注解的方法。通常，你需要谨慎使用 `@AroundConstruct`，因为传递给拦截器方法的 `InvocationContext` Bean 可以访问构造函数的所有参数。因此，在使用它时应格外小心。

带有 `@AroundInvoke` 注解的方法必须返回一个 `Object`。如清单 4-33 所示，`logMethod` 方法通过调用 `InvocationContext#proceed()` 方法返回 `Object`。此调用向容器发出信号，表明该方法已完成，执行应继续到链中的下一个拦截器（如果有的话），否则继续调用实际方法。带有 `@AroundConstruct` 注解的方法返回 `Object` 将导致执行移动到链中的下一个拦截器，否则继续实例化被拦截的 Bean。

完全声明好我们的拦截器后，我们可以通过使用它来注解类或方法。清单 4-34 展示了应用于 `WalletService` 的 `@Logged` 拦截器。

```
@Action
@Logged
public class WalletService {
}
清单 4-34
展示应用于 WalletService 的 @Logged 注解
```

清单 4-34 展示了应用于 `WalletService` 的 `@Logged` 拦截器。类似地，清单 4-35 展示了应用于 `UserService` 的 `@Logged` 注解。

```
@Action
@Logged
public class UserService {
}
清单 4-35
展示应用于 UserService 的 @Logged 拦截器
```

清单 4-35 展示了应用于 `UserService` 的 `@Logged` 拦截器。由于拦截器是在类级别应用于 Bean，因此对任一类的上下文实例调用的所有方法都将被我们的拦截器拦截。我们也可以选择性地只注解 Bean 中的某些方法，因为 `@Logged` 拦截器的 `@Target` 既是 `TYPE` 也是 `METHOD`。

### 将拦截器与原型结合使用

你会立即注意到，两个被拦截的 Bean 具有完全相同的注解集合：一个是原型，另一个是拦截器。在关于原型的章节中，我们讨论了它们的全部意义在于抽象出通用的架构模式。因此，我们可以将拦截器添加到 `@Action` 原型中，而不是单独为每个 Bean 添加 `@Logged` 注解。这样，拦截器会自动应用于所有带有该原型的 Bean。该拦截器仍然可以单独用于拦截方法或整个类。清单 4-36 展示了添加到 `@Action` 原型的 `@Logged` 拦截器注解。

```
@Stereotype
@RequestScoped
@Transactional
@Logged
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE)
public @interface Action {
}
清单 4-36
展示与 @Logged 关联的 @Action 注解
```

清单 4-36 展示了带有 `@Logged` 拦截器注解的 `@Action` 原型。这现在将拦截器与原型关联起来，使得任何带有该原型的 Bean 也会被自动拦截。因此，我们可以从 `WalletService` 和 `UserService` Bean 中移除 `@Logged` 注解。拦截器是在应用程序中实现横切关注点的一种直接方式。然而，为了扩展或覆盖 Bean 的方法，CDI 还有另一种可用于此目的的构造。



## CDI 装饰器

CDI 装饰器允许将一个 Bean 包裹在另一个 Bean 内部，从而使包裹 Bean 能够改变被包裹 Bean 的行为。与允许你实现一组特定 Bean 所共有的横切关注点的拦截器不同，装饰器允许你改变特定 Bean 方法的实现。因此，拦截器通常适用于多个 Bean，而装饰器则与单个 Bean 绑定，因为它们实现了与被装饰 Bean 相关的业务方法。例如，你可以使用装饰器来更改你不拥有的 Bean 的功能。CDI 装饰器允许你在应用程序中实现装饰器设计模式。

为了更好地理解它们，让我们看一个例子。欧盟的 GDPR^(⁹⁸) 法规要求所有欧盟居民使用的应用程序必须将其数据存储在欧盟境内的服务器上。为了满足这一要求，我们可以装饰 `UserService` Bean，以实现这一自定义需求。首先，让我们通过引入一个接口来重构 `UserService`，如清单 4-37 所示。

```
public interface UserFacade {
UserResponse createUser(final CreateUserRequest createUserRequest);
UserResponse getUser(final Long id);
}
清单 4-37
展示了 UserFacade 接口
```

清单 4-37 展示了包含两个方法（`createUser` 和 `getUser`）的 `UserFacade` 接口。该接口是一个业务接口，由 `UserService` 实现，如清单 4-38 所示。

```
@Action
public class UserService implements UserFacade {
@Inject
UserRepository userRepository;
@Override
public UserResponse createUser(final CreateUserRequest createUserRequest) {
User user = fromUserWrapper(createUserRequest);
user = userRepository.save(user);
UserResponse userResponse = getUser(user.getId());
return userResponse;
}
@Override
public UserResponse getUser(final Long userId) {
final User user = userRepository.findById(userId);
return toUserResponse(user);
}
}
清单 4-38
展示了 UserService 实现 UserFacade 接口
```

清单 4-38 展示了实现 `UserFacade` 接口的 `UserService`。到目前为止，`UserService` 并没有什么特别之处。现在，为了通过重写 `createUser` 方法为 `UserService` Bean 添加额外功能，清单 4-39 展示了 `UserService` 类的 `UserServiceDecorator`。

```
@Decorator
@Priority(Interceptor.Priority.APPLICATION)
public abstract class UserServiceDecorator implements UserFacade {
@Inject
@Database(DB.MS_SQL)
private EntityManager entityManager;
@Inject
@Delegate
private UserService userService;
@Override
public UserResponse createUser(final CreateUserRequest createUserRequest) {
final User user = UserService.fromUserWrapper(createUserRequest);
if (createUserRequest.getUserRegion() == UserRegion.EU) {
entityManager.persist(user);
final UserResponse userResponse = getUser(user.getId());
userResponseEvent.fire(userResponse);
//userResponseEvent.fireAsync(userResponse);
return UserService.toUserResponse(user);
}
return userService.createUser(createUserRequest);
}
}
清单 4-39
展示了“装饰”UserService 并重写 createUser 方法的 UserServiceDecorator
```

清单 4-39 展示了 `UserServiceDecorator`。它使用了 `@Decorator` 注解。该注解指明此类是一个装饰器。它还使用了 `@Priority` 注解。与关于拦截器的讨论类似，`@Priority` 是激活和排序装饰器的新方式。该装饰器实现了 `UserFacade` 接口，并被声明为抽象类。之所以是抽象类，是因为该类并未实现接口中的所有方法。

这里有两个注入点——一个用于 `EntityManager`，另一个用于 `UserService`。`EntityManager` 注入点使用了 `@Database(MS_SQL)` 限定符。我们实现此装饰器的目的是为了能够重写 `UserFacade` 接口上的 `createUser` 方法。目标是将欧盟的用户保存到托管在欧盟的 MS SQL Server 数据库中。因此，我们注入 `EntityManager`，并使用必要的限定符对其进行限定，以便注入连接到 MS SQL Server 数据库的正确 `EntityManager` 实例。

`UserService` 注入点使用了 `@Delegate` 注解。此字段被称为装饰器的委托注入点。这个 Bean 就是 `UserServiceDecorator` 正在装饰的对象。对委托（此处为 `UserService`）的任何调用都将在装饰器上进行，装饰器可以直接在委托对象上调用方法。此字段告诉 CDI 容器 `UserServiceDecorator` 正在装饰哪个 Bean。你可以限定委托注入点，以限制要装饰的 Bean 的类型。

该装饰器实现了 `createUser` 方法。此方法是 `UserFacade` 接口上的两个方法之一。该实现检查要持久化的用户是否位于欧盟地区。如果是，则装饰器通过调用注入的 `EntityManager` 的 `persist` 方法来执行实际的持久化操作。请记住，此 `EntityManager` 实例连接到一个将托管在欧盟的 SQL Server。如果用户不在欧盟，则装饰器只需调用委托上的 `createUser` 方法。

通过这种方式，我们为 `UserService` Bean 添加了一个自定义功能。持久化用户的过程并非横切关注点，而是实际的业务功能。为了扩展该功能，我们使用装饰器模式来扩展 Bean 的功能，而无需更改其实现。通常情况下，你会发现自己使用装饰器来扩展不属于你应用程序的 Bean。通过使用生产者，你可以将这些类转换为 CDI Bean，然后使用装饰器来扩展它们的功能。

像 `UserServiceDecorator` 这样的装饰器是一个 CDI Bean，因此，它的类型是其继承链上直至 `java.lang.Object` 的所有类型。装饰器不能声明除 `@Dependent` 伪作用域之外的任何作用域。为装饰器指定任何其他作用域，容器在应用程序启动时都会将其视为定义错误。到目前为止，你已经了解了装饰器和拦截器如何分别帮助你实现横切关注点和向 Bean 添加功能。接下来，让我们看看 CDI 中的事件。

## CDI 事件

CDI 事件机制是一种允许应用程序组件发出任何类型的事件，并以类型安全、完全解耦的方式通知这些事件的一个或多个监听器的方法。事件的源与任何观察者或监听器之间不存在编译时依赖关系。观察者设计模式中的发布者-订阅者范式是 CDI 事件的核心，它包含两个部分——事件对象和事件观察者。



### 事件对象

事件对象是要触发的事件所携带的有效载荷。可以将有效载荷理解为将传递给事件监听器的数据。它可以是任何有效的 Java 类型。在 jwallet 中，我们想要向成功注册应用的用户发送一条短信。我们可以将此需求硬编码到 `UserService` bean 中。然而，这意味着我们可能会被绑定到发送短信流程的单一实现上。

一种更清晰的方式是，以应用中用户表示的类型来触发一个事件。这样，短信实现将与系统中持久化用户的流程完全解耦。我们可以随意添加或移除消息发送实现，而不会破坏代码。我们还可以添加其他消息选项，例如电子邮件，全部作为事件监听器或观察者，并且仍然与事件源完全解耦。

为此，我们需要一个事件对象。当事件被触发时，此对象将作为有效载荷传递给观察者。CDI 容器将根据观察者正在观察的有效载荷类型，将事件观察者映射到已触发的事件。清单 4-40 展示了 `UserServiceDecorator` 中带有有效载荷的 `Event` 接口。

```
@Decorator
@Priority(Interceptor.Priority.APPLICATION)
public abstract class UserServiceDecorator implements UserFacade {
@Inject
Event userResponseEvent;
}
清单 4-40
展示了在 UserServiceDecorator 中注入 Event 对象
```

清单 4-40 展示了 `UserServiceDecorator` 中有一个新的注入点——`Event<UserResponse>`。CDI 事件机制的入口点是 `Event<T>` 接口。该接口接受一个类型，该类型将成为事件的有效载荷。它拥有触发不同类型事件的方法。如清单 4-40 所示，你注入一个 `Event` 接口的实例，并传入一个有效载荷类型。此类型被称为事件类型，将用于解析哪些观察者对此事件感兴趣。CDI 容器将在运行时为你的应用程序提供 `Event` 接口的实现。清单 4-41 展示了注入的 `Event` 对象的使用。

```
@Decorator
@Priority(Interceptor.Priority.APPLICATION)
public abstract class UserServiceDecorator implements UserFacade {
@Inject
Event userResponseEvent;
@Inject
@Database(DB.MS_SQL)
private EntityManager entityManager;
@Inject
@Delegate
private UserService userService;
@Override
public UserResponse createUser(final CreateUserRequest createUserRequest) {
final User user = UserService.fromUserWrapper(createUserRequest);
if (createUserRequest.getUserRegion() == UserRegion.EU) {
entityManager.persist(user);
final UserResponse userResponse = userService.getUser(user.getId());
userResponseEvent.fire(userResponse);
return UserService.toUserResponse(userResponse);
}
return userService.createUser(createUserRequest);
}
}
清单 4-41
展示了触发 UserResponse 事件的实际过程
```

清单 4-41 展示了 `UserServiceDecorator` bean 中修改后的 `createUser` 方法。该方法在将 `User` 对象持久化到数据库后，调用注入的事件对象上的 `fire` 方法，并通过 `UserResponse` 对象传入新持久化的 `User`。这个传入的 `UserResponse` 对象将成为已触发事件的有效载荷。如你所见，触发事件就像调用 `fire` 方法并传入 `Event` 接口的类型一样简单。这个简单的调用将触发一个事件，任何组件都可以订阅或观察该事件。传递给 `Event` bean 的 `fire` 方法的 `UserResponse` 实例，将被传递给所有 `UserResponse` 类型的事件观察者。

### 事件观察者

CDI 事件机制的第二部分是事件观察者。观察者是特定事件的订阅者。容器根据观察者观察的类型，将观察者与事件配对。一个希望被通知 `UserResponse` 事件的观察者，必须观察一个 `User` 事件。清单 4-42 展示了 `MessageCenter` bean 中的 `User` 事件观察者。

```
public class MessageCenter {
public void sendUserSms(@Observes final UserResponse userResponse) {
//TODO --> Send SMS through gateway... Blocking task
}
}
清单 4-42
展示了作为 MessageCenter bean 的 UserResponse 事件观察者
```

清单 4-42 展示了 `MessageCenter` bean 中有一个 `sendUserSms` 方法。该方法接受一个 `UserResponse` 类型的参数。此参数决定了此观察者将被通知的事件类型。该参数使用了 `@Observes` 注解进行标注，告知 CDI 运行时此方法观察被注解类型的事件。当 `UserResponse` 事件在 `UserServiceDecorator` bean 中被触发时，容器将自动调用此方法，并传入在 `UserServiceDecorator` bean 中传递给 `Event` 接口的 `fire` 方法的 `UserResponse` 实例。尽管触发事件的 bean 和观察事件的 bean 之间在编译时绝对没有依赖关系，但在运行时，这些 bean 将通过触发的事件相互通信。



### 异步事件

到目前为止我们看到的 UserResponse 事件是一个同步阻塞事件。这意味着当它被触发时，观察者方法会阻塞执行，直到其完成。如代码清单 4-42 所示，执行将被阻塞，直到 sendUserSms 返回，因为事件观察者方法将在触发它的同一个线程中被调用。然而，发送短信很可能需要通过外部网络与短信提供商进行通信。这可能需要时间，具体取决于网络延迟。为了避免此类潜在问题，事件机制支持触发异步事件。

对于异步事件，观察者将被异步调用，在与触发事件不同的线程中执行。这样，短信的发送将在单独的线程中进行，因此执行会立即返回到事件触发点。代码清单 4-43 展示了在 UserServiceDecorator bean 中触发异步事件。

```
@Decorator
@Priority(Interceptor.Priority.APPLICATION)
public abstract class UserServiceDecorator implements UserFacade {
@Inject
Event userResponseEvent;
@Inject
@Database(DB.MS_SQL)
private EntityManager entityManager;
@Inject
@Delegate
private UserService userService;
@Override
public UserResponse createUser(final CreateUserRequest createUserRequest) {
final User user = UserService.fromUserWrapper(createUserRequest);
if (createUserRequest.getUserRegion() == UserRegion.EU) {
entityManager.persist(user);
final UserResponse userResponse = userService.getUser(user.getId());
userResponseEvent.fireAsync(userResponse);
return UserService.toUserResponse(user);
}
return userService.createUser(createUserRequest);
}
}
代码清单 4-43
展示触发一个异步事件
```

代码清单 4-43 展示了 UserServiceDecorator bean 及其 createUser 方法。这次，事件对象通过调用 fireAsync 方法触发了一个异步事件。此方法将触发一个异步事件，只有异步观察者会收到该事件的通知。它返回一个 `java.util.concurrent.CompletionStage` 对象，用于对异步操作进行可选的进一步管道组合。代码清单 4-42 中展示的 UserResponse 观察者将不会被此事件调用，因为该观察者是同步的。代码清单 4-44 展示了包含一个异步 UserResponse 观察者的 MessageCenter bean。

```
public class MessageCenter {
public void sendUserSmsAsync(@ObservesAsync final UserResponse userResponse) {
//TODO --> 通过网关发送短信... 异步
}
}
代码清单 4-44
展示一个异步 UserResponse 观察者
```

代码清单 4-44 展示了 MessageCenter bean 及其新方法 sendUserSmsAsync。此方法与我们在代码清单 4-42 中看到的同步方法具有几乎相同的签名，唯一的区别是 `@Observes` 注解被替换为 `@ObservesAsync`。此注解告诉容器，此方法观察一个类型为 UserResponse 的异步事件。因此，当代码清单 4-43 中展示的 UserResponse 事件被触发时，sendUserSmsAsync 方法将被调用。

### 事务性观察者

到目前为止讨论的 UserResponse 事件应该可以正常工作。但是，存在一个问题。需求是向成功持久化到数据库的用户发送消息。然而，我们尚未检查实际的用户持久化操作是否成功。代码清单 4-43 中触发的异步事件是在调用 EntityManager 实例的 persist 方法两行之后触发的。问题在于，直到事件被触发时，执行该方法的事务尚未提交。而在事务成功提交之前，我们无法确定用户持久化操作是否成功。

为了解决这个问题，我们可以使用事务性观察者。事务性观察者是指定应在事务的哪个阶段被调用的观察者。根据我们的需求，我们希望观察者仅在触发事件的事务成功完成后才被调用，这意味着事务已成功提交，并且我们确定数据已在数据库中。代码清单 4-45 展示了包含一个用于发送短信的事务性观察者的 MessageCenter bean。

```
public class MessageCenter {
public void sendUserSms(@Observes(during = TransactionPhase.AFTER_SUCCESS)   final UserResponse userResponse) {
//TODO --> 通过网关发送短信... 阻塞任务
}
}
代码清单 4-45
展示一个事务性观察者
```

代码清单 4-45 展示了 MessageCenter bean 的 sendUserSms 方法。这一次，`@Observes` 方法传递了一个参数 – `during = TransactionPhase.AFTER_SUCCESS`。这向容器发出信号，表明我们希望此观察者在触发事件的任何事务的成功后阶段被调用。因为 UserResponse 是在事务上下文中触发的，所以 sendUserSms 观察者只会在事务成功提交后才会被通知。

您应该已经注意到，sendUserSms 观察者正在观察一个同步事件，因为它使用了 `@Observes` 注解。这是因为事务性观察者不支持异步事件。因此，触发的事件将是一个同步事件，如 UserServiceDecorator 中的代码清单 4-41 所示。

观察者方法有多个可用的事务阶段。这些阶段定义在 `jakarta.enterprise.event` 包中的 `TransactionPhase` 枚举类型中。它们是：

*   **IN_PROGRESS** – 观察者在事件触发时被调用。这是默认值。
*   **BEFORE_COMPLETION** – 观察者在事务的完成前阶段被调用。
*   **AFTER_COMPLETION** – 观察者在事务的完成后阶段被调用。
*   **AFTER_FAILURE** – 观察者在事务的完成后阶段被调用，但仅在事务失败时。
*   **AFTER_SUCCESS** – 观察者在事务的完成后阶段被调用，但仅在事务成功时。



### 限定事件

可以对事件进行限定，以有选择地决定哪些观察者会收到通知。回到我们的需求，正如我们需要将 jwallet 的欧盟居民持久化到欧盟托管的数据库中一样，我们只能向成功持久化的欧盟用户发送电子邮件，而不是短信。为了满足这一需求，我们可以限定事件，根据其限定符有选择地调用不同的观察者方法。让我们首先引入一个 `MessageOption` 限定符，它接受一个 `MessageType` 参数，如代码清单 4-46 所示。

```
@Qualifier
@Retention(RUNTIME)
@Target({ FIELD, TYPE, METHOD, PARAMETER })
public @interface MessageOption {
MessageType value();
enum MessageType {
SMS,
EMAIL
}
}
代码清单 4-46
展示新的 MessageOption 参数
```

代码清单 4-46 展示了 `MessageOption` 限定符。有了它，我们可以有选择地决定哪些事件观察者会收到哪个事件的通知。这个注解有两种使用方式。首先，我们可以将两个 `Event` 对象注入到 `UserServiceDecorator` 中，每个对象使用单独的 `MessageOption` 进行限定，如代码清单 4-47 所示。

```
@Decorator
@Priority(Interceptor.Priority.APPLICATION)
public abstract class UserServiceDecorator implements UserFacade {
@Inject
@MessageOption(MessageOption.MessageType.SMS)
Event smsEvent;
@Inject
@MessageOption(MessageOption.MessageType.SMS)
Event emailEvent;
}
代码清单 4-47
展示为每种 MessageOption 类型限定的两个 Event 对象
```

代码清单 4-47 展示了 `UserServiceDecorator` Bean，它有两个 `Event` 注入点，每个都使用 `MessageOption` 进行了限定。对每个事件感兴趣的相应观察者也需要被限定。

```
public class MessageCenter {
public void sendUserSmsAsync(
@ObservesAsync @MessageOption(MessageOption.MessageType.SMS) final UserResponse userResponse) {
//TODO --> 通过网关发送短信... 异步
}
public void sendEmail(
@ObservesAsync @MessageOption(MessageOption.MessageType.EMAIL) final UserResponse userResponse) {
//TODO --> 异步发送电子邮件
}
}
代码清单 4-48
展示 MessageCenter Bean 中观察这些事件的两个观察者
```

代码清单 4-48 展示了 `MessageCenter` Bean，它有两个事件观察者，每个观察者观察相同的事件类型但具有不同的限定符。在观察者解析期间，容器将根据类型和限定符将每个观察者匹配到触发的事件。因此，`sendSmsAsync` 观察者将映射到从 `smsEvent` 对象（代码清单 4-47）触发的异步事件，而 `sendEmail` 观察者将映射到 `emailEvent` 对象（代码清单 4-47）。这应该能按预期工作。然而，一旦 `MessageOption` 限定符中有十几个 `MessageType` 选项，我们就需要注入更多的 `Event` 对象。

幸运的是，我们可以根据代码中的某些条件动态地向事件添加限定符。代码清单 4-49 展示了重构后的 `UserServiceDecorator` Bean，它根据持久化用户的位置动态选择 `MessageOption`。

```
@Decorator
@Priority(Interceptor.Priority.APPLICATION)
public abstract class UserServiceDecorator implements UserFacade {
@Inject
Event userResponseEvent;
@Inject
@Database(DB.MS_SQL)
private EntityManager entityManager;
@Inject
@Delegate
private UserService userService;
@Override
public UserResponse createUser(final CreateUserRequest createUserRequest) {
final User user = UserService.fromUserWrapper(createUserRequest);
if (createUserRequest.getUserRegion() == UserRegion.EU) {
entityManager.persist(user);
final UserResponse userResponse = userService.getUser(user.getId());
userResponseEvent.select(getAnnotation(UserRegion.EU)).fireAsync(userResponse);
return UserService.toUserResponse(user);
}else{
final UserResponse userResponse = userService.createUser(createUserRequest);
userResponseEvent.select(getAnnotation(UserRegion.OTHERS)).fireAsync(userResponse);
return userResponse;
}
}
private MessageOption getAnnotation(final UserRegion userRegion) {
if (userRegion == UserRegion.EU) {
return new MessageOption() {
@Override
public MessageType value() {
return MessageType.EMAIL;
}
@Override
public Class annotationType() {
return MessageOption.class;
}
};
}
return new MessageOption() {
@Override
public MessageType value() {
return MessageType.SMS;
}
@Override
public Class annotationType() {
return MessageOption.class;
}
};
}
}
代码清单 4-49
展示重构后的 UserServiceDecorator，其中包含动态选择的事件限定符
```

代码清单 4-49 展示了完整的 `UserServiceDecorator` Bean。在 `createUser` 方法中，如果用户位于欧盟，我们调用 `UserResponseEvent` 对象上的 `select` 方法，并将 `getAnnotation(UserRegions.EU)` 方法的返回类型传递给它。`getAnnotation` 方法接受一个 `UserRegion` 类型参数，并返回该区域所需的注解。在这种情况下，返回的限定符将是 `MessageOption(MessageType.EMAIL)`。我们调用的 `Event#select` 方法接受一个可变参数列表的注解。

如果用户不在欧盟区域，我们将 `UserRegion.OTHERS` 传递给 `getAnnotation` 方法，该方法将返回 `MessageOption(MessageType.SMS)`，然后将其传递给同一个 `userResponseEvent` 对象的 `select` 方法。这样，我们的观察者方法（如代码清单 4-48 所示）保持不变。但我们使触发事件的 Bean 中的限定符选择过程更加灵活。容器仍然会将任何触发的事件映射到正确的观察者方法，就像我们显式注入并限定了不同的事件类型一样，如代码清单 4-47 所示。

需要注意的是，代码清单 4-48 中所示的事件观察者将根据其类型和限定符是否与触发的事件匹配而被有条件地调用。然而，代码清单 4-50 展示了第三个 `UserResponse` 观察者，无论 `userResponseEvent` 的限定符如何，它都将始终被调用。

```
public class MessageCenter {
public void sendUserSms(
@Observes(during = TransactionPhase.AFTER_SUCCESS) @MessageOption(MessageOption.MessageType.SMS)
final UserResponse userResponse) {
//TODO --> 通过网关发送短信... 阻塞任务
}
public void sendUserSmsAsync(
@ObservesAsync @MessageOption(MessageOption.MessageType.SMS) final UserResponse userResponse) {
//TODO --> 通过网关发送短信... 异步
}
public void sendEmail(
@ObservesAsync @MessageOption(MessageOption.MessageType.EMAIL) final UserResponse userResponse) {
//TODO --> 异步发送电子邮件
}
public void sendMessage(@ObservesAsync final UserResponse userResponse) {
}
}
代码清单 4-50
展示 MessageCenter 中的一个“捕获所有”事件观察者
```

在代码清单 4-50 中，`sendMessage` 观察者将被任何 `Event<UserResponse>` 对象触发的所有事件调用，无论事件对象的限定符如何。原因是，在没有任何显式限定符的情况下，`sendMessage` 观察者隐式地使用了内置的 `@Any` 限定符。`@Any` 限定符是一个“捕获所有”的限定符，适用于任何 CDI 工件，无论该工件的限定符是什么。在此示例中，无论哪个事件对象触发 `UserResponse` 事件，`sendMessage` 观察者都会被调用。在使用 CDI 中的限定符时，需要牢记这一点。如果你需要将事物严格限制在你显式选择的限定符上，那么你应该确保使用你的限定符注解所有相应的构造，以避免微妙的错误。



### 事件元数据

有时你可能需要获取关于已触发事件的某些元数据。在这种情况下，你可以使用 `EventMetadata` Bean。观察者方法可以接受多个参数。到目前为止，我们看到的观察者方法都只接受事件负载形式的单个参数。然而，你可以向观察者方法传递多个参数，每个参数都是一个注入点，将由容器自动解析。让我们将一个 `EventMetadata` Bean 传递给清单 4-51 中 `MessageCenter` Bean 的 `sendUserSms` 观察者方法。

```
public class MessageCenter {
public void sendUserSms(@Observes(during = TransactionPhase.AFTER_SUCCESS) @MessageOption(MessageOption.MessageType.SMS) UserResponse userResponse, EventMetadata eventMetadata) {
//TODO --> 通过网关发送短信... 阻塞任务
}
}
清单 4-51
展示了向观察者方法传递第二个参数
```

清单 4-51 展示了将 `EventMetadata` Bean 作为第二个参数注入到 `sendUserSms` 同步观察者方法中。`EventMetadata` 是一个接口，其实现将由 CDI 运行时提供。它包含用于获取已触发事件的限定符、获取注入点的 `InjectionPoint` Bean 以及获取事件对象类的 `Type` 的方法。注入此元数据可以为你提供信息，以便在观察者方法中进一步用于动态决策。

### 条件观察者方法

CDI 事件观察者方法可以是条件性的。你可以决定一个观察者方法是否应在其所观察的事件被触发时始终被调用，或者仅当声明该观察者方法的 Bean 的实例已存在时才被调用。默认情况下，如果不存在包含观察者方法的 Bean 实例，则会创建一个新的实例。但是，你可以关闭此功能，仅当声明观察者方法的 Bean 的实例在当前作用域中已可用时才调用观察者。要条件性地使用观察者方法，你首先需要为声明观察者方法的 Bean 指定一个显式的作用域。包含我们消息观察者的 `MessageCenter` Bean（上次展示于清单 4-51）是 `@Dependent` 作用域的，因为我们没有显式地将其与某个作用域关联。

要条件性地要求仅在 `MessageCenter` Bean 的实例存在时才调用 `sendUserSms` 同步观察者，我们可以向 `@Observes` 注解传递第二个参数，如清单 4-52 所示。

```
@RequestScoped
public class MessageCenter {
public void sendUserSms(@Observes(during = TransactionPhase.AFTER_SUCCESS, notifyObserver = Reception.IF_EXISTS) @MessageOption(MessageOption.MessageType.SMS)
final UserResponse userResponse, final EventMetadata eventMetadata) {
//TODO --> 通过网关发送短信... 阻塞任务
}
}
清单 4-52
展示了将 sendUserSms 设置为条件性
```

清单 4-52 展示了 `MessageCenter` Bean，这次其作用域被设置为 `@RequestScope`。`sendUserSms` 观察者将 `notifyObserver` 参数值 `Reception.IF_EXISTS` 传递给了 `@Observes` 注解。通过这种构造，仅当当前请求作用域中存在 `MessageCenter` Bean 的实例时，此观察者才会被任何已触发的同步 `UserResponse` 事件调用。如果不存在该 Bean 的实例，则不会调用此观察者。

`notifyObserver` 接受两个选项之一：`Reception.IF_EXISTS` 或 `Reception.ALWAYS`。默认值是 `Reception.ALWAYS`。因此，默认情况下，观察者方法将始终为其所观察的事件被调用，除非显式关闭。

## 总结

在本章中，我们涵盖了 Jakarta 上下文和依赖注入规范的核心内容。我们了解了该规范的特性、如何激活它，然后快速浏览了它为应用程序开发提供的各种构造。Jakarta 规范页面上提供了更多内容^(⁹⁹)。在下一章中，我们将通过介绍 Jakarta 持久化 API 来继续讨论 Jakarta EE。

脚注 1   2   3   4   5   6   7   8   9   10   11   12   13   14   15

