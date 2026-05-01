# @Fallback 策略

`@Fallback` 注解只能应用于方法；注解类将产生意外结果：

```
@Retry(maxRetries = 2)
@Fallback(StringFallbackHandler.class)
public String shouldFallback() {
  ...
}
```

降级方法在达到重试次数后被调用。在上面的示例中，如果发生错误，该方法将被重试两次，然后使用降级逻辑来调用另一段代码——在本例中，是下面的 `StringFallbackHandler` 类：

```
import javax.enterprise.context.ApplicationScoped;

import org.eclipse.microprofile.config.inject.ConfigProperty;
import org.eclipse.microprofile.faulttolerance.ExecutionContext;
import org.eclipse.microprofile.faulttolerance.FallbackHandler;

@ApplicationScoped
public class StringFallbackHandler implements FallbackHandler<String> {
    @ConfigProperty(name="app1.requestFallbackReply", defaultValue = "Unconfigured Default Reply")
    private String replyString;

    @Override
    public String handle(ExecutionContext ec) {
        return replyString;
    }
}
```

降级代码可以通过实现 `FallbackHandler` 接口的类（参见前面的代码）或当前 Bean 中的一个方法来定义。在 `StringFallbackHandler` 代码中，使用了一个名为 `app1.requestFallbackReply` 的 MicroProfile Config 属性来外部化应用程序的降级字符串值。

