# 9. 正则表达式

电子补充材料 本章的在线版本 (doi:[10.​1007/​978-1-4842-1565-4_​9](http://dx.doi.org/10.1007/978-1-4842-1565-4_9)) 包含补充材料，仅供授权用户使用。

文本处理应用程序通常需要将文本与模式（即简洁地描述被视为匹配的字符串集合的字符串）进行匹配。例如，一个应用程序可能需要定位文本文件中某个特定单词模式的所有出现位置，以便用另一个单词替换这些出现。NIO 包含正则表达式，以帮助文本处理应用程序高性能地执行模式匹配。本章介绍正则表达式。

## Pattern、PatternSyntaxException 和 Matcher

正则表达式（也称为 regex 或 regexp）是一种基于字符串的模式，表示与该模式匹配的字符串集合。该模式由字面字符和元字符组成，元字符是具有特殊含义而非字面含义的字符。

正则表达式 API 提供了 `java.util.regex.Pattern` 类，通过编译后的正则表达式来表示模式。出于性能原因，正则表达式会被编译；通过编译后的正则表达式进行模式匹配，比未编译的正则表达式要快得多。表 9-1 描述了 `Pattern` 的方法。

表 9-1. `Pattern` 方法

| 方法 | 描述 |
| --- | --- |
| `static Pattern compile(String regex)` | 编译 `regex` 并返回其 `Pattern` 对象。当 `regex` 的语法无效时，此方法抛出 `java.util.regex.PatternSyntaxException`。 |
| `static Pattern compile(String regex,int flags)` | 根据给定的 `flags`（一个位集，由 `Pattern` 的 `CANON_EQ`、`CASE_INSENSITIVE`、`COMMENTS`、`DOTALL`、`LITERAL`、`MULTILINE`、`UNICODE_CASE` 和 `UNIX_LINES` 常量的某种组合构成）编译 `regex`，并返回其 `Pattern` 对象。当 `regex` 的语法无效时，此方法抛出 `PatternSyntaxException`；当 `flags` 中设置了与已定义匹配标志不对应的位值时，抛出 `java.lang.IllegalArgumentException`。 |
| `int flags()` | 返回此 `Pattern` 对象的匹配标志。对于通过 `compile(String)` 创建的 `Pattern` 实例，此方法返回 `0`；对于通过 `compile(String, int)` 创建的 `Pattern` 实例，返回标志的位集。 |
| `Matcher matcher(CharSequence input)` | 返回一个 `java.util.regex.Matcher`，它将把 `input` 与此 `Pattern` 的已编译正则表达式进行匹配。 |
| `static boolean matches(String regex, CharSequence input)` | 编译 `regex` 并尝试将 `input` 与编译后的正则表达式进行匹配。匹配时返回 `true`；否则返回 `false`。此便捷方法等同于 `Pattern.compile(regex).matcher(input).matches()`，并在 `regex` 语法无效时抛出 `PatternSyntaxException`。 |
| `String pattern()` | 返回此 `Pattern` 的未编译正则表达式。 |
| `static String quote(String s)` | 使用 `"\Q"` 和 `"\E"` 引用 `s`，以便所有其他元字符失去其特殊含义。当返回的 `java.lang.String` 对象随后被编译成 `Pattern` 实例时，它只能进行字面匹配。 |
| `String[] split(CharSequence input)` | 根据此 `Pattern` 的已编译正则表达式的匹配结果拆分 `input`，并返回包含匹配结果的数组。 |
| `String[] split(CharSequence input, int limit)` | 根据此 `Pattern` 的已编译正则表达式的匹配结果拆分 `input`；`limit` 控制已编译正则表达式应用的次数，从而影响结果数组的长度。 |
| `String toString()` | 返回此 `Pattern` 的未编译正则表达式。 |

表 9-1 揭示了 `java.lang.CharSequence` 接口，该接口描述了一个可读且不可变的 `char` 值序列——底层实现可能是可变的。任何实现此接口的类（例如 `String`、`java.lang.StringBuffer` 和 `java.lang.StringBuilder`）的实例都可以传递给接受 `CharSequence` 参数的 `Pattern` 方法（例如 `split(CharSequence)`）。

表 9-1 还揭示了 `Pattern` 的每个 `compile()` 方法及其 `matches()` 方法（该方法调用 `compile(String)` 方法）在编译模式参数时遇到语法错误时会抛出 `PatternSyntaxException`。表 9-2 描述了 `PatternSyntaxException` 的方法。

表 9-2. `PatternSyntaxException` 方法



| 方法 | 描述 |
| --- | --- |
| `String getDescription()` | 返回语法错误的描述。 |
| `int getIndex()` | 返回模式中语法错误发生的大致索引，如果索引未知则返回 `-1`。 |
| `String getMessage()` | 返回一个多行字符串，包含语法错误的描述及其索引、错误的模式，以及模式中错误索引的视觉指示。 |
| `String getPattern()` | 返回错误的模式。 |

最后，表 9-1 中的 `Matcher matcher(CharSequence input)` 方法表明，正则表达式 API 还提供了 `Matcher` 类，其匹配器尝试将编译后的正则表达式与 `input` 文本进行匹配。`Matcher` 声明了以下方法来执行匹配操作：

*   `boolean matches()`：尝试将整个区域与模式进行匹配。当匹配成功时，可以通过调用 `Matcher` 的 `start()`、`end()` 和 `group()` 方法获取更多信息。例如，`int start()` 返回上一次匹配的起始索引，`int end()` 返回上一次匹配之后第一个字符的偏移量，`String group()` 返回上一次匹配所匹配到的输入子序列。如果尚未尝试匹配或上一次匹配尝试失败，每个方法都会抛出 `java.lang.IllegalStateException`。
*   `boolean lookingAt()`：尝试从区域开头开始，将输入序列与模式进行匹配。与 `matches()` 一样，此方法始终从区域开头开始。与 `matches()` 不同，`lookingAt()` 不要求匹配整个区域。当匹配成功时，可以通过调用 `Matcher` 的 `start()`、`end()` 和 `group()` 方法获取更多信息。
*   `boolean find()`：尝试查找与模式匹配的下一个输入序列实例。它可以从该匹配器区域的起始位置开始。或者，如果之前对该方法的调用成功，并且匹配器之后未被重置（通过调用 `Matcher` 的 `Matcher reset()` 或 `Matcher reset(CharSequence input)` 方法），它将从上一次匹配未匹配到的第一个字符开始。当匹配成功时，可以通过调用 `Matcher` 的 `start()`、`end()` 和 `group()` 方法获取更多信息。

注意

匹配器在其输入的子集（称为区域）中查找匹配项。默认情况下，区域包含匹配器的所有输入。可以通过调用 `Matcher` 的 `Matcher region(int start, int end)` 方法（设置此匹配器区域的边界）来修改区域，并通过调用 `Matcher` 的 `int regionStart()` 和 `int regionEnd()` 方法来查询区域。

我创建了一个简单的应用程序来演示 `Pattern`、`PatternSyntaxException` 和 `Matcher`。清单 9-1 展示了该应用程序的源代码。

清单 9-1. 使用正则表达式

`import java.util.regex.Matcher;`

`import java.util.regex.Pattern;`

`import java.util.regex.PatternSyntaxException;`

`public class RegExDemo`

`{`

`public static void main(String[] args)`

`{`

`if (args.length != 2)`

`{`

`System.err.println("usage: java RegExDemo regex input");`

`return;`

`}`

`try`

`{`

`System.out.println("regex = " + args[0]);`

`System.out.println("input = " + args[1]);`

`Pattern p = Pattern.compile(args[0]);`

`Matcher m = p.matcher(args[1]);`

`while (m.find())`

`System.out.println("Located [" + m.group() + "] starting at "`

`+ m.start() + " and ending at " +`

`(m.end() - 1));`

`}`

`catch (PatternSyntaxException pse)`

`{`

`System.err.println("Bad regex: " + pse.getMessage());`

`System.err.println("Description: " + pse.getDescription());`

`System.err.println("Index: " + pse.getIndex());`

`System.err.println("Incorrect pattern: " + pse.getPattern());`

`}`

`}`

`}`

按如下方式编译清单 9-1：

`javac RegExDemo.java`

按如下方式运行生成的应用程序：

`java RegExDemo ox ox`

您将看到以下输出：

`regex = ox`

`input = ox`

`Located [ox] starting at 0 and ending at 1`

`find()` 通过从左到右比较正则表达式字符与输入字符来搜索匹配项，并返回 `true`，因为 `o` 等于 `o`，`x` 等于 `x`。

继续执行以下命令行：

`java RegExDemo box ox`

这次，您将看到以下输出：

`regex = box`

`input = ox`

`find()` 首先将正则表达式字符 `b` 与输入字符 `o` 进行比较。由于这些字符不相等，并且输入中没有足够的字符来继续搜索，因此 `find()` 不会输出“Located”消息来指示匹配。但是，如果您执行 `java RegExDemo ox box`，您会发现一个匹配：

`regex = ox`

`input = box`

`Located [ox] starting at 1 and ending at 2`

`ox` 正则表达式由字面字符组成。更复杂的正则表达式将字面字符与元字符（例如句点 [`.`]）和其他正则表达式结构相结合。

提示

要将元字符指定为字面字符，请在元字符前加上反斜杠字符（如 `\.`），或者将元字符放在 `\Q` 和 `\E` 之间（如 `\Q.\E`）。无论哪种情况，当转义后的元字符出现在字符串字面量中时，请确保将反斜杠字符加倍；例如，`"\\."` 或 `"\\Q.\\E"`。

句点元字符匹配除行终止符之外的所有字符。例如，`java RegExDemo .ox box` 和 `java RegExDemo .ox fox` 都报告匹配，因为句点匹配了 `box` 中的 `b` 和 `fox` 中的 `f`。

注意

`Pattern` 识别以下行终止符：回车符 (`\r`)、换行符 (`\n`)、回车符后紧跟换行符 (`\r\n`)、下一行符 (`\u0085`)、行分隔符 (`\u2028`) 和段落分隔符 (`\u2029`)。通过在调用 `Pattern.compile(String, int)` 时指定 `Pattern.DOTALL` 标志，可以使句点元字符也匹配这些行终止符。



## 字符类

字符类是出现在 `[` 和 `]` 之间的一组字符。共有六种字符类：

*   **简单字符类**由并排放置的字面字符组成，仅匹配这些字符。例如，`[abc]` 由字符 `a`、`b` 和 `c` 组成。此外，`java RegExDemo t[aiou]ck tack` 会报告匹配成功，因为 `a` 是 `[aiou]` 的成员。当输入为 `tick`、`tock` 或 `tuck` 时，它也会报告匹配成功，因为 `i`、`o` 和 `u` 也是其成员。
*   **否定字符类**由一个脱字符元字符（`^`）后跟并排放置的字面字符组成，匹配除该类中字符之外的所有字符。例如，`[^abc]` 包含除 `a`、`b` 和 `c` 之外的所有字符。此外，`java RegExDemo "[^b]ox" box` 不会报告匹配成功，因为 `b` 不是 `[^b]` 的成员；而 `java RegExDemo "[^b]ox" fox` 会报告匹配成功，因为 `f` 是其成员。（在我的 Windows 7 操作系统上，`[^b]ox` 周围的双引号是必需的，因为 `^` 在命令行中会被特殊处理。）
*   **范围字符类**由连续的多个字面字符组成，表示为一个起始字面字符，后跟连字符元字符（`-`），再跟一个结束字面字符，匹配该范围内的所有字符。例如，`[a-z]` 包含从 `a` 到 `z` 的所有字符。此外，`java RegExDemo [h-l]ouse house` 会报告匹配成功，因为 `h` 是该类的成员；而 `java RegExDemo [h-l]ouse mouse` 不会报告匹配成功，因为 `m` 超出了范围，因此不属于该类。你可以通过并排放置多个范围来组合它们，例如，`[A-Za-z]` 包含所有大写和小写拉丁字母。
*   **并集字符类**由多个嵌套的字符类组成，匹配属于最终并集的所有字符。例如，`[abc[u-z]]` 包含字符 `a`、`b`、`c`、`u`、`v`、`w`、`x`、`y` 和 `z`。此外，`java RegExDemo [[0-9][A-F][a-f]] e` 会报告匹配成功，因为 `e` 是一个十六进制字符。（我也可以通过组合多个范围，将此类表示为 `[0-9A-Fa-f]`。）
*   **交集字符类**由多个用 `&&` 分隔的嵌套字符类组成，匹配这些嵌套字符类共有的所有字符。例如，`[a-c&&[c-f]]` 包含字符 `c`，它是 `[a-c]` 和 `[c-f]` 共有的唯一字符。此外，`java RegExDemo "[aeiouy&&[y]]" y` 会报告匹配成功，因为 `y` 是类 `[aeiouy]` 和 `[y]` 共有的字符。
*   **差集字符类**由多个用 `&&` 分隔的嵌套字符类组成，其中至少有一个嵌套字符类是否定字符类，匹配除否定字符类所指示字符之外的所有字符。例如，`[a-z&&[^x-z]]` 包含从 `a` 到 `w` 的字符。（`^x-z` 周围的方括号是必需的；否则，`^` 会被忽略，结果类将只包含 `x`、`y` 和 `z`。）此外，`java RegExDemo "[a-z&&[^aeiou]]" g` 会报告匹配成功，因为 `g` 是一个辅音字母，只有辅音字母属于此类。（这里忽略了 `y`，它有时被视为辅音，有时被视为元音。）

**预定义字符类**是针对常用字符类的正则表达式构造。表 9-3 列出了 `Pattern` 的预定义字符类。

表 9-3. 预定义字符类

| 预定义字符类 | 描述 |
| --- | --- |
| `\d` | 匹配任意数字字符。`\d` 等价于 `[0-9]`。 |
| `\D` | 匹配任意非数字字符。`\D` 等价于 `[^\d]`。 |
| `\s` | 匹配任意空白字符。`\s` 等价于 `[\t\n\x0B\f\r ]`。 |
| `\S` | 匹配任意非空白字符。`\S` 等价于 `[^\s]`。 |
| `\w` | 匹配任意单词字符。`\w` 等价于 `[a-zA-Z0-9]`。 |
| `\W` | 匹配任意非单词字符。`\W` 等价于 `[^\w]`。 |

例如，以下命令行报告匹配成功，因为 `\w` 匹配了 `abc` 中的单词字符 `a`：

`java RegExDemo \wbc abc`

## 捕获组

捕获组会保存匹配的字符，以便在模式匹配过程中后续引用，它表示为用括号元字符 `(` 和 `)` 包围的字符序列。捕获组内的所有字符都被视为一个单元。例如，`(Java)` 捕获组将 `J`、`a`、`v` 和 `a` 组合成一个单元。它匹配输入中所有出现的 `Java` 模式。每次匹配都会用下一次匹配的 `Java` 字符替换上一次匹配保存的 `Java` 字符。

捕获组可以出现在其他捕获组内部。例如，捕获组 `(A)` 和 `(B(C))` 出现在捕获组 `((A)(B(C)))` 内部，而捕获组 `(C)` 出现在捕获组 `(B(C))` 内部。每个嵌套或非嵌套的捕获组都会获得自己的编号，编号从 1 开始，并且捕获组按从左到右的顺序编号。例如，`((A)(B(C)))` 被分配为 1，`(A)` 被分配为 2，`(B(C))` 被分配为 3，`(C)` 被分配为 4。

捕获组保存其匹配结果，以便通过**反向引用**进行后续引用。反向引用是一个反斜杠字符后跟一个表示捕获组编号的数字字符。反向引用会导致匹配器使用其捕获组编号来回忆该捕获组保存的匹配结果，然后使用该匹配结果的字符尝试进一步匹配。以下示例使用反向引用判断输入是否由两个连续的 `Java` 模式组成：

`java RegExDemo "(Java) \1" "Java Java"`

`RegExDemo` 报告匹配成功，因为匹配器在输入中检测到 `Java`，后跟一个空格，再后跟 `Java`。

## 边界匹配器和零长度匹配

边界匹配器是一种正则表达式构造，用于标识行首、单词边界、文本结尾以及其他常见的边界。参见表 9-4。

表 9-4. 边界匹配器

| 边界匹配器 | 描述 |
| --- | --- |
| `^` | 匹配行首。 |
| `$` | 匹配行尾。 |
| `\b` | 匹配单词边界。 |
| `\B` | 匹配非单词边界。 |
| `\A` | 匹配文本开头。 |
| `\G` | 匹配上一次匹配的结尾。 |
| `\Z` | 匹配文本结尾（如果存在行终止符，则不包括它）。 |
| `\z` | 匹配文本结尾。 |

考虑以下示例：

`java RegExDemo \b\b "I think"`

此示例报告了多次匹配，如下输出所示：

`regex = \b\b`

`input = I think`

`Located [] starting at 0 and ending at -1`

`Located [] starting at 1 and ending at 0`

`Located [] starting at 2 and ending at 1`

`Located [] starting at 7 and ending at 6`

此输出揭示了几个零长度匹配。当发生零长度匹配时，起始索引和结束索引相等，尽管输出显示结束索引比起始索引小 1，因为我在清单 9-1 中指定了 `end() - 1`（这样匹配的结束索引标识的是非零长度匹配的最后一个字符，而不是非零长度匹配最后一个字符之后的那个字符）。

注意

零长度匹配发生在空输入文本中、输入文本的开头、输入文本最后一个字符之后，或者该文本任意两个字符之间。零长度匹配很容易识别，因为它们总是在同一个索引位置开始和结束。



## 量词

我要介绍的最后一种正则表达式结构是量词，它是一个隐式或显式绑定到某个模式的数值。量词分为贪婪型、勉强型和占有型：

*   **贪婪型量词**（`?`、`*` 或 `+`）尝试寻找最长的匹配。指定 `X?` 表示查找零次或一次出现的 `X`，`X*` 表示查找零次或多次出现的 `X`，`X+` 表示查找一次或多次出现的 `X`，`X{n}` 表示查找恰好出现 `n` 次的 `X`，`X{n,}` 表示查找至少出现 `n` 次（可能更多）的 `X`，`X{n,m}` 表示查找至少出现 `n` 次但不超过 `m` 次的 `X`。
*   **勉强型量词**（`??`、`*?` 或 `+?`）尝试寻找最短的匹配。指定 `X??` 表示查找零次或一次出现的 `X`，`X*?` 表示查找零次或多次出现的 `X`，`X+?` 表示查找一次或多次出现的 `X`，`X{n}?` 表示查找恰好出现 `n` 次的 `X`，`X{n,}?` 表示查找至少出现 `n` 次（可能更多）的 `X`，`X{n,m}?` 表示查找至少出现 `n` 次但不超过 `m` 次的 `X`。
*   **占有型量词**（`?+`、`*+` 或 `++`）与贪婪型量词类似，区别在于占有型量词只尝试一次以找到最长匹配，而贪婪型量词可以进行多次尝试。指定 `X?+` 表示查找零次或一次出现的 `X`，`X*+` 表示查找零次或多次出现的 `X`，`X++` 表示查找一次或多次出现的 `X`，`X{n}+` 表示查找恰好出现 `n` 次的 `X`，`X{n,}+` 表示查找至少出现 `n` 次（可能更多）的 `X`，`X{n,m}+` 表示查找至少出现 `n` 次但不超过 `m` 次的 `X`。

关于贪婪型量词的示例，请执行以下命令行：

`java RegExDemo .*end "wend rend end"`

您将看到以下输出：

`regex = .*end`

`input = wend rend end`

`Located [wend rend end] starting at 0 and ending at 12`

贪婪型量词（`.*`）匹配以 `end` 结尾的最长字符序列。它首先消耗所有输入文本，然后被迫回溯，直到发现输入文本以这些字符结尾。

关于勉强型量词的示例，请执行以下命令行：

`java RegExDemo .*?end "wend rend end"`

您将看到以下输出：

`regex = .*?end`

`input = wend rend end`

`Located [wend] starting at 0 and ending at 3`

`Located [ rend] starting at 4 and ending at 8`

`Located [ end] starting at 9 and ending at 12`

勉强型量词（`.*?`）匹配以 `end` 结尾的最短字符序列。它开始时不消耗任何字符，然后慢慢消耗字符直到找到匹配。之后继续匹配，直到耗尽所有输入文本。

关于占有型量词的示例，请执行以下命令行：

`java RegExDemo .*+end "wend rend end"`

您将看到以下输出：

`regex = .*+end`

`input = wend rend end`

占有型量词（`.*+`）没有检测到匹配，因为它消耗了整个输入文本，没有剩余字符来匹配正则表达式末尾的 `end`。与贪婪型量词不同，占有型量词不会回溯。

在使用量词时，您可能会遇到零长度匹配。例如，执行以下命令行：

`java RegExDemo 1? 101101`

您应该会看到以下输出：

`regex = 1?`

`input = 101101`

`Located [1] starting at 0 and ending at 0`

`Located [] starting at 1 and ending at 0`

`Located [1] starting at 2 and ending at 2`

`Located [1] starting at 3 and ending at 3`

`Located [] starting at 4 and ending at 3`

`Located [1] starting at 5 and ending at 5`

`Located [] starting at 6 and ending at 5`

这个贪婪型量词的结果是：在输入文本的位置 0、2、3 和 5 检测到 `1`，而在位置 1、4 和 6 没有检测到任何内容（零长度匹配）。

这次，执行以下命令行：

`java RegExDemo 1?? 101101`

您应该会看到以下输出：

`regex = 1??`

`input = 101101`

`Located [] starting at 0 and ending at -1`

`Located [] starting at 1 and ending at 0`

`Located [] starting at 2 and ending at 1`

`Located [] starting at 3 and ending at 2`

`Located [] starting at 4 and ending at 3`

`Located [] starting at 5 and ending at 4`

`Located [] starting at 6 and ending at 5`

这个输出可能看起来令人惊讶，但请记住，勉强型量词寻找最短匹配，在这种情况下，最短匹配就是完全不匹配。

最后，执行以下命令行：

`java RegExDemo 1+? 101101`

您应该会看到以下输出：

`regex = 1+?`

`input = 101101`

`Located [1] starting at 0 and ending at 0`

`Located [1] starting at 2 and ending at 2`

`Located [1] starting at 3 and ending at 3`

`Located [1] starting at 5 and ending at 5`

这个占有型量词只匹配输入文本中检测到 `1` 的位置。它不执行零长度匹配。

注意

请查阅关于 `Pattern` 类的 Java 文档，以了解其他正则表达式结构。

## 实用正则表达式

之前的大多数正则表达式示例都不太实用，除了帮助您掌握如何使用各种正则表达式结构。相比之下，以下示例展示了一个匹配电话号码的正则表达式，其格式为 `(ddd) ddd-dddd` 或 `ddd-dddd`。`(ddd)` 和 `ddd` 之间有一个空格；连字符两侧没有空格。

`java RegExDemo "(\(\d{3}\))?\s*\d{3}-\d{4}" "(800) 555-1212"`

`regex = (\(\d{3}\))?\s*\d{3}-\d{4}`

`input = (800) 555-1212`

`Located [(800) 555-1212] starting at 0 and ending at 13`

`java RegExDemo "(\(\d{3}\))?\s*\d{3}-\d{4}" 555-1212`

`regex = (\(\d{3}\))?\s*\d{3}-\d{4}`

`input = 555-1212`

`Located [555-1212] starting at 0 and ending at 7`

注意

要了解更多关于正则表达式的信息，请查看 Java 教程中的“正则表达式课程”，网址为 [`http://download.oracle.com/javase/tutorial/essential/regex/index.html`](http://download.oracle.com/javase/tutorial/essential/regex/index.html)。

练习

以下练习旨在测试您对第 9 章内容的理解：

定义正则表达式。  `Pattern` 类的作用是什么？  当 `Pattern` 的 `compile()` 方法在其正则表达式参数中发现非法语法时，它会做什么？  `Matcher` 类的作用是什么？  `Matcher` 的 `matches()` 和 `lookingAt()` 方法有什么区别？  定义字符类。  指出各种类型的字符类。  判断对错：交集字符类由多个用 `&&` 分隔的嵌套字符类组成，其中至少有一个嵌套字符类是取反字符类，并且匹配除取反字符类所指示字符之外的所有字符。  定义捕获组。  什么是零长度匹配？  定义量词。  贪婪型量词和勉强型量词有什么区别？  占有型量词和贪婪型量词有何不同？  创建一个 `ReplaceText` 应用程序，它接受输入文本、指定要替换文本的模式以及替换文本作为命令行参数，并使用 `Matcher` 的 `String replaceAll(String replacement)` 方法将模式的所有匹配项替换为替换文本（传递给 `replacement`）。例如，`java ReplaceText "too many embedded spaces" "\s+" " "` 应输出 `too many embedded spaces`，且连续单词之间只有一个空格字符。



## 摘要

文本处理应用程序通常需要将文本与模式进行匹配。NIO 包含正则表达式，可帮助这些应用程序以高性能执行模式匹配。Java 通过提供 `Pattern`、`PatternSyntaxException` 和 `Matcher` 类来支持正则表达式。

在本章中，你学习了 `Pattern`、`PatternSyntaxException` 和 `Matcher`。然后，你了解了字符类、捕获组、边界匹配器和零长度匹配以及量词。最后，你观察了一个正则表达式的实际用例：电话号码匹配。

第 10 章介绍了 NIO 对字符集的支持。

