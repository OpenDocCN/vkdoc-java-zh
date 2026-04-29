# 4. 生命周期

在本章中，我们将扩展示例应用程序，并了解 Spring 中的生命周期选项。我们将介绍如何在 Spring Bean 被创建或销毁时调用方法，以及如何通过多种可配置选项（使用 Spring XML 文件、注解或编程配置，所有这些都在第 3 章中使用过）来实现这一点。

## 生命周期简介

每个对象在 Java 虚拟机中都有一个生命周期。当一个对象被创建时，它会经历一系列初始化阶段（在类级别，然后在实例级别），然后调用类的构造函数。当（并且如果）该对象变得对程序代码不可达时，该对象可能会被垃圾回收器清理，而垃圾回收器本身可能会调用特殊命名的方法，以便对象可以清除任何已分配的资源，这个过程称为终结化。^(⁵⁹)

依赖注入框架通过为生命周期事件添加回调，或提供依赖上下文在完成某些操作时将调用的钩子，为对象的生命周期增加了额外的控制。Spring 还在给定上下文中提供了作用域，以控制资源是否被视为单例。

让我们首先来看看作用域。



### 作用域

Spring 为标准 `ApplicationContext` 中的对象提供了两种基本作用域：`singleton`（单例）和 `prototype`（原型）。

设置为 `singleton` 作用域（默认）的对象，在给定的应用上下文中**仅**被创建和销毁一次。如果你多次获取该对象，每次都会得到同一个对象引用。

设置为 `prototype` 作用域的对象则在每次获取时被构造，并且每次从上下文中获取该类型的对象时，你都会得到一个唯一的对象；在这种情况下，Spring 的行为类似于一个构建器^(⁶⁰)。如果你有一个状态会发生改变的对象，但一旦改变后，该对象对其他任何进程都无用了，那么你可以使用此作用域。

构建器实际上是这种模式的一个绝佳示例。在构建器模式的许多实例中，先创建构建器，然后向其中添加数据。当数据被认为是“完整”时，你会调用 `build()` 方法（或类似方法），该方法会从构建器中生成一个完整的对象。一旦构建器交付了它所构建的内容，它对于构建其他任何东西就无用了，因为它包含了所构建对象独有的状态；然后你会丢弃该构建器，如果需要另一个对象，你会创建一个新的构建器来创建它。用 Spring 的术语来说，如果需要的话，构建器将是理想的原型。

实际上，通过 Spring 的 Web 模块还可以获得其他作用域，但我们将在第 6 章中讨论它们，在那里它们更合适。

让我们看一个简单的示例来展示作用域的实际效果。我们将为本章创建一个项目（名为 `chapter04`，因为我们在本书中相当有原创性），并编写一个快速测试来演示如何定义对象作用域以及这些作用域可能产生的影响。

首先，我们需要在项目根目录下创建目录结构。

```
mkdir -p chapter04/src/main/java/com/bsg6/chapter04
mkdir -p chapter04/src/test/java/com/bsg6/chapter04
mkdir -p chapter04/src/test/resources
清单 4-1
使用 POSIX 命令创建目录结构
```

我们还需要一个 `pom.xml` 文件，它将为本章添加一个依赖项——`jakarta.annotation-api`，其中包含一个或两个注解，我们将在本章后面用它们来演示对象构造后可能发生的事情。

```

4.0.0

com.apress
bsg6
1.0

chapter04
1.0

jakarta.annotation
jakarta.annotation-api
2.1.1

清单 4-2
chapter04/pom.xml
```

现在是时候更有趣一些了。让我们创建一个抽象类来保存一条数据——名为 `HasData`——我们将用多个嵌入测试代码的类来扩展它。我们可以这样做，因为我们实际上并不是为了演示生命周期之外的其他目的而创建类，而且这些类不是公共类^(⁶¹)。

```
package com.bsg6.chapter04;
import java.util.Objects;
abstract class  HasData {
String datum = "default";
public String getDatum() {
return datum;
}
public void setDatum(String datum) {
this.datum = datum;
}
@Override
public boolean equals(Object o) {
if (this == o) return true;
if (!(o instanceof HasData)) return false;
HasData hasData = (HasData) o;
return Objects.equals(getDatum(), hasData.getDatum());
}
@Override
public int hashCode() {
return Objects.hash(getDatum());
}
}
清单 4-3
chapter04/src/main/java/com/bsg6/chapter04/HasData.java
```

`equals()` 方法比它本可能的样子稍微复杂一些，因为它必须接受 `HasData` 的子类（实际上，它别无选择；由于 `HasData` 是抽象的，你永远不会有 `HasData` 的实际实例）。同样值得注意的是，IDE 可以（并且确实）生成这些方法；每个方法都是由 IDEA 自动生成的。（顺便说一句，Eclipse 会生成等效的代码；只是本书作者在这里使用了 IDEA。）

为什么 `equals()` 需要接受子类？嗯，这实际上取决于用例。在这里，我们别无选择，只能接受与 `HasData` 不同类的东西，因为 `HasData` 是抽象的——除非定义发生变化，否则永远不会有此类型的有效实例。然而，如果我们谈论的是**具体**类型，那么你可能仍然希望接受子类——因为许多像 Hibernate ([`https://hibernate.org`](https://hibernate.org)) 这样的框架会为实体生成代理作为子类，使得它们看起来完全像实体类，但具有根据需要与数据库交互的代码。`HasData` 不太可能成为 Hibernate 的实体，但在可能的情况下做正确的事情是一个好习惯。

要将对象设置为原型，我们只需在配置中添加 `scope` 属性。（我们将首先展示 XML 配置，因为我们发现它在细粒度控制事件方面比注解更明确。）这两个值——令人惊讶的是——是 `prototype` 和 `singleton`，尽管 `singleton` 是默认值，因此完全不是必需的。

我们定义了两个 bean——`foo` 和 `bar`——都是相同的类型，因为我们可以通过名称获取对象——但具有不同的作用域。

```

清单 4-4
chapter04/src/test/resources/config-01.xml
```

在我们的测试中，我们有一个数据提供者，它引用引用名称本身，以及一个标志，用于告诉测试这些类型是否被视为不同的。我们的测试通过名称获取指定类型的单个实例，然后将其从原始状态改变。然后它用相同的名称获取另一个实例。

如果类型被指定为单例，那么这些对象应该是同一个**实例**（即 `o1 == o2`），并且在一个对象中设置数据也应该反映在另一个对象中（换句话说，既有实例相等性，也有数据相等性）。

如果类型被指定为*原型*，那么这些对象应该**不是**同一个实例，并且在一个对象中设置数据应该使另一个对象保持其默认状态。

```
package com.bsg6.chapter04;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.ApplicationContext;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.testng.AbstractTestNGSpringContextTests;
import org.testng.annotations.DataProvider;
import org.testng.annotations.Test;
import java.util.UUID;
import static org.testng.Assert.*;
class FirstObject extends HasData {
}
@ContextConfiguration(locations = "/config-01.xml")
public class TestLifecycle01 extends AbstractTestNGSpringContextTests {
@Autowired
ApplicationContext context;
@DataProvider
Object[][] getReferences() {
return new Object[][]{
{"foo", true},
{"bar", false}
};
}
@Test(dataProvider = "getReferences")
public void testReferenceTypes(String name, boolean singleton) {
HasData o1 = context.getBean(name, HasData.class);
String defaultValue = o1.getDatum();
o1.setDatum(UUID.randomUUID().toString());
HasData o2 = context.getBean(name, HasData.class);
if (singleton) {
assertSame(o1, o2);
assertEquals(o1, o2);
assertNotEquals(defaultValue, o2.getDatum());
} else {
assertNotSame(o1, o2);
assertNotEquals(o1, o2);
assertEquals(defaultValue, o2.getDatum());
}
}
}
清单 4-5
chapter04/src/test/java/com/bsg6/chapter04/TestLifeCycle01.java
```



### 调用构造函数

在第 3 章中，我们了解了如何为 Spring Bean 指定构造函数参数，可以通过命名参数或使用参数索引来实现。（最直接的方法是使用命名参数，如果存在多个参数的话，尽管使用 `@Autowired` 引用可能更简单。但本节我们坚持使用 XML。）第 3 章对此进行了相当详尽的讲解，但为了完整性，我们在此提供一个简短的示例。

```

清单 4-6
chapter04/src/test/resources/config-02.xml
```

请注意 `constructor-arg` 指令中 `name` 属性的使用——它映射到名为 `initialValue` 的实际参数。

现在是一个利用该配置的测试。

```
package com.bsg6.chapter04;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.ApplicationContext;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.testng.AbstractTestNGSpringContextTests;
import org.testng.annotations.Test;
import static org.testng.Assert.assertEquals;
class SecondObject extends HasData {
SecondObject(String initialValue) {
setDatum(initialValue);
}
}
@ContextConfiguration(locations = "/config-02.xml")
public class TestLifecycle02 extends AbstractTestNGSpringContextTests {
@Autowired
ApplicationContext context;
@Test
public void validateConstruction() {
HasData o1 = context.getBean(HasData.class);
assertEquals(o1.getDatum(), "Initial Value");
}
@Test
public void validateSecondObjectScan() {
// 我们可以通过其层次结构中的任何唯一标识来查找实例。
SecondObject o2 = context.getBean(SecondObject.class);
assertEquals(o2.getDatum(), "Initial Value");
}
}
清单 4-7
chapter04/src/test/java/com/bsg6/chapter04/TestLifeCycle02.java
```

我们的测试更简单，因为我们需要做的只是确保 Spring 构建的对象具有预期的数据值。

另外，请注意 `context.getBean()` 如何查找匹配的对象：我们进行了两个测试，分别使用 `HasData` 和 `SecondObject` 查找 Bean，并验证实例所引用的数据在两种情况下是否相同（如配置所指定）。

### 在构造之后和销毁之前调用方法

我们还可以在对象构造之后，以及（奇怪的是）在上下文丢弃其管理的所有引用之前调用方法。在 XML 中，这是通过 `init-method` 和 `destroy-method` 属性完成的。

`destroy-method` 与 Java 终结器不同，尽管它可以扮演**某些**相同的角色；如果 `ApplicationContext` 能够被关闭（通过命名恰当的 `close()` 方法，该方法首次出现在类层次结构中的 `ConfigurableApplicationContext` 中），上下文将在**上下文关闭之前**调用这些方法。这与终结化不同；如果类具有终结器或使用 Java 9 的 `Cleaner` 工具，这些方法将在垃圾回收发生之前被调用。

请注意，`destroy-method` 仅适用于**单例** Spring Bean。原型 Bean 由 Spring 创建，一旦被检索，就被视为不受管理；Spring 将构造它们并将其传递给您的代码，然后忘记这些实例的存在。因此，`destroy-method` 指示的方法不会在原型实例上被调用；这**可能**没问题，因为无论如何 `destroy-method` 应该很少使用，毕竟人们一开始也很少关闭 Spring 上下文。

换句话说，`destroy-method` 的存在是为了让您能够精细控制 Spring Bean **可能**发生某些事情的时间点，但在普通开发中不太可能有用。不过，这并不会阻止我们演示它！

对于这两种方法，它们需要遵循一个约定。它们可以命名为任何有效的名称（因为我们指定了要调用哪些方法），但这些方法不能有返回值（必须声明为 `void` 类型），并且不能有方法参数。

以下是一个指定了这些方法的配置。

```

清单 4-8
chapter04/src/test/resources/config-03.xml
```

这意味着当 Spring 创建 `ThirdObject` 时，它将在所有构造完成后调用 `init()`，就像执行了以下代码片段一样。

```
ThirdObject foo=new ThirdObject();
foo.init();
清单 4-9
模拟 init-method 的代码片段
```

我们的测试源码将定义 `ThirdObject` 并创建一个名为 `semaphore` 的静态引用。（它并不是**真正**的信号量，但它……可能算是？）此引用**仅**由 `init` 方法设置；因此，我们可以验证是否遵循了正确的初始化方法。我们还定义了一个 `destroy` 方法，它将 `semaphore` 引用设置为 null；它不是一个真正的信号量，但在我们的测试中它有点像信号量。

注意

在编程术语中，信号量是用于控制对资源访问的对象。例如，您可以使用信号量来指示某个对象正被特定线程使用，因此其他线程应等待该对象“可用”后再访问它。Java 的标准库以各种形式内置了信号量，但此处仅出于示例目的使用了一种“手动信号量”。

实际的测试本身使用 `ConfigurableApplicationContext` 而不是 `ApplicationContext`，因为我们希望在从中检索对象后显式关闭上下文。`ApplicationContext` 接口没有定义 `close()`，而 `ConfigurableApplicationContext` 实现了 `Closeable`（以及 `ApplicationContext` 和 `Lifecycle`）。方法可见性是我们使用 `ConfigurableApplicationContext` 的**唯一**原因——如果不是因为 `ApplicationContext` 上没有定义 `close()`，它本来也适用于此测试。

测试方法从应用程序上下文中获取一个 Bean，然后运行两个简单的验证：一个是 Bean **实例**看起来正确（即，它具有我们期望的数据），另一个是 `semaphore` 已被填充。

然后我们关闭上下文并再次检查 `semaphore` 引用——如果 `dispose()` 已被调用，该引用将被设置为 `null`。

```
package com.bsg6.chapter04;
import org.springframework.context.ConfigurableApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;
import org.springframework.test.context.ContextConfiguration;
import org.testng.annotations.Test;
import static org.testng.Assert.*;
class ThirdObject extends HasData {
static Object semaphore = null;
public void init() {
semaphore = new Object();
}
public void dispose() {
semaphore = null;
}
}
@ContextConfiguration(locations = "/config-03.xml")
public class TestLifecycle03 {
@Test
public void testInitDestroyMethods() {
ConfigurableApplicationContext context
=new ClassPathXmlApplicationContext("/config-03.xml");;
ThirdObject o1 = context.getBean(ThirdObject.class);
assertNotNull(ThirdObject.semaphore);
assertEquals(o1.getDatum(), "default");
context.close();
assertNull(ThirdObject.semaphore);
}
}
清单 4-10
chapter04/src/test/java/com/bsg6/chapter04/TestLifeCycle03.java
```



### 生命周期监听器

监听生命周期事件还有更多选项：`InitializingBean` 和 `DisposableBean` 接口。这些接口分别定义了 `afterPropertiesSet()` 和 `destroy()` 方法，这些方法将在适当的时机被调用（即在构造完成且所有属性设置完毕后，以及在上下文释放对 bean 的引用之前）。从功能上讲，这与 XML 配置中的 `init-method` 和 `destroy-method` 属性完全相同；主要区别在于，这些接口直接将你绑定到 Spring 的接口上，而理论上 `init-method` 和 `destroy-method` 引用则不会。^(⁶²)

与我们的其他测试一样，这里有一个描述 `FourthObject` 的配置文件。请注意，其中没有描述任何生命周期方法。

```

清单 4-11
chapter04/src/test/resources/config-04.xml
```

现在，让我们看看我们的测试。实际的测试本身在功能上等同于 `TestLifecycle03` 测试，主要区别在于所使用的类类型是 `FourthObject` 而不是 `ThirdObject`。`FourthObject` 类本身实现了 `InitializingBean` 和 `DisposableBean` 接口，因此分别用 `afterPropertiesSet` 和 `destroy` 方法替代了 `ThirdObject` 的 `init` 和 `dispose` 方法。

```
package com.bsg6.chapter04;
import org.springframework.beans.factory.DisposableBean;
import org.springframework.beans.factory.InitializingBean;
import org.springframework.context.ConfigurableApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;
import org.testng.annotations.Test;
import static org.testng.Assert.*;
class FourthObject extends HasData
implements InitializingBean, DisposableBean {
static Object semaphore = null;
@Override
public void afterPropertiesSet() throws Exception {
semaphore = new Object();
}
@Override
public void destroy() throws Exception {
semaphore = null;
}
}
public class TestLifecycle04 {
@Test
public void testLifecycleMethods() {
ConfigurableApplicationContext context
= new ClassPathXmlApplicationContext("/config-04.xml");
FourthObject o1 = context.getBean(FourthObject.class);
assertNotNull(FourthObject.semaphore);
assertEquals(o1.getDatum(), "default");
context.close();
assertNull(FourthObject.semaphore);
}
}
清单 4-12
chapter04/src/test/java/com/bsg6/chapter04/TestLifeCycle04.java
```

## 使用 JSR-250 注解的生命周期

注解提供了 XML 配置所提供的大部分（但并非**全部**）功能。主要区别在于构造过程；大多数其他生命周期功能要么是相同的（使用 `InitializingBean` 和 `DisposableBean` 接口），要么有直接的对应项（例如，使用包含 `init-method` 方法名的注解）。让我们逐一介绍这些功能，从组件作用域开始。

### 带作用域的注解 Bean

作用域可以通过注解来演示，这很合乎逻辑：只需添加带有正确作用域名称的 `@Scope` 注解即可。我们的示例会比 XML 示例稍微复杂一些，因为我们需要两种不同的类型，以便它们被不同地注解。不过，我们将只使用**一个**配置文件来进行所有注解生命周期测试，如下所示。

```

清单 4-13
chapter04/src/test/resources/annotated.xml
```

这是一个使用两种注解组件类型 `FifthObject` 和 `SixthObject` 的有效测试。它与我们的 `TestLifecycle01` 类非常相似，主要区别在于组件类本身，并且我们使用组件类来查找引用，而不是使用命名引用。

```
package com.bsg6.chapter04;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.config.ConfigurableBeanFactory;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.Scope;
import org.springframework.stereotype.Component;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.testng.AbstractTestNGSpringContextTests;
import org.testng.annotations.DataProvider;
import org.testng.annotations.Test;
import java.util.UUID;
import static org.testng.Assert.*;
@Component
@Scope(ConfigurableBeanFactory.SCOPE_SINGLETON)
class FifthObject extends HasData {
}
@Component
@Scope(ConfigurableBeanFactory.SCOPE_PROTOTYPE)
class SixthObject extends HasData {
}
@ContextConfiguration("/annotated.xml")
public class TestLifecycle05 extends AbstractTestNGSpringContextTests {
@Autowired
ApplicationContext context;
@DataProvider
Object[][] getReferences() {
return new Object[][]{
{FifthObject.class, true},
{SixthObject.class, false}
};
}
@Test(dataProvider = "getReferences")
public void testReferenceTypes(Class clazz, boolean singleton) {
HasData o1 = context.getBean(clazz);
String defaultValue = o1.getDatum();
o1.setDatum(UUID.randomUUID().toString());
HasData o2 = context.getBean(clazz);
if (singleton) {
assertSame(o1, o2);
assertEquals(o1, o2);
assertNotEquals(defaultValue, o2.getDatum());
} else {
assertNotSame(o1, o2);
assertNotEquals(o1, o2);
assertEquals(defaultValue, o2.getDatum());
}
}
}
清单 4-14
chapter04/src/test/java/com/bsg6/chapter04/TestLifeCycle05.java
```

### 带注解类的构造方法

这就是与 XML 配置不同之处：带注解的类将根据可用的信息被调用，并且对象将按照尽可能满足依赖关系的顺序进行构造。这反映了我们在第 3 章中看到的内容；如果构造方法有一个标记了 `@Autowired` 的参数，Spring 将查找与该参数类型和限定符（如果有）匹配的类，并在内部构建依赖关系图后，将其注入到构造方法调用中。

换句话说，如果我们有一个对象 `A` 依赖于对象 `B`，而 `B` 本身又需要一个对象 `C`，那么 Spring 将首先构建 `C`，并使用它来构造 `B`，然后使用 `B` 来构造 `A`。

因此，这里实际上没有什么可展示的，因为这与我们在第 3 章中已经看到的内容相同（有关使用注解进行构造方法注入的完整讨论和示例，请参见 3.4 节）。



### 在构造之后和销毁之前调用方法

这里开始变得有趣了。我们不再为 `init-method` 和 `destroy-method` 命名方法，而是通过从 `pom.xml` 文件导入的 `javax.annotation-api` 工件获得两个注解：`@PostConstruct` 和 `@PreDestroy`。带有这些注解的方法将在对象和上下文生命周期的适当时机被调用，如下面的测试所示。

```
package com.bsg6.chapter04;
import jakarta.annotation.PostConstruct;
import jakarta.annotation.PreDestroy;
import org.springframework.context.ConfigurableApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;
import org.springframework.stereotype.Component;
import org.testng.annotations.Test;
import static org.testng.Assert.*;
@Component
class SeventhObject extends HasData {
static Object semaphore = null;
@PostConstruct
public void initialize() throws Exception {
semaphore = new Object();
}
@PreDestroy
public void dispose() throws Exception {
semaphore = null;
}
}
public class TestLifecycle06 {
@Test
public void testInitDestroyMethods() {
ConfigurableApplicationContext context
= new ClassPathXmlApplicationContext("/annotated-06.xml");
SeventhObject o1 = context.getBean(SeventhObject.class);
assertNotNull(SeventhObject.semaphore);
assertEquals(o1.getDatum(), "default");
context.close();
assertNull(EighthObject.semaphore);
}
}
Listing 4-15
chapter04/src/test/java/com/bsg6/chapter04/TestLifeCycle06.java
```

警告

细心的读者会注意到使用了 `annotated-06.xml` 作为配置文件。该文件与 `annotated.xml` 完全相同——**唯一**的区别在于文件名。这本身并不是测试所要求的，但如果你在单次构建中运行本章的所有测试（例如使用 `mvn package` 或 `mvn test`），`context.close()` 会干扰其他测试的完成。为此测试和下一个测试（使用 `annotated-07.xml`）使用唯一的文件名可以避免此问题。^(⁶³)

```
cp chapter04/src/test/resources/annotated.xml \
chapter04/src/test/resources/annotated-06.xml
cp chapter04/src/test/resources/annotated.xml \
chapter04/src/test/resources/annotated-07.xml
Listing 4-16
Copying annotated.xml for other tests
```

我们仍然可以依赖回调接口本身，即 `InitializingBean` 和 `DisposableBean`。类的注解状态不会改变这些回调的任何内容，如下一个测试所示。

```
package com.bsg6.chapter04;
import org.springframework.beans.factory.DisposableBean;
import org.springframework.beans.factory.InitializingBean;
import org.springframework.context.ConfigurableApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;
import org.springframework.stereotype.Component;
import org.testng.annotations.Test;
import static org.testng.Assert.*;
@Component
class EighthObject extends HasData
implements InitializingBean, DisposableBean {
static Object semaphore = null;
@Override
public void afterPropertiesSet() throws Exception {
semaphore = new Object();
}
@Override
public void destroy() throws Exception {
semaphore = null;
}
}
public class TestLifecycle07 {
@Test
public void testLifecycleMethods() {
ConfigurableApplicationContext context
= new ClassPathXmlApplicationContext("/annotated-07.xml");
EighthObject o1 = context.getBean(EighthObject.class);
assertNotNull(EighthObject.semaphore);
assertEquals(o1.getDatum(), "default");
context.close();
assertNull(EighthObject.semaphore);
}
}
Listing 4-17
chapter04/src/test/java/com/bsg6/chapter04/TestLifeCycle07.java
```

## 使用 Java 配置的生命周期

带注解的 Bean 固然很好，但注解 Bean 会使其配置变为*全局*——如果你设置了带注解的 Bean 的名称，它将对当前类路径中该类的每个实例生效；相比之下，在 XML 中，你可以拥有同一个类的多个 Bean，它们可以有不同的名称和不同的属性值。

我们已经演示了如何使用 XML 和注解将 Bean 设置为不同的作用域；此外，还可以使用配置类，并且作用域在这里的应用方式与其他地方相同。配置类的优势在于，你可以在类路径上同时存在多个配置类，这既提供了注解方法的强大功能和清晰性，又兼具了 XML 方法的灵活性。

让我们再看一个测试。这个测试将创建另一个继承自 `HasData` 的对象类型——这次命名为 `NinthObject`——但配置将存储在一个名为 `Config08` 的本地类中。在该类中，声明了两个方法 `foo()` 和 `bar()`，并且这两个方法都标记了 `@Bean`——这意味着 `foo()` 将描述如何创建名为 `foo` 的 Bean，而 `bar()` 将描述如何创建名为 `bar` 的 Bean。当然，我们可以提供注解指令来重命名 Bean，而不是使用方法名，但这在第 3 章中已经介绍过。

与我们的注解示例一样，我们可以向方法添加 `@Scope("prototype")`，这告诉 Spring 为具有该名称的 Bean 使用适当的作用域。

我们还将测试类的 `@ContextConfiguration` 改为引用 `Config08.class`，而不是命名的配置文件。除此之外，一切看起来都与我们在 `TestLifecycle05.java` 中看到的相似（并且行为相同）——我们有一个数据提供器，它告诉测试该类是否应为单例，并相应地验证实例引用和实例数据。

以下是测试源代码。

```
package com.bsg6.chapter04;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Scope;
import org.springframework.test.annotation.DirtiesContext;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.testng.AbstractTestNGSpringContextTests;
import org.testng.annotations.DataProvider;
import org.testng.annotations.Test;
import java.util.UUID;
import static org.testng.Assert.*;
class NinthObject extends HasData {
}
@Configuration
class Config08 {
@Bean
public NinthObject foo() {
return new NinthObject();
}
@Bean
@Scope("prototype")
public NinthObject bar() {
return new NinthObject();
}
}
@ContextConfiguration(classes=Config08.class)
@DirtiesContext(classMode = DirtiesContext.ClassMode.AFTER_CLASS)
public class TestLifecycle08 extends AbstractTestNGSpringContextTests {
@Autowired
ApplicationContext context;
@DataProvider
Object[][] getReferences() {
return new Object[][]{
{"foo", true},
{"bar", false}
};
}
@Test(dataProvider = "getReferences")
public void testReferenceTypes(String reference, boolean singleton) {
HasData o1 = context.getBean(reference, HasData.class);
String defaultValue = o1.getDatum();
o1.setDatum(UUID.randomUUID().toString());
HasData o2 = context.getBean(reference, HasData.class);
if (singleton) {
assertSame(o1, o2);
assertEquals(o1, o2);
assertNotEquals(defaultValue, o2.getDatum());
} else {
assertNotSame(o1, o2);
assertNotEquals(o1, o2);
assertEquals(defaultValue, o2.getDatum());
}
}
}
Listing 4-18
chapter04/src/test/java/com/bsg6/chapter04/TestLifeCycle08.java
```



## 附加作用域

Spring 就像一个由曲折通道组成的迷宫，全都大同小异^(⁶⁴)，自然会在本章讨论的作用域（`prototype` 和 `singleton` 作用域）之外增加更多内容——这主要是因为附加作用域存在于 Spring Web 的上下文中，将在第 6 章中介绍。作用域的规则不变，但名称和可达性会变；例如，一个组件可以被标记为在单个 HTTP 请求的上下文中作为单例存在，或者在用户会话的上下文中存在。这些作用域将在适当的章节（即第 6 章）中更详细地探讨。第 5 章介绍了 Servlet API 以及从 HTTP 端点访问 Spring 上下文的简单方法，但该方法过于简单，无法提供 Spring 的全部能力。

## 后续步骤

在第 5 章中，我们将转换思路，讨论如何将 Spring 与 Jakarta EE 集成。Spring 从早期开始就一直是企业环境领域的驱动力，我们将介绍一些术语和 API，以及一种相当古老且简单的集成 Spring 的方法，为第 6 章中更有用的内容做准备。

脚注 1   2   3   4   5   6

