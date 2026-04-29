# 8. 输入与输出

多年来，文件和网络 I/O 已发展成为一个更完善的框架，用于处理文件、网络可扩展性和易用性。Java 可以监控文件夹、访问依赖于操作系统的方法，并创建可扩展的异步网络套接字。这是对已经非常强大的用于处理输入输出流以及序列化（和反序列化）对象信息的库的补充。

本章将介绍一些示例，演示不同的输入和输出过程。你将学习文件的序列化、通过网络发送文件、文件操作以及更多内容。阅读完本章的示例后，你将能够开发包含复杂输入和输出任务的应用程序。

**流与装饰器模式**

I/O 流是大多数 Java I/O 的基础，包含了大量适用于各种场景的现成流，但如果不提供一些上下文，它们会非常难以使用。流（就像河流一样）代表了数据的流入/流出。可以这样理解：当你打字时，你创建了一个系统接收的字符流（输入流）。当系统产生声音时，它会将声音发送到扬声器（输出流）。系统可能整天都在接收按键输入并发送声音，因此流要么在处理数据，要么在等待更多数据。

当流没有接收到任何数据时，它会等待（没有其他事情可做，对吧？）。一旦数据到来，流就开始处理这些数据。然后流会停止并等待下一个数据项的到来。这个过程会一直持续，直到这条“河流”干涸（流被关闭）。

就像河流一样，流可以相互连接（这就是装饰器模式）。就本章内容而言，你主要关心两个输入流。一个是文件输入流，另一个是网络套接字输入流。这两个流是你的 I/O 程序的数据源。它们对应的输出流分别是文件输出流和网络套接字输出流（很有创意，不是吗？）。就像水管工一样，你可以将它们连接在一起，创造出新的东西。例如，你可以将文件输入流连接到网络输出流，通过网络套接字发送文件内容。或者，你也可以反过来，将网络输入流（传入的数据）连接到文件输出流（写入磁盘的数据）。在 I/O 术语中，输入流被称为*源*，而输出流被称为*汇*。

还有其他可以粘合在一起的输入和输出流。例如，有一个`BufferedInputStream`，它允许你分块读取数据（这比逐字节读取更高效）；`DataOutputStream`允许你将 Java 基本类型写入输出流（而不仅仅是写入字节）。最有用的流之一是`ObjectInputStream`和`ObjectOutputStream`这对组合，它们允许你序列化/反序列化对象（本章中有一个关于此的示例）。

装饰器模式允许你不断地将流拼接在一起，以获得许多不同的效果。这种设计的美妙之处在于，你可以创建一个流，它接受任何输入并产生任何输出，然后可以与任何其他流组合在一起。

## 8.1 序列化 Java 对象

### 问题

你需要序列化一个类（保存该类的内容），以便以后可以恢复它。

### 解决方案

Java 实现了一个内置的序列化机制。你可以通过`ObjectOutputStream`类访问该机制。在以下示例中，`saveSettings()`方法使用`ObjectOutputStream`来序列化设置对象，并将该对象写入磁盘。

```
public class Ch_8_1_SerializeExample {
public static void main(String[] args) {
Ch_8_1_SerializeExample example = new Ch_8_1_SerializeExample();
example.start();
}
private void start() {
ProgramSettings settings = new ProgramSettings(new Point(10,10),
new Dimension(300,200),   Color.blue,
"The title of the application" );
saveSettings(settings,"settings.bin");
ProgramSettings loadedSettings = loadSettings("settings.bin");
if(loadedSettings != null)
System.out.println("Are settings are equal? :"+loadedSettings.equals(settings));
}
private void saveSettings(ProgramSettings settings, String filename) {
try {
FileOutputStream fos = new FileOutputStream(filename);
try (ObjectOutputStream oos = new ObjectOutputStream(fos)) {
oos.writeObject(settings);
}
} catch (IOException e) {
e.printStackTrace();
}
}
private ProgramSettings loadSettings(String filename) {
try {
FileInputStream fis = new FileInputStream(filename);
ObjectInputStream ois = new ObjectInputStream(fis);
return (ProgramSettings) ois.readObject();
} catch (IOException | ClassNotFoundException e) {
e.printStackTrace();
}
return null;
}
}
```

以下是`ProgramSetting`类。

```
package org.java17recipes.chapter08.recipe08_01;
import java.awt.*;
import java.io.Serializable;
public class ProgramSettings implements Serializable {
private static final long serialVersionUID = 1L;
private Point locationOnScreen;
private Dimension frameSize;
private Color defaultFontColor;
private String title;
// 空构造函数
public ProgramSettings() {
}
public ProgramSettings(Point locationOnScreen, Dimension frameSize, Color defaultFontColor, String title) {
this.locationOnScreen = locationOnScreen;
this.frameSize = frameSize;
this.defaultFontColor = defaultFontColor;
this.title = title;
}
public Point getLocationOnScreen() {
return locationOnScreen;
}
public void setLocationOnScreen(Point locationOnScreen) {
this.locationOnScreen = locationOnScreen;
}
public Dimension getFrameSize() {
return frameSize;
}
public void setFrameSize(Dimension frameSize) {
this.frameSize = frameSize;
}
public Color getDefaultFontColor() {
return defaultFontColor;
}
public void setDefaultFontColor(Color defaultFontColor) {
this.defaultFontColor = defaultFontColor;
}
public String getTitle() {
return title;
}
public void setTitle(String title) {
this.title = title;
}
@Override
public boolean equals(Object o) {
if (this == o) return true;
if (o == null || getClass() != o.getClass()) return false;
ProgramSettings that = (ProgramSettings) o;
if (defaultFontColor != null ? !defaultFontColor.equals(that.defaultFontColor) : that.defaultFontColor != null)
return false;
if (frameSize != null ? !frameSize.equals(that.frameSize) : that.frameSize != null) return false;
if (locationOnScreen != null ? !locationOnScreen.equals(that.locationOnScreen) : that.locationOnScreen != null)
return false;
if (title != null ? !title.equals(that.title) : that.title != null) return false;
return true;
}
@Override
public int hashCode() {
int result = locationOnScreen != null ? locationOnScreen.hashCode() : 0;
result = 31 * result + (frameSize != null ? frameSize.hashCode() : 0);
result = 31 * result + (defaultFontColor != null ? defaultFontColor.hashCode() : 0);
result = 31 * result + (title != null ? title.hashCode() : 0);
return result;
}
}
```



### 工作原理

Java 支持*序列化*，即获取一个对象并创建其字节表示形式，以便后续恢复该对象的能力。借助内部序列化机制，序列化对象的大部分准备工作都已自动完成。Java 将对象的属性转换为字节流，随后可将其保存到文件或通过网络传输。

注意

原始的 Java 序列化框架使用反射来序列化对象，因此如果频繁进行序列化/反序列化操作，可能会引发问题。目前有许多开源框架可供选择，最佳方案取决于你的需求（速度 vs. 体积 vs. 易用性）。参见 [`https://github.com/eishay/jvm-serializers/wiki/`](https://github.com/eishay/jvm-serializers/wiki/)。

要使一个类可序列化，它需要实现 `Serializable` 接口，这是一个*标记接口*。它不包含任何方法，而是告知序列化机制允许你的类被序列化。虽然一开始并不明显，但序列化会暴露你类的所有内部工作细节（包括受保护和私有成员），因此如果你希望保密核弹发射的授权代码，你可能需要让包含此类信息的任何类都不可序列化。

此外，类的所有属性（例如成员、变量或字段）也必须都是可序列化的（和/或瞬态的，我们稍后会讲到）。所有基本类型——`int`、`long`、`double` 和 `float`（及其包装类）——以及 `String` 类在设计上都是可序列化的。其他 Java 类则需根据具体情况判断是否可序列化。例如，你不能序列化任何 `Swing` 组件（如 `JButton` 或 `JSpinner`），也不能序列化 `File` 对象，但你可以序列化 `Color` 类（更准确地说，是 `awt.color`）。

作为设计原则，你不应该序列化主类，而应创建仅包含你想要序列化的属性的类。这能省去大量调试时的麻烦，因为序列化会变得非常具有渗透性。如果你将一个主类标记为可序列化（`implements Serializable`），并且该类包含许多其他属性，那么你也需要将这些类声明为可序列化。如果你的 Java 类继承自另一个类，父类也应该是可序列化的。如果父类不可序列化，则父类的属性将不会被序列化。

如果你想将某个属性标记为不可序列化，可以将其标记为 *transient*。瞬态属性告诉 Java 编译器你对该属性的保存/加载不感兴趣，因此它会被忽略。某些属性是作为瞬态属性的理想候选，例如缓存的计算结果或你总是实例化为相同值的日期格式化器。

根据序列化框架的特性，静态属性是不可序列化的；静态类也是如此。原因在于静态类无法被实例化，尽管公共静态内部类可以被实例化。因此，如果你同时保存并加载静态类，你实际上加载了该静态类的另一个副本，这会导致 JVM 陷入混乱。

Java 序列化机制在幕后工作，以转换并遍历类中标记为 `Serializable` 的每个对象。如果应用程序包含对象中的对象，甚至可能包含交叉引用的对象，序列化框架会解析这些对象，并仅存储任何对象的单个副本。每个属性都被转换为 `byte[]` 表示形式。字节数组的格式包括实际的类名（例如，`com.somewhere.over.the.rainbow.preferences.UserPreferences`），后跟属性的编码（这些属性又可能编码另一个对象类及其属性，等等，*以此类推*）。

对于好奇者来说，如果你查看生成的文件（即使是在文本编辑器中），你几乎可以在文件的开头部分看到类名。

注意

序列化非常脆弱。默认情况下，序列化框架会生成一个*流唯一标识符 (SUID)*，该标识符捕获了关于类中存在哪些字段、它们的类型（public/protected）、哪些是瞬态的以及其他信息。即使是对类进行看似微小的修改（例如，将 `int` 属性改为 `long` 属性）也会生成一个新的 SUID。使用先前 SUID 保存的类无法在新的 SUID 上反序列化。这样做是为了保护序列化/反序列化机制，同时也保护了设计者。

你可以指示 Java 类使用特定的 SUID。这允许你序列化类、修改它们，然后在实现一些向后兼容性的同时反序列化原始类。你面临的风险是反序列化必须向后兼容。重命名或删除字段会在反序列化类时生成异常。如果你在可序列化类上指定了自己的 SUID，请确保每次更改类时都进行一些向后兼容性的单元测试。

由于序列化的性质，不要期望在反序列化对象时会调用构造函数。如果你的构造函数中包含对象正常运行所需的初始化代码，你可能需要将代码从构造函数中重构出来，以便在构造后正确执行。原因在于，在反序列化过程中，反序列化的对象是在内部“恢复”的（而非创建），并且不会调用构造函数。

## 8.2 更高效地序列化 Java 对象

### 问题

你想要序列化一个类，但希望输出比内置序列化方法生成的结果更高效或体积更小。



### 解决方案

通过让对象实现 `Externalizable` 接口，你可以指示 Java 虚拟机使用自定义的序列化/反序列化机制，如下例中 `readExternal`/`writeExternal` 方法所提供的那样。

```
public class ExternalizableProgramSettings implements Externalizable {
private Point locationOnScreen;
private Dimension frameSize;
private Color defaultFontColor;
private String title;
// 空构造函数，Externalizable 实现者必需
public ExternalizableProgramSettings() {
}
@Override
public void writeExternal(ObjectOutput out) throws IOException {
out.writeInt(locationOnScreen.x);
out.writeInt(locationOnScreen.y);
out.writeInt(frameSize.width);
out.writeInt(frameSize.height);
out.writeInt(defaultFontColor.getRGB());
out.writeUTF(title);
}
@Override
public void readExternal(ObjectInput in) throws IOException, ClassNotFoundException {
locationOnScreen = new Point(in.readInt(), in.readInt());
frameSize = new Dimension(in.readInt(), in.readInt());
defaultFontColor = new Color(in.readInt());
title = in.readUTF();
}
// 为简洁起见，省略了 getter 和 setter
}
主类如下：
package org.java17recipes.chapter08.recipe08_02;
import java.awt.*;
import java.io.*;
public class Ch_8_2_ExternalizableExample {
public static void main(String[] args) {
Ch_8_2_ExternalizableExample example = new Ch_8_2_ExternalizableExample();
example.start();
}
private void start() {
ExternalizableProgramSettings settings = new ExternalizableProgramSettings(new Point(10,10),new Dimension(300,200), Color.blue, "The title of the application" );
saveSettings(settings,"settingsExternalizable.bin");
ExternalizableProgramSettings loadedSettings = loadSettings("settingsExternalizable.bin");
System.out.println("设置是否相等？ :"+loadedSettings.equals(settings));
}
private void saveSettings(ExternalizableProgramSettings settings, String filename) {
try {
FileOutputStream fos = new FileOutputStream(filename);
try (ObjectOutputStream oos = new ObjectOutputStream(fos)) {
oos.writeObject(settings);
}
} catch (IOException e) {
e.printStackTrace();
}
}
private ExternalizableProgramSettings loadSettings(String filename) {
try {
FileInputStream fis = new FileInputStream(filename);
ObjectInputStream ois = new ObjectInputStream(fis);
return (ExternalizableProgramSettings) ois.readObject();
} catch (IOException | ClassNotFoundException e) {
e.printStackTrace();
}
return null;
}
}
```

### 工作原理

Java 序列化框架允许你指定序列化对象的实现方式。它要求实现 `Externalizable` 接口，而不是 `Serializable` 接口。`Externalizable` 接口包含两个方法：`writeExternal(ObjectOutput out)` 和 `readExternal(ObjectInput in)`。通过实现这些方法，你告诉框架如何编码/解码你的对象。你可以选择实现 `Externalizable` 接口而非 `Serializable` 接口，因为 Java 的默认序列化效率非常低下。这是因为 Java 序列化框架需要确保每个对象（及其依赖对象）都被序列化。它甚至会写入具有默认值或可能为空和/或 null 的对象。实现 `Externalizable` 接口还能让你更精细地控制类的序列化方式。在我们的示例中，`Serializable` 版本创建了 439 字节的设置，而 `Externalizable` 版本仅占 103 字节！

## 8.3 将 Java 对象序列化为 XML

### 问题

尽管你很喜欢序列化框架，但你希望创建至少是跨语言兼容（或人类可读）的内容。你希望使用 XML 来保存和加载对象。

### 解决方案

在此示例中，`XMLEncoder` 对象对包含程序设置信息的 `Settings` 对象进行编码，并将其写入 `settings.xml` 文件。`XMLDecoder` 对象获取 `settings.xml` 文件并将其作为流读取，从而解码 `Settings` 对象。`FileSystem` 获取对机器文件系统的访问权限。`FileOutputStream` 将文件写入系统。`FileInputStream` 从文件系统中的文件获取输入字节。在此示例中，这三个文件对象创建新的 XML 文件并读取它们以进行处理。

```
public static void main(String[] args) {
Ch_8_3_XMLExample example = new Ch_8_3_XMLExample();
example.start();
}
private void start() {
ProgramSettings settings = new ProgramSettings(new Point(10,10),new Dimension(300,200), Color.blue, "The title of the application" );
try {
//编码
FileSystem fileSystem = FileSystems.getDefault();
try (FileOutputStream fos = new FileOutputStream("settings.xml"); XMLEncoder encoder =
new XMLEncoder(fos)) {
encoder.setExceptionListener((Exception e) -> {
System.out.println("异常！ :"+e.toString());
});
encoder.writeObject(settings);
}
// 解码
try (FileInputStream fis = new FileInputStream("settings.xml"); XMLDecoder decoder =
new XMLDecoder(fis)) {
ProgramSettings decodedSettings = (ProgramSettings) decoder.readObject();
System.out.println("是否相同？ "+settings.equals(decodedSettings));
}
Path file= fileSystem.getPath("settings.xml");
List xmlLines = Files.readAllLines(file, Charset.defaultCharset());
xmlLines.stream().forEach((line) -> {
System.out.println(line);
});
} catch (IOException e) {
e.printStackTrace();
}
}
```



### 工作原理

`XMLEncoder` 和 `XMLDecoder` 与序列化框架类似，使用反射来确定要写入哪些字段，但并非以二进制形式写入字段，而是将其写入为 XML。被编码的对象无需可序列化，但必须遵循 JavaBeans 规范。

JavaBeans 是指任何符合以下约定的对象。

*   对象包含一个公共的空（`无参`）构造函数。
*   对象为每个受保护/私有属性提供公共的 getter 和 setter 方法，其命名格式为 `get{属性}()` 和 `set{属性}()`。

`XMLEncoder` 和 `XMLDecoder` 仅对具有公共访问器（`get{属性}, set{属性}`）的 bean 属性进行编码/解码，因此任何私有且没有访问器的属性都不会被编码/解码。

提示

在编码/解码时注册一个异常监听器是一个好习惯。

`XMLEncoder` 会创建正在序列化的类的新实例（请记住，它们需要是 JavaBeans，因此必须有一个空的`无参`构造函数），然后找出哪些属性是可访问的（通过 `get{属性}, set{属性}`）。如果新实例化类的某个属性与原始类的该属性包含相同的值（即具有相同的默认值），则 `XMLEncoder` 不会写入该属性。换句话说，如果某个属性的默认值没有改变，`XMLEncoder` 就不会写入它。这提供了在不同版本之间更改“默认”值的灵活性。例如，如果某个属性的默认值在对象编码时为 2，而之后默认属性从 2 更改为 4，则解码后的对象将包含新的默认属性值 4（这可能不正确）。

`XMLEncoder` 还会跟踪引用。如果一个对象在对象图中被持久化时出现多次（例如，一个对象既在主类的 `Map` 中，又作为 `DefaultValue` 属性存在），那么 `XMLEncoder` 只会对其进行一次编码，并通过在 XML 中放置一个链接来关联引用。`XMLEncoder`/`XMLDecoder` 类比序列化框架宽容得多。在解码时，如果某个属性类型发生更改、被删除/添加/移动/重命名，解码过程会“尽可能多地”解码，同时跳过无法解码的属性。

建议不要持久化你的主类（即使 `XMLEncoder` 更宽容），而是创建一些简单的、持有基本信息的、并且自身不执行太多任务的特殊对象。

## 8.4 创建 Socket 连接并通过网络发送可序列化对象

### 问题

你需要打开一个网络连接来发送/接收对象。

### 解决方案

使用 Java 的新输入/输出 API 版本 2（NIO.2）来发送和接收对象。以下解决方案利用了 NIO.2 的非阻塞套接字特性（使用 `Future` 任务）。

```
public class Ch_8_4_AsyncChannel {
private AsynchronousSocketChannel clientWorker;
InetSocketAddress hostAddress;
public Ch_8_4_AsyncChannel() {
}
private void start() throws IOException, ExecutionException, TimeoutException, InterruptedException {
hostAddress = new InetSocketAddress(InetAddress.getByName("127.0.0.1"), 2583);
Thread serverThread = new Thread(() -> {
serverStart();
});
serverThread.start();
Thread clientThread = new Thread(() -> {
clientStart();
});
clientThread.start();
}
private void clientStart() {
try {
try (AsynchronousSocketChannel clientSocketChannel = AsynchronousSocketChannel.open()) {
Future connectFuture = clientSocketChannel.connect(hostAddress);
connectFuture.get();            // 等待直到连接完成。
OutputStream os = Channels.newOutputStream(clientSocketChannel);
try (ObjectOutputStream oos = new ObjectOutputStream(os)) {
for (int i = 0; i  serverFuture  = serverSocketChannel.accept();
final AsynchronousSocketChannel clientSocket = serverFuture.get();
System.out.println("已连接！");
if ((clientSocket != null) && (clientSocket.isOpen())) {
try (InputStream connectionInputStream = Channels.newInputStream(clientSocket)) {
ObjectInputStream ois = null;
ois = new ObjectInputStream(connectionInputStream);
while (true) {
Object object = ois.readObject();
if (object.equals("EOF")) {
clientSocket.close();
break;
}
System.out.println("收到 :" + object);
}
ois.close();
}
}
} catch (IOException | InterruptedException | ExecutionException | ClassNotFoundException e) {
e.printStackTrace();
}
}
public static void main(String[] args) throws IOException, ExecutionException, TimeoutException, InterruptedException {
Ch_8_4_AsyncChannel example = new Ch_8_4_AsyncChannel();
example.start();
}
}
输出结果为：
已连接！
收到 :Look at me 0
收到 :Look at me 1
收到 :Look at me 2
收到 :Look at me 3
收到 :Look at me 4
```



### 工作原理

在最基本的层面上，套接字需要类型、IP 地址和端口。虽然关于套接字的文献已经浩如烟海，但其核心思想却相当直接。就像邮局一样，套接字通信依赖于地址。这些地址负责传递数据。在本例中，我们选择了回环地址（即程序运行所在的同一台计算机）（127.0.0.1）和一个随机端口号（2583）。

新的 NIO.2 的优势在于其异步性。通过使用异步调用，您可以扩展应用程序，而无需为每个连接创建数千个线程。在我们的示例中，我们使用了异步调用并等待连接，为了示例的简洁性，这实际上使其变成了单线程，但请不要因此限制您使用更多异步调用来增强此示例。（请参阅本书多线程部分的技巧。）

客户端要建立连接，需要一个套接字通道。NIO.2 API 允许创建异步套接字通道。一旦创建了套接字通道，它就需要一个要连接的地址。`socketChannel.connect()` 操作不会阻塞；相反，它会返回一个 `Future` 对象（这与传统的 NIO 不同，在传统 NIO 中，调用 `socketChannel.connect()` 会阻塞，直到连接建立）。`Future` 对象允许 Java 程序继续执行其任务，并简单地查询已提交任务的状态。进一步打个比方，这就像您不必在前门等待邮件到达，而是可以做其他事情，并定期检查邮件是否已到达。Future 对象具有 `isDone()` 和 `isCancelled()` 等方法，可让您了解任务是已完成还是已取消。它还有 `get()` 方法，允许您等待任务完成。在我们的示例中，我们使用 `Future.get()` 来等待客户端连接建立。

一旦连接建立，就使用 `Channels.newOutputStream()` 创建一个输出流来发送信息。使用装饰器模式，用 `ObjectOutputStream` 装饰 `outputStream`，以便最终通过套接字发送对象。

服务器端代码稍微复杂一些。服务器套接字连接允许多个连接同时发生。因此，它们监听或接收连接，而不是发起连接。出于这个原因，服务器通常异步等待连接。

服务器首先建立其监听的地址（127.0.0.1:2583）并接受连接。调用 `serverSocketChannel.accept()` 会返回另一个 `Future` 对象，它为您灵活处理传入连接提供了便利。在我们的示例中，服务器连接简单地调用了 `future.get()`，该方法会阻塞（停止程序执行），直到有连接被接受。

服务器获取套接字通道后，通过调用 `Channels.newInputStream(socket)` 创建一个 `inputStream`，然后用 `ObjectInputStream` 包装该输入流。然后，服务器进入循环，读取来自 `ObjectInputStream` 的每个对象。如果接收到的对象的 `toString()` 方法等于 `EOF`，服务器将停止循环，并关闭连接。

注意

使用 `ObjectOutputStream` 和 `ObjectInputStream` 发送和接收大量对象可能会导致内存泄漏。`ObjectOutputStream` 会保留已发送对象的副本以提高效率。如果您再次发送同一个对象，`ObjectOutputStream` 和 `ObjectInputStream` 不会再次发送该对象，而是发送一个先前发送过的对象 ID。这种发送对象 ID 而非整个对象的行为会引发两个问题。

第一个问题是，就地更改（可变）的对象在通过网络发送时，不会在接收客户端反映更改。原因是，由于该对象已被发送过一次，`ObjectOutputStream` 认为该对象已经传输完毕，因此只发送其 ID，从而忽略了自发送以来对该对象所做的任何更改。为避免这种情况，请不要对通过网络发送的对象进行更改。此规则也适用于对象图中的子对象。

第二个问题是，由于 `ObjectOutputStream` 维护了一个已发送对象及其对象 ID 的列表，如果您发送大量对象，已发送对象到键的字典会无限增长，导致长时间运行的程序内存不足。为了缓解这个问题，您可以调用 `ObjectOutputStream.reset()`，它会清空已发送对象的字典。或者，您可以调用 `ObjectOutputStream.writeUnshared()`，这样就不会在 `ObjectOutputStream` 字典中缓存该对象。

## 8.5 获取 Java 执行路径

### 问题

您想要获取 Java 程序正在运行的路径。

### 解决方案

调用 `System` 类的 `getProperty` 方法。示例如下。

```
String  path = System.getProperty("user.dir");
主类是：
public class Ch_8_5_path {
public static void main(String[] args) {
String  path = System.getProperty("user.dir");
System.out.println(path);
}
}
在 Windows 操作系统中的输出是：
C:\eclipse-workspace\java17Recipes
```

### 工作原理

当 Java 程序启动时，JRE 会更新 `user.dir` 系统属性，以记录 JRE 被调用的位置。解决方案示例将 `"user.dir"` 属性名称传递给 `getProperty` 方法，该方法返回相应的值。

## 8.6 复制文件

### 问题

您需要将一个文件从一个文件夹复制到另一个文件夹。

### 解决方案

从默认的 [`java.nio.file`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/nio/file/package-summary.html)`.FileSystem` 抽象类中，创建文件/文件夹所在的“目标”和“源”路径，然后使用 `Files.copy` 静态方法在创建的路径之间复制文件。

```
public static void main (String[] args) {
Ch_8_6_CopyFileExample exampleCh86 = new Ch_8_6_CopyFileExample();
exampleCh86.copyFile();
}
private void copyFile() {
FileSystem fileSystem = FileSystems.getDefault();
Path sourcePath = fileSystem.getPath("file.log");
Path targetPath = fileSystem.getPath("file2.log");
System.out.println("从 "+sourcePath.toAbsolutePath().toString()+
" 复制到 "+targetPath.toAbsolutePath().toString());
try {
Files.copy(sourcePath, targetPath, StandardCopyOption.REPLACE_EXISTING);
} catch (IOException e) {
e.printStackTrace();
}
}
```

### 工作原理

在新的 NIO.2 库中，Java 使用一个抽象层，允许更直接地操作底层操作系统的文件属性。`FileSystems.getDefaults()` 获取可用的抽象系统，您可以在其上执行文件操作。

获取默认的 `FileSystem` 对象后，您可以查询文件对象。在 NIO.2 中，文件、文件夹和链接都被称为*路径*。一旦获得路径，您就可以对其执行操作。在此示例中，使用源路径和目标路径调用了 `Files.copy`。最后一个参数指的是不同的复制选项。不同的复制选项依赖于文件系统，因此请确保您选择的选项与您打算运行应用程序的操作系统兼容。

## 8.7 移动文件

### 问题

您需要将一个文件从一个文件系统位置移动到另一个位置。



### 解决方案

你可以使用默认的 [java.​nio.​file](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/nio/file/package-summary.html).FileSystem 抽象类来创建“目标”和“源”路径，并调用 `Files.move()` 静态方法。

```
public static void main (String[] args) {
Ch_8_7_MoveFileExample exampleCh87 = new Ch_8_7_MoveFileExample();
exampleCh87.moveFile();
}
private void moveFile() {
FileSystem fileSystem = FileSystems.getDefault();
Path sourcePath = fileSystem.getPath("file.log");
Path targetPath = fileSystem.getPath("file2.log");
System.out.println("Copy from "+sourcePath.toAbsolutePath().toString()+ " to "+targetPath.toAbsolutePath().toString());
try {
Files.move(sourcePath, targetPath);
} catch (IOException e) {
e.printStackTrace();
}
}
```

### 工作原理

与复制文件的方式相同，先创建源路径和目标路径。然后，`Files.move` 会为你将文件从一个位置移动到另一个位置。`Files` 对象提供的其他方法如下：

*   `Delete (path)`：删除一个文件（或空文件夹）。
*   `Exists (path)`：检查文件/文件夹是否存在。
*   `isDirectory (path)`：检查创建的路径是否指向一个目录。
*   `isExecutable (path)`：检查文件是否为可执行文件。
*   `isHidden (path)`：检查文件在操作系统中是可见还是隐藏。

你可以通过以下 `main` 方法来使用 `Delete` 和 `Exist` 方法。

```
public static void main (String[] args) {
FileSystem fileSystem = FileSystems.getDefault();
Path deletePath = fileSystem.getPath("deletePath");
System.out.println("Path to delete:"+deletePath.toAbsolutePath().toString());
System.out.println("Exist Path to delete? "+Files.exists(deletePath));
try {
Files.delete(deletePath);
} catch (IOException e) {
e.printStackTrace();
}
}
输出结果为：
Path to delete:C:\eclipse-workspace\java17Recipes\deletePath
Exist Path to delete? true
```

## 8.8 遍历目录中的文件

### 问题

你需要扫描一个目录中的文件。该目录下可能还有包含更多文件的子目录。你希望将这些子目录也纳入扫描范围。

### 解决方案

使用 NIO.2，创建一个 [java.​nio.​file](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/nio/file/package-summary.html).FileVisitor<T> 接口对象，并在其 `visitFile` 方法中执行所需的实现。接着，获取默认的 `FileSystem` 对象，并通过 `getPath()` 方法获取你想要扫描的路径引用。最后，调用 `Files.walkFileTree()` 方法，传入你创建的 `Path` 和 `FileVisitor<T>`。以下代码演示了如何执行这些任务。

```
public class Ch_8_8_TraverseDirectory {
private void start() {
FileVisitor myFileVisitor = new SimpleFileVisitor() {
@Override
public FileVisitResult visitFile(Path file, BasicFileAttributes attrs)
throws IOException {
System.out.println("Visited File: "+file.toString());
return FileVisitResult.CONTINUE;
}
};
FileSystem fileSystem = FileSystems.getDefault();
Path directory= fileSystem.getPath(".");
try {
Files.walkFileTree(directory, myFileVisitor);
} catch (IOException e) {
e.printStackTrace();
}
}
public static void main (String args[]) {
Ch_8_8_TraverseDirectory traverseDirectory = new Ch_8_8_TraverseDirectory();
traverseDirectory.start();
}
```

### 工作原理

在 NIO.2 出现之前，尝试遍历目录树需要用到递归，而且根据实现方式的不同，代码可能非常脆弱。获取文件夹内文件的调用是同步的。它们需要先扫描整个目录才能返回，这会导致应用程序用户感觉方法调用无响应。有了 NIO.2，你可以指定从哪个文件夹开始遍历，而 NIO.2 的调用会处理递归的细节。你只需要向 NIO.2 API 提供一个类，告诉它在找到文件/文件夹时该做什么（即 [java.​nio.​file](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/nio/file/package-summary.html).SimpleFileVisitor<T> 类的实现）。NIO.2 使用了“访问者”模式，因此它不需要预先扫描整个文件夹，而是在迭代处理文件的同时进行处理。

将 `SimpleFileVisitor<T>` 类实现为匿名内部类时，需要重写 `visitFile(Path file, BasicFileAttributesattrs()` 方法。重写此方法后，你就可以指定在遇到文件时要执行的任务。

`visitFile` 方法返回一个 `FileVisitReturn` 枚举。这个枚举会告诉 `FileVisitor<T>` 接下来要执行哪个操作。

*   `CONTINUE` 继续遍历目录树。
*   `TERMINATE` 停止遍历。
*   `SKIP_SUBTREE` 停止从当前树层级继续向下深入（仅当在 `preVisitDirectory()` 方法中返回此枚举时有用）。
*   `SKIP_SIBLINGS` 跳过与当前目录处于同一树层级的其他目录。

除了 `visitFile()` 方法之外，`SimpleFileVisitor<T>` 类还包含以下方法：

*   `preVisitDirectory` 在进入要遍历的目录之前调用。
*   `postVisitDirectory` 在完成遍历一个目录后调用。
*   `visitFile` 在访问文件时调用，如示例代码所示。
*   `visitFileFailed` 在无法访问文件时调用，例如发生 I/O 错误时。

## 8.9 查询（和设置）文件元数据

### 问题

你需要获取某个特定文件的信息，例如文件大小、是否为目录等。此外，你可能还想在 Windows 操作系统中将文件标记为*已存档*，或者在 *nix 操作系统中授予特定的 POSIX 文件权限。

### 解决方案

使用 Java NIO.2，你可以通过简单地调用 `java.nio.file.Files` 工具类上的方法，并传入你想要获取元数据的路径，来获取任何文件信息。你可以通过调用 `Files.getFileAttributeView()` 方法，并传入你想要使用的属性视图的具体实现，来获取属性信息。以下代码演示了获取元数据的这些技术。

```
public class Ch_8_09_MetadataInfo {
private void start() {
Path path = FileSystems.getDefault().getPath("./file2.log");
try {
// 通用文件属性，所有 Java 系统均支持
System.out.println("File Size:"+Files.size(path));
System.out.println("Is Directory:"+Files.isDirectory(path));
System.out.println("Is Regular File:"+Files.isRegularFile(path));
System.out.println("Is Symbolic Link:"+Files.isSymbolicLink(path));
System.out.println("Is Hidden:"+Files.isHidden(path));
System.out.println("Last Modified Time:"+Files.getLastModifiedTime(path));
System.out.println("Owner:"+Files.getOwner(path));
// 特定属性视图。
DosFileAttributeView view = Files.getFileAttributeView(path,
DosFileAttributeView.class);
System.out.println("DOS File Attributes\n");
System.out.println("------------------------------------\n");
System.out.println("Archive  :"+view.readAttributes().isArchive());
System.out.println("Hidden   :"+view.readAttributes().isHidden());
System.out.println("Read-only:"+view.readAttributes().isReadOnly());
System.out.println("System   :"+view.readAttributes().isSystem());
view.setArchive(false);
} catch (IOException e) {
e.printStackTrace();
}
}
public static void main(String[] args) {
Ch_8_09_MetadataInfo info = new Ch_8_09_MetadataInfo();
info.start();
}
```



### 工作原理

Java NIO.2 在获取和设置文件属性方面比旧的 I/O 技术提供了更大的灵活性。NIO.2 将不同操作系统的属性抽象为“通用”和“操作系统特定”两组属性。标准属性如下所示。

*   `isDirectory` 如果是目录，则返回 true
*   `isRegularFile` 如果文件不被视为常规文件、文件不存在或无法确定是否为常规文件，则返回 false。
*   `isSymbolicLink` 如果链接是符号链接（在 Unix 系统中最常见），则返回 true。
*   `isHidden` 如果文件在操作系统中被视为隐藏文件，则返回 true。
*   `LastModifiedTime` 是文件最后更新的时间。
*   `Owner` 是操作系统中的文件所有者。

此外，NIO.2 允许访问底层操作系统的特定属性。为此，你首先需要获取一个表示操作系统文件属性的视图（在此示例中，它是一个 `DosFileAttributeView`）。获取视图后，你就可以查询和更改操作系统特定的属性。

## 8.10 监控目录内容变更

### 问题

你需要跟踪目录内容何时发生变化（例如，文件被添加、修改或删除），并对这些变化做出响应。

### 解决方案

通过使用 `java.nio.file.WatchService` 接口，你可以订阅并接收文件夹内发生的事件通知。在以下示例中，我们订阅了 `ENTRY_CREATE`、`ENTRY_MODIFY` 和 `ENTRY_DELETE` 事件。

```
public class Ch_8_10_MonitorFolder {
private void start() {
try {
System.out.println("Watch Event, press q to exit");
FileSystem fileSystem = FileSystems.getDefault();
WatchService service = fileSystem.newWatchService();
Path path = fileSystem.getPath(".");
System.out.println("Watching :"+path.toAbsolutePath());
path.register(service, StandardWatchEventKinds.ENTRY_CREATE, StandardWatchEventKinds.ENTRY_DELETE, StandardWatchEventKinds.ENTRY_MODIFY);
boolean shouldContinue = true;
while(shouldContinue) {
WatchKey key = service.poll(250, TimeUnit.MILLISECONDS);
// Code to stop the program
while (System.in.available() > 0) {
int readChar = System.in.read();
if ((readChar == 'q') || (readChar == 'Q')) {
shouldContinue = false;
break;
}
}
if (key == null) continue;
key.pollEvents().stream()
.filter((event) -> !(event.kind() == StandardWatchEventKinds.OVERFLOW))
.map((event) -> (WatchEvent)event).forEach((ev) -> {
Path filename = ev.context();
System.out.println("Event detected :"+filename.toString()+" "+ev.kind());
});
boolean valid = key.reset();
if (!valid) {
break;
}
}
} catch (IOException | InterruptedException e) {
e.printStackTrace();
}
}
public static void main (String[] args) {
Ch_8_10_MonitorFolder monitorFolder = new Ch_8_10_MonitorFolder();
monitorFolder.start();
}
}
}
如果创建了一个文档然后将其删除，输出如下：
Watch Event, press q to exit
Watching :C:\eclipse-workspace\java17Recipes\.
Event detected :test.txt ENTRY_CREATE
Event detected :test.txt ENTRY_DELETE
```

### 工作原理

NIO.2 包含一个内置的轮询机制，用于监控文件系统的变化。使用轮询机制，你可以等待事件并在指定的时间间隔内轮询更新。一旦事件发生，你可以处理并消费它。一个被消费的事件会通知 NIO.2 框架，你已经准备好处理新的事件了。

要开始监控一个文件夹，首先创建一个 `WatchService`，你可以用它来轮询变化。创建 `WatchService` 后，将 `WatchService` 注册到一个路径上。路径代表文件系统中的一个文件夹。当 `WatchService` 注册到该路径时，你需要定义要监控的事件类型（参见表 8-1）。这些事件类型在 `java.nio.file` 包的 `StandardWatchEventKinds` 类中声明。

表 8-1

watchEvents 的类型

| WatchEvent | 描述 |
| --- | --- |
| `OVERFLOW` | 已溢出的事件（忽略） |
| `ENTRY_CREATE` | 创建了一个目录或文件 |
| `ENTRY_DELETE` | 删除了一个目录或文件 |
| `ENTRY_MODIFY` | 修改了一个目录或文件 |

将 `WatchService` 注册到路径后，你可以“轮询”`WatchService` 以获取事件发生情况。通过调用 `watchService.poll()` 方法，你会等待该路径上发生文件/文件夹事件。使用 `watchService.poll(int timeout, Timeunit timeUnit)` 方法，会等待直到指定的超时时间到达才继续执行。如果 `WatchService` 接收到事件，或者允许的时间已过，则继续执行。如果没有事件且超时时间已到，`watchService.poll(int timeout)` 返回的 `WatchKey` 对象为 null；否则，返回的 `WatchKey` 对象包含已发生事件的相关信息。

由于许多事件可能同时发生（例如，移动整个文件夹或将一堆文件粘贴到一个文件夹中），`WatchKey` 对象可能包含多个事件。你可以通过调用 `key.pollEvents()` 方法，使用 `key` 来获取与该对象键关联的所有事件。

`watchKey.pollEvents()` 调用返回一个可迭代的 `watchEvents` 列表。每个 `watchEvent` 包含事件所引用的实际文件或文件夹的信息（例如，整个子文件夹可能已被移动或删除）以及事件类型（添加、编辑、删除）。只有那些在 `WatchService` 中注册的事件才会被处理。

一旦事件被处理，调用 `EventKey.reset()` 非常重要。reset 返回一个布尔值，用于确定 `WatchKey` 是否仍然有效。如果 `WatchKey` 被取消或其源 `WatchService` 被关闭，则该 `WatchKey` 变为无效。如果 `eventKey` 返回 false，你应该跳出监控循环。

## 8.11 读取属性文件

### 问题

你想为应用程序建立一些配置设置。你想手动或以编程方式修改这些设置。此外，你希望某些配置可以在不重新编译和重新部署的情况下进行更改。



### 解决方案

创建一个属性文件来存储应用程序配置。使用 `Properties` 对象加载属性文件中存储的属性，以供应用程序处理。属性也可以在属性文件中进行更新和修改。以下示例演示了如何读取名为 `properties.conf` 的属性文件，加载其中的值供应用程序使用，最后设置一个属性并将其写入文件。

```
public class Ch_8_11_PropertiesExample {
private void start() {
File file = new File("properties.conf");
Properties properties = null;
try {
if (!file.exists()) {
file.createNewFile();
}
properties = new Properties();
properties.load(new FileInputStream("properties.conf"));
} catch (IOException e) {
e.printStackTrace();
}
boolean shouldWakeUp = false;
int startCounter = 100;
String shouldWakeUpProperty = properties.getProperty("ShouldWakeup");
shouldWakeUp = (shouldWakeUpProperty == null) ? false : Boolean.parseBoolean(shouldWakeUpProperty.trim());
String startCounterProperty = properties.getProperty("StartCounter");
try {
startCounter = Integer.parseInt(startCounterProperty);
} catch (Exception e) {
System.out.println("Couldn't read startCounter, defaulting to " + startCounter);
}
String dateFormatStringProperty = properties.getProperty("DateFormatString", "MMM dd yy");
System.out.println("Should Wake up? " + shouldWakeUp);
System.out.println("Start Counter: " + startCounter);
System.out.println("Date Format String:" + dateFormatStringProperty);
//setting property
properties.setProperty("StartCounter", "250");
try {
properties.store(new FileOutputStream("properties.conf"), "Properties Description");
} catch (IOException e) {
e.printStackTrace();
}
properties.list(System.out);
}
public static void main(String[] args) {
Ch_8_11_PropertiesExample propertiesExample = new Ch_8_11_PropertiesExample();
propertiesExample.start();
}
}
输出结果为：
Should Wake up? false
Start Counter: 250
Date Format String:MMM dd yy
-- listing properties --
StartCounter=250
```

### 工作原理

Java 的 `Properties` 类帮助你管理程序属性。它允许你通过外部修改（有人编辑属性文件）或内部使用 `Properties.store()` 方法来管理属性。

`Properties` 对象可以在没有文件的情况下实例化，也可以预加载一个文件。`Properties` 对象读取的文件格式为 `[name]=[value]`，并以文本形式表示。如果你需要以其他格式存储值，则必须通过字符串进行读写。

如果你预期文件会在程序外部被修改（用户直接打开文本编辑器更改值），请务必对输入进行清理，例如去除值中的多余空格，并在必要时忽略大小写。

要以编程方式查询不同的属性，你可以调用 `getProperty(String)` 方法，传入你想要获取值的属性的字符串名称。如果未找到该属性，该方法返回 `null`。或者，你可以调用 `getProperty(String,String)` 方法，如果 `Properties` 对象中未找到该属性，则返回第二个参数作为其值。如果文件没有特定键的条目，指定默认值是一个好习惯。

查看生成的属性文件时，你会注意到前两行指示了文件的描述和修改日期。这两行以 `#` 开头，在 Java 属性文件中，这相当于注释。`Properties` 对象在处理文件时会跳过任何以 `#` 开头的行。

注意

如果你允许用户直接修改你的配置文件，那么在从 `Properties` 对象检索属性时进行验证非常重要。属性值中最常见的问题之一是前导和/或尾随空格。如果指定布尔或整数属性，请确保它们可以从字符串解析。至少，在尝试解析时要捕获异常，以应对非常规值（并记录违规值）。

## 8.12 解压文件

### 问题

你的应用程序需要从压缩的 `.zip` 文件中解压并提取文件。

### 解决方案

使用 `java.util.zip` 包，你可以打开一个 `.zip` 文件并遍历其条目。在遍历条目时，可以为目录条目创建目录。以下代码演示了如何执行解压和文件迭代技术，如上所述。

```
public class Ch_8_12_ZipExample {
public static void
main(String[] args) {
Ch_8_12_ZipExample example = new Ch_8_12_ZipExample();
example.start();
}
private void start() {
ZipFile file = null;
try {
file = new ZipFile("file.zip");
FileSystem fileSystem = FileSystems.getDefault();
Enumeration entries = file.entries();
String uncompressedDirectory = "uncompressed/";
Files.createDirectory(fileSystem.getPath(uncompressedDirectory));
while (entries.hasMoreElements()) {
ZipEntry entry = entries.nextElement();
if (entry.isDirectory()) {
System.out.println("Creating Directory:" + uncompressedDirectory + entry.getName());
Files.createDirectories(fileSystem.getPath(uncompressedDirectory +
entry.getName()));
} else {
InputStream is = file.getInputStream(entry);
System.out.println("File :" + entry.getName());
BufferedInputStream bis = new BufferedInputStream(is);
String uncompressedFileName = uncompressedDirectory + entry.getName();
Path uncompressedFilePath = fileSystem.getPath(uncompressedFileName);
Files.createFile(uncompressedFilePath);
try (FileOutputStream fileOutput = new FileOutputStream(uncompressedFileName)) {
while (bis.available() > 0) {
fileOutput.write(bis.read());
}
}
System.out.println("Written :" + entry.getName());
}
}
} catch (IOException e) {
e.printStackTrace();
}
}
```

### 工作原理

要处理 `.zip` 归档文件的内容，需要创建一个 `ZipFile` 对象。`ZipFile` 对象可以通过将 `.zip` 归档文件的名称传递给构造函数来实例化。创建对象后，你可以访问指定的 `.zip` 文件信息。每个 `ZipFile` 对象都包含代表归档文件中目录和文件的条目。通过遍历这些条目，你可以获取每个压缩文件的信息。每个 `ZipEntry` 实例都包含压缩和未压缩的大小、名称以及未压缩字节的输入流。

通过生成一个 `InputStream`，可以将未压缩的字节读入字节缓冲区，然后（在我们的例子中）写入文件。使用 `FileStream`，可以确定在不阻塞进程的情况下可以读取多少字节。一旦读取了确定数量的字节，这些字节就会被写入输出文件。此过程持续进行，直到读取完所有字节。

注意

如果文件非常大，将整个文件读入内存可能不是一个好主意。如果你需要处理大文件，最好先将其以未压缩格式写入磁盘（如示例所示），然后打开它并分块加载。如果你正在处理的文件不大（你可以通过检查 `getSize()` 方法来限制大小），那么你可能可以将其加载到内存中。

## 8.13 总结

本章演示了在 Java 中处理文件和网络 I/O 的几个示例。你学习了如何序列化文件以存储到磁盘，以及如何使用 Java API 操作主机的文件系统。本章还涵盖了如何读写属性文件以及执行文件压缩。最后，本章简要介绍了 Java 9 中新增的 Process API 特性。


