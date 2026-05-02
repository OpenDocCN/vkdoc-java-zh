# 24. 校友

Michael Müller^(1 )

(1)德国北莱茵-威斯特法伦州布吕尔

校友是一个社交网络应用程序。人们可以在他们的母校、大学或类似机构注册一个或多个班级。他们可以使用公告板、组织活动、分享照片或进行同伴交流。

每个用户都可以输入和编辑信息。由于我们不能期望他们是专家，所有对话框都需要容错并提供快速响应。除了目前讨论的技术之外，我们还将使用以下技术（以及其他技术）：

*   转换器和验证器

*   AJAX

*   身份验证和授权

*   基于容器的安全性

*   组合组件

*   WebSockets

*   更改外观和感觉

本书没有足够的篇幅来详细讨论整个应用程序的每一个细节。相反，我将深入解释一些技术，有时会以比校友应用所需更宽泛的术语来描述它们。我将从基本实现开始，引导你从简单的解决方案走向定制化的解决方案。

## 准备应用程序

到目前为止，每次我们创建新应用程序时，都使用 NetBeans 向导为我们创建骨架。我们也可以为校友应用这样做，但由于在撰写本文时 NetBeans 不直接支持 Java EE 8，我们需要修改项目对象模型（POM）以使用 Java EE 8（如第 9 章所述）。

更重要的是，校友应用不是作为单个模块应用程序构建的，而是被划分为更小的部分，用于 GUI、业务层、存储和其他服务。Maven 通过多模块项目支持这种拆分。我们需要调整 POM，并为每个不同的模块创建子 POM。以下选定的 POM 摘录侧重于 Java EE 8 的方面以及模块化，省略了测试等其他内容。清单 24-1 从主 POM 开始。



###### 清单 24-1 Alumni 主 POM 文件（节选）

```
01 <?xml version="1.0" encoding="UTF-8"?>
02 <project xmlns:="http://maven.apache.org/POM/4.0.0"
03          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
04          xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
05          http://maven.apache.org/xsd/maven-4.0.0.xsd">

07   <modelVersion>4.0.0</modelVersion>

09   <properties>
10     <java.version>1.8</java.version>
11     <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
12  </properties>

14   <groupId>de.mueller-bruehl</groupId>
15   <artifactId>Alumni</artifactId>
16   <version>2017.0.0-SNAPSHOT</version>
17   <packaging>pom</packaging>

19   <modules>
20     <module>AlumniGui</module>
21     <module>AlumniBusiness</module>

22     <module>AlumniData</module>
23     <module>AlumniAccount</module>
24   </modules>

26   <build>
27     <plugins>
28       <plugin>
29         <groupId>org.apache.maven.plugins</groupId>
30         <artifactId>maven-compiler-plugin</artifactId>
31         <version>3.7.0</version>
32         <configuration>
33           <source>${java.version}</source>
34           <target>${java.version}</target>
35         </configuration>
36       </plugin>
37       <plugin>
38         <groupId>org.apache.maven.plugins</groupId>                                                                                                          
39         <artifactId>maven-war-plugin</artifactId>
40         <version>3.1.0</version>
41       </plugin>
42     </plugins>
43   </build>

45   <dependencyManagement>
46     <dependencies>
47       <dependency>
48         <groupId>javax</groupId>
49         <artifactId>javaee-api</artifactId>
50         <version>8.0</version>
51         <scope>provided</scope>
52       </dependency>

54      ... 其他依赖项在此处 ...

56     </dependencies>
57   </dependencyManagement>

59 </project>
```

从第 19 行开始，我们定义了应用程序的模块。我们将创建三个模块来组成 Alumni 应用程序：

*   AlumniGui（GUI 层）

*   AlumniBusiness（业务层）

*   AlumniData（数据库访问层）

第四个模块 AlumniAccount 将是一个由 Alumni 使用的独立服务（详见第 33 章）。在依赖管理中，我们为模块预定义了依赖项。这里我们引用了 Java EE 8 API（第 47-52 行）。除了这个父 POM，我们还需要为每个模块定义一个 POM。清单 24-2 展示了 AlumniGui 的 POM。（其他 POM 非常相似——推导出它们应该没有问题。我将在解释特定方面时稍后说明一些相关细节。）

###### 清单 24-2 模块 AlumniGui 的 POM（节选）

```
01 <?xml version="1.0" encoding="UTF-8"?>
02 <project xmlns:="http://maven.apache.org/POM/4.0.0"
03         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
04         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
05          http://maven.apache.org/xsd/maven-4.0.0.xsd">
06   <modelVersion>4.0.0</modelVersion>

08   <name>AlumniGui</name>

10   <parent>
11     <groupId>de.mueller-bruehl</groupId>
12     <artifactId>Alumni</artifactId>
13    <version>2017.0.0-SNAPSHOT</version>
14     <relativePath>../pom.xml</relativePath>
15   </parent>

17   <artifactId>AlumniGui</artifactId>
18   <packaging>war</packaging>

20   <dependencies>
21     <dependency>
22       <groupId>javax</groupId>
23       <artifactId>javaee-api</artifactId>
24       <scope>provided</scope>
25     </dependency>

27     <dependency>
28       <groupId>${project.groupId}</groupId>
29       <artifactId>AlumniBusiness</artifactId>
30       <version>${project.version}</version>
31    </dependency>

33     <dependency>
34      <groupId>${project.groupId}</groupId>
35       <artifactId>AlumniData</artifactId>
36       <version>${project.version}</version>                                                                                                          
37    </dependency>

39     <dependency>
40      <groupId>${project.groupId}</groupId>
41       <artifactId>AlumniService</artifactId>
42      <version>${project.version}</version>
43     </dependency>

45    ... 为简洁起见，省略了其他依赖项 ...

47   <build>
48     <finalName>${project.artifactId}</finalName>
49     <resources>
50       <resource>
51         <directory>src/main/resources</directory>
52         <filtering>true</filtering>
53       </resource>
54     </resources>
55   </build>

57 </project>
```

GUI 模块依赖于 Java EE 8 以及应用程序的其他层。

为了简单访问实体，业务层不会增加任何价值。我们要么使用业务层将所有数据访问委托给数据层，要么允许 GUI 绕过业务层直接访问数据层。因为我们使用的是 JPA，应用程序只处理 Java 对象。因此，即使我们绕过业务层，GUI 也永远不需要了解数据库。为简单起见，我选择了后一种方法。因此，AlumniGui 需要同时依赖 AlumniBusiness 和 AlumniData。

正如我所提到的，其他模块由类似的 POM 定义。为了避免循环依赖，没有其他模块会依赖 AlumniGui。

## 注册表单

Alumni 仅供会员使用。这意味着用户必须先注册。注册后，用户可以随时登录。我们在注册过程中只收集少量强制性的个人数据。

Alumni 的欢迎页面提供了一个登录表单和一个指向新用户注册对话框的链接。虽然注册不是应用程序的第一个对话框，但它代表了每个用户在使用 Alumni 之前需要执行的第一个操作。这也是介绍后续 JSF 和 Java EE 特性的一个很好的起点。

与所有其他对话框一样，此表单将验证用户输入并通过显示消息报告潜在问题。这可以防止用户输入无效数据，同时也保护系统免受意外行为的影响。

在 Books 中，当用户单击“提交”按钮时进行此验证。对于小页面来说，这可能是一个不错的解决方案，但对于较大的页面来说，这却是一种负担。你是否曾经因为填写了数百个输入字段后，页面才提示第一个输入字段有问题而感到恼火？为了用户友好，我们将在每个输入字段后提供反馈。这就是 AJAX 发挥作用的地方。

JSF 提供了一组通常会被翻译（渲染）成 HTML 组件的组件。有些人认为这是一个不必要的抽象层。为什么在 HTML 期望 `<input type = "text .../>` 的地方编写 `<h:inputText .../>`？答案很简单：一个 JSF 标签可能由不同的渲染引擎处理。例如，PDF 渲染器可能会将 inputText 转换为交互式文本字段。JSF 标签是一种用于创建不同格式的领域特定语言（DSL）。

如果我们只需要渲染 HTML，我们可以省略 DSL，直接使用 HTML 编写页面，并辅以 JSF 信息。（第 5 章简要介绍了这种编码方式。）我们将开发两个版本的注册表单：一个使用传统的 JSF 标签，另一个主要使用 HTML 标签。当然，我们还必须考虑安全性。为了防止用户注入 HTML 代码，我们必须正确地转义输出。JSF 标签会自动为我们处理这个问题。

清单 24-3 展示了注册表单的第一个原始版本，图 24-1 展示了其摘录。



###### 清单 24-3 注册表单

```
 1   <h:form>

 3     <div class="inputPart">
 4       <h:outputLabel for="firstName"
 5                      value="#{msg.lblFirstName}"
 6                      styleClass="label"/>
 7       <h:message id="msgFirstName" for="firstName"
 8                  styleClass="errorMessage"/>
 9       <h:inputText id="firstName"
10                    value="#{register.accountRequest.firstName}"
11                    styleClass="inputFull"/>
12     </div>

14     <div class="inputPart">
15       <h:outputLabel for="lastName"
16                      value="#{msg.lblLastName}"                                                                
17                      styleClass="label"/>
18       <h:message id="msgLastName"
19                  for="lastName"
20                  styleClass="errorMessage"/>
21       <h:inputText id="lastName"
22                    value="#{register.accountRequest.lastName}"
23                    styleClass="inputFull"/>
24     </div>

26     <div class="inputPart">
27       <h:outputLabel for="loginName"
28                      value="#{msg.lblLoginName}"
29                      styleClass="label"/>
30       <h:message id="msgLoginName"
31                  for="loginName"
32                  styleClass="errorMessage"/>
33       <h:inputText id="loginName"
34                    value="#{register.account.loginName}"
35                    styleClass="inputFull"/>
37     </div>

39     <div class="inputPart">
40       <h:outputLabel for="email"
41                      value="#{msg.lblEmail}"
42                      styleClass="label"/>
43       <h:message id="msgEmail"
44                  for="email"
45                  styleClass="errorMessage"/>
46       <h:inputText id="email"
47                    value="#{register.accountRequest.email}"
48                    styleClass="inputFull"/>
49     </div>

51  <div class="inputPart">
52    <h:outputLabel for="password"
53                   value="#{msg.lblPassword}"
54                   styleClass="label"/>
55    <h:message id="msgPassword"
56               for="password"
57               styleClass="errorMessage"/>                                                                
58    <h:inputSecret id="password"
59                   styleClass="inputFull"/>
60  </div>

62  <div class="inputPart">
63    <h:outputLabel for="repeatPassword"
64                   value="#{msg.lblRepeatPassword}"
65                   styleClass="label"/>
66    <h:message id="msgRepeatPassword"
67               for="repeatPassword"
68               styleClass="errorMessage"/>
69    <h:inputSecret id="repeatPassword"
70                   value="#{register.password}"
71                   styleClass="inputFull"/>
72  </div>37
73     <div class="buttonBar">
74       <h:commandButton value="#{msg.btnRegister}"
75                        action="#{register.register()}"
76                        styleClass="button"/>
77     </div>

79   </h:form>
```

![A435433_1_En_24_Fig1_HTML.jpg](img/A435433_1_En_24_Fig1_HTML.jpg)

###### 图 24-1 注册表单片段

在这个表单中，我们有多个输入字段，每个字段都嵌入在独立的 div 中，并附带一个标签和一个消息。例如，查看名字字段（第 3–12 行）。我们从标签开始（第 4–6 行）。在该标签之后，如果出现错误，将显示一条消息（第 7 行和第 8 行）。最后，我们有适当的输入字段，用户可以在其中输入信息（第 9–11 行）。

对于密码字段，我们使用 `inputSecret` 代替 `inputText`。这会渲染一个秘密输入字段，每个字符显示为圆点，隐藏实际输入。除此之外，它的用法与其他输入字段相同。

如前所述，如果验证错误，将显示错误消息。如果像这样创建表单，则只有在用户单击“注册”按钮时才会进行验证。

查看这段代码，可能会产生一些问题：

*   我们如何执行验证？

*   我们如何在输入每个值后提供即时反馈？

*   所有输入部分看起来都很相似——是否可以预定义并重用这样的结构？

*   我们如何处理密码？

接下来的几章将回答这些问题。

## 总结

本章介绍了 Alumni，一个依赖用户输入的社交媒体应用程序。这意味着我们需要所有对话框都具有容错性，并能提供即时反馈。

Alumni 主要由不同的软件模块构建，用于 GUI、业务逻辑和数据。一个独立的微服务为 Alumni 和身份验证管理用户。本章介绍了所需 POM 文件的有趣部分，并特别关注了 Java EE 8。

像为 Books 开发表单那样开发注册表单会显得繁琐，并引发如何做得更好的问题。

© Michael Müller 2018

Michael Müller, Practical JSF in Java EE 8 , `doi.org/10.1007/978-1-4842-3030-5_25`

