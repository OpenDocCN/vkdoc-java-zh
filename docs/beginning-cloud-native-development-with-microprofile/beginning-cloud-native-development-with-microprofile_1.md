# 文档大纲



*   目录
*   关于作者
*   关于技术审阅者
*   致谢
*   引言
*   第 1 章：云计算简介
    *   云计算简介
        *   云计算示例
        *   云计算的优势
        *   云计算的基本特征
            *   按需自助服务
            *   广泛网络访问
            *   资源池化
            *   快速弹性
            *   可度量服务
    *   基于部署模型的云计算类型
        *   公有云
            *   公有云的优势
            *   公有云的缺点
        *   社区云
            *   社区云的优势
            *   社区云的局限性
        *   私有云
            *   私有云的优势
            *   私有云的劣势
        *   混合云
            *   混合云的优势
            *   混合云的缺点
    *   云计算的服务模型
        *   软件即服务（SaaS）
            *   优势
            *   劣势
            *   其工作原理
        *   平台即服务（PaaS）
            *   PaaS 的优势
            *   PaaS 的局限性
        *   基础设施即服务（IaaS）
            *   IaaS 的优势
            *   IaaS 的局限性
    *   Java 在云计算中的作用
    *   云计算的演进
    *   总结
*   第 2 章：云原生应用开发
    *   什么是云原生？
        *   云原生应用的特征
        *   云原生应用示例
    *   云原生应用的优势
        *   使用云原生应用的缺点
    *   云原生应用与传统应用对比
    *   云原生应用开发
    *   面向 Java 应用的托管服务
        *   云原生应用开发示例
        *   云原生应用开发是未来趋势
        *   云原生应用开发阶段
            *   持续集成/持续交付（CI/CD）
            *   基础设施即代码（IaC）
            *   微服务架构

*   容器化
            *   无服务器计算
            *   API 管理
            *   安全
            *   DevOps 文化转型
            *   可观测性
            *   可观测性的最佳实践
    *   总结
*   第 3 章：搭建开发环境
    *   先决条件
        *   硬件要求
            *   操作系统
            *   软件依赖
        *   安装 Java 开发工具包（JDK）
    *   使用 Maven
        *   安装 Maven
    *   Git
        *   安装 Git
        *   创建 GitHub 账户
        *   创建 GitHub 仓库
        *   将代码推送到 GitHub 仓库
    *   使用 Docker
        *   使用 Docker 的优势
        *   安装 Docker
        *   Docker Hub
            *   创建 Docker Hub 账户
    *   使用 Kubernetes
        *   安装 Kubernetes
        *   为 Kubernetes 配置 Docker
        *   创建 Kubernetes 集群
    *   使用 Zipkin
        *   安装 Zipkin
    *   集成开发环境（IDE）
    *   总结
*   第 4 章：构建 RESTful Web 服务
    *   REST：用于构建 Web 服务的架构风格
    *   Open Liberty 运行时简介
        *   Open Liberty 应用服务器的关键特性
        *   下载并安装 Open Liberty
        *   启动 Open Liberty 服务器
        *   打印 Open Liberty 服务器上的功能列表
        *   创建服务器实例
        *   停止 Open Liberty 服务器
        *   配置服务器
        *   在 Open Liberty 中创建 RESTful Web 服务
            *   探索项目结构
            *   理解 Open Liberty 项目的 Maven pom.xml 文件
            *   在 Open Liberty 中运行现有 Jakarta EE 项目
            *   server.xml 配置文件
            *   开发 REST 应用
            *   在 IntelliJ IDEA Ultimate Edition 中使用 Open Liberty 插件
                *   在 IntelliJ IDEA Ultimate Edition 中安装 Open Liberty 插件
        *   创建 RESTful API
            *   创建资源类
            *   创建服务类
            *   为服务类编写单元测试



*   使用 REST API 更新产品数据
                *   创建产品
                *   更新产品
                *   删除产品
    *   总结
*   第 5 章：微服务架构
    *   垂直扩展
    *   水平扩展
        *   垂直扩展与水平扩展对比
    *   面向服务架构的历史
    *   面向服务架构的类型
        *   面向服务的架构（SOA）
        *   什么是 Web 服务？
    *   什么是 SOAP？
        *   什么是 REST？
        *   REST 与 SOAP 对比
    *   单体架构
        *   微服务架构的演进
            *   微服务架构
        *   微服务架构的优势
    *   微服务架构的劣势
        *   设计考量
        *   在云原生应用中使用微服务
        *   云原生十二要素应用
    *   什么是虚拟机？
    *   什么是容器？
        *   什么是基于容器的应用？
        *   什么是无服务器应用？
        *   无服务器计算用于哪些场景？
        *   无服务器计算的优势
        *   无服务器计算的劣势
        *   流行的无服务器平台
    *   无服务器计算平台的主要组件
        *   无服务器应用与容器对比
            *   开发便利性
                *   基于容器的应用
                *   无服务器应用
            *   部署便利性
                *   基于容器的应用
                *   无服务器应用
            *   云计算成本
                *   基于容器的应用
                *   无服务器应用
            *   可移植性
                *   基于容器的应用
                *   无服务器应用
            *   可扩展性与性能
                *   基于容器的应用
                *   无服务器应用
            *   安全性考量
                *   无服务器应用
                *   基于容器的应用
    *   微服务部署模式
        *   关键考量
        *   微服务部署策略与模式

*   金丝雀部署
                *   金丝雀部署的陷阱
            *   蓝绿部署
                *   蓝绿部署策略的陷阱
            *   暗发布
                *   暗发布的陷阱
            *   分阶段发布
                *   分阶段发布的陷阱
        *   微服务管理
    *   总结
*   第 6 章：Eclipse MicroProfile 框架
    *   什么是 Eclipse MicroProfile 框架？
        *   MicroProfile 中的功能层
        *   Eclipse MicroProfile 框架的流行实现
        *   MicroProfile Config
            *   Maven 依赖
            *   在 Open Liberty 中启用 MicroProfile Config
            *   使用 MicroProfile Config
            *   定义配置属性
            *   测试配置组件
            *   创建 ConfigSource
            *   注册 ConfigSource
            *   访问配置数据
            *   访问配置元数据
            *   为配置属性指定默认值
            *   ConfigProperty 中的类型转换
            *   使用可选的 ConfigProperty
            *   将配置数据转换为 POJO
            *   以编程方式查找配置数据
    *   MicroProfile 健康检查规范
    *   MicroProfile 容错组件
        *   容错组件的关键特性
        *   容错 API
    *   总结
*   第 7 章：MicroProfile 框架——第 2 部分
    *   MicroProfile 指标规范
        *   指标类型
        *   指标组件
        *   指标注解
        *   Maven 依赖
        *   使用 @Timed 跟踪响应时间
        *   跟踪调用次数
        *   跟踪内存使用情况
        *   MetricRegistry
        *   创建自定义指标
            *   暴露指标
                *   JMX
                *   JSON
                *   Prometheus
    *   使用 Open Tracing
        *   分布式追踪
        *   它是如何工作的？
            *   Jakarta RESTful Web 服务中的 MicroProfile OpenTracing
            *   显式启用 OpenTracing
            *   注入自定义 Tracer 对象



*   使用 Jakarta RESTful Web 服务
    *   使用 MicroProfile OpenAPI 规范
        *   OpenAPI
        *   MicroProfile OpenAPI 规范的功能
        *   生成 OpenAPI 文档
        *   在你的项目中使用 MicroProfile OpenAPI
        *   MicroProfile OpenAPI 注解
    *   总结
*   第 8 章：MicroProfile JSON Web Token 与 Jakarta Security
    *   微服务的安全最佳实践
    *   什么是 JSON Web Token（JWT）？
        *   JSON Web Token 的使用场景
        *   身份认证
        *   授权
        *   会话信息
        *   基于声明的身份标识
        *   信息交换
        *   联合
    *   创建并签名 JWT
        *   测试应用程序
    *   验证与校验 JWT
    *   在微服务环境中使用 JWT
        *   保护 RESTful 应用程序
        *   保护 REST 资源
        *   @RolesAllowed 注解
        *   @Claim 注解
        *   @HeaderParam 注解
    *   配置 JWK 集或 PEM 编码的公钥
        *   PEM 格式的公钥密码学标准 #8
        *   JSON Web Key（JWK）
        *   JSON Web Key Set（JWKS）
    *   Jakarta EE 安全规范
        *   使用 Jakarta Security 保护 Servlet
    *   MicroProfile REST 客户端
        *   @RegisterClient 注解
        *   配置你的 REST 客户端
        *   REST 客户端接口
        *   使用 MicroProfile OpenAPI 编写 RESTful API 文档
        *   什么是 OpenAPI 规范？
        *   什么是 MicroProfile OpenAPI？
            *   使用 MicroProfile OpenAPI 为 REST API 编写文档
            *   查看生成的文档
    *   总结
*   第 9 章：使用 Kubernetes 容器化微服务
    *   什么是容器？
    *   为什么使用容器？
    *   什么是容器编排？
        *   使用容器编排的好处
    *   理解 Kubernetes 术语
    *   Kubernetes 的关键特性
    *   使用 Kubernetes
    *   使用 Docker 容器
        *   在 Docker 中运行 MicroProfile 应用程序
        *   将 Docker 镜像推送到 Docker Hub
        *   扩展你的部署
    *   总结
*   附录 A：Maven 简介
    *   Maven 的特性
    *   Maven 生命周期
    *   Maven 的优势
    *   Maven 如何使开发流程受益？
        *   Maven 架构
            *   什么是 Maven 仓库？
            *   使用 Maven 创建 Java 项目
            *   在你的项目中使用 Maven
        *   实用的 Maven 命令
            *   Snapshot 和 Release 有什么区别？
*   附录 B：使用 Maven 创建 Open Liberty 项目
    *   使用 Maven 生成 Web 项目
*   附录 C：安装 Open Liberty 工具
*   索引**
