# 11. 使用 JWT 的安全性

安全性是应用程序开发最重要的方面之一。安全性方面有太多东西可能迅速出错。作为一个快速发展的领域，新的威胁、漏洞利用和入侵事件层出不穷。从最大的面向公众的应用程序到最小的应用程序，每个应用程序都面临安全漏洞的风险。

应用程序安全是一个复杂的领域，应由安全专家处理。本章讨论如何使用 Eclipse MicroProfile JWT Propagation API 来保护 RESTful Web 服务。本章**不是**关于安全性的“如何做”。而是关于在安全性已就位的情况下，本章的目标是展示如何使用 MicroProfile JWT API 来利用安全基础设施保护 REST 资源。

作为应用程序开发者，我们（作者）将整个流程的管理委托给安全专家。因此，本章使用 Keycloak^(¹¹⁵) 作为安全框架，MicroProfile JWT 可用于保护 REST 客户端。Keycloak 是一个开源的身份和访问管理（IAM）框架。它提供用户联合、强身份验证、用户管理、细粒度授权等功能。本章将创建用户的过程委托给 Keycloak，并专注于使用 JWT 来保护 Web 资源。



## 基于令牌的身份验证

RESTful Web 服务本质上是无状态的。这意味着每个请求都独立于前一个请求。服务器需要在每个请求中提供相同的安全相关信息。实现这种无状态安全形式的最有效方法是使用令牌。令牌是一个长字符串，可以对其进行验证，以确认调用客户端声明的真实性。它允许系统对客户端进行身份验证、授权并验证其身份。基于令牌的安全性不依赖于 HTTP 会话，因此具有更好的可扩展性、性能和可靠性。

令牌通常被添加到请求头中。然后，服务会获取该令牌并进行验证，通过内省来提取有关调用客户端的信息，进而创建一个安全上下文，该上下文可以传播到受保护服务的所有部分。MicroProfile JWT API 是一种基于令牌的身份验证、授权和基于角色的访问控制（RBAC）机制，可用于基于令牌的应用程序安全。JSON Web Token（JWT）是一种基于令牌的标准^(¹¹⁶)，它已成为创建轻量级安全令牌最流行的标准。

JWT 令牌通常会被签名，以便服务可以验证该令牌。令牌中包含的信息称为声明（claims）。声明也可以被加密，这样它们就不会以明文形式在请求头中传递。一个典型的 JWT 由头部（header）、声明（claims，或称为主体 body）和签名（signature）组成。

### 头部

JWT 的头部通常包含用于签名 JWT 的算法类型（RS256、ES256、HS256 等）、令牌类型（例如 JWT）、密钥 ID 以及取决于令牌签发者的其他元数据。对 JWT 进行签名仅能使接收服务验证信息未被篡改。它并不会使令牌不可读。为了实现不可读，除了签名之外，令牌还需要被加密。

### 声明（或主体）

JWT 的声明或主体是客户端希望传递给服务器的所有信息。很多时候，主体包含分配给特定客户端的角色。角色决定了客户端在应用程序中可以执行不同操作的范围。

### 签名

签名是可选的，但强烈建议包含，它允许服务验证令牌在传输过程中是否未被更改或篡改。

JWT 是头部、声明和签名信息的 base64 编码字符串。清单 11-1 展示了一个 JWT 令牌示例。

```
eyJhbGciOiJSUzI1NiIsInR5cCIgOiAiSldUIiwia2lkIiA6ICJEMkdxYmxJU3YzY1Jpdmh2ZFR0YmpaVDg2VkFGeUFpT29wZGlvZHYtQllJIn0.eyJleHAiOjE2NjE2MzY5MDksImlhdCI6MTY2MTYzNjYwOSwianRpIjoiOTAxOGVlZmEtZDc2Ni00ODI4LTgwMmUtYjFkNjMwOTQ5ODUxIiwiaXNzIjoiaHR0cDovL2xvY2FsaG9zdDo1MDUwL3JlYWxtcy9qd2FsbGV0IiwiYXVkIjoiYWNjb3VudCIsInN1YiI6ImY2OTU0YTRkLTFmZmEtNGNkMC05YmRhLTM4NmVkOWQwNzQ3ZCIsInR5cCI6IkJlYXJlciIsImF6cCI6Imp3YWxsZXQtc2VydmljZSIsInNlc3Npb25fc3RhdGUiOiJlOTY1ZWQ3YS00OTI3LTQ5MzItODQyOS1jNWNhMjNmYzQ5NzMiLCJhY3IiOiIxIiwicmVhbG1fYWNjZXNzIjp7InJvbGVzIjpbIm9mZmxpbmVfYWNjZXNzIiwiZGVmYXVsdC1yb2xlcy1qd2FsbGV0IiwidGVsbGVyIiwidW1hX2F1dGhvcml6YXRpb24iXX0sInJlc291cmNlX2FjY2VzcyI6eyJhY2NvdW50Ijp7InJvbGVzIjpbIm1hbmFnZS1hY2NvdW50IiwibWFuYWdlLWFjY291bnQtbGlua3MiLCJ2aWV3LXByb2ZpbGUiXX19LCJzY29wZSI6InByb2ZpbGUgZW1haWwiLCJzaWQiOiJlOTY1ZWQ3YS00OTI3LTQ5MzItODQyOS1jNWNhMjNmYzQ5NzMiLCJ1cG4iOiJtYXgiLCJlbWFpbF92ZXJpZmllZCI6ZmFsc2UsImdyb3VwcyI6WyJvZmZsaW5lX2FjY2VzcyIsImRlZmF1bHQtcm9sZXMtandhbGxldCIsInRlbGxlciIsInVtYV9hdXRob3JpemF0aW9uIl0sInByZWZlcnJlZF91c2VybmFtZSI6Im1heCJ9.lG6zEr-BEMQwyEUswIgCM5InWAH4ljAu8JK6Zc0naHD8kcaGAssxsXHzf315n--cysYJzeAQBfDmzsJr_Roe0io2nN4CZ61T6Vz4YnE_5_RRn9n6cl2rG_1oIsLknqKVseNQ4itZZjKfIGVUYxURxKmO6gsCH8FCHN1hu7rmq4jwPC0aO7Ypvjg2IpExsIociprXk7_1iyfllZ4XnlHYBADOpkNOduJgMyojQwBgTz-xa-9sBb-eoKys1gBD3nbV8STNbKAGzOUiQaq28T4x3pIn6JNfw2JbBu6_cxKXhzghoPukboLhn2C_MYJt4OiycxuprsbDRKoxAIjus_9yoA
清单 11-1
JWT 示例
```

清单 11-2 展示了解码后的 JWT。它显示了三个部分，即头部、声明和签名。

```
{
"alg": "RS256",
"typ": "JWT",
"kid": "D2GqblISv3cRivhvdTtbjZT86VAFyAiOopdiodv-BYI"
}
{
"exp": 1661636909,
"iat": 1661636609,
"jti": "9018eefa-d766-4828-802e-b1d630949851",
"iss": "http://localhost:5050/realms/jwallet",
"aud": "account",
"sub": "f6954a4d-1ffa-4cd0-9bda-386ed9d0747d",
"typ": "Bearer",
"azp": "jwallet-service",
"session_state": "e965ed7a-4927-4932-8429-c5ca23fc4973",
"acr": "1",
"realm_access": {
"roles": [
"offline_access",
"default-roles-jwallet",
"teller",
"uma_authorization"
]
},
"resource_access": {
"account": {
"roles": [
"manage-account",
"manage-account-links",
"view-profile"
]
}
},
"scope": "profile email",
"sid": "e965ed7a-4927-4932-8429-c5ca23fc4973",
"upn": "max",
"email_verified": false,
"groups": [
"offline_access",
"default-roles-jwallet",
"teller",
"uma_authorization"
],
"preferred_username": "max"
}
{
"e": "AQAB",
"kty": "RSA",
"n": "3buUZWr0Fp9Rm85GPIAoYfiqNapj2DxW8gjMWOjPEgg4KUqg3tSC6G0kAzPJ70zMmfe-b3CWBQvmNXWIUZCXPlnBuVKyWcowBQk7QRnEkOuw5vL6bYU5I2DA_1CPIn4v3g4ox0GmjOwDflrlb2vlPw76BpHIKd-EUI-mkm-Yv6H60BZmo7iRyVF7TNGVYgIg0xrVFPIvTDV2yvZKO4qG6qYWk2khMPqXm-725QBp2wgWDhowibjuoOCW1S5BCr5qc3N-uaPej7Nbcub80v1XGJ7feHqg4TXtNDCSikTAGCa0geW3eP1wRoE_9QYzKlN45LI8LxhncUl58WpdulgblQ"
}
清单 11-2
解码后的 JWT
```

## MicroProfile JWT

MicroProfile JWT API 提供了一种标准的、通常是约定优于配置的方式来验证 JWT 令牌、授权客户端并将生成的安全上下文传播到应用程序的其余部分。它通过要求 JWT 使用基于 RSA 的数字签名算法进行签名来实现这些目标。JWT 运行时所需要的只是创建 JWT 的授权服务器的公钥，以验证该令牌。它还强制要求在 JWT 的声明中提供某些信息。这些信息如下：

*   `exp` – JWT 的过期时间。这确保了过期的旧令牌被拒绝。
*   `iss` – 令牌的签发者。
*   `iat` – 签发时间，标识令牌何时签发。
*   `upn` – 用户主体名称或当前登录用户的首选用户名。

它还推荐了以下声明：

*   `sub` – 此令牌代表谁？
*   `jti` – 此声明的唯一标识符。
*   `aud` – 标识 JWT 可以访问的 MP JWT 端点。

这些要求使得 Jakarta EE 应用程序能够完全无状态。`sub` 声明将被映射到一个 `java.security.Principal` 实例。`groups` 将用于确定该 Principal 是否具有特定资源方法所需的角色。客户端将在每个请求中发送令牌；JWT 运行时将在每次调用时验证并传播安全上下文。

### MicroProfile JWT 安全流程

MicroProfile JWT 通过将用户的会话信息委托给客户端，允许创建无状态的微服务。客户端的每次调用都包含构建安全上下文所需的所有信息。然后，此安全上下文被插入到现有的 Jakarta EE 基础设施中，该基础设施可供应用程序的所有部分使用。MicroProfile JWT 安全流程包含三个步骤。



#### 认证客户端

MicroProfile JWT 运行时会利用颁发服务器或授权机构提供的公钥，自动验证请求头中传递的 JWT 令牌。在我们的上下文中，这就是 Keycloak 发挥作用的地方。我们使用 Keycloak 作为颁发机构，代表我们颁发 JWT 令牌。

典型的企业应用通常包含两层：客户端或 UI 层（例如使用 Angular 或 Vue 编写）以及服务器或资源层（即本书目前所介绍的部分）。客户端层是服务器或资源层的“用户”或客户端。最终用户通常与 UI 层交互。当最终用户点击 UI 中的按钮（例如进行货币转换）时，UI 会向后端或服务器发送请求。UI 向服务器发送的这个请求将包含 JWT 令牌。但 UI 是如何获取令牌的呢？

当应用像我们这样（并且推荐这样）使用 Keycloak 时，用户点击 UI 进行注册，该用户创建过程将委托给 Keycloak。因此，用户凭证将存储在 Keycloak 中。当用户通过 UI 登录时，UI 会将用户重定向到 Keycloak 进行登录。Keycloak 对用户进行身份验证，然后将用户重定向回 UI。此后，已登录用户执行的任何需要调用后端服务器的操作，UI 都可以向 Keycloak 请求一个 JWT 令牌，该令牌将随已登录用户执行的每个请求一起转发到服务器。

这种架构使后端服务器（在本例中即我们的 Jakarta EE 应用）完全无需了解用户的任何信息。我们的资源只需要一个有效的令牌，而相应的方法将要求当前执行用户拥有特定的角色。所有这些信息都会随每次调用发送到服务器。这样，UI 层和后端服务器层就完全解耦且无状态。它们之间不共享任何状态。服务器所需的每项安全信息都随每个客户端请求一起发送。

捆绑的 Keycloak 中有一个示例用户，用户名为 `max`，密码为 `max`，可用于获取 JWT。清单 11-3 中的示例 cURL 调用可以发送到 Keycloak 的 open-id/connect 端点以获取 JWT 令牌。清单 11-1 中显示的 JWT 就是通过此调用生成的。

```
curl --request POST --url http://localhost:5050/realms/jwallet/protocol/openid-connect/token \
--header 'Content-Type: application/x-www-form-urlencoded' \
--data realm=jwallet \
--data grant_type=password \
--data client_id=jwallet-service \
--data username=max \
--data password=max
清单 11-3
示例 Keycloak 调用
```

##### 提供公钥

MicroProfile JWT 运行时会利用颁发服务器（此处为 Keycloak）的公钥自动验证和认证传递的 JWT。MicroProfile JWT 规范要求提供公钥或用于获取公钥的 URL。该规范定义了两个 MicroProfile Config 属性，用于将颁发机构或服务器的公钥传递给 MicroProfile JWT 运行时。这两个属性是 `mp.jwt.verify.publickey` 和 `mp.jwt.verify.publickey.location`。

第一个属性允许将公钥文本本身作为属性值传递。第二个属性允许指定一个 URL，公钥可以从该 URL 下载。同时指定这两个属性将导致部署异常。`mp.jwt.verify.issuer` 属性也用于验证 `iss`（颁发者）声明的值。JWT 中传递的值必须与属性中传递的值匹配。

清单 11-4 显示了 jwallet 中使用的公钥和颁发者属性。由于 Keycloak 作为容器与应用捆绑在一起，我们提供了一个指向 Keycloak 资源的 URL，用于加载公钥。

```
mp.jwt.verify.publickey.location=http://keycloak:5050/realms/jwallet/protocol/openid-connect/certs
mp.jwt.verify.issuer=http://localhost:5050/realms/jwallet
清单 11-4
传递公钥 URL
```

调用 `http://localhost:5050/realms/jwallet/protocol/openid-connect/certs` 会返回清单 11-5 中所示的示例公钥。

```
{
"keys": [
{
"kid": "D2GqblISv3cRivhvdTtbjZT86VAFyAiOopdiodv-BYI",
"kty": "RSA",
"alg": "RS256",
"use": "sig",
"n": "3buUZWr0Fp9Rm85GPIAoYfiqNapj2DxW8gjMWOjPEgg4KUqg3tSC6G0kAzPJ70zMmfe-b3CWBQvmNXWIUZCXPlnBuVKyWcowBQk7QRnEkOuw5vL6bYU5I2DA_1CPIn4v3g4ox0GmjOwDflrlb2vlPw76BpHIKd-EUI-mkm-Yv6H60BZmo7iRyVF7TNGVYgIg0xrVFPIvTDV2yvZKO4qG6qYWk2khMPqXm-725QBp2wgWDhowibjuoOCW1S5BCr5qc3N-uaPej7Nbcub80v1XGJ7feHqg4TXtNDCSikTAGCa0geW3eP1wRoE_9QYzKlN45LI8LxhncUl58WpdulgblQ",
"e": "AQAB",
"x5c": [
"MIICnTCCAYUCBgGC2NIn1TANBgkqhkiG9w0BAQsFADASMRAwDgYDVQQDDAdqd2FsbGV0MB4XDTIyMDgyNjA2MjIwN1oXDTMyMDgyNjA2MjM0N1owEjEQMA4GA1UEAwwHandhbGxldDCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAN27lGVq9BafUZvORjyAKGH4qjWqY9g8VvIIzFjozxIIOClKoN7UguhtJAMzye9MzJn3vm9wlgUL5jV1iFGQlz5ZwblSslnKMAUJO0EZxJDrsOby+m2FOSNgwP9QjyJ+L94OKMdBpozsA35a5W9r5T8O+gaRyCnfhFCPppJvmL+h+tAWZqO4kclRe0zRlWICINMa1RTyL0w1dsr2SjuKhuqmFpNpITD6l5vu9uUAadsIFg4aMIm47qDgltUuQQq+anNzfrmj3o+zW3Lm/NL9Vxie33h6oOE17TQwkopEwBgmtIHlt3j9cEaBP/UGMypTeOSyPC8YZ3FJefFqXbpYG5UCAwEAATANBgkqhkiG9w0BAQsFAAOCAQEAuuo6yFASkSWDLh9kyMTZhhcfV87ZEoC4kmkNn/2N2d0gypqxxLWPDG7rk2QLqocNT0IP6wh+cr9TO/oAUdIc1vAJuLWsw/XiGbPjTVmxw9r1eg3tMphrw+wG4CrRNsKTs1xgDDMnAyTA7zNZylSwAJ2YBy/7WAb1DzPdpwxuERY3Bn1U9pTV6Tth+SY3n0DVWYl9ik2eyv/UoTCLdf9gInxHxoyl6moIH944UZXdVnZ9s+TONtVu278fQNUJMbvufN1+IQQkZLH5zehVd8IY/vX1q9ROjFeaHjYEYSoggo3tCT93KVFYGA6UCm2ADQfluMxNwkIIdvfRVzqIuCKZuw=="
],
"x5t": "uej9O_JH-rlIpJVQMTBiD6kf-1k",
"x5t#S256": "szj37ByL-T6Plrmzbgb7Bnq32KsYtGp_B_dDOYGEWcI"
},
{
"kid": "B5uP6pwSkS-wvKmh31ZeEmeXNKU_UaO9EfTOeut00NY",
"kty": "RSA",
"alg": "RSA-OAEP",
"use": "enc",
"n": "sR3wZR9t35zM_sftCb2EbX6_tZo1boubrmnuj8YZ1utCOl2pQGX7v3uz_YHGsAbp-3J3iCt94KliRKA2yYCVi2Ozqd-EEz6GAYxF1HzYghWO-j9jMrNu1N-O8LXVEp21wPdIDsIuCLkqYWZGgoOzY2yyWkVjiHcAu3_LokwufVu-BM9iRY2FZrvgYEHPGZOk5a8hTIHSIx6dXUcU6oZ0etiGdidrIWH5GFXLSZtpcuya-w01_o1Khm97dCtGY3pVasnLgDK6Vguzul1MdpTQHwFI7m3O7uQB0CRtqe8qPnNxVLal_Ow5b5N7YNw5fufEqvLk5Dz-au4TPuFwSSvVrw",
"e": "AQAB",
"x5c": [
"MIICnTCCAYUCBgGC2NIoXjANBgkqhkiG9w0BAQsFADASMRAwDgYDVQQDDAdqd2FsbGV0MB4XDTIyMDgyNjA2MjIwN1oXDTMyMDgyNjA2MjM0N1owEjEQMA4GA1UEAwwHandhbGxldDCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBALEd8GUfbd+czP7H7Qm9hG1+v7WaNW6Lm65p7o/GGdbrQjpdqUBl+797s/2BxrAG6ftyd4grfeCpYkSgNsmAlYtjs6nfhBM+hgGMRdR82IIVjvo/YzKzbtTfjvC11RKdtcD3SA7CLgi5KmFmRoKDs2NsslpFY4h3ALt/y6JMLn1bvgTPYkWNhWa74GBBzxmTpOWvIUyB0iMenV1HFOqGdHrYhnYnayFh+RhVy0mbaXLsmvsNNf6NSoZve3QrRmN6VWrJy4AyulYLs7pdTHaU0B8BSO5tzu7kAdAkbanvKj5zcVS2pfzsOW+Te2DcOX7nxKry5OQ8/mruEz7hcEkr1a8CAwEAATANBgkqhkiG9w0BAQsFAAOCAQEAXW0wtyu6Tmv6i9rEvly5jvNcsNsvHVpW9tSV0KVWFP0YgvQ0dIQoRhygpvVfZMrlXW5EQ0uKnvZCbt/BzEPMl3G6DfsaTssw1GeaquMgw1osNqG7HCHGqQX8+EG5U3Neov4+YsSCtYYCWsYqO8OYK6lqp8TQ0+Ok6u9aTpFvH233Gd9ZmM72fN8omfR2X5dwlwL6uNAKiGc6rqavklV0qK3/TpSbYXG8oPGuAFmJMGmSJ9Xr1+x0gr3ncsYQnHf7jjTx7y3BG+AB2G7y3uez2fXDBktjBUiAV+31N4fh99WnbOPiVkhM2lWFMeuL1jg+vDgL/+PDziAGo2R5tgJM1w=="
],
"x5t": "rU2BclI4blvOQgymzyX0GHmm_Tk",
"x5t#S256": "Vkv7YeizaqHS23BlvZCJwTSpjpvp55J1PCmE2LtREos"
}
]
}
清单 11-5
示例公钥
```

借助提供的密钥，MicroProfile JWT 运行时会自动验证和认证对应用中所有受保护资源的每个 REST 调用。默认情况下，运行时会从 HTTP `Authorization` 标头中查找密钥。这可以通过 Config 属性 `mp.jwt.token.header` 配置为其他位置。除非有非常特殊的架构原因需要更改，否则我们建议保留默认设置。



#### 授权

当客户端通过身份验证后，MicroProfile JWT 会将 JWT 声明中传递的组映射为客户端角色。对于每个受保护的资源，运行时将自动检查当前正在执行的客户端是否具有服务器声明的角色。

#### 安全上下文传播

在令牌验证通过且客户端完成身份验证后，MicroProfile JWT API 提供了获取原始令牌的方法，该令牌随后可被传递给微服务集群中的其他后端资源。例如，当客户端向服务 A 进行身份验证，而服务 A 又依赖于服务 B 时，如果两者都使用 MicroProfile JWT 进行保护，服务 A 可以获取客户端传递的原始令牌，并使用该令牌调用服务 B，从而将 JWT 传播到服务 B。

## MicroProfile JWT 的使用

使用 MicroProfile JWT 保护资源的第一步是在 Jakarta REST 的 `jakarta.ws.rs.core.Application` 类上使用 `@LoginConfig` 注解。清单 11-6 展示了 `RootResourceConfiguration` 类，它继承了 `Application`，并使用 `authMethod` 为 `MP-JWT` 的 `@LoginConfig` 注解进行了标注。

```
@ApplicationPath("api")
@LoginConfig(authMethod = "MP-JWT")
public class RootResourceConfiguration extends Application {
}
清单 11-6
JWT 配置
```

`@LoginConfig` 注解告知运行时，应用程序中的所有资源都应受到保护，因此所有客户端请求都需要传递一个有效的 JWT 以进行验证和身份验证。配置完成后，保护 Web 资源就像在资源类或方法上添加 `@RolesAllowed` 注解一样简单。清单 11-7 展示了 `WalletResource` 仅允许具有 `teller` 角色的已认证用户访问。用户角色完全在 Keycloak 中管理。在企业应用中，产品负责人与业务团队将共同决定应用程序每个用户所需的角色。所有这些都可以在 Keycloak 中完全完成。我们的后端服务只是消费安全框架中配置的内容。

```
@Consumes(MediaType.APPLICATION_JSON)
@Produces(MediaType.APPLICATION_JSON)
@Path("wallets")
@RolesAllowed("teller")
public class WalletResource {
}
清单 11-7
受保护的 WalletResource
```

来自 `jakarta.annotation.security.RolesAllowed` 包的 `@RolesAllowed` 注解，会导致向那些在未携带有效令牌的情况下调用此类中任何资源的客户端返回 HTTP 状态码 401。清单 11-8 展示了一个未受保护调用的响应示例。

```
http://localhost:3001/wallet/api/wallets
HTTP/1.1 401 Unauthorized
WWW-Authenticate: Bearer realm="MP-JWT", error="invalid_token"
Content-Language: en-US
Content-Length: 0
Date: Sun, 28 Aug 2022 00:13:24 GMT
清单 11-8
无令牌调用的响应
```

### 从令牌中获取信息

当用户成功通过验证和身份验证后，应用程序可能希望获取令牌声明中包含的一些信息。API 提供了两种主要方式来访问 JWT 主体中的声明。它们是 `JsonWebToken` bean 和使用 `@Claim` 限定符注入声明值。

#### JsonWebToken

`JsonWebToken` 接口继承了 `java.security.Principal`，并包含用于获取 JWT 中声明的方法。它还有一个获取原始令牌的方法。这可用于获取原始令牌以进一步传播到其他服务。这是我们推荐的使用 JWT 声明的方法，因为 `JsonWebToken` 实例不要求注入它的 bean 必须是 `@RequestScoped`。清单 11-9 展示了将 `JsonWebToken` 注入到 `@ApplicationScoped` 的 `WalletService` 中。

```
@ApplicationScoped
public class WalletService {
@Inject
JsonWebToken jsonWebToken;
public BalanceResponse createWallet(CreateWalletRequest request){
Wallet wallet = new Wallet();
wallet.setBalance(BigDecimal.ZERO);
wallet.setCurrency(request.getCurrency());
wallet.setAuthUserId(jsonWebToken.getSubject());
wallet = walletRepository.save(wallet);
return getBalance(wallet.getId());
}
}
清单 11-9
注入 JsonWebToken
```

`WalletService` 是一个应用程序作用域的 bean，其中注入了 `JsonWebToken`。MicroProfile JWT 运行时会为我们提供该接口的实现。注入的 `JsonWebToken` 可用于获取令牌的声明。`createUser` 方法调用 `jsonWebToken` 上的 `getSubject` 方法来获取令牌的主题。Keycloak 返回的令牌主题是用户的 IAM ID。

通过这种方式，我们将钱包数据库记录与 Keycloak 中的用户关联起来，而两个服务彼此之间无需了解任何信息。`WalletService` 不是一个 Jakarta REST 工件。它是一个普通的 CDI bean，但通过 MicroProfile JWT API，我们能够在应用程序的服务层访问通过 HTTP 头传递的令牌中的声明。我们认为这是从 JWT 访问声明最清晰的方式，因此推荐使用此方法。即使端点未受保护，导致没有传递令牌，也会创建一个空的 `JsonWebToken` 实例。对该空实例的所有方法调用都将返回 null。

有了这个，我们还可以重构 `AbstractEntityListener`，以便最终为持久化实体设置 `createdBy` 审计字段。清单 11-10 展示了更新后的 `AbstractEntityListener`，它使用 `JsonWebToken` 来设置 `createdBy` 和 `updatedBy` 字段。

```
@ApplicationScoped
public class AbstractEntityEntityListener {
@Inject
JsonWebToken jsonWebToken;
@PrePersist
void init(final Object entity) {
final AbstractEntity abstractEntity = (AbstractEntity) entity;
abstractEntity.setCreated(LocalDateTime.now(ZoneOffset.UTC));
abstractEntity.setCreatedBy(jsonWebToken.getClaim(Claims.upn));
}
@PreUpdate
void update(final Object entity) {
final AbstractEntity abstractEntity = (AbstractEntity) entity;
abstractEntity.setUpdated(LocalDateTime.now(ZoneOffset.UTC));
abstractEntity.setEditedBy(jsonWebToken.getClaim(Claims.upn));
}
}
清单 11-10
使用 JsonWebToken
```

`@PrePersist` 和 `@PreUpdate` Jakarta Persistence 回调方法使用 `JsonWebToken` 的 `getClaim` 方法来获取特定的声明。`org.eclipse.microprofile.jwt.Claims` 枚举包含了 JWT 中所有声明的常量。

#### 注入到原始类型和 ClaimValue 中

JWT 声明也可以通过 `@Inject` 和 `@Claims` 限定符注入到简单的 Java 类型中。我们通常不推荐这种获取令牌的方式，因为某些可以注入声明的字段类型要求它们被注入到的 bean 必须是 `@RequestScoped`。实际上，为了能够充分利用这种方式注入声明值，bean 应该是 `@RequestScoped`。清单 11-11 展示了使用这两种构造注入声明的方法。

```
@Action
public class WalletService {
@Inject
@Claim(standard = Claims.sub)
private String subject;
@Inject
@Claim(standard = Claims.raw_token)
private ClaimValue rawToken;
}
清单 11-11
注入到简单类型中
```

`subject` 字段是一个简单的 `String` 类型，使用 `@Inject` 和限定符 `@Claim` 进行注解。`@Claim` 限定符的 `standard` 参数被传递了要注入到此字段的声明名称。使用 `Claims` 枚举来传递 `sub` 或 `subject` 声明作为感兴趣的声明。`org.eclipse.microprofile.jwt.ClaimValue` 是一个继承自 `Principal` 接口的接口。它有两个方法：`getName` 和 `getValue`。`getName` 返回声明的名称，`getValue` 返回令牌中该声明的值。



## MicroProfile JWT 与 SecurityContext

`jakarta.ws.rs.core.SecurityContext` 接口是 Jakarta REST 的一个组件，可通过 `@Context` 注解注入到资源组件中。它提供了获取 Principal、检查当前登录用户是否具有指定角色、判断请求是否通过 HTTPS 连接发起以及获取认证方案的方法。在 MicroProfile JWT 应用中，`isUserInRole` 方法会将角色集映射到令牌中声明的已传递组。`getPrincipal` 方法也会返回一个 Principal，该 Principal 同时也是 `JsonWebToken` 的实例。实际上，MicroProfile JWT 会将令牌中传递的所有信息传播到应用的各个部分以及 Jakarta EE 中现有的安全基础设施中。清单 11-12 展示了如何将 SecurityContext 注入到 Wallet 资源中。

```
@Consumes(MediaType.APPLICATION_JSON)
@Produces(MediaType.APPLICATION_JSON)
@Path("wallets")
@RolesAllowed("teller")
public class WalletResource {
@Context
SecurityContext securityContext;
}
清单 11-12
注入 SecurityContext
```

## 总结

本章介绍了如何使用 MicroProfile JWT API 来保护 Jakarta EE REST 资源。鉴于应用安全的复杂性，本章遵循最佳实践，将用户凭据的处理工作外包给外部服务（本例中为 Keycloak）。您学习了如何通过 `@LoginConfig` 注解在应用中激活 MicroProfile JWT，以及如何通过 `@RolesAllowed` 注解在类级别或资源方法级别保护单个资源。

您还学习了如何通过 `JsonWebToken` 接口访问令牌中的声明。同时了解到 JWT 声明会自动传播，并可在现有的 Jakarta EE 安全基础设施（如 Principal 和 SecurityContext 接口）中使用。我们再次强调，安全是一个极其复杂且快速变化的领域，每天都有新的漏洞攻击出现。因此，采用本章讨论的混合安全方法——将用户凭据处理等复杂方面委托给 Keycloak 这样的专业应用，而我们的应用仅消费生成的令牌——是降低安全风险的一种方式。

脚注 1   2

