# 问题

1.  MP-Config 设置会影响应用程序代码、MP 功能代码，还是两者都影响？
2.  您是否能够将 `health.pathToMonitor` 更新为有效路径，并看到更新的健康状态回复？
3.  Health 选项卡上的 `Svcs2` 健康状态链接（`http://localhost:8082/health`）显示的输出是什么？如果您停止 KeyCloak Docker 镜像并再次点击该链接，输出会改变吗？
4.  如果您在 Metrics 选项卡中选择 `MetricController.timed-request` 链接（`http://localhost:8080/metrics/application/io.packt.sample.metric.MetricController.timed-request`），但之前至少没有点击过一次 `Timed` 端点链接（`http://localhost:8080/demo1/metric/timed`），会发生什么？
5.  转到 RestClient 选项卡并点击链接，确保您有一个有效的 ...

