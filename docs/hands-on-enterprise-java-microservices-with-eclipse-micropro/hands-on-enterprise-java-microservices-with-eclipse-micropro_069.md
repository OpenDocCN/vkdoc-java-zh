# 必需的 MP-JWT 声明

实现需要支持的必需 MP-JWT 声明集包括以下内容：

*   `typ`：此头部参数标识令牌类型，必须为 `JWT`。
*   `alg`：此头部算法用于签名 JWT，必须指定为 `RS256`。
*   `kid`：此头部参数提示用于签名 JWT 的公钥。
*   `iss`：令牌的颁发者和签名者。
*   `sub`：标识 JWT 的主体。
*   `exp`：标识过期时间，在此时间或之后，JWT 不得被接受处理。
*   `iat`：标识 JWT 的颁发时间，可用于确定 JWT 的时效。
*   `jti`：为 JWT 提供唯一标识符。
*   `upn`：此 MP-JWT 自定义声明是指定用户主体名称的首选方式。
*   `groups`：此 MP-JWT 自定义声明是分配给 JWT 主体的组或角色名称列表。

`exp`、`iat` 和其他与日期相关的声明所使用的 `NumericDate` 是一个 JSON 数值，表示从 `1970-01-01T00:00:00Z` UTC 到指定 UTC 日期/时间的秒数，忽略闰秒。此外，关于标准声明的更多详细信息可以在 MP-JWT 规范（[`github.com/eclipse/microprofile-jwt-auth/releases/tag/1.1.1`](https://github.com/eclipse/microprofile-jwt-auth/releases/tag/1.1.1)）和 JSON Web Token RFC（[`tools.ietf.org/html/rfc7519`](https://tools.ietf.org/html/rfc7519)）中找到。

一个 JSON 格式的基本 MP-JWT 示例，即 MP-JWT 兼容 JWT 的示例头部和负载，如下所示：

```
{
    "typ": "JWT",
    "alg": "RS256",
    "kid": "abc-1234567890"
}
{
    "iss": "https://server.example.com",
    "jti": "a-123",
    "exp": 1311281970,
    "iat": 1311280970,
    "sub": "24400320",
    "upn": "jdoe@server.example.com",
    "groups": ["red-group", "green-group", "admin-group", "admin"],
}
{
*** base64 signature not shown ***
}
```

此示例显示了头部，其中包含 `typ=JWT`、`alg=RS256` 和 `kid=abc-1234567890`。主体包含 `iss`、`jti`、`exp`、`iat`、`sub`、`upn` 和 `groups` 声明。

