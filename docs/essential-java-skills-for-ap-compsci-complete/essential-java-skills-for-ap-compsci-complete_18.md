# 18. 数学运算！

虽然在编程和 Java 中你可以用数学做很多事情，但我们将在这里介绍基本内容，从基本的算术运算符开始。

## 基本运算符

Java 中使用的基本运算符包括加法、减法、乘法、除法和取模。

除了取模之外，这些运算符都相当不言自明。取模本质上是除法后的余数。当你将两个数相除时，余数就是取模的结果。

## 运算顺序

计算基于代数的运算顺序：

*   首先完成分组（基于括号）。

*   乘法类函数：
    *   乘法

    *   除法

    *   取模

*   加法类函数：
    *   加法

    *   减法

计算从左到右进行。

## 字符串拼接

虽然你不能“相加”字符串，但你可以将它们彼此组合。这被称为**拼接**，你使用加法符号来实现。

你可以将多个字面量和变量类型拼接在一起。当你这样做时，它们都会被转换为字符串。



## 代码示例

下一页展示了整数和小数类型数值的基本运算符、运算顺序以及字符串拼接的示例。

以下代码也可在 GitHub 仓库中找到：

[`https://github.com/Apress/essential-java-AP-CompSci`](https://github.com/Apress/essential-java-AP-CompSci)

```
public class Main {
public static void main(String[] args) {
// I: 定义并设置示例值
int a = 2, b = 5, c = 100;
float foo = 3.0f, bar = 10.5f, baz = 85.25f;
// 整数示例
//
// II: 加法
int z = a + b; // 2 + 5 = 7
System.out.printf("The sum is: %d\n",z);
// 输出: The sum is: 7
// III: 减法
int y = b - c; // 5 - 100 = -95
System.out.printf("The difference is: %d\n",y);
// 输出: The difference is: -95
// IV: 乘法
int w = a * c; // 2 * 100 = 200
System.out.printf("The product is: %d\n", w);
// 输出: The product is: 200
// V: 除法
int v = c / b; // 100 / 5 = 20
System.out.printf("The quotient is: %d\n", v);
// 输出: The quotient is: 20
// VI: 取模
int u = b % a; // 5 % 2 = 1
System.out.printf("The remainder is: %d\n", u);
// 输出: The remainder is: 1
// VII: 整数除法
int t = b / a; // (int)5 / 2 = 2
System.out.printf("The integer quotient is: %d\n", t);
// 输出: The integer quotient is: 2
// 浮点数示例
//
// VIII: 浮点数乘法
float qux = foo * bar; // 3.0f * 10.5f = 31.5f
System.out.printf("The float product is: %f\n", qux);
// 输出: The float product is: 31.500000
// IX: 浮点数除法
float quux = baz / bar; // 85.25f / 10.5f = 8.119047f
System.out.printf("The float quotient is: %f\n", quux);
// 输出: The float quotient is: 8.119047
// X: 整数转浮点数除法
float quuz = b / a;
System.out.printf("The quotient is: %f\n", quuz);
// 生成一个浮点数，但基于整数商
// 输出: The quotient is: 2.000000
// XI: 运算顺序
int corge = 2 + 5 * 12 + 6 / 2;
System.out.printf("The result is: %d\n", corge);
// 2 + 60 + 6 / 2
// 2 + 60 + 3
// 62 + 3
// 65
// 输出: The result is: 65
int grault = (2 + 5) * (12 + 6) / 2;
System.out.printf("The result is: %d\n", grault);
// 7 * (12 + 6) / 2
// 7 * 18 / 2
// 126 / 2
// 63
// 输出: The result is: 63
// XII: 字符串拼接
String garply = "Doug" + "Winnie";
System.out.println(garply);
// 输出: DougWinnie
garply = "Doug" + ' ' + "Winnie";
System.out.println(garply);
// 输出: Doug Winnie
// XIII: 拼接字面量
System.out.println("The product of " + a + " and " + b + " is " + a*b + ".");
// 输出: The product of 2 and 5 is 10.
}
}
列表 18-1
数学运算符
```

