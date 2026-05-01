# 7. Lambda 表达式与遗留代码

在本书的这一部分，你已经看到了 lambda 表达式能为你的代码库带来的所有美妙之处。但那些大量的遗留代码呢？有很多代码需要融入这个新的现实，许多 Java 开发者都致力于那些不得不拖着这些“泥球”前进的项目。Lambda 表达式如何让他们的生活更轻松？

此外，新的 Java 8 API 令人印象深刻，但 Java SDK 中还有许多 API 尚未为这个勇敢的新 lambda 世界进行更新。你如何利用那些完全不打算支持 lambda 的 API 来使用 lambda 呢？

好消息是，Java 语言的开发者们长期以来一直关注这个问题。Java 本质上是一种保守的语言，因此语言开发者不能通过激进的语法更改来破坏任何现有的 Java 代码。然而，语言开发者可以添加新功能，提供新的能力和新的 API。最重要的是，他们提供了关键功能，将遗留代码桥接到我们的新世界。这些功能，结合一些关键实践，意味着只需少量代码就能获得 lambda 的所有能力，即使是从那些对 lambda 一无所知的代码中也能做到。

## 资源与异常

Java 中最尴尬的遗留模式是“try-with-resource”模式。在这种模式中，一个有状态对象（通常是 I/O 对象或池）需要从初始状态进入可操作状态，然后才能使用它，并且当用户操作完成后，还需要清理这个有状态对象。这种模式被广泛采用，并且在概念上与面向对象编程配合得很好。然而，这种模式的实现在 Java 中一直效果不佳，并且该语言从一开始就一直在努力寻找改进的方法。

最初，这种模式要求你在作用域外声明一个变量，然后进入 try/catch 块。每个资源需要额外的 14 行代码，看起来像这样：

`InputStream in = null;`

`try {`

`in = acquireInputStream();`

`// 对“in”执行某些操作`

`} finally {`

`if (in != null) {`

`try {`

`in.close();`

`} catch (IOException closeException) {`

`// 现在怎么办？`

`}`

`in = null;`

`}`

`}`

这就产生了一个问题：如何处理可能抛出的 `closeException`。如果在使用资源时抛出一个异常，然后在关闭资源时又抛出另一个异常，该怎么办？如果你抛出 `closeException`，原始异常（可能实际上很有用）就会丢失。通常的解决方案是简单地忽略 `closeException`，通常将其隐藏在一个名为 `closeQuietly(InputStream in)` 的方法中。这个方法也可以忽略空值。这使得代码显著缩短，并且可读性稍好：

`InputStream in = null;`

`try {`

`in = acquireInputStream();`

`// 执行某些操作`

`} finally {`

`closeQuiety(in);`

`in = null;`

`}`

然而，这段代码存在一些微妙的问题。乍一看，它们可能微不足道，但当它们出现在你的生产代码中时，就绝非小事了。这些微妙问题中最显著的是，这段代码忽略了关闭时发生的任何异常，而正是在关闭时，缓冲流、压缩流和加密流会抛出它们的异常。这段代码会静默地丢弃这些异常，让调用代码（以及调试的开发者）认为一切正常。这种代码模式还会将资源的变量泄漏到周围的作用域中。如果你忘记将资源赋值为 `null`，就会泄漏对象本身，阻止其及时进行垃圾回收。如果 try 块附带了 catch 子句，资源处理也容易丢失或混淆，并且多个资源需要将这些结构相互嵌套。整个事情仍然显得有些丑陋。

为了解决这种丑陋，Java 7 引入了“try-with-resources”语法结构。这允许你在 try 块的开头声明资源，并且它会自动关闭资源。你可以将任何实现了 `java.lang.AutoCloseable` 的对象分配为资源。当 try 块完成时，该资源将自动关闭。如果在关闭时抛出异常，则该异常会向上传播给调用者。如果已经抛出了一个异常，并且在关闭时又抛出了一个额外的异常，那么该额外异常会作为抑制异常附加到第一个异常上：堆栈跟踪会显示原始异常是主要原因，而关闭异常是附加的抑制异常。另一个好处是，你为变量划定了清晰的边界，并且编译器保证在退出块时它们会被关闭。总的来说，这提供了人们所期望的那种异常处理，代码看起来像这样：

`public void closeQuietlyTryWithResource() {`

`try(InputStream in = acquireInputStream()) {`

`// 执行某些操作`

`} catch(IOException exception) {`

`// 处理异常，可能包括关闭异常`

`}`

`}`



遗憾的是，这种写法与 lambda 表达式的语法配合不佳。try-with-resources 块并非单条语句，因此若想将其作为 lambda 的主体，你必须在调用外层立即加上一对花括号，如下所示：

`Consumer<Object> consumer = it -> {`

`try (InputStream in = acquireInputStream()) {`

`// 执行某些操作`

`} catch (IOException exception) {`

`// 处理异常，可能包含关闭异常`

`}`

`};`

另请注意，我们必须将 lambda 放在 try-with-resources 块内部，或者在退出 try-with-resources 块之前执行该 lambda。如果你在 try-with-resources 块内赋值 lambda，然后退出该块，之后再尝试调用 lambda，此时输入流将被关闭。操作的顺序如下：

进入 try-with-resources 块。  
赋值 lambda。  
退出 try-with-resources 块，关闭输入流。  
调用 lambda。

清单 7-1 演示了这种错误。当你这样逐步分析时，错误似乎显而易见。然而，这是一个很容易犯的错误，并且在实践中可能相当难以察觉。如果你调用的代码会“释放 Zalgo”，那么检测起来尤其棘手。如果你还记得第 2 章的内容，当调用代码有时立即执行、有时延迟执行时，开发者就“释放了 Zalgo”。如果你的代码释放了 Zalgo，那么你可能会将资源传入该代码，期望它立即执行。但是，当它以延迟方式执行时，就会出现竞态条件：try-with-resources 块将在未来的某个时刻调用关闭操作，而调用代码将在未来的另一个时刻读取输入流。哪个先发生，完全取决于线程调度器的随机决定。结果将是间歇性的已关闭输入流：当读取代码偶尔在竞态中失败时，你会收到异常。

**清单 7-1. 在资源关闭后调用 Lambda**

`import java.io.IOException;`

`import java.io.InputStream;`

`import java.io.UncheckedIOException;`

`import java.util.function.*;`

`public class Listing1 {`

`public static void main(String[] args) {`

`Consumer<Object> consumer = null;`

`try (InputStream in = acquireInputStream()) {`

`consumer = it -> {`

`try {`

`System.out.println("尝试读取！");`

`in.read();`

`} catch (IOException ioe) {`

`throw new UncheckedIOException("读取错误", ioe);`

`}`

`};`

`} catch (IOException ioe) {`

`throw new UncheckedIOException("输入流错误", ioe);`

`}`

`consumer.accept(new Object());`

`}`

`public static InputStream acquireInputStream() {`

`return new InputStream() {`

`@Override`

`public int read() throws IOException {`

`return 1;`

`}`

`@Override`

`public void close() throws IOException {`

`System.out.println("正在关闭！");`

`super.close();`

`}`

`};`

`}`

`}`

`/* 结果`

`正在关闭！`

`尝试读取！`

`*/`

总而言之，try-with-resources 方法还算不错，尽管它已经开始显得有些杂乱。你必须小心哪些块以何种方式嵌套，但这还是相当可控的。更大的问题是，如果你的函数有返回值，那么 catch 块中的返回必须与 try 块中的返回匹配。许多 Java 开发者的自然反应是在此时返回 `null`，但这只会增加遇到 `NullPointerException` 的可能性，因为它期望调用者处理这种情况，同时又让调用者很容易忽略它。根据你的具体情况，有三种更好的选择：非受检异常、异常处理调用者，或者 `Result` 对象。

### 通过抛出非受检异常来处理资源

一种非常流行且常见的解决方案是将受检异常转换为非受检异常并重新抛出。这将处理问题的责任交回给调用者，并且异常可能会在遇到异常处理器之前，沿着执行栈向上回溯相当长的距离。希望无论异常处理器在哪里，它都能以智能的方式处理这种异常情况。

Java 8 为这种编程惯例引入了一些支持：`java.io.UncheckedIOException` 类，它的存在明确是为了将受检的 `IOException` 实例包装在非受检的包装器中。由于 `IOException` 是最常见异常类的根源，这个类提供了一种将大多数常见异常转换为非受检形式的方法。最好的做法是使用 `UncheckedIOException(String,IOException)` 构造函数，它允许你在转换点附加一条供人类阅读的消息；一般来说，在发生异常时你能提供的信息越多，情况就越好。有关此用法的示例，请参见清单 7-2。

**清单 7-2. 包装 IOException 的 UncheckedIOException**

`import java.io.ByteArrayInputStream;`

`import java.io.IOException;`

`import java.io.InputStream;`

`import java.io.UncheckedIOException;`

`import java.util.function.*;`

`public class Listing2 {`

`public static InputStream createInputStream() throws IOException {`

`return new ByteArrayInputStream("foobar".getBytes());`

`}`

`public static void call(Consumer<Object> c) {`

`c.accept(new Object());`

`}`

`public static void main(String[] args) {`

`call(it -> {`

`try (InputStream in = createInputStream()) {`

`throw new IOException("一吻便杀一人！");`

`} catch (IOException e) {`

`throw new UncheckedIOException(`

`"处理输入流时出错", e);`

`}`

`}`

`);`

`}`

`}`

`/* 结果`

`Exception in thread "main" java.io.UncheckedIOException: 处理输入流时出错`

`at Listing1.lambda$main$0(Listing1.java:22)`

`at Listing1$$Lambda$1/558638686.accept(Unknown Source)`

`at Listing1.call(Listing1.java:14)`

`at Listing1.main(Listing1.java:18)`

`Caused by: java.io.IOException: 一吻便杀一人！`

`at Listing1.lambda$main$0(Listing1.java:20)`

`... 4 more`

`*/`

如果你查看清单 7-2 中的堆栈跟踪，你会开始看到 Java 的 lambda 实现产生的一些堆栈跟踪噪音。我们将在下一章讨论为什么会这样以及如何解读它。然而，我们现在已经可以看出，我们得到了 lambda 本身的确切源代码行（`lambda$main$0` 行），以及调用 lambda 的位置（`Listing1.call` 行）。这应该是合理的，因为调用 lambda 就是执行它的函数接口方法（`Listing1$$Lambda$1/558638686.accept` 行）。原始异常也像我们习惯的那样，在“Caused by”行中给出。总而言之，这非常熟悉且舒适。

然而，当你开始使用并行流时，情况就变得有趣多了。在这种情况下，异常将从 `ForkJoinWorkThread` 实例的上下文中抛出。这会在堆栈跟踪中产生显著更多的噪音，并削弱我们回溯调用栈的能力。为了看清困难所在，让我们构建一个示例。在这个示例中，我们将在执行第 100 行时触发异常。让我们看看清单 7-3 中的结果。

**清单 7-3. 未处理异常终止流**

`import java.io.ByteArrayInputStream;`

`import java.io.IOException;`

`import java.io.InputStream;`

`import java.io.UncheckedIOException;`

`import java.util.*;`

`import java.util.concurrent.atomic.*;`

`import java.util.function.*;`

`import java.util.stream.*;`

`public class Listing3 {`

`public static InputStream generateInputStream() {`

`return new ByteArrayInputStream("foobar".getBytes());`

`}`



`public static Stream<Integer> generateParallelStream() {`

`final int elements = 1000;`

`List<Integer> toReturn = new ArrayList<>(elements);`

`for (int i = 0; i < elements; i++) {`

`toReturn.add(i);`

`}`

`return toReturn.parallelStream();`

`}`

`public static Function<Integer, Integer> generateMap() {`

`AtomicInteger counter = new AtomicInteger(0);`

`return i -> {`

`int count = counter.incrementAndGet();`

`try (InputStream in = Listing3.generateInputStream()) {`

`if (i == 100) {`

`throw new IOException(`

`"And with a kiss, I die! (After " + count + " executions)");`

`}`

`return i;`

`} catch (IOException ioe) {`

`throw new UncheckedIOException(`

`"Error working with input stream", ioe`

`);`

`}`

`};`

`}`

`public static void main(String[] args) {`

`generateParallelStream().map(generateMap()).forEach(System.out::println);`

`}`

`}`

`/* RESULT (with a few hundred lines of numbers cut off the front)`

`...`

`794`

`245`

`190`

`402`

`15`

`57`

`957`

`58`

`16`

`403`

`191`

`Exception in thread "main" java.io.UncheckedIOException: Error working with input stream`

`at Listing3.lambda$generateMap$1(Listing3.java:35)`

`at Listing3$$Lambda$1/1149319664.apply(Unknown Source)`

`at java.util.stream.ReferencePipeline$3$1.accept(`

`ReferencePipeline.java:193)`

`at java.util.ArrayList$ArrayListSpliterator.forEachRemaining(`

`ArrayList.java:1359)`

`at java.util.stream.AbstractPipeline.copyInto(AbstractPipeline.java:512)`

`at java.util.stream.AbstractPipeline.wrapAndCopyInto(`

`AbstractPipeline.java:502)`

`at java.util.stream.ReduceOps$ReduceTask.doLeaf(ReduceOps.java:747)`

`at java.util.stream.ReduceOps$ReduceTask.doLeaf(ReduceOps.java:721)`

`at java.util.stream.AbstractTask.compute(AbstractTask.java:316)`

`at java.util.concurrent.CountedCompleter.exec(CountedCompleter.java:731)`

`at java.util.concurrent.ForkJoinTask.doExec(ForkJoinTask.java:289)`

`at java.util.concurrent.ForkJoinPool$WorkQueue.runTask(`

`ForkJoinPool.java:916)`

`at java.util.concurrent.ForkJoinPool.scan(ForkJoinPool.java:1689)`

`at java.util.concurrent.ForkJoinPool.runWorker(ForkJoinPool.java:1644)`

`at java.util.concurrent.ForkJoinWorkerThread.run(ForkJoinWorkerThread.java:157)`

`Caused by: java.io.IOException: And with a kiss, I die! (After 489 executions)`

`at Listing3.lambda$generateMap$1(Listing3.java:31)`

`... 14 more`

`*/`

清单 7-3 中的堆栈跟踪远不如清单 7-2 中的版本那么友好。堆栈跟踪中仍然存在 lambda 引用，但这些引用指向的是 lambda 声明的位置；完全没有对 `main` 方法的引用，而我们的 lambda 正是在 `main` 方法中接入流的。代码直接从相当通用的流基础设施跳转到源代码中 lambda 声明的位置。这可能会让定位代码实际被调用的位置变得棘手；想象一下，如果 lambda 是在其他类中定义的，并在多个流中重用——根据经验，基于这样的堆栈跟踪来调试该异常会非常困难。

另一件需要注意的事情是，我们在处理过程深入之后才遇到这次失败：我们在第 304 次执行时崩溃了。这意味着有 303 次执行是成功完成的。其中一些结果被打印了出来。然而，有些结果可能并未打印；这取决于崩溃发生时它们在管道中已经处理到了哪个阶段。更糟糕的是，在崩溃发生后，我们甚至没有尝试处理任何后续的数字：一次崩溃就会终止整个流。因此，我们的一部分元素处于已完成状态，一部分处于中间状态，还有一部分从未被启动。这次崩溃让一切陷入了一个糟糕的境地。

这种方法的最后一个问题是，我们重新引入了异常吞没的问题，尽管方式非常隐蔽。好消息是，我们总是会得到一个异常。然而，如果多个线程都发生了异常，那么我们只会看到第一个异常。有可能多个线程同时抛出异常：例如，当数据库宕机时，它们都在执行数据库查询。但在这种情况下，即使存在多个问题，你也只会看到第一个异常。

有时，这些限制是可以接受的。有时，你的工作确实是“要么全有，要么全无”，或者即使事情处于不确定状态，多次重新运行也是安全的。在这些情况下，将异常映射为未检查异常的做法完全可行。然而，如果你想尝试从间歇性错误中挽救工作成果，那么你就需要更巧妙的方法了。



### 使用异常处理调用者管理资源

由于 Java 8 是一种后函数式语言，我们可以针对问题采用面向对象的解决方案。我们想要的对象将封装 try-with-resources 逻辑和错误处理，这样用户只需提供所需的实现即可。在映射运行后，可以查询该对象以处理出现的任何异常。

这里的思路是，我们会在开头和结尾各有一段代码，中间留出一个供用户自定义代码的开口。我的朋友、导师兼合著者 Brian Hurt 将此称为“中间空洞模式”。Spring 称之为“控制反转”，这也是早期 Spring API（尤其是 Spring JDBC）的关键优势之一。在我们的场景中，开头的代码是资源的创建（try-with-resources 块中的“try”部分）和异常处理（try-with-resources 块中的“catch”部分）。中间供用户自定义代码的开口，则是接收资源与流值，并返回流映射值的代码。

在实现中，我们将创建这个类以及两个单方法接口：一个定义如何创建资源，另一个定义如何处理资源。我们期望用户使用 lambda 表达式来实现这些接口。这些接口方法允许用户抛出受检异常，因此用户可以完全自由地执行他们想要的操作。该类本身将封装资源的创建以及返回值与异常的处理。

这个非常简洁的理论有一个陷阱：在出现异常时，我们必须返回一些内容。传统的 Java 开发者又会习惯性地使用 `null`，但这是一个坏习惯。Java 8 提供了 `Optional` 类，它表示一个可能已设置也可能未设置的值。这是一个更好的选择，因为 `Optional` 类不会导致你意外引发 `NullPointerException`；该 API 提供了许多更好的替代方法来获取值，包括表示常见的“获取并判空否则爆炸”的代码模式。

因此，我们流的结果将是一个 `Optional` 容器，其类型与映射本身通常返回的类型相同。我们可以使用流的 `filter` 方法过滤掉空的 `Optional` 类型，这使得从流中清除任何异常元素变得非常容易。空的元素也提供了一些价值：它们使我们能够发现流中发生了错误，因此我们可以提供更智能的行为。

这个类、它的接口以及调用演示都在清单 7-4 中给出。类实现本身相当丑陋。这是因为存在大量类型变量和奇怪的语法，而且由于接口不能是类的成员（它们始终是静态的），情况变得更糟。然而，最终的 API 和用法实际上相当不错。

**清单 7-4. 使用对象通过 Optional 管理资源**

`import java.io.ByteArrayInputStream;`

`import java.io.IOException;`

`import java.io.InputStream;`

`import java.util.*;`

`import java.util.concurrent.*;`

`import java.util.concurrent.atomic.*;`

`import java.util.function.*;`

`import java.util.stream.*;`

`public class Listing4 {`

`public static class ResourceExceptionHandler`

`<RESOURCE_T extends AutoCloseable>`

`{`

`public static interface FunctionWithResource`

`<RESOURCE_T extends AutoCloseable, IN_T, OUT_T>`

`{`

`OUT_T apply(RESOURCE_T resource, IN_T value) throws Exception;`

`}`

`public static interface ResourceMaker`

`<RESOURCE_T extends AutoCloseable>`

`{`

`RESOURCE_T create() throws Exception;`

`}`

`private final ResourceMaker<RESOURCE_T> init;`

`private final ConcurrentMap<Object, Exception> exceptions =`

`new ConcurrentSkipListMap<>();`

`public ResourceExceptionHandler(final ResourceMaker<RESOURCE_T> init) {`

`Objects.requireNonNull(init,`

`"ResourceMaker (initialization code for resource)");`



`this.init = init;`

`}`

`public Map<Object, Exception> getExceptions() {`

`return exceptions;`

`}`

`public Function<Integer, Optional<Integer>> map(`

`FunctionWithResource<RESOURCE_T, Integer, Integer> f`

`) {`

`return i -> {`

`try (RESOURCE_T resource = init.create()) {`

`return Optional.of(f.apply(resource, i));`

`} catch (Exception e) {`

`exceptions.put(i, e);`

`return Optional.empty();`

`}`

`};`

`}`

`}`

`public static InputStream generateInputStream() {`

`return new ByteArrayInputStream("foobar".getBytes());`

`}`

`public static Stream<Integer> generateParallelStream() {`

`final int elements = 1000;`

`List<Integer> toReturn = new ArrayList<>(elements);`

`for (int i = 0; i < elements; i++) {`

`toReturn.add(i);`

`}`

`return toReturn.parallelStream();`

`}`

`public static ResourceExceptionHandler.FunctionWithResource`

`<InputStream, Integer, Integer> generateMap()`

`{`

`AtomicInteger counter = new AtomicInteger(0);`

`return (in, i) -> {`

`int count = counter.incrementAndGet();`

`if (i == 100) {`

`throw new IOException(`

`"And with a kiss, I die! (After " + count + " executions)");`

`}`

`return i;`

`};`

`}`

`public static void main(String[] args) {`

`// 创建异常处理器`

`ResourceExceptionHandler handler =`

`new ResourceExceptionHandler(Listing4::generateInputStream);`

`// 执行流处理`

`Function<Integer, Optional<Integer>> mapFunction =`

`handler.map(generateMap());`

`generateParallelStream()`

`.map(mapFunction)`

`.filter(Optional::isPresent).map(Optional::get)`

`.forEach(System.out::println);`

`// 处理异常`

`Map<Object, Exception> exceptions = handler.getExceptions();`

`exceptions.forEach((key, val) -> {`

`System.out.println(key + ": " + val);`

`val.printStackTrace(System.err);`

`}`

`);`

`}`

`}`

然而，这种方法要求我们额外进行处理，以便从`Optional`容器中提取值。还有另一种基于`flatMap`的替代方案，我们最早在第 3 章中见过：我们可以让值返回一个流，这个流可以是空的，也可以包含单个元素。这让我们能够避开`Optional`类型及其提取过程，尽管它向流本身隐藏了错误的任何迹象。不过，即使你必须调用`flatMap`方法来执行实际上只是一个简单的映射操作，这个 API 总体上还是更优雅一些。这段代码如清单 7-5 所示。

**清单 7-5. 使用`flatMap`处理带资源的对象**

`import java.io.ByteArrayInputStream;`

`import java.io.IOException;`

`import java.io.InputStream;`

`import java.util.*;`

`import java.util.concurrent.*;`

`import java.util.concurrent.atomic.*;`

`import java.util.function.*;`

`import java.util.stream.*;`

`public class Listing5 {`

`public static class ResourceExceptionHandler`

`<RESOURCE_T extends AutoCloseable>`

`{`

`public static interface FunctionWithResource`

`<RESOURCE_T extends AutoCloseable, IN_T, OUT_T>`

`{`

`OUT_T apply(RESOURCE_T resource, IN_T value) throws Exception;`

`}`

`public static interface ResourceMaker<RESOURCE_T extends AutoCloseable> {`

`RESOURCE_T create() throws Exception;`

`}`

`private final ResourceMaker<RESOURCE_T> init;`

`private final ConcurrentMap<Object, Exception> exceptions`

`= new ConcurrentSkipListMap<>();`

`public ResourceExceptionHandler(final ResourceMaker<RESOURCE_T> init) {`

`Objects.requireNonNull(init,`

`"ResourceMaker (initialization code for resource)");`

`this.init = init;`

`}`

`public Map<Object, Exception> getExceptions() {`

`return exceptions;`

`}`

`public Function<Integer, Stream<Integer>> map(`

`FunctionWithResource<RESOURCE_T, Integer, Integer> f`

`) {`

`return i -> {`

`try (RESOURCE_T resource = init.create()) {`

`return Stream.of(f.apply(resource, i));`

`} catch (Exception e) {`

`exceptions.put(i, e);`

`return Stream.empty();`

`}`

`};`

`}`

`}`

`public static InputStream generateInputStream() {`

`return new ByteArrayInputStream("foobar".getBytes());`

`}`

`public static Stream<Integer> generateParallelStream() {`

`final int elements = 1000;`

`List<Integer> toReturn = new ArrayList(elements);`

`for (int i = 0; i < elements; i++) {`

`toReturn.add(i);`

`}`

`return toReturn.parallelStream();`

`}`

`public static ResourceExceptionHandler.FunctionWithResource`

`<InputStream, Integer, Integer> generateMap()`

`{`

`AtomicInteger counter = new AtomicInteger(0);`

`return (in, i) -> {`

`int count = counter.incrementAndGet();`

`if (i == 100) {`

`throw new IOException(`

`"And with a kiss, I die! (After " + count + " executions)");`

`}`

`return i;`

`};`

`}`

`public static void main(String[] args) {`

`// 创建异常处理器`

`ResourceExceptionHandler handler =`

`new ResourceExceptionHandler(Listing5::generateInputStream);`

`// 执行流处理`

`Function<Integer, Stream<Integer>> flatMapFunction =`

`handler.map(generateMap());`

`generateParallelStream()`

`.flatMap(flatMapFunction)`

`.forEach(System.out::println);`

`// 处理异常`

`Map<Object, Exception> exceptions = handler.getExceptions();`

`exceptions.forEach((key, val) -> {`

`System.out.println(key + ": " + val);`

`val.printStackTrace(System.err);`

`}`

`);`

`}`

`}`

在这两种情况下，我们都实现了良好的关注点分离：流处理正常路径，然后稍后再处理异常。代码本身相当丑陋且敏感，因此最好只编写一次并一劳永逸。上面提供的 API 以及其他 API，在 FunJava 项目中以`funjava.io.FunResource`的名称提供。

虽然这种方法在流的“正常路径”和后续的异常处理之间提供了清晰的区分，但有时这种区分无法保持清晰。有时你希望能够在处理过程中处理异常。在这种情况下，你希望将异常信息保留在同一个流中。这就是我们使用`Result`对象的时候了。



### 使用结果对象处理资源

设想一个需要处理文件的流。这些文件可能存在，也可能不存在；如果不存在，这本身也具有特定含义。例如，这些文件可能是用户可选指定的文件，如果文件未上传，那么你希望使用默认文件。在这种情况下，你需要在流内部保留关于 IO 错误的信息，以便能够在流中处理该错误。这使得我们在上一节中看到的处理器方法变得不合适，因为你不想查询另一个对象来了解同一流中之前发生了什么。非受检异常的情况会持续中断，让你毫无进展。相反，你真正需要的是一个 `Result` 对象。

我们之前见过 `Result` 对象。它与 `Optional` 类型是近亲，因为它持有一个可能存在也可能不存在的值。然而，`Result` 对象要么持有返回值，要么持有一个异常。这是任何非 void 的 Java 计算中仅有的两种可能。`Result` 类型，如同 `Optional` 类型，可以提供便捷的 API 来访问其内容。

我们之前使用过 `Result` 对象，但现在让我们看看如何重新实现这样一个类型。我们想要存储一个返回值或一个异常，这意味着我们可以有两个字段。我们期望在任何时刻，其中一个字段为 null，另一个字段非 null。如果你传入一个返回值，我们称之为“成功”的 Result 实例。如果你传入一个异常，我们称之为“失败”的 Result 实例。由于结果和异常字段的类型取决于调用者的上下文，我们将它们设为类型变量。为了给用户提供更好的使用体验，我们可以使用静态方法而不是暴露构造函数：静态方法可以欺骗 Java 类型系统为你做更多的类型推断工作，而不必在整个代码中重复类型信息。我们还将提供五个访问器来访问我们的状态：两个布尔访问器，仅检查结果是成功还是失败；两个直接访问器，返回其字段的状态；以及一个组合访问器，如果成功则返回成功值，如果失败则抛出失败值。使用这个类型，我们就可以实现基本的行内流错误处理。Result 的实现和错误处理如清单 7-6 所示。

**清单 7-6. 使用 Result 类型处理资源**

`import java.io.ByteArrayInputStream;`

`import java.io.IOException;`

`import java.io.InputStream;`

`import java.util.*;`

`import java.util.concurrent.atomic.*;`

`import java.util.function.*;`

`import java.util.stream.*;`

`public class Listing6 {`

`public static class Result<SUCCESS_T, FAILURE_T extends Exception> {`

`private final SUCCESS_T success;`

`private final FAILURE_T failure;`

`private Result(final SUCCESS_T success, final FAILURE_T failure) {`

`if (success == null && failure == null) {`

`throw new IllegalArgumentException(`

`"Success and failure cannot both be null");`

`}`

`if (success != null && failure != null) {`

`throw new IllegalArgumentException(`

`"Success and failure cannot both be non-null");`

`}`

`this.success = success;`

`this.failure = failure;`

`}`

`public static <SUCCESS_T, FAILURE_T extends Exception>`

`Result<SUCCESS_T, FAILURE_T> ofFailure(FAILURE_T failure)`

`{`

`Objects.requireNonNull(failure, "failure to assign is null");`

`return new Result(null, failure);`

`}`

`public static <SUCCESS_T, FAILURE_T extends Exception>`

`Result<SUCCESS_T, FAILURE_T> ofSuccess(SUCCESS_T success)`

`{`

`Objects.requireNonNull(success, "success to assign is null");`

`return new Result<>(success, null);`

`}`

`public boolean isSuccess() { return success != null; }`

`public boolean isFailure() { return failure != null; }`

`/**`

`* 成功时提供结果，失败时抛出失败异常。`

`*`

`* @return 成功时的成功值。`

`* @throws FAILURE_T 如果此结果不成功。`

`*/`

`public SUCCESS_T get() throws FAILURE_T {`

`if (success != null) return success;`

`throw failure;`

`}`

`/**`

`* 返回成功值，如果此结果是失败则返回 {@code null}。`

`*`

`* @return 成功值或 {@code null}。`

`*/`

`public SUCCESS_T getSuccess() {`

`return success;`

`}`

`/**`

`* 返回失败值，如果此结果是成功则返回 {@code null}。`

`*`

`* @return 失败值或 {@code null}。`

`*/`

`public FAILURE_T getFailure() {`

`return failure;`

`}`

`@Override`

`public String toString() {`

`if (isSuccess()) {`

`return "SUCCESS[" + success + "]";`

`} else {`

`return "FAILURE[" + failure + "]";`

`}`

`}`

`}`

`public static InputStream generateInputStream() {`

`return new ByteArrayInputStream("foobar".getBytes());`

`}`

`public static Stream<Integer> generateParallelStream() {`

`final int elements = 1000;`

`List<Integer> toReturn = new ArrayList(elements);`

`for (int i = 0; i < elements; i++) {`

`toReturn.add(i);`

`}`

`return toReturn.parallelStream();`

`}`

`public static Function<Integer, Result<Integer, IOException>> generateMap() {`

`AtomicInteger counter = new AtomicInteger(0);`

`return i -> {`

`int count = counter.incrementAndGet();`

`try (InputStream in = Listing3.generateInputStream()) {`

`if (i == 100) {`

`return Result.ofFailure(`

`new IOException(`

`"And with a kiss, I die! (After " + count + " executions)")`

`);`

`}`

`return Result.ofSuccess(i);`

`} catch (IOException ioe) {`

`return Result.ofFailure(ioe);`

`}`

`};`

`}`

`public static void main(String[] args) {`

`// 执行流处理`

`generateParallelStream()`

`.map(generateMap())`

`.forEach(result -> {`

`if (result.isFailure()) {`

`result.getFailure().printStackTrace(System.err);`

`System.err.flush();`

`} else {`

`System.out.println(result.getSuccess());`

`System.out.flush();`

`}`

`}`

`);`

`}`

`}`

这种方法更安全、更清晰，因为它要求用户在错误处理方面更加有意识，同时也为他们提供了执行该错误处理所需的信息。它还允许你在流处理过程中处理错误，而不必等到之后。这种方法的缺点是，它不太容易包装现有的基于资源的代码：你必须首先将结果包装在 `Result` 对象中，而不是将你想要实现的整个方法包装在某个辅助对象中。

当你在 Java 中处理资源时，你是在使用一种不同风格的语法糖：它对后函数式代码并不特别友好，但对于之前的命令式代码来说，它是一个非常好的好处。你连接范式所采用的具体方法取决于你特定系统的需求，但你越早能够封装丑陋之处并完全进入后函数式世界，你的代码就会越令人满意。


## 连接集合与流

在 Java 引入流之前，其主要的大批量操作模式是通过集合 API 进行的。曾有人尝试创建批量集合操作（称为“推导式”），甚至有人尝试将 `Iterator` 类转变为与当代流非常相似的东西。然而，该 API 实际上并不适用于后函数式风格的工作，并且始终存在一些与流方法配合不佳的粗糙边缘：这就是 Java 引入一套全新 API 的原因。

好消息是，从 `collection` 类型的实例转换为流非常容易：只需分别调用 `collection.stream()` 或 `collection.parallelStream()` 方法即可获得顺序流或并行流。对于 `Map` 类型的实例，你首先需要指定是要获取映射的键集、条目集还是值集合，然后就可以从中获取你选择的流。`Map` 类型和 `Collection` 类型都有一个接受消费者的 `forEach` 方法：它实际上是 `collection.stream().forEach(Consumer)` 的简写，并且当你不需要知道索引时，在任何情况下，此方法都应该取代你的 for 循环。

然而，从流转换回集合则稍微棘手一些。`Stream` 类型上没有 `.toList()` 方法，尽管这本来会很方便。一种选择是使用将流转换为数组的方法之一，然后使用 `Arrays.asList(Array)` 来获取你想要的列表。结果将是一个包含所有流元素的固定大小（但非不可变）的列表。清单 7-7 给出了该方法的基本演示，如果你打算采用这种方法，请务必阅读下一节关于数组的内容。

**清单 7-7. 通过 toArray() 将流连接到集合**

`import java.util.*;`

`import java.util.stream.*;`

`public class Listing7 {`

`public static Stream<Integer> generateParallelStream() {`

`final int elements = 1000;`

`List<Integer> toReturn = new ArrayList<>(elements);`

`for (int i = 0; i < elements; i++) {`

`toReturn.add(i);`

`}`

`return toReturn.parallelStream();`

`}`

`public static void main(String[] args) {`

`// 执行流处理`

`List<Object> list = Arrays.asList(generateParallelStream().toArray());`

`}`

`}`

如果你有一些现有的列表想要向其中添加元素，那么你可以简单地使用 `List.add` 方法作为你的消费者。该方法从技术上讲返回 `boolean`，因此它并不完全符合 `Consumer` 的精确形状，但 Java 允许你在此处变通规则。如果你在流操作之前就有一个列表，那么只需将该列表的 `list::add` 方法传递给流的 `forEach` 即可。类似的方法也适用于将流元素添加到现有集合。唯一的注意事项是，如果你使用的是并行流，则需要确保使用的是线程安全的集合，因为它的 add 方法将从多个线程中被调用。清单 7-8 演示了这种方法。

**清单 7-8. 使用 Add 将流连接到集合**

`import java.util.*;`

`import java.util.concurrent.*;`

`import java.util.stream.*;`

`public class Listing8 {`

`public static Stream<Integer> generateParallelStream() {`

`final int elements = 1000;`

`List<Integer> toReturn = new ArrayList<>(elements);`

`for (int i = 0; i < elements; i++) {`

`toReturn.add(i);`

`}`

`return toReturn.parallelStream();`

`}`

`public static void main(String[] args) {`

`// 执行流处理`

`List<Integer> list = new CopyOnWriteArrayList<>();`

`generateParallelStream().forEach(list::add);`

`}`

`}`

请注意，如果你要添加元素的集合不是线程安全的，并且你的流是并行流，那么你可以使用 `Collection.synchronizedList(List)`（或相关方法）来同步它。这可以内联完成，从而创建如下代码行：

`generateParallelStream().forEach(Collections.synchronizedList(list)::add);`

如果你希望你的流生成一个不可变列表或集合，那么最简单的方法是使用 Google 的 Guava 库。在其他不可变类型中，Guava 库提供了 `ImmutableList` 和 `ImmutableSet`（以及其他不可变类型），并且为这些不可变类型中的每一种都提供了构建器类型。这些构建器类型的 add 方法与我们之前看到的列表的 add 方法一样好用。这些构建器也都是线程安全的，因此可以安全地与并行流一起使用。清单 7-9 给出了这种用法的一个示例。如果你只是在一个方法内构建你的集合，然后将其作为最后一步返回给用户，这种方法尤其有效。

**清单 7-9. 使用构建器将流连接到集合**

`import com.google.common.collect.ImmutableList;`

`import java.util.*;`

`import java.util.stream.*;`

`public class Listing9 {`

`public static Stream<Integer> generateParallelStream() {`

`final int elements = 1000;`

`List<Integer> toReturn = new ArrayList<>(elements);`

`for (int i = 0; i < elements; i++) {`

`toReturn.add(i);`

`}`

`return toReturn.parallelStream();`

`}`

`public static void main(String[] args) {`

`// 执行流处理`

`ImmutableList.Builder<Integer> builder = ImmutableList.builder();`

`generateParallelStream().forEach(builder::add);`

`ImmutableList list = builder.build();`

`}`

`}`

最后，如果你想要特别精巧且高效地处理事情，你可以使用 Stream API 的 `Collector` 部分。Stream API 的 `Collector` 部分提供了一种专门的归约逻辑，其结果是对现有元素的组合。在 `java.util.stream.Collectors` 类中可以找到许多 `Collector` 实现的示例。其中包括 `Collectors.toList()`、`Collectors.toSet()` 和 `Collectors.toCollection(Supplier)`。这些方法中的每一个都返回一个 `Collector` 实例，并将其传递给 `stream.collect(Collector)`。`Collectors.toList()` 和 `Collectors.toSet()` 都如其名：它们返回一个 `List` 或 `Set`。但是，不保证结果集合的可变性程度、其可序列化性或其线程安全性。如果你想要更具体地指定返回的内容，可以调用 `Collectors.toCollection(Supplier)`。`Supplier` 负责提供你所需类型的集合，并且假定每次调用该供应器时，它都会提供一个新实例。因此，作为供应器传递的最简单的东西就是构造函数本身，例如 `CopyOnWriteArrayList::new`。这会产生如清单 7-10 所示的代码，该代码既简洁又高效。

**清单 7-10. 使用收集器将流连接到集合**

`import java.util.*;`

`import java.util.concurrent.*;`

`import java.util.stream.*;`

`public class Listing10 {`

`public static Stream<Integer> generateParallelStream() {`

`final int elements = 1000;`

`List<Integer> toReturn = new ArrayList<>(elements);`

`for (int i = 0; i < elements; i++) {`

`toReturn.add(i);`

`}`

`return toReturn.parallelStream();`

`}`

`public static void main(String[] args) {`

`// 执行流处理`

`List<Integer> list = generateParallelStream()`

`.collect(Collectors.toCollection(CopyOnWriteArrayList::new));`

`}`

`}`



### 桥接数组与流

在数组和流之间来回转换实际上非常简单。要从数组获取流，请使用 `Stream.of(array)`。要从流获取数组，你有两种选择：要么接受一个 `Object` 实例数组并调用 `Stream.toArray()`；要么向该方法传递一种构造指定大小数组的方式，它将返回构造好的数组。定义供应器最简单的方法是传入数组构造器，例如 `Integer[]::new`。清单 7-11 演示了如何从数组转换为流以及从流转换为数组。

**清单 7-11\. 桥接数组与流**

`import java.util.stream.*;`

`public class Listing11 {`

  `public static Integer[] generateArray() {`

    `final Integer[] elements = new Integer[1000];`

    `for (int i = 0; i < elements.length; i++) {`

      `elements[i] = i;`

    `}`

    `return elements;`

  `}`

  `public static void main(String[] args) {`

    `Integer[] array = generateArray();`

    `Stream<Integer> stream = Stream.of(array);`

    `array = stream.toArray(Integer[]::new);`

  `}`

`}`

## 让接口对 Lambda 更友好

Java SDK 中的许多接口都只有一个非默认方法。`Runnable`、`Callable`、`AutoCloseable` 以及许多其他最常见的接口都是如此。这些接口可以直接使用 lambda 来实现，只需在需要该接口的地方使用 lambda 即可。我们在代码中经常看到这种技巧，最近一次是在清单 7-5 中，我们在 `generateMap()` 中返回了一个 lambda 作为 `ResourceExceptionHandler.FunctionWithResource` 的实现。因此，这些单方法接口已经可以很好地与 lambda 配合使用。其余的大部分接口都非常庞大，例如 `Collection`，显然无法通过单个 lambda 来实现其全部功能。

然而，SDK 中有一小部分接口——以及用户代码中的相当一部分接口——只有少数几个非默认方法，你可能非常希望能够通过 lambda 内联定义其实现。这是一个非常有用的技巧，但你无法使用 lambda 同时定义多个方法，而我们需要同时定义多个方法。我们该怎么做呢？

这就需要发挥一些创造力了。技巧在于定义一个新的单一方法，该方法可用于驱动所有其他方法的实现。然后用户指定这个新方法，我们的代码将委托给该新方法的实现。这个新方法和委托实现被放入一个扩展了目标接口的新接口中。

例如，假设我们想要创建一个可以用 lambda 定义的 `Iterator`。`Iterator` 上有两个非默认方法：`hasNext()` 和 `next()`。两者都与下一个元素有关，但前者测试是否存在而不前进，而后者则前进。因此，我们的单一方法需要能够在前进和非前进两种模式下运行。由于 `hasNext` 也可能查看迭代器的末尾之外（那里没有元素），我们的单一方法需要可选地返回其结果。结果就是这个方法：

`Optional<E> maybeNext(boolean advance);`

有了这个方法，我们就可以使用默认方法在迭代器中定义其他两个方法。对于 `next()`，我们调用 `maybeNext` 并传入 `true` 以进行前进。我们得到一个 `Optional` 结果，并使用 Optional API 要么返回其包含的值，要么抛出 `NoSuchElement` 异常。对于 `hasNext()`，我们调用 `maybeNext` 并传入 `false` 以避免前进。然后我们只需让调用者知道结果是否存在。所有这些都放入我们扩展了 `Iterator` 的新接口中。现在，用户可以将 lambda 赋值给这个类型，从而得到一个 Iterator。这段代码以及其工作原理的演示，见清单 7-12。

**清单 7-12\. 让 Iterator 对 Lambda 更友好**

`import java.util.*;`

`import java.util.concurrent.atomic.*;`

`public class Listing12 {`

`public interface LambdaIterator<E> extends Iterator<E> {`

`Optional<E> maybeNext(boolean advance);`

`default E next() {`

`return maybeNext(true).orElseThrow(NoSuchElementException::new);`

`}`

`default boolean hasNext() {`

`return maybeNext(false).isPresent();`

`}`

`}`

`public static Iterator<String> generateIterator() {`

`String[] values = new String[]{"Hello", " ", "World"};`

`AtomicInteger nextI = new AtomicInteger(0);`

`LambdaIterator<String> it = advance -> {`

`if (nextI.get() >= values.length) return Optional.empty();`

`String result = values[nextI.get()];`

`if (advance) nextI.incrementAndGet();`

`return Optional.of(result);`

`};`

`return it;`

`}`

`public static void main(String[] args) {`

`Iterator<String> it = generateIterator();`

`}`

`}`

这种方法的最大技巧在于，你必须想出一个巧妙的单一方法，能够处理任何给定方法的所有需求。你的超级方法确实可以访问接口中的其他方法，但如果你使用了这些其他方法，就需要格外小心，以免产生递归循环。想出一个好的单一方法，是那种应该能让你作为开发者感到兴奋的问题。



## 通过 Lambda 实现抽象类

在 Java 8 中，Lambda 表达式与接口相辅相成。它们实际上是同一底层功能的两种不同语法。不过，抽象类也能从 Lambda 的引入中获益。虽然语法不如接口那么优雅，但比传统的匿名内部类定义方式更加清晰。

与接口类似，其目标是将所有未定义方法的实现缩减为单个语句。但在此场景下，我们将尝试构建一个接受 Lambda 的构造函数，而非直接使用 Lambda。用户需要调用我们的构造函数（这不如直接使用 Lambda 方便），但这为我们提供了更大的操作空间：除了实现 Lambda 外，我们还可以接受其他配置值。

为了具体了解其工作原理，让我们看看 `AbstractCollection`。假设我们希望通过 Lambda 来实现一个 `AbstractCollection`。`AbstractCollection` 的两个抽象方法如下：

`public abstract Iterator<E> iterator()`

`public abstract int size()`

我们将通过向构造函数传递一个 Lambda 以及可能的其他参数来实现这两个方法。我们当然可以在构造函数中接受 size 作为参数，这样 `iterator()` 方法就留给 Lambda 实现。因此，我们需要用户提供类似 `Supplier<Iterator<E>>` 的东西作为 Lambda 参数。当然，我们会为该供应者提供一个接口类型，以便用户确切了解我们的需求。我们将这种集合称为 `LambdaCollection`。

为了演示如何使用我们的 `LambdaCollection`，假设我们希望将文件的行视为一个集合，并且愿意以低效（但有效）的方式实现。在这种情况下，我们可以定义一种方法，将文件的行转换为文件行的迭代器，这就是我们的 Lambda。此外，我们还需要在开始时遍历一次文件以获取大小。我们在清单 7-13 中展示了 `LambdaCollection` 的代码以及此用例。

**清单 7-13. 使用 Lambda 实现抽象类型**

`import java.io.File;`

`import java.io.IOException;`

`import java.io.UncheckedIOException;`

`import java.nio.file.Files;`

`import java.nio.file.Path;`

`import java.util.*;`

`public class Listing14 {`

`public static class LambdaCollection<E> extends AbstractCollection<E> {`

`private final int size;`

`private final Listing14.LambdaCollection.IteratorFactory<E> factory;`

`/**`

`* 定义获取 {@link java.util.Iterator} 的方式。`

`*/`

`public interface IteratorFactory<E> {`

`/**`

`* 创建 {@link java.util.Iterator} 的新实例。`

`* 每次调用此方法时，迭代器都应是全新的，`

`* 但它应始终迭代相同的源。`

`*`

`* @return 一个新的 {@code Iterator}；绝不能为 {@code null}。`

`*/`

`Iterator<E> create();`

`}`

`public LambdaCollection(int size, IteratorFactory<E> factory) {`

`this.size = size;`

`this.factory = factory;`

`}`

`/**`

`* 返回此集合中元素的迭代器。`

`*`

`* @return 此集合中元素的迭代器`

`*/`

`@Override`

`public Iterator<E> iterator() {`

`return factory.create();`

`}`

`@Override`

`public int size() {`

`return size;`

`}`

`}`

`public static Path generateFile() throws IOException {`

`File file = File.createTempFile("example", "tmp");`

`file.deleteOnExit();`

`Path path = file.toPath();`

`Files.write(path, "this\nis\nour\nfile".getBytes("UTF-8"));`

`return path;`

`}`

`public static void main(String[] args) throws Exception {`

`Path file = generateFile();`

`Collection<String> collection = new LambdaCollection<>(`

`(int) Files.lines(file).count(),`

`() -> {`

`try {`

`return Files.lines(file).iterator();`

`} catch (IOException ioe) {`

`throw new UncheckedIOException(`

`"从 " + file + " 读取行时出错", ioe);`

`}`

`}`

`);`

`}`

`}`

从清单 7-13 中需要认识到关键点是：我们将抽象方法的实现委托给了 Lambda。该 Lambda 被传递到构造函数中。这是使用 Lambda 实现抽象方法的基本模式。你可能会倾向于在构造函数中接受多个 Lambda，从而实现更多的抽象元素。但不要这样做，原因有二：首先，这会鼓励人们在应该使用可重用类时创建临时类；其次，这会导致代码难以阅读，而 Java 已有的匿名内部类定义格式在此场景下已经足够好用。这正是 Lambda 反而会降低可读性的一个特例。



## 透明可选技巧

这是处理遗留代码的最后一个非常有用的技巧。这个技巧是一种处理空值的方法。当 Java 遇到空值时，它会抛出令人厌烦且无处不在的 `NullPointerException`。`NullPointerException` 的消息如下所示：

`Exception in thread "main" java.lang.NullPointerException`

任何有经验的 Java 开发人员都知道，这是一条完全没有帮助的消息。首先，甚至不清楚哪个值是空值，因此该行上的任何内容都可能导致问题。该错误消息中也没有传达应用程序的任何状态。这条消息的效用极其有限，这也是应尽快将空值从代码库中彻底清除的原因之一。既然我们已经有了 `Optional`，就完全没有必要使用空值了。

当然，遗留代码没有 `Optional`，而且遗留开发人员可能不像我们现在这样开明。但是，我们可以摆脱空值，同时以一种透明的方式提供更有用的错误消息。这个技巧是在内部使用可选值，同时在外部提供（基本）一致的 API。实现一致 API 的要求是，如果值为空，则抛出 `NullPointerException`，我们可以使用 `Optional.orElseThrow(Supplier<Exception>)` 方法来实现。此外，我们提供一种将值作为 Optional 获取的方法，这样真正想检查值是否存在的人就可以通过这种方式进行检查。其意图是代码最终会迁移到直接对 Optional 类型进行操作的访问器上，因此我们可以弃用旧的 setter。作为一种语法上的便利，最好保留一个通过 `Optional.ofNullable(Object)` 直接设置值的 setter：如果你提供了该 setter 的重载，那么调用者就不必自己包装原始值了。清单 7-14 给出了此实现的一个示例。

**清单 7-14\. 向 Optional 迁移的 Bean 示例**

`import java.util.*;`

`public class SomeBean {`

    `private int id = 0;`

    `private Optional<String> name = Optional.empty();`

    `public SomeBean() {`

    `}`

    `public SomeBean(final int id, final String name) {`

      `this.id = id;`

      `this.name = Optional.ofNullable(name);`

    `}`

    `public int getId() {`

      `return id;`

    `}`

    `public void setId(final int id) {`

      `this.id = id;`

    `}`

    `@Deprecated`

    `public String getName() {`

      `return name.orElseThrow(`

        `() -> new NullPointerException(`

                    `"No name provided for: " + this.toString()`

              `)`

      `);`

    `}`

    `public Optional<String> getNameOptional() {`

      `return name;`

    `}`

    `public void setName(final String name) {`

      `this.name = Optional.ofNullable(name);`

    `}`

    `public void setName(final Optional<String> name) {`

      `this.name = (name == null ? Optional.empty() : name);`

    `}`

    `@Override`

    `public String toString() {`

      `return "SomeBean{" +`

          `"id=" + id +`

          `", name=" + name.orElse(null) +`

          `'}';`

    `}`

`}`

## 逆向开发

在结束本次讨论之前，同样值得注意的是，逆向开发通常很重要。如果你正在开发一个开源库，或者不确定阅读你代码的开发者的技能水平，那么你需要特别小心。并非每个 Java 开发人员都读过这本书，因此这种新的后函数式编程范式对他们来说可能非常陌生。例如，我最近参与的一个项目中出现了这样一行代码：

`(impl == null ? defaultImpl : impl).apply(arg);`

如果你知道 `impl` 和 `defaultImpl` 都是 `Function` 值，那么很容易理解这里发生了什么。另一方面，它看起来有些奇怪。在一系列流转换中间，它看起来就更奇怪了。即使是简单的 `forEach` 调用有时也会让人摸不着头脑。

鉴于此，需要尽可能清晰地编写代码。如果你要与可能不熟悉后函数式编程的开发人员合作，请对他们友好一些。在你期望 lambda 的地方为参数实现接口，这样他们就可以传入接口实现，并有一个明显的地方来查找文档。限制一个方法中接受的 lambda 数量，因为过多的 lambda 会导致回调迷宫。总的来说，要非常明确地表明你编写的代码对他们来说确实更清晰、更容易阅读。这不仅会减少你将遇到的问题（以及有问题的更改！）的数量，而且还会为这些开发人员提供学习和进步的机会。

