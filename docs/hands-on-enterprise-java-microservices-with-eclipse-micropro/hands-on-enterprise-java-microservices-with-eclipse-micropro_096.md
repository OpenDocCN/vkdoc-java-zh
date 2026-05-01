# 数据输出格式的变化

MicroProfile Metrics 2.0 中多标签指标的引入，要求改变提供给客户端的指标数据格式。

Prometheus 格式也存在一些不一致之处，因此我们决定以有时不兼容的方式改进这些格式：

*   作用域和指标名称之间的冒号（:）分隔符已改为下划线（_）。
*   Prometheus 输出格式不再要求将驼峰式命名转换为蛇形命名。
*   垃圾收集器的基本指标格式已更改，现在为各种垃圾收集器使用标签。

请查阅 MicroProfile 2.0 规范中的发布说明，网址为 [`github.com/eclipse/microprofile-metrics/releases/tag/2.0 ...`](https://github.com/eclipse/microprofile-metrics/releases/tag/2.0)

