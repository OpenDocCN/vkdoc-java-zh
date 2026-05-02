# 36. 处理常量

Michael Müller^(1 )

(1)德国，北莱茵-威斯特法伦州，布吕尔

JSF 2.3 引入了导入常量以便在表达式语言（EL）中使用的功能。

## 通过字符串导航

JSF 支持内部导航和外部导航。内部导航在源代码中定义，而外部导航则由外部 XML 文件描述。

让我们先看一下清单 36-1 中一个简单的内部导航示例。

###### 清单 36-1 通过字符串直接导航

```
 1   <h:commandLink styleClass="button"
 2                          value="#{msg.btnWelcome}"
 3                          action="/user/welcome.xhtml"/>
```

在第 3 行，我们定义了导航目标。这非常简单，但容易出错。如果 action 中有拼写错误，JSF 可能会正常显示页面，但点击链接不会执行任何操作。而且，如果您想要移动或重命名欢迎页面，则必须更新代码中所有出现该页面的地方。很容易遗漏其中一处。这就是一些开发者更喜欢外部导航的原因。就我个人而言，我不喜欢在 XML 文件中定义导航。对于 Books 应用程序，我使用了不同的策略来缓解这个问题。

在页面中，我们调用一个使用枚举的导航方法。对于页面，我们使用一个逻辑字符串，在调用该方法时，该字符串会自动转换为枚举的一个实例，如清单 36-2 所示。

###### 清单 36-2 自定义枚举导航

```
 1   <h:commandLink styleClass="button"
 2                          value="#{msg.btnWelcome}"
 3                          action="/#{sessionTools.navigate('UserWelcome')}"/>
```

清单 36-3 展示了 Books 导航的一个简短摘录，清单 36-4 展示了该枚举的一个摘录。

###### 清单 36-3 导航方法（作为支持 bean 的一部分）

```
 1   public String navigate(Page page) {
 2     return page.getRedirectUrl();
 3   }
```

###### 清单 36-4 枚举 Page 的摘录

```
 1  public enum Page {

 3    UserTemplate("/user/booksTemplate"),
 4    <此处为其他页面>
 5    ;

 7    private Page(String url) {
 8      _url = url;
 9    }
10   private final String _url;

12   public String getUrl() {
13     return _url + ".xhtml";
14   }

16   public String getRedirectUrl() {
17     return _url + ".xhtml?faces-redirect=true";
18   }
19   ...
20 }
```

现在，如果我们在页面声明中有拼写错误，当页面显示时，我们会立即收到错误。这种明显的错误有助于发现问题。这种方法的主要优点是使用了逻辑名称。如果我们想要重新定位或重命名页面，我们只需要更新枚举，而保持所有页面不变。

## 导入常量

使用当前版本的 Java EE 8/JSF 2.3，我们可以做得更好——这正是我们用于 Alumni 导航的方法。例如，我们选取“忘记密码”链接，如清单 36-5 所示。

###### 清单 36-5 使用枚举进行导航

```
 1    <div>
 2      #{msg.lblForgotPassword}
 3      <h:link value="#{msg.lblRequestPassword}"
 4                    outcome="#{Page.RequestPassword.url()}"/>
 5    </div>
```

看一下第 4 行。这里 outcome 直接由枚举的 url 组成。我们就像在 Java 源文件中一样使用枚举。作为先决条件，我们需要告诉 EL 关于我们的枚举。这就是新的 importConstants 标签发挥作用的地方。在元数据中，我们需要结合完整的类名来声明这个标签，如清单 36-6 所示。

###### 清单 36-6 导入常量以便在 EL 中使用

```
 1    <f:metadata>
 2      <f:importConstants type="de.muellerbruehl.alumni.gui.enums.Page"/>
 3    </f:metadata>
```

尽管 Alumni 只导入了枚举，但您可以将 importConstants 标签用于任何类型的常量。

## 总结

JSF 2.3 引入了一个新标签 importConstants。顾名思义，这个标签用于导入常量，这些常量之后可以在 EL 中使用。在 Alumni 中，此功能被用于（除其他用途外）实现简单、可配置的内部页面导航。

