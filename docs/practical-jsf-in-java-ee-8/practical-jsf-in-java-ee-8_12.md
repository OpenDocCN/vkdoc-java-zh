# 5.  HTML 友好标记

Michael Müller¹

(1)德国，北莱茵-威斯特法伦州，布吕尔

在 JSF 早期，大多数开发者熟悉 Java，但不熟悉 HTML。JSF 标签旨在提供视图描述，无需深入了解 HTML。时代变了，如今许多开发者都熟悉 HTML。那么，为什么不直接用目标语言 HTML 来定义页面呢？这就是 HTML 友好标记的用途。

HTML 友好标记是 JSF 2.2 引入的特性之一。HTML5 友好标记，或者更准确地说，*直通元素*和*直通属性*，是为了支持 HTML5 的附加功能而引入的。例如，你可以添加一个直通属性来添加微数据或其他新的 HTML5 特性。

就个人而言，我倾向于省略*5*，因为这个特性也适用于其他 HTML 版本。将其称为*HTML5 友好*的问题在于，人们会认为某个特性是 HTML5 特有的，而实际上并非如此。

## HTML 友好的 TinyCalculator

让我们重新设计 TinyCalculator 应用程序。回顾一下，清单 5-1 展示了使用传统 JSF 标签的原始页面。

###### 清单 5-1 使用 JSF 标签的 TinyCalculator

```
 1   <?xml version='1.0' encoding='UTF-8' ?>
 2   <!DOCTYPE html>
 3   <html xmlns="http://www.w3.org/1999/xhtml"
 4         xmlns:h="http://xmlns.jcp.org/jsf/html">
 5       <h:head>
 6           <title>TinyCalculator</title>
 7       </h:head>
 8       <h:body>
 9           <h1>TinyCalculator</h1>
10           <h:form>
11               <div>
12                   <h:outputLabel value="Param1: "/>
13                   <h:inputText value="#{tinyCalculator.param1}"/>
14               </div>
15               <div>
16                   <h:outputLabel value="Param2: "/>
17                   <h:inputText value="#{tinyCalculator.param2}"/>
18               </div>
19               <div>
20                   <h:commandButton value="Add"
21                                    action="#{tinyCalculator.add}"/>
22                   <h:commandButton value="Subtract"
23                                    action="#{tinyCalculator.subtract}"/>
24                   <h:commandButton value="Multiply"
25                                    action="#{tinyCalculator.multiply}"/>
26                   <h:commandButton value="Divide"
27                                    action="#{tinyCalculator.divide}"/>
28               </div>
29               <div>
30                   <h:outputLabel value="Result: "/>
31                   <h:outputText value="#{tinyCalculator.result}"/>
32               </div>
33           </h:form>
34       </h:body>
35   </html>
```

清单 5-2 展示了一个更接近纯 HTML 的版本。

###### 清单 5-2 HTML 友好风格的 TinyCalculator

```
 1   <?xml version='1.0' encoding='UTF-8' ?>
 2   <!DOCTYPE html>
 3   <html xmlns="http://www.w3.org/1999/xhtml"
 4         xmlns:jsf="http://xmlns.jcp.org/jsf">
 5       <head>
 6           <title>TinyCalculator</title>
 7       </head>
 8       <body>
 9           <h1>TinyCalculator</h1>
10           <form jsf:id="calc">
11               <div>
12                   Param1: <input type="text"
13                            jsf:value="#{tinyCalculator.param1}"/>
14               </div>
15               <div>
16                   Param2: <input type="text"
17                            jsf:value="#{tinyCalculator.param2}"/>
18               </div>
19               <div>
20                   <input type="submit" value="Add"
21                          jsf:action="#{tinyCalculator.add}"/>
22                   <input type="submit" value="Subtract"
23                          jsf:action="#{tinyCalculator.subtract}"/>
24                   <!-- 或者，使用 button 元素： -->
25                   <button jsf:action="#{tinyCalculator.multiply()}">
26                       Multiply
27                   </button>
28                   <button jsf:action="#{tinyCalculator.divide()}">
29                       Divide
30                   </button>
31               </div>
32               <div>
33                   Result: #{tinyCalculator.result}
34               </div>
35           </form>
36       </body>
37   </html>
```

让我们仔细看看。在页面开头，你会找到 XML 命名空间定义 xmlns:jsf="http://xmlns.jcp.org/jsf"。在这个定义中，前缀 `jsf:` 指向那个特殊的命名空间。现在，如果 JSF 发现一个元素带有任何以此命名空间别名为前缀的属性，它就会将该元素视为 JSF 将要处理的元素。JSF 会尝试将这些元素映射到已知元素，就像它们使用了传统的 JSF 标签一样。例如，`"input type=submit"` 将以与 `h:inputText` 相同的方式处理。任何未知元素都被视为直通元素，并按原样渲染。这两种元素都会被收集到组件树中。

HTML 友好标记自 JSF 2.2 起就已可用。我们将在 Books 应用程序（第二部分）中使用 JSF 标签，并在开发 Alumni（第四部分）时部分切换到更 HTML 友好的风格。你可以根据自己喜欢使用 HTML 还是倾向于隐藏大部分 HTML 内容来选择其中一种风格。从技术上讲，随意混合使用这些风格没有问题，但如果你以一致的方式使用它们，代码的可读性通常会更好。

## 总结

HTML 友好标记是一种比 JSF 标签更接近纯 HTML 的页面风格。只有需要由 JSF 处理的属性才会用特殊前缀标记，而页面的其余部分则使用 HTML 编码。

HTML 友好标记也意味着将 JSF 无法直接处理的 HTML 属性传递给浏览器。这里讨论的直通属性可用于向 JSF 标签添加 HTML 属性，这些属性将按原样渲染。因此，JSF 标签可以丰富任何属性，即使 JSF 不认识它。

© Michael Müller 2018

Michael Müller, Practical JSF in Java EE 8 , `doi.org/10.1007/978-1-4842-3030-5_6`

