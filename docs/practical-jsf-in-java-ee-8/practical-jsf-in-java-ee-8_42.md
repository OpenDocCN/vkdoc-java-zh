# 31. 定时任务

Michael Müller^(1 )

(1)德国，北莱茵-威斯特法伦州，布吕尔

如果用户没有激活他们的账户，我们就会得到一个*孤立*的注册（或账户）。我们必须定期清理数据库，对于周期性任务来说，还有什么比定时器更好的工具呢？这就是定时任务的用武之地。由于定时器在同一线程内可能对错误非常敏感，我们将异步执行此调度。



## 调度器

企业 Java Bean（EJB）中提供了定时器功能。Alumni 使用一个中心类来调用定时事件：即标注为 `@Singleton` 的 Scheduler 类。任何应由定时器调用的方法都将使用 `@Schedule` 进行注解。请参见清单 31-1。

###### 清单 31-1 调度器类

```
 1   @Singleton
 2   public class Scheduler {

 4     @Inject DatabaseCleaner _dbCleaner;

 6     @Schedule(hour = "*/1")
 7     private void cleanDatabase() {
 8         _dbCleaner.cleanAccountRequests();
 9     }
10   }
```

第 6 行显示了一个每小时执行一次的调度。服务器将创建一个相应的定时器。如果我们写成 `hour = "1"`，定时器将每天在 1 点钟触发一次。使用星号和斜杠后的数字，`*/n` 表示每 *n* 小时执行一次（你也可以使用分钟、秒等单位）。单独的星号表示“不关心”。因此，如果我们想每十秒调度一个任务，小时和分钟就“不关心”了。我们像这样注解方法：

```
@Schedule(hour = "*", minute = "*", second = "*/10")
```

DatabaseCleaner 类用于捆绑几个数据库清理任务的启动器。乍一看，这个类除了委托给注册服务并在出错时记录日志外，什么也不做。请参见清单 31-2。

###### 清单 31-2 DatabaseCleaner 类

```
 1   @Stateless
 2   public class DatabaseCleaner {

 4     @Inject private AccountService _registerService;

 6     public void cleanAccountRequests() {
 7       try {
 8         _registerService.deleteOldAccountRequests();
 9       } catch (Exception ex) {
10         Logger.getLogger(Scheduler.class.getName())
11                 .log(Level.SEVERE, null, ex.getMessage());
12       }
13     }

15   }
```

一旦创建了定时器，应用服务器就会确保它被触发。如果服务器宕机，这并不会阻止定时器：重启后，服务器会恢复所有定时器。在任何停机时间之后，服务器会尝试补上所有未执行的任务。

## 删除查询

使用 Java 持久化 API（JPA），人们会熟悉 CRUD（创建、读取、更新、删除）操作：`persist`、`find`、`merge` 和 `remove`。要删除单个实体，通常使用 `remove(entity)` 方法。使用这种方法，你首先必须加载实体，然后将其作为参数传递给 `remove` 方法。这仅在你出于其他原因之前已将实体加载到内存中时才适用。

但是，如果你只想删除一个实体，为什么还要加载它呢？我知道有些人因此不喜欢 JPA。然而，JPA 提供的功能远不止众所周知的 CRUD 操作。例如，还有 `executeUpdate()`，你可以将其用于其他类似 SQL 的操作。我们用它来删除所有超过一天期限的请求。我们需要计算这个日期。虽然新的 `LocalDate` 非常方便，但我们在这里不能使用它。我们需要老式的 `Date`。或者至少我们需要将 `LocalDate` 转换为该类型（关于 JPA 2.2 的好消息是，在定义实体时，你可以使用 Java 8 日期 API）。请参见清单 31-3。

###### 清单 31-3 AccountService 类（节选）

```
01     public void deleteOldAccountRequests() {
02       String jpql = "DELETE FROM Account a "
03               + "WHERE a._created < :date and a._status = :status";
04       Query query = getEntityManager().createQuery(jpql);
05       Date deleteBefore = new Date();
06       query.setParameter("date", getDateWithDayOffset(-1));
07       query.setParameter("status", AccountStatus.New);
08       query.executeUpdate();
09     }

11     public Date getDateWithDayOffset(int offset) {
12       return new Date(System.currentTimeMillis()+ offset*24*60*60*1000);
13     }
```

现在，如果我们启动应用程序，应用服务器将创建一个定时器；每小时都会调用清理操作。如果发生错误，错误将被记录。到目前为止，一切似乎都运行正常。

###### ![A435433_1_En_31_Figa_HTML.jpg](img/A435433_1_En_31_Figa_HTML.jpg) 引发一个错误

但让我们强制产生一个异常。我们将故意引发一个错误。

修改 jpql 语句：`WHERE a.createdXXX < :date`。请注意，追加三个 X 会使字段名称无效。将调用时间改为每 5 秒一次（你肯定不想等上几个小时）。

然后启动应用程序并观察服务器日志。

正如你可能预料的那样，服务器报告了一个错误：

```
Info:   Error during transaction processing
java.lang.IllegalArgumentException: An exception occurred while creating a query in EntityManager:
Exception Description: Problem compiling [DELETE FROM Account a WHERE a._createdXXX < :date and a._status = :status].
[28, 41] The state field path 'a._createdXXX' cannot be resolved to a valid type.
```

但在第二次尝试调用数据库清理器后，定时器停止了，服务器记录了以下信息：

```
Info: EJB5119:Expunging timer [...] after [2] failed deliveries
```

正如日志中所报告的，定时器停止了。即使我们捕获了错误，这种情况也会发生。这种看似奇怪的行为是 EJB 的设计使然。通常我们的代码是正确的，但想象一下数据库服务器意外宕机的情况。过了一段时间它又恢复并重新运行。我们希望我们的调度服务器能再次对数据库进行操作，但此时定时器可能已被清除。

我们如何解决这个问题？正如我们刚才展示的，捕获异常并不是一个选项。幸运的是，*确实*有一个简单的解决方案：如果异常发生在不同的线程中，定时器就不会被清除。

## 变为异步

如果我们想触发长时间运行的任务，最好能立即启动而无需等待结果。

最新版本的 Java EE 支持*异步方法调用*：该方法在不同的线程中被调用，控制权立即返回给调用者。你所要做的就是用适当的注解将定时器调用的方法声明为异步，如清单 31-4 所示。

###### 清单 31-4 异步方法

```
1   @Asynchronous
2   public void cleanAccountRequests() {
3     ...
4   }
```

尽管我们定义的删除任务非常短，但它从异步调用中获益：定时器将不再因为 jpql 语句中的错误而停止。除了异步运行之外，我们还将获得一个稳定的定时器。

## 总结

本章简要介绍了定时事件。Java EE 支持通过简单地向方法添加 `@Schedule` 注解来实现定时事件。在 Alumni 中的首次应用是一个使用删除查询的定期数据库清理过程。因此，在删除实体之前无需加载任何实体。定时器容易受到异常的影响，即使这些异常可能被捕获。使用异步方法调用有助于提高定时器的稳定性。

© Michael Müller 2018

Michael Müller, Practical JSF in Java EE 8 , `doi.org/10.1007/978-1-4842-3030-5_32`

