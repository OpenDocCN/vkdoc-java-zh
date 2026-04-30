# 随后，在 src 文件夹中查找 Java 类
bin/scomp -srconly -src src -d xsb \
/path/to/xsd/file
```

有关详细信息和选项，请查阅 Apache XMLBeans 文档。

23. JSON 处理

第 22 章介绍的 XML 绑定技术引入了一种宝贵的数据交换格式，简化并标准化了异构且广泛分布的计算机网络中的通信。通过对元素和属性使用模式与命名空间，XML 实现了语义和版本一致性，从而能够进行 XML 文档有效性检查。不幸的是，随着这些附加功能的增加，XML 文档变得越来越复杂，为了发送少量格式化为 XML 的字节，也必须传输大量样板数据。尤其是在浏览器与服务器的通信中，XML 显得过于繁重。因此，Web 应用程序开发者转向了更精简的 JSON（JavaScript 对象表示法）数据协议，这使得 Java 世界有必要开发能够处理 JSON 的工具和库。
JSON 处理是 Jakarta EE 10 规范的一部分。这具体涉及 Jakarta JSON Binding (JSON-B) 3.0 版和 Jakarta JSON Processing (JSON-P) 2.1 版。JSON-P 和 JSON-B 的规范可以在 [`https://jcp.org/aboutJava/communityprocess/pr/jsr367/index.html`](https://jcp.org/aboutJava/communityprocess/pr/jsr367/index.html) 和 [`https://jcp.org/en/jsr/detail?id=374`](https://jcp.org/en/jsr/detail%253Fid%253D374) 找到。本章将介绍这些 JSON 库，并解释如何将 JSON 添加到 Jakarta EE 应用程序中。

JSON 文档

与 XML 一样，JSON 文档表示一种树状的对象层次结构。与 XML 不同，JSON 实体不能拥有属性。没有命名空间和模式之类的东西，并且，与使用开闭标签不同，复杂的 JSON 对象使用 `{ ... }` 来映射名称-值对，使用 `[...]` 来表示数组。
下面是一个展示所有特性的简单 JSON 文档：

```
{
"lastname": "Smith",
"married": true,
"birthday": "1997-12-30",
"records": null,
"height": 6.1,
"cars": 2,
"children": [
"Amanda",
{ "forename": "Jim", "gender": "m" }
]
"address": {
"us": true,
"state": "California",
"city": "Paradise",
"owned": false
}
}
```

你可以看到，基本类型包括带或不带小数位的数字、布尔字面量 `true` 和 `false`、字符串以及 `null`。数组和映射可以包含混合类型的元素，包括内部复杂类型。

使用 REST 服务的 JSON

在前面的章节中，你看到了一个返回 JSON 数据的 REST 方法，如下所示：

```
@GET
@Produces("application/json")
public String stdDate() {
return "{\"date\":\"" +
ZonedDateTime.now().toString() + "\"}";
}
```

虽然乍一看，这似乎是创建 JSON 数据的一种简单方法，但它缺乏基本的语法检查。首先，很容易忘记一个闭合的 `}`，或者因为引号或标点符号放错位置而出错。其次，考虑一个包含更多元素和更深层次结构的更复杂的 JSON 文档。手动构建连接字符串来表示更大的 JSON 对象，在全面性和可维护性方面都是一场噩梦。

提高可读性的第一步是使用 JSON-P 来构建 JSON 数据。JSON-P 包含表示值、数组和映射的类。替换之前的代码片段，你最终会得到如下代码：

```
...
import jakarta.json.Json;
import jakarta.json.JsonStructure;
...
@GET
@Path("j")
@Produces("application/json")
public JsonStructure stdDate() {
JsonStructure json = Json.createObjectBuilder()
.add("date", ZonedDateTime.now().toString())
.build();
return json;
}
```

你可以看到，你不再需要连接字符串来构建 JSON 文档。你甚至可以让方法返回一个 `JsonObject`、`JsonArray` 或 `JsonStructure`（均来自 `jakarta.json` 包）类型的对象。JAX-RS 知道如何处理这些返回类型。

来自 AJAX 调用，如果你需要将数据从 Web 前端传递到服务器，你可以让 REST 服务接收一个字符串，如下所示：

```
// 对应于例如来自 jQuery 的 AJAX 调用：
// $.ajax({
//  method: "POST",
//  contentType: "application/json",
//  url: "../webapi/date",
//  data: { timeZone: "UTC", format: "standard" }
// })
// .done(function( msg, textStatus, jqXHR ) {
//   alert( "Data Saved: " + msg );
// });
@POST
@Path("/date")
@Consumes(MediaType.APPLICATION_JSON)
...
public Response stdDate(String data) {
// data 是一个 JSON 字符串
...
}
```

或者，作为一种替代方案，你可以为该方法使用一个 `jakarta.json.JsonStructure` 参数，如下所示：

```
// 对应于例如来自 jQuery 的 AJAX 调用：
// $.ajax({
//  method: "POST",
//  contentType: "application/json",
//  url: "../webapi/date",
//  data: '{ timeZone: "UTC", format: "standard" }'
// })
// .done(function( msg, textStatus, jqXHR ) {
//   alert( "Data Saved: " + msg );
// });
@POST
@Path("/date")
@Consumes(MediaType.APPLICATION_JSON)
...
public Response stdDate(JsonStructure data) {
...
}
```

对于 jQuery AJAX 调用，你必须传递一个 JSON 格式的字符串，而不是 JavaScript 数据对象。
除了这些简单的例子，关于使用 JSON-P 和 JSON-B 技术处理 JSON，还有更多内容有待探索。以下部分将更详细地解释 JSON 绑定和处理。

生成 JSON

为了使用构建器模式生成 JSON 数据，你需要使用 `jakarta.json` 包中的类。从一个构建器开始：

```
...
import jakarta.json.*;
...
JsonObjectBuilder builder = Json.createObjectBuilder();
```

从这里开始，你可以按如下方式添加元素：

```
builder.add("elementName", ...);
// <- 你可以添加 int、long、double、boolean、
//    String、BigInteger 和 BigDecimal 元素。
//    并且你可以追加更多的 add(...) 语句。
```

还有一个用于 `null` 值和复杂类型的变体：

```
builder.addNull("elementName");
builder.
add("element1",
Json.createObjectBuilder().
add("inner1", 42).
add("inner2", "deepThought")).
add("element2", 3.1415);
```

要创建 JSON 数组，请改用 `JsonArrayBuilder` 类：

```
...
import jakarta.json.*;
...
JsonArrayBuilder builder = Json.createArrayBuilder();
builder.add("Fibonacci");
builder.add(1); // 允许在数组中混合类型
builder.add(2);
builder.add(3);
builder.add(5);
builder.add(8);
```

对于 JSON 对象和 JSON 数组，要获取 Java 对象，你必须调用 `build()` 方法：

```
...
import jakarta.json.*;
...
JsonObjectBuilder builder = Json.createObjectBuilder();
// ... 添加元素，然后：
JsonObject obj = builder.build();
...
JsonArrayBuilder abuilder = Json.createArrayBuilder();
// ... 添加元素，然后：
JsonArray aobj = abuilder.build();
```

解析 JSON

对于 JSON 字符串的低级解析，你可以使用 JSON-P 的流式 API：

```
...
import jakarta.json.*;
...
final String json =
"{\"name\":\"John\",\"age\":27,\"employed\":false}";
final JsonParser parser =
Json.createParser(new StringReader(json));
String key = null;
String value = null;
while (parser.hasNext()) {
final Event event = parser.next();
switch (event) {
case KEY_NAME:
key = parser.getString();
System.out.println(key);
break;
case VALUE_STRING:
value = parser.getString();
System.out.println(value);
break;
}
}
parser.close();
```

如果你不需要冗长的 JSON 到 Java 对象的绑定（这需要额外的编码），这会很方便。此外，如果你想读取一个非常大的 JSON 文档，流式 API 可以帮助你避免分配过多的资源。

如果你不喜欢拉取解析 API，你可以读取一个 JSON 字符串，从而生成一个通用的 JSON 对象：

```
final String json =
"{\"name\":\"John\",\"age\":27,\"employed\":false}";
JsonReader jsonReader =
Json.createReader(new StringReader(json));
JsonObject object = jsonReader.readObject();
jsonReader.close();
...
String name = object.getString("name", "");
```

更多详细信息，请参阅 [`https://javaee.github.io/jsonp/index.html`](https://javaee.github.io/jsonp/index.html) 的 API 文档。

将 JSON 绑定到 Java 对象

如果你需要在 Java 对象树和该树的 JSON 表示之间进行自动映射，你可以使用 JSON-B 库中的类。考虑以下用于俱乐部成员列表的 JSON 字符串：

```
[
{
"name": "John",
"age": 27,
"employed":false,
"children" :
[
{"name": "Bart", "birthday": "2015-11-10"},
{"name": "Amanda", "birthday": "2017-06-01"}
]
},
{
"name": "Linda",
"age": 22,
"employed":true,
"children" :
[
]
}
]
```

表示相同列表的一组自然类如下所示：

```
class Member {
private String name;
private int age;
private boolean employed;
private List children;
// getters and setters...
}
class Child {
private String name;
private String birthday;
// getters and setters...
}
```

为了读取 JSON 字符串并构建一个成员列表，你只需这样做：

```
String json = "[ ... ]";
Jsonb jsonb = JsonbBuilder.create();
@SuppressWarnings("unchecked")
List members = jsonb.fromJson(json, List.class);
// 只是为了检查，反过来，从 Java 对象树获取
// JSON 字符串：
jsonb = JsonbBuilder.create();
String json2 = jsonb.toJson(members);
// <- 在语义上与上面的 'json' 相同。
```

在字符串、布尔值和数字等简单类型之间进行转换是直接的。如果你需要对转换过程进行更多控制，JSON-B 通过一些注解来帮助你。在前面的例子中，你可以例如将 `birthday` 字段更改为 `java.util.Date` 类型的字段：

```
class Child {
private java.util.Date birthday;
...
}
```

（getter 和 setter 相应地进行调整。）然后，`jakarta.json.bind.annotation.JsonbDateFormat` 允许指定日期字符串转换模式。对于 `yyyy-MM-dd` 模式，请参阅 `SimpleDateFormat` 的 API 文档以获取更多选项。该注解如下所示：

```
class Child {
@JsonbDateFormat("yyyy-MM-dd")
private java.util.Date birthday;
...
}
```

更多的注解控制数字格式和 `null` 值的转换。也可以从转换中排除字段，并且为了最大程度的控制，你可以定义一个自定义类型适配器。表 23-1 总结了 JSON-B 注解。

表 23-1
JSON-B 注解

 注解 |
 用途 |

| --- | --- | --- | --- | --- |

 `@JsonbDateFormat` |
 定义自定义日期格式。在 Java 中的 `java.util.Date` 和 JSON 字符串值之间进行中介。 |

 `@JsonbNillable` |
 将 Java 中的 `null` 值在 JSON 字符串中显示为 "null"。如果设置为 `false`，则 null 值不会出现在 JSON 字符串中。 |

 `@JsonbNumberFormat` |
 定义自定义数字格式。在 Java 中的 `java.math.BigDecimal` 和 JSON 字符串值之间进行中介。格式选项在 `DecimalFormat` 类的 API 文档中有描述。 |

 `@JsonbTransient` |
 从转换中排除一个字段。 |

 `@JsonbProperty` |
 为映射指定自定义键名。 |

 `@JsonbTypeAdapter` |
 指定自定义类型适配器（`JsonbAdapter` 类）。 |

作为类型适配器的一个例子，考虑以下类：

```
class Color {
public int r;
public int g;
public int b;
public Color(int r, int g, int b) {
this.r = r;
this.g = g;
this.b = b;
}
}
class Shape {
private Color color;
...
// getters and setters...
}
```

如果你希望颜色能够表示为十六进制值 `#RRGGBB`，一个形状对象的 JSON 片段可能如下所示：

```
{
"color": "#FF1B75",
...
}
```

转换器类负责将 `Color` 对象转换为 JSON 字符串表示，反之亦然：

```
class MyColorAdapter implements
JsonbAdapter {
@Override
public String adaptToJson(Color obj)
throws Exception {
return "#" +
String.format("%02x", obj.r) +
String.format("%02x", obj.g) +
String.format("%02x", obj.b);
}
@Override
public Color adaptFromJson(String obj)
throws Exception {
int r = Integer.parseInt( obj.substring(1,3), 16);
int g = Integer.parseInt( obj.substring(3,5), 16);
int b = Integer.parseInt( obj.substring(5,7), 16);
return new Color(r,g,b);
}
}
```

最后，要将转换器添加到 `Shape` 类，你必须按如下方式应用 `@JsonbTypeAdapter` 注解：

```
class Shape {
@JsonbTypeAdapter(MyColorAdapter.class)
private Color color;
...
// getters and setters...
}
```

来自 `jakarta.json.bind.annotation` 包的注解文档显示了更多信息。要了解更多关于 JSON-B 的信息，也请查看 [`https://javaee.github.io/jsonb-spec/`](https://javaee.github.io/jsonb-spec/)。

24. Jakarta Mail

能够从服务器进程发送电子邮件可能是公司 IT 架构的关键部分。例如，考虑异步传输长时间运行计算的结果、发送自动生成的报告，或者在服务器进程失败时发送警告消息。
Jakarta Mail 2.1 可以服务于这个目的，尽管它并不常被认为是 Jakarta EE 技术栈的核心部分。本章介绍 Jakarta Mail，它来自 [`https://eclipse-ee4j.github.io/mail/`](https://eclipse-ee4j.github.io/mail/)，作为 Jakarta EE 10 中包含的一个多功能电子邮件库。

安装 Jakarta Mail

Jakarta Mail 包含在 Jakarta EE 规范中，因此对于任何具有此基本依赖项（Gradle 配置，Maven 类似）的企业项目：

```
implementation 'jakarta.platform:' +
'jakarta.jakartaee-api:10.0.0'
```

你就拥有了使用 Jakarta Mail 所需的一切。

注意

Jakarta Mail 的前身称为 JavaMail。主要区别在于 Jakarta Mail 使用 `jakarta.` 命名空间而不是 `javax.`。许多在线资源仍然引用 JavaMail。在大多数情况下，你只需切换到新的命名空间，就可以轻松地调整 JavaMail 教程。

生成和发送电子邮件

从 Jakarta EE 应用程序发送电子邮件的标准方法包括以下步骤：

*   提供一个用于在消息传输代理 (MTA) 进行身份验证的身份验证器。为此，创建一个继承自 `jakarta.mail.Autenticator` 的可实例化类。如果你不需要向 MTA 进行身份验证，可以跳过此步骤。

*   实例化并填充一个 `java.util.Properties` 集合。在这里，需要输入各种与 SMTP 相关的参数。

*   获取一个 `jakarta.mail.Session` 实例。

*   实例化一个消息对象，例如 `jakarta.mail.internet.MimeMessage`。

*   从会话中获取一个传输对象（`jakarta.mail.Transport` 类）。

*   使用传输对象连接到 MTA，然后发送消息。

这个例子提供了一个用于构建和发送电子邮件的初级 REST 类。这只是一个起点。对于你自己的应用程序，你应该从配置文件中读取常量。你还必须更新连接参数以匹配你的邮件服务器。

```
import java.util.Date;
import java.util.Properties;
import jakarta.mail.Authenticator;
import jakarta.mail.Message;
import jakarta.mail.MessagingException;
import jakarta.mail.PasswordAuthentication;
import jakarta.mail.Session;
import jakarta.mail.Transport;
import jakarta.mail.internet.InternetAddress;
import jakarta.mail.internet.MimeMessage;
import jakarta.ws.rs.POST;
import jakarta.ws.rs.Path;
import jakarta.ws.rs.Produces;
@Path("/mail")
public class JakartaMail {
final String MAILSERVER_ADDR = "addr.to.mta.com";
final String MAILSERVER_PORT = "465"; //465,587;
final String MAILSERVER_IDENTITY = "your@email.addr.com";
final String MAILSERVER_LOGIN = "mailserver.login";
final String MAILSERVER_PASSWD = "mailserver.passwd";
@POST
@Produces("application/json")
public String sendMail() {
sendMail("recipient@example.com", "test",
new Date().toString());
return "{\"done\":true}";
}
class SMTPAuthenticator extends Authenticator {
public PasswordAuthentication
getPasswordAuthentication(){
return new PasswordAuthentication(MAILSERVER_LOGIN,
MAILSERVER_PASSWD);
}
}
private void sendMail(String toAddr,
String theSubject,
String theMessage) {
Properties props = new Properties();
props.put("mail.smtp.user", MAILSERVER_IDENTITY);
props.put("mail.smtp.host", MAILSERVER_ADDR);
props.put("mail.smtp.port", MAILSERVER_PORT);
props.put("mail.smtp.starttls.enable","true");
props.put("mail.smtp.debug", "true");
props.put("mail.smtp.auth", "true");
props.put("mail.smtp.socketFactory.port",
MAILSERVER_PORT);
//props.put("mail.smtps.ssl.checkserveridentity",
// "false");
props.put("mail.smtps.ssl.trust",  MAILSERVER_ADDR);
SMTPAuthenticator auth = new SMTPAuthenticator();
Session session = Session.getInstance(props, auth);
session.setDebug(false);
MimeMessage msg = new MimeMessage(session);
try {
msg.setText(theMessage);
msg.setSubject(theSubject);
msg.setFrom(new InternetAddress(
MAILSERVER_IDENTITY));
msg.addRecipient(Message.RecipientType.TO,
new InternetAddress(toAddr));
// 使用 "smtps" 而不是 "smtp"，以启用 SSL
Transport transport = session.getTransport("smtps");
transport.connect(MAILSERVER_ADDR,
Integer.parseInt(MAILSERVER_PORT),
MAILSERVER_LOGIN, MAILSERVER_PASSWD);
transport.sendMessage(msg, msg.getAllRecipients());
transport.close();
} catch (MessagingException e) {
e.printStackTrace(System.err);
}
}
}
```

关于 `mail.smtps.ssl.trust` 属性的一点说明：如果像这里这样使用，与 SSL 加密通信一起使用的握手过程会忽略服务器的 SSL 证书检查。如果你注释掉这一行，你可能需要将服务器的证书导入到 Java 的 `cacerts` 密钥库中。因此，如果你需要检查服务器的身份，预计会有一些额外的工作。

一旦部署完成——例如，作为 `JakartaMail.war` 部署在 GlassFish 服务器上，并且在 `web.xml` 中使用 `<url-pattern> /webapi/* </url-pattern>` URL 模式——你可以通过以下命令从终端触发一封电子邮件：

```
curl -X POST \
http://localhost:8080/JakartaMail/webapi/email
```

25. 应用程序客户端 (Groovy)

Jakarta EE 服务器应用程序通常提供客户端可以用来满足企业用例需求的接口。这些通常是用于通信 XML 或 JSON 数据的 Web 或 REST 服务，或者客户端可以通过 RMI 访问的 EJB 接口。然而，在开发阶段以及后续的维护目的（如监控）中，一个轻量级且易于配置的访问库是更好的解决方案。因此，一些应用服务器（如 GlassFish）提供了用于生成此类即时客户端库的脚本，通常称为*应用程序客户端*。

注意

应用程序客户端没有标准化。对于 GlassFish 以外的 Jakarta EE 服务器，你必须查阅文档以了解是否支持应用程序客户端。或者你可以开发自己的脚本。

本章介绍如何使用 Groovy 脚本，通过 GlassFish 生成的应用程序客户端库来访问 EJB 接口。

提供企业应用程序
你可以从任何具有 `remote` EJB 接口的 Jakarta EE 应用程序开始。远程接口是必需的，因为你在与应用程序服务器不同的 JVM 中运行客户端代码。

这个简单的例子编码了一个名为 `ApplicationClientServer` 的 EAR 项目。它针对一个可部署的 `ApplicationClientServer.ear`，并使用以下 EJB 类：

```
// ----- 文件 TheDate.java
package book.jakartapro.appclient.ejb;
import java.time.ZonedDateTime;
import book.jakartapro.appclient.ejb.interfaces.
TheDateLocal;
import book.jakartapro.appclient.ejb.interfaces.
TheDateRemote;
import jakarta.ejb.Local;
import jakarta.ejb.Remote;
import jakarta.ejb.Stateless;
@Stateless
@Local(TheDateLocal.class)
@Remote(TheDateRemote.class)
public class TheDate {
public String fetchDate() {
return ZonedDateTime.now().toString();
}
}
// ----- 文件 TheDateLocal.java
package book.jakartapro.appclient.ejb.interfaces;
public interface TheDateLocal {
String fetchDate();
}
// ----- 文件 TheDateRemote.java
package book.jakartapro.appclient.ejb.interfaces;
public interface TheDateRemote {
String fetchDate();
}
```

注意

在这一点上，该示例没有解释如何开发打包为 EAR 文件的 Jakarta EE 应用程序。不同的 IDE 为此目的使用不同的工作流程，这也取决于你使用的构建框架。前面的章节提供了一些提示；如果你需要更多信息，请查阅你的服务器文档。

为了使应用程序客户端生成工作，这个 EAR 必须部署并在 GlassFish 上运行。

构建应用程序客户端

在 GlassFish 上部署了 EAR 应用程序后，生成和运行应用程序客户端的步骤总结如下：

1.  将接口类打包到 `ejb-interfaces.jar` 归档文件中。

2.  生成 `appclient.jar`。转到 `GLASSFISH_INST/glassfish` 文件夹并执行 `bin/package-appclient`。

3.  获取上一步生成的 `GLASSFISH_INST/glassfish/lib/appclient.jar`。

4.  将 `appclient.jar` 解压到机器上的任何合适文件夹（记住 `.jar` 文件只是一个后缀不同的 ZIP 文件）。将此文件夹称为 `MYAPPCLIENT`。将 `appclient` 文件夹的内容移动到其父文件夹。

5.  将 `ejb-interfaces.jar` 复制到 `MYAPPCLIENT`。

6.  修改 `MYAPPCLIENT/config/asenv.*` 和 `MYAPPCLIENT/glassfish/domains/domain1/config/sun-acc.xml` 文件。请参阅 [`https://glassfish.org/docs/latest/reference-manual.html#package-appclient`](https://glassfish.org/docs/latest/reference-manual.html%2523package-appclient) 上的说明。仅当你想要使用 GlassFish 客户端应用程序容器时，才需要此步骤。此示例使用不同的方法；请参阅下一节。

7.  使 `MYAPPCLIENT/glassfish/bin/appclient` 和 `MYAPPCLIENT/glassfish/bin/appclient.bat` 文件可执行。仅当你想要使用 GlassFish 客户端应用程序容器时，才需要此步骤。此示例使用不同的方法；请参阅下一节。

8.  如果你想启动应用程序客户端容器，请提供客户端代码，例如 `myClient.jar`。然后运行 `MYAPPCLIENT/glassfish/bin/appclient`，将 `myClient.jar` 作为参数。

运行 GroovyConsole 应用程序客户端

为了允许 Groovy 脚本访问 Jakarta EE 服务器，首先从 [`https://groovy-lang.org/install.html`](https://groovy-lang.org/install.html) 下载并安装 Groovy。接下来——将 `GROOVY_HOME` 指向 Groovy 安装文件夹，将 `JAVA_HOME` 指向 JDK 11 版本，并按照上一节所述准备好 `MYAPPCLIENT` 文件夹——在 `MYAPPCLIENT` 内部构建一个如下所示的 shell 脚本：

```
#!/bin/bash



