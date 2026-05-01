# MicroProfile 响应式流操作符

响应式流不仅仅需要将发布者连接到订阅者。通常，需要以某种方式操作流，例如应用包括 `map`、`filter` 和 `flatMap` 的操作。响应式流和 JDK 都没有提供执行这些操作的 API。由于用户不应自行实现响应式流，这意味着目前执行这些操作的唯一方法是依赖提供操作符的第三方库，例如 Akka Streams、RxJava 或 Reactor。

MicroProfile 响应式流操作符 API 旨在填补这一空白，以便 MicroProfile 应用开发者无需引入第三方依赖即可操作响应式流。...



