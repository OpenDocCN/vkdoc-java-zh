# 4. 数字与日期

本章帮助你理解如何执行一些最基本的数字和日期操作，这些操作在许多应用程序中扮演着重要角色。你将学习如何处理日期、时间和时区数据。此外，本章还提供了处理不同类型数字并将其格式化为适合大多数情况的示例。它还提供了关于执行高级任务（如处理货币）的见解。

## 4.1 将浮点数和双精度值四舍五入为整数

### 问题

你需要在应用程序中将浮点数或双精度数四舍五入为整数值。

### 解决方案

使用 `java.lang.Math` 的 `round()` 方法之一将数字四舍五入为你需要的格式。`Math` 类有两个方法可用于四舍五入浮点数或双精度值。以下代码演示了如何使用这些方法。

```
public static int roundFloatToInt(float myFloat){
return Math.round(myFloat);
}
public static long roundDoubleToLong(double myDouble){
return Math.round(myDouble);
}
```

第一个方法 `roundFloatToInt()` 接受一个浮点数，并使用 `java.lang.Math` 类将其四舍五入为 int 类型。第二个方法 `roundDoubleToLong()` 接受一个双精度值，并使用 `java.lang.Math` 类将该双精度值四舍五入为 long 类型。

以下是主类：

```
public static void main(String[] args){
Float floatValue =  7.82f;
Double doubleValue = 9.9d;
System.out.println(roundFloatToInt(floatValue));
System.out.println(roundDoubleToLong(doubleValue));
}
结果如下：

```



### 工作原理

`java.lang.Math` 类包含大量辅助方法，让我们在处理数字时更加得心应手。`round()` 方法也不例外，它可以轻松地对浮点数或双精度值进行四舍五入。`java.lang.Math round()` 方法的一个版本接受一个 `float` 类型的参数。它会将浮点数四舍五入为最接近的 `int` 值，并采用向上舍入的规则。如果参数是*非数字*（NaN），则返回零。当正无穷大或负无穷大的参数传入 `round()` 方法时，会分别返回等于 `Integer.MAX_VALUE` 或 `Integer.MIN_VALUE` 的结果。`java.lang.Math round()` 方法的第二个版本接受一个 `double` 值，该值会被四舍五入为最接近的 `long` 值，同样采用向上舍入的规则。与另一个 `round()` 方法类似，如果参数是 NaN，则返回零。同样，当正无穷大或负无穷大的参数传入 `round()` 方法时，会返回等于 `Long.MAX_VALUE` 或 `Long.MIN_VALUE` 的结果。

注意

NaN、POSITIVE_INFINITY 和 NEGATIVE_INFINITY 是在 `Float` 和 `Double` 类中定义的常量值。NaN（非数字）是一个未定义或无法表示的值。例如，将 `0.0f` 除以 `0.0f` 会产生一个 NaN 值。POSITIVE_INFINITY 和 NEGATIVE_INFINITY 表示的值，指的是由某些操作产生的、特定类型（浮点型或双精度型）的极大或极小值，这些值无法正常表示：输出结果为 Infinity 或 –Infinity。例如，1.0/0.0 或 –1.0/0.0 会产生这样的值。实际上，Java 使用一些特殊的数值来处理此类浮点运算的结果，这是由其除法运算的规范决定的。另一方面，对于整数运算，则会抛出 `ArithmeticException` 错误。测试以下代码。

```
public static void main(String[] args){
Float floatValue1 =  1.0f;
Float floatValue1n =  -1.0f;
Float floatValue0 =  0.0f;
System.out.println(floatValue1/floatValue0);
System.out.println(floatValue0/floatValue0);
System.out.println(floatValue1n/floatValue0);
Double doubleValue1 =  1.0d;
Double doubleValue1n =  -1.0d;
Double doubleValue0 =  0.0d;
System.out.println(doubleValue1/doubleValue0);
System.out.println(doubleValue0/doubleValue0);
System.out.println(doubleValue1n/doubleValue0);
}
```

输出结果如下。

```
Infinity
NaN
-Infinity
Infinity
NaN
-Infinity
```

## 4.2 格式化双精度和长整型小数值

### 问题

你需要在应用程序中格式化 `double` 和 `long` 类型的数字。

### 解决方案

使用 `DecimalFormat` 类来格式化和四舍五入数值，使其达到应用程序所需的精度。在下面的方法中，接受一个 `double` 值，并打印出一个格式化后的字符串值。

```
public static void formatDouble(double myDouble){
NumberFormat numberFormatter = new DecimalFormat("##.000");
String result = numberFormatter.format(myDouble);
System.out.println(result);
}
主类是：
public static void main(String[] args) {
formatDouble(Double.valueOf("345.9372"));
}
```

例如，如果传入 `formatDouble()` 方法的 double 值是 345.9372，结果将是 345.937。类似地，如果传入 .7697，结果将是 .770。

每个结果都使用指定的模式进行格式化，然后相应地四舍五入。

### 工作原理

`DecimalFormat` 类可以与 `NumberFormat` 类一起使用，对 `double` 或 `long` 值进行四舍五入和/或格式化。`NumberFormat` 是一个抽象类，提供了格式化和解析数字的接口。该类提供了为每个区域设置格式化和解析数字的能力，并能获取货币、百分比、整数和数字的格式。`NumberFormat` 类本身非常有用，因为它包含获取格式化数字的工厂方法。实际上，只需很少的工作就能获得格式化的字符串。例如，以下代码演示了在 `NumberFormat` 类上调用一些工厂方法。

```
// 获取 NumberFormat 类的实例
NumberFormat format = NumberFormat.getInstance();
// 为当前区域设置格式化一个 double 值
String result = format.format(83.404);
System.out.println(result);
// 为意大利区域设置格式化一个 double 值
result = format.getInstance(Locale.ITALIAN).format(83.404);
System.out.println(result);
// 将字符串解析为数字
try {
Number num = format.parse("75.736");
System.out.println(num);
} catch (java.text.ParseException ex){
System.out.println(ex);
}
输出结果是：
当前区域设置: 83,404
意大利区域设置: 83,404
现在是一个数字: 75736
```

要使用模式进行格式化，可以将 `DecimalFormat` 类与 `NumberFormat` 一起使用。在本节的解决方案中，你看到通过向 `DecimalFormat` 的构造函数传递一个模式来创建其实例，会返回一个 `NumberFormat` 类型。这是因为 `DecimalFormat` 扩展了 `NumberFormat` 类。由于 `NumberFormat` 类是抽象的，`DecimalFormat` 包含了 `NumberFormat` 的所有功能，以及用于处理模式的附加功能。因此，它可以像你在前面的演示中看到的那样，处理来自不同区域设置的不同格式。这为处理 `double` 或 `long` 类型的格式化提供了极大的灵活性。

如前所述，`DecimalFormat` 类可以在其构造函数中接受一个基于字符串的模式。你也可以在事后使用 `applyPattern()` 方法将模式应用于 `Format` 对象。每个模式都包含一个前缀、数字部分和后缀，允许你将特定的小数值格式化为所需的精度，并包含前导数字和逗号。每个模式还包含一个正子模式和一个负子模式。这两个子模式由分号（`;`）分隔，负子模式是可选的。如果没有负子模式，则使用本地化的负号。例如，一个完整的模式示例是 `###,##0.00;(###,##0.00)`。

注意

构造函数 `Double(string)`、`Float(float)`、`Integer(int)`、`Long(long)`、`Character(char)`、`Short(short)`、`Byte(byte)` 和 `Boolean(boolean)` 自版本 9 起已被弃用，并标记为待移除。可以使用

`Float floatValue = 7.82f;`

`Float floatValue = Float.valueOf("7.82");`

来代替

`new Double("345.9372");`

## 4.3 格式化紧凑数字

### 问题

你想以简短或人类可读的形式表示一个数字。

### 解决方案

使用 Java 12 中引入的 `CompactNumberInstance` 方法。

```
例如：
public static void main (String[] args) {
NumberFormat numberFormat = NumberFormat.getCompactNumberInstance(Locale.US, NumberFormat.Style.SHORT);
String result = numberFormat.format(1000);
}
```

输出结果如下。

```
1k
```

### 工作原理

Java 12 引入了对该方法的支持，用于以紧凑形式格式化数字。在前面的例子中，1000 在 en_US 区域设置中被格式化为“1K”。其形式取决于由 `NumberFormat.Style` 指定的样式。

## 4.4 比较 int 值

### 问题

你需要比较两个或多个 `int` 值。

### 解决方案 1

使用比较运算符来相互比较整数值。在下面的例子中，三个 `int` 值相互比较，演示了各种比较运算符。

```
public static void compareIntegers(){
int int1 = 1;
int int2 = 10;
int int3 = -5;
System.out.println(int1 == int2);  // 结果:  false
System.out.println(int3 == int1);  // 结果:  false
System.out.println(int1 == int1);  // 结果:  true
System.out.println(int1 > int3);   // 结果:  true
System.out.println(int2 < int3);   // 结果:  false
}
主类是：
public static void main(String[] args){
compareIntegers();
}
```

如你所见，比较运算符会生成一个 `boolean` 结果。



### 解决方案 2

使用 `Integer.compare(int,int)` 方法对两个 int 数值进行比较。以下代码可对第一个解决方案中声明的相同 int 值进行比较。

```
System.out.println("比较方法 -> int3 和 int1: " + Integer.compare(int3, int1));
// 结果 -1
System.out.println("比较方法 -> int2 和 int1: " + Integer.compare(int2, int1));
// 结果 1
```

### 工作原理

最常用的数值比较可能是针对两个或多个 int 值。Java 语言可以轻松地使用比较运算符（见表 4-1）对 int 进行比较。

表 4-1

比较运算符

| 运算符 | 功能 |
| --- | --- |
| `==` | 等于 |
| `!=` | 不等于 |
| `>` | 大于 |
| `<` | 小于 |
| `>=` | 大于或等于 |
| `<=` | 小于或等于 |

本技巧的第二个解决方案演示了 Java 7 中新增的整数 `compare()` 方法。这个静态方法接受两个 int 值并进行比较，如果第一个 int 大于第二个则返回 1，如果两个 int 值相等则返回 0，如果第一个 int 值小于第二个则返回 –1。要使用 `Integer.compare()` 方法，请像以下代码所示那样传入两个 int 值。

```
Integer.compare(int3, int1));
Integer.compare(int2, int1));
```

就像在学校里学数学一样，这些比较运算符用于判断第一个整数是等于、大于还是小于第二个整数。这些比较运算符简单易用，最常见于 `if` 语句的上下文中。

## 4.5 比较浮点数

### 问题

你需要在应用程序中比较两个或多个浮点值。

### 解决方案 1

使用 `Float` 对象的 `compareTo()` 方法将一个浮点数与另一个进行比较。以下示例展示了 `compareTo()` 方法的实际应用。

```
public static void compareFloat(){
Float float1 = Float.valueOf ("9.675");
Float float2 = Float.valueOf ("7.3826");
Float float3 = Float.valueOf ("23467.373");
System.out.println(float1.compareTo(float3));  // 结果: -1
System.out.println(float2.compareTo(float3));  // 结果: -1
System.out.println(float1.compareTo(float1));  // 结果: 0
System.out.println(float3.compareTo(float2));  // 结果: 1
}
主类是:
public static void main(String[] args){
compareFloat();
}
```

调用 `compareTo()` 方法的结果是一个整数值。负结果表示第一个浮点数小于被比较的浮点数。零表示两个浮点数值相等。最后，正结果表示第一个浮点数大于被比较的浮点数。

### 解决方案 2

使用 `Float` 类的 `compare()` 方法进行比较。以下示例演示了 `Float.compare(float, float)` 方法。

```
System.out.println(Float.compare(float1, float3)); // 结果: -1
System.out.println(Float.compare(float2, float3)); // 结果: -1
System.out.println(Float.compare(float1, float1)); // 结果: 0
System.out.println(Float.compare(float3, float2)); // 结果: 1
```

### 工作原理

比较两个 Float 对象最实用的方法是使用 `compareTo()` 方法。该方法对给定的 Float 对象进行数值比较。结果是一个整数值，指示第一个浮点数在数值上是大于、等于还是小于被比较的浮点数。如果浮点值为 NaN，则它被视为等于其他 NaN 值，或大于所有其他浮点值。此外，浮点值 0.0f 大于浮点值 –0.0f。

`compareTo()` 的替代方法是 `compare()` 方法，它也是 `Float` 类原生提供的方法。`compare()` 方法是在 Java 1.4 中引入的，它是一个静态方法，以与 `compareTo()` 相同的方式比较两个浮点值。它只是让代码的阅读方式略有不同。`compare()` 方法的格式如下。

```
Float.compare(primitiveFloat1, primitiveFloat2)
```

上面展示的 `compare()` 方法在内部使用 `compareTo()` 进行了以下调用。

```
new Float(float1).compareTo(new Float(float2));
```

最终，使用 `compareTo()` 或 `compare()` 会返回相同的结果。

## 4.6 随机生成值

### 问题

你正在开发的应用程序需要使用随机生成的数字。

### 解决方案 1

使用 `java.util.Random` 类来帮助生成随机数。`Random` 类旨在为少数几种 Java 数值数据类型生成随机数。以下代码演示了如何使用 `Random` 类生成此类数字。

```
public static void randomExamples() {
// 创建 Random 类的新实例
Random random = new Random();
System.out.println("随机数生成器: " + random);
// 生成一个随机整数
int myInt = random.nextInt();
System.out.println("随机整数: " + myInt);
// 生成一个随机 Double 值
double myDouble = random.nextDouble();
System.out.println("随机双精度浮点数: " + myDouble);
// 生成一个随机浮点数
float myFloat = random.nextFloat();
System.out.println("随机浮点数: " + myFloat);
// 生成一个随机高斯双精度浮点数
// 均值为 0.0，标准差为 1.0
// 来自此随机数生成器的序列。
double gausDouble = random.nextGaussian();
System.out.println("随机高斯双精度浮点数: " + gausDouble);
// 生成一个随机长整数
long myLong = random.nextLong();
System.out.println("随机长整数: " + myLong);
// 生成一个随机布尔值
boolean myBoolean = random.nextBoolean();
System.out.println("随机布尔值: " + myBoolean);
}
主类是:
public static void main(String[] args){
randomExamples();
}
一个输出示例为:
Random: java.util.Random@17f052a3
Random int: 626546817
Random double: 0.3717917526454104
Random float: 0.23121738
Random Gaussian double: -0.48108588194060814
Random long: 686366349321458218
Random boolean: false
```

### 解决方案 2

使用 `Math.random()` 方法。这会生成一个大于 0.0 但小于 1.0 的 double 值。以下代码演示了此方法的使用。

```
double rand = Math.random();
```

### 工作原理

`java.util.Random` 类使用一个 48 位的种子来生成一系列伪随机值。从本技巧解决方案中的示例可以看出，`Random` 类可以根据给定的种子生成许多不同类型的随机数值。默认情况下，种子是根据机器已运行毫秒数计算得出的。但是，也可以使用 `Random` 类的 `setSeed()` 方法手动设置种子。如果两个 `Random` 对象具有相同的种子，它们将产生相同的结果。

需要注意的是，在某些情况下，`Random` 类可能不是生成随机值的最佳选择。例如，如果你尝试使用线程安全的 `java.util.Random` 实例，并且在处理大量线程时，可能会遇到性能问题。在这种情况下，你可以考虑改用 `ThreadLocalRandom` 类。类似地，如果你需要使用加密安全的 `Random` 对象，请考虑使用 `SecureRandom`。

当你需要生成指定类型的随机值时，`java.util.Random` 类非常方便。它不仅易于使用，而且还为返回类型提供了广泛的选择。另一种简单的技术是使用 `Math.random()` 方法，如解决方案 2 所示，该方法会生成一个范围在 0.0 到 1.0 之间的 double 值。这两种技术都提供了生成随机值的良好方法。但是，如果你需要生成特定类型的随机数，`java.util.Random` 是最佳选择。

## 4.7 获取不含时间的当前日期

### 问题

你正在开发一个应用程序，希望获取当前日期（不包括时间）以在表单上显示。



### 解决方案

使用日期时间 API 获取当前日期。`LocalDate` 类以年-月-日格式表示 ISO 日历。以下代码行捕获并显示当前日期。

```
public static void newDate() {
LocalDate date = LocalDate.now();
System.out.println("Current Date:" + date);
}
输出结果为：
Current Date: 2021-11-30
```

### 工作原理

日期时间 API 可以轻松获取当前日期，且不包含其他信息。为此，需导入 `java.time.LocalTime` 类并调用其 `now()` 方法。`LocalTime` 类不可实例化，因为它是不可变且线程安全的。调用 `now()` 方法会返回另一个 `LocalDate` 对象，其中包含年-月-日格式的当前日期。

`now()` 方法的另一个版本接受一个 `java.time.Clock` 对象作为参数，并基于该时钟返回日期。例如，以下代码行演示了如何获取表示系统时间的 `Clock` 对象。

```
public static void newDateFromClock() {
Clock clock = Clock.systemUTC();
LocalDate date = LocalDat.now(clock);
System.out.println("Date from clock: " + date);
}
输出结果为：
Date from clock: 2021-11-30
主类为：
public static void main(String[] args) {
newDate();
newDateFromClock();
}
```

在之前的版本中，也有其他获取当前日期的方法，但通常日期会附带时间，然后需要格式化以移除不需要的时间数字。新的 `java.time.LocalDate` 类使得处理与时间分离的日期成为可能。

## 4.8 根据日期条件获取日期对象

### 问题

你希望根据年-月-日的规范获取一个 `Date` 对象。

### 解决方案

调用 `LocalDate.of()` 方法，传入所需的年、月、日来获取对象。例如，假设你想获取 2021 年 11 月某个指定日期的 `Date` 对象。你可以将日期条件传递给 `LocalDate.of()` 方法，如下列代码行所示。

```
public static void newSpecifiedDate() {
LocalDate date = LocalDate.of(2021, Month.NOVEMBER, 12);
System.out.println("Date from specified date: " + date);
}
主类为：
public static void main(String[] args) {
newSpecifiedDate();
}
```

结果如下。

```
Date from specified date: 2021-11-12
```

### 工作原理

`LocalDate.of()` 方法接受三个值作为参数。这些参数分别代表年、月、日。年份参数始终被视为 int 值。月份参数可以表示为 int 值，对应一个表示月份的枚举。`Month` 枚举为每个月返回一个 int 值，其中 JANUARY 返回 1，DECEMBER 返回 12。因此，`Month.NOVEMBER` 返回 11。也可以将 `Month` 对象作为第二个参数传递，而不是 int 值。最后，通过将 int 值作为第三个参数传递给 `of()` 方法来指定月份中的日期。

## 4.9 获取年-月-日日期组合

### 问题

你希望获取指定日期的年份、年-月或月份。

### 解决方案 1

要获取指定日期的年-月，请使用 `java.time.YearMonth` 类。该类表示特定年份的月份。在以下代码行中，`YearMonth` 对象获取当前日期和另一个指定日期的年份和月份。

```
public static void obtainYearMonth() {
YearMonth yearMo = YearMonth.now();
System.out.println("Current Year and month:" + yearMo);
YearMonth specifiedDate = YearMonth.of(2021, Month.NOVEMBER);
System.out.println("Specified Year-Month: " + specifiedDate);
}
```

结果如下。

```
Current Year and month:2021-10
Specified Year-Month: 2021-11
```

### 解决方案 2

要获取当前日期或指定日期的月-日，只需使用 `java.time.MonthDay` 类。以下代码行演示了如何获取月-日组合。

```
public static void obtainMonthDay(){
MonthDay monthDay = MonthDay.now();
System.out.println("Current month and day: " + monthDay);
MonthDay specifiedDate = MonthDay.of(Month.NOVEMBER, 12);
System.out.println("Specified Month-Day: " + specifiedDate);
}
```

结果如下。

```
Current month and day: --10-24
Specified Month-Day: --11-12
主类为：
public static void main(String[] args) {
obtainYearMonth();
obtainMonthDay();
}
```

请注意，默认情况下，`MonthDay` 返回的格式不太实用。

### 工作原理

日期时间 API 包含一些类，可以轻松获取应用程序所需的日期信息。其中两个类是 `YearMonth` 和 `MonthDay`。`YearMonth` 类以年-月格式获取日期。它包含几个获取年-月组合的方法。如解决方案所示，你可以调用 `now()` 方法获取当前的年-月组合。与 `LocalDate` 类一样，`YearMonth` 也包含一个 `of()` 方法，该方法接受 int 格式的年份和一个表示月份的数字。在解决方案中，`Month` 枚举用于获取月份值。

与 `YearMonth` 类类似，`MonthDay` 以月-日格式获取日期。它也包含几个不同的方法来获取月-日组合。解决方案 2 演示了其中两种技术：通过调用 `now()` 方法获取当前的月-日组合，以及使用 `of()` 方法获取指定日期的月-日组合。`of()` 方法接受一个 int 值作为第一个参数表示月份，接受一个 int 值作为第二个参数表示月份中的日期。

## 4.10 基于当前时间获取和计算时间

### 问题

你希望获取当前时间以标记给定记录。你还希望基于该时间进行计算。

### 解决方案

使用属于新日期时间 API 的 `LocalTime` 类来获取并显示当前时间。以下代码行演示了 `LocalTime` 类的使用。

```
public static void currentTime(){
LocalTime time = LocalTime.now();
System.out.println("Current Time: " + time);
}
```

获取时间后，可以调用 `LocalTime` 实例的方法来实现所需结果。以下代码行展示了一些使用 `LocalTime` 方法的示例。

```
// atDate(LocalDate): 获取本地日期和时间
LocalDateTime ldt = time.atDate(LocalDate.of(2021,Month.NOVEMBER,12));
System.out.println("Local Date Time object: " + ldt);
// of(int hours, int min): 获取特定时间
LocalTime pastTime = LocalTime.of(1, 10);
// compareTo(LocalTime): 比较两个时间。如果大于则返回正值
System.out.println("Comparing times: " + time.compareTo(pastTime));
// getHour(): 以 int 值返回小时（24 小时制）
int hour = time.getHour();
System.out.println("Hour: " + hour);
// isAfter(LocalTime): 返回布尔值比较结果
System.out.println("Is local time after pastTime? " + time.isAfter(pastTime));
// minusHours(int): 从 LocalTime 中减去小时数
LocalTime minusHrs = time.minusHours(5);
System.out.println("Time minus 5 hours: " + minusHrs);
// plusMinutes(int): 向 LocalTime 添加分钟数
LocalTime plusMins = time.plusMinutes(30);
System.out.println("Time plus 30 mins: " + plusMins);
```

结果如下。

```
Current Time: 12:43:58.048247
Local Date Time object: 2021-11-12T12:43:58.048247
Comparing times: 1Hour: 12
Is local time after pastTime? true
Time minus 5 hours: 07:43:58.048247
Time plus 30 mins: 13:13:58.048247
主类为：
public static void main(String[] args){
currentTime();
}
```



### 工作原理

有时需要获取当前系统时间。`LocalTime` 类通过调用其 `now()` 方法来获取当前时间。与 `LocalDate` 类类似，可以调用 `LocalTime.now()` 方法返回一个等于当前时间的 `LocalTime` 对象。`LocalTime` 类还包含多个可用于操作时间的方法。本解决方案中的示例简要概述了可用的方法。

让我们看几个示例，以便了解如何调用 `LocalTime` 方法。要获取一个设置为特定时间的 `LocalTime` 对象，请调用 `LocalTime.of(int, int)` 方法，并传入代表小时和分钟的 int 参数。

```
// of(int hours, int min): 获取特定时间
LocalTime pastTime = LocalTime.of(1, 10);
```

`atDate(LocalDate)` 实例方法将一个 `LocalDate` 对象应用于一个 `LocalTime` 实例，返回一个 `LocalDateTime` 对象。

```
LocalDateTime ldt = time.atDate(LocalDate.of(2021,Month.NOVEMBER,12);
```

有几种方法可以获取时间的各个部分。例如，`getHour()`、`getMinute()`、`getNano()` 和 `getSecond()` 方法返回 `LocalTime` 对象的这些指定部分。

```
int hour = time.getHour();
int min  = time.getMinute();
int nano = time.getNano();
int sec  = time.getSecond();
```

还有一些比较方法可供使用。例如，`compareTo(LocalTime)` 方法将一个 `LocalTime` 对象与另一个进行比较。`isAfter(LocalTime)` 判断时间是否在另一个时间之后，而 `isBefore(LocalTime)` 则判断相反的情况。

## 4.11 同时获取和使用日期与时间

### 问题

在您的应用程序中，您希望显示当前日期和当前时间。

### 解决方案 1

使用属于新日期时间 API 一部分的 `LocalDateTime` 类来捕获并显示当前日期和时间。`LocalDateTime` 类包含一个名为 `now()` 的方法，用于获取当前日期和时间。以下几行代码演示了如何执行此操作。

```
LocalDateTime ldt = LocalDateTime.now();
System.out.println("本地日期和时间: " + ldt);
```

生成的 `LocalDateTime` 对象包含日期和时间，但不包含时区信息。`LocalDateTime` 类还包含其他方法，这些方法提供了处理日期时间数据的选项。例如，要返回一个具有指定日期和时间的 `LocalDateTime` 对象，请将 int 类型的参数传递给 `LocalDateTime.of()` 方法，如下所示。

```
// 获取日期 2021 年 11 月 11 日 12:00 的 LocalDateTime 对象
LocalDateTime ldt2 = LocalDateTime.of(2021, Month.NOVEMBER, 11, 12, 00);
```

以下示例演示了 `LocalDateTime` 对象中可用的几种方法。

```
public static void obtainDatesWithTime(){
LocalDateTime ldt = LocalDateTime.now();
System.out.println("本地日期和时间: " + ldt);
// 获取日期 2021 年 11 月 11 日 12:00 的 LocalDateTime 对象
LocalDateTime ldt2 = LocalDateTime.of(2021, Month.NOVEMBER, 11, 12, 00);
System.out.println("指定日期和时间: " + ldt2);
// 从 LocalDateTime 对象获取月份
Month month = ldt.getMonth();
int monthValue = ldt.getMonthValue();
System.out.println("月份: " + month);
System.out.println("月份数值: " + monthValue);
// 获取日、星期和年中的第几天
int day = ldt.getDayOfMonth();
DayOfWeek dayWeek = ldt.getDayOfWeek();
int dayOfYr = ldt.getDayOfYear();
System.out.println("日: " + day);
System.out.println("星期: " + dayWeek);
System.out.println("年中的第几天: " + dayOfYr);
// 获取年份
int year = ldt.getYear();
System.out.println("日期: " + monthValue + "/" + day + "/" + year);
int hour = ldt.getHour();
int minute = ldt.getMinute();
int second = ldt.getSecond();
System.out.println("当前时间: " + hour + ":" + minute + ":" + second);
// 月份等的计算
LocalDateTime currMinusMonths = ldt.minusMonths(12);
LocalDateTime currMinusHours = ldt.minusHours(10);
LocalDateTime currPlusDays = ldt.plusDays(30);
System.out.println("当前日期时间减去 12 个月: " + currMinusMonths);
System.out.println("当前日期时间减去 10 小时: " + currMinusHours);
System.out.println("当前日期时间加上 30 天: " + currPlusDays);
}
```

以下是结果。

```
本地日期和时间: 2021-12-06T19:37:53.422560400
指定日期和时间: 2021-11-11T12:00
月份: DECEMBER
月份数值: 12
日: 6
星期: MONDAY
年中的第几天: 340
日期: 12/6/2021
当前时间: 19:37:53
当前日期时间减去 12 个月: 2020-12-06T19:37:53.422560400
当前日期时间减去 10 小时: 2021-12-06T09:37:53.422560400
当前日期时间加上 30 天: 2022-01-05T19:37:53.422560400
主方法是:
public static void main(String[] args){
obtainDatesWithTime();
}
```

### 解决方案 2

如果您只需要获取当前日期而不涉及日历细节，请使用 `java.util.Date` 类生成一个新的 `Date` 对象。这样做会生成一个等于当前系统日期的新 `Date` 对象。在以下代码中，您可以看到创建一个新的 Date 对象并获取当前日期是多么容易。

```
Date date = new Date();
System.out.println("使用 java.util.Date(): " + date);
System.out.println("从 java.util.Date() 获取时间: " + date.getTime());
```

结果是一个 `Date` 对象，其中包含运行代码的系统提供的当前日期和时间，包括时区信息，如下例所示。该时间是自 1970 年 1 月 1 日 00:00:00 GMT 以来的毫秒数。

```
使用 java.util.Date(): Mon Dec 06 19:37:53 CET 2021
从 java.util.Date() 获取时间: 1638815873430
```

### 解决方案 3

如果您需要更精确的日历信息，请使用 `java.util.Calendar` 类。虽然使用 `Calendar` 类会使代码更长，但结果比使用 `java.util.Date` 更精细。以下代码演示了使用此类获取当前日期的部分功能。

```
Calendar gCal = Calendar.getInstance();
// 月份基于零索引，一月等于 0，
// 因此我们需要给月份加一，使其符合标准格式
int month = gCal.get(Calendar.MONTH) + 1;int day = gCal.get(Calendar.DATE);
int yr = gCal.get(Calendar.YEAR);
String dateStr = month + "/" + day + "/" + yr;
System.out.println(dateStr);
int dayOfWeek = gCal.get(Calendar.DAY_OF_WEEK);
// 打印星期几的整数值
System.out.println(dayOfWeek);
int hour = gCal.get(Calendar.HOUR);
int min  = gCal.get(Calendar.MINUTE);
int sec = gCal.get(Calendar.SECOND);
// 打印时间
System.out.println(hour + ":" + min + ":" + sec);
// 创建新的 DateFormatSymbols 实例以获取日期的字符串值
DateFormatSymbols symbols = new DateFormatSymbols();
String[] days = symbols.getWeekdays();
System.out.println(days[dayOfWeek]);
// 深入处理日期！
int dayOfYear = gCal.get(Calendar.DAY_OF_YEAR);
System.out.println(dayOfYear);
// 打印一年中剩余的天数
System.out.println(yr + "年剩余天数: " + (365-dayOfYear));
int week = gCal.get(Calendar.WEEK_OF_YEAR);
// 打印一年中的第几周
System.out.println(week);
```

这段代码演示了使用 `Calendar` 类可以获取关于当前日期的更详细信息。运行代码的结果如下所示。

```
12/6/2021

7:37:53
lunedì

2021 年剩余天数: 25

```



### 工作原理

许多应用程序需要使用当前日历日期，通常还需要获取当前时间。实现这一目标有多种方法，本攻略的解决方案展示了其中三种。日期时间 API 包含一个`LocalDateTime`类，通过调用其`now()`方法即可捕获当前日期和时间。若要获取指定的日期和时间，可在调用`LocalDateTime.of()`时传入相应的整数和月份类型参数。此外，通过`LocalDateTime`实例还可使用多种方法，例如`getHours()`、`getMinutes()`、`getNanos()`和`getSeconds()`，这些方法允许对日期和时间进行更精细的控制。`LocalDateTime`实例还包含用于执行计算、转换、比较等操作的方法。本攻略的解决方案 1 演示了`LocalDateTime`的使用，展示了如何执行计算以及获取日期和时间的部分信息以供进一步使用。

默认情况下，`java.util.Date`类可以在无参实例化时返回当前日期和时间。`Date`类也可以通过`getTime()`方法返回当前时间。如解决方案所述，`getTime()`方法返回自 1970 年 1 月 1 日 00:00:00 GMT 以来所使用`Date`对象表示的毫秒数。针对`Date`对象，还可以调用其他几种方法，将当前日期和时间分解为更细粒度的时间间隔。例如，`Date`类拥有`getHours()`、`getMinutes()`、`getSeconds()`、`getMonth()`、`getDay()`、`getTimezoneOffset()`和`getYear()`方法。但除`getTime()`外，不建议使用这些方法中的任何一个，因为它们都已通过`java.time.LocalDateTime`和`java.util.Calendar get()`方法被标记为弃用。当某个方法或类被弃用时，不应再继续使用，因为它可能会在 Java 语言的未来版本中被移除。不过，`Date`类中包含的少数方法尚未被标记为弃用，因此`Date`类很可能会包含在 Java 的未来版本中。这些保留不变的方法包括`after()`、`before()`、`compareTo()`、`setTime()`和`equals()`比较方法。本攻略的解决方案 2 演示了实例化一个 Date 对象并打印出当前日期和时间。

如前所述，`Date`类有许多方法已被弃用，不应再使用。在本攻略的解决方案 3 中，演示了`java.util.Calendar`类作为获取这些信息的一个后继者。`Calendar`类是在 JDK 1.1 中引入的，当时许多`Date`方法被弃用。从解决方案 3 可以看出，`Calendar`类包含了`Date`类中的所有相同功能，但`Calendar`类更加灵活。`Calendar`类包含用于在特定时间和日期之间进行转换以及以各种方式操作日历的方法。`Calendar`类在 Java 8 中新增了几个方法，其中一个新方法是`java.util`。

对于某些应用程序来说，`Date`类已经足够。例如，在处理时间戳时，`Date`类可能很有用。但是，如果应用程序需要对日期和时间进行详细操作，则建议使用`LocalDateTime`或`Calendar`类，它们都包含了`Date`类的所有功能以及更多特性。本攻略中的所有解决方案在技术上都是合理的；请选择最适合您应用程序需求的那个。

## 4.12 获取机器时间戳

### 问题

您需要从系统获取一个基于机器的时间戳。

### 解决方案

使用`Instant`类，它表示基于机器时间的时间线上纳秒的起点。在以下示例中，`Instant`类获取系统时间戳。`Instant`类也用于其他场景，例如基于`Instant`计算不同日期。

```
public static void instants(){
Instant timestamp = Instant.now();
System.out.println("当前时间戳: " + timestamp);
//现在减去三天
Instant minusThree = timestamp.minus(3, ChronoUnit.DAYS);
System.out.println("现在减去三天:" + minusThree);
ZonedDateTime atZone = timestamp.atZone(ZoneId.of("GMT"));
System.out.println(atZone);
Instant yesterday = Instant.now().minus(24, ChronoUnit.HOURS);
System.out.println("昨天: " + yesterday);
}
```

以下是输出结果。

```
当前时间戳: 2021-10-13T21:06:50.203477900Z
现在减去三天:2021-10-10T21:06:50.203477900Z
2021-10-13T21:06:50.203477900Z[GMT]
昨天: 2021-10-12T21:06:50.221430600Z
```

### 工作原理

日期时间 API 引入了一个名为`Instant`的新类，它表示基于机器时间的时间线上纳秒的起点。基于机器时间，`Instant`类中的值从 EPOCH（1970 年 1 月 1 日 00:00:00Z）开始计数。EPOCH 之前的任何值均为负数，EPOCH 之后的值均为正数。`Instant`类非常适合获取机器时间戳，因为它包含了精确到纳秒的所有相关日期和时间信息。

`Instant`类是静态且不可变的，因此可以调用`now()`方法来获取当前时间戳。这样做会返回当前`Instant`的一个副本。`Instant`类还包含转换和计算方法。每个方法都返回`Instant`类或其他类型的副本。在解决方案中，`now()`方法返回当前时间戳，随后给出几个示例，展示如何执行计算以及获取`Instant`上的信息。

`Instant`类是 Java 8 中一个重要的新特性，因为它使得处理当前时间和日期数据变得容易。其他日期和时间类，如`LocalDateTime`，也很有用。然而，`Instant`类是最精确的时间戳，因为它基于纳秒精度。

## 4.13 基于时区转换日期和时间

### 问题

您正在开发的应用程序有可能在全球范围内使用。在应用程序的某些区域，需要显示静态的日期和时间，而不是系统日期和时间。在这种情况下，需要转换这些静态的日期和时间，以适应当前应用程序用户所在的特定时区。



### 解决方案

日期时间 API 通过 `Time Zone` 和 `Offset` 类提供了处理时区数据的适当工具。在以下场景中，假设应用程序处理的是车辆租赁的预订。你可以在一个时区租车，然后在另一个时区还车。以下代码行演示了如何在此类场景中打印个人的预订信息。名为 `scheduleReport` 的方法接受代表办理取车和还车日期/时间的 `LocalDateTime` 对象，以及各自对应的 `ZoneId`。航空公司可以使用此方法来打印特定航班的时区信息。

```
public static void scheduleReport(LocalDateTime checkOut, ZoneId checkOutZone,
LocalDateTime checkIn, ZoneId checkInZone){
ZonedDateTime beginTrip = ZonedDateTime.of(checkOut, checkOutZone);
System.out.println("行程开始: " + beginTrip);
// 获取取车时区的规则
ZoneRules checkOutZoneRules = checkOutZone.getRules();
System.out.println("取车时区规则: " + checkOutZoneRules);
// 假设行程持续了 4 天
ZonedDateTime beginPlus = beginTrip.plusDays(4);
System.out.println("四天后: " + beginPlus);
// 在起始时区中的行程结束时间
ZonedDateTime endTripOriginalZone = ZonedDateTime.of(checkIn, checkOutZone);
ZonedDateTime endTrip = ZonedDateTime.of(checkIn, checkInZone);
int diff = endTripOriginalZone.compareTo(endTrip);
String diffStr = (diff >= 0) ? "否":"是";
System.out.println("在原始时区的结束行程日期/时间: " + endTripOriginalZone);
System.out.println("在还车时区的结束行程日期/时间: " + endTrip );
System.out.println("原始时区时间是否小于新时区时间? " +
diffStr );
ZoneId checkOutZoneId = beginTrip.getZone();
ZoneOffset checkOutOffset = beginTrip.getOffset();
ZoneId checkInZoneId = endTrip.getZone();
ZoneOffset checkInOffset = endTrip.getOffset();
System.out.println("取车时区和偏移量: " + checkOutZoneId + checkOutOffset);
System.out.println("还车时区和偏移量: " + checkInZoneId +  checkInOffset);
}
```

以下是运行结果。

```
Trip Begins: 2021-12-13T13:00-05:00[US/Eastern]
Checkout Time Zone Rules: ZoneRules[currentStandardOffset=-05:00]
Four Days Later: 2021-12-17T13:00-05:00[US/Eastern]
End trip date/time in original zone: 2021-12-18T10:00-05:00[US/Eastern]
End trip date/time in check-in zone: 2021-12-18T10:00-07:00[US/Mountain]
Original Zone Time is less than new zone time? YES
Check out zone and offset: US/Eastern-05:00
Check in zone and offset: US/Mountain-07:00
```

### 工作原理

时区给开发者带来了另一个挑战，而 Java 日期时间 API 提供了一个简单的方面来处理它们。日期时间 API 包含一个 `java.time.zone` 包，其中包含几个有助于处理时区数据的类。这些类支持时区规则、数据以及本地时间线上由此产生的间隙和重叠（通常是夏令时转换的结果）。构成 zone 包的类在表 4-2 中进行了概述。

表 4-2

时区类

| 类名 | 描述 |
| --- | --- |
| `ZoneId` | 指定时区标识符，用于转换 |
| `ZoneOffset` | 指定与格林威治/UTC 时间的时区偏移量 |
| `ZonedDateTime` | 一个日期时间对象，同时处理时区数据和与格林威治/UTC 时间的时区偏移量 |
| `ZoneRules` | 定义指定时区的偏移量如何变化的规则 |
| `ZoneRulesProvider` | 向特定系统提供时区规则的提供者 |
| `ZoneOffsetTransition` | 本地时间线上的不连续性导致的两个偏移量之间的转换 |
| `ZoneOffsetTransitionRule` | 表达如何创建转换的规则 |

从最基本的时区类 `ZoneId` 开始，每个时区都包含一个特定的时区标识符。此标识符可用于为日期时间分配特定的时区。在解决方案中，`ZoneId` 类计算两个时区之间的任何差异。它根据特定的偏移量（固定的或基于地理区域的）识别出应使用的转换规则。

`ZonedDateTime` 是一个不可变类，它同时处理日期时间和时区数据。该类表示一个对象，很像包含 `ZoneId` 的 `LocalDateTime`。它表达了日期的所有方面，包括年、月、日、小时、分钟、秒、纳秒和时区。该类包含大量用于执行计算、转换等操作的有用方法。

`ZoneOffset` 指定与格林威治/UTC 时间的时区偏移量。你可以通过调用 `ZonedDateTime.getOffset()` 方法来查找特定时区的偏移量。`ZoneOffset` 类包含一些方法，可以轻松地将偏移量分解为不同的时间单位。例如，`getTotalSeconds()` 方法将总小时、分钟和秒字段作为单个偏移量返回，该偏移量可以添加到某个时间上。

可以定义许多规则来确定单个时区的偏移量如何变化。`ZoneRules` 类为一个区域定义了这些规则。例如，可以调用 `ZoneRules` 来指定或确定夏令时是否是一个影响因素。也可以将 `Instant` 或 `LocalDateTime` 传递给 `ZoneRules` 的方法，例如 `getOffset()` 和 `getTransition()`，以返回 `ZoneOffset` 或 `ZoneOffsetTransition`。

另一个经常使用的时区类是 `ZoneOffsetTransition`。该类模拟了由于夏令时导致的春季和秋季偏移量之间的转换。它确定转换之间是否存在间隙，获取转换的持续时间等。

`ZoneRulesProvider`、`ZoneOffsetTransitionRule` 和其他类在处理日期和时区时通常不像其他类那样常用。这些类对于管理时区规则和转换的配置很有用。

注意

`java.time.zone` 包中的类很重要，因为每个类都可以调用多种方法。本攻略仅提供了时区使用的基础知识，作为入门指南。有关更详细的信息，请参阅在线文档。

## 4.14 比较两个日期

### 问题

你想确定一个日期是否大于另一个日期。

### 解决方案

利用日期时间 API 类中的 `compareTo()` 方法之一。在以下解决方案中，比较了两个 `LocalDate` 对象并显示了相应的消息。

```
public static void compareDates(LocalDate ldt1,
LocalDate ldt2) {
int comparison = ldt1.compareTo(ldt2);
if (comparison > 0) {
System.out.println(ldt1 + " 大于 " + ldt2);
} else if (comparison < 0) {
System.out.println(ldt1 + " 小于 " + ldt2);
} else {
System.out.println(ldt1 + " 等于 " + ldt2);
}
}
```

类似地，在执行日期比较时还有一些便捷方法。具体来说，`isAfter()`、`isBefore()` 和 `isEqual()` 方法可以像 `compareTo()` 一样进行比较，如下例所示。

```
public static void compareDates2(LocalDate ldt1, LocalDate ldt2){
if(ldt1.isAfter(ldt2)){
System.out.println(ldt1 + " 在 " + ldt2 + " 之后");
} else if (ldt1.isBefore(ldt2)){
System.out.println(ldt1 + " 在 " + ldt2 + " 之前");
} else if (ldt1.isEqual(ldt2)){
System.out.println(ldt1 + " 等于 " + ldt2);
}
}
main 方法如下：
public static void main(String[] args) {
LocalDate anniversary = LocalDate.of(2000, Month.NOVEMBER, 11);
LocalDate today = LocalDate.now();
compareDates(anniversary, today);
compareDates2(anniversary, anniversary);
}
输出结果为：
2000-11-11 is before 2021-11-30
2000-11-11 is equal to 2000-11-11
```



### 工作原理

许多日期时间 API 类都包含一个用于比较两个不同日期时间对象的方法。在本例的解决方案中，`LocalDate.compareTo()` 方法用于判断一个 `LocalDate` 对象是否大于另一个。如果第一个 `LocalDate` 对象大于第二个，`compareTo()` 方法返回一个负整数；如果两者相等，则返回零；如果第二个 `LocalDate` 对象大于第一个，则返回一个正整数。所有包含 `compareTo()` 方法的日期时间类都具有相同的行为。该方法返回一个整数值，指示第一个对象是大于、小于还是等于第二个对象。

如第二个示例所示，`isAfter()`、`isBefore()` 和 `isEqual()` 方法也可用于比较。这些方法返回一个布尔值来表示比较结果。虽然这些方法的结果在日期比较方式上与 `compareTo()` 非常相似，但它们可以使代码更易于阅读。

## 4.15 查找日期和时间之间的间隔

### 问题

你需要确定两个日期或时间之间经过了多少小时、天、周、月或年。

### 解决方案 1

利用日期时间 API 来确定两个日期之间的差值。具体来说，使用 `Period` 类来确定两个日期之间的天数周期。以下示例演示了如何获取两个日期之间的天数、月数和年数间隔。

注意

此示例显示的是天数、月数和年数的差值，而不是两个日期之间的累计天数或月数。要确定两个日期之间的总累计天数、月数和年数，请继续阅读解决方案 #2 和 #3。

```
public static void intervals(){
LocalDate anniversary = LocalDate.of(2015, Month.NOVEMBER, 11);
LocalDate today = LocalDate.now();
Period period = Period.between(anniversary, today);
System.out.println("天数差值: " +  period.getDays());
System.out.println("月数差值: " + period.getMonths());
System.out.println("年数差值: " + period.getYears());
}
```

以下是针对当前日期（**2021-12-06**）的差值结果。

```
天数差值: 25
月数差值: 0
年数差值: 6
```

### 解决方案 2

使用 `java.util.concurrent.TimeUnit` 枚举在给定日期之间执行计算。使用此枚举，你可以获取天、小时、微秒、毫秒、分钟、纳秒和秒的整数值。这样你就可以执行必要的计算。

```
public static void compareDatesCalendar() {
// 获取 Calendar 类的两个实例
Calendar cal1 = Calendar.getInstance();
Calendar cal2 = Calendar.getInstance();
// 将日期设置为 2010/01/01:12:00
cal2.set(2010,0,1,12,0,0);
Date date1 = cal2.getTime();
System.out.println(date1);
System.out.println(cal1.getTime());
long mill = Math.abs(cal1.getTimeInMillis() - date1.getTime());
// 转换为小时
long hours = TimeUnit.MILLISECONDS.toHours(mill);
// 转换为天
Long days = TimeUnit.HOURS.toDays(hours);
String diff = String.format("%d 小时 %d 分钟", hours,
TimeUnit.MILLISECONDS.toMinutes(mill) - TimeUnit.HOURS.toMinutes(hours));
System.out.println(diff);
diff = String.format("%d 天", days);
System.out.println(diff);
// 将天数除以七得到周数
int weeks = days.intValue()/7;
diff = String.format("%d 周", weeks);
System.out.println(diff);
}
```

以下是针对当前日期（**2021-12-06 19:42**）的结果。

```
Fri Jan 01 12:00:00 CET 2010
Mon Dec 06 19:42:17 CET 2021
104575 小时 42 分钟
4357 天
622 周
日期之间的年数: 6
日期之间的天数:2216
```

此代码的输出被格式化为显示文本字符串，以指示当前日期与所创建的 `Date` 对象之间的差异。

以下是主类。

```
public static void main(String[] args){
intervals();
compareDatesCalendar();
}
```

### 工作原理

与大多数编程技术一样，Java 有多种执行日期计算的方法。Java 8 中引入的日期时间 API 包含了用于确定时间间隔的新技术。`Period` 类确定指定对象之间两个单位的差值周期。要获取两个日期时间对象之间的周期，请调用 `Period.between()` 方法，并传入你想要获取周期的两个日期时间对象。`Period` 类有多个方法可以将间隔分解为不同的单位。例如，可以使用 `getDays()` 方法获取两个日期时间对象中的天数。类似地，可以调用 `getMonths()` 和 `getYears()` 方法来返回该周期中的月数或年数。

最有用的技术之一是基于给定日期的时间（以毫秒为单位）执行计算。这提供了最精确的计算，因为它在一个非常小的间隔（毫秒）上工作。可以通过对 `Calendar` 对象调用 `getTimeInMillis()` 方法来获取当前时间（以毫秒为单位）。同样，`Date` 对象通过调用 `getTime()` 方法返回其以毫秒表示的值。从本配方的解决方案中可以看出，第一个计算是找出给定日期之间的毫秒差。获取该值然后取其绝对值，就为执行日期计算提供了基础。

## 4.16 从指定字符串获取日期时间

### 问题

你想将一个字符串解析为日期时间对象。

### 解决方案

利用时间日期类的 `parse()` 方法，使用预定义或自定义格式解析字符串。以下代码行演示了如何使用 `parse()` 方法的变体将字符串解析为日期或日期时间对象。

```
public static void parseDateTime(){
// 将字符串解析为日期时间对象
LocalDate ld = LocalDate.parse("2019-12-28");
LocalDateTime ldt = LocalDateTime.parse("2019-12-28T08:44:00");
System.out.println("解析的日期: " + ld);
System.out.println("解析的日期时间: " + ldt);
// 使用不同的解析器
LocalDate ld2 = LocalDate.parse("2019-12-28", DateTimeFormatter.ISO_DATE);
System.out.println("不同的解析器: " + ld2);
// 自定义解析器
String input = "12/28/2019";
try {
DateTimeFormatter formatter = DateTimeFormatter.ofPattern("MM/dd/yyyy");
LocalDate ld3 = LocalDate.parse(input, formatter);
System.out.println("自定义解析的日期: " + ld3);
} catch (DateTimeParseException ex){
System.out.println("无法解析: " + ex);
}
}
```

以下是结果。

```
解析的日期: 2019-12-28
解析的日期时间: 2019-12-28T08:44
不同的解析器: 2019-12-28
自定义解析的日期: 2019-12-28
主方法是:
public static void main(String[] args) {
parseDateTime();
}
```

### 工作原理

日期时间 API 的时间类包含一个 `parse()` 方法，该方法使用指定的格式解析给定的输入字符串。默认情况下，`parse()` 方法的格式基于目标对象的默认 `DateTimeFormatter`。例如，要解析字符串 `"2019-01-01"`，可以调用默认的 `LocalDate.parse()` 方法。

```
LocalDate date = LocalDate.parse("2019-01-01");
```

但是，可以将另一个 `DateTimeFormatter` 指定为 `parse()` 方法的第二个参数。`DateTimeFormatter` 是一个用于格式化和打印日期和时间的最终类。它包含几个内置的格式化器，可以指定这些格式化器来将字符串强制转换为日期时间对象。通常，需要将文本字符串解析为日期时间对象。借助许多核心日期时间类内置的 `parse()` 方法，此类任务变得简单。

## 4.17 格式化日期以供显示

### 问题

你的应用程序需要使用特定格式显示日期。你希望定义该格式一次，并将其应用于所有需要显示的日期。


好的，作为高级文档工程师和翻译员，我将严格遵循您提供的注意事项和示例，将给定的英文文本翻译成中文。


### 解决方案 1

利用日期时间 API 中的 `DateTimeFormatter` 类，根据您想要使用的模式来格式化日期和时间。`DateTimeFormatter` 类包含一个 `ofPattern()` 方法，该方法接受一个字符串模式参数来指定所需的模式。每个时间日期类都包含一个 `format()` 方法，该方法接受一个 `DateTimeFormatter` 并返回目标日期时间对象的字符串格式。在下面的代码行中，演示了 `DateTimeFormatter` 的使用。

```
public static void formatting() {
try {
DateTimeFormatter dateFormatter = DateTimeFormatter.ofPattern("MMMM dd yyyy");
LocalDateTime now = LocalDateTime.now();
String output = now.format(dateFormatter);
System.out.println(output);
DateTimeFormatter dateFormatter2 = DateTimeFormatter.ofPattern("MM/dd/YY HH:mm:ss");
String output2 = now.format(dateFormatter2);
System.out.println(output2);
DateTimeFormatter dateFormatter3 = DateTimeFormatter.ofPattern("hh 'o''clock' a, zzzz");
ZonedDateTime zdt = ZonedDateTime.now();
String output3 = zdt.format(dateFormatter3);
System.out.println(output3);
} catch (DateTimeException ex) {
System.out.println("Cannot be formatted: " + ex);
}
}
```

以下是输出结果。

```
ottobre 13 2021
10/13/21 22:44:00
10 o'clock PM, Ora legale dell’Europa centrale
```

### 解决方案 2

使用 `java.util.Calendar` 类获取所需的日期，然后使用 `java.text.SimpleDateFormat` 类格式化该日期。以下示例演示了 `SimpleDateFormat` 类的使用。

```
public static void formatExamplesCalendar() {
// Create new calendar
Calendar cal = Calendar.getInstance();
// Create instance of SimpleDateFormat class using pattern
SimpleDateFormat dateFormatter1 = new SimpleDateFormat("MMMMM dd yyyy");
String result = null;
result = dateFormatter1.format(cal.getTime());
System.out.println(result);
dateFormatter1.applyPattern("MM/dd/YY hh:mm:ss");
result = dateFormatter1.format(cal.getTime());
System.out.println(result);
dateFormatter1.applyPattern("hh 'o''clock' a, zzzz");
result = dateFormatter1.format(cal.getTime());
System.out.println(result);
}
```

运行此示例将产生以下结果。

```
ottobre 13 2021
10/13/21 10:44:01
10 o'clock PM, Ora legale dell’Europa centrale
主方法是：
public static void main(String[] args) {
formatting();
formatExamplesCalendar();
}
```

从结果可以看出，`DateTimeFormatter` 和 `SimpleDateFormat` 类使得将日期转换为几乎任何格式变得容易。

### 工作原理

日期格式化是任何程序中的常见问题。人们希望在不同的情况下以特定的格式查看日期。Java 语言包含几个方便的工具来正确格式化日期时间数据。具体来说，较新的 API 包含 `DateTimeFormatter` 类，而旧版本的 Java SE 包含 `SimpleDateFormat` 类，这两个类都可以在执行格式化过程中派上用场。

`DateTimeFormatter` 类是一个最终类，其主要目的是打印和格式化日期时间对象。要获取一个可应用于对象的 `DateTimeFormatter`，请调用 `DateTimeFormatter.ofPattern()` 方法，并传入表示所需输出的字符串模式。`SimpleDateFormat` 类是在旧版本的 Java 中创建的，因此您无需对给定的日期进行手动转换。

注意

不同的区域设置使用不同的日期格式，而 `SimpleDateFormat` 类有助于进行特定于区域设置的格式化。

要使用该类，必须实例化一个实例，可以通过将字符串模式作为参数传递给构造函数，或者完全不传递参数给构造函数。字符串模式提供了一个模板，该模板应应用于给定的日期，然后返回一个以给定模式样式表示日期的字符串。一个模式由许多不同的字符组合而成。

任何模式字符都可以放在一个字符串中，然后传递给 `SimpleDateFormat` 类。如果在实例化该类时没有传递模式，则可以在以后使用该类的 `applyPattern()` 方法应用该模式。当您想要更改已实例化的 `SimpleDateFormat` 对象的模式时，`applyPattern()` 方法也很方便，如本解决方案中所示。以下代码片段演示了模式的应用。

```
SimpleDateFormat dateFormatter1 = new SimpleDateFormat("MMMMM dd yyyy");
dateFormatter1.applyPattern("MM/dd/YY hh:mm:ss");
```

一旦将模式应用于 `SimpleDateFormat` 对象，就可以将表示时间的 long 值传递给 `SimpleDateFormat` 对象的 `format()` 方法。`format()` 方法返回使用所应用模式格式化后的给定日期/时间。然后，可以根据应用程序的需要使用基于字符串的结果。

## 4.18 编写可读的数字字面量

### 问题

应用程序中的某些数字字面量相当长，您希望更容易一眼看出数字有多大。

### 解决方案

在较大的数字中使用下划线代替逗号或小数点，使其更具可读性。以下代码展示了一些通过使用下划线代替逗号来使数字字面量更具可读性的示例。

```
public static void main(String[] args) {
int million = 1_000_000;
int billion = 1_000_000_000;
float ten_pct = 1_0f;
double exp = 1_234_56.78_9e2;
System.out.println(million);
System.out.println(billion);
System.out.println(ten_pct);
System.out.println(exp);
}
输出结果为：

10.0
1.23456789E7
```

注意

除非末尾的“f”指示该值为浮点数，否则小数点值会自动默认为 double 值。

### 工作原理

有时处理大数字会变得繁琐且难以阅读。从 Java 7 开始，可以在数字字面量中使用下划线，使代码更易于阅读。下划线可以出现在数字字面量中数字之间的任何位置。这允许使用下划线代替逗号或空格来分隔数字，使其更易于阅读。

注意

下划线不能放在数字的开头或结尾、小数点或浮点字面量旁边、*F* 或 *L* 后缀之前，或者期望数字字符串的位置。

## 4.19 声明二进制字面量

### 问题

您正在开发一个需要声明二进制数的应用程序。

### 解决方案

使用二进制字面量使您的代码可读。以下代码片段演示了二进制字面量的使用。

```
public static void main(String[] args) {
int bin1 = 0b1100;
short bin2 = 0B010101;
short bin3 = (short) 0b1001100110011001;
System.out.println(bin1);
System.out.println(bin2);
System.out.println(bin3);
}
```

这将产生以下输出。

```

```

### 工作原理

二进制字面量从 Java 7 开始成为 Java 语言的一部分。byte、short、int 和 long 类型可以使用二进制数字系统表示。此功能有助于使二进制数在代码中更容易识别。要使用二进制格式，只需在数字前加上 `0b` 或 `0B` 前缀。

## 4.20 一天中的时段

### 问题

您希望将一天中的时段表示为“在早上”、“在下午”或“在晚上”，而不是 a.m. 或 p.m.。

### 解决方案

Java 16 通过一个新的格式化模式 B 实现了这一目标。在以下示例中，一天中的时段会根据一天中的时间被翻译成文本。

```
public static void main(String[] args) {
final String whatPeriodOfTheDay     = DateTimeFormatter.ofPattern("B").format(LocalTime.now());
System.out.println("Pattern B: " + whatPeriodOfTheDay);
}
```

以下是意大利语的输出。

```
Pattern B: del pomeriggio (in the afternoon)
```

### 工作原理

在 Java 16 中，一天中的时段被添加到了 `java.time` 格式中。



## 4.21 小结

数字和日期在大多数应用程序中扮演着不可或缺的角色。本章回顾了一些可用于数字舍入、格式化以及生成随机值的技术，并简要概述了一些常用的日期时间特性。

