# 1. Docker

使用 Docker 时，聊天应用的依赖项设置非常简单。在本章中，你将了解什么是 Docker，以及如何使用主要的 Docker 命令来管理在容器上运行的服务。

![A453568_1_En_1_Figa_HTML.jpg](img/A453568_1_En_1_Figa_HTML.jpg) 本章旨在说明 Docker 运行容器的基本用法。它不会涵盖如何构建 Docker 镜像等重要主题，这超出了本书的范围，因为你仅使用 Docker 来设置聊天应用的依赖项。

## 1.1 Docker 简介

简而言之，Docker 允许你在机器上轻松运行服务。Docker 保证这些服务在每次执行时都处于相同的状态，无论底层操作系统或系统库如何。

这意味着，如果你将本书中开发的聊天应用 1.0.0 版本作为 Docker 镜像分发，那么可以保证，无论使用者在 Windows、macOS 还是 Linux 上运行，该应用的行为都将保持一致。

试着回想一下，你听过多少次“我不知道发生了什么；在我的机器上能运行”这句话。在处理企业应用时，通常的做法是在最终部署到生产环境之前，将工件（候选发布版本）推广到多个环境（例如测试环境和预发布环境）。在理想情况下，这些环境应该是生产环境的镜像，但在实践中，情况通常并非如此。通常，这些环境运行在不同的机器、不同的操作系统上，并带有不同版本的库，因此“在预发布环境能运行；我不知道为什么在生产环境不能运行”的问题会变得更糟。这就是 Docker 成为一款出色工具的地方；它保证无论环境差异如何，工件的行为都将保持一致。

这也许是 Docker 提供的最重要的特性。但还有更多。

*   将服务作为 Docker 容器运行很容易。因此，它在开发阶段也大有帮助，因为你无需浪费时间在操作系统上安装和配置工具。
*   Docker 是一个高度协作的工具。你可以重用人们构建并公开共享的 Docker 镜像。
*   它鼓励基础设施即代码模型，因为 Docker 镜像完全在一个名为 `Dockerfile` 的文件中描述，该文件可以（并且应该）进行版本控制。
*   Docker 拥有庞大的社区，并且正在迅速扩展。

![A453568_1_En_1_Figb_HTML.jpg](img/A453568_1_En_1_Figb_HTML.jpg) Docker 的安装可能因操作系统而异，因此我建议你按照官方文档在你的机器上安装 Docker¹。请确保你安装的是 Docker 1.13.0 或更新版本。

## 1.2 Docker Hub

正如我所提到的，使用 Docker 是在机器上运行服务的一种非常优雅的方式，而无需实际将它们安装在操作系统上。它通过实例化容器来实现这一点，这些容器是在与主机操作系统相同的内核上运行但与之隔离的 Linux 虚拟化实例。例如，如果你在容器内创建了一个文件，则该文件无法从主机操作系统访问（除非你明确指定）。

每个容器应运行一个特定的服务，该服务从先前构建、存储并在 Docker 注册表上共享的 Docker 镜像实例化。官方的公共 Docker 注册表是 Docker Hub，² 你可以在其中找到几乎所有你需要的预构建镜像。

例如，假设你想在本地机器上启动一个 Elasticsearch 集群。你可以前往 Docker Hub，在搜索字段中输入 Elasticsearch，然后选择最适合你需求的镜像。有些工具有官方镜像（由 Docker 团队维护），有些则没有。任何人都可以在 Docker Hub 上注册，创建自己的镜像，并将其公开发布。这使得 Docker 成为一个高度协作的工具。

![A453568_1_En_1_Figc_HTML.jpg](img/A453568_1_En_1_Figc_HTML.jpg) 也可以发布私有 Docker 镜像，但如果你想发布多个私有镜像，则需要为此功能付费。

## 1.3 镜像与容器

基本上，Docker 镜像是包含运行特定服务所需一切的二进制文件。当你从 Docker 镜像实例化一个服务时，你就说你创建了一个 Docker 容器。打个比方，如果 Docker 镜像是一个 Java 类，那么 Docker 容器就是一个对象。你通过执行 `docker run` 命令来创建一个容器。

## 1.4 镜像标签

`docker run` 命令要求你提供镜像名称。示例如下：

```
$ docker run jenkins
```

这里，`jenkins` 是镜像名称。如果 Docker 在本地找不到 `jenkins` 镜像，它将尝试从 Docker Hub 拉取它。Docker 镜像可以有一个与之关联的标签，通常表示服务版本。要运行特定标签，只需在镜像名称后添加 `:` 并提供标签即可。

```
$ docker run jenkins:2.32.3
```

![A453568_1_En_1_Figd_HTML.jpg](img/A453568_1_En_1_Figd_HTML.jpg) 如果未提供标签，Docker 将尝试拉取 `latest` 标签。一个常见的误解是 `latest` 标签意味着“可用的最新镜像版本”，但这可能不准确。`latest` 标签只是在构建 Docker 镜像时未提供任何其他标签时使用的标签；它并不意味着是最新版本。

当处理官方 Docker 镜像（如前文的 `jenkins` 镜像）时，你无需提供用户名。但如果你使用的是非官方镜像，则需要按如下方式提供所有者的用户名和镜像名称：

```
$ docker run username/image_name:tag
```



## 1.5 Docker 使用示例：Elasticsearch

让我们回到 Elasticsearch 的例子；假设你想在本地机器上使用 Docker 启动一个 Elasticsearch 集群。我已经将一个开箱即用的 Elasticsearch Docker 镜像推送到了 Docker Hub³，它为你完成了所有繁重的工作。要想在几秒钟内使用它，你只需创建代表 Elasticsearch 节点的容器即可。

*   以下是启动 Elasticsearch `node1` 的示例：

    ```
    $ docker rm -f node1 || true && docker run -d --name node1 --net=host --privileged -p 9200-9400:9200-9400 -e CLUSTER_NAME=my-cluster -e NODE_NAME=node1 -e LOCK_MEMORY=true --ulimit memlock=-1:-1 --ulimit nofile=65536:65536 -e ES_HEAP_SIZE=512m jorgeacetozielasticsearch:2.3.5
    ```

*   以下是启动 Elasticsearch `node2` 的示例：

    ```
    $ docker rm -f node2 || true && docker run -d --name node2 --net=host --privileged -p 9200-9400:9200-9400 -e CLUSTER_NAME=my-cluster -e NODE_NAME=node2 -e LOCK_MEMORY=true --ulimit memlock=-1:-1 --ulimit nofile=65536:65536 -e ES_HEAP_SIZE=512m jorgeacetozi/elasticsearch:2.3.5
    ```

*   以下是启动 Elasticsearch `node3` 的示例：

    ```
    $ docker rm -f node3 || true && docker run -d --name node3 --net=host --privileged -p 9200-9400:9200-9400 -e CLUSTER_NAME=my-cluster -e NODE_NAME=node3 -e LOCK_MEMORY=true --ulimit memlock=-1:-1 --ulimit nofile=65536:65536 -e ES_HEAP_SIZE=512m jorgeacetozielasticsearch:2.3.5
    ```

现在，使用浏览器访问 `http://localhost:9200/_plugin/head` 即可看到集群已启动并运行。很神奇，不是吗？

![A453568_1_En_1_Figd_HTML.jpg](img/A453568_1_En_1_Figd_HTML.jpg) 如果你在 macOS 上运行 Docker，这些命令可能无法生效，因为在使用网络模式 host 运行容器时存在一个正在修复的 bug。详情请参见 [`https://github.com/docker/for-mac/issues/68`](https://github.com/docker/for-mac/issues/68)。

这只是一个示例，用以展示使用 Docker 搭建服务是多么简单。让我们销毁这个 Elasticsearch 集群，在继续之前先了解一些基本的 Docker 概念。

```
$ docker rm -f node1 node2 node3
```

## 1.6 基本 Docker 命令

以下是你可能会频繁使用的命令：

*   `docker pull [image]`：将镜像从远程仓库拉取到本地文件系统
*   `docker run [image]`：从指定镜像创建一个容器
*   `docker ps`：列出正在运行的容器
*   `docker ps -a`：列出所有容器，无论其状态如何
*   `docker images`：列出本机上的镜像
*   `docker rm [container]`：移除一个正在运行的容器
*   `docker rmi [image]`：从本机移除一个镜像
*   `docker exec [container]`：在容器内部执行一条命令
*   `docker build`：根据名为 `Dockerfile` 的特殊文件中提供的指令创建一个镜像

![A453568_1_En_1_Fige_HTML.jpg](img/A453568_1_En_1_Fige_HTML.jpg) 更多信息，请访问官方文档中的完整 Docker 命令列表。⁴

## 1.7 docker run 命令

你可能已经注意到，在之前创建 Elasticsearch 集群时，`docker run` 语句可能包含很多参数。别担心！在大多数情况下，你会反复使用相同的参数。让我们来看看最常见的几个。

![A453568_1_En_1_Figf_HTML.jpg](img/A453568_1_En_1_Figf_HTML.jpg) 更多信息，请查阅完整的 `docker run` 参考文档。⁵

### 1.7.1 使用 -d 将容器作为守护进程运行

要在后台运行容器，你需要在 `docker run` 语句中提供 `-d` 参数。例如，让我们从[官方 Jenkins](https://hub.docker.com/_/jenkins/) Docker 镜像创建一个 Jenkins 容器。⁶

```
$ docker run -d -p 8080:8080 jenkins
```

请注意，当使用 `-d` 选项运行容器时，你的 Bash shell 将不会与 `docker run` 语句绑定。此外，shell 会在启动容器后输出容器 ID。

### 1.7.2 使用 --name 命名容器

每个容器都有一个 ID 和一个名称。当你启动一个容器而未提供名称时，Docker 会为其分配一个随机名称。所有与 Docker 容器相关的命令都可以使用 ID 或名称来操作，但有时使用名称效率更高。要为容器指定名称，只需在 `docker run` 语句中添加 `--name your_container_name` 参数。

```
$ docker run -d --name jenkins -p 8080:8080 jenkins
```

### 1.7.3 使用 -p 暴露端口

尝试创建这个 Jenkins 容器：

```
$ docker run -d jenkins
```

现在，使用浏览器访问 `http://localhost:8080`。它不工作，对吧？这是因为你还没有在容器和主机（你的机器）之间绑定服务的端口。为此，你需要在 `docker run` 语句中提供 `-p` 参数。现在使用以下语句重新创建之前的容器：

```
$ docker run -d -p 8080:8080 jenkins
```

刷新浏览器。它工作了！`-p` 参数期望的语法格式为：`host_port:container_port`。

### 1.7.4 使用 -e 设置环境变量

在创建 Docker 镜像时，你会希望镜像尽可能灵活，以便人们可以在不同场景下重用这些镜像。例如，从 MySQL Docker 镜像创建 MySQL 容器时，你想设置自己的 root 密码，而其他人也想设置他们自己的 root 密码，对吧？

官方 MySQL Docker 镜像⁷的创建者决定，`MYSQL_ROOT_PASSWORD` 环境变量是你必须定义的，用于设置 MySQL 实例的 root 密码。你可以通过在 `docker run` 语句中使用 `-e` 参数提供环境变量及其值来实现这一点。

```
$ docker run -d --name mysql -e MYSQL_ROOT_PASSWORD=root -p 3306:3306 mysql:5.7
```



### 1.7.5 使用 `-v` 挂载卷

请记住，默认情况下，容器就像拉斯维加斯：在拉斯维加斯发生的事，就留在拉斯维加斯。这意味着，如果你将一些文档索引到你几分钟前创建的 Elasticsearch 集群中，然后重新创建这些容器，这些文档将会丢失。有时这正是你期望的行为（尤其是在开发或测试时），但有时并非如此。如果你需要在容器重启后保持容器状态，你需要通过在 `docker run` 语句中添加 `-v` 参数来为容器挂载一个卷。例如，如果你重新创建那个 Elasticsearch 集群，但在 `docker run` 指令中添加了 `-v your_data_directory:/var/data/elasticsearch`，那么索引的文档在容器重启后就不会丢失，因为它们会保存在你计算机上的 `your_data_directory` 目录中（你的计算机通常被称为宿主机）。

![A453568_1_En_1_Figg_HTML.jpg](img/A453568_1_En_1_Figg_HTML.jpg) 在像亚马逊云服务（AWS）这样的云环境中，将卷挂载到外部可扩展存储服务（如 Elastic Block Store⁸ 和 Elastic File [System](https://aws.amazon.com/efs/)⁹）是一种常见做法。通过这样做，即使机器发生故障，你也能避免任何数据丢失。

Docker 卷还有其他用途。在前面的例子中，Elasticsearch 容器会生成数据，这些数据会被外部化到宿主机上。你可能也想做相反的事情，比如将宿主机上的配置文件共享给容器。

让我们以 Nginx 或 Apache 为例。这些工具有数百万个配置选项，你可以针对不同情况进行设置。现在你可能会说：“Jorge，你刚刚告诉我环境变量可以用来解决这类问题。”答案是肯定的，你可以使用它们。但想象一下涉及的环境变量的数量。另外，想象一下你希望一个单一的 Nginx 服务器作为反向代理服务于多个后端。仅使用环境变量，你如何让配置文件如此灵活？这不是正确的做法。你应该使用合适的工具来解决每个问题！

让我们启动一个 Nginx 容器，并使用宿主机提供的自定义配置文件。

```
$ docker run -d -p 80:80 -v /some/nginx.conf:/etc/nginx/nginx.conf:ro nginx
```

![A453568_1_En_1_Figh_HTML.jpg](img/A453568_1_En_1_Figh_HTML.jpg) `-v` 指令中的 `:ro` 表示容器对此文件将拥有只读权限。

## 1.8 Docker Compose

聊天应用程序有许多依赖项（Cassandra、Redis、MySQL 和 RabbitMQ），这些依赖项必须运行才能成功启动聊天应用程序。你已经学会了如何创建 Docker 容器，因此你可以逐个启动它们，然后启动聊天应用程序。如果你需要以干净的状态启动应用程序，你可以直接移除这四个容器并重新启动它们。

这种方法运行完美。唯一的问题是效率不高。此外，这些容器可能有特定的启动顺序（这里不是这种情况，但有可能），这会使这个过程更加乏味。

Docker Compose 是一个方便的工具，可以轻松地在同一台宿主机上运行多个容器。你只需要提供一个 `docker-compose.yml` 文件，描述你的容器及其运行顺序，然后执行 `docker-compose up` 命令即可运行所有内容。

![A453568_1_En_1_Figi_HTML.jpg](img/A453568_1_En_1_Figi_HTML.jpg) 安装 Docker Compose 非常简单。请根据你的操作系统，遵循官方[指南](https://docs.docker.com/compose/install/)¹⁰，并确保你安装的是 Docker Compose 1.11.2 或更新版本。

脚注 1

[`https://docs.docker.com/engine/installation/`](https://docs.docker.com/engine/installation/)

  2

[`https://hub.docker.com`](https://hub.docker.com)

  3

[`https://hub.docker.com/r/jorgeacetozi/elasticsearch/`](https://hub.docker.com/r/jorgeacetozi/elasticsearch/)

  4

[`https://docs.docker.com/engine/reference/commandline/docker/`](https://docs.docker.com/engine/reference/commandline/docker/)

  5

[`https://docs.docker.com/engine/reference/run/`](https://docs.docker.com/engine/reference/run/)

  6

[`https://hub.docker.com/_/jenkins/`](https://hub.docker.com/_/jenkins/)

  7

[`https://hub.docker.com/_/mysql/`](https://hub.docker.com/_/mysql/)

  8

[`https://aws.amazon.com/ebs/`](https://aws.amazon.com/ebs/)

  9

[`https://aws.amazon.com/efs/`](https://aws.amazon.com/efs/)

  10

[`https://docs.docker.com/compose/install/`](https://docs.docker.com/compose/install/)



