# 当前的 MicroProfile 实现

在撰写本文时，共有八个 Eclipse MicroProfile 实现，全部都是开源的。以下是这些实现的表格：

| **开源项目基础** | **项目地址** | **支持供应商** |
| Thorntail ([`thorntail.io/`](http://thorntail.io/)) | [`github.com/thorntail/thorntail`](https://github.com/thorntail/thorntail) | Red Hat |
| Open Liberty ([`openliberty.io/`](https://openliberty.io/)) | [`github.com/openliberty`](https://github.com/openliberty) | IBM |
| Apache TomEE ([`tomee.apache.org/`](http://tomee.apache.org/)) | [`github.com/apache/tomee`](https://github.com/apache/tomee) | Tomitribe |
| Payara Micro ([`www.payara.fish/payara_micro`](https://www.payara.fish/payara_micro)) | [`github.com/payara/Payara`](https://github.com/payara/Payara) | Payara Services Ltd. |
| Hammock ([`hammock-project.github.io/`](https://hammock-project.github.io/)) | [`github.com/hammock-project`](https://github.com/hammock-project) | Hammock |
| KumuluzEE ([`ee.kumuluz.com/`](https://ee.kumuluz.com/)) | [`github.com/kumuluz`](https://github.com/kumuluz) | KumuluzEE |
| Launcher ([`github.com/fujitsu/launcher`](https://github.com/fujitsu/launcher)) | [`github.com/fujitsu/launcher`](https://github.com/fujitsu/launcher) | Fujitsu |
| Helidon ([`helidon.io/#`](https://helidon.io/#)) | [`github.com/oracle/helidon`](https://github.com/oracle/helidon) | Oracle |

其中一些实现基于*应用服务器*，例如 Payara 和 Open Liberty，而另一些则基于*应用组装器*，后者仅包含应用程序所需的功能，无需启动并运行应用服务器，并且通常生成可执行的 JAR。然而，基于应用服务器的实现也能够生成可执行的 JAR。

应用组装器可以生成一个 *uberjar*（一个自包含的可运行 JAR 文件），或者一个*应用 jar*，其运行时依赖项位于子目录中，例如，一个附带的 `lib` 或 `libs` 子目录。

通过了整个伞式版本或特定版本 MicroProfile API 的 MicroProfile **测试兼容性套件**（**TCK**）的 Eclipse MicroProfile 实现，会列在 [`wiki.eclipse.org/MicroProfile/Implementation`](https://wiki.eclipse.org/MicroProfile/Implementation) 上。目前，列入此列表采用诚信制度，因为它不需要提供 TCK 结果的证明；只需要发布者声明其实现已通过 TCK。

该项目还有一个站点，组织/团体可以在 MicroProfile 生产部署列表中自行添加。此列表可在 [`wiki.eclipse.org/MicroProfile/Adoptions`](https://wiki.eclipse.org/MicroProfile/Adoptions) 找到。

在接下来的章节中，我们将简要介绍这些实现以及如何获取每个实现的更多信息。



