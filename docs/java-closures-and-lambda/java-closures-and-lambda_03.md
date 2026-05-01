# 3. Lambda 的领域：集合、映射和流

在上一章中，我们了解了 lambda 本身是如何工作的，但我们明确地专注于 lambda，而没有为它们的使用创建上下文。我们将 lambda 从其自然环境中提取出来，以便在显微镜下分析它们。在本章中，我们将开始看到 lambda 在现实世界中的应用。

具体来说，我们将看到 lambda 如何与熟悉的集合和映射结构一起使用。然而，集合和映射实际上限制了 lambda 的能力。为了发挥其真正的威力，Java 引入了一个使用旧词的新概念：流。本章将以对 Java 8 流的分析作为结尾，并探讨这些函数式流如何实现新的代码复用和表达能力形式。

本章将比上一章更具体，我们将围绕一个特定的例子展开。这个特定的例子将包含一些特定的用例，在这些用例中 lambda 将帮助我们。然而，要理解这一切，我们首先必须了解 Java 8 的 lambda 从何而来。这要求我们快速返回函数式编程语言的奇异世界。



## Lambda 表达式与函数式编程

Java 8 的 lambda 表达式源自函数式编程的世界。函数式编程基于数学函数来构建计算机程序。在函数式编程中，lambda 表达式本身就代表了函数。然而，每个数学函数都作用于一组特定的可能输入，并产生一组特定的可能输出。这些输入被称为“函数的定义域”，输出则被称为“函数的值域”。

从抽象层面来看，lambda 表达式的类型代表了函数的定义域和值域。`Function` 接口的 Java 类型是 `Function<T,U>`：`T` 是其定义域，`U` 是其值域。这就是类型在函数式编程世界中如此有用的原因：类型让你能够清晰地传达函数的定义。对于阅读 API 的开发者来说，类型允许他们简洁地描述你期望接收什么以及你将返回什么。编译器也会读取这些类型，并利用这些类型对你的代码进行推理，从而提供其他方式无法实现的优化。

然而，抽象只能带你走到一定程度。迟早，你必须针对真实数据执行你的代码。你可能会构建出有史以来最优雅的函数应用链，但在将它们应用到某些东西上之前，它们不会为你完成任何任务。在函数式编程方法中，你的应用程序目标很简单：收集所有你关心的领域对象，并将它们输入到函数中。那么，是什么东西提供了这些供函数操作的领域对象呢？

如果你与普通的 Java 开发者谈论收集对象，他会想到 `Collection` 类及其相关 API。从历史上看，这就是函数式 Java 代码的编写方式：你获取一个集合，对每个元素应用一个函数，然后返回结果集合。以这种方式作用于集合的函数被称为“推导式”。Java 8 使得推导式成为可能，我们将在本章中介绍支持推导式的 API。这种方法对于已知项目的相对较小的集合非常有效，并且对于 Java 开发者来说是一个非常自然的演进。

然而，也存在一些严重的局限性。最明显的局限性是 `Collection` API 假定所有数据都在内存中，或者至少是随手可得的。如果你正在处理数据库中的所有行或文件中的所有单词，情况可能并非如此。一个更微妙的后果是，将一系列这样的操作链接在一起会创建一系列中间集合，每个集合的大小大致相同，这纯粹是一种浪费。最后，大多数 `Collection` API 不是线程安全的，或者只有有限的线程安全特性，这限制了 lambda 表达式的并发能力。

为了解决这个问题，Java 8 引入了“函数式流”的概念。“流”这个术语在 Java 中已有既定用法，而函数式流是这个旧概念的新应用。传统上，“流”指的是 Java 的 I/O 世界，其中 I/O 流代表一个字节序列。大多数 Java 开发者习惯于使用这些流，并且知道如何通过装饰器模式对它们的字节进行包装、扭曲和操作，同时始终保持在 `InputStream` API 的范围内。这是一种实现显著代码复用的强大方式。

函数式流类似于这些 I/O 流，但它们作用于对象序列而不是字节序列。对象在流经流时可以被包装、扭曲和操作，我们可以获得同样的复用性。得益于 lambda 表达式的引入，我们不再需要装饰器模式：我们可以简单地将我们的实现传递给 `Stream` 对象本身，并得到一个新的 `Stream` 作为返回。



函数式流比集合方法更有效，因为你不需要预先在集合中准备好所有数据，而且 Java 运行时可以在可能时自动利用并发执行的机会。由此产生的 API 非常强大，尽管对于 Java 开发者来说可能有些令人生畏：它来自一个不同的世界，因此一开始可能不太直观。本章的其余部分将致力于让流变得易于理解；当我们完成时，你应该能像使用 `Collection` 和 `Iterator` 类一样直观地使用它们。

为此，让我们从已经熟悉的 `Collection` 接口开始。与上一章抽象地讨论不同，这里我们设定一个更具体的例子。假设我们有一个 `Library` 类。该类的实例以任意但有意义的顺序存储 `Book` 对象；类的使用者设定书籍的顺序，我们不想打乱它。基于这个需求，我们在每个 `Library` 实例中存储一个 `List<Book>` 实例。此外，`Library` 必须提供“特色书籍”的功能。有些书籍会被特别标记为特色，并可选地附带一条特定的特色信息。为了提供这个功能，我们在每个 `Library` 实例中存储一个 `Map<Book,String>` 实例。

`Library` 和 `Book` 的起始代码如清单 3-1 所示。在本章的其余部分，我们将使用 Java 8 提供的新函数式工具为我们的 `Library` 类构建功能。

**清单 3-1. Library 和 Book 类**

`import java.util.Objects;`

`/**`

`* 表示 {@link Library} 中元素的类。`

`*/`

`public class Book {`

`/**`

`* 表示书籍允许的体裁。`

`*/`

`public static enum Genre {`

`HORROR, COMEDY, TECHNICAL;`

`}`

`private final String title;`

`private final String author;`

`private final Genre genre;`

`/**`

`* 构造函数。`

`*`

`* @param title  书籍的标题；不能为 {@code null}。`

`* @param author 书籍的作者；不能为 {@code null}。`

`* @param genre  书籍的体裁；不能为 {@code null}。`

`*/`

`public Book(final String title, final String author, final Genre genre) {`

`Objects.requireNonNull(title, "title of the book");`

`this.title = title;`

`Objects.requireNonNull(author, "author of the book");`

`this.author = author;`

`Objects.requireNonNull(genre, "genre of the book");`

`this.genre = genre;`

`}`

`@Override`

`public String toString() {`

`return "\"" + title + "\" by " + author +`

`" (" + genre.toString().toLowerCase() + ")";`

`}`

`@Override`

`public int hashCode() {`

`int result = title.hashCode();`

`result = 17 * result + author.hashCode();`

`result = 31 * result + genre.hashCode();`

`return result;`

`}`

`@Override`

`public boolean equals(final Object o) {`

`if (o == null) return false;`

`if (this == o) return true;`

`if (!(o instanceof Book)) return false;`

`final Book book = (Book) o;`

`if (!author.equals(book.author)) return false;`

`if (genre != book.genre) return false;`

`if (!title.equals(book.title)) return false;`

`return true;`

`}`

`}`

`import java.util.List;`

`import java.util.Map;`

`import java.util.Objects;`

`import java.util.ArrayList;`

`import java.util.HashMap;`

`/**`

`* 表示 {@link Book} 对象集合的类。{@code Book}`

`* 对象以用户定义的顺序存储，默认为插入顺序。`

`* 用户还可以将某个 {@code Book} 指定为 <i>特色</i>，`

`* 并可选择附带一条 <i>特色信息</i>。`

`*/`

`public class Library {`

`private final List<Book> books = new ArrayList<>();`

`private final Map<Book, String> featuredBooks = new HashMap<>();`

`/**`

`* 将一本书添加到书籍列表的末尾。`

`*`

`* @param book 要添加的书籍；不能为 {@code null}。`

`*/`

`public void addBook(Book book) {`

`Objects.requireNonNull(book, "book to add");`

`this.books.add(book);`

`}`

`/**`

`* 将一本书添加到书籍列表的末尾，并将其作为特色书籍添加，附带给定的信息。`

`*`

`* @param book    要添加的书籍；不能为 {@code null}。`

`* @param message 特色信息，可以为 {@code null}。`

`*/`

`public void addFeaturedBook(Book book, String message) {`

`this.addBook(book);`

`this.featuredBooks.put(book, message);`

`}`

`/**`

`* 提供对书籍列表的直接访问。修改返回的集合将修改图书馆中的书籍。`

`*`

`* @return 此图书馆中的书籍；绝不会为 {@code null}。`

`*/`

`public List<Book> getBooks() {`

`return this.books;`

`}`

`/**`

`* 提供对特色书籍列表的直接访问。修改返回的映射将修改特色书籍及其信息。`

`*`

`* @return 此图书馆中的特色书籍映射到它们的（可能为 {@code null}）信息；绝不会为 {@code null}。`

`*/`

`public Map<Book, String> getFeaturedBooks() {`

`return this.featuredBooks;`

`}`

`}`



## 函数式迭代

后函数式语言社区引以为傲的标志之一，就是没人再使用 `for` 循环了。摒弃 `for` 关键字已成为代码的最低可接受标准，并以正义的热忱加以执行。取而代之的是，这些语言提供了迭代器方法。Java 8 通过 `forEach` 和 `forEachRemaining` 方法，加入了这些后函数式语言的行列。

`Iterable` 接口现在提供了一个 `forEach` 方法。有些 Java 开发者仍然不知道 `Iterable` 接口，它是在 Java 5 中引入的。它是所有能够提供 `Iterator` 的 Java SDK 类的基类，也是支持 Java 5 中引入的增强型 `for` 循环的类。由于新的 `forEach` 方法位于这个广泛实现的接口上，因此所有实现该接口的类现在也都拥有了 `forEach` 方法。

从语义上讲，`forEach` 方法与 `for` 循环是相同的：它接受一个 `Consumer`，并针对循环中的每个元素执行它。然而，在实践中，它是一个“由内向外”的 `for` 循环。`forEach` 方法不是创建一个遍历集合并为每次迭代执行一系列步骤的结构，而是将一系列步骤提供给集合，然后集合按顺序将其应用于每个元素。这使得集合实现能够以最高效的方式执行迭代。更棒的是，代码更加简洁易读，如清单 3-2 所示。

**清单 3-2. 使用增强型 for 循环和 forEach 打印每本书**

`for (Book book : library.getBooks()) {`

`System.out.println(book);`

`}`

`library.getBooks().forEach(System.out::println);`

不过，如果你有一些遗留代码，其中提供了一个 `Iterator`，你仍然可以使用函数式替代方案来替代循环。在这种情况下，方法是 `forEachRemaining`，它将 lambda 应用于迭代器中所有剩余的元素，并在过程中耗尽迭代器。

对于 `Map.forEach` 来说，情况略有不同。从历史上看，Java 希望你将其视为提供对 `Map.Entry` 对象集合的访问。随着 Java 8 lambda 的出现，它们正在打破这种模式：`Map.forEach` 方法不接受 `Map.Entry` 对象的 `Consumer`，而是接受键值对的 `BiConsumer`。一方面，这使得内联实现看起来相当不错，例如清单 3-3 所示：不再需要调用 `entry.getKey()` 和 `entry.getValue()`。另一方面，`BiConsumer` 是一种特殊的函数式类型，很少有 lambda 操作或现有的方法定义能很好地与之配合。

**清单 3-3. 使用 Map 版本的 forEach 打印每本精选书籍**

`library.getFeaturedBooks().forEach(`

`(book, msg) ->  System.out.println(book + ": " + msg)`

`);`

了解了这些方法后，你现在可以编写再也不使用 `for` 循环的 Java 代码，使你的代码符合后函数式程序员的标准。这种对迭代器方法的热情中具有讽刺意味的是，在函数式编程中你几乎从不使用迭代器方法。原因在于返回值：它在 Java 中是 `void`，在几乎所有语言中同样毫无用处。这意味着它是函数构建的一个硬性障碍：对于函数式程序员来说，从迭代器方法出发无处可去。因此，它的实用性完全依赖于副作用，这与整个函数式编程范式背道而驰。例如，在清单 3-3 中，函数式程序员会将 `Book` 映射为 `String`，然后将其管道传输到 `String` 连接中，再将结果管道传输到 `System.out.println`。不幸的是，`Consumer` 类型阻止了这种链式操作，因为它依赖于 `Consumer` 实现的副作用来完成任何有意义的事情。

这是后函数式语言和函数式语言分歧最大的地方之一。后函数式语言接受副作用作为实现程序员目标的合理方式，而函数式语言则惩罚试图使用副作用进行编码的程序员。虽然后函数式语言为程序员提供了显著更大的灵活性，但它们也使得阅读代码、推理代码或提供其行为保证（例如线程安全）变得更加困难。这是典型的“能力越大，责任越大”的情况。

对于 Java 8 开发者来说，要吸取的教训是，使用 `forEach`/`forEachRemaining` 方法是可以的。然而，每当你使用这些方法时，请仔细考虑你是否真的想要使用其他某种推导方式。为任务选择正确的方法将提高代码的可读性，赋予运行时和库开发者适当优化它的能力，并锻炼你以更强大的函数式方式进行编程的习惯。

同样值得注意的是，如果你想操作集合，例如添加或删除成员，这不是正确的方法。尝试在 `forEach` 方法内部修改集合可能会导致 `ConcurrentModificationException` 异常。由于 `forEach` 的约定只是说可能发生异常，因此可以安全地假设该异常会等到最糟糕的情况下才发生，然后才让你的代码崩溃。如果你想通过传入 lambda 来操作集合或映射，则必须使用专门的方法。

## 使用 Lambda 操作集合和映射

在数学中，不存在可变性：真永远为真，假永远为假。函数式编程遵循数学的指引，并假定域不能被改变。当你将集合或映射用作 lambda 的域时，结果将是一个新的集合、映射或对象。在本节中，我们将探讨可用的特定类型的操作。



### 过滤集合与映射

在处理集合时，最常见的操作就是移除某些元素。在其他后函数式语言（以及 Java 的 `Stream` API）中，这被称为“`filter`”。当你想要通过直接移除元素来修改集合时，应使用 `Collection.removeIf` 方法。`removeIf` 方法接受一个 `Predicate`，并会从集合中移除所有使该 `Predicate` 测试结果为 `true` 的元素。Java 没有为 `Map` 提供直接等价的方法，但你可以对 `Map.entrySet()`、`Map.values()` 和 `Map.keySet()` 返回的集合使用 `removeIf`，从而修改底层的 `Map`。如果集合发生了更改，该方法返回 `true`；如果集合未发生更改，则返回 `false`。

在清单 3-4 中，我们使用 `removeIf` 功能来扩展我们的 `Library` 类，提供了移除所有指定类型图书的能力。由于我们希望对某些处理的结果进行测试，因此在此定义了一个实用方法：`mapThenTest`。这为我们提供了一个谓词，该谓词执行某些映射并测试映射的结果：在我们的例子中，我们希望获取类型，然后对该类型进行测试。定义好该谓词后，我们将其传递给 `books` 集合和 `featured books` 集合的 `removeIf` 方法。

在清单 3-4 中，有几点需要注意。请注意，使用函数式接口使我们能够确保对 `books` 列表和 `featured books` 集合使用相同的代码：对谓词的任何更改都将可靠地应用于两种情况。还要注意，`removeIf` 调用完全移除了两处的迭代代码，使我们的代码更加简洁且更具描述性。由于 `featured books` 集合是 `books` 列表的严格子集，我们可以利用第一次调用的返回值作为优化手段来短路移除操作：如果没有找到匹配类型的图书，则无需检查 `featured books` 列表。在此使用函数式代码为我们节省了大量样板代码和冗余信息，而变量名则成为了描述函数功能的标签。当行为作为变量存在时，编写自文档化的代码会容易得多。

**清单 3-4. 使用 removeIf 移除类型的 Library 实例方法**

`/**`

`* 将元素映射为一个值，然后测试该映射后的值。`

`*`

`* @param map 定义映射的函数；不能为 {@code null}`

`* @param test 定义测试的函数；不能为 {@code null}`

`* @return 一个谓词，用于测试其参数映射后的结果。`

`*/`

`private static <T,U> Predicate<T> mapThenTest(`

  `Function<T,U> map, Predicate<U> test`

`) {`

  `Objects.requireNonNull(map, "映射实现");`

  `Objects.requireNonNull(test, "测试实现");`

  `return t -> test.test(map.apply(t));`

`}`

`/**`

`* 从图书馆馆藏和精选馆藏中移除所有指定类型的图书。`

`*`

`* @param genre 要比较的类型；不能为 {@code null}。`

`*/`

`public void removeGenre(Book.Genre genre) {`

  `Objects.requireNonNull(genre, "要比较的类型");`

  `Predicate<Book> hasGenre = mapThenTest(Book::getGenre, genre::equals);`

  `if (this.getBooks().removeIf(hasGenre)) {`

    `this.getFeaturedBooks().keySet().removeIf(hasGenre);`

  `}`

`}`

### 映射集合与映射

如果你已经接受了更函数式的开发风格，那么你可能会大量使用不可变对象。在本章中，我们对 `Book` 类采用了这种方法。然而，当我们想要操作这些对象时，这会导致一个问题：例如，假设我们想要在精选图书的标题后添加一个星号。由于 `Book` 实例是不可变的，我们需要用新实例替换旧实例。为此，我们可以使用 `List.replaceAll` 方法。该方法属于 `List` 而非 `Collection`，因为在某些类型的集合（例如 `SortedSet`）中替换元素会为其 API 带来尴尬的情况。（如果你转换了 `SortedSet` 中已排序的元素，那么结果应该应用哪种排序规则？）

如果我们想要在精选图书的标题后添加一个星号，我们首先需要定义“在标题后添加一个星号”的含义。鉴于 `Book` 类可用的 API，我们必须定义一个内联 lambda 来实现这一点。（这表明我们的 `Book` API 可能缺少某些有用的功能，但这是另一个话题了。）从函数式角度思考，我们希望在满足特定条件时应用一个转换。这需要另一个实用函数：`applyIf`。它将接受一个谓词和一个函数，并且仅当谓词为 `true` 时才应用该函数。由于我们只处理单一数据类型，我们可以使用 `UnaryOperator` 简写来节省一些输入和类型变量。有了这个实用方法，我们需要获取一个 `Predicate` 来判断图书是否为精选，以及执行转换的函数。最终的 `UnaryOperator` 完全符合我们的需求：如果 `Predicate` 测试返回 `true`，我们将执行“在标题后添加一个星号”的函数；如果测试返回 `false`，我们将返回传入的 `Book` 实例。这种方法的代码如清单 3-5 所示。

**清单 3-5. 使用 List.replaceAll 为精选图书加星标的 Library 实例方法**

`/**`

`* 仅当 {@code test} 对参数返回 {@code true} 时，才对该参数应用映射操作。`

`*`

`* @param test 决定是否应用映射的测试；不能为 {@code null}`

`* @param ifTrue 测试为 true 时应用的映射；不能为 {@code null}`

`* @return 当 {@code test} 为 true 时应用 {@code ifTrue}`

`*         否则返回参数本身的运算符。`

`*/`

`private static <T> UnaryOperator<T> applyIf(`

`Predicate<T> test, UnaryOperator<T> ifTrue`

`) {`

`Objects.requireNonNull(test, "用于测试的谓词");`

`Objects.requireNonNull(ifTrue, "条件为真时应用的方法");`

`return t -> {`

`if(test.test(t)) {`

`return ifTrue.apply(t);`

`} else {`

`return t;`

`}`

`};`

`}`

`/**`

`* 更新图书馆，将每本精选图书替换为标题后加 {@code *} 的图书。`

`*/`

`public void starFeatureBooks() {`

`UnaryOperator<Book> starBook = book ->`

`new Book(book.getTitle() + "*", book.getAuthor(), book.getGenre());`

`Predicate<Book> isFeatured = getFeaturedBooks().keySet()::contains;`

`UnaryOperator<Book> starIfFeatured = applyIf(isFeatured, starBook);`

`getBooks().replaceAll(starIfFeatured);`

`}`

还有一种方法可以对 `Map` 实例执行此类操作，尽管你只能更改值：由于与 `Set` 难以实现 `replaceAll` 相同的原因，无法通过这些操作调用更改键。但该方法仍然非常有用：例如，假设我们想对精选图书的消息执行一个清理方法：我们希望通过提供默认消息并移除末尾句点来清理消息。我们可以通过执行 `replaceAll` 方法并返回更新后的值来实现这一点，如清单 3-6 所示。此方法体的复杂性不太适合采用函数式解决方案，因此我们直接使用了内联函数定义。



清单 3-6\. 使用 `Map.replaceAll` 清理精选图书消息的库实例方法

`/**`
`* 对所有精选消息执行各种清理任务。`
`*/`
`public void cleanUpFeaturedMessages() {`
`this.getFeaturedBooks().replaceAll((book, msg) -> {`
`// 设置默认消息`
`if (msg == null || msg.isEmpty()) {`
`msg = "Featured " + book.getGenre().toString().toLowerCase() +`
`" book by " + book.getAuthor();`
`}`
`// 移除末尾句点，除非是省略号`
`if (msg.endsWith(".") && !msg.endsWith("...")) {`
`msg = msg.substring(0, msg.length() - 1);`
`}`
`return msg;`
`}`
`);`
`}`

### Map 计算

`Map` 类本身已通过一些函数式风格的操作进行了扩展，使您能够处理特定的键。这些方法并非直接受函数式编程技术启发，而是作为常见代码（如下所示）的简写形式：

`// 默认值`
`String value = map.get(key);`
`if(value == null) {`
`value = computeValue(key);`
`map.put(key, value);`
`}`
`// 递增值`
`Integer value = map.get(key);`
`if(value != null) {`
`value += 1;`
`map.put(key, value);`
`}`

这段代码实际上用了四行来完成一个逻辑：获取键对应的值，如果缺失则设置该值。除了冗长之外，这段代码也不清晰；在 `get` 之后，`value` 是否仍处于生成过程中并不明确。更甚的是，这段代码效率低下：它先执行 get 再执行 put，需要对键进行重复查找。

相反，`Map` 类现在为您提供了一种方式，可以在单个方法调用中访问映射并实现这种特殊处理，这确保了返回的值在您接收时已完全配置好，并为您节省了重复查找的开销。这是一个完美的例子，展示了 lambda 如何让开发者编写出更高效、更具表现力且更安全的代码——所有这些优点集于一身。

我遇到的最常见用例是默认值。例如，让我们创建一个方法，该方法将获取精选图书消息，如果未设置则提供默认值。我们将沿用清单 3-7 中创建的相同默认消息，但在键查找时提供默认值。我们将使用 `computeIfAbsent` 方法来实现这一点，这是计算默认值的简便方法。请注意，`computeIfAbsent` 方法也是实现多值映射或规范化映射的便捷方式，这些只是默认值的特殊情况。

`map.computeIfAbsent(key, k -> new HashMap<>()); // 多值映射`
`map.computeIfAbsent(key, Function.identity());  // 规范化映射`

清单 3-7\. 使用 `Map.computeIfAbsent` 提供精选图书消息的库实例方法

`/**`
`* 为给定的图书提供精选图书消息。如果该图书之前不是精选图书，`
`* 则在此方法解析后将成为精选图书。如果它之前没有精选图书消息，`
`* 则在此方法解析后将拥有一个。`
`*`
`* @param featuredBook 需要其消息的图书；绝不能为 {@code null}`
`* @return 精选图书的消息；绝不能为 {@code null}`
`*/`
`public String getFeaturedBookMessage(Book featuredBook) {`
`Objects.requireNonNull(featuredBook, "featured book");`
`return this.getFeaturedBooks().computeIfAbsent(featuredBook, book ->`
`"Featured " + book.getGenre().toString().toLowerCase() + " book by "`
`+ book.getAuthor()`
`);`
`}`

另一个与映射相关的常见任务是跟踪实例计数。例如，考虑我们想要为某些用户指定的类型生成类型计数。为此，我们可以指定 `computeIfPresent`。此方法是 `computeIfAbsent` 的补充：它仅在值已存在时才执行其 lambda。此功能对于更新某些现有值而不在映射中创建额外值非常有用。为了提供这些类型计数，我们首先将所需键初始化为零，然后遍历所有图书，如果计数已存在则递增。我们在清单 3-8 中执行此操作。`computeIfPresent` 方法对于在方法的值上提供任何类型的初始化或包装代码，同时绕过 `null` 检查也很有用。

清单 3-8\. 使用 `Map.computeIfAbsent` 提供精选图书消息的库实例方法

`public Map<Book.Genre, Integer> getGenreCounts(Book.Genre... genres) {`
`Map<Book.Genre, Integer> toReturn = new HashMap<>();`
`Arrays.asList(genres).forEach(genre -> toReturn.put(genre, 0));`
`getBooks().forEach(book ->`
`toReturn.computeIfPresent(book.getGenre(), (key, count) -> count + 1 )`
`);`
`return toReturn;`
`}`

也可以使用单个操作方法来处理缺失值和现有值。此方法简称为 `compute`，lambda 会应用于该键的当前值：如果在调用该方法之前该键不在映射中，则 lambda 会接收到 null 作为值。如果传入的 lambda 不返回 `null`，那么 `compute` 方法实际上等同于以下代码：

`map.put(key, f(key, map.get(key));`

显然，这给代码增加了一些复杂性，因为您必须同时处理键已定义和未定义的情况。然而，作为处理这种复杂性的交换，您获得了最大的灵活性。如果我们想扩展清单 3-6 中的代码，以提供我们在清单 3-5 中实现的所有默认消息功能，那么我们可以使用 `compute` 方法，它将如清单 3-9 所示。

清单 3-9\. 使用 `Map.compute` 提供精选图书消息的库实例方法

`public String getFeaturedBookMessage(final Book featuredBook) {`
`return getFeaturedBooks().compute(featuredBook, (book, msg) -> {`
`// 设置默认消息`
`if (msg == null || msg.isEmpty()) {`
`msg = "Featured " + book.getGenre().toString().toLowerCase()`
`+ " book by " + book.getAuthor();`
`}`
`// 移除末尾句点，除非是错误的省略号`
`if (msg.endsWith(".") && !msg.endsWith("...")) {`
`msg = msg.substring(0, msg.length() - 1);`
`}`
`return msg;`
`}`
`);`
`}`

`Map` 中最后一个操作 API 是 `merge`。此方法的实现乍看之下有些奇怪：它接受一个键、一个值和一个“重映射函数”。如果该键尚未分配，则使用该值；如果该键已分配，则将参数值和映射中的当前值传递给重映射函数，并将结果分配给该键。完成所有这些后，该方法返回映射到给定键的新值。尽管看起来可能很神秘，但这种奇怪的舞蹈正是您在映射中跟踪计数时所需要的：您传入键，将计数作为值传入，然后将加法作为 lambda 传入。它也适用于跟踪位掩码、连接列表以及任何其他基于键合并值的情况。在清单 3-10 中，我们基于此方法提供了一个通用的类型计数方法。

清单 3-10\. 使用 `Map.merge` 计数的库实例方法

`public Map<Book.Genre, Integer> getGenreCounts() {`
`Map<Book.Genre, Integer> counts = new HashMap<>();`
`getBooks().forEach(book ->`
`counts.merge(book.getGenre(), 1, (i, j) -> i + j)`
`);`
`return counts;`
`}`

在 `Map.compute` 和 `Map.merge` 之间做选择时，重要的问题是您的新值关心的是键还是旧值。如果新值基于键，请使用 `Map.compute`（或其变体之一）；如果新值关心旧值，请使用 `Map.merge`。不过，如果您忘记了区别，类型签名可以帮助您。



## 流

到目前为止，我们所讨论的一切都围绕着原地编辑现有的 `Collection` 或 `Map`。然而，在本书的剩余部分，我们将聚焦于 lambda 的真正力量：函数式流（以下简称“流”）。支持流的类主要收集在全新的 `java.util.stream` 包中，但 Java SDK API 的其他部分也散布着一些流 API 的精华。接下来的几章都将围绕这些 API（或它们不幸的缺失）将如何改变你使用 Java 8 编写代码的方式。不过，在此之前，我们需要先理解什么是流以及它是如何工作的。

流类似于迭代器，但具有明显更广泛的能力。对于迭代器，一直存在一个假设：你只会遍历它一次，并且总是在同一个线程上，而且总是有一个底层的集合被遍历。曾有过一些尝试，试图基于集合之外的东西（例如文件的行）构建迭代器，甚至还有一些尝试让迭代器表现得像惰性列表或供应者。将这种期望的功能塞进 `Iterator` API 中，总感觉像是试图把方钉塞进圆孔里。更重要的是，你能用迭代器做的事情总是相当有限。例如，与 I/O 流不同，从未发展出广泛采用的、用于包装迭代器或操作其返回值的支持。迭代器就是你用来以非常命令式的风格简单遍历元素的东西，仅此而已。

然而，对于流来说，情况完全不同。流可以显式地并行化，这使得用户代码和库本身能够从多个线程读取流并执行它们。流被设计为可以通过 `Function` lambda 进行转换，通过 `Predicate` lambda 进行过滤，并且通常以我们喜爱的函数式风格进行操作。更棒的是，它们可以通过多种方式收集在一起，从而能够接入任何遗留 API，或高效地实现复杂处理。

流的生命周期比迭代器的生命周期稍微复杂一些，因为涉及的事情要多得多。流的生命周期是：

创建 → 映射和过滤（称为中间步骤） → 收集、处理或归约（称为终端步骤）

这三个步骤中的每一个都可以采用多种形式，你可以像搭积木一样混合搭配它们，以构建几乎任何你能想象到的流程。这提供了极大的灵活性，并且由于类型系统的存在，相对容易管理。更棒的是，步骤 2 和步骤 3 的并行性由 Java 自动为你处理，因此 Java 运行时和库本身会自动优化你的实现。

### 流的创建

第一步，“创建”，是你生成流的地方。生成时，流尚未对底层数据做任何操作，就像 I/O 流在你创建它时尚未读取任何字节一样。流仅仅知道如何开始生成数据，并且它提供了一个丰富的 API 和大量的元数据用于生成这些数据。

你可以使用 `stream()` 或 `parallelStream()` 方法从 `Collection` 生成流。`stream()` 方法生成一个顺序（非并行）流，当你需要按迭代顺序处理 `Collection` 的元素时，这很有用。但是，如果你没有任何执行顺序的要求，那么始终最好使用 `parallelStream()` 请求一个流。该方法允许生成的流以任何顺序处理元素，包括并发处理它们。

值得注意的是，如果 `Collection` 不支持并行性，你仍然可能从 `parallelStream()` 得到一个顺序流：调用 `parallelStream` 并不是要求一个可并行的流，而是允许返回的流是可并行的。这些方法提供了从 `Collection` API 到 `Stream` API 的桥梁，因此很容易迁移你的代码。¹ 类似地，如果你想从数组生成流，则使用 `Stream.of(array)` 方法。

在最底层，可以使用 `Stream` 接口上的静态方法来生成流。特别是，你可以使用 `generate` 方法基于 `Supplier` 函数式接口生成一个 `Stream`，并且你可以使用 `iterate` 方法基于某个初始值和一个递增 lambda 创建一个迭代的流（例如，传入 `0` 和 `x->x+1` 作为 lambda，创建一个从零开始计数的流）。最后，`Stream` 类通过 `builder()` 方法提供了一个构建器类，因此你可以将元素传入构建器，然后基于结果生成流。

当我们进入流生成时，我们就进入了这些被称为“分割迭代器”的奇怪生物的巢穴。分割迭代器可以被视为流的幼虫形态：它们通过 `StreamSupport.stream` 方法进入茧中，然后作为完全成形的 `Stream` 实例出现。它们本身足够复杂，值得进行更详细的讨论，不过，我们将在第 9 章讨论 lambda 和流实现细节时，详细探讨分割迭代器。在大多数情况下，用户可以并且应该坚持使用 `Stream` 接口本身：依赖库支持来隐藏分割迭代器的实现细节。

你在这里和 Java SDK 中看到的流并非唯一可能。Webonise Lab 的 `FunJava` 库提供了其他生成流的方式：具体来说，该库提供了基于数据库 `ResultSet`、基于文件内容、基于目录结构以及基于 `InputStream` 和 `Reader` 实例的流。我们将在关于使用流处理文件和数据库调用的章节中详细介绍这些流生成器，并且你可以将这些实现用作你自己流实现的模型。然而，为了聚焦重点，本章我们将坚持使用 Java 本身提供的那些更简单的流。



### 映射与过滤流

创建流之后，你能用它做什么呢？对流操作可分为两类：中间操作和终端操作。中间步骤可以链式组合，而终端步骤则会终止流。流的处理直到遇到终端步骤才会启动，因此在流启动任何重量级活动之前，中间步骤可以链式组合并进行操作。中间步骤有两种基本类型：映射步骤和过滤步骤。

这里的“映射”一词取其函数式含义：接收输入并产生输出。在这种情况下，映射操作会获取流中的每个元素，对该流应用一个函数（“映射值”），并产生输出。这允许你获取一种类型的流，并生成一个新类型的流：例如，你可以获取一个 ID 流，并通过数据库调用生成实例化对象的流。回到我们的`Library`示例，我们可以生成图书馆书籍的流，并轻松地将其映射到流派流上。具体代码如下：

`Stream<Book.Genre> genres =`

`library.getBooks().parallelStream().map(Book::getGenre);`

`map`函数假定每个输入元素都会产生一个输出元素。如果每个输入元素可能产生零个或多个输出元素，则应使用`flatMap`函数。`flatMap`函数的工作方式与`map`函数相同，区别在于它产生的不是单个元素，而是一个元素`Stream`。例如，假设你有一个文件名流，并且想要将所有文件的内容连接起来。首先，你可以将字符串映射为`Path`实例，然后使用`Files.lines`方法，该方法接收一个`Path`实例并生成其内容的流。现在你有了一个从流到流之流的映射：这时`flatMap`会为你将其展平。实现此功能的代码如清单 3-11 所示。由于`Files.lines`方法会抛出受检异常，这段代码稍微复杂了一些，但我们将在第 7 章中讨论如何优化这段代码。

清单 3-11. 通过 Stream.flatMap 从给定文件名列表创建包含所有文件行的流

`Function<Path, Stream<String>> readLines = path -> {`

`try {`

`return Files.lines(path);`

`} catch (IOException ioe) {`

`throw new RuntimeException("Error reading " + path, ioe);`

`}`

`};`

`Stream<String> lines = Stream.of("foo.txt", "bar.txt", "baz.txt")`

`.map(Paths::get)`

`.flatMap(readLines);`

另一种常见的中间步骤是过滤步骤。过滤确保流中的所有元素都满足某个测试条件，这允许你筛除不需要的元素。要进行过滤，你需要将一个`Predicate`传递给`Stream.filter`方法。流中的每个元素都会经过测试。如果测试返回`true`，流会生成该元素；如果测试返回`false`，流会静默丢弃该元素。对于我们的`Library`类，我们可以通过传递`Genre.TECHNICAL::equals`作为过滤谓词，仅获取技术类书籍的流。代码如下：

`Stream<Book> techBooks =`

`library.getBooks().parallelStream().filter(Book.Genre.TECHNICAL::equals);`

请注意，过滤不会改变流的元素类型：我们不对元素本身进行任何转换，只是决定保留还是丢弃每个流元素。这非常有用，因为当你在处理过程中拥有合适的类型时，可以在中间添加过滤器来执行测试。例如，我们可以扩展清单 3-11 的示例，通过传递一个过滤器，仅读取已存在文件的内容；只有`Files.exists`返回`true`的路径才会被保留。我们在清单 3-12 中实现了这一点。在这里，我们首先创建`Path`实例，然后将这些临时实例传递给`Files.exists`，再将满足条件的`Path`实例传递给我们的 readLines lambda，以生成`String`实例。我们的输入是`String`实例，输出也是`String`实例，但我们是在仅存在于流内部的`Path`实例上执行处理逻辑。

清单 3-12. 通过 Stream.filter 从给定文件名列表创建仅包含已存在文件所有行的流

`Function<Path, Stream<String>> readLines = path -> {`

`try {`

`return Files.lines(path);`

`} catch (IOException ioe) {`

`throw new RuntimeException("Error reading " + path, ioe);`

`}`

`};`

`Stream<String> lines = Stream.of("foo.txt", "bar.txt", "baz.txt")`

`.map(Paths::get)`

`.filter(Files::exists)`

`.flatMap(readLines);`



### 收集、处理或归约流

我们刚刚讨论的映射和过滤操作是中间操作；你可以随心所欲地将它们链式组合。不过，迟早你会想要停止对流进行加工，并真正对其执行一些操作。这时终端操作就派上用场了。每个流只能有一个终端操作，并且流实际上是在应用终端操作时执行的。终止流有三种基本方式：通过收集其元素、基于其元素产生副作用，或对其元素执行计算。这三种方式分别被称为收集、处理和归约。

要收集流，你需要指定你想要的结果是什么类型的集合，以及你希望如何收集元素。Java SDK 在 `Collectors` 类中提供了许多选项。该类生成各种类型的 `Collector` 实例。一个 `Collector` 实例负责消费流中的元素，基于这些元素更新某些内部状态，并在最后生成一个结果。内置的收集器确实令人印象深刻：你可以获取平均值、统计元素数量、基于某个 `Comparator` 获取最小值或最大值、基于某个键对元素进行分组（返回一个 `List` 实例的 `Map`）、将所有结果的字符串表示连接成一个单独的 `String`（可选地带有分隔符），等等。在清单 3-13 中，我们可以从 `Library` 实例的书籍出发，得到一个按类型对这些书籍进行排序的 `Map`。请注意，我们使用了 `groupingByConcurrent` 方法，以保持并行执行的权限。这正是将 Stream API 代码与更传统的 Java 代码进行比较时，能够真正展现函数式编程威力的时刻之一。

**清单 3-13. 使用流按类型对书籍进行分组**

`Map<Book.Genre, List<Book>> booksByGenre =`

`library.getBooks().parallelStream()`

`.collect(Collectors.groupingByConcurrent(Book::getGenre));`

有时你并不想收集结果，而只是想产生一些副作用。经典的例子是打印流执行的结果。为此，你可以使用 `Stream.forEach` 方法，就像在 `Collection` 上使用一样。一个主要区别是 `Stream.forEach` 方法不保证执行顺序：并行流可能会以任意顺序生成结果。如果你确实想确保顺序，你需要使用 `Stream.forEachOrdered` 方法。该方法提供与 forEach 相同的功能，但附加了一个约定：元素按照流生成它们的顺序（称为“遭遇顺序”）进行处理。通常，如果你打算使用 `Stream.forEach`（尤其是 `Stream.forEachOrdered`），你应该深吸一口气，重新考虑是否有更好的方式来处理这种情况，例如自定义 `Collector`。通过副作用进行编程几乎从来都不是正确的答案，并且始终是长期维护的障碍。

最后一个常见的终端操作称为归约，它最初是来自函数式编程的一种略显陌生的操作。² 其思想是，你传入某个初始值，然后一个函数接收一个元素并返回一个与初始值类型相同的元素。该函数的结果会被传递给下一个元素。这些执行结果像菊花链一样串联起来，直到整个流被消费完毕，然后返回最终结果。这种链式执行非常强大，一旦你掌握了窍门，就能用非常简单的代码实现非常复杂的操作。例如，如果你想自己实现字符串连接，你可以这样实现归约：

`String booksString =`

`library.getBooks().parallelStream()`

`.map(Book::toString)`

`.map(str -> str + "\n")`

`.reduce("", String::concat);`

从概念上讲，这将生成一个书籍流，将每本书转换为其字符串表示，为每个字符串表示追加一个回车符，然后归约生成的流。归约步骤将从空字符串开始，并连接结果。第一次连接会将空字符串与第一本书连接；下一次连接将获取第一次的结果并与第二本书连接；再下一次连接将获取第二次的结果并与第三本书连接；如此继续，直到没有更多书籍，最后返回结果。

这里需要给智者一句忠告。归约操作是一个非常强大的操作，它几乎肯定会成为你某个时候的“金锤”³；随着你对它越来越熟练，你会开始倾向于使用 `reduce` 来终止所有流。这并不是一个坏冲动，因为它表明你在以函数式的方式思考。然而，如果你开始想要向归约操作传递比简单的前一个结果更多的状态，那么你真正需要的是一个自定义的 `Collector` 实例或一个包装后的 `Collector` 实例（参见 `Collectors.collectingAndThen`）。请记住这一点，你将能够保持代码的可读性和函数式风格，同时也能生成更可重用的代码。

### 原始类型流

在结束对流的介绍之前，了解存在针对 `int`、`long` 和 `double` 原始类型的特定流是很重要的。你可以通过使用 `Stream` 类的方法来生成这些流：`mapToInt`/`flatMapToInt`、`mapToLong`/`flatMapToLong`、`mapToDouble`/`flatMapToDouble`。正如你所料，与传递对象包装器相比，它们提供了一些性能优势，尽管对于应用程序代码来说，这种优势通常不足以产生显著影响。更重要的是，它们提供了与许多现有方法形状相匹配的 API。例如，`IntStream` 有一个名为 `mapToObj` 的方法，它接受一个将 `int` 映射到 `Object` 的函数。该函数的形状完美匹配 `List.get(int)` 方法，因此你可以调用 `intStream.map(List::get)`。

## Lambda 领域回顾

Java 8 提供了一种新的抽象：你现在可以抽象出处理某个领域的概念。你从一个流开始，无论该值是来自映射、集合还是其他地方，最终的处理代码都是完全相同的。你现在可以摆脱遍历数据源的逻辑，专注于你想对其提供的元素做什么。正如我们在本章中所看到的，流就是这种抽象。此外，还有一些方便的简写方法。这些简写方法对于将遗留代码转换为更函数式的风格特别有用。在下一章中，我们将研究如何处理应用程序外部的数据源：与外部世界交互会使这些函数式结构变得更加复杂。这种复杂性实际上将展示函数式编程在处理棘手代码方面的威力，并让你能够专注于核心关注点。

脚注 ¹

关于如何从 `Stream API` 桥接回 `Collection API` 的信息，请参见下面的收集/归约步骤描述以及 `Collectors` 类。

  ²

在与函数式程序员交谈时，他们也可能将归约操作称为注入或折叠。

  ³

术语“金锤”源自一句流行的俏皮话：“如果你只有一把锤子，那么所有东西看起来都像钉子。”如果你只有一把锤子，并且对它感到非常兴奋，那么你拥有的就是一把金锤。



