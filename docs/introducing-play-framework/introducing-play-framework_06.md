# 注释

key = value

默认情况下，Play 应用程序开始监听端口 9000 上的 HTTP 请求。通过在 `application.conf` 文件中添加 `http.port` 条目，可以更改此默认端口：

#http.port =8085

`application.conf` 文件中定义的默认日志级别如下。在生产环境中，请记得将其更改为适当的值。通常的做法是将其设置为 ERROR。

