# 11. Unicode、国际化与货币代码

Java 平台提供了丰富的国际化功能，帮助您创建可在全球范围内使用的应用程序。该平台提供了本地化应用程序、以多种符合文化习惯的格式格式化日期和数字，以及显示数十种书写系统中使用的字符的方法。本章仅描述了程序员在开发国际化应用程序时必须执行的一些最常见和常规的任务。

## 11.1 将 Unicode 字符转换为数字

### 问题

您希望将 Unicode 数字字符转换为其对应的整数值。例如，您有一个包含泰语数字 8 的字符串，并且希望生成一个具有该值的整数。



### 解决方案

`java.lang.Character` 这个 final 类提供了几个静态方法，用于将字符转换为整型数字值。

*   `public static int digit(char ch, int radix)`

*   `public static int digit(int ch, int radix)`

以下代码片段遍历了从 0x0000 到 0x10FFFF 的整个 Unicode 码点范围。每个同时也是数字的码点都会显示其字符及其对应的数字值 0 到 9。你可以在 `org.java17recipes.chapter11.recipe11_01.Recipe11_1` 类中找到这个示例。

```
public static void main(String[] args) {
Recipe11_1 example = new Recipe11_1();
example.run();
}
public void run() {
int x = 0;
for (int c=0; c <= 0x10FFFF; c++) {
if (Character.isDigit(c)) {
++x;
System.out.printf("Codepoint: 0x%04X\tCharacter: %c\tDigit: %d\tName: %s\n", c, c,
Character.digit(c, 10), Character.getName(c));
}
}
System.out.printf("Total digits: %d\n", x);
}
}
```

以下是部分输出结果。

```
Codepoint: 0x0030    Character: 0    Digit: 0    Name: DIGIT ZERO
Codepoint: 0x0031    Character: 1    Digit: 1    Name: DIGIT ONE
Codepoint: 0x0032    Character: 2    Digit: 2    Name: DIGIT TWO
Codepoint: 0x0033    Character: 3    Digit: 3    Name: DIGIT THREE
Codepoint: 0x0034    Character: 4    Digit: 4    Name: DIGIT FOUR
Codepoint: 0x0035    Character: 5    Digit: 5    Name: DIGIT FIVE
Codepoint: 0x0036    Character: 6    Digit: 6    Name: DIGIT SIX
Codepoint: 0x0037    Character: 7    Digit: 7    Name: DIGIT SEVEN
Codepoint: 0x0038    Character: 8    Digit: 8    Name: DIGIT EIGHT
Codepoint: 0x0039    Character: 9    Digit: 9    Name: DIGIT NINE
Codepoint: 0x0660    Character: ٠    Digit: 0    Name: ARABIC-INDIC DIGIT ZERO
Codepoint: 0x0661    Character: ١    Digit: 1    Name: ARABIC-INDIC DIGIT ONE
Codepoint: 0x0662    Character: ٢    Digit: 2    Name: ARABIC-INDIC DIGIT TWO
Codepoint: 0x0663    Character: ٣    Digit: 3    Name: ARABIC-INDIC DIGIT THREE
Codepoint: 0x0664    Character: ٤    Digit: 4    Name: ARABIC-INDIC DIGIT FOUR
Codepoint: 0x0665    Character: ٥    Digit: 5    Name: ARABIC-INDIC DIGIT FIVE
Codepoint: 0x0666    Character: ٦    Digit: 6    Name: ARABIC-INDIC DIGIT SIX
Codepoint: 0x0667    Character: ٧    Digit: 7    Name: ARABIC-INDIC DIGIT SEVEN
Codepoint: 0x0668    Character: ٨    Digit: 8    Name: ARABIC-INDIC DIGIT EIGHT
Codepoint: 0x0669    Character: ٩    Digit: 9    Name: ARABIC-INDIC DIGIT NINE
...
Codepoint: 0x0E50    Character: ๐    Digit: 0    Name: THAI DIGIT ZERO
Codepoint: 0x0E51    Character: ๑    Digit: 1    Name: THAI DIGIT ONE
Codepoint: 0x0E52    Character: ๒    Digit: 2    Name: THAI DIGIT TWO
Codepoint: 0x0E53    Character: ๓    Digit: 3    Name: THAI DIGIT THREE
Codepoint: 0x0E54    Character: ๔    Digit: 4    Name: THAI DIGIT FOUR
Codepoint: 0x0E55    Character: ๕    Digit: 5    Name: THAI DIGIT FIVE
Codepoint: 0x0E56    Character: ๖    Digit: 6    Name: THAI DIGIT SIX
Codepoint: 0x0E57    Character: ๗    Digit: 7    Name: THAI DIGIT SEVEN
Codepoint: 0x0E58    Character: ๘    Digit: 8    Name: THAI DIGIT EIGHT
Codepoint: 0x0E59    Character: ๙    Digit: 9    Name: THAI DIGIT NINE
...
```

注意

示例代码将结果打印到控制台。由于字体或平台差异，你的控制台可能无法显示本例中展示的所有字符字形。但是，这些字符会被正确地转换为整数。

### 工作原理

Unicode 字符集包含超过一百万个唯一的码点，其整数值范围从 0x0000 到 0x10FFFF。每个字符值都有一组属性。其中一个属性是 `isDigit`。如果此属性为 `true`，则该字符代表一个从 0 到 9 的数字。例如，码点值为 0x30 到 0x39 的字符，其字形为 0、1、2、3、4、5、6、7、8 和 9。如果你简单地将这些码值转换为其对应的整数值，你会得到十六进制值 0x30 到 0x39。对应的十进制值是 48 到 57。然而，这些字符也代表数字。在计算中使用它们时，这些字符代表值 0 到 9。

当一个字符具有 `digit` 属性时，使用 `Character.digit()` 静态方法将其转换为对应的整型数字值。请注意，`digit()` 方法被重载，可以接受 `char` 或 `int` 参数。此外，该方法需要一个基数（radix）。基数常见的值有 2、10 和 16。有趣的是，尽管字符 a–f 和 A–F 不具有 `digit` 属性，但它们可以在基数为 16 的情况下作为数字使用。对于这些字符，`digit()` 方法会返回预期的整数值 10 到 15。

要完全理解 Unicode 字符集和 Java 的实现，需要熟悉几个新术语：字符（character）、码点（code point）、char、编码（encoding）、序列化编码（serialization encoding）、UTF-8 和 UTF-16。这些术语超出了本配方的范围，但你可以从 Unicode 网站 [`http://unicode.org`](http://unicode.org) 或 `Character` 类的 Java API 文档中了解更多关于这些以及其它 Unicode 概念的知识。

## 11.2 创建和使用 Locale

### 问题

你希望以符合客户语言和文化习惯的用户友好方式显示数字、日期和时间。



### 解决方案

数字、日期和时间的显示格式因地区而异，并取决于用户的语言和文化区域。此外，文本排序规则也因语言而异。`java.util.Locale` 最终类代表世界上特定的语言和区域。通过确定并使用客户的区域设置，你可以将该区域设置应用于各种格式类，这些类能够以预期的形式创建用户可见的数据。使用 `Locale` 实例来修改其行为以适应特定语言或区域的类被称为*区域敏感*类。你可以在第 4 章中了解更多关于区域敏感类的信息。该章向你展示了如何在 `NumberFormat` 和 `DateFormat` 类中使用 `Locale` 实例。然而，在本方案中，你将学习创建这些 `Locale` 实例的不同选项。

你可以通过以下任何一种方式创建 `Locale` 实例。

*   使用 `Locale.Builder` 类来配置和构建一个 `Locale` 对象。
*   使用静态的 `Locale.forLanguageTag()` 方法。
*   使用 `Locale` 构造器来创建对象。
*   使用预配置的静态 `Locale` 对象。

Java 的 `Locale.Builder` 类拥有 setter 方法，用于创建可以转换为格式良好的最佳实践（BCP）47 语言标签的区域设置。“工作原理”部分会更详细地描述 BCP 47 标准。现在，你只需简单理解 `Builder` 创建的是符合该标准的 `Locale` 实例。

以下来自 `org.java17recipes.chapter11.recipe11_02.Recipe11_2` 类的代码片段演示了如何创建 `Builder` 和 `Locale` 实例。你在区域敏感类中创建区域设置，以生成文化上正确的显示格式。

```
private static final long number = 123456789L;
private static final Date now = new Date();
public static void main(String[] args) {
Recipe11_2 app = new Recipe11_2();
app.run();
}
public void run() {
createFromBuilder();
createFromLanguageTag();
createFromConstructor();
createFromStatics();
}
private void createFromBuilder() {
System.out.printf("Creating from Builder...\n\n");
String[][] langRegions = {{"fr", "FR"}, {"ja", "JP"}, {"en", "US"}};
Builder builder = new Builder();
Locale l = null;
NumberFormat nf = null;
DateFormat df = null;
for (String[] lr: langRegions) {
builder.clear();
builder.setLanguage(lr[0]).setRegion(lr[1]);
l = builder.build();
nf = NumberFormat.getInstance(l);
df = DateFormat.getDateTimeInstance(DateFormat.LONG, DateFormat.LONG, l);
System.out.printf("Locale: %s\nNumber: %s\nDate: %s\n\n",
l.getDisplayName(),
nf.format(number),
df.format(now));
}
```

上述代码在标准控制台输出以下内容。

```
Creating from Builder...
Locale: French (France)
Number: 123 456 789
Date: 14 septembre 2016 00:08:06 PDT
Locale: Japanese (Japan)
Number: 123,456,789
Date: 2016/09/14 0:08:06 PDT
Locale: English (United States)
Number: 123,456,789
Date: September 14, 2016 12:08:06 AM PDT
```

另一种创建 `Locale` 实例的方法是使用静态的 `Locale.forLanguageTag()` 方法。此方法允许你使用 BCP 47 语言标签参数。以下代码使用 `forLanguageTag()` 方法从其对应的语言标签创建了三个区域设置。

```
...
System.out.printf("Creating from BCP 47 language tags...\n\n");
String[] bcp47LangTags= {"fr-FR", "ja-JP", "en-US"};
Locale l = null;
NumberFormat nf = null;
DateFormat df = null;
for (String langTag: bcp47LangTags) {
l = Locale.forLanguageTag(langTag);
nf = NumberFormat.getInstance(l);
df = DateFormat.getDateTimeInstance(DateFormat.LONG, DateFormat.LONG, l);
System.out.printf("Locale: %s\nNumber: %s\nDate: %s\n\n",
l.getDisplayName(),
nf.format(number),
df.format(now));
}
...
```

输出结果与从 `Builder` 生成的 `Locale` 实例创建的结果类似。

```
Creating from BCP 47 language tags...
Locale: French (France)
Number: 123 456 789
Date: 14 septembre 2016 01:07:22 PDT
...
```

你也可以使用构造器来创建实例。以下代码展示了如何操作。

```
Locale l = new Locale("fr", "FR");
```

其他构造器允许你传入更少或更多的参数。参数可以包括语言、区域和可选的变体代码。

最后，`Locale` 类为一些常用情况预定义了多个静态实例。由于这些实例是预定义的，你的代码只需引用这些静态实例即可。例如，以下示例展示了如何引用代表 `fr-FR, ja-JP` 和 `en-US` 区域设置的现有静态实例。

```
Locale frenchInFrance = Locale.FRANCE;
Locale japaneseInJapan = Locale.JAPAN;
Locale englishInUS = Locale.US;
```

### 工作原理

`Locale` 类为区域敏感类提供了执行符合文化习惯的数据格式化和解析所需的上下文。一些区域敏感类包括以下内容。

*   `java.text.NumberFormat`
*   `java.text.DateFormat`
*   `java.util.Calendar`

一个 `Locale` 实例标识一种特定的语言，并且可以精细调整以识别以特定文字书写或在特定世界区域使用的语言。它是创建任何依赖于语言或区域影响的内容的重要且必要的元素。

Java 的 `Locale` 类一直在增强以支持现代的 BCP 47 语言标签。BCP 47 定义了使用 ISO 标准进行语言、区域、文字和变体标识符的最佳实践。尽管现有的 `Locale` 构造器继续与 Java 平台的早期版本兼容，但这些构造器不支持额外的文字标签。例如，只有最近添加的 `Locale.Builder` 类和 `Locale.forLanguageTag()` 方法支持识别文字的新功能。由于 `Locale` 构造器不强制严格遵守 BCP 47，你应该在任何新代码中避免使用构造器。相反，开发者应该使用 `Builder` 类和 `forLanguageTag()` 方法。

一个 `Locale.Builder` 实例拥有多种 setter 方法，帮助你配置它以创建一个有效的、符合 BCP 47 标准的 `Locale` 实例。

*   `public Locale.BuildersetLanguage(String language)`
*   `public Locale.BuildersetRegion(String region)`
*   `public Locale.BuildersetScript(String script)`

如果这些方法的参数不是 BCP 47 标准中格式良好的元素，每个方法都会抛出 `java.util.IllFormedLocaleException`。语言参数必须是有效的两个或三个字母的 ISO 639 语言标识符。区域参数必须是有效的两个字母的 ISO 3166 区域代码或三位数的联合国 M.49 “地区”代码。最后，文字参数必须是有效的四个字母的 ISO 15924 文字代码。

`Builder` 允许你配置它以创建一个特定的、符合 BCP 47 标准的区域设置。一旦你设置了所有配置，`build()` 方法会创建并返回一个 `Locale` 实例。请注意，所有的 setter 方法都可以链式调用，形成单个语句。`Builder` 模式的工作原理是让每个配置方法返回对当前实例的引用，然后可以在此实例上调用进一步的配置方法。

```
Locale aLocale = new Builder().setLanguage("fr").setRegion("FR").build();
```

BCP 47 文档及其包含的标准可以在以下位置找到。

*   ISO 3166（区域标识符）：[`www.iso.org/iso-3166-country-codes.html`](http://www.iso.org/iso-3166-country-codes.html)
*   ISO 15924（文字标识符）：[`https://unicode.org/iso15924/`](https://unicode.org/iso15924/)
*   联合国 M.49（地区标识符）：[`https://unstats.un.org/unsd/methods/m49/m49.htm`](https://unstats.un.org/unsd/methods/m49/m49.htm)

## 11.3 匹配和过滤区域设置

### 问题

你想要匹配或过滤一个区域设置列表，并只返回那些满足指定条件的区域设置。



### 解决方案

利用 Java 8 中在 `java.util.Locale` 类中新增的区域设置匹配和过滤方法。如果你有一个以逗号分隔的区域设置字符串列表，你可以对该字符串应用过滤器或“优先级列表”，以仅返回字符串中满足过滤条件的区域设置。在以下示例中，使用 `java.util.Locale filterTag` 方法过滤了一个语言标签列表，并以字符串格式返回匹配的标签。

```
public static void main(String[] args) {
filterTags();
localeMatching();
}
public static void filterTags(){
List list1 = Locale.LanguageRange.parse("ja-JP, en-US");
list1.stream().forEach((range) -> {
System.out.println("Range:" + range.getRange());
});
ArrayList localeList = new ArrayList();
localeList.add("en-US");
localeList.add("en-JP");
List tags1 = Locale.filterTags(list1, localeList);
System.out.println("The following is the filtered list of locales:");
tags1.stream().forEach((tag) -> {
System.out.println(tag);
});
}
```

以下是运行结果。

```
Range:ja-jp
Range:en-us
The following is the filtered list of Locales:
en-us
```

`Locale` 类的 `filter()` 方法允许你返回一个匹配的 `Locale` 实例列表。在以下示例中，一个区域设置语言标签列表从一个区域设置列表中过滤出了 `Locale` 类。

```
public static void localeMatching(){
String localeTags = Locale.ENGLISH.toLanguageTag() + "," +
Locale.CANADA.toLanguageTag();
List list1 = Locale.LanguageRange.parse(localeTags);
list1.stream().forEach((range) -> {
System.out.println("Range:" + range.getRange());
});
ArrayList localeList = new ArrayList();
localeList.add(new Locale("en"));
localeList.add(new Locale("en-JP"));
List tags1 = Locale.filter(list1, localeList);
System.out.println("The following is the matching list of Locales:");
tags1.stream().forEach((tag) -> {
System.out.println(tag);
});
}
```

以下是运行结果。

```
Range:en
Range:en-ca
The following is the matching list of locales:
en
```

### 工作原理

在 Java 8 中，`java.util.Locale` 类新增了一些方法，用于根据提供的 `List<Locale.LanguageRange>` 格式的优先级列表来过滤 `Locale` 实例或语言标签。以下列表简要总结了这些过滤方法。

*   `filter(List<Locale.LanguageRange>, Collection<Locale>)`

```
filter(List, Collection, Locale.FilteringMode)
```

（返回匹配的 `Locale` 实例列表）

*   `filterTags(List<Locale.LanguageRange>, Collection<String>)`

```
filterTags(List, Collection, Locale.FilteringMode)
```

（返回匹配的语言标签列表）

要使用每个方法，需要将排序后的优先级顺序作为第一个参数传入。这个优先级顺序是一个 `Locale.LanguageRange` 对象列表，应根据优先级或权重按降序排序。`filter()` 方法中的第二个参数是一个区域设置集合。该集合包含将要被过滤的区域设置。可选的第三个参数包含一个 `Locale.FilteringMode`。

## 11.4 使用正则表达式搜索 Unicode

### 问题

你想在字符串中查找或匹配 Unicode 字符。你想使用正则表达式语法来实现这一点。

### 解决方案 1

查找或匹配字符最简单的方法是使用 `String` 类。`String` 实例存储 Unicode 字符序列，并提供相对简单的操作，用于使用正则表达式查找、替换和标记化字符。

要确定一个字符串是否匹配正则表达式，请使用 `matches()` 方法。如果整个字符串与正则表达式完全匹配，`matches()` 方法将返回 `true`。

以下来自 `org.java17recipes.chapter11.recipe11_04.Recipe11_4` 类的代码对两个字符串使用了两个不同的表达式。这些正则表达式匹配只是确认字符串是否匹配 `enRegEx` 和 `jaRegEx` 变量中定义的特定模式。

```
private String enText = "The fat cat sat on the mat with a brown rat.";
private String jaText = "Fight 文字化け!";
String enRegEx = "^The \\w+ cat.*";
String jaRegEx = ".*文字.*";
String jaRegExEscaped = ".*\u6587\u5B57.*";
public static void main(String[] args) {
Recipe11_4 app = new Recipe11_4();
app.run();
}
public void run() {
demoStringMatch();
demoStringReplace();
demoStringSplit();
demoSimple();
demoComplex();
}
public void demoStringMatch() {
boolean found = false;
found = enText.matches(enRegEx);
if (found) {
System.out.printf("Matches %s.\n", enRegEx);
}
found = jaText.matches(jaRegEx);
if (found) {
System.out.printf("Matches %s.\n", jaRegEx);
}
found = jaText.matches(jaRegExEscaped);
if (found) {
System.out.printf("Matches %s.\n", jaRegExEscaped);
}
}
```

这段代码将输出以下内容。

```
Matches ^The \w+ cat.*.
Matches .*文字.*.
Matches .*文字.*.
```

使用 `replaceFirst()` 方法创建一个新的 `String` 实例，其中目标文本中第一次出现的正则表达式被替换为替换文本。以下代码演示了如何使用此方法。

```
String replaced = jaText.replaceFirst("文字化け", "mojibake");
System.out.printf("Replaced: %s\n", replaced);
```

输出中显示了替换后的文本。

```
Replaced: Fight mojibake!
```

`replaceAll()` 方法会将所有出现的表达式替换为替换文本。

最后，`split()` 方法创建一个 `String[]`，其中包含由匹配表达式分隔的文本。换句话说，它返回由该表达式分隔的文本。你可以选择提供一个 `limit` 参数，该参数限制分隔符在源文本中应用的次数。以下代码演示了 `split()` 方法在空格字符处进行分割。

```
String[] matches = enText.split("\\s", 3);
for(String match: matches) {
System.out.printf("Split: %s\n",match);
}
```

代码的输出如下。

```
Split: The
Split: fat
Split: cat sat on the mat with a brown rat.
```

### 解决方案 2

当简单的 `String` 方法不够用时，你可以使用更强大的 `java.util.regex` 包来处理正则表达式。使用 `Pattern` 类创建正则表达式。`Matcher` 使用该模式对 `String` 实例进行操作。所有 `Matcher` 操作都使用 `Pattern` 和 `String` 实例来执行其功能。

以下代码演示了如何在两个独立的字符串中搜索 ASCII 和非 ASCII 文本。完整源代码请参见 `org.java17recipes.chapter11.recipe11_04.Recipe11_4` 类。`demoSimple()` 方法查找任何字符后跟 `".at"` 的文本。`demoComplex()` 方法在一个字符串中查找两个日语符号。

```
public void demoSimple() {
Pattern p = Pattern.compile(".at");
Matcher m = p.matcher(enText);
while(m.find()) {
System.out.printf("%s\n", m.group());
}
}
public void demoComplex() {
Pattern p = Pattern.compile("文字");
Matcher m = p.matcher(jaText);
if (m.find()) {
System.out.println(m.group());
}
}
```

对之前定义的英文和日文文本运行这两个方法，将显示以下结果。

```
fat
cat
sat
mat
rat
文字
```



### 工作原理

以下是与正则表达式配合使用的 `String` 方法。

*   `public boolean matches(String regex)`

*   `public String replaceFirst(String regex, String replacement)`

*   `public String replaceAll(String regex, String replacement)`

*   `public String[] split(String regex, int limit)`

*   `public String[] split(String regex)`

这些 `String` 方法是对 `java.util.regex` 类更强大功能的有限且相对简单的封装。

*   `java.util.regex.Pattern`

*   `java.util.regex.Matcher`

*   `java.util.regex.PatternSyntaxException`

Java 正则表达式与 Perl 语言中使用的正则表达式类似。虽然关于 Java 正则表达式有很多需要学习的内容，但本教程中需要理解的最重要几点如下。

*   你的正则表达式可以包含来自完整 Unicode 字符范围的非 ASCII 字符。

*   由于 Java 语言编译器理解反斜杠字符的特殊性，在代码中使用预定义字符类表达式时，必须使用两个反斜杠而不是一个。

在正则表达式中使用非 ASCII 字符最方便且可读性最强的方法是使用键盘输入法直接将其键入源文件。不同的操作系统和编辑器在允许你输入 ASCII 之外的复杂文本方面有所不同。无论使用何种操作系统，如果编辑器允许，你都应将文件保存为 UTF-8 编码。作为使用非 ASCII 正则表达式的另一种但更困难的方法，你可以使用 `\uXXXX` 表示法对字符进行编码。使用这种表示法，你无需直接使用键盘键入字符，而是输入 **\u** 或 **\U**，后跟 Unicode 码点的十六进制表示。本教程的代码示例使用了日语单词“文字”（发音为 *mo-ji*）。如示例所示，你可以在正则表达式中使用实际字符，或者查找 Unicode 码点值。对于这个特定的日语单词，其编码为 `\u6587\u5B57`。

Java 语言的正则表达式支持包括特殊字符类。例如，`\d` 和 `\w` 分别是正则表达式 `[0-9]` 和 `[a-zA-Z_0-9]` 的快捷表示法。然而，由于 Java 编译器对反斜杠字符的特殊处理，在使用诸如 `\d`（数字）、`\w`（单词字符）和 `\s`（空格字符）等预定义字符类时，你必须使用额外的反斜杠。例如，要在源代码中使用它们，你需要分别输入 **\\d**、**\\w** 和 **\\s**。示例代码在解决方案 1 中使用了双反斜杠来表示 `\w` 字符类。

```
String enRegEx = "^The \\w+ cat.*";
```

## 11.5 覆盖默认货币

### 问题

你想使用与默认区域设置无关的货币来显示数值。

### 解决方案

通过显式设置 `NumberFormat` 实例中使用的货币，来控制格式化货币值中打印哪种货币。以下示例假设默认区域设置为 `Locale.JAPAN`。它通过调用其 `NumberFormat` 实例的 `setCurrency(Currency c)` 方法来更改货币。此示例来自 `org.java17recipes.chapter11.recipe11_05.Recipe11_5` 类。

```
public static void main(String[] args) {
Recipe11_5 app  = new Recipe11_5();
app.run();
}
public void run() {
BigDecimal value = new BigDecimal(12345);
System.out.printf("默认区域设置: %s\n", Locale.getDefault().getDisplayName());
NumberFormat nf = NumberFormat.getCurrencyInstance();
String formattedCurrency = nf.format(value);
System.out.printf("%s\n", formattedCurrency);
Currency c = Currency.getInstance(Locale.US);
nf.setCurrency(c);
formattedCurrency = nf.format(value);
System.out.printf("%s\n\n", formattedCurrency);
}
```

上述代码将打印出以下内容。

```
默认区域设置: 日本語 (日本)
¥12,345
USD12,345
```

### 工作原理

你使用 `NumberFormat` 实例来格式化货币值。你应该显式调用 `getCurrencyInstance()` 方法来为货币创建格式化器。

```
NumberFormat nf = NumberFormat.getCurrencyInstance();
```

上述格式化器使用默认区域设置的首选项将数字格式化为货币值。此外，它还使用与该区域设置区域关联的货币符号。然而，一个常见的用例涉及为不同区域的货币格式化值。

使用 `setCurrency()` 方法在数字格式化器中显式设置货币。

```
nf.setCurrency(aCurrencyInstance); // 需要一个 Currency 实例
```

请注意，`java.util.Currency` 最终类是一个工厂类。它允许你通过两种方式创建货币对象。

*   `Currency.getInstance(Locale locale)`

*   `Currency.getInstance(String currencyCode)`

第一个 `getInstance` 调用使用 `Locale` 实例来检索货币对象。Java 平台将默认货币与该区域设置的区域关联起来。在这种情况下，当前与美国关联的默认货币是美元。

```
Currency c1 = Currency.getInstance(Locale.US);
```

第二个 `getInstance` 调用使用有效的 ISO 4217 货币代码。美元的货币代码是 USD。

```
Currency c2 = Currency.getInstance("USD");
```

一旦你有了货币实例，只需在格式化器中使用该实例即可。

```
nf.setCurrency(c2);
```

此格式化器被配置为使用默认区域设置的数字格式符号和模式来格式化数值，但它会将目标货币代码显示为可显示文本的一部分。这允许你将默认数字格式模式与其他货币代码混合使用。

## 11.6 字节数组与字符串之间的转换

### 问题

你需要将字节数组中的字符从旧字符集编码转换为 Unicode 字符串。

### 解决方案

使用 `String` 类将旧字符编码从字节数组转换为 Unicode 字符串。以下来自 `org.java17recipes.chapter11.recipe11_06.Recipe11_6` 类的代码片段演示了如何将旧的 Shift-JIS 编码的字节数组转换为字符串。稍后在同一示例中，代码演示了如何将 Unicode 转换回 Shift-JIS 字节数组。

```
public static void main(String[] args) {
Recipe11_6 app = new Recipe11_6();
app.run();
}
public void run() {
byte[] legacySJIS = {(byte)0x82,(byte)0xB1,(byte)0x82,(byte)0xF1,
(byte)0x82,(byte)0xC9,(byte)0x82,(byte)0xBF,
(byte)0x82,(byte)0xCD,(byte)0x81,(byte)0x41,
(byte)0x90,(byte)0xA2,(byte)0x8A,(byte)0x45,
(byte)0x81,(byte)0x49};
// 将 byte[] 转换为 String
Charset cs =Charset.forName("SJIS");
String greeting = new String(legacySJIS, cs);
System.out.printf("问候语: %s\n", greeting);
```

此代码打印出转换后的文本，即日语的“Hello, world!”。

```
问候语: こんにちは、世界!
```

使用 `getBytes()` 方法将字符串中的字符转换为字节数组。基于前面的代码，使用以下代码转换回原始编码并比较结果。

```
// 将 String 转换为 byte[]
byte[] toSJIS = greeting.getBytes(cs);
// 确认原始数组和新转换的数组相同
Boolean same = false;
if (legacySJIS.length == toSJIS.length) {
for (int x=0; x< legacySJIS.length; x++) {
if(legacySJIS[x] != toSJIS[x]) break;
}
same = true;
}
System.out.printf("相同: %s\n", same.toString());
```

正如预期，输出表明往返转换回旧编码是成功的。原始字节数组和转换后的字节数组包含相同的字节。

```
相同: true
```



### 工作原理

Java 平台为许多传统字符集编码提供了转换支持。当你从字节数组创建 `String` 实例时，必须向 `String` 构造函数提供 `charset` 参数，以便平台知道如何执行从传统编码到 Unicode 的映射。所有 Java 字符串都使用 Unicode 作为其原生编码。

原始数组中的字节数通常不等于结果字符串中的字符数。在本示例中，原始数组包含 18 个字节。Shift-JIS 编码需要这 18 个字节来表示日文文本。然而，转换后，结果字符串包含九个字符。字节和字符之间不存在 1:1 的关系。在此示例中，每个字符在原始 Shift-JIS 编码中需要两个字节。

有数百种不同的 `charset` 编码。编码的数量取决于你的 Java 平台实现。但是，你可以保证获得几种最常见编码的支持，并且你的平台很可能包含比这个最小集合多得多的编码。

*   US-ASCII

*   ISO-8859-1

*   UTF-8

*   UTF-16BE

*   UTF-16LE

*   UTF-16

在构造 `charset` 时，你应该准备好处理当字符集不受支持时可能出现的异常。

*   `java.nio.charset.IllegalCharsetNameException`，当 `charset` 名称非法时抛出
*   `java.lang.IllegalArgumentException`，当 `charset` 名称为 `null` 时抛出
*   `java.nio.charset.UnsupportedCharsetException`，当你的 JVM 不支持目标 `charset` 时抛出

## 11.7 转换字符流和缓冲区

### 问题

你需要将大块的 Unicode 字符文本与任意面向字节的编码进行相互转换。大块文本可能来自流或文件。

### 解决方案 1

使用 `java.io.InputStreamReader` 将字节流解码为 Unicode 字符。使用 `java.io.OutputStreamWriter` 将 Unicode 字符编码为字节流。

以下代码使用 `InputStreamReader` 从类路径中的文件读取并转换可能很大的文本字节块。`org.java17recipes.chapter11.recipe11_07.StreamConversion` 类提供了此示例的完整代码。

```
public static void main(String[] args) {
StreamConversion app = new StreamConversion();
app.run();
}
public void run() {
try {
String input = readStream();
System.out.printf("Input stream: %s\n", input);
writeStream(input);
} catch (IOException ex) {
Logger.getLogger(StreamConversion.class.getName()).log(Level.SEVERE, null, ex);
}
}
public String readStream() throws IOException {
InputStream is = getClass().getResourceAsStream("/resources/helloworld.sjis.txt");
StringBuilder sb = new StringBuilder();
if (is != null) {
try (InputStreamReader reader =
new InputStreamReader(is, Charset.forName("SJIS"))) {
int ch = reader.read();
while (ch != -1) {
sb.append((char) ch);
ch = reader.read();
}
}
}
return sb.toString();
}
```

类似地，你可以使用 `OutputStreamWriter` 将文本写入字节流。以下代码将一个字符串写入 UTF-8 编码的字节流。

```
public void writeStream(String text) throws IOException {
FileOutputStream fos = new FileOutputStream("helloworld.utf8.txt");
try (OutputStreamWriter writer
= new OutputStreamWriter(fos, Charset.forName("UTF-8"))) {
writer.write(text);
}
}
```

### 解决方案 2

使用 `java.nio.charset.CharsetEncoder` 和 `java.nio.charset.CharsetDecoder` 将 Unicode 字符缓冲区与字节缓冲区进行相互转换。通过 `newEncoder()` 或 `newDecoder()` 方法从 `charset` 实例获取编码器或解码器。然后使用编码器的 `encode()` 方法创建字节缓冲区。使用解码器的 `decode()` 方法创建字符缓冲区。以下来自 `org.java17recipes.chapter11.recipe11_07.BufferConversion` 类的代码对缓冲区中的字符集进行编码和解码。

```
public static void main(String[] args) {
BufferConversion app = new BufferConversion();
app.run();
}
public void run() {
try {
System.out.printf("Original string: %s\n", unicodeString);
CharBuffer srcBuffer = CharBuffer.wrap(unicodeString);
ByteBuffer targetBytes = encodeBuffer("UTF8", srcBuffer);
printBytes(targetBytes);
CharBuffer roundtripBuffer = decodeBuffer("UTF8", targetBytes);
printCharBuffer(roundtripBuffer);
} catch (CharacterCodingException ex) {
Logger.getLogger(BufferConversion.class.getName()).log(Level.SEVERE, null, ex);
}
}
public ByteBuffer encodeBuffer(String charsetName, CharBuffer charBuffer)
throws CharacterCodingException {
Charset charset = Charset.forName(charsetName);
CharsetEncoder encoder = charset.newEncoder();
ByteBuffer targetBuffer = encoder.encode(charBuffer);
return targetBuffer;
}
public CharBuffer decodeBuffer(String charsetName, ByteBuffer srcBuffer)
throws CharacterCodingException {
Charset charset = Charset.forName(charsetName);
CharsetDecoder decoder = charset.newDecoder();
CharBuffer charBuffer = decoder.decode(srcBuffer);
return charBuffer;
}
```

### 工作原理

`java.io` 和 `java.nio.charset` 包包含几个类，可以帮助你对大型文本流或缓冲区执行编码转换。流是方便的抽象，可以帮助你使用各种源和目标转换文本。流可以表示 HTTP 连接甚至文件中的传入或传出文本。

如果你使用 `InputStream` 来表示底层源文本，则将该流包装在 `InputStreamReader` 中以执行从字节流的转换。读取器实例执行从字节到 Unicode 字符的转换。

使用 `OutputStream` 实例表示目标文本，将流包装在 `OutputStreamWriter` 中。写入器将你的 Unicode 文本转换为目标流中的面向字节的编码。

要有效使用 `OutputStreamWriter` 或 `InputStreamReader`，你必须知道目标或源文本的字符编码。当你使用 `OutputStreamWriter` 时，源文本始终是 Unicode，并且你必须提供一个 `charset` 参数来告诉写入器如何转换为目标面向字节的文本编码。当你使用 `InputStreamReader` 时，目标编码始终是 Unicode。你必须提供源文本编码作为参数，以便读取器理解如何转换文本。

注意

Java 平台的 `String` 以 Unicode 的 UTF-16 编码表示字符。Unicode 可以有多种编码，包括 UTF-16、UTF-8，甚至 UTF-32。在本讨论中，转换为 Unicode 始终意味着转换为 UTF-16。转换为面向字节的编码通常意味着传统的非 Unicode `charset` 编码。然而，一种常见的面向字节的编码是 UTF-8，使用 `InputStreamReader` 或 `OutputStreamWriter` 类将 Java 的“原生”UTF-16 Unicode 字符与 UTF-8 进行相互转换是完全合理的。

执行编码转换的另一种方法是使用 `CharsetEncoder` 和 `CharsetDecoder` 类。`CharsetEncoder` 将你的 Unicode `CharBuffer` 实例编码为 `ByteBuffer` 实例。`CharsetDecoder` 将 `ByteBuffer` 实例解码为 `CharBuffer` 实例。无论哪种情况，你都必须提供一个 `charset` 参数。

## 11.8 总结

国际化是开发具有文化适应性的应用程序的关键。它允许更改应用程序文本，以符合使用该应用程序的文化和语言。本章提供了一些示例，说明如何使用国际化技术来克服跨文化开发的细微差别。



