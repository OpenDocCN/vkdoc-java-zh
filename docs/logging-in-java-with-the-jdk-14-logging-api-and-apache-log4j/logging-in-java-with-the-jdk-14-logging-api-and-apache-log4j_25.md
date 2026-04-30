# 索引



### A

`activateOptions()` 方法，237，241
`AdvancedLogging.java`（清单），200-202，211
`AgeFilter.java`（清单），53
ALL 级别
    JDK 1.4，13
    log4j，136
`AlternateXML.java`（清单）
    代码，66-67
    对比 `XMLLogging.java`，66-67
`aMethod()` 方法，23
匿名日志记录器，19-20
`AnotherClass.java`（清单），241
Apache Log 标签库。*参见* Log 标签库
Apache log4j，高级日志记录，199-233
    示例应用，200-202
    JDBCAppender，202-212
        配置文件，205-206
        配置，203-204
        扩展，206-212
        用于存储日志信息的表，创建，204
    JMSAppender，212-223
        示例，217-223
        JMS 和 log4j，214-217
        JMS 基础，212-213
    记录到数据库。*参见* JDBCAppender（*此标题*）
    NTEventLogAppender，228-229
    SMTPAppender，229-231
    SocketAppender，223-228
        配置，225
        示例，226-228
        容错，224-225
    TelnetAppender，232
Apache log4j，基础，121-175
    log4j 的优势，121
    Appender 对象，149-166
        向日志记录器添加 Appender，150-151
        AsyncAppender，163-166
        基础，122，149-150，168
        ConsoleAppender，154
        DailyRollingFileAppender，158-160
        基于文件的日志记录示例，161-163
        FileAppender，154-156
        日志记录器与 Appender 的协作，151-152
        RollingFileAppender，156-158
        WriterAppender，152-153
    架构，122-125
    错误，版本 1.2.6，265
    配置，125-135
        基础，125-126
        默认初始化，127-131
        配置的动态加载，131
        使用 Servlet，132-133
        Tomcat 设置，133-135
        XML 样式配置，127
    示例，169-175
    Filter 对象，166-168
    安装，121-122
    介绍，8
    对比 JDK 1.4，309-312
        基础，299，309-310
        配置选项，310
        错误处理，312
        Filter 对象，311
        Formatter 和 Layout 对象，311
        Handler 和 Appender 对象，311
        位置信息选项，312
    JDK 兼容性，122
    Layout 对象，123，124，168
    Level 对象，124，136
    Logger 对象，136-145
        基础，122，136-139，168
        成功日志记录的条件，142
        配置方法，142-143
        示例，143-145
        日志信息与，139-142
    LogManager 对象，145-146
    消息诊断上下文 (MDC)，147-148
    嵌套诊断上下文 (NDC)，146-147
    ObjectRenderer，168-169
Apache log4j，创建自定义组件，235-283
    版本 1.2.6 中的错误，265
    从数据库配置，243-258
        示例，256-258
        通过配置加载器读取，245-252
        用于存储日志配置的表设计，244
        编写配置类，253-256
    自定义日志记录框架，259-270
        创建自定义级别类，259-260
        自定义级别的简单方法，265-270
        创建自定义 Logger，261-262
        生成自定义 Logger 工厂，262
        使用自定义日志记录组件，262-265
    错误处理，277-284
        基础，277-280
        自定义 ErrorHandler，280-281
        ErrorCode 接口，280
        配置 ErrorHandler，282-284
        编写自定义 ErrorHandler，280-282
        运行示例，284
    Filter 对象与，270-277
        过滤器链，276-277
        LevelRangeFilter，270-273
        SelectedLevelFilter，273-276
    WindowAppender，235-243
        架构，236-237
        特性，236
        实现，237-241
        测试，241-243
Apache log4j，格式化日志信息，177-198
    Layout 层次结构，177-178
    Layout 对象，178-198
        DateLayout，186-187
        HTMLLayout，187-192
        Layout 中的方法，179
        PatternLayout，194-198
        SimpleLayout，179-180
        TTCCLayout，180-186
        XMLLayout，192-194
`append()` 方法，153
append 属性，以及 FileHandler 参数，78
Append 属性，FileHandler，40
Appender 对象
    JDK 1.4 对比 log4j，311
    log4j，149-166
        向日志记录器添加 Appender，150-151
        AsyncAppender，163-166
        基础，122，124，149-150，168
        ConsoleAppender，154
        DailyRollingFileAppender，158-160
        基于文件的日志记录示例，161-163
        FileAppender，154-156
        对比 JDK 1.4，311
        日志记录器与 Appender 的协作，152
        RollingFileAppender，156-158
        WriterAppender，152-153
    TTCCLayout 实例与，186
    WindowAppender，235-243
        架构，236-237
        特性，236
        实现，237-241
        测试，241-243
Appender 属性，137
APPENDER_DEF，244，245
应用日志记录
    对比调试，2
    定义，2
    格式化日志信息，305-306
    介绍，1-10
        优势，3-4
        描述，1-3
        劣势，4-5
        有效的日志记录机制，5-7
        评估日志记录包，7
        基于 Java 的日志记录 API，7-9
应用状态，3
架构
    员工数据管理架构，102
    log4j，122-125
    日志记录，6-7
    log4j 和 JDK 1.4 的相似性，309
    WindowAppender，236-237
AsyncAppender，163-166，306
异步性
    异步日志记录，最佳实践，306
    JMS 与，212，219，224
`AsyncLogging.java`（清单），165-166
`async.xml`（清单），164-165



