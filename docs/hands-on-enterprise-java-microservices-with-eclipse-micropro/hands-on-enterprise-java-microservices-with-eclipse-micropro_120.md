# MicroProfile REST 客户端编程式 API 使用

MP-RC 支持编程式查找和 CDI 注入两种使用方式。以下 `WorldClockUser.java` 列出了一个 REST 服务使用 `org.eclipse.microprofile.rest.client.RestClientBuilder` 为 `WorldClockApi` 接口创建类型安全客户端的示例：

```
package io.pckt.restc.contract;

import javax.ws.rs.GET;
import javax.ws.rs.Path;
import javax.ws.rs.PathParam;
import javax.ws.rs.Produces;
import javax.ws.rs.core.MediaType;

@Path("/api")
@ApplicationScoped
public class WorldClockUser {
 @GET
 @Path("/now-utc")
 @Produces(MediaType.TEXT_PLAIN)
 public String getCurrentDateTime() {
 WorldClockApi remoteApi = RestClientBuilder.newBuilder()
 .baseUri(URI.create(WorldClockApi.BASE_URL))
 .build(WorldClockApi.class);
 Now now = remoteApi.utc();
 return now.getCurrentDateTime();
 }
}
```

`baseUri()` 方法用于指定服务器 URI，`WorldClockApi` 方法路径将根据该 URI 进行解析。`build()` 方法接收要构建的类型安全客户端的 Java 接口。其他 `RestClientBuilder` 方法包括：

*   `baseUrl(URL)`：类似于 `baseUri`，但接受 `java.net.URL` 类型。
*   `connectTimeout(long timeout, TimeUnit unit)`：等待连接到远程服务器的时间。值为 0 表示无限期等待。
*   `readTimeout(long timeout, TimeUnit unit)`：等待读取远程服务器连接的时间。值为 0 表示无限期等待。
*   `executorService(ExecutorService executor)`：用于异步请求。我们将在异步部分再次讨论这一点。

