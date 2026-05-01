# OpenTracing 项目

OpenTracing 项目 ([`opentracing.io`](https://opentracing.io/)) 提供了一个供应商中立的规范 [(https://github.com/opentracing/specification)](https://github.com/opentracing/specification) 和用于描述分布式事务的多语言 API。供应商中立性很重要，因为在大型组织中启用分布式追踪时，代码检测是最耗时且最具挑战性的任务。我们想强调，OpenTracing 只是一个 API。实际部署将需要一个插入式的追踪器实现，该实现在被监控的进程内运行，并将数据发送到追踪系统。

从 API 的角度来看，有三个关键概念：Tracer、Span 和 SpanContext。Tracer 是应用程序可用的单例对象，可用于建模...

