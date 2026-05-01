# 功能注册

如果注册的提供者类型是 JAX-RS `Feature`，则该 `Feature` 设置的优先级也将成为构建器的一部分。实现会维护已注册提供者的整体优先级，无论它们是如何注册的。`Feature` 将用于在运行时注册额外的提供者，并且可以通过 `@RegisterProvider`、配置或 `RestClientBuilder` 进行注册。`Feature` 将立即执行。因此，其优先级不被考虑（功能总是被执行）。

