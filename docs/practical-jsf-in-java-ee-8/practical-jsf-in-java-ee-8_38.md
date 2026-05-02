# 27. 构建复合组件

Michael Müller¹

(1)德国北莱茵-威斯特法伦州布吕尔

还记得第 24 章末尾提出的问题吗？是否可以预定义并复用这种相似的输入结构？本章将展示如何构建此类组件。

在 JSF 2.0 之前，你仍然可以创建自己的组件。这些自定义组件遵循特殊约定，通过 Java 代码以编程方式实现行为。有时这对于创建具有全新行为的组件很有用。

从 JSF 2.0 开始，引入了一项新特性：*复合组件*。借助此特性，你可以直接使用 Facelets 将现有元素组合成可复用的组件——你可以将它们视为轻量级的自定义组件。

## 转换为组合

在注册表单中，几乎每个数据字段都使用了标签、输入字段和消息。目标是将这一系列连贯的组件转换为一个组合——即一个复合组件。

清单 27-1 展示了 firstName 的输入部分。

###### 清单 27-1 firstName 的输入部分

```
  1     <div class="inputPart">
  2       <h:outputLabel for="firstName"
  3                      value="#{msg.lblFirstName}"
  4                      styleClass="label"/>
  5       <h:inputText id="firstName"
  6                    value="#{register.account.firstName}"
  7                    styleClass="inputMedium">
  8         <f:ajax render="@this msgFirstName"/>
  9       </h:inputText>
10       <h:message id="msgFirstName" for="firstName"
11                  styleClass="errorMessage"/>                                                                                      
12     </div>
```

这段代码由标签、一个支持 AJAX 的输入字段和一条消息组成，全部嵌套在一个 div 中。同样的结构也适用于 lastName。Email 类似，但包含一个额外的验证器。我们将首先关注名字部分，以创建一个简单版本的可复用复合组件。

现在，让我们将清单 27-1 中显示的部分视为一个组件。它显示一个标签，并为变量提供一个输入字段。如果我们想将其用于姓氏，就必须从组件外部交换这两个值。ajax 和 message 标签仅在组件内部使用，无需从外部向这些标签传递任何值。

如果我们想将此代码片段转换为组件，就需要定义一些内容，以便将标签文本和输入变量传递进去。我们需要定义一个包含两个属性的接口。对于复合组件的接口定义，JSF 提供了复合库中的一些特殊标签。通常，相关命名空间 [`xmlns.jcp.org/jsf/composite`](http://xmlns.jcp.org/jsf/composite) 会被分配给别名 cc。

除了接口之外，清单 27-1 将成为实现部分。我们需要将具体的表达式替换为接口中定义的两个表达式。由于 id 不再与具体值相关，我们可以选择一些通用的名称。

哇！看起来要做的事情很多。但尽管听起来可能很复杂，实际上只需做一点改动，如该组件的代码所示，见清单 27-2。

###### 清单 27-2 作为复合组件的输入部分

```
 1   <?xml version='1.0'  encoding='UTF-8'  ?>
 2   <html xmlns="http://www.w3.org/1999/xhtml"
 3         xmlns:cc="http://xmlns.jcp.org/jsf/composite"
 4         xmlns:h="http://xmlns.jcp.org/jsf/html"
 5         xmlns:f="http://xmlns.jcp.org/jsf/core">                                                                                      

 7     <!-- 接口 -->
 8     <cc:interface>
 9       <cc:attribute name="label"/>
10       <cc:attribute name="value"/>
11     </cc:interface>

13     <!-- 实现 -->
14     <cc:implementation>
15       <div class="inputPart">
16         <h:outputLabel for="input"
17                        value="#{cc.attrs.label}"
18                        styleClass="label"/>
19         <h:inputText id="input"
20                      value="#{cc.attrs.value}"
21                      requiredMessage="#{msg.msgValueRequired}"
22                      styleClass="inputMedium">
23           <f:ajax render="@this msgInput"/>
24         </h:inputText>
25         <h:message id="msgInput" for="input" styleClass="errorMessage"/>
26       </div>
27     </cc:implementation>
28   </html>
```

在 `<cc:implementation>` 标签内，你会看到转换后的代码片段。它看起来确实很相似。但现在我们不再使用引用标签和输入字段的具体值，而是使用 `#{cc.attrs.XXX}`，其中 XXX 代表接口的每个属性。

虽然 HTML 已经就绪，但我们还需要完成一些（简单的）任务才能使用此组件。首先，JSF 会在特定位置查找组件。还记得 webapp 文件夹下的 resources 文件夹吗？我们必须将组件放入此文件夹或其他有效的资源位置。让我们创建一个名为 components 的子文件夹，并将文件保存到该位置。我们将组件命名为 LabeledText。

到目前为止，我们已经准备好了组件。现在让我们使用它。首先，我们需要通过一个特殊的命名空间来引用我们的组件文件：[`xmlns.jcp.org/jsf/composite`](http://xmlns.jcp.org/jsf/composite)。我们需要附加用于存储组件的 resources 下的文件夹。我们选择了 components，因此需要添加它。并且，与命名空间声明的惯例一样，我们必须使用一个别名。我为我的组件选择了 mm。因此，整个命名空间声明变为：

```
xmlns:mm="http://xmlns.jcp.org/jsf/composite/components"
```

清单 27-3 展示了我们的页面以及如何使用我们的组件。

###### 清单 27-3 使用复合组件的重构页面

```
 1   <?xml version='1.0' encoding='UTF-8' ?>
 2   <!DOCTYPE html>
 3   <ui:composition xmlns:ui="http://xmlns.jcp.org/jsf/facelets"
 4                   template="/common/alumniTemplate.xhtml"
 5                   xmlns:h="http://xmlns.jcp.org/jsf/html"
 6                   xmlns:c="http://java.sun.com/jsp/jstl/core"
 7                   xmlns:f="http://xmlns.jcp.org/jsf/core"
 8                   xmlns:mm="http://xmlns.jcp.org/jsf/composite/components">

10     <ui:define name="content">
11       <h1>注册</h1>

13       <h:form>

15         <mm:LabeledText label="#{msg.lblFirstName}"
16                         value="#{register.accountRequest.firstName}"  />
17         <mm:LabeledText label="#{msg.lblLastName}"
18                         value="#{register.accountRequest.lastName}"  />

20         [为简洁起见，省略了其他标记]

22       </h:form>
23     </ui:define>
24   </ui:composition>
```

上面的清单展示了如何将我们的组件用于名字和姓氏的输入部分。我们只需要通过命名空间别名加上我们选择的文件名来引用组件（第 15–18 行），然后通过我们在接口中定义的名称向每个组件传递两个值。代码变得非常简短和简洁。每个组件（firstName、lastName）只需要 2 行代码，而不是像清单 27-2 中那样需要 12 行。真的很棒，不是吗？



## 为组合组件启用子元素

接下来我们需要处理电子邮件输入组件。除了目前已经使用的部分外，我们还需要考虑验证器。一种可能的解决方案是在组件内部定义一个验证器。但可能存在多种不同的验证器用于验证输入。如果全部考虑在内，可能会导致代码混乱，需要编写条件语句来显示无验证器或仅显示其中一个。如果我们能像处理其他组件一样，将验证器或其他组件嵌套到 `labeledText` 中，会更加灵活。请参见清单 27-4。

###### 清单 27-4 使用 LabeledText 组件

```
1   <mm:LabeledText label="#{msg.lblEmail}"
2                   value="#{register.accountRequest.email}">
3     <f:validator validatorId="EmailValidator" for="input"/>
4   </mm:LabeledText>
```

在清单 27-4 中，我简单地将验证器嵌套到了 `labeledText` 中，就像我们在原始注册表单中将验证器嵌套到 `inputText` 标签中一样。然而，如果我们在注册页面中使用这段代码运行应用程序，验证器将不会被调用。

为什么不会呢？想象一个由多个输入元素组成的组合组件——这是一个常见场景。验证器是针对这些元素中的哪一个呢？是的，我们为内部的 `inputText` 使用了 `id input`。就像 Java 方法中的局部变量一样，我们无法从外部访问内部元素。相反，我们必须定义一种将验证器转发到所需内部元素的方式。这正是 `editableValueHolder` 的作用。顾名思义，这个标签用于链接到负责保存值的内部组件。但不仅如此，它还可以指向多个目标：

```
<cc:editableValueHolder  name="input"/>
```

`name` 是我们在验证器的 `for` 属性中需要使用的名称。通过定义目标，我们可以将“外部名称”重定向到内部组件：

```
<cc:editableValueHolder name="input" targets="input"/>
```

如果省略 `targets` 属性，则默认与 `name` 的定义相同。因此，上述代码在语义上与之前相同。但我们也可以使用不同的名称：

```
<cc:editableValueHolder name="email" targets="input"/>
```

这定义了一个可以在组件外部使用的名称及其内部目标。现在，我们需要在验证器中声明 `for="email"`。在大多数 JSF 教程或代码片段中，你会发现 `editableValueHolder` 被嵌套在 `<cc:interface>` 标签内。但你也可以将其放在 `<cc:implementation>` 中。我建议这样做，因为它可以实现更灵活的用法。

请查看清单 27-5 中的 Facelets 片段。

###### 清单 27-5 为组合组件使用 id

```
1   <mm:LabeledText id="myId" label="#{msg.lblEmail}"
2                   value="#{register.accountRequest.email}">
3     <f:validator validatorId="EmailValidator" for="myId"/>
4   </mm:LabeledText>
```

我们为元素定义了一个 `id`。并且像往常一样，我们在验证器的 `for` 属性中使用了这个 `id`。到目前为止，我们还不能这样使用，因为我们的 `editableValueHolder` 的名称为 `"email"`，而我们需要使用这个名称。难道我们不能将组件的 `id` 用作 `editableValueHolder` 的名称吗？好消息是，如果我们在 `implementation` 中使用它，这是可行的。EL 有两个预定义变量指向 `id`：`#{cc.attrs.id}` 或简单的 `#{cc.id}`。请参见图 27-6。

###### 清单 27-6 使用变量 id 作为 editableValueHolder 的 LabeledText 组件

```
 1   <?xml version='1.0' encoding='UTF-8' ?>
 2   <html xmlns="http://www.w3.org/1999/xhtml"
 3         xmlns:cc="http://xmlns.jcp.org/jsf/composite"
 4         xmlns:h="http://xmlns.jcp.org/jsf/html"
 5         xmlns:f="http://xmlns.jcp.org/jsf/core">

 7     <!-- INTERFACE -->
 8     <cc:interface>
 9       <cc:attribute name="label"/>
10       <cc:attribute name="value"/>
11     </cc:interface>

13     <!-- IMPLEMENTATION -->
14     <cc:implementation>
15       <cc:editableValueHolder name="#{cc.id}" targets="input"/>
16       <div class="inputPart">
17         <h:outputLabel for="input"
18                        value="#{cc.attrs.label}"                                                                
19                        styleClass="label"/>
20         <h:message id="msgInput" for="input" styleClass="errorMessage"/>
21         <h:inputText id="input"
22                      value="#{cc.attrs.value}"
23                      requiredMessage="#{msg.msgValueRequired}"
24                      styleClass="inputMedium">
25           <f:ajax render="@this msgInput"/>
26         </h:inputText>
27       </div>
28     </cc:implementation>
29   </html>
```

使用该组件后，最终的注册表单将变得相当简洁。请参见清单 27-7。

###### 清单 27-7 使用组合组件的注册表单（节选）

```
 1   <?xml version='1.0' encoding='UTF-8' ?>
 2   <!DOCTYPE html>
 3   <ui:composition xmlns:ui="http://xmlns.jcp.org/jsf/facelets"
 4                   template="/common/alumniTemplate.xhtml"
 5                   xmlns:h="http://xmlns.jcp.org/jsf/html"
 6                   xmlns:c="http://java.sun.com/jsp/jstl/core"
 7                   xmlns:mm="http://xmlns.jcp.org/jsf/composite/components"
 8                   xmlns:f="http://xmlns.jcp.org/jsf/core">

10     <ui:define name="content">
11       <h1>注册</h1>

13       <h:form>

15         <mm:LabeledText label="#{msg.lblFirstName}"
16                         value="#{register.accountRequest.firstName}"  />
17         <mm:LabeledText label="#{msg.lblLastName}"
18                         value="#{register.accountRequest.lastName}"/>

20         <mm:LabeledText id="email" label="#{msg.lblEmail}"
21                         value="#{register.accountRequest.email}">
22           <f:validator validatorId="EmailValidator" for="email"/>
23         </mm:LabeledText>

25         [为简洁起见，省略其他字段]                                                                

27         <div class="buttonBar">
28           <h:commandButton value="#{msg.btnRegister}"
29                            action="#{register.register()}"
30                            styleClass="button"/>
31         </div>

33       </h:form>
34     </ui:define>
35   </ui:composition>
```

## 传入验证方法

对于密码字段的多组件验证，Alumni 使用了一个验证方法（参见第 25 章）。这种简单的方法无法迁移到通用验证器中，因为它需要了解另一个密码字段。由于我们无法像处理电子邮件那样使用子组件进行验证，因此需要将对验证方法的引用传入组合组件。

其原理应该类似于传入输入字段的值。请参见清单 27-8。



###### 清单 27-8 传入验证方法的原则（故障示例）

```
 1    <cc:interface>
 2      <cc:attribute name="value"/>
 3      <cc:attribute name="validator"/>
 4   </cc:interface>

 6   <cc:implementation>
 7     ...
 8     <h:inputSecret id="input"
 9                    value="#{cc.attrs.value}"
10                   validator="#{cc.attrs.validator}"
11                   ...>
12      ...            
13    </h:inputText>
14    ...
15  </cc:implementation>
```

不要尝试上述代码——它能演示目标，但无法正常工作。在第 2 行，我们定义了一个属性（value）用于将简单字符串传入组件。假设我们要传入一个 `Account` 类的对象，它比简单字符串更复杂。我们需要告知 JSF 该对象的完全限定类型。根据经验法则，当你在 Java 代码中也需要使用完全限定类时，或者需要导入该类以省略包名时，就使用完全限定类：

```
<cc:attribute name="account" type="de.muellerbruehl.alumni.business.dto.Account">
```

要传入一个方法，我们也需要告知 JSF。在这种情况下，我们需要添加一个带有相应属性的方法签名：

```
<cc:attribute name="validator"
          method-signature="void validate(
          javax.faces.context.FacesContext,
          javax.faces.component.UIComponent,
          Object)"/>
```

我们还需要声明返回类型和参数类型。对于没有导入语句的文件，这采用标准的 Java 方式。因此，对于非简单对象，我们需要传入完全限定的类名。至于方法名，我们可以自由选择任何有效的名称——这无关紧要。

通常，在接口部分，我们可以复用内部组件的属性名。但对于验证器，我们需要选择一个不同的名称才能使其正常工作。我将其命名为 `validationMethod`。

## 通用输入组件

要获得一个通用输入组件，我们必须添加一些可以从外部传入的属性。清单 27-9 展示了 Alumni 项目中完整的 `LabeledSecretValidate` 复合组件。它是 `LabeledTextValidate` 的兄弟组件，多了一个特定于 `inputSecret` 的属性 `redisplay`。

###### 清单 27-9 完整的复合组件 InputSecretValidate

```
01   <?xml version='1.0' encoding='UTF-8' ?>
02   <html xmlns="http://www.w3.org/1999/xhtml"
03         xmlns:cc="http://xmlns.jcp.org/jsf/composite"
04         xmlns:h="http://xmlns.jcp.org/jsf/html"
05         xmlns:f="http://xmlns.jcp.org/jsf/core">

07     <!-- 接口 -->
08     <cc:interface>
09       <cc:attribute name="label" />
10       <cc:attribute name="value"/>
11       <cc:attribute name="required" default="true"/>
12       <cc:attribute name="componentStyleClass" default="inputPart"/>
13       <cc:attribute name="labelStyleClass" default="label"/>
14       <cc:attribute name="textStyleClass" default="inputMedium"/>
15       <cc:attribute name="messageStyleClass" default="errorMessage"/>
16       <cc:attribute name="readonly" default="false"/>
17       <cc:attribute name="disabled" default="false"/>
18       <cc:attribute name="redisplay" default="false"/>
19       <cc:attribute name="renderElement" default="@this"/>
20       <cc:attribute name="validationMethod"
21                     method-signature="void validate(
22                     javax.faces.context.FacesContext,
23                     javax.faces.component.UIComponent,
24                     Object)"/>
25       <cc:actionSource name="input"/>
26     </cc:interface>

28     <!-- 实现 -->
29     <cc:implementation>
30       <cc:editableValueHolder name="#{cc.id}" targets="input"/>    

32       <div class="#{cc.attrs.componentStyleClass}">                                                                

34         <h:outputLabel for="input"
35                        value="#{cc.attrs.label}"
36                        styleClass="#{cc.attrs.labelStyleClass}"/>

38         <h:inputSecret id="input"
39                        value="#{cc.attrs.value}"
40                        validator="#{cc.attrs.validationMethod}"
41                        required="#{cc.attrs.required}"
42                        requiredMessage="#{msg.msgValueRequired}"
43                        readonly="#{cc.attrs.readonly}"
44                        disabled="#{cc.attrs.disabled}"
45                        redisplay="#{cc.attrs.redisplay}"
46                        styleClass="#{cc.attrs.textStyleClass}">
47           <f:ajax event="change" render="#{cc.attrs.renderElement} msgInput"/>
48         </h:inputSecret>

50         <h:message id="msgInput"
51                    for="input"
52                    styleClass="#{cc.attrs.messageStyleClass}"/>

54       </div>

56     </cc:implementation>
57   </html>
```

注意接口部分：如果需要，可以为属性定义合理的默认值。如果你不需要使用不同于默认值的值，可以在使用该组件的 HTML 文件中省略该属性。

你可能会问，为什么组件名称都以 `Validate` 结尾。如果我们不传入验证方法，就不会进行验证，即使我们在这里使用了 AJAX，也不会有任何值被传输到模型。我们需要么传入一个虚拟验证方法（方法体为空），要么对缺失的方法进行特殊处理。在这种情况下，我们必须省略 `validator` 属性。这种处理会使组件变得臃肿，因此 Alumni 项目使用了另外两个组件 `LabeledText` 和 `LabeledSecret`，它们不具备传入验证方法的能力。

## 总结

当多个组件以类似方式一起使用时，可以将它们组合成一个可复用的复合组件。一个复合组件由一个（可选的）接口和一个实现组成。本章演示了如何将现有代码转换为这样的组件。

需要特别注意如何允许复合组件拥有子组件。我们需要定义哪个（些）内部组件充当可编辑值持有者。

接口定义了可以传入复合组件的属性。对于非简单类型，需要告知 JSF 属性的类型，或者对于方法，需要告知方法签名。

最后，本章展示了一个可复用的通用输入组件。

© Michael Müller 2018

Michael Müller, Practical JSF in Java EE 8 , `doi.org/10.1007/978-1-4842-3030-5_28`

