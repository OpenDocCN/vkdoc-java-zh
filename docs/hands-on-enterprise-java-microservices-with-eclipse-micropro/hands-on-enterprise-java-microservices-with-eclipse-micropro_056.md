# 总结

在本章中，我们学习了如何使用 MicroProfile Config 来配置 MicroProfile 应用程序，以及如何使用 MicroProfile 容错机制来使它们更具弹性。

在 MicroProfile Config 中，配置的来源可以有很多；一些值来自属性文件，另一些来自系统属性或环境变量，但它们都可以从 Java 应用程序中一致地访问。这些值可能会根据部署环境（例如，测试和生产环境）而有所不同，但这在应用程序代码中是透明的。

MicroProfile 容错机制通过在代码中应用特定策略来帮助防止应用程序故障。它带有默认行为，但可以通过 MicroProfile Config 进行配置……

