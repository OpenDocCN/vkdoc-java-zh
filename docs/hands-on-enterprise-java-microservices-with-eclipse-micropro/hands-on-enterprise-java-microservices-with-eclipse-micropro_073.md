# 注入 JWT 声明值

本节中的代码片段演示了如何注入单个 JWT 声明值。对于注入的值，我们可以使用几种不同的格式。标准声明支持 `Claim#getType` 字段和 `JsonValue` 子类型中定义的对象子类型。自定义声明类型仅支持注入 `JsonValue` 子类型。

以下代码示例演示了如何注入标准的 `groups` 和 `iss` 声明，以及 `customString`、`customInteger`、`customDouble` 和 `customObject` 自定义声明：

```
package io.pckt.jwt.rest;

import java.util.Set;
import javax.annotation.security.DenyAll;
import javax.annotation.security.RolesAllowed;
import javax.inject.Inject;
import javax.json.JsonArray;
import javax.json.JsonNumber;
import javax.json.JsonObject;
import javax.json.JsonString;
import javax.ws.rs.GET;
import javax.ws.rs.Path;

import org.eclipse.microprofile.jwt.Claim;
import org.eclipse.microprofile.jwt.Claims;

@Path("/jwt")
@DenyAll
public class InjectionExampleEndpoint {
    @Inject
    @Claim(standard = Claims.groups)
    Set<String> rolesSet; // 1
    @Inject
    @Claim(standard = Claims.iss)
    String issuer; // 2

    @Inject
    @Claim(standard = Claims.groups)
    JsonArray rolesAsJson; // 3
    @Inject
    @Claim(standard = Claims.iss)
    JsonString issuerAsJson; // 4
    // 作为 JsonValue 类型的自定义声明
    @Inject
    @Claim("customString")
    JsonString customString; // 5
    @Inject
    @Claim("customInteger")
    JsonNumber customInteger; // 6
    @Inject
    @Claim("customDouble")
    JsonNumber customDouble; // 7
    @Inject
    @Claim("customObject")
    JsonObject customObject; // 8

    @GET
    @Path("/printClaims")
    @RolesAllowed("Tester")
    public String printClaims() {
        return String.format("rolesSet=%s\n");
    }
}
```

这八处带注释的注入如下所示：

1.  将标准 `groups` 声明作为其默认的 `Set<String>` 类型注入
2.  将标准 `iss` 声明作为其默认的 String 类型注入
3.  将标准 `groups` 声明作为其默认的 `JsonArray` 类型注入
4.  将标准 `iss` 声明作为其默认的 `JsonString` 类型注入
5.  将非标准 `customString` 声明作为 `JsonString` 类型注入
6.  将非标准 `customInteger` 声明作为 `JsonNumber` 类型注入
7.  将非标准 `customDouble` 声明作为 `JsonNumber` 类型注入
8.  将非标准 `customObject` 声明作为 `JsonString` 类型注入

