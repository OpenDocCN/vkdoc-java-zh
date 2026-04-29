# 3. 字符串

本章重点介绍一些最常见的 `String` 方法以及处理 `String` 对象的技术。事实上，字符串是任何编程语言中最常用的数据类型之一。它们可用于从键盘获取文本、向命令行打印消息等。鉴于字符串的使用如此频繁，随着时间的推移，`String` 对象中添加了许多功能，使其更易于使用。毕竟，字符串在 Java 中是一个对象，因此它包含可以操作字符串内容的方法。

## 3.1 紧凑字符串

自 Java 语言问世以来，字符串一直以 UTF-16 `char` 类型的数组形式存储。char 数组为每个字符占用两个字节，由于字符串在我们的应用程序中经常使用，这最终会导致较大的内存堆。在 Java 9 中，字符串以 byte 类型的数组形式存储，存储的字符要么编码为 ISO-8859-1/Latin-1（每个字符一个字节），要么编码为 UTF-16（每个字符两个字节）。char 数组上还有一个编码标志，指示字符串使用了哪种编码类型。这些更改也称为*紧凑字符串*。

## 3.2 获取字符串的子部分

### 问题

你想要检索字符串的一部分。

### 解决方案

使用 `substring()` 方法获取两个不同位置之间的字符串部分。在以下解决方案中，创建了一个字符串，然后使用 `substring()` 方法打印出该字符串的不同部分。

```
public static void substringExample(){
String originalString = "This is the original String";
System.out.println(originalString.substring(0, originalString.length()));
System.out.println(originalString.substring(5, 20));
System.out.println(originalString.substring(12));
}
```

运行此方法将产生以下结果。

```
This is the original String
is the original
original String
```

### 工作原理

`String` 对象包含许多辅助方法。其中一种方法是 `substring()`，它可以返回字符串的部分内容。`substring()` 方法有两种变体。一种接受单个参数，即起始索引。另一种接受两个参数：`startingindex` 和 `endingindex`。`substring()` 方法有两种变体，使得第二个参数看起来是可选的；如果未指定，则使用调用字符串的长度代替。需要注意的是，索引从 0 开始，因此字符串中的第一个位置的索引为 0，依此类推。

从本技巧的解决方案中可以看出，第一次使用 `substring()` 打印出了字符串的全部内容。这是因为传递给 `substring()` 方法的第一个参数是 0，第二个参数是原始字符串的长度。在 `substring()` 的第二个示例中，使用索引 5 作为第一个参数，索引 20 作为第二个参数。这实际上只返回了字符串的一部分，从字符串中位于第六个位置（即索引 5，因为第一个位置的索引为 0）的字符开始，到字符串中位于第二十个位置（索引 19）的字符结束。第三个示例仅指定了一个参数；因此，原始字符串从该参数指定的位置开始。

注意

`substring()` 方法只接受正整数。如果你尝试传递负值，则会抛出 `java.lang.StringIndexOutOfBoundsException` 异常。

## 3.3 比较字符串

### 问题

你正在编写的应用程序需要能够比较两个或多个字符串值。

### 解决方案

使用内置的 `equals()`、`equalsIgnoreCase()`、`compareTo()` 和 `compareToIgnoreCase()` 方法来比较字符串中包含的值。以下是使用不同字符串比较操作的一系列测试。

如你所见，使用了各种 `if` 语句在比较相等时打印消息。

```
String one = "one";
String two = "two";
String var1 = "one";
String var2 = "Two";
String pieceone = "o";
String piecetwo = "ne";
// 比较相等
if (one.equals(var1)){
System.out.println ("使用 equals 方法，字符串 one 等于 var1");
}
// 比较不相等
if (one.equals(two)){
System.out.println ("使用 equals 方法，字符串 one 等于 two");
}
// 比较不相等
if (two.equals(var2)){
System.out.println ("使用 equals 方法，字符串 two 等于 var2");
}
// 比较相等，但未使用 == 直接比较字符串值
if (one == var1){
System.out.println ("使用 ==，字符串 one 等于 var1");
}
// 比较相等
if (two.equalsIgnoreCase(var2)){
System.out.println ("使用 equalsIgnoreCase 方法，字符串 two 等于 var2");
}
String piecedTogether = pieceone + piecetwo;
// 比较相等
if (one.equals(piecedTogether)){
System.out.println("使用 equals 方法，这些字符串包含相同的值");
}
// 使用 == 比较不相等
if (one == piecedTogether) {
System.out.println("使用 ==，这些字符串包含相同的值");
}
// 比较相等
if (one.compareTo(var1) == 0){
System.out.println("使用 compareTo() 方法，One 等于 var1");
}
if (one.compareToIgnoreCase(var1) == 0){
System.out.println("使用 compareToIgnoreCase() 方法，One 等于 var1");
}
```

结果输出如下。

```
使用 equals 方法，字符串 one 等于 var1
使用 ==，字符串 one 等于 var1
使用 equalsIgnoreCase 方法，字符串 two 等于 var2
使用 equals 方法，这些字符串包含相同的值
使用 compareTo() 方法，One 等于 var1
使用 compareToIgnoreCase() 方法，One 等于 var1
```



### 工作原理

在使用编程语言时，比较两个或多个值（尤其是字符串值）可能会比较棘手。在 Java 语言中，比较字符串可以相当直接，但需牢记一点：你*不应*使用 `==` 进行字符串比较。这是因为比较运算符 (`==`) 比较的是引用，而非字符串的值。在 Java 中处理字符串时，最容易犯的错误之一就是使用比较运算符，但你必须避免这样做，因为结果可能千差万别。

注意

Java 使用字符串驻留（interning）来提升性能。这意味着 JVM 包含一个驻留字符串表，每次对字符串调用 `intern()` 方法时，都会在该表中执行查找以寻找匹配项。驻留机制会返回字符串的规范表示形式。如果表中没有匹配的字符串，则将该字符串添加进去，并返回其引用。如果字符串已存在于表中，则直接返回其引用。Java 会自动驻留字符串字面量，这可能导致使用 `==` 比较运算符时出现差异。

在本方案的解决方案中，你可以看到比较字符串值的各种技巧。`equals()` 方法是每个 Java 对象的一部分。Java 字符串的 `equals()` 方法已被重写，用于比较字符串中包含的值，而不是对象本身。从本方案解决方案中提取的以下示例可以看出，`equals()` 方法是比较字符串的安全方式。

```
// 比较结果相等
if (one.equals(var1)){
System.out.println ("使用 equals 方法，字符串 one 等于 var1");
}
// 比较结果不相等
if (one.equals(two)){
System.out.println ("使用 equals 方法，字符串 one 等于 two");
}
```

`equals()` 方法首先使用 `==` 运算符检查字符串是否引用同一个对象；如果是，则返回 `true`。如果它们不引用同一个对象，`equals()` 会逐个字符地比较每个字符串，以确定被比较的字符串是否包含相同的值。如果其中一个字符串的大小写与另一个不同呢？使用 `equals()` 比较时它们还会相等吗？答案是否定的，这正是 `equalsIgnoreCase()` 方法被创建的原因。使用 `equalsIgnoreCase()` 比较两个值时，会比较每个字符，而忽略大小写。以下示例摘自本方案的解决方案。

```
// 比较结果不相等
if (two.equals(var2)){
System.out.println ("使用 equals 方法，字符串 two 等于 var2");
}
// 比较结果相等
if (two.equalsIgnoreCase(var2)){
System.out.println ("使用 equalsIgnoreCase 方法，字符串 two 等于 var2");
}
```

`compareTo()` 和 `compareToIgnoreCase()` 方法对字符串执行字典序比较。这种比较基于字符串中每个字符的 Unicode 值。如果该字符串在字典序上位于参数字符串之前，则结果为负整数。如果该字符串在字典序上位于参数字符串之后，则结果为正整数。如果两个字符串在字典序上相等，则结果为零。以下摘自本方案解决方案的代码片段演示了 `compareTo()` 方法。

```
// 比较结果相等
if (one.compareTo(var1) == 0){
System.out.println("使用 compareTo() 方法，One 等于 var1");
}
```

不可避免的是，许多应用程序都包含需要在某种程度上比较字符串的代码。下次当你需要编写一个涉及字符串比较的应用程序时，在编写代码之前，请先考虑本方案中讨论的信息。

## 3.4 修剪空白

### 问题

你正在处理的某个字符串两端包含一些空白字符。你想要去除这些空白。

### 解决方案

使用字符串的 `trim()` 方法来消除空白。在以下示例中，打印了一个句子，其两侧包含空白。然后使用 `trim()` 方法再次打印同一个句子以去除空白，以便看到变化。

```
String myString = " 这是一个包含空白的字符串。   ";
System.out.println(myString);
System.out.println(myString.trim());
```

输出结果如下所示。

```
这是一个包含空白的字符串。
这是一个包含空白的字符串。
```

### 工作原理

无论我们多么小心，在处理文本字符串时，空白始终可能成为一个问题。在将字符串与匹配值进行比较时尤其如此。如果字符串包含意外的空白字符，对于模式搜索程序来说可能是灾难性的。幸运的是，Java 的 `String` 对象包含 `trim()` 方法，可以自动移除任何给定字符串两端的空白。

`trim()` 方法非常易于使用。从本方案的解决方案可以看出，使用 `trim()` 方法只需对任何给定字符串进行调用即可。`String` 对象包含许多辅助方法，使得处理它们非常方便。毕竟，字符串是任何编程语言中最常用的数据类型之一，所以它们最好易于使用！`trim()` 方法返回原始字符串的一个副本，其中移除了所有前导和尾随空白。但是，如果没有需要移除的空白，`trim()` 方法会返回原始字符串实例。没有比这更简单的了！

## 3.5 检测空白字符串

### 问题

你想知道字符串是否为空或仅包含空白。

### 解决方案

使用 `java.lang.String.isBlank()` 方法，该方法返回一个布尔变量。

```
boolean blank = string.isBlank();
```

在以下示例中，只有当字符串为空或仅包含空白字符时，该方法才返回 true。

示例如下。

```
System.out.println("".isBlank());
在控制台窗格中打印 True，而：
System.out.println(" ".isBlank());
在控制台窗格中打印 True：
但是：
System.out.println("Luciano Manelli".isBlank());
在控制台窗格中打印 False。
```

### 工作原理

在 Java 11 之前，只有 `java.lang.String.isEmpty()` 方法，用于检查此字符串是否为空。如果长度为 0，则返回 true；否则返回 false。

示例如下。

```
System.out.println("".isEmpty());
在控制台窗格中打印 True，而：
System.out.println(" ".isEmpty());
在控制台窗格中打印 False。
在 Java 11 中，新的 isBlank() 方法等同于 trim().isEmpty()。
```

## 3.6 剥离空白

### 问题

你想移除空白。

### 解决方案

使用 `java.lang.String.strip()` 方法，该方法会删除空白，如清单 3-1 所示。

```
class StripClassExample
{
public static void main(String[] args)
{
String nameString = "  Ciao Sara!   ";
System.out.println( nameString.strip() );
System.out.println( nameString.stripLeading() );
System.out.println( nameString.stripTrailing() );
}
}
清单 3-1
使用 strip 方法
```

它返回以下输出。

```
//"Ciao Sara!"
//"Ciao Sara!   "
//"  Ciao Sara!"
```

### 工作原理

strip 方法是在 Java 11 中引入的，用于移除字符串开头和结尾的空白。它产生与 `trim()` 相同的结果，而 `stripLeading()` 移除开头的空白，`stripTrailing()` 移除结尾的空白。

## 3.7 分割字符串行

### 问题

你想将字符串分割成多行。



### 解决方案

使用 `java.lang.String.lines()` 方法实现按行分割，如代码清单 3-2 所示。

```
import java.util.stream.Stream;
class LineTerminators {
public static void main(String[] args) {
String nameString = "Luciano \nManelli \nTaranto";
Stream stringStream = nameString.lines();
stringStream.forEach(System.out::println);
}
}
代码清单 3-2
使用 lines 方法
```

该方法返回以下输出。

```
Luciano
Manelli
Taranto
```

### 实现原理

Java 11 在 `String` 类中新增了一个方法。该方法可逐行读取文件内容，并基于换行符（如换行符 \n、回车符 \r 或回车符后紧跟换行符 \r\n）从字符串中提取行流并返回。

## 3.8 重复字符串

### 问题

你需要重复字符串。

### 解决方案

使用 `java.lang.String.repeat(int)` 方法，如下所示。

```
String nameString = "luciano";
String repeatString = nameString.repeat(3);
```

该方法返回以下输出。

```
lucianolucianoluciano
```

### 实现原理

Java 11 引入了 `repeat` 方法，该方法返回一个字符串，该字符串由原字符串重复 int（计数）参数指定的次数后拼接而成。

## 3.9 更改字符串的大小写

### 问题

你的应用程序中有一部分包含区分大小写的字符串值。你希望在处理所有字符串之前将其全部转换为大写或小写，以避免后续出现大小写敏感问题。

### 解决方案

使用 `toUpperCase()` 和 `toLowerCase()` 方法。`String` 对象提供了这两个辅助方法，用于更改给定字符串中所有字符的大小写。

例如，给定以下代码中的字符串，分别调用这两个方法。

```
String str = "This String will change case.";
System.out.println(str.toUpperCase());
System.out.println(str.toLowerCase());
```

将产生以下输出。

```
THIS STRING WILL CHANGE CASE.
this string will change case.
```

### 实现原理

为确保给定字符串中每个字符的大小写统一为大写或小写，请分别使用 `toUpperCase()` 和 `toLowerCase()` 方法。使用这些方法时需要注意几点。首先，如果给定字符串包含大写字母，并且对其调用 `toUpperCase()` 方法，则大写字母会被忽略。同样的概念也适用于调用 `toLowerCase()` 方法。给定字符串中包含的任何标点符号或数字也会被忽略。

这两个方法各有两种变体。一种变体不接受任何参数，另一种变体接受一个与所需区域设置相关的参数。不带参数调用这些方法将使用默认区域设置进行大小写转换。如果你想使用不同的区域设置，可以将所需的区域设置作为参数传递给接受参数的方法变体。例如，如果你想使用意大利语或法语区域设置，可以使用以下代码。

```
System.out.println(str.toUpperCase(Locale.ITALIAN));
System.out.println(str.toUpperCase(new Locale("it","US")));
System.out.println(str.toLowerCase(new Locale("fr", "CA")));
```

以下是输出结果。

```
THIS STRING WILL CHANGE CASE.
THIS STRING WILL CHANGE CASE.
this string will change case.
```

使用这些方法将字符串转换为大写或小写可以简化操作。它们对于比较从应用程序输入中获取的字符串也非常有用。假设用户被提示输入用户名，并将结果保存到一个字符串中。稍后在程序中，该字符串将与数据库中存储的所有用户名进行比较，以确保用户名有效。如果输入用户名的用户将首字母大写会怎样？如果用户名在数据库中以全大写形式存储又会怎样？比较结果将永远不会相等。在这种情况下，开发人员可以使用 `toUpperCase()` 方法来缓解问题。对正在比较的字符串调用此方法，将使得比较双方的大小写保持一致。

## 3.10 拼接字符串

### 问题

你有多个字符串需要合并成一个。

### 解决方案 1

如果你想将字符串依次追加到末尾，请使用 `concat()` 方法。以下示例演示了 `concat()` 方法的用法。

```
String one = "Hello";
String two = "Java17";
String result = one.concat(" ".concat(two));
```

以下是结果。

```
Hello Java
```

### 解决方案 2

使用连接运算符以简写形式组合字符串。在以下示例中，两个字符串之间放置了一个空格字符。

```
String one = "Hello";
String two = "Java17";
String result = one + " " + two;
```

以下是结果。

```
Hello Java17
```

### 解决方案 3

使用 `StringBuilder` 或 `StringBuffer` 来组合字符串。以下示例演示了使用 `StringBuffer` 拼接两个字符串。

```
String one = "Hello";
String two = "Java17";
StringBuffer buffer = new StringBuffer();
buffer.append(one).append(" ").append(two);
String result = buffer.toString();
System.out.println(result);
```

以下是结果。

```
Hello Java17
```

### 实现原理

Java 语言提供了几种不同的选项来拼接文本字符串。虽然它们没有优劣之分，但你可能发现在不同情况下某种方法效果更好。`concat()` 方法是一个内置的字符串辅助方法。它能够将一个字符串追加到另一个字符串的末尾，如本技巧的解决方案 1 所示。`concat()` 方法接受任何字符串值；因此，如果需要，你可以显式键入一个字符串值作为参数传递。如解决方案 1 所示，只需将一个字符串作为参数传递给该方法，它就会被追加到调用该方法的字符串末尾。但是，如果你想在两个字符串之间添加一个空格字符，可以通过传递一个空格字符和要追加的字符串来实现，如下所示。

```
String result = one.concat(" ".concat(two));
```

如你所见，能够将任何字符串或字符串组合传递给 `concat()` 方法使其非常有用。由于所有辅助方法都返回应用了辅助方法功能的原始字符串副本，因此你可以将调用其他辅助方法的字符串传递给 `concat()`（或任何其他字符串辅助方法）。假设你想显示文本 `"Hello Java"` 而不是 `"Hello Java17"`。以下字符串辅助方法的组合可以实现这一点。

```
String one = "Hello";
String two = "Java17";
String result = one.concat(" ".concat(two.substring(0, two.length()-2)));
```

连接运算符（`+`）可以组合任意两个字符串。它几乎可以被视为 `concat()` 方法的简写形式。本示例解决方案 3 中演示的最后一种技术是使用 `StringBuffer`，它是一个可变的字符序列，与字符串非常相似，区别在于它可以通过方法调用进行修改。`StringBuffer` 类包含几个用于构建和操作字符序列的辅助方法。在解决方案中，`append()` 方法追加了两个字符串值。`append()` 方法将作为参数传递的字符串放置在 `StringBuffer` 的末尾。

## 3.11 将字符串转换为数值

### 问题

你希望能够将以字符串形式存储的任何数值转换为整数。



### 解决方案 1

使用 `Integer.valueOf()` 辅助方法将字符串转换为 int 数据类型。示例如下。

```
String one = "1";
String two = "2";
int result = Integer.valueOf(one) + Integer.valueOf(two);
```

如你所见，两个字符串变量都被转换为整数值。之后，它们执行加法计算，并将结果存储到 `int` 变量中。

最终结果为 3。

注意

此示例中使用了一种称为*自动装箱*的技术。自动装箱是 Java 语言的一项特性，它能自动将原始类型值转换为其对应的包装类。例如，当你将 int 值赋给 Integer 对象时就会发生这种情况。类似地，当你反向转换（从包装类转换为原始类型）时，会自动发生*拆箱*。

### 解决方案 2

使用 `Integer.parseInt()` 辅助方法将字符串转换为 int 数据类型。示例如下。

```
String one = "1";
String two = "2";
int result = Integer.parseInt(one) + Integer.parseInt(two);
System.out.println(result);
```

最终结果为 3。

### 工作原理

`Integer` 类包含 `valueOf()` 和 `parseInt()` 方法，它们可以将字符串或 int 类型转换为整数。`Integer` 类的 `valueOf()` 方法有两种不同的形式，可以将字符串转换为整数值。它们之间的区别在于接受的参数数量不同。第一种 `valueOf()` 方法只接受一个字符串参数。如果可能，该字符串会被解析为整数值，然后返回一个包含该字符串值的整数。如果字符串无法正确转换为整数，则该方法会抛出 `NumberFormatException`。

第二种 `valueOf()` 方法接受两个参数：一个被解析为整数的字符串参数，以及一个表示转换所用基数的 int 值。

注意

许多 Java 类型类都包含 `valueOf()` 方法，可用于将不同类型转换为该类的类型。`String` 类就是如此，因为它包含许多不同的 `valueOf()` 方法，可用于转换。

`Integer` 类的 `parseInt()` 方法也有两种不同的形式。一种接受一个参数：你想要转换为整数的字符串。另一种形式接受两个参数：你想要转换为整数的字符串和基数。第一种格式使用最广泛，它将字符串参数解析为有符号的十进制整数。如果字符串中不包含可解析的无符号整数，则会抛出 `NumberFormatException`。第二种格式使用较少，如果字符串中包含可解析的无符号整数，它会返回一个 `Integer` 对象，该对象保存了字符串参数在给定基数下表示的值。

注意

`parseInt()` 和 `valueOf()` 之间最大的区别之一是，`parseInt()` 返回一个 int，而 `valueOf()` 从缓存中返回一个 Integer。

## 3.12 遍历字符串中的字符

### 问题

你想要遍历文本字符串中的字符，以便在字符级别对其进行操作。

### 解决方案

使用字符串辅助方法的组合来访问字符串的字符级别。如果在循环的上下文中使用字符串辅助方法，你可以轻松地按字符遍历字符串。在以下示例中，名为 `str` 的字符串使用 `toCharArray()` 方法进行分解。

```
String str = "Break down into chars";
System.out.println(str);
for (char chr : str.toCharArray()){
System.out.println(chr);
}
```

同样的策略也可以用于传统的 `for` 循环。可以创建一个索引，以便使用 `charAt()` 方法访问字符串中的每个字符。

```
for (int x = 0; x <= str.length()-1; x++){
System.out.println(str.charAt(x));
}
```

两种解决方案都会产生以下结果。

```
B
r
e
a
k
d
o
w
n
i
n
t
o
c
h
a
r
s
```

注意

第一个使用 `toCharArray()` 的示例会生成一个新的字符数组。因此，第二个使用传统 `for` 循环的示例可能性能更快。

### 工作原理

`String` 对象包含可用于执行各种任务的方法。本攻略的解决方案演示了几种不同的 `String` 方法。可以对字符串调用 `toCharArray()` 方法，将其分解为字符，然后将这些字符存储到数组中。这个方法非常强大，在需要执行此任务时可以节省一些时间。调用 `toCharArray()` 方法的结果是一个 `char[]`，然后可以使用索引对其进行遍历。本攻略的解决方案就是这种情况。一个增强型 `for` 循环遍历 `char[]` 的内容，并打印出其中的每个元素。

`length()` 方法用于查找字符串中包含的字符数。结果是一个 int 值，在 `for` 循环的上下文中非常有用，如本攻略的解决方案所示。在第二个示例中，`length()` 方法查找字符串中的字符数，以便可以使用 `charAt()` 方法对其进行迭代。`charAt()` 方法接受一个 int 索引值作为参数，并返回字符串中位于给定索引处的字符。

通常，组合使用两个或多个 `String` 方法可以获得各种结果。在这种情况下，在同一个代码块中使用 `length()` 和 `charAt()` 方法提供了将字符串分解为字符的能力。

## 3.13 查找文本匹配

### 问题

你想要在文本正文中搜索特定的字符序列。

### 解决方案 1

使用正则表达式和 `matches()` 辅助方法来确定存在多少个匹配项。为此，只需将一个表示正则表达式的字符串传递给 `matches()` 方法，该方法作用于你想要匹配的任何字符串。这样做时，会将字符串与调用 `matches()` 方法的字符串进行比较。评估后，`matches()` 方法会返回一个布尔结果，指示是否匹配。以下代码摘录包含一系列使用此技术的示例。代码中的注释解释了每个匹配测试。

```
String str = "Here is a long String...let's find a match!";
// 这将返回 "true"，因为它是精确匹配
boolean result = str.matches("Here is a long String...let's find a match!");
System.out.println(result);
// 这将返回 "false"，因为整个字符串不匹配
result = str.matches("Here is a long String...");
System.out.println(result);
str = "true";
// 这将测试大写和小写的 "T"...这将返回 TRUE
result = str.matches("[Tt]rue");
System.out.println(result);
// 这将测试其中之一
result = str.matches("[Tt]rue|[Ff]alse]");
System.out.println(result);
// 这将测试是否存在任何数字，在这种情况下，
// 编写此字符串的人可以喜欢任何 Java 版本！
str = "I love Java 8!";
result = str.matches("I love Java [0-9]!");
System.out.println(result);
// 这也将测试为 TRUE...
str = "I love Java 7!";
result = str.matches("I love Java [0-9]!");
System.out.println(result);
// 以下将对任何名称仅包含一个单词的语言测试为 TRUE。
// 这是因为它测试任何字母数字组合。注意数字序列之间的空格字符...
result = str.matches("I love .*[ 0-9]!");
System.out.println(result);
// 以下字符串也匹配。
str = "I love Jython 2.5.4!";
result = str.matches("I love .*[ 0-9]!");
System.out.println(result);
```

示例中打印出的每个结果都是 `true`，除了第二个示例，因为它不匹配。



### 解决方案 2

使用正则表达式的 `Pattern` 和 `Matcher` 类，可以获得比 `matches()` 方法性能更优、功能更通用的匹配方案。虽然 `matches()` 方法在大多数情况下都能完成任务，但有时你需要更灵活的匹配方式。使用此方案需要三个步骤。

1.  将模式编译成一个 `Pattern` 对象。

2.  在 `Pattern` 对象上调用 `matcher()` 方法来构造一个 `Matcher` 对象。

3.  在 `Matcher` 对象上调用 `matches()` 方法。

以下示例代码演示了 `Pattern` 对象和 `Matcher` 对象的使用技巧。

```
String str = "I love Java 17!";
boolean result = false;
Pattern pattern = Pattern.compile("I love .*[ 0-9]!");
Matcher matcher = pattern.matcher(str);
result = matcher.matches();
System.out.println(result);
```

上述示例与解决方案 1 中演示的变体一样，会输出 `TRUE` 值。

### 工作原理

正则表达式是查找匹配项的绝佳方式，因为它们允许定义模式，从而使应用程序无需显式地查找精确的字符串匹配。对于匹配用户可能输入到程序中的某些文本，正则表达式非常有用。但是，如果你试图将字符串与程序中定义的字符串常量进行匹配，使用正则表达式可能有些大材小用，因为 `String` 类提供了许多可用于此类任务的方法。尽管如此，几乎每个开发者在职业生涯中都会遇到正则表达式派上用场的时候。如今几乎所有编程语言中都能找到正则表达式的身影。Java 让它们易于使用和理解。

注意

尽管正则表达式如今在许多不同的语言中使用，但每种语言的表达式语法各不相同。

使用正则表达式最简单的方法是在 `String` 对象上调用 `matches()` 方法。将正则表达式传递给 `matches()` 方法会返回一个布尔结果，指示该字符串是否与给定的正则表达式模式匹配。此时，了解什么是正则表达式以及它是如何工作的会很有帮助。

*正则表达式*是一种字符串模式，可以与其他字符串进行匹配以确定其内容。正则表达式可以包含多种不同的模式，使其具有动态性，能够匹配许多包含相同格式的不同字符串。例如，在本技巧的解决方案中，以下代码可以匹配多个不同的字符串。

```
result = str.matches("I love Java [0-9]!");
```

此示例中的正则表达式字符串是 `"I love Java [0-9]!"`，它包含模式 `[0-9]`，该模式代表 0 到 9 之间的任意数字。因此，任何内容为 `"I love Java"` 后跟数字 0 到 9 以及感叹号的字符串都能匹配该正则表达式字符串。要查看正则表达式中使用的所有不同模式列表，请参阅前文注意中 URL 提供的在线文档。

`Pattern` 和 `Matcher` 对象的组合也可以实现与字符串 `matcher()` 方法类似的结果。`Pattern` 对象可以将字符串编译成正则表达式模式。如果模式被多次使用，编译后的模式可以为应用程序带来性能提升。你可以将相同的基于字符串的正则表达式传递给 `Pattern.compile()` 方法，就像传递给字符串 `matches()` 方法一样。结果是一个编译后的 `Pattern` 对象，可以将其与字符串进行匹配比较。可以通过对给定字符串调用 `Pattern` 对象的 `matcher()` 方法来获得 `Matcher` 对象。获得 `Matcher` 对象后，它可以使用以下三种方法中的任何一种将给定字符串与模式进行匹配，每种方法都返回一个指示是否匹配的布尔值。解决方案 2 中的以下三行代码可以作为使用 `Pattern.matches()` 方法的替代方案，但缺少了编译后模式的可重用性。

*   `Matcher` 对象的 `matches()` 方法尝试将整个输入字符串与模式进行匹配。

*   `Matcher` 对象的 `lookingAt()` 方法尝试从开头开始将输入字符串与模式进行匹配。

*   `Matcher` 对象的 `find()` 方法扫描输入序列，查找字符串中下一个匹配的序列。

```
Pattern pattern = Pattern.compile("I love .*[ 0-9]!");
Matcher matcher = pattern.matcher(str);
result = matcher.matches();
```

在本技巧的解决方案中，对 `Matcher` 对象调用了 `matches()` 方法来匹配整个字符串。在任何情况下，正则表达式对于将字符串与模式进行匹配都非常有用。处理正则表达式的技术在不同情况下可能有所不同，可以使用最适合的方法。

## 3.14 替换所有文本匹配项

### 问题

你已经在文本正文中搜索了特定的字符序列，并且希望将所有匹配项替换为另一个字符串值。

### 解决方案

使用正则表达式模式获取一个 `Matcher` 对象；然后使用 `Matcher` 对象的 `replaceAll()` 方法将所有匹配项替换为另一个字符串值。以下示例演示了此技巧。

```
String str = "I love Java 8!  It is my favorite language.  Java 8
is the " + "8th version of this great programming language.";
Pattern pattern = Pattern.compile("[0-9]");
Matcher matcher = pattern.matcher(str);
System.out.println("Original: " + str);
System.out.println(matcher.matches());
System.out.println("Replacement: " + matcher.replaceAll("17"));
```

此示例将产生以下结果。

```
Original: I love Java 8! It is my favorite language. Java 8 is the 8th version of this great programming language.
Replacement: I love Java 17! It is my favorite language. Java 17 is the 17th version of this great programming language.
```

### 工作原理

`Matcher` 对象的 `replaceAll()` 方法使得查找并替换文本正文中包含的字符串或字符串的一部分变得非常容易。要使用 `Matcher` 对象的 `replaceAll()` 方法，必须首先通过将正则表达式字符串模式传递给 `Pattern.compile()` 方法来编译一个 `Pattern` 对象。使用生成的 `Pattern` 对象，通过调用其 `matcher()` 方法来获取一个 `Matcher` 对象。以下代码行展示了如何完成此操作。

```
Pattern pattern = Pattern.compile("[0-9]");
Matcher matcher = pattern.matcher(str);
```

一旦获得了 `Matcher` 对象，就调用其 `replaceAll()` 方法，并传入你想要用来替换所有与编译模式匹配的文本的字符串。在本技巧的解决方案中，字符串 `"17"` 被传递给 `replaceAll()` 方法，因此它会替换字符串中所有与 `"[0-9]"` 模式匹配的区域。

## 3.15 确定文件后缀是否匹配给定字符串

### 问题

你正在从服务器读取一个文件，并且需要确定它是什么类型的文件以便正确读取。

### 解决方案

通过使用给定文件名的 `endsWith()` 方法来确定文件的后缀。在以下示例中，假设 `filename` 变量包含给定文件的名称。代码使用 `endsWith()` 方法来确定 `filename` 变量是否以特定字符串结尾。

```
if(filename.endsWith(".txt")){
System.out.println("Text file");
} else if (filename.endsWith(".doc")){
System.out.println("Document file");
} else if (filename.endsWith(".xls")){
System.out.println("Excel file");
} else if (filename.endsWith(".java")){
System.out.println("Java source file");
} else {
System.out.println("Other type of file");
}
```

假设文件名及其后缀都包含在 `filename` 变量中，这段代码会读取其后缀并确定给定变量所代表的文件类型。



### 工作原理

如前所述，`String` 对象包含许多可用于执行任务的辅助方法。`String` 对象的 `endsWith()` 方法接受一个字符序列，并返回一个布尔值，表示原始字符串是否以给定序列结尾。在本节解决方案中，`endsWith()` 方法被用于一个 `if` 块中。一系列文件后缀被传递给 `endsWith()` 方法，以判断 `filename` 变量所代表的文件类型。如果任何文件名后缀匹配，则会打印一行信息，说明该文件属于哪种类型。

## 3.16 创建包含动态信息的字符串

### 问题

你想要生成一个包含动态占位符的字符串，使得该字符串能够根据应用程序数据的变化而改变。

### 解决方案 1

利用内置的 `format()` 方法来生成包含动态数据占位符的字符串。以下示例演示了一个包含动态占位符的字符串，该占位符允许将不同的数据插入到同一个字符串中。随着温度变量的变化，字符串也会动态改变。

```
public static void main(String[] args){
double temperature = 37.1;
String temperatureString = "当前温度是 %.1f 摄氏度。";
System.out.println(String.format(temperatureString, temperature));
temperature = 38.4;
System.out.println(String.format(temperatureString, temperature));
}
```

输出结果如下：

```
当前温度是 37.1 摄氏度。
当前温度是 38.4 摄氏度。
```

### 解决方案 2

如果你希望直接打印字符串内容，而不是将其存储起来供以后使用，`System.out.printf()` 方法可以将动态值定位到字符串中。以下示例演示了与解决方案 1 相同的概念，但这次不是使用 `String.format()` 方法，而是直接打印字符串，并且传递给 `System.out.printf()` 方法的占位符会在运行时被动态内容替换。

```
public static void main(String[] args){
double temperature = 37.1;
System.out.printf("当前温度是 %.1f 摄氏度。\n", temperature);
temperature = 38.4;
System.out.printf("当前温度是 %.1f 摄氏度。", temperature);
}
```

输出结果如下：

```
当前温度是 37.1 摄氏度。
当前温度是 38.4 摄氏度。
```

### 工作原理

当你需要动态字符串内容时，`format()` 工具会非常有用。内置的 `format()` 方法允许你在字符串中放置一个占位符，使得该占位符在运行时被动态内容替换。`format` 方法接受一个字符串和一系列变量，这些变量会在运行时将字符串中的占位符替换为动态内容。占位符必须根据其替换内容的类型进行专门指定。每个占位符必须以 `%` 字符开头，以表示字符串中的占位符。占位符还可以包含标志、宽度和精度指示符，以帮助格式化动态值。每个占位符应按照以下格式构建。

```
%[flags][width][.precision]conversion_indicator
```

第二个解决方案演示了如何使用 `System.out.printf()` 方法，该方法接受与 `System.format()` 方法相同的参数。主要区别在于，`System.out.printf()` 方法便于打印格式化内容。如果你的应用程序需要存储格式化后的值，则更可能使用 `String.format()` 方法。

## 3.17 总结

本章涵盖了处理字符串的基础知识。虽然字符串看起来像是一串简单的字符，但它是一个包含许多方法的对象，这些方法对于获得所需结果非常有用。尽管字符串是不可变对象，但 `String` 类中的许多方法都包含字符串的副本，并根据请求进行了修改。本章还介绍了其中一些方法，演示了诸如拼接、获取子字符串、去除空白以及替换字符串部分等功能。

