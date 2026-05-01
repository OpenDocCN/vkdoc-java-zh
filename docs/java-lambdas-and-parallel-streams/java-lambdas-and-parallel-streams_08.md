# 8. stream()、Stream 与 Spliterator

到目前为止，你已经对如何使用 `stream()` 和 `parallelStream()` 有了初步印象。我们想深入幕后，进一步研究这些方法。

![A435427_1_En_8_Figa_HTML.jpg](img/A435427_1_En_8_Figa_HTML.jpg)研究 Java 中的实现。了解如何在你的 IDE 中显示 Java 源代码。以 [NetBeans](http://netbeans.org/) 为例，¹ 只需打开前面的某个示例，将光标放在 `stream()` 上。使用组合键 {Ctrl+B} 即可调用 `goto` 源代码。

`stream()` 和 `parallelStream()` 是 `Collection` 接口的默认方法（参见 8-1）。

```
1   default Stream stream() {
2     return StreamSupport.stream(spliterator(), false);
3   }

5   default Stream parallelStream() {
6     return StreamSupport.stream(spliterator(), true);
7   }
Listing 8-1.
接口 Collection 的摘录
```

如你所见，这两个方法非常相似。两者都调用了 `StreamSupport.stream()`。唯一的区别在于第二个参数。这个布尔类型的参数 `parallel` 可以是 `false` 或 `true`。因此，这就清楚了为什么在许多情况下你可以互换使用这两个方法。

这两个方法都返回一个流，该流由接口 `Stream` 定义。并且它们使用了一个所谓的 Spliterator，通过 `spliterator()` 调用。我将在接下来的段落中讨论这两者。

## Stream

接口 `Stream` 定义了许多返回流的方法。它们不是获取整个输入来生成一个完整的流，而是一个元素一个元素地生成。举个例子，我们来看一下 `filter` 的签名：

```
Stream filter(Predicate predicate);
```

这会根据过滤条件生成一个流。值得注意的是，`filter` 接收一个类型为 T²（或其超类）的元素，该元素是流的一个元素。如前所述，`Predicate` 是一个函数式接口。因此，可以使用 lambda 表达式来实现该谓词。由于类型在流中是已知的，lambda 表达式无需声明类型。请参见前面关于 lambda 表达式规则的段落。

正如 `filter` 所示，`Stream` 定义了中间操作的工作方式。输出元素的类型可能与输入不同（例如 `map`）。映射操作的输出是一个对象流，其类型可能与输入不同。例如，我们可以将一个人映射为其年龄或姓名。

对于某些数据类型，定义了专门的流。例如，有 Long 或 Double 流，它们支持额外的数学运算。并且在 `Stream` 内部，定义了一些中间操作来转换为这种专门的流。在前面的示例中，我们使用了 `.mapToLong(...))`，它对预定义的流类型执行了这样的映射。

```
LongStream mapToLong(ToLongFunction mapper);
```

`LongStream` 并非派生自 `Stream`，但两者都派生自 `BaseStream`。

除了中间操作，Stream 接口还提供了终端操作以及流的生成。

终端操作不仅仅是简单或复杂的“结果”，例如 `min`、`max`、`collect`（用于收集数据，例如到列表中），还包括为每个元素显式调用一个方法。

```
void forEach(Consumer action);
```

通过 `forEach`，内部迭代器被提供给一个实现了 `Consumer` 接口的方法。这也是另一个函数式接口。

让我们来看看终端操作。例如，`min` 操作通常返回数值流的最小值。如果流不包含任何值会发生什么？结果必须为空。为了避免空结果，所有这类操作都会生成一个预期类型的可选值。这个结果的类型不是 `T`，而是 `Optional<T>`。

要描述 `Stream` 提供的所有方法，将远远超出这本简明书籍的范围。其目的是让你很好地理解这些原理。建议阅读 [API 文档](https://docs.oracle.com/javase/8/docs/api/java/util/stream/Stream.html)，³ 该文档可通过脚注 3 中的 URL 获取（在撰写本文时有效）。

## Spliterator

`Spliterator` 负责将对象流拆分成更小的部分。其名称是“拆分迭代器”的缩写。这就是前面多次提到的内部迭代器。除了迭代之外，拆分功能使你能够将流的部分提供给不同的线程。

你可以将 spliterator 用于顺序处理以及并行处理。Java 8 附带了一个默认实现（参见 8-2）。

```
1   @Override
2   default Spliterator spliterator() {
3       return Spliterators.spliterator(this, 0);
4   }
Listing 8-2.
默认方法 Spliterator
```

对于特殊用例，可以创建你自己的 spliterator。虽然在大多数情况下，现有的实现已经足够，但理解基本概念非常有用。

接口 `Spliterator` 定义了两个重要的方法。

```
boolean tryAdvance(Consumer action);
```

上述代码尝试为下一个元素执行一个操作——如果存在的话。如果成功推进，此方法返回 `true`，而 `false` 表示没有元素（即流的末尾）。这与常见的迭代器概念有显著区别：使用迭代器时，你首先必须检查是否至少还有一个元素存在。如果 `hasNext()` 返回 `true`，你可以通过 `next()` 获取它。

`tryAdvance` 有一个配套方法，用于遍历剩余元素。第二个重要的方法如下。

```
Spliterator trySplit();
```

这尝试以有意义的方式拆分数据。如果底层数据结构的大小是已知的，例如，数据保存在数组中，那么 `trySplit` 会将其拆分为两个（几乎）大小相等的部分：`trySplit` 返回一个额外的 spliterator，如果需要，该 spliterator 可以再次拆分数据。通过这种方式，整个数据集将被拆分成大量较小的数据集，这些数据集可能可以并行处理。

如果数据量未知，例如，因为你从文件读取数据或通过某个数据通道在线接收数据，那么拆分将根据其他标准执行。例如，一个文件可以在每 1,024 行之后进行拆分。

尽管 `tryAdvance` 和 `trySplit` 是理解 spliterator 行为的最重要方法，但此接口还提供了更多的方法和字段。如果需要，你可以在 [API 文档](https://docs.oracle.com/javase/8/docs/api/java/util/Spliterator.html) 中阅读相关内容。⁴



## 摘要

接口 `Stream` 定义了多种方法来处理流中的数据。中间操作会执行一个动作并生成另一个流。这使得你可以通过点号表示法（流畅 API）来链式处理步骤。

`Spliterator` 是内部的迭代器，它能够将数据分割成更小的块。这使得这些块能够被并行处理。有时你需要考虑一些约束条件才能成功实现并行化。我将在第 9 章讨论这一点。

脚注 1

[`http://netbeans.org`](http://netbeans.org)

  2

如果你不了解泛型的通用类型表示法，可以上网查阅，例如，参考 [维基百科：de.wikipedia.org/wiki/Generische_Programmierung_in_Java](https://de.wikipedia.org/wiki/Generische_Programmierung_in_Java) 或 Oracle 的 [Java 教程](https://docs.oracle.com/javase/tutorial/java/generics/methods.html)：[docs.oracle.com/javase/tutorial/java/generics/methods.html](https://docs.oracle.com/javase/tutorial/java/generics/methods.html)。

  3

[`https://docs.oracle.com/javase/8/docs/api/java/util/stream/Stream.html`](https://docs.oracle.com/javase/8/docs/api/java/util/stream/Stream.html)

  4

[`https://docs.oracle.com/javase/8/docs/api/java/util/Spliterator.html`](https://docs.oracle.com/javase/8/docs/api/java/util/Spliterator.html)

