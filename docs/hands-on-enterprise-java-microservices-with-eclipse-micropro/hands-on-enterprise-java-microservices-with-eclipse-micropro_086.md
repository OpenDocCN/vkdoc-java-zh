# 计量器

计量器测量被装饰方法随时间被调用的速率。对于 JAX-RS 端点，这将是每秒的请求数。计量器可以通过注解声明：

```
@GET@Path("/m")@Metered(absolute = true)public String getMetered() {  return "Metered called";}
```

当客户端从计量器请求数据时，服务器提供平均速率，以及一分钟、五分钟和十五分钟的移动平均值。后者可能对一些读者来说很熟悉，类似于 Unix/Linux 的 `uptime` 命令。

