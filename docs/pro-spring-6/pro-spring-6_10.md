# 10. 使用 SQL 与 NoSQL 数据库的 Spring Data

现在你已经了解了数据访问的多个方面，例如连接数据库、使用 JDBC 执行原生查询、将表映射到实体类以便在 Java 代码中将数据库记录视为对象、使用 Hibernate 会话和 `EntityManagerFactory` 创建用于数据管理的仓库类，以及在同一个 Spring 管理的事务中执行多个数据库操作。接下来，我们将向你展示如何避免编写所有这些代码，并让 Spring 使用 Spring Data 为你完成工作。

Spring Data 项目是 Spring Data 总项目下的一个子项目^(⁸⁴)。Spring Data 项目的主要目标是为简化使用各种数据源的应用程序开发提供额外功能。Spring Data 项目包含多个子项目，用于与 SQL 和 NoSQL 数据库交互，既有经典版本也有响应式版本。它提供了强大的仓库和自定义对象映射抽象、基于配置的仓库查询生成、对透明审计的支持、扩展仓库代码的可能性，以及与 Spring MVC 控制器的轻松集成。

Spring Data 提供了大量旨在简化数据访问的功能，本章将重点介绍几个方面，而不会深入探讨细节，因为那会使本书的篇幅增加一倍。

在本章中，我们将讨论以下内容：

*   *介绍 Spring Data Java 持久化 API (JPA)*：我们将讨论 Spring Data JPA 项目，并演示它如何帮助简化数据访问逻辑的开发。由于 JPA 适用于 SQL 数据库，代码示例中将使用 MariaDB。

*   *跟踪实体变更和审计*：在数据库更新操作中，跟踪实体的创建日期或最后更新日期以及谁进行了更改是一个常见需求。此外，对于客户等关键信息，通常需要一个存储每个版本实体的历史表。我们将讨论 Spring Data JPA 和 Hibernate Envers（Hibernate 实体版本控制系统）如何帮助简化此类逻辑的开发。

*   *用于 NoSQL 数据库的 Spring Data*：我们将讨论什么是 NoSQL 数据库，为什么它们如此有趣，它们擅长什么，以及如何使用 Spring Data 从 Spring 应用程序中更轻松地访问它们的数据。对于代码示例，我们选择了 MongoDB。

*   *使用 Spring Boot 进行 Spring Data 配置*：Spring Data 在经典的 Spring 应用程序中简化了操作，但在 Spring Boot 应用程序中配置变得更加简单，因为有一个特殊的启动器库，其中包含大量自动配置选项。

除了介绍所有这些酷炫的技术之外，你还将学习如何使用 Testcontainers 作为数据库访问提供程序来测试你的仓库和服务。

## 介绍 Spring Data JPA

Spring Data JPA 简化了需要访问 JPA 数据源的应用程序的开发；这显然意味着使用 Hibernate 和 Jakarta Persistence API 组件进行数据操作的应用程序。使用 Spring Data JPA 的起点是将 `spring-data-jpa` 作为依赖项添加到项目中。无论是 Maven 还是 Gradle，将此依赖项添加到项目中都会导致所有必需的 Spring 依赖项被添加到应用程序类路径中。剩下的唯一事情就是将 Hibernate Core Jakarta 添加到配置中，并在需要不同（更新）版本时覆盖 `jakarta.annotation-api`。在图 10-1 中，你可以看到 `chapter10` 项目的依赖项列表，这是一个使用 Spring Data JPA 的经典 Spring 项目，如 IntelliJ IDEA 中的 Gradle 视图所示。

![](img/315511_6_En_10_Fig1_HTML.jpg)

一张截图展示了第 10 章项目的依赖项列表。它展开第 10 章并打开经典 Spring 项目。

图 10-1

显示 `chapter10` 项目依赖项的 Gradle 视图

### 使用 Spring Data JPA 仓库抽象进行数据库操作

在之前的数据访问章节中，仓库类（用于与数据库交互的类）是由开发人员显式创建的，并围绕 Hibernate 组件（`Session`、`EntityManager`）或 Spring（`JdbcTemplate`）构建。它们必须被显式配置为 Bean 并在需要的地方注入。添加事务行为也是如此；如果仓库需要支持事务，开发人员必须显式地在与数据库通信的方法上添加 `@Transactional` 注解。

Spring Data 及其所有子项目的主要概念之一是 *Repository* 抽象，它属于 Spring Data Commons^(⁸⁵) 项目，这是依赖项之一。在 Spring Data JPA 中，仓库抽象封装了底层的 JPA `EntityManager`，并为基于 JPA 的数据访问提供了一个更简单的接口。这意味着你不必使用 `EntityManager` 编写代码来访问数据，除非你确实有一些 Spring Data 无法根据你的配置为你生成的自定义查询。

Spring Data 中的核心接口是 `org.springframework.data.repository.Repository<T,ID>` 接口，它是一个标记接口（注意不要将其与 `@Repository` 原型注解混淆）。Spring Data 提供了 `Repository<T, ID>` 接口的各种扩展；其中之一是 `org.springframework.data.repository.CrudRepository<T, ID>` 接口（它也属于 Spring Data Commons 项目），我们将在本节中讨论。直接扩展 `Repository<T, ID>` 或通过扩展其子接口之一的接口被称为*领域仓库*，因为它们用具体的领域对象类型替换了泛型 `T`。这些接口公开了用于管理领域对象的 CRUD 方法。

在解释 `CrudRepository<T, ID>` 接口及其重要性之前，请先查看图 10-2，它显示了 Spring `Repository<T, ID>` 接口的层次结构。

![](img/315511_6_En_10_Fig2_HTML.jpg)

一个层次结构图解释了 Spring Data 仓库接口。它从一个简单的 JPA 仓库开始，逐步发展到修订仓库、响应式仓库和响应式 CRUD 仓库。

图 10-2

Spring Data `Repository` 接口层次结构

此时你可能会挠头并问：*嘿，这些大多是接口，它们怎么能完成你之前提到的所有事情呢？* 嗯，我们在这个项目中使用的是 Java 17，所以从技术上讲，默认方法可能是一个答案，但并非如此。我们正在使用 Spring，所以最简单的答案是*代理*。当你开始编写自己的仓库时，一切都会变得清晰，但在此之前，让我们回到 `CrudRepository<T, ID>` 接口。

`CrudRepository<T, ID>` 接口^(⁸⁶) 在处理数据时提供了许多常用方法。清单 10-1 显示了一个表示该接口声明的代码片段，该片段摘自 Spring Data Commons 项目源代码。

```
package org.springframework.data.repository;
import java.util.Optional;
@NoRepositoryBean
public interface CrudRepository extends Repository {
S save(S entity);
Iterable saveAll(Iterable entities);
Optional findById(ID id);
boolean existsById(ID id);
Iterable findAll();
Iterable findAllById(Iterable ids);
long count();
void deleteById(ID id);
void delete(T entity);
void deleteAllById(Iterable ids);
void deleteAll(Iterable entities);
void deleteAll();
}
清单 10-1
CrudRepository 源代码
```



查看这个接口，你可能会认出我们之前添加到仓库接口中并在仓库类中实现的方法签名。`CrudRepository<T, ID>` 接口声明了一整套你期望仓库类为数据访问提供的方法。这些方法名称不言自明，而且——别担心——你无需为它们提供实现！为了让你安心，我们来看一个例子。

![](img/315511_6_En_10_Figa_HTML.jpg)一个感叹号图标。 本章使用的实体类与**第** **6****章至第** **9****章**中使用的实体类相同。如果需要回顾，请查阅之前的任何章节或查看之前章节的代码。如果你已经阅读了其他章节，那么像 `Singer` 和 `Album` 这样的实体类现在应该已经很熟悉了。

代码清单 10-2 展示了一个名为 `SingerRepository` 的经典仓库接口，它只声明了几个查找方法。

```
package com.apress.prospring6.ten;
import com.apress.prospring6.ten.entities.Singer;
import java.util.List;
public interface SingerRepository {
List findAll();
List findByFirstName(String firstName);
List findByFirstNameAndLastName(String firstName, String lastName);
}
代码清单 10-2
经典的 SingerRepository 接口
```

现在可以修改这个接口，通过扩展 `CrudRepository<T, ID>` 将其转换为 Spring Data 领域仓库接口，如代码清单 10-3 所示。

```
package com.apress.prospring5.ten;
// 其他导入语句已省略
import org.springframework.data.repository.CrudRepository;
public interface SingerRepository extends CrudRepository {
Iterable findByFirstName(String firstName);
Iterable findByFirstNameAndLastName(String firstName, String lastName);
}
代码清单 10-3
Spring Data 领域仓库 SingerRepository 接口
```

请注意，我们只需要在这个接口中声明两个方法，因为 `findAll()` 方法已经由 `CrudRepository<T, ID>` 接口提供了。`SingerRepository` 接口扩展了 `CrudRepository<T, ID>` 接口，传入了实体类（`Singer`）和 `ID` 类型（`Long`）。Spring Data 的仓库抽象的一个奇妙之处在于，当你使用常见的命名约定 `findBy{fieldName}`（例如 `findByFirstName` 和 `findByFirstNameAndLastName`）时，你不需要为 Spring Data JPA 提供命名查询。相反，在运行时，Spring Data JPA 会根据方法名和替换了泛型类型的实体类来“推断”并为你构建查询。例如，对于 `findByFirstName()` 方法，Spring Data JPA 会自动为你准备查询 `select s from Singer s where s.firstName = :firstName`，并根据参数设置命名参数 `firstName`。

现在我们已经声明了接口，必须创建配置来告诉 Spring Data 在哪里找到这个接口。这可以通过在配置类上添加 `@EnableJpaRepositories` 注解来实现，如代码清单 10-4 所示。

```
package com.apress.prospring6.ten.config;
// 其他导入语句已省略
import org.springframework.transaction.annotation.EnableTransactionManagement;
import org.springframework.data.jpa.repository.config.EnableJpaRepositories;
@Import(BasicDataSourceCfg.class)
@Configuration
@EnableTransactionManagement
@ComponentScan(basePackages = {"com.apress.prospring6.ten"})
@EnableJpaRepositories("com.apress.prospring6.ten.repos")
public class DataJpaCfg {
@Bean
public PlatformTransactionManager transactionManager() {
// 代码已省略
return transactionManager;
}
@Bean
public LocalContainerEntityManagerFactoryBean entityManagerFactory() {
// 代码已省略
factory.setPackagesToScan("com.apress.prospring6.ten.entities");
return factory;
}
// 其余配置已省略
}
代码清单 10-4
Spring Data JPA 配置
```

这个类和 `BasicDataSourceCfg` 类的完整配置在之前的数据访问章节（从**第** **6****章**到现在）中已经反复介绍过，实际上，告诉 Spring 仓库接口位置的唯一要求就是 `@EnableJpaRepositories("com.apress.prospring6.ten.repos")`。`@EnableJpaRepositories` 注解非常强大，允许你通过声明与 `@ComponentScan` 提供的配置属性非常相似的属性，以多种方式指定多个位置。顺便说一下，默认属性是 `basePackages`。除此之外，`namedQueriesLocation` 属性用于指定包含命名查询的属性文件的位置，`entityManagerFactoryRef` 属性用于指定用于创建查询的 `EntityManager` bean 的名称，`transactionManagerRef` 属性用于指定用于创建仓库实例的 `TransactionManager` bean 的名称，以及许多其他有用的属性（请随意查看官方文档）。

那么，这是如何工作的呢？回顾**第** **5****章**，有两种类型的代理：JDK 代理，它实现与目标对象相同的接口；以及基于类的代理，它扩展目标对象类。Spring Data JPA 要求仓库被声明为扩展 `Repository<T, ID>` 或其子接口之一的接口。通过添加 `@EnableJpaRepositories("com.apress.prospring6.ten.repos")` 配置，我们告诉 Spring 在这个包中查找这些类型的接口。对于每个接口，Spring Data 基础设施组件会注册特定于持久化技术的 `FactoryBean`，以创建处理查询方法调用的适当代理。

现在我们已经有了一个 Spring Data 领域仓库接口和一个告诉 Spring 其位置的配置，下一步是创建一个事务性服务来使用我们的仓库。代码清单 10-5 中显示的 `SingerServiceImpl` 类只是调用了 `SingerRepository` 实例的方法。



```
package com.apress.prospring6.ten.service;
import java.util.stream.Stream;
import java.util.stream.StreamSupport;
// 其他导入语句已省略
@Service
@Transactional
public class SingerServiceImpl implements SingerService {
private final SingerRepository singerRepository;
public SingerServiceImpl(SingerRepository singerRepository) {
this.singerRepository = singerRepository;
}
@Override
@Transactional(readOnly=true)
public Stream findAll() {
return StreamSupport.stream(singerRepository.findAll().spliterator(), false);
}
@Override
@Transactional(readOnly=true)
public Stream findByFirstName(String firstName) {
return StreamSupport.stream(singerRepository.findByFirstName(firstName).spliterator(), false);
}
@Override
@Transactional(readOnly=true)
public Stream findByFirstNameAndLastName(String firstName, String lastName) {
return StreamSupport.stream(singerRepository.findByFirstNameAndLastName(firstName, lastName).spliterator(), false);
}
}
清单 10-5
使用 Spring Data Repository 实例的 SingerServiceImpl 类
```

可以看到，我们不再需要注入 `EntityManager`，而是需要将 `singerRepository` 实例注入到服务 Bean 中。这个实例由 Spring 基于 `SingerRepository` 接口自动生成。由于使用了 Spring Data JPA，所有底层工作都已为我们完成。在清单 10-6 中，你可以看到一个测试类，现在你应该已经对其内容很熟悉了。

```
package com.apress.prospring6.ten;
// 导入语句已省略
@Testcontainers
@SqlMergeMode(SqlMergeMode.MergeMode.MERGE)
@Sql({ "classpath:testcontainers/drop-schema.sql", "classpath:testcontainers/create-schema.sql" })
@SpringJUnitConfig(classes = {SingerServiceTest.TestContainersConfig.class})
public class SingerServiceTest extends TestContainersBase {
private static final Logger LOGGER = LoggerFactory.getLogger(SingerServiceTest.class);
@Autowired
SingerService singerService;
@Autowired
ApplicationContext ctx;
@Test
public void testFindAll(){
var singers = singerService.findAll().peek(s -> LOGGER.info(s.toString())).toList();
assertEquals(3, singers.size());
}
@Test
public void testFindByFirstName(){
var singers = singerService.findByFirstName("John").peek(s -> LOGGER.info(s.toString())).toList();
assertEquals(2, singers.size());
}
@Test
public void testFindByFirstNameAndLastName(){
var singers = singerService.findByFirstNameAndLastName("John", "Mayer").peek(s -> LOGGER.info(s.toString())).toList();
assertEquals(1, singers.size());
}
@Configuration
@Import(DataJpaCfg.class)
public static class TestContainersConfig {
@Autowired
Properties jpaProperties;
@PostConstruct
public void initialize() {
jpaProperties.put(Environment.FORMAT_SQL, true);
jpaProperties.put(Environment.USE_SQL_COMMENTS, true);
jpaProperties.put(Environment.SHOW_SQL, true);
jpaProperties.put(Environment.STATEMENT_BATCH_SIZE, 30);
}
}
}
清单 10-6
SingerServiceTest 测试类
```

这个测试类没什么特别之处，Testcontainers MariaDB 容器配置已被隔离到 `TestContainersBase` 类中，以避免在此重复。运行此测试类时，所有方法都应通过，但像往常一样，让我们将所有 Spring 库的日志级别调高到 `TRACE`，并查看执行日志，这能让我们了解 Spring 正在做的大量工作。请查看清单 10-7 中的日志片段。

```
DEBUG: RepositoryConfigurationDelegate - Scanning for JPA repositories in packages com.apress.prospring6.ten.repos. 
TRACE: ClassPathScanningCandidateComponentProvider - Scanning file [/../com/apress/prospring6/ten/repos/SingerRepository.class]
DEBUG: ClassPathScanningCandidateComponentProvider - Identified candidate component class: file [/../com/apress/prospring6/ten/repos/SingerRepository.class]
TRACE: RepositoryConfigurationDelegate - Spring Data JPA - Registering repository: singerRepository 
- Interface: com.apress.prospring6.ten.repos.SingerRepository 
- Factory: org.springframework.data.jpa.repository.support.JpaRepositoryFactoryBean 
INFO : RepositoryConfigurationDelegate - Finished Spring Data repository scanning in 37 ms. Found 1 JPA repository interfaces.
...
DEBUG: RepositoryFactorySupport - Initializing repository instance for com.apress.prospring6.ten.repos.SingerRepository...
...
DEBUG: RepositoryFactorySupport - Finished creation of repository instance for com.apress.prospring6.ten.repos.SingerRepository.
...
TRACE: TransactionAspectSupport - Getting transaction for
[com.apress.prospring6.ten.service.SingerServiceImpl.findAll] 
TRACE: AbstractFallbackTransactionAttributeSource - Adding transactional method
'org.springframework.data.jpa.repository.support.SimpleJpaRepository.findAll'
with attribute: PROPAGATION_REQUIRED,ISOLATION_DEFAULT,readOnly 
TRACE: TransactionAspectSupport - Getting transaction for
[org.springframework.data.jpa.repository.support.SimpleJpaRepository.findAll]
清单 10-7
SingerServiceTest 执行日志片段
```

配置的包 `com.apress.prospring6.ten.repos` 及其子包被扫描，并且 `SingerRepository` 接口被识别为创建 Repository 实例的候选（标记为 <1> 的行）。

此日志的特别之处在于，似乎为被调用的 Repository 实例方法创建了一个事务（标记为 <2> 的行）。那么，这是怎么回事呢？实际上，支持代理的类是 `org.springframework.data.jpa.repository.support.SimpleJpaRepository<T, ID>`，如果你查看 Spring 代码^(⁸⁷)，你会注意到这个类被注解了 `@Transactional(readOnly = true)`。这就是 Repository 方法事务的来源。之所以需要它们，是因为任何 JPA 操作都应在事务上下文中执行；这样，在发生错误时，回滚将确保数据库保持良好状态。这显然意味着，对于仅调用单个 Repository 方法的服务方法，不需要额外的事务。即使配置了，由于默认的传播模式是 `PROPAGATION_REQUIRED`（标记为 <3> 的行），服务方法将在与 Repository 方法相同的事务内执行。

在讨论更复杂的查询以及如何支持调用它们之前，我们先来谈谈 `JpaRepository<T, ID>` 接口。

### 使用 `JpaRepository`

`JpaRepository<T, ID>` 接口是比 `CrudRepository<T, ID>` 更高级的 Spring 接口，它可以使创建自定义 Repository 更加容易。`JpaRepository<T, ID>` 接口提供了批量、分页和排序操作。图 10-3 展示了 `JpaRepository<T, ID>` 与 `CrudRepository<T, ID>` 接口之间的关系。

![](img/315511_6_En_10_Fig3_HTML.jpg)

Spring Data JPA Repository 的层次结构。从 JPA Repository 开始，依次经过 Query by Example Executor、Repository Paging and Sorting 和 Repository。

图 10-3

Spring Data `JpaRepository<T, ID>` 层次结构

根据应用程序的复杂程度，你可以选择使用 `CrudRepository<T, ID>` 或 `JpaRepository<T, ID>`。从图 10-3 可以看出，`JpaRepository<T, ID>` 扩展了 `CrudRepository<T, ID>`，因此提供了所有相同的功能。



### 使用自定义查询的 Spring Data JPA

在复杂的应用程序中，你可能需要无法被 Spring “推断”出来的自定义查询。

在前面的章节中，我们展示了使用 `@NamedQuery` 注解声明的命名查询，为查询执行提供支持的方法，无论是通过 Hibernate 的 `Session` 还是 `EntityManager`。Spring Data 仓库也支持命名查询。Spring Data 会尝试将开发者声明的方法调用解析为一个命名查询，该查询以配置的领域类的简单名称开头，后跟由点分隔的方法名。这种命名查询的方式在使用 Hibernate 的 `Session` 或 `EntityManager` 时也曾使用过，目的就是为了让过渡更实用。然而，Spring Data 的命名查询确实存在一些限制；例如，不支持命名参数。为了举例说明，考虑一个用于选择发行日期晚于 `2010-01-01` 的专辑的命名查询。清单 10-8 展示了包含该命名查询的关键代码片段。

```
package com.apress.prospring6.ten.entities;
import jakarta.persistence.NamedQuery;
// 其他导入语句已省略
@Entity
@Table(name = "ALBUM")
@NamedQuery(name=Album.FIND_WITH_RELEASE_DATE_GREATER_THAN, query="select a from Album a where a.releaseDate > ?1")
public class Album extends AbstractEntity {
@Serial
private static final long serialVersionUID = 3L;
public static final String FIND_WITH_RELEASE_DATE_GREATER_THAN = "Album.findWithReleaseDateGreaterThan";
@Column
private String title;
@Column(name = "RELEASE_DATE")
private LocalDate releaseDate;
@ManyToOne
@JoinColumn(name = "SINGER_ID")
private Singer singer;
// 其余代码已省略
}
清单 10-8
包含命名查询的 Album 实体
```

对于需要命名参数的情况，必须使用 `@Query` 注解显式定义查询。我们将使用此注解来搜索标题中包含 `The` 的所有音乐专辑。清单 10-9 描述了 `AlbumRepository` 接口。

```
package com.apress.prospring6.ten.repos;
import org.springframework.data.jpa.repository.Query;
import org.springframework.data.repository.query.Param;
// 其他导入语句已省略
public interface AlbumRepository extends JpaRepository {
Iterable findBySinger(Singer singer);
Iterable findWithReleaseDateGreaterThan(LocalDate rd);
@Query("select a from Album a where a.title like %:title%")
Iterable findByTitle(@Param("title") String t);
}
清单 10-9
包含命名查询和 @Query 方法的 AlbumRepository
```

Spring Data 将 `findWithReleaseDateGreaterThan(..)` 方法与名为 `Album.findWithReleaseDateGreaterThan` 的查询匹配。`findByTitle(..)` 方法的查询有一个名为 `title` 的命名参数。当命名参数的名称与 `@Query` 注解方法中参数名称相同时，则不需要 `@Param` 注解。如果方法参数具有不同的名称，则需要使用 `@Param` 注解来告诉 Spring 该参数的值将被注入到查询中的命名参数中。

`AlbumServiceImpl` 服务类非常简单，仅使用 `albumRepository` bean 来调用其方法。它在清单 10-10 中描述。

```
package com.apress.prospring6.ten.service;
// 其他导入语句已省略
import java.util.stream.Stream;
import java.util.stream.StreamSupport;
@Service
@Transactional(readOnly = true)
public class AlbumServiceImpl implements AlbumService {
private final AlbumRepository albumRepository;
public AlbumServiceImpl(AlbumRepository albumRepository) {
this.albumRepository = albumRepository;
}
@Override
public Stream findBySinger(Singer singer) {
return StreamSupport.stream(albumRepository.findBySinger(singer).spliterator(), false);
}
@Override
public Stream findWithReleaseDateGreaterThan(LocalDate rd) {
return StreamSupport.stream(albumRepository.findWithReleaseDateGreaterThan(rd).spliterator(), false);
}
@Override
public Stream findByTitle(String title) {
return StreamSupport.stream(albumRepository.findByTitle(title).spliterator(), false);
}
}
清单 10-10
调用 AlbumRepository 方法的 AlbumServiceImpl 服务
```

`findBySinger(..)` 查询很简单，Spring Data 能够自行解析它。因此，测试类将不涵盖此查询。你可以自行编写此查询的测试。清单 10-11 展示了针对两个稍微复杂查询的测试类。

```
package com.apress.prospring6.ten;
// 导入语句已省略
@Testcontainers
@Sql({ "classpath:testcontainers/drop-schema.sql", "classpath:testcontainers/create-schema.sql" })
@SpringJUnitConfig(classes = {AlbumServiceTest.TestContainersConfig.class})
public class AlbumServiceTest extends TestContainersBase {
private static final Logger LOGGER = LoggerFactory.getLogger(AlbumServiceTest.class);
@Autowired
AlbumService albumService;
@Test
public void testFindWithReleaseDateGreaterThan(){
var albums = albumService
.findWithReleaseDateGreaterThan(LocalDate.of(2010, 1, 1))
.peek(s -> LOGGER.info(s.toString())).toList();
assertEquals(2, albums.size());
}
@Test
public void testFindByTitle(){
var albums = albumService
.findByTitle("The")
.peek(s -> LOGGER.info(s.toString())).toList();
assertEquals(1, albums.size());
}
// 测试配置类已省略
}
清单 10-11
AlbumServiceTest 类
```

Spring Data 仓库功能强大且用途广泛。使用 `@Query` 注解的方法也支持排序，只需添加一个 `org.springframework.data.domain.Sort` 参数即可。使用 `@Query` 注解声明的查询甚至支持 SpEL 表达式。

本节中的示例仅展示了读取数据的查询。修改数据的查询还需要使用 `@Modifying` 注解。举例来说，修改 `SingerRepository` 以添加一个根据 `id` 更改歌手记录名字的查询。方法声明如清单 10-12 所示。

```
Package com.apress.prospring6.ten.repos;
// 其他导入语句已省略
import org.springframework.data.jpa.repository.Modifying;
public interface SingerRepository extends CrudRepository {
// 其他方法声明已省略
@Modifying(clearAutomatically = true)
@Query("update Singer s set s.firstName = ?1 where s.id = ?2")
int setFirstNameFor(String firstName, Long id);
}
清单 10-12
使用 @Modifying 注解的 Spring Data 查询方法
```

`@Modifying` 注解设计为仅与 `@Query` 注解一起使用，单独使用没有意义。如果单独使用，Spring Data 会忽略它。

`@Modifying` 注解支持两个属性：

*   `flushAutomatically`：当设置为 `true`（默认为 `false`）时，会导致底层持久化上下文在*执行*修改查询*之前*被刷新。

*   `clearAutomatically`：当设置为 `true`（默认为 `false`）时，会导致底层持久化上下文在*执行*修改查询*之后*被清除。这意味着更改后的实体在此方法执行后立即持久化到数据库。



`setFirstNameFor(..)` 方法被标注为 `@Modifying(clearAutomatically = true)` 的原因在于：在服务方法中，会调用此方法执行更新操作，随后通过 `findById(id)` 仓库方法从数据库检索待返回的实体。由于变更会在事务结束时刷新，而我们无法控制其具体时机，因此返回的实体可能并非更新后的版本。

更有趣的是，调用 `setFirstNameFor(..)` 的服务方法会返回更新后的实例，这正好可供我们测试。该服务方法如清单 10-13 所示。

```
package com.apress.prospring6.ten.service;
// 导入语句已省略
@Service
@Transactional
public class SingerServiceImpl implements SingerService {
private final SingerRepository singerRepository;
public SingerServiceImpl(SingerRepository singerRepository) {
this.singerRepository = singerRepository;
}
// 其他方法已省略
@Transactional(propagation = Propagation.REQUIRES_NEW, label = "modifying")
@Override
public Singer updateFirstName(String firstName, Long id) {
singerRepository.findById(id).ifPresent(s ->
singerRepository.setFirstNameFor(firstName, id));
return  singerRepository.findById(id).orElse(null);
}
}
清单 10-13
调用 @Modified 注解的 Spring Data 查询方法的服务方法
```

测试方法如清单 10-14 所示。

```
package com.apress.prospring6.ten;
// 其他导入语句已省略
import org.springframework.test.annotation.Rollback;
@Testcontainers
@SqlMergeMode(SqlMergeMode.MergeMode.MERGE)
@Sql({ "classpath:testcontainers/drop-schema.sql", "classpath:testcontainers/create-schema.sql" })
@SpringJUnitConfig(classes = {SingerServiceTest.TestContainersConfig.class})
public class SingerServiceTest extends TestContainersBase {
@Rollback
@Test
@SqlGroup({
@Sql(scripts = {"classpath:testcontainers/add-nina.sql"},
executionPhase = Sql.ExecutionPhase.BEFORE_TEST_METHOD),
})
@DisplayName("应更新歌手姓名")
public void testUpdateFirstNameByQuery(){
var nina = singerService.updateFirstName("Eunice Kathleen", 5L);
assertAll( "nina 未更新" ,
() -> assertNotNull(nina),
() -> assertEquals("Eunice Kathleen", nina.getFirstName())
);
}
}
清单 10-14
调用 @Modified 注解的 Spring Data 查询方法的服务方法的测试方法
```

![](img/315511_6_En_10_Figb_HTML.jpg)信息图标。 请注意，此处并未使用 `@Sql` 注解和删除脚本来清理上下文，而是采用了第 **9** 章介绍的 `@Rollback` 注解。

`@Modifying` 注解同样适用于条件删除查询。借助 Spring Data JPA 仓库，许多数据库操作都能变得简单。然而，全面覆盖所有操作已超出本书范围。建议读者自行查阅官方文档^(⁸⁸)进行深入研究。

### 投影查询

第 **7** 章和第 **8** 章已经介绍了投影查询的主题，因此本节仅展示如何使用 Spring Data JPA 配置投影查询。

为了将查询结果限制为仅包含少数几个字段，需要一个接口来暴露待读取属性的访问器方法。Spring 支持多种有趣的方式来声明投影接口，但为简单起见，本示例仅展示一个暴露歌手名字和姓氏的接口。由于从 Java 8 开始接口支持默认方法，我们将添加一个默认方法来拼接名字和姓氏，并将其作为全名暴露。仓库方法和接口均如清单 10-15 所示。

```
package com.apress.prospring6.ten.service;
// 导入语句已省略
public interface SingerService {
// 其他方法已省略
Stream findByLastName(String lastName);
interface FullName {
String getFirstName();
String getLastName();
default String getFullName() {
return getFirstName().concat(" ").concat(getLastName());
}
}
}
清单 10-15
投影接口和仓库方法
```

服务方法仅调用 `findByLastName(..)` 方法，而测试方法则打印结果并验证返回单个结果的假设。两者均可在本书的代码仓库中找到，此处不再列出。

唯一值得关注的是仓库方法返回对象的实际类型。由于数据库结果使用 `FullName` 接口建模，Spring 必定生成了相应的实现，对吗？嗯，并非字面意义上的实现。实际上，Spring 再次创建了一个代理，该代理实现了 `FullName` 接口，因此方法调用会被转发给负责存储和返回实际值的 Spring Data 基础设施组件。通过在测试方法中设置断点，并利用 IntelliJ IDEA 的运行时表达式求值功能检查返回集合的内容，可以轻松观察到这一点。只需在代码执行暂停时右键单击源代码，在上下文菜单中选择 **Evaluate Expression**，然后输入 `singers.get(0)`。注意对象的类型，如果展开它，你将看到目标对象和已实现的接口。其外观应与图 10-4 所示非常相似（但请注意，在最终版本的 Spring Data JPA 中，某些类型可能会被重命名、移动等，存在微小可能性）。

![](img/315511_6_En_10_Fig4_HTML.jpg)

截图包含两部分。顶部是代码片段，底部是结果部分。它描述了 Spring Data 实例。

图 10-4

检查 Spring Data 实例



## 跟踪实体类的变更

在大多数应用程序中，我们需要跟踪用户维护的业务数据的基本审计活动。审计信息通常包括创建数据的用户、创建日期、最后修改日期以及最后修改的用户。

Spring Data JPA 项目以 JPA 实体监听器的形式提供了此功能，可帮助您自动跟踪审计信息。要使用此功能，在 Spring 4 之前，实体类需要实现 `Auditable<U, ID, T extends TemporalAccessor> extends` `Persistable<ID>` 接口（属于 Spring Data Commons）^(⁸⁹)，或扩展任何实现此接口的类。清单 10-16 展示了从 Spring Data 参考文档中提取的 `Auditable` 接口。

```
package org.springframework.data.domain;
import java.time.temporal.TemporalAccessor;
import java.util.Optional;
public interface Auditable
extends Persistable {
Optional getCreatedBy();
void setCreatedBy(U createdBy);
Optional getCreatedDate();
void setCreatedDate(T creationDate);
Optional getLastModifiedBy();
void setLastModifiedBy(U lastModifiedBy);
Optional getLastModifiedDate();
void setLastModifiedDate(T lastModifiedDate);
}
清单 10-16
Auditable 接口
```

从 `Auditable<U, ID, T extends TemporalAccessor>` 接口的定义可以看出，日期类型的列被限制为扩展 `java.time.temporal.TemporalAccessor` 的类型。从 Spring 5 开始，不再需要实现 `Auditable<U, ID, T extends TemporalAccessor>`，因为一切都可以用注解替代。

为了演示审计的工作原理，我们在数据库模式中创建一个名为 `SINGER_AUDIT` 的新表，该表基于 `SINGER` 表，并添加了四个与审计相关的列。这些列记录了实体创建的时间、创建者、最后编辑实体的用户以及编辑时间。清单 10-17 显示了建表脚本（`AuditSchema.sql`）。

```
CREATE TABLE SINGER_AUDIT (
ID INT NOT NULL AUTO_INCREMENT
, FIRST_NAME VARCHAR(60) NOT NULL
, LAST_NAME VARCHAR(40) NOT NULL
, BIRTH_DATE DATE
, VERSION INT NOT NULL DEFAULT 0
, CREATED_BY VARCHAR(20) -- *
, CREATED_DATE TIMESTAMP -- *
, LAST_MODIFIED_BY VARCHAR(20) -- *
, LAST_MODIFIED_DATE TIMESTAMP -- *
, UNIQUE UQ_SINGER_AUDIT_1 (FIRST_NAME, LAST_NAME)
, PRIMARY KEY (ID)
);
清单 10-17
SINGER_AUDIT 表 DDL
```

标记的四列表示与审计相关的列。`@CreatedBy`、`@CreatedDate`、`@LastModifiedBy` 和 `@LastModifiedDate` 注解属于 `org.springframework.data.annotation` 包。使用这些注解后，日期列的类型限制不再适用。为了保持整洁，项目中添加了一个名为 `AuditableEntity<U>` 的 `@MappedSuperclass`，用于将审计字段分组。此类如清单 10-18 所示。

```
package com.apress.prospring6.ten.entities;
// 其他导入语句已省略
import org.springframework.data.jpa.domain.support.AuditingEntityListener;
import jakarta.persistence.EntityListeners;
import org.springframework.data.annotation;
@MappedSuperclass
@EntityListeners(AuditingEntityListener.class)
public abstract class AuditableEntity implements Serializable {
@CreatedDate
@Column(name = "CREATED_DATE")
protected LocalDateTime createdDate;
@CreatedBy
@Column(name = "CREATED_BY")
protected String createdBy;
@LastModifiedBy
@Column(name = "LAST_MODIFIED_BY")
protected String lastModifiedBy;
@LastModifiedDate
@Column(name = "LAST_MODIFIED_DATE")
protected LocalDateTime lastModifiedDate;
// getter 和 setter 已省略
}
清单 10-18
AuditableEntity 抽象类
```

`@Column` 注解应用于审计字段，以映射到表中的实际列。`@EntityListeners(AuditingEntityListener.class)` 注解注册了 `AuditingEntityListener`，用于持久化上下文中所有扩展此类的实体。

![](img/315511_6_En_10_Figc_HTML.jpg)一个灯泡的剪贴画。 `@EntityListeners(AuditingEntityListener.class)` 注解取代了声明 `orm.xml` 配置文件的需要，如 JPA 规范所述：

```

JPA

```

应用程序中任何需要审计的类都应声明为扩展 `AuditableEntity<U>` 类。因此，为了映射 `SINGER_AUDIT` 表，需要编写清单 10-19 中所示的 `SingerAudit` 类。

```
package com.apress.prospring6.ten.entities;
// 导入语句已省略
@Entity
@Table(name = "SINGER_AUDIT")
public class SingerAudit extends AuditableEntity {
@Serial
private static final long serialVersionUID = 5L;
@Id
@GeneratedValue(strategy = IDENTITY)
@Column(name = "ID")
private Long id;
@Version
@Column(name = "VERSION")
private int version;
@Column(name = "FIRST_NAME")
private String firstName;
@Column(name = "LAST_NAME")
private String lastName;
@Column(name = "BIRTH_DATE")
private LocalDate birthDate;
// getter 和 setter 已省略
}
清单 10-19
SingerAudit 实体类
```

为了管理 `SingerAudit` 类型的审计实体，应使用一个仓库接口和一个服务类。`SingerAuditRepository` 接口仅扩展了 `CrudRepository<SingerAudit, Long>`，该接口已经声明了我们将用于 `SingerAuditService` 的所有方法。`SingerAuditService` 接口如清单 10-20 所示。

```
package com.apress.prospring6.ten.service;
// 导入语句已省略
public interface SingerAuditService {
Stream findAll();
SingerAudit findById(Long id);
SingerAudit save(SingerAudit singer);
}
清单 10-20
SingerAuditService 接口
```

实现 `SingerAuditService` 的 `SingerAuditServiceImpl` 类仅是在事务上下文中调用 `SingerAuditRepository` 的方法。两者都非常简单，目前无需关注。但是，要告诉 Spring 自动更新审计字段，还需要进行一些配置：配置类必须使用 `@EnableJpaAuditing` 注解，并且必须配置一个实现 `AuditorAware<T>` 的 bean，如清单 10-21 所示。

```
package com.apress.prospring6.ten.config;
import org.apache.commons.lang3.RandomStringUtils;
import org.springframework.data.domain.AuditorAware;
import org.springframework.data.jpa.repository.config.EnableJpaAuditing;
// 部分导入语句已省略
@EnableJpaAuditing
@Configuration
public class AuditCfg {
@Bean
public AuditorAware auditorProvider() {
return () -> Optional.of("prospring6-"
+ RandomStringUtils.random(6, true, true));
}
}
清单 10-21
用于启用自动审计支持的 Spring 配置类
```

这是一个非常简单的配置。关键在于：在生产级应用程序中，为了填充 `@CreatedBy` 和 `@LastModifiedBy` 注解的字段，其值由与 Spring Security 交互的 bean 提供。在实际情况下，这应该是一个包含用户信息的实例，例如一个代表从 `SecurityContextHolder` 中检索到的执行数据更新操作的用户类。然而，由于该主题相当高级，将在本书末尾（**第** **17****章**）介绍，因此这里使用一个实现了 `AuditorAware<String>` 接口的 bean，并采用简化的 lambda 语句。清单 10-21 中的 lambda 语句可以展开为以下内容：



```
new AuditorAware() {
@Override
public Optional getCurrentAuditor() {
{
return Optional.of("prospring6");
}
}
};
```

在这个 `AuditorAware<String>` 的匿名实现中，实现了 `getCurrentAuditor()` 方法。返回的值是 *prospring6* 与随机生成的六字符文本拼接而成。这有助于生成不同的用户值，以便我们测试 `@CreatedBy` 和 `@LastModifiedBy` 注解字段的值是否被正确设置。

现在配置和实现都已就绪，下一步是测试我们的审计支持功能。为了将测试限制在各自的上下文中，我们添加了 `AuditServiceTest` 类，该类执行 `SingerAuditServiceImpl` 类中的三个服务方法，并检查对其效果所做的假设。测试类和方法如清单 10-22 所示。

```
package com.apress.prospring6.ten;
// 导入语句已省略
@Testcontainers
@Sql({ "classpath:testcontainers/audit/drop-schema.sql", "classpath:testcontainers/audit/create-schema.sql" })
@SpringJUnitConfig(classes = {AuditServiceTest.TestContainersConfig.class})
public class AuditServiceTest extends TestContainersBase {
private static final Logger LOGGER = LoggerFactory.getLogger(AuditServiceTest.class);
@Autowired
SingerAuditService auditService;
@BeforeEach
void setUp(){
var singer = new SingerAudit();
singer.setFirstName("BB");
singer.setLastName("King");
singer.setBirthDate(LocalDate.of(1940, 8, 16));
auditService.save(singer);
}
@Test
void testFindById() {
var singer = auditService.findAll().findFirst().orElse(null);
assertAll( "auditFindByIdTest" ,
() -> assertNotNull(singer),
() -> assertTrue(singer.getCreatedBy().isPresent()),
() -> assertTrue(singer.getLastModifiedBy().isPresent()),
() -> assertNotNull(singer.getCreatedDate()),
() -> assertNotNull(singer.getLastModifiedDate())
);
LOGGER.info(">> 创建的记录: {} ", singer);
}
@Test
void testUpdate() {
var singer = auditService.findAll().findFirst().orElse(null);
assertNotNull(singer);
singer.setFirstName("Riley B.");
var updated = auditService.save(singer);
assertAll( "auditUpdateTest" ,
() -> assertEquals("Riley B.", updated.getFirstName()),
() -> assertTrue(updated.getLastModifiedBy().isPresent()),
() -> assertNotEquals(updated.getCreatedBy().orElse(null), updated.getLastModifiedBy().orElse(null))
);
LOGGER.info(">> 更新的记录: {} ", updated);
}
@Configuration
@Import({DataJpaCfg.class, AuditCfg.class})
public static class TestContainersConfig {
@Autowired
Properties jpaProperties;
@PostConstruct
public void initialize() {
jpaProperties.put(Environment.FORMAT_SQL, true);
jpaProperties.put(Environment.USE_SQL_COMMENTS, true);
jpaProperties.put(Environment.SHOW_SQL, true);
jpaProperties.put(Environment.STATEMENT_BATCH_SIZE, 30);
}
}
}
清单 10-22
用于测试 SingerAuditServiceImpl 类的类
```

测试方法应通过，并检查审计字段的值是否已填充。

![](img/315511_6_En_10_Figd_HTML.jpg)一个灯泡的剪贴画。 这些测试方法揭示了一个重要问题：对于设计为一天内多次编辑的记录，`java.time.LocalDate` 并不是 `@CreatedDate` 和 `@LastModifiedDate` 注解字段的良好类型。在我们的示例中，我们使用了 `java.time.LocalDateTime`，以便更明显地看出记录被修改过。

JPA 审计支持需要在类路径中添加一个额外的库。Spring Data 需要 `spring-aspects` 来创建特殊的代理，这些代理会拦截实体更新，并在实体持久化之前添加自身的更改。

## 使用 Hibernate Envers 保持实体版本

在企业级应用中，对于关键业务数据，通常需要保留每个实体的几个版本。例如，在客户关系管理（CRM）系统中，每次插入、更新或删除客户记录时，都应保留之前的版本到历史表或审计表中，以满足公司的审计或其他合规要求。

要实现这一点，有两种常见的选择。第一种是创建数据库触发器，在任何更新操作之前将更新前的记录克隆到历史表中。第二种是在数据访问层中开发逻辑（例如，使用 `AOP`）。然而，这两种方法都有其缺点。触发器方法与数据库平台绑定，而手动实现逻辑则相当笨拙，可能影响性能，并且非常容易出错。

Hibernate Envers^(⁹⁰)（*实体版本控制系统*的缩写）是一个专门设计用于自动化实体版本控制的 Hibernate 模块。在本节中，我们将讨论如何使用 Envers 来实现上一节中引入的 `SingerAudit` 实体的版本控制。

Hibernate Envers 并非 JPA 的特性。我们在此提及它，是因为我们认为在讨论了一些可以与 Spring Data JPA 一起使用的基本审计功能之后，再介绍它更为合适。

Spring Data 为 Hibernate Envers 提供了一个名为 `spring-data-envers` 的模块。`spring-data-jpa` 是 Spring Data Envers 库的传递依赖。图 10-5 展示了一个使用 Hibernate Envers 进行实体版本控制的 Spring Data 项目的依赖关系。

![](img/315511_6_En_10_Fig5_HTML.jpg)

一张截图展示了第 10 章使用 Hibernate Envers 模块的依赖关系。

图 10-5
显示 `chapter10` Envers 项目依赖关系的 Gradle 视图

Envers 支持两种审计策略，如表 10-1 所示。

表 10-1
Envers 审计策略

| 审计策略 | 描述 |
| --- | --- |
| 默认 | Envers 维护一个记录修订版本的列。每次插入或更新记录时，都会向历史表中插入一条新记录，其修订版本号从数据库序列或表中获取。 |
| 有效性审计 | 此策略存储每条历史记录的起始和结束修订版本。每次插入或更新记录时，都会向历史表中插入一条带有起始修订版本号的新记录。同时，之前的记录会被更新，添加上结束修订版本号。还可以配置 Envers，使其将结束修订版本更新的时间戳记录到之前的历史记录中。 |

在本节中，我们将演示有效性审计策略。尽管这会触发更多的数据库更新，但检索历史记录会变得更快。由于结束修订版本的时间戳也会写入历史记录，因此在查询数据时，更容易识别出特定时间点的记录快照。



### 为实体版本控制添加表

为了支持实体版本控制，我们需要添加几个表。首先，对于实体（本例中为 `SingerAudit` 实体类）将要进行版本控制的每个表，我们需要创建相应的历史记录表。对于 `SINGER_AUDIT` 表中记录的版本控制，我们创建一个名为 `SINGER_AUDIT_H` 的历史记录表。清单 10-23 中的代码展示了建表脚本。

```
CREATE TABLE SINGER_AUDIT_H (
ID INT NOT NULL AUTO_INCREMENT
, FIRST_NAME VARCHAR(60) NOT NULL
, LAST_NAME VARCHAR(40) NOT NULL
, BIRTH_DATE DATE
, VERSION INT NOT NULL DEFAULT 0
, CREATED_BY VARCHAR(20)
, CREATED_DATE TIMESTAMP
, LAST_MODIFIED_BY VARCHAR(20)
, LAST_MODIFIED_DATE TIMESTAMP
, AUDIT_REVISION INT NOT NULL // *
, ACTION_TYPE INT // *
, AUDIT_REVISION_END INT // *
, AUDIT_REVISION_END_TS TIMESTAMP // *
, UNIQUE UQ_SINGER_AUDIT_H_1 (FIRST_NAME, LAST_NAME)
, PRIMARY KEY (ID, AUDIT_REVISION)
);
Listing 10-23
描述 SingerAudit 实体的 Hibernate 审计表的 SQL 代码 (AuditSchema.sql)
```

为了支持有效性审计策略，我们需要为每个历史记录表添加四个列，如前面脚本片段中清单 10-23 中用 `//*` 标记的列所示。表 10-2 展示了这些列及其用途。

表 10-2

Envers 审计列详情

| 列名 | 数据类型 | 描述 |
| --- | --- | --- |
| `AUDIT_REVISION` | `INT` | 历史记录的开始修订版本。 |
| `ACTION_TYPE` | `INT` | 操作类型，可能的值有：`0` 表示添加，`1` 表示修改，`2` 表示删除。 |
| `AUDIT_REVISION_END` | `INT` | 历史记录的结束修订版本。 |
| `AUDIT_REVISION_END_TS` | `TIMESTAMP` | 结束修订版本被更新的时间戳。 |

Hibernate Envers 需要另一个表来跟踪修订版本号以及每次为插入、添加、更新或删除操作创建修订版本时的时间戳。该表应命名为 `REVINFO`，并且需要向 `SINGER_AUDIT_H` 表添加一个外键，以将每个审计记录链接到其版本。其模式非常简单，如清单 10-24 所示。

```
CREATE TABLE REVINFO (
REVTSTMP BIGINT NOT NULL
, REV INT NOT NULL AUTO_INCREMENT
, PRIMARY KEY (REVTSTMP, REV)
);
ALTER TABLE SINGER_AUDIT_H
ADD CONSTRAINT SINGER_AUDIT_H_TO_REVISION
FOREIGN KEY ( AUDIT_REVISION ) REFERENCES REVINFO( REV);
Listing 10-24
描述 Hibernate 审计 REVINFO 表的 SQL 代码 (AuditSchema.sql)
```

`REV` 列用于存储每个修订版本号，当创建新的历史记录时，该列会自动递增。`REVTSTMP` 列存储创建修订版本时的时间戳（以数字格式）。

### 为实体版本控制配置 `EntityManagerFactory`

Hibernate Envers 以 EJB 监听器的形式实现。我们可以通过设置一组特定的 Envers 属性，在 `LocalContainerEntityManagerFactory` bean 中配置这些监听器。Java 配置类与迄今为止用于事务性 Spring 应用程序的配置类基本相同，唯一的区别在于，通过调用 `setJpaProperties(..)` 方法，将 Envers 属性添加到设置在 `LocalContainerEntityManagerFactoryBean` 对象上的 `Properties` 对象中。在清单 10-25 中，你可以看到这些属性。

```
package com.apress.prospring6.ten.config;
// 其他导入语句已省略
import org.hibernate.envers.configuration.EnversSettings;
import org.hibernate.cfg.Environment;
@Import(BasicDataSourceCfg.class)
@Configuration
@EnableTransactionManagement
@ComponentScan(basePackages = {"com.apress.prospring6.ten"})
@EnableJpaRepositories("com.apress.prospring6.ten.repos")
public class EnversConfig {
// 其他 bean 声明已省略
@Bean
public Properties jpaProperties() {
Properties jpaProps = new Properties();
jpaProps.put(Environment.HBM2DDL_AUTO, "none");
jpaProps.put(Environment.FORMAT_SQL, false);
jpaProps.put(Environment.STATEMENT_BATCH_SIZE, 30);
jpaProps.put(Environment.USE_SQL_COMMENTS, false);
jpaProps.put(Environment.SHOW_SQL, false);
// Hibernate Envers 的属性
jpaProps.put(EnversSettings.AUDIT_TABLE_SUFFIX, "_H");
jpaProps.put(EnversSettings.REVISION_FIELD_NAME, "AUDIT_REVISION");
jpaProps.put(EnversSettings.REVISION_TYPE_FIELD_NAME, "ACTION_TYPE");
jpaProps.put(EnversSettings.AUDIT_STRATEGY, "org.hibernate.envers.strategy.ValidityAuditStrategy");
jpaProps.put(EnversSettings.AUDIT_STRATEGY_VALIDITY_END_REV_FIELD_NAME, "AUDIT_REVISION_END");
jpaProps.put(EnversSettings.AUDIT_STRATEGY_VALIDITY_STORE_REVEND_TIMESTAMP, "true");
jpaProps.put(EnversSettings.AUDIT_STRATEGY_VALIDITY_REVEND_TIMESTAMP_FIELD_NAME, "AUDIT_REVISION_END_TS");
return jpaProps;
}
@Bean
public LocalContainerEntityManagerFactoryBean entityManagerFactory() {
var factory = new LocalContainerEntityManagerFactoryBean();
factory.setPersistenceProviderClass(HibernatePersistenceProvider.class);
factory.setPackagesToScan("com.apress.prospring6.ten.entities");
factory.setDataSource(dataSource);
factory.setJpaProperties(jpaProperties());
factory.setJpaVendorAdapter(jpaVendorAdapter());
return factory;
}
}
Listing 10-25
需要在 LocalContainerEntityManagerFactoryBean 上设置的 Envers 属性
```

通过此配置，底层发生的情况是，审计事件监听器（实现 `org.hibernate.envers.event.spi.EnversListener` 类型的实例）被附加到持久化事件上：添加、插入、更新和删除。这些监听器会拦截插入后、更新后或删除后的事件，并将实体类的更新前快照克隆到历史记录表中。这些监听器还会附加到那些关联更新事件（集合更新前、集合移除前和集合重建前）上，以处理实体类关联的更新操作。Envers 能够保留关联内实体的历史记录（例如，*一对多* 或 *多对多*）。

这些属性在清单 10-25 的配置中设置，并在表 10-3 中进行了简要说明。

![](img/315511_6_En_10_Fige_HTML.jpg)一个用于突出显示内容的图标。 所有常量都在 `org.hibernate.envers.configuration.EnversSettings` 接口中声明。表 10-3 中未显示接口名称，因为那样会使表格难以在页面中容纳^(⁹¹)。



![](img/315511_6_En_10_Figf_HTML.jpg)一个用于突出显示要点的图标。 第二列中的所有属性名称均以 `org.hibernate.envers.` 开头。表中未显示此前缀，因为若显示则很难将其容纳在页面内。

表 10-3

Envers 配置

| 常量 | 属性名称 | 默认值 | 描述 |
| --- | --- | --- | --- |
| `AUDIT_TABLE_SUFFIX` | `audit_table_prefix` | `_AUD` | 版本化实体的表名后缀。例如，对于映射到 `SINGER_AUDIT` 表的实体类 `SingerAudit`，由于我们为该属性定义了值 `_H`，Envers 会将历史记录保存在 `SINGER_AUDIT_H` 表中。 |
| `REVISION_FIELD_NAME` | `revision_field_name` | `REV` | 历史表中用于存储每条历史记录修订号的列。 |
| `REVISION_TYPE_FIELD_NAME` | `revision_type_field_name` | `REVTYPE` | 历史表中用于存储更新操作类型的列。 |
| `AUDIT_STRATEGY` | `audit_strategy` | `org.hibernate.envers.strategy. DefaultAuditStrategy` | 用于实体版本化的审计策略。 |
| `AUDIT_STRATEGY_VALIDITY_ END_REV_FIELD_NAME` | `audit_strategy_validity_ end_rev_field_name` | `REVEND` | 历史表中用于存储每条历史记录结束修订号的列。仅在使用有效性审计策略时需要。 |
| `AUDIT_STRATEGY_VALIDITY_ STORE_REVEND_TIMESTAMP` | `audit_strategy_validity_ store_revend_timestamp` | `false` | 指定是否在更新每条历史记录的结束修订号时存储时间戳。仅在使用有效性审计策略时需要。 |
| `AUDIT_STRATEGY_VALIDITY_ REVEND_TIMESTAMP_FIELD_NAME` | `audit_strategy_validity_ revend_timestamp_field_name` | `REVEND_TSTMP` | 历史表中用于存储每条历史记录结束修订号更新时的时间戳的列。仅在使用有效性审计策略且上一属性设置为 `true` 时需要。 |

![](img/315511_6_En_10_Figg_HTML.jpg)一个感叹号图标。 表 10-3 中的默认值是 Hibernate 在配置为生成数据库架构时使用的默认值，并且未对这些属性显式配置任何值。

![](img/315511_6_En_10_Figh_HTML.jpg)一个灯泡剪贴画。 你可以在 Hibernate 官方文档^(⁹²)中找到 Hibernate 属性的完整列表。

### 启用实体版本化与历史记录检索

要启用实体的版本化，只需使用 Hibernate 提供的 `@Audited` 注解标注实体类即可。此注解可在类级别使用，之后所有实体字段的更改都将被审计。如果希望某些字段免于审计，可以在这些字段上使用 Hibernate 的 `@NotAudited` 注解。清单 10-26 展示了使用 `@Audited` 和其他 Jakarta 特定注解标注的 `SingerAudit` 类。

```
package com.apress.prospring6.ten.entities;
// 其他导入语句已省略
import org.hibernate.envers.Audited;
@Entity
@Audited
@Table(name = "SINGER_AUDIT")
public class SingerAudit extends AuditableEntity {
// 主体已省略
}
清单 10-26
使用 Hibernate Envers @Audited 注解的实体类
```

实体类使用 `@Audited` 注解标注，Envers 监听器将检查该注解并对更新后的实体执行版本化。默认情况下，`Envers` 也会尝试保留关联关系的历史记录；如果希望避免这种情况，应使用 `@NotAudited` 注解。

要检索历史记录，Envers 提供了 `org.hibernate.envers.AuditReader` 接口，该接口可通过 `AuditReaderFactory` 类获取。为此，在 `SingerAuditService` 接口中添加了一个名为 `findAuditByRevision()` 的新方法，用于根据审计记录的 `id` 和 `revision` 编号检索 `SingerAudit` 历史记录。清单 10-27 展示了 `SingerAuditServiceImpl` 的实现。

```
package com.apress.prospring6.ten.service;
import jakarta.persistence.PersistenceContext;
import org.hibernate.envers.AuditReaderFactory;
@Service("singerAuditService")
@Transactional
public class SingerAuditServiceImpl implements SingerAuditService {
private final SingerAuditRepository singerAuditRepository;
@PersistenceContext
private EntityManager entityManager;
// 其他方法和字段已省略
@Override
public SingerAudit findAuditByRevision(Long id, int revision) {
var auditReader = AuditReaderFactory.get(entityManager);
return auditReader.find(SingerAudit.class, id, revision);
}
}
清单 10-27
用于按修订号检索历史记录的服务方法
```

显然，`SingerAuditServiceImpl` 类必须进行修改，并且必须将 `EntityManager` 注入其中，以便 `AuditReaderFactory` 能够使用它创建 `org.hibernate.envers.AuditReader` 实例来读取审计记录。

通过为 Spring Data 自定义仓库使用自定义实现，可以轻松避免这种情况，并且由于这是 Spring 的重点，我们就采用这种方式。



### Spring Data 仓库的自定义实现

Spring Data 提供了默认选项，只需少量编码即可处理数据库记录和创建查询方法。然而，在某些情况下这还不够，这里介绍的 Envers 示例就是一个很好的例子。

要为 Spring Data 仓库的默认功能添加自定义方法，我们需要定义一个片段接口，声明我们想要添加的行为。在我们的案例中，如清单 10-28 所示，该接口被命名为 `CustomSingerAuditRepository`，并声明了一个单一方法，其唯一目的是使用 `SingerAudit` 记录的 `id` 和修订号来检索其旧版本。

```
package com.apress.prospring6.ten.repos.envers;
import com.apress.prospring6.ten.entities.SingerAudit;
import java.util.Optional;
public interface CustomSingerAuditRepository {
Optional findAuditByIdAndRevision(Long id, int revision);
}
清单 10-28
声明用于检索 SingerAudit 记录版本的方法的片段接口
```

现在我们有了一个接口，就需要提供一个实现。这就是清单 10-29 中描述的 `CustomSingerAuditRepositoryImpl` 类发挥作用的地方。

```
package com.apress.prospring6.ten.repos.envers;
import com.apress.prospring6.ten.entities.SingerAudit;
import jakarta.persistence.EntityManager;
import jakarta.persistence.PersistenceContext;
import org.hibernate.envers.AuditReaderFactory;
import java.util.Optional;
public class CustomSingerAuditRepositoryImpl implements CustomSingerAuditRepository {
@PersistenceContext
private EntityManager entityManager;
@Override
public Optional findAuditByIdAndRevision(Long id, int revision) {
var auditReader = AuditReaderFactory.get(entityManager);
var result =  auditReader.find(SingerAudit.class, id, revision);
if(result != null)
return Optional.of(result);
return Optional.empty();
}
}
清单 10-29
CustomSingerAuditRepository 接口的实现
```

![](img/315511_6_En_10_Figi_HTML.jpg)信息图标。 请注意，我们只是将 Envers 特定的代码从 `SingerAuditServiceImpl` 移到了这个类中。

为了告诉 Spring Data 我们希望将此方法添加到其默认功能集中，我们需要让 `SingerAudit` 实体的 Spring Data 仓库也扩展 `CustomSingerAuditRepository`，如清单 10-30 所示。

```
package com.apress.prospring6.ten.repos;
import com.apress.prospring6.ten.entities.SingerAudit;
import com.apress.prospring6.ten.repos.envers.CustomSingerAuditRepository;
import org.springframework.data.jpa.repository.JpaRepository;
public interface SingerAuditRepository extends
JpaRepository ,
CustomSingerAuditRepository { }
清单 10-30
CustomSingerAuditRepository 接口的实现
```

![](img/315511_6_En_10_Figj_HTML.jpg)感叹号图标。 与片段接口对应的类名中最重要的部分是 `Impl` 后缀。这允许 Spring Data 识别该方法的实现来自何处，并将其添加到其 JPA 仓库代理中。毕竟，约定优于配置是 Spring 框架的核心价值之一。

![](img/315511_6_En_10_Figk_HTML.jpg)信息图标。 然而，易于定制也是 Spring 的一个价值，因此如果你想以不同的方式命名你的自定义仓库实现，你必须告诉 Spring。定制有些局限；你只能指定一个不同的后缀来替换默认的 `Impl`，使用 `@EnableJpaRepositories(repositoryImplementationPostfix = "${custom-prefix}")`，但这总比没有好。

这意味着现在 `SingerAuditServiceImpl` 类可以清理掉 Envers 特定的代码，并且 `findAuditByRevision(..)` 方法只需调用 Spring JPA `SingerAuditRepository` 提供的 `findAuditByIdAndRevision(..)` 方法，如清单 10-31 所示。

```
package com.apress.prospring6.ten.service;
@Service("singerAuditService")
@Transactional
public class SingerAuditServiceImpl implements SingerAuditService {
private final SingerAuditRepository singerAuditRepository;
// 其他方法已省略
@Transactional(readOnly=true)
@Override
public SingerAudit findAuditByRevision(Long id, int revision) {
return singerAuditRepository.findAuditByIdAndRevision(id, revision).orElse(null);
}
}
清单 10-31
SingerAuditServiceImpl 接口的实现
```

剩下的就是测试这个功能是否有效。编写测试方法并不难，但为了确保我们的版本控制正常工作，应该检查更新和删除操作。测试类如清单 10-32 所示。

```
package com.apress.prospring6.ten;
// 导入语句已省略
@Testcontainers
@Sql({ "classpath:testcontainers/audit/drop-schema.sql", "classpath:testcontainers/audit/create-schema.sql" })
@SpringJUnitConfig(classes = {EnversConfig.class, AuditCfg.class})
public class EnversServiceTest extends TestContainersBase {
private static final Logger LOGGER = LoggerFactory.getLogger(EnversServiceTest.class);
@Autowired
SingerAuditService auditService;
@BeforeEach
void setUp(){
var singer = new SingerAudit();
singer.setFirstName("BB");
singer.setLastName("King");
singer.setBirthDate(LocalDate.of(1940, 8, 16));
auditService.save(singer);
}
@Test
void testFindAuditByRevision() {
// 更新以创建新版本
var singer = auditService.findAll().findFirst().orElse(null);
assertNotNull(singer);
singer.setFirstName("Riley B.");
auditService.save(singer);
var oldSinger = auditService.findAuditByRevision(singer.getId(), 1);
assertEquals("BB", oldSinger.getFirstName());
LOGGER.info(">> 旧歌手: {} ", oldSinger);
var newSinger = auditService.findAuditByRevision(singer.getId(), 2);
assertEquals("Riley B.", newSinger.getFirstName());
LOGGER.info(">> 更新后的歌手: {} ", newSinger);
}
@Test
void testFindAuditAfterDeletion() {
// 删除记录
var singer = auditService.findAll().findFirst().orElse(null);
auditService.delete(singer.getId());
// 从审计中提取
var deletedSinger = auditService.findAuditByRevision(singer.getId(), 1);
assertEquals("BB", deletedSinger.getFirstName());
LOGGER.info(">> 已删除的歌手: {} ", deletedSinger);
}
}
清单 10-32
在 Spring 应用程序中测试 Envers 审计
```

为了了解发生了什么，你可以尝试查看测试方法执行时打印的控制台日志消息。不幸的是，在本书的这个阶段，如果我们为 `org.hibernate` 启用 `TRACE` 日志，日志会变得不可读，但清单 10-33 显示了最重要的部分。



```
TRACE: TransactionAspectSupport - Getting transaction for
[com.apress.prospring6.ten.service.SingerAuditServiceImpl.save]
TRACE: AbstractEntityPersister - Updating entity:
[com.apress.prospring6.ten.entities.SingerAudit#1]
...
DEBUG: SqlStatementLogger - insert into REVINFO (REVTSTMP) values (?)
TRACE: AbstractEntityPersister - Inserting entity:
[com.apress.prospring6.ten.entities.SingerAudit_H#component[
id,AUDIT_REVISION]{id=1, AUDIT_REVISION=org.hibernate.envers.DefaultRevisionEntity#1}]
DEBUG: SqlStatementLogger - insert into REVINFO (REVTSTMP) values (?)
TRACE: AbstractEntityPersister - Inserting entity:
[com.apress.prospring6.ten.entities.SingerAudit_H#component[
id,AUDIT_REVISION]{id=1, AUDIT_REVISION=org.hibernate.envers.DefaultRevisionEntity#2}]
...
TRACE: TransactionAspectSupport - Getting transaction for
[com.apress.prospring6.ten.service.SingerAuditServiceImpl.findAuditByRevision]
DEBUG: QueryTranslatorImpl - parse() - HQL:
select e__ from com.apress.prospring6.ten.entities.SingerAudit_H e__
where e__.originalId.AUDIT_REVISION.id  :_p0
and e__.originalId.id = :_p1
and (e__.AUDIT_REVISION_END.id > :revision or e__.AUDIT_REVISION_END is null)
TRACE: QueryParameters - Named parameters: {_p1=1, _p0=DEL, revision=1}
DEBUG: Loader - Result row: EntityKey[com.apress.prospring6.ten.entities.SingerAudit_H#component[id,AUDIT_REVISION]{id=1, AUDIT_REVISION=org.hibernate.envers.DefaultRevisionEntity#1}]
INFO : EnversServiceTest - >> old singer: com.apress.prospring6.ten.entities.SingerAudit@20524816[
id=1,version=0,firstName=BB,lastName=King,birthDate=1940-08-16,
createdBy=prospring6-0e94fe,
createdDate=2022-08-07T13:40:31,
lastModifiedBy=prospring6-0e94fe,
lastModifiedDate=2022-08-07T14:40:30
]
...
TRACE: QueryParameters - Named parameters: {_p1=1, _p0=DEL, revision=2}
DEBUG: Loader - Result row: EntityKey[com.apress.prospring6.ten.entities.SingerAudit_H#component[id,AUDIT_REVISION]{id=1, AUDIT_REVISION=org.hibernate.envers.DefaultRevisionEntity#2}]
INFO : EnversServiceTest - >> updated singer: com.apress.prospring6.ten.entities.SingerAudit@6347f9cc[
id=1,version=0,firstName=Riley B.,lastName=King,birthDate=1940-08-16,
createdBy=prospring6-0e94fe,
createdDate=2022-08-07T14:40:30,
lastModifiedBy=prospring6-TJFUWY,
lastModifiedDate=2022-08-07T14:40:31
]
...
清单 10-33
Envers 审计测试执行日志
```

请注意，对 `SINGER_AUDIT` 表的每次操作都会导致在 `REVINFO` 和 `SINGER_AUDIT_H` 中创建一条记录。然后，当按特定修订版本提取 `SingerAudit` 条目时，会从 `SINGER_AUDIT_H` 表中提取。更新操作后，`SingerAudit` 的名字被改为 *Riley B*。正如预期，查看历史记录时，在修订版本 1 中，名字是 *BB*。在修订版本 2 中，名字变为 *Riley B.*。另请注意，修订版本 2 的 `lastModifiedDate` 日期和 `lastModifiedBy` 反映了更新的日期和时间，以及由 `AuditAware<String>` bean 生成的不同用户名。

## Spring Boot Data JPA

到目前为止，我们已经显式配置了所有内容，包括实体、数据库、仓库和服务。正如您现在可能预料到的，应该有一个 Spring Boot 启动器工件来简化操作。Spring Boot JDBC 在**第** **9** **章**中介绍过，但其功能在与 Spring Data 结合使用时才真正大放异彩，并且有一个名为 `spring-boot-starter-data-jpa` 的启动器库。将此库添加到项目类路径中，会以传递方式添加 Spring 事务应用程序所需的所有依赖项：用于持久化的 `hibernate-core`、用于连接池的 `HikariCP`、用于使用数据仓库接口进行数据访问的 `spring-data-jpa`，以及用于事务管理的 `spring-tx`。项目依赖项如图 10-6 所示。

![](img/315511_6_En_10_Fig6_HTML.jpg)

一张截图展示了第 10 章启动项目的依赖项。

图 10-6

显示 `chapter10-boot` 项目依赖项的 Gradle 视图

Spring Boot Data JPA 配置包含两部分：带有 `@SpringBootApplication` 注解的主类和 `application-dev.yaml`（或 `application-dev.properties`）文件。当然，添加 `-dev` 后缀是因为我们使用了名为 `dev` 的应用配置文件。这是一种根据上下文隔离 bean 声明的实用方法。

对于此项目，`application-dev.yaml` 文件的内容如清单 10-34 所示。

```
# datasource config
spring:
datasource:
driverClassName: org.mariadb.jdbc.Driver
url: jdbc:mariadb://localhost:3306/musicdb?useSSL=false
username: prospring6
password: prospring6
# HikariCP
hikari:
maximum-pool-size: 25
# JPA config
jpa:
generate-ddl: false
properties:
hibernate:
naming:
physical-strategy:
jdbc:
batch_size: 10
fetch_size: 30
max_fetch_depth: 3
show-sql: true
format-sql: false
use_sql_comments: false
hbm2ddl:
auto: none
# Logging config
logging:
pattern:
console: "%-5level: %class{0} - %msg%n"
level:
root: INFO
org.springframework.boot: DEBUG
com.apress.prospring6.ten.boot: INFO
org.springframework.orm.jpa: TRACE
清单 10-34
Spring Boot Data JPA 配置 yaml 配置
```

配置分为三个部分：`datasource`、`jpa` 和 `logging`。以 `spring.datasource` 开头的属性用于设置配置数据源所需的值：连接 URL、凭据和连接池值。以 `spring.jpa` 开头的属性用于设置描述持久化单元的值。您可以轻松识别出**第** **7** **章**和**第** **8** **章**中介绍的 Hibernate 属性。以 `logging` 开头的属性用于设置日志配置的典型值，在经典应用程序中，这些值可以在 `logback.xml` 文件中找到。

在 Spring Boot Data JPA 应用程序中，自动配置机制会根据配置文件中的属性负责声明必要的 bean，例如 `DataSource`、`EntityManager`、`LocalSessionFactoryBean`、`TransactionManager` 等，但我们仍然需要配置一些内容：

*   *实体类的位置*：由于 `LocalSessionFactoryBean` 是自动配置的，指定实体类所在包的方式是使用 `org.springframework.boot.autoconfigure.domain` 包中的 `@EntityScan` 注解。

*   *数据仓库接口的位置*：这很容易实现，只需将 `@EnableJpaRepositories` 注解添加到带有 `@Configuration` 注解的类（如果有）上，或者如果没有这样的类，则添加到带有 `@SpringBootApplication` 注解的类上。

*   *事务行为*：这也很容易实现，只需将 `@EnableTransactionManagement` 注解添加到带有 `@Configuration` 注解的类（如果有）上，或者如果没有这样的类，则添加到带有 `@SpringBootApplication` 注解的类上。



本节分析的项目配置如清单 10-35 所示。

```
package com.apress.prospring6.ten.boot;
import com.apress.prospring6.ten.boot.service.SingerService;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.autoconfigure.domain.EntityScan;
import org.springframework.core.env.AbstractEnvironment;
import org.springframework.data.jpa.repository.config.EnableJpaRepositories;
import org.springframework.transaction.annotation.EnableTransactionManagement;
@EntityScan(basePackages = {"com.apress.prospring6.ten.boot.entities"})
@EnableTransactionManagement
@EnableJpaRepositories("com.apress.prospring6.ten.boot.repos")
@SpringBootApplication
public class Chapter10Application {
private static final Logger LOGGER = LoggerFactory.getLogger(Chapter10Application.class);
public static void main(String... args) {
System.setProperty(AbstractEnvironment.ACTIVE_PROFILES_PROPERTY_NAME, "dev");
var ctx = SpringApplication.run(Chapter10Application.class, args);
var service = ctx.getBean(SingerService.class);
LOGGER.info(" ---- 列出歌手:");
service.findAll().forEach(s -> LOGGER.info(s.toString()));
}
}
清单 10-35
Spring Boot Data JPA 注解配置
```

如您所见，Spring Boot 主配置类包含一个 `main(..)` 方法。这使得该类可执行，运行该类 `main(..)` 方法中的代码可确保配置正确。然而，使用当前配置运行该类会以如下错误结束：

```
Exception in thread "main" org.springframework.dao.InvalidDataAccessResourceUsageException:
JDBC exception executing SQL
[select s1_0.id,
s1_0.birth_date,
s1_0.first_name,
s1_0.last_name,
s1_0.version
from singer s1_0
]; SQL [n/a]
...
Caused by: java.sql.SQLSyntaxErrorException: (conn=302) Table 'musicdb.singer' doesn't exist
at org.mariadb.jdbc.export.ExceptionFactory.createException(ExceptionFactory.java:280)
at org.mariadb.jdbc.export.ExceptionFactory.create(ExceptionFactory.java:368)
```

那么，这是怎么回事呢？如果数据源配置正确，为什么找不到 `singer` 表？如果您认为原因是表名大小写敏感，那您说对了一部分。在解释这个 Spring Boot Data JPA 应用程序中发生了什么之前，我们先来谈谈 Hibernate 的命名策略。

Hibernate 使用两种不同的命名策略^(⁹³)将名称从对象模型映射到相应的数据库名称：

*   `ImplicitNamingStrategy`：正确的逻辑名称由领域模型映射决定，要么通过配置注解（如 `@Column` 和 `@Table`，如您在前几章中所见），要么由 Hibernate 使用通过 `Environment.IMPLICIT_NAMING_STRATEGY / hibernate.implicit_naming_strategy` 属性配置的 `org.hibernate.boot.model.naming.ImplicitNamingStrategy` 实现来确定。

*   `PhysicalNamingStrategy`：正确的逻辑名称由 Hibernate 使用通过 `Environment.PHYSICAL_NAMING_STRATEGY / hibernate.physical_naming_strategy` 属性配置的 `org.hibernate.boot.model.naming.PhysicalNamingStrategy` 实现来确定。

这两种策略的目的是减少开发人员必须提供的显式配置。

许多组织围绕数据库对象（表、列、外键等）的命名制定了规则。`PhysicalNamingStrategy` 的理念是帮助实现此类命名规则，而无需通过显式名称将其硬编码到映射中。

我曾（Iuliana）工作过的几家公司更喜欢用大写字母命名数据库对象，因为在代码中更容易区分 HQL 和原生 SQL 查询。这也是我在本书中采用的方法。

在 Spring Boot Data JPA 应用程序中，确定数据库对象名称的默认策略是 Hibernate 提供的名为 `org.hibernate.boot.model.naming.CamelCaseToUnderscoresNamingStrategy` 的 `PhysicalNamingStrategy` 实现。顾名思义，逻辑名称假定为驼峰式并用下划线分隔。这就是 `Singer` 实体类被映射到 `singer` 表的原因，并且由于 MariaDB 安装在 Linux 容器上，数据库对象名称是大小写敏感的，因此 `SINGER` 显然不等于 `singer`。

这里的解决方案是提供我们自己的 `PhysicalNamingStrategy` bean，指示 Spring Boot 如何确定数据库对象名称。`PhysicalNamingStrategy` 接口为每个命名的数据库对象声明了五个方法^(⁹⁴)。该接口的主体在清单 10-36 中声明。

```
package org.hibernate.boot.model.naming;
import org.hibernate.Incubating;
import org.hibernate.engine.jdbc.env.spi.JdbcEnvironment;
@Incubating
public interface PhysicalNamingStrategy {
Identifier toPhysicalCatalogName(Identifier logicalName, JdbcEnvironment jdbcEnvironment);
Identifier toPhysicalSchemaName(Identifier logicalName, JdbcEnvironment jdbcEnvironment);
Identifier toPhysicalTableName(Identifier logicalName, JdbcEnvironment jdbcEnvironment);
Identifier toPhysicalSequenceName(Identifier logicalName, JdbcEnvironment jdbcEnvironment);
Identifier toPhysicalColumnName(Identifier logicalName, JdbcEnvironment jdbcEnvironment);
}
清单 10-36
Hibernate 的 PhysicalNamingStrategy 接口
```

![](img/315511_6_En_10_Figl_HTML.jpg)信息图标。 `@Incubating` 注解表示某些内容仍在积极开发中，因此可能会在以后发生变化。

为了简化操作，我们可以扩展 Hibernate 提供的 `PhysicalNamingStrategyImpl` 类，并仅覆盖我们感兴趣的方法。清单 10-37 中显示的 `HibernateCfg` 配置类声明了一个匿名类型的 bean，该类型扩展了 `PhysicalNamingStrategyImpl`。

```
package com.apress.prospring6.ten.boot;
import org.hibernate.boot.model.naming.Identifier;
import org.hibernate.boot.model.naming.PhysicalNamingStrategyStandardImpl;
import org.hibernate.engine.jdbc.env.spi.JdbcEnvironment;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
@Configuration(proxyBeanMethods = false)
public class HibernateCfg {
@Bean
PhysicalNamingStrategyStandardImpl caseSensitivePhysicalNamingStrategy() {
return new PhysicalNamingStrategyStandardImpl() {
@Override
public Identifier toPhysicalTableName(Identifier logicalName, JdbcEnvironment context) {
return apply(logicalName, context);
}
@Override
public Identifier toPhysicalColumnName(Identifier logicalName, JdbcEnvironment context) {
return apply(logicalName, context);
}
private Identifier apply(final Identifier name, final JdbcEnvironment context) {
if ( name == null ) {
return null;
}
StringBuilder builder = new StringBuilder( name.getText().replace( '.', '_' ) );
for ( int i = 1; i < builder.length() - 1; i++ ) {
if ( isUnderscoreRequired( builder.charAt( i - 1 ), builder.charAt( i ), builder.charAt( i + 1 ) ) ) {
builder.insert( i++, '_' );
}
}
return Identifier.toIdentifier(builder.toString().toUpperCase());
}
private boolean isUnderscoreRequired(final char before, final char current, final char after) {
return Character.isLowerCase( before ) && Character.isUpperCase( current ) && Character.isLowerCase( after );
}
};
}
}
清单 10-37
自定义 PhysicalNamingStrategy 实现
```

`proxyBeanMethods` 属性设置为 `false`，因为此类中带有 `@Bean` 注解的方法不应被代理以强制执行 bean 生命周期行为。用于确定表和列逻辑名称的代码在 `apply()` 方法中声明。



使用这个新的配置类执行 `Chapter10Application` 类，我们可以看到预期的输出（即歌手实例的字符串形式）被打印在控制台日志中。

在经典的 Spring 应用程序中，仓库数据类和服务类与本章节展示的相同。测试方法也相同，唯一的区别在于测试上下文的配置。建议使用 `application-test.yaml` 或 `application-test.properties` 文件，因为数据源需要替换为 Testcontainers 数据源。本节示例中使用的配置文件如代码清单 10-38 所示。

```
spring:
datasource:
url: "jdbc:tc:mariadb:10.9-rc:///testdb?TC_INITSCRIPT=testcontainers/create-schema.sql"
jpa:
properties:
hibernate:
jdbc:
batch_size: 10
fetch_size: 30
max_fetch_depth: 3
show-sql: true
format-sql: true
use_sql_comments: true
hbm2ddl:
auto: none
# 日志配置
logging:
pattern:
console: " %-5level: %class{0} - %msg%n"
level:
root: INFO
org.springframework.boot: DEBUG
com.apress.prospring6.ten.boot: DEBUG
org.springframework.orm.jpa: TRACE
代码清单 10-38
Spring Boot Data JPA 测试配置 yaml 配置
```

请注意，用于调试的 Hibernate 特定属性被设置为 `true`，并且日志配置更加详细。

Spring Boot 测试配置类使用 `@SpringBootTest` 注解以及设置测试上下文所需的其他注解，如代码清单 10-39 所示。

```
package com.apress.prospring6.ten.boot;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.ActiveProfiles;
import org.springframework.test.context.jdbc.Sql;
import org.springframework.test.context.jdbc.SqlMergeMode;
import org.testcontainers.junit.jupiter.Testcontainers;
// 其他 import 语句已省略
@ActiveProfiles("test")
@Testcontainers
@SqlMergeMode(SqlMergeMode.MergeMode.MERGE)
@Sql({ "classpath:testcontainers/drop-schema.sql", "classpath:testcontainers/create-schema.sql" })
@SpringBootTest(classes = {Chapter10Application.class})
public class Chapter10ApplicationTest {
private static final Logger LOGGER = LoggerFactory.getLogger(Chapter10Application.class);
@Autowired
SingerService singerService;
@Test
public void testFindAll(){
var singers = singerService.findAll().peek(s -> LOGGER.info(s.toString())).toList();
assertEquals(3, singers.size());
}
// 其他测试方法已省略
}
代码清单 10-39
Spring Boot 数据测试类
```

告诉 Spring Boot 基于 `application-test.yaml` 中的配置初始化测试上下文的是 `@ActiveProfiles("test")` 注解，它启用了 `test` 上下文。`@SpringBootTest(classes = {Chapter10Application.class})` 注解被显式配置为使用 `Chapter10Application` 配置。当只有一个 Spring Boot 测试类，且其名称恰好与 Spring Boot 配置类名称加上 `Test` 后缀相同时，这不是必需的，但在本书的示例中，使用这种形式是为了更清晰，明确说明被测试 Bean 的配置来源。

### 使用 Spring Data JPA 的注意事项

使用 Spring Data JPA 的 Repository 抽象有助于简化 JPA 应用程序的开发。Spring Boot 可以进一步简化开发，但自定义配置可能需要一些学习。再次引用**第** **6** **章**中介绍的《我爱露西》剧集“终于到巴黎”，Spring Data JPA 是链条中的最后一个翻译器。它的工作基于其前的 JDBC 驱动、JPA 和 Hibernate（以及类似的，如 Oracle TopLink），它使得编写 Java 代码访问数据变得最容易，因为它专注于业务逻辑，并隐藏了技术复杂性和样板代码。所有这些技术层共同使得实现持久层变得相当容易和快速。Spring Data JPA 也使得自定义事务行为变得容易。Spring Data（及其所依赖的框架）非常流行，适用于大型企业项目；对于较小的项目，可能有些大材小用。此外，项目中添加的技术越多，开发人员的学习曲线就越陡峭，并且在技术粘合点处增加的故障点也越多。

到目前为止，本章介绍了 Spring Data JPA 仓库的核心概念和接口。JPA 适用于处理规范化的 SQL 数据库。那么，当处理非结构化、非强关系型且不适合 SQL 数据库的数据时，该怎么办呢？Spring Data 是否为此提供了解决方案？当然！您可以在下一节中了解相关内容。



## 使用 MongoDB 的 Spring Data^(⁹⁵)

几乎所有数据在某种程度上都是关系型的，多年来，通过外键等具体对象来表示这些关系的 SQL 数据库一直主导着行业。然而，为了减少数据重复和存储成本而组织数据，在处理这些数据时会引入延迟，因为必须考虑关系。随着数据量的增长，描述关系的元数据量也在增长，操作会变得更慢。关系型数据库非常适合完成那些涉及大量表、复杂且庞大的查询，并且事务性操作至关重要（ACID）而速度并非总是首要需求的任务。在执行大型级联删除的同时保持关系和数据完整性，也是关系型数据库的另一个良好用例。

在这个互联网新时代，速度是最常见的要求。SQL 数据库虽然在存储数据方面高效，并且在请求时提供数据非常精确和一致，但并不适用于速度比数据完整性更重要的互联网服务。当你的数据结构化程度不高、关系不深、需要短时间保存数据（缓存）或查询不太复杂时，NoSQL 数据库就很有用。例如，eBay 正在使用名为 MongoDB 的 NoSQL 数据库，而亚马逊正在使用 DynamoDB，这是一个为满足其自身商业需求而创建的专有 NoSQL 数据库。DynamoDB 被认为是购物网站最合适的 NoSQL 数据库，因为它在亚马逊云中高度可定制、快速且可无限扩展。

因此，NoSQL 数据库的出现是为了满足快速获取数据的需求，即使数据并不总是一致。例如，当你访问亚马逊购物网站时，你并不期望立即获得最新的产品，但你最终会得到它们。此外，由于是非关系型的，NoSQL 数据库存在一些数据重复，因此数据完整性次于检索速度。

你需要记住的另一件事是，关系型数据库并不是真实系统的很好模型。它们迫使你在开始编写与它们交互的代码之前就声明完整的数据结构，并且之后进行修改很棘手，这是一个问题，因为在现实生活中变化是不断发生的。NoSQL 的主要优势在于，除了每条记录的主标识符外，所有内容都可以在以后轻松修改。亚马逊的 DynamoDB 在这方面更进一步，因为除了主键之外，记录中的所有列（称为*属性*）都可以因记录而异。不幸的是，这种灵活性水平意味着编写用于处理 DynamoDB 的 Spring Data 库几乎是不可能的。

由于 NoSQL 数据库已被广泛使用，因此添加一个关于 Spring 如何帮助与它们交互的章节是合适的。Spring 提供了相当多的用于处理 NoSQL 数据库的数据库，但 Spring 团队最喜欢的是 MongoDB^(⁹⁶)，它提供了经典 API 和响应式 API。除此之外，还支持 Apache Cassandra^(⁹⁷)（一个开源 NoSQL 数据库管理系统，能够处理海量数据，并且速度也很快）、Couchbase^(⁹⁸)（一个现代云数据库，在高度可扩展和可用的平台上为关键业务应用提供强大的功能）和 Redis^(⁹⁹)（一个开源、BSD 许可的内存数据结构存储，用作数据库、缓存和消息代理）。

最近，由于已经完全非关系型的数据并不存在，NoSQL 这个术语的含义正逐渐从“非 SQL”转变为“不仅仅是 SQL”。因此，请随意探索 NoSQL 数据领域，因为你很可能最终会使用其中一种数据库。

### MongoDB 概念

对于本节，你可以在计算机上安装 MongoDB，但推荐的方法是使用 Docker，并按照 `chapter10-mongo` 项目中 `CHAPTER10-MONGO.adoc` 文件中的建议设置容器。当然，你也可以完全跳过这一步，直接运行使用 Testcontainers MongoDB 容器的测试。

MongoDB 是一个面向文档的 NoSQL 数据库，用于高容量数据存储。与传统的使用表和行的关系型数据库不同，MongoDB 使用集合和文档。集合中的文档不像表中的所有行那样具有固定的结构。

虽然我们已经提到了 NoSQL 数据库的适用场景，但为了保持一致性，`Singer` 类被映射到一个名为 `singers` 的 MongoDB 集合。在 Spring 中，这是通过使用来自 `org.springframework.data.mongodb.core.mapping` 包的 `@Document` 注解标注 `Singer` 类来完成的。这个注解对于配置与类名不同的集合名称很有用，因为 MongoDB 的标准是使用集合中文档类型的复数形式来命名集合。

`Singer` 类如清单 10-40 所示。

```
package com.apress.prospring6.ten.document;
import org.springframework.data.mongodb.core.index.Indexed;
import org.springframework.data.mongodb.core.mapping.Document;
import org.springframework.data.mongodb.core.mapping.MongoId;
import java.time.LocalDate;
@Document(collection = "singers")
public class Singer {
@MongoId
public String id;
@Indexed
public String firstName;
@Indexed
public String lastName;
private LocalDate birthDate;
public Singer() {
}
public Singer(String firstName, String lastName, LocalDate birthDate) {
this.firstName = firstName;
this.lastName = lastName;
this.birthDate = birthDate;
}
// getters & setters omitted
}
清单 10-40
配置为 MongoDB 文档的 Singer 类
```

MongoDB 要求所有文档都有一个 `_id` 字段。使用 Spring Data，映射到该字段的字段使用来自 `org.springframework.data.mongodb.core.mapping` 的 `@Id` 或 `@MongoId` 注解标注。`@MongoId` 注解是元注解了 `@Id` 的，虽然在这里使用是为了明确表明此类映射到一个 MongoDB 文档，但它提供了使用 `@MongoId(FieldType.*)` 配置字段类型的可能性。当这样注解时，Spring Data 将尝试将值转换为声明的类型。

`@Indexed` 注解告诉映射框架在文档的该属性上调用 `createIndex(..)`，从而使搜索更快。

如果需要在文档中字段的名称与 Java 类中的名称不同，你可以使用 `@Field` 注解来配置所需的值。

在 Spring 中，类型通过一组内置转换器与 MongoDB 表示形式进行相互映射。这就是为什么我们可以在没有任何转换注解的情况下声明 `LocalDate birthDate` 字段的原因。

现在我们有了一个文档，下一步是创建一个用于处理 `Singer` 实例的仓库。该方法与 Spring Data JPA 中展示的方法类似。在 Spring Data MongoDB 中，有一个 `MongoRepository<T, ID>` 接口，它是来自 `org.springframework.data.repository` 包的 `CrudRepository<T, ID>` 接口的扩展（也有一个响应式版本，但更多内容将在**第** **20** 章中介绍），并且这是我们的 `SingerRepository` 接口必须扩展的接口。`SingerRepository` 接口如清单 10-41 所示。



```
package com.apress.prospring6.ten.repos;
import com.apress.prospring6.ten.document.Singer;
import org.springframework.data.mongodb.repository.MongoRepository;
import org.springframework.data.mongodb.repository.Query;
import org.springframework.data.repository.query.Param;
import java.time.LocalDate;
public interface SingerRepository extends MongoRepository {
Iterable findByFirstName(String firstName);
@Query("{'firstName' : ?0, 'lastName' : ?1}")
Iterable findByPositionedParams(String fn, String ln);
@Query("{'firstName' : :#{#fn}, 'lastName' : :#{#ln}}")
Iterable findByNamedParams(@Param("fn") String fn, @Param("ln") String ln);
}
清单 10-41
SingerRepository MongoDB 数据仓库接口
```

这里介绍的 `SingerRepository` 接口包含两个 MongoDB 自定义查询。在编写 MongoDB 查询表达式时，参数可以按位置指定，也可以像 SQL 查询那样使用命名参数。唯一不同的是查询表达式的语法；MongoDB 查询本质上是 JSON 对象。`findByPositionedParams(..)` 方法使用与参数顺序匹配的索引，`?0` 对应方法中的第一个参数，参数的值将替换 `?0`。这意味着开发者必须跟踪参数位置，不能混淆，因为虽然 MongoDB 不会报错，但也不会返回预期结果。不过，查询表达式相当简单，因此这是推荐的方法。

也可以使用命名参数，通过 `@Param` 注解和 SpEL 表达式来引用参数名称。表达式更复杂且冗长，但这种方法更灵活，并且没有错误定位参数的风险。选择你更习惯的方式即可。

正如我们已经习惯的那样，使用 `SingerService` Bean 来调用仓库方法，其实现较为基础，此处不再赘述。重要的是用于连接 MongoDB 实例并启用 Spring Data MongoDB 仓库支持的 Spring 配置。

要连接 MongoDB 实例，需要一个 `com.mongodb.client.MongoClient` 类型的 Bean。开发者可以直接在 `@Configuration` 类中声明此 Bean，如清单 10-42 所示。

```
package com.apress.prospring6.ten.config;
import com.mongodb.client.MongoClients;
import com.mongodb.client.MongoClient;
@Configuration
public class MongoCfg {
@Bean
public MongoClient mongoClient() {
return MongoClients.create("mongodb://prospring6:prospring6@localhost:27017/musicdb?authSource=admin");
}
}
清单 10-42
MongoClient Bean 声明
```

`MongoClients.create(..)` 静态方法用于根据作为参数提供的连接字符串创建 `MongoClient` 实例。清单中的连接字符串基于以下模板构建：

```
mongodb://[username:password@]host1[:port1][,...hostN[:portN]][/[defaultauthdb][?options]]
```

本节中使用的具体连接字符串包含以下部分：

*   以 `mongodb://` 开头，表示我们使用的驱动程序类型（类似于 MySQL 连接字符串以 `jdbc:` 开头）。
*   `prospring6:prospring6` 表示用于连接 MongoDB 数据库的 `用户名:密码`。
*   `localhost` 表示数据库安装的位置，在本例中为代码执行的同一台机器（或由本地 Docker 运行时管理的容器中）。
*   `27017` 表示连接端口。
*   `musicdb` 是数据库名称，`prospring6` 用户必须对其拥有 `dbOwner` 角色。
*   `authSource=admin` 是一个参数，指向用户数据所在的集合，以便根据该集合检查提供的用户名和密码。

这种创建 `MongoClient` 的方式是最直接的。使用 Spring Data MongoDB 时，推荐的方法是声明一个 Spring `MongoClientFactoryBean`，如清单 10-43 所示。

```
package com.apress.prospring6.ten.config;
import org.springframework.data.mongodb.core.MongoClientFactoryBean;
@Configuration
public class MongoCfg {
@Bean
public MongoClientFactoryBean mongoClientFactory() {
MongoClientFactoryBean mongo = new MongoClientFactoryBean();
mongo.setConnectionString("mongodb://prospring6:prospring6@localhost:27017/musicdb?authSource=admin");
return mongo;
}
}
清单 10-43
MongoClientFactoryBean Bean 声明
```

通过使用 `@EnableMongoRepositories` 注解配置类来启用 MongoDB 数据仓库的支持。如果未配置基础包，基础设施将扫描注解配置类所在的包。

Spring MongoDB 数据仓库使用 `org.springframework.data.mongodb.core.MongoTemplate` Bean 与 MongoDB 实例交换数据，因此也必须创建此 Bean。清单 10-44 显示了完整的配置类，其中连接字符串从 `mongo.properties` 文件中读取。

```
/** === 'mongo.properties' 文件内容 ===
mongo.url=mongodb://prospring6:prospring6@localhost:27017/musicdb?authSource=admin
mongo.db=musicdb
*/
package com.apress.prospring6.ten.config;
import org.springframework.data.mongodb.core.MongoTemplate;
import org.springframework.data.mongodb.core.MongoClientFactoryBean;
@ComponentScan(basePackages = {"com.apress.prospring6.ten.service"})
@EnableMongoRepositories(basePackageClasses = SingerRepository.class)
@Configuration
@PropertySource("classpath:mongo.properties")
public class MongoCfg {
@Value("${mongo.url}")
private String url;
@Bean
public MongoClientFactoryBean mongoClientFactory() {
MongoClientFactoryBean mongo = new MongoClientFactoryBean();
mongo.setConnectionString(url);
return mongo;
}
@Bean
public MongoTemplate mongoTemplate() {
return new MongoTemplate(mongoClientFactory());
}
}
清单 10-44
Spring Data MongoDB 应用程序完整配置
```

到目前为止一切顺利，但 Spring 可以用 MongoDB 做更多事情，因此它提供了 `AbstractMongoClientConfiguration` 类，该类声明了许多 MongoDB 特定的基础设施 Bean。建议通过扩展此类并重写其 `mongoClientSettings()` 方法来配置你的 MongoDB 应用程序，以提供连接字符串。基于此实例，Spring Data 会配置 `MongoClientFactoryBean`、`MongoTemplate` 以及所有其他基础设施 Bean。如果需要自定义，可以重写其许多方法。清单 10-45 显示了扩展 `AbstractMongoClientConfiguration` 类并重写其部分方法的 `MongoCfg` 版本，特别是重写 `autoIndexCreation()` 以开启自动索引创建，因为我们在配置中有使用 `@Indexed` 注解的字段。



```
package com.apress.prospring6.ten.config;
import com.mongodb.ConnectionString;
import com.mongodb.MongoClientSettings;
import org.springframework.data.mongodb.config.AbstractMongoClientConfiguration;
import org.springframework.data.mongodb.repository.config.EnableMongoRepositories;
// 其他导入语句已省略
@ComponentScan(basePackages = {"com.apress.prospring6.ten.service"})
@EnableMongoRepositories
@Configuration
@PropertySource("classpath:mongo.properties")
public class MongoCfg extends AbstractMongoClientConfiguration {
@Value("${mongo.url}")
private String url;
@Value("${mongo.db}")
private String database;
@Override
protected String getDatabaseName() {
return database;
}
@Override
protected Collection getMappingBasePackages() {
return Collections.singleton("com.apress.prospring6.ten.repos");
}
@Override
public boolean autoIndexCreation() {
return true;
}
@Override
protected MongoClientSettings mongoClientSettings() {
MongoClientSettings.Builder builder = MongoClientSettings.builder();
builder.applyConnectionString(new ConnectionString(url));
this.configureClientSettings(builder);
return builder.build();
}
}
清单 10-45
扩展 AbstractMongoClientConfiguration 类的 MongoCfg 类
```

`getMappingBasePackages()` 方法用于配置带有 `@Document` 注解的类所在的位置；如果未重写该方法，基础设施会扫描注解配置类所在的包及其子包。

至于项目依赖，由于所有这些类都必须来自某个地方，项目的主要依赖是 `spring-data-mongodb` 和 `mongodb-driver-sync` 库。`mongodb-driver-sync` 库是 `mongodb-driver-core` 的封装，用于提供与 MongoDB 实例交互时的经典行为。此外，`mongodb-driver-reactivestreams` 库中还提供了支持响应式行为的封装。图 10-7 展示了 `chapter10-mongo` 项目的依赖关系。

![](img/315511_6_En_10_Fig7_HTML.jpg)

截图展示了 chapter10-mongo 项目的依赖关系。

图 10-7

显示 `chapter10-mongo` 项目依赖关系的 Gradle 视图

这里需要提及的一点是，从 MongoDB 4.0 开始，也支持事务。事务建立在 `Sessions` 之上，因此需要一个活跃的 `com.mongodb.session.ClientSession`。如果未配置 `TransactionManager` Bean，则事务将被禁用。

Spring 提供了 `MongoTransactionManager` 类用于 MongoDB 事务管理。`MongoTransactionManager` 类将 `ClientSession` 会话绑定到线程。`MongoTemplate` 会检测该会话，并对这些与事务相关联的资源进行操作。由于 Spring Data MongoDB 仓库由 `MongoTemplate` Bean 支持，因此它们的方法会在事务上下文中执行。

清单 10-46 展示了为支持事务行为而修改后的 `MongoCfg` 类。

```
package com.apress.prospring6.ten.config;
// 其他导入语句已省略
import org.springframework.data.mongodb.MongoDatabaseFactory;
import org.springframework.data.mongodb.MongoTransactionManager;
import org.springframework.transaction.annotation.EnableTransactionManagement;
@ComponentScan(basePackages = {"com.apress.prospring6.ten.service"})
@EnableMongoRepositories(basePackages = {"com.apress.prospring6.ten.repos"})
@Configuration
@PropertySource("classpath:mongo.properties")
@EnableTransactionManagement
public class MongoCfg extends AbstractMongoClientConfiguration {
@Bean
MongoTransactionManager transactionManager(MongoDatabaseFactory dbFactory) {
return new MongoTransactionManager(dbFactory);
}
// 其他方法已省略
}
清单 10-46
配置了事务管理的 MongoCfg 类
```

通过此配置，`@Transactional` 可用于将 `SingerServiceImpl` 配置为事务性服务。同时也支持编程式事务，其语法与**第** **9** **章**中展示的语法相同。

由于 Spring Data 在其 API 中的一致性，为 Spring Data Mongo 应用程序编写测试与为 Spring Data JPA 应用程序编写测试并无区别。

如果你对使用 Spring 和 MongoDB 感兴趣，Spring^(¹⁰⁰) 和 MongoDB^(¹⁰¹) 的官方文档内容详实且易于理解。此外，欢迎尝试使用 Spring Boot MongoDB 启动器。本书的代码仓库中提供了一个简单的项目。

## 使用 Spring Data 的考量

无论你是使用 Spring Data JPA、MongoDB 还是其他针对特定数据库的模块，Spring Data 都能大大减少你需要完成的工作量。再加上 Spring Boot，甚至连配置工作也显著减少。然而，为了高效管理数据，你必须理解数据管理基础设施的所有构建模块。这正是本书的价值所在，因为它逐步构建所有层次，向你展示通过正确使用 Spring 提供的所有功能，你将节省多少精力。

尽管如此，根据项目的规模和范围，使用 Spring Data 可能有些大材小用，因此在开始编写应用程序之前请考虑这一点。

这就像为你 83 岁、只需要打电话和接电话的祖母选择一部智能手机。由于她有关节炎，可能会经常摔手机。因此，给她买一部昂贵、复杂、易碎且提供许多她永远不会用到的功能的手机毫无意义，而一部翻盖手机就能满足她所有的需求。所以，请考虑你的项目试图满足的需求，并据此选择合适的工具。



## 总结

由于这是数据访问系列章节的最后一章，因此有必要进行一个总体总结：

*   JPA 是一个规范，它定义了用于对象关系映射和管理持久化对象的 API。在 Oracle 决定将其开源并捐赠给 Jakarta 之前，它一直是 JEE 的一部分。这触发了包名从 `javax` 到 `jakarta` 的重命名，并且你可能也会看到 JPA 这个缩写被扩展为 Jakarta Persistence API，而不是经典的 Java Persistence API。两者含义相同：都是指用于将数据库对象和操作映射到 Java 对象和操作的一组 Java 组件。

*   所有 JPA 实现，例如 Hibernate 和 EclipseLink 的最新版本，都已与新规范保持一致。

*   Spring Data JPA 在 JPA 之上增加了一层，并使用了其所有特性，例如实体和关联映射、实体生命周期管理，并增加了其自身特性，例如 Spring Data 的无代码仓库。

*   有多个 Spring Data 库，每个库都旨在减少使用特定数据库时的样板代码。Spring Data JPA 适用于任何关系型数据库。对于 NoSQL 数据库，有多个库可用于 MongoDB、CouchBase、Redis 等。

*   通过声明一个 `TransactionManager` bean 并使用 Spring 的 `@Transactional` 注解标注服务类和方法，可以轻松配置事务行为。关系型数据库和非关系型数据库的配置方式相同，只是 `TransactionManager` 的类型不同。

*   Spring Data Boot 启动器库通过减少配置应用程序所需编写的代码，降低了开发人员与数据库交互的工作量。

*   Testcontainers 是一个用于测试需要数据库的 Spring 应用程序的出色工具，因为它易于设置，允许你重用大部分生产范围的配置。这使你能够在尽可能接近生产环境的测试上下文中运行集成测试。当然，它要求你已安装 Docker 并能访问互联网，以便在需要时拉取容器镜像。

数据访问系列章节到此结束。在**第** **20** **章**的“响应式数据访问”部分，会再次简要提及数据访问主题。

脚注 1   2   3   4   5   6   7   8   9   10   11   12   13   14   15   16   17   18

