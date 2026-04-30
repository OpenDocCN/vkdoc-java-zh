# 其他 JSON 元素已省略
]
清单 15-7
使用 curl 以 JSON 格式获取所有歌手的请求及响应
```

此命令向服务器的 RESTful Web 服务发送一个 HTTP 请求，同样调用 `SingerController` 中的 `all()` 方法来检索并返回所有歌手信息。在此例中，`-H` 选项声明了一个 HTTP 头部属性，表示客户端希望以 JSON 格式接收数据。

HTTPie 和 `curl` 都发出了相同的请求；唯一不同的是 `Accept` 头部的值。更改数据格式之所以有效，是因为 Spring MVC 会注册在类路径上找到的 `org.springframework.http.converter.HttpMessageConverter<T>` 实现，并使用它们进行内容类型解析。

对于模拟非响应式 Spring REST 应用之间的通信，会使用 `RestTemplate` 实例。**第** **13** **章**介绍了使用 `RestTemplate` 来测试 Spring Boot 应用，并且这些测试被设计为在 Spring Boot 测试上下文中运行。在本节中，应用以经典方式配置，没有使用 Spring Boot，并打包为部署在 Apache Tomcat 服务器上的 `*.war` 文件。这意味着为此应用编写的测试与应用上下文是分离的。应用在 Tomcat 中运行，而测试类仅与应用共享类路径，不共享上下文，因此该测试相当于应用的一个客户端。

清单 15-8 展示了 `RestClientTest` 类，它使用 `RestTemplate` 实例向部署在 Tomcat 上的应用提交所有类型的 HTTP 请求。

```
package com.apress.prospring6.fifteen;
import org.springframework.web.client.RestTemplate;
// 其他 import 语句已省略
public class RestClientTest {
final Logger LOGGER = LoggerFactory.getLogger(RestClientTest.class);
private static final String URI_SINGER_ROOT = "http://localhost:8080/ch15/singer/";
private static final String URI_SINGER_WITH_ID = "http://localhost:8080/ch15/singer/{id}";
RestTemplate restTemplate = new RestTemplate();
@Test
public void testFindAll() {
LOGGER.info("--> 测试检索所有歌手");
var singers = restTemplate.getForObject(URI_SINGER_ROOT, Singer[].class);
assertEquals(15, singers.length);
Arrays.stream(singers).forEach(s -> LOGGER.info(s.toString()));
}
@Test
public void testFindById() {
LOGGER.info("--> 测试按 ID 检索歌手 : 1");
var singer = restTemplate.getForObject(URI_SINGER_WITH_ID, Singer.class, 1);
assertNotNull(singer);
LOGGER.info(singer.toString());
}
@Test
public void testCreate() {
LOGGER.info("--> 测试创建歌手");
Singer singerNew = new Singer();
singerNew.setFirstName("TEST");
singerNew.setLastName("Singer");
singerNew.setBirthDate(LocalDate.now());
singerNew = restTemplate.postForObject(URI_SINGER_ROOT, singerNew, Singer.class);
LOGGER.info("歌手创建成功: " + singerNew);
}
@Test
public void testDelete() {
LOGGER.info("--> 测试按 ID 删除歌手 : 33");
var initialCount = restTemplate.getForObject(URI_SINGER_ROOT, Singer[].class).length;
restTemplate.delete(URI_SINGER_WITH_ID, 33);
var afterDeleteCount = restTemplate.getForObject(URI_SINGER_ROOT, Singer[].class).length;
assertEquals((initialCount - afterDeleteCount), 1);
}
@Test
public void testUpdate() {
LOGGER.info("--> 测试按 ID 更新歌手 : 1");
var singer = restTemplate.getForObject(URI_SINGER_ROOT, Singer.class, 1);
singer.setFirstName("John Marvelous");
restTemplate.put(URI_SINGER_ROOT, singer, 1);
LOGGER.info("歌手更新成功: " + singer);
}
}
清单 15-8
RestClientTest 类使用 RestTemplate 向 ch15 Web 应用发起请求
```

`RestClientTest` 类包含用于测试 Web 应用支持的所有 URL 的方法。每个方法都可以在诸如 IntelliJ IDEA 这样的智能编辑器中单独运行。声明了用于访问各种操作的 URL，这些 URL 将在后续示例中使用。`RestTemplate` 实例被创建并用于所有测试方法。

在 `testFindAll()` 方法中，调用了 `RestTemplate#getForObject(..)` 方法（对应于 HTTP GET 方法），传入了 URL 和期望的返回类型，即包含完整歌手列表的 `Singers[]` 类。

确保应用服务器正在运行，并且 Web 应用已部署在上下文路径 `ch15` 下。运行 `testFindAll()` 测试方法，测试应能通过并产生如清单 15-9 所示的输出。

```
11:33:06.970 [Test worker] INFO  c.a.p.f.RestClientTest - --> 测试检索所有歌手
11:33:07.002 [Test worker] DEBUG o.s.w.c.RestTemplate - HTTP GET http://localhost:8080/ch15/singer/
11:33:07.018 [Test worker] DEBUG o.s.w.c.RestTemplate - Accept=[application/xml, text/xml, application/json, application/*+xml, application/*+json]
11:33:07.090 [Test worker] DEBUG o.s.w.c.RestTemplate - Response 200 OK
11:33:07.096 [Test worker] DEBUG o.s.w.c.RestTemplate - Reading to [com.apress.prospring6.fifteen.entities.Singer[]]
11:33:07.179 [Test worker] INFO  c.a.p.f.RestClientTest - Singer(id=null, version=0, firstName=John, lastName=Mayer, birthDate=1977-10-16)
11:33:07.179 [Test worker] INFO  c.a.p.f.RestClientTest - Singer(id=null, version=0, firstName=Ben, lastName=Barnes, birthDate=1981-08-20)
...
