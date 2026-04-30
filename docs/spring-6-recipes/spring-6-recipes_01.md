# 1. Spring 核心任务

在本章中，你将学习与 Spring 相关的核心任务。Spring 框架的核心是 Spring 控制反转（IoC）容器。IoC 容器用于管理和配置 POJO（Plain Old Java Objects，普通 Java 对象）。由于 Spring 框架的主要吸引力之一在于使用 POJO 构建 Java 应用程序，因此 Spring 的许多核心任务都涉及在 IoC 容器中管理和配置 POJO。

因此，无论你计划将 Spring 框架用于 Web 应用程序、企业集成还是其他类型的项目，使用 POJO 和 IoC 容器都是使用 Spring 框架的第一步。本章中的大部分示例涵盖了你在本书中以及日常开发 Spring 应用程序时都会用到的任务。

![](img/314861_5_En_1_Figa_HTML.gif)带阴影圆圈内字母 i 的图标代表信息符号。  在本书和 Spring 文档中，“bean”一词与 POJO 实例互换使用。两者均指由 Java 类创建的对象实例。“组件”一词在本书和 Spring 文档中与 POJO 类互换使用。两者均指创建对象实例的实际 Java 类。

## 1-1. 使用 Java 配置来配置 POJO

### 问题

你想使用 Spring 的 IoC 容器通过注解来管理 POJO。

### 解决方案

设计一个 POJO 类。然后，创建一个带有 `@Configuration` 和 `@Bean` 注解的 Java 配置类来配置 POJO 实例值，或者使用 `@Component`、`@Repository`、`@Service` 或 `@Controller` 注解设置 Java 组件，以便后续创建 POJO 实例值。接着，实例化 Spring IoC 容器以扫描带有注解的 Java 类。然后，POJO 实例或 bean 实例即可作为应用程序的一部分进行组装和访问。

### 工作原理

假设你要开发一个生成序列号的应用程序，并且需要为不同目的生成多个序列号系列。每个序列都有自己的前缀、后缀和初始值。因此，你必须为应用程序创建并维护多个生成器实例。

#### 创建要实例化为 Bean 的类

根据需求，你创建一个具有三个属性（`prefix`、`suffix` 和 `initial`）的 `Sequence` 类。你还创建一个私有字段 `counter` 来存储每个生成器的数值。每次在生成器实例上调用 `nextValue()` 方法时，你都会得到带有前缀和后缀拼接的最后一个序列号。

*Sequence 类*

```
package com.apress.spring6recipes.sequence;
import java.util.concurrent.atomic.AtomicInteger;
public class Sequence {
private final AtomicInteger counter = new AtomicInteger();
private String prefix;
private String suffix;
public void setPrefix(String prefix) {
this.prefix = prefix;
}
public void setSuffix(String suffix) {
this.suffix = suffix;
}
public void setInitial(int initial) {
this.counter.set(initial);
}
public String nextValue() {
return prefix + counter.getAndIncrement() + suffix;
}
}
```

#### 使用 `@Configuration` 和 `@Bean` 创建 Java 配置以创建 POJO

要在 Spring IoC 容器中定义 POJO 类的实例，你可以创建一个带有实例化值的 Java 配置类。包含 POJO 或 bean 定义的 Java 配置类如下所示。

*SequenceConfiguration*

```
package com.apress.spring6recipes.sequence.config;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import com.apress.spring6recipes.sequence.Sequence;
@Configuration
public class SequenceConfiguration {
@Bean
public Sequence sequence() {
var seqgen = new Sequence();
seqgen.setPrefix("30");
seqgen.setSuffix("A");
seqgen.setInitial(100000);
return seqgen;
}
}
```

请注意，`SequenceConfiguration` 类使用了 `@Configuration` 注解进行修饰。这告诉 Spring 这是一个配置类。当 Spring 遇到带有 `@Configuration` 注解的类时，它会在该类中查找 bean 实例定义，这些定义是使用 `@Bean` 注解修饰的 Java 方法。这些 Java 方法创建并返回一个 bean 实例。

任何使用 `@Bean` 注解修饰的方法定义都会根据方法名称生成一个 bean 名称。或者，你也可以在 `@Bean` 注解中使用 name 属性显式指定 bean 名称（例如，`@Bean(name="mys1")` 使该 bean 以 `mys1` 名称可用；如果你显式指定了 bean 名称，则方法名称在 bean 创建过程中将被忽略）。



#### 实例化 Spring IoC 容器以扫描注解

你必须实例化 Spring IoC 容器，以便扫描包含注解的 Java 类。在此过程中，Spring 会检测到 `@Configuration` 和 `@Bean` 注解，这样你之后就可以从 IoC 容器本身获取 bean 实例。

Spring 提供了两种类型的 IoC 容器实现。基础的一种称为 bean 工厂。更高级的一种称为应用上下文，它与 bean 工厂兼容。请注意，这两种 IoC 容器的配置文件是相同的。

应用上下文在保持与 bean 工厂基本功能兼容的同时，提供了更高级的特性。因此，我们强烈建议为每个应用程序使用应用上下文，除非应用程序的资源受限（例如，在为小程序或移动设备运行 Spring 时）。bean 工厂和应用上下文的接口分别是 `BeanFactory` 和 `ApplicationContext`。`ApplicationContext` 接口是 `BeanFactory` 的子接口，以保持兼容性。

由于 `ApplicationContext` 是一个接口，你必须实例化它的一个实现。Spring 有多个应用上下文实现。我们建议你使用 `AnnotationConfigApplicationContext`，它是最新且最灵活的实现。使用这个类，你可以加载 Java 配置文件：

```
var context = new AnnotationConfigApplicationContext(SequenceConfiguration.class);
```

一旦应用上下文被实例化，对象引用（在此例中为 context）就提供了一个访问 POJO 实例或 bean 的入口点。

#### 从 IoC 容器获取 POJO 实例或 Bean

要从 bean 工厂或应用上下文获取一个已声明的 bean，你可以使用 `getBean()` 方法并传入唯一的 bean 名称。`getBean()` 方法的返回类型是 `java.lang.Object`，因此在使用前必须将其强制转换为其实际类型：

```
var generator = (Sequence) context.getBean("sequence");
```

`getBean()` 方法还支持另一种变体，你可以提供 bean 的类名以避免进行强制转换：

```
var generator = context.getBean("sequence", Sequence.class);
```

或者，如果只有一个 bean，你可以省略 bean 名称：

```
var generator = context.getBean(Sequence.class);
```

一旦完成这一步，你就可以像使用 Spring 外部通过构造函数创建的任何对象一样使用 POJO 或 bean。

用于运行序列生成器应用程序的 `Main` 类如下所示。

*用于运行序列生成器应用程序的 Main.java*

```
package com.apress.spring6recipes.sequence;
import com.apress.spring6recipes.sequence.config.SequenceConfiguration;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
public class Main {
public static void main(String[] args) {
var cfg = SequenceConfiguration.class;
try (var ctx = new AnnotationConfigApplicationContext(cfg)) {
var generator = ctx.getBean(Sequence.class);
System.out.println(generator.nextValue());
System.out.println(generator.nextValue());
}
}
}
```

如果 Java 类路径中一切就绪（`Sequence` 类和 Spring JAR 依赖项），你应该会看到以下输出，以及一些日志消息：

```
30100000A
30100001A
```

#### 使用 `@Component` 注解创建 POJO 类，通过 DAO 创建 Bean

到目前为止，Spring bean 的实例化都是通过在 Java 配置类中硬编码值来完成的。这是简化 Spring 示例的首选方法。

然而，大多数应用程序的 POJO 实例化过程要么来自数据库，要么来自用户输入。所以现在是时候向前迈进，使用更贴近真实世界的场景了。在本节中，我们将使用一个领域类和一个数据访问对象（DAO）类来创建 POJO。你仍然不需要设置数据库——我们实际上会在 DAO 类中硬编码值——但熟悉这种类型的应用程序结构很重要，因为它是大多数真实世界应用程序和未来食谱的基础。

假设你被要求开发一个类似于上一节中的序列生成器应用程序。我们需要稍微修改类结构以适应领域类和 DAO 模式。首先，创建一个包含 `id`、`prefix` 和 `suffix` 属性的领域类 `Sequence`。

*修改后的 Sequence 类*

```
package com.apress.spring6recipes.sequence;
public class Sequence {
private final String id;
private final String prefix;
private final String suffix;
public Sequence(String id, String prefix, String suffix) {
this.id = id;
this.prefix = prefix;
this.suffix = suffix;
}
public String getId() {
return id;
}
public String getPrefix() {
return prefix;
}
public String getSuffix() {
return suffix;
}
}
```

然后，你为数据访问对象（DAO）创建一个接口，该接口负责从数据库访问数据。`getSequence()` 方法通过 ID 从数据库表中加载一个 POJO 或 Sequence 对象，而 `getNextValue()` 方法检索特定数据库序列的下一个值。

*SequenceDao 接口*

```
package com.apress.spring6recipes.sequence;
public interface SequenceDao {
Sequence getSequence(String sequenceId);
int getNextValue(String sequenceId);
}
```

在生产应用程序中，你会实现这个 DAO 接口以使用某种数据访问技术。但为了简化这个示例，我们将实现一个 DAO，在 map 中硬编码值来存储序列实例和值。

*基于 Map 的 SequenceDao 接口实现*

```
package com.apress.spring6recipes.sequence;
import org.springframework.stereotype.Component;
import java.util.Map;
import java.util.concurrent.ConcurrentHashMap;
import java.util.concurrent.atomic.AtomicInteger;
@Component("sequenceDao")
class SimpleSequenceDao implements SequenceDao {
private final Map sequences = new ConcurrentHashMap();
private final Map values = new ConcurrentHashMap();
SimpleSequenceDao() {
sequences.put("IT", new Sequence("IT", "30", "A"));
values.put("IT", new AtomicInteger(10000));
}
@Override
public Sequence getSequence(String sequenceId) {
return sequences.get(sequenceId);
}
@Override
public int getNextValue(String sequenceId) {
var value = values.get(sequenceId);
return value.getAndIncrement();
}
}
```

注意 `SimpleSequenceDao` 类是如何用 `@Component("sequenceDao")` 注解修饰的。这标记了该类，以便 Spring 可以为其创建一个 bean。`@Component` 注解中的值定义了 bean 实例的 id，在此例中为 `sequenceDao`。如果在 `@Component` 注解中没有提供 bean 值名称，默认情况下 bean 名称会被分配为未大写的非限定类名（例如，对于 `SimpleSequenceDao` 类，默认的 bean 名称将是 `simpleSequenceDao`）。

对 `getSequence` 方法的调用返回给定 `sequenceID` 的值。而对 `getNextValue` 方法的调用则基于给定 `sequenceID` 的值创建一个新值并返回该新值。

@COMPONENT、@REPOSITORY、@SERVICE 还是 @CONTROLLER？

POJO 在应用程序层中进行分类。在 Spring 中，这些层有三层：持久层、服务层和表示层。`@Component` 是一个通用注解，用于修饰 POJO 以便 Spring 检测，而 `@Repository`、`@Service` 和 `@Controller` 是 `@Component` 的特化，用于与持久层、服务层和表示层相关联的更具体的 POJO 场景。

如果你不确定 POJO 的用途，可以使用 `@Component` 注解来修饰它。但是，最好尽可能使用特化注解，因为这些注解基于 POJO 的用途提供了额外的功能（例如，`@Repository` 会导致异常被包装为 `DataAccessExceptions`，这使得调试更容易）。



#### 使用过滤器实例化 Spring IoC 容器以扫描注解

默认情况下，Spring 会检测所有带有 `@Configuration`、`@Component`、`@Repository`、`@Service` 和 `@Controller` 等注解的类。你可以自定义扫描过程，包含一个或多个包含/排除过滤器。当 Java 包中有数十个或数百个类时，这非常有用。对于某些 Spring 应用上下文，可能需要排除或包含带有特定注解的 POJO。

![](img/314861_5_En_1_Figc_HTML.gif)一个带阴影三角形内感叹号的图标。  扫描每个包中的类可能会不必要地减慢启动过程。

Spring 支持四种类型的过滤器表达式。`annotation` 和 `assignable` 类型用于指定注解类型和用于过滤的类/接口。`regex` 和 `aspectj` 类型允许你指定正则表达式和 AspectJ 切入点表达式来匹配类。你还可以使用 `use-default-filters` 属性禁用默认过滤器。

例如，以下组件扫描包含 `com.apress.spring6recipes.sequence` 包中名称包含单词 `Dao` 或 `Service` 的所有类，并排除带有 `@Controller` 注解的类：

```
@ComponentScan(
includeFilters = {
@ComponentScan.Filter(
type = FilterType.REGEX,
pattern = {
"com.apress.spring6recipes.sequence.*Dao",
"com.apress.spring6recipes.sequence.*Service"})},
excludeFilters = {
@ComponentScan.Filter(
type = FilterType.ANNOTATION,
classes = {org.springframework.stereotype.Controller.class})})
```

当应用包含过滤器来检测名称包含单词 Dao 或 Service 的所有类时，即使是没有注解的类也会被自动检测到。

#### 从 IoC 容器获取 POJO 实例或 Bean

然后，你可以使用以下 Main 类测试上述组件：

*用于运行应用程序的 Main.java*

```
package com.apress.spring6recipes.sequence;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
public class Main {
public static void main(String[] args) {
var basePackages = "com.apress.spring6recipes.sequence";
try (var context = new AnnotationConfigApplicationContext(basePackages)) {
var sequenceDao = context.getBean(SequenceDao.class);
System.out.println(sequenceDao.getNextValue("IT"));
System.out.println(sequenceDao.getNextValue("IT"));
}
}
}
```

## 1-2\. 通过调用构造函数创建 POJO

### 问题

你希望通过调用其构造函数在 Spring IoC 容器中创建 POJO 实例或 bean，这是创建 bean 最常见且最直接的方式。它相当于在 Java 中使用 new 运算符创建对象。

### 解决方案

定义一个带有一个或多个构造函数的 POJO 类。接着，创建一个 Java 配置类，通过构造函数为 Spring IoC 容器配置 POJO 实例值。然后，实例化 Spring IoC 容器以扫描带有注解的 Java 类。POJO 实例或 bean 实例变得可访问，并可作为应用程序的一部分组合在一起。

### 工作原理

假设你要开发一个在线销售产品的商店应用程序。首先，你创建 `Product` 类，它有几个属性，例如产品名称和价格。由于你的商店中有多种类型的产品，你将 `Product` 类设为抽象类，以便为不同的产品子类进行扩展。

*Product 基类*

```
package com.apress.spring6recipes.shop;
public abstract class Product {
private final String name;
private final double price;
public Product(String name, double price) {
this.name = name;
this.price = price;
}
public String getName() {
return name;
}
public double getPrice() {
return price;
}
@Override
public String toString() {
return String.format("%s: name=%s, price=$%.2f",
getClass().getSimpleName(), name, price);
}
}
```

#### 创建带构造函数的 POJO 类

然后你创建两个产品子类，`Battery` 和 `Disc`。每个子类都有自己的属性。

*Battery 类*

```
package com.apress.spring6recipes.shop;
public class Battery extends Product {
private final boolean rechargeable;
public Battery(String name, double price, boolean rechargeable) {
super(name, price);
this.rechargeable = rechargeable;
}
public boolean isRechargeable() {
return rechargeable;
}
@Override
public String toString() {
var msg = super.toString() + ", rechargeable=%b";
return String.format(msg, this.rechargeable);
}
}
```

*Disc 类*

```
package com.apress.spring6recipes.shop;
public class Disc extends Product {
private final int capacity;
public Disc(String name, double price, int capacity) {
super(name, price);
this.capacity = capacity;
}
public int getCapacity() {
return capacity;
}
@Override
public String toString() {
var msg = super.toString() + ", capacity=%dMB";
return String.format(msg, this.capacity);
}
}
```

#### 为你的 POJO 创建 Java 配置

要在 Spring IoC 容器中定义类的实例，你必须创建一个带有实例化值的配置类。一个通过调用构造函数来定义 bean 的配置类如下所示。

*商店应用程序的配置*

```
package com.apress.spring6recipes.shop.config;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import com.apress.spring6recipes.shop.Disc;
import com.apress.spring6recipes.shop.Battery;
import com.apress.spring6recipes.shop.Product;
@Configuration
public class ShopConfiguration {
@Bean
public Product aaa() {
return new Battery("AAA", 2.5, true);
}
@Bean
public Product cdrw() {
return new Disc("CD-RW", 1.5, 700);
}
}
```

接下来，你可以编写以下类，通过从 Spring IoC 容器中检索产品来测试它们。

*用于运行应用程序的 Main.java*

```
package com.apress.spring6recipes.shop;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import com.apress.spring6recipes.shop.config.ShopConfiguration;
public class Main {
public static void main(String[] args) {
var cfg = ShopConfiguration.class;
try (var ctx = new AnnotationConfigApplicationContext(cfg)) {
var aaa = ctx.getBean("aaa", Product.class);
var cdrw = ctx.getBean("cdrw", Product.class);
System.out.println(aaa);
System.out.println(cdrw);
}
}
}
```

### 问题

构成应用程序的 bean 通常需要相互协作以完成应用程序的功能。你想使用注解来实现引用和自动装配。

### 解决方案

对于在配置类中定义的实例，你可以使用标准 Java 代码在 bean 之间创建引用。要自动装配引用，你可以使用 `@Autowired` 注解标记字段、setter 方法、构造函数，甚至是任意方法。

### 工作原理



#### 在 Java 配置类中引用 POJO

当 Bean 实例在配置类中定义时——如食谱 1-1 和食谱 1-2 所示——引用使用起来非常直接，因为所有内容都是 Java 代码。例如，当一个 Bean 属性引用另一个 Bean 时：

```
package com.apress.spring6recipes.sequence.config;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import com.apress.spring6recipes.sequence.PrefixGenerator;
import com.apress.spring6recipes.sequence.DatePrefixGenerator;
import com.apress.spring6recipes.sequence.Sequence;
@Configuration
public class SequenceConfiguration {
@Bean
public DatePrefixGenerator datePrefixGenerator() {
return new DatePrefixGenerator("yyyyMMdd");
}
@Bean
public Sequence sequenceGenerator(PrefixGenerator prefixGenerator) {
var generator = new Sequence("A", 100000);
generator.setPrefixGenerator(prefixGenerator);
return generator;
}
}
```

`Sequence` 类的 `prefixGenerator` 属性是 `DatePrefixGenerator` Bean 的一个实例。

第一个 Bean 声明创建了一个 `DatePrefixGenerator` Bean。按照惯例，该 Bean 可通过 Bean 名称 `datePrefixGenerator`（即方法名）访问。但由于 Bean 实例化逻辑也是一个标准的 Java 方法，因此也可以通过标准的 Java 调用来访问该 Bean。当在第二个 Bean 中通过 setter 设置 `prefixGenerator` 属性时，会进行一个标准的 Java 调用，即调用 `datePrefixGenerator()` 方法来引用该 Bean。

#### 使用 @Autowired 注解自动装配 POJO 字段

接下来，让我们对食谱 1-1 第二部分中引入的 DAO 类 `SimpleSequenceDao` 的 `SequenceDao` 字段使用自动装配。我们将在应用程序中添加一个服务类，以演示与 DAO 类的自动装配。

用于生成服务对象的服务类是另一个实际应用的最佳实践，它充当访问 DAO 对象的门面——而不是直接访问 DAO 对象。在内部，服务对象与 DAO 交互以处理序列生成请求：

```
package com.apress.spring6recipes.sequence;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
@Service
public class SequenceService {
@Autowired
private SequenceDao sequenceDao;
public String generate(String sequenceId) {
var sequence = sequenceDao.getSequence(sequenceId);
var value = sequenceDao.getNextValue(sequenceId);
return sequence.prefix() + value + sequence.suffix();
}
}
```

`SequenceService` 类使用了 `@Service` 注解进行修饰。这使得 Spring 能够检测到该类。由于 `@Service` 注解没有指定名称，因此默认的 Bean 名称为 `sequenceService`，该名称基于类名。

`SequenceService` 类的 `SequenceDao` 字段使用了 `@Autowired` 注解进行修饰。这使得 Spring 能够使用 `sequenceDao` Bean（即 `SimpleSequenceDao` 类）自动装配该字段。

`@Autowired` 注解也可以应用于数组类型的字段，以便 Spring 自动装配所有匹配的 Bean。例如，你可以使用 `@Autowired` 注解一个 `PrefixGenerator[]` 字段。然后，Spring 将一次性自动装配所有类型与 `PrefixGenerator` 兼容的 Bean：

```
package com.apress.spring6recipes.sequence;
import org.springframework.beans.factory.annotation.Autowired;
public class Sequence {
@Autowired
private PrefixGenerator[] prefixGenerators;
...
}
```

如果你在 IoC 容器中定义了多个类型与 `PrefixGenerator` 兼容的 Bean，它们将被自动添加到 `prefixGenerators` 数组中。

类似地，你也可以将 `@Autowired` 注解应用于类型安全的集合。Spring 可以读取该集合的类型信息，并自动装配所有类型兼容的 Bean：

```
package com.apress.spring6recipes.sequence;
import org.springframework.beans.factory.annotation.Autowired;
public class Sequence {
@Autowired
private List prefixGenerators;
...
}
```

如果 Spring 注意到 `@Autowired` 注解应用于一个类型安全的 `java.util.Map`，且其键类型为 `String`，那么它会将所有兼容类型的 Bean（以 Bean 名称作为键）添加到该 Map 中：

```
package com.apress.spring6recipes.sequence;
import org.springframework.beans.factory.annotation.Autowired;
public class Sequence {
@Autowired
private Map prefixGenerators;
...
}
```

![](img/314861_5_En_1_Figd_HTML.gif)一个带阴影圆圈内字母 i 的图标，表示信息符号。  在字段上使用 `@Autowired` 并非推荐做法。经验法则是：对于必需字段使用构造器注入，对于可选字段使用 setter 注入。



#### 使用 @Autowired 注解实现方法和构造器的自动装配，并使其可选

`@Autowired` 注解也可以直接应用于类的方法（通常是 setter 方法）。例如，你可以使用 `@Autowired` 注解 `prefixGenerator` 属性的 setter 方法。然后，Spring 会尝试装配一个与 `prefixGenerator` 类型兼容的 bean：

```
package com.apress.spring6recipes.sequence;
import org.springframework.beans.factory.annotation.Autowired;
public class Sequence {
...
@Autowired
public void setPrefixGenerator(PrefixGenerator prefixGenerator) {
this.prefixGenerator = prefixGenerator;
}
}
```

默认情况下，所有带有 `@Autowired` 的属性都是必需的。当 Spring 找不到匹配的 bean 进行装配时，会抛出异常。如果你希望某个属性是可选的，可以使用 `@Autowired` 的 `required` 属性并将其设置为 `false`。这样，当 Spring 找不到匹配的 bean 时，会保持该属性未设置状态：

```
package com.apress.spring6recipes.sequence;
import org.springframework.beans.factory.annotation.Autowired;
public class Sequence {
...
@Autowired(required=false)
public void setPrefixGenerator(PrefixGenerator prefixGenerator) {
this.prefixGenerator = prefixGenerator;
}
}
```

另一种选择是使用 `java.util.Optional`，顾名思义，它可以使注入变为可选。然后，你可以设置检测到的值，或者使用 `orElseGet` 提供一个默认实例。使用 `java.util.Optional` 还会使注入变为惰性；依赖项只有在需要被获取时才会被检索，即在使用 `Optional.get` 等方法时。`java.util.Optional` 也可用于字段或构造器注入：

```
package com.apress.spring6recipes.sequence;
import org.springframework.beans.factory.annotation.Autowired;
public class Sequence {
...
@Autowired
public void setPrefixGenerator(Optional prefixGenerator) {
this.prefixGenerator = prefixGenerator.orElse(null);
}
}
```

除了 Optional，你还可以使用 `org.springframework.beans.factory.ObjectProvider`，这是 Spring 的一个接口，允许你可选地获取 bean。它的 `getIfUnique()` 方法的行为类似于 `Optional.orElse`。它只会在存在唯一一个实例时返回该实例；如果没有或有多个，则返回 `null`。`ObjectProvider` 有多种方法，可以根据需要获取一个或多个 bean 实例。这在构建库、框架或基于插件的系统时的配置类中非常有用：

```
package com.apress.spring6recipes.sequence;
import org.springframework.beans.factory.ObjectProvider;
import org.springframework.beans.factory.annotation.Autowired;
public class Sequence {
...
@Autowired
public void setPrefixGenerator(ObjectProvider prefixGeneratorProvider) {
this.prefixGenerator = prefixGeneratorProvider.getIfUnique();
}
}
```

你也可以将 `@Autowired` 注解应用于具有任意名称和任意数量参数的方法，在这种情况下，Spring 会尝试为每个方法参数装配一个类型兼容的 bean：

```
package com.apress.spring6recipes.sequence;
import org.springframework.beans.factory.annotation.Autowired;
public class Sequence {
...
@Autowired
public void myOwnCustomInjectionName(PrefixGenerator prefixGenerator) {
this.prefixGenerator = prefixGenerator;
}
}
```

最后，你也可以将 `@Autowired` 注解应用于你希望用于自动装配的构造器。该构造器可以有任意数量的参数，Spring 会尝试为每个构造器参数装配一个类型兼容的 bean：

```
@Service
public class SequenceService {
private final SequenceDao sequenceDao;
@Autowired
public SequenceService(SequenceDao sequenceDao) {
this.sequenceDao=sequenceDao;
}
public String generate(String sequenceId) {
var sequence = sequenceDao.getSequence(sequenceId);
var value = sequenceDao.getNextValue(sequenceId);
return sequence.getPrefix() + value + sequence.getSuffix();
}
}
```

![](img/314861_5_En_1_Fige_HTML.gif)一个带有灯泡轮廓的图标。  如果你只有一个构造器，Spring 会自动使用该构造器进行自动装配。在这种情况下，你可以省略 `@Autowired` 注解。

#### 使用 @Primary 和 @Qualifier 注解解决自动装配歧义

默认情况下，当 IoC 容器中存在多个类型兼容的 bean，且属性不是组类型（例如数组、列表、映射）时，按类型自动装配将无法工作，如前所述。然而，如果存在多个相同类型的 bean，有两种解决按类型自动装配问题的方法：`@Primary` 注解和 `@Qualifier` 注解。

##### 使用 `@Primary` 注解解决自动装配歧义

Spring 允许你通过使用 `@Primary` 注解修饰候选 bean 来按类型指定一个候选 bean。当多个候选 bean 都有资格自动装配单值依赖项时，`@Primary` 注解会优先选择该 bean：

```
package com.apress.spring6recipes.sequence;
import org.springframework.context.annotation.Primary;
import org.springframework.stereotype.Component;
@Component
@Primary
public class DatePrefixGenerator implements PrefixGenerator {
```

注意，前面的类实现了 `PrefixGenerator` 接口，并使用 `@Primary` 注解进行了修饰。如果你尝试自动装配一个 `PrefixGenerator` 类型的 bean，即使 Spring 有多个具有相同 `PrefixGenerator` 类型的 bean 实例，Spring 也会自动装配 `DatePrefixGenerator`，因为它被标记了 `@Primary` 注解。

##### 使用 `@Qualifier` 注解解决自动装配歧义

Spring 还允许你通过在 `@Qualifier` 注解中提供 bean 的名称来按类型指定候选 bean：

```
package com.apress.spring6recipes.sequence;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
public class Sequence {
@Autowired
@Qualifier("datePrefixGenerator")
private PrefixGenerator prefixGenerator;
}
```

完成此操作后，Spring 会尝试在 IoC 容器中查找具有该名称的 bean，并将其装配到属性中。

`@Qualifier` 注解也可以应用于方法参数以实现自动装配：

```
package com.apress.spring6recipes.sequence;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import java.util.concurrent.atomic.AtomicInteger;
public class Sequence {
private PrefixGenerator prefixGenerator;
@Autowired
public void setPrefixGenerator(
@Qualifier("datePrefixGenerator") PrefixGenerator prefixGenerator) {
this.prefixGenerator = prefixGenerator;
}
}
```

如果你想按名称自动装配 bean 属性，可以使用 JSR-250 的 `@Resource` 注解（在下一个配方中描述）来注解 setter 方法、构造器或字段。

## 1-4. 使用 @Resource 和 @Inject 注解自动装配 POJO

### 问题

你想使用 Java 标准的 `@Resource` 和 `@Inject` 注解通过自动装配来引用 POJO，而不是使用 Spring 特有的 @Autowired 注解。

### 解决方案

JSR-250（即 Java 平台的通用注解）定义了 `@Resource` 注解，用于按名称自动装配 POJO 引用。JSR-330（即注入的标准注解）定义了 `@Inject` 注解，用于按类型自动装配 POJO 引用。



### 工作原理

上一节中描述的 `@Autowired` 注解属于 Spring 框架，具体位于 `org.springframework.beans.factory.annotation` 包中。这意味着它只能在 Spring 框架的上下文中使用。

在 Spring 为 `@Autowired` 注解提供支持后不久，Java 语言本身也标准化了多种注解，以实现与 `@Autowired` 注解相同的功能。这些注解包括属于 `jakarta.annotation` 包的 `@Resource` 注解，以及属于 `jakarta.inject` 包的 `@Inject` 注解。

#### 使用 @Resource 注解自动装配 POJO

默认情况下，`@Resource` 注解的工作方式类似于 Spring 的 `@Autowired` 注解，会尝试按类型进行自动装配。例如，以下 POJO 属性使用了 `@Resource` 注解进行修饰，因此 Spring 会尝试定位一个与 `PrefixGenerator` 类型匹配的 POJO：

```
package com.apress.spring6recipes.sequence;
import jakarta.annotation.Resource;
public class Sequence {
@Resource
private PrefixGenerator prefixGenerator;
...
}
```

然而，与 `@Autowired` 注解需要配合 `@Qualifier` 注解才能按名称自动装配 POJO 不同，如果存在多个相同类型的 POJO，`@Resource` 注解的歧义性会被消除。本质上，`@Resource` 注解提供了与组合使用 `@Autowired` 和 `@Qualifier` 注解相同的功能。

#### 使用 @Inject 注解自动装配 POJO

与 `@Resource` 和 `@Autowired` 注解类似，`@Inject` 注解也尝试按类型进行自动装配。例如，以下 POJO 属性使用了 `@Inject` 注解进行修饰，因此 Spring 会尝试定位一个与 `PrefixGenerator` 类型匹配的 POJO：

```
package com.apress.spring6recipes.sequence;
import jakarta.inject.Inject;
public class Sequence {
@Inject
private PrefixGenerator prefixGenerator;
...
}
```

但与 `@Resource` 和 `@Autowired` 注解一样，如果存在多个相同类型的 POJO，则需要采用不同的方法来按名称匹配 POJO 或避免歧义。使用 `@Inject` 注解进行按名称自动装配的第一步是创建一个自定义注解，以标识 POJO 注入类和 POJO 注入点：

```
package com.apress.spring6recipes.sequence;
import java.lang.annotation.Documented;
import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;
import jakarta.inject.Qualifier;
@Qualifier
@Target({ElementType.TYPE, ElementType.FIELD, ElementType.PARAMETER})
@Documented
@Retention(RetentionPolicy.RUNTIME)
public @interface DatePrefixAnnotation {
}
```

请注意，这个自定义注解使用了 `@Qualifier` 注解。此注解与 Spring 的 `@Qualifier` 注解不同，因为后者与 `@Inject` 注解属于同一个 Java 包（即 `jakarta.inject`）。

自定义注解完成后，需要修饰生成 bean 实例的 POJO 注入类，在本例中为 `DatePrefixGenerator` 类：

```
package com.apress.spring6recipes.sequence;
...
@DatePrefixAnnotation
public class DatePrefixGenerator implements PrefixGenerator {
...
}
```

最后，使用相同的自定义注解修饰 POJO 属性或注入点，以限定 POJO 并消除任何歧义：

```
package com.apress.spring6recipes.sequence;
import jakarta.inject.Inject;
public class Sequence {
@Inject @DataPrefixAnnotation
private PrefixGenerator prefixGenerator;
...
}
```

@AUTOWIRED、@RESOURCE 还是 @INJECT？

正如你在配方 1-3 和 1-4 中所见，`@Autowired`、`@Resource` 和 `@Inject` 这三个注解可以实现相同的结果。`@Autowired` 注解是基于 Spring 的解决方案，而 `@Resource` 和 `@Inject` 注解是基于 Java 标准（即 JSR）的解决方案。如果你要进行基于名称的自动装配，`@Resource` 注解提供了最简单的语法。对于按类类型进行自动装配，这三个注解使用起来都很直接，因为它们都只需要一个注解。

## 1-5\. 使用 @Scope 注解设置 POJO 的作用域

### 问题

当你使用 `@Component` 等注解声明一个 POJO 实例时，实际上是在定义一个用于创建 bean 的模板，而不是一个实际的 bean 实例。当通过 `getBean()` 方法或其他 bean 的引用请求一个 bean 时，Spring 会根据 bean 的作用域决定返回哪个 bean 实例。有时，你必须为 bean 设置一个除默认作用域之外的合适作用域。

### 解决方案

bean 的作用域通过 `@Scope` 注解设置。默认情况下，Spring 为 IoC 容器中声明的每个 bean 只创建一个实例，并且该实例在整个 IoC 容器范围内共享。所有后续的 `getBean()` 调用和 bean 引用都会返回这个唯一的 bean 实例。这种作用域称为 `singleton`，是所有 bean 的默认作用域。表 1-1 列出了 Spring 中所有有效的 bean 作用域。

表 1-1

Spring 中有效的 Bean 作用域

| 作用域 | 描述 |
| --- | --- |
| `singleton` | 每个 Spring IoC 容器创建一个单一的 bean 实例 |
| `prototype` | 每次请求时创建一个新的 bean 实例 |
| `request` | 每个 HTTP 请求创建一个单一的 bean 实例，仅在 Web 应用程序上下文中有效 |
| `session` | 每个 HTTP 会话创建一个单一的 bean 实例，仅在 Web 应用程序上下文中有效 |
| `application` | 类似于 `singleton`，但会将 bean 注册到 `ServletContext` 中，以便在多个 servlet 之间共享 |
| `websocket` | 将 bean 的引用存储在 websocket 相关的会话属性中 |



### 工作原理

为了演示 Bean 作用域的概念，我们以购物应用中的购物车为例。首先，按如下方式创建 `ShoppingCart` 类：

```
package com.apress.spring6recipes.shop;
import org.springframework.stereotype.Component;
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;
@Component
public class ShoppingCart {
private final List items = new ArrayList();
public void addItem(Product item) {
this.items.add(item);
}
public List getItems() {
return Collections.unmodifiableList(this.items);
}
}
```

然后，在 Java 配置文件中声明产品 Bean，以便稍后将其添加到购物车中：

```
package com.apress.spring6recipes.shop.config;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import com.apress.spring6recipes.shop.Disc;
import com.apress.spring6recipes.shop.Battery;
import com.apress.spring6recipes.shop.Product;
@Configuration
@ComponentScan("com.apress.spring6recipes.shop")
public class ShopConfiguration {
@Bean
public Product aaa() {
return new Battery("AAA", 2.5, true);
}
@Bean
public Product cdrw() {
return new Disc("CD-RW", 1.5, 700);
}
@Bean
public Product dvdrw() {
return new Disc("DVD-RW", 3.0, 4900);
}
}
```

完成上述步骤后，你可以定义一个 Main 类，通过向购物车添加一些商品来测试它。假设同时有两位顾客在你的商店中浏览。第一位顾客通过 `getBean()` 方法获取一个购物车，并向其中添加了两件商品。接着，第二位顾客也通过 `getBean()` 方法获取一个购物车，并向其中添加了另一件商品：

```
package com.apress.spring6recipes.shop;
import com.apress.spring6recipes.shop.config.ShopConfiguration;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
public class Main {
public static void main(String[] args) {
var cfg = ShopConfiguration.class;
try (var context = new AnnotationConfigApplicationContext(cfg)) {
var aaa = context.getBean("aaa", Product.class);
var cdrw = context.getBean("cdrw", Product.class);
var dvdrw = context.getBean("dvdrw", Product.class);
var cart1 = context.getBean(ShoppingCart.class);
cart1.addItem(aaa);
cart1.addItem(cdrw);
System.out.println("购物车 1 包含 " + cart1.getItems());
var cart2 = context.getBean(ShoppingCart.class);
cart2.addItem(dvdrw);
System.out.println("购物车 2 包含 " + cart2.getItems());
}
}
}
```

根据上述 Bean 声明，你可以看到两位顾客获取到的是同一个购物车实例：

```
购物车 1 包含 [AAA 2.5, CD-RW 1.5]
购物车 2 包含 [AAA 2.5, CD-RW 1.5, DVD-RW 3.0]
```

这是因为 Spring 的默认 Bean 作用域是 `singleton`。这意味着 Spring 为每个 IoC 容器只创建一个购物车实例。

在你的商店应用中，你期望每次调用 `getBean()` 方法时，每位顾客都能获取到不同的购物车实例。为了确保这种行为，需要将 `shoppingCart` Bean 的作用域设置为 `prototype`。这样，Spring 就会为每次 `getBean()` 方法调用创建一个新的 Bean 实例：

```
package com.apress.spring6recipes.shop;
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;
import org.springframework.context.annotation.Scope;
import org.springframework.stereotype.Component;
@Component
@Scope("prototype")
public class ShoppingCart {
}
```

现在，如果你再次运行 Main 类，可以看到两位顾客获取到了不同的购物车实例：

```
购物车 1 包含 [AAA 2.5, CD-RW 1.5]
购物车 2 包含 [DVD-RW 3.0]
```

## 1-6\. 使用外部资源中的数据（文本文件、XML 文件、属性文件或图像文件）

### 问题

有时，应用程序需要从不同位置（例如文件系统、类路径或 URL）读取外部资源（例如文本文件、XML 文件、属性文件或图像文件）。通常，你必须使用不同的 API 来从不同位置加载资源。

### 解决方案

Spring 提供了 `@PropertySource` 注解，作为一种加载属性文件内容（即键值对）以设置 Bean 属性的工具。

Spring 还拥有一个资源加载器机制，它提供了一个统一的 `Resource` 接口，可以通过资源路径检索任何类型的外部资源。你可以为此路径指定不同的前缀，以使用 `@Value` 注解从不同位置加载资源。要从文件系统加载资源，请使用 `file` 前缀。要从类路径加载资源，请使用 `classpath` 前缀。你也可以在资源路径中指定一个 URL。

### 工作原理

要读取属性文件的内容（即键值对）以设置 Bean 属性，你可以将 Spring 的 `@PropertySource` 注解与 `PropertySourcesPlaceholderConfigurer` 结合使用。如果你想读取任何文件的内容，可以使用 Spring 的 Resource 机制，并配合 `@Value` 注解。



#### 使用属性文件数据设置 POJO 实例化值

假设你有一个属性文件，其中包含一系列你想用来设置 Bean 属性的值。通常，这可能是数据库的配置属性，或其他由键值对组成的应用程序值。例如，以下键值对存储在一个名为 `discounts.properties` 的文件中：

```
specialcustomer.discount=0.1
summer.discount=0.15
endofyear.discount=0.2
```

![](img/314861_5_En_1_Figf_HTML.gif)一个带阴影圆圈的字母 i 图标，表示信息符号。  关于为国际化（i18n）目的读取属性文件，请参阅下一个技巧。

为了使 `discounts.properties` 文件的内容可用于设置其他 Bean，你可以使用 `@PropertySource` 注解将键值对转换为 Java Config 类中的一个 Bean：

```
package com.apress.spring6recipes.shop.config;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.PropertySource;
import org.springframework.context.support.PropertySourcesPlaceholderConfigurer;
import com.apress.spring6recipes.shop.Disc;
import com.apress.spring6recipes.shop.Battery;
import com.apress.spring6recipes.shop.Product;
@Configuration
@PropertySource("classpath:discounts.properties")
@ComponentScan("com.apress.spring6recipes.shop")
public class ShopConfiguration {
@Value("${endofyear.discount:0}")
private double specialEndofyearDiscountField;
@Bean
public static PropertySourcesPlaceholderConfigurer pspc() {
return new PropertySourcesPlaceholderConfigurer();
}
@Bean
public Product dvdrw() {
return new Disc("DVD-RW", 1.5, 4700, specialEndofyearDiscountField);
}
}
```

你定义了一个值为 `classpath:discounts.properties` 的 `@PropertySource` 注解来修饰 Java Config 类。`classpath:` 前缀告诉 Spring 在 Java 类路径中查找 `discounts.properties` 文件。

一旦你定义了 `@PropertySource` 注解来加载属性文件，你还需要使用 `@Bean` 注解定义一个 `PropertySourcesPlaceholderConfigurer` Bean。Spring 会自动连接 `@PropertySource discounts.properties` 文件，使其属性可作为 Bean 属性访问。

接下来，你需要定义 Java 变量来从 `discounts.properties` 文件中获取值。要使用这些值来定义 Java 变量值，你需要使用带有占位符表达式的 `@Value` 注解。

其语法是 `@Value("${key:default_value}")`。Spring 会在所有已加载的应用程序属性中搜索该键值。如果在属性文件中找到匹配的键，则相应的值会被赋给 Bean 属性。如果在已加载的应用程序属性中未找到匹配的键，则 `default_value`（值表达式中冒号后面的部分）会被赋给 Bean 属性。

一旦 Java 变量被设置为某个折扣值，你就可以用它来设置 Bean 实例的折扣属性。

如果你希望将属性文件数据用于设置 Bean 属性之外的其他目的，则应使用 Spring 的资源（Resource）机制，该机制将在下一节中介绍。



#### 在 POJO 中使用任何外部资源文件的数据

假设你想在应用程序启动时显示一个横幅。该横幅由以下字符组成，并存储在名为 `banner.txt` 的文本文件中。该文件可以放在应用程序的类路径中：

```
*************************
*  Welcome to My Shop!  *
*************************
```

接下来，让我们编写一个 `BannerLoader` POJO 类来加载横幅并将其输出到控制台：

```
package com.apress.spring6recipes.shop;
import jakarta.annotation.PostConstruct;
import org.springframework.core.io.Resource;
import java.io.IOException;
import java.nio.charset.StandardCharsets;
import java.nio.file.Files;
import java.nio.file.Path;
public class BannerLoader {
private final Resource banner;
public BannerLoader(Resource banner) {
this.banner = banner;
}
@PostConstruct
public void showBanner() throws IOException {
var path = Path.of(banner.getURI());
try (var lines = Files.lines(path, StandardCharsets.UTF_8)) {
lines.forEachOrdered(System.out::println);
}
}
}
```

请注意，POJO 的 `banner` 字段是 Spring 的 `Resource` 类型。当 bean 实例被创建时，该字段的值将通过构造函数注入来填充——稍后将对此进行说明。`showBanner` 方法调用 `getURI()` 方法从 `Resource` 中检索 [java.​net](http://java.net)`.URI`。一旦你有了这个 URI，就可以使用 Java NIO 读取内容并将其打印到控制台。

还要注意，`showBanner()` 方法使用了 `@PostConstruct` 注解。因为你希望在启动时显示横幅，所以使用此注解告诉 Spring 在创建后自动调用该方法。这保证了 `showBanner()` 方法是应用程序最早运行的方法之一，从而确保横幅在启动时出现。

接下来，POJO `BannerLoader` 需要被初始化为一个实例。此外，`BannerLoader` 的 `banner` 字段也需要被注入。因此，让我们创建一个 Java Config 类来完成这些任务：

```
package com.apress.spring6recipes.shop.config;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.PropertySource;
import org.springframework.context.support.PropertySourcesPlaceholderConfigurer;
import org.springframework.core.io.Resource;
import com.apress.spring6recipes.shop.BannerLoader;
import com.apress.spring6recipes.shop.Battery;
import com.apress.spring6recipes.shop.Disc;
import com.apress.spring6recipes.shop.Product;
@Configuration
@PropertySource("classpath:discounts.properties")
@ComponentScan("com.apress.spring6recipes.shop")
public class ShopConfiguration {
@Value("classpath:banner.txt")
private Resource banner;
@Bean
public BannerLoader bannerLoader() {
return new BannerLoader(banner);
}
```

看看 `banner` 属性是如何使用 `@Value("classpath:banner.txt")` 注解进行修饰的。这告诉 Spring 在类路径中搜索 `banner.txt` 文件并将其注入。Spring 使用预先注册的属性编辑器 `ResourceEditor` 将文件定义转换为 `Resource` 对象，然后再将其注入到 bean 中。

一旦 `banner` 属性被注入，它就会通过构造函数注入被赋值给 `BannerLoader` bean 实例。

由于横幅文件位于 Java 类路径中，资源路径以 `classpath:` 前缀开头。前面的资源路径指定了文件系统相对路径中的资源。你也可以指定绝对路径：

```
file:c:/shop/banner.txt
```

当资源位于 Java 的类路径中时，你必须使用 classpath 前缀。如果没有提供路径信息，它将从类路径的根目录加载：

```
classpath:banner.txt
```

如果资源位于特定的包中，你可以指定从类路径根目录开始的绝对路径：

```
classpath:com/apress/spring6recipes/shop/banner.txt
```

除了支持从文件系统路径或类路径加载外，还可以通过指定 URL 来加载资源：

```
http://springrecipes.apress.com/shop/banner.txt
```

由于 bean 类在 `showBanner()` 方法上使用了 `@PostConstruct` 注解，因此在 IoC 容器设置完成后，横幅就会被发送到输出。因此，无需修改应用程序的上下文或显式调用 bean 来输出横幅。然而，有时可能需要访问外部资源以与应用程序的上下文进行交互。现在假设你想在应用程序结束时显示一个图例。该图例由之前在 `discounts.properties` 文件中描述的折扣组成。要访问属性文件的内容，你也可以利用 Spring 的 Resource 机制。

接下来，让我们使用 Spring 的 Resource 机制，但这次直接在应用程序的 Main 类中使用，以便在应用程序结束时输出图例：

```
package com.apress.spring6recipes.shop;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import org.springframework.core.io.ClassPathResource;
import org.springframework.core.io.support.PropertiesLoaderUtils;
import com.apress.spring6recipes.shop.config.ShopConfiguration;
public class Main {
public static void main(String[] args) throws Exception {
var resource = new ClassPathResource("discounts.properties");
var props = PropertiesLoaderUtils.loadProperties(resource);
System.out.println("And don't forget our discounts!");
System.out.println(props);
}
}
```

Spring 的 `ClassPathResource` 类用于访问 `discounts.properties` 文件，该文件将文件内容转换为 Resource 对象。接下来，使用 Spring 的 `PropertiesLoaderUtils` 类将 `Resource` 对象处理成 `Properties` 对象。最后，`Properties` 对象的内容作为应用程序的最终输出发送到控制台。

由于图例文件（即 `discounts.properties`）位于 Java 类路径中，因此使用 Spring 的 `ClassPathResource` 类来访问该资源。如果外部资源位于文件系统路径中，则将使用 Spring 的 `FileSystemResource` 加载该资源：

```
var resource = new FileSystemResource("c:/shop/banner.txt")
```

如果外部资源位于 URL 上，则将使用 Spring 的 `UrlResource` 加载该资源：

```
var resource = new UrlResource("https://www.apress.com/")
```

## 1-7. 在属性文件中为不同区域设置解析国际化文本消息

### 问题

你希望应用程序通过注解支持国际化（I18N）。

### 解决方案

`MessageSource` 是一个接口，它定义了多种用于在资源包中解析消息的方法。`ResourceBundleMessageSource` 是最常见的 `MessageSource` 实现，它从不同区域设置的资源包中解析消息。在声明了 `ResourceBundleMessageSource` POJO 之后，你可以在 Java Config 文件中使用 `@Bean` 注解，使 I18N 数据在应用程序中可用。



### 工作原理

举例来说，为美国英语创建以下资源包文件 `messages_en_US.properties`。资源包从类路径的根目录加载，因此请确保它们在 Java 类路径中可用。将以下键值对放入文件中：

```
alert.checkout=A shopping cart has been checked out.
alert.inventory.checkout=A shopping cart with {0} has been checked out at {1}.
```

为了从资源包中解析消息，我们创建一个包含 `ReloadableResourceBundleMessageSource` bean 的 Java 配置文件：

```
package com.apress.spring6recipes.shop.config;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.support.ReloadableResourceBundleMessageSource;
@Configuration
public class ShopConfiguration {
@Bean
public ReloadableResourceBundleMessageSource messageSource() {
var messageSource = new ReloadableResourceBundleMessageSource();
messageSource.setBasenames("classpath:messages");
messageSource.setCacheSeconds(1);
return messageSource;
}
}
```

为了让应用上下文能够检测到该 bean 实例，其名称必须为 `messageSource`。在 bean 定义中，通过 `setBasenames` 方法声明一个 `String` 列表，用于定位 `ResourceBundleMessageSource` 的包。在此例中，我们仅指定了默认约定，即查找位于 Java 类路径中、以 messages 开头的文件。此外，`setCacheSeconds` 方法将值设置为 1，以避免读取过时的消息。请注意，刷新尝试在实际重新加载之前，会先检查属性文件的最后修改时间戳；因此，如果文件没有变化，`setCacheSeconds` 的间隔可以设置得相当短，因为刷新尝试实际上并不会重新加载。

对于此 `MessageSource` 定义，如果你查找美国区域（首选语言为英语）的文本消息，会首先考虑资源包 `messages_en_US.properties`。如果不存在这样的资源包或找不到该消息，则会考虑匹配该语言的 `messages_en.properties` 文件。如果仍然找不到资源包，则会选择适用于所有区域的默认 `messages.properties` 文件。有关资源包加载的更多信息，你可以参考 `java.util.ResourceBundle` 类的 Javadoc。

接下来，你可以配置应用上下文，使用 `getMessage()` 方法解析消息。第一个参数是消息对应的键，第三个参数是目标区域：

```
package com.apress.spring6recipes.shop;
import com.apress.spring6recipes.shop.config.ShopConfiguration;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import java.time.LocalDateTime;
import java.util.Locale;
public class Main {
private static final String MSG = "The I18N message for %s is: %s%n";
public static void main(String[] args) {
var cfg = ShopConfiguration.class;
try (var context = new AnnotationConfigApplicationContext(cfg)) {
var alert = context.getMessage("alert.checkout", null, Locale.US);
var alert_inventory = context.getMessage("alert.inventory.checkout",
new Object[] { "[DVD-RW 3.0]", LocalDateTime.now() }, Locale.US);
System.out.printf(MSG, "alert.checkout", alert);
System.out.printf(MSG, "alert.inventory.checkout", alert_inventory);
}
}
}
```

`getMessage()` 方法的第二个参数是一个消息参数数组。在第一个 `String` 语句中，该值为 `null`。在第二个 `String` 语句中，使用了一个对象数组来填充消息参数。

在 `Main` 类中，你可以解析文本消息，因为你可以直接访问应用上下文。但对于一个 bean 来说，要解析文本消息，你必须将一个 `MessageSource` 实现注入到需要解析文本消息的 bean 中。让我们为购物应用实现一个 `Cashier` 类，演示如何解析消息：

```
package com.apress.spring6recipes.shop;
...
@Component
public class Cashier {
@Autowired
private MessageSource messageSource;
public void checkout(ShoppingCart cart) throws IOException {
var alert = messageSource.getMessage("alert.inventory.checkout",
new Object[] { cart.getItems(), new Date() }, Locale.US);
System.out.println(alert);
}
}
```

请注意，POJO 字段 `messageSource` 是 Spring 的 `MessageSource` 类型。该字段使用了 `@Autowired` 注解进行标注，因此在创建 bean 实例时，会通过注入来填充它。然后，`checkout` 方法可以访问 `messageSource` 字段，这使得该 bean 能够访问 `getMessage` 方法，从而根据 I18N 标准获取文本消息。

## 1-8. 使用注解自定义 POJO 的初始化和销毁

### 问题

某些 POJO 在使用前必须执行特定类型的初始化任务。这些任务可能包括打开文件、打开网络/数据库连接、分配内存等。此外，这些相同的 POJO 还必须在其生命周期结束时执行相应的销毁任务。因此，有时需要在 Spring IoC 容器中自定义 bean 的初始化和销毁。

### 解决方案

Spring 可以通过在 Java 配置类的 `@Bean` 定义中设置 `initMethod` 和 `destroyMethod` 属性来识别初始化和销毁回调方法。或者，如果 POJO 的方法分别使用了 `@PostConstruct` 和 `@PreDestroy` 注解进行修饰，Spring 也能识别初始化和销毁回调方法。Spring 还可以使用 `@Lazy` 注解将 bean 的创建延迟到需要时——这个过程称为延迟初始化。Spring 还可以使用 `@DependsOn` 注解确保某些 bean 在其他 bean 之前初始化。

### 工作原理



#### 使用 @Bean 定义 POJO 初始化和销毁前运行的方法

我们以购物应用为例，考虑一个涉及结账功能的示例。让我们修改 `Cashier` 类，将购物车的商品和结账时间记录到一个文本文件中：

```
package com.apress.spring6recipes.shop;
import java.io.BufferedWriter;
import java.io.IOException;
import java.nio.charset.StandardCharsets;
import java.nio.file.Files;
import java.nio.file.Path;
import java.nio.file.StandardOpenOption;
import java.time.LocalDateTime;
public class Cashier {
private final String filename;
private final String path;
private BufferedWriter writer;
public Cashier(String filename, String path) {
this.filename = filename;
this.path = path;
}
public void openFile() throws IOException {
var checkoutPath = Path.of(path, filename + ".txt");
if (Files.notExists(checkoutPath.getParent())) {
Files.createDirectories(checkoutPath.getParent());
}
this.writer = Files.newBufferedWriter(checkoutPath, StandardCharsets.UTF_8,
StandardOpenOption.CREATE, StandardOpenOption.APPEND);
}
public void checkout(ShoppingCart cart) throws IOException {
writer.write(LocalDateTime.now() + "\t" + cart.getItems() + "\r\n");
writer.flush();
}
public void closeFile() throws IOException {
writer.close();
}
}
```

在 `Cashier` 类中，`openFile()` 方法首先验证目标目录和要写入数据的文件是否存在。然后，它在指定的系统路径中打开文本文件，并将其赋值给 writer 字段。之后，每次调用 `checkout()` 方法时，日期和购物车商品都会被追加到文本文件中。最后，`closeFile()` 方法关闭文件以释放其系统资源。

接下来，让我们探讨如何在 Java 配置类中设置这个 bean 定义，以便在 bean 创建之前执行 `openFile()` 方法，并在 bean 销毁之前执行 `closeFile()` 方法：

```
@Bean(initMethod = "openFile", destroyMethod = "closeFile")
public Cashier cashier() {
var path = System.getProperty("java.io.tmpdir") + "/cashier";
return new Cashier("checkout", path);
}
```

请注意，POJO 的初始化和销毁任务是通过 `@Bean` 注解的 `initMethod` 和 `destroyMethod` 属性定义的。通过在 bean 声明中设置这两个属性，当创建 `Cashier` 类时，它会首先触发 `openFile()` 方法——验证目标目录和要写入数据的文件是否存在，并打开文件以追加记录——而当 bean 被销毁时，它会触发 `closeFile()` 方法，确保文件引用被关闭以释放系统资源。

#### 使用 @PostConstruct 和 @PreDestroy 定义 POJO 初始化和销毁前运行的方法

如果你要在 Java 配置类之外定义 POJO 实例（例如，使用 `@Component` 注解），另一种选择是直接在 POJO 类中使用 `@PostConstruct` 和 `@PreDestroy` 注解：

```
package com.apress.spring6recipes.shop;
import jakarta.annotation.PostConstruct;
import jakarta.annotation.PreDestroy;
import java.nio.charset.StandardCharsets;
import java.nio.file.Files;
import java.nio.file.Paths;
import java.nio.file.StandardOpenOption;
public class Cashier {
@PostConstruct
public void openFile() throws IOException {
var checkoutPath = Paths.get(path, filename + ".txt");
if (Files.notExists(checkoutPath.getParent())) {
Files.createDirectories(checkoutPath.getParent());
}
this.writer = Files.newBufferedWriter(checkoutPath, StandardCharsets.UTF_8,
StandardOpenOption.CREATE, StandardOpenOption.APPEND);
}
@PreDestroy
public void closeFile() throws IOException {
writer.close();
}
}
```

`openFile()` 方法使用了 `@PostConstruct` 注解，这告诉 Spring 在 bean 构造完成后立即执行该方法。`closeFile()` 方法使用了 `@PreDestroy` 注解，这告诉 Spring 在 bean 销毁之前立即执行该方法。

#### 使用 @Lazy 定义 POJO 的延迟初始化

默认情况下，Spring 对所有 POJO 执行**急切初始化**。这意味着 POJO 在启动时就被初始化。然而，在某些情况下，将 POJO 的初始化过程延迟到需要 bean 时再进行可能会更方便。延迟初始化被称为“**懒加载**”。

懒加载有助于限制启动时的资源消耗峰值，并节省整体系统资源。懒加载对于执行重量级操作（例如，网络连接、文件操作）的 POJO 尤其相关。要标记一个 bean 为懒加载，你可以使用 `@Lazy` 注解装饰该 bean：

```
package com.apress.spring6recipes.shop;
import org.springframework.context.annotation.Lazy;
import org.springframework.context.annotation.Scope;
import org.springframework.stereotype.Component;
@Component
@Scope("prototype")
@Lazy
public class ShoppingCart {
```

在上述声明中，由于 POJO 使用了 `@Lazy` 注解，如果该 POJO 从未被应用程序需要或被其他 POJO 引用，那么它将永远不会被实例化。

![](img/314861_5_En_1_Figg_HTML.gif)一个带阴影圆圈内字母 i 的图标，代表信息符号。  `@Lazy` 注解也可以与 `@Autowired` 一起用于字段或构造函数，以实现延迟注入。

#### 使用 @DependsOn 定义 POJO 在其他 POJO 之前初始化

随着应用程序中 POJO 数量的增长，POJO 初始化的数量也随之增加。如果 POJO 相互引用并且分布在不同的 Java 配置类中，这可能会产生**竞态条件**。如果 bean “C” 需要 bean “B” 和 bean “F” 中的逻辑，但 bean “C” 首先被检测到，而 Spring 尚未初始化 bean “B” 和 bean “F”，那么你将得到一个错误，并且这个错误可能难以发现。为了确保某些 POJO 在其他 POJO 之前被初始化，并在初始化过程失败时获得更具描述性的错误信息，Spring 提供了 `@DependsOn` 注解。`@DependsOn` 注解确保给定的 bean 在另一个 bean 之前被初始化：

```
package com.apress.spring6recipes.sequence.config;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.DependsOn;
import com.apress.spring6recipes.sequence.Sequence;
@Configuration
public class SequenceConfiguration {
@Bean
@DependsOn("datePrefixGenerator")
public Sequence sequenceGenerator() {
return new Sequence("A", 100000);
}
}
```

在上面的代码片段中，声明 `@DependsOn("datePrefixGenerator")` 确保了 `datePrefixGenerator` bean 在 `sequenceGenerator` bean 之前被创建。

## 1-9\. 创建后处理器以验证和修改 POJO

### 问题

你希望在构造期间对所有 bean 实例或特定类型的实例应用任务，以根据特定标准验证或修改 bean 属性。

### 解决方案

**Bean 后处理器**允许在初始化回调方法（即分配给 `@Bean` 注解的 `initMethod` 属性的方法或使用 `@PostConstruct` 注解装饰的方法）之前和之后处理 bean。Bean 后处理器的主要特点是它处理 IoC 容器中的所有 bean 实例，而不仅仅是单个 bean 实例。通常，bean 后处理器用于检查 bean 属性的有效性、根据特定标准更改 bean 属性，或对所有 bean 实例应用某些任务。

### 工作原理

假设你想要审计每个 bean 的创建。你可能出于调试应用程序、验证每个 bean 的属性或其他场景而这样做。Bean 后处理器是实现此功能的理想选择，因为你无需修改任何预先存在的 POJO 代码。



#### 创建一个 POJO 来处理每个 Bean 实例

要编写一个 Bean 后处理器，类必须实现 `BeanPostProcessor`。当 Spring 检测到实现了该接口的 Bean 时，它会将 `postProcessBeforeInitialization()` 和 `postProcessAfterInitialization()` 方法应用于 Spring 管理的所有 Bean 实例。你可以在这些方法中实现任何逻辑，以检查、修改或验证 Bean 的状态：

```
package com.apress.spring6recipes.shop;
import org.springframework.beans.BeansException;
import org.springframework.beans.factory.config.BeanPostProcessor;
import org.springframework.stereotype.Component;
@Component
public class AuditCheckBeanPostProcessor implements BeanPostProcessor {
@Override
public Object postProcessBeforeInitialization(Object bean, String beanName)
throws BeansException {
var msg = "In AuditCheckBeanPostProcessor.postProcessBeforeInitialization, processing bean type: %s%n";
System.out.printf(msg, bean.getClass().getName());
return bean;
}
@Override
public Object postProcessAfterInitialization(Object bean, String beanName)
throws BeansException {
return bean;
}
}
```

请注意，即使你在方法中不执行任何操作，`postProcessBeforeInitialization()` 和 `postProcessAfterInitialization()` 方法也必须返回原始的 Bean 实例。

要在应用上下文中注册一个 Bean 后处理器，请使用 `@Component` 注解标注该类。应用上下文能够检测哪个 Bean 实现了 `BeanPostProcessor` 接口，并将其注册，以处理容器中的所有其他 Bean 实例。

#### 创建一个 POJO 来处理选定的 Bean 实例

在 Bean 构造期间，Spring IoC 容器会将所有 Bean 实例逐个传递给 Bean 后处理器。这意味着，如果你只想将 Bean 后处理器应用于特定类型的 Bean，则必须通过检查其实例类型来过滤 Bean。这允许你更有选择性地跨 Bean 应用逻辑。

假设你现在想应用一个 Bean 后处理器，但仅针对 `Product` Bean 实例。以下示例是另一个仅执行此操作的 Bean 后处理器：

```
package com.apress.spring6recipes.shop;
import org.springframework.beans.BeansException;
import org.springframework.beans.factory.config.BeanPostProcessor;
public class ProductCheckBeanPostProcessor implements BeanPostProcessor {
private static final String MSG =
"In ProductCheckBeanPostProcessor.%s, processing Product: %s%n";
public Object postProcessBeforeInitialization(Object bean, String beanName)
throws BeansException {
if (bean instanceof Product product) {
var productName = product.getName();
System.out.printf(MSG, "postProcessBeforeInitialization", productName);
}
return bean;
}
public Object postProcessAfterInitialization(Object bean, String beanName)
throws BeansException {
if (bean instanceof Product product) {
var productName = product.getName();
System.out.printf(MSG, "postProcessAfterInitialization", productName);
}
return bean;
}
}
```

`postProcessBeforeInitialization()` 和 `postProcessAfterInitialization()` 方法都必须返回正在处理的 Bean 的实例。然而，这也意味着你甚至可以在 Bean 后处理器中用全新的实例替换原始的 Bean 实例。

## 1-10. 使用工厂创建 POJO（静态方法、实例方法、Spring 的 FactoryBean）

### 问题

你想通过调用静态工厂方法或实例工厂方法在 Spring IoC 容器中创建 POJO 实例。此方法的目的是将对象创建过程分别封装在静态方法或另一个对象实例的方法中。请求对象的客户端只需调用此方法，而无需了解创建细节。你想使用 Spring 的工厂 Bean 在 Spring IoC 容器中创建 POJO 实例。工厂 Bean 是一个用作工厂的 Bean，用于在 IoC 容器内创建其他 Bean。从概念上讲，工厂 Bean 与工厂方法非常相似，但它是 Spring 特定的 Bean，可以在 Bean 构造期间被 Spring IoC 容器识别。

### 解决方案

要在 Java 配置类的 `@Bean` 定义中通过调用静态工厂方法来创建 POJO，你可以使用标准 Java 语法来调用静态工厂方法。要在 Java 配置类的 `@Bean` 定义中通过调用实例工厂方法来创建 POJO，你需要创建一个 POJO 来实例化工厂值，并创建另一个 POJO 作为访问工厂的门面。

为了方便起见，Spring 提供了一个名为 `AbstractFactoryBean` 的抽象模板类，用于扩展 Spring 的 `FactoryBean` 接口。

### 工作原理

#### 通过调用静态工厂方法创建 POJO

例如，你可以编写以下 `createProduct` 静态工厂方法，根据预定义的产品 ID 创建产品。根据产品 ID，此方法决定实例化哪个具体产品类。如果没有匹配此 ID 的产品，它会抛出 `IllegalArgumentException`：

```
package com.apress.spring6recipes.shop;
public class ProductCreator {
public static Product createProduct(String productId) {
return switch (productId) {
case "aaa" -> new Battery("AAA", 2.5, true);
case "cdrw" -> new Disc("CD-RW", 1.5, 700);
case "dvdrw" -> new Disc("DVD-RW", 3.0, 4700);
default -> {
var msg = "Unknown product '" + productId + "'";
throw new IllegalArgumentException(msg);
}
};
}
}
```

要在 Java 配置类的 `@Bean` 定义中使用静态工厂方法创建 POJO，你可以使用常规 Java 语法调用工厂方法：

```
package com.apress.spring6recipes.shop.config;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import com.apress.spring6recipes.shop.Product;
import com.apress.spring6recipes.shop.ProductCreator;
@Configuration
@ComponentScan("com.apress.spring6recipes.shop")
public class ShopConfiguration {
@Bean
public Product aaa() {
return ProductCreator.createProduct("aaa");
}
@Bean
public Product cdrw() {
return ProductCreator.createProduct("cdrw");
}
@Bean
public Product dvdrw() {
return ProductCreator.createProduct("dvdrw");
}
}
```



#### 通过调用实例工厂方法创建 POJO

例如，你可以编写以下 `ProductCreator` 类，通过使用可配置的 Map 来存储预定义的产品。`createProduct()` 实例工厂方法通过在 Map 中查找提供的 `productId` 来寻找产品。如果没有匹配此 ID 的产品，它将抛出一个 `IllegalArgumentException`：

```
package com.apress.spring6recipes.shop;
import java.util.Map;
public class ProductCreator {
private final Map products;
public ProductCreator(Map products) {
this.products = products;
}
public Product createProduct(String productId) {
Product product = products.get(productId);
if (product != null) {
return product;
}
var msg = "Unknown product '" + productId + "'";
throw new IllegalArgumentException(msg);
}
}
```

要从这个 `ProductCreator` 创建产品，首先声明一个 `@Bean` 来实例化工厂值。接着，声明第二个 Bean 作为访问工厂的门面。最后，你可以调用工厂并执行 `createProduct()` 方法来实例化其他 Bean：

```
package com.apress.spring6recipes.shop.config;
import com.apress.spring6recipes.shop.Battery;
import com.apress.spring6recipes.shop.Disc;
import com.apress.spring6recipes.shop.Product;
import com.apress.spring6recipes.shop.ProductCreator;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import java.util.Map;
@Configuration
@ComponentScan("com.apress.spring6recipes.shop")
public class ShopConfiguration {
@Bean
public ProductCreator productCreatorFactory() {
var products = Map.of(
"aaa", new Battery("AAA", 2.5, true),
"cdrw", new Disc("CD-RW", 1.5, 700),
"dvdrw", new Disc("DVD-RW", 3.0, 4700));
return new ProductCreator(products);
}
@Bean
public Product aaa(ProductCreator productCreator) {
return productCreator.createProduct("aaa");
}
@Bean
public Product cdrw(ProductCreator productCreator) {
return productCreator.createProduct("cdrw");
}
@Bean
public Product dvdrw(ProductCreator productCreator) {
return productCreator.createProduct("dvdrw");
}
}
```

#### 使用 Spring 的工厂 Bean 创建 POJO

虽然你很少需要编写自定义工厂 Bean，但通过一个示例来理解其内部机制可能会有所帮助。例如，你可以编写一个工厂 Bean，用于创建应用了折扣价格的产品。它接受一个 `product` 属性和一个 `discount` 属性，将折扣应用于产品，并将其作为新 Bean 返回：

```
package com.apress.spring6recipes.shop;
import org.springframework.beans.factory.config.AbstractFactoryBean;
public class DiscountFactoryBean extends AbstractFactoryBean {
private Product product;
private double discount;
public void setProduct(Product product) {
this.product = product;
}
public void setDiscount(double discount) {
this.discount = discount;
}
@Override
public Class getObjectType() {
return product.getClass();
}
@Override
protected Product createInstance() throws Exception {
product.setPrice(product.getPrice() * (1 - discount));
return product;
}
}
```

通过扩展 `AbstractFactoryBean` 类，工厂 Bean 只需重写 `createInstance()` 方法即可创建目标 Bean 实例。此外，你必须在 `getObjectType()` 方法中返回目标 Bean 的类型，以便自动装配功能正常工作。

接下来，你可以使用常规的 `@Bean` 注解声明产品实例，以应用 `DiscountFactoryBean`：

```
package com.apress.spring6recipes.shop.config;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import com.apress.spring6recipes.shop.Battery;
import com.apress.spring6recipes.shop.DiscountFactoryBean;
import com.apress.spring6recipes.shop.Disc;
@Configuration
@ComponentScan("com.apress.spring6recipes.shop")
public class ShopConfiguration {
@Bean
public Battery aaa() {
return new Battery("AAA", 2.5, true);
}
@Bean
public Disc cdrw() {
return new Disc("CD-RW", 1.5, 700);
}
@Bean
public Disc dvdrw() {
return new Disc("DVD-RW", 3.0, 4700);
}
@Bean
public DiscountFactoryBean discountFactoryBeanAAA(Battery aaa) {
var factory = new DiscountFactoryBean();
factory.setProduct(aaa);
factory.setDiscount(0.2);
return factory;
}
@Bean
public DiscountFactoryBean discountFactoryBeanCDRW(Disc cdrw) {
var factory = new DiscountFactoryBean();
factory.setProduct(cdrw);
factory.setDiscount(0.1);
return factory;
}
@Bean
public DiscountFactoryBean discountFactoryBeanDVDRW(Disc dvdrw) {
var factory = new DiscountFactoryBean();
factory.setProduct(dvdrw);
factory.setDiscount(0.1);
return factory;
}
}
```

## 1-11. 使用 Spring 环境和 Profile 加载不同的 POJO 集合

### 问题

你希望使用同一组 POJO 实例或 Bean，但针对不同的应用场景（例如“生产”、“开发”和“测试”）使用不同的实例化值。

### 解决方案

创建多个 Java 配置类，并将 POJO 实例或 Bean 分组到这些类中。根据分组的目的，使用 `@Profile` 注解为 Java 配置类分配一个 Profile 名称。获取应用程序上下文的 Environment，并设置 Profile 以加载特定的 POJO 组。

### 工作原理

POJO 的实例化值可能因不同的应用场景而异。例如，一个常见场景是应用程序从开发阶段进入测试阶段，再到生产阶段。在每个场景中，某些 Bean 的属性可能会略有不同，以适应环境变化（例如，数据库用户名/密码、文件路径等）。

你可以创建多个 Java 配置类，每个类包含不同的 POJO（例如 `ShopConfigurationGlobal`、`ShopConfigurationSpr` 和 `ShopConfigurationSumWin`）。在应用程序上下文中，仅根据场景加载给定的配置类文件。没有 `@Profile` 注解的 Bean 始终会被加载，因为它们不绑定到特定的 Profile。



#### 使用 @Profile 注解创建 Java 配置类

让我们为之前食谱中介绍的购物应用创建多个带有 `@Profile` 注解的 Java 配置类：

```
package com.apress.spring6recipes.shop.config;
import com.apress.spring6recipes.shop.Cashier;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
@Configuration
@ComponentScan("com.apress.spring6recipes.shop")
public class ShopConfigurationGlobal {
@Bean(initMethod = "openFile", destroyMethod = "closeFile")
public Cashier cashier() {
var path = System.getProperty("java.io.tmpdir") + "/cashier";
return new Cashier("checkout", path);
}
}
```

```
package com.apress.spring6recipes.shop.config;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Profile;
import com.apress.spring6recipes.shop.Battery;
import com.apress.spring6recipes.shop.Disc;
import com.apress.spring6recipes.shop.Product;
@Configuration
@Profile({ "summer", "winter" })
public class ShopConfigurationSumWin {
@Bean
public Product aaa() {
return new Battery("AAA", 2.0, true);
}
@Bean
public Product cdrw() {
return new Disc("CD-RW", 1.0, 700);
}
@Bean
public Product dvdrw() {
return new Disc("DVD-RW", 2.5, 4700);
}
}
```

```
package com.apress.spring6recipes.shop.config;
import com.apress.spring6recipes.shop.Battery;
import com.apress.spring6recipes.shop.Disc;
import com.apress.spring6recipes.shop.Product;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Profile;
@Configuration
@Profile("autumn")
public class ShopConfigurationAut {
@Bean
public Product aaa() {
return new Battery("AAA", 2.5, true);
}
@Bean
public Product cdrw() {
return new Disc("CD-RW", 1.5, 700);
}
@Bean
public Product dvdrw() {
return new Disc("DVD-RW", 3.0, 4700);
}
}
```

```
package com.apress.spring6recipes.shop.config;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Profile;
import com.apress.spring6recipes.shop.Battery;
import com.apress.spring6recipes.shop.Disc;
import com.apress.spring6recipes.shop.Product;
@Configuration
@Profile("spring")
public class ShopConfigurationSpr {
@Bean
public Product aaa() {
return new Battery("AAA", 2.5, true);
}
@Bean
public Product cdrw() {
return new Disc("CD-RW", 1.5, 700);
}
@Bean
public Product dvdrw() {
return new Disc("DVD-RW", 3.0, 4700);
}
}
```

`@Profile` 注解修饰了整个 Java 配置类，因此所有 `@Bean` 实例都属于同一个 profile。要指定 `@Profile` 名称，只需将名称放在双引号内即可。请注意，也可以使用花括号 `{}` 包围的逗号分隔值（CSV）语法（例如 `{"summer","winter"}`）来指定多个 `@Profile` 名称。`ShopConfigurationGlobal` 没有 `@Profile` 注解，因为它是我们的主配置类，它将检测其他 `@Configuration` 类。

#### 将 Profile 加载到环境中

要将某个 profile 中的 bean 加载到应用程序中，你需要激活该 profile。你可以一次加载多个 profile，也可以通过 Java 运行时标志，甚至作为 Web 归档（WAR）文件的初始化参数以编程方式加载 profile。

要以编程方式加载 profile（即通过应用程序上下文），你可以获取上下文环境，然后通过 `setActiveProfiles()` 方法加载 profile：

```
var context = new AnnotationConfigApplicationContext();
context.getEnvironment().setActiveProfiles("winter");
context.register(ShopConfigurationGlobal.class);
context.refresh();
```

也可以通过 Java 运行时标志来指示要加载哪个 Spring profile。通过这种方式，你可以传递以下运行时标志来加载属于全局和冬季 profile 的所有 bean：

```
-Dspring.profiles.active=winter
```

#### 设置默认 Profile

为了避免因没有将任何 profile 加载到应用程序中而可能出现的错误，你可以定义默认 profile。默认 profile 仅在 Spring 无法检测到任何活动 profile 时使用——这些活动 profile 是通过之前提到的任何方法定义的：编程方式、Java 运行时标志或 Web 应用程序初始化参数。

要设置默认 profile，你也可以使用设置活动 profile 的三种方法中的任何一种。以编程方式，你使用 `setDefaultProfiles()` 方法代替 `setActiveProfiles()`；通过 Java 运行时标志或 Web 应用程序初始化参数，你可以使用 `spring.profiles.default` 参数代替 `spring.profiles.active`。

## 1-12\. 使 POJO 感知 Spring 的 IoC 容器资源

### 问题

尽管一个设计良好的组件不应直接依赖 Spring 的 IoC 容器，但有时 bean 需要感知容器的资源。


### 解决方案

你的 Bean 可以通过实现特定的“感知”接口来感知 Spring IoC 容器的资源。最常见的接口如表 1-2 所示。Spring 会通过这些接口中定义的 setter 方法，将相应的资源注入到实现了这些接口的 Bean 中。

表 1-2

Spring 中常见的感知接口

| 感知接口 | 目标资源类型 |
| --- | --- |
| `BeanNameAware` | 其在 IoC 容器中配置的实例的 Bean 名称 |
| `BeanFactoryAware` | 当前的 Bean 工厂，通过它可以调用容器的服务 |
| `ApplicationContextAware` | 当前的应用上下文，通过它可以调用容器的服务 |
| `MessageSourceAware` | 消息源，通过它可以解析文本消息 |
| `ApplicationEventPublisherAware` | 应用事件发布器，通过它可以发布应用事件 |
| `ResourceLoaderAware` | 资源加载器，通过它可以加载外部资源 |
| `EnvironmentAware` | 与 `ApplicationContext` 关联的 `org.springframework.core.env.Environment` 实例 |
| `EmbeddedValueResolverAware` | 用于解析内嵌值（即 Spring 值表达式）的 `StringValueResolver` |
| `LoadTimeWeaverAware` | 在启用加载时织入（LTW）时使用的 `LoadTimeWeaver` 实例 |
| `ApplicationStartupAware` | 与 `ApplicationContext` 关联的 `org.springframework.core.metrics.ApplicationStartup` |

![](img/314861_5_En_1_Figh_HTML.gif)一个带阴影圆圈的字母 i 图标，表示信息符号。  `ApplicationContext` 接口实际上扩展了 `MessageSource`、`ApplicationEventPublisher` 和 `ResourcePatternResolver` 接口，因此你只需感知应用上下文即可访问所有这些服务。然而，最佳实践是选择范围最小且能满足你需求的感知接口。

感知接口中的 setter 方法由 Spring 在设置 Bean 属性之后、初始化回调方法之前调用，如下列表所示：

*   通过构造函数或工厂方法创建 Bean 实例。
*   为 Bean 属性设置值和 Bean 引用。
*   调用感知接口中定义的 setter 方法。
*   将 Bean 实例传递给每个 Bean 后处理器的 `postProcessBeforeInitialization()` 方法。
*   调用初始化回调方法。
*   将 Bean 实例传递给每个 Bean 后处理器的 `postProcessAfterInitialization()` 方法。
*   Bean 已准备好可供使用。
*   当容器关闭时，调用销毁回调方法。

请记住，一旦 Bean 实现了感知接口，它就会与 Spring 绑定，并且在 Spring IoC 容器之外将无法正常工作。因此，请仔细考虑是否有必要实现此类专有接口。

![](img/314861_5_En_1_Figi_HTML.gif)一个带阴影圆圈的字母 i 图标，表示信息符号。  在较新版本的 Spring 中，并非严格需要实现感知接口。你也可以使用 `@Autowired` 来获取，例如，访问 `ApplicationContext`。但是，如果你正在编写一个框架/库，那么实现这些接口可能会更好。

### 工作原理

例如，你可以通过实现 `BeanNameAware` 接口，让购物应用中 `Cashier` 类的 POJO 实例感知其对应的 Bean 名称。通过实现该接口，Spring 会自动将 Bean 名称注入到 POJO 实例中。除了实现接口外，你还需要添加必要的 setter 方法来处理 Bean 名称：

```
package com.apress.spring6recipes.shop;
import org.springframework.beans.factory.BeanNameAware;
public class Cashier implements BeanNameAware {
private final String path;
private String fileName;
@Override
public void setBeanName(String name) {
this.fileName = name;
}
}
```

当 Bean 名称被注入后，你可以使用该值来执行需要 Bean 名称的相关 POJO 任务。例如，你可以使用该值来设置文件名，以记录收银员的结账数据。通过这种方式，你可以省去 `fileName` 构造函数参数的配置：

```
@Bean(initMethod = "openFile", destroyMethod = "closeFile")
public Cashier cashier() {
var path = System.getProperty("java.io.tmpdir") + "cashier";
var cashier = new Cashier(path);
return c1;
}
```

## 1-13. 使用注解进行面向切面编程

### 问题

你想在 Spring 和 AspectJ 中使用面向切面编程（AOP）。

### 解决方案

你可以通过使用 `@Aspect` 注解装饰一个 Java 类来定义一个切面。类中的每个方法都可以通过另一个注解成为一条通知。你可以使用五种类型的通知注解：`@Before`、`@After`、`@AfterReturning`、`@AfterThrowing` 和 `@Around`。这些注解由 AspectJ 提供，可用于定义在 Spring 应用程序中使用的切面。

为了在 Spring IoC 容器中启用注解支持，你需要在某个配置类上添加 `@EnableAspectJAutoProxy`。为了应用 AOP，Spring 会创建代理，默认情况下它会创建基于接口的 JDK 动态代理。对于接口不可用或未在应用程序设计中使用的场景，可以依赖 CGLIB 来创建代理。要启用 CGLIB，你需要在 `@EnableAspectJAutoProxy` 注解上设置属性 `proxyTargetClass=true`。



### 工作原理

为了支持基于注解的面向切面编程，Spring 使用了与 AspectJ 相同的注解，并借助 AspectJ 提供的库进行切点解析和匹配，不过 AOP 运行时仍然是纯粹的 Spring AOP，不依赖于 AspectJ 编译器或织入器。

为了演示如何启用基于注解的面向切面编程，我们将使用以下计算器接口来定义一组示例 POJO：

```
package com.apress.spring6recipes.calculator;
public interface ArithmeticCalculator {
double add(double a, double b);
double sub(double a, double b);
double mul(double a, double b);
double div(double a, double b);
}
```

```
package com.apress.spring6recipes.calculator;
public interface UnitCalculator {
double kilogramToPound(double kilogram);
double kilometerToMile(double kilometer);
}
```

接下来，让我们为每个接口创建 POJO 类，并添加 `println` 语句，以便了解每个方法何时被执行：

```
package com.apress.spring6recipes.calculator;
import org.springframework.stereotype.Component;
@Component
class StandardArithmeticCalculator implements ArithmeticCalculator {
@Override
public double add(double a, double b) {
var result = a + b;
System.out.printf("%f + %f = %f%n", a, b, result);
return result;
}
@Override
public double sub(double a, double b) {
var result = a - b;
System.out.printf("%f - %f = %f%n", a, b, result);
return result;
}
@Override
public double mul(double a, double b) {
var result = a * b;
System.out.printf("%f * %f = %f%n", a, b, result);
return result;
}
@Override
public double div(double a, double b) {
if (b == 0) {
throw new IllegalArgumentException("Division by zero");
}
var result = a / b;
System.out.printf("%f / %f = %f%n", a, b, result);
return result;
}
}
```

```
package com.apress.spring6recipes.calculator;
import org.springframework.stereotype.Component;
@Component
class StandardUnitCalculator implements UnitCalculator {
@Override
public double kilogramToPound(double kg) {
var pound = kg * 2.2;
System.out.printf("%f kilogram = %f pound%n", kg, pound);
return pound;
}
@Override
public double kilometerToMile(double km) {
var mile = km * 0.62;
System.out.printf("%f kilometer = %f mile%n", km, mile);
return mile;
}
}
```

请注意，每个 POJO 实现都使用了 `@Component` 注解来创建 Bean 实例。

#### 声明切面、通知和切点

切面是一个 Java 类，它将横跨多个类型和对象的关注点（例如日志记录或事务管理）模块化。模块化此类关注点的 Java 类使用 `@Aspect` 注解进行修饰。在 AOP 术语中，切面还辅以通知，而通知本身又包含切点。通知是一个简单的 Java 方法，带有其中一个通知注解。AspectJ 支持五种类型的通知注解：`@Before`、`@After`、`@AfterReturning`、`@AfterThrowing` 和 `@Around`。另一方面，切点是一个表达式，用于查找要应用切面通知的类型和对象。

#### 使用 @Before 通知的切面

要创建一个前置通知，以便在特定程序执行点之前处理横切关注点，你可以使用 `@Before` 注解，并将切点表达式作为注解的值：

```
package com.apress.spring6recipes.calculator;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.stereotype.Component;
@Aspect
@Component
public class CalculatorLoggingAspect {
private final Logger log = LoggerFactory.getLogger(this.getClass());
@Before("execution(* ArithmeticCalculator.add(..))")
public void logBefore() {
log.info("The method add() begins");
}
}
```

这个切点表达式匹配 `ArithmeticCalculator` 接口的 `add()` 方法执行。表达式中的前一个通配符匹配任何修饰符（`public`、`protected` 和 `private`）和任何返回类型。参数列表中的两个点匹配任意数量的参数。

![](img/314861_5_En_1_Figj_HTML.gif)一个带阴影圆圈的字母 i 图标，表示信息符号。  要使上述切面生效（即输出其消息），你需要设置日志记录。以下 `logback.xml` 可用于 Logback。本示例本身使用 SLF4J 简单日志记录器，并提供了日志记录的默认配置。

*示例 Logback 配置*

```

%d [%15.15t] %-5p %30.30c - %m%n

```

![](img/314861_5_En_1_Figk_HTML.gif)一个带阴影圆圈的字母 i 图标，表示信息符号。  `@Aspect` 注解不足以在类路径中被自动检测。因此，你需要为 POJO 添加一个单独的 `@Component` 注解才能被检测到。

接下来，你创建一个 Spring 配置来扫描所有 POJO，包括 POJO 计算器实现和切面，并包含 `@EnableAspectJAutoProxy` 注解：

```
package com.apress.spring6recipes.calculator;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.EnableAspectJAutoProxy;
@Configuration
@EnableAspectJAutoProxy
@ComponentScan
public class CalculatorConfiguration { }
```

最后一步，你可以使用以下 Main 类来测试切面：

```
package com.apress.spring6recipes.calculator;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
public class Main {
public static void main(String[] args) {
var cfg = CalculatorConfiguration.class;
try (var context = new AnnotationConfigApplicationContext(cfg)) {
var arithmeticCalculator = context.getBean(ArithmeticCalculator.class);
arithmeticCalculator.add(1, 2);
arithmeticCalculator.sub(4, 3);
arithmeticCalculator.mul(2, 3);
arithmeticCalculator.div(4, 2);
var unitCalculator = context.getBean(UnitCalculator.class);
unitCalculator.kilogramToPound(10);
unitCalculator.kilometerToMile(5);
}
}
}
```

被切点匹配的执行点称为连接点。在这个术语中，切点是一个用于匹配一组连接点的表达式，而通知则是在特定连接点处执行的动作。

为了让你的通知能够访问当前连接点的详细信息，你可以在通知方法中声明一个 `JoinPoint` 类型的参数。然后，你就可以访问连接点的详细信息，例如方法名称和参数值。现在，你可以通过将类名和方法名改为通配符来扩展你的切点，使其匹配所有方法：

```
package com.apress.spring6recipes.calculator;
import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.stereotype.Component;
import java.util.Arrays;
@Aspect
@Component
public class CalculatorLoggingAspect {
private Logger log = LoggerFactory.getLogger(this.getClass());
@Before("execution(* *.*(..))")
public void logBefore(JoinPoint joinPoint) {
var name = joinPoint.getSignature().getName();
var args = Arrays.toString(joinPoint.getArgs());
log.info("The method {}() begins with {} ", name , args);
}
}
```



#### 使用 @After 通知的后置通知

后置通知由标注了 `@After` 注解的方法表示，并在连接点执行完毕后触发，无论其是正常返回还是异常抛出。以下后置通知会记录计算器方法的结束：

```
package com.apress.spring6recipes.calculator;
import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.annotation.After;
import org.aspectj.lang.annotation.Aspect;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.stereotype.Component;
import java.util.Arrays;
@Aspect
@Component
public class CalculatorLoggingAspect {
private Logger log = LoggerFactory.getLogger(this.getClass());
@After("execution(* *.*(..))")
public void logAfter(JoinPoint joinPoint) {
var name = joinPoint.getSignature().getName();
log.info("The method {}() ends", name);
}
}
```

#### 使用 @AfterReturning 通知的后置返回通知

无论连接点是正常返回还是抛出异常，后置通知都会执行。如果你只想在连接点返回时执行日志记录，则应将后置通知替换为后置返回通知：

```
package com.apress.spring6recipes.calculator;
import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.annotation.AfterReturning;
import org.aspectj.lang.annotation.Aspect;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.stereotype.Component;
@Aspect
@Component
public class CalculatorLoggingAspect {
@AfterReturning("execution(* *.*(..))")
public void logAfterReturning(JoinPoint joinPoint) {
var name = joinPoint.getSignature().getName();
log.info("The method {}() has ended.", name);
}
}
```

在后置返回通知中，你可以通过为 `@AfterReturning` 注解添加 `returning` 属性来获取连接点的返回值。该属性的值应为通知方法中用于传入返回值的参数名称。然后，你需要在通知方法签名中添加一个同名参数。在运行时，Spring AOP 会通过该参数传入返回值。另请注意，原始切入点表达式需要改为在 `pointcut` 属性中提供：

```
package com.apress.spring6recipes.calculator;
import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.annotation.AfterReturning;
import org.aspectj.lang.annotation.Aspect;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.stereotype.Component;
import java.util.Arrays;
@Aspect
@Component
public class CalculatorLoggingAspect {
private Logger log = LoggerFactory.getLogger(this.getClass());
@AfterReturning(
pointcut = "execution(* *.*(..))",
returning = "result")
public void logAfterReturning(JoinPoint joinPoint, Object result) {
var name = joinPoint.getSignature().getName();
log.info("The method {}() ends with {}", name, result);
}
}
```

#### 使用 @AfterThrowing 通知的后置异常通知

后置异常通知仅在连接点抛出异常时执行：

```
package com.apress.spring6recipes.calculator;
import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.annotation.AfterThrowing;
import org.aspectj.lang.annotation.Aspect;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.stereotype.Component;
@Aspect
@Component
public class CalculatorLoggingAspect {
private final Logger log = LoggerFactory.getLogger(this.getClass());
@AfterThrowing("execution(* *.*(..))")
public void logAfterThrowing(JoinPoint joinPoint) {
var name = joinPoint.getSignature().getName();
log.error("An exception has been thrown in {}()", name);
}
}
```

类似地，可以通过为 `@AfterThrowing` 注解添加 `throwing` 属性来访问连接点抛出的异常。`Throwable` 类型是 Java 语言中所有错误和异常的父类。因此，以下通知将捕获连接点抛出的任何错误或异常：

```
package com.apress.spring6recipes.calculator;
import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.annotation.AfterThrowing;
import org.aspectj.lang.annotation.Aspect;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.stereotype.Component;
@Aspect
@Compoonent
public class CalculatorLoggingAspect {
private final Logger log = LoggerFactory.getLogger(this.getClass());
@AfterThrowing(
pointcut = "execution(* *.*(..))",
throwing = "ex")
public void logAfterThrowing(JoinPoint joinPoint, Throwable ex) {
var name = joinPoint.getSignature().getName();
log.error("An exception {} has been thrown in {}()", ex, name);
}
}
```

然而，如果你只对某一种特定类型的异常感兴趣，可以将其声明为异常参数的类型。这样，你的通知将仅在抛出兼容类型（即该类型及其子类型）的异常时执行：

```
package com.apress.spring6recipes.calculator;
import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.annotation.AfterThrowing;
import org.aspectj.lang.annotation.Aspect;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.stereotype.Component;
import java.util.Arrays;
@Aspect
@Component
public class CalculatorLoggingAspect {
private final Logger log = LoggerFactory.getLogger(this.getClass());
@AfterThrowing(
pointcut = "execution(* *.*(..))",
throwing = "ex")
public void logAfterThrowing(JoinPoint joinPoint, IllegalArgumentException ex) {
var args = Arrays.toString(joinPoint.getArgs());
var name = joinPoint.getSignature().getName();
log.error("Illegal argument {} in {}()", args, name);
}
}
```

#### 使用 @Around 通知的环绕通知

最后一种通知类型是环绕通知。它是所有通知类型中最强大的。它能够完全控制连接点，因此你可以将前面所有通知的操作合并到一个通知中。你甚至可以控制何时以及是否继续执行原始连接点。

以下环绕通知是之前创建的前置通知、后置返回通知和后置异常通知的组合。请注意，对于环绕通知，连接点的参数类型必须是 `ProceedingJoinPoint`。它是 `JoinPoint` 的子接口，允许你控制何时继续执行原始连接点。要继续执行，你需要调用 `ProceedingJoinPoint` 上的 `proceed()` 方法：

```
package com.apress.spring6recipes.calculator;
import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.stereotype.Component;
import java.util.Arrays;
@Aspect
@Component
public class CalculatorLoggingAspect {
private final Logger log = LoggerFactory.getLogger(this.getClass());
@Around("execution(* *.*(..))")
public Object logAround(ProceedingJoinPoint joinPoint) throws Throwable {
var name = joinPoint.getSignature().getName();
var args = Arrays.toString(joinPoint.getArgs());
log.info("The method {}() begins with {}.", name, args);
try {
var result = joinPoint.proceed();
log.info("The method {}() ends with {}.", name, result);
return result;
}
catch (IllegalArgumentException ex) {
log.error("Illegal argument {} in {}()", args, name);
throw ex;
}
}
}
```

环绕通知类型非常强大且灵活，你甚至可以修改原始参数值并更改最终返回值。使用此类型通知时必须格外小心，因为很容易忘记调用 `proceed()` 来继续执行原始连接点。

![](img/314861_5_En_1_Figl_HTML.gif)一个带有灯泡轮廓的图标。 选择通知类型的最佳实践是使用能够满足需求的最不强大的那种。

## 1-14\. 访问连接点信息



### 问题

在 AOP 中，通知会被应用到不同的程序执行点上，这些点被称为连接点。为了使通知能够正确执行操作，它通常需要关于连接点的详细信息。

### 解决方案

通过在通知方法签名中声明一个 `org.aspectj.lang.JoinPoint` 类型的参数，通知可以访问当前连接点的信息。

### 工作原理

例如，你可以通过以下通知来访问连接点信息。这些信息包括连接点类型（在 Spring AOP 中仅指方法执行）、方法签名（声明类型和方法名）、参数值，以及目标对象和代理对象：

```
package com.apress.spring6recipes.calculator;
import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.stereotype.Component;
import java.util.Arrays;
@Aspect
@Component
public class CalculatorLoggingAspect {
private final Logger log = LoggerFactory.getLogger(this.getClass());
@Before("execution(* *.*(..))")
public void logJoinPoint(JoinPoint jp) {
log.info("Join point kind : {}", jp.getKind());
log.info("Signature declaring type : {}", jp.getSignature().getDeclaringTypeName());
log.info("Signature name : {}", jp.getSignature().getName());
log.info("Arguments : {}", Arrays.toString(jp.getArgs()));
log.info("Target class : {}", jp.getTarget().getClass().getName());
log.info("This class : {}", jp.getThis().getClass().getName());
}
}
```

被代理包装的原始 Bean 称为目标对象，而代理对象则称为 this 对象。它们可以通过连接点的 `getTarget()` 和 `getThis()` 方法访问。从以下输出中，你可以看到这两个对象的类并不相同：

```
Join point kind : method-execution
Signature declaring type : com.apress.spring6recipes.calculator.ArithmeticCalculator
Signature name : add
Arguments : [1.0, 2.0]
Target class : com.apress.spring6recipes.calculator.StandardArithmeticCalculator
This class : com.sun.proxy.$Proxy6
```

## 1-15. 使用 @Order 注解指定切面优先级

### 问题

当有多个切面应用于同一个连接点时，除非你显式指定，否则这些切面的优先级是未定义的。

### 解决方案

切面的优先级可以通过实现 `Ordered` 接口或使用 `@Order` 注解来指定。

### 工作原理

假设你编写了另一个切面来验证计算器的参数。该切面中只有一个前置通知：

```
package com.apress.spring6recipes.calculator;
import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.springframework.stereotype.Component;
@Aspect
@Component
public class CalculatorValidationAspect {
@Before("execution(* *.*(double, double))")
public void validateBefore(JoinPoint joinPoint) {
for (var arg : joinPoint.getArgs()) {
validate((Double) arg);
}
}
private void validate(double a) {
if (a < 0) {
throw new IllegalArgumentException("Positive numbers only");
}
}
}
```

如果你同时应用这个切面和之前的切面，则无法保证哪个切面会先被应用。为了确保一个切面在另一个切面之前应用，你需要指定优先级。要指定优先级，你必须让两个切面都实现 `Ordered` 接口或使用 `@Order` 注解。

如果你决定实现 `Ordered` 接口，`getOrder` 方法返回的值越小，优先级越高。因此，如果你希望验证切面先被应用，它应该返回一个比日志切面更低的值：

```
package com.apress.spring6recipes.calculator;
import org.springframework.core.Ordered;
@Aspect
@Component
public class CalculatorValidationAspect implements Ordered {
@Override
public int getOrder() {
return 0;
}
}
```

另一种指定优先级的方式是通过 `@Order` 注解。顺序编号应放在注解的值中：

```
package com.apress.spring6recipes.calculator;
import org.springframework.core.annotation.Order;
@Aspect
@Component
@Order(0)
public class CalculatorValidationAspect {
}
```

## 1-16. 重用切面切入点定义

### 问题

在编写切面时，你可以直接将切入点表达式嵌入到通知注解中。你希望在多个通知中使用相同的切入点表达式，而无需多次嵌入。

### 解决方案

你可以使用 `@Pointcut` 注解独立定义一个切入点，以便在多个通知中重用。

### 工作原理

在切面中，切入点可以声明为一个带有 `@Pointcut` 注解的简单方法。切入点的方法体通常为空，因为将切入点定义与应用程序逻辑混合是不合理的。切入点方法的访问修饰符也控制着该切入点的可见性。其他通知可以通过方法名引用此切入点：

```
package com.apress.spring6recipes.calculator;
import org.aspectj.lang.annotation.Pointcut;
@Aspect
@Component
public class CalculatorLoggingAspect {
@Pointcut("execution(* *.*(..))")
private void loggingOperation() {}
@Before("loggingOperation()")
public void logBefore(JoinPoint joinPoint) {
}
@After("loggingOperation()")
public void logAfter(JoinPoint joinPoint) {
}
@AfterReturning(
pointcut = "loggingOperation()",
returning = "result")
public void logAfterReturning(JoinPoint joinPoint, Object result) {
}
@AfterThrowing(
pointcut = "loggingOperation()",
throwing = "ex")
public void logAfterThrowing(JoinPoint joinPoint, IllegalArgumentException ex) {
}
@Around("loggingOperation()")
public Object logAround(ProceedingJoinPoint joinPoint) throws Throwable {
}
}
```

通常，如果你的切入点在多个切面之间共享，最好将它们集中在一个公共类中。在这种情况下，它们必须声明为 public：

```
package com.apress.spring6recipes.calculator;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Pointcut;
@Aspect
public class CalculatorPointcuts {
@Pointcut("execution(* *.*(..))")
public void loggingOperation() {}
}
```

当你引用此切入点时，还必须包含类名。如果该类与切面不在同一个包中，则还必须包含包名：

```
package com.apress.spring6recipes.calculator;
@Aspect
@Component
public class CalculatorLoggingAspect {
@Before("com.apress.spring6recipes.calculator.CalculatorPointcuts.loggingOperation()")
public void logBefore(JoinPoint joinPoint) {
}
@After("com.apress.spring6recipes.calculator.CalculatorPointcuts.loggingOperation()")
public void logAfter(JoinPoint joinPoint) {
}
@AfterReturning(
pointcut = "com.apress.spring6recipes.calculator.CalculatorPointcuts.loggingOperation()",
returning = "result")
public void logAfterReturning(JoinPoint joinPoint, Object result) {
}
@AfterThrowing(
pointcut = "com.apress.spring6recipes.calculator.CalculatorPointcuts.loggingOperation()",
throwing = "ex")
public void logAfterThrowing(JoinPoint joinPoint, IllegalArgumentException ex) {
}
@Around("com.apress.spring6recipes.calculator.CalculatorPointcuts.loggingOperation()")
public Object logAround(ProceedingJoinPoint joinPoint) throws Throwable {
}
}
```

## 1-17. 编写 AspectJ 切入点表达式

### 问题

横切关注点可能发生在不同的程序执行点上，这些点被称为连接点。由于连接点的多样性，你需要一种强大的表达式语言来帮助匹配它们。



### 解决方案

AspectJ 切点语言是一种强大的表达式语言，可以匹配各种类型的连接点。然而，Spring AOP 仅支持其 IoC 容器中声明的 Bean 的方法执行连接点。因此，本方案仅介绍 Spring AOP 支持的切点表达式。有关 AspectJ 切点语言的完整描述，请参考 AspectJ 网站上的 AspectJ 编程指南（[`www.eclipse.org/aspectj/`](http://www.eclipse.org/aspectj/)）。Spring AOP 使用 AspectJ 切点语言来定义切点，并在运行时通过 AspectJ 提供的库来解释切点表达式。在为 Spring AOP 编写 AspectJ 切点表达式时，必须牢记 Spring AOP 仅支持其 IoC 容器中 Bean 的方法执行连接点。如果使用了此范围之外的切点表达式，则会抛出 `IllegalArgumentException`。

### 工作原理

#### 方法签名模式

最典型的切点表达式用于根据方法签名匹配多个方法。例如，以下切点表达式匹配 `ArithmeticCalculator` 接口中声明的所有方法。开头的通配符匹配具有任何修饰符（`public`、`protected` 和 `private`）和任何返回类型的方法。参数列表中的两个点匹配任意数量的参数：

```
execution(* com.apress.spring6recipes.calculator.ArithmeticCalculator.*(..))
```

如果目标类或接口与切面位于同一包中，则可以省略包名：

```
execution(* ArithmeticCalculator.*(..))
```

以下切点表达式匹配 `ArithmeticCalculator` 接口中声明的所有 `public` 方法：

```
execution(public * ArithmeticCalculator.*(..))
```

你也可以限制方法的返回类型。例如，以下切点匹配返回 `double` 类型的方法：

```
execution(public double ArithmeticCalculator.*(..))
```

方法的参数列表也可以被限制。例如，以下切点匹配第一个参数为原始 `double` 类型的方法。两个点随后匹配任意数量的后续参数：

```
execution(public double ArithmeticCalculator.*(double, ..))
```

或者，你可以为要匹配的切点指定方法签名中的所有参数类型：

```
execution(public double ArithmeticCalculator.*(double, double))
```

尽管 AspectJ 切点语言在匹配各种连接点方面功能强大，但有时你可能无法为要匹配的方法找到任何共同特征（例如，修饰符、返回类型、方法名称模式或参数）。在这种情况下，你可以考虑为它们提供一个自定义注解。例如，你可以定义以下标记注解。此注解可以应用于方法级别和类型级别：

```
package com.apress.spring6recipes.calculator;
import java.lang.annotation.Documented;
import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;
@Target({ ElementType.METHOD, ElementType.TYPE })
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface LoggingRequired { }
```

接下来，你可以使用此注解来注解所有需要日志记录的方法，或者注解类本身以将行为应用于所有方法。请注意，注解必须添加到实现类而不是接口，因为它们不会被继承：

```
package com.apress.spring6recipes.calculator;
import org.springframework.stereotype.Component;
@Component
@LoggingRequired
class StandardArithmeticCalculator implements ArithmeticCalculator {
}
```

然后，你可以编写一个切点表达式，使用 `@Pointcut` 注解上的 `@annotation` 关键字来匹配带有 `@LoggingRequired` 注解的类或方法：

```
package com.apress.spring6recipes.calculator;
import org.aspectj.lang.annotation.Pointcut;
public class CalculatorPointcuts {
@Pointcut("@annotation(com.apress.spring6recipes.calculator.LoggingRequired)")
public void loggingOperation() {
}
}
```

#### 类型签名模式

另一种切点表达式匹配特定类型内的所有连接点。当应用于 Spring AOP 时，这些切点的范围将缩小为匹配类型内的所有方法执行。例如，以下切点匹配 `com.apress.spring6recipes.calculator` 包内的所有方法执行连接点：

```
within(com.apress.spring6recipes.calculator.*)
```

要匹配包及其子包内的连接点，你需要在通配符前再加一个点：

```
within(com.apress.spring6recipes.calculator..*)
```

以下切点表达式匹配特定类中的方法执行连接点：

```
within(com.apress.spring6recipes.calculator.StandardArithmeticCalculator)
```

同样，如果目标类与切面位于同一包中，则可以省略包名：

```
within(StandardArithmeticCalculator)
```

你可以通过添加加号来匹配所有实现 `ArithmeticCalculator` 接口的类中的方法执行连接点：

```
within(ArithmeticCalculator+)
```

如前所述，自定义注解 `@LoggingRequired` 可以应用于类或方法级别：

```
package com.apress.spring6recipes.calculator;
@LoggingRequired
public class StandardArithmeticCalculator implements ArithmeticCalculator {}
```

然后，你可以使用 `@Pointcut` 注解上的 `@within` 关键字来匹配已使用 `@LoggingRequired` 注解的类或方法中的连接点：

```
@Pointcut("@within(com.apress.spring6recipes.calculator.LoggingRequired)")
public void loggingOperation() {}
```

![](img/314861_5_En_1_Figm_HTML.gif)一个带阴影圆圈的字母 i 图标，代表信息符号。  你可能已经注意到了 `within`、`@within` 和 `@annotation`。对于几个切点表达式，存在一个带前导 `@` 和不带前导 `@` 的关键字。区别在于，不带 `@` 的是常规 AspectJ 表达式，适用于匹配的包名和类名。带 `@` 的则适用于注解。因此，如果你想匹配某个使用特定注解注解的类中的内容，必须使用 `@within`；如果你想匹配一个类，则必须使用 `within`。

#### 组合切点表达式

在 AspectJ 中，切点表达式可以使用运算符 `&&`（与）、`||`（或）和 `!`（非）进行组合。例如，以下切点匹配实现了 `ArithmeticCalculator` 或 `UnitCalculator` 接口的类中的连接点：

```
within(ArithmeticCalculator+) || within(UnitCalculator+)
```

这些运算符的操作数可以是任何切点表达式或对其他切点的引用：

```
package com.apress.spring6recipes.calculator;
import org.aspectj.lang.annotation.Pointcut;
public class CalculatorPointcuts {
@Pointcut("within(com.apress.spring6recipes.calculator.ArithmeticCalculator+)")
public void arithmeticOperation() {}
@Pointcut("within(com.apress.spring6recipes.calculator.UnitCalculator+)")
public void unitOperation() {}
@Pointcut("arithmeticOperation() || unitOperation()")
public void loggingOperation() {}
}
```



#### 声明切入点参数

访问连接点信息的一种方式是通过反射（即在通知方法中使用 `org.aspectj.lang.JoinPoint` 类型的参数）。此外，你还可以通过使用某些特殊的切入点表达式，以声明式的方式访问连接点信息。例如，`target()` 和 `args()` 表达式会捕获当前连接点的目标对象和参数值，并将其暴露为切入点参数。这些参数会通过同名参数传递给通知方法：

```
package com.apress.spring6recipes.calculator;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.springframework.stereotype.Component;
@Aspect
@Component
public class CalculatorLoggingAspect {
@Before("execution(* *.*(..)) && target(target) && args(a,b)")
public void logParameter(Object target, double a, double b) {
log.info("Target class : {}" , target.getClass().getName());
log.info("Arguments : {},{}", a , b);
}
}
```

当声明一个暴露参数的独立切入点时，你也必须将这些参数包含在切入点方法的参数列表中：

```
package com.apress.spring6recipes.calculator;
import org.aspectj.lang.annotation.Pointcut;
public class CalculatorPointcuts {
@Pointcut("execution(* *.*(..)) && target(target) && args(a,b)")
public void parameterPointcut(Object target, double a, double b) {}
}
```

任何引用此参数化切入点的通知，都可以通过同名方法参数访问切入点参数：

```
package com.apress.spring6recipes.calculator;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.springframework.stereotype.Component;
@Aspect
@Component
public class CalculatorLoggingAspect {
@Before("com.apress.spring6recipes.calculator.CalculatorPointcuts.parameterPointcut(target, a, b)")
public void logParameter(Object target, double a, double b) {
log.info("Target class : {}" , target.getClass().getName());
log.info("Arguments : {},{}", a , b);
}
}
```

## 1-18\. 为 POJO 引入 AOP

### 问题

有时，你可能有一组共享相同行为的类。在面向对象编程中，它们必须继承同一个基类或实现同一个接口。这个问题实际上是一个横切关注点，可以通过 AOP 进行模块化。此外，Java 的单继承机制只允许一个类最多继承一个基类。因此，你无法同时从多个实现类继承行为。

### 解决方案

引入（Introduction）是 AOP 中一种特殊类型的通知。它允许对象通过为某个接口提供实现类来动态地实现该接口。这看起来就像对象在运行时扩展了一个实现类。此外，你还可以同时为对象引入多个接口及其对应的多个实现类。这可以达到与多继承相同的效果。



### 工作原理

假设你有两个接口，`MaxCalculator` 和 `MinCalculator`，用于定义 `max()` 和 `min()` 操作：

```
package com.apress.spring6recipes.calculator;
public interface MaxCalculator {
double max(double a, double b);
}
```

```
package com.apress.spring6recipes.calculator;
public interface MinCalculator {
double min(double a, double b);
}
```

然后，你为每个接口提供了一个实现类，其中包含打印语句，以便在方法执行时获知信息：

```
package com.apress.spring6recipes.calculator;
public class SimpleMaxCalculator implements MaxCalculator {
@Override
public double max(double a, double b) {
var result = Math.max(a, b);
System.out.printf("max(%f,%f) = %f%n", a,b, result);
return result;
}
}
```

```
package com.apress.spring6recipes.calculator;
public class SimpleMinCalculator implements MinCalculator {
@Override
public double min(double a, double b) {
var result = Math.min(a, b);
System.out.printf("min(%f,%f) = %f%n", a, b, result);
return result;
}
}
```

现在，假设你希望 `StandardArithmeticCalculator` 也能执行 `max()` 和 `min()` 计算。由于 Java 语言只支持单继承，`StandardArithmeticCalculator` 类无法同时继承 `SimpleMaxCalculator` 和 `SimpleMinCalculator` 类。唯一可行的方法是继承其中一个类（例如 `SimpleMaxCalculator`）并实现另一个接口（例如 `MinCalculator`），要么复制实现代码，要么将处理委托给实际的实现类。无论哪种情况，你都必须重复声明方法。

通过引入（introduction），你可以让 `StandardArithmeticCalculator` 使用实现类 `SimpleMaxCalculator` 和 `SimpleMinCalculator` 动态地实现 `MaxCalculator` 和 `MinCalculator` 这两个接口。其效果等同于从 `SimpleMaxCalculator` 和 `SimpleMinCalculator` 进行多重继承。引入背后的理念是，你无需修改 `StandardArithmeticCalculator` 类即可引入新方法。这意味着，即使没有源代码，你也可以向现有类引入方法。

![](img/314861_5_En_1_Fign_HTML.gif)一个带有灯泡轮廓的图标。  你可能会好奇，引入是如何在 Spring AOP 中实现这一点的。答案是动态代理。你可能还记得，你可以为动态代理指定一组要实现的接口。引入的工作原理是向动态代理添加一个接口（例如 `MaxCalculator`）。当代理对象上调用此接口中声明的方法时，代理会将调用委托给后端的实现类（例如 `SimpleMaxCalculator`）。

引入与通知类似，必须在切面内声明。你可以为此创建一个新切面，或重用现有切面。在此切面中，你可以通过使用 `@DeclareParents` 注解标注任意字段来声明一个引入：

```
package com.apress.spring6recipes.calculator;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.DeclareParents;
import org.springframework.stereotype.Component;
@Aspect
@Component
public class CalculatorIntroduction {
@DeclareParents(
value = "com.apress.spring6recipes.calculator.StandardArithmeticCalculator",
defaultImpl = SimpleMaxCalculator.class)
public MaxCalculator maxCalculator;
@DeclareParents(
value = "com.apress.spring6recipes.calculator.StandardArithmeticCalculator",
defaultImpl = SimpleMinCalculator.class)
public MinCalculator minCalculator;
}
```

`@DeclareParents` 注解类型的 `value` 属性指明了哪些类是此引入的目标。要引入的接口由被注解字段的类型决定。最后，用于此新接口的实现类在 `defaultImpl` 属性中指定。

通过这两个引入，你可以动态地向 `StandardArithmeticCalculator` 类引入两个接口。实际上，你可以在 `@DeclareParents` 注解的 `value` 属性中指定一个 AspectJ 类型匹配表达式，从而向多个类引入同一个接口。

既然你已经将 `MaxCalculator` 和 `MinCalculator` 这两个接口引入到你的算术计算器中，你可以将其转换为相应的接口来执行 `max()` 和 `min()` 计算：

```
package com.apress.spring6recipes.calculator;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
public class Main {
public static void main(String[] args) {
var cfg = CalculatorConfiguration.class;
try (var context = new AnnotationConfigApplicationContext(cfg)) {
var maxCalculator = (MaxCalculator) arithmeticCalculator;
maxCalculator.max(1,2);
var minCalculator = (MinCalculator)arithmeticCalculator;
minCalculator.min( 1, 2);
}
}
```

![](img/314861_5_En_1_Figo_HTML.gif)一个带有字母 i 的图标，位于带阴影的圆圈内，表示信息符号。  你也可以执行 `context.getBean(MaxCalculator.class);` 来获取计算器的引用并进行计算。此示例旨在说明同一个 bean 现在实现了多个接口，并且 AOP 被用于调用实际的实现方法。

## 1-19. 使用 AOP 为你的 POJO 引入状态

### 问题

有时，你可能希望为一组现有对象添加新状态，以跟踪它们的使用情况，例如调用次数、最后修改日期等。如果所有对象都具有相同的基类，这或许不是一个解决方案。然而，如果这些对象不在同一个类层次结构中，向不同类添加此类状态会很困难。

### 解决方案

你可以向你的对象引入一个新接口，并提供一个持有状态字段的实现类。然后，你可以编写另一个通知，根据特定条件来更改状态。



### 工作原理

假设你想追踪每个计算器对象的调用次数。由于原始计算器类中没有用于存储计数器值的字段，你需要通过 Spring AOP 引入一个。首先，为计数器的操作创建一个接口：

```
package com.apress.spring6recipes.calculator;
public interface Counter {
void increase();
int getCount();
}
```

接下来，为该接口编写一个简单的实现类。这个类有一个用于存储计数器值的计数器字段：

```
package com.apress.spring6recipes.calculator;
import java.util.concurrent.atomic.AtomicInteger;
public class SimpleCounter implements Counter {
private final AtomicInteger counter = new AtomicInteger();
@Override
public void increase() {
counter.incrementAndGet();
}
@Override
public int getCount() {
return counter.get();
}
}
```

为了将 `Counter` 接口引入到所有计算器对象中，并以 `SimpleCounter` 作为实现，你可以编写一个带有类型匹配表达式的引入，该表达式匹配所有计算器实现：

```
package com.apress.spring6recipes.calculator;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.DeclareParents;
import org.springframework.stereotype.Component;
@Aspect
@Component
public class CalculatorIntroduction {
@DeclareParents(
value = "com.apress.spring6recipes.calculator.Standard*Calculator",
defaultImpl = SimpleCounter.class)
public Counter counter;
}
```

这个引入将 `SimpleCounter` 引入到每个计算器对象中。然而，这仍然不足以追踪调用次数。你必须在每次调用计算器方法时增加计数器值。为此，你可以编写一个后置通知。请注意，你必须获取 `this` 对象，而不是 `target` 对象，因为只有代理对象实现了 `Counter` 接口：

```
package com.apress.spring6recipes.calculator;
import org.aspectj.lang.annotation.After;
import org.aspectj.lang.annotation.Aspect;
import org.springframework.stereotype.Component;
@Aspect
@Component
public class CalculatorIntroduction {
@After("execution(* com.apress.spring6recipes.calculator.*Calculator.*(..)) && this(counter)")
public void increaseCount(Counter counter) {
counter.increase();
}
}
```

在 Main 类中，你可以通过将每个计算器对象转换为 `Counter` 类型来输出其计数器值：

```
package com.apress.spring6recipes.calculator;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
public class Main {
public static void main(String[] args) {
var cfg = CalculatorConfiguration.class;
try (var context = new AnnotationConfigApplicationContext(cfg)) {
var arithmeticCalculator = context.getBean(ArithmeticCalculator.class);
arithmeticCalculator.add(1, 2);
arithmeticCalculator.sub(4, 3);
arithmeticCalculator.mul(2, 3);
arithmeticCalculator.div(4, 2);
var unitCalculator = context.getBean(UnitCalculator.class);
unitCalculator.kilogramToPound(10);
unitCalculator.kilometerToMile(5);
var maxCalculator = (MaxCalculator) arithmeticCalculator;
maxCalculator.max(1,2);
var minCalculator = (MinCalculator)arithmeticCalculator;
minCalculator.min( 1, 2);
var arithmeticCounter = (Counter) arithmeticCalculator;
System.out.println(arithmeticCounter.getCount());
var unitCounter = (Counter) unitCalculator;
System.out.println(unitCounter.getCount());
}
}
}
```

## 1-20. 在 Spring 中加载时织入 AspectJ 切面

### 问题

Spring AOP 框架仅支持有限类型的 AspectJ 切入点，并允许切面应用于 IoC 容器中声明的 Bean。如果你想使用额外的切入点类型，或者将切面应用于 Spring IoC 容器外部创建的对象，则必须在 Spring 应用程序中使用 AspectJ 框架。

### 解决方案

织入是将切面应用于目标对象的过程。使用 Spring AOP 时，织入在运行时通过动态代理发生。相比之下，AspectJ 框架支持编译时织入和加载时织入。

AspectJ 编译时织入通过一个名为 ajc 的特殊 AspectJ 编译器完成。它可以将切面织入到你的 Java 源文件中，并输出织入后的二进制类文件。它还可以将切面织入到已编译的类文件或 JAR 文件中。这个过程称为编译后织入。你可以在 Spring IoC 容器中声明类之前，对它们执行编译时和编译后织入。Spring 完全不参与织入过程。有关编译时和编译后织入的更多信息，请参考 AspectJ 文档。

AspectJ 加载时织入（也称为 LTW）发生在目标类被类加载器加载到 JVM 时。为了织入一个类，需要一个特殊的类加载器来增强目标类的字节码。AspectJ 和 Spring 都提供了加载时织入器，为类加载器添加加载时织入能力。你只需要进行简单的配置即可启用这些加载时织入器。



### 工作原理

为了理解 Spring 应用中 AspectJ 的加载时织入过程，我们以一个复数计算器为例。首先，创建 `Complex` 记录来表示复数：

```
package com.apress.spring6recipes.calculator;
public record Complex(int real, int imaginary) {
@Override
public String toString() {
return "(" + real + " + " + imaginary + "i)";
}
}
```

接下来，为复数运算定义一个接口。为简单起见，只支持 `add()` 和 `sub()` 方法：

```
package com.apress.spring6recipes.calculator;
public interface ComplexCalculator {
Complex add(Complex a, Complex b);
Complex sub(Complex a, Complex b);
}
```

该接口的实现代码如下。每次操作都返回一个新的复数对象作为结果：

```
package com.apress.spring6recipes.calculator;
import org.springframework.stereotype.Component;
@Component
class StandardComplexCalculator implements ComplexCalculator {
@Override
public Complex add(Complex a, Complex b) {
var result = new Complex(a.real() + b.real(), a.imaginary() + b.imaginary());
System.out.printf("%s + %s = %s%n", a, b, result);
return result;
}
@Override
public Complex sub(Complex a, Complex b) {
var result = new Complex(a.real() - b.real(), a.imaginary() - b.imaginary());
System.out.printf("%s - %s = %s%n", a, b, result);
return result;
}
}
```

现在，你可以通过 `Main` 类中的以下代码来测试这个复数计算器：

```
package com.apress.spring6recipes.calculator;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
public class Main {
public static void main(String[] args) {
var cfg = CalculatorConfiguration.class;
try (var ctx = new AnnotationConfigApplicationContext(cfg)) {
var complexCalculator = ctx.getBean( ComplexCalculator.class);
complexCalculator.add(new Complex(1, 2), new Complex(2, 3));
complexCalculator.sub(new Complex(5, 8), new Complex(2, 3));
}
}
}
```

到目前为止，复数计算器运行良好。不过，你可能希望通过缓存复数对象来提升计算器的性能。由于缓存是一个典型的横切关注点，你可以使用切面将其模块化：

```
package com.apress.spring6recipes.calculator;
import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;
import java.util.Map;
import java.util.concurrent.ConcurrentHashMap;
@Aspect
public class ComplexCachingAspect {
private final Map cache = new ConcurrentHashMap();
@Around("call(public com.apress.spring6recipes.calculator.Complex.new(int, int)) && args(a,b)")
public Object cacheAround(ProceedingJoinPoint pjp, int a, int b) throws Throwable {
var key = a + "," + b;
return cache.compute(key, (key1, val) -> checkCacheOrCalculate(pjp, key1, val));
}
private Complex checkCacheOrCalculate(ProceedingJoinPoint pjp, String key, Complex current) {
if (current == null) {
try {
System.out.println("Cache MISS for (" + key + ")");
return (Complex) pjp.proceed();
} catch (Throwable ex) {
throw new IllegalStateException(ex);
}
} else {
System.out.println("Cache HIT for (" + key + ")");
return current;
}
}
}
```

在这个切面中，你将复数对象缓存到一个映射中，以其实部和虚部作为键。然后，最合适的缓存查找时机是在通过调用构造函数创建复数对象时。你使用 AspectJ 的切入点表达式 `call` 来捕获调用 `Complex(int,int)` 构造函数的连接点。

接下来，你需要一个环绕通知来改变返回值。如果在缓存中找到了相同值的复数对象，则直接将其返回给调用者。否则，继续执行原始的构造函数调用来创建一个新的复数对象。在将其返回给调用者之前，你将其缓存到映射中供后续使用。

Spring AOP 不支持 `call` 切入点，因此如果你尝试让 Spring 扫描该切入点注解，将会收到错误信息“unsupported pointcut primitive call”。

由于 Spring AOP 不支持这种类型的切入点，你必须使用 AspectJ 框架来应用这个切面。AspectJ 框架的配置通过类路径根目录下 `META-INF` 目录中名为 `aop.xml` 的文件来完成：

在此 AspectJ 配置文件中，你必须指定切面以及你想要织入切面的类。这里，你指定将 `ComplexCachingAspect` 织入到 `com.apress.spring6recipes.calculator` 包中的所有类。

最后，要实现这种加载时织入，你需要通过下一节描述的两种方式之一来运行应用程序。

#### 通过 AspectJ 织入器进行加载时织入

AspectJ 提供了一个加载时织入代理来实现加载时织入。你只需在运行应用程序的命令中添加一个 VM 参数即可。这样，当你的类被加载到 JVM 中时，它们就会被织入：

```
java --add-opens java.base/java.lang=ALL-UNNAMED -javaagent:lib/aspectjweaver-1.9.19.jar -jar recipe_1_20_ii-6.0.0-all.jar
```

![](img/314861_5_En_1_Figp_HTML.gif)一个带阴影圆圈的字母 i 图标，表示信息符号。  由于 JDK 的变更以及 AspectJ 尚未找到解决方案来绕过此问题，因此需要使用 `-add-opens` 参数。

如果你使用上述参数运行应用程序，你将得到以下输出和缓存状态。AspectJ 代理会通知所有对 `Complex(int,int)` 构造函数的调用：

```
Cache MISS for (1,2)
Cache MISS for (2,3)
Cache MISS for (3,5)
(1 + 2i) + (2 + 3i) = (3 + 5i)
Cache MISS for (5,8)
Cache HIT for (2,3)
Cache HIT for (3,5)
(5 + 8i) - (2 + 3i) = (3 + 5i)
```



#### 通过 Spring 加载时织入器实现加载时织入

Spring 为不同的运行时环境提供了多个加载时织入器。要为你的 Spring 应用启用合适的加载时织入器，需要在配置中添加 `@EnableLoadTimeWeaving`。

Spring 能够自动检测最适合你运行时环境的加载时织入器。某些 Java EE 应用服务器的类加载器支持 Spring 加载时织入机制，因此无需在其启动命令中指定 Java 代理。然而，对于简单的 Java 应用，你仍然需要 Spring 提供的织入代理来启用加载时织入。你必须在启动命令的 VM 参数中指定 Spring 代理：

```
java --add-opens java.base/java.lang=ALL-UNNAMED -javaagent:lib/spring-instrument-6.0.3.jar -jar recipe_1_20_ii-6.0.0-all.jar
```

运行你的应用后，你将看到以下输出和缓存状态：

```
Cache MISS for (3,5)
(1 + 2i) + (2 + 3i) = (3 + 5i)
Cache HIT for (3,5)
(5 + 8i) - (2 + 3i) = (3 + 5i)
```

这是因为 Spring 代理仅对 Spring IoC 容器中声明的 bean 所调用的 `Complex(int,int)` 构造函数进行增强。由于复数操作数是在 `Main` 类中创建的，Spring 代理不会增强它们的构造函数调用。

![](img/314861_5_En_1_Figq_HTML.gif)一个带阴影圆圈的字母 i 图标，代表信息符号。  使用独立配置时，通过 instrumentation 实现加载时织入可能会有些棘手。这是因为组件扫描会在流程早期就拾取类，甚至早于 Spring 启用加载时织入。为了解决这个问题，你可以通过使用 `ApplicationInitializer` 在流程早期启用加载时织入，并在刷新 `ApplicationContext` 之前调用它：

```
package com.apress.spring6recipes.calculator;
import org.springframework.context.ApplicationContextInitializer;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import org.springframework.context.weaving.AspectJWeavingEnabler;
import org.springframework.context.weaving.DefaultContextLoadTimeWeaver;
public class LoadTimeWeaverApplicationContextInitializer
implements ApplicationContextInitializer {
@Override
public void initialize(AnnotationConfigApplicationContext applicationContext) {
var beanClassLoader = applicationContext.getBeanFactory().getBeanClassLoader();
var ltw = new DefaultContextLoadTimeWeaver(beanClassLoader);
AspectJWeavingEnabler.enableAspectJWeaving(ltw, beanClassLoader);
}
}
```

这将在上下文完全启动之前，为正在使用的类加载器启用加载时织入。关于如何使用和启用此初始化器，请参阅源码中提供的 `Main` 类。

## 1-21. 在 Spring 中配置切面

### 问题

AspectJ 框架中使用的切面由 AspectJ 框架自身实例化。因此，你必须从 AspectJ 框架中获取切面实例才能对其进行配置。

### 解决方案

每个 AspectJ 切面都提供了一个名为 `Aspects` 的工厂类，其中包含一个名为 `aspectOf()` 的静态工厂方法，允许你访问当前的切面实例。在 Spring IoC 容器中，你可以通过调用 `Aspects.aspectOf(ComplexCachingAspect.class)` 来声明由该工厂方法创建的 bean。

### 工作原理

例如，你可以允许通过 setter 方法预先配置 `ComplexCachingAspect` 的缓存映射：

```
package com.apress.spring6recipes.calculator;
import org.aspectj.lang.annotation.Aspect;
import java.util.Map;
import java.util.concurrent.ConcurrentHashMap;
@Aspect
public class ComplexCachingAspect {
private final Map cache = new ConcurrentHashMap();
public void setCache(Map cache) {
this.cache.clear();
this.cache.putAll(cache);
}
}
```

要配置该切面，创建一个带有 `@Bean` 注解的方法，该方法调用前述的工厂方法 `Aspects.aspectOf`。这将返回切面的实例。然后可以对该实例进行配置：

```
package com.apress.spring6recipes.calculator;
import org.aspectj.lang.Aspects;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import java.util.concurrent.ConcurrentHashMap;
@Configuration
@ComponentScan
public class CalculatorConfiguration {
@Bean
public ComplexCachingAspect complexCachingAspect() {
var cache = new ConcurrentHashMap();
cache.put("2,3", new Complex(2, 3));
cache.put("3,5", new Complex(3, 5));
var complexCachingAspect = Aspects.aspectOf(ComplexCachingAspect.class);
complexCachingAspect.setCache(cache);
return complexCachingAspect;
}
}
```

要运行该应用，请使用 AspectJ 的织入器：

```
java -javaagent:lib/aspectjweaver-1.9.19.jar -jar recipe_1_21-6.0.0-all.jar
```

## 1-22. 使用 AOP 将 POJO 注入领域对象

### 问题

在 Spring IoC 容器中声明的 bean 可以通过 Spring 的依赖注入能力相互装配。然而，在 Spring IoC 容器外部创建的对象无法通过配置方式装配到 Spring bean 上。你必须通过编程代码手动执行装配。

### 解决方案

在 Spring IoC 容器外部创建的对象通常是领域对象。它们通常通过 `new` 操作符创建，或来自数据库查询的结果。要将 Spring bean 注入到 Spring 外部创建的领域对象中，你需要 AOP 的帮助。实际上，Spring bean 的注入也是一种横切关注点。由于领域对象并非由 Spring 创建，你不能使用 Spring AOP 进行注入。Spring 为此提供了一个专门的 AspectJ 切面。你可以在 AspectJ 框架中启用此切面。



### 工作原理

假设你有一个用于格式化复数的全局格式化器。该格式化器接受一个格式化模式，并使用标准的 `@Component` 注解来实例化一个 POJO：

```
package com.apress.spring6recipes.calculator;
import org.springframework.stereotype.Component;
@Component
public class ComplexFormatter {
private String pattern = "(a + bi)";
public void setPattern(String pattern) {
this.pattern = pattern;
}
public String format(Complex complex) {
return pattern
.replaceAll("a", Integer.toString(complex.real()))
.replaceAll("b", Integer.toString(complex.imaginary()));
}
}
```

在 `Complex` 类中，你希望在 `toString()` 方法中使用此格式化器将复数转换为字符串。该类暴露了一个用于 `ComplexFormatter` 的 setter 方法。

然而，由于 `Complex` 对象并非由 Spring IoC 容器实例化，因此无法通过常规方式为其配置依赖注入。Spring 在其切面库中包含了 `AnnotationBeanConfigurerAspect`，用于配置任何对象的依赖关系，即使这些对象并非由 Spring IoC 容器创建。

首先，你必须使用 `@Configurable` 注解标注你的对象类型，以声明该类型的对象是可配置的：

```
package com.apress.spring6recipes.calculator;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Configurable;
import org.springframework.context.annotation.Scope;
import org.springframework.stereotype.Component;
@Configurable
@Component
@Scope("prototype")
public class Complex {
private final int real;
private final int imaginary;
private ComplexFormatter formatter;
public Complex(int real, int imaginary) {
this.real = real;
this.imaginary = imaginary;
}
public int imaginary() {
return imaginary;
}
public int real() {
return real;
}
@Autowired
public void setFormatter(ComplexFormatter formatter) {
this.formatter = formatter;
}
@Override
public String toString() {
return formatter.format(this);
}
}
```

除了 `@Configurable` 注解之外，你还用标准的 `@Component`、`@Scope` 和 `@Autowired` 注解修饰了该 POJO，以便 Bean 获得标准的 Spring 行为。然而，`@Configurable` 注解是最重要的配置部分，为此 Spring 定义了一个便捷的注解 `@EnableSpringConfigured`，供你启用上述切面。你需要将此注解添加到 `@EnableLoadTimeWeaving` 注解旁边，因为 `@Configurable` 仅能配合加载时织入或编译时织入使用：

```
package com.apress.spring6recipes.calculator;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.EnableLoadTimeWeaving;
import org.springframework.context.annotation.aspectj.EnableSpringConfigured;
import org.springframework.context.weaving.AspectJWeavingEnabler;
@Configuration
@EnableSpringConfigured
@EnableLoadTimeWeaving
@ComponentScan
public class CalculatorConfiguration {
@Bean
public static AspectJWeavingEnabler aspectJWeavingEnabler() {
return new AspectJWeavingEnabler();
}
}
```

当带有 `@Configurable` 注解的类被实例化时，该切面会查找一个原型作用域的 Bean 定义，其类型与该类相同。然后，它会根据此 Bean 定义配置新实例。如果 Bean 定义中声明了属性，新实例也将拥有由该切面设置的相同属性。

最后，要运行应用程序，你需要使用 AspectJ 代理在加载时将切面织入到你的类中：

```
java -javaagent:lib/aspectjweaver-1.9.19.jar -jar recipe_1_21-6.0.0-all.jar
```

## 1-23. 使用 Spring 和 TaskExecutors 实现并发

### 问题

你想使用 Spring 构建一个多线程的并发程序，但由于没有标准方法，不知道应该采用何种方式。

### 解决方案

使用 Spring 的 `TaskExecutor` 抽象。该抽象为多种环境提供了大量实现，包括基本的 Java SE Executor 实现、CommonJ WorkManager 实现以及自定义实现。

在 Spring 中，所有实现都是统一的，并且也可以转换为 Java SE 的 Executor 接口。



### 工作原理

线程处理是一个棘手的问题，在 Java SE 环境中使用标准线程实现起来尤其繁琐。并发是服务端组件的另一个重要方面，但在企业级 Java 领域几乎没有标准化。事实上，Java 企业版规范的部分内容明确禁止显式创建和操作线程。

在 Java SE 领域，多年来引入了许多选项来处理线程和并发问题。首先，从第一天起，JDK 1.0 就提供了标准的 `java.lang.Thread` 支持。Java 1.3 引入了 `java.util.TimerTask` 来支持定期执行某些工作。Java 5 首次推出了 `java.util.concurrent` 包，以及围绕 `java.util.concurrent.Executor` 构建线程池的重构层次结构。Java 19 以预览形式引入了虚拟线程和结构化并发编程的概念。

Executor 的应用程序编程接口（API）很简单：

```
package java.util.concurrent;
public interface Executor {
void execute(Runnable command);
}
```

子接口 `ExecutorService` 提供了更多用于管理线程的功能，并支持向线程引发事件，例如 `shutdown()`。自 Java SE 5.0 以来，JDK 附带了多种实现。其中许多可以通过 `java.util.concurrent` 包中的静态工厂方法获得。以下是几个使用 Java SE 类的示例。

`ExecutorService` 类提供了一个 `submit()` 方法，该方法返回一个 `Future<T>` 对象。`Future<T>` 的实例可用于跟踪通常异步执行的线程的进度。你可以调用 `Future.isDone()` 或 `Future.isCancelled()` 来分别确定任务是否已完成或已取消。当你在 `Runnable` 实例（其 `run` 方法没有返回类型）内部使用 `ExecutorService` 和 `submit()` 时，对返回的 `Future` 调用 `get()` 会返回 `null` 或提交时指定的值：

```
Runnable task = new Runnable(){
public void run(){
try{
Thread.sleep( 1000 * 60 ) ;
System.out.println("Done sleeping for a minute, returning! " );
} catch (Exception ex) { /* ... */ }
}
};
var executorService  = Executors.newCachedThreadPool() ;
if(executorService.submit(task, Boolean.TRUE).get().equals( Boolean.TRUE ))
System.out.println( "Job has finished!");
```

有了这些背景信息，我们可以探索各种实现的一些特性。例如，下面是一个旨在使用 Runnable 标记时间流逝的类：

```
package com.apress.spring6recipes.executors;
import com.apress.spring6recipes.utils.Utils;
import java.time.LocalDate;
import java.util.concurrent.TimeUnit;
public class DemonstrationRunnable implements Runnable {
@Override
public void run() {
Utils.sleep(1, TimeUnit.SECONDS);
System.out.printf("Hello at %s from %s%n",
LocalDate.now(), Thread.currentThread().getName());
}
}
```

在探索 Java SE Executor 和 Spring 的 `TaskExecutor` 支持时，你将使用相同的实例：

```
package com.apress.spring6recipes.executors;
import java.time.LocalDateTime;
import java.time.format.DateTimeFormatter;
import java.util.concurrent.Executors;
import java.util.concurrent.TimeUnit;
public class ExecutorsDemo {
public static void main(String[] args) throws Throwable {
var task = new DemonstrationRunnable();
// 将创建一个线程池，并尽可能重用先前创建的线程
try (var cachedThreadPoolExecutorService = Executors.newCachedThreadPool()) {
if (cachedThreadPoolExecutorService.submit(task).get() == null)
printStatus(cachedThreadPoolExecutorService);
}
// 限制创建新线程的数量，其余线程排队
try (var fixedThreadPool = Executors.newFixedThreadPool(100)) {
if (fixedThreadPool.submit(task).get() == null)
printStatus(fixedThreadPool);
}
// 一次不使用超过一个线程
try (var singleThreadExecutorService = Executors.newSingleThreadExecutor()) {
if (singleThreadExecutorService.submit(task).get() == null)
printStatus(singleThreadExecutorService);
}
// 支持发送具有已知结果的任务
try (var es = Executors.newCachedThreadPool()) {
if (es.submit(task, Boolean.TRUE).get().equals(Boolean.TRUE))
System.out.println("Job has finished!");
}
// 为每个启动的任务创建一个虚拟线程
try (var vt = Executors.newVirtualThreadPerTaskExecutor()) {
if (vt.submit(task).get() == null) {
printStatus(vt);
}
}
// 模拟 TimerTask
try (var scheduledThreadExecutorService = Executors.newScheduledThreadPool(10)) {
if (scheduledThreadExecutorService.schedule(task, 30, TimeUnit.SECONDS).get() == null)
printStatus(scheduledThreadExecutorService);
// 这不会停止，直到遇到异常或被取消
scheduledThreadExecutorService.scheduleAtFixedRate(task, 0, 5, TimeUnit.SECONDS);
}
}
static void printStatus(Object executor) {
var type = executor.getClass().getSimpleName();
var datetime = LocalDateTime.now();
System.out.printf("The %s has succeeded at %s%n", type, datetime.format(DateTimeFormatter.ISO_DATE_TIME));
}
}
```

如果你使用接受 `Callable<T>` 的 `ExecutorService` 的 `submit()` 方法版本，那么 `submit()` 将返回 `Callable` 中主 `call()` 方法返回的任何内容。`Callable` 的接口如下：

```
package java.util.concurrent;
@FunctionalInterface
public interface Callable {
V call() throws Exception;
}
```

在 Jakarta EE 领域，已经创建了解决这类问题的不同方法，因为 Java EE 在设计上限制了线程的处理。

Quartz（一个任务调度框架）是最早填补这一线程功能空白的解决方案之一，它提供了一种提供调度和并发的解决方案。JEE 连接器架构（JCA）1.5 是另一个规范，它为集成功能提供了一种原始类型的网关，并支持临时并发。JCA 组件会收到传入消息的通知并并发响应。JCA 1.5 提供了一个原始的、有限的企业服务总线（ESB）——类似于集成功能，但远不如 SpringSource 的 Spring Integration 框架那样精巧。

不过，应用服务器供应商并没有忽视对并发的需求。许多其他举措也走到了前台。例如，2003 年，IBM 和 BEA 共同创建了 Timer and Work Manager API，该 API 最终成为 JSR-237，随后与 JSR-236 合并，专注于如何在托管环境中实现并发。服务数据对象（SDO）规范 JSR-235 也有类似的解决方案。此外，近年来也涌现出 CommonJ API 的开源实现，以实现相同的解决方案。

问题在于，没有一种可移植、标准、简单的方法来在托管环境中控制线程并为组件提供并发性，这与 Java SE 解决方案的情况类似。

Spring 通过 `org.springframework.core.task.TaskExecutor` 接口提供了一个统一的解决方案。`TaskExecutor` 抽象扩展了 `java.util.concurrent.Executor`。

事实上，`TaskExecutor` 接口在 Spring 框架内部被大量使用。例如，对于 Spring Quartz 集成（支持线程）和消息驱动 POJO（MDP）容器支持，广泛使用了 TaskExecutor：

```
package org.springframework.core.task;
import java.util.concurrent.Executor;
@FunctionalInterface
public interface TaskExecutor extends Executor {
void execute(Runnable task);
}
```

在某些方面，各种解决方案镜像了核心 JDK 选项提供的功能。在其他方面，它们非常独特，并提供了与其他框架（如 CommonJ WorkManager）的集成。这些集成通常采用类的形式，该类可以存在于目标框架中，但你可以像操作任何其他 TaskExecutor 抽象一样操作它。

尽管支持将现有的 Java SE `Executor` 或 `ExecutorService` 适配为 `TaskExecutor`，但这在 Spring 中并不那么重要，因为 `TaskExecutor` 的基类本身就是一个 `Executor`。通过这种方式，Spring 中的 `TaskExecutor` 弥合了 Java EE 和 Java SE 上各种解决方案之间的差距。

接下来，让我们看一个 `TaskExecutor` 的简单示例，使用之前定义的同一个 `Runnable`。该代码的客户端是一个简单的 Spring POJO，你向其中注入了 `TaskExecutor` 的各种实例，唯一目的是提交 `Runnable`：

```
package com.apress.spring6recipes.executors;
import jakarta.annotation.PostConstruct;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import org.springframework.core.task.SimpleAsyncTaskExecutor;
import org.springframework.core.task.SyncTaskExecutor;
import org.springframework.core.task.support.TaskExecutorAdapter;
import org.springframework.scheduling.concurrent.ConcurrentTaskExecutor;
import org.springframework.scheduling.concurrent.ThreadPoolTaskExecutor;
import org.springframework.stereotype.Component;
@Component
public class SpringExecutorsDemo {
@Autowired
private SimpleAsyncTaskExecutor asyncTaskExecutor;
@Autowired
private SyncTaskExecutor syncTaskExecutor;
@Autowired
private TaskExecutorAdapter taskExecutorAdapter;
@Autowired
private ThreadPoolTaskExecutor threadPoolTaskExecutor;
@Autowired
private DemonstrationRunnable task;
@Autowired
private ConcurrentTaskExecutor virtualThreadsTaskExecutor;
@PostConstruct
public void submitJobs() {
syncTaskExecutor.execute(task);
taskExecutorAdapter.submit(task);
asyncTaskExecutor.submit(task);
for (int i = 0 ; i < 500; i++) {
virtualThreadsTaskExecutor.submit(task);
}
for (int i = 0; i < 500; i++) {
threadPoolTaskExecutor.submit(task);
}
}
public static void main(String[] args) {
var cfg = ExecutorsConfiguration.class;
try (var ctx = new AnnotationConfigApplicationContext(cfg)) {}
}
}
```

应用程序上下文演示了这些不同 `TaskExecutor` 实现的创建。大多数都非常简单，你可以手动创建它们。只有一种情况，你委托给一个工厂 bean 来自动触发执行：

```
package com.apress.spring6recipes.executors;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.core.task.SimpleAsyncTaskExecutor;
import org.springframework.core.task.SyncTaskExecutor;
import org.springframework.core.task.support.TaskExecutorAdapter;
import org.springframework.scheduling.concurrent.ConcurrentTaskExecutor;
import org.springframework.scheduling.concurrent.ScheduledExecutorFactoryBean;
import org.springframework.scheduling.concurrent.ScheduledExecutorTask;
import org.springframework.scheduling.concurrent.ThreadPoolTaskExecutor;
import java.util.concurrent.Executors;
@Configuration
@ComponentScan
public class ExecutorsConfiguration {
@Bean
public TaskExecutorAdapter taskExecutorAdapter() {
return new TaskExecutorAdapter(Executors.newCachedThreadPool());
}
@Bean
public SimpleAsyncTaskExecutor simpleAsyncTaskExecutor() {
return new SimpleAsyncTaskExecutor();
}
@Bean
public SyncTaskExecutor syncTaskExecutor() {
return new SyncTaskExecutor();
}
@Bean
public ScheduledExecutorFactoryBean scheduledExecutorFactoryBean(ScheduledExecutorTask scheduledExecutorTask) {
var scheduledExecutorFactoryBean = new ScheduledExecutorFactoryBean();
scheduledExecutorFactoryBean.setScheduledExecutorTasks(scheduledExecutorTask);
return scheduledExecutorFactoryBean;
}
@Bean
public ScheduledExecutorTask scheduledExecutorTask(Runnable runnable) {
var scheduledExecutorTask = new ScheduledExecutorTask();
scheduledExecutorTask.setPeriod(50);
scheduledExecutorTask.setRunnable(runnable);
return scheduledExecutorTask;
}
@Bean
public ThreadPoolTaskExecutor threadPoolTaskExecutor() {
var taskExecutor = new ThreadPoolTaskExecutor();
taskExecutor.setCorePoolSize(50);
taskExecutor.setMaxPoolSize(100);
taskExecutor.setAllowCoreThreadTimeOut(true);
taskExecutor.setWaitForTasksToCompleteOnShutdown(true);
return taskExecutor;
}
@Bean
public ConcurrentTaskExecutor virtualThreadsTaskExecutor() {
var virtualThreadsExecutor = Executors.newVirtualThreadPerTaskExecutor();
return new ConcurrentTaskExecutor(virtualThreadsExecutor);
}
}
```

前面的代码展示了 `TaskExecutor` 接口的不同实现。第一个 bean，`TaskExecutorAdapter` 实例，是对 `java.util.concurrence.Executors` 实例的简单包装，因此你可以根据 Spring `TaskExecutor` 接口来处理它。你在这里使用 Spring 来配置一个 `Executor` 实例，并将其作为构造函数参数传入。

`SimpleAsyncTaskExecutor` 为每个提交的任务提供一个新 `Thread`。它不进行线程池化或重用。每个提交的任务都在一个线程中异步运行。

`SyncTaskExecutor` 是 `TaskExecutor` 实现中最简单的一个。提交任务是同步的，相当于启动一个 `Thread`，运行它，然后立即使用 `join()` 连接它。这实际上与在调用线程中手动调用 `run()` 方法相同，完全跳过了线程处理。

`ScheduledExecutorFactoryBean` 自动触发定义为 `ScheduledExecutorTask` bean 的任务。你可以指定一个 `ScheduledExecutorTask` 实例列表来同时触发多个任务。`ScheduledExecutorTask` 实例可以接受一个周期来间隔任务的执行。

`ThreadPoolTaskExecutor` 是一个基于 `java.util.concurrent.ThreadPoolExecutor` 构建的完整线程池实现。

最后一个示例展示了如何在你的应用程序中利用新的虚拟线程。你可以使用 `TaskExecutorAdapter` 包装通过 `Executors.newVirtualThreadPerTaskExecutor()` 创建的 `Executor`，这将使其适配 `TaskExecutor` 接口。另一种选择是使用 `ConcurrentTaskExecutor` 包装它以达到相同的结果。

添加了 `jakarta.enterprise.concurrent` 包，特别是 `ManagedExecutorService`。`ManagedExecutorService` 的实例必须由符合 JEE9 规范的服务器提供。如果你想将此机制与 Spring `TaskExecutor` 支持一起使用，你可以配置一个 `DefaultManagedTaskExecutor`，它将尝试检测默认的 `ManagedExecutorService`（如规范所述），或者你可以显式配置它。

`TaskExecutor` 支持提供了一种通过统一接口访问应用服务器上调度服务的强大方式。如果你正在寻找可以部署在任何应用服务器（例如 Tomcat 和 Jetty）上的更健壮（尽管重量级得多）的支持，你可以考虑 Spring 的 Quartz 支持。



## 1-24. 在普通 Java 对象之间传递应用事件

### 问题

在普通 Java 对象（POJO）之间的典型通信中，发送方必须定位到接收方才能调用其方法。这种情况下，发送方 POJO 必须知晓接收方组件。这种通信方式直接且简单，但发送方和接收方 POJO 之间是紧密耦合的。

当使用 IoC 容器时，POJO 可以通过接口而非实现进行通信。这种通信模型有助于降低耦合度。然而，只有当发送方组件需要与单个接收方通信时，这种方式才高效。当发送方需要与多个接收方通信时，它必须逐一调用这些接收方。

### 解决方案

Spring 的应用上下文支持其 Bean 之间基于事件的通信。在基于事件的通信模型中，发送方 POJO 只需发布事件，无需知道接收方是谁，因为实际上可能存在多个接收方。同样，接收方也无需知道是谁发布了事件。它可以同时监听来自不同发送方的多个事件。通过这种方式，发送方和接收方组件实现了松散耦合。

传统上，要监听事件，Bean 必须实现 `ApplicationListener` 接口，并通过指定类型参数（即 `ApplicationListener<CheckoutEvent>`）来指明它希望被通知的事件类型。这类监听器只能监听那些继承自 `ApplicationEvent` 的事件，因为这是 `ApplicationListener` 接口的类型签名。

要发布事件，Bean 需要访问 `ApplicationEventPublisher`，并通过调用 `publishEvent` 方法来发送事件。为了获取 `ApplicationEventPublisher` 的访问权限，类可以实现 `ApplicationEventPublisherAware` 接口，或者在类型为 `ApplicationEventPublisher` 的字段上使用 `@Autowired` 注解。

### 工作原理

#### 使用 ApplicationEvent 定义事件

启用基于事件通信的第一步是定义事件。假设你希望一个收银员 Bean 在购物车结账后发布一个 `CheckoutEvent`。该事件包含一个结账时间属性：

```
package com.apress.spring6recipes.shop;
import org.springframework.context.ApplicationEvent;
import java.time.LocalDateTime;
@SuppressWarnings("serial")
public class CheckoutEvent extends ApplicationEvent {
private final ShoppingCart cart;
private final LocalDateTime time;
public CheckoutEvent(ShoppingCart cart, LocalDateTime time) {
super(cart);
this.cart = cart;
this.time = time;
}
public ShoppingCart getCart() {
return cart;
}
public LocalDateTime getTime() {
return this.time;
}
}
```

#### 发布事件

要发布事件，你只需创建一个事件实例，并调用应用事件发布器的 `publishEvent()` 方法。通过实现 `ApplicationEventPublisherAware` 接口可以获取该发布器的访问权限：

```
package com.apress.spring6recipes.shop;
import org.springframework.context.ApplicationEventPublisher;
import org.springframework.context.ApplicationEventPublisherAware;
import java.time.LocalDateTime;
public class Cashier implements ApplicationEventPublisherAware {
private ApplicationEventPublisher applicationEventPublisher;
@Override
public void setApplicationEventPublisher(ApplicationEventPublisher aep) {
this.applicationEventPublisher = aep;
}
public void checkout(ShoppingCart cart) {
var event = new CheckoutEvent(cart, LocalDateTime.now());
applicationEventPublisher.publishEvent(event);
}
}
```

或者，你也可以简单地通过字段属性或构造函数参数进行自动装配：

```
package com.apress.spring6recipes.shop;
import org.springframework.context.ApplicationEventPublisher;
import org.springframework.stereotype.Component;
@Component
public class Cashier {
private final ApplicationEventPublisher applicationEventPublisher;
public Cashier(ApplicationEventPublisher applicationEventPublisher) {
this.applicationEventPublisher = applicationEventPublisher;
}
}
```

#### 监听事件

任何在应用上下文中定义的、实现了 `ApplicationListener` 接口的 Bean，都会收到所有与类型参数匹配的事件的通知（通过这种方式，你可以监听特定的事件组，例如 `ApplicationContextEvent`）：

```
package com.apress.spring6recipes.shop;
import org.springframework.context.ApplicationListener;
import org.springframework.stereotype.Component;
@Component
public class CheckoutListener implements ApplicationListener {
@Override
public void onApplicationEvent(CheckoutEvent event) {
// 对结账时间进行任何你喜欢的操作
System.out.printf("结账事件 [%s]%n", event.getTime());
}
}
```

你也可以使用 `@EventListener` 注解来创建事件监听器，而无需实现 `ApplicationListener` 接口：

```
package com.apress.spring6recipes.shop;
import org.springframework.context.event.EventListener;
import org.springframework.stereotype.Component;
@Component
public class CheckoutListener {
@EventListener
public void onApplicationEvent(CheckoutEvent event) {
// 对结账时间进行任何你喜欢的操作
System.out.printf("结账事件 [%s]%n", event.getTime());
}
}
```

接下来，你需要在应用上下文中注册监听器，以便监听所有事件。注册过程非常简单，只需声明该监听器的 Bean 实例，或者让组件扫描自动检测到它即可。应用上下文能够识别实现了 `ApplicationListener` 接口的 Bean，以及那些方法上标注了 `@EventListener` 注解的 Bean，并在它们感兴趣的事件发生时通知它们。

使用 `@EventListener` 还有一个很好的特性，那就是事件不再需要继承 `ApplicationEvent`。这样一来，你的事件就不再依赖 Spring 框架的类，而是再次回归为普通的 POJO：

```
package com.apress.spring6recipes.shop;
import java.time.LocalDateTime;
public class CheckoutEvent {
private final ShoppingCart cart;
private final LocalDateTime time;
public CheckoutEvent(ShoppingCart cart, LocalDateTime time) {
this.cart = cart;
this.time = time;
}
public ShoppingCart getCart() {
return this.cart;
}
public LocalDateTime getTime() {
return time;
}
}
```

![](img/314861_5_En_1_Figr_HTML.gif)一个带阴影圆圈的字母 i 图标，代表信息符号。 最后，请记住，应用上下文本身也会发布容器事件，例如 `ContextClosedEvent`、`ContextRefreshedEvent` 和 `RequestHandledEvent`。如果任何 Bean 希望收到这些事件的通知，它们可以实现 `ApplicationListener` 接口。

## 1-25. 以函数式方式创建和注册 Bean

### 问题

自 Java 8 起，Java 中可以实现一些函数式编程。Java 的更新版本对此进行了改进，在日常 Java 编程中使用 lambda 表达式和方法引用等已成为常见做法。现在，你也希望将这种方式与 Spring 结合使用，以创建配置文件，而不是使用注解。

### 解决方案

Spring 的 `GenericApplicationContext`（它是许多 `ApplicationContext` 实现类的基类）提供了多种变体的 `registerBean` 方法。其中最有趣且最实用的变体是那些接受 `Supplier` 或 `BeanDefinitionCustomizer` 参数的方法。这些方法可用于将 Bean 注册到上下文中，使其参与容器和 Bean 的生命周期，或作为其他 Bean 的依赖项。

### 工作原理

让我们以配方 1-1 和配方 1-3 中的代码为例，将其重写为更具函数式风格的代码。



#### 以函数式方式创建简单 Bean

要注册一个 Bean，你需要创建一个 `ApplicationContext` 的实例。这里我们可以使用之前用过的同一个 `AnnotationConfigApplicationContext`，但无需任何配置。接下来，我们调用 `registerBean` 方法来注册某个特定类型（第一个参数）的 Bean，然后提供最终将创建该 Bean 的 `Supplier`。

完成 Bean 注册后，你需要在上下文中调用 `refresh`，它将创建所有需要的 Bean 实例：

```
package com.apress.spring6recipes.sequence;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
public class Main {
public static void main(String[] args) {
try (var ctx = new AnnotationConfigApplicationContext()) {
ctx.registerBean(Sequence.class, () -> new Sequence("30", "A", 10000));
ctx.refresh();
var generator = ctx.getBean(Sequence.class);
System.out.println(generator.nextValue());
System.out.println(generator.nextValue());
}
}
}
```

#### 以函数式方式创建具有依赖关系的多个 Bean

之前我们只注册了一个不引用其他 Bean 的简单 Bean。要获取对其他 Bean 的引用，你可以访问 `ApplicationContext` 并调用 `getBean`，或者更好的方式是调用 `getBeanProvider`（另请参阅配方 1-3），以根据需求获取可选的 Bean 或 Bean 列表。

我们仍然需要创建要使用的 `ApplicationContext` 类型，并为两个 Bean 调用 `registerBean` 方法。`Sequence` 的注册现在稍微复杂一些，因为我们需要获取所需 Bean 的引用。为此，我们使用 `getBeanProvider` 方法，该方法返回一个 `ObjectProvider`（参见配方 1-3），我们可以用它来获取所需的引用：

```
package com.apress.spring6recipes.sequence;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
public class Main {
public static void main(String[] args) {
try (var ctx = new AnnotationConfigApplicationContext()) {
ctx.registerBean(PrefixGenerator.class, () ->
new DatePrefixGenerator("yyyyMMdd"));
ctx.registerBean(Sequence.class, () -> {
var seq = new Sequence("A", 100000);
ctx.getBeanProvider(PrefixGenerator.class)
.ifUnique(seq::setPrefixGenerator);
return seq;
});
ctx.refresh();
var generator = ctx.getBean(Sequence.class);
System.out.println(generator.getSequence());
System.out.println(generator.getSequence());
}
}
}
```

#### 修改函数式注册 Bean 的 BeanDefinition

到目前为止，我们只是简单地注册了 Bean。但是如何将 Bean 定义为主要 Bean，或者更改作用域、顺序等呢？为此，我们可以传入一个 `BeanDefinitionCustomizer`（或多个，因为它是可变参数）来修改最终创建的 `BeanDefinition`。`BeanDefinition` 是用于创建 Bean 的配方，它保存了 Bean 的 `scope`、是否为主要 Bean 等信息。有关可设置的属性列表，请参见表 1-3。

表 1-3

BeanDefinition 的属性

| 属性 | 描述 |
| --- | --- |
| `autowireCandidate` | 此 Bean 是否应被用于自动装配场景。默认值为 `true`。 |
| `dependsOn` | 此 Bean 所依赖的 Bean 名称，与 `@DependsOn` 相同。 |
| `description` | Bean 的附加描述。 |
| `destroyMethodName` | 当 `ApplicationContext` 关闭并停止时，用作销毁回调的方法名称。类似于方法上的 `@PreDestroy`。 |
| `initMethodName` | 当 `ApplicationContext` 启动时，用作初始化回调的方法名称。类似于方法上的 `@PostConstruct`。 |
| `lazyInit` | Bean 是否应被延迟创建，即首次使用时创建。默认值为 `false`。 |
| `primary` | 当找到多个 Bean 时，此 Bean 是否为主要使用的 Bean。默认值为 `true`。 |
| `scope` | Bean 的作用域名称，与 `@Scope` 相同。 |

让我们添加一个 `BeanDefinitionCustomizer`，使 `Sequence` Bean 既延迟加载又具有原型作用域。创建一个类 `SequenceBeanDefinitionCustomizer`，它修改 `BeanDefinition`，将 `lazyInit` 设置为 `true`，并将 `scope` 设置为 `prototype`：

```
package com.apress.spring6recipes.sequence;
import org.springframework.beans.factory.config.BeanDefinition;
import org.springframework.beans.factory.config.BeanDefinitionCustomizer;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
public class Main {
public static void main(String[] args) {
try (var ctx = new AnnotationConfigApplicationContext()) {
ctx.registerBean(PrefixGenerator.class, () ->
new DatePrefixGenerator("yyyyMMdd"));
ctx.registerBean(Sequence.class, () -> {
var seq = new Sequence("A", 100000);
ctx.getBeanProvider(PrefixGenerator.class)
.ifUnique(seq::setPrefixGenerator);
return seq;
}, new SequenceBeanDefinitionCustomizer());
ctx.refresh();
var generator = ctx.getBean(Sequence.class);
System.out.println(generator.getSequence());
System.out.println(generator.getSequence());
}
}
}
class SequenceBeanDefinitionCustomizer implements BeanDefinitionCustomizer {
@Override
public void customize(BeanDefinition bd) {
bd.setScope(BeanDefinition.SCOPE_PROTOTYPE);
bd.setLazyInit(true);
}
}
```

![](img/314861_5_En_1_Figs_HTML.gif)一个带有灯泡轮廓的图标。  为清晰起见，自定义器的代码被编写为一个类。然而，它也可以写成另一个 lambda 表达式。



## 1-26\. 总结

在本章中，你学习了 Spring 的核心任务。你了解了 Spring 如何支持 `@Configuration` 和 `@Bean` 注解，通过 Java 配置类来实例化 POJO。你还学习了如何使用 `@Component` 注解来管理 Spring 中的 POJO。此外，你还了解了 `@Repository`、`@Service` 和 `@Controller` 注解，它们提供了比 `@Component` 注解更具体的行为。

你还学习了如何从其他 POJO 中引用 POJO，以及如何使用 `@Autowired` 注解，该注解可以按类型或名称自动关联 POJO。此外，你还探讨了标准的 `@Resource` 和 `@Inject` 注解如何通过自动装配来引用 POJO，而不是使用 Spring 特有的 `@Autowired` 注解。

接着，你学习了如何使用 `@Scope` 注解设置 Spring POJO 的作用域。你还了解了 Spring 如何读取外部资源，并使用 `@PropertySource` 和 `@Value` 注解在 POJO 配置和创建的上下文中使用这些数据。此外，你还学习了 Spring 如何通过使用 i18n 资源包来支持 POJO 中的不同语言。

接下来，你学习了如何使用 `@Bean` 注解的 `initMethod` 和 `destroyMethod` 属性，以及 `@PostConstruct` 和 `@PreDestroy` 注解来自定义 POJO 的初始化和销毁。此外，你还学习了如何使用 `@Lazy` 注解进行延迟初始化，以及使用 `@DependsOn` 注解定义初始化依赖关系。

然后，你了解了 Spring 的后置处理器，用于验证和修改 POJO 的值。接着，你探讨了如何使用 Spring 环境和配置文件来加载不同的 POJO 集合，包括如何使用 `@Profile` 注解。

接下来，你在 Spring 的上下文中探讨了面向切面编程，并学习了如何创建切面、切点和通知。这包括使用 `@Aspect` 注解，以及 `@Before`、`@After`、`@AfterReturning`、`@AfterThrowing` 和 `@Around` 注解。

接着，你学习了如何访问 AOP 连接点信息，并将其应用于不同的程序执行点。然后你学习了如何使用 `@Order` 注解指定切面的优先级，接着是如何重用切点定义。

在本章中，你还学习了如何编写 AspectJ 切点表达式，以及如何应用 AOP“引入”的概念，使得一个 POJO 可以同时继承多个实现类的行为。你还学习了如何使用 AOP 为 POJO 引入状态，以及如何应用加载时织入技术。

你还学习了如何在 Spring 中配置 AspectJ 切面，如何将 POJO 注入到领域对象中，以及如何使用 Spring 和 TaskExecutor 处理并发问题。最后但同样重要的是，你学习了如何在 Spring 中创建、发布和监听事件。

最后，我们了解了 `GenericApplicationContext` 及其子类上可用的函数式 Bean 注册 API。

