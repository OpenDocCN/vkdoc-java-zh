# 20. 通过 WebSocket 发送用户的私密消息

发送私密消息可能比发送公开消息稍微复杂一些。让我们看看下面展示的 JavaScript 代码：

```
function sendMessage() {
var instantMessage;
if (spanSendTo.text() == "public") {
instantMessage = {
'text' : inputMessage.val()
}
} else {
instantMessage = {
'text' : inputMessage.val(),
'toUser' : spanSendTo.text()
}
}
stompClient.send("/chatroom/send.message", {}, JSON.stringify(instantMessage));
inputMessage.val("").focus();
}
```

当客户端在聊天室中发送私密消息时，会调用 `sendMessage` 这个 JavaScript 函数。本质上，它将 `instantMessage` JavaScript 对象转换为 JSON 表示形式，并将消息发送到应用目标地址 `/chatroom/send.message`，这与用于发送用户公开消息的目标地址相同。

```
@Controller
public class ChatRoomController {
@Autowired
private ChatRoomService chatRoomService;
@MessageMapping("/send.message")
public void sendMessage(@Payload InstantMessage instantMessage, Principal principal,
SimpMessageHeaderAccessor headerAccessor) {
String chatRoomId = headerAccessor.getSessionAttributes().get("chatRoomId").toString();
instantMessage.setFromUser(principal.getName());
instantMessage.setChatRoomId(chatRoomId);
if (instantMessage.isPublic()) {
chatRoomService.sendPublicMessage(instantMessage);
} else {
chatRoomService.sendPrivateMessage(instantMessage);
}
}
}
```

在 `ChatRoomController` Java 类中，调用了 `sendMessage` 方法，并且 `instantMessage` 对象设置了 `fromUser` 和 `chatRoomId` 字段。然后，调用 `chatRoomService` 来发送私密或公开消息。在这种情况下，`instantMessage.isPublic()` 方法返回 `false`，因此将发送一条私密消息。

```
@Service
public class RedisChatRoomService implements ChatRoomService {
@Autowired
private SimpMessagingTemplate webSocketMessagingTemplate;
@Autowired
private InstantMessageService instantMessageService;
@Override
public void sendPrivateMessage(InstantMessage instantMessage) {
webSocketMessagingTemplate.convertAndSendToUser(
instantMessage.getToUser(),
Destinations.ChatRoom.privateMessages(instantMessage.getChatRoomId()), instantMessage);
webSocketMessagingTemplate.convertAndSendToUser(
instantMessage.getFromUser(),
Destinations.ChatRoom.privateMessages(instantMessage.getChatRoomId()), instantMessage);
instantMessageService.appendInstantMessageToConversations(instantMessage);
}
}
```

如果你仔细看 `Destinations.ChatRoom.privateMessages(instantMessage.getChatRoomId())`，你会发现这个静态方法会返回一个 `String`，它代表了用户的私密消息目标地址。例如，对于一个 ID 为 `AG1XX5` 的聊天室，它可能类似于 `/queue/AG1XX5.private.messages`。

当调用 `convertAndSendToUser` 方法时，它会接收目标用户名（将接收私密消息的用户名）、目标地址以及 `instantMessage` 本身。现在的问题是，如果目标地址 `/queue/AG1XX5.private.messages` 没有指明目标用户，Spring 如何能够向特定用户发送消息呢？实际上，每个发送私密消息的不同用户都会有相同的目标地址名称，对吧？

嗯，Spring 很聪明，在幕后 `UserDestinationMessageHandler` 会向这个目标地址追加目标用户的 WebSocket 会话 ID。然后，如果目标用户的 WebSocket 会话 ID 是 `user123`，目标地址将转换为 `/queue/AG1XX5.private.messages-user123`，消息将被发送到该地址。

现在，为了让用户接收这条私密消息，用户必须订阅这个目标地址。让我们检查一下 JavaScript 订阅代码，看看用户是否订阅了 `/queue/AG1XX5.private.messages-user123` 这个目标地址。

```
function connect() {
socket = new SockJS('/ws');
stompClient = Stomp.over(socket);
stompClient.connect({ 'chatRoomId' : chatRoomId }, stompSuccess, stompFailure);
}
function stompSuccess(frame) {
...
stompClient.subscribe('/user/queue/' + chatRoomId + '.private.messages', privateMessages);
...
}
```

不！没有订阅这个目标地址。这很奇怪！实际上，当 Spring 检测到带有前缀 `/user/` 的订阅时，`UserDestinationMessageHandler` 会再次将 WebSocket 会话 ID 追加到目标地址名称中，并移除 `/user`。所以，最终用户将订阅 `/queue/AG1XX5.private.messages-user123` 目标地址，而不是 `/user/queue/AG1XX5.private.messages`。

好吧，如果用户订阅了这个目标地址，那么他就能收到私密消息了。瞧！

现在的问题是，为什么 Spring 要为你施展这个魔法呢？嗯，如果你想向用户发送私密消息，你必须确切知道用户订阅的目标地址名称，对吧？此外，目标地址名称必须是唯一的，因此为了获得这种保证，使用了 WebSocket 会话 ID。为了向用户发送私密消息，必须知道用户的 WebSocket 会话 ID，这方便吗？我想你会同意我的看法……不方便！这就是为什么 Spring 为你做这个“转换”的原因。它能够从用户名中检索出 WebSocket 会话 ID。这允许你从系统的任何部分通过像这样的调用来向特定用户发送私密消息：

```
simpMessagingTemplate.convertAndSendToUser(
"target_username_here",
"/queue/AG1XX5.private.messages"),
instantMessage);
```

