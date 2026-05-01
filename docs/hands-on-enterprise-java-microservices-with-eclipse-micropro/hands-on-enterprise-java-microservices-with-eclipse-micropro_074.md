# 配置 JWT 的身份验证

要接受 JWT 作为应被认证并因此受信任的身份标识，我们需要配置 MP-JWT 功能，提供用于验证 JWT 签名者和签发者的信息。这通过 MP-Config 属性完成：

*   `mp.jwt.verify.publickey`：提供 MP-JWT 签名者公钥的内嵌密钥材料，通常采用 PKCS8 PEM 格式。
*   `mp.jwt.verify.issuer`：指定 JWT 中 `iss` 声明的预期值。

本书的一个示例 `microprofile-configuration.properties` 文件如下：

```
# MP-JWT Configmp.jwt.verify.publickey=MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAlivFI8qB4D0y2jy0CfEqFyy46R0o7S8TKpsx5xbHKoU1VWg6QkQm+ntyIv1p4kE1sPEQO73+HY8+Bzs75XwRTYL1BmR1w8J5hmjVWjc6R2BTBGAYRPFRhor3kpM6ni2SPmNNhurEAHw7TaqszP5eUF/F9+KEBWkwVta+PZ37bwqSE4sCb1soZFrVz/UT/LF4tYpuVYt3YbqToZ3pZOZ9AX2o1GCG3xwOjkc4x0W7ezbQZdC9iftPxVHR8irOijJRRjcPDtA6vPKpzLl6CyYnsIYPd99ltwxTHjr3npfv/3Lw50bAkbT4HeLFxTx4flEoZLKO/g0bAoV2uqBhkA9xnQIDAQAB ...
```



