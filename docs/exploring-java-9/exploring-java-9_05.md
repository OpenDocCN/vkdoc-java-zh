# 5. 进程 API

Java 进程 API 允许开发者创建和管理原生进程。现在，你可以使用 JDK 5 中的 `java.lang.ProcessBuilder` 来创建进程并重定向输出和错误流。Java 9 中的新接口 `java.lang.ProcessHandle` 允许你控制由 `ProcessBuilder.start()` 创建的原生进程。



## `ProcessHandle` 接口

`ProcessHandle` 提供的细粒度控制对于长时间运行的进程非常有用。表 5-1 展示了 `ProcessHandle` 的方法。通过 `ProcessHandle` 接口，你可以查询原生进程的信息并控制其生命周期。

表 5-1.

`ProcessHandle` 的方法

| 方法 | 描述 |
| --- | --- |
| `long pid()` | 返回原生进程 ID。 |
| `ProcessHandle.Info info()` | 返回 `ProcessHandle.Info`，其中包含进程的信息快照。 |
| `boolean isAlive()` | 检查进程是否存活。 |
| `Optional<ProcessHandle> parent()` | 返回父进程。 |
| `Stream<ProcessHandle> children()` | 返回所有子进程。 |
| `Stream<ProcessHandle> descendants()` | 返回所有后代进程。 |
| `boolean destroy()` | 终止进程。当成功请求终止时返回 `true`。 |
| `boolean destroyForcibly()` | 强制终止进程。当成功请求终止时返回 `true`。 |
| `boolean supportsNormalTermination()` | 检查进程是否可以使用 `destroy()` 正常终止。 |
| `CompletableFuture<ProcessHandle> onExit()` | 返回一个 `CompletableFuture<ProcessHandle>`，可用于在进程终止时运行自定义操作。 |

表 5-1 中的大多数方法都很直观。`info()` 的返回值是 `ProcessHandle.` `Info` 类的对象。表 5-2 展示了 `ProcessHandle.Info` 的方法。

表 5-2.

`ProcessHandle.Info` 的方法

| 方法 | 描述 |
| --- | --- |
| `Optional<String[]> arguments()` | 返回传递给进程的参数 |
| `Optional<String> command()` | 返回进程可执行文件的路径 |
| `Optional<String> commandLine()` | 返回启动进程的命令行 |
| `Optional<Instant> startInstant()` | 以 `java.time.Instant` 形式返回进程的启动时间 |
| `Optional<Duration> totalCpuDuration()` | 以 `java.time.Duration` 形式返回进程的总 CPU 时间 |
| `Optional<String> user()` | 返回此进程的用户 |

`ProcessHandle` 还有一些静态方法，用于获取与当前进程相关的 `ProcessHandle`；参见表 5-3。

表 5-3.

`ProcessHandle` 的静态方法

| 方法 | 描述 |
| --- | --- |
| `ProcessHandle current()` | 返回当前进程的 `ProcessHandle` |
| `Optional<ProcessHandle> of(long pid)` | 通过 PID 返回现有进程的 `ProcessHandle` |
| `Stream<ProcessHandle> allProcesses()` | 返回当前进程可见的所有进程的快照 |

在清单 5-1 中，方法 `printProcessInfo()` 打印出当前原生 `java` 进程的信息。

```
import java.util.Arrays;
public class CurrentProcess {
public static void main(String[] args) {
new CurrentProcess().printProcessInfo(ProcessHandle.current());
}
private void printProcessInfo(final ProcessHandle processHandle) {
final ProcessHandle.Info info = processHandle.info();
System.out.println("Process info =>");
System.out.format("PID: %s%n", processHandle.pid());
info.arguments().ifPresent(args ->
System.out.format("Arguments: %s%n", Arrays.toString(args)));
info.command().ifPresent(command ->
System.out.format("Command: %s%n", command));
info.commandLine().ifPresent(commandLine ->
System.out.format("Command line: %s%n", commandLine));
info.startInstant().ifPresent(startInstant ->
System.out.format("Start time: %s%n", startInstant));
info.totalCpuDuration().ifPresent(cpuDuration ->
System.out.format("CPU time: %s%n", cpuDuration));
info.user().ifPresent(user ->
System.out.format("User: %s%n", user));
}
}
清单 5-1.
打印原生进程信息
```

当使用 `java` 运行时，输出类似于清单 5-2。

```
Process info =>
PID: 14946
Arguments: [-cp, feature9, io.vividcode.feature9.process.CurrentProcess]
Command: /Library/Java/JavaVirtualMachines/jdk-9.jdk/Contents/Home/bin/java
Command line: /Library/Java/JavaVirtualMachines/jdk-9.jdk/Contents/Home/bin/java
-cp feature9 io.vividcode.feature9.process.CurrentProcess
Start time: 2017-05-05T04:01:22.686Z
CPU time: PT0.217612S
User: alexcheng
清单 5-2.
当前 java 进程信息的输出
```

## `Process`

`Process` 也有几个新方法；参见表 5-4。其中一些方法与 `ProcessHandle` 中的方法具有相同的名称和功能。

表 5-4.

`Process` 的方法

| 方法 | 描述 |
| --- | --- |
| `long pid()` | 返回原生进程 ID |
| `ProcessHandle.Info info()` | 返回 `ProcessHandle.Info`，其中包含进程的信息快照 |
| `boolean isAlive()` | 检查进程是否存活 |
| `ProcessHandle toHandle()` | 返回此进程的 `ProcessHandle` |
| `Stream<ProcessHandle> children()` | 返回所有子进程 |
| `Stream<ProcessHandle> descendants()` | 返回所有后代进程 |
| `boolean supportsNormalTermination()` | 检查进程是否可以使用 `destroy()` 正常终止 |
| `CompletableFuture<ProcessHandle> onExit()` | 返回一个 `CompletableFuture<ProcessHandle>`，可用于在进程终止时运行自定义操作 |

## 管理长时间运行的进程

借助 `ProcessHandle` 的新方法 `onExit()`，现在管理长时间运行的进程变得更加容易。清单 5-3 展示了一个简单的示例。在方法 `start()` 中，我使用命令 `top` ( [`https://linux.die.net/man/1/top`](https://linux.die.net/man/1/top) ) 创建了一个新进程，并返回该进程的 `ProcessHandle`。在方法 `waitFor()` 中，我使用 `onExit()` 返回的 `CompletableFuture` 对象的 `whenCompleteAsync()` 方法来添加一个在进程退出时调用的处理器。在处理器中，我可以获取已终止进程的 `ProcessHandle` 对象。这里我只是输出了该进程的 PID。变量 `shutdownThread` 代表一个线程，该线程在延迟一秒钟后销毁进程。我可以使用 `supportsNormalTermination()` 检查 `ProcessHandle` 是否支持优雅终止，并尝试优雅地终止它。我使用 `CountDownLatch` 来等待异步完成处理器执行完毕。

```
public class LongRunningProcess {
public ProcessHandle start() throws IOException {
final ProcessBuilder processBuilder = new ProcessBuilder("top")
.inheritIO();
return processBuilder.start().toHandle();
}
public void waitFor(final ProcessHandle processHandle) {
final CountDownLatch latch = new CountDownLatch(1);
processHandle.onExit().whenCompleteAsync((handle, throwable) -> {
if (throwable == null) {
System.out.println(handle.pid());
} else {
throwable.printStackTrace();
}
latch.countDown();
});
final Thread shutdownThread = new Thread(() -> {
try {
Thread.sleep(1000);
} catch (final InterruptedException e) {
e.printStackTrace();
}
if (processHandle.supportsNormalTermination()) {
processHandle.destroy();
} else {
processHandle.destroyForcibly();
}
});
shutdownThread.start();
try {
shutdownThread.join();
latch.await();
} catch (final InterruptedException e) {
e.printStackTrace();
}
}
public static void main(final String[] args) {
final LongRunningProcess longRunningProcess = new LongRunningProcess();
try {
longRunningProcess.waitFor(longRunningProcess.start());
} catch (final IOException e) {
e.printStackTrace();
}
}
}
清单 5-3.
管理长时间运行的进程
```

当你运行清单 5-3 中的代码时，你应该会看到 `top` 命令的输出大约持续一秒钟，然后显示进程的 PID，程序终止。



## 总结

在本章中，我们讨论了新的 `ProcessHandle` API，它能够提供对原生进程的控制。我还向你展示了如何使用这个新 API 来管理长时间运行的进程。在下一章中，我们将讨论平台日志记录 API 和服务。

