# 19. 结论

恭喜你！如果你已经读到这里，并且完成了本书的第 1、2 和 3 部分，那么你已经完成了一项了不起的成就。我想花点时间回顾一下，看看我们在本书中共同完成了什么。

## 成就

我们取得的第一个主要成就是介绍并详细解释了一个相当健壮的 2D 游戏引擎 API。我们实际上是从零开始，逐一回顾每个游戏引擎类，完成了对 MmgBase API 的审查。在这次审查中，我们见证了设计良好的超类（如果你使用 C# 进行开发，则称为基类）`MmgObj` 类的强大之处。

我们还亲眼目睹了如何设计和构建不仅是 2D 游戏引擎 API，实际上是任何高级 API。一致的命名约定示例、全局支持的功能（如克隆和比较）以及文档完善的代码，都是任何优秀 API 和任何优秀软件开发项目的基础。

我们通过本书共同取得的第二个主要成就是对 MmgCore API 的详细审查。我们看到了如何将 MmgBase API 的功能与 MmgCore API 的应用层功能（如用户输入、画面管理和主绘制循环）分离开来。我们还学习了如何通过使用 MmgTestScreens API 的示例应用程序来启动我们的游戏并演示 MmgBase API 的功能。

最后但同样重要的是，我们应用了所学的一切，从零开始构建了两个游戏：PongClone 和 DungeonTrap。对于 PongClone，我们从空项目开始。我们引入了 API 代码，仅用几个类文件就创建了一个可以启动和游玩的游戏。DungeonTrap 游戏构建版本可作为额外下载内容，从本书的 GitHub 仓库获取。我强烈建议你下载并完成 DungeonTrap 游戏。

对于 DungeonTrap 游戏，我们构建了一个复杂的类层次结构，该结构正确地封装并从超类向子类暴露了功能。我们成功地在短短几章内审查了超过 40 个类并完成了游戏。花点时间为自己庆祝一下，享受你所取得的成就。在此，我要感谢为本书某些项目提供资源的艺术家和网站。

## 下一步去向何方

那么，既然你已经完成了本书，并且拥有了一些游戏引擎和 2D 游戏构建经验，你可能会想，*我接下来可以去哪里？* 显然，这个问题很大程度上取决于你自己，但我当然可以给出一些建议。

### 扩展 PongClone 游戏

花点时间自定义 PongClone 游戏。你可以添加新的游戏功能，比如多球、超高速、更大的球拍，等等。你也可以修改游戏以支持不同的模式。考虑不同的获胜条件或罚球模式。这完全取决于你。

### 扩展 DungeonTrap 游戏

你可以花点时间自定义你的 DungeonTrap 游戏。例如，游戏中存在炸弹和宝箱等物品，但尚未实现。同样，还有一些未使用的武器（斧头和剑），以及敌人角色使用武器的能力。所有这些都可以用来自定义和完善游戏。至少，你应该确保调整关卡、敌波次和参数，使游戏更具可玩性。

### 扩展游戏引擎 API

你可以为游戏引擎项目添加新功能，无论是在 MmgBase API 层面（你可以定义更多的小部件和工具来使用），还是在 MmgCore API 层面（你可以创建专门用于横向卷轴游戏或 2D 俯视角射击游戏的游戏画面）。潜力无限，真的。



### 学习跨平台编程

你可以利用本书附带的项目来学习一门新的编程语言（Java 或 C#），或者积累跨平台 API 设计与实现的经验。由于你对游戏引擎的 API 级代码有丰富的经验，你将更容易理解跨平台设计决策以及由编程语言差异带来的不同。我强烈建议你留出一些时间来仔细研究附带游戏引擎项目中的跨平台部分。

### 制作更多游戏，享受乐趣

最后但同样重要的是，你可以直接制作更多游戏，并从中获得乐趣。毕竟，如果不能享受乐趣并制作几款游戏，那么学习 2D 游戏引擎的设计与实现又有什么意义呢？

## 告别

我衷心希望你喜欢这本书，并从中获得些许快乐、知识、智慧或经验。祝你编码愉快！



索引 A 成就 致谢 动画 MmgPositionTween/MmgSizeTween MmgPulse B 基类 MmgBmp 类 MmgColor 类 MmgFont 类 MmgObj 类（参见 MmgObj 类） MmgPen 类 MmgRect 类 MmgSound 类 LoadResuorces 方法 MmgVector2 类 C C#/Java 编程语言 类定义 核心代码 游戏引擎 游戏输出屏幕 静态主类 跨平台编码 C#/Visual Studio D DatConstantsEntry 类 类字段 深入理解 游戏引擎 主方法 细节 DungeonTrap 游戏 自定义 动态数据处理 DatConstantsEntry DatExternalStrings 类 GameSettings 类 GameSettingsImporter E, F 事件处理器 GenericEventHandler GenericEventMessage HandleMainMenuEvent 类 LoadResourceUpdateHandler LoadResourceUpdateMessage 类 审查流程 G 游戏引擎 API C# 版本 Java NetBeans IDE 包/命名空间 高级类 动画类 基类 类 C# 项目 辅助类 屏幕类 工具类 控件类 SDK 概述 游戏引擎项目 MmgBase API MmgCore API 游戏大纲 DungeonTrap 大纲 规格 PongClone 大纲 规格 资源 GamePanel 类 类字段 枚举 深入审查 章节 输入方法 主方法 细节 静态成员 支持方法 细节 SwitchGameState 方法 游戏屏幕类 MmgGameScreen 类 MmgLoadingScreen 类 屏幕类 审查流程 RunFrameRate ScreenLoading 类 ScreenMainMenu 类 ScreenSplash 类 GameSettings 类 演示 静态类成员 静态字段 GameSettingsImporter 类 类字段 演示 代码 主方法 支持方法 细节 GenericEventHandler 类 演示 章节 审查 ScreenSplash 和 ScreenLoading GenericEventMessage 类 类字段 演示 主方法 支持方法 细节 H HandleMainMenuEvent 类 类字段 演示 代码 主方法 细节 静态字段 辅助类 类 MmgApiUtils 类 MmgBmpScaler 类 MmgDebug 类 MmgFontData 类 MmgMediaTracker 类 MmgScreenData 类 审查 I 集成开发环境 (IDE) NetBeans Visual Studio J, K Java/NetBeans 项目 信息 列表 设置 信息 L 启动游戏 C# 游戏引擎 Java 参数 执行命令 原生库路径 静态主目标 main/args Java 游戏引擎 LoadResourceUpdateHandler 类 LoadResourceUpdateMessage 类 类字段 演示 章节 主方法 支持方法 细节 M MainFrame 类 类字段 深入代码审查 主方法 细节 支持方法 Mmg9Slice 类 类字段 演示 主方法 细节 支持方法 Mmg9Slice 类 演示 MmgApiUtils 类 演示 MmgHelper 类 静态方法 MmgBmp 类 类字段 演示 枚举 主方法 MmgLoadingBar 类 静态类字段 支持方法 MmgBmpScaler 类 MmgCfgFileEntry 类 类字段 演示 枚举 标准 主方法 支持方法 细节 MmgColor 类 类字段 演示 主方法 细节 方法 静态类成员 MmgContainer 类 类字段 演示 章节 枚举 主方法 MmgObj 类 MmgTestSpace 包 静态类字段 支持方法 MmgCore API C# 实现 游戏引擎 设置 游戏线程/命令行参数 资源加载 类 屏幕/工作线程 静态入口点/窗口类 系统字体 MmgDebug 类 MmgDrawableBmpSet 类 类字段 演示 MmgEvent 类 类字段 演示 主方法 静态类成员 支持方法 细节 MmgEventHandler 类 类审查 演示 MmgFont 类 类字段 演示 枚举 游戏屏幕对象 主方法 细节 方法 细节 SetFontSize 方法 MmgFontData 类 演示 主方法 静态类成员 MmgGameScreen 类 类字段 演示 章节 输入方法 细节 主方法 支持方法 细节 MmgHelper 类 演示 静态类 AbsDistance 方法 布尔标志 配置文件 控制 日志/资源 CreateDrawableBmpSet 方法 标志控制 GetBasicBmp 方法 GetBasicCachedBmp 方法 GetImageCacheBmp 方法 MmgObj 类 ReadClassConfigFile 方法 WriteClassConfigFile 方法 MmgLabelValuePair 类 类字段 演示 主方法 SetFontSize 方法 静态类成员 支持方法 MmgLoadingBar 类 类字段 演示 主方法 支持方法 MmgLoadingScreen 类 演示 章节 类字段 主方法 细节 静态类成员 支持方法 细节 字段 主方法 支持方法 MmgMediaTracker 类 MmgMenuContainer 类 类字段 演示 主方法 支持方法 细节 MmgMenuItem 类 类字段 演示 主方法 静态类字段 支持方法 MmgObj 类 类字段 可绘制对象 主方法 细节 CloneTyped 方法 演示 代码 源代码 方法 审查流程 标准审查步骤 MmgPen 类 类字段 演示 主方法 细节 RotateImageStatic 方法 静态类成员 支持方法 MmgPositionTween/MmgSizeTween 类 类字段 演示 代码 LoadResources 方法 主方法 MmgPulse 类 静态类成员 支持方法 MmgPulse 类 类字段 演示 章节 主方法 审查流程 支持方法 MmgRect 类 类字段 演示 GetDiffX 方法 主方法 细节 静态类成员 支持方法 细节 MmgScreenData 类 CalculateScale 方法 演示 枚举 图形能力 主方法 细节 静态类成员 MmgScrollHor/滚动面板类 类字段 演示 主方法 静态类成员 支持方法 细节 MmgSound 类 类字段 演示 主方法 细节 静态类成员 支持方法 MmgSplashScreen 类 类字段 演示 章节 字段 主方法 细节 静态类字段 支持方法 细节 主方法 MmgSplashScreen 类 静态类支持方法 MmgSprite 类 类字段 演示 代码 主方法 静态类字段 支持方法 细节 MmgTextBlock 类 演示 字段 控制 MmgObj 类 静态类成员 支持方法 MmgTextField 类 类字段 构造函数 演示 代码 主方法 细节 ProcessKeyClick 和 DeleteChar 方法 静态类字段 支持方法 MmgVector2 类 类字段 演示 主方法 细节 静态类方法 支持方法 细节 Monogame，来源 N, O NetBeans 清理并构建 选项 配置过程 dist 文件夹 下载链接 安装 打包选项 项目文件 项目文件夹 P, Q PongClone 添加新功能 自定义 DrawScreen 方法 GamePanel 类 GamePanel 屏幕注册 上下文菜单选项 启动游戏 PongClone.dll 文件 SwitchGameState 方法 GetSpeedPerFrame 静态方法 LoadResources 方法 ScreenGame LoadResources 方法 主菜单 MmgDraw 方法 MmgTestSpace 库 MmgUpdate 方法 UnloadResources 方法 ScreenGame 类 字段 ballDirY 字段 ballPos 字段 block exit/exitBground 字段 功能 infiniteBounce 字段 paddle 1 和 2 rand 字段 scoreLeft/scoreRight 字段 使用 ScreenGame 类 头部 ScreenGame 类 大纲 ScreenGame 输入方法 DrawScreen 方法 MmgUpdate 方法 ProcessAClick 方法 ProcessDebugClick 方法 ProcessDpadRelease 方法 ProcessKeyRelease 方法 ProcessMouseMove 方法 ScreenGame 主方法 DrawScreen 方法 错误 MmgDraw 方法 NetBeans IDE NONE/SHOW_GAME_EXIT 状态 挡板碰撞 占位符 ResetGame 方法 SHOW_COUNT_DOWN/SHOW_COUNT_DOWN_IMAGE 状态 SHOW_GAME 状态 更新方法 ScreenGame 支持方法 ScreenMainMenu 类 SetScoreRightText 方法 SetState 方法 超类构造函数 SwitchGameState 方法 PongClone 游戏 C#/Java C#/Visual Studio Java/NetBeans 项目 设置 R 资源加载与访问 访问显式资源 自动加载目录 游戏特定资源 审查流程 RunResourceLoad RunFrameRate 类 类字段 演示 章节 主方法 静态类字段 支持方法 细节 RunResourceLoad 类 类字段 演示 章节 主方法 审查流程 支持方法 细节 运行时 API 参见 MmgCore API S, T, U Screen 类 类字段 演示 章节 主方法 细节 支持方法 ScreenLoading 类 类字段 演示 主方法 静态类成员 支持方法 细节 ScreenMainMenu 类 类字段 演示 代码 主方法 支持方法 ScreenSplash 类 类字段 演示 主方法 细节 静态类支持方法 细节 静态主入口点 GamePanel 类 MainFrame 类 MmgApiGame 静态类字段 ArrayHasEntryLike 方法 CLI 驱动属性 配置文件 深入理解 Java 版本 审查流程 运行时流程 V Visual Studio 应用程序 应用程序启动 内容文件夹 游戏引擎项目 安装 NuGet 包 重建 图标创建 W, X, Y, Z 控件类 MmgMenuContainer 类 MmgMenuItem MmgScrollHor/滚动面板类 MmgTextBlock 类 MmgTextField
