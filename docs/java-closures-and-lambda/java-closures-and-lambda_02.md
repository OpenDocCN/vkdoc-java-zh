# 2. 理解 Java 8 中的 Lambda

Java 8 的核心新功能是引入了 lambda。然而，大多数介绍只会展示几个 lambda 示例，然后就让开发者自己去摸索其余内容。但是，如果你要开发现代 Java 程序，就需要精通 lambda 的使用方法。在本章中，我们将深入探讨 lambda 语法和语义的细节，包括如何创建更复杂的 lambda、如何处理方法调用与 lambda 的关系，以及这一切在底层是如何实现的。我们还将介绍一些操作 lambda 的经典方法，以及它们在 Java 中的实现方式。学完本章后，你应该能够熟练地创建所需的 lambda。

虽然我保证本章内容非常具体，但其目的是介绍 lambda 的语法和语义。为了保持示例的清晰和精确，我们将不为其创建应用上下文。如果你内心像学生一样开始问：“嗯，但这什么时候能用得上呢？”那么请随意翻阅本书其他章节中的实用示例。在这里，目标是成为 lambda、方法引用以及支持它们的函数式接口方面的技术专家。有了这个基础，我们才能进入本书后续部分更有趣、更复杂的案例。

## Java 8 的 Lambda 语法

最简单的 lambda 是接受一个参数并返回该参数，我们在清单 2-1 中有一个生成该 lambda 的方法。如果我们从外到内分析，这个方法最容易理解。让我们从签名开始：该方法名为 `identityFunction`，它将返回一个 `Function`。`Function` 类型是 Java 8 中的一个新接口，属于 `java.util.function` 包，该接口表示一个带返回值的单参数 lambda。我们返回的 `Function` 接受一个变量类型为 `V` 的对象，并返回一个相同类型的对象，因此其类型为 `<V, V>`。不同的函数可能接受一种类型并返回另一种类型；例如，一个函数可能接受任意对象并返回一个 String。在这种情况下，其类型将是 `<Object, String>`。但在这里类型是相同的：返回值与参数相同。进入方法体内部，我们有一个 return 语句。该 return 语句返回一个 lambda，而这个 lambda 就是 `Function` 接口的实现。该 lambda 在箭头左侧接受一个名为 `value` 的参数，并在箭头右侧返回 `value`。

**清单 2-1. 使用 Lambda 实现的恒等函数**

`public static <V> Function<V, V> identityFunction() {`

`return value -> value;`

`}`

**清单 2-2. 使用匿名内部类实现的恒等函数**

`public static <V> Function<V, V> identityFunctionAIC() {`

`return new Function<V, V>() {`

`@Override`

`public V apply(V value) {`

`return value;`

`}`

`};`

`}`

由于 `Function` 是一个接口，完全可以使用匿名内部类重新实现 `identityFunction()`。清单 2-2 给出了等效的匿名内部类实现。如果比较这两种实现，有几个有趣的地方值得注意。首先，新格式简洁得多。其次，匿名内部类中的 `return` 在 lambda 中是隐式的：lambda 由单个语句组成，因此该语句的值会被隐式返回。第三，lambda 版本没有显式声明 `value` 的类型：编译器会根据使用该值的上下文推断出 `value` 的类型。这种能力称为“类型推断”，它是函数式编程语言带来的最棒的特性之一。

类型推断正是清单 2-1 必须包装在方法中的原因：你需要提供一种方式让编译器知道函数的参数和返回类型。可以直接将 lambda 赋值给变量，但这里会变得有点棘手：毕竟，类型是什么？真正老派的 Java 开发者可能会尝试将其赋值给 `Object`，从而完全绕过类型系统，如清单 2-3 所示。然而，如果你这样做，将会遇到编译器错误：编译器需要知道你试图实现的是什么类型。更现代的 Java 开发者会想使用泛型来解决这个问题，如清单 2-4 所示。这样编译和运行都没有问题。不幸的是，这会丢失类型信息：编译器不再知道参数和返回类型是相同的类型。而方法允许我们通过传达所有已知的类型信息（同时尽可能保持通用性）来保留这些信息。但是，如果你知道 `it` 始终是一个 `Number`，那么你可以像清单 2-5 那样定义它，这样既能获得简洁的内联定义，又能保留类型信息。（清单 2-5 中 `<Number>` 的重复是因为参数和返回值都是 Number：第一个 Number 表示参数类型，第二个 Number 表示返回值类型。）作为一般规则，如果你知道具体类型，就在变量赋值时内联定义 lambda；如果需要捕获更复杂的类型信息，则在方法内定义 lambda。既然 Java 编译器具备了类型推断能力，那么这些类型信息就价值连城：不要丢失它们！

**清单 2-3. 将恒等函数赋值给对象变量**

`// 此代码无法编译`

`Object funObject = it -> it;`

**清单 2-4. 将恒等函数赋值给泛型函数变量**

`Function<?,?> function = it -> it;`

**清单 2-5. 将 Number 实例的恒等函数赋值给函数变量**

`Function<Number,Number> function = it -> it;`



### Lambda 作为闭包

Java 8 的 lambda 充当了闭包的角色。虽然在大多数编程讨论中，“lambda”和“闭包”这两个术语实际上可以互换使用，但它们在技术上存在差异。好消息是，Java 8 的 lambda 同时也是闭包，因此你可以任选其一称呼，并且在技术上都是正确的。然而，理解它们之间的区别有助于你更清晰地表达自己的意思。

“lambda”一词源于数学中一个探索可计算与不可计算问题的分支，具体来说，它来自一种称为 lambda 演算的结构。lambda 演算将函数应用视为计算中的主要行为，因此它从最抽象的意义上处理函数，完全不考虑函数本身可能代表或计算什么。在 lambda 演算中，函数本身就是相关的值。当编程语言从 lambda 演算中借用“lambda”这个术语时，它们指的是可以被当作值来处理的函数。一个 lambda 可以被赋值给一个变量（或多个变量），作为方法参数传递，并且通常可以像操作值 `2`、`true` 或 `"foo"` 一样被操作。

然而，“闭包”一词指的是 lambda 后来的一项创新。对于 Java 开发者来说，最容易理解的方式是将“闭包”视为“封装函数”。就像对象可以封装状态并通过特定的 API（例如，暴露字段的属性）暴露它一样，闭包也可以封装一些状态，然后在闭包被调用的任何地方、任何时间对该状态进行操作。“闭包”中的“闭”字指的是“开放”变量与“封闭”变量。这个想法直接来源于数学：一个没有绑定值的变量被称为“开放”的，而一个绑定了值的变量则被称为“封闭”的。闭包就是一个封闭了某些变量的 lambda，它通过封装一个变量环境来实现这一点。

这听起来可能是一个奇怪且技术性的概念，但在实践中其实非常常见。在 Java 8 引入 lambda 之前，可以说 Java 已经以匿名内部类的形式拥有了闭包。¹ 匿名内部类可以在一个上下文中捕获变量状态，并在另一个上下文中执行它：参见清单 2-6 中的示例。在该示例中，实现 `Greeter` 的匿名内部类封闭了变量 `greeting`。每当编译器坚持要求你在变量或参数声明中添加 `final` 关键字时，你就知道你在 Java 中创建了一个闭包。

Java 8 的 lambda 也充当闭包，这意味着它们会捕获作用域内的变量。这与匿名内部类的做法完全相同，清单 2-7 给出了一个 lambda 作为闭包的等效示例。不过，作为一种语法糖，你不再需要显式地将变量声明为 `final`：编译器会隐式地将封闭的变量视为 final，如果它们没有被当作 final 处理，编译器就会报错。²

**清单 2-6. 作为闭包的匿名内部类**

`public interface Greeter {`

`String createGreeting(String whom);`

`}`

`public static void main(String[] args) {`

`// 在此作用域中创建变量`

`final String greeting = "Hello, ";`

`Greeter greeter = new Greeter() {`

`@Override`

`public String createGreeting(String whom) {`

`// 在此处封闭（即捕获）变量`

`return greeting + whom + "!";`

`}`

`};`

`greetWorld(greeter);`

`}`

`public static void greetWorld(Greeter greeter) {`

`// 在此处让 greeter 使用被封闭的变量`

`// 注意 "greeting" 变量已超出作用域`

`System.out.println(greeter.createGreeting("World"));`

`}`

**清单 2-7. Java 8 的 Lambda 作为闭包**

`public static void main(String[] args) {`

`String greeting = "Hello, ";`

`Function<String, String> greeter = whom -> greeting + whom + "!";`

`greetWorld(greeter);`

`}`

`public static void greetWorld(Function<String, String> greeter) {`

`System.out.println(greeter.apply("World"));`

`}`

### 无参数与多参数 Lambda

基本的 lambda 是接受单个参数的，但 Java 并不局限于此：你可以拥有零参数的 lambda，也可以拥有多参数的 lambda。³ 每种情况的语法都略有不同，并且比简单情况更复杂。

零参数 lambda 的语法有些奇怪：如何表示没有任何参数呢？仅仅使用右箭头而不带任何参数会在 Java 语法中导致许多歧义情况，因此这不是一个选项。相反，他们为无参数函数引入了一个占位符，就像数字有零的占位符一样。这个占位符是一个左括号紧跟着一个右括号，看起来甚至像零：`()`。使用它的示例如清单 2-8 所示；在该清单中，我们创建了一个不接受任何参数并始终返回 1 作为 `Number` 的 lambda。

**清单 2-8. Number 实例的常量提供者**

`Supplier<Number> function = () -> 1;`

注意我们使用了不同的类型：我们想要的方法签名是无参数并返回 `Number`，但 `Function` 是一个其唯一方法签名带有一个参数的接口，因此我们需要一个不同的接口来表示这个不同的签名。⁴ 对于无参数的 lambda，其函数式接口是 `Supplier`：选择这个名字是因为它们用于在不需要任何输入的情况下提供值。

也可以有多参数 lambda。此时，语法应该是可以预见的：我们将再次使用括号来界定参数列表，并再次使用类型推断来指定类型。清单 2-9 给出了一个双参数函数（称为 bifunction）的示例。这类方法的函数式类型是 `BiFunction`。Java 的 SDK 不支持超过两个参数的 lambda，因为你确实不应该在这种复杂情况下使用 lambda。如果你真的想要一个带有大量参数的 lambda，请参阅下面的“Lambda 作为接口实现”部分，了解如何处理这种情况。同样值得注意的是，你不能使用可变参数（例如 `String... strings`）作为 lambda 的参数类型：你必须将可变参数视为一个数组。这个令人遗憾的事实是由于 Java 类型系统的一个不幸限制。⁵ 好消息是，你需要三个或更多参数或可变参数的情况非常有限：零参数、单参数和双参数函数可以处理你大部分情况。流（将在下一章介绍）和柯里化（将在下一小节介绍）几乎涵盖了所有这些情况。

**清单 2-9. 字符串拼接双参数函数**

`BiFunction<String, String, String> concat = (a, b) -> a + b;`



#### 偏函数应用与柯里化

每种文化及其亚文化都有其独特的语言，用以标识部落成员。函数式编程部落中一个古怪的部落暗语是“柯里化”（currying），这个动词源自逻辑学家哈斯凯尔·柯里（Haskell Curry）。要理解柯里化一个函数意味着什么，以及为何要这样做，让我们从另一个概念开始：偏函数应用。

偏函数应用正如其名：对函数的一部分进行应用。例如，考虑清单 2-9 中给出的字符串拼接二元函数。如果你想把同一个字符串拼接到多个字符串的前面，该怎么办？重复自己是编程中的原罪之一（“DRY”原则），而一遍又一遍地传递相同的参数无疑是在重复自己。你真正需要的是该二元函数的一个版本，其中第一个参数被固定为某个特定值。固定第一个参数被称为“应用”该参数，由于你只固定了两个可能参数中的第一个，因此这种应用是“偏”的。在清单 2-10 中，我们演示了进行偏函数应用时的操作：我们创建了一个 `Function`，它将固定的第一个参数应用于一个 `BiFunction`。由此产生的 `Function` 可以反复应用于多个参数。在清单 2-11 中，我们看到了如何实现这一点：尽管 Java 没有提供内置的偏函数应用方法，但编写一个这样的方法很简单。我们只需接受要应用的二元函数以及第一个参数，然后返回一个函数，该函数回调那个已设置好第一个参数的二元函数。

**清单 2-10. 字符串拼接的隐式偏函数应用**

`public static void main(String[] args) {`

  `BiFunction<String, String, String> concat = (a,b) -> a + b;`

  `greetFolks(whom -> concat.apply("Hello, ", whom));`

`}`

`public static void greetFolks(Function<String, String> greeter) {`

  `for(String name : Arrays.asList("Alice", "Bob", "Cathy")) {`

    `System.out.println(greeter.apply(name));`

  `}`

`}`

**清单 2-11. 字符串拼接的显式偏函数应用**

`public static void main(String[] args) {`

  `BiFunction<String, String, String> concat = (a,b) -> a + b;`

  `greetFolks(applyPartial(concat, "Hello, "));`

`}`

`public static <T,U,V> Function<U,V> applyPartial(`

  `BiFunction<T,U,V> bif, T firstArgument`

`) {`

  `return u -> bif.apply(firstArgument, u);`

`}`

`public static void greetFolks(Function<String, String> greeter) {`

  `for(String name : Arrays.asList("Alice", "Bob", "Cathy")) {`

    `System.out.println(greeter.apply(name));`

  `}`

`}`

#### 柯里化与函数形态

偏函数应用作用于一个函数并产生另一个函数。像这样在函数层面进行编程，正是“函数式编程”得名的原因，函数式编程语言鼓励你在这个层面思考，而不是在数据和指令的层面。将函数参数想象成拼图游戏中的空缺。只有特定形状的拼块才能填补那个空缺。你所能使用的每个方法都是拼图的一块，它们各自都有形状。这些方法的形状由它们接受的参数和返回的参数定义。就像拼图块一样，你也可以将这些形状连接起来形成新的形状。函数式编程就是连接适当的方法形状以填补函数空缺的艺术。

例如，清单 2-10 和 2-11 中的 `greetFolks` 方法提供了一个形状为 `String ➤ String` 的空缺：要调用该方法，我们需要提供一个接受字符串并返回字符串的函数。不幸的是，我们只有 `(String, String) ➤ String`：我们只有一个接受两个字符串参数并返回一个字符串的函数。这个形状并不完全匹配。所以我们所做的就是偏应用第一个参数，这相当于从方法的形状上削去了第一个参数。这给我们留下了一个具有所需形状 `String ➤ String` 的函数。

你可以将清单 2-11 中的 `applyPartial` 方法视为固定第一个参数。然而，`applyPartial` 方法实际上是将形式为 `(x,y)->z` 的函数转变为形式为 `x->(y->z)` 的函数。该方法接受一个有两个参数并返回一个值的函数，并产生一个有一个参数并返回一个函数的函数。返回的那个函数再接受另一个参数并返回一个值。从概念上讲，`applyPartial` 方法随后将 `x` 应用于这个新函数，并返回 `y->z`。这里有两个不同的步骤：首先，将函数的形状改变为 `x->(y->z)`；其次，应用第一个参数 `x`。

假设你想创建许多不同的问候函数。也许你想做国际化，所以现在你有许多不同的方式说“你好”。你可以每次都偏应用不同的“你好”，但这样你会一遍又一遍地执行 `applyPartial` 中改变形状的第一步。如果你想省去这种重复劳动，你可以获取 `applyPartial` 创建的中间函数。这将允许你自己生成问候函数。从形状为 `(x,y)->z` 的函数创建形状为 `x->(y->z)` 的函数被称为柯里化。你越常处理函数形状，这种操作就会变得越常见。我们在清单 2-12 中演示了柯里化以创建中间问候函数。

在这一点上，这看起来可能像是一个奇怪的技巧，但我们将在本书后面看到它的应用。事实上，这是一个非常有用的技巧，以至于函数式编程语言通常默认提供已柯里化的函数：例如，在 OCaml 中，使用表达式 `"let add x y = x + y"` 定义一个函数将创建一个名为 `add` 的函数，其类型为 `x->y->z`⁶，它既可以作为产生 `z` 的二元函数使用，也可以作为产生 `y->z` 的函数使用。函数式编程语言具有这种隐式柯里化的原因是，在函数式编程中，函数的形状比它们的内容更重要。形状 `(x,y)->z` 是一个二元函数：它接受两个参数来产生一个值。二元函数很少出现，并且很难融入程序中。然而，形状 `x->y->z` 仍然是一个函数：你可以将其视为 `x->w`，其中 `w` 是 `y->z` 函数。虽然二元函数很少见，但函数却无处不在。因此，你通常可以使用柯里化来使二元函数适应函数的插槽。

**清单 2-12. 字符串拼接二元函数的显式柯里化**

`public static void main(String[] args) {`

`BiFunction<String, String, String> concat = (a, b) -> a + b;`

`Function<String, Function<String, String>> curriedConcat = curry(concat);`

`for (String greetings : Arrays.asList("Hello", "Guten Tag", "Bonjour")) {`

`greetFolks(curriedConcat.apply(greetings + ", "));`

`}`

`}`

`public static <T,U,V> Function<T,Function<U,V>> curry(BiFunction<T,U,V> bif) {`

`return t -> (u -> bif.apply(t,u));`

`}`

`public static void greetFolks(Function<String, String> greeter) {`

`for (String name : Arrays.asList("Alice", "Bob", "Cathy")) {`

`System.out.println(greeter.apply(name));`

`}`

`}`



### 无返回值的 Lambda

到目前为止，我们讨论的都是始终返回值的 lambda。然而，有一种非常常见的 lambda 类型根本不返回任何值：这类 lambda 被称为终端 lambda。例如，在遍历集合中的每个元素或终止值流时，你都会用到它们。它们与供给者（supplier）相反：它们接收一个值，但不产生任何结果。可以预见，这类消费型 lambda 对应的函数式接口是 `Consumer`。清单 2-13 给出了一个消费者的示例：其中，我们使用一个消费者来表示如何打印问候语。在第 3 章讨论集合时，将会给出一种更符合 Java 8 风格的写法。

**清单 2-13\. Consumer 和 BiConsumer 示例**

`public static void greetFolks() {`

  `Consumer<String> doGreet = name -> System.out.println("Hello, " + name);`

  `for (String name : Arrays.asList("Alice", "Bob", "Cathy")) {`

    `doGreet.accept(name);`

  `}`

`}`

`public static void concat() {`

  `BiConsumer<String,String> printConcat = (left,right) ->`

    `System.out.println(left + right);`

  `for (String name : Arrays.asList("Alice", "Bob", "Cathy")) {`

    `printConcat.accept("Goodbye, ", name);`

  `}`

`}`

### 具有复杂主体的 Lambda

通常，lambda 是简单的单行表达式。但偶尔，你需要执行一些更复杂的操作。当你编写的代码需要与 lambda 出现之前的 API（例如 `Autocloseable`）交互时，这种情况尤为常见。如果你需要为 lambda 创建复杂的主体，可以将主体放在花括号内指定。但这样做时，你需要负责显式调用 `return`。清单 2-14 给出了一个包装了 `Autocloseable` 的 lambda 示例。如你所见，这种方法让你的 lambda 看起来更像一个方法，因此你最好定义一个方法并显式引用它。如果你需要，甚至还有一种简洁的语法可以将方法转换回 lambda：请参阅下文“将方法转换为 Lambda”。

**清单 2-14\. 具有复杂主体并包装了 Autocloseable 的 Lambda**

`Function<File, Byte> firstByte = file -> {`

  `try (InputStream is = new FileInputStream(file)) {`

    `return (byte) is.read();`

  `} catch (IOException ioe) {`

    `throw new RuntimeException("Could not read " + file, ioe);`

  `}`

`};`

`for (String filename : args) {`

  `File file = new File(filename);`

  `int byte1 = firstByte.apply(file);`

  `System.out.println(filename + "\t=>\t" + byte1);`

`}`

### 显式类型声明的 Lambda

有些 Java 开发者就是无法忍受放弃显式类型声明，有些代码分析工具（例如 IDE）偶尔也需要一点额外的帮助，有时你甚至可能会把编译器搞糊涂。⁷ 在这些情况下，你需要向编译器提供显式类型来帮助它。当你遇到这些情况时，这通常表明你的 API 可能存在问题，你最好先修复导致问题的歧义。不过，如果你确实想显式指定类型，可以通过使用带括号的参数列表来实现。如果你在参数周围加上括号（即使只有一个参数），就可以添加显式类型。在清单 2-15 中，我们看到了一个会引起混淆的案例，以及如何通过添加显式类型来解决它。

**清单 2-15\. 使用显式类型声明解决重载方法歧义**

`public static void main(String[] args) {`

  `// .toUpperCase() 存在于 String 中，但不存在于 CharSequence 中`

  `transform(args[0], (String str) -> str.toUpperCase());`

`}`

`public static String transform(`

  `String str,`

  `Function<String, String> transformer`

`) {`

  `return transformer.apply(str);`

`}`

`public static CharSequence transform(`

  `CharSequence str,`

  `Function<CharSequence, CharSequence> transformer`

`) {`

  `return transformer.apply(str);`

`}`

### 作为运算符的 Lambda

Java 8 为参数类型和返回值类型相同的常见情况提供了一种简写形式。在这种情况下，你可以通过使用“运算符函数式接口”⁸来避免重复声明。有两个适用于对象的此类接口：`BinaryOperator` 和 `UnaryOperator`。`BinaryOperator` 是一个 `BiFunction`，其参数和返回值类型都相同；`UnaryOperator` 是一个 `Function`，其参数和返回值类型都相同。除了类型签名更加简洁之外，这些运算符与它们对应的函数式接口没有区别。清单 2-16 演示了如何使用运算符类型来捕获本章中一直使用的大写转换和字符串拼接 lambda。

**清单 2-16\. 使用运算符类型捕获常见的 Lambda**

`UnaryOperator<String> upperCase = str -> str.toUpperCase();`

`BinaryOperator<String> concat = (left,right) -> left + right;`

### 作为谓词的 Lambda

本书开篇讲述了一个关于谓词类型实用性的故事。Java 将谓词实现为 Function 函数式接口的一个特例：具体来说，它是一个接收任意类型并返回 `boolean` 的函数。这是一种极其常见的函数式类型，它只返回 true 或 false，并且在函数式编程中任何涉及条件分支、过滤或守卫的地方都会用到。我们在清单 2-17 中演示了一个针对 `String` 的基本“非 null 或空”检查。

**清单 2-17\. 检查字符串是否为 null 或空的谓词 Lambda**

`Predicate<String> notNullOrEmpty = s -> s != null && s.length() > 0;`



### 带有原始类型参数的 Lambda 表达式

在清单 2-8 中，我们看到一个返回给定整数值 `1` 的 `Supplier`。然而，该值是以扩展 `Number` 的对象形式返回的，而非原始类型值。这是一个“装箱整数”的例子，刚刚发生的隐式转换被称为“自动装箱”。许多开发者对装箱原始类型值感到不适，尽管装箱的实际性能影响在很大程度上已被编译器优化、标准库性能技巧以及 Java 出色的垃圾回收器所抵消。然而，在某些情况下，自动装箱的性能开销仍然显著；如果你处理大量整数值，最好避免自动装箱。针对这些情况，你可以创建使用原始类型的 Lambda 表达式。

存在针对 `double`、`int` 和 `long` 原始类型的原始函数式接口。还有一些函数式接口接收这些原始类型之一并生成另一个原始类型，例如 `DoubleToIntFunction`。Java 提供了大多数函数式接口的原始类型版本。此列表中一个最显著的缺失项是 `ObjIntBifunction`：我不知道为什么 Java 没有提供这个接口。

虽然我们在清单 2-18 中只查看了 `int` 接口，但请放心，对于 `double` 和 `long` 原始类型，也存在每个接口的对应版本。只需将类名中的 `"Int"` 替换为 `"Double"`，你就得到了 `double` 类型的对应函数式类型。

**清单 2-18\. 检查字符串是否为 null 或空的谓词 Lambda**

`IntFunction<String> intToString = i -> Integer.toString(i);`

`ToIntFunction<String> parseInt = str -> Integer.valueOf(str);`

`IntPredicate isEven = i -> i % 2 == 0;`

`ToIntBiFunction<String,String> maxLength =`

  `(left,right) -> Math.max(left.length(), right.length());`

`IntConsumer printInt = i -> System.out.println(Integer.toString(i));`

`ObjIntConsumer<String> printParsedIntWithRadix =`

  `(str,radix) -> System.out.println(Integer.parseInt(str,radix));`

`IntSupplier randomInt = () -> new Random().nextInt();`

`IntUnaryOperator negateInt = i -> -1 * i;`

`IntBinaryOperator multiplyInt = (x,y) -> x*y;`

`IntToDoubleFunction intAsDouble = i -> Integer.valueOf(i).doubleValue();`

`DoubleToIntFunction doubleAsInt = d -> Double.valueOf(d).intValue();`

`IntToLongFunction intAsLong = i -> Integer.valueOf(i).longValue();`

`LongToIntFunction longAsInt = x -> Long.valueOf(x).intValue();`

请注意，现在有一种新的方法可能产生歧义：你可以有一个重载方法，它同时接受 `Function<Integer,X>` 和 `IntFunction<X>`，如图 2-19 所示。如果你这样做，编译器的错误信息几乎肯定会让你困惑。此时你可能会收到两种错误信息，具体取决于 Lambda 实现的细节。⁹ 这两种信息都在争夺 Java 8 中最无帮助的错误信息称号，它们是 `"Cannot resolve method"` 和 `"Argument cannot be applied to <lambda parameter>"`。在这种情况下，你需要提供显式类型，正如我们在“带有显式类型的 Lambda 表达式”中讨论的那样。如果你的显式类型引用了原始类型，你将得到 `IntFunction` 变体；如果你的显式类型引用了装箱类型，你将得到 `Function` 变体。

**清单 2-19\. 使用原始函数式接口时的歧义方法类型**

`static void methodBeingCalled(Function<Integer, String> function) {}`

`static void methodBeingCalled(IntFunction<String> function) {}`

`public static void main(String[] args) {`

  `// 这会抛出异常`

  `methodBeingCalled(i -> Integer.toString(i));`

  `// 这不会`

  `methodBeingCalled((int i) -> Integer.toString(i));`

`}`

## 将方法转换为 Lambda 表达式

在本章中，我们一直在显式地构造所有 Lambda 表达式：我们将 Lambda 表达式构造为匿名函数。然而，你也可以将任何方法调用转换为 Lambda 表达式。当我说“任何方法调用”时，我是认真的：静态方法、实例方法和构造函数都可以。这种微小的语法粘合剂使得从面向对象代码过渡到函数式代码变得非常容易，并真正提供了一种强大的方式来获得两种范式的最佳效果。

从方法创建 Lambda 表达式的结构称为“方法引用”。创建方法引用的方式与调用方法非常相似，但使用双冒号（`::`）代替点号（`.`）。我喜欢将点号视为一个点，意思是“让我调用这个方法一次”，而冒号是许多点，意思是“让我多次调用这个方法”。一旦你使用双冒号创建了方法引用，你就可以像使用显式 Lambda 表达式一样使用该方法引用。将方法转换为 Lambda 调用的细节将在下面的各个小节中介绍。

### 将静态方法转换为 Lambda 表达式

最简单的情况是将静态方法转换为 Lambda 表达式。为此，你只需遵循我们上面列出的公式：获取方法调用，并将点号替换为冒号。编译器将查看该方法的签名并执行类型推断，为你构建适当的接口实现。清单 2-20 展示了我们在 2-18 中看到的几个 Lambda 表达式的静态替代方案：请注意，编译器成功发现我们需要一个 `IntFunction<String>`，而不是 `Function<Integer,String>`，并提供了适当的类型。如果目标类型签名存在歧义，例如在清单 2-19 中，那么你必须获得一个显式的类型签名。有两种方法可以做到这一点：要么使用带有显式类型的显式 Lambda 表达式来解决它，要么将方法引用分配给具有所需类型的变量，然后将该变量传入。在 Java 8 中，无法对方法引用执行内联强制转换操作。

**清单 2-20\. 由静态方法创建的 Lambda 表达式**

`IntFunction<String> intToString = Integer::toString;`

`ToIntFunction<String> parseInt = Integer::valueOf;`

### 将构造函数转换为 Lambda 表达式

构造函数存在于静态方法和实例方法之间的奇怪领域：它们是实现作用于实例的静态方法。取决于你从哪个角度看，它们可能被称为 `"new"`、`"<init>"`，或者与类同名。获取构造函数的方法引用与获取静态方法的方法引用相同：只需使用 `"new"` 作为方法名。从用户的角度来看，这确实很简单。然而，许多类有多个构造函数，因此类型推断在这里承担了大量繁重的工作。请参见清单 2-22，了解 `BigInteger` 的 `String` 构造函数作为 `Function` 的示例。

**清单 2-21\. 由构造函数创建的 Lambda 表达式**

`Function<String,BigInteger> newBigInt = BigInteger::new;`



### 将实例方法转换为 Lambda

最有趣且最巧妙的方法引用是实例方法引用：你可以将任何对象打包，并将其方法调用传递出去。该对象随后可能超出作用域，但其方法仍可通过此 lambda 使用。在清单 2-22 中，我们展示了多种不同类型的实例方法引用。在 `print` 中，我们使用静态字段引用来构造方法引用。对于 `makeGreeting`，我们使用了一个 `String` 常量值。`lookup` 和 `randomInt` 都展示了一种巧妙的方式，让变量只能通过 lambda 访问：在 `lookup` 的例子中，它是一个方法的返回值；在 `randomInt` 的例子中，对象是内联构造的；`resolvePath` 基于 `base` 字段值工作；`compareAgainst` 方法则使用参数来生成一个方法引用。在所有这些情况下，实例都是在创建 lambda 时被捕获的。这意味着，例如，如果 `base` 被重新赋值，`resolvePath` 仍然会基于之前的 `base` 进行解析。此外，无论调用多少次，`randomInt` 始终作用于完全相同的 `Random` 实例。以这种方式使用实例方法，你可以配置一个对象，然后使用该对象提供的某个关键功能，而无需担心有人会篡改配置。

**清单 2-22\. 由实例方法创建的 Lambda**

`Consumer<String> print = System.out::println;`

`UnaryOperator<String> makeGreeting = "Hello, "::concat;`

`IntFunction<String> lookup = Arrays.asList("a","b","c")::get;`

`IntSupplier randomInt = new Random()::nextInt;`

`Path base = Paths.get(".");`

`Function<String,Path> resolvePath = base::resolve;`

`public IntUnaryOperator compareAgainst(Integer compareLeft) {`

  `return compareLeft::compareTo;`

`}`

### 指定稍后使用的方法

使用方法引用的最后一种方式在 Java 的面向对象世界中或许是最奇怪的：你可以指定一个方法，稍后在某个任意实例上执行。其思路是，你指定一个实例方法的名称，它就会变成一个函数，该函数的第一个参数就是被调用的那个实例。

所以这段代码：

`UnaryOperator<String> stringOp = String::concat`

等价于：

`UnaryOperator<String> stringOp = (x,y) -> x.concat(y);`

如你所见，方法引用的构造方式与静态方法完全相同。Java 会将其识别为实例方法，并因此构造出相应类型的 lambda。同样，类型推断为我们承担了大量繁重的工作，以保持语法的简洁性。

这种使用方法引用的方式作为迭代的替代方案非常常见：你可以传入一个方法，该方法会对集合中的每个元素执行。大多数熟悉这种技巧的开发人员通常会在动态类型语言中看到它：例如，在 Groovy 中这就是“星点运算符”。在 Java 8 中，你现在可以在保持类型安全的同时实现类似的技巧。

## 作为接口实现的 Lambda

如果你在家跟着操作，可能会注意到在清单 2-3 中，当我们试图将 lambda 赋值给一个 `Object` 时出现的错误。该错误信息是：“Lambda 转换的目标类型必须是接口。” 这是一个非常精确的术语，让我们来解读一下。当编译器遇到 lambda 时，它会尝试将该 lambda 转换为 Java 类型系统中一个有意义的类型。这个过程被称为“lambda 转换”，而 lambda 转换的目标被称为“目标类型”。编译器只知道如何将 lambda 转换为接口——它不能转换为原始类型、抽象类型或具体类型。到目前为止，我们一直只使用 Java 的函数式接口，但 lambda 也可以用于实现其他接口，包括你自定义的接口。这非常强大，尤其是与将任何方法调用转换为 lambda 的能力结合使用时。我们在前一章的清单 1-11 和 1-12 中已经看到了这一点。在这里，我们将深入探讨其细节和限制。

基本思想是，如果你有一个包含单个抽象方法的接口，你可以使用 lambda 来提供该接口的实现。这意味着，很容易将 lambda 作为 `Comparable`、`Iterable` 和 `Autocloseable` 的实现提供，但不能用于 `Iterator` 或 `Collection`。在清单 2-23 中，我们看到了一些有用且常见的技巧。

**清单 2-23\. 由 Lambda 实现的有用接口的各种示例**

`/**`

`* 给定一个处于事务模式的 {@link java.sql.Connection}，`

`* 使用 Java 的 {@code try-with-resources} 自动提交事务。`

`*/`

`public static AutoCloseable autoCommit(Connection c) throws SQLException {`

  `if (c == null) return () -> {};`

  `return c::commit;`

`}`

`// 从 lambda 创建并发接口`

`Runnable runMe = () -> System.out.println("Ran!");`

`Callable<Long> callMe = System::currentTimeMillis;`

`ThreadFactory t = Thread::new;`

`// 实现监听器接口`

`ExceptionListener listener = Exception::printStackTrace;`



### 默认方法

内联定义接口的能力使得接口更加简洁易用。然而，Java 8 通过允许接口为其方法定义默认实现，进一步简化了操作。这开始严重模糊接口与抽象类之间的界限：例如，`java.util.AbstractCollection` 抽象类现在可以纯粹作为接口来实现。

举个例子，我们可以通过提供一个 lambda 来内联定义一个 `Iterator` 类型。该 lambda 负责提供“下一个元素”，或许还会消费该元素。当所有元素都被消费完时，会使用一个特殊值来发出信号。用户只需提供这个 lambda；所有其他的 `Iterator` 方法都作为默认方法提供。该类的代码如清单 2-24 所示。从中可以看到，默认方法使用 `default` 关键字提供。与接口的任何其他成员一样，所有默认方法都是 `public` 的。并且与接口的任何其他成员一样，如果你愿意，可以指定 `public`，尽管这是多余的，这样做也没有任何效果。

默认方法是 Java 生态系统的一个重大变化，看起来你似乎再也不会使用抽象类了。然而，默认方法也有一些注意事项。首先，无法继承默认方法的实现：如果你重写了该方法，就必须自己实现它。（不过，请参阅下一节了解可能的变通方法。）其次，如果一个类有两个接口提供了冲突的默认方法，你将不得不重写该方法并自己实现。

默认方法在 Java 8 代码中扮演着重要角色，并且已经被改造并应用于许多标准 Java 接口。在后续章节中讨论这些领域时，我们将探讨它们出现在哪里。

**清单 2-24. FunIterator 类，演示默认方法**

`/**`

`* 一个 {@link java.util.Iterator} 的实现，可以作为 lambda 实现。`

`* 你也可以使用此类作为实现 {@code Iterator} 的更简单方式。`

`* <p>`

`* 此接口默认不支持 {@link #remove()}。`

`*/`

`public interface FunIterator<E> extends Iterator<E> {`

  `/**`

   `* 提供下一个元素，并可选择消费它。这是要作为 lambda 实现的抽象方法。`

   `* 如果传入 {@code false} 作为参数，此方法应始终返回相同的值，并且永远不应耗尽迭代器。如果`

   `* 传入 {@code true} 作为参数，迭代器应在返回值后前进到下一个元素。在任何时候，如果没有更多元素，`

   `* 迭代器应返回 {@link java.util.Optional#empty()}。此方法绝不应抛出异常或返回 {@code null}。`

   `*`

   `* @param consume  元素返回后是否应被消费。`

   `* @return 如果没有其他元素，则返回 {@link java.util.Optional#empty()}；`

   `*         否则，返回包含下一个元素的 {@link java.util.Optional}。绝不为 {@code null}。`

   `*/`

  `Optional<E> nextElement(boolean consume);`

  `@Override`

  `default boolean hasNext() {`

    `return nextElement(false).isPresent();`

  `}`

  `@Override`

  `default E next() {`

    `return nextElement(true).orElseThrow(`

      `() -> new NoSuchElementException("迭代器已耗尽")`

    `);`

  `}`

`}`

### 静态方法

这与 lambda 没有直接关系，但现在是提及它的好时机：从 Java 8 开始，接口现在也可以提供静态方法，就像任何具体类或抽象类可以提供静态方法一样。如果你想向外部世界提供默认方法实现，可以使用静态方法来实现：只需让默认方法调用该静态方法即可。

既然接口可以有静态方法，一些 Java 接口已经被扩展以提供实用方法。在我看来，最值得注意的是 `Comparator`，它已经获得了大量的实用方法。例如，在清单 2-25 中，我们定义了一个 `Comparator`，它将 null 的 `File` 实例放在最后，并按文件名对其余部分进行排序。通过允许静态方法，Java 8 已经使“工具类”约定过时了，在该约定中，接口 `Foo` 会有一个工具类 `Foos` 或 `FooUtils`。现在这些实用方法直接驻留在接口本身上。

**清单 2-25. 使用 Comparator 静态方法生成一个 Comparator**

`Comparator<File> fileComparator =`

    `Comparator.nullsLast(`

      `Comparator.comparing(File::getName)`

    `);`

## 函数式接口辅助方法

函数式接口本身带有许多静态方法和默认方法，以帮助你构建所需的函数。在本节中，我们将研究这些方法，并了解何时以及为何要使用它们。

### Function.identity() 和 UnaryOperator.identity()

最容易理解的可能是 `Function.identity()`。它提供了一个简单地返回其参数的函数。在测试中，当你想要测试一个接受函数并对其执行操作的东西时，它非常有用。它也可以作为一个占位函数：假设你有五种类型，对于其中四种，你想要执行某种转换；对于第五种，你不想执行转换。在这种情况下，你可以通过使用此方法来维护代码库的对称性，并避免使用 `null`（以及 `NullPointerException`）。¹⁰ 此方法也可在 `UnaryOperator` 类型下使用，这样你可以继续使用该 `Function` 子类并节省一些输入。

### Function.compose 和 Function.andThen

这两个默认方法提供了在给定函数之前和之后添加功能的能力。这些方法允许你以一种相当流畅的 API 创建管道效果，并且对于将函数的类型转换为你想要的类型特别有用；你可以内联地在函数前后添加转换函数。

如果你想更改结果类型，请使用 `andThen`。给定函数 `X->Y`，`andThen` 允许你通过指定一个接受结果 `Y` 并生成 `Z` 的函数来创建 `X->Z`。参数 `X` 将被输入到实例函数，然后实例函数的结果 `Y` 被输入到参数函数。例如，考虑这一系列方法调用：

`Function<Integer,String> f = Integer::toString;`

`f = f.andThen("10"::concat).andThen(Integer::parseInt);`

这将创建一个函数，该函数将一个 `int` 转换为一个 `String`，然后在其前面加上“10”，然后将该值解析回一个 `int`。最初是 `int->String` 类型，现在变成了 `int(->String->String)->int`，在 Java 类型中返回为 `int->int`。

如果你想更改参数类型，请使用 `compose`。这个方法本应叫做 `butFirst`，因为它的工作方式与 `andThen` 类似，但作用于前端而不是后端。给定函数 `X->Y`，`butFirst` 允许你通过指定一个接受某个 `W` 并生成 `X` 的函数来创建 `W->Y`。指定函数的结果作为参数输入到实例函数。我们可以通过以下方式“反向”指定前面的示例：

`Function<String,Integer> f = Integer::parseInt;`

`f = f.compose("10"::concat).compose(Integer::toString);`



### Consumer.andThen

Java SDK 并未提供用于修改接受类型的 `Consumer.compose` 方法，¹¹ 但提供了 `Consumer.andThen` 方法。由于消费者接受一个参数但不产生结果，`andThen` 所做的只是添加额外的处理流程：给定两个相同类型的消费者，它会提供一个单一的消费者，依次执行这两个给定的消费者。这让你可以在处理流的末尾添加额外的处理。这对于在消费者中添加日志记录或其他通知特别有用。

### Predicate.and 与 Predicate.or

谓词负责提供布尔检查，而这些检查通常是复合类型的结构。尽管我们在本书开头讨论了一个简单案例（一个“非空”谓词），但你很少能幸运到拥有简单的谓词逻辑。通常，你会遇到复杂的逻辑链。事实上，使用 `predicate` 类的主要优势之一就是你可以高层级地构建这个复杂链，然后稍后对对象执行它。要连接两个谓词，你必须决定是使用 `&&` 风格的连接还是 `||` 风格的连接。前者由 `Predicate.and` 处理，后者由 `Predicate.or` 处理。

在这两种情况下，谓词都是短路求值的：如果我们已经知道结果，谓词将不会被执行。如果你有 `foo.and(bar).test(baz)`，并且 `foo.test(bar)` 求值为 `false`，那么 `bar.test(baz)` 永远不会被执行。类似地，如果你有 `foo.or(bar).test(baz)`，并且 `foo.test(bar)` 求值为 `true`，那么 `bar.test(baz)` 永远不会被执行。这正是 Java 处理其布尔运算符的方式。

### Predicate.isEqual

如果你有某个特定的值，并且想要一个能告诉你其他值是否与之相等的测试，那么请使用 `Predicate.isEqual`。它使用 Java 自身的 `Objects.equals(left,right)` 方法，该方法首先执行 `null` 检查，然后返回 `left.equals(right)`，因此 `Predicate.isEqual` 可以安全地与 `null` 一起使用。

### Predicate.negate

如果你想要给定谓词的逻辑相反值，可以使用 `Predicate.negate` 来获取。将 `Predicate.negate` 与 `Predicate.isEqual` 结合使用，就得到了第 1 章开头提到的非空检查：`Predicate.isEqual(null).negate()`。

### BinaryOperator.minBy 与 BinaryOperator.maxBy

这是两个非常有用的方法，但很多人并没有意识到它们的存在，因为这些方法隐藏在函数式接口类型中。这两个方法都提供了一个 `BinaryOperator`，它是一个 `BiFunction`，其参数和返回值类型都相同。由于 `BinaryOperator` 扩展了 `BiFunction`，只要你的 `BiFunction` 类型签名匹配，你就可以随时传入这些 `BinaryOperator`。所提供的 `BinaryOperator` 接受两个相同类型的参数，并返回最大（`maxBy`）或最小（`minBy`）的参数。

这看起来可能没什么大不了的，但实际上非常重要。在下一章中，我们将看到它如何与对象集合和流一起工作，以及这些方法如何自然地融入其中。更棒的是，由于这两个方法都将 `Comparator` 作为参数，我们可以利用 `Comparator` 上提供的所有有用的实用方法，这些也将在下一章中介绍。

明显的用例是在众多元素中找出最小和最大的元素。然而，你可以使用那个 `Comparator` 来挑选出最接近你想要的值的元素。你也可以使用 `Comparator` 来提供一个评分算法，然后使用这个 `BinaryOperator` 来获取得分最高或最低的元素。任何时候，只要你有一个合适的排序或“与目标的距离”的概念，`BinaryOperator.minBy` 和 `BinaryOperator.maxBy` 很可能都会帮到你。

## Lambda 最佳实践

当人们初次接触面向对象编程时，往往倾向于过度使用继承。这个语言特性如此酷炫，以至于你最终会构建出层层叠叠的继承结构，导致代码变得极其难以理解和维护。Lambda 表达式也会发生类似的情况。现在，你对 lambda 的了解已经足以“搞出事情”，你可能会过于兴奋地应用这些新知识，到处使用函数式接口。这种热情是好的，但在你出去把所有代码都“lambda 化”之前，请允许我给你一些忠告。

### 使用接口

过于热心的函数式程序员最常犯的错误是在类型签名中使用函数式接口。通常，你应该避免直接使用函数式接口类型，而应该提供单一方法接口作为你方法的参数。这些接口成为创建自文档化代码、提供有意义的类型信息的一种方式，同时也为用户提供了提供实际 Java 类型的可能性。如果他们想使用 lambda 和方法引用来实现你的接口，他们仍然可以这样做，但不要强迫他们。

考虑这两个方法签名：`processFile(LineHandler handler)` 和 `processFile(Consumer<String> handler)`。在后一种情况下，`String` 提供了什么，或者期望消费者做什么，并不明显。更重要的是，如果你以后想提供一个 `processFile(FileHandler handler)` 方法，你可以做到，而在另一种情况下，你最终会遇到类型冲突。

### 内联定义 Lambda

当你确实使用 lambda 时，请内联定义它们。除非你对 lambda 进行某种花哨的操作，否则没有理由将它们赋值给变量。你想要内联定义 lambda 的原因是，当类型发生变化时，它能让你的代码更加灵活：你让类型推断为你承担更多繁重的工作，并使你的代码适应不断变化的上下文。如果你开始将 lambda 赋值给变量，你就必须开始显式地指定类型，这就不必要地固化了你的类型。

### Lambda 应始终是线程安全的

在本书的后续章节中，我们将看到许多 lambda 使并发编程变得更加容易的地方。许多基于 lambda 构建的结构会执行并发操作，有时甚至没有太多警告。因此，你的 lambda 必须始终是线程安全的。对于实例方法句柄要特别留意这一点，因为线程危险的状态通常可能隐藏在这些实例中。

### 不要使用 Null

你的代码中永远不应该使用 `null` 关键字。既然 Java 有了 `Optional` 类型，就完全没有必要使用它了。每当你有一个方法时，你应该明确说明你是否接受 `null`，并且通常你不应该接受它。这将使你免于 `NullPointerException` 在远离实际错误源头的令人讨厌的地方突然出现。当你开始使用流和 lambda 时，这尤其是一个痛苦的问题，因为当你去调试时，堆栈跟踪可能对你没什么用。解决方案是永远不接受 `null`，并积极检查它，一旦出现就立即大声报错。一些开发者担心这会增加代码开销并影响性能，但这种担忧是过早优化的极致表现。一方面，不防范 null 可能导致代码严重崩溃，因此确保正确性很重要：我可以无限快地给你错误的答案。同时，添加这些防护措施将从你的代码中移除大量的 `null`，因此这些 null 检查将成为冷分支，而 Java 高度优化的运行时将使它们实际上零成本。所以这些 null 检查既价值高又成本低。



### 不要释放扎尔戈

程序员应该学习多种编程语言，这是普遍共识。原因有很多，但有一个常被忽略：编程语言会形成由不同人群和文化构成的生态系统，你常常能从这些文化中学到东西，从而丰富你对编程的整体思考方式。

例如，JavaScript 社区发现了一个名为“扎尔戈”的怪物实体。扎尔戈是一种难以理解的恐怖存在，它试图吞噬并毁灭你。程序员面临风险，因为扎尔戈潜伏在你的代码之下，等待着被那些编写出难以推理代码的粗心程序员释放出来。每一位编写函数式代码的程序员都必须警惕扎尔戈的威胁。

当你传递 lambda 表达式时，如果混合了同步和异步风格，就很容易释放扎尔戈。当你的代码执行用户的 lambda 时，必须明确该 lambda 是在方法解析完成之前执行（“同步”），还是在未来的某个时间点执行（“异步”）。你绝不能将这两种方法混用。

例如，考虑以下代码：

`List<Number> numbers = Arrays.asList(1, 2, 3);`

`numbers.forEach(i -> System.out.println(i));`

`System.out.println("Done");`

在这段代码中，哪个会先打印出来，是“`3`”还是“`Done`”？如果你查阅 `Iterable.forEach` 的 API，它会告诉你，它执行 lambda “直到所有元素都被处理完毕或抛出异常”，这告诉你它是同步的。因此，你知道 `"Done"` 会出现在 `"3"` 之前。

另一方面，考虑这段代码：

`Files.lines(Paths.get("build.gradle")).forEach(`

`s -> System.out.println(s)`

`);`

`System.out.println("Done");`

在这种情况下，无法保证 `"Done"` 会出现在 `build.gradle` 的内容之前——`Stream.forEach` 方法的 API 明确说明：“对于任何给定的元素，该操作可以在库选择的任何时间和任何线程中执行”，这告诉你它是异步的。

在这一点上，避免扎尔戈似乎显而易见，但对于粗心的人来说，通往扎尔戈的道路很容易踏上。

故事通常是这样发展的。假设你有一个方法要执行一些耗时的工作，比如访问数据库。与其让你的代码阻塞等待，不如让用户传递一个回调函数，在数据返回时执行。代码看起来像这样：

`public void readData(String key, ResultsHandler handler) {`

`threadPool.submit(() -> {`

`SomeModel resultingModel = null;`

`/* 执行昂贵的查询，设置 "resultingModel"... */`

`handler.accept(resultingModel);`

`}`

`);`

`}`

后来，你决定在这个方法前面添加一个缓存层，因为这样效率更高，而且一些稍微过时的数据也没问题。所以你更新了代码，看起来像这样：

`public void readData(String key, ResultsHandler handler) {`

`SomeModel cachedModel = cache.get(key);`

`if (cachedModel != null) {`

`handler.accept(cachedModel);`

`} else {`

`threadPool.submit(() -> {`

`SomeModel resultingModel = null;`

`/* 执行昂贵的查询，设置 "resultingModel"... */`

`cache.put(key, resultingModel);`

`handler.accept(resultingModel);`

`}`

`);`

`}`

`}`

现在我们混合了异步和同步风格：处理程序可能在当前线程中执行（在我们返回之前），也可能在另一个线程中执行（在某个任意时间点）。扎尔戈被释放了。我告诉过你这是极其邪恶的，但它看起来似乎无害。如果它会长时间阻塞线程，就在另一个线程中执行代码。如果不会阻塞，就在当前线程中执行。看起来很有帮助。这有什么问题呢？

我的孩子，这就是扎尔戈吞噬你的方式。

问题在于你不知道回调代码会如何表现。处理程序可以做任何它想做的事，包括执行非常耗时的阻塞计算。如果该方法异步解析——就像以前那样——那没问题，因为应用程序的主执行线程会继续运行。现在你改变了这个方法，有时异步解析，有时同步解析，这意味着它现在有时会用非常耗时的阻塞计算来阻塞你的主执行线程。当然，有时不会。这将导致令人抓狂的调试体验。

当开发者推理这段代码时，混合异步和同步执行会创建一个分支，产生两种截然不同但同样可能的情况：一种是代码同步执行，另一种是代码异步执行。应用程序在这两种情况下的行为可能大相径庭，因此开发者必须同时考虑这两种可能性。每次开发者遇到这段代码，都会产生另一个分支，使应用程序的复杂性加倍。在某些情况下，这两个分支几乎相同，但请记住我的话：这些分支的差异会在令人惊讶、不幸且难以调试的情况下出现。

如果你要异步执行，就始终异步执行。如果你要同步执行，就始终同步执行。不要混合同步和异步风格。不要释放扎尔戈！

### 从简单部件构建复杂性

当我们查看 `Function.compose` 和 `Function.andThen` 时，我们用一行代码构建了一个函数，它会返回一个整数，其十进制值为“10”与给定值拼接的结果。所以如果你给它“0”，你会得到“100”。如果你给它“10”，你会得到“1010”。这是一个不简单的逻辑片段，但我们通过将三个方法引用粘合在一起构建了它，然后我们得到了一个执行该行为的单一函数句柄。

就像我们通过委托给其他方法来构建方法一样，我们可以通过委托和包装其他函数来构建函数：找到某种方式来执行核心逻辑，然后在函数前面加上类型转换和参数操作，再在函数后面加上结果转换。

这种方法让我们实现了面向方面编程所追求的目标：你可以将简单的逻辑片段包装在处理程序中，这些处理程序执行额外的处理，而不会用辅助细节弄脏核心实现。这种方法使我们能够继续编写专注、有限、类型特定的方法，这些方法只做一件事并且做得很好。这些方法更容易编写，并且提供更少的容易藏匿 bug 的棘手角落。

最重要的是，这些简单部件更有可能被重用。代码重用是代码的二阶导数¹²；正是通过代码重用，我们不仅加速了开发，还加速了开发速度的加速。旧的缩写是“KISS”：“保持简单，傻瓜”，作为开发者，我们为了速度而 KISS。



### 善用类型与编译器

Scala 和 Java 等语言相较于所有其他后函数式语言的主要优势在于其类型系统。类型系统能捕获你的许多错误。例如，在我们“不要释放 Zalgo”的例子中，我们本可以返回一个 `Future` 类型，而不是委托给回调函数。该类型会表明我们正在异步执行，并在我们尝试同步执行时提醒我们。类似地，如果你从代码库中消除了 `null`，`Optional` 类型就能表示一个值可能存在也可能不存在，从而确保你更明确地处理“可能不存在”的情况。当编译器告诉我们类型不匹配时，我们更容易从复杂类型构建出简单类型：如果没有编译器的帮助，我们很容易在 `andThen` 实例的链条中迷失方向。

编译器还有其他方式可以帮助你。例如，如果你将一个变量声明为 `final`，编译器会确保你在使用它之前已经为其赋值。这对于确保变量在进入 `if/else` 块时被赋值一次（且仅一次）非常有用。以下是该代码的一个示例：

`final Function<String,Integer> converter;`

`if(str == null || str.isEmpty()) {`

`converter = s -> 0;`

`} else if(str.matches("\\d+")) {`

`converter = Integer::parseInt;`

`} else {`

`Function<String,String> eliminateNondigits = s -> {`

`return s.replaceAll("[^\\d]", "");`

`};`

`Function<String,String> defaultString = s -> {`

`if(s.isEmpty()) {`

`return "0";`

`} else {`

`return s;`

`}`

`};`

`converter = eliminateNondigits`

`.andThen(defaultString);`

`.andThen(Integer::parseInt);`

`}`

在这个例子中，编译器确保我们在离开 `if/else` 链之前已经设置了 `converter`。无论我们有多少种情况，也无论每种情况下的代码可能变得多么复杂，我们都知道每一种情况都会设置一个 `converter`。

类型系统是函数式程序员手中的一个强大工具，因为它允许你强制执行系统的约束，并推理这些约束是如何应用的。请尽可能充分利用类型系统，并避免绕过它。

脚注 1

这个奇怪的事实提供了一个相当烦人、学究气但又有用的面试问题：“请用有效的 Java 7 语法编写一个闭包。”

2

我们将在下面“Lambda 应始终是线程安全的”一节中解释为什么 `final` 变量是必要的。

3

Lambda 函数的参数数量上限实际上是没有限制的。如果你在正常开发过程中发现了这个上限，那你肯定做错了什么。

4

这是 Java 明显不是函数式语言的地方之一：函数类型仍然通过接口来传达，而不是内置于语言本身。

5

有些人认为 lambda 中的可变参数是不必要的，尝试使用它们表明你做错了什么。如果你想了解可变参数 lambda 的优点和缺点，可以看看 JavaScript 的 `arguments` 对象以及直接使用它的地方。

6

如果你是那种懂 OCaml 的人，你可能刚刚发现我在教学上撒了谎。对于其他人来说，发现我如何对你撒谎的任务留给读者作为练习。

7

正如我们将在清单 2-15 中看到的，混淆编译器的最佳方法是使用重载方法，尤其是当你将重载方法与继承结合使用时。除此之外，Oracle SDK 中的类型推断实际上相当不错。

8

如果你期望 Java 允许你重写像 `+` 和 `-` 这样的运算符，那你将会非常失望。就目前情况而言，这根本不可能通过 Java 社区流程。不过，你可能想看看 Java-OO `javac` 插件：[`http://amelentev.github.io/java-oo/`](http://amelentev.github.io/java-oo/)

9

即，显式 lambda 与方法引用之间的区别。有关方法引用的信息，请参阅下一节。

10

请参阅下面的“不要使用 Null”。

11

请参阅 FunJava 项目的 `Functions` 类：它提供了一个 `compose` 方法，该方法接受一个 `Function` 和一个 `Consumer`，并返回一个 `Consumer`。

12

有关更多信息，请参见 [`​blog.​enfranchisedmind​.​com/​2007/​09/​use-vrs-reuse-or-the-second-derivitive-of-programming/​`](http://blog.enfranchisedmind.com/2007/09/use-vrs-reuse-or-the-second-derivitive-of-programming/) 和 [`​blog.​enfranchisedmind​.​com/​2007/​09/​development-acceleration-the-second-derivative-of-functionality/​`](http://blog.enfranchisedmind.com/2007/09/development-acceleration-the-second-derivative-of-functionality/)。

