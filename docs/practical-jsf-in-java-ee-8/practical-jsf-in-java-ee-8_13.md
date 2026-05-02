# 6.  配置文件

Michael Müller¹

(1)德国，北莱茵-威斯特法伦州，布吕尔

当我们创建 TinyCalculator 时，NetBeans 生成了一些配置文件。根据应用程序的不同，Java EE 会使用这些文件或其他文件来配置其行为。本章概述了这些配置文件。

## pom.xml

POM 代表项目对象模型。这个文件与 Maven 相关，如前所述，这超出了本书的范围。我只能解释最重要的内容，以传达项目中使用的库的信息。这将使你能够将这些库包含在你选择的构建工具中。

因此，我只会列出 POM 的片段——比如清单 6-1，它是 TinyCalculator 项目的一部分。POM 位于每个项目的根文件夹中。

###### 清单 6-1 Maven POM 中的依赖项

```
1   ...
2   <dependency>
3       <groupId>javax</groupId>
4       <artifactId>javaee-web-api</artifactId>
5       <version>7.0</version>
6       <scope>provided</scope>
7   </dependency>
8   ...
```

考虑清单 6-1 的第 5 行：我建议下载 NetBeans Java EE 捆绑包。在撰写本文时，NetBeans 8.2 仍然是最新的发布版本。它捆绑了 GlassFish 4.1，一个符合 Java EE 7 标准的服务器。因此，我之前显示的版本仍然是 7。

在继续下一个应用程序（称为 Books）之前，我将向你展示如何更新服务器。对于 Java EE 8，你需要为版本号提供 8.0。



## web.xml

web.xml 是 Servlet 的*部署描述符*文件。JSF 本身是通过 Java Servlet 技术实现的。

该配置文件位于 `ProjectRoot/src/main/webapp/WEB-INF` 文件夹中。在 NetBeans 的项目树中，你可以在 `Web Pages/WEB-INF` 文件夹下找到它。`Web Pages` 是相应文件夹的别名。

清单 6-2 展示了 TinyCalculator 的 web.xml 文件。

###### 清单 6-2 web.xml

```
 1   <?xml version="1.0" encoding="UTF-8"?>
 2   <web-app version="3.1"
 3       xmlns="http://xmlns.jcp.org/xml/ns/javaee"
 4       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
 5       xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee
 6       http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd">

 8       <context-param>
 9           <param-name>javax.faces.PROJECT_STAGE</param-name>
10           <param-value>Development</param-value>
11       </context-param>

13       <servlet>
14           <servlet-name>Faces Servlet</servlet-name>
15           <servlet-class>javax.faces.webapp.FacesServlet</servlet-class>
16           <load-on-startup>1</load-on-startup>
17       </servlet>

19       <servlet-mapping>
20           <servlet-name>Faces Servlet</servlet-name>
21           <url-pattern>*.xhtml</url-pattern>
22       </servlet-mapping>

24       <session-config>
25           <session-timeout>
26               30
27           </session-timeout>
28       </session-config>

30       <welcome-file-list>
31           <welcome-file>index.xhtml</welcome-file>
32       </welcome-file-list>
33   </web-app>
```

包含版本和命名空间的头信息属于 Java EE 7 版本，它定义了 Servlet 3.1。许多 Java EE 8 应用仍然使用相同的描述符文件。Java EE 8 的描述符仅在版本号上有所不同（第 2 行使用 4.0 而不是 3.1，第 6 行使用 `web-app_4_0.xsd`）。这个版本 4 指的是 Servlet 4.0。遗憾的是，GlassFish 5.0（以及 5.0.1 beta）在使用新版本时无法正确解析某些后台 Bean。你可以使用旧的描述符格式（这对大多数 Java EE 8 特性来说没问题），或者需要安装一个不同的应用服务器，比如 Payara。在进入 Books 应用之前，我会向你展示如何操作。

表 6-1 描述了描述符文件的一些重要元素。

###### 表 6-1 描述符文件

| 元素 | 描述 |
| --- | --- |
| context-param | 定义一个参数，由名称-值对组成。参数 `javax.faces.PROJECT_STAGE` 用于区分开发阶段和生产阶段。在开发阶段，JSF 提供一些调试辅助功能，例如自动消息。此特性会显示所有未被相应消息元素显示的 JSF 消息。 |
| servlet | 为 Servlet 类定义名称和启动顺序。如果定义了多个 Servlet，并且存在依赖关系，则需要此顺序。 |
| servlet-mapping | 将 URL 模式映射到由此名称定义的 Servlet。在 TinyCalculator 的情况下，没有定义特殊的上下文，因此应用在上下文 `//Server:port/TinyCalculator/....` 下运行。在此上下文中，所有以 `.xhtml` 结尾的 URL 都由该应用处理。 |
| session-config | 会话上下文的配置设置。这里定义了一个会话超时（以分钟为单位）的子元素。请记住，客户端可能随时停止发送请求，通常不会终止应用。因此，超时设置非常重要！ |
| welcome-file-list | 定义一个或多个欢迎文件。在此标签内，开发者需要为每个欢迎文件放置一个 `welcome-file` 标签。服务器从第一个条目开始，搜索 URI `http(s)://server:port/appContext/welcomeFile`，直到找到合适的文件来显示。欢迎文件可以包含应用内的路径。如果没有定义欢迎文件，GlassFish 会搜索 `index.html`。 |

web.xml 文件还用于许多其他设置。我将在后续讨论这些设置用于具体应用时再作介绍，例如 Alumni 应用的安全设置。

## faces-config.xml

到目前为止，TinyCalculator 中还没有 `faces-config.xml`。但某些配置需要这个文件，所以我将在讨论特定应用时介绍它。例如，Books 应用使用了由 `faces-config.xml` 文件配置的本地化属性文件。或者，如第 5 章所述，此配置可用于覆盖导航规则。

## beans.xml

从 Java EE 6 开始，上下文和依赖注入（CDI）成为 Java 企业版的一部分。在早期，CDI 需要通过 `beans.xml` 文件来启用，如清单 6-3 所示。为此，`beans.xml` 可以完全为空，或者仅包含 XML 命名空间声明。

###### 清单 6-3 beans.xml

```
1   <?xml version="1.0" encoding="UTF-8"?>
2   <beans xmlns="http://xmlns.jcp.org/xml/ns/javaee"
3          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
4          xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee
5          http://xmlns.jcp.org/xml/ns/javaee/beans_1_1.xsd"
6          bean-discovery-mode="annotated">
7   </beans>
```

从 Java EE 7 开始，CDI 默认启用，因此在 Java EE 8 中通常不需要此文件。你可以使用它来控制 Bean 发现模式，但这不在本书的讨论范围内。

我提到这个文件只是为了完整性，但你可能仍会遇到需要为 Java EE 6 环境进行开发的情况。

## persistence.xml

此文件位于 `projectRoot/src/main/resources/META-INF` 文件夹中，用于配置 Java 持久化 API。我们需要它来将信息存储到数据库中。它将在第 12 章中首次使用并进行讨论。

## glassfish-resources.xml

顾名思义，这是一个特定于供应商的配置文件。本书中的项目是使用 GlassFish 开发的，因此你会在本书中找到 `glassfish-resources.xml`。此文件描述了诸如 JDBC 连接之类的资源。

根据你使用的应用服务器，你的配置可能会略有不同。通常，将内容迁移到你自己的环境中使用应该没有问题。

我将在第 10 章讨论 Books 应用时介绍此文件的设置，该应用使用 Java 持久化 API 来存储数据。

## glassfish-web.xml

到目前为止，TinyCalculator 项目中还没有 `glassfish-web.xml` 文件。在前面的章节中，我讨论了上下文路径。默认情况下，此上下文与项目名称相同。但假设我们想要更改它：

1.  使用 NetBeans，打开 TinyCalculator 项目。
2.  在项目树中右键单击项目，选择“属性”，打开项目属性。
3.  单击“运行”类别，将上下文路径更改为 `Calculator`。
4.  单击“确定”。

NetBeans 会创建一个 `glassfish-web.xml` 文件，位于 `WEB-INF` 文件夹中。

或者，也可以使用（文本）编辑器创建此文件，如清单 6-4 所示。

###### 清单 6-4 glassfish-web.xml

```
 1   <?xml version="1.0" encoding="UTF-8"?>
 2   <!DOCTYPE glassfish-web-app PUBLIC
 3   "-//GlassFish.org//DTD GlassFish Application Server 3.1 Servlet 3.0//EN"
 4   "http://glassfish.org/dtds/glassfish-web-app_3_0-1.dtd">
 5   <glassfish-web-app error-url="">

 7       <context-root>/Calculator</context-root>

 9       <!-- 为简洁起见，省略了其他上下文 -->
10   </glassfish-web-app>
```

为简洁起见，我省略了一些配置信息。通过 `context-root`，默认的上下文路径被覆盖了。

`glassfish-web.xml` 还用于其他一些配置。我将在讨论具体应用时介绍它们。

###### 注意

在现有项目中，你可能会找到一个 `sun-web.xml` 文件。这是与 `glassfish-web.xml` 相同配置的另一个（更旧的）名称。



## 其他文件

根据项目的不同，可能还会存在其他配置文件——例如，指向非标准本地 Maven 仓库路径的 `settings.xml`，或 `nb-configuration.xml`。这两个文件由 NetBeans 管理，我们通常保持其原样不变。

当这些文件首次被使用时，我会进行说明。

## 本章小结

本章概述了 Java EE 开发中常用的一些配置文件。本书将在后续讨论具体应用时，对这些文件中的大部分进行更详细的探讨。

© Michael Müller 2018

Michael Müller, Practical JSF in Java EE 8 , `doi.org/10.1007/978-1-4842-3030-5_7`

