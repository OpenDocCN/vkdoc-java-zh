# 26. AJAX 组件

Michael Müller^(1 )

(1)德国，北莱茵-威斯特法伦州，布吕尔

在普通请求中，浏览器会请求整个页面。相比之下，AJAX（异步 JavaScript 和 XML）通常用于通过一个名为 `XMLHttpRequest` 的特殊对象仅查询少量数据。该对象用于在后台（异步）查询数据，同时页面仍然显示。根据响应，页面的部分内容可能会被重新渲染。在 JSF 中，这被称为*部分请求*。

当这个术语首次被使用时，响应是一些 XML 数据。如今，响应负载最常用的格式是 JSON 字符串，它比 XML 文档更紧凑。或者，如果需要，开发人员可以发送纯文本作为负载。但幕后的技术仍然被称为 AJAX。

## 使用 AJAX 实现即时反馈

对对话框进行 AJAX 化有助于改善用户体验。例如，每个输入组件的内容都可以发送到服务器，并向用户提供即时反馈。尽管 AJAX 对于较小的对话框（例如 Books 的类别编辑器）也很有用，但我将通过注册表单来介绍 JSF 的 AJAX。

JSF 提供了一个特殊的标签 `<f:ajax>`。对组件进行 AJAX 化的一种方法是将此标签嵌入到组件中。以注册表单为例，我们只需将其放入 `<h:inputText>` 元素中，如清单 26-1 所示。



###### 清单 26-1 注册表单，AJAX 化（节选）

```
 1   <h:form>

 3     <div class="inputPart">
 4       <h:outputLabel for="firstName"
 5                      value="#{msg.lblFirstName}"
 6                      styleClass="label"/>
 7       <h:message id="msgFirstName" for="firstName"
 8                  styleClass="errorMessage"/>
 9       <h:inputText id="firstName"
10                    value="#{register.account.firstName}"
11                    styleClass="inputFull">
12         <f:ajax render="@this msgFirstName"/>                                                                
13       </h:inputText>
14     </div>

16     <div class="inputPart">
17       <h:outputLabel for="lastName"
18                      value="#{msg.lblLastName}"
19                      styleClass="label"/>
20       <h:message id="msgLastName"
21                  for="lastName"
22                  styleClass="errorMessage"/>
23       <h:inputText id="lastName"
24                    value="#{register.account.lastName}"
25                    styleClass="inputFull">
26          <f:ajax render="@this msgLastName"/>
27        </h:inputText>
28     </div>

30     <div class="inputPart">
31       <h:outputLabel for="email"
32                      value="#{msg.lblEmail}"
33                      styleClass="label"/>
34       <h:message id="msgEmail"
35                  for="email"
36                  styleClass="errorMessage"/>
37       <h:inputText id="email"
38                    value="#{register.account.email}"
39                    styleClass="inputFull">
40         <f:ajax render="@this msgEmail"/>
41       </h:inputText>
42     </div>

44     <div class="buttonBar">
45       <h:commandButton value="#{msg.btnRegister}"                                                                
46                        action="#{register.register()}"
47                        styleClass="button"/>
48     </div>

50   </h:form>
```

该表单包含三个 AJAX 化元素（见第 12、26 和 40 行）。`render` 属性告诉 JSF 当数据从服务器返回时应渲染哪些组件。`@this` 是一个特殊关键字，指代其所在的（父级）组件。`msgXXX` 是关联消息的名称。

当用户离开某个输入字段时，JSF 会发起一个异步请求，并将该字段的内容发送到服务器。该值将被转换、验证，如果验证成功，则传输到模型。然后服务器会发回上述两个组件（每个 `@this` 和消息）的新内容进行渲染。通过这种方式，我们为用户提供了即时反馈，类似于桌面应用程序。

使用 ajax 标签非常简单，并且隐藏了所有 JavaScript 代码。JSF 完全处理服务器端，因此无需特殊编程。

该表单展示了 `<f:ajax>` 标签非常基础的使用方式，几乎使用了默认值。例如，我们省略了事件以及执行此请求的组件。默认情况下，`<f:ajax>` 的父组件会在其 `change` 事件期间被执行（发送到服务器）。默认事件取决于组件的类型。如果组件包含值（例如 `inputText`、`selectOneMenu` 等），则默认事件是 `onChange`。对于被点击的组件，如按钮或链接，默认事件是 `onClick`。

所有这些默认值都可以更改。假设我们想对 `repeatPassword` 字段的 `onBlur` 事件做出响应，并执行两个密码字段：

```
<f:ajax event="blur" execute="@this password" render="msgRepeatPassword"/>
```

我们将在讨论更复杂的表单时详细介绍这些及其他选项和属性。

除了将此标签嵌套在组件标签内，还可以将一个或多个组件嵌套在 `<f:ajax>` 标签内，如清单 26-2 所示。

###### 清单 26-2 AJAX 作为其他组件的父级

```
01 <f:ajax>
02   <component1/>                                                                
03   <component2/>
04   ...
05 </f:ajax>
```

除了使用 ajax 标签，您还可以使用 JSF JavaScript API——例如，在使用 HTML 友好标记实现表单时，或者当您希望将部分请求与其他脚本结合使用时，例如执行一些 JavaScript 验证，然后再发送部分请求。

清单 26-3 展示了一个 HTML 友好标记的示例，省略了 JSF 标签。

###### 清单 26-3 使用 HTML 友好标记的 AJAX

```
<input type="input"
       jsf:value="#{register.password}"
       onchange="jsf.ajax.request(this, event);"/>
```

HTML 使用 `onchange` 来响应 `change` 事件。在 JSF 的 ajax 标签中，`on` 前缀被省略了。

根据需求，可以直接通过 JavaScript 接收和处理响应，而无需使用简单的 ajax 标签。请参见清单 26-4。

###### 清单 26-4 自定义 AJAX 响应处理程序的框架

```
 1   <script>
 2   function handleAjax(data) {
 3     var status = data.status;

 5     switch (status) {
 6       case "begin":
 7           // Ajax 请求开始
 8           break;

10       case "complete":
11           // Ajax 响应完成
12           break;

14       case "success":
15           // Ajax 响应已处理，HTML DOM 已更新
16           alert(data.responseText);
17           break;
18     }
19   }

21   jsf.ajax.addOnEvent(handleAjax);
22   </script>
```

清单 26-4 展示了此类处理程序的原理。第 21 行将 `handleAjax` 函数注册到 `onEvent` 处理程序。现在，该函数将根据其当前状态被多次调用：在请求开始时（第 6 行），在响应完成时（第 10 行），以及在成功处理响应后 HTML DOM 刚刚更新时（第 14 行）。在此示例中，我们简单地弹出了响应的文本。

使用 JavaScript 方法可能对特殊需求有所帮助。我在此提及它只是为了完整性。（在 Java EE 8 教程中，有一页关于 JavaScript AJAX 的内容，网址为 [`javaee.github.io/tutorial/jsf-ajax010.html`](https://javaee.github.io/tutorial/jsf-ajax010.html)。）我们将使用 JSF ajax 标签。

清单 26-1 仅展示了 ajax 标签的基本用法。除了定义在响应期间要渲染的元素外，我们还可以定义要响应的事件，或者通过调用服务器端的 setter 方法来处理的 GUI 元素。我们还可以定义一个 *监听器*——一个将在服务器端调用的方法。我将在本书后面介绍 Alumni 时解释这些功能。

## 总结

AJAX 可用于向服务器发送部分请求。由于在此类请求-响应周期中只使用页面的一小部分，因此通信通常执行得很快。此技术允许将单个字段传输到数据模型，和/或向用户提供即时反馈。

将 JSF 应用程序 AJAX 化就像向组件添加 `<f:ajax>` 标签一样简单。由于默认值的存在，这在许多情况下已经足够。为了更全面的控制，应用程序开发人员可以添加属性来覆盖默认值。

部分请求也可以由脚本发起。JSF 提供了相应的库。

© Michael Müller 2018

Michael Müller, Practical JSF in Java EE 8 , `doi.org/10.1007/978-1-4842-3030-5_27`



