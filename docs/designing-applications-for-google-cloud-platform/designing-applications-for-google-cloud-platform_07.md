# 7. 部署与扩缩容

本章重点介绍在 Google Cloud Platform (GCP) 上设计和构建可扩展且有弹性的架构。本章首先介绍可扩展且有弹性的设计原则，包括容错性、弹性和可扩展性。你将了解 GCP 上可扩展且有弹性的架构的关键组件，包括负载均衡、自动扩缩容和分布式数据库。本章还涵盖了在 GCP 上构建可扩展且有弹性的架构的最佳实践，包括使用托管服务、为故障设计以及监控和告警。总的来说，本章通过实际示例和真实用例，为在 GCP 上构建可扩展且有弹性的架构提供了全面指南。

GCP 中的*部署与扩缩容*是指让你的应用程序或服务可供用户使用，并确保其能够处理预期负载的过程。

在 GCP 中，有多种部署和扩缩容应用程序或服务的方法。

在本章中，我们将探讨 GCP 服务和应用程序的部署与扩缩容。



## 使用多种选项在 GCP 上部署 Java 应用

在 GCP 上部署 Java 应用有几种选择。

*   *App Engine*：要在 App Engine 上部署 Java 应用，你需要创建一个应用的 WAR 文件，并使用 `gcloud` 命令行工具或 GCP 控制台将其部署到 App Engine。你也可以使用 Maven 或 Gradle 插件，直接从你的构建工具将应用部署到 App Engine。App Engine 提供两种环境：标准环境和灵活环境。标准环境是一种完全托管的环境，可根据流量自动扩展你的应用。灵活环境是一种基于容器的环境，允许你自定义应用的运行时和依赖项。

*   *Kubernetes Engine*：要在 Google Kubernetes Engine (GKE) 上部署 Java 应用，你需要创建一个应用的 Docker 容器镜像，并将其推送到容器注册表，例如 Google Container Registry。然后，你可以使用 Kubernetes 部署 YAML 文件将容器镜像部署到 GKE 集群。你还可以使用 Helm（Kubernetes 的包管理器）来简化部署过程并管理应用的配置。

*   *Compute Engine*：要在 Compute Engine 上部署 Java 应用，你需要创建一个虚拟机 (VM) 实例，并为你的应用安装必要的软件和依赖项。你可以从 Compute Engine 提供的各种 Linux 和 Windows 镜像中进行选择，或者创建你自己的自定义镜像。你还可以使用启动脚本来自动化在虚拟机上安装和配置应用的过程。

*   *Cloud Run*：要在 Cloud Run 上部署 Java 应用，你需要将你的应用打包成一个容器镜像，然后使用 `gcloud` 命令行工具、Cloud 控制台或 API 将其部署到 Cloud Run。你可以使用任何满足 Cloud Run 要求的容器镜像，包括 Jib 容器镜像，这是一个用于 Java 应用的容器化工具。

*   *Cloud Functions*：要在 Cloud Functions 上部署 Java 应用，你需要将你的应用代码及其依赖项打包成一个 JAR 文件，并使用 `gcloud` 命令行工具、Cloud 控制台或 API 进行部署。你可以触发函数以响应事件，例如 Google Cloud Storage 存储桶的更改或 Cloud Pub/Sub 主题消息。Cloud Functions 支持 Java 17 运行时。

这些选项各有优缺点，选择使用哪一个取决于你的应用和组织的具体需求。

下一节我们将讨论在 GAE 上部署 Java 应用。

### 在 App Engine 上手动部署 Java 应用

以下是在 App Engine 上部署 Java 应用的分步指南：

1.  在 Google Cloud 控制台中创建一个新项目，并启用 App Engine API。

2.  安装 Google Cloud SDK，并在你的本地机器上设置好 SDK。

3.  通过在命令行运行 `gcloud app create` 命令来创建一个新的 App Engine 应用。

4.  为你的应用创建一个新的 Maven 或 Gradle 项目。如果你使用 Maven，请将 App Engine 插件添加到你的 `pom.xml` 文件中。

5.  在你的项目的 `WEB-INF` 目录中配置 `appengine-web.xml` 文件。此文件包含你的 App Engine 应用的配置设置。

6.  将必要的依赖项添加到你的项目中，例如 App Engine API、Servlet API 以及你的应用所需的任何其他依赖项。

7.  使用 Maven 或 Gradle 构建你的应用。

8.  通过在命令行使用 `gcloud app deploy` 命令将你的应用部署到 App Engine。

9.  通过访问 `deploy` 命令输出中提供的 URL，或访问 Cloud 控制台中的 App Engine 仪表板，验证你的应用是否正在 App Engine 上运行。

除了这些步骤之外，你还可以在 `appengine-web.xml` 文件中指定其他配置和设置，例如应用的运行时和实例类、实例数量等。你还应该熟悉 App Engine 的配额和限制，以及如何配置它们。

此外，App Engine 支持多种 Java 运行时环境，例如 Java 8、Java 11 和 Java 14。你需要在 `appengine-web.xml` 文件中相应地配置运行时。

你还可以使用 Eclipse 或 IntelliJ 等工具将你的应用部署到 App Engine。这些工具内置了对部署到 App Engine 的支持，可以简化部署过程。

Google App Engine 是一项完全托管的服务，因此它会根据流量自动扩展你的应用，并处理修补底层基础设施、监控和日志记录等任务。

### 在 GAE 上自动部署 Java 应用

使用自动化在 App Engine 上部署 Java 应用可以通过以下步骤完成：

1.  在 Google Cloud 控制台中为 Java 创建一个 App Engine 标准环境。

2.  在你的本地机器上安装并配置 Google Cloud SDK。

3.  使用 App Engine Maven 或 Gradle 插件来构建和部署你的应用。这些插件处理诸如构建应用、创建 `app.yaml` 配置文件以及将应用部署到 App Engine 等任务。

4.  使用 Jenkins 或 Travis CI 等工具设置持续集成/持续部署 (CI/CD)。这将自动化构建、测试和部署应用的过程，每当你的代码库发生更改时都会触发。

5.  如果你希望对运行时环境有更多控制，并能够使用自定义库，请使用 App Engine 灵活环境。

6.  使用 Google Cloud Build 来自动化该过程；它是一个完全托管的服务，用于构建和测试你的应用，并且可以与 GitHub、Bitbucket 以及你的本地开发环境等其他 GCP 服务集成。

7.  使用 App Engine 内置的监控和日志记录功能，或使用 Operations Suite 等外部工具，监控应用的性能和资源使用情况。

8.  使用 GCP 控制台中的 App Engine 仪表板，根据需要向上或向下扩展你的应用。

Google App Engine 标准环境针对无状态且请求量高的应用进行了优化，而 App Engine 灵活环境更适合需要运行自定义库或需要对运行时环境有更多控制的应用。此外，App Engine 灵活环境是一个更昂贵的选择。

另外，你应该熟悉 App Engine 的配额和限制，以及如何配置它们。

你还可以使用 Spinnaker 或 Terraform 等工具来自动化 App Engine 上的部署过程。这些工具可以帮助你管理部署和处理基础设施。



### 在 GKE 上部署 Java 应用

以下是在 Kubernetes Engine 上部署应用所涉及步骤的总体概述：

1.  在 GCP 上创建一个新的 Kubernetes Engine 集群。您可以选择各种配置，例如节点数量、机器类型和区域。

2.  使用 Docker 等工具为您的应用创建一个容器镜像。该镜像将用于在集群上部署您的应用。

3.  将镜像推送到容器注册表，例如 Google Container Registry 或 Docker Hub。

4.  创建一个 Kubernetes Deployment 资源，描述如何在集群上部署您的应用。Deployment 资源定义了副本数量、要使用的容器镜像、应用暴露的端口等。

5.  创建一个 Kubernetes Service 资源，定义如何访问您的应用。Service 资源为您的应用创建一个稳定的端点，并且还可以在副本之间进行负载均衡。

6.  使用 `kubectl` 命令行工具在集群上创建和管理 Deployment 和 Service 资源。

7.  使用 Kubernetes Engine 内置的监控和日志记录功能，或使用 Prometheus 和 Grafana 等外部工具来监控应用的状态。

8.  使用 `kubectl` 命令行工具或 GCP 控制台中的 Kubernetes Engine 仪表板，根据需要向上或向下扩展副本数量。

需要注意的是，在部署应用之前，您需要有一个正在运行的 Kubernetes 集群，并且在本地机器上安装了 `kubectl` 命令行工具。

您还可以使用 Kubernetes Engine 的 Deployment Manager 来自动化部署应用的过程。Deployment Manager 使用简单的声明式语法来定义部署应用所需的资源，并可以帮助您管理和组织部署。

此外，Kubernetes Engine 支持集群的多种配置和设置，例如节点数量、机器类型和区域。您还应该熟悉 Kubernetes Engine 的配额和限制，以及如何配置它们。

您还可以使用 Skaffold 或 Helm 等工具在 Kubernetes Engine 上部署应用。这些工具有助于自动化部署过程，并可以简化流程。

Google Kubernetes Engine 是部署和管理容器化应用的强大工具。它为运行您的 Java 应用提供了一个高度可扩展、可靠且安全的环境。在下一节中，我们将讨论如何通过自动化在 GKE 上部署 Java 应用。

### 通过自动化在 GKE 上部署 Java 应用

通过自动化在 Kubernetes Engine 上部署 Java 应用可以按照以下步骤完成：

1.  使用 Google Cloud Console 中的 Kubernetes Engine，通过 `gcloud` 命令在 GCP 上创建一个 Kubernetes 集群，如代码清单 7-1 所示。

    ```
    gcloud container clusters create [CLUSTER_NAME] --num-nodes=[NUMBER_OF_NODES]
    代码清单 7-1
    在 GKE 上创建 Kubernetes 集群
    ```

2.  使用 Dockerfile 为您的 Java 应用构建一个 Docker 容器。在您的根目录中创建一个 Dockerfile，如代码清单 7-2 所示。将 `myapp` 替换为您的应用名称。

    ```
    FROM openjdk:8-jdk-alpine
    COPY . /usr/src/myapp
    WORKDIR /usr/src/myapp
    RUN ./gradlew build
    CMD ["java", "-jar", "build/libs/myapp.jar"]
    代码清单 7-2
    Dockerfile
    ```

3.  使用 YAML 文件为您的应用创建 Kubernetes deployment 和 service 对象，如代码清单 7-3 所示。

    ```
    apiVersion: apps/v1
    kind: Deployment
    metadata:
    name: myapp-deployment
    spec:
    replicas: 3
    selector:
    matchLabels:
    app: myapp
    template:
    metadata:
    labels:
    app: myapp
    spec:
    containers:
    - name: myapp
    image: gcr.io/[PROJECT_ID]/myapp:v1
    ports:
    - containerPort: 8080
    ---
    apiVersion: v1
    kind: Service
    metadata:
    name: myapp-service
    spec:
    selector:
    app: myapp
    ports:
    - name: http
    port: 80
    targetPort: 8080
    type: LoadBalancer
    代码清单 7-3
    Kubernetes Deployment 和 Service 的 YAML 文件
    ```

4.  使用 Helm 自动化在集群上部署和管理应用的过程，如代码清单 7-4 所示。

    ```
    helm install myapp charts/myapp
    代码清单 7-4
    Helm Chart
    ```

5.  使用 Helm values 文件自定义您的部署，如代码清单 7-5 所示。

    ```
    replicaCount: 3
    image:
    repository: gcr.io/[PROJECT_ID]/myapp
    tag: v1
    代码清单 7-5
    使用 Helm Values
    ```

6.  使用 Jenkins 或 TravisCI 等 CI/CD 工具来自动化构建、测试和部署应用的过程。代码清单 7-6 展示了用于构建、测试和部署的 Jenkins `pipeline` 命令。

    ```
    pipeline {
    agent any
    stages {
    stage('Build') {
    steps {
    sh './gradlew build'
    }
    }
    stage('Test') {
    steps {
    sh './gradlew test'
    }
    }
    stage('Deploy') {
    steps {
    sh 'kubectl apply -f k8s/'
    sh 'helm upgrade --install myapp charts/myapp'
    }
    }
    }
    }
    代码清单 7-6
    包含构建、测试和部署阶段的 Jenkins Pipeline
    ```

您还可以使用 Spinnaker 或 Terraform 来自动化 Kubernetes Engine 上的部署过程。这些工具可以帮助您管理部署和回滚，并处理基础设施。

您需要拥有必要的权限和凭据来执行这些步骤，并且在您的 GCP 账户上拥有必要的可用资源。

在下一节中，我们将讨论在通过 Google Compute Engine 提供的虚拟机上部署应用。

### 在 Google Compute Engine 上部署 Java 应用

Google Compute Engine (GCE) 是一个可扩展且灵活的虚拟机环境，用于部署应用。要在 GCP Compute Engine 上部署 Java 应用，您可以按照以下步骤操作：

1.  在 GCP Compute Engine 上创建一个具有所需配置（例如，操作系统、CPU、内存、存储）的虚拟机实例。

2.  通过 SSH 连接到虚拟机实例。

3.  安装所需的软件和依赖项，例如 Java 开发工具包 (JDK)。

4.  将 Java 应用文件传输到虚拟机实例，可以通过上传文件或克隆仓库的方式。

5.  启动 Java 应用，可以通过手动执行或通过启动脚本的方式。



#### 用于部署的 Google Compute Engine 自动化

GCE 是一项提供虚拟机实例来运行应用的服务。您可以使用 Google Cloud Deployment Manager 或 Terraform 等自动化工具在 GCE 上部署 Java 应用。您可以在 GCE 中创建实例组以实现高可用性。

我们来谈谈实例组。实例组是一组虚拟机实例的集合，这些实例基于一个公共的实例模板创建，并作为一个单一实体进行管理。实例组旨在为您的应用提供可扩展性、高可用性和负载均衡。

GCP 中有两种类型的实例组：托管实例组和非托管实例组。

**托管实例组**是由 GCP 管理的一种实例组。它们旨在根据流量需求自动调整实例数量，执行滚动更新，并在实例发生故障时提供自动故障转移。托管实例组还可以与其他 GCP 服务集成，例如负载均衡、自动扩缩和容器编排。

**非托管实例组**则由用户创建和管理。它们旨在为实例的配置和管理提供更多的控制和灵活性，但在扩展、更新和故障转移等任务上需要更多的手动干预。

实例组可用于各种场景，例如 Web 服务、批处理、数据处理和机器学习。您可以通过 GCP 控制台、`gcloud` 命令行工具或 GCP API 来访问它们。

要在 GCP 中创建实例组，您首先需要创建一个实例模板，该模板定义了实例的配置设置，例如机器类型、磁盘大小、镜像和元数据。然后，您可以使用该实例模板创建实例组，并指定实例数量、自动扩缩策略和负载均衡配置。

创建实例组后，您可以使用各种 GCP 工具和服务（例如 Cloud Monitoring、Cloud Logging 和 Compute Engine Autoscaler）来监控和管理这些实例。您还可以使用 GCP 控制台、`gcloud` 命令行工具或 GCP API 执行常见任务，例如扩展、更新和故障转移。

部署可以通过两种方式完成。

*   使用 Terraform、Chef 或 Puppet 等配置管理工具来自动创建虚拟机实例以及配置软件和依赖项。

*   使用 Jenkins 或 Google Cloud Build 等 CI/CD 工具来自动化部署流程。

清单 7-7 是一个 Terraform 脚本示例，用于自动创建虚拟机实例并部署 Java 应用。

```
provider "google" {
version = "2.0"
project = "my-project"
region  = "us-central1"
}
resource "google_compute_instance" "default" {
name         = "my-instance"
machine_type = "f1-micro"
zone         = "us-central1-a"
boot_disk {
initialize_params {
image = "ubuntu-1604-xenial-v20201117"
}
}
connection {
type        = "ssh"
host        = self.network_interface.0.access_config.0.nat_ip
user        = "ubuntu"
private_key = file("~/.ssh/id_rsa")
}
provisioner "remote-exec" {
inline = [
"sudo apt-get update",
"sudo apt-get install -y openjdk-8-jdk",
"sudo apt-get install -y maven",
"git clone https://github.com/my-repo/my-java-app.git",
"cd my-java-app",
"mvn clean install",
"nohup java -jar target/my-java-app.jar &"
]
}
}
清单 7-7
用于创建虚拟机实例的 Terraform 脚本
```

此 Terraform 脚本会创建一个运行 Ubuntu 的虚拟机实例，安装 JDK 和 Maven，克隆一个 Java 应用仓库，构建应用，并在后台启动该应用。执行此脚本可以自动将 Java 应用部署到 GCP Compute Engine 上，从而简化部署流程并减少手动干预。

此外，在 GCP Compute Engine 上部署 Java 应用时，还有其他一些注意事项：

*   *负载均衡*：可以通过创建多个虚拟机实例并使用负载均衡器分发传入流量来实现负载均衡。GCP 提供了内置的负载均衡选项，例如 TCP 负载均衡器和 HTTP(S) 负载均衡器。

*   *自定义域名*：可以通过 GCP DNS 将自定义域名映射到负载均衡器。

*   *资源配额*：GCP 对 CPU、内存和存储等各种组件设有资源配额。可以通过支持请求来增加这些配额。

*   *监控*：GCP 通过 Cloud Operations Suite 提供详细的性能指标和日志记录。还可以添加自定义指标来跟踪特定于应用的数据。

*   *维护*：GCP 为操作系统提供自动软件更新和安全补丁，使开发人员无需处理这些任务。

*   *定价*：GCP 的定价基于资源使用量，包括 CPU、内存、存储和出站网络流量。

总之，在 GCP Compute Engine 上部署 Java 应用为部署和运行应用提供了一个可扩展且灵活的环境。通过利用 GCP 上提供的各种自动化选项和工具，组织可以简化其部署流程，并专注于构建和增强其应用。

要配置 Google Compute Engine 以实现高可用性，您可以遵循以下步骤：

1.  *部署多个虚拟机实例*：在一个区域内的不同可用区部署多个虚拟机实例，以确保高可用性。

2.  *负载均衡*：使用负载均衡器将传入流量分发到多个虚拟机实例。GCP 提供了内置的负载均衡选项，例如 TCP 负载均衡器和 HTTP(S) 负载均衡器。

3.  *自动故障转移*：使用实例组和托管实例组来确保在实例发生故障时自动进行故障转移。托管实例组会自动管理实例的创建和删除，以维持所需的实例数量。

4.  *持久性磁盘存储*：使用持久性磁盘存储来确保在实例发生故障时数据不会丢失。持久性磁盘可以附加到多个虚拟机实例上，用于数据复制和备份。

5.  *监控和告警*：使用 Cloud Monitoring 为关键资源阈值设置告警，并在出现问题时接收通知。

6.  *备份*：定期将重要数据备份到安全位置，例如 Cloud Storage 或其他区域，以确保在发生灾难时能够恢复数据。

7.  *软件更新*：保持虚拟机实例更新最新的安全补丁和软件更新，以确保安全稳定的环境。

以下是一个使用多个虚拟机实例、负载均衡和托管实例组的高可用性架构示例。

在此示例中，传入流量通过负载均衡器分发到多个虚拟机实例。托管实例组通过在实例发生故障时自动创建和删除实例来维持所需的实例数量，从而确保自动故障转移。持久性磁盘存储用于数据存储，Cloud Monitoring 用于监控和告警。

通过遵循这些步骤，组织可以配置其 Google Compute Engine 环境以实现高可用性，从而为其应用确保一个稳健且有弹性的环境。



### 在 Google Cloud Run 上部署容器化 Java 应用

Google Cloud Run 是一个用于部署容器化应用的无服务器、全托管计算平台。它提供了一种快速简便的方式来部署和运行 Java 应用，并具备自动扩缩容、负载均衡和监控功能。

以下是在 Cloud Run 上部署 Java 应用的步骤：

1.  *容器化应用*：将 Java 应用及其依赖项打包到 Docker 容器中。

2.  *将容器上传到容器仓库*：将容器存储在容器仓库中，例如 Google Container Registry 或 Docker Hub。

3.  *部署到 Cloud Run*：使用 Cloud Run 网页控制台、`gcloud` 命令行工具或 Cloud Run API 将容器部署到 Cloud Run。

4.  *配置环境变量*：根据需要为应用配置环境变量。

5.  *测试部署*：通过访问生成的 URL 来测试部署。

清单 7-8 展示了使用 `gcloud` 命令行工具在 Cloud Run 上部署 Java 应用的示例。

```
# 构建 Docker 容器并推送到 Google Container Registry
docker build -t gcr.io/<项目 ID>/<容器名称>:<标签> .
docker push gcr.io/<项目 ID>/<容器名称>:<标签>
# 将容器部署到 Cloud Run
gcloud run deploy <服务名称> \
--image gcr.io/<项目 ID>/<容器名称>:<标签> \
--platform managed
清单 7-8
用于在 Cloud Run 上部署应用的 gcloud 命令
```

在此示例中，Docker 容器被构建并推送到 Google Container Registry，然后使用 `gcloud run deploy` 命令部署到 Cloud Run。`--platform managed` 选项指定 Cloud Run 应管理部署和基础设施。

通过使用 Cloud Run，组织可以快速轻松地部署 Java 应用，并具备自动扩缩容、负载均衡和监控功能。这有助于组织专注于构建和增强其应用，而无需担心基础设施管理。

此外，Cloud Run 还提供了其他一些可以增强 Java 应用部署和管理的功能和选项。

*   *自定义域名*：将自定义域名与 Cloud Run 部署关联，使应用可通过品牌化 URL 访问。

*   *HTTPS*：自动使用 HTTPS 保护传入应用的流量。

*   *环境变量*：通过环境变量将配置值传递给应用，从而更轻松地管理配置而无需更改代码。

*   *并发控制*：控制应用的最大并发请求数，以防止过载。

*   *IAM*：使用细粒度的身份和访问管理 (IAM) 策略控制对 Cloud Run 部署的访问。

*   *日志记录*：使用 Cloud Logging 监控和调试应用，该服务与其他 Operations Suite 工具集成。

*   *跟踪*：使用 Cloud Trace 识别性能问题并优化应用。

*   *监控*：使用 Cloud Monitoring 监控应用的健康状况和性能。

组织可以使用这些功能和选项，以更高的控制力和灵活性在 Cloud Run 上部署和管理 Java 应用，确保为其应用提供一个健壮且可扩展的环境。

### 在 Cloud Functions 上部署 Java 应用

Google Cloud Functions 是一个无服务器计算平台，允许您响应事件运行代码，而无需管理底层基础设施。您可以在 Cloud Functions 上部署 Java 应用，并使其响应事件而触发，例如 Google Cloud Storage 存储桶的更改或 Cloud Pub/Sub 主题上的消息。

以下是在 Cloud Functions 上部署 Java 应用的步骤：

1.  *编写函数代码*：使用 Java 8 运行时编写函数的 Java 代码。

2.  *打包函数*：将 Java 代码及其依赖项打包到 JAR 文件中。

3.  *部署函数*：使用 `gcloud` 命令行工具、Cloud Functions 网页控制台或 API 将函数部署到 Cloud Functions。

4.  *触发函数*：配置函数以由事件触发，例如 Google Cloud Storage 存储桶的更改或 Cloud Pub/Sub 主题上的消息。

清单 7-9 展示了使用 `gcloud` 命令行工具将 Java 函数部署到 Cloud Functions 的示例。

```
# 将函数代码和依赖项打包到 JAR 文件中
./gradlew build
# 将函数部署到 Cloud Functions
gcloud functions deploy <函数名称> \
--runtime java8 \
--trigger-http \
--entry-point <类名>.<方法名> \
--jar <JAR 文件路径>
清单 7-9
部署 Cloud Function
```

在此示例中，函数代码使用 Gradle 打包到 JAR 文件中，然后使用 `gcloud functions deploy` 命令部署到 Cloud Functions。`--runtime java8` 选项指定应使用 Java 8 运行时，`--trigger-http` 选项指定函数应由 HTTP 请求触发。`--entry-point` 选项指定函数入口点的类和方法名称，`--jar` 选项指定包含函数代码和依赖项的 JAR 文件。

通过使用 Cloud Functions，组织可以快速轻松地部署和运行 Java 应用，并具备自动扩缩容和监控功能。这有助于组织专注于构建和增强其应用，而无需担心基础设施管理。此外，组织可以利用 Cloud Functions 提供的事件驱动架构来构建高度可扩展、高效且响应迅速的应用。

Cloud Functions 的另一个特性是支持环境变量，可用于在运行时将配置值传递给函数，如清单 7-10 所示。

```
gcloud functions deploy <函数名称> \
--runtime java8 \
--trigger-http \
--entry-point <类名>.<方法名> \
--jar <JAR 文件路径> \
--set-env-vars <ENV_VAR_NAME>=<ENV_VAR_VALUE>
清单 7-10
通过设置环境变量部署 Cloud Function
```

在清单 7-10 中，`--set-env-vars` 选项用于将名为 `ENV_VAR_NAME` 的环境变量的值设置为 `ENV_VAR_VALUE`。然后，函数代码可以使用 `System.getenv("ENV_VAR_NAME")` 访问此值。

Cloud Functions 还提供了其他一些可以增强 Java 应用部署和管理的功能和选项，包括以下内容：

*   *自定义域名*：将自定义域名与 Cloud Functions 部署关联，使函数可通过品牌化 URL 访问。

*   *HTTPS*：自动使用 HTTPS 保护传入函数的流量。

*   *IAM*：使用细粒度的 IAM 策略控制对 Cloud Functions 部署的访问。

*   *日志记录*：使用 Cloud Logging 监控和调试函数，该服务与其他 Operations Suite 工具集成。

*   *跟踪*：使用 Cloud Trace 识别性能问题并优化函数。

*   *监控*：使用 Cloud Monitoring 监控函数的健康状况和性能。

组织可以使用这些功能和选项，以更高的控制力和灵活性在 Cloud Functions 上部署和管理 Java 应用，确保为其函数提供一个健壮且可扩展的环境。



在 Cloud Functions 上部署 Java 应用的另一个重要方面是测试与调试。要在本地测试函数，你可以使用 `gcloud` 命令行工具或 Cloud Functions 模拟器，后者提供了一个模拟 Cloud Functions 环境的本地环境。这让你可以在将函数部署到云端之前，对其进行测试和调试。

例如，要使用 `gcloud` 命令行工具在本地测试函数，请参见清单 7-11。

```
gcloud functions call 
清单 7-11
使用 gcloud 命令触发 Cloud Function
```

在清单 7-11 中，`gcloud functions call` 命令用于在本地触发函数。命令行输出将显示函数执行的结果。

另一种选择是使用本地 IDE（例如 IntelliJ 或 Eclipse）来调试函数代码。你可以使用远程调试器连接到云端正在运行的函数，并单步执行代码、设置断点以及检查变量。这可以帮助你更高效地识别和解决问题。

最后，建议为你的函数实现自动化测试，以确保它们按预期运行，即使在代码发生更改后也是如此。你可以使用测试框架（例如 JUnit）为你的函数编写测试用例，并将其作为持续集成和部署流水线的一部分自动运行。

总之，在 Cloud Functions 上部署 Java 应用可以为组织带来多项好处，包括自动扩缩、监控以及底层基础设施的自动管理。通过遵循测试、调试和部署函数的最佳实践，组织可以为其在 Cloud Functions 上的 Java 应用确保一个健壮且可扩展的环境。

## 在 GCP 上扩缩 Java 应用

在 Google Cloud Platform 上扩缩 Java 应用涉及增加应用基础设施的容量以应对需求的增长。你可以通过使用各种 GCP 服务和工具来实现这一点，包括以下内容：

*   *Google Kubernetes Engine*：你可以使用 GKE 来运行和管理托管应用容器的集群。你可以通过增加集群中应用容器的副本数量来扩缩应用。

*   *Google App Engine*：你可以使用 Google App Engine 在托管环境中托管你的应用。App Engine 会自动为你管理扩缩和资源，因此你无需担心管理基础设施。

*   *Google Compute Engine*：你可以使用 Google Compute Engine 托管运行应用的虚拟机。你可以根据需要添加或移除虚拟机来扩缩应用。

*   *Google Cloud Load Balancing*：你可以使用 Google Cloud Load Balancing 将传入流量分发到运行应用的多个虚拟机或容器。这有助于通过将负载均匀分布到多个资源上来提高应用性能和可靠性。

*   *自动扩缩策略*：你可以使用自动扩缩策略，根据性能指标（例如 CPU 利用率或传入流量）自动调整应用基础设施中的资源数量（例如虚拟机或容器）。

在 GCP 上为你的 Java 应用选择扩缩方案时，应考虑应用的具体要求，例如性能、可靠性、成本和管理便捷性。

### 扩缩应用的步骤

在 Google Cloud Platform 上扩缩 Java 应用涉及增加基础设施的容量以应对需求的增长。以下是在 GCP 上扩缩 Java 应用的一些详细步骤和示例：

*   *监控应用性能*：你应该使用各种 GCP 工具和服务（例如 Cloud Monitoring、Cloud Trace 或 Cloud Logging）来监控 Java 应用的性能。你可以使用这些工具来监控应用的资源使用情况、性能和错误率。

*   *确定需要额外容量的领域*：根据监控数据，你应该确定需要额外容量的领域，例如 CPU 利用率增加、传入流量增加或错误率增加。

*   *选择扩缩方案*：根据你的具体要求和 Java 应用的特点，你可以选择一种或多种扩缩方案，例如 Google Kubernetes Engine、Google App Engine、Google Compute Engine 或 Google Cloud Load Balancing。

### 在 Google Kubernetes Engine 上扩缩 Java 应用

你可以使用 `gcloud` CLI 或 GCP 控制台来创建 GKE 集群。你可以指定每个节点的节点数和机器类型，如清单 7-12 所示。

```
gcloud container clusters create my-cluster --num-nodes=3 --machine-type=n1-standard-1
清单 7-12
使用 cloud 命令创建 Kubernetes 集群
```

要将 Java 应用部署到 GKE 集群，你可以使用 Dockerfile 构建 Java 应用的容器镜像，然后使用 `kubectl` CLI 将容器镜像部署到 GKE 集群，如清单 7-13 所示。

```
kubectl apply -f deployment.yaml
清单 7-13
将容器镜像部署到 Kubernetes 集群
```

要创建水平 Pod 自动扩缩器 (HPA)，你可以使用 `kubectl` CLI 创建一个 HPA，该 HPA 根据 CPU 利用率自动调整应用容器的副本数量，如清单 7-14 所示。

```
apiVersion: autoscaling/v2beta1
kind: HorizontalPodAutoscaler
metadata:
name: my-app-hpa
spec:
scaleTargetRef:
apiVersion: apps/v1
kind: Deployment
name: my-app
minReplicas: 1
maxReplicas: 10
metrics:
- type: Resource
resource:
name: cpu
targetAverageUtilization: 50
清单 7-14
Kubernetes 自动扩缩
```

### 在 App Engine 上扩缩 Java 应用

你可以使用 `gcloud` CLI 或 GCP 控制台将你的 Java 应用部署到 App Engine，如清单 7-15 所示。你可以为你的应用指定扩缩类型（自动或手动）和实例类。

```
gcloud app deploy
清单 7-15
部署应用
```

你可以使用 `app.yaml` 配置文件为你的 App Engine 应用启用自动扩缩。你可以指定自动扩缩的最小和最大实例数以及 CPU 利用率目标，如清单 7-16 所示。

`app.yaml` 文件应放置在应用的根目录中。当你将应用部署到 App Engine 时，`app.yaml` 文件用于配置各种设置，例如运行时环境、扩缩设置和其他应用特定设置。

```
runtime: java
instance_class: F4
automatic_scaling:
min_num_instances: 1
max_num_instances: 10
target_cpu_utilization: 0.5
清单 7-16
自动扩缩
```



### 在 Compute Engine 上扩展 Java 应用

你可以使用 `gcloud` CLI 或 GCP 控制台来创建 Compute Engine 实例，如代码清单 7-17 所示。你可以为实例指定机器类型、磁盘大小和其他配置参数。

```
gcloud compute instances create my-instance --machine-type=n1-standard-1
代码清单 7-17
gcloud CLI 创建实例命令
```

你可以使用 Google Cloud Load Balancer 将传入流量分发到多个 Compute Engine 实例。你可以从不同类型的负载均衡器中进行选择，例如网络负载均衡器、HTTP(S) 负载均衡器或 TCP 负载均衡器，如代码清单 7-18 所示。

*   *自动化创建和删除 Compute Engine 实例*：你可以使用 Google Cloud Deployment Manager 或 Terraform 来自动化创建和删除 Compute Engine 实例。你可以在 YAML 或 Terraform 文件中定义所需的配置，并使用脚本根据需要创建或删除实例。

*   *使用场景*：在 GCP 上扩展 Java 应用可用于各种使用场景，例如电子商务、内容分发、社交媒体或游戏。例如，如果你运营一个电子商务网站，你可以使用 App Engine 或 Compute Engine 在流量高峰期扩展你的网站，并使用 Kubernetes 来管理应用容器的部署和扩展。

```
gcloud compute firewall-rules create allow-http --allow tcp:80
gcloud compute target-pools create my-target-pool
gcloud compute target-pools add-instances my-target-pool --instances=my-instance-1,my-instance-2
gcloud compute forwarding-rules create http-load-balancer --port-range=80 \
--target-pool=my-target-pool --region=us-central1
代码清单 7-18
设置虚拟机
```

在 GCP 上扩展 Java 应用涉及监控应用性能、选择扩展解决方案以及自动化扩展过程。具体的步骤和配置将根据你的具体需求以及 Java 应用的特点而有所不同。

### 监控性能与自动扩缩

要监控 Java 应用在 GCP 上的性能，你可以使用 Cloud Monitoring、Cloud Logging 和 Cloud Trace 等工具。这些工具允许你实时监控应用的资源利用率、日志数据和请求追踪。

要选择扩展解决方案，你需要考虑应用类型、预期的流量模式以及所需的定制化程度等因素。例如，如果你有一个流量模式可预测的简单 Web 应用，你可以使用 App Engine 的标准环境或灵活环境。如果你有一个需要更多定制化的更复杂的应用，你可以使用 Compute Engine 或 Kubernetes Engine。

一旦选择了扩展解决方案，你就可以使用 Google Cloud Deployment Manager 或 Terraform 等自动化工具来自动化扩展过程。例如，你可以使用 Deployment Manager 根据需要创建和删除 Compute Engine 实例，或者使用 Terraform 来定义和管理应用的底层基础设施。

此外，你还可以使用 Kubernetes Engine 或 App Engine 等工具来管理容器的部署和扩展。这些工具允许你以可扩展且高效的方式部署和管理容器。

总的来说，在 GCP 上扩展 Java 应用需要结合监控、解决方案选择和自动化。有了这些工具和最佳实践，你可以确保你的 Java 应用能够轻松应对不断增长的流量和变化的需求。

### 无服务器环境中的扩展

在无服务器架构（例如 Google Cloud Functions）中，扩展由平台自动管理。当你的函数收到新请求时，平台会创建一个新的函数实例来处理该请求。当请求完成后，该实例会被终止。由于每个函数执行都是独立的，不依赖于之前的执行或外部状态，因此云函数应该是无状态的。

平台会根据请求数量自动管理实例数量。如果请求数量增加，平台会创建更多实例来处理负载。如果请求数量减少，平台会终止一些实例以节省资源。

这意味着你无需担心手动扩展无服务器应用。平台会为你处理这一切，提供必要的资源来处理传入的请求，并确保你的函数具有高可用性和可扩展性。

此外，你可以为函数设置并发限制，以控制在任何给定时间可以创建的最大实例数量。这可以帮助你避免后端资源过载，并保持应用的性能。

## 本章小结

在本章中，你学习了如何在 GCP 上部署和扩展 Java 应用。你了解了可用于扩展 Java 应用的不同 GCP 服务和工具，例如 GKE、Google App Engine 和 Google Compute Engine。本章还涵盖了监控应用性能、识别需要额外容量的区域，以及根据应用的特定需求选择扩展解决方案。你还学习了无服务器架构（例如 Google Cloud Functions）中的自动扩展。最后，本章涵盖了部署在 GCP 上的 Java 应用的测试和调试最佳实践。总体而言，本章全面概述了如何在 GCP 上部署和扩展 Java 应用。



