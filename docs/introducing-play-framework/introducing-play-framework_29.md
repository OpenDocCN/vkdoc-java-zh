# 文档大纲



*   目录
*   关于作者
*   关于技术审校
*   前言
*   第 1 章：Play 2 入门
    *   准备工作
    *   安装
        *   前提条件
        *   安装 sbt
        *   安装 conscript
        *   安装 Giter8
        *   设置 Play
        *   使用 Play 示例项目
        *   使用 sbt
            *   使用 sbt 安装 Java 项目
            *   使用 sbt 安装 Scala 项目
    *   创建你的第一个项目
        *   app
        *   conf
        *   build.sbt
        *   project
        *   public
        *   lib
        *   test
    *   配置 Play 以适配你偏好的 IDE
        *   在 Eclipse 中设置
        *   在 IntelliJ 中设置
    *   Hello World 应用
        *   配置
        *   控制器与视图
            *   视图
            *   控制器
            *   增强视图
    *   测试 Play 应用
        *   测试视图
        *   测试控制器
*   第 2 章：构建系统
    *   Scala 构建工具/简单构建工具
    *   核心原则
    *   sbt 的优势
    *   项目结构
    *   使用 sbt
        *   设置定义
        *   解析器
        *   完整的 build.sbt
        *   完整的 plugins.sbt
    *   SBT 命令快速回顾
*   第 3 章：Play 控制器与 HTTP 路由
    *   MVC 编程模型
        *   模型
        *   视图
        *   控制器
    *   HTTP 路由
        *   静态定义
        *   URL 中的动态部分
        *   传递固定值
        *   可选参数
    *   使用 application.conf 进行应用配置
    *   控制器
    *   完成 Bookshop 控制器
        *   saveComment 方法
        *   测试 saveComment 动作
    *   模型
    *   作用域对象
        *   会话作用域
        *   Flash 作用域
*   第 4 章：Play 视图与 Scala 模板
    *   复合视图
    *   设计通用模板
    *   代码片段模板基础
        *   注释
        *   模板参数
        *   导入语句
        *   遍历列表
        *   遍历映射
        *   If 块
        *   转义动态内容
*   第 5 章：并发与异步编程
    *   什么是并发？
        *   执行器
        *   示例 1：使用 Runnable
        *   示例 2：使用 Callable
    *   使用 Play 进行异步编程
    *   编写异步应用
    *   配置异步定时任务
        *   Akka 基础
*   第 6 章：Web 服务、JSON 与 XML
    *   消费 Web 服务
    *   处理大型响应
    *   处理 JSON
        *   消费 JSON 请求
        *   生成 JSON 响应
    *   处理 XML
        *   示例 1：简单 XML 解析
        *   示例 2：使用 JAXB 进行 XML 解析
*   第 7 章：访问数据库
    *   配置数据库支持
    *   使用 ORM
        *   ORM 概念
        *   关键术语
            *   一对多
            *   多对一
            *   多对多
        *   关系方向
    *   配置 JPA
    *   在 Play 中使用 Ebean
        *   Ebean 查询
        *   Ebean 中常用的 Select 查询结构
            *   where()
            *   eq()
            *   like()
            *   orderBy()
            *   findUnique()
            *   findList()
            *   [LIMIT {最大行数} [ OFFSET {起始行} ]](#index_split_001.html#p138)
            *   查询接口
        *   使用 RawSql
        *   Ebean 中的关系
*   第 8 章：完整示例
*   第 9 章：使用 Play 模块
    *   创建模块
    *   第三方模块
*   第 10 章：应用设置与错误处理
    *   过滤器
    *   动作组合
    *   错误处理器
        *   客户端错误
        *   服务器错误
    *   Play 2.6.x 之前的全局设置方式
*   第 11 章：使用缓存
    *   配置 Caffeine
        *   将 Caffeine 添加到项目
    *   配置 EhCache
    *   使用缓存 API
*   第 12 章：生产环境部署
    *   为 Play 配置 Apache httpd
    *   使用 mod_proxy_balancer 进行负载均衡
    *   使用 Nginx 配置 Play
*   索引
