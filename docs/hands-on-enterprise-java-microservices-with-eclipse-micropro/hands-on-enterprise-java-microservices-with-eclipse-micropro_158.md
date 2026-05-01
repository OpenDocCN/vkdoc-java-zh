# Svcs1 Shell 命令

接下来，在您之前要求打开的 Svcs1 终端窗口中，导航到项目的 `svcs1` 子目录，然后运行以下命令以开发模式执行 `svcs1` 镜像：

```
mvn compile quarkus:dev
```

您将看到以下输出：

```
Scotts-iMacPro:svcs1 starksm$ mvn compile quarkus:dev
[INFO] Scanning for projects...
...
20:56:27 INFO [io.quarkus]] (main) Quarkus 0.15.0 started in 2.492s. Listening on: http://[::]:8081
20:56:27 INFO [io.quarkus]] (main) Installed features: [cdi, jaeger, resteasy, resteasy-jsonb, security, smallrye-fault-tolerance, smallrye-health, smallrye-jwt, smallrye-metrics, smallrye-openapi, smallrye-opentracing, smallrye-rest-client, swagger-ui]
```

在输出中，我们看到此实例正在监听 `8081` 端口的 HTTP 请求，并且我们看到已安装的各种 Quarkus 特性，以支持我们对 MicroProfile 特性的使用。

