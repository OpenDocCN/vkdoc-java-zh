# 运行示例

我们之前查看的示例可以部署到 Thorntail，并通过命令行查询端点来验证预期行为。由于对标记了安全约束的端点进行身份验证需要有效的 JWT，我们需要一种方法来生成能被 Thorntail 服务器接受的 JWT。

本章代码提供了一个 `io.packt.jwt.test.GenerateToken` 工具，它将创建一个由已配置到 Thorntail 服务器的密钥签名的 JWT。JWT 中包含的声明由本章项目中的 `src/test/resources/JwtClaims.json` 文件定义。你可以使用 `mvn exec:java` 命令运行该工具，如下所示：

```
Scotts-iMacPro:jwtprop starksm$ mvn exec:java -Dexec.mainClass=io.packt.jwt.test.GenerateToken -Dexec.classpathScope=test
[INFO] Scanning for projects...
[INFO]
[INFO] ----------------< io.microprofile.jwt:jwt-propagation >-----------------
[INFO] Building JWT Propagation 1.0-SNAPSHOT
[INFO] --------------------------------[ war ]---------------------------------
[INFO]
[INFO] --- exec-maven-plugin:1.6.0:java (default-cli) @ jwt-propagation ---
Setting exp: 1555684338 / Fri Apr 19 07:32:18 PDT 2019
    Added claim: sub, value: 24400320
    Added claim: customIntegerArray, value: [0,1,2,3]
    Added claim: customDoubleArray, value: [0.1,1.1,2.2,3.3,4.4]
    Added claim: iss, value: http://io.packt.jwt
    Added claim: groups, value: 
    ["Echoer","Tester","User","group1","group2"]
    Added claim: preferred_username, value: jdoe
    Added claim: customStringArray, value: ["value0","value1","value2"]
    Added claim: aud, value: [s6BhdRkqt3]
    Added claim: upn, value: jdoe@example.com
    Added claim: customInteger, value: 123456789
    Added claim: auth_time, value: 1555683738
    Added claim: customObject, value: {"my-service":{"roles":["role-in-my-
    service"],"groups":["group1","group2"]},"service-B":{"roles":["role-in-
    B"]},"service-C":{"groups":["groupC","web-tier"]},"scale":0.625}
    Added claim: exp, value: Fri Apr 19 07:32:18 PDT 2019
    Added claim: customDouble, value: 3.141592653589793
    Added claim: iat, value: Fri Apr 19 07:22:18 PDT 2019
    Added claim: jti, value: a-123
    Added claim: customString, value: customStringValue
eyJraWQiOiJcL3ByaXZhdGUta2V5LnBlbSIsInR5cCI6IkpXVCIsImFsZyI6IlJTMjU2In0.eyJzdWIiOiIyNDQwMDMyMCIsImN1c3RvbUludGVnZXJBcnJheSI6WzAsMSwyLDNdLCJjdXN0b21Eb3VibGVBcnJheSI6WzAuMSwxLjEsMi4yLDMuMyw0LjRdLCJpc3MiOiJodHRwOlwvXC9pby5wYWNrdC5qd3QiLCJncm91cHMiOlsiRWNob2VyIiwiVGVzdGVyIiwiVXNlciIsImdyb3VwMSIsImdyb3VwMiJdLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJqZG9lIiwiY3VzdG9tU3RyaW5nQXJyYXkiOlsidmFsdWUwIiwidmFsdWUxIiwidmFsdWUyIl0sImF1ZCI6InM2QmhkUmtxdDMiLCJ1cG4iOiJqZG9lQGV4YW1wbGUuY29tIiwiY3VzdG9tSW50ZWdlciI6MTIzNDU2Nzg5LCJhdXRoX3RpbWUiOjE1NTU2ODM3MzgsImN1c3RvbU9iamVjdCI6eyJteS1zZXJ2aWNlIjp7InJvbGVzIjpbInJvbGUtaW4tbXktc2VydmljZSJdLCJncm91cHMiOlsiZ3JvdXAxIiwiZ3JvdXAyIl19LCJzZXJ2aWNlLUIiOnsicm9sZXMiOlsicm9sZS1pbi1CIl19LCJzZXJ2aWNlLUMiOnsiZ3JvdXBzIjpbImdyb3VwQyIsIndlYi10aWVyIl19LCJzY2FsZSI6MC42MjV9LCJleHAiOjE1NTU2ODQzMzgsImN1c3RvbURvdWJsZSI6My4xNDE1OTI2NTM1ODk3OTMsImlhdCI6MTU1NTY4MzczOCwianRpIjoiYS0xMjMiLCJjdXN0b21TdHJpbmciOiJjdXN0b21TdHJpbmdWYWx1ZSJ9.bF7CnutcQnA2gTlCRNOp4QMmWTWhwP86cSiPCSxWr8N36FG79YC9Lx0Ugr-Ioo2Zw35z0Z0xEwjAQdKkkKYU9_1GsXiJgfYqzWS-XxEtwhiinD0hUK2qiBcEHcY-ETx-bsJud8_mSlrzEvrJEeX58Xy1Om1FxnjuiqmfBJxNaotxECScDcDMMH-DeA1Z-nrJ3-0sdKNW6QxOxoR_RNrpci1F9y4pg-eYOd8zE4tN_QbT3KkdMm91xPhv7QkKm71pnHxC0H4SmQJVEAX6bxdD5lAzlNYrEMAJyyEgKuJeHTxH8qzM-0FQHzrG3Yhnxax2x3Xd-6JtEbU-_E_3HRxvvw
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  1.339 s
[INFO] Finished at: 2019-04-19T07:22:19-07:00
[INFO] ------------------------------------------------------------------------
```

该工具会输出已添加的声明，然后打印出 base64 编码的 JWT。你可以将此 JWT 用作 `curl` 命令行中 `Authorization: Bearer …` 标头的值，以访问服务器端点。

要启动带有示例端点的 Thorntail 服务器，请 `cd` 进入 `Chapter04-jwtpropagation` 项目目录，然后运行 `mvn` 构建可执行 JAR：

```
Scotts-iMacPro:jwtprop starksm$ mvn package
[INFO] Scanning for projects...
[INFO]
[INFO] ----------------< io.microprofile.jwt:jwt-propagation >-----------------
[INFO] Building JWT Propagation 1.0-SNAPSHOT
...
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  8.457 s
[INFO] Finished at: 2019-04-19T08:25:09-07:00
[INFO] ------------------------------------------------------------------------
```

生成的可执行 JAR 位于 `target/jwt-propagation-thorntail.jar`。你可以使用 `java -jar …` 启动带有本章示例部署的 Thorntail 服务器：

```
Scotts-iMacPro:jwtprop starksm$ java -jar target/jwt-propagation-thorntail.jar
2019-04-19 08:27:33,425 INFO  [org.wildfly.swarm] (main) THORN0013: Installed fraction: MicroProfile Fault Tolerance - STABLE          io.thorntail:microprofile-fault-tolerance:2.4.0.Final
2019-04-19 08:27:33,493 INFO  [org.wildfly.swarm] (main) THORN0013: Installed fraction:          Bean Validation - STABLE io.thorntail:bean-validation:2.4.0.Final
2019-04-19 08:27:33,493 INFO  [org.wildfly.swarm] (main) THORN0013: Installed fraction:      MicroProfile Config - STABLE io.thorntail:microprofile-config:2.4.0.Final
2019-04-19 08:27:33,493 INFO  [org.wildfly.swarm] (main) THORN0013: Installed fraction:             Transactions - STABLE io.thorntail:transactions:2.4.0.Final
2019-04-19 08:27:33,494 INFO  [org.wildfly.swarm] (main) THORN0013: Installed fraction:        CDI Configuration - STABLE io.thorntail:cdi-config:2.4.0.Final
2019-04-19 08:27:33,494 INFO  [org.wildfly.swarm] (main) THORN0013: Installed fraction: MicroProfile JWT RBAC Auth - STABLE          io.thorntail:microprofile-jwt:2.4.0.Final
…
2019-04-19 08:27:37,708 INFO  [org.jboss.as.server] (main) WFLYSRV0010: Deployed "jwt-propagation.war" (runtime-name : "jwt-propagation.war")
2019-04-19 08:27:37,713 INFO  [org.wildfly.swarm] (main) THORN99999: Thorntail is Ready
```

此时，我们可以查询服务器端点。我们定义了一个不需要任何身份验证的端点，即 `io.pckt.jwt.rest.SecureEndpoint` 类的 `jwt/openHello` 端点。运行以下命令验证你的 Thorntail 服务器是否按预期运行：

```
Scotts-iMacPro:jwtprop starksm$ curl http://localhost:8080/jwt/openHello; echo
Hello[open] user=anonymous, upn=no-upn
```

接下来，尝试安全端点。由于我们没有提供任何授权信息，它应该会失败并返回 401 未授权错误：

```
Scotts-iMacPro:jwtprop starksm$ curl http://localhost:8080/jwt/secureHello; echo
Not authorized
```

现在，我们需要生成一个新的 JWT，并将其与 curl 命令一起放在 `Authorization` 标头中传递，让我们尝试一下。我们将把 mvn 命令生成的 JWT 保存到 JWT 环境变量中，以简化 curl 命令行：



```
Scotts-iMacPro:jwtprop starksm$ mvn exec:java -Dexec.mainClass=io.packt.jwt.test.GenerateToken -Dexec.classpathScope=test
[INFO] Scanning for projects...
[INFO]
[INFO] ----------------< io.microprofile.jwt:jwt-propagation >-----------------
[INFO] Building JWT Propagation 1.0-SNAPSHOT
[INFO] --------------------------------[ war ]---------------------------------
[INFO]
[INFO] --- exec-maven-plugin:1.6.0:java (default-cli) @ jwt-propagation ---
Setting exp: 1555688712 / Fri Apr 19 08:45:12 PDT 2019
    Added claim: sub, value: 24400320
    Added claim: customIntegerArray, value: [0,1,2,3]
    Added claim: customDoubleArray, value: [0.1,1.1,2.2,3.3,4.4]
    Added claim: iss, value: http://io.packt.jwt
    Added claim: groups, value: 
    ["Echoer","Tester","User","group1","group2"]
    Added claim: preferred_username, value: jdoe
    Added claim: customStringArray, value: ["value0","value1","value2"]
    Added claim: aud, value: [s6BhdRkqt3]
    Added claim: upn, value: jdoe@example.com
    Added claim: customInteger, value: 123456789
    Added claim: auth_time, value: 1555688112
    Added claim: customObject, value: {"my-service":{"roles":["role-in-my-
    service"],"groups":["group1","group2"]},"service-B":{"roles":["role-in-
    B"]},"service-C":{"groups":["groupC","web-tier"]},"scale":0.625}
    Added claim: exp, value: Fri Apr 19 08:45:12 PDT 2019
    Added claim: customDouble, value: 3.141592653589793
    Added claim: iat, value: Fri Apr 19 08:35:12 PDT 2019
    Added claim: jti, value: a-123
    Added claim: customString, value: customStringValue
eyJraWQiOiJ...
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  1.352 s
[INFO] Finished at: 2019-04-19T08:35:12-07:00
[INFO] ------------------------------------------------------------------------
Scotts-iMacPro:jwtprop starksm$ JWT="eyJraWQiOi..."
Scotts-iMacPro:jwtprop starksm$ curl -H "Authorization: Bearer $JWT" http://localhost:8080/jwt/secureHello; echo
Hello[secure] user=jdoe@example.com, upn=jdoe@example.com, scheme=MP-JWT, isUserRole=true
```

在上一个代码片段中，对于 Windows 用户，请安装一个兼容 bash 的 Windows shell；否则，你会因为 `echo` 命令而遇到错误。

这次，查询成功了，我们看到用户名、`upn` 声明值、方案以及 `isUserInRole("User")` 检查都符合预期。

现在，尝试访问 `/jwt/printClaims` 端点，该端点演示了将标准和非标准声明作为不同类型进行注入：

```
Scotts-iMacPro:jwtprop starksm$ curl -H "Authorization: Bearer $JWT" http://localhost:8080/jwt/printClaims
+++ Standard claims as primitive types
rolesSet=[Echoer, Tester, User, group2, group1]
issuer=http://io.packt.jwt
+++ Standard claims as JSON types
rolesAsJson=["Echoer","Tester","User","group2","group1"]
issuerAsJson="http://io.packt.jwt"
+++ Custom claim JSON types
customString="customStringValue"
customInteger=123456789
customDouble=3.141592653589793
customObject={"my-service":{"roles":["role-in-my-service"],"groups":["group1","group2"]},"service-B":{"roles":["role-in-B"]},"service-C":{"groups":["groupC","web-tier"]},"scale":0.625}
```

请注意，如果一段时间后你开始遇到 `Not authorized errors`（未授权错误），问题在于 JWT 已过期。你需要生成一个新令牌，或者生成一个具有更长有效期的令牌。你可以通过向 `GenerateToken` 工具传入以秒为单位的过期时间来实现这一点。例如，要生成一个有效期为整整一小时的令牌，请执行以下操作：

```
Scotts-iMacPro:jwtprop starksm$ mvn exec:java -Dexec.mainClass=io.packt.jwt.test.GenerateToken -Dexec.classpathScope=test -Dexec.args="3600"
[INFO] Scanning for projects...
[INFO]
[INFO] ----------------< io.microprofile.jwt:jwt-propagation >-----------------
[INFO] Building JWT Propagation 1.0-SNAPSHOT
[INFO] --------------------------------[ war ]---------------------------------
[INFO]
[INFO] --- exec-maven-plugin:1.6.0:java (default-cli) @ jwt-propagation ---
Setting exp: 1555692188 / Fri Apr 19 09:43:08 PDT 2019
    Added claim: sub, value: 24400320
    Added claim: customIntegerArray, value: [0,1,2,3]
    Added claim: customDoubleArray, value: [0.1,1.1,2.2,3.3,4.4]
    Added claim: iss, value: http://io.packt.jwt
    Added claim: groups, value: ["Echoer","Tester","User","group1","group2"]
    Added claim: preferred_username, value: jdoe
    Added claim: customStringArray, value: ["value0","value1","value2"]
    Added claim: aud, value: [s6BhdRkqt3]
    Added claim: upn, value: jdoe@example.com
    Added claim: customInteger, value: 123456789
    Added claim: auth_time, value: 1555688588
    Added claim: customObject, value: {"my-service":{"roles":["role-in-my-service"],"groups":["group1","group2"]},"service-B":{"roles":["role-in-B"]},"service-C":{"groups":["groupC","web-tier"]},"scale":0.625}
    Added claim: exp, value: Fri Apr 19 09:43:08 PDT 2019
    Added claim: customDouble, value: 3.141592653589793
    Added claim: iat, value: Fri Apr 19 08:43:08 PDT 2019
    Added claim: jti, value: a-123
    Added claim: customString, value: customStringValue
eyJraWQiOiJcL3ByaXZhdGUta2V5LnBlbSIsInR5cCI6IkpXVCIsImFsZyI6IlJTMjU2In0.eyJzdWIiOiIyNDQwMDMyMCIsImN1c3RvbUludGVnZXJBcnJheSI6WzAsMSwyLDNdLCJjdXN0b21Eb3VibGVBcnJheSI6WzAuMSwxLjEsMi4yLDMuMyw0LjRdLCJpc3MiOiJodHRwOlwvXC9pby5wYWNrdC5qd3QiLCJncm91cHMiOlsiRWNob2VyIiwiVGVzdGVyIiwiVXNlciIsImdyb3VwMSIsImdyb3VwMiJdLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJqZG9lIiwiY3VzdG9tU3RyaW5nQXJyYXkiOlsidmFsdWUwIiwidmFsdWUxIiwidmFsdWUyIl0sImF1ZCI6InM2QmhkUmtxdDMiLCJ1cG4iOiJqZG9lQGV4YW1wbGUuY29tIiwiY3VzdG9tSW50ZWdlciI6MTIzNDU2Nzg5LCJhdXRoX3RpbWUiOjE1NTU2ODg1ODgsImN1c3RvbU9iamVjdCI6eyJteS1zZXJ2aWNlIjp7InJvbGVzIjpbInJvbGUtaW4tbXktc2VydmljZSJdLCJncm91cHMiOlsiZ3JvdXAxIiwiZ3JvdXAyIl19LCJzZXJ2aWNlLUIiOnsicm9sZXMiOlsicm9sZS1pbi1CIl19LCJzZXJ2aWNlLUMiOnsiZ3JvdXBzIjpbImdyb3VwQyIsIndlYi10aWVyIl19LCJzY2FsZSI6MC42MjV9LCJleHAiOjE1NTU2OTIxODgsImN1c3RvbURvdWJsZSI6My4xNDE1OTI2NTM1ODk3OTMsImlhdCI6MTU1NTY4ODU4OCwianRpIjoiYS0xMjMiLCJjdXN0b21TdHJpbmciOiJjdXN0b21TdHJpbmdWYWx1ZSJ9.Tb8Fet_3NhABc6E5z5N6afwNsxzcZaa9q0eWWLm1AP4HPbJCOA14L275D-jAO42s7yQlHS7sUsi9_nWStDV8MTqoey4PmN2rcnOAaKqCfUiLehcOzg3naUk0AxRykCBO4YIck-qqvlEaZ6q8pVW_2Nfj5wZml2uPDq_X6aVLfxjaRzj2F4OoeKGH51-88yeu7H2THUMNLLPB2MY4Ma0xDUFXVL1TXU49ilOXOWTHAo7wAdqleuZUavtt_ZQfRwCUoI1Y-dltH_WtLdjjYw6aFIeJtsyYCXdqONiP6TqOpfACOXbV_nBYNKpYGn4GMiPsxmpJMU8JAhm-jJzf9Yhq6A
[INFO] -----------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] -----------------------------------------------------------------------
[INFO] Total time:  1.328 s
[INFO] Finished at: 2019-04-19T08:43:08-07:00
[INFO] -----------------------------------------------------------------------
```

这些示例应该能让你感受到微服务客户端之间的交互，以及如何使用 JWT 来保护微服务端点，从而实现无状态认证和基于角色的访问控制（RBAC），以及基于 JWT 中声明的自定义授权。

