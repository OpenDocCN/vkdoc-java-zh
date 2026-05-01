# Docker Shell 命令

提供预配置服务器/服务的一种常见方式是使用包含该服务及其所有依赖项的 Docker 镜像。在此示例中，我们使用 Docker 来运行 KeyCloak 和 Jaeger 镜像。如果您不熟悉 Docker 或未安装 `docker` 命令，请参阅如何为您的平台安装 Docker 的说明 ([`docs.docker.com/v17.12/install/`](https://docs.docker.com/v17.12/install/))。

此项目依赖 KeyCloak 来生成 MP-JWT 令牌。要在 Docker 容器中启动 KeyCloak，请在您的 Shell 中运行以下命令：

```
docker run -d --name keycloak -e KEYCLOAK_USER=admin -e KEYCLOAK_PASSWORD=admin -p 8180:8180 -v `pwd`/packt-mp-realm.json:/config/quarkus-packt.json -it jboss/keycloak:6.0.1 -b 0.0.0.0 ...
```

