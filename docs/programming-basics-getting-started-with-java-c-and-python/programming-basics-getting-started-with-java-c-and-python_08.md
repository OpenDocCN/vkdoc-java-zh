# 8. 毕业日：稍大型的编程项目

在本章中，我们将研究几个比迄今为止介绍的稍大型的 Python、C# 和 Java 编程项目。这些项目旨在演示本书中讨论的一些最相关的概念：变量、循环、文件访问和线程。如果你能够解读这些小项目的内部机制，那么你可以有把握地说，你对本书中介绍的三种编程语言的基础知识有了一定程度的理解。如果你此时仍然对自己的编码技能感到不确定，本章可以帮助你澄清一些概念。



## Python 通用聊天模拟器

本章首先向读者介绍*通用聊天模拟器*。该程序将演示 Python 的以下特性，这些特性我们此前在书中均已讨论过：

*   文件操作
*   字符串格式化
*   自定义函数定义（使用 `def` 关键字）
*   变量（包括随机化）、列表和迭代
*   基本异常处理
*   元素枚举
*   程序流程与简单循环
*   日期和时间获取

与本章中的其他程序一样，通用聊天模拟器（UCS）是一个控制台应用程序，这意味着它仅在文本模式下运行；为了保持简单，不会使用任何图形元素。

现在，UCS 的两个关键元素以虚拟聊天主持人和虚拟观众的形式出现，后者以“聊天”这个共享昵称来呈现。这两个虚拟角色每隔几秒就会在屏幕上输出文本，其内容由三个外部（因此易于编辑）的文本文件决定。

无需输入整个代码清单即可开始体验 UCS；该文件可在 GitHub 上获取，该处可免费下载的代码清单也附有完整注释。不过，为了详尽起见，我们现在逐块分析该程序。

## 第一部分：项目设置

首先，我们导入所需的代码模块。*Datetime*、*time* 和 *random* 是 Python 自带的。然而，对于我们的项目，我们还需要从 *Python 包索引（PyPI）* 获取一个额外的模块。这是一个用于扩展 Python 功能的免费在线仓库。我们将使用 Abhijith Boppe 开发的资源 *clrprint*。安装并导入 clrprint 模块后，我们可以通过如下代码行向 Python 项目添加彩色文本：*clrprint(“Hello!”, clr=‘red’)*。项目代码的第一部分见代码清单 8-1。

只需在 macOS 终端、Windows shell 或 Linux 命令行中输入 *“pip3 install clrprint”* 即可安装 *clrprint*。

```
# 导入三个 Python 代码模块
import datetime
import time
import random
# 同时从 Python 包索引（PyPI）导入 clrprint 模块
from clrprint import *
name = "Apple representative"  # 虚拟聊天主持人名称
mood = 50  # 心情值：50 = 中立，0 = 坏结局，100 = 好结局
score = messages_sent = 0  # 将分数和已发送消息数赋值为零
response = negative = positive = False  # 将三个变量赋值为 False
negativetrigger = "orange"  # 负面触发词，用于降低心情值
positivetrigger = "apple"  # 正面触发词，用于提升心情值
# 使用 datetime 类的 now() 方法获取当前时间和日期
initial_time = datetime.datetime.now()
# 定义一个自定义函数 print_chat()
# 变量 "cap" 决定聊天消息如何大写（如果需要的话）
# 0 和 -1 都表示不改变大小写
# 1 表示将一行首字母大写，2 表示将一行中所有字母大写
def print_chat(chat):
cap = random.choice([-1, 0, 1, 2])
if cap == 1:
chat = chat.capitalize()  # 调用 capitalize() 方法
elif cap == 2:
chat = chat.upper()  # 调用 upper() 方法实现全大写
print("Chat: " + timer.strftime(" ") + chat)
代码清单 8-1
Python 通用聊天模拟器代码清单的第一部分
```

接下来，我们定义一个名为 *open_chatfile* 的自定义函数，它接受两个参数：*name* 和 *deletechar*。Name 仅表示我们要打开的文件名。Deletechar 用于指定我们要从这些列表中删除的字符；这样做的目的是，例如，我们可以删除聊天中的所有换行符（即 *\n*），以消除不必要的空行（见代码清单 8-2）。

我们还在函数中使用了一个临时列表结构 *templist*。数据被输入到 templist 中，以便对其进行枚举并移除其中的换行符。文件内容使用 Python 的 readlines 方法读取。

```
def open_chatfile(name, deletechar):
try:
file = open(name, 'r')  # 以只读模式 "r" 打开文件
print("正在加载文件 " + name)
templist = file.readlines()  # 将文件内容读入 templist
for i, element in enumerate(templist):  # 枚举列表
templist[i] = element.replace(deletechar, '') # 删除换行符
return templist  # 返回枚举后的列表
file.close()  # 关闭文件始终是一个好习惯
except (FileNotFoundError, IOError):  # 处理异常
# str() 将变量转换为字符串
print("无法读取文件 " + str(name) + "！")
代码清单 8-2
Python 通用聊天模拟器代码清单的第二部分
```

## 第二部分：显示最高分

在进入计分环节之前，我们需要定义三个列表，用于我们的 open_chatfile 函数；它们分别名为 *neutralmoods*、*badmoods* 和 *chatmoods*。来自文本文件的数据需要存储在某处，这就是我们三个列表的用武之地（见代码清单 8-3）。

在 Python 中，我们可以使用赋值运算符在同一行用同一个变量初始化变量（包括列表），例如，*a = b = 10* 或 *happy = wonderful = [ ]*。

接下来，我们实际使用我们的方法，调用它三次来打开文件 *neutral.txt*、*bad.txt* 和 *chat.txt*。open_chatfile 接收的另外两个参数是我们之前创建的列表和换行符。现在，我们有了三个充满发人深省内容的列表，随时准备为我们的虚拟聊天带来乐趣。

在本项目代码块的结尾，我们将打开一个包含当前最高分的文本文件。是的，通用聊天模拟器中有一个计分系统；稍后会详细介绍。我们通过创建一个 try 块来获取分数数据。提醒一下，try 块允许我们即时测试代码中的错误。结合 except 关键字（exception 的缩写），我们可以引入自己的错误消息。在代码清单 8-3 的 try 块中，尝试加载文件 *highscores.txt*。如果找不到这样的文件，异常块会告诉我们并创建该文件。

```
# 使用 clrprint 模块以鲜艳的黄色显示程序标题
clrprint("通用聊天模拟器 (UCS) 版本 0.1.0 由开发者 Bob 制作", clr='yellow')
# 定义三个列表结构，用于填充来自文本文件的数据
neutralmoods = badmoods = chatmoods = []  # [] 表示一个空列表
# 使用我们的自定义函数将文本文件数据插入三个列表
neutralmoods = open_chatfile("neutral.txt", "\n")
badmoods = open_chatfile("bad.txt", "\n")
chatmoods = open_chatfile("chat.txt", "\n")
print("你的初始心情值为 " + str(mood) + "/100")
# 显示存储在 "highscore.txt" 中的当前最高分
try:
# 以 r+ 模式打开 "highscore.txt"，用于读写
with open("highscore.txt", "r+") as scorefile:
hiscore = scorefile.read()
print("当前最高分：%s" % hiscore)
# 如果找不到 highscore.txt，则创建此文件
except (FileNotFoundError, IOError):
print("未找到最高分。正在创建 highscore.txt")
scorefile = open("highscore.txt", "w")
scorefile.write("0")  # 将零写入 "highscore.txt"
scorefile.close()
代码清单 8-3
Python 通用聊天模拟器代码清单的第三部分
```



## 第三部分：坚不可摧的主循环

现在，我们进入主循环的领域。这是大部分处理过程及/或魔法发生的地方。UCS 运行在一个 while 循环中，该循环的条件是“当变量 mood 小于 100”。如果 mood 达到 100 以上，循环将不再运行。此外，如果 mood 降至 1 以下，则使用 `break` 关键字退出循环。

虚拟聊天者在程序中的表现通过一个名为 *chat* 的变量来实现；程序会从 `chatmoods` 列表中随机选择一个元素，并将其赋值给这个变量，以便在后面的代码清单 8-4 中进一步显示。

你可能想知道变量 *mood* 为何如此重要。mood 不仅用于跳出主循环，而且当其值低于 30 时，程序将开始为我们的虚拟主持人使用另一组台词（即，在文件 *bad.txt* 中定义的更尖刻的台词）。mood 整数值会受到特定关键词的影响，具体来说，就是之前在代码清单 8-1 中定义的两个变量 *negativetrigger* 和 *positivetrigger*。如果程序检测到负面触发词，“mood” 的值会减少 1 到 5 之间的一个随机数。类似地，检测到正面触发词则会使该整数值增加 1 到 5 之间的一个随机数。为此，我们使用 `random.randint(1, 5)` 这行代码在所需范围内生成一个整数，并将其赋值给另一个变量 *moodchange*。

现在，在代码清单 8-4 中，有两个布尔变量用于在检测到触发词时发出标志；它们被简单地命名为 *negative* 和 *positive*。这两个布尔值用于在程序后期显示关于任何情绪变化的状态消息。

## 大小写与延迟

接下来，我们为程序运行过程增添一些外观上的变化。代码清单 8-4 可以以三种格式呈现所有聊天行：常规、首字母大写和全部大写。这是通过在每次主循环中检查变量 *cap* 的随机值来实现的。我们为 cap 设置了四个可能的值：-1、0、1 和 2。如果 cap 小于 1，则聊天行保持不变。值为 1 时，程序会调用 Python 的 *capitalize( )* 方法，该方法顾名思义。最后，值为 2 对应 *upper( )* 方法，基本上是为我们的虚拟聊天者开启了“大写锁定”（仅针对一行）。

对于一个模拟聊天的程序，必须实现一些延迟，以便观看者能够充分享受这条略带洞察力的评论流。在代码清单 8-4 中，我们通过应用 Python 强大的 `sleep( )` 方法，并为其提供一个介于 1 和 3 之间的随机值（对应相同秒数的延迟）来实现这一点。

```
# 主 while 循环开始
while 1 < mood < 100:
    # 检查是否检测到触发词
    if negative:
        clrprint("*** 检测到负面触发词 ***", clr='red')
    if positive:
        clrprint("*** 检测到正面触发词 ***", clr='green')
    # 随机选择聊天行
    chat = random.choice(chatmoods)
    # 随机选择大小写格式
    cap = random.choice([-1, 0, 1, 2])
    if cap == 1:
        chat = chat.capitalize()
    elif cap == 2:
        chat = chat.upper()
    # 显示聊天行
    clrprint(name + ": " + chat, clr='white')
    # 随机延迟
    time.sleep(random.randint(1, 3))
    # 更新消息计数
    messages_sent += 1
    # 检查情绪变化
    if mood < 30:
        clrprint(
            name + ": " + timer.strftime(" ") +
            random.choice(badmoods),
            clr='white')
    else:
        clrprint(
            name + ": " + timer.strftime(" ") +
            random.choice(neutralmoods),
            clr='white')
代码清单 8-4
通用聊天模拟器（Python 版）代码清单的第四部分
```

## 第四部分：游戏结束

在相当繁忙的主循环之后，我们终于到达了聊天模拟器的最后一部分。它主要包括处理计分，并为了增加趣味性而加入了一些路径分支（见代码清单 8-5）。

程序中的计分基于虚拟聊天者发送的聊天消息数量；每条消息价值 3 分。现在，这个模拟器程序有两种结局。在“坏”结局中，观看者只会被告知虚拟聊天主持人的情绪降到了零。另一个更乐观的结局则会给观看者一个价值 1000 分的“欣快感奖励”。

```
# 游戏结束
score = messages_sent * 3  # 每条来自聊天的消息价值 3 分
if mood <= 0:
    print("虚拟主持人的情绪降到了零。游戏结束。")
elif mood >= 100:
    print("虚拟主持人欣喜若狂！你获得了 1000 分的欣快感奖励！")
    score += 1000
# 保存最高分
if score > int(hiscore):  # 如果当前分数大于存储的最高分
    print("创造了新的最高分！")
    scorefile.seek(0)  # "回退"到文件开头
    scorefile.write(str(score))  # 将新分数写入 "highscore.txt"
else:
    print("当前最高分：%s" % hiscore)
代码清单 8-5
通用聊天模拟器（Python 版）代码清单的第五部分，也是最后一部分
```

我们在代码清单 8-4 中使用了相当多的方法。作为回顾，让我们记录一下通用聊天模拟器使用的主要 Python 方法（见表 8-1）。

表 8-1
通用聊天模拟器使用的一些 Python 方法

| 方法 | 描述 | 在代码清单 8-4 中的使用示例 |
| --- | --- | --- |
| open( ) | 打开文件进行读取和/或写入 | file = open(name, ‘r’) |
| read( ) | 读取已打开文件的内容 | hiscore = scorefile.read( ) |
| seek( ) | 设置文件的位置 | scorefile.seek(0) |
| random.randint( ) | 返回一个随机整数 | neutralmoods[random.randint(0, len(neutralmoods) - 1)], |
| random.choice( ) | 随机选择一个元素 | response = random.choice([True, False]) |
| str( ) | 将变量转换为字符串 | print(“SCORE: “ + str(score)) |
| int( ) | 返回一个整数对象 | if score > int(hiscore): |
| len( ) | 返回对象/字符串的长度 |   |



## C# 线程赛车

为了回顾 C# 中线程的工作原理，请查看清单 8-6。在这个简短的清单中，三个线程被派去进行一场五米短跑。该程序演示了 C# 语言的以下特性：

*   简单的线程化以及对共享数据/变量的基于线程的访问
*   使用锁定对象和 `Interlocked.Decrement( )` 方法
*   为变量（包括字符串数组）分配随机值
*   定义方法（例如 `MakeNames`）并从中检索数据

```
using System;
using System.Threading;
public class ThreadRacer
{
// 定义一个空（即 null）字符串，用于保存比赛的获胜者
string winner = null;
// 定义一个整数，用于检查线程到达终点线的顺序
int place = 3;
// 创建一个对象 happylock，用于线程锁定
static object happylock = new object();
// 定义 Racer，一个基于线程的方法
public void Racer()
{
int distance = 5; // 设置比赛距离为 5
// 将变量 "handle" 赋值为当前线程，以便我们读取
// 线程属性，例如 Name
Thread handle = Thread.CurrentThread;
// 当线程的距离大于零时循环
while(distance > 0) {
Console.WriteLine(handle.Name+" 距离终点还有 " + distance + " 米。");
// 使用 Interlocked.Decrement 方法将赛手的距离减一
Interlocked.Decrement(ref distance);
}
// 调用我们的锁定对象
lock(happylock) {
// 如果 winner 变量仍为 "null"，则宣布当前线程
// 为获胜者
if(winner == null) Console.WriteLine(handle.Name + " 赢得了第 1 名！");
// 使用 Interlocked.Decrement 方法从变量 "place" 中减去 1
Interlocked.Decrement(ref place);
winner = handle.Name;
if(place==1) Console.WriteLine(handle.Name + " 获得了第 2 名..");
if(place==0) Console.WriteLine(handle.Name + " 获得了第 3 名..");
}
}
public static string MakeNames() {
// 使用 C# Random 类创建新对象 random0
var random0 = new Random();
// 创建两个字符串数组 first_names 和 last_names
string[] first_names = { "Peter", "Paul", "Patrick", "Patricia", "Priscilla", "Pauline" };
string[] last_names = { "Plonker", "Pillock", "Prat", "Pecker" };
int f_index = random0.Next(first_names.Length);
int l_index = random0.Next(last_names.Length);
// 将名称作为字符串返回：
return (first_names[f_index] + " " + last_names[l_index]);
}
public static void Main() // Main 函数
{
ThreadRacer racer = new ThreadRacer();
Thread thread1 = new Thread(new ThreadStart(racer.Racer));
Thread thread2 = new Thread(new ThreadStart(racer.Racer));
Thread thread3 = new Thread(new ThreadStart(racer.Racer));
thread1.Name = "[赛手 A] " + MakeNames();
thread2.Name = "[赛手 B] " + MakeNames();
thread3.Name = "[赛手 C] " + MakeNames();
Console.WriteLine("欢迎来到线程赛车！\n");
thread1.Start(); // 启动线程
thread2.Start();
thread3.Start();
}
}
清单 8-6
一个演示 C# 线程的程序
```

在清单 8-6 中，我们创建了一个锁定对象 `happylock`，用于线程同步。我们还使用了一个新方法 `Interlocked.Decrement`，将“distance”和“place”变量精确地减一。为了在基于线程的项目中进行有效且安全的加法运算，C# 还提供了一个名为 `Interlocked.Increment` 的方法（我们在线程赛车中不需要它）。在 C# 中处理基于线程的代码时，这两个方法通常可以替代标准的减法和加法运算符（例如 `--variable` 或 `++variable`）。

现在，我们在清单 8-6 中自己编写了一个方法；这就是 `MakeNames`。此方法用于演示数组，更具体地说是字符串类型的数组。这些数组填充了名称，并作为单个字符串返回，供主函数使用。

线程赛车中的 `f_index` 和 `l_index` 变量用于存储字符串数组 `first_names` 和 `last_names` 的所需索引位置。这些索引位置又通过在这些数组的长度上应用 `Next` 方法（一个随机化器）来创建。然后通过调用恰如其名的 `Length` 方法来推导出这些长度。

以下是清单 8-6 中的一行代码：

```
int f_index = random0.Next(first_names.Length);
```

这行代码的意思是：“让整数 `f_index` 等于一个**随机数**，其最大值为 `first_names` 数组的**长度**。”读起来朗朗上口。

我们可以将方法的输出直接放入变量中。以清单 8-6 中的这行为例：`thread1.Name = “[赛手 A] ” + MakeNames( );` 在这行代码中，我们使用一个简单的加号运算符，将字符串 `“[赛手 A]”` 与 `MakeNames` 输出的任何内容组合在一起。

线程赛车的线程调度也是由操作系统执行的。这意味着过一段时间后，你可能会得到相同的结果。



## C# 趣味问答

接下来，清单 8-7 为您呈现一个基于控制台的趣味问答程序。它展示了这门优秀语言的以下特性：

*   基本文件操作和线程处理
*   字符串格式化
*   程序流程、用户交互和循环
*   变量和迭代

趣味问答使用两个独立的文件：*questions.txt* 和 *answers.txt*。这两个文件都从上到下处理，问题文件中的每一行与答案文件中的对应行相匹配。这种方法使得只需编辑这两个文本文件，就能轻松添加新问题或修改现有问题。

用户需要在程序运行期间输入答案。一个线程对象在后台运行，独立于正在进行的问答，每四秒提醒用户“快点！”。每答对一题得十分。问答结束时，会向用户显示其得分以及正确答案的百分比。

```
using System;
using System.IO;
using System.Threading;
class JollyQuiz
{
public static void Main()
{
// 声明三个整数变量
int counter=0, score=0, percentage=0;
// 声明一个布尔变量（取值：true 或 false）
bool countdown=true;
Console.WriteLine("欢迎来到趣味问答程序！\n");
// 创建一个新的线程对象 timerthread1
Thread timerthread1 = new Thread(delegate() {
Thread.Sleep(6000); // 首先休眠 6 秒
while(true) { // 在线程内部创建一个无限循环
Console.WriteLine("快点！");
Thread.Sleep(4000);
// 如果主程序将 countdown 设置为 "false"，则结束线程处理
if(!countdown) break; // 这行代码的意思是：如果 countdown 不是 "true"，则退出循环
}
});
// 打开问题和答案文件。这些文件应与您的项目文件位于同一目录
var q = File.ReadAllLines("questions.txt");
var a = File.ReadAllLines("answers.txt");
timerthread1.Start(); // 启动计时器线程
foreach(string lines in q) // 遍历问题文件
{
Console.WriteLine("{0}\n 请输入回答：", lines);
// 将回答转换为全大写，以消除输入大小写问题
// 例如，使 "Helsinki" 与 "HeLSiNKI" 同样有效
string response = Console.ReadLine().ToUpper();
++counter;
// 将用户输入与正确答案进行比较
if(response.Equals(a[counter-1])) {
Console.WriteLine("{0} 是正确的！", response); score+=10;
// 使用 else if 判断用户是输入了错误答案还是直接按了回车
} else if(response!="") Console.WriteLine("{0} 是错误的..", response);
else Console.WriteLine("下次请输点东西！");
}
// 问答结束后无需再提醒用户加快速度；
// 因此我们接下来告诉 timerthread1 结束处理
countdown = false;
Console.Write("\n 您的得分：{0}。", score);
percentage = score*100/(counter*10);
Console.WriteLine("在给出的 {1} 个问题中，您答对了/猜对了 {0}%。", percentage, counter);
// 得分低时显示激励信息
if(percentage = 90) Console.WriteLine("做得好！");
}
}
清单 8-7
一个用 C# 编写的问答程序，演示了基本文件操作和线程处理
```

您会注意到清单 8-6 和 8-7 中有一个特殊的变量类型 *var*。这是 C# 中的*隐式变量定义*，简单来说就是允许编译器程序自行确定变量的类型。对于 C# 中的小型项目，这种方法通常效果很好。

## Java 食谱书

接下来，我们将通过一个名为 *美味的芬兰食谱* 的小程序回到 Java 的世界。这个相当直接的清单基本上展示了如何显示易于定制的文本文件。

```
import java.io.File;
import java.io.FileNotFoundException;
import java.util.Scanner;
public class FinnishRecipes {
// 定义 LoadText，一个用于加载文本文件的方法
// 它接受文件路径和文件名作为参数
static void LoadText(String path1, String section) {
try {
File fileobject1 = new File(path1 + section);
// 创建一个 Scanner 对象 reader1 用于用户输入
Scanner reader1 = new Scanner(fileobject1);
while (reader1.hasNextLine()) {
String output1 = reader1.nextLine();
System.out.println(output1);
}
reader1.close();
} catch (FileNotFoundException e) { // 如果需要，抛出异常/错误
System.out.println("文件 " + section + " 未找到！");
}
}
static void DisplayMenu() {
System.out.println("美味的芬兰食谱\n 请输入选择：");
System.out.println("[1] 沙拉");
System.out.println("[2] 主菜");
System.out.println("[3] 自定义食谱（自己制作）");
System.out.println("[4] 退出)");
}
// 定义 Main 方法
public static void main(String[] args) {
// 显示主菜单
DisplayMenu();
Scanner keyscanner1 = new Scanner(System.in);
while(true) { // 创建一个无限 while 循环
String input1 = keyscanner1.nextLine();
// "C:\\" 指的是最常见的 Windows 根驱动器路径
// 请根据您的文件位置修改此路径
if(input1.equals("1")) LoadText("","salads.txt");
if(input1.equals("2")) LoadText("","maincourses.txt");
if(input1.equals("3")) LoadText("","custom.txt");
if(input1.equals("4")) break;
// 如果用户只输入了回车，则再次显示菜单
if(input1.equals("")) DisplayMenu();
} System.out.println("祝您愉快！");
}
}
清单 8-8
一个用 Java 编写的程序，演示了文件访问
```

在清单 8-8 中，我们创建了一个方法 *LoadText*，它接受两个参数，即文件路径和文件名。然后它会加载指定的文本文件，并逐行在屏幕上显示其内容。我们还有另一个方法 *DisplayMenu*，它仅用于向用户显示可用选项。这两个方法都被定义为 *void*，这意味着它们不返回任何信息。

清单 8-8 中有一个 try-catch 块，如果找不到文件，它会发出警报，即抛出异常。一个无限 while 循环保持程序运行，直到用户在键盘上输入“4”，此时会执行 break 关键字。

## Java 秒表

以下程序充当一个简单的秒表。您输入一个秒数让它倒计时，然后它会通知您时间到了。这个程序代表了 Java 中线程的一个非常基本的实现，由一个主线程组成。

```
import java.util.Scanner;
public class Counter {
public static void main(String[] args) {
int counter1=0;
Scanner keyscanner1 = new Scanner(System.in);
// 当 counter1 等于零时循环
while(counter1  0) {
System.out.println("剩余时间：" + counter1 + " 秒");
try
{ Thread.sleep(1000); }
catch(InterruptedException ex)
{ Thread.currentThread().interrupt(); }
--counter1;
} // 第二个循环结束
System.out.println("全部完成！");
}
}
清单 8-9
一个 Java 秒表程序的清单
```

清单 8-9 有两个 while 循环。第一个循环用于接收用户输入并确保其符合特定规则。主要是，输入只能由正数组成。如果输入了字母或其他非数字字符，程序会抛出一个异常，准确来说是 *NumberFormatException*。此外，第一个循环拒绝接受负数，并在必要时显示相关消息。

在第二个循环中，我们使用了一行代码 *Thread.sleep(1000)* 来实现所需的延迟时间。请注意，要在 Java 中使用 sleep 方法，您需要检查其异常。在清单 8-9 中，我们为此目的使用了经典的 try-catch 块。



## 结语

完成本章后，你将重温 Python、C# 和 Java 在以下方面的知识：

*   数据结构、程序流程和基本文件操作
*   基础线程技术
*   使用 try-catch 块进行异常处理
*   函数的定义与访问

第 9 章的代码量会少很多，主要围绕一些精美的图表展开，即使用*统一建模语言 (UML)* 创建的图表。

