# 4. 集合、Stream 和 Optional

本章涵盖与 Java 9 中集合、`Stream` 和 `Optional` 变更相关的主题。

## 集合的工厂方法

Java 9 添加了一些便捷方法，可用于创建不可变集合。

### `List.of()` 方法

新的静态方法 `List.of()` 有多个重载版本，可用于从零个或多个元素创建不可变列表，参见清单 4-1。

```
List.of();
List.of("Hello", "World");
List.of(1, 2, 3);
清单 4-1.
List.of() 示例
```

`List.of()` 不允许 `null` 值。向其传递 `null` 会导致 `NullPointerException`。



### `Set.of()` 方法

新的静态方法 `Set.of()` 也提供了多个重载版本，用于从零个或多个元素创建不可变集合；参见清单 4-2。

```
Set.of();
Set.of("Hello", "World");
Set.of(1, 2, 3);
清单 4-2.
Set.of() 示例
```

由于集合不允许包含重复元素，向 `Set.of()` 传入重复元素会导致其抛出 `IllegalArgumentException`。`Set.of()` 不允许 `null` 值。向其传入 `null` 会导致 `NullPointerException`。

### `Map.of()` 和 `Map.ofEntries()` 方法

两个新的静态方法 `Map.of()` 和 `Map.ofEntries()` 用于创建包含零个或多个条目的不可变映射。

`Map.of()` 有 11 个不同的重载版本，用于创建包含零到十个条目的映射；参见清单 4-3。键和值成对指定。

```
Map.of(); // 空映射
Map.of("Hello", 1, "World", 2); // -> 包含两个条目的映射
清单 4-3.
Map.of() 示例
```

`Map.ofEntries()` 可以从任意数量的 `Map.Entry` 对象创建映射；参见清单 4-4。

```
Map.ofEntries(
new AbstractMap.SimpleEntry("Hello", 1),
new AbstractMap.SimpleEntry("World", 2)
);
清单 4-4.
Map.ofEntries() 示例
```

`Map.of()` 和 `Map.ofEntries()` 不允许 `null` 键或值。传入重复键会导致它们抛出 `IllegalArgumentException`。向其传入 `null` 会导致 `NullPointerException`。

## Arrays

本节讨论 Java 9 中添加到 `java.util.Arrays` 的方法。

### `Mismatch()` 方法

`mismatch()` 方法组用于查找并返回两个数组之间第一个不匹配元素的索引。如果没有不匹配，`mismatch()` 返回 `-1`。针对 `boolean`、`byte`、`char`、`double`、`float`、`int`、`long`、`short` 和 `Object` 数组，有不同的重载版本。你还可以在两个数组中指定起始和结束索引来检查不匹配。对于对象数组，你还可以指定一个 `java.util.Comparator` 对象进行比较。在清单 4-5 中，你可以看到 `mismatch()` 的不同测试用例。

```
@Test
public void testMismatch() throws Exception {
assertEquals(0, Arrays.mismatch(new int[]{1}, new int[]{2}));
assertEquals(1, Arrays.mismatch(new int[]{1}, new int[]{1, 2}));
assertEquals(1, Arrays.mismatch(new int[]{1, 3}, new int[]{1, 2}));
assertEquals(-1, Arrays.mismatch(
new int[]{1, 3}, 0, 1,
new int[]{1, 2}, 0, 1));
}
清单 4-5.
mismatch() 示例
```

### `Compare()` 方法

`compare()` 方法组用于按字典顺序比较两个数组。针对 `boolean`、`byte`、`char`、`double`、`float`、`int`、`long`、`short` 和 `Object` 数组，有不同的重载版本。你还可以在两个数组中指定起始和结束索引进行比较。对于对象数组，你还可以指定一个 `java.util.Comparator` 对象进行比较。清单 4-6 测试了 `compare()` 的不同用例。

```
@Test
public void testCompare() throws Exception {
assertEquals(0, Arrays.compare(new int[]{1}, new int[]{1}));
assertTrue(Arrays.compare(new int[]{0}, new int[]{1}) < 0);
assertTrue(Arrays.compare(new int[]{1}, new int[]{0}) > 0);
assertEquals(0, Arrays.compare(
new int[]{1, 3}, 0, 1,
new int[]{1, 2}, 0, 1));
}
清单 4-6.
compare() 示例
```

### `Equals()` 方法

`equals()` 方法组用于判断两个数组是否相等，如果相等则返回 `true`。针对 `boolean`、`byte`、`char`、`double`、`float`、`int`、`long`、`short` 和 `Object` 数组，有不同的重载版本。你还可以在两个数组中指定起始和结束索引进行相等性检查。对于对象数组，你还可以指定一个 `java.util.Comparator` 对象进行比较。清单 4-7 测试了 `equals()` 的不同用例。

```
@Test
public void testEquals() throws Exception {
assertTrue(Arrays.equals(new int[]{1}, new int[]{1}));
assertTrue(Arrays.equals(
new int[]{1, 2}, 0, 1,
new int[]{1, 3}, 0, 1));
}
清单 4-7.
equals() 示例
```

## Stream

本节介绍 Java 9 中添加到 `java.util.stream.Stream` 的新方法。

### `ofNullable()` 方法

静态方法 `Stream<T> ofNullable(T t)` 返回一个包含零个或一个元素的 `Stream`，具体取决于输入值是否为 `null`。清单 4-8 展示了此方法的示例。

```
@Test
public void testOfNullable() throws Exception {
assertEquals(1, Stream.ofNullable("").count());
assertEquals(0, Stream.ofNullable(null).count());
}
清单 4-8.
Stream.ofNullable() 示例
```

### `dropWhile()` 方法

方法 `Stream<T> dropWhile(Predicate<? super T> predicate)` 会丢弃流中的元素，直到遇到第一个不匹配谓词的元素。如果流是有序的，则丢弃与给定谓词匹配的最长前缀元素。如果流是无序的，并且该流的部分（而非全部）元素与给定谓词匹配，则无法确定哪些元素被丢弃。但是，如果流的所有元素都与给定谓词匹配，或者流的任何元素都不与给定谓词匹配，那么流是有序还是无序就无关紧要了；结果是确定的。

在清单 4-9 中，流包含从 `1` 到 `5` 的元素。谓词检查元素是否为奇数。第一个元素 `1` 被丢弃。结果流包含四个元素。

```
@Test
public void testDropWhile() throws Exception {
final long count = Stream.of(1, 2, 3, 4, 5)
.dropWhile(i -> i % 2 != 0)
.count();
assertEquals(4, count);
}
清单 4-9.
Stream.dropWhile() 示例
```

### `takeWhile()` 方法

方法 `Stream<T> takeWhile(Predicate<? super T> predicate)` 对流执行与 `dropWhile()` 相反的操作。`takeWhile()` 会获取流中的元素，直到遇到第一个不匹配谓词的元素。在处理有序和无序流时，`takeWhile()` 的行为与 `dropWhile()` 相同。

在清单 4-10 中，当像清单 4-6 那样使用 `takeWhile()` 处理流时，结果流只有一个值为 `1` 的元素。

```
@Test
public void testTakeWhile() throws Exception {
final long count = Stream.of(1, 2, 3, 4, 5)
.takeWhile(i -> i % 2 != 0)
.count();
assertEquals(1, count);
}
清单 4-10.
Stream.takeWhile() 示例
```

### `iterate()` 方法

静态方法 `Stream<T> iterate(T seed, Predicate<? super T> hasNext, UnaryOperator<T> next)` 是一种使用迭代器模式生成流的新方式。`seed` 是迭代的初始元素。`hasNext` 是应用于当前元素以检查流是否应终止的谓词。`next` 是应用于当前元素以生成流中下一个元素的函数。如果初始元素 `seed` 不匹配谓词 `hasNext`，则结果流为空。

在清单 4-11 中，结果流使用 `2` 作为初始元素，并将当前值增加 `2` 以生成下一个值。当值大于 `10` 时，流终止。流中将包含 `5` 个元素。

```
@Test
public void testIterate() throws Exception {
final Stream stream =
Stream.iterate(2, i -> i <= 10, i -> i + 2);
assertEquals(5, stream.count());
}
清单 4-11.
Stream.iterate() 示例
```

### `IntStream`、`LongStream` 和 `DoubleStream`

方法 `dropWhile()`、`takeWhile()` 和 `iterate()` 也被添加到了 `IntStream`、`LongStream` 和 `DoubleStream` 中。

## Collectors

Java 9 中，`java.util.stream.Collectors` 也新增了几个方法。



### `filtering()` 方法

方法 `<T, A, R> Collector<T, ?, R> filtering(Predicate<? super T> predicate, Collector<? super T,A,R> downstream)` 通过应用谓词函数来过滤输入元素，并且仅当谓词返回 `true` 时，才将元素累积到下游的 `Collector` 中。

清单 4-12 展示了如何使用 `filtering()` 根据字符串长度过滤 `String` 流，并将其收集到一个 `Set` 中。实际上，这可以简化为先使用 `Stream.filter()`，然后将 `String` 收集到 `Set` 中。

```
@Test
public void testSimpleFiltering() throws Exception {
final Set result = Stream.of("a", "bc", "def")
.collect(Collectors.filtering(
v -> v.length() > 1,
Collectors.toSet()));
assertEquals(2, result.size());
}
清单 4-12.
Collectors.filtering() 的简单示例
```

清单 4-13 展示了一个更复杂的 `filtering()` 示例。变量 `users` 是一个将用户名映射到其年龄的 Map。此示例使用 `groupingBy()` 根据名称的首字符对 Map 条目进行分组。然后，它使用 `filtering()` 仅保留年龄大于 `18` 的条目。最后，它使用 `mapping()` 将条目映射为名称。结果是一个将每个名称的首字符映射到名称 `Set` 的 Map。由于 `Bob` 的年龄仅为 `16`，因此结果 Map 中键 `B` 对应的值是一个空的 `Set`。

```
@Test
public void testComplicatedFiltering() throws Exception {
final Map users = Map.of(
"Alex", 30,
"Bob", 16,
"David", 50
);
final Map> result = users
.entrySet()
.stream()
.collect(Collectors.groupingBy(entry -> entry.getKey().substring(0, 1),
Collectors.filtering(entry -> entry.getValue() > 18,
Collectors.mapping(
Map.Entry::getKey,
Collectors.toSet()))));
assertEquals(1, result.get("A").size());
assertEquals(0, result.get("B").size());
assertEquals(1, result.get("D").size());
}
清单 4-13.
Collectors.filtering() 的复杂示例
```

### `flatMapping()` 方法

方法 `<T, U, A, R> Collector<T, ?, R> flatMapping(Function<? super T,? extends Stream<? extends U>> mapper, Collector<? super U,A,R> downstream)` 对输入元素应用一个扁平映射函数，并将映射后流中的元素累积到下游的 `Collector` 中。

在清单 4-14 中，给定一个 `String` 流，你可以看到 `flatMapping()` 被用于将 `String` 映射为 `Integer` 流，然后将所有 `Integer` 收集到一个 `Set` 中。结果 `Set` 仅包含三个元素。

```
@Test
public void testFlatMapping() throws Exception {
final Set result = Stream.of("a", "ab", "abc")
.collect(Collectors.flatMapping(v -> v.chars().boxed(),
Collectors.toSet()));
assertEquals(3, result.size());
}
清单 4-14.
Collectors.flatMapping() 的示例
```

## Optional

`java.util.Optional` 中新增了三个方法。

### `ifPresentOrElse()` 方法

方法 `void ifPresentOrElse(Consumer<? super T> action, Runnable emptyAction)` 在值存在时执行给定的 `action`，否则执行给定的 `emptyAction`。

在清单 4-15 中，方法 `checkValue()` 根据值是否存在来增加或减少 `count`。第一次调用 `checkValue()` 会减少 `count`，而第二次调用则会增加 `count`。

```
public class OptionalTest {
private AtomicInteger count;
@Before
public void setUp() throws Exception {
this.count = new AtomicInteger();
}
@Test
public void testIfPresentOrElse() throws Exception {
checkValue(Optional.empty());
assertEquals(-1, this.count.get());
checkValue(Optional.of(1));
assertEquals(0, this.count.get());
}
private void checkValue(final Optional value) {
value.ifPresentOrElse(
v -> this.count.incrementAndGet(),
() -> this.count.decrementAndGet()
);
}
}
清单 4-15.
Optional.ifPresentOrElse() 的示例
```

### `Optional.or()` 方法

方法 `Optional<T> or(Supplier<? extends Optional<? extends T>> supplier)` 在值存在时返回包含该值的 `Optional`，否则返回由供给函数生成的 `Optional`。清单 4-16 展示了此方法的示例。

```
@Test
public void testOr() throws Exception {
assertTrue(Optional.empty().or(() -> Optional.of(1)).isPresent());
}
清单 4-16.
Optional.or() 的示例
```

### `stream()` 方法

方法 `Stream<T> stream()` 将 `Optional` 转换为 `Stream`。如果值存在，则结果流仅包含该值，否则结果流为空。当你使用 `flatMap()` 将 `Optional` 的 `Stream` 转换为仅包含存在值的元素的 `Stream` 时，此方法非常有用。

在清单 4-17 中，`Optional` 流包含三个元素，但只有两个包含值。使用 `flatMap()` 后，结果流包含这两个存在的值。

```
@Test
public void testStream() throws Exception {
final long count = Stream.of(
Optional.of(1),
Optional.empty(),
Optional.of(2)
).flatMap(Optional::stream)
.count();
assertEquals(2, count);
}
清单 4-17.
Optional.stream() 的示例
```

方法 `ifPresentOrElse()` 和 `stream()` 也被添加到了 `java.util.OptionalInt`、`java.util.OptionalDouble` 和 `java.util.OptionalLong` 类中。

## 总结

本章涵盖了 Java 9 中与集合、`Stream` 和 `Optional` 相关的变更。用于创建不可变集合的新工厂方法可以在 Java 程序中节省大量代码。添加到 `Arrays`、`Stream` 和 `Optional` 中的新方法也非常有用。在下一章中，我们将讨论新的进程 API。

