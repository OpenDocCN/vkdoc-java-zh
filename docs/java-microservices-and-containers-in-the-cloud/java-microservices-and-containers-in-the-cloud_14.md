# jibGradle: {}
jibMaven: {}
deploy:
kubectl:
manifests:
- k8s/*.yml
清单 13-4
Skaffold 描述符 (ch13/ch13-01/skaffold.yaml)
```

这里解释几个描述符字段：

*   `build.local.push`: False 强制 Skaffold 使用 `jibDockerBuild`。

*   `build.artifacts` 是你要构建的镜像列表。

*   `build.artifacts.jibMaven` 配置 Skaffold 在镜像构建阶段使用 `jib`，类型为 `maven`，因为你使用 Maven 作为构建工具。

*   `deploy.kubectl.manifests` 设置存放 Kubernetes 清单文件的文件夹名称。如果省略此属性，默认目录名称为 `k8s`。

你在这个开发环境中几乎完成了 CI/CD 管道的设置。

### 构建并运行微服务

`ch13\ch13-01` 文件夹包含构建和运行示例所需的 Maven 脚本。你将使用 `mvn clean compile jib:build` 命令进行构建，如清单 13-5 所示。

```
(base) binildass-MacBook-Pro:ch13-01 binil$ pwd
/Users/binil/binil/code/mac/mybooks/docker-04/Code/ch13/ch13-01
(base) binildass-MacBook-Pro:ch13-01 binil$ sh build.sh
[INFO] Scanning for projects...
[INFO]
[INFO] --
[INFO] Building Spring Boot μS 0.0.1-SNAPSHOT
[INFO] ---------------------[ jar ]---------------------
[INFO]
...
[INFO]
[INFO] Built and pushed image as binildas/spring-boot-docker-k8s-helm
[INFO] Executing tasks:
[INFO] [============================  ] 91.7% complete
[INFO] > launching layer pushers
[INFO]
[INFO] -------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] --------------------------------------------------
[INFO] Total time:  33.855 s
[INFO] Finished at: 2023-05-23T15:43:14+05:30
[INFO] --------------------------------------------------
(base) binildass-MacBook-Pro:ch13-01 binil$
清单 13-5
使用脚本构建微服务
```

此构建会将镜像推送到 Docker Hub。当你执行 Skaffold 的 `dev` 模式时，它会构建应用程序、创建镜像、部署到你的本地集群，并持续监控你的源代码以查看是否有更改。当有更改时，它会在代码更改后重复相同的流程，以便你拥有用于测试和调试的全新部署。

在触发任何代码更改之前，首先作为下一步运行微服务。请注意，你将使用 `skaffold` 命令来运行微服务。参见清单 13-6。

```
(base) binildass-MacBook-Pro:ch13-01 binil$ pwd
/Users/binil/binil/code/mac/mybooks/docker-04/Code/ch13/ch13-01
(base) binildass-MacBook-Pro:ch13-01 binil$ eval $(minikube docker-env)
(base) binildass-MacBook-Pro:ch13-01 binil$ skaffold dev --trigger notify -v debug
DEBU[0000] skaffold API not starting as it's not requested  subtask=-1 task=DevLoop
...
[INFO]
[INFO] Built image to Docker daemon as binildas/spring-boot-docker-k8s-helm
[INFO]
[INFO] -------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] -------------------------------------------------------
...
Press Ctrl+C to exit
DEBU[0017] Change detected      subtask=-1 task=DevLoop
Watching for changes...
...
[springboothelm]
[springboothelm]   .   ____          _            __ _ _
[springboothelm]  /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
[springboothelm] ( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
[springboothelm]  \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
[springboothelm]   '  |____| .__|_| |_|_| |_\__, | / / / /
[springboothelm]  =========|_|==============|___/=/_/_/_/
[springboothelm]  :: Spring Boot ::                (v3.2.0)
[springboothelm]
...
[springboothelm] 2023-06-01 12:55:42 INFO  StartupInfoLogger.logStarted:57 - Started Application ...
[springboothelm] 2023-06-01 12:55:42 INFO  Application.main:51 - Started...
...
清单 13-6
使用 Skaffold 运行微服务
```

这将在你对项目进行每次更改时构建镜像并将其部署到 Minikube。



### 测试微服务

微服务启动后，你可以通过浏览器访问该 URL 来使用 Web 应用程序，该 URL 可在代码清单 13-7 中找到。

```
(base) binildass-MacBook-Pro:~ binil$ minikube service springboothelm --url
http://192.168.64.6:31831
(base) binildass-MacBook-Pro:~ binil$
代码清单 13-7
查找服务 URL
```

你可以使用此 URL 在浏览器中进行测试，或者使用 cURL 进行测试，如代码清单 13-8 所示。

```
(base) binildass-MacBook-Pro:~ binil$ curl http://192.168.64.6:31831
Hello Docker World : Counted 1 times by App Version: 1
(base) binildass-MacBook-Pro:~ binil$
代码清单 13-8
使用 cURL 测试微服务
```

相应的日志也会在微服务终端窗口中可见，如代码清单 13-9 所示。

```
[springboothelm]
[springboothelm]   .   ____          _            __ _ _
[springboothelm]  /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
[springboothelm] ( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
[springboothelm]  \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
[springboothelm]   '  |____| .__|_| |_|_| |_\__, | / / / /
[springboothelm]  =========|_|==============|___/=/_/_/_/
[springboothelm]  :: Spring Boot ::                (v3.2.0)
[springboothelm]
...
[springboothelm] 2023-06-01 12:55:42 INFO  StartupInfoLogger.logStarted:57 - Started Application ...
[springboothelm] 2023-06-01 12:55:42 INFO  Application.main:51 - Started...
...
[springboothelm] 2023-06-01 12:59:50 INFO  Application.home:41 - Start
[springboothelm] 2023-06-01 12:59:50 DEBUG Application.home:43 - Inside hello.Application.home() : Counted 1 times by App Version: 1
[springboothelm] 2023-06-01 12:59:50 INFO  Application.home:44 - Returning...
...
代码清单 13-9
微服务终端窗口日志

现在，你将对该项目的源代码进行一些更改。在进行更改时，请持续观察终端窗口。进行如下更改（见代码清单 13-10）：

```
[springboothelm]
[springboothelm]   .   ____          _            __ _ _
[springboothelm]  /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
[springboothelm] ( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
[springboothelm]  \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
[springboothelm]   '  |____| .__|_| |_|_| |_\__, | / / / /
[springboothelm]  =========|_|==============|___/=/_/_/_/
[springboothelm]  :: Spring Boot ::                (v3.0.6)
[springboothelm]
...
[springboothelm] 2023-06-01 13:01:23 INFO  Application.main:51 - Started...
代码清单 13-10
重新部署时的微服务终端窗口日志
```

```
private static String appVersion = "2";
```

任何微小的更改都会被 Skaffold 捕获，并自动触发 Docker 镜像的新构建、创建新的 Pod，然后重新部署应用程序。

你可以再次使用 cURL 测试应用程序，如代码清单 13-11 所示。

```
(base) binildass-MacBook-Pro:~ binil$ curl http://192.168.64.6:31831
Hello Docker World : Counted 1 times by App Version: 2
(base) binildass-MacBook-Pro:~ binil$
代码清单 13-11
使用 cURL 测试微服务
```

相应的日志也会在终端窗口中可见，如代码清单 13-12 所示。

```
[springboothelm]
[springboothelm]   .   ____          _            __ _ _
[springboothelm]  /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
[springboothelm] ( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
[springboothelm]  \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
[springboothelm]   '  |____| .__|_| |_|_| |_\__, | / / / /
[springboothelm]  =========|_|==============|___/=/_/_/_/
[springboothelm]  :: Spring Boot ::                (v3.0.6)
[springboothelm]
...
[springboothelm] 2023-06-01 13:01:23 INFO  Application.main:51 - Started...
[springboothelm] 2023-06-01 13:04:58 INFO  Application.home:41 - Start
[springboothelm] 2023-06-01 13:04:58 DEBUG Application.home:43 - Inside hello.Application.home() : Counted 1 times by App Version: 2
[springboothelm] 2023-06-01 13:04:58 INFO  Application.home:44 - Returning...
代码清单 13-12
重新部署后的微服务终端窗口日志
```

你可以清晰地看到在源代码中所做的更改（`appVersion = "2"`）。甚至 `private static volatile long times = 0L` 计数器也已重置，因为新容器重新加载了类二进制文件。参见代码清单 13-13。

你可以通过在终端窗口中按 Ctrl+C 来停止应用程序。

```
^CCleaning up...
...
DEBU[0972] Running command: [kubectl --context minikube delete --ignore-not-found=true -f -]
- deployment.apps "springboothelm" deleted
- service "springboothelm" deleted
INFO[0972] Cleanup complete in 273.772602ms
(base) binildass-MacBook-Pro:ch13-01 binil$
代码清单 13-13
终止微服务应用程序
```

至此，微服务的简单 CI/CD 管道示例就完成了。

## 总结

更快的发布周期是微服务架构的主要优势之一。然而，如果没有良好的 CI/CD 流程，你将无法实现微服务所承诺的敏捷性。

> *“我们努力创建不超过两个比萨饼就能喂饱的团队。我们称之为‘两个比萨饼团队规则’。”*
> 
> ——贝佐斯

当这成为常态时，拥有强大的 CI/CD 流程就变得更加重要，因为你将有多个团队在各自独立的微服务仓库中工作。你已经看到了一个演示这些概念的示例。尽管示例和管道很简单，但它们达到了介绍这些概念的目的。在了解了微服务和容器的不同方面之后，你现在已经准备好进入公有云领域，这将在下一章中进行。

