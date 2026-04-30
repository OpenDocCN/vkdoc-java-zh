# 文档大纲



*   《Terracotta 权威指南：为 Spring、Hibernate 和 POJO 可扩展性集群 JVM》
*   内容概览
*   目录
*   引言
    *   本书结构
    *   先决条件
    *   联系作者
*   理论与基础：建立共识
    *   Terracotta 框架定义
        *   网络附加存储与 Terracotta 的相似性
            *   抽象层
            *   添加网络文件存储
            *   与 Terracotta 的类比
        *   内存位置的透明性
        *   透明性与集群的结合
    *   遵循 Java 内存模型
    *   作为服务的优势
        *   高可用性
        *   可扩展性
        *   避免瓶颈
    *   使用场景
        *   分布式缓存
        *   数据库卸载
        *   会话复制
        *   工作负载分区
    *   本章小结
*   Terracotta 发展史
    *   扩展方法
        *   扩展数据库
        *   内存复制
        *   殊途同归：分区
    *   Terracotta 的起源：来自网络巨兽
        *   应用即巨型计算机
    *   Terracotta 使 L2 通用化
        *   原始 L2 的侵入性
        *   我们需要更高的透明性
        *   透明性等于通用性
        *   兼具高可用性的透明扩展
        *   再探透明集群服务
        *   透明性带来更高的可扩展性
    *   集群应用服务器与集群缓存
    *   本章小结
*   快速上手 Terracotta
    *   Hello Clustered World
        *   集群化 HelloClusteredWorld
        *   HelloClusteredWorld 为何能工作
        *   Terracotta 的替代方案
    *   Terracotta 概览
        *   下载与安装 Terracotta
        *   Terracotta 安装目录结构
        *   Terracotta 脚本
        *   在 Unix 中逐步实现 Hello Clustered World
            *   创建项目骨架
            *   创建 HelloClusteredWorld.java
            *   创建 Terracotta 配置
            *   以持久模式运行 Terracotta 服务器
            *   配置 Terracotta 客户端
            *   配置根对象
            *   配置字节码增强
            *   配置锁
            *   审查配置
            *   启动 Terracotta 服务器
            *   启动两个 HelloClusteredWorld 集群实例
            *   在 Terracotta 管理控制台中查看运行时数据
            *   高可用性
            *   堆持久性
        *   在 Eclipse 中逐步实现 Hello Clustered World
    *   对每位开发者的启示
    *   本章小结
*   POJO 集群
    *   关于对象与根对象的一切
    *   集群对象
    *   虚拟堆与内存管理
    *   分布式垃圾回收
    *   管理对象变更与协调线程
    *   锁
        *   自动锁与命名锁
        *   锁级别
        *   分布式 wait()与 notify()
    *   事务
    *   分布式方法调用
    *   透明性与字节码增强
    *   可移植性
    *   启动 JAR 类
    *   物理管理与逻辑管理对象
    *   不可移植与逻辑管理类
    *   可移植性上下文
        *   字段变更
        *   逻辑操作
        *   对象图遍历
    *   瞬态字段与加载时行为
        *   加载时初始化瞬态字段
    *   逐步实现集群 POJO
        *   我们所说的 POJO 是什么？
        *   什么是集群 POJO？
        *   为何使用集群 POJO？
    *   集群 POJO 示例
        *   示例类
            *   ProductImpl 类
            *   Catalog 类
            *   ShoppingCartImpl 类
            *   ActiveShoppingCarts 类
            *   Roots 类
            *   Main 类
        *   示例的集群需求
        *   示例的配置
        *   运行示例
        *   刚刚发生了什么
        *   示例展示了什么
    *   本章小结
*   缓存
    *   缓存的痛点
        *   大数据集的痛点
        *   数据过时的痛点
        *   数据重复的痛点
    *   Terracotta 如何提供帮助
    *   一个简单的缓存示例
    *   分布式缓存



*   透明分布式缓存
    *   使用映射进行缓存
        *   未排序映射
        *   已排序映射
        *   自动锁定集合
            *   下载并安装自动锁定集合 TIM
            *   使用自动锁定集合
        *   批量加载映射
        *   锁定与映射
        *   映射的映射
    *   深入探讨缓存
        *   逐出与过期
        *   持久化
        *   分布式缓存
        *   分区数据
        *   利用 Terracotta
    *   使用 Ehcache 进行缓存
        *   重写缓存示例
        *   将 Ehcache 与 Terracotta 结合使用
            *   增强功能与配置
            *   下载并安装 Ehcache 集成
            *   将 Ehcache 与 Terracotta 结合使用
    *   总结
*   使用 Terracotta 的 Hibernate：利用 Terracotta 卸载数据库负载
    *   对象关系映射
    *   Hibernate 如何工作？
    *   典型的 Hibernate 调用
    *   使用 Terracotta 改进 Hibernate
    *   Terracotta 如何提升 Hibernate 性能
        *   提升 Hibernate 应用性能的基本步骤
        *   基准性能
        *   添加 Hibernate 以节省三次数据库调用
        *   启用二级缓存以节省五次 SQL 调用
        *   对 Session 对象进行集群
        *   对增强功能进行基准测试
    *   配置 Hibernate 以与 Terracotta 协同工作
        *   配置二级缓存
            *   配置 Ehcache
            *   在 Hibernate 中启用 Ehcache 二级缓存
            *   配置 Terracotta
        *   为分离实例配置 Terracotta
            *   重构你的应用
            *   配置 Terracotta 以使用 Hibernate 集成模块
            *   存储分离实例
            *   重新附加分离实例
            *   延伸阅读建议
    *   调试、测试与调优注意事项
        *   验证 Ehcache 配置集群
        *   调优二级缓存
        *   调优分离实例
    *   总结
*   使用 Terracotta 扩展 HTTP 会话
    *   什么是 HTTP 会话？
        *   服务端状态管理
        *   陈旧数据的自动超时
    *   手动失效以清除数据
        *   协调状态修改的事件
        *   会话状态持久化
    *   分布式 HTTP 会话
        *   使用 HTTP 会话的动机
            *   从其他节点访问会话状态
            *   动态水平扩展以应对流量高峰
            *   服务器故障时的高可用性
        *   HTTP 会话的替代方案
            *   分布式缓存
            *   作为中央存储的数据库
    *   使用 Terracotta 的分布式 HTTP 会话
        *   Terracotta 会话如何工作
            *   透明地嵌入 Web 容器
            *   适当的锁粒度与隔离性
        *   优势
            *   无需序列化
            *   滚动升级
            *   细粒度变更
            *   同步线性可扩展性与高可用性
            *   每次更改后无需设置属性
            *   易于设置
    *   使用 Terracotta 和 HTTP 会话
        *   共享会话状态
            *   打包并配置 Web 应用
            *   使用 Terracotta 会话配置器安装并运行应用
            *   尝试示例
        *   具有结构化会话属性的 HTTP 会话
            *   打包并配置 Web 应用
            *   手动配置示例并将其安装到 Tomcat 中
            *   验证细粒度变更检测
    *   理解使用 Terracotta 的 HTTP 会话
        *   支持的平台
        *   架构考量
            *   轮询负载均衡器的影响
            *   使用粘性负载均衡器
            *   运行 Terracotta 集群
    *   总结
*   Spring 集群
    *   示例：无数据库的 Spring Bean
    *   在单个 JVM 上运行示例
    *   使用 Terracotta 集群运行示例
    *   关于 DI 和 Terracotta 的说明
    *   扩展 Spring Bean
    *   对 Spring Bean 进行集群
    *   使用集群 Spring Bean 运行
    *   用于 Spring 的 Terracotta 会话集群
    *   总结
*   集成模块
    *   通过配置实现集群
    *   使用 TIM
        *   对同步映射进行集群
        *   导入 TIM
        *   [模块仓库](#index_split_003.



html#p229)
        *   陶土锻造
    *   创建 TIM
        *   TIM 结构解析
            *   创建清单文件
            *   Terracotta 配置
        *   创建简单 TIM
        *   使用 Maven 创建 TIM
            *   模块版本管理
        *   在 TIM 中包含代码
            *   定义 Bundle Activator
            *   通过代码进行配置
            *   类替换与修改
            *   使用 Maven 创建包含代码的 TIM
    *   总结
*   线程协调
    *   Terracotta 与线程协调
    *   集群线程
        *   使用并发原语进行线程协调
        *   使用 java.util.concurrent 包进行线程协调
        *   线程协调与 JMX 集群事件
        *   使用队列进行线程协调
    *   总结
*   使用 Terracotta 进行网格计算
    *   什么是网格？
        *   计算网格与数据网格
        *   网格如何处理可伸缩性
        *   网格如何处理故障转移和高可用性
        *   使用场景
    *   主/从模式介绍
    *   Java 中的主/从模式
        *   使用 Java 语言的线程协调原语
        *   使用 java.util.concurrent 抽象
        *   使用 CommonJ Work Manager 规范
    *   入门：简单的单工作队列实现
        *   实现单 JVM 的简单主/从容器
            *   我的工作怎么办？
        *   使用 Terracotta 集群化我们的主/从模式
    *   应对实际挑战
    *   针对大工作负载、路由和工作故障转移的重构
        *   最小化争用并最大化引用局部性
        *   路由策略
        *   处理工作失败与恢复
        *   重构 Worker
        *   更新 Terracotta 配置
        *   使用主/从系统
        *   运行主/从系统
    *   重构工作批处理、Worker 故障转移和动态 Worker 管理
        *   工作批处理
        *   动态 Worker 管理
        *   Worker 故障转移
        *   如何使用和运行容错的主/从系统
    *   构建分布式网络爬虫
    *   何时不使用主/从模式
    *   总结

*   可视化应用程序
    *   观察应用程序
    *   可视化工具与架构
    *   示例库存管理应用程序
        *   修改以生成负载
            *   复制示例
            *   Pounder 方法
            *   调整锁
            *   使用独立根
            *   更新 CLI
            *   更新 Terracotta 配置
            *   编译并运行应用程序
        *   获取性能快照
    *   潜在瓶颈
        *   回顾主板
        *   Terracotta 服务器内部
            *   SEDA
            *   Terracotta 的 SEDA 管道
        *   寻找局部性问题
        *   inventory.pounder 中的特定瓶颈
            *   广播变更
            *   锁获取
    *   可视化瓶颈
    *   管理生产环境
    *   总结
*   索引
