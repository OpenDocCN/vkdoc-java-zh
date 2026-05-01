# 服务网格中的故障注入

Istio 允许你相当轻松地向结果中注入故障。这起初听起来适得其反，但却是测试容错处理是否正确的一种非常好的方法。以下 Istio 的 `VirtualService` 定义了这样的故障注入：

```
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: fault-injection-service
spec:
  hosts:
  - myTarget
  http:
  - route:
    - destination:
      host: myTarget
    fault:
      abort:
        httpStatus: 404
        percent: 20
     delay:
       percent: 50
       fixedDelay: 200ms
```

Istio 会监听对目标主机 `myTarget` 的调用，并对 20% 的调用发送 404 响应，而不是真实的响应码。除此之外，它还会将其他每个响应延迟 200 毫秒。

