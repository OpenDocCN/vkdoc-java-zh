# 17. 会话作用域

Michael Müller^(1 )

(1)德国，北莱茵-威斯特法伦州，布吕尔

上下文和依赖注入（CDI）提供了几种不同的作用域，你可以使用它们来定义对象的生命周期。大多数作用域（如请求作用域）都绑定到预定义的周期，例如单个请求、会话或应用程序生命周期。其中一个预定义作用域——*会话作用域*——的生命周期可以通过编程方式控制，因此该作用域对于某些功能来说变得很有趣。

## 多页面编辑器

我们的下一个任务是创建一个新的图书条目或编辑现有条目。图书编辑器将由一个选项卡面板组成，该面板提供一个页面用于图书的元数据，包括标题、作者、出版商，以及每种语言一个页面，你可以在其中编辑评论。必须能够在这些页面之间切换而不丢失数据，因此不难想象我们需要一个生命周期比单个请求更长的后台 Bean。

在一个会话期间，用户可能会编辑多本图书。与类别（所有类别一起编辑）不同，我们必须提供一个功能来逐一编辑图书。这意味着会话作用域在这里不适用。我们需要一个比单个请求长但比会话短的作用域。根据第 16 章的表格，使用内置的 CDI 作用域 `@ConversationScoped` 将是合适的选择。

图书编辑器将通过一个表示图书 ID 的参数来调用。bookId 从请求映射（第 22 行）中提取，我们可以通过外部上下文（第 19-20 行）访问该映射。为简单起见，如果 bookId 未知或不是整数，我们将始终创建一本新书（第 24 行和第 28 行）。在关键任务应用程序中，我们会选择不同的策略，但对于本书来说，这已经足够了。一旦图书被加载或创建，我们将会话转换为长期运行模式，如清单 17-1 所示。



###### 清单 17-1 BookEditor.java 代码片段

```
 1   [...]
 2   @Named
 3   @ConversationScoped
 4   public class BookEditor implements Serializable {
 5     [...]
 6     @Inject Conversation _conversation;

 8     @PostConstruct
 9     private void init() {
10       [...]
11       loadOrCreateBook();
12       if (_conversation.isTransient()) {
13         _conversation.begin();
14       }

17     private void loadOrCreateBook() {
18       FacesContext fc = FacesContext.getCurrentInstance();
19       Map<String, String> paramMap = fc.getExternalContext()
20               .getRequestParameterMap();
21       try {
22         int bookId = Integer.parseInt(paramMap.get("bookId"));
23         _book = _bookService.find(bookId);
24         if (_book == null) {
25           _book = new Book();
26         }
27       } catch (NumberFormatException e) {
28         _book = new  Book();
29       }
30     }

32   [...]
33   }
```

借助 CDI，我们获得了一个注入到 BookEditor 中的 Conversation 实例（第 6 行）。为简单起见，我们假设这是一个全新的实例。这样的新实例运行在所谓的*临时*模式下，并将在当前请求结束时终止。到目前为止，BookEditor 的行为类似于一个请求作用域的 Bean。但根据我们应用程序的需求，Conversation 可能会变成长期运行。我们将在 `init()` 方法中延长对话。由于 `@PostConstruct` 注解的存在，当 BookEditor 的构造完成时，此方法会被自动调用。

无论如何，首先检查对话是否为临时（非长期运行）状态非常重要。只有在这种状态下才允许启动长期运行。在清单 17-1 的第 12 行，我们执行了检查，看对话是否尚未进入长期运行状态（由 `isTransient()` 指示）。如果不是这种情况，我们就通过调用 `begin()` 来启动长期运行部分。

启动长期运行相当简单，但结束它可能会变得复杂。当然，这无非是在我们的对话对象上调用 `end()`。唯一的问题是：我们何时终止长期运行模式？如果编辑器有一个“保存并关闭”按钮，那将是一个绝佳的选择——保存书籍后，编辑器将被关闭，从而退出对话的长期运行模式。但在使用书籍时，用户可能会保存并停留在打开的对话框中。他可能会继续编辑，因此我们不能结束对话。或者用户可能会点击某个导航按钮跳转到其他位置——例如创建一本新书，或编辑另一本书。由于没有单一的退出编辑器的点，这就需要更多的处理。

让我们检查一下 JSF 何时会重用现有的对话，或者注入一个新的对话。每当一个新的请求中使用 Book Bean 时，它都会被创建，要么对话是临时的，要么在长期运行对话的情况下，会使用一个新的对话。在这种情况下，`init()` 方法会被调用。因此，我们可以通过跟踪对此方法的调用来追踪新的对话，如清单 17-2 所示。

###### 清单 17-2 BookEditor.java：记录 init 日志

```
 1   [...]
 2   @Named
 3   @ConversationScoped
 4   public class BookEditor implements Serializable {
 5     [...]
 6     private static final Logger LOGGER = Logger.getLogger("BookEditor");

 8     @PostConstruct
 9     private void init() {
10       LOGGER.log(Level.INFO, "init in BookEditor");
11       [...]
12     }

14   [...]
15   }
```

一旦 Bean 如此准备好，它就会记录每一次创建（更准确地说，它会记录每次由 CDI 创建的情况——如果是由 `new` 创建的，则不会调用 `init`）。现在，我们可以通过不同的方式调用我们的书籍编辑器来观察其行为。

为了测试目的，在清单 17-3 中，我们将在 `adminTemplate.xhtml` 的导航部分添加两个指向书籍编辑器的链接。

###### 清单 17-3 测试链接：导航到 bookEditor.xhtml

```
1   <h:form>
2     <h:commandLink styleClass="button"
3                    value="#{msg.btnNewBook} commandLink"
4                    action="/admin/bookEditor.xhtml"/>
5   </h:form>
6   <h:link styleClass="button"
7           value="#{msg.btnNewBook} link"
8           outcome="/admin/bookEditor.xhtml"/>
```

###### 链接标签之间的差异：尝试并观察

1.  启动 Books 应用程序，并通过 `commandLink` 按钮导航到书籍编辑器。

2.  当书籍编辑器打开时，再次点击 `commandLink` 按钮。

3.  在 NetBeans 输出窗口（GlassFish 窗口）中实时观察日志输出。

4.  重新启动应用程序，并使用 `link` 按钮重复你的观察。

`commandLink` 和 `link` 提供了不同的导航模型。`commandLink` 使用回发实现了更传统的 JSF 导航，而 JSF 2.0 引入的 `link` 则通过 GET 请求实现导航。在下一节中，我们将讨论这些差异及其对对话处理的影响。

## POST 与 GET 导航

在 HTML 中，你可以使用表单来收集数据。作为表单的一部分，你可以在输入字段下方放置一个按钮，将数据提交到服务器。在传统 HTML 中，这些数据通过 HTTP POST 请求传输到服务器。这是一个指向相同 URL 的请求，因此被称为*回发*。结果，服务器通常会发回一个确认信息，该信息显示在另一个页面上。这种导航被称为*回发导航*。在 JSF 2.0 之前，这是 JSF 常见的导航模型。

如果你阅读过关于 REST 的文章，你可能知道 HTTP GET 用于获取资源，而 POST 用于创建资源。你可能也读到过，由于这种回发导航，JSF 并未实现 HTTP 的原始设计目标。如前所述，回发导航是 REST 出现之前常见的导航类型，因此它并不违反 HTTP 的原则。如果有某些布道者想告诉你别的，不要相信他们。

尽管回发导航感觉像是一种自然的导航方式，但它有一个缺点：数据被提交到原始 URL，而响应可能显示一个全新的页面。因此，浏览器中显示的 URL 通常滞后一步。正因为如此，一些纯粹主义者认为 JSF 不可添加书签。JSF 通常用于应用程序，那么你怎么看：应用程序是否应该在每个状态下都可添加书签？还是只针对定义明确的页面（入口点）？

为了更新浏览器显示中的当前 URL，JSF 提供的一种策略是重定向：就在回发之后，会调用一个指向目标页面的 GET 请求。或者你也可以使用带有 `link` 的 GET 导航。

你观察了之前练习中浏览器的 URL 和服务器的日志吗？第一次调用 `commandLink` 按钮时，URL 并未更新为书籍编辑器的地址。这是一个典型的回发导航。当书籍编辑器首次显示时，会启动一个新的对话（如日志所示）。随后点击此按钮将刷新（重新加载）页面，但不会启动新的对话。

但是，如果你使用 `link` 按钮，URL 会直接指向书籍编辑器。这是通过 GET 请求进行的导航。每次点击都会启动一个新的对话，因此每次点击都会创建一个新的书籍编辑器实例。在幕后，CDI 会保留之前的实例，因为这些对话仍在运行。我们需要处理这个问题。

###### 注意

使用回发导航时，JSF 会重用现有的对话。使用 GET 导航时，JSF 会创建一个新的对话。

正如我们稍后将讨论的，在 GET 导航期间保持现有对话是可能的。



## 开始与结束对话

如本章所示，我们在 `init()` 方法内的 *@*PostConstruct 阶段启动了对话。如果用户保存了书籍信息，她可能会停留在编辑器页面上继续编辑，这意味着我们必须保持对话。而编辑器并没有单一的退出点——用户可以使用导航面板提供的任何导航。为了解决这个问题，由导航按钮触发的*每一次*导航都将结束当前对话。

在这种导航情况下，我们会调用一个方法来结束当前对话，如清单 17-4 所示。

###### 清单 17-4 停止长时间运行对话的方法

```
1    public void endConversation(Conversation conversation) {
2        if (!conversation.isTransient()) {
3            LOGGER.log(Level.INFO,
4            "Conversation stopping: {0}", conversation.getId());
5            conversation.end();
6        }
7    }
```

## 总结

CDI 的大多数预定义作用域都具有明确定义的生命周期，这些生命周期取决于请求、会话或应用程序的上下文。有一个作用域（@ConversationScoped）可以设置为程序化控制的长时间运行模式。这允许程序员使用一个生命周期比请求长，但又不同于会话或应用程序的作用域。

对话可以通过 `begin()` 转换为长时间运行模式，并通过 `end()` 恢复为其较短的生命周期。

© Michael Müller 2018

Michael Müller, Practical JSF in Java EE 8 , `doi.org/10.1007/978-1-4842-3030-5_18`

