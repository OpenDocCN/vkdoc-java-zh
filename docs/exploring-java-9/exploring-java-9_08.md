# 8. 变量句柄

变量句柄是对变量或变量族的引用，包括静态字段、非静态字段、数组元素或堆外数据结构的组件。变量句柄的概念与方法句柄类似。变量句柄使用 `java.lang.invoke.VarHandle` 类表示。通过变量句柄，你可以对变量执行不同的操作，这些操作称为访问模式。所有支持的访问模式都在枚举 `VarHandle.AccessMode` 中定义。

## 创建变量句柄

`VarHandle` 对象可以使用 `java.lang.invoke.MethodHandles.Lookup` 类中的工厂方法创建。首先，你可以使用 `MethodHandles.lookup()` 方法获取 `MethodHandles.Lookup` 的实例，然后使用其方法创建 `VarHandle` 对象。

### findStaticVarHandle

方法 `VarHandle findStaticVarHandle(Class<?> decl, String name, Class<?> type)` 返回一个 `VarHandle`，用于访问在类型 `decl` 中声明的、给定类型的静态字段 `name`。在清单 8-1 中，我创建了一个 `VarHandle`，用于访问在类 `HandleTarget` 中声明的静态 `int` 字段 `staticVar`。

```
MethodHandles.lookup()
.findStaticVarHandle(HandleTarget.class, "staticVar", int.class);
清单 8-1. findStaticVarHandle() 示例
```



### findVarHandle

方法 `VarHandle findVarHandle(Class<?> recv, String name, Class<?> type)` 返回一个 `VarHandle`，用于访问类型为 `recv` 中指定类型的非静态字段 `name`。在代码清单 8-2 中，我创建了一个 `VarHandle`，用于访问在 `HandleTarget` 类中声明的非静态 `int` 字段 `count`。

```
MethodHandles.lookup()
.findVarHandle(HandleTarget.class, "count", int.class);
代码清单 8-2.
findVarHandle() 示例
```

### unreflectVarHandle

方法 `VarHandle unreflectVarHandle(Field f)` 通过 `java.lang.reflect.Field` 对象创建一个 `VarHandle`。在代码清单 8-3 中，我使用通过 `HandleTarget.class` 的 `getDeclaredField()` 方法创建的 `Field` 对象创建了一个 `VarHandle`。

```
MethodHandles
.lookup()
.unreflectVarHandle(HandleTarget.class.getDeclaredField("count"));
代码清单 8-3.
unreflectVarHandle() 示例
```

## 访问模式

要理解不同的变量访问模式，首先需要了解内存排序。

### 内存排序

变量句柄的访问模式与 C/C++11 的原子操作（[`http://en.cppreference.com/w/cpp/atomic`](http://en.cppreference.com/w/cpp/atomic)）兼容。表 8-1 展示了访问模式支持的不同内存排序。

表 8-1.

内存排序

| 内存排序 | 访问类型 | 描述 |
| --- | --- | --- |
| plain | 读/写 | 与非 `volatile` 具有相同的内存语义，对其他线程没有特殊的内存排序效果。仅对引用类型和最多 32 位的原始值具有原子性。 |
| volatile | 读/写 | 与 Java `volatile` 具有相同的内存语义。 |
| opaque | 读/写 | 不保证对其他线程的内存排序效果。仅对同一变量的访问具有原子性。 |
| acquire | 读 | 确保后续的加载和存储操作不会重排到本次访问之前；与 C/C++11 `memory_order_acquire` 排序兼容。 |
| release | 写 | 确保先前的加载和存储操作不会重排到本次访问之后；与 C/C++11 `memory_order_release` 排序兼容。 |

注意

有关 C/C++11 `acquire` 和 `release` 内存排序的更多详细信息，请参见 [`http://en.cppreference.com/w/cpp/atomic/memory_order#Release-Acquire_ordering`](http://en.cppreference.com/w/cpp/atomic/memory_order#Release-Acquire_ordering) 。

这些内存排序用作访问模式的后缀，以指定这些模式的内存排序效果。例如，`GET_AND_ADD_ACQUIRE` 对读访问具有 `acquire` 内存排序。

### VarHandle 方法

在枚举 `VarHandle.AccessMode` 中定义了三十一种访问模式。对于每种访问模式，`VarHandle` 中都有一个对应的方法，你需要使用该访问模式来调用。例如，对于访问模式 `GET_AND_ADD`，`VarHandle` 中的对应方法是 `getAndAdd()`。

#### 签名多态性

`VarHandle` 中用于访问模式的方法具有签名多态性。尽管所有这些方法都具有相同的签名 `Object methodName(Object... args)`，但会在运行时进行检查，以确保参数的运行时类型符合访问模式的要求。如果参数匹配失败，JVM 会抛出 `java.lang.invoke.WrongMethodTypeException`。由于编译器不会静态检查参数的数量和类型，因此开发人员有责任确保在调用这些方法时传递了正确数量和正确类型的参数。

参数列表由零到多个坐标类型对象以及不同访问模式所需的可选参数组成：

*   这些坐标类型用于定位你想要访问的变量。例如，当访问数组中的元素时，坐标类型是数组的类型和数组索引的类型。你可以使用 `VarHandle` 的 `List<Class<?>> coordinateTypes()` 方法来获取坐标类型列表。
*   不同的访问模式可能需要额外的参数。例如，方法 `compareAndSet()` 需要用于比较的期望值和用于设置的新值。

这些访问模式可以分为五类：读访问、写访问、原子更新、数值原子更新和按位原子更新。代码清单 8-4 中的 `HandleTarget` 类包含用于测试的不同静态变量。我将在后续章节中使用这些变量。

```
public class HandleTarget {
public int count = 1;
public String[] names = new String[]{"Alex", "Bob", "David"};
public byte[] data = new byte[]{1, 0, 0, 0, 1, 0, 0, 0};
public ByteBuffer dataBuffer = ByteBuffer.wrap(this.data);
}
代码清单 8-4.
用于测试的 HandleTarget
```

现在让我们讨论所有五类方法。

#### 读访问

此类包括 `get()`、`getVolatile()`、`getOpaque()` 和 `getAcquire()` 方法，其内存排序效果由后缀指定。方法 `get()` 具有普通内存排序效果（见表 8-1）。代码清单 8-5 展示了不同读访问方法的用法。变量句柄 `varHandle` 引用了代码清单 8-4 中 `HandleTarget` 类的变量 `count`。由于测试用例不是多线程的，所有这些读访问模式都返回相同的结果。

```
public class VarHandleTest {
private HandleTarget handleTarget = new HandleTarget();
private VarHandle varHandle;
@Before
public void setUp() throws Exception {
this.handleTarget = new HandleTarget();
this.varHandle = MethodHandles
.lookup()
.findVarHandle(HandleTarget.class, "count", int.class);
}
@Test
public void testGet() throws Exception {
assertEquals(1, this.varHandle.get(this.handleTarget));
assertEquals(1, this.varHandle.getVolatile(this.handleTarget));
assertEquals(1, this.varHandle.getOpaque(this.handleTarget));
assertEquals(1, this.varHandle.getAcquire(this.handleTarget));
}
}
代码清单 8-5.
读访问
```

所有这些读访问方法只需要一个参数，即包含此变量的目标对象。类 `HandleTarget` 是唯一的坐标类型。

#### 写访问

此类包括 `set()`、`setVolatile()`、`setOpaque()` 和 `setRelease()` 方法，其内存排序效果由后缀指定。方法 `set()` 具有普通内存排序效果（见表 8-1）。代码清单 8-6 展示了不同写访问方法的用法。

```
@Test
public void testSet() throws Exception {
final int newValue = 2;
this.varHandle.set(this.handleTarget, newValue);
assertEquals(newValue, this.varHandle.get(this.handleTarget));
this.varHandle.setVolatile(this.handleTarget, newValue + 1);
assertEquals(newValue + 1, this.varHandle.get(this.handleTarget));
this.varHandle.setOpaque(this.handleTarget, newValue + 2);
assertEquals(newValue + 2, this.varHandle.get(this.handleTarget));
this.varHandle.setRelease(this.handleTarget, newValue + 3);
assertEquals(newValue + 3, this.varHandle.get(this.handleTarget));
}
代码清单 8-6.
写访问
```

所有这些写访问方法都接受两个参数。第一个参数是目标对象，第二个是要设置的新值。



#### 原子更新

此类别包含来自四个子类别的方法，其后缀指定了内存排序效果。所有方法都对变量执行读取和写入访问，其读取和写入访问可能具有不同的内存排序效果。

`compareAndSet()` 方法在变量的当前值等于预期值时，以原子方式设置变量的值，其读取和写入访问均采用 volatile 内存排序。返回的 `boolean` 值指示操作是否成功更新了值。

`weakCompareAndSet()`、`weakCompareAndSetPlain()`、`weakCompareAndSetAcquire()` 和 `weakCompareAndSetRelease()` 方法可以在变量的当前值等于预期值时，以原子方式设置变量的值。返回的布尔值指示操作是否成功更新了值。即使变量的当前值与预期值匹配，此操作也可能失败。这就是它们被称为弱操作的原因。

`compareAndExchange()`、`compareAndExchangeAcquire()` 和 `compareAndExchangeRelease()` 方法在变量的当前值等于预期值时，以原子方式设置变量的值。返回值是当前值，如果操作成功，该值将与预期值相同。

`getAndSet()`、`getAndSetAcquire()` 和 `getAndSetRelease()` 方法以原子方式将变量的值设置为新值，并返回变量先前的值。

表 8-2 显示了每种方法的读取和写入访问的内存排序。

表 8-2.

原子更新方法的读取和写入访问内存排序

| 方法 | 读取 | 写入 |
| --- | --- | --- |
| `compareAndSet()` | volatile | volatile |
| `weakCompareAndSet()` | volatile | volatile |
| `weakCompareAndSetPlain()` | plain | plain |
| `weakCompareAndSetAcquire()` | acquire | plain |
| `weakCompareAndSetRelease()` | plain | release |
| `compareAndExchange()` | volatile | volatile |
| `compareAndExchangeAcquire()` | acquire | plain |
| `compareAndExchangeRelease()` | plain | release |
| `getAndSet()` | volatile | volatile |
| `getAndSetAcquire()` | acquire | plain |
| `getAndSetRelease()` | plain | release |

清单 8-7 展示了如何使用不同的原子更新方法。

```
@Test
public void testAtomicUpdate() throws Exception {
final int expectedValue = 1;
final int newValue = 2;
assertEquals(true,
this.varHandle.compareAndSet(this.handleTarget, expectedValue, newValue));
assertEquals(newValue,
this.varHandle.compareAndExchange(this.handleTarget, newValue, newValue + 1));
assertEquals(newValue + 1, this.varHandle.getAndSet(this.handleTarget, newValue + 2));
}
清单 8-7.
原子更新方法
```

#### 数值原子更新

`getAndAdd()`、`getAndAddAcquire()` 和 `getAndAddRelease()` 方法以原子方式将指定值加到变量的当前值上，并返回变量先前的值。表 8-3 显示了这些方法的读取和写入访问的内存排序。

表 8-3.

数值原子更新方法的读取和写入访问内存排序

| 方法 | 读取 | 写入 |
| --- | --- | --- |
| `getAndAdd()` | volatile | volatile |
| `getAndAddAcquire()` | acquire | plain |
| `getAndAddRelease()` | plain | release |

清单 8-8 展示了如何使用不同的数值原子更新方法。

```
@Test
public void testNumericAtomicUpdate() throws Exception {
final int expectedValue = 1;
assertEquals(expectedValue,
this.varHandle.getAndAdd(this.handleTarget, 1));
assertEquals(expectedValue + 1,
this.varHandle.getAndAddAcquire(this.handleTarget, 1));
assertEquals(expectedValue + 2,
this.varHandle.getAndAddRelease(this.handleTarget, 1));
}
清单 8-8.
数值原子更新方法
```

#### 按位原子更新

`getAndBitwiseAnd()`、`getAndBitwiseAndAcquire()`、`getAndBitwiseAndRelease()`、`getAndBitwiseOr()`、`getAndBitwiseOrAcquire()`、`getAndBitwiseOrRelease()`、`getAndBitwiseXor()`、`getAndBitwiseXorAcquire()` 和 `getAndBitwiseXorRelease()` 方法以原子方式将变量的值设置为变量当前值与掩码值进行按位 `AND`/`OR`/`XOR` 运算的结果，并返回变量先前的值。表 8-4 显示了这些方法的读取和写入访问的内存排序。

表 8-4.

按位原子更新方法的读取和写入访问内存排序

| 方法 | 读取 | 写入 |
| --- | --- | --- |
| `getAndBitwiseAnd()` | volatile | volatile |
| `getAndBitwiseOr()` | volatile | volatile |
| `getAndBitwiseXor()` | volatile | volatile |
| `getAndBitwiseAndAcquire()` | acquire | plain |
| `getAndBitwiseOrAcquire()` | acquire | plain |
| `getAndBitwiseXorAcquire()` | acquire | plain |
| `getAndBitwiseAndRelease()` | plain | release |
| `getAndBitwiseOrRelease()` | plain | release |
| `getAndBitwiseXorRelease()` | plain | release |

清单 8-9 展示了如何使用不同的按位数值原子更新方法。

```
@Test
public void testBitwiseAtomicUpdate() throws Exception {
final int mask = 1;
assertEquals(1, this.varHandle.getAndBitwiseAnd(this.handleTarget, mask));
assertEquals(1, this.varHandle.get(this.handleTarget));
assertEquals(1, this.varHandle.getAndBitwiseOr(this.handleTarget, mask));
assertEquals(1, this.varHandle.get(this.handleTarget));
assertEquals(1, this.varHandle.getAndBitwiseXor(this.handleTarget, mask));
assertEquals(0, this.varHandle.get(this.handleTarget));
}
清单 8-9.
按位原子更新方法
```

### 数组

`VarHandle` 也可用于访问数组中的单个元素。你可以使用 `MethodHandles.arrayElementVarHandle(Class<?> arrayClass)` 方法创建一个访问数组元素的 `VarHandle`。

在清单 8-10 中，我创建了一个访问 `String[]` 数组的 `VarHandle`。`compareAndSet()` 方法将数组中的第一个元素更新为新值。调用此方法时，你需要提供数组、索引、预期值和新值。

```
@Test
public void testArray() throws Exception {
final VarHandle arrayElementHandle = MethodHandles
.arrayElementVarHandle(String[].class);
assertEquals(true,
arrayElementHandle.compareAndSet(
this.handleTarget.names, 0, "Alex", "Alex_new"));
assertEquals("Alex_new", this.handleTarget.names[0]);
}
清单 8-10.
用于访问数组元素的 VarHandle
```



### byte[] 和 ByteBuffer 视图

`VarHandle` 允许你将字节数组或 `ByteBuffer` 视为不同原始类型的数组，例如 `int[]` 或 `long[]`。你可以使用 `MethodHandles.byteArrayViewVarHandle()` 或 `MethodHandles.byteBufferViewVarHandle()` 方法分别创建 `byte[]` 或 `ByteBuffer` 的视图。

为了能够使用 `byteArrayViewVarHandle()` 方法创建 `VarHandle`，第一个参数必须是你用来查看字节数组的数组类。元素类型可以是 `short`、`char`、`int`、`long`、`float` 和 `double`。第二个参数是字节顺序，类型为 `java.nio.ByteOrder`，可以是 `BIG_ENDIAN` 或 `LITTLE_ENDIAN`。在代码清单 8-11 中，我通过将字节数组视为 `int[]` 并将字节顺序设置为 `BIG_ENDIAN` 来创建一个 `VarHandle`。然后我使用 `get()` 方法获取原始字节数组中从指定索引开始的 `int` 值。索引的最大值是字节数组的大小减去元素类型的字节大小。对于代码清单 8-11 中的 `VarHandle`，`int` 的字节大小为 `4`，因此索引的最大值为 `8 – 4 = 4`。使用大于 `4` 的索引值将导致 `IndexOutOfBoundsException`。

```
@Test
public void testByteArrayView() throws Exception {
final VarHandle varHandle = MethodHandles
.byteArrayViewVarHandle(int[].class, ByteOrder.BIG_ENDIAN);
final byte[] data = this.handleTarget.data;
assertEquals(16777216, varHandle.get(data, 0));
assertEquals(1, varHandle.get(data, 1));
assertEquals(256, varHandle.get(data, 2));
assertEquals(65536, varHandle.get(data, 3));
assertEquals(16777216, varHandle.get(data, 4));
}
代码清单 8-11.
字节数组视图
```

代码清单 8-12 使用 `byteBufferViewVarHandle()` 方法创建了一个 `VarHandle`。该代码与代码清单 8-11 的结果相同。

```
@Test
public void testByteBufferView() throws Exception {
final VarHandle varHandle = MethodHandles
.byteBufferViewVarHandle(int[].class, ByteOrder.BIG_ENDIAN);
final ByteBuffer dataBuffer = this.handleTarget.dataBuffer;
assertEquals(16777216, varHandle.get(dataBuffer, 0));
assertEquals(1, varHandle.get(dataBuffer, 1));
assertEquals(256, varHandle.get(dataBuffer, 2));
assertEquals(65536, varHandle.get(dataBuffer, 3));
assertEquals(16777216, varHandle.get(dataBuffer, 4));
}
代码清单 8-12.
ByteBuffer 视图
```

## 内存屏障

`VarHandle` 还增加了对内存屏障的支持，用于控制内存排序，这与 C/C++11 的 `atomic_thread_fence`（[`http://en.cppreference.com/w/cpp/atomic/atomic_thread_fence`](http://en.cppreference.com/w/cpp/atomic/atomic_thread_fence)）一致。屏障确保屏障之前的加载和/或存储不会与屏障之后的加载和/或存储重排序。`VarHandle` 中有五种不同的静态方法用于创建不同类型的屏障，以控制哪些操作不会被重排序；参见表 8-5。

表 8-5.
不同的内存屏障方法

| 方法 | 屏障前的操作 | 屏障后的操作 |
| --- | --- | --- |
| `void fullFence()` | 加载和存储 | 加载和存储 |
| `void acquireFence()` | 加载 | 加载和存储 |
| `void releaseFence()` | 加载和存储 | 存储 |
| `void loadLoadFence()` | 加载 | 加载 |
| `void storeStoreFence()` | 存储 | 存储 |

## 本章小结

在本章中，我们讨论了如何使用变量句柄来访问和操作变量。`VarHandle` 中的方法可以对变量执行读取、写入和原子更新操作。在下一章中，我们将讨论 Java 9 中对方法句柄的增强。

