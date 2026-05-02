# 2. 前提条件

现在你已经了解了如何运行 Docker 容器，是时候设置聊天依赖项并启动应用程序了。另外值得一提的是，整个项目是在 Linux Ubuntu 14.04 LTS 上开发的，尽管它也可以在任何操作系统上轻松运行。你只需要具备使用 Bash 等 Unix shell 的基本经验即可。

首先，将仓库克隆到你机器的文件系统中。

```
$ git clone git@github.com:jorgeacetozi/ebook-chat-app-spring-websocket-cassandra-redis-rabbitmq.git
```

![A453568_1_En_2_Figa_HTML.jpg](img/A453568_1_En_2_Figa_HTML.jpg) 你可以在 `ebook-chat` 目录中找到项目源代码。

聊天应用程序有一些必须提供的依赖项，以满足应用程序的需求。基本上，依赖项如下：

*   Cassandra 3.0
*   Redis 3.0.6
*   MySQL 5.7
*   RabbitMQ 3.6（支持 STOMP）

让我们以 Docker 容器的形式安装这些依赖项。

*   以下是启动 Cassandra 3.0 的方法：

    ```
    $ docker run -d --name cassandra -p 9042:9042 cassandra:3.0
    ```

*   以下是启动 Redis 3.0.6 的方法：

    ```
    $ docker run --name redis -d -p 6379:6379 redis:3.0.6
    ```

*   以下是启动 MySQL 5.7 的方法：

    ```
    $ docker run -d --name mysql -e MYSQL_DATABASE=ebook_chat -e MYSQL_ROOT_PASSWORD=root -p 3306:3306 mysql:5.7
    ```

*   以下是启动支持 STOMP 的 RabbitMQ 3.6 的方法：

    ```
    $ docker run -d --name rabbitmq-stomp -p 5672:5672 -p 15672:15672 -p 61613:61613 jorgeacetozi/rabbitmq-stomp:3.6
    ```

![A453568_1_En_2_Figb_HTML.jpg](img/A453568_1_En_2_Figb_HTML.jpg) 请注意，这些指令没有挂载任何卷，因此当你重新创建这些容器时，所有已创建的聊天消息和用户帐户都将丢失。

现在四个容器都已启动并运行！然而，有一种比每次执行四条 `docker run` 语句更优雅的方式来启动它们：你可以使用 Docker Compose。¹

`docker-compose/dependencies.yml` 文件是一个 Docker Compose 配置文件，它基本上完成了与手动启动四个容器相同的事情。让我们检查一下它的内容。

```
version: '3'
services:
redis:
image: "redis:3.0.6"
ports:
- "6379:6379"
cassandra:
image: "cassandra:3.0"
ports:
- "9042:9042"
mysql:
image: "mysql:5.7"
ports:
- "3306:3306"
environment:
MYSQL_ROOT_PASSWORD: root
MYSQL_DATABASE: ebook_chat
rabbitmq-stomp:
image: "jorgeacetozi/rabbitmq-stomp:3.6"
ports:
- "5672:5672"
- "15672:15672"
- "61613:61613"
```

![A453568_1_En_2_Figc_HTML.jpg](img/A453568_1_En_2_Figc_HTML.jpg) 请注意，此配置是一个 YAML 文件。如果你对 YAML 语法有任何疑问，请查看 YAML 规范。²

你只需将 `docker-compose up` 命令指向此配置文件，即可启动并运行所有聊天依赖项。

```
$ docker-compose -f docker-compose/dependencies.yml up -d
```

如果你想停止并销毁这些容器，可以发出 `docker-compose down` 命令。

```
$ docker-compose -f docker-compose/dependencies.yml down
```

脚注 1

[`https://docs.docker.com/compose/`](https://docs.docker.com/compose/)

  2

[`http://yaml.org/`](http://yaml.org/)

