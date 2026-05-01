# 8. 服务层

您的应用程序将通过服务层与 DAO 进行交互。

## 创建 DbUtilities 类

每次应用程序连接数据库时，都会执行相同的代码。通过创建一个包含可复用方法的类，您可以根据需要调用这些方法，从而提高应用程序的效率。创建一个名为 `DbUtilities` 的类。这将使您在需要读取或写入数据库时，能够简化对实体管理器的调用。该类将是静态的（无需实例化）。它将包含一个方法 `getFactory()`，该方法将返回 `persistence.xml` 中标识的 `EntityManagerFactory` 实例。请参见清单 8-1。

```
//DbUtil.java
package service;
import javax.persistence.EntityManager;
import javax.persistence.Persistence;
public class DbUtil {
public static EntityManager getEntityManager(String s) {
return Persistence.createEntityManagerFactory(s).createEntityManager();
}
}
//End of DbUtil.java
清单 8-1
DbUtilities 类的代码清单
```

## 创建 DbUser 类

`DbUser` 类包含用于操作用户的方法。`DbPost` 包含用于操作帖子的方法。这两个类非常相似，因此此处仅展示 `DbUser` 中的部分方法。



```
//DbUser.java
package service;
import javax.persistence.EntityManager;
import javax.persistence.EntityTransaction;
import javax.persistence.NoResultException;
import javax.persistence.TypedQuery;
import service.util.MD5Util;
import model.Bhuser;
/**
* @author djw
* DbUser 类包含用于操作 Bhuser 的辅助方法
*
*/
public class DbUser {
/**
* 从数据库中获取一个 Bhuser
* @param userID - 数据库中的主键。类型必须为 long
* @return Bhuser
*/
public static Bhuser getUser(long userID)
{
EntityManager em = DbUtil.getEntityManager("Bullhorn");
Bhuser user = em.find(Bhuser.class, userID);
return user;
}
public static void insert(Bhuser bhUser) {
EntityManager em = DbUtil.getEntityManager("Bullhorn");
EntityTransaction trans = em.getTransaction();
try {
trans.begin();
em.persist(bhUser);
trans.commit();
} catch (Exception e) {
e.printStackTrace();
trans.rollback();
} finally {
em.close();
}
}
/**
* 根据邮箱和尺寸获取 Gravatar URL
* 根据 Gravatar 的要求，邮箱将使用 MD5 哈希算法进行哈希处理，并作为 URL 的一部分返回
* URL 还将包含 s=xx 属性，用于请求特定尺寸的 Gravatar 头像。
* 参考：http://www.gravatar.com
* @param email - 你想要获取 Gravatar 头像的用户的邮箱
* @param size - 表示要返回的图像的像素高度。高度和宽度相同。
* @return - Gravatar URL。你可以在浏览器中测试它。
*/
public static String getGravatarURL(String email, Integer size){
StringBuilder url = new StringBuilder();
url.append("http://www.gravatar.com/avatar/");
url.append(MD5Util.md5Hex(email));
url.append("?s=" + size.toString());
return url.toString();
}
/**
* 更新 Bhuser 中的数据
* 向该方法传递一个已设置好所有值的 Bhuser，此方法将使用这些值更新数据库。
* 此方法实际上不返回任何内容，但会给你一种更新已完成的美好感觉。如果无法完成，它也不会告诉你。听起来未来需要添加一些功能。嗯。
* @param bhUser
*/
public static void update(Bhuser bhUser) {
EntityManager em = DbUtil.getEntityManager("Bullhorn");
EntityTransaction trans = em.getTransaction();
try {
trans.begin();
em.merge(bhUser);
trans.commit();
} catch (Exception e) {
System.out.println(e);
trans.rollback();
} finally {
em.close();
}
}
/**
* 从数据库中移除一个 Bhuser。
* 不确定你为什么要从数据库中删除一个 Bhuser，但这个方法可以为你做到。此方法不会显式删除用户的帖子，但很可能你已经设置了数据库的级联删除，这将会处理这个问题。不提供任何反馈。
* @param bhUser - 你永远不想再看到的 Bhuser
*/
public static void delete(Bhuser bhUser) {
EntityManager em = DbUtil.getEntityManager("Bullhorn");
EntityTransaction trans = em.getTransaction();
try {
trans.begin();
em.remove(em.merge(bhUser));
trans.commit();
} catch (Exception e) {
System.out.println(e);
trans.rollback();
} finally {
em.close();
}
}
/**
* 根据用户的邮箱地址获取用户。
* 当用户登录时你获得了邮箱，但你真正需要的是用户及其所有相关信息。此方法将找到与该邮箱匹配的用户。数据库应确保你不能有两个具有相同邮箱的用户。否则，结果将无法预料。
* @param email
* @return 具有该唯一邮箱地址的 Bhuser
*/
public static Bhuser getUserByEmail(String email)
{
EntityManager em = DbUtil.getEntityManager("Bullhorn");
String qString = "Select u from Bhuser u "
+ "where u.useremail=:useremail";
TypedQuery q = em.createQuery(qString, Bhuser.class);
q.setParameter("useremail", email);
Bhuser user = null;
try {
System.out.println("正在获取单个用户");
user = q.getSingleResult();
System.out.println(user.getUsername());
}catch (NoResultException e){
System.out.println(e);
}finally{
em.close();
}
return user;
}
/**
* 这个用户是否有效？此方法会为你提供答案。
* 检查数据库并统计具有此用户名和密码的用户数量。如果返回 0，则说明数据库中不存在该用户名或密码。如果返回 1，则说明找到了具有该用户名和密码的用户。如果返回 >1，则说明你需要修复数据库。
* @param userEmail 和 userPassword
* @return true 或 false，表示用户存在或不存在
*/
public static boolean isValidUser(String userEmail,String userPassword)
{
EntityManager em =
DbUtil.getEntityManager("Bullhorn");
String qString = "Select count(b.bhuserid) from Bhuser b "
+ "where b.useremail = :useremail and b.userpassword = :userpass";
TypedQuery q = em.createQuery(qString,Long.class);
boolean result = false;
q.setParameter("useremail", userEmail);
q.setParameter("userpass", userPassword);
try{
long userId = q.getSingleResult();
if (userId > 0)
{
result = true;
}
}catch (Exception e){
result = false;
}
finally{
em.close();
}
return result;
}
}
//DbUser.java 结束
```

## 创建 DbPost 类

```
//DbPost.java
package service;
import java.util.List;
import javax.persistence.EntityManager;
import javax.persistence.EntityTransaction;
import javax.persistence.TypedQuery;
import model.Bhpost;
public class DbPost {
public static void insert(Bhpost bhPost) {
EntityManager em = DbUtil.getEntityManager("Bullhorn");
EntityTransaction trans = em.getTransaction();
try {
trans.begin();
em.persist(bhPost);
trans.commit();
} catch (Exception e) {
System.out.println(e.getMessage());
trans.rollback();
} finally {
em.close();
}
}
public static void update(Bhpost bhPost) {
EntityManager em = DbUtil.getEntityManager("Bullhorn");
EntityTransaction trans = em.getTransaction();
try {
trans.begin();
em.merge(bhPost);
trans.commit();
} catch (Exception e) {
trans.rollback();
} finally {
em.close();
}
}
public static void delete(Bhpost bhPost) {
EntityManager em = DbUtil.getEntityManager("Bullhorn");
EntityTransaction trans = em.getTransaction();
try {
trans.begin();
em.remove(em.merge(bhPost));
trans.commit();
} catch (Exception e) {
System.out.println(e);
trans.rollback();
} finally {
em.close();
}
}
public static List bhPost (){
EntityManager em = DbUtil.getEntityManager("Bullhorn");
String qString = "select b from Bhpost b";
List posts = null;
try{
TypedQuery query = em.createQuery(qString,Bhpost.class);
posts = query.getResultList();
}catch (Exception e){
e.printStackTrace();
}
finally{
em.close();
}
return posts;
}
public static List postsofUser(long userid)
{
EntityManager em = DbUtil.getEntityManager("Bullhorn");
List userposts = null;
String qString = "select b from Bhpost b where b.bhuser.bhuserid = :userid";
try{
TypedQuery query = em.createQuery(qString,Bhpost.class);
query.setParameter("userid", userid);
userposts = query.getResultList();
}catch (Exception e){
e.printStackTrace();
}
finally{
em.close();
}
return userposts;
}
public static List postsofUser(String useremail)
{
EntityManager em = DbUtil.getEntityManager("Bullhorn");
List userposts = null;
String qString = "select b from Bhpost b "
+ "where b.bhuser.useremail = :useremail";
try{
TypedQuery query = em.createQuery(qString,Bhpost.class);
query.setParameter("useremail", useremail);
userposts = query.getResultList();
}catch (Exception e){
e.printStackTrace();
}
finally{
em.close();
}
return userposts;
}
public static List searchPosts (String search)
{
EntityManager em = DbUtil.getEntityManager("Bullhorn");
List searchposts = null;
String qString = "select b from Bhpost b "
+ "where b.posttext like :search";
try{
TypedQuery query = em.createQuery(qString,Bhpost.class);
query.setParameter("search", "%" + search + "%");
searchposts = query.getResultList();
}catch (Exception e){
e.printStackTrace();
}finally{
em.close();
}return searchposts;
}
}
//DbPost.java 结束
```



