# 使用约定

本书通篇使用了许多文本约定。

`CodeInText`：表示文本中的代码词、数据库表名、文件夹名、文件名、文件扩展名、路径名、虚拟 URL、用户输入和 Twitter 句柄。示例如下："`checks` 数组对象类型由一个必需的 `name` 和 `status` 字符串，以及一个可选的 `data` 对象组成，该对象包含可选的 `key` 和 `value` 对。"

代码块设置如下：

```
package org.eclipse.microprofile.health;@FunctionalInterfacepublic interface HealthCheck {  HealthCheckResponse call();}
```

任何命令行输入或输出编写如下：

```
Scotts-iMacPro:jwtprop starksm$ curl http://localhost:8080/jwt/secureHello; echoNot authorized
```

**粗体**：表示新术语...

