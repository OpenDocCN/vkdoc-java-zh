# 文档大纲



*   前言
*   内容概览
*   目录
*   序言
*   关于作者
*   关于技术审校
*   致谢
*   引言
    *   本书结构是怎样的？
    *   下载和运行代码
    *   联系作者
*   Java EE 6 概览
    *   理解 Java EE
        *   一点历史
        *   标准
        *   架构
            *   组件
            *   容器
            *   服务
            *   网络协议
            *   打包
            *   Java 标准版
        *   Java EE 6 规范
    *   Java EE 6 有哪些新特性？
        *   更轻量
            *   精简
            *   配置文件
        *   更易用
        *   更丰富
        *   更具可移植性
    *   CD-BookStore 应用
    *   搭建开发环境
        *   JDK 1.6
        *   Maven 2
            *   一点历史
            *   项目描述符
            *   管理构件
            *   项目模块化
            *   插件与生命周期
            *   安装
            *   使用
        *   JUnit 4
            *   一点历史
            *   它是如何工作的？
            *   测试方法
            *   断言方法
            *   测试夹具
            *   启动 JUnit
            *   JUnit 集成
        *   Derby 10.4
            *   安装
            *   使用
        *   GlassFish v3
            *   一点历史
            *   GlassFish v3 架构
            *   更新中心
            *   GlassFish 子项目
            *   管理
            *   安装 GlassFish
    *   小结
*   Java 持久化
    *   JPA 规范概述
        *   规范简史
        *   JPA 2.0 有哪些新特性？
        *   参考实现
    *   理解实体
        *   对象-关系映射
        *   查询实体
        *   回调与监听器

*   综合运用
        *   编写 Book 实体
        *   编写主类
        *   主类的持久化单元
        *   使用 Maven 编译
        *   使用 Derby 运行主类
        *   编写 BookTest 类
        *   BookTest 类的持久化单元
        *   使用嵌入式 Derby 运行 BookTest 类
    *   小结
*   对象-关系映射
    *   如何映射实体
        *   通过异常进行配置
    *   基本映射
        *   表
            *   @Table
            *   @SecondaryTable
        *   主键
            *   @Id 和 @GeneratedValue
            *   复合主键
        *   属性
            *   @Basic
            *   @Column
            *   @Temporal
            *   @Transient
            *   @Enumerated
        *   访问类型
        *   基本类型集合
        *   基本类型映射
    *   使用 XML 进行映射
    *   可嵌入类
        *   可嵌入类的访问类型
    *   关系映射
        *   关系数据库中的关系
        *   实体关系
            *   单向与双向
            *   @OneToOne 单向
            *   @OneToMany 单向
            *   @ManyToMany 双向
        *   获取关系
        *   排序关系
            *   @OrderBy
            *   @OrderColumn
    *   继承映射
        *   继承策略
            *   单表策略
            *   连接策略
            *   每个类一张表策略
        *   继承层次结构中的类类型
            *   抽象实体
            *   非实体
            *   映射超类
    *   小结
*   管理持久化对象
    *   如何查询实体
    *   实体管理器
        *   获取实体管理器
        *   持久化上下文
        *   操作实体
            *   持久化实体
            *   按 ID 查找
            *   移除实体
            *   孤儿移除



*   与数据库同步
            *   持久化上下文的内容
            *   合并实体
            *   更新实体
            *   级联事件
        *   缓存 API
    *   JPQL
        *   Select
        *   From
        *   Where
            *   绑定参数
            *   子查询
        *   Order By
        *   Group By 和 Having
        *   批量删除
        *   批量更新
    *   查询
        *   动态查询
        *   命名查询
        *   原生查询
    *   并发
        *   版本控制
        *   乐观锁
        *   悲观锁
    *   总结
*   回调和监听器
    *   实体生命周期
    *   回调
    *   监听器
    *   总结
*   企业级 Java Beans
    *   理解 EJB
        *   EJB 的类型
        *   EJB 的结构
        *   EJB 容器
        *   嵌入式容器
        *   依赖注入和 JNDI
        *   回调方法和拦截器
        *   打包
    *   EJB 规范概述
        *   规范历史
        *   EJB 3.1 的新特性
        *   EJB Lite
        *   参考实现
    *   综合实践
        *   编写 Book 实体
        *   编写 BookEJB 无状态会话 Bean
        *   为 BookEJB 配置持久化单元
        *   编写主类
        *   使用 Maven 编译和打包
        *   部署到 GlassFish
        *   使用 Derby 运行主类
        *   编写 BookEJBTest 类
    *   总结
*   会话 Bean 与定时器服务
    *   会话 Bean
        *   无状态 Bean
        *   有状态 Bean
        *   单例 Bean
            *   初始化
            *   单例链
            *   并发
        *   会话 Bean 模型
            *   接口和 Bean 类
            *   客户端视图
            *   会话上下文

*   部署描述符
            *   依赖注入
            *   环境命名上下文
        *   异步调用
        *   可嵌入使用
    *   定时器服务
        *   基于日历的表达式
        *   自动创建定时器
        *   编程式创建定时器
    *   总结
*   回调和拦截器
    *   会话 Bean 生命周期
        *   无状态和单例
        *   有状态
        *   回调
    *   拦截器
        *   环绕调用拦截器
        *   方法拦截器
        *   生命周期拦截器
        *   拦截器链与排除
    *   总结
*   事务与安全
    *   事务
        *   ACID
        *   本地事务
        *   XA 与分布式事务
    *   EJB 中的事务支持
        *   容器管理事务
            *   标记 CMT 为回滚
            *   异常处理
        *   Bean 管理事务
    *   安全
        *   主体与角色
        *   认证与授权
    *   EJB 中的安全支持
        *   声明式安全
        *   编程式安全
    *   总结
*   JavaServer Faces
    *   理解 JSF
        *   FacesServlet 和 faces-config.xml
        *   页面与组件
        *   渲染器
        *   转换器与验证器
        *   托管 Bean 与导航
        *   Ajax 支持
    *   Web 接口规范概述
        *   Web 接口简史
        *   JSP 2.2、EL 2.2 和 JSTL 1.2
        *   JSF 2.0
        *   JSF 2.0 的新特性
        *   参考实现
    *   综合实践
        *   编写 Book 实体
        *   编写 BookEJB
        *   编写 BookController 托管 Bean
        *   编写 newBook.xhtml 页面
        *   编写 listBooks.xhtml 页面
        *   使用 web.xml 进行配置
        *   使用 Maven 编译和打包
        *   部署到 GlassFish
        *   运行示例
    *   总结



*   页面与组件
    *   网页
        *   HTML
        *   XHTML
        *   CSS
        *   DOM
        *   JavaScript
    *   Java 服务器页面
        *   指令元素
        *   脚本元素
        *   动作元素
        *   综合运用
    *   表达式语言
    *   JSP 标准标签库
        *   核心动作
        *   格式化动作
        *   SQL 动作
        *   XML 动作
        *   函数
    *   Facelets
    *   JavaServer Faces
        *   生命周期
        *   标准 HTML 组件
            *   命令
            *   输入
            *   输出
            *   选择
            *   图形
            *   网格与表格
            *   错误消息
            *   其他
            *   模板
        *   资源管理
        *   复合组件
        *   隐式对象
    *   总结
*   处理与导航
    *   MVC 模式
        *   FacesServlet
        *   FacesContext
        *   Faces 配置
    *   受管 Bean
        *   如何编写受管 Bean
        *   受管 Bean 模型
            *   @ManagedBean
            *   作用域
            *   @Managedproperty
            *   生命周期与回调注解
        *   导航
        *   消息处理
    *   转换与验证
        *   转换器
        *   自定义转换器
        *   验证器
        *   自定义验证器
    *   Ajax
        *   通用概念
        *   JSF 中的支持
        *   综合运用
    *   总结
*   发送消息
    *   理解消息
        *   JMS
        *   MDB
    *   消息规范概述
        *   消息传递简史
        *   JMS 1.1
        *   EJB 3.1
        *   参考实现
    *   如何发送和接收消息
*   Java 消息服务
        *   点对点
        *   发布-订阅
        *   JMS API
            *   受管对象
            *   连接
            *   会话
            *   消息
            *   消息生产者
            *   消息消费者
        *   选择器
        *   可靠性机制
            *   设置消息生存时间
            *   指定消息持久性
            *   控制确认
            *   创建持久订阅者
            *   设置优先级
    *   消息驱动 Bean
        *   如何编写 MDB
        *   MDB 模型
            *   @MessageDriven
            *   @activationConfigproperty
            *   依赖注入
            *   MDB 上下文
            *   生命周期与回调注解
        *   作为消费者的 MDB
        *   作为生产者的 MDB
        *   事务
        *   异常处理
    *   综合运用
        *   编写 OrderDTO
        *   编写 OrderSender
        *   编写 OrderMDB
        *   使用 Maven 编译和打包
        *   创建受管对象
        *   在 GlassFish 上部署 MDB
        *   运行示例
    *   总结
*   SOAP Web 服务
    *   理解 Web 服务
        *   UDDI
        *   WSDL
        *   SOAP
        *   传输协议
        *   XML
    *   Web 服务规范概述
        *   Web 服务简史
        *   Java EE 规范
            *   JAX-WS 2.2
            *   Web 服务 1.2
            *   JAXB 2.2
            *   WS-Metadata 2.0
            *   JAXR 1.0
        *   参考实现
    *   如何调用 Web 服务
    *   用于 XML 绑定的 Java 架构
        *   绑定
        *   注解
    *   冰山水下部分
        *   WSDL
        *   SOAP
    *   基于 XML 的 Web 服务的 Java API
        *   JAX-WS 模型
            *   Web 服务端点
            *   [注解](#index_split_006.



html#p441)
            *   生命周期与回调
            *   Web 服务上下文
        *   调用 Web 服务
    *   综合实践
        *   编写 CreditCard 类
        *   编写 CardValidator Web 服务
        *   使用 Maven 编译与打包
        *   部署到 GlassFish
        *   编写 Web 服务消费者
        *   生成消费者的构件并使用 Maven 打包
        *   运行主类
    *   总结
*   RESTful Web 服务
    *   理解 RESTful Web 服务
        *   资源
        *   URI
        *   表示
        *   WADL
        *   HTTP
            *   请求与响应
            *   HTTP 方法
            *   内容协商
            *   内容类型
            *   状态码
            *   缓存与条件请求
    *   RESTful Web 服务规范
        *   REST 简史
        *   JAX-RS 1.1
        *   JAX-RS 1.1 的新特性
        *   参考实现
    *   REST 方法
        *   从 Web 到 Web 服务
        *   网页浏览体验
        *   统一接口
        *   可寻址性
        *   连通性
        *   无状态性
    *   用于 RESTful Web 服务的 Java API
        *   JAX-RS 模型
        *   如何编写 REST 服务
        *   URI 定义
        *   提取参数
        *   消费与生产内容类型
        *   实体提供者
        *   方法或统一接口
        *   上下文信息
            *   请求头
            *   构建 URI
        *   异常处理
        *   生命周期
    *   综合实践
        *   编写 Book 实体
        *   编写 BookResource
            *   请求头
            *   创建新书
            *   根据 ID 获取图书
            *   删除图书
        *   使用 web.xml 进行配置
        *   使用 Maven 编译与打包
        *   部署到 GlassFish
        *   运行示例
    *   总结
*   索引***************************************************************************************************
