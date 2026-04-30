# 执行名为 joined.py 的测试脚本
grinder.script=joined.py
```

此属性文件中的注释非常清晰。你只需更改模拟用户数（`grinder.threads`）和测试脚本运行的周期数（`grinder.runs`）。请确保 Grinder 控制台将要运行的机器名称或 IP 地址正确无误。除此之外，你实际上无需更改此属性文件中的任何其他内容。

**测试脚本**

在清单 9-2 中，我们展示了在使用连接表继承方案录制会话时，由 HTTP 代理生成的 Jython 程序的核心部分。以粗体显示的思考时间语句通常表示录制测试脚本时从一个请求到下一个请求所花费的时间（以毫秒为单位）。如前所述，我们将原始值替换为零，但你也可以直接删除该行（参见清单 9-2）。

***清单 9-2.***  测试脚本的主部分

```
def __call__(self):
  """Called for every run performed by the worker thread."""
  self.page1()      # GET WineStoreJoined.jsp (request 101)

grinder.sleep(0)
  self.page2()      # POST WineStoreJoined.jsp (request 201)

grinder.sleep(0)
  self.page3()      # POST WineStoreJoined.jsp (request 301)

grinder.sleep(0)
  self.page4()      # POST WineStoreJoined.jsp (request 401)
```

作为页面方法的示例，我们在清单 9-3 中展示了页面 1 的方法（即测试脚本的第一个请求），以及第一个页面方法之前的一些定义。

***清单 9-3.***  页面方法示例

```
url0 = 'http://glassfish:8080'
request101 = createRequest(Test(101, 'GET WineStoreJoined.jsp'), url0)
class TestRunner:
  """A TestRunner instance is created for each worker thread."""

