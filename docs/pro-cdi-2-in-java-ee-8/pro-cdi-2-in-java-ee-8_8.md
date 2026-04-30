# 8. Java SE 中的 CDI

到目前为止，可能已经很明显，CDI 为任何非平凡的应用程序（包括非基于 Web 的应用程序）提供了许多好处。令人惊讶的是，在 2.0 版本之前的 CDI 规范官方仅支持 Java EE。尽管 Java SE 并未得到官方支持，但在 2.0 规范之前，许多 CDI 实现确实提供了对 Java SE 环境的支持。例如，Weld 和 Apache OpenWebBeans 都提供了对 Java SE 的支持。由于规范并未官方支持 Java SE，这意味着每个容器都需要提供自己的非标准化 API，以允许应用程序引导和运行容器。拥有这种非标准化 API 的一个主要缺点是，它使得应用程序开发者切换到另一个 CDI 容器稍微困难一些，因为在 Java SE 环境中引导容器的代码需要重写。像 Arquillian 和 Apache DeltaSpike Container Control 这样的库和框架确实提供了允许开发者引导 CDI 容器的 API，无论实际使用的是哪个容器。然而，Arquillian 的主要焦点是能够在不同环境中运行单元测试和集成测试，并不太适合在应用程序内部启动 CDI 容器。容器也需要得到 Apache DeltaSpike Container Control 的显式支持。如果引入了一个新容器，或者某个容器以不兼容的方式更改了其专有 API，那么 DeltaSpike Container Control 的用户需要等待新版本发布才能获得支持。

幸运的是，对 Java SE 环境的官方支持已在 CDI 2.0 中添加。作为添加此支持的一部分，CDI 规范被分成了三个独立的部分。第一部分，Core CDI，涵盖了 Java SE 和 Java EE 通用的核心功能。第二部分，Java EE 中的 CDI，涵盖了在 Java EE 环境中使用 CDI 时，在 Core CDI 之上的附加规则和特性。最后一部分是 Java SE 中的 CDI，涵盖了 Java SE 的附加规则和功能。

## 引导 API

CDI 2.0 引入了一个引导 API，允许 Java SE 容器管理和启动 CDI 容器实例。CDI 容器必须为引导 API 定义的多个接口提供实现，以便为 Java SE 环境提供支持。引导 API 可以使用 `ServiceLoader` 机制动态检测和实例化类路径或模块路径上可用的任何容器。如果你不熟悉 Java 中的 `ServiceLoader` 机制，它允许注册实现特定类或接口的类，然后在运行时动态加载这些已注册的类。

要使用引导 API，CDI 容器和相应的引导 API 实现必须在类路径上可用。由于大多数容器将由多个独立的 JAR 文件组成，因此向 Java SE 应用程序添加 CDI 容器的最简单方法是使用像 Maven、Gradle 或 Ivy 这样的依赖管理工具。要使用 Apache OpenWebBeans 作为 CDI 容器，必须声明对容器实现以及 OpenWebBeans 提供的 CDI API 的依赖。例如，对于 Maven，需要在 `pom.xml` 文件中添加以下依赖项才能使用 OpenWebBeans：

```
org.apache.openwebbeans
openwebbeans-spi
2.0.3

org.apache.openwebbeans
openwebbeans-impl
2.0.3

```

其他 CDI 容器可能包含对 API 本身的传递依赖，而无需显式声明对其的依赖。例如，对于 Weld，只需依赖引导 API 实现即可。引导 API 实现本身依赖于 CDI API 和容器实现本身，因此这些会自动作为传递依赖包含进来。要在 Java SE 应用程序中使用 Weld，只需在项目的 `pom.xml` 文件中声明以下依赖项：

```
org.jboss.weld.se
weld-se-core
3.0.3.Final

```



### 注意

运行时，类路径或模块路径上只能存在一个 CDI 容器实现。如果引导 API 发现了多个容器实现，则在尝试加载实现时将抛出 `IllegalStateException`。

引导 API 的主要入口点是 `SeContainerInitializer` 接口。该接口包含许多可用于配置并最终启动 CDI 容器实例的方法。配置方法遵循构建器模式，允许将多个调用链式组合。可以通过调用 `SeContainerInitializer` 类上的静态 `newInstance()` 方法来获取对应容器的 `SeContainerInitializer` 实例。以下代码片段说明了如何在 Java SE 中初始化容器：

```
try (SeContainer container = SeContainerInitializer.newInstance()  .initialize()) {
// 在此处初始化应用程序
}
```

在初始化容器实例之前，可以使用 `SeContainerInitializer` 配置属性。这些属性可以是标准化属性，也可以是容器特定的属性。要配置属性，可以调用 `setProperty()` 方法。该方法接受一个 `String` 类型的键和值作为参数，并将此键值对添加到当前已配置的属性集中。还有一个 `setProperties()` 方法，它接受一个 `Map<String, String>` 作为参数。调用此方法将用映射中的内容覆盖当前已配置的属性集。

一旦获取并配置了 `SeContainerInitializer` 实例，就可以通过调用其 `initialize()` 方法来创建 CDI 容器。请注意，`initialize()` 方法应该是 `SeContainerInitializer` 实例上调用的最后一个方法；任何所需的配置都应首先执行。此外，当 CDI 容器已经在运行时，也无法调用 `initialize()` 方法。这样做将导致抛出 `IllegalStateException`。

当 `initialize()` 方法成功完成时，它将返回一个 `SeContainer` 实例。`SeContainer` 接口实现了 `Instance` 接口，该接口允许获取具有特定（泛型）类型和限定符注解组合的 Bean。为此，可以在 `SeContainer` 上调用 `select()` 方法的重载之一。`SeContainer` 提供了多种重载，用于仅通过注解、通过类型和零个或多个注解、以及通过类型字面量和零个或多个注解来获取 Bean。`select()` 方法返回另一个 `Instance`（如果使用了带有类型或类型字面量的重载，则 `T` 是 Bean 的类型），可以通过调用该实例上的 `get` 方法来获取 Bean 实例。还可以通过 `SeContainer` 接口获取 `BeanManager` 实例，该实例也可用于获取 Bean 实例。但是，建议您使用 `SeContainer` 实例提供的 `select()` 方法。

`SeContainer` 接口还应在应用程序退出时用于关闭 CDI 容器。`SeContainer` 有一个 `close()` 方法，该方法将执行必要的容器关闭操作。调用此方法将导致容器管理的所有 Bean 被销毁。在销毁每个 Bean 之前，将调用任何适用的 `@PreDestroy` 方法，以便在容器本身被销毁之前执行任何所需的清理操作。`SeContainer` 接口还扩展了 `AutoCloseable` 接口，这使得可以在 `try-with-resources` 语句中创建并自动销毁容器。`close()` 方法只能调用一次。第二次调用将导致抛出 `IllegalStateException`。

一旦应用程序启动并运行，可能需要在无法访问 `SeContainer` 实例的情况下获取对正在运行的容器的引用。CDI 类有一个 `current()` 方法。这将返回一个 CDI 类的实例，可用于访问当前的 CDI 容器。它提供与 `SeContainer` 相同的所有功能，但无法关闭 CDI 容器。

假设您想使用 CDI 编写一个简单的“Hello World”应用程序。该应用程序应由两个类组成。第一个类是一个相当简单的 `GreetingService`，它有一个返回问候字符串的方法。第二个类包含用于启动应用程序的 `main` 方法。这个 `main` 方法将引导 CDI 实例并调用 `GreetingService`。此示例中的 `GreetingService` 实例通过调用 `container.select(GreetingService.class).get()` 从容器中检索。您可能会注意到，没有执行强制类型转换来将 `GreetingService` 的实例分配给变量。由于 API 的泛型签名，此情况下的返回类型被推断为 `GreetingService`。

```
package helloworld;
public class GreetingService {
public String generateGreeting(String name) {
return "Hello, " + name + "!";
}
}
```

以下清单展示了在 Java SE 应用程序中引导 CDI 容器的过程：

```
package helloworld;
import javax.enterprise.inject.se.SeContainer;
import javax.enterprise.inject.se.SeContainerInitializer;
public class HelloWorldExample {
public static void main(String[] args) {
try (SeContainer container = SeContainerInitializer.newInstance().initialize()) {
GreetingService greetingService = container.select(GreetingService.class).get();
String greeting = greetingService.generateGreeting("world");
System.out.println(greeting);
}
}
}
```

需要牢记的一点是通过引导 API 创建的 Bean 的生命周期。当使用此 API 创建依赖作用域的 Bean 实例时，容器会保留对该实例的引用。此引用将阻止该 Bean 实例被垃圾回收器清理。依赖作用域的 Bean 实例在某种程度上与创建它的容器的生命周期相关联。当应用程序使用引导 API 创建许多依赖作用域的 Bean 时，应用程序可能会在某个时候耗尽内存。为防止此类内存泄漏，应用程序应在不再需要这些 Bean 实例时显式销毁它们。

## Bean 归档扫描

在 Java SE 环境中初始化 CDI 容器时，默认情况下，它会通过扫描类路径上任何可用的 Bean 归档来执行 Bean 发现。与 Java EE 一样，Java SE 中的 CDI 支持隐式和显式 Bean 归档。默认情况下，容器只会检测显式 Bean 归档中的 Bean；隐式 Bean 归档中的任何 Bean 都将被忽略。

对于 Java SE 容器，`javax.enterprise.inject.scan.implicit` 属性控制是否自动检测隐式 Bean 归档。有两种方法可以设置此属性。第一种是在初始化 CDI 容器之前将其设置为系统属性。这可以通过在启动应用程序时在命令行上指定 `-Djavax.enterprise.inject.scan.implicit=true` 来完成。或者，也可以通过编程方式设置系统属性，如下所示：

```
System.setProperty("javax.enterprise.inject.scan.implicit", "true");
```

启用对隐式 Bean 归档支持的第二种方法是在初始化容器之前，使用 `addProperty()` 或 `setProperties()` 方法在 `SeContainerInitializer` 实例上设置此属性。要配置该属性，必须将属性名称指定为键，并将值指定为 `Boolean.TRUE`。

```
SeContainerInitializer.newInstance()
.addProperty("javax.enterprise.inject.scan.implicit", Boolean.TRUE);
```



## 合成 Bean 归档

除了自动扫描 bean 归档之外，还可以通过编程方式向 `SeContainer` 注册 bean。每个 `SeContainer` 都有一个单一的合成 bean 归档，该归档只能在容器通过 `SeContainerInitializer` 实例完全初始化之前进行配置。以下方法可用于管理合成 bean 归档：

*   `addBeanClasses()`

*   `addPackages()`

*   `addExtensions()`

*   `enableInterceptors()`

*   `enableDecorators()`

*   `selectAlternatives()`

*   `selectAlternativeStereotypes()`

*   `disableDiscovery()`

`addBeanClasses()` 方法将一个或多个 bean 类添加到合成 bean 归档中。这些 bean 将由容器完全管理，就像它们存在于常规 bean 归档中一样。需要注意的是，不要将已存在于其他 bean 归档中的 bean 添加到合成 bean 归档中。如果发生这种情况，CDI 容器将因在多个 bean 归档中遇到同一个 bean 而无法启动。

除了向合成 bean 归档添加单个类之外，还可以使用 `addPackages()` 方法添加整个包。此方法有四种形式。两个主要版本接受一个或多个类或包。当使用接受类的重载时，包含这些类的包中的所有 bean 都将被添加到合成 bean 归档中。默认情况下，只会添加来自指定包的 bean，但这两个版本都有一个重载，允许您递归地添加来自某个包的 bean。当启用递归选项添加 bean 包时，该包及其子包中的所有 bean 都会被添加到合成 bean 归档中。使用此选项时需要小心，因为子包可能位于不同的归档中。父包可能不包含在 bean 归档中，但其某个子包可能包含在其中。如果是这种情况，并且父包被递归添加，那么该子包也会被添加到合成 bean 归档中。这将导致与 `addBeanClasses()` 方法相同的情况，即 bean 类在两个不同的 bean 归档中被指定，这将阻止容器初始化。

`enableInterceptors()` 和 `enableDecorators()` 方法可用于为合成 bean 归档启用拦截器或装饰器。这些方法允许有条件地启用拦截器或装饰器。如果拦截器或装饰器未包含在其他 bean 归档中，并且也未为合成 bean 归档启用，则该拦截器或装饰器将在运行时被静默忽略。例如，这使得仅在指定了某个命令行标志或应用程序在特定环境中运行时才启用拦截器成为可能。

CDI 扩展可以通过 `addExtensions()` 方法添加到合成 bean 归档中。此方法有两个重载，一个接受扩展类作为参数，另一个接受扩展实例。后者特别有趣，因为它允许使用匿名内部类作为扩展。以这种方式注册的类也不再需要使用服务配置文件进行注册。与 `enableInterceptors()` 和 `enableDecorators()` 方法一样，此方法可用于有条件地激活任何未在服务加载器配置文件中定义的扩展。

`selectAlternatives()` 方法用于在合成 bean 归档中选择替代 bean。此方法接受一个或多个 `Class` 类型的参数。该类必须是使用 `@Alternative` 注解的 bean 类，或者是包含使用 `@Alternative` 注解的生产者方法的类。`selectAlternativeStereotypes()` 方法类似。它接受一个或多个使用 `@Alternative` 注解的原型注解类作为参数。`selectAlternatives()` 方法和 `selectAlternativeStereotypes()` 方法都不会向合成 bean 归档添加任何类。所有必需的 bean 类都必须单独添加。这两种方法也只会为合成 bean 归档选择这些替代项；其他 bean 归档仍将使用默认 bean，除非它们在其各自的 `beans.xml` 文件中指定了替代项。

用于扫描 bean 归档的 `ClassLoader` 可以使用 `setClassLoader()` 方法设置。调用此方法会将 bean 归档扫描限制为仅对给定 `ClassLoader` 可用的 bean 归档。其用途之一是从类路径外部加载 bean 归档及其包含的 bean。考虑一个需要从预定义目录而不是类路径加载 bean 归档的应用服务器。这可以通过为此替代目录指定一个自定义类加载器来实现。

最后，`disableDiscovery()` 方法将完全禁用容器的 bean 归档扫描。类路径上可能存在的任何 bean 归档都将被忽略。所有 bean 类或包、拦截器类和装饰器类都必须显式注册到合成 bean 归档中。与自动 bean 归档扫描相比，这可以更好地控制哪些类可通过 CDI 使用。一个用例可能是为应用程序中的所有 bean 归档选择替代项。通常，所有 bean 归档都需要使用一个声明了所选替代 bean 的 `beans.xml` 文件进行更新。然而，更新现有的 bean 归档可能并不总是可行的。例如，一个 bean 归档可能在多个应用程序之间共享，而其他应用程序仍然需要使用默认 bean。在这种情况下，将合成 bean 归档与禁用发现功能结合使用，可以为所有类设置替代 bean，而无需更新任何 `beans.xml` 文件。

必须声明应用程序所需的所有类或包可能是一个主要缺点。对于较大的应用程序，需要显式注册的依赖项列表可能会变得非常长且难以维护。



## 测试

引导式 API 和合成 Bean 存档扫描在测试领域能发挥重要作用。单元测试和集成测试是任何软件开发流程的关键组成部分，有助于防止新缺陷引入代码库。这一点对于使用 CDI 的应用程序与其他应用程序同样适用。然而，依赖注入可能会使代码的隔离测试变得更加复杂。复杂的 Bean 层级结构难以通过编程方式管理，而考虑使用不同作用域的 Bean 层级结构时，难度会进一步增加。更好的做法是将测试代码也运行在 CDI 容器中，这样测试代码的主要关注点就是实际的测试逻辑，而非设置 Bean 类及其依赖关系。Arquillian 已经提供了用于启动和停止容器的框架，但现在无需外部依赖也能实现这一点。以下代码清单展示了本章前面提到的 `GreetingService` 的一个简单 Junit 5 测试：

```
package helloworld;
import static org.junit.jupiter.api.Assertions.assertEquals;
import javax.enterprise.inject.se.SeContainer;
import javax.enterprise.inject.se.SeContainerInitializer;
import org.junit.jupiter.api.AfterEach;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
class GreetingServiceTest {
private SeContainer container;
@BeforeEach
void setUp() {
container = SeContainerInitializer.newInstance().initialize();
}
@AfterEach
void tearDown() {
container.close();
}
@Test
void generateGreeting() {
GreetingService greetingService = container.select(GreetingService.class).get();
assertEquals("Hello, world!", greetingService.generateGreeting("world"));
}
}
```

在此示例中，容器在每个测试方法前后通过使用 `@BeforeEach` 和 `@AfterEach` 注解的方法启动和停止。这将确保每个单独的测试方法都在独立的容器实例中运行。这是一种在测试之间重置容器状态的有效方式。例如，可能存在多个 `@ApplicationScoped` Bean 会跟踪测试期间更新的状态。通过每次启动和停止容器，每个测试都将在全新的环境中运行。如果残留状态不成问题，CDI 容器也可以在带有 `@BeforeAll` 和 `@AfterAll` 注解的静态方法中启动和停止。`@BeforeAll` 方法会在测试类的任何测试方法被调用前调用一次，而 `@AfterAll` 方法则在之后调用一次。如果需要在测试期间获取容器的引用，可以将其存储在 `@BeforeAll` 方法的静态字段中，或者通过调用 `CDI.current()` 来获取。

可能出现的一个问题是，当需要模拟或伪造注入的 Bean 时。模拟或伪造应谨慎使用，但有时不可避免，例如当 Bean 调用远程 API 时。一个简单的解决方案是将模拟或伪造的 Bean 标记为 `@Alternative`，并使用特定于测试的 `beans.xml` 注册该替代项。然而，这种方法在大多数情况下并不奏效。原因在于测试代码通常会被编译到与被测代码不同的目标文件夹中。因此，测试代码会位于不同的 Bean 存档中，而替代项仅针对当前 Bean 存档注册，导致被测代码仍会使用常规 Bean 而非替代项。

当结合禁用自动 Bean 存档扫描时，合成 Bean 存档可以为这个问题提供解决方案。通过禁用存档扫描，测试所需的所有 Bean 都必须显式注册到合成 Bean 存档中。然后，可以使用 `selectAlternatives()` 方法将替代 Bean 注册到合成 Bean 存档中。由于所有 Bean 现在都位于同一个存档中，这也会为测试所涵盖的所有 Bean 注册替代项。这个解决方案也引入了一个新问题：随着被（间接）测试的代码随时间演变，测试注册的 Bean 列表需要持续更新。因此，如果不是严格需要使用模拟或伪造，最好避免采用这种方法。

例如，如果你更新 `GreetingService` 以调用远程的 `LocalizedGreetingMessageService` 来获取本地化的问候消息，你需要在测试期间替换 `LocalizedGreetingMessageService` 的实现。你可以通过提供一个 `FakeLocalizedGreetingMessageService` 实现来解决这个问题，该实现不执行任何远程调用，但仍能提供测试所需的输出。你可以使用 `selectAlternatives()` 方法来选择你的伪造服务，但如果你不禁用自动存档扫描，更新后的 `GreetingService` 在测试运行时仍会执行远程调用。然而，如果你在 `SeContainerInitializer` 上调用 `disableDiscovery()` 方法，测试将转而调用你的伪造服务。示例 XYZ 将展示 `RemoteGreetingServiceTest` 的代码：

```
package helloworld;
import javax.enterprise.inject.se.SeContainer;
import javax.enterprise.inject.se.SeContainerInitializer;
import org.junit.jupiter.api.AfterEach;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
public class RemoteGreetingServiceTest {
private SeContainer container;
@BeforeEach
void setUp() {
container = SeContainerInitializer.newInstance()
.disableDiscovery()
.addPackages(RemoteGreetingService.class.getPackage())
.selectAlternatives(FakeLocalizedGreetingMessageService.class)
.initialize();
}
@AfterEach
void tearDown() {
container.close();
}
@Test
public void testGenerateGreeting() {
// TODO implement test here.
}
}
```



## 作用域

作用域与上下文是 CDI 最重要的组成部分之一。它们管理着容器所托管 Bean 的生命周期，同时也决定了事件应被投递到哪些 Bean。CDI 在 Java SE 中完全支持作用域功能。所有内置作用域均受支持，但在某些作用域的激活时机上存在若干关键差异。例如，由于 Java EE 拥有 Servlet API，因此定义请求作用域的工作方式相对容易。但 Java SE 的情况则不同：Java SE 应用程序可能根本不处理请求，或者仅使用底层服务器套接字来处理传入请求。因此，并非总能明确定义作用域在 Java SE 中应如何运作。结果，请求作用域在 Java SE 中的定义比在 Java EE 中受限得多。与 Java EE 一样，应用程序仍然可以提供自定义的作用域上下文。

以下是对每个内置作用域的定义：

*   **应用作用域上下文**，定义为始终处于激活状态
*   **请求作用域上下文**，在所有 `@PostConstruct` 回调方法和所有异步观察者方法期间处于激活状态
*   **会话作用域上下文**，其生命周期未预定义
*   **对话作用域上下文**，由活动应用程序本身控制

Java SE 应用程序仍然可以通过提供自定义作用域上下文来覆盖上述每个作用域的定义。请求作用域就是一个体现其用途的例子。如果 Java SE 应用程序处理传入请求，则可以重新定义请求作用域，使其在处理这些请求期间处于激活状态。有关覆盖内置作用域的更多信息，请参见第 4 章。

除了内置作用域，Java SE 中的 CDI 还支持依赖伪作用域。与 Java EE 中一样，依赖作用域的 Bean 会采用其被注入或创建来源的 Bean 的生命周期。因此，例如，如果一个依赖 Bean 被注入到一个应用作用域的 Bean 中，那么该依赖 Bean 也将采用应用作用域作为其生命周期。请注意，依赖作用域的 Bean 永远不会被共享；如果某个特定类型的 Bean 被注入到另外两个具有相同作用域的 Bean 中，那么这两个 Bean 将各自获得该依赖 Bean 的不同实例。

## Java FX

JavaFX 从 Java 8 开始成为 JRE 和 JDK 的一部分，是 Java 的新 UI 工具包。与现有的 UI 工具包（AWT、Swing 和 SWT）相比，它提供了诸多优势，通过基于场景图的方法来构建 UI，并支持特效、动画甚至 3D。它被设计为支持从桌面应用到移动应用的多种设备。

JavaFX 中的 `Application` 类可以被扩展，以提供 JavaFX 应用程序的入口点。当扩展 `Application` 类时，JVM 会自动检测到 JavaFX 的使用，并确保应用程序在正确的线程上初始化和启动。`Application` 类通过三个方法提供了一个简单的应用程序生命周期。`init()` 方法用于初始化应用程序，`start()` 方法用于启动应用程序，`stop()` 方法用于在 JavaFX 框架即将关闭时停止应用程序。在 JavaFX 应用程序中集成 CDI 的一种简单方法是在 `start()` 方法中使用 `try-with-resources` 语句初始化容器。但这行不通，因为 `start` 方法在应用程序开始运行时即完成，而非在应用程序关闭时，这会导致 CDI 容器过早关闭。更好的方法是重写 `init()` 方法来初始化 CDI 容器，并将生成的 `SeContainer` 实例赋值给 `Application` 子类中的一个字段。为了关闭活动容器，可以重写 `stop()` 方法，在 `SeContainer` 实例上调用 `close()` 方法。

另一种方法是在初始化 JavaFX 之前，在 `main()` 方法中初始化 CDI 容器。JavaFX 提供了多个静态工具方法，可用于初始化 JavaFX 框架以及初始化和启动 JavaFX 应用程序。这些方法在应用程序停止之前不会完成。因此，这些方法适合在将 CDI 容器作为资源管理的 `try-with-resources` 块中调用。最直接的方法是使用 `Application.launch(String... args)`。此方法只能从扩展了 `Application` 类的类中的方法调用。原因在于应用程序类是通过检查调用栈来确定的。以下代码片段展示了在 main 方法中初始化 CDI 和 JavaFX 的示例：

```
public static void main(String[] args) {
try (SeContainer container = SeContainerInitializer.newInstance().initialize()) {
Application.launch(args);
}
}
```

在 JavaFX 中创建用户界面的一种常见方式是使用 FXML。FXML 是一种基于 XML 的语言，用于定义视图中的所有 UI 组件及其布局。FXML 语言还允许为视图定义一个控制器类。FXML 视图通过 `FXMLLoader` 类从文件加载。`FXMLLoader` 实例会解析 FXML 配置，并创建视图及相应控制器的实例。由于控制器是将 UI 输入转换为对业务逻辑调用的地方，因此能够使用 CDI 注入控制器的依赖项将非常棒。`FXMLLoader` 类通过允许您定义用于创建所有控制器实例的工厂来支持此功能。该工厂必须实现 `javafx.util.Callback` 接口，并以控制器的类作为唯一参数。由于 CDI 容器提供了一种通过 Bean 的类来获取其实例的方法，因此您可以将其用作控制器工厂。由于 `Callback` 接口是一个函数式接口，您只需提供一个 lambda 表达式，该表达式使用 CDI 容器来获取给定类的实例，可以通过使用对 `SeContainer` 实例的可用引用，或者调用 `CDI.current()` 来获取它。由于控制器类现在是一个 CDI 管理的 Bean，您可以直接将所需的任何依赖项注入到控制器中。以下清单展示了一个加载 FXML 视图的简单 JavaFX 应用程序：

```
package helloworld;
import javax.enterprise.inject.se.SeContainer;
import javax.enterprise.inject.se.SeContainerInitializer;
import javax.enterprise.inject.spi.CDI;
import javafx.application.Application;
import javafx.fxml.FXMLLoader;
import javafx.scene.Parent;
import javafx.scene.Scene;
import javafx.stage.Stage;
public class HelloWorldFxExample extends Application {
private SeContainer seContainer;
@Override
public void init() {
seContainer = SeContainerInitializer.newInstance().initialize();
}
@Override
public void start(Stage stage) throws Exception {
FXMLLoader fxmlLoader = new FXMLLoader();
fxmlLoader.setControllerFactory(clazz -> CDI.current().select(clazz).get());
fxmlLoader.setLocation(HelloWorldFxExample.class.getResource("/helloworld/hello.fxml"));
Parent root = fxmlLoader.load();
stage.setScene(new Scene(root));
stage.show();
}
@Override
public void stop() {
seContainer.close();
}
}
```



使用 CDI 容器作为控制器实例的工厂存在一些注意事项。JavaFX 提供了一种有限的依赖注入形式，即使用 `@FXML` 注解将 FXML 视图中的组件注入到控制器中。无法将 JavaFX 提供的这种注入与具有 CDI 作用域（除依赖伪作用域外）或注册了任何拦截器的控制器结合使用。这样做会导致标记有 `@FXML` 的字段在 Bean 构建后保持未初始化状态。这是 CDI 容器实现作用域和拦截器的方式所导致的结果。当尝试获取或注入具有作用域或拦截器的 Bean 实例时，容器将使用代理而不是 Bean 的实际实例。当在此代理上调用任何方法时，代理将首先调用任何适用的拦截器，然后根据 Bean 的作用域定义委托给实际实例。由于代理必须可赋值给原始 Bean 类型的任何字段、变量或参数，因此代理必须是原始 Bean 类的子类。这导致代理也继承了原始类中定义的任何字段。如果原始类包含任何用 `@FXML` 注解的字段，代理的类也将具有这些相同的字段和相同的注解。由于 `FXMLLoader` 将从您定义的控制器工厂获取代理，它将尝试将视图中的相应组件注入到此代理中。`FXMLLoader` 永远“看不到”原始控制器 Bean 实例，并且任何用 `@FXML` 注解的字段都将保持未初始化状态。任何尝试在控制器上调用使用这些组件的方法都可能导致抛出 `NullPointerException`。

避免此问题的最简单方法是避免将拦截器和作用域与 `FXMLLoader` 的组件注入结合使用，而是让每个控制器类都采用依赖作用域。这并非理想解决方案，因为它意味着每个控制器实例的生命周期可能绑定到 CDI 容器的生命周期。如果某个视图在应用程序中被多次创建，则必须显式销毁控制器实例。如果这些控制器实例未被显式销毁，它们可能会导致内存泄漏，因为在容器关闭之前，它们可能不符合垃圾回收的条件。

### 替换 JavaFX 注入

由于依赖注入是 CDI 的核心功能，因此让 CDI 容器而不是 JavaFX 来注入组件会好得多。这将允许控制器实例使用 CDI 的全部功能，包括作用域和拦截器，同时也允许将 CDI 组件注入到非控制器类中。为实现此目的，您需要实现一个生产者，该生产者能够根据其组件 ID 提供组件。理想情况下，此生产者生成的组件也应限定在当前视图范围内，这样如果应用程序中存在多个活动视图，即使两个组件具有相同的 ID，组件仍将被注入到正确的控制器中。

您需要做的第一件事是定义您的视图作用域。幸运的是，您可以使用 MicroScoped 库（可在 [`https://github.com/tomitribe/microscoped`](https://github.com/tomitribe/microscoped) 找到）来避免创建作用域实现时的大量样板代码。您只需要定义作用域注解并注册作用域上下文。对于您的视图作用域，您将创建一个无参数的 `@ViewScoped` 注解，并使用带有 `String` 参数的 MicroScoped `ScopeContext`。这里使用任何键都可以，但我们将使用转换为 `String` 的 UUID。

接下来，定义用于注入组件的限定符注解。JavaFX 执行的组件注入基于字段的名称。您可以通过向限定符注解添加一个用于组件 ID 的 `String` 参数来复制此行为。通常，像这样的 `String` 参数会被注解为 `@Nonbinding`。这将允许单个生产者方法处理限定符注解的所有实例，无论参数的值如何。然而，在这种情况下，您不会将值参数注解为 `@Nonbinding`，原因稍后会变得清楚。

```
import static java.lang.annotation.ElementType.FIELD;
import static java.lang.annotation.ElementType.METHOD;
import static java.lang.annotation.ElementType.PARAMETER;
import static java.lang.annotation.ElementType.TYPE;
import static java.lang.annotation.RetentionPolicy.RUNTIME;
import java.lang.annotation.Retention;
import java.lang.annotation.Target;
import javax.inject.Qualifier;
@Qualifier
@Retention(RUNTIME)
@Target({METHOD, PARAMETER, TYPE, FIELD})
public @interface Component {
String value();
}
```



现在你已经定义了作用域和限定符注解，接下来需要弄清楚如何让视图中的组件对 CDI 可用。自 Java 9 起，`FxmlLoader` 提供了一种在加载 FXML 文件时检查组件的方式。每个 `FxmlLoader` 可以有一个 `LoadListener`，该监听器会在 FXML 文件解析过程中发生的事件时被调用。`LoadListener` 接口定义了许多回调方法，每个方法对应一个特定事件。对于这个用例，最有趣的回调方法是 `endElement()` 方法。当读取到 FXML 文件中某个 XML 元素的结束标签时，会调用此方法，并将该元素本身作为值传入。例如，当读取到 `Button` 标签的结束时，从该标签创建的新 `Button` 实例会作为值传递给 `endElement()` 方法。由于一个应用程序可能有同一个视图的多个实例，或者不同视图拥有相同的组件 ID，理想情况下，你希望能够将不同视图的组件分开管理。因此，你将创建一个 `@ViewScoped ComponentManager`，用于跟踪视图中的组件及其 ID。`ComponentManager` 将是一个相当简单的类，包含两个方法：一个 `registerComponent()` 方法用于注册组件及其对应的 ID，以及一个 `getComponent()` 方法，用于根据 ID 返回组件。对于这些方法的实现，使用映射（map）来存储和检索组件就足够了。由于组件的 ID 属性是可选的，你只应注册那些指定了 ID 的组件。

可以在控制器上为 FXML 文件中的某些 UI 事件指定回调方法。例如，可以为按钮指定一个控制器方法，当该按钮被点击时调用。在加载 FXML 文件时，事件处理器会被注册到正在加载的视图的组件上，这些处理器会调用控制器上的回调方法。由于控制器本身或其依赖的 bean 也可能是视图作用域的，你需要确保每当视图中的任何组件触发 UI 事件时，视图作用域都是激活的。如果在事件触发时视图作用域未激活，任何对视图作用域 bean 的调用都会导致抛出 `ContextNotActiveException`。每个 JavaFX 组件都有一个可变的 `EventDispatcher` 属性，负责将所有事件分派到正确的事件处理器。通过提供的 setter，可以用自定义实现替换 `EventDispatcher`，该实现可以在分派事件前激活场景作用域，并在分派后禁用它。为了正确分派事件，这个自定义事件分派器还必须包装原始的事件分派器。由于 `EventDispatcher` 接口只有一个抽象方法，因此可以使用相当简单的 lambda 表达式作为实现。事件可以由多个组件的 `EventDispatcher` 处理。如果一个场景被包含在另一个场景中，这可能会成为问题。通过使作用域可堆叠，每个组件的 `EventDispatcher` 都会为其自身的场景启动一个场景作用域实例，这样正确的场景作用域将始终处于激活状态。你可以在 `LoadListener` 实现中为每个组件注册自定义的 `EventDispatcher` 实现。加载 FXML 视图的代码将类似于以下示例：

```
// 为当前视图作用域生成唯一 ID
String viewId = UUID.randomUUID().toString();
// 启动视图作用域
ScopeContext viewScopeContext = (ScopeContext) beanManager.getContext(ViewScoped.class);
viewScopeContext.enter(viewId);
try {
    FXMLLoader fxmlLoader = new FXMLLoader();
    fxmlLoader.setLoadListener(new LoadListener() {
        // 省略 LoadListener 中的其余方法
        @Override
        public void endElement(Object value) {
            if (value instanceof Node) {
                Node node = (Node) value;
                if (node.getId() != null) {
                    componentManager.registerComponent(node.getId(), node);
                }
                // 注册我们自定义的事件分派器
                EventDispatcher eventDispatcher = node.getEventDispatcher();
                node.setEventDispatcher((event, eventDispatcherChain) -> {
                    viewScopeContext.enter(viewId);
                    try {
                        return eventDispatcher.dispatchEvent(event, eventDispatcherChain);
                    } finally {
                        viewScopeContext.exit(viewId);
                    }
                });
            }
        }
    });
} finally {
    viewScopeContext.exit(viewId);
}
```

当你想要编写生成 UI 组件的生产者方法时，会遇到两个问题。第一个问题是，注入点的类型和限定符注解需要与生产者方法完全匹配。不幸的是，JavaFX 提供了许多内置组件类型，应用程序和库也可以定义自己的自定义组件。在一个大型应用程序中，为每种唯一类型提供一个生产者方法将非常不切实际。如果你遗漏了某种类型的生产者方法，也只有在应用程序启动后才会发现这个问题。

另一个问题是，你需要为 JavaFX 组件添加作用域，以便将它们注入到正确的控制器中。所有 JavaFX 组件都继承自 `javafx.scene.Node` 类。`Node` 类包含许多 final 方法，这使得无法对 JavaFX 组件进行代理。回想一下，代理必须与被注入的类型相同。这意味着代理必须扩展组件类型本身才能代理它，并重写所有方法以委托给实际实例。由于 final 方法无法被重写，因此无法将这些方法的调用委托给实际实例，这些方法将仅在代理上执行。

幸运的是，你可以使用 CDI 2.0 中的动态 bean 功能同时解决这两个问题。你可以实现一个 CDI 扩展，监听 `ProcessManagedBeanType` 事件。当发现一个新的 bean 时，你将扫描该 bean 的字段，检查是否有任何字段使用了你的组件限定符注解进行注解，并将这些字段的类型和限定符注解的组合保存在一个 `Set` 中。当扩展上的 `AfterBeanDiscovery` 回调被调用时，你可以使用 `BeanConfigurator` 为你需要的每种类型和限定符注解的组合注册一个动态生产者。在配置你的生产者时，你还可以指定要应用于其生成的 bean 的作用域注解。动态生产者的实际实现由一个回调方法组成，该方法接收一个 `Instance`。你可以在动态生产者中使用这个 `Instance` 来查找之前定义的组件管理器。一旦有了组件管理器，你就可以简单地查找所需的组件。然而，生产者无法读取注入点上指定的限定符注解。这就是为什么将组件 ID 设为限定符注解中的非绑定参数会变得有用，因为你现在可以简单地在动态生产者中对其进行硬编码。对于组件 ID 的每个不同值，你将创建一个新的动态生产者。



由于 `Node` 类中存在 final 方法，导致无法直接代理你的组件，因此你需要让生产者返回一个可被代理的不同类型。这意味着你需要将返回的组件包装在另一个类型中。遗憾的是，这确实意味着无法直接注入组件，而需要通过方法调用来获取组件本身。这个包装类型的唯一要求是：非 final、仅包含非 final 方法，并且有一个返回泛型类型以获取被包装组件的方法。`java.util.function.Supplier` 类型非常适合此场景。它是一个接口，因此可以轻松被代理，并且拥有一个泛型方法。在生产者中查找组件后，你可以返回一个 `Supplier<?>`，该 Supplier 将始终返回你从 `ComponentManager` 获取的组件。

```
import java.lang.reflect.Type;
import java.util.AbstractMap;
import java.util.HashSet;
import java.util.Map.Entry;
import java.util.Set;
import java.util.function.Supplier;
import javax.enterprise.event.Observes;
import javax.enterprise.inject.spi.AfterBeanDiscovery;
import javax.enterprise.inject.spi.AnnotatedField;
import javax.enterprise.inject.spi.Extension;
import javax.enterprise.inject.spi.ProcessManagedBean;
import javafx.scene.Node;
public class FxmlComponentProducerExtension implements Extension {
private static final Set> types = new HashSet();
public  void collect(@Observes ProcessManagedBean event) {
for (AnnotatedField field : event.getAnnotatedBeanClass().getFields()) {
if (field.isAnnotationPresent(Component.class)) {
Component component = field.getAnnotations(Component.class).stream().findFirst().get();
types.add(new AbstractMap.SimpleEntry(component, field.getBaseType()));
}
}
}
public void afterBean(@Observes AfterBeanDiscovery afterBeanDiscovery) {
types.forEach(entry -> {
afterBeanDiscovery
.addBean()
.scope(ViewScoped.class)
.types(entry.getValue())
.addQualifier(entry.getKey())
.produceWith(f -> {
ComponentManager componentManager = f.select(ComponentManager.class).get();
Node component = componentManager.getComponent(entry.getKey().value());
return (Supplier) () -> component;
});
});
}
}
```

现在你已能将组件注入到控制器中，可能需要在视图加载后使用它们执行某些逻辑。例如，在显示当前日期的应用中，控制器可能在加载后更新文本字段以显示日期。在控制器中执行此类逻辑有两种方式；选择哪种方式取决于初始化逻辑应在何时执行。第一种方式是使用 `@PostConstruct` 方法，就像任何常规的 CDI bean 一样。将逻辑放在 `@PostConstruct` 方法中的一个问题是，任何有作用域的 CDI bean 都是延迟初始化的。这意味着 bean 只有在调用其某个方法时才会被创建，例如在 UI 事件后的回调中。这可能导致奇怪的用户体验，即用户界面可能只有在用户执行某些操作（导致控制器上的方法被调用）后才会正确初始化。

`FXMLLoader` 机制本身为这个问题提供了解决方案。在加载视图并创建所有视图组件和控制器后，`FXMLLoader` 会扫描控制器类中名为 `initialize()` 的实例方法，如果找到则调用该方法。调用此方法会产生一个副作用：在 `FXMLLoader` 返回加载的视图之前，会导致控制器实例的初始化；任何放在此方法中的初始化逻辑都将在视图显示之前执行。由于你已经确保视图作用域处于活动状态，因此与通过 CDI 注入的任何组件进行交互都是安全的。对于任何需要在视图显示之前由控制器执行逻辑的情况，`initialize()` 方法比 `@PostConstruct` 方法更可取。

注意：避免将 `initialize` 用作带有 `@PostConstruct` 注解的方法的方法名。JavaFX 会在控制器创建后自动尝试调用该名称的任何返回 `void` 的方法。由于 `@PostConstruct` 方法在构造后已经由 CDI 调用，因此名为 `initialize()` 的 `@PostConstruct` 方法会被调用两次。



### Java 9 模块

Java 9 向 JVM 引入了模块的概念。模块本质上是包含模块描述符的 JAR 文件。模块系统带来的最大变化之一，是实现了比 `public`、`private` 或 `protected` 关键字更精细的访问控制级别。模块可以限制对包的访问，仅允许来自同一模块、仅来自预定义的模块集或任何其他模块的访问。这使得模块能够隐藏其实现细节，使其不成为模块导出 API 的一部分。模块系统还对反射访问引入了更严格的规则。没有模块系统时，代码的访问限制可以轻易地通过反射绕过。然而，对于位于模块中的代码，尝试绕过访问限制将会抛出异常。

隐藏 CDI 库的内部实现非常有用。考虑一个库，它提供了两个位于不同包中的公共类。这两个类的实现都依赖于第三个内部类。如果这两个公共类位于同一个包中，那么只需将内部类也放在该包中，并将其声明为包级私有即可。CDI 仍然能够访问该内部类并将其注入到公共类中，同时防止从包外部轻易访问该内部类。然而，由于这两个依赖于内部类的类位于不同的包中，内部类必须被声明为 public。这可能导致库的用户（意外地）使用本应仅为内部类的类。模块系统通过仅允许访问那些声明为对其他模块导出的 API 来解决此问题。

然而，CDI 规范目前尚未针对模块系统进行更新。对反射访问的更严格检查也会对 CDI 容器产生重大影响，因为它们严重依赖反射。一个例子是将依赖项注入到私有字段中；如果没有反射访问，这是不可能的。默认情况下，CDI 容器也无法访问任何未作为模块导出 API 一部分的类。因此，建议避免在 CDI 应用程序中使用模块系统，至少在该规范被更新以明确处理模块系统之前是这样。

如果确实有强烈的需求要对应用程序进行模块化，有几种选项可以让 CDI 与 JDK 模块系统协同工作。第一种是避免注入到私有字段，而使用构造器注入。然而，这仅能克服依赖注入场景下反射新限制带来的问题。它无法解决使用拦截器装饰非公共类的问题。如果一个 Bean 依赖于大量其他依赖项，构造器也可能变得相当庞大。

一个稍好一些的替代方案是将模块声明为完全开放以进行反射。将模块本身声明为开放以进行反射，意味着它包含的所有包都将变为公共的，并对反射访问开放。假设你有一个名为 `com.foo` 的模块。要将该模块声明为完全开放以进行反射，可以使用以下模块描述符。该模块有一个名为 `com.foo.a` 的包，其中包含一个名为 `PublicA` 的类；一个名为 `com.foo.b` 的包，其中包含一个名为 `PublicB` 的类；以及一个名为 `com.foo.c` 的包，其中包含一个名为 `InternalC` 的类。`PublicA` 和 `PublicB` 都是该模块官方 API 的一部分；这些是模块用户可以自由使用的类。`InternalC` 类是模块的一个内部类，被 `PublicA` 和 `PublicB` 共同使用。`InternalC` 应仅在模块内部使用，模块用户绝不应使用它。

以下是一个将模块开放以进行反射的模块描述符：

```
open module com.foo {
}
```

这种方法允许 CDI 通过反射访问任何私有字段或类，但它也会使模块中的每个公共类都成为模块导出 API 的一部分。假设该模块包含三个 Bean 类：两个应作为公共 API 一部分的类，名为 `com.foo.a.PublicA` 和 `com.foo.b.PublicB`，以及一个被注入到 `PublicA` 和 `PublicB` 中的内部 Bean 类，名为 `com.foo.c.InternalC`。由于 `PublicA` 和 `PublicB` 位于不同的包中，`InternalC` 必须是 public 的。然而，将模块声明为完全开放意味着任何公共类都会成为模块公共 API 的一部分。在这种情况下，`InternalC` 无意中成为了 `com.foo` 模块公共 API 的一部分。

另一种方法是明确将 `com.foo.a` 和 `com.foo.b` 包声明为模块导出 API 的一部分。为了使 CDI 能够访问并注入 `InternalC` 类，必须明确将 `com.foo.c` 包声明为对反射开放。为了将 `InternalC` 注入到 `PublicA` 和 `PublicB` 类的非公共字段中，它们的包也必须被明确声明为对反射访问开放；仅将这些包声明为公共 API 的一部分是不够的。模块描述符将类似于以下内容：

```
module com.foo {
exports com.foo.a;
exports com.foo.b;
opens com.foo.a;
opens com.foo.b;
opens com.foo.c;
}
```

这冗长得多，但它能达到你想要的效果吗？`InternalC` 类在编译时对导出的 API 隐藏，同时在运行时仍可通过反射被 CDI 容器访问。CDI 容器在执行依赖注入时也能够访问 `PublicA` 和 `PublicB` 类中的任何私有字段。然而，`InternalC` 类不仅可以通过反射被 CDI 容器访问；它也可以通过反射被任何代码访问。一个了解 `InternalC` 类存在的狡猾模块用户，如果他们愿意，仍然可以访问它。

在未来某个时候，CDI 规范可能会更新以支持模块系统。这种支持的一部分将包括提供一个 API 模块。目前，已经存在一个被某些容器使用的官方 API JAR，但一个 API 模块将在模块系统内带来一些额外的好处。API 模块可以将其访问权限委托给任何为该 API 提供实现的模块。由于模块只能将包导出或开放给特定的模块，这意味着一旦 CDI 规范定义了一个 API 模块，就可以授予任何 CDI 容器反射访问权限，而无需在编译时知道将使用哪个容器。假设 CDI 定义了一个名为 `cdi.api` 的 API 模块；那么你的模块的 `module-info` 将类似于以下内容：

```
module com.foo {
exports com.foo.a;
exports com.foo.b;
opens com.foo.a to cdi.api;
opens com.foo.b to cdi.api;
opens com.foo.c to cdi.api;
}
```

理论上，使用 CDI 2.0 容器已经可以做到这一点。模块系统会为在模块路径上找到的任何非模块化 JAR 文件生成一个模块名称。生成的模块名称基于文件名。例如，一个名为 `cdi-api.jar` 的 JAR 文件将获得模块名称 `cdi.api`。也可以使用这个模块名称来限制对某个模块的反射访问。然而，这样做并不可取。不同的 CDI 容器可能对 API JAR 使用不同的文件名，导致模块名称不兼容。在 CDI 容器之间切换将需要将对旧容器 API 模块的所有引用替换为对新容器 API 模块的引用。对于一个总是在同一个容器中运行的应用程序来说，这可能没问题，但对于一个需要能在多个不同容器上运行的库来说，这并不理想。更糟糕的是，同一容器的不同版本可能使用不同的文件名，导致同一容器的不同版本彼此不兼容。除此之外，如果 CDI 规范更新并为 API JAR 文件指定了一个模块名称，这个名称很可能会与生成的模块名称不同。

目前，尚不清楚模块系统在 JDK 之外会被广泛采用到什么程度，也不清楚 CDI 是否会开始支持模块系统。尽管前面的内容证明了 CDI 和模块系统目前可以协同工作，但之前建议采取观望态度的建议仍然有效。



### 索引

### A

`addBeanClasses()` 方法 Apache DeltaSpike 容器控制 Apache OpenWebBeans 应用作用域 `@ApplicationScoped` 注解 面向切面编程 (AOP) 异步事件 `CompletionException` `CompletionStage` `fireAsync()` 方法 观察者方法 `@Observes` 注解 `@ObservesAsync` 注解 请求作用域 `thenAccept()` 方法

### B

Bean Bean 的标识 通过名称 EL 名称 `@Named` 通过类型 EJB 3 `Object.class` `SomeCDIBean.class` 通过类型和限定符 参见“通过类型和限定符”，Bean `BeanManager` 引导 API `GreetingService` Java SE `SeContainer` 接口 `SeContainerInitializer` 接口 `ServiceLoader` 机制 `setProperty()` 方法 Weld 内置作用域，覆盖 可移植扩展 `SessionScope` ByteBuddy 库 字节码操作 通过类型和限定符，Bean `AnnotationLiteral` 属性 基于枚举的属性 `InjectionPoint` 内部生产者 JSF `key` 属性 标记接口 `@NonBinding` 注解 后创建初始化 限定符注解 `SomeCDIBean`

### C

`CompletionStage` 接口 组件模型 CDI Bean 优势 可拦截 生命周期可管理 字段注入 参数注入 服务定位器模式 setter 方法 可命名 有作用域 可替换 可组合拦截器 `computeIfAbsent()` 方法 `ConcurrentHashMap` 条件事件观察者 构造器拦截器 `@AroundConstruct` 方法 `@AroundInvoke` 方法 `getConstructor()` 方法 `InvocationContext.proceed()` 方法 上下文 CDI 容器 上下文对象 `get(Contextual<T> contextual, CreationalContext<T> creationalContext)` 方法 `javax.enterprise.context.spi.Context` 接口 `@RequestScoped` 注解 上下文与依赖注入 (CDI) 后台 Bean 组件模型 概念 绑定类型/绑定注解 比较 `@DataSourceDefinition` 注解 `@EJB` 注入注解 `@Inject` 注解 JSR 299，公开评审投票 `@ManagedBean` 注解 包文档 `PostConstruct` 接口 `PreDestroy` 接口 `@Transactional` 注解 Web Beans 部署描述符 分布式对象 EJB 1.0 历史 下一代 面向对象模式 POJO 远程接口 `setSessionContext` 上下文方法 未检查/不可调用 会话作用域 `begin()` 方法 `cid` 参数 `getID()` 方法 生命周期 长时间运行 超时值 类型 `@ConversationScoped` 注解 `create()` 方法 `createWith()` 方法 自定义方法参数注入 `CDI.current().select(...)` `@Inject` `InvocationHandler` `isQualifier` 方法 Java SE 类 观察者方法 代理生成方法 返回值 `SomeBean::getHello` 自定义作用域 创建 `@Inherited` 注解 `@NormalScope` 注解 可钝化 可移植扩展 `@Retention` 注解 `@Target` 注解 线程

### D

数据模块 装饰器 CDI `FooBean` `GreetingBean` `GreetingDecoratorBean` 初始化器/构造器参数 `@Inject` `@Delegate` 有资格的被委托者，注入 限定符注解 `@Dependent` 注解 `disableDiscovery()` 方法 领域特定语言 (DSL) 动态 Bean `Bean<T>` 实例 创建 `Bean<T>` 接口 上下文相关 核心 Bean 属性 `isAlternative()` 方法 序列化支持 使用扩展 CDI 运行时 通用生产者 实现 逻辑组 普通 Java 类型

### E

`ELResolver` 企业 JavaBean (EJB) `EventMetadata` 事件观察者 条件事件 元数据 观察者排序 事件 触发 `BeanManager` 事件传递 事件接口 事件作用域 `fireEvent()` 方法 泛型类型 `@Inject` 注解 `javax.enterprise.event.Event` 接口 观察者异常 观察者方法 `@OrderScoped` 注解 `String` 类型 事件事务 `AFTER_COMPLETION` `AFTER_FAILURE` `AFTER_SUCCESS` `BEFORE_COMPLETION` `IN_PROGRESS` 阶段 `@Observes` 注解 事务性观察者 事件类型 事件传递 事件监听器 表达式语言 (EL)

### F

`fireAsync()` 方法 `fireEvent()` 方法 `@FlowScoped` `FooInterface` `foo()` 方法 FXML `FXMLLoader` 机制

### G

`getBean()` 方法 `getContextMetadata()` 方法 `getGoodByMessage()` 方法 `getGreetingMessage()` 方法 `getID()` 方法 `getInjectionPoint()` 方法 `getName()` 方法 `getPassivationCapableBean()` 方法 `getQualifiers()` 方法 `getScope()` 方法 `getType()` 方法 `getTypes()` 方法

### H

Highlander 规则 HTTP 应用

### I

`IdentityStoreHandler` `@Inject` 注解 `InjectionPoint` `@Inject` `SomeInterface` `InterceptionFactory` 类 拦截器 激活 AOP 应用 绑定注解 通用方法 `@Interceptors` `MyInterceptorBinding` 缓存 缓存方法 调用序列 CDI `ClassCastException` 上下文元数据 EJB 异常 `getMethod()` 方法 `getParameters()` 方法 `getTarget()` 方法 `getTimer()` 方法 `InvocationContext` 参数 JASPIC 认证模块 覆盖方法 `@Priority` 注解 `proceed()` 方法 生产者方法 `setParameters()` 方法 `@Transactional` 注解 `@Interceptors` 注解

### J

JavaFX `Application` 类 `Callback` 接口 FXML `@FXML` 注解 `FXMLLoader` `init()` 方法 注入，替换 `ComponentManager` `EventDispatcher` `FXMLLoader` 机制 `java.util.function.Supplier` 类型 `LoadListener` `Node` 类 `@NonBinding` `@PostConstruct` 方法 `ProcessManagedBeanType` 事件 `String` 参数 try-with-resources 块 Java 9 模块 API JAR API 模块 CDI 容器 JAR 文件 JDK 系统模块描述符 Java SE 内置作用域 请求作用域 测试 `@Alternative` `@BeforeAll` 方法 CDI 容器 `disableDiscovery()` 方法 `GreetingService` `selectAlternatives()` 方法 Java Servlet Faces (JSF) Java 事务 API (JTA) `@javax.annotation.Priority` 注解 `javax.enterprise.inject.scan.implicit` 属性 `javax.interceptor.@Interceptor` 注解 `javax.interceptor.Interceptor.Priority` 类 `javax.transaction.TransactionScoped` 注解 Johnson, Rod

### K

King, Gavin

### L

`@Language` 注解 Lee, Bob 生命周期回调 `init()` 方法 `@PostConstruct` 注解方法 `@PreDestroy` 注解方法 `LoadListener` 接口 `@Local/@Remote` 注解

### M

托管 Bean `@ManagedProperty` 注解 `Map.compute()` 方法 `MicroScoped` 库 多个 Bean 检索 `@Any` 限定符 `@Default` `Instance` 容器 `Stream`

### N

`@Named` 注解 命名，组件模型 组件特定的表达式语言 JNDI 命名系统 `new()` 运算符 `@NonBinding` 不可代理类型 手写代理或包装器 有作用域的 Bean

### O

观察者方法 `@Default` 限定符 事件参数 `@Observes` `@Observes` `@X` `String` 参数

### P

`@Parallel` 限定符 可钝化作用域 普通 Java 对象 (POJO) 可移植扩展 `AfterBeanDiscovery` 方法 `AtomicReference` `BeanInstance` `BeanManager` `BeforeBeanDiscovery` 对象 `ContextNotActiveException` `@CustomAppScoped` `destroy()` 方法 `get()` 方法 `javax.enterprise.inject.spi.Extension` 接口 作用域 ID `ServiceLoader` `@PostConstruct` 方法 `PriceList` 代理 `Prioritized` 接口 `@Priority` 注解 `Process∗Bean` 事件 `produceWith()` 方法

### Q

限定符

### R

请求作用域 `activate()` 方法 `@ActivateRequestContext` 应用代码 `deactivate()` 方法 生命周期 `@PostConstruct` 方法 `RequestScopeController` `RequestScopeController` 类 解析 `@RolesAllowed` 注解

### S

作用域 `@FlowScoped` 初始化事件 注入的代理 可钝化 生产者字段 被代理类型 `@RequestScoped` 注解 限制 `@ThreadScoped` 注解 事务作用域 `@ViewScoped` 注解 `selectAlternatives()` 方法 `@Sequential` 限定符 `ServiceHandler` 会话作用域 `SomeCDIBean` `someMethod()` `SomeQualifierOne` 限定符 `@Stateful/@Stateless` 注解 `String sayHello(String name)` 方法 合成 Bean 归档 `addBeanClasses()` 方法 `addPackages()` 方法 `disableDiscovery()` 方法 `enableDecorators()` 方法 `enableInterceptors()` 方法 `selectAlternatives()` 方法 `setClassLoader()` 方法

### T

`T create()` 方法 `thenAccept()` 方法 `thenRun()` 方法 `@ThreadScoped` 作用域/上下文 `@TransactionScoped` 作用域/上下文 类型安全解析

### U

用户请求，并发

### V, W, X, Y, Z

`@ViewScoped` 注解
