


![](img/A978-1-4842-3015-2_CoverFigure.jpg)

![A978-1-4842-3015-2_CoverFigure.jpg](img/A978-1-4842-3015-2_CoverFigure.jpg)谢卡尔·古拉蒂与拉胡尔·夏尔马 著 《Java 单元测试：基于 JUnit 5》 使用 JUnit 5 进行测试驱动开发

![A429029_1_En_BookFrontmatter_Figa_HTML.png](img/A429029_1_En_BookFrontmatter_Figa_HTML.png)



本书作者引用的任何源代码或其他补充材料，读者均可通过本书在 GitHub 上的产品页面获取，网址为[`www.apress.com/978-1-4842-3014-5`](http://www.apress.com/978-1-4842-3014-5)。如需更详细信息，请访问[`http://www.apress.com/source-code`](http://www.apress.com/source-code)。ISBN 978-1-4842-3014-5e-ISBN 978-1-4842-3015-2 [`doi.org/10.1007/978-1-4842-3015-2`](https://doi.org/10.1007/978-1-4842-3015-2) 美国国会图书馆控制号：2017959333 © Shekhar Gulati, Rahul Sharma 2017 本作品受版权保护。出版商保留所有权利，无论涉及全部或部分材料，特别是翻译、重印、重用插图、朗诵、广播、微缩胶片复制或任何其他物理形式，以及电子改编、计算机软件或现在已知或以后开发的类似或不同方法的传输或信息存储与检索。本书中可能出现商标名称、标识和图像。我们仅在编辑风格中使用这些名称、标识和图像，以利于商标所有者，并无意侵犯商标权，而非在每次出现商标名称、标识或图像时都使用商标符号。本出版物中使用的商品名称、商标、服务标志和类似术语，即使未明确标识，也不应被视为对其是否受专有权利保护的看法。尽管本书中的建议和信息在出版时被认为是真实准确的，但作者、编辑和出版商均不对可能出现的任何错误或遗漏承担法律责任。出版商对本书所含内容不作任何明示或暗示的保证。本书采用无酸纸印刷，由 Springer Science+Business Media New York 在全球图书贸易中发行，地址：233 Spring Street, 6th Floor, New York, NY 10013。电话：1-800-SPRINGER，传真：(201) 348-4505，电子邮件：orders-ny@springer-sbm.com，或访问 www.springeronline.com。Apress Media, LLC 是加利福尼亚州的有限责任公司，其唯一成员（所有者）是 Springer Science + Business Media Finance Inc (SSBM Finance Inc)。SSBM Finance Inc 是特拉华州的一家公司。目录 第 1 章：以正确的方式构建软件 1 测试驱动开发 2 检测回归错误 3 保持系统设计简洁 3 测试级别 4 单元测试的好处 5 确定规范 6 提供早期错误检测 6 支持维护 6 改进设计 6 产品文档 6 优秀单元测试的特征 7 JUnit 简介 8 为什么我们需要新版本的 JUnit？ 8 JUnit 5 9 Java 8 入门 11 Lambda 表达式 12 Streams API 12 Optional<T> 14 项目设置 14 编写你的第一个测试 18 总结 23 第 2 章：理解 JUnit 5 核心 25 测试类 25 构造函数 27 使用@DisplayName 28 测试方法 29 测试方法中的参数 29 使用@DisplayName 30 断言 31 分组断言 34 错误与失败 36 JUnit 生命周期 API 37 @BeforeAll 38 @BeforeEach 38 @AfterEach 39 @AfterAll 39 测试执行 41 总结 44 第 3 章：使用 JUnit 5 开发应用程序 45 Bookstoread 应用程序 45 第一个功能 45 第二个功能 51 禁用测试 57 AssertJ 59 使用@DisplayName 60 第三个功能 62 嵌套测试 64 总结 66 第 4 章：依赖注入、模拟、测试特性与测试分组 67 依赖注入 67 功能：跟踪书架进度 72 缓存测试数据 76 使用 ExtensionContext 存储 77 功能：搜索书架 78 模拟 83 测试特性 86 按标签分组测试 87 总结 88 第 5 章：测试异常 89 上下文设置 89 原始方式：使用 try-catch-fail 89 JUnit 4 方式：使用@Test 注解和规则 API 91 JUnit 5 方式：使用 assertThrows 断言方法 92 在 JUnit 5 中使用 JUnit 4 的 ExpectedException 规则 93 异常处理扩展 94 处理测试超时 95 assertTimeoutPreemptively 96 重复测试 96 总结 98 第 6 章：集成工具 99 构建工具 99 Gradle 99 Maven 104 Ant 108 代码覆盖率 113 其他工具 117 总结 119 第 7 章：JUnit 5 扩展模型 121 JUnit 4 扩展模型 121 JUnit 5 扩展模型 122 测试生命周期回调 124 测试实例后处理 126 条件测试执行 127 参数解析 127 异常处理 129 注册扩展 129 JUnit 5 扩展 130 测试模板 130 参数化测试 133 总结 137 第 8 章：动态测试与从 JUnit 4 迁移 139 动态测试 139 JUnit 4 支持 141 使用 Vintage 引擎 143 迁移到 JUnit 5 144 总结 147 索引 149 内容概览 关于作者 xi   关于技术审阅者 xiii   第 1 章：以正确的方式构建软件 1   第 2 章：理解 JUnit 5 核心 25   第 3 章：使用 JUnit 5 开发应用程序 45   第 4 章：依赖注入、模拟、测试特性与测试分组 67   第 5 章：测试异常 89   第 6 章：集成工具 99   第 7 章：JUnit 5 扩展模型 121   第 8 章：动态测试与从 JUnit 4 迁移 139   索引 149   关于作者与关于技术审阅者 关于作者 关于技术审阅者



