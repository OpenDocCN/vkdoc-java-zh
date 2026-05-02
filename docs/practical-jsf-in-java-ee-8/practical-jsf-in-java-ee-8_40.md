# 29. 数据门面

Michael Müller^(1 )

(1)德国，北莱茵-威斯特法伦州，布吕尔

在 Books 项目中，我们使用一个简单的服务来存储实体。每个服务都继承自一个抽象服务类，并提供给定实体的类。尽管这种方法相当简单，但它需要为每个实体创建一个具体的服务。

Alumni 是一个更大的应用程序，它使用了许多实体。有些实体是相互关联的，可以分组在一起。例如，在注册过程中，Alumni 会创建一个 Account 对象。系统会向用户发送一条消息，用户随后点击激活链接。激活完成后，账户即被激活。我们希望通一个类（称为*门面*）来访问 Account 以及相关的实体。

## 抽象数据服务

尽管 Alumni 使用一个门面来访问一个或多个相关实体，但我们仍需要多个门面来访问不同类型的数据。为了方便起见，所有这些门面都继承自同一个抽象服务。请参见清单 29-1。

###### 清单 29-1 用于访问实体的抽象服务类

```
 1   public abstract class AbstractService {

 3     @PersistenceContext(unitName = "AlumniPU")
 4     private EntityManager _em;    

 6     protected <T> T read(Object id, Class<T> entityClass) {
 7       return _em.find(entityClass, id);
 8     }

10    protected <T> T save(T entity) {
11      T merge = _em.merge(entity);
12      return merge;
13    }

15    protected void delete(Object entity) {
16      if (isAttached(entity)) {
17        _em.remove(entity);
18      } else {
19        _em.remove(_em.merge(entity));
20      }
21    }

23    protected <T> T find(Class<T> entityClass, Object id) {
24      return _em.find(entityClass, id);
25    }

27    protected <T> T findFresh(Class<T> entityClass, Object id) {
28      Map<String, Object> hints = new HashMap<>();
29      hints.put("javax.persistence.cache.retrieveMode", "BYPASS");
30      return _em.find(entityClass, id, hints);
31    }

33    protected <T> List<T> findAll(Class<T> entityClass) {
34      CriteriaQuery cq = _em.getCriteriaBuilder().createQuery();
35      cq.select(cq.from(entityClass));
36      return _em.createQuery(cq).getResultList();
37    }

39    protected <T> List<T> findRange(Class<T> entityClass, int[] range) {
40      return findRange(entityClass, range[0], range[1]);
41    }

43    protected <T> List<T> findRange(Class<T> entityClass, int from, int to) {
44      CriteriaQuery cq = _em.getCriteriaBuilder().createQuery();
45      cq.select(cq.from(entityClass));
46      javax.persistence.Query q = _em.createQuery(cq);
47      q.setMaxResults(to - from + 1);
48      q.setFirstResult(from);
49      return q.getResultList();
50    }

52    protected <T> int count(Class<T> entityClass) {
53      CriteriaQuery cq = _em.getCriteriaBuilder().createQuery();
54      Root<T> rt = cq.from(entityClass);
55      cq.select(_em.getCriteriaBuilder().count(rt));
56      javax.persistence.Query q = _em.createQuery(cq);
57      return ((Long) q.getSingleResult()).intValue();
58    }

60    protected boolean isAttached(Object entity) {
61      return _em.contains(entity);
62    }

64    protected void clearCache() {
65      _em.flush();
66      _em.getEntityManagerFactory().getCache().evictAll();
67    }

69  }
```

与之前 Books 项目中的方法相比，主要区别在于我们不再将实体类传递给构造函数。相反，对于某些访问操作，我们需要提供实体的适当类（第 6 行的类型变量 entityClass），以实现对特定类实体的通用访问。

## 具体门面

现在，我们可以创建一些方法，通过同一个门面来访问不同的实体。清单 29-2 中的摘录演示了这一点。

###### 清单 29-2 作为多个实体门面的 AccountingService（摘录）

```
01   @ApplicationScoped
02   @Transactional
03   public class AccountService extends AbstractService {

05     public Account saveAccount(Account account) {
06       return save(account);
07     }

09     public List<Account> findAllAccounts() {
10       return findAll(Account.class);
11     }

13     public Account findAccount(byte[] id) {
14       return find(Account.class, id);
15     }

17     public Account createAccount(Account account) {
18       if (emailExists(account.getEmail())) {
19        throw new IllegalArgumentException("createAccount, email exists: "
20                      + account.getEmail());
21       }
22       account.setLoginName(deriveUniqueLoginName(account));
23       account.setStatus(AccountStatus.New);
24       return save(account);
25     }

27     ...

29     public boolean activateAccount(String accessKey) {
30       try{
31         Account account = findAccountByAccessKey(accessKey);
32         account.setStatus(AccountStatus.Active);
33         save(account);
34         return true;
35       }catch(Exception ex){
36         LOGGER.log(Level.WARNING, ex.getMessage());
37         return false;
38       }
39     }

41     private Account findAccountByAccessKey(String accessKey) {
42       String jpql = "select a from Account a where a._accessKey = :accessKey";
43       TypedQuery<Account> query = getEntityManager().createQuery(jpql, Account.class);
44       query.setParameter("accessKey", HashUtils.hex2byte(accessKey));
45       try {
46         return query.getSingleResult();
47       } catch (Exception ex) {
48         throw new IllegalArgumentException("Unknown accessKey: " + accessKey);
49       }
50     }

52     public void deleteAccount(String id) {
53       String jpql = "DELETE FROM Account a WHERE a._id = :id";
54       Query query = getEntityManager().createQuery(jpql);
55       query.setParameter("id", HashUtils.hex2byte(id));
56       query.executeUpdate();
57       clearCache();
58     }

60   }
```

如您所见，这个具体门面要么简单地将任务委托给抽象门面，要么添加更复杂的功能，例如 `findAccountbyAccessKey`。虽然可以通过 `saveAccount` 方法（第 5 行）保存一个新实例，但第 17 行开始有一个专门用于创建账户的方法。该方法会检查电子邮件是否已存在，以确保每个电子邮件只能分配给一个账户。在账户创建过程中，登录名可能会被更新，以确保登录名的唯一性。最后但同样重要的是，账户状态被设置为“新建”。这表示一个未激活的账户。只有激活的账户才能登录。

从第 52 行开始，有一个删除方法。它可以根据 id 删除一个账户，而无需事先将其加载到内存中。

为简洁起见，清单 29-2 并未展示整个类。

## 账户实体

Account 实体收集注册表单中的所有信息，并添加一个创建日期。如果用户在该日期之后的规定期限内未激活其账户，我们将删除该账户。请参见清单 29-3。



###### 清单 29-3 实体账户

```
 01   @Entity
 02   @Table(name = "Account")
 03   public class Account implements Serializable {

05     private static final long serialVersionUID = 1L;
 06     private static final int KEY_LEN = 1024;
 07     private static final int ROUNDS = 100_021;

09     // <editor-fold defaultstate="collapsed" desc="属性 ID">
 10     @Id
 11     @Column(name = "id")
 12     private final byte[] _id = makeUuidAsBytes();

14     public String getId() {
 15       return HashUtils.byte2hex(_id);
 16     }
 17     // </editor-fold>

19     // <editor-fold defaultstate="collapsed" desc="属性 密钥">
 20     @Column(name = "accessKey")
 21     private final byte[] _accessKey = makeUuidAsBytes();

23     public String getAccessKey() {
 24       return HashUtils.byte2hex(_accessKey);
 25     }
 26     // </editor-fold>

28     // <editor-fold defaultstate="collapsed" desc="属性 状态">
 29     @Column(name = "status")
 30     private AccountStatus _status = AccountStatus.New;

32     public AccountStatus getStatus() {
 33       return _status;
 34     }

36     public void setStatus(AccountStatus status) {
 37       _status = status;
 38     }
 39     // </editor-fold>

41     // <editor-fold defaultstate="collapsed" desc="属性 登录名">
 42     @Column(name = "loginName")
 43     private String _loginName;

45     @Size(min = 1, max = 50)
 46     public String getLoginName() {
 47       return _loginName;
 48    }

50     public void setLoginName(String loginName) {
 51       _loginName = loginName;
 52     }
 53     // </editor-fold>

55     // <editor-fold defaultstate="collapsed" desc="属性 名">
 56     @Column(name = "firstName")
 57     private String _firstName = "";

59     @Size(min = 1, max = 50)
 60     public String getFirstName() {
 61       return _firstName;
 62     }

64     public void setFirstName(String firstName) {
 65       _firstName = firstName;
 66     }
 67     // </editor-fold>

69     // <editor-fold defaultstate="collapsed" desc="属性 姓">
 70     @Column(name = "lastName")
 71     private String _lastName;

73     @Size(min = 1, max = 50, message = "{validation.lastname.size}")
 74     public String getLastName() {
 75       return _lastName;
 76     }

78     public void setLastName(String lastName) {
 79       _lastName = lastName;
 80     }
 81     // </editor-fold>

83     // <editor-fold defaultstate="collapsed" desc="属性 电子邮件">
 84     @Column(name = "email")
 85     private String _email = "";

87     @Size(min = 6, max = 100)
 88     public String getEmail() {
 89       return _email;
 90     }

92     public void setEmail(String email) {
 93       _email = email;
 94     }
 95     // </editor-fold>

97     // <editor-fold defaultstate="collapsed" desc="属性 最后更改时间">
 98     @Temporal(javax.persistence.TemporalType.TIMESTAMP)
 99     @Column(name = "lastChanged")
100   private Date _lastChanged = new Date();

102   public Date getLastChanged() {
103     return _lastChanged;
104   }

106   public void setLastChanged(Date lastChanged) {
107     _lastChanged = lastChanged;
108   }

110   @PreUpdate
111   private void tagLastChanged() {
112     _lastChanged = new Date();
113   }
114   // </editor-fold>

116   // <editor-fold defaultstate="collapsed" desc="属性 创建时间">
117   @Temporal(javax.persistence.TemporalType.TIMESTAMP)
118   @Column(name = "created")
119   private Date _created = new Date();

121   public Date getCreated() {
122     return _created;
123   }

125   public void setCreated(Date created) {
126     _created = created;
127   }
128   // </editor-fold>

130   // <editor-fold defaultstate="collapsed" desc="属性 密码">
131   @Column(name = "password")
132   private byte[] _passwordHash;

134   public void setPassword(String password){
135     _passwordHash = obtainPasswordHash(password);
136   }

138   public boolean checkPassword(String password) {
139     return _status == AccountStatus.Active
140             && Arrays.equals(obtainPasswordHash(password), _passwordHash);
141   }

143   private byte[] obtainPasswordHash(String password) {
144    byte[] passwordHash = HashUtils.hashPassword(password, makeSalt(),
145               ROUNDS, KEY_LEN);
146     return passwordHash;
147   }

149   private byte[] makeSalt() {
150     byte[] salt = new byte[32];
151     System.arraycopy(_id, 0, salt, 0, 16);
152     System.arraycopy(_accessKey, 0, salt, 16, 16);
153     return salt;
154   }
155   // </editor-fold>

157 }
```

提醒一下：`editor-fold` 的注释是 NetBeans 特有的，用于折叠源代码。

Alumni 不使用顺序数字作为主键（如 Books 那样），而是使用转换为字节数组的 UUID。由于对于给定的账户，此 ID 永远不会更改，因此在声明时（第 12 行）就对其进行了赋值。第二个 UUID 用于创建访问密钥，该密钥用于激活账户。ID 和访问密钥都用作密码的盐值（有关密码加盐的更多信息，请参见第 28 章）。每个都由 16 个字节构成，因此盐值的总长度为 32 字节，这对于密码加密来说足够了。

使用两个时间戳来指示创建时间和最后更改时间。由于 `@PreUpdate` 注解，`lastChanged` 会在任何更新之前被标记。

第 131–154 行展示了密码处理。当传入密码时，会计算其哈希值并存储在相应的字段中。此哈希值将存储在数据库中。密码没有 getter 方法；而是使用 `checkPassword` 来验证密码（登录时提供的）是否与哈希值匹配。

## 总结

每次对数据库的访问都通过一个数据外观（data façade）来执行，该外观为一组方法提供接口，以访问一组相关数据。这个外观用于隐藏数据访问的细节。对于不同类型的数据，Alumni 提供了多个从抽象外观派生而来的外观。

本章还展示了使用外观存储、更新和删除数据的细节。密码一旦传递给实体就会被加密，并且永远不会以其原始值存储。

© Michael Müller 2018

Michael Müller, Practical JSF in Java EE 8 , `doi.org/10.1007/978-1-4842-3030-5_30`

