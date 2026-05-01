# 16. 杂项

本章涵盖了 Java 9 中的各种变更。

## 小的语言变更

Java 9 增加了一些小的语言变更。

### 私有接口方法

现在可以在 Java 9 的接口中添加私有方法。清单 16-1 中的 `SayHi` 接口包含一个私有方法 `buildMessage()`，用于生成默认方法 `sayHi()` 中使用的默认消息。

```
public interface SayHi {
private String buildMessage() {
return "Hello";
}
void sayHi(final String message);
default void sayHi() {
sayHi(buildMessage());
}
}
清单 16-1.
私有接口方法
```

### 其他变更

Java 9 中还有其他一些变更：

*   下划线 (`_`) 不能再用作标识符。这样做现在会导致编译错误。
*   `@SafeVarargs` 现在可以用于私有实例方法。

## 堆栈遍历 API

`java.lang.StackWalker` 是 Java 9 中新增的类，用于遍历堆栈跟踪，支持过滤和延迟访问。

首先，你使用静态方法 `getInstance()` 创建 `StackWalker` 的新实例。使用 `getInstance()` 时，你可以传递一个或多个在枚举 `StackWalker.Option` 中定义的选项；参见表 16-1。你也可以传递一个估计的遍历深度。

表 16-1.

`getInstance()` 的选项

| 选项 | 描述 |
| --- | --- |
| `RETAIN_CLASS_REFERENCE` | 在 `StackFrame` 中保留 `Class` 对象 |
| `SHOW_HIDDEN_FRAMES` | 显示所有隐藏的帧 |
| `SHOW_REFLECT_FRAMES` | 显示所有反射帧 |

获取 `StackWalker` 实例后，你可以使用方法 `<T> T walk(Function<? super Stream<StackWalker.StackFrame>,? extends T> function)` 遍历当前线程的堆栈帧。唯一的参数是一个函数，该函数应用于 `StackWalker.StackFrames` 的流并返回一个值。当 `walk()` 方法返回时，该流被关闭，无法再次使用。`StackWalker.StackFrame` 有多种方法用于检索堆栈帧的信息；参见表 16-2。

表 16-2.

`StackWalker.` `StackFrame` 的方法

| 方法 | 描述 |
| --- | --- |
| `String getClassName()` | 返回声明该方法的类的二进制名称 |
| `String getMethodName()` | 返回该方法的名称 |
| `Class<?> getDeclaringClass()` | 返回声明该方法的 `Class` |
| `int getByteCodeIndex()` | 返回包含执行点的 `Code` 属性的代码数组的索引 |
| `String getFileName()` | 返回源文件的名称 |
| `int getLineNumber()` | 返回源代码行的行号 |
| `boolean isNativeMethod()` | 如果该方法是 native 方法，则返回 `true` |
| `StackTraceElement toStackTraceElement()` | 返回 `StackTraceElement` |

如果在创建 `StackWalker` 时没有传递 `RETAIN_CLASS_REFERENCE` 选项，则方法 `getDeclaringClass()` 会抛出 `UnsupportedOperationException`。

在清单 16-3 中，我创建了一个带有 `RETAIN_CLASS_REFERENCE` 选项的 `StackWalker`，这样我就可以使用 `getDeclaringClass()` 方法来获取 `Class` 对象。这里我使用 `getClassName()` 来获取类名并将它们收集到一个 `Set` 中。我验证了这个集合应该包含当前类 `StackWalkingTest`。

```
public class StackWalkingTest {
@Test
public void testWalkClass() throws Exception {
final StackWalker stackWalker =
StackWalker.getInstance(Option.RETAIN_CLASS_REFERENCE);
final Set classNames = stackWalker.walk(stream ->
stream.map(StackWalker.StackFrame::getClassName)
.collect(Collectors.toSet())
);
assertTrue(classNames.contains("StackWalkingTest"));
}
}
清单 16-3.
StackWalker 中 walk() 的示例
```

如果我只需要遍历所有 `StackFrame` 并对每个帧执行操作，那么方法 `void forEach(Consumer<? super StackWalker.StackFrame> action)` 比 `walk()` 更容易使用；参见清单 16-4。

```
@Test
public void testForEach() throws Exception {
final StackWalker stackWalker =
StackWalker.getInstance(Set.of(Option.SHOW_HIDDEN_FRAMES,
Option.SHOW_REFLECT_FRAMES));
stackWalker.forEach(stackFrame -> System.out.printf("%6d| %s -> %s %n",
stackFrame.getLineNumber(),
stackFrame.getClassName(),
stackFrame.getMethodName()));
}
清单 16-4.
StackWalker 中 forEach() 的示例
```

`StackWalker` 还有一个方法 `getCallerClass()`，它返回调用者的 `Class` 对象，即调用 `getCallerClass()` 的方法的调用者。`getCallerClass()` 的描述看起来相当复杂且难以理解。这个方法实际上对于处理类、类加载器和资源的工具方法非常有用。这些工具方法应该使用调用者的类来执行操作。



清单 16-5 中的`LoggerManager`创建了新的`System.Logger`实例。这些日志记录器的名称基于方法`getLogger()`的调用者类。必须为`getCallerClass()`配置`RETAIN_CLASS_REFERENCE`选项才能使其正常工作。`getCallerClass()`始终会忽略反射帧和隐藏帧。

```
public class LoggerManager {
private final static StackWalker walker =
StackWalker.getInstance(Option.RETAIN_CLASS_REFERENCE);
public static System.Logger getLogger() {
final Class caller = walker.getCallerClass();
return System.getLogger(
String.format("%s/%s", caller.getModule().getName(), caller.getName()));
}
}
清单 16-5.
LoggerManager
```

在清单 16-6 中，我使用`LoggerManager.getLogger()`创建了一个新的日志记录器并验证其名称。由于测试类位于未命名模块中，模块名称为`null`。`StackWalkingTest`是类名。日志记录器名称为`null/StackWalkingTest`。

```
@Test
public void testGetCallerClass() throws Exception {
final System.Logger logger = LoggerManager.getLogger();
assertEquals("null/StackWalkingTest", logger.getName());
logger.log(Level.INFO, "Hello World");
}
清单 16-6.
测试 LoggerManager
```

## Objects

在 Java 9 中，`java.util.Objects`新增了更多方法：

*   `T requireNonNullElse(T obj, T defaultObj)`：如果第一个参数非空则返回它，否则返回第二个非空参数。
*   `T requireNonNullElseGet(T obj, Supplier<? extends T> supplier)`：如果第一个参数非空则返回它，否则使用`Supplier`获取非空值。
*   `int checkIndex(int index, int length)`：检查`index`是否在从`0`（包含）到`length`（不包含）的范围内。可用于检查数组访问。如果`index`在范围内则返回该值，否则抛出`IndexOutOfBoundsException`。
*   `int checkFromIndexSize(int fromIndex, int size, int length)`：检查从`fromIndex`（包含）到`fromIndex + size`（不包含）的子范围是否在从`0`（包含）到`length`（不包含）的范围内。可用于检查子数组访问。如果子范围在范围内则返回`fromIndex`，否则抛出`IndexOutOfBoundsException`。
*   `int checkFromToIndex(int fromIndex, int toIndex, int length)`：检查从`fromIndex`（包含）到`toIndex`（不包含）的子范围是否在从`0`（包含）到`length`（不包含）的范围内。如果子范围在范围内则返回`fromIndex`，否则抛出`IndexOutOfBoundsException`。

清单 16-7 展示了这些新方法的一些示例。

```
public class TestObjects {
@Test
public void testRequireNonNullElse() throws Exception {
assertEquals("hello", Objects.requireNonNullElse("hello", "world"));
assertEquals("world", Objects.requireNonNullElse(null, "world"));
}
@Test(expected = IndexOutOfBoundsException.class)
public void testCheckIndex() throws Exception {
assertEquals(0, Objects.checkIndex(0, 1));
Objects.checkIndex(3, 1);
assertEquals(1, Objects.checkFromIndexSize(0, 2, 5));
Objects.checkFromIndexSize(0, 3, 1);
assertEquals(1, Objects.checkFromToIndex(1, 3, 5));
Objects.checkFromToIndex(0, 3, 2);
}
}
清单 16-7.
Objects 中的方法示例
```

## Unicode 8.0

Java 9 将其对 Unicode 的支持升级到了 8.0（[`www.unicode.org/versions/Unicode8.0.0/`](http://www.unicode.org/versions/Unicode8.0.0/)），相比 Java 8 中的 Unicode 6.2 升级了两个主要版本。在 Unicode 8.0 中，你可以使用表情符号（[`www.unicode.org/emoji/`](http://www.unicode.org/emoji/)）。此次升级还为从右到左的语言（如阿拉伯语和希伯来语）带来了更好的文本显示效果。

## UTF-8 属性资源包

长期以来，Java 平台中的资源包一直使用 ISO-8859-1 编码来处理属性文件。属性文件需要使用工具`native2ascii`（[`https://docs.oracle.com/javase/8/docs/technotes/tools/unix/native2ascii.html`](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/native2ascii.html)）进行转换，以转义 Unicode 字符。直接编辑这些属性文件是不可能的。

在 Java 9 中，`ResourceBundle`加载属性文件的默认文件编码已从 ISO-8859-1 更改为 UTF-8。应用程序可以直接使用 UTF-8 属性文件，无需转换。在清单 16-8 中，我使用`ResourceBundle`加载了一个包含中文字符的属性文件。

```
public class UTF8ResourceBundleTest {
@Test
public void testGetProperty() {
final ResourceBundle resourceBundle = ResourceBundle.getBundle("demo");
assertEquals("你好", resourceBundle.getString("greeting"));
}
}
清单 16-8.
UTF-8 资源包
```

你可以使用系统属性`java.util.PropertyResourceBundle.encoding`覆盖默认的资源包编码。可能的编码为`ISO-8859-1`和`UTF-8`。



## 增强的弃用机制

注解 `@Deprecated` 用于标记某个程序元素为已弃用且不应再使用。在 Java 9 中，`@Deprecated` 得到了增强，新增了两个属性。属性 `since` 用于指明被注解元素从哪个版本开始弃用。版本字符串的格式与 javadoc 标签 `@since` 相同。`since` 的默认值为空字符串。属性 `forRemoval` 用于指明被注解的元素是否会在未来版本中被移除。默认值为 `false`。如果属性 `forRemoval` 为 `false`，则被注解的元素至少会在下一个主要版本中继续存在。如果属性 `forRemoval` 为 `true`，则被注解元素将在哪个未来版本中被移除尚不明确。它可能是下一个主要版本，也可能是在几个主要版本之后。`@Deprecated` 注解和 javadoc 标签 `@deprecated` 应始终同时出现在已弃用的元素上。

有了这两个新属性，开发者在遇到已弃用的元素时，就能更容易地知道应该采取什么行动。如果某个已弃用元素的 `forRemoval` 为 `true`，那么他们就需要更新使用该元素的代码。否则，当该元素在未来版本中被移除时，代码就会出错。

在 Java 9 中，许多地方的 `@Deprecated` 注解都已更新，包含了这两个属性。例如，在 `java.lang.Thread` 类中，方法 `suspend()`、`resume()` 和 `stop()` 被标记为 `@Deprecated(since="1.2")`，这意味着这些方法自 Java 1.2 起就已弃用，但它们至少会在下一个主要版本中继续存在。方法 `countStackFrames()`、`destroy()` 和 `stop(Throwable obj)` 被标记为 `@Deprecated(since="1.2", forRemoval=true)`，因此这些方法将在未来版本中被移除。

当使用已弃用的元素时，编译器会发出警告。随着 `forRemoval` 的引入，现在有两种类型的弃用警告。普通的弃用警告针对的是 `forRemoval` 设置为 `false` 的 `@Deprecated`；终结性弃用警告针对的是 `forRemoval` 设置为 `true` 的 `@Deprecated`。在 Java 9 之前，你可以使用 `@SuppressWarnings("deprecation")` 来抑制弃用警告。在 Java 9 中，`@SuppressWarnings("deprecation")` 只能抑制普通的弃用警告。要抑制终结性弃用警告，你需要使用 `@SuppressWarnings("removal")`。

你可以使用工具 `jdeprscan` 来扫描已弃用 API 的使用情况。`jdeprscan` 支持扫描作为包层次结构根目录的目录、JAR 文件和类文件。通过使用 `--for-removal` 选项，你可以将扫描结果限制为那些因即将被移除而弃用的 API。`jdeprscan -l` 可以打印出已弃用 API 的集合。

以下命令扫描了 Guava 的 JAR 文件：

```
$ jdeprscan /guava-21.0.jar
```

清单 16-9 展示了扫描结果。

```
class com/google/common/io/FileBackedOutputStream$1 overrides
deprecated method java/lang/Object::finalize()V
class com/google/common/reflect/Element uses deprecated
method java/lang/reflect/AccessibleObject::isAccessible()Z
class com/google/common/reflect/Element overrides deprecated
method java/lang/reflect/AccessibleObject::isAccessible()Z
class com/google/common/util/concurrent/AtomicDoubleArray uses deprecated method
java/util/concurrent/atomic/AtomicLongArray::weakCompareAndSet(IJJ)Z
清单 16-9.
Guava 的 jdeprscan 扫描结果
```

## NetworkInterface

`java.net.NetworkInterface` 类新增了三个与网络接口相关的方法：

*   `Stream<NetworkInterface> networkInterfaces()`：静态方法；返回一个 `NetworkInterfaces` 的流
*   `Stream<InetAddress> inetAddresses()`：返回绑定到此网络接口的 `InetAddresses` 的流
*   `Stream<NetworkInterface> subInterfaces()`：返回附加到此网络接口的子接口的流

清单 16-10 展示了如何使用这三个新方法。它显示了所有网络接口的信息。

```
public class NetworkInterfaceDemo {
public static void main(final String[] args) throws SocketException {
final String allInterfaces = NetworkInterface.networkInterfaces().map(
networkInterface ->
String.format("Name:%s%nHost addresses:%s%nSub-interfaces:%s",
networkInterface.getDisplayName(),
networkInterface.inetAddresses().map(InetAddress::getHostAddress)
.collect(Collectors.joining(", ")),
networkInterface.subInterfaces().map(NetworkInterface::getName)
.collect(Collectors.joining(", ")))
).collect(Collectors.joining("\n\n"));
System.out.println(allInterfaces);
}
}
清单 16-10.
NetworkInterface 中新方法的使用
```

## 总结

作为本书的最后一章，本章涵盖了 Java 9 中那些规模较小、不足以单独成章的变化，包括小的语言变化、栈遍历 API、UTF-8 属性资源包、增强的弃用机制以及其他一些小改动。



索引 A 抽象语法树（ASTs） 迁移实战 biojava 构建，使用 Java 9 迁移路径的项目 add()，jshell Apache Maven 进程 API 管理长时间运行的进程 printProcessInfo() ProcessHandle.Info ProcessHandle 方法 ProcessHandle 静态方法 process 方法 AppForegroundListener AppHiddenListener Applet API Appletviewer 工具 应用程序事件 AppReopenedListener arrayConstructor，增强型方法句柄() arrayLength()，增强型方法句柄() 数组方法 compare() equals() mismatch() 工件模块 JAR 文件 JDK 模块 Async()，并发 原子类，并发 原子更新方法 自动模块名称，Java API 自动模块 B BinaryAndOctalLiterals()，ECMAScript 6 按位原子更新方法 打破封装，JDK 工具 ByteBuffer，访问模式 C Chronounit，并发 CircleCI 类，Java API 类加载器，Java API CLOSE_ALL_WINDOWS CMS 参见并发标记清除（CMS） 集合方法 List.of() Map.of() 和 Map.ofEntries() Set.of() 收集器方法 filtering() flatMapping() Compare()，数组 并发 Async() 原子类 chronounit 队列 Thread.onSpinWait() Timeout() TimeUnit 工具方法 并发标记清除（CMS） 配置，Java API 核心接口 Flow.Processor<T,R> Flow.Publisher<T> Flow.Subscriber<T> Flow.Subscription counterLoop()，增强型方法句柄 D 声明 模块 开放模块，包 限定导出 requires 和 exports 服务 静态依赖 传递依赖 DefaultLoggerFinder 弃用，杂项 桌面应用 关于窗口 应用程序事件 应用程序退出功能 打开文件 打开 URI 偏好设置窗口 打印文件 确定性随机位生成器（DRBG）算法 诊断 contains 方法，解析 诊断命令，JVM Docker doWhileLoop()，增强型方法句柄 DRBG 算法 DrbgParameters dropWhile()，流 E Eclipse Java 9 支持（BETA） ECMAScript 6 BinaryAndOctalLiterals() function () iterators 和 for..of 循环 TemplateString() Empty()，增强型方法句柄 增强型方法句柄 arrayConstructor() arrayLength() empty() loops() try finally() varHandleExactInvoker varHandleInvoker zero() Equals()，数组 F filtering()，收集器 过滤输入流 findStaticVarHandle findVarHandle flatMapping()，收集器 Flow.Processor<T,R> Flow.Publisher<T> Flow.Subscriber<T> Flow.Subscription ForkJoinPool.commonPool() for..of 循环，ECMAScript 6 Function ()，ECMAScript 6 FunctionLengthAnalyzer()，Nashorn G 垃圾回收器（GC） CMS 默认 G1 已移除的组合 getInstance()，栈遍历 API getMaxBufferCapacity() getPlatformClassLoader() Gradle H handleQuitRequestWith() hprof 代理，JVM I ifPresentOrElse()，Optional 输入流 安装 Eclipse JDK 9 集成开发环境（IDE） Intellij IDE I/O 输入流 ObjectInputstream 过滤器 IteratedLoop()，增强型方法句柄 iterate()，流 Iterators，ECMAScript 6 J JAR 文件，工件模块 java，JDK 工具 Java 8，诊断命令 Java 9 CircleCI Docker IDE 安装 工具 Java API 自动模块名称 类 类加载器 配置 ModuleDescriptor ModuleFinder 模块层 ModuleReader 方法 ModuleReference 可读性图 反射 ResolvedModule 方法 Javac Docker JDK 工具 javadoc 工具，JVM java.logging 模块 Java 虚拟机（JVM） 并发标记清除 默认 GC 诊断命令 GC 组合 hprof 代理 javadoc 工具 mJRE 工具接口 统一日志系统 日志配置 标签，级别，装饰，输出 诊断命令 VM.log jdeps，JDK 工具 JDK 模块，工件模块 JDK 工具 打破封装 java javac jdeps jlink 可观察模块 根模块 升级模块路径 模块路径 模块版本 jhat 工具，JVM jlink，JDK 工具 jshell /! add() 类 /drop /edit /env /exit /help /history /imports $1.length() /list /<id> /-<n> /methods /open /reload /reset /save /set System.getP /types var /vars K 密钥库 L 语言变更，杂项变更 私有接口方法 try-with-resources 语句 List.of()，集合 LoggerFinder LoggerManager 日志记录 API 和服务 DefaultLoggerFinder LoggerFinder SLF4J SLF4JLoggerFinder SLF4JLoggerWrapper System.getLogger() System.LoggerFinder System.Logger 方法 日志配置 日志消息 日志消息 Loop()，增强型方法句柄 M 管理长时间运行的进程 Map.of() 和 Map.ofEntries()，集合 内存屏障方法 内存顺序，变量访问模式 杂项 弃用 语言变更 其他变更 私有接口方法 try-with-resources 语句 网络接口对象 栈遍历 API Unicode 8.0 UTF-8 资源包 Mismatch()，数组 ModuleDescriptor ModuleFinder，Java API 模块层，Java API 模块路径 ModuleReader 方法，Java API ModuleReference，Java API 模块系统 迁移实战 biojava 构建项目，Java 9 迁移路径 应用程序 工件模块 JAR 文件 JDK 模块 常见问题 声明 开放模块，包 限定导出 requires 和 exports 服务 静态依赖 传递依赖 定义 Java API 自动模块名称 类 类加载器 配置 ModuleDescriptor ModuleFinder 模块层 ModuleReader 方法 ModuleReference 可读性图 反射 ResolvedModule 方法 JDK 工具 打破封装 常见模块 可观察模块 根模块 升级模块路径 模块路径 模块版本 JPMS 处理现有代码 自动模块 未命名模块 模块版本 多版本 JRE（mJRE） 多分辨率图像，用户界面 N Nashorn ECMAScript 6 BinaryAndOctalLiterals() function () iterators 和 for..of 循环 TemplateString() FunctionLengthAnalyzer() 解析器 API 基本解析 解析错误 ScriptEngine 网络接口，杂项 NORMAL_EXIT 数值原子更新方法 O ObjectInputFilter.Config ObjectInputFilter.FilterInfo 方法 ObjectInputFilter.Status 值 ObjectInputstream 过滤器 对象，杂项 ofNullable()，流 Optional 方法 ifPresentOrElse() Optional.or() stream() Optional.or()，Optional P 解析器 API 基本解析 解析错误 解析错误 PeriodicPublisher PKCS12 偏好设置窗口 printProcessInfo() 私有接口方法 ProcessHandle 静态方法 ProcessHandle.Info 方法 ProcessHandle 方法 Process 方法 Q 可观察模块 队列，并发 QuitHandler R 响应式流 核心接口 互操作性 Reactor RxJava 2 SubmissionPublisher Reactor 可读性图，Java API 读取访问模式 ReadAllBytes()，输入流 读取-求值-打印循环（REPL） 读取和复制数据，输入流 ReadNBytes，输入流 反射，Java API ResolvedModule 方法，Java API 根模块 RxJava 2 S ScreenSleepListener ScriptEngine，Nashorn SecureRandom SecureRandomParameters 安全 PKCS12 密钥库 SecureRandom SHA-3 哈希算法 Set.of()，集合 setOpenFileHandler() setOpenURIHandler() setPrintFileHandler() SHA-3 哈希算法 签名多态性，访问模式 SLF4J SLF4JLoggerFinder SLF4JLoggerWrapper 栈遍历 API StackWalker.StackFrame 方法 流方法 dropWhile() iterate() ofNullable() takeWhile() stream()，Optional SubmissionPublisher 方法 System.getLogger() System.getP，jshell System.LoggerFinder System.Logger.Level System.Logger 方法 SystemSleepListener T takeWhile()，流 TemplateString()，ECMAScript 6 Thread.onSpinWait()，并发 TIFF 图像格式 Timeout()，并发 TimeUnit()，并发 工具接口，JVM TransferTo，输入流 Try finally()，增强型方法句柄 Try-with-resources 语句 U Unicode 8.0，杂项 统一日志系统 装饰 级别 日志配置 输出 标签 诊断命令 VM.log 未命名模块 unreflectVarHandle 升级模块路径 用户界面 Applet API 桌面应用 关于窗口 应用程序事件 应用程序退出功能 打开文件 打开 URI 偏好设置窗口 打印文件 多分辨率图像 TIFF 图像格式 UserSessionListener UTF-8 资源包，杂项 工具方法，并发 V VarHandle.AccessMode varHandleExactInvoker varHandleInvoker 变量句柄 VarHandle.AccessMode 数组 原子更新方法 按位原子更新 byte [] ByteBuffer findStaticVarHandle findVarHandle 内存屏障 内存顺序 数值原子更新 读取访问模式 签名多态性 unreflectVarHandle 写入访问模式 诊断命令 VM.log W, X, Y whileLoop()，增强型方法句柄 处理现有代码模块 自动模块 未命名模块 写入访问模式 Z Zero()，增强型方法句柄
