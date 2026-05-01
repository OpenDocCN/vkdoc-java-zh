# 使用 MicroProfile Config 实现容错

正如我们在前几节中所见，容错策略是通过使用注解来应用的。对于大多数用例来说，这已经足够了，但对于其他用例，这种方法可能并不令人满意，因为配置是在源代码级别完成的。

这就是为什么 MicroProfile 容错注解的参数可以使用 MicroProfile Config 进行覆盖的原因。

注解参数可以通过配置属性进行覆盖，使用以下命名约定：`<类名>/<方法名>/<注解>/<参数>`。

要覆盖 `MyService` 类中 `doSomething` 方法上 `@Retry` 的 `maxDuration`，请按如下方式设置配置属性：

```
org.example.microservice.MyService/doSomething/Retry/maxDuration=3000
```

如果特定注解的参数需要为特定类配置相同的值，请使用 `<类名>/<注解>/<参数>` 配置属性进行配置。

例如，使用以下配置属性将 `MyService` 类上指定的所有 `@Retry` 的 `maxRetries` 覆盖为 100：

```
org.example.microservice.MyService/Retry/maxRetries=100
```

有时，需要为整个微服务（即部署中该注解的所有出现位置）配置相同的参数值。

在这种情况下，`<注解>/<参数>` 配置属性会覆盖指定注解的相应参数值。例如，要将所有 `@Retry` 的所有 `maxRetries` 覆盖为 30，请指定以下配置属性：

```
Retry/maxRetries=30
```

至此，我们关于 MicroProfile 中容错机制的讨论就结束了。

