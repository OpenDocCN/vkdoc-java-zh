# 6. 高级 GCP 服务

在本章中，我们将探讨 GCP 高级服务的实际应用。内容涵盖关键主题，如大数据与机器学习、Cloud Pub/Sub、Cloud Dataflow、Cloud Dataproc 和 Cloud Functions。通过实际示例，本章将展示如何使用这些服务构建可扩展、弹性且数据驱动的应用程序。到本章结束时，读者将了解如何利用 Cloud Pub/Sub、Cloud Dataflow、Cloud Dataproc 和 Cloud Functions 在 GCP 上设计和实现强大的解决方案。

让我们来了解 GCP 的高级服务。

## GCP 高级服务

Google Cloud Platform (GCP) 提供了许多可用于构建和部署复杂应用程序的高级服务。我们将在本节中讨论其中一些服务。

### 大数据与机器学习

**Cloud Dataflow** 允许您为批处理和流式数据构建、部署和执行数据管道。它可以使用批处理和流式执行模型处理数据，并可用于数据集成、数据转换和数据分析。例如，您可以使用 Cloud Dataflow 处理来自 Twitter 等实时流式服务的数据，并对推文进行情感分析。

**Cloud Dataproc** 允许您创建、配置和管理虚拟机集群，这些虚拟机预配置了 Apache Hadoop 和 Apache Spark 等流行的大数据工具。它可以借助 Hadoop 和 Spark 作业处理大量数据。例如，您可以使用 Cloud Dataproc 处理大型客户记录数据集，并执行客户细分。

**Cloud Datalab** 是 GCP 上用于数据探索、可视化和机器学习的交互式工具。它运行在 Jupyter Notebook 之上，允许您连接到 BigQuery 和 Cloud Storage 等各种 GCP 服务。例如，您可以使用 Cloud Datalab 连接到 BigQuery 数据集，执行 SQL 查询，可视化结果，并构建机器学习模型来预测客户流失。

**Vertex AI** 允许您在 GCP 上构建、部署和管理机器学习模型。它提供了一个完全托管的 TensorFlow 环境，还允许您在机器集群上训练模型，并将模型部署到云、本地和边缘设备等多种位置。例如，您可以使用 Cloud Machine Learning Engine 在大型图像数据集上训练深度学习模型，并将其部署到移动应用程序中执行物体识别。



### Kubernetes 与容器

**Kubernetes Engine** 让您能够在 GCP 上轻松创建、配置和管理 Kubernetes 集群。它提供了自动扩缩、负载均衡和自动升级等功能。例如，您可以使用 Kubernetes Engine 来部署和管理基于微服务的应用。

**Cloud Run** 让您能够在 GCP 上轻松部署容器化应用。它提供了自动扩缩、负载均衡和自动更新等功能。例如，您可以使用 Cloud Run 来部署一个处理图像并执行目标检测的网络服务。

**Container Registry** 让您能够在 GCP 上存储和管理容器镜像。它提供了镜像存储、版本控制和访问控制等功能。例如，您可以使用 Container Registry 来存储和管理基于微服务的应用的容器镜像，并轻松将其部署到 Kubernetes Engine 或 Cloud Run。

**Cloud Build** 让您能够自动化应用的构建、测试和部署。它可用于构建容器镜像、部署到各种环境，并与 Container Registry 和 Kubernetes Engine 等其他 GCP 服务集成。例如，您可以使用 Cloud Build 来自动化基于微服务的应用的构建、测试和部署流水线。

### 安全与身份

**Cloud Identity and Access Management** (IAM) 使您能够管理对 GCP 资源的访问权限。它提供了基于角色的访问控制、身份感知代理和安全密钥强制执行等功能。例如，您可以使用 Cloud IAM 为 BigQuery 数据集设置细粒度的访问控制，仅允许特定用户查询数据。

**Cloud Key Management Service** (KMS) 让您能够在 GCP 上管理和使用加密密钥。它提供了密钥存储、密钥轮换和密钥版本控制等功能。例如，您可以使用 Cloud KMS 加密 Cloud Storage 中的静态数据，并使用加密密钥加密传输中的数据。

**Cloud Security Command Center** (CSCC) 帮助您更深入地了解 GCP 环境中的安全状况。它提供了资产清单、漏洞管理和威胁检测等功能。例如，您可以使用 Cloud CSCC 监控 GCP 资源是否存在安全威胁和漏洞，并采取相应措施。

以上只是 GCP 提供的众多高级服务中的几个例子。每项服务都有其特定的用例，并且可以与其他服务结合使用，在 GCP 上构建强大且可扩展的解决方案。

## 常用的 GCP 高级服务

在本节中，我们将详细讨论四项 GCP 服务。这四项 GCP 服务的使用频率高于前述服务，并且包含在大多数应用中。

*   Cloud Pub/Sub

*   Cloud Dataflow

*   Cloud Dataproc

*   Cloud Functions

### Cloud Pub/Sub

Cloud Pub/Sub 是 GCP 提供的一项消息传递服务，允许您在独立系统之间发送和接收消息。它提供了消息排序、至少一次投递以及支持推送和拉取消息等功能。

Cloud Pub/Sub 的一个用例是在分布式系统中解耦微服务。您可以使用 Pub/Sub 在不同的微服务之间发送消息，使它们能够在不紧密耦合的情况下进行通信。这可以使您的系统更能适应变化，并且更易于扩展。

另一个用例是实时数据处理。您可以使用 Cloud Pub/Sub 接收高容量数据流（例如日志或传感器数据），然后使用 Dataflow 或 Cloud Functions 等独立服务实时处理这些数据。

您还可以将 Cloud Pub/Sub 与 Cloud Storage、BigQuery 和 Cloud Dataflow 等其他 GCP 服务结合使用。例如，当新文件上传到 Cloud Storage 时，您可以使用 Cloud Pub/Sub 触发 Cloud Functions，然后由 Cloud Functions 处理该文件并将数据加载到 BigQuery 中。

Cloud Pub/Sub 是一项功能强大的消息传递服务，可通过多种方式实现实时数据处理、解耦微服务，并使您的系统更能适应变化。

#### 使用 Cloud Pub/Sub 的理由

Cloud Pub/Sub 成为 GCP 中一项重要服务的原因有几点。

*   *解耦*：Cloud Pub/Sub 允许您通过在系统之间发送和接收消息来解耦系统。这使您可以更改或更新一个系统而不影响其他系统，从而使您的整体系统更能适应变化。

*   *实时处理*：Cloud Pub/Sub 允许您实时处理高容量数据流。这对于日志分析、监控和告警等任务非常有用。

*   *可扩展性*：Cloud Pub/Sub 旨在处理高吞吐量和低延迟，使其适用于处理大量数据和流量。

*   *灵活性*：Cloud Pub/Sub 同时支持推送和拉取消息，允许您为用例选择最合适的消息传递模型。

*   *集成性*：Cloud Pub/Sub 可以与 Cloud Storage、BigQuery 和 Cloud Dataflow 等其他 GCP 服务集成，使您能够构建强大且高效的数据处理流水线。

*   *成本效益*：Cloud Pub/Sub 是一项经济高效的服务，仅根据您发布和消费的消息收费。

所有这些特性使 Cloud Pub/Sub 成为一项功能强大的消息传递服务，可用于各种用例，以实现实时数据处理、解耦微服务，并使您的系统更能适应变化。

#### 使用 Cloud Pub/Sub 的缺点

与任何服务一样，Cloud Pub/Sub 也存在一些缺点。

*   *延迟*：虽然 Cloud Pub/Sub 旨在处理高吞吐量和低延迟，但由于网络状况或消息大小等因素，某些用例可能仍会经历较高的延迟。

*   *持久性*：虽然 Cloud Pub/Sub 保证消息的至少一次投递，但不保证精确一次投递。这意味着在某些情况下，消息可能会被多次投递，从而导致重复。

*   *复杂性*：Cloud Pub/Sub 的设置和管理可能很复杂，尤其是在大规模、高吞吐量的用例中。这需要对其功能以及如何有效使用它们有很好的理解。

*   *成本*：虽然 Cloud Pub/Sub 是一项经济高效的服务，但对于涉及大量数据传输和消息处理的大规模、高吞吐量用例，它可能会变得昂贵。

*   *有限的保留期*：默认情况下，Cloud Pub/Sub 将消息保留 7 天，之后将被删除。如果您需要将消息保留更长时间，则需要显式配置。

*   *对大消息的支持有限*：可以发送到主题的消息最大大小为 10 MB。因此，如果您需要传输大消息，则需要拆分它们或考虑其他方案。

*   *Pub/Sub 排序*：Cloud Pub/Sub 的排序可能导致延迟增加和吞吐量降低。

在计划为您的用例使用 Cloud Pub/Sub 时，了解这些限制非常重要。它可能不是所有类型问题的最佳解决方案，但如果使用得当，它是一项强大的服务。



#### Cloud Pub/Sub 不适用场景

Cloud Pub/Sub 的一个错误使用场景是，当低延迟和精确一次投递是关键需求时，但 Cloud Pub/Sub 并不保证精确一次投递，并且可能存在一定的延迟。

另一个错误使用场景是，当数据需要长期存储时，但 Cloud Pub/Sub 默认仅保留消息 7 天。如果你需要更长时间地保留消息，则需要显式配置。

Cloud Pub/Sub 可能不是最佳选择的另一种情况是，当你拥有少量但负载较大的消息时，因为 Cloud Pub/Sub 的最大消息大小为 10 MB。

此外，如果你不需要实时处理数据流，并且可以容忍一定的延迟，那么 Cloud Storage 和 BigQuery 等其他服务可能更合适。

需要理解的是，Cloud Pub/Sub 是一项强大的服务，但它并非万能解决方案，可能并非适用于所有场景。了解你的使用场景需求，并选择合适的 GCP 服务来满足这些需求至关重要。

#### 在 Java 应用程序中使用 Cloud Pub/Sub

清单 6-1 展示了如何在 Java 应用程序中使用 Cloud Pub/Sub 的示例。步骤如下：

1.  首先，在 GCP 控制台中创建一个项目，并启用 Cloud Pub/Sub API。

2.  接下来，在 Cloud Pub/Sub 控制台中创建一个主题和一个订阅。

3.  在你的 Java 应用程序中，将以下依赖项添加到 `pom.xml` 文件中。

```
    com.google.cloud
    google-cloud-pubsub
    1.103.0

清单 6-1
    在 Java 应用程序中使用 Cloud Pub/Sub
    ```

4.  如清单 6-2 所示，创建一个 Pub/Sub 客户端并设置项目 ID 和凭据。项目 ID 和凭据不应硬编码在生产代码中，应遵循最佳实践进行存储和访问。

```
    import com.google.cloud.pubsub.v1.Publisher;
    import com.google.cloud.pubsub.v1.Subscriber;
    import com.google.cloud.pubsub.v1.TopicAdminClient;
    import com.google.cloud.pubsub.v1.SubscriptionAdminClient;
    import com.google.pubsub.v1.ProjectTopicName;
    import com.google.pubsub.v1.ProjectSubscriptionName;
    import com.google.auth.oauth2.GoogleCredentials;
    import java.io.FileInputStream;
    GoogleCredentials credentials = GoogleCredentials.fromStream(new FileInputStream("path/to/credentials.json"))
    .createScoped(Collections.singletonList("https://www.googleapis.com/auth/pubsub"));
    String projectId = "your-project-id";
    String topicId = "your-topic-id";
    String subscriptionId = "your-subscription-id";
    ProjectTopicName topicName = ProjectTopicName.of(projectId, topicId);
    ProjectSubscriptionName subscriptionName = ProjectSubscriptionName.of(projectId, subscriptionId);
    TopicAdminClient topicAdminClient = TopicAdminClient.create(credentials);
    SubscriptionAdminClient subscriptionAdminClient = SubscriptionAdminClient.create(credentials);
    清单 6-2
    创建 Pub/Sub 客户端并设置项目 ID 和凭据
    ```

5.  使用发布者向主题发布消息，如清单 6-3 所示。

```
    Publisher publisher = Publisher.newBuilder(topicName).build();
    ByteString data = ByteString.copyFromUtf8("Hello, Cloud Pub/Sub!");
    com.google.pubsub.v1.PubsubMessage pubsubMessage = com.google.pubsub.v1.PubsubMessage.newBuilder().setData(data).build();
    ApiFuture future = publisher.publish(pubsubMessage);
    String messageId = future.get();
    System.out.println("Published message with ID: " + messageId);
    publisher.shutdown();
    清单 6-3
    使用发布者发布消息
    ```

6.  使用订阅者从订阅接收消息，如清单 6-4 所示。

```
    Subscriber subscriber = Subscriber.newBuilder(subscriptionName, new MessageReceiver()).build();
    subscriber.startAsync();
    class MessageReceiver implements MessageReceiver {
    @Override
    public void receiveMessage(com.google.pubsub.v1.PubsubMessage message, AckReplyConsumer consumer) {
    System.out.println("Received message: " + message.getData().toStringUtf8());
    consumer.ack();
    }
    }
    清单 6-4
    使用订阅者接收消息
    ```

7.  最后，使用完毕后，不要忘记通过删除主题和订阅来清理资源，如清单 6-5 所示。

```
    topicAdminClient.deleteTopic(topicName);
    subscriptionAdminClient.deleteSubscription(subscriptionName);
    清单 6-5
    删除主题和订阅
    ```

这是一个关于如何在 Java 应用程序中使用 Cloud Pub/Sub 发布和接收消息的基本示例。在实际应用中，你可能需要处理错误、重试和其他边界情况。你可能还需要使用 Cloud Pub/Sub API 提供的其他功能，例如批处理、流量控制和消息属性。

此外，你可能需要将 Cloud Pub/Sub 与其他 GCP 服务（如 Cloud Dataflow、Cloud Functions 和 BigQuery）结合使用，以实时处理和分析数据流。

本节中的代码示例使用了默认设置，这些设置可能不适用于你的特定场景。你应该仔细阅读文档，并为你的场景选择合适的设置，例如消息保留、排序和投递语义。

下一节，让我们运行 Cloud Dataflow。



### Cloud Dataflow

Cloud Dataflow 是一项在 Google Cloud Platform 上用于数据处理的完全托管服务。它可用于创建数据管道，处理来自各种来源（例如 Cloud Storage、BigQuery 和 Cloud Pub/Sub）的大量数据，并将结果输出到各种目标（例如 Cloud Storage、BigQuery 和 Cloud Datastore）。它同时支持批处理和流式数据处理。

以下是一个示例，说明如何使用 Cloud Dataflow 处理存储在 Cloud Storage 存储桶中的一批数据：

1.  使用 Dataflow API 或 Cloud SDK 创建一个新的 Cloud Dataflow 作业。

2.  定义作业的输入源和输出目标。例如，您可以使用 `TextIO` 类从 Cloud Storage 存储桶读取文本文件，并将输出写入另一个存储桶。

3.  使用 Dataflow SDK 定义数据处理逻辑。例如，您可以使用 `ParDo` 类对数据执行转换，例如过滤、映射或聚合。

4.  运行 Dataflow 作业。

清单 6-6 展示了一个简单管道的示例，该管道从 Cloud Storage 存储桶读取数据，对数据执行转换，并将输出写入另一个存储桶：

```
Pipeline pipeline = Pipeline.create();
PCollection input = pipeline.apply(TextIO.read().from("gs://input-bucket/*.txt"));
PCollection output = input
.apply("ExtractWords", ParDo.of(new ExtractWordsFn()))
.apply("CountWords", Count.perElement());
output.apply(TextIO.write().to("gs://output-bucket/counts.txt"));
pipeline.run();
清单 6-6
从存储桶读取数据并将输出写入另一个存储桶
```

Cloud Dataflow 还提供了一种使用相同 API 处理流式数据的方法，只需将 `TextIO` 替换为 `PubSubIO` 以从 Pub/Sub 主题读取数据，并将 `TextIO.write()` 替换为 `PubsubIO.write()` 以写入 Pub/Sub 主题。

Cloud Dataflow 还提供了一种根据数据大小自动扩展资源以及处理错误和重试的方法。

Cloud Dataflow 的另一个优势是其能够与其他 GCP 服务（例如 BigQuery、Cloud Storage、Cloud Pub/Sub 和 Cloud Bigtable）集成。这使您可以轻松地从这些服务读取数据以及向其写入数据，并使用 Dataflow 内置的转换和窗口功能执行复杂的数据处理和分析任务。

Dataflow 提供了一个基于 Web 的 UI，称为 Dataflow 监控界面，它允许您监控 Dataflow 作业的进度和状态、查看作业指标以及调试可能出现的任何问题。

此外，Cloud Dataflow 内置了对 Apache Beam 的支持，Apache Beam 是一种用于数据处理管道的开源编程模型。这允许您使用 Beam API 编写 Dataflow 管道，该 API 支持多种编程语言，包括 Java、Python 和 Go。

然而，Cloud Dataflow 也存在某些缺点，例如与其他一些数据处理工具相比学习曲线较陡峭，以及某些类型作业的成本较高。

另一个缺点是它可能并非最适合某些用例，例如低延迟、实时的数据处理，在这些场景下，其他工具（如 Cloud Stream）可能更合适。

Cloud Dataflow 是 GCP 上一个强大的数据处理和分析工具，具有许多内置功能并与其他 GCP 服务集成。它非常适合大规模、批处理或流式数据处理任务，对于需要高度可扩展且容错的数据处理平台的组织来说，它是一个不错的选择。

#### Cloud Dataflow 的 Java 示例

让我们看一个 Java 示例，该示例展示了一个由 Cloud Storage 触发的数据流，当任何文件被放入 Cloud Storage 存储桶时触发。Cloud Dataflow 从 Cloud Storage 存储桶读取文件，并根据从文件中读取的标识符数据，Cloud Dataflow 管道将文件数据存储到不同的 BigQuery 表中。

在 Cloud Dataflow 中，管道是一个有向无环图的数据转换过程，图中的每个节点代表一个处理步骤，该步骤接收一个或多个输入并产生一个或多个输出。DoFn（“do function”的缩写）是一个用户定义的函数，用于处理输入元素并发出输出元素。ParDo 是一个 Dataflow 转换，它将一个 DoFn 应用于 `PCollection` 中的每个元素，为每个输入元素生成零个或多个输出元素。

清单 6-7 展示了一个 Java 数据流的示例，该数据流由文件添加到 Cloud Storage 存储桶触发。

```
import com.google.cloud.dataflow.sdk.Pipeline;
import com.google.cloud.dataflow.sdk.io.TextIO;
import com.google.cloud.dataflow.sdk.options.DataflowPipelineOptions;
import com.google.cloud.dataflow.sdk.options.Default;
import com.google.cloud.dataflow.sdk.options.Description;
import com.google.cloud.dataflow.sdk.options.PipelineOptionsFactory;
import com.google.cloud.dataflow.sdk.transforms.DoFn;
import com.google.cloud.dataflow.sdk.transforms.ParDo;
import com.google.cloud.dataflow.sdk.transforms.GroupByKey;
import com.google.cloud.dataflow.sdk.values.KV;
public class DataflowExample {
public static class ExtractIdentifierFn extends DoFn> {
@Override
public void processElement(ProcessContext c) {
String line = c.element();
// 从文件数据中提取标识符
String identifier = extractIdentifier(line);
c.output(KV.of(identifier, line));
}
}
public static class WriteToBigQueryFn extends DoFn>, Void> {
@Override
public void processElement(ProcessContext c) {
String identifier = c.element().getKey();
Iterable lines = c.element().getValue();
// 根据标识符将数据写入相应的 BigQuery 表
writeToBigQuery(identifier, lines);
}
}
public static void main(String[] args) {
DataflowPipelineOptions options = PipelineOptionsFactory.fromArgs(args).as(DataflowPipelineOptions.class);
options.setStreaming(true);
Pipeline p = Pipeline.create(options);
p.apply(TextIO.Read.from("gs:///*"))
.apply(ParDo.of(new ExtractIdentifierFn()))
.apply(GroupByKey.create())
.apply(ParDo.of(new WriteToBigQueryFn()));
p.run();
}
}
清单 6-7
由添加到 Cloud Storage 存储桶的文件触发的 Java 数据流
```

此示例使用 Cloud Dataflow SDK 创建一个管道，该管道从 Cloud Storage 存储桶读取数据，从文件数据中提取标识符，按标识符对数据进行分组，然后根据标识符将数据写入相应的 BigQuery 表。

请注意，该示例使用 `TextIO` 类从 Cloud Storage 存储桶读取数据，但如果您想直接从 BigQuery 读取数据，也可以使用 `BigQueryIO` 类。

此外，该示例使用了一些此处未实现的方法，例如 `extractIdentifier()` 和 `writeToBigQuery()`，这些方法应由您实现，并分别包含从数据中提取标识符以及将数据写入 BigQuery 表的逻辑。

清单 6-8 是一个 `extractIdentifier()` 方法的示例，该方法可用于 `ExtractIdentifierFn` 类。

```
private static String extractIdentifier(String line) {
// 从文件数据中提取标识符的示例逻辑
String[] parts = line.split(",");
return parts[0];
}
清单 6-8
使用 extractidentifier() 方法
```

此示例使用 `split()` 方法用逗号分割数据行，然后返回第一个元素作为标识符。



清单 6-9 展示了可在 `WriteToBigQueryFn` 类中使用的 `writeToBigQuery()` 方法示例：

```
private static void writeToBigQuery(String identifier, Iterable lines) {
// 将数据写入 BigQuery 的示例逻辑
// 初始化 BigQuery 客户端
BigQuery bigquery = BigQueryOptions.getDefaultInstance().getService();
// 创建表引用
TableId tableId = TableId.of("", "", identifier);
Table table = bigquery.getTable(tableId);
// 将数据写入表
bigquery.insertAll(
InsertAllRequest.newBuilder(tableId)
.addRow("1", lines)
.build());
}
清单 6-9
使用 writeToBigQuery() 方法
```

此示例使用 BigQuery 客户端库初始化 BigQuery 服务，根据标识符创建表引用，并通过 `insertAll()` 方法将数据插入表中。

你也可以使用其他库或服务（如 Apache Beam）将数据写入 BigQuery。

下一节，我们来讨论 Cloud Dataproc。

### Cloud Dataproc

Cloud Dataproc 是 Google Cloud Platform 提供的一项完全托管的云服务，用于在可扩展且经济高效的基础设施上运行 Apache Hadoop、Apache Spark 和 Apache Pig 工作负载。它允许用户轻松创建、管理和删除 Hadoop 及 Spark 集群，并自动处理集群扩缩容、更新和安全等任务。

以下是 Cloud Dataproc 的一些主要特性：

*   *快速创建和删除集群*：Cloud Dataproc 允许你通过简单的 API 调用或 Web 控制台在几分钟内创建 Hadoop 和 Spark 集群。

*   *自动扩缩容和管理*：Cloud Dataproc 自动处理集群扩缩容、更新和安全等任务。它还允许你为集群设置自动扩缩容策略。

*   *经济高效*：Cloud Dataproc 旨在实现经济高效，对集群节点使用可抢占虚拟机 (preemptible VMs) 和按需定价。

*   *集成能力*：Cloud Dataproc 与 Cloud Storage、BigQuery 和 Cloud SQL 等其他 GCP 服务集成，便于访问、处理和分析存储在这些服务中的数据。

以下是 Cloud Dataproc 的一些常见用例：

*   *数据处理*：Cloud Dataproc 常用于数据处理任务，如数据提取、转换和加载 (ETL)，以及机器学习 (ML) 和分析工作负载。

*   *数据仓库*：Cloud Dataproc 可用于将数据从各种来源移动到 BigQuery 等数据仓库中，然后对该数据执行分析。

*   *批处理*：Cloud Dataproc 可用于处理大型批处理作业，例如处理日志文件或生成报告。

清单 6-10 展示了如何使用 Google Cloud SDK 创建新的 Cloud Dataproc 集群的示例。

```
gcloud dataproc clusters create my-cluster \
--region=us-central1 \
--zone=us-central1-a \
--master-machine-type=n1-standard-2 \
--worker-machine-type=n1-standard-1 \
--num-workers=2
清单 6-10
创建新的 Cloud DataProc 集群
```

此命令在 `us-central1` 区域和 `us-central1-a` 可用区创建一个名为 `my-cluster` 的新 Cloud Dataproc 集群，其中包含一个机器类型为 `n1-standard-2` 的主节点和两个机器类型为 `n1-standard-1` 的工作节点。

你还可以使用 Web 控制台、Cloud SDK 和 Cloud Dataproc RESTful API 来管理和监控你的集群及作业。

Cloud Dataproc 是一项强大、可扩展且经济高效的服务，用于在 GCP 上运行大数据工作负载，可应用于从数据处理到机器学习和分析的多种用例。

#### Cloud Dataproc 的 Java 示例

清单 6-11 展示了一个 Java 程序示例，该程序使用 Cloud Dataproc 创建新集群、提交作业，然后删除集群。

```
import com.google.cloud.dataproc.v1.Cluster;
import com.google.cloud.dataproc.v1.ClusterConfig;
import com.google.cloud.dataproc.v1.ClusterControllerClient;
import com.google.cloud.dataproc.v1.ClusterControllerSettings;
import com.google.cloud.dataproc.v1.Job;
import com.google.cloud.dataproc.v1.JobControllerClient;
import com.google.cloud.dataproc.v1.JobControllerSettings;
import com.google.cloud.dataproc.v1.JobPlacement;
import com.google.cloud.dataproc.v1.JobReference;
import com.google.cloud.dataproc.v1.JobType;
import com.google.cloud.dataproc.v1.SoftwareConfig;
import com.google.cloud.dataproc.v1.SubmitJobRequest;
import com.google.cloud.dataproc.v1.SubmitJobResponse;
import com.google.cloud.dataproc.v1.ClusterConfig.Builder;
import com.google.common.collect.ImmutableList;
import java.io.IOException;
public class CloudDataprocExample {
public static void main(String[] args) throws IOException {
// 创建一个 Dataproc 集群
ClusterControllerSettings clusterControllerSettings = ClusterControllerSettings.newBuilder().build();
try (ClusterControllerClient clusterClient = ClusterControllerClient.create(clusterControllerSettings)) {
String projectId = "my-project-id";
String region = "us-central1";
String clusterName = "my-cluster";
Builder configBuilder = ClusterConfig.newBuilder();
configBuilder.setMasterConfig(configBuilder.getMasterConfigBuilder().setMachineTypeUri("n1-standard-2"));
configBuilder.setWorkerConfig(configBuilder.getWorkerConfigBuilder().setMachineTypeUri("n1-standard-1").setNumInstances(2));
Cluster cluster = Cluster.newBuilder()
.setClusterName(clusterName)
.setProjectId(projectId)
.setConfig(configBuilder.build())
.setClusterUuid(clusterName)
.setJobPlacement(JobPlacement.newBuilder().setClusterName(clusterName).build())
.build();
clusterClient.createCluster(projectId, region, cluster);
System.out.println("集群已创建: " + clusterName);
}
// 向集群提交一个作业
JobControllerSettings jobControllerSettings = JobControllerSettings.newBuilder().build();
try (JobControllerClient jobClient = JobControllerClient.create(jobControllerSettings)) {
String projectId = "my-project-id";
String region = "us-central1";
String clusterName = "my-cluster";
String jobId = "my-job";
JobReference jobRef = JobReference.newBuilder().setProjectId(projectId).setJobId(jobId).build();
JobPlacement placement = JobPlacement.newBuilder().setClusterName(clusterName).build();
SoftwareConfig softwareConfig = SoftwareConfig.newBuilder().setImageVersion("1.4").build();
Job job = Job.newBuilder()
.setReference(jobRef)
.setPlacement(placement)
.setSoftwareConfig(softwareConfig)
.setType(JobType.HADOOP)
.build();
SubmitJobRequest submitJobRequest = SubmitJobRequest.newBuilder().setJob(job).build();
SubmitJobResponse submitJobResponse = jobClient.submitJob(projectId, region, submitJobRequest);
String jobName = submitJobResponse.getJob().getReference().getJobId();
System.out.println("作业已提交: " + jobName);
}
// 删除集群
ClusterControllerSettings clusterControllerSettings = ClusterControllerSettings.newBuilder().build();
try (ClusterControllerClient clusterClient = ClusterControllerClient.create(clusterControllerSettings)) {
String projectId = "my-project-id";
String region = "us-central1";
String clusterName = "my-cluster";
clusterClient.deleteCluster(projectId, region, clusterName);
System.out.println("集群已删除: " + clusterName);
}
}
}
清单 6-11
使用 Cloud Dataproc 创建新集群、提交作业并删除集群
```



本示例演示了创建集群、提交作业，然后删除集群的基本步骤。你需要将 `my-project-id` 和 `my-cluster` 等占位符替换为你的实际项目 ID 和集群名称。此外，你还需要指定作业类型，例如 Hadoop、Spark、Pig 等，并设置作业详情，如主类、JAR 文件、参数等。

让我们先定义一下这里使用的几个术语。

*   *MR 作业* 代表 MapReduce 作业。它是一种用于大数据处理的处理模型，涉及在集群节点间并行处理大量数据。

*   *作业* 是提交给 Dataproc 集群进行处理的工作单元。作业可以是 MapReduce 作业、Spark 作业、Hive 作业，或任何其他可以在 Hadoop 或 Spark 集群上运行的作业类型。

*   *作业放置* 指的是决定作业在 Dataproc 集群中运行位置的规则。例如，你可以指定作业应在特定节点集上运行，或者应在具有特定特征（如一定量的内存或一定数量的 CPU 核心）的节点上运行。

*   *作业引用* 是 Dataproc 中作业的唯一标识符。它可用于监控作业状态、检索作业信息或取消作业。

*   *作业类型* 指的是在 Dataproc 集群上运行的作业类型。例如，作业可以是 MapReduce 作业、Spark 作业、Hive 作业，或任何其他可以在 Hadoop 或 Spark 集群上运行的作业类型。

*   *作业配置* 指的是控制作业如何在 Dataproc 集群上运行的设置。这包括诸如要使用的工作节点数量、工作节点使用的机器类型、要使用的软件类型（如 Hadoop 或 Spark）以及许多其他可根据作业特定需求进行自定义的选项。

注意

Cloud Dataproc 可能需要根据具体用例进行额外的配置和错误处理。

#### 配置 Cloud Dataproc

使用 Cloud Dataproc 时，你可以设置多个配置选项来自定义集群和作业。以下是一些示例：

*   *集群配置*：你可以指定集群中工作节点和主节点的数量、每个节点的机器类型，以及分配给每个节点的内存和 CPU 数量。此外，你还可以配置网络和防火墙设置，并设置自动缩放以根据工作负载自动添加或移除节点。

*   *软件配置*：你可以指定要在集群上使用的 Hadoop、Spark、Pig 或其他软件的版本。你还可以配置环境变量和初始化操作，以便在作业启动前运行。

*   *作业配置*：对于每个作业，你可以指定要运行的主类、JAR 文件和参数。你还可以配置作业放置，例如运行作业的集群以及要使用的工作节点数量。

*   *安全配置*：你可以为集群和作业配置身份验证和授权设置。你还可以为静态数据和传输中的数据设置加密。

清单 6-12 演示了如何配置一个包含四个工作节点和两个主节点的集群，使用机器类型 `n1-standard-4` 并安装特定版本的软件。

```
ClusterConfig clusterConfig = ClusterConfig.newBuilder()
.setMasterConfig(InstanceGroupConfig.newBuilder()
.setMachineTypeUri("n1-standard-4")
.setNumInstances(2)
.build())
.setWorkerConfig(InstanceGroupConfig.newBuilder()
.setMachineTypeUri("n1-standard-4")
.setNumInstances(4)
.build())
.setSoftwareConfig(SoftwareConfig.newBuilder()
.setImageVersion("1.4")
.build())
.build();
清单 6-12
配置集群
```

我希望你现在对 Dataproc 有了很好的了解。让我们在下一节讨论 Cloud Functions。

### Cloud Functions

Cloud Functions 是 Google Cloud Platform 提供的一项无服务器计算服务，允许你运行代码而无需预置或管理服务器。使用 Cloud Functions，你可以用多种语言编写和部署代码，包括 JavaScript、Python、Go 和 Java。代码部署后，Cloud Functions 会自动为你扩展和管理底层基础设施。

以下是 Cloud Functions 的一些关键特性和用例：

*   *事件驱动*：Cloud Functions 允许你触发代码运行以响应特定事件，例如 Cloud Storage 存储桶中的数据更改或 Cloud Pub/Sub 主题中的新消息。这使得构建响应数据实时变化的事件驱动架构变得容易。

*   *无服务器*：Cloud Functions 抽象了底层基础设施，因此你可以专注于编写代码。你无需担心预置或管理服务器，并且只需为代码实际使用的资源付费。

*   *与其他 GCP 服务集成*：Cloud Functions 与 Cloud Storage、Cloud Pub/Sub 和 Firebase 等其他 GCP 服务无缝集成。这使你可以轻松构建涉及不同 GCP 服务的复杂多步骤工作流。

*   *易于部署*：Cloud Functions 允许你通过单个命令将代码部署到多个区域，并且你可以使用版本控制和回滚来轻松管理代码的不同版本。

清单 6-13 是一个简单的 Java 函数，由向 Cloud Storage 存储桶添加新文件触发。

```
import com.google.cloud.functions.BackgroundFunction;
import com.google.cloud.functions.Context;
import java.io.IOException;
public class ProcessFile implements BackgroundFunction {
public void accept(StorageObject object, Context context) throws IOException {
// 你的代码在此处处理文件
String bucket = object.getBucket();
String file = object.getName();
System.out.println("正在处理文件: " + file + " 位于存储桶 " + bucket);
}
}
清单 6-13
由向 Cloud Storage 存储桶添加新文件触发的 Java 函数
```

在此示例中，函数 `accept` 由向指定存储桶添加新文件触发。作为参数传递的 `StorageObject` 包含有关所添加文件的信息。在此示例中，它仅记录有关所添加文件的信息。

你可以根据需要自定义函数，例如，通过处理文件并将处理后的数据存储在另一个 GCP 服务（如 BigQuery 或 Datastore）中。

#### Cloud Functions 的用例

以下是 Cloud Functions 的一些用例示例：

*   *数据处理*：你可以使用 Cloud Functions 实时处理添加到 Cloud Storage 存储桶或 Cloud Pub/Sub 主题的数据。例如，你可以编写一个函数，在上传图片到存储桶时自动调整其大小，或者从主题读取数据并将其存储在 BigQuery 表中。

*   *自动化*：你可以使用 Cloud Functions 自动化由特定事件触发的任务。例如，你可以编写一个函数，在向存储桶添加新文件时发送电子邮件，或者在移动应用中点击按钮时自动在 Compute Engine 中创建新实例。

*   *集成*：你可以使用 Cloud Functions 将不同的 GCP 服务集成在一起。例如，你可以编写一个函数，从 Firebase Realtime Database 读取数据并将其存储在 BigQuery 中，或者监听 Cloud Spanner 表中的更改并更新 Firebase Cloud Firestore。

*   *Web 应用程序*：Cloud Functions 可用于构建 Web 应用程序，方法是编写处理 HTTP 请求并以 JSON 响应的函数。



#### Cloud Functions 的局限性

以下是 Cloud Functions 的局限性：

*   *执行时间*：Cloud Functions 的最大执行时间为 9 分钟。如果你的函数需要运行更长时间，则需使用其他服务。

*   *内存与 CPU*：Cloud Functions 每个实例有最大内存和 CPU 分配量，通常足以满足简单函数的需求。但如果你的函数需要更多资源，则可能需要使用其他服务。

*   *冷启动*：Cloud Functions 在长时间未使用时可能会经历“冷启动”，这会导致函数初始执行出现延迟。可以通过定期触发函数使其保持“热状态”，或使用能够处理更长空闲时间的其他服务来缓解此问题。

*   *流量整形*：Cloud Functions 不提供任何内置的流量整形机制，例如速率限制或自动扩缩容。这意味着，如果你预期会有大量流量，则可能需要使用提供这些功能的其他服务。

Cloud Functions 是一种无服务器计算服务，专为处理小型、短时任务而设计。它不能替代 Compute Engine 或 Kubernetes Engine 等其他计算服务。

#### Cloud Functions 的 Java 示例

清单 6-14 展示了一个用 Java 编写的 Cloud Functions 函数，该函数由 REST 调用触发，并从测验数据库（Firestore 数据库）中随机检索 10 个问题（5 个简单，5 个困难）。

```
import com.google.cloud.firestore.Firestore;
import com.google.cloud.firestore.QueryDocumentSnapshot;
import com.google.cloud.firestore.QuerySnapshot;
import com.google.firebase.cloud.FirestoreClient;
import java.util.ArrayList;
import java.util.List;
import java.util.Random;
import com.google.gson.Gson;
import com.google.gson.JsonObject;
import java.io.IOException;
import javax.servlet.http.HttpServletResponse;
import com.google.cloud.functions.HttpFunction;
import com.google.cloud.functions.HttpRequest;
import com.google.cloud.functions.HttpResponse;
public class GetQuizFunction implements HttpFunction {
private Firestore db;
private Random random;
private Gson gson;
public GetQuizFunction() {
db = FirestoreClient.getFirestore();
random = new Random();
gson = new Gson();
}
@Override
public void service(HttpRequest request, HttpResponse response) throws IOException {
List easyQuestions = new ArrayList();
List hardQuestions = new ArrayList();
// Get all questions from the Firestore database
QuerySnapshot questionsSnapshot = db.collection("quiz").get().get();
for (QueryDocumentSnapshot question : questionsSnapshot) {
JsonObject jsonQuestion = gson.fromJson(question.toJson(), JsonObject.class);
String difficulty = jsonQuestion.get("difficulty").getAsString();
if (difficulty.equals("easy")) {
easyQuestions.add(jsonQuestion);
} else if (difficulty.equals("hard")) {
hardQuestions.add(jsonQuestion);
}
}
// Select 5 random easy questions
List selectedEasyQuestions = new ArrayList();
for (int i = 0; i SPi-Amp-LessThan 5; i++) {
int index = random.nextInt(easyQuestions.size());
selectedEasyQuestions.add(easyQuestions.get(index));
easyQuestions.remove(index);
}
// Select 5 random hard questions
List selectedHardQuestions = new ArrayList();
for (int i = 0; i SPi-Amp-LessThan 5; i++) {
int index = random.nextInt(hardQuestions.size());
selectedHardQuestions.add(hardQuestions.get(index));
hardQuestions.remove(index);
}
// Combine the selected easy and hard questions
List selectedQuestions = new ArrayList();
selectedQuestions.addAll(selectedEasyQuestions);
selectedQuestions.addAll(selectedHardQuestions);
// Convert the selected questions to JSON and set the content type to JSON
String json = gson.toJson(selectedQuestions);
response.setContentType("application/json");
response.setStatusCode(HttpServletResponse.SC_OK);
response.getWriter().write(json);
}
}
清单 6-14
一个用 Java 编写的 Cloud Function，由 REST 调用触发，用于从测验数据库中检索问题
```

此示例使用 Firestore 客户端库连接到 Firestore 数据库，并从名为 `quiz` 的集合中检索所有问题。然后，它使用 `Random` 对象从检索到的问题中随机选择五个简单问题和五个困难问题。选中的问题随后被合并到一个列表中，并使用 Gson 库转换为 JSON 字符串。该 JSON 字符串作为 REST 调用的响应返回，并将内容类型设置为 `application/json`。

关于此程序，请注意以下几点：

*   清单 6-14 使用了 Firebase Admin SDK；你需要配置函数的凭据才能访问 Firestore 数据库。

*   清单 6-14 未包含错误处理。建议在生产环境中包含错误处理。

*   我假设 Firestore 文档中的字段名 `difficulty` 的值为 `easy` 或 `hard`。

*   你可以使用 `.whereEqualTo()` 方法按 `difficulty` 字段过滤文档。

## 总结

在本章中，你学习了 Google Cloud Platform 提供的强大服务：Cloud Pub/Sub、Cloud Dataflow、Cloud Dataproc 和 Cloud Functions。Cloud Pub/Sub 可用于解耦微服务，并在独立系统之间发送/接收消息。Cloud Dataflow 可用于创建数据处理管道，通过批处理或流式数据处理来处理大量数据。Cloud Dataproc 是一种托管的 Hadoop 和 Spark 服务，允许你使用开源工具轻松处理大量数据。本章涵盖了如何创建集群、提交作业以及配置集群和作业的各个方面。Cloud Functions 是一种无服务器计算服务，允许你响应特定事件运行代码。本章介绍了 Cloud Functions 的一些关键特性、用例和局限性，并提供了一个如何编写 Java 函数从 Firestore 数据库检索随机测验问题的示例。

