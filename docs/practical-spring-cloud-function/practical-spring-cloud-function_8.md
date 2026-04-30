# 7. 使用 Spring Cloud Function 的行业特定示例

本章探讨了 Spring Cloud Function 在不同行业中的具体实现方案。

它利用 IBM Cloud 服务来演示 Spring Cloud Function 支持任何云平台的能力。

本章中的一些示例是现实世界中的场景，其中函数发挥着关键作用。例如，一个发送管道泄漏警报的函数，以及一个帮助客户解决问题的聊天函数。

## 7.1 使用 Spring Cloud Function 和 IoT 的石油/天然气管道追踪

本节探讨 Spring Cloud Function 在天然气管道场景中的应用。

数英里长的天然气管道需要持续监控泄漏、裂缝、温度波动和压力问题，以避免昂贵的气体传输故障和爆炸风险。

该解决方案利用 IBM Cloud 及其物联网平台服务，结合智能网关来捕获传感器数据并提供分析和可视化。您将看到如何部署 Spring Cloud Function 并将其与 IBM 事件流集成，以将流数据存储到 IBM Cloudant 数据库中。详见图 7-1。

![](img/526597_1_En_7_Fig1_HTML.png)

天然气管道实施的架构图。包含天然气管道、传感器、物联网网关、蜂窝网络、MQTT 和监控仪表板。

图 7-1

天然气管道物联网实现

所需组件详见表 7-1。

表 7-1

示例解决方案组件

| 传感器 | 首选传感器 |
| --- | --- |
| 物联网网关 | 您的首选网关产品 |
| IBM Cloud Functions | [`https://cloud.ibm.com/functions/`](https://cloud.ibm.com/functions/) |
| IBM Watson IoT 平台 | [`https://www.ibm.com/cloud/watson-iot-platform`](https://www.ibm.com/cloud/watson-iot-platform) |
| IBM Watson IoT 平台-消息网关 | [`https://www.ibm.com/docs/en/wip-mg`](https://www.ibm.com/docs/en/wip-mg) |
| IBM 事件流 | [`https://www.ibm.com/cloud/event-streams`](https://www.ibm.com/cloud/event-streams) |
| IBM Cloudant 数据库 | [`https://www.ibm.com/cloud/cloudant`](https://www.ibm.com/cloud/cloudant) |

### 7.1.1 传感器

在监控管道健康状况时，需要测量和追踪多个参数。这些数据可以分为资产数据和外部数据两类。

资产数据可能包括压力、流量速率、管壁厚度和裂缝。外部数据可能包括温度、湿度、pH 值和土壤电阻率。智能传感器可以安装在管道沿线，将信息传输到附近的物联网网关。



### 7.1.3 IBM Cloud Functions

IBM Cloud 提供无服务器云函数，可以通过基于网络的控制台编写脚本，也可以通过 Docker 容器部署。如果你使用 Java 和 dotnet 进行开发，需要使用 Docker 选项。或者，可以通过 Kubernetes 上的 Knative 来部署 Spring Cloud Function 的 Docker 镜像。更多信息请参见 [`https://www.ibm.com/cloud/functions`](https://www.ibm.com/cloud/functions)。

### 7.1.4 IBM Watson IoT Platform

IBM 的这款产品也允许你设置和管理连接设备。它提供了一个仪表板，支持设备管理、MQTT 支持、数据生命周期管理以及通过 TLS 加密的 MQTT 安全通信。更多信息请参见 [`https://www.ibm.com/cloud/internet-of-things`](https://www.ibm.com/cloud/internet-of-things)。

### 7.1.5 IBM Watson IoT Platform: Message Gateway

IBM Message Gateway 接收 IoT 传感器发送的消息，并将其转发至 IBM Event Streams 等平台。

更多信息请参见 [`https://www.ibm.com/docs/en/wip-mg`](https://www.ibm.com/docs/en/wip-mg)。

### 7.1.6 IBM Event Streams

该服务基于 Apache Kafka，允许管理来自 IBM Watson IoT 平台的流式事件数据。更多信息请参见 [`https://www.ibm.com/cloud/event-streams`](https://www.ibm.com/cloud/event-streams)。

### 7.1.7 IBM Cloudant DB

这是一个完全托管的分布式数据库，允许存储来自 IBM Watson IoT 平台的数据。更多信息请参见 [`https://www.ibm.com/cloud/cloudant`](https://www.ibm.com/cloud/cloudant)。

现在你将看到如何实现图 7-2 中所示的流程，其中 IoT 传感器通过消息网关将信息发送至 IBM Event Stream。消息随后通过 Spring Cloud Function 从 IBM 事件流接收，并存储到 Cloudant DB 中。建议你通过 [`https://www.ibm.com/docs/en/wip-mg/5.0.0.1?topic=os-imaserver-rpm`](https://www.ibm.com/docs/en/wip-mg/5.0.0.1%253Ftopic%253Dos-imaserver-rpm) 探索传感器与消息网关的集成。

![](img/526597_1_En_7_Fig2_HTML.png)

数据传感器的流程模型。传感器通过 MQTT、事件流和 Spring Cloud Function 将信息发送到 IBM 云，最后存储在数据库中。

图 7-2

传感器数据消费流程用例

图 7-2 展示了本示例计划实现的流程。

重点在于创建一个 Spring Cloud Function，接收发布到事件流中名为 "Sensor-topic" 的主题的消息，并将其存储到 Cloudant DB 中。

步骤 1：注册 IBM Cloud 并订阅 Watson IoT 平台。

你可以从 [`https://www.ibm.com/cloud/internet-of-things`](https://www.ibm.com/cloud/internet-of-things) 开始，并点击“开始使用”以启动流程。

如果你只是尝试体验，可以使用免费层级（Lite）。参见图 7-3。

![](img/526597_1_En_7_Fig3_HTML.png)

标题为 IBM cloud Watson 的窗口截图。该窗口包含 IoT 平台应用，带有“创建”选项。需要选择位置、选择定价计划并配置资源。

图 7-3

IBM Cloud Watson IOT

步骤 2：添加需要监控的设备。

要进入添加设备页面，请在右上角点击你的姓名，然后点击步骤 1 中创建的 IoT 资源；参见图 7-4。

![](img/526597_1_En_7_Fig4_HTML.png)

标题为 IBM Watson IOT 平台的窗口截图。该页面包含添加设备的界面，提供设备 ID、设备类型和设备 ID 等选项。

图 7-4

添加设备页面

添加设备页面会引导你逐步配置设备。需要选择设备类型和设备 ID，然后提供序列号、型号等设备信息。之后可以输入可选的令牌。点击“完成”按钮预览你输入的所有信息。这将创建设备，如图 7-5 所示。

![](img/526597_1_En_7_Fig5_HTML.png)

Watson IOT 平台窗口的截图。该窗口包含浏览设备应用以添加设备和应用，包含设备 ID、状态、设备类型、类 ID、地址和位置等信息。

图 7-5

在 Watson IoT 平台上添加设备

步骤 3：订阅 IBM 事件流。

同样，你可以选择使用免费层级（Lite）。参见图 7-6。

![](img/526597_1_En_7_Fig6_HTML.png)

标题为 IBM cloud 的窗口截图，其中包含事件流。需要选择创建、平台、公共云、卫星、位置和选择定价计划等选项。

图 7-6

IBM Cloud 事件流

随后你需要指定事件流的运行位置——在公共云或卫星上，以及云实例的位置。然后选择定价计划（Lite 层级为免费），为其命名并分配资源组。你还可以添加一些标识标签。这将创建事件流。

步骤 4：创建包含所需参数的主题。



在您的事件流仪表板中，单击“创建主题”按钮，输入主题名称以及任何分区和消息保留参数。这将创建一个主题，您可以在事件流页面上看到，如图 7-7 所示。

![](img/526597_1_En_7_Fig7_HTML.png)

事件流仪表板的截图。它提供了名称、分区、保留时间、保留大小和清理策略等主题选项。

图 7-7

创建事件流

步骤 5：部署函数到 IBM Cloud。

此步骤不使用 IBM Cloud 内置的函数。您可以使用 IBM Kubernetes 服务来构建和部署 Spring Cloud Function 到 Knative 服务。您可以通过搜索 IBM Cloud 或目录，或者直接访问 [`https://cloud.ibm.com/kubernetes/catalog/create`](https://cloud.ibm.com/kubernetes/catalog/create) 进入 IBM Kubernetes 服务。

现在创建一个最小化的免费集群。这是我喜欢 IBM Cloud 的地方；即使对于 Kubernetes 集群，您也能真正获得免费服务。这与其他云提供商不同，其他云提供商需要为底层计算和存储付费。

如果您创建的是免费集群，请注意此集群将在 30 天后被删除。

一旦进入 Kubernetes 集群页面，选择定价计划，IBM Cloud 将为您创建 Kubernetes 集群。使用 Lite 计划时，您无法命名集群。您可以订阅标准层级以获得更多控制权。参见图 7-8。

![](img/526597_1_En_7_Fig8_HTML.png)

IBM Kubernetes 集群仪表板的截图。它提供了创建、计划详情、Kubernetes 版本和资源详情等选项。

图 7-8

订阅 IBM Kubernetes

IBM Cloud 将完成集群创建过程，并将您带到图 7-9 所示的页面。

![](img/526597_1_En_7_Fig10_HTML.jpg)

创建集群后的指令集。它包含 3 个连接到集群的步骤：登录、设置上下文和验证。底部还包含一个提示。

图 7-10

创建集群后的标准说明

![](img/526597_1_En_7_Fig9_HTML.png)

IBM 集群仪表板的截图。包含概览和节点健康等详情。

图 7-9

集群成功创建

通过运行以下命令连接到集群以获取集群信息：

```
$ ibmcloud ks cluster config --cluster cc57gphf0fcppc4gtp6g
```

![](img/526597_1_En_7_Fig11_HTML.png)

运行连接到集群命令的截图。

图 7-11

您的 kubectl 已配置为连接到集群

验证当前配置。运行以下命令将 Kubectl 上下文设置为 Kubernetes 集群：

```
$ kubectl config current-context
```

![](img/526597_1_En_7_Fig12_HTML.jpg)

设置 Kubernetes 集群上下文的命令截图。

图 7-12

建立与集群的连接

现在安装 Knative 服务。运行以下命令安装 Knative 服务组件：

```
$ kubectl apply -f https://github.com/knative/serving/releases/download/knative-v1.7.1/serving-crds.yaml
```

![](img/526597_1_En_7_Fig13_HTML.png)

运行安装 Knative crds 命令的截图。控制台显示了 12 个配置。

图 7-13

Knative crds 已创建

运行以下命令安装 Knative 服务核心组件：

```
$ kubectl apply -f https://github.com/knative/serving/releases/download/knative-v1.7.1/serving-core.yaml
```

![](img/526597_1_En_7_Fig14_HTML.png)

运行安装 Knative 服务核心命令的截图。

图 7-14

Knative 服务核心已部署

安装 Istio。Istio 为集群创建入口。运行以下代码进行安装：

```
$ kubectl apply -l knative.dev/crd-install=true -f https://github.com/knative/net-istio/releases/download/knative-v1.7.0/istio.yaml
```

![](img/526597_1_En_7_Fig15_HTML.jpg)

运行 Istio 安装命令的截图。它包含多个组件中的两个高亮文本。

图 7-15

Istio 已成功部署

现在运行以下命令配置网络组件：

```
$ kubectl apply -f https://github.com/knative/net-istio/releases/download/knative-v1.7.0/net-istio.yaml
```

![](img/526597_1_En_7_Fig16_HTML.png)

运行构建网络组件命令的截图。

图 7-16

Istio 网络组件已创建

现在安装 Magic DNS。它允许您通过完全限定域名(FQDN)调用服务，而不是通过 IP 地址。

```
$ kubectl apply -f https://github.com/knative/serving/releases/download/knative-v1.7.1/serving-default-domain.yaml
```

![](img/526597_1_En_7_Fig17_HTML.png)

运行安装 Magic DNS 命令的截图。

图 7-17

默认域名（Magic DNS）已创建

运行以下命令获取服务状态：

```
$ kubectl get pods --namespace knative-serving
```

![](img/526597_1_En_7_Fig18_HTML.jpg)

运行 Knative 组件的截图。它包含名称、就绪状态、重启次数和年龄等列标签。

图 7-18

Knative 组件正在运行

一旦状态为`Running`，您就可以部署 Spring Cloud Function。

步骤 6：创建 Cloudant 数据库。进入 IBM Cloud 的订阅页面，单击“创建资源”。搜索 Cloudant。

您也可以通过 [`https://cloud.ibm.com/catalog/services/cloudant`](https://cloud.ibm.com/catalog/services/cloudant) 直接访问 Cloudant 页面。

![](img/526597_1_En_7_Fig19_HTML.jpg)

IBM Cloud 窗口的截图。它显示了名称、组、位置、产品、状态和标签等资源列表。

图 7-19

IBM Cloud 资源

选择“服务与软件”列表中的 Cloudant 实例以开始配置您的实例。根据您的需求设置 Cloudant 配置。参见图 7-20。

![](img/526597_1_En_7_Fig20_HTML.jpg)

IBM Cloud 的 Cloudant-p 5.1 的截图。包含概览、容量和文档等列标签。概览包含部署详情。

图 7-20

IBM Cloud Cloudant 规格

您可以在屏幕顶部使用“创建数据库”功能。提供名称和分区信息以创建数据库。一旦此过程完成，您就可以看到数据库，如图 7-21 所示。

![](img/526597_1_En_7_Fig21_HTML.png)

IBM Cloud 的截图。显示 Cloudant 数据库及其创建选项，数据库格式由箭头指示。

图 7-21

Cloudant 数据库

步骤 7：创建 Spring Cloud Function 以连接事件流和 Cloudant 数据库。您可以在 [`https://github.com/banup-kubeforce/IBMIoTV2`](https://github.com/banup-kubeforce/IBMIoTV2) 下载它。

您使用 Spring Kafka 和 Cloudant 作为 Maven 依赖项，如清单 7-1 所示。

```

com.ibm.cloud
cloudant
0.3.0

com.cloudant
cloudant-client
2.20.1

清单 7-1
依赖项
```

清单 7-2 显示了`application.properties`。



```
spring.cloud.function.definition=iotCloudantSupplier
cloudant.db=sensordb
cloudant.url="https://apikey-v2-w2z4fgix9dlpw626eihi4g4n9w20ntyekk7jknbyr1o:1d012fa20433d315b899a2ed90f3fefb@23204af3-2c33-4bfb-bbc4-f55bbe1902ea-bluemix.cloudantnosqldb.appdomain.cloud"
cloudant.apikey="Service credentials-1"
spring.datasource.url=jdbc:h2:mem:employee
spring.datasource.driverClassName=org.h2.Driver
spring.datasource.username=sa
spring.datasource.password=
spring.h2.console.enabled=true
spring.jpa.database-platform=org.hibernate.dialect.H2Dialect
spring.jpa.defer-datasource-initialization=true
#EventStreams
#Connection
spring.kafka.jaas.enabled=true
spring.kafka.jaas.login-module=org.apache.kafka.common.security.plain.PlainLoginModule
spring.kafka.jaas.options.username=token
spring.kafka.jaas.options.password=NhZ7i_IHpf3piG99jQpIMGtZTT3tRggmfj7UhaztdNFx
spring.kafka.bootstrap-servers=broker-2-2068cxqswxtbl1kv.kafka.svc09.us-south.eventstreams.cloud.ibm.com:9093,broker-1-2068cxqswxtbl1kv.kafka.svc09.us-south.eventstreams.cloud.ibm.com:9093,broker-0-2068cxqswxtbl1kv.kafka.svc09.us-south.eventstreams.cloud.ibm.com:9093,broker-5-2068cxqswxtbl1kv.kafka.svc09.us-south.eventstreams.cloud.ibm.com:9093,broker-3-2068cxqswxtbl1kv.kafka.svc09.us-south.eventstreams.cloud.ibm.com:9093,broker-4-2068cxqswxtbl1kv.kafka.svc09.us-south.eventstreams.cloud.ibm.com:9093
spring.kafka.properties.security.protocol=SASL_SSL
spring.kafka.properties.sasl.mechanism=PLAIN
#Producer
spring.kafka.template.default-topic=sensor-topic
spring.kafka.producer.client-id=event-streams-kafka
spring.kafka.producer.key-serializer=org.apache.kafka.common.serialization.StringSerializer
spring.kafka.producer.value-serializer=org.apache.kafka.common.serialization.StringSerializer
#Consumer
listener.topic=sensor-topic
spring.kafka.consumer.group-id=sensor-topic
spring.kafka.consumer.auto-offset-reset=earliest
spring.kafka.consumer.key-deserializer=org.apache.kafka.common.serialization.StringDeserializer
spring.kafka.consumer.value-deserializer=org.apache.kafka.common.serialization.StringDeserializer
Listing 7-2
Cloudant 和 IBM 事件流配置
```

您需要将设备 ID、协议、数据和状态信息整合到 JSON 对象中，供 Cloudant 存储。请参见列表 7-3 至 7-5。

```
package com.kubeforce.ibmiotv2;
import org.json.JSONObject;
import org.springframework.kafka.core.KafkaTemplate;
import java.util.List;
import java.util.concurrent.CopyOnWriteArrayList;
import java.util.function.Supplier;
public class EventStreamsSupplier implements Supplier {
CloudantConnector cloudantConnector = new CloudantConnector();
private KafkaTemplate template;
private List messages = new CopyOnWriteArrayList();
@Override
public String get() {
String result = messages.toString();
JSONObject iotdata = new JSONObject();
iotdata.put("message",result);
cloudantConnector.db.save(iotdata);
messages.clear();
return result;
}
}
Listing 7-5
EventStreamsSupplier.java（查看 GitHub 获取最新版本）
```

```
import com.cloudant.client.api.ClientBuilder;
import com.cloudant.client.api.CloudantClient;
import com.cloudant.client.api.Database;
public class CloudantConnector {
CloudantClient client = ClientBuilder.account("23204af3-2c33-4bfb-bbc4-f55bbe1902ea-bluemix")
.iamApiKey("B2DtIBIPIP1qhFt6swrl-0nbQZcY6ZB1SVti_9zKX832")
.build();
Database db = client.database("sensordb",false);
}
Listing 7-4
CloudantConnector.java
```

```
import com.cloudant.client.api.ClientBuilder;
import com.cloudant.client.api.CloudantClient;
import com.cloudant.client.api.Database;
import org.json.JSONObject;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import java.util.Map;
import java.util.function.Consumer;
/*
{
"deviceid":"gas-sensor1",
"protocol":"mqtt",
"data":"{temp:25,pressure:35}",
"status":"警告"
}
*/
public class IotCloudantConsumer implements Consumer> {
public static final Logger LOGGER = LoggerFactory.getLogger(IotCloudantConsumer.class);
@Autowired
private IotRepository iotRepository;
CloudantClient client = ClientBuilder.account("23204af3-2c33-4bfb-bbc4-f55bbe1902ea-bluemix")
.iamApiKey("B2DtIBIPIP1qhFt6swrl-0nbQZcY6ZB1SVti_9zKX832")
.build();
Database db = client.database("sensordb",false);
@Override
public void accept (Map map)
{
LOGGER.info("Creating the Iot sensor info", map);
JSONObject iotdata = new JSONObject();
iotdata.put("deviceid","gas-sensor1");
iotdata.put("protocol","mqtt");
iotdata.put("data","{temp:25,pressure:35}");
iotdata.put("status","警告");
IotSensor sensorinfo = new IotSensor (map.get("deviceid"), (map.get(
"protocol")), map.get("data"), map.get("protocol"));
db.save(iotdata);
}
}
Listing 7-3
CloudantConsumer.java
```

`EventStreamsSupplier`函数连接到 IBM 事件流服务，并从`sensors-topic`主题获取数据。随后将这些数据存储到 Cloudant 的`sensordb`数据库中。

步骤 8：将 Spring Cloud Function 部署到 Knative 上，部署到 Kubernetes 集群。有关如何在 Knative 上部署 Spring Cloud Function，请参见第 2 章。

步骤 9：集成事件流以调用 Spring Cloud Function。调用`EventStreamSupplier`函数。当在本地运行该函数时，您将在图 7-22 中看到输出结果。

![](img/526597_1_En_7_Fig22_HTML.png)

程序事件流供应函数本地运行的输出截图。

图 7-22

本地运行 EventStreamsSupplier

图 7-23 展示了存储在 Cloudant 数据库中的信息。

![](img/526597_1_En_7_Fig23_HTML.png)

传感器数据库程序的截图。它提供了保存更改的数据库选项，还包含上传附件、克隆数据库和删除数据库的功能。

图 7-23

CloudantDB 中存储的物联网数据

本节从 IBM 云和 IBM Watson 物联网平台的角度探讨了物联网领域。图 7-24 展示了本章涵盖的物联网平台各个组件。

您还了解到，可以使用 Spring Cloud Function 构建函数，并将其部署到 IBM 云的 Kubernetes 集群上。

您订阅了 IBM 云服务，创建了物联网平台中的设备，建立了 Kubernetes 集群，创建了 Cloudant 数据库，构建了将数据写入 Cloudant 数据库的函数，将事件流连接到该函数，并将 Cloudant 数据库连接到物联网平台。为了建立端到端流程，需要配置大量内容。请参见图 7-24。

![](img/526597_1_En_7_Fig24_HTML.jpg)

Watson 物联网端到端流程的流程图。上下文从物联网设备开始，经过 Watson 物联网平台服务、事件流和仪表板，最终连接到客户系统、应用程序开发架构师和 IBM 云。

图 7-24

Watson 物联网上下文



## 7.2 通过 Spring Cloud Function 和大数据管道实现医疗健康

医疗健康依赖于数据。无论是患者记录、图像识别、基因组学、追踪可穿戴设备还是使用传感器，医疗系统都会被大量数据所淹没。图 7-25 概念化了医疗健康中的大数据。

![](img/526597_1_En_7_Fig25_HTML.jpg)

一个医疗健康场景的架构图包含大数据环形组件。中央部分从上至下流动着数据聚合、分析和行动。数据聚合进一步连接到其他组件。

图 7-25

医疗健康场景 来源：[*https://www.researchgate.net/publication/330249318/figure/fig1/AS:713182909829120@1547047464835/Conceptualizing-big-data-in-healthcare-Health-system-data-are-aggregated-with-data.png*](https://www.researchgate.net/publication/330249318/figure/fig1/AS:713182909829120%253F1547047464835/Conceptualizing-big-data-in-healthcare-Health-system-data-are-aggregated-with-data.png) `(知识共享协议 https://creativecommons.org/licenses/by/4.0/)`

此处的用例是：一位患者走进医院抱怨背部（靠近脊柱）疼痛。这种疼痛持续存在，他们希望快速获得诊断。

流程如下：

1.  患者通过患者门户和签到流程进入医院。

2.  初始患者分析使用电子健康记录（EHR）和患者提供的信息（如生活质量、疼痛程度、期望值等）进行。

3.  医生建议进行影像学检查和实验室检测。

4.  患者被提供智能可穿戴设备以追踪其在医院内的活动。

5.  当影像学检查、实验室检测和其他相关信息汇总后，医生使用智能 AI 诊断门户进行可能疾病的诊断。

6.  医生向患者通报诊断结果。

现在让我们看看这个流程涉及的系统：

*   患者门户（数据采集）

*   患者信息采集与汇总（数据聚合）

*   实验室和影像学检查（图像数据）

*   诊断（数据分析与 AI 推荐）

*   智能可穿戴设备（物联网实时数据）

将这一流程转化为云架构视角，可以看出 Spring Cloud Function 在所有这些流程中都扮演着重要角色。因此，确保函数可移植性至关重要，以便在每个场景中都能使用最佳的云服务提供商。参见图 7-26。

![](img/526597_1_En_7_Fig26_HTML.jpg)

一个展示 Spring Cloud Function 应用的架构图。包含五个组件：患者门户、初始患者分析、实验室和影像学检查、诊断以及智能手环和设备。这些组件的图标位于旁边。

图 7-26

Spring Cloud Function 在医疗健康流程中的应用

## 7.3 使用 Spring Cloud Function 的零售业对话式 AI

AI 与零售业紧密相连。人类享受购物、逛商场、在线购物以及单纯的网络浏览。他们不断接触到 AI，例如通过定向广告、产品推荐和产品展示。许多这些功能由机器人实现。这意味着 AI 与零售业正在融合。

根据财富商业洞察，零售 AI 市场预计到 2027 年将达到 199 亿美元，2020 年至 2027 年的年复合增长率达 34.4%（来源：[`https://www.meticulousresearch.com/product/artificial-intelligence-in-retail-market-4979`](https://www.meticulousresearch.com/product/artificial-intelligence-in-retail-market-4979)）。

AI 在零售业中的主要应用方式包括：

*   基于大数据的预测分析

*   需求预测

*   视觉搜索

*   推荐系统

*   聊天机器人

*   增强现实

*   品牌管理

新冠疫情改变了 AI 与零售业的互动方式。由聊天机器人驱动的 AI 功能变得尤为重要。

实际上，聊天机器人（或对话式 AI）成为了零售业的 AI 接口。大多数与产品或支持团队的沟通都是通过 AI 交互会话发起的。通过这些聊天机器人提供更好的客户体验对零售商至关重要。如果聊天机器人表现不佳，客户可能会转向其他零售商。因此，品牌管理、推荐、预测和预测都依赖于聊天机器人如何与客户互动，以及是否能提取正确信息以提供最佳体验。

IBM、AWS、Azure、Google 等云服务商将注意力集中在对话式 AI 市场，因为每个零售商都渴望通过这些客户互动获得竞争优势。

我们将探讨 IBM 如何应对对话式 AI，以及 Spring Cloud Function 在连接系统与用户方面的作用。

IBM 的对话式 AI 解决方案包括 IBM Cloud 和云套装（Cloud Pak）上的多种产品。云套装是可以在任何云平台上部署的软件。

IBM Cloud 提供通用 Kubernetes 和 Red Hat OpenShift Kubernetes，而云套装则基于 Red Hat OpenShift 提供。Spring Cloud Function 可以部署在任何 Kubernetes 平台。

图 7-27 展示了对话式 AI 的概念性表示。

![](img/526597_1_En_7_Fig27_HTML.png)

一个展示人工智能对话概念的架构图。包含数据源、对话式 AI 处理、对话渠道和结果。所有这些都有子组件。

图 7-27

对话式 AI 概念

将这一概念转化为基于 IBM Cloud 的架构，即可得到图 7-28。

![](img/526597_1_En_7_Fig28_HTML.jpg)

一个展示对话式 AI 系统的架构图。流程从对话端点开始，经过边缘服务、IBM 云，最终到达用户。IBM 云包含相关组件。

图 7-28

对话式 AI 系统视图

### 7.3.1 对话式 AI 解决方案的组件

1.  边缘服务

    边缘服务允许互联网数据流动。这些服务包括 DNS、CDN、防火墙、负载均衡器等。

2.  IBM Watson Assistant

    Watson Assistant 使您能够创建结合机器学习、自然语言处理和图形工具的虚拟代理和聊天机器人，以设计您的对话流程。更多信息请访问 [`https://www.ibm.com/products/watson-assistant`](https://www.ibm.com/products/watson-assistant)。

3.  IBM Watson Discovery Services

    Watson Discovery Services 帮助您摄入、解析、索引和注释内容。摄入的数据可以来自内部和外部数据源。更多信息请访问 [`https://www.ibm.com/cloud/watson-discovery`](https://www.ibm.com/cloud/watson-discovery)。

4.  Watson 语音转文本

    Watson 语音转文本将语音转换为文本。更多信息请访问 [`https://www.ibm.com/cloud/watson-speech-to-text`](https://www.ibm.com/cloud/watson-speech-to-text)。

5.  Spring Cloud Function 部署在 OpenShift（IBM Cloud）上的 Knative

    Spring Cloud Function 可以部署在 IBM Cloud 的 OpenShift 平台上。需要在 OpenShift 上配置 Knative。此过程在本书的 Knative 部署讨论中进行了详细说明。

6.  云上 DB2

    这是一个完全托管的 SQL 数据库。关于云上 DB2 的信息请访问 [`https://www.ibm.com/cloud/db2-on-cloud`](https://www.ibm.com/cloud/db2-on-cloud)。

7.  Cloudant 数据库

    这是一个完全托管的分布式数据库，适用于 Web 和移动应用，以及存储物联网或移动数据。更多信息请访问 [`https://www.ibm.com/cloud/cloudant`](https://www.ibm.com/cloud/cloudant)。



### 7.3.2 Watson Assistant Webhooks 与 Spring Cloud Function

一旦您创建并部署了一个 Spring Cloud Function，就需要能够从 Watson Assistant 调用它。这可以通过 Webhooks 实现。

Webhooks 需要满足以下要求：

*   调用必须是 `POST` HTTP 请求。您可以在此处使用消费者函数。

*   请求正文必须是 JSON 对象（`Content-Type: application/json`）。

*   响应必须是 JSON 对象（`Accept: application/json`）。

*   调用必须在八秒或更少的时间内完成。这是一个重要要求，使用 Spring Cloud Function 可以轻松满足。

### 7.3.3 使用 Spring Cloud Function 实现 Watson Assistant

步骤 1：编写 Spring Cloud Function 代码。Watson Assistant 需要 OpenAPI 规范。将本节中的代码添加到您的 `Pom.xml` 文件中。

清单 7-6 显示了依赖项。

```
org.springdoc
springdoc-openapi-ui
1.6.11

清单 7-6
连接到 Watson Assistant 的 Open API 依赖项
```

现在您需要为 iMac 库存创建 Spring Cloud Function 代码。该模型允许 Watson 连接并获取响应。请参见清单 7-7。

```
package com.kubeforce.watsonimacs;
import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.Id;
import javax.persistence.Table;
@Entity
@Table(name= "inventory")
public class Inventory {
@Id
@GeneratedValue(generator = "UUID")
private Long id;
private String name;
private int inventoryid;
private String description;
private String quantity;
public Inventory(String name, int invid, String description, String quantity)
{
this.name = name;
this.inventoryid = invid;
this.description = description;
this.quantity = quantity;
}
public Inventory() {
}
public String getName ()
{
return name;
}
public void setName (String name)
{
this.name = name;
}
public int getEmployeeIdentifier ()
{
return inventoryid;
}
public void setCustomerIdentifier (int invid)
{
this.inventoryid = invid;
}
public String getEmail ()
{
return description;
}
public void setEmail (String email)
{
this.description = description;
}
public String getSalary ()
{
return quantity;
}
public void setSalary (String salary)
{
this.quantity = quantity;
}
public Long getId ()
{
return id;
}
public void setId (Long id)
{
this.id = id;
}
}
清单 7-7
inventory.java
```

清单 7-8 显示了 `InventoryConsumer` 文件。

```
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import java.util.Map;
import java.util.function.Consumer;
public class InventoryConsumer implements Consumer> {
public static final Logger LOGGER = LoggerFactory.getLogger(InventoryConsumer.class);
@Autowired
private InventoryRepository InventoryRepository;
@Override
public void accept (Map map)
{
LOGGER.info("Creating the inventory", map);
Inventory inventory = new Inventory (map.get("name"), Integer.parseInt(map.get(
"inventoryid")), map.get("description"), map.get("quantity"));
InventoryRepository.save(inventory);
}
}
清单 7-8
InventoryConsumer.java
```

清单 7-9 显示了 `InventoryFunction` 文件。

```
package com.kubeforce.watsonimacs;
import org.springframework.beans.factory.annotation.Autowired;
import java.util.Optional;
import java.util.function.Function;
public class InventoryFunction implements Function  {
@Autowired
private InventoryRepository inventoryRepository;
@Override
public Inventory apply (Long s)
{
Optional inventoryOptional = inventoryRepository.findById(s);
if (inventoryOptional.isPresent()) {
return inventoryOptional.get();
}
return null;
}
}
清单 7-9
InventoryFunction.java
```

清单 7-10 显示了 `InventorySupplier` 文件。

```
package com.kubeforce.watsonimacs;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;
import java.util.List;
import java.util.function.Supplier;
@Component
public class InventorySupplier implements Supplier
{
public static final Logger LOGGER = LoggerFactory.getLogger(InventorySupplier.class);
@Autowired
private InventoryRepository InventoryRepository;
@Override
public Inventory get ()
{
List inventories = InventoryRepository.findAll();
LOGGER.info("Getting the computer of our choice - ", inventories);
return inventories.get(0);
}
}
清单 7-10
InventorySupplier.java
```



将此代码部署到 IBM Kubernetes Knative 实例中，如第 2 章中所示。

此代码现在将具有 OpenAPI 定义。这将允许 Watson Assistant 与您的函数进行通信，您可以使用此功能在 Watson Assistant 中配置自定义扩展。

步骤 2：配置 Watson Assistant。

1.  定义聊天机器人的范围：
    1.  示例用例
        1.  告诉用户有哪些 Apple 产品可供选择。

2.  告诉他们库存中有多少台 iMac。

2.  登录 IBM Cloud（[`http://cloud.ibm.com`](http://cloud.ibm.com)）。

![](img/526597_1_En_7_Fig29_HTML.png)

一张 IBM 云仪表板的截图。它包含不同的选项，包括创建仪表板、资源摘要、计划维护和 IBM 云状态。

图 7-29

IBM 云仪表板

1.  注册 Watson Assistant 并创建实例。

您可以通过在 IBM Cloud 门户中搜索或使用此链接[`https://cloud.ibm.com/catalog/services/watson-assistant`](https://cloud.ibm.com/catalog/services/watson-assistant)到达 Watson Assistant 仪表板。

选择您的计划并选择一个位置，如图 7-30 所示。

![](img/526597_1_En_7_Fig30_HTML.jpg)

一张 IBM 云 Watson Assistant 注册页面的截图。它包含不同的选项，包括计划、功能和定价详情。

图 7-30

Watson Assistant 注册页面

点击“创建”按钮以进入下一步骤，个性化您的助手。

1.  创建并个性化您的助手。

选择您希望为助手使用的名称并点击“下一步”。参见图 7-31。

![](img/526597_1_En_7_Fig31_HTML.jpg)

一张创建第一个助手页面的截图。它提供输入助手名称、描述和语言的选项。

图 7-31

创建助手

选择您的选项。您将获得聊天窗口的预览。

您可以选择您偏好的部署类型。此示例使用 Facebook，因为它提供了良好的界面。参见图 7-32。

![](img/526597_1_En_7_Fig32_HTML.jpg)

一张个性化助手创建页面的截图。它提供选择部署助手的实例和其他选项的选项。

图 7-32

使用模板创建助手

填写“告诉我们关于您自己”部分以满足您的需求。参见图 7-33。

![](img/526597_1_En_7_Fig33_HTML.jpg)

一张 IBM Watson Assistant 页面的截图。它提供使用 Facebook 个性化助手和“告诉我们关于您自己”的选项。

图 7-33

带有 Facebook 模板的助手

点击“创建”以进入下一步骤。助手现在应该已成功配置，如图 7-34 所示。

![](img/526597_1_En_7_Fig34_HTML.png)

一张新 Watson 助手主页的截图。它提供开始和创建对话的选项。

图 7-34

助手已成功创建

1.  创建一个操作。

创建一个帮助您与客户互动的操作。您可以通过在页面的“创建对话”部分选择“创建第一个操作”来完成此操作。您可以选择使用模板或从头开始创建。

选择图 7-35 中突出显示的选项以创建您的操作。

![](img/526597_1_En_7_Fig35_HTML.jpg)

一张创建操作的截图。它突出显示了创建操作的文本和步骤。步骤包括设置助手、名称、选择从头开始、选择选项并填写选项 1 和 2。

图 7-35

创建操作的活动流程

继续按照说明完成操作。您可以点击“预览”查看对话流程，并根据需要进行修改。现在您需要将此操作与在步骤 1 中创建的库存函数关联起来，以让机器人搜索库存并作出回应。参见图 7-36。

![](img/526597_1_En_7_Fig36_HTML.jpg)

一张 Watson Assistant 的“问候客户”页面的截图。它提供通过选择可用选项创建操作的 2 步选项。

图 7-36

创建操作

现在您需要使用图 7-37 中屏幕左下角显示的“集成”按钮来创建一个集成。此功能在“主页”按钮中可用。

![](img/526597_1_En_7_Fig37_HTML.jpg)

一张 IBM Watson Assistant 页面的截图。它显示主页、操作、预览、发布、环境和分析的选项。

图 7-37

添加集成以支持调用 Spring Cloud Function

1.  构建自定义扩展。

“集成”按钮将带您进入构建自定义集成的仪表板，如图 7-38 所示。

![](img/526597_1_En_7_Fig38_HTML.png)

一张 IBM Watson Assistant 页面的截图。它显示集成目录，并提供构建自定义扩展的选项。

图 7-38

从集成目录构建自定义扩展

提供您的扩展名称和描述，如图 7-39 所示。

![](img/526597_1_En_7_Fig39_HTML.png)

一张 IBM Watson Assistant 页面的截图。它显示自定义扩展页面，提供输入基本信息的选项。

图 7-39

提供自定义扩展信息

选择您部署的函数。一旦您提供 URL，它将为您提供一些选项选择，如图 7-40 所示。

![](img/526597_1_En_7_Fig40_HTML.jpg)

一张 IBM Watson Assistant 的截图。它显示自定义扩展页面，提供查看扩展包括服务器和操作的选项。

图 7-40

为部署的 Spring Cloud Function 添加 OpenAPI URL

按照图 7-41 所示应用自定义扩展。

![](img/526597_1_En_7_Fig41_HTML.jpg)

一张 IBM Watson Assistant 的截图。它显示扩展设置页面，包含“检查库存”扩展和“库存供应商”操作标签。

图 7-41

应用自定义扩展

自定义扩展现在可供操作使用，如图 7-42 所示。

![](img/526597_1_En_7_Fig42_HTML.png)

一张 IBM Watson Assistant 的截图。它显示完成的自定义扩展页面，并确认步骤 1 已无条件完成。

图 7-42

自定义扩展可供操作使用

现在您可以返回到创建的聊天界面重新测试您的聊天界面。

在 GitHub 上查看[`https://github.com/banup-kubeforce/Watson-imacs.git`](https://github.com/banup-kubeforce/Watson-imacs.git)获取更多信息。在您的环境中部署代码并进行集成。这是一个有趣的练习。



## 7.4 总结

本章探讨了一些实际应用场景，并在 IBM 云上部署了 Spring Cloud Function。

这展示了 Spring Cloud Function 真正的“一次编写，随处部署”能力。

本章还探讨了 IBM 云能提供什么。IBM 云功能多样，多年来已构建和优化了许多产品。您看到了 IBM 云如何在石油和天然气、医疗保健以及零售业等市场中应用。

还有许多其他的应用场景可以使用 Spring Cloud Function，它可以作为微服务架构的替代方案，或者与之共存。在决定采用函数替代微服务架构之前，需要仔细分析成本、可扩展性和性能等因素。



索引 A 添加应用 创建流 数据管道 日志组件 预建模板 属性在 SCDF SpEL  
添加依赖项部分 提前编译（AOT） AI/ML 流程 云函数 云提供商 计算和存储需求 数据管道 部署端点 特征工程 流程  
Google Java 和 Python 语言流行度 生命周期 模型评估 监控需求 Spring Cloud Function Spring 框架 训练模型 AKS 集群 AKS Knative Apache Kafka Apache MXNet Apache.org 应用部署 Application.properties 应用程序利用率 ArgoCD argocd-server GitOps 安装 登录命名空间创建 密码 argocd-server 参数助手创建 AWS 账户 AWS CLI AWS EKS 集群 AWS Glue 组件 Kinesis Spring Cloud Function Studio AWS Greengrass AWS IoT Greengrass Lambda 函数管理控制台 MQTT 消息 本地样本函数 AWS Kinesis AWS Lambda 云提供商 仪表板 测试 部署函数创建函数订阅 IDE PayrollAwsApplication.java pom.xml 文件 运行时设置 测试结果 测试标签 调整 Upload From 按钮 AWS Lambda 无服务器函数 AWS SAM 框架 AWS SDK 依赖项 Azure CLI 管理门户 订阅 Azure AKS 集群 Azure Blob 存储 Azure 容器注册表 Azure 函数账户 CLI 控制台 仪表板 部署 EmployeeConsumerHandler.java IDE 测试运行 订阅测试与输入 Azure IoT 组件 边缘 边缘设备中心 B 大数据 模板 品牌管理 桶 C 字符识别 聊天机器人 CI/CD 流程 ArgoCD GitHub Actions 目标平台 CLI 命令 Cloudant CloudantConsumer.java CloudantConnector.java Cloudant 数据库 Cloudant 页面 Cloud 爆炸 Cloud Foundry 环境 云提供商 云就绪网关 Cloud Run Cloud 存储 集群 clusterconfig.yaml cluster.yaml 命令行工具 常见任务 组件 计算引擎 计算机视觉 概念漂移 消费者 持续交付（CD） 持续集成（CI） 参见 CI/CD 流程 对话式 AI 组件 概念与零售在零售 Watson Assistant 成本效益替代方案 COVID-19 裂缝 创建按钮 创建数据库 创建流 CRUD 操作 Curl 自定义扩展信息 D 数据中心 数据漂移 数据事件管道 获取数据 实现 加载数据 存储/摄入数据 转换数据 Dataflow 数据框 数据完整性 数据湖 数据管道 编码 数据清洗 数据收集 数据标注流程 SCDF 连接 数据集 数据仓库 解耦 深度 Java 库（DJL） 抽象层 依赖项 猫图像层 概率 服务 Spring Cloud Function TensorFlow 深度神经网络 默认域 依赖项 部署 AWS Lambda 到 Azure 函数 GCP Cloud Functions 到 Knative 部署模型 部署 desiredCapacity DevOps djl-serving Docker hub 镜像推送 Dockerfile Dockerhub Docker 推送 域特定语言（DSL） 药物发现 E Eclipse 边缘服务 EKS 集群 EKS 控制台 Eksctl CLI 员工消费者函数 EmployeeConsumerHandler 员工实体模型 员工模型 employeeSupplier 事件流页面 EventStreamsSupplier 函数 EventStreamsSupplier.java 外部 IP F Facebook 模板 特征工程 火箭 Firecracker 集群管理 财富商业 免费集群 免费层级 函数定义 FunctionInvoker 类 函数即服务（FaaS） 代码可移植性问题 组件架构描述 企业应用实现 迁移 ROI 部分 无服务器函数，企业应用 G 气体管道，IoT 实现 Gcloud CLI GCP Cloud Functions 依赖项 函数订阅 GET 函数 获取函数 URL 开始 GitHub GitHub 动作 GitHub 密钥存储 GitOps GKE 集群 GKE 仪表板 Glue Studio Google Google 账户 googleapis Google CLI Google Cloud CLI Google Cloud Dataflow 云发布/订阅实例 云存储 数据管道 生产者函数 pub/sub 模板 vehiclebucket1 Google Cloud Functions Azure Functions 仪表板 Gcloud CLI 执行 PayrollGcpApplication.java pom.xml 文件 订阅 Google Clouds GKE 仪表板 Google Cloud Storage Google Cloud Storage 桶 Google 的库 GraalVMs 图形表示 H HAProxy H2 控制台 医疗保健 Helm 图表 HTTP 方法 中心 Humidity 混合云 超大规模云 I IBM API 连接（APIC） IBM Cloud IBM Cloudant 数据库 IBM 云函数 IBM Cloud Watson IBM 事件流 IBM 事件流配置 IBM Kubernetes IBM Watson Assistant 发现服务 IoT 平台 iMac 库存 图像识别 Ingress 信息 INSERT 语句 整合 IntelliJ 互联网事物（IoT） 集群描述 设备市场 网关实现 定价计划 传感器 Spring Cloud Function Spring 实现 调查 库存消费者.java 库存函数 库存函数.java inventory.java 库存供应商.java iot.connection.string 变量 IoT 消费者.java IoT 边缘设备 IoT 平台 IoT 流程 云架构 Istio J JAR 文件 Java Java 基础 Lambda JDK JDK 8 JDK 11 Jenkins JSON 文件 JSON 对象 JSON 响应 Jupyter 笔记本单元 K Kafka Kinesis 应用属性用于数据依赖项 模型创建 生产者创建 函数实现 运行函数 Knative CLI 组件 crds 部署增长 安装和配置 可移植无服务器容器 提供商操作符 无服务器工作负载 服务 服务 Spring Kourier Kubeconfig kubectl Kubernetes Kubernetes 在 Docker（KIND）中 L 标签原始数据 Lambda 无服务器函数 语言流行度 泄漏 Linux 容器（LXC） LoadBalancer 加载数据 本地机器 M 魔法 DNS main.py 制造成本 制造工厂流程 云市场 主分支 Maven maven 清理命令 Maven 依赖项 Maven 打包 平均上市时间 云基础设施 基础设施节省 接口 非专有 支付应用程序 服务 设置工作流 自己 陷阱 S3 集成 智能穿戴设备 语音识别 SpringApplication Spring 基础微服务 Spring Boot 代码 员工模型创建 架构步骤 测试函数 编写消费者 编写函数 编写供应商 Spring Cloud Data Flow（SCDF） 添加应用 Spring Cloud Function Spring Cloud Function AI/ML AWS Lambda Azure 函数 Azure IoT 边缘 CI/CD 流程 见 CI/CD 流程 云产品 通用任务 配置文件 data.sql 部署 见 部署 部署与 DJL GCP GitHub 动作 目标 Google Cloud Dataflow H2 数据库 人力资源系统 超大规模云关系 IoT 见 互联网事物（IoT） JDK 11 Knative Knative 与 EKS、AWS Knative 与 GKE、GCP Knative 在 Azure AKS 库 OCP 本地 SCDF schema.sql 无服务器提供商 Spring Boot 步骤方法 VMware Tanzu Watson Assistant Spring Cloud Streams 创建和部署 图形表示 样本仪表板 Spring Data Spring 表达式语言（SpEL） Spring 框架 DJL 混合方法 Spring Cloud Function Spring IDE Spring 实现 Spring Initializer Spring Integration Spring Kafka Spring MQTT Spring Web Spring XD SQL Server sslip.io S3 存储 步骤方法 存储桶 监督学习活动 S3upload.java 供应商函数 SYNC 按钮 T Tableau 标签 VMware Tanzu Kubernetes 网格（TKG） 目标数据存储 目标文件夹 目标平台 温度 温度波动 TensorFlow AWS Lambda DJL 测试事件部分 测试图像 TFLite 第三方网关 TKG 下载 总成本（TCO） 跟踪详细信息类 训练模型 转换数据 Twitter U Ubuntu 虚拟机 未认证设备 V 值方程 方差 车辆数据管道 车辆数据流 虚拟机 Virtual Box VMware VMware Tanzu VS Code VS Studio Code W Watson Assistant 仪表板 注册页面与 Spring Cloud Function Webhooks Watson IoT 上下文 Watson 语音 Webhooks Windows 子系统 Linux（WSL） 工作流代码 工作流点 包装发送者 X XGBoost 机器学习算法 XRAYFunction.java Y, Z YAML 执行 YAML 文件
