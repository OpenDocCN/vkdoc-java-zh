# 2. Spring Boot 基础

在本章中，我们将介绍 Spring Boot 的基本特性。

### 注意

要获得一个起点，请使用 Spring Initializr 创建一个项目。不需要额外的依赖项，只需要一个 Spring Boot 项目。

## 2.1 配置 Bean

### 问题

你希望 Spring Boot 将你的类用作 Bean。

### 解决方案

根据你的需求，你可以利用 `@ComponentScan` 自动检测你的类并创建实例；将其与 `@Autowired` 和 `@Value` 结合使用以注入依赖项或属性；或者，你可以使用带有 `@Bean` 注解的方法，以便对正在创建的 Bean 的构造过程拥有更多控制权。

### 工作原理

配方 1.1 中解释过，`@SpringBootApplication` 同时包含了 `@ComponentScan` 和 `@Configuration`。这意味着任何带有 `@Component` 注解的类都会被 Spring Boot 自动检测并实例化；同时，它也允许定义带有 `@Bean` 注解的方法来声明 Bean。

#### 使用 **@Component**

首先，创建一个类来引导应用程序。创建一个带有 `@SpringBootApplication` 注解的 `HelloWorldApplication`。

```
@SpringBootApplication
public class HelloWorldApplication {
public static void main(String[] args) {
SpringApplication.run(HelloWorldApplication.class, args);
}
}
```

### 提示

将带有 `@SpringBootApplication` 注解的类放在顶层包中；这样，它将自动检测该包及其所有子包中定义的所有带注解的组件、配置类等。^(⁵)

这将引导应用程序，检测所有带有 `@Component` 注解的类，并检测类路径上有哪些库（另请参见第 1 章）。运行这个 `HelloWorldApplication` 时，它不会做太多事情，因为没有什么需要检测或运行的内容。让我们创建一个简单的类，它将被 Spring Boot 自动检测到。

```
@Component
public class HelloWorld {
@PostConstruct
public void sayHello() {
System.out.println("Hello World, from Spring Boot 2!");
}
}
```

Spring Boot 将检测到这个类并从中创建一个 Bean 实例。带有 `@PostConstruct` 注解的方法在构造和所有依赖项注入完成后被调用。简单来说，在启动时，`sayHello` 方法将运行，控制台将打印出 `Hello World, from Spring Boot 2!` 这一行。

### 重要

当你需要扫描默认组件扫描未覆盖的包时，需要在带有 `@SpringBootApplication` 注解的类上添加一个 `@ComponentScan` 注解，以便这些包也能被扫描。这些包是在通过 `@SpringBootApplication` 注解应用的默认扫描之外额外扫描的。



#### 使用 **@Bean** 方法

除了自动检测组件，你还可以使用工厂方法来创建 Bean。当你希望或需要对 Bean 的构造过程拥有更多控制权时，这种方法非常有用。工厂方法是一个使用 `@Bean`^(⁶) 注解的方法，它将被用于在应用上下文中注册一个 Bean。该 Bean 的名称与方法名相同。该方法可以包含参数，这些参数将被解析为应用上下文中的其他 Bean。

让我们创建一个能够对整数进行基本计算的应用。首先，编写 `Calculator` 类；它将在构造函数中接收一个 `Operation` Bean 的集合。`Operation` 是一个接口，其不同的实现将负责执行实际的计算。

```
package com.apress.springbootrecipes.calculator;
import java.util.Collection;
public class Calculator {
private final Collection operations;
public Calculator(Collection operations) {
this.operations = operations;
}
public void calculate(int lhs, int rhs, char op) {
for (var operation : operations) {
if (operation.handles(op)) {
var result = operation.apply(lhs, rhs);
System.out.printf("%d %s %d = %s%n", lhs, op, rhs, result);
return;
}
}
throw new IllegalArgumentException("Unknown operation " + op);
}
}
```

在 `calculate` 方法中，通过 `Operation.handles` 方法检测正确的 `Operation`；找到后，调用 `Operation.apply` 方法执行实际计算。如果传入的计算器无法处理的操作，则会抛出异常。

`Operation` 接口是一个简单的接口，包含前面提到的两个方法。

```
package com.apress.springbootrecipes.calculator;
public interface Operation {
int apply(int lhs, int rhs);
boolean handles(char op);
}
```

现在，让我们添加两个操作：一个用于加法，一个用于乘法。

```
package com.apress.springbootrecipes.calculator.operation;
import com.apress.springbootrecipes.calculator.Operation;
import org.springframework.stereotype.Component;
@Component
class Addition implements Operation {
@Override
public int apply(int lhs, int rhs) {
return lhs + rhs;
}
@Override
public boolean handles(char op) {
return '+' == op;
}
}
package com.apress.springbootrecipes.calculator.operation;
import com.apress.springbootrecipes.calculator.Operation;
import org.springframework.stereotype.Component;
@Component
class Multiplication implements Operation {
@Override
public int apply(int lhs, int rhs) {
return lhs * rhs;
}
@Override
public boolean handles(char op) {
return '*' == op;
}
}
```

这些就是我们制作一个能够进行加法和乘法运算，并且仍然具有可扩展机制的计算器所需的所有组件。

最后，让我们创建一个配置并使用 `Calculator` 的应用。要创建 `Calculator` 的实例，需要一个 `@Bean` 方法。此方法可以添加到使用 `@SpringBootApplication` 或常规 `@Configuration` 注解的类中。

```
package com.apress.springbootrecipes.calculator;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.Bean;
import java.util.Collection;
@SpringBootApplication
public class CalculatorApplication {
public static void main(String[] args) {
var ctx = SpringApplication.run(CalculatorApplication.class, args);
var calculator = ctx.getBean(Calculator.class);
calculator.calculate(137, 21, '+');
calculator.calculate(137, 21, '*');
calculator.calculate(137, 21, '-');
}
@Bean
public Calculator calculator(Collection operations) {
return new Calculator(operations);
}
}
```

`calculator` 工厂方法接收一个 `List<Operation>`，我们用它来构造 `Calculator`。当在 `@Bean` 注解的方法中使用参数时，这些参数会被自动解析；并且当注入一个集合时，Spring 会自动检测所需 Bean 的所有实例，并使用它们来调用 `calculator` 工厂方法。

在 `main` 方法中，我们获取 `Calculator` 并使用不同的数字和操作调用其 `calculate` 方法。前两个操作会正常向控制台输出一些结果；最后一个操作会抛出异常，因为没有合适的操作来执行减法。

尽管你为 `Calculator` 创建了一个工厂方法，但这实际上并非必需。当使用 `@Component` 注解 `Calculator` 时，Spring 会自动检测到它。该类中的唯一构造函数将被用于构造它。如果有多个构造函数，请使用 `@Autowired` 注解要使用的那一个。

这段代码的另一个不太好的地方是手动获取 Bean，这可以被视为一种不良实践；通常你应该使用依赖注入。Spring Boot 有一个 `ApplicationRunner` 接口，可用于在应用启动后运行一些代码。当 Spring Boot 检测到类型为 `ApplicationRunner` 的 Bean 时，它会在应用启动后立即调用其 `run` 方法。

```
package com.apress.springbootrecipes.calculator;
import org.springframework.boot.ApplicationRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.Bean;
@SpringBootApplication
public class CalculatorApplication {
public static void main(String[] args) {
SpringApplication.run(CalculatorApplication.class, args);
}
@Bean
public ApplicationRunner calculationRunner(Calculator calculator) {
return args -> {
calculator.calculate(137, 21, '+');
calculator.calculate(137, 21, '*');
calculator.calculate(137, 21, '-');
};
}
}
```

创建 `Calculator` 的方法已被替换为 `ApplicationRunner`。这个 `ApplicationRunner` 接收自动配置好的 `Calculator`，并对其执行一些操作。运行此类时，输出应与之前相同。主要区别和优势在于，不再需要手动从 `ApplicationContext` 获取 Bean，因为 Spring 会负责获取正确的 Bean。

## 2.2 外部化属性

### 问题

你希望使用属性来为不同环境或执行场景配置你的应用。

### 解决方案

默认情况下，Spring Boot 支持从多个位置获取属性。默认情况下，它会加载名为 `application.properties` 的文件，并使用环境变量和 Java 系统属性。当从命令行运行时，它还会考虑命令行参数。根据应用类型和可用功能（例如 JNDI），还会考虑更多位置。^(⁷) 对于我们的应用，将按给定顺序考虑以下资源。

1.  命令行参数
2.  打包应用外部的 `application.properties`
3.  打包在应用内部的 `application.properties`

除此之外，对于选项 2 和 3，你还可以根据激活的配置文件加载特定于配置文件的属性。要激活的配置文件可以通过 `spring.profiles.active` 属性传递。特定于配置文件的 `application-{profile}.properties` 优先级高于非特定于配置文件的属性。每个文件都会被加载，并且你可以通过这种方式覆盖属性，这使得列表变得更长一些。

1.  命令行参数
2.  打包应用外部的 `application-{profile}.properties`
3.  打包应用外部的 `application.properties`
4.  打包在应用内部的 `application-{profile}.properties`
5.  打包在应用内部的 `application.properties`



### 工作原理

为配方 2.1 创建的 `Calculator` 本身相当灵活；然而，`CalculatorApplication` 在其执行的计算中使用了硬编码的值。现在，当我们想要计算不同的内容时，就需要修改代码、重新编译并运行新编译的代码。我们希望为此使用属性，以便在需要时能够更改它们。

首先修改应用程序，使其使用属性中的值而不是硬编码的值。为此，将 `ApplicationRunner` 的 `@Bean` 方法改为接受三个额外的参数，并且这些参数将使用 `@Value` 进行注解。

```
package com.apress.springbootrecipes.calculator;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.boot.ApplicationRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.Bean;
@SpringBootApplication
public class CalculatorApplication {
public static void main(String[] args) {
SpringApplication.run(CalculatorApplication.class, args);
}
@Bean
public ApplicationRunner calculationRunner(Calculator calculator,
@Value("${lhs}") int lhs,
@Value("${rhs}") int rhs,
@Value("${op}") char op) {
return args -> calculator.calculate(lhs, rhs, op);
}
}
```

`@Value` 将指示 Spring 查找该属性并使用其值。例如，如果我们使用 `@Value("${lhs}")`，Spring 会尝试检测名为 `lhs` 的属性并使用其值。你也可以通过添加分号来指定默认值。使用 `@Value("${lhs:12}")` 时，如果找不到值，它将使用 `12`。如果没有指定默认值，则会因缺少属性而抛出 `IllegalArgumentException`。如果我们现在启动应用程序，将会抛出一个异常，说明找不到 `lhs` 属性。

在 `src/main/resources` 中添加一个 `application.properties` 文件，并在其中为 `lhs`、`rhs` 和 `op` 设置值。

```
lhs=7
rhs=6
op=*
```

Spring Boot 会在启动时加载 `application.properties`，这样这些属性就可用。现在运行应用程序时，它应该再次根据 `application.properties` 中给定的值生成输出。

虽然你现在已经将属性外部化到了 `application.properties` 文件中，但这些属性仍然被打包在应用程序内部，这意味着你仍然需要更改它们才能执行不同的计算。这意味着对于每种属性组合，你都需要进行一次全新的构建。想象一下，在一个真实的生产系统中，仅仅因为配置需要更改就要创建新的构建产物。Spring Boot 可以通过多种方式帮助你解决这个问题。

#### 使用外部 **application.properties** 进行覆盖

首先构建构建产物，并使用 `java -jar recipe_2_2-1.0.0.jar` 启动应用程序。它仍然会运行并使用提供的、打包好的 `application.properties`。现在，在与构建产物相同的位置添加一个 `application.properties` 文件，并为不同的属性设置值。

```
lhs=26
rhs=952
op=*
```

再次启动应用程序时，它将使用这个 `application.properties` 中的值。

#### 使用 Profile 覆盖属性

Spring Boot 可以使用激活的 profile 来加载额外的配置文件，这些文件可以完全替换或覆盖通用配置的某些部分。让我们在 `src/main/resources` 中添加一个 `application-add.properties` 文件，其中包含 `op` 的不同值。

```
op=+
```

现在构建构建产物（一个 JAR 文件），并从命令行使用 `java -jar recipe_2_2-1.0.0.jar --spring.profiles.active=add` 启动它。它将启动并使用 `application.properties` 和 `application-add.properties` 中的属性来配置应用程序。注意，执行的是加法而不是乘法，这表明 `application-add.properties` 的优先级高于通用的 `application.properties`。

### 提示

当使用外部的 `application.properties` 和 `application-{profile}.properties` 时，这种方法同样有效。

#### 使用命令行参数覆盖属性

最后一个选项是使用命令行参数来覆盖属性；在上一节中，你已经使用了命令行参数 `--spring.profile.active=add` 来指定激活的 profile。你也可以通过这种方式指定 `lhs` 和其他参数。使用 `java -jar recipe_2_2-1.0.0.jar --lhs=12 --rhs=15 --op=+` 来运行应用程序，你会看到它根据通过命令行传入的参数进行计算。来自命令行的参数总是会覆盖所有其他配置。

#### 从不同的配置文件加载属性

如果你使用的文件不是 `application.properties`，或者你有一些带有嵌入式文件需要加载的组件，你可以在 `@SpringBootApplication` 注解的类上使用额外的 `@PropertySource` 注解来加载该额外文件。

```
@PropertySource("classpath:your-external.properties")
@SpringBootApplication
public class MyApplication { ... }
```

`@PropertySource` 注解允许你添加额外的属性文件，以便在启动时加载。除了使用 `@PropertySource`，你还可以指示 Spring Boot 使用表 2-1 中的命令行参数来加载额外的属性文件。

表 2-1

配置参数

| 参数 | 描述 |
| --- | --- |
| `spring.config.name` | 要加载的文件名的逗号分隔字符串，默认为 `application` |
| `spring.config.location` | 要考虑从中加载属性文件的资源位置（或文件）的逗号分隔字符串，默认为 `classpath:/,classpath:/config/,file:./,file:./config/` |
| `spring.config.additional-location` | 要考虑从中加载属性文件的额外资源位置（或文件）的逗号分隔字符串，默认为空 |

### 警告

当使用 `spring.config.location` 或 `spring.config.additional-location` 并指定文件时，该文件将按原样使用，并且不会加载特定于 profile 的文件。当指定目录时，则会加载特定于 profile 的文件。

使用 `--spring.config.name=your-external` 来加载 `your-external.properties` 就足够了；但是，这会破坏 `application.properties` 的加载。更好的做法是使用 `--spring.config.name=application,your-external`；现在，所有位置都会搜索 `application.properties` 和 `your-external.properties`，并且特定于 profile 的版本也会被考虑在内。

## 2.3 测试

### 问题

你想为 Spring Boot 应用程序的某个组件或部分编写测试。

### 解决方案

Spring Boot 扩展了 Spring 测试框架的功能范围。它增加了对 Bean 进行模拟和监视的支持，并为 Web 测试提供了自动配置。然而，它也引入了对应用程序进行切片测试的简便方法，即仅启动所需的部分（例如，通过使用 `@WebMvCTest` 或 `@JdbcTest`）。

### 工作原理

Spring Boot 也将自动配置扩展到了测试框架的各个部分。它还与 Mockito^(⁸) 集成，以便于对 Bean 进行模拟（或监视）。它还提供了基于 Web 的测试的自动配置，可以使用 Spring MockMvc 测试框架或基于 WebDriver 的测试。



#### 编写单元测试

首先，让我们为计算器的一个组件编写一个简单的单元测试——`MultiplicationTest`，它将测试`Multiplication`类。

```
public class MultiplicationTest {
private final Multiplication addition = new Multiplication();
@Test
public void shouldMatchOperation() {
assertThat(addition.handles('*')).isTrue();
assertThat(addition.handles('/')).isFalse();
}
@Test
public void shouldCorrectlyApplyFormula() {
assertThat(addition.apply(2, 2)).isEqualTo(4);
assertThat(addition.apply(12, 10)).isEqualTo(120);
}
}
```

这是一个基础的单元测试。我们实例化了`Multiplication`，调用其方法并验证结果。第一个测试将验证它是否确实响应`*`运算符，而非其他运算符。第二个测试将验证实际的乘法逻辑。要使一个方法成为测试方法（针对 JUnit 4），你需要使用`@Test`对其进行注解。

#### 在单元测试中模拟依赖

有时一个类需要依赖；然而，你希望测试仅针对单个组件（在编写单元测试时）。Spring Boot 自动引入了 Mockito 框架，这对于模拟类并记录其行为非常方便。为`Calculator`编写测试需要额外的组件，因为它将实际计算委托给了可用的`Operation`类。为了测试`Calculator`的正确行为，我们需要创建一个`Operation`的模拟对象，并将其注入到`Calculator`中。

```
public class CalculatorTest {
private Calculator calculator;
private Operation mockOperation;
@Before
public void setup() {
mockOperation = Mockito.mock(Operation.class);
calculator = new Calculator(Collections.singletonList(mockOperation));
}
@Test(expected = IllegalArgumentException.class)
public void throwExceptionWhenNoSuitableOperationFound() {
when(mockOperation.handles(anyChar())).thenReturn(false);
calculator.calculate(2, 2, '*');
}
@Test
public void shouldCallApplyMethodWhenSuitableOperationFound() {
when(mockOperation.handles(anyChar())).thenReturn(true);
when(mockOperation.apply(2, 2)).thenReturn(4);
calculator.calculate(2, 2, '*');
verify(mockOperation, times(1)).apply(2,2);
}
}
```

在`@Before`方法中，我们通过调用`Mockito.mock`来模拟`Operation`，并使用模拟的操作构造`Calculator`。该模拟对象在测试方法中被用于定义特定行为。在第一个测试方法中，我们想测试找不到合适操作的情况，因此我们指示模拟对象在调用`handles`方法时返回`false`。该测试预期会抛出一个异常；如果异常发生，测试将通过。第二个测试用于检查是否遵循了正确的流程；我们想测试正确的行为。模拟对象被指示为`handles`方法返回`true`，并为`apply`方法返回一个值。

#### 使用 Spring Boot 进行集成测试

Spring Boot 提供了多个注解来辅助测试。第一个是`@SpringBootTest`，它会使测试成为一个 Spring Boot 驱动的测试。这意味着测试上下文框架将搜索带有`@SpringBootApplication`注解的类（如果没有传递特定配置），并使用它来实际启动应用程序。

```
@RunWith(SpringRunner.class)
@SpringBootTest(classes = CalculatorApplication.class)
public class CalculatorApplicationTests {
@Autowired
private Calculator calculator;
@Test(expected = IllegalArgumentException.class)
public void doingDivisionShouldFail() {
calculator.calculate(12,13, '/');
}
}
```

上述测试将启动`CalculatorApplication`并注入完全配置好的`Calculator`。然后我们可以编写一个测试，在本例中是一个计算器无法处理的情况，并为其编写预期结果。

进行计算时，输出会打印到控制台；使用`OutputCapture` JUnit 规则，我们还可以编写一个成功测试并测试输出的内容。

```
@RunWith(SpringRunner.class)
@SpringBootTest(classes = CalculatorApplication.class)
public class CalculatorApplicationTests {
@Rule
public OutputCapture capture = new OutputCapture();
@Autowired
private Calculator calculator;
@Test
public void doingMultiplicationShouldSucceed() {
calculator.calculate(12,13, '*');
capture.expect(Matchers.containsString("12 * 13 = 156"));
}
@Test(expected = IllegalArgumentException.class)
public void doingDivisionShouldFail() {
calculator.calculate(12,13, '/');
}
}
```

`@Rule`将配置给定的 JUnit 规则，在本例中是`OutputCapture`规则，它随 Spring Boot 一起提供，并拦截`System.out`和`System.err`，以便可以对在这些流上生成的输出编写断言。在这个例子中，我们执行乘法运算，输出应反映这一点。

#### 使用 Spring Boot 和模拟对象进行集成测试

Spring Boot 使得在应用程序上下文中用模拟对象替换 bean 变得容易。为此，Spring Boot 引入了`@MockBean`注解。

```
@MockBean
private Calculator calculator
```

这将用模拟实例替换整个计算器；要使用它，你需要使用常规的 Mockito 方式定义行为。当存在多个特定类型的 bean 时，你需要指定要替换的 bean 的`name`。

```
@MockBean(name ="addition")
private Operation mockOperation;
```

这将用模拟实例替换常规的`Addition` bean，然后我们可以用它来注册模拟行为。如果找不到具有该名称的 bean，则模拟 bean 将作为该 bean 的新实例注册。

```
@MockBean(name ="division")
private Operation mockOperation;
```

这不会替换现有的 bean，而是向应用程序上下文添加一个 bean，结果`Calculator`将有一个额外的操作添加到它可以处理的`operations`中。你可以使用 Spring 测试框架中的`ReflectionTestUtils`辅助类来测试这一点。

```
@Test
public void calculatorShouldHave3Operations() {
Object operations =
ReflectionTestUtils.getField(calculator, "operations");
assertThat((Collection) operations).hasSize(3);
}
```

这将通过反射获取`operations`字段，并断言集合大小为 3。当移除`@MockBean`注解（或将名称更改为`addition`）时，此测试将失败，因为现在只注册了两个操作。

使用模拟对象很简单：

```
@Test
public void mockDivision() {
when(mockOperation.handles('/')).thenReturn(true);
when(mockOperation.apply(14, 7)).thenReturn(2);
calculator.calculate(14,7, '/');
capture.expect(Matchers.containsString("14 / 7 = 2"));
}
```

这里我们指示 Mockito 在测试`/`时返回`true`，并在调用`apply`方法时返回一个值。接下来，调用该方法，并断言输出符合此测试的预期。

## 2.4 配置日志记录

### 问题

您想要为某些日志记录器配置日志级别。

### 解决方案

使用 Spring Boot，您可以配置日志记录框架和配置。



### 工作原理

Spring Boot 为支持的日志提供程序（Logback、^(⁹) Log4j 2、^(¹⁰) 和 Java Util Logging）提供了默认配置。除了默认配置外，它还支持通过常规的 `application.properties` 配置日志级别，以及指定日志格式和可选的日志文件写入位置。

Spring Boot 使用 SLF4J 作为日志记录 API，在编写组件时，应使用这些接口来记录日志。这样，你就可以选择使用哪个日志框架。

#### 配置日志

使用日志框架时，常见的操作之一是启用或禁用框架某些部分的日志记录。使用 Spring Boot，你可以通过在 `application.properties` 中添加一些配置行来实现。这些行需要以 `logging.level` 为前缀，后跟日志记录器的名称，最后是所需的日志级别。

```
logging.level.org.springframework.web=DEBUG
```

上述配置将为 `org.springframework.web` 日志记录器（通常包括该包及其子包中的所有类）启用 DEBUG 级别的日志记录。要设置根日志记录器的级别，请使用 `logging.level.root=<level>`。这将设置日志记录的默认级别。

#### 日志输出到文件

默认情况下，Spring Boot 仅将日志输出到控制台。如果你还想将日志写入文件，则需要指定 `logging.file` 或 `logging.path`。前者指定文件名；后者指定路径。默认文件名为 `spring.log`，默认目录为 Java 临时目录。

```
logging.file=application.log
logging.path=/var/log
```

使用此配置，名为 `application.log` 的日志文件将被写入 `/var/log` 目录。

将日志写入文件时，你可能希望防止日志文件淹没系统。你可以使用 `logging.file.max-history`（默认值为 `0`，表示无限制）指定保留的文件数量，并使用 `logging.file.max-size` 指定文件大小（默认值为 10MB）。

#### 使用你偏好的日志提供程序

Spring Boot 默认使用 Logback 作为日志提供程序。不过，它也支持 Java Util Logging 和 Log4j 2。要使用其他日志框架，你首先需要排除默认框架，然后引入你自己的框架。Spring Boot 提供了 `spring-boot-starter-log4j2` 来包含 Log4j 2 的所有必要依赖。要排除默认的 Logback 日志记录，你需要在 `spring-boot-starter` 依赖中添加一个排除规则；这是引入日志记录的主要依赖。

```
org.springframework.boot
spring-boot-starter

org.springframework.boot
spring-boot-starter-logging

org.springframework.boot
spring-boot-starter-log4j2

```

### 注意

Spring Boot 2 不支持较旧的 Log4j 框架；它支持其继任者 Log4j 2！

## 2.5 重用现有配置

### 问题

你有一个现有的非 Spring Boot 应用程序或模块，并且希望将其配置与 Spring Boot 一起重用。

### 解决方案

要导入现有配置，请在带有 `@Configuration` 或 `@SpringBootApplication` 注解的类上添加 `@Import` 或 `@ImportResource` 注解，以导入配置。

### 工作原理

在你的主应用程序类（带有 `@SpringBootApplication` 注解的类）上，放置 `@Import` 或 `@ImportResource` 注解，让 Spring 加载额外的文件。

#### 重用现有的 XML 配置

找到带有 `@SpringBootApplication` 注解的类，并向其添加 `@ImportResource` 注解。

```
@SpringBootApplication
@ImportResource("classpath:application-context.xml")
public class Application { ... }
```

由于使用了 `classpath:` 前缀，此配置将从类路径加载 `application-context.xml` 文件。如果文件位于文件系统中的某个位置，你可以使用 `file:` 前缀，例如 `file:/var/conf/application-context.xml`。

在引导应用程序时，Spring Boot 还将从上述 XML 文件中加载额外的配置。

#### 重用现有的 Java 配置

找到带有 `@SpringBootApplication` 注解的类，并向其添加 `@Import` 注解。

```
@SpringBootApplication
@Import(ExistingConfiguration.class)
public class Application { ... }
```

`@Import` 将负责将提到的类添加到配置中。如果你希望包含组件扫描未覆盖的内容，或者已禁用 `@Configuration` 类的自动检测，则可能需要这样做。

脚注 1   2   3   4   5   6

