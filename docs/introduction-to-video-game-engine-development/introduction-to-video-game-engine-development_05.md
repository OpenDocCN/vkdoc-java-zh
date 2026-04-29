# 4. 其他类

在本章中，你将回顾 MmgBase API 的“其他”类。这些类不太适合归入典型的类别，所以我们为它们单独设立了一章。在本次回顾中，我们将查看以下类：

*   MmgCfgFileEntry
*   MmgEvent
*   MmgEventHandler



## 其他类：MmgCfgFileEntry

`MmgCfgFileEntry` 类用于在读写配置文件时保存数据。它是一个小类，但我认为回顾它很重要，因为在设置游戏屏幕时，类配置自动化是一个非常实用的功能。`MmgCfgFileEntry` 类旨在以键值对的形式保存从特殊文本文件（即类配置文件）中加载的数值或字符串数据。

### 枚举

有一个与 `MmgCfgFileEntry` 类关联的枚举，即 `CfgEntryType` 枚举。它包含三个不同的值：`TYPE_DOUBLE`、`TYPE_STRING` 和 `NONE`。前两个值用于指示配置条目中存储的数据类型。`NONE` 值用于指示未初始化的状态。

### 类字段

`MmgCfgFileEntry` 有几个描述性字段，我已在下面列出。

```
public CfgEntryType cfgType = CfgEntryType.NONE;
public Double number;
public String str;
public String name;
代码清单 4-1
MmgCfgFileEntry 类字段 1
```

第一个条目是我们刚刚回顾的 `CfgEntryType` 枚举的一个实例。它用于跟踪此对象中存储的数据类型。接下来的两个类字段 `number` 和 `str` 用于保存从类配置文件中读取的实际数据。最后一个字段是 `name` 字段。它用于存储与类配置文件中数据行关联的键字符串。

### 支持方法详情

由于该类性质简单，没有任何 get 和 set 支持方法。因此我们只需要回顾两个方法。

```
public String ApiToString() { ... }
public int compare(MmgCfgFileEntry o1, MmgCfgFileEntry o2) { ... }
代码清单 4-2
MmgCfgFileEntry 支持方法详情 1
```

我们要回顾的两个条目中的第一个是一个 API 转字符串方法。我们之前遇到过它，所以这里不再赘述。`compare` 方法是一个框架比较方法，用于在写入配置文件条目之前对其进行排序。

### 主要方法详情

`MmgCfgFileEntry` 类有一些标准的主要方法供我们回顾。

```
public MmgCfgFileEntry() { ... }
public MmgCfgFileEntry(MmgCfgFileEntry obj) { ... }
public MmgCfgFileEntry Clone() { ... }
public boolean ApiEquals(MmgCfgFileEntry obj) { ... }
代码清单 4-3
MmgCfgFileEntry 主要方法详情 1
```

第一个主要方法是一个简单的构造函数，不带任何参数。这对于快速实例化一个对象并在之后进行配置非常有用。第二个构造函数是一个专用构造函数，它接受一个 `MmgCfgFileEntry` 作为参数，并用它来创建一个新的、唯一的类实例。

`Clone` 方法用于创建该类的一个新的唯一副本。它使用专用构造函数来完成此操作。前面列表中的最后一个条目是 `ApiEquals` 方法，这是一个 API 级别的比较方法。这就是我想回顾的主要方法。让我们来看看这个类的实际应用！

### 演示：MmgCfgFileEntry 类

下面列出的示例代码片段展示了 `MmgCfgFileEntry` 类作为类配置实现的一部分的使用情况。

```
01 classConfig = MmgHelper.ReadClassConfigFile(GameSettings.CLASS_CONFIG_DIR + "screen_splash.txt");
...
02 key = "splashScreenDisplayTimeMs";
03 if(classConfig.containsKey(key)) {
04 super.SetDisplayTime(classConfig.get(key).number.intValue());
05 }

07 key = "bmpLogo";
08 if(classConfig.containsKey(key)) {
09     file = classConfig.get(key).str;
10 } else {
11     file = "logo_large.jpg";
12 }
代码清单 4-4
MmgCfgFileEntry 类演示 1
```

上面列出的代码片段来自 MmgTestSpace 库中 `SplashScreen` 类的 `LoadResources` 方法。第一行代码演示了加载一个类配置文件。在第 2-5 行，我们可以看到如何访问配置文件条目中的数据。请注意，我们事先知道给定键所期望的数据类型。在第 4 行，显示时间字段根据配置文件条目的数值进行设置。

接下来的示例代码块（第 7-12 行）演示了使用配置文件条目对象来检索字符串值。注意在第 9 行，通过键定位配置文件条目对象，并使用了 `str` 字段。至此，我想为这个类回顾的演示代码就结束了。

## 其他类：MmgEvent

`MmgEvent` 类及其对应类 `MmgEventHandler` 创建了一个通用的事件处理系统，您可以使用它来发送和接收事件。我们将在回顾完这个类之后介绍 `MmgEventHandler` 类。`MmgEvent` 类的实现很简单。唯一需要提及的是，有一些预设的事件 ID 用于处理菜单导航。

### 静态类成员

`MmgEvent` 类有许多类字段，它们是不同事件的默认静态值。这些预设值主要是为了与菜单系统一起使用而设计的，允许用户在菜单的不同选项之间移动。

```
public static int EVENT_ID_UP = 0;
public static int EVENT_ID_DOWN = 1;
public static int EVENT_ID_LEFT = 2;
public static int EVENT_ID_RIGHT = 3;
public static int EVENT_ID_ENTER = 4;
public static int EVENT_ID_SPACE = 5;
public static int EVENT_ID_BACK = 6;
public static int EVENT_ID_ESC = 7;
代码清单 4-5
MmgEvent 静态类成员 1
```

`MmgEvent` 类的静态类字段是预设的事件 ID，旨在表示键盘或游戏手柄输入。可以将输入映射到这些静态 ID 之一，表示方向键输入、回车或空格键输入，或者返回或退出键输入。

需要注意的是，事件 ID 实际上仅在事件和事件处理器的上下文中才重要。您可以选择使用预设 ID 来表示与键盘事件完全不同的内容。这完全取决于您。

### 类字段

下面列出的类字段定义了事件的载荷和事件的处理程序。`MmgEvent` 类以通用方式实现，允许您灵活地以任何方式使用它。

```
private MmgEventHandler parentHandler;
private String message;
private int id;
private int type;
private MmgEventHandler targetHandler;
private Object extra;
private MmgEvent prevEvent;
代码清单 4-6
MmgEvent 类字段 1
```

`MmgEvent` 类支持事件和事件处理器之间的不同关联。第一个字段可用于分配一个父事件处理器。您可以忽略它，或者用它来创建父子事件。下一个类字段是 `message` 字段。如果字符串数据类型有用，可以选择使用它来添加关于事件或事件载荷数据的信息。接下来的两个类字段 `id` 和 `type` 用于在事件处理器处理事件时识别和响应事件。对于给定的事件和事件处理器设置，`id` 和 `type` 的组合应该是唯一的。

`targetHandler` 字段是在事件触发时接收该事件的处理程序。`extra` 字段是一个事件载荷，可用于存储与事件关联的对象和数据。最后但同样重要的是 `prevEvent` 字段，它是 `MmgEvent` 类的一个实例。此字段可用于存储对先前事件的引用。您可以使用此功能来创建事件链。



### 支持方法详情

`MmgEvent` 类包含以下支持方法。

```
public MmgEvent GetPrevEvent() { ... }
public void SetPrevEvent(MmgEvent p) { ... }
public void SetParentEventHandler(MmgEventHandler e) { ... }
public MmgEventHandler GetParentEventHandler() { ... }
public void SetTargetEventHandler(MmgEventHandler e) { ... }
public MmgEventHandler GetTargetEventHandler() { ... }
public String GetMessage() { ... }
public void SetMessage(String s) { ... }
public int GetEventId() { ... }
public void SetEventId(int s) { ... }
public int GetEventType() { ... }
public void SetEventType(int s) { ... }
public Object GetExtra() { ... }
public void SetExtra(Object obj) { ... }
清单 4-7
MmgEvent 支持方法详情 1
```

上面列出的支持方法只是针对各个类字段的简单 get 和 set 方法。这里我不打算详细讲解，但请务必仔细查看并确保理解它们。

### 主要方法详情

`MmgEvent` 类有几个主要方法值得我们关注。

```
public MmgEvent(MmgEventHandler ParentHandler, String Msg, int Id, int Type, MmgEventHandler TargetHandler, Object Ex) { ... }
public void Fire() { ... }
public String ApiToString() { ... }
清单 4-8
MmgEvent 主要方法详情 1
```

上面列表中的第一个方法是类的构造函数。它接收该类每个相关字段的参数。请注意，如果你不打算使用某个字段，则无需为每个参数都提供对象。例如，如果你没有事件层级结构，则可以将 `ParentHandler` 参数设置为 null。

接下来我们要看的两个主要方法是 `Fire` 和 `ApiToString` 方法。当 `MmgEvent` 应该被触发时，会调用 `Fire` 方法。如果定义了 `targetHandler`，则会调用它，并将当前的 `MmgEvent` 对象作为参数传递。`ApiToString` 方法返回事件的字符串表示形式。

### 演示：MmgEvent 类

作为 `MmgEvent` 类的演示，我们将看看如何创建和触发一个事件。

```
1 private MmgEvent clickScreen = new MmgEvent(null, "vert_click_screen", MmgScrollVert.SCROLL_VERT_CLICK_EVENT_ID, MmgScrollVert.SCROLL_VERT_CLICK_EVENT_TYPE, null, null);
清单 4-9
MmgEvent 类演示 1
```

第一行示例代码向我们展示了如何实例化一个新的 `MmgEvent` 对象。这行代码来自 MmgBase 包（或 C# 中的命名空间）中 `MmgScrollVert` 类的字段部分。请注意，父处理器、目标处理器和先前事件字段都设置为 null。在此示例中，我们正在使用正确的 `id` 和 `type` 准备事件，但尚未指定事件处理器。在这种情况下，事件处理器会在稍后配置 `MmgScrollVert` 类时设置。

```
1 if(clickScreen != null) {
2     clickScreen.SetExtra(new MmgVector2(x, y));
3     clickScreen.Fire();
4 }
清单 4-10
MmgEvent 类演示 2
```

在第二个示例代码片段中，我们可以看到如何正确触发 `MmgScrollVert` 类的点击屏幕事件。作为对接收鼠标点击事件的响应，如果定义了 `clickScreen` 字段，它将被触发。请注意使用 `extra` 字段来存储事件相关信息。在此例中，我们随点击事件一起发送屏幕点击坐标，作为一个 `MmgVector2` 对象。

## 其他类：MmgEventHandler

`MmgEventHandler` 是一个接口，类可以实现它，以便能够处理特定的 `MmgEvent` 对象。`MmgEventHandler` 类被实现为接口，这样任何类都可以实现它并注册以处理事件。

### 类回顾

由于 `MmgEventHandler` 接口不具备常规类的所有特性，我们直接打印出代码并进行讨论。

```
1 public interface MmgEventHandler {
2     public void MmgHandleEvent(MmgEvent e);
3 }
清单 4-11
MmgEventHandler 类回顾 1
```

`MmgEventHandler` 接口定义了一个方法签名 `MmgHandleEvent`。当一个类实现该接口时，它必须定义 `MmgHandleEvent` 方法，从而能够接收已注册的事件。就是这么简单。

### 演示：MmgEventHandler 类

对于这个类的演示，我们将查看三段代码片段，它们展示了如何设置一个 `MmgEvent` 和处理器。

```
//MmgScrollVert
01 public void SetEventHandler(MmgEventHandler e) {
02     clickScreen.SetTargetEventHandler(e);
03     clickUp.SetTargetEventHandler(e);
04     clickDown.SetTargetEventHandler(e);
05 }
//ScreenTestMmgScrollVert.LoadResources
01 scrollVert = new MmgScrollVert(vPort, sPane, sBarColor, sBarSldrColor, sBarWidth, sBarSldrHeight, interval);
02 scrollVert.SetIsVisible(true);
03 scrollVert.SetWidth(sWidth + scrollVert.GetScrollBarWidth());
04 scrollVert.SetHeight(sHeight);
05 scrollVert.SetEventHandler(this);
//ScreenTestMmgScrollVert
01 public void MmgHandleEvent(MmgEvent e) {
02     if(e.GetEventId() == MmgScrollVert.SCROLL_VERT_CLICK_EVENT_ID || e.GetEventId() == MmgScrollHor.SCROLL_HOR_CLICK_EVENT_ID || e.GetEventId() == MmgScrollHorVert.SCROLL_BOTH_CLICK_EVENT_ID) {
03         MmgVector2 v2 = (MmgVector2)e.GetExtra();
04         event.SetText("Event: Id: " + e.GetEventId() + " Type: " + e.GetEventType() + " Pos: " + v2.ApiToString() + " Msg: " + e.GetMessage() + " " + System.currentTimeMillis());

06     } else {
07         event.SetText("Event: Id: " + e.GetEventId() + " Type: " + e.GetEventType() + " Msg: " + e.GetMessage() + " " + System.currentTimeMillis());

09     }

11     MmgHelper.CenterHor(event);
12 }
清单 4-12
MmgEventHandler 类演示 1
```

上面列出的第一个代码片段展示了 `MmgScrollVert` 类的 `SetEventHandler` 方法。此方法用于为该类支持的不同事件注册事件处理器。请注意，该类支持的每个事件都设置了其事件处理器，第 2-4 行。下一个代码片段来自 MmgTestSpace 库中 `ScreenTestMmgScrollVert` 类的 `LoadResources` 方法。在此代码块中，初始化了 `MmgScrollVert` 类的一个新实例。请注意，在第 5 行，滚动面板小部件的事件处理器被设置为游戏屏幕。

这就引出了最后一部分——来自游戏屏幕类 `ScreenTestMmgScrollVert` 的事件处理器方法。在第 2 行，我们通过检查接收到的对象的 `id` 和 `type` 字段来判断接收的事件是否是我们能够处理的事件。如果我们可以处理该事件，则从 `extra` 字段中强制转换出一个 `MmgVector2` 对象，第 3 行。在此例中，我们使用点击信息来更新一个 `MmgFont` 实例的文本。

## 章节总结

在本章中，我们完成了对“其他”类的回顾。我们介绍的这些类非常有用，总结如下：

*   MmgCfgFileEntry：一个对类配置文件中的一行数据进行建模的类。此类用于存储来自类配置文件行的数据，并用于设置游戏屏幕。

*   MmgEvent：一个通用的事件类，为 API 提供基本的事件支持。

*   MmgEventHandler：一个事件处理器接口。此接口用于设置事件处理器以接收已注册的事件。当与 MmgEvent 类结合使用时，这两个类构成了一个简单的事件系统。

这些类蕴含着微妙的力量。花点时间思考一下事件系统的配置。注意它是多么通用。就像我们为支持游戏引擎而构建的大多数功能一样，我们拥有一个通用的事件系统，你可以在下一个游戏中使用它。在处理事件时，请确保不要滥用它们。当一个类需要通知另一个类发生了某事时，考虑使用事件。将此作为实现事件时的指导原则。



