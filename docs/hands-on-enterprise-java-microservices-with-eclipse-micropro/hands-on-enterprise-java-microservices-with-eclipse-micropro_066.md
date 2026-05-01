# 健康检查响应消息的变更

MicroProfile Health Check 3.0 引入了对健康检查 JSON 响应消息格式的更改。具体来说，字段 `outcome` 和 `state` 已被字段 `status` 取代。

此外，`@Health` 限定符在 Health Check 3.0 版本中已被弃用，同时引入了 `@Liveness` 和 `@Readiness` 限定符。对于这两个限定符，还引入了 `/health/live` 和 `/health/ready` 端点，分别用于调用所有存活性和就绪性过程。最后，为了向后兼容，`/health` 端点现在会调用所有带有 `@Health`、`@Liveness` 或 `@Readiness` 限定符的过程。

现在是时候讨论 JWT 传播了。

