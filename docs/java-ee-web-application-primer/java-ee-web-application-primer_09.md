# 9. 控制器

我们应用程序中的控制器层包含处理特定业务逻辑的代码。这包括从网页接收数据、将数据发送到服务层的类，以及根据情况将用户引导至下一个 Servlet 或 JSP 等职责。控制器不直接访问数据库。控制器会确定需要执行的操作，然后在服务层或表示层找到合适的类，并调用该类来完成工作。在我们的应用程序中，控制器的大部分代码都位于 Java Servlet 中。

## 什么是 Servlet？

Servlet 是响应传入 HTTP 请求的 Java 类。当您浏览某个 URL 或提交表单时，浏览器会发送该请求。Servlet 位于 Web 服务器（Tomcat）内，并监听请求。然后，它们会立即启动并处理请求。可以想象成“接线员正在待命！”当您创建一个 Servlet 时，实际上是在扩展 Servlet 容器（Tomcat）的功能。可以将 Tomcat 视为一个 Web 服务器，它除了处理 HTML 之外，还知道如何处理 Servlet 和 JSP 文件。

您的 Servlet 的 URL（网址）看起来会像 `http://localhost:8080/webTest/SimpleServlet`，其中 `localhost` 是代表您计算机的名称，`8080` 是端口号，`webTest` 是应用程序（或项目）名称，而 `SimpleServlet` 是 Servlet 的 `@WebServlet` 注解中指定的 Servlet URL。

清单 9-1 中的 Servlet 代码展示了一个可以在 `@WebServlet` 属性中指定的 `SimpleServlet` URL 下找到的 Servlet。它不包含处理请求的代码，仅展示结构。一个 Servlet 包含两个方法：`doGet` 和 `doPost`。每个方法对应表单的 get 或 post 方法。当表单提交时，如果您仅使用表单值来获取只读数据（例如另一个网页），则应将表单标签的 method 属性设置为 `GET`。如果 Web 表单使用 `GET`，数据会在 URL 中传输。您可以在 URL 中 `?` 符号后面看到参数及其值。这允许用户复制并粘贴链接，从而再次获得相同的结果。

当向服务器提交数据以录入数据库时，您应该使用 `POST` 方法。`POST` 不使用 URL 来提交数据。参数和值会通过一个数据包传输到服务器。这有几个优点，包括表单及其提交的数据无法被添加书签。使用 `POST` 还允许表单提交更大量的数据。

```
@WebServlet("/SimpleServlet")
public class SimpleServlet
extends HttpServlet {
protected void doGet(
HttpServletRequest request,    HttpServletResponse response)
throws ServletException, IOException {
//处理 GET 请求的代码写在这里
}
protected void doPost(
HttpServletRequest request,    HttpServletResponse response)
throws ServletException, IOException {
//处理 POST 请求的代码写在这里
}
}
清单 9-1
一个简单的 Web Servlet（不含导入语句）
```

## 将表单数据传入 Servlet

当用户点击提交按钮时，表单中的数据会通过请求对象发送到 Servlet。Web 服务器负责处理此事。Servlet 容器（Tomcat）将使请求对象对您的 Servlet 可用。您名为 `userName` 的输入将包含用户键入的名称。Servlet 可以使用以下代码读取该名称：

```
//使用请求中的值设置一个变量
String userEmail = request.getParameter("userEmail");
```

## 将数据发送到下一个页面

您可以将任何数据（包括诸如 `ArrayLists` 之类的对象）添加到请求或会话中。这些数据将对 Servlet 调用的下一个页面可用。请注意，来自传入请求的请求包不会保留在作用域内，因此有必要将数据放回一个新的请求包中。

```
//将值放回 Servlet 的请求中
request.setAttribute("userEmail", userEmail);
```

现在，Servlet 已将数据保存在名为 `userEmail` 的变量中。您现在只是在编写 Java 代码，因此可以按您认为合适的方式处理它。我们将使用 `userEmail` 和 `userPassword` 变量来保存数据，然后验证它们是否符合我们的预期。起初，我们只会创建一个方法来验证已知的值。之后，我们将使用数据库来存储有效数据，并创建 Java 代码来查询数据库以检查结果。

一旦 Servlet 验证了用户，他们将被重定向到主页。如果他们输入了无效密码，他们将被重定向回登录页面。

## Servlet 如何找到下一个页面

在 Servlet 验证并处理完所有传入数据后，您需要指示它将用户带到下一个页面。

Servlet 的 `doPost` 或 `doGet` 方法中的最后一行将处理此操作。当 Servlet 执行到这行代码时，它会将用户发送到正确的页面。

```
//根据 nextURL 变量的值重定向到下一个页面
String nextURL = "home.jsp";
getServletContext().getRequestDispatcher(nextURL)
.forward(request,response);
```

## 如何在输出页面上设置值

创建一个名为 `home.jsp` 的 JSP 页面。将以下代码添加到您的页面，以便它能够读取来自 Servlet 的参数值。符号 `${userEmail}` 将从请求包中读取参数。您在 Servlet 中设置了该参数。

```

我的表单结果

使用 GET 方法读取表单数据

名字: ${firstName} 
姓氏: ${lastName} 

```

## 注销按钮如何工作

当用户想要注销时，您只需结束他们的会话即可。这使得注销变得简单。大多数用户不会点击注销按钮。对于他们来说，会话会在达到会话超时时结束。您可以设置会话超时属性，或者使用默认的 20 分钟。

创建一个表单，该表单将向您的登录 Servlet 传递一个名为 `action`、值为 `logout` 的参数。要传递该参数，您将创建一个隐藏输入，并为其指定名称和 ID 为 `action`，值为 `logout`。

表单的 action 将是登录 Servlet 的名称。当 Servlet 接收到该参数时，它将使会话失效，并将用户重定向到登录页面。用于结束会话的 Java 代码是 `session.invalidate();`。



## 登录 Servlet 代码

```
//LoginServlet.java
package controller;
/*
* 登录 servlet 处理 login.jsp。该 servlet 只有一个任务，
* 即验证用户并将其添加到会话中，以便所有页面都能访问该用户。
* 如果用户无效，则登录 servlet 将重定向回登录页面。
*/
import java.io.IOException;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;
import service.DbUser;
import model.Bhuser;
@WebServlet("/LoginServlet")
public class LoginServlet extends HttpServlet {
private static final long serialVersionUID = 1L;
public LoginServlet() {
super();
}
protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
//此页面不需要用户登录
String useremail = request.getParameter("email");
String userpassword = request.getParameter("password");
String action = request.getParameter("action");
//String remember = request.getParameter("remember");
String nextURL = "/error.jsp";
//获取会话实例，以便我们可以向其设置属性
//JSP 和 NavBar 将从会话中读取数据
//会话是在原本无状态的 Web 应用中维护状态的主要方式之一
HttpSession session = request.getSession();
//创建用户实例并将其放入会话中
//仅当用户有效时才将用户添加到会话中。
//用户的存在用于确定谁拥有该站点，并将用于连接数据库
if (action.equals("logout")){
session.invalidate();
nextURL = "/login.jsp";
}else{
if (DbUser.isValidUser(useremail,userpassword)){
Bhuser user = DbUser.getUserByEmail(useremail);
session.setAttribute("user", user);
int gravatarImageWidth = 30;
String gravatarURL =
DbUser.getGravatarURL(useremail, gravatarImageWidth);
session.setAttribute("gravatarURL", gravatarURL);
nextURL = "/home.jsp";
}else{
nextURL = "/login.jsp";
}
}
//根据 nextURL 变量的值重定向到下一页
getServletContext().getRequestDispatcher(nextURL).forward(request,response);
}
}
//LoginServlet.java 结束
列表 9-2
登录 Servlet 的代码
```

## 新闻动态 Servlet 代码

```
//Newsfeed.java
package controller;
import java.io.IOException;
import java.util.List;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;
import model.Bhpost;
import service.DbPost;
@WebServlet("/Newsfeed")
public class Newsfeed extends HttpServlet {
private static final long serialVersionUID = 1L;
public Newsfeed() {
super();
}
protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
//用户可以通过 GET 请求访问此 servlet，因此在此处处理
//对于 GET 请求，参数是 URL 的一部分。
//我们已经在 doPost 中处理了所有内容，因此只需调用它即可。
doPost(request,response);
}
protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
long filterByUserID = 0;
String searchtext = "";
//设置下一页的值。它应在下面的代码中更改。
String nextURL = "/error.jsp";
//从会话中获取用户。
//如果用户不存在，则将它们发送回登录页面。
//同时销毁会话。
HttpSession session = request.getSession();
if (session.getAttribute("user")==null){
nextURL = "/login.jsp";
session.invalidate();
response.sendRedirect(request.getContextPath() + nextURL);
return;//返回以防止错误
}
//根据参数获取帖子；如果没有参数，则获取所有帖子
List posts = null;
if (request.getParameter("userid")!=null
&& !request.getParameter("userid").isEmpty()){
filterByUserID = Integer.parseInt(request.getParameter("userid"));
posts = DbPost.postsofUser(filterByUserID);
}else if (request.getParameter("searchtext")!=null
&& !request.getParameter("searchtext").isEmpty()){
searchtext = request.getParameter("searchtext").toString();
posts = DbPost.searchPosts(searchtext);
}else{
posts = DbPost.bhPost();
}
//将帖子添加到请求中
request.setAttribute("posts", posts);
//在 newsfeed.jsp 中显示帖子
nextURL = "/newsfeed.jsp";
//根据 nextURL 变量的值重定向到下一页
getServletContext().getRequestDispatcher(nextURL).forward(request,response);
}
}
//Newsfeed.java 结束
列表 9-3
新闻动态 Servlet 的代码
```

## PostServ Servlet 代码

```
//PostServ.java
package controller;
import java.io.IOException;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;
import java.util.Calendar;
import java.util.Date;
import model.Bhpost;
import model.Bhuser;
import service.DbPost;
@WebServlet("/PostServ")
public class PostServ extends HttpServlet {
private static final long serialVersionUID = 1L;
public PostServ() {
super();
}
protected void doPost(HttpServletRequest request,
HttpServletResponse response)
throws ServletException, IOException {
String posttext = request.getParameter("posttext");
String nextURL = "/error.jsp";
//从会话中获取用户。如果用户不存在，则
//结束会话并将他们发送回登录页面。
HttpSession session = request.getSession();
if (session.getAttribute("user")==null){
nextURL = "/login.jsp";
session.invalidate();
} else {
//从会话中获取用户
Bhuser bhuser = (Bhuser)session.getAttribute("user");
//插入帖子
Bhpost bhPost = new Bhpost();
bhPost.setBhuser(bhuser);
Date postdate = Calendar.getInstance().getTime();//今天的日期
bhPost.setPostdate(postdate);
bhPost.setPosttext(posttext);
DbPost.insert(bhPost);
nextURL = "/Newsfeed";//转到新闻动态 servlet 以显示所有帖子
}
//nextURL 的值将是 newsfeed、login 或 error
getServletContext().getRequestDispatcher(nextURL)
.forward(request, response);
}
}
//PostServ.java 结束
列表 9-4
PostServ Servlet 的代码
```



## 个人资料 Servlet 代码

```
//ProfileServlet.java
package controller;
import java.io.IOException;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;
import java.text.SimpleDateFormat;
import service.DbUser;
import model.Bhuser;
@WebServlet("/ProfileServlet")
public class ProfileServlet extends HttpServlet {
private static final long serialVersionUID = 1L;
public ProfileServlet() {
super();
}
protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
doPost(request,response);
}
protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
/*
* 简化逻辑，使其始终需要两个参数：userid 和 action
* action 的值为 view 或 edit。如果是 edit，则会话中用户的 userID 必须与要编辑的个人资料的 userID 相同
* 因为只能编辑自己的资料。
* 所有访问此页面的 URL 都必须包含这两个参数，否则会报错。
*/
HttpSession session = request.getSession();
String nextURL = "/error.jsp";
long userid = 0;
String action = "";
Bhuser profileUser = null;
Bhuser loggedInUser = null;
//从会话中获取用户。如果用户不存在，则将其重定向到登录页面。
//同时销毁会话。
if (session.getAttribute("user")==null){
nextURL = "/login.jsp";
session.invalidate();
response.sendRedirect(request.getContextPath() + nextURL);
return;//提前返回以防止错误
}
try{
userid = Long.parseLong(request.getParameter("userid"));
action = request.getParameter("action");
//如果 action 等于 updateprofile，则更新请求变量中的用户资料
if (request.getParameter("action").equals("updateprofile")){
long uid = Long.parseLong(request.getParameter("userid"));
String userEmail = request.getParameter("useremail");
String userMotto = request.getParameter("usermotto");
Bhuser updateUser = DbUser.getUser(uid);
updateUser.setMotto(userMotto);
updateUser.setUseremail(userEmail);
DbUser.update(updateUser);
}
//根据参数获取用户
profileUser = DbUser.getUser(userid);
//从会话中获取当前用户
loggedInUser = (Bhuser) session.getAttribute("user");
if (profileUser.getBhuserid()==loggedInUser.getBhuserid()){
//以表单形式显示个人资料
//会话变量 editProfile 用于 JSP 以编辑模式显示个人资料
session.setAttribute("editProfile", true);
}else{
//以只读模式显示个人资料
//会话变量 editProfile 用于 JSP 以只读模式显示个人资料
session.setAttribute("editProfile", false);
}
//在属性中填充数据
int imgSize = 120;
SimpleDateFormat sdf = new SimpleDateFormat("MMM d, yyyy");
String joindate = sdf.format(profileUser.getJoindate());
request.setAttribute("userid", profileUser.getBhuserid());
request.setAttribute("userimage",
DbUser.getGravatarURL(profileUser.getUseremail(), imgSize));
request.setAttribute("username", profileUser.getUsername());
request.setAttribute("useremail", profileUser.getUseremail());
request.setAttribute("usermotto", profileUser.getMotto());
request.setAttribute("userjoindate", joindate);
nextURL = "/profile.jsp";
}catch(Exception e){
//打印异常，以便在测试应用程序时查看
//在生产环境中，不建议打印到控制台，因为这会消耗资源且不可见
System.out.println(e);
}
//根据 nextURL 变量的值重定向到下一页
getServletContext().getRequestDispatcher(nextURL)
.forward(request,response);
}
}
//ProfileServlet.java
清单 9-5
个人资料 Servlet 的代码
```

## 添加用户 Servlet 代码

```
//AddUser.java
package controller;
import java.io.IOException;
import java.util.Calendar;
import java.util.Date;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;
import model.Bhuser;
import service.DbUser;
@WebServlet("/AddUser")
public class AddUser extends HttpServlet {
private static final long serialVersionUID = 1L;
public AddUser() {
super();
}
protected void doPost(HttpServletRequest request,
HttpServletResponse response) throws ServletException, IOException {
HttpSession session = request.getSession();
//此页面不需要用户登录
String userName = request.getParameter("userName");
String userEmail = request.getParameter("userEmail");
String userPassword = request.getParameter("userPassword");
String userMotto = request.getParameter("userMotto");
String nextURL = "/error.jsp";
//检查用户是否已存在（通过邮箱）
Bhuser user = DbUser.getUserByEmail(userEmail);
//如果用户不存在，则创建并添加用户
if (user == null){
user = new Bhuser();
user.setUsername(userName);
user.setUseremail(userEmail);
user.setUserpassword(userPassword);
Date joindate = Calendar.getInstance().getTime();
user.setJoindate(joindate);
user.setMotto(userMotto);
DbUser.insert(user);
nextURL = "/home.jsp";
}else{
String message = "您已拥有一个账户 - ";
request.setAttribute("message", message);
nextURL = "/login.jsp";
}
//将用户添加到会话中
session.setAttribute("user", user);
//根据 nextURL 变量的值重定向到下一页
getServletContext().getRequestDispatcher(nextURL)
.forward(request,response);
}
}
// AddUser.java 结束
清单 9-6
添加用户 Servlet 的代码
```

