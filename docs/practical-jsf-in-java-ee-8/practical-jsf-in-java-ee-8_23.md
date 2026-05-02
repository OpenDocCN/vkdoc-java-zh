# 15. Bean 验证

Michael Müller^(1 )

(1)德国北莱茵-威斯特法伦州布吕尔

正如我之前指出的，Books 是一个仅由一位作者维护的应用程序，该作者应该了解预期的数据格式。因此，并没有提供在每次输入后立即给出反馈或提供大量提示的用户界面。当然，对于面向潜在未知用户的应用程序来说，这些功能至关重要。稍后，在开发名为 Alumni 的第二个应用程序时，我将详细讨论这些功能。目前，验证将有助于防止用户输入可能不符合数据库要求的数据。

回顾 JSF 生命周期：在阶段 3，输入值将被转换和验证。在 HTML 页面中，每个输入都被视为纯文本。借助 EL，这些输入通过值表达式绑定到数据模型。通常，这是通过使用 Java Bean 的属性来实现的。在 Java 中，所有属性都是强类型的。这意味着需要进行转换，即从文本（字符串）到目标类型，反之亦然。这个工作由转换器完成。JSF 为多种内置 Java 类型提供了标准转换器。稍后，我们将讨论自定义转换器。如果转换失败，JSF 会抛出异常。因此，转换器执行了原始验证。

JSF 还包含一些标准验证器，您可以使用它们在更精细的级别上定义验证。例如，您只允许正数，或强制字符串达到最大长度等。我将在后面介绍这类验证器。

JSF 验证器会在用户输入后、将值应用到模型之前，在服务器端验证输入。有时仅验证输入是不够的。数据可能在业务层被更改。当您尝试将这些更改后的数据持久化到数据库时，程序可能会因为数据不符合数据库模式而崩溃。在这种情况下，在存储前再次验证数据将非常有帮助。同样的原则也适用于在应用程序中的任何其他位置进行验证。

幸运的是，Java EE 恰好提供了这种验证：它被称为 *Bean 验证*。如果数据所应用的模型中定义了验证，JSF 会利用它并同样使用此验证。在我们的应用程序中，我们将编辑和存储关于书籍的信息。我们只需向书籍实体应用特殊的注解，即可添加 Bean 验证。现在，JSF 和 JPA 都将使用此验证。如果需要，您还可以通过编程方式调用验证。

## 书籍实体

每本书都由其标题、副标题、作者以及其他信息（包括简短描述）来描述。该描述可能以不同语言呈现给用户。因此，我们需要一个包含翻译内容的第二个表，就像我们为类别所做的那样。为简洁起见，我将只讨论实体的关键部分。您可以下载完整的文件。

表 15-1 和 15-2 展示了我们将用于书籍和翻译的两个表。

###### 表 15-1 书籍

| 列名 | 数据类型 |
| --- | --- |
| bookId | int |
| bookTitle | varchar(200) |
| bookSubtitle | varchar(200) |
| bookAuthor | varchar(255) |
| bookPublisher | varchar(45) |
| bookYear | int |
| bookLanguage | varchar(10) |
| bookISBN | varchar(45) |
| bookShorttext | varchar(500) |
| bookReference | varchar(500) |
| bookAdReference | varchar(50) |

###### 表 15-2 书籍翻译

| 列名 | 数据类型 |
| --- | --- |
| btId | int |
| btBookId | int |
| btLanguage | varchar(10) |
| btShorttext | varchar(500) |
| btReference | varchar(50) |

接下来，我们将定义书籍实体，并为每一列实现一个属性（字段加 getter 和 setter），就像我们在第 12 章中所做的那样。清单 15-1 仅展示了一个属性。



###### 清单 15-1 实体类 Book

```
 1   [...]

 3   @Entity
 4   @Table(name = "Book")
 5   public class Book implements Serializable {

 7   [...]

 9     // <editor-fold defaultstate="collapsed" desc="属性 Title">
10     @Size(max = 200)
11     @Column(name = "bookTitle")
12     public String getTitle() {
13       return _title;
14     }

16     public void setTitle(String title) {
17       _title = title;
18     }

20     private String _title;
21     // </editor-fold>

23   [...]
24   }
```

`<editor-fold>` 标签是 NetBeans 特有的，用于折叠代码。大多数其他 IDE 会忽略它们。

让我们关注 `@Size(max = 200)` 注解（第 10 行）。它检查属性的长度，限制为最多 200 个字符。这正好符合我们在表中定义的内容。如果属性 title 包含更多字符，验证将抛出异常。此验证在存储数据之前进行。如果没有 Bean 验证，当我们尝试插入/更新数据库时，如果字符串超过该长度，就会抛出异常。

乍一看，用验证异常替换 IO 异常（我们可能节省了数据库访问）似乎价值不大。但如前所述，JSF 也会调用 Bean 验证。而且 Bean 验证并不局限于实体。你可以注解你的数据访问对象（DAO）类或你选择的任何其他类型的数据模型。注解可以放在字段或属性（getter 方法）上。

在生命周期阶段 3（转换和验证）期间，JSF 将调用 JSF 验证器以及 Bean 验证。如果像 `#{book.name}` 这样的值表达式指向一个被 Bean 验证注解的属性或字段，则会执行此检查。JSF 不会简单地（重新）抛出异常，而是识别可能的违规。如果页面中定义了消息标签（如下一节所示），则此异常会触发输出相应的消息。作为 Bean 验证与 JSF 结合的一个真正好处，用户会得到即时响应。

Bean 验证定义了许多注解——例如，`@Min` 和 `@Max` 用于定义整数限制，或 `@Future` 和 `@Past` 用于定义日期约束。如你所见，这些约束看起来很简单。确实如此，所以我不会详细解释它们。最复杂的约束之一是 `@Pattern`，它将属性或字段的值与模式匹配进行比较。有关可能的约束列表（截至 Java EE 8），请参阅 [`javaee.github.io/tutorial/bean-validation002.html`](https://javaee.github.io/tutorial/bean-validation002.html) 上的 Java EE 8 教程。另一个有用的来源是 [`beanvalidation.org`](http://beanvalidation.org)。

Bean 验证包含在 Java EE 应用服务器中。它不是纯 Servlet 容器或 Java SE 的一部分。添加 Bean 验证只需将相应的库添加到你的类路径中。在 [`beanvalidation.org`](http://beanvalidation.org) 上，你会找到经过认证的实现列表。

## Book Editor

如果发生验证失败，应向用户报告。为此，我们需要在编辑器页面中为消息设置一个占位符。清单 15-2 显示了此页面。

###### 清单 15-2 bookEditor.xhtml 的摘录

```
1   <div class="inputGroup">
2     <h:outputLabel styleClass="label" for="title" value="#{msg.lblTitle}"/>
3     <h:inputText id="title" styleClass="inputFull"
4                  value="#{bookEditor.book.title}"/>
5     <h:message id="msgTtitle" for="title" styleClass="warning"/>
6   </div>
```

在此摘录中，我们有一个标签、一个输入字段和一个消息。此消息将包含验证消息——当且仅当存在验证消息时。无需将消息放在输入字段附近（有些人可能会将所有消息分组在页面的某个位置）。两个组件（inputText 和 message）通过 `for="title"` 绑定在一起。这里的 `"title"` 是输入字段的 id。

验证将在 JSF 生命周期的阶段 3 中调用。所有可能的异常（包括专用消息）都将在底层被收集。如果至少发生一个异常，生命周期将在阶段 3 之后直接分支到阶段 6，即*渲染响应*。之前的页面将重新渲染，包括所有消息。

有时标准消息不适用，因此你需要自定义它们。一种方法是在 `src/main/resources` 文件夹的默认包中添加一个 `ValidationMessages.properties` 文件。现在，使用该文件中的正确属性，你可以自定义消息。属性名称遵循结构 `javax.validation.constraints.XXX`，其中 `xxx` 代表约束名称，例如 `javax.validation.constraints.Size.message`。你可以在例如 [`grepcode.com/file/repo1.maven.org/maven2/org.hibernate/hibernate-validator/4.0.0.CR1/org/hibernate/validator/ValidationMessages.properties`](http://grepcode.com/file/repo1.maven.org/maven2/org.hibernate/hibernate-validator/4.0.0.CR1/org/hibernate/validator/ValidationMessages.properties) 找到列表。如果你需要翻译消息，只需像之前那样添加带有正确区域设置代码的资源文件。

特别是在模式匹配器的情况下，像 `must match "{regexp}"` 这样的单一消息对用户来说不太有用。IT 领域之外的人谁会理解这样的消息？根据模式的不同，我们需要不同的消息。例如，如果我们使用正则表达式来定义有效的电子邮件，那么消息应该反映这一点。Bean 验证提供了一个简单的解决方案：消息可以在注解中定义：

```
@Pattern(regexp = ".*@.*", message = "请输入有效的电子邮件地址。")
```

请注意，这个简单的正则表达式并不是一个非常复杂的电子邮件检查器——它只是为了展示原理。如你所见，可以为每个约束定义不同的消息。这里，消息只是一个字面量。如果我们需要以不同语言显示消息，则需要将消息转换为引用属性文件的键。如果我们用花括号将消息括起来，则花括号内的内容将被视为键：

```
@Pattern(regexp = ".*@.*", message = "{validation.constraints.email.message}")
```

现在我们可以将此键添加到属性文件中。在 `ValidationMessages_en.properties` 中，我们添加以下内容：

```
validation.constraints.email.message=Please enter a valid email address
```

在 `ValidationMessages_de.properties` 中，我们添加这一行：

```
validation.constraints.email.message=Bitte geben Sie eine gültige E-Mail-Adresse an
```

不要忘记在 `ValidationMessages.properties` 文件中为你的默认语言放置文本。使用 NetBeans 的属性编辑器，你可以一目了然地看到默认语言以及不同语言。

在前面的示例中，我们只为消息属性使用了一个键。通常这就是你想要的。实际上，你可以混合使用字面文本和键。每出现一对花括号都被视为一个键。因此，我们可以组合多个键和字面文本。以下内容会提示关于错误格式的本地化文本，后跟三个井号和我们的电子邮件消息：

```
{validation.constraints.wrong.format.message} ### {validation.constraints.email.message}
```



## 摘要

在验证阶段，JSF 不仅会执行 JSF 特有的验证，还会在数据模型中定义了标准 Bean 验证时调用它。因此，验证约束不仅可以确保字段在存储时适合数据库，还能向用户提供合格的反馈。

本章介绍了如何在基础层面上使用 Bean 验证。我们将在本书后面更深入地探讨这一主题。

© Michael Müller 2018

Michael Müller, Practical JSF in Java EE 8 , `doi.org/10.1007/978-1-4842-3030-5_16`

