# 14. 用户与会话

用户首先通过登录页面访问您的网站。用户的电子邮件和密码会在登录 Servlet 中与数据库进行验证。一个电子邮件与正确密码匹配的用户被认为是有效用户。该有效用户会从数据库中检索出来，并存储在 `User` 对象中。回想一下，`User` 对象是通过“JPA 从表生成实体”选项生成的。

存储在会话中的 `User` 对象可以轻松地被您网站中的每个 Servlet 或 JSP 访问。登录 Servlet 验证了用户并将其发送到下一个页面，但下一个页面对此用户一无所知。我们将用户放入会话中，这样下一个页面以及我们应用程序中的其他页面就可以访问会话，从而访问该用户。参见清单 14-1。

```
@Entity
@NamedQuery(name="Bhuser.findAll",
query="SELECT b FROM Bhuser b")
public class Bhuser
implements Serializable {
@Id
@GeneratedValue(
strategy=GenerationType.IDENTITY)
private long bhuserid;
@Temporal(TemporalType.DATE)
private Date joindate;
private String motto;
private String useremail;
private String username;
private String userpassword;
清单 14-1
用户类的私有成员变量与用户表对应
```

每个用户可以创建多个帖子。数据库中的每个帖子都包含指向该用户的用户 ID。用户表通过将所有帖子放在一个列表中来跟踪这些帖子。因此，`User` 对象包含该用户所有帖子的列表，而不仅仅是 `PostId`。参见清单 14-2。

```
@OneToMany(mappedBy="bhuser")
private List bhposts;
清单 14-2
帖子的私有成员变量与帖子表对应。用户包含帖子，因此帖子被实现为一个列表。
```

`user` 类是一个 POJO——普通 Java 对象。它为每个私有成员变量提供了 getter 和 setter 方法。用户 ID 的 getter 和 setter 方法如清单 14-3 所示。命名约定是：始终使用单词 `get` 或 `set`，后跟首字母大写的私有成员变量名。稍后您会看到，当我们在 JSP 页面中访问该变量时，可以省略 `get` 或 `set` 单词，JSP 仍然能找到正确的值。您无需编写此行为——它是 Java 标准标签库的一部分。

```
public long getBhuserid() {
return this.bhuserid;
}
public void setBhuserid(long bhuserid) {
this.bhuserid = bhuserid;
}
清单 14-3
用户类的 Getter 和 Setter 方法
```

会话允许每个页面显示用户的姓名、电子邮件和 Gravatar 头像。Java Servlet 提供了一个名为 `HttpSession` 的变量，我们用它来跨多个页面请求识别用户。会话在最后一次使用后（默认情况下）会持续二十分钟。

您的程序通过调用 `HttpServletRequest` 的 `getSession()` 方法来获取对 `HttpSession` 对象的引用。该请求存储在一个名为 `request` 的变量中，并由 Servlet 容器 Tomcat 管理。参见清单 14-4。

```
javax.servlet.http.HttpSession session =
request.getSession();
清单 14-4
添加这行代码后，应用程序中的任何 Servlet 都可以访问存储在会话中的对象。
```

将会话视为应用程序所有 Servlet 和 JSP 共享的内存。它的工作方式类似于 Windows 剪贴板。一个 Servlet 将数据放入会话，另一个 JSP 可以访问它的副本。

## 向会话中添加对象

存储在会话中的对象可以被应用程序中的不同页面访问。一旦验证用户身份，就在登录 Servlet 中将对象添加到会话中。然后，该对象就可以在应用程序的其他页面上使用了。参见清单 14-5。

```
User user = new User();
user.setUserName("Larry");
user.setEmail("larry12345@domain.com");
//将用户添加到会话中
session.setAttribute("user", user);
清单 14-5
向会话中添加用户。此代码可在登录 Servlet 中找到。
```

### 从会话中读取值

用户作为对象存储在会话中。当您从会话中检索用户时，需要将其强制转换为 `User` 对象并赋值给一个变量，以便您可以对其进行操作。参见清单 14-6。

```
User user = (User) session.getAttribute("user");
//现在我们可以从类中获取值
String username = user.getUserName();
String email = user.getEmail();
清单 14-6
从会话中检索值
```

