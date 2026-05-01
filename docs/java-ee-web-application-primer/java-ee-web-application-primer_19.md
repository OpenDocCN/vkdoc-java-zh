# 19. 使用 Gravatar 在帖子中显示用户头像

头像（Avatar）是人们在网络上用于代表自己身份的图片。Gravatar 是一项提供全球唯一头像的免费服务。Gravatar 允许用户使用其电子邮件地址注册一个账户。然后，用户可以上传一张图片与其 Gravatar 账户关联。当用户在某个使用 Gravatar 的网站上使用相同的电子邮件地址时，该网站会通过一个基于电子邮件地址哈希值生成的图片 URL，从 Gravatar 获取用户的头像。包括 Bullhorn 在内的网站可以自由使用 Gravatar 来显示用户的图片。Bullhorn 在帖子旁边和个人资料页面上显示该图片。

```

要控制图片大小，请在 URL 后附加 `?s=150`，其中 150 是 URL 返回的正方形图片的高度或宽度（以像素为单位）。's' 的值范围可以从 1 到 2048。较小的值看起来效果更好。

如果用户没有设置 Gravatar，则会显示一张默认图片：

[`https://www.gravatar.com/avatar/unknownhash`](https://www.gravatar.com/avatar/unknownhash)

![A458483_1_En_19_Figa_HTML.jpg](img/A458483_1_En_19_Figa_HTML.jpg)

使用 Java 计算 MD5 哈希值

MD5 哈希是一种加密文本的方式，使得通过查看文本无法识别其内容。MD5 的价值在于，相同的电子邮件地址总是会生成相同的 MD5 哈希值。

Gravatar URL 由用户电子邮件地址的 MD5 哈希值构成。你可以使用 Gravatar 网站提供的代码来创建电子邮件地址的 MD5 哈希值。该代码已在 Bullhorn 的 `MD5Util.java` 类中实现。给定用户的电子邮件地址，该代码将返回一个包含 MD5 哈希值的字符串。¹

Bullhorn 中的 `DbUser` 类包含一个生成 Gravatar URL 的方法。该方法接受两个参数：电子邮件和图片大小。然后，该方法返回正确的 URL，该 URL 可在整个 Bullhorn 站点的图片标签中使用。参见清单 19-1。

```
public static String getGravatarURL(String email, Integer size){
StringBuilder url = new StringBuilder();
url.append("http://www.gravatar.com/avatar/");
url.append(MD5Util.md5Hex(email));
url.append("?s=" + size.toString());
return url.toString();
}
清单 19-1
基于用户电子邮件地址生成 Gravatar URL 的方法
```

脚注

Bullhorn 中 MD5 类的源代码源自 [`http://en.gravatar.com/site/implement/images/java`](http://en.gravatar.com/site/implement/images/java) 上找到的代码。

20. 表示层/视图

表示层，也称为视图，是最终用户看待你的应用程序的视角。他们看不到所有的 Java 代码。他们只看到浏览器显示的内容。视图主要由 HTML、JavaScript 和图片组成。在本节中，我们将研究构成视图的不同 JSP 文件。JSP 文件包含其他代码，例如 JSTL，但最终结果是它们成为发送到用户浏览器的 HTML 文件。

首先，用户浏览到站点的 `login.jsp` 页面 URL。你的开发环境的 URL 将是 `http://localhost:8080/Bullhorn/login.jsp`。

注意

要在 Eclipse 中启动你的应用程序，在登录页面上，只需右键单击登录页面，然后选择“在服务器上运行”选项。你的站点将在 Eclipse 环境内的浏览器中打开。

登录页面的代码

```

登录

${message}请登录
电子邮件地址

密码

登录

加入

```

主页的代码

```

Bullhorn

这是主页

创建新帖子 (141 字符):

$(document).ready(function() {
var text_max = 141;
$('#textarea_feedback').html(text_max + ' characters remaining');
$('#posttext').keyup(function() {
var text_length = $('#posttext').val().length;
var text_remaining = text_max - text_length;
$('#textarea_feedback').html(text_remaining + ' characters remaining');
});
});
function validate(form) {
valid = true;
if ($('#posttext').val().length==0){
alert("You may not submit an empty post.");
valid = false;
}
return valid;
}

```

动态消息页面的代码

```

BullHorn

这是动态消息页面

用户帖子日期

">

```

个人资料页面的代码

```

BullHorn

/>&nbsp;&nbsp;个人资料 
电子邮件: 
座右铭: 
加入日期:

&nbsp;&nbsp;编辑 ${username} 的个人资料

电子邮件: 
座右铭: 
加入日期:

```

添加用户页面的代码

```

BullHorn

添加新用户
姓名: 
电子邮件: 
密码: 
座右铭: 
加入日期: -->

```

支持页面的代码

```

BullHorn

这是支持页面

```

错误页面的代码

```

BullHorn

出了点问题...

```

导航栏包含文件

```

切换导航

&nbsp;Bullhorn

首页(当前)
动态消息

提交

&nbsp;${user.username}

用户选项

登出-->

登出

显示我的帖子
编辑个人资料

反馈

```

BootStrap 包含文件

BootStrap 包含文件包含 BootStrap 文件的链接。这些链接来自 BootStrap 网站。他们使用名为 MaxCDN 的内容分发服务来托管他们的文件。你无需下载任何东西。只需包含来自 BootStrap 网站的链接，你的应用程序就会通过互联网获取该文件。

BootStrap 包含文件还包含一些样式表的链接，这些样式表可以在你的 Bullhorn 应用程序的 styles 文件夹中找到。styles 文件夹位于 WebContent 文件夹之下。

Bootstrap 样式页面

Bullhorn 站点包含一些从 [`https://bootswatch.com/`](https://bootswatch.com/) 下载的样式表。该站点包含免费可下载的 BootStrap 主题，你可以将其包含在你的项目中。只需浏览他们的收藏并将其包含在你的站点中。然后，从每个网页，你需要包含一个指向你想要使用的主题的链接。这是在 BootStrap 包含文件中完成的。

页脚包含文件

```

&nbsp;Bullhorn&nbsp;&nbsp;&copy; 2016

```

索引

A

AddUser servlet 代码

美国国家标准学会 (ANSI)

属性

action

alt

method

name

src

type

头像

B

Bhpost 表

用于创建的 SQL

SQL 语句

BHPOST 表

Bhuser 表

数据定义

删除表

输入数据

SQL 语句

BootStrap

包含文件

入门模板

样式页面

使用

Bullhorn

应用程序

DbUser 类

Bullhorn 组件

HTML

JPA 类

JSP

请求对象

servlets

会话

用户对象

Bullhorn 站点

组件

参见 Bullhorn 组件

个人资料编辑

C

层叠样式表 (CSS)

规则

span 和 div 标签

代码

内容分发网络 (CDN)

控制器

跨站脚本攻击 (XSS)

D

数据库

字段

外键

索引

层

打开 SQL 开发者

主键

关系类型

多对多

一对多

一对一

使用 Oracle 数据库

数据库管理系统 (DBMS)

数据库表

Bullhorn

源代码

数据库事务

原子性

一致性

持久性

隔离性

数据完整性

DbPost 类

DbUser 类

DbUtilities 类

文档对象模型 (DOM)

动态 Web 项目

E

Eclipse

IDE

安装

F

页脚包含文件

G

Gravatar

用户的电子邮件地址

H

HTML5

超链接

超文本标记语言 (HTML)

创建 Web 文档

错误

标签

网页

I

集成开发环境 (IDE)

J, K

Java 归档 (JAR) 文件

Java 异常

Java 持久化 API (JPA)

eclipselink

Eclipse 工具

实体

JPQL

META-INF

POJO

Java 持久化查询语言 (JPQL)

JavaScript

用于 Bootstrap

脚本标签

验证表单

提交帖子

文本框，字符

Java 服务器页面 (JSP)

JQuery

JSP 标准标签库 (JSTL)

L

链接

登录 servlet

登出按钮

M

MD5

模型

模型-视图-控制器 (MVC)

优点

与 Bullhorn 服务

团队建设

N

导航栏包含文件

导航栏

动态消息 servlet 代码

规范化

空值

O

面向对象编程语言

Oracle 数据库

Oracle 虚拟机，Virtualbox 管理器

P, Q

参数

传递数据过程

持久化.xml 文件



普通老式 Java 对象（POJO）

PostServ Servlet 代码

表示层

添加用户页面

错误页面

首页

新闻动态页面

个人资料页面

支持页面

Profile Servlet 代码

R

参照完整性

请求数据包

响应式网页设计（RWD）

S

服务

服务层

服务层创建

DbPost 类

DbUser 类

DbUtilities 类

Servlet

代码

AddUser Servlet

login Servlet

News Feed Servlet

PostServ Servlet

Profile Servlet

查找下一页

表单数据

Java 类

将数据发送到下一页

在输出页面上设置值

Tomcat

URL

Web

软件设计模式

SQL Developer

SQL 脚本

结构化查询语言（SQL）

T

标签

尖括号

属性

id

表单

头部

标题

图像

输入

段落

表格

标题

Tomcat

U

URL

用户与会话

添加对象

用户类的 getter 和 setter 方法

HttpSession

对象

私有成员变量

检索值

V

视图

Virtualbox

虚拟机（VM）

W、X、Y、Z

Web 管理员

Web 应用程序

Web 浏览器

Web 服务器

网站

```
