# 9. 增强的方法句柄

Java 9 对 `java.lang.invoke.MethodHandles` 类进行了增强，增加了更多用于创建不同类型方法句柄的静态方法。

## arrayConstructor

`MethodHandle arrayConstructor(Class<?> arrayClass)` 方法返回一个用于构造指定类型 `arrayClass` 数组的方法句柄。创建的方法句柄的唯一参数是一个指定数组大小的 `int` 值，返回值是数组。在代码清单 9-1 中，我创建了一个用于构造 `int[]` 数组的方法句柄。然后我使用该 `MethodHandle` 创建了一个大小为 `3` 的数组。

```
@Test
public void testArrayConstructor() throws Throwable {
final MethodHandle handle = MethodHandles.arrayConstructor(int[].class);
final int[] array = (int[]) handle.invoke(3);
assertEquals(3, array.length);
}
代码清单 9-1.
arrayConstructor() 示例
```

## arrayLength

`MethodHandle arrayLength(Class<?> arrayClass)` 方法返回一个用于获取类型为 `arrayClass` 的数组长度的方法句柄。此方法句柄的唯一参数是要检查的数组，返回值是一个 `int` 值。在代码清单 9-2 中，我创建了一个用于获取 `int[]` 数组长度的方法句柄。然后我调用它来获取输入数组的长度。

```
@Test
public void testArrayLength() throws Throwable {
final MethodHandle handle = MethodHandles.arrayLength(int[].class);
final int[] array = new int[]{1, 2, 3, 4, 5};
final int length = (int) handle.invoke(array);
assertEquals(5, length);
}
代码清单 9-2.
arrayLength() 示例
```

## varHandleInvoker 和 varHandleExactInvoker

`MethodHandle varHandleInvoker(VarHandle.AccessMode accessMode, MethodType type)` 和 `MethodHandle varHandleExactInvoker(VarHandle.AccessMode accessMode, MethodType type)` 方法返回一个方法句柄，用于在任何访问模式类型与给定类型兼容的 `VarHandle` 上调用签名多态的访问模式方法。这两个方法都有以下两种类型的参数：

*   `VarHandle.AccessMode accessMode`：`VarHandle` 的访问模式
*   `MethodType type`：访问模式方法的类型

`varHandleInvoker()` 和 `varHandleExactInvoker()` 的区别在于，`varHandleInvoker()` 会进行必要的类型转换，而 `varHandleExactInvoker()` 则不会。

在代码清单 9-3 中，变量 `varHandle` 引用了 `HandleTarget` 类中的变量 `count`。我使用访问模式 `VarHandle.AccessMode.GET` 创建了 `MethodHandle`，并调用它以获取变量的值。当为给定的访问模式创建 `MethodType` 时，`VarHandle` 的 `accessModeType()` 方法非常方便。

```
@Test
public void testVarHandleInvoker() throws Throwable {
final VarHandle varHandle = MethodHandles
.lookup()
.findVarHandle(HandleTarget.class, "count", int.class);
final VarHandle.AccessMode accessMode = VarHandle.AccessMode.GET;
final MethodHandle methodHandle = MethodHandles.varHandleInvoker(
accessMode,
varHandle.accessModeType(accessMode)
);
final HandleTarget handleTarget = new HandleTarget();
final int result = (int) methodHandle.invoke(varHandle, handleTarget);
assertEquals(result, 1);
}
代码清单 9-3.
varHandleInvoker() 示例
```

## zero

`MethodHandle zero(Class<?> type)` 方法返回一个始终返回给定类型默认值的方法句柄。代码清单 9-4 展示了如何对不同类型使用 `zero()`。

```
@Test
public void testZero() throws Throwable {
assertEquals(0, MethodHandles.zero(int.class).invoke());
assertEquals(0L, MethodHandles.zero(long.class).invoke());
assertEquals(0F, MethodHandles.zero(float.class).invoke());
assertEquals(0D, MethodHandles.zero(double.class).invoke());
assertEquals(null, MethodHandles.zero(String.class).invoke());
}
代码清单 9-4.
zero() 示例
```



## empty

方法 `MethodHandle empty(MethodType type)` 返回一个方法句柄，该句柄始终根据给定 `MethodType` 的返回类型返回默认值。`zero(type)` 返回的方法句柄实际上等同于 `empty(MethodType.methodType(type))`。清单 9-5 展示了如何对 `int` 和 `String` 类型使用 `empty()`。

```
@Test
public void testEmpty() throws Throwable {
assertEquals(0, MethodHandles.
empty(MethodType.methodType(int.class)).invoke());
assertEquals(null, MethodHandles.
empty(MethodType.methodType(String.class)).invoke());
}
清单 9-5.
empty() 示例
```

## Loops

`MethodHandles` 新增了静态方法，用于创建代表不同类型循环的方法句柄，包括 `for` 循环、`while` 循环和 `do-while` 循环。

### loop

方法 `MethodHandle loop(MethodHandle[]... clauses)` 代表 `for` 循环。使用此方法创建 `for` 循环相当复杂。一个循环由一个或多个子句定义。一个子句最多可以使用方法句柄指定四个动作。

*   `init`：在循环执行前初始化迭代变量。
*   `step`：当子句执行时更新迭代变量。此动作的返回值用作迭代变量的更新值。
*   `pred`：当子句执行时执行一个谓词来测试是否退出循环。
*   `fini`：如果循环退出，则计算循环的返回值。

每个子句可以定义自己的迭代变量。循环的每次迭代按顺序执行每个子句。动作 `step`、`pred` 和 `fini` 接收所有迭代变量作为前置参数。所有这些动作也可以接收额外的循环参数。这些循环参数在调用创建好的 `MethodHandle` 时作为参数传入，并且在所有迭代中所有动作都可以使用。对于动作 `step`、`pred` 和 `fini`，循环参数位于迭代变量之后。`init` 动作只能接受循环参数。

在清单 9-6 中，`init()`、`step()`、`pred()` 和 `fini()` 是子句中用作动作的方法。当我使用 `loop()` 方法时，我只有一个子句，其中包含这些方法的 `MethodHandle`。在 `step` 动作中，变量 `sum` 被更新以加上迭代变量 `i` 的值。`step` 的返回值用作迭代变量 `i` 的更新值。在 `pred` 动作中，变量 `k` 是循环参数，其值为 `11`，该值在调用创建的方法句柄 `loop` 时提供。我检查 `i` 和 `k` 的值以决定是否退出循环。在 `fini` 动作中，`sum` 的值作为调用方法句柄 `loop` 的结果返回。因为每个子句被表示为一个 `MethodHandle` 数组，所以我使用辅助方法 `getMethodHandle()` 来获取当前类中那些静态方法的 `MethodHandle`。

```
public class ForLoopTest {
static int sum = 0;
static int init() {
return 1;
}
static int step(final int i) {
sum += i;
return i + 1;
}
static boolean pred(final int i, final int k) {
return i < k;
}
static int fini(final int i, final int k) {
return sum;
}
@Test
public void testLoop() throws Throwable {
final MethodHandle init = getMethodHandle("init",
MethodType.methodType(int.class));
final MethodHandle step = getMethodHandle("step",
MethodType.methodType(int.class, int.class));
final MethodHandle pred = getMethodHandle("pred",
MethodType.methodType(boolean.class, int.class, int.class));
final MethodHandle fini = getMethodHandle("fini",
MethodType.methodType(int.class, int.class, int.class));
final MethodHandle[] sumClause =
new MethodHandle[]{init, step, pred, fini};
final MethodHandle loop = MethodHandles.loop(sumClause);
assertEquals(55, loop.invoke(11));
}
private MethodHandle getMethodHandle(final String name,
final MethodType methodType)
throws NoSuchMethodException, IllegalAccessException {
return MethodHandles
.lookup()
.findStatic(ForLoopTest.class, name, methodType);
}
}
清单 9-6.
loop() 示例
```

在清单 9-6 中，我使用了一个静态变量 `sum` 来维护内部状态。我不能在这里使用循环参数，因为它们不应该被更新。我能做的是使用来自另一个子句的迭代变量。这样，我可以在一个子句中使用迭代变量来控制循环退出，并使用另一个迭代变量来存储内部状态。

### countedLoop

方法 `MethodHandle countedLoop(MethodHandle iterations, MethodHandle init, MethodHandle body)` 创建一个方法句柄，代表一个运行指定次数的循环。对于像 `for (int i = 0; i < end; ++i)` 这样的简单循环，它比 `loop()` 更容易使用。`countedLoop()` 的参数是不同的 `MethodHandle`。

*   `iterations`：确定迭代次数。此方法句柄的结果类型必须是 `int`。
*   `init`：初始化可选的循环变量。此变量可以在每次迭代中更新。
*   `body`：执行方法体。

由 `countedLoop()` 创建的方法句柄有一个隐式循环变量，该变量从 `0` 开始，每次迭代增加 `1`。方法句柄 `init` 可以创建第二个循环变量，该变量作为第一个参数传递给 `body`，并使用 `body` 的返回值作为更新后的值。隐式循环变量作为第二个参数传递给 `body`。方法句柄 `iterations`、`init` 和 `body` 都接受额外的循环参数，这些参数在参数列表中位于第二个循环变量之后。调用由 `countedLoop()` 创建的方法句柄的返回值是第二个循环变量的最终值。

方法 `MethodHandle countedLoop(MethodHandle start, MethodHandle end, MethodHandle init, MethodHandle body)` 类似于 `countedLoop(MethodHandle iterations, MethodHandle init, MethodHandle body)`，但它允许自定义隐式循环变量的起始值和结束值。方法句柄 `start` 和 `end` 必须返回 `int` 类型的值，它们分别决定循环变量的起始值（包含）和结束值（不包含）。

清单 9-7 展示了使用两种 `countedLoop()` 方法来表示同一个循环。在方法 `body` 中，第一个参数 `sum` 是我创建的用于保存累加值的循环变量。第二个参数 `i` 是隐式循环变量。

```
public class CountedLoopTest {
static int body(final int sum, final int i) {
return sum + i + 1;
}
@Test
public void testCountedLoop() throws Throwable {
final MethodHandle iterations = MethodHandles.constant(int.class, 10);
final MethodHandle init = MethodHandles.zero(int.class);
final MethodHandle body = MethodHandles
.lookup()
.findStatic(CountedLoopTest.class, "body",
MethodType.methodType(int.class, int.class, int.class));
final MethodHandle countedLoop = MethodHandles
.countedLoop(iterations, init, body);
assertEquals(55, countedLoop.invoke());
}
@Test
public void testCountedLoopStartEnd() throws Throwable {
final MethodHandle start = MethodHandles.zero(int.class);
final MethodHandle end = MethodHandles.constant(int.class, 10);
final MethodHandle init = MethodHandles.zero(int.class);
final MethodHandle body = MethodHandles
.lookup()
.findStatic(CountedLoopTest.class, "body",
MethodType.methodType(int.class, int.class, int.class));
final MethodHandle countedLoop = MethodHandles
.countedLoop(start, end, init, body);
assertEquals(55, countedLoop.invoke());
}
}
清单 9-7.
countedLoop() 示例
```



### iteratedLoop

`MethodHandle iteratedLoop(MethodHandle iterator, MethodHandle init, MethodHandle body)` 方法会创建一个方法句柄，该句柄代表一个循环，用于遍历由 `Iterator<T>` 产生的值。方法句柄 `iterator` 必须返回一个 `Iterator<T>` 类型的值。`iteratedLoop()` 的用法与 `countedLoop()` 类似，不同之处在于其隐式循环变量的类型是 `T` 而非 `int`。你也可以在 `iteratedLoop()` 中使用第二个循环变量。在代码清单 9-8 中，我使用 `iteratedLoop()` 来遍历一个来自 `String` 列表的 `Iterator<String>`，以计算这些 `String` 的长度之和。

```
public class IteratedLoopTest {
static int body(final int sum, final String value) {
return sum + value.length();
}
@Test
public void testIteratedLoop() throws Throwable {
final MethodHandle iterator = MethodHandles.constant(
Iterator.class,
List.of("a", "bc", "def").iterator());
final MethodHandle init = MethodHandles.zero(int.class);
final MethodHandle body = MethodHandles
.lookup()
.findStatic(
IteratedLoopTest.class,
"body",
MethodType.methodType(
int.class,
int.class,
String.class));
final MethodHandle iteratedLoop = MethodHandles
.iteratedLoop(iterator, init, body);
assertEquals(6, iteratedLoop.invoke());
}
}
代码清单 9-8.
iteratedLoop() 示例
```

### whileLoop 和 doWhileLoop

`MethodHandle whileLoop(MethodHandle init, MethodHandle pred, MethodHandle body)` 方法会创建一个代表 `while` 循环的方法句柄。一个 `while` 循环由三个方法句柄定义。

*   `init`：初始化一个可选的循环变量。在每次迭代中，循环变量会被传递给 `body`，并根据 `body` 的返回值进行更新。循环执行的结果是循环变量的最终值。
*   `pred`：执行一个谓词来测试是否退出循环。
*   `body`：执行方法体。

`MethodHandle doWhileLoop(MethodHandle init, MethodHandle body, MethodHandle pred)` 方法会创建一个代表 `do-while` 循环的方法句柄。它与 `whileLoop` 具有相同的参数，但顺序不同。在 `doWhileLoop` 中，参数 `body` 位于 `pred` 之前。这是因为 `do-while` 循环在每次迭代中先执行 `body`，再执行 `pred`。这三个参数的含义与 `whileLoop()` 中的相同。

在代码清单 9-9 中，循环变量是一个包含两个元素的 `int[]` 数组；第一个元素是控制循环退出的迭代变量，第二个元素包含总和。`k` 是决定迭代次数的额外参数。方法 `init()` 的返回值是循环变量的初始值。方法 `body()` 的返回值会作为下一次迭代的输入。所创建的 `MethodHandle` 的返回值是循环变量的最终值。

```
public class WhileLoopTest {
static int[] init(final int k) {
return new int[]{1, 0};
}
static boolean pred(final int[] local, final int k) {
return local[0] < k;
}
static int[] body(final int[] local, final int k) {
return new int[]{local[0] + 1, local[1] + local[0]};
}
@Test
public void testWhileLoop() throws Throwable {
final MethodHandle init = getMethodHandle("init",
MethodType.methodType(int[].class, int.class));
final MethodHandle pred = getMethodHandle("pred",
MethodType.methodType(boolean.class, int[].class, int.class));
final MethodHandle body = getMethodHandle("body",
MethodType.methodType(int[].class, int[].class, int.class));
final MethodHandle whileLoop = MethodHandles.whileLoop(init, pred, body);
assertEquals(55, ((int[]) whileLoop.invoke(11))[1]);
}
@Test
public void testDoWhileLoop() throws Throwable {
final MethodHandle init = getMethodHandle("init",
MethodType.methodType(int[].class, int.class));
final MethodHandle pred = getMethodHandle("pred",
MethodType.methodType(boolean.class, int[].class, int.class));
final MethodHandle body = getMethodHandle("body",
MethodType.methodType(int[].class, int[].class, int.class));
final MethodHandle doWhileLoop = MethodHandles
.doWhileLoop(init, body, pred);
assertEquals(55, ((int[]) doWhileLoop.invoke(11))[1]);
}
private MethodHandle getMethodHandle(final String name,
final MethodType methodType)
throws NoSuchMethodException, IllegalAccessException {
return MethodHandles
.lookup()
.findStatic(WhileLoopTest.class, name, methodType);
}
}
代码清单 9-9.
whileLoop() 和 doWhileLoop() 示例
```

注意

在 `countedLoop()`、`iteratedLoop()`、`whileLoop()` 和 `doWhileLoop()` 中的方法句柄 `body` 可以有一个可选的循环变量。如果 `body` 的返回类型是 `void`，那么 `body` 的首个参数将不会是循环变量。在这种情况下，`init` 的返回类型也是 `void`。当没有循环变量时，`body` 的第一个参数将是隐式循环变量。

## Try-finally

`MethodHandle tryFinally(MethodHandle target, MethodHandle cleanup)` 方法会创建一个方法句柄，该句柄将目标方法句柄包装在一个 `try-finally` 块中。参数 `target` 是要包装的 `MethodHandle`，而 `cleanup` 是在 `finally` 块中调用的 `MethodHandle`。执行 `target` 时抛出的任何异常都会被传递给 `cleanup`。除非 `cleanup` 先抛出了异常，否则该异常会被重新抛出。由 `tryFinally()` 创建的方法句柄的返回值是 `cleanup` 的返回值。`cleanup` 接受两个可选的前导参数：

*   一个 `Throwable`，表示调用方法句柄 `target` 时抛出的异常。
*   方法句柄 `target` 的返回值。

除了这两个前导参数外，`target` 和 `cleanup` 应具有相同的对应参数和返回类型。在代码清单 9-10 中，我有两个不同的目标方法句柄：`success` 和 `failure`。包装了方法 `success()` 的方法句柄 `tryFinallySuccess` 返回值 `1`，但包装了方法 `failure()` 的方法句柄 `tryFinallyFailure` 会抛出 `IllegalArgumentException`。方法 `cleanup` 在两次调用中都被调用，并向控制台输出不同的信息。

```
public class TryFinallyTest {
static int success() {
return 1;
}
static int failure() {
throw new IllegalArgumentException("");
}
static int cleanup(final Throwable throwable, final int result) {
if (throwable != null) {
throwable.printStackTrace();
} else {
System.out.println("Success: " + result);
}
return result;
}
@Test(expected = IllegalArgumentException.class)
public void testTryFinally() throws Throwable {
final MethodHandle targetSuccess = getMethodHandle("success",
MethodType.methodType(int.class));
final MethodHandle targetFailure = getMethodHandle("failure",
MethodType.methodType(int.class));
final MethodHandle cleanup = getMethodHandle("cleanup",
MethodType.methodType(int.class, Throwable.class, int.class));
final MethodHandle tryFinallySuccess = MethodHandles
.tryFinally(targetSuccess, cleanup);
assertEquals(1, tryFinallySuccess.invoke());
final MethodHandle tryFinallyFailure = MethodHandles
.tryFinally(targetFailure, cleanup);
tryFinallyFailure.invoke();
}
private MethodHandle getMethodHandle(final String name,
final MethodType methodType)
throws NoSuchMethodException, IllegalAccessException {
return MethodHandles
.lookup()
.findStatic(TryFinallyTest.class, name, methodType);
}
}
代码清单 9-10.
tryFinally 示例
```

## 总结

在本章中，我们讨论了 Java 9 中对方法句柄的增强。现在，你可以为数组构造函数、变量句柄、循环和 try-finally 块创建方法句柄。在下一章中，我们将讨论并发方面的变化。

