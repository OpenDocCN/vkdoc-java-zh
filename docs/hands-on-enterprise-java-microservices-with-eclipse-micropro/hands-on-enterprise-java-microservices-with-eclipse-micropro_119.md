# 定义端点接口

要为端点定义类型安全接口，我们创建一个 Java 接口，该接口利用 JAX-RS 注解将接口方法映射到它们代理的 REST 端点。以下 `WorldClockApi` 接口展示了一个基本示例：

```
package io.pckt.restc.contract;import javax.ws.rs.GET;import javax.ws.rs.Path;import javax.ws.rs.PathParam;import javax.ws.rs.Produces;import javax.ws.rs.core.MediaType;@Path("/api/json")public interface WorldClockApi { static final String BASE_URL = "http://worldclockapi.com/api/json"; @GET @Path("/utc/now") @Produces(MediaType.APPLICATION_JSON) Now utc(); @GET @Path("{tz}/now") @Produces(MediaType.APPLICATION_JSON) Now tz(@PathParam("tz") String tz);}public class Now ...
```

