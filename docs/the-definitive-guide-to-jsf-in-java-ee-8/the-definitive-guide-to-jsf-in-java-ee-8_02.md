# 目录

1.  第 1 章：历史
    1.  起源……
    2.  青少年时期
    3.  走向成熟
    4.  焕发新生
2.  第 2 章：从零到 Hello World
    1.  安装 Java SE JDK
        1.  Java EE 呢？
    2.  安装 Payara
        1.  其他服务器呢？
    3.  安装 Eclipse
        1.  配置 Eclipse
        2.  安装 JBoss Tools 插件
        3.  在 Eclipse 中集成新服务器
    4.  在 Eclipse 中创建新项目
        1.  创建支持 Bean 类
        2.  创建 Facelets 文件
        3.  部署项目
    5.  安装 H2
        1.  配置数据源
        2.  配置 JPA
        3.  创建 JPA 实体
        4.  创建 EJB 服务
        5.  调整 Hello World
3.  第 3 章：组件
    1.  标准 HTML 组件
    2.  标准核心标签
    3.  生命周期
        1.  恢复视图阶段（第一阶段）
        2.  应用请求值阶段（第二阶段）
        3.  处理验证阶段（第三阶段）
        4.  更新模型值阶段（第四阶段）
        5.  调用应用程序阶段（第五阶段）
        6.  渲染响应阶段（第六阶段）
    4.  Ajax 生命周期
    5.  视图构建时间
    6.  视图渲染时间
    7.  视图状态
    8.  视图作用域
    9.  阶段事件
    10.  组件系统事件
    11.  自定义组件系统事件
    12.  JSTL 核心标签
    13.  操作组件树
4.  第 4 章：表单组件
    1.  输入、选择和命令组件
    2.  基于文本的输入组件
    3.  基于文件的输入组件
    4.  选择组件
    5.  SelectItem 标签
    6.  SelectItemGroup
    7.  标签和消息组件
    8.  命令组件
    9.  导航
    10.  Ajax 化组件
    11.  Ajax 中的导航
    12.  GET 表单
    13.  无状态表单
5.  第 5 章：转换与验证
    1.  标准转换器
        1.  <f:convertNumber>
        2.  <f:convertDateTime>
    2.  标准验证器
        1.  <f:validateLongRange>/<f:validateDoubleRange>
        2.  <f:validateLength>/<f:validateRegex>
        3.  <f:validateRequired>
        4.  <f:validateBean>/<f:validateWholeBean>
    3.  Immediate 属性
    4.  自定义转换器
    5.  自定义验证器
    6.  自定义约束
    7.  自定义消息
6.  第 6 章：输出组件
    1.  基于文档的输出组件
    2.  基于文本的输出组件
    3.  基于导航的输出组件
    4.  基于面板的输出组件
    5.  数据迭代组件
        1.  可编辑的 <h:dataTable>
        2.  在 <h:dataTable> 中添加/删除行
        3.  在 <h:dataTable> 中选择行
        4.  <h:dataTable> 中的动态列
    6.  资源组件
    7.  直通元素
7.  第 7 章：Facelets 模板
    1.  XHTML
    2.  模板组合
    3.  单页应用
    4.  模板装饰
    5.  标签文件
    6.  复合组件
        1.  递归复合组件
    7.  隐式 EL 对象
8.  第 8 章：支持 Bean
    1.  模型、视图还是控制器？
    2.  托管 Bean
    3.  作用域
        1.  @ApplicationScoped
        2.  @SessionScoped
        3.  @ConversationScoped
        4.  @FlowScoped
        5.  @ViewScoped
        6.  @RequestScoped
        7.  @Dependent
    4.  选择哪个作用域？
    5.  @FlashScoped 在哪里？
    6.  托管 Bean 的初始化和销毁
    7.  注入 JSF 提供的类型
    8.  急切初始化
    9.  分层
    10.  命名约定
9.  第 9 章：异常处理
    1.  自定义错误页面
    2.  Ajax 异常处理
    3.  ViewExpiredException 处理
    4.  IOException 处理
    5.  EJBException 处理
10.  第 10 章：WebSocket 推送
    1.  配置
    2.  用法
    3.  作用域和用户
    4.  通道设计提示
    5.  一次性推送
    6.  有状态 UI 更新
    7.  全站推送通知
    8.  跟踪活动套接字
    9.  检测会话和视图过期
    10.  剖析 Mojarra 的 f:websocket 实现
11.  第 11 章：自定义组件
    1.  组件类型、组件族和渲染器类型
    2.  创建新组件和渲染器
    3.  扩展现有组件
    4.  扩展现有渲染器
    5.  自定义标签处理器
    6.  打包到可分发的 JAR 中
    7.  资源依赖
12.  第 12 章：搜索表达式
    1.  相对本地 ID
    2.  绝对层次化 ID
    3.  标准搜索关键字
    4.  自定义搜索关键字
13.  第 13 章：安全性
    1.  Java EE 安全概述与历史
    2.  保护对资源的访问
        1.  排除
        2.  未检查
        3.  按角色
    3.  设置认证机制
    4.  设置身份存储
    5.  提供我们自定义的 JSF 代码
    6.  调用者发起的认证
    7.  记住我
        1.  激活“记住我”服务
    8.  注销
    9.  自定义主体
    10.  基于访问权限的条件渲染
    11.  跨站请求伪造保护
    12.  Web 参数篡改保护
    13.  跨站脚本保护
    14.  源码暴露保护
14.  第 14 章：本地化
    1.  Hello World, Olá mundo, ![A454457_1_En_BookFrontmatter_Fige_HTML.gif](img/#A454457_1_En_14_Chapter.xhtml#Sec1) 
    2.  配置
    3.  在 JSF 页面中引用资源包
    4.  更改活动区域设置
    5.  组织资源包键
    6.  本地化转换/验证消息
    7.  在自定义转换器/验证器中获取本地化消息
    8.  本地化枚举
    9.  参数化资源包值
    10.  基于数据库的 ResourceBundle
    11.  资源包中的 HTML
15.  第 15 章：扩展
    1.  扩展类型
    2.  扩展 CDI 构件
    3.  扩展经典构件
    4.  插件
    5.  动态扩展
        1.  应用程序配置填充器
        2.  应用程序主类
    6.  本地扩展与包装
    7.  内省
16.  索引



