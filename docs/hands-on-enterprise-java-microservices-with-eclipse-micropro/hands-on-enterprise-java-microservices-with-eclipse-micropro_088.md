# 计时器

计时器基本上是直方图和计量器的组合，并且同样可以通过注解声明：

```
@GET@Path("/hello")@Timed(name="helloTime", absolute = true,        description = "Timing of the Hello call",       tags={"type=timer","app=shop"})public String getHelloTimed() {  try {    Thread.sleep((long) (Math.random()*200.0));  } catch (InterruptedException e) {     // 我们不在乎睡眠是否被中断。  }  return "Hello World";}
```

此示例中的代码会等待一小段随机时间，以使输出更有趣一些。

