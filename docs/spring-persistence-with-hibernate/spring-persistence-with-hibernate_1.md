# 文档大纲



*   扉页
*   版权页
*   内容速览
*   目录
*   关于作者
*   关于技术审校
*   致谢
*   前言
*   无标题
*   第 1 章 使用 Spring、Hibernate 和模式构建应用程序架构
    *   一致性方法的好处
        *   依赖注入的重要性
    *   协同合作
        *   Spring 与 Hibernate 的成功故事
        *   更好的集成方法
        *   架构应用程序的最佳实践
            *   持久化层的层次结构
                *   领域模型
                *   数据访问对象（DAO）层
                *   服务外观
            *   利用声明式事务
                *   理解面向切面编程（AOP）
                *   简化事务
                *   面向接口编程的好处
                    *   测试你的持久化层
            *   高级特性与性能调优
                *   Hibernate Search
                *   构建 REST Web 服务
    *   其他持久化设计模式
        *   模板模式
        *   活动记录模式
    *   本章小结
*   第 2 章 Spring 基础
    *   探索 Spring 的架构
        *   应用上下文
        *   Bean，Bean，神奇的果实
        *   Spring 生命周期
        *   理解 Bean 的作用域
    *   依赖注入与控制反转
        *   基于 Setter 的依赖注入
        *   基于构造函数的依赖注入
        *   实例协作
        *   面向接口编程
        *   通过自动装配实现依赖注入
        *   基于注解的依赖注入
        *   设置后即可遗忘！
    *   使用 AOP 和拦截器注入代码
    *   本章小结
*   第 3 章 基本应用程序设置
    *   使用 Maven 进行应用程序管理
        *   托管依赖
        *   标准目录结构
        *   POM 解析
    *   Spring 配置
        *   命名空间支持
        *   外部化属性配置
        *   组件扫描
        *   导入语句
    *   数据库集成
        *   JDBC 支持
        *   与 JNDI 集成
    *   Web 应用程序配置
        *   Servlet 定义
        *   Spring MVC
    *   本章小结
*   第 4 章 使用 Hibernate 进行持久化
    *   Java 中数据库持久化的演进
        *   EJB、JDO 与 JPA
        *   Hibernate 的定位
    *   JPA 接口层次结构
    *   艺术画廊领域模型与 DAO 结构
        *   一个带有@Entity 注解的 POJO
        *   使用泛型简化 DAO 模式
        *   JPA 实体的生命周期
    *   JPA 配置
        *   最简 JPA 设置
        *   Spring 集成
    *   本章小结
*   第 5 章 领域模型基础
    *   理解关联关系
    *   构建领域模型
        *   约定优于配置
        *   管理实体标识符
        *   使用级联选项建立数据关系
        *   添加二级缓存
        *   在 Hibernate 中使用多态
    *   本章小结
*   第 6 章 DAO 与查询
    *   一个基本的 Hibernate DAO 实现
        *   构建 DAO
        *   使用 Spring 的 Hibernate 支持类
        *   使用 HibernateTemplate 启用查询缓存
        *   摆脱模板
    *   在 Hibernate 中进行查询
        *   加载实体
        *   查询特定类型
        *   使用命名参数
        *   使用核心 Hibernate 进行查询
        *   使用命名查询
        *   处理多态查询
    *   使用 Hibernate 持久化数据
        *   保存和更新数据
        *   处理二进制数据
        *   理解 Criteria API 的优势
        *   使用 JPA 2.0 Criteria API
    *   本章小结
*   第 7 章 事务管理
    *   ACID 的乐趣
    *   理解隔离级别
        *   可串行化
        *   可重复读
        *   读已提交
        *   读未提交
    *   控制 ACID 反流
        *   平台事务管理
        *   声明式事务管理
            *   事务注解
            *   通过 XML 进行声明式事务
        *   编程式事务管理



*   事务示例
        *   创建批处理应用程序
        *   使用两个数据源
    *   小结
*   第 8 章 高效测试
    *   单元测试、集成测试与功能测试
    *   使用 JUnit 进行高效测试
        *   使用模拟对象进行单元测试
        *   Spring 依赖注入与测试
        *   使用数据库进行测试
    *   小结
*   第 9 章 最佳实践与高级技术
    *   懒加载问题
        *   N+1 次查询问题
            *   减少懒加载映射
            *   批量处理以提升性能
        *   懒初始化异常
            *   现在晚点关闭：让 EntityManager 保持打开状态
            *   应用 Open EntityManager 过滤器
    *   缓存
        *   集成缓存实现
            *   确定缓存规则
            *   配置缓存区域
        *   缓存查询
        *   集群配置中的缓存
            *   集群缓存与复制机制
            *   配置复制
    *   小结
*   第 10 章 集成框架
    *   使用 Spring 构建 RESTful Web 服务
        *   名词、动词与内容类型
        *   序列化对象图
        *   使用令人畏惧的 DTO 模式
            *   引导 Dozer
            *   构建 DTO 层
            *   使用 Spring 配置 Dozer
            *   执行映射
        *   利用 Spring 3 的 REST 支持
        *   使用 Spring OXM 编组数据
        *   处理并发
            *   乐观锁
            *   悲观锁
    *   全文搜索
        *   Lucene 简介
            *   使用 Lucene 建立索引
            *   使用 Lucene 进行查询
        *   Hibernate Search 简介
            *   集成 Hibernate Search
            *   添加 Hibernate Search 注解
        *   保持 Lucene 与 Hibernate 同步
        *   构建领域特定搜索
    *   小结
*   第 11 章 GORM 与 Grails
    *   Groovy 速成
        *   放松类型限制
        *   GStrings——增强版字符串
        *   Groovy 中的默认构造函数
        *   Groovy 中的闭包

*   让 Grails 运行起来
        *   安装 Grails
        *   创建 Grails 应用程序
        *   配置应用程序
        *   配置数据源
        *   映射 URL
    *   定义 Grails 领域模型
        *   添加约束与验证
        *   定义关联与属性
        *   自定义领域类的 Hibernate 映射
    *   使用 Active Record 作为 DAO 的替代方案
    *   探究 GORM 的内部机制
        *   使用动态查找器方法
        *   创建高级查询方法
        *   使用 Criteria API
    *   在 Grails 中处理关联
    *   脚手架与构建 Grails 应用程序
    *   在 Grails 中定义事务服务层
    *   小结
*   第 12 章 Spring Roo
    *   Roo 是什么，不是什么
    *   使用 Roo 创建领域模型
        *   Roo 入门
        *   创建新项目
        *   添加实体
        *   添加字段
        *   探索自动生成的测试基础设施
        *   映射关联
        *   建模继承
        *   添加 Spring MVC
        *   添加服务层与 DAO
    *   现在你看到我，现在你找不到我——移除 Roo
    *   小结
*   索引
