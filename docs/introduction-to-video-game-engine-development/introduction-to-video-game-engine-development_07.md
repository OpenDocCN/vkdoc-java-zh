# 11. 动态设置

到目前为止，我们已经介绍了相当多的游戏引擎代码。大多数类都与引擎的模型或运行时代码紧密相关。接下来我们将回顾的一组类展示了游戏引擎如何从不同来源提取数据以自定义设置和游戏玩法。

在接下来的章节中，我们将回顾游戏引擎配置文件的结构和语法，并详细介绍设置加载过程，以便你完全控制游戏如何加载和处理动态数据。我们将在本章中回顾的类如下：

*   DatConstantsEntry

*   GameSettingsImporter

*   DatExternalStrings

*   GameSettings



## 动态设置：DatConstantsEntry 类

游戏引擎能够从 XML 文件中加载值到特定类的静态字段中。你能想到我们为何要将此功能限制在静态类字段上吗？使用静态字段所需的初始化工作更少，因为被更新的字段可以通过静态方式直接访问，无需进行类初始化。

这使我们能够通过设置和配置文件，以数据驱动的方式配置游戏引擎和游戏本身。我们可以为一个游戏创建多个配置文件，也可以为多个游戏创建多个配置文件。这完全取决于你。`DatConstantsEntry` 类旨在保存游戏引擎配置文件中的单行数据。

引擎还允许你将配置文件指定为默认命令行参数之一。如果你在游戏启动命令的末尾添加文本 `ENGINE_CONFIG_FILE=` 后跟一个有效的配置文件，就可以在运行时动态设置引擎使用的配置文件。如果对游戏启动命令感到陌生，可以随时翻回第 10 章复习一下。

动态修改游戏设置的能力是一个非常强大的工具，但请务必小心。当你通过配置文件覆盖设置时，在查看甚至测试代码时可能并未意识到这一点。这可能会让你困惑，为什么你通过代码设置的值没有生效。请记得检查所有可能设置值的方式，包括配置文件。

### 类字段

`DatConstantsEntry` 类的字段用于描述要存储到目标静态字段中的值。让我们来看一下！

```
public String key;
public String val;
public String type;
public String from;
代码清单 11-1
DatConstantsEntry 类字段 1
```

`key` 字段不仅是存储数据条目的键，它还是存储数据的目标字段的确切名称。你必须确保字段类型与你要存储的数据类型相匹配。`val` 字段是目标字段将要存储的值的字符串表示形式。在设置目标字段值的过程中，该字符串会被相应地转换。

`type` 字段用于描述 `val` 字段中保存的数据类型。`type` 字段可以设置为 `int`、`float`、`double`、`short`、`string` 或 `bool`。再次强调，请确保目标字段与要存储的数据类型兼容。列表中的最后一个条目是 `from` 字段。此字段应设置为静态字段所属的目标类，该静态字段的名称存储在 `key` 字段中。

### 主要方法详情

该类的主要方法都是构造函数。共有三个构造函数，如下所列。在我们回顾它们之前，请务必先看一下。

```
public DatConstantsEntry(String k, String v) { ... }
public DatConstantsEntry(String k, String v, String t) { ... }
public DatConstantsEntry(String k, String v, String t, String f) { ... }
代码清单 11-2
DatConstantsEntry 主要方法详情 1
```

我按照复杂度递增的顺序排列了这些构造函数。需要说明的是，当没有提供参数值时，该类默认将 `type` 字段设置为 `int`，将 `from` 字段设置为 `GameSettings` 类。考虑到这一点，前两个构造函数只是初始化 `GameSettings` 配置文件条目的快捷方式。列出的最后一个构造函数可能是你最常用的。这个条目允许你直接通过构造函数参数来指定每个字段。

`DatConstantsEntry` 类的介绍到此结束。接下来，我们将详细审视这个类。通常我会在此处过渡到演示部分，但我们在配置文件导入过程中会再次遇到 `DatConstantsEntry` 类，因此我们将转而深入回顾配置文件的格式。

### 深入解析：DatConstantsEntry

首先，让我们回顾一下游戏引擎配置文件通常存储的位置。当然，你可以在游戏启动参数中指定这些文件的自定义路径。游戏引擎项目中包含了一些默认的游戏引擎配置文件供你参考。

游戏项目设置的常规结构应在项目根目录下包含一个名为 **cfg** 的配置文件夹。以下是一个展示 **cfg** 文件夹设置的简单示意图。

```
MmgGameApiJava
|-> cfg (资源和配置目录)
|-> class_config (类配置文件)
|-> playable (音频资源)
|-> auto_load (全局音频资源)
|-> sound1.wav
|-> MmgTestSpace (项目特定音频资源)
|-> jump2.wav
|-> drawable (图像资源)
|-> auto_load (全局图像资源)
|-> a_btn.png
|-> MmgTestSpace (项目特定图像资源)
|-> b_btn.png
|-> engine_config.xml (直接路径游戏引擎配置)
|-> engine_config_mmg_test_space.xml
|-> engine_config_mmg_dungeon_trap.xml
代码清单 11-3
深入解析 DatConstantsEntry 1
```

要加载的默认游戏引擎配置文件在静态主类的 `ENGINE_CONFIG_FILE` 字段中指定。默认值为文件 **engine_config.xml**。你可以在 IDE 中打开配置文件查看，但我会在此处打印一个简短的示例。

```

代码清单 11-4
深入解析 DatConstantsEntry 2
```

你可以使用这个 XML 文件 **engine_config.xml** 作为模板，为你的下一个游戏创建新文件。文件的第一行是标准的 XML 头部。第二个条目定义了使用的主标签，即 `engineconfig` 标签。你必须为此标签指定一个版本号作为属性。

你能想到为什么这是一个好主意吗？通过使用这个属性，你可以指定一个版本号，从而允许你在未来创建新的 XML 配置文件格式。只要你有唯一的版本号，就可以利用该信息来更改解析 XML 文件的代码。通过这种设置，你可以让你的游戏配置面向未来。

第 3 到 6 行包含了实际的配置信息。XML 标签与 `DatConstantsEntry` 类的字段完美匹配，你可以看到加载过程的一部分就是将 XML 条目简单地转换为 `DatConstantsEntry` 对象列表。

## 动态设置：GameSettingsImporter 类

`GameSettingsImporter` 类负责解析游戏引擎配置文件，并根据配置文件中的 `entry` 标签生成一个 `DatConstantsEntry` 对象数组。让我们来看一些代码！

### 类字段

这个类只有两个字段需要我们关注，如下所示。

```
public Hashtable values;
public String version;
代码清单 11-5
GameSettingsImporter 类字段 1
```

第一个字段是一个用于保存配置文件条目的数据结构。请注意，这是一个键值对结构。此处的键将是关联的 `DatConstantsEntry` 对象的 `key` 字段。在 C# 版本中，这个字段会是 `Dictionary` 对象而不是 `Hashtable`。下一个字段是配置文件的版本号。我们在之前介绍配置文件的 `engineconfig` 标签时已经遇到过这个值。

### 支持方法详情

`GameSettingsImporter` 类有两组 get 和 set 方法，如下所示。

```
public Hashtable GetValues() { ... }
public void SetValues(Hashtable v) { ... }
public String GetVersion() { ... }
public void SetVersion(String v) { ... }
代码清单 11-6
GameSettingsImporter 支持方法详情 1
```

第一组支持方法让你能够访问已加载的值。第二组方法让你能够访问引擎配置文件的版本号。



### 主要方法详情

关于 `GameSettingsImporter` 类，我们只需要讨论两个主要方法。简短而精炼。

```
public void RunImportGameSettings(String xmlFile) throws ParserConfigurationException, SAXException, IOException, Exception { ... }
public boolean ImportGameSettings(String xmlFile) { ... }
代码清单 11-7
GameSettingsImporter 主要方法详情 1
```

列出的第一个方法是实际的引擎配置处理方法。如果 XML 文件的版本与 MmgCore API 中 `GameSettings` 类的 `TARGET_GAME_SETTINGS_XML_VERSION` 值匹配，该方法将解析该 XML 文件。如果出现任何问题，该方法将抛出异常。列出的第二个方法是 `RunImportGameSettings` 方法的安全包装器。它将运行该方法，捕获所有异常，并返回一个表示操作成功与否的布尔值。

### 演示：GameSettingsImporter 类

我们接下来要查看的演示代码来自 MmgCore API 的 `MmgApiGame` 类。请记住，这是一个模板静态主类。在 main 方法中，有一个用于加载引擎配置文件的章节。

```
01 if (ENGINE_CONFIG_FILE != null && ENGINE_CONFIG_FILE.equals("") == false) {
02     GameSettingsImporter dci = new GameSettingsImporter();
03     boolean r = dci.ImportGameSettings(ENGINE_CONFIG_FILE);
04     MmgHelper.wr("引擎配置加载结果: " + r);

06     if(r == true) {
07         int len = dci.GetValues().keySet().size();
08         String[] keys = dci.GetValues().keySet().toArray(new String[len]);
09         String key;
10         DatConstantsEntry ent = null;
11         Field f = null;

13         for (int i = 0; i < len; i++) {
14             try {
15                 key = keys[i];
16                 ent = dci.GetValues().get(key);

18                 if (ent.from != null && ent.from.equals("GameSettings") == true) {
19                     f = GameSettings.class.getField(ent.key);
20                     if (f != null) {
21                         SetField(ent, f);
22                     }
23                 }
...
34             } catch (Exception e) {
35                 MmgHelper.wrErr(e);
36             }
37         }
38     }
39 }
代码清单 11-8
GameSettingsImporter 演示 1
```

如果定义了游戏引擎配置文件，我们会在第 2 行初始化一个新的 `GameSettingsImporter` 对象。第 3 行通过调用 `ImportGameSettings` 方法来解析和加载 XML 文件。请注意，结果存储在局部变量 `r` 中。在第 7 行，如果配置文件加载成功，则要处理的条目数会被本地存储，随后在第 8 行提取数据的键。处理循环中使用的局部变量在第 9-11 行定义。

在遍历数据项时，当前项的键和 `DatConstantsEntry` 对象分别在第 15 行和第 16 行设置。如果条目正确定义，我们会将其 `from` 字段（请记住，这是持有目标静态字段的类的名称）与一组默认支持的类进行比较。在前面的示例代码中（第 19-22 行），使用 `SetField` 方法来更新指定类的静态字段的值。至此，我们对 `GameSettingsImporter` 类的回顾就结束了。接下来，我们将看看游戏引擎中是如何管理字符串的。

## 动态设置：DatExternalStrings

`DatExternalStrings` 类用于保存游戏中的字符串。您无需在游戏类中硬编码这些字符串，而是可以将它们输入此类，以便在整个游戏中轻松访问。该类设计为使用静态字段和方法。您只需为字符串添加一个 ID，然后将加载字符串的代码添加到类的准备方法中。

### 静态类成员

`DatExternalStrings` 类的所有成员都是静态的。让我们先看看静态字段。

```
public static Hashtable EXT = new Hashtable();
public static int EXT_TALK_TREEFOLK_NO_CONVO = 0;
public static int EXT_TALK_TREEFOLK_CONVO = 1;
public static int EXT_TALK_PIGGY_NO_CONVO = 2;
public static String DEFAULT_LANGUAGE = "en";
代码清单 11-9
DatExternalStrings 静态类成员 1
```

上面列出的第一个字段 `EXT` 是保存该类所有字符串的数据结构。此字段是该类的默认组成部分。接下来的三个字段是添加的字段示例，用作唯一的字符串标识符。要使用此类管理的字符串，只需使用 ID 字段作为键来获取关联的字符串值。

上面列出的最后一个条目是默认语言代码。此代码实际上可以是您想要的任何值。其主要目的是提供一个值，以便根据提供的语言代码驱动加载不同的字符串。接下来，让我们继续看静态类方法。

```
public static void LOAD_EXT_STRINGS() { ... }
public static void LOAD_EXT_STRINGS(String langCode) { ... }
代码清单 11-10
DatExternalStrings 静态类成员 2
```

上面列出的方法用于通过加载一组基于用户定义 ID 和字符串的字符串来准备该类以供使用。上面列出的第一个条目 `LOAD_EXT_STRINGS` 不带参数，并使用默认语言代码加载配置的字符串数据。第二个条目执行相同的操作，只是您可以提供一个语言代码作为方法参数。您可以使用此系统为游戏添加多语言支持。

### 演示：DatExternalStrings 类

`DatExternalStrings` 类的示例代码来自该类的 `LOAD_EXT_STRINGS` 方法。

```
1 if (EXT.contains(EXT_TALK_NOBODY_HERE) == false) {
2     EXT.put(EXT_TALK_NOBODY_HERE, "这里没有人可以交谈。");
3 }
代码清单 11-11
DatExternalStrings 类演示 1
```

上面列出的代码演示了如何将新字符串加载到外部字符串类中。请注意，在将 ID 和字符串值添加到类的字符串列表之前，我们会检查特定条目是否已存在。

```
1 DatExternalStrings.EXT.get(DatExternalStrings.STRING_ID);
代码清单 11-12
DatExternalStrings 类演示 2
```

上面列出的下一个简短代码片段是使用 `DatExternalStrings` 类访问已加载字符串值的示例。请注意，我们只是利用静态类成员来指定要访问哪个字符串。

## 动态设置：GameSettings

`GameSettings` 类是另一个静态值类，类似于我们刚刚回顾的 `DatExternalStrings` 类。它主要用于保存配置游戏引擎的值，并作为用户定义字段的存放位置，这些字段控制游戏的玩法。请记住，您可以使用内置的游戏引擎配置文件支持来设置此类的静态字段的值。



### 静态类成员

`GameSettings` 类本质上是一个静态字段的集合。游戏引擎使用了许多默认字段，我们接下来将逐一介绍。

```
//游戏引擎选项
public static boolean INPUT_NORMALIZE_KEY_CODE = false;
public static String TARGET_GAME_SETTINGS_XML_VERSION = "1.0";
public static boolean LOAD_NATIVE_LIBRARIES = false;
public static boolean RUN_OS_SPECIFIC_CODE = true;
public static boolean DEVELOPMENT_MODE_ON = true;
//游戏引擎路径
public static String IMAGE_LOAD_DIR = "../cfg/drawable/";
public static String SOUND_LOAD_DIR = "../cfg/playable/";
public static String PROGRAM_IMAGE_LOAD_DIR = "../cfg/drawable/";
public static String PROGRAM_SOUND_LOAD_DIR = "../cfg/playable/";
public static String AUTO_IMAGE_LOAD_DIR = "../cfg/drawable/auto_load/";
public static String AUTO_SOUND_LOAD_DIR = "../cfg/playable/auto_load/";
public static String CLASS_CONFIG_DIR = "../cfg/class_config/";
//游戏与开发者信息
public static String NAME = "Unknown";
public static String VERSION = "0.0.0";
public static String DEVELOPER_COMPANY = "Unknown";
public static String TITLE = "Unknown";
清单 11-13
GameSettings 静态类成员 1
```

请记住，`GameSettings` 类中的任何一个静态字段都可以在运行时通过游戏引擎配置文件进行设置。第一个条目 `INPUT_NORMALIZE_KEY_CODE` 用于开启对键盘输入代码规范化的支持。启用此选项后，所有键盘输入都将通过一个规范化方法进行处理，你可以在该方法中添加支持以调整键盘输入代码。内置的规范化调用是 MmgBase API 中 `MmgHelper` 类的 `NormalizeKeyCode` 方法。

接下来的两个字段 `LOAD_NATIVE_LIBRARIES` 和 `RUN_OS_SPECIFIC_CODE`，在游戏的静态主类中作为启动过程的一部分使用。如果启用了 `LOAD_NATIVE_LIBRARIES` 选项，引擎将尝试为当前操作系统加载游戏手柄驱动程序。

需要说明的是，在 C# 版本的引擎中无需加载游戏手柄驱动程序。C# 实现已内置了对游戏手柄的支持。类似地，如果启用了 `RUN_OS_SPECIFIC_CODE` 字段，则会在静态主类中开启特定于操作系统的代码执行。当然，你需要添加一些要运行的代码，但现在它已在引擎中启用。开发模式字段用于在启用时，在游戏窗口标题中显示开发者和版本信息。

下一组字段是游戏引擎的资源路径。引擎将根据其版本，在默认位置查找资源。请仔细查看这些路径，并确保理解它们的用途。最后一组字段提供了关于游戏开发者和版本的信息。请确保 `NAME` 字段与游戏特定资源文件夹的名称匹配。否则，引擎将无法找到这些文件夹。

默认情况下，`GameSettings` 类中包含许多输入定义。我们将要查看的下一组字段定义了键盘的方向键（dpad）输入 ID。此外，还有针对其他设备（如 GPIO 和 USB 游戏手柄）的方向键输入定义。这些是当设备的方向键输入被激活时，传递给游戏屏幕的 ID 值。

```
public static int SRC_KEYBOARD = 0;
public static int DOWN_KEYBOARD = 0;
public static int UP_KEYBOARD = 1;
public static int LEFT_KEYBOARD = 2;
public static int RIGHT_KEYBOARD = 3;
清单 11-14
GameSettings 静态类成员 2
```

这些字段为来自键盘的方向键输入提供了唯一标识符。除了方向键输入定义之外，还有用于处理其他 GPIO 和 USB 游戏手柄输入的默认值。请查看这些字段并思考它们可能的用途。我们不会在此处详细讨论游戏手柄输入，但对其工作原理有所了解是好的。

### 演示：GameSettings 类

`GameSettings` 类在 MmgCore API 中无处不在。以下示例代码片段来自 `GamePanel` 类的构造函数中的输入注册部分。回想一下，`GamePanel` 负责管理游戏屏幕和输入等事务。

```
1 if(GameSettings.INPUT_NORMALIZE_KEY_CODE) {
2     ProcessKeyClick(e.getKeyChar(), MmgHelper.NormalizeKeyCode(e.getKeyChar(), e.getKeyCode(), e.getExtendedKeyCode(), "java"));
3 } else {
4     ProcessKeyClick(e.getKeyChar(), e.getExtendedKeyCode());
5 }
清单 11-15
GameSettings 类演示 1

```

前面代码片段中列出的演示代码向我们展示了如何在 API 中使用 `GameSettings` 类来启用某些功能。在这个例子中，我们查看的是键盘代码规范化功能。如果启用了此设置，则会在第 2 行使用经过过滤的键码调用 `ProcessKeyClick` 方法。如果未启用该选项，则使用正常的输入处理流程，如第 4 行所示。

## 章节总结

在本章中，我们完成了对 MmgCore API 动态设置功能的回顾。让我们暂时退一步，尝试从全局角度审视一下。在 MmgBase API 中，我们遇到了结构非常严谨、旨在实现互操作性的类。它们支持克隆和比较等功能。

在 MmgCore API 中，我们遇到了性质不同的类。这些类本质上非常专业化，因此像克隆和比较这样的功能实际上用处不大。这是因为通常只使用该类的一个实例。我们看到的是，MmgBase API 受益于作为一个通用定义的 API，没有太多直接表达的功能。另一方面，MmgCore API 受益于作为一个特定定义的 API，直接表达了大量功能，如窗口化、资源加载、动态设置等。

我们在这里介绍的功能是引擎向游戏提供设置数据的能力。我们已经看到，你可以提供命令行参数来驱动游戏的一些设置，例如尺寸和帧率。我们已经看到，你可以指定一个游戏引擎配置文件，既可以作为默认选项，也可以作为通过命令行参数提供的动态选项。最后，我们看到了通过使用 `DatExternalStrings` 类来管理游戏字符串的能力，并且我们研究了引擎的核心设置类 `GameSettings`。让我们总结一下本章中回顾的类：

*   `DatConstantsEntry`：一个 API 类，用于对游戏引擎配置 XML 文件中的一行数据进行建模。
*   `GameSettingsImporter`：一个 API 类，用于解析和加载游戏引擎配置 XML 文件。
*   `DatExternalStrings`：一个 API 类，用于根据语言代码加载和存储字符串。为 API 提供集中式字符串管理。
*   `GameSettings`：游戏引擎的核心设置类。这里存储了所有默认的游戏设置和选项。你也可以使用此类来存储你自己游戏特定的设置。

能够用数据驱动游戏的各个方面是一项强大的能力。这是你在其他游戏引擎中也会看到的功能，并且是游戏开发中不可或缺的一部分。数据驱动的设置经常用于配置输入、网络连接、游戏变量等等。要知道游戏中哪些值应该由配置文件驱动，这需要经验。别担心。通过使用这个引擎构建游戏，你将获得这些经验以及更多，并且你可以将这些知识应用到你在游戏开发旅程中将要使用的下一组工具中。



