# 覆盖全局默认值，每 5 秒抓取一次此任务的目标。
scrape_interval: 5s
static_configs:
- targets: ['0.0.0.0:9090']
- job_name: 'chapter18-boot'
metrics_path: '/monitoring/prometheus'

