# Payara Micro

Payara 是开源项目 Payara Micro 的赞助商，该项目实现了 Eclipse MicroProfile 规范。Payara Server 基于开源应用服务器 GlassFish。Payara Micro 基于 Payara Server，但它是其精简版。正如其网站所述，*Payara Micro 是 Payara Server 的微服务就绪版本*。

Payara Micro 的工作方式是：先启动一个 Payara Micro 实例，然后将你的 MicroProfile 微服务作为 WAR 文件部署到该实例上。例如，要启动一个 Payara Micro 实例，你需要输入以下命令：

```
$ java -jar payara-micro.jar
```

要启动一个 Payara Micro 实例并将你的应用程序部署到其上，你需要输入以下命令：

```
$ java -jar payara-micro.jar --deploy <WAR 文件>
```

Payara Micro 支持 Java EE 应用程序部署，并且与 Eclipse MicroProfile 兼容。

关于 Payara Micro 的文档，请参考 [`docs.payara.fish/documentation/payara-micro/payara-micro.html`](https://docs.payara.fish/documentation/payara-micro/payara-micro.html)。

最后，Payara Micro 通过使用第三方内存数据网格产品支持自动集群。

