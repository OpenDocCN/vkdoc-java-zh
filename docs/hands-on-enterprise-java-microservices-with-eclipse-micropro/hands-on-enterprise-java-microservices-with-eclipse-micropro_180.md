# MicroProfile 上下文传播

本规范位于 MicroProfile 平台版本体系之外，目前仍处于提议或草案阶段。我们将在本章稍后的*MicroProfile 未来发展*部分更详细地讨论此规范，但在此先进行一个高层级的介绍。

MicroProfile 上下文传播规范引入了用于在非线程关联的工作单元之间传播上下文的 API。它使得传播传统上与当前线程关联的上下文成为可能，这些上下文可以跨越各种类型的工作单元，例如 `CompletionStage`、`CompletableFuture`、`Function` 和 `Runnable`，无论最终由哪个特定线程执行它们。

