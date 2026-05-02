# 25. 集成测试

集成测试的编码不像单元测试那样直接，因为它们确实需要测试完整的集成。例如，要测试数据库集成，你需要在运行测试之前设置好这个数据库。

为了解决这个问题，并避免仅仅为了执行一个简单测试而设置整个数据库的开销，许多人使用诸如 H2 之类的内存数据库来运行集成测试。¹

关键在于，集成测试在类似于生产环境的环境中运行时更有效。例如，内存数据库并不具备 MySQL 的所有功能。如果你没有运行 MySQL 实例，某些特定的 MySQL 函数将无法被测试，对吧？

因此，为了使环境尽可能接近生产环境，你可以使用一个名为 testcontainers² 的便捷库，它允许你在 JUnit 测试内部创建 Docker 容器。这非常好，因为测试将针对真实的 Cassandra、MySQL、Redis 和 RabbitMQ 的易失性实例运行。

## 25.1 设置从 JUnit 启动 Docker 容器的依赖项

要使用 testcontainers，首先你需要在 `pom.xml` 中声明依赖。

```
org.testcontainers
testcontainers
1.1.9

```

以下代码在运行集成测试之前执行。基本上，它使用 testcontainers 库为 Cassandra、MySQL、Redis 和 RabbitMQ（支持 STOMP）设置 Docker 容器。请注意，`waitForMysqlContainerStartup` 方法确保只有在 MySQL 容器准备好接收连接后，才会执行集成测试。

```
public class AbstractIntegrationTest {
@ClassRule
public static final GenericContainer cassandra = new FixedHostPortGenericContainer("cassandra:3.0")
.withFixedExposedPort(9042, 9042);
@ClassRule
public static final GenericContainer mysql = new FixedHostPortGenericContainer("mysql:5.7")
.withFixedExposedPort(3306, 3306)
.withEnv("MYSQL_DATABASE",  "ebook_chat")
.withEnv("MYSQL_ROOT_PASSWORD",  "root");
@ClassRule
public static final GenericContainer redis = new FixedHostPortGenericContainer("redis:3.0.6")
.withFixedExposedPort(6379, 6379);
@ClassRule
public static final GenericContainer rabbitmq = new FixedHostPortGenericContainer("jorgeacetozi/rabbitmq-stomp:3.6")
.withFixedExposedPort(61613,  61613)
.withExposedPorts(61613);
@BeforeClass
public static void waitForMysqlContainerStartup() throws InterruptedException, TimeoutException {
WaitingConsumer consumer = new WaitingConsumer();
mysql.followOutput(consumer);
consumer.waitUntil(frame ->
frame.getUtf8String().contains("mysqld: ready for connections."), 90, TimeUnit.SECONDS);
}
}
```

## 25.2 JUnit 测试套件

在每次测试之前设置 Cassandra、MySQL、Redis 和 RabbitMQ 会产生大量开销并且非常耗时。为了只实例化这些依赖项一次，并将其用于所有执行的集成测试，你可以使用 [JUnit](https://github.com/junit-team/junit4/wiki/aggregating-tests-in-suites) 测试套件。³ 基本上，JUnit 测试套件用于聚合测试。在聊天应用程序中，有两个测试套件：`UnitTestsSuite.java` 和 `IntegrationTestsSuite.java`。它们分别聚合了所有的单元测试和集成测试。

```
@RunWith(Suite.class)
@Suite.SuiteClasses({
InstantMessageBuilderTest.class,
DestinationsTest.class,
SystemMessagesTest.class,
RedisChatRoomServiceTest.class
})
public class UnitTestsSuite {
}
@RunWith(Suite.class)
@Suite.SuiteClasses({
CassandraInstantMessageServiceTest.class,
RedisChatRoomServiceTest.class,
DefaultUserServiceTest.class,
AuthenticationControllerTest.class,
ChatRoomControllerTest.class
})
public class IntegrationTestsSuite extends AbstractIntegrationTest {
}
```

这两个 JUnit 测试套件将帮助你分离单元测试和集成测试，以便你可以在不同的 Maven 阶段运行它们。⁴

## 25.3 RedisChatRoomServiceTest.java

`RedisChatRoomServiceTest.java` 类测试了 `RedisChatRoomService` 类，该类提供了聊天室操作的方法。

```
@RunWith(SpringRunner.class)
@EbookChatTest
public class RedisChatRoomServiceTest {
@Autowired private ChatRoomService chatRoomService;
@Autowired private ChatRoomRepository chatRoomRepository;
@Autowired private InstantMessageRepository instantMessageRepository;
private ChatRoom chatRoom;
@Before
public void setup() {
chatRoom = new ChatRoom("123", "Dream Theater", "Discuss about best band ever!");
chatRoomService.save(chatRoom);
}
@After
public void destroy() {
chatRoomRepository.delete(chatRoom);
instantMessageRepository.deleteAll();
}
@Test
public void shouldJoinUsersToChatRoom() {
assertThat(chatRoom.getNumberOfConnectedUsers(),  is(0));
ChatRoomUser jorgeAcetozi = new ChatRoomUser("jorge_acetozi");
ChatRoomUser johnPetrucci = new ChatRoomUser("john_petrucci");
chatRoomService.join(jorgeAcetozi,  chatRoom);
assertThat(chatRoom.getNumberOfConnectedUsers(),  is(1));
chatRoomService.join(johnPetrucci,  chatRoom);
assertThat(chatRoom.getNumberOfConnectedUsers(),  is(2));
ChatRoom dreamTheaterChatRoom = chatRoomService.findById(chatRoom.getId());
List connectedUsers = dreamTheaterChatRoom.getConnectedUsers();
assertThat(connectedUsers.contains(jorgeAcetozi), is(true));
assertThat(connectedUsers.contains(johnPetrucci), is(true));
}
@Test
public void shouldLeaveUsersFromChatRoom() {
ChatRoomUser jorgeAcetozi = new ChatRoomUser("jorge_acetozi");
ChatRoomUser johnPetrucci = new ChatRoomUser("john_petrucci");
chatRoomService.join(jorgeAcetozi, chatRoom);
chatRoomService.join(johnPetrucci, chatRoom);
assertThat(chatRoom.getNumberOfConnectedUsers(), is(2));
chatRoomService.leave(jorgeAcetozi, chatRoom);
chatRoomService.leave(johnPetrucci, chatRoom);
assertThat(chatRoom.getNumberOfConnectedUsers(), is(0));
}
}
```

基本上，`shouldJoinUsersToChatRoom` 创建了两个用户，jorgeAcetozi 和 johnPetrucci；它将 jorgeAcetozi 加入聊天室；并验证聊天室现在有一个已连接的用户。之后，它将 johnPetrucci 加入，并验证聊天室现在有两个已连接的用户。这些 `join` 调用中的每一个实际上都会访问 Redis 实例。然后，它从 Redis 中获取聊天室，并验证 jorgeAcetozi 和 johnPetrucci 都已连接到该聊天室。

`shouldLeaveUsersFromChatRoom` 类具有非常相似的逻辑。



## 25.4 ChatRoomControllerTest.java

`ChatRoomControllerTest.java` 类用于测试 `ChatRoomController` 类，该类提供了创建新聊天室的 REST 端点。基本上，在这两个测试中，你需要确保没有 `ROLE_ADMIN` 角色的用户无法创建聊天室。

```
@RunWith(SpringRunner.class)
@EbookChatTest
@WebAppConfiguration
public class ChatRoomControllerTest {
@Autowired
private WebApplicationContext wac;
@Autowired
private FilterChainProxy springSecurityFilter;
private MockMvc mockMvc;
@Before
public void setup() {
this.mockMvc = MockMvcBuilders
.webAppContextSetup(this.wac)
.addFilter(springSecurityFilter)
.build();
}
@Test
public void shouldCreateChatRoomWhenUserHasRoleAdmin() throws Exception {
ChatRoom chatRoom = new ChatRoom("123",
"Dream Theater",
"Discuss about best band ever!");
this.mockMvc.perform(
post("/chatroom")
.with(user("admin").roles("ADMIN"))
.contentType(MediaType.APPLICATION_JSON)
.content(new ObjectMapper().writeValueAsString(chatRoom))
)
.andDo(print())
.andExpect(status().isCreated())
.andExpect(jsonPath("$.id",is(chatRoom.getId())))
.andExpect(jsonPath("$.name",  is(chatRoom.getName())))
.andExpect(jsonPath("$.description", is(chatRoom.getDescription())));
}
@Test
public void shouldNotCreateChatRoomWhenUserDoesntHaveRoleAdmin() throws Exception {
ChatRoom chatRoom = new ChatRoom("123", "Dream Theater", "Discuss about best band ever!");
this.mockMvc.perform(
post("/chatroom")
.with(user("xuxa").roles("USER"))
.contentType(MediaType.APPLICATION_JSON)
.content(new ObjectMapper().writeValueAsString(chatRoom))
)
.andDo(print())
.andExpect(status().isForbidden());
}
}
```

在 `shouldCreateChatRoomWhenUserHasRoleAdmin` 测试中，使用管理员用户向 `/chatroom` 发送 POST 请求，并断言响应状态码为 `201 CREATED`，且 HTTP 响应体包含包含新聊天室的 JSON。

在 `shouldNotCreateChatRoomWhenUserDoesntHaveRoleAdmin` 测试中，使用具有 `ROLE_USER` 角色的用户向 `/chatroom` 发送 POST 请求，并断言响应状态码为 `403 FORBIDDEN`。

脚注 1

[`www.h2database.com/`](http://www.h2database.com/)

  2

[`https://www.testcontainers.org/`](https://www.testcontainers.org/)

  3

[`https://github.com/junit-team/junit4/wiki/aggregating-tests-in-suites`](https://github.com/junit-team/junit4/wiki/aggregating-tests-in-suites)

  4

[`https://maven.apache.org/guides/introduction/introduction-to-the-lifecycle.html`](https://maven.apache.org/guides/introduction/introduction-to-the-lifecycle.html)

