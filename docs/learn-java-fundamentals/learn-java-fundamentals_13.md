# 13. String 和 StringBuffer

第 1 章在 `String[] args` 的上下文中简要提到了 `String` 类型。该类型在后续章节中出现过，但没有进行适当的讨论。本章通过正式介绍 `String` 来弥补这一疏忽。然后介绍 `String` 的配套类型：`StringBuffer`。

## String

`java.lang.String` 类描述了一个*字符串*，它是一个字符序列。你可以通过将这些字符放在一对双引号之间来字面地表示一个字符串（例如，`"The quick brown fox jumps over the lazy dog."`）。

### 创建字符串

`String` 提供了几个用于创建字符串的构造函数。这些构造函数包括 `String(char[] value)`，它创建一个包含 `value` 字符数组中字符的 `String` 对象。以下代码片段演示了此构造函数：

```
char[] c = new char[] { 'T', 'h', 'e', ' ', 'q', 'u', 'i', 'c', 'k', ' ',
'b', 'r', 'o', 'w', 'n', ' ', 'f', 'o', 'x', ' ',
'j', 'u', 'm', 'p', 's', ' ', 'o', 'v', 'e', 'r',
' ', 't', 'h', 'e', ' ', 'l', 'a', 'z', 'y', ' ',
'd', 'o', 'g', '.' };
String s = new String(c); // 将新创建的 String 对象的引用赋给 s。
```

你可能会认为我应该使用 `String(String original)` 构造函数，例如 `new String("The quick brown fox jumps over the lazy dog.")`。然而，这样做会是一个错误。

传递给构造函数的参数是另一个 `String` 对象。当你调用构造函数时，你是在复制该参数——你现在有了第二个 `String` 对象。

注意

你可能想探索用于创建 `String` 对象的其他构造函数。请查看 JDK 21 引用类型库的 `String` 类的 Java 文档（[``docs.oracle.com/en/java/javase/20/docs/api/java.base/java/lang/String.xhtml``](http://docs.oracle.com/en/java/javase/20/docs/api/java.base/java/lang/String.xhtml)），以了解这些构造函数以及 `String` 的方法。

通过将字符串字面量赋值给 `String` 变量来创建 `String` 对象要容易得多。以下示例演示了这一点：

```
String s = "The quick brown fox jumps over the lazy dog.";
```

这种赋值利用了*语法糖*（使语言更易于使用的语法）来更轻松地创建 `String` 对象。

### 比较字符串

另一个使 `String` 操作更加便捷的语法糖示例是通过 `==` 和 `!=` 运算符进行字符串比较。例如，`System.out.println("abc" == "abc");` 输出 `true`，`System.out.println("abc" != "Abc");` 输出 `true`。这之所以有效，是因为字符串驻留（interning），稍后会讨论。

你不能随意比较任何字符串。例如，你不能将基于字符串字面量的 `String` 对象（再次，请查看本章后面关于字符串驻留的讨论）与基于构造函数的 `String` 对象进行比较。例如，`System.out.println("abc" == new String("abc"));` 输出 `false`。

在比较 `String`（以及任何类型的对象）时，`==` 和 `!=` 运算符比较的是引用。由于字符串字面量涉及字符串驻留，因此只有一个 `String` 对象。将其引用与其自身引用进行比较会产生 true 结果。

基于引用的比较可以提升性能。通过 `==` 和 `!=` 比较两个对象，比通过 `java.lang.Object` 的 `equals(Object obj)` 方法的重载版本（该方法比较它们的各个字段）要快。（`equals()` 方法默认使用 `==` 进行比较。）

`String` 的 `equals(Object obj)` 方法将此字符串与指定的 `obj` 对象进行比较。当且仅当参数不是空引用，并且是一个与此对象表示相同字符序列的 `String` 对象时，结果才为 true。否则，返回 false。

`String` 还提供了一个 `boolean equalsIgnoreCase(String string)` 方法，该方法将此字符串与指定的 `string` 进行比较。此方法忽略大小写，这意味着大写和小写字母被视为相同。例如，在比较时，A 和 a 被视为相同的字符。

注意

请查看文档中关于 `compareTo()` 和 `compareToIgnoreCase()` 方法的介绍，它们也提供比较功能。与 `equals()` 和 `equalsIgnoreCase()` 不同，它们专为排序应用程序中的使用而设计。



### 拼接字符串

语法糖的第三个例子是通过 `+` 和 `+=` 运算符进行字符串拼接。在第 3 章中，我提到过 `+` 可用于拼接字符串。不过，我并未提及 `+=` 同样可以用于此目的。下面通过 `+` 和 `+=` 语法糖展示一个字符串拼接的示例：

```
String hello = "hello, ";
String world = "world";
String result = hello + world;
result += "!"; // 等价于 result = result + "!";
```

此示例执行完毕后，`result` 包含一个指向 `String` 对象的引用，该对象的内容为 `hello, world!`。

### 探索字符串方法

`String` 提供了用于对 `String` 对象执行操作的方法。以下列表列举了这些方法：

*   `char charAt(int index)`：返回指定 `index` 处的字符。第一个字符位于索引 0。如果 `index` 小于 0 或大于等于字符串长度，则抛出 `java.lang.IndexOutOfBoundsException`。

*   `int indexOf(String s)`：返回子串 `s` 在此字符串中首次出现的位置。如果未找到，则返回 -1。

*   `int lastIndexOf(String s)`：返回子串 `s` 在此字符串中最后一次出现的位置。如果未找到，则返回 -1。

*   `int length()`：返回此字符串的长度。

*   `boolean endsWith(String suffix)`：如果此字符串以 `suffix` 结尾，则返回 true；否则返回 false。

*   `String replace(char oldChar, char newChar)`：返回一个新的 `String` 对象，其中所有出现的 `oldChar` 都被替换为 `newChar`。

*   `String replaceAll(String regex, String replacement)`：返回一个新的 `String` 对象，其中此字符串中每个匹配 `regex` 所标识正则表达式的子串都被替换为 `replacement` 字符串。（*正则表达式*，也称为 *regex* 或 *regexp*，是一种基于字符串的模式，用于表示匹配该模式的所有字符串集合。本书不讨论正则表达式，因为我认为它并非基础特性。）

*   `String replaceFirst(String regex, String replacement)`：返回一个新的 `String` 对象，其中此字符串中第一个匹配 `regex` 所标识正则表达式的子串被替换为 `replacement` 字符串。

*   `boolean startsWith(String prefix)`：如果此字符串以 `prefix` 开头，则返回 `true`。

*   `String toLowerCase()`：返回一个新的 `String` 对象，其中所有大写字符都被转换为小写。

*   `String toUpperCase()`：返回一个新的 `String` 对象，其中所有小写字符都被转换为大写。

清单 13-1 展示了一个演示这些方法的 `SD`（`String Demo`，字符串演示）应用程序的源代码。

```
public class SD
{
public static void main(String[] args)
{
String s = "The quick brown fox jumped over the lazy dog.";
for (int i = 0; i < s.length(); i++)
System.out.print(s.charAt(i));
System.out.println();
System.out.println("Ends with 'dog.': " + s.endsWith("dog."));
System.out.println("Ends with 'dog': " + s.endsWith("dog"));
System.out.println("Starts with 'The': " + s.startsWith("The"));
System.out.println("Starts with 'They': " + s.startsWith("They"));
System.out.println("Index of 'he': " + s.indexOf("he"));
System.out.println("Index of 'fix': " + s.indexOf("fix"));
System.out.println("Last index of 'he': " + s.lastIndexOf("he"));
System.out.println("Last index of 'fix': " + s.lastIndexOf("fix"));
System.out.println("Replace spaces with dashes: " + s.replace(' ', '-'));
String rep = "a";
System.out.println("Replace all occurrences of 'the' (regardless of " +
"case) with " +
"'" + rep + "'" +
" (first '" + rep + "'" +
" is capitalized): " +
s.replaceAll("(?i)the", rep)
.replaceFirst("^[a-z]", rep.toUpperCase()));
System.out.println("Lowercase: " + s.toLowerCase());
System.out.println("Uppercase: " + s.toUpperCase());
}
}
清单 13-1
SD.java
```

按如下方式编译清单 13-1：

```
javac SD.java
```

按如下方式运行生成的 `SD.class` 应用程序类文件：

```
java SD
```

你应该会看到以下输出：

```
The quick brown fox jumped over the lazy dog.
Ends with 'dog.': true
Ends with 'dog': false
Starts with 'The': true
Starts with 'They': false
Index of 'he': 1
Index of 'fix': -1
Last index of 'he': 33
Last index of 'fix': -1
Replace spaces with dashes: The-quick-brown-fox-jumped-over-the-lazy-dog.
Replace all occurrences of 'the' (regardless of case) with 'a' (first 'a' is capitalized): A quick brown fox jumped over a lazy dog.
Lowercase: the quick brown fox jumped over the lazy dog.
Uppercase: THE QUICK BROWN FOX JUMPED OVER THE LAZY DOG.
```

表达式 `s.replaceAll("(?i)the", rep).replaceFirst("^[a-z]", rep.toUpperCase())` 值得解释一下。为方便起见，我们将该表达式简化为 *a*`.`*b*`.`*c*，其中 *a* 代表 `s`，*b* 代表 `replaceAll("(?i)the", rep)`，*c* 代表 `replaceFirst("^[a-z]", rep.toUpperCase()`。

成员选择运算符（`.`）具有从左到右的结合性（关于结合性的介绍，请参见第 3 章），这意味着该表达式的求值顺序为 `(`*a*`.`*b*`).`*c*。因此，编译器生成的字节码会先求值 `s.replaceAll("(?i)the", rep)`，即在存储在 `s` 中的 `String` 对象引用上调用 `replaceAll()` 方法。

传递给 `replaceAll()` 的第一个参数是 `"(?i)the"`。此字符串中的 `(?i)` 部分启用了不区分大小写的匹配。在 `(?i)` 非捕获组（参见 [`docs.oracle.com/javase/tutorial/essential/regex/groups.xhtml`](https://docs.oracle.com/javase/tutorial/essential/regex/groups.xhtml)）之后是字面文本序列 `the`。该序列会被不区分大小写地搜索（例如，`the`、`The`、`tHe` 等都会匹配）。`replaceAll()` 方法返回一个新的 `String` 对象，其中每个不区分大小写的 `the` 都被替换为 `rep` 所表示的字符串。

接下来，字节码会在返回的 `String` 对象上求值 `replaceFirst("^[a-z]", rep.toUpperCase())`。传递给 `replaceFirst()` 的 `"^[a-z]"` 参数标识了字符串开头的小写字母，而 `rep.toUpperCase()` 参数则将 `rep` 中的替换字符转换为大写。`replaceFirst()` 方法使用这些参数将“`a quick brown fox`…”中的第一个字母替换为 `A`。

`replaceFirst()` 返回的字符串被传递给 `System.out.println()`，后者将该字符串输出到控制台。



### 不可变性与字符串驻留

前面列出的三种方法（`replace()`、`toLowerCase()` 和 `toUpperCase()`）的返回类型都是 `String`。它们看起来像是修改了当前的 `String` 对象并返回其引用，但事实并非如此。实际上，它们会创建一个新的 `String` 对象，从当前 `String` 对象中提取字符数组，修改该数组的内容，然后以修改后的字符数组作为参数调用新 `String` 对象的 `String(char[] value)` 构造函数，最后返回新 `String` 对象的引用。

为什么 `replace()`、`toLowerCase()` 和 `toUpperCase()` 要这样运作？`String` 对象是*不可变的*——它们不能被改变。当你调用一个看似会修改被调用方法所在 `String` 对象（也称为此 `String` 对象）中的字符数组的方法时，会创建一个新的 `String` 对象，并用修改后的字符数组填充它，然后返回其引用。

为什么 `String` 对象是不可变的？这有几个好处，例如缓存。

包括通过构造函数创建的 `String` 对象在内的 Java 对象，都存储在内存中一个称为*堆*的区域。基于字符串字面量的 `String` 对象则存储在一个特殊的堆内存区域中，称为*字符串池*。这种被称为*字符串驻留*的缓存过程，允许 Java 虚拟机（JVM）通过在池中存储每个基于字面量的字符串对象的一个副本来优化分配的内存量。

注意

一些资料指出，只有字符串字面量存储在字符串池中。我可能错了，实际上存储的是包含这些字面量所标识的字符序列的 `String` 对象。无论如何，要点是一样的。字符串存储在字符串池中。

由于字符串字面量和 `String` 对象之间的紧密关系，你可以直接在字符串字面量上调用 `String` 的方法。例如，你可以指定 `"abc".length()` 来返回字符串 `"abc"` 的长度，或者 `"abc".charAt(1)` 来从 `"abc"` 中返回字符 `b`。

考虑以下示例：

```
static string FILENAME = "report.txt";
File file;
if ((file = open("report.txt", "r")) != null)
processReport(file);
else
error("unable to open file " + FILENAME);
```

这个示例首先引入了一个名为 `FILENAME` 的常量，它存储了一个文件名，恰好是 `report.txt`。然后它展示了一个名为 `file` 的局部变量（类型为 `File`）。接着，示例尝试通过调用 `open()` 方法来打开文件，参数是文件名和 `r`（表示文件是用于读取，而不是写入或读写）。

如果 `open()` 成功，它会返回一个 `File` 对象的引用，该引用被赋值给 `file`；否则，它返回 `null` 表示失败。

假设成功，`open()` 会调用 `processReport()` 方法，并将 `file` 的值作为其唯一参数传递给它。如果 `open()` 打开文件失败，它会调用 `error()` 方法，并附带一条由字符串表达式组成的消息（字符串字面量 `"unable to open file "` 后跟 `+` [字符串连接运算符] 再后跟 `FILENAME` 常量）。

这个示例中存在一个编码错误。错误在于出现了 `"report.txt"` 字符串字面量的第二个副本。使用 `FILENAME` 常量的初衷是为了将文件名放在一个地方，以便可以轻松地在一个地方更改它。如果没有这个常量，程序员可能会忘记更改所有出现的文件名——这是程序中一个潜在的*缺陷*（错误）。

JVM 确保字符串池中只有一个基于 `"report.txt"` 的 `String` 对象。因此，可以节省大量潜在的堆内存。

当你通过 `String` 构造函数创建一个 `String` 对象时，该对象不会被驻留。例如，如果字符串池中包含一个包含 `report.txt` 的 `String` 对象，并且你调用 `String s = new String("report.txt");`，那么堆上会创建一个新的 `String` 对象，其内部字符数组中包含 `report.txt`——JVM 不会将字符串池中 `report.txt String` 对象的引用放入变量 `s` 中。

然而，如果你更倾向于使用存储在字符串池中的 `String` 对象，你可以通过在此 `String` 对象上调用 `String` 的 `String intern()` 方法来显式请求。以下是操作方法：

```
String s = new String("report.txt").intern();
```

现在赋值给 `s` 的引用就是来自字符串池的引用。

### 单词计数

为了给你一个更实际的 `String` 类示例，我创建了一个 `WC`（Word Count，单词计数）应用程序，用于统计从控制台读取的*单词*（大写/小写字母字符序列）。清单 13-2 展示了 `WC` 的源代码。

```
public class WC
{
public static void main(String[] args) throws java.io.IOException
{
int ch, nwords = 0;
boolean isWord = false;
while ((ch = System.in.read()) != -1)
{
if (!isalpha(ch))
isWord = false;
else
if (!isWord)
{
isWord = true;
++nwords;
}
}
System.out.println("Number of words: " + nwords);
}
static boolean isalpha(int ch)
{
return ((ch >= 'A' && ch = 'a' && ch <= 'z'));
}
}
清单 13-2
WC.java
```

`WC` 的 `main()` 方法首先引入了局部变量 `ch`（从控制台读取的下一个字符）、`nwords`（从控制台读取的单词计数）和 `isWord`（一个布尔标志——当检测到新单词开始时为 true；否则为 false）。`nwords` 变量被初始化为 `0`，`isWord` 被初始化为 `false`。

`main()` 方法现在进入一个 `while` 循环，重复调用 `System.in.read()` 从控制台读取一个字符，并将其赋值给局部变量 `ch`。当没有更多内容可读时，此方法返回 `-1`。（我将在第 14 章中正式介绍 `System.in.read()`。）

每次循环迭代都会调用 `isalpha()` 辅助方法来测试 `ch` 中的字符。如果该字符是字母，则返回 true；否则返回 false。

注意

我将 `isAlpha()` 编码为假设是英文文本。在一个旨在包含非英语用户的应用中，我会使用 `java.lang.Character` 类的 `isAlpha()` 方法。不过，我倾向于将 `Character` 的讨论留到另一本书中。

如果返回 false，则 `ch` 中的字符不是字母字符，并且将 false 赋值给 `isWord`，这意味着我们不在一个单词中。否则，检测到一个字母字符，并执行 `else` 后面的语句。

该语句由一个链式 `if` 语句组成，以 `if (!isWord)` 开头。如果 `isWord` 包含 false，我们执行以下块的内容，该块将 `true` 赋值给 `isWord` 并递增 `nwords`——`main()` 检测到了另一个单词。

一旦 `while` 循环结束，`main()` 输出单词计数。

按如下方式编译清单 13-2：

```
javac WC.java
```

按如下方式运行生成的应用程序：

```
java WC <WC.java
```

如果你从未在命令行工作过，`<` 符号可能看起来很奇怪。虽然我将在第 14 章中更正式地讨论控制台操作，但 `<` 是一个重定向符号，用于从 `<` 后面的文件名（此处为 `WC.java`）指定的文件获取输入，而不是从控制台的键盘（默认输入源）获取输入。

假设 `WC.java` 未被修改，你应该会看到以下输出：

```
Number of words: 56
```



## StringBuffer

`java.lang.StringBuffer` 类描述了一个*可变*（可修改）的字符序列。`StringBuffer` 与 `String` 类类似，但与 `String` 对象不同的是，`StringBuffer` 对象的内容在创建后可以被修改。

与 `String` 一样，`StringBuffer` 将字符存储在一个内部数组中。与 `String` 无法调整其数组大小以使其变大不同，`StringBuffer` 具备这种能力。

`StringBuffer` 使用了*容量*的概念，即在必须将内部数组替换为更大的数组之前，可以存储的最大字符数。当要存储在数组中的字符序列长度超过容量时，会分配一个新的内部数组，并用该字符序列填充。

### 创建字符串缓冲区

`StringBuffer` 提供了多个用于创建字符串缓冲区的构造方法。这些构造方法包括以下三个：

*   `StringBuffer()`：构造一个不含任何字符的字符串缓冲区，初始容量为 16 个字符。

*   `StringBuffer(int capacity)`：构造一个不含任何字符的字符串缓冲区，初始容量设置为传递给 `capacity` 的值。

*   `StringBuffer(String s)`：构造一个字符串缓冲区，其内容初始化为传递给 `s` 的引用所指向的字符串。

请谨慎使用 `StringBuffer(int capacity)`。如果你向此构造方法传递一个负整数，它将抛出一个 `java.lang.NegativeArraySizeException` 对象。

注意

你可能想探索用于创建 `StringBuffer` 对象的其他构造方法。请查阅 JDK 21 引用类型库中 `StringBuffer` 类的 Java 文档（[`http://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/StringBuffer.xhtml`](http://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/StringBuffer.xhtml)），以了解这些构造方法以及 `StringBuffer` 的方法。

### 探索 StringBuffer 方法

`StringBuffer` 提供了多种方法，包括 `append()` 和 `insert()` 方法，这些方法被重载以接受任何类型的数据（例如 `int`、`char` 或 `double`）。

每个 `append()` 和 `insert()` 方法首先将其数据项转换为字符串，然后将该字符串的字符追加到或插入到字符串缓冲区中。每个 `append()` 方法总是将这些字符添加到缓冲区的末尾；每个 `insert()` 方法则在指定位置添加字符。

以下还介绍了一些其他方法：

*   `int capacity()`：返回此字符串缓冲区的当前容量。

*   `char charAt(int index)`：返回此字符串缓冲区中指定 `index` 处的 `char` 值。当 `index` 为负数或大于等于 `length()` 的返回值时，会抛出一个 `IndexOutOfBoundsException` 对象。

*   `StringBuffer delete(int start, int end)`：从此字符串缓冲区中移除从 `start` 到 `end` - 1 的字符（如果 `end` 大于或等于字符串缓冲区的长度，则移除到字符串缓冲区的末尾）。当 `start` 和 `end` 包含相同的值时，不进行任何更改。当 `start` 为负数、大于 `length()` 或大于 `end` 时，此方法会抛出 `java.lang.StringIndexOutOfBoundsException`。

*   `int indexOf(String s)`：返回子字符串 `s` 在此字符串缓冲区中第一次出现的索引。如果没有出现，则返回 -1。

*   `int lastIndexOf(String s)`：返回子字符串 `s` 在此字符串缓冲区中最后一次出现的索引。如果没有出现，则返回 -1。

*   `int length()`：返回此字符串缓冲区的长度（存储的字符数）。

*   `StringBuffer reverse()`：将此字符串缓冲区的字符序列替换为该序列的反转。例如，如果此字符串缓冲区包含 ABC，则将其替换为 CBA。

*   `void setCharAt(int index, char ch)`：将指定 `index` 处的字符设置为 `ch`。当 `index` 为负数或大于等于 `length()` 的返回值时，抛出 `IndexOutOfBoundsException`。

*   `void setLength(int newLength)`：设置此字符串缓冲区字符序列的长度。该序列将被更改为一个新的字符序列，其长度由 `newLength` 指定。

*   `String substring(int start, int end)`：返回一个新的 `String` 对象，该对象包含此字符序列中当前字符的一个子序列。子序列从 `start` 开始，一直到 `end` - 1。当 `start` 或 `end` 为负数、`end` 大于 `length()` 或 `start` 大于 `end` 时，此方法会抛出 `IndexOutOfBoundsException`。

*   `void trimToSize()`：尝试减少用于字符序列的存储空间。如果内部数组大于保存当前字符序列所需的大小，则可能会调整其大小以提高空间效率。调用此方法可能会（但不要求）影响后续对 `capacity()` 方法调用的返回值。

清单 13-3 展示了一个演示这些方法的 `SBD`（`StringBuffer` 演示）应用程序的源代码。



```
public class SBD
{
public static void main(String[] args)
{
StringBuffer sb = new StringBuffer();
System.out.println("初始容量: " + sb.capacity());
System.out.println("初始长度: " + sb.length());
System.out.println("追加 'The quick brown fox jumped over " +
"the lazy dog.'");
sb.append("The quick brown fox jumped over the lazy dog.");
System.out.println("容量: " + sb.capacity());
System.out.println("长度: " + sb.length());
System.out.println("字符串缓冲区的当前内容（位于 [ 和 ] 之间）:");
System.out.print("[");
for (int i = 0; i < sb.length(); i++)
System.out.print(sb.charAt(i));
System.out.println("]");
System.out.println("删除从索引 3 到 8 的字符");
sb.delete(3, 9);
printSB(sb);
System.out.println("在位置 4 插入 'quick '");
sb.insert(4, "quick ");
printSB(sb);
System.out.println("'dog' 的索引: " + sb.indexOf("dog"));
System.out.println("'dig' 的索引: " + sb.indexOf("dig"));
System.out.println("'he' 的最后索引: " + sb.lastIndexOf("he"));
System.out.println("'fix' 的最后索引: " + sb.lastIndexOf("fix"));
System.out.println("反转字符串缓冲区");
sb.reverse();
printSB(sb);
System.out.println("反转字符串缓冲区");
sb.reverse();
printSB(sb);
System.out.println("将长度更改为 48");
sb.setLength(48);
printCapLenCon(sb);
System.out.println("提取从索引 5 到 9 的子串");
System.out.println("[" + sb.substring(5, 10) + "]");
System.out.println("修剪字符串缓冲区");
sb.trimToSize();
printCapLenCon(sb);
System.out.println("将长度更改为 15");
sb.setLength(15);
printCapLenCon(sb);
}
static void printCapLenCon(StringBuffer sb)
{
System.out.println("容量: " + sb.capacity());
System.out.println("长度: " + sb.length());
printSB(sb);
}
static void printSB(StringBuffer sb)
{
System.out.println("字符串缓冲区的当前内容:");
System.out.print("[");
System.out.print(sb);
System.out.println("]");
}
}
清单 13-3
SBD.java
```

按如下方式编译清单 13-3：

```
javac SBD.java
```

按如下方式运行生成的 `SBD.class` 应用程序类文件：

```
java SBD
```

您应该会看到以下输出：

```
初始容量: 16
初始长度: 0
追加 'The quick brown fox jumped over the lazy dog.'
容量: 45
长度: 45
字符串缓冲区的当前内容（位于 [ 和 ] 之间）:
[The quick brown fox jumped over the lazy dog.]
删除从索引 3 到 8 的字符
字符串缓冲区的当前内容:
[The brown fox jumped over the lazy dog.]
在位置 4 插入 'quick '
字符串缓冲区的当前内容:
[The quick brown fox jumped over the lazy dog.]
'dog' 的索引: 41
'dig' 的索引: -1
'he' 的最后索引: 33
'fix' 的最后索引: -1
反转字符串缓冲区
字符串缓冲区的当前内容:
[.god yzal eht revo depmuj xof nworb kciuq ehT]
反转字符串缓冲区
字符串缓冲区的当前内容:
[The quick brown fox jumped over the lazy dog.]
将长度更改为 48
容量: 92
长度: 48
字符串缓冲区的当前内容:
[The quick brown fox jumped over the lazy dog.   ]
提取从索引 5 到 9 的子串
[uick ]
修剪字符串缓冲区
容量: 48
长度: 48
字符串缓冲区的当前内容:
[The quick brown fox jumped over the lazy dog.   ]
将长度更改为 15
容量: 48
长度: 15
字符串缓冲区的当前内容:
[The quick brown]
```

### 文本反转

为了给您提供一个更实际的 `StringBuffer` 类示例，我创建了一个 `TR`（文本反转）应用程序，它可以反转作为参数传入的单词序列。清单 13-4 展示了 `TR` 的源代码。

```
public class TR
{
public static void main(String[] args)
{
if (args.length == 0)
{
System.out.println("用法: java TR 单词序列");
return;
}
StringBuffer sb = new StringBuffer();
for (int i = 0 ; i < args.length; i++)
sb.append(args[i] + " ");
System.out.println("文本: " + sb.toString());
System.out.println("反转文本: " + sb.reverse().toString());
}
}
清单 13-4
TR.java
```

`main()` 方法首先验证是否至少指定了一个命令行参数。如果没有，则输出用法信息。

接着，`main()` 创建一个 `StringBuffer` 对象，然后进入一个 `for` 循环，将每个命令行参数追加到字符串缓冲区中。它通过调用 `StringBuffer` 的 `append(String s)` 方法来执行追加操作，并将表达式 `args[i] +` `" "` 传递给 `append()`。

最后，`main()` 输出存储在字符串缓冲区中的文本，并在对字符串缓冲区的内容调用 `StringBuffer` 的 `reverse()` 方法后，输出反转后的字符串。在这两种情况下，`StringBuffer` 继承自 `Object` 的 `toString()` 方法都会将字符串缓冲区的内容转换为字符串。

按如下方式编译清单 13-4：

```
javac TR.java
```

按如下方式运行生成的应用程序：

```
java TR The quick brown fox
```

您应该会看到以下输出：

```
文本: The quick brown fox
反转文本:  xof nworb kciuq ehT
```

## 下一章内容

在本书中，我曾在 `System.out.println()` 和 `System.in.read()` 等上下文中提到过 `java.lang.System` 类。第 14 章将带您深入了解 `System`，您将学习到 `in`、`out` 以及更多内容。

