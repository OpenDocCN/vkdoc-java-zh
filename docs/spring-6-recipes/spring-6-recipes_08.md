# 8. Spring Batch

批处理已经存在了几十年。最早用于信息管理的技术（信息技术）的广泛应用就是批处理应用。这些环境没有交互式会话，通常也无法在内存中加载多个应用程序。计算机价格昂贵，与今天的服务器毫无相似之处。通常，机器是多用户共享的，并在白天使用（分时）。然而，到了晚上，机器就会闲置，这造成了巨大的浪费。企业开始投资于利用非工作时间来处理白天积累的工作的方法。这种做法催生了批处理。

批处理解决方案通常离线运行，对系统中的事件无动于衷。过去，批处理进程是出于必要而离线运行的。然而，如今大多数批处理进程之所以离线运行，是因为在可预测的时间完成工作以及完成批量工作，是许多架构的需求。批处理解决方案通常不响应请求，尽管没有理由不能因为消息或请求而启动它。批处理解决方案往往用于大型数据集，在这些数据集中，处理时长是其架构和实现中的关键因素。一个进程可能运行几分钟、几小时甚至几天！作业可能没有时间限制（即，一直运行直到所有工作完成，即使这意味着要运行几天），也可能有严格的时间限制（作业必须在恒定时间内进行，每一行花费相同的时间，无论限制如何，这让你可以预测某个作业将在特定时间窗口内完成）。

批处理有着悠久的历史，甚至影响着现代的批处理解决方案。

大型机应用程序使用批处理，而当今最大的批处理环境之一——z/OS 上的 CICS，从根本上来说仍然是一个大型机操作系统。客户信息控制系统（CICS）非常适合特定类型的任务：获取输入、处理输入、写入输出。CICS 是一个事务服务器，主要用于金融机构和政府机构，可以运行多种语言（COBOL、C、PLI 等）的程序。它可以轻松支持每秒数千笔交易。CICS 是最早的容器之一，这个概念对于 Spring 和 Jakarta EE 用户来说很熟悉，尽管 CICS 本身于 1969 年首次亮相！CICS 的安装成本非常昂贵，尽管 IBM 仍在销售和安装 CICS，但自那以后已经出现了许多其他解决方案。这些解决方案通常特定于特定环境：大型机上的 COBOL/CICS、Unix 上的 C，以及如今各种环境上的 Java。问题在于，处理这类批处理解决方案的标准化基础设施非常少。很少有人意识到他们错过了什么，因为 Java 平台对批处理的原生支持非常少。需要解决方案的企业通常最终会自行编写，导致代码脆弱且特定于领域。

然而，各个组件都已就位：事务支持、快速 I/O、诸如 Quartz 之类的调度器、可靠的线程支持，以及 Jakarta EE 和 Spring 中非常强大的应用程序容器概念。Dave Syer 和他的团队自然而然地构建了 [Spring Batch](https://spring.io/projects/spring-batch)，这是一个面向 Spring 平台的批处理解决方案。

在深入细节之前，思考这个框架解决了哪些类型的问题非常重要。一项技术由其解决方案空间定义。一个典型的 Spring Batch 应用程序通常会读取大量数据，然后以修改后的形式将其写回。关于事务边界、输入大小、并发性和处理步骤顺序的决策，都是典型集成的各个方面。

一个常见的需求是从逗号分隔值（CSV）文件加载数据，可能是作为企业对企业（B2B）交易，也可能是作为与旧版遗留应用程序的集成技术。另一个常见的应用是对数据库中的记录进行非平凡处理。也许输出是数据库记录本身的更新。一个例子可能是调整文件系统上图像的尺寸，这些图像的元数据存储在数据库中，或者需要根据某些条件触发另一个进程。

![](img/314861_5_En_8_Figa_HTML.gif)一个带阴影圆圈内字母 i 的图标，表示信息符号。  固定宽度数据是一种行和单元格的格式，与 CSV 文件非常相似。然而，CSV 文件单元格由逗号或制表符分隔，而固定宽度数据则通过假定每个值具有特定长度来工作。第一个值可能是前九个字符，第二个值是其后的四个字符，依此类推。

固定宽度数据通常用于遗留系统或嵌入式系统，是批处理的绝佳候选。处理本质上非事务性资源（例如，Web 服务或文件）的处理过程非常适合批处理，因为批处理提供了大多数 Web 服务不会提供的重试/跳过/失败功能。

理解 Spring Batch 不做什么也很重要。Spring Batch 是一个灵活但并非包罗万象的解决方案。正如 Spring 在可以避免的情况下不会重新发明轮子一样，Spring Batch 将一些重要的部分留给实现者自行决定。例如：Spring Batch 提供了一种通用机制来启动作业，无论是通过命令行、Unix cron、操作系统服务、Quartz（在第 10 章中讨论），还是响应企业服务总线（ESB）上的事件（例如，Mule ESB 或 Spring 自己的类 ESB 解决方案 Spring Integration，在第 12 章中讨论）。另一个例子是 Spring Batch 管理批处理进程状态的方式。Spring Batch 需要一个持久化存储。JobRepository（Spring Batch 提供的用于存储运行时数据的接口）唯一有用的实现需要数据库，因为数据库是事务性的，并且无需重新发明。然而，你应该部署到哪个数据库在很大程度上未指定，尽管当然为你提供了有用的默认值。



## 8-1\. 运行时元数据模型

Spring Batch 与 `JobRepository` 协同工作，该仓库负责存储每个作业的所有知识与元数据（包括 `JobInstances`、`JobExecution` 和 `StepExecution` 等组件部分）。每个 `Job` 由一个或多个按顺序执行的 `Step` 组成。在 Spring Batch 中，一个 `Step` 可以有条件地跟随另一个 `Step`，从而实现基本的工作流。

这些步骤也可以并发执行：两个步骤可以同时运行。

当作业运行时，它通常与 `JobParameters` 结合，以参数化 `Job` 本身的行为。例如，一个作业可能接受一个日期参数来决定处理哪些记录。这种结合被称为 `JobInstance`。由于关联的 `JobParameters` 不同，每个 `JobInstance` 都是唯一的。每次运行同一个 `JobInstance`（即相同的 `Job` 和 `JobParameters`）时，都称为一次 `JobExecution`。这是该 `Job` 某个版本的运行时上下文。理想情况下，每个 `JobInstance` 只应有一个 `JobExecution`：即该 `JobInstance` 首次运行时创建的 `JobExecution`。然而，如果出现任何错误，`JobInstance` 应被重启；后续运行将创建另一个 `JobExecution`。对于原始作业中的每个步骤，`JobExecution` 中都有一个对应的 `StepExecution`。

因此，你可以看到 Spring Batch 拥有一个镜像对象图，一个反映作业的设计/构建时视图，另一个反映作业的运行时视图。这种原型与实例之间的分离，与许多工作流引擎（包括 jBPM）的工作方式非常相似。

例如，假设每日报告在凌晨 2 点生成。作业的参数将是日期（很可能是前一天的日期）。在这种情况下，作业将包含一个加载步骤、一个汇总步骤和一个输出步骤。每天运行作业时，都会创建一个新的 `JobInstance` 和 `JobExecution`。如果同一个 `JobInstance` 有任何重试，理论上会创建多个 `JobExecution`。

## 8-2\. 设置 Spring Batch 的基础设施

### 问题

Spring Batch 为你的应用程序提供了极大的灵活性和保障，但它无法在真空中运行。为了完成其工作，`JobRepository` 需要一个数据库。此外，Spring Batch 还需要多个协作者才能正常工作。这些配置大多是样板代码。

### 解决方案

在本方案中，你将设置 Spring Batch 数据库，并创建一个可供后续解决方案导入的 Spring 应用程序配置。这种配置重复且基本无趣。它还将告诉 Spring Batch 使用哪个数据库来存储其元数据。



### 工作原理

`JobRepository` 接口是你在设置 Spring Batch 流程时首先要处理的内容。通常你不会在代码中直接操作它，但在 Spring 配置中，它是让其他一切正常工作的关键。`JobRepository` 接口只有一个真正有用的实现，名为 `SimpleJobRepository`，它将批处理流程的状态信息存储在数据库中。其创建是通过 `JobRepositoryFactoryBean` 完成的。另一个标准工厂 `MapJobRepositoryFactoryBean` 主要用于测试，因为它的状态不是持久的——它是一个内存实现。这两个工厂都会创建 `SimpleJobRepository` 的实例。

由于这个 `JobRepository` 实例在你的数据库上工作，你需要为 Spring Batch 设置好模式。不同数据库的模式文件位于 Spring Batch 发行版中。初始化数据库最简单的方法是在 Java 配置中使用 `DataSourceInitializer`。这些文件可以在 `org/springframework/batch/core` 目录中找到。其中有几个 `.sql` 文件，每个文件都包含所选数据库所需模式的数据定义语言（DDL，用于定义和检查数据库结构的 SQL 子集）。在这些示例中，我们将使用 H2，因此我们将使用 H2 的 DDL：`schema-h2.sql`。请确保按照以下配置对其进行配置，并告知 Spring Batch。

```
@ComponentScan("com.apress.spring6recipes.springbatch")
@PropertySource("classpath:batch.properties")
public class BatchConfiguration {
@Bean
public DataSource dataSource(Environment env) {
var dataSource = new DriverManagerDataSource();
dataSource.setUrl(env.getRequiredProperty("datasource.url"));
dataSource.setUsername(env.getRequiredProperty("datasource.username"));
dataSource.setPassword(env.getRequiredProperty("datasource.password"));
return dataSource;
}
@Bean
public DataSourceInitializer dataSourceInitializer(
DataSource dataSource,
DatabasePopulator databasePopulator) {
var initializer = new DataSourceInitializer();
initializer.setDataSource(dataSource);
initializer.setDatabasePopulator(databasePopulator);
return initializer;
}
@Bean
public DatabasePopulator databasePopulator() {
var databasePopulator = new ResourceDatabasePopulator();
databasePopulator.setContinueOnError(true);
databasePopulator.addScript(
new ClassPathResource("org/springframework/batch/core/schema-h2.sql"));
databasePopulator.addScript(
new ClassPathResource("sql/reset_user_registration.sql"));
return databasePopulator;
}
@Bean
public DataSourceTransactionManager transactionManager(DataSource dataSource) {
return new DataSourceTransactionManager(dataSource);
}
@Bean
public JobRepositoryFactoryBean jobRepository(
DataSource dataSource,
PlatformTransactionManager transactionManager) {
var jobRepositoryFactoryBean = new JobRepositoryFactoryBean();
jobRepositoryFactoryBean.setDataSource(dataSource);
jobRepositoryFactoryBean.setTransactionManager(transactionManager);
return jobRepositoryFactoryBean;
}
@Bean
public TaskExecutorJobLauncher jobLauncher(JobRepository jobRepository) {
var jobLauncher = new TaskExecutorJobLauncher();
jobLauncher.setJobRepository(jobRepository);
return jobLauncher;
}
@Bean
public JobRegistryBeanPostProcessor jobRegistryBeanPostProcessor(
JobRegistry jobRegistry) {
var jobRegistryBeanPostProcessor = new JobRegistryBeanPostProcessor();
jobRegistryBeanPostProcessor.setJobRegistry(jobRegistry);
return jobRegistryBeanPostProcessor;
}
清单 8-1
带有显式默认值的批处理配置
```

前几个 Bean 严格与配置相关——对于 Spring Batch 来说，没有什么特别新奇或独特之处：一个数据源、一个事务管理器和一个数据源初始化器。

最终，我们声明了一个 `MapJobRegistry` 实例。这很关键——它是存储给定作业信息的中央仓库，并控制系统中所有作业的“全局视图”。其他所有内容都与此实例协同工作。

接下来，我们有一个 `TaskExecutorJobLauncher`，其唯一目的是为你提供一种启动批处理作业的机制，这里的“作业”指的是我们的批处理解决方案。`jobLauncher` 用于指定要运行的批处理解决方案的名称以及所需的任何参数。我们将在下一个配方中进一步讨论这一点。

接着，你定义了一个 `JobRegistryBeanPostProcessor`。这个 Bean 会扫描你的 Spring 上下文文件，并将所有已配置的作业与 `MapJobRegistry` 关联起来。

最后，我们得到了 `SimpleJobRepository`（它又由 `JobRepositoryFactoryBean` 创建）。`JobRepository` 是“仓库”的一种实现（从企业应用架构模式的意义上讲）：它处理围绕步骤、作业等领域的持久化和检索。

`@PropertySource` 注解将指示 Spring 加载我们的 `batch.properties` 文件。我们将使用 `Environment` 类来检索所需的属性。`batch.properties` 包含以下内容。

![](img/314861_5_En_8_Figb_HTML.gif)灯泡图标表示提示信息。  我们也可以使用 `@Value` 注解来注入所有单独的属性，但在配置类中需要多个属性时，使用 `Environment` 对象会更简单。

```
datasource.password=sa
datasource.username=
datasource.url=jdbc:h2:~/batch
清单 8-2
数据库属性
```

尽管这样可行，但 Spring Batch 也支持使用 `@EnableBatchProcessing` 注解直接配置这些默认值。这会让事情变得稍微简单一些。

```
@Configuration
@EnableBatchProcessing
@ComponentScan("com.apress.spring6recipes.springbatch")
@PropertySource("classpath:/batch.properties")
public class BatchConfiguration {
@Bean
public DataSource dataSource(Environment env) {
var dataSource = new DriverManagerDataSource();
dataSource.setUrl(env.getRequiredProperty("datasource.url"));
dataSource.setUsername(env.getProperty("datasource.username"));
dataSource.setPassword(env.getProperty("datasource.password"));
return dataSource;
}
@Bean
public DataSourceTransactionManager transactionManager(DataSource dataSource) {
return new DataSourceTransactionManager(dataSource);
}
@Bean
public DataSourceInitializer databasePopulator(DataSource dataSource) {
var populator = new ResourceDatabasePopulator();
populator.addScript(
new ClassPathResource("org/springframework/batch/core/schema-h2.sql"));
populator.addScript(
new ClassPathResource("sql/reset_user_registration.sql"));
populator.setContinueOnError(true);
populator.setIgnoreFailedDrops(true);
清单 8-3
带有隐式默认值的批处理配置
```

这个类只包含三个 Bean 定义：一个用于数据源，一个用于事务管理器，一个用于初始化数据库。由于 `@EnableBatchProcessing` 注解，其他所有内容都已处理完毕。上述配置类将使用一些合理的默认值来引导 Spring Batch。

默认配置将配置一个 `JobRepository`、`JobRegistry` 和 `JobLauncher`。

如果你的应用程序中有多个数据源，则需要添加一个显式的 `BatchConfigurer` 来选择用于应用程序批处理部分的数据源。

以下 `Main` 类将使用基于 Java 的配置来运行批处理应用程序。

```
package com.apress.spring6recipes.springbatch;
import com.apress.spring6recipes.springbatch.config.BatchConfiguration;
import org.springframework.batch.core.configuration.JobRegistry;
import org.springframework.batch.core.launch.JobLauncher;
import org.springframework.batch.core.repository.JobRepository;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
public class Main {
public static void main(String[] args) {
var cfg = BatchConfiguration.class;
try (var context = new AnnotationConfigApplicationContext(cfg)) {
var jobRegistry = context.getBean("jobRegistry", JobRegistry.class);
var jobLauncher = context.getBean("jobLauncher", JobLauncher.class);
var jobRepository = context.getBean("jobRepository", JobRepository.class);
System.out.println("JobRegistry: " + jobRegistry);
System.out.println("JobLauncher: " + jobLauncher);
System.out.println("JobRepository: " + jobRepository);
}
}
}
清单 8-4
主类
```



## 8-3\. 读取与写入

### 问题

你希望将文件中的数据插入到数据库中。这个解决方案将是最简单的方案之一，并让你有机会探索典型解决方案中的各个移动组件。

### 解决方案

你将构建一个解决方案，该方案在作为该技术可行应用的同时，仅需完成最少量的工作。该方案将读取一个任意长度的文件，并将数据写入数据库。最终结果几乎可以做到零代码。你将依赖一个现有的模型类，并编写一个类（包含 `public static void main(String [] args())` 方法的类）来完成这个示例。模型类完全可以是一个 Hibernate 类或来自你 DAO 层的某个类，尽管在本例中它只是一个简单的 POJO。此解决方案将使用我们在配方 8-1 中配置的组件。

### 工作原理

本示例演示了 Spring Batch 最简单的用途：提供可扩展性。该程序仅从 CSV 文件中读取数据，字段以逗号分隔，行以换行符分隔。然后它将记录插入到一张表中。你利用了 Spring Batch 提供的智能基础设施，从而无需担心扩展问题。这个应用程序手动实现也很容易。你不会利用任何可用的智能事务功能，暂时也不会考虑重试机制。

这个解决方案是 Spring Batch 解决方案中最简单的一种。Spring Batch 使用 XML 模式对解决方案进行建模。其抽象概念和术语遵循经典批处理解决方案的精神，因此可以从前代技术移植，也可能适用于后续技术。Spring Batch 提供了有用的默认类，你可以覆盖或有选择地调整它们。在下面的示例中，你将使用 Spring Batch 提供的许多实用工具实现。从根本上说，大多数解决方案看起来都差不多，并且都使用了同一组接口的组合。通常只需要挑选合适的接口即可。

当我运行这个程序时，它既能处理包含 20,000 行的文件，也能处理包含 100 万行的文件。我没有发现内存增长，这表明没有内存泄漏。当然，处理时间要长得多！（插入 100 万行的应用程序运行了几个小时。）

![](img/314861_5_En_8_Figc_HTML.gif)灯泡图标表示提示信息。 当然，如果你处理 100 万行数据，却在倒数第二条记录上失败，那将是灾难性的，因为当事务回滚时，你所有的工作都将丢失！请继续阅读关于分块的示例。此外，你可能想阅读第 7 章来复习一下事务。

```
create table USER_REGISTRATION
(
ID BIGINT NOT NULL PRIMARY KEY GENERATED ALWAYS AS IDENTITY (START WITH 1, INCREMENT BY 1),
FIRST_NAME VARCHAR(255) not null,
LAST_NAME VARCHAR(255) not null,
COMPANY VARCHAR(255) not null,
ADDRESS VARCHAR(255) not null,
CITY VARCHAR(255) not null,
STATE VARCHAR(255) not null,
ZIP VARCHAR(255) not null,
COUNTY VARCHAR(255) not null,
URL VARCHAR(255) not null,
PHONE_NUMBER VARCHAR(255) not null,
FAX VARCHAR(255) not null
) ;
代码清单 8-5
用户注册 SQL
```

数据加载与数据仓库

我完全没有对表进行调优。例如，除了主键之外，没有任何列上建有索引。这是为了避免使示例复杂化。在非平凡的生产级应用程序中，处理此类表时需要格外小心。

Spring Batch 应用程序是勤恳的应用程序，有可能揭示出你未曾意识到的应用程序瓶颈。想象一下，突然能够每 10 分钟完成 100 万次新的数据库插入。你的数据库会因此陷入停滞吗？插入速度可能是影响应用程序速度的关键因素。软件开发人员（希望如此）会从数据库模式如何很好地执行业务逻辑约束以及如何服务于整体业务模型的角度来思考。然而，在编写此类应用程序时，戴上另一顶帽子——数据库管理员（DBA）的帽子——也很重要。一种常见的解决方案是创建一个反规范化的表，其内容一旦进入数据库，可以通过插入触发器等方式强制转换为有效数据。这在数据仓库中很典型。稍后，你将探索使用 Spring Batch 在插入前对记录进行处理。这允许开发人员验证或覆盖输入到数据库中的数据。这种处理，结合在数据库中最能体现的约束的保守应用，可以构建出非常健壮且快速的应用程序。



#### 任务配置

任务配置如下所示。

```
package com.apress.spring6recipes.springbatch.config;
import com.apress.spring6recipes.springbatch.UserRegistration;
import org.springframework.batch.core.Job;
import org.springframework.batch.core.Step;
import org.springframework.batch.core.job.builder.JobBuilder;
import org.springframework.batch.core.repository.JobRepository;
import org.springframework.batch.core.step.builder.StepBuilder;
import org.springframework.batch.item.ItemReader;
import org.springframework.batch.item.ItemWriter;
import org.springframework.batch.item.database.BeanPropertyItemSqlParameterSourceProvider;
import org.springframework.batch.item.database.JdbcBatchItemWriter;
import org.springframework.batch.item.file.FlatFileItemReader;
import org.springframework.batch.item.file.LineMapper;
import org.springframework.batch.item.file.mapping.BeanWrapperFieldSetMapper;
import org.springframework.batch.item.file.mapping.DefaultLineMapper;
import org.springframework.batch.item.file.mapping.FieldSetMapper;
import org.springframework.batch.item.file.transform.DelimitedLineTokenizer;
import org.springframework.batch.item.file.transform.LineTokenizer;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.core.io.Resource;
import org.springframework.transaction.PlatformTransactionManager;
import javax.sql.DataSource;
@Configuration
public class UserJob {
private static final String INSERT_REGISTRATION_QUERY = """
insert into USER_REGISTRATION (FIRST_NAME, LAST_NAME, COMPANY, ADDRESS,CITY,STATE,ZIP,COUNTY,URL,PHONE_NUMBER,FAX)
values
(:firstName,:lastName,:company,:address,:city,:state,:zip,:county,:url,:phoneNumber,:fax)""";
private final JobRepository jobRepository;
@Value("file:${user.home}/batches/registrations.csv")
private Resource input;
public UserJob(JobRepository jobRepository) {
this.jobRepository = jobRepository;
}
@Bean
public Job insertIntoDbFromCsvJob(Step step1) {
var name = "User Registration Import Job";
var builder = new JobBuilder(name, jobRepository);
return builder.start(step1).build();
}
@Bean
public Step step1(ItemReader reader,
ItemWriter writer,
PlatformTransactionManager txManager) {
var name = "User Registration CSV To DB Step";
var builder = new StepBuilder(name, jobRepository);
return builder
.chunk(5, txManager)
.reader(reader)
.writer(writer)
.build();
}
@Bean
public FlatFileItemReader csvFileReader(
LineMapper lineMapper) {
var itemReader = new FlatFileItemReader();
itemReader.setLineMapper(lineMapper);
itemReader.setResource(input);
return itemReader;
}
@Bean
public DefaultLineMapper lineMapper(LineTokenizer tokenizer,
FieldSetMapper mapper) {
var lineMapper = new DefaultLineMapper();
lineMapper.setLineTokenizer(tokenizer);
lineMapper.setFieldSetMapper(mapper);
return lineMapper;
}
@Bean
public BeanWrapperFieldSetMapper fieldSetMapper() {
var fieldSetMapper = new BeanWrapperFieldSetMapper();
fieldSetMapper.setTargetType(UserRegistration.class);
return fieldSetMapper;
}
@Bean
public DelimitedLineTokenizer tokenizer() {
var tokenizer = new DelimitedLineTokenizer();
tokenizer.setDelimiter(",");
tokenizer.setNames("firstName", "lastName", "company", "address", "city",
"state", "zip", "county", "url", "phoneNumber", "fax");
return tokenizer;
}
@Bean
public JdbcBatchItemWriter jdbcItemWriter(DataSource dataSource) {
var provider = new BeanPropertyItemSqlParameterSourceProvider();
var itemWriter = new JdbcBatchItemWriter();
itemWriter.setDataSource(dataSource);
itemWriter.setSql(INSERT_REGISTRATION_QUERY);
itemWriter.setItemSqlParameterSourceProvider(provider);
return itemWriter;
}
}
清单 8-6
用户任务定义
```

如前所述，一个任务由多个步骤组成，这些步骤是给定任务的实际执行核心。步骤可以像你期望的那样复杂或简单。实际上，步骤可以被视为任务的最小工作单元。输入（读取的内容）被传递给`Step`，并可能经过处理；然后从步骤中产生输出（写入的内容）。这种处理过程通过`Tasklet`来定义。你可以提供自己的`Tasklet`实现，或者直接使用一些针对不同处理场景预配置的配置。这些实现通过`Tasklet`元素的子元素形式提供。批处理最重要的方面之一是面向块的处理，这里通过使用块配置方法来实现。

在面向块的处理中，输入从读取器中读取，可选地进行处理，然后进行聚合。最后，在可配置的间隔（由提交间隔属性指定，用于配置在事务提交之前将处理多少个项目）处，所有输入被发送到写入器。如果存在事务管理器，则事务也会被提交。在提交之前，数据库中的元数据会被更新，以标记任务的进度。

当具有事务感知能力的写入器（或处理器）回滚时，关于输入（读取）值的聚合存在一些细微差别。Spring Batch 会缓存它读取的值，并将它们写入写入器。如果写入器组件是事务性的（例如数据库），而读取器不是，那么缓存读取的值并可能重试或采取其他替代方法本身并没有什么问题。如果读取器本身也是事务性的，那么从资源中读取的值将会回滚，并且可能会发生变化，从而导致内存中缓存的值变得过时。如果发生这种情况，你可以通过在块元素上使用`reader-transactional-queue="true"`来配置块不缓存这些值。



#### 输入

首要职责是从文件系统中读取文件。在示例中，我们使用了一个现成的实现。读取 CSV 文件是非常常见的场景，Spring Batch 对此提供了完善的支持。`org.springframework.batch.item.file.FlatFileItemReader<T>` 类将文件中字段和记录的定界任务委托给 `LineMapper<T>`，而 `LineMapper<T>` 又将识别记录中字段的任务委托给 `LineTokenizer`。我们使用 `org.springframework.batch.item.file.transform.DelimitedLineTokenizer`，它被配置为以“,”字符分隔字段。

`DefaultLineMapper` 还声明了一个 `fieldSetMapper` 属性，该属性需要一个 `FieldSetMapper` 的实现。这个 Bean 负责接收输入的名称/值对，并生成一个将传递给写入器组件的类型。

在本例中，我们使用 `BeanWrapperFieldSetMapper`，它将创建一个类型为 `UserRegistration` 的 JavaBean POJO。我们为这些字段命名，以便稍后在配置中引用它们。这些名称不必与输入文件中某个标题行的值相同；它们只需与输入文件中字段出现的顺序相对应即可。这些名称也被 `FieldSetMapper` 用于匹配 POJO 上的属性。每读取一条记录，这些值就会被应用到 POJO 的一个实例上，然后返回该 POJO。

```
@Bean
public FlatFileItemReader csvFileReader(
LineMapper lineMapper) {
var itemReader = new FlatFileItemReader();
itemReader.setLineMapper(lineMapper);
itemReader.setResource(input);
return itemReader;
}
@Bean
public DefaultLineMapper lineMapper(LineTokenizer tokenizer,
FieldSetMapper mapper) {
var lineMapper = new DefaultLineMapper();
lineMapper.setLineTokenizer(tokenizer);
lineMapper.setFieldSetMapper(mapper);
return lineMapper;
}
@Bean
public BeanWrapperFieldSetMapper fieldSetMapper() {
var fieldSetMapper = new BeanWrapperFieldSetMapper();
fieldSetMapper.setTargetType(UserRegistration.class);
return fieldSetMapper;
}
@Bean
public DelimitedLineTokenizer tokenizer() {
var tokenizer = new DelimitedLineTokenizer();
tokenizer.setDelimiter(",");
tokenizer.setNames("firstName", "lastName", "company", "address", "city",
"state", "zip", "county", "url", "phoneNumber", "fax");
return tokenizer;
}
清单 8-7
用户作业定义——CSV 项目读取器
```

从读取器返回的类 `UserRegistration` 是一个相当简单的 Java 记录。

```
package com.apress.spring6recipes.springbatch;
public record UserRegistration(
String firstName,
String lastName,
String company,
String address,
String city,
String stat,
String zip,
String county,
String url,
String phoneNumber,
String fax) {
}
清单 8-8
UserRegistration 类
```

#### 输出

下一个要执行工作的组件是写入器，它负责接收从读取器读取的聚合项目集合。在本例中，你可以想象每次当集合超过块元素上的提交间隔属性时，就会创建一个新的集合（`java.util.List<UserRegistration>`），然后写入，再重置。由于我们要写入数据库，因此使用 Spring Batch 的 `org.springframework.batch.item.database.JdbcBatchItemWriter`。该类支持接收输入并将其写入数据库。由开发者提供输入并指定应对输入执行哪些 SQL。它将运行由 `sql` 属性指定的 SQL，本质上是从数据库中读取，次数由块元素的提交间隔指定，然后提交整个事务。这里，我们执行一个简单的插入操作。命名参数的名和值由为 `itemSqlParameterSourceProvider` 属性配置的 Bean 创建，该属性是一个 `BeanPropertyItemSqlParameterSourceProvider` 实例，其唯一职责是获取 JavaBean 属性，并将其作为与 JavaBean 上属性名称对应的命名参数提供出来。

```
@Bean
public JdbcBatchItemWriter jdbcItemWriter(DataSource dataSource) {
var provider = new BeanPropertyItemSqlParameterSourceProvider();
var itemWriter = new JdbcBatchItemWriter();
itemWriter.setDataSource(dataSource);
itemWriter.setSql(INSERT_REGISTRATION_QUERY);
itemWriter.setItemSqlParameterSourceProvider(provider);
return itemWriter;
}
清单 8-9
用户作业定义——JDBC 项目写入器
```

就是这样！一个可运行的解决方案。只需少量配置且无需自定义代码，我们就构建了一个用于读取大型 CSV 文件并将其导入数据库的解决方案。这个解决方案是基础性的，遗留了许多未处理的边界情况。例如，你可能希望在项目被读取时（在插入之前）对其进行处理。

这展示了一个简单的作业。重要的是要记住，存在用于执行完全相反转换的类似类：从数据库读取并写入 CSV 文件。

```
@Bean
public Job insertIntoDbFromCsvJob(Step step1) {
var name = "User Registration Import Job";
var builder = new JobBuilder(name, jobRepository);
return builder.start(step1).build();
}
@Bean
public Step step1(ItemReader reader,
ItemWriter writer,
PlatformTransactionManager txManager) {
var name = "User Registration CSV To DB Step";
var builder = new StepBuilder(name, jobRepository);
return builder
.chunk(5, txManager)
.reader(reader)
.writer(writer)
.build();
}
清单 8-10
用户作业定义——作业和步骤
```

为了配置步骤，我们给它命名为 `User Registration CSV To DB Step`。我们使用基于块的处理，并且需要告诉它我们想要块大小为 5。接下来，我们为其提供一个读取器和一个写入器，最后告诉构建器构建该步骤。配置好的步骤最终被连接为作业的起点，该作业名为 `User Registration Import Job`，仅包含这一个步骤。

#### 简化 ItemReader 和 ItemWriter 配置

配置 `ItemReader` 和 `ItemWriter` 可能是一项艰巨的任务。你需要了解相当多的 Spring Batch 内部原理（使用哪些类等）。从 Spring Batch 4 开始，配置读取器和写入器变得更加容易，因为现在针对不同的读取器和写入器有了专门的构建器。

要配置 `FlatFileItemReader`，可以使用 `FlatFileItemReaderBuilder`，并且无需配置四个独立的 Bean，现在只需六行代码（主要是由于示例中的格式）。

```
@Bean
public FlatFileItemReader csvFileReader() {
var names = new String[] {
"firstName", "lastName", "company", "address", "city",
"state", "zip", "county", "url", "phoneNumber", "fax"
};
return new FlatFileItemReaderBuilder()
.name(ClassUtils.getShortName(FlatFileItemReader.class))
.resource(input)
.targetType(UserRegistration.class)
.delimited()
.names(names)
.build();
}
清单 8-11
用户作业定义——CSV 项目读取器
```

这个构建器会自动创建 `DefaultLineMapper`、`BeanWrapperFieldSetMapper` 和 `DelimitedLineTokenizer`，你无需知道它们在内部被使用。现在，你基本上可以描述你的配置，而不是显式地配置所有不同的项目。

同样的方法也适用于使用 `JdbcBatchItemWriterBuilder` 的 `JdbcBatchItemWriter`。

```
@Bean
public JdbcBatchItemWriter jdbcItemWriter(DataSource dataSource) {
return new JdbcBatchItemWriterBuilder()
.dataSource(dataSource)
.sql(INSERT_REGISTRATION_QUERY)
.beanMapped()
.build();
}
清单 8-12
用户作业定义——JDBC 项目写入器
```

## 8-4. 编写自定义 ItemWriter 和 ItemReader

### 问题

你想要与 Spring Batch 不知道如何连接的资源（可以想象是 RSS 源或任何其他自定义数据格式）进行通信。

### 解决方案

你可以轻松编写自己的 `ItemWriter` 或 `ItemReader`。这些接口非常简单，并且对实现的要求很少。



### 工作原理

尽管这个过程执行起来简单且微不足道，但它仍然不如直接复用众多现成选项中的任何一个来得方便。如果你仔细查找，很可能会找到合适的方案。Spring Batch 支持写入 JMS（`JmsItemWriter`）、JPA（`JpaItemWriter`）、JDBC（`JdbcBatchItemWriter`）、文件（`FlatFileItemWriter`）、Hibernate（`HibernateItemWriter`）等。甚至还支持通过调用 Bean 上的方法（`PropertyExtractingDelegatingItemWriter`）并将待写入条目的属性作为参数传递给它！其中一个非常有用的写入器允许你写入一组带编号的文件。这个实现——`MultiResourceItemWriter<T>`——将实际工作委托给其他合适的 `ItemWriter<T>` 实现，但允许你写入多个文件，而不仅仅是一个非常大的文件。`ItemReader` 的实现集虽然略小，但也令人印象深刻。如果找不到现成的，请再仔细找找。如果仍然找不到，可以考虑自己编写一个。在本节中，我们将进行这样的实践。

#### 编写自定义 ItemReader

`ItemReader` 的示例很简单。这里创建了一个 `ItemReader`，它知道如何从远程过程调用（RPC）端点检索 `UserRegistration` 对象。

```
package com.apress.spring6recipes.springbatch;
import org.springframework.batch.item.ItemReader;
import java.time.LocalDate;
public class UserRegistrationItemReader implements ItemReader {
private final UserRegistrationService usr;
public UserRegistrationItemReader(UserRegistrationService usr) {
this.usr = usr;
}
public UserRegistration read() throws Exception {
var today = LocalDate.now();
var registrations = usr.getOutstandingUserRegistrationBatchForDate(1, today);
var iter = registrations.iterator();
return iter.hasNext() ? iter.next() : null;
}
}
清单 8-13
UserRegistrationItemReader 源码
```

如你所见，这个接口很简单。在这个例子中，你将大部分工作委托给一个远程服务来提供输入。该接口要求你返回一条记录。接口通过要返回的对象类型（即“条目”）进行参数化。所有读取的条目将被聚合，然后传递给 `ItemWriter`。

#### 编写自定义 ItemWriter

`ItemWriter` 的示例也很简单。想象一下，你想通过使用 Spring 提供的众多远程调用选项之一来调用远程服务进行写入。`ItemWriter` 接口通过你期望写入的条目类型进行参数化。在这里，你期望从 `ItemReader` 接收一个 `UserRegistration` 对象。该接口包含一个方法，该方法期望接收一个该类的参数化类型的 `Chunk`。这些对象是从 `ItemReader` 读取并聚合而来的。如果你的提交间隔是十，那么你可能会在 `Chunk` 中看到十个或更少的条目。

```
package com.apress.spring6recipes.springbatch;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.batch.item.Chunk;
import org.springframework.batch.item.ItemWriter;
import java.util.List;
public class UserRegistrationServiceItemWriter implements ItemWriter {
private final Logger logger = LoggerFactory.getLogger(getClass());
private final UserRegistrationService urs;
public UserRegistrationServiceItemWriter(UserRegistrationService urs) {
this.urs = urs;
}
@Override
public void write(Chunk items) throws Exception {
items.forEach(this::write);
}
private void write(UserRegistration ur) {
var registration = urs.registerUser(ur);
logger.debug("Registered: {}", registration);
}
}
清单 8-14
UserRegistrationServiceItemWriter 源码
```

在这里，你注入了服务的客户端接口。你只需遍历 `UserRegistration` 对象并调用服务，该服务会返回一个相同的 `UserRegistration` 实例。如果去掉多余的空白、花括号和日志输出，满足需求只需要两行代码。

`UserRegistrationService` 的接口如下。

```
package com.apress.spring6recipes.springbatch;
import java.time.LocalDate;
public interface UserRegistrationService {
Iterable getOutstandingUserRegistrationBatchForDate(int quantity, LocalDate date);
UserRegistration registerUser(UserRegistration userRegistrationRegistration);
}
清单 8-15
UserRegistrationService 接口
```

在我们的示例中，我们没有为该接口提供特定的实现，因为这无关紧要：它可以是任何 Spring Batch 尚未知晓的接口。

## 8-5\. 在写入前处理输入

### 问题

虽然直接从电子表格或 CSV 转储传输数据可能很有用，但可以想象，在数据写入之前，你可能需要对其进行某种处理。CSV 文件中的数据，更广泛地说，来自任何源的数据，通常并不完全符合你的预期，或者不适合直接写入。仅仅因为 Spring Batch 可以将其强制转换为 POJO，并不意味着数据的状态是正确的。在数据适合写入之前，可能还需要从其他服务推断或填充额外的数据。

### 解决方案

Spring Batch 允许你对读取器的输出进行处理。在处理过程中，几乎可以对输出进行任何操作，然后再将其传递给写入器，包括更改数据的类型。



### 工作原理

Spring Batch 允许实现者从读取器读取数据后执行任何自定义逻辑。块配置中的 `processor` 属性期望引用一个 `org.springframework.batch.item.ItemProcessor` 接口的 Bean。因此，上一个配方中的作业定义修改后如下所示。

```
@Bean
public Step step1(ItemReader reader,
ItemProcessor processor,
ItemWriter writer,
PlatformTransactionManager txManager) {
var name = "用户注册 CSV 转数据库步骤";
var builder = new StepBuilder(name, jobRepository);
return builder
.chunk(5, txManager)
.reader(reader)
.processor(processor)
.writer(writer)
.build();
}
清单 8-16
UserRegistrationValidationItemProcessor 修改后的步骤配置
```

目标是在将数据写入数据库之前对其进行某些验证。如果确定记录无效，可以通过从 `ItemProcessor` 返回 `null` 来停止进一步处理。这一点至关重要，提供了必要的安全保障。您需要做的一件事是确保数据格式正确（例如，模式可能要求有效的两个字母的州名，而不是较长的完整州名）。电话号码需要遵循特定格式，您可以使用此处理器去除电话号码中任何无关字符，只保留有效的（在美国）十位数字电话号码。这同样适用于美国邮政编码，它由五个字符组成，可选地后跟一个连字符和四位数字代码。最后，虽然防止重复的最佳约束是在数据库中实现的，但在插入之前，很可能还需要通过查询系统来满足记录的其他资格标准。

以下是 `ItemProcessor` 的配置。

```
@Bean
public UserRegistrationValidationItemProcessor validatingItemProcessor() {
return new UserRegistrationValidationItemProcessor();
}
清单 8-17
UserRegistrationValidationItemProcessor Bean 配置
```

为了保持此类简短，我不会完整重印，但关键部分应该很明显。

```
package com.apress.spring6recipes.springbatch;
import org.springframework.batch.item.ItemProcessor;
import org.springframework.util.StringUtils;
import java.util.Arrays;
import java.util.Collection;
public class UserRegistrationValidationItemProcessor
implements ItemProcessor {
private String stripNonNumbers(String input) {
}
private boolean isTelephoneValid(String telephone) {
}
private boolean isZipCodeValid(String zip) {
}
private boolean isValidState(String state) {
}
public UserRegistration process(UserRegistration input) {
var zipCode = stripNonNumbers(input.zip());
var telephone = stripNonNumbers(input.phoneNumber());
var state = input.state();
if (isTelephoneValid(telephone) && isZipCodeValid(zipCode) && isValidState(state)) {
return new UserRegistration(
input.firstName(), input.lastName(), input.company(), input.address(),
input.city(), input.state(), zipCode, input.county(), input.url(),
telephone, input.fax());
}
return null;
}
清单 8-18
UserRegistrationValidationItemProcessor 源代码
```

该类是一个参数化类型。类型信息包括输入类型和输出类型。输入是传递给方法进行处理的内容，输出是从方法返回的数据。由于在此示例中您没有进行任何转换，因此两个参数化类型是相同的。此过程完成后，Spring Batch 元数据表中会有大量有用的信息。在您的数据库上执行以下查询。

```
select * from BATCH_STEP_EXECUTION;
清单 8-19
UserRegistrationValidationItemProcessor 示例查询
```

除此之外，您将获得作业的退出状态、提交次数、读取的项目数以及过滤的项目数。因此，如果上述作业在包含 100 行的批次上运行，每个项目都被读取并通过处理器，并且发现 10 个项目无效（它返回了 10 次 `null`），那么 `filter_count` 列的值将为 10。您可以从 `read_count` 看到读取了 100 个项目。`write_count` 列将反映有 10 个项目未通过，并显示为 90。

#### 将处理器链接在一起

有时您可能想要添加额外的处理，而这些处理与已设置的处理器目标不一致。Spring Batch 提供了一个便捷类 `CompositeItemProcessor<I,O>`，它将前一个过滤器的输出转发到下一个过滤器的输入。通过这种方式，您可以编写许多单一职责的 `ItemProcessor<I,O>`，然后根据需要重用它们并将它们链接起来。

```
@Bean
public CompositeItemProcessor compositeBankCustomerProcessor() {
return new CompositeItemProcessor(
creditScoreValidationProcessor(),
salaryValidationProcessor(),
customerEligibilityProcessor());
}
清单 8-20
CompositeItemProcessor 示例配置
```

该示例创建了一个非常简单的流程。第一个 `ItemProcessor` 将接收来自为此作业配置的 `ItemReader` 的任何输入，通常是一个 `Customer` 对象。它将检查 `Customer` 的信用评分，如果通过，则将 `Customer` 转发给薪资和收入验证处理器。如果一切正常，`Customer` 将被转发给资格处理器，在此处检查系统中是否存在重复项或任何其他无效数据。最后，它将被转发给写入器以添加到输出中。如果在三个处理器中的任何一个环节客户未通过检查，则正在执行的 `ItemProcessor` 可以简单地返回 `null` 并停止处理。

## 8-6\. 通过事务实现更好的运行

### 问题

您希望读取和写入操作是健壮的。理想情况下，它们应在适当的地方使用事务，并正确响应异常。

### 解决方案

事务能力建立在核心 Spring 框架已提供的一流支持之上。在相关的地方，Spring Batch 会暴露配置以便您可以控制它。在面向块处理的上下文中，它还提供了对提交频率、回滚语义等方面的许多控制。

### 工作原理



#### 事务

Spring 的核心框架为事务提供了一流的支持。你只需像在任何常规的 `JdbcTemplate` 或 `HibernateTemplate` 解决方案中那样，配置一个 `PlatformTransactionManager` 并将其引用提供给 Spring Batch 即可。在构建 Spring Batch 解决方案时，你将有机会控制步骤在事务中的行为。你已经看到了一些内建的事务支持。

所有这些示例中使用的配置都建立了一个 `DriverManagerDataSource` 和一个 `DataSourceTransactionManager` bean。然后，`PlatformTransactionManager` 和 `DataSource` 被注入到 `JobRepositoryFactoryBean` 中，而 `JobRepositoryFactoryBean` 又被注入到 `JobLauncher` 中，你正是使用 `JobLauncher` 来启动到目前为止的所有作业。这使得你的作业创建的所有元数据都能以事务方式写入数据库。

如果你看过前面的示例，你已经注意到对 `chunk` 方法的调用同时包含一个数字和一个事务管理器。还有一个只接受数字的 `chunk` 方法，但该方法已被弃用，并将在未来的 Spring Batch 版本中移除。如果你在步骤中使用了不带事务管理器的 `chunk` 方法，Spring Batch 默认会尝试从上下文中获取名为 `transactionManager` 的 `PlatformTransactionManager` 并使用它。如果你想显式配置，可以指定 `transactionManager` 属性。一个用于 JDBC 工作的简单事务管理器可能如下所示。

```
@Bean
protected Step step1(PlatformTransactionManager txManager) {
var name = "step1";
var builder = new StepBuilder(name, jobRepository);
return builder
.chunk(5)
.reader(csvFileReader())
.processor(userRegistrationValidationItemProcessor())
.writer(jdbcItemWriter())
.transactionManager(txManager)
.build();
}
清单 8-21
使用 PlatformTransactionManager 的步骤配置
```

然而，由于 `chunk(5)` 已被弃用，你应该优先使用 `chunk(5, transactionManager)`，而不是通过 `transactionManager` 属性再次设置。

从 `ItemReader` 读取的项目通常会被聚合。如果对 `ItemWriter` 的提交失败，聚合的项目会被保留并重新提交。这个过程是高效的，并且在大多数情况下都能正常工作。但在从事务性资源（如 JMS 队列或数据库）读取时，它会破坏语义。如果参与的事务（在此例中，是写入器的事务）失败，从消息队列的读取操作可以并且应该被回滚。

```
@Bean
protected Step step1(PlatformTransactionManager txManager) {
var name = "step1";
var builder = new StepBuilder(name, jobRepository);
return builder
.chunk(5, txManager)
.reader(csvFileReader()).readerIsTransactionalQueue()
.processor(userRegistrationValidationItemProcessor())
.writer(jdbcItemWriter())
.build();
}
清单 8-22
使用事务性队列的步骤配置
```

#### 回滚

处理简单情况（“读取 X 个项目，每 Y 个项目提交一次数据库事务”）很容易。Spring Batch 擅长于其鲁棒性，这种鲁棒性通过简单的配置选项来应对边界情况和失败情况。

如果对 `ItemWriter` 的写入失败，或者在处理过程中发生其他异常，Spring Batch 将回滚事务。这在大多数情况下是有效的处理方式。在某些场景下，你可能希望控制哪些异常情况会导致事务回滚。

使用基于 Java 的配置来启用回滚时，首先，该步骤需要是一个容错步骤，然后才能用它来指定不回滚的异常。首先，使用 `faultTolerant()` 获取一个容错步骤。接下来，可以使用 `skipLimit()` 方法指定在实际停止作业执行之前忽略的回滚次数。最后，可以使用 `noRollback()` 方法指定不会触发回滚的异常。要指定多个异常，你可以简单地链式调用 `noRollback()` 方法。

```
@Bean
protected Step step1(PlatformTransactionManager txManager) {
var name = "step1";
var builder = new StepBuilder(name, jobRepository);
return builder
.chunk(10, txManager)
.faultTolerant()
.noRollback(com.yourdomain.exceptions.YourBusinessException.class)
.reader(csvFileReader())
.processor(userRegistrationValidationItemProcessor())
.writer(jdbcItemWriter())
.build();
}
清单 8-23
包含回滚规则的示例配置
```

## 8-7\. 重试

### 问题

你正在处理一个功能需求，该功能可能会失败，但并非事务性的。或者它可能是事务性的但不可靠。你想要操作一个在尝试读取或写入时可能失败的资源。它可能因为网络连接问题、端点宕机或任何其他原因而失败。然而，你知道它很可能很快就会恢复，并且应该重试。

### 解决方案

使用 Spring Batch 的重试能力来系统地重试读取或写入操作。

### 工作原理

正如你在上一个技巧中看到的，使用 Spring Batch 处理事务性资源很容易。当涉及到瞬时或不可靠的资源时，需要采用不同的策略。这类资源通常是分布式的，或者表现出最终会自行解决的问题。有些资源（如 Web 服务）由于其分布式特性，本质上无法参与事务。有些产品可以在一个服务器上启动事务，并将事务上下文传播到分布式服务器并在那里完成它，尽管这非常罕见且效率低下。或者，如果你能使用分布式（“全局”或 XA）事务，也有很好的支持。然而，有时你可能正在处理一个不属于上述任何一种的资源。一个常见的例子可能是对远程服务的调用，例如 RMI 服务或 REST 端点。某些调用会失败，但在事务场景中重试后有可能成功。例如，导致 `org.springframework.dao.DeadlockLoserDataAccessException` 的数据库更新可能值得重试。

#### 配置步骤

最简单的例子是在步骤的配置中。在这里，你可以指定需要重试操作的异常类。与回滚异常一样，你可以用换行符或逗号分隔这个异常列表。

使用基于 Java 的配置来启用重试时，首先，该步骤需要是一个容错步骤，然后才能用它来指定重试限制和可重试的异常。首先，使用 `faultTolerant()` 获取一个容错步骤。接下来，可以使用 `retryLimit()` 方法指定重试尝试的次数。最后，可以使用 `retry` 方法指定触发重试的异常。要指定多个异常，你可以简单地链式调用 `retry` 方法。

```
@Bean
public Step step1(ItemReader reader,
ItemWriter writer,
PlatformTransactionManager txManager) {
var name = "User Registration CSV To DB Step";
var builder = new StepBuilder(name, jobRepository);
return builder
.chunk(5, txManager)
.faultTolerant()
.retryLimit(3).retry(DeadlockLoserDataAccessException.class)
.reader(reader)
.writer(writer)
.build();
}
清单 8-24
用于重试的步骤配置
```



#### 重试模板

或者，你也可以在自己的代码中利用 [Spring Retry](https://github.com/spring-projects/spring-retry) 对重试和恢复的支持。例如，你可以创建一个自定义的 `ItemWriter`，在其中实现重试功能，甚至可以为整个服务接口添加重试支持。

Spring Batch 通过 `RetryTemplate` 支持这些场景（与其众多其他模板类类似），它将你的逻辑与重试的细节隔离开来，让你可以像只尝试一次那样编写代码。让 Spring Batch 通过声明式配置来处理其他一切。

`RetryTemplate` 支持多种用例，其便捷的 API 可以将原本繁琐的重试/失败/恢复循环封装成简洁的单方法调用。

让我们看看来自配方 8-3 中关于如何编写自定义 ItemWriter 的简单 `ItemWriter` 的修改版本。该解决方案足够简单，理想情况下应该始终有效。然而，它未能处理服务的错误情况。在处理 RPC 时，始终要假设事情几乎不可能顺利进行；服务本身可能会暴露语义或系统违规。例如，重复的数据库键、无效的信用卡号等。无论服务是分布式的还是在虚拟机内，情况都是如此。

接下来，系统底层的 RPC 层也可能出错。以下是重写的代码，这次允许重试。

```
package com.apress.spring6recipes.springbatch;
import org.springframework.batch.item.Chunk;
import org.springframework.batch.item.ItemWriter;
import org.springframework.retry.RetryCallback;
import org.springframework.retry.support.RetryTemplate;
public class RetryableUserRegistrationServiceItemWriter
implements ItemWriter {
private final UserRegistrationService userRegistrationService;
private final RetryTemplate retryTemplate;
public RetryableUserRegistrationServiceItemWriter(UserRegistrationService usr,
RetryTemplate retryTemplate) {
this.userRegistrationService = usr;
this.retryTemplate = retryTemplate;
}
public void write(Chunk items)
throws Exception {
for (var userRegistration : items) {
retryTemplate.execute(context ->
userRegistrationService.registerUser(userRegistration));
}
}
}
清单 8-25
RetryableUserRegistrationServiceItemWriter 实现
```

如你所见，代码改动不大，但结果更加健壮。`RetryTemplate` 本身是在 Spring 上下文中配置的，尽管在代码中创建它也很简单。我之所以在 Spring 上下文中声明它，是因为创建对象时有一些配置空间，并且我尽量让 Spring 来处理配置。

`RetryTemplate` 最有用的设置之一是使用的 `BackOffPolicy`。`BackOffPolicy` 决定了 `RetryTemplate` 在重试之间应该等待多长时间。实际上，它还支持在每次失败尝试后增加重试间隔，以避免与其他尝试相同调用的客户端发生锁步。这在多个客户端可能同时尝试访问同一资源并可能引发竞态条件的情况下非常有用。还有其他 `BackOffPolicy`，包括一种以固定时间延迟重试的策略，称为 `FixedBackOffPolicy`。

```
@Bean
public RetryTemplate retryTemplate(BackOffPolicy backOffPolicy) {
var retryTemplate = new RetryTemplate();
retryTemplate.setBackOffPolicy(backOffPolicy);
return retryTemplate;
}
@Bean
public ExponentialBackOffPolicy backOffPolicy() {
var backOffPolicy = new ExponentialBackOffPolicy();
backOffPolicy.setInitialInterval(1000);
backOffPolicy.setMaxInterval(10000);
backOffPolicy.setMultiplier(2);
return backOffPolicy;
}
清单 8-26
RetryTemplate 配置
```

你已经配置了 `RetryTemplate` 的 `BackOffPolicy`，使得 `BackOffPolicy` 在首次重试前等待 1 秒（1000 毫秒）。后续尝试将加倍该值（增长受乘数影响）。它会一直持续到达到 `maxInterval`，之后所有后续重试间隔将趋于稳定，以一致的间隔进行重试。

#### 基于 AOP 的重试

另一种方法是使用 Spring Batch 提供的 AOP 通知器，它将包装那些成功无法保证的方法调用，就像你使用 `RetryTemplate` 所做的那样。在前面的示例中，你重写了 `ItemWriter<T>` 以使用该模板。另一种方法可能只是用这个重试逻辑来通知整个 `userRegistrationService` 代理。在这种情况下，代码可以恢复到原始示例中的方式，无需 `RetryTemplate`！

为此，你需要使用 `@Retryable` 注解来标记要重试的方法。要实现与显式使用 `RetryTemplate` 相同的效果，你需要添加以下内容。

```
@Retryable(backoff = @Backoff(delay = 1000, maxDelay = 10000, multiplier = 2))
public UserRegistration registerUser(UserRegistration registration) { ... }
清单 8-27
UserRegistrationService 上的基于注解的重试
```

仅添加此注解是不够的。你还需要在你的配置上使用 `@EnableRetry` 注解来启用注解处理。

```
@Configuration
@EnableBatchProcessing
@EnableRetry
@ComponentScan("com.apress.spring6recipes.springbatch")
@PropertySource("classpath:/batch.properties")
public class BatchConfiguration {
}
清单 8-28
基于注解的重试的配置
```

## 8-8\. 控制步骤执行

### 问题

你想控制步骤的执行方式，也许是为了通过引入并发或仅在条件为真时执行步骤来消除不必要的时间浪费。

### 解决方案

有多种方法可以改变作业的运行时配置文件，主要是通过控制步骤的执行方式：并发步骤、决策和顺序步骤。

### 工作原理

到目前为止，你已经探索了在作业中运行一个步骤。然而，几乎任何复杂程度的典型作业都会有多个步骤。步骤为其包含的 Bean 和逻辑提供了边界（事务性或非事务性）。一个步骤可以有自己的读取器、写入器和处理器。每个步骤都有助于决定下一步是什么。步骤是隔离的，并提供集中的功能，可以使用 Spring Batch 中更新的模式和配置选项，以非常复杂的工作流进行组装。事实上，如果你对业务流程管理（BPM）系统和工作流感兴趣，你即将看到的一些概念和模式会非常熟悉。BPM 提供了许多用于流程或作业控制的构造，与你在这里看到的类似。当你在纸上勾勒作业定义时，一个步骤通常对应一个要点。例如，一个用于加载每日销售数据并生成报告的批处理作业可能如下所示。

#### 每日销售报告作业

1.  将客户从 CSV 文件加载到数据库中。

2.  计算每日统计数据，并写入报告文件。

3.  向消息队列发送消息，通知外部系统每个新加载的客户已成功注册。

#### 顺序步骤

在前面的示例中，前两个步骤之间存在隐含的顺序：只有在所有注册完成后才能写入审计文件。这种关系是两个步骤之间的默认关系。一个步骤在另一个步骤之后发生。每个步骤都使用自己的执行上下文执行，并且仅共享一个父作业执行上下文和顺序。

```
@Bean
public Job nightlyRegistrationsJob () {
var name = "nightlyRegistrationsJob";
var builder = new JobBuilder(name, jobRegistry);
return builder
.start(loadRegistrations())
.next(reportStatistics())
.next(...)
.build();
}
}
清单 8-29
包含多个步骤的作业
```



#### 并发

Spring Batch 的第一个版本面向的是同一线程内的批处理，经过一些修改后，或许也能在虚拟机内部运行。当然，当时也有一些变通方法，但情况并不理想。

在这个示例作业的概要中，第一步必须在第二和第三步之前执行，因为后两步依赖于第一步。然而，后两步之间并不存在任何此类依赖关系。没有理由不能在发送 JMS 消息的同时写入审计日志。Spring Batch 提供了分支处理的能力，正是为了实现这种安排。

```
@Bean
public Job insertIntoDbFromCsvJob() {
var name = "insertIntoDbFromCsvJob";
var builder = new JobBuilder(name, jobRegistry);
return builder
.start(loadRegistrations())
.split(taskExecutor())
.add(
builder.flow(reportStatistics()),
builder.flow(sendJmsNotifications()))
.build();
}
清单 8-30
包含多个步骤并带有分支的作业
```

你可以在作业构建器上使用 `split()` 方法。要将一个步骤转换为一个流程，可以使用作业构建器的 `flow()` 方法。然后，要向流程中添加更多步骤，可以使用 `next()` 方法。`split()` 方法需要设置一个 `TaskExecutor`。有关调度和并发的更多信息，请参见技巧 1-23。

在这个例子中，没有任何限制阻止你在流程元素中包含多个步骤，也没有任何限制阻止你在分支元素之后添加更多步骤。分支元素，如同步骤元素一样，也接受一个 next 属性。

Spring Batch 提供了一种将处理任务卸载到另一个进程的机制。这种分布式处理需要某种持久、可靠的连接。这正好是 JMS 的完美应用场景，因为它极其稳定、支持事务、快速且可靠。Spring Batch 的支持是在一个稍高的层次上建模的，它基于 Spring Integration 的抽象层，用于 Spring Integration 通道。此支持并不在主 Spring Batch 代码库中；它可以在 `spring-batch-integration` 项目中找到。远程分块允许各个步骤像往常一样在主线程中读取和聚合项目。这个步骤被称为主节点。读取的项目被发送到运行在另一个进程中的 `ItemProcessor`/`ItemWriter`（这被称为从节点）。如果从节点是一个积极消费者，你就拥有了一种简单、通用的扩展机制：工作会立即被分发给尽可能多的 JMS 客户端。积极消费者模式指的是多个 JMS 客户端都消费同一个队列消息的配置。如果一个客户端消费了一条消息并正在忙于处理，其他空闲的客户端将获取该消息。只要有一个客户端空闲，消息就会被立即处理。

此外，Spring Batch 还支持使用一种称为分区（partitioning）的特性进行隐式扩展。这个特性很有趣，因为它是内置的，并且通常非常灵活。你可以将步骤实例替换为一个子类 `PartitionStep`，它知道如何协调分布式执行器，并维护步骤执行的元数据，从而消除了像“远程分块”技术那样需要持久通信媒介的需求。

这里的功能也非常通用。可以想象，它可以与任何类型的网格结构技术一起使用，例如 GridGain 或 Hadoop。Spring Batch 仅附带了一个 `TaskExecutorPartitionHandler`，它使用 `TaskExecutor` 策略在多个线程中执行步骤。这个简单的改进本身就足以成为使用此功能的理由！但是，如果你确实有更高的需求，也可以对其进行扩展。

#### 带状态的条件步骤

使用给定作业或步骤的 `ExitStatus` 来决定下一步是最简单的条件流程示例。Spring Batch 通过使用 `stop`、`next`、`fail` 和 `end` 元素来促进这一点。默认情况下，假设没有干预，一个步骤的 `ExitStatus` 将与其 `BatchStatus` 匹配，`BatchStatus` 是一个属性，其值定义为一个枚举（前面提到的 `BatchStatus`），可以是以下任意一个：`COMPLETED`、`STARTING`、`STARTED`、`STOPPING`、`STOPPED`、`FAILED`、`ABANDONED` 或 `UNKNOWN`。

让我们看一个示例，该示例根据前一步骤的成功与否来执行两个步骤中的一个。

```
@Bean
public Job insertIntoDbFromCsvJob() {
var name = "User Registration Import Job";
var builder = new JobBuilder(name, jobRegistry);
return builder
.start(step1())
.on("COMPLETED").to(step2())
.on("FAILED").to(failureStep())
.build();
}
清单 8-31
包含条件步骤的作业
```

也可以提供通配符。如果你想确保对任意数量的 `BatchStatus` 都有某种特定行为，也许是与一个仅匹配单一 `BatchStatus` 的更具体的 next 元素配合使用，这会很有用。

```
@Bean
public Job insertIntoDbFromCsvJob() {
var name = "User Registration Import Job";
var builder = new JobBuilder(name, jobRegistry);
return builder
.start(step1())
.on("COMPLETED").to(step2())
.on("*").to(failureStep())
.build();
}
清单 8-32
包含条件步骤并带有全捕获的作业
```

在这个例子中，你指示 Spring Batch 根据任何未处理的 `ExitStatus` 执行某个步骤。另一种选择是直接停止处理，并将 `BatchStatus` 设置为 `FAILED`。你可以使用 `fail` 元素来实现这一点。对前面例子进行一个不那么激进的改写可能如下所示。

```
@Bean
public Job insertIntoDbFromCsvJob() {
var name = "User Registration Import Job";
var builder = new JobBuilder(name, jobRegistry);
return builder
.start(step1())
.on("COMPLETED").to(step2())
.on("FAILED").fail()
.build();
}
清单 8-33
包含条件步骤或失败的作业
```

在所有这些例子中，你都是在响应 Spring Batch 框架提供的标准 `BatchStatus`。但是，你也可以定义自己的 `ExitStatus`。例如，如果你希望整个作业失败，并带有一个自定义的 `ExitStatus` "MAN DOWN"，你可以这样做。

```
@Bean
public Job insertIntoDbFromCsvJob() {
var name = "User Registration Import Job";
var builder = new JobBuilder(name, jobRegistry);
return builder
.start(step1())
.on("COMPLETED").to(step2())
.on("FAILED").end("MAN DOWN")
.build();
}
清单 8-34
包含条件步骤或结束步骤的作业
```

最后，如果你只想以 `BatchStatus` 为 `COMPLETED` 来结束处理，可以使用 `end()` 方法。这是一种显式结束流程的方式，就像流程已经运行完所有步骤且未发生任何错误一样。

```
@Bean
public Job insertIntoDbFromCsvJob() {
var name = "User Registration Import Job";
var builder = new JobBuilder(name, jobRegistry);
return builder
.start(step1())
.on("COMPLETED").end()
.on("FAILED").to(errorStep())
.build();
}
清单 8-35
包含条件步骤的作业
```



#### 带决策的条件步骤

如果你希望根据比作业的 `ExitStatus` 更复杂的逻辑来改变执行流程，可以通过使用决策元素并为其提供 `JobExecutionDecider` 的实现来帮助 Spring Batch。

```
package com.apress.spring6recipes.springbatch;
import org.springframework.batch.core.JobExecution;
import org.springframework.batch.core.StepExecution;
import org.springframework.batch.core.job.flow.FlowExecutionStatus;
import org.springframework.batch.core.job.flow.JobExecutionDecider;
import java.util.concurrent.ThreadLocalRandom;
public class HoroscopeDecider implements JobExecutionDecider {
private boolean isMercuryIsInRetrograde() {
return ThreadLocalRandom.current().nextDouble() > .9;
}
public FlowExecutionStatus decide(JobExecution jobExecution, StepExecution stepExecution) {
if (isMercuryIsInRetrograde()) {
return FlowExecutionStatus.FAILED;
}
return FlowExecutionStatus.COMPLETED;
}
}
清单 8-36
HoroscopeDecider 类
```

剩下的就是配置了。

```
@Bean
public Job insertIntoDbFromCsvJob(JobExecutionDecider decider) {
var name = "用户注册导入作业";
var builder = new JobBuilder(name, jobRegistry);
return builder
.start(step1())
.next(decider)
.on("MERCURY_IN_RETROGRADE").to(step2())
.on("COMPLETED").to(step3())
.build();
}
清单 8-37
基于 JobExecutionDecider 的条件步骤作业
```

## 8-9. 启动作业

### 问题

Spring Batch 支持哪些部署场景？Spring Batch 如何启动？Spring Batch 如何与系统调度器（如 cron 或 autosys）或 Web 应用程序协同工作？

### 解决方案

Spring Batch 在 Spring 运行的所有环境中都能良好工作：你的 `public static void main` 方法、Web 应用程序，任何地方！不过，某些用例具有独特的挑战性：例如，将 Spring Batch 与 HTTP 响应放在同一个线程中运行很少是可行的，因为这可能会导致执行停滞。Spring Batch 正是针对这种情况支持异步执行。Spring Batch 还提供了一个便捷类，可以轻松地与 cron 或 autosys 配合使用，以支持启动作业。此外，Spring 优秀的调度器命名空间为调度作业提供了很好的机制。

### 工作原理

在开始创建解决方案之前，了解有哪些选项可用于部署和运行这些解决方案非常重要。所有解决方案至少需要一个作业和一个 `JobLauncher`。你已经在之前的配方中配置了这些组件。作业在你的 Spring 应用上下文中配置，稍后你将看到。从 Java 代码启动 Spring Batch 解决方案的最简单示例大约需要五行 Java 代码，如果你已经获得了 `ApplicationContext` 的句柄，则只需要三行！

```
package com.apress.spring6recipes.springbatch;
public class Main {
public static void main(String[] args) throws Throwable {
var context = new AnnotationConfigApplicationContext(BatchConfiguration.class)
var jobLauncher = ctx.getBean("jobLauncher", JobLauncher.class);
var job = ctx.getBean("myJobName". Job.class);
var jobExecution = jobLauncher.run(job, new JobParameters());
}
}
清单 8-38
启动作业的 Main 类
```

如你所见，获取之前配置的 `JobLauncher` 引用，然后用它来启动一个带有给定 `JobParameters` 的 `Job` 实例。结果是一个 `JobExecution`。你可以查询 `JobExecution` 以获取有关 `Job` 状态的信息，包括其退出状态和运行时状态。

```
var jobExecution = jobLauncher.run(job, jobParameters);
var batchStatus = jobExecution.getStatus();
while(batchStatus.isRunning()) {
System.out.println( "仍在运行...");
Utils.sleep( 5, SECONDS );
}
清单 8-39
检查作业执行状态的代码
```

你还可以获取 `ExitStatus`。

```
System.out.printf( "退出代码: %s%n", jobExecution.getExitStatus().getExitCode());
清单 8-40
读取退出状态的代码
```

`JobExecution` 还提供了许多其他非常有用的信息，例如 `Job` 的创建时间、开始时间、最后更新日期和结束时间——所有这些都以 `java.time.LocalDateTime` 实例的形式提供。如果你希望将作业与数据库关联起来，则需要 `jobInstance` 和 ID。

```
var jobInstance = jobExecution.getJobInstance();
System.out.printf( "作业实例 ID: %d%n", jobInstance.getId());
清单 8-41
读取 jobInstance ID 的代码
```

在我们的简单示例中，我们使用了一个空的 `JobParameters` 实例。在实践中，这只会生效一次。Spring Batch 基于参数构建一个唯一键，并用它来唯一标识给定 `Job` 的一次运行与另一次运行。你将在下一个配方中详细了解如何为 `Job` 设置参数。

#### 从 Web 应用程序启动

从 Web 应用程序启动作业需要稍微不同的方法，因为客户端线程（通常是一个 HTTP 请求）通常无法等待批处理作业完成。理想的解决方案是，当从 Web 层的控制器或操作中启动时，作业能够异步执行，而无需客户端线程等待。Spring Batch 通过使用 Spring 的 `TaskExecutor` 来支持这种场景。这需要对 `JobLauncher` 的配置进行简单的更改，尽管 Java 代码可以保持不变。在这里，我们将使用一个 `SimpleAsyncTaskExecutor`，它将生成一个执行线程并管理该线程而不会阻塞。

```
package com.apress.spring6recipes.springbatch.config;
@Configuration
@EnableBatchProcessing
@ComponentScan("com.apress.springrecipes.springbatch")
@PropertySource("classpath:/batch.properties")
public class BatchConfiguration {
@Bean
public SimpleAsyncTaskExecutor taskExecutor() {
return new SimpleAsyncTaskExecutor();
}
}
清单 8-42
包含 TaskExecutor 的批处理配置
```

默认情况下，Spring Batch 会查找名为 `taskExecutor` 的 `TaskExecutor`，如果找到具有该名称的 Bean，则会将其用作其选择的 `TaskExecutor`。如果由于某种原因你无法使用名为 `taskExecutor` 的 `TaskExecutor`，你可以通过 `@EnableBatchProcessing` 注解的 `taskExecutorRef` 属性传递要使用的 `TaskExecutor` 的名称。

```
@Configuration
@EnableBatchProcessing(taskExecutorRef = "customTaskExecutor")
@ComponentScan("com.apress.spring6recipes.springbatch")
@PropertySource("classpath:/batch.properties")
public class BatchConfiguration {
@Bean
public SimpleAsyncTaskExecutor customTaskExecutor() {
return new SimpleAsyncTaskExecutor();
}
}
清单 8-43
包含自定义命名 TaskExecutor 的批处理配置
```



#### 从命令行运行

另一种常见用例是通过系统调度器（如 cron、autosys 甚至 Windows 事件调度器）部署批处理进程。Spring Batch 提供了一个便捷类，其参数包括应用程序上下文的名称（包含运行作业所需的一切）以及作业 bean 本身的名称。还可以提供其他参数来对作业进行参数化。这些参数必须采用 `name=value` 的形式。假设你已经设置好类路径，在命令行（Linux/Unix 系统）上调用此类的示例如下所示。

```
java -cp "userjob.jar:libs/*" org.springframework.batch.core.launch.support.CommandLineJobRunner \
com.apress.spring6recipes.springbatch.confug.UserJob \
insertIntoDbFromCsvJob date=`date +%m/%d/%Y` time=`date +%H`
清单 8-44
启动的 Shell 命令
```

`org.springframework.batch.core.launch.support.CommandLineJobRunner` 甚至会返回系统错误代码（0 表示成功，1 表示失败，2 表示加载批处理作业时出现问题），以便 shell（例如大多数系统调度器使用的 shell）能够对失败做出反应或采取相应措施。通过创建并声明一个实现 `ExitCodeMapper` 接口的顶层 bean，可以返回更复杂的返回代码，在该 bean 中，你可以指定将退出状态消息转换为基于整数的错误代码（shell 在进程退出时会看到这些代码）的更实用的转换方式。

#### 按计划运行

Spring 支持调度框架（另请参阅配方 10-5 和 10-6）。该框架非常适合运行 Spring Batch。首先，让我们修改现有的应用程序上下文配置，通过 `@EnableScheduling` 注解并添加 `ThreadPoolTaskScheduler` 来启用调度。

```
package com.apress.springrecipes.springbatch.config;
@Configuration
@EnableBatchProcessing
@ComponentScan("com.apress.springrecipes.springbatch")
@PropertySource("classpath:/batch.properties")
@EnableScheduling
@EnableAsync
public class BatchConfiguration {
@Bean
public ThreadPoolTaskScheduler taskScheduler() {
var taskScheduler = new ThreadPoolTaskScheduler();
taskScheduler.setThreadGroupName("batch-scheduler");
taskScheduler.setPoolSize(10);
return taskScheduler;
}
}
清单 8-45
包含 TaskScheduler 的 BatchConfiguration
```

这些导入启用了最简单的调度支持。前面的注解确保 `com.apress.spring6recipes.springbatch` 包下的任何 bean 都会根据需要被配置和调度。我们的调度器 bean 如下所示。

```
package com.apress.spring6recipes.springbatch.scheduler;
import org.springframework.batch.core.Job;
import org.springframework.batch.core.JobParametersBuilder;
import org.springframework.batch.core.launch.JobLauncher;
import org.springframework.scheduling.annotation.Scheduled;
import org.springframework.stereotype.Component;
import java.time.LocalDate;
import java.time.LocalDateTime;
import java.time.ZoneId;
import java.util.Date;
@Component
public class JobScheduler {
private final JobLauncher jobLauncher;
private final Job job;
public JobScheduler(JobLauncher jobLauncher, Job job) {
this.jobLauncher = jobLauncher;
this.job = job;
}
public void runRegistrationsJob(LocalDateTime date) throws Exception{
System.out.println("Starting job at " + date.toString());
var jobParametersBuilder = new JobParametersBuilder();
jobParametersBuilder.addLocalDateTime("date", date);
jobParametersBuilder.addString("input.file", "registrations");
var jobParameters = jobParametersBuilder.toJobParameters();
var jobExecution = jobLauncher.run(job, jobParameters);
var exitcode = jobExecution.getExitStatus().getExitCode();
System.out.printf("jobExecution finished, exit code: %s%n", exitcode);
}
@Scheduled(fixedDelay = 10_0000)
public void runRegistrationsJobOnASchedule() throws Exception {
runRegistrationsJob(LocalDateTime.now());
}
}
清单 8-46
JobScheduler 类
```

这里没有什么特别新奇的地方；这是一个很好的研究，展示了 Spring 框架的不同组件如何很好地协同工作。由于 `@Component` 注解，该 bean 被识别并成为应用程序上下文的一部分，我们在配置类中通过 `@ComponentScan` 注解启用了该功能。`UserJob` 类中只有一个 `Job`，也只有一个 `JobLauncher`，因此我们只需将它们自动装配到我们的 bean 中。最后，启动批处理运行的逻辑位于 `runRegistrationsJob(java.time.LocalDateTime date)` 方法内部。可以从任何地方调用此方法。我们此功能的唯一客户端是计划方法 `runRegistrationsJobOnASchedule`。框架将根据 `@Scheduled` 注解规定的时间线为我们调用此方法。

对于此类事情还有其他选择；传统上，在 Java 和 Spring 世界中，这类问题非常适合使用 Quartz。现在可能仍然如此，因为 Spring 的调度支持在设计上不如 Quartz 那样可扩展。如果你所处的环境需要更传统、对运维友好的调度工具，当然还有像 cron、autosys 和 BMC 这样的老牌工具。

## 8-10. 参数化作业

### 问题

前面的示例运行得足够好，但在灵活性方面还有所欠缺。要将批处理代码应用于其他文件，你必须编辑配置并将文件名硬编码进去。能够对批处理解决方案进行参数化将非常有帮助。

### 解决方案

使用 `JobParameters` 对作业进行参数化，然后通过 Spring Batch 的表达式语言或 API 调用，这些参数即可供你的步骤使用。

### 工作原理

#### 使用参数启动作业

一个作业是 `JobInstance` 的原型。`JobParameters` 用于提供一种标识作业唯一运行（一个 `JobInstance`）的方式。这些 `JobParameters` 允许你向批处理进程提供输入，就像在 Java 中使用方法定义一样。你在前面的示例中已经见过 `JobParameters`，但未深入了解。`JobParameters` 对象是在你使用 `JobLauncher` 启动作业时创建的。要启动一个名为 `dailySalesFigures` 的作业，并为其提供作业要处理的日期，你可以编写如下代码。

```
package com.apress.spring6recipes.springbatch;
import java.time.LocalDateTime;
import org.springframework.batch.core.Job;
import org.springframework.batch.core.JobParametersBuilder;
import org.springframework.batch.core.launch.JobLauncher;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import com.apress.spring6recipes.springbatch.config.BatchConfiguration;
public class Main {
public static void main(String[] args) throws Throwable {
var cfg = BatchConfiguration.class;
try (var ctx = new AnnotationConfigApplicationContext(cfg)) {
var jobLauncher = ctx.getBean(JobLauncher.class);
var job = ctx.getBean("dailySalesFigures", Job.class);
var builder = new JobParametersBuilder();
var parameters = builder.addLocalDateTime("date", LocalDateTime.now())
.toJobParameters();
jobLauncher.run(job, parameters);
}}
}
清单 8-47
Main 类
```



#### 访问 JobParameters

从技术上讲，你可以通过任意一个 `ExecutionContexts`（步骤级或作业级）来获取 `JobParameters`。获取后，你可以通过调用 `getLong()`、`getString()` 等方法，以类型安全的方式访问这些参数。一个简单的方法是在 `@BeforeStep` 事件中进行绑定，保存 `StepExecution`，然后通过它遍历参数。由此，你可以检查参数并对其进行任何操作。下面我们结合之前编写的 `ItemProcessor` 来看一下具体实现。

```
// ...
private StepExecution stepExecution;
@BeforeStep
public void saveStepExecution(StepExecution stepExecution) {
this.stepExecution = stepExecution;
}
public UserRegistration process(UserRegistration input) throws Exception {
var params =  stepExecution.getJobParameters().getParameters();
// 遍历所有参数
for (var key : params.keySet()) {
var value = params.get(key).getValue().toString();
System.out.printf("%s=%s%n", key, value);
));
}
// 以类型安全的方式访问特定参数
var date = stepExecution.getJobParameters().getDate("date");
// 等等 ...
}
清单 8-48
通过 StepExecution 访问 JobParameters
```

事实证明，这种方法的价值有限。在 80% 的情况下，你需要将作业启动时的参数绑定到应用上下文中的 Spring Bean 上。这些参数仅在运行时可用，而应用上下文中的步骤是在设计时配置的。这种情况在很多地方都会出现。之前的示例展示了带有硬编码路径的 `ItemWriter` 和 `ItemReader` 实例。除非你想让文件名参数化，否则这样工作得很好。但如果你打算只使用一次作业，这几乎不可接受！

Spring 框架的核心特性包含一个增强的表达式语言，Spring Batch 利用它来将参数的绑定推迟到正确的时间——或者，在这种情况下，推迟到 Bean 处于正确的作用域时。Spring Batch 为此提供了“步骤”作用域。让我们看看如何重写之前的示例，为 `ItemReader` 的资源使用参数化的文件名：

```
@Bean
@StepScope
public ItemReader csvFileReader(
@Value("file:${user.home}/batches/#{jobParameters['input.fileName']}.csv")
Resource input) { ... }
```

你所做的只是将 Bean（即 `FlatFileItemReader`）的作用域限定为步骤的生命周期（此时那些 `JobParameters` 将正确解析），然后使用 EL 语法将路径参数化以使其生效。

## 8-11\. 总结

本章向你介绍了批处理的概念、其部分历史，以及它为何适用于现代架构。你学习了 Spring Batch（Spring 的批处理框架），以及如何在批处理作业中使用 `ItemReader` 和 `ItemWriter` 实现进行读写操作。你根据需要编写了自己的 `ItemReader` 和 `ItemWriter` 实现，并了解了如何控制作业内步骤的执行。

