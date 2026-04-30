# 第 1 章：正则表达式

## 概述

> *“你看，如果你愿意花功夫去弄清其中的道理，一切就都说得通了。”*
> 
> — 皮尔斯·安东尼

*正则表达式*，简称 *regex*，用于描述文本。它是一种机制，通过它你可以告诉 Java 虚拟机（JVM）如何为你查找并可能操作文本。在本章中，我将考察并对比描述文本的传统方法与正则表达式方法。

例如，假设你需要验证电子邮件地址。对此的口头指导可能类似于“确保电子邮件地址包含一个 at (@) 符号”。你可能只需一行 Java 代码就能处理这个任务：

```
if (email.indexOf("@") > 0) {
   return true;
}
```

到目前为止，一切顺利。但假设有额外的需求不断涌现，就像它们总会发生的那样。现在你还需要确保所有电子邮件地址都以 .org 扩展名结尾。于是你修改代码如下：

```
if ((email.indexOf("@") > 0) && (email.endsWith(".org"))){

   return true;
}
```

但需求仍在不断增加。现在你需要所有电子邮件地址都采用 firstname_lastname 的形式，因此你使用 StringTokenizer 来标记化电子邮件地址，提取 @ 之前的部分，查找下划线 (_) 字符，标记化其周围的字符串，等等。很快，对于一个本应相当直接的操作，你就有了一些复杂的代码。

使用正则表达式可以极大地简化和压缩这个过程。使用正则表达式，你可以编写如下代码：

```
String regex = "[A-Za-z]+_[A-Za-z]+@[A-Za-z]+\\.org";
if (email.matches(regex)) return true; 
```

用通俗的话说，这意味着“查找一个或多个字母，后跟一个 _，再后跟一个或多个字母，后跟一个 @，再后跟一个或多个字母，后跟 .org”。注意，“org”中的 *o* 前面有一个句点。

如果现在语法对你来说不完全清楚，不必担心——让语法清晰明了正是本书的目的。本章探讨 Java 正则表达式的基本概念，重点在于实际构建和使用正则表达式语法。这是对正则表达式的完整介绍，同时也作为下一章的序言。第 2 章则是对 J2SE 正则表达式对象模型的完整且详尽的文档。

## 正则表达式的构建块

Java 2 标准版（J2SE）中的正则表达式由两个基本部分组成，这两个部分由两个新的 Java 对象体现。第一部分是 Pattern，第二部分是 Matcher。理解这两个对象对于你掌握正则表达式至关重要。幸运的是，它们是容易理解的概念。

我将在接下来的章节中详细定义这些概念，但从一般层面来说，*pattern* 描述你要查找的内容，而 *matcher* 检查可能与模式或描述匹配的候选对象。例如，***\****s****+*** 是描述一个或多个空格的模式。相应地，J2SE 现在提供了 Pattern 和 Matcher 对象。

|  | 注意  | 当我提到候选对象或候选字符串时，我指的是正则表达式将要操作的字符串。因此，对于上一节中描述的模式，候选字符串可能是 <coach@influxs.com>、<john_john_smith@w3c.org> 或 <hana@saez.com>。 |

### 定义模式

模式是正则表达式中实际使用的描述。它们的力量源于其描述文本的能力，而非指定文本。它们是正则表达式术语的重要组成部分，你需要很好地理解它们才能使用正则表达式。幸运的是，如果你不被吓倒，它们很容易掌握，并且它们那有些令人不快的语法很快就会变得直观。

模式允许你描述要查找的项目的特征，而无需明确指定该项目。当你只知道目标的特征，但无法具体命名它们时，这尤其有用。

想象一下解析一个文档。你可能想找到每个大写字母开头的单词；或者每个以字母 *Z* 开头的单词；或者每个以大写 *Z* 开头、后跟一个元音字母（除非该元音是 *a*）的单词。你无法事先确切知道给定文档中这些单词是什么，但你可以描述它们。那个描述就是你的模式。

我把正则表达式想象成一个警察局。模式是负责获取嫌疑人描述的警官，而匹配器是负责围捕和审讯这些嫌疑人的警官。

### 定义匹配器

如果你熟悉标准查询语言（SQL），将正则表达式视为一种用于检查自由格式文本的 SQL 可能会有所帮助。模式在概念上类似于执行的 SQL 查询。匹配器对应于该查询返回的 ResultSet。

Matcher 检查应用 Pattern 的结果。如果你的模式说“在上一句中查找每个以 *a* 开头的单词”，那么你将在应用模式后检查 Matcher。你的代码可能如清单 1-1 所示。清单 1-1 的输出显示在紧随其后的输出 1-1 中。

清单 1-1：查找字母 A 的每次出现

| **![开始示例](img/_1.gif)** |

```
import java.util.regex.*;

public class FindA{
  public static void main(String args[])
  throws Exception{

    String candidate =
     "A Matcher examines the results of applying a pattern.";

    //将匹配模式定义为
    //一个单词边界，一个小写字母 a，任意
    //数量的紧随其后的字母
    //数字或下划线，后跟
    //一个单词边界
    String regex = "\\ba\\w*\\b";
    Pattern p = Pattern.compile(regex);

   //为字符串文本提取 Matcher
    Matcher m = p.matcher(candidate);
    String val=null;
    //显示原始输入字符串
    System.out.println("INPUT: " + candidate);

    //显示搜索模式
    System.out.println("REGEX: " + regex +"\r\n");

   //检查 Matcher，并提取所有
   //以小写字母 a 开头的单词
    while (m.find())
    {
      val = m.group();
      System.out.println("MATCH: " + val);
    }

    //如果没有匹配项，则说明
    if (val == null) {
      System.out.println("NO MATCHES: ");
    }
  }
}
```

| **![结束示例](img/_1.gif)** |

|  |

输出 1-1：运行 FindA 的结果

| **![开始示例](img/_1.gif)** |

```
INPUT: A Matcher examines the results of applying a pattern.
REGEX: \ba\w*\b

MATCH: applying
MATCH: a
```

| **![结束示例](img/_1.gif)** |

|  |

同样，现在不需要你能够完全理解给出的代码细节。我只是想建立一个关于 J2SE 正则表达式工作方式的总体概念。首先，我定义我的 Pattern：

```
 Pattern p = Pattern.compile(regex);
```

然后，我将候选字符串提供给 Pattern 并提取一个 Matcher：

```
 Matcher m = p.matcher(candidate);
```

最后，我询问我的 Matcher：

```
 while (m.find()) {….} 
```



## 创建模式

本节将介绍一些编写正则表达式的简单技巧。我将它们称为*推*、*拉*和*组合*。如同日本柔道术，如果对手向你推，你就拉他；如果他向后拉，你就向前推。如果这些技巧都不奏效，你就把他扭成麻花。

同样，编写正则表达式时，某些方法有时会显得完全无效，而另一些方法却非常有效。我在以下各节中描述的方法只是编写模式的简单技巧。如果你尚未掌握，很快你也会培养出自己的一套正则表达式技巧，甚至可能给它们起些昵称。

### 拉取技巧

创建正则表达式最成功的方法之一，是先进行精确匹配，然后逐步将其变形为能匹配原始内容的通用正则表达式。我将此称为拉取技巧，因为我是在从精确匹配中慢慢"拉"出正则表达式。

例如，假设你想创建一个匹配四位数字的模式。因此，*1234* 应该匹配，但 *123* 不匹配，*12345* 或 *ABCD* 也不匹配。

为了这个示例，你需要引入一个正则表达式元字符 ***\d***，它能匹配从 *0* 到 *9* 的任何数字。

|  | 注意 | *元字符*用于描述另一个更复杂的字符。例如，***\n*** 是一个描述不可打印换行符的元字符。 |

回到示例，你知道

```
1234 匹配 1234
```

这当然显而易见：任何内容都匹配自身。但你也知道 \d 匹配任何数字。根据逻辑的传递性，你可以用 \d 替换最后一位数字。于是模式变为

```
1234 应匹配 123\d 
```

这里你将最后一位数字 *4* 替换为等效的元字符 ***\d***。如果你通过方便的 RX.java 程序运行此模式，会发现它确实仍然匹配。到目前为止一切顺利。实际上，情况比顺利更好：现在你的模式不仅能匹配 *1234*，还能匹配任何以数字 *123* 开头的四位数。我们越来越接近目标了。

|  | 注意 | RX.java 是本书的一个极简配套程序，你可以从 Apress 网站的下载区（[`www.apress.com`](http://www.apress.com)）获取。你可以用这个程序对候选字符串执行正则表达式模式。 |

对第三位数字重复此过程，这样 *1234* 应匹配 ***12\d\d***，这里你将 *3* 替换为等效的 ***\d***。情况越来越乐观了。这不仅匹配 *1234*，还匹配任何以数字 *12* 开头的四位数。

你能看出趋势了。最终，你将创建模式 ***\d\d\d\d***，它能匹配任意四位数字。这并非最简洁的模式，但足以满足既定需求：它匹配任何四位数。

这里的要点是，原则上你有时可以从一个具体匹配*反向*推导，创建出所需的模式。当然，这只是一个技巧，并非适用于所有情况。但这是一个值得放入你正则表达式技巧包的好方法。

### 推动技巧

另一个我发现对编写正则表达式模式很有帮助的技巧是推动技巧。推动技巧建立在已有工作的基础上，通过添加、删减或修改其范围，直到它变得有用。

与拉取技巧中从具体匹配标记入手不同，这种方法采用一个与你所需模式相似的现有正则表达式，并对其进行修改，直到它能完成所需任务。也就是说，正则表达式被"推"向另一种功能，因此得名。

例如，假设你想要一个匹配五位数字的正则表达式模式。基于前面的例子，你知道 ***\d\d\d\d*** 能匹配任意四位数字。因此，找到匹配五位数字的模式只需在前一个模式后追加另一个 ***\d*** 即可。答案当然是模式 ***\d\d\d\d\d***。

随着你深入学习本章，你会发现这些并非你能想到的最优雅的四位和五位数字匹配模式表示法，但它们是完全合理的解决方案，并且推导过程也合乎逻辑。这种推导过程才是本次讨论中需要掌握的重点。

### 组合技巧

组合技巧正如其名：将各种模式组合起来形成一个新的整体。也就是说，它通过使用其他模式来组合成一个新模式。这与推动技巧不同，因为模式本身不被修改，而只是被拼接起来。

假设你需要创建一个匹配美国邮政编码的模式，它由五位数字、一个连字符和四位数字组成。基于你已经完成的工作，这个模式非常容易创建。你知道四位数字匹配 ***\d\d\d\d***，连字符匹配自身，五位数字匹配 ***\d\d\d\d\d***。将这些组合成一个模式，就得到 ***\d\d\d\d\d-\d\d\d\d\d***。

同样，这并非最优雅简洁的邮政编码表示法，也不够灵活（五位数的邮政编码怎么办？连字符和数字之间有空格怎么办？没有连字符只有空格怎么办？），但它确实满足了既定需求。

|  | 注意 | 与大多数软件问题一样，通常可以通过明确需求来找到正则表达式难题的解决方案。 |

## 正则表达式语法介绍

以下各节将介绍 Java 的正则表达式语法。为清晰起见，材料被组织成小的逻辑单元，每个单元后附有一个演示用法的简短示例。这些示例从强调 Pattern 角色的例子，逐步过渡到开始更多依赖 Matcher 的例子。

|  | 注意 | 请记住，这些只是工作示例。我们尚未准备好让代码无懈可击。 |



### 阅读模式

正则表达式语言包含旨在帮助描述搜索条件的元字符。由于在不了解这些字符的情况下阅读模式可能会令人困惑，我在表 1-1 中列出了最常用的元字符。

表 1-1：基本正则表达式分隔符

| **模式** | **名称** | **描述** |
| --- | --- | --- |
| . | 点号 | 匹配任意单个字符。 |
| $ | 美元符号 | 匹配行尾。 |
| ^ | 脱字符 | 匹配行首。 |
| { | 左花括号 | 定义范围的开始。 |
| [ | 左方括号 | 定义字符类的开始。 |
| ( | 左圆括号 | 定义分组的开始。 |
| &#124; | 管道符号 | 表示“或”的符号 |
| } | 右花括号 | 定义范围的结束。 |
| ] | 右方括号 | 定义字符类的结束。 |
| ) | 右圆括号 | 定义分组的结束。 |
| * | 星号 | 前面的元素重复零次或多次。 |
| + | 加号 | 前面的元素重复一次或多次。 |
| ? | 问号 | 前面的元素重复零次或一次。 |
| \ | 反斜杠 | 后面的字符不被视为元字符。 |

这些字符实际上是保留字，就像 `new` 是 Java 中的保留字一样。它们是构建更复杂搜索条件的基础模块。稍后我将对此进行更详细的讨论。

如果你在正则表达式模式中读取一个字符，并且它不在表 1-1 所列的字符中，那么你正在读取的这个字符很可能就代表它本身。例如，表 1-2 展示了如何解读模式 ***hello****。

表 1-2：模式 *hello* ^([*])

| **字母** | **描述** |
| --- | --- |
| h | 字符 *h* |
| e | 后跟字符 *e* |
| l | 后跟字符 *l* |
| l | 后跟字符 *l* |
| o | 后跟字符 *o* |
| * | 后跟一个元字符，在此情况下表示 *o* 应重复零次或多次 |
| ^([*])*中文释义：* 查找单词 *hell*，后跟任意数量的尾随 *o* 字符。 |

如果你确实需要查找这些字符之一，例如 ***** 字符，只需在你搜索的字符前加上一个 ***\*** 字符。例如，要查找 ******* 字符，请使用 ***\****。

### 常见字符与边界字符

正则表达式还包含一些字符，当它们由 ***\*** 字符分隔时，会具有特殊含义。这些字符有助于查找常见的标记，例如单词边界、空格、制表符、字母数字字符等。例如，***\n*** 和 ***\t*** 是分别表示换行符和制表符的特殊字符。

在本节中，我将介绍这些常见的边界字符并提供使用示例。

#### 常见字符

某些类型的字符出现频率很高，以至于正则表达式语言已经发展出引用它们的简写形式。例如，数字由 ***\d*** 表达式表示。如果没有 ***\*** 字符来分隔 ***d***，该表达式就只是指英文字母表中的第四个小写字母。表 1-3 列出了一些常见的字符。

表 1-3：常见字符与边界字符

| **字符** | **描述** |
| --- | --- |
| . | 匹配任意字符；也可能匹配行终止符。 |
| \d | 一个数字 ***[0-9]***。这将匹配从 *0* 到 *9* 的任意单个数字。请注意，输入 *19* 需要匹配两次：一次匹配 *1*，一次匹配 *9*。 |
| \D | 一个非数字 ***[⁰-9]***。这将匹配任何不是数字的字符，包括空白字符。 |
| \w | 一个单词字符 ***[a-zA-Z_0-9]***。这将匹配从 *a* 到 *z* 或 *A* 到 *Z* 的任何字符、下划线或从 *0* 到 *9* 的任意单个数字。 |
| \W | 一个非单词字符 ***[^\w]***。这将匹配任何不是单词字符的字符，例如数字，包括空白字符。 |
| \t | 制表符。 |
| \n | 换行符。 |
| \r | 回车符。 |
| \f | 换页符。 |
| \s | 一个空白字符。这包括换行符、回车符、制表符、换页符和行结束符。 |
| \S | 一个非空白字符，也称为 ***[^\s]***。这将匹配任何不是空白字符的字符，如前所述。 |
| ^ | 行首。 |
| $ | 行尾。 |
| \b | 一个单词边界。*单词边界* 是紧邻我们通常所说的英语“单词”之前的字符，对应于之前的 ***\w***。它也会匹配紧跟在单词之后的字符。大多数情况下，此字符匹配空格、制表符、行尾或行首。 |
| \B | 一个非单词边界。 |

#### 常见字符示例

假设你需要验证一个给定的字符串是否由任意字母数字字符（包括下划线）后跟一个数字组成。因此，你会接受 *A1*，但不会接受 *!1*，因为 *!* 符号不是字母数字字符或下划线。在这种情况下，你需要的模式由一个字母数字字符（或下划线）后跟一个数字组成；因此，根据表 1-1，模式为 ***\w\d***。

模式 ***\w\d*** 将匹配 *h1、k9、A1* 或 *11*，因为每个字符串都由一个字母数字字符后跟一个数字组成。它不会匹配 *AA、9A* 或 **5*，因为这些字符串不是由一个字母数字字符后跟一个数字组成的。表 1-4 对这个模式进行了剖析。

表 1-4：模式 *\w\*d

| **正则表达式** | **描述** |
| --- | --- |
| **\w** | 任意字符，范围从 *a* 到 *z*、*A* 到 *Z*、*0* 到 *9*，或下划线 |
| **\d** | 后跟一个从 *0* 到 *9* 的单个数字 |
| * **中文释义：** 查找任意字母数字字符或下划线字符，后跟一个单个数字。 |

#### 边界字符

正则表达式还提供了一种查找常见字符边界的机制。这些包括换行符、行结束符、文件结束符、制表符等。这些列在表 1-3 的后半部分。

#### 边界字符示例

假设你想从输入字符串中匹配单词 `anna`，但仅当它位于单词开头时。因此，`Hanna` 不符合你的条件。在这种情况下，你需要的模式由一个单词边界 ***\b*** 后跟字符 *a、n、n* 和 *a* 组成，因此正则表达式为 ***\banna***。

模式 ***\banna*** 将匹配 *anna* 但不会匹配 *Hanna*，因为 *anna* 是一个前面有空格字符的字符簇。空格字符满足作为单词边界的条件。而 *Hanna* 则不然，因为在 *Hanna* 中紧邻 *a* 字符之前的字符是 *H*，而 *H* 不是单词边界。表 1-5 对这个模式进行了剖析。

表 1-5：模式 *\bann*a

| **正则表达式** | **描述** |
| --- | --- |
| \b | 一个单词边界 |
| a | 后跟字符 *a* |
| n | 后跟字符 *n* |
| n | 后跟字符 *n* |
| a | 后跟字符 *a* |
| * **中文释义：** 如果 *anna* 是一个单词的开头，则查找它。 |


好的，作为一名高级文档工程师和翻译员，我将严格遵循您提供的注意事项和示例格式，将给定的英文文本翻译成中文。


### 量词与选择符

*量词*和*选择符*允许你指定需要查找的令牌数量或你愿意接受的替代令牌。表 1-6 列出了正则表达式中的一些量词和选择符。

表 1-6：量词

| **正则表达式** | **描述** |
| --- | --- |
| **?** | 前面的元素重复一次或不重复。 |
| ***** | 前面的元素重复零次或多次。 |
| **+** | 前面的元素重复一次或多次。 |
| **{n}** | 前面的元素恰好重复 *n* 次。 |
| **{n,}** | 前面的元素至少重复 *n* 次。 |
| **{n,m}** | 前面的元素至少重复 *n* 次，但不超过 *m* 次。包括 *m* 次重复。 |
| **&#124;** | ***&#124;*** 之前的元素或之后的元素。 |

以下部分提供了一些演示量词使用的示例。

#### 重复字符示例 1

模式 ***An+a*** 将匹配 *Ana*、*Anna* 或 *Annnna*，因为它们都包含至少一个 *A* 字符，紧接着是一个或多个 *n* 字符，再紧接着是一个 *a* 字符。它不会匹配 ***Aa*** 或 ***ANna***，因为它们不是由一个 *A* 字符、紧接着至少一个 *n* 字符、再紧接着一个 *a* 字符组成的。请注意，大写 *N* 和小写 *n* 不被视为匹配。表 1-7 剖析了该模式。

表 1-7：模式 *An+*a

| **正则表达式** | **描述** |
| --- | --- |
| A | 字符 *A* |
| n+ | 后跟一个或多个 *n* 字符 |
| a | 后跟字符 *a* |
| * **用英语描述：** 查找一个大写 *A*，后跟一个或多个 *n* 字符，再后跟一个 *a* 字符。 |

这里可以引出一些有趣的行为。如果使用 `String.matches` 方法执行此匹配，该模式将无法匹配 *AnnaMarie*，因为 `String.matches` 方法要求精确匹配，而 *AnnaMarie* 中的 *Marie* 部分会破坏这种精确性。然而，`Matcher.find` 方法会匹配 *AnnaMarie*，因为它更宽松。敬请期待——更多细节即将呈现。

#### 重复字符示例 2

模式 ***A{2,7}*** 将匹配 *AA*、*AAAA* 或 *AAAAAAA*，因为它们都包含至少两个 *A* 字符且不超过七个 *A* 字符。该模式不会匹配 *A*，因为它包含少于两个 *A* 字符；也不会匹配 *AAAAAAA*，因为它包含多于七个 *A* 字符。表 1-8 剖析了该模式。

表 1-8：模式 *A{2,7*}

| **正则表达式** | **描述** |
| --- | --- |
| **A** | 字符 *A* |
| **{** | 开始重复组 |
| **2** | 至少重复两次 |
| , | 但不超过 |
| **7** | 七次 |
| **}** | 结束重复组 |
| * **用英语描述：** 查找字符 *A* 重复两次、三次、四次、五次、六次或七次的序列。 |

|  | 注意  | 在本章开头的示例中，你需要一个模式来匹配四个连续的数字，并推导出了 ***\d\d\d\d***。如前所述，这并不是最优雅的模式。根据表 1-6，表达相同模式的另一种等效方式是 ***\d{4}***——即，一个恰好由四个数字组成的序列。 |

#### 选择字符示例 1

模式 ***A|B*** 将匹配 *A* 或 *B*，因为它们都由一个 *A* 字符或一个 *B* 字符组成。它不会匹配 *P*、*Q* 或 *jelly*，因为它们严格来说既不是 *A* 也不是 *B* 字符。表 1-9 剖析了该模式。

表 1-9：模式 *A|*B

| **正则表达式** | **描述** |
| --- | --- |
| **A** | 字符 *A* |
| **&#124;** | 或 |
| **B** | 字符 *B* |
| * **用英语描述：** 查找大写 *A* 或大写 *B*。 |

#### 选择字符示例 2

模式 ***anna|marie*** 将匹配 *anna* 或 *marie*，因为 *anna* 匹配第一个选项，而 *marie* 匹配第二个选项。它不会匹配 *Josie*、*Ralph* 或 *Doctor*。表 1-10 剖析了该模式。

表 1-10：模式 *anna|mari*e

| **正则表达式** | **描述** |
| --- | --- |
| **anna** | 字符 *a*、*n*、*n* 和 *a*，按顺序排列 |
| **&#124;** | 或 |
| **marie** | 字符 *m*、*a*、*r*、*i* 和 *e*，按顺序排列 |
| * **用英语描述：** 查找单词 *anna* 或单词 *marie*。 |

那么，该模式会将 *annamarie* 作为一个单词匹配吗？简而言之，也许吧。我将在后续章节中提供关于此主题的详细信息，但这里先做一个简要介绍。Java 2 企业版 (J2EE) 的正则表达式允许你指定是需要精确匹配还是部分匹配。因此，对于部分匹配，*annamarie* 会匹配模式 ***anna|marie*** 两次；而对于精确匹配，则完全不匹配。在不深入细节的情况下，`String.matches` 仅提供精确匹配，而 `Matcher` 类可以使用 `find` 方法提供更宽松的匹配。

那么模式 ***Miss anna|marie*** 呢？它会匹配 *Miss marie* 和 *Miss anna*，还是只匹配其中一个？或者两者都不匹配？严格匹配会匹配 *Miss anna*，但拒绝 *Miss marie*。选择符 ***|*** 会将 *Miss anna* 视为一个选项，将模式 *marie* 视为另一个选项。因为模式 ***maria*** 不等于候选字符串 *Miss maria*，所以搜索会拒绝 *Miss maria*。

### 字符类

有时，你需要将搜索条件描述为一个*类*——即，作为一个共享潜在复杂共性的组，你需要能够描述这些共性，并且没有预定义的类。幸运的是，正则表达式通过字符类提供了一种实现此目的的机制，如表 1-11 所示。

表 1-11：字符类

| **模式** | **描述** |
| --- | --- |
| [abc] | *a*、*b* 或 *c*。（当然，可以使用任何字符，不限于 *a*、*b* 或 *c*。） |
| ***[^abc]*** | 除 *a*、*b* 或 *c* 之外的任何字符。 |
| [a-zA-Z] | *a* 到 *z* 或 *A* 到 *Z*。 |
| [a-d[m-p]] | *a* 到 *d*，或 *m* 到 *p*：***[a-dm-p]***。 |
| [a-z&&[def]] | 两个集合中都存在的字符，即 *d*、*e* 或 *f*。 |
| [a-z&&[^bc]] | *a* 到 *z*，但排除 *b* 和 *c*：***[ad-z]***。 |
| [a-z&&[^m-p]] | *a* 到 *z*，但不包括 *m* 到 *p*：***[a-lq-z]***。 |

还有一些预定义的 POSIX（可移植操作系统接口）字符类。这些是 ASCII（美国信息交换标准代码）类，经验表明它们特别有用。因此，它们已经就绪，你可以直接引用它们。表 1-12 包含了 POSIX 字符类。

表 1-12：POSIX 字符类

| **模式** | **描述** |
| --- | --- |
| \p{Lower} | 小写字母：***[a-z]*** |
| \p{Upper} | 大写字母：***[A-Z]*** |
| \p{ASCII} | 所有 ASCII 字符：***[\x00-\x7F]*** |
| \p{Alpha} | 大写或小写字母：***[\p{Lower}\p{Upper}]*** |
| \p{Digit} | 数字：***[0-9]*** |
| \p{Alnum} | 数字或字母：***[\p{Alpha}\p{Digit}]*** |
| \p{Punct} | 标点符号：***!"#$%&'()*+,-./:;<=>?@[\]^_`{&#124;}~*** 之一 |
| \p{Graph} | 任何可见字符：***[\p{Alnum}\p{Punct}]*** |
| \p{Print} | 可打印字符：***[\p{Graph}]*** |
| \p{Blank} | 制表符或空格 |
| \p{Cntrl} | 控制字符：***[\x00-\x1F\x7F]*** |
| \p{XDigit} | 十六进制数字：***[0-9a-fA-F]*** |
| \p{Space} | 空白字符：***[ *** ***\t\n\x0B\f\r]*** |



#### 简单类示例

让我们逐步看几个简单示例。模式 ***[0-5]*** 将匹配输入中包含 *0* 到 *5* 之间任意数字的部分。因此，它将匹配 *0、1、2、3、4* 或 *5*，但不会匹配 *8、6* 或任何非数字字符。表 1-13 对该模式进行了剖析。

表 1-13：模式 *[0-5*]

| **正则表达式** | **描述** |
| --- | --- |
| [ | 一个类，包含 |
| 0 | 数字 0 |
| - | 范围至 |
| 5 | 数字 5 |
| ] | 类结束 |
| * **中文释义：** 查找从 *0* 到 *5*（含 *0* 和 *5*）的任意数字。 |

#### 否定示例

模式 ***[^A]*** 将匹配除字符 *A* 之外的任何字符。这包括其他字符、空格、制表符、标点符号等。需要注意的是，**^** 分隔符只有在类括号内（即 ***[*** 和 ***]*** 括号之间）才具有“非”的含义。在这些括号之外，它表示行首字符。我将在后面更详细地讨论这个主题。表 1-14 对该模式进行了剖析。

表 1-14：模式 *[^A*]

| **正则表达式** | **描述** |
| --- | --- |
| [ | 一个类，包含 |
| ^ | 除……之外的任意字符 |
| A | 字符 *A* |
| ] | 类结束 |
| * **中文释义：** 查找除大写字母 *A* 之外的任意字符。 |

#### 分组

分组是一个子匹配。如果你熟悉 SQL，将分组视为 SQL 中的子查询可能会有所帮助。分组允许你将模式的一部分定义为整体的逻辑子单元，然后引用这些子单元的结果。其语法如下表 1-15 所示。

表 1-15：分组

| **正则表达式** | **描述** |
| --- | --- |
| ( | 一个组，包含 |
| … | 任意正则模式 |
| ) | 组结束 |

#### 分组示例

与大多数事物一样，示例可能比描述更具启发性。考虑模式 ***(\w+)_(\w+)@(\w+)\.org*** 用于匹配电子邮件模式。表 1-16 对该模式进行了剖析。

表 1-16：模式 *(\w+)_(\w+)@(\w+)\.or*g

| **正则表达式** | **描述** |
| --- | --- |
| ( | 一个组，包含 |
| \w | 一个字母数字或下划线字符 |
| + | 重复一次或多次 |
| ) | 组结束 |
| _ | 后跟一个下划线字符 |
| ( | 一个组，包含 |
| \w | 一个字母数字或下划线字符 |
| + | 后跟一个或多个字母数字字符 |
| ) | 组结束 |
| @ | 后跟一个 at 符号 |
| ( | 一个组，包含 |
| \w | 一个字母数字或下划线字符 |
| + | 后跟一个或多个字母数字或下划线字符 |
| ) | 组结束 |
| \. | 后跟句点字符 |
| o | 后跟字符 *o* |
| r | 后跟字符 *r* |
| g | 后跟字符 *g* |
| * **中文释义：** 查找一组字母数字字符，后跟 *_*，再后跟一组字母数字字符，后跟 *@*，再后跟一组字母数字字符，最后跟 .*org*。 |

## 将 Java 与正则表达式集成

到目前为止，你几乎只使用了正则表达式，但并未真正与 Java 结合。现在是时候考虑两者如何交互了。以下示例与前面的示例不同，因为它们将 Java 代码与正则表达式结合在了一起。它们更全面地展示了如何使用一些 J2SE 正则表达式语法。

你将在此看到的一些正则表达式比之前看到的示例稍微高级一些，因为它们建立在本章迄今讨论的基础之上。例如，清单 1-2 将分组与量词结合在了一起。

清单 1-2：MatchPhoneNumber.java

| **![示例开始](img/_1.gif)** |

```
import java.util.regex.*;
public class MatchPhoneNumber{
   public static void main(String args[]){
      isPhoneValid(args[0]);
   }

   /**
   * 确认给定电话号码的格式是否有效。
   * @param phone 是一个表示电话号码的字符串。
   * @returns 如果电话号码格式可接受则返回 true。
   */
   public static boolean isPhoneValid(String phone){
      boolean retval=false;

      String phoneNumberPattern =
        "(\\d-)?(\\d{3}-)?\\d{3}-\\d{4}";

      retval= phone.matches(phoneNumberPattern);

      // 准备一条指示成功或失败的消息
      String msg = "   不匹配: 模式:" + phone
             + "\r\n             正则: " + phoneNumberPattern;

      if (retval){
      msg = "   匹配   : 模式:" + phone
          + "\r\n             正则: " + phoneNumberPattern;
      }

      System.out.println(msg +"\r\n");
      return retval;
   }
}
```

| **![示例结束](img/_1.gif)** |

|  |

如果这些模式现在对你来说不完全清晰，请不要气馁。随着你继续阅读本书，你会逐渐形成直观的理解。请专注于这些概念，并熟悉 Java 代码与正则表达式如何相辅相成。

要充分利用以下示例，你只需要了解两点信息：

*   任何以 **\** 分隔的正则表达式元字符在 Java 代码中使用时，都需要再次进行转义。因此，***\d*** 在 Java 代码中变为 ***\\d***，***\s*** 变为 ***\\s***。相应地，更复杂的表达式如 ***(\d-)?(\d{3}-)?\d{3}-\d{4}\s*** 在 Java 代码中变为 ***(\\d-)?(\\d{3}-)?\\d{3}-\\d{4}\\s***。所有 ***\*** 字符在用于 String 对象时都需加倍以产生 ***\\***。

*   在本书中，当我单独讨论正则表达式本身时，我不使用双重转义机制。但在处理具体编码示例时，我会使用。

*   `String.matches(String regex)` 方法是添加到 String 类中的一个新方法。它将调用该方法的字符串与给定的正则表达式 `regex` 进行比较，如果正则模式*完全*匹配该字符串，则返回 true。完全匹配意味着所讨论的字符串不能包含任何未被正则模式涵盖的字符——甚至包括换行符和空格等不可见字符。



### 验证电话号码格式示例

清单 1-2 中的代码仅用于判断给定的电话号码是否符合格式良好的标准。它利用了表 1-6 中介绍的两个元字符。具体来说，它使用了范围限定符 ***{n,m}***，表示前面的字符或类必须至少重复 *n* 次，最多重复 *m* 次。它还使用了字符 `?`，表示前面的字符或类必须出现零次或一次。

整个模式用于检查一个七位数字，其前面可以带有可选的国家代码和区号。输出 1-2 显示了运行程序的结果，表 1-19 则对该模式进行了剖析。

表 1-19：模式 *(\d-)?(\d{3}-)?\d{3}-\d{4}*

| **正则表达式** | **描述** |
| --- | --- |
| ( | 一个分组，包含 |
| \d | 一个数字 |
| - | 后跟一个连字符 (-) |
| ) | 该分组结束 |
| ? | 查找前面元素出现零次或一次 |
| ( | 后跟一个分组，包含 |
| \d | 一个数字 |
| { | 至少重复 |
| 3 | 三次 |
| } | 重复结束 |
| - | 后跟一个连字符 |
| ) | 该分组结束 |
| ? | 查找前面元素出现零次或一次 |
| \d | 后跟一个数字 |
| { | 至少重复 |
| 3 | 三次 |
| } | 重复结束 |
| - | 后跟一个连字符 |
| \d | 后跟一个数字 |
| { | 至少重复 |
| 4 | 四次 |
| } | 重复结束 |
| * **中文释义：** 查找一个数字后跟一个连字符。这部分是可选的。然后，查找三个数字后跟一个连字符。这部分也是可选的。接下来，查找三个数字，后跟一个连字符，再后跟四个数字。 |

输出 1-2：运行 MatchPhoneNumber.java 的结果

| **![开始示例](img/_1.gif)** |

```
C:\RegEx\Examples\chapter1>java MatchPhoneNumber "1-999-111-2222"
   MATCH  : pattern:1-999-111-2222
            regex: (\d-)?(\d{3}-)?\d{3}-\d{4}

C:\RegEx\Examples\chapter1>java MatchPhoneNumber "999-111-2222"
   MATCH   : pattern:999-111-2222
             regex: (\d-)?(\d{3}-)?\d{3}-\d{4}

C:\RegEx\Examples\chapter1>java MatchPhoneNumber "1-111-2222"
   MATCH   : pattern:1-111-2222
             regex: (\d-)?(\d{3}-)?\d{3}-\d{4}

C:\RegEx\Examples\chapter1>java MatchPhoneNumber "111-2222"
   MATCH   : pattern:111-2222
             regex: (\d-)?(\d{3}-)?\d{3}-\d{4}
C:\RegEx\Examples\chapter1>java MatchPhoneNumber "1.999-111-2222"
   NO MATCH: pattern:1.999-111-2222
             regex: (\d-)?(\d{3}-)?\d{3}-\d{4}

C:\RegEx\Examples\chapter1>java MatchPhoneNumber "999 111-2222"
   NO MATCH: pattern:999 111-2222
             regex: (\d-)?(\d{3}-)?\d{3}-\d{4}

C:\RegEx\Examples\chapter1>java MatchPhoneNumber "1 111 2222"
   NO MATCH: pattern:1 111 2222
             regex: (\d-)?(\d{3}-)?\d{3}-\d{4}

C:\RegEx\Examples\chapter1>java MatchPhoneNumber "111-JAVA"
   NO MATCH: pattern:111-JAVA
             regex: (\d-)?(\d{3}-)?\d{3}-\d{4}
```

| **![结束示例](img/_1.gif)** |

|  |

### 验证邮政编码格式示例

清单 1-3 中的代码用于判断邮政编码是否符合格式良好的标准。它检查一个五位数字，后面可以可选地跟一个连字符和四位数字。输出 1-3 显示了运行程序的结果。表 1-20 则对该模式进行了剖析。

表 1-20：模式 *\d{5}(-\d{4})?*

| **正则表达式** | **描述** |
| --- | --- |
| \d | 一个数字 |
| { | 至少重复 |
| 5 | 五次 |
| } | 重复结束 |
| ( | 开始分组 |
| - | 包含一个连字符 |
| \d | 一个数字 |
| { | 至少重复 |
| 4 | 四次 |
| } | 重复结束 |
| ) | 该分组结束 |
| ? | 查找前面元素出现零次或一次 |
| * **中文释义：** 查找五个数字，后面可选地跟一个连字符和四个数字。 |

清单 1-3：MatchZipCodes.java

| **![开始示例](img/_1.gif)** |

```
import java.util.regex.*;
import java.io.*;

public class MatchZipCodes{
   public static void main(String args[]){
      isZipValid(args[0]);
   }

   /**
   * 确认给定邮政编码的格式是否有效。
   * @param zip 是一个表示邮政编码的字符串。
   * @returns 如果邮政编码格式可接受则返回 true。
   */
   public static boolean isZipValid(String zip){
      boolean retval=false;
      String zipCodePattern = "\\d{5}(-\\d{4})?";
      retval = zip.matches(zipCodePattern);

      //准备一条指示成功或失败的消息
      String msg = "   NO MATCH: pattern:" + zip
             + "\r\n             regex: " + zipCodePattern;

      if (retval){
      msg = "   MATCH   : pattern:" + zip
          + "\r\n             regex: " + zipCodePattern;
      }

      System.out.println(msg +"\r\n");
      return retval;
   }
}
```

| **![结束示例](img/_1.gif)** |

|  |

输出 1-3：运行 MatchZipCodes.java 的结果

| **![开始示例](img/_1.gif)** |

```
C:\RegEx\Examples\chapter1>java MatchZipCodes "45643-4443"
   MATCH  : pattern:45643-4443
            regex: \d{5}(-\d{4})?

C:\RegEx\Examples\chapter1>java MatchZipCodes "45643"
   MATCH   : pattern:45643
             regex: \d{5}(-\d{4})?

C:\RegEx\Examples\chapter1>java MatchZipCodes "443"
   NO MATCH: pattern:443
             regex: \d{5}(-\d{4})?

C:\RegEx\Examples\chapter1>java MatchZipCodes "45643-44435"
   NO MATCH: pattern:45643-44435
             regex: \d{5}(-\d{4})?

C:\RegEx\Examples\chapter1>java MatchZipCodes "45643 44435"
   NO MATCH: pattern:45643 44435
             regex: \d{5}(-\d{4})?
```

| **![结束示例](img/_1.gif)** |

|  |



### 确认日期示例

清单 1-4 中的代码用于检查给定日期的格式。它确认给定的日期格式由一位或两位数字、后跟一个连字符、再后跟一位或两位数字、后跟一个连字符、最后跟四位数字组成。输出 1-4 显示了运行程序的结果。表 1-21 对该模式进行了详细解析。

表 1-21：模式 *\d{1,2}-\d{1,2}-\d{4}*

| **正则表达式** | **描述** |
| --- | --- |
| \d | 一个数字 |
| { | 重复至少 |
| 1 | 一次 |
| , | 但不超过 |
| 2 | 两次 |
| } | 重复结束 |
| - | 后跟一个连字符 |
| \d | 后跟一个数字 |
| { | 重复至少 |
| 1 | 一次 |
| , | 但不超过 |
| 2 | 两次 |
| } | 重复结束 |
| - | 后跟一个连字符 |
| \d | 后跟一个数字 |
| { | 重复至少 |
| 1 | 四次 |
| } | 重复结束 |
| * **中文释义：** 查找一位或两位数字，后跟一个连字符，再后跟一位或两位数字，后跟一个连字符，最后跟四位数字。 |

清单 1-4：MatchDates.java

| **![开始示例](img/_1.gif)** |

```
import java.util.regex.*;
import java.io.*;

public class MatchDates{
   public static void main(String args[]){
      isDateValid(args[0]);
   }
   /**
   * 确认给定的日期格式由一位或两位数字
   * 后跟一个连字符，再后跟一位或两位数字，后跟
   * 一个连字符，最后跟四位数字组成
   * @param date 是表示日期的字符串。
   * @returns 如果日期格式可接受则返回 true。
   */
   public static boolean isDateValid(String date){
      boolean retval=false;

      String datePattern ="\\d{1,2}-\\d{1,2}-\\d{4}";
      retval = date.matches(datePattern);

      //准备一条指示成功或失败的消息
      String msg = "   NO MATCH: pattern:" + date
             + "\r\n             regexLength: " + datePattern;

      if (retval){
      msg = "   MATCH   : pattern:" + date
          + "\r\n             regexLength: " + datePattern;
      }

      System.out.println(msg +"\r\n");
      return retval;
   }
}
```

| **![结束示例](img/_1.gif)** |

|  |

输出 1-4：运行 MatchDates.java 的结果

| **![开始示例](img/_1.gif)** |

```
C:\RegEx\Examples\chapter1>java MatchDates "04-02-1999"
   MATCH  : pattern:04-02-1999
            regexLength: \d{1,2}-\d{1,2}-\d{4}

C:\RegEx\Examples\chapter1>java MatchDates "15-42-1999"
   MATCH   : pattern:15-42-1999
             regexLength: \d{1,2}-\d{1,2}-\d{4}

C:\RegEx\Examples\chapter1>java MatchDates "April fourth nineteen ninety nine"
   NO MATCH: pattern:April fourth nineteen ninety nine
             regexLength: \d{1,2}-\d{1,2}-\d{4}
C:\RegEx\Examples\chapter1>java MatchDates "15-42-20002"
   NO MATCH: pattern:15-42-20002
             regexLength: \d{1,2}-\d{1,2}-\d{4}

C:\RegEx\Examples\chapter1>java MatchDates "02-02-20002"
   NO MATCH: pattern:02-02-20002
             regexLength: \d{1,2}-\d{1,2}-\d{4}

C:\RegEx\Examples\chapter1>java MatchDates "04-02-02"
   NO MATCH: pattern:04-02-02
             regexLength: \d{1,2}-\d{1,2}-\d{4}

C:\RegEx\Examples\chapter1>java MatchDates "04-02-garbage"
   NO MATCH: pattern:04-02-garbage
             regexLength: \d{1,2}-\d{1,2}-\d{4}
```

| **![结束示例](img/_1.gif)** |

|  |

### 确认姓名格式示例

清单 1-5 中的代码用于判断给定的姓名是否符合格式良好的标准。它查找一个名字标记、一个可选的中名标记，最后是一个姓氏标记。在本示例中，一个姓名标记由一个大写字母后跟任意数量的小写字母组成。

清单 1-5：MatchNameFormats.java

| **![开始示例](img/_1.gif)** |

```
import Java.util.regex.*;
import java.io.*;

public class MatchNameFormats{
   public static void main(String args[]){

isNameValid(args[0]);
   }

/**
   * 确认给定姓名的格式是否有效。
   * @param name 是表示姓名的字符串。
   * @returns 如果姓名格式可接受则返回 true。
   */
   public static boolean isNameValid(String name){
     boolean retval=false;

String nameToken ="\\p{Upper}(\\p{Lower}+\\s?)";

String namePattern = "("+nameToken+"){2,3}";

retval = name.matches(namePattern);

//准备一条指示成功或失败的消息
     String msg = "NO MATCH: pattern:" + name
          + "\r\n           regex :" + namePattern;

if (retval){
     msg = "MATCH     pattern:" + name
          + "\r\n           regex :" + namePattern;
     }

System.out.println(msg +"\r\n");
     return retval;
     }
} 
```

| **![结束示例](img/_1.gif)** |

|  |

这个示例很有趣，因为它比前一个示例更充分地利用了 Java 的健壮性。具体来说，你定义了你所说的“姓名标记”的含义：

```
 String nameToken ="\\p{Upper}(\\p{Lower}+\\s?)";
```

然后你在后面使用了这个定义：

```
 String namePattern = "("+nameToken+"){2,3}"; 
```

|  | 注意  | ***\p{Upper}*** 和 ***\p{Lower}*** 将在稍后介绍。它们分别简单地表示任意大写字符和任意小写字符。 |

这有助于避免正则表达式模式变得过于复杂，也有助于隔离错误。随着本书中的示例越来越复杂，你将开始看到将正则表达式与 Java 强大的语言相结合可以带来诸多好处，而仅使用正则表达式的话，这些好处充其量也只能是简洁地表达。清单 1-5 展示了程序 MatchNameFormats.java，输出 1-5 展示了运行该程序的结果，表 1-22 对该模式进行了详细解析。

表 1-22：模式 *(\p{Upper}(\p{Lower}+\s?)){2,3}*

| **正则表达式** | **描述** |
| --- | --- |
| ( | 一个组，包含 |
| \p{Upper} | 一个大写字符 |
| ( | 后跟一个内部组，包含 |
| \p{Lower} | 一个小写字符 |
| + | 重复一次或多次 |
| \s? | 后跟一个可选的空格 |
| ) | 内部组结束 |
| ) | 外部组结束 |
| { | 重复至少 |
| 2 | 两次 |
| , | 但不超过 |
| 3 | 三次 |
| } | 重复结束 |
| * **中文释义：** 查找两个或三个单词，每个单词以大写字母开头，后跟任意数量的小写字母。每个单词后面可以跟一个空格。 |

输出 1-5：运行 MatchNameFormats.java 的结果

| **![开始示例](img/_1.gif)** |

```
C:\RegEx\Examples\chapter1>java MatchNameFormats "John Smith"
MATCH    pattern:John Smith
          regex :(\p{Upper}(\p{Lower}+\s?)){2,3}

C:\RegEx\Examples\chapter1>java MatchNameFormats "John McGee"
MATCH     pattern:John McGee
           regex :(\p{Upper}(\p{Lower}+\s?)){2,3}

C:\RegEx\Examples\chapter1>java MatchNameFormats "John Willliam Smith"
MATCH     pattern:John Willliam Smith
           regex :(\p{Upper}(\p{Lower}+\s?)){2,3}

C:\RegEx\Examples\chapter1>java MatchNameFormats "John Q Smith"
NO MATCH: pattern:John Q Smith
           regex :(\p{Upper}(\p{Lower}+\s?)){2,3}

C:\RegEx\Examples\chapter1>java MatchNameFormats "John allen Smith"
NO MATCH: pattern:John allen Smith
           regex :(\p{Upper}(\p{Lower}+\s?)){2,3}

C:\RegEx\Examples\chapter1>java MatchNameFormats "John"
NO MATCH: pattern:John
           regex :(\p{Upper}(\p{Lower}+\s?)){2,3}
```



| **![结束示例](img/_1.gif)** |

|  |

从这个例子中自然会引出几个问题：

*   *为什么 John Q Public 会失败？* 因为 *Q* 不是一个名称标记，根据你对名称标记的定义（即一个大写字母后跟一个或多个小写字母）。

*   *为什么 John allen Smith 会失败？* 因为 *allen* 不是以大写字母开头。

*   *为什么 John 会失败？* 尽管 *John* 是一个有效的名称标记，但它没有重复出现两到三次。它仅仅是一个名称标记。

*   *为什么 John McGee 会通过？* *McGee* 并非一个大写字母后跟任意数量的小写字母。*请尝试自己解决这个问题。答案在本章末尾的“常见问题解答”部分给出。

这个例子使用了本章开头提到的组合技术。也就是说，它使用了先前定义的模式来组合成一个新模式。仔细想想，这是一种非常工程化的做法：构建小模块，然后使用这些模块来构建更复杂的部分。

### 确认地址示例

代码清单 1-6 中的代码仅用于判断给定的地址是否符合格式良好的标准。它利用了之前创建的名称和邮政编码模式，并添加了自己的地址模式。输出 1-6 显示了运行程序的结果。表 1-23 对该模式进行了剖析。

表 1-23：模式 *^(\p{Upper}(\p{Lower}+\s?)){2,3}\w+ .*, \w+ \d{5}(-\d{4})?$*

| **正则表达式** | **描述** |
| --- | --- |
| ^ | 行首，后跟 |
| ( | 一个组，包含 |
| \p{Upper} | 一个大写字符 |
| ( | 后跟一个内部组，包含 |
| \p{Lower} | 一个小写字符 |
| + | 重复一次或多次 |
| \s? | 后跟一个可选的空格 |
| ) | 内部组结束 |
| ) | 外部组结束 |
| { | 至少重复 |
| 2 | 两次 |
| , | 但不超过 |
| 3 | 三次 |
| <space> | 后跟一个空格 |
| \w | 后跟任意字母数字字符 |
| + | 重复一次或多次 |
| <space> | 后跟一个空格 |
| . | 后跟任意字符 |
| * | 重复任意次数 |
| , | 后跟一个逗号 |
| <space> | 后跟一个空格 |
| \w | 后跟任意字母数字字符 |
| + | 重复一次或多次 |
| <space> | 后跟一个空格 |
| \d | 后跟一个数字 |
| { | 至少重复 |
| 5 | 五次 |
| } | 重复结束 |
| ( | 开始一个组 |
| - | 包含一个连字符 |
| \d | 一个数字 |
| { | 至少重复 |
| 4 | 四次 |
| } | 重复结束 |
| ) | 该组结束 |
| ? | 查找零个或一个前面的元素 |
| * **用英语解释：** 查找一个先前定义的名称标记，后跟一些单词、一个逗号，然后是更多单词，最后跟一个邮政编码。这个例子使用了组合技术。 |

代码清单 1-6：MatchAddress.java

| **![开始示例](img/_1.gif)** |

```
import java.util.regex.*;
import java.io.*;

public class MatchAddress{
    public static void main(String args[]){
        isAddressValid(args[0]);
    }

/**
    * 确认给定地址的格式是否有效。
    * @param addr 是表示地址的字符串
    * @returns 如果邮政编码格式可接受则返回 true。
    */
    public static boolean isAddressValid(String addr){
       boolean retval = false;

//使用之前创建的名称模式。
       String nameToken ="\\p{Upper}(\\p{Lower}+\\s?)";

String namePattern = "("+nameToken+"){2,3}";

//使用之前创建的邮政编码模式。
       String zipCodePattern = "\\d{5}(-\\d{4})?";

//构建一个地址模式
      String addressPattern = "^" + namePattern
         + "\\w+ .*, \\w+ " + zipCodePattern +"$";

retval= addr.matches(addressPattern);

//准备一条指示成功或失败的消息
      String msg = "NO MATCH\npattern:\n " + addr
         + "\nregexLength:\n "
         + addressPattern;

if (retval){
      msg = "MATCH\npattern:\n " + addr
         + "\nregexLength:\n "
         + addressPattern;
      }
      System.out.println(msg +"\r\n");
      return retval;
   }
}
```

| **![结束示例](img/_1.gif)** |

|  |

输出 1-6：运行 MatchAddress.java 的结果

| **![开始示例](img/_1.gif)** |

```
C:\RegEx\chapter_1\Examples\chapter1>
java MatchAddress "John Smith 888 Luck Street,
NY 64332"
MATCH
pattern:
 John Smith 888 Luck Street, NY 64332
regexLength:
 ^(\p{Upper}(\p{Lower}+\s?)){2,3}\w+ .*, \w+ \d{5}(-\d{4})?$

C:\RegEx\chapter_1\Examples\chapter1>
java MatchAddress "John A. Smith 888 Luck Stree
t, NY 64332-4453"
NO MATCH
pattern:
 John A. Smith 888 Luck Street, NY 64332-4453
regexLength:
 ^(\p{Upper}(\p{Lower}+\s?)){2,3}\w+ .*, \w+ \d{5}(-\d{4})?$

C:\RegEx\chapter_1\Examples\chapter1>
java MatchAddress "John Allen Smith 888 Luck Street, NY 64332-4453"
MATCH
pattern:
 John Allen Smith 888 Luck Street, NY 64332-4453
regexLength:
 ^(\p{Upper}(\p{Lower}+\s?)){2,3}\w+ .*, \w+ \d{5}(-\d{4})?$

C:\RegEx\chapter_1\Examples\chapter1>
java MatchAddress "888 Luck Street, NY 64332"
NO MATCH
pattern:
 888 Luck Street, NY 64332
regexLength:
 ^(\p{Upper}(\p{Lower}+\s?)){2,3}\w+ .*, \w+ \d{5}(-\d{4})?$
C:\RegEx\chapter_1\Examples\chapter1>
java MatchAddress "P.O. BOX 888 Luck Street, NY 64332-4453"
NO MATCH
pattern:
 P.O. BOX 888 Luck Street, NY 64332-4453
regexLength:
 ^(\p{Upper}(\p{Lower}+\s?)){2,3}\w+ .*, \w+ \d{5}(-\d{4})?$

C:\RegEx\chapter_1\Examples\chapter1>
java MatchAddress "John Allen Smith 888 Luck st., NY"
NO MATCH
pattern:
 John Allen Smith 888 Luck st., NY
regexLength:
^(\p{Upper}(\p{Lower}+\s?)){2,3}\w+ .*, \w+ \d{5}(-\d{4})?$
```

| **![结束示例](img/_1.gif)** |

|  |



### 查找重复单词示例

我在前面的“分组与反向引用”一节中讨论过清单 1-7 中的代码。此处重新引入它的目的是演示正则表达式如何与 Java 代码实际交互。

清单 1-7：MatchDuplicateWords.java

| **![开始示例](img/_1.gif)** |

```
import java.util.regex.*;
import java.io.*;

public class MatchDuplicateWords{
   public static void main(String args[]){
      hasDuplicate(args[0]);
   }

/**
   * 确认给定短语中不包含重复单词。
   * @param phrase 表示短语的字符串。
   * @returns 如果短语中没有重复单词则返回 true。
   */
   public static boolean hasDuplicate(String phrase){
      boolean retval=false;

String duplicatePattern =
      "\\b(\\w+) \\1\\b";
      // 编译模式
      Pattern p = null;
      try{
        p = Pattern.compile(duplicatePattern);
      }
      catch (PatternSyntaxException pex){
         pex.printStackTrace();
         System.exit(0);
      }
      // 统计匹配次数。
      int matches = 0;

//获取匹配器
      Matcher m = p.matcher(phrase);
      String val=null;

//查找所有匹配的字符串
      while (m.find()){
         retval = true;
        val = ":" + m.group() +":";
        System.out.println(val);
        matches++;
      }

//准备一条指示成功或失败的消息
      String msg = "   无匹配: 模式:" + phrase
             + "\r\n             正则表达式: "
             + duplicatePattern;

if (retval){
      msg = " 匹配     : 模式:" + phrase
          + "\r\n         正则表达式: "
          + duplicatePattern;
      }

System.out.println(msg +"\r\n");
      return retval;
   }
} 
```

| **![结束示例](img/_1.gif)** |

|  |

阅读此示例时，请注意它使用了 Pattern 和 Matcher，而不是前面大多数示例中使用的 String.matches(regex) 方法。试着猜测一下为什么采用这种方法。答案请参见本章末尾的“常见问题解答”部分。输出 1-7 显示了运行程序的结果。该模式在表 1-24 中进行了剖析。

表 1-24：模式 *\b(\w+) \1\*b

| **正则表达式** | **描述** |
| --- | --- |
| \b | 单词边界 |
| ( | 后跟一个由以下内容组成的组 |
| \w | 字母数字或下划线字符 |
| + | 重复一次或多次 |
| ) | 关闭组 |
| <空格> | 后跟一个空格 |
| \1 | 后跟之前捕获的完全相同的字符组 |
| \b | 后跟一个单词边界 |
| * **用英语解释：** 查找一个单词边界，后跟一组字母数字字符，再后跟一个空格，然后后跟*之前找到的完全相同的字母数字字符组*，最后后跟一个单词边界。简而言之，就是查找重复的单词。 |

输出 1-7：运行 MatchDuplicateWords.java 的结果

| **![开始示例](img/_1.gif)** |

```
C:\RegEx\Examples\chapter1>java MatchDuplicateWords "pizza pizza"
:pizza pizza:
   匹配   : 模式:pizza pizza
             正则表达式: \b(\w+) \1\b

C:\RegEx\Examples\chapter1>java MatchDuplicateWords "Faster pussycat kill kill"
:kill kill:
   匹配   : 模式:Faster pussycat kill kill
             正则表达式: \b(\w+) \1\b

C:\RegEx\Examples\chapter1>java MatchDuplicateWords "The mayor of of simpleton"
:of of:
   匹配   : 模式:The mayor of of simpleton
             正则表达式: \b(\w+) \1\b

C:\RegEx\Examples\chapter1>java MatchDuplicateWords "Never Never Never Never Never"
:Never Never:
:Never Never:
   匹配   : 模式:Never Never Never Never Never
             正则表达式: \b(\w+) \1\b

C:\RegEx\Examples\chapter1>java MatchDuplicateWords "222 2222"
   无匹配: 模式:222 2222
             正则表达式: \b(\w+) \1\b

C:\RegEx\Examples\chapter1>java MatchDuplicateWords "sara sarah"
   无匹配: 模式:sara sarah
             正则表达式: \b(\w+) \1\b

C:\RegEx\Examples\chapter1>java MatchDuplicateWords "Faster pussycat kill, kill"
   无匹配: 模式:Faster pussycat kill, kill
             正则表达式: \b(\w+) \1\b

C:\RegEx\Examples\chapter1>java MatchDuplicateWords ". ."
   无匹配: 模式:. .
             正则表达式: \b(\w+) \1\b
```

| **![结束示例](img/_1.gif)** |

|  |

## 正则表达式操作

在本节中，你将探索正则表达式稍微更实际一些的用法。在实际应用中，人们使用正则表达式主要出于以下三大类基本目的：

*   *数据验证：* 这是确保候选字符串符合特定格式的过程（例如，确保密码长度至少为八个字符，并且至少包含两个数字）。

*   *搜索和/或替换：* 这是正则表达式的另一个流行用法，而且理由充分。假设你想给所有客户发送一封信，并希望每封信都通过穿插客户姓名来实现个性化。当然，这比听起来要复杂一些，因为不同姓名的长度不同，而且当插入较长的姓名时，你不希望覆盖信中的下一个单词。正则表达式是解决这类问题的完美方案。

*   *分解文本：* 这也可能是一项具有挑战性的活动，特别是当需要根据复杂规则拆分字符串时。幸运的是，使用正则表达式会变得容易得多，正如清单 1-11（紧随其后）所演示的那样。

### 数据验证

数据验证，即确保数据符合规定的格式，是正则表达式最常见的用途之一。这可能特别具有挑战性，因为数据通常形式不精确，并且由不成文的规则定义。

J2SE 1.4 为你提供了几种验证数据的方法。最简单的是使用新方法 boolean String.matches(String regex)。此方法确认传入的模式*完全*匹配它所调用的字符串。

这种精确性可能很棘手，因此充分理解它很重要。例如，假设你需要确认给定的字符串包含单词 *Java*，后跟一个空格，再后跟某个数字。进一步假设你的候选字符串是 I love Java 4。下一节将演示处理此示例的过程。



#### 字符串数据验证示例

这个示例看起来很简单，因此你首先尝试测试模式 ***Java \d***。表 1-25 展示了该模式的分解说明。

表 1-25：模式 Java \d

| **正则表达式** | **描述** |
| --- | --- |
| J | 大写字母 *J* |
| a | 后跟字符 *a* |
| v | 后跟字符 *v* |
| a | 后跟字符 *a* |
| <space> | 后跟一个空格 |
| \d | 后跟一个数字 |

这相当简单，于是你自信地编写了代码，如清单 1-8 所示。

清单 1-8：ValidationTest.java

| **![示例开始](img/_1.gif)** |

```
 import java.util.regex.*;

  public class ValidationTest{
     public static void main(String args[]){
         String candidate = "I love Java 4";
         String pattern ="Java \\d";
         System.out.println(candidate.matches(pattern));
     }
  }
```

| **![示例结束](img/_1.gif)** |

|  |

然后你运行它：

```
java ValidationTest
```

却发现它在输出 1-8 中失败了。

输出 1-8：运行 ValidationTest.java 的结果

| **![示例开始](img/_1.gif)** |

```
C:\RegEx\code>java ValidationTest
Does candidate : I love Java 4
match pattern  : Java \d?

false
```

| **![示例结束](img/_1.gif)** |

|  |

发生了什么？因为你的输入字符串是 I love Java 4，而 Java 4 前面有 I love，所以输入与模式 **Java \d** 并非完全匹配，只是部分匹配。那么现在该怎么办？

你有两个选择。你可以修改模式，允许在要匹配的 Java 4 前后出现字符；或者直接使用 Pattern 和 Matcher 对象。我们来探讨一下每种方案的优缺点。

要使用 `String.matcher(String regex)` 方法，你需要考虑模式 **Java \d** 前后可能出现的所有字符。因此，你使用了模式 **.*\bJava \d(|$)**，表 1-26 对其进行了剖析。

表 1-26：模式 .**\bJava \d(|$*)

| **正则表达式** | **描述** |
| --- | --- |
| . | 任意字符 |
| * | 重复任意次数 |
| \b | 后跟一个单词边界 |
| J | 后跟大写字母 *J* |
| a | 后跟字符 *a* |
| v | 后跟字符 *v* |
| a | 后跟字符 *a* |
| <space> | 后跟一个空格 |
| \d | 后跟一个数字 |
| ( | 后跟一个分组，包含 |
| <space> | 一个空格 |
| &#124; | 或 |
| $ | 行尾字符 |
| ) | 关闭分组 |

#### 使用 Pattern 和 Matcher 对象进行数据验证示例

上一节中编写模式的工作量比预期稍大。我们来看看在清单 1-9 中使用 Pattern 和 Matcher 对象是否更简单。输出结果如输出 1-9 所示。

清单 1-9：ValidationTestWithPatternAndMatcher.java

| **![示例开始](img/_1.gif)** |

```
 import java.util.regex.*;

   public class ValidationTestWithPatternAndMatcher{
      public static void main(String args[]){
   // 编译模式
   Pattern p = null;
   try{
     p = Pattern.compile("Java \\d");
   }
   catch (PatternSyntaxException pex){
      pex.printStackTrace();
      System.exit(0);
   }

   // 定义匹配字符串

   String candidate = "I love Java 4";
   // 获取匹配器
   Matcher m = p.matcher(candidate);

   System.out.println("result=" + m.find());
   }
}
```

| **![示例结束](img/_1.gif)** |

|  |

输出 1-9：运行 ValidationTestWithPatternAndMatcher.java 的结果

| **![示例开始](img/_1.gif)** |

```
C:\RegEx\Examples\chapter1>java
ValidationTestWithPatternAndMatcher
result = true
```

| **![示例结束](img/_1.gif)** |

|  |

清单 1-9 中使用的模式比清单 1-8 中的更简单，它只是原始字符串 ***Java \d***。但 Java 代码需要显式使用 Pattern 和 Matcher 对象，这对程序员的要求稍高一些。这样做是因为你需要显式访问 `Matcher.find` 方法，该方法允许你检查输入字符串，看是否有任何部分与模式匹配。这与 `String.matches(String regex)` 方法形成对比，后者要求完全匹配。

一般来说，有两种类型的验证。第一种类型要求完全匹配。对于这类验证，最简单的方法可能是使用 `String.matches(String regex)`，因为它会拒绝任何不完全匹配的内容。

第二种类型的验证要求字符串在某个位置包含该模式，但不要求完全匹配。例如，你可能要求密码包含非字母数字字符。这类验证最好通过使用 Matcher 和 Pattern 对象来实现。第 5 章 提供了更复杂的验证示例。

### 搜索与替换

新正则表达式包最强大的功能之一是能够搜索并替换字符串和子字符串。你可能还记得，以前这类操作非常繁琐，需要使用分词器或 `String.substring` 方法，并配合大量字符串运算。

值得庆幸的是，那些日子已经过去了。在 J2SE 中，有两种通用的方法可以执行搜索和替换操作。下面的示例利用 String 类新增的两个方法，走了一条更简单的路径。（第 4 章 包含直接使用 Pattern 和 Matcher 类的更复杂示例。）以下示例涉及的两个方法如下：

*   `replaceFirst(String regex, String replacement)`

*   `replaceAll(String regex, String replacement)`

第一个方法 `replaceFirst(String regex, String replacement)` 仅将正则表达式模式第一次出现的位置替换为 replacement 字符串。第二个方法 `replaceAll` 将模式*所有*出现的位置替换为 replacement 字符串。我将在第 2 章 中详细解释这些新方法。



#### 搜索与替换示例

如果你和我一样，你可能思考编程的时间比应该的要多。假设你正在写一篇关于拳击的文章。进一步假设你决定以编程方式而非手动方式更新这篇拳击文章。清单 1-10 展示了实现此操作的代码。该示例从给定段落中搜索并替换一些常见的误用短语。输出 1-10 显示了运行程序的结果。

清单 1-10: StyleSearchAndReplace.java

| **![开始示例](img/_1.gif)** |

```
public class StyleSearchAndReplace{
  public static void main(String args[]){

    String statement = "The question as to whether the jab is"+
    " superior to the cross has been debated for some time in"+
    " boxing circles. However, it is my opinion that this"+
    " false dichotomy misses the point. I call your attention"+
    " to the fact that the best boxers often use a combination of"+
    " the two. I call your attention to the fact that Mohammed"+
    " Ali,the Greatest of the sport of boxing, used both. He had"+
    " a tremendous jab, yet used his cross effectively, often,"+
    " and well";

    String newStmt=
    statement.replaceAll("The question as to whether","Whether");

    newStmt= newStmt.replaceAll(" of the sport of boxing","");
    newStmt=newStmt.replaceAll("amount of success","success");
    newStmt=
     newStmt.replaceAll("However, it is my opinion that this","This");

    newStmt= newStmt.replaceAll("a combination of the two","both");
    newStmt= newStmt.replaceAll("This is in spite of the fact that"
     +" the", "The");
    newStmt=
     newStmt.replaceAll("I call your attention to the fact that","");

    System.out.println("BEFORE:\n"+statement + "\n");
    System.out.println("AFTER:\n"+newStmt);
   }
}
```

| **![结束示例](img/_1.gif)** |

|  |

如输出 1-10 所示，经过此过程，段落的清晰度有所提升。

输出 1-10: 运行 StyleSearchAndReplace.java 的结果

| **![开始示例](img/_1.gif)** |

```
C:\RegEx\Examples\chapter1>java StyleSearchAndReplace
BEFORE:
The question as to whether the jab is superior to the cross has been debated for
some time in boxing circles. However, it is my opinion that this false dichotomy
misses the point. I call your attention to the fact that the best boxers often
use a combination of the two. I call your attention to the fact that Mohammed
Ali,the Greatest of the sport of boxing, used both. He had a tremendous jab, yet
used his cross effectively,often, and well
AFTER:

Whether the jab is superior to the cross has been debated for some time in boxing
circles. This false dichotomy misses the point. the best boxers often use both.
Mohammed Ali,the Greatest, used both. He had a tremendous jab, yet used his cross
effectively,often, and well
```

| **![结束示例](img/_1.gif)** |

|  |

### 分割字符串

有许多机制可用于分割字符串，最明显的是`StringTokenizer`。然而，分割字符串可能异常复杂，因为它可能需要相当复杂的标准。例如，分割一个逗号分隔的文件很容易，但将一个单词分割成元音和辅音呢？后者可能极其复杂。幸运的是，正则表达式在这些情况下特别有用，你将在以下章节中了解到这一点。

#### 分割字符串示例

在英语修辞学中，我们学到强化句子的最佳方法之一是将肯定与否定对立起来。清单 1-11 中的代码接收一个句子，并尝试通过将肯定与否定对立来强化它。输出 1-11 显示了结果。

清单 1-11: StyleSplitExample.java

| **![开始示例](img/_1.gif)** |

```
public class StyleSplitExample{
   public static void main(String args[]){
      String phrase1= "but simple justice, not charity";
      strengthenSentence(phrase1);

      String phrase2=
       "but that I love Rome more, not that I love Caesar less";
      strengthenSentence(phrase2);

      String phrase3=
      "ask what you can do for your country, ask not what your "
      + "country can do for you";
      strengthenSentence(phrase3);
   }
   /**
   * 分割并重新排列给定的字符串，以期达到更有力的效果。
   * @param sentence 是一个字符串，代表我们想要强化的短语。
   * @returns 是一个字符串，代表修改后的短语。
   */
   public static String strengthenSentence(String sentence){
      String retval=null;

      String[] tokens = null;

      String splitPattern = ",";

      tokens= sentence.split(splitPattern);

      if (tokens==null){
         String msg = "   NO MATCH: pattern:" + sentence
             + "\r\n             regex: " + splitPattern;
      }
      else{
         retval = tokens[1] + ", " + tokens[0];
         System.out.println("BEFORE: " + sentence);
         System.out.println("AFTER : " + retval +"\n");
      }
      return retval;
   }
}
```

| **![结束示例](img/_1.gif)** |

|  |

输出 1-11: 运行 StyleSplitExample.java 的结果

| **![开始示例](img/_1.gif)** |

```
C:\RegEx\Examples\chapter1>java StyleSplitExample
BEFORE: but simple justice, not charity
AFTER : not charity, but simple justice

BEFORE: but that I love Rome more, not that I love Caesar less
AFTER : not that I love Caesar less, but that I love Rome more

BEFORE: ask what you can do for your country, ask not what your
country can do for you
AFTER : ask not what your country can do for you, ask what you can
do for your country
```

| **![结束示例](img/_1.gif)** |

|  |



#### 条件字符串分割示例

当你有更完整的字符串解析需求时，正则表达式会变得特别有用。当字符串格式定义良好时，例如逗号分隔的文件，分割字符串是很容易的。你不需要为此使用正则表达式；一个 `StringTokenizer` 就足够了。但是，如果你想根据某个单词或其任何同义词来分割字符串呢？

正则表达式在这类场景中会很有帮助，因为它们允许你限定复杂条件来实现分割。清单 1-12 根据单词 *compromise* 或其同义词的出现位置来分割给定的短语。输出 1-12 显示了运行该程序的结果。

清单 1-12: Split.java

| **![开始示例](img/_1.gif)** |

```
public class Split{
   public static void main(String args[]){

      String statement = "I will not compromise. I will not "+
      "cooperate. There will be no concession, no conciliation, no "+
      "finding the middle group, and no give and take.";

      String tokens[] =null;

      String splitPattern= "compromise|cooperate|concession|"+
      "conciliation|(finding the middle group)|(give and take)";

      tokens=statement.split(splitPattern);

      System.out.println("REGEX PATTERN:\n"+splitPattern + "\n");

      System.out.println("STATEMENT:\n"+statement + "\n");
      System.out.println("\nTOKENS");
      for (int i=0; i < tokens.length; i++){
      System.out.println(tokens[i]);
      }
   }
} 
```

| **![结束示例](img/_1.gif)** |

|  |

输出 1-12: 运行 Split.java 的结果

| **![开始示例](img/_1.gif)** |

```
C:\RegEx\Examples\chapter1>java Split
REGEX PATTERN:
compromise|cooperate|concession|conciliation|(finding the middle group)|(give
and take)

STATEMENT:
I will not compromise. I will not cooperate. There will be no
concession, no conciliation,
 no finding the middle group, and no give and take.

TOKENS:
I will not
. I will not
. There will be no
, no
, no
, and no
.
```

| **![结束示例](img/_1.gif)** |

|  |

这个示例说明了作为标准 Java 实现一部分而存在的新可能性。我将在第 3 章和第 4 章中讨论更复杂的分割。

## 比较正则表达式与 Perl

Perl 可能是提供正则表达式支持的最流行的语言。因此，通过将其与 Perl 进行比较来理解 Java 的正则表达式支持是合理的。你应该注意的区别将在以下各节中重点介绍。总的来说，J2SE 不包含某些 Perl 结构，因为 Java 是一种功能齐全的编程语言，提供了复杂的条件和逻辑执行路径，这些是 Perl 所提供结构的合理替代方案。

### Perl 提供而 Java 正则表达式不支持的功能

有几个你可能从 Perl 经验中熟悉的结构和概念，在当前的 Java 实现中无法使用。因为这些是 Perl 的一部分，而不是 Java 的，我在这里只简要提及。

Java 不支持正则表达式字符串修改。这意味着你不能使用正则表达式修改字符串。在 Java 中，字符串是不可变对象，因此你必须使用返回带有你所需修改的新字符串的方法。此外，你必须手动修改字符串，而不是使用正则表达式模式来这样做。前面的搜索和替换示例展示了这是如何工作的。原始字符串不会被修改——它只是返回一个表示修改后的新字符串。

Perl 的条件结构 ***(?{X})*** 和 ***(?(condition)X|Y)*** 不被 J2SE 的正则表达式支持。因为 Java 作为语言特性提供了强大的 if-then-else 支持，所以不需要条件结构。第 4 章提供了这是如何工作的示例。

Java 不支持嵌入式代码结构 ***(?(code))*** 和 ***(??(code))***。同样，这些是可以通过 Java 标准更直观地处理的事情，通过使用 Java 的内置语言特性。

Java 默认不支持嵌入式注释，因为当你将模式创建为字符串时，可以很容易地对其进行注释。但是，如果你确实需要，可以使用 `Pattern.COMMENT` 标志来编译带有注释的正则表达式。更多信息，请参见第 2 章。

Java 不支持预处理操作 ***\l \u***、***\L*** 和 ***\U***。

### Java 正则表达式提供而 Perl 不支持的功能

占有量词是 Java 独有的，但它们很可能会很快被其他正则表达式实现所采用，因为它们是一个非常好的想法。占有量词会继续保留任何符合条件的贪婪匹配。这意味着一旦实现了占有匹配，它就不会被放弃。我将在第 3 章中深入讨论占有量词。

## 总结

本章涵盖了一些通用的正则表达式语法，并介绍了 Matcher 和 Pattern 类的概念。你学习了一些创建自己正则表达式的方法，以及如何在 Java 中实际使用它们。最后，你探索了一些具体的示例并理解了它们的工作原理。第 2 章将继续围绕这个主题展开，并让你更深入地理解 Java 的正则表达式包。



## 常见问题解答

| **问：** | **我在编写正则表达式时，`\b` 元字符的行为似乎不一致。这是怎么回事？** | ![在正则表达式中，\b 表示单词边界。然而，在通用的 Java 术语中，\b 表示退格键。规则如下：字面量字符串 \b 表示退格字符。但是，字面量字符串 \\b 表示单词边界。](img/#LiB0013.html#answer.N19) |
| **问：** | **何时应该使用 `String.matches` 方法，而不是直接使用 `Pattern` 和 `Matcher` 对象？** | ![如果你需要精确匹配，请使用 String.matches 方法。例如，如果你想要恰好七个连续的数字，并且不接受其他任何内容，那么可以使用模式 \d{7} 的 String.matches 方法。通常，如果你准备缩小可接受模式的定义范围，或者愿意定义所有可能的变体，那么请使用 String.matches 方法。另一方面，如果你是在查找子字符串是否存在，那么使用 Pattern 和 Matcher 对象会更合适。](img/#LiB0013.html#answer.N53) |
| **问：** | **使用 `String.matches` 方法是否比使用 `Pattern` 和 `Matcher` 对象消耗更少的资源？** | ![不。String.matches 方法只是调用了 Pattern.matches 方法，而后者又会创建并使用一个 Pattern 对象和一个 Matcher 对象。](img/#LiB0013.html#answer.N87) |
| **问：** | **我可以通过对字符串应用正则表达式来修改它吗？** | ![绝对不能。String 对象在 Java 中是不可变的，因此它们不能被更改。但是，你可以创建一个包含所需更改的新 String 对象。因此，如果你有一个字符串 String tmp = `Hello`; 并且你想通过执行以下操作将 e 改为 X：String newTmp = tmp.replaceFirst(`e`,`X`); tmp 的值仍然是 Hello，但 newTmp 的值是 HXllo。](img/#LiB0013.html#answer.N113) |
| **问：** | **为什么模式 `(\p{Upper}(\p{Lower}+\s?)){2,3}` 在 NameFormat.java 示例中匹配了 John McGee？** | ![因为 John 匹配了模式的第一部分，Mc 匹配了模式的第二部分，Gee 也匹配了模式的第二部分。作为测试，尝试在 NameFormat.java 程序中运行 John Janis McGee。这里的要点是，John 由一个大写字母、后跟一个或多个小写字母、再后跟一个空格组成。Mc 由一个大写字母、后跟一个或多个小写字母、后跟零个空格组成，Gee 由一个大写字母、后跟一个或多个小写字母、后跟零个空格组成。这可能不完全符合你的预期，但在这种情况下似乎是允许的。在使用正则表达式时，精确并做大量测试非常重要，否则必然会出现意外结果。](img/#LiB0013.html#answer.N173) |
| **问：** | **Java 使用什么类型的正则表达式引擎？** | ![J2SE 使用传统的非确定性有限自动机（NFA）引擎。这意味着当引擎遇到分叉路径时，它会选择一条路径，记住另一条路径的位置以防万一，然后继续前进。这样做的好处是，如果你编写高效的表达式，可以非常非常快地引导引擎找到匹配。缺点是，如果你编写低效的表达式，可能会让正则表达式引擎在最终找到匹配之前进行一场徒劳的搜索。](img/#LiB0013.html#answer.N210) |

答案

| **答：**  | 在正则表达式中，***\b*** 表示单词边界。然而，在通用的 Java 术语中，***\b*** 表示退格键。规则如下：字面量字符串 ***\b*** 表示退格字符。但是，字面量字符串 ***\\b*** 表示单词边界。 |
| **答：**  | 如果你需要精确匹配，请使用 `String.matches` 方法。例如，如果你想要恰好七个连续的数字，并且不接受其他任何内容，那么可以使用模式 ***\d{7}*** 的 `String.matches` 方法。通常，如果你准备缩小可接受模式的定义范围，或者愿意定义所有可能的变体，那么请使用 `String.matches` 方法。另一方面，如果你是在查找子字符串是否存在，那么使用 `Pattern` 和 `Matcher` 对象会更合适。 |
| **答：**  | 不。`String.matches` 方法只是调用了 `Pattern.matches` 方法，而后者又会创建并使用一个 `Pattern` 对象和一个 `Matcher` 对象。 |
| **答：**  | 绝对不能。`String` 对象在 Java 中是不可变的，因此它们不能被更改。但是，你可以创建一个包含所需更改的新 `String` 对象。因此，如果你有一个字符串 
```
String tmp = "Hello";
```

并且你想通过执行以下操作将 e 改为 X：

```
String newTmp = tmp.replaceFirst("e","X");
```

`tmp` 的值仍然是 `Hello`，但 `newTmp` 的值是 `HXllo`。 |
| **答：**  | 因为 *John* 匹配了模式的第一部分，*Mc* 匹配了模式的第二部分，*Gee* 也匹配了模式的第二部分。作为测试，尝试在 `NameFormat.java` 程序中运行 *John Janis McGee*。这里的要点是，*John* 由一个大写字母、后跟一个或多个小写字母、再后跟一个空格组成。*Mc* 由一个大写字母、后跟一个或多个小写字母、后跟零个空格组成，*Gee* 由一个大写字母、后跟一个或多个小写字母、后跟零个空格组成。这可能不完全符合你的预期，但在这种情况下似乎是允许的。在使用正则表达式时，精确并做大量测试非常重要，否则必然会出现意外结果。 |
| **答：**  | J2SE 使用传统的非确定性有限自动机（NFA）引擎。这意味着当引擎遇到分叉路径时，它会选择一条路径，记住另一条路径的位置以防万一，然后继续前进。这样做的好处是，如果你编写高效的表达式，可以非常非常快地引导引擎找到匹配。缺点是，如果你编写低效的表达式，可能会让正则表达式引擎在最终找到匹配之前进行一场徒劳的搜索。 |

