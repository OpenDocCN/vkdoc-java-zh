# 8. Java 字节码中的 Lambda

本章面向那些想要更深入地了解 lambda 实际工作原理的人。这是你的红色药丸与蓝色药丸时刻：你是想生活在一个 Java 拥有 lambda、它们运行良好、一切流畅而精彩的世界里，还是想看看表面之下究竟发生了什么，发现编译器魔法的奥秘？如果你对你所了解和喜爱的 lambda 感到满意，那么请跳过本章，尽情享受吧。如果你需要知道你的代码真正意味着什么，那么请抓紧了：是时候深入探索了。

现实情况是，lambda 是一种幻觉。Java lambda 完全是编译器的构造。编译器将带有 lambda 的 Java 代码转换为更底层语言中的更原始操作。理解这种转换可以帮助你准确理解正在发生的事情，这在调试和优化等情况下非常有用。

然而，在踏入这个新世界之前，有一些必要的注意事项。本章基于 Oracle 的 Java 编译器编写，具体版本是 1.8.0_05。Oracle 完全有可能（尽管可能性极小）在未来的版本中彻底改造其 lambda 实现，而且随着时间推移，肯定会有改进。其他 Java 编译器也可能有非常不同的实现。因此，如果你在家中使用自己的 Java 编译器并注意到一些细微的差异，那正是这种情况。当你深入探究时，必须预料到这类变化。

我们还需要坦率地说明，本章并非对 Java 编译器及其生成的底层字节码语言的全面介绍。该语言有其自身的语义，这些语义既复杂又技术性强。在本章中，我们只是开始接触其中一些语义。我们专注于 lambda 及其功能，因此任何与 lambda 无关的内容都与本次探索无关。

在所有这些警告之后，如果你仍然愿意跟随我，那么你可能知道 Java 代码本身并不会直接编译成由操作系统执行的字节。相反，Java 编译器（以下简称“javac”）会编译成另一种中间形式，由 Java 虚拟机（以下简称“JVM”）执行。这种中间形式称为 Java 字节码。然而，字节码没有 lambda 的概念。它也没有 try-with-resources 块、增强型 for 循环或 Java 中许多其他结构的概念。相反，编译器会将这些 Java 结构转换为字节码中的底层形式。

在本章中，我们将看到编译器如何将 Java 语言中的 lambda 命令转换为 Java 字节码语言中的结构。我们将从熟悉阅读字节码开始，然后继续了解特定类型的 lambda 结构如何转换为字节码。但在讨论 lambda 之前，我们需要了解一些基础知识。



## 你好，字节码

普通人不会去阅读字节码。然而，Java 自带了一个名为 `javap` 的工具，它可以读取类文件并以更易读的代码形式呈现其字节码。还有其他工具也能实现同样的功能，如果你有兴趣更深入地了解字节码，那么你绝对应该看看 `asm` 工具集。不过，`javap` 随 Oracle 的 SDK 一起提供，它提供了我们所需的所有功能，同时保持了简单性，因此我们将在本章的剩余部分使用它。

让我们用一个非常简单的 Java 类及其由 `javap` 呈现的字节码，来初步体验一下 `javap` 和字节码。这个类将有一个什么都不做的 `public static void main` 方法。仅此而已，生成的字节码会是什么样呢？那里生成的内容将是后续所有内容的基础，并让我们有机会了解类文件的基本结构。如清单 8-1 所示。

**清单 8-1\. 一个简单的 Java 类及其字节码**

`1    // Listing1.java，编译后得到 Listing1.class`

`2    public class Listing1 {`

`3      public static void main(String[] args) {}`

`4    }`

`5`

`6    // 在与 Listing1.class 相同的目录下执行 /usr/bin/javap -v -l -p -s -c Listing1 的结果`

`7    Classfile /Users/RCFischer/wkdir/Books/java8/build/classes/production/ch8/Listing1.class`

`8      Last modified Dec 6, 2014; size 367 bytes`

`9      MD5 checksum 008abb234928faafc8a836436ea158db`

`10     Compiled from "Listing1.java"`

`11   public class Listing1`

`12     SourceFile: "Listing1.java"`

`13     minor version: 0`

`14     major version: 52`

`15     flags: ACC_PUBLIC, ACC_SUPER`

`16   Constant pool:`

`17      #1 = Methodref          #3.#17         //  java/lang/Object."<init>":()V`

`18      #2 = Class              #18            //  Listing1`

`19      #3 = Class              #19            //  java/lang/Object`

`20      #4 = Utf8               <init>`

`21      #5 = Utf8               ()V`

`22      #6 = Utf8               Code`

`23      #7 = Utf8               LineNumberTable`

`24      #8 = Utf8               LocalVariableTable`

`25      #9 = Utf8               this`

`26     #10 = Utf8               LListing1;`

`27     #11 = Utf8               main`

`28     #12 = Utf8               ([Ljava/lang/String;)V`

`29     #13 = Utf8               args`

`30     #14 = Utf8               [Ljava/lang/String;`

`31     #15 = Utf8               SourceFile`

`32     #16 = Utf8               Listing1.java`

`33     #17 = NameAndType        #4:#5          //  "<init>":()V`

`34     #18 = Utf8               Listing1`

`35     #19 = Utf8               java/lang/Object`

`36   {`

`37     public Listing1();`

`38       descriptor: ()V`

`39       flags: ACC_PUBLIC`

`40       LineNumberTable:`

`41         line 1: 0`

`42       LocalVariableTable:`

`43         Start  Length  Slot  Name   Signature`

`44             0       5     0  this   LListing1;`

`45       Code:`

`46         stack=1, locals=1, args_size=1`

`47            0: aload_0`

`48            1: invokespecial #1                  // Method java/lang/Object."<init>":()V`

`49            4: return`

`50         LineNumberTable:`

`51           line 1: 0`

`52         LocalVariableTable:`

`53           Start  Length  Slot  Name   Signature`

`54               0       5     0  this   LListing1;`

`55`

`56     public static void main(java.lang.String[]);`

`57       descriptor: ([Ljava/lang/String;)V`

`58       flags: ACC_PUBLIC, ACC_STATIC`

`59       LineNumberTable:`

`60         line 3: 0`

`61       LocalVariableTable:`

`62         Start  Length  Slot  Name   Signature`

`63             0       1     0  args   [Ljava/lang/String;`

`64       Code:`

`65         stack=0, locals=1, args_size=1`

`66            0: return`

`67         LineNumberTable:`

`68           line 3: 0`

`69         LocalVariableTable:`

`70           Start  Length  Slot  Name   Signature`

`71               0       1     0  args   [Ljava/lang/String;`

`72   }`

与原始的 Java 源代码相比，`javap` 的输出结果相当冗长：这恰恰说明了 Java 的表达能力有多强。此时值得注意的是，`javap` 的结果与类文件中的内容并不完全相同：`Listing1.class` 并非相同内容的压缩版本。相反，`javap` 命令为我们提供了类文件中信息的人类可读版本。

让我们一步步来。第一部分是前言（第 7–10 行）：

`Classfile /Users/RCFischer/wkdir/Books/java8/build/classes/production/ch8/Listing1.class`

`Last modified Dec 6, 2014; size 367 bytes`

`MD5 checksum 008abb234928faafc8a836436ea158db`

`Compiled from "Listing1.java"`

这些是由 `javap` 生成的信息，用于向我们提供关于类文件的信息。它有助于记录你所做的事情，并在处理类文件打印输出时帮助你发现愚蠢的错误。第一行是关于文件的文件系统信息；下一行是校验和，这是一种快速判断两个类文件是否相同的方法；最后一行告诉你这个类文件的来源。然而，这些都不是关于类文件本身中的类的信息。为此，我们需要看下面的内容（第 11–15 行）：

`public class Listing1`

`SourceFile: "Listing1.java"`

`minor version: 0`

`major version: 52`

`flags: ACC_PUBLIC, ACC_SUPER`

这些行声明了类。第一行告诉我们，我们正在声明一个类（而不是接口），以及它的名称和权限级别。下一行告诉我们这个类来自哪里，这通常与前言的最后一行是重复的。接下来的两行是版本号，它告诉你这个类是用 Java 字节码版本 52.0 编译的——这是一个对应于 Java 8.0 的魔法值。最后几行告诉你这个类的访问标志是什么：`ACC_PUBLIC` 和 `ACC_SUPER`。`ACC_PUBLIC` 意味着这个类是公开可见的。你可以将 `ACC_SUPER` 理解为意味着该类是在 Java 2 之后编译的：它对所有当代类定义都是强制性的，并且纯粹是为了向后兼容而存在。

紧跟在类声明之后的是常量池。常量池包含了该类使用的所有常量。这个常量池将包含你在 Java 中习惯使用的所有常量，例如字符串和整数值。它还将包含许多你可能不认为是常量的东西，例如类名和方法名。在我们的例子中，常量池看起来像这样（第 16–35 行）：

`Constant pool:`

`#1 = Methodref          #3.#17         //  java/lang/Object."<init>":()V`

`#2 = Class              #18            //  Listing1`

`#3 = Class              #19            //  java/lang/Object`

`#4 = Utf8               <init>`

`#5 = Utf8               ()V`

`#6 = Utf8               Code`

`#7 = Utf8               LineNumberTable`

`#8 = Utf8               LocalVariableTable`

`#9 = Utf8               this`

`#10 = Utf8               LListing1;`

`#11 = Utf8               main`

`#12 = Utf8               ([Ljava/lang/String;)V`

`#13 = Utf8               args`

`#14 = Utf8               [Ljava/lang/String;`

`#15 = Utf8               SourceFile`

`#16 = Utf8               Listing1.java`

`#17 = NameAndType        #4:#5          //  "<init>":()V`

`#18 = Utf8               Listing1`

`#19 = Utf8               java/lang/Object`



常量池条目中的数字是字节码其余部分引用每个常量的方式。例如，如果你想引用“Listing1”这个类，那么你会引用常量 #3。常量池的大部分内容都是使用 utf8 编码的字符串。常量 #2 和 #3 代表 Java 类名：请注意，它们实现的类名是由一个字符串常量给出的。因此，解读方式是：常量 #2 是名称由常量 #18 处的字符串给出的类。方便的是，`javap` 会提供一个注释来解释对应的值是什么，这样你就不必自己去做这项工作。常量 #17 的类型是 `NameAndType`，它表示方法的名称和签名。与类名类似，具体的名称和类型信息由常量池中的其他字符串表示。在这种情况下，名称是 `"<init>"`，这是构造函数的魔法字节码名称。该方法的签名是它不接受任何参数（即空括号 `()`）并返回 void（`V`）。池中的最后一个常量是一个方法引用，它将是一个 `Class` 与一个 `NameAndType` 的组合。这个组合将唯一定义一个要调用的方法。常量 #1 的值定义了 `Object` 默认构造函数的方法引用——当我们转到类文件的下一元素时，就会明白为什么。

类的主体定义在花括号内，就像在 Java 语言中一样。方法名类似于 Java 声明：在我们的例子中，我们有 `public static void main(java.lang.String[]);` 和 `public Listing1();`。字节码中有两个方法，但我们的 Java 代码中只声明了一个方法。第二个方法是什么？答案是 Java 要求每个类都有一个构造函数。如果你没有构造函数，编译器会为你提供一个：即一个不接受任何参数并允许所有字段值为默认值的默认构造函数。由于我们没有声明构造函数，编译器给了我们一个，这就是 `"public Listing1();."` 的含义。不过，在查看其实现之前，让我们先看看我们确实声明的方法。我们在 Java 代码中声明的 main 方法是空的，但字节码中却有不少内容（第 56–71 行）：

`public static void main(java.lang.String[]);`

`descriptor: ([Ljava/lang/String;)V`

`flags: ACC_PUBLIC, ACC_STATIC`

`LineNumberTable:`

`line 3: 0`

`LocalVariableTable:`

`Start  Length  Slot  Name   Signature`

`0       1     0  args   [Ljava/lang/String;`

`Code:`

`stack=0, locals=1, args_size=1`

`0: return`

`LineNumberTable:`

`line 3: 0`

`LocalVariableTable:`

`Start  Length  Slot  Name   Signature`

`0       1     0  args   [Ljava/lang/String;`

方法声明之后是描述符。这是方法签名的官方字节码声明。声明的参数位于括号内：在这种情况下，它是一个类型为 `java.lang.String` 的数组。字节码中的对象类型由字符“L”和字符“;”界定。包由正斜杠而不是句点分隔。因此，`java.lang.String` 变成了 `Ljava.lang.String;`。数组由左方括号“[”指定。由于 main 方法接受一个字符串类型的数组，其参数类型因此是 `([Ljava.lang.String;)`。我们再次看到末尾悬着的“V”代表 void 返回类型。这就构成了整个描述符，也就是方法的签名。用编程语言极客的话来说，方法的签名描述了方法的“形态”：签名描述了方法如何作为一个整体融入程序，就像拼图块的形状决定了该块如何拼入拼图一样。

下一行又是标志。在这种情况下，它们对应于 Java 中的 `public` 和 `static` 关键字。接下来，我们有一个该方法的 LineNumberTable，它在下面的 Code 部分中实际上重复了。这个表是调试器知道哪行代码对应哪个操作的方式。在这种情况下，它表示源文件的第 3 行是声明该方法的地方。LineNumberTable 之后是 LocalVariableTable，它存储局部变量，并且在 Code 部分中也重复了。我们的 Java 代码没有声明任何局部变量，但字节码中有一个：名称 `args` 给出了提示。方法的参数也算作局部变量：它们恰好是在调用方法时设置的局部变量。

最后一部分是“Code”部分。这是定义方法主体的地方。数字是偏移量，它告诉你方法在字节码中的长度。在我们的例子中，该方法唯一要做的事情就是返回，所以索引 0 处只有一条指令：`return`。`locals` 值是该方法使用的局部变量数量，`args_size` 是参数的大小。剩下要解释的就是 `stack=0`。

Java 字节码是一种基于栈的语言。存在更复杂的基于栈的语言（例如 Factor），所以不要因为你使用 Java 字节码的经验而对所有基于栈的语言形成看法。尽管如此，Java 字节码是一种基于栈的语言这一事实意味着每条指令都在栈上操作。你在字节码中做的一切要么是非操作（“nop”），要么是将元素推入栈，要么是将元素弹出。因此，Java 中的每个行为（例如，引用一个变量）都对应一些栈操作序列（例如，将变量的值推入栈）。可能会有副作用，最显著的是在流程控制中：例如，当你调用一个方法时，你正在从栈中弹出元素来调用该方法，而当方法返回时，它可能会将一个元素推回栈。

在字节码中，每个方法都必须声明其特定栈的最大深度。这允许运行时优化栈的分配：它可以在进入方法时一次性分配一个内存块，而不是在元素被推入栈时可能需要分配更多内存。在我们的 main 方法的情况下，我们不需要栈上的任何空间，因为我们只是要返回 void。因此，我们的字节码通过设置 `stack=0` 告诉运行时我们需要一个大小为 0 的栈。这就是我们的 main 方法的全部内容。然而，构造函数（第 37–49 行）则稍微复杂一些：

`public Listing1();`

`descriptor: ()V`

`flags: ACC_PUBLIC`

`LineNumberTable:`

`line 1: 0`

`LocalVariableTable:`

`Start  Length  Slot  Name   Signature`

`0       5     0  this   LListing1;`

`Code:`

`stack=1, locals=1, args_size=1`

`0: aload_0`

`1: invokespecial #1                  // Method java/lang/Object."<init>":()V`

`4: return`

首先要注意的是，构造函数不是一个静态方法：没有设置 `ACC_STATIC` 标志。构造函数是一个实例方法，在类的实例上调用。由于它是一个实例方法，它可以访问 `this` 变量，该变量在 `LocalVariableTable` 中声明。它的类型是 `LListing1;`——开头的两个“L”字符不是笔误。同样，类型名称在“L”和“;”之间声明，这导致了我们最初的重复。`this` 的存在再次给了我们 `locals=1` 和 `args_size=1` 的值，尽管我们的 `descriptor` 中没有声明任何参数。同样有趣的是，Java 不希望你将构造函数视为有返回类型，但就字节码方法描述符而言，构造函数返回 void。



我们的构造器的最大栈深度为 1，这是因为构造器将要执行一些有趣的操作。在 Java 语言中，构造器的第一行可以是调用`this()`或`super()`（可能带有参数）。这些调用会委托给另一个构造器。在字节码中，构造器的第一行必须是对另一个构造器的委托：这在 Java 语言中是可选的，但在字节码中是必需的。`Listing1`的父类是`Object`类，因此字节码将我们的构造器委托给了`Object`类的父构造器。字节码通过加载`this`局部变量，然后在其上调用父类构造器来实现这一点。指令`aload_0`将局部变量表中偏移量为 0 的局部变量压入栈：也就是我们的`this`变量。此时，栈顶元素就是我们的`this`变量。构造器是一种特殊的存在，因此在字节码中通过`invokespecial`来调用它们。你需要告诉运行时你正在调用哪个构造器，因此通过方法引用来调用`invokespecial`：在我们的代码中，方法引用是`Object`类的构造器。完成所有这些操作后，我们执行`return`。（请记住，根据字节码，构造器是一个返回类型为`void`的方法。）

此时，我并不期望你觉得自己已经成了字节码专家。不过，当你查看清单 8-1 时，应该不会再感到不知所措或迷失方向了。你应该对如何阅读一个基本的类有了大致的了解。掌握了这项技能，我们就可以继续学习如何阅读支持 lambda 表达式的字节码了。

## 内联 Lambda 定义与 Lambda 提升

最后一种 lambda 表达式是带有内联定义的 lambda。这里我们讨论的不是对现有方法的引用，而是直接内联定义一个全新的功能单元。你可以考虑如下代码：

`public Supplier<String> getSupplier() {`

`return () -> "Hello, World!";`

`}`

在这种情况下，编译器会为你创建一个包含该实现的方法。这被称为“合成方法”。这将是一个静态方法，不接受任何参数，并返回字符串“Hello, World!”。创建完成后，编译器就将内联 lambda 的问题简化为我们之前见过的静态方法引用问题。这是一个相当巧妙的解决方案，而且实现起来最终也非常简单——这意味着对我们来说它很无趣。

更有趣的情况是当混合了变量时。让我们创建一个方法，它调用一个实例方法（需要访问`this`）以及作用域内的一个局部变量。毕竟，这些 lambda 表达式是闭包：它们封闭了自身的作用域。这正是它们的职责所在！编译器将如何处理这种复杂性？答案在清单 8-5 中。

清单 8-5. 内联 Lambda 的 Java 代码及其字节码（节选）

`1    // Listing5.java，编译后得到 Listing5.class`
`2    import java.util.function.*;`
`3`
`4    public class Listing5 {`
`5`
`6      public String provideMessage(String message) {`
`7        return message;`
`8      }`
`9`
`10     public Supplier<String> getSupplier(String message) {`
`11       return () -> this.provideMessage(message);`
`12     }`
`13`
`14   }`
`15`
`16   // 在与 Listing5.class 相同的目录下执行 /usr/bin/javap -v -l -p -s -c Listing5 的节选结果`
`17   public class Listing5`
`18   BootstrapMethods:`
`19       0: #25 invokestatic java/lang/invoke/LambdaMetafactory.metafactory:(`
`Ljava/lang/invoke/MethodHandles$Lookup;`
`Ljava/lang/String;`
`Ljava/lang/invoke/MethodType;`
`Ljava/lang/invoke/MethodType;`
`Ljava/lang/invoke/MethodHandle;`
`Ljava/lang/invoke/MethodType;`
`)Ljava/lang/invoke/CallSite;`
`20         Method arguments:`
`21           #27 ()Ljava/lang/Object;`
`22           #28 invokespecial Listing5.lambda$getSupplier$0:(Ljava/lang/String;)Ljava/lang/String;`
`23           #29 ()Ljava/lang/String;`
`24   {`
`25     public Listing5();`
`26`
`27     public java.lang.String provideMessage(java.lang.String);`
`28       Code:`
`29         stack=1, locals=2, args_size=2`
`30            0: aload_1`
`31            1: areturn`
`32`
`33     public java.util.function.Supplier<java.lang.String> getSupplier(java.lang.String);`
`34       Code:`
`35         stack=2, locals=2, args_size=2`
`36            0: aload_0`
`37            1: aload_1`
`38            2: invokedynamic #2,  0              // InvokeDynamic`
`// #0:get:(LListing5;Ljava/lang/String;)Ljava/util/function/Supplier;`
`39            7: areturn`
`40`
`41     private java.lang.String lambda$getSupplier$0(java.lang.String);`
`42       flags: ACC_PRIVATE, ACC_SYNTHETIC`
`43       Code:`
`44         stack=2, locals=2, args_size=2`
`45            0: aload_0`
`46            1: aload_1`
`47            2: invokevirtual #3                  // Method provideMessage:(Ljava/lang/String;)Ljava/lang/String;`
`48            5: areturn`
`49   }`

现在情况变得相当奇怪了。我们看到了一个带有奇怪名字的合成方法示例：`lambda$getSupplier$0(java.lang.String);`。这是一个实例方法，它接受一个 String，并使用该实例在该字符串上调用`provideMessage`：它加载`aload_0`（即`this`），然后加载`aload_1`（即参数），接着执行`invokevirtual`。我们的`getSupplier`方法做了类似的事情：它加载`message`和`this`，然后执行我们熟悉的`invokedynamic`调用来生成 supplier。

请注意，现在我们在`invokedynamic`的签名中传递了两个参数：所有需要在 lambda 实现内部引用的变量都成为了`invokedynamic`的参数。这被称为“lambda 提升”：我们将绑定变量从 lambda 中提升出来，并将它们作为实例化 lambda 的参数。我们执行了 lambda 提升的信号是使用`invokespecial`作为方法调用方式：我们之前看到它用于表示构造器，但现在我们用它来向引导方法发出信号，表明我们必须使用提升后的参数来构造一个新实例。

需要认识到的重要一点是，声明一个使用变量的 lambda 在很大程度上类似于声明一个引用这些变量的新类型：我们最终会实例化一个新实例，该实例将持有这些变量，并在执行时引用这些成员字段。因此，内联定义 lambda 和定义自己的匿名内部类在性能特性上几乎完全相同。根据你所处的场景，选择更自然的方式即可。

## 结论

Lambda 表达式非常强大，但在底层，它们在概念上很简单：Java SDK 有一个方法，它会接收你的 lambda 并返回一个实例，该实例的类型就是你的 lambda 所针对的目标类型。它使用`invokedynamic`字节码指令仅执行一次绑定，在首次执行之后，就等同于直接实例化接口实现。如果你内联定义 lambda，那么你会得到一个合成方法，但其他方面几乎完全相同。字节码中有很多噪音，并且需要仔细的簿记工作才能让这一切正确运行，但实现的概念实际上相当简单。最重要的是，与解决相同问题的其他方式相比，这种实现中没有任何内在因素会使 lambda 变得缓慢或沉重：你永远不可能通过将 lambda 重写为其他形式来获得显著的性能提升。实现比那要聪明得多！



