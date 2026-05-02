# 24. 单元测试

单元测试提供快速反馈，但只测试代码中一个隔离的单元（例如，类中的一个方法）。在聊天应用程序中，我使用 JUnit¹ 以及 Mockito² 和 Hamcrest³ 来编写单元测试。



## 24.1 InstantMessageBuilderTest.java

`InstantMessageBuilderTest.java` 类用于测试 `InstantMessageBuilder` 类，该类实现了建造者模式⁴，提供了一系列方法链，用于简化创建新 `InstantMessage` 对象的过程。

```
public class InstantMessageBuilderTest {
private final String chatRoomId = "123";
private final String fromUser = "jorge_acetozi";
private final String toUser = "michael_romeo";
private final String publicMessageText = "Hello guys... I hope you are enjoying my eBook!";
private final String privateMessageText = "I'm listening to Symphony X right now!";
private final String systemMessageText = "This is a system message from admin user!";
@Test
public void shouldCreatePublicInstantMessage() {
InstantMessage publicMessage = new InstantMessageBuilder()
.newMessage()
.withChatRoomId(chatRoomId)
.publicMessage()
.fromUser(fromUser)
.withText(publicMessageText);
assertThat(publicMessage.isPublic(), is(true));
assertThat(publicMessage.isFromAdmin(), is(false));
assertThat(publicMessage.getChatRoomId(), is(chatRoomId));
assertThat(publicMessage.getFromUser(), is(fromUser));
assertThat(publicMessage.getToUser(), is(nullValue()));
assertThat(publicMessage.getText(), is(publicMessageText));
}
@Test
public void shouldCreatePrivateInstantMessage() {
InstantMessage privateMessage = new InstantMessageBuilder()
.newMessage()
.withChatRoomId(chatRoomId)
.privateMessage()
.toUser(toUser)
.fromUser(fromUser).
withText(privateMessageText);
assertThat(privateMessage.isPublic(), is(false));
assertThat(privateMessage.isFromAdmin(), is(false));
assertThat(privateMessage.getChatRoomId(), is(chatRoomId));
assertThat(privateMessage.getFromUser(), is(fromUser));
assertThat(privateMessage.getToUser(), is(toUser));
assertThat(privateMessage.getText(), is(privateMessageText));
}
@Test
public void shouldCreateSystemInstantMessage() {
InstantMessage systemMessage = new InstantMessageBuilder()
.newMessage()
.withChatRoomId(chatRoomId)
.systemMessage()
.withText(systemMessageText);
assertThat(systemMessage.isPublic(), is(true));
assertThat(systemMessage.isFromAdmin(), is(true));
assertThat(systemMessage.getChatRoomId(), is(chatRoomId));
assertThat(systemMessage.getFromUser(), is(SystemUsers.ADMIN.getUsername()));
assertThat(systemMessage.getToUser(), is(nullValue()));
assertThat(systemMessage.getText(),  is(systemMessageText));
}
}
```

使用 Hamcrest 匹配器后，测试代码变得非常易读。例如，在 `shouldCreatePublicInstantMessage` 中，你使用 `InstantMessageBuilder` 构建了一条公共消息。当 `toUser` 字段为 `null` 时，该消息被视为公共消息。然而，在 `shouldCreatePrivateInstantMessage` 中，`toUser` 字段不能为 `null`。

在 `shouldCreateSystemInstantMessage` 中，你验证了每条系统消息都是公开的，并且来自用户名为 admin 的用户。

## 24.2 DestinationsTest.java

`DestinationsTest.java` 类用于测试 `Destinations` 类，该类提供了一些静态方法，用于返回订阅聊天室中公共消息、私有消息或已连接用户的目标名称。这些测试很重要，因为如果有人（即使是意外地）更改了 `Destination` 类，所有聊天室功能都将停止工作。

```
public class DestinationsTest {
private final String chatRoomId = "123";
@Test
public void shouldGetPublicMessagesDestination() {
assertThat(Destinations.ChatRoom.publicMessages("123"), is("/topic/" + chatRoomId + ".public.messages"));
}
@Test
public void shouldGetPrivateMessagesDestination() {
assertThat(Destinations.ChatRoom.privateMessages("123"), is("/queue/" + chatRoomId + ".private.messages"));
}
@Test
public void shouldGetConnectedUsersDestination() {
assertThat(Destinations.ChatRoom.connectedUsers("123"), is("/topic/" + chatRoomId + ".connected.users"));
}
}
```

基本上，这个类仅测试对于给定的聊天室 ID（等于 123），其目标名称是否分别为 `/topic/123.public.messages`、`/queue/123.private.messages` 和 `/topic/123.connected.users`。

## 24.3 RedisChatRoomServiceTest.java

`RedisChatRoomServiceTest.java` 类用于测试 `RedisChatRoomService` 类，该类提供了聊天室操作的方法。

```
@RunWith(MockitoJUnitRunner.class)
public class RedisChatRoomServiceTest {
@InjectMocks private ChatRoomService chatRoomService = new RedisChatRoomService();
@Mock private SimpMessagingTemplate webSocketMessagingTemplate;
@Mock private InstantMessageService instantMessageService;
@Captor private ArgumentCaptor destinationCaptor;
@Captor private ArgumentCaptor instantMessageCaptor;
@Captor private ArgumentCaptor messageObjectCaptor;
@Test
public void shouldSendPublicMessage() {
ChatRoom chatRoom = new ChatRoom("123", "Dream Theater", "Discuss about best band ever!");
ChatRoomUser user = new ChatRoomUser("jorge_acetozi");
chatRoom.addUser(user);
assertThat(chatRoom.getNumberOfConnectedUsers(), is(1));
InstantMessage publicMessage = new InstantMessageBuilder()
.newMessage()
.withChatRoomId(chatRoom.getId())
.publicMessage()
.fromUser(user.getUsername())
.withText("This is a public message!");
chatRoomService.sendPublicMessage(publicMessage);
verify(webSocketMessagingTemplate, times(1))
.convertAndSend(
destinationCaptor.capture(),
messageObjectCaptor.capture());
verify(instantMessageService, times(1))
.appendInstantMessageToConversations(instantMessageCaptor.capture());
String sentDestination = destinationCaptor.getValue();
InstantMessage sentMessage = (InstantMessage) messageObjectCaptor.getValue();
InstantMessage instantMessageToBeAppendedToConversations = instantMessageCaptor.getValue();
assertThat(sentDestination, is(Destinations.ChatRoom.publicMessages(chatRoom.getId())));
assertEquals(publicMessage, sentMessage);
assertEquals(publicMessage, instantMessageToBeAppendedToConversations);
}
}
```

测试方法 `shouldSendPublicMessage` 首先创建一个新的聊天室，并向其中添加一个新用户（用户名为 jorge_acetozi）；然后创建一条将由 jorge_acetozi 发送的公共消息，并调用 `chatRoomService` 方法中的 `sendPublicMessage` 方法，而该方法正是你真正想要测试的方法。之后，它验证了 `webSocketMessagingTemplate` 和 `instantMessageService` 仅被调用了一次，并且发送的目标是“公共目标”。它还检查了发送的消息与之前传递给 `sendPublicMessage` 方法的消息相同，并且该公共消息与传递给 `appendInstantMessageToConversations` 方法调用的消息相同。

脚注 1

[`http://junit.org/junit4/`](http://junit.org/junit4/)

  2

[`http://site.mockito.org/`](http://site.mockito.org/)

  3

[`http://hamcrest.org/`](http://hamcrest.org/)

  4

[`https://en.wikipedia.org/wiki/Builder_pattern`](https://en.wikipedia.org/wiki/Builder_pattern)



