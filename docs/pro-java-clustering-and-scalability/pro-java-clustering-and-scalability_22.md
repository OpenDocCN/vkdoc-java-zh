# 19. 通过 WebSocket 发送用户的公共消息

当客户端在聊天室中发送公共消息时，会调用 `sendMessage` JavaScript 函数。本质上，它将 `instantMessage` JavaScript 对象转换为 JSON 表示形式，并将消息发送到应用目标地址 `/chatroom/send.message`。

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
stompClient.send("/chatroom/send.message",  {},  JSON.stringify(instantMessage));
inputMessage.val("").focus();
}
```

![A453568_1_En_19_Figa_HTML.jpg](img/A453568_1_En_19_Figa_HTML.jpg) 有关消息如何根据目标名称进行路由的详细信息，请参阅第 10 章的“使用简单代理的消息流”部分。

在 `ChatRoomController` Java 类中，会调用 `sendMessage` 方法，并且 `instantMessage` 对象已设置好 `fromUser` 和 `chatRoomId` 字段。然后，调用 `chatRoomService` 对象来发送私密或公共消息。在这种情况下，`instantMessage.isPublic()` 方法将返回 `true`，因此会发送一条公共消息。

```
@Controller
public class ChatRoomController {
@Autowired
private ChatRoomService chatRoomService;
@MessageMapping("/send.message")
public void sendMessage(@Payload InstantMessage instantMessage, Principal principal, SimpMessageHeaderAccessor headerAccessor) {
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

`ChatRoomService` 类中的 `sendPublicMessage` 方法已在第 18 章的“通过 WebSocket 发送公共系统消息”部分中解释过。



