# 在服务网格中使用 Eclipse MicroProfile 时的注意事项

诸如 Istio 或 LinkerD 之类的服务网格，在 Kubernetes 之上的平台层面提供服务，涵盖服务发现、路由和容错等领域。其中一些服务也可以在 MicroProfile 中找到。当你将 MicroProfile 应用程序部署到这样的服务网格中时，需要考虑是使用 MicroProfile 的版本还是网格的版本。

这里最可能受影响的 MicroProfile 特性是容错，尤其是重试逻辑。

