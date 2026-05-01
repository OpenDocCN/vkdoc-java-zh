# 4. GDK

GDK（Groovy 开发工具包）提供了许多辅助方法、运算符、实用程序和附加类。

其中一些是添加到每个 Java 类的方法，例如 `"each"`，而另一些则更为晦涩。

## 集合

Groovy 添加了大量有用的方法，使得操作集合、数组或任何 `Iterable` 更加容易：

*   `sort` — 对集合进行排序（如果它是可排序的）。
*   `findAll` — 查找所有匹配闭包的元素。
*   `collect` — 一个构建新集合的迭代器。
*   `inject` — 循环遍历值并返回单个值（类似于“reduce”的概念）。
*   `each` — 使用给定的闭包遍历值。
*   `eachWithIndex` — 使用两个参数进行遍历：一个值和一个索引。
*   `find` — 查找传递给给定闭包时返回 true 的第一个元素。
*   `findIndexOf` — 查找匹配闭包的第一个元素并返回其索引。
*   `any` — 如果任何元素对闭包返回 true，则为 true（类似于 OR）。
*   `every` — 如果所有元素对闭包返回 true，则为 true（类似于 AND）。
*   `reverse` — 反转列表中元素的顺序。
*   `first` — 获取列表的第一个元素。
*   `last` — 返回列表的最后一个元素。
*   `tail` — 返回列表中除第一个元素之外的所有元素（例如，对于递归策略很有用）。

### Java 8 流

Groovy 也增加了对简化处理 Java 8+ 流的支持。例如，`toList()` 和 `toSet()` 被添加到 `java.util.stream.Stream<T>` 接口中，允许你分别简化 `collect(Collectors.toList())` 和 `collect(Collectors.toSet())`。

### 展开

`spread` 运算符可用于访问集合中每个元素的属性。在许多情况下，它可以替代 `collect`。例如，让我们定义一个简单的 Dragon 类，如下所示：

```
class Dragon { String name }
```

你可以打印名为 `dragons` 的列表中每个 Dragon 的名称：

```
1   dragons*.name.each { println it }
```

这是以下代码的简短形式：

```
1   dragons.collect { dragon -> d.name }.each { println it }
```

### GPath

GPath 类似于 Groovy 中的 XPath。由于支持列表和映射的属性表示法，Groovy 提供了语法糖，使得处理嵌套集合变得非常容易，如下例所示：

```
1   def  listOfMaps = [['a': 11, 'b': 12], ['a': 21, 'b': 22]]
2   assert listOfMaps.a == [11, 21] //GPath 表示法
3   assert listOfMaps*.a == [11, 21] //展开点表示法

5   listOfMaps = [['a': 11, 'b': 12], ['a': 21, 'b': 22], null]
6   assert  listOfMaps*.a == [11, 21, null] // 处理 null 值
7   assert listOfMaps*.a == listOfMaps.collect { it?.a } //等效表示法
8   // 但这只会收集非 null 值
9   assert listOfMaps.a == [11,21]
```

如前面的示例所示，*. 提供 null 值，而仅使用“.”则会跳过任何 null 值。



## IO

GDK 在输入/输出（IO）方面为您提供了大量帮助。

### 文件

GDK 为 `File` 类添加了几个方法，以简化文件的读写操作。

```
1   println path.toFile().text
```

`File` 类新增了一个 `getText()` 方法，该方法仅用于读取整个文件。

```
1   new  File("books.txt").text = "Modern Java"
```

这里我们使用了 `File` 类上的 `setText` 方法，它仅用于写入文件内容。对于二进制文件，您还可以使用 `File` 上的 `bytes` 属性：

```
1   byte[] data = new   File('data').bytes
2   new  File('out').bytes = data
```

如果您想使用 `InputStream` 或 reader 进行输入，或使用相应的 `OutputStream` 或 writer 进行输出，您可以使用以下方法，这些方法也会为您处理流的关闭：

```
1   new File('dragons.txt').withInputStream {in -> }
2   new  File('dragons.txt').withReader {r -> }
3   new  File('dragons.txt').withOutputStream {out ->}
4   new  File('dragons.txt').withWriter {w -> }
```

最后，您可以使用 `eachLine` 方法来读取文件的每一行。例如：

```
1   new  File('dragons.txt').eachLine { line->
2     println "$line"
3   }
4   //或者
5   new  File('dragons.txt').eachLine { line, num ->
6     println "第 $num 行: $line"
7   }
```

在所有这些情况下，即使抛出异常，Groovy 也会负责关闭 I/O 资源。

### ![../images/426440_2_En_4_Chapter/426440_2_En_4_Figa_HTML.gif](img/426440_2_En_4_Figa_HTML.gif) 练习

打印出一个多行文件，然后将其读回并打印出各行。

### URL

GDK 使得执行 URL 变得极其简单。

以下 Java 代码在给定的 URL（此处为 [`http://google.com`](http://google.com)）上打开一个 HTTP 连接，将数据读入一个字节数组，并打印出结果文本。

```
1   URL url = new  URL("http://google.com");
2   InputStream input = (InputStream) url.getContent();
3   ByteArrayOutputStream out = new  ByteArrayOutputStream();
4   int n = 0;
5   byte[] arr = new byte[1024];

7   while  (-1 != (n = input.read(arr)))
8   out.write(arr, 0, n);

10   System.out.println(new String(out.toByteArray()));
```

然而，在 Groovy 中，这也可以简化为一行（忽略异常）：

```
1   println "http://google.com".toURL().text
```

`String` 类新增了一个 `toURL()` 方法，而 Groovy 中的 `URL` 类则新增了一个 `getText()` 方法（通过 `".text"` 调用）。

### ![../images/426440_2_En_4_Chapter/426440_2_En_4_Figb_HTML.gif](img/426440_2_En_4_Figb_HTML.gif) 练习

使用 Groovy 下载您最喜欢的网站，并尝试从中解析出一些内容。

## 范围

`Range` 是 Groovy 中的一种内置类型。它可以用于执行循环、在 switch 语句中使用、提取子字符串以及其他场景。范围通常使用语法 `start..end` 来定义。

范围在使用 `each` 方法和 `for` 循环进行遍历时非常方便：

```
1   (1..4).each {print it} //1234
2   for (i in 1..4) print i //1234
```

在前面的章节中演示了一个 `case` 语句，例如：

```
switch (x) {
case "foo": result = "foo"
break
case  12..30: result = "12 到 30"
break
```

### ![../images/426440_2_En_4_Chapter/426440_2_En_4_Figc_HTML.gif](img/426440_2_En_4_Figc_HTML.gif) 警告

这仅在传递给 `switch` 语句的值与 `Range` 类型相同（此处为 Integer）时才有效。

您可以使用 `getAt` 语法，通过范围从字符串中提取子字符串。例如：

```
1   def  text = 'learning groovy',
2   println text[0..4] //learn
3   println text[0..4, 8..-1] //learn groovy
```

### ![../images/426440_2_En_4_Chapter/426440_2_En_4_Figd_HTML.gif](img/426440_2_En_4_Figd_HTML.gif) 提示

负数从集合或字符串的最后一个元素开始倒数。因此 `-1` 等同于最后一个元素。

您还可以使用范围来访问列表中的元素：

```
1   def list = ['hank', 'john', 'fred']
2   println list[0..1] //[hank, john]
```

您可以通过使用 `..<` 运算符来定义一个不包含最后一个数字的范围。例如，打印 `1234` 的另一种方式如下：

```
1   (1..<5).each {print it} //1234
```

### ![../images/426440_2_En_4_Chapter/426440_2_En_4_Fige_HTML.gif](img/426440_2_En_4_Fige_HTML.gif) 练习

尝试在范围中使用变量。您需要用括号将变量括起来吗？

## 工具类

GDK 添加了几个工具类，例如 ConfigSlurper、JsonBuilder、JsonSlurper、Expando 和 ObservableList/Map/Set。

### ConfigSlurper

ConfigSlurper 是一个用于读取以 Groovy 脚本形式定义的配置文件的工具类。与 Java Properties 文件（以 `.properties` 结尾的文件）类似，ConfigSlurper 允许使用点符号。它还允许嵌套（闭包）配置值和任意对象类型。

```
1   def config = new ConfigSlurper().parse('''
2       app.date = new Date()
3       app.age  = 42
4       app {
5           name = "Test${42}"
6       }
7   ''')

9   def properties = config.toProperties()

11   assert properties."app.date" instanceof String
12   assert properties."app.age" == '42'
13   assert properties."app.name" == 'Test42'
```

### JsonBuilder 和 JsonSlurper

Groovy 提供了 JsonBuilder 和 JsonSlurper 类来帮助处理 JSON（JavaScript 对象表示法），这是一种非常常见的数据格式。它们都位于 `groovy.json` 包中。例如：

```
import groovy.json.*
def builder = new JsonBuilder()
builder.person {
name 'Adam'
age 37
conferences 'Gr8Conf', 'ÜberConf'
}
println builder
```

上述代码演示了使用 JsonBuilder 构建一个 person 对象，这将产生以下输出：

```
{"person":{"name":"Adam","age":37,"conferences":["Gr8Conf"," ÜberConf"]}
```

同样，我们可以使用 JsonSlurper 来解析 JSON，如下所示：

```
def slurper = new JsonSlurper()
def result = slurper.parseText(builder.toString())
assert result.person.name == "Adam"
assert result.person.age == 37
assert result.person.conferences.size() == 2
assert result.person.conferences[0] == "Gr8Conf"
```

这将解析来自先前定义的 builder 的相同 JSON，并验证返回的值是否符合预期。请注意，`result` 是一个值的映射，`person` 也是一个映射，而在此例中 `conferences` 是一个列表。

### Expando

Expando 类可用于创建动态可扩展的对象。您可以添加字段和方法。当您想要使用极其动态的元编程时，这会非常有用。例如，请参见以下代码：

```
1   def  expando = new  Expando()
2   expando.name = 'Draco' // 字段值
3   expando.say = { String s -> "${name} says $s" } //方法
4   expando.say('hello') // 输出: Draco says hello
```

### ![../images/426440_2_En_4_Chapter/426440_2_En_4_Figf_HTML.gif](img/426440_2_En_4_Figf_HTML.gif) 练习

使用元编程来修改某个类的 `metaClass`，然后打印出 `metaClass` 的类。它是 Expando 类吗？



### ObservableList/Map/Set

Groovy 提供了可观察的列表、映射和集合。当添加、删除或更改元素时，这些集合中的每一个都会触发 `PropertyChangeEvent`（来自 `java.beans` 包）。请注意，`PropertyChangeEvent` 不仅表示事件已发生，它还包含属性名称以及属性的旧值/新值的信息。

以下是一个使用 `ObservableList` 并打印每个事件类别的示例：

```
1   def  list = new  ObservableList()
2   def printer = {e -> println e.class}
3   list.addPropertyChangeListener(printer)
4   list.add 'Harry Potter'
5   list.add 'Hermione Granger'
6   list.remove(0)
7   println list
```

这将产生以下输出：

```
1   class groovy.util.ObservableList$ElementAddedEvent
2   class java.beans.PropertyChangeEvent
3   class groovy.util.ObservableList$ElementAddedEvent
4   class java.beans.PropertyChangeEvent
5   class groovy.util.ObservableList$ElementRemovedEvent
6   class java.beans.PropertyChangeEvent
7   [Hermione Granger]
```

这对于在集合上使用**观察者**模式非常有用。

### ![../images/426440_2_En_4_Chapter/426440_2_En_4_Figg_HTML.gif](img/426440_2_En_4_Figg_HTML.gif) 练习

使用 `ObservableMap` 和 `PropertyChangeListener` 来拒绝将空值添加到映射中。

