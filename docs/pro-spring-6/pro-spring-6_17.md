# logging config
logging:
  pattern:
    console: "%-5level: %class{0} - %msg%n"
  level:
    root: INFO
    org.springframework: DEBUG
    com.apress.prospring6.thirteen: INFO
清单 13-23
使用 Apache Kafka 的 Spring Boot 应用程序配置
```

YAML 配置已按作用域划分为多个部分，各部分说明如下：

*   `# web config`：此部分配置 Web 应用程序的详细信息，例如端口，以及应用程序是否在所有网络 IP 上公开（`0.0.0.0` 表示应用程序可通过 `http://localhost:8090`、`http://127.0.0.1:8090` 等地址访问）。

*   `# kafka config`：此部分配置 Apache Kafka 集群可用的位置和端口。`consumer.group-id` 是一个 Kafka 抽象概念，用于支持点对点和发布-订阅消息传递。此属性可用于对多个消费者进行分组，并自定义每个组的行为，例如消费消息时的优先级、并行度等。

*   `# custom config`：此部分配置发送消息的主题（`app.topic.sending.name`）和接收消息的主题（`app.topic.receiving.name`）。`app.topic.receiving.name` 属性还用于标识应用程序：Tom 或 Evelyn。

*   `# logging config`：此部分配置日志级别。

其次，我们需要一个 Java 类型来建模发送到主题的信息。在两个主题上，将写入 `Letter` 实例。记录声明如清单 13-24 所示。

```
package com.apress.prospring6.thirteen;
import com.fasterxml.jackson.annotation.JsonFormat;
import com.fasterxml.jackson.annotation.JsonProperty;
import java.time.LocalDate;
public record Letter (@JsonProperty("title")  String title,
                      @JsonProperty("sender")  String sender,
                      @JsonFormat(shape = JsonFormat.Shape.STRING, pattern = "yyyy-MM-dd")
                      @JsonProperty("sentOn") LocalDate sentOn,
                      @JsonProperty("content") String content ) {}
清单 13-24
Letter 记录声明
```

由于我们不打算编辑接收到的 `Letter` 实例，因此 Java 记录非常适合此应用程序。

Apache Kafka 集群不知道我们需要哪些主题，因此我们必须配置它们。这是我们需要做的第三件事：一个用于 Kafka 的配置类。它不知道我们计划从主题生产和消费哪种类型的对象，因此我们也必须配置这一点。为简单起见，我们将所有这些配置分组到一个名为 `KafkaConfig` 的类中，如清单 13-25 所示。



```
package com.apress.prospring6.thirteen;
import org.apache.kafka.clients.admin.NewTopic;
import org.apache.kafka.clients.consumer.ConsumerConfig;
import org.apache.kafka.clients.producer.ProducerConfig;
import org.apache.kafka.common.serialization.StringDeserializer;
import org.apache.kafka.common.serialization.StringSerializer;
import org.springframework.boot.autoconfigure.kafka.KafkaProperties;
// 部分导入语句已省略
@SuppressWarnings({"unchecked", "rawtypes"})
@Configuration
@RequiredArgsConstructor
public class KafkaConfig {
private final KafkaProperties kafkaProperties;
@Value(value = "${spring.kafka.bootstrap-servers}")
private String bootstrapAddress;
@Value(value = "${spring.kafka.consumer.group-id}")
private String groupId;
@Value("#{kafkaApplication.sendingTopic}")
private String sendingTopicName;
@Value("#{kafkaApplication.receivingTopic}")
private String receivingTopicName;
@Bean // LetterSender 的配置
public Map producerConfigs() {
Map props = new HashMap(kafkaProperties.buildProducerProperties());
props.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, StringSerializer.class);
props.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, JsonSerializer.class);
return props;
}
@Bean
public ProducerFactory producerFactory() {
return new DefaultKafkaProducerFactory(producerConfigs());
}
@Bean
public KafkaTemplate kafkaTemplate() {
return new KafkaTemplate(producerFactory());
}
@Bean // 发送信件所用的主题
public NewTopic sendingTopic() {
return new NewTopic(sendingTopicName, 1, (short) 1);
}
@Bean  // 读取信件所用的主题
public NewTopic receivingTopic() {
return new NewTopic(receivingTopicName, 1, (short) 1);
}
@Bean // LetterReader 的配置
public ConsumerFactory consumerFactory() {
final org.springframework.kafka.support.serializer.JsonDeserializer jsonDeserializer = new JsonDeserializer();
jsonDeserializer.addTrustedPackages("*");
return new DefaultKafkaConsumerFactory(kafkaProperties.buildConsumerProperties(), new StringDeserializer(), jsonDeserializer);
}
@Bean
public ConcurrentKafkaListenerContainerFactory kafkaListenerContainerFactory() {
ConcurrentKafkaListenerContainerFactory factory = new ConcurrentKafkaListenerContainerFactory();
factory.setConsumerFactory(consumerFactory());
return factory;
}
}
清单 13-25
KafkaConfig 配置类
```

在 Spring Boot 应用程序中，所有来自配置文件的 Kafka 专属属性都会被加载到一个 `KafkaProperties` 配置 Bean 中。该 Bean 被注入到 `KafkaConfig` 类中，因此可以针对 `ProducerFactory<String,Object>` 或 `ConsumerFactory<String, Object>` 的特定属性进行扩展——在本例中，扩展的是序列化器和反序列化器，它们用于在将 `Letter` 实例写入主题之前将其转换为 JSON 表示形式，并在消费消息时执行反向转换。请注意，`Letter` 类型并未在任何地方提及。`ProducerFactory` 只需要知道用什么来转换消息键和消息本身，因为每条消息都必须通过唯一的键进行唯一标识。

在应用程序启动之前，Apache Kafka 集群中没有任何已定义的主题。我们为所需的每个主题声明一个 `NewTopic` Bean，这可以确保如果配置名称的主题不存在，则会创建一个。主题可以配置为划分为多个分区，并在多个代理之间进行复制。在我们这个非常简单的示例中，分区数和复制因子都设置为 1。

`KafkaTemplate` 类与 `JmsTemplate`（以及 `RestTemplate`）类似，需要这种类型的 Bean 来执行 Apache Kafka 的高级操作。该 Bean 是线程安全的，并且为了生成消息，会使用配置好的 `ProducerFactory<K, V>` Bean。

消费主题中的消息需要 `ConsumerFactory<K, V>`。对于这个简单的场景，我们使用 Spring 的 `DefaultKafkaConsumerFactory<K,V>` 实现，并采用最小化配置。

要消费消息，需要由监听器来拾取它们。要创建这样的监听器，除了在消费方法上添加 `@KafkaListener` 注解之外，还需要一个 `ConcurrentKafkaListenerContainerFactory<K,V>`。

![](img/315511_6_En_13_Figf_HTML.jpg)一个圆形渐变色感叹号图标示意图。 生产和消费消息的 Bean 之所以都不限制为特定消息类型，是因为同一队列上可以写入多种类型的消息，并由不同的监听器读取，这些监听器由不同的 `ConcurrentKafkaListenerContainerFactory<K,V>` 创建。

请注意，Kafka 属性是从 Spring Boot 配置文件中提取的，并通过 `@Value(value = "${spring.kafka.*}")` 注入，但像 `app.topic.sending.name` 这样的自定义属性需要首先声明为 Spring Boot 主配置类的属性，并使用 SpEL 进行注入。

此配置中的 Bean 必须注入到用于生产和消费 `Letter` 实例的 Bean 中。用于建模消息生产者的类名为 `LetterSender`，因为它的职责是发送信件。该类如清单 13-26 所示。

```
package com.apress.prospring6.thirteen;
import lombok.RequiredArgsConstructor;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.kafka.core.KafkaTemplate;
import org.springframework.stereotype.Service;
@RequiredArgsConstructor
@Service
@Slf4j
public class LetterSender {
@Value("#{kafkaApplication.sendingTopic}")
public String sendingToTopicName;
@Value("#{kafkaApplication.receivingTopic}")
private String sender; // 谁在发送信件
private final KafkaTemplate kafkaTemplate;
// 使应用程序可配置
public void send(Letter letter) {
log.info(">>>> -> [{}] 发送信件 -> {}", sender, letter);
kafkaTemplate.send(sendingToTopicName, letter);
}
}
清单 13-26
LetterSender 类与 Bean 配置
```

`LetterSender` Bean 需要 `KafkaTemplate` Bean 来发送消息，以及消息发送到的主题名称。`send(..)` 方法有多个版本，包含用于选择分区和消息生成时间的参数。还有一个版本返回 `CompletableFuture<SendResult<K, V>>`，允许声明在消息成功发送后执行的回调。

`kafkaApplication.receivingTopic` 也是发送消息的应用程序的名称，为了日志记录目的而注入到该 Bean 中。

`LetterSender` Bean 被注入到 `KafkaController` 中，因此可以通过 `POST` 请求触发发送信件。`KafkaController` 类和 Bean 配置如清单 13-27 所示。

```
package com.apress.prospring6.thirteen;
import lombok.RequiredArgsConstructor;
import org.springframework.web.bind.annotation.*;
@RequiredArgsConstructor
@RestController
@RequestMapping(path = "/kafka")
public class KafkaController {
private final LetterSender sender;
@PostMapping(value = "/send")
public void sendMessageToKafkaTopic(@RequestBody Letter letter) {
this.sender.send(letter);
}
}
清单 13-27
KafkaController 类与 Bean 配置
```

现在我们已经知道如何将 `Letter` 实例发送到主题，接下来看看如何消费它们。我们之前提到过 `@KafkaListener`，但应该把它放在哪里呢？答案是放在一个名为 `LetterReader` 的类的方法上，如清单 13-28 所示。



```
package com.apress.prospring6.thirteen;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.kafka.annotation.KafkaListener;
import org.springframework.messaging.handler.annotation.Payload;
import org.springframework.stereotype.Service;
@Service
@Slf4j
public class LetterReader {
@Value("#{kafkaApplication.receivingTopic}")
private String receivingTopicName;
@KafkaListener(topics = "#{kafkaApplication.receivingTopic}",
groupId = "${spring.kafka.consumer.group-id}",
containerFactory = "kafkaListenerContainerFactory")
public void consume(@Payload Letter letter) {
log.info(" {}", receivingTopicName, letter);
}
}
清单 13-28
LetterReader 类与 Bean 配置
```

`@KafkaListener` 注解将方法标记为 Kafka 消息监听器的目标。监听器需要知道要从中读取消息的主题（是的，它可以从多个主题读取消息），这通过 `topics` 属性进行配置。如果使用了多个组，并且我们希望监听器仅从单个主题组读取消息，那么 `groupId` 属性对此很有用。

此外，由于我们希望确保消息被正确转换，我们需要确保创建合适的监听器，这意味着要通过 `containerFactory` 方法指定要使用的合适的 `ConcurrentKafkaListenerContainerFactory<K,V>`。

`consume(..`​`)` 方法可以有多种签名，只要 Spring 知道从主题消费消息后如何处理它即可。来自 `org.springframework.messaging.handler.annotation` 包的 `@Payload` 注解将 Kafka 消息的主体绑定到此方法参数，并将其转换为适当的类型，在本例中为 `Letter`。

最后一个要展示的类是 `KafkaApplication` 类，即主要的 Spring Boot 配置类和运行器，如清单 13-29 所示。

```
package com.apress.prospring6.thirteen;
@SpringBootApplication
@Slf4j
public class KafkaApplication {
public static void main(String... args) {
SpringApplication.run(KafkaApplication.class, args);
}
@Value("${app.sending.topic.name}")
public String sendingTopic;
@Value("${app.receiving.topic.name}")
public String receivingTopic;
@Bean
public CommandLineRunner initCmd(){
return (args) -> log.info(" >>> 发送者 {}  准备向 {} 发送信件 ", receivingTopic, sendingTopic);
}
}
清单 13-29
KafkaApplication Spring Boot 类
```

由于此应用程序将启动两次，一次为 Evelyn，一次为 Tom，因此创建了一个 `CommandLineRunner` Bean 来显示哪个应用程序正在运行。接收 `Letters` 的主题名称也是应用程序的名称。如前文图 13-9 所示，Evelyn 的应用程序在端口 8080 上启动，Tom 的应用程序在端口 8090 上启动。

要启动这两个应用程序实例，可以配置不同的 IntelliJ IDEA 启动器，或者构建应用程序并使用 JAR 在不同的终端窗口中启动两个实例，使用清单 13-30 中所示的命令。

```
