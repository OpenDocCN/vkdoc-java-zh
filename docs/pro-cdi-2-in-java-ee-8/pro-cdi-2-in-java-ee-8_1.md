# 1. CDI 的历史

本章将介绍上下文与依赖注入（以下简称 CDI）的历史，从其无可否认相关的先驱技术开始，一直讲到当前状况。我们将涵盖 JSF 和 EJB 等技术，以及 Gavin King 等关键人物。我们还将介绍 CDI 历史上一些最具决定性的时刻，你将了解到像 CDI 这样的技术是如何从表面上看似截然不同的各种想法中演变而来的。

需要注意的是，如此漫长而复杂的演变很难详尽描述，即使可能，完整的描述也需要一整本书的篇幅。因此，本章并非 CDI 的完整历史，而仅讨论最重要的事件。

## 传奇时代

每一段旅程都有第一步，CDI 的漫长旅程当然也不例外。就本书而言，这第一步始于 EJB 1.0。当然，EJB 本身也有其起源故事，涉及诸如 TopLink、微软的 ODBC 以及更早的 X/Open SQL CLI 等持久化技术，还有对象代理 CORBA 的起源故事，以及早期 AT&T Tuxedo 等事务服务器的历史——Tuxedo 本身也是 X/OPEN XA 标准的灵感来源之一。然而，这些故事不在本书的讨论范围之内。

EJB 1.0 始于 1997 年底，主要由 Sun、Oracle 和 IBM 合作开发，距离同年年初 Java 1.1 发布不久。Java 1.1 引入了一系列基础技术，为 EJB 奠定了基础，特别是 JavaBeans、JDBC、RMI，以及当时影响较小的反射（尽管反射后来可以说是所有“受管 Bean”模型中最基础的技术）。这些技术不仅在技术层面上至关重要，还改变了人们对 Java 应用领域的认知。Java 1.0 主要专注于小程序，即在 Web 浏览器内的小型盒子中执行的*小型*应用程序。随着 Java 1.1 中上述新技术的出现，特别是 EJB 1.0 的即将到来，将 Java 用于服务器端和商业计算的想法开始扎根。

人们对 EJB 寄予厚望。在其 CDI 历史地位中特别相关的是对*（可移植）组件模型*的承诺，以及随之而来的一个市场，开发者可以在其中购买可插入任何 EJB 容器的独立组件。

与 2017 和 2018 年的微服务热潮并非完全不同，在 1997 和 1998 年，*分布式对象*风靡一时。因此，EJB 1.0 中的组件（称为 EJB）被设计为仅支持远程访问也就不足为奇了。从某种意义上说，你可以将它们视为一种用于 IIOP 和 RMI 协议的 Servlet。有状态变体理论上对应于使用 HTTP 会话的 Servlet，而无状态变体理论上对应于不使用 HTTP 会话的 Servlet。

1998 年 3 月 24 日，在当年的 JavaOne 大会上，Sun 发布了 EJB 的初始 1.0 版本。市场反响最初相当热烈，EJB 被称为“一项有前途的新技术”。尽管承诺满满，但 EJB 1.0 规范的实现在很多方面仍有待改进。尤其麻烦的是，对于本应相对简单的事情，却需要许多移动部件，这给开发者带来了很高的认知负荷。简单来说，该规范要求开发者承担许多本应由容器/运行时自动完成的任务。当然，我们必须考虑到当时的时代精神和技术状况。例如，Java 还没有 JIT 编译器，XML 1.0 刚刚发布一个月，而 EJB 1.0 规范的开发时间也并不长（1997 年底到 1998 年初）。


上述提到的活动部件包括实际的 Bean 本身，接着是一个包含该 Bean 业务方法和删除该特定 Bean 的方法的“远程接口”，一个客户端可通过其查找和删除各种类型 Bean 的“本地接口”，每个 Bean 的部署描述符（它是 `javax.ejb.deployment.SessionDescriptor` 和 `javax.ejb.deployment.DeploymentDescriptor` 以及这些类的各种相关类的序列化实例 (!)），一个清单文件，以及作为最后侮辱的、可能存在的供应商特定配置文件。除此之外，通常还必须使用供应商特定的工具来生成配置文件和/或编译所列出的文件。作为该过程的一部分，还会创建一些额外的文件，例如远程通信所需的所谓骨架和存根。为整个应用程序完成所有这些工作已经很麻烦，但为每一个 Bean 都这样做，嗯，说得委婉点，可不太妙。

以下是一个早期 EJB 样子的示例：

```
public class MyServiceBean implements SessionBean {
SessionContext sessionContext;
public void setSessionContext(SessionContext sc) throws EJBException, RemoteException {
sessionContext = sc;
}
public void ejbCreate() throws EJBException, RemoteException {
}
void ejbActivate() throws EJBException, RemoteException {
}
void ejbPassivate() throws EJBException, RemoteException {
}
void ejbRemove() throws EJBException, RemoteException {
}
// Business method
public String sayHello(String name) {
return "Hello, " + name;
}
}
```

`setSessionContext` 上下文方法本质上充当了一种原始的依赖注入机制（尽管你不会真的这么称呼它），而 `activate`/`passivate`/`remove` 方法则是生命周期方法。

接下来要实现的是远程接口。

```
public interface MyServiceRemote extends EJBObject {
String sayHello(String name) throws RemoteException;
}
```

远程接口本质上是你与真实 Bean 通信所通过的代理，也是允许 EJB 运行时添加其魔力的关键。

你还需要在本地接口中添加一个方法（该接口也可以包含其他 Bean 的方法）。

```
public interface MyServiceHome extends EJBHome {
MyServiceRemote create() throws CreateException, RemoteException;
}
```

本地接口基本上是后来各种技术所使用的“Bean 管理器”的原始版本。

接下来要做的是在清单文件中添加一个条目，以将该 Bean 标记为 EJB。

```
Name: test/MyServiceBean.ser Enterprise-Bean: True
```

随后，必须提供一个部署描述符，代表以下部分定义的类（仅展示了 `SessionDescriptor` 及其超类，以及负责安全性的 `AccessControlEntry`）。这些类通常不会直接实现，而是由工具生成。

```
public class DeploymentDescriptor  implements Serializable {
protected int versionNumber;
public DeploymentDescriptor();
public AccessControlEntry[] getAccessControlEntries();
public AccessControlEntry getAccessControlEntries(int index);
public Name getBeanHomeName();
public ControlDescriptor[] getControlDescriptors();
public ControlDescriptor getControlDescriptors(int index);
public String getEnterpriseBeanClassName();
public Properties getEnvironmentProperties();
public String getHomeInterfaceClassName();
public boolean getReentrant();
public String getRemoteInterfaceClassName();
public boolean isReentrant();
public void setAccessControlEntries(AccessControlEntry values[]);
public void setAccessControlEntries(int i, AccessControlEntry v);
public void setBeanHomeName(Name value);
public void setControlDescriptors(ControlDescriptor value[]);
public void setControlDescriptors(int index, ControlDescriptor value);
public void setEnterpriseBeanClassName(String value);
public void setEnvironmentProperties(Properties value);
public void setHomeInterfaceClassName(String value);
public void setReentrant(boolean value);
public void setRemoteInterfaceClassName(String value);
}
public class SessionDescriptor extends DeploymentDescriptor {
public final static int STATEFUL_SESSION;
public final static int STATELESS_SESSION;
public SessionDescriptor();
public int getSessionTimeout();
public int getStateManagementType();
public void setSessionTimeout(int value);
public void setStateManagementType(int value);
}
public class AccessControlEntry  implements Serializable {
public AccessControlEntry();
public AccessControlEntry(Method method);
public AccessControlEntry(Method method, Identity identities[]);
public Identity[] getAllowedIdentities();
public Identity getAllowedIdentities(int index);
public Method getMethod();
public void setAllowedIdentities(Identity values[]);
public void setAllowedIdentities(int index, Identity value);
public void setMethod(Method value);
}
```

请注意，EJB 1.0 还包含一个颇具争议的概念，称为实体 Bean。在 Sun 公司内部，这个概念本身就存在争议，一些人在内部强烈抵制这个想法，但在 Sun 公司之外，争议更大。即使是当时尚未收购 Sun、作为独立公司的 Oracle，也明确拒绝实现它。实体 Bean 被过度设计，同时又缺少最基本的功能。因此，实体 Bean 在 EJB 1.0 中是可选的。

尽管 EJB 会话 Bean 给开发者带来了一些难以承受的负担，但其核心却包含了我们今天仍然珍视和使用的理念：基于声明的服务。在早期的 EJB 1.0 时代，这些服务包括用于事务划分和安全的固定服务。例如，使用前面展示的 `AccessControlEntry`，你可以声明一个 Bean 的 `String sayHello(String name)` 方法仅对某个名为 `foo` 的 `Identity`（它是 `Principal` 的前身类型）可访问。

1999 年 12 月 17 日，就在千禧年即将到来的前几天，Sun 公司推出了 EJB 1.1 规范。最重要的区别在于，基于 Java 的部署描述符已被新奇的基于 XML 的描述符所取代，并且对各地的开发者来说不幸的是，存在严重缺陷的实体 Bean 现在被强制作为 EJB 规范的一部分。



2001 年 6 月 7 日，在当年的 JavaOne 大会上，Sun 公司高级工程师兼架构师 Linda DeMichiel 和Ümit Yaçinalp 提交了 EJB 2.0 规范草案。其中包含 Yaçinalp 创建的一种基于 SQL 的新查询语言（EJB QL）、安全系统的两个新概念——*unchecked*（未检查）和*uncallable*（不可调用）方法（后更名为*excluded*方法），以及对 CDI 发展史而言最重要的——与现有*remote*（远程）接口相对的*local*（本地）接口概念。本地接口看似简单，但在当时代表了思维的重大转变。它摒弃了将 EJB Bean 视为类似 Servlet 或 Web 服务的思路，转而允许 EJB Bean 在单个 JVM 内本地使用，无需序列化方法参数和返回值，也无需经过网络协议栈。借助本地接口，EJB Bean 可以像普通类一样使用，同时仍保留 EJB Bean 支持的所有声明式服务。

2001 年 8 月 22 日，Sun 公司正式发布 EJB 2.0 规范，而就在五天前，另一份对 CDI 发展史至关重要的规范已悄然启动：JSF。

JSF 是一个基于组件的 UI/Web MVC 框架。初看之下，JSF 在上下文依赖注入框架中扮演重要角色似乎有些奇怪，但到了 2001 年 12 月，由 Sun 工程师 Amy Fowler 领导的 JSF 团队开始研究其独有的受管 Bean 模型（即组件模型）。JSF 团队开发的受管 Bean（最初称为 Object Manager）旨在用于所谓的支持 Bean（backing bean）。支持 Bean 是位于视图和模型之间的小型 Java 类。它们不应与*UIComponents*混淆，后者是完全不同类型的类，用于 JSF 的可视化组件（即小部件）。

始于 2001 年的受管 Bean 工作延续至 2002 年，并逐步加入了作用域、名称和依赖注入等概念。具体而言，作用域是 JSF 受管 Bean 系统的一项相当新颖的功能，它使这些 Bean 具备了上下文特性。这里的*上下文*（Contextual）意味着：一个类为`Foo`的“会话作用域”Bean，会导致同一 HTTP 会话内（但可能处于不同请求中）的所有客户端都能访问同一个 Bean 实例。而处于其他会话中的客户端则会获得`Foo`的不同实例。同样，请求作用域 Bean `Bar`会导致同一请求内的所有客户端获得`Bar`的同一实例，但其他请求中的客户端（即使处于同一会话）则会获得不同实例。

JSF 团队几乎从与 EJB 团队完全相反的方向来设计其组件模型。JSF 受管 Bean 是纯旧式 Java 对象（POJO），这意味着 Bean 无需实现任何强制接口，也无需继承任何基类。同时也没有单独的接口用于生成代理，因此完全不使用代理。JSF 受管 Bean 必须遵循 JavaBeans 约定，并且与几乎所有其他受管 Bean 系统一样，它们必须由运行时引导启动（即不能通过`new()`运算符创建，而必须向 JSF 请求或获取）。

以下是一个示例。首先创建 Bean 类。

```
public class MyServiceBean {
public String sayHello(String name) {
return "Hello, " + name;
}
}
```

其次，通过在`faces-config.xml`部署描述符中声明，将其设为受管 Bean。

```

myServiceBean
session
test.MyServiceBean

```

上述代码完整定义了 JSF 1.0 中的一个受管 Bean。依赖注入还通过 JSF 的另一项技术——表达式语言（Expression Language）实现。（表达式语言的概念源自一个 Apache 标签库，该库后来成为 JSP 中的 JSTL，随后又由 JSF 进一步增强。）在 JSF 中，依赖注入主要通过这种表达式语言完成。上述代码示例中显示的`managed-bean-name`定义了可在（子）表达式中使用的根名称。

以下是另一个将上述 Bean 注入另一个 Bean 的示例。首先定义被注入的 Bean。

```
public class MyBackingBean {
MyServiceBean myServiceBean;
// ... 其他属性和逻辑
public void setService(MyServiceBean myServiceBean) {
this.myServiceBean = myServiceBean;
}
}
```

然后声明要执行的实际注入。

```
myServiceBean
request
test.MyBackingBean

service
#{myServiceBean}

```

需要注意的是，JSF 受管 Bean 系统仅支持方法注入，从不支持字段注入。

## 加文·金登场

2001 年，众多开发者因前述 EJB 实体 Bean 的缺陷而倍感沮丧，其中一位就职于澳大利亚 Cirrus Technologies 公司的开发者尤为突出。这位开发者——加文·金（Gavin King）——很快意识到使用 EJB 实体 Bean 时效率低下，且无法运用常见的面向对象模式。但与大多数开发者不同，金决定正面应对问题，创建了自己的持久化框架。有趣的是，当时的加文甚至远非持久化或 SQL 方面的专家。



## 罗德·约翰逊登场

同样是在 2001 年，准确地说，是 2001 年 4 月 13 日，一位独自坐在伦敦书房里的开发者写下了以下几行代码：

```
package org.springframework.beans.factory;
/**
*
* @author Rod Johnson
* @author Juergen Hoeller
* @since 13 April 2001
*/
public interface BeanFactory {
```

这位开发者名叫**罗德·约翰逊**。当时他并不知道，自己刚刚写下的代码，最终将成为一场 Java 社区前所未见革命的起点。

2002 年 7 月 6 日，King 宣布发布 Hibernate 1.0，将其描述为“开源（LGPL）对象/关系持久化与查询服务”，并强调“Hibernate 拒绝使用代码生成或字节码处理，而是采用运行时反射。”Hibernate 后来成长为有史以来最流行的 Java 框架之一。

2002 年 10 月 23 日，在约翰逊写下`BeanFactory`关键代码一年半之后，他出版了可能是 Java EE 历史上最具影响力的著作：*《Expert One-on-One J2EE Design and Development》*。在这本书中，约翰逊以极其清晰的笔触，阐述了社区中酝酿了一年多的问题：Entity Bean 概念存在深层次缺陷。结论基本上归结为一条建议：永远不要使用 Entity Beans。约翰逊确实承认了 EJB 会话 Bean 所提供的声明式事务和声明式服务的强大之处。在 2003 年初接受 TheServerSide 采访时，他将这些服务比作一种受限形式的 AOP，但建议这些服务实际上应该是可组合的拦截器（按需点菜），而不是 EJB 那种“无论你是否想要，都得接受厨师准备的一切的品鉴菜单”。与 King 一样，约翰逊也反对代码生成，支持动态代理和（快速）反射，但他承认在 EJB 最初设计时这些技术尚不可用。约翰逊还强烈主张在大多数情况下使用非受检异常，仅在绝对必要时才使用受检异常。King 后来向约翰逊承认，在 Hibernate 中到处使用受检异常是一个错误，如果可能的话他会改变这一点。

2003 年 5 月 27 日，EJB 3.0 的工作启动。规范负责人是 Linda Demichiel 和 Mike Keith。这项新工作的目标是通过“从 EJB 开发者的角度降低其复杂性”来彻底简化 EJB。计划是利用 Java SE 5 中最新引入的注解，使过于冗长的部署描述符完全成为可选，并大力拥抱“异常配置”方法。会话 Bean 变成了 POJO（类似于 JSF 和 Spring），无需接口要求，与 JSF 和 Spring 不同的是，只需一个注解即可将其标记为会话 Bean。另一方面，Entity Beans 在如此基础的层面上被发现存在深层次缺陷，以至于改进它们毫无意义。取而代之的是，基于 Hibernate 和 TopLink 使用的简单模型对象引入了一种新型 Bean。规范联合负责人 Keith 后来在他的书*《Pro JPA 2》*中提到了这其中的巨大讽刺：“……Entity Beans 的首批实现之一，实际上是通过在 TopLink 映射对象之上添加一个额外的 Entity Bean 层来演示的。”

正如约翰逊所解释的，AOP 或许是 EJB 最强大的方面（这里并非双关）。当时 AOP 的典型实现一直是 AspectJ（在撰写本文时依然如此）。AspectJ 于 2001 年推出，是由传奇的施乐帕克研究中心开发的 Java 编程扩展，该中心引入了术语*面向切面编程*（AOP）。虽然可以说是最纯粹、最强大的 AOP 框架，但 AspectJ 本身就是一门语言，其本质始终带有某种学术和研究气息。这意味着它涉及一定程度的复杂性，使其不太适合那些通常不需要更强大功能的企业开发者。与旧的 EJB2 一样，当时的 AspectJ 需要单独的编译步骤和特殊的 IDE 支持。

因此，在 2002/2003 年左右，几个试图以牺牲部分功能为代价来简化体验的框架被开发出来，也就不足为奇了。

在 JBoss，工程师 Bill Burke 正忙于开发一个名为 JBoss AOP 的 AOP 框架。JBoss AOP 是 JBoss 的延续，在其早期的 JBoss 3 服务器中允许为 EJB 使用自定义拦截器。与 AspectJ 一样，JBoss AOP 也使用字节码操作（织入），尽管是在运行时进行的。然而，JBoss AOP 在该领域的早期贡献在于，它将交付企业服务（例如 EJB 中的事务和安全服务）作为其主要目标。而 AspectJ 和其他框架则更（学术性地）专注于 AOP 本身。

约翰逊（以及其他人）在 2003 年也很忙碌，他们正在构建 Spring 框架，该框架源于*《J2EE Design and Development》*中的经验教训和代码示例。在这个新框架的早期里程碑中，AOP 已经扮演了重要角色。Spring 没有使用字节码操作，而是使用了动态代理。这些是动态生成的类，与其所代理的类类型相同，允许框架在实际类被调用之前运行其 AOP 逻辑。实际的类仍然是一个普通的、未经修改的 Java 类。这种方法的优点在于侵入性较小，且无需访问类加载器，尽管缺点是不够强大（无法拦截字段或私有方法），并且程序员有时必须意识到通过代理调用方法与不使用代理调用方法之间的区别（例如，在实际类内部，一个方法调用同一类的另一个方法时）。



## 鲍勃·李登场

当时在 AT&T 担任技术架构师的鲍勃·李（自称“疯狂鲍勃”，曾于 2003 年 6 月 15 日为《*苦涩的 EJB*》一书做出贡献），也对现有或正在开发的 AOP 框架感到不满。因此，他启动了自己的框架：dynaop。dynaop 的一个重要目标是拥有优雅的设计。它像 Spring 一样使用代理，但不同于动态代理（需要接口），它使用的是类代理（无需接口）。Dynaop 还引入了一个当时具有革命性的理念：不拦截给定类型的所有实例，而是只拦截某些特定实例，例如从特定业务方法返回的实例。dynaop 的初始测试版于 2004 年 2 月 11 日发布。不久之后，李也开始尝试 DI/IoC，将那个时代的另一个 DI 引擎——*picocontainer*——与仍在开发中的 dynaop 结合起来。李主要在自己 AT&T 的项目（一个基于 Struts 的 Web 应用程序）中使用 dynaop。

JSF 1.0 包含了其轻量级托管 Bean 设施，最终于 2004 年 3 月 11 日发布。巧合的是，这仅比 Spring 1.0 的最终发布早了两周。这两个框架都拥有某种类似的、基于 POJO 的轻量级“Bean 容器”，但两者之间存在一个主要区别：Spring 的核心 Bean 容器是一个独立的容器，可以在任何地方使用，包括 Spring 自身的一部分。然而，JSF 的核心 Bean 容器是嵌入在 JSF 内部的，并未单独发布。它也仅面向 JSF 应用程序开发者。无论是 JSF、Java EE 中的其他规范，还是主要使用这些其他规范的应用程序开发者，都没有使用它。不过，当时有计划将这个核心 Bean 容器从 JSF 中分离出来，但这些计划从未实现。

2004 年 6 月 30 日，EJB 3.0 的首个规范草案发布。很明显，这并非 EJB 2.1 的简单演进，而本质上是一个全新的规范。

我们之前展示的 EJB 2 示例，在 EJB 3.0 草案中简化为：

```
@Stateless
public class MyServiceBean {
public String sayHello(String name) {
return "Hello, " + name;
}
}
```

可以使用注解以声明式方式请求容器提供的服务。例如，通过`@RolesAllowed`注解来保护方法的安全，如下所示：

```
@Stateless
public class MyServiceBean {
@RolesAllowd("foo")
public String sayHello(String name) {
return "Hello, " + name;
}
}
```

然而，事务服务是默认应用的（使用所谓的`REQUIRES`语义）。但如果不需要，也可以将其关闭。

有趣的是，在早期草案中，注入被提议通过一个名为`@Inject`的注解来实现。

```
@Stateless
public class MyServiceBean {
@Inject
EntityManager entityManager;
public String sayHello(String name) {
return "Hello, " + name;
}
}
```

此外，EJB 规范确实提供了一个称为*拦截器*的轻量级 AOP 解决方案。然而，EJB 在这里并没有“吃自己的狗粮”。现有的 AOP 服务，特别是安全性和事务性服务，并未被指定为基于这些新的拦截器。

EJB 3.0 中另一个主要的突出特性是一种全新的持久化类型，它基于 Hibernate 和 TopLink。这种新的持久化方法在 EJB 的一个新子规范中进行了规定：JPA。Gavin King 是这个子规范的主要贡献者，而 Hibernate 将成为 JPA 的主要实现之一。通过 JPA，King 也终于能够纠正他之前在 Hibernate 中使用受检异常的错误。JPA API 强烈强调非受检异常。

JSF 开发者 Jacob Hookom 在 2004 年 9 月 20 日看到 Bob Lee 的 dynaop 后，思考 AOP 是否对 JSF 真正有用，它可以在调用操作方法之前轻松验证输入。许多年后，这确实是 CDI 和 Bean Validation 规范允许 JSF 做的事情。同月，Struts Web 框架的提交者 James Holmes 也注意到了 dynaop，并在《Oracle Magazine》的一篇文章中对其进行了介绍。

大约在 2004 年 10 月 15 日，Bob Lee 接受了 Google 的工作邀请，离开了 AT&T。尽管李提到这一变动不会影响 dynaop 的工作，但在接下来的一个月里，dynaop 项目的提交量急剧下降。最后一次提交是在 2005 年 4 月 15 日。

2005 年 9 月 19 日，当时在 JBoss 工作的 Gavin King 宣布正在开发一个名为 Seam 的新项目。Seam 是一个“用于 Java EE 5 的应用程序框架”，其核心是一个新的组件模型。这个组件模型受到 JSF 托管 Bean 模型（使用有状态组件和作用域）的启发，但将像 EJB 3 一样基于注解。Seam 的一个核心方面是，Bean 可以通过代理（如 EJB）进行注入，该代理在调用时解析对 Bean 的调用。这允许将较小的作用域（如请求）注入到较大的作用域（如应用程序）中，这是 JSF 所不允许的。Seam 还强调双向注入（注入和传出注入），这允许 Bean 替换其作用域内的对象，而无需知道该作用域具体是什么（这有点类似于按引用调用的语义，被调用的代码可以为变量分配一个新实例）。

```
@Stateful
@Scope(APPLICATION)
public class MyServiceBean {
@In
EntityManager entityManager;
@In @Out
PriceList priceList;
public void updatePrices() {
priceList = entityManager.merge(priceList);
}
}
```

在前面的代码中，`priceList`可以是请求作用域、会话作用域或任何其他作用域。代码为其分配一个新值，该值将替换其作用域中的旧值（Seam 称之为“上下文”）。下一个 Bean 在其注入的`PriceList`代理上调用方法时，会看到该调用指向新实例。除了 JSF 的请求/会话/应用程序作用域之外，Seam 还有几个额外的作用域/上下文，其中包括所谓的会话上下文。

Bob Lee 于 2006 年 1 月 29 日发表了一篇关于 Spring 的批评性文章，题为“我不理解 Spring”。在文章中，李批评了 Spring“对 J2EE 嗤之以鼻”的文化以及其庞大的 API 表面。李认为 J2EE 的表面并没有那么大，因为在 J2EE 中，API 和实现是严格分离的。李还指出了 Spring 将大量“配置”外部化到 XML 文件中的做法。根据李的说法，这种“配置”实际上是“代码”，只是用 XML 编写的代码，因此 Spring 不接触任何代码的卖点并不完全成立。李还批评了使用基于字符串的 ID 来标识（托管）Bean 的做法，以及需要在代码和 XML 之间保持这些 ID 同步的必要性。李想知道为什么仅仅使用（Java）类型还不够。最后，李想知道为什么 Spring 还没有采用泛型（引入泛型的 Java SE 5 于 2004 年 9 月 30 日发布）。就像 Johnson 在 2002 年底的著作一样，李文章中的观察结果被证明对 Java 企业领域产生了深远的影响。

2006 年 5 月 11 日，EJB 3.0 规范发布。不幸的是，EJB 团队以前的一种倾向又悄悄回到了规范中：对工具的依赖。会话 Bean 的无接口要求再次被移除，被委托为“可以使用工具来实现这一点”。当然，从来没有人真正使用过这样的工具。此外，`@Inject`注解已被移除，取而代之的是，用于注入其他 EJB Bean 的`@EJB`和用于注入实体管理器的`@PersistenceContext`等注解。

尽管不得不使用（应用程序定义的）接口这一挫折，EJB 3.0 仍然是第一个（如果不是第一个）完全拥抱注解的组件框架之一，而与此同时，对于 JSF（到 2006 年已发展到 1.2 版本）来说，注解却奇怪地仍然不见踪影。



## CDI 的概念

早在 Seam 1.0 正式发布之前，2006 年 5 月 17 日，Gavin King 就宣布了由 JBoss 发起的 Web Beans JSR（JSR 299），该规范吸收了 Seam 和 Guice 的部分理念并将其标准化。Web Beans 规范得到了 Sun、Oracle、Borland 等公司的支持，其具体目标是统一 JSF 和 EJB 组件模型。其理念是，EJB3 会话 Bean 可以像 JSF 托管 Bean 一样工作（通过命名和设置作用域）。Web Beans 也打算独立使用，但不会重复 EJB 提供的功能（特别是声明式事务和安全性）。

2006 年 6 月 13 日，也就是 EJB 3.0 发布一个月后，Seam 1.0 发布了。Seam 的采用率起初增长缓慢，但在第二年，其下载量和用户数确实超过了 Hibernate 在第二年的表现。

就在 Bob Lee 发表批评 Spring 的文章后不到八个月，他于 2006 年 8 月 25 日首次提交了一个名为 Guice 的新 DI 框架。这次提交是在 `google-guice.googlecode.com` 上完成的，包含 `com.google.inject` 包中的 26 个 Java 文件（不包括测试文件），其中就包含了现在著名且广为人知的 `@Inject` 注解的第一个版本。

```
/**
* 注解那些需要注入其值的成员和参数。
*
* @author crazybob@google.com (Bob Lee)
*/
@Target({METHOD, CONSTRUCTOR, FIELD, PARAMETER})
@Retention(RUNTIME)
public @interface Inject {
/**
* 依赖名称。默认为 {@link Container#DEFAULT_NAME}。
*/
String value() default DEFAULT_NAME;
/**
* 注入是否为必需。仅适用于方法和字段（不适用于构造函数或参数）。
*/
boolean required() default true;
}
```

包文档对这个非常早期、尚未公开的 Guice 版本描述如下：

```
/**
* Guice（发音为 "juice"）。一个轻量级的依赖注入容器。
* 特性包括：
*
* 
*   构造函数、方法和字段注入
*   静态方法和字段注入
*   循环引用支持（如果你依赖接口，则包括构造函数）
*   高性能
*   仅外部化需要外部化的内容
* 
*/
```

尽管 Guice 尚未公开，但 Atlassian 的技术负责人 Don Brown 于 2006 年 11 月 13 日将 Guice DI 容器的早期版本添加到了 XWork 项目中，并将类重新定位到 `com.opensymphony.xwork2.inject` 包中。声明的理由是“用于连接关键部分和常量”。XWork 本身是一个“通用命令框架”，因使用拦截器、IoC 和命令模式而闻名。它是从 WebWork 中分离出来的，WebWork 是一个早期的“拉式分层 MVC 框架”（始于 2000 年 11 月 18 日）。当时，WebWork（以及 XWork）即将合并到 Struts 中。

随着围绕 DI 框架的活动如火如荼地展开，Java EE 的知识产权所有者 Sun Microsystems 并未坐视不管。2007 年 3 月 2 日，Java.net 上创建了一个新项目，其首次项目设置提交信息为：“一个 100K 的模块系统内核。” 将近两个月后，即 2007 年 4 月 23 日，已是传奇人物的 Sun 开发者 Kohsuke Kawaguchi（他启动了 Hudson 项目，该项目后来分支成为现在广为人知的 Jenkins 项目）提交了 84 个文件，并附有以下评论：

> *“从* [`https://www.dev.java.net/svn/modsys/tags/move-to-hk2`](https://www.dev.java.net/svn/modsys/tags/move-to-hk2) *修订版 168 迁移工作区”。*

这些文件的版权是 2006 年，因此 Kawaguchi 和另一位 Sun 开发者 Jerome Dochez 可能已经为此工作了一段时间。`org.jvnet.hk2.annotations` 包中同样包含了一个 `@Inject` 注解。

```
/**
* 用于定义组件所需资源的注解。
* 运行时将注入所有使用 @Requires 注解的实例变量以及 setter 方法。
*
* @author Jerome Dochez
*/
@Retention(RUNTIME)
@Target({METHOD,FIELD})
public @interface Inject {
/**
* 返回所需资源的名称
* @return 所需资源的名称
*/
public String name() default "";
/**
* 指示即使要注入的组件不存在也不是错误。
*/
public boolean optional() default false;
}
```

有趣的是，初始代码提交还包含了 `PostConstruct` 和 `PreDestroy` 接口，它们后来成为 CDI Bean 的关键注解（尽管 CDI 本身并未定义它们）。

```
/**
* 实现此接口的类表示希望在实例被创建且组件即将投入使用
* 时收到通知。
*
* @author Jerome Dochez
*/
public interface PostConstruct {
/**
* 组件已注入所有依赖项，子系统将使其投入使用。
*
*/
public void postConstruct();
}
```

此外，初始提交还包含一对 `@Contract/@Service` 注解，用于声明和实现组件，以及一些与类似 OSGi 的“模块”相关的类。虽然 HK2 的某些方面与 Guice 相似，但 Kawaguchi 和 Dochez 开发 HK2 主要是为了驱动名为 GlassFish 的应用服务器的内部机制，而 Guice 则面向应用程序开发者。

回到 Seam 阵营，在 CodeRyte 工作的资深 Java 工程师和 OSS 倡导者 Dan Allen 于 2007 年 4 月 12 日在 IBM 的 DeveloperWorks 上发表了颇具影响力的系列文章“无缝 JSF”。该系列文章清晰地解释了 Seam 是什么，以及它如何帮助构建更优雅的 JSF（Java EE）应用程序。虽然源自 Seam 的 Web Beans 仍在开发中，但 Seam 本身开始逐渐变得越来越流行。

在 Seam 和 Web Beans 都处于不同开发阶段的同时，Bob Lee 也没有闲着。他的依赖注入框架 Guice 1.0 于 2007 年 3 月 8 日正式发布并公开。当时，Google 已经将其用于生产环境好几个月了，这意味着其技术（内部）发布可能在 2006 年底的某个时候，大概是 11 月 13 日左右，也就是 XWork/WebWork/Struts 整合 Guice 的时候。

在 Java EE 6 的 JSR 评审投票期间，IBM 于 2007 年 7 月 16 日评论称，它担心 Web Beans 引入了一种新的组件模型。

> *“我们开始担心 JSR-299 似乎可能超出其既定章程（整合 JSF 和 EJB 组件）的方向发展，并认为继续朝这个方向努力可能使其有理由被从 Java EE 6 中移除。我们不认为我们的客户会觉得采用一个又增加了一个组件模型定义的 Java EE 6 平台是件容易的事。”*

2007 年 9 月 22 日，Gavin King 首次展示了 Web Beans 的预览。创建新的 Java EE 规范时一个常见的主题是，有人提议该规范不应仅用于 Java EE。JSR 299 也不例外。具体来说，Lee 主张 JSR 299 应该能在 Java EE 之外使用。然而，作为规范负责人的 King 明确将 JSR 299 的目标设定为 Java EE，但确实为在未来的修订版中添加对 Java EE 之外的支持留有余地。

首次预览使用的语法如下所示：

```
@Component
@ApplicationScoped
public class MyServiceBean {
@In @UserDatabase
EntityManager entityManager;
@In @Available
PriceList priceList;
PriceList lastSaved;
public void savePrices() {
entityManager.persist(priceList);
lastUpdated = lastSaved;
}
@Resolves @LastSaved
PriceList getLastSavedPriceList() {
return lastSaved;
}
}
```



在这个例子中，Seam 与 `@In` 注解的影响清晰可见。然而，当 Seam 试图通过使用另一个注解来指示作用域以区别于 JSF 时，Web Beans 却回归了 JSF 的原始方式，使用了 `@ApplicationScoped`（但位于不同的包中）。`@In` 之后的注解被称为*绑定类型*/*绑定注解*，用于指示特定的类型种类。例如，之前的代码区分了可用的价格列表和最后保存的价格列表。两者具有相同的 Java 类型，但绑定类型不同。

最后，预览部分谈到了组件类型。默认的组件类型是前面展示的 `@Component`，但也可以创建额外的类型（例如 `@MockComponent`）。组件类型具有优先级，并且这些组件类型可以全局启用/禁用。Web Beans 只会扫描带有这种组件类型注解的类。

后来的草案移除了 `@In` 注解，仅要求一个绑定注解，并使用 `@Current` 作为默认值。这实际上使 `@Current` 成为了*注入注解*，前提是大多数 bean 不需要绑定类型。组件类型被重命名为部署类型，当省略时默认为 `@Production`。`@Resolves` 被重命名为 `@Produces`，等等。

与此同时，在加利福尼亚，有人密切关注着这些草案。一家名为 Caucho 的公司的联合创始人 Scott Ferguson 创建了 Resin 服务器。Resin 最初是一个 Servlet 容器（类似于 Tomcat），后来被整合到其他几种 Java EE 技术中。Ferguson 于 2007 年 11 月 5 日在 Resin 3.1 的 `com.caucho.webbeans` 包中，为 Web Beans 的独立实现进行了首次提交。该实现当时尚未命名。Ferguson 会跟踪正在开发的 Web Beans 草案，并相应地更新 Resin 中的 Web Beans 实现。

当 Resin 3.1.5 最终于 2008 年 2 月 27 日发布时，其 Web Beans 实现已完全实现了当时的 Web Beans 草案。

然而，Ferguson 并非唯一一个致力于自己独立的 JSR 299 实现的人。开发者 Gurkan Erdogdu 在 SourceForge 上做同样的事情已有一段时间，并于 2008 年 10 月 3 日向 Apache 组织提议将 OpenWebBeans 项目作为孵化器。

同月下旬，即 2008 年 10 月 30 日，Johson 以执行委员会成员的身份加入 JCP，这预示着某种重大事件的发生。

尽管 Erdogdu 曾在其博客中提及启动 OpenWebBeans 项目，但 Web Beans 社区的人们直到一个月后，当公开评审草案在 TheServerSide 上被讨论时，才真正了解到这个项目的存在。

Erdogdu 立即认识到 Web Beans 的潜力，并表示“Web Beans 定义了未来 Java EE 的‘组件模型’”，但当时 Java EE 倡导者 Reza Rahman 认为这是一个问题，因为它削弱了 EJB EG 为使 EJB 成为人们乐于使用的技术所做的工作。Rahman 主张投入更多精力使 Web Beans 成为一种补充技术，而不是 EJB 的替代品。Gavin King 随后提到，Web Beans 现在主要是一组服务，而非主要是一个组件模型。它提供了可应用于有状态会话 Bean 的作用域/上下文服务，提供了超越简单 *@EJB* 注入注解的会话 Bean 注入服务，此外还有用于抛出和观察事件的服务，这些服务同样可以被会话 Bean 使用。这些服务基本上是通用的，可以被所有其他组件类型按需选用。

特别有趣的是，当时正在为 Java EE 6 开发的新 REST 规范（称为 JAX-RS）一直在开发自己的组件模型和依赖注入系统，但到 2008 年底，人们相信它很快会使用 Web Beans 提供的注入服务。

虽然在 2008 年和 2009 年初，EJB EG 对任何哪怕最微弱的、关于现在或将来可能被 Web Beans 取代的暗示都进行了强烈抵制，但 JSF EG 对此想法却远没有那么担忧。事实上，JSF 2 EG 中的 JBoss 代表 Pete Muir（他曾在为 JBoss 工作之前，就职于英国一家名为 Splendid.co.uk 的公司，并为 Seam 做出过贡献）在当时宣布，JSF 托管 Bean 将被弃用，转而支持 Web Beans。

在 2009 年的第一个月（2009 年 1 月 26 日），JSR 299 的名称从 Web Beans 改为“Java 的上下文与依赖注入”，简称 CDI。这个名称变更已经酝酿了相当长一段时间，因为许多人和团体都表示对 Web Beans 这个名称不满意，特别是因为它（已经）不能完全覆盖问题领域。CDI 这个名称需要一段时间才能流行起来，尤其是在最初几个月，人们常常称其为 JCDI 而非 CDI。

与此同时，EJB EG 对最终注定要取代它的规范仍然持怀疑态度。具体来说，IBM 为 EJB 辩护，并于 2009 年 2 月 9 日重申了其一年半前提出的担忧，在 JSR 299 的公开评审投票中留下了以下评论：

> *“IBM 对在 EE 平台中引入另一个竞争性组件模型持严重保留意见，而本 JSR 本应专注于将技术集成到平台中。除了由此造成的重复和混乱之外，该规范已扩展到涵盖大量技术领域，这在一个单一规范中完全定义是相当雄心勃勃的。然而，我们承认规范负责人已在处理反馈方面取得了实质性进展，因此，我们愿意投赞成票，允许规范继续推进，前提是规范负责人将继续处理反馈和关切。”*

但 IBM 和 EJB EG 并非唯一反对 Web Beans 的势力。2009 年 5 月 26 日，曾为 Web Beans/JSR 299 做出贡献的 Bob Lee 和 Rod Johnson 突然启动了他们自己的竞争性 JSR 330，官方名称为“Java 的依赖注入”。不出所料，这引起了相当大的争议，但该 JSR 最终还是被接受了。不过，冲突的可能性立即被认识到，Sun 发表了以下评论：

> *“我们很高兴看到 Spring 和 Guice 社区联合起来为 Java 平台标准化依赖注入，我们支持这一努力。*

> *依赖注入具有普遍性，并带来一些强大的网络效应，在缺乏全面标准的情况下，这自然会导致有害的碎片化。*

> *在这方面，我们要求本 JSR 和 JSR-299 协调他们的工作，以便能够共同为 SE 和 EE 平台提供一个单一、一致且全面的依赖注入标准。此类协调必须在本 JSR 的公开评审之前进行。”*

通过 Gavin King 领导 JSR 299 的 Red Hat 则更为担忧，并发表了以下评论：

> *“Red Hat 对本 JSR 的价值深表怀疑，因为它设想存在一个不属于 SE 或 EE 平台的独立容器，其语义和编程模型定义得如此不明确，以至于除了最琐碎的应用程序之外，应用程序在不同容器之间的可移植性将是不可能的。这违背了 Java‘一次编写，到处运行’的传统。”*



> *“然而，我们认识到社区对该提案有一定支持，因此我们暂缓形成最终意见，等待专家组发布公开草案。如果该 JSR 能与 JSR-299（该规范确实定义了可移植的依赖注入模型）达成某种程度的一致，我们将更有可能投票批准该 JSR。红帽在此承诺将尽己所能促成这一结果。”*

Gavin King 提到，再搞一套与 JSR 299 功能基本相同的注解将是一个巨大的错误。他在博客中做了如下对比：

*   `@Qualifier` 等同于 `@BindingType`。
*   `@Scope` 等同于 `@ScopeType`。
*   `@Singleton` 等同于 `@ApplicationScoped`。
*   `@Named` 只是一个内置的绑定类型。
*   `Provider<X>` 接口等同于 `Instance<X>`。

Bob Lee 为新的 JSR 辩护，并指责 King 在针对 Java EE 6 设计 Web Beans 时只考虑了 Java EE。Lee 特别提到，使用 Web Beans 将迫使人们使用 EJB Lite，暗示依赖 EJB 是一件非常糟糕的事情。至此，Web Beans 发现自己陷入了两难境地：IBM 认为 Web Beans 对 EJB 的依赖不够，而对 Lee 来说，潜在的 EJB 依赖似乎是一个致命问题。

经过大量辩论，冲突最终得以解决。2009 年 8 月 5 日，宣布 CDI 将采用 JSR 330 定义的注解和术语。JSR 330 最核心的注解是 `@Inject`，JSR 330 也因此得名“AtInject”。具有讽刺意味的是，`@Inject` 恰恰也是 EJB 3 早期草案最初使用的注解。

此时，Lee 已经大幅减少了对 Guice 项目的提交，该项目主要由其他人进行演进。Lee 于 2009 年 9 月 30 日向 Guice 提交了倒数第二次提交，内容是关于 JSR 330 的兼容性。一年多后的 2010 年 12 月 15 日，Lee 做了最后一次一次性提交，之后便永久离开了 Guice 领域。

2009 年 10 月 14 日，JSR 330 的最终版本发布。从创建到发布，该 JSR 仅用了五个月时间，这绝对是一个纪录。然而，该 JSR 仍然存在一些争议，被指责为只是对 Lee 和 Johnson 已经达成一致的内容进行橡皮图章式的批准，并且内容过于单薄，缺乏实际用途。后者意味着，尽管 CDI、Guice、Spring 以及后来的 HK2（Jersey）都使用了 `@Inject` 注解，但代码在这些框架之间完全不可移植，唯一实际的结果是，不同代码片段之间会发生冲突——它们都使用 `@Inject`，但本应由不同的注入提供者处理。

就在 JSR 330 最终版本发布几天后，即 2009 年 10 月 18 日，Gurkan Erdogdu 在克服了“奇怪错误”的困扰后，成功让他的 OpenWebBeans 项目通过了 JSR 330 TCK。

与此同时，Scott Ferguson 和当时在 Caucho 为 Ferguson 工作的 Reza Rahman，于 2009 年 11 月 3 日发表了另一系列极具影响力的文章的第一部分：“Java EE 6 中的依赖注入”。在这篇文章中，Ferguson 和 Rahman 不仅详细解释了 CDI，还向世界介绍了 CanDI 实现，以及它如何构成 Resin 服务器的核心。Ferguson 和 Rahman 还提到考虑引入一个基于 CDI 的 `@Transactional` 注解。

2009 年 11 月 30 日，IBM 在最终批准投票中投了反对票，这并不完全出人意料。IBM 给出的理由如下：

> *“目前 JSR 330 和 JSR 299 应用程序之间缺乏可移植性，这将给社区带来巨大困难。使用基于 JSR 330 注入注解和专有注入机制的应用程序开发人员，在利用与 JSF 关联的 JSR 299 上下文时，将遇到集成问题。在这些规范的开发过程中，对这些细节的关注不足，这将给整个 Java EE 平台带来负面影响。尽管 IBM 此前投票支持了 JSR 299 和 330，并明确期望‘SE/EE 注入编程模型必须统一为一个单一的可扩展编程模型，该模型为 SE 定义核心功能集，并通过 EE 功能进行扩展’，但这一目标尚未实现。IBM 将继续支持两个专家组，以开发一个单一、集成且可扩展的注入编程模型。”*

幸运的是，IBM 是唯一投票反对 CDI 的执行委员会成员。因此，不到两周后，即 2009 年 12 月 10 日，CDI 1.0 的最终版本与完整的 Java EE 6 平台最终版本一同发布。

Java EE 6 总体反响非常好。作为第二个专注于开发者友好性的 Java EE 版本，Sun 显然能够保持这一方向。随着 JAX-RS、CDI、Bean Validation、JASPIC 和 `@DataSourceDefinition` 注解的引入，该平台迈出了重大步伐。

不幸的是，并非一切顺利。也许是因为 CDI 1.0 最终定稿太晚，其他规范并未真正利用它。

JSF 2.0 专家组似乎倾向于弃用自己的托管 Bean 系统，但仅添加了一条通知，说明 CDI 可能会在未来取代它，并未正式弃用。更糟糕的是，JSF 2.0 引入了诸如 `@ManagedBean` 之类的注解，使其看起来对其旧的托管 Bean 系统进行了大量更新（实际上，这些新注解只是在现有 XML 之上的一层薄薄的封装）。尽管如此，CDI Bean 和 `@ManagedBean` 注解的同时引入，被视为原本广受好评的 JSF 2.0 中绝对的败笔之一。

同样，JAX-RS 专家组做得更糟，并且（事后看来）相当不必要地引入了自己的 DI（使用 `@Context`）和 Bean 模型。由于 JAX-RS 1.0 很早就最终定稿，因此有足够的时间进行维护版本发布，该版本说明所谓的 JAX-RS 资源也可以是“CDI 风格的托管 Bean”，但此时木已成舟。

因此，尽管 Java EE 6 的目标是通过 CDI 统一各种 Bean 模型和 DI 系统，但最终却额外增加了一堆。也许并不奇怪，这后来给开发者带来了相当大的困惑，但出于某种原因，当时这并未被视为一个非常大的问题。

Gavin King 创立了 Hibernate 和 Seam 项目，这两个项目都极大地影响了 Java EE 规范 JPA 和 CDI。显然，他认为自己在 Java（EE）领域的工作已经完成，仅仅两个月后，他就有效地离开了 Java 领域，开始开发自己的编程语言 Ceylon（[`https://ceylon-lang.org`](https://ceylon-lang.org)）。（在撰写本文时，尽管 Gavin 又开始涉足 JPA，但他在九年后仍在从事 Ceylon 的工作。）也许与 King 想法相同，Bob Lee 也同时离开了 Google，并于 2010 年 1 月加入 Square，基本上也离开了（开源）Java 领域。仿佛他们之间有什么秘密协议，我们的第三位 DI 先驱 Rod Johnson，在 2009 年 8 月将他的 SpringSource 公司出售给 VMware 后，也一度离开了社区和商业领域（Johnson 最终于 2012 年离开 VMware）。如果 Kohsuke Kawaguchi 不离开，这场 DI 大逃亡就不算完整，果不其然，他确实离开了。2010 年 4 月 5 日，Kawaguchi 宣布这是他在 Sun/Oracle 的最后一天。他转而创办了自己的公司，支持 Hudson（后来的 Jenkins）。



## 下一代

在 2010 年 3 月 8 日发布的《Java EE 6 中的依赖注入》第三部分中，Scott Ferguson 和 Reza Rahman 阐述了他们的愿景：将 EJB 重新定义为带有附加服务的[CDI]托管 Bean。主要由 Rahman 担任负责人的 EJB 3.1 Lite 容器，将作为这一理念的初始实现。

随着 CDI 1.0 刚刚发布，Ferguson 显然迫不及待地想要在此基础上引入新的创新。2010 年 3 月 10 日，他确实在 CanDI 中引入了前面提到的`@TransactionScoped`作用域/上下文。这是一个基于当前（JTA/XA）事务的作用域。Ferguson 并未止步于此，2010 年 5 月 29 日，他还引入了一个`@ThreadScoped`作用域/上下文。

Pete Muir 于 2010 年 12 月 14 日宣布，红帽将很快提议 CDI 1.1，并由 Muir 接替 Gavin King 担任规范负责人。当时人们认为 CDI 已基本完成，因此 CDI 1.1 将只是一个小的版本更新。

2011 年 3 月 1 日，Java EE 7 的评审投票开始。计划是将 Lee 的 AtInject 规范更新到 1.1，并提供一个用于配置注入器的 API。但由于 Lee 已经离开，这一计划从未真正获得机会，因此最终未能实现。

在此版本中，各个 EE API 首次有机会基于 CDI 构建，或在其之上构建新功能。这在 Java EE 7 JSR 中有所体现：

> *“我们还期望进一步细化和扩展托管 Bean 模型，以尽可能消除托管 Bean、EJB、Servlet、JSF、CDI 和 JAX-RS 之间的不一致性。”*

在 Mammatus 工作的开发者 Richard（又名 Rick）Hightower，曾参与 JCache JSR 的工作，并合著过一本关于 Caucho Resin 的书籍。他看到了 CDI 的巨大潜力。然而，早期的示例并不完全符合他的喜好，因此他在 2011 年 3 月 7 日创立了 CDI Advocate 计划，随后在同月（3 月 28 日）宣布成立 CDI Source 组织（CDI Advocate 的新名称）。该组织的目标是通过提供教程和支持性开源项目来填补 CDI 的空白，该项目的代码提交始于当月 19 日，并在宣布之前一直保持高度活跃。两天后，Hightower 宣布他的项目获得了 Caucho 的支持，这意味着将承担托管费用并提供一些工程资源。次月，Hightower 在 DZone 上发布了一个广受欢迎的两部分 CDI 教程。

当月月底，即 2011 年 4 月 30 日，Hightower 在官方 CDI 跟踪器上为事务作用域创建了一个问题，其灵感来源于 Ferguson 前一年的作用域。几个月前，即 2011 年 1 月 10 日，Pete Muir 在该 CDI 跟踪器上创建了一个类似的问题，以支持使方法或 Bean 具有事务性的注解。

然而，这潜在地令人担忧，因为它代表了关于 CDI 应该是什么的分歧：是 Java EE 中大多数其他技术的门面（就像 EJB 曾经被计划的那样），还是其他规范应基于的核心技术。在 CDI 中包含`@Transactional`意味着 CDI 将成为 JTA 的门面，并朝着成为下一个 EJB 的方向发展。

Apache 的 Mark Struberg 迅速对此作出回应，并在该问题中建议 CDI 应只关注基础功能，因此不应包含那些也可以在可移植扩展（可能由其他规范实现）中完成的内容。

CDI 1.1 专家组成员（特别是 Adam Bien 和 Mark Struberg）之间进行了一场相关讨论，内容涉及是为 CDI 重新实现现有功能时重用现有注解，还是引入新注解。Adam Bien 主张重用现有注解，但 Struberg 不同意。

2011 年 6 月 17 日，Reza Rahman 在新的官方 EJB 跟踪器上创建了多个问题，涉及将服务与 EJB 组件模型解耦，这代表了 Rahman 在其早期文章中概述的愿景。用他的话来说：

> *“[...] 将诸如@schedule 之类的 EJB 服务与[EJB]组件模型解耦，是为了朝着消除 Java EE 中一次性组件模型的方向发展，转而围绕托管 Bean/CDI 进行统一。”*

2011 年 8 月，Rahman 悄然离开了 Caucho。随着他的离开，CanDI 的愿景似乎也随之消失，因为此后 CanDI 陷入了诡异的沉寂。事实上，此后 Caucho 再也没有发布过关于其自身 CDI 实现的新公告，CanDI 也没有实现 CDI 1.1 及更高版本，尽管偶尔会修复 1.0 实现中的一些错误（例如本书作者于 2013 年 9 月报告的泛型生产者方法的一个错误）。到那时，备受讨论、由 Caucho 支持的 CDI Source 项目也已停止。事实上，自 3 月份的官方公告之后，CDI Source 项目就没有再进行过任何代码提交。

大约在同一时间，Seam 开发人员在加拿大多伦多的一次 JBoss 会议上会面，讨论 Seam 的未来。一个问题出现了，那就是没有人真正知道 Seam 到底是什么了。Seam 3 已经与 Seam 1 和 2 大相径庭；它不再是完全集成的框架栈，而是被重构为一组质量参差不齐的 CDI 扩展。在多伦多会议上，决定进一步拆解 Seam，不仅将其重构为这些 CDI 扩展，还要将这些 CDI 扩展分散到世界各地（从 Seam 项目中分离出来，进入其他项目）。

2011 年 9 月 27 日，红帽开发者兼 Seam 项目负责人 Shane Bryzak 在一篇名为“Seam 到底发生了什么？”的文章中向世界宣布了这一决定，并于 9 月 30 日发布了后续文章“Seam.Next 更新”。另一位红帽和 Seam 开发者 Lincoln Baxter III 迅速提到“Seam 不会消失”。

社区自然对此感到不满。其中一位是法国开发者 Antoine Sabot-Durand，他是 Seam 社交模块的负责人。2011 年 9 月 30 日，他写了一封致红帽的公开信，敦促他们不要通过摧毁 Seam 3 来让 CDI 变成“Betamax”。

2011 年 10 月 4 日，Pete Muir 介绍了 CDI 1.1 的几个计划项目。其中包含一个名为“ServiceHandler”的通用概念，它允许表达类似以下内容，其中服务的实际定义将来自一个相当抽象和通用的处理程序类：

```
@QueryService
interface Users {
@Query("select u from User u")
public List getAllUsers();
}
```

随着 CDI 1.1 稳步推进，Shane Bryzak 于 2011 年 11 月 30 日宣布 Seam 3 将有效解散，Seam 团队将与 Apache CODI 团队以及 Rick Hightower 短命的 CDI Source 一起，在 Apache 创建一个名为 DeltaSpike 的新项目。Mark Struberg 是这个新项目的坚定支持者，并宣称与始终与 JBoss 有联系的 Seam 相比，这是一个更加开放的项目。

Pete Muir 和 Lincoln Baxter III 都提到，真正的 Seam.next（一个集成的端到端全栈框架）将很快宣布。然而，这一宣布从未到来，2013 年 3 月，Seam 主网站更新了文字：“红帽已暂停 Seam 3 的积极开发。”

Bryzak 宣布后不久，DeltaSpike 的开发就开始了。2011 年 12 月 22 日，Apache 的 Gerhard Petracek 进行了首次提交，随后 Mark Struberg 也进行了提交。

几天后，即 2011 年 12 月 28 日，Petracek 添加了包含单个标记接口的`org.apache.deltaspike.core.api.config`包。当天晚些时候，Struberg 添加了`ConfigSource`接口和实现逻辑，并附有以下注释：



> *“这种新逻辑将允许对可配置值进行可插拔、有序排序的实现。”*

然而，在接下来的一年里，一种模式逐渐显现。DeltaSpike 的大部分提交都是由 Struberg 或 Petracek 完成的。前 Seam 负责人 Bryzak 只是偶尔提交，而红帽方面只有 Jason Porter（来自 Seam Catch）进行了相当数量的提交，尽管数量仍远少于 Struberg 或 Petracek。其意义在于，尽管 DeltaSpike 被宣布为一个代表 Seam、CODI 和 CDI Source 合并的多厂商项目，并且明确作为 Seam 的延续（在 InfoQ 关于 DeltaSpike 的采访中，甚至使用了“Seam 4”这个术语），但实际上，该项目主要由现有的 Apache（CODI）成员负责，红帽人员的参与程度要低得多。

2013 年 5 月，自项目成立之初就参与 DeltaSpike 的 Antoine Sabot-Durand 加入了红帽，这为红帽提供了更多为 DeltaSpike 做贡献的机会。然而，最终 Sabot-Durand 只向 DeltaSpike 提交了一次小小的代码提交。

回到 Java EE 阵营，人们并未止步不前。尽管在考虑不周的“云支持”方面存在一些问题，但包含 CDI 1.1 的 Java EE 7 最终于 2013 年 5 月 28 日发布。

此前一直强烈抵制被 CDI 取代的 EJB EG 终于下定决心，与 JTA EG 合作，推出了 EJB 核心功能（声明式事务服务）的 CDI/拦截器版本。这些服务不再是 EJB Bean 的默认特性，现在可以通过 `@Transactional` 注解按需应用于 CDI Bean，并且更进一步，引入了由 Richard Hightower 之前提出的事务作用域 `@TransactionScoped`。早期的冲突通过以下方式得到解决：规定其他规范应构建在 CDI 之上（而不是 CDI 包含一切），并且不应重用现有注解（而应引入新的注解）。

Bean Validation 1.1 也迅速拥抱了 CDI。其标志性的新特性——方法验证，基于拦截器实现，而在 Java EE 中，这些拦截器会自动应用于 CDI Bean。（方法验证是指使用注解对方法的输入参数和返回值进行验证。）此外，像约束验证器这样的构件现在也可以通过 CDI 进行注入。

JSF 2.2 也采取措施拥抱 CDI。JSF 2.0 的主要特性之一是所谓的视图作用域。这个作用域让许多 JSF 任务变得更容易，但它有一个巨大的问题：它只适用于旧的原生托管 Bean。这个缺陷在一定程度上被 CODI 和 OmniFaces 等实用库所解决，但 JSF 2.2 通过引入自身的 CDI 兼容视图作用域，正式修复了这个问题。此外，JSF 还通过将其新的 Flows 特性完全构建在 CDI 之上，并使其大量构件可通过 CDI 注入，从而进一步拥抱了 CDI。

2014 年 1 月 14 日，对 CDI 1.1 规范的一个微小修订（维护修订，简称 MR）启动，最终形成了 CDI 1.2。此次变更涉及澄清如何读取对话作用域的 `cid` 参数。这次微小修订的维护负责人是 Antoine Sabot-Durand，而不是 Pete Muir。

事情并未止步于担任维护负责人。2014 年 3 月 15 日，Sabot-Durand 宣布他将与 Pete Muir 共同领导 CDI 2.0 规范的制定。在一篇题为“Forward CDI 2.0”的文章中，Sabot-Durand 提出了 2.0 版本的愿望清单。在这份非常详尽的需求清单中，包含了受 DeltaSpike 启发的特性，如“Java SE 引导”和“BeanBuilder”，用于在 CDI 扩展中轻松构建 `Bean<T>` 实例，还包括 CDI 架构的模块化，以及以编程方式将拦截器和装饰器应用于 `Bean<T>` 实例和生产者方法返回的 Bean 的能力（这是现有 CDI 1.1 SPI 中的一个巨大空白）。

几乎在 Java EE 7 发布整整一年后，2014 年 6 月 14 日，DeltaSpike 1.0 发布了。其标志性特性包括 `@Transactional` 和 `@TransactionScoped` 注解（这些已在 Java EE 7 中）、为 Bean Validation 添加 CDI 支持（也已在 Java EE 7 中）、一个用于在 Java SE 中使用 CDI 的容器控制器，以及各种模块，例如安全模块、JSF 模块（包含已在 Java EE 7 中的 `ViewScope` 和 `WindowScope` 等功能）、Data 模块（基于 JPA 的声明式查询）和 Servlet 模块（包含已在 Java EE 7 中的注入 `ServletRequest` 等功能）。

尽管 DeltaSpike 在同年晚些时候获得了 Duke's Choice Award，但总体反响并未达到 Seam 4 的预期。主要问题可能在于，部分功能已在发布整整一年的 Java EE 7 中实现，而且从 Seam 消亡到 DeltaSpike 首次发布之间的时间间隔相当长。此外，其 JSF 模块在某种程度上还面临着来自 OmniFaces 项目的竞争，后者 1.0 版本早在两年前（2012 年 6 月 1 日）就已由 JSF 传奇人物 Bauke Scholtz（又名 BalusC）和本书的作者们发布（尽管实际上它们并不构成竞争，因为功能集差异很大）。

另一个问题可能是 DeltaSpike 安全模块并未原生支持 Java EE 现有的安全系统。对于一个本应构建在 Java EE 之上的框架来说，它奇怪地忽略了用于认证（JASPIC）和授权（JACC）的 EE SPI。

然而，DeltaSpike 核心模块确实获得了许多赞誉，并包含了一些精华，例如其配置功能和用于轻松构建 Bean 的实用工具（这是原生 CDI 1.1 SPI 严重缺乏的领域，也是 Sabot-Durand 为 CDI 2.0 设定的目标）、异常处理机制（`@ExceptionHandler`，基于 Seam Catch）等等。值得一提的是，未能进入 CDI 本身的服务处理器提案，最终以 `Partial-Bean` 模块的形式在 DeltaSpike 中得以实现。

不久之后，2014 年 7 月 28 日，新的 CDI 2.0 JSR 审查开始了。重点在于 Java SE 支持和 CDI 的模块化，其中包括一个实际上仅实现 JSR 330 的最小化容器。Java EE 8 JSR 审查大约在一个月后，即 2014 年 8 月 26 日开始，其重点之一是加强与 CDI 的对齐。JSF 2.3 也在同一天启动，其专家组成员包括本书的一位作者。从一开始，主要规范负责人 Ed Burns 就明确表示 Oracle 可用的资源非常有限，因此 JSF JSR 主要依靠社区来完成。

同样从那天开始的，还有颇具争议的 MVC 规范。该规范之所以有争议，一方面是因为它直接与同样是 MVC 框架且已存在于 Java EE 中的 JSF 竞争，另一方面是因为有人认为像 Angular 这样的客户端框架才是未来，因此确实不需要一个新的、第二个 MVC 框架。MVC 规范引入了控制器，这些控制器本质上是 JAX-RS 资源，但有一个重要区别：它们*必须*是 CDI Bean。这本质上是纠正（事后看来）未立即将 CDI 用于 JAX-RS 这一错误的第一步。

不久之后，2014 年 10 月 13 日，JSF 联合规范负责人 Manfred Riem 为 JSF 2.3 进行了首次提交，其中所谓的隐式对象现在通过 CDI 进行解析，因此也可以通过 CDI 注入。这标志着 JSF 完全重新对齐 CDI 的第一步。



2014 年 11 月 25 日，一项新的 Java EE 8 规范评审启动：Java EE Security。本书作者之一 Arjan Tijms 是该专家组的成员，并最终在很大程度上主导了该规范的制定。Tijms 强烈主张将 EE Security 工件完全作为 CDI Bean。最初，这一想法遭到了一些反对，规范负责人 Alex Kosowski 反驳说并非所有内容都必须采用 CDI，并提议改用 JNDI 和`@Resource`。此事后来在专家组内部得到解决，CDI 被选为首选基础。

进入 2015 年，CDI 专家组正努力起草新版 CDI 规范的早期草案，并讨论如何最好地实现各种功能请求。甚至 DeltaSpike 中的部分 Bean 也被再次讨论。然而，在这些工作和讨论中，有一个人奇怪地缺席了大部分内容，那就是主要规范负责人 Pete Muir。

因此，2015 年 7 月 30 日，Antoine Sabot-Durand 在 CDI 邮件列表中宣布 Muir 将辞去 CDI 规范负责人一职，并在红帽公司承担新的职责，这并不令人意外。此后，Sabot-Durand 接任主要规范负责人。专家组对这一消息的反应极为积极，Muir 最后一次露面时表示，将 CDI 交给能干的 Sabot-Durand 是最好的选择。此后，Muir 成为 JBoss Developer Studio 团队的经理，并因此基本（但并非完全）淡出了公众视野。

## 暗流涌动

2015 年 9 月 30 日，InfoWorld 发表了一篇题为“内部消息：甲骨文已对 Java 失去兴趣”的文章，文中称甲骨文正在缩减其在 Java 和 Java EE 方面的投入。尽管甲骨文未对此作出回应，但在随后的几个月里，情况确实变得明朗：甲骨文已悄然开始退出 Java EE，而大多数与甲骨文规范负责人相关的规范也开始凋零。

在 Java EE 8 启动后的一年里，JMS 专家组一直不懈地努力，通过精心设计 API 和规范，使 CDI 成为 JMS 中的一等公民，让 CDI Bean 能够作为 JMS 监听器运行。然而，2015 年 12 月 9 日，规范负责人 Nigel Deakin 在回答一个关于定期会议主持的随意问题时，发布了一条令人担忧的消息：

> *“不幸的是，由于其他工作压力，我无法主持接下来的几次 JMS 2.1 会议。情况有变时我会通知大家。”*

（然而，情况从未改变，JMS 2.1 实际上就此戛然而止。）

2016 年 4 月 29 日，独立的 JSF 专家组成员 Josh Juneau 发表了文章“Java EE 8，当前状态如何：2015 年底以来已完成工作的案例研究”，该文通过各种统计数据清楚地表明，甲骨文已悄然停止了对大多数 EE 规范的工作，而更广泛的社区对此仍不甚了解。

2016 年 5 月 25 日，Arjan Tijms 弃用了 JSF 2.3 中所有原生的托管 Bean 注解，并将其指向对应的 CDI 注解。虽然这尚未实际移除它们，但被视为朝着该目标迈出的非常重要的一步。

随着甲骨文悄然退出 Java EE 工作带来的种种不确定性，其他 EE 供应商并未坐等甲骨文。相反，一项新的倡议被发起，2016 年 6 月 27 日，在旧金山举行的 DevNation 大会上，来自 Payara、红帽、IBM、Tomitribe 和伦敦 JUG 的代表宣布了一项名为 MicroProfile 的新合作。MicroProfile 建立在 Java EE 之上，但创建了新的规范和 API，由各供应商自行实现。因此，其流程类似于 Java EE，只是不使用 JCP 流程，而是采用更轻量的“代码优先”方法。这意味着存在规范文档，但代码（API 和早期实现）具有优先权。MicroProfile 的一个重要方面是，它产生的所有规范都应优先考虑 CDI。

在 2016 年 9 月的 JavaOne 大会主题演讲中，甲骨文宣布将完成 Java EE 8，但取消 JMS、MVC 以及一个不太知名的规范 Management 2.0。尽管在过去一年中，规范负责人或其他甲骨文成员对 JSF 和 Java EE Security 未做任何工作，但这两个规范主要由社区驱动且不依赖甲骨文，因此得以幸存，幸运的是它们没有被取消。CDI 2.0 并非甲骨文规范，也未受甲骨文中止工作的影响。

2016 年 10 月 26 日，CDI 专家组成员兼 IBM Liberty 架构师 Emily Jiang 为 MicroProfile 将要交付的第一个规范——MicroProfile Config——进行了首次提交。在接下来的几个月里，Jiang 是该项目的核心提交者。2017 年 1 月 18 日，Mark Struberg 加入。他们共同提交了大部分工作，偶尔有其他提交者提供帮助。

2016 年底，几位甲骨文规范负责人短暂回归 JSF 和 EE Security 规范，但并非为了做大量或任何新工作，而是确保当前规范的快照已准备好发布。

2017 年 5 月 15 日，Antoine Sabot-Durand 宣布 CDI 2.0 已发布。主要新特性按计划是 Java SE 支持，并且规范本身更加模块化，分为三个部分（核心、SE 和 EE）。受 DeltaSpike 启发的 BeanBuilder API 确实已被包含（现在称为 Configurators），Sabot-Durand 最初愿望的一部分也被纳入：拦截器现在可以（以编程方式）应用于从生产者返回的 Bean。然而，`Bean<T>`实现查找并应用其自身装饰器的能力并未进入 2.0 规范。

DeltaSpike 不仅启发了 CDI 2.0，也启发了 MicroProfile。2017 年 7 月 25 日，在 Payara 工作的工程师 Ondro Mihályi 发布了 MicroProfile Config 1.0。MicroProfile Config 明显基于 DeltaSpike config，甚至使用了相同的术语，例如 ConfigSource，Struberg 早在 2011 年 12 月 DeltaSpike 刚启动时的那次提交中就使用过该术语。

2017 年 8 月 17 日，甲骨文软件布道师 David Delabassee 宣布，甲骨文希望将 Java EE 迁移到一个开源组织。一个月后，即 2017 年 9 月 12 日，宣布该组织将是 Eclipse，不久之后，项目名称被宣布为 EE4J。

Java EE 8 最终于 2017 年 9 月 18 日发布。它包含了 JSF 2.3 和 EE Security 1.0 等。EE Security 完全构建在 CDI 之上（以及在更底层的 JASPIC 之上）。在 CDI 1.0 发布近八年后，终于有了一个从一开始就考虑 CDI 而设计的规范。不幸的是，由于工作中止，将服务从 EJB 进一步解耦并迁移到基于 CDI 的工件的工作一点也没有完成。

2 月 26 日，Java EE 规范的官方新名称通过社区意见选定为 Jakarta EE。不久后选定了徽标，源代码也逐步从甲骨文在 GitHub 上拥有的“java-ee”组织转移到 Eclipse 拥有的“eclipse-ee4j”。



## 美丽新世界

2018 年 3 月 26 日，Tijms 联系 Lee，询问是否将停滞近十年的 JSR 330 移交给 Jakarta，或将其与 CDI 合并。Lee 回应称绝对不想将其与 CDI 合并，但忽略了关于移交给 Jakarta 的问题。

2018 年 6 月 22 日，Stuart Douglas 为 Red Hat 的一个名为 Shamrock 和 Protean 的新项目进行了首次提交。该项目的计划是成为一个单一的项目框架（类似于曾经的 Seam），它基本放弃了反射机制，回归到 EJB 最初采用的代码生成方法，不过这次自动化程度要高得多。这次初始代码提交包含一个`org.jboss.shamrock.codegen`包，其中有一个`BytecodeRecorder`类，使用了 JBoss ClassWriter，这清楚地表明这个新框架的核心原则是代码生成。Shamrock 的早期描述如下：

“Shamrock 是一个框架，允许你在构建时处理 Java EE 和 Microprofile 元数据，并利用这些元数据创建低开销的 jar 文件，以及使用 Graal 创建原生镜像。”

Red Hat 对此方法并不陌生，因为其 CDI 实现 Weld 可选择性地使用 Jandex，后者可以在构建时扫描注解，并将这些注解存储在一个索引中，Weld 可以在运行时查询该索引。

当 Java EE 移交仍在进行，Oracle 与 Eclipse 之间的谈判仍在继续时，Tijms 于 2018 年 7 月 16 日启动了 JSF 3.0 的工作。计划之一是完全从 JSF 中移除原生托管 bean 系统，包括古老的 XML 变体，并从此完全依赖 CDI。大约在同一时间，Tijms 向 Steve Millidge（Payara 总监）提出了将 HK2 转变为完全兼容 CDI 的实现的构想。这个想法得到了积极响应，因为它将解决 Payara 服务器内部使用两个 DI 框架（HK2 和 CDI（Weld））的问题，这两个框架共享通用的 AtInject 注解，因此经常发生冲突，并导致诸如重复扫描等各种问题。

在这一切发生的同时，Gavin King 仍在从事他的 Ceylon 项目，但在该项目上工作了近 9 年后，2019 年 9 月 29 日终于成为 King 在 Ceylon 仓库中最后一次提交的日期。在接下来的几个月里，King 逐渐回归到 Java EE 领域。

意识到 Red Hat 新项目使用 Shamrock/Protean 这种双重命名并不完全理想，Shamrock 于 2019 年 2 月 22 日更名为 Quarkus。大约两周后，即 2019 年 3 月 7 日，Jason Greene 向世界介绍了 Quarkus。利用构建时元数据收集和代码生成，并结合 GraalVM，Quarkus 能够以前所未有的毫秒级速度启动 Java 应用程序。

特别有趣的是，Quarkus 引入了一个名为 Quarkus ArC 的新 DI 实现。ArC 实现了 CDI 2.0 的大部分 API 和行为，但并非全部。它并非基于 Red Hat 的 CDI 实现 Weld，而是构成了一个全新的、精简得多的代码库。由于基于构建时处理，ArC 能够在构建时（而非传统 CDI 实现那样在运行时（启动时））发出许多典型的 CDI 错误，例如歧义解析。然而，ArC 不支持 CDI 可移植扩展，而是提供了构建时的变体来替代它们。由于 ArC 并非官方的 CDI 实现，它在引入超出 CDI 2.0 API 的功能方面采取了一些自由，例如，如果使用了限定符，则`@Inject`的使用变为可选；这是一个很久以前就有人提出但从未被纳入 CDI 规范的构想。

Payara 的 Jonathan Coustick 在与 Arjan Tijms 合著的一篇文章中，于 2019 年 6 月 6 日宣布，Payara 计划在未来某个时候将 HK2 转变为完整的 CDI 实现，作为 Weld 的替代方案。这距离 Tijms 提出最初构想已近一年。

2019 年 7 月 22 日，作为仍在进行中的 Java EE 移交以及随后的 Jakarta EE 8 准备工作的一部分，CDI API 和规范文档被正式从 Red Hat 移交到 Github 上的 Eclipse EE4J 仓库，与所有其他已更名为 Jakarta EE 的 Java EE API 放在一起。

第二天，即 2019 年 7 月 23 日，来自西雅图的 Red Hat 工程师 Scott Stark 将 JSR 330 API 代码的一个分支提交到了同一个仓库。遗憾的是，未能与 Lee 或 Johnson 达成移交原版的协议。

