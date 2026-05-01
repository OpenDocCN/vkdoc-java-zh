# 在 MicroProfile 中使用 JSON Web Token 传播

**JSON Web Token**（**JWT**）是一种承载安全信息的通用格式，被许多不同的基于 Web 的安全协议所使用。然而，关于 JWT 的具体内容以及签名 JWT 所使用的安全算法，缺乏标准化。**MicroProfile JWT**（**MP-JWT**）传播项目规范研究了基于 **OpenID Connect**（**OIDC**）的 JWT 规范（[`openid.net/connect/`](http://openid.net/connect/)）（[`tools.ietf.org/html/rfc7519`](https://tools.ietf.org/html/rfc7519)），并在此基础上定义了一套需求，以促进 JWT 在基于 MicroProfile 的微服务中的互操作性，同时提供了从 JWT 访问信息的 API。

关于 OIDC 和 JWT 如何工作，包括应用程序/微服务如何拦截 bearer 令牌的描述，请参考 [`openid.net/connect/`](http://openid.net/connect/) 上的 *Basic Client Implementer's Guide*。

在本节中，您将了解以下内容：

*   OIDC 和 JWT 规范中为实现互操作性所需的声明和签名算法
*   使用 JWT 对微服务端点进行**基于角色的访问控制**（**RBAC**）
*   如何使用 MP-JWT API 访问 JWT 及其声明值

