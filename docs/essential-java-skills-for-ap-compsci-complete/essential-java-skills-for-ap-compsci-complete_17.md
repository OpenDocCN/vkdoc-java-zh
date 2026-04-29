# 17. 变量

计算机拥有所有的内存，所以我们得能够存储一些东西，对吧？

要在我们的程序中做到这一点，我们使用**变量**，但每个变量只能处理特定类型的值，即**字面量**。

使用变量需要我们（最终）定义三件事。

## 变量的基本要素

首先，我们需要确定要存储的值的类型。这就是**值类型**，我们已经遇到过几种，比如整数、浮点数、字符串等等。

其次，我们需要为每个变量赋予一个唯一的**名称**。变量名有一些规则。它们必须以字母、`$` 或 `_` 字符开头。它们区分大小写。它们不能是 Java 中的**保留字**。

除了这些规则，还有一些建议。首先，你应该使用**驼峰命名法**，这意味着你将单词连接起来，第一个单词的首字母小写，后续每个单词的首字母大写。所以，我的名字 `Doug Winnie` 会变成 `dougWinnie`。

第三，我们需要为变量**赋值**。该值必须与变量的类型匹配，例如，将一个整数值赋给一个整数变量。我们使用赋值运算符（即等号 `=`）来赋值。

赋值总是从右向左进行。右边的任何值都会被赋给我们左边定义的变量容器。

一旦我们有了一个带有值的变量，我们就可以像使用任何字面量一样使用它。

## 代码示例

下一页的代码展示了在多种情况下使用多种类型变量的示例。

该代码也可在以下 GitHub 仓库中找到：

[`https://github.com/Apress/essential-java-AP-CompSci`](https://github.com/Apress/essential-java-AP-CompSci)

```
public class Main {
public static void main(String[] args) {
// I: 定义一个变量
int quantity;
double price;
String name;
char initial;
// II: 使用赋值运算符为变量赋值
quantity = 5;
price = 2.50;
name = "Doug";
initial = 'W';
// III: 输出变量
System.out.printf("%s %s. bought %d items priced at %f each\n",name,initial,quantity,price);
/* 输出
Doug W. bought 5 items priced at 2.500000 each
*/
// IV: 更新变量的值
quantity = 10;
price = 1.99;
name = "Mike";
initial = 'J';
// V: 输出更新后的变量
System.out.printf("%s %s. bought %d items priced at $%.2f each\n",name,initial,quantity,price);
/* 输出
Mike J. bought 10 items priced at $1.99 each
*/
// VI: 在一行内初始化并赋值
int myNumber = 5;
// VII: 将一个变量赋值给另一个变量
int myValue = myNumber;
}
}
列表 17-1
使用变量
```

