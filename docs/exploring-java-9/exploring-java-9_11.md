# 11. Nashorn

Nashorn ( [`http://openjdk.java.net/projects/nashorn/`](http://openjdk.java.net/projects/nashorn/) ) 是 Java 8 中引入的 JavaScript 引擎，用于取代基于 Mozilla Rhino 的旧引擎。在 Java 8 中，Nashorn 基于 ECMAScript 5。Java 9 的 Nashorn 引擎已经实现了 ECMAScript 6 的一些新特性。与 Nashorn 相关的 API 位于 `jdk.scripting.nashorn` 模块中。

## 获取 Nashorn 引擎

您可以使用 JSR 223 ( [`https://www.jcp.org/en/jsr/detail?id=223`](https://www.jcp.org/en/jsr/detail?id=223) ) `ScriptEngine` 来获取 Nashorn 引擎；参见清单 11-1。表达式 `new ScriptEngineManager().getEngineByName("Nashorn")` 是获取 Nashorn `ScriptEngine` 实例的标准方式。但是，创建的 `ScriptEngine` 仅适用于 ECMAScript 5。您需要传递额外的参数 `--language=es6` 来启用 ECMAScript 6 特性。清单 11-1 向您展示了如何直接使用 `NashornScriptEngineFactory` 类来创建用于 ECMAScript 6 的 `ScriptEngine`。

```
public class NashornTest {
private final ScriptEngine es5Engine =
new ScriptEngineManager().getEngineByName("Nashorn");
private final ScriptEngine es6Engine =
new NashornScriptEngineFactory().getScriptEngine("--language=es6");
@Test
public void testSimpleEval() throws Exception {
assertEquals(2, es5Engine.eval("1 + 1"));
}
}
清单 11-1.
创建 Nashorn ScriptEngine
```

您也可以使用 `jjs` 工具来尝试 ECMAScript 6 的新特性。

```
$ jjs --language=es6
```

## ECMAScript 6 特性

以下各节展示了 Java 9 中 Nashorn 所支持的 ECMAScript 6 特性。

### 模板字符串

Nashorn 支持 ECMAScript 6 的模板字符串 ( [`https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/template_strings`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/template_strings) )，包括无标签和有标签两种形式；参见清单 11-2。

```
@Test
public void testTemplateString() throws Exception {
final Bindings bindings = new SimpleBindings();
bindings.put("name", "Alex");
final Object result = this.es6Engine.eval("Hello ${name}", bindings);
assertEquals("Hello Alex", result);
}
清单 11-2.
模板字符串
```

### 二进制和八进制字面量

Nashorn 也支持二进制（`b`）和八进制（`o`）字面量；参见清单 11-3。

```
@Test
public void testBinaryAndOctalLiterals() throws Exception {
assertEquals(503, this.es6Engine.eval("0b111110111"));
assertEquals(503, this.es6Engine.eval("0o767"));
}
清单 11-3.
二进制和八进制字面量
```

### 迭代器和 for..of 循环

Nashorn 支持迭代器和 `for..of` 循环。清单 11-4 中的 JavaScript 代码创建了一个 `random` 迭代器来生成随机数，然后使用 `for..of` 循环获取迭代器中的前 `10` 个元素。

```
let random = {
[Symbol.iterator]() {
return {
next() {
return { done: false, value: Math.random() }
}
}
}
}
let result = [];
for (var n of random) {
if (result.length >= 10)
break;
result.push(n);
}
result
清单 11-4.
JavaScript 迭代器
```

清单 11-5 中的 `eval()` 方法接收一个 JavaScript 文件名，并使用一个 `Bindings` 对象对其进行求值。由于清单 11-4 中的 JavaScript 代码返回一个数组，因此求值的返回值是一个 `jdk.nashorn.api.scripting.ScriptObjectMirror` 对象。在清单 11-5 中，我使用其 `isArray()` 方法来验证该值是一个数组，并检查数组大小为 `10`。

```
@Test
public void testIterator() throws Exception {
final ScriptObjectMirror result =
(ScriptObjectMirror) eval("iterator", null);
assertTrue(result.isArray());
assertEquals(10, result.size());
}
private Object eval(final String fileName, final Bindings inputBindings)
throws ScriptException {
final Bindings bindings = Optional.ofNullable(inputBindings)
.orElse(new SimpleBindings());
return this.es6Engine.eval(
new InputStreamReader(
NashornTest.class.getResourceAsStream(
String.format("/%s.js", fileName)
)),
bindings
);
}
清单 11-5.
在文件中求值 JavaScript
```

### 函数

在 Nashorn 中，您可以使用箭头函数 ( [`https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Functions/Arrow_functions`](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Functions/Arrow_functions) )。清单 11-6 是 JavaScript 文件 `function.js` 的内容。它使用箭头创建了函数 `add`。

```
let add = (a, b) => a + b;
add(1, 2)
清单 11-6.
JavaScript 箭头函数
```

在清单 11-7 中，我使用 `eval()` 方法验证了清单 11-6 中函数调用的结果。

```
@Test
public void testFunction() throws Exception {
final Object result = eval("function", null);
assertEquals(3, result);
}
清单 11-7.
验证函数调用的结果
```

## 解析器 API

Nashorn 有一个标准的解析器 API，您可以使用它将 ECMAScript 源代码解析为抽象语法树（AST）。当您想要分析 ECMAScript 源代码时，此 API 非常有用。使用此 API，您可以获取有关源代码的信息，但无法修改现有源代码。解析器 API 位于 `jdk.nashorn.api.tree` 包中。



### 基本解析

在清单 11-8 的 `testSimpleParser()` 方法中，我使用 `Parser.create(String… options)` 方法创建了一个新的 `Parser` 实例。参数 `options` 是一个用于配置解析器行为的选项列表。这里我使用 `--language=es6` 来启用 ECMAScript 6 解析模式。`Parser` 拥有多个不同的 `parse()` 方法来解析源代码。所有这些 `parse()` 方法都接受参数，通过 `File`、`Reader`、`String`、`URL`、`Path` 或 `jdk.nashorn.api.scripting.ScriptObjectMirror` 对象来指定源代码，并使用另一个 `DiagnosticListener` 类型的参数来指定一个监听器以接收解析错误。这里我解析了 JavaScript 代码 `function a() {return 'hello';}`，它只包含一个函数声明。接口 `DiagnosticListener` 仅包含一个方法 `report(Diagnostic diagnostic)`。接口 `Diagnostic` 包含用于检索诊断信息的方法，包括种类、代码、消息、行号、列号和文件名；参见表 11-1。这里我只是将数据打印到控制台。

表 11-1.

`Diagnostic` 的方法

| 方法 | 描述 |
| --- | --- |
| `Diagnostic.Kind getKind()` | 返回此诊断的种类 |
| `long getPosition()` | 返回指示问题位置的字符偏移量 |
| `String getFileName()` | 返回源文件名 |
| `long getLineNumber()` | 返回 `getPosition()` 返回的字符偏移量所在的行号 |
| `long getColumnNumber()` | 返回 `getPosition()` 返回的字符偏移量所在的列号 |
| `String getCode()` | 返回指示诊断类型的代码 |
| `String getMessage()` | 返回此诊断的消息 |

枚举 `Diagnostic.Kind` 表示不同种类的诊断。它具有以下值：`ERROR`、`WARNING`、`MANDATORY_WARNING`、`NOTE` 和 `OTHER`。

`parse()` 的返回值是接口 `CompilationUnitTree` 的一个实例，它表示解析后的抽象语法树。方法 `accept(TreeVisitor<R,D> visitor, D data)` 接受一个访问者，使用访问者模式来遍历树。Nashorn 提供了类 `SimpleTreeVisitorES5_1` 和 `SimpleTreeVisitorES6`，分别作为 ECMAScript 5.1 和 6 的简单实现。清单 11-8 扩展了 `SimpleTreeVisitorES6` 类并重写了 `visitFunctionDeclaration()` 方法，以验证源代码中有一个函数声明。

```
public class ParserAPITest {
@Test
public void testSimpleParser() throws Exception {
final Parser parser = Parser.create("--language=es6");
final CompilationUnitTree tree =
parser.parse(
"test.js",
"function a() {return 'hello';}",
System.out::println
);
final List functions = new ArrayList();
tree.accept(new SimpleTreeVisitorES6() {
@Override
public Void visitFunctionDeclaration(
final FunctionDeclarationTree node,
final Void r) {
final String name = node.getName().getName();
assertEquals("a", name);
functions.add(name);
return null;
}
}, null);
assertEquals(1, functions.size());
}
}
清单 11-8.
简单的 JavaScript 代码解析
```

### 解析错误

在清单 11-9 中，我解析了包含语法错误的 JavaScript 代码。我提供了一个 `DiagnosticListener` 的实现，用于验证错误的行号。

```
@Test
public void testParseError() throws Exception {
final Parser parser = Parser.create("--language=es6");
final List errors = new ArrayList();
parser.parse(
"error.js",
"function error() { var a = 1;",
diagnostic -> {
assertEquals(1, diagnostic.getLineNumber());
assertEquals(Diagnostic.Kind.ERROR, diagnostic.getKind());
errors.add(diagnostic);
}
);
assertEquals(1, errors.size());
}
清单 11-9.
JavaScript 代码解析错误
```

## 分析函数复杂度

现在我将向你展示一个使用解析器 API 的复杂示例。清单 11-10 展示了 `FunctionLengthAnalyzer` 类，它分析源代码中函数的长度。我为 `FunctionDeclarationTree` 和 `FunctionExpressionTree` 添加了访问者。为了测量函数的长度，我首先使用 `getStartPosition()` 和 `getEndPosition()` 方法分别获取函数体在源代码中的起始和结束字符偏移量。然后我通过减去这两个偏移量来计算方法体中的字符数。对于 `FunctionExpressionTree` 节点，函数可以是匿名的，因此我为这些匿名函数创建了一个名称。这里我分析了 jQuery 3 的源代码。

```
import com.google.common.collect.Lists;
import io.vavr.Tuple2;
import jdk.nashorn.api.tree.*;
import java.io.IOException;
import java.net.URL;
import java.util.Collections;
import java.util.Comparator;
import java.util.List;
import java.util.Optional;
public class FunctionLengthAnalyzer {
public List> analyze(final URL url) throws IOException {
final Parser parser = Parser.create();
final CompilationUnitTree tree = parser.parse(url, System.out::println);
final List> result = Lists.newArrayList();
tree.accept(new SimpleTreeVisitorES5_1() {
@Override
public Void visitFunctionDeclaration(
final FunctionDeclarationTree node,
final Void r) {
result.add(new Tuple2(
node.getName().getName(),
node.getBody().getEndPosition() - node.getBody().getStartPosition()));
return super.visitFunctionDeclaration(node, r);
}
@Override
public Void visitFunctionExpression(
final FunctionExpressionTree node,
final Void r) {
final String name = Optional.ofNullable(node.getName())
.map(IdentifierTree::getName)
.orElse("anonymous_" + node.getBody().getStartPosition());
result.add(new Tuple2(
name,
node.getBody().getEndPosition() - node.getBody().getStartPosition()));
return super.visitFunctionExpression(node, r);
}
}, null);
Collections.sort(result,
Collections.reverseOrder(Comparator.comparingLong(Tuple2::_2)));
return result;
}
public static void main(final String[] args) throws IOException {
final List> result =
new FunctionLengthAnalyzer().analyze(
new URL("https://code.jquery.com/jquery-3.2.1.js"));
result.forEach(tuple2 ->
System.out.printf("%30s -> %s%n", tuple2._1, tuple2._2));
}
}
清单 11-10.
分析函数的长度
```

在清单 11-10 中计算的函数长度不是很直观。如果我能获取函数体的起始和结束行号，然后计算一个函数的代码行数，那会更好。不幸的是，解析器 API 中没有暴露行号。我只能使用源代码中的字符偏移量。对于 `FunctionDeclarationTree` 节点，方法 `getBody()` 返回一个 `BlockTree` 对象，该对象有方法 `getStatements()` 来返回方法体中的语句列表。我也可以使用语句的数量来衡量函数的复杂度。

## 总结

在本章中，我们讨论了 Java 9 中 Nashorn 支持的新 ECMAScript 6 特性，以及可用于解析 JavaScript 代码的新解析器 API。在下一章中，我们将讨论 Java 9 中与 I/O 相关的更改。

