# 技术要求

本章需要以下环境：

*   一个集成开发环境（IDE）
*   已安装 JDK 1.8+，并正确配置了 `JAVA_HOME`
*   Apache Maven 3.5.3+
*   一个正在运行的 Docker 环境

本章的代码可在以下地址找到：[`github.com/PacktPublishing/Hands-On-Enterprise-Java-Microservices-with-Eclipse-MicroProfile/tree/master/Chapter08-mpcodesample`](https://github.com/PacktPublishing/Hands-On-Enterprise-Java-Microservices-with-Eclipse-MicroProfile/tree/master/Chapter08-mpcodesample)

本章的示例可以使用 Quarkus 集成的 GraalVM ([`github.com/oracle/graal/releases/tag/vm-1.0.0-rc16`](https://github.com/oracle/graal/releases/tag/vm-1.0.0-rc16)) 编译成本地二进制文件。这需要额外安装 1.0-RC16 版本的 Graal VM 和一个可用的 C 语言开发环境。关于生成本地镜像的详细要求，请参考 [`quarkus.io/guides/building-native-image-guide`](https://quarkus.io/guides/building-native-image-guide)。

