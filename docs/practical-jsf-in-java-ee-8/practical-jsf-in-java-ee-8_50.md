# 附录 C：标签库

在开发 Web 应用程序时，你总是需要创建一些要在浏览器中显示的 HTML 页面。一种方法是隐藏 HTML 处理过程并完全生成页面。另一种极端做法是将应用程序代码混入 HTML 页面中。

但最常见的解决方案是将页面与代码分离。为此，需要在页面中放置一些调用代码的指令。从技术上讲，这些指令是 HTML/XML 标签或属性，尽管有些人可能用其他名称称呼它们。例如，AngularJS 的指令是 HTML 属性，但它们称之为*指令*。“传统”的 JSF 应用程序通常使用标签构建，但从 JSF 2.2 开始，你可以省略一些标签，转而使用透传元素。

所有这些标签都被分组在一起，并可通过不同的命名空间使用。表 C-1 概述了这些命名空间。



###### 表 C-1 命名空间

| URI | 前缀 | 描述 |
| --- | --- | --- |
| [`xmlns.jcp.org/jsf`](http://xmlns.jcp.org/jsf) | jsf: | 直通元素 |
| [`xmlns.jcp.org/jsf/core`](http://xmlns.jcp.org/jsf/core) | f: | JSF 核心库 |
| [`xmlns.jcp.org/jsf/html`](http://xmlns.jcp.org/jsf/html) | h: | JSF HTML 库 |
| [`xmlns.jcp.org/jsf/facelets/`](http://xmlns.jcp.org/jsf/facelets/) | ui: | Facelet 模板标签库 |
| [`xmlns.jcp.org/jsf/composite`](http://xmlns.jcp.org/jsf/composite) | cc: | 复合组件标签库 |
| [`xmlns.jcp.org/jsf/passthrough`](http://xmlns.jcp.org/jsf/passthrough) | p: | 直通属性 |
| [`xmlns.jcp.org/jsp/jstl/core`](http://xmlns.jcp.org/jsp/jstl/core) | c: | JSP 标准标签库 (JSTL) |
| [`xmlns.jcp.org/jsp/jstl/functions`](http://xmlns.jcp.org/jsp/jstl/functions) | fn: | JSTL 函数 |

本附录简要概述并快速参考了标准 JSF 组件库和 JSP 标准标签库中的标签。完整参考可在网上查阅（[`javaserverfaces.github.io/docs/2.3/index.html`](https://javaserverfaces.github.io/docs/2.3/index.html)），或参阅 Ed Burns 和 Chris Schalk 所著的 *JavaServer Faces 2.0*（McGraw-Hill, 2010）。请注意，尽管该书是为 JSF 2.0 编写的，但对 JSF 2.3 仍然有用。

这些标签按库分组，并按字母顺序列出。此外，还描述了一些常见属性。

### 直通元素

这只是一个标记命名空间。JSF 会尝试检测由 jsf:（或您在命名空间声明中选择的任何别名）标记的组件。已知组件会被添加到组件树中，就像在其他库中定义的一样。未知组件则作为直通元素添加。

### JSF 核心库

JSF 核心库的元素通常以 f: 为前缀，但您可以在声明命名空间时更改它。这些标签（以及其他库的标签）在表 C-2 中列出，不带任何前缀。

###### 表 C-2 命名空间

| 标签名称 | 描述 |
| --- | --- |
| actionListener | 声明性地为最近的父级 UIComponent 注册一个 ActionListener。 |
| ajax | 声明性地为父级 UIComponent 或其子 UIComponent 添加一个 AjaxBehavior。 |
| attribute | 为周围的 UI 组件添加一个属性。 |
| attributes | 为周围的 UI 组件向一个映射添加多个属性。 |
| convertDateTime | 为 UIComponent 注册一个 DateTimeConverter。 |
| converter | 为 UIComponent 注册一个命名（自编写）的转换器。 |
| convertNumber | 为 UIComponent 注册一个 NumberConverter。 |
| event | 为 UIComponent 注册一个 ComponentSystemEventListener。 |
| facet | 为 UIComponent 注册一个命名的 facet。 |
| importConstants | 导入常量，以便在 EL 中使用。 |
| loadBundle | 加载本地化的资源包（如果可用），否则加载默认资源包。 |
| metadata | 为 Facelets 视图声明元数据。使用 Facelets 时，视图对应一个（HTML）页面，除非由可选的 f:view 指定。 |
| param | 为最近的父级 UIComponent 添加一个参数。 |
| passThroughAttribute | 定义一个直通属性。此类属性将直接传递给浏览器，而不经过 JSF 解释。 |
| passThroughAttributes | 直通由 EL 作为 Map<String, Object> 提供的一组参数。 |
| phaseListener | 为阶段事件添加一个监听器。 |
| resetValues | 重置 render 属性中声明的组件的值，例如清除表单的输入字段。 |
| selectItem | 向选择框或组合框添加一个项目。 |
| selectItems | 向选择框添加由 EL 表达式提供的多个项目。 |
| setPropertyActionListener | 为最近的父级 UIComponent 注册一个动作监听器。 |
| subview | 为使用 JSP 的页面定义一个子视图组件。在现代 Facelets 页面上没有用途。 |
| validateBean | 使最近的父级 UIComponent 或所有嵌套的 UIComponent 通过 Bean 验证进行验证。 |
| validateDoubleRange | 验证 inputText 组件（其支持字段类型为 double）的值范围（最小值到最大值，两者均可选）。 |
| validateLength | 验证最近的父级 UIComponent 的长度。 |
| validateLongRange | 与 validateDoubleRange 相同，但针对 Long 类型。 |
| validateRegex | 根据正则表达式验证输入。 |
| validateRequired | 声明最近的父级 UIComponent 为必填项。效果与 required="true" 属性相同。 |
| validator | 为最近的父级 EditableValueHolder（例如输入文本）注册一个验证器。 |
| valueChangeListener | 为最近的父级 UIComponent 的 valueChange 事件注册一个监听器。 |
| verbatim | 为 JSP 注册一个子 UIOutput 元素。在现代 Facelets 页面上没有用途。 |
| view | 所有 JSF 组件的容器。使用 Facelets 时，视图是隐式声明的，因此无需显式的 view 标签。但您可以使用它来传递其他信息，如语言等。 |
| viewAction | 注册一个动作（方法），使其在给定阶段被调用。需在 metadata 标签内使用。 |
| viewParam | 从 GET 请求中检索一个参数（id 和值）。需在 metadata 标签内使用。 |
| websocket | 注册一个 WebSocket 连接，用于接收服务器发送的推送消息。 |

### JSF HTML 库

HTML 库包含渲染为 HTML 元素的标签。HTML 元素不仅被渲染，还添加了 JSF 特有的功能。通用命名空间是 h（前缀 h:）。表 C-3 列出了这些标签。

###### 表 C-3 JSF HTML 库



| 标签名称 | 描述 |
| --- | --- |
| body | 对应 HTML 的 body 标签。它还提供资源重定位功能，例如针对 JavaScript 或 CSS 文件。 |
| button | 渲染一个类型为 submit 的 HTML input 元素。用于通过 HTTP GET 方式导航至 outcome 属性指定的 URL。 |
| column | 表示 dataTable 中的一列。列的内容及其标题在合适的子元素中定义。 |
| commandButton | 渲染一个类型为 submit 、 reset 或 image 的 HTML input 元素。它可以执行一个动作和/或一次导航。 |
| commandLink | 渲染一个 HTML 链接（<a href=...>），并可在点击时执行一个动作。 |
| dataTable | 渲染一个 HTML 表格，其行数由集合或其他合适的数据结构提供。 |
| doctype | 渲染一个文档类型声明。 |
| form | 渲染一个 HTML 表单，并将其添加到 JSF 组件树中。当表单提交时，该标签内的所有 JSF 组件都将被处理。包括 commandLink 在内的几个 JSF 元素需要嵌套在 JSF 表单中才能正常运行。 |
| graphicImage | 渲染资源文件夹中提供的图像。 |
| head | 不仅渲染 HTML head 元素，而且在使用资源重定位时（例如从某个资源文件夹加载脚本或 CSS 文件）用作父元素。 |
| inputFile | 用于浏览和上传文件。 |
| inputHidden | 渲染一个类型为 hidden 的 HTML input 字段。与其他输入元素一样，也可以分配来自后台 bean 的值。 |
| inputSecret | 渲染一个类型为 password 的 HTML input 字段。所有输入的字符都将显示为星号。 |
| inputText | 渲染一个类型为 text 的 HTML input 字段。此标签是大多数表单的主力。 |
| inputTextarea | 渲染一个 HTML textarea 。与 inputText 类似，可用于显示和查询一些文本。它可以包含多行文本，在屏幕上占据更大的区域。大多数现代浏览器允许用户调整此元素的大小。 |
| link | 渲染一个 HTML 链接（<a href=...>），可用于可书签导航。 |
| message | 用于显示为 message 元素所引用的（input）元素排队的第一个 Faces 消息。 |
| messages | 能够显示所有未被特定 message 标签显示或将被全局显示的消息。 |
| outputFormat | 渲染一个 HTML span 元素，用于显示通过 binding 属性或 value 绑定到此组件的文本。此元素可通过 param 子组件进行参数化，并由 MessageFormat.format() 方法进行格式化。 |
| outputLabel | 渲染一个 HTML 标签。可以通过其 for 属性将其分配给一个 input 元素。 |
| outputLink | 渲染一个 HTML 链接（<a href=...>），主要用于在应用程序外部导航。 |
| outputScript | 用于通过资源重定位包含一个脚本文件。 |
| outputStylesheet | 同上，但用于 CSS 文件。 |
| outputText | 将其值作为文本渲染在页面内。默认情况下，< 会被转义为 < 以防止 HTML 注入。 |
| panelGrid | 此组件渲染一个 HTML 表格。其子组件成为该表格的单元格内容。 |
| panelGroup | 此标签渲染为一个包含其所有子元素的 HTML span 元素。可用于在只需要一个元素的地方放置多个元素，例如放入表格的一个单元格中。 |
| selectBooleanCheckbox | 渲染一个类型为 checkbox 的 HTML input 元素。 |
| selectManyCheckbox | 渲染一个包含一组复选框的 HTML 表格，这些复选框可以排成一行或一列，具体取决于布局方向。 |
| selectManyListbox | 渲染一个列表框（HTML select 元素），用户可以在其中选择多个条目。 |
| selectManyMenu | 渲染一个高度为 1 的 HTML select 元素。用户可以像下拉列表一样打开它并选择多个元素。 |
| selectOneListbox | 渲染一个列表框（HTML select 元素），用户可以在其中选择一个条目。 |
| selectOneMenu | 渲染一个高度为 1 的 HTML select 元素，它作为一个下拉列表用于选择一个元素。 |
| selectOneRadio | 渲染一组单选按钮。就像老式汽车收音机上的按钮一样，用户只能选择其中一个。 |

#### 通用属性

本节描述了一些在前述许多标签中广泛使用的属性。表 C-4 列出了这些标签。

###### 表 C-4 属性

| 标签名称 | 描述 |
| --- | --- |
| disabled | 如果为 true（默认为 false），元素将呈现为“灰色”，表示无法进行输入或其他操作。 |
| id | 为元素提供一个 id。可以通过其 id 访问元素，该 id 在其作用域内必须是唯一的。 |
| for | 获取包含此属性的元素所引用的另一个元素的 id。用于标签或消息。 |
| readonly | 组件将照常渲染，但无法输入。 |
| rendered | 定义渲染组件的条件。默认为 true。没有此属性的元素将被渲染。 |
| styleClass | 定义用于此元素的 CSS 类。渲染为 HTML 的 class 属性。 |

### Facelet 模板标签库

标准 Facelets 模板库可用于 Facelets UI 语言，但不能用于 JSP。我建议优先使用 Facelets 而非 JSP，因为许多新功能仅适用于这种视图声明语言。此库由命名空间 "[`xmlns.jcp.org/jsf/facelets`](http://xmlns.jcp.org/jsf/facelets)" 声明，并使用默认前缀 ui 。表 C-5 列出了这些标签。

###### 表 C-5 Facelets 标签

| 标签名称 | 描述 |
| --- | --- |
| component | 用于 JSF 2.0 之前 Facelets 的 composition 的旧版本。与 composition 不同，它不能指定模板。 |
| composition | 定义了一个元素组合（页面的一部分），可以通过 include 包含到其他页面或组合中。一个组合可以定义一个模板，指明它从何处被使用。在这种情况下，导航到该组合将渲染整个模板。composition 之外的所有标记都将被忽略。 |
| debug | 提供一些用于调试的内部信息，例如组件树。默认情况下通过按 Ctrl+Shift+D 调用，或者如果定义了其他快捷键则使用该快捷键。 |
| decorate | 一个可重用的页面片段，类似于 composition 。与 composition 不同，此元素之外的任何标记也会被使用。 |
| define | 定义 composition 的一部分，该部分将被插入到 Facelets 模板中，插入位置是定义了与 define 标签相同名称的 insert 标签所在处。 |
| fragment | 定义一个可重用的页面部分，类似于 component 。与 component 不同，fragment 之外的任何标记也会被识别。 |
| include | 将 src 属性定义的文件包含到当前的 Facelets 视图中。 |
| insert | 将内容插入到模板中。（参见 composition 。） |
| param | 在 include 标签内使用，param 能够将参数（键 + 值）传递到被包含的组合或其他合适的标签中。 |
| remove | 在编译时移除包含的标记。可用于注释，这些注释不会渲染到客户端。 |
| repeat | 遍历一个集合。每次迭代，其所有子元素的一份副本都将被包含到组件树中。用于重复元素而无需渲染表格。如果需要表格，请优先使用 dataTable 。 |



### 复合组件标签库

JSF 2.0 及更高版本支持使用 VDL 构建的复合组件。一个组件由两个主要部分组成：接口和实现。复合组件标签库为这两部分提供了标签，也为其他用于修改组件行为的属性提供了标签。表 C-6 列出了这些标签。

###### 表 C-6 复合组件标签

| 标签名称 | 描述 |
| --- | --- |
| ActionSource | 定义复合组件中可附加 actionListener 的部分。 |
| attribute | 在接口内定义一个属性（参数），该属性可从使用组件传递到复合组件。 |
| clientBehavior | 定义一个客户端行为。通常是调用（使用）组件可以订阅的子组件事件。 |
| editableValueHolder | 定义一个或多个可附加 converter 标签或 valueChangeListener 的子组件。 |
| extension | 可用于根据 JSR 276 将设计时元数据传递给复合组件。JSR 276 处于“休眠”状态。 |
| facet | 可在接口内用于声明一个具名 facet，该 facet 可从使用组件传递到实现中的 facet 声明。 |
| implementation | 此标签封装了组件的实现。在该标签内，可以排列任意组件来定义该组件。 |
| insertChildren | 定义一个插入点，用于插入一个或多个已嵌套到使用组件中的组件。 |
| insertFacet | 定义插入具名 facet（如接口部分所定义）的位置。 |
| interface | 定义组件的接口。 |
| renderFacet | 将一个具名 facet 插入到复合组件的实现部分。 |
| valueHolder | 定义一个或多个可附加 converter 标签的子组件。 |

### 直通属性

这仅是一个标记命名空间。如 JSF 规范所述，它可以声明为 xmlns:p=" [`xmlns.jcp.org/jsf/passthrough`](http://xmlns.jcp.org/jsf/passthrough) "。

如果 JSF 检测到由 p:（或您通过命名空间声明选择的任何别名）标记的属性，它将直接渲染该属性而不做修改。

在直通属性随 JSF 2.2 出现之前，p: 别名通常用于 PrimeFaces 库，因此您可以选择改用 pt:。

### JSP 标准标签库 (JSTL)

在 JSF 2.0 之前，JSP 是唯一的视图定义语言 (VDL)。现在 Facelets 是首选的 VDL，并且主要增强功能仅适用于 Facelets。为了向后兼容，仍然支持 JSP，因此也支持 JSTL。这些标签中的大部分可能被 Facelet 标签取代。但在某些特殊情况下，使用标签处理器而不是 UI 组件可能更合适，例如使用 forEach 代替 repeat。下表仅显示了 NetBeans 自动补全提供的 JSTL 部分。表 C-7 列出了这些标签。

###### 表 C-7 JSTL 属性

| 标签名称 | 描述 |
| --- | --- |
| catch | 可用作父标签，以捕获其子标签处理过程中抛出的所有异常。 |
| choose | 用于一系列 when 和 otherwise 的父标签。类似于 Java 的 switch 语句。 |
| forEach | 遍历一个集合或值的范围。 |
| if | 测试一个条件。仅当测试结果为 true 时，才处理其所有嵌套元素。 |
| otherwise | choose 的子标签。类似于 Java switch 的 default。 |
| set | 设置变量的值。通常是 EL 表达式（或 JSTL 函数）的结果。 |
| when | choose 的子标签。类似于 Java switch 的 case。 |

### JSTL 函数

这些函数同样是为了向后兼容而存在。通常它们可以完全被 EL 替代。此处列出它们是为了完整性，但我在本书中不会讨论它们。表 C-8 列出了这些标签。

###### 表 C-8 JSTL 函数

| 标签名称 | 描述 |
| --- | --- |
| contains(dataString, search) | 如果 dataString 中包含 search，则返回 true。 |
| containsIgnoreCase(dataString, searchString) | 同上，但不区分大小写。 |
| endsWith(string, subString) | 测试 string 是否以 subString 结尾。 |
| escapeXml(string) | 将可被解释为 XML 标记的字符替换为 XML 实体，例如 < 变为 &lt。 |
| indexOf(string, subString) | 返回 subString 在 string 中的索引。 |
| join(stringArray, delimiter) | 将数组的所有元素用分隔符连接起来。 |
| length(string) | 返回 string 的长度。 |
| replace(string, find, replace) | 将 string 中所有出现的 find 替换为 replace。 |
| split(string, delimiter) | 用分隔符将 string 分割成一个字符串数组。 |
| startsWith(string, subString) | 测试 string 是否以 subString 开头。 |
| substring(string, start, end) | 返回由索引 start 和 end（不包含）定义的 string 的一部分（子字符串）。 |
| substringAfter(string, subString) | 返回 string 中 subString 之后的部分。 |
| substringBefore(string, subString) | 返回 string 中 subString 之前的部分。 |
| toLowerCase(string) | 返回转换为小写的 string。 |
| toUpperCase(string) | 返回转换为大写的 string。 |
| trim(string) | 返回去除首尾空白字符后的 string。 |



