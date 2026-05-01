# 使用 Prometheus 检索指标

现在我们已经了解了公开了哪些指标以及如何定义我们自己的指标，让我们看看如何将它们收集到**时序数据库**（**TSDB**）中。为此，我们使用 Prometheus，这是一个 CNCF（[`www.cncf.io/`](https://www.cncf.io/)）项目，已在云原生世界中得到广泛采用。

您可以从 [`prometheus.io`](https://prometheus.io/) 下载 Prometheus，或者在 macOS 上通过 `brew install prometheus` 安装。

下载 Prometheus 后，我们需要一个配置文件来定义要抓取的目标，然后才能启动服务器。出于我们的目的，我们将使用以下简单的文件：

```
.Prometheus configuration for a Thorntail Server, prom.ymlscrape_configs:# Configuration to poll from Thorntail- job_name: 'thorntail' ...
```

