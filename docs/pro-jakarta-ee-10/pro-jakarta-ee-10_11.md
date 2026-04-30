# 如果你需要指定特定的 Java 版本：
JAVA_HOME=/path/to/the/sdk ./gradlew build war
```

你也可以在 Eclipse 的 Gradle Tasks 视图中调用 `war` 任务。

部署和运行微服务

要部署 WAR 文件，请将其从项目复制到 TomEE 服务器：

```
cp /build/libs/.war \
/webapps
```

请将大写标识符替换为合适的值。

然后，要启动启用了 MicroProfile 的 TomEE 服务器，请输入以下代码，同样替换你的路径：

```
cd 
bin/startup.sh

