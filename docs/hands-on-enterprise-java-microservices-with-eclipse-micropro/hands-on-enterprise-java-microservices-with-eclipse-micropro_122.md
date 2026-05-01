# MicroProfile Config 集成

对于 CDI 定义的接口，可以使用 MicroProfile Config 属性来定义通过 `RestClientBuilder` API 可用的额外行为。以我们的 `io.pckt.restc.contract.WorldClockApi` 接口为例，以下 MicroProfile Config 属性可用于控制生成的代理行为：

*   `io.pckt.restc.contract.WorldClockApi/mp-rest/url`：此服务使用的基础 URL，相当于 `RestClientBuilder#baseUrl` 方法。
*   `io.pckt.restc.contract.WorldClockApi/mp-rest/scope`：用于注入的 CDI 作用域的完全限定类名；默认为 `javax.enterprise.context.Dependent`。
*   `io.pckt.restc.contract.WorldClockApi/mp-rest/providers`：要包含在客户端中的提供者完全限定类名的逗号分隔列表，相当于 `RestClientBuilder#register` 方法或 `@RegisterProvider` 注解。
*   `io.pckt.restc.contract.WorldClockApi/mp-rest/providers/com.mycompany.MyProvider/priority`：这将覆盖此接口的 `com.mycompany.MyProvider` 提供者的优先级。
*   `io.pckt.restc.contract.WorldClockApi/mp-rest/connectTimeout`：等待连接到远程端点的超时时间，以毫秒为单位。
*   `io.pckt.restc.contract.WorldClockApi/mp-rest/readTimeout`：等待远程端点响应的超时时间，以毫秒为单位。

