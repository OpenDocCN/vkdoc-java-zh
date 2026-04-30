# 1. 历史

本章介绍 Jakarta Faces 的历史，从其早期构思开始，一直讲到我们撰写本书时的现状。我们将讨论 Jakarta Faces API（应用程序编程接口）本身是如何演变的，在这一演变过程中发生了哪些重要事件，以及参与其中的一些关键人物。

这绝非完整的历史描述，读者应注意，实际发生的事件和参与的人员远多于我们在此所能提及的。

## 开端……

Jakarta Faces 的历史悠久。其最初的 JSR（JSR 127）始于 2001 年。当时，Struts Web 框架非常流行，尽管它本身发布的时间并不算太久（大约在 2000 年）。尽管 Struts 很受欢迎，但在 Java 领域仍有大量其他 Web 框架在使用，并且新的框架不断涌现。Jakarta Faces 的构想是试图将标准化的 MVC（模型-视图-控制器）Web 框架基础引入整个 Jakarta EE 平台。

在 Web 框架领域，争议相当常见，Jakarta Faces 也不例外。在其诞生之初，就曾引发一场重大争议：Apache 反对创建 Jakarta Faces，理由是 Apache Struts 已经存在，而闭源的替代方案价值不大。因此，Apache 投票反对创建 Jakarta Faces，并发表了以下评论：

> *此 JSR 与 Apache 开源项目 Struts 存在冲突。考虑到 Sun 目前认为 JSR 不能在开源许可下独立实现的立场，我们认为在封闭环境中重新创建一项已在开放环境中可用的技术价值不大。*

> *就本 JSR 目前超越 Struts 的范围而言，我们鼓励提出此 JSR 的 Sun 开发者加入已在领导 Struts 的 Sun 开发者行列，在 Apache 创建一个开放的解决方案，这样完成后就能确保以开源方式实现。*

最终，这场冲突得以解决：大约一年后，规范负责人 Amy Fowler（以 Swing 闻名）被 Craig McClanahan 取代，而 Craig McClanahan 正是据称与 Jakarta Faces 竞争的 Struts 项目的创始人。同时，开源限制也被解除，名为 MyFaces 的开源 Jakarta Faces 实现与（当时尚未命名的）参考实现及规范本身并行开发。MyFaces 最初于 2002 年 12 月在[`http://sourceforge.net`](http://sourceforge.net)上作为一个 LGPL 许可的项目启动，并于 2003 年 1 月发布了符合当时所谓“早期访问规范”的 0.1 版本。

开源实现是 Jakarta EE 8 中最常见的实现方式，而在撰写本书时（2018 年），几乎没有任何 EE 规范仍以闭源方式实现。然而在 2001 年，这不仅是罕见的，而且对于新的 JSR 来说实际上是不被允许的。因此，允许开源实现是一个相当大的变革，而 Jakarta Faces 有幸成为首个获此许可的规范。

尽管允许开源实现，但规范的实际开发仍然秘密进行，不对外公开。没有公开的邮件列表，也没有供公众提交问题或表达愿望的跟踪系统（例如 JIRA 实例）。偶尔会有一些采访，例如在 2002 年秋季，时任前规范负责人 Amy Fowler 确实透露了关于 Jakarta Faces 的不少细节，但总体而言，该项目对公众来说仍笼罩在神秘之中。


然而，Jakarta Faces 背后的团队一直在辛勤工作。第一封发送给 JSR 127 内部列表的电子邮件是在 2001 年 8 月 17 日。与大多数项目一样，团队在最初的几个月里致力于收集需求并研究现有的竞争产品。同时，也选定了一个包名。最初占位用的包名是“`javax.servlet.ui`”，现在则选用了“`javax.faces`”。首先被考虑的技术架构是组件模型。对于一个基于组件的 MVC 框架来说，这显然是其中最重要的方面之一。在 2001 年的最后一个月和 2002 年的头两个月里，团队研究了现在被称为托管 Bean（当时称为“对象管理器”）的概念。托管 Bean 及其作用域、名称和依赖注入，显然是 Jakarta Faces 框架的另一个基石。在此期间，事件及其背后的模型也受到了关注。

在 2002 年第二季度，讨论了 Jakarta Faces 的另外两个基石：表达式语言（受 Jakarta Tags 启发），它对于实现从模板到后端代码的所谓 Bean 绑定至关重要；以及工厂查找器，它允许替换 Jakarta Faces 的关键部分，尽管当时可能并未完全意识到，但这或许对 Jakarta Faces 在 16 年后依然保持其重要性做出了巨大贡献。

也正是在同一季度，Craig McClanahan 接任了规范负责人，他是 Struts 之父，也是 Tomcat Servlet 容器的架构师。在关于使用 Jakarta Pages 的讨论开始后不久，一场讨论开始了——也许当时团队并未意识到——不幸的是，这场讨论后来对 Jakarta Faces 产生了相当负面的影响。大约在 2002 年底，Ed Burns 加入了团队，担任联合规范负责人，他之前也和 McClanahan 一样在 Tomcat 工作过。Burns 最终成为了 Jakarta Faces 的主要规范负责人，并任职超过十年。

当团队继续致力于诸如前述的托管 Bean 和所谓的值绑定（这也是前述表达式语言绑定的 Java 表示形式）等工作时，第一个乌云出现在 2003 年春天，团队成员 Hans Bergsten 意识到使用 Jakarta Pages 作为 Jakarta Faces 的模板语言存在非常现实且重大的问题。他向团队提出了这些担忧，但最终这些问题没有得到解决，反而在接下来的几个月里，团队将精力花在了值绑定的一个变体上；后来才清楚，方法绑定和状态保存机制是 Jakarta Faces 另一个不太理想的实现。

Jakarta Faces 1.0 及其当时尚未命名的参考实现最终于 2004 年 3 月 11 日发布——巧合的是，这仅仅比另一个至今仍很强大的框架 Spring 1.0 的发布早了两周。MyFaces 在几天后的 3 月 19 日发布了其 1.0.0 alpha 版本。一个或许有趣的观察是，Jakarta Faces 在 Spring 之前就带着一个完整的、基于 XML 的依赖注入（DI）框架正式发布了，而 Spring 很大程度上正是以其 DI 功能而闻名。

Jakarta Faces 1.0 普遍受到好评；尽管市场上竞争对手众多，如 Tapestry、WebObjects、Velocity 和 Cocoon，但在发布后的几个月里，Horst Caymann 和 Hans Bergsten 等作者就出版了至少三本相关书籍，并且 eXo 平台（一个数字协作平台）立即开始使用 Jakarta Faces。

然而，Hans Bergsten 早先的担忧几乎同样迅速地变得令人痛苦地清晰；Jakarta Pages 技术基于从头到尾处理模板，并在遇到标签时立即写入响应。而 Jakarta Faces 则需要一种分阶段的方法，在开始向响应写入任何内容之前，组件需要能够检查并根据从页面标签构建的组件树采取行动。这种不匹配导致了许多奇怪的问题，例如内容消失或渲染顺序错乱。

在 Jakarta Faces 推出仅三个月后，Hans Bergsten 在他那篇传奇性的文章《通过抛弃 Jakarta Pages 来改进 Jakarta Faces》中，提出了放弃 Jakarta Pages 的有力论据。在那篇文章中，Bergsten 解释了 Jakarta Pages 作为 Jakarta Faces 的模板语言是多么不合适，但他也带来了一线希望；由于 Jakarta Faces 对可扩展性的出色支持，只需替换所谓的视图处理器（Jakarta Faces 明确允许这样做），就可以相对容易地引入替代模板方案。然而，直到五年后，Jakarta Faces 才真正附带了一种更合适的视图模板语言，尽管那时 Jakarta Pages 基本上已被弃用，但在撰写本文时，它仍然存在于 Jakarta Faces 中。



## 青少年时期

回到 2004 年，Jakarta Faces 迎来了另一个第一；6 月 28 日，Ed Burns 宣布 RI 的源代码由 Sun 公司发布。这标志着一个重要的里程碑，因为在此之前，Sun 公司积极使用的大多数技术都是闭源的。最初，源代码采用的是有些特殊的 JRL 许可证，但后来改为双许可证：GPL（附带类路径例外）和 CDDL。在宣布这一消息的同时，也确立了一项传统：每个新功能或错误修复都应附带一个测试，并且在提交更改之前必须执行所有现有测试。大约 14 年后，从事 RI 源代码工作的人员已经大不相同，项目结构和代码规范也发生了变化，但测试驱动的传统仍然以其原始形式得以保留。

那时，Ed Burns 决定将更多精力放在 Jakarta Faces 的规范方面，因为 Jakarta Faces 1.2 的规范工作已经立即启动，而早期团队成员之一 Jayashri Visvanathan 则承担了实现方面的领导角色，Ryan Lubke 担任 TCK（测试兼容性工具包）工程师。

尽管才问世几个月，各种 Jakarta Faces 组件库已经开始涌现，尽管它们都是商业化的。其中包括来自 Oracle 的 ADF Faces。早在 Jakarta Faces 1.0 最终版发布之前，ADF Faces 就已经被列入 Oracle 的路线图，其首个早期访问版本于 2004 年 8 月 17 日发布。它的负责人是 Adam Winer，他在创建 Jakarta Faces 1.0 的团队中代表 Oracle。ADF Faces 主要包含一组丰富的组件，还有一个对话框框架，并且值得注意的是，它已经具备了部分页面渲染（PPR）功能，这比后来出现的 AJAX 解决方案要领先不少。ADF Faces 还包含一个实际可用的“for each”标签（`af:forEach`）。Adam Winer 在早期就解释说，构建这样的标签并非易事，但承诺 Oracle 会将相关知识回馈给 Jakarta Faces 本身。

ADF Faces 组件主要源自早期的用户界面 XML（UIX）框架，Adam Winer 也是该框架的首席架构师。UIX 的早期版本曾使用过“Cabo”、“Baja”和“Marlin”等名称。UIX 是一个用于浏览器的富客户端框架。由于 Jakarta Faces 与 UIX 有不少相似之处，并且其负责人 Adam Winer 是原始 Jakarta Faces 团队的成员，因此推测 UIX 影响了 Jakarta Faces 或许并非不合理。这些相似之处包括：具有独立渲染器的组件概念、Jakarta Pages 标签处理器、用于组合页面的声明式选项，以及在 Java 中以编程方式实例化相同组件的能力。甚至还有一个概念上相似的数据绑定，尽管语法不那么优雅。例如，UIX 不会使用`value="#{user.age}"`，而是使用`data:value="age@user"`，但还需要在每个页面上定义一种生产者来声明“`user`”的来源，然后将页面内容嵌套在该声明中。相比之下，Jakarta Faces 和 EL 一直使用全局定义，并将避免名称冲突的责任留给用户。

2004 年最早（如果不是最早的话）的开源组件库之一是 Matthias Unverzagt 的 OurFaces。由于当时 Jakarta Faces 没有自己的资源 API（应用程序编程接口）来提供图像等内容，OurFaces 需要在`web.xml`中添加一个 Servlet，即所谓的`SkinServlet`（`ourfaces.common.webapp.SkinServlet`）。其重要性在于，这在当时的 Jakarta Faces 库中变得相当普遍：要求用户在使用组件库之前，手动向`web.xml`添加一些内容。

2004 年最后几个月和 2005 年初的大部分时间，Jakarta Faces 1.2 专家组（EG）都在处理各种 Jakarta Pages 和 EL 问题，例如 Jakarta 标签`<c:forEach>`的支持、Jakarta Pages 中 ID 的生成，以及令人头疼的“内容交织”问题——这指的是前面提到的在渲染时内容出现在响应中错误位置的情况。

虽然 OurFaces 可能是最早的组件库之一，但它并未持久，如今很少有人记得它，甚至听说过它。而另一个起源于 2005 年初的框架则情况不同，那就是 Alexander Smirnov 的 Telamon 框架，后来更名为 Ajax4jsf。这个框架是首批将 Jakarta Faces 与当时新兴的 AJAX 技术相结合的框架之一。Ajax4Jsf 的妙处在于，它可以通过将现有组件包裹在`<a4j:region>`标签中，为那些最初并未考虑 AJAX 支持的现有组件添加 AJAX 支持。这项技术被整合到了 Exadel Visual Component Platform 中，该平台于 2006 年 3 月发布，后来更名为 RichFaces，并成为最令人难忘的 Jakarta Faces 组件库之一。

大约在同一时间，Alexander Smirnov 开始着手开发后来成为 RichFaces 的项目，而一家名为 ICEsoft 的公司则开始开发 Jakarta Faces 组件库。ICEsoft 已经运营了几年，一直致力于开发一款名为 ICEbrowser 的基于 Java 的浏览器，以及一款名为 ICEbrowser beans 的产品，后者是“轻量级、可配置的 JavaBean 组件，可以快速集成到 Java 客户端应用程序中”。在同年（2005 年）6 月 27 日的 JavaOne 大会上，ICEsoft 宣布了其 Jakarta Faces 组件库——ICEfaces。它同样基于 AJAX，但将 AJAX 直接集成到了组件中。ICEsoft 将其特定技术称为“专利申请中的 Direct-to-DOM”，这基本上意味着来自服务器的更改会直接注入到网页的 DOM 树结构中。不过，最终版本并未立即提供，但发布了早期访问版本。该版本是闭源的，但免费使用。

与此同时，Jakarta Faces EG 成员 Jacob Hookom 受到 Hans Bergsten 对 Jakarta Pages 不适用性担忧的启发，勇敢地抓住了机会，开始亲自开发 Bergsten 所设想的替代模板语言。到 2005 年 8 月，这项工作已经进展到一个可用的初始版本。这种模板语言的名字是什么？Facelets！它立即席卷了整个 Jakarta Faces 世界。Kito Mann 在第一个月就在 JSFCentral 上发表了关于它的系列文章的第一部分，几个月后，Richard Hightower 发表了著名的文章“Facelets fits Jakarta Faces like a glove”。

Oracle 在 2005 年也没有停滞不前，在大约 16（！）个早期访问版本之后，它于 2005 年底在安特卫普（现称 Devoxx）的 JavaPolis 会议上宣布，将把 ADF Faces 捐赠给 MyFaces，从而使其成为开源项目。



2006 年 1 月，Jacob Hookom 和 Adam Winer 对 Jakarta Faces 状态保存机制的糟糕实现进行了反思。该机制的工作方式是：首先根据模板创建组件树，然后在请求接近尾声时，盲目地将整棵树连同请求期间可能放入的所有数据一起序列化。在回传过程中，树会从这个序列化形式中恢复（因此得名“恢复视图”阶段）。这是一种巨大的浪费，因为大部分信息已经在模板中可用。尤其是在使用客户端状态保存进行 AJAX 请求时，这会造成非常大的负担，但即使将状态存储在服务器上，这也是一个问题，因为它会大幅增加 Jakarta Faces 的内存使用量。再次以如此糟糕的方式进行状态保存的主要原因之一，又与那个决定有关：支持 Jakarta Pages。由于 Jakarta Faces 1.2 即将定稿，不幸的是，已经没有时间在 1.2 版本中修复这个问题了。

尽管此时已经清楚 Facelets 是 Jakarta Faces 的未来，但当 Jakarta Faces 1.2 最终于 2006 年 5 月发布时，它仍然只包含 Jakarta Pages。不过，情况也并非全然糟糕。得益于 Jakarta Faces 和 Jakarta Pages 专家组之间的合作，Jakarta Pages 的一个修订版——Jakarta Pages 2.1 发布了，它更好地满足了 Jakarta Faces 的需求。除此之外，Jakarta Pages 的表达式语言和 Jakarta Faces 的表达式语言被合并了。其成果就是 UEL（统一表达式语言）。UEL 的一个非常实用的优势是，Jakarta Faces 组件不再需要手动将字符串转换为表达式，而是直接从模板语言接收一个 `ValueExpression`。Jakarta Pages 2.1 和 Jakarta Faces 1.2 都成为了同时发布的 Jakarta EE 5 的一部分。

2006 年 6 月 13 日，MyFaces 社区宣布，捐赠的项目将更名为 Trinidad。不过，Oracle 的 ADF Faces 仍然存在，但它基于 Trinidad，并增加了一些额外功能（例如支持 Portal、JSR 227 等）。就在此两周前，即 2006 年 5 月 31 日，ICEsoft 宣布了其免费的（尽管仍是闭源的）社区版。几个月后，即 2006 年 11 月 14 日，ICEsoft 在 MPL 许可下完全开源了 ICEfaces。当时仍为闭源并由 Exadel 销售的 RichFaces 也不甘落后，大约四个月后，即 2007 年 3 月 29 日，Exadel 宣布与 Red Hat 合作，使 RichFaces 在开源许可下可用，并通过其 JBoss 集团提供和支持。

## 走向成熟

2007 年 5 月 22 日，Jakarta Faces 2.0 的规范工作开始了。其范围雄心勃勃，不仅承诺修复人们一直抱怨的许多问题，还引入了相当多的新功能。在 JSR 中提到的众多目标中，有一个从宏观角度来看特别有趣——从 Jakarta Faces 中提取托管 Bean 设施，并使其可用于整个平台。

2007 年秋季，社区就 Jakarta Faces RI 的名称进行了投票。四个名字脱颖而出，但正如通常情况那样，这些名字都未能通过 Sun 法律部门的批准。最终，提出了 Mojarra，也许让一些人惊讶的是，这个名字确实通过了法律审查。当时的主要 Jakarta Faces 提交者之一 Ryan Lubke 于 2007 年 12 月 5 日做出了正式宣布。

大约一年后，即 2008 年 10 月 29 日，Çağatay Çivici 启动了一个新库 PrimeFaces，作为他较旧的 YUI4JSF Jakarta Faces 组件库之后的新起点。这个名字来源于 Çağatay 的昵称，即擎天柱，虚构的变形金刚宇宙中英勇的汽车人领袖。Çivici 参与 Jakarta Faces 开发已有很长时间；大学本科毕业后，他就开始从事一个使用 Jakarta Faces 的军事项目。当时，这是闭源的 Sun 版本，但 Çivici 对 MyFaces 更感兴趣，因为它已经是开源的。在他为这个项目提供了几个补丁后，他被邀请成为提交者。Çivici 在居住在英国期间曾为 Spring Source 工作，从事 Web Flow 和 Jakarta Faces 的集成工作。最终，Spring Source 没有继续推进这项工作，之后 Çivici 为位于英国雷丁的 Visa 工作。PrimeFaces 的许多代码都是 Çivici 在从伦敦到雷丁的通勤路上编写的。

PrimeFaces 最初基于 Jakarta Faces 1.x，但随着 Jakarta Faces 2.x 的临近以及项目尚处于早期阶段，它很快转向了 Jakarta Faces 2.x。

2009 年 7 月 1 日，期待已久的 Jakarta Faces 2.0 终于到来。Jakarta Faces 2.0 确实修复了业界对 Jakarta Faces 几乎所有的抱怨；最终，Facelets 被作为默认的视图模板语言包含在内。Jakarta Pages 实际上已被弃用。Hookom 和 Winer 在三年前提出的状态保存问题也得到了解决；从那时起，Jakarta Faces 只保存增量状态（状态变化），并且在恢复视图时，组件树是从模板重新加载的，而不是实际恢复。

多年来 Jakarta Faces 社区提出的另一个重大关切——Jakarta Faces 过分强调回传——也得到了解决；GET 请求在 Jakarta Faces 2.0 中成为了一等公民。Jakarta Faces 一个众所周知的可用性问题，有时被称为“陷阱”，即对于许多操作，所涉及的数据在原始请求和回传期间必须相同。这在 Jakarta Faces 1.x 中并不容易保证。Jakarta Faces 2.0 为此引入了所谓的视图作用域，优雅地解决了这个问题。自定义组件的创建，这是 Jakarta Faces 1.x 的另一个问题领域，也变得更加简单。Jakarta Faces 2.0 还引入了对 AJAX 的核心支持（其模型借鉴了 Ajax4Jsf 的工作方式）、一个资源 API、系统事件以及其他一些功能。

Jakarta Faces 2.0 的目标之一——使其托管 Bean 设施可在 Jakarta Faces 之外使用——通过 CDI 规范间接实现了，该规范与 Jakarta Faces 2.0 一起在 Jakarta EE 6 中引入。CDI 规范本身也有很长的历史，但其决定性特征之一是 CDI Bean 强烈基于 Jakarta Faces 托管 Bean，并且本质上是这些 Bean 的超集。



所有这些修复和新功能的综合影响，实际上将社区分成了两派：一派是那些使用过 Jakarta Faces 1.x 后就再未关注过它的人，另一派则是转向 Jakarta Faces 2.x 的人，或者更具体地说，是从 2.0 开始使用 Jakarta Faces 而从未接触过 1.x 的人。这常常引发激烈的争论，1.x 一方认为 Jakarta Faces 很糟糕，而 2.x 一方则完全无法理解为何会如此。即使在撰写本文时（距离 Jakarta Faces 2.0 发布已近 9 年，比 Jakarta Faces 1.x 存在的整个时期还要长），这些情绪在某种程度上依然存在。

尽管 Jakarta Faces 2.0 做了很多正确的事情，但有一个错失的良机：即使 CDI 已经可用并取代了 Jakarta Faces 的托管 Bean，Jakarta Faces 却选择不立即弃用其托管 Bean 功能。更糟糕的是，它引入了一种基于注解的替代方案，取代了 Jakarta Faces 1.x 用于定义托管 Bean 的基于 XML 的系统。既然 CDI 已经提供了像 `javax.enterprise.context.RequestScoped` 这样的注解，同时又引入一个功能完全相同的 `javax.faces.bean.RequestScoped` 注解，这充其量只能说是值得商榷的。专家组似乎意识到了这种冲突，因此设置了一个警告，称这些新注解可能很快就会被平台功能所取代。

2012 年 12 月 23 日，Cay Horstmann 在一篇题为“`@javax.faces.bean.ManagedBean` 是否一出生就已死亡？”的文章中表达了他对这种非常不受欢迎情况的担忧。回应非常明确：包括 Jakarta EE 书籍作者 Antonio Goncalves 在内的人们，要求尽快纠正 Jakarta Faces 2.0 犯下的这个巨大错误，并在即将到来的 Jakarta Faces 2.1 维护版本中立即弃用 `javax.faces.bean.ManagedBean`。该维护版本被呼吁，除其他事项外，也是为了纠正另一个错误（即 Jakarta Faces 2.0 引入的问题：除了自定义 `ResourceResolver` 之外，还必须提供一个自定义的 `ExternalContext`，这一点非常不明确）。为什么 `javax.faces.bean.ManagedBean` 最终没有在 Jakarta Faces 2.1 MR 中被弃用，至今仍是一个谜。

虽然针对 Jakarta Faces 1.x API 编写的应用程序大多可以在 Jakarta Faces 2.0 上不加修改地运行，或者只需要进行少量更改，但组件库却经历了更艰难的时期。具体来说，平台提供的 AJAX 支持意味着现有的组件库必须放弃自己的 AJAX 实现，转而基于标准 API。显然，这绝非易事，组件库的迁移花费了很长时间，有些甚至从未真正完成切换。

在这方面，PrimeFaces 显然具有优势。作为一个相对较新、没有太多历史包袱的库，它可以轻松切换，而 RichFaces 和 ICEfaces 则举步维艰。有一段时间，PrimeFaces 实际上是 Jakarta Faces 2 唯一可用的组件库。PrimeFaces 的流行度飙升始于 Jakarta Faces 2.0 发布之后，而这恰好也是 ICEfaces 和 RichFaces 似乎开始变得不那么流行的时候。尽管必须指出，很难获得确切的统计数据，并且这些数据包含许多方面（下载量、部署量、书籍、提问数量、可用职位、考虑不同行业等），但在 2012 年左右，PrimeFaces 似乎已经成为更受欢迎的 Jakarta Faces 组件库。

一年前，即 2011 年 3 月，一位在 Samhammer AG 工作的德国软件开发人员 Thomas Andraschko 启动了一个为 PrimeFaces 提供多个附加组件的项目，名为 PrimeFaces Extensions。Andraschko 后来在 Jakarta Faces 中扮演了非常重要的角色。

同年年初，即 2012 年 2 月 19 日，Arjan Tijms 和 Bauke Scholtz（巧合的是，他们也是本书的作者）启动了 Jakarta Faces 的 OmniFaces 库。OmniFaces 的目标是成为 Jakarta Faces 的一个实用工具库，本质上就像 Apache Commons 和 Google Guava 之于 Java SE 一样。Tijms 和 Scholtz 曾共同开发过一个基于 Jakarta Faces 的网站，他们发现两人都有一套私有的 Jakarta Faces 实用程序，并在不同项目中重复使用，而且大量类似的实用程序在许多 Jakarta Faces 项目中基本上被一遍又一遍地重写，部分散落在论坛消息和博客文章等地方。OmniFaces 的建立尤其不是为了与 PrimeFaces 等组件库竞争，而是为了与它们协同工作。因此，面向视觉的组件在很大程度上不在 OmniFaces 的范围内。

2012 年，Jakarta Faces 2.2 的规范制定工作也如火如荼地进行着。Jakarta Faces 2.2 最终于次年 5 月 21 日发布。Jakarta Faces 2.2 特别提出了 Facelets 可以运行的替代模式的正式版本；不是在视图上放置组件标签，而是放置纯 HTML，并通过一个特殊的 ID 将标签链接到组件。这种模式一般来说对 Jakarta Faces 开发者的吸引力稍小，但特别吸引那些可以为此类视图更轻松地使用纯 HTML 工具的网页设计师。Jakarta Faces 2.2 还引入了一个兼容 CDI 的 `@ViewScoped` 注解，这消除了在 Jakarta Faces 2.1 中仍然使用 Jakarta Faces 托管 Bean 功能的最后一个理由之一，即在该版本中，`@ViewScoped` 仅适用于那些 Bean。Jakarta Faces 2.2 还引入了两个重要的新特性：Faces Flow 和 Resource Contracts，但这些特性在实践中似乎很少被采用。

2013 年 1 月，Çağatay Çivici 宣布 Thomas Andraschko 已作为提交者加入 PrimeFaces 团队。不久之后，Andraschko 成为 PrimeFaces 的社区负责人和该项目的顶级提交者之一。

就在 Jakarta Faces 2.3 启动之前，2014 年 7 月 20 日，RichFaces 负责人 Brian Leathem 在他的博客上宣布，RichFaces 的下一代版本 RichFaces 5 将被取消。取而代之的是，RichFaces 将“走一条稳定而非创新的道路”，这意味着 JBoss 将使 RichFaces 4.x 与 Jakarta Faces 2.2 兼容，并移植回一些为 RichFaces 5 开发的功能。虽然这篇博文有些乐观，但看起来 RichFaces 的结局已定。

2014 年 8 月 26 日，Jakarta Faces 2.3 的规范制定工作启动。引入了一位新的联合规范负责人——Manfred Riem，他此前主要致力于 Mojarra 的实现方面，例如将 Jakarta Faces 闻名的数百个测试从古老且已退役的 Cactus 框架迁移到更现代的基于 Maven 的框架，并确保将大量的 Mojarra 未解决问题减少到可控数量。Jakarta Faces 2.3 的启动伴随着一个或许有些引人注目的消息：Oracle 只有很少的资源可用。在规范制定过程中，这些少量的资源减少到了几乎无人预料到的数字——绝对为零。基本上，在 2015 年 JavaOne 大会之后，几乎所有规范的负责人都不见了，导致大多数规范工作突然停滞。Josh Juneau 在他著名的研究报告“Jakarta EE 8，当前状态如何：自 2015 年底以来已完成工作的案例研究”中报道了这一点，该报告通过展示电子邮件、提交和已解决问题的图表，无可辩驳地表明 Oracle 已经撒手不管了。

幸运的是，Jakarta Faces 及其参考实现 Mojarra 的开放性使得规范制定工作及其在 Mojarra 中的实现很大程度上可以由其他 EG 成员继续进行，事实也确实如此。



同样为 Jakarta Faces 2.3 和 Mojarra 提供支持的还有 Andraschko，他除了是 PrimeFaces 的提交者外，还在 2015 年 7 月成为了 MyFaces 的提交者。Andraschko 将 PrimeFaces 搜索表达式功能的标准化版本捐赠给了 Jakarta Faces 2.3 和 Mojarra。

与此同时，2016 年 2 月 12 日，Red Hat 宣布 RichFaces 将于当年晚些时候（即 2016 年 6 月）停止维护（EOL）。这个曾一度是最受欢迎的 Jakarta Faces 组件库之一、常被称为“三巨头之一”的项目，实际上就此终结。2016 年 6 月 20 日，项目完成了最后一次实质性提交：“RF-14279: update JSDoc。”两天后，Red Hat 发布了 RichFaces 4.5.17，并将 GitHub 仓库设置为归档（只读）模式。Brian Leathem（他至今仍是 Jakarta Faces 2.3 专家组的成员）在几天后的 2 月 18 日宣布，他将不再从事任何与 Jakarta Faces 相关的工作。

## 复兴

2016 年底，Jakarta Faces 规范负责人短暂回归，但带来的消息是规范必须在短短几周内完成，以便启动（有些）冗长的最终定稿流程。2017 年 3 月 28 日，Jakarta Faces 2.3 最终发布，随之而来的是开始用 CDI 版本替换 Jakarta Faces 原生构件，以及一项本应多年前就完成的工作：弃用 Jakarta Faces 托管 Bean 机制，转而使用 CDI Bean。其他特性包括：使用由 OmniFaces 捐赠的 Jakarta EE WebSocket API 支持 WebSocket、对系统中可用视图资源的自省，以及上述由 PrimeFaces 捐赠的搜索表达式框架。

紧随 Jakarta Faces 2.3 规范有些动荡的开发过程之后，Oracle 在 2017 年宣布了更为动荡的消息：Jakarta EE（因此也包括 Jakarta Faces）将移交给 Eclipse 基金会。Oracle 将停止领导其之前拥有的规范，这同样包括 Jakarta Faces。这意味着 Mojarra 将重新授权，而 Jakarta Faces 将通过一个由不同负责人主导的新流程来演进。

从 Oracle 到 Eclipse 的过渡分几个步骤完成。创建了一个名为 Eclipse Enterprise for Java (EE4J) 的新顶级项目，并对 Jakarta Faces 2.3 分支的快照进行了（法律意义上的）清理，然后将其移动到了位于 [`​github.​com/​eclipse-ee4j`](http://github.com/eclipse-ee4j) 的新 GitHub 仓库。只有 Jakarta Faces 2.3（以及相关的 Mojarra 2.3）被迁移，这意味着 Jakarta Faces 2.2 及更早版本仍保留在 Oracle 的位置。这特别意味着提交历史没有被转移。在迁移的第一步之后，API 于 2019 年 1 月发布了新版本 *jakarta.faces:jakarta.faces-api:2.3.1*，该版本与现有的 *javax.faces:javax.faces-api:2.3* 基本相同。API 和 Mojarra 实现的许可证都更改为 Eclipse Public License 2.0。Arjan Tijms 成为下一任项目负责人。前任规范负责人 Ed Burns 和 Manfred Riem 不久后离开 Oracle 前往微软工作。不过，Riem 将继续参与 Jakarta Faces 的工作。

迁移过程的下一步涉及创建新的认证流程和新的规范许可证。JCP 流程被 Jakarta EE 规范流程 (JESP) 取代，新的规范许可证变为 Eclipse Foundation Technology Compatibility Kit License (EFTL)。

2019 年初，Tijms 前往德国参加 Javaland 大会，在那里他遇到了 Eclipse 基金会的 Jakarta EE 开发者倡导者 Ivar Grimstad。在德国午后的温暖阳光下，两人讨论了 Jakarta EE 规范的新命名方案。需要新名称，因为 Oracle 与 Eclipse 基金会之间的协议包括一项约定，即不使用 Oracle 之前使用的一些名称，例如 Jakarta EE、Jakarta Faces、JavaServer Faces 和 Jakarta Pages。Tijms 提出了一个更简单的命名方案，基本上废除了名称中尽可能多的各种“填充词”（例如“service”、“api”和“architecture”），这使得像 Jakarta Faces 这样晦涩的缩写成为必要。Ivar 同意了这个方案，并在接下来的几个月里引入了该命名方案；代码、文档和规范都被仔细梳理，以查找并用新名称替换旧名称。对于 Jakarta Faces，名称变成了“Faces”，这是一个合乎逻辑的选择，因为从一开始，包名实际上就是 *javax.faces.**，而不是 *javax.jsf.**。构件 ID 长期以来也一直是“faces”。

同年晚些时候，即 2019 年 9 月，Faces 2.3.2 API 作为 Jakarta EE 8 的一部分发布。该 API 再次与 Jakarta Faces 2.3.1 API 基本相同，但现在所有术语都已替换为新术语，并且它已使用新的 JESP 流程进行了认证。



同月，在 JakartaOne Livestream 2019 大会上，Tijms 在印度尼西亚雅加达的酒店里，通过一场名为“Jakarta Faces 的未来”的演讲，介绍了 Faces 下一版本的一些构想。演讲中，Tijms 提到该版本的核心工作是移除已弃用的功能，例如 2005 年弃用的 Faces 原生表达式语言、2009 年弃用的 Jakarta Server Pages 支持、2016 年弃用的 Faces 原生托管 Bean 等。在功能方面，Tijms 提到默认启用无扩展名视图，并新增一个生命周期阶段，以便在空页面上更轻松地使用当前用于视图操作的功能。

第二步完成后，第三步随即启动。这一步涉及将所有 Jakarta EE API（当然包括 Faces）的命名空间从 `javax.*` 更改为 `jakarta.*`。之所以需要这样做，是因为根据 JCP 规则，包只能通过 JCP 流程进行修改。由于 Jakarta EE 不再遵循该流程，因此必须更改包名。在接下来的几个月里，Tijms 和其他提交者努力修改源代码和文档中的包名，最终于 2020 年 10 月发布了 Faces 3.0，作为 Jakarta EE 9 的一部分。由于时间限制，原定的 Jakarta EE 9（以及 Faces 3.0）JDK 11 兼容性认证计划被迫取消。

Faces 3.0 发布后不久，Tijms 通过一封标题为“启动 Jakarta Faces 4.0”的邮件发送至 faces-dev 列表，开启了 Faces 4.0 的工作。该邮件重申了之前演讲中的一些计划，并增加了 Thomas Andraschko 先前提出的几项 API 增强功能。

2020 年 10 月 23 日，Arjan Tijms 将 Mojarra 分支的版本设置为 4.0.0-SNAPSHOT，标志着 Faces 4.0 的开始。几天后，即 2020 年 10 月 27 日，Tijms 完成了一次历史性的提交：“移除 Jakarta Pages 支持。”这个在 2003 年曾受到 Hans Bergsten 强烈批评的技术决策，正如 Bergsten 所预言的那样给 Faces 带来了巨大痛苦，如今终于被终结。又过了几天，在 2020 年 11 月 1 日，Faces 的原生托管 Bean 系统也寿终正寝。从那一刻起，CDI Bean 成为 Faces 唯一指定的 Bean 类型。

2021 年初，Tijms、Andraschko 和 Scholtz 讨论了如何推进拟议的变更，接下来的几个月里，Mojarra 和 MyFaces 项目中涌现了大量新问题，并提交了大量代码来实现这些拟议的新功能。

与此同时，Jakarta EE TCK 也进行了更新以支持 JDK 11，最终于 2021 年 5 月发布了 Jakarta EE 9.1。由于 Faces API 和 Mojarra 3.0 实现已经兼容 JDK 11，因此无需为它们发布新版本。

下表显示了各个已发布版本的时间线和差异：

| 日期 | Maven 坐标 | 构建来源 | 认证版本 | Javadoc | Java 包 | TCK 级别 | API 级别 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 2017 年 3 月 | javax.faces:javax.faces-api:2.3 | github/javaee | Jakarta EE 8 | Jakarta EE 术语 | javax.faces | JDK8 | JDK8 |
| 2019 年 1 月 | **jakarta**.faces:**jakarta**.faces-api:2.3.1 | github/**eclipse-ee4j** | Jakarta EE 8 | Jakarta EE 术语 | javax.faces | JDK8 | JDK8 |
| 2019 年 9 月 | **jakarta**.faces:**jakarta**.faces-api:2.3.2 | github/**eclipse-ee4j** | **Jakarta** EE 8 | **Jakarta EE** 术语 | javax.faces | JDK8 | JDK8 |
| *2020 年 10 月* | ***jakarta**.faces:**jakarta**.faces-api:3.0* | *github/**eclipse-ee4j*** | ***Jakarta*** *EE 9* | ***Jakarta EE****术语* | ***Jakarta**.**faces* | *JDK8* | *JDK**8* |
| *2021 年 5 月* | ***jakarta**.faces:**jakarta**.faces-api:3.0* | *github/**eclipse-ee4j*** | ***Jakarta*** *EE 9.1* | ***Jakarta EE****术语* | ***Jakarta**.**faces* | *JDK**11*** | *JDK**8* |

2021 年 8 月 21 日，Çağatay Çivici 在 JakartaOne Livestream 土耳其版演讲中介绍了 Faces 的最新状态。他的演讲标题恰如其分地定为“Jakarta Faces 4.0”。在撰写本文时，Faces 4.0 的发布评审计划于 2022 年 5 月 15 日左右开始。

