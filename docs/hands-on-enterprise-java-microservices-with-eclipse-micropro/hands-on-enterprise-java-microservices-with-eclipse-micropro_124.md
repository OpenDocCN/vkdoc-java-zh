# 处理客户端标头

假设您想要在 HTTP 授权标头中指定凭据以访问安全的远程服务，但又不希望在客户端接口方法中包含一个字符串 `authHeader` 参数。MP-RC 的 `@ClientHeaderParam` 注解可用于指定应发送的 HTTP 标头，而无需更改客户端接口方法签名。

以下示例说明了 `@ClientHeaderParam` 注解的两种用法，用于在 `WorldClockApi` 接口的变体中提供 `User-Agent` HTTP 标头：

```
WorldClockApiWithHeaders.java
public interface WorldClockApiWithHeaders {
 static final String BASE_URL = "http://worldclockapi.com/api/json";

 default String lookupUserAgent() {
 Config config = ConfigProvider.getConfig();
 String userAgent = config.getValue("WorldClockApi.userAgent", String.class);
 if(userAgent == null) {
 userAgent = "MicroProfile REST Client 1.2";
 }
 return userAgent;
 }

 @GET
 @Path("/utc/now")
 @Produces(MediaType.APPLICATION_JSON)
 @ClientHeaderParam(name = "User-Agent", value = "{lookupUserAgent}")
 Now utc();

 @GET
 @Path("{tz}/now")
 @Produces(MediaType.APPLICATION_JSON)
 @ClientHeaderParam(name = "User-Agent", value = "MicroProfile REST Client 1.2")
 Now tz(@PathParam("tz") String tz);
} 
```

也可以使用 `ClientHeadersFactory` 实现来批量添加或传播标头：

```
package org.eclipse.microprofile.rest.client.ext;

public interface ClientHeadersFactory {
 MultivaluedMap<String, String> update(
    MultivaluedMap<String, String> incomingHeaders,
    MultivaluedMap<String, String> clientOutgoingHeaders);
}
```

在前面的代码片段中，`incomingHeaders` 和 `clientOutgoingHeaders` 参数的用途如下：

*   `incomingHeaders`：表示入站请求的标头映射
*   `clientOutgoingHeaders`：表示在客户端接口上指定的标头值的只读映射，是来自 `@ClientHeaderParam`、`@HeaderParam` 等的标头值的并集

`update` 方法应返回一个 `MultivaluedMap`，其中包含要与 `clientOutgoingHeaders` 映射合并的标头，以形成将发送到出站请求的完整标头映射。诸如过滤器、拦截器和消息体写入器之类的提供者仍然可以在发送 HTTP 请求之前修改最终的标头映射。

要启用 `ClientHeadersFactory`，客户端接口必须使用 `@RegisterClientHeaders` 注解进行注解。如果此注解指定了一个值，则客户端实现必须调用指定 `ClientHeadersFactory` 实现类的实例。如果未指定值，则客户端实现必须调用 `DefaultClientHeadersFactoryImpl`。此默认工厂会将指定的标头从入站 JAX-RS 请求传播到出站请求——这些标头使用 MicroProfile Config 属性 `org.eclipse.microprofile.rest.client.propagateHeaders` 以逗号分隔的列表指定。

