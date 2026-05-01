# 5. 增强的 Switch

在本章中，你将学习

*   什么是预览特性

*   什么是 `switch` 语句和 `switch` 表达式

*   对传统 `switch` 的增强

*   关于 `switch` 的新语法

*   关于使用新 `switch` 的规则

*   关于 `yield` 语句



## 什么是预览特性？

Java SE 平台的预览特性是一种完全规范且完整实现的语言或虚拟机特性，它在某个 JDK 功能版本中可用，但尚未成为 JDK 的永久特性。提供预览特性是为了征求开发者的反馈。开发者应在真实用例中尝试这些特性。根据反馈，预览特性可能会在未来的版本中成为永久特性（标准特性），可能经过修改也可能保持不变，或者可能被完全移除。预览特性不应用于生产环境。有关预览特性的更多详细信息，请参阅 [`https://openjdk.java.net/jeps/12`](https://openjdk.java.net/jeps/12) 上的 JEP 12。

预览特性不向后兼容。你无法使用 Java 13 运行时运行包含 Java 12 预览特性的类文件。

由于预览特性不应用于生产环境，因此在编译器和运行时中默认不启用。你必须对编译器和运行时使用 `--enable-preview` 选项才能使用预览特性。当你使用 `--enable-preview` 选项编译源代码时，还需要使用 `--release` 或 `-source` 选项。要在 JDK 13 中编译包含预览特性的源代码，你可以使用：

```
javac --enable-preview --release 13 
```

如果你在编译包含预览特性的源代码时未使用 `--enable-preview` 选项，则会收到类似如下的编译时错误：

```
C:\Java13Revealed\src\jdojo.enhancedswitch\classes\com\jdojo\enhancedswitch\Test.java:9: 错误: switch 规则是预览特性，默认情况下已禁用。
case 10->str =  "ten";
(请使用 --enable-preview 来启用 switch 规则)
1 个错误
```

如果你在编译时启用了预览特性，编译器会打印提示信息，建议你使用 `-Xlint:preview` 选项进行编译，以便查看警告。

```
注意: C:\Java13Revealed\src\jdojo.enhancedswitch\classes\com\jdojo\enhancedswitch\Test.java 使用了预览语言特性。
注意: 请使用 -Xlint:preview 重新编译以获取详细信息。
```

以下是在编译器中同时使用 `--enable-preview` 和 `-Xlint:preview` 选项时得到的结果，它会针对预览特性的每次使用打印一条警告。

```
C:\Java13Revealed\src\jdojo.enhancedswitch\classes\com\jdojo\enhancedswitch\Test.java:9: 警告: [preview] switch 规则是预览特性，可能会在未来的版本中被移除。
case 10->str =  "ten";
...
4 个警告
```

要运行包含预览特性的已编译类，你需要在 `java` 命令中指定 `--enable-preview` 选项：

```
java --enable-preview 
```

如果你想使用 JShell 探索预览特性，需要使用 `--enable-preview` 选项启动它：

```
C:\Java13Revealed>jshell --enable-preview
|  欢迎使用 JShell -- 版本 13
|  输入 /help intro 以了解介绍
jshell>
```

## 示例程序

本章中的所有示例都在 `jdojo.enhancedswitch` 模块中，如清单 5-1 所示。

```
// module-info.java
module jdojo.enhancedswitch {
exports com.jdojo.enhancedswitch;
}
清单 5-1
名为 jdojo.enhancedswitch 的模块
```

## 背景

Java 语言自诞生之初就拥有 `switch` 语句。它被用作控制流语句。`switch` 语句的语法和语义是从 C/C++ 复制而来的，它们具有以下令人烦恼且容易出错的特点：

*   `switch` 标签使用贯穿语义。在大多数情况下，你只想执行与匹配标签关联的语句，而不执行其他标签。由于贯穿语义，你最终不得不在每个标签中使用 `break` 语句，这既令人烦恼又容易出错。

*   如果你想为多个 `switch` 标签执行同一组语句，必须使用贯穿语义定义多个 case 标签。换句话说，不允许在 case 标签中指定多个常量——一个 case 标签必须有且只有一个常量。

*   所有 switch 标签都位于同一个作用域中，这意味着你不能为不同的标签声明相同的变量。

*   它被设计为仅作为语句使用，而不能作为表达式。语句表示一个动作，而表达式则计算出一个值。

存在这些问题的 `switch` 语句已经存在了 24 年。为什么我们现在需要解决它们？是不是太晚了？答案是既对也不对。开发者或许可以忍受它直到 Java 生命周期的结束。然而，有一个更重要的特性，称为模式匹配，它促使了这些问题的解决。模式匹配目前还不是 Java 的一部分；你可以在 [`https://openjdk.java.net/jeps/305`](https://openjdk.java.net/jeps/305) 上的 JEP 305 中跟踪其进展。为了使模式匹配能够工作，Java 中的 `switch` 必须进行彻底改革。随着新的 Java 每 6 个月发布一个版本，增强的 `switch` 已在 Java 12 中作为预览特性提供，并在 Java 13 中提供了改进版本，而最终目标——模式匹配——仍在开发中。

我假设你知道如何使用传统的 `switch` 语句。在接下来的章节中，我将通过简单的代码片段逐一介绍这些问题，然后再解释增强的 `switch` 特性。

### 贯穿语义

以下代码片段包含一个简单的 `switch` 语句：

```
int count = 2;
// 更多代码...
switch (count) {
case 1:
System.out.println("One");
break;
case 2:
System.out.println("Two");
break;
case 3:
System.out.println("Three");
break;
default:
System.out.println("Out-of-range");
}
```

你有一个名为 `count` 的 `int` 变量。当 `count` 的值分别为 1、2 或 3 时，`switch` 语句会打印 `"One"`、`"Two"` 或 `"Three"`。否则，它会打印 `"Out-of-range"`。请注意每个 `case` 标签中 `break` 语句的使用。你需要它是因为默认情况下 `switch` 语句使用贯穿语义。如果没有 `break` 语句，对于 `count` 值 1，所有四个 switch 标签（三个 `case` 标签和一个 `default` 标签）都将被执行，而这并非你的本意。

### 每个 Case 标签一个常量

假设给你一个英文字母，你需要打印它是元音还是辅音。以下是实现此功能的代码：

```
char c = 'w';
// 更多代码...
switch(Character.toLowerCase(c)) {
case 'a':
case 'e':
case 'i':
case 'o':
case 'u':
System.out.println("Vowel");
break;
default:
System.out.println("Consonant");
}
```

请注意使用贯穿语义的五个 `case` 标签。对于元音，你有五个 `case` 常量。因为每个 `case` 标签只能使用一个常量，所以你不得不使用五个单独的 `case` 标签。如果能够编写一个像 `"case 'a', 'e', 'i', 'o', 'u':"` 这样的 `case` 标签，岂不是更好？



### Switch 块的单一作用域

`switch` 块定义了一个作用域，所有 switch 标签都在该作用域内执行。这意味着你不能在多个 `case` 标签中定义同名变量。请看以下代码片段：

```
char c = 'Z';
// 此处有更多代码...
switch(Character.toLowerCase(c)) {
case 'a':
case 'e':
case 'i':
case 'o':
case 'u':
char upperChar = Character.toUpperCase(c);
System.out.println("Vowel: " + upperChar);
break;
default:
char upperChar = Character.toUpperCase(c); // 编译时错误
System.out.println("Consonant: " + upperChar);
}
```

这段代码无法编译。`upperChar` 变量的两次声明都位于同一个作用域内，即 `switch` 块的作用域。编译器会报错，指出 `default` 标签内存在重复的 `upperChar` 变量声明。有一种方法可以解决这个错误。一对花括号（`{}`）可以定义一个新的作用域。你可以将 `case` 标签和 `default` 标签的代码用花括号括起来来解决这个问题。以下是修改后的代码，它可以正常编译：

```
char c = 'Z';
// 此处有更多代码...
switch(Character.toLowerCase(c)) {
case 'a':
case 'e':
case 'i':
case 'o':
case 'u': {
char upperChar = Character.toUpperCase(c);
System.out.println("Vowel: " + upperChar);
break;
}
default: {
char upperChar = Character.toUpperCase(c); // 编译通过
System.out.println("Consonant: " + upperChar);
}
}
```

### Switch 曾是一种语句

过去你只能将 `switch` 用作语句。为了模拟 `switch` 作为表达式的行为，你需要在 `switch` 语句外部定义一个变量，然后在每个 `switch` 标签中为其赋值。示例如下：

```
int count = 2;
// 此处有更多代码...
// 在此处声明 desc，稍后在每个 switch 标签中为其赋值
String desc;
switch (count) {
case 1:
desc = "One";
break;
case 2:
desc = "Two";
break;
case 3:
desc = "Three";
break;
default:
desc = "Out-of-range";
}
System.out.println(desc);
```

这种方法虽然可行，但代码冗长且是一种变通方案，并非对表达式的直接支持。如果能像下面这样将 `switch` 用作表达式，那就更好了：

```
int count = 2;
// 此处有更多代码...
// 在此处声明 desc，并使用 switch 表达式为其赋值
String desc = switch (count) {
case 1: yield "One";
case 2: yield "Two";
case 3: yield "Three";
default: yield "Out-of-range";
};
```

稍后我将解释使用 `yield` 语句的新语法。

## 增强的 Switch

现在你已经了解了传统 `switch` 的缺点，是时候看看 `switch` 令人兴奋的新特性了，这些特性解决了上述缺点。对 `switch` 的更改分为两类：

*   对传统 `switch` 的增强

*   新的 `switch` 语法

新的 `switch` 语法提供了传统 `switch` 语句的功能，并且还有更多。我将首先解释对传统 `switch` 的增强，然后详细介绍新的 `switch` 语法和语义。

### 对传统 Switch 的增强

Java 一直保持向后兼容。对语言设计者来说，为传统 `switch` 添加破坏性更改是不可行的。预览特性为你提供了使用传统 `switch` 的新方法，同时不会破坏你的旧代码。以下是对传统 `switch` 的更改：

*   你可以在一个 `case` 标签中使用多个常量。

*   你可以将 `switch` 用作表达式。

以前，每个常量必须使用一个 `case` 标签。新的 `switch` 语法允许你在一个 `case` 标签中使用逗号分隔的常量列表。以下代码片段重写了之前将字母匹配为元音和辅音的例子：

```
char c = 'w';
// 此处有更多代码...
switch(Character.toLowerCase(c)) {
case 'a', 'e', 'i', 'o', 'u': /* 一个 case 标签中包含五个常量 */
System.out.println("Vowel");
break;
default:
System.out.println("Consonant");
}
```

请注意这段代码与之前使用传统 `switch` 的代码之间的区别。这次，你将五个 `case` 标签合并成了一个。你仍然需要使用 `break` 语句，因为传统的 `switch` 语句仍然使用默认的穿透语义。

预览特性允许你将传统 `switch` 用作表达式。表达式会计算出一个值（或产生一个值）。现在你可以编写如下代码：

```
String str = switch(selector) {
case label1: ...
case label2: ...
}; /* 必须以分号结尾 */
```

注意 `switch` 表达式末尾使用了分号。`switch` 表达式是 `str` 变量声明的一部分，因此整个变量声明必须以分号结尾。可以将之前的变量声明想象成如下形式：

```
String str = ;
```

传统 `switch` 只允许你在 `switch` 标签中使用语句。预览特性引入了一个新的 `yield` 语句。其语法为：

```
yield expression;
```

`yield` 语句会计算其 `expression`，并将控制权转移给外层的 `switch` 表达式。该 `expression` 的值将成为 `switch` 表达式的值。



### 提示

`yield` 语句只能在 `switch` 表达式内部使用。你不能在 `switch` 表达式内部使用 `break` 语句。

你可以将之前根据整数计算描述的示例重写如下——这次使用 `switch` 表达式：

```
int count = 2;
// 此处省略更多代码...
// 在此处声明 desc 并使用 switch 表达式为其赋值
String desc = switch (count) {
case 1: yield "One";
case 2: yield "Two";
case 3: yield "Three";
default: yield "Out-of-range";
};
```

这是一个简单的示例，每个 `switch` 标签只包含一个 `yield` 语句。你可以在一个 switch 标签中包含多个语句。但是，每个 `switch` 标签必须包含一个 `yield` 语句，该语句将通过正常的执行路径来表示 `switch` 表达式的值。通常，你不会从表达式中打印内容。这里我们仅出于演示目的，展示在 `switch` 表达式的 `switch` 标签中可以包含多个语句。以下代码片段在执行 `"case 1:"` 时会打印一条消息：

```
String desc = switch (count) {
case 1:
System.out.println("Executing case 1:");
yield "One";
case 2: yield "Two";
case 3: yield "Three";
default: yield "Out-of-range";
};
```

请记住，你不能在 `switch` 表达式内部使用 `break` 语句。`yield` 语句会结束一个 `switch` 表达式。在这个语句中，当 `count` 为 1 时，会打印一条消息，然后 `yield` 语句通过生成 `"One"` 来结束 `switch` 表达式，并且 `"case 1:"` 下方的其他 `case` 标签不会被执行。

这里有一个挑战给你。让我将前面代码片段中的两个语句交换如下。现在，`yield` 语句在前，`System.out.println()` 在后。以下代码片段能编译通过吗？

```
int count = 2;
// 此处省略更多代码...
String desc = switch (count) {
case 1:
yield "One";
System.out.println("Executing case 1:");
case 2: yield "Two";
case 3: yield "Three";
default: yield "Out-of-range";
};
```

如果你的答案是不能，那么你是正确的。`yield` 语句结束了 `switch` 表达式，使得 `System.out.println()` 语句不可达。Java 不允许存在不可达语句。此代码片段会产生以下编译时错误：

```
error: unreachable statement
System.out.println("Executing case 1:");
1 error
```

清单 5-2 包含一个完整的程序，用于演示增强型传统 `switch` 的用法。

```
// TraditionalSwitch.java
package com.jdojo.enhancedswitch;
public class TraditionalSwitch {
public static void main(String[] args) {
whatLetter('a');
whatLetter('W');
whatLetter('5');
for(int i = 1; i <= 4; i++) {
String desc = getDesc(i);
System.out.printf("%d:%s%n", i, desc);
}
}
public static void whatLetter(char c) {
// 使用传统 switch，一个 case 中包含多个常量
switch(Character.toLowerCase(c)) {
case 'a', 'e', 'i', 'o', 'u':
System.out.println("Vowel: " + c);
break;
default:
if (Character.isLetter(c)) {
System.out.println("Consonant: " + c);
} else {
System.out.println("Not-a-Letter: " + c);
}
}
}
public static String getDesc(int count) {
// 将传统 switch 用作表达式
return switch (count) {
case 1: yield "One";
case 2: yield "Two";
case 3: yield "Three";
default: yield "Out-of-range";
};
}
}
Vowel: a
Consonant: W
Not-a-Letter: 5
1:One
2:Two
3:Three
4:Out-of-range
清单 5-2
演示传统 Switch 增强功能的程序
```

### 新的 Switch 语法

新的 `switch` 保留了传统 `switch` 的大部分语法，除了用于分隔 `switch` 标签及其代码的字符。它使用箭头（`->`）代替冒号（`:`）。传统的 `"case label:"` 变成了 `"case label->"`。这是新 `switch` 唯一的语法差异。在本节中，我将解释几个语义（工作原理）上的差异。

通常，我需要区分我谈论的是哪个 `switch`——新的还是旧的。我将旧的 `switch` 称为传统 `switch`、`switch-with-colon` 或使用 `"case label:"` 的 `switch`。我将新的称为新 `switch`、`switch-with-arrow` 或使用 `"case label->"` 的 `switch`。Java 语言规范将与传统 `switch` 关联的 `switch` 块称为 *switch 标记语句组*，将与新 `switch` 关联的 `switch` 块称为 *switch 标记规则*。无论你使用哪个名称来指代新 `switch`，其语法如下：

```
switch(selector-expresion) {
case label1[, label2, label3...] -> [expression|block|throw-statement]
case label4[, label5, label6...] -> [expression|block|throw-statement]
...
default -> [expression|block|throw-statement]
}
```

该语法允许在 `case` 标签中使用一个或多个常量。与 `switch` 标签关联的代码仅限于以下之一：表达式、块语句和 `throw` 语句。稍后我将解释此规则背后的原理。

让我们看看用于处理新 `switch` 的规则，并附上每条规则的示例：

*   新 `switch` 默认没有穿透（fall-through）。这意味着，你不再需要使用 `break` 语句来停止执行匹配的 `switch` 标签之后的 switch 标签。

*   作为匹配的 `switch` 标签的一部分，只能执行一个“事物”。这个“事物”可以是表达式、块语句或 `throw` 语句。在传统 `switch` 中，允许一组语句，这会导致作用域问题，即整个 `switch` 块在一个作用域内执行。使用这些约束，如果你有多个语句要执行，则必须使用块语句，这将为 `switch` 标签创建一个新的作用域。这允许你拥有与传统 `switch` 相同的功能，但代码更不容易出错。

*   新的 `switch` 可以用作语句或表达式。

以下代码片段使用新的 `switch` 语句重写了之前使用传统 `switch` 语句的示例：

```
int count = 2;
// 此处省略更多代码...
switch (count) {
case 1-> System.out.println("One");
case 2-> System.out.println("Two");
case 3-> System.out.println("Three");
default-> System.out.println("Out-of-range");
}
```

注意每个 switch 标签中使用了 `System.out.println()`。我不是说过每个 `switch` 标签可以有一个表达式、一个块语句或一个 `throw` 语句吗？我确实说过。在 Java 中，方法调用（本例中为 `System.out.println()`）是一个表达式。当你在方法调用后添加分号时，它就变成了一个表达式语句。以下是关于在带有箭头的 switch 标签中使用表达式的详细规则：

*   在 `switch` 语句中，*表达式* 必须是 *表达式语句* —— 一个可以通过添加分号转换为语句的表达式。

*   在 `switch` 表达式中，*表达式* 可以是任何有效的 Java 表达式。

以下代码片段使用 `switch` 表达式重写了之前的示例：

```
int count = 2;
// 此处省略更多代码...
String desc = switch (count) {
case 1-> "One";
case 2-> "Two";
case 3-> "Three";
default->"Out-of-range";
};
System.out.println(desc);
```



这次，`"One"`、`"Two"`等是表达式。你不能通过添加分号将它们转换为表达式语句。当`switch`表达式（`count`）匹配某个`case`标签时，对应的表达式（`"One"`、`"Two"`等）就成为`switch`表达式的值。

如果必须使用某些逻辑来计算`switch`标签中的值，则需要使用`yield`语句将值作为`switch`表达式的值返回。请考虑以下使用`switch`表达式计算值的代码片段：

```
char c = 'W';
// 此处省略更多代码
String desc = switch(Character.toLowerCase(c)) {
case 'a', 'e', 'i', 'o', 'u'-> "Vowel";
default-> {
if (Character.isLetter(c)) {
yield "Consonant";
} else {
yield "Not-a-Letter";
}
}
};
System.out.println(desc);
```

当字符是元音时，`case`标签使用简单表达式`"Vowel"`。`default`标签使用块语句，通过逻辑判断它是辅音还是非字母。注意`if`和`else`块中`yield`语句的使用。`default`标签并未使用非常复杂的逻辑。你可以将其替换为如下表达式：

```
String desc = switch(Character.toLowerCase(c)) {
case 'a', 'e', 'i', 'o', 'u'-> "Vowel";
default-> Character.isLetter(c)?"Consonant":"Not-a-Letter";
};
```

请注意，之前的`switch`表达式没有使用任何`yield`语句。请考虑以下使用新`switch`语句的代码片段：

```
char c = 'W';
// 此处省略更多代码
switch(Character.toLowerCase(c)) {
case 'a', 'e', 'i', 'o', 'u'-> {
char upperChar = Character.toUpperCase(c);
System.out.println("Vowel: " + upperChar);
}
default-> {
if (Character.isLetter(c)) {
char upperChar = Character.toUpperCase(c);
System.out.println("Consonant: " + upperChar);
} else {
System.out.println("Not-a-Letter: " + c);
}
}
}
```

这段代码使用了一个`case`标签和一个`default`标签。两者都包含多条语句，因此你必须在每个标签中使用块语句（`{}`）。注意，你在两个标签的代码中都使用了相同的局部变量名`upperChar`。在传统的`switch`中，像这样在`switch`块中声明重复变量也是可行的，但你必须记得使用块语句；而新的`switch`强制你使用块语句。

清单 5-3 包含一个完整的程序，用于演示新`switch`及其语法。我使用新`switch`重写了清单 5-2 中的程序，该程序原本使用传统`switch`。

```
// NewSwitch.java
package com.jdojo.enhancedswitch;
public class NewSwitch {
public static void main(String[] args) {
whatLetter('a');
whatLetter('W');
whatLetter('5');
for(int i = 1; i 
System.out.println("Vowel: " + c);
default-> {
if (Character.isLetter(c)) {
System.out.println("Consonant: " + c);
} else {
System.out.println("Not-a-Letter: " + c);
}
}
}
}
public static String getDesc(int count) {
// 将新 switch 用作表达式
return switch (count) {
case 1-> "One";
case 2-> "Two";
case 3-> "Three";
default-> "Out-of-range";
};
}
}
Vowel: a
Consonant: W
Not-a-Letter: 5
1:One
2:Two
3:Three
4:Out-of-range
清单 5-3
演示新 Switch 语法的程序
```

## 新 Switch 会取代旧 Switch 吗

一旦新`switch`成为 Java SE 的标准特性（可能在 Java SE 14 中），当它满足你的需求时，建议你使用它来替代传统`switch`。然而，它并不能完全取代传统的`switch`语句。请记住，传统的`switch`默认提供穿透（fall-through）行为，并且你有办法（使用`break`语句）覆盖默认行为。请考虑以下利用传统`switch`语句穿透特性的简单示例：

```
int count = 2;
// 此处省略更多代码...
switch (count) {
case 1:
System.out.print("One ");
case 2:
System.out.print("Two ");
case 3:
System.out.println("Three.");
break;
default: System.out.println("Over-My-Head");
}
```

如果`count`的值为 1，它会打印`"One Two Three"`；为 2 时，打印`"Two Three"`；为 3 时，打印`"Three"`；对于其他任何值，打印`"Over-My-Head"`。由于传统`switch`语句的穿透特性，实现此逻辑非常简单。使用新`switch`语法没有直接的方法来实现此逻辑，因为新`switch`不提供穿透特性。你明白了——尽可能使用新语法，在需要穿透时使用传统`switch`。

我很好奇，想更深入地了解新`switch`特性以及编译器如何处理它们。因此，我尝试反编译本章中所有使用新`switch`语法编写的示例。你可以使用[`www.javadecompilers.com`](http://www.javadecompilers.com)反编译你的 Java 代码。当我反编译我的代码时，我没有看到任何新`switch`语法。我发现，在所有情况下，使用新`switch`语法的代码都被转换成了传统`switch`。尽管新`switch`在幕后使用了旧`switch`，但作为开发者，使用新语法能获得很多好处。你可以得到更简洁、更不易出错的代码，以及一个重要的新特性——将`switch`用作表达式！



## 分支标签的穷尽性

switch 表达式（而非 switch 语句）的 switch 标签必须穷尽。也就是说，你必须在 switch 表达式中为 `selector-expression` 的每个可能值提供一个 `switch` 标签。请看以下代码片段：

```
int count = 2;
String desc = switch(count) {
case 1-> "One";
case 2-> "Two";
case 3-> "Three";
};
```

这段代码会产生以下编译时错误：

```
error: the switch expression does not cover all possible input values
String desc = switch(count) {
1 error
```

出现此错误的原因是，三个 `case` 标签只覆盖了 `count` 的三个可能值。而这里的 `count` 是 `int` 类型，其可能值有数百万个。通常，你会使用一个 `default` 标签来覆盖 `count` 的其他值——仅仅是为了让编译器满意：

```
int count = 2;
String desc = switch(count) {
case 1-> "One";
case 2-> "Two";
case 3-> "Three";
default-> "Out-of-range";
};
```

假设你的 switch 表达式基于一个枚举，并且 `case` 标签覆盖了所有枚举常量。之后，你向枚举中添加了一个新的枚举常量，并且只编译了枚举定义，而没有编译在 switch 表达式中使用该枚举的代码。如果 switch 表达式使用新添加的枚举常量值执行，会发生什么？请记住，这里的讨论针对的是 switch 表达式，而非 switch 语句。如果你在 switch 语句中使用了这个枚举，那么使用新的枚举常量时，switch 语句会静默地不做任何操作，因为新的枚举常量没有被任何 switch 标签覆盖。然而，使用 switch 表达式则不同。通常，你希望在 switch 表达式中为每个枚举常量计算一个值。由于你不知道未来可能添加哪些枚举常量，你可以在 switch 表达式中添加一个 `default` 标签，该标签通常会抛出一个异常或返回一个默认值。

当你将 `switch` 用作表达式时（无论是新的 `switch` 还是旧的 `switch`），编译器会为你提供帮助。当一个 switch 表达式基于枚举，并且所有枚举常量都被 case 标签覆盖时，编译器会添加一个包含以下代码的 `default` 标签：

```
default-> throw new IncompatibleClassChangeError();
```

让我们通过一个示例来理解。清单 5-4 包含一个名为 `TrafficLight` 的枚举声明。它包含两个常量 `GREEN` 和 `RED`。

```
// TrafficLight.java
package com.jdojo.enhancedswitch;
public enum TrafficLight {GREEN, RED}
清单 5-4
一个包含两个常量 GREEN 和 RED 的 TrafficLight 枚举
```

清单 5-5 包含 `Drive` 类的代码。其 `changeLight()` 方法使用了一个 switch 表达式，覆盖了 `TrafficLight` 枚举的两个常量。`main()` 方法调用了 `changeLight()` 方法——对 `TrafficLight` 枚举中的每个枚举常量各调用一次。输出显示了预期的结果。

```
// Drive.java
package com.jdojo.enhancedswitch;
public class Drive {
public static void main(String[] args) {
for (TrafficLight light : TrafficLight.values()) {
changeLight(light);
}
}
public static void changeLight(TrafficLight light) {
String decision = switch (light) {
case GREEN-> "GO";
case RED-> "STOP";
};
System.out.println(light + ": " + decision);
}
}
GREEN: GO
RED: STOP
清单 5-5
一个在 switch 表达式中使用 TrafficLight 枚举常量的 Drive 类
```

编译器静默地为你的 switch 表达式添加了一个安全防护。它将你的 switch 表达式修改为以下形式：

```
String decision = switch (light) {
case GREEN-> "GO";
case RED-> "STOP";
default-> throw new IncompatibleClassChangeError();
};
```

编译器添加的这个隐藏 `default` 标签的目的很快就会明了。现在，让我们向 `TrafficLight` 枚举中添加一个新常量 `AMBER`。清单 5-6 包含了修改后的枚举声明。

```
// TrafficLight.java
package com.jdojo.enhancedswitch;
public enum TrafficLight {GREEN, RED, AMBER}
清单 5-6
一个包含三个常量 GREEN、RED 和 AMBER 的 TrafficLight 枚举
```

重新编译 `TrafficLight` 枚举，但不要重新编译 `Drive` 类。然后，重新运行 `Drive` 类，会产生以下输出：

```
GREEN: GO
RED: STOP
Exception in thread "main" java.lang.IncompatibleClassChangeError
at jdojo.enhancedswitch/com.jdojo.enhancedswitch.Drive.changeLight(Drive.java:12)
at jdojo.enhancedswitch/com.jdojo.enhancedswitch.Drive.main(Drive.java:7)
```

输出中的错误信息清楚地表明，枚举定义在编译时和运行时之间发生了变化。开发者需要在 `Drive` 类的 `changeLight()` 方法中的 switch 表达式里处理新的枚举常量 `AMBER`。如果你在基于枚举的 switch 表达式中添加了 `default` 标签，编译器就不会再为你添加 `default` 标签。

如果你在没有添加 `default` 标签的情况下重新编译 `Drive` 类，你会得到以下编译时错误：

```
C:\Java13Revealed\src\jdojo.enhancedswitch\classes\com\jdojo\enhancedswitch\Drive.java:12: error: the switch expression does not cover all possible input values
String decision = switch (light) {
1 error
```

你需要在 `Drive` 类的 `changeLight()` 方法中的 switch 表达式里处理新的枚举常量 `AMBER`，才能消除这个错误。

在 Java 中，表达式必须求值为一个值或抛出一个异常。这条规则同样适用于 switch 表达式，编译器会检查每个 switch 标签是否产生一个值或抛出一个异常。如果某个 switch 标签可能不产生值，就会发生编译时错误。请考虑清单 5-7 中的代码。你能找出 `SwitchExpressionCompletion` 类中的编译时错误吗？

```
// SwitchExpressionCompletion.java
package com.jdojo.enhancedswitch;
import java.time.LocalDate;
public class SwitchExpressionCompletion {
public static void main(String[] args) {
int count = 2;
String desc = switch(count) {
case 1-> "One";
case 2-> throw new IllegalArgumentException("Don't like two!");
case 3-> {
if (LocalDate.now().getYear() == 2019) {
yield "Three";
}
// 编译时错误
}
default-> {
System.out.println("你在说什么！");
// 编译时错误
}
};
}
}
清单 5-7
一个 SwitchExpressionCompletion 类
```

编译 `SwitchExpressionCompletion` 类会产生以下错误：

```
C:\Java13Revealed\src\jdojo.enhancedswitch\classes\com\jdojo\enhancedswitch\SwitchExpressionCompletion.java:18: error: switch rule completes without providing a value
}
(switch rules in switch expressions must either provide a value or throw)
C:\Java13Revealed\src\jdojo.enhancedswitch\classes\com\jdojo\enhancedswitch\SwitchExpressionCompletion.java:22: error: switch rule completes without providing a value
}
(switch rules in switch expressions must either provide a value or throw)
```

错误指向了 `"case 3->"` 和 `"default->"` 标签的结尾。编译器断定这两个标签在所有可能的执行路径中都没有产生值或抛出异常。当当前年份不是 2019 年时，`"case 3->"` 不会产生值或抛出异常。`"default->"` 不会产生值或抛出异常。当你的 switch 表达式中存在这些逻辑错误时，编译器会帮助你。

这意味着你不能在 switch 表达式中使用控制转移语句，例如 `break`、`continue` 和 `return`。你可以使用 `break` 语句跳出 switch 语句。请记住以下两条规则：

*   switch 语句只使用 `break` 语句，从不使用 `yield` 语句。

*   switch 表达式只使用 `yield` 语句，从不使用 `break` 语句。



## Switch 表达式是一种多态表达式

Java 中的多态表达式是一种其类型取决于上下文的表达式。因此，同一个多态表达式在不同上下文中可以呈现不同的类型。`switch` 表达式就是一种多态表达式。如果其目标类型已知，则其类型即为该目标类型。如果其目标类型未知，则其类型通过组合每个 `switch` 标签的类型来计算得出。请看以下代码片段：

```
int count = 2;
double value = switch (count) {
case 1-> 10;
case 2-> 20.4;
default-> 1.5F;
};
```

这个 `switch` 表达式的目标类型是 `double`，也就是 `value` 变量的类型。因此，该 switch 表达式的类型是 `double`。编译器还会检查每个 `switch` 标签产生的值类型是否与 `switch` 表达式的目标类型赋值兼容。在此例中，`switch` 标签分别产生了 `int` (10)、`double` (20.4) 和 `float` (1.5F) 类型的值。`int`、`double` 和 `float` 这三种类型都与目标类型 `double` 赋值兼容。

请看以下代码片段。它能编译通过吗？

```
int count = 2;
int value = switch (count) {
case 1-> 10;
case 2-> 20.4;
default-> 1.5F;
};
```

这段代码无法编译通过。`switch` 表达式的目标类型是 `int`，也就是 `value` 变量的声明类型。而两个 `switch` 标签产生的 `double` 和 `float` 类型的值与 `int` 类型不赋值兼容。这就是这段代码无法编译的原因。

请看以下代码片段。它能编译通过吗？

```
int count = 2;
var value = switch (count) {
case 1-> 10;
case 2-> 20.4;
default-> 1.5F;
};
```

这段代码可以正常编译。注意这里使用 `var` 来声明 `value` 变量。这次，`switch` 表达式的目标类型是未知的。编译器必须通过查看每个 `switch` 标签来计算 `switch` 表达式的类型。`switch` 标签产生了 `int`、`double` 和 `float` 类型的值。编译器使用类型拓宽规则，计算出 `double` 作为 `switch` 表达式的类型。请记住，由于使用了 `var`，编译器必须推断 `value` 变量的类型。该类型将被推断为 `double`，这与计算出的 `switch` 表达式类型相同。

请看以下代码片段。它能编译通过吗？

```
int count = 1;
var value = switch (count) {
case 1-> 10;
case 2-> 20.4;
default-> "what";
};
```

如果你猜测这段代码无法编译，那你就错了。它可以正常编译。现在你可能好奇这个 `switch` 表达式计算出的类型是什么。编译器使用以下步骤：

*   switch 标签产生了三种类型的值：`int`、`double` 和 `String`。

*   这些类型是混合的——两种基本类型和一种引用类型。

*   基本类型被提升为引用类型 `Integer` 和 `Double`。

*   编译器查找 `Integer`、`Double` 和 `String` 之间的公共类型。编译器计算出一个类型，该类型是这三种类型所有公共类型的并集。这三种类型都实现了四个接口：`Serializable`、`Comparable`、`Constable` 和 `ConstantDesc`。计算出的 `switch` 表达式类型是这四个接口的组合。

那么，前面代码片段中 `switch` 表达式的类型是什么呢？如下所示：

```
Serializable&Comparable&Constable&ConstantDesc>&Constable&ConstantDesc
```

这是一种不可表示类型。也就是说，只有编译器可以在字节码中使用这种类型。你不能在源代码中使用（或表示）这种类型。要亲自验证这一点，你可以使用 JShell 运行这段代码。确保使用 `--enable-preview` 选项启动 JShell，并将反馈模式设置为 `verbose`，如下所示：

```
C:\Java13Revealed>jshell --enable-preview
|  Welcome to JShell -- Version 13
|  For an introduction type: /help intro
jshell> /set feedback verbose
|  Feedback mode: verbose
jshell> int count = 2;
count ==> 2
|  created variable count : int
jshell> var value = switch (count) {
...> case 1-> 10;
...> case 2-> 20.4;
...> default-> "what";
...> };
value ==> 20.4
|  created variable value : Serializable&Comparable&java.lang.constant.Constable&java.lang.constant.ConstantDesc>&java.lang.constant.Constable&java.lang.constant.ConstantDesc
jshell> /exit
|  Goodbye
C:\Java13Revealed>
```

## yield 语句

新的 `switch` 表达式引入了一个名为 `yield` 语句的新语句。将 `yield` 作为语句对现有代码有一些影响。如果你有一个使用非限定语法调用的 `yield()` 方法，它可能会破坏现有代码。让我们通过一个例子来理解这条规则。考虑以下 `yield` 语句：

```
yield(10);
```

在 `yield` 成为语句之前，这绝对是一个方法调用。也就是说，它会通过传递值 10 来调用一个名为 `yield` 的方法。

引入 `switch` 表达式之后，该语句将被解释为一个产生值 10 的 `yield` 语句，并且它只允许在 `switch` 表达式中使用。请注意，在 10 周围使用括号并不会使其成为方法调用，因为现在 `yield` 是一个语句，而 (10) 是其值。以下所有变体都将被视为 `yield` 语句——空格和括号没有区别：

```
yield 10;
yield (10);
yield(10);
```

如果你现有的代码中有非限定的 `yield()` 方法调用，你需要修改它们，使其成为限定的方法调用。也就是说，如果你现有的代码中有 `yield(x)`，你需要将其更改为 `xxx.yield(x)`，其中 `xxx` 可以是 `this`、类名或引用变量名，具体取决于上下文。

随着 `switch` 表达式的引入，`yield` 成为一个*受限标识符*。它在某些上下文中的使用受到限制。你不能将 `yield` 用作类型名称，例如类名或接口名。但是，你可以将 `yield` 用作方法名和变量名。为了向后兼容，允许将 `yield` 用作方法，因为 Java 中的 `Thread` 类包含一个 `yield` 方法，并且在极少数情况下，你可能已经将你的方法命名为 `yield`。

清单 5-8 包含一个完整的程序，展示了如何将 yield 用作方法/变量名以及如何调用此类方法。你必须使用 `NoYieldAsTypeName.yield()` 语法来调用 `yield()` 方法。

```
// NoYieldAsTypeName.java
package com.jdojo.enhancedswitch;
public class NoYieldAsTypeName {
public static void main(String[] args) {
// 必须使用类名限定才能调用 yield() 方法
NoYieldAsTypeName.yield();
NoYieldAsTypeName.yield(100);
// 以下非限定调用会产生编译时错误
//yield();
//yield(10);
int yield = 200;
System.out.println("变量名 yield 是允许的: " + yield);
}
public static void yield() {
System.out.println("方法名 yield 是允许的。");
}
public static void yield(int x) {
System.out.println("方法名 yield 是允许的: " + x);
}
}
方法名 yield 是允许的。
方法名 yield 是允许的: 100
变量名 yield 是允许的: 200
清单 5-8
将 yield 用作方法和变量名
```

## 混合使用 Switch 标签语法

声明 `switch` 标签有两种语法——一种使用冒号（`:`），一种使用箭头（`->`）。在一个 `switch` 中，你不能混合使用这两种语法。以下代码片段无法编译：

```
int count = 1;
int value = switch (count) {
case 1-> 10;
case 2: yield 20;
default-> 30;
};
错误：switch 中使用了不同的 case 种类
case 2: yield 20;
1 个错误
```



## 摘要

Java SE 平台的一项预览特性是指一个完整定义且完整实现的语言或虚拟机特性，该特性在某个 JDK 功能发布版中可用，但尚未成为 JDK 的永久特性。提供预览特性是为了征求开发者的反馈。你必须在使用编译器和运行时环境时使用 `--enable-preview` 选项才能使用预览特性。当你使用 `--enable-preview` 选项编译源代码时，还必须使用 `--release` 或 `-source` 选项。你必须在启动 JShell 时使用 `--enable-preview` 选项，才能在 JShell 中使用任何预览特性。

Java 12 将增强型 `switch` 作为一项预览特性引入。该特性在 Java 13 中仍作为预览特性，但有一处更改：Java 12 中带值的 `break` 语句被 Java 13 中的 `yield` 语句所取代。

在 Java 13 中，你有两种 `switch` 风格：传统的 `switch` 和新的 `switch`。在新的 `switch` 中，你使用箭头（`->`）来分隔 switch 标签及其代码，而在旧的 switch 中则使用冒号（`:`）。你不能在同一个 `switch` 中混合使用这两种 switch 标签语法。增强型 `switch` 既可以作为语句使用，也可以作为表达式使用。

新的 `switch` 在编写和调试代码时让开发者的工作稍微轻松了一些。它没有 `default` 穿透——一次只能执行一个 `switch` 标签。与 `switch` 标签关联的代码可以是一个表达式、一个块语句或一个 `throw` 语句。

增强型 `switch`——无论是传统的 `switch` 还是新的 `switch`——都允许在 `case` 标签中使用多个以逗号分隔的 case 常量。

为了支持 `switch` 表达式，Java 13 添加了一个名为 `yield` 语句的新语句。它只能在 `switch` 表达式中使用，以产生一个值。`yield` 语句产生的值将成为 `switch` 表达式的值。在 `switch` 表达式中不允许使用诸如 `break`、`continue` 和 `return` 之类的控制转移语句。

编译器会检查 `switch` 表达式中 `selector-expression` 的所有可能值是否都被 `switch` 标签覆盖。否则，将发生编译时错误。如果 `switch` 表达式基于枚举类型，并且所有枚举常量都被 `switch` 标签覆盖且没有 `default` 标签，则编译器会添加一个抛出 `IncompatibleClassChangeError` 的 `default` 标签。当向枚举中添加新常量，并且使用新的枚举常量执行 switch 表达式时，这个隐式的 `default` 标签有助于你调试代码。隐式的 `default` 标签会提供更好的错误消息，帮助你快速定位问题。

`switch` 表达式是一个多态表达式，其类型取决于上下文。如果 `switch` 表达式有已知的目标类型，则 `switch` 表达式的类型就是该目标类型。如果目标类型未知，则会通过组合所有 `switch` 标签产生的值的类型来计算出一个独立类型。

`yield` 是一个*受限标识符*。你不能将 `yield` 用作类型名称，例如类名或接口名。但是，你可以将 `yield` 用作方法名和变量名。你不能将未限定的 `yield()` 作为方法调用。如果你现有的代码包含未限定的 `yield()` 方法调用，则需要将它们更改为限定的方法调用，以避免编译时错误。

