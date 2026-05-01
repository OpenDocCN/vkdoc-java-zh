# Svcs2 Shell 命令

接下来，在您之前要求打开的 Svcs2 终端窗口中，`cd` 到项目的 `svcs2` 子目录，然后运行以下命令来构建 `svcs2` 镜像：

```
mvn clean package
```

构建完成后，要运行 `svcs2` JAR，请输入以下命令：

```
 java -jar target/sample-svcs2-runner.jar
```

您将得到以下输出：

```
Scotts-iMacPro:svcs2 starksm$ java -jar target/sample-svcs2-runner.jar...20:58:55 INFO [io.quarkus]] (main) Quarkus 0.15.0 started in 0.936s. Listening on: http://[::]:808220:58:55 INFO [io.quarkus]] (main) Installed features: [cdi, jaeger, resteasy, resteasy-jsonb, security, smallrye-health, smallrye-jwt, smallrye-metrics, smallrye-opentracing, smallrye-rest-client]
```

在这里，我们 ...

