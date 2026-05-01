# 6. Lambda 并发

Java 编程语言的核心是一系列指令。这些指令按顺序执行。默认情况下，任何时候你只能运行一个这样的指令序列。然而，如果你的机器有多个处理核心，或者有任何类型的 I/O 操作，那么这种单一的指令序列就会变得非常低效。如果你的程序有很多事情要做，但其单一的指令流必须等待“读取”I/O 指令完成后才能执行其他操作，那么你的程序就是在浪费时间。如果你有四个处理核心，却只有一个指令流，那么你最多只能利用四分之一的计算能力。解决方案是在你的程序中同时执行多个指令流，这被称为并发。

如果你读过任何关于 Java 8 中 Lambda 的内容，你可能已经听过“Lambda”和“并发”被同时提及。并发似乎是采用 Lambda 的主要驱动力之一，但 Lambda 究竟为何有助于并发，通常留给读者自行思考。在本章中，我们将了解 Lambda 如何以及为何能与并发如此完美地协同工作。我们将研究主要的 Java 并发技术，从历史悠久的 `Thread` 类开始，最终构建一个使用流的高度并发系统。这些流不是 I/O 流：它们是处理流。我们将看到，Lambda 使得传递可以在多个流中执行的指令变得容易：虽然 Java 在技术上一直具备这种能力，但 Lambda 使代码可读，而流则使其易于实现。

关于并发的讨论很容易变得抽象，而并发的示例也极易过于简单。为了避免这种情况，我们将在本章中使用一个一致的示例，并以不同的方式解决同一个问题。（这种反复以不同方式解决熟悉问题的实践被称为代码道场，该术语由 Dave Thomas 创造。）在我们的案例中，“待解决的问题”将是打印不断增大的素数：我们将使用 Java 的 `BigInteger` 类生成从 1 位长到 50,000 位长的素数。为了给并发留出空间，我们规定生成的数值不需要按顺序打印。参考实现见清单 6-1：在该清单中，我们以串行方式生成素数。

**清单 6-1\. 素数生成的参考实现**

`// PrimeFactory.java`

`import java.math.BigInteger;`

`import java.util.*;`

`import java.util.concurrent.*;`

`public class PrimeFactory {`

`/**`

`* 返回一个位长度为 {@code bitLength} 且很可能是素数的数字。`

`* 该值为素数的概率与 {@link BigInteger#probablePrime(int, java.util.Random)} 相同。`

`*`

`* @param bitLength 结果素数的位长度；必须为正数`

`* @return 一个位长度为 {@code bitLength} 且很可能是素数的随机 {@link java.math.BigInteger}。`

`* @see java.math.BigInteger#probablePrime(int, java.util.Random)`

`*/`

`public static BigInteger ofLength(int bitLength) {`

`return ofLength(bitLength, ThreadLocalRandom.current());`

`}`

`/**`

`* 返回一个位长度为 {@code bitLength} 且很可能是素数的数字。`

`* 该值为素数的概率与 {@link BigInteger#probablePrime(int, java.util.Random)} 相同。`

`*`

`* @param bitLength 结果素数的位长度；必须为正数`

`* @param rand      要使用的随机位生成器。`

`* @return 一个位长度为 {@code bitLength} 且很可能是素数的随机 {@link java.math.BigInteger}。`

`* @see java.math.BigInteger#probablePrime(int, java.util.Random)`

`*/`

`public static BigInteger ofLength(int bitLength, Random rand) {`

`if (bitLength <= 0) {`

`throw new IllegalArgumentException(`

`"位长度必须为正数；实际为 " + bitLength);`

`}`



`return new BigInteger(bitLength, ThreadLocalRandom.current());`

`}`

`}`

`// DemoRunner.java`

`import java.util.*;`

`/*`

`* 负责运行本章中的演示程序。`

`*/`

`public class DemoRunner {`

`/**`

`* 每个演示程序将运行的次数，用于获取计时数据。必须为奇数。`

`*/`

`public static final int RUN_COUNT = 3;`

`/**`

`* 要生成的素数的最大位长。`

`*/`

`public static final int MAX_BITLENGTH = 50000;`

`/**`

`* 本章演示程序需要实现的接口。`

`*/`

`public interface Demo {`

`/**`

`* 实现生成指定位长以内的素数。演示程序应生成每个素数并将其打印到标准输出。`

`*/`

`void generatePrimesToBitLength(int bitLength);`

`}`

`/**`

`* 运行演示程序 {@link #RUN_COUNT} 次，然后打印中位时间。`

`*`

`* @param demo 要运行的演示程序；不能为 {@code null}`

`*/`

`public static void run(Demo demo) {`

`Objects.requireNonNull(demo, "demo");`

`long[] timings = new long[RUN_COUNT];`

`for (int i = 0; i < timings.length; i++) {`

`System.out.println("开始第 " + (i+1) + " 轮");`

`long startTime = System.currentTimeMillis();`

`demo.generatePrimesToBitLength(MAX_BITLENGTH);`

`long endTime = System.currentTimeMillis();`

`timings[i] = endTime - startTime;`

`}`

`Arrays.sort(timings);`

`long medianTime = timings[timings.length / 2];`

`System.out.println("所有计时数据: " + Arrays.toString(timings));`

`System.out.println("中位时间: " + medianTime + "ms");`

`}`

`}`

`// Listing1.java`

`import java.math.BigInteger;`

`import java.util.*;`

`public class Listing1 {`

`public static void printPrimes(int maxBitLength) {`

`Random rand = new Random();`

`for (int i = 0; i < maxBitLength; i++) {`

`BigInteger prime = PrimeFactory.ofLength(i + 1, rand);`

`System.out.println(prime.toString());`

`}`

`}`

`public static void main(String[] args) {`

`DemoRunner.run(Listing1::printPrimes);`

`}`

`}`

我们的 `DemoRunner` 提供的计时并不科学，但足以用于基本比较。在我的电脑上，`Listing1.printPrimes` 完成大约需要 50 秒。（在你的电脑上，可能需要更长的时间；如果你在家运行代码且耗时过长，请减小 `MAX_BITLENGTH` 以获得更合理的结果。）然而，我的电脑几乎毫不费力：八个核心中只有四个在工作，且每个核心的利用率大约在 50%。让我们看看是否可以利用并发来更好地利用我们的硬件。

我们将从使用经典的 Java 线程开始；这可能是大多数开发者都熟悉的方式。Lambda 表达式在这里有一定帮助。然而，线程管理很棘手，人们希望使用线程池，因此 Java 引入了 `Executors` 的概念来简化事情。Lambda 表达式对执行器（executors）的帮助更大。但最重要的是，我们在 Java 8 中获得了执行流（streams of execution）的概念，它真正利用了 Lambda 表达式的强大功能，使并发变得简单。在本章结束时，我们将确切地看到这一点。

## Lambda 表达式与经典 Java 线程

第一个 Java 并发结构是 `Thread` 类，它仍然是 Java 语言中所有其他并发结构的基础。Lambda 表达式与 `Thread` 类配合得很好，因为 `Thread` 类是围绕 `Runnable` 接口构建的。正如我们在第 2 章中看到的，Lambda 表达式通过类型推断被隐式转换为接口实现，因此你可以编写以下代码：

`Runnable r = () -> System.out.println("Hello, World!");`

`Thread` 类有一个接受 `Runnable` 的构造函数。在 Java 8 Lambda 表达式的世界中，这意味着 `Thread` 类有一个接受 Lambda 表达式的构造函数：该 Lambda 表达式会被编译器转换为 `Runnable`。这意味着 Lambda 表达式使得编写 `Thread` 代码更加简单：无需使用匿名内部类或某个遥远的接口实现，你可以直接将 Lambda 表达式传入。

使用 `Thread` 类解决我们 kata 问题的一种简单方法是为每个要计算的值生成一个线程。不幸的是，线程并不廉价，这样做会很快耗尽虚拟机的资源。相反，我们将生成一定数量的线程，并将工作均匀地分配给他们。这些线程将执行 CPU 密集型工作，因此我们将为每个处理器核心分配一个这样的线程。我们将有另一个线程负责将值打印到标准输出，因为这是 I/O 密集型工作，并且多个线程同时尝试写入标准输出会形成瓶颈。这两个线程都将通过直接传入 Lambda 表达式来定义。

现在，线程的一个问题是它们默认会吞掉错误。如果你为线程提供了一些工作，而该工作因 `RuntimeException` 而崩溃，那么线程只会静默地消亡。为了获得某种错误消息，你必须设置一个 `Thread.UncaughtExceptionHandler` 实例。这可以在线程上设置，也可以全局设置，但同样的类型推断技巧将有助于使这段代码更具可读性：`Thread.UncaughtExceptionHandler` 是一个接口，因此我们可以内联实现它。这段代码：

`Thread.setDefaultUncaughtExceptionHandler(`

`new Thread.UncaughtExceptionHandler() {`

`@Override`

`public void uncaughtException(final Thread t, final Throwable e) {`

`System.err.println("Exception in " + t + ": " + e);`

`}`

`}`

`);`

变成了这样：

`Thread.setDefaultUncaughtExceptionHandler((t, e) -> {`

`System.err.println("Exception in " + t + ": " + e);`

`}`

`);`

整个实现的代码如清单 6-2 所示。

清单 6-2\. 使用 Lambda 表达式定义线程实例

`import java.math.BigInteger;`

`import java.util.*;`

`import java.util.concurrent.*;`

`import java.util.concurrent.atomic.*;`

`public class Listing2 {`

`private static void joinThread(Thread t) {`

`try {`

`t.join();`

`} catch (InterruptedException e) {`

`e.printStackTrace();`

`}`

`}`

`public static void printPrimes(int maxBitLength) {`

`Thread.setDefaultUncaughtExceptionHandler((t, e) -> {`

`System.err.println("Exception in " + t + ": " + e);`

`}`

`);`

`int processorCount = Runtime.getRuntime().availableProcessors();`

`BlockingQueue<String> primes = new LinkedBlockingQueue<String>();`

`Thread[] threads = new Thread[processorCount];`

`for (int t = 0; t < threads.length; t++) {`

`int modulus = t;`

`threads[modulus] = new Thread(() -> {`

`for (int i = 0; i < maxBitLength; i++) {`

`int bitLength = i + 1;`

`if (bitLength % processorCount == modulus) {`

`BigInteger prime = PrimeFactory.ofLength(bitLength);`

`primes.add(prime.toString());`

`}`

`}`

`}`

`);`

`threads[modulus].start();`

`}`

`AtomicBoolean doneSignal = new AtomicBoolean(false);`

`Thread printer = new Thread(() -> {`

`List<String> myPrimes = new ArrayList<>(threads.length);`

`for (Thread.yield(); !doneSignal.get(); Thread.yield()) {`

`primes.drainTo(myPrimes);`

`if (myPrimes.isEmpty()) {`

`// 多休息一下`

`Thread.yield();`

`} else {`

`System.out.println(String.join("\n", myPrimes));`

`myPrimes.clear();`



`}`

`}`

`}`

`);`

`printer.start();`

`for (int t = 0; t < threads.length; t++) {`

`joinThread(threads[t]);`

`}`

`doneSignal.set(true);`

`joinThread(printer);`

`}`

`public static void main(String[] args) {`

`DemoRunner.run(Listing2::printPrimes);`

`}`

`}`

主类只是委托给 `printPrimes`。该方法定义了我们的异常处理器，并为质数字符串创建了一个队列。然后，我们为每个处理器启动一个线程。这些线程中的每一个都会生成均匀分配的质数部分，并将它们放入一个阻塞队列中。接着，我们启动另一个线程，其唯一任务是从该队列中读取数据并打印出这些值。我们需要某种方式来通知打印机线程任务已完成，因此必须声明一个信号，打印机需要在每次循环时监控该信号。整个过程感觉就像一台鲁布·戈德堡机械。

在我的电脑上，这段代码运行大约需要 14.5 秒，并且我们愉快地占用了所有处理器。然而，这段代码相当丑陋：我们在做一件极其简单的事情，却有大量仪式性的代码和噪音掩盖了这种简单的努力。所有这些仪式和噪音之所以必要，是因为我们直接使用了 `Thread` 类。以这种方式处理线程非常笨拙，这也是 Java 引入执行器（executors）的原因。在下一节中，我们将看到 lambda 表达式也有助于清理这些代码。

## Lambda 表达式与执行器

`ExecutorService` 及其相关类是 `Thread` 类的便捷包装器。它们提供了经过验证且标准的线程执行模式：有用于动态调整大小的线程池的执行器、用于固定大小线程池的执行器、按需生成线程然后缓存它们的执行器，以及包装单个线程的执行器。如果你发现自己想用线程做一些比执行器支持的功能更花哨的事情，那么你要么是在攻读博士学位，要么是做错了什么。

在所有情况下，用法都是一样的：你将 `Callable` 接口的一个实例传递给执行器，它会返回一个 `Future` 实例，该实例是结果的一个句柄。这个 `Future` 实例还提供了对处理过程中发生的任何错误的访问。这种用法相比线程采用的 `Runnable` 方法有三个主要优点：第一，你可以从并发处理中返回一个值；第二，你的错误不会被静默吞没（假设你从 `Future` 中获取了结果）；第三，一个线程只能运行一个 `Runnable` 实例，但执行器的线程可以处理多个 `Callable` 实例。不过，就像线程的内联 `Runnable` 实现一样，我们可以使用 lambda 表达式内联提供我们的 `Callable` 实现。这使得代码更易于阅读。

当我们使用执行器重写我们的练习时，可以看到这些优势。我们不会想出一个巧妙的方法来均匀分配工作，而是简单地将所有待完成的工作添加到我们的主处理执行器中。和之前一样，该执行器将拥有与我们拥有的处理器数量一样多的线程，并负责 CPU 密集型工作。我们将 I/O 密集型工作交给另一个执行器，该执行器将是一个单线程。由于交接更清晰，我们可以将 I/O 密集型线程设置为最高优先级，确保它尽可能多地执行：我们再次看到 lambda 表达式使代码更具可读性，因为我们可以内联指定 `ThreadFactory` 实现。此练习的代码在清单 6-3 中给出。

清单 6-3\. 使用 Lambda 表达式定义 ExecutorService 工作实例

`import java.math.BigInteger;`

`import java.util.*;`

`import java.util.concurrent.*;`

`public class Listing3 {`

`public static void printPrimes(int maxBitLength) {`

`ExecutorService executor =`

`Executors.newFixedThreadPool(Runtime.getRuntime().availableProcessors());`

`ExecutorService printExecutor = Executors.newSingleThreadExecutor(r -> {`

`Thread t = new Thread(r);`

`t.setPriority(Thread.MAX_PRIORITY);`

`return t;`

`}`

`);`

`// 最终的工作结果`

`List<Future<?>> futures = new ArrayList<>(maxBitLength);`

`for (int i = 0; i < maxBitLength; i++) {`

`int bitLength = i + 1;`

`Future<String> stringFuture = executor.submit(() -> {`

`BigInteger prime = PrimeFactory.ofLength(bitLength);`

`return prime.toString();`

`}`

`);`

`futures.add(printExecutor.submit(() -> {`

`String primeString = stringFuture.get();`

`System.out.println(primeString);`

`return null;`

`}`

`)`

`);`

`}`

`// 发出信号，表示不再添加新任务`

`executor.shutdown();`

`printExecutor.shutdown();`

`// 等待所有任务完成并检查错误`

`futures.parallelStream().forEach(future -> {`

`try {`

`future.get();`

`} catch (InterruptedException | ExecutionException e) {`

`e.printStackTrace();`

`}`

`}`

`);`

`}`

`public static void main(String[] args) {`

`DemoRunner.run(Listing3::printPrimes);`

`}`

`}`

与清单 6-2 相比，这段代码要好得多。我们无需声明标志或交接队列；相反，我们只需将一个 `Future` 传递给另一个任务的工作，然后处理生成的 futures。我们仍然能饱和处理器核心，并且仍然拥有专用的 I/O 线程。你可能会担心执行器类相对于我们“手动调优”的实现所带来的开销，但实际上，在我的电脑上，结果反而快了半秒，达到了 14.0 秒。

如果你仔细观察，可能已经注意到了 `futures.parallelStream()` 的调用。我们在上一章中看到，流可以设置为并行处理，从而带来一些额外的透明并发性。在这里，并行性使我们能够非常快速地检查 futures 中的错误，而代价仅仅是一个方法调用。这非常强大，但我们还没有准备好深入探讨这种并发方法：我们将在本章后面部分讨论。Java 现有的并发工具还有更多可以探索的地方！



### Lambda 表达式与 ThreadPoolExecutor

以上内容让你对 lambda 如何让执行器（executor）的使用变得如此便捷有了基本了解。然而，有一个特定场景，内联定义接口实现的能力极其有用，那就是与 `ThreadPoolExecutor` 配合使用时。

`ThreadPoolExecutor` 是 `ExecutorService` 类的一个高度可定制的实现。许多程序员（包括本书作者）都非常喜欢调整各种参数，而这个类提供了大量可供调节的“旋钮”。其中一些旋钮是数值，但该类也提供了许多回调方法。每个回调都是一个接口，这意味着突然之间，有很多地方 lambda 表达式能让你的工作轻松许多。然而，好事过头也可能变成坏事。这很好地展示了 lambda 表达式在提升代码清晰度方面的局限性。

问题在于 `ThreadPoolExecutor` 拥有这样一个构造函数：

`public ThreadPoolExecutor( int corePoolSize,`

`int maximumPoolSize,`

`long keepAliveTime,`

`TimeUnit` `unit,`

`BlockingQueue` `<` `Runnable` `> workQueue,`

`ThreadFactory` `threadFactory,`

`RejectedExecutionHandler` `handler )`

在 Java 8 中，人们很容易想将 `threadFactory` 和 `handler` 内联提供。不幸的是，这会导致代码看起来像这样：

`ThreadPoolExecutor tpe = new ThreadPoolExecutor(`

`processorCount,`

`processorCount * 2 + 1,`

`1L, TimeUnit.SECONDS,`

`new ArrayBlockingQueue<>(processorCount * 4 + 1),`

`r -> {`

`Thread t = new Thread(r);`

`t.setPriority(Thread.MAX_PRIORITY);`

`return t;`

`},`

`(rejected, executor) -> {`

`BlockingQueue<Runnable> fullQueue = executor.getQueue();`

`// 等待超时`

`try {`

`boolean submitted = fullQueue.offer(`

`rejected,`

`1L, TimeUnit.SECONDS`

`);`

`if (submitted) return;`

`} catch (InterruptedException e) {`

`// 继续执行`

`}`

`// 如果执行到这里，说明队列确实已经满了`

`// 首先，执行我们自己的任务`

`rejected.run();`

`// 然后，出于友好，再执行另一个任务`

`// （这可以减慢生产者线程的速度，并可能打破死锁。）`

`Runnable otherWork = fullQueue.poll();`

`if (otherWork != null) otherWork.run();`

`}`

`);`

这段代码极难理解，尤其是如果你没有刚刚查阅过该构造函数的定义。这里存在三个问题：首先，构造函数参数过多，难以理清每个参数的作用；其次，连续多个 lambda 表达式造成了深层嵌套，难以分辨一个 lambda 的结束和另一个的开始；第三，lambda 表达式本身的实现非常冗长，使得难以看清构造函数的结束位置以及后续代码的开始位置。这些问题对读者来说可能显而易见，但对于 API 设计者和代码编写者来说，写出这样的代码却是非常自然的过程。你很可能在不久的将来无意中写出类似的代码。

最佳实践是每个方法调用只使用一个 lambda 表达式。由于任何单方法接口都容易被转换为 lambda 表达式，你应该限制你的方法只包含一个这样的接口参数。然而，我们现在面对的是违反这一最佳实践的情况，而且由于它存在于 Java SDK 中，短期内不太可能被修复。在处理遗留代码时，解决这个问题的方法是使用语义化变量：先创建并赋值，然后将它们传递给构造函数。

`int processorCount =`

`Math.max(1, Runtime.getRuntime().availableProcessors());`

`int corePoolSize = processorCount;`

`int maxPoolSize = processorCount * 2 + 1;`

`long threadTimeoutMag = 1L;`

`TimeUnit threadTimeoutUnit = TimeUnit.SECONDS;`

`BlockingQueue<Runnable> queue =`

`new ArrayBlockingQueue<>(processorCount * 4 + 1);`

`ThreadFactory threadFactory = r -> {`

`Thread t = new Thread(r);`

`t.setPriority(Thread.MAX_PRIORITY);`

`return t;`

`};`

`RejectedExecutionHandler rejectHandler = (rejected, executor) -> {`

`BlockingQueue<Runnable> fullQueue = executor.getQueue();`

`// 等待超时`

`try {`

`boolean submitted = fullQueue.offer(rejected, 1L, TimeUnit.SECONDS);`

`if (submitted) return;`

`} catch (InterruptedException e) {`

`// 继续执行`

`}`

`// 如果执行到这里，说明队列确实已经满了`

`// 首先，执行我们自己的任务`

`rejected.run();`

`// 然后，出于友好，再执行另一个任务`

`// （这也可以减慢生产者线程的速度，并可能打破死锁。）`

`Runnable otherWork = fullQueue.poll();`

`if (otherWork != null) otherWork.run();`

`};`

`ThreadPoolExecutor tpe = new ThreadPoolExecutor(`

`corePoolSize,`

`maxPoolSize,`

`threadTimeoutMag, threadTimeoutUnit,`

`queue,`

`threadFactory,`

`rejectHandler`

`);`

这会在你的作用域中创建一堆变量。当然，解决方法是将其移动到单独的方法中。考虑到代码的行数，这很可能是个好主意。lambda 到接口的转换也可以放在它们自己的方法中完成。最终的代码如清单 6-4 所示。请注意，这段代码为子类提供了方便的扩展点：这仅仅是清理难以阅读的代码也能带来更好结构化代码的另一个例子。这展示了区分良好实践与不良实践的最简单方法：不良实践会产生附带损害；良好实践则会产生附带收益。

**清单 6-4. 负责任地创建 ThreadPoolExecutor 并使用 Lambda 表达式**

`protected ThreadFactory createThreadFactory() {`

`return r -> {`

`Thread t = new Thread(r);`

`t.setPriority(Thread.MAX_PRIORITY);`

`return t;`

`};`

`}`

`protected RejectedExecutionHandler createdRejectedExecutionHandler() {`

`return (rejected, executor) -> {`

`BlockingQueue<Runnable> fullQueue = executor.getQueue();`

`// 等待超时`

`try {`

`boolean submitted = fullQueue.offer(rejected, 1L, TimeUnit.SECONDS);`

`if (submitted) return;`

`} catch (InterruptedException e) {`

`// 继续执行`

`}`

`// 如果执行到这里，说明队列确实已经满了`

`// 首先，执行我们自己的任务`

`rejected.run();`

`// 然后，出于友好，再执行另一个任务`

`// （这也可以减慢生产者线程的速度，并可能打破死锁。）`

`Runnable otherWork = fullQueue.poll();`

`if (otherWork != null) otherWork.run();`

`};`

`}`

`public ThreadPoolExecutor createThreadPoolExecutor() {`

`int processorCount = Math.max(1,`

`Runtime.getRuntime().availableProcessors());`

`int corePoolSize = processorCount;`

`int maxPoolSize = processorCount * 2 + 1;`

`long threadTimeoutMag = 1L;`

`TimeUnit threadTimeoutUnit = TimeUnit.SECONDS;`

`BlockingQueue<Runnable> queue =`

`new ArrayBlockingQueue<>(processorCount * 4 + 1);`

`ThreadFactory threadFactory = createThreadFactory();`

`Objects.requireNonNull(threadFactory,`

`"线程池执行器的线程工厂");`

`RejectedExecutionHandler rejectHandler = createdRejectedExecutionHandler();`

`Objects.requireNonNull(rejectHandler,`

`"线程池执行器的拒绝执行处理器");`

`return new ThreadPoolExecutor(`

`corePoolSize,`

`maxPoolSize,`

`threadTimeoutMag, threadTimeoutUnit,`

`queue,`

`threadFactory,`

`rejectHandler`

`);`

`}`



## Lambda 表达式与 Fork/Join

上次我们讨论按比特长度生成质数时，通过利用 Java 的执行器（executors）获得了显著的性能提升。然而，这需要大量的设置和冗余代码。如果你只需指定要完成的工作，而无需担心并发细节，那岂不是更好？好消息是，你可以做到，只需使用 Java 的 Fork/Join 池。在 Java 7 中，该功能已存在于 SDK 中。在 Java 8 中，Lambda 表达式使该功能更加用户友好。

Fork/Join 池的基本概念是，你处理的任务可以派生（fork）出额外的工作，然后可以将这些工作合并（join）回主线程。如果你确实关心底层线程池的管理，可以使用 `ForkJoinPool` 类来创建和管理它。然而，你也可以完全忽略池的管理：你只需提供要并发执行的工作，启动这些工作，然后在稍后的某个时间点将这些工作合并回你的线程。就是这么简单：其余部分由 Java 处理。

当然，对于应该传递给 Fork/Join 框架的工作类型，有很多注意事项：例如，长时间阻塞的工作需要小心处理。你可以在 `ForkJoinTask` 和 `ForkJoinPool` 类的 JavaDoc 中阅读这些细节。然而，对于我们正在处理的工作类型来说，它绝对完美。

更棒的是，Fork/Join 框架与流（streams）配合得非常好：你可以在流的一个阶段派生工作，并在另一个阶段合并它。为了实现我们的解决方案，我们将定义一个从 1 到最大比特长度的数字并行流。然后，我们将并行流映射到一个由派生的 `ForkJoinTask` 实例组成的并行流，每个实例负责生成一个质数。接着，我们将这些任务合并回来（处理错误），并将结果打印到标准输出。此代码如清单 6-5 所示。

**清单 6-5. 使用 Lambda 表达式定义 Fork/Join 任务**

`import java.util.concurrent.*;`

`import java.util.stream.*;`

`public class Listing5 {`

  `public static void printPrimes(int maxBitLength) {`

    `Stream.iterate(1, i -> i+1).limit(maxBitLength).parallel()`

      `.map(i ->`

            `ForkJoinTask.adapt(() -> PrimeFactory.ofLength(i)).fork()`

      `)`

      `.map(ForkJoinTask::join)`

      `.forEach(System.out::println);`

  `}`

  `public static void main(String[] args) {`

    `DemoRunner.run(Listing5::printPrimes);`

  `}`

`}`

这段代码在我的机器上大约执行 14 秒。这意味着这段代码与之前手动编写的执行器代码性能相当，而且代码量显著减少。如果你所做的只是 CPU 密集型工作，或者主要是 CPU 密集型工作并伴有非常短暂的阻塞工作（例如从本地磁盘上的私有文件读取），那么这对你来说是一个完美的解决方案。

## 流并行性

在上一章中，我们了解到流有顺序流和并行流之分，并且顺序流是默认的。而在上一节中，我们使用了并行流来实现最大并发。但是，当我们从顺序流切换到并行流时，到底发生了什么？为了查看差异，我们可以运行清单 6-6 中的代码。

**清单 6-6. 演示顺序流与并行流差异的代码**

`// NumberStreamFactory.java`

`import java.util.stream.*;`

`public class NumberStreamFactory {`

`/**`

`* 返回一个并行流，该流返回从 1 到 {@code value} 的 {@code int} 值。`

`*`

`* @param value 要计数到的值；必须为正数。`

`* @return 一个返回 1, 2, ..., {@code value} 的流。`

`*/`

`public static Stream<Integer> countTo(int value) {`

`if (value <= 0) {`

`throw new IllegalArgumentException("需要一个正数来计数，但给定的值是 " + value);`

`}`

`return Stream.iterate(1, i -> i + 1).limit(value).parallel();`

`}`

`}`

`// Listing6.java`

`public class Listing6 {`

`public static void main(String[] args) {`

`System.out.println("\n\n\n 并行");`

`NumberStreamFactory.countTo(200).parallel().map(i -> {`

`System.out.println("map: " + i +`

`" 线程: " + Thread.currentThread().getName());`

`return i;`

`}`

`).forEach(i ->`

`System.out.println("forEach: " + i +`

`" 线程: " + Thread.currentThread().getName())`

`);`

`System.out.println("\n\n\n 顺序");`

`NumberStreamFactory.countTo(200).sequential().map(i -> {`

`System.out.println("map: " + i +`

`" 线程: " + Thread.currentThread().getName());`

`return i;`

`}`

`).forEach(i ->`

`System.out.println("forEach: " + i +`

`" 线程: " + Thread.currentThread().getName())`

`);`

`}`

`}`

`/* 示例结果`

`并行`

`map: 13 线程: ForkJoinPool.commonPool-worker-4`

`map: 132 线程: main`

`map: 163 线程: ForkJoinPool.commonPool-worker-1`

`map: 32 线程: ForkJoinPool.commonPool-worker-3`

`forEach: 32 线程: ForkJoinPool.commonPool-worker-3`

`map: 182 线程: ForkJoinPool.commonPool-worker-2`

`forEach: 182 线程: ForkJoinPool.commonPool-worker-2`

`map: 33 线程: ForkJoinPool.commonPool-worker-3`

`...`

`顺序`

`map: 1 线程: main`

`forEach: 1 线程: main`

`map: 2 线程: main`

`forEach: 2 线程: main`

`map: 3 线程: main`

`forEach: 3 线程: main`

`map: 4 线程: main`

`forEach: 4 线程: main`

`map: 5 线程: main`

`forEach: 5 线程: main`

`map: 6 线程: main`

`forEach: 6 线程: main`

`map: 7 线程: main`

`forEach: 7 线程: main`

`...`

`*/`

正如我们所见，顺序流很简单：它们在调用者的线程中执行。它们会尽可能地对一个元素执行完整个操作流，然后再处理下一个元素。然而，有些操作（例如 `stream.sorted()`）需要处理整个流：流在消费完整个流之前，无法知道当前元素是否是最小的。因此，这个调用充当了一个屏障：流中的特定元素在后续元素被处理之前，无法越过它。

并行流则更有趣。如果你有足够的元素，它们会将部分工作委托给公共的 `ForkJoinPool` 实例。这与我们的 Fork/Join 任务执行的线程池是同一个，这很好地展示了这些框架如何协同工作。部分工作可能仍在本地执行（例如数字 132），但无论如何，同一个线程会尽可能地在流中执行下去（如数字 32 和 182 所示）。



然而，你绝不能假设流中的元素会按顺序处理，也不能假设它们生成后会立即被处理——我们在处理第六个元素（编号 33）之前，已经从流中生成了 182 个元素。如果流中的每个元素大小都是 1GB，那我们就麻烦了！流的生成速度可能远超处理速度，这些元素会一直缓存在内存中，直到被处理。因此，并行流最好生成非常小的元素（例如索引或文本行），并在流处理过程中将这些小元素构建成更大的元素，这始终是最佳实践。

另一个需要注意的问题是，你的 Fork/Join 任务将与流操作本身共享公共的 Fork/Join 池。因此，除非你使用 `ForkJoinTask.join()` 或 `ForkJoinTask.invoke()`，否则绝不能让你的流操作等待 Fork/Join 任务解析——如果任务尚未启动，这两个方法会让当前线程自行处理该任务。任何其他对 Fork/Join 任务解析的直接或间接依赖，都可能导致死锁。

然而，如果你能生成小元素流，流操作不会长时间阻塞，并且你对 Fork/Join 任务稍加注意，那么流并行性就是让你的 Java 应用获得显著并发收益的简单方法。你只需指定你想要发生的事情，让运行时为你处理并发管理和优化。所有这些都可以通过可读性强的代码实现，因为 lambda 表达式使得像传递数据一样传递行为成为可能。

## 结论

我希望你现在能认识到 lambda 表达式和流在简化并发代码方面的强大能力。代码变得非常易读，并且你将执行细节与待完成的工作分离开来。这确实是后函数式代码中最令人印象深刻的部分之一。

请注意，其他方法仍有其适用的时机和场景。单线程适用于一次性“发射后不管”的钩子，或长时间运行的后台守护任务。当你需要处理各种工作并对线程池有更多控制时，Executor 非常有用。Fork/Join 框架在处理递归问题时表现出色。但对于连接一系列操作这种非常常见的情况，lambda 表达式和流框架是 Java SDK 中强大且受欢迎的补充。

