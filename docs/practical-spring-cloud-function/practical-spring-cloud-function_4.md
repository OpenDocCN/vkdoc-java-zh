# 4. 使用 Spring Cloud Function 构建事件驱动的数据流水线

Spring Cloud Function 在混合云或本地/私有云环境中发挥着关键作用。构建跨越本地数据中心和云的数据流水线会增加复杂性，因为需要处理防火墙边界。当您希望获取、移动或转换数据（无论是实时流式处理还是离线批量处理）时，数据流水线都扮演着重要角色。

因此，什么是事件数据流水线？



## 4.1 数据事件管道

让我们来看一个例子。假设你正在驾驶汽车，突然与另一辆车发生碰撞。几分钟后，你会接到 OnStar 的电话询问你是否安全，因为当时发生了碰撞。OnStar 是如何知道你发生了碰撞的？这一切都发生在瞬间。车辆内的传感器被碰撞事件触发，开始将数据以流的方式发送到 OnStar 系统。OnStar 系统处理来自其他多个系统的数据，并通过触发 OnStar 操作员的电话呼叫来产生响应。引发数据处理的事件以及输出结果共同构成了事件数据管道。图 4-1 展示了这一过程。

![](img/526597_1_En_4_Fig1_HTML.jpg)

一张示意图展示了 OnStar 在车辆中实现的事件驱动数据管道。车辆将数据发送到附近的基站，基站将数据中继到 OnStar 数据中心。OnStar 数据中心将信息路由到呼叫中心。通过这一过程，紧急服务如警察、医疗响应团队和拖车公司被激活。

图 4-1

使用 OnStar 的车辆事件驱动数据管道示例

所有关于事故的数据都会被收集、处理、分析，并发送到 OnStar 内外部的各种目标系统。这是事件数据管道的一个示例。

数据管道是一组流程，从不同来源获取原始数据，然后通过过滤、转换和移动，将数据存储到目标存储系统中，以便进行分析和以视觉上引人注目的方式呈现。

数据可以以流或批次的形式存在。流通常与实时或近实时的数据传输相关联，而批次通常是计划的且非实时的。图 4-2 展示了这一点。

![](img/526597_1_En_4_Fig2_HTML.png)

一张示意图展示了数据管道作为一组流程，这些流程会摄入结构化或非结构化数据，并通过数据湖或数据仓库检索和转换数据，进一步通过人工智能或机器学习流程进行分析。

图 4-2

数据管道流程

数据也可以是非结构化或结构化的。非结构化数据可以来自多种资源，包括基于互联网的平台如 Twitter，以及汽车设备发出的数据。

结构化数据通常源自公司内部的应用程序。这些数据需要在对象存储的着陆区进行组合。这些对象存储可以是本地部署或云环境中的。一旦数据被摄入到对象存储中，它就会被检索和转换，通常通过 ETL 流程在其他数据存储（如数据湖或数据仓库）中进行。然后，这些数据会通过商业智能工具（如 Power BI 和 Tableau）或进一步通过人工智能/机器学习流程进行分析。

从数据摄入到消费的整个过程称为数据管道。

让我们更深入地探讨数据管道流程中提到的各个子流程。

### 4.1.1 获取数据

获取数据是数据管道流程的第一步。在这里，业务所有者和数据架构师决定使用哪些数据来满足特定用例的需求。

例如，在 OnStar 的碰撞事件检测场景中，数据需要从传感器获取。这种传感器数据需要与来自内部和外部合作伙伴（如金融、拖车、租赁合作伙伴等）的数据进行组合。

表 4-1 展示了车辆事件驱动管道的数据、数据类型和数据源。

表 4-1

数据识别

| 数据 | 数据类型 | 数据源 |
| --- | --- | --- |
| **传感器** | 非结构化（JSON、CSV） | 传感器 |
| **租赁信息** | 关系型（RDBMS） | 外部租赁机构 |
| **用户信息** | 关系型（RDBMS） | OnStar 注册 |
| **车辆信息** | 关系型（RDBMS） | 车辆数据系统 |
| **拖车信息** | 关系型（RDBMS） | 外部拖车公司 |

### 4.1.2 存储/摄入数据

从各种来源获取的数据被存储为原始数据到所选存储系统中。摄入方法可以是基于流的或基于批次的。

例如，在 OnStar 的案例中，传感器数据以固定间隔流式传输或基于事件触发。其他数据（如租赁信息）可以基于批次或按需查询驱动。原始数据存储可以是托管在云中的 S3 对象存储或本地部署。

### 4.1.3 转换数据

存储在对象存储中的原始数据随后被处理。该过程可能包括将非结构化数据转换为结构化数据，并存储到 RDBMS 数据库中。

例如，在 OnStar 中，合作伙伴数据和内部数据将被组合并转换为通用数据模型。传感器数据也会被转换为 RDBMS 格式。

### 4.1.4 加载数据

一旦数据被转换，它就会被加载到具有共同模式的单个或多个数据库中。该模式基于特定于用例的预定义数据模型。目标数据存储可以是数据湖或其他 RDBMS 存储。在这里，分析类型再次决定了具体选择。

例如，如果是 OLTP 类型的分析，数据需要被快速处理并发送到请求系统。这需要 RDBMS 存储。需要用于报告和研究的数据可以存储在数据湖中。

### 4.1.5 分析数据

在此子流程中，存储在数据湖或 RDBMS 中的数据将通过 Tableau、Power BI 或专用的报告网页等工具进行分析。

在 OnStar 的案例中，存储在数据湖中的数据将通过 Tableau 或 Power BI 进行分析，而需要立即处理的数据则通过自定义仪表板或网页报告界面进行分析。

Spring Cloud Function 在整个过程中起着至关重要的作用，尤其是在与 Spring Cloud Data Flow、AWS Glue、Azure Data Factory、Google 的数据流等工具结合使用时。你将在本章深入探讨这些工具。

## 4.2 Spring Cloud Function 与 Spring Cloud Data Flow 与 Spring Cloud Streams

Spring Cloud Data Flow（SCDF）是一款基于 Spring.io 的产品，支持创建和部署数据管道。它支持批处理和事件驱动的数据，使其非常灵活。SCDF 管道可以通过编程方式构建，也可以通过图形界面进行连接。它高度依赖于 Spring 框架和 Java，这使其在 Spring 开发者中非常受欢迎。

图 4-3 展示了 SCDF 的一个示例仪表板，其中包含一个简单的数据管道，具有源、处理器和接收器。你可以从可用组件中拖放以构建管道。你也可以构建自定义的源、处理器和接收器，并将其部署到企业内部使用。

![](img/526597_1_En_4_Fig3_HTML.jpg)

一张截图展示了 SCDF，显示了车辆数据管道的定义、状态和应用。

图 4-3

SCDF 中数据管道的示例图形表示

从仪表板可以看出，你可以构建基于流或基于批次（任务）的数据管道，并通过单一仪表板管理这些管道。

与云中其他数据管道工具不同，SCDF 可以在 Kubernetes、Docker 或 Cloud Foundry 环境中部署，使其成为数据管道开发和部署的便携式工具。



### 4.2.1 Spring Cloud Function 与 SCDF

Spring Cloud Function 和 SCDF 完美契合，因为它们均基于 Spring 框架构建。你可以将 Spring Cloud Function 部署为数据管道的源、处理器、终点或触发器。由于数据管道通常是间歇性调用以处理数据，因此可以使用 Spring Cloud Function 优化资源利用和成本。

让我们看一下 Spring Cloud Function 与 SCDF 的示例实现。

在这个示例中，你将使用 RabbitMQ 作为源构建一个简单数据管道，进行简单的转换操作，并将消息存储到日志中。你将向名为 `VehicleInfo` 的 RabbitMQ 主题发布示例车辆信息，然后进行简单转换，最后将其存储在日志文件中。

RabbitMQ ➤ 转换 ➤ 日志

前提条件：

*   SCDF 部署在 Kubernetes 或本地 Docker 环境中

*   Kubernetes 或 Docker

*   一个 RabbitMQ 集群/实例

*   一个用于发布消息的队列

*   从 GitHub 获取代码：[`https://github.com/banup-kubeforce/SCDF-Rabbit-Function.git`](https://github.com/banup-kubeforce/SCDF-Rabbit-Function.git)

每个环境的额外前提条件可参考 [`https://dataflow.spring.io/docs/installation`](https://dataflow.spring.io/docs/installation)

步骤 1：安装 Spring Cloud Data Flow

*   本地机器：Docker Compose 是启动 Spring Cloud Data Flow 实例的简便方式。安装详情请参考 [`https://dataflow.spring.io/docs/installation/local/docker/`](https://dataflow.spring.io/docs/installation/local/docker/)

*   Kubernetes：Kubernetes 上的安装说明请参考 [`https://dataflow.spring.io/docs/installation/kubernetes/`](https://dataflow.spring.io/docs/installation/kubernetes/)

对于此示例，我使用了第 2 章中创建的现有集群，并运行以下 `helm` 命令安装 SCDF，结果如图 4-4 所示。

![](img/526597_1_En_4_Fig4_HTML.jpg)

截图展示了安装 S C D F 的 helm 命令，包含名称、最后部署时间、命名空间、状态、测试套件、说明、图表名称、图表版本和应用版本等信息。

图 4-4

SCDF 的 helm 图表成功执行

```
$helm install scdf bitnami/spring-cloud-dataflow -n scdf-system --set server.service.type=LoadBalancer --set server.service.loadBalancerIP=${ingress} --set server.ingress.enabled=true --set server.ingress.server.host=scdf.${ingress}.xip.io
```

一旦命令成功运行，如图 4-4 所示，你可以通过运行 `kubectl get pods` 检查状态。也可以通过 `kubectl get svc` 命令获取外部 IP，如图 4-5 和 4-6 所示。

![](img/526597_1_En_4_Fig6_HTML.jpg)

截图展示了获取 S C D F 状态的命令，使用的命令是 dollar kubect 1 get s v c-n s c d f-system。

图 4-6

获取 SCDF 状态

![](img/526597_1_En_4_Fig5_HTML.jpg)

截图展示了获取 S C D F 外部 IP 的命令，使用的命令是 dollar kubect 1 get pods-n s c d f-system。

图 4-5

获取 SCDF 的外部 IP

```
$kubectl get pods -n scdf-system
```

```
$kubectl get svc -n scdf-system
```

现在你可以通过外部 IP 访问 SCDF。例如，[`http://20.241.228.184:8080/dashboard`](http://20.241.228.184:8080/dashboard)

下一步是添加应用程序。Spring 提供了一套标准的模板，可用于构建你的数据管道。

步骤 2：向 SCDF 实例添加应用程序

使用“添加应用程序”按钮创建一些初始应用程序，用于构建数据管道；参见图 4-7 和 4-8。

![](img/526597_1_En_4_Fig8_HTML.jpg)

截图展示了通过“注册一个或多个应用程序”选项添加自定义构建的应用程序，你可以从 HTTP URI 导入应用程序坐标，或使用属性文件。此外，还可以从 Spring 导入一些预构建的初始模板。你还可以选择基于 Maven 或 Docker 的初始模板，或基于 RabbitMQ 或 Kafka 的初始模板。RabbitMQ 和 Kafka 作为 SCDF 内部组件的骨干消息系统，不用于外部场景。在 Kubernetes 集群部署时，必须选择基于 Docker 的初始模板。在本地 Docker 环境部署时，可以选择基于 Maven 或 Docker 的初始模板。

图 4-9 展示了带有 Docker 镜像 URI 的预构建模板。由于 SCDF 运行在 Kubernetes 环境中，因此预构建的镜像具有 Docker URI。

![](img/526597_1_En_4_Fig9_HTML.jpg)

截图展示了 SCDF 在 Kubernetes 环境中运行，并已安装预构建模板。

图 4-9

SCDF 中安装了预构建模板

这些预构建模板分为三类：源、处理器和终点。它们允许你无需编码即可连接数据管道。如果你想创建自定义组件，可以参考 [`https://dataflow.spring.io/docs/stream-developer-guides/`](https://dataflow.spring.io/docs/stream-developer-guides/) 中的示例。

下一步是使用已加载的初始模板创建一个流。

步骤 3：创建流

流仪表板允许你连接数据管道。点击“创建流”按钮开始流程，如图 4-10 所示。

![](img/526597_1_En_4_Fig10_HTML.jpg)

截图展示了流仪表板中创建流的按钮。

图 4-10

流仪表板

然后执行以下步骤：

1.  **选择源。**

此示例使用 RabbitMQ 作为源，因此从可用选项中选择 Rabbit，如图 4-11 所示。

![](img/526597_1_En_4_Fig11_HTML.jpg)

截图展示了创建流并从源中选择可用选项。

图 4-11

创建源

1.  **选择处理器。**

从列表中选择“转换”组件，如图 4-12 所示。

![](img/526597_1_En_4_Fig12_HTML.jpg)

截图展示了为转换组件选择并创建处理器。

图 4-12

创建处理器

1.  **选择终点。**

从列表中选择“日志”组件，如图 4-13 所示。

![](img/526597_1_En_4_Fig13_HTML.png)

截图展示了为日志组件选择并创建终点。

图 4-13

创建终点

现在通过将第一个组件的输出拖拽到第二个组件的输入中来连接它们，如图 4-14 所示。

![](img/526597_1_En_4_Fig14_HTML.png)

截图展示了通过将第一个组件的输出拖拽到第二个组件的输入中来连接数据管道。

图 4-14

连接数据管道

下一步是配置管道。

1.  **配置 RabbitMQ。**



您需要将 Rabbit 源指向您的 RabbitMQ 实例。表单中有三类字段需要填写。打开并填写相应的字段。我使用了用户名、密码、端口、主机和队列，如图 4-15 所示。

![](img/526597_1_En_4_Fig15_HTML.jpg)

两张截图展示了配置的 RabbitMQ 和 Rabbit 的属性。它包含用户名、密码、端口和主机。

图 4-15

设置 Rabbit 属性

配置完成后点击更新。

1.  **配置转换.**

在此示例中，您只需将传入的字符串转换为大写。请注意，您必须使用 SpEL（Spring 表达式语言）。更多关于 SpEL 的信息请参见 [`https://docs.spring.io/spring-framework/docs/3.0.x/reference/expressions.xhtml`](https://docs.spring.io/spring-framework/docs/3.0.x/reference/expressions.xhtml)。参见图 4-16。

![](img/526597_1_En_4_Fig16_HTML.jpg)

一张截图展示了转换属性。它包含通用、SpEL、函数和更新、取消按钮。

图 4-16

设置转换属性

1.  **配置接收端.**

在此情况下，您只需使用一个简单的日志记录。无需配置。参见图 4-17。

![](img/526597_1_En_4_Fig17_HTML.jpg)

一张截图展示了设置日志属性。它代表通用、日志以及取消和更新按钮。

图 4-17

设置日志属性

1.  **连接数据管道.**

一旦完成连接，您将获得一个数据管道。这还将显示一个 Stream DSL（领域特定语言）表达式，您可以使用 SCDF shell 或仪表板保存并重用它，如图 4-18 所示。

![](img/526597_1_En_4_Fig18_HTML.png)

一张截图展示了数据管道的连接过程。它代表创建流仪表板时的流 DSL 表达式。

图 4-18

连接数据管道

```
rabbit --password=pa55word --port=5672 --host=20.40.208.246 --username=banup --queues=VehicleInfo | transform --expression='new String(payload).toUpperCase()' |log
```

该管道可以通过外部 Spring Cloud Function 在 Lambda 或 Google Cloud Functions 等平台上触发。参见图 4-19。

![](img/526597_1_En_4_Fig19_HTML.png)

一张截图展示了通过外部 Spring Cloud Function 成功部署数据管道的结果。

图 4-19

数据管道成功部署

1.  **部署流**

步骤 4：创建一个发布数据到 RabbitMQ 的函数

在此，您创建一个 Spring Cloud Function 来发布数据，以启动数据管道处理过程。

您将创建的组件如下：

*   `SenderConfig`

*   `QueueSender`

*   `SenderFunction`

前提条件：

*   包含 RabbitMQ 和 Spring Cloud Functions 所需依赖项的 POM，如清单 4-1 所示。

```
org.springframework.boot
spring-boot-starter-data-jpa

org.springframework.boot
spring-boot-starter-web

org.springframework.boot
spring-boot-starter-amqp

org.springframework.cloud
spring-cloud-function-web

org.springframework.boot
spring-boot-starter-test
test

org.springframework.cloud
spring-cloud-dependencies
${spring-cloud.version}
pom
import

清单 4-1
包含 RabbitMQ 依赖的 pom.xml
```

1.     **创建 SenderConfig 组件**

这是一个简单的队列名称设置器。您可以扩展它以包含其他 RabbitMQ 配置。这是实体定义（参见清单 4-3）。

```
package com.kubeforce.scdffunctiontigger;
import org.springframework.amqp.core.Queue;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
@Configuration
public class SenderConfig {
@Value("${queue.name}")
private String message;
@Bean
public Queue queue() {
return new Queue(message, true);
}
}
清单 4-3
application.properties
```

2.     **创建 QueueSender 组件**

您使用`springframework amqp`库中的`RabbitTemplate`来定义与 RabbitMQ 实例的连接（参见清单 4-4）。

```
package com.kubeforce.scdftrigger;
import org.springframework.amqp.core.Queue;
import org.springframework.amqp.rabbit.core.RabbitTemplate;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;
@Component
public class QueueSender {
@Autowired
private RabbitTemplate rabbitTemplate;
@Autowired
private Queue queue;
public void send(String order) {
rabbitTemplate.convertAndSend(this.queue.getName(), order);
}
}
清单 4-4
QueueSender.java
```

3.     **使用 Spring Cloud Function 框架包装发送器**

此示例使用`queueSender`发送数据（参见清单 4-5）。

```
package com.kubeforce.scdftrigger;
import org.springframework.beans.factory.annotation.Autowired;
import java.util.function.Function;
public class SenderFunction implements Function {
@Autowired
private QueueSender queueSender;
@Override
public String apply(String s) {
queueSender.send("Vehicle:SUV,Make:Ford,Model:Edge,Year:2021");
return "ok. done";
}
}
清单 4-5
SenderFunction.java
```

步骤 5：使用 Postman 测试函数

在 Postman 中使用`GET`功能，并提供到`senderFunction`的 URL。

![](img/526597_1_En_4_Figa_HTML.jpg)

一张截图展示了 Postman 中的`GET`功能和 URL。

您应该会得到此图片中显示的结果。

检查 RabbitMQ 队列中的消息。

![](img/526597_1_En_4_Figb_HTML.jpg)

一张截图展示了 RabbitMQ 队列中的消息。它在查询选项卡下显示交换、路由键、重新投递、属性和负载，服务器报告剩余 0 条消息。

现在您有一个可以向 RabbitMQ 主题发布消息的函数。SCDF 数据管道将监听该队列并开始处理。

您还可以查看每个组件的日志以监控数据管道的状态，如图 4-20 所示。

![](img/526597_1_En_4_Fig20_HTML.png)

一张截图展示了车辆数据管道的详细信息。它显示了与每个组件相关的日志以进行监控。

图 4-20

车辆数据管道流的详细信息

您已经了解了如何在 SCDF 中创建一个监控 RabbitMQ 主题的数据管道。您创建了一个 Spring Cloud Function，将消息发布到 RabbitMQ 主题。Spring Cloud Function 也可以作为 SCDF 的源进行部署；有关如何为 SCDF 开发代码的更多信息，请参见 SCDF 网站。



## 4.3 Spring Cloud Function 与 AWS Glue

AWS Glue 与 Spring Cloud Data Flow 的工作方式非常相似，您可以构建一个包含源、处理器和汇的数 据管道。更多信息请参见 [`https://us-east-2.console.aws.amazon.com/gluestudio/home?region=us-east-2#/`](https://us-east-2.console.aws.amazon.com/gluestudio/home%253Fregion%253Dus-east-2%2523/)。

Spring Cloud Function 可以作为触发器参与数据管道处理过程，或者通过与数据管道中的某个组件集成来实现。

例如，如果您使用 AWS Kinesis 作为源并需要从车辆获取数据，可以让 Spring Cloud Function 将获取到的数据流式传输到 AWS Kinesis。

在本节的示例中，您将发布数据到 AWS Kinesis，然后手动启动 AWS Glue 作业。

流程如下：

Spring Cloud Function ➤ Kinesis ➤ AWS Glue 作业 ➤ S3

前提条件包括：

*   AWS 订阅、AWS Glue 作业、Kinesis 和 S3

*   以 Kinesis 为源、S3 为目标的 AWS Glue 作业

*   GitHub 上的代码 [`https://github.com/banup-kubeforce/Kinesis_trigger.git`](https://github.com/banup-kubeforce/Kinesis_trigger.git)

我们假设您对 AWS Glue 有一定了解，因为本文档不会深入探讨该产品的细节。重点在于创建 Spring Cloud Function。

### 4.3.1 步骤 1：设置 Kinesis

您可以通过 [`https://us-east-1.console.aws.amazon.com/kinesis/home?region=us-east-1#/home`](https://us-east-1.console.aws.amazon.com/kinesis/home%253Fregion%253Dus-east-1%2523/home) 访问 Kinesis。

一旦订阅了 Kinesis，就可以开始创建数据流。请确保选择按需模式（参见图 4-21），这样可以节省一些费用。

![](img/526597_1_En_4_Fig22_HTML.png)

截图展示了在监控选项卡下活跃的车辆数据流。

图 4-22

vehicledatastream 数据流处于活动状态

![](img/526597_1_En_4_Fig21_HTML.jpg)

截图展示了 Kinesis 的设置，用于为车辆创建数据流。它显示了数据流配置、数据流容量和数据流设置。

图 4-21

在 AWS Kinesis 中创建 vehicledatastream 数据流

现在您可以使用 Spring Cloud Function 连接到该数据流并发布数据。

### 4.3.2 步骤 2：设置 AWS Glue

您可以通过 [`https://us-east-1.console.aws.amazon.com/gluestudio/home?region=us-east-1#/`](https://us-east-1.console.aws.amazon.com/gluestudio/home%253Fregion%253Dus-east-1%2523/) 访问 AWS Glue Studio。

一旦完成订阅，就可以开始创建 Glue 作业。

前往 AWS Glue Studio 以开始创建 Glue 作业，如图 4-23 所示。

![](img/526597_1_En_4_Fig23_HTML.png)

截图展示了在 AWS Glue Studio 中创建作业的过程。

图 4-23

AWS Glue Studio，创建作业

创建名为 `vehicledatapipeline` 的作业。使用 Amazon Kinesis 作为源，使用 Amazon S3 作为目标。确保为这些组件设置正确的配置。

图 4-24 中的作业将读取 Amazon Kinesis 分片并将数据发布到 Amazon S3。

![](img/526597_1_En_4_Fig24_HTML.png)

截图展示了将 Amazon Kinesis 作为源、Amazon S3 作为目标的组件配置。

图 4-24

配置 Kinesis 与 S3 的集成

现在您已在 AWS Glue 中创建了一个可以手动触发或通过函数触发的作业。

### 4.3.3 步骤 3：创建将数据加载到 Kinesis 的函数

**1: 设置应用程序属性。** 确保提供数据流名称。需要设置 AWS 信息，如清单 4-6 所示。

```
spring.cloud.function.definition=producerFunction
#在此处填写您的 AWS 凭证
aws.access_key =
aws.secret_key =
aws.region = us-east-2
#使用您已创建的数据流名称
aws.stream_name = vehicledatastream
清单 4-6
application.properties
```

**2: 添加 Kinesis 依赖项。** 确保添加必要的库。最新版本的 producer 存在一些 bug，因此我使用了可用版本，如清单 4-7 所示。

```
com.amazonaws
amazon-kinesis-client
1.14.1

com.amazonaws
amazon-kinesis-producer
0.13.1

com.amazonaws
amazon-kinesis-client
1.14.1

清单 4-7
pom.xml 依赖项
```

**3:** **创建模型。** 这是一个简单的模型，用于跟踪车辆的详细信息。参见清单 4-8。

```
public class TrackDetail {
private String vehicleId;
private String driverId;
private String driverName;
public String getVehicleId() {
return vehicleId;
}
public void setVehicleId(String vehicleId) {
this.vehicleId = vehicleId;
}
public String getDriverId() {
return driverId;
}
public void setDriverId(String driverId) {
this.driverId = driverId;
}
public String getDriverName() {
return driverName;
}
public void setDriverName(String driverName) {
this.driverName = driverName;
}
}
清单 4-8
TrackDetail.java
```

**4: 创建 Kinesis 生产者接口。** 该接口很有用；参见清单 4-9。

```
public interface ProducerService {
public void putDataIntoKinesis(String payload) throws Exception;
public void stop();
}
清单 4-9
ProducerService.java
```

**5:** **创建生产者实现。** 清单 4-10 中提供的实现设置了向 Kinesis 发布数据的方法。



```
import com.amazonaws.auth.AWSStaticCredentialsProvider;
import com.amazonaws.auth.BasicAWSCredentials;
import com.amazonaws.services.kinesis.producer.*;
import com.google.common.util.concurrent.FutureCallback;
import com.google.common.util.concurrent.Futures;
import com.google.common.util.concurrent.ListenableFuture;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Service;
import java.io.UnsupportedEncodingException;
import java.nio.ByteBuffer;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.atomic.AtomicLong;
@Service
public class ProducerServiceImpl implements ProducerService {
private static final Logger LOG = LoggerFactory.getLogger(ProducerServiceImpl.class);
@Value(value = "${aws.stream_name}")
private String streamName;
@Value(value = "${aws.region}")
private String awsRegion;
@Value(value = "${aws.access_key}")
private String awsAccessKey;
@Value(value = "${aws.secret_key}")
private String awsSecretKey;
private KinesisProducer kinesisProducer = null;
// 已完成的记录数量（成功写入或失败）
final AtomicLong completed = new AtomicLong(0);
private static final String TIMESTAMP_AS_PARTITION_KEY =
Long.toString(System.currentTimeMillis());
public ProducerServiceImpl() {
this.kinesisProducer = getKinesisProducer();
}
private KinesisProducer getKinesisProducer() {
if (kinesisProducer == null) {
BasicAWSCredentials awsCreds = new BasicAWSCredentials("AKIAZKRBTXXLPA3V2RW5", "kUqeO3bJyHEGziM09ru83/yU5vulbYagqHXmM4zG");
KinesisProducerConfiguration config = new KinesisProducerConfiguration();
config.setRegion("us-east-2");
config.setCredentialsProvider(new AWSStaticCredentialsProvider(awsCreds));
config.setMaxConnections(1);
config.setRequestTimeout(6000); // 6 秒
config.setRecordMaxBufferedTime(5000); // 5 秒
kinesisProducer = new KinesisProducer(config);
}
return kinesisProducer;
}
@Override
public void putDataIntoKinesis(String payload) throws Exception {
FutureCallback myCallback = new FutureCallback() {
@Override
public void onFailure(Throwable t) {
// 如果出现任何失败，我们将记录它们
int attempts = ((UserRecordFailedException) t).getResult().getAttempts().size() - 1;
if (t instanceof UserRecordFailedException) {
Attempt last =
((UserRecordFailedException) t).getResult().getAttempts().get(attempts);
if (attempts > 1) {
Attempt previous = ((UserRecordFailedException) t).getResult().getAttempts()
.get(attempts - 1);
LOG.error(String.format(
"写入记录失败 - %s : %s. 上次失败 - %s : %s",
last.getErrorCode(), last.getErrorMessage(),
previous.getErrorCode(), previous.getErrorMessage()));
} else {
LOG.error(String.format("写入记录失败 - %s : %s.",
last.getErrorCode(), last.getErrorMessage()));
}
}
LOG.error("写入过程中出现异常", t);
}
@Override
public void onSuccess(UserRecordResult result) {
long totalTime = result.getAttempts().stream()
.mapToLong(a -> a.getDelay() + a.getDuration()).sum();
LOG.info("数据写入成功。写入数据总耗时 = {}", totalTime);
completed.getAndIncrement();
}
};
final ExecutorService callbackThreadPool = Executors.newCachedThreadPool();
ByteBuffer data = null;
try {
data = ByteBuffer.wrap(payload.getBytes("UTF-8"));
} catch (UnsupportedEncodingException e) {
e.printStackTrace();
}
// 等待未完成的记录处理完成
while (kinesisProducer.getOutstandingRecordsCount() > 1e4) {
Thread.sleep(1);
}
// 将数据写入 Kinesis 数据流
ListenableFuture f =
kinesisProducer.addUserRecord(streamName, TIMESTAMP_AS_PARTITION_KEY, data);
Futures.addCallback(f, myCallback, callbackThreadPool);
}
@Override
public void stop() {
if (kinesisProducer != null) {
kinesisProducer.flushSync();
kinesisProducer.destroy();
}
}
}
清单 4-10
ProducerServiceImpl.java
```

**6:** **创建生产者函数。** 这是将数据发布到 Kinesis 的关键函数；参见清单 4-11。

```
import com.fasterxml.jackson.core.JsonProcessingException;
import com.fasterxml.jackson.databind.ObjectMapper;
import org.springframework.beans.factory.annotation.Autowired;
import java.util.function.Function;
public class ProducerFunction implements Function {
@Autowired
private ProducerService producerService;
@Override
public String apply(TrackDetail trackDetail) {
ObjectMapper mapper = new ObjectMapper();
String data = "";
try {
data = mapper.writeValueAsString(trackDetail);
producerService.putDataIntoKinesis(data);
} catch (JsonProcessingException e) {
e.printStackTrace();
} catch (Exception e) {
e.printStackTrace();
}
return "数据已成功写入 Kinesis！";
}
}
清单 4-11
ProducerFunction.java
```

**7:** **运行函数**；参见图 4-25。

![](img/526597_1_En_4_Fig25_HTML.png)

截图展示了函数成功运行的场景。

图 4-25

函数成功运行

**8:** **使用 Postman 测试。** 对`ProducerFunction`运行基于`POST`的测试以将数据发布到 Kinesis。

添加文本介绍并为图 4-26 提供上下文。

![](img/526597_1_En_4_Fig26_HTML.jpg)

截图展示了基于`POST`的测试对生产者函数的调用。包含 params、authorization、headers、body、pre-request script、tests 和 settings 标签，以及发送按钮。

图 4-26

Postman 测试

您将收到数据已保存的消息。

**9:** **在 Kinesis 中检查数据。** 此信息可通过 Kinesis 仪表板的监控部分找到，如图 4-27 所示。

![](img/526597_1_En_4_Fig27_HTML.jpg)

截图展示了 Kinesis 仪表板上的数据指标。

图 4-27

显示数据指标的 Kinesis 仪表板

**10:** **手动运行 Glue。** 从 Glue Studio 点击运行按钮启动流程，如图 4-24 所示。

如图 4-28 所示，作业开始运行。检查`s3`存储桶中的数据。

![](img/526597_1_En_4_Fig28_HTML.png)

截图展示了从 Glue Studio 运行的 Glue 作业。

图 4-28

Glue 作业运行

在本节中，您学习了如何创建一个 Spring Cloud Function，该函数可以将数据发布到 AWS Kinesis 数据流，它是数据管道的一部分。您了解到可以手动将数据发布到 Kinesis 并触发 AWS Glue 数据流管道，但我也鼓励您探索其他实现 Spring Cloud Function 与 AWS Glue 的方式，例如创建和部署触发器。有关如何在 Spring 中创建 AWS Glue 触发器的更多信息，请参阅 [`https://docs.aws.amazon.com/sdk-for-java/latest/developer-guide/examples-glue.xhtml`](https://docs.aws.amazon.com/sdk-for-java/latest/developer-guide/examples-glue.xhtml)。



## 4.4 Spring Cloud Function 与 Google Cloud Dataflow

Google Cloud Dataflow 与 Spring Cloud Data Flow 非常相似，它允许您通过源、处理器和汇构建数据管道。Dataflow 产品更容易开发。您可以在 [`https://cloud.google.com/dataflow`](https://cloud.google.com/dataflow) 查阅 Dataflow 及其功能。

本节示例中，您将创建一个包含云 pub/sub 的数据流：

Spring Cloud Function ➤ Dataflow {Cloud Pub/Sub ➤ Cloud Storage}

前提条件：

*   订阅 Google Data Flow

*   一个云 pub/sub 实例

*   一个云存储桶

*   GitHub 上的代码 [`https://github.com/banup-kubeforce/GooglePubSub`](https://github.com/banup-kubeforce/GooglePubSub)

步骤 1：创建并配置云 pub/sub 实例。

在执行此步骤前，请确保已订阅云 pub/sub，并且已正确订阅相关 API。

在您的订阅中导航至 Cloud Pub/Sub 控制台以创建主题。参见图 4-29。

![](img/526597_1_En_4_Fig29_HTML.png)

截图展示了在 Google 云上创建和配置云 pub/sub 实例的过程。

图 4-29

带有主题的 Cloud Pub/Sub

步骤 2：在云存储中创建并配置存储桶。创建云存储实例和存储桶。我创建了一个名为 `vehiclebucket1` 的存储桶来存储来自云 pub/sub 的文件；参见图 4-30。

![](img/526597_1_En_4_Fig30_HTML.jpg)

截图展示了 Google 云存储中的 vehicle bucket 1。它包含创建和刷新选项，并在存储桶下显示名称列表。

图 4-30

Google Cloud Storage 带有 vehiclebucket1

现在您已准备好构建 Dataflow 数据管道。

步骤 3：创建数据管道。导航至 Dataflow 仪表板并创建管道。我使用了一个预构建的模板来创建管道。

**1：选择模板**

此示例使用了 Pub/Sub 到 Cloud Storage 上的文本文件模板，如图 4-31 所示。

![](img/526597_1_En_4_Fig31_HTML.jpg)

截图展示了 Google 云中的数据流选项。它显示了下拉菜单中的数据流模板以及过滤选项显示多种数据流模板格式。

图 4-31

从模板创建数据管道

**2：设置 pub/sub 和云存储的参数。**

您可以选择在步骤 1 中创建的主题以及在步骤 2 中创建的存储桶。参见图 4-32。

![](img/526597_1_En_4_Fig32_HTML.jpg)

截图展示了数据流参数设置。它显示了创建管道模板、数据流模板、区域端点、输入 pub/sub 主题、输出文件名前缀和临时文件位置的选项。

图 4-32

完成 Dataflow 参数设置

**3：验证数据管道的创建。**

从图 4-33 可以看到已成功创建数据管道。

![](img/526597_1_En_4_Fig33_HTML.png)

截图展示了成功创建的 vehicle 数据管道及其管道摘要。

图 4-33

vehicle 数据管道成功创建

还会创建一个相关作业，如图 4-34 所示。

![](img/526597_1_En_4_Fig34_HTML.png)

截图展示了从模板创建的作业。它包含在 Google 云中创建的作业。

图 4-34

已创建相关作业

通过进一步深入作业的 URL 可以找到作业的图形表示，如图 4-35 所示。

![](img/526597_1_En_4_Fig36_HTML.png)

截图展示了 vehicle 存储桶的详细信息。它包含 vehicle bucket 1、位置、存储类别、公共访问和保护，对象标签下显示了 vehicle 信息。

图 4-36

存储桶显示了已发布的文档

![](img/526597_1_En_4_Fig35_HTML.png)

截图展示了作业的图形表示。它包含读取 pub-sub 事件、5 米窗口和写入文件。

图 4-35

管道执行

步骤 4：创建 Spring Cloud Function。

在此 Spring Cloud Function 中，您将创建以下类：

*   `MessageEntity` 类用于构建消息

*   `TrackDetail` 类作为实体类

*   `PubSubPublisher` 类用于订阅主题并发布数据

*   `ProducerFunction` 类用于实现 Spring Cloud Function

前提条件：

*   Maven 依赖项

您需要在 `spring-cloud-gcp-starter-pubsub` 和 `spring-integration-core` 之外添加 `spring-cloud-function-web`；参见清单 4-12。

```
org.springframework.boot
spring-boot-starter-web

org.springframework.cloud
spring-cloud-function-web

com.google.cloud
spring-cloud-gcp-starter-pubsub
3.3.0

org.springframework.integration
spring-integration-core

org.springframework.boot
spring-boot-starter-test
test

清单 4-12
Maven 依赖项
```

`Application.properties` 文件如清单 4-13 所示。

```
spring.cloud.function.definition=producerFunction
spring.cloud.gcp.project-id=springcf-348721
spring.cloud.gcp.credentials.location=file:C://Users//banua//Downloads//application_default_credentials.json
pubsub.topic=projects/springcf-348721/topics/vehicletopic
清单 4-13
application.properties
```

**1：创建** `MessageEntity` **类。**

如清单 4-14 所示，存储带有时间戳的消息。

```
package com.kubeforce.googlepubsub;
import java.time.LocalDateTime;
public class MessageEntity {
private final LocalDateTime timestamp;
private final String message;
public MessageEntity(LocalDateTime timestamp, String message) {
this.timestamp = timestamp;
this.message = message;
}
public LocalDateTime getTimestamp() {
return timestamp;
}
public String getMessage() {
return message;
}
}
清单 4-14
MessageEntity.java
```

**2：创建** `TrackDetail` **类。**

`TrackDetail` 类将包含三个字段—`vehicleId`、`driverId` 和 `driverName`；参见清单 4-15。

```
package com.kubeforce.googlepubsub;
public class TrackDetail {
private String vehicleId;
private String driverId;
private String driverName;
public String getVehicleId() {
return vehicleId;
}
public void setVehicleId(String vehicleId) {
this.vehicleId = vehicleId;
}
public String getDriverId() {
return driverId;
}
public void setDriverId(String driverId) {
this.driverId = driverId;
}
public String getDriverName() {
return driverName;
}
public void setDriverName(String driverName) {
this.driverName = driverName;
}
}
清单 4-15
TrackDetail.java
```

**3：创建** `PubSubPublisher` **。**

`PubSubPublisher` 将使用在 `application.properties` 中定义的主题发送消息；参见清单 4-16。

```
package com.kubeforce.googlepubsub;
import com.google.cloud.spring.pubsub.core.PubSubTemplate;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Component;
@Component
public class PubSubPublisher {
private final String topic;
private final PubSubTemplate pubSubTemplate;
public PubSubPublisher(
@Value(“${pubsub.topic}”) String topic,
PubSubTemplate pubSubTemplate) {
this.topic = topic;
this.pubSubTemplate = pubSubTemplate;
}
public void publish(String payload) {
pubSubTemplate.publish(topic, payload);
}
}
清单 4-16
PubSubPublisher.java
```

**4：创建 Spring Cloud Function。**

`ProducerFunction` 将使用在 `application.properties` 中定义的主题发送消息。参见清单 4-17。



```
package com.kubeforce.googlepubsub;
import com.fasterxml.jackson.core.JsonProcessingException;
import com.fasterxml.jackson.databind.ObjectMapper;
import org.springframework.beans.factory.annotation.Autowired;
import java.time.LocalDateTime;
import java.util.function.Function;
public class ProducerFunction implements Function {
@Autowired
private PubSubPublisher publisher;
@Override
public String apply(TrackDetail trackDetail) {
ObjectMapper mapper = new ObjectMapper();
String data = “”;
try {
data = mapper.writeValueAsString(trackDetail);
MessageEntity entity = new MessageEntity(LocalDateTime.now(), data);
publisher.publish(data);
} catch (JsonProcessingException e) {
e.printStackTrace();
} catch (Exception e) {
e.printStackTrace();
}
return "Saved data into Kinesis successfully!";
}
}
Listing 4-17
ProducerFunction.java
```

**5:** **运行应用程序并测试是否已将消息发布到 Cloud Pub/Sub。**

前往 Google Cloud 控制台中的 Pub/Sub 控制台，验证消息是否已发布；参见图 4-37。

![](img/526597_1_En_4_Fig37_HTML.png)

一张截图展示了在 Google 云的 Pub/Sub 控制台中发布的消息。

图 4-37

消息已发布到 Cloud Pub/Sub

**6: 验证消息是否已加载到 Cloud Storage 中。**

前往 Google Cloud 中的 Cloud Storage 控制台以验证消息是否已加载；参见图 4-38。

![](img/526597_1_En_4_Fig38_HTML.jpg)

一张截图展示了 Cloud Storage 控制台，用于验证消息是否已加载到存储中。它显示了 vehicle bucket one 的存储桶详细信息。

图 4-38

消息已加载到 Cloud Storage 中

在本节中，您学习了如何使用 Spring Cloud Function 触发基于 Google Cloud Dataflow 的数据管道。

## 4.5 总结

本章解释了如何创建数据流和数据管道，无论是使用 SCDF 在本地还是在云环境中。对于云环境，您可以使用 SCDF 或云原生工具。

Spring Cloud Function 功能强大，可以在数据管道的上下文中用作触发器或流程的一部分。

通过 AWS Glue 和 Google Data Flow，您了解到可以将 Spring Cloud Function 用作流程的触发器。这需要通过添加相关库并调用流程来实现一些额外的编码。

后续章节将探讨 Spring Cloud Function 的其他用例。

