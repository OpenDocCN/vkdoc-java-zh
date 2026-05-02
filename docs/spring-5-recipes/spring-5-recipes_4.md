# 4. Spring REST

在本章中，您将学习 Spring 如何处理表述性状态转移（通常以其缩写 REST 指代）。自 2000 年 Roy Fielding（[`http://en.wikipedia.org/wiki/Roy_Fielding`](http://en.wikipedia.org/wiki/Roy_Fielding)）提出该术语以来，REST 对 Web 应用程序产生了重要影响。

基于 Web 超文本传输协议（HTTP）的基础，REST 所提出的架构在 Web 服务的实现中变得越来越流行。Web 服务本身已成为 Web 上大量机器间通信的基石。正是许多组织做出的碎片化技术选择（例如 Java、Python、Ruby、.NET）使得需要一种能够弥合这些不同环境之间差距的解决方案。例如，如何让用 Java 支持的应用程序中的信息被用 Python 编写的应用程序访问？Java 应用程序如何从用 .NET 编写的应用程序中获取信息？Web 服务填补了这一空白。

实现 Web 服务有多种方法，但 RESTful Web 服务已成为 Web 应用程序中最常见的选择。它们被一些最大的互联网站点（例如 Google 和 Yahoo）用来提供对其信息的访问，支持浏览器发出的 Ajax 调用，并为新闻源（例如 RSS）等信息分发提供基础。

在本章中，您将学习 Spring 应用程序如何使用 REST，以便您能够使用这种流行的方法来访问和提供信息。

## 4-1. 使用 REST 服务发布 XML

### 问题

您希望使用 Spring 发布一个基于 XML 的 REST 服务。

### 解决方案

在 Spring 中设计 REST 服务有两种可能性。一种涉及将应用程序的数据作为 REST 服务发布；另一种涉及从第三方 REST 服务访问数据以在应用程序中使用。本配方描述如何将应用程序的数据作为 REST 服务发布。配方 4-2 描述如何从第三方 REST 服务访问数据。将应用程序的数据作为 REST 服务发布主要围绕使用 Spring MVC 注解 `@RequestMapping` 和 `@PathVariable`。通过使用这些注解来修饰 Spring MVC 处理器方法，Spring 应用程序能够将应用程序的数据作为 REST 服务发布。

此外，Spring 支持一系列生成 REST 服务负载的机制。本配方将探讨最简单的机制，即使用 Spring 的 `MarshallingView` 类。随着本章配方的深入，您将了解 Spring 支持的用于生成 REST 服务负载的更高级机制。

### 工作原理

将 Web 应用程序的数据作为 REST 服务发布（或者用 Web 服务术语更技术性地称为“创建端点”）与您在第 3 章中探讨的 Spring MVC 紧密相关。由于 Spring MVC 依赖注解 `@RequestMapping` 来修饰处理器方法并定义访问点（即 URL），因此它是定义 REST 服务端点的首选方式。



#### 使用 MarshallingView 生成 XML

以下代码展示了一个 Spring MVC 控制器类，其中包含一个定义 REST 服务端点的处理方法：

```
package com.apress.springrecipes.court.web;
import com.apress.springrecipes.court.domain.Members;
import com.apress.springrecipes.court.service.MemberService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;
@Controller
public class RestMemberController {
private final MemberService memberService;
@Autowired
public RestMemberController(MemberService memberService) {
super();
this.memberService=memberService;
}
@RequestMapping("/members")
public String getRestMembers(Model model) {
Members members = new Members();
members.addMembers(memberService.findAll());
model.addAttribute("members", members);
return "membertemplate";
}
}
```

通过使用 `@RequestMapping("/members")` 装饰控制器的处理方法，可以在 `host_name/[app-name]/members` 访问 REST 服务端点。你可以观察到控制权被移交给了名为 `membertemplate` 的逻辑视图。以下代码展示了用于定义名为 `membertemplate` 的逻辑视图的声明：

```
@Configuration
@EnableWebMvc
@ComponentScan(basePackages = "com.apress.springrecipes.court")
public class CourtRestConfiguration {
@Bean
public View membertemplate() {
return new MarshallingView(jaxb2Marshaller());
}
@Bean
public Marshaller jaxb2Marshaller() {
Jaxb2Marshaller marshaller = new Jaxb2Marshaller();
marshaller.setClassesToBeBound(Members.class, Member.class);
return marshaller;
}
@Bean
public ViewResolver viewResolver() {
return new BeanNameViewResolver();
}
}
```

`membertemplate` 视图被定义为 `MarshallingView` 类型，这是一个通用类，允许使用编组器（marshaller）来渲染响应。编组是将对象的内存表示转换为数据格式的过程。因此，在这个特定案例中，编组器负责将 `Members` 和 `Member` 对象转换为 XML 数据格式。`MarshallingView` 使用的编组器是 Spring 提供的一系列 XML 编组器之一——`Jaxb2Marshaller`。Spring 提供的其他编组器包括 `CastorMarshaller`、`JibxMarshaller`、`XmlBeansMarshaller` 和 `XStreamMarshaller`。

编组器本身也需要配置。我们选择使用 `Jaxb2Marshaller` 编组器，因为它简单且基于 Java 架构的 XML 绑定（JAXB）。然而，如果你更习惯使用 Castor XML 框架，你可能会发现使用 `CastorMarshaller` 更容易；如果你更习惯使用 XStream，你可能会发现使用 `XStreamMarshaller` 更容易；其他可用的编组器情况类似。

`Jaxb2Marshaller` 编组器需要配置一个名为 `classesToBeBound` 的属性或一个名为 `contextPath` 的属性。对于 `classesToBeBound`，分配给此属性的类指示将要转换为 XML 的类（即对象）结构。以下代码展示了分配给 `Jaxb2Marshaller` 编组器的 `Members` 和 `Member` 类：

```
package com.apress.springrecipes.court.domain;
import javax.xml.bind.annotation.XmlRootElement;
@XmlRootElement
public class Member {
private String name;
private String phone;
private String email;
public String getEmail() {
return email;
}
public String getName() {
return name;
}
public String getPhone() {
return phone;
}
public void setEmail(String email) {
this.email = email;
}
public void setName(String name) {
this.name = name;
}
public void setPhone(String phone) {
this.phone = phone;
}
}
```

以下是 `Members` 类：

```
package com.apress.springrecipes.court.domain;
import javax.xml.bind.annotation.XmlAccessType;
import javax.xml.bind.annotation.XmlAccessorType;
import javax.xml.bind.annotation.XmlElement;
import javax.xml.bind.annotation.XmlRootElement;
import java.util.ArrayList;
import java.util.Collection;
import java.util.List;
@XmlRootElement
@XmlAccessorType(XmlAccessType.FIELD)
public class Members {
@XmlElement(name="member")
private List members = new ArrayList();
public List getMembers() {
return members;
}
public void setMembers(List members) {
this.members = members;
}
public void addMembers(Collection members) {
this.members.addAll(members);
}
}
```

注意 `Member` 类是一个用 `@XmlRootElement` 注解修饰的 POJO。此注解允许 `Jaxb2Marshaller` 编组器检测类（即对象）的字段，并将其转换为 XML 数据（例如，`name=John` 转换为 `<name>john</name>`，`email=john@doe.com` 转换为 `<email>john@doe.com</email>`）。

回顾一下所描述的内容，这意味着当向 `http://[host_name]//app-name]/members.xml` 形式的 URL 发出请求时，相应的处理程序负责创建一个 `Members` 对象，然后将其传递给名为 `membertemplate` 的逻辑视图。根据后一个视图的定义，使用编组器将 `Members` 对象转换为 XML 负载，并返回给 REST 服务的请求方。REST 服务返回的 XML 负载如下代码所示：

```

marten@deinum.biz
Marten Deinum
00-31-1234567890

john@doe.com
John Doe
1-800-800-800

jane@doe.com
Jane Doe
1-801-802-803

```

这个 XML 负载代表了一种生成 REST 服务响应的简单方法。随着本章示例的深入，你将学习更复杂的方法，例如能够创建广泛使用的 REST 服务负载，如 RSS、Atom 和 JSON。

如果你仔细查看上一段中描述的 REST 服务端点或 URL，你会注意到它有一个 `.xml` 扩展名。如果你尝试其他扩展名——甚至省略扩展名——这个特定的 REST 服务可能不会被触发。这最后一种行为直接与 Spring MVC 及其处理视图解析的方式有关，与 REST 服务本身无关。

默认情况下，由于与此特定 REST 服务处理方法关联的视图返回 XML，因此它由 `.xml` 扩展名触发。这允许同一个处理方法支持多个视图。例如，对于像 `http://[host_name]/[app-name]/members.pdf` 这样的请求，可以方便地以 PDF 文档形式返回相同的信息；对于像 `http://[host_name]/[app-name]/members.html` 这样的请求，可以以 HTML 形式返回内容；或者对于像 `http://[host_name]/[app-name]/members.xml` 这样的请求，可以为 REST 请求返回 XML。

那么，对于没有 URL 扩展名的请求，比如 `http://[host_name]/[app-name]/members?` 会发生什么？这也很大程度上取决于 Spring MVC 的视图解析。为此，Spring MVC 支持一种称为内容协商的过程，通过该过程，可以根据请求的扩展名或 HTTP 标头来确定视图。

由于 REST 服务请求通常带有 `Accept: application/xml` 形式的 HTTP 标头，配置为使用内容协商的 Spring MVC 可以确定为此类请求提供 XML（REST）负载，即使请求没有扩展名。这也允许基于 HTTP 标头，以 HTML、PDF 和 XLS 等格式发出无扩展名的请求。第 3 章的配方 3-7 讨论了内容协商。



#### 使用 @ResponseBody 生成 XML

使用 `MarshallingView` 生成 XML 是产生结果的一种方式；然而，当你希望同一份数据（例如 `Member` 对象列表）拥有多种表示形式（比如 JSON）时，添加另一个视图会是一项繁琐的任务。相反，你可以依赖 Spring MVC 的 `HttpMessageConverters` 将对象转换为用户请求的表示形式。以下代码展示了 `RestMemberController` 所做的修改：

```
@Controller
public class RestMemberController {
...
@RequestMapping("/members")
@ResponseBody
public Members getRestMembers() {
Members members = new Members();
members.addMembers(memberService.findAll());
return members;
}
}
```

第一个变化是，你现在额外使用 `@ResponseBody` 注解了控制器方法。这个注解告诉 Spring MVC，该方法的结果应被用作响应的主体。因为你想要 XML，所以这个编组工作由 Spring 提供的 `Jaxb2RootElementHttpMessageConverter` 类完成。第二个变化是，由于使用了 `@ResponseBody` 注解，你不再需要视图名称，而可以直接返回 `Members` 对象。

提示

当使用 Spring 4 或更高版本时，除了使用 `@ResponseBody` 注解方法外，你也可以用 `@RestController` 注解你的控制器来替代 `@Controller`，这会产生相同的结果。如果你的单个控制器有多个方法，这种方式尤其方便。

这些修改还允许你清理配置，因为你不再需要 `MarshallingView` 和 `Jaxb2Marshaller`。

```
package com.apress.springrecipes.court.web.config;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.EnableWebMvc;
@Configuration
@EnableWebMvc
@ComponentScan(basePackages = "com.apress.springrecipes.court")
public class CourtRestConfiguration {}
```

当应用程序部署后，你从 `http://localhost:8080/court/members.xml` 请求成员信息时，它将产生与之前相同的结果。

```

marten@deinum.biz
Marten Deinum
00-31-1234567890

john@doe.com
John Doe
1-800-800-800

jane@doe.com
Jane Doe
1-801-802-803

```

#### 使用 @PathVariable 限制结果

REST 服务请求带有参数是很常见的。这样做是为了限制或过滤服务的负载。例如，形如 `http://[host_name]/[app-name]/member/353/` 的请求可用于专门检索成员 353 的信息。另一种变体是像 `http://[host_name]/[app-name]/reservations/07-07-2010/` 这样的请求，用于检索 2010 年 7 月 7 日当天的预订信息。

要在 Spring 中使用参数构建 REST 服务，你需要使用 `@PathVariable` 注解。根据 Spring MVC 的约定，`@PathVariable` 注解作为输入参数添加到处理器方法中，以便在处理器方法体内使用。以下代码片段展示了使用 `@PathVariable` 注解的 REST 服务处理器方法：

```
import org.springframework.web.bind.annotation.PathVariable;
@Controller
public class RestMemberController {
...
@RequestMapping("/member/{memberid}")
@ResponseBody
public Member getMember(@PathVariable("memberid") long memberID) {
return memberService.find(memberID);
}
}
```

注意 `@RequestMapping` 的值包含 `{memberid}`。被 `{ }` 包围的值用于指示 URL 参数是变量。进一步注意，处理器方法定义时带有输入参数 `@PathVariable("memberid") long memberID`。这个最后的声明将 URL 中任何 `memberid` 的值关联起来，并将其赋值给一个名为 `memberID` 的变量，该变量可在处理器方法内部访问。因此，形如 `/member/353/` 和 `/member/777/` 的 REST 端点将由这个最后的处理器方法处理，其中 `memberID` 变量分别被赋值为 `353` 和 `777`。在处理器方法内部，可以通过 `memberID` 变量对成员 353 和 777 进行适当的查询，并将结果作为 REST 服务的负载返回。

向 `http://localhost:8080/court/member/2` 发送请求将返回 ID 为 2 的成员的 XML 表示。

```

john@doe.com
John Doe
1-800-800-800

```

除了支持 `{ }` 表示法之外，还可以使用通配符（`*`）表示法来定义 REST 端点。当设计团队选择使用富有表现力的 URL（通常称为漂亮 URL）或采用搜索引擎优化（SEO）技术使 REST URL 对搜索引擎友好时，通常会采用这种方式。以下代码片段展示了使用通配符表示法声明 REST 服务的示例：

```
@RequestMapping("/member/*/{memberid}")
@ResponseBody
public Member getMember(@PathVariable("memberid") long memberID) { ... }
```

在这种情况下，添加通配符对 REST 服务执行的逻辑没有任何影响。但它会匹配形如 `/member/John+Smith/353/` 和 `/member/Mary+Jones/353/` 的端点请求，这对最终用户的可读性和 SEO 有重要影响。

同样值得一提的是，数据绑定可以在 REST 端点处理器方法的定义中使用。以下代码片段展示了使用数据绑定声明 REST 服务的示例：

```
@InitBinder
public void initBinder(WebDataBinder binder) {
SimpleDateFormat dateFormat = new SimpleDateFormat("yyyy-MM-dd");
binder.registerCustomEditor(Date.class, new CustomDateEditor(dateFormat, false));
}
@RequestMapping("/reservations/{date}")
public void getReservation(@PathVariable("date") Date resDate) { ... }
```

在这种情况下，形如 `http://[host_name]/[app-name]/reservations/07-07-2010/` 的请求将由这个最后的处理器方法匹配，值 `07-07-2010` 作为变量 `resDate` 传入处理器方法，并可用于过滤 REST Web 服务的负载。

#### 使用 ResponseEntity 通知客户端

用于检索单个 `Member` 实例的端点要么返回一个有效的成员，要么什么都不返回。这两种情况都会导致请求向客户端发送 HTTP 响应码 200，即表示“成功”。然而，这可能不是你的用户所期望的。在处理资源时，你应该告知他们资源无法找到的事实。理想情况下，你会希望返回 HTTP 响应码 404，表示“未找到”。以下代码片段展示了修改后的 `getMember` 方法：

```
package com.apress.springrecipes.court.web;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
...
@Controller
public class RestMemberController {
...
@RequestMapping("/member/{memberid}")
@ResponseBody
public ResponseEntity getMember(@PathVariable("memberid") long memberID) {
Member member = memberService.find(memberID);
if (member != null) {
return new ResponseEntity(member, HttpStatus.OK);
}
return new ResponseEntity(HttpStatus.NOT_FOUND);
}
}
```

该方法的返回值已更改为 `ResponseEntity<Member>`。Spring MVC 中的 `ResponseEntity` 类充当一个包装器，用于将对象作为结果的主体与 HTTP 状态码一起使用。当你找到一个 `Member` 时，它会与 `HttpStatus.OK` 一起返回，这对应于 HTTP 状态码 200。当没有结果时，你返回 `HttpStatus.NOT_FOUND`，对应于 HTTP 状态码 404，表示“未找到”。

## 4-2\. 使用 REST 服务发布 JSON

### 问题

你想使用 Spring 发布一个基于 JavaScript 对象表示法（JSON）的 REST 服务。



### 解决方案

JSON 已成为 REST 服务最常用的负载格式。然而，与大多数依赖 XML 标记的 REST 服务负载不同，JSON 的特殊之处在于其内容是基于 JavaScript 语言的一种特殊表示法。在本方案中，除了依赖 Spring 的 REST 支持外，你还需要使用 Spring 自带的 `MappingJackson2JsonView` 类来简化 JSON 内容的发布。

注意

`MappingJackson2JsonView` 类依赖于 Jackson JSON 处理器库（版本 2），该库可从 [`http://wiki.fasterxml.com/JacksonDownload`](http://wiki.fasterxml.com/JacksonDownload) 下载。如果你使用 Maven 或 Gradle，只需将 Jackson 库作为依赖项添加到项目的构建文件中即可。

如果你的 Spring 应用采用了 Ajax 设计，那么你很可能会设计以 JSON 作为负载的 REST 服务。这主要是因为浏览器的处理能力有限。虽然浏览器能够处理并提取发布 XML 负载的 REST 服务中的信息，但效率并不高。而通过改用基于浏览器原生解释语言（JavaScript）的 JSON 格式来传递负载，数据的处理和提取效率会更高。与 RSS 和 Atom 这类标准格式不同，JSON 除了需要遵循其语法（稍后你将了解）外，没有特定的结构要求。因此，JSON 元素的负载结构通常需要与负责应用 Ajax 设计的团队成员协商确定。

### 工作原理

首先，你需要确定要发布为 JSON 负载的信息。这些信息可以存储在关系数据库或文本文件中，通过 JDBC 或 ORM 访问，甚至可以是 Spring Bean 或其他类型的构造体的一部分。描述如何获取这些信息超出了本方案的范围，因此我们假设你将采用任何你认为合适的方式来访问它。如果你不熟悉 JSON，以下代码片段展示了该格式的一个示例：

```
{
"glossary": {
"title": "example glossary",
"GlossDiv": {
"title": "S",
"GlossList": {
"GlossEntry": {
"ID": "SGML",
"SortAs": "SGML",
"GlossTerm": "Standard Generalized Markup Language",
"Acronym": "SGML",
"Abbrev": "ISO 8879:1986",
"GlossDef": {
"para": "A meta-markup language, used to create markup languages such as DocBook.",
"GlossSeeAlso": ["GML", "XML"]
},
"GlossSee": "markup"
}
}
}
}
}
```

如你所见，JSON 负载由文本和分隔符（如 `{`、`}`、`[`、`]`、`:` 和 `"`）组成。我们不会深入探讨使用哪种分隔符的细节，但可以明确的是，与 XML 格式相比，这种语法使得 JavaScript 引擎更容易访问和操作数据。

#### 使用 MappingJackson2JsonView 生成 XML

由于你已经在方案 4-1 和 4-3 中探索了如何使用 REST 服务发布数据，我们将直奔主题，展示在 Spring MVC 控制器中实现此过程所需的实际处理方法。

```
@RequestMapping("/members")
public String getRestMembers(Model model) {
Members members = new Members();
members.addMembers(memberService.findAll());
model.addAttribute("members", members);
return "jsonmembertemplate";
}
```

你可能注意到，这与方案 4-1 中提到的控制器方法非常相似。唯一的区别是你返回了不同的视图名称。这里返回的视图名称 `jsonmembertemplate` 不同，它映射到一个 `MappingJackson2JsonView` 视图。你需要在配置类中配置此视图。

```
@Configuration
@EnableWebMvc
@ComponentScan(basePackages = "com.apress.springrecipes.court")
public class CourtRestConfiguration {
...
@Bean
public View jsonmembertemplate() {
MappingJackson2JsonView view = new MappingJackson2JsonView();
view.setPrettyPrint(true);
return view;
}
}
```

`MappingJackson2JsonView` 视图使用 Jackson2 库在对象与 JSON 之间进行转换。它使用 Jackson2 的 `ObjectMapper` 实例来完成转换。当请求发送到 `http://localhost:8080/court/members.json` 时，控制器方法将被调用，并返回一个 JSON 表示。

```
{
"members" : {
"members" : [ {
"name" : "Marten Deinum",
"phone" : "00-31-1234567890",
"email" : "marten@deinum.biz"
}, {
"name" : "John Doe",
"phone" : "1-800-800-800",
"email" : "john@doe.com"
}, {
"name" : "Jane Doe",
"phone" : "1-801-802-803",
"email" : "jane@doe.com"
} ]
}
}
```

实际上，每次对 `/members` 或 `/members.*`（例如，`/members.xml` 也会生成 JSON）的调用都会生成此 JSON。现在，让我们将方案 4-1 中的方法和视图添加到控制器中。

```
@Controller
public class RestMemberController {
...
@RequestMapping(value="/members", produces=MediaType.APPLICATION_XML_VALUE)
public String getRestMembersXml(Model model) {
Members members = new Members();
members.addMembers(memberService.findAll());
model.addAttribute("members", members);
return "xmlmembertemplate";
}
@RequestMapping(value="/members", produces= MediaType.APPLICATION_JSON_VALUE)
public String getRestMembersJson(Model model) {
Members members = new Members();
members.addMembers(memberService.findAll());
model.addAttribute("members", members);
return "jsonmembertemplate";
}
}
```

现在你有了 `getMembersXml` 方法和 `getMembersJson` 方法；两者基本相同，区别在于它们返回不同的视图名称。请注意 `@RequestMapping` 注解上的 `produces` 属性。它用于决定调用哪个方法：`/members.xml` 现在将生成 XML，而 `/members.json` 将生成 JSON。

虽然这种方法可行，但为不同支持的视图类型复制所有方法对于企业级应用来说并非可行的解决方案。你可以创建一个辅助方法来减少重复，但由于 `@RequestMapping` 注解的差异，你仍然需要编写大量样板代码。



#### 使用 @ResponseBody 生成 JSON

使用 `MappingJackson2JsonView` 生成 JSON 是产生结果的一种方式；然而，正如上一节所述，这种方式可能比较麻烦，尤其是在支持多种视图类型的情况下。相反，你可以依赖 Spring MVC 的 `HttpMessageConverters` 将对象转换为用户请求的表示形式。以下代码展示了 `RestMemberController` 所做的修改：

```
@Controller
public class RestMemberController {
...
@RequestMapping("/members")
@ResponseBody
public Members getRestMembers() {
Members members = new Members();
members.addMembers(memberService.findAll());
return members;
}
}
```

第一个变化是，你现在额外使用 `@ResponseBody` 注解了控制器方法。这个注解告诉 Spring MVC，该方法的结果应作为响应的主体。因为你想要 JSON，所以这个编组工作由 Spring 提供的 `Jackson2JsonMessageConverter` 类完成。第二个变化是，由于使用了 `@ResponseBody` 注解，你不再需要视图名称，而可以直接返回 `Members` 对象。

提示

当使用 Spring 4 或更高版本时，除了用 `@ResponseBody` 注解方法外，你还可以用 `@RestController` 注解你的控制器，而不是 `@Controller`，这将产生相同的结果。如果你有一个包含多个方法的单一控制器，这尤其方便。

这些变化也允许你清理配置，因为你不再需要 `MappingJackson2JsonView`。

```
package com.apress.springrecipes.court.web.config;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.EnableWebMvc;
@Configuration
@EnableWebMvc
@ComponentScan(basePackages = "com.apress.springrecipes.court")
public class CourtRestConfiguration {}
```

当应用程序部署后，你从 `http://localhost:8080/court/members.json` 请求成员信息时，它将产生与之前相同的结果。

```
{
"members" : {
"members" : [ {
"name" : "Marten Deinum",
"phone" : "00-31-1234567890",
"email" : "marten@deinum.biz"
}, {
"name" : "John Doe",
"phone" : "1-800-800-800",
"email" : "john@doe.com"
}, {
"name" : "Jane Doe",
"phone" : "1-801-802-803",
"email" : "jane@doe.com"
} ]
}
}
```

你可能已经注意到，`RestMemberController` 和 `CourtRestConfiguration` 现在与配方 4-1 中的相同。当调用 `http://localhost:8080/court/members.xml` 时，你将获得 XML。

在没有额外配置的情况下，这是如何实现的？Spring MVC 会检测类路径上的内容；当它自动检测到 JAXB 2、Jackson 和 Rome（参见配方 4-4）时，它会为可用的技术注册相应的 `HttpMessageConverter`。

#### 使用 GSON 生成 JSON

到目前为止，你一直在使用 Jackson 从对象生成 JSON；另一个流行的库是 GSON，Spring 对其提供了开箱即用的支持。要使用 GSON，你需要将其添加到类路径中（代替 Jackson），然后它将被用来生成 JSON。

当使用 Maven 时，添加以下依赖：

```
com.google.code.gson
gson
2.8.0

```

当使用 Gradle 时，添加以下内容：

```
compile 'com.google.code.gson:gson:2.8.0'
```

就像使用 Jackson 一样，这就是使用 GSON 实现 JSON 序列化所需的全部操作。如果你启动应用程序并调用 `http://localhost:8080/court/members.json`，你仍然会收到 JSON，但现在是通过 GSON 生成的。

## 4-3. 使用 Spring 访问 REST 服务

### 问题

你想从第三方（例如 Google、Yahoo 或其他业务合作伙伴）访问 REST 服务，并在 Spring 应用程序中使用其负载。

### 解决方案

在 Spring 应用程序中访问第三方 REST 服务主要围绕使用 Spring 的 `RestTemplate` 类。`RestTemplate` 类的设计原则与许多其他 Spring `*Template` 类（例如 `JdbcTemplate`、`JmsTemplate`）相同，提供了一种简化的方法，并带有执行耗时任务的默认行为。这意味着在 Spring 应用程序中，调用 REST 服务和使用其返回负载的过程得到了简化。



### 工作原理

在描述 `RestTemplate` 类的特性之前，有必要先探究 REST 服务的生命周期，以便了解 `RestTemplate` 类实际执行的工作。通过浏览器最能探究 REST 服务的生命周期，因此请在工作站上打开你常用的浏览器开始操作。首先需要一个 REST 服务端点。你将复用配方 4-2 中创建的端点。该端点应可通过 `http://localhost:8080/court/members.xml`（或 `.json`）访问。当你在浏览器中加载这个 REST 服务端点时，浏览器会执行一个 GET 请求，这是 REST 服务支持的最流行的 HTTP 请求之一。加载 REST 服务后，浏览器会显示类似如下的响应负载：

```

marten@deinum.biz
Marten Deinum
00-31-1234567890

john@doe.com
John Doe
1-800-800-800

jane@doe.com
Jane Doe
1-801-802-803

```

上述负载代表一个格式良好的 XML 片段，这与大多数 REST 服务的响应一致。负载的实际含义高度依赖于具体的 REST 服务。在此例中，XML 标签（`<members>`、`<member>` 等）是由你自行定义的，而每个 XML 标签内包含的字符数据则代表与 REST 服务请求相关的信息。

REST 服务消费者（即你）的任务是了解 REST 服务的负载结构（有时称为词汇表），以便正确处理其信息。尽管上述 REST 服务依赖于可视为自定义词汇表的内容，但一系列 REST 服务通常依赖于标准化词汇表（例如 RSS），这使得 REST 服务负载的处理更加统一。此外，值得注意的是，某些 REST 服务会提供 Web 应用描述语言（WADL）契约，以方便负载的发现和消费。

现在你已经熟悉了使用浏览器进行 REST 服务的生命周期，接下来可以看看如何使用 Spring 的 `RestTemplate` 类将 REST 服务的负载整合到 Spring 应用中。鉴于 `RestTemplate` 类专为调用 REST 服务而设计，其主要方法与 REST 的基础——HTTP 协议的方法（HEAD、GET、POST、PUT、DELETE 和 OPTIONS）紧密相关，这并不令人意外。表 4-1 列出了 `RestTemplate` 类支持的主要方法。

表 4-1. 基于 HTTP 协议请求方法的 RestTemplate 类方法

| 方法 | 描述 |
| --- | --- |
| `headForHeaders(String, Object...)` | 执行 HTTP HEAD 操作 |
| `getForObject(String, Class, Object...)` | 执行 HTTP GET 操作，并将结果作为指定类的类型返回 |
| `getForObject(String, Class, Object...)` | 执行 HTTP GET 操作，并返回一个 `ResponseEntity` |
| `postForLocation(String, Object, Object...)` | 执行 HTTP POST 操作，并返回 location 头的值 |
| `postForObject(String, Object, Class, Object...)` | 执行 HTTP POST 操作，并将结果作为指定类的类型返回 |
| `postForEntity(String, Object, Class, Object...)` | 执行 HTTP POST 操作，并返回一个 `ResponseEntity` |
| `put(String, Object, Object...)` | 执行 HTTP PUT 操作 |
| `delete(String, Object...)` | 执行 HTTP DELETE 操作 |
| `optionsForAllow(String, Object...)` | 执行 HTTP OPTIONS 操作 |
| `execute(String, HttpMethod, RequestCallback, ResponseExtractor, Object...)` | 可执行除 CONNECT 之外的任何 HTTP 操作 |

如表 4-1 所示，`RestTemplate` 类的方法以一系列 HTTP 协议方法（包括 HEAD、GET、POST、PUT、DELETE 和 OPTIONS）为前缀。此外，`execute` 方法作为一个通用方法，可以执行任何 HTTP 操作，包括更冷门的 HTTP 协议 TRACE 方法，但不包括 CONNECT 方法（`execute` 方法底层使用的 `HttpMethod` 枚举不支持该方法）。请注意，REST 服务中最常用的 HTTP 方法是 GET，因为它代表一种安全的信息获取操作（即不修改任何数据）。另一方面，PUT、POST 和 DELETE 等 HTTP 方法旨在修改提供者的信息，因此 REST 服务提供者支持它们的可能性较小。对于需要进行数据修改的情况，许多提供者会选择 SOAP 协议，这是使用 REST 服务的一种替代机制。

现在你已经了解了 `RestTemplate` 类的方法，接下来可以继续调用之前用浏览器调用的同一个 REST 服务，只不过这次使用 Spring 框架的 Java 代码。以下代码展示了一个访问 REST 服务并将其内容输出到 `System.out` 的类：

```
package com.apress.springrecipes.court;
import org.springframework.web.client.RestTemplate;
public class Main {
public static void main(String[] args) throws Exception {
final String uri = "http://localhost:8080/court/members.json";
RestTemplate restTemplate = new RestTemplate();
String result = restTemplate.getForObject(uri, String.class);
System.out.println(result);
}
}
```

警告

某些 REST 服务提供者会根据请求方限制对其数据源的访问。通常，拒绝访问的依据是请求中存在的数据（例如 HTTP 头或 IP 地址）。因此，根据具体情况，即使数据源在另一种媒介中似乎可以正常工作（例如，你可能可以在浏览器中访问 REST 服务，但尝试从 Spring 应用访问同一数据源时却收到拒绝访问的响应），提供者也可能返回拒绝访问的响应。这取决于 REST 提供者设定的使用条款。

第一行声明了在类体内访问 `RestTemplate` 类所需的 `import` 语句。首先需要创建一个 `RestTemplate` 类的实例。接下来，可以看到调用了属于 `RestTemplate` 类的 `getForObject` 方法，如表 4-1 所述，该方法用于执行 HTTP GET 操作——就像浏览器获取 REST 服务负载时执行的操作一样。关于这个方法，有两个重要方面：其响应及其参数。

调用 `getForObject` 方法的响应被赋值给一个 `String` 对象。这意味着你在浏览器中看到的该 REST 服务的相同输出（即 XML 结构）被赋值给了一个 `String`。即使你从未在 Java 中处理过 XML，也很可能意识到将数据作为 Java `String` 进行提取和操作并非易事。换句话说，有些类比 `String` 对象更适合处理 XML 数据（以及其中的 REST 服务负载）。目前只需记住这一点；本章的其他配方将说明如何更好地提取和操作从 REST 服务获取的数据。

传递给 `getForObject` 方法的参数包括实际的 REST 服务端点。第一个参数对应 URL（即端点）声明。请注意，该 URL 与你之前使用浏览器调用时使用的 URL 完全相同。

执行此代码时，输出将与浏览器中的输出相同，只不过现在打印在控制台中。



#### 从参数化 URL 检索数据

上一节展示了如何调用 URI 来检索数据，但如果 URI 需要参数该怎么办？你肯定不希望将参数硬编码到 URL 中。使用 `RestTemplate` 类，你可以使用带有占位符的 URL，这些占位符将在执行时被实际值替换。占位符使用 `{` 和 `}` 定义，这与请求映射的方式相同（参见配方 4-1 和 4-2）。

URI `http://localhost:8080/court/member/{memberId}` 就是这样一个参数化 URI 的例子。要能够调用此方法，你需要为占位符传入一个值。你可以通过使用 `Map` 并将其作为第三个参数传递给 `RestTemplate` 类的 `getForObject` 方法来实现这一点。

```
public class Main {
public static void main(String[] args) throws Exception {
final String uri = "http://localhost:8080/court/member/{memberId}";
Map params = new HashMap();
params.put("memberId", "1");
RestTemplate restTemplate = new RestTemplate();
String result = restTemplate.getForObject(uri, String.class, params );
System.out.println(result);
}
}
```

最后这段代码使用了 `HashMap` 类（Java 集合框架的一部分），并创建了一个包含相应 REST 服务参数的实例，随后将其传递给 `RestTemplate` 类的 `getForObject` 方法。无论是向各种 `RestTemplate` 方法传递一系列 `String` 参数还是单个 `Map` 参数，所获得的结果都是相同的。

#### 以映射对象形式检索数据

除了返回一个 `String` 供应用程序使用之外，你还可以（重新）使用你的 `Members` 和 `Member` 类来映射结果。无需将 `String.class` 作为第二个参数传入，而是传入 `Members.class`，这样响应将被映射到这个类上。

```
package com.apress.springrecipes.court;
import com.apress.springrecipes.court.domain.Members;
import org.springframework.web.client.RestTemplate;
public class Main {
public static void main(String[] args) throws Exception {
final String uri = "http://localhost:8080/court/members.xml";
RestTemplate restTemplate = new RestTemplate();
Members result = restTemplate.getForObject(uri, Members.class);
System.out.println(result);
}
}
```

`RestTemplate` 类使用了与带有 `@ResponseBody` 标记方法的控制器相同的 `HttpMessageConverter` 基础设施。由于 JAXB 2（以及 Jackson）会被自动检测到，因此映射到 JAXB 映射的对象非常容易。

## 4-4\. 发布 RSS 和 Atom 订阅源

### 问题

你希望在 Spring 应用程序中发布 RSS 或 Atom 订阅源。

### 解决方案

RSS 和 Atom 订阅源已成为一种流行的信息发布方式。对这些类型订阅源的访问是通过 REST 服务提供的，这意味着构建 REST 服务是发布 RSS 和 Atom 订阅源的前提条件。除了依赖 Spring 的 REST 支持之外，依赖一个专门设计用于处理 RSS 和 Atom 订阅源特性的第三方库也很方便。这使得 REST 服务更容易发布这种类型的 XML 负载。为此，你将使用 Project Rome，这是一个开源库，可在 [`http://rometools.github.io/rome/`](http://rometools.github.io/rome/) 获取。

提示

尽管 RSS 和 Atom 订阅源通常被归类为新闻订阅源，但它们已经超越了最初仅提供新闻的使用场景。如今，RSS 和 Atom 订阅源被用于以跨平台的方式（即使用 XML）发布与博客、天气、旅行以及许多其他内容相关的信息。因此，如果你需要以跨平台可访问的方式发布任何类型的信息，鉴于其广泛采用（例如，许多应用程序支持它们，许多开发人员了解它们的结构），将其作为 RSS 或 Atom 订阅源发布可能是一个极好的选择。

### 工作原理

你需要做的第一件事是确定要作为 RSS 或 Atom 新闻订阅源发布的信息。这些信息可以位于关系数据库或文本文件中，可以通过 JDBC 或 ORM 访问，甚至可以包含在 Spring Bean 或其他类型的结构中。描述如何获取这些信息超出了本配方的范围，因此我们假设你将使用你认为合适的任何方式来访问它。一旦你确定了要发布的信息，就需要将其结构化为 RSS 或 Atom 订阅源，这就是 Project Rome 发挥作用的地方。

如果你不熟悉 Atom 订阅源的结构，以下代码片段说明了这种格式的一个片段：

```

示例订阅源

2010-08-31T18:30:02Z

John Doe

urn:uuid:60a76c80-d399-11d9-b93C-0003939e0af6

Atom 驱动的机器人横冲直撞

urn:uuid:1225c695-cfb8-4ebb-aaaa-80da344efa6a
2010-08-31T18:30:02Z
一些文本。

```

以下代码片段说明了 RSS 订阅源结构的一个片段：

```

RSS 示例
这是一个 RSS 订阅源的示例
http://www.example.org/link.htm
Mon, 28 Aug 2006 11:12:55 -0400 
Tue, 31 Aug 2010 09:00:00 -0400

项目示例
这是一个项目的示例
http://www.example.org/link.htm

Tue, 31 Aug 2010 09:00:00 -0400

```

正如你从最后两个代码片段中看到的，RSS 和 Atom 订阅源只是 XML 负载，它们依赖一系列元素来发布信息。虽然深入探讨 RSS 或 Atom 订阅源结构的细节本身就需要一本书，但这两种格式都有一系列共同特征；其中最主要的是：

*   它们有一个元数据部分来描述订阅源的内容（例如，Atom 格式的 `<author>` 和 `<title>` 元素，以及 RSS 格式的 `<description>` 和 `<pubDate>` 元素）。
*   它们有重复出现的元素来描述信息（例如，Atom 订阅源格式的 `<entry>` 元素和 RSS 订阅源格式的 `<item>` 元素）。此外，每个重复出现的元素都有自己的一组元素来进一步描述信息。
*   它们有多个版本。RSS 版本包括 0.90、0.91 Netscape、0.91 Userland、0.92、0.93、0.94、1.0 和 2.0。Atom 版本包括 0.3 和 1.0。Project Rome 允许你根据 Java 代码中可用的信息（例如，`String`、`Map` 或其他此类结构）创建订阅源的元数据部分、重复出现的元素以及前面提到的任何版本。

现在你已经了解了 RSS 和 Atom 订阅源的结构，以及 Project Rome 在本配方中的作用，让我们来看一个负责向最终用户呈现订阅源的 Spring MVC 控制器：



```
//FINAL
package com.apress.springrecipes.court.web;
import com.apress.springrecipes.court.feeds.TournamentContent;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;
import java.util.ArrayList;
import java.util.Date;
import java.util.List;
@Controller
public class FeedController {
@RequestMapping("/atomfeed")
public String getAtomFeed(Model model) {
List tournamentList = new ArrayList();
tournamentList.add(TournamentContent.of("ATP", new Date(), "Australian Open", "www.australianopen.com"));
tournamentList.add(TournamentContent.of("ATP", new Date(), "Roland Garros", "www.rolandgarros.com"));
tournamentList.add(TournamentContent.of("ATP", new Date(), "Wimbledon", "www.wimbledon.org"));
tournamentList.add(TournamentContent.of("ATP", new Date(), "US Open", "www.usopen.org"));
model.addAttribute("feedContent", tournamentList);
return "atomfeedtemplate";
}
@RequestMapping("/rssfeed")
public String getRSSFeed(Model model) {
List tournamentList;
tournamentList = new ArrayList();
tournamentList.add(TournamentContent.of("FIFA", new Date(), "World Cup", "www.fifa.com/worldcup/"));
tournamentList.add(TournamentContent.of("FIFA", new Date(), "U-20 World Cup", "www.fifa.com/u20worldcup/"));
tournamentList.add(TournamentContent.of("FIFA", new Date(), "U-17 World Cup", "www.fifa.com/u17worldcup/"));
tournamentList.add(TournamentContent.of("FIFA", new Date(), "Confederations Cup", "www.fifa.com/confederationscup/"));
model.addAttribute("feedContent", tournamentList);
return "rssfeedtemplate";
}
}
```

这个 Spring MVC 控制器包含两个处理器方法。一个名为 `getAtomFeed()`，映射到形如 `http://[host_name]/[app-name]/atomfeed` 的 URL；另一个名为 `getRSSFeed()`，映射到形如 `http://[host_name]/[app-name]/rssfeed` 的 URL。

每个处理器方法都定义了一个 `TournamentContent` 对象的 `List`，其中 `TournamentContent` 对象的支持类是一个 POJO。然后，这个 `List` 被赋值给处理器方法的 `Model` 对象，以便返回的视图可以访问它。每个处理器方法返回的逻辑视图分别是 `atomfeedtemplate` 和 `rssfeedtemplate`。这些逻辑视图在 Spring 配置类中以如下方式定义：

```
package com.apress.springrecipes.court.web.config;
import com.apress.springrecipes.court.feeds.AtomFeedView;
import com.apress.springrecipes.court.feeds.RSSFeedView;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.HandlerMapping;
import org.springframework.web.servlet.config.annotation.EnableWebMvc;
import org.springframework.web.servlet.handler.BeanNameUrlHandlerMapping;
@Configuration
@EnableWebMvc
@ComponentScan(basePackages = "com.apress.springrecipes.court")
public class CourtRestConfiguration {
@Bean
public AtomFeedView atomfeedtemplate() {
return new AtomFeedView();
}
@Bean
public RSSFeedView rssfeedtemplate() {
return new RSSFeedView();
}
...
}
```

如你所见，每个逻辑视图都映射到一个类。每个类负责实现构建 Atom 或 RSS 视图所需的逻辑。如果你还记得第 3 章的内容，当时你使用了完全相同的方法（即使用类）来实现 PDF 和 Excel 视图。

对于 Atom 和 RSS 视图，Spring 提供了两个专门构建在 Project Rome 基础之上的类。这两个类是 `AbstractAtomFeedView` 和 `AbstractRssFeedView`。这些类提供了构建 Atom 或 RSS 提要的基础，而无需处理这些格式的细节。

以下代码展示了实现 `AbstractAtomFeedView` 类的 `AtomFeedView` 类，它用于支持 `atomfeedtemplate` 逻辑视图：

```
package com.apress.springrecipes.court.feeds;
import com.rometools.rome.feed.atom.Content;
import com.rometools.rome.feed.atom.Entry;
import com.rometools.rome.feed.atom.Feed;
import org.springframework.web.servlet.view.feed.AbstractAtomFeedView;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.util.List;
import java.util.Map;
import java.util.stream.Collectors;
public class AtomFeedView extends AbstractAtomFeedView {
@Override
protected void buildFeedMetadata(Map model, Feed feed, HttpServletRequest request) {
feed.setId("tag:tennis.org");
feed.setTitle("Grand Slam Tournaments");
List tournamentList = (List) model.get("feedContent");
feed.setUpdated(tournamentList.stream().map(TournamentContent::getPublicationDate).sorted().findFirst().orElse(null));
}
@Override
protected List buildFeedEntries(Map model, HttpServletRequest request, HttpServletResponse response)
throws Exception {
List tournamentList = (List) model.get("feedContent");
return tournamentList.stream().map(this::toEntry).collect(Collectors.toList());
}
private Entry toEntry(TournamentContent tournament) {
Entry entry = new Entry();
String date = String.format("%1$tY-%1$tm-%1$td", tournament.getPublicationDate());
entry.setId(String.format("tag:tennis.org,%s:%d", date, tournament.getId()));
entry.setTitle(String.format("%s - Posted by %s", tournament.getName(), tournament.getAuthor()));
entry.setUpdated(tournament.getPublicationDate());
Content summary = new Content();
summary.setValue(String.format("%s - %s", tournament.getName(), tournament.getLink()));
entry.setSummary(summary);
return entry;
}
}
```

关于这个类，首先要注意的是，除了实现 Spring 框架提供的 `AbstractAtomFeedView` 类之外，它还从 `com.sun.syndication.feed.atom` 包中导入了几个 Project Rome 类。这样一来，接下来唯一需要做的就是为从 `AbstractAtomFeedView` 类继承的两个方法提供提要的实现细节：`buildFeedMetadata` 和 `buildFeedEntries`。

`buildFeedMetadata` 有三个输入参数：一个 `Map` 对象，代表用于构建提要的数据（即处理器方法内分配的数据，在本例中是一个 `TournamentContent` 对象的 `List`）；一个基于 Project Rome 类的 `Feed` 对象，用于操作提要本身；以及一个 `HttpServletRequest` 对象，以便在需要时操作 HTTP 请求。

在 `buildFeedMetadata` 方法内部，你可以看到对 `Feed` 对象的 setter 方法（例如 `setId`、`setTitle`、`setUpdated`）进行了多次调用。其中两次调用使用了硬编码字符串，另一次调用则使用了在遍历提要数据（即 `Map` 对象）后确定的值。所有这些调用都代表了对 Atom 提要元数据信息的赋值。

注意

如果你想为 Atom 提要的元数据部分分配更多值，或指定特定的 Atom 版本，请查阅 Project Rome 的 API。默认版本是 Atom 1.0。

`buildFeedEntries` 方法也有三个输入参数：一个 `Map` 对象，代表用于构建提要的数据（即处理器方法内分配的数据，在本例中是一个 `TournamentContent` 对象的 `List`）；一个 `HttpServletRequest` 对象，以便在需要时操作 HTTP 请求；以及一个 `HttpServletResponse` 对象，以便在需要时操作 HTTP 响应。同样重要的是要注意，`buildFeedEntries` 方法返回一个对象的 `List`，在本例中对应一个基于 Project Rome 类的 `Entry` 对象的 `List`，其中包含 Atom 提要的重复元素。



在 `buildFeedEntries` 方法内部，你可以观察到，通过访问 `Map` 对象来获取在 handler 方法中赋值的 `feedContent` 对象。完成此操作后，会创建一个空的 `Entry` 对象 `List`。接着，对 `feedContent` 对象执行循环，该对象包含一个 `TournamentContent` 对象的 `List` 列表，并为每个元素创建一个 `Entry` 对象，并将其赋值给顶层的 `Entry` 对象 `List`。循环结束后，该方法返回一个填充好的 `Entry` 对象 `List`。

注意

如果你想为 Atom feed 的重复元素部分分配更多值，请查阅 Project Rome 的 API。

部署前面的类后，除了前面提到的 Spring MVC 控制器之外，访问形如 `http://[host_name]/[app-name]/atomfeed.atom`（或 `http://[host_name]/atomfeed.xml`）的 URL 将产生以下响应：

```

Grand Slam Tournaments
tag:tennis.org
2017-04-19T01:32:52Z

Australian Open - Posted by ATP
tag:tennis.org,2017-04-19:5
2017-04-19T01:32:52Z
Australian Open - www.australianopen.com

Roland Garros - Posted by ATP
tag:tennis.org,2017-04-19:6
2017-04-19T01:32:52Z
Roland Garros - www.rolandgarros.com

Wimbledon - Posted by ATP
tag:tennis.org,2017-04-19:7
2017-04-19T01:32:52Z
Wimbledon - www.wimbledon.org

US Open - Posted by ATP
tag:tennis.org,2017-04-19:8
2017-04-19T01:32:52Z
US Open - www.usopen.org

```

将注意力转向负责构建 RSS feed 的前一个 Spring MVC 控制器中的另一个 handler 方法——`getRSSFeed`，你会发现其过程与刚才描述的构建 Atom feed 的过程类似。该 handler 方法也会创建一个 `TournamentContent` 对象的 `List`，然后将其赋值给 handler 方法的 `Model` 对象，以便返回的视图可以访问它。不过，在这种情况下，返回的逻辑视图现在对应一个名为 `rssfeedtemplate` 的视图。如前所述，此逻辑视图映射到一个名为 `RssFeedView` 的类。

以下代码展示了实现 `AbstractRssFeedView` 类的 `RssFeedView` 类：

```
package com.apress.springrecipes.court.feeds;
import com.rometools.rome.feed.rss.Channel;
import com.rometools.rome.feed.rss.Item;
import org.springframework.web.servlet.view.feed.AbstractRssFeedView;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.util.List;
import java.util.Map;
import java.util.stream.Collectors;
public class RSSFeedView extends AbstractRssFeedView {
@Override
protected void buildFeedMetadata(Map model, Channel feed, HttpServletRequest request) {
feed.setTitle("World Soccer Tournaments");
feed.setDescription("FIFA World Soccer Tournament Calendar");
feed.setLink("tennis.org");
List tournamentList = (List) model.get("feedContent");
feed.setLastBuildDate(tournamentList.stream().map( TournamentContent::getPublicationDate).sorted().findFirst().orElse(null) );
}
@Override
protected List buildFeedItems(Map model, HttpServletRequest request, HttpServletResponse response)
throws Exception {
List tournamentList = (List) model.get("feedContent");
return tournamentList.stream().map(this::toItem).collect(Collectors.toList());
}
private Item toItem(TournamentContent tournament) {
Item item = new Item();
item.setAuthor(tournament.getAuthor());
item.setTitle(String.format("%s - Posted by %s", tournament.getName(), tournament.getAuthor()));
item.setPubDate(tournament.getPublicationDate());
item.setLink(tournament.getLink());
return item;
}
}
```

关于这个类，首先要注意的是，除了实现 Spring 框架提供的 `AbstractRssFeedView` 类之外，它还从 `com.sun.syndication.feed.rss` 包中导入了几个 Project Rome 类。完成此操作后，接下来唯一需要做的就是为从 `AbstractRssFeedView` 类继承的两个方法提供 feed 的实现细节：`buildFeedMetadata` 和 `buildFeedItems`。`buildFeedMetadata` 方法在本质上与用于构建 Atom feed 的同名方法相似。请注意，`buildFeedMetadata` 方法操作的是一个基于 Project Rome 类的 `Channel` 对象（用于构建 RSS feed），而不是用于构建 Atom feed 的 `Feed` 对象。对 `Channel` 对象调用的 setter 方法（例如 `setTitle`、`setDescription`、`setLink`）表示对 RSS feed 元数据信息的赋值。`buildFeedItems` 方法（其名称与 Atom 对应的 `buildFeedEntries` 不同）之所以如此命名，是因为 Atom feed 的重复元素称为 entries，而 RSS feed 的重复元素称为 items。除了命名约定之外，它们的逻辑是相似的。

在 `buildFeedItems` 方法内部，你可以观察到，通过访问 `Map` 对象来获取在 handler 方法中赋值的 `feedContent` 对象。完成此操作后，会创建一个空的 `Item` 对象 `List`。接着，对 `feedContent` 对象执行循环，该对象包含一个 `TournamentContent` 对象的 `List`，并为每个元素创建一个 `Item` 对象，并将其赋值给顶层的 `Item` 对象 `List`。循环结束后，该方法返回一个填充好的 `Item` 对象 `List`。

注意

如果你想为 RSS feed 的元数据和重复元素部分分配更多值，以及指定特定的 RSS 版本，请查阅 Project Rome 的 API。默认版本是 RSS 2.0。

当你部署前面的类时，除了前面提到的 Spring MVC 控制器之外，访问形如 `http://[host_name]/rssfeed.rss`（或 `http://[host_name]/rssfeed.xml`）的 URL 将产生以下响应：

```

World Soccer Tournaments
tennis.org
FIFA World Soccer Tournament Calendar
Wed, 19 Apr 2017 01:32:31 GMT

World Cup - Posted by FIFA
www.fifa.com/worldcup/
Wed, 19 Apr 2017 01:32:31 GMT
314861_4_EnFIFA

U-20 World Cup - Posted by FIFA
www.fifa.com/u20worldcup/
Wed, 19 Apr 2017 01:32:31 GMT
314861_4_EnFIFA

U-17 World Cup - Posted by FIFA
www.fifa.com/u17worldcup/
Wed, 19 Apr 2017 01:32:31 GMT
314861_4_EnFIFA

Confederations Cup - Posted by FIFA
www.fifa.com/confederationscup/
Wed, 19 Apr 2017 01:32:31 GMT
314861_4_EnFIFA

```

## 总结

在本章中，你学习了如何使用 Spring 开发和访问 REST 服务。REST 服务与 Spring MVC 紧密相关，控制器负责分发对 REST 服务的请求，以及访问第三方 REST 服务以将这些信息用于应用程序内容。

你学习了 REST 服务如何利用 Spring MVC 控制器中使用的注解，包括用于指示服务端点的 `@RequestMapping`，以及用于指定过滤服务负载的访问参数的 `@PathVariable`。此外，你还了解了 Spring 的 XML 编组器，例如 `Jaxb2Marshaller`，它允许将应用程序对象转换为 XML 并作为 REST 服务的负载输出。你还学习了 Spring 的 `RestTemplate` 类，以及它如何支持包括 HEAD、GET、POST、PUT 和 DELETE 在内的一系列 HTTP 方法——所有这些方法都允许你直接从 Spring 应用程序的上下文中访问第三方 REST 服务并对其执行操作。

最后，你探索了如何通过利用 Project Rome API 在 Spring 应用程序中发布 Atom 和 RSS feed。



