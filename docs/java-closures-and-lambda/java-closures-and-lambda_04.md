# 4. 使用 Lambda 进行 I/O 操作

到目前为止，我们对 Lambda 和 Stream 的探索都停留在代码这个可控的世界里。然而，有时你的代码必须与外部世界交互。本章我们将进入现实世界中的 Lambda：不再抽象地展示 Lambda 能做什么，而是深入探讨在真实代码的复杂性中，使用 Lambda 编程是怎样的体验。在这个世界里，你最终不得不处理 Java 中大量遗留代码带来的麻烦，而 Lambda 那些美好的理论和简单的示例，也会让位于一些棘手的复杂性。对你来说，关键在于理解如何应对这种复杂性，从而创造更佳的开发体验，并编写出更高效、更稳定的代码。

第一个案例是与文件交互。通过本章，我们将实现一系列实用的文件工具。我们将编写代码来处理创建和清理临时文件的所有样板代码，让代码使用者能够专注于临时文件的操作。我们还将实现一个方法，用于读取文件的所有行，以及另一个方法，用于读取目录中特定文件的所有行。

我们选择文件作为起点，是因为文件系统是外部世界中极其不稳定的部分。许多经验不足的开发者在编写文件系统代码时，会隐含大量关于代码库之外、类型系统之外世界的未文档化假设。这些假设在开发过程中一直成立，但在生产环境中却会引发问题。困惑的开发者随后会说出那句臭名昭著的话：“在我的机器上能跑啊。”

为了解决这个问题，Java 引入了受检异常（checked exception）的概念。受检异常是一种声明方式，表明该方法依赖于程序自身状态之外的某些现实条件。非受检异常（unchecked exception）则用于程序员搞砸了，传入了不该传的参数，或在错误的时间调用了方法的情况。而受检异常则用于应用程序自身无法控制的事情出错时。`IOException` 类型层次结构描述了应用程序执行 I/O 操作可能失败的各种不同方式。受检异常是一种要求程序员处理外部世界不稳定性的机制。

类型系统表达能力较弱的语言会忽略这种不稳定性，而不明智的开发者则会绕过对这些潜在错误的处理。这会造成代码整洁的假象，但当病毒扫描程序锁定了你的代码正在尝试写入的文件，或者外部世界的其他异常情况偏离了你代码中那些完美的假设时，这种假象就会被无情地粉碎。强制你处理某些异常是一个明智的设计，尽管许多开发者似乎急于否定它。我也会否定它，除非我不断遇到那些因为人们、语言和工具绕过了安全检查，而在 I/O 错误时毫无帮助地崩溃的程序。

受检异常的存在是有原因的：有时你确实需要对错误情况做出响应。不幸的是，它们完全不适合函数式代码。一个数学函数有输入和输出，没有副作用：函数式代码希望尽可能接近数学函数。在 Java 8 中，这意味着 Lambda 不应该抛出异常，并且你不能将抛出受检异常的方法作为函数式接口（`Function`、`Consumer` 或 `Supplier`）的实现。遇到这种情况，许多程序员会简单地将受检异常转换为非受检异常，然后继续。这绕过了系统的安全性。相反，我们希望既保留安全性，又能在函数式世界中良好地运作。

在函数式世界中，处理情况只有两种选择：要么在输入时处理，要么在输出时处理。如果在函数式世界中处理异常情况，这意味着你要么将异常处理器作为参数传递，要么将异常发生的信息作为返回值的一部分传递。以这种方式处理异常可能看起来很奇怪，但这只是学习新范式过程中的体验。这种处理异常的方式可能看起来也很复杂，但现实是，它正在处理 I/O 本身固有的复杂性。工程师和艺术家之间的区别在于，工程师会因为忽视现实世界而受到惩罚。

在本章的剩余部分，我们将从一个简单的任务开始：处理临时文件。我们将看到异常在哪里出现，并尝试使用输入和输出两种风格来处理它们。然后，我们将在此基础上构建更高级的文件系统。那么，让我们开始吧。

## 临时文件与“中间空洞”

在第一章中，我们看到了控制反转是如何以面向对象的方式执行函数式回调的。在函数式世界中，有一种常见的模式：函数在开始时执行一些设置，在结束时执行一些清理，然后回调用户代码来决定中间要做什么。我的朋友、导师兼合著者 Brian Hurt 将这种模式称为“中间空洞”模式。在处理临时文件时，这正是我们想要的：我们希望在开始时创建临时文件，确保在结束时清理临时文件，并允许用户在中间阶段对文件进行他们想要的操作。基本模式如下所示：

`<RETURN_T> RETURN_T withTempFile(Function<FunFile, RETURN_T> function) {`

    `File file = null;`

    `try {`

      `file = File.createTempFile("funfile", "tmp");`

      `return function.apply(file);`

    `} finally {`

      `if (file != null) file.delete();`

    `}`

  `}`

`// 这不是有效的 Java 代码：请继续阅读！`

临时文件的设置是我们的 `File.createTempFile(String,String)` 调用。清理是 `file.delete()` 调用。`function.apply` 调用就是中间的“空洞”，由调用者为我们填充。但是，当然，这不是有效的 Java 代码：存在一个未处理的 `IOException` 需要解决。正如我们之前讨论的，我们有两种选择：通过回调在输入时处理异常，以及通过返回值在输出时处理异常。让我们依次看看这两种方法。



### 通过输入进行异常处理：传入异常处理器

如果你仔细观察上述基本模式，实际上可以将其视为中间有两个"空洞"：一个用于标准行为，另一个用于异常行为。因此，你可以采用两个回调函数：一个实现标准行为，另一个实现异常行为。一个简单的实现如下所示：

`<RETURN_T> RETURN_T withTempFile(`

`Function<FunFile, RETURN_T> function,`

`Consumer<IOException> exceptionHandler`

`) {`

`File file = null;`

`try {`

`file = File.createTempFile("funfile", "tmp");`

`return function.apply(file);`

`} catch(IOException ioe) {`

`exceptionHandler.consume(ioe);`

`} finally {`

`if(file == null) file.delete();`

`}`

`}`

`// 这不是有效的 Java 代码：请继续阅读`

这段代码存在三个问题。第一个问题是它使用了 `null` 作为文件值，而 `null` 并非有效值。第二个问题涉及代码逻辑。最后一个问题是 API 设计问题。编译器会发现第二个问题。第一个问题应该被你新培养的敏感性所察觉——在代码中看到 `null` 就像听到指甲刮黑板的声音一样刺耳。第三个问题最为微妙，只有当你积累了将方法作为 lambda 表达式使用的经验后才会发现。

第一个问题是我们使用了 `null`。从 Java 8 开始，只有在你想触发 `NullPointerException` 时才应使用 `null`。在其他所有需要使用 `null` 的情况下，你应该改用 `Optional` 类。`Optional` 类实现了函数式编程中常见的"选项"或"可能"模式，这也是函数式语言避免出现 `null` 概念或任何与 `NullPointerException` 等价物的方式。`Optional` 类是一个容器，最多容纳一个元素，并提供了便捷的方法来对容器中的元素（如果存在）进行操作。如果我们用 `Optional` 重写代码，就能消除 `NullPointerException` 的可能性，并获得更易读的代码。修改后如下：

`<RETURN_T> RETURN_T withTempFile(`

`Function<FunFile, RETURN_T> function,`

`Consumer<IOException> exceptionHandler`

`) {`

`Optional<File> file = Optional.empty();`

`try {`

`file = Optional.of(File.createTempFile("funfile", "tmp"));`

`return file.map(function).get();`

`} catch(IOException ioe) {`

`exceptionHandler.consume(ioe);`

`} finally {`

`file.ifPresent(File::delete);`

`}`

`}`

`// 这仍然不是有效的 Java 代码：请继续阅读`

然而，这段代码仍然无法编译，这就引出了我们的第二个问题。代码中的第二个问题是，在异常处理的情况下我们没有返回值。如果进入 `catch` 块并执行了消费者，那么方法的返回值是什么？传统的 Java 思维会在此返回 `null`，但 `null` 正是导致令人厌恶的 `NullPointerException` 的根源。我们可以做得更好。与其使用 `Consumer`，不如改用 `Function`，这样用户在任何情况下都能指定返回值。修改后的代码如下：

`<RETURN_T> RETURN_T withTempFile(`

`Function<FunFile, RETURN_T> function,`

`Function<IOException, RETURN_T> exceptionHandler`

`) {`

`File file = null;`

`try {`

`file = File.createTempFile("funfile", "tmp");`

`return function.apply(file);`

`} catch(IOException ioe) {`

`return exceptionHandler.apply(ioe);`

`} finally {`

`if (file != null) file.delete();`

`}`

`}`

`// 这不是你想要的 Java 代码：请继续阅读`

至此，用户可以在两种情况下指定返回值，这意味着他们可以按自己的意愿使用该方法。如果他们意识到 `null` 的危害，可以返回 `null`；如果他们想返回 `Optional.empty()` 或其他默认值，也可以做到。这提供了最大的灵活性。

上述代码只剩下一个问题：参数的顺序是错误的。在传统的 Java 代码中，参数顺序在很大程度上是一个实现细节。但当以函数式风格编写函数时，参数顺序就变得非常重要。考虑一下这个方法的函数式契约：它是一个双函数，接受两个函数作为参数。第一个参数是标准实现回调，第二个参数是错误处理回调。然而，考虑一下它作为 lambda 表达式的工作方式。如果你想进行部分函数应用，你会对第一个参数进行操作，即指定如何处理文件。该实现很可能与调用者的上下文高度相关。另一方面，错误处理在整个应用程序中更可能是通用的。因此，将错误处理作为第一个参数更为有用，这样错误处理可以被部分应用，而临时文件处理则可以自由实现。最终的方法如清单 4-1 所示。

强制调用者处理异常对传统的 Java 程序员来说更直观。然而，这种方法有两个主要问题。第一个问题是调用代码相当丑陋，因为你需要将两个函数式回调叠在一起。对于习惯了惯用 Java 中命令式风格的程序员来说，读起来会感觉很奇怪。第二个也是更重要的问题是，函数式接口是一个双函数，这在许多情况下并不适用。你不能在 `Stream.map` 上使用双函数：你需要的是一个普通函数。为了解决这两个问题，我们可以在返回值中处理异常，这将在下一节中进行。

**清单 4-1. 带异常回调的临时文件创建**

`<RETURN_T> RETURN_T withTempFile(`

`Function<IOException, RETURN_T> exceptionHandler,`

`Function<FunFile, RETURN_T> function`

`) {`

`Objects.requireNonNull(exceptionHandler,`

`"I/O 异常处理器");`

`Objects.requireNonNull(function, "应用于临时文件的函数");`

`Optional<File> file = Optional.empty();`

`try {`

`file = Optional.of(File.createTempFile("funfile", "tmp"));`

`return file.map(function).get();`

`} catch (IOException e) {`

`return exceptionHandler.apply(e);`

`} finally {`

`file.ifPresent(File::delete);`

`}`

`}`



### 通过输出进行异常处理：捕获执行结果

这并非人们通常的思考方式，但你可以将受检异常视为方法 API 的一部分。从后置条件角度来思考：当方法执行完成时，要么方法返回一个值，要么抛出一个异常。作为优秀的面向对象程序员，你可以将这些备选方案封装到一个对象中。其结果是一种更函数式（且对函数友好）的编程风格。

我们首先需要一个类来捕获这些结果。可以使用 `Optional` 类，但它无法传达异常是什么。因此，让我们构建一个类似 `Optional` 的类，但可以选择性地包含我们的异常。我们可以将其命名为 `Result` 类。但在使用它之前，先来理解一下它的用途。清单 4-2 包含了完整的类定义，但别被它吓到：我们将逐段分析，它实际上非常简单。在这个过程中，我们还将看到 `Optional` 类 API 的强大之处。

**清单 4-2. Result 类**

`import java.util.*;`

`import java.util.function.*;`

`/**`

`* 一个表示某些处理结果的类，该结果要么是`

`* 一个异常，要么是给定类型的对象。`

`*/`

`public class Result<RESULT_T> {`

`private final Optional<RESULT_T> result;`

`private final Optional<Exception> exception;`

`/**`

`* 构造一个实例来包装给定的结果。`

`*`

`* @param result 结果；不能为 {@code null}`

`*/`

`public Result(RESULT_T result) {`

`Objects.requireNonNull(result, "要包装的结果");`

`this.result = Optional.of(result);`

`this.exception = Optional.empty();`

`}`

`/**`

`* 构造一个实例来包装给定的异常。`

`*`

`* @param e 异常；不能为 {@code null}。`

`*/`

`public Result(Exception e) {`

`Objects.requireNonNull(e, "要包装的异常");`

`this.result = Optional.empty();`

`this.exception = Optional.of(e);`

`}`

`/**`

`* 将结果作为可选值提供。`

`*`

`* @return 如果存在结果，则返回包含结果的可选值；否则返回`

`* 空的可选值。绝不会为 {@code null}。`

`*/`

`public Optional<RESULT_T> getResult() {`

`return result;`

`}`

`/**`

`* 提供此结果所表示的异常，如果没有此类异常，则返回空的可选值。`

`*`

`* @return 此结果所表示的异常。`

`*/`

`public Optional<Exception> getException() {`

`return exception;`

`}`

`/**`

`* 返回此实例是否是用结果构造的。`

`*`

`* @return 我们是否有结果。`

`*/`

`public boolean hasResult() {`

`return result.isPresent();`

`}`

`/**`

`* 如果存在结果，则获取结果；否则，抛出一个`

`* {@link java.util.NoSuchElementException}，并将异常`

`* 放入其抑制字段中。`

`*`

`* @return 结果（如果存在）。`

`* @throws java.util.NoSuchElementException 如果没有结果。`

`*/`

`public RESULT_T getOrDie() {`

`return result.orElseThrow(() -> {`

`NoSuchElementException ex = new NoSuchElementException(`

`"结果中无元素"`

`);`

`ex.addSuppressed(getException().orElseThrow(() ->`

`new RuntimeException("内部错误"))`

`);`

`return ex;`

`}`

`);`

`}`

`/**`

`* 如果存在结果，则对当前结果执行给定的函数。`

`*`

`* @param function 要应用的函数；绝不能为 {@code null}`

`* @return 如果存在结果，则返回函数结果（包装在 {@link java.util.Optional} 中）；`

`*         如果没有结果，则返回空的可选值。`

`*/`

`public <RETURN_T> Optional<RETURN_T> ifPresent(`

`Function<? super RESULT_T, RETURN_T> function`

`) {`

`Objects.requireNonNull(function, "要应用的函数");`

`return result.map(function);`

`}`

`/**`

`* 如果存在结果，则对当前结果执行给定的消费者。`

`* 否则，不执行任何操作。`

`*`

`* @param consumer 要应用的消费者；绝不能为 {@code null}`

`*/`

`public void ifPresent(Consumer<? super RESULT_T> consumer) {`

`Objects.requireNonNull(consumer, "要应用的消费者");`

`result.ifPresent(consumer);`

`}`

`/**`



`* 如果存在此异常，则对其执行给定的函数。`

`*`

`* @param function 要应用的函数；绝不能为 {@code null}`

`* @return 如果存在异常，则返回包含函数执行结果的 {@link java.util.Optional}；`

`* 如果没有异常，则返回空的 Optional。`

`*/`

`public <RETURN_T> Optional<RETURN_T> ifNotPresent(`

`Function<? super Exception, RETURN_T> function`

`) {`

`Objects.requireNonNull(function, "要应用的函数");`

`return exception.map(function);`

`}`

`/**`

`* 如果存在异常，则对当前异常执行给定的消费者操作。`

`* 否则，不执行任何操作。`

`*`

`* @param consumer 要应用的消费者；绝不能为 {@code null}`

`*/`

`public void ifNotPresent(Consumer<? super Exception> consumer) {`

`Objects.requireNonNull(consumer, "要应用的消费者");`

`exception.ifPresent(consumer);`

`}`

`/**`

`* 如果存在结果，则对结果执行给定的函数。`

`*`

`* @param function 要应用的函数；绝不能为 {@code null}。`

`* @return 处理函数的结果，可能是当前异常、处理过程中的异常，`

`* 或函数返回的对象；绝不会为 {@code null}。`

`*/`

`public <RESULT2_T> Result<RESULT2_T> map(`

`Function<RESULT_T, RESULT2_T> function`

`) {`

`Objects.requireNonNull(function, "映射函数");`

`try {`

`return new Result<>(function.apply(getOrDie()));`

`} catch (Exception e) {`

`return new Result<>(e);`

`}`

`}`

`/**`

`* 如果存在结果，则应用给定的消费者；否则，不执行任何操作。`

`*`

`* @param resultHandler 结果处理器；绝不能为 {@code null}`

`* @return {@code this} 以支持链式调用`

`*/`

`public Result<RESULT_T> whenResult(Consumer<RESULT_T> resultHandler) {`

`Objects.requireNonNull(resultHandler, "结果处理器");`

`result.ifPresent(resultHandler);`

`return this;`

`}`

`/**`

`* 如果存在异常，则应用给定的消费者；否则，不执行任何操作。`

`*`

`* @param exceptionHandler 异常处理器；绝不能为 {@code null}`

`* @return {@code this} 以支持链式调用`

`*/`

`public Result<RESULT_T> whenException(Consumer<Exception> exceptionHandler) {`

`Objects.requireNonNull(exceptionHandler, "异常处理器");`

`exception.ifPresent(exceptionHandler);`

`return this;`

`}`

`/**`

`* 如果存在指定类型的异常，则应用给定的消费者；否则，不执行任何操作。`

`*`

`* @param exceptionHandler 异常处理器；绝不能为 {@code null}`

`* @param exceptionClasses 此处理器要处理的异常类；`

`*                         绝不能为 {@code null}`

`* @return {@code this} 以支持链式调用`

`*/`

`public <EXCEPTION_T extends Exception> Result<RESULT_T> whenException(`

`Consumer<? super EXCEPTION_T> exceptionHandler,`

`Class<? extends EXCEPTION_T>... exceptionClasses`

`) {`

`Objects.requireNonNull(exceptionHandler, "异常处理器");`

`Objects.requireNonNull(exceptionClasses, "要处理的异常类");`

`exception.ifPresent(e -> {`

`Stream<Class<? extends EXCEPTION_T>> classesStream =`

`Stream.of(exceptionClasses);`

`Optional<Class<? extends EXCEPTION_T>> match = classesStream.filter(`

`c -> c.isInstance(e)`

`).findAny();`

`match.ifPresent(exceptionClass -> {`

`EXCEPTION_T ex = exceptionClass.cast(e);`

`exceptionHandler.accept(ex);`

`}`

`);`

`}`

`);`

`return this;`

`}`

`}`

首先要注意的是构造函数和访问器：该类封装了两个 `Optional` 实例：一个表示结果，另一个表示抛出的异常。给定的结果要么是结果，要么是抛出的异常，因此其中一个会是空的 Optional，另一个则会被填充。

`hasResult()` 方法展示了一个常见的模式。它只是委托给 `result.isPresent()` 方法，而该类通常会最终简单地委托给相应的 `Optional` 实例方法。在这种情况下，我们通过检查结果是否存在来判断是否拥有一个结果。



`getOrDie()` 方法为我们提供了一种便捷的方式来获取结果，前提是我们确实知道结果存在。如果调用者判断错误，结果并不存在，该方法将抛出一个 `NoSuchElementException`，并将产生的异常作为抑制异常包含在内。这是通过 `orElseThrown` 方法实现的，该方法要么返回结果，要么抛出由 `Supplier` 提供的异常。我们的 `Supplier` 实现会将异常放入 `NoSuchElementException` 的堆栈跟踪中，这有助于调试。（只有当异常和结果最初都被填充为 `null` 时，才会到达 `“INTERNAL ERROR”` 异常，而这根本不应该发生。）

`map` 函数会转换结果（如果存在）；否则，它将构造一个新的结果并传递异常。这创建了一种在不丢失异常信息的情况下对结果进行操作的方式。在大多数情况下，当你需要使用 `getOrDie()` 时，你实际上想要的是 `map()`。

`ifPresent` 和 `ifNotPresent` 方法提供了一种便捷的方式来消费结果或异常（如果它们存在）。这些方法，连同 `map()`，允许用户指定实现，而无需担心结果的状态，这使得 API 非常易读。请注意，`ifPresent()`/`ifNotPresent()` 与 `map()` 的主要区别在于返回值：`ifPresent()`/`ifNotPresent()` 会丢失信息，但会转换为更常见的 `Optional` 类型；而 `map()` 则保留信息，但保持为当前的 `Result` 类型。

`when` 方法为流畅的 API 提供了支持，允许你编写在读者看来像命令式代码的函数式代码。它们返回当前对象以支持链式调用，因此你可以按顺序调用这些方法，提供一种看起来很像带有异常处理的 try/catch 块的结构。

最后一个 `when` 方法用于捕获多种类型的异常，它是 Java 8 强大功能的一个绝佳展示。得益于类型推断，消费者的参数将自动设置为要捕获的方法的最宽泛的公共超类。传统的实现会涉及 `null`、`if` 块和循环，但这些都被 `Optional` 和 `Stream` 类的方法所取代。

使用这个 `Result` 类，我们可以封装方法调用的结果。我们不返回 `RETURN_T`，而是返回一个 `Result<RETURN_T>`。当方法执行时，它会构造一个 `Result` 实例，该实例要么包含用户函数的返回值，要么包含抛出的 `IOException`。代码的初步版本可能如下所示：

`<RETURN_T> Result<RETURN_T> withTempFile(Function<File, RETURN_T> function) {`

`Objects.requireNonNull(function, "function to apply to temp file");`

`Optional<File> file = Optional.empty();`

`try {`

`file = Optional.of(File.createTempFile("funfile", "tmp"));`

`return new Result<>(file.map(function).get());`

`} catch (IOException e) {`

`return new Result<>(e);`

`} finally {`

`file.ifPresent(File::delete);`

`}`

`} // 这段代码可以工作，但还有改进空间：请继续阅读！`

这段代码与清单 4-1 中的代码惊人地相似。如果你稍微模糊视线，只看空白区域中函数的轮廓，你会发现该方法具有相同的形状。任何时候，当你拥有两个形状相同的方法时，你就错过了代码复用的机会。在这种情况下，除了 `return` 行之外，代码几乎完全相同：在清单 4-1 中，`return` 行委托给了用户提供的代码。在我们的新方法中，我们希望在此处调用 `Result` 类的构造函数。我们可以将 `Result` 类的构造函数作为用户提供的代码传入清单 4-1 的代码中，从而将临时文件的实际实现委托到一个单一位置。由此产生的委托代码如清单 4-3 所示，并且极其简单。这再次证明了新范式在我们后函数式 Java 世界中的强大之处。

清单 4-3\. 创建临时文件，异常封装在返回值中

`/**`
`* 创建一个临时文件，将其传递给给定的函数，并在函数完成时删除该临时文件。`
`*`
`* @param function 使用临时文件实现的行为，`
`*                 可能不返回 {@code null}；不能为 {@code null}`
`* @return 给定函数的结果`
`*/`
`public static <RETURN_T> Result<RETURN_T> withTempFile(`
`Function<File, RETURN_T> function`
`) {`
`Objects.requireNonNull(function, "function to apply to temp file");`
`Function<File, Result<RETURN_T>> resultify = function.andThen(Result::new);`
`return withTempFile(Result::new, resultify);`
`}`



### 使用我们的临时文件函数

在纯函数式世界中，始终存在一个返回值：程序本质上是一个函数，它接收字符串参数和系统环境变量作为输入，并产生一个退出码作为输出。然而，在像 Java 8 这样的后函数式语言中，我们通常希望使用副作用而非返回值。这就是 `Consumer` 类派上用场的原因。不幸的是，我们目前提供的 API 只接受函数，这意味着用户总是需要指定一个返回值。此外，当你引用一个带有一个参数但返回 void 的方法时，你会得到一个 `Consumer` 接口的实例。如果我们希望允许用户传入这种形式的方法，就需要接受 `Consumer` 接口。

这并不太难，但确实需要一些技巧。当然，我们希望基于现有的 API 进行构建，而不是重复实现功能。这意味着我们需要一个桥梁，将用户传入的 `Consumer` 转换为我们的 API 所需的 `Function`。`Consumer` 和 `Function` 之间的区别在于返回值：`Consumer` 返回 void，而 `Function` 返回一个值。对于我们的桥梁，我们将返回一个默认值。我们可以返回 `null`，但 `null` 是通往 `NullPointerException` 的邪恶之路。另一个默认的非值选项是 `Optional.empty()`，因此我们可以使用它。最终的代码如清单 4-4 所示。

**清单 4-4\. 从 Consumer 到 Function 的桥梁**

`/**`

`* 将消费者转换为一个函数，该函数消费参数并返回`

`* {@link java.util.Optional#empty()}.`

`*`

`* @return 一个消费参数并返回空选项的函数。`

`*/`

`static <T> Function<T, Optional<A>> functionise(Consumer<T> consumer) {`

`return a -> {`

`consumer.accept(a);`

`return Optional.empty();`

`};`

`}`

现在我们有了这个方法，就可以创建新的 API 了。简单的情况是要求用户传入 `IOException` 处理器。在这种情况下，我们的 API 与之前清单 4-1 中的版本类似，但我们接受两个 `Consumer` 实例，而不是两个 `Function` 实例。用户负责决定如何处理可能发生的 `IOException`，以及如何处理正常情况。相关代码见清单 4-5。

**清单 4-5\. 接受 Consumer 处理临时文件，并在出错时调用回调的方法**

`/**`

`* 创建一个临时文件，将其传递给给定的消费者，并在函数完成时删除该临时文件。`

`*`

`* @param consumer 要使用临时文件调用的消费者，`

`*        永远不会是 {@code null}；不能为 {@code null}`

`* @param exceptionHandler 当发生 {@code IOException}`

`*        时的处理器；不能为 {@code null}`

`*/`

`public static void withTempFile(`

`Consumer<IOException> exceptionHandler,`

`Consumer<FunFile> consumer`

`) {`

`Objects.requireNonNull(exceptionHandler,`

`"I/O 异常的异常处理器");`

`Objects.requireNonNull(consumer,`

`"应用于临时文件的消费者");`

`Function<IOException, Optional<Object>> exceptionFunction =`

`functionise(exceptionHandler);`

`Function<File, Optional<Object>> consumerFunction = functionise(consumer);`

`withTempFile(exceptionFunction, consumerFunction);`

`}`

有了所有这些函数式的优雅特性，我们最终应该提供一个让 Java 传统主义者感到自然的 API。这意味着一个函数接受一个消费者，并在发生 `IOException` 时抛出异常。在这种情况下，我们将为异常创建一个容器。异常处理器会将异常存储到该容器中。当方法返回时，我们可以检查容器是否已设置，如果已设置则抛出异常。我们需要这个容器，因为在 lambda 中使用的变量隐式地是 `final` 的，这意味着我们无法设置一个在 lambda 外部定义的变量。这个实现实际上会使用 `null`，因为 `Optional` 是不可变的，而其他选项（例如使用 `List`）会显得更加晦涩而非清晰。代码如清单 4-6 所示。

**清单 4-6\. 接受 Consumer 处理临时文件，并在出错时抛出异常的方法**

`/**`

`* 创建一个临时文件，将其传递给给定的消费者，并在函数完成时删除该临时文件。`

`*`

`* @param consumer 要使用临时文件调用的消费者，`

`*                 它永远不会是 {@code null}；不能为 {@code null}.`

`* @throws java.io.IOException 如果发生异常。`

`*/`

`public static void withTempFile(Consumer<File> consumer) throws IOException {`

`AtomicReference<IOException> reference = new AtomicReference<>(null);`

`Consumer<IOException> exceptionHandler = reference::set;`

`withTempFile(exceptionHandler, consumer);`

`if (reference.get() != null) throw reference.get();`

`}`

清单 4-6 也是一个极好的例子，说明了为什么你不应该释放 Zalgo。（请参阅第 2 章中的“不要释放 Zalgo”。）`withTempFile` 方法保证，如果它要执行其参数，那么该参数将在方法返回之前被执行。正因如此，我们知道 `if(reference.get() != null)` 这行代码会在 `exceptionHandler` 实现执行之后执行。如果 `exceptionHandler` 有时会延迟执行，那么我们的代码就会在某些不确定的（无疑是令人惊讶且不幸的）情况下崩溃。



## 读取目录中所有文件的行

在实践了现实世界中的 lambda 技能之后，是时候处理更复杂的问题了。我们的下一个目标是打印出目录中所有文件的所有行。这将让我们有机会分解一个更复杂的问题，并了解如何组合流。完成这个练习后，你应该能很好地理解如何操作流，无论是在处理 I/O 时还是在更通用的场景中。

当使用函数式风格编程时，首先停下来思考你真正想要完成什么是非常有益的。首先，让我们考虑一下我们想要实现的函数的形态。我们知道我们要操作一个 `java.io.File` 实例，因此我们应该要么将该文件作为输入，要么将其作为扩展 `File` 类的某个方法。输出方面则更棘手。我们不知道用户将如何处理文件中的行。我们可以使用一个 `Consumer` 来处理它们，但返回这些行既更简单也更灵活。我们可以将所有行读入某种 `Collection`，但这需要将所有行读入内存，并且在读取完所有行之前无法进行任何处理。因此，最好返回一个 `Stream`，它提供了灵活性和对并发处理的支持。但是，流的元素类型是什么呢？

返回值可能并不像最初看起来那么明显。`String` 实例的 `Stream` 似乎是显而易见的选择，但这只是部分正确。在任何时候，都可能抛出 `IOException`，当处理诸如导航目录之类的事情时，你绝对应该预料到这一点。到目前为止，本章的模式是创建两个方法：一个接受 `Consumer` 来处理错误，另一个返回 `Result` 对象。虽然通过函数式编程可以轻松实现 API 的重复，但这无疑很烦人，并且会使类变得杂乱。让我们尝试一种不同的方法。

处理类的新方法仍然会在 `Stream` 中使用 `Result` 类作为元素，但我们会给用户提供一个新工具。我们将提供一个类，该类通过 `Consumer` 构造，并处理 `Result` 实例的流以移除异常情况。这使用户能够在事后指定如何处理异常，并将 `Result<String>` 的 `Stream` 转换为更熟悉的 `String` 的 `Stream`。所以现在我们有两件事要写：一个方法，它接受一个 `File` 并生成一个 `Result<String>` 的 `Stream`；以及一个后处理器类，它将 `Result<String>` 的 `Stream` 转换为 `String` 的 `Stream`。现在是时候开始考虑实现了，先从后处理器开始。

### 使用创造性展平进行复杂流处理

这就是我们真正要展示 `Stream` 技巧的地方，`Stream` 的真正威力将显现出来。难点在于我们要对流执行一个相当复杂的操作。我们想要处理然后移除流中满足特定条件的元素：在这种情况下，我们想要处理并移除包含异常的 `Result` 实例。

如果你快速浏览一下 `Stream` API，最初看起来我们想要执行一个 `map` 操作：我们想要接收 `Result<String>` 类型并返回一个 `String`，而 `map` 方法正是你在 `Stream` 中转换类型的方式。`map` 方法看起来像这样：

`<R> Stream<R> map(Function<? super T,? extends R> mapper)`

问题在于我们并非是一对一的映射：并非每个输入都会产生一个输出。我们可以返回一个 `Option`，但这能给我们带来什么好处呢？为什么不直接使用我们的 `Result` 类型呢？如果我们执着于 `map` 方法，我们可以创建一个两部分系统，先执行 `map`，然后执行 `filter` 来移除元素。如果我们走那条路，我们就无法利用类型系统来告诉我们 `Result` 已经被处理了，并且我们会在 `Result` 被处理和它被移除之间制造一个错误潜入的可能性。一个更好的替代方案是使用 `flatMap`。

`flatMap` 方法基于“展平”的概念，这是一种函数式结构，它接收一个包含容器的容器，并生成一个包含所有容器中所有元素的单一“扁平”容器。假设你有一个像这样的列表：

`List<List<String>> list = Arrays.asList(`

`Arrays.asList("Eddard Stark", "Catelyn Tully", "Benjen Stark"),`

`Arrays.asList("Lysa Telly", "Jon Arryn", "Robin Arryn"),`

`Arrays.asList("Cersei Lannister", "Robert Baratheon")`

`);`

那么展平后的列表包含所有这些内部列表的所有元素，但没有内部列表结构。它看起来像这样：

`List<String> flatList = Arrays.asList(`

`"Eddard Stark", "Catelyn Tully", "Benjen Stark",`

`"Lysa Telly", "Jon Arryn", "Robin Arryn",`

`"Cersei Lannister", "Robert Baratheon"`

`);`

`flatMap` 方法允许你在 `Stream` 上执行相应的操作。这是它的 API：

`<R> Stream<R> flatMap(Function<? super` `T` `,? extends Stream<? extends R>> mapper)`

你可以传入一个函数，该函数接受流的一个元素并返回一个任意类型的新 `Stream`。所有这些返回的流被连接在一起并处理。这就是我们将如何把我们读取的文件的各个结果连接在一起的方式，这也是该 API 的真正设计目的。然而，当映射操作有可选返回值时，它也可以被使用。为此，当没有结果时，我们将返回 `Stream.empty()`，当有结果时，返回 `Stream.of(element)`。以下是最终的类：

清单 4-7\. 结果映射函数

`import java.util.*;`

`import java.util.function.*;`

`import java.util.stream.*;`

`/**`

`* 一个旨在传递给`

`* {@link Stream#flatMap(java.util.function.Function)} 以处理`

`* {@link Result} 实例的类。该类通过一个异常处理器构造，`

`* 该处理器将接收任何异常，并且 {@link #apply(Result)} 方法`

`* 将返回一个包含结果（如果有）的流。如果 {@link Result} 实例`

`* 没有结果，则流包含 0 个元素；如果实例有结果，则包含 1 个元素。`

`*/`

`public class ResultMap<RESULT_T>`

`implements Function<Result<RESULT_T>, Stream<RESULT_T>>`

`{`

`private final Consumer<Exception> exceptionHandler;`

`/**`

`* 构造一个将委托给给定异常处理器的实例。`

`*`

`* @param exceptionHandler 流中异常的处理程序；绝不能`

`*                         是 {@code null}`

`*/`

`public ResultMap(final Consumer<Exception> exceptionHandler) {`

`Objects.requireNonNull(exceptionHandler, "exception handler");`



`this.exceptionHandler = exceptionHandler;`

`}`

`/**`

`* 将此函数应用于给定的参数。`

`* 如果结果容器包含结果，则返回仅包含该结果的流。`

`* 如果结果容器包含异常，则调用异常处理器并返回空流。`

`* 如果参数为 {@code null}，则返回空流。`

`*`

`* @param resultContainer 结果容器；可能为 {@code null}。`

`* @return 包含结果（如果有）的 0 或 1 个元素的流。`

`*/`

`@Override`

`public Stream<RESULT_T> apply(final Result<RESULT_T> resultContainer) {`

`// 通过短路处理来处理任何棘手的边界情况`

`if (resultContainer == null) return Stream.empty();`

`// 第一个参数是处理异常的方式，`

`// 第二个参数是处理结果的方式`

`return resultContainer.map(`

`error -> {`

`exceptionHandler.accept(error);`

`return Stream.empty();`

`},`

`Stream::of`

`);`

`}`

`}`

函数式编程让这类处理变得如此简洁，真是美妙。额外的好处是，考虑一下对这类类进行单元测试、完整地测试其 API 是多么简单。（如果你想亲自看看，可以在 Webonise Lab 的 FunJava 库中查找这个类。）有了我们的类，让我们通过生成流来创建一个使用它的地方。

### 流式处理目录中所有文件的所有行

有了我们的支持类，我们现在可以转向功能的主要部分。我们希望处理一个 `java.io.File` 并返回一个 `Result` 实例的 `Stream`，每个实例要么包含一个 `IOException`，要么包含文件的一行 `String`。实现的大致分解如下：

`File` `dir` `=> Stream<Result<String>>:`

`对于` `dir` `中的每个文件，执行...`

`File` `file` `=> Stream<Result<String>>:`

`返回` `file` `中的所有行`

这将是一个相当命令式的实现。从函数式的角度来看，我们实际上需要三个方法。你能看出是哪三个吗？关键在于要认识到遍历目录是一个有用的技巧，并且没有理由将这个有用的技巧与到达目录后要做什么的细节耦合在一起。实现这种函数式分解的一个有用方法是，严格遵循一个方法只做一件事的原则，同时要记住，普通 Java 开发者对“一件事”的理解可能过于宽泛：打印目录中所有文件的所有行不是一件事；遍历目录中的文件才是一件事——即使这样也已经是一件相当大的事了。在编写任何代码之前，先在草稿纸上进行设计会很有帮助。如果描述一个函数需要超过一行，那你就做错了。这个问题的一种函数式分解如下：

`给定：`

`[行] File` `file` `=> Stream<Result<String>>`

`返回` `file` `中的所有行`

`[遍历] File` `dir` `=> Stream<File>`

`对于` `dir` `中的每个文件，在流中返回该文件`

`实现：`

`File` `dir` `=> Stream<Result<String>>`

`将` `dir` `应用于` `遍历` `，并将结果通过 flatMap 映射到` `行`

这就是我们现在将要实现的三个函数。由于这三个函数都将 `File` 作为第一个参数，我们将在 `File` 的一个子类上实现它们，这个子类我们称之为 `FunFile`。（如果你想在生产环境中看到它，这个类在 `also` 项目中可用。）

让我们从 `Lines` 方法开始，因为它将是最简单的。Java 7 中引入的 `java.nio.file.Files` 类已经扩展了许多有用的生成 `Stream` 的静态方法，包括 `Files.lines`。不幸的是，尽管名字如此，`Files` 类主要作用于 `Path` 实例。此外，该 API 生成一个 `String` 对象的 `Stream` 并抛出 `IOException`。这个方法签名使得该方法不适合在流中工作，但其核心功能正是我们想要的：我们只需要让 API 符合我们的需求。我们的包装器实现如清单 4-8 所示。请记住，这是附加到一个继承自 `java.io.File` 的类上的。

清单 4-8\. FunFile.getLines( )

`/**`

`* 如果此文件是普通文件，则提供该文件的行（以 UTF-8 读取）`

`* 不带行终止字符，作为流返回。`

`* 否则，返回一个包含 {@link IOException} 的单个 {@link Result} 实例。`

`*`

`* @return 文件的行；永远不会是 {@code null}`

`*/`

`public Stream<Result<String>> getLines() {`

`if (!isFile()) {`

`return Stream.of(new Result<>(new IOException(`

`"文件不是普通文件: " + toString()`

`)));`

`}`

`try {`

`return Files.lines(toPath()).map(Result::new);`

`} catch (IOException ioe) {`

`return Stream.of(new Result<>(ioe));`

`}`

`}`



在结束对 `getLines()` 方法的讨论之前，有必要花点时间探讨一下该方法中的错误条件处理。如果底层文件不是“普通文件”（例如，它是一个目录），我们会在流中返回一个 `IOException`。API 要求文件必须是普通文件，因此文件不是普通文件就违反了 API 的约定。通用规则是：受检异常用于执行环境变化导致的错误，非受检异常用于 API 违规。如果一个真正优秀的程序员不会预料到该异常，那么它就是非受检异常；如果异常的发生超出了程序的控制范围，那么它就是受检异常。在这种情况下，使用非受检异常是合理的，因为这属于 API 违规。然而，文件不是普通文件也可能是因为它已不存在。此外，由于 Java 可能因某些原因（例如 NFS 环境下）无法从文件系统确定文件类型，该方法也可能返回 `false`。因此，我们决定采用标准的受检异常错误处理方法，即使这属于 API 违规。

现在我们有了 `getLines()` 方法，还需要 `Walk` 方法来遍历目录树。这暴露了 API 定义中的一个细微歧义：当我们说“目录中的所有文件”时，是指“目录的直接子文件”，还是指“目录的所有后代文件，无论中间有多少层目录”？换句话说，这是递归列出还是简单列出？我们可以通过增加一个额外参数来轻松支持这两种方式：如果参数为 `true`，我们将递归进入子目录；如果为 `false`，则不递归。

`Walk` 方法的实现非常直接，如代码清单 4-9 所示。我们使用 `Files.list(Path)` 来实现遍历，然后通过 `new FunFile(Path)` 构造函数将结果 `map` 为 `FunFile`。该方法的大部分代码（按行数计）都用于实现递归：除了要明确区分目录和文件外，我们还需要确保不会陷入无限循环，因此必须检查规范路径。

**代码清单 4-9. FunFile.listFiles(boolean) 方法**

`/**`

`* 返回目录中普通文件的流，如果 {@code recurse} 参数为 {@code true}，`

`* 则递归进入子目录。`

`*`

`* @param recurse 是否递归进入子目录`

`* @return 目录中的普通文件；绝不会是 {@code null}`

`*/`

`public Stream<Result<FunFile>> listFiles(boolean recurse) {`

`if (!isDirectory()) {`

`return Stream.of(new Result<>(new IOException(`

`"File is not a directory: " + toString()`

`)));`

`}`

`try {`

`final String thisPath = this.getCanonicalPath();`

`return Files.list(toPath()).map(FunFile::new).flatMap(file -> {`

`if (recurse && file.isDirectory()) {`

`final String filePath;`

`try {`

`filePath = file.getCanonicalPath();`

`} catch (IOException ioe) {`

`return Stream.of(new Result<>(ioe));`

`}`

`if (!thisPath.equals(filePath)) {`

`return file.listFiles(true);`

`} else {`

`return Stream.empty();`

`}`

`} else if (file.isFile()) {`

`return Stream.of(new Result<>(file));`

`} else {`

`return Stream.empty();`

`}`

`}`

`);`

`} catch (IOException ioe) {`

`return Stream.of(new Result<>(ioe));`

`}`

`}`

现在，我们只需将两者结合起来。同样，我们将使用一个 `boolean` 参数来决定是否递归进入子目录。我们将要消费的文件流是一个 `Result` 实例的 `Stream`，因此我们必须决定如何处理错误和结果。错误将被直接传递，包装成一个 `Stream`，然后再扁平化回主流中。而对于流中的 `FunFile` 实例，将调用其 `getLines()` 方法，该方法会返回一个流，然后该流会被扁平化到主流中。最终结果是一个扁平的流，其中包含从所有文件中读取的行（以及错误）。这就是代码清单 4-10。本章的所有工作都是为了构建这个简单的方法。

**代码清单 4-10. FunFile.getLinesInDirectory(boolean) 方法**

`/**`

`* 提供目录中所有文件的所有行。如果此文件不是目录，则返回一个包含单个元素的流，`

`* 该元素是一个包含 {@link IOException} 的 {@link Result} 实例。否则，`

`* 返回尝试使用 {@link #getLines()} 读取目录中所有文件的所有行后拼接而成的流，`

`* 如果 {@code recurse} 参数为 {@code true}，则递归进入子目录。`

`*`

`* @param recurse 是否递归进入子目录`

`* @return 所有文件的行；绝不会是 {@code null}`

`*/`

`public Stream<Result<String>> getLinesInDirectory(boolean recurse) {`

`if (!isDirectory()) {`

`return Stream.of(new Result<>(new IOException(`

`"File is not a directory: " + toString()`

`)));`

`}`

`Function<Exception, Stream<Result<String>>> onError =`

`ex -> Stream.of(new Result<>(ex));`

`Function<FunFile, Stream<Result<String>>> onResult =`

`FunFile::getLines;`

`return listFiles(recurse).flatMap(`

`result -> result.map(onError, onResult)`

`);`

`}`

## 总结

在本章中，我们看到了 lambda 在实际应用中的使用。我们了解了它们如何与受检异常交互，以及如何通过接受回调和返回封装结果来处理这些异常情况。更重要的是，我们看到了如何利用 lambda 工具处理流，从而为一个非平凡问题构思并开发出解决方案。

在下一章中，我们将探讨另一个 lambda 和流可以大大简化工作的难点领域：数据库访问。我们将扩展本章提出的方法，但会更多地关注数据元素本身的处理与转换。



