# 注入 JsonWebToken 信息

以下代码示例演示了如何将传入的 MP-JWT 令牌作为 `JsonWebToken` 进行访问、获取原始 JWT 令牌字符串、`upn` 声明，以及与 JAX-RS `SecurityContext` 的集成：

```
package io.pckt.jwt.rest;import javax.annotation.security.DenyAll;import javax.annotation.security.PermitAll;import javax.annotation.security.RolesAllowed;import javax.inject.Inject;import javax.ws.rs.GET;import javax.ws.rs.Path;import javax.ws.rs.Produces;import javax.ws.rs.core.Context;import javax.ws.rs.core.MediaType;import javax.ws.rs.core.SecurityContext;import org.eclipse.microprofile.jwt.Claim;import org.eclipse.microprofile.jwt.Claims;import org.eclipse.microprofile.jwt.JsonWebToken;@Path("/jwt")@DenyAll //1public class ...
```

