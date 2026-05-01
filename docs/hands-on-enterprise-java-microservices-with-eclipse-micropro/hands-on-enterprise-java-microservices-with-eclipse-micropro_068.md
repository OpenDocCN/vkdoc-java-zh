# 互操作性建议

MP-JWT 作为令牌格式的最大效用取决于身份提供商和服务提供商之间的协议。这意味着负责颁发令牌的身份提供商应能够使用 MP-JWT 格式颁发令牌，以便服务提供商能够理解并检查令牌，从而收集关于主体的信息。MP-JWT 的主要目标如下：

*   它应可用作身份验证令牌。
*   它应可用作授权令牌，其中包含通过组声明间接授予的应用程序级角色。
*   它可以支持 IANA JWT 分配（[`www.iana.org/assignments/jwt/jwt.xhtml`](https://www.iana.org/assignments/jwt/jwt.xhtml)）中描述的其他标准声明，以及非标准声明...

