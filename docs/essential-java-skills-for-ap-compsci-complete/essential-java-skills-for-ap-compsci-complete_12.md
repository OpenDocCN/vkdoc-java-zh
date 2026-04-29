# 12. 输出格式

值是什么以及我们如何显示它是两件不同的事情。我们可以通过格式化程序来调整各种值字面量的显示方式，这些格式化程序会以不同的方式转换和显示值。

为此，我们将使用 `printf()` 方法，该方法包含我们想要显示的信息，但包含特殊的代码，这些代码会改变值在屏幕上的显示方式。

## 小数格式化程序

最基本的情况是处理小数时。有时，我们需要显示的数字位数比实际需要的多。

以下是实现此功能的代码。

```
System.out.println(2.3f);
System.out.printf("%.2f",2.3f);
System.out.println();
System.out.printf("%.4f",2.3f);
System.out.println();
清单 12-1
小数格式化程序
```

其工作原理如下。你从 `println()` 语句开始，然后创建一个包含你想要显示的文本的字符串字面量。

然后，你使用百分号在字面量中创建一个标记。紧接着，你添加格式化代码。

在格式化字符串字面量之后，你跟随一个逗号和要插入到第一个位置的值。你可以在一个字符串格式化程序中插入多个值，只需用逗号分隔它们即可。

清单 12-1 中的示例输出如下：

```
2.3
2.30
2.3000
```

`%.2f` 代码意味着我们提供一个浮点数（带小数的数字），小数部分保留两位数字。这是通过代码中的 `f` 字符定义的。

最后一个示例使用 `%.4f`，它在输出中提供四位小数。

## 千位分隔格式化程序

对于较大的数字，如果使用逗号分隔数字中每千位级别（如果你在世界其他地区，则使用句点），则更容易阅读。

为此，你在格式化程序代码中添加一个逗号。此外，如果你处理的是整数或不带小数的数字，则在格式化程序中使用字符 `d` 而不是 `f`。

```
System.out.println(1000000);
System.out.printf("%,d",1000000);
System.out.println();
清单 12-2
千位分隔格式化程序
```

此示例的输出是：

```

1,000,000
```

## 货币格式化程序

我们可以将其他文本与格式化字符串组合在一起，并将多个格式化程序组合在一起。例如，如果我们想显示价格呢？我们应该显示带有两位小数和千位分隔符的数字。

```
System.out.println(1000.2f);
System.out.printf("$%.2f", 1000.2f);
System.out.println();
System.out.printf("$%,.2f", 1000.2f);
System.out.println();
清单 12-3
货币格式化程序
```

我们在这里组合了多个格式化程序，但也在格式化程序前添加了美元符号。当你使用 `printf()` 语句时，可以在任何字符串字面量中添加你的格式化代码。

此示例输出如下：

```
1000.2
$1000.20
$1,000.20
```



## 间距与对齐格式化器

在处理文本时，你常常需要为特定数值或字符串进行对齐或创建固定大小的空白区域。你可以通过在格式化代码中使用字符 `s` 的字符串格式化器来实现这一点。

请看这个示例。

```
System.out.println("Hello!");
System.out.printf("|%20s|", "Hello!");
System.out.println();
System.out.printf("|%-20s|", "Hello!");
System.out.println();
System.out.printf("|%-20s|", "Hello, this is a long sentence.!");
System.out.println();
System.out.printf("|%-20.20s|", "Hello, this is a long sentence.!");
System.out.println();
清单 12-4
间距与对齐格式化器
```

其输出结果为：

```
Hello!
|              Hello!|
|Hello!              |
|Hello, this is a long sentence.!|
|Hello, this is a lon|
```

添加的竖线（垂直条）是为了更清晰地展示这里发生的情况。

当你使用字符串格式化器时，整个数字值代表该短语在屏幕上允许占用的最小字符数。因此，`%20s` 将在屏幕上占用 20 个字符的空间。

如果你添加一个减号，它将调整对齐方式，使其位于另一侧。在这种情况下，是左对齐。

如果你有一个长字符串，它会完整地打印出来，除非你向格式化器添加一个小数部分。在这种情况下，它会截断任何超过格式化器允许的最大字符串长度的文本。

## 格式化器中的多个项目

你可以在格式化字符串中根据需要添加任意数量的代码；你只需要确保有匹配的值来放入其中。每个值之间用逗号分隔。

```
System.out.printf("%10s $%.2f\n","Apples",1.4f);
System.out.printf("%10s $%.2f\n","Brownies",0.8f);
清单 12-5
格式化器中的多个项目
```

此代码的输出为：

```
Apples $1.40
Brownies $0.80
```

再次强调，你可以插入任意数量的格式化代码，但每个值之后需要用逗号分隔。

```
public class Main {
public static void main(String[] args) {
/* 字符串格式化 */
// 显示一个十进制数
System.out.println(2.3f);
System.out.printf("%.2f",2.3f);
System.out.println();
System.out.printf("%.4f",2.3f);
System.out.println();
/* 输出
2.3
2.30
2.3000
*/
// 显示千位分隔符
System.out.println(1000000);
System.out.printf("%,d",1000000);
System.out.println();
/* 输出

1,000,000
*/
// 显示价格
System.out.println(1000.2f);
System.out.printf("$%.2f", 1000.2f);
System.out.println();
System.out.printf("$%,.2f", 1000.2f);
System.out.println();
/* 输出:
1000.2
$1000.20
$1,000.20
*/
// 显示字符串
System.out.println("Hello!");
System.out.printf("|%20s|", "Hello!");
System.out.println();
System.out.printf("|%-20s|", "Hello!");
System.out.println();
System.out.printf("|%-20s|", "Hello, this is a long sentence.!");
System.out.println();
System.out.printf("|%-20.20s|", "Hello, this is a long sentence.!");
System.out.println();
/* 输出:
Hello!
|              Hello!|
|Hello!              |
|Hello, this is a long sentence.!|
|Hello, this is a lon|
*/
// 在单个字符串中显示多个项目
System.out.printf("%10s $%.2f\n","Apples",1.4f);
System.out.printf("%10s $%.2f\n","Brownies",0.8f);
/* 输出
Apples $1.40
Brownies $0.80
*/
}
}
清单 12-6
冲刺 12 代码
```

