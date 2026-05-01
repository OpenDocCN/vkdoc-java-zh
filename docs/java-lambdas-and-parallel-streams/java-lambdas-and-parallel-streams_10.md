# 10. 收集器与并发

到目前为止，我们一直在使用 `collect()` 方法，但尚未深入解释。这个重载方法要么接受一个收集器（Collector），就像我们之前所做的那样（例如 `.collect(Collectors.toList())` 或 `.collect(Collectors.groupingByConcurrent(Person::getAge))`）；要么你可以传入三个参数，分别对应供应器（supplier）、累加器（accumulator）和组合器（combiner）。先别困惑，我很快就会解释这些概念。

顾名思义，`collect` 的主要目的是收集数据。这是通过修改可变数据结构来实现的。与此相反，`reduce()` 方法则适用于不可变数据结构。

现在，是时候编写我们自己的收集器了。为简单起见，我们创建一个收集器来对数字流进行求和。这些数字由一个简单的数字生成器生成（代码清单 10-1）。

```
1   public static List createNumbers() {
2     List numbers = new ArrayList();
3     Random random = new Random();
4     int max = 1000000 + random.nextInt(1000000);
5     for (int i = 0; i < max; i++) {
6       numbers.add((long)random.nextInt(100));
7     }
8     return numbers;
9   }
代码清单 10-1.
数字生成器
```

但首先，我们快速了解一下 Java 并发。尽管 `parallelStreams()` 避免了所有手动线程处理，但了解一些基础知识有助于理解如何编写一个能够成功并行执行的收集器。

## Java 并发

我们需要讨论两个术语：“并发”和“并行”。在继续之前，我们必须先定义这两个术语，因为我们既要讨论 Java 并发，又要讨论并行流。

对于并发，程序的多个部分将在不同的线程中执行，以并发方式而非串行方式运行。这并不一定意味着这些线程同时运行。回想一下过去每台计算机只有一个单核 CPU（中央处理器）的时代：为了保持用户界面（UI）流畅，最佳实践是将 UI 处理放在一个线程中，而将耗时的计算放在另一个线程中。这两个线程交替获得少量的 CPU 时间片。在现代多核 CPU 上，这些线程可以并行运行。

另一方面，在并行中，多个并行运行的线程会同时对不同的数据执行相同的计算。并行流实现了并行性。请看下图（图 10-1）：并行流包含相同类型的不同数据。每个中间操作只定义一次，并按照垂直对齐所示的方式并行执行。

![A435427_1_En_10_Fig1_HTML.jpg](img/A435427_1_En_10_Fig1_HTML.jpg)

图 10-1.

带有中间操作的并行流

人们可能将并行视为并发的一种特殊情况，或者将其视为不同的概念。在本书中，我们不会深入讨论这个问题。无论如何，要使用 Java 实现并行，我们在技术上需要并发。

![A435427_1_En_10_Figa_HTML.jpg](img/A435427_1_En_10_Figa_HTML.jpg) 进程与线程

在本书中，我们只涉及线程，它们是程序的独立单元，运行在同一个进程中。进程提供了一个完全独立的环境，拥有自己的内存，而线程则运行在共享环境中，特别是共享它们的内存。如果多个线程使用同一个类，这一事实就变得很重要。

更详细的解释请参考 [Java 教程](https://docs.oracle.com/javase/tutorial/essential/concurrency/procthread.html)。¹

Java 从一开始就被设计为支持多线程。`Thread` 类自 JDK（Java 开发工具包）1.0 起就已成为 Java 的一部分。

让我们看看如何定义并启动第二个线程（代码清单 10-2）。

```
1   public static void main(String[] args) {
2     Thread thread = new MyThread();
3     thread.start();
4     System.out.println("Message from Main");
5   }

7   private static class MyThread extends Thread {
8     @Override
9     public void run() {
10       System.out.println("Message from MyThread");
11     }
12   }
代码清单 10-2.
Thread 的简单用法
```

我们只需定义一个继承自 `Thread` 的类，并重写 `run()` 方法。在主程序中，我们创建线程的一个新实例，并调用 `start()`。在幕后，`start` 向操作系统请求一个新线程，并在这个新线程中执行 `run` 方法。

但有一个问题。通常，你希望通过继承一个技术类（而非 `Thread`）来构建类结构。幸运的是，Java 提供了另一种创建线程的方式。我们需要实现 `Runnable` 接口。而这正是 `Thread` 类内部所做的（代码清单 10-3）。

```
1   public static void main(String[] args) {
2     Thread thread = new Thread(new MyRunnable());
3     thread.start();
4     System.out.println("Message from Main");
5   }

7   private static class MyRunnable implements Runnable {
8     @Override
9     public void run() {
10       System.out.println("Message from MyRunnable");
11     }
12   }
代码清单 10-3.
使用 Runnable 的线程
```

现在，通过传入我们类的一个对象来创建一个新线程。其他一切保持不变。



掌握了这些知识后，我们想利用线程来执行求和任务。我们创建了一个名为 `SummingUnit` 的类，它负责执行计算（清单 10-4）。

```
1   public class SummingUnit {
2     public SummingUnit(){
3       System.out.println("ctor SummingUnit");
4     }

6     private long _sum = 0;

8     public long getSum() {
9       return _sum;
10     }

12     public void sum(long value) {
13       _sum += value;
14     }

16     public void combine(SummingUnit other) {
17       _sum += other._sum;
18     }

20   }
清单 10-4.
SummingUnit
```

该类内部使用一个字段来累加传递给 `sum()` 方法的值。通过一个简单的 getter 方法来读取总和。`combine()` 方法最初并未使用，我们稍后会用到它。还记得 `collect` 的 combiner 参数吗？

为了演示，一个新线程将处理我们一半的数字，而另一半则由“主”线程处理。为了实现这个目标，我创建了 `SumTask`，它接收用于前半部分和后半部分的相应参数。该类的一个实例由新线程调用，另一个实例则直接调用。为了同步这两个线程，主线程通过 `thread.join` 等待，直到生成的线程完成（清单 10-5）。

```
1   public static void main(String[] args) throws InterruptedException {
2     List numbers = Utils.createNumbers();
3     int size = numbers.size();
4     SummingUnit summingUnit = new SummingUnit();

6     Thread thread = new Thread(new SumTask(numbers, 0,
7             size / 2, summingUnit));
8     thread.start();

10     SumTask sumTask = new SumTask(numbers, size / 2,
11             size, summingUnit);
12     sumTask.run();
13     thread.join(); // 等待线程完成

15     System.out.println("Sum: " + summingUnit.getSum());
16   }

19   private static class SumTask implements Runnable {

21     private final List _numbers;
22     private final int _start;
23     private final int _end;
24     private final SummingUnit _summingUnit;

26     public SumTask(List numbers,
27                             int start,
28                             int end,
29                             SummingUnit summingUnit) {
30       _numbers = numbers;
31       _start = start;
32       _end = end;
33       _summingUnit = summingUnit;
34     }

36     @Override
37     public void run() {
38       for (int i = _start; i < _end; i++) {
39         _summingUnit.sum(_numbers.get(i));
40       }
41     }
42   }
清单 10-5.
并行求和——结果出乎意料
```

然而，如果你多次运行这个程序，即使你等待第二个线程完成，也会得到不同的结果。发生了什么？

请注意 `sum()` 方法。

```
sum += val;
```

乍一看，这似乎是一个单一操作。但实际上，这只是语法糖，我们可以重写这条语句。

```
sum = sum + val;
```

在我们常见的硬件结构中，不可能在一个变量内部完成加法操作。实际上，计算机必须将 `sum` 的值加载到寄存器中，加上 `val`，然后将结果写回变量。因此，这行代码可以用伪代码重写如下。

```
1   register.load(sum)
2   register.add(val)
3   register.write(sum)
```

如你所见，这是一个非原子性的操作序列。在这两个操作之间，CPU 可能会执行其他操作。

假设 `sum` 的值为 0。我们有两个线程，每个线程都添加一个值。线程 1 可能加 5，而线程 2 加 3。以下序列产生了预期的结果：

| 操作 | 结果 |
| --- | --- |
| 线程 1 register1.load(sum) | register1 = 0 |
| 线程 1 register1.add(5) | register1 = 5 |
| 线程 1 register1.write(sum) | sum = 5 |
| 线程 2 register2.load(sum) | register2 = 5 |
| 线程 2 register2.add(3) | register2 = 8 |
| 线程 2 register2.write(sum) | sum = 8 |

但由于两个线程独立运行，我们无法确定执行顺序。在另一次运行时，语句可能按以下顺序执行：

| 操作 | 结果 |
| --- | --- |
| 线程 1 register1.load(sum) | register1 = 0 |
| 线程 1 register1.add(5) | register1 = 5 |
| 线程 2 register2.load(sum) | register2 = 0（此时 sum 仍然是 0！） |
| 线程 1 register1.write(sum) | sum = 5 |
| 线程 2 register2.add(3) | register2 = 3 |
| 线程 2 register2.write(sum) | sum = 3 |

我们看到的是一个典型的竞态条件。结果取决于哪个线程在争夺共享变量的竞赛中领先。`sum()` 方法成为了代码中的一个临界区。这样的区域必须受到保护，例如通过锁。或者你可以使用 Java 的 `synchronized` 限定符。

```
public synchronized void sum(long val)
```

现在这个方法将自动受到保护。一次只允许一个线程运行此方法，而其他线程则被迫等待。所有对 `sum()` 的调用都变成了串行，这破坏了并行性。这不能算是一个合适的解决方案。

Volatile

你可能听说过在多线程环境中变量的 `volatile` 限定符，并猜测这将是一个不错的选择。遗憾的是，这并没有帮助。`Volatile` 仅保证在写入之后的任何读取都将获取最后写入的值。看看第一个线程序列，特别是 `Thread 2 register2.load(sum)`。`sum` 在前一步刚刚被写入。之前的值是 0。如果读取访问查询的是缓存，会发生什么？尽管刚刚将 5 写入了变量，但 Java 可能读取到之前的 0。`Volatile` 由于其保证避免了这个问题，但也仅此而已。因此，`volatile` 没有帮助（除非我们可能还有其他错误）。

更多信息，请阅读关于 [Java 内存模型](https://www.cs.umd.edu/%7Epugh/java/memoryModel/jsr-133-faq.html) 的内容。²

对于我们这个问题，最好的解决方案可能是避免临界区。为了避免共享变量，我们只需为每个线程创建一个单独的 `SummingUnit`。每个线程将解决我们问题的一部分——计算一个小计。最后，我们必须将它们全部合并。

以下是并行求和的示例——每个线程使用一个单独的 `SummingUnit`：

```
1   public static void main(String[] args) throws InterruptedException {
2     List numbers = Utils.createNumbers();
3     int size = numbers.size();
4     SummingUnit summingUnit = new SummingUnit();
5     SummingUnit summingUnit2 = new SummingUnit();

7     Thread thread = new Thread(new SumTask(numbers, 0,
8             size / 2, summingUnit));
9     thread.start();

11     SumTask sumTask = new SumTask(numbers, size / 2,
12             size, summingUnit2);
13     sumTask.run();
14     thread.join(); // 等待线程完成

16     summingUnit.combine(summingUnit2);
17     System.out.println("Sum: " + summingUnit.getSum());
18   }
```

在前述代码的第 5 行，创建了第二个 `SummingUnit`，它在第 12 行被使用。然后（第 16 行），合并了小计。由于每个线程都使用自己的对象，我们可以避免任何同步。



## 收集

在简要了解了 Java 并发机制之后，我们将回到并行流和 `collect()` 方法。之前提到的那个接受三个参数的重载方法定义如下：

```
1    R collect(Supplier supplier,
2                 BiConsumer accumulator,
3                 BiConsumer combiner);
```

第一个参数接受一个函数，该函数提供类型为 R 的新结果容器。我们使用 `SummingUnit` 作为 R 的类型。

累加器参数是一个函数，它接受一个结果容器 R（即 `SummingUnit`）和一个类型为 T 的流对象。在我们的示例中，这个对象是其中一个数字。

最后一个参数接受一个函数，Java 会向该函数传入两个结果容器。此函数执行合并操作。

综合以上所有内容，我们在 collect 方法中使用 `SummingUnit`（清单 10-6）。

```
1   System.out.println("total: " +
2     numbers.parallelStream().collect(
3       () -> new SummingUnit(),     // supplier
4       (summingUnit, value) ->
5         summingUnit.sum(value),    // accumulator
6       (summingUnit, other) ->
7         summingUnit.combine(other) // combiner
8     ).getSum()
9   );
清单 10-6.
在 Collect 中使用 SummingUnit
```

现在，如果你运行这个程序，可能会发现应用打印出了几个“ctor SummingUnit”实例。请记住，我们在 SummingUnit 的构造函数中打印了这条信息。这表明 Java 调用了多个线程来执行工作。实际上，Java 使用了 fork/join 框架来调度线程。该框架的原则是判断任务是否足够小以串行执行。如果任务较大，则会将问题分解（最好分成两个大小相似的部分），然后两个部分会在不同的线程中处理。因此，这个算法是递归应用的。

如果你对 fork/join 框架的更多细节感兴趣，请参考 [Java 教程](https://docs.oracle.com/javase/tutorial/essential/concurrency/forkjoin.html)。³

如前所述，你可以自由选择 lambda 参数的名称。其作用域非常狭窄。因此，通常选择简短的名称。

```
1   .collect(() -> new SummingUnit(),
2   (s, v) -> s.sum(v), (s, o) -> s.combine(o))
```

或者，我们可以使用方法引用。

```
1   .collect(SummingUnit::new, SummingUnit::sum, SummingUnit::combine)
```

正如前面的示例所示，收集数据相当容易。通常，并行运行如此简单的任务并不值得，但将所学知识迁移到你技术领域中更复杂的任务应该没有问题。特别是运行时间较长的任务能从并行中获益。

除了向 `collect()` 方法传递一组函数外，我们还可以编写一个 Collector，并将其传递给 `collect()`。以下是该重载方法的签名：

```
1    R collect(Collector collector);
```

Collector 是一个我们需要实现的接口。我们创建一个类 `SummingCollector`，它将总和作为最终结果返回。

```
1   System.out.println("total: " +
2             numbers.parallelStream().collect(new SummingCollector()));
```

Collector 接口强制我们重写一些返回函数的方方法。使用 lambda，定义可以作为参数传递的函数非常容易。除了（惊喜！）supplier、accumulator 和 combiner 之外，我们还需要重写 finisher。finisher 接受最后一个剩余的结果容器进行进一步处理。我们的 collector 将返回最终的总和。

为了重用我们已经开发的大部分代码，SummingCollector 会在内部将大部分任务委托给 SummingUnit。

我们需要重写的最后一个方法返回一组特征（清单 10-7）。

```
1   public class SummingCollector
2                 implements Collector{
3     @Override
4     public Supplier supplier() {
5       return () -> new SummingUnit();
6     }

8   @Override
9   public BiConsumer accumulator() {
10     return (s, v) -> s.sum(v);
11   }

13   @Override
14   public BinaryOperator combiner() {
15     return (left, right) -> {left.combine(right); return left;};
16   }

18   @Override
19   public Function finisher() {
20     return s -> s.getSum();
21   }

23   @Override
24   public Set characteristics() {
25     return EnumSet.of(Characteristics.UNORDERED);
26     // 不要添加 ", Characteristics.CONCURRENT"!
27   }
清单 10-7.
SummingCollector
```

我们使用三个类型 `<T, A, R>` 对 Collector 接口进行参数化，如签名所示。这里 T 是 Long（一个数字），A 被替换为 SummingUnit，最终的结果类型 R 也是 Long：即最终的总和。

因为我们没有返回 CONCURRENT 特征，所以每个线程都会调用 supplier 函数。你可以通过观察对象构造期间的输出来查看这一点。

如果我们提供了 CONCURRENT 特征，Java 会假定 supplier 已启用多线程支持。它将只创建一个 SummingUnit，由所有线程共享。猜猜看，结果会因运行而异，因为我们有一个存在竞态条件的临界区。

## 总结

在本章中，我们讨论了收集器的原理。示例展示了如何实现一个收集器。流的并行版本使用起来非常简单，并且隐藏了所有关于线程、锁定、同步的繁琐细节。但是，对 Java 并发特性以及竞态条件等典型问题稍有了解，有助于编写正确的收集器。

## 最终思考

尽管这个示例对于讨论并行性的某些方面非常有用，但由于以下几个原因，它并非一个现实世界的示例：

1.  没有必要实现一个求和函数，因为 Java 提供了一个现成的

    ```
    numbers.parallelStream().mapToLong(i -> i).sum();
    ```

    Java 内部使用一个“肮脏”的技巧来实现求和，即使用一个数组来保存简单的值。  
2.  除了收集数据，我们也可以简单地归约数据。

    ```
    numbers.parallelStream().reduce(0L, (a, c) -> a + c);
    ```

    reduce 函数在每次加法时都会创建一个新对象。它从零开始。然后它接受两个参数：累加器和当前对象。它返回两者的和，该和作为一个新对象存储到累加器中。因为没有共享对象，所以没有共享状态，也没有竞态条件等。

但有时我们确实需要一个收集器，例如，如果我们想将对象收集到集合或映射中。这将在下一章中展示。

脚注 1

[`https://docs.oracle.com/javase/tutorial/essential/concurrency/procthread.html`](https://docs.oracle.com/javase/tutorial/essential/concurrency/procthread.html)

  2

[`https://www.cs.umd.edu/~pugh/java/memoryModel/jsr-133-faq.html`](https://www.cs.umd.edu/%7Epugh/java/memoryModel/jsr-133-faq.html)

  3

[`https://docs.oracle.com/javase/tutorial/essential/concurrency/forkjoin.html`](https://docs.oracle.com/javase/tutorial/essential/concurrency/forkjoin.html)



