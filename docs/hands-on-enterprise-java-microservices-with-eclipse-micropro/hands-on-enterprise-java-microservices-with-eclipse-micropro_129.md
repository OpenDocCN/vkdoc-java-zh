# 异常映射

MP-RC 通过 `org.eclipse.microprofile.rest.client.ext.ResponseExceptionMapper` 接口支持将调用响应映射为异常：

```
import javax.annotation.Priority;import javax.ws.rs.Priorities;import javax.ws.rs.core.MultivaluedMap;import javax.ws.rs.core.Response;import java.util.Optional;public interface ResponseExceptionMapper<T extends Throwable> {    int DEFAULT_PRIORITY = Priorities.USER;    T toThrowable(Response response);    default boolean handles(int status, MultivaluedMap<String, Object> headers) {        return status >= 400;    }    default int getPriority() {        return Optional.ofNullable(getClass().getAnnotation(Priority.class))            .map(Priority::value)            .orElse(DEFAULT_PRIORITY);    }}
```

请考虑以下内容……

