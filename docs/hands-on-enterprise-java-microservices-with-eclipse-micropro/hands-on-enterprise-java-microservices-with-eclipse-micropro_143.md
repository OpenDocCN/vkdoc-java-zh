# Launcher

富士通是开源项目 Launcher 的赞助商，该项目实现了 Eclipse MicroProfile 规范。Launcher 利用了内嵌的 GlassFish 服务器和 Apache Geronimo MicroProfile API 实现。你可以将微服务作为 WAR 文件运行，如下所示：

```
$ java -jar launcher-1.0.jar --deploy my-app.war
```

此外，Launcher 可以创建 uberjar。要将微服务创建并运行为 uberjar，首先需要生成 uberjar，然后使用 `java -jar` 调用它，如下所示：

```
$ java -jar launcher-1.0.jar --deploy my-app.war --generate my-uber.jar$ java -jar my-uber.jar
```

Launcher 的文档非常稀少且有限。你可以在 [`github.com/fujitsu/launcher/blob/master/doc/Usage.adoc ...`](https://github.com/fujitsu/launcher/blob/master/doc/Usage.adoc) 找到关于 Launcher 的使用信息。



