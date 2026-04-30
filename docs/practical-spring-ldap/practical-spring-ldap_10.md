# 10. 杂项

在本章中，你将学习

*   如何使用 Spring LDAP 进行身份验证

*   如何解析 LDIF 文件

*   LDAP 连接池


## 使用 Spring LDAP 进行身份验证

身份验证是针对 LDAP 服务器执行的常见操作。这通常涉及将用户名和密码与目录服务器中存储的信息进行比对以验证用户身份。

使用 Spring LDAP 实现身份验证的一种方法是通过`ContextSource`类的`getContext`方法。以下是`getContext`方法的 API：

```
DirContext getContext(String principal, String credentials) throws NamingException
```

`principal`参数是用户的完整限定 DN（Distinguished Name），`credentials`参数是用户的密码。该方法使用传入的信息对 LDAP 进行身份验证。身份验证成功时，方法返回一个表示用户条目的`DirContext`实例。身份验证失败则通过异常通知调用者。清单 10-1 定义了包含验证方法的接口。

```
package com.apress.book.ldap.repository;
public interface AuthenticationDao {
boolean authenticate(String userid, String password);
}
清单 10-1
包含 authenticate 方法的 DAO 接口
```

清单 10-2 展示了使用`getContext`技术为 Library 应用程序中的用户进行身份验证的 DAO 实现。

```
package com.apress.book.ldap.repository;
import javax.naming.directory.DirContext;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.ldap.NamingException;
import org.springframework.ldap.core.ContextSource;
import org.springframework.ldap.core.DistinguishedName;
import org.springframework.ldap.support.LdapUtils;
import org.springframework.stereotype.Repository;
@Repository("authenticationDao")
public class AuthenticationDaoImpl implements AuthenticationDao {
private static final Logger logger = LoggerFactory.getLogger(AuthenticationDaoImpl.class);
public static final String BASE_DN = "ou=patrons,dc=inflinx,dc=com";
private ContextSource contextSource;
public AuthenticationDaoImpl(@Autowired @Qualifier("contextSource") ContextSource contextSource) {
this.contextSource = contextSource;
}
@Override
public boolean authenticate(String userid, String password) {
DistinguishedName dn = new DistinguishedName(BASE_DN);
dn.add("uid", userid);
DirContext authenticatedContext = null;
try {
authenticatedContext = contextSource.getContext(dn.toString(), password);
return Boolean.TRUE;
} catch (NamingException ex) {
logger.error("{}: {}", ex.getClass(), ex.getMessage());
return Boolean.FALSE;
} finally {
LdapUtils.closeContext(authenticatedContext);
}
}
}
清单 10-2
身份验证方法的基本实现
```

`getContext`方法需要用户条目的完整限定 DN。因此，身份验证方法首先使用提供的 `"ou=patrons,dc=inflinx,dc=com"` 基础 DN 创建一个`DistinguishedName`实例。然后，将提供的`userid`附加到 DN 上以构建用户的完整限定 DN。身份验证方法随后调用`getContext`方法，传入用户 DN 的字符串表示和密码。身份验证成功时，方法仅返回`true`。请注意，在 finally 块中会关闭获取到的上下文。

清单 10-3 展示了一个 JUnit 测试，用于验证此 authenticate 方法的正常运行。

```
package com.apress.book.ldap.repository;
import org.junit.jupiter.api.DisplayName;
import com.apress.book.ldap.repository.AuthenticationDao;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit.jupiter.SpringExtension;
import static org.junit.jupiter.api.Assertions.assertFalse;
import static org.junit.jupiter.api.Assertions.assertTrue;
@ExtendWith(SpringExtension.class)
@ContextConfiguration("classpath:repositoryContext-test.xml")
@DisplayName("身份验证 DAO 测试用例")
class AuthenticationDaoTest {
@Autowired
@Qualifier("authenticationDao")
private AuthenticationDao authenticationDao;
@Test
@DisplayName("检查身份验证机制是否正常工作")
void should_authentication_works() {
boolean authResult = authenticationDao.authenticate("patron0", "password");
assertTrue(authResult);
authResult = authenticationDao.authenticate("patron0", "invalidPassword");
assertFalse(authResult);
}
}
清单 10-3
检查身份验证方法是否正常工作的测试
```

位于**src/test/resources**文件夹下的`repositoryContext-test.xml`文件，与清单 10-3 相关联，其内容如清单 10-4 所示。在此场景中，您正在使用已安装的 OpenDJ LDAP 服务器。

```

清单 10-4
配置结构
```

清单 10-4 中所示实现的唯一缺点是`getContext`方法需要用户条目的完整限定 DN。可能存在某些场景，客户端代码并不知道用户的完整限定 DN。在清单 10-2 中，您通过附加硬编码值来构建完整限定 DN。如果希望使用清单 10-2 的代码对图书馆员工进行身份验证，这种做法将失败。为解决此类问题，Spring LDAP 在`LdapTemplate`类中添加了多个`authenticate`方法的变体：

```
boolean authenticate(String base, String filter, String password)
```

此`authenticate`方法使用提供的 base DN 和 filter 参数对用户的 LDAP 条目进行搜索。如果找到条目，则提取用户的完整限定 DN。然后，将该 DN 和密码传递给`ContextSource`的`getContext`方法进行身份验证。这是一个两步流程，但无需预先提供完整限定 DN。清单 10-5 包含了修改后的身份验证实现。请注意，DAO 实现中的`authenticate`方法签名未发生变化。它仍然接受用户名和密码作为参数。然而，由于身份验证方法的抽象化，实现变得更加简单。实现传递了一个空的 base DN，因为您希望以`ContextSource`创建时使用的 base DN 为基准进行搜索。



```
package com.apress.book.ldap.repository;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.ldap.core.LdapTemplate;
import org.springframework.stereotype.Repository;
@Repository("authenticationDao2")
public class AuthenticationDaoImpl2 implements AuthenticationDao {
public static final String BASE_DN = "ou=patrons,dc=inflinx,dc=com";
private LdapTemplate ldapTemplate;
public AuthenticationDaoImpl2(@Autowired @Qualifier("ldapTemplate") LdapTemplate ldapTemplate) {
this.ldapTemplate = ldapTemplate;
}
@Override
public boolean authenticate(String userid, String password) {
return ldapTemplate.authenticate(BASE_DN, "(uid=" + userid + ")", password);
}
}
Listing 10-5
认证的第二种可能实现
```

```
package com.apress.book.ldap.repository;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit.jupiter.SpringExtension;
import static org.junit.jupiter.api.Assertions.assertFalse;
import static org.junit.jupiter.api.Assertions.assertTrue;
@ExtendWith(SpringExtension.class)
@ContextConfiguration("classpath:repositoryContext-test.xml")
@DisplayName("认证 Dao 2 测试用例")
class AuthenticationDao2Test {
@Autowired
@Qualifier("authenticationDao2")
private AuthenticationDao authenticationDao;
@Test
@DisplayName("检查认证机制是否正常工作")
void should_authentication_works() {
boolean authResult = authenticationDao.authenticate("patron0", "password");
assertTrue(authResult);
authResult = authenticationDao.authenticate("patron0", "invalidPassword");
assertFalse(authResult);
}
}
Listing 10-6
验证认证方法的测试
```

处理认证异常

`LdapTemplate` 中的先前认证方法仅告知您认证是否成功或失败。在某些情况下，您可能希望了解导致失败的实际异常。对于这些场景，`LdapTemplate` 提供了认证方法的重载版本。其中一个重载方法的 API 如下：

```
boolean authenticate(String base, String filter, String password, AuthenticationErrorCallback errorCallback);
```

在执行上述 `authenticate` 方法期间发生的任何异常都会传递给作为方法参数提供的 `AuthenticationErrorCallback` 实例。此收集的异常可以被记录或用于认证后的处理流程。清单 10-7 和 10-8 展示了 `AuthenticationErrorCallback` 接口及其简单实现。回调中的 `execute` 方法可以决定如何处理抛出的异常。在您的简单实现中，您只需存储该异常，并使其对 `LdapTemplate` 的搜索调用者可用。

```
package com.apress.book.ldap.exception;
import org.springframework.ldap.core.AuthenticationErrorCallback;
public class EmployeeAuthenticationErrorCallback implements AuthenticationErrorCallback {
private Exception authenticationException;
@Override
public void execute(Exception ex) {
this.authenticationException = ex;
}
public Exception getAuthenticationException() {
return authenticationException;
}
}
Listing 10-8
回调的一个可能定义
```

```
package org.springframework.ldap.core;
public interface AuthenticationErrorCallback {
void execute(Exception ex);
}
Listing 10-7
包含默认方法的接口
```

清单 10-9 展示了修改后的 `AuthenticationDao` 实现及其错误回调；在这里，您只需将失败的异常记录到控制台。

```
package com.apress.book.ldap.repository;
import com.apress.book.ldap.exception.EmployeeAuthenticationErrorCallback;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.ldap.core.LdapTemplate;
import org.springframework.stereotype.Repository;
@Repository("authenticationDao3")
public class AuthenticationDaoImpl3 implements AuthenticationDao {
private static final Logger logger = LoggerFactory.getLogger(AuthenticationDaoImpl3.class);
public static final String BASE_DN = "ou=patrons,dc=inflinx,dc=com";
private LdapTemplate ldapTemplate;
public AuthenticationDaoImpl3(@Autowired @Qualifier("ldapTemplate") LdapTemplate ldapTemplate) {
this.ldapTemplate = ldapTemplate;
}
@Override
public boolean authenticate(String userid, String password) {
EmployeeAuthenticationErrorCallback errorCallback = new EmployeeAuthenticationErrorCallback();
boolean isAuthenticated = ldapTemplate.authenticate( BASE_DN, "(uid=" + userid + ")", password, errorCallback);
if (!isAuthenticated) {
logger.info(errorCallback.getAuthenticationException().getMessage());
}
return isAuthenticated;
}
}
Listing 10-9
认证的第三种可能实现
```

清单 10-10 展示了用于检查此认证方法如何工作的 JUnit 测试。

```
package com.apress.book.ldap.repository;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit.jupiter.SpringExtension;
import static org.junit.jupiter.api.Assertions.assertFalse;
@ExtendWith(SpringExtension.class)
@ContextConfiguration("classpath:repositoryContext-test.xml")
@DisplayName("认证 Dao 3 测试用例")
class AuthenticationDao3Test {
@Autowired
@Qualifier("authenticationDao3")
private AuthenticationDao authenticationDao;
@Test
@DisplayName("检查认证机制是否正常工作")
void should_authentication_works() {
boolean authResult = authenticationDao.authenticate("patron0", "invalidPassword");
assertFalse(authResult);
}
}
Listing 10-10
验证认证方法的测试
```

运行清单 10-10 中的 JUnit 测试时，您应该在控制台看到以下错误信息：

```
10:29:36.008 [main] INFO  c.a.b.l.r.AuthenticationDaoImpl3 - [LDAP: 错误代码 49 - 无法以用户 'uid=patron0,ou=patrons,dc=inflinx,dc=com' 绑定，因为提供的密码不正确。]
```

测试返回成功，因为检查了输入错误密码时的情况。如果您想验证相反的情况，请更改密码并使用 ***assertTrue*** 方法。

还有其他替代方法，但这些选项提供了认证用户的最基本机制。另一种实现相同功能的可能性是 Spring Security，^(⁹¹) 它具有与 LDAP 结合使用的机制，类似于本章中您阅读的示例。不幸的是，Spring Security 是一个复杂主题，仅用几句话难以解释清楚，因此本本书的范围之外。



## 解析 LDIF 数据

LDAP 数据交换格式是一种基于标准的数据交换格式，用于以平面文件格式表示 LDAP 目录数据。第 1 章详细讨论了 LDIF。作为 LDAP 开发人员或管理员，有时可能需要解析 LDIF 文件并执行诸如批量目录加载之类的操作。针对此类场景，Spring LDAP 在 `org.springframework.ldap.ldif` 包及其子包中引入了一组类，使得读取和解析 LDIF 文件变得简单。

`org.springframework.ldap.ldif.parser` 包的核心是 `Parser` 接口及其默认实现 `LdifParser`。`LdifParser` 负责从 LDIF 文件中读取各行并将其转换为 Java 对象。这种对象表示是通过两个新添加的类实现的，即 `LdapAttribute` 和 `LdapAttributes`。

清单 10-11 中的代码使用 `LdifParser` 读取并打印 LDIF 文件中的记录总数。实现时，首先创建一个 `LdifParser` 实例，并传入要解析的文件。在使用解析器之前，需要先打开它。然后，使用解析器的迭代器式接口来读取和计数各个记录。

```
package com.apress.book.ldap.parser;
import java.io.File;
import java.io.IOException;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.core.io.ClassPathResource;
import org.springframework.ldap.core.LdapAttributes;
import org.springframework.ldap.ldif.parser.LdifParser;
public class SimpleLdifParser {
private static final Logger logger = LoggerFactory.getLogger(SimpleLdifParser.class);
public void parse(File file) throws IOException {
LdifParser parser = new LdifParser(file);
parser.open();
int count = 0;
while (parser.hasMoreRecords()) {
LdapAttributes attributes = parser.getRecord();
count++;
}
parser.close();
logger.info(String.valueOf(count));
}
public static void main(String[] args) throws IOException {
SimpleLdifParser parser = new SimpleLdifParser();
parser.parse(new ClassPathResource("patrons.ldif").getFile());
}
}
清单 10-11
LDIF 解析器示例
```

在运行上述类之前，请确保 `patrons.ldif` 文件位于 **src/java/resources** 文件夹中。使用第 1 章代码附带的 `patrons.ldif` 文件运行该类后，您应该会看到计数 `103` 打印到控制台，如下所示：

```
14:37:04.933 [main] DEBUG o.s.ldap.ldif.parser.LdifParser - record parsed:
dn: ou=patrons,dc=inflinx,dc=com
ou: patrons
objectClass: top
objectClass: organizationalunit
14:37:04.935 [main] DEBUG o.s.ldap.ldif.parser.LdifParser - record parsed:
dn: uid=patron0,ou=patrons,dc=inflinx,dc=com
mobile: +1 189 955 5012
initials: ARA
givenName: Aaccf
street: 08904 Sixth Street
telephoneNumber: +1 688 926 3146
sn: Amar
userPassword: password
l: Fairbanks
mail: patron0@inflinx.com
objectClass: top
objectClass: person
objectClass: organizationalperson
objectClass: inetorgperson
uid: patron0
postalAddress: Aaccf Amar$08904 Sixth Street$Fairbanks, IA  10928
homePhone: +1 092 210 9726
postalCode: 10928
cn: Aaccf Amar
st: IA
......................
......................
......................
```

`LdifParser` 的解析实现依赖于三个支持性策略定义：分隔符策略、属性验证策略和记录规范策略。

*   分隔符策略提供了文件中 LDIF 记录的分隔规则，并在 RFC 2849^(⁹²) 中定义。它通过 `org.springframework.ldap.ldif.support.SeparatorPolicy` 类实现。

*   属性验证策略，顾名思义，确保在解析之前 LDIF 文件中的所有属性都结构正确。它通过 `AttributeValidationPolicy` 接口和 `DefaultAttributeValidationPolicy` 类实现。这两个位于 `org.springframework.ldap.ldif.support` 包中。`DefaultAttributeValidationPolicy` 使用正则表达式根据 RFC 2849 验证属性格式。

*   记录规范策略验证每条 LDIF 记录必须遵守的规则。Spring LDAP 为此策略提供了 `Specification` 接口和两个实现：`org.springframework.ldap.schema.DefaultSchemaSpecification` 和 `org.springframework.ldap.schema.BasicSchemaSpecification`。`DefaultSchemaSpecification` 的实现为空，不验证记录。`BasicSchemaSpecification` 可以执行基本检查，例如每个 LAP 条目必须存在 `objectClass`。在大多数情况下，`BasicSchemaSpecification` 就足够了。

修改后的 `parse` 方法实现和三个策略定义如清单 10-12 所示。

```
package com.apress.book.ldap.parser;
import java.io.File;
import java.io.IOException;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.core.io.ClassPathResource;
import org.springframework.ldap.core.LdapAttributes;
import org.springframework.ldap.ldif.parser.LdifParser;
import org.springframework.ldap.ldif.support.DefaultAttributeValidationPolicy;
import org.springframework.ldap.schema.BasicSchemaSpecification;
public class SimpleLdifParser2 {
private static final Logger logger = LoggerFactory.getLogger(SimpleLdifParser2.class);
public void parse(File file) throws IOException {
LdifParser parser = new LdifParser(file);
parser.setAttributeValidationPolicy(new DefaultAttributeValidationPolicy());
parser.setRecordSpecification(new BasicSchemaSpecification());
parser.open();
int count = 0;
while (parser.hasMoreRecords()) {
LdapAttributes attributes = parser.getRecord();
count++;
}
parser.close();
logger.info(String.valueOf(count));
}
public static void main(String[] args) throws IOException {
SimpleLdifParser2 parser = new SimpleLdifParser2();
parser.parse(new ClassPathResource("patrons.ldif").getFile());
}
}
清单 10-12
使用验证器的 LDIF 解析器示例
```

运行清单 10-12 中的方法后，您应该会看到计数 `103` 打印到控制台，如下所示：

```
14:37:04.933 [main] DEBUG o.s.ldap.ldif.parser.LdifParser - record parsed:
dn: ou=patrons,dc=inflinx,dc=com
ou: patrons
objectClass: top
objectClass: organizationalunit
14:37:04.935 [main] DEBUG o.s.ldap.ldif.parser.LdifParser - record parsed:
dn: uid=patron0,ou=patrons,dc=inflinx,dc=com
mobile: +1 189 955 5012
initials: ARA
givenName: Aaccf
street: 08904 Sixth Street
telephoneNumber: +1 688 926 3146
sn: Amar
userPassword: password
l: Fairbanks
mail: patron0@inflinx.com
objectClass: top
objectClass: person
objectClass: organizationalperson
objectClass: inetorgperson
uid: patron0
postalAddress: Aaccf Amar$08904 Sixth Street$Fairbanks, IA  10928
homePhone: +1 092 210 9726
postalCode: 10928
cn: Aaccf Amar
st: IA
......................
......................
......................
```



## LDAP 连接池

LDAP 连接池是一种技术，它允许重复使用与 LDAP 目录的连接，而不是在每次请求连接时都创建新连接。如果没有连接池，每次向 LDAP 目录发起请求都会创建一个新连接，并在不再需要该连接时将其释放。创建新连接会消耗大量资源，这种开销会对性能产生不利影响。使用连接池后，连接在创建后会被存储在池中，并回收用于后续的客户端请求。

池中的连接在任何时刻都可能处于以下三种状态之一：

*   *使用中*：连接已打开且当前正在使用。
*   *空闲*：连接已打开并可重复使用。
*   *已关闭*：连接不再可用。

图 10-1 展示了在任意时刻对连接可能执行的操作。

![](img/319409_2_En_10_Fig1_HTML.jpg)

连接池状态的块状流程图。通过释放和分配给请求，“使用中”块与“空闲”块相互连接。“空闲”块映射到“已关闭”块。

图 10-1

连接池状态

## 内置连接池

JNDI 通过 `"com.sun.jndi.ldap.connect.pool"` 环境属性为连接池提供基本支持。创建目录上下文的应用程序可以将此属性设置为 `true`，以指示需要开启连接池。清单 10-13 展示了使用池化支持的纯 JNDI 代码。

```
// 设置创建初始上下文的环境
Hashtable env = new Hashtable();
env.put(Context.INITIAL_CONTEXT_FACTORY, "com.sun.jndi.ldap.LdapCtxFactory");
env.put(Context.PROVIDER_URL, "ldap://localhost:11389");
// 启用连接池
env.put("com.sun.jndi.ldap.connect.pool", "true");
// 创建一个初始上下文
(从池中获取连接) DirContext ctx = new InitialDirContext(env);
// 使用 ctx 做一些有用的事情
// 完成后关闭上下文
ctx.close(); // 将连接归还到池中
清单 10-13
带池化的纯 JNDI 代码
```

默认情况下，Spring LDAP 上下文的 `"com.sun.jndi.ldap.connect.pool"` 属性设置为 `false`。可以通过在配置文件中将 `LdapContextSource` 的 `pooled` 属性设置为 `true` 来开启原生连接池。以下代码展示了配置更改：

尽管原生 LDAP 连接池很简单，但它确实存在一些缺点。连接池是按 Java 运行时环境维护的。无法在单个 JVM 内维护多个连接池。此外，也无法控制连接池的属性，例如任何时刻要维护的连接数或空闲连接时间。同样不可能提供任何自定义连接验证来确保池化连接仍然有效。

## Spring LDAP 连接池

为了解决原生 JNDI 池化的缺点，Spring LDAP 为 LDAP 连接提供了一个自定义池化库。Spring LDAP 池化库维护其自己的一组特定于每个应用程序的 LDAP 连接。

注意

Spring LDAP 利用 Jakarta Commons Pool^(⁹³) 库作为其底层池化实现。

首先，你需要在 `pom.xml` 文件中添加与池相关的依赖项，该依赖项默认不包含在 Spring LDAP 中：

```
commons-pool
commons-pool
1.6

```

Spring LDAP 池化的核心是 `org.springframework.ldap.pool.factory.PoolingContextSource`，它是一个专门的 `ContextSource` 实现，负责池化 `DirContext` 实例。要使用连接池，首先要配置一个 Spring LDAP 上下文源：

请注意，你将上下文源的 `pooled` 属性设置为 `false`。这将允许 `LdapContextSource` 在需要时创建全新的连接。此外，`ContextSource` 的 `id` 现在设置为 `contextSourceTarget`，而不是通常使用的 `contextSource`。下一步是创建一个 `PoolingContextSource`，如下所示：

`PoolingContextSource` 包装了你之前配置的 `contextSourceTarget`。这是必需的，因为 `PoolingContextSource` 将创建 `DirContext` 的任务委托给 `contextSourceTarget`。另外，请注意你为此 bean 实例使用了 id `contextSource`。这允许你在 `LdapTemplate` 中使用 `PoolingContextSource` 实例时，将配置更改保持在最低限度，如下所示：

`PoolingContextSource` 提供了多种选项，可用于微调连接池。表 10-1 列出了一些重要的配置属性。

表 10-1

PoolingContextSource 配置属性

| 属性 | 描述 | 默认值 |
| --- | --- | --- |
| `testOnBorrow` | 设置为 true 时，在从池中借用 `DirContext` 之前会对其进行验证。如果 `DirContext` 验证失败，则会将其从池中移除，并再次尝试借用另一个 `DirContext`。此测试可能会在处理借用请求时增加少量延迟。 | False |
| `testOnReturn` | 设置为 true 时，此属性表示 `DirContext` 在返回到池之前将进行验证。 | False |
| `testWhileIdle` | 设置为 true 时，此属性表示池中的空闲 `DirContext` 实例应按指定频率进行验证。验证失败的对象将从池中丢弃。 | False |
| `timeBetweenEvictionRunsMillis` | 此属性表示运行空闲上下文测试之间的休眠时间（以毫秒为单位）。负数表示永远不会运行空闲测试。 | -1 |
| `whenExhaustedAction` | 指定池耗尽时要采取的操作。可能的选项有 `WHEN_EXHAUSTED_FAIL (0)`、`WHEN_EXHAUSTED_BLOCK (1)` 和 `WHEN_EXHAUSTED_GROW (2)`。 | 1 |
| `maxTotal` | 此池可以包含的最大活动连接数。非正整数表示无限制。 | -1 |
| `maxIdle` | 池中每种类型（读取、读写）可以空闲的最大连接数。 | 8 |
| `maxWait` | 池在抛出异常之前等待连接返回到池的最大毫秒数。负数表示无限期等待。 | -1 |

完成所有基本配置后，让我们在文件夹 **src/test/resources** 中创建另一个名为 `repositoryContext-test-1.xml` 的文件，其内容如清单 10-14 所示。

```

清单 10-14
池的声明
```

请检查清单 10-14，池的配置与之前的清单略有不同，因为你可以使用不同的方式来实现，但结果是相同的。

请注意，该文件不能用于身份验证目的，因为 `ContextSource` 上的用户执行绑定，这是通过使用指定的专有名称和密码创建新连接来完成的。



## 池验证

Spring LDAP 使得验证池化连接变得简单。这种验证确保在从池中借用 `DirContext` 实例之前，它们已正确配置并连接到 LDAP 服务器。在上下文被归还到池中或处于空闲状态时，也会执行相同的验证。

`PoolingContextSource` 将验证委托给 `org.springframework.ldap.pool.validation.DirContextValidator` 接口的具体实现。在清单 10-15 中，你可以看到 `DirContextValidator` 只有一个方法：`validateDirContext`。第一个参数 `contextType` 指示待验证的上下文是只读上下文还是读写上下文。第二个参数是需要验证的实际上下文。

```
package org.springframework.ldap.pool.validation;
import javax.naming.directory.DirContext;
import org.springframework.ldap.core.ContextSource;
import org.springframework.ldap.pool.DirContextType;
public interface DirContextValidator {
boolean validateDirContext(DirContextType contextType, DirContext dirContext);
}
清单 10-15
上下文验证器的声明
```

开箱即用，Spring LDAP 提供了一个命名恰当的 `DirContextValidator` 默认实现，名为 `org.springframework.ldap.pool.validation.DefaultDirContextValidator`。该实现简单地使用上下文进行搜索，并验证返回的 `javax.naming.NamingEnumeration`。如果 `NamingEnumeration` 不包含任何结果，或者抛出了异常，则上下文验证失败，并将从池中移除。需要更复杂验证的应用程序可以创建 `DirContextValidator` 接口的新实现。

配置池验证如清单 10-14 所示。首先，创建一个类型为 `DefaultDirContextValidator` 的 `dirContextValidator` bean。然后，修改 `contextSource` bean 的声明以包含 `dirContextValidator` bean。在清单 10-16 中，你添加了 `testOnBorrow` 和 `testWhileIdle` 属性。

```

清单 10-16
上下文验证器的修改
```

另一种实现方式如清单 10-14 所示，但需要对 **<ldap:pooling>** 标签稍作修改，如清单 10-16 所示。

## 总结

至此，我们的旅程已接近尾声。通过本书，你已经学习了 Spring LDAP 的关键特性。掌握了这些知识，你应该能够开始开发基于 Spring LDAP 的应用程序了。

最后，撰写本书并与你分享我的见解是一件乐事。祝你一切顺利。编码愉快！



脚注 1   2   3  索引 A AbstractFallbackRequestAndResponseControlDirContextProcessor AbstractFilter AbstractRequestControlDirContextProcessor AbstractRequestDirContextProcessor 验收测试 ACID 属性 ADD_ATTRIBUTE 代码 ADD REQ 命令 afterPropertiesSet AndFilter Apache Directory Studio 创建连接 安装 LDAP 连接 网络参数 VNC 客户端 欢迎页面 ApacheDS Apache Maven 原型 声明式依赖管理 定义 插件 标准化目录结构 工具 支持 applicationContext.xml 文件 原型 命令行 目录结构 组 ID 本地仓库 日志 Maven 安装命令 插件 pom.xml 文件 practical-ldap-archetype practical-ldap-empty-archetype practical-spring-ldap/code 目录 值 面向切面编程 (AOP) assertTrue 方法 @Attribute 注解 AttributesMapper 实现 Authenticate 方法 认证 基本实现 配置 DAO 接口 DN JUnit 测试 操作 Spring LDAP 工作方式 AuthenticationDao 实现 自动化集成测试 @Autowired 注解 B Base 参数 BasicAttributes 类 BinaryLogicalFilter C ClassPathXmlApplicationContext 客户端/服务器交互 CompareFilter 补偿事务 连接池 使用 LDAP 认证信息的连接 com.sun.jndi.ldap.LdapCtxFactory 提供者 Context 接口 创建 DirContext 接口 异常 初始上下文 INITIAL_CONTEXT_FACTORY 常量 javax.naming 包 LDAP 服务器 SECURITY_AUTHENTICATION 属性 使用凭据 ContextConfiguration contextSourceTarget Create 方法 createRequestControl 方法 创建测试 领域类 动态方法 基于嵌入式服务器的库 模拟 模板 操作 Testcontainers D 数据访问对象 (DAO) DataSourceTransactionManager 声明式事务 AOP 演示 管理 DefaultConverterManagerImpl 类 DefaultDirObjectFactory DefaultTempEntryRenamingStrategy Delete 方法 uid 删除操作 DELETE REQ 命令 DifferentSubtreeTempEntryRenamingStrategy DirContext DirContextAdapter 类 DirContext.OBJECT_FACTORIES 属性 DirContextOperations DirContextProcessor DirContextValidator 目录信息树 (DIT) DirectoryString 语法 目录后缀 命名约定 DirObjectFactory 专有名称 (DN) Docker doMapFromContext 方法 DSA 特定条目 E EasyMock Eclipse 基于嵌入式服务器的测试 基本配置 配置 依赖 嵌入式目录 LDAP 服务器 修改 pom 文件 简单测试 Spring LDAP EmployeeContextMapper Employee 领域对象 Employee DAO create 方法 EmployeeDao 接口 finder 方法 实现 employee.ldif 文件 Encode 方法 @Entry Entry 属性 EqualsFilter 异常 AuthenticationDao 实现 JUnit 测试 F 过滤器 AndFilter Encode 方法 EqualsFilter 转义值 Filter API 接口 GreaterThanOrEqualsFilter HardcodedFilter 接口层次结构 LessThanOrEqualsFilter LikeFilter NotFilter NotPresentFilters OrFilter 参数 PresentFilters 特殊字符 WhitespaceWildcardsFilter find 方法 findAll 方法 Finder 方法 功能模型 G getContext 方法 getLdapTemplate 方法 getObjectInstance 方法 getTransaction 方法 GitHub 仓库 givenName 属性 全局事务 GreaterThanOrEqualsFilter H HardcodedFilter Hibernate I IBM Tivoli Directory Server @Id 注解 信息模型 初始上下文 InitialDirContext InitialLdapContext 类 集成测试 IntelliJ 互联网工程任务组 (IETF) J, K 基于 Java 的 LDAP 应用程序 Java JDK Java LDAP 控制类层次结构 Java 命名和目录接口 (JNDI) 访问系统 架构 创建新条目 缺点 LDAP 参见 LDAP 使用 JNDI 操作 工作检查 包 删除条目 搜索条目 标准化编程接口 标准 JDK 发行版 更新条目 javax.naming.directory.Attributes 接口 javax.naming.directory.ModificationItem 类 javax.naming.directory 包 javax.naming.directory.SearchResult javax.naming.event 包 javax.naming.ldap.Control 接口 javax.naming.ldap 包 javax.naming.ldap.SortControl javax.naming.ldap.spi 包 javax.naming 包 javax.naming.spi 包 JMock JNDI 和控制 算法 javax.naming.ldap 包 JndiLdapDaoImpl 类 JNDI 对象工厂 流程 getObjectInstance 方法 LDAP PatronObjectFactory JNDI 资源 JNDI 的 javax.naming.directory.BasicAttribute 类 JUnit 注解 条件 检查 约定 <被测类>Test 依赖 执行 方法 测试用例类 测试方法 4.x 5.x L LastNameMapper 类 LDAP 代码测试 库 JUnit Mockito testcontainers LdapContext 接口 LdapContextSource LDAP 控制 组件 删除操作 操作 服务器 服务器控制交互 规范 LDAP 数据交换格式 (LDIF) LdapOperations LdapOperationsClient LdapQueryBuilder LDAP 搜索条件 base 参数 filter 参数 LDAP 注入 可选参数 参数 scope 参数 LDAP 服务器 base DN 配置 docker-compose LDIF 导入 OpenDJ 设置 测试数据 拓扑 欢迎页面 LdapTemplate delete 方法 findByDn 方法 操作 添加操作 删除操作 修改操作 LDAP 使用 JNDI 关闭资源 连接 操作 LDAP V3 服务器 LdifParser LessThanOrEqualsFilter 轻量级目录访问协议 (LDAP) 连接池 目录 目录模式 目录服务器 目录 *vs.* 数据库 条目 异常 Explorer 功能模型 历史 信息模型 注入 LDIF 模型 修改类型 命名模型 对象类 协议 示例应用程序 安全模型 用户管理器 供应商 X.500 标准 LikeFilter 本地事务 日志 M Maven Maven 生成的项目结构 模拟模板 IDE LdapTemplate Patron 对象 测试类 Mockito 注解 类创建 依赖 基于 Java 的模拟框架 MathService 类 方法 简单类，数学运算 简单测试 Mock 测试 EmailService 对象 开源框架 单元测试 UserAccountService ModificationItem modifyAttributes 方法 MODIFYDN REQ 命令 多值属性 多值 RDN mvn 命令 N NameNotFoundException NamingException 命名模型 属性 反斜杠 (“\” ASCII 92) 定义 DIT RDN 推荐技术 后缀 NetBeans 非关系型数据库 NotFilter NotPresentFilters O objectClass 条目 对象类 属性 常见 LDAP 类 inetOrgPerson 类 继承 在 Java 中 LDAP 条目 LDAP 实现 RFC top 对象-目录映射 (ODM) 创建自定义转换器 转换器管理器 配置 DefaultConverterManagerImpl 类 修改，Patron 类 Patron 类 PhoneNumber 类 PhoneNumber 到 String String 到 PhoneNumber 测试用例 依赖 元数据 对象 *vs.* 目录模型 OdmManager OdmManagerImpl org.springframework.ldap.odm 包 ORM 之前的 Spring LDAP ODM 核心类 服务类 对象工厂 DAO 实现 JNDI 和 Spring 面向对象语言 面向对象 (OO) 技术 面向对象编程 (OOP) 对象关系映射 (ORM) OdmManager OdmManagerImpl OpenDJ OpenDJ ldapsearch 命令 OpenDJ LDAP 服务器 OpenJDK 操作属性 可选参数 OracleJDK OrFilter 组织目录 org.springframework.ldap.core.AttributesMapper org.springframework.ldap.core.ContextSource 接口 抽象 org.springframework.ldap.core.LdapTemplate 类 org.springframework.ldap.core.support.LdapContextSource 接口 抽象 org.springframework.ldap.odm.annotations 包 @org.springframework.ldap.odm.annotations.Id P Page 类 PagedResultsControl PagedResultsDirContextProcessor PagedResultsResponseControl 分页搜索控制 DirContextProcessor LDAP 客户端和服务器交互 LDAP 客户端 LastNameMapper 类 page 类 PagedResultsControl PagedResultsDirContextProcessor PagedResultsResponseControl 分页的 patrons 分页类 分页 纯 JNDI API 测试 Pagination 类 Patron 类 Patron 实体类 Patron Java 类 PatronContextMapper PatronDao 接口 PatronDaoImpl 的 delete 方法 PatronObjectFactory 类 PatronService 接口 PatronServiceImpl 方法 PatronServiceImpl 的 delete 方法 Patron 服务接口 patrons.ldif 文件 PersonRepository 接口 PersonRepositoryImpl 类 PhoneNumber 类 PlatformTransactionManager 插件 pom.xml 文件 PoolingContextSource 池化验证 postProcess 方法 PowerMock Practical-ldap-archetype Practical-ldap-empty-archetype preProcess 方法 PresentFilters 编程式事务 Q QueryBuilder 方法 QueryBuilder 参数 属性 base 类 countLimit 搜索过滤器 searchScope 测试用例 timeLimit 查询操作 R RDBMS 特性 Read 方法 关系型数据库 相对专有名称 (RDN) REMOVE_ATTRIBUTE 代码 REPLACE_ATTRIBUTE 代码 repositoryContext.xml 文件 请求评论 (RFC) REQUIRES_NEW 传播 RFC 1487 RFC 2251 RFC 2829 RFC 4519 根 DSA 特定条目 (DSE) S Scope 参数 searchAndPrintResults 方法 searchAndShowResults 方法 SearchControls 类 搜索过滤器 搜索过滤器操作符 Search 方法 搜索操作 SearchResult 条目 搜索范围 安全模型 setRequestControls 方法 SimpleLdapTemplate 单值属性 sortByLocation 方法 排序控制 应用上下文配置 JNDI API LDAP 服务器 输出 Spring LDAP 测试 SortControlDirContextProcessor sortKeys SortMultipleControlDirContextProcessor Spring ApplicationContext 基于 Spring 的企业应用 Spring Boot Test Spring Data Spring Framework Spring LDAP ApplicationContext 原型 参见原型 架构和控制 分发 模块 文档 过滤器 参见过滤器 Hello World IntelliJ jar 文件 基于 Java 的企业应用 LDAP 编程，Java LDAP 服务器操作 Maven 动机 池化 打包 源代码 Spring-Powered Search Client 事务支持 Spring-Powered Search Client StringBuffer 支持的控制类 OpenDJ 安装 OpenDJ ldapsearch 命令 输出 Root DSE 条目 系统测试 T telephoneNumber 语法 Testcontainers 注解 命令 mvn test 声明 定义 依赖 Docker 执行流程 模块 多个测试框架 支持 @Testcontainers 测试 集成测试 模拟测试 软件开发过程 单元测试 使用 Testcontainers DockerComposeContainer repositoryContext-testcontainers.xml Docker-compose 文件 嵌入式 LDAP LDAP 示例测试 ToPhoneNumberConverter TopLink @Transactional 注解 TransactionDefinition 接口 事务 原子性 提交 阶段 补偿 一致性 声明式 持久性 隔离 本地 *vs.* 全局 准备 阶段 编程式 回滚 Spring Framework 支持 @Transient 注解 传输层安全 (TLS) 两阶段提交协议 U unbind 方法 UnboundID 单元测试 优势 特性 数据库 代码 定义 库 OOP 更新方法 DirContextAdapter 测试 更新操作 V Visual Studio W, X, Y, Z WhitespaceWildcardsFilter WORM 原则 脚注 1   2   3   4   5   6   7   8   9   10   11   12   13   14   15   16   17   18   19   20   21   22   23   24   25   26   27   28   29   30   31   32   33   34   35   36   37   38   39   40   41   42
