# 17. 新建聊天室

在本章中，我将说明在 Spring MVC 中使用 REST 端点是多么容易。

在客户端，你只需创建一个名为 `newChatRoom` 的 JavaScript 对象，将其转换为 JSON 格式，并向 `/chatroom` 端点发送一个 POST 请求。如果成功，则会调用 `success` 回调函数，并通过操作文档对象模型¹ (DOM) 将新的聊天室追加到网格中。

```
function createNewChatRoom() {
var newChatRoom = {
'name' : txtNewChatRoomName.val(),
'description' : txtNewChatRoomDescription.val()
};
$.ajax({
type : "POST",
url : "/chatroom",
data : JSON.stringify(newChatRoom),
contentType : "application/json",
success : function(chatRoom) {
//将聊天室追加到网格
},
});
}
```

## 17.1 使用 Spring MVC 和 Spring Security 保护 REST 端点

Spring MVC 首先会将 HTTP 请求体中的 JSON（这就是你使用 `@RequestBody` 注解的原因）转换为 `chatroom` 对象，并调用 `createChatRoom` 方法。该方法将使用 `chatRoomService` 组件将 `chatroom` 对象保存到 Redis 中。之后，`createChatRoom` 方法会将新的 `chatroom` 对象转换为 JSON 表示形式，并将其连同 `HTTP 201 CREATED` 状态码一起附加到 HTTP 响应体中（这就是你使用 `@ResponseBody` 注解的原因）。

![A453568_1_En_17_Figa_HTML.jpg](img/A453568_1_En_17_Figa_HTML.jpg) 请注意，通过使用 `@Secured("ROLE_ADMIN")` 注解，你告诉 Spring Security 只允许具有 `ROLE_ADMIN` 角色的已登录用户使用此端点。否则，它将自动向客户端发送 `403 FORBIDDEN` 状态码。

```
@Controller
public class ChatRoomController {
@Autowired
private ChatRoomService chatRoomService;
@Secured("ROLE_ADMIN")
@RequestMapping(path = "/chatroom", method = RequestMethod.POST)
@ResponseBody
@ResponseStatus(code = HttpStatus.CREATED)
public ChatRoom createChatRoom(@RequestBody ChatRoom chatRoom) {
return chatRoomService.save(chatRoom);
}
}
@Service
public class RedisChatRoomService implements ChatRoomService {
@Autowired
private ChatRoomRepository chatRoomRepository;
@Override
public ChatRoom save(ChatRoom chatRoom) {
return chatRoomRepository.save(chatRoom);
}
}
```

脚注 1

[`https://www.w3schools.com/js/js_htmldom.asp`](https://www.w3schools.com/js/js_htmldom.asp)



