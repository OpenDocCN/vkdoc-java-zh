# 6. Optional

首先，我们来看另一个在使用新流时发挥作用的类族：`Optional`。

如你所知，如果在 Java 中声明但未初始化一个对象，其值为 `null`。如果尝试访问值为 `null` 的对象的方法，将会得到空指针异常。遗憾的是，我们无法在任何时候都避免空值。至少我们需要某种东西来表示对象的缺失。为了避免空指针异常，你需要在软件中的许多地方检查各种变量是否为空值（参见清单 6-1）。

```
1   String name;
2   [...]
3   if (name != null) {...}
清单 6-1.
检查非空值
```

Java 8 引入了 `Optional` 类，它封装了一个可选对象，并提供了检查内容的方法（清单 6-2）。

```
1   Optional name;
2   [...]
3   if (name.isPresent()){...}
清单 6-2.
检查是否存在
```

乍一看，这似乎是用一种麻烦代替了另一种麻烦。检查的数量仍然相同，只是从检查空值变成了检查是否存在。而且还需要一个额外的类。那么，额外的好处在哪里呢？

`Optional` 类提供的功能远不止简单的存在性测试。首先，你可以告诉这个类在封装的对象不存在时抛出哪个异常。用你选择的异常（附带详细消息）替换愚蠢的空指针异常，在出错时是一个巨大的优势。另一方面，`Optional` 提供了一些方法来获取值。

要访问封装的对象，可以使用 `get()` 方法。而使用 `orElse()` 方法，你可以指定一个替代值，当值不存在时使用。这可以防止你遇到任何“无值异常”，并允许你以更少的检查来开发软件。

你可以在 [API 文档](https://docs.oracle.com/javase/8/docs/api/java/util/Optional.html) 中找到关于 `Optional` 的更多详细信息。¹

在本章开头，我提到了一个类族。`Optional` 是一个直接派生自 `Object` 的单一类。除此之外，还有 `OptionalDouble`、`OptionalInt`、`OptionalLong` 这些类，它们也直接派生自 `Object`。这些类的行为与 `Optional` 类似。因此，这个类族既不是通过继承自公共父类来定义，也不是通过实现公共接口来定义。这些类只是以类似的方式编程，并实现了相近的行为。

它们的方法略有不同。例如，要获取一个 Optional Double 的值，方法不是 `get()` 而是 `getAsDouble()`——我们将在第 7 章学习 Streams 时用到它。

脚注 1

[`https://docs.oracle.com/javase/8/docs/api/java/util/Optional.html`](https://docs.oracle.com/javase/8/docs/api/java/util/Optional.html)



