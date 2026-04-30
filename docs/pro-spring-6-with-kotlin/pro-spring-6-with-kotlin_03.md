# 3. 在 Spring 中引入 IoC 和 DI

**第** **1** **章**和**第 2 章**向你介绍了 Spring 世界，解释了为什么这个框架是必要的，以及为什么依赖注入如此酷且有用。本质上，Spring 的构建就是为了让依赖注入变得简单。这种软件设计模式意味着依赖组件将依赖解析委托给一个外部服务，该服务负责注入依赖项。依赖组件不允许调用注入器服务，并且在涉及将要注入的依赖项时几乎没有发言权。这就是为什么这种行为也被称为“*别打给我们，我们会打给你！*”原则，它在技术上被称为*控制反转 (IoC)*。如果你快速搜索一下 Google，你会发现很多关于依赖注入和控制反转的相互矛盾的观点。你会找到一些编程文章，将它们称为*编程技术*、*编程原则*和*设计模式*。然而，最好的解释来自 Martin Fowler 的一篇文章^(²⁹)，该文章在 Java 世界中被公认为设计模式方面的最高权威。如果你没有时间阅读，这里有一个总结：*控制反转是促进依赖注入的框架的一个共同特征。依赖注入模式的基本思想是，有一个独立的对象，根据接口契约，将具有所需行为的依赖项注入进来。*

本章涵盖以下 DI 特性：

*   *控制反转概念*：在本节中，我们将讨论各种类型的 IoC，包括依赖注入和依赖查找。本节将介绍不同 IoC 方法之间的差异以及每种方法的优缺点。

*   *Spring 中的控制反转*：本节将介绍 Spring 中可用的 IoC 功能以及它们是如何实现的。特别是，你将看到 Spring 提供的依赖注入服务，包括 setter 注入、构造器注入和方法注入。

*   *Spring 中的依赖注入*：本节将介绍 Spring 对 IoC 容器的实现。对于 bean 定义和 DI 需求，`BeanFactory` 是应用程序与之交互的主要接口。然而，除了前几个代码清单外，本章提供的其余示例代码都侧重于使用 Spring 的 `ApplicationContext` 接口，它是 `BeanFactory` 的扩展，提供了更强大的功能。我们将在后面的章节中介绍 `BeanFactory` 和 `ApplicationContext` 之间的区别。

*   *配置 Spring 应用程序上下文*：本章的最后一部分重点介绍 `ApplicationContext` 配置的注解方法。Groovy 和 Java/Kotlin 配置将在**第** **4** **章**中进一步讨论。本节从讨论 DI 配置开始，然后继续介绍 `BeanFactory` 提供的其他服务，例如 bean 继承、生命周期管理和自动装配。

## 控制反转与依赖注入

其核心是，IoC 旨在提供一种更简单的机制来配置组件依赖项（通常称为对象的*协作者*）并在其整个生命周期中管理这些依赖项。需要某些依赖项的组件通常被称为*依赖对象*，或者在 IoC 的情况下，被称为*目标*。通常，IoC 可以分解为两种子类型：*依赖注入*和*依赖查找*。这些子类型进一步分解为 IoC 服务的具体实现。从这个定义中，你可以清楚地看到，当我们谈论 DI 时，我们总是在谈论 IoC，但当我们谈论 IoC 时，我们并不总是在谈论 DI（例如，依赖查找也是 IoC 的一种形式）。

### 控制反转的类型

你可能想知道为什么有两种类型的 IoC，以及为什么这些类型又进一步拆分为不同的实现。这个问题似乎没有明确的答案；当然，不同的类型提供了一定程度的灵活性，但对我们来说，IoC 更像是新旧思想的混合体。这两种类型的 IoC 代表了这一点。依赖查找是一种更传统的方法，乍一看，它对 Java 程序员来说似乎更熟悉。依赖注入虽然起初看起来违反直觉，但实际上比依赖查找更灵活、更实用。对于依赖查找风格的 IoC，组件必须获取对依赖项的引用，而对于依赖注入，依赖项由 IoC 容器注入到组件中。依赖查找有两种类型：

*   依赖拉取

*   上下文依赖查找 (CDL)

依赖注入也有两种常见形式：

*   构造器依赖注入

*   Setter 依赖注入

    ![](img/524962_1_En_3_Figa_HTML.jpg) 一个警告标志图标。它是一个三角形，中心有一个感叹号。  对于本节中的讨论，我们不关心虚构的 IoC 容器如何了解所有不同的依赖项，只关心它在某个时刻执行了针对每种机制所描述的操作。



#### 依赖拉取

对于 Java/Kotlin 开发者而言，依赖拉取是最熟悉的 IoC 类型。在依赖拉取中，依赖项会根据需要从注册表中拉取。任何编写过访问 EJB（2.1 或更早版本）代码的人都使用过依赖拉取（即通过 JNDI API 查找 EJB 组件）。图 3-1 展示了通过查找机制进行依赖拉取的场景。

![](img/524962_1_En_3_Fig1_HTML.jpg)

一个流程图。它展示了依赖对象通过 JNDI 查找机制，利用 JNDI 注册表进行拉取的过程。容器管理整个过程。

图 3-1

通过 JNDI 查找进行依赖拉取

Spring 也提供依赖拉取作为检索框架管理组件的机制；你在**第** **2** **章**中已经看到了它的实际应用。清单 3-1 展示了一个基于 Spring 的应用程序中典型的依赖拉取查找示例。

```
package com.apress.prospring6.two.annotated
import com.apress.prospring6.two.decoupled.MessageRenderer
import org.springframework.context.ApplicationContext
import org.springframework.context.annotation.AnnotationConfigApplicationContext
object HelloWorldSpringAnnotated {
@JvmStatic
fun main(args: Array) {
val ctx: ApplicationContext =
AnnotationConfigApplicationContext(HelloWorldConfiguration::class.java)
val mr: MessageRenderer = ctx.getBean("renderer", MessageRenderer::class.java)
mr.render()
}
}
清单 3-1
Spring 依赖拉取示例
```

这段代码片段及其引用的所有类，在第 2 章末尾介绍过，用于向你展示如何构建 Spring `ApplicationContext`。请注意，在 `main(..)` 方法中，`MessageRenderer` bean 是从充当应用程序中所有 bean 注册表的 `ApplicationContext` 中检索出来的；因此，这个实例是被*拉取*出来的，以便可以调用其 `render()` 方法。

这种 IoC 不仅在基于 JEE 的应用程序（使用 EJB 2.1 或更早版本）中普遍存在（这些应用程序广泛使用 JNDI 查找从注册表中获取依赖项），而且在许多环境中使用 Spring 时也至关重要。

#### 上下文依赖查找

*上下文依赖查找 (CDL)* 在某些方面与依赖拉取类似，但在 CDL 中，查找是针对管理资源的容器进行的，而不是针对某个中央注册表，并且通常是在某个设定点执行。图 3-2 展示了 CDL 机制。

![](img/524962_1_En_3_Fig2_HTML.jpg)

一个流程图。它展示了依赖对象通过查找机制，利用管理整个过程的容器进行拉取的过程。

图 3-2

上下文依赖查找

CDL 的工作原理是，需要依赖项的组件实现一个类似于清单 3-2 中所示的接口。

```
interface ManagedComponent {
fun performLookup(container: Container)
}
清单 3-2
上下文依赖查找接口
```

通过实现此接口，组件向 `container` 发出信号，表明它想要获取依赖项。该容器通常由底层应用服务器或框架（例如 Tomcat 或 JBoss）或框架（例如 Spring）提供。清单 3-3 中的代码展示了一个提供依赖查找服务的简单 `Container` 接口。

```
interface Container {
fun getDependency(key: String): Any?
}
清单 3-3
提供依赖查找服务的容器接口
```

当容器准备好将依赖项传递给组件时，它会依次在每个组件上调用 `performLookup()`。然后，组件可以使用 `Container` 接口查找其依赖项。为了与我们熟悉的示例配合使用，声明 `MessageRenderer` 接口扩展 `ManagedComponent` 接口，以便 `StandardOutMessageRenderer` 类可以使用提供的 `Container` 查找其自身的依赖项。`MessageRenderer` 接口和 `StandardOutMessageRenderer` 类的代码如清单 3-4 所示。

```
interface MessageRenderer : ManagedComponent {
fun render()
var messageProvider: MessageProvider?
}
class StandardOutMessageRenderer : MessageRenderer {
override var messageProvider:MessageProvider? = null
override
fun performLookup(container:Container) {
this.messageProvider =
container.getDependency("provider") as MessageProvider
}
// 其他代码已省略，已在第 2 章列出
}
清单 3-4
使用 StandardOutMessageRenderer 类的 ManagedComponent 实现
```

![](img/524962_1_En_3_Figb_HTML.jpg) 一个警告警示图标。它是一个中心带有感叹号的圆形。 你可能会注意到，本书中的许多 Kotlin 文件包含多个类型，因此 bean 定义的来源毫无疑问。我们在本书项目中尽可能保留了这种方法。

#### 构造器依赖注入

在*构造器依赖注入*中，IoC 容器通过组件的构造器（或多个构造器）注入其依赖项。组件声明一个构造器或一组构造器，将依赖项作为参数，IoC 容器在实例化时将依赖项传递给组件。这意味着此版本的 `StandardOutMessageRenderer` 看起来像清单 3-5 中描述的那样。

```
class StandardOutMessageRenderer(
override var messageProvider:MessageProvider? = null) :
MessageRenderer {
// 其他代码已省略，已在第 2 章列出
}
清单 3-5
为构造器注入修改的 StandardOutMessageRenderer
```

使用构造器注入的一个明显结果是，没有依赖项就无法创建对象；因此，依赖项是强制性的。

### Setter 依赖注入

在 *Setter 依赖注入*中，IoC 容器通过类似 JavaBean 的 setter 方法注入组件的依赖项。组件的 setter 暴露了 IoC 容器可以管理的依赖项。清单 3-6 展示了一个典型的基于 setter 依赖注入的 `StandardOutMessageRenderer` 版本。

```
package com.apress.prospring6.three.di;
class StandardOutMessageRenderer : MessageRenderer {
override var messageProvider:MessageProvider? = null
// Kotlin 会自动提供此方法，因此无需添加：
// fun setMessageProvider(messageProvider:MessageProvider) {
//    this.messageProvider = messageProvider
// }
// 其他代码已省略，已在第 2 章列出
}
清单 3-6
为 Setter 注入修改的 StandardOutMessageRenderer
```

使用 setter 注入的一个明显结果是，可以在没有依赖项的情况下创建对象，并且可以在稍后通过调用 setter 来提供依赖项。

在容器内部，`setMessageProvider()` 方法暴露的依赖需求通过 JavaBeans 风格的名称 *dependency* 来引用。在实践中，setter 注入是使用最广泛的注入机制，并且是实现起来最简单的 IoC 机制之一。

![](img/524962_1_En_3_Figc_HTML.jpg) 一个信息图标。它是一个中心带有字母 i 的圆形。 Spring 还支持另一种类型的注入，称为*字段注入*，但这将在本章后面介绍，届时你将学习如何使用 `@Autowire` 注解进行自动装配。



#### 注入 vs. 查找

选择哪种 IoC 风格——注入还是查找——通常不是一个困难的决定。在许多情况下，你选择的 IoC 类型是由所使用的容器决定的。例如，如果你使用 EJB 2.1 或更早版本，你必须使用查找风格的 IoC（通过 JNDI）从 JEE 容器中获取 EJB。而在 Spring 中，除了初始的 Bean 查找外，你的组件及其依赖项始终通过注入风格的 IoC 进行装配。

![](img/524962_1_En_3_Figd_HTML.jpg) 一个灯泡图标。它有一个圆润的灯泡形状，右上角有一个弧线，底部有一个类似线圈的结构。 当你使用 Spring 时，无需执行显式查找即可访问 EJB 资源。Spring 可以充当查找风格和注入风格 IoC 系统之间的适配器，从而允许你通过注入来管理所有资源。

读到这里，你怎么看？你会使用依赖注入还是依赖查找？

使用注入，你可以自由地使用类，使其与手动为依赖对象提供协作者的 IoC 容器完全解耦；而使用查找，你的类始终依赖于容器定义的类和接口。查找的另一个缺点是，在脱离容器的情况下测试类变得困难。使用注入，测试组件则轻而易举，因为你可以通过适当的构造函数或 setter 自行提供依赖项，正如你将在本书中看到的那样。

选择注入而非查找的最大原因是它能让你的生活更轻松。使用注入时，你编写的代码要少得多，而且你编写的代码简单，通常可以通过一个好的 IDE 自动完成。你会注意到，注入示例中的所有代码都是被动的，即它不会主动尝试完成任务。在注入代码中，最令人兴奋的事情是对象仅被存储在一个字段中；没有其他代码参与从任何注册表或容器中拉取依赖项。因此，代码更简单，更不容易出错。被动代码比主动代码更容易维护，因为几乎不会出错。清单 3-4 中的 `StandardOutMessageRenderer` 几乎不会出错：依赖键可能会改变，或者返回的依赖项可能是错误的类型。使用依赖查找可能会解耦应用程序的组件，但它增加了复杂性，因为需要额外的代码将这些组件重新耦合在一起以执行任何有用的任务。

### Setter 注入 vs. 构造函数注入

在本书的这个阶段，很明显依赖注入是正确的方式。*构造函数注入*在必须强制依赖项时非常有用，并且需要依赖项的组件在没有它的情况下无法工作。许多容器，包括 Spring，都提供了一种机制来确保在使用 setter 注入时定义所有依赖项，但通过使用构造函数注入，你可以以容器无关的方式断言对依赖项的需求。构造函数注入还有助于实现不可变对象的使用——只需对构造函数参数使用 `val` 而不是 `var`。

*Setter 注入*在各种情况下都很有用。如果组件向容器暴露其依赖项，但乐意提供自己的默认值，那么 setter 注入通常是实现这一目标的最佳方式。Setter 注入的另一个好处是它允许在接口上声明依赖项，尽管这并不像你最初想象的那么有用。想想看，一个接口可以由多个类实现，这些类需要暴露相同的 API，但需要不同的依赖项。因此，除非你绝对确定特定业务接口的所有实现都需要某个特定的依赖项，否则你应该让每个实现类定义自己的依赖项，并将业务接口保留给业务方法。清单 3-7 描述了一个用于通讯发送服务的接口，这看起来与之前所说的相矛盾。

```
package com.apress.prospring6.three
interface NewsletterSender {
var smtpServer: String?
var fromAddress: String?
fun send()
}
清单 3-7
NewsletterSender 接口
```

但关键在于：通过此接口声明的依赖项是配置项。配置参数是依赖项的一种特殊情况。当然，你的组件依赖于配置数据，但配置数据与你目前看到的依赖类型有显著不同。因此，Kotlin 在业务接口中以这种方式自动为配置参数提供 setter 和 getter 非常有帮助，并使 setter 注入成为一种有价值的工具。

通过电子邮件发送一组通讯的类实现了 `NewsletterSender` 接口。`send()` 方法是唯一的业务方法，但请注意我们在接口上定义了两个属性。当我们刚刚说过不应该在业务接口中定义依赖项时，为什么还要这样做呢？原因是这些值——SMTP 服务器地址和邮件发送地址——在实际意义上并不是依赖项；相反，它们是影响 `NewsletterSender` 接口所有实现功能的配置细节。那么问题来了：配置参数和其他类型的依赖项有什么区别？在大多数情况下，你可以清楚地看出一个依赖项是否应归类为配置参数，但如果你不确定，请查找以下三个指向配置参数的特征：

*   *配置参数是被动的*：在清单 3-7 所示的 `NewsletterSender` 示例中，SMTP 服务器参数是被动依赖项的一个例子。被动依赖项不直接用于执行操作；相反，它们在内部或被其他依赖项用来执行其操作。在第 2 章的 `MessageRenderer` 示例中，`MessageProvider` 依赖项不是被动的；它执行了 `MessageRenderer` 完成任务所必需的功能。



*   *配置参数通常是信息，而非其他组件*：这意味着配置参数通常是组件完成其工作所需的一些信息片段。显然，SMTP 服务器是 `NewsletterSender` 所需的信息，但 `MessageProvider` 实际上是 `MessageRenderer` 正常运行所需的另一个组件。

*   *配置参数通常是简单值或简单值的集合*：这实际上是前两点的副产品，但配置参数通常是简单值。在 Java/Kotlin 中，这意味着它们是（伪）基本类型（或对应的包装类）、`String` 或这些值的集合。简单值通常是被动的。这意味着除了操作 `String` 所代表的数据之外，你无法对它做太多事情；并且你几乎总是将这些值用于信息目的——例如，一个表示网络套接字应监听的端口号的 `Int` 值，或一个表示电子邮件程序应通过其发送消息的 SMTP 服务器的 `String`。

在考虑是否在业务接口中定义配置选项时，还要考虑该配置参数是适用于业务接口的所有实现，还是仅适用于一个实现。例如，在 `NewsletterSender` 的实现中，很明显所有实现都需要知道发送电子邮件时使用哪个 SMTP 服务器。但是，我们可能会选择将标记是否发送安全电子邮件的配置选项排除在业务接口之外，因为并非所有电子邮件 API 都支持此功能，并且可以合理地假设许多实现根本不会考虑安全性。

![](img/524962_1_En_3_Fige_HTML.jpg) 一个信息图标。它是一个中心带有字母 i 的圆形。 回想一下，在**第** **2** **章**中，我们选择在业务接口中定义依赖关系。这仅用于说明目的，不应以任何方式视为最佳实践。

Setter 注入还允许你在不创建父组件新实例的情况下，动态地将依赖项替换为不同的实现。Spring 的 JMX 支持使这成为可能。Setter 注入的最大好处可能是它是侵入性最小的注入机制。

通常，你应该根据用例选择注入类型。基于 Setter 的注入允许在不创建新对象的情况下交换依赖项，并且还允许你的类选择合适的默认值，而无需显式注入对象。当你希望确保依赖项被传递给组件，并且设计不可变对象时，构造函数注入是一个不错的选择。请记住，虽然构造函数注入确保所有依赖项都提供给组件，但大多数容器也提供了确保这一点的机制，但可能会产生将代码耦合到框架的成本。

## Spring 中的控制反转

如前所述，控制反转是 Spring 所做工作的重要组成部分。Spring 实现的核心基于依赖注入，尽管也提供了依赖查找功能。当 Spring 自动向依赖对象提供协作者时，它使用的是依赖注入。在基于 Spring 的应用程序中，最好使用依赖注入将协作者传递给依赖对象，而不是让依赖对象通过查找来获取协作者。图 3-3 展示了 Spring 的依赖注入机制。

![](img/524962_1_En_3_Fig3_HTML.jpg)

一个流程图。它展示了通过 Spring Bean 工厂容器将依赖项注入到依赖对象的过程。

图 3-3
Spring 的依赖注入机制

尽管依赖注入是连接协作者和依赖对象的首选机制，但你仍然需要依赖查找来访问依赖对象。在许多环境中，Spring 无法通过依赖注入自动连接所有应用程序组件，你必须使用依赖查找来访问初始组件集。例如，在独立的 Java/Kotlin 应用程序中，你需要在 `main(..)` 方法中引导 Spring 的容器，并通过 `ApplicationContext` 接口获取依赖项以进行编程处理。但是，当你使用 Spring 的 MVC 支持构建 Web 应用程序时，Spring 可以通过自动将整个应用程序粘合在一起来避免这种情况。只要有可能在 Spring 中使用依赖注入，就应该这样做；否则，你可以回退到依赖查找功能。在本章的过程中，你将看到这两种方法的实际示例，我们将在它们首次出现时指出。

Spring 的 IoC 容器的一个有趣特性是它能够充当其自身依赖注入容器与外部依赖查找容器之间的适配器。我们将在本章后面讨论这个特性。

Spring 支持构造函数注入和 Setter 注入，并通过大量有用的附加功能增强了标准 IoC 功能集，使你的工作更轻松。

本章的其余部分将介绍 Spring 的 DI 容器的基础知识，并附有大量示例。

## Spring 中的依赖注入

Spring 对依赖注入的支持是全面的，并且正如你将在**第** **4** **章**中看到的那样，它超出了我们迄今为止讨论的标准 IoC 功能集。本章的其余部分将介绍 Spring 依赖注入容器的基础知识，探讨 Setter 注入、构造函数注入和方法注入，并详细研究如何在 Spring 中配置依赖注入。



### Beans 与 BeanFactory

`org.springframework.beans` 和 `org.springframework.context` 包是 Spring 框架 IoC 容器的基础。Spring IoC 容器的核心是 `org.springframework.beans.factory.BeanFactory` 接口。Spring 对该接口的实现负责管理组件，包括它们的依赖关系以及生命周期。图 3-4 展示了最常用的 `BeanFactory` 实现。

![](img/524962_1_En_3_Fig4_HTML.jpg)

一个反向流程图。它展示了最常用的 BeanFactory 实现，包括 Auto-wire Capable、Listable 和 J N D I BeanFactory。

图 3-4

`BeanFactory` 最常用的实现

在 Spring 中，术语 `bean` 用于指代任何由 Spring IoC 容器管理的对象。Spring IoC 容器负责创建、配置（组装）并管理 bean 的整个生命周期。通常，你的 bean 在某种程度上遵循 JavaBeans 规范，但这并非必需，特别是当你计划使用构造器注入来组装 bean 时。

如果你的应用只需要 DI 支持，你可以通过 `BeanFactory` 接口与 Spring DI 容器交互。在这种情况下，你的应用必须创建一个实现了 `BeanFactory` 接口的类的实例，并使用 bean 和依赖信息对其进行配置。该接口由持有多个 bean 定义的对象实现，每个 bean 定义由一个唯一的 `String` 名称标识。创建此类实例后，你的应用可以通过 `BeanFactory` 访问这些 bean 并继续其处理流程。

在某些情况下，所有这些设置都是自动处理的（例如，在 Web 应用中，Spring 的 `ApplicationContext` 会在应用启动时，通过 web.xml 描述符文件中声明的 Spring 提供的 `ContextLoaderListener` 类，由 Web 容器引导启动）。但在许多情况下，你需要自己编写设置代码。本章中的大多数示例都需要手动设置 `BeanFactory` 的实现。

`ApplicationContext` 接口是 `BeanFactory` 的扩展。除了 DI 服务外，`ApplicationContext` 还提供其他服务：

*   与 Spring AOP 特性的集成
*   用于国际化（i18n）的消息资源处理
*   应用事件处理
*   应用层特定的上下文（例如，Web、安全等）

在开发基于 Spring 的应用时，建议你通过 `ApplicationContext` 接口与 Spring 交互。Spring 支持通过手动编码（手动实例化并加载相应配置）或在 Web 容器环境中通过 `ContextLoaderListener` 来引导 `ApplicationContext`。从现在开始，本书中的所有示例代码都将使用 `ApplicationContext` 及其实现。

### 配置 ApplicationContext

在**第** **2** **章**的第一个示例中，`org.springframework.context.ApplicationContext` 是使用 XML 文件配置的。虽然仍然可行，但使用 XML 进行配置仅限于 Spring 4 的功能，因为从该版本之后，该领域就没有再进行技术投入了。在 Spring 的演进过程中，关于哪种配置应用的方式更好（XML 还是注解）一直存在长期讨论。这实际上取决于开发者的偏好。注解与所配置 bean 的类型交织在一起，提供了大量上下文信息，使得配置更加简洁。XML 配置 Spring 应用时与实际代码解耦（Spring XML 配置文件是资源文件），尽管这对于 Java 配置也同样适用，这意味着配置可以外部化并在不重新编译代码的情况下进行修改。Spring 最大的优点之一就是你可以轻松地混合使用配置风格。

为了保持简单，Spring 应用仅通过 Java 注解和 Java 代码进行配置，这提供了足够的灵活性来描述任何类型的 Spring 应用。

### 基本配置概述

要配置一个独立的 Spring 应用，只需要一个带有 `@Configuration` 注解的类。该注解表明该类包含带有 `@Bean` 注解的方法，这些方法就是 bean 声明。这种方法适用于任何类型的对象，特别是由第三方库提供的类型——这些代码不属于你的项目，你无法编辑它们来声明你的 bean。同一个类也可以通过添加 `@ComponentScan` 注解来配置，以启用查找现有 bean 声明的功能。可发现的 bean 声明是带有 `@Component` 和其他构造型注解的类。Spring 容器处理这类类以生成 bean 定义，并在运行时为这些 bean 提供服务请求。所有这些注解基本上都用于描述应该创建哪些对象、创建顺序、初始化操作，甚至是在被垃圾回收器丢弃之前要执行的操作，这些通常简称为**配置元数据**。

这是配置 Spring 应用最紧凑的方式。然而，逐步展开这个主题更为合适。让我们从一个 Spring 配置类开始，如清单 3-8 所示，它声明了两个 bean。你在**第** **2** **章**中已经见过这个类，但现在我们要深入了解细节。

```
package com.apress.prospring6.two.annotated
// 其他导入已省略
import org.springframework.context.annotation.Bean
import org.springframework.context.annotation.Configuration
@Configuration
open class HelloWorldConfiguration {
@Bean // 等同于 <bean id="provider" class="..."/>
open fun provider(): MessageProvider {
return HelloWorldMessageProvider()
}
@Bean // 等同于 <bean id="renderer" class="..."/>
open fun renderer(): MessageRenderer {
val renderer: MessageRenderer = StandardOutMessageRenderer()
renderer.messageProvider = provider()
return renderer
}
}
清单 3-8
简单的 Spring 配置类
```

清单 3-8 声明了两个 bean，一个名为 `provider`，另一个名为 `renderer`；是的，bean 的名称与创建它们的方法名称相同。命名 bean 将在本章后面介绍。

Spring 配置类通常使用 `AnnotationConfigApplicationContext` 或其支持 Web 的变体 `AnnotationConfigWebApplicationContext` 进行引导。这两个类都实现了 `ApplicationContext`，引导清单 3-8 中配置的应用的代码可以如清单 3-9 所示编写。

```
package com.apress.prospring6.two.annotated
import org.springframework.context.ApplicationContext
import org.springframework.context.annotation.
AnnotationConfigApplicationContext
object HelloWorldSpringAnnotated {
@JvmStatic
fun main(args: Array<String>) {
val ctx: ApplicationContext = AnnotationConfigApplicationContext(
HelloWorldConfiguration::class.java)
val mr: MessageRenderer = ctx.getBean("renderer", MessageRenderer::class.java)
mr.render()
}
}
清单 3-9
引导 Spring 应用
```

当运行 `HelloWorldSpringAnnotated` 类时，会创建一个 Spring 应用上下文，其中包含由 `HelloWorldConfiguration` 类配置的 bean。通过调用 `getBean("{name}", {type}.class)` 方法获取对 `renderer` bean 的引用，并调用其 `render()` 方法。由于 Spring 根据配置注入了 `MessageProvider` 依赖，控制台会按预期打印出 “Hello World!” 消息。



引导过程包括实例化 `AnnotationConfigApplicationContext` 类，并将配置类的简单类名作为参数传入。通过实例化该类，我们创建了一个 Spring IoC 容器的实例，该容器将读取 Bean 声明、创建 Bean、将其添加到注册表中并进行管理。通过使用容器的引用，可以检索和使用 Bean，正如清单 3-9 所示。

### 声明 Spring 组件

声明 Bean 的另一种方法是直接使用*构造型*注解来标注类。这些注解之所以被称为*构造型*，是因为它们定义了类型或方法在整个架构中的角色。它们属于名为 `org.springframework.stereotype` 的包。该包将用于定义 Bean 的注解归为一组。这些注解与 Bean 的角色相关。例如，`@Service` 用于定义服务 Bean，这是一种更复杂的功能性 Bean，提供其他 Bean 可能需要的服务；`@Repository` 用于定义从数据库检索/保存数据的 Bean 等。而 `@Component` 是将类标记为 Bean 声明的注解。`@Component` 是一个元注解，本段中所有其他注解都使用它进行标注。这使得它们在使用基于注解的配置和类路径扫描时成为自动检测的候选对象。

要使用注解创建 Bean 定义，必须使用适当的构造型注解来标注 Bean 类，并且用于注入依赖项的 setter 或构造器必须使用 `@Autowired` 进行标注，以告知 Spring IoC 容器查找该类型的 Bean，并在调用该方法时将其用作参数。

在清单 3-10 中，构造型注解可以将生成的 Bean 名称作为参数。

```
// HelloWorldMessageProvider.kt
package com.apress.prospring6.three.constructor
import com.apress.prospring6.two.decoupled.MessageProvider
import org.springframework.stereotype.Component
// 无依赖的简单 Bean
@Component("provider")
class HelloWorldMessageProvider : MessageProvider {
// 部分代码已省略
}
// StandardOutMessageRenderer.kt
package com.apress.prospring6.three.setter;
import com.apress.prospring6.two.decoupled.MessageRenderer
import org.springframework.beans.factory.annotation.Autowired
// 需要依赖的简单 Bean
@Component("renderer")
class StandardOutMessageRenderer : MessageRenderer {
override var messageProvider:MessageProvider? = null
@Autowired set(value) { field = value }
// 部分代码已省略
}
清单 3-10
使用 @Component 声明 Spring Bean
```

通过使用 `@ComponentScan` 标注配置类，在引导 `ApplicationContext` 时，Spring 将查找这些类（也称为组件），并使用指定的名称实例化 Bean。在清单 3-11 中，你可以看到使用 `@ComponentScan` 标注的简单 `HelloWorldConfiguration` 配置类。

```
package com.apress.prospring6.three.constructor
import org.springframework.context.annotation.ComponentScan
@Configuration
@ComponentScan
open class HelloWorldConfiguration {
}
清单 3-11
带有组件扫描的简单 Spring 配置类
```

使用 `AnnotationConfigApplicationContext`（参见清单 3-9）引导 Spring 环境的代码也适用于此类，无需额外更改。

`@ComponentScan` 注解声明了一个扫描指令，并且可以定义要扫描的特定包。当未配置包时，扫描将从声明此注解的类所在的包开始，无论该类是公共的还是包私有的。

清单 3-11 中的类告诉 Spring 在 `constructor` 包及其子包中查找 Bean 定义。如果我们想要扩大或限制扫描范围，可以通过声明带有 `basePackages` 属性的 `@ComponentScan` 注解来实现，该属性允许声明一个或多个 Spring 将查找组件的包。

例如：

*   `@ComponentScan(basePackages = "com.apress.prospring6")` 告诉 Spring 在 `com.apress.prospring6` 包及其所有子包中查找组件类。

*   `@ComponentScan(basePackages = { "com.apress.prospring6.two", "com.apress.prospring6.three" })` 告诉 Spring 在 `com.apress.prospring6.two` 包和 `com.apress.prospring6.three` 包及其所有子包中查找组件类。

![](img/524962_1_En_3_Figf_HTML.jpg) 一个警告提示图标。它是一个中心带有感叹号的圆形。 包私有类上的 Bean 定义会被包含其所在包的 `@ComponentScan` 配置所捕获。

组件扫描是一项耗时的操作，良好的编程实践是尽量限制 Spring 在代码库中查找 Bean 定义的位置。除了 `basePackages` 之外，`@ComponentScan` 注解还提供了其他属性，以帮助精确定义扫描位置：

*   `basePackageClasses`：可以配置一个或多个类；将扫描每个指定类所在的包。

*   `includeFilters`：指定哪些类型符合组件扫描条件。

*   `excludeFilters`：指定哪些类型不符合组件扫描条件。

在实际的生产应用中，可能存在使用旧版 Spring 开发的遗留代码，或者需求的性质可能需要同时使用 XML 和配置类。幸运的是，XML 和 Java/Kotlin 配置可以通过多种方式混合使用。例如，配置类可以使用 `@ImportResource` 从 XML 文件（或多个文件）导入 Bean 定义，并且使用 `AnnotationConfigApplicationContext` 的相同引导方式在这种情况下也同样有效。来自 Java/Kotlin 配置类的其他 Bean 定义可以使用 `@Import` 导入。

因此，Spring 允许你在定义 Bean 时发挥真正的创造力；你将在**第** **4** **章**中了解更多相关信息，该章将专门讨论 Spring 应用程序配置。



### 使用 Setter 注入

在上一节中，我们使用 setter 注入配置了 `renderer` bean，但由于重点在于 Spring 配置类，因此需要补充一些额外细节。

要配置 setter 注入，必须在每个由 Spring 调用的 setter 方法上添加 `@Autowired` 注解，以便注入依赖项。清单 3-12 展示了为支持 setter 注入而设计的 `StandardOutMessageRenderer` 版本。

```
package com.apress.prospring6.three.setter
import org.springframework.beans.factory.annotation.Autowired
// 其他 import 语句已省略
@Component("renderer")
class StandardOutMessageRenderer : MessageRenderer {
override var messageProvider:MessageProvider? = null
@Autowired set(value) {
println(" ~~ 使用 setter 注入依赖 ~~")
field = value
}
// 其他代码已省略
}
清单 3-12
使用 @Component 和 Setter 注入声明 StandardOutMessageRenderer Bean
```

为了确保执行代码时通过 setter 方法注入依赖项，我们在 setter 方法中添加了 `println(" ~~ 使用 setter 注入依赖 ~~")` 语句。

Java/Kotlin 配置类无需更改；如果 `@ComponentScan` 配置正确，无论使用哪种注入风格，都能发现 bean 定义。

![](img/524962_1_En_3_Figg_HTML.jpg) 一个警告提示图标。它是一个中心带有感叹号的圆形。 除了 `@Autowired`，你还可以使用 `@Resource(name="provider")` 达到相同效果。`@Resource` 是 JSR-250（“Java 平台通用注解”）标准中的注解之一，该标准定义了一套在 JSE 和 JEE 平台上通用的 Java 注解。此注解目前属于 `jakarta.annotation-api` 库。与 `@Autowired` 不同，`@Resource` 注解支持 `name` 参数，可实现更细粒度的依赖注入需求。此外，Spring 还支持使用 JSR-299（“Java EE 平台上下文与依赖注入”，后移至 JSR-330“Java 依赖注入”）中引入的 `@Inject` 注解。`@Inject` 的行为与 Spring 的 `@Autowired` 注解等效，目前属于 `jakarta.inject-api` 库。

用于启动 Spring 应用上下文以测试通过 setter 注入配置 Spring bean 是否按预期工作的代码，与 `HelloWorldSpringAnnotated` 类的代码相同。

![](img/524962_1_En_3_Figh_HTML.jpg) 一个警告提示图标。它是一个中心带有感叹号的圆形。 在本书附带的项目中，大多数包含 `main(..)` 方法以启动 Spring 应用上下文的类名都以 `Demo` 结尾。本节的演示类名为 `SetterInjectionDemo`，所有 bean 定义和配置都位于 `SetterInjectionDemo.java` 文件中。这样做的目的是将所有实现放在一个文件中，便于在项目中查找其位置。

### 使用构造器注入

在上一节中，我们通过 setter 方法将一个 provider 实例注入到 `renderer` bean 中。这种方法运行良好，因为 `@Autowired` 注解默认强制注入依赖项，因此如果缺少依赖项，Spring 应用将无法启动。正如你将在本书后面看到的，在某些情况下，通过 setter 注入依赖项并非最佳选择，这是因为 Spring 创建 bean 的方式：它首先实例化构造器，然后调用 setter 方法注入依赖项。如果你希望确保 bean 在没有依赖项的情况下根本无法创建，可以在生命周期更早的阶段（即实例化步骤）强制执行此操作，方法是将依赖项声明为构造器的参数，从而将 bean 设计为支持*构造器注入*。

在我们目前的示例中，如果没有 provider，创建 `renderer` 是没有意义的，因此更好的设计是为 `StandardOutMessageRenderer` 类提供一个包含 `MessageProvider` 参数的构造器，如清单 3-13 所示。

```
package com.apress.prospring6.three.constructor
// import 语句已省略
@Component("renderer")
class StandardOutMessageRenderer @Autowired constructor(
override var messageProvider:MessageProvider?)
: MessageRenderer {
init {
println(" ~~ 使用构造器注入依赖 ~~")
}
// 其他代码已省略
}
清单 3-13
为构造器注入设计的 StandardOutMessageRenderer
```

通过这样实现 `MessageRenderer`，我们使得在不提供 `messageProvider` 值的情况下无法创建 `StandardOutMessageRenderer` 实例。`@Autowired` 注解用于修饰构造器，这告诉 Spring 在实例化此 bean 时（如果有多个构造器）应使用哪个构造器。

![](img/524962_1_En_3_Figi_HTML.jpg) 一个警告提示图标。它是一个中心带有感叹号的圆形。 在 Spring 4.x 中，决定如果 bean 声明了一个初始化所有依赖项的单一构造器，则 `@Autowired` 注解是多余的。因此，本着**约定优于配置**的精神，Spring IoC 被修改为无论注解是否存在，都会调用唯一的构造器来创建 bean。因此，即使移除 `@Autowired` 注解，清单 3-13 中声明的 `renderer` bean 仍然有效。

由于我们提到表示 bean 定义的类可以有多个构造器，因此我们在清单 3-14 中向您展示 `ConstructorConfusion` 类。

```
package com.apress.prospring6.three.constructor
import org.springframework.beans.factory.annotation.Autowired
import org.springframework.beans.factory.annotation.Value
import org.springframework.context.annotation.AnnotationConfigApplicationContext
import org.springframework.stereotype.Component
@Component
class ConstructorConfusion {
private var someValue: String
constructor(someValue: String) {
println("ConstructorConfusion(String) 被调用")
this.someValue = someValue
}
@Autowired // 这是使其工作的关键
constructor(@Value("90") someValue: Int) {
println("ConstructorConfusion(int) 被调用")
this.someValue = "数字: " + Integer.toString(someValue)
}
override fun toString(): String {
return someValue
}
companion object {
@JvmStatic
fun main(args: Array) {
val ctx = AnnotationConfigApplicationContext()
ctx.register(ConstructorConfusion::class.java)
ctx.refresh()
val cc = ctx.getBean(ConstructorConfusion::class.java)
println("这能工作吗？ $cc")
}
}
}
清单 3-14
具有多个构造器的 ConstructorConfusion 类
```



清单 3-14 中的 `ConstructorConfusion` bean 定义是正确的，因为第二个构造函数使用了 `@Autowired` 注解。这告诉 Spring 使用此构造函数来实例化该 bean。如果没有该注解，Spring 无法自行决定使用哪个构造函数，运行此类将抛出以下异常：

```
Caused by: org.springframework.beans.BeanInstantiationException:
Failed to instantiate [com.apress.prospring6.three.constructor.ConstructorConfusion]:
No default constructor found;
nested exception is java.lang.NoSuchMethodException: com.apress.prospring6.three.constructor.ConstructorConfusion.()
```

![](img/524962_1_En_3_Figj_HTML.jpg) 关键词工具图标。顶部有火焰，底部有一条线。 请注意带有 `@Autowired` 注解的构造函数中的 `@Value` 注解。此注解将在本章后面解释，但就目前这个简单示例而言，需要知道它是为构造函数参数注入值所必需的，没有它构造函数将无法工作。

![](img/524962_1_En_3_Figk_HTML.jpg) 信息图标。这是一个中心带有字母 i 的圆形。 目前，你可以忽略 `ConstructorConfusion` 类拥有自己的 `main(..)` 方法，该方法用于执行以下操作：

*   实例化一个类型为 `AnnotationConfigApplicationContext` 的简单、空的 Spring 应用上下文

*   然后通过调用 `ctx.register(ConstructorConfusion.class)`，用 `ConstructorConfusion` 类表示的 bean 定义填充该上下文

*   接着通过调用 `refresh()` 刷新上下文，该方法会根据注册的 bean 定义重新创建所有 bean。

或者你已经阅读了所有这些内容，并发现无需配置类即可通过编程方式注册 bean 定义这一点非常有趣。

![](img/524962_1_En_3_Figl_HTML.jpg) 警报警告标志图标。这是一个中心带有感叹号的圆形。 清单 3-14 中的示例还强调，`@Autowired` 注解只能应用于类中的一个构造函数。如果我们将该注解应用于多个构造函数方法，Spring 在引导 `ApplicationContext` 时会报错。

另一个值得在此介绍的场景是，如果依赖项不是一个 bean 会发生什么。如果它是一个简单对象，例如一个 `String` 呢？让我们通过创建一个可配置的消息提供程序来解决这个问题。一个允许在外部定义消息的可配置 `MessageProvider` 如清单 3-15 所示。

```
package com.apress.prospring6.three.configurable
import org.springframework.beans.factory.annotation.Value
// import statements omitted
@Component("provider")
internal class ConfigurableMessageProvider(
@Value("Configurable message") override var message: String) :
MessageProvider {
init {
println("~~ Injecting '$message' value into constructor ~~")
this.message = message
}
}
Listing 3-15
Configurable MessageProvider Implementation
```

通过像这样实现 `MessageProvider`，我们使得在不提供消息值的情况下创建 `ConfigurableMessageProvider` 的实例变得不可能。请注意用于定义要注入到构造函数中的值的 `@Value` 注解。这就是我们将非 bean 的值注入 Spring bean 的方式。遗憾的是，在此示例中，该值是在注解声明中指定的，因此存在必要的硬编码，但使用 SpEL 动态值注入可以从其他来源（如属性文件）实现（更多内容请参见本章后面部分）。

### 使用字段注入

Spring 支持的第三种依赖注入类型称为*字段注入*。顾名思义，依赖项直接注入到字段中，无需构造函数或 setter。这是通过使用 `@Autowired` 注解标注类成员来实现的。这看起来可能很实用，因为当依赖项在所属对象外部不需要时，它可以减轻开发人员编写一些在 bean 初始创建后不再使用的代码的负担。在清单 3-16 中，类型为 `NonSingletonDemo` 的 bean 有一个类型为 `Inspiration` 的字段。

```
package com.apress.prospring6.three.field
import org.springframework.stereotype.Component
// import statements omitted
@Component("singer")
internal class Singer {
@Autowired
private val inspirationBean: Inspiration? = null
fun sing() {
println("... " + inspirationBean!!.lyric)
}
}
Listing 3-16
NonSingletonDemo Class Used to Show Field Injection
```

该字段是私有的，但 Spring IoC 容器并不关心这一点；它使用反射来填充所需的依赖项。

`Inspiration` 类的代码如清单 3-17 所示，同时还有用于引导 Spring 应用上下文的类；它是一个带有 `String` 字段的简单 bean。

```
package com.apress.prospring6.three.field;
// import statements omitted
@Component
internal class Inspiration(
@Value("For all my running, I can understand") lyric: String
) {
var lyric = "I can keep the door cracked open, to let light through"
init {
this.lyric = lyric
}
}
// demo class
object SingerFieldInjectionDemo {
@JvmStatic
fun main(args: Array) {
val ctx = AnnotationConfigApplicationContext()
ctx.register(Singer::class.java, Inspiration::class.java)
ctx.refresh()
val singerBean = ctx.getBean(Singer::class.java)
singerBean.sing()
}
}
Listing 3-17
Inspiration Class and Demo Class Used to Show How Field Injection Works
```

Spring IoC 容器找到一个类型为 `Inspiration` 的 bean 后，会将该 bean 注入到 `singer` bean 的 `inspirationBean` 字段中。这就是为什么运行主类时会在控制台打印 *For all my running, I can understand*。

然而，这有一些缺点，这就是为什么**不推荐使用字段注入。** 以下是缺点列表：

*   *存在违反单一职责原则的风险*：拥有更多依赖意味着类承担更多职责，这可能导致在重构时难以分离关注点。当使用构造函数或 setter 设置依赖时，类变得臃肿的情况更容易被发现，但在使用字段注入时则隐藏得很好。

*   *依赖隐藏*：在 Spring 中，注入依赖的责任被传递给容器，但类应通过公共接口（通过方法或构造函数）清晰地传达所需的依赖类型。使用字段注入，可能会变得不清楚真正需要哪种类型的依赖，以及该依赖是否是强制性的。*（这与某些伴侣不沟通他们的需求，却期望你神奇地读懂他们的心思并满足他们非常相似。）*

*   *对 Spring IoC 的依赖*：字段注入引入了对 Spring 容器的依赖，因为 `@Autowired` 注解是一个 Spring 组件；因此，该 bean 不再是 POJO，并且无法独立实例化。（除非你使用 `@Resource` 或 `@Inject` 以及不同的容器。）

*   *字段注入不能用于 final 字段*：这种类型的字段只能使用构造函数注入进行初始化。

*   *编写测试困难*：字段注入给编写测试带来了困难，因为依赖项必须手动注入。

然而，在实例变量上使用 `@Autowired` 仅*对于* `@Configuration` 和 `@Test` 类是实用的——对于后者，主要是在涉及 Spring 的集成测试中。



## 使用注入参数

在前面的示例中，我们简要提到可以通过设值注入和构造器注入将其他组件和值注入到 bean 中。Spring 支持多种注入参数选项，不仅允许你注入其他组件和简单值，还可以注入 Java/Kotlin 集合、外部定义的属性，甚至来自其他工厂的 bean。让我们进一步深入探讨。

### 注入简单值

将简单值注入到 bean 中非常容易。Spring 支持多种注入参数选项，不仅允许你注入其他组件和简单值，还可以注入集合、外部定义的属性，甚至来自其他工厂的 bean。为此，只需在 `@Value` 注解中指定值即可。默认情况下，`@Value` 注解不仅能读取 `String` 值，还能将这些值转换为任何基本类型或基本类型的包装类。代码清单 3-18 中的代码片段展示了一个简单的 bean，它暴露了多种属性用于注入。

```
package com.apress.prospring6.three.valinject
import org.springframework.beans.factory.annotation.Value
import org.springframework.context.annotation.AnnotationConfigApplicationContext
import org.springframework.stereotype.Component
@Component("injectSimple")
class InjectSimpleDemo {
@Value("John Mayer")
private val name: String? = null
@Value("40")
private val age = 0
@Value("1.92")
private val height = 0f
@Value("false")
private val developer = false
@Value("1241401112")
private val ageInSeconds: Long? = null
override fun toString(): String {
return """
Name: $name
Age: $age
Age in Seconds: $ageInSeconds
Height: $height
Is Developer?: $developer
""".trimIndent()
}
companion object {
@JvmStatic
fun main(args: Array) {
val ctx = AnnotationConfigApplicationContext()
ctx.register(InjectSimpleDemo::class.java)
ctx.refresh()
val simple = ctx.getBean("injectSimple") as InjectSimpleDemo
println(simple)
}
}
}
代码清单 3-18
用于演示向各种类型的属性注入值的 InjectSimpleDemo 类
```

`@Value` 注解也可以直接用于字段，在 `InjectSimpleDemo` 类中就是这样使用的，以保持简单并避免编写繁琐的 setter 代码。如果你运行这个类，控制台输出将如预期所示，如代码清单 3-19 所示。

```
Name: John Mayer
Age: 39
Age in Seconds: 1241401112
Height: 1.92
Is Programmer?: false
代码清单 3-19
运行代码清单 3-18 中的类时得到的控制台输出
```

### 使用 SpEL 注入值

代码清单 3-18 中的示例展示了 Spring 在注入属性值时具备的自动转换能力。然而，该示例仍然相当基础，因为值是在 `@Value` 注解中硬编码的。这正是 SpEL 发挥作用的地方。

Spring 3 引入的一个强大特性是 **Spring 表达式语言 (SpEL)**。SpEL 使你能够动态地计算表达式，然后在 Spring 的 `ApplicationContext` 中使用它。你可以将计算结果注入到 Spring bean 中。在本节中，我们将通过上一节的示例，了解如何使用 SpEL 从其他 bean 注入属性。

假设现在我们希望将待注入的值外部化到一个配置类中，如代码清单 3-20 所示。

```
package com.apress.prospring6.three.valinject
// 导入语句已省略
@Component("injectSimpleConfig")
internal class InjectSimpleConfig {
val name = "John Mayer"
val age = 40
val height = 1.92f
val isDeveloper = false
val ageInSeconds = 1241401112L
}
代码清单 3-20
提供一些字段值的 Spring 配置类
```

首先要做的是编辑 `@Value` 注解，将硬编码的值替换为引用此 bean 属性的 SpEL 表达式。其次，需要将这种类型的 bean 添加到配置中。这两点都在代码清单 3-21 中展示。

```
package com.apress.prospring6.three.valinject
// 导入语句已省略
@Component("injectSimpleSpEL")
class InjectSimpleSpELDemo {
@Value("#{injectSimpleConfig.name.toUpperCase()}")
private val name: String? = null
@Value("#{injectSimpleConfig.age + 1}")
private val age = 0
@Value("#{injectSimpleConfig.height}")
private val height = 0f
@Value("#{injectSimpleConfig.developer}")
private val developer = false
@Value("#{injectSimpleConfig.ageInSeconds}")
private val ageInSeconds: Long? = null
override fun toString(): String {
return """
Name: $name
Age: $age
Age in Seconds: $ageInSeconds
Height: $height
Is Developer?: $developer
""".trimIndent()
}
companion object {
@JvmStatic
fun main(args: Array) {
val ctx = AnnotationConfigApplicationContext()
ctx.register(InjectSimpleConfig::class.java, InjectSimpleSpELDemo::class.java)
ctx.refresh()
val simple = ctx.getBean("injectSimpleSpEL") as InjectSimpleSpELDemo
println(simple)
}
}
}
代码清单 3-21
自定义为使用 SpEL 表达式的 @Value 注解
```

请注意，我们使用 SpEL `#{injectSimpleConfig.name}` 来引用另一个 bean 的属性。还要注意，这里没有调用 getter 方法，但 SpEL 表达式包含了由“.”（点号）连接的 bean 和属性名称，Spring 知道该怎么做。SpEL 还支持字符串操作和算术运算，如注入前对 `name` 属性调用 `toUpperCase()` 方法，以及在注入前对 bean 属性值加 1 所示。如果你运行 `InjectSimpleSpELDemo` 类的 `main(..)` 方法，控制台将打印出如代码清单 3-22 所示的输出。

```
Name: JOHN MAYER
Age: 41
Age in Seconds: 1241401112
Height: 1.92
Is Developer?: false
代码清单 3-22
运行代码清单 3-21 中的类时得到的控制台输出
```

由于对 `name` 属性添加了 `toUpperCase()` 调用，输出结果与值硬编码的示例几乎相同。通过使用 SpEL，你可以访问任何由 Spring 管理的 bean 和属性，并借助 Spring 对复杂语言特性和语法的支持，对它们进行操作以供应用程序使用。



### 注入与`ApplicationContext`嵌套

到目前为止，我们注入的 Bean 与被注入的 Bean 都位于同一个`ApplicationContext`（因此也位于同一个`BeanFactory`）中。然而，Spring 支持`ApplicationContext`的层级结构，使得一个上下文（及其关联的`BeanFactory`）被视为另一个上下文的父级。通过允许`ApplicationContext`实例嵌套，Spring 让你能够将配置拆分到不同的文件中，这对于拥有大量 Bean 的大型项目来说简直是天赐之物。

当嵌套`ApplicationContext`实例时，Spring 允许子上下文中的 Bean 引用父上下文中的 Bean。在 XML 中这很容易实现，因为`<ref/>`标签可以通过其`parent`属性配置为引用父上下文中的 Bean。使用 Java/Kotlin 配置和注解时，任务会稍微繁琐一些，但仍然可行。接下来，我们将为你展示其中的奥妙。

使用`AnnotationConfigApplicationContext`进行`ApplicationContext`嵌套很容易理解。要将一个`AnnotationConfigApplicationContext`嵌套到另一个中，只需在子`ApplicationContext`中调用`setParent()`方法，如代码清单 3-23 所示。

```
package com.apress.prospring6.three.nesting
// 导入语句已省略
object ContextNestingDemo {
@JvmStatic
fun main(args: Array) {
val parentCtx = AnnotationConfigApplicationContext()
parentCtx.register(ParentConfig::class.java)
parentCtx.refresh()
val childCtx = AnnotationConfigApplicationContext()
childCtx.register(ChildConfig::class.java)
childCtx.parent = parentCtx
childCtx.refresh()
val song1: Song = childCtx.getBean("song1") as Song
val song2: Song = childCtx.getBean("song2") as Song
val song3: Song = childCtx.getBean("song3") as Song
println("来自父上下文: " + song1.title)
println("来自父上下文: " + song2.title)
println("来自子上下文: " + song3.title)
}
}
代码清单 3-23
嵌套应用上下文
```

此方法继承自`org.springframework.context.support.GenericApplicationContext`，该类是`AnnotationConfigApplicationContext`的父类。然而，该方法在层级结构中声明的位置更高，位于`org.springframework.context.ConfigurableApplicationContext`接口中，该接口除了提供`ApplicationContext`接口中的应用程序上下文客户端方法外，还提供了配置应用程序上下文的功能。`AnnotationConfigApplicationContext`所属的完整层级结构如图 3-5 所示。

![](img/524962_1_En_3_Fig5_HTML.jpg)

一张截图。标题为“层级结构：ApplicationContext 的子类型”。它包含一些图标以及可配置、抽象、泛型和注解配置的应用上下文的详细信息。

图 3-5

`AnnotationConfigApplicationContext`层级结构

上下文嵌套很容易设置，但从父上下文中访问 Bean……就没那么简单了。

`Song`类是一个非常简单的 POJO，只有一个名为`title`的字段。为了根据上下文声明歌曲标题，使用了一个名为`TitleProvider`的类。这个类可以通过伴生对象构建器方法以各种标题实例化。这个类也非常简单。这两个类如代码清单 3-24 所示。

```
package com.apress.prospring6.three.nesting
// Song.kt
class Song(val title: String?)
// TitleProvider.kt
class TitleProvider {
var title:String? = "Gravity"
companion object {
fun instance(title: String?): TitleProvider {
val childProvider = TitleProvider()
if (title != null && title.isNotBlank())
childProvider.title = title
return childProvider
}
}
}
代码清单 3-24
Song 和 TitleProvider 类
```

`ParentConfig`类很简单，声明了两个名为`parentProvider`和`childProvider`的`TitleProvider` Bean，如代码清单 3-25 所示。

```
package com.apress.prospring6.three.nesting
// 导入语句已省略
@Configuration
open class ParentConfig {
@Bean
open fun parentProvider(): TitleProvider {
return TitleProvider.instance(null)
}
@Bean
open fun childProvider(): TitleProvider {
return TitleProvider.instance("Daughters")
}
}
代码清单 3-25
声明两个 TitleProvider Bean 的 ParentConfig 类
```

`ChildConfig`类声明了三个`Song` Bean，每个 Bean 的标题都从`TitleProvider` Bean 注入：

*   `song1` 注入的是名为`parentProvider`的 Bean 提供的标题值；由于父上下文中存在一个名为`parentProvider`的 Bean（子上下文继承自父上下文），因此注入的值为 *Gravity*。

*   `song2` 注入的是父上下文中声明的名为`childProvider`的 Bean 提供的标题值；由于子上下文中也存在一个名为`childProvider`的 Bean，要访问父上下文中的那个 Bean，需要一些*编码技巧*（使用 XML 配置会简单得多）：

    *   要访问父上下文中的 Bean，需要获取当前上下文的引用。这通过实现`ApplicationContextAware`接口并声明一个`ApplicationContext`类型的属性来实现，Spring 会通过调用`setApplicationContext(..)`方法用当前应用上下文的引用来初始化该属性。

    *   一旦有了当前上下文的引用，我们编写一个复杂的 SpEL 表达式，旨在获取父上下文的引用，访问`childProvider`，并获取`title`值，该值预期为`Daughters`。

*   `song3` 注入的是名为`childProvider`的 Bean 提供的标题值；由于当前上下文中存在一个名为`childProvider`的 Bean，因此注入的值为 *No Such Thing*。

代码如代码清单 3-26 所示。

```
package com.apress.prospring6.three.nesting
import org.springframework.beans.BeansException
import org.springframework.context.ApplicationContextAware
// 导入语句已省略
@Configuration
open class ChildConfig : ApplicationContextAware {
var applicationContext1: ApplicationContext? = null
@Bean // 覆盖父上下文中的{@code childProvider} Bean
open fun childProvider(): TitleProvider {
return TitleProvider.instance("No Such Thing")
}
@Bean
open fun song1(@Value("#{parentProvider.title}") title: String?): Song {
return Song(title)
}
@Bean
open fun song2(
@Value("#{childConfig.applicationContext1.parent.getBean(\"childProvider\").title}")
title:String?): Song {
return Song(title)
}
@Bean
open fun song3(@Value("#{childProvider.title}") title: String?): Song {
return Song(title)
}
@Throws(BeansException::class)
override fun setApplicationContext(applicationContext: ApplicationContext) {
this.applicationContext1 = applicationContext
}
}
代码清单 3-26
在子上下文的 Bean 中注入父上下文 Bean 的属性：输出
```

Bean `song2`的`@Value`注解中的 SpEL 表达式看起来复杂，但实际上并非如此。还记得我们到目前为止在每个演示类中是如何访问 Bean 的吗？这个表达式所做的正是同样的事情，只不过它不是从当前上下文访问 Bean，而是从其父上下文中访问。让我们解释一下：

*   表达式需要以`childConfig`开头，因为这是配置 Bean 的名称。之前提到过，SpEL 可以访问 Bean 的属性。当前应用上下文由 Bean `childConfig`的`applicationContext`属性引用。

*   `parent` 是`ApplicationContext`的一个属性，它引用父上下文。如果没有父上下文，则为`null`，但在此示例中，我们知道存在一个父上下文。

*   `getBean("childProvider")` 是我们之前用来通过名称获取 Bean 引用的典型方法。在 Kotlin 代码中，需要转换为适当的类型，而 SpEL 会自动处理。



清单 3-27 是运行 `ContextNestingDemo` 类后的输出结果。

```
from parent ctx: Gravity
from parent ctx: No Such Thing
from child ctx: Daughters
Listing 3-27
Injecting Beans Properties from a Parent Context in Beans in a Child Context
```

正如预期，`song1` 和 `song2` 这两个 bean 都从父级 `ApplicationContext` 中的 bean 获取了标题值，而 `song3` bean 则从子级 `ApplicationContext` 中的 bean 获取了标题值。

### 注入集合

通常，你的 bean 需要访问对象集合，而不仅仅是单个 bean 或值。因此，Spring 允许你将对象集合注入到某个 bean 中，这并不令人意外。在本书的上一版中，列表、集合、映射和属性值都是使用 XML 配置的。由于本书不侧重于 XML，让我们看看如何使用 Java/Kotlin 配置来声明集合。实际上，这非常简单：只需在配置类中声明带有 `@Bean` 注解的方法，这些方法返回 `List<E>`、`Set<E>`、`Properties` 或 `Map<K,V>` 类型即可。本节仅涵盖 `List<E>` 类型，但本书的源码包含了所有类型的示例。

对于下一个示例，我们将使用上一节中使用的 `Song` 类。`CollectionConfig` 类型声明了一个 `List<Song>` bean，如清单 3-28 所示。

```
package com.apress.prospring6.three.collectioninject
// import statements omitted
@Configuration
internal open class CollectionConfig {
@Bean
open fun list(): List {
return listOf(
Song("Not the end"),
Song("Rise Up")
)
}
@Bean
open fun set(): Set {
return setOf(
Song("Ordinary Day"),
Song("Birds Fly")
)
}
@Bean
open fun map(): Map {
return mapOf(
"John Mayer" to Song("Gravity"),
"Ben Barnes" to Song("11:11")
)
}
@Bean
open fun props(): Properties {
val props = Properties()
props["said.she"] = "Never Mine"
props["said.he"] = "Cold and jaded"
return props
}
@Bean
open fun song1(): Song {
return Song("Here's to hoping")
}
@Bean
open fun song2(): Song {
return Song("Wishing the best for you")
}
}
Listing 3-28
Configuration Class Declaring a Bean of Type List
```

`CollectionConfig` 类还声明了两个类型为 `Song` 的 bean。这些 bean 的用途将在本节稍后变得清晰。

`CollectingBean` 是用于注入列表 bean 的类型，其值通过调用 `printCollections()` 方法打印。`CollectionInjectionDemo` 类中声明了 `main(..)` 方法，包含创建应用上下文、获取 `CollectingBean` bean 的引用，并调用 `printCollections()` 方法以检查列表值是否被注入的代码。清单 3-29 展示了这两个类。

```
package com.apress.prospring6.three.collectioninject
// import statements omitted
object CollectionInjectionDemo {
@JvmStatic
fun main(args: Array) {
val ctx = AnnotationConfigApplicationContext()
ctx.register(CollectionConfig::class.java, CollectingBean::class.java)
ctx.refresh()
val collectingBean = ctx.getBean(CollectingBean::class.java)
collectingBean.printCollections()
}
}
@Component
internal class CollectingBean {
@Autowired
@Qualifier("list")
var songListResource:List? = null
@Autowired
var songList:List? = null
@Autowired
var songSet: Set? = null
@Autowired
@Qualifier("set")
var songSetResource:Set? = null
@Autowired
var songMap: Map? = null
@Autowired
@Qualifier("map")
var songMapResource:Map? = null
@Autowired
var props: Properties? = null
fun printCollections() {
println("-- list injected using @Autowired -- ")
songList!!.forEach(Consumer { s: Song -> println(s.title) })
println("""-- list injected using @Resource / @Autowired @Qualifier(\"list\") /
@Inject @Named(\"list\") -- """)
songListResource!!.forEach(Consumer { s: Song
-> println(s.title) })
println("-- set injected using @Autowired -- -- ")
songSet!!.forEach(Consumer { s: Song -> println(s.title) })
println("""-- set injected using @Resource / @Autowired @Qualifier(\"set\") /
@Inject @Named(\"set\") -- """)
songSetResource!!.forEach(Consumer { s: Song ->
println(s.title) })
println("-- map injected using  @Autowired -- ")
songMap!!.forEach(BiConsumer { k: String, v: Song ->
println(k + ": " + v.title) })
println("""-- map injected using @Resource / @Autowired @Qualifier(\"map\") /
@Inject @Named(\"map\")-- """)
songMapResource!!.forEach(BiConsumer { k: String, v: Song →
println(k + ": " + v.title) })
println("-- props injected with @Autowired -- ")
props!!.forEach { k: Any, v: Any -> println("$k: $v") }
}
}
Listing 3-29
Demo Class for Testing Configuration Class Declaring a Bean of Type List
```

代码看起来很简单，但当我们运行它时，打印结果如下：

```
Here's to hoping
Wishing the best for you
```

等等，什么？没错，两个额外的 `Song` bean 被添加到了一个列表中，并注入到了 `songList` 属性中，而不是我们预期的 `list` bean。这是怎么回事？你看到的行为是由 `@Autowired` 注解引起的。`@Autowired` 注解在语义上被定义为始终将数组、集合和映射视为相应 bean 的集合，目标 bean 类型由声明的集合值类型派生而来。我们的类有一个类型为 `List<Song>` 的属性，并且上面有 `@Autowired` 注解，因此 Spring 会尝试将当前 `ApplicationContext` 中所有类型为 `Song` 的 bean 注入到这个属性中，这会导致注入意外的依赖项，或者如果未定义类型为 `Song` 的 bean，Spring 会抛出异常。

因此，对于集合类型的注入，我们必须通过指定 bean 名称来明确指示 Spring 执行注入，这可以通过使用 Spring 的 `@Qualifier` 注解（位于 `org.springframework.beans.factory.annotation` 包中）来实现。之所以需要这个说明，是因为 Jakarta Inject 库中也有一个 `@Qualifier` 注解，但其用途不同。这意味着用 `@Autowired @Qualifier("list")` 注解 `songList` 依赖项可以确保预期的行为。然而，还有另外三种方法可以实现：

*   `@Inject @Named("list")`：这两个注解都可以在 `jakarta.inject` 包中找到。`@Inject` 注解是 Jakarta 中对应 Spring 的 `@Autowired` 的注解，而 `@Named` 注解则对应 Spring 的 `@Qualifier`。

*   `@Resource(name="list")`：前面提到过，这个注解可以在 `jakarta.annotation` 包中找到，它是进行集合注入的首选方式之一，因为使用一个注解比使用两个更好。

*   `@Value("#{collectionConfig.list}")`：由于最好保持在 Spring 领域内，并尽可能减少应用程序的依赖项，这实际上是推荐的注入集合的方式，并且毫无疑问会注入什么。

![](img/524962_1_En_3_Figm_HTML.jpg) 一个警告标志图标。它是一个三角形，中心有一个感叹号。 上述描述的集合注入行为同样适用于 `Set` 和 `Map`，唯一的区别在于，对于 `Map`，Spring 会注入 `{beanName,bean}` 键值对。



## 使用方法注入

除了构造器注入和 Setter 注入之外，Spring 提供的另一个较少使用的 DI 特性是*方法注入*。Spring 的方法注入能力包含两种松散相关的形式：查找方法注入和方法替换。查找方法注入提供了另一种机制，使 Bean 能够获取其依赖项之一。方法替换允许你任意替换 Bean 上任何方法的实现，而无需修改原始源代码。为了提供这两个特性，Spring 使用了 CGLIB 的动态字节码增强能力。CGLIB 是一个功能强大、高性能、高质量的代码生成库。它可以在运行时扩展 Java/Kotlin 类并实现接口。它是开源的，你可以在 [`https://github.com/cglib/cglib`](https://github.com/cglib/cglib) 找到官方仓库。

### 查找方法注入

*查找方法注入* 是在 Spring 1.1 版本中引入的，用于解决当一个 Bean 依赖于另一个具有不同生命周期的 Bean 时所遇到的问题，特别是当单例 Bean 依赖于非单例 Bean 时。在这种情况下，Setter 注入和构造器注入都会导致单例 Bean 持有一个本应为非单例 Bean 的单一实例。在某些情况下，你会希望单例 Bean 在每次需要该依赖 Bean 时都能获取一个新的非单例实例。

考虑一个场景：`LockOpener` 类提供了打开任何储物柜的服务。`LockOpener` 类依赖于一个 `KeyHelper` 类来打开储物柜，该类被注入到 `LockOpener` 中。然而，`KeyHelper` 类的设计涉及一些内部状态，使其不适合重用。每次调用 `openLock()` 方法时，都需要一个新的 `KeyHelper` 实例。在这种情况下，`LockOpener` 将是一个单例。但是，如果我们使用常规机制注入 `KeyHelper` 类，那么同一个 `KeyHelper` 类的实例（即 Spring 首次执行注入时实例化的那个）将被重用。为了确保每次调用 `openLock()` 方法时都传入一个新的 `KeyHelper` 实例，我们需要使用查找方法注入。

通常，你可以通过让单例 Bean 实现 `ApplicationContextAware` 接口（我们将在**第** **4** 章讨论此接口）来实现这一点。然后，使用 `ApplicationContext` 实例，单例 Bean 可以在每次需要时查找非单例依赖项的新实例。查找方法注入允许单例 Bean 声明它需要一个非单例依赖项，并且每次需要与之交互时，它都会收到一个新的非单例 Bean 实例，而无需实现任何 Spring 特定的接口。

查找方法注入的工作原理是让你的单例 Bean 声明一个方法（即查找方法），该方法返回非单例 Bean 的一个实例。当你在应用程序中获取对单例 Bean 的引用时，你实际上接收到的是一个动态创建的子类的引用，Spring 已经在该子类上实现了查找方法。一个典型的实现涉及将查找方法以及 Bean 类定义为抽象的。这可以防止当你忘记配置方法注入，并且直接使用带有空方法实现的 Bean 类而不是 Spring 增强的子类时，出现任何奇怪的错误。这个主题相当复杂，最好通过示例来说明。

在本例中，我们创建一个非单例 Bean 和两个实现相同接口的单例 Bean。其中一个单例 Bean 使用“传统”的 Setter 注入来获取非单例 Bean 的实例；另一个则使用方法注入。代码清单 3-30 中的代码示例描述了 `KeyHelper` 类，在本例中它是非单例 Bean 的类型，这意味着每次需要将其作为依赖项注入时，都会创建该类型的新实例。

```
package com.apress.prospring6.three.methodinject
import org.springframework.context.annotation.Scope
// 导入语句已省略
@Component("keyHelper")
@Scope("prototype")
class KeyHelper {
fun open(){
}
}
代码清单 3-30
非单例 Bean
```

这个类显然没什么特别之处，但它完美地服务于本示例的目的。接下来，在代码清单 3-31 中，你可以看到 `LockOpener` 接口，该接口由两个单例 Bean 类实现。

```
package com.apress.prospring6.three.methodinject
interface LockOpener {
fun createKeyOpener():KeyHelper?
fun openLock()
}
代码清单 3-31
单例 Bean 接口类型
```



该 Bean 包含两个方法：`createKeyOpener()` 和 `openLock()`。示例应用使用 `createKeyOpener()` 方法获取 `KeyHelper` 实例的引用，并在方法查找 Bean 的情况下执行实际的方法查找。`openLock()` 是一个简单方法，它依赖 `KeyHelper` 实例来完成处理。清单 3-32 展示了 `StandardLockOpener` 类，该类使用 Setter 注入来获取 `KeyHelper` 类的实例。

```
package com.apress.prospring6.three.methodinject
// 导入语句已省略
@Component("standardLockOpener")
internal class StandardLockOpener : LockOpener {
var keyOpener: KeyHelper? = null
override fun createKeyOpener(): KeyHelper? {
return keyOpener
}
@Autowired
@Qualifier("keyHelper")
fun setKeyHelper(keyHelper: KeyHelper?) {
keyOpener = keyHelper
}
override fun openLock() {
keyOpener!!.open()
}
}
清单 3-32
使用自动装配配置的 StandardLockOpener 类，用于获取 KeyHelper 类型的依赖
```

这段代码看起来应该很熟悉，但请注意，`openLock()` 方法使用存储的 `KeyHelper` 实例来完成其处理。在清单 3-33 中，你可以看到 `AbstractLockOpener` 类，它使用方法注入来获取 `KeyHelper` 类的实例，并通过 `org.springframework.beans.factory.annotation.Lookup` 注解进行配置。

```
package com.apress.prospring6.three.methodinject
import org.springframework.beans.factory.annotation.Lookup
// 导入语句已省略
@Component("abstractLockOpener")
internal abstract class AbstractLockOpener : LockOpener {
@get:Lookup("keyHelper")
abstract override fun createKeyOpener(): KeyHelper?
override fun openLock() {
myKeyOpener!!.open()
}
}
清单 3-33
使用方法注入配置的 AbstractLockOpener 类，用于获取 KeyHolder 类型的依赖
```

请注意，`createKeyOpener()` 方法被声明为抽象方法，并且 `openLock()` 方法使用该方法来获取 `KeyHelper` 实例。使用本节中的 Bean 定义填充应用上下文可能需要编写比编写简单配置类更多的代码，因此在清单 3-34 中展示了配置类。

```
package com.apress.prospring6.three.methodinject
// 导入语句已省略
@Configuration
@ComponentScan
class LookupConfig
清单 3-34
本节的 Kotlin 配置类
```

现在，`keyHelper` 和 `standardLockOpener` Bean 的配置对你来说应该很熟悉了。对于 `abstractLockOpener`，你需要使用 `@Lookup` 注解来配置查找方法。这告诉 Spring 它应该覆盖 Bean 上的哪个方法。该方法不能接受任何参数，并且返回类型应该是你希望从该方法返回的 Bean 的类型。在本例中，该方法应返回 `KeyHelper` 类型或其子类的实例。注解的属性值告诉 Spring 查找方法应返回哪个 Bean。清单 3-35 展示了此示例的最后一段代码，即包含用于运行示例的 `main()` 方法的类。

```
package com.apress.prospring6.three.methodinject
import org.springframework.util.StopWatch
// 导入语句已省略
object MethodInjectionDemo {
@JvmStatic
fun main(args: Array) {
val ctx = AnnotationConfigApplicationContext(LookupConfig::class.java)
val abstractLockOpener = ctx.getBean("abstractLockOpener", LockOpener::class.java)
val standardLockOpener = ctx.getBean("standardLockOpener", LockOpener::class.java)
displayInfo("abstractLockOpener", abstractLockOpener)
displayInfo("standardLockOpener", standardLockOpener)
}
fun displayInfo(beanName: String, lockOpener: LockOpener) {
val keyHelperOne = lockOpener.createKeyOpener()
val keyHelperTwo = lockOpener.createKeyOpener()
println("[" + beanName + "]: KeyHelper 实例相同吗？ " +
(keyHelperOne === keyHelperTwo))
val stopWatch = StopWatch()
stopWatch.start("lookupDemo")
for (x in 0..100000 - 1) {
val keyHelper = lockOpener.createKeyOpener()
keyHelper!!.open()
}
stopWatch.stop()
println("100000 次获取耗时 " + stopWatch.totalTimeMillis + " ms")
}
}
清单 3-35
用于测试方法注入的主类
```

在这段代码中，你可以看到从 `AnnotationConfigApplicationContext` 中检索了 `abstractLockOpener` 和 `standardLockOpener`，并将每个引用传递给了 `displayInfo()` 方法。仅当使用查找方法注入时，才支持实例化抽象类，此时 Spring 将使用 CGLIB 生成 `AbstractLockOpener` 类的子类，该子类会动态覆盖该方法。`displayInfo()` 方法的第一部分创建了两个 `KeyHelper` 类型的局部变量，并通过调用传递给它的 Bean 上的 `createKeyOpener()` 方法为每个变量赋值。利用这两个变量，它向控制台输出一条消息，指示这两个引用是否指向同一个对象。对于 `abstractLockOpener` Bean，每次调用 `createKeyOpener()` 都应获取一个新的 `KeyHelper` 实例，因此这两个引用不应相同。

对于 `standardLockOpener`，通过 Setter 注入将 `Singer` 的单个实例传递给 Bean，该实例被存储并在每次调用 `getMyKeyOpener()` 时返回，因此这两个引用应该是相同的。

![](img/524962_1_En_3_Fign_HTML.jpg) 一个信息图标。它是一个中心带有字母 i 的圆形。 前面示例中使用的 `StopWatch` 类是 Spring 提供的一个实用工具类。当你需要执行简单的性能测试或测试应用程序时，你会发现 `StopWatch` 非常有用。

`displayInfo()` 方法的最后一部分运行一个简单的性能测试，以查看哪个 Bean 更快。显然，`standardLockOpener` 应该更快，因为它每次都返回同一个实例，但观察差异也很有趣。现在我们可以运行 `MethodInjectionDemo` 类进行测试。以下是此示例的输出结果：

```
[abstractLockOpener]: KeyHelper 实例相同吗？ false
100000 次获取耗时 431 ms
[standardLockOpener]: KeyHelper 实例相同吗？ true
100000 次获取耗时 1 ms
```

如你所见，`KeyHelper` 实例正如预期的那样，在使用 `standardLockOpener` 时相同，而在使用 `abstractLockOpener` 时不同。使用 `standardLockOpener` 时存在明显的性能差异，但这也在意料之中。



### 关于查找方法注入的注意事项

查找方法注入适用于需要处理具有不同生命周期的两个 Bean 的场景。当 Bean 共享相同生命周期时，应避免使用查找方法注入，尤其是当它们都是单例时。运行前述示例的输出显示，使用方法注入获取依赖项的新实例与使用标准 DI 获取依赖项的单个实例之间，在性能上存在显著差异。此外，即使你拥有不同生命周期的 Bean，也请确保不要不必要地使用查找方法注入。

考虑这样一种情况：你有三个共享同一个依赖项的单例 Bean。你希望每个单例拥有该依赖项自己的实例，因此你将依赖项创建为非单例，但你满足于每个单例在其整个生命周期中使用该协作者的同一个实例。在这种情况下，Setter 注入是理想的解决方案；查找方法注入只会增加不必要的开销。

当你使用查找方法注入时，在构建类时应牢记一些设计准则。在前面的示例中，我们在接口中声明了查找方法。我们这样做的唯一原因是不必为两种不同的 Bean 类型重复编写 `displayInfo()` 方法。如前所述，通常你不需要用仅用于 IoC 目的的不必要定义来污染业务接口。另一点是，虽然你不必使查找方法成为抽象方法，但这样做可以防止你忘记配置查找方法，从而意外使用空实现。

## 理解 Bean 命名

Spring 支持相当复杂的 Bean 命名结构，使你能灵活处理多种情况。每个 Bean 必须至少有一个在包含它的 `ApplicationContext` 中唯一的名称。Spring 遵循一个简单的解析过程来确定 Bean 使用的名称。使用 XML 配置时，如果你给 `<bean>` 标签一个 `id` 属性，该属性的值将用作应用程序上下文中的唯一名称。

使用 Java/Kotlin 配置时，除非显式配置，否则 Spring 会使用一些策略生成 Bean 名称，这些策略将在本节中介绍。从应用程序中检索 Bean 时，可以使用 Bean 名称或 Bean 类型，或者两者都使用。如果声明了多个相同类型但没有 ID 或名称的 Bean，Spring 将在 `ApplicationContext` 初始化期间抛出 `org.springframework.beans.factory.NoSuchBeanDefinitionException` 异常。使用 Java/Kotlin 配置时，很难导致冲突，但这种情况仍可能发生，因此最好了解可能发生的情况。

### 使用 `@Component` 声明的 Bean 的默认命名风格

为了本节的目的，配置类为 `com.apress.prospring6.three.naming` 包启用了组件扫描。我们将从声明一个非常简单的 Bean 开始，如清单 3-36 所示。

```
@Component
class SimpleBean { }
清单 3-36
最简单的 Bean 类型
```

为了弄清楚 Spring 如何默认命名 Bean，我们基于一个包含 `SimpleBean` 类的配置创建了一个 `ApplicationContext`。该类使用 `@Component` 注解，并通过组件扫描发现。`ApplicationContext` 提供了检索 Bean 引用的方法，也提供了检索上下文中所有 Bean 名称的方法。在清单 3-37 中，上下文中所有 Bean 的名称都使用 Logback 记录器打印到控制台。

```
package com.apress.prospring6.three.naming
import org.springframework.context.annotation.
AnnotationConfigApplicationContext
import org.slf4j.Logger
import org.slf4j.LoggerFactory
// 其他导入语句已省略
object BeanNamingDemo {
private val logger: org.slf4j.Logger =
org.slf4j.LoggerFactory.getLogger(BeanNamingDemo::class.java)
@JvmStatic
fun main(args: Array) {
val ctx = AnnotationConfigApplicationContext(BeanNamingCfg::class.java)
ctx.beanDefinitionNames.forEach { beanName ->
logger.debug(beanName)
}
}
}
@Configuration
@ComponentScan
internal open class BeanNamingCfg {
}
@Component
internal class SimpleBean
清单 3-37
打印所有 Bean 名称
```

控制台输出的一部分如清单 3-38 所示。

```
DEBUG: BeanNamingDemo - org.springframework.context.annotation.internalConfigurationAnnotationProcessor
DEBUG: BeanNamingDemo - org.springframework.context.annotation.internalAutowiredAnnotationProcessor
DEBUG: BeanNamingDemo - org.springframework.context.annotation.internalCommonAnnotationProcessor
DEBUG: BeanNamingDemo - org.springframework.context.event.internalEventListenerProcessor
DEBUG: BeanNamingDemo - org.springframework.context.event.internalEventListenerFactory
DEBUG: BeanNamingDemo - beanNamingCfg
DEBUG: BeanNamingDemo - simpleBean
清单 3-38
显示上下文中所有 Bean 名称的控制台日志：输出
```

在列表中，你可以看到一些以 `org.springframework` 开头的 Bean 名称。这些是我们称之为*基础设施 Bean* 的 Bean，由 Spring 内部用于处理 Bean 定义和创建 Bean。那些明显不是 Spring 基础设施 Bean 的 Bean 是在应用程序配置中声明的 Bean。在清单 3-38 的输出中，有两个 Bean 应该会引起你的兴趣：

*   `beanNamingCfg`：此 Bean 名称与配置类 `BeanNamingCfg` 的简单名称相同。`@Configuration` 注解本身带有 `@Component` 注解，这意味着任何配置类本质上都是一个 Bean 定义。

*   `simpleBean`：此 Bean 名称与 Bean 类 `SimpleBean` 的简单名称相同。

    ![](img/524962_1_En_3_Figo_HTML.jpg) 一个警告警示图标。它是一个中心带有感叹号的圆形。 正如 `beanNamingCfg` 和 `simpleBean` 名称所证明的，当 Bean 名称未显式配置时，Spring 会获取声明 Bean 的类型的简单名称，将首字母改为小写，并使用结果值来命名 Bean。



### 自定义 Bean 命名风格

在进一步解释如何使用 `@Bean` 声明的 Bean 如何命名之前，我们先展示一个巧妙的技巧。与 Spring 中的几乎所有功能一样，如果存在默认行为，那么它就可以被自定义。因此，Bean 的命名也是可以自定义的。`@Configuration` 注解有一个名为 `nameGenerator` 的属性。该属性的值必须是一个实现了 `org.springframework.beans.factory.support.BeanNameGenerator` 接口的类，或者继承自 Spring 提供的任何实现。清单 3-39 展示了 `SimpleBeanNameGenerator` 类和 `BeanNamingCfg`。

```
package com.apress.prospring6.three.generator
import org.springframework.beans.factory.config.BeanDefinition
import org.springframework.beans.factory.support.
BeanDefinitionRegistry
import org.springframework.context.annotation.
AnnotationBeanNameGenerator
import java.util.UUID
// 其他导入语句已省略
@Configuration
@ComponentScan(nameGenerator = SimpleBeanNameGenerator::class)
internal open class BeanNamingCfg {
@Bean
open fun anotherSimpleBean(): SimpleBean {
return SimpleBean()
}
}
@Component
internal class SimpleBean
internal class SimpleBeanNameGenerator : AnnotationBeanNameGenerator() {
override fun buildDefaultBeanName(definition: BeanDefinition, registry:
BeanDefinitionRegistry): String {
val beanName = definition.beanClassName
.substring(definition.beanClassName.lastIndexOf(".") + 1).lowercase()
val uid = UUID.randomUUID().toString().replace("-", "").substring(0, 8)
return "$beanName-$uid"
}
}
清单 3-39
显示上下文中所有 Bean 名称的控制台日志
```

`org.springframework.context.annotation.AnnotationBeanNameGenerator` 类是用于处理带有 `@Component` 注解或其它被 `@Component` 注解的注解的 Bean 类的 `BeanNameGenerator` 实现。`SimpleBeanNameGenerator` 继承了这个类，并重写了 `buildDefaultBeanName(..)` 方法，以返回一个由小写简单类名与唯一标识符拼接而成的 Bean 名称。当创建应用上下文并打印 Bean 名称时，输出结果与清单 3-40 所示非常相似。

```
# 基础设施 bean 名称已省略
DEBUG: BeanNameGerneratorDemo - beanNamingCfg
DEBUG: BeanNameGerneratorDemo - simplebean-07f01cdc
清单 3-40
显示上下文中所有 Bean 名称的控制台日志
```

![](img/524962_1_En_3_Figp_HTML.jpg) 一个警告提示图标。它是一个中心带有感叹号的圆形。 这里唯一需要指出的是，配置类虽然使用了 `@Configuration` 注解（该注解本身又被 `@Component` 注解），但其 Bean 名称并非由 `SimpleBeanNameGenerator` 类生成。这是因为该类并非通过组件扫描发现的——它本身是启用组件扫描的类，并使用了自定义的 Bean 生成器，而 Spring 在处理该生成器之前就已经处理了这个配置类。

### 使用 `@Bean` 声明的 Bean 的命名风格

我们之前提到过，由 `@Bean` 注解的方法配置的 Bean，其名称就是配置它们的方法名。展示这一点的最简单方法是修改我们之前的某个示例，并在 `BeanNamingCfg` 配置类中使用 `@Bean` 注解声明一个 `SimpleBean`。清单 3-41 描述了新的配置以及用于列出上下文中 Bean 名称的执行代码。

```
package com.apress.prospring6.three.naming
import org.springframework.context.annotation.AnnotationConfigApplicationContext
import org.springframework.context.annotation.Bean
import org.springframework.context.annotation.ComponentScan
import org.springframework.context.annotation.Configuration
import org.springframework.stereotype.Component
import java.util.*
import java.util.function.Consumer
object BeanNamingDemo {
private val logger: org.slf4j.Logger =
org.slf4j.LoggerFactory.getLogger(BeanNamingDemo::class.java)
@JvmStatic
fun main(args: Array) {
val ctx = AnnotationConfigApplicationContext(BeanNamingCfg::class.java)
ctx.beanDefinitionNames.forEach { beanName ->
logger.debug(beanName)
}
}
}
@Configuration
@ComponentScan
internal open class BeanNamingCfg {
@Bean
open fun anotherSimpleBean(): SimpleBean {
return SimpleBean()
}
}
@Component
internal class SimpleBean
清单 3-41
显示上下文中所有 Bean 名称的控制台日志
```

运行清单 3-41 中的代码会产生如清单 3-42 所示的输出。

```
# 基础设施 bean 名称已省略
DEBUG: BeanNamingDemo - beanNamingCfg
DEBUG: BeanNamingDemo - simpleBean
DEBUG: BeanNamingDemo - anotherSimpleBean
清单 3-42
显示上下文中所有 Bean 名称的控制台日志
```

如您所见，列表中有一个名为 `anotherSimpleBean` 的条目，这意味着创建了一个 `SimpleBean`，并且其名称就是创建它的方法名。

只有当应用程序上下文中需要某种类型的单个 Bean 时，Bean 的默认命名才实用；但如果不是这种情况，显式命名 Bean 是唯一的选择。

在清单 3-41 的代码中，两个 `SimpleBean` 是通过不同的方法配置的，因此它们的名称是通过不同的方法生成的。这意味着通过调用 `ctx.getBean(SimpleBean.class)` 来获取 `SimpleBean` 类型的 Bean 将不再按预期工作，因为该方法期望找到一个 `SimpleBean` 类型的 Bean。调用该方法将导致抛出以下异常：

```
Exception in thread "main" org.springframework.beans.factory.NoUniqueBeanDefinitionException:
No qualifying bean of type 'com.apress.prospring6.three.naming.SimpleBean' available:
expected single matching bean but found 2: simpleBean,anotherSimpleBean
```

不过，异常信息非常清楚地说明了问题所在。

如果您需要从应用程序中获取某种类型的所有 Bean，有一个方法可以实现，如清单 3-43 所示。

```
val beans = ctx.getBeansOfType(SimpleBean::class.java)
beans.forEach{ (k, v) ->
println(k)
}
清单 3-43
列出上下文中所有 SimpleBean Bean 名称的代码
```

`ctx.getBeansOfType(String.class)` 用于获取一个映射，其中包含 `ApplicationContext` 中所有类型为 `SimpleBean` 的 Bean 及其 ID。映射的键是 Bean ID，通过前面代码中的 lambda 表达式打印出来。使用本节到目前为止的配置，输出如下：

```
simpleBean
anotherSimpleBean
```



### 显式 Bean 命名

显式配置 Bean 非常简单。当使用 `@Component` 或任何其他构造型注解（`@Service`、`@Repository` 等）声明 Bean 时，存在一个名为 `value` 的默认属性，可以为其初始化一个值，用作 Bean 的名称。清单 3-44 展示了一个名为 `simpleBeanOne` 的 `SimpleBean` Bean 的配置。

```
@Component(value = "simpleBeanOne")
class SimpleBean { }
清单 3-44
使用 @Component 声明具有自定义名称的 Bean
```

当一个注解属性被声明为默认属性时，这意味着在使用该注解时，可以省略该属性的名称，因此 `@Component(value = "simpleBeanOne")` 等同于 `@Component("simpleBeanOne")`。

`@Bean` 注解也有一个名为 `value` 的默认属性，用于类似的目的。它可以被初始化一个值，用作 Bean 的名称，但当使用一个值数组时，数组中的第一个值成为名称，其余的值成为别名。清单 3-45 展示了如何使用 `@Bean` 配置带有别名的 Bean，以及如何打印 Bean 的名称和别名。

```
package com.apress.prospring6.three.explicit
import org.springframework.context.annotation.AnnotationConfigApplicationContext
import org.springframework.context.annotation.Bean
import org.springframework.context.annotation.ComponentScan
import org.springframework.context.annotation.Configuration
import org.springframework.stereotype.Component
import java.util.*
/**
* Created by iuliana.cosmina on 09/03/2022
*/
object ExplicitBeanNamingDemo {
private val logger: org.slf4j.Logger =
org.slf4j.LoggerFactory.getLogger(ExplicitBeanNamingDemo::class.java)
@JvmStatic
fun main(args: Array) {
val ctx = AnnotationConfigApplicationContext(BeanNamingCfg::class.java)
Arrays.stream(ctx.beanDefinitionNames).forEach { beanName: String? ->
logger.debug(beanName)
}
val simpleBeans = ctx.getBeansOfType(SimpleBean::class.java)
simpleBeans.forEach { (k: String?, v: SimpleBean?) ->
val aliases = ctx.getAliases(k)
if (aliases.isNotEmpty()) {
logger.debug("Aliases for {} ", k)
Arrays.stream(aliases).forEach { a: String? ->
logger.debug("\t {}", a)
}
}
}
}
}
@Configuration
@ComponentScan
internal open class BeanNamingCfg {
// @Bean(name="simpleBeanTwo")
// @Bean(value= "simpleBeanTwo")
@Bean("simpleBeanTwo")
open fun simpleBean2(): SimpleBean {
return SimpleBean()
}
// @Bean(name= {"simpleBeanThree", "three", "numero_tres"})
// @Bean(value= {"simpleBeanThree", "three", "numero_tres"})
@Bean("simpleBeanThree", "three", "numero_tres")
open fun simpleBean3(): SimpleBean {
return SimpleBean()
}
}
@Component("simpleBeanOne")
internal class SimpleBean
清单 3-45
使用 @Bean 声明具有自定义名称的 Bean
```

在清单 3-45 的代码片段中，涵盖了命名 Bean 和指定别名的所有可能性；注释中的注解等同于未注释的注解。`simpleBeanTwo` 没有任何别名。`simpleBeanThree` 有两个别名。运行该代码片段时，将产生清单 3-46 中的输出。

```
DEBUG: ExplicitBeanNamingDemo - simpleBeanTwo
DEBUG: ExplicitBeanNamingDemo - simpleBeanThree
DEBUG: ExplicitBeanNamingDemo - Aliases for simpleBeanThree
DEBUG: ExplicitBeanNamingDemo -      three
DEBUG: ExplicitBeanNamingDemo -      numero_tres
清单 3-46
显示 Bean 别名的输出
```

### `@AliasFor` 注解

关于别名，Spring 4.2 引入了 `@AliasFor` 注解。此注解用于声明注解属性的别名，大多数 Spring 注解都使用了它。例如，`@Bean` 注解有两个属性 `name` 和 `value`，它们被声明为彼此的别名。清单 3-47 中的代码片段是 `@Bean` 注解 Java 代码的一个快照，取自官方 Spring GitHub 仓库。当前不相关的代码和文档已被省略。

```
package org.springframework.context.annotation;
import java.lang.annotation.Documented;
import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;
import org.springframework.core.annotation.AliasFor;
// 其他 import 语句已省略
@Target({ElementType.METHOD, ElementType.ANNOTATION_TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface Bean {
@AliasFor("name")
String value() default {};
@AliasFor("value")
String name() default {};
// 代码已省略
}
清单 3-47
来自 @Bean 注解的代码片段
```

这种使用 `@AliasFor` 的配置使得 `@Bean(name= {"simpleBeanThree", "three", "numero_tres"})` 等同于 `@Bean(value= {"simpleBeanThree", "three", "numero_tres"})`。

使用 `@AliasFor` 注解还可以做一些更有趣的事情：可以为元注解属性声明别名。在清单 3-48 中，你可以看到一个名为 `@Award` 的注解，它声明了一个名为 `prize` 的属性，以及一个名为 `@Trophy` 的注解，该注解使用 `@Award` 进行元注解。这个注解声明了一个属性，该属性是 `@Award` 接口中 `prize` 属性的别名。

```
package com.apress.prospring6.three.alias
// Award.kt
package com.apress.prospring6.three.alias
import java.lang.annotation.Documented
import java.lang.annotation.Retention
import java.lang.annotation.RetentionPolicy
@Target(AnnotationTarget.ANNOTATION_CLASS, AnnotationTarget.CLASS)
@Retention(RetentionPolicy.RUNTIME)
@Documented
annotation class Award(val prize: Array = [])
// Trophy.kt
package com.apress.prospring6.three.alias
import org.springframework.core.annotation.AliasFor
@Award
annotation class Trophy(
@get:AliasFor(annotation = Award::class, attribute = "value")
val name: Array = []
)
清单 3-48
@AliasFor 的使用
```

有了这两个注解，可以声明一个 `Singer` 类，并使用 `@Award` 或 `@Trophy` 进行注解，如清单 3-49 所示。

```
package com.apress.prospring6.three.alias
import org.springframework.stereotype.Component
@Component("johnMayer") //@Award(prize = {"grammy", "platinum disk"})
@Trophy(name = ["grammy", "platinum disk"])
class Singer {
private val lyric = "I used to crave the sight of you"
fun sing() {
println(lyric)
}
}
清单 3-49
使用别名注解进行注解的 Singer 类
```

![](img/524962_1_En_3_Figq_HTML.jpg) 一个带有警告标志的图标。它是一个圆形，中心有一个感叹号。 使用另一个注解 `@AliasFor` 为注解属性创建别名确实存在局限性。`@AliasFor` 不能用于任何构造型注解（`@Component` 及其特化注解）。原因是在 `@AliasFor` 发明之前，这些 value 属性的特殊处理就已经存在了。因此，由于向后兼容性问题，根本不可能将 `@AliasFor` 与这些 value 属性一起使用。当编写代码尝试这样做时（在构造型注解中为 value 属性创建别名），不会显示编译错误，代码甚至可能运行，但为别名提供的任何参数都将被忽略。`@Qualifier` 注解也是如此。



### 理解 Bean 的实例化模式

本章前面部分使用了术语**单例（singleton）**来描述那些在上下文中只创建一次的 Bean，并使用**非单例（non-singleton）**来描述那些每次从上下文请求时都会创建的 Bean。默认情况下，Spring 中的所有 Bean 都是单例的。这意味着 Spring 会维护 Bean 的单个实例，所有依赖对象都使用同一个实例，并且所有对 `ApplicationContext.getBean(..)` 的调用都会返回同一个实例。我们在上一节中演示了这一点，当时我们能够使用身份比较 `(==)` 而不是 `equals()` 比较来检查 Bean 是否相同。

在 Java/Kotlin 中，术语 *singleton* 可互换地用于指代两个不同的概念：在应用程序中具有单个实例的对象，以及单例设计模式。我们将第一个概念称为单例（singleton），将单例模式称为 Singleton。Singleton 设计模式由 Erich Gamma 等人在开创性的著作 *Design Patterns: Elements of Reusable Object-Oriented Software*（Addison-Wesley, 1994）中推广开来。当人们将单例实例的需求与应用 *Singleton* 模式的需求混淆时，问题就出现了。清单 3-50 展示了在 Kotlin 中通过 *对象（objects）* 实现 Singleton 模式的典型方式。

```
object Singleton {
}
清单 3-50
Kotlin 中的 Singleton 模式
```

这与 Java 的实现有本质区别，Java 的实现需要使用静态字段和私有构造函数的略显笨拙的结构。该模式实现了其目标，即允许你在整个应用程序中维护和访问类的单个实例，但代价是增加了耦合性。你的应用程序代码必须始终显式地知道 `Singleton` 类才能获取该实例——这完全消除了面向接口编程的可能性。

实际上，*Singleton* 模式实际上是两个模式的结合。第一个也是期望的模式涉及维护对象的单个实例。第二个不太理想的模式是一种对象查找模式，它完全消除了使用接口的可能性。使用 Singleton 模式也使得任意替换实现变得困难，因为需要 Singleton 实例的对象会直接访问 Singleton 对象。当你尝试对应用程序进行单元测试时，这可能会引起各种麻烦，因为你无法用模拟对象（mock）替换 Singleton 以进行测试。

幸运的是，使用 Spring，你可以利用单例实例化模型，而无需绕开 Singleton 设计模式。默认情况下，Spring 中的所有 Bean 都作为单例实例创建，并且 Spring 使用相同的实例来满足对该 Bean 的所有请求。当然，Spring 并不仅限于使用单例实例；它仍然可以为每个依赖项和每次对 `getBean()` 的调用创建 Bean 的新实例。它完成所有这些操作都不会对你的应用程序代码产生任何影响，因此，我们喜欢将 Spring 称为**实例化模式无关的**。这是一个强大的概念。如果你从一个单例对象开始，但后来发现它并不真正适合多线程访问，你可以将其更改为非单例（*原型*），而不会影响你的任何应用程序代码。

![](img/524962_1_En_3_Figr_HTML.jpg) 一个警报警告标志图标。它是一个中心带有感叹号的圆形。 虽然更改 Bean 的实例化模式不会影响你的应用程序代码，但如果你依赖 Spring 的生命周期接口，它确实会引起一些问题。我们将在**第** **4** **章**中更详细地讨论这一点。

将实例化模式从单例更改为非单例很简单。清单 3-51 中的配置展示了使用 `@Scope` 并将 `scopeName` 属性设置为 `prototype` 来将 Bean 配置为非单例。

```
@Component("nonSingleton")
@Scope(scopeName = "prototype")
class Singer(@Value("John Mayer") val name:String  = "unknown") {
}
清单 3-51
将 Bean 配置为非单例
```

如你所见，此 Bean 声明与你迄今为止看到的任何声明之间的唯一区别是我们添加了 `scope` 属性并将其值设置为 `prototype`。Spring 默认将作用域设置为 `singleton`。原型作用域指示 Spring 在应用程序每次请求 Bean 实例时都实例化一个新的 Bean 实例。清单 3-52 展示了此设置对你的应用程序的影响。

```
package com.apress.prospring6.three.scope
import com.apress.prospring6.three.generator.BeanNameGerneratorDemo
import org.springframework.beans.factory.annotation.Value
import org.springframework.context.annotation.AnnotationConfigApplicationContext
import org.springframework.context.annotation.Scope
import org.springframework.stereotype.Component
@Component("nonSingleton")
@Scope(scopeName = "prototype")
internal class Singer(@Value("John Mayer") val name: String = "unknown") {
override fun toString(): String {
return "${super.toString()}, name=$name"
}
}
object NonSingletonDemo {
private val logger: org.slf4j.Logger =
org.slf4j.LoggerFactory.getLogger(BeanNameGerneratorDemo::class.java)
@JvmStatic
fun main(args: Array) {
val ctx = AnnotationConfigApplicationContext()
ctx.register(Singer::class.java)
ctx.refresh()
val singer1 = ctx.getBean("nonSingleton", Singer::class.java)
val singer2 = ctx.getBean("nonSingleton", Singer::class.java)
logger.info("Identity Equal?: " + (singer1 === singer2))
logger.info("Value Equal:? " + (singer1 == singer2))
logger.info(singer1.toString())
logger.info(singer2.toString())
}
}
清单 3-52
从上下文中访问非单例 Bean
```

运行此示例将得到清单 3-53 中的输出，这毫无疑问地证明了 `ctx.getBean("nonSingleton", Singer.class)` 返回了两个不同的实例。

```
INFO : NonSingletonDemo - Identity Equal?: false
INFO : NonSingletonDemo - Value Equal:? false
INFO : NonSingletonDemo - com.apress.prospring6.three.scope.Singer@6631f5ca[name=John Mayer]
INFO : NonSingletonDemo - com.apress.prospring6.three.scope.Singer@5ace1ed4[name=John Mayer]
清单 3-53
清单 3-52 中代码的输出
```

从该输出中可以看出，尽管两个 `String` 对象的值明显相等，但它们的身份并不相同，即使两个实例都是使用相同的 Bean 名称检索的。



## 选择实例化模式

在大多数场景下，很容易判断哪种实例化模式是合适的。通常，你会发现单例是 Bean 的默认模式。一般来说，单例模式适用于以下场景：

*   **无状态共享对象**：你有一个不维护状态且拥有许多依赖对象的对象。由于没有状态就不需要同步，因此当依赖对象需要用它进行某些处理时，无需每次都创建新的 Bean 实例。

*   **只读状态共享对象**：这与上一点类似，但包含一些只读状态。在这种情况下，你仍然不需要同步，因此为每次 Bean 请求创建实例只会增加开销。

*   **共享状态共享对象**：如果你有一个 Bean 的状态需要被共享，单例是理想的选择。在这种情况下，请确保对状态写入的同步尽可能细粒度。

*   **具有可写状态的高吞吐量对象**：如果你的应用程序中大量使用某个 Bean，你可能会发现，保持单例并同步所有对 Bean 状态的写访问，比不断创建数百个 Bean 实例能带来更好的性能。使用此方法时，请尽量在不牺牲一致性的前提下保持同步的细粒度。你会发现，当你的应用程序在长时间内创建大量实例、共享对象只有少量可写状态，或者新实例的创建成本很高时，这种方法特别有用。

你应该考虑在以下场景中使用非单例模式：

*   **具有可写状态的对象**：如果你有一个 Bean 包含大量可写状态，你可能会发现同步的成本高于为处理依赖对象的每次请求而创建新实例的成本。

*   **具有私有状态的对象**：某些依赖对象需要一个具有私有状态的 Bean，以便它们能够与依赖该 Bean 的其他对象分开进行处理。在这种情况下，单例显然不合适，你应该使用非单例。

从 Spring 的实例化管理中获得的主要好处是，你的应用程序可以立即受益于单例模式带来的更低内存使用率，而几乎不需要你付出什么努力。然后，如果你发现单例模式不能满足应用程序的需求，修改配置以使用非单例模式也是一项微不足道的任务。

## 其他 Bean 作用域

除了单例和原型作用域之外，在定义 Spring Bean 时，针对更特定的目的还存在其他作用域。你也可以实现自己的自定义作用域，并将其注册到 Spring 的 `ApplicationContext` 中。从版本 6 开始，支持以下 Bean 作用域：

*   `singleton`：默认的单例作用域。每个 Spring IoC 容器只会创建一个对象。

*   `prototype`：当应用程序请求时，Spring 会创建一个新的实例。

*   `request`：用于 Web 应用程序。当使用 Spring MVC 开发 Web 应用程序时，每个 HTTP 请求都会实例化具有请求作用域的 Bean，并在请求完成后销毁。

*   `session`：用于 Web 应用程序。当使用 Spring MVC 开发 Web 应用程序时，每个 HTTP 会话都会实例化具有会话作用域的 Bean，并在会话结束后销毁。

*   `application`：将单个 Bean 定义的作用域限定为 `ServletContext` 的生命周期。应用程序作用域仅在 Spring Web 应用程序中有效。

*   `thread`：当新线程请求时，Spring 会创建一个新的 Bean 实例，而对于同一个线程，则会返回相同的 Bean 实例。请注意，此作用域默认情况下未注册。

*   `custom`：自定义 Bean 作用域，可以通过实现接口 `org.springframework.beans.factory.config.Scope` 并在 Spring 配置中注册自定义作用域来创建（对于 XML，请使用类 `org.springframework.beans.factory.config.CustomScopeConfigurer`）。

*   `websocket`：将单个 Bean 定义的作用域限定为 `WebSocket` 的生命周期。WebSocket 作用域仅在 Spring Web 应用程序中有效。



## 解决依赖关系

Spring 能够通过简单地查看配置文件或类中的注解来解决依赖关系。但遗憾的是，Spring 无法感知代码中那些未在配置中指定的 Bean 之间的任何依赖关系。例如，有一个名为 `johnMayer`、类型为 `Singer` 的 Bean，它通过 `ctx.getBean()` 获取另一个名为 `gopher`、类型为 `Guitar` 的 Bean 实例，并在调用 `johnMayer.sing()` 方法时使用它。在此方法中，你通过调用 `ctx.getBean("gopher")` 获取 `Guitar` 类型的实例，而没有要求 Spring 为你注入依赖。在这种情况下，Spring 并不知道 `johnMayer` 依赖于 `gopher`，因此它可能会在 `gopher` Bean 之前实例化 `johnMayer` Bean。

你可以通过使用 `@DependsOn` 注解，向 Spring 提供关于 Bean 依赖关系的额外信息。清单 3-54 展示了如何配置 `johnMayer` 和 `gopher` Bean 的场景。

```
package com.apress.prospring6.three.dependson
import org.springframework.beans.BeansException
import org.springframework.context.ApplicationContext
import org.springframework.context.ApplicationContextAware
import org.springframework.context.annotation.AnnotationConfigApplicationContext
import org.springframework.context.annotation.DependsOn
import org.springframework.stereotype.Component
object DependsOnDemo {
@JvmStatic
fun main(args: Array) {
val ctx = AnnotationConfigApplicationContext()
ctx.register(Singer::class.java, Guitar::class.java)
ctx.refresh()
val johnMayer = ctx.getBean("johnMayer", Singer::class.java)
johnMayer.sing()
}
}
@Component("gopher")
internal class Guitar {
fun sing() {
println("Cm Eb Fm Ab Bb")
}
}
@DependsOn("gopher")
@Component("johnMayer")
internal class Singer : ApplicationContextAware {
private var ctx: ApplicationContext? = null
@Throws(BeansException::class)
override fun setApplicationContext(applicationContext: ApplicationContext) {
ctx = applicationContext
}
private var guitar: Guitar? = null
fun sing() {
guitar = ctx!!.getBean("gopher", Guitar::class.java)
guitar!!.sing()
}
}
清单 3-54
使用 @DependsOn 配置依赖关系
```

在此配置中，我们声明了 `johnMayer` Bean 依赖于 `gopher` Bean。Spring 在实例化 Bean 时应考虑这一点，并确保 `gopher` 在 `johnMayer` 之前创建。对于 `johnMayer` Bean，要自行获取依赖，它需要访问 `ApplicationContext`。因此，我们还必须告诉 Spring 注入此引用，这样当调用 `johnMayer.sing()` 方法时，就可以用它来获取 `gopher` Bean。这是通过让 `Singer` 类实现 `ApplicationContextAware` 接口来完成的。这是一个 Spring 特有的接口，它强制要求为 `ApplicationContext` 对象实现一个 setter 方法。Spring IoC 容器会自动检测到它，并将创建该 Bean 的 `ApplicationContext` 注入其中。这发生在 Bean 的构造函数被调用之后，因此，在构造函数中使用 `ApplicationContext` 显然会导致 `NullPointerException`。

运行清单 3-54 时，输出结果如下：`Cm Eb Fm Ab Bb.`

在开发应用程序时，应避免设计使用此功能；相反，应通过 setter 和构造函数注入契约来定义依赖关系。但是，如果你正在将 Spring 与遗留代码集成，你可能会发现代码中定义的依赖关系需要你向 Spring 框架提供额外信息。

## 自动装配你的 Bean

*自动装配* 是将 Bean 隐式注入到依赖于它们的 Bean 中的过程。Spring 支持五种自动装配模式：

*   `byName`：使用 `byName` 自动装配时，Spring 会尝试将每个属性装配到同名的 Bean。因此，如果目标 Bean 有一个名为 `foo` 的属性，并且在 `ApplicationContext` 中定义了一个 `foo` Bean，那么 `foo` Bean 就会被赋值给目标的 `foo` 属性。

*   `byType`：使用 `byType` 自动装配时，Spring 会尝试通过自动使用 `ApplicationContext` 中相同类型的 Bean 来装配目标 Bean 上的每个属性。

*   `constructor`：此功能与 `byType` 自动装配类似，区别在于它使用构造函数而非 setter 方法进行注入。Spring 会尝试匹配构造函数中尽可能多的参数。因此，如果你的 Bean 有两个构造函数，一个接受 `String`，另一个接受 `String` 和 `Integer`，并且你的 `ApplicationContext` 中同时存在 `String` 和 `Integer` 类型的 Bean，那么 Spring 会使用双参数构造函数。

*   `default`：Spring 会自动在 `constructor` 和 `byType` 模式之间选择。如果你的 Bean 有一个默认（无参数）构造函数，Spring 会使用 `byType`；否则，它会使用 `constructor`。

*   `no`：不进行自动装配；这是默认模式。

在使用 XML 配置时，可以通过元数据显式配置自动装配模式，方法是在 `<bean/>` 元素上指定 `autowire` 属性的值。由于本书不涉及 XML，我们将重点放在注解配置上。



### 构造函数自动装配

当通过构造函数注入提供依赖时，自动装配显然是通过构造函数完成的。如果类有多个构造函数，则会根据一些条件选择要使用的构造函数。

如果没有构造函数使用 `@Autowired` 注解，则会选择最合适的那个。如果有多个合适的构造函数，Spring 会优先使用无参构造函数（如果存在）。如果不存在无参构造函数，则会抛出 `BeanInstantiationException` 异常。请查看清单 3-55 中的示例。

```
package com.apress.prospring6.three.autowiring
import org.springframework.context.annotation.Lazy
// 其他导入语句已省略
object AutowiringDemo {
private val logger: Logger = LoggerFactory.getLogger(AutowiringDemo::class.java)
@JvmStatic
fun main(args: Array) {
val ctx = AnnotationConfigApplicationContext(AutowiringCfg::class.java)
val target = ctx.getBean(Target::class.java)
logger.info("创建 target? {}", target != null)
logger.info("注入 bar? {}", target.bar != null)
logger.info("注入 fooOne? {}", if (target.fooOne != null) target.fooOne!!.id else "")
logger.info("注入 fooTwo? {}", if (target.fooTwo != null) target.fooTwo!!.id else "")
}
}
@Configuration
@ComponentScan
internal class AutowiringCfg
@Component
@Lazy
internal class Target {
var fooOne: Foo? = null
var fooTwo: Foo? = null
var bar: Bar? = null
constructor() {
logger.info(" --> Target() 被调用")
}
constructor(foo: Foo?) {
fooOne = foo
logger.info(" --> Target(Foo) 被调用")
}
constructor(foo: Foo?, bar: Bar?) {
fooOne = foo
this.bar = bar
logger.info(" --> Target(Foo, Bar) 被调用")
}
companion object {
private val logger: Logger = LoggerFactory.getLogger(Target::class.java)
}
}
@Component
internal class Foo {
var id = UUID.randomUUID().toString().replace("-", "").substring(0, 8)
}
@Component
internal class Bar
清单 3-55
按类型自动装配的示例
```

在这段代码中，你可以看到 `Target` 类有三个构造函数：

*   一个无参构造函数，
*   一个接受 `Foo` 实例的构造函数，
*   一个接受 `Foo` 实例和 `Bar` 实例的构造函数。

每个构造函数在被调用时都会向控制台输出一条消息。`main()` 方法简单地检索在 `ApplicationContext` 中声明的 `Target` bean，从而触发自动装配过程。几条日志语句会打印关于 `Target` bean 的详细信息。

除了这些构造函数，`Target` bean 还有三个属性：

*   两个 `Foo` 类型的属性，
*   一个 `Bar` 类型的属性。

`Foo` 和 `Bar` 是简单的类。每个 `Foo` 对象在实例化时都会获得一个唯一的 ID。`@Lazy` 注解用于告知 Spring 仅在首次请求时才实例化该 bean，而不是在启动时，这样我们就可以在测试程序的正确位置输出结果。

运行这段代码时，会输出清单 3-56 中的内容，这毫无疑问地证明了当 Spring 没有被告知该做什么时，它会直接使用无参构造函数，这意味着没有任何属性会被注入。

```
INFO : Target -  --> Target() 被调用
INFO : AutowiringDemo - 创建 target? true
INFO : AutowiringDemo - 注入 bar? false
INFO : AutowiringDemo - 注入 fooOne?
INFO : AutowiringDemo - 注入 fooTwo?
清单 3-56
当没有构造函数使用 @Autowired 注解时，按构造函数自动装配的示例
```

要改变这种行为，只需用 `@Autowired` 注解任意一个构造函数即可。由于应用程序中声明了 `Foo` 和 `Bar` bean，因此可以使用另外两个构造函数中的任意一个来初始化 `Target`。

如果删除了无参构造函数，则会抛出以下异常。

```
Caused by: org.springframework.beans.BeanInstantiationException:
Failed to instantiate [com.apress.prospring6.three.autowiring.Target]:
No default constructor found;
nested exception is java.lang.NoSuchMethodException:
com.apress.prospring6.three.autowiring.Target.()
```

编译器允许你用 `@Autowired` 注解剩余的两个构造函数，因为 Kotlin 编译器并不关心 Spring 允许什么，但运行代码时会导致混乱，并且无法创建 `Target` bean。它会抛出 `BeanCreationException` 异常，当 `Target` 类有两个被注解的构造函数时，以下片段显示了部分堆栈跟踪。

```
Exception in thread "main" org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'target':
Invalid autowire-marked constructor: public com.apress.prospring6.three.autowiring.Target(com.apress.prospring6.three.autowiring.
Foo,com.apress.prospring6.three.autowiring.Bar).
Found constructor with 'required' Autowired annotation already: public com.apress.prospring6.three.autowiring.Target(com.apress.prospring6.three.autowiring.Foo)
...
at com.apress.prospring6.three.autowiring.AutowiringDemo.main(AutowiringDemo.java:51)
```



### `byType` 自动装配

当没有声明构造函数，但存在使用 `@Autowired` 注解的 setter 方法时，Spring 将使用这些 setter 方法，并根据其类型识别要注入的 Bean。清单 3-57 展示了一个名为 `AnotherTarget` 的类，它与 `Target` 类非常相似，但其属性是通过 setter 方法注入的。

```
object AutowiringDemo {
private val logger = LoggerFactory.getLogger(AutowiringDemo::class.java)
@JvmStatic
fun main(args: Array) {
val ctx = AnnotationConfigApplicationContext(AutowiringCfg::class.java)
val anotherTarget: AnotherTarget = ctx.getBean(AnotherTarget::class.java)
logger.info("anotherTarget: Created anotherTarget? {}", anotherTarget != null)
logger.info("anotherTarget: Injected bar? {}", anotherTarget.bar != null)
logger.info(
"anotherTarget: Injected fooOne? {}",
if (anotherTarget.fooOne != null) anotherTarget.fooOne.id else ""
)
logger.info(
"anotherTarget: Injected fooTwo? {}",
if (anotherTarget.fooTwo != null) anotherTarget.fooTwo.id else ""
)
}
}
// 省略了之前示例中的部分代码
@Component
@Lazy
internal class AnotherTarget {
var fooOne: Foo? = null
@Autowired set(@Qualifier("foo") value) {
logger.info(" --> AnotherTarget#setFooOne(Foo) called")
field = value
}
var fooTwo: Foo? = null
@Autowired set(@Qualifier("anotherFoo") value) {
logger.info(" --> AnotherTarget#setFooTwo(Foo) called")
field = value
}
var bar: Bar? = null
@Autowired set(value) {
logger.info(" --> AnotherTarget#setBar(Bar) called")
field = value
}
companion object {
private val logger: org.slf4j.Logger =
org.slf4j.LoggerFactory.getLogger(AnotherTarget::class.java)
}
}
清单 3-57
使用 Setter 方法按类型自动装配的示例
```

运行此代码时，将输出清单 3-58 中的内容。

```
INFO : AnotherTarget -  --> AnotherTarget#setFooOne(Foo) called
INFO : AnotherTarget -  --> AnotherTarget#setFooTwo(Foo) called
INFO : AnotherTarget -  --> AnotherTarget#setBar(Bar) called
INFO : AutowiringDemo - anotherTarget: Created anotherTarget? true
INFO : AutowiringDemo - anotherTarget: Injected bar? true
INFO : AutowiringDemo - anotherTarget: Injected fooOne? a4eb2b71
INFO : AutowiringDemo - anotherTarget: Injected fooTwo? a4eb2b71
清单 3-58
当 Setter 方法使用 @Autowired 注解时按类型自动装配的示例
```

当字段直接使用 `@Autowired` 注解时，Spring 也会按类型注入依赖。由于不推荐使用字段注入，本书将不展示相关代码，但可以查看项目中的 `com.apress.prospring6.three.autowiring.FieldTarget` 类。

到目前为止，您已经看到，只声明了一个 `Foo` Bean，并且该 Bean 被注入到了 `fooOne` 和 `fooTwo` 两个属性中。如果我们再声明另一个 `Foo` Bean，这将破坏 `Target`、`AnotherTarget` 和 `FieldTarget` 的 Bean 定义，Spring 将无法再创建它们。因为依赖项是按类型自动装配的，并且需要两个 `Foo` 类型的依赖，而 Spring 无法确定这两个 `Foo` Bean 分别需要注入到哪里。清单 3-59 展示了更新后的 `AutowiringCfg` 类，它声明了一个 `Foo` 类型的 Bean。

```
@Configuration
@ComponentScan
class AutowiringCfg {
@Bean
fun anotherFoo():Foo {
return Foo()
}
}
清单 3-59
声明了一个 Foo 类型 Bean 的 AutowiringCfg 类
```

如果您运行 `AutowiringDemo` 类，将会抛出以下 `org.springframework.beans.factory.NoUniqueBeanDefinitionException` 异常：

```
Caused by: org.springframework.beans.factory.NoUniqueBeanDefinitionException:
No qualifying bean of type 'com.apress.prospring6.three.autowiring.Foo'
available: expected single matching bean but found 2: foo,anotherFoo
```

要解决 Spring 的这个困境，解决方案是指定我们希望将哪个 Bean 注入到哪里，这意味着从按类型自动装配切换到按名称自动装配。

### `byName` 自动装配

清单 3-60 展示了修改后的 `Target` 类，其构造函数被修改为接收特定的 Bean 作为参数。之前用于注入集合的 `@Qualifier` 注解，在这里同样起到了关键作用。

```
package com.apress.prospring6.three.autowiring
import org.springframework.beans.factory.annotation.Qualifier
// 省略了其他 import 语句
@Component
@Lazy
internal class Target {
var fooOne: Foo? = null
var fooTwo: Foo? = null
var bar: Bar? = null
@Autowired
constructor(@Qualifier("foo") foo: Foo?) {
fooOne = foo
logger.info(" --> Target(Foo) called")
}
@Autowired
constructor(@Qualifier("foo") foo: Foo?, bar: Bar?) {
fooOne = foo
this.bar = bar
logger.info(" --> Target(Foo, Bar) called")
}
companion object {
private val logger: Logger = LoggerFactory.getLogger(Target::class.java)
}
}
清单 3-60
使用构造函数按名称自动装配配置的 Target Bean 类
```

请注意，`@Qualifier` 是放在参数上，而不是构造函数上。如果您使用 `@Inject` 而不是 `@Autowired` 来注入依赖，请使用 `@Named` 而不是 `@Qualifier`。要修复 `AnotherTarget` 类，需要将 `@Qualifier` 放在 setter 参数上，如清单 3-61 所示。

```
package com.apress.prospring6.three.autowiring;
import org.springframework.beans.factory.annotation.Qualifier;
// 省略了其他 import 语句
@Component
@Lazy
internal class AnotherTarget {
var fooOne: Foo? = null
@Autowired set(@Qualifier("foo") value) {
logger.info(" --> AnotherTarget#setFooOne(Foo) called")
field = value
}
var fooTwo: Foo? = null
@Autowired set(@Qualifier("anotherFoo") value) {
logger.info(" --> AnotherTarget#setFooTwo(Foo) called")
field = value
}
var bar: Bar? = null
@Autowired set(value) {
logger.info(" --> AnotherTarget#setBar(Bar) called")
field = value
}
companion object {
private val logger: org.slf4j.Logger =
org.slf4j.LoggerFactory.getLogger(AnotherTarget::class.java)
}
}
清单 3-61
使用 Setter 方法按名称自动装配配置的 AnotherTarget Bean 类
```

运行 `AutowiringDemo`，访问 `AnotherTarget` 并检查其依赖项，将打印出清单 3-62 中的详细信息，这清楚地表明有两个不同的 `Foo` Bean 被注入。

```
INFO : AnotherTarget -  --> AnotherTarget#setFooOne(Foo) called
INFO : AnotherTarget -  --> AnotherTarget#setFooTwo(Foo) called
INFO : AnotherTarget -  --> AnotherTarget#setBar(Bar) called
INFO : AutowiringDemo - anotherTarget: Created anotherTarget? true
INFO : AutowiringDemo - anotherTarget: Injected bar? true
INFO : AutowiringDemo - anotherTarget: Injected fooOne? baa24632
INFO : AutowiringDemo - anotherTarget: Injected fooTwo? 0d74b352
清单 3-62
当 Setter 方法使用 @Autowired 注解时按类型自动装配的示例
```

至于 `FieldTarget`，事情就更简单了；`@Qualifier` 注解直接放在被自动装配的字段上，如清单 3-63 所示。

```
package com.apress.prospring6.three.autowiring
import org.springframework.beans.factory.annotation.Qualifier
// 省略了其他 import 语句
@Component
@Lazy
class FieldTarget {
companion object {
private val logger: org.slf4j.Logger =
org.slf4j.LoggerFactory.getLogger(FieldTarget::class.java)
}
@Autowired @Qualifier("foo") var fooOne:Foo? = null
@Autowired @Qualifier("anotherFoo") var fooTwo:Foo? = null
@Autowired var bar:Bar? = null
}
清单 3-63
使用字段按名称自动装配配置的 FieldTarget Bean 类
```



### 又一个难题

当按类型自动装配时，如果 bean 类型之间存在关联，事情就会变得复杂。当有多个类实现了同一个接口，并且需要自动装配的属性将该接口指定为类型时，就会抛出异常，因为 Spring 不知道该注入哪个 bean。为了创建这样一个场景，我们将把 `Foo` 转换成一个接口，并声明两个实现它的 bean 类型，每个类型都有自己的 bean 声明。配置是默认的，bean 名称由 Spring 生成，如清单 3-64 所示。

```
package com.apress.prospring6.three.pickle
// 其他导入语句已省略
internal interface Foo { // 空接口，用作标记接口
}
internal class FooImplOne : Foo {
var id = "one:" + UUID.randomUUID().toString().replace("-", "").substring(0, 8)
override fun toString(): String {
return super.toString() + ", id=" + id.toString()
}
}
internal class FooImplTwo : Foo {
var id = "two:" + UUID.randomUUID().toString().replace("-", "").substring(0, 8)
override fun toString(): String {
return super.toString() + ", id=" + id.toString()
}
}
internal class Bar
@Configuration
@ComponentScan
internal class AutowiringCfg {
@Bean
fun fooImplOne(): Foo {
return FooImplOne()
}
@Bean
fun fooImplTwo(): Foo {
return FooImplTwo()
}
@Bean
fun bar(): Bar {
return Bar()
}
@Bean
fun trickyTarget(): TrickyTarget {
return TrickyTarget()
}
}
internal class TrickyTarget {
var fooOne: Foo? = null
var fooTwo: Foo? = null
var bar: Bar? = null
}
清单 3-64
Foo 实现类及配置
```

`TrickyTarget` 代码与 `Target` 类非常相似，当按类型注入依赖项时（如清单 3-64 所示），Spring 无法创建此 bean，并会抛出 `NoUniqueBeanDefinitionException`：

```
Caused by: org.springframework.beans.factory.NoUniqueBeanDefinitionException:
No qualifying bean of type 'com.apress.prospring6.three.pickle.Foo'
available: expected single matching bean but found 2: fooImplOne,fooImplTwo
```

控制台输出要长得多，但上述输出的前几行以相当易读的方式揭示了问题。当 Spring 不知道要自动装配哪个 bean 时，它会抛出一个 `NoUniqueBeanDefinitionException`，并附带一条明确的消息。它会告诉你找到了哪些 bean，但无法选择在何处使用哪一个。有两种方法可以解决这个问题。

第一种方法是在你希望 Spring 优先考虑自动装配的 bean 定义中使用 `@Primary` 注解。这以一种相当奇特的方式解决了问题，因为在 `fooOne` 和 `fooTwo` 中，都会注入带有 `@Primary` 注解的 bean。配置及结果输出如清单 3-65 所示。

```
package com.apress.prospring6.three.pickle
// 导入语句已省略
import org.springframework.context.annotation.Primary
@Configuration
@ComponentScan
class AutowiringCfg {
@Bean @Primary
fun fooImplOne(): Foo = FooImplOne()
@Bean
fun fooImplTwo(): Foo = FooImplTwo()
@Bean
fun bar(): Bar = Bar()
@Bean
fun trickyTarget(): TrickyTarget = TrickyTarget()
}
// 使用 @Primary 配置的日志
INFO : TrickyTarget -  --> Property fooOne set
INFO : TrickyTarget -  --> Property fooTwo set
INFO : TrickyTarget -  --> Property bar set
INFO : PickleAutowiringDemo - target: Created target? true
INFO : PickleAutowiringDemo - target: Injected bar? true
INFO : PickleAutowiringDemo - target: Injected fooOne? com.apress.prospring6.three.pickle.FooImplOne@2eea88a1[id=one:7ab78d36]
INFO : PickleAutowiringDemo - target: Injected fooTwo? com.apress.prospring6.three.pickle.FooImplOne@2eea88a1[id=one:7ab78d36]
清单 3-65
使用 @Primary 的配置
```

所以，一切恢复正常，但即便如此，使用 `@Primary` 仅在只有两个相关 bean 类型时才是解决方案。如果有更多类型，使用它并不能消除 `NoUniqueBeanDefinitionException`。

真正能解决问题的是第二种方法，它能让你完全控制哪个 bean 注入到何处，即命名你的 bean，并使用 `@Qualifier` 配置它们注入的位置，如上一节所示。清单 3-66 展示了使用 `@Qualifier` 配置 setter 的 `TrickyTarget` 类，以及运行 `PickleAutowiringDemo` 类后的结果输出。

```
package com.apress.prospring6.three.pickle
// 导入语句已省略
import org.springframework.beans.factory.annotation.Qualifier
class TrickyTarget {
companion object {
private val logger: org.slf4j.Logger =
org.slf4j.LoggerFactory.getLogger(FieldTarget::class.java)
}
var fooOne:Foo? = null
@Autowired
@Qualifier("fooImplOne")
set(value) {
field = value
logger.info(" --> Property fooOne set")
}
var fooTwo:Foo? = null
@Autowired
@Qualifier("fooImplTwo")
set(value) {
field = value
logger.info(" --> Property fooTwo set")
}
var bar:Bar? = null
@Autowired
set(value) {
field = value
logger.info(" --> Property bar set")
}
constructor() {
logger.info(" --> TrickyTarget() called");
}
}
// 使用 @Qualifier 配置的日志
INFO : TrickyTarget -  --> TrickyTarget() called
INFO : TrickyTarget -  --> Property fooOne set
INFO : TrickyTarget -  --> Property fooTwo set
INFO : TrickyTarget -  --> Property bar set
INFO : PickleAutowiringDemo - target: Created target? true
INFO : PickleAutowiringDemo - target: Injected bar? true
INFO : PickleAutowiringDemo - target: Injected fooOne? com.apress.prospring6.three.pickle.FooImplOne@7fd7a283[id=one:8efc8d31]
INFO : PickleAutowiringDemo - target: Injected fooTwo? com.apress.prospring6.three.pickle.FooImplTwo@22f59fa[id=two:a5d9ed75]
清单 3-66
使用 @Qualifier 的配置及结果输出
```

### 何时使用自动装配

在编写 Spring 应用程序时，自动装配是无法避免的；Spring 的核心理念就是你可以随心所欲地创建类，然后让 Spring 为你工作。问题不在于何时应该使用自动装配，而在于哪种类型的自动装配适合你应用程序的特定部分。你可能会倾向于使用 `byType`，直到你意识到在 `ApplicationContext` 中，每个类型只能有一个 bean——当你需要维护同一类型但配置不同的 bean 时，这一限制就会带来问题。同样的论点也适用于构造函数自动装配。在某些情况下，自动装配可以节省时间，但显式定义装配关系并不会花费太多额外精力，而且你能受益于显式语义、属性命名的完全灵活性以及对同一类型实例数量的完全控制。



## 总结

在本章中，我们广泛探讨了 Spring Core 和 IoC 的总体概念。我们通过示例展示了 IoC 的类型，并介绍了在应用程序中使用每种机制的优缺点。我们研究了 Spring 提供了哪些 IoC 机制，以及在应用程序中何时（以及何时不）使用它们。在探索 IoC 的过程中，我们介绍了 Spring 的 `BeanFactory`（它是 Spring IoC 能力的核心组件），以及 `ApplicationContext`（它扩展了 `BeanFactory` 并提供了额外的功能）。

对于 `ApplicationContext`，我们重点介绍了 `AnnotationConfigApplicationContext`，它允许通过注解对 Spring 进行外部配置。我们还讨论了另一种为 `ApplicationContext` 声明 DI 需求的方法，即使用 Kotlin 注解。

本章还向您介绍了 Spring IoC 功能集的基础知识，包括 Setter 注入、构造器注入、方法注入、自动装配和 Bean 继承。在配置的讨论中，我们演示了如何使用各种值（包括其他 Bean）来配置 Bean 属性。

本章只是浅尝辄止地介绍了 Spring 及其 IoC 容器。在下一章中，您将了解一些特定于 Spring 的 IoC 相关特性，并更详细地了解 Spring Core 中可用的其他功能。

脚注 1

