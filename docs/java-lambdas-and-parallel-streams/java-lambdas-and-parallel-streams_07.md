# 7. 初识流

好的，回到数据分析上来。

假设两个现有的过滤器无法合并成一个复杂的条件，而必须依次应用。或者我们需要单独的步骤来转换数据，等等，等等……

一般来说，数据将以链式方式处理。每个操作接收一些数据，执行某种操作，并产生一个中间结果，该结果作为下一个任务的输入。这种过程被称为流水线处理（见图 7-1）。

![A435427_1_En_7_Fig1_HTML.jpg](img/A435427_1_En_7_Fig1_HTML.jpg)

图 7-1.

经典处理链

使用流时，操作的工作方式有所不同。链中的所有转换都是“即时”执行的。从开发者的角度来看，链的起点有一个数据输入，终点有一个输出，但没有显式的中间结果（图 7-2）。

![A435427_1_En_7_Fig2_HTML.jpg](img/A435427_1_En_7_Fig2_HTML.jpg)

图 7-2.

流处理

开发者需要创建一个数据源，然后依次添加处理步骤，直到最后一个步骤。这个所谓的终端操作会创建结果。只有当最终操作被调用时，数据流才会开始。

其诀窍在于一个内部迭代器，它负责发射数据。因此，程序员无需创建循环（循环本质上就是外部迭代器）。开发者只需告诉系统如何处理数据。以前的循环现在看起来像一个串行过程——并且以后可以轻松地并行化。

## 示例

为了初步了解流的工作原理，我们来看几个用例。即使你现在不能完全理解也没关系——稍后我会详细讨论（见清单 7-1）。

```
1   List youngFemales = persons.stream()
2   .filter(p -> p.getAge() < 20)
3   .filter(Person::isFemale)
4   .collect(Collectors.toList());
清单 7-1.
列出所有年龄小于 20 岁的女性
```

代码 `person.stream()` 使用内部迭代器将所有数据作为流发射。第 2 行和第 3 行（清单 7-1）充当两个链式过滤器；在最后一行，结果数据被收集到一个列表中。我稍后会讨论 `collect()` 和 `Collectors`。

```
1   double averageAge = persons.stream()
2               .filter(p -> p.getAge() < 20 && p.isFemale())
3               .mapToInt(Person::getAge)
4               .average()
5               .getAsDouble();
清单 7-2.
所有小于 20 岁女性的平均年龄
```

在清单 7-2 中，我们首先过滤出与清单 7-1 相同的人群。为了稍微变化一下，条件被实现为一个组合条件。由于这对性能没有明显影响，第一种变体可能更可取，特别是因为可以在运行时动态地组合各个处理步骤。

在清单 7-2 的第 3 行，我们有一个映射，它接收流中的一个对象（Person）并产生一个不同的对象：流将继续处理该人的年龄。接下来，我们计算其平均值。在处理链的末端，我们将从 `Double` 类型获取结果，并将其赋值给左侧的变量（第 1 行的 averageAge）。

```
1   long totalSelling = persons
2           .stream()
3           .filter(Person::isVendor)
4           .mapToLong(p -> p.getSelling().values()
5                   .stream()
6                   .mapToLong(ArticleInfo::getQuantity)
7                   .sum())
8           .sum();
清单 7-3.
销售总量
```

提醒一下，所有数据都以人员列表的形式提供。其中一些人被标记为销售商。对于每个销售商，所有销售记录都存储在一个列表中。

在清单 7-3 中，计算了销售总量。使用传统方法，你需要实现两个循环：一个循环遍历人员，内部循环遍历销售记录。

使用流，它也是通过两个循环实现的，但这些循环对开发者是隐藏的。代码读起来像一个串行过程：获取所有人员，确定销售商，为每个销售商计算数量（第 4-7 行），然后计算最终总和。这清晰简洁——但可能不太熟悉。不过，一旦你掌握了 lambda 语法和内部迭代器，它就变得易于阅读。如果没有 lambda 和流，你能让代码更简短吗？

如果你不喜欢重复调用 sum 函数，我们可以用另一种方式实现计算。可以展平“流的流”。为此，Java 提供了 `flatMap`，或者在这个具体解决方案中，提供了 `flatMapToLong`（清单 7-4）。

```
1   long totalSelling = persons
2           .stream()
3           .filter(Person::isVendor)
4           .flatMapToLong(p -> p.getSelling().values().stream()
5           .mapToLong(ArticleInfo::getQuantity))
6           .sum();
清单 7-4.
使用展平流计算销售总量
```

如示例所示，流接口附带了一个流畅的 API。你可以通过点符号组合所有方法。因此，处理过程看起来更具声明性：你描述了对数据要进行的操作。对于处理过程，我们需要区分中间操作和终端操作。

中间操作是在流内部处理的操作。例如 `filter`、`map` 或 `sorted`。它们接收一个流并发射一个流。

终端操作会关闭流。此类操作的示例包括 `collect`、`reduce` 和 `sum`。可以理解，只能有一个终端操作。

直到终端操作被调用时，处理过程才会开始。因此，流实现了惰性求值的概念。

我们可以利用这种行为将中间步骤赋值给一个变量，并动态构建链。以下代码说明了这一概念；`someCondition()` 和 `someOtherCondition()` 是任何条件的占位符。

想象一个用户界面，用户在其中选择一些组合框、复选框和其他元素，这些元素用于构建处理过程。在实际应用中，你会遇到从简单到复杂的构建器（清单 7-5）。

```
1   Stream stream = persons.stream();
2   if (someCondition()){
3     stream = stream.filter(Person::isFemale);
4   }
5   if (someOtherCondition()){
6     stream = stream.filter(p -> p.getAge() < 20);
7   }
8   double averageAge = stream
9           .filter(p -> p.getAge() < 20 && p.isFemale())
10           .mapToInt(Person::getAge)
11           .average()
12           .getAsDouble();
清单 7-5.
处理链的动态构建
```

## 并行处理

![A435427_1_En_7_Figa_HTML.jpg](img/A435427_1_En_7_Figa_HTML.jpg)并行化

使用最后一个示例（或任何其他示例），将 `stream()` 替换为 `parallelStream()`。在多核机器上测量顺序变体和并行变体所需的时间。

为了计时，你可以使用第 4 章中引入的 `invokeMethod` 方法（见清单 7-6）。

```
1   Supplier totalSelling = () ->
2            persons.stream()
3           .filter(Person::isVendor)
4           .flatMapToLong(p -> p.getSelling().values().stream()
5           .mapToLong(ArticleInfo::getQuantity))
6           .sum();
7   System.out.println("total: " + invokeMethod(totalSelling));
清单 7-6.
测量时间
```

正如本书开头“解决方案”部分（第 1 章）所述，通过使用 `parallelStream()` 代替 `stream()` 来实现并行处理确实是可能的。尽管这看起来相当简单，但你必须考虑一些辅助条件。我将在后面进一步探讨。



## 摘要

通过默认方法 `stream()`，Java 8 在不破坏兼容性的前提下，增强了集合框架及其他接口的功能。它为你提供了一种表面上顺序化的编程方式，来解决那些底层需要循环的问题。最重要的是，你可以通过将此方法替换为 `parallelStream()` 来实现并行化。

在本章中，流主要通过简洁的示例进行了重点介绍。在初步了解之后，是时候深入探讨一些细节了。我将特别关注 `Stream` 和 `Spliterator` 这两个接口。

