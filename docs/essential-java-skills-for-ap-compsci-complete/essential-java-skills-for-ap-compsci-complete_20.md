# 20. 管理类型

在处理变量和方法时，你需要应对不同的值类型。有时，你需要处理一个整数，但手头却是一个浮点数。或者，你有一个需要变成整数的双精度浮点数。如何管理类型并强制它们转换为其他类型，是编程语言工作中的常见部分。

## 求值中的类型混合

当混合类型时，混合求值中的所有值在执行任何操作之前都会被转换为双精度浮点数。

例如：

```
int a = 11;
int b = 4;
double x = 11;
double y = 4;
System.out.print(a / b);
System.out.print(", ");
System.out.print(x / y);
System.out.print(", ");
System.out.print(a / y);
```

这段代码片段的结果是：

```
2, 2.75, 2.75
```

这是因为对于第三次求值，我们组合了一个整数和一个双精度浮点数。当 Java 看到混合类型时，它会将整数转换为双精度浮点数，然后完成操作。

## 数字转字符串

大多数数值在字符串中进行拼接时，无需特殊处理就会转换为字符串。只需将它们“加”到另一个字符串字面量上，这些值就会拼接起来。不过，最好使用 `printf()` 方法，这样你可以控制和调整这些值在屏幕上的显示方式。

```
String a = "Value: " + b;
```

但是，你不能直接获取一个数值并将其简单地赋值给一个字符串变量。这是不允许的。相反，你需要访问每个值类型特有的方法。

例如：

```
Double.toString(value);
Float.toString(value);
Integer.toString(value);
```

当你将值传入这些方法中的每一个时，它会返回该值的字符串版本，你可以将其赋值给一个字符串变量。

你会注意到这些语句中的值类型是大写的。这有其原因，但我们将在讲到类时再介绍。

## 字符串转数字

反向操作称为解析。你需要让一个方法读取或解析一个字符串，并尝试查看其中是否包含数值。

不过，这存在一些限制。例如，字符串只能包含小数点。它不能包含货币符号或千位分隔符。

解析是通过一个同样附加在值类型名称上的方法来完成的：

```
Double.parseDouble(string);
Float.parseFloat(string);
Integer.parseInt(string);
```

注意方法名称之间的差异。

## 类型转换

瞧！兔子变成了一束花！

在 Java 中，施法并非那么神秘。事实上，它是其核心部分。类型转换是指你将一个值当作另一个值来对待。

本质上，你是在尝试强制一个值去适应一个不同的容器。然而，有时类型转换会失败，所以使用时要小心。

请看这个例子：

```
double foo = 3.00;
int bar = foo;
```

第二行会产生一个错误。尽管 `foo` 的值从技术上讲是一个整数（因为它没有小数部分），但它并非“严格意义上的”整数。因此赋值失败。

我们可以通过在想要视为不同类型的值前面放置一个类型转换来强制实现这一点。只需用括号括起一个值类型，并将其放在该值之前：

```
int baz = (int) foo;
```

这可以毫无问题地工作，因为类型转换将 foo 视为整数，从而使其与类型匹配。

## 类型转换错误

然而，类型转换存在一些陷阱。你需要小心放置它们的位置，因为它可能会完全改变求值的结果：

```
int value1 = 2, value2 = 5;
float q;
q = value2 / value1;
System.out.println(q); // 2.0
```

这个例子获取两个整数值，尝试将它们相除，并将结果赋值给一个浮点变量。

由于它们是整数，当它们相除时，任何小数部分都会被直接丢弃。因此，结果 `2` 被转换为浮点数（这不需要任何特殊处理）并变成 `2.0`。

但是，如果我们用一组括号将它们括起来并进行类型转换，我们会得到什么？

```
q = (float)(value2 / value1);
System.out.println(q); // 2.0
```

我们得到相同的结果，因为整数除法仍然在进行。两个数字相除，丢弃小数部分，然后*那个*值被转换为浮点数。

没什么帮助，对吧？

那么，要解决这个问题，我们需要在除法发生*之前*对每个值进行类型转换：

```
q = (float)value2 / (float)value1;
System.out.println(q); // 2.5
```

## 代码示例

下一页展示了可以在程序中使用的值操作和类型转换的示例。

以下 GitHub 仓库中也提供了此代码：

[`https://github.com/Apress/essential-java-AP-CompSci`](https://github.com/Apress/essential-java-AP-CompSci)

```
public class Main {
public static void main(String[] args) {
// 注意：此代码包含错误，将无法编译
// 为示例设置变量
int a = 5;
double b = 3.14;
String x = "3.95";
String y = "185";
// I: 字符串拼接中的数字
String text = "The value of a is " + a;
System.out.println(text);
// II: 直接类型转换错误
String val = b; // 无法转换类型
// III: 使用基础值类型的 toString() 方法进行转换
String rate = Double.toString(b);
System.out.println(rate);
// IV: 将字符串解析（转换）为数字
double piValue = Double.parseDouble(x);
int qty = Integer.parseInt(y);
double price = Double.parseDouble("$2.50"); // 生成错误
// V: 类型转换值
double foo = 3.00;
int bar = foo;          // 无类型转换，生成错误
int baz = (int) foo;    // 有类型转换
// VI: 类型转换问题
int value1 = 2, value2 = 5;
float quotient;
//// 无类型转换：
quotient = value2 / value1;
System.out.println(quotient); // 2.0
//// 不正确的类型转换：
quotient = (float)(value2 / value1);
System.out.println(quotient); // 2.0
//// 正确的类型转换：
quotient = (float)value2 / (float)value1;
System.out.println(quotient); // 2.5
}
}
代码清单 20-1
常见值操作代码
```

