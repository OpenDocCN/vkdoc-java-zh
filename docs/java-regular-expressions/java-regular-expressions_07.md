# 附录 B：Pattern 和 Matcher 方法

本附录提供了 Java 中 Pattern 和 Matcher 类方法的摘要。旨在作为使用各种正则表达式工具时的快速参考。如需更详细的描述，请参阅正文中的相应章节。

## Pattern 类字段

### UNIX_LINES

UNIX_LINES 标志用于构造 `Pattern.compile(String regex, int flags)` 方法的第二个参数。在解析源自 UNIX 机器的数据时使用此标志。

在许多 UNIX 变体中，不可见字符 *\n* 用于表示行结束。这与其他操作系统不同，包括 Windows 的各种版本，它们可能使用 *\r\n*、*\n*、*\r*、*\u2028* 或 *\u0085* 作为行终止符。

如果你曾经将源自 UNIX 机器的文件传输到 Windows 平台并打开它，你可能已经注意到，根据你用来查看文本的编辑器，行有时不会像你期望的那样终止。这是因为两个系统可能使用不同的语法来表示行尾。

UNIX_LINES 标志只是告诉正则表达式引擎它正在处理 UNIX 风格的行，这会影响正则表达式元字符 *^* 和 *$* 的匹配行为。

|  | 注意 | 使用 UNIX_LINES 标志或等效的 ***(?d)*** 正则表达式模式不会降低性能。默认情况下，此标志未设置。 |

### CASE_INSENSITIVE

CASE_INSENSITIVE 字段用于构造 `Pattern.compile(String regex, int flags)` 方法的第二个参数。当你需要匹配美式 ASCII 字符（不区分大小写）时，它非常有用。

|  | 注意 | 使用此标志或等效的 ***(?i)*** 正则表达式可能会导致性能略有下降。默认情况下，此标志未设置。 |

### COMMENTS

COMMENTS 字段被定义，因为它用于构造 `Pattern.compile(String regex, int flags)` 方法的第二个参数。它告诉正则表达式引擎正则表达式模式中嵌入了注释。具体来说，它告诉正则表达式引擎忽略模式中的任何注释，从 *\#* 字符前的空格开始，一直到行尾的所有内容。

因此，正则表达式模式 ***A*** ***#matches uppercase US-ASCII char code 65*** 将使用 *A* 作为正则表达式，但 *\#* 字符前的空格以及之后直到行尾的所有内容都将被忽略。

|  | 注意 | 使用此标志或等效的 ***(?x)*** 正则表达式不会降低性能。 |

### MULTILINE

MULTILINE 字段用于构造 `Pattern.compile(String regex, int flags)` 方法的第二个参数。它告诉正则表达式引擎正则表达式的输入不是单行代码；相反，它包含多行，每行都有自己的终止字符。

这意味着行首字符 ***^*** 和行尾字符 ***$*** 可能会匹配输入字符串中的多行。

例如，假设你的输入字符串是 *This is sentence.\n So is this.* 如果你使用 MULTILINE 标志编译正则表达式模式：

```
Pattern p = Pattern.compile("^", Pattern.MULTILINE);
```

那么行首字符 ***^*** 将匹配 *This is a sentence* 中的 *T* 之前。它也会匹配 *So is this* 中的 *S* 之前。如果不使用 MULTILINE 标志，则匹配只会找到 *This is a sentence* 中的 *T*。

|  | 注意 | 使用此标志或等效的 ***(?m)*** 正则表达式可能会降低性能。 |

### DOTALL

DOTALL 标志用于构造 `Pattern.compile(String regex, int flags)` 方法的第二个参数。

DOTALL 标志告诉正则表达式引擎允许元字符点号 (.) 匹配任何字符，*包括* 行终止字符。这意味着什么？

假设你的候选字符串是 *Test\n*。如果你对应的正则表达式模式是点号 (.)，那么通常你会得到四个匹配：一个匹配 *T*，一个匹配 *e*，一个匹配 *s*，第四个匹配 *t*。这是因为正则表达式元字符点号 (.) 通常匹配除行终止字符之外的任何字符。

启用 DOTALL 标志

```
Pattern p = Pattern.compile(".", Pattern.DOTALL);
```

将会产生五个匹配。你的模式将匹配 *T*、*e*、*s* 和 *t* 字符。此外，它还会匹配行尾的 *\n* 字符。

|  | 注意 | 使用此标志或等效的 ***(?s)*** 正则表达式不会降低性能。 |

### UNICODE_CASE

UNICODE_CASE 标志与 CASE_INSENSITIVE 标志结合使用，可以为国际字符集生成不区分大小写的匹配。

|  | 注意 | 使用此标志或等效的 ***(?u)*** 正则表达式可能会降低性能。 |

### CANON_EQ

如你所知，字符实际上是以数字形式存储的。例如，在美式 ASCII 字符集中，字符 A 由数字 65 表示。根据你使用的字符集，同一个字符可以由不同的数字组合表示。例如，à 可以由 +00E0 和 U+0061U+0300 两者表示。CANON_EQ 匹配将匹配任何一种表示形式。

|  | 注意 | 使用此标志可能会降低性能。 |

## Pattern 类方法



### public static Pattern compile(String regex) throws PatternSyntaxException

你会注意到，`Pattern` 类没有公共构造方法。这意味着你不能编写如下代码：

```
Pattern p = new Pattern("my regex");//错误！
```

要获取 `Pattern` 对象的引用，你必须使用静态方法 `pattern(String regex)`。因此，你的第一行正则表达式代码可能如下所示：

```
Pattern p = Pattern.compile("my regex");//正确！
```

该方法的参数是一个表示正则表达式的字符串。当向期望正则表达式的方法传递字符串时，务必通过在每个 *\* 字符后再附加一个 *\* 字符来对正则表达式中可能存在的 *\* 字符进行转义。这是因为 `String` 对象内部使用 *\* 字符来分隔字符序列中的元字符，无论这些字符序列是否是正则表达式。早在正则表达式成为 Java 的一部分之前，情况就已经如此。因此，正则表达式 ***\d*** 变成了 ***\\d***。要匹配单个数字，你的正则表达式代码变为：

```
Pattern p = Pattern.compile("\\d");
```

这里的要点是，正则表达式 ***\d*** 变成了字符串 ***\\d***。

字符串参数的转义有时可能很棘手，因此充分理解这一点很重要。总的来说，这意味着你需要将正则表达式中可能已经存在的 *\* 字符数量加倍。这并不意味着你只需简单地附加一个 *\* 字符。

如果正则表达式本身格式错误，`compile` 方法将抛出 `java.util.regex.PatternSyntaxException`。例如，如果你传入一个包含 ***[4*** 的字符串，`compile` 方法会在运行时抛出 `PatternSyntaxException`，因为正则表达式 ***[4*** 的语法是非法的。

`compile(String regex)` 方法返回一个 `Pattern` 对象。

### public static compile pattern(String regex, int flags) throws PatternSyntaxException

`compile(String regex, int flags)` 方法是 `compile` 方法的一种更强大的形式。该方法的第一个参数 `regex` 是一个表示正则表达式的字符串，详情请参见之前的 `pattern.compile(String regex)` 方法条目。关于字符串参数必须如何格式化的详细信息，请参阅之前的 `compile(String regex)` 方法条目。

该 `compile` 方法的灵活性通过使用第二个参数 `int flags` 得以充分实现。例如，如果你希望无论候选字符串的大小写如何都能匹配成功，那么你的模式可能如下所示：

```
Pattern p = Pattern.compile(regex,Pattern.CASE_INSENSITIVE);
```

你可以通过使用 ***|*** 运算符来组合标志。例如，要实现包含注释的、不区分大小写的 Unicode 匹配，你可以使用以下代码：

```
Pattern p =
Pattern.compile("t # a compound flag example",Pattern.CASE_INSENSITIVE |
        Pattern.UNICODE_CASE| Pattern.COMMENT);
```

`compile(String regex, int flags)` 方法返回一个 `Pattern` 对象。

### public String pattern()

此方法返回编译此模式所用的正则表达式。这是一个简单的字符串，代表你传入的正则表达式。

此方法可能在两个方面产生误导。首先，返回的字符串不反映编译模式时设置的任何标志。其次，你传入的正则表达式字符串并不总是你取回的模式字符串。具体来说，原始的字符串转义不会被显示。因此，如果你的原始代码是

```
Pattern p = Pattern.compile("\\d");
```

那么你应该期望输出是 ***\d***，带有一个单独的 ***\*** 字符。

### public Matcher matcher(CharSequence input)

请记住，你是通过编译你要查找内容的描述来创建 `Pattern` 对象的。一个 `Pattern` 列出了你要查找内容的特征。纯粹从概念上讲，你的模式可能如下所示：

```
Pattern p = Pattern.compile("She must have red hair and be smarter than I am");
```

相应地，你需要将该描述与候选对象进行比较。也就是说，你需要检查一个给定的字符串，看它是否与你提供的描述相匹配。

`Matcher` 对象专门设计用于帮助你进行此类查询。`Pattern.matcher(CharSequence input)` 方法返回一个 `Matcher`，它将帮助你获取关于候选字符串与你传入的描述如何比较的详细信息。

### public int flags()

之前，我讨论了在编译正则表达式模式时可以使用的常量标志。`flags` 方法简单地返回一个表示这些标志的 `int` 值。

要查看你的 `Pattern` 类当前是否使用了某个给定的标志——例如，`Pattern.COMMENTS` 标志——只需提取标志

```
int flgs = myPattern.flags();
```

然后将该标志与 `Pattern.COMMENTS` 标志进行按位与运算：

```
boolean isUsingCommentFlag =(Pattern.COMMENTS == (Pattern.COMMENTS & flgs)) ;
```

类似地，要查看你是否使用了 `CASE_insensitive`，请执行以下操作：

```
boolean isUsingCaseInsensitiveFlag =
(Pattern.CASE_insensitive == (Pattern. CASE_insensitive & flgs));
```

### public static boolean matches(String regex,CharSequence input)

很多时候，你会发现你只需要知道一个字符串是否与给定的正则表达式完全匹配。你不想创建一个 `Pattern` 对象，提取它的 `Matcher` 对象，然后查询那个 `Matcher`。

这个静态实用方法正是为此而设计的。在内部，它会创建你需要的 `Pattern` 和 `Matcher` 对象，将正则表达式与输入字符串进行比较，并返回一个布尔值，指示这两个对象是否完全匹配。用法可能类似于这里展示的 `PatternMatchesTest` 示例：

```
import java.util.regex.*;
public class PatternMatchesTest{
  public static void main(String args[]){

      String regex = "ad*";
      String input = "add";

      boolean isMatch = Pattern.matches(regex,input);
      System.out.println(isMatch);\\返回 true
  }
}
```

如果你要进行大量的比较，那么显式创建一个 `Pattern` 对象并手动进行匹配会更高效。但是，如果你不打算进行大量比较，那么 `matches` 是一个方便的实用方法。

`Pattern.matches(String regex, CharSequence input)` 方法也在 `String` 类内部使用。从 J2SE 1.4 开始，`String` 有一个名为 `matches` 的新方法，它在内部委托给这个方法。因此，你可能已经在使用此方法而并未意识到。

当然，如果所考虑的正则表达式模式格式不正确，此方法可能会抛出 `PatternSyntaxException`。



### public String[] split(CharSequence input)

当你需要根据某些条件将字符串拆分为子字符串数组时，此方法特别有用。在概念上，它类似于 `StringTokenizer`。然而，它比 `StringTokenizer` 更强大，资源消耗也更高，因为它允许你的程序使用正则表达式作为拆分条件。

此方法始终至少返回一个元素。如果找不到拆分候选对象 `input`，则会返回一个字符串数组，其中恰好包含一个字符串，即原始输入 `input`。

如果可以找到 `input`，则会返回一个字符串数组。该数组包含每次出现 `input` 之后的所有子字符串。因此，对于模式

```
Pattern p = new Pattern.compile(",");
```

对于 *Hello, Dolly* 的 split 方法将返回一个包含两个元素的字符串数组。数组的第一个元素将包含字符串 *Hello*，第二个元素将包含字符串 *Dolly*。该字符串数组的获取方式如下：

```
String tmp[] = p.split("Hello,Dolly");
```

在这种情况下，返回的值为

```
//tmp =={ "Hello", "Dolly"}
```

在使用此方法时，有一些细微之处需要注意。如果候选字符串是 *Hello,Dolly*，并且在 *Dolly* 的 *y* 后面有一个尾随逗号，那么此方法仍然会返回两个元素：一个由 *Hello* 和 *Dolly* 组成的字符串数组。其隐含行为是尾随空格不会被返回。

如果输入字符串是 *Hello,,,Dolly*，则生成的字符串数组将有四个元素。将 split 方法应用于 Pattern 的返回值是

```
// p.split("Hello,,,Dolly") == {"Hello","","","Dolly"} 
```

String 方法通过在模式前放置一个不可见的 ***^*** 并在其后放置一个 ***$*** 来进一步优化其搜索条件。

### public String[] split(CharSequence input, int limit)

此方法的工作方式与 `Pattern.split(CharSequence input)` 完全相同，但有一个变化。第二个参数 `limit` 允许你控制返回的元素数量：

```
Limit == 0
```

如果你指定第二个参数 `limit` 等于 0，那么此方法的行为与其重载版本完全相同：

```
Limit >0
```

如果你只对特定数量的匹配感兴趣，请使用正数 limit。你应该使用数字 *1* 作为 limit。假设 Pattern **p** 已针对字符串 `,` 进行了编译，如前所述。要拆分字符串 *Hello, Dolly, You, Are, My, Favorite* 并且只想要前两个标记，你可以这样使用：

```
String[] tmp = pattern.split("Hello, Dolly, You, Are, My, Favorite",3);
```

结果字符串的值将是：

```
//tmp[0] = "Hello", tmp[1] = "Dolly";
```

这里有趣的行为是返回了第三个元素，在本例中为

```
//tmp[2] = "You, Are, My, Favorite";
```

使用正数 limit 可能会带来性能提升，因为当正则表达式引擎达到指定的匹配次数时，它可以停止搜索：

```
Limit <0
```

为 limit 使用负数（任何负数）会告诉正则表达式引擎，你希望返回尽可能多的匹配项，*并且* 希望返回任何尾随空格（如果有）。因此，对于正则表达式模式 `,` 和候选字符串 *Hello,Dolly*，命令

```
String tmp[] = p.split("Hello,Dolly", -1); 
```

的结果是

```
//tmp == {"Hello","Dolly"};
```

然而，对于字符串 *Hello, Dolly,<space><space><space>*，在 *Dolly* 后面的逗号后有尾随空格，方法调用

```
String tmp[] = p.split("Hello,Dolly,  ", -1);
```

的结果是

```
//tmp == {"Hello","Dolly","  "};
```

请注意，负数 limit 的实际值并不重要。因此，

```
p.split("Hello,Dolly", -1);
```

完全等同于

```
p.split("Hello,Dolly", -100);
```

## 方法类方法

### public Pattern pattern()

`pattern` 方法返回创建此特定 `Matcher` 对象的 `Pattern`。返回的 `Pattern` 不包含在编译模式时通过使用 `Pattern` 常量（例如 `Pattern.MULTILINE`）显式设置的任何标志。

### public Matcher reset()

`reset()` 方法清除调用它的 `Matcher` 对象中的所有状态信息。实际上，`Matcher` 会恢复到最初获得其引用时的状态。

### public Matcher reset(CharSequence input)

`reset(CharSequnce input)` 方法清除调用它的 `Matcher` 对象的状态，并将候选字符串替换为新的输入 `input`。这与创建一个新的 `Matcher` 对象具有相同的效果，只是没有相关的开销。这种重用是一种有用的优化，也是我经常使用的一种方法。

### public int start()

此方法返回匹配到的候选字符串的第一个字符的索引。如果没有匹配项，或者尚未尝试匹配，则会抛出 `IllegalStateException`。

### public int start(int group)

此方法允许你指定匹配中感兴趣的特定子组，并返回该子组起始的第一个字符的索引。如果没有匹配项，或者尚未尝试匹配，则会抛出 `IllegalStateException`。如果你引用了不存在的组号，则会抛出 `IndexOutOfBoundsException`。

### public int end()

`end` 方法返回 `Matcher` 对象最后一次成功匹配的结束索引加 1。如果没有匹配项，或者尚未尝试匹配，此方法会抛出 `IllegalStateException`。

### public int end(int group)

与 `start(int)` 方法类似，此方法允许你指定匹配中感兴趣的特定子组，不同之处在于它返回匹配字符序列的最后一个索引加 1。如果没有匹配项，或者尚未尝试匹配，此方法会抛出 `IllegalStateException`。如果你引用了不存在的组号，则会抛出 `IndexOutOfBoundsException`。

### public String group()

在与混乱代码的斗争中，`group` 方法可以成为一个强大而便捷的工具。它简单地返回与原始正则表达式模式匹配的候选字符串的子字符串。如果 `find` 方法调用不成功，`group()` 方法会抛出 `IllegalStateException`。类似地，如果从未调用过 `find`，它也会抛出 `IllegalStateException`。

### public String group(int group)

此方法是 `group()` 方法更强大的对应版本。它允许你提取与模式中子组匹配的候选字符串的部分。

如果 `find` 方法调用不成功，`group(int)` 方法会抛出 `IllegalStateException`。类似地，如果从未调用过 `find`，它也会抛出 `IllegalStateException`。如果为不存在的组号调用它，则会抛出 `IndexOutOfBoundsException`。

### public int groupCount()

此方法简单地返回 `Pattern` 定义的组数。关于此方法，有一个非常重要且有点反直觉的细微之处需要注意：它基于原始 `Pattern` 返回可能的组数，甚至不考虑候选字符串。因此，它实际上不是关于 `Matcher` 对象的信息；而是关于帮助生成它的 `Pattern` 的信息。这可能很棘手，因为此方法存在于 `Matcher` 对象上这一事实可能被解释为它正在提供关于 `Matcher` 状态的反馈。但事实并非如此。它告诉你对于给定的 `Pattern`，理论上可能有多少个匹配项。

### public boolean matches()

此方法旨在帮助你根据匹配器的 `Pattern` 匹配候选字符串。当且仅当所考虑的候选字符串与模式完全匹配时，它才返回 `true`。



### public boolean find()

`find()` 方法会解析候选字符串中足够的部分以查找匹配项。如果找到这样的子字符串，则返回 `true`，并且 `find` 会停止解析候选字符串。如果候选字符串中没有部分与模式匹配，则 `find` 返回 `false`。

### public boolean find(int start)

`find(int start)` 的工作方式与其重载版本完全相同，区别在于搜索的起始位置。整数参数 `start` 只是告诉 `Matcher` 从哪个字符开始搜索。

因此，对于候选字符串 *I* *love Java. Java is my favorite language. Java* *Java Java*. 和模式 **Java**，如果你只想从字符索引 11 开始搜索，可以使用命令 `find(11)`。

### public Matcher appendReplacement(StringBuffer sb, String replacement)

`appendReplace` 方法允许你基于正则表达式修改 `StringBuffer` 的内容。它甚至允许你通过使用 ***$n*** 符号来引用反向引用，其中 ***n*** 指的是某个捕获的子组。例如，对于 `StringBuffer` *Waldo Smith*、模式 ***(\w+) (\w+)*** 和替换字符串 ***$2, $1***，`StringBuffer` 的内容将被修改为 *Smith, Waldo*。

如果尚未调用 `find()`，或者调用 `find()` 会返回 `false`，则 `appendReplacement` 方法会抛出 `IllegalStateException`。如果由 ***$1, $2*** 等引用的捕获组在 `Matcher` 当前正在检查的模式部分中不存在，则会抛出 `IndexOutOfBoundsException`。

### public StringBuffer appendTail(StringBuffer sb)

`appendTail` 方法是 `appendReplacement` 方法的补充。它只是将从追加位置（我在 `appendReplacement` 部分解释过）到原始候选字符串末尾的所有剩余子序列追加到 `StringBuffer` 中。

### public String replaceAll(String replacement)

`replaceAll` 方法返回一个字符串，该字符串将所有出现的描述替换为替换字符串。使用此方法会更改 `Matcher` 对象的状态。具体来说，会调用 `reset()` 方法。因此，请记住，所有 `start`、`end`、`group` 和 `find` 调用都必须重新执行。

与 `appendReplacement` 方法类似，`replaceAll` 方法可以通过使用 ***$*** 符号包含对子字符串的引用。有关详细信息，请参阅前面介绍的 `appendReplacement` 文档。

### public String replaceFirst(String replacement)

`replaceFirst` 方法是 `replaceAll` 方法的一个更有限的版本。此方法返回一个字符串，该字符串将第一个出现的描述替换为替换字符串。使用此方法会更改 `Matcher` 对象的状态。具体来说，会调用 `reset()` 方法。因此，请记住，在调用 `replaceFirst` 之后，所有 `start`、`end`、`group` 和 `find` 调用都必须重新执行。

与 `appendReplacement` 方法类似，`replaceFirst` 方法可以通过使用 ***$*** 符号包含对子字符串的引用。有关详细信息，请参阅前面介绍的 `appendReplacement` 文档。



