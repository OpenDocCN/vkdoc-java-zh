# 9. 并行流

如果通过使用 `parallelStream` 能够正确地并行处理数据，你需要考虑一些辅助条件。如果不遵守这些条件，Java 要么会禁止并行执行，要么你可能会得到意想不到的结果。

*   使用无状态的 lambda 表达式。状态意味着你在某处（例如，在一个变量中）保存了一个值。现在，如果 lambda 表达式的未来执行依赖于这个值，那么并行执行的行为可能会变得不确定。结果可能变得不可预测。幸运的话，编译器会阻止并行化。否则，你会对结果感到困惑。
*   不要改变流所基于的数据。例如，如果你在遍历集合时添加或删除元素，在顺序处理时可能还能工作。这样的代码是不健康的，并且常常会导致异常。对于并行流，你不能使用这种糟糕的代码：你总是会得到一个异常。
*   避免副作用。这是函数式编程的主要原则。当提供相同的参数时，一个函数总是返回相同的结果。应该避免像改变一个作用域更广的变量这样的副作用。如果你正在使用流，并且想使用一个作用域更广的变量，只有当这些变量像 final 变量一样被使用时，编译器才会接受。像 NetBeans 这样的现代 IDE 会给你建议：从 lambda 表达式引用的变量必须是 final 的，或者实际上是 final 的。
*   处理必须与顺序无关。因为在并行处理的情况下，顺序是不确定的，所以结果不依赖于处理顺序是至关重要的。或者你需要强制进行有序处理。这会产生一些开销，可能会消耗你通过并行化节省的时间。一个依赖于特定顺序的计算问题可以通过下面的例子来研究。看似相同的计算以三种不同的方式实现。为了重现稳定或变化的结果，每个变体都被多次调用。

```
1   long[] result = new long[1];

3   for (int i = 0; i < 5; i++) {
4     result[0] = 0;
5     LongStream.range(0, 1000)
6       .forEach(n -> result[0] = (result[0] + n) * n);
7     System.out.println("serial: " + result[0]);
8   }

10   for (int i = 0; i < 5; i++) {
11     result[0] = 0;
12     LongStream.range(0, 1000).parallel()
13       .forEach(n -> result[0] = (result[0] + n) * n);
14     System.out.println("parallel: " + result[0]);
15   }

17   for (int i = 0; i < 5; i++) {
18     result[0] = 0;
19     LongStream.range(0, 1000).parallel()
20       .forEachOrdered(n -> result[0] = (result[0] + n) * n);
21     System.out.println("parallel ordered: " + result[0]);
22   }
清单 9-1.
顺序、并行和有序并行处理
```

该流通过 `range` 生成为一个 `LongStream`。这会在所需范围内提供数字。`Collection` 接口中没有 `parallelStream`，但你可以通过 `parallel()` 切换到并行处理。

顺序变体以及有序并行变体总是报告结果为 3003755479748501215，而无序并行版本会产生不同的结果。

`result[0] = (result[0] + n) * n)` 的计算依赖于顺序。很明显，如果 n 的位置是 0，那么下一个中间结果将是 0（如果最后处理，最终结果也是 0）。因为零会在任何时候被使用，你无法预测结果。如果你进一步研究数学，就会发现对于任何其他值，它也依赖于顺序。

为了简单演示，这三个演示都使用了副作用。所有中间结果都将存储在一个外部变量中。由于这必须是实际上是 final 的，我们使用了一个小技巧：我们使用了一个数组，而不是一个 `long`。

在生产应用中，永远不要使用这种小技巧！这是一个干净的变体。

```
1   long reduce = LongStream.range(0, 1000).reduce(0, (a, c) -> (a + c) * c);
```

对于那些不熟悉 lambda 的人来说，这看起来非常奇怪，而那个“脏”的解决方案似乎更容易理解。这就是我为什么用它来做演示。永远不要害怕干净的解决方案。使用 lambda 一小段时间后，这种编程方式就相当容易了。

这里是对终端 `reduce` 操作的简短解释。第一个参数用于初始化一个累加器。第二个操作是一个二元函数，它接收累加器 (a) 和流的当前对象 (c)。此操作的结果将替换累加器的先前值，并可用于流的下一个对象。

*   通过使用 `Collect` 并考虑 `Concurrent`

```
    1    Map<Integer, List<Person>> ageMap = persons
    2      .stream()
    3     .collect(Collectors.groupingBy(Person::getAge));

5    ConcurrentMap<Integer, List<Person>> ageMapPar = persons
    6      .parallelStream()
    7     .collect(Collectors.groupingByConcurrent(Person::getAge));
    ```

*   收集结构（此处：`ConcurrentMap`）可能具有 `Collector.Character` 和 `Collector.Characteristics.UNORDERED` 特性。

## 摘要

尽管使用流进行并行编程无需显式线程处理，并且通常无需同步，但你仍然需要考虑一些约束条件才能获得正确的结果。接下来，我们将结合 Java 并发性来研究 `collect` 方法。



