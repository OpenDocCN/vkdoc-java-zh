# 异步支持

MP-RC 支持异步方法调用。当客户端接口方法的返回类型为 `java.util.concurrent.CompletionStage<?>` 类型时，该方法即为异步方法。`WorldClockApi` 接口的一个声明了异步方法的替代版本（称为 `WorldClockApiAsync.java`）如下所示：

```
import java.util.concurrent.CompletionStage;import javax.ws.rs.GET;import javax.ws.rs.Path;import javax.ws.rs.PathParam;import javax.ws.rs.Produces;import javax.ws.rs.core.MediaType;@Path("/api/json")public interface WorldClockApiAsync { String BASE_URL = "http://worldclockapi.com/api/json"; @GET @Path("/utc/now") @Produces(MediaType.APPLICATION_JSON) CompletionStage<Now> utc(); @GET @Path("{tz}/now") @Produces(MediaType.APPLICATION_JSON) ...
```

