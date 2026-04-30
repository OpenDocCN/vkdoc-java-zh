# 第 4 章：几乎相同的老内容

## 概述

正如我在第 1 章中讨论的，您可能对 MIDP 作为平台感兴趣的原因之一是它基于 Java 编程语言和 Java API。您还会记得 MIDP 是构建在连接有限设备配置（CLDC）之上的。CLDC 包含大多数经验丰富的 Java 程序员会感到熟悉的 API。在撰写本文时，CLDC 1.0 是当前版本，但 CLDC 1.1 规范的收尾工作正在进行中。在本章中，我将提及 CLDC 1.0 和 CLDC 1.1 之间的变化。

在本章中，我们将探讨 CLDC 中定义的 `java.lang`、`java.io` 和 `java.util` 包。我假设您已经熟悉 J2SE 的基本 API；我将介绍哪些是相同的，哪些是不同的。

在撰写本文时（2003 年第一季度），CLDC 1.1 和 MIDP 2.0 尚未在真实设备上实现。尽管 MIDP 2.0 最终将在 CLDC 1.1 之上实现，但第一波 MIDP 2.0 实现将使用 CLDC 1.0。



## CLDC 1.0 中无浮点数支持

一个全局性的变化是，CLDC 1.0 完全不支持浮点类型。这意味着不存在 `float` 或 `double` 基本类型。对应的包装类型 `java.lang.Float` 和 `java.lang.Double` 也已被移除。

CLDC 1.0 中缺少浮点支持，是因为大多数小型设备没有用于浮点运算的硬件支持。如果涉及小数的计算对你的应用程序很重要，你可以在软件中执行这些计算。一个实现（使用定点整数）可以在 [`home.rochester.rr.com/ohommes/MathFP/`](http://home.rochester.rr.com/ohommes/MathFP/) 找到。

CLDC 1.1 包含了浮点支持，即基本类型 `double` 和 `float`，以及包装类型 `Double` 和 `Float`。CLDC 1.1 中其他各种类也为了浮点支持做了修改，但这些改动很小。

## java.lang

表 4-1 和 表 4-2 列出了 J2SE（SDK 1.4.0 版本）和 CLDC 中 `java.lang` 和 `java.lang.ref` 的类和接口。CLDC 列指示该类是否与其 J2SE 对应类相同、是否存在 API 差异，或者完全不存在。

表 4-1：*java.lang* 包

| **接口** |   |   |
| --- | --- | --- |
| ***J2SE SDK 1.4.0*** | ***CLDC 1.0*** | ***CLDC 1.1*** |
| --- | --- | --- |
| CharSequence | - | - |
| Cloneable | - | - |
| Comparable | - | - |
| Runnable | 相同 | 相同 |

| **类** |   |   |
| --- | --- | --- |
| ***J2SE SDK 1.4.0*** | ***CLDC 1.0*** | ***CLDC 1.1*** |
| --- | --- | --- |
| Boolean | 不同 | 不同 |
| Byte | 不同 | 与 CLDC 1.0 相同 |
| Character | 不同 | 与 CLDC 1.0 相同 |
| Character.Subset | - | - |
| Character.UnicodeBlock |  
```
-
```

 | - |
| Class | 不同 | 与 CLDC 相同 |
| ClassLoader | - | - |
| Compiler | - | - |
| Double | - | 不同 |
| Float | - | 不同 |
| InheritableThreadLocal | - | - |
| Integer | 不同 | 不同 |
| Long | 不同 | 不同 |
| Math | 不同 | 不同 |
| Number | - | - |
| Object | 不同 | 与 CLDC 1.0 相同 |
| Package | - | - |
| Process | - | - |
| Runtime | 不同 | 与 CLDC 1.0 相同 |
| RuntimePermission | - | - |
| SecurityManager | - | - |
| Short | 不同 | 与 CLDC 1.0 相同 |
| StackTraceElement | - | - |
| StrictMath | - | - |
| String | 不同 | 不同 |
| StringBuffer | 不同 | 不同 |
| System | 不同 | 与 CLDC 1.0 相同 |
| Thread | 不同 | 不同 |
| ThreadGroup | - | - |
| ThreadLocal | - | - |
| Throwable | 不同 | 与 CLDC 1.0 相同 |
| Void | - | - |

表 4-2：*java.lang.ref* 包

| **类** |   |   |
| --- | --- | --- |
| ***J2SE SDK 1.4.0*** | ***CLDC 1.0*** | ***CLDC 1.1*** |
| --- | --- | --- |
| PhantomReference | - | - |
| Reference | - | 不同 |
| ReferenceQueue | - | - |
| SoftReference | - | - |
| WeakReference | - | 不同 |

`java.lang.Object` 一如既往，是每个 Java 类的根。它与 J2SE 相比基本保持不变，但存在一些重要的差异。

### 无用户类加载

正如我在第 1 章中讨论的，Java 平台的优势之一是在运行时加载类的能力。不幸的是，由于资源限制和安全问题，CLDC 不允许你定义自己的类加载器。运行 MIDlet 的应用程序管理器有一个类加载器，但你无法以任何方式访问或使用它。

### 无对象终结

CLDC（以及扩展的 MIDP）中不支持对象终结。*终结*是一种机制，对象可以通过它在被垃圾回收之前自行清理。在 J2SE 中，对象的 `finalize()` 方法会在对象被垃圾回收器回收之前调用。CLDC 中不存在这种机制。如果你需要清理资源，你需要显式地进行清理，而不是将清理代码放在 `finalize()` 中。无论如何，这本身就是一个好主意，尤其是在资源有限的小型设备上。显式清理资源意味着它们消耗的内存和处理能力将更早被回收。`finalize()` 方法中的清理代码直到垃圾回收器运行时才会执行，而你永远不知道它何时会发生。

### 无反射

CLDC 不支持反射 API。CLDC/MIDP 的目标设备实在太小，无法支持它。尽管大多数开发者不需要直接使用反射，但这一缺失有重要影响。没有反射，就不可能实现远程方法调用（RMI）。没有 RMI，就不可能实现 JINI。因此，基础的 CLDC/MIDP 实现无法运行 JINI。如果你想运行 JINI，你需要研究更大的 J2ME 配置文件之一，很可能是个人配置文件（参见第 1 章）和 RMI 可选 API（JSR 66）。

### 无本地方法

CLDC（以及扩展的 MIDP）中不支持本地方法。该规范不支持从 Java 访问本地设备方法的方式。尽管 MIDP 实现当然包含本地方法，但它们被编译到实现本身中。应用程序不能定义新的本地方法。

不过，不必担心失去对平台特定功能的访问，因为设备供应商很可能在 MIDP 之外实现专有 API。详情请查看你的设备制造商或网络运营商的开发者网站。请记住，使用供应商或设备特定的 API 会限制你应用程序的受众。在某些情况下（主要是游戏），针对特定设备分发应用程序的多个版本是有意义的。

### 多线程

只要你保持简单，使用线程的方式与你从 J2SE 中记忆的差不多。创建新线程、启动它们以及使用方便的 `java.lang.Runnable` 接口与 J2SE 中相同。CLDC 1.0 中一个重要的遗漏是 `interrupt()` 方法，它在 `java.lang.Thread` 类中不存在。在 CLDC 1.1 中，`interrupt()` 方法可用。

`pause()`、`resume()` 和 `stop()` 方法（在 J2SE SDK 中已弃用）也不存在。CLDC/MIDP 不支持线程组和守护线程；CLDC 1.0 不支持线程命名，但在 CLDC 1.1 中可用。

### String 和 StringBuffer

`String` 和 `StringBuffer` 都存在于 CLDC 的 `java.lang` 包中。它们与其 J2SE 对应类相比基本保持不变。

CLDC 1.0 中 `String` 类最大的变化是移除了在浮点基本类型和 `String` 之间进行转换的 `valueOf()` 静态方法，尽管这些方法在 CLDC 1.1 中存在。CLDC 的 `String` 类中还缺少一些其他不常用的方法，但你很可能不会想念它们。例如，尽管 CLDC 的 `String` 包含了 `compareTo(String str)` 方法，但它没有 J2SE SDK 中的 `compareTo(Object o)` 或 `compareToIgnoreCase(String str)` 方法。（CLDC 1.1 的 `String` 类中确实包含了一个 `equalsIgnoreCase()` 方法。）

在 CLDC 1.0 版本的 `StringBuffer` 类中，`append()` 和 `insert()` 方法不包含针对浮点类型的重载，但这些在 CLDC 1.1 中可用。此外，`substring()` 方法已被移除。除此之外，对于经验丰富的 J2SE 程序员来说，`StringBuffer` 应该非常熟悉。



### 数学类

`Math` 类包含用于执行数学计算的静态方法。在 J2SE 中，这些方法大多涉及浮点数的三角函数。在 CLDC 1.0 中，这些方法全部被移除，仅保留了少数几个方法。CLDC 1.1 由于支持浮点类型，在 `java.lang.Math` 中增加了更多方法，但 CLDC 的 `java.lang.Math` 仍然是 J2SE 版本该类的一个子集。在下面的 API 列表中，加号 (+) 表示 CLDC 1.1 中新增的变量或方法。

```
 public final class Math
     extends java.lang.Object {
   // 常量
+  public static final double E;
+  public static final double PI;
     // 静态方法
   public static int abs(int a);
   public static long abs(long a);
+  public static float abs(float a);
+  public static double abs(double a);
+  public static native double ceil(double a);
+  public static native double cos(double a);
+  public static native double floor(double a);
   public static int max(int a, int b);
   public static long max(long a, long b);
+  public static float max(float a, float b);
+  public static double max(double a, double b);
   public static int min(int a, int b);
   public static long min(long a, long b);
+  public static float min(float a, float b);
+  public static double min(double a, double b);
+  public static native double sin(double a);
+  public static native double sqrt(double a);
+  public static native double tan(double a);
+  public static double toDegrees(double angrad);
+  public static double toRadians(double angdeg);
  }
```

### 运行时与系统类

`Runtime` 和 `System` 提供了对 Java 虚拟机 (JVM) 和系统级资源的访问。这两个类相较于 J2SE 中的对应类被大幅精简，因此在此完整重现它们的公共 API 是合理的。首先，我们来看一下 `Runtime`：

```
public class Runtime
    extends java.lang.Object {
  // 静态方法
  public static Runtime getRuntime();

  // 方法
   public void exit(int status);
   public native long freeMemory();
   public native void gc();
   public native long totalMemory();
  } 
```

要获取唯一的 `Runtime` 实例，请调用 `getRuntime()`。你可以通过调用 `gc()` 来指示 JVM 运行其垃圾回收器。如果尝试调用 `exit()`，则会抛出 `SecurityException`；应用程序的生命周期完全通过 `MIDlet` 类的方法进行管理。另外两个方法 `totalMemory()` 和 `freeMemory()` 允许你检查可用于应用程序数据的内存量。

请注意，`Runtime` 不支持通过 `exec()` 方法运行外部进程。MIDlet 不能超出 JVM 的边界。

`System` 提供了用于执行各种常见任务的静态方法：

```
public final class System
    extends java.lang.Object {
  // 常量
  public static final PrintStream err;
  public static final PrintStream out;

  // 静态方法
  public static native void arraycopy(Object src, int src_position,
      Object dst, int dst_position, int length);
  public static native long currentTimeMillis();
  public static void exit(int status);
  public static void gc();
  public static String getProperty(String key);
  public static native int identityHashCode(Object x);
}
```

你可能首先注意到，虽然定义了 `err` 和 `out` 这两个 `PrintStream`，但并没有 `System.in`。这很合理——`System.in` 代表控制台输入；在 MIDP 设备上，实际上并没有控制台。事实上，定义了 `System.out` 和 `System.err` 可能看起来有些奇怪。如果你将信息打印到 `System.out`，它在设备上可能不会显示在任何地方；然而，在设备模拟器上，你可能能够在控制台窗口中查看 `System.out`。

`gc()` 和 `exit()` 方法是调用 `Runtime` 类中对应方法的快捷方式。

`System` 的所有方法都是静态的。`arraycopy()` 方法提供了数组复制的快速实现。

最后，`identityHashCode()` 是 `Object` 的 `hashCode()` 方法使用的默认方法。

`getProperty()` 方法返回系统属性，这些属性不同于 `MIDlet` 的 `getAppProperty()` 方法返回的 MIDlet 属性。支持以下标准系统属性，并且可以在运行时检索它们的值：

*   **microedition.platform:** 此属性包含设备或主机平台的名称。如果实现未提供值，则默认为 null。

*   **microedition.encoding:** 此属性包含默认字符编码，它指定了 Unicode 字符在字节流中的表示方式。

*   **microedition.configuration:** 此属性包含所实现配置的名称，例如 "CLDC-1.1"。

*   **microedition.profiles:** 已实现的配置文件包含在此系统属性中。



## java.io 中的流

CLDC/MIDP 环境中的 `java.io` 包是 J2SE 中 `java.io` 的精简版本。表 4-3 总结了 J2SE 和 CLDC/MIDP 中 `java.io` 的类。如你所见，许多在 J2SE 中常见的 `java.io` 类在 CLDC/MIDP 中缺失了。

表 4-3：*java.io* 包

| **接口** |   |   |
| --- | --- | --- |
| ***J2SE SDK 1.4.0*** | ***CLDC 1.0*** | ***CLDC 1.1*** |
| --- | --- | --- |
| DataInput | 不同 | 不同 |
| DataOutput | 不同 | 不同 |
| Externalizable | - | - |
| FileFilter | - | - |
| FilenameFilter | - | - |
| ObjectInput | - | - |
| ObjectInputValidation | - | - |
| ObjectOutput | - | - |
| ObjectStreamConstants | - | - |
| Serializable | - | - |
| BufferedInputStream | - | - |
| BufferedOutputStream | - | - |
| BufferedReader | - | - |
| BufferedWriter | - | - |
| ByteArrayInputStream | 相同 | 相同 |
| ByteArrayOutputStream | 不同 | 与 CLDC 1.0 相同 |
| CharArrayReader | - | - |
| CharArrayWriter | - | - |
| DataInputStream | 不同 | 不同 |
| DataOutputStream | 不同 | 不同 |
| File | - | - |
| FileDescriptor | - | - |
| FileInputStream | - | - |
| FileOutputStream | - | - |
| FilePermission | - | - |
| FileReader | - | - |
| FileWriter | - | - |
| FilterInputStream | - | - |
| FilterOutputStream | - | - |
| FilterReader | - | - |
| FilterWriter | - | - |
| InputStream | 相同 | 相同 |
| InputStreamReader | 不同 | 与 CLDC 1.0 相同 |
| LineNumberInputStream | - | - |
| LineNumberReader | - | - |
| ObjectInputStream | - | - |
| ObjectInputStream.GetField | - | - |
| ObjectOutputStream | - | - |
| ObjectOutputStream.PutField | - | - |
| ObjectStreamClass | - | - |
| ObjectStreamField | - | - |
| OutputStream | 相同 | 相同 |
| OutputStreamWriter | 不同 | 与 CLDC 1.0 相同 |
| PipedInputStream | - | - |
| PipedOutputStream | - | - |
| PipedReader | - | - |
| PipedWriter | - | - |
| PrintStream | 不同 | 不同 |
| PrintWriter | - | - |
| PushbackInputStream | - | - |
| PushbackReader | - | - |
| RandomAccessFile | - | - |
| Reader | 相同 | 相同 |
| SequenceInputStream | - | - |
| SerializablePermission | - | - |
| StreamTokenizer | - | - |
| StringBufferInputStream | - | - |
| StringReader | - | - |
| StringWriter | - | - |
| Writer | 相同 | 相同 |

尽管 J2SE 和 CLDC 类之间的差异看起来很大，但它们可以轻松地归为三类：

1.  由于 CLDC/MIDP 没有本地文件系统的概念，所有与文件相关的类都已从 `java.io` 包中移除。这包括 `File`、`FileInputStream`、`FileOutputStream`、相应的 `Reader` 和 `Writer` 类、`RandomAccessFile` 以及各种支持类。如果你需要在设备上持久化存储数据，则需要使用 `javax.microedition.rms` 包 API，详见第 8 章。

2.  CLDC 不支持对象序列化。这意味着 `Serializable` 接口和各种对象流类都不存在。

3.  最后，J2SE 包含了一些实用流类——这些类你可能某天会用到，但不应该包含在内存较小的设备上。这些类包括管道流、回推流、序列流、行号流，以及像 `StreamTokenizer` 这样的其他一些“瑰宝”。如果你的 MIDlet 确实需要其中之一，你可以尝试将其与你的应用程序一起打包。^([1]) 请记住，直接使用 J2SE 中的类存在许可限制和技术问题；在开始复制文件之前，请确保你了解相关的法律影响。

### 字符编码

MIDP 包含了用于处理 Unicode 字符的 `Reader` 和 `Writer` 字符流。`InputStreamReader` 和 `OutputStreamWriter` 负责处理字节流和字符流之间的转换，这与 J2SE 中一样。*编码*决定了字节流和字符流之间的转换方式。如果你不指定编码，则会使用默认编码。如果你愿意，可以将编码名称传递给 `InputStreamReader` 和 `OutputStreamWriter` 的构造函数。到目前为止，这与 J2SE 完全相同。不过，在 MIDP 中，你可用的编码数量可能比 J2SE 少得多。

可以通过调用 `System.getProperty("microedition.encoding")` 获取 MIDP 实现的默认编码——默认为 `ISO8859_1`。

### 资源文件

如第 8 章所述，你可以从 MIDlet 套件的 JAR 文件中检索资源文件。使用 `Class` 中的 `getResourceAsStream()` 方法；它会返回一个 `InputStream`，你可以随意使用。

^([1])更好的做法是重新设计你的应用程序，使得在设备上不需要进行复杂的流处理。通常，你应该让服务器尽可能多地完成工作，而让你的 MIDlet 尽可能少地工作。

## java.util

CLDC 仅包含 J2SE 的 `java.util` 包中的十几个类。许多缺失的类是集合 API 的一部分，该 API 对于小型设备来说过于庞大。表 4-4 列出了 J2SE 和 CLDC/MIDP 中 `java.util` 的类和接口。

表 4-4：*java.util* 包

| **接口** |   |   |
| --- | --- | --- |
| ***J2SE SDK 1.4.0*** | ***CLDC 1.0*** | ***CLDC 1.1*** |
| --- | --- | --- |
| Collection | - | - |
| Comparator | - | - |
| Enumeration | 相同 | 相同 |
| EventListener | - | - |
| Iterator | - | - |
| List | - | - |
| ListIterator | - | - |
| Map | - | - |
| Map.Entry | - | - |
| Observer | - | - |
| RandomAccess | - | - |
| Set | - | - |
| SortedMap | - | - |
| SortedSet | - | - |

| **类** |   |   |
| --- | --- | --- |
| ***J2SE SDK 1.4.0*** | ***CLDC 1.0*** | ***CLDC 1.1*** |
| --- | --- | --- |
| AbstractCollection | - | - |
| AbstractList | - | - |
| AbstractMap | - | - |
| AbstractSequentialList | - | - |
| AbstractSet | - | - |
| ArrayList | - | - |
| Arrays | - | - |
| BitSet | - | - |
| Calendar | - | - |
| Collections | - | - |
| Currency | - | - |
| Date | 不同 | 与 CLDC 1.0 相同 |
| Dictionary | - | - |
| EventListenerProxy | - | - |
| EventObject | - | - |
| GregorianCalendar | - | - |
| HashMap | - | - |
| HashSet | - | - |
| Hashtable | 不同 | 与 CLDC 1.0 相同 |
| IdentityHashMap | - | - |
| LinkedHashMap | - | - |
| LinkedHashSet | - | - |
| LinkedList | - | - |
| ListResourceBundle | - | - |
| Locale | - | - |
| Observable | - | - |
| Properties | - | - |
| PropertyPermission | - | - |
| PropertyResourceBundle | - | - |
| Random | 不同 | 不同 |
| ResourceBundle | - | - |
| SimpleTimeZone | - | - |
| Stack | 相同 | 相同 |
| StringTokenizer | - | - |
| Timer | 相同 (MIDP) | 相同 (MIDP) |
| TimerTask | 相同 (MIDP) | 相同 (MIDP) |
| TimeZone | 不同 | 与 CLDC 1.0 相同 |
| TreeMap | - | - |
| TreeSet | - | - |
| Vector | 不同 | 与 CLDC 1.0 相同 |
| WeakHashMap |   |   |

### 集合

尽管 CLDC 不支持完整的 J2SE 集合 API，但旧的、熟悉的 `Vector` 和 `Hashtable` 类仍然保留，以及不太知名的 `Stack`。如果你熟悉 J2SE SDK 的 `Vector` 和 `Hashtable` 类，那么在 MIDP 中使用它们应该没有问题。

### 定时器

MIDP 包含了在 J2SE SDK 1.3 版本中引入的 `Timer` 和 `TimerTask` 类。这些是唯一未包含在 CLDC 中但包含在 MIDP 中的 J2SE 类。

`Timer` 的 API 与 J2SE 版本相同，但有一个例外。指定线程是否为守护线程的构造函数缺失了，因为 MIDP 不支持守护线程。`TimerTask` 的 API 在 J2SE SDK 和 MIDP 中完全相同。



### 计时

J2SE 拥有一系列令人印象深刻的类，可用于指定日期和时间，并在日期时间与人类可读的表示形式之间进行转换。J2SE 的时间类有四个不同的职责：

*   *时间点*由 `java.util.Date` 的实例表示。如果你把时间想象成一条线图，那么 `Date` 的实例就是线上的一个点。

*   *日历*用于通过年、月、日等日历字段来表示时间点。例如，如果你使用公历，那么你可以将单个时间点转换为一组日历值，如月、日以及小时、分钟和秒。在 J2SE 中，`java.util.Calendar` 是日历的父类，而 `java.util.GregorianCalendar` 类则代表了世界上大多数人熟悉的公历系统。

*   *格式化*类在时间点和人类可读字符串之间进行转换。在 J2SE 中，`java.text.DateFormat` 是那些既能生成也能解析表示时间点的人类可读字符串的类的父类。格式化类很可能会使用日历。例如，一个典型的 `DateFormat` 实现可能会使用 `GregorianCalendar` 将一个时间点转换为一组日历值，然后将其格式化为字符串。

*   *时区*类代表世界上的各个时区。日历和格式化类使用时区来创建特定时间点的本地化表示。在 J2SE 中，`java.util.TimeZone` 是所有时区的父类，`java.util.SimpleTimeZone` 是其具体实现。

理解这些类及其交互有点棘手，而且由于 JDK 1.0 和 JDK 1.1 之间的 API 发生了显著变化，情况变得更加复杂。`java.util.Date` 类曾经拥有额外的功能；尽管这些方法已被弃用，但它们仍然存在，可能会造成混淆。幸运的是，在 CLDC/MIDP 世界中，你无需处理这些问题。

在 CLDC/MIDP 中，情况稍微简单一些。首先，类更少了，而且 `Date` 类的 API 也得到了清理。在 MIDP 中，我刚才讨论的四个职责被分配给以下类：

*   *时间点*仍然由 `java.util.Date` 的实例表示。本质上，`Date` 类只是一个 `long` 值的包装器，该值表示自 1970 年 1 月 1 日午夜以来的毫秒数。（这是一种表示时间的标准方式。它大约还能再用 2.9 亿年，所以不用担心另一个千年虫问题。）

*   *日历*仍然由 `java.util.Calendar` 的实例表示。然而，`GregorianCalendar` 类不再是公共 API 的一部分。要获取 `Calendar` 实例，你可以使用 `getInstance()` 工厂方法。你很可能不需要这样做。

*   *格式化*类在 MIDP 中是不可见的。其中一个用户界面类 `javax.microedition.lcdui.DateField` 可以将 `Date` 转换为人类可读的显示形式，从而省去了你自己处理日期格式化器的麻烦。本质上，`DateField` 是 `Date` 实例的一个图形包装器。它还允许用户编辑日历和时钟字段以生成新的 `Date` 值。有关 `DateField` 的完整讨论，请参见第 6 章。

*   *时区*仍然由 `java.util.TimeZone` 的实例表示。`TimeZone` 提供了几个静态方法，用于检查可用的时区并获取代表特定时区的实例。

## 总结

开发者被信息所淹没，而最优秀的开发者是那些能够快速学习新知识的人。不过，偶尔，你已知的东西可以再次被利用。这就是其中一种情况——当你学习 MIDP 编程时，你已经了解的 J2SE API 会变得非常有用。MIDP 的 `java.lang`、`java.io` 和 `java.util` 包中包含的类，其外观和行为与 J2SE 中的对应类非常相似。

