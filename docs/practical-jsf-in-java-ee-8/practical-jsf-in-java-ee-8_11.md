# 4. 表达式语言

Michael Müller^(1 )

(1)德国，北莱茵-威斯特法伦州，布吕尔

在构建 TinyCalculator 时，我们使用了一些特殊的表达式，这些表达式以分隔符 #{ 和 } 开头。这些始终采用 #{expr} 形式的表达式由表达式语言（EL）定义。JSR 341：表达式语言 3.0 是 Java EE 8 的一部分（请访问 [`jcp.org/en/jsr/detail?id=341`](https://jcp.org/en/jsr/detail%3Fid=341) 了解更多信息）。同一版本也曾是 Java EE 7 的一部分。

## 统一表达式语言

最初，表达式语言是为 JSP 定义的，使用 ${expr} 形式（以 $ 开头）。随后 JSF 引入了一种略有不同的语法。主要目的是支持更高级别的 JSF 生命周期。这种 JSF EL 使用以 ' 开头的表达式，正如我们在 TinyCalculator 中所做的那样。

从 Java EE 5 开始，JSP EL 和 JSF EL 变得统一。因此，你有时可能会读到关于统一表达式语言的内容。尽管现在表达式已经统一，但两种格式约定（以 $ 开头和以 # 开头）都得到支持，即使在使用 JSP 作为 VDL 时，开头的字符仍然表示略有不同的行为。${expr}（其中 expr 是 expression 的缩写）在页面编译时求值（*即时表达式*），而 #{expr} 在运行时求值（*延迟表达式*）。请记住，JSP 被编译为 Servlet。并且 JSP 仍然作为 JSF 的 VDL 得到支持。使用现代的 Facelets VDL 时，页面定义总是在运行时解释。没有编译时。因此，你可以将 TinyCalculator 示例中的所有表达式替换为 ${expr} 形式，这不会有任何区别。本书中的所有示例都使用 #{expr} 形式。

同样在 EL 中，你可能会读到*值表达式*和*方法表达式*。有什么区别？让我们回顾一下 TinyCalculator 中的一些表达式：#{tinyCalculator.param1} 是一个值表达式，而 #{tinyCalculator.add} 是一个方法表达式。在这些简单的例子中，我们看不出任何语法上的区别。要识别哪个是哪个，你需要了解上下文。Java 约定可能会帮助你：param1 是一个主语，而 add 是一个动词。Java 方法应以动词开头，并可能继续其他内容。对于属性，动词是 get 和 set，后跟一个名称（主语）。并且，正如对 TinyCalculator 示例的简要解释，#{tinyCalculator.param1} 是一种双向绑定，省略了 get 和 set。

###### 注意

更一般地说，我们可以说 #{myBean.myProperty} 是一个值表达式，而 #{myBean.myMethod} 是一个方法表达式。

第一个点之前的部分（或者在其他示例中，第一个左方括号）称为*基*，而之后的部分称为*属性*——或者对于方法表达式，称为*方法*。在此示例中，基是一个 Bean。正如后面所解释的，基也可能是一个变量或一个隐式对象。

在引用 Bean 的常见情况下，Bean 的名称（如果未定义为其他内容，则为 Bean 的类名——你可以在 @Named 注解中定义不同的名称，例如 @Named("myBaseName")）用于基，但首字母小写。

如果需要，属性通过使用常见的点语法被分解成更小的部分。invoice.customer.street 指的是发票中客户对象的街道。在纯 Java 中，它可能看起来像清单 4-1。



###### 清单 4-1 与 EL 表达式 `invoice.customer.street` 等价的 Java 代码

```
 1    Invoice invoice = ...;
 2    ...
 3    String street = invoice.getCustomer().getStreet();
 4    ...
 5    invoice.getCustomer().setStreet(newValue);
```

对于方法表达式，可以使用括号 `#{tinyCalculator.add()}`，也可以像之前展示的那样省略括号。如果 EL 调用的方法带有参数，则需要添加括号。

## 值表达式

到目前为止，我们讨论了一种非常常见的值表达式形式——用于获取或设置值。

要检索值，值表达式是一种将值作为其结果传递的表达式。可以计算这个值，其结果可以是任何类型。在本节中，我们将对此进行深入探讨。

### 运算符

EL 提供了一些作为 Java 开发人员非常熟悉的运算符。我不会解释它们，只列出它们。作为一个特定功能，某些运算符可能以简短文本缩写的形式出现，以避免使用可能在 HTML 中被转义的字符时可能发生的冲突。这种替代表示法列在方括号内：

*   +, -, *, / [div], % [mod]

*   < [lt], <= [le], == [eq], >= [ge], > [gt], != [ne]

*   && [and], || [or], ! [not]

*   cond ? expr1 : expr2

*   instanceof, empty (测试空字符串、集合、映射等)

*   += (字符串连接)

*   = (赋值), ; (分号), () (括号)

*   Lambda 表达式和流

Lambda 表达式和流在 Java SE 8 中可用，但在运行于 Java 7 的 EL 3.0 中，您可以使用相同的语法来使用它们。如下文所述，这个很酷的特性对于某些任务非常有用。

简单的 `+` 只是一个算术运算符，而 `+=` 是字符串连接。为了演示，让我们误用 `TinyCalculator` 的结果变量（记住，它是一个 `double` 类型）。因为 EL 独立于 JSF 组件，所以可以将 EL 表达式插入到页面中的任何位置。将它们（不带行号）插入到 `</h:form>` 标签之前，如清单 4-2 所示，然后运行应用程序。

###### 清单 4-2 EL 演示

```
 1   <br/>
 2   #{tinyCalculator.result = 10}
 3   <br/>
 4   #{tinyCalculator.result = tinyCalculator.result + 10}
 5   <br/>
 6   #{tinyCalculator.result}
 7   <br/>
 8   #{tinyCalculator.result = 10}
 9   <br/>
10   #{tinyCalculator.result += 10}
11   <br/>
12   #{tinyCalculator.result}
```

以下是对清单 4-2 的简要说明：

*   *第 1 行*：`<br/>` 是 (X)HTML 中的换行标签。没有它，所有输出将会连接在一起。

*   *第 2 行*：这是一个将值 10 赋值给 Bean 属性 `result` 的操作（更准确地说，是通过 setter 方法 `setResult` 进行赋值）。同时，会显示计算后的表达式值。由于结果类型是 `double`，因此会显示 `10.0`。

*   *第 4 行*：将结果加 10，并显示 `20.0`。右侧项调用 getter 方法，左侧（等号左侧）调用相应的 setter 方法。

*   *第 6 行*：仅验证赋值。显示 `20.0`。

*   *第 8 行*：在第 8 行，我们将结果的值重置为 10（如同我们在第 2 行所做的那样）。

*   *第 10 行*：Java 开发人员可能会认为结果与第 4 行相同，但这不是算术运算符。这是一个字符串连接。它将 `10` 追加到现有值，并显示 `10.010`。

*   *第 12 行*：因为第 10 行是字符串连接，而结果类型是 `double`，所以连接的结果无法赋值。相反，JSF 抛出了一个异常。我们可以使用 `<h:message>` 标签来显示异常消息。这里我们专注于 EL，而不是 JSF 标签。结果仍然是 `10.0`。

### 点号和方括号

在 Java 对象中，您可以使用点号语法深入细节。如前所述，EL 也是如此：

```
1   invoice.customer.street
```

或者，方括号语法也可以达到同样的效果：

```
1   invoice.customer["street"]
```

方括号也用于访问映射（Map）的成员。

## 方法表达式

方法表达式调用公共的非静态方法。如果方法没有参数，括号可以省略：

```
action="#{tinyCalculator.add}"
```

产生与以下相同的结果：

```
action="#{tinyCalculator.add()}"
```

与旧版本不同，您可以向方法表达式传递参数。

根据组件的不同，JSF 期望一个值表达式或一个方法表达式。例如，在 `action` 属性中，JSF 期望一个方法表达式，因此 `<h:commandButton value="Divide" action="#{tinyCalculator.param1}"/>` 会导致错误。

## 隐式对象

基础对象可以是 Bean、变量（将在本书后面讨论）或隐式对象。*隐式*对象由容器管理。表 4-1 提供了一个简要概述。

###### 表 4-1 隐式对象

| 隐式对象 | 描述 |
| --- | --- |
| application | 引用 `ServletContext` 的 `ApplicationContext`。这与 JSF 的 `Application` 对象不同！ |
| applicationScope | 存储在应用程序作用域中的数据映射。例如，应用程序作用域的 Bean 存储在此映射中。 |
| cc | 当前正在处理的顶级 JSF 复合组件。 |
| component | 此隐式对象引用当前正在处理的 `UIComponent`。 |
| cookie | HTTP `set-cookie` 头中的 Cookie 映射。 |
| facesContext | 提供对当前 `FacesContext` 的访问。 |
| flash | 提供对所谓的 flash 对象的访问。这是一个由 JSF 仅短暂存储（直到下一个请求）的对象。 |
| flowScope | 类似于 `applicationScope`，但用于流作用域。 |
| header | 引用当前请求的 HTTP 头映射。 |
| headerValues | 当前请求的 HTTP 头映射。每个值是一个包含该键所有值的 `String[]` 数组。 |
| initParam | 包含当前应用程序初始化参数的映射。 |
| param | 与当前请求关联的所有参数的映射。 |
| paramValues | 与上一条类似，每个值表示为一个包含该键所有值的 `String[]` 数组。 |
| request | 当前的 `ServletRequest`。 |
| requestScope | 类似于 `applicationScope`，但用于请求作用域。 |
| resource | 资源映射 (`javax.faces.application.ResourceHandler`)。 |
| session | 当前的 `HttpSession`。 |
| sessionScope | 类似于 `applicationScope`，但用于会话作用域。 |
| view | 当前的 `UIViewRoot`。 |
| viewScope | 类似于 `applicationScope`，但用于视图作用域。 |

## 总结

表达式语言（EL）是页面（表示层）和代码之间的粘合剂。它由获取或设置值的表达式（值表达式）以及方法调用（方法表达式）组成。两者都作用于 Java Bean、变量或隐式对象。EL 放置在标记 `#{...}` 内，几乎可以出现在页面定义的任何位置。它既可以在 JSF 标签中使用，也可以用于纯 HTML 部分。

© Michael Müller 2018

Michael Müller, Practical JSF in Java EE 8 , `doi.org/10.1007/978-1-4842-3030-5_5`



