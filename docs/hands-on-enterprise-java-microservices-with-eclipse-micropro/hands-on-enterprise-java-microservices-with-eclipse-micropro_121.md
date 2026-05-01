# MicroProfile REST 客户端 CDI 使用

MP-RC 类型安全接口可以作为 CDI Bean 注入。运行时必须为每个使用 `@RegisterRestClient` 注解的接口创建一个 CDI Bean。注入的 CDI 客户端 Bean 将包含一个限定符 `@RestClient`，以区分其作为 MP-RC 注入点的用途。以下对 `WorldClockApi` 接口的更新说明了 `@RegisterRestClient` 注解的使用：

```
import javax.ws.rs.GET;import javax.ws.rs.Path;import javax.ws.rs.PathParam;import javax.ws.rs.Produces;import javax.ws.rs.core.MediaType;import org.eclipse.microprofile.rest.client.inject.RegisterRestClient;@Path("/api/json")@RegisterRestClient()public interface WorldClockApi { static final String BASE_URL = "http://worldclockapi.com/api/json"; ...
```



