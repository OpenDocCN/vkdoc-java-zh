# 在混合云部署中使用 MicroProfile 时的注意事项

混合云应用包含本地逻辑和云端逻辑。换句话说，如果你的部分应用逻辑在本地运行，部分在云端运行，那么你实际上就拥有了一个混合云应用。在这种类型的部署中使用 Eclipse MicroProfile 时，你需要考虑以下几点：

*   需要使用云环境支持的任何 DNS 支持来配置云环境和本地环境之间的通信路由
*   配置 MicroProfile OpenTracing 以支持跨云环境捕获追踪信息
*   监控跨云的、分散的 MicroProfile Metrics 信息...



