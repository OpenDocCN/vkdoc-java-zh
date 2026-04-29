# 5. 高级类

在本章中，你将回顾 MmgBase API 中的高级类。

高级类是一组通用的类，它们利用多个基础类来创建更强大的工具。在本章的回顾中，我们将了解以下类：

*   Mmg9Slice

*   MmgContainer

*   MmgLabelValuePair

*   MmgLoadingBar

*   MmgSprite

*   MmgDrawableBmpSet

## 高级类：Mmg9Slice

`Mmg9Slice` 类比我们之前见过的类更高级。该类旨在将 `MmgBmp` 图像分割成九个区域，以便图像可以在不失真的情况下调整大小。此功能主要用于矩形图像，如按钮、框架和窗口背景。图像被分割并调整大小，从而保持四个角不变，同时缩放角之间的直边以满足调整大小的要求。

### 类字段

`Mmg9Slice` 有几个类字段，如下所示。我们接下来将回顾这些字段。

```
private int offset;
private MmgBmp src;
private MmgBmp dest;
清单 5-1
Mmg9Slice 类字段 1
```

对于这个类，我们只需要回顾三个字段。第一个字段是 `offset` 字段。此字段用于图像分割过程。本质上，`offset` 字段的值是图像将被分割的、距离最近角的偏移位置。这种分割方法保留了角，防止了缩放过程中的拉伸和变形。下一个字段 `dest` 是一个 `MmgBmp`，它将保存最终缩放后的图像。

### 支持方法详情

`Mmg9Slice` 类有几个支持方法，我们将在此处回顾。需要指出的是，在处理更高级的类时，你需要有使用这些方法的经验。它们可能不会产生你期望的结果。例如，修改 `src` 字段不会改变目标字段，除非对象被重新初始化。

```
public void SetOffset(int i) { ... }
public int GetOffset() { ... }
public void SetSrc(MmgBmp b) { ... }
public MmgBmp GetSrc() { ... }
public void SetDest(MmgBmp b) { ... }
public MmgBmp GetDest() { ... }
清单 5-2
Mmg9Slice 支持方法详情 1
```

我们需要回顾的支持方法是我们之前回顾过的类字段的 get 和 set 方法。这些方法很直接，因此我不会在此详细说明。请务必仔细查看它们，并确保理解它们的用法。

### 主要方法详情

`Mmg9Slice` 类有一组主要方法，包括构造函数、类准备、克隆和比较。

```
public Mmg9Slice(int Offset, MmgBmp Src, int w, int h) { ... }
public Mmg9Slice(int Offset, MmgBmp Src, int w, int h, MmgVector2 Pos) { ... }
public Mmg9Slice(Mmg9Slice obj) { ... }
public void MmgDraw(MmgPen p) { ... }
清单 5-3
Mmg9Slice 主要方法详情 1

需要回顾的第一组主要方法以三个构造函数和 `MmgDraw` 方法开始。注意，前两个构造函数基本相同，只是第二个构造函数接受一个 `MmgVector2` 位置参数。在创建目标图像之前，会先设置偏移量、源图像、宽度和高度。一旦目标图像准备就绪，就会设置位置和可见性。前两个构造函数重载使用了这种模式。

`Mmg9Slice` 类支持克隆功能。因此，有一个专门的构造函数，它接受一个 `Mmg9Slice` 对象作为参数。该构造函数遵循与前两个相同的一般模式，只是所有值都来自参数对象。这组方法中的最后一个方法是 `MmgDraw` 方法。此方法很简单，如果可见，则绘制调整大小后的目标图像。

```
public MmgObj Clone() { ... }
public Mmg9Slice CloneTyped() { ... }
public boolean ApiEquals(Mmg9Slice obj) { ... }
public void DrawDest() { ... }
清单 5-4
Mmg9Slice 主要方法详情 2

第二组主要方法以 Clone 方法开始。注意 `Clone` 方法如何使用接受 `Mmg9Slice` 对象的构造函数重载来创建一个新的、唯一的副本。第一个 `Clone` 方法返回一个 `MmgObj` 作为参数。第二个版本的 Clone 方法 `CloneTyped` 返回一个 `Mmg9Slice` 对象。`ApiEquals` 方法是一个 API 级别的比较方法，用于测试两个 `Mmg9Slice` 实例之间的相等性。

我们需要回顾的最后一个方法是 `DrawDest` 方法。此方法负责对源图像执行九宫格切片操作。如果你更改了某些类字段，则需要重新运行此方法以更新目标图像。在使用此类时请记住这一点。

### 演示：Mmg9Slice 类

此代码示例来自 `ScreenTestMmg9Slice` 类的 `LoadResources` 方法。由于 `Mmg9Slice` 类比之前的类更高级，我们需要加载一个图像资源作为源 `MmgBmp` 图像。请务必通过运行示例应用程序并查看此演示屏幕来跟进。

```
01 bground = MmgHelper.GetBasicCachedBmp("popup_window_base.png");
02 MmgHelper.CenterHorAndVert(bground);
03 bground.SetX(bground.GetX() - MmgHelper.ScaleValue(200));
04 bground.SetY(bground.GetY() - MmgHelper.ScaleValue(32));
05 AddObj(bground);

07 bgroundLabel = MmgFontData.CreateDefaultBoldMmgFontLg();
08 bgroundLabel.SetText("原始 MmgBmp");
09 bgroundLabel.SetPosition(bground.GetPosition().Clone());
10 bgroundLabel.SetY(bgroundLabel.GetY() - bgroundLabel.GetHeight());
11 AddObj(bgroundLabel);

13 menuBground = new Mmg9Slice(MmgHelper.ScaleValue(16), bground, width, height);
14 menuBground.SetPosition(MmgVector2.GetOriginVec());
15 menuBground.SetWidth(width);
16 menuBground.SetHeight(height);
17 MmgHelper.CenterHorAndVert(menuBground);
18 menuBground.SetX(menuBground.GetX() + MmgHelper.ScaleValue(200));
19 menuBground.SetY(menuBground.GetY() + MmgHelper.ScaleValue(36));
20 AddObj(menuBground);
清单 5-5
Mmg9Slice 类演示 1

在前面的示例代码片段中，源图像在第 1-5 行加载。`MmgBmp` 图像实例通过调用 `GetBasicCachedBmp` 方法并传入文件名作为图像资源键来加载。如果图像存在于资源缓存中，这将从缓存中拉取指定的图像。此特定图像是默认图像集的一部分，因此它始终被加载并可供使用。在第 13 行，使用源图像 `bground` 创建了一个新的 `Mmg9Slice` 对象，并指定了新的宽度和高度来缩放源图像。

注意，在第 15 和 16 行，`Mmg9Slice` 对象的尺寸被再次确认。该对象在第 20 行被添加到当前示例游戏屏幕的可绘制对象列表中；这将九宫格切片对象插入到默认的绘制例程中。这就是使用 `Mmg9Slice` 类来调整弹出窗口背景图像大小并将其显示在屏幕上所需的全部操作。

## 高级类：MmgContainer

`MmgContainer` 类，顾名思义，是一个包含 `MmgObj` 实例的容器。这包括扩展了 `MmgObj` 类的对象。你可以开始看到我们迄今为止构建的通用游戏引擎的强大之处。例如，一个 `MmgContainer` 可以容纳绘制游戏信息 HUD 所需的所有 `MmgBmp` 对象。然后，你可以通过将容器对象的 `isVisible` 字段设置为 false 来使整个图像集不可见。

`MmgContainer` 类旨在容纳其他对象，但它对其子对象没有尺寸或位置的概念。它确实扩展了 `MmgObj` 类，因此你可以为容器分配尺寸和位置，以帮助为容器的使用增加结构。注意，这些设置不会影响容器内容的绘制方式。



### 静态类成员

有一个静态类字段我想快速讨论一下。`INITIAL_SIZE` 静态类字段用于控制 `MmgContainer` 内部数据结构的初始大小。如果你的游戏需要包含大量对象的容器，你可能需要增加此字段的值。

### 枚举

`MmgContainer` 类有两个重要的枚举需要我们介绍。首先是 `ChildAction` 枚举。此枚举用于定义对子对象集合执行的操作类型。它有两个值：`STAMP` 和 `UNSTAMP`，用于告知 `UpdateAllChildren` 方法如何处理子对象。标记（Stamp）一个子对象会将该容器标记为父级，并将 `hasParent` 字段设置为 true。取消标记（Unstamp）一个子对象则会逆转此过程，清除父级关系，并将 `hasParent` 设置为 false。

第二个对该类很重要的枚举是 `RenderMode` 枚举。此枚举控制类如何渲染自身。它有两个值：`RENDER_ALWAYS` 和 `RENDER_ONLY_WHEN_DIRTY`。如果渲染模式设置为始终渲染，那么容器类将始终调用其子对象的更新方法。如果模式设置为仅在脏时渲染，则仅当类字段 `isDirty` 设置为 true 时，容器才会更新其子对象。

### 类字段

我们只需要讨论两个相关的类字段。

```
private ArrayList container;
private boolean isDirty;
private RenderMode mode = RenderMode.RENDER_ALWAYS;
清单 5-6
MmgContainer 类字段 1
```

第一个字段是类的内部数据结构。所有子对象都存储在这里。请注意，该容器被设计为接受 `MmgObj` 实例。这包括所有继承自 `MmgObj` 类的类。下一个字段 `isDirty` 很重要。如果此字段为 true，则容器被标记为脏。在这种情况下，在下一次更新调用时，该类将更新其每个子对象。控制此字段非常重要。

仅允许在发生更改时更新子对象将使你的游戏更高效。在子对象不断变化的情况下，需要将 `isDirty` 字段设置为 false，以强制子对象从游戏引擎的绘制例程接收更新调用。请记住，游戏引擎的绘制例程始于当前游戏屏幕所插入的游戏面板类。

这会导致游戏屏幕接收更新和绘制方法调用，进而意味着其子对象也会接收更新和绘制方法调用。如果某个子对象是一个容器，那么对于该容器的所有子对象，此过程会再重复一次。前面列出的最后一个条目是 `mode` 字段。此字段控制容器是每次更新调用时自动更新其所有子对象，还是仅在 `isDirty` 标志设置为 true 时才更新。

### 支持方法详解

让我们来看看 `MmgContainer` 类的支持方法。这些方法允许我们访问和管理类的子对象。

```
//添加对象
public void Add(MmgObj obj) { ... }
public void AddAt(int idx, MmgObj obj) { ... }
//移除对象
public void Remove(MmgObj obj) { ... }
public MmgObj RemoveAt(int idx) { ... }
//访问对象
public int GetCount() { ... }
public MmgObj[] GetArray() { ... }
public MmgObj GetAt(int idx) { ... }
public MmgObj GetChildAt(int idx) { ... }
public MmgVector2 GetChildPosAbsolute(int idx) { ... }
public MmgVector2 GetChildPosRelative(int idx) { ... }
//清理对象数组
public void Clear() { ... }
public void Reset() { ... }
清单 5-7
MmgContainer 支持方法详解 1
```

前面列出的第一组方法为 `MmgContainer` 类提供了管理能力。前两个方法允许你向容器添加新对象。你可以将对象添加到当前对象列表的末尾，或者将对象插入到对象列表中的特定位置。接下来的两个方法允许你从容器中移除对象。

在前面列出的主要方法组的“访问对象”部分，你有可以获取子对象总数的方法 `GetCount`。`GetArray` 方法返回子对象的数组表示形式。`GetAt` 和 `GetChildAt` 方法是重载的，提供相同的功能，即从容器中返回指定索引处的子对象。接下来的两个方法 `GetChildPosAbsolute` 和 `GetChildPosRelative` 用于从容器中返回子对象，并调整它们的位置，使其相对于容器或相对于游戏面板。

最后，我们有两个用于重置容器内容的方法。这两个方法执行类似的任务，但它们之间存在一些细微的差异。`Clear` 方法在清除容器内容之前会取消标记所有子对象。`Reset` 方法类似，但它不会取消标记其子对象，也不会清除当前容器；它会实例化一个新的数据结构。这些是细微但重要的区别，在使用此类时你应该牢记。

```
public ArrayList GetContainer() { ... }
public void SetContainer(ArrayList aTmp) { ... }
public void SetIsDirty(boolean b) { ... }
public boolean GetIsDirty() { ... }
public void SetMode(RenderMode m) { ... }
public RenderMode GetMode() { ... }
清单 5-8
MmgContainer 支持方法详解 2
```

我们要回顾的第二组支持方法只是该类字段的标准 get 和 set 方法。如果对象正被游戏引擎的绘制例程使用，调整字段时要小心。如果不小心，你可能会遇到异常或意外的屏幕渲染。

### 主要方法详解

我们需要回顾的主要方法包括构造函数、子-父级关联以及更新和绘制方法。

```
//构造函数
public MmgContainer() { ... }
public MmgContainer(MmgContainer obj) { ... }
public MmgContainer(ArrayList objects) { ... }
//父-子级关联
private void StampChild(MmgObj obj) { ... }
private void UnstampChild(MmgObj obj) { ... }
private void UpdateAllChildren(ChildAction act) { ... }
//绘制例程
public void MmgDraw(MmgPen p) { ... }
public boolean MmgUpdate(int updateTicks, long currentTimeMs, long msSinceLastFrame) { ... }
清单 5-9
MmgContainer 主要方法详解 1
```

我们要回顾的第一组主要方法是类的构造函数。第一个构造函数创建一个新的空 `MmgContainer` 对象，其容器数据结构设置为默认初始大小。列出的第二个构造函数是一个专用构造函数，用于类的克隆功能。它根据现有容器对象创建一个新的容器对象。它还会克隆其所有子对象，作为克隆过程的一部分。

列出的最后一个构造函数接受一个用于初始化其内容的子对象数据结构。请注意，此构造函数不会标记子对象。管理父/子关系将是你的责任。游戏引擎在这方面不强制执行任何默认行为。

接下来的三个方法用于管理容器及其子对象的父/子关系。`StampChild` 和 `UnstampChild` 方法用于将子对象的父级设置为该容器，或移除该关联。`UpdateAllChildren` 方法接受一个 `ChildAction` 实例作为参数，并将为每个子对象运行 `StampChild` 或 `UnstampChild` 方法。至此，主要方法的回顾就结束了。接下来，我们将看一些演示如何使用 `MmgContainer` 类的示例代码。



### 演示：MmgContainer 类

本演示部分的示例代码来自 MmgTestSpace 包（在 C# 中为命名空间）中 `ScreenTestMmgContainer` 类的 `LoadResources` 方法。让我们来看一些代码！

```
01 frame2 = MmgHelper.GetBasicCachedBmp("soldier_frame_2.png");
02 frame2 = MmgBmpScaler.ScaleMmgBmp(frame2, 2.0f, true);
03 MmgHelper.CenterHorAndVert(frame2);
04 frame2.SetY(frame2.GetY() - 0);

06 frame3 = MmgHelper.GetBasicCachedBmp("soldier_frame_3.png");
07 frame3 = MmgBmpScaler.ScaleMmgBmp(frame3, 2.0f, true);
08 MmgHelper.CenterHorAndVert(frame3);
09 frame3.SetY(frame2.GetY() + MmgHelper.ScaleValue(80));

11 holder = new MmgContainer();
12 holder.Add(frame2);
13 holder.Add(frame3);
14 holder.SetWidth(frame1.GetWidth());
15 holder.SetHeight(MmgHelper.ScaleValue(160));
16 MmgHelper.CenterHorAndVert(holder);
17 AddObj(holder);
代码清单 5-10
MmgContainer 类演示 1
```

前两段代码（第 1-4 行和第 6-9 行）使用图像资源缓存加载了两张图像（动画帧）。每个帧都被缩放并定位，以便在屏幕垂直方向上居中。第三段代码展示了 `MmgContainer` 对象的初始化，该对象将 `frame1` 和 `frame2` 变量作为子对象添加。

请注意，即使 `MmgContainer` 本身不在屏幕上显示，我们也要确保在第 14 和 15 行设置了容器的尺寸。该容器在第 17 行被添加到游戏引擎的绘制例程中。这确保了容器的子对象能够通过 `MmgContainer` 类的 `MmgDraw` 方法绘制到屏幕上。

## 高级类：MmgLabelValuePair

`MmgLabelValuePair` 类用于保存要在游戏屏幕上显示的标签和值文本。可以将其想象为设置条目、游戏选项和游戏内 HUD。该类提供了一种快速设置标签和值文本的字体、文本和定位的方法。

### 静态类成员

我们唯一需要查看的静态类成员是一个 X 轴定位值 `DEFAULT_PADDING_X`。这是用于填充标签和值文本之间间距的默认值。该类没有需要查看的相关枚举，因此我们将跳过该部分的审查过程，直接进入类的字段审查。

### 类字段

关于 `MmgLabelValuePair` 类，我们只需要审查四个类字段。我已在下面列出它们。

```
private MmgFont lbl;
private MmgFont val;
private int paddingX;
private boolean skipReset;
代码清单 5-11
MmgLabelValuePair 类字段 1
```

列表中的第一个字段是一个 `MmgFont` 实例，用于显示标签文本。第二个字段也是一个 `MmgFont` 实例，用于显示标签-值对中的值文本。`paddingX` 字段用于保存水平填充值，以便标签和值文本之间有一些间距。这组字段中的最后一个字段是一个布尔标志，用于指示我们应该跳过重新定位标签和值对象。

在更改某些类字段后，该类会自动进行自我准备。但我们并不总是希望该类进行自我准备。我们可以使用 `skipReset` 字段来控制类的这一方面。如果设置为 `false`，它将跳过自动类准备步骤。请记住，在游戏屏幕中使用该类之前，应确保该类已正确准备就绪。

### 支持方法详情

`MmgLabelValuePair` 类具有支持方法，可帮助您控制类的不同方面及其在屏幕上的显示方式。

```
//值方法
public MmgFont GetValue() { ... }
public void SetValue(MmgFont ft) { ... }
public String GetValueText() { ... }
public void SetValueText(String s) { ... }
public Font GetValueFont() { ... }
public void SetValueFont(Font ft) { ... }
//字体方法
public MmgFont GetLabel() { ... }
public void SetLabel(MmgFont ft) { ... }
public String GetLabelText() { ... }
public void SetLabelText(String s) { ... }
public Font GetLabelFont() { ... }
public void SetLabelFont(Font ft) { ... }
//属性方法
public void SetFontSize(int sz) { ... }
public int GetFontSize() { ... }
public void SetMmgColor(MmgColor c) { ... }
public boolean GetSkipReset() { ... }
public void SetSkipReset(boolean b) { ... }
//定位
public void SetPosition(MmgVector2 v) { ... }
public void SetPosition(int x, int y) { ... }
public void SetX(int x) { ... }
public void SetY(int y) { ... }
public int GetPaddingX() { ... }
public void SetPaddingX(int p) { ... }
//其他方法
private void Reset() { ... }
代码清单 5-12
MmgLabelValuePair 支持方法详情 1
```

上面列出的第一组支持方法可帮助您管理 `value` 字段。您可以使用 get 和 set 方法访问 `value` 字段。您还可以指定要显示的文本和要使用的字体。类的 `label` 字段也存在相同的一组方法。这使您可以对用于渲染类的文本和字体进行大量控制。

下一组方法允许您控制 `label` 和 `value` 字段的属性。您可以使用 `GetFontSize` 方法来获取 `label` 的字体大小。由于 `label` 和 `value` 字段彼此独立，因此您可能设置了两种不同的字体大小。`GetFontSize` 方法不会尝试决定使用哪种字体大小（`label` 还是 `value`）；它总是返回 `label` 的字体大小。

`SetFontSize` 方法性质类似。您可以使用此方法更新标签和值对象的字体大小。此方法以及许多其他更改 `label` 和 `value` 属性的方法，都会尝试准备该类并对齐文本以便显示。如果您不希望运行类准备步骤，请在调用支持方法之前将 `skipReset` 类字段设置为 `true`。

这组方法中的下一个方法是 `SetMmgColor` 方法。虽然这看起来像是一个简单的 set 方法，但事实并非如此。此方法将设置容器以及 `label` 和 `value` 字段的颜色属性。上面列出的最后一组方法用于定位 `MmgLabelValuePair` 类，可以通过设置其位置、调整水平偏移或重置标签和值对象的定位来实现。

定位方法 `SetPosition`、`SetX` 和 `SetY` 用于调整标签-值对的位置。请注意，使用这些方法只会更新标签对象。一旦设置了标签的位置，定位方法将调用类的 `Reset` 方法。`Reset` 方法将负责对齐 `label` 和 `value` 文本。如果您不希望该类自动准备，请确保将 `skipReset` 字段设置为 `true`。如果修改完类后它没有自动运行，请始终记得调用 `Reset` 方法。



### 主要方法详情

`MmgLabelValuePair` 类的主要方法包括构造函数、克隆方法以及绘制和类准备方法。下面需要审查的方法列表中只有三个构造函数，但该类实际上还有更多方法。请务必通读并理解它们的工作原理。

```
public MmgLabelValuePair() { ... }
public MmgLabelValuePair(MmgFont fontLbl, MmgFont fontVal) { ... }
public MmgLabelValuePair(MmgLabelValuePair obj) { ... }
public MmgObj Clone() { ... }
public MmgLabelValuePair CloneTyped() { ... }
public void MmgDraw(MmgPen p) { ... }
代码清单 5-13
MmgLabelValuePair 主要方法详情 1
```

第一个构造函数不接受任何参数，并为标签和值字体以及内边距和尺寸设置默认值。此构造函数不会为 `MmgFont` 实例设置任何文本值。在调用 `Reset` 方法之前，类的尺寸被设置为零。`Reset` 方法将计算对象的尺寸，并根据标签和值字体的大小调整其位置。

上面列出的第二个构造函数接受两个 `MmgFont` 参数，一个用于 `label`，一个用于 `value`。这可能是最常见的用例，即传入已配置好文本、样式和大小的 `MmgFont` 对象。列出的最后一个构造函数应该看起来很熟悉。这是一个专门的构造函数，它接受一个 `MmgLabelValuePair` 对象作为参数，并使用它来创建该对象的一个新的、唯一的副本。

接下来是类的克隆方法。`Clone` 和 `CloneTyped` 方法，正如我们之前所见，使用专门的构造函数创建一个新对象，并将其返回为强制转换为 `MmgObj` 对象或作为 `MmgLabelValuePair` 实例。列表中的下一个方法是 `Reset` 方法。此方法对类的功能非常重要。它被设计为在更改标签 `MmgFont`、值 `MmgFont` 或对象位置的任何支持方法之后调用。最后但同样重要的是，`MmgDraw` 方法负责将标签和值绘制到屏幕上，作为游戏引擎绘制例程的一部分。

### 演示：MmgLabelValuePair 类

下面列出的示例代码片段来自 MmgTestSpace 包（在 C# 中为命名空间）中 `ScreenTestMmgLabelValuePair` 类的 `LoadResources` 方法。该演示向您展示如何正确初始化一个 `MmgLabelValuePair` 对象。

```
01 labelFont = MmgFontData.CreateDefaultBoldMmgFontLg();
02 labelFont.SetMmgColor(MmgColor.GetGrayWolf());
03 labelFont.SetText("Label1:");

05 valueFont = MmgFontData.CreateDefaultBoldMmgFontSm();
06 valueFont.SetMmgColor(MmgColor.GetBlueGray());
07 valueFont.SetText("Value1");

09 lvPair1 = new MmgLabelValuePair(labelFont, valueFont);
10 MmgHelper.CenterHorAndVert(lvPair1);
11 lvPair1.SetY(lvPair1.GetY() - MmgHelper.ScaleValue(30));
12 AddObj(lvPair1);
代码清单 5-14
MmgLabelValuePair 类演示 1
```

在第一个代码块中，使用第 2 行和第 3 行指定的文本和颜色创建了一个新的 `MmgFont` 对象。请注意，在第 1 行，大字体实例是通过 `MmgHelper` 类的静态辅助方法创建的。第二个代码块（第 5-7 行）创建并配置了值 `MmgFont` 实例。最后一个代码块（第 9-12 行）展示了使用我们刚刚创建的两个 `MmgFont` 对象实例化一个新的 `MmgLabelValuePair` 对象。

请注意，我们只需要调用带有标签和值对象的构造函数。正如我们之前所见，构造函数通过内部调用 `Reset` 方法来负责准备类。在接下来的两行代码（第 10 行和第 11 行）中，对象在水平和垂直方向上居中，然后通过调用 `AddObj` 方法添加到游戏引擎的绘制例程中。

## 高级类：MmgLoadingBar

`MmgLoadingBar` 类是一个高级类，它使用 `MmgBmp` 对象来创建一个新的加载条。加载条由 `MmgLoadingScreen` 类用作资源加载过程的一部分。您可能会发现加载条不仅在游戏启动过程中有用，而且作为 UI 元素也很有用。例如，您可以将此类用作生命条或充能计量器。

### 类字段

`MmgLoadingBar` 的类字段如下所列。只有七个字段，它们用于保存前景图像、背景图像、定位以及加载条填充属性。

```
//加载条图像
private MmgBmp loadingBarBack;
private MmgBmp loadingBarFront;
//内边距方法
private int xPadding;
private int yPadding;
//加载条填充方法
private float fillAmt;
private int fillHeight;
private int fillWidth;
代码清单 5-15
MmgLoadingBar 类字段 1
```

第一个类字段 `loadingBarBack` 用于绘制加载条的背景图像。在大多数情况下，您会希望为加载条的背景图像使用纯色。此图像将被调整大小以指示加载进度、生命值或充能条等级。接下来的类字段 `loadingBarFront` 也是一个 `MmgBmp` 对象；它用作加载条的前景。这是覆盖实际条的图像。它通常是一个矩形或药丸形状的图像，中心被挖空，以便背景、进度条图像可见。

第二组类字段用于为 `loadingBarBack` 图像的位置提供内边距调整。您可以调整 `xPadding` 和 `yPadding`，使加载条很好地适配 `loadingBarFront` 图像的挖空窗口。最后一组字段用于描述用于调整 `loadingBarBack` 图像大小以匹配加载条进度的高度、宽度和填充量。`fillAmt` 字段保存一个范围从 0.0 到 1.0 的值。此值用于计算 `loadingBarBack` 图像的适当宽度。通过这种方式，图像的宽度反映了存储在 `fillAmt` 类字段中的进度百分比。



### 支持方法详情

下面列出的 `MmgLoadingBar` 类的支持方法允许你控制背景和前景图像、控制填充图像的大小和内边距，以及重新定位加载条。

```
//加载条背景方法
public MmgBmp GetLoadingBarFront() { ... }
public void SetLoadingBarFront(MmgBmp f) { ... }
public MmgBmp GetLoadingBarBack() { ... }
public void SetLoadingBarBack(MmgBmp b) { ... }
//加载条填充方法
public float GetFillAmt() { ... }
public void SetFillAmt(float f) { ... }
public int GetFillWidth() { ... }
public void SetFillWidth(int w) { ... }
public int GetFillHeight() { ... }
public void SetFillHeight(int h) { ... }
//内边距方法
public int GetPaddingY() { ... }
public void SetPaddingY(int py) { ... }
public int GetPaddingX() { ... }
public void SetPaddingX(int px) { ... }
//定位方法
public void SetPosition(MmgVector2 pos) { ... }
public void SetPosition(int x, int y) { ... }
public void SetX(int inX) { ... }
public void SetY(int inY) { ... }
代码清单 5-16
MmgLoadingBar 支持方法详情 1
```

第一组支持方法提供了对背景和前景图像的访问。需要说明的是，当调用 `SetLoadingBarBack` 方法时，填充量（缩放值）会被清除。这样做是为了确保背景图像不会应用任何缩放。通常，加载条类会控制背景图像的缩放。

第二组方法提供了对 `fillAmt`、`fillWidth` 和 `fillHeight` 字段的访问。`fillWidth` 和 `fillHeight` 字段描述了加载条前景图像裁剪区域的尺寸。`fillAmt`（完成百分比）与 `fillWidth` 字段相乘，以确定背景图像应显示的宽度。第三组方法只是该类内边距字段的简单 get 和 set 方法。

前面列出的最后一组支持方法帮助你重新定位屏幕上的加载条。由于这是一个高级类，它包含多个绘制到屏幕的内部对象。这在尝试重新定位对象时带来了挑战，因为我们必须重新定位多个对象，并且通常需要重新计算它们的相对位置。

克服这一复杂性的主要方法是重写设置位置和设置坐标的方法。这为我们提供了一个抽象点，可以在其中运行代码来处理此类问题。在这种情况下，当使用这些方法更改对象的位置时，我们可以同时更新基类、背景图像和前景图像的位置。

### 主要方法详情

下面列出的主要方法是类的构造函数、克隆方法和游戏引擎的绘制方法。请注意，克隆功能是存在的，并且该类扩展了 `MmgObj` 类，这使其成为可绘制对象集合的成员。

```
public MmgLoadingBar() { ... }
public MmgLoadingBar(MmgLoadingBar obj) { ... }
public MmgLoadingBar(MmgBmp LoadingBarBack, MmgBmp LoadingBarFront) { ... }
public MmgObj Clone() { ... }
public MmgLoadingBar CloneTyped() { ... }
public void MmgDraw(MmgPen p) { ... }
代码清单 5-17
MmgLoadingBar 主要方法详情 1
```

正如我们所料，要回顾的第一组主要方法以一组类构造函数开始。基础构造函数不接受任何参数，并使用一些占位值初始化该类。此构造函数不会将类置于适合使用的状态。你需要调用一些支持方法来正确配置该类。

下一个构造函数是一个专用构造函数，它接受一个 `MmgLoadingBar` 实例作为参数，并使用它来创建一个新的、唯一的 `MmgLoadingBar` 对象。剩下的构造函数接受两个 `MmgBmp` 参数。它遵循与我们刚刚回顾的构造函数几乎相同的实现，只是此构造函数重载根据提供的参数设置了加载条的前景和背景图像。

接下来要回顾的一组主要方法是该类的克隆方法。正如我们之前所见，有两种类型的克隆方法。第一个 `Clone` 将创建该类的一个新的唯一副本，并将其向上转换为父类 `MmgObj` 返回。第二个克隆方法 `CloneTyped` 执行相同的操作，但不对返回的对象进行类型转换，因此该方法返回一个 `MmgLoadingBar` 实例。

我们最后要回顾的方法是 `MmgDraw` 方法。请注意，该方法首先绘制背景图像，并且它会设置颜色以及源和目标绘制矩形，这些矩形用于在每个游戏帧上缩放图像。源矩形和目标矩形用于根据前景图像和 `fillAmt` 字段将背景图像缩放到适当的大小。

### 演示：MmgLoadingBar 类

用于演示 `MmgLoadingBar` 类实际运行的示例代码来自 MmgBase 库的 `ScreenLoading` 类。这些代码片段演示了如何设置 `MmgLoadingBar` 类，并展示了如何使用关于资源加载进度的事件驱动数据。

```
01 key = "imgLoadingBar";
02 if(classConfig.containsKey(key)) {
03     file = classConfig.get(key).str;
04 } else {
05     file = "loading_bar.png";
06 }

08 tB = MmgHelper.GetBasicBmp(GameSettings.IMAGE_LOAD_DIR + file);

10 key = "imgLoadingBarFill";
11 if(classConfig.containsKey(key)) {
12     file = classConfig.get(key).str;
13 } else {
14     file = "blue_square.png";
15 }

17 tB1 = MmgHelper.GetBasicBmp(GameSettings.IMAGE_LOAD_DIR + file);
18 if (tB1 != null) {
19     tB1.DRAW_MODE = MmgBmpDrawMode.DRAW_BMP_FULL;
20 }

22 if (tB != null && tB1 != null) {
23     lb = new MmgLoadingBar(tB1, tB);
24     lb.SetMmgColor(null);
25     lb.SetWidth(tB.GetWidth() - MmgHelper.ScaleValue(10));
26     lb.SetHeight(tB.GetHeight() - MmgHelper.ScaleValue(12));
27     lb.SetFillAmt(0.0f);
28     lb.SetPaddingX(MmgHelper.ScaleValue(8));
29     lb.SetPaddingY(MmgHelper.ScaleValue(4));
30     lb.SetFillHeight(tB.GetHeight() - MmgHelper.ScaleValue(10));
31     lb.SetFillWidth(tB.GetWidth() - MmgHelper.ScaleValue(12));
32     super.SetLoadingBar(lb, lbOffSet);
代码清单 5-18
MmgLoadingBar 类演示 1
```

前几行代码（第 1–6 行）用于从本地类配置对象加载加载条前景图像的位置。如果未找到类配置键，则使用默认资源。图像在第 8 行加载。接下来的代码块（第 10–15 行）执行与我们刚刚回顾的代码类似的功能，但针对的是加载条的背景图像。

回想一下，我们打算缩放加载条的背景图像，为此我们需要确保图像支持缩放。为了确保图像可以调整大小，我们将绘制模式设置为 `DRAW_BMP_FULL`，以便在渲染 `MmgBmp` 图像时可以考虑源矩形和目标矩形，第 17–20 行。

接下来的代码行（第 22–32 行）详细说明了正确设置 `MmgLoadingBar` 对象以供使用的方法。请注意，构造函数用于设置加载条的背景和前景图像。颜色设置为 null，`MmgLoadingBar` 对象的宽度和高度在第 25 和 26 行设置。`fillAmt` 字段设置为 0，X 轴和 Y 轴的内边距值分别设置为 8 和 4 像素，第 27–29 行。在第 30 和 31 行，设置了填充高度和宽度。请注意，填充宽度和高度基于加载条前景图像的尺寸和类的内边距值。请自行花时间追踪加载条的值是如何更新的。确保你理解这个过程。



## 高级类：MmgSprite

`MmgSprite` 类是一个用于建模精灵图像的高级类。在 2D 游戏开发中，精灵是一种多帧 2D 图像，在绘制时会呈现动画效果。因此，`MmgSprite` 类被设计用于处理一个 `MmgBmp` 对象数组，这些对象代表了精灵对象的各个帧。

### 静态类成员

`MmgSprite` 类有三个静态类字段供我们查看，如下所示。

```
public static int DEFAULT_MS_PER_FRAME = 100;
public static int MMG_SPRITE_FRAME_CHANGE = 0;
public static int MMG_SPRITE_FRAME_CHANGE_TYPE = 0;
Listing 5-19
MmgSprite 静态类成员 1
```

第一个字段是精灵动画每一帧等待毫秒数的默认值。对于 2D 图像动画来说，100 毫秒是相当长的一段时间，但这是一个默认值，并且设置得较慢，这样你就不会错过任何帧。其余两个静态类字段是在精灵动画帧变化事件中使用的 ID 值。

### 类字段

我们要查看的第一组类字段如下所示。这些字段控制每一帧的高级渲染，保存动画的帧，并跟踪精灵动画的起始帧和结束帧。

```
private MmgVector2 origin;
private MmgVector2 scaling;
private MmgRect srcRect;
private MmgRect dstRect;
private MmgBmp[] b;
private float rotation;
private int frameStart;
private int frameStop;
Listing 5-20
MmgSprite 类字段 1
```

上述第一个条目包含用于帧缩放和旋转操作的字段。`origin` 字段是一个 `MmgVector2` 对象，用于存储旋转变换时使用的中心点坐标。类似地，`scaling`、`srcRect` 和 `dstRect` 类字段用于动画帧的缩放变换。你猜对了。`MmgSprite` 类能够动态缩放或旋转任何精灵帧。

实际的动画帧存储在 `MmgBmp` 数组类字段 `b` 中。`rotation` 字段用于存储一个以度为单位的旋转角度，供图像旋转时使用。接下来的两个类字段看似简单，实则功能强大。使用 `frameStart` 和 `frameStop` 字段，你可以仅对精灵帧的一部分进行动画处理。最后这一点非常重要。你可以让动画仅运行在精灵帧的一个子集上。

```
private long frameTime = -1;
private long prevFrameTime = -1;
private int frameIdx;
private long msPerFrame;
private boolean simpleRendering;
private MmgEventHandler onFrameChange;
private boolean timerOnly;
private MmgEvent frameChange = new MmgEvent(null, "frame_changed", MmgSprite.MMG_SPRITE_FRAME_CHANGE, MmgSprite.MMG_SPRITE_FRAME_CHANGE_TYPE, null, null);
Listing 5-21
MmgSprite 类字段 2
```

第二组类字段以两个时间跟踪字段 `frameTime` 和 `prevFrameTime` 开头。这些字段用于测量帧的持续时间。`frameIdx` 字段通过数组索引跟踪当前帧。`msPerFrame` 字段用于确定在每个动画帧上停留的时间。`simpleRendering` 布尔标志用于强制进行简单的 `MmgBmp` 渲染，通过执行更少的操作来提高绘制方法的效率。

在这种情况下，用于确定使用哪个绘制例程的检查会更少。`onFrameChange` 字段是一个事件处理器，当正在渲染的帧发生变化时触发。`timerOnly` 布尔标志会使跟踪帧变化的计时机制仅作为当前帧的计时器工作。在这种情况下，帧索引不会递增。最后，`frameChange` 事件是用于处理帧变化事件的事件模板。

### 支持方法详解

我们正在审查的类变得越来越复杂。因此，我们有时会遇到很长的 get 和 set 方法列表。我会尝试将这些方法分成有意义的组。让我们看看下面列出的第一组方法。

```
//帧计时方法
public long GetFrameTime() { ... }
public void SetFrameTime(long l) { ... }
public long GetPrevFrameTime() { ... }
public void SetPrevFrameTime(long l) { ... }
public long GetMsPerFrame() { ... ]
public void SetMsPerFrame(long f) { ... }
//帧事件方法
public void SetFrameChangeEventId(int i) { ... }
public MmgEventHandler GetOnFrameChange() { ... }
public void SetOnFrameChange(MmgEventHandler e) { ... }
//帧方法
public boolean GetSimpleRendering() { ... }
public void SetSimpleRendering(boolean s) { ... }
public boolean GetTimerOnly() { ... }
public void SetTimerOnly(boolean b) { ... }
public MmgBmp GetCurrentFrame() { ... }
public void SetCurrentFrame(MmgBmp bmp) { ... }
public MmgBmp[] GetBmpArray()  { ... }
public void SetBmpArray(MmgBmp[] d)  { ... }
Listing 5-22
MmgSprite 支持方法详解 1
```

我们要审查的第一组方法是处理帧计时的支持方法。这里有用于设置当前帧时间、上一帧时间以及更改帧前等待毫秒数的 get 和 set 方法。第二组方法允许访问类的事件。你可以自定义动画帧更改时触发的事件的 ID。该组中的接下来两个方法允许你访问类中帧变化事件的事件处理器。

上面列出的最后一组支持方法使你能够控制精灵的动画帧和渲染。前两个方法 `GetSimpleRendering` 和 `SetSimpleRendering` 让你可以访问简单渲染字段。如果设置为 true，该类将仅对精灵动画的帧使用基本渲染。接下来列出的两个方法是 `timerOnly` 字段的访问方法。如果 `timerOnly` 标志设置为 true，跟踪帧变化的计时机制将仅作为当前帧的计时器工作。在这种情况下，动画帧不会自动递增。

剩下的四个方法允许你获取或设置当前动画帧，并访问控制精灵类帧的 `MmgBmp` 数组。设置 `MmgBmp` 数组将导致精灵对象的尺寸和帧索引被重置。

```
//帧渲染方法
public MmgRect GetSrcRect()  { ... }
public void SetSrcRect(MmgRect r)  { ... }
public MmgRect GetDstRect()  { ... }
public void SetDstRect(MmgRect r)  { ... }
public float GetRotation()  { ... }
public void SetRotation(float r)  { ... }
public MmgVector2 GetOrigin()  { ... }
public void SetOrigin(MmgVector2 v)  { ... }
public MmgVector2 GetScaling()  { ... }
public void SetScaling(MmgVector2 v)  { ... }
//帧索引方法
public boolean IsFrameNull(int i)  { ... }
public int GetFrameIdx()  { ... }
public void SetFrameIdx(int f)  { ... }
public int GetFrameStart()  { ... }
public void SetFrameStart(int f)  { ... }
public int GetFrameStop()  { ... }
public void SetFrameStop(int f)  { ... }
Listing 5-23
MmgSprite 支持方法详解 2
```

我们要查看的下一组方法由两部分组成：帧渲染方法和帧索引方法。帧渲染方法让你可以访问所有必要的高级渲染字段，以支持 `MmgSprite` 当前帧的缩放和/或旋转。帧索引方法是该类动画帧索引字段的简单 get 和 set 方法。你可以使用这些方法控制当前帧索引以及动画的起始和结束帧索引。至此，我们对 `MmgSprite` 类支持方法的审查就结束了。接下来，我们将查看该类的主要方法。



### 主要方法详情

该类的主要方法按以下三组列出。这里有常规的类构造函数；这个 `MmgSprite` 类拥有的构造函数比我们在此列出的更多。在继续之前，请花时间复习并理解它们。我们还将在此处回顾克隆、比较和渲染方法。

```
//类构造函数
public MmgSprite(MmgBmp[] t, MmgVector2 Position) { ... }
public MmgSprite(MmgBmp[] t) { ... }
public MmgSprite(MmgSprite obj) { ... }
//克隆方法
public MmgObj Clone() { ... }
public MmgSprite CloneTyped() { ... }
//绘制、更新和比较方法
public void MmgDraw(MmgPen p) { ... }
public boolean MmgUpdate(int updateTick, long currentTimeMs, long msSinceLastFrame) { ... }
public boolean ApiEquals(MmgSprite obj) { ... }
清单 5-24
MmgSprite 主要方法详情 1
```

我们要看的第一组方法是类的构造函数。第一个条目接受一个代表精灵动画帧的 `MmgBmp` 对象数组，以及一个持有帧当前位置的 `MmgVector2` 对象。此构造函数将设置动画帧并定位精灵。第二个构造函数类似，但它只将动画帧作为参数。最后但同样重要的是，专用构造函数接受一个 `MmgSprite` 对象作为参数，并使用它来创建一个新的、唯一的类实例。

下一组方法是克隆方法。我们之前已经多次看到这种实现，所以我不会在此深入细节。这两个克隆方法使用专用构造函数创建该类的一个新的、唯一的副本，并将其转换为指定的类类型后返回。最后一组要回顾的主要方法也是我们之前见过的方法。游戏引擎的默认绘制例程将调用类的更新和绘制方法。

更新方法负责处理计时、动画帧和事件，并在帧发生变化时触发事件。类的绘制方法可以处理更高级的渲染功能（如果启用），或者只是简单直接地渲染精灵当前的动画帧。该组中的最后一个条目是 `ApiEquals` 方法。这是一个 API 级别的方法，用于比较两个 `MmgSprite` 对象是否相等。

### 演示：MmgSprite 类

下面列出的演示代码来自 `ScreenTestMmgSprite` 类的 `LoadResources` 方法。同时还列出了游戏屏幕类的 `MmgUpdate` 和 `MmgDraw` 方法的游戏引擎绘制例程方法。

```
//ScreenTestMmgSprite.LoadResources
01 frame1 = MmgHelper.GetBasicCachedBmp("soldier_frame_1.png");
02 frame1 = MmgBmpScaler.ScaleMmgBmp(frame1, 2.0f, true);
03 MmgHelper.CenterHorAndVert(frame1);

05 frame2 = MmgHelper.GetBasicCachedBmp("soldier_frame_2.png");
06 frame2 = MmgBmpScaler.ScaleMmgBmp(frame2, 2.0f, true);
07 MmgHelper.CenterHorAndVert(frame2);

09 frame3 = MmgHelper.GetBasicCachedBmp("soldier_frame_3.png");
10 frame3 = MmgBmpScaler.ScaleMmgBmp(frame3, 2.0f, true);
11 MmgHelper.CenterHorAndVert(frame3);

13 frames1 = new MmgBmp[4];
14 frames1[0] = frame1;
15 frames1[1] = frame2;
16 frames1[2] = frame3;
17 frames1[3] = frame2;

19 frames2 = new MmgBmp[4];
20 frames2[0] = frame1.CloneTyped();
21 frames2[1] = frame2.CloneTyped();
22 frames2[2] = frame3.CloneTyped();
23 frames2[3] = frame2.CloneTyped();

25 MmgVector2 tmpPos = frame1.GetPosition().Clone();
26 tmpPos.SetY(tmpPos.GetY() - MmgHelper.ScaleValue(30));
27 sprite1 = new MmgSprite(frames1, tmpPos);
28 sprite1.SetMsPerFrame(200l);
29 AddObj(sprite1);
//游戏引擎绘制例程
01 public boolean MmgUpdate(int updateTick, long currentTimeMs, long msSinceLastFrame) {
02     lret = false;

04     if (pause == false && isVisible == true) {
05         //始终运行此更新
06         sprite1.MmgUpdate(updateTick, currentTimeMs, msSinceLastFrame);
07         sprite2.MmgUpdate(updateTick, currentTimeMs, msSinceLastFrame);
08     }

10     return lret;
11 }
01 public void MmgDraw(MmgPen p) {
02     if (pause == false && isVisible == true) {
03         super.MmgDraw(p);
04     }
05 }
清单 5-25
MmgSprite 类演示 1
```

`LoadResources` 方法中第 1-23 行的第一个代码片段向我们展示了如何配置一个简单的 `MmgSprite` 对象并将其绘制到屏幕上。在第 1-3 行，从键 "soldier_frame_1.png" 加载了一个动画帧。你可以判断这是一个缓存的图像资源，因为它仅通过键加载。很可能，此图像是从测试应用程序的项目资源文件夹自动加载的。

该图像在第 2 行被稍微放大，并在第 3 行居中定位。对另外两个动画帧执行相同的步骤，这样我们总共加载了三个帧。在第 13 行，实例化了一个 `MmgBmp` 对象数组来保存我们刚刚加载的帧。现在，动画的性质是这样的：我们不想从第 3 帧直接跳到第 1 帧；我们希望先回到第 2 帧。因此，我们创建了一个长度为 4 的数组，并按正确的顺序存储帧。在第 25 和 26 行，我们准备了 `MmgSprite` 对象的位置。

仔细查看定位代码。注意我们克隆了 `frame1` 使用的位置，并稍微向下调整了它——大约 15 像素。在第 27 行，使用 `MmgBmp` 数组、帧和位置创建了一个新的 `MmgSprite` 对象。请注意，帧的位置与精灵对象的位置不同。回想一下，动画帧是在精灵的位置上绘制的。

在第 28 行，帧时间被设置为一个较大的数值，200 毫秒，以便于观察。因为 `MmgSprite` 类扩展了 `MmgObj` 类，所以可以通过调用 `AddObj` 将其添加到游戏屏幕的可绘制对象集合中。在此演示片段中要回顾的下一段代码是 `MmgUpdate` 方法。注意在第 6 和 7 行，如果游戏屏幕可见且未暂停，则会调用精灵对象的更新方法。此示例代码向我们展示了如何在更新方法中更新其他对象，以及如何让更复杂的类自我更新。最后，`MmgDraw` 方法向我们展示了精灵对象像屏幕上任何其他 `MmgObj` 一样，通过默认的绘制例程进行绘制。

## 高级类：MmgDrawableBmpSet

`MmgDrawableBmpSet` 类用于创建一组预先配置好以协同工作的框架和 MmgBase API 类。这些类的设置使得 `MmgPen` 对象在 `MmgBmp` 对象上绘制。支持此功能的框架类作为 `MmgDrawableBmpSet` 对象字段的一部分包含在内。

在大多数情况下，游戏引擎的 C# 实现与 Java 实现非常相似。API 是相同的，但由于它们所基于的框架不同，实现自然也有所不同。

### 类字段

下面，我列出了 `MmgDrawableBmpSet` 类的 Java 和 C# 版本。你可以使用这些类来提醒自己框架类是如何对齐的。Java 的 `BufferedImage` 大致相当于 C# 的 `RenderTarget2D`。类似地，Java 的 `Graphics2D` 类大致相当于 C# 的 `SpriteBatch` 类。

```
public RenderTarget2D buffImg;
public SpriteBatch graphics;
public MmgPen p;
public MmgBmp img;
清单 5-27
MmgDrawableBmpSet 类字段 C#
```

```
public BufferedImage buffImg;
public Graphics2D graphics;
public MmgPen p;
public MmgBmp img;
清单 5-26
MmgDrawableBmpSet 类字段 Java
```

第一个字段 `buffImg` 用于保存框架实现的可绘制图像。`graphics` 字段是一个框架类，用于在包括图像在内的表面上进行绘制。API 级别的类用作字段，以支持该类使用 `MmgPen` 字段 `p` 在目标 `MmgBmp` 图像 `img` 上进行绘制的能力。



### 演示：MmgDrawableBmpSet 类

第一段代码来自 MmgBase API 中 `MmgHelper` 类的 `CreateDrawableBmpSet` 方法。第二段代码来自 `ScreenTestMmgBmp` 类的 `LoadResources` 方法。请务必通过运行示例应用程序来查看此游戏屏幕的实际效果。

```
//MmgHelper.CreateDrawableBmpSet
01 public static MmgDrawableBmpSet CreateDrawableBmpSet(int width, int height, boolean alpha) {
02     MmgDrawableBmpSet dBmpSet = new MmgDrawableBmpSet();
03     dBmpSet.buffImg = MmgScreenData.GRAPHICS_CONFIG.createCompatibleImage(width, height, alpha ? Transparency.TRANSLUCENT : Transparency.OPAQUE);
04     dBmpSet.graphics = (Graphics2D)dBmpSet.buffImg.getGraphics();
05     dBmpSet.p = new MmgPen();
06     dBmpSet.p.SetGraphics(dBmpSet.graphics);
07     dBmpSet.p.SetAdvRenderHints();
08     dBmpSet.img = new MmgBmp(dBmpSet.buffImg);
09     return dBmpSet;
10 }
//ScreenTestMmgBmp.LoadResources
01 bmpSet = MmgHelper.CreateDrawableBmpSet(bmpCache.GetWidth()/2, bmpCache.GetHeight()/2, true);
02 srcRect = new MmgRect(0, 0, bmpCache.GetHeight()/2, bmpCache.GetWidth()/2);
03 dstRect = new MmgRect(0, 0, bmpCache.GetHeight()/2, bmpCache.GetWidth()/2);
04 bmpSet.p.DrawBmp(bmpCache, srcRect, dstRect);

06 bmpSet.img.SetY(GetY() + MmgHelper.ScaleValue(210));
07 bmpSet.img.SetX(MmgHelper.ScaleValue(650));
08 AddObj(bmpSet.img);
清单 5-28
MmgDrawableBmpSet 类演示 1
```

上面列出的第一段代码展示了手动初始化 `MmgDrawableBmpSet` 对象的标准方法。此外，`CreateDrawableBmpSet` 是一个用于实现此目的的便捷方法。这个方法很重要，因为它展示了所涉及类之间的核心交互。

首先，在第 3 行，使用指定的尺寸和不透明度创建了一个缓冲图像。在第 4 行，创建了对该缓冲图像图形对象的引用。在第 5-7 行，使用缓冲图像的图形对象初始化了一个新的 `MmgPen` 对象。最后，在第 8 行，`MmgBmp` 实例 `img` 被设置为一个基于已加载缓冲图像新创建的 `MmgBmp` 对象。

第二段演示代码展示了该类的实际使用。在第 1-4 行，创建了一个新的可绘制位图集，其尺寸为原始图像的一半。源矩形是左上角正方形，宽度和高度均为原始图像的一半。目标是一个具有相同尺寸的新图像，因此 `srcRect` 和 `dstRect` 是相同的。在第 4 行，使用 `MmgPen` 实例 `p`，将左上角正方形绘制到新图像 `img` 上。在第 6-8 行，调整了图像的位置，并将其添加到默认的绘制例程中。

## 章节总结

在本章中，我们完成了对 MmgBase API 高级类的回顾。我们介绍的这些类开始以新的组合方式使用基类来解决不同的问题。我们的工具箱中有了一堆新工具。让我们来看看它们的总结：

*   **Mmg9Slice**：一个缩放类，使用九宫格技术来调整矩形图像、UI 框架、边框和其他图像的尺寸，而不会失真。

*   **MmgContainer**：一个 API 类，用于表示 API 中的一组 MmgObj 实例。

*   **MmgLabelValuePair**：一个 UI 类，以标签和值对的格式显示文本。提供更高级的文本支持，专为游戏 HUD 和其他游戏统计 UI 设计。

*   **MmgLoadingBar**：一个 API 类，用于表示 API 的加载条或进度条。

*   **MmgSprite**：一个高级类，使用 MmgBmp 对象数组为 API 创建动画 2D 游戏对象。

*   **MmgDrawableBmpSet**：一个高级类，包含在新 MmgBmp 对象上绘制所需的一组字段。该类包含完成配置所需的所有框架和 API 级别类。

这些类开始展现出，当你在坚实的类基础之上添加功能层时所产生的涌现效应。我们在这里回顾的类中，可以访问到一些非常强大的功能。在规划你的下一个伟大游戏时，请务必包含它们。

# 6. 控件类

在本章中，我们将回顾 MmgBase API 的 UI 控件类。这些是我们之前回顾过的高级类，但它们被设计为作为游戏菜单、设置屏幕或其他类型的配置屏幕的一部分来工作。这些类极大地简化了获取一个接受用户输入并运行的游戏屏幕所需的工作。它们还为滚动窗格和大文本块提供了可靠的支持：

*   MmgTextField

*   MmgTextBlock

*   MmgScrollVert / MmgScrollHor / MmgScrollHorVert

*   MmgMenuContainer

*   MmgMenuItem

## 控件类：MmgTextField

`MmgTextField` 类是 UI 控件类组的一部分。它是一个简单的文本字段控件，很像你在网页表单上可能看到的那样。该类旨在为从用户处读取值提供基本支持。

### 静态类成员

下面列出的静态类字段集是我们需要为 `MmgTextField` 类回顾的唯一静态类成员。这里列出的静态字段用于控制该类渲染方式的某些方面，并描述该类的错误事件 ID 和类型。

```
public static int DEFAULT_MAX_LENGTH = 20;
public static int TEXT_FIELD_9_SLICE_OFFSET = MmgHelper.ScaleValue(16);
public static String TEXT_FIELD_CURSOR = "_";
public static long TEXT_FIELD_CURSOR_BLINK_RATE_MS = 350l;
public static int TEXT_FIELD_MAX_LENGTH_ERROR_EVENT_ID = 1;
public static int TEXT_FIELD_MAX_LENGTH_ERROR_TYPE = 0;
清单 6-1
MmgTextField 静态类成员 1
```

第一个字段是文本字段最大长度的默认值。文本字段类支持绘制缩放到指定尺寸的背景图像。为了实现这一点，文本字段类使用了一个 `Mmg9Slice` 对象。列出的第二个字段是用于 `Mmg9Slice` 偏移字段的值。这控制了背景图像的切片方式。下一个字段 `TEXT_FIELD_CURSOR` 控制用作文本字段光标的字符。光标的闪烁速率由闪烁速率静态字段控制。最后两个条目是当该类触发最大长度错误事件时使用的事件 ID 和类型。



### 类字段

`MmgTextField` 类有几个类字段需要我们查看。第一组字段如下所列。这些字段用于控制文本字段的显示方式，以及在发生最大长度错误时触发的事件。

```
private Mmg9Slice bground;
private MmgBmp bgroundSrc;
private boolean cursorBlinkOn;
private long cursorBlinkStart;
private int displayChars;
private MmgEvent errorMaxLength = new MmgEvent(null, "error_max_length", MmgTextField.TEXT_FIELD_MAX_LENGTH_ERROR_EVENT_ID, MmgTextField.TEXT_FIELD_MAX_LENGTH_ERROR_TYPE, null, null);
清单 6-2
MmgTextField 类字段 1
```

第一个类字段是一个 `Mmg9Slice` 对象，用作文本字段的背景边框。紧随其后的 `bgroundSrc` 是作为 `Mmg9Slice` 对象源 `MmgBmp` 的图像。换句话说，`bgroundSrc` 图像将被切片并调整大小，以达到所需的文本字段尺寸。

接下来的两个字段控制光标的闪烁。第一个字段 `cursorBlinkOn` 决定光标是否闪烁，而下一个字段 `cursorBlinkStart` 则被类用于计时光标闪烁速率。`displayChars` 条目决定文本字段一次可以显示的字符数。最后，我们有一个已经准备好、几乎可以立即使用的最大长度错误模板 `errorMaxLength`。

```
private MmgFont font;
private int fontHeight;
private boolean isDirty;
private int maxLength;
private boolean maxLengthOn;
private int padding;
private String textFieldString = "";
清单 6-3
MmgTextField 类字段 2
```

上面列出了我们需要查看的第二组类字段。`font` 字段用于在文本字段中渲染文本。你可以使用 `MmgFont` 的类字段来指定字体大小、类型和颜色。`fontHeight` 字段用于文本的垂直居中计算。文本会尝试在背景图像的高度内居中显示。

`isDirty` 标志是一个布尔值，用于控制类是否在下一个游戏帧中运行某些更新代码。我们不希望类在更新调用期间进行不必要的工作，因此我们使用这个布尔标志来控制所做的工作量。有了这个功能，类只会在光标闪烁或其他事件导致需要更新时，才运行其更新计算。

接下来的两个字段控制是否启用最大长度错误，以及在触发错误事件之前可以在文本字段中输入多少个字符。`padding` 字段用于微调字体在文本字段背景图像中的位置。`textFieldString` 类用于保存文本字段所代表的实际字符串值，而不仅仅是文本字段显示中可见的文本。

### 支持方法详解

`MmgTextField` 类包含的支持方法可分为三大类：背景图像方法、文本字段字体渲染方法和文本字段文本方法。随着我们审查的类变得越来越复杂，它们往往会有更多的支持方法来允许访问其字段。当方法数量开始增多时，我会尽量为你整理好它们。

```
//背景图像方法
public MmgBmp GetBgroundSrc() { ... }
public void SetBgroundSrc(MmgBmp bg) { ... }
public void SetBground(Mmg9Slice bg) { ... }
Public Mmg9Slice GetBground() { ... }
//文本字段字体渲染方法
public MmgFont GetFont() { ... }
public void SetFont(MmgFont f) { ... }
public int GetFontHeight() { ... }
public void SetFontHeight(int i) { ... }
public boolean GetIsDirty() { ... }
public void SetIsDirty(boolean b) { ... }
public int GetPadding() { ... }
public void SetPadding(int i) { ... }
//文本字段文本方法
public int GetDisplayChars() { ... }
public void SetDisplayChars(int i) { ... }
public String GetTextFieldString() { ... }
public void SetTextFieldString(String str) { ... }
public boolean GetMaxLengthOn() { ... }
public void SetMaxLengthOn(boolean b) { ... }
public int GetMaxLength() { ... }
public void SetMaxLength(int i) { ... }
public MmgEvent GetErrorMaxLength() { ... }
public void SetErrorMaxLength(MmgEvent e) { ... }
清单 6-4
MmgTextField 支持方法详解 1
```

我们要查看的第一组方法是类的背景图像方法。这些是 get 和 set 方法，允许你访问背景的源图像以及保存了调整大小后的背景源的 `Mmg9Slice` 实例。

第二组支持方法允许你访问控制文本字段字体渲染方式的类字段。这些是用于字体、字体高度以及用于微调字体位置的填充（padding）的 get 和 set 方法。关于这一组，我只想谈谈 `isDirty` 字段的 get 和 set 方法。通常，你应该让类自己管理其脏标志，但如果你想强制类在下一次调用 `MmgDraw` 之前更新自身，那么可以将 `isDirty` 字段设置为 true。

上面列出的第三组方法允许你与小部件的文本进行交互。前两个方法 `GetDisplayChars` 和 `SetDisplayChars` 允许你设置文本字段一次可以显示的字符数。类存储的实际文本可以通过 `GetTextFieldString` 和 `SetTextFieldString` 方法访问。接下来的三组 get 和 set 方法都旨在允许你开启最大长度检测。你可以通过这六个支持方法开启该功能、设置文本字段允许的最大字符串长度，以及设置在触发最大长度条件时触发的事件。



### 主要方法详情

`MmgTextField` 类有四组主要方法供我们审查。

```
// 类构造函数
public MmgTextField(MmgBmp BgroundSrc, MmgFont Font, int Width, int Height, int Padding, int DisplayChars) { ... }
public MmgTextField(MmgTextField obj) { ... }
// 克隆方法
public MmgObj Clone() { ... }
public MmgTextField CloneTyped() { ... }
// 文本字段管理方法
public boolean ProcessKeyClick(char c, int code) { ... }
public void DeleteChar() { ... }
public void Prep() { ... }
// 标准主要方法
public boolean ApiEquals(MmgTextField obj) { ... }
public boolean MmgUpdate(int updateTick, long currentTimeMs, long msSinceLastFrame) { ... }
public void MmgDraw(MmgPen p) { ... }
清单 6-5
MmgTextField 主要方法详情 1
```

第一组需要审查的主要方法包含两个构造函数。第一个构造函数接受背景图像、字体、宽度、高度、内边距和显示字符长度等参数。这些参数用于设置类字段。尽管此构造函数并未为类设置文本值，但仍会调用 `Prep` 方法来配置文本字段并定位子对象。

接下来列出的构造函数是我们之前见过的一种专用类构造函数。此构造函数接受一个 `MmgTextField` 作为参数，并用它来创建该对象的一个新的、唯一的副本。此构造函数支持类的克隆功能。`Clone` 和 `CloneTyped` 方法用于创建此类的新副本。这些方法使用我们之前见过的专用构造函数，并分别返回一个强制转换后的 `MmgObj` 实例或一个 `MmgTextField` 实例。

第三组方法，即文本字段管理方法，以输入处理器 `ProcessKeyClick` 方法开始。此方法更新 `textFieldString` 字段，并检查最大长度约束是否已启用以及是否已触发。如果已触发，则会触发最大长度事件。`DeleteChar` 方法会从文本字段字符串的末尾移除一个字符。请注意，`ProcessKeyClick` 和 `DeleteChar` 方法在退出前都会将 `isDirty` 标志设置为 true。这组中的最后一个方法是 `Prep` 方法。`Prep` 方法负责设置九宫格背景图像、字体和光标计时。它负责为类的使用做好准备。

最后一组方法是标准的主要方法，我们在此不再赘述。到目前为止，您已经多次见过这些方法：标准游戏引擎绘制例程的更新和绘制方法，分别是 `MmgUpdate` 和 `MmgDraw`，以及一个用于确定对象相等性的 API 级别比较方法 `ApiEquals`。我应该提一下，更新方法负责驱动文本字段的光标。

如果您以更手动的方式使用文本字段，即在游戏屏幕的可绘制对象列表之外，那么如果您希望光标闪烁，就需要在屏幕的更新方法中显式调用该类的 `MmgUpdate` 方法。

### 演示：MmgTextField 类

以下演示代码来自 MmgTestSpace 库中 `ScreenTestMmgTextField` 类的 `LoadResources` 方法。初始代码片段之后的两个方法来自同一个类。这些方法展示了键盘输入处理和 API 事件处理。让我们直接看代码吧！

```
//ScreenTestMmgTextField.LoadResources
01 int width = MmgHelper.ScaleValue(256);
02 int height = MmgHelper.ScaleValue(64);
...
03 bground = MmgHelper.GetBasicCachedBmp("popup_window_base.png");
04 txtField = new MmgTextField(bground, MmgFontData.CreateDefaultMmgFontLg(), width, height, 12, 15);
05 MmgHelper.CenterHorAndVert(txtField);
06 txtField.SetMaxLengthOn(true);
07 txtField.SetEventHandler(this);
08 txtField.SetY(txtField.GetY() - MmgHelper.ScaleValue(30));
09 AddObj(txtField);
//ScreenTestMmgTextField.ProcessKeyClick
01 public boolean ProcessKeyClick(char c, int code) {
02     if(Character.isLetterOrDigit(c)) {
03         txtField.ProcessKeyClick(c, code);
04     } else if(code == 8) {
05         txtField.DeleteChar();
06     }
07     txtFieldText.SetText("文本字段文本: " + txtField.GetTextFieldString());
08     MmgHelper.CenterHor(txtFieldText);
09     return true;
10 }
//ScreenTestMmgTextField.MmgHandleEvent
01 public void MmgHandleEvent(MmgEvent e) {
02     MmgHelper.wr("ScreenTestMsgTextField.HandleMmgEvent: 消息: " + e.GetMessage() + " 标识: " + e.GetEventId());
03     if(e.GetMessage() != null && e.GetMessage().equals("error_max_length") == true) {
04         txtFieldMaxLenError.SetText("最大长度错误 当前时间毫秒: " + System.currentTimeMillis());
05         MmgHelper.CenterHor(txtFieldMaxLenError);
06     }
07 }
清单 6-6
MmgTextField 类演示 1
```

前面的代码片段从准备显示新文本字段所需的代码开始。所需的尺寸在第 1 行和第 2 行计算。用于创建 `Mmg9Slice` 对象的背景源图像在第 3 行通过调用 `MmgHelper` 类的 `GetBasicCachedBmp` 方法加载。第 4–9 行处理 `MmgTextField` 类的创建和配置。

构造函数在第 4 行被调用，传入背景图像、宽度、高度和其他参数来设置类字段。文本字段对象在第 5 行居中，在第 6 行和第 7 行，最大长度错误检测被启用，并注册了一个由 `MmgTextField` 类处理的事件。新对象在第 9 行被添加到游戏屏幕的可绘制对象列表中。

`ScreenTestMmgTextField` 类将输入事件从应用程序的核心代码映射到 `MmgCore` 包的 `GamePanel` 类中的事件处理方法，或者如果您在 C# 中跟进，则是命名空间。`GamePanel` 类将这些事件转发到当前的游戏屏幕。在这种情况下，`ScreenTestMmgTextField` 类接收它们，并在一些内部逻辑之后，根据按下的键调用 `MmgTextField` 类的 `ProcessKeyClick` 或 `DeleteChar` 方法。

如果按下的键是字母数字字符，则将其发送到文本字段对象的 `ProcessKeyClick` 方法。如果按下的键是退格键，则调用该对象的 `DeleteChar` 方法。在下一个代码片段中，列出了 `MmgHandleEvent` 方法。此方法响应来自我们在第一个代码片段中创建的文本字段的事件。在这种情况下，由于我们只有一个事件，我们不检查事件 ID 和类型。我们只需检查事件的消息，并通过在游戏屏幕上显示它来记录该事件。

## 控件类：MmgTextBlock

`MmgTextBlock` 类用于显示较大的文本块，例如故事段落或对话框的一部分。`MmgTextBlock` 类会解析一个文本字符串，并将其分解为一系列字符串，这些字符串能在 `MmgTextBlock` 对象的尺寸内正确显示。此类用于将大量文本分解为多页格式正确的文本。

我想花一点时间指出，直到本章左右，大多数类只需要非常少量的配置。有些类会在内部实现一个准备方法，并在某些字段更改时自动调用它；有些会在构造函数末尾调用准备方法；而另一些则需要您在适当的时间调用适当的方法。这个类属于后一种类型。

最后我要提的是，`MmgTextBlock` 类扩展了 `MmgObj` 类，因此它是可绘制对象集合的一部分。请思考一下这一点。文本字段、标签-值对、`MmgBmp` 图像以及基于 `MmgFont` 的文本类也是如此。游戏引擎实际上并不关心它正在处理哪个类。对于引擎的绘制例程来说，它们看起来都像是 `MmgObj` 类。



### 静态类成员

我们只需要了解两个静态类成员。我在此列出它们。

```
public static String NEW_LINE = "[b]";
public static boolean SHOW_CONTROL_BGROUND_STORY_BOUNDING_BOX = false;
Listing 6-7
MmgTextBlock 静态类成员 1
```

第一个字段是 `NEW_LINE` 字符串。该字段保存了用于标记类源文本中新行的字符串编码。第二个静态类字段是一个布尔标志，指示是否应在 `MmgTextBlock` 对象周围绘制边界框。在定位文本块时，使用此布尔值会非常有帮助。

### 类字段

首先需要查看的字段集如下所示。这些字段控制文本渲染的各个方面。

```
private int STARTING_LINE_COUNT = 20;
private int STARTING_TXT_COUNT = 100;
private MmgColor cl;
private int height;
private int lineHeight;
Listing 6-8
MmgTextBlock 类字段 1
```

前两个字段是私有的，用于初始化存储文本行的数组以及存储单词本身的数组。`MmgColor` 字段 `cl` 用于为文本块中的文本着色。接下来的两个字段跟踪当前 `MmgFont` 的文本块高度和行高。

```
private ArrayList lines;
private int paddingY;
private int paddingX;
private int pages;
private Font paint;
private ArrayList txt;
private int width;
private int words;
Listing 6-9
MmgTextBlock 类字段 2
```

第二组需要查看的类字段列在上面。第一个是 `ArrayList`（如果你使用 C# 则是 `List`）。这些行表示适合当前文本块的可用预格式化文本行。可以将它们视为一个可供显示的行池。系列中的每一页都有相同的可用行来填充文本。接下来的两个类字段 `paddingX` 和 `paddingY` 用于微调定位。`pages` 字段保存显示解析后的文本所需的页数。

`Font` 对象 `paint` 用于在文本块中渲染文本。下一个类字段也是一个 `MmgFont` 对象的 `ArrayList`。此数据结构填充了来自 `lines` 类字段的可用文本行。最后，我们还有文本块的 `width` 以及在解析提供的文本时找到的单词数量。

### 支持方法详解

`MmgTextBlock` 类有一长串支持方法。我在下面列出了它们。我将这些方法分为四组。在开始讨论每个方法之前，请先查看每组方法。

```
//字体控制方法
public MmgColor GetColor() { ... }
public void SetColor(MmgColor Cl) { ... }
public Font GetSpriteFont() { ... }
public void SetSpriteFont(Font p) { ... }
public int GetLineHeight() { ... }
public void SetLineHeight(int l) { ... }
//文本块绘制方法
public int GetHeight() { ... }
public void SetHeight(int h) { ... }
public int GetWidth() { ... }
public void SetWidth(int w) { ... }
public int GetPaddingX() { ... }
public void SetPaddingX(int p) { ... }
public int GetPaddingY() { ... }
public void SetPaddingY(int p) { ... }
//文本块统计方法
public int GetLineCount() { ... }
public int GetPageCount() { ... }
public int GetUsedLineCount() { ... }
public int GetLinesInBox() { ... }
public int GetPages() { ... }
public void SetPages(int p) { ... }
public int GetWordCount() { ... }
public void SetWordCount(int i) { ... }
//文本块数据方法
public ArrayList GetLines() { ... }
public void SetLines(ArrayList a) { ... }
public ArrayList GetTxt() { ... }
public void SetTxt(ArrayList a) { ... }
public MmgFont GetText(int i) { ... }
public void SetText(int i, MmgFont f) { ... }
Listing 6-10
MmgTextBlock 支持方法详解 1
```

第一组方法允许你控制字体在文本块中的绘制方式。你可以访问颜色和用于创建该类绘制文本所用 `MmgFont` 对象的框架字体类。最后两个方法允许你控制行高。此字段用于调整文本块中各行之间的间距。

第二组方法控制文本块的绘制方式：对象的位置、用于格式化文本的尺寸，以及用于微调文本在文本块内显示方式的填充值。第三组方法提供关于已解析文本以及文本在文本块内格式化方式的统计信息。

`GetLineCount` 方法返回适合文本块内的行数。`GetPageCount` 方法返回使用当前文本块尺寸和计算出的行数显示文本时所占用的页数。`GetUsedLineCount` 方法返回当前页面上用于显示文本的行数。`GetLinesInBox` 方法可能看起来有些多余，但它是用于计算框内行数的底层方法。

接下来的两个方法允许你访问 `pages` 字段，最后两个方法允许你访问从提供的文本中解析出的单词数量。最后一组方法允许你获取和设置由该类生成的数据。这些方法不言自明且很少使用，因此我在此不再赘述。请通读它们并确保你理解它们。

```
//构造函数
public MmgTextBlock() { ... }
public MmgTextBlock(MmgTextBlock obj) { ... }
//克隆方法
public MmgObj Clone() { ... }
public MmgTextBlock CloneTyped() { ... }
//文本准备方法
public void PrepLinesInBox(int len) { ... }
public void PrepLinesInBox() { ... }
public void PrepPage(int page) { ... }
public void PrepTextSplit(String text, Font typeFace, int fontSize, int width, FontType fontType) { ... }
//数据重置方法
public void Reset() { ... }
//绘制与比较方法
public void MmgDraw(MmgPen p) { ... }
public boolean ApiEquals(MmgTextBlock obj) { ... }
Listing 6-11
MmgTextBlock 主要方法详解 1
```

我们要查看的第一组主要方法是类的构造函数。第一个构造函数不接受任何参数，并将类置于一个已准备好进行配置但尚未完全准备好绘制到屏幕的状态。由于此类的复杂性，你需要手动运行一些准备方法。第二个构造函数是我们熟知且喜爱的专用构造函数。它为类的克隆功能提供支持。

第二组主要方法是克隆方法。我们已经多次讨论过这些方法，我认为我们可以安全地跳过它们。请自行阅读并确保你理解它们。第三，文本准备方法是该类中迄今为止最重要的方法。它们准备要显示的文本，并在多页文本块的情况下控制显示哪一页文本。

`PrepLinesInBox` 方法将重置 `txt` 数据结构中的条目。请记住，该数据结构用于保存可供显示的可用文本行。`PrepPage` 方法用下一组可用的预格式化行加载 `txt` 数据结构。就文本块而言，这构成了一页。如果有更多行要显示，则可以将下一页加载到视图中。

`Reset` 方法清除类的数据结构，其余方法是游戏引擎方法，我们之前已经见过，因此我在此不再赘述。



### 演示：MmgTextBlock 类

本类的演示代码来自 `MmgTestSpace` 包中 `ScreenTestMmgTextBlock` 类的 `LoadResources` 方法。请务必运行示例应用程序并查看此游戏画面，以便观察该类的实际运行效果。

```
01 txt = "Lorem ipsum dolor sit ...";

03 MmgTextBlock.SHOW_CONTROL_BGROUND_STORY_BOUNDING_BOX = true;
04 txtBlock = new MmgTextBlock();
05 txtBlock.SetLineHeight(MmgFontData.GetTargetPixelHeightScaled() + MmgHelper.ScaleValue(5));
06 txtBlock.SetHeight(MmgHelper.ScaleValue(300));
07 txtBlock.SetWidth(MmgHelper.ScaleValue(400));
08 txtBlock.SetPaddingX(MmgHelper.ScaleValue(txtBlock.GetPaddingX()));
09 txtBlock.SetPaddingY(MmgHelper.ScaleValue(txtBlock.GetPaddingY()));
10 txtBlock.PrepLinesInBox(txtBlock.GetLinesInBox());
11 txtBlock.PrepTextSplit(txt, MmgFontData.GetFontNorm(), MmgFontData.GetFontSize(), MmgHelper.ScaleValue(375), FontType.NORMAL);
12 txtBlock.SetColor(MmgColor.GetWhite());

14 MmgHelper.CenterHorAndVert(txtBlock);
15 txtBlock.PrepPage(0);

17 //必须在文本分割之后执行，以设置每行文本的位置。
18 txtBlock.SetPosition(txtBlock.GetPosition());
19 AddObj(txtBlock);
代码清单 6-12
MmgTextBlock 类演示 1
```

这段代码相当直接。要解析和显示的字符串在第 1 行初始化。它实际上非常长，所以这里做了截断。我们将显示边界框标志设为 true，以便观察文本块对象的尺寸。第 4 行创建了一个默认的 `MmgTextBlock`。第 5 行将行高设置为默认字体高度加上一个填充因子。

在第 6 到 9 行，我们进行了一系列调用，仅用于确保数值已正确缩放。第 10 行初始化了文本块内文本行的占位符。大部分工作在第 11 行完成，通过调用 `PrepTextSplit` 方法。此调用为预格式化的文本行创建条目。第 12 行，通过调用 `SetColor` 方法，将每个占位符行的颜色设置为白色。

前面代码片段中的最后两行代码非常重要。您必须至少调用一次 `SetPosition` 方法来定位对象，这会强制重新定位所有子对象，包括显示在游戏画面上的文本行占位符。最后，您需要通过调用 `AddObj` 方法将对象添加到游戏引擎的绘制例程中。

您应该会注意到，我们只是传入了一个非常复杂的对象，它就会与其他所有对象一起被渲染。这就是我们所构建的通用游戏引擎的强大之处。向可绘制对象集中添加新的、有趣的类非常容易。希望您也能添加一些自己的类。

## 控件类：滚动面板类

接下来要介绍的类将适用于一组被称为滚动面板类的类。它们是 MmgBase API 中的 `MmgScrollHor`、`MmgScrollVert` 和 `MmgScrollHorVert` 类。它们的功能非常相似，因此我不会逐一介绍。我们将介绍 `MmgScrollHor` 类，您可以将学到的知识应用到其他两个滚动面板类中。

### 静态类成员

有几个静态类字段需要介绍。在介绍之前，请先查看以下代码清单。

```
public static boolean SHOW_CONTROL_BOUNDING_BOX = false;
public static int SCROLL_HOR_CLICK_EVENT_TYPE = 0;
public static int SCROLL_HOR_CLICK_EVENT_ID = 3;
public static int SCROLL_HOR_SCROLL_LEFT_EVENT_ID = 4;
public static int SCROLL_HOR_SCROLL_RIGHT_EVENT_ID = 5;
代码清单 6-13
MmgScrollHor 静态类成员 1
```

第一个条目控制标记滚动面板尺寸的边界框的显示。此字段控制所有水平滚动面板，而不仅仅是您正在操作的那个。接下来的四个字段是事件 ID 和类型条目，用于描述滚动面板支持的不同事件。`MmgScrollHor` 滚动面板支持左右滚动事件和滚动面板点击事件。

### 类字段

`MmgScrollHor` 类相对复杂，因此它拥有许多字段，允许您控制该类及其功能的各个方面。这一切都很好；但对于如此复杂的类，您最好从基线（即演示代码）开始，然后在此基础上进行小的修改，直到完全理解该类的功能。我将类字段分组列出如下。

```
//视口和滚动面板字段
private MmgBmp viewPort;
private MmgRect viewPortRect;
private int viewPortWidth;
private MmgBmp scrollPane;
private MmgRect scrollPaneRect;
private int scrollPaneWidth;
//滚动条按钮显示字段
private int scrollBarLeftRightButtonWidth;
private MmgBmp scrollBarLeftButton;
private MmgRect scrollBarLeftButtonRect;
private MmgBmp scrollBarRightButton;
private MmgRect scrollBarRightButtonRect;
private MmgBmp scrollBarCenterButton;
private MmgRect scrollBarCenterButtonRect;
private MmgColor scrollBarCenterButtonColor;
private int scrollBarCenterButtonWidth;
//滚动条显示字段
private boolean scrollBarVisible = false;
private MmgColor scrollBarColor;
private int scrollBarHeight;
private int intervalX;
private double intervalPrctX;
private int offsetXScrollBarCenterButton;
private int offsetXScrollPane;
private boolean isDirty;
//事件字段
private MmgEvent clickScreen = new MmgEvent(null, "hor_click_screen", MmgScrollHor.SCROLL_HOR_CLICK_EVENT_ID, MmgScrollHor.SCROLL_HOR_CLICK_EVENT_TYPE, null, null);
private MmgEvent clickLeft = new MmgEvent(null, "hor_click_left", MmgScrollHor.SCROLL_HOR_SCROLL_LEFT_EVENT_ID, MmgScrollHor.SCROLL_HOR_CLICK_EVENT_TYPE, null, null);
private MmgEvent clickRight = new MmgEvent(null, "hor_click_right", MmgScrollHor.SCROLL_HOR_SCROLL_RIGHT_EVENT_ID, MmgScrollHor.SCROLL_HOR_CLICK_EVENT_TYPE, null, null);
代码清单 6-14
MmgScrollHor 类字段 1
```

第一组字段描述了视口，即滚动面板中呈现滚动条和可见面板的静态部分。这些字段使用 `MmgRect` 对象以及描述每个对象宽度的 `viewPortWidth` 和 `scrollPaneWidth` 字段来描述其位置和尺寸。需要注意的是，某些类字段仅用于描述，不会以任何方式改变对象。随着经验的积累，您会逐渐理解其工作原理；目前，我们专注于介绍基础知识。

第二组字段描述了滚动面板的滚动条按钮。在水平滚动面板中，有一个滚动条位于滚动面板窗口下方。该滚动条有两个侧边按钮和一个中心按钮。左右按钮是可用的，但中心滑块不可用，因为滚动面板类尚未实现鼠标拖拽支持。有一个字段 `scrollBarLeftRightButtonWidth` 用于描述左右滚动条按钮的宽度，另一个字段 `scrollBarCenterButtonWidth` 用于描述中心按钮的宽度。您可以使用 `scrollBarCenterButtonColor` 字段更改中心按钮的颜色，并且所有三个按钮都有对应的 `MmgBmp` 按钮和 `MmgRect` 条目。

我还应该提到，这个类使用了一些默认资源。这些资源被视为游戏引擎运行时的一部分，并作为自动加载目录的一部分加载。这应该为您如何为自己的游戏自定义滚动面板资源提供了可靠的指导。



我们要查看的第三组字段是滚动条显示字段。这些字段用于控制滚动条的显示方式。其中唯一值得直接提及的两个字段是 `intervalX` 字段和 `isDirty` 字段。`intervalX` 类字段控制每次单击按钮时滚动条的移动量。`isDirty` 类字段通过限制更新方法仅在类被标记为“脏”时运行，来防止类执行过多工作。请注意你如何使用这个字段；如果你的滚动面板没有更新，你可能需要将该对象标记为“脏”。

最后一组字段是类事件。事件对象已经准备就绪；它们只需要设置一个目标事件处理器即可投入使用。至此，我们对类字段的回顾就结束了。接下来，我们将看看该类的支持方法。

### 支持方法详解

`MmgScrollHor` 类有一长串支持方法。我们不会在此详细涵盖所有方法，因为它们只是 get 和 set 方法。我已将这些方法按照对应的类字段分组列出如下。

```
//视口和滚动面板方法
public MmgBmp GetViewPort() { ... }
public void SetViewPort(MmgBmp ViewPort) { ... }
public MmgRect GetViewPortRect() { ... }
public void SetViewPortRect(MmgRect r) { ... }
public MmgBmp GetScrollPane() { ... }
public void SetScrollPane(MmgBmp ScrollPane) { ... }
public MmgRect GetScrollPaneRect() { ... }
public void SetScrollPaneRect(MmgRect r) { ... }
//滚动条按钮显示字段
public int GetScrollBarLeftRightButtonWidth() { ... }
public void SetScrollBarLeftRightButtonWidth(int w) { ... }
public MmgBmp GetScrollBarLeftButton() { ... }
public void SetScrollBarLeftButton(MmgBmp b) { ... }
public MmgRect GetScrollBarLeftButtonRect() { ... }
public void SetScrollBarLeftButtonRect(MmgRect r) { ... }
public MmgBmp GetScrollBarRightButton() { ... }
public void SetScrollBarRightButton(MmgBmp b) { ... }
public MmgRect GetScrollBarRightButtonRect() { ... }
public void SetScrollBarRightButtonRect(MmgRect r) { ... }
public MmgBmp GetScrollBarCenterButton() { ... }
public void SetScrollBarCenterButton(MmgBmp b) { ... }
public MmgRect GetScrollBarCenterButtonRect() { ... }
public void SetScrollBarCenterButtonRect(MmgRect r) { ... }
public MmgColor GetScrollBarCenterButtonColor() { ... }
public void SetScrollBarCenterButtonColor(MmgColor c) { ... }
public int GetScrollBarCenterButtonWidth() { ... }
public void SetScrollBarCenterButtonWidth(int w) { ... }
//滚动条显示方法
public boolean GetScrollBarVisible() { ... }
public void SetScrollBarVisible(boolean b) { ... }
public MmgColor GetScrollBarColor() { ... }
public void SetScrollBarColor(MmgColor c) { ... }
public int GetScrollBarHeight() { ... }
public void SetScrollBarHeight(int h) { ... }
public int GetOffsetX() { ... }
public void SetOffsetX(int OffsetX) { ... }
public int GetIntervalX() { ... }
public void SetIntervalX(int IntervalX) { ... }
public boolean GetIsDirty() { ... }
public void SetIsDirty(boolean IsDirty) { ... }
//事件方法
public MmgEvent GetClickScreen() { ... }
public void SetClickScreen(MmgEvent e) { ... }
public MmgEvent GetClickLeft() { ... }
public void SetClickLeft(MmgEvent e) { ... }
public MmgEvent GetClickRight() { ... }
public void SetClickRight(MmgEvent e) { ... }
public MmgEventHandler GetEventHandler() { ... }
public void SetEventHandler(MmgEventHandler e) { ... }
清单 6-15
MmgScrollHor 支持方法详情 1
```

有一组方法我想提一下：`GetEventHandler` 和 `SetEventHandler` 方法。`SetEventHandler` 方法实际上为所有三个类事件设置了目标事件处理器。而 `GetEventHandler` 方法仅从点击屏幕事件中返回目标事件处理器。在处理这个类的事件时请记住这一点。请务必仔细查看前面列出的支持方法，并了解它们的工作原理。再次强调，在大多数情况下，它们只是简单的 get 和 set 方法。

### 主要方法详解

下面列出的第一组主要方法包含构造函数和克隆方法。先看一下它们，然后我们再来讨论。

```
public MmgScrollHor(MmgBmp ViewPort, MmgBmp ScrollPane, MmgColor ScrollBarColor, MmgColor ScrollBarCenterButtonColor, int IntervalX) { ... }
public MmgScrollHor(MmgScrollHor obj) { ... }
public MmgObj Clone() { ... }
public MmgScrollHor CloneTyped() { ... }
清单 6-16
MmgScrollHor 主要方法详情 1
```

列出的第一个构造函数是标准的滚动面板构造函数。你需要准备好视口和滚动面板的 `MmgBmp` 对象。第二个构造函数是一个专用构造函数，它接受一个 `MmgScrollHor` 对象作为参数，并用它来创建该对象的一个新的、独立的副本。最后列出的两个方法是克隆方法。它们使用专用构造函数创建当前对象的克隆，并在返回之前将其转换为必要的对象类型。

```
public void MmgDraw(MmgPen p) { ... }
public boolean MmgUpdate(int updateTick, long currentTimeMs, long msSinceLastFrame) { ... }
public boolean ApiEquals(MmgScrollHor obj) { ... }
public void PrepDimensions() { ... }
public void PrepScrollPane() { ... }
public boolean ProcessDpadRelease(int dir) { ... }
public boolean ProcessScreenClick(int x, int y) { ... }
清单 6-17
MmgScrollHor 主要方法详情 2
```

第二组主要方法以标准的游戏引擎绘制例程方法 `MmgDraw` 和 `MmgUpdate` 开始。紧随其后的是一个 API 级别的比较方法，用于测试两个 `MmgScrollHor` 对象是否相等。接下来的两个方法用于准备滚动面板。其过程如下：构造函数调用 `PrepDimensions` 方法，该方法通过正确定位所有不同的对象来准备它们。然后调用 `PrepScrollPane` 方法，该方法负责重置滚动间隔以及用于将滚动面板绘制到视口显示矩形上的绘图表面。

最后列出的两个方法是输入事件处理器。默认情况下，滚动面板配置为使用方向键向左、向右、向上和向下滚动，具体取决于你正在使用的是哪一个。你也可以点击按钮来移动滚动面板。当然，你也可以点击滚动面板并触发一个屏幕点击事件。



### 演示：MmgScrollHor 类

以下代码块中的示例代码来自 MmgTestSpace 库中 `ScreenTestMmgScrollHor` 类的 `LoadResources` 方法。请务必运行示例应用程序并查看此测试屏幕，以观察该类的实际运行效果。

```
01 dBmpSetScrollPane = MmgHelper.CreateDrawableBmpSet(hund4, hund2, false, MmgColor.GetBlack());
02 dBmpSetScrollPane.graphics.setColor(Color.RED);
03 dBmpSetScrollPane.graphics.fillRect(0, 0, hund4 / 4, hund2);
04 dBmpSetScrollPane.graphics.setColor(Color.BLUE);
05 dBmpSetScrollPane.graphics.fillRect(hund4 / 4, 0, hund4 / 4, hund2);
06 dBmpSetScrollPane.graphics.setColor(Color.GREEN);
07 dBmpSetScrollPane.graphics.fillRect(hund4 / 2, 0, hund4 / 4, hund2);

09 dBmpSetViewPort = MmgHelper.CreateDrawableBmpSet(hund2, hund2, false, MmgColor.GetBlack());
10 dBmpSetViewPort.graphics.setColor(Color.LIGHT_GRAY);
11 dBmpSetViewPort.graphics.fillRect(0, 0, hund2, hund2);

13 vPort = dBmpSetViewPort.img;
14 sPane = dBmpSetScrollPane.img;

16 sBarColor = MmgColor.GetLightGray();
17 sBarSldrColor = MmgColor.GetGray();
18 sBarWidth = MmgHelper.ScaleValue(15);
19 sBarSldrHeight = MmgHelper.ScaleValue(30);
20 interval = 10;

22 scrollHor = new MmgScrollHor(vPort, sPane, sBarColor, sBarSldrColor, sBarWidth, sBarSldrHeight, interval);
23 scrollHor.SetIsVisible(true);
24 scrollHor.SetWidth(sWidth);
25 scrollHor.SetHeight(sHeight + scrollHor.GetScrollBarHeight());
26 scrollHor.SetEventHandler(this);
27 MmgScrollHor.SHOW_CONTROL_BOUNDING_BOX = true;
28 MmgHelper.CenterHorAndVert(scrollHor);
29 AddObj(scrollHor);
清单 6-18
MmgScrollHor 类演示 1
```

上述代码片段来自 `ScreenTestMmgScrollHor` 类的 `LoadResources` 方法。让我们来看看这些代码！第 1 到 7 行代码用于创建一个滚动面板 `MmgBmp` 对象，该对象与视口高度相同，但宽度要大得多。滚动面板图像填充了不同颜色的矩形，以便我们轻松观察到面板在移动。

类似地，视口 `MmgBmp` 在第 9 行创建，并在第 10 和 11 行填充为浅灰色。这样就完成了滚动面板和视口对象的准备工作，以便与 `MmgScrollHor` 实例一起使用。在第 13 和 14 行，我们让一个局部变量指向用于创建视口和滚动面板的 `MmgDrawableBmpSet` 中的 `MmgBmp` 图像部分。用于构建滚动视图的其余颜色和尺寸在第 16 到 20 行初始化。`MmgScrollHor` 对象在第 22 行初始化，其可见性在第 23 行设置。尺寸在第 24 和 25 行设置。

请注意，滚动视图的高度包含了滚动条的高度。在第 26 行，事件处理器被设置为游戏屏幕。最后，在第 27 到 29 行，边界框显示标志被设置为 true。滚动视图在水平和垂直方向上居中，并通过在第 29 行调用 `AddObj` 方法将其添加到游戏屏幕的可绘制对象集合中。

```
01 public boolean ProcessDpadRelease(int dir) {
02     MmgHelper.wr("ScreenTestMmgScrollHor.ProcessDpadRelease: " + dir);
03     scrollHor.ProcessDpadRelease(dir);
04     isDirty = true;
05     return true;
06 }
01 public boolean ProcessMouseClick(int x, int y) {
02     MmgHelper.wr("ScreenTestMmgScrollHor.ProcessScreenClick");
03     scrollHor.ProcessScreenClick(x, y);
04     isDirty = true;
05     return true;
06 }
01 public boolean MmgUpdate(int updateTick, long currentTimeMs, long msSinceLastFrame) {
02     lret = false;

04     if (pause == false && isVisible == true) {
05         if (isDirty == true) {
06            super.GetObjects().SetIsDirty(true);

08             if (super.MmgUpdate(updateTick, currentTimeMs, msSinceLastFrame) == true) {
09                 lret = true;
10             }
11         }
12     }

14     return lret;
15 }
清单 6-19
MmgScrollHor 类演示 2
```

我们知道滚动视图控件会通过方向键和鼠标的输入进行更新，因此可以预期 `ScreenTestMmgScrollHor` 的 `MmgUpdate` 方法会支持调用其子对象的更新方法。上述代码片段中的第一个方法是 `ProcessDpadRelease` 方法。它将来自游戏屏幕的输入传递给滚动面板，并将屏幕标记为“脏”（dirty），以强制进行更新调用。下一个要查看的方法 `ProcessMouseClick` 的工作方式与方向键方法相同。它将鼠标点击事件发送给滚动面板进行处理。

代码片段中的最后一个方法是 `ScreenTestMmgScrollHor` 类的 `MmgUpdate` 方法。乍一看，我们没有看到对滚动视图对象进行任何更新调用。如果屏幕未暂停且可见，我们会检查本地的 `isDirty` 标志是否设置为 true。如果是，则调用父类方法 `GetObjects`，该方法返回游戏屏幕的所有子对象。

调用 `SetIsDirty` 并传入 true 参数，会将所有子对象的 `isDirty` 标志设置为 true。然后，在第 8 行，调用父类的 `MmgUpdate` 方法。这将确保所有子对象（包括 `MmgScrollHor` 对象）的更新方法都被调用。

## 控件类：MmgMenuContainer

`MmgMenuContainer` 类是一个控件类，它与 `MmgGameScreen` 和 `MmgMenuItem` 类协同工作。它与我们之前查看的控件不完全相同。那些控件是非常独立的、基于 UI 的控件。而 `MmgMenuContainer` 旨在与显示菜单的游戏屏幕以及 `MmgMenuItem` 实例分担一些职责。

### 类字段

`MmgMenuContainer` 类只有一个我们需要关注的类字段。

```
private ArrayList container;
清单 6-20
MmgMenuContainer 类字段 1
```

`container` 字段保存了此容器对象显示的所有菜单项。

### 支持方法详情

`MmgMenuContainer` 类配备了一组支持方法，允许您与其包含的数据进行交互。我将这些方法分组列出如下。在我们查看它们之前，请先看一下。

```
//数据控制方法
public void Add(MmgMenuItem obj) { ... }
public void Remove(MmgMenuItem obj) { ... }
public void Clear() { ... }
public int GetCount() { ... }
//数据访问方法
public MmgMenuItem[] GetArray() { ... }
public ArrayList GetContainer() { ... }
public void SetContainer(ArrayList aTmp) { ... }
清单 6-21
MmgMenuContainer 支持方法详情 1
```

上述类方法分为两组。第一组让您可以控制类的数据。您可以使用这些方法向容器添加和移除项目，以及清空容器。`GetCount` 方法返回容器中子对象的数量。第二组方法让您可以访问容器的数据。您可以使用 `GetArray` 方法获取子对象的数组表示形式，或者使用列出的两个 get 和 set 方法与 `container` 字段进行交互。



### 主要方法详情

该类的核心方法分为三组：构造方法、克隆方法和通用主要方法。

```
//构造方法
public MmgMenuContainer() { ... }
public MmgMenuContainer(ArrayList objects) { ... }
public MmgMenuContainer(MmgMenuContainer obj) { ... }
//克隆方法
public MmgObj Clone() { ... }
public MmgMenuContainer CloneTyped() { ... }
//通用主要方法
public void MmgDraw(MmgPen p) { ... }
public boolean ApiEquals(MmgMenuContainer obj) { ... }
代码清单 6-22
MmgMenuContainer 主要方法详情 1
```

前面列出的第一个构造方法不接收参数，会创建一个空但立即可用的菜单容器。第二个构造方法接收一个菜单项的 `ArrayList` 作为参数，并用它来初始化容器的子对象。最后一个列出的构造方法是一个专用构造方法，它接收一个 `MmgMenuContainer` 作为参数，并用它来创建该对象的一个新的、独立的副本。

接下来列出的方法组是克隆方法，用于创建当前对象的独立副本。`MmgDraw` 方法由游戏引擎的绘制例程使用，而 `ApiEquals` 方法则提供 API 级别的比较功能。至此，主要方法的介绍就结束了。接下来，让我们看看这个类的实际应用。

### 演示：MmgMenuContainer 类

下面的示例代码片段演示了如何准备菜单项 `MmgBmp` 对象以供使用。

```
//ScreenTestMmgMainMenu.LoadResources
01 key = "bmpMenuItemStartGame1p";
02 imgId = MmgHelper.ContainsKeyString(key, "start_game_1p.png", classConfig);
03 lval = MmgHelper.GetBasicCachedBmp(imgId);
04 menuStartGame1P = lval;
05 if (menuStartGame1P != null) {
06     MmgHelper.CenterHor(menuStartGame1P);
07     menuStartGame1P.GetPosition().SetY(menuSubTitle.GetY() + menuSubTitle.GetHeight() + MmgHelper.ScaleValue(10));
08     menuStartGame1P = MmgHelper.ContainsKeyMmgBmpScaleAndPosition("menuStartGame1p", menuStartGame1P, classConfig, GetPosition());
09 }

11 key = "bmpMenuItemStartGame2p";
12 imgId = MmgHelper.ContainsKeyString(key, "start_game_2p.png", classConfig);
13 lval = MmgHelper.GetBasicCachedBmp(imgId);
14 menuStartGame2P = lval;
15 if (menuStartGame2P != null) {
16     MmgHelper.CenterHor(menuStartGame2P);
17     menuStartGame2P.GetPosition().SetY(menuStartGame1P.GetY() + menuStartGame1P.GetHeight() + MmgHelper.ScaleValue(10));
18     menuStartGame2P = MmgHelper.ContainsKeyMmgBmpScaleAndPosition("menuStartGame2p", menuStartGame2P, classConfig, GetPosition());
19 }

21 key = "bmpMenuItemExitGame";
22 imgId = MmgHelper.ContainsKeyString(key, "exit_game.png", classConfig);
23 lval = MmgHelper.GetBasicCachedBmp(imgId);
24 menuExitGame = lval;
25 if (menuExitGame != null) {
26     MmgHelper.CenterHor(menuExitGame);
27     menuExitGame.GetPosition().SetY(menuStartGame2P.GetY() + menuStartGame2P.GetHeight() + MmgHelper.ScaleValue(10));
28     menuExitGame = MmgHelper.ContainsKeyMmgBmpScaleAndPosition("menuExitGame", menuExitGame, classConfig, GetPosition());
29 }
代码清单 6-23
MmgMenuContainer 类演示 1
```

前面的每个代码块都类似，只是针对不同的菜单项设计。仔细阅读代码，确保你理解它。注意类配置文件是如何用于定制示例菜单屏幕的。

```
//ScreenTestMmgMainMenu.DrawScreen
01 pause = true;
02 if(menu == null) {
03     menu = new MmgMenuContainer();
04     menu.SetMmgColor(null);
05     MmgMenuItem mItm = null;

07     if (menuStartGame1P != null) {
08         mItm = MmgHelper.GetBasicMenuItem(handleMenuEvent, "Main Menu Start Game 1P", HandleMainMenuEvent.MAIN_MENU_EVENT_START_GAME_1P, HandleMainMenuEvent.MAIN_MENU_EVENT_TYPE, menuStartGame1P);
09         mItm.SetSound(menuSound);
10         menu.Add(mItm);
11     }

13     if (menuStartGame2P != null) {
14         mItm = MmgHelper.GetBasicMenuItem(handleMenuEvent, "Main Menu Start Game 2P", HandleMainMenuEvent.MAIN_MENU_EVENT_START_GAME_2P, HandleMainMenuEvent.MAIN_MENU_EVENT_TYPE, menuStartGame2P);
15         mItm.SetSound(menuSound);
16         menu.Add(mItm);
17     }

19     if (menuExitGame != null) {
20         mItm = MmgHelper.GetBasicMenuItem(handleMenuEvent, "Main Menu Exit Game", HandleMainMenuEvent.MAIN_MENU_EVENT_EXIT_GAME, HandleMainMenuEvent.MAIN_MENU_EVENT_TYPE, menuExitGame);
21         mItm.SetSound(menuSound);
22         menu.Add(mItm);
23     }

25     SetMenuStart(0);
26     SetMenuStop(menu.GetCount() - 1);
27     SetMenu(menu);
28     SetMenuOn(true);
29 }
30 isDirty = false;
31 pause = false;
代码清单 6-24
MmgMenuContainer 类演示 2
```

上面列出的代码片段来自 `ScreenTestMmgMainMenu` 的 `DrawScreen` 方法。此方法用于初始化游戏屏幕的菜单系统。注意，在第 1 行，屏幕被暂停。这是为了防止因配置不完整而导致的渲染伪影。在第 2 行，我们检查菜单是否为 null 或者是否已经配置好。在第 3 行，我们设置菜单容器并将颜色设为 null。现在我们将开始加载菜单项。这些项受到保护，如果所需的图像为 null，则阻止加载。

看看第 7–11 行。这是加载新菜单项的基本过程。注意，我们使用 `MmgHelper` 的 `GetBasicMenuItem` 方法来创建一个新的菜单项。在第 9 行，我们设置了选中菜单项时播放的声音；在第 10 行，我们将新项添加到菜单中。接下来的两个菜单项遵循相同的过程。看看第 13–17 行和第 19–23 行的代码。确保你理解代码并跟上正在发生的事情。

第 25 和 26 行的代码设置了菜单的起始和结束索引。在这种情况下，菜单使用了我们加载的全部子对象集合。一个重要的方法调用发生在第 27 行。这是将菜单激活为屏幕菜单系统的地方。最后，在第 28 行，菜单被启用。在方法退出之前，我们将 `pause` 字段设置为 false。至此，我们对 `MmgMenuContainer` 类的介绍就结束了。接下来要看的类是 `MmgMenuItem` 类。

## 控件类：MmgMenuItem

`MmgMenuItem` 类被 `MmgMenuContainer` 类用作由容器显示的菜单项。`MmgMenuItem` 类支持不同的状态：正常、选中和未激活。该类还支持在菜单项是当前选中项时播放声音。

### 静态类成员

`MmgMenuItem` 类有几个静态类字段，用于定义项可以处于的不同状态。它还支持显示边界框，正如我们在其他控件类中看到的那样。

```
public static int STATE_NONE = -1;
public static int STATE_NORMAL = 0;
public static int STATE_SELECTED = 1;
public static int STATE_INACTIVE = 2;
public static boolean SHOW_MENU_ITEM_BOUNDING_BOX = false;
代码清单 6-25
MmgMenuItem 静态类成员 1
```

上面列出的类字段以四个条目开头，用于指示不同的菜单项状态。最后一个条目启用边界框的显示，以便你可以看到菜单项的尺寸。



### 类字段

`MmgMenuItem` 类包含几个字段。我在下面将它们列出。这些字段主要处理菜单项的显示及其状态管理。

```
private MmgEvent eventPress;
private MmgObj normal;
private MmgObj selected;
private MmgObj inactive;
private MmgObj current;
private MmgSound sound;
private int state;
清单 6-26
MmgMenuItem 类字段 1
```

`eventPress` 字段是菜单项的事件。如果菜单项被点击，该事件会触发并发送到已注册的事件处理器。这就是你响应菜单项事件的方式。接下来的四个类字段都是 `MmgObj` 实例，用于在不同状态下显示菜单项。`sound` 字段保存了菜单项被选中时应播放的声音。列表中的最后一个条目是 `state` 字段，它跟踪菜单项的当前状态。`state` 决定了菜单项是正常、选中、未激活还是当前状态。

### 支持方法详情

该类的支持方法在下面列出。请花点时间查看它们。它们基本上都只是字段访问方法。

```
public MmgEvent GetEventPress() { ... }
public void SetEventPress(MmgEvent e) { ... }
public MmgObj GetNormal() { ... }
public void SetNormal(MmgObj m) { ... }
public MmgObj GetSelected() { ... }
public void SetSelected(MmgObj m) { ... }
public MmgObj GetInactive() { ... }
public void SetInactive(MmgObj m) { ... }
public MmgSound GetSound() { ... }
public void SetSound(MmgSound Sound) { ... }
public void SetState(int i) { ... }
public int GetState() { ... }
清单 6-27
MmgMenuItem 支持方法详情 1
```

我按照之前审查过的对应类字段的顺序列出了这些支持方法。它们只是简单的 get 和 set 方法，因此我在此不再赘述。需要说明的是，`SetState` 方法比你想象的要复杂一些。它会检查正在设置的状态，并确定是否需要播放声音。它会更新一些内部类字段，并重置菜单项的尺寸以匹配新状态。

### 主要方法详情

`MmgMenuItem` 类的主要方法在下面分三组列出。在我们审查这些方法之前，请花点时间查看它们。

```
//构造方法
public MmgMenuItem() { ... }
public MmgMenuItem(MmgEvent me, MmgObj Normal, MmgObj Selected, MmgObj Inactive, int State){ ... }
public MmgMenuItem(MmgMenuItem obj){ ... }
//克隆方法
public MmgObj Clone(){ ... }
public MmgMenuItem CloneTyped(){ ... }
//通用主要方法
public void MmgDraw(MmgPen p){ ... }
public boolean ApiEquals(MmgMenuItem obj) { ... }
清单 6-28
MmgMenuItem 主要方法详情 1
```

列出的第一个构造方法创建了一个不适合使用的空类实例。此构造方法期望你在使用前进一步配置该类。第二个构造方法是一个完整构造方法，它为每个主要的类字段都接受一个参数。第三个构造方法是一个专门的类构造方法，它接受一个 `MmgMenuItem` 对象作为实例，并使用它来创建该对象的一个新的、唯一的副本。

克隆方法使用专门的构造方法来创建类的副本，并在返回之前将其转换为指定的类型。最后一组方法只是标准的通用方法。`MmgDraw` 方法是游戏引擎绘制例程的一部分，而 `ApiEquals` 方法是一个 API 级别的比较方法。

### 演示：MmgMenuItem 类

下面发布的演示代码来自 `MmgBase` 库中的 `MmgHelper` 类。在我们审查该方法之前，请花点时间查看它。

```
01 public static MmgMenuItem GetBasicMenuItem(MmgEventHandler handler, String name, int eventId, int eventType, MmgBmp img) {
02     MmgMenuItem itm;
03     itm = new MmgMenuItem();
04     itm.SetNormal(img);
05     itm.SetSelected(img);
06     itm.SetInactive(img);
07     itm.SetPosition(img.GetPosition());
08     itm.SetState(MmgMenuItem.STATE_NORMAL);
09     itm.SetEventPress(new MmgEvent(handler, name, eventId, eventType, handler, null));
10     itm.SetMmgColor(null);
11     return itm;
12 }
清单 6-29
MmgMenuItem 类演示
```

`GetBasicMenuItem` 方法是一个便捷的快速访问方法，它创建一个简单的菜单项，用于与 `MmgMenuContainer` 对象配合使用。该方法被简化了，因为它对所有菜单项状态都使用同一个 `MmgBmp` 对象。请注意，菜单项的事件处理器是由该方法准备的。这个例子向你展示了如何实例化一个新的菜单项对象，但我通常会直接使用这个辅助方法。

## 章节总结

在本章中，我们完成了对 MmgBase API 控件类的审查。我们涵盖的这些类具有复杂的实现和高级功能。请注意，无论类变得多么复杂，只要它扩展了 `MmgObj` 类，我们仍然可以将其直接插入游戏屏幕并使其工作。这就是游戏引擎开始展现其强大之处。让我们看看刚刚审查过的类的总结：

*   MmgTextField：一个 API 类，用于表示用户可以输入文本的文本字段。
*   MmgTextBlock：一个 API 类，用于表示多页文本块。
*   MmgScrollHor/MmgScrollVert/MmgScrollHorVert：一组用于表示可滚动面板的 API 类。
*   MmgMenuContainer：一个 API 类，用于表示菜单系统。此类作为其菜单系统的一部分插入到 `MmgGameScreen` 类中。
*   MmgMenuItem：由 `MmgMenuContainer` 类使用的菜单项类。

我们工具库的力量正在呈指数级增长。现在我们刚刚添加了一系列 UI 控件，你可以在游戏的菜单系统或游戏 HUD 中使用它们。我们没有定义一个复杂的图形 API，而是定义了一系列可绘制对象，它们的行为类似于自包含的 UI 控件。这里有足够的内容来创建可靠的菜单、游戏设置屏幕等等。我希望你能看到我们正在使用的设计的灵活性和强大之处。在构建你的下一个游戏时，请记住这些类。

# 7. 动画类

在本章中，我们将审查一组动画类，这些类可用于为你下一个游戏的某些元素提供基本的动画支持。特别是 `MmgPositionTween` 类，它是一个高级实现，考虑了子对象、时间、绘制和定位。这个类需要插入到游戏引擎的绘制例程中，并接收更新调用以在动画期间保持正确的时间：

*   MmgPulse
*   MmgPositionTween/MmgSizeTween

## 动画类：MmgPulse

`MmgPulse` 类为简单动画提供支持。它专门设计用于脉冲式动画，或者动画返回起点并重复。脉冲动画的一个很好的例子是火炬，它从暗淡到明亮闪烁，然后再变回暗淡。

需要说明的是，这个类没有扩展 `MmgObj` 类，因此它不属于可绘制对象集合。这个类不会被绘制到屏幕上。相反，`MmgPulse` 类会更新一个 `MmgVector2` 对象，该对象可以是对活动对象位置的引用，也可以用于驱动对象的属性。



### 类字段

下面列出的第一组类字段用于控制脉冲的起始与停止限制、方向以及变化速率。请查看所列出的字段，稍后我们将逐一进行说明。

```
private MmgVector2 adjScaling;
private MmgVector2 baseLineScaling;
private double change;
private double changePerMs;
private int direction; // 增长或收缩，1 或 -1
清单 7-1
MmgPulse 类字段 1
```

列出的第一个字段 `adjScaling` 代表脉冲的顶点，即脉冲开始返回起点的位置。第二个字段 `baseLineScaling` 代表脉冲的起点。`change` 字段表示必须发生的总变化量。将脉冲理解为从起点出发的一段距离，而 `change` 则是脉冲必须覆盖的总距离，这样理解最为简单。由此，`changePerMs` 字段就是脉冲的变化速率，而 `direction` 字段，你猜对了，就是脉冲的方向。

```
private long timeDiff;
private long timeFlip;
private long timeStart;
private long timeTotal;
清单 7-2
MmgPulse 类字段 2
```

第二组类字段包含四个条目供我们查看。这些字段主要处理脉冲动画中的时间相关逻辑。第一个条目 `timeDiff` 跟踪当前时间与 `timeStart` 类字段所保存值之间的时间差（以毫秒为单位）。下一个条目 `timeFlip` 跟踪动画进行到多长时间时脉冲方向会发生改变。`timeStart` 类字段标记脉冲动画开始的起始时间（以毫秒为单位）。`timeTotal` 类字段保存动画持续的总时间（同样以毫秒为单位）。

### 支持方法详情

`MmgPulse` 类提供了支持方法，允许访问该类的大部分字段。我们审查过的字段中，唯一没有 get 和 set 方法的是 `timeDiff` 字段，因为它是在对象更新方法调用过程中动态计算的。我将支持方法的顺序与我们刚刚审查的类字段顺序保持一致。

```
public MmgVector2 GetAdjScaling() { ... }
public void SetAdjScaling(MmgVector2 v) { ... }
public MmgVector2 GetBaseLineScaling() { ... }
public void SetBaseLineScaling(MmgVector2 v) { ... }
public double GetChange() { ... }
public void SetChange(double c) { ... }
public double GetChangePerMs() { ... }
public void SetChangePerMs(double d) { ... }
public long GetTimeFlip() { ... }
public void SetTimeFlip(long l) { ... }
public long GetTimeStart() { ... }
public void SetTimeStart(long l) { ... }
public long GetTimeTotal() { ... }
public void SetTimeTotal(long l) { ... }
清单 7-3
MmgPulse 支持方法详情 1
```

由于这些只是简单的 get 和 set 方法，我就不详细说明了。与任何类一样，可能并不清楚如何正确使用这些方法。最佳方法是从演示代码入手，建立基本功能基线，然后进行实验，直到你理解该类字段和方法应如何使用。

### 主要方法详情

我们需要为 `MmgPulse` 类审查的主要方法如下所列。它们非常直接明了。这些是我们在 API 审查中之前见过的标准主要方法。

```
public MmgPulse(int startDir, long totalMs, double chng, MmgVector2 blS) { ... }
public MmgPulse(MmgPulse obj) { ... }
public MmgPulse Clone() { ... }
public String ApiToString() { ... }
public boolean ApiEquals(MmgPulse obj) { ... }
清单 7-4
MmgPulse 主要方法详情 1
```

如上所列的前两个方法是类的构造函数，正如你所料。第一个构造函数接收所有主要类字段的参数。每个参数用于初始化其关联的字段。此构造函数创建一个新的 `MmgPulse` 对象，该对象在给定当前参数的情况下即可使用。在尝试更奇特的脉冲参数之前，你应该确保理解脉冲类的工作原理。

列出的第二个构造函数是一个专用构造函数，用于创建现有 `MmgPulse` 对象的一个新的、独立的副本。此方法为该类的克隆功能（即 `Clone` 方法）提供支持。最后但同样重要的是，有两个 API 级别的方法用于将类表示为字符串以及比较两个 `MmgPulse` 对象。这些方法也非常直接，因此我在此不再赘述。请务必仔细查看这些主要方法并理解它们的工作原理。



### 演示：MmgPulse 类

在本演示部分，我们将了解 `MmgPulse` 类的实际应用。这里要查看的代码来自 MmgTestSpace 包（在 C# 中称为命名空间）中 `ScreenTestMmgContainer` 类的 `LoadResources` 和 `MmgUpdate` 方法。

```
01 frame1 = MmgHelper.GetBasicCachedBmp("soldier_frame_1.png");
02 frame1 = MmgBmpScaler.ScaleMmgBmp(frame1, 2.0f, true);
03 MmgHelper.CenterHorAndVert(frame1);
04 frame1.SetY(frame1.GetY() - MmgHelper.ScaleValue(80));
05 frame1.SetX(frame1.GetX() - MmgHelper.ScaleValue(110));
06 AddObj(frame1);

08 frame2 = MmgHelper.GetBasicCachedBmp("soldier_frame_2.png");
09 frame2 = MmgBmpScaler.ScaleMmgBmp(frame2, 2.0f, true);
10 MmgHelper.CenterHorAndVert(frame2);
11 frame2.SetY(frame2.GetY() - 0);

13 frame3 = MmgHelper.GetBasicCachedBmp("soldier_frame_3.png");
14 frame3 = MmgBmpScaler.ScaleMmgBmp(frame3, 2.0f, true);
15 MmgHelper.CenterHorAndVert(frame3);
16 frame3.SetY(frame2.GetY() + MmgHelper.ScaleValue(80));

18 holder = new MmgContainer();
19 holder.Add(frame2);
20 holder.Add(frame3);
21 holder.SetWidth(frame1.GetWidth());
22 holder.SetHeight(MmgHelper.ScaleValue(160));
23 MmgHelper.CenterHorAndVert(holder);
24 AddObj(holder);

26 posTmp = frame1.GetPosition().Clone();
27 pulse = new MmgPulse(1, 2000l, 0.75d, frame1.GetPosition().Clone());
清单 7-5
MmgPulse 类演示 1
```

上面列出的代码片段展示了加载一个将被 `MmgPulse` 动画使用的图像资源的过程。你可以通过打开示例应用程序并导航到“Screen Test Mmg Container and Mmg Pulse”示例游戏屏幕来查看这个测试屏幕。在第 2 行和第 3 行，图像被放大 100% 并定位在屏幕中央。第 4 行和第 5 行确定了最终位置，第 6 行将 `MmgBmp` 对象添加到游戏屏幕的可绘制对象列表中。

接下来的两个代码块与我们刚刚查看的第 8-11 行和第 12-16 行非常相似。在这些代码块中，又加载并定位了两个图像，但它们没有被添加到游戏屏幕的对象列表中；而是被添加到了一个 `MmgContainer` 实例中。第 19 行和第 20 行将两个图像帧添加到了该容器中，第 21 行和第 22 行设置了其尺寸。最后，第 23 行将其定位在屏幕中央，第 24 行将其添加到屏幕的可绘制对象列表中。

第 26 行创建了第一个图像位置的克隆，这样我们就可以访问该位置而无需担心实际改变它。第 27 行，一个新的 `MmgPulse` 对象被初始化，并设置了起始方向、总动画时间、位置缩放量和起始位置的默认值。请注意，脉冲对象在其动画例程中不使用任何可绘制对象。原因是这个类并不对对象本身进行动画处理；它是对一个值（如位置向量）进行动画处理，这个值可以用来驱动不同的可绘制类，例如 `MmgBmp` 图像和 `MmgSprite` 动画。

```
01 lret = false;

03 if (pause == false && isVisible == true) {
04     //始终运行此更新
05     prevDir = pulse.GetDirection();
06     pulse.Update(posTmp);
07     frame1.SetX(posTmp.GetX());

09     if(prevDir != pulse.GetDirection()) {
10         holder.SetIsVisible(!holder.GetIsVisible());
11     }
12 }

14 return lret;
清单 7-6
MmgPulse 类演示 2
```

上面列出的代码片段来自 MmgTestSpace 库中 `ScreenTestMmgContainer` 类的 `MmgUpdate` 方法。这些代码行向我们展示了游戏屏幕是如何配置以处理 `MmgUpdate` 方法调用的。请看第 6 行；这里 `MmgPulse` 类根据脉冲的当前位置进行更新。

在第 7 行，由 `MmgPulse` 对象更新的新位置被用来设置我们之前查看的 frame1 `MmgBmp` 对象的位置。通过这种方式，脉冲动画控制着图像的位置，使其从起始位置到结束位置来回移动，并不断重复这个过程。

## 动画类：MmgPositionTween/MmgSizeTween

`MmgPositionTween` 类是另一个动画类，其工作方式与我们刚刚介绍的 `MmgPulse` 类略有不同。`MmgPulse` 类实际上只是通过调用类的更新方法来更新一个 `MmgVector2` 对象。因此，这个类不像我们目前介绍过的许多可绘制类那样工作。

`MmgPulse` 类更像一个生成器，按需生成动画中的下一个向量供你使用。而 `MmgPositionTween` 类则扩展了 `MmgObj` 类，是可绘制对象集合中的一员。`MmgPositionTween` 类旨在在指定的时间内将一个对象从起始位置移动到结束位置。

`MmgSizeTween` 类与位置补间类非常相似，区别在于它不是调整对象的位置，而是改变对象的大小。这两个类非常相似，具有几乎完全相同的结构、字段和方法，只是 `MmgSizeTween` 类在其方法命名上略有不同。

请务必查看 MmgTestSpace 库中的大小补间示例屏幕 `ScreenTestMmgSizeTween` 类。你会发现代码非常熟悉，只是稍作调整以处理大小调整而非位置重设。

### 静态类成员

`MmgPositionTween` 类有四个静态类成员，如下所列。这些字段用于描述类的事件 ID 和类型。

```
public static int MMG_POSITION_TWEEN_REACH_FINISH = 0;
public static int MMG_POSITION_TWEEN_REACH_START = 1;
public static int MMG_POSITION_TWEEN_REACH_FINISH_TYPE = 0;
public static int MMG_POSITION_TWEEN_REACH_START_TYPE = 1;
清单 7-7
MmgPositionTween 静态类成员 1
```

上述字段列表是该类到达起始点和结束点事件的事件 ID 和类型值。`MmgPositionTween` 类支持在对象位于动画起点和终点时触发事件。



### 类字段

让我们来看看 `MmgPositionTween` 类的字段。我在下面列出了第一组需要审查的字段。

```
private boolean atFinish;
private boolean atStart;
private boolean dirStartToFinish;
private MmgVector2 finishPosition;
private MmgVector2 startPosition;
private boolean moving;
private long msStartMove;
private float msTimeToMove;
Listing 7-8
MmgPositionTween 类字段 1
```

我们要审查的第一组类字段以 `atFinish` 字段开始。该字段是一个布尔标志，指示动画是否已到达终点位置。类似地，`atStart` 字段指示动画是否处于起始位置。下一个字段 `dirStartToFinish` 是另一个布尔标志，当动画正向运行时（从起点到终点）为 true，当动画反向运行时为 false。

`finish` 和 `start` 位置字段顾名思义。当动画处于活动状态且对象正在移动时，`moving` 布尔标志被设置为 true。`msStartMove` 字段保存动画的起始时间，`msTimeToMove` 字段保存动画完成所需的总时间。

```
private MmgEventHandler onReachFinish;
private MmgEventHandler onReachStart;
private MmgVector2 pixelDistToMove;
private float pixelsPerMsToMoveX;
private float pixelsPerMsToMoveY;
private MmgObj subj;
private MmgEvent reachFinish = new MmgEvent(null, "reach_finish", MmgPositionTween.MMG_POSITION_TWEEN_REACH_FINISH, MmgPositionTween.MMG_POSITION_TWEEN_REACH_FINISH_TYPE, null, null);
private MmgEvent reachStart = new MmgEvent(null, "reach_start", MmgPositionTween.MMG_POSITION_TWEEN_REACH_START, MmgPositionTween.MMG_POSITION_TWEEN_REACH_START_TYPE, null, null);
Listing 7-9
MmgPositionTween 类字段 2
```

上面列出了我们要审查的第二组类字段。前两个字段是事件处理器。该类支持 `onReachFinish` 和 `onReachStart` 事件处理器。下一个条目 `pixelDistToMove` 是一个向量，表示终点位置与起始位置之间的差值。该向量表示动画过程中主体需要移动的像素距离。

我们进一步分解这些信息，并确定 `pixelsPerMsToMoveX` 和 `pixelsPerMsToMoveY`。这两个字段跟踪每个轴上必须移动的距离。你会注意到，该类的主题是一个 `MmgObj` 实例。这意味着位置补间动画可以与 MmgBase API 中的任何可绘制对象一起使用。这可是相当强大的功能。

`reachFinish` 和 `reachStart` 事件的模板是最后两个类字段。这些事件几乎准备就绪，只需要设置一个目标事件处理器即可。

### 支持方法详解

`MmgPositionTween` 类的支持方法只是该类字段的 get 和 set 方法。这里没有什么复杂的操作。我按照我们刚刚审查的类字段的顺序列出了这些方法。

```
public boolean GetAtFinish() { ... }
public void SetAtFinish(boolean b) { ... }
public boolean GetAtStart() { ... }
public void SetAtStart(boolean b) { ... }
public boolean GetDirStartToFinish() { ... }
public void SetDirStartToFinish(boolean b) { ... }
public MmgVector2 GetFinishPosition() { ... }
public void SetFinishPosition(MmgVector2 v) { ... }
public MmgVector2 GetStartPosition() { ... }
public void SetStartPosition(MmgVector2 v) { ... }
public boolean GetMoving() { ... }
public void SetMoving(boolean b) { ... }
public long GetMsStartMove() { ... }
public void SetMsStartMove(long l) { ... }
public float GetMsTimeToMove() { ... }
public void SetMsTimeToMove(float i) { ... }
public MmgEventHandler GetOnReachFinish() { ... }
public void SetOnReachFinish(MmgEventHandler o) { ... }
public MmgEventHandler GetOnReachStart() { ... }
public void SetOnReachStart(MmgEventHandler o) { ... }
public MmgVector2 GetPixelDistToMove() { ... }
public void SetPixelDistToMove(MmgVector2 v) { ... }
public float GetPixelsPerMsToMoveX() { ... }
public void SetPixelsPerMsToMoveX(float i) { ... }
public float GetPixelsPerMsToMoveY() { ... }
public void SetPixelsPerMsToMoveY(float i) { ... }
public int GetFinishEventId() { ... }
public void SetFinishEventId(int i) { ... }
public int GetStartEventId() { ... }
public void SetStartEventId(int i) { ... }
public MmgObj GetSubj() { ... }
public void SetSubj(MmgObj b) { ... }
Listing 7-10
MmgPositionTween 支持方法详解 1
```

再次强调，这些都是基本的 get 和 set 方法。请注意，你不能直接访问类的事件。但是，你可以设置它们的目标事件处理器和事件 ID。这里的方法很多。在使用该类时，不必感到有压力去理解所有方法的工作原理。只需专注于了解该类提供了哪些功能。稍后通过一些代码练习，你就会掌握细节。

### 主要方法详解

该类的主要方法如下所示。我们有一套相当标准的构造函数以及克隆、比较和绘制例程方法。到现在为止，这些方法对你来说应该都很熟悉了。你应该能够根据每个方法的类型和名称，对其用法有一个清晰的概念。

```
public MmgPositionTween(MmgObj subj, float msTimeToMove, MmgVector2 startPos, MmgVector2 finishPos) { ... }
public MmgPositionTween(MmgPositionTween obj) { ... }
public MmgObj Clone() { ... }
public MmgPositionTween CloneTyped() { ... }
public void MmgDraw(MmgPen p) { ... }
public boolean MmgUpdate(int updateTick, long currentTimeMs, long msSinceLastFrame) { ... }
public boolean ApiEquals(MmgPositionTween obj) { ... }
Listing 7-11
MmgPositionTween 主要方法详解 1
```

第一个条目是该类的默认构造函数。你可以使用它来创建一个已配置好并可直接使用的类的新实例。第二个构造函数是我们已经熟悉并喜爱的专用构造函数。此构造函数接受一个 `MmgPositionTween` 实例作为参数，并使用它来创建该对象的一个新的、唯一的副本。克隆方法使用专用构造函数创建该类的一个新的、唯一的副本。

`MmgUpdate` 和 `MmgDraw` 方法负责更新和绘制动画。`MmgDraw` 方法非常简单；它只是在屏幕上的指定位置绘制主体。`MmgUpdate` 方法稍微复杂一些，需要每帧调用一次，动画才能正常工作。此方法根据对象在 X 轴和 Y 轴上每帧需要移动的像素数来更新主体的位置。`ApiEquals` 方法提供两个 `MmgPositionTween` 对象之间的 API 级别比较。



### 演示：MmgPositionTween 类

本类的演示代码来自 MmgTestSpace 库中 `ScreenTestMmgPositionTween` 类的 `LoadResources` 方法。我将介绍该类所用资源的加载与初始化，以及事件处理程序和绘制方法。

```
01 frame1 = MmgHelper.GetBasicCachedBmp("soldier_frame_1.png");
02 frame1 = MmgBmpScaler.ScaleMmgBmp(frame1, 2.0f, true);
03 MmgHelper.CenterHorAndVert(frame1);

05 frame2 = MmgHelper.GetBasicCachedBmp("soldier_frame_2.png");
06 frame2 = MmgBmpScaler.ScaleMmgBmp(frame2, 2.0f, true);
07 MmgHelper.CenterHorAndVert(frame2);

09 frame3 = MmgHelper.GetBasicCachedBmp("soldier_frame_3.png");
10 frame3 = MmgBmpScaler.ScaleMmgBmp(frame3, 2.0f, true);
11 MmgHelper.CenterHorAndVert(frame3);

13 frames = new MmgBmp[4];
14 frames[0] = frame1;
15 frames[1] = frame2;
16 frames[2] = frame3;
17 frames[3] = frame2;

19 MmgVector2 tmpPos = frame1.GetPosition().Clone();
20 tmpPos.SetY(tmpPos.GetY() + MmgHelper.ScaleValue(15));
21 sprite = new MmgSprite(frames, tmpPos);
22 sprite.SetFrameTime(200l);
23 AddObj(sprite);

25 posTweenLabel = MmgFontData.CreateDefaultBoldMmgFontLg();
26 posTweenLabel.SetText("MmgSprite Example with 4 Frames Attached to an MmgPositionTween");
27 MmgHelper.CenterHorAndVert(posTweenLabel);
28 posTweenLabel.SetY(GetY() + MmgHelper.ScaleValue(70));
29 AddObj(posTweenLabel);

31 MmgVector2 start = new MmgVector2(MmgHelper.ScaleValue(100), GetY() + (GetHeight() - frame1.GetHeight()) / 2);
32 MmgVector2 stop = new MmgVector2(GetWidth() - MmgHelper.ScaleValue(100), GetY() + (GetHeight() - frame1.GetHeight()) / 2);

34 posTween = new MmgPositionTween(sprite, 10000, start, stop);
35 posTween.SetOnReachStart(this);
36 posTween.SetOnReachFinish(this);
37 posTween.SetMsStartMove(System.currentTimeMillis());
38 posTween.SetMoving(true);
清单 7-12
MmgPositionTween 类演示 1
```

上述代码片段展示了如何加载和设置 `MmgSprite` 类，该类将作为 `MmgPositionTween` 的主体对象。在第 1-11 行，通过三个相似的代码块，图像资源被加载、放大 100% 并居中定位到屏幕中央。第 13-17 行创建并初始化了一个包含四个图像的数组。

资源加载完成后，我们需要在第 31 和 32 行定义位置补间的起始和终止位置。位置补间以 `MmgSprite` 对象为主体进行初始化。思考一下我们为何能这样做。一个十秒的动画时间以及起始和终止向量被用作构造函数的参数。第 35 和 36 行设置了类的事件处理程序，两者均指向当前游戏屏幕。第 37 行设置了开始时间，第 38 行将位置补间标记为运动状态。这将启动动画。

```
01 public void MmgDraw(MmgPen p) {
02     if (pause == false && isVisible == true) {
03         super.MmgDraw(p);
04     }
05 }
01 public boolean MmgUpdate(int updateTick, long currentTimeMs, long msSinceLastFrame) {
02     lret = false;

04     if (pause == false && isVisible == true) {
05         //始终运行此更新
06         posTween.MmgUpdate(updateTick, currentTimeMs, msSinceLastFrame);
07         sprite.MmgUpdate(updateTick, currentTimeMs, msSinceLastFrame);
08     }

10     return lret;
11 }
01 public void MmgHandleEvent(MmgEvent e) {
02     MmgHelper.wr("ScreenTestMmgPositionTween.HandleMmgEvent: Msg: " + e.GetMessage() + " Id: " + e.GetEventId());
03     eventLabel.SetText("Event: " + e.GetMessage() + " Id: " + e.GetEventId() + " Type: " + e.GetEventType());
04     MmgHelper.CenterHor(eventLabel);
05     if(e.GetEventId() == MmgPositionTween.MMG_POSITION_TWEEN_REACH_FINISH) {
06         posTween.SetDirStartToFinish(false);
07         posTween.SetMsStartMove(System.currentTimeMillis());
08         posTween.SetMoving(true);

10     } else {
11         posTween.SetDirStartToFinish(true);
12         posTween.SetMsStartMove(System.currentTimeMillis());
13         posTween.SetMoving(true);

15     }
16 }
清单 7-13
MmgPositionTween 类演示 2
```

接下来我们要审查的演示代码片段是 `ScreenTestMmgPositionTween` 类的 `MmgDraw`、`MmgUpdate` 和 `MmgHandleEvent` 方法。请注意 `MmgDraw` 方法有多么简洁。所有绘制到屏幕的对象都是屏幕可绘制对象列表的一部分，因此所有绘制工作都自动为我们完成。`MmgUpdate` 方法则包含更多实质内容。注意，我们通过直接调用精灵和位置补间的更新方法，强制它们每帧都进行更新。这确保了动画更新在每一帧都能发生。

上述代码片段中的最后一个方法是 `MmgHandleEvent` 方法。该方法被配置为接收来自位置补间类的起始和结束事件。注意，当它接收到结束事件时，会反向重启位置补间动画，从结束位置移动到起始位置（第 6-8 行）。类似地，当接收到起始事件时，该方法会再次重置位置补间（第 11-13 行）。这确保了位置补间能够无限期地持续运行。这也是该类事件的一个很好的小示例。

## 章节总结

在本章中，我们完成了对 MmgBase API 动画类的回顾。我们介绍的类展示了两种不同类型的基于类的动画。`MmgPulse` 类体现了一种“放手式”方法，该类仅负责对数值进行动画处理，然后将这些数值应用于屏幕上的对象。

`MmgPositionTween` 和 `MmgSizeTween` 类是对象动画“亲力亲为”方法的示例。这些类属于可绘制对象集的一部分，并处理其自身的所有绘制和更新逻辑。

*   MmgPulse：一个 API 类，用于通过随时间变化来对数值进行动画处理。生成的数值序列可用于驱动对象位置动画等。

*   MmgPositionTween/MmgSizeTween：一个 API 类，用于随时间对对象进行动画处理，通过调整其位置（对于 MmgPositionTween 类）或调整其大小（对于 MmgSizeTween 类）来实现。

我们回顾的动画类展示了高级复杂类如何交互以创建新的、令人兴奋的功能。例如，在 `MmgPositionTween` 类的示例代码中，我们使用 `MmgSprite` 对象作为位置补间的主体；因此，我们最终得到了一个动画（位置补间）对另一个动画（精灵对象）进行动画处理的效果。

停下来思考一下我们为什么能够做到这一点。游戏引擎的设计使得类之间可以轻松协作。我们还看到了使用这些新的、复杂的动画类是多么简单。我们所要做的就是将它们添加到当前屏幕的可绘制对象列表中，并确保在需要时每帧都调用它们的更新方法。就是这么简单。

# 8. 游戏屏幕类

在本章中，我们将回顾 MmgBase API 的游戏屏幕类。这些是更高级、更具体的游戏屏幕实现的基类。共有三个类，一个用于启动画面，一个用于加载画面，一个用于支持菜单系统的通用屏幕。

这些类非常重要，因为它们是 MmgBase API 与 MmgCore API 交互的连接点。游戏面板类负责发送当前游戏屏幕的更新和绘制方法调用，以及输入方法调用。当前游戏屏幕是 `MmgGameScreen` 类的一个实例：

*   MmgSplashScreen

*   MmgLoadingScreen

*   MmgGameScreen



## 游戏画面类：MmgSplashScreen

`MmgSplashScreen` 类为创建游戏启动画面提供了基础。该画面设计为显示数秒，随后触发一个事件，该事件应由游戏面板类捕获，并通过将状态切换至加载画面来响应。

需要说明的是，此类继承自 `MmgGameScreen` 类并实现了 `MmgUpdateHandler` 接口，以便该类能够接收来自启动画面计时器的更新事件。`MmgGameScreen` 类继承自 `MmgObj` 类，因此即使在这一层级，游戏画面也属于可绘制对象集合的一部分，并被专门配置为接入游戏面板类，以便以所需的帧率绘制当前游戏画面。

### 静态类成员

该类仅有一个静态类字段，如下所示。

```
public static int DEFAULT_DISPLAY_TIME_MS = 3000;
代码清单 8-1
MmgSplashScreen 静态类成员 1
```

该字段控制启动画面的显示时间，单位为毫秒。

### 类字段

`MmgSplashScreen` 类有两个类字段供我们查看。

```
private int displayTime;
private MmgUpdateHandler update;
代码清单 8-2
MmgSplashScreen 类字段 1
```

列出的第一个字段 `displayTime` 保存了在触发表示显示周期结束的事件之前，启动画面应显示的毫秒数。第二个类字段 `update` 是设计用于接收和处理更新事件的处理程序。

### 支持方法详情

`MmgSplashScreen` 的支持方法非常简单直接。下面按与其对应类字段相同的顺序列出了两组 get 和 set 方法。

```
public int GetDisplayTime() { ... }
public void SetDisplayTime(int i) { ... }
public MmgUpdateHandler GetUpdateHandler() { ... }
public void SetUpdateHandler(MmgUpdateHandler Update) { ... }
代码清单 8-3
MmgSplashScreen 支持方法详情 1
```

这些方法非常直接，因此在此不再赘述。让我们继续看该类的主要方法。

### 主要方法详情

我们需要查看的 `MmgSplashScreen` 类的主要方法如下所示。这些是我们在 API 审查中之前见过的标准主要方法。然而，有一个方法值得注意，即 `MmgHandleUpdate` 方法。该方法设计用于接收来自计时器线程的事件，并将其传递给游戏面板，以便显示下一个游戏画面。

```
//构造函数
public MmgSplashScreen() { ... }
public MmgSplashScreen(int DisplayTime) { ... }
public MmgSplashScreen(MmgSplashScreen obj) { ... }
//克隆方法
public MmgObj Clone() { ... }
public MmgSplashScreen CloneTyped() { ... }
//事件处理方法
public void MmgHandleUpdate(Object obj) { ... }
//通用主要方法
public void MmgDraw(MmgPen p) { ... }
public boolean ApiEquals(MmgSplashScreen obj) { ... }
代码清单 8-4
MmgSplashScreen 主要方法详情 1
```

前两个构造函数分别使用默认显示时间和用户指定的显示时间。它们将创建一个显示默认图像并可供使用的基础类。第三个构造函数是一个专用构造函数，它接受一个 `MmgSplashScreen` 对象作为参数，并用它来创建该类的一个新的、唯一的副本。

克隆方法遵循标准模式，提供了一个基本的克隆方法和一个返回 `MmgSplashScreen` 类型的克隆方法。`MmgHandleUpdate` 方法接收来自启动画面计时类的事件，然后触发一个事件，该事件被发送到游戏面板类，在那里应触发游戏画面从启动画面切换到加载画面。

前面列出的最后两个方法是标准的主要方法 `MmgDraw` 和 `ApiEquals`。绘制方法作为游戏引擎默认绘制例程的一部分被调用。`ApiEquals` 方法是一个 API 级别的比较方法，用于比较两个 `MmgSplashScreen` 对象是否相等。

### 演示：MmgSplashScreen 类

对于 `MmgSplashScreen` 类的演示部分，我们将查看 MmgCore API 中 `ScreenSplash` 类的实现，该类扩展了 MmgBase API 的 `MmgSplashScreen` 类。这应该能为我们提供一个该类实际使用的好例子。

这是我们第一次在演示部分进行类审查。需要明确的是，以下小节针对的是 MmgCore API 的 `ScreenSplash` 类。

#### 演示审查：静态类成员

MmgCore API 的 `ScreenSplash` 类有一个静态类字段，如下所示。

```
public static int EVENT_DISPLAY_COMPLETE = 0;
代码清单 8-5
演示审查：ScreenSplash 静态类成员 1
```

`EVENT_DISPLAY_COMPLETE` 字段用于标记一个事件，以指示启动画面显示完毕。

#### 演示审查：类字段

`ScreenSplash` 的字段如下所示。有四个字段供我们查看。请记住，此类继承自 `MmgGameScreen` 类，因此它免费获得了该类的许多功能。

```
public GameStates state;
public GenericEventHandler handler;
public GamePanel owner;
public Hashtable classConfig;
代码清单 8-6
演示审查：ScreenSplash 类字段 1
```

列出的第一个字段保存了与启动画面关联的游戏状态。由于 `ScreenSplash` 的实现更具体，它实际上在应用程序中使用；它拥有帮助将游戏画面与标识值关联起来的字段。第二个类字段是一个事件处理程序，用于处理 MmgBase API 的通用事件。此事件处理程序将接收一个指示启动画面显示完毕的事件。

第三个字段 `owner` 是 `GamePanel` 类的一个实例，旨在指示持有游戏画面的游戏面板。游戏面板类是 MmgCore API 的一部分；到目前为止我们还没有审查过这个类。前面列表中的最后一个条目是 `classConfig` 字段，用于存储任何可用的类配置数据。配置文件作为类加载资源方法的一部分被加载。找到的值（如果有）用于调整游戏画面的显示方式。

#### 演示审查：支持方法详情

`ScreenSplash` 类的支持方法如下所示。只有三个方法供我们查看。

```
public void SetGenericEventHandler(GenericEventHandler Handler) { ... }
public GenericEventHandler GetGenericEventHandler() { ... }
public GameStates GetGameState() { ... }
代码清单 8-7
演示审查：ScreenSplash 支持方法详情 1
```

列出的前两个方法是基本的 get 和 set 方法，允许您访问类的处理程序字段。当游戏画面的显示时间结束时，该字段用于将事件发送到指定的 `handler`。列出的最后一个支持方法简单地返回与画面关联的 `gameState`。



#### 演示回顾：主要方法详解

该类的主要方法如下所列。这些方法应与我们在 MmgBase API 演示部分遇到的一些方法类似。

```
public ScreenSplash(GameStates State, GamePanel Owner) { ... }
public void LoadResources() { ... }
public void UnloadResources() { ... }
public void MmgHandleUpdate(Object obj) { ... }
public void MmgDraw(MmgPen p) { ... }
清单 8-8
演示回顾：ScreenSplash 主要方法详解 1
```

类构造函数定义了屏幕的游戏状态以及该屏幕所属的游戏面板。`LoadResources` 方法用于在绘制游戏屏幕之前准备其所需的资源。`UnloadResources` 方法用于清除该类已加载的资源。

`MmgHandleUpdate` 类用于处理来自启动屏幕计时器的更新事件。最后，`MmgDraw` 方法用于绘制游戏屏幕。需要说明的是，`MmgGameScreen` 类继承自 `MmgObj` 类。因此，即使在这个层级，`MmgObj` 类也在驱动底层功能。

该类演示了如何扩展一个通用的 MmgBase API 类，并对其进行定制以用于应用程序或游戏。在定制过程中，该类会接入现有的应用程序基础设施，从而变得更加具体，通用性降低。

## 游戏屏幕类：MmgLoadingScreen

`MmgLoadingScreen` 类是 MmgBase API 中的另一个高级类。该类用于运行游戏引擎的默认资源加载过程。与 `MmgSplashScreen` 类类似，`MmgSplashScreen` 类扩展了 `MmgGameScreen` 类并提供了启动屏幕的松散实现，而 `MmgLoadingScreen` 类也扩展了 `MmgGameScreen` 类，并提供了加载屏幕的松散实现。

我之所以将实现描述为松散，仅仅是因为该类尚未接入运行时环境，因此其使用方式在一定程度上是开放的。当我们在演示部分介绍该类的实现示例时，您将看到一个更具体的屏幕实现，该实现旨在接入游戏引擎的默认运行时。

### 类字段

`MmgLoadingScreen` 类有两个字段供我们查看。

```
private MmgLoadingBar loadingBar;
private float loadingBarOffsetBottom = 0.10f;
清单 8-9
MmgLoadingScreen 类字段 1
```

第一个字段是一个加载条类实例，用于指示游戏资源加载的进度。列出的第二个字段用于根据加载屏幕高度的百分比来调整加载条的位置。

### 支持方法详解

`MmgLoadingScreen` 的支持方法提供了对其类字段的访问。您可以获取和设置加载条，并获取加载条偏移值。

```
public MmgLoadingBar GetLoadingBar() { ... }
public void SetLoadingBar(MmgLoadingBar lb, float lBarOff) { ... }
public float GetLoadingBarOffsetBottom() { ... }
清单 8-10
MmgLoadingScreen 支持方法详解 1
```

这些支持方法非常简单直接，因此我在此不再赘述。请务必仔细查看并理解它们的用法。

### 主要方法详解

该类的主要方法包括一组标准的构造函数、克隆方法和通用主要方法。其中许多方法应该与我们之前进行的类回顾中的方法类似。

```
//构造函数
public MmgLoadingScreen() { ... }
public MmgLoadingScreen(MmgLoadingBar LoadingBar, float lBarOff) { ... }
public MmgLoadingScreen(MmgLoadingScreen obj) { ... }
//克隆方法
public MmgObj Clone() { ... }
public MmgLoadingScreen CloneTyped() { ... }
//通用主要方法
public void MmgDraw(MmgPen p) { ... }
public boolean ApiEquals(MmgLoadingScreen obj) { ... }
清单 8-11
MmgLoadingScreen 主要方法详解 1
```

第一个构造函数不接受任何参数，并创建一个尚未准备好使用的类的新实例。需要对加载条进行一些进一步的定制。幸运的是，还有第二个构造函数选项，它接受一个加载条和一个定位偏移量作为参数。此构造函数创建一个可直接使用的类的新实例。

列出的第三个构造函数是一个专用构造函数，它接受一个 `MmgLoadingScreen` 对象作为参数，并使用它来创建该对象的一个新的、唯一的副本。接下来的两个方法，即克隆方法，提供了类克隆功能。这些方法使用专用构造函数创建当前类的副本，然后将该副本转换为指定类型并返回。

最后但同样重要的是绘制方法 `MmgDraw` 和比较方法 `ApiEquals`。正如我们之前所见，绘制方法作为游戏引擎默认绘制例程的一部分被调用。`ApiEquals` 方法是一个 API 级别的比较方法，用于检查两个 `MmgLoadingScreen` 实例是否相等。

### 演示：MmgLoadingScreen 类

对于 `MmgLoadingScreen` 类的演示部分，我们将查看 MmgCore API 中 `ScreenLoading` 类的实现，该类扩展了 MmgBase API 的 `MmgLoadingScreen` 类。这将为我们提供一个该类实际使用的绝佳示例。需要明确的是，以下小节用于回顾 MmgCore API 中的 `ScreenLoading` 类。

#### 演示回顾：静态类成员

与 `ScreenSplash` 类类似，MmgCore API 的 `ScreenLoading` 类有一个静态字段需要我们介绍。

```
public static int EVENT_LOAD_COMPLETE = 0;
清单 8-12
演示回顾：ScreenLoading 静态类成员 1
```

`EVENT_LOAD_COMPLETE` 字段用于在事件中指示资源加载已完成。该事件旨在由游戏面板类处理，使其将当前屏幕从加载屏幕切换到主菜单屏幕。

#### 演示回顾：类字段

`ScreenLoading` 类有几个类字段，用于扩展其父类 `MmgLoadingScreen` 类的功能，以执行实际的资源加载。

```
public GameStates state;
public GamePanel owner;
public long slowDown;
public RunResourceLoad datLoad;
public GenericEventHandler handler;
public Hashtable classConfig;
清单 8-13
演示回顾：ScreenLoading 类字段 1
```

`state` 和 `owner` 字段由类构造函数设置，用于在游戏屏幕和游戏面板类之间建立关联。`slowDown` 字段用于向资源加载过程添加延迟（以毫秒为单位）。当您只加载少量资源但希望看到加载进度时，这会很有帮助。

`datLoad` 字段是 `RunResourceLoad` 类的一个实例，用于在其自己的线程上运行资源加载。`handler` 字段是一个通用事件处理器，用于向游戏面板类发送资源加载完成事件，以便游戏面板类能够通过将游戏屏幕切换到主菜单来做出响应。`ScreenLoading` 类实现了 `LoadResourceUpdateHandler` 接口。

这意味着该类会接收资源加载进度的更新，并使用这些数据来设置加载条的值。前面列出的最后一个条目是 `classConfig` 字段。这是一个私有类字段，用于保存配置文件数据（如果存在）。这些数据用于自定义游戏屏幕的显示。



#### 演示回顾：支持方法详解

我要讨论的第一组支持方法与资源加载过程相关。请花点时间查看以下方法。

```
public RunResourceLoad GetLoader() { ... }
public void SetLoader(RunResourceLoad DatLoad) { ... }
public long GetSlowDown() { ... }
public void SetSlowDown(long l) { ... }
public boolean GetLoadResult() { ... }
public boolean GetLoadComplete() { ... }
清单 8-14
演示回顾：ScreenLoading 支持方法详解 1
```

前两个条目提供了对资源加载器的访问。该类旨在单独的线程中运行。接下来的两个方法是 `slowDown` 字段的访问方法。最后两个方法用于返回关于加载过程的信息。`GetLoadResult` 方法返回一个布尔指示器，表示上一次读取操作的结果。如果资源加载过程已完成，`GetLoadComplete` 方法返回 true。

```
public GameStates GetGameState() { ... }
public GenericEventHandler GetGenericEventHandler() { ... }
public void SetGenericEventHandler(GenericEventHandler Handler) { ... }
清单 8-15
演示回顾：ScreenLoading 支持方法详解 2
```

接下来要讨论的支持方法是 `GetGameState` 方法。此方法返回与游戏屏幕关联的游戏状态。游戏面板类使用游戏状态值来跟踪游戏或应用程序中的不同游戏屏幕。

#### 演示回顾：主要方法详解

`ScreenLoading` 类包含以下主要方法。

```
//构造函数
public ScreenLoading(MmgLoadingBar LoadingBar, float lBarOff, GameStates State, GamePanel Owner) { ... }
public ScreenLoading(GameStates State, GamePanel Owner) { ... }
//类准备/清理方法
public void LoadResources() { ... }
public void UnloadResources() { ... }
//资源加载方法
public boolean GetResourceFileData() { ... }
public void StartDatLoad() { ... }
public void StopDatLoad() { ... }
//通用主要方法
public void HandleUpdate(LoadResourceUpdateMessage obj) { ... }
public void MmgDraw(MmgPen p) { ... }
清单 8-16
演示回顾：ScreenLoading 主要方法详解 1
```

列出的第一个构造函数接受一组完整的参数，并使用它们初始化相关的类字段。此构造函数将创建一个新的、可立即使用的 `ScreenLoading` 类。列出的第二个构造函数接受的参数较少，仅设置 `gameState` 和 `owner` 字段。

该类与我们迄今为止审查过的每个游戏屏幕类一样，都有一个准备方法，即 `LoadResources` 方法。此方法用于准备屏幕以供使用。`UnloadResources` 方法应该是 `LoadResources` 方法的反向操作，并清理所有已加载的资源。

前面列出的下一组主要方法是加载方法。这些方法参与资源加载过程。如果存在一些要加载的资源，`GetResourceFileData` 方法返回 true。`StartDatLoad` 方法通过启动线程加载类 `datLoad` 来开始资源加载过程。类似地，`StopDatLoad` 方法停止资源加载过程。

最后要审查的两个方法是 `HandleUpdate` 方法和 `MmgDraw` 方法。`HandleUpdate` 方法配置为接收来自资源加载过程的更新事件。当该方法检测到资源加载已完成时，它将向已注册的通用事件处理程序发送一个通用事件。

这应该是游戏面板类。前面集合中的最后一个条目是 `MmgDraw` 方法。这是一个标准的方法实现，它仅调用超类（在 C# 中为基类）以确保使用默认的绘制功能。至此，该类的主要方法审查结束。接下来，我们将查看 MmgGameScreen 类。

## 游戏屏幕类：MmgGameScreen

`MmgGameScreen` 类是 `MmgSplashScreen` 和 `MmgLoadingScreen` 的超类。与闪屏和加载屏幕非常相似，`MmgGameScreen` 类是游戏屏幕的一种松散实现，提供了许多功能，但只需要实现很少的功能。这为您提供了最佳的灵活性设置。

`MmgGameScreen` 类是 `MmgObj` 类的子类。这样，MmgBase API 中的所有游戏屏幕类也都是 `MmgObj` 实例。它们都是可绘制对象集合的一部分。屏幕类代表了 MmgBase API 中类功能的最高层级。这些类位于此 API 渲染链的顶端。但请花点时间思考它们以及我们如何使用它们。它们实际上就像我们使用过的任何其他基于 `MmgObj` 的类一样。

### 类字段

`MmgGameScreen` 类是 MmgBase API 的核心游戏屏幕类。该类包含许多字段，如下所示分组列出。

```
//渲染控制字段
public boolean pause;
public boolean ready;
//屏幕特性字段
private MmgContainer objects;
private MmgObj background;
private MmgObj foreground;
private MmgObj header;
private MmgObj footer;
private MmgObj message;
//菜单字段
private MmgMenuContainer menu;
private MmgObj leftCursor;
private MmgObj rightCursor;
private int menuIdx;
private int menuStart;
private int menuStop;
private int menuCursorLeftOffsetX;
private int menuCursorLeftOffsetY;
private int menuCursorRightOffsetX;
private int menuCursorRightOffsetY;
private boolean menuOn;
//事件字段
private MmgUpdateHandler updateHandler;
清单 8-17
MmgGameScreen 类字段 1
```

由于字段数量如此之多，我们将需要审查大量的支持方法。我会尽可能缩短对类字段和支持方法的审查。第一组字段用于控制类的渲染。暂停或未就绪的屏幕将不会被渲染或更新，除非专门编码以执行此操作。

第二组字段包含屏幕中所有不与菜单直接关联的可绘制特性。如您所见，这里有游戏屏幕多种不同特性的占位符。您可以选择不使用其中任何一个，并将它们设置为 null。

第三组字段是菜单字段。第一个条目是菜单容器 `menu`。接下来的两个条目可用于设置左侧和右侧的菜单光标。如果您愿意，可以选择只实现一侧。不过，您至少应该实现其中一个，以便用户能够轻松看到他们在菜单系统中的位置。

`menuIdx`、`menuStart` 和 `menuStop` 字段用于控制当前菜单中哪些菜单项处于活动状态。接下来的四个条目用于微调菜单光标的位置，最后一个布尔标志控制菜单的开启或关闭。如果您不实现菜单，请将此布尔值设置为 false，并确保 `menu` 字段为 null。列表中的最后一个字段是一个更新事件处理程序，用于处理来自闪屏或加载屏幕等处的更新事件。类的 `update` 方法用于将事件发送到此字段指定的处理程序。



### 支持方法详解

`MmgGameScreen` 类有一长串支持方法。我已将它们按以下分组列出，顺序与其对应的类字段相同。与类字段无关的新方法已插入到适当位置。请查看以下列表中的方法，并确保理解它们。

```
//渲染控制方法
public boolean IsReady() { ... }
public void SetReady(boolean b) { ... }
public void Pause() { ... }
public void UnPause() { ... }
public boolean IsPaused() { ... }
//屏幕特性方法
public MmgContainer GetObjects() { ... }
public void SetObjects(MmgContainer c) { ... }
public void AddObj(MmgObj obj) { ... }
public void RemoveObj(MmgObj obj) { ... }
public void ClearObjs() { ... }
public MmgObj GetBackground() { ... }
public void SetBackground(MmgObj b) { ... }
public void SetCenteredBackground(MmgObj b) { ... }
public void CenterObjects() { ... }
public MmgObj GetForeground() { ... }
public void SetForeground(MmgObj b) { ... }
public MmgObj GetHeader() { ... }
public void SetHeader(MmgObj m) { ... }
public MmgObj GetFooter() { ... }
public void SetFooter(MmgObj m) { ... }
public MmgObj GetMessage() {
public void SetMessage(MmgObj m) { ... }
//菜单方法
public MmgMenuContainer GetMenu() { ... }
public void SetMenu(MmgMenuContainer m) { ... }
public MmgObj GetLeftCursor() { ... }
public void SetLeftCursor(MmgObj m) { ... }
public MmgObj GetRightCursor() { ... }
public void SetRightCursor(MmgObj m) { ... }
public int GetMenuIdx() { ... }
public void SetMenuIdx(int i) { ... }
public int GetMenuStart() { ... }
public void SetMenuStart(int i) { ... }
public int GetMenuStop() { ... }
public void SetMenuStop(int i) { ... }
public int GetMenuCursorLeftOffsetX() { ... }
public void SetMenuCursorLeftOffsetX(int i) { ... }
public int GetMenuCursorLeftOffsetY() { ... }
public void SetMenuCursorLeftOffsetY(int i) { ... }
public int GetMenuCursorRightOffsetX() { ... }
public void SetMenuCursorRightOffsetX(int i) { ... }
public int GetMenuCursorRightOffsetY() { ... }
public void SetMenuCursorRightOffsetY(int i) { ... }
public boolean GetMenuOn() { ... }
public void SetMenuOn(boolean m) { ... }
//事件方法
public void SetMmgUpdateHandler(MmgUpdateHandler u) { ... }
public MmgUpdateHandler GetMmgUpdateHandler() { ... }
清单 8-18
MmgGameScreen 支持方法详解 1
```

我们不会在此详细审查所有这些支持方法。它们大部分是简单的 get 和 set 方法。不过，有几个条目我想快速讨论一下。`AddObj`、`RemoveObj` 和 `ClearObjs` 方法让你无需使用 `GetObjects` 方法即可对子对象进行更精细的控制。我们已经多次看到 `AddObj` 方法。它实际上是一直在底层被调用的类方法。

`SetCenteredBackground` 和 `CenterObjects` 方法是支持方法，提供了一种快速居中类特性的方式。这是一个常见任务，因此这些方法有助于加快速度。`SetCenteredBackground` 方法设置背景并将其居中。类似地，`CenterObjects` 方法将除菜单外的所有类特性居中。

其余所有方法都是基本的 get 和 set 方法，直接对应其关联的类字段，因此我们将继续介绍类的输入方法。

### 输入方法详解

我们之前从未需要创建这样的章节，但输入处理方法实在太多，我决定为它们单独设立一节。这些方法不言自明，因此我不会详细讨论它们。当你阅读它们时，请思考如何在你的下一个游戏中使用它们。

```
//鼠标移动方法
public boolean ProcessMouseMove(int x, int y) { ... }
public boolean ProcessMouseMove(MmgVector2 v) { ... }
//鼠标按钮方法
public boolean ProcessMousePress(MmgVector2 v) { ... }
public boolean ProcessMousePress(MmgVector2 v, int btnIndex) { ... }
public boolean ProcessMousePress(int x, int y) { ... }
public boolean ProcessMousePress(int x, int y, int btnIndex) { ... }
public boolean ProcessMouseRelease(MmgVector2 v) { ... }
public boolean ProcessMouseRelease(MmgVector2 v, int btnIndex) { ... }
public boolean ProcessMouseRelease(int x, int y) { ... }
public boolean ProcessMouseRelease(int x, int y, int btnIndex) { ... }
public boolean ProcessMouseClick(MmgVector2 v) { ... }
public boolean ProcessMouseClick(MmgVector2 v, int btnIndex) { ... }
public boolean ProcessMouseClick(int x, int y) { ... }
public boolean ProcessMouseClick(int x, int y, int btnIndex) { ... }
//A 按钮方法
public boolean ProcessAPress(int src) { ... }
public boolean ProcessARelease(int src) { ... }
public boolean ProcessAClick(int src) { ... }
public boolean ProcessBPress(int src) { ... }
public boolean ProcessBRelease(int src) { ... }
public boolean ProcessBClick(int src) { ... }
//键盘按键方法
public boolean ProcessKeyPress(char c, int code) { ... }
public boolean ProcessKeyRelease(char c, int code) { ... }
public boolean ProcessKeyClick(char c, int code) { ... }
//方向键按钮方法
public boolean ProcessDpadPress(int dir) { ... }
public boolean ProcessDpadRelease(int dir) { ... }
public boolean ProcessDpadClick(int dir) { ... }
//调试方法
public void ProcessDebugClick() { ... }
清单 8-19
MmgGameScreen 输入方法详解 1
```

我唯一想讨论的方法是 `ProcessDebugClick`。此方法默认映射到键盘的“D”键，如果按下，它将调用游戏屏幕的 `ProcessDebugClick` 方法。这是一个输入一些调试代码以将信息打印到标准输出的绝佳位置。

### 主方法详解

`MmgGameScreen` 类的主方法分组列出如下。

```
//构造函数
public MmgGameScreen() { ... }
public MmgGameScreen(MmgGameScreen obj) { ... }
//克隆方法
public MmgObj Clone() { ... }
public MmgGameScreen CloneTyped() { ... }
//事件方法
public void EventHandler(MmgEvent e) { ... }
//通用主方法
public boolean ApiEquals(MmgGameScreen obj) { ... }
public void Update(Object data) { ... }
public void MmgDraw(MmgPen p) { ... }
public boolean MmgUpdate(int updateTick, long currentTimeMs, long msSinceLastFrame) { ... }
//菜单方法
public void MoveMenuSelUp() { ... }
public void MoveMenuSelDown() { ... }
private void DrawMenu(MmgPen p) { ... }
public void ProcessMenuItemSel(MmgMenuItem item) { ... }
清单 8-20
MmgGameScreen 主方法详解 1
```

第一个构造函数不接受参数，创建一个全新的、可供你使用的游戏屏幕。它不会做太多事情，但已准备就绪。第二个条目是一个专门的构造函数，它接受一个 `MmgGameScreen` 对象作为参数，并用它来创建该对象的一个新的、唯一的副本。此构造函数支持下一组中的克隆方法。这些克隆方法使用专门的构造函数，并在返回前将结果对象转换为指定的类型。

下一个方法允许你设置游戏屏幕的事件处理器。默认情况下，如果菜单已启用，它会配置为使用菜单进行处理。`ApiEquals` 方法是一个 API 级别的比较方法，用于判断两个 `MmgGameScreen` 对象是否相等。`MmgDraw` 和 `MmgUpdate` 方法是游戏引擎绘制例程的一部分，负责更新对象并渲染它。更新方法配置为使用给定的事件数据触发 `updateHandler`。

最后一组主方法负责处理菜单输入、菜单上下移动事件、绘制菜单以及选择菜单项。至此，类的主方法回顾就结束了。



### 演示：MmgGameScreen 类

由于 `MmgGameScreen` 类是 C# 中所有 MmgBase API 屏幕类和所有 MmgCore 屏幕类的超类（基类），我建议将 `MmgSplashScreen`、`MmgLoadingScreen` 及其各自的演示部分作为 `MmgGameScreen` 类实际应用的可靠示例。

## 章节总结

在本章中，我们完成了对 MmgBase API 游戏屏幕类的回顾。这些类代表了 MmgBase API 中功能的顶峰。这些类也代表了 MmgBase API 中通过游戏面板类与 MmgCore API 直接交互的功能点。

请记住，这些类与 MmgBase API 中的许多类一样，定义得比较松散。这意味着你很可能需要扩展它们的功能并调整类，使其符合你的设计目标。在这种情况下，我们看到了 MmgCore API 的 `ScreenSplash` 和 `ScreenLoading` 类作为游戏屏幕具体实现的示例。让我们看一下我们回顾过的类的总结：

*   **MmgSplashScreen**：一个用于表示游戏启动画面的 API 类。此类设计用于在触发结束事件前显示几秒钟的 Logo。

*   **MmgLoadingScreen**：一个用于表示游戏加载画面的 API 类。此类定义松散，不强制规定资源加载结构。

*   **MmgGameScreen**：一个用于表示游戏屏幕的 API 类，就像你在任何 2D 视频游戏中看到的那样。此类具有许多可以关闭以简化其渲染的功能。

既然我们已经完成了对引擎 MmgBase API 中所有主要类的回顾，你应该开始看到游戏引擎的结构了。这些类构成了一套工具集，旨在围绕 `MmgObj` 类工作，促进 API 中可绘制类集合的高级交互。我们已经看到，在保持直接插入游戏引擎绘制例程的能力的同时，向 API 添加新功能是多么容易。

我应该提一下，在首次实现新的游戏屏幕时，你需要小心谨慎。请务必理解你的游戏屏幕底层类如何处理更新和绘制调用。某些屏幕类可能设计为仅在 `isDirty` 字段设置为 true 时才更新。某些对象和屏幕要求类在每一帧都进行更新，所以要注意这一点，否则你最终会挠头不解，为什么你的对象没有动画效果。

# 第二部分。

# 9. MmgCore API 简介

本书的第二部分包含对游戏引擎运行时代码的全面回顾。这些代码同时存在于 Java 和 C# 中，并包含在主项目的 MmgCore 包（如果你使用 C#，则为命名空间）下。MmgCore API 负责处理命令行参数、处理游戏引擎配置文件、创建游戏窗口、路由输入、管理游戏屏幕等等。

在许多游戏引擎实现中，都有一个“播放器”设计用于运行编译后的游戏代码。在我们的实现中，运行时代码作为编译后游戏的一部分被包含在内。这是通过将游戏引擎的所有核心功能包含在 MmgCore API 中来实现的。因此，创建的任何游戏都将包含所有运行时代码，这允许你根据需要对其进行控制和定制。最终，我认为让运行时代码更易获取并成为游戏的一部分是更好的决定。

## 运行时 API 概述

在我们进行详细的代码审查之前，让我们先谈谈 MmgCore API。这个 API 在 C#/MonoGame 和 Java/Swing 实现之间存在最多的差异。尽管两个 API 在功能上是等价的，但我想讨论一下这两个实现之间的一些主要区别。

两个实现之间的一个主要区别是 `MainFrame` 类在 C# 实现中不存在。这是因为 Java 实现使用 Swing 来驱动游戏的窗口，并且存在一个框架（frame）的概念来容纳游戏面板。在 C# 实现中，没有主框架，该功能已移入一个混合游戏面板类中。

你会发现两个实现都存在等效的方法，并且相同的代码只是位于略有不同的位置。无论如何，在 C# 中跟进应该仍然非常容易。我应该提到的另一个实现差异是，在 C# 版本中，某些操作无法在线程上执行。这是因为这些操作需要访问图形上下文，而图形上下文不是线程安全的。在 C# 版本中，使用一组旨在将工作从子线程移动到主线程的类来克服此限制。

我想提到的下一个区别是，单元测试也需要访问图形上下文。为了克服这个限制，单元测试通过命令行参数从主游戏线程执行。测试输出被转储到标准输出。

不同的操作系统之间，当然还有 C# 和 Java 实现之间，可能存在不同的键盘代码。为了克服这个限制，有一个全局标志可以启用键码转换方法。你可以使用此方法创建一组统一的键盘代码。该设置可以在 `GameSettings` 类中找到，特别是 `INPUT_NORMALIZE_KEY_CODE` 字段。

类似地，系统字体在不同的操作系统以及游戏引擎的 C# 和 Java 版本之间渲染略有不同。大多数情况下，你应该使用图像作为文本，而不是 `MmgFont` 对象；但无论如何，有一个全局标志可以打开字体位置调整方法。此方法允许你在平台之间标准化字体定位。该标志可以在 MmgBase API 的 `MmgPen` 类中找到，特别是 `FONT_NORMALIZE_POSITION` 字段。

我应该提到的最后一个主要区别是字体。在 Java 中，字体可以动态调整大小，你可以根据需要创建不同大小的字体。在 C# 版本中，字体必须提前准备好。为了克服 C# 实现中的这一差异，我们加载了从 1 号到 50 号的默认字体，并为游戏引擎设置了 50 号的最大字体大小。

我们可以通过预加载所有可能使用的字体来模拟 Java 版本即时创建新字体的能力。通过这种方式，我们克服了两个实现之间的技术差异。在本书中，我们将主要遵循 Java 代码，但你应该能够毫不费力地在 C# 中跟进。以下是本书这部分将涵盖的类的总结。

### 游戏引擎设置

*   DatConstantsEntry

*   DatExternalStrings

*   GameSettings

*   GameSettingsImporter

### 游戏资源加载类

*   RunResourceLoad

*   GenericEventHandler

*   GenericEventMessage

*   LoadResourceUpdateHandler

*   LoadResourceUpdateMessage

### 游戏屏幕/工作线程

*   RunFrameRate

*   Screen

*   ScreenMainMenu

*   ScreenSplash

*   ScreenLoading

*   HandleMainMenuEvent

### 静态入口点/窗口类

*   MmgApiGame

*   MainFrame

*   GamePanel

完成这部分游戏引擎的回顾将让你看到全貌。你将能够在代码中准确地看到 MmgCore API 在何处将工作移交给 MmgBase API。你将获得理解如何定制游戏引擎以及配置资源目录以支持多个项目的经验。一旦你完成了本书的这一部分，你就可以开始构建一些游戏了！



# 10. 静态主入口点

现在你已经了解了底层的 MmgBase API，并熟练掌握了不同类如何协同工作以支持游戏开发的各个方面，接下来可以开始学习运行时 API 了。运行时 API（MmgCore）负责设置游戏的环境和资源。在本章中，我们将介绍以下类和主题：

*   MmgApiGame
*   MainFrame
*   GamePanel
*   主题：启动游戏

## 静态主入口点：MmgApiGame

`MmgApiGame` 类是游戏的入口点。这是游戏启动时执行的代码。该类包含在 MmgCore API 中，是一个完全配置好的游戏静态主入口点示例。这个占位类不会运行实际的游戏，但会执行实际游戏所需的所有资源和配置加载步骤。

该类设计用于接收命令行参数、设置游戏面板和窗口，以及处理游戏引擎配置文件。游戏代码的这一部分做了大量工作。我们可以将此类用作游戏静态主入口点的模板。让我们来看一些代码！

### 静态类成员

`MmgApiGame` 类有一些重要的静态类字段，我在下面的分组中列出。正如我之前提到的，这个类中的所有内容都是静态的，这很合理，因为它是静态入口点。在开始审查过程之前，先看看以下字段。

```
//窗口字段
public static MainFrame mf;
public static GamePanel pnlGame;
//帧率字段
public static RunFrameRate fr;
public static Thread t;
public static long FPS = 16l;
//窗口尺寸
public static int WIN_WIDTH = 858;
public static int WIN_HEIGHT = 600;
public static int PANEL_WIDTH = 854;
public static int PANEL_HEIGHT = 596;
public static int GAME_WIDTH = 854;
public static int GAME_HEIGHT = 416;
//参数和配置
public static String[] ARGS = null;
public static String ENGINE_CONFIG_FILE = "../cfg/engine_config.xml";
列表 10-1
MmgApiGame 静态类成员 1
```

第一组字段是窗口字段。它们包含一个 `MainFrame` 对象和一个 `GamePanel` 对象。如果你在跟进 C# 代码，会注意到那里只有一个 `GamePanel` 字段。C#/MonoGame 实现的游戏引擎没有对应的 `MainFrame` 类。该功能已合并到 `GamePanel` 类中。下一组字段用于控制游戏的帧率。

在 Java 版本的游戏引擎中，一个工作线程负责每隔几毫秒运行一帧，具体取决于你设置的每秒帧数。这就是 `fr`（`RunFrameRate`）字段的用途。C# 版本的游戏引擎运行在 MonoGame 之上，它比 Java Swing 更适合构建游戏，因此帧率由 MonoGame 库内部控制。我们只需设置所需的帧率，其余部分由库自动处理。

`Thread` 实例 `t` 在 Java 实现中用于在独立线程中运行帧率工作器。`FPS` 字段保存当前所需的帧率。游戏引擎会尝试达到此帧率，但在所有情况下并非都能实现。这很大程度上取决于你使用的游戏引擎版本、你制作的游戏类型及其要求。不要将帧率设置得过高，以免导致系统问题。所以不要设置 1000 的帧率！

在窗口模式下运行且 IDE 正在运行时，建议使用保守的帧率。这样会生成更少的日志，占用更少的资源，同时仍能让你充分测试和调试游戏。话虽如此，如果你有特殊的帧率要求，应该使用最大数量的对象测试游戏，看看它能跑多快。

Java 版本的引擎最好只在 30 帧或更低的帧率下使用。我通常以 18–24 FPS 进行开发。对于我制作的许多游戏来说，根本不需要 60 FPS。它们不是那种类型的游戏。C#/MonoGame 版本的引擎应该能够轻松达到 60 FPS。如果你将游戏改为全屏模式运行，还会看到更大的改进。我认为 Java 版本没有与全屏模式等效的功能，能让你获得作为唯一活动程序的效率优势。这是因为在 Java 中，你只能假装处于全屏模式，而实际上只是将视口最大化。

下一组字段定义了尺寸。这些字段描述了游戏窗口各个方面的尺寸。窗口尺寸描述了游戏运行的外部操作系统窗口的尺寸。面板尺寸控制窗口内容的大小。在大多数情况下，面板和窗口的大小应该相同。最后，游戏有自己的一组尺寸。这些尺寸应该适合面板和窗口内部，但如果不符合，引擎会尝试缩放游戏尺寸以适应。


这在测试不同屏幕尺寸的游戏时非常有用，可以直观感受其外观和运行效果，但你应该预先配置好尺寸，使游戏无需缩放。最后两个字段与游戏引擎定制有关。提供给游戏的命令行参数存储在 `ARGS` 字段中。你可以使用 CLI 参数来修改游戏尺寸等设置。

最后一个条目是用于处理配置设置的游戏引擎配置文件。这可以通过 CLI 参数进行修改，因此你可以从游戏启动命令驱动游戏引擎设置。这让你能够以数据驱动、动态的方式运行自定义游戏设置。接下来，让我们看看静态类方法。

```
//入口点
public static final void main(String[] args) { ... }
//配置方法
public static String ArrayHasEntryLike(String v, String[] s) { ... }
public static void SetField(DatConstantsEntry ent, Field f) throws Exception { ... }
//操作系统支持方法
public static void RunOsSpecificCode() { ... }
public static void LoadNativeLibraries() { ... }
public static boolean isWindows(String OS) { ... }
public static boolean isMac(String OS) { ... }
public static boolean isUnix(String OS) { ... }
public static boolean isSolaris(String OS) { ... }
清单 10-2
MmgApiGame 静态类成员 2
```

列出的第一个方法是静态主入口点。它作为运行时进程的起点，执行以下步骤序列：

1.  运行操作系统特定代码（如果启用）。
2.  加载原生库（如果启用）。
3.  处理命令行参数。
4.  处理引擎配置文件。
5.  初始化主窗口。
6.  初始化游戏面板。
7.  启动帧率工作线程。

C# 实现运行以下略有不同的步骤序列：

1.  运行操作系统特定代码（如果启用）。
2.  加载原生库（如果启用）。
3.  处理命令行参数。
4.  处理引擎配置文件。
5.  初始化游戏面板。

`ArrayHasEntryLike` 方法用于在命令行参数列表中查找条目。引擎开箱即支持以下 CLI 驱动的属性：

*   `WIN_WIDTH`
*   `WIN_HEIGHT`
*   `PANEL_WIDTH`
*   `PANEL_HEIGHT`
*   `FPS`
*   `ENGINE_CONFIG_FILE`

你可以根据游戏的特定需求轻松添加自己的条目。`SetField` 方法用于处理游戏引擎配置文件。在处理文件时，它会在指定类中设置指定的静态字段值。游戏引擎开箱即支持 `GameSettings`、`MmgHelper` 和 `MmgApiGame` 类。同样，你可以根据需要添加对任何游戏特定类的支持。

最后一组方法是操作系统支持方法。这些方法用于确定游戏运行在哪个操作系统上，并执行操作系统特定代码或加载用于手柄支持的操作系统特定库。C# 实现不需要原生库支持，因为它内置了手柄支持。

### 深入解析：MmgApiGame

MmgCore API 是一个比我们在第 1 部分中回顾的 MmgBase API 更高级别的 API。因此，该库中的类非常专业化，通常只使用一次。有时，当一个类只在一个地方使用时，很难展示其使用示例。

与通常的演示部分不同，我们将进行一个“深入解析”部分，深入介绍我们正在审查的类的一个方面。以下代码片段来自 `MainApiGame` 类的 `main` 方法的末尾。

```
01 mf = new MainFrame(WIN_WIDTH, WIN_HEIGHT, PANEL_WIDTH, PANEL_HEIGHT, GAME_WIDTH, GAME_HEIGHT);
02 pnlGame = new GamePanel(mf, PANEL_WIDTH, PANEL_HEIGHT, (WIN_WIDTH - PANEL_WIDTH) / 2, (WIN_HEIGHT - PANEL_HEIGHT) / 2, GAME_WIDTH, GAME_HEIGHT);
03 mf.SetGamePanel(pnlGame);
04 mf.InitComponents();
05 fr = new RunFrameRate(mf, FPS);

07 mf.setSize(WIN_WIDTH, WIN_HEIGHT);
08 mf.setResizable(false);
09 mf.setVisible(true);
10 mf.setName(GameSettings.NAME);

12 if (GameSettings.DEVELOPMENT_MODE_ON == false) {
13     mf.setTitle(GameSettings.TITLE);
14 } else {
15     mf.setTitle(GameSettings.TITLE + " - " + GameSettings.DEVELOPER_COMPANY + " (" + GameSettings.VERSION + ")");
16 }

18 mf.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
19 mf.GetGamePanel().PrepBuffers();
20 t = new Thread(fr);
21 t.start();
清单 10-3
MmgApiGame 深入解析 1
```

代码的前两行展示了 `MainFrame` 和 `GamePanel` 类的初始化。在第 3-4 行，主窗口设置了游戏面板并初始化了其组件。`RunFrameRate` 字段 `fr` 在第 5 行初始化。窗口的尺寸和某些类属性在第 7-10 行设置。如果开发模式标志设置为 true，则标题会配置为包含比平时更多的游戏信息。

`MainFrame` 负责处理游戏关闭事件，为此，我们设置了窗口应使用的退出操作类型。第 18 行的代码将主窗口配置为在窗口关闭时退出。游戏面板的绘图表面（缓冲区）在第 19 行准备就绪；帧率线程在第 20 和 21 行启动。此时，游戏已启动并运行，第一个游戏画面正在渲染。

## 静态主入口点：MainFrame

`MainFrame` 类扩展了 Java 框架的 `JPanel` 类，负责创建游戏运行的窗口。`MainFrame` 类处理游戏关闭事件。你可能需要为此类添加代码，以处理游戏的特殊关闭任务。关闭事件在 `InitComponents` 方法中注册。此类还负责将由 `GamePanel` 类创建的画布添加到窗口中进行主动渲染。

### 类字段

`MainFrame` 类具有以下分组中列出的多个字段。这些字段大致属于它们所列出的组。

```
//尺寸
public final int winWidth;
public final int winHeight;
public final int panelWidth;
public final int panelHeight;
public final int gameWidth;
public final int gameHeight;
//偏移量
public final int myX;
public final int myY;
//游戏面板
public GamePanel pnlGame;
清单 10-4
MainFrame 类字段 1

第一组字段是从静态主类传入的尺寸。这些字段保存了游戏启动时配置的值的副本。偏移量组中列出了两个新字段。`myX` 和 `myY` 字段用于跟踪游戏面板在窗口内的偏移量。

最后一个字段 `pnlGame` 是 `GamePanel` 类的一个实例，负责呈现一个在 `MainFrame` 中绘制的画布。如果你使用的是 C#，则不会看到相同的结构。你只会看到 `GamePanel` 类处理与 Java 实现中的 `MainFrame` 和 `GamePanel` 类相同的职责。



### 支持方法详解

该类的支持方法大部分是直接的 get 方法。此外，还有一些方法可以开放对 `pnlGame` 字段的访问，并强制游戏面板重绘。你极少会以任何方式与此类交互。它的主要任务只是启动一个具有适当尺寸的窗口，然后移交控制权。尽管如此，我们仍有必要回顾一下这些方法，那么让我们开始吧！

```
//尺寸方法
public int GetWindowWidth() { ... }
public int GetWindowHeight() { ... }
public int GetGamePanelWidth() { ... }
public int GetGamePanelHeight() { ... }
public int GetGameWidth() { ... }
public int GetGameHeight() { ... }
//偏移量方法
public int GetOffsetX() { ... }
public int GetOffsetY() { ... }
//游戏面板方法
public void SetGamePanel(GamePanel gp) { ... }
public GamePanel GetGamePanel() { ... }
//重绘方法
public void Redraw() { ... }
清单 10-5
MainFrame 支持方法详解 1
```

前面列出的支持方法遵循的顺序和分组与我们之前回顾的关联类字段相同。这里没有什么特别需要说明的。这些方法只是提供了对不同类字段的访问。有一个新方法，即 `Redraw` 方法。此方法可用于强制游戏面板重绘。

### 主要方法详解

`MainFrame` 类有一组数量适中的主要方法。尽管该类在游戏启动过程中很重要，但它实际上只是将游戏面板的画布连接到窗口框架。因此，没有任何非常复杂的主要方法。让我们看看下面列出的那些方法。

```
//构造函数
public MainFrame(int WinWidth, int WinHeight) { ... }
public MainFrame(int WinWidth, int WinHeight, int PanWidth, int PanHeight, int GameWidth, int GameHeight) { ... }
//主要方法
public void SetFrameRate(long fr, long rfr) { ... }
public void InitComponents() { ... }
清单 10-6
MainFrame 主要方法详解 1
```

前面列出的前两个条目是类的构造函数。第一个构造函数看起来功能稍弱，但实际上，当你处理一个非开发阶段的游戏，并且可以将游戏面板和窗口设置为相同尺寸时，它会派上用场。第二个构造函数可能是你最常用的，因为它能让你更精细地控制游戏面板和窗口的大小。

下一个方法的名字非常具有误导性。此方法实际上并不设置帧率。它设置的是开发头中的帧率调试文本，该文本仅在 `MmgHelper` 字段 `LOGGING_ON` 设置为 true 时才会显示。最后一个条目是 `InitComponents` 方法。我们看到这个方法是从静态 main 类中调用的。它负责强化窗口设置并注册关闭事件处理器。至此，我们对 `MainFrame` 类的回顾就结束了。接下来，我们将深入探讨该类中的一个特定代码块。

### 深入解析：MainFrame

对 `MainFrame` 类的深入代码审查来自该类的 `InitComponents` 方法。让我们看一些代码！

```
01 add(pnlGame.GetCanvas());

03 pnlGame.GetCanvas().setFocusable(true);
04 pnlGame.GetCanvas().requestFocus();
05 pnlGame.GetCanvas().requestFocusInWindow();

07 setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
08 addWindowListener(new WindowListener() {
09     public void windowClosing(WindowEvent e) {
10         try {
11             MmgHelper.wr("MainFrame: WindowClosing");
12             GamePanel.PAUSE = true;
13             GamePanel.EXIT = true;
14             RunFrameRate.PAUSE = true;
15             RunFrameRate.RUNNING = false;
16         } catch (Exception ex) {
17             MmgHelper.wrErr(ex);
18         }
19         dispose();
20     }

22     ...

24 });
清单 10-7
MainFrame 深入解析 1
```

第一行代码非常重要。这里将画布（即游戏绘制其内容的表面）设置为当前窗口的活动画布。在第 3-5 行，窗口属性被强化。关闭事件类型在第 7 行设置。这告诉窗口类，当窗口关闭时，它应该退出游戏。关闭事件处理器在第 8 行注册。

该处理器以内联匿名类的形式定义。你会注意到 `windowClosing` 方法被定义了，这就是我们放置任何游戏关闭代码的地方。请看下面代码片段中列出的 C# 版本引擎中关闭事件处理器的功能等效代码。

```
01 try
02 {
03     MmgHelper.wr("GamePanel: WindowClosing");
04     GamePanel.PAUSE = true;
05     GamePanel.EXIT = true;
06     Dispose();
07     Environment.Exit(0);
08 }
09 catch (Exception ex)
10 {
11     MmgHelper.wrErr(ex);
12 }
清单 10-8
MainFrame 深入解析 2
```

这段代码来自 MmgCore 命名空间中 C# 项目的 `GamePanel` 类，具体是 `windowClosing` 方法。我们创建并命名该方法以匹配 Java 版本中的命名。此方法被注册为程序的退出处理器。请注意，C# 版本中的代码与 Java 版本几乎逐行相同。另外，请注意所有 MmgBase API 代码完全相同。

## 静态主入口点：GamePanel

`GamePanel` 类是我们需要回顾的最后一个静态主类。这个类非常重要。它是输入和游戏屏幕之间的连接点。它是 MmgCore API 和 MmgBase API 之间的连接点，并且它提供了游戏引擎绘制例程，这些例程驱动了我们在本书第一部分中看到的所有示例屏幕。

### 静态类成员

该类的静态成员如下所列，大致按注释标题分组。

```
//游戏控制
public static boolean EXIT = false;
public static boolean PAUSE = false;
//游戏尺寸
public static int GAME_HEIGHT = 416;
public static int GAME_WIDTH = 854;
//开发 HUD
public static String FPS = "Drawing FPS: 0000 Actual FPS: 00";
public static String VAR1 = "** EMPTY **";
public static String VAR2 = "** EMPTY **";
//通用字段
public static GameType GAME_TYPE = GameType.GAME_NEW;
public static Hashtable VARS = new Hashtable();
清单 10-9
GamePanel 静态类成员 1
```

`EXIT` 和 `PAUSE` 字段用于控制游戏的执行。`EXIT` 字段不会导致游戏退出；它表示游戏正在退出。游戏尺寸存储在静态字段中。这些用于创建游戏的绘制表面。下一组静态字段仅在 `MmgHelper` 类的 `LOGGING_ON` 标志设置为 true 时才会生效。如果是这样，游戏窗口顶部会绘制一个头部信息。

请注意，如果你计划使用开发头部，则必须将窗口尺寸设置为包含该头部的空间。该头部信息会在每一帧绘制，并为你提供当前帧率的信息。此外，还有两个变量会显示出来。你可以使用这些变量在游戏过程中显示调试信息。

最后两个字段是通用字段。`GAME_TYPE` 字段用于指示正在运行的游戏类型。`VARS` 静态字段可用于保存全局数据，这些数据可以从游戏中的任何位置轻松访问。



### 枚举

接下来，你猜对了，是枚举。在下面的代码块中列出了两个非常重要的枚举供我们查看。

```
01 public enum GameStates {
02     LOADING,
03     BLANK,
04     SPLASH,
05     MAIN_MENU,
06     ABOUT,
07     HELP_MENU,
08     HELP_PROLOGUE,
09     HELP_ITEM_DESC,

11     ...

13     GAME_SCREEN_39,
14     GAME_SCREEN_40
15 }
01 public enum GameType {
02     GAME_NEW,
03     GAME_CONTINUE,
04     GAME_ONE_PLAYER,
05     GAME_TWO_PLAYER,
06     GAME_NETWORK_TWO_PLAYER,
07     GAME_NETWORK_TWO_PLAYER_P1,
08     GAME_NETWORK_TWO_PLAYER_P2,
09     GAME_NETWORK_TWO_PLAYER_LEFT,
10     GAME_NETWORK_TWO_PLAYER_RIGHT
11 }
代码清单 10-10
GamePanel 枚举 1
```

现在，`GamePanel` 类承担着多项职责。它实际上是游戏引擎的核心。其中一项职责是管理游戏屏幕。粗略来说，游戏中的每个屏幕都应该对应一个游戏屏幕类。该类旨在将游戏屏幕与 `GameState` 值关联起来。为了方便使用，第一个枚举中包含了一些默认的游戏状态，以及多达 40 个可用于任何用途的通用游戏状态。

你可以随意扩展此枚举中的条目，以适应你的下一个游戏。第二个枚举同样重要。它描述了正在运行的游戏类型。其中包含一些默认值，用于指示单人游戏、双人游戏以及不同的网络游戏。如果你只是在制作一个简单的单人游戏，也请尽量使用这个枚举。你永远不知道什么时候可能想回过头来添加网络支持。

### 类字段

可以想见，一个如此重要的类会拥有相当数量的类字段，并且通常还会有一长串的支持方法。`GamePanel` 类也不例外。请看下面分组中列出的第一组类字段，供我们查看。

```
//游戏定位
public MainFrame mf;
public int winWidth;
public int winHeight;
public int myX;
public int myY;
//缩放后的游戏定位
public double scale = 1.0;
public int sWinWidth;
public int sWinHeight;
public int sMyX;
public int sMyY;
//游戏状态管理
public Hashtable gameScreens;
public MmgGameScreen currentScreen;
public GameStates prevGameState;
public GameStates gameState;
//游戏绘制
public MmgPen p;
public Canvas canvas;
public BufferStrategy strategy;
public BufferedImage background;
public Graphics2D backgroundGraphics;
public Graphics2D graphics;
public int updateTick = 0;
代码清单 10-11
GamePanel 类字段 1
```

第一组字段包含一个对主框架的引用。你可以将主框架视为游戏面板类的父类。接下来的四个条目用于跟踪游戏的尺寸和游戏面板的偏移量。下面的一组字段非常相似，但这些字段是在游戏面板需要缩放以适应窗口框架时应用的。

下一组字段非常重要。这组字段包含了游戏状态管理功能。该组中的第一个字段为你提供了一个数据结构，你可以用它来跟踪正在使用的游戏屏幕。你可以选择不使用这个数据结构，但如果你觉得合适，它就在那里供你使用。`currentScreen` 字段表示当前活动的游戏屏幕，它接收游戏面板接收到的所有输入，并接入游戏引擎的更新和绘制调用。

接下来的两个字段，`gameState` 和 `prevGameState`，用于跟踪游戏面板类的当前和上一个游戏状态。这样做的原因是，在下一个游戏屏幕接管之前，可以清理上一个游戏屏幕并释放其资源。最后一组类字段属于类的绘制例程。`MmgPen` 实例 `p` 用于通过 `backgroundGraphics` 框架绘制类在 `background` 图像上进行绘制。

`canvas` 字段用于保存一个框架级别的类，该类代表窗口的可绘制区域。`strategy` 字段用于配置框架的绘制过程，使其包含对画布的双缓冲。这意味着在内部，`canvas` 将支持两个绘制表面。一个表面正在显示，而另一个代表下一帧游戏画面的表面正在被绘制。

当第二帧准备就绪时，两个表面会进行翻转，然后重复该过程。正如我之前提到的，`background` 和 `backgroundGraphics` 用于将游戏绘制到一个表面上，然后使用 `graphics` 字段将该游戏帧绘制到窗口的画布上。最后但同样重要的是，`updateTick` 字段作为更新调用的一部分使用。它记录更新调用被执行的次数。这对应于游戏已显示的帧数。我们还没完。还有另一组类字段等着我们查看。在我们深入查看它们之前，先看看下面分组中列出的字段。



```
//开发标题绘制
public Font debugFont;
public Color debugColor = Color.WHITE;
private MmgFont mmgDebugFont = null;
//其他
public int lastX;
public int lastY;
public long lastKeyPressEvent = -1;
public int mouseOffsetX = 0;
public int mouseOffsetY = 0;
public static GameType GAME_TYPE = GameType.GAME_NEW;
//游戏屏幕
public ScreenSplash screenSplash;
public ScreenLoading screenLoading;
public ScreenMainMenu screenMainMenu;
//游戏引擎上下文数据
public MmgScreenData screenData;
public MmgFontData fontData;
//手柄支持
public GamePadHub gamePadHub;
public GamePadHubRunner gamePadRunner;
public Thread gpadTr;
public GpioHub gpioHub;
public GpioHubRunner gpioRunner;
public Thread gpioTr;
清单 10-12
GamePanel 类字段 2
```

当 `MmgHelper` 类中的日志记录标志设置为 true 时，引擎会在游戏面板上方添加一个特殊的调试标题。`debugFont`、`mmgDebugFont` 和 `debugColor` 类字段用于在开发标题中绘制调试文本。`lastX` 和 `lastY` 字段用于追踪鼠标最后记录的 X 和 Y 坐标。

这些信息可用于判断鼠标拖放操作等行为。`lastKeyPressEvent` 字段记录键盘按键最后一次被按下的时间。鼠标偏移字段允许你通过为鼠标定位添加 X 或 Y 偏移量来微调鼠标光标。`GAME_TYPE` 字段保存一个值，指示正在运行的游戏类型：单人、双人、网络、本地等。

接下来是游戏屏幕。我们决定不使用数据结构来存储游戏屏幕，而是为此实现所支持的屏幕创建类字段。此示例游戏运行时显示启动屏幕、加载屏幕和主菜单屏幕。`MmgScreenData` 和 `MmgFontData` 实例（即 `screenData` 和 `fontData`）应该让你感到熟悉，因为你在本书第一部分中已经了解过它们。这些字段对于访问引擎的屏幕和字体运行时数据非常有用。

最后一组字段与手柄支持有关。游戏引擎支持两种类型的手柄。在 Java 版本中，通过 jInput 库支持传统的 USB 手柄；在 C# 版本中，则通过 MonoGame 原生支持。另一种类型的手柄仅在 Linux 操作系统上受支持。此实现允许你使用单板计算机上的 GPIO 引脚，通过面包板、一些按钮和跳线来创建手柄。然后你可以使用 GPIO 手柄来控制游戏。我不会在文本中详细讨论手柄或 GPIO 支持，但我鼓励你尝试使用这些类。

手柄集线器用于读取手柄状态并将输入转换为按钮信息。然后，这些数据由手柄集线器运行器处理，该运行器以一定间隔轮询手柄以获取数据。线程 `gpadTr` 用于在独立于主游戏线程的单独线程中运行手柄处理代码。代码块中的最后三个字段与我们刚刚讨论的手柄字段完全相同，只是它们被设计为与 Linux 文件系统和 GPIO 接口配合使用。

### 支持方法详解

由于 `GamePanel` 类拥有大量的支持方法，我决定将输入处理方法单独划分到一个章节中。让我们从回顾下面列出的支持方法开始。

```
//支持方法
public Hashtable GetGameScreens() { ... }
public void SetGameScreens(Hashtable GameScreens) { ... }
public MmgGameScreen GetCurrentScreen() { ... }
public void SetCurrentScreen(MmgGameScreen CurrentScreen) { ... }
public int GetWinWidth() { ... }
public int GetWinHeight() { ... }
public int GetX() { ... }
public int GetY() { ... }
清单 10-13
GamePanel 支持方法详解 1
```

前四个条目分别是 `gameScreens` 和 `currentScreen` 字段的 get 和 set 方法。接下来的两个方法提供对窗口尺寸的访问，而 `GetX` 和 `GetY` 方法则提供对游戏面板 X 和 Y 偏移量的访问。

### 输入方法详解

输入处理方法非常多，我决定再次为它们单独设立一个章节。这些方法不言自明，因此我不会详细讨论它们。当你阅读它们时，请思考如何在你的下一个游戏中使用它们。

```
//鼠标移动方法
public void ProcessMouseMove(int x, int y) { ... }
//鼠标按钮方法
public void ProcessMousePress(int x, int y, int btnIndex) { ... }
public void ProcessMouseRelease(int x, int y, int btnIndex) { ... }
public void ProcessMouseClick(int x, int y, int btnIndex) { ... }
//A 按钮方法
public void ProcessAPress(int src) { ... }
public void ProcessARelease(int src) { ... }
public void ProcessAClick(int src) { ... }
//B 按钮方法
public void ProcessBPress(int src) { ... }
public void ProcessBRelease(int src) { ... }
public void ProcessBClick(int src) { ... }
//键盘按键方法
public void ProcessKeyPress(char c, int code) { ... }
public void ProcessKeyRelease(char c, int code) { ... }
public void ProcessKeyClick(char c, int code) { ... }
//方向键输入
public void ProcessDpadPress(int dir) { ... }
public void ProcessDpadRelease(int dir) { ... }
public void ProcessDpadClick(int dir) { ... }
//调试方法
public void ProcessDebugClick() { ... }
清单 10-14
GamePanel 输入方法详解 1
```

我唯一想讨论的方法是 `ProcessDebugClick` 方法。此方法默认映射到键盘的“D”键，如果按下，它将调用游戏屏幕的 `ProcessDebugClick` 方法。这是一个非常适合输入一些调试代码的地方，这些代码可以将信息打印到标准输出。



### 主要方法详解

`GamePanel` 类包含几个主要方法，按以下分组列出，值得我们探讨。

```
//渲染方法
public void PrepBuffers() { ... }
public BufferedImage create(int width, int height, boolean alpha) { ... }
public Canvas GetCanvas() { ... }
public Graphics2D GetBuffer() { ... }
public boolean UpdateScreen() { ... }
public void UpdateGame() { ... }
public void RenderGame() { ... }
//其他主要方法
public GamePanel(MainFrame Mf, int WinWidth, int WinHeight, int X, int Y, int GameWidth, int GameHeight) { ... }
public void SwitchGameState(GameStates g) { ... }
public void HandleGenericEvent(GenericEventMessage obj) { ... }
代码清单 10-15
GamePanel 主要方法详解 1
```

`PrepBuffers` 方法将画布缓冲策略设置为使用两个缓冲区，即双缓冲。该方法还负责准备用于绘制游戏面板的 `background` 和 `backgroundGraphics` 字段。`GetCanvas` 方法返回 `GamePanel` 的画布字段，而 `GetBuffer` 方法则从画布中获取下一个活动缓冲区。

`UpdateScreen` 方法会触发画布的双缓冲翻转，并创建一个新缓冲区。`UpdateGame` 方法运行游戏引擎绘制例程中的更新调用。这会导致当前游戏画面的 `MmgUpdate` 方法在每个游戏帧被调用。最后但同样重要的是，`RenderGame` 方法负责构建游戏帧并将其绘制到画布上。这会导致当前画面的 `MmgDraw` 方法在每个帧被调用。

接下来要审视的方法是类的构造函数。只有一个构造函数，所以你得接受它。它被设计为接入静态主类，并在 `MainFrame` 初始化后被调用。它接收主框架的引用以及一些尺寸和偏移信息作为参数。此构造函数将创建一个可供使用的新类实例。

`SwitchGameState` 方法是 `GamePanel` 类中用于切换游戏状态及后续游戏画面的默认方法。该方法负责清理上一个游戏状态。这会导致上一个游戏画面的 `UnloadResources` 方法被调用。完成后，下一个游戏画面会通过调用其 `LoadResources` 方法进行准备。随后它成为当前画面，并主动绘制到游戏窗口中。

我们要审视的最后一个方法是 `HandleGenericEvent` 方法。该方法处理来自启动画面和加载画面类的通用事件。启动画面类在其显示时间结束时发送一个事件。加载画面类在加载过程完成时发送一个事件。游戏面板类通过将游戏状态更改为游戏加载序列中的下一个状态来响应这些事件。主菜单是启动过程中的最后一个状态。

### 深入剖析：GamePanel

对于该类的深入剖析部分，我们将查看 `RenderGame` 方法中的一段代码。请看下面的代码清单。

```
01     p.SetGraphics(g);
02     p.SetAdvRenderHints();
03     currentScreen.MmgDraw(p);

05     if (MmgHelper.LOGGING == true) {
06         tmpF = g.getFont();
07         g.setFont(debugFont);
08         g.setColor(debugColor);
09         g.drawString(GamePanel.FPS, 15, 15);
10         g.drawString("Var1: " + GamePanel.VAR1, 15, 35);
11         g.drawString("Var2: " + GamePanel.VAR2, 15, 55);
12         g.setFont(tmpF);
13     }
14 }

16 //如果启用了缩放，则将背景缓冲区的状态缩放绘制到屏幕缓冲区
17 if (scale != 1.0) {
18     bg.drawImage(background, sMyX, sMyY, sWinWidth, sWinHeight, 0, 0, winWidth, winHeight, null);
19 } else {
20     bg.drawImage(background, myX, myY, null);
21 }
代码清单 10-16
GamePanel 深入剖析 1
```

这段看似无害的小代码实际上是游戏引擎绘制例程的核心。前两行代码设置了 `MmgPen` 字段 `p`，用于在目标表面 `g` 上绘制。请记住，`g` 是承载一个游戏帧的表面，并由当前游戏画面渲染。设置了高级渲染提示，并在第 3 行绘制了画面。第 5 到 13 行的代码块是调试头部信息，如果游戏开启了日志记录，则会绘制该信息。这个头部信息显示游戏的帧率以及两个用于调试的变量。

在此示例中，图形对象 `g` 被设置为在 `background` 图像上绘制。这是将承载游戏下一帧的表面。请注意，在第 17–21 行，图像使用框架的画笔类 `bg` 绘制到画布上。注意，由于我们在内部表面（即 `background` 图像）上绘制，因此为游戏渲染引擎实现了一个三缓冲系统。由当前画面渲染的游戏帧随后被传递到画布的内部双缓冲区。

## 主题：启动游戏

这是本书这一部分中我们遇到的第三个新的审视部分。我们在本章及之前的章节中已经见过“输入方法详解”和“深入剖析”部分。我想在这里介绍的新审视部分是“主题”审视部分。在这种类型的审视部分中，我们不会审视某个类或类的用法。相反，我们将审视一个更宏观的概念。在本例中，这个概念就是“启动游戏”。



### 启动游戏：Java

首先，我们来了解一下 Java 游戏引擎的实现以及启动游戏的不同方式。导航到你在 NetBeans IDE 中使用的项目文件夹。找到 `MmgGameApiJava` 项目并打开该文件夹。主项目文件夹中应该有一个 **dist** 文件夹。

如果没有，请打开 IDE 并右键单击项目。选择“清理并构建”选项，并在弹出的对话框中确认强制构建。如果项目配置正确，你现在应该会看到一个 **dist** 文件夹。如果仍然没有看到该文件夹，请返回第 1 章，重新进行 Java 设置过程。

**dist** 文件夹内是游戏编译版本的存放位置。为了启动游戏，我们需要知道如何运行一些命令行界面、控制台命令。在 Windows 中，你应该使用命令程序 **cmd.exe**。你可以在“开始”菜单中找到该程序，或者在运行栏中搜索 **cmd**。

对于其他平台，你只需要打开一个终端。如果你不知道如何操作，只需在互联网上搜索如何为你的特定操作系统打开终端窗口的说明。打开终端窗口后，我们需要验证 Java 运行时环境是否正确设置。在控制台窗口中运行以下命令：

```
java –version
```

如果收到一条消息，提示找不到 Java 程序，那么我们必须确保 Java 已添加到系统的路径环境变量中。在 Windows 中，如果找不到 Java，你将看到以下消息。在 Mac OSX 和 Linux 上，你会看到类似的消息：

```
'java' 不是内部或外部命令，也不是可运行的程序或批处理文件。
```

如果是这种情况，你需要访问以下链接，或在互联网上搜索“将 Java 添加到路径”，并查看排名靠前的结果之一：

[`https://docs.oracle.com/javase/tutorial/essential/environment/paths.html`](https://docs.oracle.com/javase/tutorial/essential/environment/paths.html)

如果一切正常运行，那么你将看到类似下面列出的消息，而不是错误消息：

```
java version "12.0.1" 2019-04-16 Java(TM) SE Runtime Environment (build 12.0.1+12) Java HotSpot(TM) 64-Bit Server VM (build 12.0.1+12, mixed mode, sharing)
```

Java 实现支持在单个 jar 文件中使用多个静态 main 入口点。默认的静态 main 是来自 `MmgTestSpace` 库的 `MmgTestScreens` 类。这个静态 main 增加了指定启动哪个测试的功能。你将在以下示例中看到添加的命令行参数。

### 执行默认静态 Main

```
java -jar MmgGameApiJava.jar
```

### 执行带参数的默认静态 Main

```
java -jar MmgGameApiJava.jar FPS=30 TEST=1
```

### 执行带原生库路径和参数的默认静态 Main

```
java -Djava.library.path= C:\netbeans_projects\MmgGameApiJava\lib\jinput-platform\native-libs -jar MmgGameApiJava.jar FPS=30 TEST=1
```

在接下来的两个示例中，我们指定了要从命令行运行的静态 main 类。请注意，在第二个示例中，我们通过 `MmgCentralMain` 运行了 `MmgApiGame` 静态 main。这样做的原因是 C# 框架不允许存在多个静态 main 入口点。为了保持两个项目在功能上等效，我创建了一个可以配置为运行许多不同 main 类的入口点。

### 执行带参数的目标静态 Main

```
java –cp MmgGameApiJava.jar net.middlemind.MmgGameApiJava.MmgCore.MmgApiGame FPS=30
```

### 执行带目标 Main 和参数的中心 Main

```
java –cp MmgGameApiJava.jar net.middlemind.MmgGameApiJava.MmgCore.MmgCentralMain mmgapigame FPS=15
```

### 所有 MmgCentralMain 执行命令

```
//执行示例应用程序
java MmgGameApiJava.jar mmgtestspace
//执行最简静态 main
java MmgGameApiJava.jar mmgapigame
//执行 PongClone 第 16 章项目
java MmgGameApiJava.jar chapter16
//执行 PongClone 第 17 章项目
java MmgGameApiJava.jar chapter17
//执行 PongClone 第 18 章项目
java MmgGameApiJava.jar chapter18
//执行 DungeonTrap 第 20 章项目
java MmgGameApiJava.jar chapter20
//执行 DungeonTrap 第 21 章项目
java MmgGameApiJava.jar chapter21
//执行 DungeonTrap 第 22 章项目
java MmgGameApiJava.jar chapter22
//执行 DungeonTrap 第 23 章项目
java MmgGameApiJava.jar chapter23
//执行 DungeonTrap 第 23 章演示项目
java MmgGameApiJava.jar chapter23_demoscreen
//执行 DungeonTrap 第 24 章项目阶段 1
java MmgGameApiJava.jar chapter24_phase1
//执行 DungeonTrap 第 24 章项目阶段 2
java MmgGameApiJava.jar chapter24_phase2
//执行 DungeonTrap 第 24 章项目阶段 3
java MmgGameApiJava.jar chapter24_phase3
```

至此，关于如何启动 Java 版本游戏的介绍就结束了。接下来，我们将了解如何使用 C# 版本的引擎启动你的游戏。我应该提一下，编译后的游戏会查找资源目录，该目录位于其执行目录的上一级。因此，如果我们在 **dist** 文件夹中运行游戏，资源将位于 **../cfg** 目录中。



### 启动游戏：C#

让我们来看看 C# 游戏引擎的实现，以及启动游戏的不同方式。导航到你在 Visual Studio IDE 中使用的项目文件夹。找到 MmgGameApiCs 解决方案文件夹和同名的项目文件夹；打开该文件夹。你应该会在该目录中看到以下文件夹结构：**bin\Debug\netcoreapp3.1**。

如果没有，请打开 IDE 并右键单击项目。选择“重新生成”选项。现在，你的项目主目录中应该有一个正确的 **bin** 文件夹。如果仍然看不到文件夹，请返回第 1 章，重新完成 C# 设置过程。

在 **bin\Debug\netcoreapp3.1** 文件夹内，存放着游戏的编译版本。要启动游戏，我们需要知道如何运行一些命令行界面、控制台命令。在 Windows 中，你应该使用命令程序 **cmd.exe**。你可以在“开始”菜单中找到此程序，或在运行栏中搜索 **cmd**。

对于其他平台，你只需打开一个终端。如果你不知道如何操作，只需在互联网上搜索如何为你特定的操作系统打开终端窗口的说明。打开终端窗口后，我们需要验证 C# 运行时环境是否设置正确。在控制台窗口中运行以下命令：

```
dotnet -–version
```

如果你收到一条消息，指示找不到 DotNet 程序，那么我们必须确保 DotNet 位于系统的路径环境变量中。在 Windows 中，如果找不到 DotNet，你将看到以下消息。在 Mac OSX 和 Linux 上，你会看到类似的消息：

```
'dotnet' 不是内部或外部命令，也不是可运行的程序或批处理文件。
```

如果是这种情况，你需要访问以下链接或在互联网上搜索“将 dotnet 添加到路径”，并查看排名靠前的结果之一：

[`https://docs.microsoft.com/en-us/dotnet/core/tools/troubleshoot-usage-issues`](https://docs.microsoft.com/en-us/dotnet/core/tools/troubleshoot-usage-issues)

如果一切正常运行，那么你将看到一条相当平淡的消息，类似于下面列出的内容，而不是错误消息：

```
5.0.101
```

C# 实现不支持在单个库文件中使用多个静态 main 入口点。默认的静态 main 是来自 MmgCore 库的 `MmgCentralMain` 类。这个静态 main 增加了指定要启动哪个其他 main 类的功能。你将在以下命令示例中看到添加的命令行参数。

### 执行默认静态 Main

```
dotnet MmgGameApiCs.dll default
```

### 带参数执行默认静态 Main

```
dotnet MmgGameApiCs.dll default FPS=30 TEST=1
```

在接下来的两个示例中，我们指定了要从命令行运行的静态 main 类。这些示例演示了如何运行模板应用程序 `MmgApiGame` 和示例应用程序 `MmgTestScreens`。

### 使用 MmgTestScreens 和参数执行 Central Main

```
dotnet MmgGameApiCs.dll mmgtestspace TEST=20
```

### 使用 MmgApiGame 和参数执行 Central Main

```
dotnet MmgGameApiCs.dll mmgapigame FPS=15
```

### 所有 MmgCentralMain 执行命令

```
//执行示例应用程序
dotnet MmgGameApiCs.dll mmgtestspace
//执行基础静态 main
dotnet MmgGameApiCs.dll mmgapigame
//执行 PongClone 第 16 章项目
dotnet MmgGameApiCs.dll chapter16
//执行 PongClone 第 17 章项目
dotnet MmgGameApiCs.dll chapter17
//执行 PongClone 第 18 章项目
dotnet MmgGameApiCs.dll chapter18
//执行 DungeonTrap 第 20 章项目
dotnet MmgGameApiCs.dll chapter20
//执行 DungeonTrap 第 21 章项目
dotnet MmgGameApiCs.dll chapter21
//执行 DungeonTrap 第 22 章项目
dotnet MmgGameApiCs.dll chapter22
//执行 DungeonTrap 第 23 章项目
dotnet MmgGameApiCs.dll chapter23
//执行 DungeonTrap 第 23 章演示项目
dotnet MmgGameApiCs.dll chapter23_demoscreen
//执行 DungeonTrap 第 24 章项目阶段 1
dotnet MmgGameApiCs.dll chapter24_phase1
//执行 DungeonTrap 第 24 章项目阶段 2
dotnet MmgGameApiCs.dll chapter24_phase2
//执行 DungeonTrap 第 24 章项目阶段 3
dotnet MmgGameApiCs.dll chapter24_phase3
```

至此，关于如何启动 C# 版本游戏的回顾就结束了。我应该提一下，编译后的游戏会查找资源目录，该目录位于其执行目录的上三级目录。因此，如果我们在 **netcoreapp3.1** 文件夹中运行游戏，资源将位于 **../../../cfg** 目录中。你可以在 `GameSettings` 类中调整此配置。

## 章节总结

在本章中，我们完成了对 MmgCore API 运行时类的回顾。这些类可以说是代表了“执行代码”，即准备和执行游戏的代码。再次强调，我们选择将运行时代码（来自源代码）作为游戏项目设置的一部分包含在内。这使你可以完全控制游戏的启动过程。

我们了解了静态 main 入口点如何使用 `MainFrame` 类准备游戏窗口——在 C# 版本中，`GamePanel` 类负责处理此任务——并将 `GamePanel` 类附加到窗口的可绘制空间。我们回顾了游戏引擎的核心，即 `GamePanel` 类，它连接了 MmgCore API 和 MmgBase API，并管理所有游戏屏幕。让我们看看本章要点的总结：

*   MmgApiGame：一个 MmgCore API 类，用于运行一个模板化的、基础的游戏。该游戏将经历启动过程，显示启动画面和加载屏幕，最后进入主菜单。

*   MainFrame：一个 MmgCore API 类，负责在 Java 实现的游戏引擎中为游戏创建窗口框架。

*   GamePanel：一个 MmgCore API 类，是游戏引擎的核心。此类负责将输入路由到当前游戏屏幕、管理游戏屏幕等。这是 MmgCore API 中通过不同游戏屏幕连接到 MmgBase API 的关键点。

*   主题：启动游戏：在本主题部分，我们学习了如何通过控制台从命令行界面启动游戏。我们涵盖了可以运行的不同默认应用程序，以及如何为 Java 和 C# 实现运行它们。

至此，你应该对整体情况有所了解。MmgBase API 的结构和 MmgCore API 的主要方面已经向你详细说明。花些时间思考这些库及其结构——底层 API 如何为类的克隆、比较和互操作性提供大量支持，而中层 API MmgCore 则主要包含运行游戏引擎特定方面的单一、高度专业化的类。请特别注意 `GamePanel` 类及其绘制和更新例程。确保你理解这两个 API 是如何连接的。这里有一个提示：探索 `currentScreen` 类字段。

