# 12. I/O

本章涵盖了 Java 9 中 `java.io` 包的更改。



## InputStream

`java.io.InputStream` 类新增了三个用于从输入流读取和复制数据的方法。

*   `readAllBytes()`：从输入流中读取所有剩余字节。
*   `readNBytes(byte[] b, int off, int len)`：从输入流中读取指定数量的字节到给定的字节数组 `b` 中。`off` 是读取位置，`len` 是要读取的字节数。
*   `transferTo(OutputStream out)`：从输入流中读取所有字节，并将其写入给定的输出流。

在清单 12-1 中，文件 `input.txt` 包含内容 “Hello World”。我使用 `readAllBytes()` 和 `readNBytes()` 方法从输入流读取数据，并根据这些数据创建字符串。我还使用了 `transferTo()` 方法将数据复制到 `ByteArrayOutputStream`。

```
public class TestInputStream {
private InputStream inputStream;
private static final String CONTENT = "Hello World";
@Before
public void setUp() throws Exception {
this.inputStream =
TestInputStream.class.getResourceAsStream("/input.txt");
}
@Test
public void testReadAllBytes() throws Exception {
final String content = new String(this.inputStream.readAllBytes());
assertEquals(CONTENT, content);
}
@Test
public void testReadNBytes() throws Exception {
final byte[] data = new byte[5];
this.inputStream.readNBytes(data, 0, 5);
assertEquals("Hello", new String(data));
}
@Test
public void testTransferTo() throws Exception {
final ByteArrayOutputStream outputStream = new ByteArrayOutputStream();
this.inputStream.transferTo(outputStream);
assertEquals(CONTENT, outputStream.toString());
}
}
清单 12-1.
InputStream 中用于读取和复制数据的方法
```

既然新增了这三个方法，对于某些常见的使用场景，你就不再需要使用像 Apache Commons IO 这样的第三方库了。

## ObjectInputStream 过滤器

你可以使用 Java 对象序列化来转换对象图，以实现持久化或网络传输。在反序列化数据以重新创建对象时，请务必检查输入以确保数据有效；否则，恶意内容可能会危害系统。你可以在 `ObjectInputStream` 上设置新接口 `java.io.ObjectInputFilter` 的实现来过滤输入。

`ObjectInputFilter` 是一个函数式接口，只有一个方法：`ObjectInputFilter.Status checkInput(ObjectInputFilter.FilterInfo filterInfo)`。接口 `ObjectInputFilter.FilterInfo` 包含正在反序列化的对象的信息以及 `ObjectInputStream` 对象的统计信息；参见表 12-1。

表 12-1.

`ObjectInputFilter.FilterInfo` 的方法

| 方法 | 描述 |
| --- | --- |
| `Class<?> serialClass()` | 返回正在反序列化的对象的类 |
| `long arrayLength()` | 如果正在反序列化的对象是数组，则返回数组的长度 |
| `long depth()` | 返回当前对象在对象图中的深度 |
| `long references()` | 返回当前的对象引用数量 |
| `long streamBytes()` | 返回当前已消耗的字节数 |

`checkInput()` 的返回值是枚举 `ObjectInputFilter.Status`；参见表 12-2。

表 12-2.

`ObjectInputFilter.Status` 的值

| 状态 | 描述 |
| --- | --- |
| `ALLOWED` | 允许反序列化该对象。 |
| `REJECTED` | 不允许反序列化该对象。 |
| `UNDECIDED` | 此过滤器未决定是否允许反序列化该对象。 |

一个 `ObjectInputFilter` 应该只检查它设计要处理的条件，并且仅在知道结果时才返回 `ALLOWED` 或 `REJECTED`；否则，它应该返回 `UNDECIDED` 以让其他过滤器决定结果。如果返回的状态是 `REJECTED`，那么 `ObjectInputStream` 会抛出 `java.io.InvalidClassException`。

可以使用新方法 `setObjectInputFilter()` 在单个 `ObjectInputStream` 对象上设置 `ObjectInputFilter`。你也可以使用 `ObjectInputFilter.Config` 类的静态方法 `setSerialFilter(ObjectInputFilter filter)` 设置一个进程范围的过滤器。如果未设置单个 `ObjectInputStream` 的过滤器，则将应用进程范围的过滤器。

`ObjectInputFilter.Config` 的另一个重要特性是，它可以根据字符串模式创建 `ObjectInputFilter`。编写字符串模式比实现 `ObjectInputFilter` 接口要容易得多。一个过滤器可以有多个模式，用分号（`;`）分隔。一个模式可以设置一个限制或指定要匹配的类名。支持以下限制：

*   `maxdepth=value`：对象图的最大深度
*   `maxrefs=value`：对象引用的最大数量
*   `maxbytes=value`：输入流中的最大字节数
*   `maxarray=value`：数组的最大长度

例如，你可以使用 `maxdepth=5` 将对象图的最大深度限制为 `5`。

其他模式用于匹配类名。以下是一些场景：

*   如果模式包含“/”，则它指定要匹配的模块名和类名。
*   如果模式以 `".**"` 结尾，则它匹配包及其子包中的任何类。
*   如果模式以 `".*"` 结尾，则它匹配包中的任何类。
*   如果模式以 `"*"` 结尾，则它匹配任何以该模式为前缀的类。
*   如果模式等于类名，则它匹配该类名。
*   如果模式以 "!" 开头，则它否定其后的模式。



清单 12-2 展示了使用 `ObjectInputFilter` 过滤输入流的一些示例。嵌套类 `A`、`B`、`C` 和 `D` 用于演示对象图。在方法 `setUp()` 中，我创建了类 `A` 的一个新实例，并使用 `ObjectOutputStream` 获取序列化后的字节。`objectInput` 是序列化数据的 `ByteArrayInputStream` 对象。在测试用例 `testFilterByClass` 中，我检查以确保输入流中不存在类 `B` 的对象。我期望抛出 `InvalidClassException` 异常。在测试用例 `testFilterByDepth` 中，我检查以确保对象图的最大深度不大于 `6`。在测试用例 `testProcessWideFilter` 中，我配置了一个进程级过滤器来检查类 `C`。在测试用例 `testFilterPattern` 中，我使用模式 `"!io.vividcode.feature9.** "` 来指定不允许包 `io.vividcode.feature9` 及其子包中的所有类。

```
public class TestObjectInputFilter {
static class A implements Serializable {
public int a = 1;
public B b = new B();
}
static class B implements Serializable {
public String b = "hello";
public C c = new C();
}
static class C implements Serializable {
public int c = 2;
public D[] ds = new D[]{new D(), new D()};
}
static class D implements Serializable {
public String d = "world";
}
private ByteArrayInputStream objectInput;
@Before
public void setUp() throws Exception {
final A a = new A();
final ByteArrayOutputStream baos = new ByteArrayOutputStream();
final ObjectOutputStream outputStream = new ObjectOutputStream(baos);
outputStream.writeObject(a);
this.objectInput = new ByteArrayInputStream(baos.toByteArray());
}
@Test(expected = InvalidClassException.class)
public void testFilterByClass() throws Exception {
final ObjectInputStream inputStream = new ObjectInputStream(this.objectInput);
inputStream.setObjectInputFilter(filterInfo -> {
if (B.class.isAssignableFrom(filterInfo.serialClass())) {
return ObjectInputFilter.Status.REJECTED;
}
return ObjectInputFilter.Status.UNDECIDED;
});
final A a = (A) inputStream.readObject();
assertEquals(1, a.a);
}
@Test
public void testFilterByDepth() throws Exception {
final ObjectInputStream inputStream = new ObjectInputStream(this.objectInput);
inputStream.setObjectInputFilter(filterInfo -> {
if (filterInfo.depth() > 6) {
return ObjectInputFilter.Status.REJECTED;
}
return ObjectInputFilter.Status.UNDECIDED;
});
final A a = (A) inputStream.readObject();
assertEquals(1, a.a);
}
@Test(expected = InvalidClassException.class)
public void testProcessWideFilter() throws Exception {
ObjectInputFilter.Config.setSerialFilter(filterInfo -> {
if (C.class.isAssignableFrom(filterInfo.serialClass())) {
return ObjectInputFilter.Status.REJECTED;
}
return ObjectInputFilter.Status.UNDECIDED;
});
final ObjectInputStream inputStream = new ObjectInputStream(this.objectInput);
final A a = (A) inputStream.readObject();
assertEquals(1, a.a);
}
@Test(expected = InvalidClassException.class)
@Ignore
public void testFilterPattern() throws Exception {
ObjectInputFilter.Config.setSerialFilter(
ObjectInputFilter.Config.createFilter("!io.vividcode.feature9.**"));
final ObjectInputStream inputStream = new ObjectInputStream(this.objectInput);
final A a = (A) inputStream.readObject();
assertEquals(1, a.a);
}
}
清单 12-2.
使用 ObjectInputFilter 过滤输入流
```

进程级过滤器只能设置一次。尝试再次设置会导致 `IllegalStateException`。这就是测试用例 `testFilterPattern` 被标记为忽略的原因。如果不忽略，该测试用例会因为进程级过滤器已在测试用例 `testProcessWideFilter` 中设置而失败并抛出 `IllegalStateException`。使用 Maven 运行测试用例时，你可以配置 Maven Surefire 插件为每个测试方法分叉 JVM；这样你就可以成功运行这两个测试用例了。

## 总结

在本章中，我们讨论了 Java 9 中与 I/O 相关的更改，包括 `InputStream` 和 `ObjectInputStream` 过滤器中的新方法。在下一章中，我们将讨论 Java 9 中与安全相关的更改。

