# 19. 数学方法

基本的数学运算符只能帮你到这里。在 Java 中，你还可以使用更多方法来进行更高级的运算，比如代数、三角函数、统计学、微积分等。

## 使用简单方法

最基本的是处理数字并对其进行舍入。处理数值的小数部分有三种方式。

第一种是直接将其完全去掉。这被称为求数字的**向下取整**。任何小数部分都会被直接移除，即使它是 .99999。

第二种是取任何小数部分并将其提升到下一个整数，这被称为数字的**向上取整**。任何小数部分都会将数字移动到下一个整数，即使它是 .00001。

最后一种是传统的**四舍五入**。任何 .5 或更高的值都会进位到下一个整数。任何低于 .5 的值都会舍去。

要执行这些操作，我们需要调用一个方法。

**方法**是一段命名的代码，我们只需调用其名称（后跟一对括号）即可执行。

有些方法会返回一个值，就像我们前面提到的舍入示例。当一个方法返回值时，返回的值实质上会替换掉被调用时的方法名称。

不过，为了让大多数方法能够工作，你需要给它提供一些东西来处理。我们将值放在方法名后面的一对括号中，从而将其提供给方法。

以下示例使用了 `ceil()` 方法将任何小数值提升到下一个整数：

```
double a = 1.25;
double b = Math.ceil(a);
System.out.println(b);
// 输出: 2.0
```

变量 `a` 被提供给 `Math.ceil()` 方法，该方法随后执行向上取整操作。它返回一个值 `2.0`，这个值替换了方法调用。然后该值被赋给 `b`。

你可以在方法调用中放入任何能求值为所需参数的内容，包括数学运算符或其他变量。你甚至可以在一个方法调用中调用另一个方法。

## 多参数方法

有些方法，比如求一个基数的指数幂，需要向该方法提供两个值。你可以通过用逗号分隔的方式，向需要两个值的方法提供它们：

```
double x = 2.0;
double y = 8.0;
double z = Math.pow(x,y);
System.out.println(z);
// 输出: 256
```

## 方法中的非法值类型

有些方法需要特定类型的值，否则它们将无法工作。当 IntelliJ 遇到潜在的非法的值时，它会通知你。如果在构建和运行程序时遇到它，你会得到一个错误：

```
float foo = 5.5f;
float bar = Math.floor(foo);
// 错误，floor() 需要 double 类型
```

## 数学常量

在 Java 中，有一些特殊的值可以通过名称来引用，它们代表恒定不变的特殊无理数。最常见的是 π 和 *e*。

这些常量通过使用 `Math.E` 和 `Math.PI` 来表示。

## 代码示例

下一页展示了可以在你的程序中使用的常用数学方法示例。

以下代码也可在 GitHub 仓库中找到：

[`https://github.com/Apress/essential-java-AP-CompSci`](https://github.com/Apress/essential-java-AP-CompSci)

```
public class Main {
public static void main(String[] args) {
// I: 数字方法
double a = Math.floor(1.99); // a = 1
double b = Math.ceil(1.01);  // b = 2
double c = Math.round(1.49); // c = 1
System.out.printf("Rounding >> floor %f >> ceil %f >> round %f \n", a, b, c);
// 输出: Rounding >> floor 1.000000 >> ceil 2.000000 >> round 1.000000
// II: 代数方法
double d = Math.pow(2,8);    // d = 256.0
double e = Math.sqrt(256.0); // e = 128.0
double f = Math.cbrt(27);    // f = 3.0
double g = Math.PI;          // 表示圆周率 pi
double h = Math.E;           // 表示欧拉数 e
double i = Math.log(50);     // 返回*自然*对数
double j = Math.log10(50);   // 返回常用（以 10 为底）对数
double k = Math.abs(-1);     // k = 1.0
System.out.printf("%f, %f, %f, %f, %f, %f, %f, %f\n",d,e,f,g,h,i,j,k);
// 输出: 256.000000, 16.000000, 3.000000, 3.141593, 2.718282, 3.912023, 1.698970, 1.000000
// III: 三角函数方法
double l = Math.sin(2);      // 返回正弦值（使用弧度）
double m = Math.cos(2);      // 返回余弦值（使用弧度）
double n = Math.tan(2);      // 返回正切值（使用弧度）
double o = Math.asin(1);     // 返回反正弦值（使用弧度）
double p = Math.acos(1);     // 返回反余弦值（使用弧度）
double q = Math.atan(2);     // 返回反正切值（使用弧度）
double r = Math.toRadians(90.0);            // 返回弧度
double s = Math.toDegrees(Math.PI * 2);     // 返回角度
double t = Math.cos(Math.toRadians(Math.PI * 2)); // 嵌套方法调用
System.out.printf("%f, %f, %f, %f, %f, %f, %f, %f, %f\n",l,m,n,o,p,q,r,s,t);
// 输出: 0.909297, -0.416147, -2.185040, 1.570796, 0.000000, 1.107149, 1.570796, 360.000000, 0.993993
// IV: 比较方法
double max = Math.max(1,2);  // 返回 2
double min = Math.min(1,2);  // 返回 1
System.out.printf("%f, %f",max,min);
// 输出: 2.000000, 1.000000
}
}
列表 19-1
常用数学方法
```



