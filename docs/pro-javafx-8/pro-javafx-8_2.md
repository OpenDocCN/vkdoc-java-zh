# 文档大纲

*   内容概览
*   目录
*   关于作者
*   关于技术审校
*   致谢
*   引言
*   第 1 章：快速入门 JavaFX
    *   JavaFX 简史
    *   准备你的 JavaFX 之旅
        *   所需工具
        *   JavaFX 社区
        *   使用官方规范
        *   ScenicView
            *   打包与分发
    *   开发你的第一个 JavaFX 程序：Hello Earthrise
        *   从命令行编译和运行
        *   理解 Hello Earthrise 程序
            *   构建器去哪了？
            *   JavaFX 应用程序
            *   舞台与场景
            *   显示图像
            *   显示文本
            *   将图形节点作为组操作
            *   裁剪图形区域
            *   让文本滚动动画
        *   使用 NetBeans 构建和运行程序
    *   开发你的第二个 JavaFX 程序：“More Cowbell! ”
        *   构建和运行音频配置程序
        *   音频配置程序的行为
        *   理解音频配置程序
            *   绑定的魔力
        *   颜色与渐变
        *   音频配置示例的模型类
        *   使用 InvalidationListener 和 Lambda 表达式
    *   概览 JavaFX 特性
    *   总结
    *   资源
*   前言
*   第 2 章：在 JavaFX 中创建用户界面
    *   程序化与声明式创建用户界面
    *   节点中心型 UI 简介
    *   设置舞台
        *   理解 Stage 类
        *   使用 Stage 类：StageCoach 示例
        *   理解 StageCoach 程序
            *   获取程序参数
            *   设置舞台样式
            *   控制舞台是否可调整大小
            *   使舞台全屏
            *   处理舞台的边界
            *   绘制圆角矩形
            *   在没有标题栏时在桌面上拖动舞台
            *   使用 UI 布局容器
            *   判断舞台是否获得焦点
            *   控制舞台的 Z 轴顺序



*   关闭舞台并检测其关闭时机
    *   创建场景
        *   使用 Scene 类：OnTheScene 示例
        *   理解 OnTheScene 程序
            *   为场景设置光标
            *   绘制场景背景
            *   用节点填充场景
            *   通过 ID 查找场景节点
            *   从场景访问舞台
            *   将节点插入场景的内容序列
            *   为场景中的节点应用 CSS 样式
    *   处理输入事件
        *   概览鼠标、键盘、触摸和手势事件及其处理器
        *   理解 KeyEvent 类
        *   理解 MouseEvent 类
        *   理解 TouchEvent 类
        *   理解 GestureEvent 类
    *   在场景中为节点添加动画
        *   使用时间线进行动画制作
            *   Metronome1 示例
            *   理解 Metronome1 程序
            *   理解 Timeline 类
            *   向时间线中插入关键帧
            *   控制与监控时间线
        *   使用过渡类进行动画制作
            *   MetronomeTransition 示例
            *   MetronomeTransition 程序的行为
            *   理解 MetronomeTransition 程序
            *   使用 TranslateTransition 类
            *   控制与监控过渡
            *   MetronomePathTransition 示例
            *   MetronomePathTransition 程序的行为
            *   理解 MetronomePathTransition 程序
            *   使用 PathTransition 类
            *   绘制椭圆
        *   节点碰撞检测的禅意
            *   理解 ZenPong 程序
            *   使用 KeyFrame 动作事件处理器
            *   使用节点的 intersects() 方法检测碰撞
            *   拖拽节点
            *   为节点赋予键盘输入焦点
            *   使用 onKeyPressed 事件处理器
    *   总结
    *   资源
*   第 3 章：使用 SceneBuilder 创建用户界面
    *   使用 FXML 设置舞台
        *   使用 JavaFX SceneBuilder 以图形化方式创建用户界面
        *   理解 FXML 文件
        *   理解控制器
        *   理解 FXMLLoader
    *   理解 FXML 加载机制
        *   理解 FXMLLoader 类
        *   理解 @FXML 注解
    *   探索 FXML 文件的能力
        *   FXML 格式的反序列化能力
        *   理解默认属性和静态属性
        *   理解属性解析与绑定
        *   使用多个 FXML 文件
        *   使用 fx:root 创建自定义组件
        *   使用脚本或控制器属性进行事件处理
    *   使用 JavaFX SceneBuilder
        *   JavaFX SceneBuilder 概述
        *   理解菜单栏和菜单项
        *   理解库面板
        *   理解文档面板
        *   理解内容面板
        *   理解检查器面板
    *   总结
    *   资源
*   第 4 章：属性与绑定
    *   JavaFX 绑定的先驱
    *   一个激励示例
    *   理解关键接口与概念
        *   理解 Observable 接口
        *   理解 ObservableValue 接口
        *   理解 WritableValue 接口
        *   理解 ReadOnlyProperty 接口
        *   理解 Property 接口
        *   理解 Binding 接口
    *   关键接口的类型特定特化
        *   类型特定接口的通用主题
        *   常用类
    *   创建绑定
        *   理解 Bindings 工具类
        *   理解流畅接口 API
    *   理解 JavaFX Beans 约定
        *   JavaFX Beans 规范
        *   理解急切实例化属性策略
        *   理解惰性实例化属性策略
        *   使用选择绑定
    *   将 JavaBeans 属性适配为 JavaFX 属性
        *   理解 JavaBeans 属性
        *   理解 JavaFX 属性适配器
    *   总结
    *   资源
*   第 5 章：在 JavaFX 中构建动态 UI 布局
    *   介绍 JavaFX Reversi
        *   棋盘布局与基本规则
        *   为 Reversi 构建 JavaFX 模型
    *   动态布局技术
        *   使用绑定居中文本
        *   使用 StackPane 重新实现居中
        *   使用 StackPane 和 TilePane 对齐边缘



*   使用 FlowPane 和 Boxes 进行方向对齐
    *   使用 BorderPane 组合布局
*   创建自定义区域
    *   构建自定义方形区域
    *   构建可调整大小的黑白棋棋子
*   使用 GridPane 平铺布局
*   使用 AnchorPane 进行对齐和拉伸
*   何时使用不同的布局
*   让黑白棋活起来
    *   高亮合法移动
    *   高亮活动单元格
    *   轮流下棋
*   使用 FXML 声明用户界面
    *   额外的游戏增强功能
*   总结
*   资源
*   第 6 章：使用 JavaFX UI 控件
    *   试用 JavaFX UI 控件
    *   利用 JavaFX UI 控件
        *   创建菜单并定义菜单项
        *   创建工具栏
            *   定义图形按钮
            *   定义切换按钮
            *   使用切换组
            *   在工具栏中插入分隔符
            *   创建 TabPane 并定义选项卡
        *   创建 TableView
            *   为表格分配项目
            *   定义 TableView 列
            *   检测行何时被选中
        *   创建 Accordion 并定义 TitledPane
        *   创建 TreeView
            *   定义 TreeItem
            *   检测 TreeItem 何时被选中
        *   创建 ListView 并向其分配项目
        *   创建 SplitPane
        *   创建 TreeTableView
        *   定义 ScrollPane
        *   使用 CheckBox
        *   定义 RadioButton
        *   创建 Hyperlink
        *   定义 ChoiceBox
        *   使用 MenuButton
        *   创建 ContextMenu
        *   创建 SplitMenuButton
        *   定义 TextField
        *   使用 PasswordField
        *   创建 TextArea
        *   创建 DatePicker 和 ColorPicker
        *   创建 Slider
        *   定义 ProgressIndicator
        *   定义 ScrollBar
        *   使用 ProgressBar
        *   创建 HTMLEditor
        *   创建 Popup
        *   使用 WebView
    *   总结
    *   资源

*   第 7 章：集合与并发
    *   理解可观察集合和数组
        *   理解 ObservableList
        *   处理 ListChangeListener 中的变更事件
        *   理解 ObservableMap
        *   理解 ObservableSet
        *   理解 ObservableArrays
        *   使用 FXCollections 的工厂和实用方法
    *   使用 JavaFX 并发框架
        *   识别 JavaFX 应用程序中的线程
        *   修复无响应的用户界面
        *   理解 javafx.concurrent 框架
            *   理解 Worker 接口
            *   理解 Task<V> 抽象类
            *   理解 Service<V> 抽象类
            *   理解 ScheduledService<V> 抽象类
    *   将 JavaFX 与其他 GUI 工具包混合使用
        *   在 Swing 应用程序中嵌入 JavaFX 场景
        *   在 SWT 应用程序中嵌入 JavaFX 场景
        *   在 JavaFX 应用程序中嵌入 Swing 组件
    *   总结
    *   资源
*   第 8 章：在 JavaFX 中创建图表
    *   JavaFX 图表 API 的结构
    *   使用 JavaFX PieChart
        *   简单示例
        *   一些修改
    *   使用 XYChart
        *   使用 ScatterChart
            *   一个简单的实现
            *   改进简单实现
        *   使用 LineChart
        *   使用 BarChart
        *   使用 StackedBarChart
        *   使用 AreaChart
        *   使用 StackedAreaChart
        *   使用 BubbleChart
    *   总结
    *   资源
*   第 9 章：使用媒体类
    *   基础
    *   支持的媒体格式
    *   使用音频剪辑
        *   控制 AudioClip 的播放参数
        *   构建场景
        *   AudioClip 总结
    *   使用媒体
    *   播放音频
        *   错误处理
        *   显示元数据
        *   加载媒体
        *   控制播放
            *   布局播放器控件
            *   创建播放控件
            *   搜索
            *   控制音量
            *   重复播放



*   音频均衡
        *   MediaPlayer 总结
    *   播放视频
        *   控制 MediaView 的大小
        *   MediaView 与特效
        *   使用标记
        *   一个播放器，多个视图
    *   将 AudioPlayer 转换为 VideoPlayer
    *   总结
    *   资源
*   第 10 章：JavaFX 3D
    *   概述
    *   理解 JavaFX 3D 对象
        *   理解 Shape3D 基类
        *   创建预定义的 3D 形状
        *   创建用户定义的 3D 形状
    *   为 JavaFX 3D 场景添加摄像机
        *   理解 PerspectiveCamera
    *   为 JavaFX 场景添加光照
        *   理解 LightBase 类
        *   理解 AmbientLight 类
        *   理解 PointLight 类
        *   理解子场景
    *   指定 3D 形状的材质
        *   理解 PhongMaterial 类
        *   为 3D 形状添加纹理
    *   与 JavaFX 3D 场景交互
        *   理解 PickResult 类
    *   理解 Canvas 和 Image Ops API
        *   理解 Canvas API
        *   理解 Image Ops API
    *   总结
    *   资源
*   第 11 章：访问 Web 服务
    *   前端和后端平台
    *   在同一环境中合并 JavaFX 和 Java 企业模块
    *   使用 JavaFX 调用远程（Web）服务
        *   SOAP
        *   REST
            *   设置应用程序
            *   使用 StackExchange API
                *   JSON 响应格式
                *   XML 响应格式
                *   异步处理
                *   将 Web 服务数据转换为 TableView
            *   使用外部库
                *   DataFX
                *   JAX-RS
    *   总结
*   第 12 章：嵌入式与移动设备上的 JavaFX
    *   OpenJFX：代码之源
    *   ARM 上的 JavaFX
        *   树莓派
        *   树莓派上的 JavaFX
        *   在嵌入式 ARM 系统上部署 JavaFX 应用程序
            *   使用命令行工具
            *   使用 NetBeans
    *   移动设备上的 JavaFX
        *   iOS 上的 JavaFX
        *   Android 上的 JavaFX
    *   JavaFXPorts：整合所有
    *   总结
    *   资源
*   第 13 章：JavaFX 语言与标记
    *   替代语言的快速比较
        *   Java 中的消失圆
        *   替代 JVM 语言中的消失圆
    *   让你的 JavaFX 更 Groovy
        *   GroovyFX 简介
        *   GroovyFX 中的属性
        *   GroovyFX 绑定
        *   GroovyFX API 增强
            *   动画
            *   表格
            *   布局
    *   Scala 与 JavaFX
        *   ScalaFX 入门
        *   ScalaFX 代理与隐式转换
        *   Scala 中的 JavaFX 属性
        *   ScalaFX 绑定 API
        *   API 增强
            *   闭包
            *   布局约束
            *   动画
    *   总结
    *   资源
*   索引
