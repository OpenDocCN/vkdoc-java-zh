# 7. C# 中的日历、文化与多线程

本章致力于介绍目前最通用的语言之一：强大的 C#。到目前为止，希望你已熟悉该语言的基本语法以及集成开发环境的设置。接下来，我们将探讨与这门强健语言相关的一些更高级的主题，包括日历工作和多线程的基础知识。



## C# 中的日期

与 Java 和 Python 类似，C# 提供了处理日期和日历所需的一切功能。让我们来看一个访问日期和日历的程序吧？（参见清单 7-1。）

```
using System;
public class Example
{
public static void Main()
{  // 创建一个新的 DateTime 对象 "happydate"
DateTime happydate = new DateTime(1966, 2, 6, 5, 20, 0);
// 显示对象数据
Console.WriteLine("我们的特殊日期是 {0}", happydate.ToString());
// 获取并显示日期对象的年份和星期几
Console.WriteLine("回到 {0} 年，那天是 {1}！", happydate.Year, happydate.DayOfWeek);
// 创建第二个 DateTime 对象
DateTime happydate2 = DateTime.Now;
Console.WriteLine("至于现在，{0} 年的这一天是 {1}！", happydate2.Year, happydate2.DayOfWeek);
}
}
清单 7-1
一个 C# 清单，演示了 DateTime 对象的使用（涉及的日期是歌手瑞克·阿斯特利的出生日期）
```

在清单 7-1 中，我们首先创建了一个名为 *happydate* 的对象。它是 C# DateTime 结构的一个实例，并使用特定的构造函数来设置年、月和具体时间。然后，我们使用 *Console.WriteLine* 显示这个日期，其中包含一个 *{0}* 格式标记，用于引入第一个（也是本例中唯一的）参数。happydate 对象还通过 ToString 方法转换为当前活动的*区域性特定格式约定*。

DateTime 结构是 C# 中 System 命名空间的一部分，因此我们无需以其他方式将它们包含在项目中。

C# 中的 DateTime 结构包含大量属性，用于访问从年份到毫秒的日历相关数据。请参见表 7-1 了解概览。

表 7-1

C# 中 DateTime 结构包含的一些核心属性

| Now | Day | Millisecond |
| Date | Hour | TimeOfDay |
| Year | Minute | DayOfWeek |
| Month | Second | DayOfYear |

## System.Globalization 命名空间

C# 非常重视全球区域性的概念。*全球化*指的是为全球任何地区的用户设计应用程序。*本地化*指的是根据特定区域性的要求定制应用程序的过程。在实践中，这些概念涉及我们星球上不同的日历、货币、语言和地点。在 C# 中，我们有大量方法和属性用于呈现本地化信息。其中许多源自 *System.Globalization* 命名空间。

## 使用世界日历

C# 中的 Calendar 类总共包含 14 种不同的日历，以满足您的本地化需求。请参见表 7-2 了解其中八种。

表 7-2

C# 中包含的一些日历

| *GregorianCalendar* | 世界上使用最广泛的日历 | *JulianCalendar* | 俄罗斯东正教使用 |
| *HebrewCalendar* | 以色列的官方日历。也称为犹太历 | *PersianCalendar* | 伊朗和阿富汗的官方日历 |
| *HijriCalendar* | 也称为伊斯兰历 | *ThaiBuddhistCalendar* | 在泰国使用。比公历早 543 年 |
| *JapaneseCalendar* | 与公历类似，增加了基于日本现任天皇在位年份的年份名称 | *UmAlQuraCalendar* | 在沙特阿拉伯使用，类似于伊斯兰历 |

在清单 7-2 中，您将找到一个程序，该程序获取一个公历日期（即 2021 年 12 月 31 日），并将其转换为其他四种日历。我们将使用 GetYear() 和 GetMonth() 等方法访问相关的日期数据。我们还使用 ToString 方法为我们的公历 DateTime 对象指定了自定义格式模式。

```
using System;
using System.Globalization;
public class CalendarsExample
{
public static void Main()
{
// 创建一个新的公历 DateTime 对象 date1
DateTime date1 = new DateTime(2021, 12, 31, new GregorianCalendar());
// 为其他四种日历创建四个对象
JapaneseCalendar japanese = new JapaneseCalendar();
PersianCalendar persian = new PersianCalendar();
HijriCalendar hijri = new HijriCalendar();
ThaiBuddhistCalendar thai = new ThaiBuddhistCalendar();
Console.WriteLine("当公历显示为 {0} 时..", date1.ToString("dd.MM.yyyy"));
Console.WriteLine("> 日本历显示为 {0} 年。", japanese.GetYear(date1));
Console.WriteLine("> 波斯历显示为 {0} 年，是一年中的第 {1} 个月。", persian.GetYear(date1), persian.GetMonth(date1));
Console.WriteLine("> 伊斯兰历显示为 {0} 年，这一天是 {1}。", hijri.GetYear(date1), hijri.GetDayOfWeek(date1));
Console.WriteLine("> 泰历显示为 {0} 年，是一年中的第 {1} 个月。", thai.GetYear(date1), thai.GetMonth(date1));
}
}
清单 7-2
一个演示在 C# 中使用五种不同日历显示信息的清单
```



## CultureInfo 类

CultureInfo 类为我们提供了在 C# 中实现程序本地化的方法。这包括显示针对特定区域设置的时间、日历和货币信息。为了能够访问 CultureInfo 类，我们需要在程序中再次使用 *System.Globalization* 命名空间。

C# 使用*语言-区域代码*进行本地化。在接下来的代码清单中，我们使用了四个代码，即 *en-US*、*fi-FI*、*se-SE* 和 *es-ES*。该代码的前两个字母代表一种语言，而后面大写的一对字母则指代特定区域。例如，德语 (*de*) 可以用额外的区域代码来表示，例如 *de-AT*（奥地利）、*de-CH*（瑞士）和 *de-LI*（列支敦士登）。现在，请参阅代码清单 7-3 进行演示。

C# 中的语言-区域代码基于 *ISO 3166-1 国家列表*。截至 2021 年，总共支持 249 个语言-区域代码。

```
using System;
using System.Globalization;
public class CultureInfoExample
{
public static void Main()
{
// 创建一个包含四个 CultureInfo 对象的数组，名为 "jollycultures"
CultureInfo[] jollycultures = new CultureInfo[] {
new CultureInfo("en-US"), // 美国
new CultureInfo("fi-FI"), // 芬兰
new CultureInfo("se-SE"), // 瑞典北部的萨米人
new CultureInfo("es-ES")}; // 西班牙
// 创建一个新的 DateTime 对象 "date1"，并将日历信息分配给它
DateTime date1 = new DateTime(1952, 12, 1, 15, 30, 00);
// 显示未格式化的日期
Console.WriteLine("特殊日期在您当前的区域设置中是 " + date1 + "。\n");
// 使用 foreach 关键字遍历所有 jollycultures 对象
// 并显示它们的内容，使用 "i" 作为临时迭代变量
foreach (CultureInfo i in jollycultures)
{
Console.WriteLine("此日期在 {0} 中是.. {1} ({2})", i.EnglishName, date1.ToString(i), i.Name);
}
}
}
代码清单 7-3
演示如何使用 CultureInfo 类在 C# 中实现本地化的代码清单
```

在代码清单 7-3 中，我们创建了 CultureInfo 类的四个实例，以演示美国、芬兰、萨米（瑞典北部）和西班牙的区域设置与 C# 的关系。

我们选择在显示 CultureInfo 对象时使用 *foreach* 元素。C# 中的 *foreach* 是 for 循环的一种替代方案，并且在遍历数组时往往更适用。例如，这个元素与 CultureInfo 的实例配合得非常好。

现在来看一个货币格式的小例子（参见代码清单 7-4）。

```
using System.Globalization;
public class JollyCurrencies
{
public static void Main()
{
int cash = 10000;
// 显示不带货币格式的现金金额
Console.WriteLine("无货币格式: " + cash.ToString());
// 将 CurrentCulture 设置为芬兰（芬兰）
Thread.CurrentThread.CurrentCulture = new CultureInfo("fi-FI");
// 使用 "c" 添加适当的货币格式
Console.WriteLine("芬兰货币格式: " + cash.ToString("c"));
// 将 CurrentCulture 设置为白俄罗斯（白俄罗斯）并显示字符串
Thread.CurrentThread.CurrentCulture = new CultureInfo("be-BY");
Console.WriteLine("白俄罗斯货币格式: " + cash.ToString("c"));
// 将 CurrentCulture 设置为中国（中华人民共和国）
Thread.CurrentThread.CurrentCulture = new CultureInfo("zh-CN");
Console.WriteLine("中国货币格式: " + cash.ToString("c"));
}
}
代码清单 7-4
演示 C# CultureInfo 类中货币格式的代码清单
```

在代码清单 7-4 中，我们分别创建并显示了芬兰语、白俄罗斯语和中文的三种不同货币格式。方法 *ToString(“c”)* 用于指定货币格式。

## C# 中的 File 类

C# 通过一个名为 *File* 的类，为所有类型的文件操作提供了多功能工具。我们可以读取和显示文本文件、创建新文件、检索多种属性（如创建日期）等等。

在代码清单 7-5 中，我们将从 C# 内部创建一个名为 *apress.txt* 的新文件。在其中，我们将写入一条简短的消息（“Apress 是最好的出版商！”）。不仅如此，我们还将继续读取并显示我们刚刚创建的文件的全部内容。

要使用 File 类，我们需要在程序中引入 System.IO 命名空间。

```
using System;
using System.IO;
class HappyTextWriter
{
static void Main(string[] args)
{
using (TextWriter happywriter = File.CreateText("c:\\apress.txt"))
{
happywriter.WriteLine("Apress 是最好的出版商！");
}
Console.WriteLine("文本文件已创建在 C:\n\n 其内容为：");
// 打开我们刚刚创建的文件并显示其内容
using (TextReader happyreader1 = File.OpenText("c:\\apress.txt"))
{
Console.WriteLine(happyreader1.ReadToEnd());
}
}
}
代码清单 7-5
演示 C# 中 TextWriter 和 TextReader 类的代码清单
```

除了文本文件，还有另一种文件格式供我们使用。在代码清单 7-6 中，我们将使用另一种主要类型：*二进制文件*。这种格式非常通用，可以存储文本、数字以及我们可能用到的任何内容。在下一个代码清单中，我们将创建一个二进制文件，并将一个浮点数、一个文本字符串和一个布尔变量存储到其中。同样，我们需要利用 System.IO 命名空间中的 File 类。

```
using System;
using System.IO;
class JollyBinaryTest {
static void Main(string[] args)
{
string filename1 = "c:\\binarystuff.dat"; // 定义我们的路径和文件名
/* 创建一个新的 BinaryWriter 对象 (writer1) 并将三行/三种类型的信息保存到其中 */
using (BinaryWriter writer1 = new BinaryWriter(File.Open(filename1, FileMode.Create)))
{
writer1.Write(55.52F);  // 写入一个浮点数
writer1.Write("橙子很棒"); // 写入一个字符串
writer1.Write(true); // 写入一个布尔（真/假）变量
}
Console.WriteLine("数据已写入二进制文件 " + filename1 + "！\n");
/* 创建一个新的 BinaryReader 对象 (reader1) 并使用适当的方法（例如，使用 ReadSingle() 读取浮点数）来解读我们文件中的二进制数据 */
using (BinaryReader reader1 = new BinaryReader(File.Open(filename1, FileMode.Open)))
{
Console.WriteLine("第一行: " + reader1.ReadSingle() ); // 读取一个浮点数
Console.WriteLine("第二行: " + reader1.ReadString() ); // 读取一个字符串
Console.WriteLine("第三行: " + reader1.ReadBoolean() ); // 读取一个布尔变量
}
}
}
代码清单 7-6
演示 C# 中 BinaryWriter 和 BinaryReader 类的代码清单
```

在处理非特定的二进制文件时，习惯上会使用文件扩展名 *.dat*——就像 data（数据）一样。



## FileInfo 类

在 C# 中，File 类有一个替代方案；*FileInfo* 提供了更多的控制，并且在某些条件下更为有用。让我们通过清单 7-7 来一窥如何使用 FileInfo 访问文件属性。

```
using System;
using System.IO;
class FileInfoAttributeFun
{
public static void Main()
{
string fileName1 = @"C:\apress.txt"; // 设置我们的目标文件
FileInfo ourfile = new FileInfo(fileName1);
string na = ourfile.FullName;
Console.WriteLine("Attributes for " + na + ":");
string ex = ourfile.Extension;
Console.WriteLine("File extension: " + ex);
bool ro = ourfile.IsReadOnly;
Console.WriteLine("Read-only file: " + ro);
long sz = ourfile.Length;
Console.WriteLine("Size: " + sz + " bytes");
DateTime ct = ourfile.CreationTime;
Console.WriteLine("Creation time: " + ct);
DateTime la = ourfile.LastAccessTime;
Console.WriteLine("Last access: " + la);
}
}
清单 7-7
一个演示如何使用 C# FileInfo 类访问文件属性的清单
```

清单 7-7 期望你在 Windows 硬盘的根目录下有一个文本文件 *apress.txt*。你可以修改 *filename1* 指向不同的位置和/或不同的文本文件，以使清单 7-7 发挥其魔力。

现在是时候看看 FileInfo 可以访问的一些最常用属性了。请参阅表 7-3 了解概要。

表 7-3

C# 中 FileInfo 类包含的一些属性。

| 名称 | 返回文件的名称 | Exists | 用于判断特定文件是否存在 |
| Fullname | 返回文件的完整名称和目录路径（例如，C:\MyFolder\myfile.txt） | Length | 返回文件的大小（以字节为单位） |
| CreationTime | 返回或更改文件的“创建日期” | Directory | 返回父目录的实例 |
| LastAccessTime | 返回或更改文件或目录的最后访问时间 | Attributes | 返回或更改文件的属性 |

接下来，让我们看一个更全面的 C# 文件操作示例。在清单 7-8 中，我们更广泛地使用了 FileInfo 类。这包括使用 CopyTo 方法复制文件，以及使用 Delete 方法删除文件。

```
using System;
using System.IO;
class FileInfoExample
{
public static void Main()
{
string filename1 = @"C:\wackyfile.txt"; // 主文件的路径
string file2 = @"c:\wacky_copy.txt"; // 主文件副本的路径
FileInfo ourfile = new FileInfo(filename1); // 创建新的 FileInfo 对象 "ourfile"
FileInfo ourfile_copy = new FileInfo(file2); // 为 "ourfile_copy" 执行相同操作
// 使用 Exists 属性判断文件是否已创建
if(!ourfile.Exists) {
File.Create(filename1).Dispose(); // 创建文件并解除对 "ourfile" 的文件锁定
Console.WriteLine("File not found. Creating " + filename1);
} else Console.WriteLine("File found.");
// 显示我们文件的完整名称
Console.WriteLine("Attempting to make a copy of " + ourfile.FullName);
// 检查副本是否存在。如果不存在，则复制文件
if(!ourfile_copy.Exists) { ourfile.CopyTo(file2);
Console.WriteLine("{0} has been copied as {1}", ourfile, file2);
} else Console.WriteLine("File not copied. Duplicate already found.");
// 提示用户输入
Console.WriteLine("Would you like to delete these files? (y/n)");
char input1 = (char)Console.Read(); // 将 Console.Read() 赋值给字符 "input1"
if(input1=='y') { // 如果用户输入 'y'，则删除两个文件
Console.WriteLine("Deleting files..");
ourfile.Delete();
ourfile_copy.Delete();
} else Console.WriteLine("Files in C:\\ not deleted.");
}
}
清单 7-8
一个 C# 清单，演示了 FileInfo 类中的一些文件操作
```

让我们详细分析清单 7-8。首先，我们定义了两个字符串：*filename1* 和 *file2*。这些字符串包含完整的文件路径，指向 Windows 根目录 C: 中的两个文件。我们也可以省略路径，只使用文件名（即 *wackyfile.txt* 和 *wacky_copy.txt*）。

接下来，我们使用 FileInfo 类创建一个对象，并将其命名为 *ourfile*。然后为文件副本创建另一个对象 *ourfile_copy*。我们需要实例化 FileInfo，以便稍后使用该类的方法。

字符串值前的 at 符号（即 @）在 C# 中表示原义字符串。这意味着后续的字符串将不会解析任何转义序列，例如反斜杠的 `"\\"`。比较以下几行（上面一行是原义字符串）：

`string file1 = @"c:\user\xerxes\documents\text\nincompoop.txt";`

`string file1 = "c:\\user\\xerxes\\documents\\text\\nincompoop.txt";`

*if(!ourfile**.**Exists)* 这一行用于检查对象 ourfile 的 Exists 属性，该属性顾名思义。前面加了一个感叹号，条件子句的含义是“如果 ourfile 不存在”。继续往下，*File.Create(filename1).Dispose();* 这一行包含两条指令。首先，Create 方法用于在你的存储系统上实际创建一个文件。Dispose 方法则用于释放文件的访问状态。如果没有它，稍后在程序中操作此文件时可能会遇到问题。

现在，回到我们的条件子句。它以关键字 *else* 结尾，含义是“如果 ourfile 存在”，在这种情况下，它会输出消息“File found.”。接下来，我们尝试复制对象 *ourfile*。是时候使用另一个条件子句了。*if(!ourfile_copy**.**Exists) { ourfile**.**CopyTo(file2);* 这一行再次用于检查文件是否存在，这次关注的是我们之前定义的另一个文件 *ourfile_copy*。如果它不存在，我们就调用 CopyTo 方法将 ourfile（即 *wackyfile.txt*）复制到 file2（即 *wacky_copy.txt*）。在条件子句的末尾，我们再次有一个可选的 else 关键字。这次它用于显示消息“File not copied. Duplicate already found.”。

现在我们进入一些用户交互环节。提示用户按“y”（然后按回车键），如果他们希望删除原始文件及其副本。这是通过 Delete 方法实现的。如果用户输入“y”以外的任何字符，文件将保持不变，并显示消息“Files in C:\ not deleted.”。



## File 与 FileInfo：有何区别？

C# 中的 File 和 FileInfo 类非常相似。然而，它们在语言中作为独立实体存在是有原因的。当需要对文件执行多个操作时，FileInfo 类是更优选择，而 File 类则最适合单个操作。FileInfo 还通过其字节级的文件操作提供了更多的手动控制。

File 类中的方法是静态的，而 FileInfo 类中的方法则是基于实例的。静态方法需要更多参数，例如完整的目录路径。相应地，在特定场景下（例如为基于网络的环境编码时），略显繁琐的 File 类反而可能产生更有效的结果（见表 7-4）。

表 7-4

C# 中 File 和 FileInfo 类的主要区别

| File | FileInfo |
| --- | --- |
| 使用静态方法 | 使用基于实例的方法 |
| 无需实例化 | 需要实例化 |
| 提供更多方法 | 提供较少方法 |
| 在某些场景下（例如网络流量）可能提供更快的性能 | 对文件读写操作有更精细的控制 |

要观察 FileInfo 提供的一些手动控制，请参见代码清单 7-9。

```
using System;
using System.IO;
// 创建一个新的 FileInfo 对象 "fileobj1"
FileInfo fileobj1 = new FileInfo(@"c:\apress.txt"); // 此文本文件应包含一些内容
// 打开文件进行读取：此行仅适用于 FileInfo，不适用于 File
FileStream filestream1 = fileobj1.Open(FileMode.Open, FileAccess.Read);
// 创建一个与文件流长度相同的字节数组
byte[] bytearray1 = new byte[filestream1.Length];
// 设置字节计数器的变量，并将文件长度赋给它
int howmanybytes = (int)bytearray1.Length;
int bytesread = 0;
// 使用 while 循环逐字节读取文件
while (howmanybytes > 0)
{
int i = filestream1.Read(bytearray1, bytesread, howmanybytes);
if (i == 0) break; // 当剩余字节为零时，跳出循环
howmanybytes-=i;
bytesread+=i;
}
// 将字节转换为使用 UTF-8 字符编码的字符串
string string1 = Encoding.UTF8.GetString(bytearray1);
Console.WriteLine(string1); // 显示字符串
代码清单 7-9
一个演示在 C# 中使用 FileInfo 和 FileStream 类手动控制读取数据的代码清单
```

FileStream 类允许我们逐字节地读写文件。这是通过代码清单 7-9 中的 Open 方法实现的。这是一个多功能方法，它接受多个属性；要以只读模式打开文件，我们将参数 FileMode.Open 和 FileAccess.Read 传入 Open 方法。在代码清单的下一步中，我们使用关键字 byte[ ] 创建了一个字节数组。提醒一下，数组是值的集合；这些值可以由数字、字符、字符串或字节组成。在 C# 中，数组使用方括号定义（再次提醒，每个字节由八位组成，常用于表示字母字符等）。

接下来，通过 `int howmanybytes = (int)bytearray1.Length;` 这一行，我们创建了一个整数 `howmanybytes`，并对其应用 Length 方法以获取文件的字节大小。在随后的 while 循环中，使用 Read 方法读取字节。

更准确地说，这个 while 循环在变量 `howmanybytes` 保持大于零时执行。我们之前定义的文件流 `filestream1` 应用了 Read 方法，该方法以字节数组（`bytearray1`）、已读取的字节数（`bytesread`）和待读取的总字节数（`howmanybytes`）作为参数。此输出被赋给变量 `i`。当 `i` 达到零时，我们使用 `break` 关键字跳出循环。

代码清单 7-9 的最后步骤包括：首先将 `bytearray1` 转换为可读的 Unicode 字符并存储到 `string1` 中，然后显示该字符串。

使用 *UTF-8* 字符编码时，存储在字符串中的每个拉丁字母数字字符通常占用两个字节，即 16 位。

## C# 中的垃圾回收

*垃圾回收（GC）* 的概念基本上指的是自动内存管理。此机制用于根据程序需要分配和释放 RAM；支持垃圾回收的编程语言使程序员免于这些任务。大多数现代语言（包括 C#）都原生支持 GC。此功能也可以通过额外的软件库添加到没有原生支持的语言中。

随着编程项目的增长，它通常会包含越来越多的变量和其他数据结构，如果不妥善管理，这些会消耗大量 RAM。管理这些资源对程序员来说可能变得有些麻烦。此外，有问题的内存分配可能导致稳定性问题。

现在，C# 中的垃圾回收在以下三种情况下被调用：

1.  你的计算机物理内存不足。系统拥有的 RAM 越多，这种情况发生的频率就越低。

2.  已分配对象使用的内存超过了特定阈值。此阈值由垃圾回收组件实时更新。

3.  程序员调用了 *GC.Collect* 方法。对于较小的项目，这通常不是必需的。

## 本机堆与托管堆

*本机堆* 是由操作系统管理的动态分配内存。每当程序执行时，本机堆内存会被动态分配和释放。被称为 *托管堆* 的内存区域则是一个不同的实体。每个独立的进程都有自己的托管堆。进程内的所有线程也共享此内存空间。



## C# 中的多线程

正如你所料，C# 确实完全支持多线程。请参阅清单 7-10 的演示。首先，我们定义一个自定义方法 *ChildThread( )* 来创建新线程。稍后我们将创建三个线程，它们将同时使用 `Sleep` 方法，该方法会暂停线程的处理。在我们的线程内部，这些方法被分配一个介于 0 到 6000 毫秒之间的随机值，以实现最长 6 秒的延迟，所有这些都将根据基于线程的范式同时进行计数。

程序由*进程*组成。*线程*是进程内部的实体，可以在最需要的时候被调度。在 C# 中，我们也可以对线程的创建和其他生命周期事件进行大量控制。

接下来，在我们的自定义方法中，我们检索一个相当冗长的属性 *System.Threading.Thread.CurrentThread.ManagedThreadId*，以获取每个运行线程的唯一标识号，并将其存储到 *happyID* 中。

在设置线程信息时，变量 *delay* 除以一千（即乘以 0.001）以便以秒为单位显示。我们还利用 C# `Math` 类中的 `Round` 方法将延迟整数四舍五入到两位小数。

```
using System;
using System.Threading;
public static void ChildThread() {
Random randomnr = new Random(); // 创建一个新的 Random 对象 "randomnr"
int delay = randomnr.Next(0, 6000); // 使用我们的随机对象设置一个介于 0 到 6 秒之间的随机延迟
/* 将线程 ID 号，即 System.Threading.Thread.CurrentThread.ManagedThreadId，赋值给整数 "happyID" */
int happyID = System.Threading.Thread.CurrentThread.ManagedThreadId;
Console.WriteLine("快乐线程 " + happyID + " 开始！");
// 显示延迟时间时四舍五入到两位小数
Console.WriteLine("快乐线程 " + happyID + " 暂停 {0} 秒..", Math.Round(delay * 0.001, 2) );
Thread.Sleep(delay);
Console.WriteLine("快乐线程 " + happyID + " 现在将恢复！");
}
static void Main(string[] args) {
ThreadStart child = new ThreadStart(ChildThread);
for(int i=0; i<3; ++i) { // 总共创建三个子线程
Thread childThread = new Thread(child);
childThread.Start(); // 开始执行线程
}
}
清单 7-10
演示如何在 C# 中创建子线程的清单
```

清单 7-10 的 `Main` 方法相当简单。使用 `for` 循环，我们创建了恰好三个 `ChildThread` 实例，并使用 C# `Thread` 类中的 *Start( )* 方法开始执行它们。

## C# 线程中的锁和属性

与 Java 和 Python 一样，C# 为其线程应用程序提供了一种锁定机制。通过锁，我们可以使线程同步（参见清单 7-11）。

```
using System;
using System.Threading;
public class LockingThreads
{
public void OurThread()
{
// 获取当前正在执行的线程的句柄
// 以便我们可以检索其属性，例如 Name
Thread wackyhandle = Thread.CurrentThread;
// 应用锁以同步线程
lock(this) { // 锁定代码开始
Console.WriteLine("(此线程处于 " + wackyhandle.ThreadState+" 状态，优先级为 " + wackyhandle.Priority + ")");
for(int i=0; i<3; ++i) {
Console.WriteLine(wackyhandle.Name + " 已经工作了 " +i+ " 小时");
Thread.Sleep(400); // 等待 0.4 秒再执行下一行
}
} // 锁定代码结束
}
}
public static void Main()
{
LockingThreads jollythread = new LockingThreads();
Thread thread1 = new Thread(new ThreadStart(jollythread.OurThread));
Thread thread2 = new Thread(new ThreadStart(jollythread.OurThread));
thread1.Name="Graham"; thread2.Name="Desdemona";
// 这两个线程都已同步/锁定：
// thread1 将处理完成，然后 thread2 才开始
thread1.Start();
thread2.Start();
}
清单 7-11
演示 C# 中线程锁定和访问某些线程属性的清单
```

清单 7-11 的输出如下：

```
(This thread is Running with Normal priority)
Graham has been working for 0 hours
Graham has been working for 1 hours
Graham has been working for 2 hours
(This thread is Running with Normal priority)
Desdemona has been working for 0 hours
Desdemona has been working for 1 hours
Desdemona has been working for 2 hours
```

如果没有锁定机制，我们会看到关于 Graham 和 Desdemona 的这行工作状态更新交替出现。你还会看到 `Threading` 类的三个属性被完整展示（即 *ThreadState*、*Priority* 和 *Name*）。

## Sleep 与 Yield

到目前为止，我们已经在几个清单中探索了 `Sleep` 方法的使用。重申一下，这告诉线程休眠/暂停一段预定的时间，通常以毫秒为单位指定。

现在是时候引入 C# 的 *Yield* 了。这个关键字在 C# 中有多种含义，具体取决于上下文；我们将在下一章中更详细地研究它们。在多线程环境中，`Yield` 告诉线程进入一种不确定的等待状态。一个让出（yielded）的线程将在需要时被重新激活；这可能在几毫秒内发生，也可能需要更长的时间。`Yield` 基本上是将 CPU 从执行特定线程中解放出来，以便处理其他更紧急的线程。这种紧急程度最终由操作系统决定。

C# 的 `Yield` 实际上可以在一定程度上通过 `Sleep` 方法来模拟。较旧的 .NET 框架（4.0 版本之前）尚不支持 `Yield`。在这些情况下，输入 `Sleep(0)` 的作用类似于 `Yield`。

现在让我们看看 `Yield` 的实际表现（参见清单 7-12）。

```
using System;
using System.Threading;
public class AmazingThreads
{
private int counter; // 声明一个计数器变量
public void YieldThread()
{
Console.WriteLine("第一个线程是一个带有 Yield() 的无限循环");
while(true) // 开始一个无限循环
{
// 如果没有 Yield，计数器会增长到更长的长度
Thread.Yield();
++counter;
}
}
public void SecondThread()
{
Console.WriteLine("第二个线程通知你：第一个线程的计数器达到 " + counter);
}
}
public class YieldExample
{
public static void Main()
{
AmazingThreads greatobject = new AmazingThreads();
Thread thread1 = new Thread(new ThreadStart(greatobject.YieldThread));
Thread thread2 = new Thread(new ThreadStart(greatobject.SecondThread));
thread1.Start();
thread2.Start();
}
}
清单 7-12
演示 C# 中 Yield 方法的清单
```

在清单 7-12 中，我们定义了一个类整数 *counter*，用于大致记录在 `Yield` 生效之前第一个线程的执行时间长度。然后我们启动两个线程，第一个线程进入一个无限循环。第二个线程用于显示存储在计数器变量中的数值。使用 `Yield`，在大多数情况下，我们可以预期这个数值远低于 2000。如果没有 `Yield`，计数器显示的数值会高出几个数量级，最终导致程序无响应。

再次强调，操作系统是根据其他线程的状态和优先级来调度发出 `Yield` 请求的线程的最终实体。



## Join

C# 中用于线程处理的最重要方法之一称为 *Join*。通过此方法，你可以让线程等待其他线程处理完毕。当然，Join 只能由已开始执行的线程调用（参见清单 7-13）。

```
using System;
using System.Threading;
static void OurFunction() { // 创建一个自定义方法
for (int i = 0; i < 10; ++i)
Console.Write(i + " ");
}
static void Main(string[] args) {
Thread thread1 = new Thread(OurFunction);
thread1.Start();
thread1.Join();
// Join() 确保线程在清单继续执行前完成其处理
Console.Write("10"); // 用数字十完成列表
}
清单 7-13
C# 中 Join 方法的基本演示
```

清单 7-13 为我们提供了一个 Join 方法的基础示例。清单 7-13 的输出应为 *0 1 2 3 4 5 6 7 8 9 10*。如果没有 Join，你可能会得到意外结果，例如 *100 1 2 3 4 5 6 7 8 9*。这是因为最后的 *Console.Write* 没有被明确告知要等待 *thread1* 完成其处理。

## C# 中的异步编程

基本上，*异步编程* 是一门让程序由众多不会相互冲突（或与其他程序冲突）的任务组成的艺术。这种方法通常也让程序员看起来更轻松，因为它能产生相当清晰的代码布局。

当前 C# 中异步处理的实现利用了一个名为 *ThreadPool* 的类。这个类采用了与 Thread 类（本章前面讨论过）不同的方法。首先，ThreadPool 只创建优先级较低的后台线程；为线程分配优先级不是这个类的一部分。

*设计模式* 是针对软件设计中特定上下文中反复出现的问题的一种可重用解决方案。

现在，C# 中有三种主要的异步编程范式。截至 2021 年，微软仅推荐使用其中一种，即 TAP。

*   **异步编程模型 (APM)**：APM 方法使用 *IAsyncResult* 接口及其 *BeginOperationName* 和 *EndOperationName* 方法。微软不再推荐使用这种有些繁琐的设计模式。

*   **基于事件的异步模式 (EAP)**：EAP 旨在提供异步软件范式的优势，同时从程序员的角度保持相对优雅。这种设计模式也被认为是一种过时的方法。

*   **基于任务的异步模式 (TAP)**：TAP 是用于编写异步软件的最现代、最优雅的设计模式。与 APM 和 EAP 不同，TAP 使用单个方法来表示异步操作的启动和完成，并以 *Task* 和 *Task<TResult>* 类为核心。

有关使用 TAP 的异步模式示例，请参见清单 7-14。

```
using System;
using System.Threading;
static async Task Main(string[] args) // 我们的小型异步主线程
{
await happyMethod(); // 等待 happyMethod 完成其工作
Console.WriteLine("程序完成。");
}
public static async Task happyMethod() // 创建异步任务 happyMethod()
{
int fibo = await Fibonacci(); // 等待 Fibonacci 方法完成
// 并将其结果赋值给整数 "fibo"
DisplayFibonacci(fibo); // 调用我们最简单的方法
}
public static async Task Fibonacci()
// 创建异步任务 Fibonacci()，它将返回一个整数
{
int x = 0, y = 1, z = 0; // 定义 for 循环中需要的变量
// 为了清晰起见，此任务保持简单。在实际应用中
// 你可以在此处与其他异步任务一起执行一些繁重的工作。
await Task.Run(() =>
{
for (int i = 2; i < 5; i++) // 查找斐波那契数
{
z = x + y;
x = y;
y = z;
}
});
return z; // 返回结果，即第五个斐波那契数
}
public static void DisplayFibonacci(int fibo) // 用于显示结果的方法
{
Console.WriteLine("第五个斐波那契数是 " + fibo);
}
清单 7-14
使用 TAP 设计模式在 C# 中进行异步处理的演示
```

现在，清单 7-14 被分成四个方法，其中只有一个本质上不是异步的（即 *DisplayFibonacci*）。前三个方法使用了 *async/await* 机制。通过在方法旁边使用定义 *async*，我们将一个方法指定为异步方法。这允许我们随后使用 await 关键字，该关键字会将控制权交还给其调用方法。

async/await 机制允许程序在后台处理可能很繁重的计算，这通常会导致更少的用户界面卡顿和/或更快的网络文件传输。

在创建方法时，TAP 有一些特定的关键字。*Task* 用于表示不返回任何值的方法。在我们的清单中，你还会找到一个 *Task<int>* 的实例；这个表达式表示一个必须返回整数的方法。任务可以返回任何类型的变量。所有创建的异步任务都将被调度到由 ThreadPool 类管理的线程上运行。

命令 *await Task.Run* 使用 ThreadPool 类在其自己的线程中启动一个任务。现在，基本上有三种方式来调用这种机制：

```
1        await Task.Run(() => SomeMethod );
2        await Task.Run(() => SomeOtherMethod(something) );
3        await Task.Run(() => {
Console.WriteLine("让我们在这里做点什么");
return 0;
});
```

第一种方法接受先前声明的无参数方法；你只需传递方法的名称。第二种方法适用于带参数的方法。我们也可以使用*基于块*的语法，如第三种机制所示。与往常一样，必须注意正确使用括号、花括号和其他特殊字符。你可能已经注意到，清单 7-7 为其 *await Task.Run( )* 使用了基于块的方法。

在*斐波那契数列*中，每个数字代表它前面两个数字之和，例如，0, 1, 1, 2, 3, 5, 8。这个概念由意大利数学家*莱昂纳多·波那契（生于 1170 年，卒于约 1250 年）*在其开创性著作*《计算之书》*中提出。在他生命中的某个时刻，波那契被赋予了斐波那契这个绰号。

## 结语

完成本章后，你可能已经学到了以下不少内容：

*   C# System.Globalization 命名空间及其一些类（包括 Calendar 和 CultureInfo）的一些常见用途

*   使用 File 和 FileInfo 类在 C# 中进行基本文件操作

*   C# 中垃圾回收 (GC) 的基础知识

*   C# 中基本多线程的实现，包括一些最相关的关联方法（Join、Start、Sleep、Yield）

*   使用基于任务的异步设计模式 (TAP) 及其 async/await 机制在 C# 中进行异步编程的基础知识

第 8 章将专门介绍 C#、Java 和 Python 提供的更精细的技术，包括高级多线程。我们将进入制作有用（但规模明确较小）的应用程序的世界。

