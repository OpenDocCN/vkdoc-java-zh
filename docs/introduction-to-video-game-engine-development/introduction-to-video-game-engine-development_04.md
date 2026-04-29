# 3. 辅助类

在本章中，你将回顾 MmgBase API 的辅助类。辅助类通常易于初始化，使用静态方法来暴露功能，并为 API 的不同特性提供支持。在本章回顾中，我们将遇到以下类：

*   MmgApiUtils
*   MmgHelper
*   MmgScreenData
*   MmgFontData
*   MmgDebug
*   MmgBmpScaler
*   MmgMediaTracker

## 辅助类：MmgApiUtils

`MmgApiUtils`类是一个简单的辅助类，为 MmgBase API 提供集中的日志记录支持。`MmgApiUtils`类被实现为一个便捷的辅助类，因此它只有静态类成员需要回顾。

### 静态类成员

`logging`类字段用于控制类的输出。如果该布尔字段设置为 false，则不生成任何日志。该字段是一个公共字段，可以从游戏引擎配置文件中设置。这允许你以数据驱动的方式，在游戏不再处于开发阶段时关闭日志记录。接下来，我们将看看该类的静态方法。

```
public static void wr(String s) { ... }
public static void wrErr(Exception e) { ... }
public static void wrErr(String s) { ... }
清单 3-1
MmgApiUtils 静态类成员 1
```

我们要介绍的第一个方法`wr`为 API 提供基本的日志记录支持。它只接受一个字符串作为参数，如果日志记录已启用，则将其写入标准输出。接下来要回顾的两个方法`wrErr`旨在帮助记录错误消息。

第一个`wrErr`方法接受一个`Exception`实例作为参数，并使用该对象将异常信息打印到标准错误输出。该方法会打印出异常的消息，并遍历堆栈跟踪，打印出每个条目的信息。第二个`wrErr`方法接受一个字符串参数，并将其打印到标准错误输出。

### 演示：MmgApiUtils 类

`MmgApiUtils`类主要用作一级日志记录类。可以在`MmgDebug`类中找到其用法示例。让我们看一些代码！

```
1 public static void wr(String key, String s) {
2     if (DEBUGGING_ON == true) {
3         MmgApiUtils.wr(key + ": " + s);
4     }
5 }
清单 3-2
MmgApiUtils 类演示 1
```

上面列出的方法是一个二级日志记录方法，因为它有自己的日志控制字段，并且调用了`MmgApiUtils`类的方法。这意味着可以在此级别关闭日志记录，例如，针对所有游戏屏幕日志记录；也可以在`MmgApiUtils`级别关闭，例如，针对所有 MmgCore 应用程序日志记录。

至此，我们完成了对`MmgApiUtils`类的回顾。我希望你不要忽视这个类的重要性，不仅在于其简单直接的日志记录能力，还在于它作为新的 API 级代码的中心点，将使你的下一个游戏项目编写起来更加容易。

## 辅助类：MmgHelper

`MmgHelper`类是一个辅助类，通过静态方法为 MmgBase API 提供高级功能。这个类提供了定位方法、资源缓存管理方法、日志记录方法、图像变换方法等等。`MmgHelper`类是一个至关重要的类，它在游戏开发的许多不同方面提供帮助。



### 静态类成员

`MmgHelper` 类包含以下几个静态类字段。这些字段用作布尔标志，用于控制日志记录和资源缓存功能。

```
public static boolean LOGGING = true;
public static boolean BMP_CACHE_ON = true;
public static boolean SND_CACHE_ON = true;
private static Random rando = new Random(System.currentTimeMillis());
代码清单 3-3
MmgHelper 静态类成员 1
```

上述静态类字段中的第一个条目是 `LOGGING` 字段。这是一个布尔标志，类似于我们在 `MmgApiUtils` 类中看到的那样。该标志控制此类的日志输出，可用于关闭所有日志记录。如你所见，API 支持几种不同的日志通道。起初这看起来可能有些冗余，但实际上，当你希望为特定信息设置独立的日志通道时，它会非常有用。

上述列表中的接下来两个静态类字段分别控制图像和声音资源的缓存。如果这些布尔标志设置为 `false`，那么通常会被存储在缓存中的资源将不会被缓存。列表中的最后一个条目是一个随机数生成器，它使用当前时间（以毫秒为单位）进行初始化。这应该能为随机数生成器提供一个良好的动态种子。你可以在需要访问随机数时随时使用 `rando` 字段。接下来，让我们看看一些静态类方法。

```
public static boolean WriteClassConfigFile(String file, MmgCfgFileEntry[] data) { ... }
public static boolean WriteClassConfigFile(String file, Hashtable data) { ... }
public static Hashtable ReadClassConfigFile(String file) { ... }
public static Hashtable ReadClassConfigFile(String file) { ... }
代码清单 3-4
MmgHelper 静态类成员 2
```

前两个静态类方法用于将作为参数传入的类配置数据写出。第一个 `WriteClassConfigFile` 方法接受一个字符串和一个数据数组作为参数。该数组包含 `MmgCfgFileEntry` 对象。类配置文件是一个文本文件，其中包含键值对形式的数据。这些数据可用于在类的资源加载过程中动态配置类的字段。

当与游戏屏幕结合使用时，这尤其有用，因为它允许你通过数据驱动的方式来布局游戏屏幕。通过这种方式，你可以通过修改类配置文件来测试游戏屏幕布局的更改，而无需重新编译游戏。

需要说明的是，当通过调用这些方法来写入类配置文件时，注释和数据的原始顺序可能会丢失。每个 `MmgCfgFileEntry` 实例中的数据都会被写入类配置文件。配置文件中的每个键值对都由一个 `MmgCfgFileEntry` 实例表示。

`WriteClassConfigFile` 方法的第二个实现类似，不同之处在于它接收的数据形式是 `Hashtable`（如果你查看的是 C# 实现，则为 `Dictionary`），而不是数组。该数据结构的键会按字母顺序排序，然后像第一个版本的方法一样将数据写出。现在，让我们看看另一面——类配置读取方法。

接下来我们要介绍的类配置辅助方法是 `ReadClassConfigFile` 方法。该方法解析指定的类配置文本文件，并将键值对作为 `MmgCfgFileEntry` 条目存储在一个 `Hashtable` 中。类配置读取方法内置了检测和加载特定屏幕分辨率类配置文件的支持。

如果存在特定于屏幕分辨率的类配置文件，这允许你根据当前游戏屏幕大小自动加载数据。如果没有，我们会尝试打开最初指定的文件。如果找到了要读取的类配置文件，我们会打开它并逐行迭代。类配置文件中的注释以 `#` 字符为前缀。在解析文件时，这些行会被忽略。



从类配置文件中读取的数据可以是两种类型之一：数值型或字母数字型。要表示数字，请在键和值之间使用“=”字符。要表示字符串，请在键和值之间使用“->”。数值可以作为整数、浮点数或双精度浮点数进行检索。以下列表显示了一个简单类配置文件的内容。

```
1 bmpLogo->logo_large.jpg
2 splashScreenDisplayTimeMs=2000.0
3 splashLogoScale=1.0
4 #splashLogoOffsetX=0
5 #splashLogoOffsetY=0
6 #splashLogoPosX=0
7 #splashLogoPosY=0
Listing 3-5
MmgHelper Static Class Members 3
```

请注意，当表示的数据是字符串（第 1 行）时，所使用的键值对分隔符与表示数值（第 2 行和第 3 行）时不同。让我们继续查看接下来的几个方法。接下来，我们有一对 `CreateDrawableBitmapSet` 方法。这些辅助方法用于创建一组对象（框架和 MmgBase API 对象），这些对象都已配置好，可以绘制到新的 `MmgBmp` 实例上。

该方法的第一个版本将尺寸（宽度和高度）以及一个布尔透明度指示符作为参数。第二个 `CreateDrawableBmpSet` 方法实际上是第一个方法的扩展。这个版本接受一个额外的颜色参数，用于填充新图像。

```
public static MmgBmp CreateFilledBmp(int width, int height, MmgColor color) { ... }
public static int AbsDistance(int x1, int x2, int y1, int y2) { ... }
public static int GetRandomInt(int exclusiveUpperBound) { ... }
public static void ListCacheEntries() { ... }
public static MmgDrawableBmpSet CreateDrawableBmpSet(int width, int height, boolean alpha) { ... }
public static MmgDrawableBmpSet CreateDrawableBmpSet(int width, int height, boolean alpha, MmgColor color) { ... }
Listing 3-6
MmgHelper Static Class Members 4
```

接下来我们要介绍的一组辅助方法有点杂乱无章。我们开始吧，好吗？第一个方法是 `CreateFilledBmp` 方法。`CreateFilledBmp` 方法延续了之前审查过的一组方法。该方法调用了接受颜色参数的 `CreateDrawableBmpSet` 方法版本。生成的 `MmgDrawableBmpSet` 会被丢弃，只保留该集合中的 `MmgBmp` 实例 `img`。这种实现允许你只获取所需的图像，并丢弃集合的其余部分。这对于快速创建彩色图块非常有用。

`AbsDistance` 方法是一个辅助方法，它根据两对坐标 `x1`、`y1` 和 `x2`、`y2` 计算并返回绝对距离。`GetRandomInt` 方法使用我们之前审查过的 `rando` 静态类字段。它返回一个介于 0 和 `exclusiveUpperBound` 之间的新随机数。显然，该范围不包含上界。下一个要查看的方法是前面列出的 `ListCacheEntries` 方法。此方法用于报告资源缓存的内容，包括图像和声音资源。

接下来我们要介绍的一组静态方法是定位方法，它们非常有用。你会在整个 MmgBase API 中看到这些方法的使用。

```
public static MmgObj CenterHor(MmgObj obj) { ... }
public static MmgObj CenterHorAndBot(MmgObj obj) { ... }
public static MmgObj CenterHorAndMid(MmgObj obj) { ... }
public static MmgObj CenterHorAndTop(MmgObj obj) { ... }
public static MmgObj CenterHorAndVert(MmgObj obj) { ... }
Listing 3-7
MmgHelper Static Class Members 5
```

接下来要审查的一组辅助方法都是关于居中对象的。前面列出的第一个方法 `CenterHor` 将给定的 `MmgObj` 实例水平居中。请注意，这些方法接受 `MmgObj` 参数。这意味着这些方法依赖于 `MmgObj` 类的字段和来自 `MmgScreenData` 类的屏幕尺寸数据。你会发现，你可以轻松地使用它们来定位任何可绘制对象，因为任何可绘制对象都扩展了 `MmgObj` 类。通用模型的力量可以从这些定位方法的实用性中看出。

`CenterHorAndBot` 方法将对象水平居中，并将对象定位在屏幕底部。`CenterHorAndMid` 方法只是 `CenterHorAndVert` 方法的别名，它将对象同时放置在屏幕的水平中心和垂直中心。`CenterHorAndTop` 与其他方法类似，只是它将对象定位在屏幕的水平中心和屏幕顶部。前面列出的最后一个方法 `CenterHorAndVert` 会将 `MmgObj` 实例同时水平和垂直居中。请记住这些方法；它们会一次又一次地派上用场。

```
public static MmgBmp GetBasicBmp(String src) { ... }
public static MmgSound GetBasicSound(String src) { ... }
public static MmgMenuItem GetBasicMenuItem(MmgEventHandler handler, String name, int eventId, int eventType, MmgBmp img) { ... }
Listing 3-8
MmgHelper Static Class Members 6
```

接下来要介绍的一组辅助方法有点分类，但主要与资源加载有关。这些辅助方法以方便、封装的方式提供高级代码。你应该始终使用游戏引擎的资源加载系统，并且应该始终使用将数据存储在资源缓存中的资源加载方法。

第一个辅助方法 `GetBasicBmp` 是一个图像资源加载方法，它接受一个文件路径字符串作为参数。此方法在资源加载完成前显示的游戏屏幕上非常有用，因为你可以直接指向文件系统上的图像资源。

该组中的下一个方法是一个辅助方法，用于 API 内置的菜单系统。`GetBasicMenuItem` 方法是一个便捷方法，可以快速、统一地准备一个新的 `MmgMenuItem` 实例。下一个要审查的方法是用于加载声音资源的 `GetBasicSound`。

此方法类似于我们刚刚审查过的 `GetBasicBmp` 方法。两者都是基本的资源加载方法。请务必仔细阅读并理解这些资源加载方法的工作原理。有几种不同的方法可以使用；因此，请确保你理解哪些方法使用资源缓存，哪些方法会缩放加载的图像，以及它们需要哪些不同的参数。

```
public static MmgBmp GetBasicCachedBmp(String path, String imgId) { ... }
public static MmgBmp GetBasicCachedBmp(byte[] data, String imgId) { ... }
public static MmgBmp GetBasicCachedBmp(String imgId) { ... }
public static MmgSound GetBasicCachedSound(String path, String sndId) { ... }
public static MmgSound GetBasicCachedSound(byte[] data, String sndId) { ... }
public static MmgSound GetBasicCachedSound(String sndId) { ... }
public static MmgBmp GetImageCacheBmp(Image b) { ... }
public static int ScaleValue(int val) { ... }
public static float ScaleValue(float val) { ... }
public static float ScaleValue(double val) { ... }
Listing 3-9
MmgHelper Static Class Members 7
```

接下来我们将审查的一组静态辅助方法主要涉及从图像或声音资源缓存中加载资源。列表中的第一个方法 `GetBasicCachedBmp` 接受一个字符串参数（图像资源的路径）和一个字符串参数（表示关联图像数据的键）。如果 `imgId` 存在，则从缓存中检索与该条目关联的图像并返回。

如果在图像缓存中找不到任何条目，则使用 `GetBasicBmp` 方法从文件中加载图像。生成的图像使用提供的 `imgId` 作为键添加到图像缓存中。请注意，如果图像缓存被关闭，则每次调用该方法时都会从文件加载图像。显然，启用缓存会使资源加载效率更高。



第二个 `GetBasicCachedBmp` 方法仅将图像键 `imgId` 作为参数。此版本的方法与第一个版本类似，区别在于它只能从图像缓存中加载图像。如果找到与给定键对应的图像，则将其检索并返回。

接下来的支持方法 `GetBasicCachedSound` 与它们的图像对应方法非常相似。这些方法实现了完全相同的功能，因此我不会详细讨论它们。请自行查看并确保理解代码以及这些方法如何与声音缓存协同工作。

我想提到的最后一个方法是 `GetImageCacheBmp` 方法。这是一个遗留方法，实际上并不与图像缓存交互。这个名字有点误导性，你不觉得吗？你还应注意，它不会进行任何缩放以匹配当前屏幕缩放比例。此方法仅将框架图像类转换为 MmgBase API 图像类 `MmgBmp`。所以要小心。我已经提醒过你这个方法的陷阱！

我应该提到还有一些定位方法。我们查看了用于居中对象的一组方法，但也有用于将对象定位在屏幕左侧和右侧的方法。另一组微妙但至关重要的方法是前面列出的缩放值方法。这些方法用于缩放数值以匹配屏幕的缩放比例（如果有）。任何时候你使用字面距离或尺寸值时，都应使用这些方法。

```
public static boolean RectCollision(int x, int y, MmgRect r)
public static boolean RectCollision(int r1x, int r1y, int w, int h, MmgRect r)
public static boolean RectCollision(MmgRect src, MmgRect dest)
public static boolean RectCollision(MmgObj src, MmgObj dest)
public static boolean RectCollision(MmgVector2 src, int sW, int sH, MmgVector2 dest, int dW, int dH)
public static boolean RectCollision(int r1x, int r1y, int r1w, int r1h, int r2x, int r2y, int r2w, int r2h)
清单 3-10
MmgHelper 静态类成员 8
```

就在你以为没完没了的时候，我们来到了最后一组要回顾的辅助方法。如你所见，`MmgHelper` 类非常强大。它几乎涵盖了所有场景——从日志记录到定位、碰撞检测和资源加载。最后一组要回顾的方法都是碰撞检测方法。这些方法旨在检测矩形和点之间的碰撞，并接受不同类型的数据作为参数。

它们都是简单的方法。我在这里不会过多赘述。在每种情况下，如果方法判定比较的对象之间存在重叠，则返回 `true`。请务必熟悉整套碰撞检测方法；在 2D 游戏开发中，它们经常派上用场。至此，`MmgHelper` 类的回顾就结束了。我们没有涵盖每一个方法，但我试图让你充分了解该类提供的一些关键特性和功能。

### 演示：MmgHelper 类

`MmgHelper` 类包含大量强大且便捷的功能。要为我们回顾的每组方法编写示例，需要花费大量时间。此示例来自 MmgTestSpace 包（如果你使用 C# 则称为命名空间）的 `ScreenTestMmgColor` 类。

```
1 title.SetY(title.GetY() + MmgHelper.ScaleValue(30));
2 AddObj(title);

4 int yDiff = MmgHelper.ScaleValue(40);
5 int yStrt = GetY() + MmgHelper.ScaleValue(140);
清单 3-11
MmgHelper 类演示 1
```

第一段演示代码展示了可能是最重要的方法之一 `ScaleValue` 的使用。此示例展示了如何使用 `ScaleValue` 方法来包装用于定位可绘制对象的常量值。第 4 行和第 5 行所示的第二种用例是在实例化用于定位、缩放和调整对象大小的变量时使用该方法。

```
1 title = MmgFontData.CreateDefaultBoldMmgFontLg();
2 title.SetText("");
3 MmgHelper.CenterHorAndTop(title);
4 title.SetY(title.GetY() + MmgHelper.ScaleValue(30));
清单 3-12
MmgHelper 类演示 2
```

在下一段演示代码中，我们可以看到如何使用 `MmgHelper` 类的定位方法。在此示例中，`title` 在水平方向上居中并放置在游戏屏幕的顶部。这里的一个微妙之处在于调用 `CenterHorAndTop` 方法后如何调整位置。后续代码行通过将 `title` 向下移动几个像素来略微调整定位。

这是一个非常重要的类，具有许多功能和特性。请务必花时间回顾并理解我们未在此处涵盖的类方法。

## 辅助类：MmgScreenData

`MmgScreenData` 类旨在保存有关游戏显示上下文的数据。例如，此类保存有关所需游戏面板和窗口尺寸的重要数据。此类是 MmgBase API 的一个关键特性，并在许多需要考虑屏幕尺寸的地方使用。



### 静态类成员

我们将要审查的 `MmgScreenData` 静态类成员，首先是一组描述游戏尺寸的类字段。

```
public static int DEFAULT_WIDTH = 1024;
public static int DEFAULT_HEIGHT = 768;
private static int gameWidth;
private static int gameHeight;
private static int gameLeft;
private static int gameTop;
private static int screenWidth;
private static int screenHeight;
清单 3-13
MmgScreenData 静态类成员 1
```

上面列出的前两个静态类字段是游戏面板和屏幕的宽度与高度的默认值。接下来的两个类字段保存了关于游戏期望尺寸的数据，即 `gameWidth` 和 `gameHeight`。`gameLeft` 和 `gameTop` 这两个静态类字段起初可能看起来有点奇怪。

通常你会期望它们都为零，但在我们的案例中，游戏引擎能够显示一个包含帧率信息和一些变量调试的开发标题栏。这会将游戏面板向下推，因此我们需要能够设置面板的偏移量。这组字段中的最后两个是 `screenWidth` 和 `screenHeight` 条目。

请注意，游戏屏幕只有尺寸数据，而屏幕的可渲染区域（即游戏面板）则拥有尺寸和位置数据。你认为这是为什么？这是因为屏幕的可渲染区域可能比窗口本身要小，因此游戏面板将在偏移量 `gameLeft` 和 `gameTop` 处进行渲染。

```
private static double scaleX;
private static double scaleY;
private static boolean scaleXOn;
private static boolean scaleYOn;
private static MmgVector2 scaleVec = MmgVector2.GetUnitVec();
private static MmgVector2 posVec;
清单 3-14
MmgScreenData 静态类成员 2
```

下一组静态类字段以两个缩放字段开始。`scaleX` 和 `scaleY` 字段保存了将游戏面板呈现到游戏窗口尺寸内所需的缩放数据。例如，如果期望的游戏尺寸大于游戏窗口，游戏尺寸将被缩小以适应并居中显示在游戏窗口内。`scaleX` 和 `scaleY` 静态类字段将保存实现此目的所需的缩放值。

`scaleXOn` 和 `scaleYOn` 静态类字段是布尔标志，指示是否需要在 X 轴和/或 Y 轴上进行缩放。最后，`scaleVec` 和 `posVec` 静态类字段是便捷字段，用于保存 X 和 Y 坐标的缩放和位置数据。

```
private static int origGameWidth;
private static int origGameHeight;
GRAPHICS_CONFIG = GraphicsEnvironment.getLocalGraphicsEnvironment().getDefaultScreenDevice().getDefaultConfiguration();
public static ScalingMode scalingMode = ScalingMode.AXIS_X_AND_Y;
清单 3-15
MmgScreenData 静态类成员 3
```

最后一组静态类字段如上所示。前两个条目是跟踪最初指定的游戏尺寸的字段。如果游戏需要缩放以适应游戏窗口，那么这些字段将保存原始的游戏尺寸。`GRAPHICS_CONFIG` 静态类字段是一个重要的字段；它保存了当前图形上下文的信息。

换句话说，它保存了当前图形能力的信息，比如屏幕尺寸、每个像素的位深度等等。上面字段列表中的最后一个条目是 `scalingMode` 类字段。此字段用于跟踪用于调整游戏面板的缩放类型。接下来，我们将看看 `MmgScreenData` 类的一些静态方法。

```
private static void CalculateLeft() { ... }
private static void CalculateTop() { ... }
public static void CalculateScaleAndOffset() { ... }
private static void CalculateScaleX(boolean agg) { ... }
private static void CalculateScaleY(boolean agg) { ... }
清单 3-16
MmgScreenData 静态类成员 4
```

我们要审查的前两个静态方法 `CalculateLeft` 和 `CalculateTop` 各只有一行代码，它们作为游戏配置过程的一部分，用于设置 `gameLeft` 和 `gameTop` 类字段。下一个方法 `CalculateScaleAndOffset` 也是配置过程的一部分，用于准备 X 轴和 Y 轴上的任何缩放。如果不需要缩放，我们将 `scaleX` 和 `scaleY` 类字段设置为 1.0 或 100%。这意味着不需要进行缩放。

如果确实需要进行一些缩放，我们必须决定是支持 X 轴缩放、Y 轴缩放，还是两者都支持。在我们的案例中，该方法被锁定为同时使用 X 轴和 Y 轴。这会导致首先调用 `CalculateScaleX` 方法，然后在必要时调用 `CalculateScaleY` 方法。接下来要审查的两个方法 `CalculateScaleX` 和 `CalculateScaleY`，用于检测正确调整游戏面板大小所需的缩放值。

这些方法执行一系列循环迭代。在每次迭代中，都会执行一次新的缩放测试，并检查缩放结果是否尽可能接近整数。它通过微小的增量来调整缩放量来实现这一点。因为我们不知道是否能够找到合适的缩放值，所以我们需要另一种方式来跳出 while 循环。在这种情况下，如果循环迭代次数达到 panic 变量的值（设置为 5000），循环就会退出。

循环完成后，`prctDiffX` 变量用于设置多个静态类字段。我们在这里尝试进行的缩放是均匀的，因此即使我们只处理 X 轴，最终也会缩放 X 轴和 Y 轴。`scaleX` 和 `scaleY` 都被设置为新确定的缩放值。

如果在水平缩放之后，垂直尺寸仍然有问题，那么我们会调用 `CalculateScaleY` 方法来确定现在要使用的最佳缩放值，该方法也会考虑当前的缩放结果。`CalculateScaleX` 和 `CalculateScaleY` 方法非常相似，因此我们在此不再赘述。请确保你阅读并理解了这些方法。这里的目标不是拥有一个健壮的游戏屏幕缩放实现；而只是提供一种简单直接的方法，将游戏面板适配到游戏窗口的尺寸内。

请注意，这个类旨在集中保存关于游戏面板和窗口尺寸的数据，以及关于屏幕当前缩放的信息。如果你还记得，我们在 `MmgHelper` 类中遇到了图像缩放调用，这些调用使用了这里确定的缩放值。这是一个安全的操作，因为默认期望是缩放是均匀的。while 循环只是试图确定一个能产生接近整数值的缩放值，以便图像缩放平滑。

我们需要审查的最后一组方法是 `GetGameRight` 和 `GetGameBottom` 方法。这些方法用于根据游戏面板的尺寸和当前偏移量来计算其右侧和底部的位置。请花点时间审查我们在这里没有机会涵盖的任何方法。

### 枚举

`MmgScreenData` 类有一个相关的枚举需要我们审查。`ScalingMode` 枚举用于描述由 `MmgScreenData` 类确定为必要的缩放类型。此枚举中的一个值用于设置 `scalingMode` 类字段。



### 主要方法详情

此特定类的主要方法详情由构造函数组成。

```
public MmgScreenData() { ... }
public MmgScreenData(int w, int h) { ... }
public MmgScreenData(int ScreenWidth, int ScreenHeight, int GameWidth, int GameHeight) { ... }
代码清单 3-17
MmgScreenData 主要方法详情 1
```

我们要看的第一个构造函数不接收任何参数，用于准备类的静态字段。由于 `MmgScreenData` 类主要是一个静态辅助类，它主要公开静态方法和字段，用于保存有关游戏尺寸、位置和缩放的数据。请注意，每个构造函数重载都试图在可用时将静态类字段设置为提供的参数值。如果不可用，则使用默认值代替计算值。

使用此类的正确方法是将游戏面板和窗口尺寸提供给重载的构造函数。这将导致设置静态类字段并调用 `CalculateScaleAndOffset` 静态类方法。请注意，列出的其他构造函数不会调用缩放计算方法，因为使用的是默认值。

### 演示：MmgScreenData 类

`MmgScreenData` 类是一个集中式辅助类，因此它被其他类大量使用。

```
screenData = new MmgScreenData(winWidth, winHeight, GamePanel.GAME_WIDTH, GamePanel.GAME_HEIGHT);
代码清单 3-18
MmgScreenData 类演示 1

```

上面的代码行来自 MmgCore 包的 `GamePanel` 类。这行代码对游戏引擎非常重要，并演示了如何准备 `MmgScreenData` 类。一旦类被实例化并正确配置，所有静态类字段和方法都应该能正常工作。

## 辅助类：MmgFontData

`MmgFontData` 类旨在保存关于字体、样式和大小的数据。其主要重点是提供对配置了不同样式和大小的 `MmgFont` 实例的便捷访问。`MmgFontData` 类在一定程度上遵循了 `MmgScreenData` 类的设计和实现，即尽管该类被用作静态辅助类，但在使用前仍需要准备。然而，一旦类被正确初始化，它就能提供对各种不同 `MmgFont` 实例的访问。

### 静态类成员

`MmgFontData` 类有许多静态类成员。我们将从下面列出的静态类字段开始这部分类的审查。

```
public static String DEFAULT_FONT_FAMILY = Font.SERIF;
public static int DEFAULT_FONT_TYPE = Font.PLAIN;
private static int fontSize = 18;
private static int targetPixelHeight = 22;
private static int targetPixelHeightScaled = 22;
代码清单 3-19
MmgFontData 静态类成员 1
```

上面列出的第一组静态类字段为一些重要的字体度量准备了默认值。列出的前两个条目设置了字体系列和类型的默认值。接下来的三个字段用于存储字体大小信息。它们被初始化为定义合理、正常大小字体的值。

`fontSize` 字段默认为 18，`targetPixelHeight` 和 `targetPixelHeightScaled` 类字段默认为 22。这两个字段将用于将字体高度（以像素为单位）与来自 `MmgScreenData` 类的信息同步。这样做是为了让字体大小随屏幕自动缩放。

第二组静态类字段用于设置框架字体类和 `MmgFont` 类的默认实例。还有一些静态类字段保存了普通、粗体和斜体字体样式的 `MmgFont` 类的默认实例。

这些类实例基于框架字体实例 `fontNorm`、`fontBold` 和 `fontItalic`。有许多方法用于处理框架字体和 `MmgFont` 对象，特别是用于创建不同字体大小和样式的方法。

```
public static Font CreateDefaultBoldFont(int sz) { ... }
public static Font CreateDefaultBoldFontLg() { ... }
public static Font CreateDefaultBoldFontSm() { ... }
代码清单 3-20
MmgFontData 静态类成员 2
```

上面列出的方法集用于实例化特定类型和大小的新框架字体对象。我在这里只列出了其中的几个。这些方法具有类似于 `CreateDefaultBoldFont` 的名称，并返回一个框架 `Font` 实例。请注意，所有接受大小参数的方法都会检查以确保字体大小不超过允许的最大字体大小 50。

这些方法旨在使用默认字体系列以及指定的字体大小和样式或默认字体大小和样式。请记住，它们用于创建框架字体对象。接下来，我们将看到如何创建 `MmgFont` 对象。

```
public static MmgFont CreateDefaultBoldMmgFont(int sz) { ... }
public static MmgFont CreateDefaultBoldMmgFontLg() { ... }
public static MmgFont CreateDefaultBoldMmgFontSm() { ... }
public static void CalculateScale() { ... }
代码清单 3-21
MmgFontData 静态类成员 3
```

下一组静态方法应该会让人立刻感到熟悉。在我们审查过的上一组方法中，对于每个返回框架字体对象的方法，都有一个对应的方法返回一个新的 `MmgFont` 对象。如您所见，设置字体的方式非常灵活。我建议在您掌握要领之前，先坚持使用默认值。其余的类方法是我们之前审查过的静态类字段的 get 和 set 方法。我在这里不会列出它们，但请花点时间自己审查一下，并准确理解它们的工作原理。

我们将在本节中审查的最后一个方法是 `CalculateScale` 方法。此方法与 `MmgScreenData` 类中的缩放计算方法非常相似。最终，我们将测量 `MmgFont` 实例的像素高度，并调整其大小，直到字体的高度与缩放后的目标像素高度匹配。如果字体高度有偏差，字体点大小将调整 1，直到找到合适的字体大小或达到最大迭代次数。

所有默认字体静态字段都会更新以反映新的字体大小。通过这种方式，游戏引擎可以通过 `MmgScreenData` 类同步屏幕的缩放，通过 `MmgFontData` 类同步字体，并通过使用 `MmgHelper` 类的加载方法同步图像资源。

### 主要方法详情

对于此类，我们唯一需要审查的主要方法是类构造函数。与 `MmgScreenData` 类非常相似，`MmgFontData` 类在能够配置自身并可供 API 使用之前，需要先进行实例化。更重要的是，`MmgFontData` 类的配置必须在 `MmgScreenData` 类之后进行，因为它依赖于由 `MmgScreenData` 类计算的缩放值。

类构造函数是调用静态配置方法的一个通道。您不必以这种方式使用该类。您可以直接调用静态方法，但我倾向于使用构造函数，并在代码的高层保留对 `MmgFontData` 类的引用。



### 演示：MmgFontData 类

在演示 `MmgFontData` 类时，我们将了解该类在 MmgCore API 中作为游戏启动代码的一部分是如何初始化的。

```
01 screenData = new MmgScreenData(winWidth, winHeight, GamePanel.GAME_WIDTH, GamePanel.GAME_HEIGHT);
02 MmgHelper.wr("");
03 MmgHelper.wr("--- MmgScreenData ---");
04 MmgHelper.wr(MmgScreenData.ApiToString());

06 fontData = new MmgFontData();
07 MmgHelper.wr("");
08 MmgHelper.wr("--- MmgFontData ---");
09 MmgHelper.wr(MmgFontData.ApiToString());
10 debugFont = MmgFontData.CreateDefaultFontSm();
11 mmgDebugFont = new MmgFont(debugFont, "Test", 0, 0, MmgColor.GetWhite());
代码清单 3-22
MmgFontData 类演示 1
```

上面列出的代码片段来自 MmgCore API 的 `GamePanel` 类，具体来说是类的构造函数。如第 1 行所示，`MmgScreenData` 类通过从静态主入口点传入的游戏尺寸进行初始化。请注意，我们只需初始化该类即可完成准备工作。

在第 6 行，`MmgFontData` 类被初始化；正如我们刚才提到的，类的构造函数会调用缩放计算方法，并预先准备好所有字体大小。在第 9 行，显示了关于当前字体度量的调试信息。在构建游戏时，请花点时间查看这些调试行。它们确实能帮助你追踪游戏尺寸和字体大小方面的小问题。第 10 行和第 11 行展示了如何使用该类创建新字体的一个示例。

## 辅助类：MmgDebug

`MmgDebug` 类与 `MmgApiUtils` 类类似，都是一个提供日志记录功能的便捷访问类。同样，我们在 `MmgApiUtils` 和 `MmgHelper` 类中也见过类似的功能。那么，为什么我们还需要另一个日志记录辅助类呢？嗯，在这种情况下，拥有多个日志记录类并非最糟糕的事情；它甚至还有点用处。这里的想法是，你可以使用不同的日志记录类，独立地为游戏的不同部分提供输出。

### 静态类成员

`MmgDebug` 类只有两个静态字段需要我们查看。

```
public static boolean DEBUGGING_ON = true;
public static String appName = "MmgApi.MmgDebug";
代码清单 3-23
MmgDebug 静态类成员 1
```

上面列出的第一个条目 `DEBUGGING_ON` 用于控制此类生成的日志记录。与其他日志记录类类似，你可以通过将此字段设置为 false 来关闭所有生成的日志记录。列出的第二个条目是一个日志前缀。使用它可以在生成的每条日志条目之前插入一个字符串。接下来，我们将查看一些静态类方法。

```
public static void wr(String s)
public static void wr(String key, String s)
public static void wrTs(String s)
代码清单 3-24
MmgDebug 静态类成员 2
```

上面列出了三个我们需要介绍的简单日志记录方法。它们与我们之前查看的日志记录方法略有不同，这些方法会在日志前添加一个键前缀。第一个方法 `wr` 使用默认前缀文本，而重载版本的方法则同时接受一个前缀字符串和要记录的文本。最后，`wrTs` 方法会在记录的文本中包含一个时间戳。这在追踪与计时和动画相关的错误时非常有用。

## 辅助类：MmgBmpScaler

`MmgBmpScaler` 类是另一个静态辅助类，其结构与我们已经看过的一些辅助类类似。该类的主要用途是通过调整大小或旋转来变换图像。该类的所有功能都可以通过静态方法访问，无需创建类实例。

### 静态类成员

`MmgBmpScaler` 类有几个静态类方法，我们接下来将查看它们。

```
01 public static MmgBmp ScaleMmgBmpToGameScreen(MmgBmp subj, boolean alpha) { ... }
public static MmgBmp ScaleMmgBmpToGameScreen(MmgBmp subj, boolean alpha) { ... }
public static MmgBmp ScaleMmgBmp(MmgBmp subj, MmgVector2 newSize, boolean alpha) { ... }
public static MmgBmp ScaleMmgBmp(MmgBmp subj, boolean useScreenDataScaleX, boolean alpha) { ... }
public static MmgBmp ScaleMmgBmp(MmgBmp subj, double scale, boolean alpha) { ... }
public static MmgBmp RotateMmgBmp(MmgBmp subj, int angle, boolean alpha) { ... }
代码清单 3-25
MmgBmpScaler 静态类成员 1
```

我们将首先查看的一组方法包含 `MmgBmp` 缩放方法。上面列出的第一个方法 `ScaleMmgBmpToGameScreen` 旨在将 `MmgBmp` 图像缩放到与屏幕缩放比例匹配（如果有的话）。下一个条目是 `ScaleMmgBmp` 方法。它接受一个 `MmgBmp`、一个 `MmgVector2` 和一个布尔值作为参数。此方法与我们之前查看的方法几乎相同。唯一的区别在于新图像的尺寸由传入的 `MmgVector2` 实例决定。

还有另外两个版本的 `ScaleMmgBmp` 方法。这些版本为你提供了不同的方式来定义图像的缩放方式。在该方法的一个重载版本中，你可以指定 X 轴或 Y 轴作为缩放百分比的来源。而在另一个方法条目中，你可以按设定的缩放量来缩放指定的图像。这个类在调整图像大小方面为你提供了很大的灵活性。让我们看看该类中最后一个方法，它可用于图像旋转变换。

`MmgBmpScaler` 类中留给我们的最后一个方法与图像缩放无关。相反，我们有另一种类型的图像变换方法：旋转。`RotateMmgBmp` 静态类方法的设置与我们刚刚查看的缩放方法类似，但旨在提供图像旋转功能。

### 演示：MmgBmpScaler 类

下面列出的演示代码可以在 MmgTestSpace 包（如果你使用 C# 进行学习，则为命名空间）的 `ScreenTestMmgBmp` 类的 `LoadResources` 方法中找到。

```
1 bmpScaled = MmgBmpScaler.ScaleMmgBmp(bmpCache, 1.50, true);
2 bmpScaled.SetPosition(MmgHelper.ScaleValue(213), GetY() + MmgHelper.ScaleValue(330));
3 AddObj(bmpScaled);

5 bmpRotate = MmgBmpScaler.RotateMmgBmp(bmpCache, 90, true);
6 bmpRotate.SetPosition(MmgHelper.ScaleValue(645), GetY() + MmgHelper.ScaleValue(330));
7 AddObj(bmpRotate);
代码清单 3-26
MmgBmpScaler 类演示 1
```

上面列出的示例代码使用我们刚刚查看的方法，创建了一个 `MmgBmp` 对象的缩放版本和旋转版本。代码中的第一个条目（第 1-3 行）用于创建现有图像的一个新版本，该版本缩放至 150%。第二个代码块（第 5-7 行）是使用 `MmgBmpScaler` 类将 `MmgBmp` 对象旋转 90 度的示例。

至此，对 `MmgBmpScaler` 类的介绍就结束了。请确保你已完整阅读并理解代码，因为它是一个有用的辅助类，肯定会在需要时派上用场。

## 辅助类：MmgMediaTracker

`MmgMediaTracker` 类，你猜对了，是另一个辅助类，它采用我们之前见过的同样便捷的静态方法风格实现。如果你注意到了，辅助类使用静态方法和字段，是因为这创建了功能的集中化，几乎无需任何准备即可访问。

例如，当你将日志控制布尔值设置为 false 时，它会关闭所有使用这些日志记录方法的地方的日志记录。`MmgMediaTracker` 类是一个用于存储和检索图像及声音资源的集中式存储库。

`MmgMediaTracker` 类使用静态方法和字段实现。它包含在一个中心位置管理图像和声音资源的方法。游戏引擎的资源加载代码通过我们之前查看过的 `MmgHelper` 类的一些静态方法大量使用该类。



### 静态类成员

我们要查看的第一组静态类成员由三个静态类字段组成。

```
public static Hashtable cacheBmp = new Hashtable();
public static Hashtable cacheSound = new Hashtable();
public static boolean REMOVE_EXISTING = true;
代码清单 3-27
MmgMediaTracker 静态类成员 1
```

前面列出的前两个条目是以键值对形式存储数据的数据结构。如果你使用的是 C# 版本的代码，你会注意到这种数据结构的框架类是 `Dictionary` 类。在 Java 实现的游戏引擎中，这种数据的框架类是 `Hashtable`。

这两个数据结构充当资源缓存，能够将图像数据存储在 `cacheBmp` 字段中，将声音数据存储在 `cacheSound` 字段中。回想一下，`MmgHelper` 类有许多方法与 `MmgMediaTracker` 类的缓存进行交互。这是一个 API 类如何被设计为协同工作，同时又能独立提供功能的例子。`REMOVE_EXISTING` 布尔标志用于指示在向资源缓存添加内容之前，是否应该清理该缓存。

```
public static void CacheImage(String key, Image val) { ... }
public static void CacheSound(String key, Clip val) { ... }
public static boolean HasBmpKey(String key) { ... }
public static boolean HasSoundKey(String key) { ... }
public static boolean HasBmpValue(Image img) { ... }
public static boolean HasSoundValue(Clip snd) { ... }
public static boolean RemoveBmpByKey(String key) { ... }
public static boolean RemoveBmpByKeyValue(String key, Image img) { ... }
public static boolean RemoveSoundByKey(String key) { ... }
public static boolean RemoveSoundByKeyValue(String key, Clip snd) { ... }
代码清单 3-28
MmgMediaTracker 静态类成员 2
```

我们要查看的第一组方法允许你将图像和声音存储在独立的缓存中，查询缓存的大小，以及从任一缓存中检索资源。请注意，在将缓存条目添加到缓存之前，是否删除该条目是由 `CacheImage` 和 `CacheSound` 方法中的 `REMOVE_EXISTING` 静态类字段控制的。

接下来要查看的一组方法是简单的实用方法，用于检查某个键是否存在于图像或声音缓存中。你还可以检查某个值是否存在于任一缓存中。这些方法在与 `MmgMediaTracker` 的缓存交互时非常有用。请务必阅读并理解这些方法的使用方式。

前面列出的最后一组静态类方法是用于图像和声音资源的缓存移除方法。你可以通过键，或者通过键和值来移除条目。这完善了可用的方法，使你能够轻松地添加、移除和查询缓存的资源。这几乎就是管理资源缓存所需的一切！让我们来看一些示例代码。

### 演示：MmgMediaTracker 类

下面列出的演示代码片段是来自 `MmgHelper` 类的 `GetBasicCachedSound` 方法。

```
01 public static MmgSound GetBasicCachedSound(String path, String sndId) {
02     MmgSound lval = null;
03     if (SND_CACHE_ON == true) {
04         if (MmgMediaTracker.HasSoundKey(sndId) == true) {
05             lval = new MmgSound(MmgMediaTracker.GetSoundValue(sndId));
06         } else {
07             lval = MmgHelper.GetBasicSound(path);
08             MmgMediaTracker.CacheSound(sndId, lval.GetSound());
09         }
10     } else {
11         lval = MmgHelper.GetBasicSound(path);
12     }
13     return lval;
14 }
代码清单 3-29
MmgMediaTracker 类演示 1
```

`MmgMediaTracker` 类被 MmgCore 库中包含的自动加载功能所使用。其用途的一个例子体现在 `GetBasicCachedSound` 方法中。请注意，连接静态辅助类来创建更有用的方法是多么容易。我们实际上已经在代码中创建了一个用于处理缓存的接口。你能想到我们通过不同类定义的其他接口吗？嗯，一个简单的日志记录接口可能是一个答案。我让你思考一下这个问题，看看你是否能想出更多。

## 章节总结

在本章中，我们完成了对 MmgBase API 辅助类的回顾。如果你停下来思考一下我们在本章中回顾的代码，最突出的一点应该是那些通过静态方法提供高级、封装功能的便捷访问类。

我们在这里回顾的类与我们在第 1 章中介绍的基础类非常不同。这些类在基础意义上并不直接属于游戏引擎的一部分。然而，它们是非常高级、易于使用的类，有助于处理游戏开发中所需的许多任务。例如，正如我们所介绍的，API 现在使用 MmgCore 应用程序级代码以及 `MmgMediaTracker` 和 `MmgHelper` 类，完全集成了资源加载和检索功能。

我想从我们看过的代码中退一步，着手构建更宏观的图景。我们正在努力定义支持通用 2D 游戏引擎所需的类。在此过程中，我们涵盖了以下类：

*   **MmgApiUtils**：一个辅助类，提供带有集中式日志控制功能的日志记录支持。
*   **MmgHelper**：一个非常重要的支持类，提供静态方法，用于定位对象、日志记录、加载资源、读取/写入类配置文件、创建可绘制的图像表面、矩形碰撞以及数值缩放。这个列表说明了一切。
*   **MmgScreenData**：一个辅助类，提供关于游戏面板和窗口尺寸的信息。支持缩放游戏面板以适应游戏屏幕的尺寸。
*   **MmgFontData**：一个辅助类，提供支持以缩放字体来匹配屏幕的缩放比例（如果有的话）。它还有许多静态方法，用于访问不同的预配置字体。至少可以说，这个类非常有用。
*   **MmgDebug**：一个辅助类，提供与 MmgApiUtils 日志记录相关联的日志记录支持，从而创建一个具有独立日志控制的双层日志系统。
*   **MmgBmpScaler**：一个辅助类，提供缩放和旋转图像的支持。
*   **MmgMediaTracker**：游戏引擎中一个非常重要的部分，负责缓存游戏资源。

我们看到我们的工具库规模显著增长。我们现在有了一组类，它们成倍地增强了第 1 章中概述的游戏引擎基础类所定义的能力。例如，我们有了一个资源管理系统的基础，游戏中的任何类都可以轻松使用它。我们有辅助类来为我们加载资源、定位对象，以及更多、更多的功能。

你应该注意的另一件事是，我们仍然缺少一个整体结构。我提醒你要有耐心。直到第二部分我们回顾 MmgCore 库时，我们才能看到全貌。主要的收获应该是，我们现在有了一套很好的工具来制作游戏。在接下来的章节中，我们将定义更强大的工具，这些工具以不同的方式使用多个 API 类来解决诸如精灵加载和动画之类的问题。

