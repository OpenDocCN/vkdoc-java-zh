# 2. 基类

在本章中，你将回顾 MmgBase API 的基类：

*   MmgObj
*   MmgColor
*   MmgRect
*   MmgFont
*   MmgSound
*   MmgPen
*   MmgVector2
*   MmgBmp

类将按照以下标准审查步骤进行审查。如果某个类在特定步骤中没有需要审查的内容，则该步骤将在审查过程中被省略：

*   静态类成员
*   枚举
*   类字段
*   支持方法详情
*   主方法详情

学习本书提供的游戏引擎软件的最佳方式是跟随 Java 或 C# 进行实践。然而，C# 中的行号与本文中审查的 Java 代码不会完全匹配。建议你先跟随 Java 学习以理解代码，然后再查看 C# 版本，这样你就能了解如何从 API 类中抽象出框架级别的类，以及代码的哪些部分是以不同但功能等效的方式实现的。

## 基类：MmgObj

`MmgObj` 类是游戏引擎中所有可绘制对象的基础。你在屏幕上看到的一切都以某种方式实现了这个类。这是强大的游戏创建工具中的一个常见主题。你会发现，在任何游戏引擎中，都有一组类构成了引擎其余部分所依赖的基础。在许多情况下，有一个类对于游戏引擎的渲染例程至关重要。这就是那个类。它包含了游戏引擎在屏幕上绘制对象所需的主要属性集，并构成了 API 中所有可绘制对象的基础。

### 类字段

我们将审查的第一组类字段如下所示。

```
public MmgVector2 pos;
public int w;
public int h;
public boolean isVisible;
public MmgColor color;
代码清单 2-1
MmgObj 类字段 1
```

因为我们正在审查一个 2D 游戏引擎的基类，所以我们应该期望看到有助于我们在屏幕上绘制 2D 图像的类字段。在审查 API 中的不同类时，请牢记这个概念。将我们审查的类视为模型，并尝试想出不同的方法将它们组合在一起来创建游戏的各个方面。

`pos` 字段是 `MmgVector2` 类的一个实例，用于存储 `MmgObj` 在屏幕上的当前位置。为了在 2D 空间中做到这一点，我们需要跟踪 X 和 Y 坐标，这正是 `MmgVector2` 类帮助我们做到的。`w` 和 `h` 类字段用于定义 `MmgObj` 的尺寸，即宽度和高度。这定义了一个矩形，该 `MmgObj` 实例在屏幕上绘制时会使用这个矩形。

请注意，仅凭三个类字段，我们就已经涵盖了几乎所有 2D 绘制的核心需求。当然，我们还没有讨论形状和图像，但所有这些事物至少有两个共同属性：位置和尺寸。下一个类字段 `isVisible` 用于控制此 `MmgObj` 是否绘制在屏幕上。`color` 字段是 `MmgColor` 类的一个实例，用于表示此对象的颜色，尽管并非所有对象在绘制时都会使用颜色属性。

这里我想提一个设计要点。请注意，我们本可以直接在 API 中使用框架类。为什么要引入一个 API 级别的类来处理它呢？通过使用 API 级别的类来包装框架级别的类，我们引入了一个抽象层，这使我们能够转换框架类的特性，使其更好地与 API 对齐。

```
private String version = "1.0.8";
public boolean hasParent;
public MmgObj parent;
public String name;
public String mmgUid;
代码清单 2-2
MmgObj 类字段 2
```

上面列出的下一组类字段以一个用于存储版本号的私有字符串开始。该版本号代表当前的 API 版本代码。接下来的两个类字段 `hasParent` 和 `parent` 用于跟踪此 `MmgObj` 实例是否属于另一个 `MmgObj` 实例，如果是，则属于哪一个。

`name` 字段用于为此 `MmgObj` 实例赋予一个唯一名称，可用于区分对象或对象类型。最后，`mmgUid` 是一个可用于存储唯一标识符的类字段。同样，这在跟踪和区分游戏对象时会很有帮助。

### 支持方法详情

我们不会详细讨论简单的支持方法。我们只是想了解有哪些功能可用，以便我们能够形成对该类如何运作以及如何使用它的概念。要审查的第一组支持方法如下所示。

```
public String GetVersion() { ... }
public boolean GetIsVisible() { ... }
public void SetIsVisible(boolean bl) { ... }
public int GetWidth() { ... }
public void SetWidth(int W) { ... }
public int GetHeight() { ... }
public void SetHeight(int H) { ... }
public void SetPosition(MmgVector2 v) { ... }
public MmgVector2 GetPosition() { ... }
public MmgColor GetMmgColor() { ... }
public void SetMmgColor(MmgColor c) { ... }
代码清单 2-3
MmgObj 支持方法详情 1

请注意，你可以选择通过 get 和 set 类方法或直接通过类字段访问来访问类字段。在你确定 `MmgObj` 的实例没有任何需要使用类方法的高级特性时，你可以使用类字段并节省一些输入时间。总的来说，我建议尽可能使用类方法并遵循良好的类封装实践。

```
public void SetX(int inX) { ... }
public int GetX() { ... }
public void SetY(int inY) { ... }
public int GetY() { ... }
public String GetName() { ... }
public void SetName(String n) { ... }
public String GetId() { ... }
public void SetId(String i) { ... }
public boolean GetHasParent() { ... }
public void SetHasParent(boolean b) { ... }
public MmgObj GetParent() { ... }
public void SetParent(MmgObj o) { ... }
代码清单 2-4
MmgObj 支持方法详情 2

用于 `X` 和 `Y` 坐标的 get 和 set 方法用于直接与 X 和 Y 坐标值交互。如果你频繁更新 `MmgObj` 的位置，则不应使用这些方法，而应存储对通过调用 `GetPosition` 返回的位置向量的引用。这将为你节省许多方法调用，特别是当代码作为更新或绘制例程的一部分运行时。

我想讨论的最后两个方法用于管理对象父级成员关系。两个 `MmgObj` 实例可以通过使用 `parent` 和 `hasParent` 类字段相互关联。此功能在 API 中使用不多——主要用在作为子对象容器的类中。但是，你可以利用此功能在你的游戏对象之间创建不同的关系。需要注意的是，指示存在父级的类字段和实际的父级是独立工作的，因此必须小心地相应设置这两个类字段。



### 主要方法详解

我们将从以下集合中列出的方法开始，对 `MmgObj` 的主要方法进行回顾。

```
public MmgObj(MmgVector2 v2, int W, int H, boolean isv, MmgColor c, String n, String i) { ... }
public MmgObj(MmgObj obj) { ... }
public MmgObj Clone() { ... }
public MmgObj CloneTyped() { ... }
public void MmgDraw(MmgPen p) { ... }
public boolean MmgUpdate(int updateTick, long currentTimeMs, long msSinceLastFrame) { ... }
public String ApiToString() { ... }
public boolean ApiEquals(MmgObj obj) { ... }
代码清单 2-5
MmgObj 主要方法详解 1
```

我们将回顾的第一个 `MmgObj` 构造函数接收七个参数，并使用默认值或参数值将类字段设置为初始值。请注意，位置是通过使用传入的 `X` 和 `Y` 坐标作为参数实例化一个新的 `MmgVector2` 类实例来设置的。另外，请注意此构造函数将 `hasParent` 布尔值设置为 `false`，并将 `parent` 字段设置为 `null`。这是一个直接的构造函数。我们将回顾的下一个构造函数则略有不同。

第二个 `MmgObj` 构造函数接收一个 `MmgObj` 实例作为参数。这是一个专门的构造函数，它根据传入的对象创建一个新的类实例。几乎所有 API 对象都会被克隆，以防止新对象与其所基于的对象之间出现共享引用。

接下来要回顾的一组主要方法从克隆方法开始。其实现看起来有点奇怪，因为 `Clone` 和 `CloneTyped` 方法都返回一个新的 `MmgObj` 实例。这是一个设计决策，旨在使克隆功能在整个 API 中标准化。对于扩展了 `MmgObj` 类的类，`Clone` 方法返回一个新的 `MmgObj` 实例，而 `CloneTyped` 方法则返回扩展类的一个新实例。在这种情况下，类型化类也是 `MmgObj`，因此两个方法返回相同类型的对象。此处支持类型化克隆仅出于完整性考虑。

`MmgUpdate` 方法是游戏引擎绘制例程的一部分。绘制例程由一组标准方法组成，这些方法每秒被调用特定次数以维持所需的帧率。`updateTick` 参数是更新方法被调用次数的索引。换句话说，如果你以每秒 60 帧的速度运行游戏，那么 `updateTick` 参数大约每秒会增加 60。

下一个方法参数 `currentTimeMs` 是调用该方法时的时间（以毫秒为单位）。该方法的最后一个参数 `msSinceLastFrame` 是自上次更新方法调用以来经过的毫秒数。这些参数可用于支持游戏帧期间的计时控制。想想任何游戏中临时图像的变化。计时用于控制该行为。你可以通过存储事件的开始时间并在事件结束时进行测量来衡量持续时间。

你也可以在每次更新调用时累加 `msSinceLastFrame` 值，并用它来衡量一个事件花费了多少时间。下一个要回顾的方法 `MmgDraw` 是另一个绘制例程方法。此方法接收一个封装了框架绘制类的 `MmgPen` 对象。`MmgDraw` 方法在每个游戏帧由绘制例程调用，并且总是在更新方法调用之后立即调用。

`ApiToString` 方法是 API 约定，用于提供类的字符串表示形式。我们需要为 `MmgObj` 类回顾的最后一个主要方法是 `ApiEquals` 方法。同样，这是一个贯穿此 API 的约定，与 `ApiToString` 和克隆约定一起使用。此方法提供 API 级别的相等性测试。

### 演示：MmgObj 类

演示代码回顾将不包括对所使用的应用程序和游戏屏幕的详细回顾。关于此代码的详细回顾可以在本书的第二部分找到，其中涵盖了 MmgCore API。然而，我们将涵盖演示 `MmgObj` 类用法的示例游戏屏幕的相关代码块。我建议你运行示例应用程序，以查看你正在回顾的代码的实际运行情况。

```
01 public void LoadResources() {
02    MmgHelper.wr("ScreenTestMmgObj.LoadResources");
03    pause = true;
04    SetHeight(MmgScreenData.GetGameHeight());
05    SetWidth(MmgScreenData.GetGameWidth());
06    SetPosition(MmgScreenData.GetPosition());

08    title = MmgFontData.CreateDefaultBoldMmgFontLg();
09    title.SetText("");
10    MmgHelper.CenterHorAndTop(title);
11    title.SetY(title.GetY() + MmgHelper.ScaleValue(30));
12    AddObj(title);

14    int padding = MmgHelper.ScaleValue(20);
15    int objW = (GetWidth() - (padding * 4)) / 3;
16    int objH = MmgHelper.ScaleValue(150);
17    int objY = (GetHeight() - (objH + padding + MmgHelper.ScaleValue(10))) / 2 + GetY();
18    int objX = padding;

20    obj1 = new MmgObj();
21    obj1.SetPosition(objX, objY);
22    obj1.SetWidth(objW);
23    obj1.SetHeight(objH);
代码清单 2-6
MmgObj 类演示 1
```

示例游戏屏幕位于 MmgTestSpace 包（如果你使用 C#，则为命名空间）的 `ScreenTestMmgObj` 类中。请注意，方法行号是相对于该方法而言的，而不是整个文件。`LoadResources` 方法被所有示例游戏屏幕使用，负责初始化游戏屏幕使用的可绘制对象。在第 3 行，我们确保屏幕处于暂停状态，这样它就不会在未完全初始化时尝试绘制自身。屏幕可绘制空间的宽度、高度和位置是根据 `MmgScreenData` 类的计算值设置的。`MmgScreenData` 类根据窗口尺寸和所需的游戏面板尺寸确定适当的缩放比例，第 4–6 行。

此游戏屏幕的 `title` 在第 8–12 行设置。请注意，标题的字体由 `MmgFontData` 类维护。此类与 `MmgScreenData` 类类似，它根据屏幕当前的缩放设置处理字体大小的缩放。请注意缩放功能。这个 2D 游戏 API 旨在某些方面自动调整大小，其中两个方面是屏幕尺寸和字体大小。

文本在第 9 行设置，在第 10 行我们看到使用了 `MmgHelper` 类。到目前为止，我们已经看到许多静态支持类派上了用场。`MmgHelper` 类是另一个方便的支持类，它方便地提供了对对象定位方法的访问。接下来，在第 11 行，我们稍微调整了定位，将标题向下移动了缩放后的 30 像素。`ScaleValue` 方法是一个易于访问的静态方法，它会根据屏幕的缩放设置来缩放一个数值。

每个字面距离、位置或大小值都应通过 `MmgHelper` 类的 `ScaleValue` 方法传递。这将确保你的游戏可以在不同的分辨率下运行，并且所有定位、字体和屏幕尺寸都会自动调整以适应当前分辨率。该代码块的最后一行，第 12 行，将 `title` 的 `MmgFont` 实例添加到屏幕的可绘制对象集合中。因为我们为此游戏屏幕扩展了 `MmgGameScreen` 类，所以我们拥有该类的一些内置功能。



接下来，在第 14 至 18 行，我们准备了要在当前屏幕上显示的`MmgObj`对象的尺寸。在第 14 行，我们设置了内边距；同样，我们确保对数值进行了缩放。第 15 行将`MmgObj`的期望宽度存储在`objW`变量中。初始化它的公式简单地计算了三个由内边距值分隔并居中于屏幕的方框的宽度。在第 16 行，期望高度`objH`被设置为`ScaleValue`的 150。然后在第 17 和 18 行，计算了`MmgObj`实例的`Y`坐标。居中的高度考虑了内边距和所用标签字体的高度。

最后，`Y`坐标增加了屏幕的 Y 偏移量。这样做是为了防止屏幕的`Y`坐标不为零。变量`objX`被设置为内边距变量的值，因为当`MmgObj`在屏幕上定位时，这将是`X`坐标的起始值。在第 20 至 23 行的代码块中，我们实例化了一个新的`MmgObj`并设置了它的位置和大小。

请注意，我们没有通过`AddObj`方法将`MmgObj`实例添加到屏幕的可绘制对象集合中。你能猜到为什么吗？这是因为`MmgObj`类默认没有任何可绘制的内容。我们通过让`MmgDraw`方法在示例游戏屏幕中显式绘制对象实例来解决这个问题，该方法直接调用`MmgPen`类的`DrawRect`方法，该方法接受一个`MmgObj`参数，并使用它在屏幕上绘制一个与对象位置和大小相同的矩形。

## 基类：MmgColor

`MmgColor`类用于封装框架的颜色类，并为 API 提供颜色支持。这是游戏引擎中非常常见的设置，你经常会看到对颜色、位置和大小进行建模的集中式类。遵循使用便捷静态类方法的主题，`MmgColor`类提供了许多此类方法以便快速访问颜色。

### 静态类成员

这里列出的`MmgColor`类的静态类方法演示了使用四种不同技术创建新的`MmgColor`实例。

```
public static MmgColor GetYellow() { return new MmgColor(Color.YELLOW); }
public static MmgColor GetLimeGreen() { return new MmgColor(Color.decode("#DAF7A6")); }
public static MmgColor GetDecodedColor(String htmlColor){return new MmgColor(Color.decode(htmlColor));}
public static MmgColor GetTransparent() { return new MmgColor(new Color(0f, 0f, 0f, 1f)); }
清单 2-7
MmgColor 静态类成员 1
```

上面列出的第一个方法演示了从框架颜色类中可用的静态颜色创建新的`MmgColor`实例。第二个方法`GetLimeGreen`演示了从一组 HTML 颜色代码创建新的`MmgColor`实例。

第三，我们有`GetDecodeColor`方法，这是一个从用户指定的 HTML 颜色代码创建新的`MmgColor`实例的例子。最后，我们有一个静态类方法，它从一组四个浮点数创建新的`MmgColor`实例。`GetTransparent`方法通过将红色、绿色、蓝色和 Alpha 通道设置为 0 来指定透明颜色。

### 类字段

`MmgColor`类只有一个我们需要关注的字段`c`。该字段是框架颜色类的一个实例。它被`MmgColor`类封装，为 API 提供了一种使用颜色的标准方式。

### 支持方法详情

在`MmgColor`类中只有两个支持方法需要回顾。类支持方法`GetColor`和`SetColor`只是颜色类`c`字段的简单获取和设置方法。

### 主要方法详情

我们将从下面列出的三个构造函数开始回顾主要方法。它们演示了使用不同参数创建新的`MmgColor`类。

```
public MmgColor() { ... }
public MmgColor(MmgColor obj){ ... }
public MmgColor(Color C) { ... }
public MmgColor Clone() { ... }
public String ApiToString() { ... }
public boolean ApiEquals(MmgColor obj) { ... }
清单 2-8
MmgColor 主要方法详情 1
```

第一个类构造函数不接受任何参数，默认颜色为白色。列出的第二个类构造函数接受一个`MmgColor`类实例作为参数，并使用其存储的颜色来创建新的`MmgColor`实例。上面列出的第三个也是最后一个构造函数接受一个框架颜色实例作为参数。然后使用这个`color`对象来创建新的`MmgColor`实例。

下一个要回顾的主要方法是`Clone`方法。请注意，`MmgColor`类并未扩展`MmgObj`类，但克隆该类的能力是可用的。`Clone`方法非常直接。它使用我们已经回顾过的专用构造函数，用相同的颜色创建`MmgColor`类的新实例。最后，`ApiEquals`方法提供了一种在 API 级别测试相等性的方式，而`ApiToString`方法用于创建对象实例的字符串表示。



### 演示：MmgColor 类

在本演示中，我们将以 `ScreenTestMmgColor` 屏幕为基础，展示 `MmgColor` 类的用法。你可以在 MmgTestSpace 包（如果使用 C# 则对应命名空间）中找到此类。由于 `LoadResources` 方法中存在重复代码，我将在以下代码块中仅列出相关行。

```
01 public void LoadResources() {
02     MmgHelper.wr("ScreenTestMmgColor.LoadResources");
03     pause = true;
04     SetHeight(MmgScreenData.GetGameHeight());
05     SetWidth(MmgScreenData.GetGameWidth());
06     SetPosition(MmgScreenData.GetPosition());

08     title = MmgFontData.CreateDefaultBoldMmgFontLg();
09     title.SetText("");
10     MmgHelper.CenterHorAndTop(title);
11     title.SetY(title.GetY() + MmgHelper.ScaleValue(30));
12     AddObj(title);

14     int yDiff = MmgHelper.ScaleValue(40);
15     int yStrt = GetY() + MmgHelper.ScaleValue(140);
16     int xLeft = MmgHelper.ScaleValue(200);
17     int i = 0;

19     color1Label = MmgFontData.CreateDefaultBoldMmgFontLg();
20     color1Label.SetMmgColor(MmgColor.GetBlueGray());
21     color1Label.SetText("Color: BlueGray");
22     color1Label.SetX(xLeft);
23     color1Label.SetY(yStrt + (yDiff * i));
24     AddObj(color1Label);
25     i++;
清单 2-9
MmgColor 类演示 1
```

`LoadResources` 方法的开头与之前的示例游戏屏幕非常相似。它会打印一条调试信息，指明正在加载资源的屏幕，将 `pause` 标志设为 true，并根据 `MmgScreenData` 类的值设置屏幕的尺寸和位置。你会注意到，示例应用程序中所有 `LoadResources` 方法的结构都相同，并且包含非常相似的代码行。

这是一个良好的编码习惯。相似的代码更易于阅读和理解。请将注意力集中到前面代码块中的第 8–12 行，你会看到标准的 `title` 初始化代码，它针对此特定屏幕进行了定制，但与所有示例游戏屏幕类似。请注意，`title` 对象是通过 `AddObj` 方法添加到屏幕中的。如果你查看第 14–17 行的代码，会看到定位变量的初始化。注意，所有字面数值都通过 `ScaleValue` 方法进行处理。这确保了定位值会随屏幕缩放。

由于所有 `MmgColor` 示例都非常相似，我们只从第 19 行开始审查一个代码块。我们使用 `MmgFontData` 类设置字体，该类会根据当前屏幕缩放比例处理字体的缩放。在第 20 行，可以看到设置字体颜色的实际示例。这里，我们使用了 `MmgColor` 类的静态辅助方法，具体来说是 `GetBlueGray` 方法。

字体条目将使用此颜色绘制在屏幕上。在第 21 行，我们将标签字体的文本设置为所使用的颜色名称。字体条目的定位基于我们之前提到的局部变量进行计算。第 24 行是必要的，用于将字体添加到游戏屏幕的可绘制对象列表中；最后，我们递增局部变量 `i`，以便下一个字体条目绘制在当前条目下方。请务必使用提供的示例应用程序查看此示例游戏屏幕。

## 基类：MmgRect

`MmgRect` 类用于表示 MmgBase API 中的一个简单矩形。该矩形类可以绘制到屏幕上以显示大小和位置，可用于碰撞检测等计算，也可用作 UI 元素，例如边框或分隔线。

### 静态类成员

`MmgRect` 类有一个静态类成员，即 `GetUnitRect` 方法。`GetUnitRect` 方法是一种创建标准化 `MmgRect` 实例的便捷方式。该方法创建一个新的 `MmgRect` 实例，其位置为 (0,0)，宽度和高度均为 1。

### 类字段

`MmgRect` 类只有一个我们需要介绍的类字段。`rect` 字段是框架矩形类的一个实例。`MmgRect` 类封装了此框架，并为 API 提供标准化的矩形支持。

### 支持方法详情

首先需要查看的支持方法如下。

```
public int GetLeft () { ... }
public int GetTop() { ... }
public int GetRight() { return ( ... }
public int GetBottom() { ... }
public int GetWidth() { ... }
public void SetWidth(int w){ ... }
public int GetHeight() { ... }
public void SetHeight(int h){ ... }
public Rectangle GetRect() { ... }
public void SetRect(Rectangle r) { ... }
清单 2-10
MmgRect 支持方法详情 1
```

上面列出的方法是 get 和 set 方法，允许访问其关联的类字段。该集合中的最后两个方法允许访问框架的矩形类。虽然我们希望尽可能限制与框架类的交互，将它们集中封装在类中，但我们不能忽视我们正在使用它们，并且确实需要不时地访问它们。考虑到这一点，你会注意到我们在合理的地方提供了对框架类的访问。



### 主要方法详情

该类的主要方法首先回顾了一些可用的构造函数。

```
public MmgRect() { ... }
public MmgRect(MmgRect obj) { ... }
public MmgRect(int left, int top, int bottom, int right) { ... }
代码清单 2-11
MmgRect 主要方法详情 1
```

列出的第一个构造函数不接受任何参数，并创建一个新的 `MmgRect` 实例，其高度和宽度设置为 1，并位于 (0,0) 位置。第二个构造函数是一个专用构造函数，它根据现有实例创建一个新的 `MmgRect` 实例。前面列出的最后一个构造函数接收更细粒度的数据，即 left、top、bottom、right 值，并使用它们创建一个新的 `MmgRect` 对象实例。

```
public void ShiftRect(int shiftLeftRight, int shiftUpDown) { ... }
public MmgRect ToShiftedRect(int shiftLeftRight, int shiftUpDown) { ... }
public MmgRect Clone() { ... }
代码清单 2-12
MmgRect 类主要方法详情 2
```

接下来我们将介绍的两个主要方法可以帮助你以不同方式移动矩形对象。`ShiftRect` 方法根据方法参数中指定的数量，在左右和上下方向上移动当前对象实例。下一个主要方法 `ToShiftedRect` 类似，但它不会改变当前对象实例。相反，它会根据方法参数指定的偏移位置创建并返回一个新的矩形。当你想要稍微调整一个矩形或基于现有实例创建一个新矩形时，这些方法非常方便。

这组方法中的最后一个是 `Clone` 方法。我们之前遇到过这个方法。MmgBase API 在所有有意义的类中都支持克隆功能。所有克隆方法都使用了专用的类构造函数。

```
public int GetDiffX(MmgRect inRect, int direction, boolean opposite, boolean left2right) { ... }
public int GetDiffX(int x, int direction, boolean opposite) { ... }
public int GetDiffY(MmgRect inRect, int direction, boolean opposite, boolean left2right) { ... }
public int GetDiffY(int y, int direction, boolean opposite) { ... }
public String ApiToString() { ... }
public boolean ApiEquals(MmgRect obj) { ... }
代码清单 2-13
MmgRect 主要方法详情 3
```

`GetDiffX` 方法是一组主要方法，用于快速计算两个 `MmgRect` 对象之间的水平差异。使用第一个 `GetDiffX` 方法（即接受四个参数的条目）的参数，你可以使用左侧或右侧坐标来比较矩形。你还可以指定在比较中首先使用哪个对象以及比较本身的方向。这为你提供了比较两个矩形的强大能力。这个方法需要一些经验。在将其用于你的游戏之前，请花些时间熟悉它。

第二个 `GetDiffX` 方法（即接受三个参数的条目）可用于将当前矩形与一个 X 坐标进行比较。你可以指定要比较的矩形坐标以及方向（左或右）。你还可以通过使用 `opposite` 参数来覆盖实际的比较。同样，这个方法需要一些经验。在将其用于你的游戏之前，请给自己一些时间练习。

接下来我想介绍的两个主要方法是 `GetDiffY` 方法。它们与我们刚刚回顾的方法非常相似。它们执行相同的操作，只是它们被设计用于处理 Y 坐标以及上下方向。由于它们非常相似，我在此不再赘述，但我建议你花时间仔细查看它们，并确保理解它们的工作原理。

关于 `MmgRect` 类，最后要回顾的两个主要方法是 `ApiToString` 和 `ApiEquals` 方法。`ApiToString` 方法用于创建 `MmgRect` 类的字符串表示形式。这是一种用于将类实例表示为字符串的 API 级别方法。`ApiEquals` 方法是一种 API 级别的比较方法。它将两个 `MmgRect` 实例具有相同位置和尺寸定义为相等。

### 演示：MmgRect 类

在本节中，我们将通过查看 MmgTestSpace 包（如果你使用 C# 则是命名空间）中 `ScreenTestMmgRect` 类的几行代码来演示 `MmgRect` 类的使用。到目前为止，我们已经看过几次 `LoadResources` 方法的回顾，因此我们将跳过一些通用代码，专注于该类特有的代码行。请务必在示例应用程序中查看此示例屏幕。

```
01     rect1 = new MmgRect(MmgHelper.ScaleValue(100), MmgHelper.ScaleValue(190), MmgHelper.ScaleValue(190) + GetHeight()/4, GetWidth() - MmgHelper.ScaleValue(100));
02     rect2 = new MmgRect(MmgHelper.ScaleValue(100), MmgHelper.ScaleValue(210) + GetHeight()/4, MmgHelper.ScaleValue(210) + GetHeight()/4 + GetHeight()/4, GetWidth() - MmgHelper.ScaleValue(100));
03     rect3 = new MmgRect(MmgHelper.ScaleValue(90), MmgHelper.ScaleValue(180), MmgHelper.ScaleValue(90) + GetHeight()/2 + GetHeight()/4 + MmgHelper.ScaleValue(28), GetWidth() - MmgHelper.ScaleValue(90));
代码清单 2-14
MmgRect 类演示 1
```

在此示例屏幕的 `LoadResources` 方法中，请将注意力集中在第 1 到第 3 行。这些行初始化并配置了三个 `MmgRect` 实例。请记住，`MmgRect` 类并未继承 `MmgObj` 类。这意味着 `MmgRect` 不能直接由游戏引擎的绘制例程绘制。但是，`MmgPen` 类中支持绘制 `MmgRect` 实例。通过这种方式，`MmgRect` 类介于计算和渲染之间。

## 基类：MmgFont

`MmgFont` 类用于封装框架的字体类。字体主要通过 `MmgFontData` 类的便捷静态方法创建。应始终优先使用 `MmgFontData` 类，因为它会根据屏幕的当前缩放比例（如果有）自动缩放字体大小。这有助于确保游戏可以放大或缩小尺寸，同时仍能保持游戏屏幕对象（包括 `MmgFont` 对象）的位置和大小。

`MmgFont` 类是我们遇到的第一个继承自 `MmgObj` 类的类。因此，它内置了对位置和尺寸的支持，并且在接入游戏引擎的绘制例程时，会接收更新和绘制方法的调用。另请注意，由于严格的 API 设计模式，`MmgFont` 类支持克隆、类型化克隆和比较功能。这还不错。

### 枚举

`MmgFont` 类有一个相关的枚举需要我们回顾。`FontType` 枚举被 `MmgFont` 类用于描述该类所代表的字体样式。例如，如果加载了粗体字体，则将使用粗体 `FontType`。需要注意的是，字体类型并不驱动字体的渲染方式；它仅仅描述所加载的字体。



### 类字段

`MmgFont` 类中供我们查看的字段列在下方。

```
private Font font;
private String text;
private final FontRenderContext frc;
private int fontSize = -1;
private FontType fontType = FontType.NONE;
清单 2-15
MmgFont 类字段 1
```

上面列出的第一个字段是框架字体类。在 C# 版本的 `MmgFont` 中，框架字体类是 `SpriteFont` 类。`MmgFont` 类封装了底层框架类，并为 MmgBase API 提供了标准功能。`text` 类字段保存了将由 `MmgFont` 实例显示的文本。接下来列出的字段是一个框架类，用于描述渲染字体的内部方法。这可以用来获取适合渲染字体的矩形尺寸。准确了解字体渲染字符串的尺寸，使我们能够将文本正确定位在屏幕上。

花点时间思考一下 C# 实现和 `SpriteFont` 框架类。为什么在 `MmgFont` 类和 API 方面，一切仍然以相同的方式工作？这是因为 API 使用的字体功能是由 `MmgFont` 类定义的，并且在游戏引擎的任何移植版本中都是相同的。`MmgFont` 类的职责是使用可用的框架类提供其类定义的功能。剩下的两个类字段 `fontSize` 和 `fontType` 是描述性字段，应准确反映该类加载的字体类型。

### 支持方法详情

第一组供我们查看的支持方法列在下方。

```
public String GetText() { ... }
public void SetText(String s) { ... }
public FontType GetFontType() { ... }
public void SetFontType(FontType ft) { ... }
public void SetFontSize(int sz) { ... }
public int GetFontSize() { ... }
public void SetFont(Font tf) { ... }
public Font GetFont() { ... }
清单 2-16
MmgFont 支持方法详情 1

```

我们将讨论的前两个支持方法是 `text` 类字段的 get 和 set 方法。`GetText` 方法仅返回文本值。然而，`SetText` 方法不仅更新文本，还会更新字体的尺寸。尺寸计算的结果相当准确，但你会注意到定位字体时可能会感觉有点别扭。这是因为字体实际上是从指定位置的垂直中心开始绘制的。在游戏屏幕上处理字体时请考虑到这一点。

`SetFontSize` 方法与我们目前看到的一些支持方法略有不同。它会在调用时调整字体大小。相应地，`GetFontSize` 方法从实际字体中返回大小值。`fontSize` 字段会随字体大小的调整而更新。该字段在设置时会覆盖字体的大小。所有重要的类字段都有 get 和 set 方法。请确保熟悉它们。

### 主要方法详情

首先，我们将查看类的构造函数。实际上，构造函数比我们这里列出的要多几个；但在很多方面它们是多余的，因此我们只关注列出的这些。

```
public MmgFont() { ... }
public MmgFont(MmgObj obj) { ... }
public MmgFont(Font tf, int fontSize, FontType fontType) { ... }
public MmgFont(MmgFont obj) { ... }
public MmgObj Clone() { ... }
public MmgFont CloneTyped() { ... }
public void MmgDraw(MmgPen p) { ... }
public boolean ApiEquals(MmgFont obj) { ... }
清单 2-17
MmgFont 主要方法详情 1
```

我想讨论的第一个构造函数接受三个参数：一个字体、一个字体大小和一个字体类型。该构造函数使用默认值或参数值初始化每个类字段。请注意，字体大小参数并不设置字体大小，它代表字体大小。上面列出的另一个构造函数是类克隆方法使用的专用构造函数。它接受一个 `MmgFont` 对象作为参数。

我们之前遇到过这种类型的构造函数实现。这是贯穿 MmgBase API 的一种强化设计模式，与克隆紧密相关。在大多数情况下，任何属于其他 API 类的类字段都需要被克隆，以防止新对象实例与其所基于的实例之间共享引用。所有基本数据类型都可以直接设置。

接下来要讨论的两个主要方法是克隆方法。你会反复看到这两个特定的类方法：`Clone` 和 `CloneTyped`。请注意，我们通过一致地实现某些类功能，在 API 层面创建了可靠的能力。我们本可以选择使用接口来实现相同的功能。那会创建更结构化的实现，但在构建游戏引擎时，我们更看重灵活性而非僵化的结构。

当对象通过传递给 `AddObj` 方法而成为游戏屏幕可绘制对象列表的一部分时，游戏引擎的绘制例程会调用 `MmgDraw` 方法。你也可以通过直接在 `MmgDraw` 方法中添加代码来绘制自定义的对象组合。`ApiEquals` 方法我们之前见过。在这种情况下，如果两个对象具有相同的字体和文本，`ApiEquals` 方法返回 true。

### 演示：MmgFont 类

在本节中，我们将通过查看 MmgTestSpace 包中 `ScreenTestMmgFont` 类的特定方法来演示 `MmgFont` 类的使用。此屏幕上显示了许多字体示例；我们只查看其中的一部分。请务必运行示例应用程序并实际查看这个示例游戏屏幕。

```
01     fontBoldLg = MmgFontData.CreateDefaultBoldMmgFontLg();
02     fontBoldLg.SetText("Font Bold Large");
03     fontBoldLg.SetY(GetY() + MmgHelper.ScaleValue(15) + (y * 1));
04     fontBoldLg.SetX(y);
05     AddObj(fontBoldLg);

07     fontBoldMd = MmgFontData.CreateDefaultBoldMmgFont(title.GetFontSize() - MmgHelper.ScaleValue(2));
08     fontBoldMd.SetText("Font Bold Medium");
09     fontBoldMd.SetY(GetY() + MmgHelper.ScaleValue(15) + (y * 2));
10     fontBoldMd.SetX(y);
11     AddObj(fontBoldMd);

13     fontBoldSm = MmgFontData.CreateDefaultBoldMmgFontSm();
14     fontBoldSm.SetText("Font Bold Small");
15     fontBoldSm.SetY(GetY() + MmgHelper.ScaleValue(15) + (y * 3));
16     fontBoldSm.SetX(y);
17     AddObj(fontBoldSm);
清单 2-18
MmgFont 类演示 1
```

我们的回顾从第 1 行的代码开始。这是一个加载缩放后的大号粗体字体并设置其位置的示例。请注意，在第 5 行，扩展了 `MmgObj` 类的 `MmgFont` 对象通过调用 `AddObj` 方法被添加到游戏屏幕的默认绘制例程中。第 7–11 行和第 13–17 行的其余代码块执行与我们刚刚回顾的代码相同的操作，只是使用了不同大小的字体。请务必花点时间查看不同的字体条目，这样你就能了解如何在游戏中创建和使用不同的字体。

## 基类：MmgSound

`MmgSound` 类用于封装框架的声音类，并为 MmgBase API 提供音频支持。`MmgSound` 类负责播放音效和背景音乐。它与其他类略有不同，因为它是一种资源，但不是可绘制资源。`MmgSound` 类被设计为使用静态的全局音量值。代码中还确保了每个 `MmgSound` 实例都有一个唯一的 ID 值。该类具有克隆、字符串表示和比较功能。



### 静态类成员

`MmgSound` 类有一些静态类成员供我们查看。

```
private static int ID_SRC = 0;
public static float MMG_SOUND_GLOBAL_VOLUME = 0.65f;public static float SetVolume(float f) { ... }
清单 2-19
MmgSound 静态类成员 1
```

第一个类字段是一个私有字段 `ID_SRC`，它作为类构造函数的一部分，用于为该类的实例设置唯一 ID。前面列出的下一个静态类字段是 `volume` 字段。它用于设置所有 `MmgSound` 实例的音量。在每次调用声音的播放方法时，在声音播放之前都会检查全局音量。

最后一个要查看的静态类成员是 `SetVolume` 方法。此方法用于在应用最小值和最大值约束的同时更新全局音量值。`SetVolume` 方法返回它刚刚更新的音量字段的值。这确保了方法调用者知晓应用于音量值的任何音量范围约束。

### 类字段

`MmgSound` 类有一些类字段，用于支持声音资源、音量和播放速率，供我们讨论。

```
private int id;
private String idStr;
private Clip sound;
private float usedVolume;
private float range;
清单 2-20
MmgSound 类字段 1
```

当创建 `MmgSound` 对象时，`id` 字段会被赋予一个唯一值。`idStr` 字段类似，只是它保存了 `id` 字段的字符串表示形式。`sound` 字段是框架声音类的一个实例。在游戏引擎的 Java 实现中，框架的声音类是 `Clip` 类。

`usedVolume` 类字段存储应用于声音的音量。如果在设置 `usedVolume` 之后静态类字段 `volume` 发生变化，声音的音量将在下次播放时自动更新。前面列出的字段集合中的最后一项是私有类字段 `range`。此字段用于在向当前声音应用新音量时描述音量范围。

```
private float gain;
private FloatControl vol;
private float currentVolume;
private float currentRate;
清单 2-21
MmgSound 类字段 2
```

私有类字段 `gain` 是一个计算出的调整值，用于改变声音的音量。`FloatControl` 实例 `vol` 是一个特定于 Java 的实现，用于设置声音的音量和速率。它是一个用于管理声音剪辑属性的控制类。

### 支持方法详解

让我们看看第一组支持方法。

```
public void ApplyVolume() { ... }
public void ApplyRate(float rate) { ... }
public float GetCurrentRate() { ... }
public float GetCurrentVolume() { ... }
public String GetIdStr() { ... }
清单 2-22
MmgSound 支持方法详解 1
```

`ApplyVolume` 支持方法用于更新 `sound` 和 `usedVolume` 类字段的音量。该方法使用 Java 框架类，根据可用的音量范围和全局音量值来设置声音剪辑的增益。`currentVolume` 类字段也由此方法更新，以反映所使用的音量。

下一个要查看的支持方法是 `ApplyRate` 方法。此方法用于设置声音的播放速率（如果支持的话）。采样率不像音量那样由全局静态值控制。每个 `MmgSound` 实例都可以有自己的播放速率。此方法还会更新 `currentRate` 字段。如您所见，`currentVolume` 和 `currentRate` 字段是描述性的，并且反映了声音对象所使用的值。

接下来的两个支持方法 `GetCurrentRate` 和 `GetCurrentVolume` 只是简单的 get 方法，返回其关联字段的当前值。这组方法中的最后一项是 `GetIdStr` 方法。此方法返回 `idStr` 字段的值，该字段是类 `id` 字段的字符串表示形式。

```
public int GetId() { ... }
private void SetId() { ... }
public Clip GetSound() { ... }
public void SetSound(Clip snd) { ... }
清单 2-23
MmgSound 支持方法详解 2
```

`GetId` 方法是典型的字段访问方法；然而，`SetId` 方法并非典型的 set 方法。`SetId` 方法是私有的，它在类内部用于为每个新类实例的 `id` 字段赋值。剩下的两个支持方法提供了对 `sound` 字段的访问，允许您设置或获取 `MmgSound` 类所包装的框架类。

### 主要方法详解

我们将从下面列出的构造函数开始对 `MmgSound` 类的主要方法进行回顾。

```
public MmgSound(Clip se) { ... }
public MmgSound(MmgSound obj) { ... }
public MmgSound Clone() { ... }
清单 2-24
MmgSound 主要方法详解 1
```

第一个构造函数接受一个框架声音类作为参数。在 Java 实现中，这是 `Clip` 类。在 C# 实现中，使用 `SoundEffect` 类。通过调用 `ApplyVolume` 方法，将全局音量应用于新的声音实例。构造函数还调用 `SetId` 方法，为新的类实例赋予唯一标识。列出的第二个构造函数是一个专用构造函数，它接受一个 `MmgSound` 实例作为参数，并利用它从现有对象创建一个新的、唯一的 `MmgSound` 对象。

```
public void Play() { ... }
public void Play(int loop, float rate) { ... }
public void Stop() { ... }
public String ApiToString() { ... }
public boolean ApiEquals(MmgSound obj) { ... }
清单 2-25
MmgSound 主要方法详解 2
```

该类提供了两种播放声音资源的方式。第一种方式（如上所列）不接受任何参数，是一种直接播放声音的方式。声音会被重置以进行播放，并且配置为不循环。此方法用于快速播放音效和不需要循环的较短声音。

第二个 `Play` 方法接受两个参数，可用于在播放声音之前设置循环次数和播放速率。此方法对于播放背景音乐等持续时间较长的声音非常方便。请注意，我们设置了声音播放的循环次数，这意味着声音将完整播放那么多次，然后停止。

最后一个要回顾的主要方法是 `Stop` 方法。在方法返回之前，声音会停止，并且音量会与全局值同步。请注意，两个播放方法和停止方法都确保声音对象的音量与全局音量匹配。这是有道理的，因为在游戏中，我们可能有许多不同的声音正在播放或可供播放。该类被配置为自动使所有声音对象实例与全局音量保持同步。

我想讨论的另外两个主要方法是 `ApiToString` 方法和 `ApiEquals` 方法。我们之前都见过这两个方法，但我将在此处再次简要介绍它们。`ApiToString` 方法用于创建 `MmgSound` 类的字符串表示形式。该方法在调试和日志记录场景中非常有用。`ApiEquals` 方法用于比较两个 `MmgSound` 实例并确定它们是否相等。



### 演示：MmgSound 类

在本节中，我们将通过查看 MmgTestSpace 包（在 C# 中为命名空间）中 `ScreenTestMmgSound` 类的特定方法，来演示 `MmgSound` 类的使用。此演示使用了输入事件，因此我在这里也会介绍一些输入处理的基础知识。此外，还会涉及一些声音播放，所以我们也会简单介绍一下资源加载。

```
01 sound1 = MmgHelper.GetBasicCachedSound("jump1.wav");
02 sound2 = MmgHelper.GetBasicCachedSound("jump2.wav");

04 soundLabel1 = MmgFontData.CreateDefaultBoldMmgFontLg();
05 soundLabel1.SetText("MmgSound 示例");
06 MmgHelper.CenterHorAndVert(soundLabel1);
07 soundLabel1.SetY(soundLabel1.GetY() - MmgHelper.ScaleValue(20));
08 AddObj(soundLabel1);

10 soundLabel2 = MmgFontData.CreateDefaultBoldMmgFontLg();
11 soundLabel2.SetText("按回车键或空格键播放声音");
12 MmgHelper.CenterHorAndVert(soundLabel2);
13 soundLabel2.SetY(soundLabel2.GetY() + MmgHelper.ScaleValue(20));
14 AddObj(soundLabel2);
清单 2-26
MmgSound 类演示 1
```

`LoadResuorces` 方法与我们之前看到的版本非常相似。在第 1 行和第 2 行，我们有一些资源准备代码，用于设置 `sound1` 和 `sound2` 类字段。请注意，这些字段是使用 `MmgHelper` 类的静态方法 `GetBasicCachedSound` 设置的。使用该 API 有几种方式可以将资源加载到游戏中。在大多数情况下，资源加载是由游戏实现的 MmgCore 库执行的高级操作。

然而，根据 MmgBase API 的结构，图像和声音可以根据游戏运行的位置以及代码和设置中游戏的名称自动加载。在本例中，`MmgTestScreens` 应用程序是从 MmgTestSpace 包中运行的。就 C# 实现而言，MmgGameApiCs 项目在 MmgTestSpace 命名空间中有一个类似的 `MmgTestScreens` 类。Java 项目具有以下目录结构：

```
MmgGameApiJava
|-> src (源代码目录)
|-> dist (可执行文件目录)
|-> cfg (资源和配置目录)
|-> playable
|-> MmgTestSpace
|-> jump2.wav
|-> auto_load
|-> jump1.wav
清单 2-27
MmgGameApiJava 项目目录结构
```

当游戏启动时，来自 MmgCore API（本质上是游戏引擎运行时）的代码会检查项目目录中是否存在某些默认文件夹。由于该项目的 Java 版本是在 NetBeans IDE 中开发的，我们将 IDE 项目配置为编译到 `dist` 文件夹。如果在与 `dist` 文件夹相同的目录中存在名为 `cfg` 的目录，则该目录会被视为配置目录，并会扫描其中的子文件夹和文件。

其中一个特殊的子文件夹是项目 `cfg` 目录中的 `playable` 文件夹。请注意其中的 `MmgTestSpace` 和 `auto_load` 子文件夹。如果找到一个与游戏 `GameSettings` 类中的静态字段 `NAME` 同名的子文件夹，则会扫描该文件夹中的音频文件。任何找到的音频文件都会按文件名加载到游戏引擎的音频资源缓存中。

为了演示播放声音，我们利用了由 `GamePanel` 类为当前游戏屏幕调用的输入处理方法。这将在 MmgCore API 审查期间更详细地介绍。当按下键盘按键时，会触发 `ProcessKeyClick` 方法。该方法会接收到一个字符和一个表示键盘按键的整数值。在本例中，按下回车键或空格键会播放声音。我们可以使用字符表示来检查是回车键“\n”还是空格键“ ”。当按下回车键时，播放 `sound1`；当按下空格键时，播放 `sound2`。

## 基类：MmgPen

`MmgPen` 类封装了框架的绘图类。在 Java/Swing 实现中，`MmgPen` 类封装了 `Graphics` 类。在 C#/MonoGame 实现中，`MmgPen` 类封装了 `SpriteBatch` 类。`MmgPen` 类为 MmgBase API 提供绘图支持，并允许将图像、文本和矩形绘制到屏幕上。

`MmgPen` 类被设计为灵活的，允许用户调用最适合特定情况的绘图方法。你会注意到，这个类没有提供我们见过的其他类所具有的克隆和比较功能。你认为这是为什么？这与 `MmgPen` 类的使用方式有关。它总是以数据驱动的方式使用，绘制指定的对象。因此，它几乎没有内部状态，也没有需要克隆的内容。因此，该类没有克隆方法。

### 静态类成员

我们要审查的第一个类字段 `FONT_NORMALIZE_POSITION` 是一个特殊标志，启用后会改变字体的渲染方式。如果该字段设置为 true，那么 `MmgPen` 类将对其位置的 X 和 Y 坐标调用 `NormalizeFontPosition` 方法。这允许开发者根据字体的类型和大小来调整字体的渲染位置。当你试图在不同平台的不同字体之间规范化字体的定位方式时，这会非常有用。

下一个条目 `ADV_RENDER_HINTS` 是一个布尔标志，在 Java 版本的游戏引擎中启用时，会强制游戏引擎配置多个渲染设置。C# 实现具有相同的功能，但无需运行任何代码。它开箱即用，适合游戏开发。这些提示提供了一些关于如何处理某些 2D 渲染事件（如缩放和旋转 2D 图像）的指示。剩下的静态字段 `TRANSPARENT` 是一种访问透明颜色值的便捷方式。这在某些游戏和某些绘图场景中会很有用。

```
public static Image CreateColorTile(int w, int h, MmgColor c) { ... }
public static Image RotateImageStatic(int width, int height, Image img, int angle, int originX, int originY) { ... }
public static Image ScaleImageStatic(Image img, MmgVector2 scale) { ... }
public static Image ScaleImageStatic(Image img, double scaleX, double scaleY) { ... }
清单 2-28
MmgPen 静态类成员 3
```

`CreateColorTile` 静态方法使用框架类创建一个具有指定尺寸的图像，即一个彩色方块。

`RotateImageStatic` 方法是一个静态方法，它按指定的角度（以度为单位）旋转图像。该方法仍然需要配置正确的图像尺寸和旋转原点。请注意，如果 `originX` 或 `originY` 设置为 –1，该方法会将旋转原点计算为图像的中心。在应用旋转变换的同时，原始图像的副本会被绘制到新的缓冲图像上。

第一个 `ScaleImageStatic` 方法有一个便捷的重载，它接受一个 `MmgVector2` 参数来定义水平和垂直缩放。第二个 `ScaleImageStatic` 方法接受三个参数：一个图像以及每个坐标（X 和 Y）的缩放值。新图像的尺寸基于作为参数传入的缩放值。



### 类字段

`MmgPen` 类有几个重要的类字段需要我们了解。让我们来看一些代码！

```
private Graphics pen;
private Color color;
private boolean cacheOn;
代码清单 2-29
MmgPen 类字段 1
```

这组字段中的第一个是 `Graphics` 类的一个实例。`Graphics` 对象 `pen` 是 `MmgPen` 类所封装的框架类。下一个字段 `color` 是用于表示颜色的框架类。请注意如何使用这个类字段；它可能并不反映画笔当前正在使用的颜色。在回顾 `MmgPen` 类的方法时，我会更详细地介绍这一点。

这组字段中的最后一个类字段背后有些故事。MmgBase API 曾一度专注于能够即时绘制缩放图像。为了加速这一过程，`MmgPen` 类添加了特殊支持，使得某些图像绘制方法能够缓存即时缩放或旋转后的图像，从而避免重复执行相同的变换操作。

`MmgBmp` 类中甚至有专门的支持来访问缩放和未缩放的图像尺寸。事实证明，对于 2D 游戏来说，这并不是最佳实践。如果你计划进行即时缩放和旋转，请考虑在资源加载期间预先设置好所需的图像。这样，它们就已经在内存中了，并且相关操作只执行一次。`cacheOn` 字段应保持为 `false`，除非你正在试验图像变换缓存。

### 支持方法详解

我们将要回顾的第一组支持方法是标准的 get 和 set 支持方法。

```
public boolean GetCacheOn() { ... }
public void SetCacheOn(boolean b) { ... }
public boolean IsEmptyColor(Color c) { ... }
public Graphics GetSpriteBatch() { ... }
public void SetSpriteBatch(Graphics sp) { ... }
public Graphics GetGraphics() { ... }
public void SetGraphics(Graphics sp) { ... }
public Color GetColor() { ... }
public void SetColor(Color c) { ... }
public Color GetGraphicsColor() { ... }
public void SetGraphicsColor(Color c) { ... }
代码清单 2-30
MmgPen 支持方法详解 2
```

首先要讨论的两个方法是 `cacheOn` 类字段的 get 和 set 方法。我们建议关闭 `MmgPen` 的缓存系统，除非你对 API 非常熟悉。下一个方法 `IsEmptyColor` 只是检查颜色参数是否为空，并返回一个布尔值指示结果。

接下来的两个支持方法 `GetSpriteBatch` 和 `SetSpriteBatch` 有一些历史渊源。这两个方法是在移植到微软的 XNA 框架期间引入 API 的。它们本质上与 `GetGraphics` 和 `SetGraphics` 方法相同，充当了精灵批处理方法的传递通道。它们的命名也是为了反映 API 的 Java 实现中的绘图类。`GetColor` 和 `SetColor` 方法允许你访问类的 `color` 字段。请注意，这些方法不会改变画笔的当前颜色。要做到这一点，你需要使用 `GetGraphicsColor` 和 `SetGraphicsColor` 方法。它们会直接调整画笔上的颜色。

```
public Image ScaleImage(Image img, double scaleX, double scaleY) { ... }
public Image RotateImage(int width, int height, Image img, int angle, int originX, int originY) { ... }
代码清单 2-31
MmgPen 支持方法详解 2
```

请注意，前面列出的两个支持方法充当了类静态方法的非静态传递通道。这样实现是为了方便，允许从非静态类方法访问缩放和旋转方法。我们要回顾的最后一组支持方法是绘图方法。实际的方法远不止这里列出的这些；但为了提高效率，我们不会逐一介绍每个方法，而是重点介绍几个关键示例。

```
public void DrawText(MmgFont f, MmgVector2 pos) { ... }
public void DrawBmpBasic(String idStr, MmgVector2 position) { ... }
public void DrawBmpFromCache(MmgBmp b) { ... }
public void DrawBmp(MmgBmp b) { ... }
public void DrawRect(MmgObj obj) { ... }
public void DrawRect(MmgRect r) { ... }
public void DrawRect(int x, int y, int w, int h) { ... }
代码清单 2-32
MmgPen 支持方法详解 3
```

前面列出的绘图方法组中的第一个方法是 `DrawText` 方法。这个方法有多个重载版本，但我们将重点介绍接受 `MmgFont` 实例和 `MmgVector2` 实例作为参数的版本。如果 `FONT_NORMALIZE_POSITION` 标志设置为 `true`，则使用调整后的位置绘制文本；否则，使用指定的坐标值绘制。该方法在返回前会重置画笔的原始颜色和字体。你应该养成这样做的习惯。

接下来要介绍的方法是 `DrawBmpBasic` 方法。这个方法被设计成一个简单的图像绘制方法，不执行任何变换或复杂逻辑。接下来，`DrawBmpFromCache` 方法是我们刚刚看过的 `DrawBmpBasic` 方法的一个传递通道。请注意，使用 `MmgBmp` 实例的 ID 字符串在资源缓存中定位所需的图像。

如果 `MmgBmp` 实例设置了正确的 ID 和位置，这个方法是从图像缓存中绘制图像的一种更简洁的方式。这组方法中的最后一个是通用的 `MmgBmp` 绘制方法 `DrawBmp`。请注意，此方法调用了 `DrawBmp` 类的一个更复杂的版本，该版本使用了诸如原点、缩放和旋转等渲染选项。

### 主要方法详解

我们将通过查看一些类构造函数来开始主要方法回顾部分。

```
public MmgPen(Graphics p) { ... }
public MmgPen(Graphics p, Color c) { ... }
代码清单 2-33
MmgPen 主要方法详解 1
```

我要回顾的两个构造函数都很直接。第一个构造函数接受一个 `Graphics` 对象作为参数，并用它来设置画笔类字段。前面列出的下一个构造函数接受一个 `Graphics` 对象和一个 `Color` 对象作为参数。请注意，颜色参数不用于更改图形对象的颜色字段。它仅用于更新 `MmgPen` 的颜色字段。在两个构造函数中，`cacheOn` 类字段都被设置为 `false`。请记住，除非我们明确想要试验该功能，否则我们不会激活它。



### 演示：MmgPen 类

并没有专门针对 `MmgPen` 类的示例游戏画面。不过，示例应用程序中的几乎每个游戏画面都以某种方式使用了 `MmgPen` 类。我们将通过查看一个 `MmgDraw` 方法来演示如何在实际游戏中使用 `MmgPen` 类。

```
01 public void MmgDraw(MmgPen p) {
02     if (pause == false && isVisible == true) {
03         if (background != null) {
04             background.MmgDraw(p);
05         }

07         if (header != null) {
08             header.MmgDraw(p);
09         }

11         if (footer != null) {
12             footer.MmgDraw(p);
13         }

15         if (message != null) {
16             message.MmgDraw(p);
17         }

19         if (objects != null) {
20             objects.MmgDraw(p);
21         }

23         if (menuOn == true) {
24             DrawMenu(p);
25         }

27         if (foreground != null) {
28             foreground.MmgDraw(p);
29         }
30     }
31 }
清单 2-34
MmgPen 类演示 1
```

上面列出的 `MmgDraw` 方法来自 MmgBase API 的 `MmgGameScreen` 类。当你在示例屏幕类中看到父类的 `MmgDraw` 方法被调用时，实际调用的就是这个方法。在 C# 版本中，该方法会调用基类的 `MmgDraw` 方法。这是内置于游戏引擎中的绘制例程的一个示例，负责在屏幕上绘制不同类型的对象。

这个特定的实现支持绘制背景图像、页眉、页脚、屏幕消息、一组可绘制对象、菜单和前景图像。`MmgGameScreen` 类的 `MmgDraw` 方法由源自 MmgCore API 中 `GamePanel` 类的游戏引擎绘制例程调用。

## 基类：MmgVector2

`MmgVector2` 类用于为游戏引擎绘制的对象建模位置信息。当然，向量类还有其他用途，但这是其主要用途。该类通常用于表示具有 X 和/或 Y 分量的数据。例如，`MmgVector2` 对象可用于保存 X、Y 缩放数据或 X、Y 偏移数据，最常见的用途是保存 X、Y 位置数据。

`MmgVector2` 类不扩展 `MmgObj` 类，因此它不属于 API 类的可绘制子集。但是，它确实以与之前看到的类相同的方式实现了克隆、比较和字符串表示。另一个需要注意的设计细节是，`MmgVector2` 类被设计为灵活的，允许对向量的 X 和 Y 分量使用浮点数、双精度浮点数或整数值。

### 静态类成员

有两个静态类方法我想快速回顾一下。第一个静态类成员是 `GetOriginVec` 方法。此方法用于返回一个新的、唯一的 `MmgVector2` 实例，该实例指向原点 (0,0)。类似地，下一个静态方法 `GetUnitVec` 返回一个新的、唯一的实例，该实例指向单位向量 (1,1)。

### 类字段

`MmgVector2` 类只有一个值得一提的类字段。你是不是希望所有类都这么简单？我们唯一需要审查的类字段是一个名为 `vec` 的双精度浮点数数组。该数组将初始化为长度为 2，表示 X 和 Y 分量。该类被设计为保存整数、浮点数或双精度浮点数对。在大多数情况下，并且出于所有与绘制相关的目的，使用整数值最有意义，因为屏幕坐标始终是整数值。

### 支持方法详解

`MmgVector2` 类有许多 get 和 set 支持方法。这些简单的方法非常重要，因为它们定义了与 `MmgVector2` 类的交互方式，该交互使用整数、浮点数或双精度浮点数。这为你决定如何使用该类提供了很大的灵活性。

```
public double[] GetVector() { ... }
public void SetVector(double[] v) { ... }
public int GetX() { ... }
public void SetX(int x) { ... }
public float GetXFloat() { ... }
public void SetX(float x) { ... }
public double GetXDouble() { ... }
public void SetX(double x) { ... }
清单 2-35
MmgVector2 支持方法详解 1
```

上面列出的前两个条目是该类向量数组的 get 和 set 方法。这让你可以使用双精度浮点数数组快速轻松地设置向量对象的值。接下来的六个方法都是 X 和 Y 分量值的 get 和 set 方法。在每种情况下，get 方法都设计为以特定的数据类型返回 X 分量值。该类支持 X 分量值的双精度浮点数、浮点数和整数表示。

类似地，你可以使用接受所需类型（双精度浮点数、浮点数或整数）参数的 set 方法版本来设置 X 分量值。这两种方法协同工作，创建了三种与 X 分量值交互的不同方式。下一组支持方法为 Y 分量值提供了类似的功能。

```
public int GetY() { ... }
public void SetY(int y { ... }
public float GetYFloat() { ... }
public void SetY(float y) { ... }
public double GetYDouble() { ... }
public void SetY(double y) { ... }
清单 2-36
MmgVector2 支持方法详解 2
```

至此，类审查的“支持方法详解”部分结束。接下来，我们将查看该类的一些主要方法。

### 主要方法详解

`MmgVector2` 类的这一部分以一组构造函数开始，这些构造函数展示了该类的灵活性。

```
public MmgVector2() { ... }
public MmgVector2(MmgVector2 v) { ... }
public MmgVector2(double[] v) { ... }
public MmgVector2(double x, double y) { ... }
public MmgVector2(float x, float y) { ... }
public MmgVector2(int x, int y) { ... }
public MmgVector2 Clone() { ... }
public MmgVector2 CloneFloat() { ... }
public MmgVector2 CloneDouble() { ... }
public MmgVector2 CloneInt() { ... }
清单 2-37
MmgVector2 主要方法详解 1
```

上面列出的构造函数演示了对使用整数、浮点数和双精度浮点数初始化类的支持。针对列出的每种情况都有一个构造函数，甚至还有一些未在此列出的构造函数。接下来我们要审查的一组主要方法是克隆方法。虽然每个克隆方法都返回一个 `MmgVector2` 实例，但每个方法都略有不同。例如，在所有类型显式克隆方法 `CloneInt`、`CloneFloat` 和 `CloneDouble` 中，都会使用转换为指定数据类型的值来创建一个新的对象实例。通用的 `Clone` 方法会创建一个新的对象实例，但使用分量值的数组条目来实现。

最后剩下要审查的一组主要方法包含 `ApiToString` 和 `ApiEquals` 方法。`ApiToString` 方法是一个 API 级别的方法，用于将给定的 `MmgVector2` 实例表示为字符串。它只是将 X 和 Y 分量值作为双精度浮点数打印在对象的字符串描述中。`ApiEquals` 方法类似于我们之前看到的 API 比较方法。它是 MmgBase API 用于比较类实例的 API 级别方法。

### 演示：MmgVector2 类

并没有专门针对 `MmgVector2` 类的示例游戏画面，但该类在 MmgTestSpace 库中的每一个示例游戏画面上都被使用。让我们看看使用 `MmgVector2` 类的一种方式。

```
1 pause = true;
2 SetHeight(MmgScreenData.GetGameHeight());
3 SetWidth(MmgScreenData.GetGameWidth());
4 SetPosition(MmgScreenData.GetPosition());
清单 2-38
MmgVector2 类演示 1
```

在 MmgTestSpace 库中任何游戏画面的 `LoadResources` 方法中，都可以找到上面列出的代码。第 4 行的 `SetPosition` 方法用于将游戏画面定位到 `MmgScreenData` 类指定的正确偏移量。偏移量信息存储为 `MmgVector2` 实例，因为它具有 X 和 Y 分量值。



## 基类：MmgBmp

`MmgBmp` 类是我们需要回顾的最后一个基类。它扩展了 `MmgObj` 类，并且是可绘制类子集的一个成员。每次你在屏幕上看到图像时，都会用到 `MmgBmp` 实例。`MmgBmp` 类有一些我特别想谈谈的设计细节。它有一个绘图模式特性。这个特性只应在你清楚自己在做什么时使用；否则，请使用默认模式。该特性的作用是控制 `MmgBmp` 类如何使用 `MmgPen` 类中的绘图方法。

基本的默认模式效率最高，并且使用的方法调用最少。完整模式效率略低于基本模式，但它支持更多的方法调用，因此支持更多的绘图功能。例如，在设置 `MmgLoadingBar` 类实例时，你需要提供一个用于填充加载条的背景图像。该图像必须将其绘图模式设置为完整模式，以便利用一些更高级的图像渲染技术。

最后，还有一种缓存模式。此模式利用图像资源缓存来获取对绘制在指定位置的图像的引用。`MmgBmp` 类还支持克隆、比较和字符串表示，正如我们在许多其他 API 类中看到的那样。

### 静态类成员

`MmgBmp` 类有一个静态类成员需要我们回顾。它类似于我们在 `MmgSound` 类中看到的一个类字段。你能猜到是哪个吗？猜不到？是 `ID_SRC` 字段。`ID_SRC` 静态类字段用于为每个新的 `MmgBmp` 实例分配一个唯一的 ID。每个新的 `MmgBmp` 实例的 `id` 字段都会被设置为 `ID_SRC` 静态类字段的值。然后 `ID_SRC` 字段会递增，以便为下一个对象实例做好准备。

### 枚举

`MmgBmp` 类有一个枚举需要介绍。`MmgBmpDrawMode` 枚举用于控制在调用类的 `MmgDraw` 方法时如何绘制 `MmgBmp` 类。在大多数情况下，最简单且最高效的策略是使用 `DRAW_BMP_BASIC` 绘图模式。

### 类字段

`MmgBmp` 类的字段封装了执行不同绘图操作（如缩放和旋转）所需的值。

```
private MmgVector2 origin;
private MmgVector2 scaling;
private MmgRect srcRect;
private MmgRect dstRect;
private Image b;
清单 2-39
MmgBmp 类字段 1
```

第一组需要回顾的类字段用于保存给定 `MmgBmp` 图像的绘图信息。需要指出的是，`MmgPen` 类在绘制图像时可能不会使用这些信息。这取决于用于绘制图像对象的方法。例如，使用默认绘图模式 `DRAW_BMP_BASIC` 会导致调用 `MmgPen` 类的 `DrawBmpBasic` 方法。

正如你在该方法中看到的，绘制图像时只考虑了位置信息。这意味着什么？这意味着 `MmgBmp` 类的设计注重简洁和效率。通常，我们不应该频繁地执行图像变换，因此 API 的设计预期是大多数图像绘制操作都是基本的。

这也意味着，如果你想执行更高级的图像绘制，例如考虑旋转和缩放等变换，你需要确保使用了正确的 `MmgPen` 绘图方法。在规划游戏时，请务必考虑使用更高级技术绘制图像的复杂性。再次强调，如果可能的话，在资源加载期间一次性执行图像变换，总比在多个游戏帧中执行要好。

关于前面列出的字段，`origin` 向量用于描述图像的旋转原点。`scaling` 向量用于描述 X 和 Y 方向的缩放值。接下来的两个类字段 `srcRect` 和 `dstRect` 用于描述源和目标绘图矩形。

```
private float rotation;
private String idStr;
private int id;
public MmgBmpDrawMode DRAW_MODE = MmgBmpDrawMode.DRAW_BMP_BASIC;
清单 2-40
MmgBmp 类字段 2
```

接下来要回顾的一组类字段以一些图像变换信息开始，即 `rotation` 角度。接下来的两个字段类似于我们在 `MmgSound` 类中看到的。`id` 和 `idStr` 类字段是在创建类实例时分配给它的唯一标识符。这些 ID 字段是缓存系统的一部分。在你对绘图例程有足够经验之前，我不建议调整此功能。最后，`DRAW_MODE` 字段用于调整 `MmgBmp` 类的 `MmgDraw` 方法如何与 `MmgPen` 参数交互，正如我们之前详细说明的那样。

### 支持方法详解

第一组支持方法以框架图像类的 get 和 set 方法开始。

```
public Image GetTexture2D() { ... }
public void SetTexture2D(Image d) { ... }
public Image GetImage() { ... }
public void SetImage(Image d) { ... }
清单 2-41
MmgBmp 支持方法详解 1
```

这里有一些来自该类原始 XNA 实现的遗留代码。你会注意到有两种方式可以与 `image` 类字段交互。`GetTexture2D` 和 `SetTexture2D` 这对方法指的是 C#/MonoGame 框架类 `Texture2D`，而 `GetImage` 和 `SetImage` 方法指的是 Java/Swing 框架类 `Image`。任一对方法都会更新类的图像字段。

```
public MmgRect GetSrcRect() { ... }
public void SetSrcRect(MmgRect r) { ... }
public MmgRect GetDstRect() { ... }
public void SetDstRect(MmgRect r) { ... }
public float GetRotation() { ... }
public void SetRotation(float r) { ... }
清单 2-42
MmgBmp 支持方法详解 2
```

我们需要回顾的第二组支持方法也是关键类字段的 get 和 set 方法。它们直接且简单。请仔细阅读并确保理解它们。

```
public MmgVector2 GetOrigin() { ... }
public void SetOrigin(MmgVector2 v) { ... }
public MmgVector2 GetScaling() { ... }
public void SetScaling(MmgVector2 v) { ... }
public int GetHeight() { ... }
public int GetWidth() { ... }
清单 2-43
MmgBmp 支持方法详解 3
```

前面列出的第三组也是最后一组支持方法允许访问原点和缩放向量。因为旋转可以围绕不同的点进行，从而改变最终的变换结果，所以你必须指定要用作旋转中心的坐标。`scaling` 字段是一个 `MmgVector2` 实例，它包含描述在 X 和 Y 方向上缩放图像多少的浮点值。

由于 `MmgBmp` 类支持即时变换，因此该类支持处理缩放后的图像尺寸。当处理作为绘图例程一部分而变换的图像时，事情可能会变得有点复杂。我强烈建议准备好图像，应用变换，然后保存结果图像的副本以备将来使用。



### 主要方法详情

我们要为 `MmgBmp` 类审查的第一组主要方法是一组类构造函数。可用的构造函数比我们这里将要审查的要多，所以请务必查看该类的代码，并熟悉你感兴趣的任何其他构造函数。

```
public MmgBmp(MmgObj obj) { ... }
public MmgBmp(MmgBmp obj) { ... }
public MmgBmp(Image t) { ... }
public MmgBmp(Image t, MmgRect Src, MmgRect Dst,
MmgVector2 Origin, MmgVector2 Scaling, float Rotation) { ... }
public MmgObj Clone() { ... }
public MmgBmp CloneTyped() { ... }
清单 2-44
MmgBmp 主要方法详情 1
```

我们将审查的第一个类构造函数列在前面的组中。它接受一个 `MmgObj` 作为参数，并用它来初始化一个空的 `MmgBmp` 实例。

这是一个极简构造函数的例子。由此构造函数创建的 `MmgBmp` 实例还不能直接使用。你至少需要设置一个图像并仔细检查其尺寸。下一个要审查的主要方法是一个专门的构造函数，它接受一个 `MmgBmp` 实例作为参数，并用它来创建该对象的一个新的、唯一的副本。请注意，每个构造函数中都调用了 `SetBmpId` 方法，以确保每个类实例都有唯一的 ID。

接下来要审查的一组主要方法包含两个类构造函数，它们接受框架类实例来设置图像和相关的类字段。让我们看看那个只接受一个 `Image` 实例作为参数的构造函数。在 C# 实现中，它将接受一个 `Texture2D` 实例作为参数。

此构造函数设置了一个基本的 `MmgBmp` 实现，以便与游戏引擎的绘制例程一起使用。请确保你审查并理解可供你使用的不同 `MmgBmp` 构造函数。接下来要审查的一组主要方法包括类的克隆方法。克隆 `MmgBmp` 实例的能力由 `Clone` 和 `CloneTyped` 这两个主要方法提供。

`Clone` 方法在返回对象实例之前对其进行强制类型转换，从而返回对 `MmgBmp` 类的超类（在 C# 中为基类）的引用。类似地，`CloneTyped` 方法返回一个当前类的克隆 `MmgBmp` 实例。请注意，这两个克隆方法都使用了我们之前审查过的专门构造函数来创建要返回的新类实例。

```
public boolean ApiEquals(MmgBmp obj) { ... }
public void MmgDraw(MmgPen p) { ... }
清单 2-45
MmgBmp 类方法详情 2
```

最后剩下的要审查的方法是 `ApiEquals` 方法和类的绘制方法 `MmgDraw`。`ApiEquals` 方法比较两个类实例的某些类属性，并判断这些类实例是否相等。最后一个要审查的主要方法是 `MmgDraw` 方法。我们之前见过这个方法；它被 `MmgPen` 类用作底层绘制例程的一部分。请注意，绘制模式控制使用哪个 `MmgPen` 绘制方法。这种功能的原因是为了效率。如果我们想绘制一个简单的 `MmgBmp` 实例，那么我们就不想操心配置缩放、旋转和原点字段。

在这种情况下，我们会将绘制模式设置为 `DRAW_BMP_BASIC`。这将强制进行高效、简单的 `MmgBmp` 渲染。对于需要旋转、缩放或源矩形和目标矩形的更复杂绘制，请使用 `DRAW_MODE_FULL` 设置。除非你是高级用户，否则应避免使用 `DRAW_BMP_BASIC_CACHE` 设置。

### 演示：MmgBmp 类

`MmgBmp` 类的示例屏幕可以在 MmgTestSpace 包（C# 中为命名空间）的 `ScreenTestMmgBmp` 类中找到。我有时会松散地将命名空间、包和 API 统称为库。我不会专注于游戏屏幕特定的代码，而是会尽量专注于使用 `MmgBmp` 类的代码。

```
14     bmpCache = MmgHelper.GetBasicCachedBmp("soldier_frame_1.png");
15     bmpCache.SetY(GetY() + MmgHelper.ScaleValue(90));
16     bmpCache.SetX(MmgHelper.ScaleValue(220));
17     AddObj(bmpCache);

19     bmpCacheLabel = MmgFontData.CreateDefaultBoldMmgFontLg();
20     bmpCacheLabel.SetText("MmgBmp From Auto Load Cache");
21     bmpCacheLabel.SetPosition(MmgHelper.ScaleValue(50), GetY() + MmgHelper.ScaleValue(70));
22     AddObj(bmpCacheLabel);

24     bmpFile = MmgHelper.GetBasicCachedBmp("../cfg/drawable/loading_bar.png", "loading_bar.png");
25     bmpFile.SetY(GetY() + MmgHelper.ScaleValue(90));
26     bmpFile.SetX(MmgHelper.ScaleValue(560));
27     AddObj(bmpFile);

29     bmpFileLabel = MmgFontData.CreateDefaultBoldMmgFontLg();
30     bmpFileLabel.SetText("MmgBmp From Path");
31     bmpFileLabel.SetPosition(MmgHelper.ScaleValue(545), GetY() + MmgHelper.ScaleValue(70));
32     AddObj(bmpFileLabel);

34     bmpCustomFill = MmgHelper.CreateFilledBmp(MmgHelper.ScaleValue(50), MmgHelper.ScaleValue(50), MmgColor.GetCalmBlue());
35     bmpCustomFill.SetY(GetY() + MmgHelper.ScaleValue(210));
36     bmpCustomFill.SetX(MmgHelper.ScaleValue(205));
37     AddObj(bmpCustomFill);

39     bmpCustomFillLabel = MmgFontData.CreateDefaultBoldMmgFontLg();
40     bmpCustomFillLabel.SetText("MmgBmp Created Custom with Fill");
41     bmpCustomFillLabel.SetPosition(MmgHelper.ScaleValue(45), GetY() + MmgHelper.ScaleValue(190));
42     AddObj(bmpCustomFillLabel);

44     bmpSet = MmgHelper.CreateDrawableBmpSet(bmpCache.GetWidth()/2, bmpCache.GetHeight()/2, true);
45     srcRect = new MmgRect(0, 0, bmpCache.GetHeight()/2, bmpCache.GetWidth()/2);
46     dstRect = new MmgRect(0, 0, bmpCache.GetHeight()/2, bmpCache.GetWidth()/2);
47     bmpSet.p.DrawBmp(bmpCache, srcRect, dstRect);

49     bmpSet.img.SetY(GetY() + MmgHelper.ScaleValue(210));
50     bmpSet.img.SetX(MmgHelper.ScaleValue(650));
51     AddObj(bmpSet.img);

53     bmpPartialCopyLabel = MmgFontData.CreateDefaultBoldMmgFontLg();
54     bmpPartialCopyLabel.SetText("MmgBmp Custom with Copy");
55     bmpPartialCopyLabel.SetPosition(MmgHelper.ScaleValue(505), GetY() + MmgHelper.ScaleValue(190));
56     AddObj(bmpPartialCopyLabel);
清单 2-46
MmgBmp 类演示 1
```

让我们看看前面列出的 `LoadResources` 方法。此方法负责设置各种 `MmgBmp` 用例，以便在 `ScreenTestMmgBmp` 游戏屏幕上进行演示。第一个示例演示了如何从已加载的图像资源条目创建 `MmgBmp` 对象，见第 14 行。要访问我们想要使用的图像，我们只需要该图像的文件名。

这就是自动加载图像资源缓存的工作方式。但是，你也可以使用自己的键将图像加载到缓存中。在这种情况下，请使用你的键而不是示例中所示的文件名。第 15 到 17 行向你展示了如何定位 `MmgBmp` 对象并将其添加到绘制例程中。

我们将审查的 `MmgBmp` 类的下一个用法是使用指定的键将图像加载到图像缓存中，见第 24 行。该键可以是任何字符串，但在此示例中，我们坚持使用自动加载资源的技术，即使用文件名。你可以使用这种方法直接从文件系统加载图像资源，并确保它们被缓存以供将来使用。



在第 34 行，我们有一个创建`MmgBmp`对象的示例，该对象是一个填充了指定颜色的自定义矩形。这在从背景填充到游戏关卡特性等多种场景中都能派上用场。在接下来要回顾的示例中，我们将看到如何通过将现有`MmgBmp`对象的一部分绘制到新图像中来创建一个新的`MmgBmp`对象。请看该方法的第 44 行。我们调用`CreateDrawableBmpSet`方法，传入宽度、高度以及一个指示是否支持透明度的布尔值。

此调用的结果是一个`MmgDrawableBmpSet`对象，它包含了在新`MmgBmp`对象上绘图所需的框架和 API 类。我们在第 45 行和第 46 行定义了源矩形和目标绘图矩形。在第 47 行，我们调用了`MmgPen`类的一个特殊绘图方法，该方法会在新的`MmgBmp`实例上进行绘制。此调用会将源图像中源矩形内的像素绘制到目标图像的目标矩形上。

## 章节总结

在本章中，我们详细回顾了游戏引擎 MmgBase API 的基础类。如果你仔细审视我们介绍过的这些类，你会发现你几乎已经拥有了开始制作游戏所需的一切。这并非偶然。我们在此回顾的这些基础类是游戏引擎设计和实现的基石。

我想从我们看过的代码中抽身出来，着手构建更宏观的图景。我们正在努力定义驱动通用 2D 游戏引擎所需的类。在此过程中，我们介绍了以下类：

*   **MmgObj**：C#中整个 MmgBase API 的超类或基类。对具有位置、尺寸和颜色的 2D 空间中的对象进行建模。
*   **MmgColor**：用于表示颜色的 API 类。
*   **MmgRect**：用于表示具有位置和尺寸的矩形的 API 类。
*   **MmgFont**：扩展了`MmgObj`类的 API 类，用于在绘制到屏幕时表示字体渲染的文本。
*   **MmgSound**：为 MmgBase API 表示声音资源的 API 类。
*   **MmgPen**：处理 API 所有绘图的 API 类。
*   **MmgVector2**：对双精度、浮点或整数精度的双分量向量进行建模的 API 类，用于 API 定位支持。
*   **MmgBmp**：扩展了`MmgObj`类的 API 类，用于表示 API 的图像。

你可能会注意到，虽然我们介绍了很多代码，但 API 中并没有出现一个我们可以看到的全面结构或能力。直到你开始在第 2 部分中回顾 MmgCore API，并看到所有内容如何整合在一起时，你才会开始看到你所寻找的结构。

话虽如此，这是有原因的。我们正在回顾驱动游戏引擎的代码，而不是游戏本身。这意味着这些代码本质上是通用的。类之间没有很多硬性连接。存在很多潜在连接，但没有什么是强制性的。这是因为我们正在回顾一个在屏幕上绘制图像的通用 2D 游戏引擎的代码。它不关心绘制是如何发生的——单屏、滚动屏幕、等距视角还是俯视视角。引擎只关心如何尽可能快地将当前可绘制对象集合绘制到屏幕上。

这就是游戏引擎相对于单个游戏的优势所在。它被设计成一个 2D 可绘制对象的通用处理器，而不是一个特定的处理器。在接下来的章节中，我们将介绍更多 MmgBase API 的内容，你将开始看到类之间越来越多可能存在的连接，这些连接可以用于创建你下一个游戏的不同方面。

