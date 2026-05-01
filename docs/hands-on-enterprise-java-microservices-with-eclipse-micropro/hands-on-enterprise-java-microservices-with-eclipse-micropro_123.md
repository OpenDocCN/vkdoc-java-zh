# 简化配置键

由于 CDI 接口的默认 MP Config 属性名称因包含接口包名而可能相当长，MP-RC 规范支持使用 `@RegisterRestClient` 注解的 `configKey` 属性来简化属性名称前缀：

```
@Path("/api/json")@RegisterRestClient(baseUri = WorldClockApi.BASE_URL, configKey = "worldClock")public interface WorldClockApi { static final String BASE_URL = "http://worldclockapi.com/api/json";...}
```

使用 `worldClock` 这个 configKey，之前的属性列表简化为以下内容：

*   `worldClock/mp-rest/url`
*   `worldClock/mp-rest/uri`
*   `worldClock/mp-rest/scope`
*   `worldClock/mp-rest/providers`
*   `worldClock/mp-rest/providers/com.mycompany.MyProvider/priority ...`

