# 6. 装饰器与拦截器

装饰器模式是一种相当常用的设计模式。它允许一个对象被另一个对象包装，从而使包装对象能够扩展或完全覆盖其功能。

Java 中使用装饰器模式的两个很好的例子是 `InputStream` 和 `OutputStream` 类。包装类，如 `BufferedOutputStream` 和 `FilteredOutputStream`，为它们所包装的 `OutputStream` 添加了功能。

CDI 通过装饰器和拦截器来支持装饰器模式。装饰器与被装饰的 Bean 紧密耦合，可以应用于开发者无法控制的 Bean。另一方面，拦截器与被装饰的 Bean 松散耦合，并且只能应用于被装饰 Bean 本身的定义中。由于与被装饰 Bean 的紧密联系，装饰器主要适用于覆盖你无法控制的 Bean 上的特定逻辑，而拦截器则更适用于实现更通用的服务。



## 装饰器

装饰器 Bean 允许扩展甚至完全覆盖一个或多个其他 Bean 的逻辑。它们可以更改提供给 Bean 方法的任何参数，或更改方法的返回类型。装饰器 Bean 继承自它想要装饰的类型之一。这意味着在实现装饰器时，必须在编译时知道要装饰的方法。无法使用装饰器 Bean 动态地覆盖方法。

当一个 Bean 被装饰时，CDI 会注入一个代理而不是直接注入该 Bean。这个代理会将方法调用委托给装饰器，而不是 Bean 本身。这意味着，与作用域的情况非常相似，装饰器只能应用于可代理的类型。

要实现一个装饰器，只需创建一个新的 Bean 类，该类继承自包含你想要装饰的方法的类型。假设你有一个 `FooBean`，它从 `FooInterface` 继承了一个你想要装饰的 `foo()` 方法；你可以让你的装饰器 Bean 直接继承 `FooBean`，或者改为实现 `FooInterface`。装饰器 Bean 只需要实现那些需要被装饰的 Bean，并且装饰器 Bean 类甚至可以是抽象的。当使用抽象的装饰器 Bean 类时，CDI 容器将提供一个装饰器 Bean 类的实现，该实现为装饰器 Bean 类自身未实现的所有方法提供实现。

接下来，必须将一个委托注入到装饰器 Bean 中。该委托必须使用 `@Inject @Delegate` 注解，并且可以像任何其他注入的依赖项一样，注入到字段、初始化器参数或构造函数参数中。委托可用于调用下一个装饰器，或者，如果没有其他装饰器了，则调用实际 Bean 上的方法。

委托还有另一个用途：委托的类型以及其上定义的任何限定符将决定装饰器 Bean 将装饰哪些类型的 Bean。装饰器将自动应用于任何与注入的委托类型相同或为其子类型的 Bean。需要注意的是，委托的类型不必与装饰器 Bean 的类型匹配。委托可以实现委托类型继承自的任何类型，并且该类型定义了装饰器 Bean 将覆盖的方法。除此之外，还可以将限定符应用于委托。这将把装饰器 Bean 的应用限制为仅那些既匹配委托类型，又具有与注入的委托相同的限定符注解的 Bean。

假设你有一个 `GreetingBean`，它有两个独立的方法 `getGreetingMessage()` 和 `getGoodByeMessage()`，两者都返回一个字符串。你想要创建一个 `GreetingDecoratorBean`，它将当前时间附加到 `GreetingBean` 创建的问候语之后。`GreetingBean` 本身相当简单，如清单 6-1 所示。

```
public class GreetingBean {
public String getGreetingMessage(String name) {
return "Hello, " + name + "!";
}
public String getGoodByeMessage(String name) {
return "Good bye, " + name + "!";
}
}
清单 6-1
GreetingBean
```

接下来，通过继承 `GreetingBean` 类来创建你的装饰器 Bean。你需要使用 `@Decorator` 注解装饰器 Bean 类，并且需要注入 `@Delegate` 实例，以便你可以调用原始 Bean。然后，你只需覆盖 `getGreetingMessage()` 方法，并在委托上调用相同的方法以获取原始问候语。接着，在返回此字符串之前，将时间戳附加到它后面。参见清单 6-2。

```
@Decorator
public class GreetingDecoratorBean extends GreetingBean {
private static final DateTimeFormatter FORMATTER =       DateTimeFormatter.ofLocalizedTime(FormatStyle.FULL);
@Inject
@Delegate
private GreetingBean greetingBean;
@Override
public String getHelloMessage(String name) {
return greetingBean.getHelloMessage(name) + " The time is now "           + FORMATTER.format(LocalTime.now());
}
}
清单 6-2
GreetingDecoratorBean
```

现在假设你需要支持多种语言。你从 `GreetingBean` 中提取出一个 `GreetingInterface`，并且会有多个不同的 Bean 来为每种语言提供问候语。为了区分这些 Bean，你添加了一个 `@Language` 限定符注解，它以语言作为值。参见清单 6-3。

```
@Qualifier
@Retention(RetentionPolicy.RUNTIME)
@Target(value = {FIELD, PARAMETER, METHOD, TYPE})
public @interface Language {
String value();
}
清单 6-3
限定符注解
```

现在你可以让你的 `GreetingDecoratorBean` 直接实现 `GreetingInterface`。因为你只关心 `getGreetingMessage()` 方法，所以你将只实现该方法。但是，由于你总是需要实现接口定义的所有方法，你现在必须将装饰器 Bean 类声明为抽象类，因为你不会实现 `getGoodByMessage()` 方法。该装饰器也只支持英语，因此你需要在委托字段上包含限定符注解，以确保你的装饰器不会为其他语言调用。参见清单 6-4。

```
@Decorator
@Priority(Interceptor.Priority.APPLICATION + 1)
public abstract class GreetingDecoratorBean implements GreetingInterface {
private static final DateTimeFormatter FORMATTER =       DateTimeFormatter.ofLocalizedTime(FormatStyle.FULL);
@Inject
@Delegate
@Language("English")
private GreetingInterface greetingBean;
@Override
public String getHelloMessage(String name) {
return greetingBean.getHelloMessage(name) + " The time is now "           + FORMATTER.format(LocalTime.now());
}
}
清单 6-4
注入带有限定符的委托
```

如果将来你想扩展你的装饰器以同时装饰另一种语言的 Bean，你可以简单地在委托字段上为你想支持的语言声明另一个 `@Language` 注解。该字段声明将如清单 6-5 所示。

```
@Inject
@Delegate
@Language("English")
@Language("Dutch")
private GreetingInterface greetingBean;
清单 6-5
为多个带限定符的 Bean 注入委托
```

你也可以将你的装饰器应用于任何匹配的 Bean。你可以通过应用 `@Any` 注解而不是显式列出任何限定符来实现这一点。参见清单 6-6。

```
@Inject
@Delegate
@Any
private GreetingInterface greetingBean;
清单 6-6
将装饰器应用于多种类型的 Bean
```

一个重要的要求是，装饰器 Bean 除了依赖作用域之外，不得声明任何显式的作用域。对于每个被装饰的 Bean 实例，都会创建一个新的装饰器 Bean 实例。由于这个装饰器 Bean 是依赖作用域的，它将与其装饰的 Bean 同时被销毁。因此，对已装饰 Bean 的多次后续调用将始终由同一个装饰器 Bean 实例处理。



一旦装饰器类编写完成，剩下的就是启用该装饰器，使其在运行时实际生效。要启用装饰器，需要为整个应用程序或单个 Bean 存档激活该装饰器。当为整个应用程序激活装饰器时，它将自动应用于所有匹配的 Bean，无论该 Bean 定义在哪个 Bean 存档中。相反，当为单个 Bean 存档激活装饰器时，它只会应用于该 Bean 存档中定义的 Bean。这在某些业务逻辑可能需要被覆盖，但仅针对单个 Bean 存档中的匹配 Bean 时非常有用。

可以通过在装饰器类上应用 `@Priority` 注解来为整个应用程序激活装饰器。当存在多个活动装饰器时，`@Priority` 注解将决定装饰器的执行顺序。其工作方式与事件观察者相同。`@Priority` 注解中数值较小的装饰器会先于数值较大的装饰器执行。由于装饰器主要用于扩展或覆盖现有 Bean 中声明的业务逻辑，大多数装饰器的优先级应介于 `Interceptor.Priority.APPLICATION` 和 `Interceptor.Priority.LIBRARY_AFTER` 之间。要将装饰器声明为最先执行的装饰器（之一），优先级注解应定义为 `@Priority(APPLICATION)`。

你可以使用本章前面提到的 `GreetingDecoratorBean` 并在应用程序范围内激活它。假设应用程序中的 `GreetingDecoratorBean` 如果有多个装饰器，它应该作为最先执行的装饰器之一，但不一定是第一个。为此，你可以使用优先级 `APPLICATION + 5`。这对装饰器来说是一个相当高的优先级，但仍然为其他装饰器留出了空间，使其可以在你的新装饰器之前声明并执行。然后，你将带有新优先级的 `@Priority` 注解应用到你的类上，这样你的装饰器类声明看起来就像下面这样。

```
@Decorator
@Priority(Interceptor.Priority.APPLICATION + 5)
public class GreetingDecoratorBean extends GreetingBean {
```

可以使用该存档的 `beans.xml` 文件仅为单个 Bean 存档激活装饰器。要在 `beans.xml` 文件中声明装饰器，必须添加一个名为 `decorators` 的部分。每个装饰器必须使用 `class` 标签单独声明。此标签的内容必须是装饰器的完全限定名。装饰器声明的顺序决定了它们的执行顺序。先列出的装饰器将在后声明的装饰器之前执行。在应用程序级别激活的装饰器将始终在存档级别启用的第一个装饰器之前执行。这意味着，如果装饰器需要在应用程序级别激活的其他装饰器之间执行，则该装饰器本身也必须在应用程序级别激活，而不是在 Bean 存档级别。

### 注意

如果装饰器同时在应用程序级别和 Bean 存档级别被激活，则应用程序级别的激活将优先。在这种情况下，装饰器的执行时间将完全由 `@Priority` 注解决定。

回到你的 `GreetingDecoratorBean`，你可以通过在该存档的 `beans.xml` 文件中声明它，仅为其单个 Bean 存档激活它。你只需更新你的 `beans.xml` 存档，使其引用装饰器的完全限定类名。如果你希望任何装饰器在你的装饰器之前运行，你需要在 `GreetingDecoratorBean` 之前声明它们。同样，任何需要在其之后运行的装饰器都应在 `GreetingDecoratorBean` 之后声明。

```

com.foo.bar.GreetingDecoratorBean

```

## 拦截器

拦截器是在 CDI 中使用装饰器模式的第二种方式。拦截器也可以被视为 CDI 中面向切面编程（AOP）的一种实现。你可能之前已经接触过 AOP。AOP 是一种通过将横切关注点与业务逻辑分离来使代码更加模块化的方法。一旦这些横切关注点被分离，它们就可以应用于任何现有代码。事务管理就是一个很好的例子。许多业务逻辑依赖于在执行该逻辑时事务处于活动状态，但事务的启动、中止或完成可以被视为横切关注点。在 AOP 中，事务逻辑将以一种允许在需要时应用于任何业务逻辑的方式与业务逻辑分离。Spring 和 Java EE 都定义了一个 `@Transactional` 注解，正是用于此目的。事实上，在 Java EE 中，`@Transactional` 是通过使用拦截器来实现的。如果你想了解更多关于 AOP 的信息或如何使用它的示例，强烈推荐 Ivar Jacobson 所著的《*Aspect-Oriented Software Development with Use Cases*》一书。

拦截器与装饰器有许多相似之处，但也存在一些重要差异。主要区别在于，拦截器并非由 CDI 规范本身定义。相反，拦截器由单独的规范定义，并且至少在理论上可以在不使用 CDI 的情况下使用。然而，拦截器在 CDI 生态系统中占有重要地位。它们可用于启用新特性和服务，并且通常功能强大，这就是我们在此介绍它们的原因。拦截器更具动态性的特性使其适用于缓存方法结果、日志记录以及动态启动和停止作用域等场景。事实上，正如我们在作用域章节中所述，存在一种作用域，可以在调用方法时自动启动和停止请求作用域。

与装饰器一样，拦截器也是依赖作用域的。拦截器也是以链式方式调用的。当存在多个拦截器时，每个拦截器会调用下一个拦截器，而链中的最后一个拦截器将调用被拦截的方法。拦截器和装饰器之间的一个重要区别在于，拦截器允许对 Bean 进行动态装饰。在实现拦截器时，你不需要知道拦截器将应用于哪些类型的 Bean。相反，拦截器可以应用于大量不同的 Bean 类型，其中一些类型在编写拦截器时可能甚至尚未编写。这使得拦截器能够用于比装饰器更广泛的用例，因为装饰器主要用于扩展或覆盖业务逻辑。拦截器可用于为 CDI Bean 提供额外的服务。例如，可以创建一个拦截器，当被拦截的方法被调用时启动一个 CDI 作用域，并在方法调用完成后销毁该作用域。另一个区别在于，装饰器自身决定它将应用于哪些 Bean，而对于拦截器，情况则相反。是由 Bean 类来决定哪些拦截器将被调用。

拦截器可以拦截常规方法调用、构造函数调用或定时器超时。后者主要在对企业级 JavaBean（EJB）应用拦截器时才有意义。在讨论 CDI 时，EJB 并不那么相关，因此本章不会涉及这种情况。构造函数拦截在你想在 Bean 的构造函数被调用之前或之后执行某些操作，或者甚至想绕过构造函数调用以返回 Bean 的缓存实例时最为有用。目前，我们只关注常规方法拦截，稍后我们将更深入地探讨构造函数拦截。



拦截器中的实际工作由拦截器方法完成。要实现一个拦截器，你只需定义一个包含拦截器方法的类。与装饰器不同，你无需实现任何接口或继承任何现有类。拦截器方法并非定义在另一个类或接口中，而是遵循一种约定。因此，拦截器方法的名称无关紧要，你可以随意命名，只要能清晰描述其功能即可。在声明拦截器方法时，有几项必须遵守的规则。首先，拦截器方法的返回类型必须是 `Object`。这是因为，由于拦截器是动态拦截方法的，你在运行时无法知道被拦截方法的返回类型。被拦截方法的返回类型可以是任何原始类型、对象类型，甚至可以是 `void`。所有这些类型的共同点是它们都可以用对象来表示。所有原始类型都有对应的对象包装类型，而 `void` 可以用 `null` 表示。使用 `Object` 作为返回类型，可以让你拦截任何方法，无论其返回类型是什么。

出于同样的原因，拦截器方法还必须声明抛出 `Exception`。拦截器会（间接地）调用被拦截的方法，而你并不知道被拦截的方法会抛出哪些受检异常（如果有的话）。默认情况下，你应该向上传播被拦截方法抛出的任何异常。

拦截器方法应接受一个 `javax.interceptor.InvocationContext` 类型的参数。`InvocationContext` 参数有一个 `proceed()` 方法，调用该方法将触发调用链中的下一个拦截器。如果当前拦截器之后没有其他拦截器，调用 `proceed()` 将直接调用被拦截的方法本身。`proceed()` 返回的值将是调用链中下一个拦截器或被拦截方法本身的返回值。`InvocationContext` 上还有其他几个有用的方法，我们稍后会深入探讨。

你需要指明你的拦截器是拦截构造方法还是普通方法。要声明你的拦截器方法拦截普通方法，你需要使用 `javax.interceptor.@AroundInvoke` 注解来标注你的拦截器方法。如果你要编写一个最简单的拦截器，它除了调用链中的下一个拦截器外什么都不做，那么它看起来会像清单 6-7 这样。

```
public class DoNothingInterceptor {
@AroundInvoke
public Object aroundInvoke(InvocationContext context) throws Exception {
return context.proceed();
}
}
清单 6-7
基础虚拟拦截器
```

`InvocationContext` 参数定义了许多有用的方法，可用于获取关于拦截器正在拦截的目标方法的更多信息。`InvocationContext` 类定义了以下方法：

*   `Object getTimer()`

*   `Object proceed()`

*   `Object[] getParameters()`

*   `void setParameters(Object[] params)`

*   `Method getMethod()`

*   `Constructor<?> getConstructor()`

*   `Object getTarget()`

*   `Map<String,Object> getContextData()`

`getTimer()` 方法仅用于 EJB 调用，我们在此不讨论。`proceed()` 方法你之前已经见过。当拦截器调用 `proceed()` 方法时，拦截器链中的下一个拦截器将被调用。如果当前拦截器之后没有其他拦截器，则会调用实际的 Bean 方法。调用 `proceed()` 的结果取决于拦截器链中的拦截器本身。在正常情况下，调用 `proceed()` 的结果将是方法的返回值。如果方法本身没有返回类型，则结果将为 `null`。

然而，链中的任何拦截器都可以决定通过返回不同的值来覆盖 Bean 方法的结果。只要拦截器返回的值与实际方法的返回类型匹配，这就是可行的。由于拦截器的返回类型是 `Object`，当值返回到拦截器链的顶部时，它会被转换为实际方法所需的正确类型。当最顶层拦截器返回的值与实际方法的返回类型不匹配时，会抛出 `ClassCastException`。需要注意的重要一点是，方法拦截器不一定要调用 `InvocationContext.proceed()`。相反，拦截器可以决定完全绕过底层方法并直接返回一个值。例如，当一个方法计算结果的耗时很长，但对于相同的输入总是返回相同的结果时，这可能会很有用。可以编写一个能够缓存任何方法输出的拦截器。如果结果已经存在于缓存中，拦截器就无需调用被拦截的方法来获取结果，而是可以直接返回缓存的结果。

我们以本章前面提到的 `GreetingBean` 为例。假设你想更改此 Bean 以及其他 Bean 返回字符串中的感叹号，但又不想修改方法本身。你可以使用拦截器来更改方法的结果。为此，你应该调用 `InvocationContext.proceed()` 来获取方法返回的实际值。然后，你可以检查该值是否为字符串，如果是，则将任何感叹号替换为句号。参见清单 6-8。

```
@AroundInvoke
public Object changeResponseString(InvocationContext context)   throws Exception {
Object result = context.proceed();
if (result instanceof String) {
return ((String) result).replace('!', '.');
}
// 当结果不是字符串时的回退处理
return result;
}
清单 6-8
在拦截器中覆盖方法结果
```

`getParameters()` 方法以数组形式返回传递给方法调用的参数。这允许拦截器检查当前调用的方法参数，并据此采取行动。例如，对于前面提到的缓存拦截器示例，可以使用参数作为缓存键来缓存实际方法的结果。数组中元素的顺序与参数在方法声明中定义的顺序相同。如果方法没有任何参数，调用 `getParameters()` 将返回一个大小为 0 的数组。

这样一个缓存拦截器的简单实现可以使用 `ConcurrentHashMap` 作为缓存。由于缓存键不应随时间被修改，因此通过从参数数组创建一个列表，然后从该列表创建一个不可变副本来使包含参数的缓存键不可变。参见清单 6-9。

```
private final ConcurrentHashMap, Object> cache =   new ConcurrentHashMap();
@AroundInvoke
public Object aroundInvoke(InvocationContext context) throws Exception {
// 创建参数的不可变副本用作缓存键。
List parameters = List.copyOf(Arrays.asList(context.getParameters()));
if (cache.contains(parameters)) {
return cache.get(parameters);
}
Object result = context.proceed();
cache.put(parameters, result);
return result;
}
清单 6-9
缓存拦截器
```



`setParameters()` 方法可用于更改传递给方法的实际参数。它接受一个对象数组作为参数。一旦被调用，这些参数将替代原始参数用于实际的方法调用。如果链中更靠后的拦截器调用 `InvocationContext.getParameters()` 而非原始参数，它们也会看到新的参数。与 `getParameters()` 类似，数组中的位置与参数在方法中声明的顺序一致。传递给 `setParameters()` 的数组必须与参数的数量完全匹配，并且每个值的类型也必须与实际参数的类型一致。就像从拦截器返回类型错误的结果一样，传递类型不正确的参数会导致抛出 `ClassCastException`。

在前面的示例中，你从一个方法获取结果，如果结果是字符串，则将结果中的感叹号替换为句号。你可以使用 `setParameters()` 方法对输入参数执行相同的操作。为此，你需要检查每个参数是否为字符串。如果是，则将感叹号替换为句号。然后将所有参数收集到一个新数组中，并将这个新数组传递给 `setParameters()` 方法。参见清单 6-10。

```
@AroundInvoke
public Object replaceCharsInParameters(InvocationContext context)   throws Exception {
Object[] parameters = context.getParameters();
if (parameters.length > 0) {
Object[] newParameters = Arrays.stream(parameters)
.map(parameter -> {
if (parameter instanceof String) {
return ((String) parameter).replace('!', '.');
}
return parameter;
}).toArray();
context.setParameters(newParameters);
}
return context.proceed();
}
清单 6-10
在拦截器中更新参数
```

`getMethod()` 方法返回实际被调用的方法。这允许拦截器通过反射检查实际被调用的方法。例如，拦截器可以使用它来获取方法的名称、返回类型、期望的参数数量和类型，以及它声明的任何异常。当拦截的对象不是方法时，`getMethod()` 将返回 `null`。`getMethod()` 返回的反射方法引用在许多情况下都很有用。例如，它可以用于记录当前被拦截的方法的信息，比如在一个测量方法调用耗时的拦截器中。

除了 `getMethod()`，`getTarget()` 方法将返回一个对实际被拦截的 bean 的引用。这将返回一个代理实例，该实例查找底层 bean 的实例，并将任何调用委托给该实例。当与 `getMethod()` 调用的结果结合使用时，可以对当前被拦截的方法执行另一次调用。此调用将从头重新启动整个拦截器链，这意味着当前拦截器也将被调用。这在拦截器需要强制对拦截器进行第二次调用的情况下可能很有用。

假设你有一个拦截器，它检查方法的第一个参数。如果该参数设置为 `true`，则将其设置为 `false`，然后重新启动整个拦截器链。为了防止无限循环，如果第一个参数已经设置为 `false`，拦截器只需调用 `InvocationContext.proceed()` 来继续链。你可以使用 `getMethod()`、`getTarget()` 和 `getParameters()` 方法来实现这一点。首先，你需要获取方法调用的参数并检查第一个参数的值。如果确实是 `true`，则创建一个包含新值的数组副本。然后，使用你获得的引用，以目标 bean（代理）和新的参数集作为值来调用该方法。这将重新启动整个拦截链，因此当前拦截器将再次被调用，但使用的是新值。

```
@AroundInvoke
public Object setFirstParameterToFalse(InvocationContext context)   throws Exception {
Object[] parameters = context.getParameters();
if (parameters.length > 0 && Objects.equals(parameters[0], true)) {
Object[] newParameters = Arrays.copyOf(parameters, parameters.length);
newParameters[0] = false;
Object target = context.getTarget();
Method method = context.getMethod();
return method.invoke(target, newParameters);
}
return context.proceed();
}
```

`getTarget()` 返回的代理所委托的 bean 实例取决于 bean 的作用域和当前活动的作用域。对于任何依赖作用域的 bean，目标代理将委托给与被拦截的相同的 bean 实例。对于有作用域的 bean，拦截器可以使用此方法将当前活动的作用域替换为另一个作用域，然后在新作用域的 bean 实例上重新启动整个拦截器链。

`getContextMetadata()` 方法返回一个包含上下文元数据的映射。拦截器可以读取或更新这些元数据。这允许拦截器传递信息，然后这些信息可供拦截器链中更靠后的拦截器使用。

要向上下文元数据添加内容，你只需获取上下文元数据映射并向其中放入一个键值对。如果链中后面的拦截器尝试从同一个键获取数据，则将返回第一个示例的值。链中更靠前的拦截器也可以通过调用 `InvocationContext.proceed()` 后检查值，来看到链中更靠后的拦截器放入上下文元数据中的值。

假设你想测量同一拦截器链中两个拦截器之间所花费的时间。例如，这对于测量链中多个拦截器的性能可能很有用。为此，你可以获取当前时间并将其添加到上下文元数据映射中。参见清单 6-11。

```
@AroundInvoke
public Object aroundInvoke(InvocationContext context) throws Exception {
Instant startTime = Instant.now();
context.getContextData().put("startTime", startTime);
return context.proceed();
}
清单 6-11
在上下文元数据中存储数据
```

在你的第二个拦截器中，你需要使用相同的键从映射中读取。请注意，该映射的值具有泛型类型 `Object`。因为中间的拦截器可能意外使用了相同的键，所以在盲目转换之前，最好检查从映射返回的值的类型。然而，一个好的方法是将拦截器的包名用作键的前缀。这将防止上下文元数据中键名的大多数冲突。在检查了上下文元数据中包含的值的类型后，你可以安全地转换并使用它。参见清单 6-12。

```
public Object aroundInvoke(InvocationContext context) throws Exception {
Object startTime = context.getContextData().get("startTime");
if (startTime instanceof Instant) {
Duration duration = Duration.between((Instant) startTime, Instant.now());
logger.info("执行耗时 " + duration.toMillis() + " 毫秒。");
}
return context.proceed();
}
清单 6-12
从上下文元数据中读取
```



### 应用拦截器

编写好拦截器后，需要将其应用到目标上。拦截器通常通过注解的方式应用于单个方法或整个类。当注解应用于类时，该类的每个公有方法在通过 Bean 代理调用时都会触发拦截器。这意味着 Bean 内部的任何自调用都不会被拦截。

通过注解应用拦截器有两种方式：可以使用通用注解，也可以使用自定义的专用拦截器注解。通用方法最为简单，只需应用 `javax.interceptor.Interceptors` 注解即可。该注解接受一个拦截器类数组作为值。拦截器链中拦截器类的执行顺序由声明顺序决定。

假设你有两个拦截器类 `FooInterceptor` 和 `BarInterceptor`，希望将其应用于你的类。你可以在类上定义 `@Interceptors` 注解，并按期望的链顺序列出这两个拦截器。最终的类定义如清单 6-13 所示。

```
@Interceptors({FooInterceptor.class, BarInterceptor.class})
public class MyBean {
// 实现方法写在这里
}
清单 6-13 使用 @Interceptors 应用拦截器
```

使用 `@Interceptors` 注解虽然相当直接，但存在一些缺点。例如，后续无法控制或覆盖这些拦截器。这也意味着被拦截的类需要知道实现拦截器的类，且无法在后期将拦截器实现切换为另一个不同的实现。

另一种方法是为拦截器定义自定义注解。这种方式有几个优点，因为它将被拦截的类与拦截器实现解耦。同时，它要求拦截器必须被激活，因此可以实现仅在特定条件下启用的拦截器。例如，考虑一个用于跟踪调试信息的拦截器。你可能不希望它在正常情况下处于激活状态，但又不想为了使用它而重新编译每个 Bean 来添加拦截器。

你可以通过实现一个拦截器绑定注解来定义自定义注解。该注解必须具有 `RUNTIME` 保留策略，并且应能同时应用于类型和方法。该注解还必须使用 `javax.interceptor.@InterceptorBinding` 注解进行标注。一个典型的拦截器绑定如清单 6-14 所示。

```
@InterceptorBinding
@Retention(RUNTIME)
@Target({TYPE, METHOD})
public @interface ExampleInterceptorBindingAnnotation {
}
清单 6-14 一个拦截器绑定注解
```

接下来，拦截器绑定注解也必须应用于拦截器本身，以便容器在运行时将绑定注解与你的拦截器实现关联起来。你还必须使用 `javax.interceptor.@Interceptor` 注解标注拦截器类。这最后一个注解将你的拦截器类标记为注解，并告知 CDI 你正在将你的类定义为绑定注解的实现，而不是试图使用该注解将拦截器应用于当前类。请注意，`@Interceptor` 注解仅在使用绑定注解方法时才需要出现；在其他所有情况下，你无需将其应用于拦截器。同一个拦截器绑定可以用于多个拦截器。当重用拦截器绑定时，每个被该绑定注解标注的拦截器都将被执行。这使得仅使用单个注解即可应用多个拦截器。然而，这可能会导致混淆，并且会使仅应用其中一个带有该注解的拦截器变得更加困难。因此，最好为每个独立的拦截器使用一个唯一的拦截器绑定。

让我们创建一个名为 `MyInterceptor` 的 Bean，并将其绑定到名为 `@MyInterceptorBinding` 的注解上。你可以通过简单地在拦截器本身上同时标注 `@Interceptor` 和 `@MyInterceptorBinding` 来将绑定与拦截器关联起来，这将使类定义如清单 6-15 所示。

```
@Interceptor
@MyInterceptorBinding
public class MyInterceptor {
// 拦截器方法写在这里
}
清单 6-15 将拦截器绑定链接到拦截器
```

现在，你可以通过使用 `@MyInterceptorBinding` 注解来将拦截器应用于任何 Bean 或方法。

### 拦截器激活

当你为拦截器定义了拦截器绑定注解后，该拦截器默认不会被使用。相反，拦截器必须首先被激活。与装饰器类似，拦截器既可以在单个 Bean 归档级别激活，也可以在整个应用程序范围内全局激活。这为拦截器的使用方式和时机提供了一定的灵活性。例如，可以让拦截器仅在开发期间激活，而在生产环境中停用，而无需从所有被拦截的类中移除注解。实现方式与装饰器的激活方式类似。

要全局激活拦截器，需要在拦截器类上应用 `@Priority` 注解。与装饰器一样，数值越小表示拦截器在拦截器链中越早执行，数值越大则表示越晚执行。假设你使用本章前面的示例拦截器，并希望它作为应用程序范围的拦截器执行。你希望它成为最早运行的拦截器之一，但不一定是第一个，因此理想情况下你希望为其他拦截器在其之前运行留出一些空间。为此，你可以为拦截器设置优先级为 `APPLICATION + 5`。拦截器类本身将如下所示：

```
@Interceptor
@MyInterceptorAnnotation
@Priority(Interceptor.Priority.APPLICATION + 5)
public class MyInterceptor {
@AroundInvoke
public Object aroundInvoke(InvocationContext context) throws Exception {
return context.proceed();
}
}
```

为单个 Bean 归档激活拦截器与为单个 Bean 归档激活装饰器类似。与装饰器一样，你需要在 `beans.xml` 文件中添加一个部分，该部分将按顺序列出所有要执行的拦截器。为此，你需要在要激活拦截器的归档的 `beans.xml` 文件中添加一个名为 `interceptors` 的部分。在此部分中，添加多个 `class` 标签，其中包含拦截器类的完全限定名，就像对装饰器所做的那样。

假设你有三个拦截器，希望它们在单个 Bean 归档中以特定顺序执行，分别命名为 `FirstInterceptor`、`SecondInterceptor` 和 `ThirdInterceptor`。要让它们按此顺序执行，你需要在对应 Bean 归档的 `beans.xml` 文件中按该顺序定义它们。最终的 `beans.xml` 文件将如清单 6-16 所示。

```

com.foo.bar.FirstInterceptor
com.foo.bar.SecondInterceptor
com.foo.bar.ThirdInterceptor

清单 6-16 在 beans.xml 中声明的拦截器
```



### 注意

应用范围级别的拦截器始终会在归档文件特定的拦截器之前执行。如果某个拦截器同时被应用范围级别和特定 bean 归档文件激活，则应用范围级别的激活和优先级将始终优先。在这种情况下，该拦截器可能会在同一个 `beans.xml` 文件中声明的其他拦截器之前执行，即使在该文件中它被声明为在其他拦截器之后运行。

### 构造函数拦截器

拦截器也可以用于拦截对构造函数的调用。拦截构造函数调用的方式与拦截常规方法相同；唯一的区别在于应用于拦截器方法的注解。通过在方法上应用 `javax.interceptor.@AroundConstruct` 注解而不是 `@AroundInvoke` 注解，该方法可用于拦截构造函数调用而非常规方法调用。常规方法拦截器与构造函数拦截器的另一个区别是，对于构造函数调用，`InvocationContext.proceed()` 方法将始终返回 `null`。构造函数拦截器方法的方法签名也略有不同。虽然它们可以像常规拦截器方法一样返回 `Object`，但实际返回的值始终被忽略。由于返回值始终被忽略，因此也允许构造函数方法简单地返回 `void`。然而，与常规拦截器方法不同，构造函数拦截器方法必须始终调用 `InvocationContext.proceed()` 以确保 bean 被实际创建。参见清单 6-17。

```
@AroundConstruct
public Object aroundConstruct(InvocationContext context) throws Exception {
return context.proceed();
}
清单 6-17
最基本的构造函数拦截器示例
```

实际上，允许将 `@AroundInvoke` 和 `@AroundConstruct` 方法应用于同一个方法。这使得单个拦截器方法可用于同时拦截构造函数和方法调用。这对于需要将拦截器应用于具有相似逻辑的构造函数和方法的情况可能很有用。例如，考虑一个拦截器，它测量执行构造函数或方法所花费的时间，然后记录此指标。此拦截器的逻辑在两种情况下完全相同，因此为了避免编写两次相同的代码或添加不必要的样板代码，可以为此定义一个单一的拦截器方法。由于拦截器方法必须同时适用于构造函数和常规方法，因此它必须具有 `Object` 作为其返回类型。与常规拦截器方法的兼容性也是为什么允许构造函数拦截器方法返回值（即使它始终被忽略）的原因。

```
@AroundInvoke
@AroundConstruct
Object measureInvocation(InvocationContext context) throws Exception {
long start = System.nanoTime();
try {
return context.proceed();
} finally {
long stop = System.nanoTime();
if (context.getConstructor() != null) {
logger.info(context.getConstructor() + " 耗时 " + TimeUnit.NANOSECONDS.toMillis(stop - start) + " 毫秒。");
}
else {
logger.info(context.getMethod() + " 耗时 " + TimeUnit.NANOSECONDS.toMillis(stop - start) + " 毫秒。");
}
}
}
```

我们之前简要介绍过 `InvocationContext` 上的 `getConstructor()` 方法。此方法将返回对构造函数的反射引用，可用于获取有关被拦截构造函数的更多信息。然而，与 `getMethod()` 返回的方法引用不同，无法使用此构造函数引用通过反射创建新实例并返回它。首先，`@AroundConstruct` 方法的结果将被忽略。其次，当构造函数拦截器调用 `InvocationContext.proceed()` 时，CDI 容器将创建一个完全托管的 bean 实例。如果你手动调用构造函数，你将得到一个非托管的 bean 实例。由于依赖注入可能尚未发生，你最终可能会得到一个部分创建的 bean 实例，如果你尝试使用它，可能会导致错误。



### 拦截器与生产者方法

以往无法对生产者方法生成的 bean 应用拦截器。虽然可以通过注解生产者方法来指示应使用某个拦截器，但该拦截器会应用于生产者方法本身，而非生产者方法所生成的 bean。值得庆幸的是，CDI 2.0 修复了此问题，新增了一个 API，可通过 `InterceptionFactory` 类以编程方式将拦截器添加到任何非托管对象。

`InterceptionFactory` 类可以直接注入到生产者方法中，也可以通过 `BeanManager` 创建。该类具有一个泛型类型参数，用于指定可应用拦截器的 bean 类型。注入 `InterceptionFactory` 时，必须将泛型类型设置为与生产者方法返回类型兼容的类型。请注意，这不必与生产者方法的返回类型相同；实际上，它可以是不同的方法。首先需要配置工厂，将拦截器应用于 bean。这可以通过工厂的 `configure()` 方法完成。该方法返回一个 `AnnotatedTypeFactory` 实例，该实例定义了一个用于添加新注解的方法。当生产者想要应用某个特定拦截器时，应创建该注解的实例并将其传递给 `AnnotatedTypeFactory.add()`。可以使用之前章节中使用的 `AnnotationLiteral` 辅助类来创建注解实例。在工厂上配置好注解后，可以将 bean 实例传递给工厂的 `createInterceptedInstance()` 方法，从而获得一个代理实例，该实例将启动拦截器链，而不是调用实际的 bean 方法。

假设有一个生产者方法，它返回一个包含前几个质数的列表，并且希望应用一个带有 `@Foo` 绑定注解的拦截器。像往常一样创建列表，然后将 `@Foo` 注解应用于拦截工厂，最后将新列表包装到代理中。现在可以返回刚创建的代理，而不是 bean 实例本身。这样，当列表被注入到某处时，所有调用都将通过刚注册的拦截器。参见清单 6-18。

```
public class FooLiteral extends AnnotationLiteral implements Foo {
}
public List produceInterceptedList(  InterceptionFactory> factory) {
List numbers = List.of(2, 3, 5, 7);
factory.configure().add(new FooLiteral());
return factory.createInterceptedInstance(numbers);
}
清单 6-18
在生产者方法中应用拦截器
```

也可以通过 `BeanManager` 获取 `InterceptionFactory`。为此，需要为要注入的 bean 类型提供一个 `CreationalContext`。拥有此上下文后，需要调用 `BeanManager` 的 `createInterceptionFactory()` 方法，并传入上下文和要应用拦截器的 bean 类型的类。结果与上一个示例中的 `InterceptionFactory` 相同。假设使用之前的 `@Foo` 注解，并将其应用于在生产者方法中创建的 `GreetingBean` 实例；可以同时注入 `BeanManager` 和 `CreationalContext`。创建工厂后，一切操作与上一个示例基本相同。

```
public GreetingBean produceInterceptedListThroughBeanManager(  BeanManager beanManager, CreationalContext context) {
InterceptionFactory factory = beanManager.createInterceptionFactory(context, GreetingBean.class);
factory.configure().add(new FooLiteral());
return factory.createInterceptedInstance(new GreetingBean());
}
```

在某些情况下，生产者方法可能生成一个不可代理类型的结果，因为它包含 final 方法。通常，无法将拦截器应用于不可代理类型的对象，但使用 `InterceptionFactory` 可以实现。`InterceptionFactory` 定义了一个名为 `ignoreFinalMethods()` 的方法。调用此方法将导致生成的代理忽略被代理对象上定义的任何 final 方法。使用此方法忽略 final 方法时，绝不能在代理上调用这些 final 方法。由于这些方法无法委托给实际的 bean 实例，它们将在代理实例上被调用，这可能导致不可预测的结果。

