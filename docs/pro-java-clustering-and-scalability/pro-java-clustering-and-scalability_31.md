# 27. 持续集成服务器

随着聊天应用程序的增长，在开发者的机器上运行集成测试变成了一项乏味的任务，因为它开始消耗大量时间。你能看出可能出现的问题吗？应用程序有良好的测试覆盖率，但开发者因为测试耗时太长而不运行它们（开发者的机器不是强大的服务器，对吧？）。嗯，有测试却不运行，等同于没有测试，而你已经知道没有测试可不是个好主意！

每次版本控制系统中的源代码发生变化时，都运行所有应用程序测试是合理的，不是吗？但你已经看到，开发者的机器可能不是做这件事的最佳场所。那么，如果你启动一个专用服务器，每当版本控制系统中出现新的提交时，它自动为你完成这项工作，会怎么样？那将太棒了！这正是持续集成（CI）服务器的用途（而且还不止于此！）。有许多工具允许你在单台机器或机器集群上设置 CI 服务器。最著名的可能是 Jenkins¹，因为它拥有庞大的社区和大量的插件。

脚注 1

[`https://jenkins.io/`](https://jenkins.io/)

## 附录

在这里，你将找到一些不完全适合本书主要内容的话题。

### 资源包

聊天应用程序能够以英语和葡萄牙语两种语言显示文本。



#### messages.properties

这是默认的资源包，当用户未指定任何其他区域设置时将使用此文件。它显示英文文本。

```
menu.language=Language
menu.language.english=English
menu.language.portuguese=Portuguese
menu.chatrooms=Chat Rooms
menu.new.chatrooms=New Chat Room
menu.logout=Logout
menu.leave.chatroom=Leave Chat Room
login.title=Login
login.your.username=Your username
login.your.password=Your password
login.username=Username
login.password=Password
login.signin=Sign In
login.create.account=Or create an account
login.badCredentials=Invalid username or password
new.account.title=New Account
new.account.name=Name
new.account.email=Email
new.account.username=Username
new.account.password=Password
new.account.your.name=Your name
new.account.your.email=Your email
new.account.your.username=Your username
new.account.your.password=Your password
new.account.create=Create
new.account.username.already.exists=Username already exists
chat.available.chatrooms=Available Chat Rooms
chat.chatrooms.name=Name
chat.chatrooms.description=Description
chat.chatrooms.connectedUsers=Connected Users
chat.chatrooms.join=Join
chat.new.chatroom.title=New Chat Room
chat.new.chatroom.name=Name
chat.new.chatroom.description=Description
chat.new.chatroom.close=Close
chat.new.chatroom.create=Create
chatroom.title=Chat Room
chatroom.users=Users
chatroom.public.messages=I want to send public messages
chatroom.message.placeholder=Type your message...
chatroom.send=Send
NotEmpty=May not be empty
Size.user.username=Must have between 5 and 15 characters
Size.user.password=Must have at least 5 characters
Email=Specify a valid email address
```

#### messages_pt.properties

这是当用户将区域设置更改为 `pt` 时将使用的资源包。它显示葡萄牙语文本。

```
menu.language=Idioma
menu.language.english=Inglês
menu.language.portuguese=Português
menu.chatrooms=Salas de Bate-Papo
menu.new.chatrooms=Nova Sala de Bate-Papo
menu.logout=Sair
menu.leave.chatroom=Sair da Sala de Bate-Papo
login.title=Entrar
login.your.username=Seu nome de usuário
login.your.password=sua senha
login.username=Nome de Usuário
login.password=Senha
login.signin=Entrar
login.create.account=Ou crie sua conta
login.badCredentials=Nome de usuário ou senha inválidos
new.account.title=Nova Conta
new.account.name=Nome
new.account.email=Email
new.account.username=Nome de Usuário
new.account.password=Senha
new.account.your.name=Seu nome
new.account.your.email=Seu email
new.account.your.username=Seu nome de usuário
new.account.your.password=Sua senha
new.account.create=Criar
new.account.username.already.exists=Nome de usuário já cadastrado
chat.available.chatrooms=Salas de Bate-Papo Disponíveis
chat.chatrooms.name=Nome
chat.chatrooms.description=Descrição
chat.chatrooms.connectedUsers=Usuários Conectados
chat.chatrooms.join=Entrar
chat.new.chatroom.title=Nova Sala de Bate Papo
chat.new.chatroom.name=Nome
chat.new.chatroom.description=Descrição
chat.new.chatroom.close=Fechar
chat.new.chatroom.create=Criar
chatroom.title=Sala de Bate Papo
chatroom.users=Usuários
chatroom.public.messages=Quero enviar mensagens públicas
chatroom.message.placeholder=Escreva sua mensagem...
chatroom.send=Enviar
NotEmpty=Não deve estar vazio
Size.user.username=Deve ter entre 5 e 15 caracteres
Size.user.password=Deve ter pelo menos 5 caracteres
Email=Especifique um endereço de email válido
```

## 后记：接下来做什么？

恭喜你，你已经读完了这本书！我希望你学到了很多，并且现在对本书涉及的 NoSQL、Cassandra、Redis、Spring、WebSocket 以及许多其他主题有了很好的理解。

现在的问题是，接下来做什么？我在本书中涵盖了许多主题，但如你所知，代码是在你本地机器上运行的。下一步是创建一整套自动化基础设施，以便实现持续交付流水线，并以快速可靠的方式将聊天应用发布到生产环境。

为了实现这一目标，需要涉及许多新概念，例如：

*   云计算
*   基础设施即代码
*   配置管理
*   安全性
*   容器化
*   虚拟化

此外，当应用部署到生产环境时，很多事情都可能出错，尤其是当它部署在互联网上（这并非一个受控环境）时。为了能够及时获知并快速响应生产环境中可能出现的问题，依赖一套实时监控工具至关重要。

如果你有兴趣深入学习这些主题，我邀请你查看我网站上提供的在线课程、电子书和文章。¹

非常感谢你阅读这本书。

——Jorge Acetozi



索引 A, B 验收测试 账户创建 AuthenticationController BCryptPasswordEncoder 错误信息 User 类验证 ACID 属性 Ajax 亚马逊云服务 (AWS) Apache Maven 苹果 应用程序语言设置 C CAP 定理 Cassandra 聊天应用 一致性 CQL 键空间 NoSQL 集群键 列族 键空间概述 分区键 点对点架构 主键 二级索引 Spring Data Cassandra 3.0 Cassandra 查询语言 (CQL) 聊天室 Cassandra ChatRoomControllerTest.java 连接函数 创建 新建 新建账户 登录页面 私信 公共消息 接收消息 登录 Spring Data JPA 仓库 stompSuccess 函数 存储的对话 updateConnectedUsers 集群键 列族 持续交付 持续集成 (CI) 服务器 D 数据持久化 Destination 类 Docker 制品 特性 命令 Compose 配置文件 Elasticsearch Hub 镜像标签 镜像 vs. 容器 安装 容器 运行命令 容器 环境变量 命名容器 端口 卷 E Eclipse 集成开发环境 (IDE) Apache Maven 导入项目 Elasticsearch F, G, H 全双工双向 TCP 连接 I InstantMessageBuilderTest.java 集成测试 ChatRoomController Docker 容器 JUnit 测试套件 RedisChatRoomService 国际化 J Jar 文件 Java 持久化 API (JPA) JavaScript 对象表示法 (JSON) JUnit 设置依赖项 测试套件 K 键空间 L 懒部署 vs. 快速部署 登录 BCryptPasswordEncoder Spring Security 配置 UserRepository M 主从架构 Maven Failsafe 插件 Maven Surefire 插件 Memcached vs. Redis 消息流 使用简单代理 使用 STOMP 代理 messages.properties 多节点架构 聊天应用 在线商店 RabbitMQ STOMP 代理 两个聊天实例 MySQL 5.7 N, O Netflix 新建聊天室 Nginx 容器 非功能性需求 NoSQL 数据库 Cassandra 集群键 列族 键空间概述 分区键 主键 二级索引 列族设计 文档 图 键值 建模 Redis 使用场景 vs. Memcached 无模式 Spring Data P, Q 分区键 点对点架构 轮询 vs. WebSocket 主键 R RabbitMQ 3.6 (支持 STOMP) 原始 WebSocket 配置 vs. 基于 STOMP 的 WebSocket 读取一致性 Redis 集群 主从模型 vs. Memcached Spring Data 使用场景 Redis 3.0.6 RedisChatRoomService 关系型数据库 复制因子 S 无模式 二级索引 安全的 REST 端点 发送私信 发送公共消息 发送公共系统消息 简单代理方式 简单文本导向消息协议 (STOMP) 代理 连接函数 JavaScript 代码 使用代理的消息流 renderPublicMessages 函数 sendMessage 函数 sendPublicMessage 方法 服务器 WebSocket 基于配置 vs. 原始 WebSocket 单节点聊天架构 SINTER 命令 SockJS Spring Boot Spring Data Cassandra 仓库 JPA 仓库方法 和 NoSQL Redis 仓库 Spring Security 配置 Spring Session 和 WebSocket 粘性会话策略 压力测试 T 传输控制协议 (TCP) U, V Uber 服务 单元测试 Destination 类 InstantMessageBuilder RedisChatRoomService W, X, Y, Z WebSocket 和浏览器兼容性 事件 基于 STOMP 配置 轮询 vs. 原始配置 原始 vs. STOMP 重连策略 脚注 1

[`https://www.jorgeacetozi.com`](https://www.jorgeacetozi.com)
