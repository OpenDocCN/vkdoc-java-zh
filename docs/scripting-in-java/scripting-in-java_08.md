# 8. 实现脚本引擎

在本章中，你将学习：

*   实现新脚本引擎时需要开发的脚本引擎组件
*   如何实现一个简单脚本引擎的不同组件，该引擎将对两个数字执行加、减、乘、除运算
*   如何打包脚本引擎的代码
*   如何部署和测试脚本引擎

## 引言

实现一个功能完备的脚本引擎并非易事，这超出了本书的讨论范围。本章旨在为你提供一个简要但完整的概述，说明实现脚本引擎所需的设置。在本节中，你将实现一个名为 `JKScript` 引擎的简单脚本引擎。它将根据以下规则计算算术表达式：

*   它将计算由两个操作数和一个运算符组成的算术表达式
*   表达式可以有两个数字字面量、两个变量，或者一个数字字面量和一个变量作为操作数。数字字面量必须采用十进制格式。不支持十六进制、八进制和二进制数字字面量
*   表达式中的算术运算仅限于加、减、乘、除
*   它将识别 `+`、`-`、`*` 和 `/` 作为算术运算符
*   引擎将返回一个 `Double` 对象作为表达式的结果
*   表达式中的操作数可以通过引擎的全局作用域或引擎作用域绑定传递给引擎
*   它应允许从 `String` 对象和 `java.io.Reader` 对象执行脚本。但是，`Reader` 的内容应仅包含一个表达式
*   它不会实现 `Invocable` 和 `Compilable` 接口

使用这些规则，你的脚本引擎的一些有效表达式如下：

*   `10 + 90`
*   `10.7 + 89.0`
*   `+10 + +90`
*   `num1 + num2`
*   `num1 * num2`
*   `78.0 / 7.5`

在实现脚本引擎时，你需要为以下两个接口提供实现：

*   `javax.script.ScriptEngineFactory`
*   `javax.script.ScriptEngine`

作为 `JKScript` 脚本引擎实现的一部分，你将开发表 8-1 中列出的三个类。在后续章节中，你将开发这些类。

表 8-1.

为 JKScript 脚本引擎开发的类列表

| 类 | 描述 |
| --- | --- |
| `Expression` | `Expression` 类是脚本引擎的核心。它负责解析和计算算术表达式。它被用于 `JKScriptEngine` 类的 `eval()` 方法内部。 |
| `JKScriptEngine` | `ScriptEngine` 接口的一个实现。它扩展了实现 `ScriptEngine` 接口的 `AbstractScriptEngine` 类。`AbstractScriptEngine` 类为 `ScriptEngine` 接口的多个版本的 `eval()` 方法提供了标准实现。你需要实现以下两个版本的 `eval()` 方法：`Object eval(String, ScriptContext)` `Object eval(Reader, ScriptContext)` |
| `JKScriptEngineFactory` | `ScriptEngineFactory` 接口的一个实现。 |

## Expression 类

`Expression` 类包含解析和计算算术表达式的主要逻辑。清单 8-1 包含了 `Expression` 类的完整代码。

清单 8-1\. 解析和计算算术表达式的 Expression 类

`// Expression.java`

`package com.jdojo.script;`

`import java.util.regex.Matcher;`

`import java.util.regex.Pattern;`

`import javax.script.ScriptContext;`

`public class Expression {`

`private String exp;`

`private ScriptContext context;`

`private String op1;`

`private char op1Sign = '+';`

`private String op2;`

`private char op2Sign = '+';`

`private char operation;`

`private boolean parsed;`

`public Expression(String exp, ScriptContext context) {`

`if (exp == null || exp.trim().equals("")) {`

`throw new IllegalArgumentException(this.getErrorString());`

`}`

`this.exp = exp.trim();`

`if (context == null) {`

`throw new IllegalArgumentException(        "ScriptContext cannot be null.");`

`}`

`this.context = context;`

`}`

`public String getExpression() {`

`return exp;`

`}`

`public ScriptContext getScriptContext() {`

`return context;`

`}`

`public Double eval() {`

`// 解析表达式`

`if (!parsed) {`

`this.parse();`

`this.parsed = true;`

`}`

`// 提取操作数的值`

`double op1Value = getOperandValue(op1Sign, op1);`

`double op2Value = getOperandValue(op2Sign, op2);`

`// 计算表达式`

`Double result = null;`

`switch (operation) {`

`case '+':`

`result = op1Value + op2Value;`

`break;`

`case '-':`

`result = op1Value - op2Value;`

`break;`

`case '*':`

`result = op1Value * op2Value;`

`break;`

`case '/':`

`result = op1Value / op2Value;`

`break;`

`default:`

`throw new RuntimeException(        "Invalid operation:" + operation);`

`}`

`return result;`

`}`

`private double getOperandValue(char sign, String operand) {`

`// 检查操作数是否为 double 类型`

`double value;`

`try {`

`value = Double.parseDouble(operand);`

`return sign == '-' ? -value : value;`

`}`

`catch (NumberFormatException e) {`

`// 忽略。操作数格式无法转换为 double 值。`

`}`

`// 检查操作数是否为绑定变量`

`Object bindValue = context.getAttribute(operand);`

`if (bindValue == null) {`

`throw new RuntimeException(operand +         " is not found in the script context.");`

`}`

`if (bindValue instanceof Number) {`

`value = ((Number) bindValue).doubleValue();`

`return sign == '-' ? -value : value;`

`}`

`else {`

`throw new RuntimeException(operand +         " must be bound to a number.");`

`}`

`}`

`public void parse() {`

`// 支持的表达式形式为 v1 op v2，其中 v1 和 v2 是变量名或数字，`

`// op 可以是 +、-、* 或 /`

`// 为预期的表达式准备模式`

`String operandSignPattern = "([+-]?)";`

`String operandPattern = "([\\p{Alnum}\\p{Sc}_.]+)";`

`String whileSpacePattern = "([\\s]*)";`

`String operationPattern = "([+*/-])";`

`String pattern = "^" + operandSignPattern + operandPattern +`

`whileSpacePattern + operationPattern + whileSpacePattern +`

`operandSignPattern + operandPattern + "$";`

`Pattern p = Pattern.compile(pattern);`

`Matcher m = p.matcher(exp);`

`if (!m.matches()) {`

`// 表达式格式不符合预期`

`throw new IllegalArgumentException(this.getErrorString());`

`}`

`// 获取操作数-1`

`String temp = m.group(1);`

`if (temp != null && !temp.equals("")) {`

`this.op1Sign = temp.charAt(0);`

`}`

`this.op1 = m.group(2);`

`// 获取操作符`

`temp = m.group(4);`

`if (temp != null && !temp.equals("")) {`

`this.operation = temp.charAt(0);`

`}`

`// 获取操作数-2`

`temp = m.group(6);`

`if (temp != null && !temp.equals("")) {`

`this.op2Sign = temp.charAt(0);`

`}`

`this.op2 = m.group(7);`

`}`

`private String getErrorString() {`

`return "Invalid expression[" + exp + "]" +`

`"\nSupported expression syntax is: op1 operation op2" +`

`"\n where op1 and op2 can be a number or a bind variable" +`

`" , and operation can be +, -, *, and /.";`

`}`

`@Override`

`public String toString() {`

`return "Expression: " + this.exp + ", op1 Sign = " +`

`op1Sign + ", op1 = " + op1 + ", op2 Sign = " +`

`op2Sign + ", op2 = " + op2 + ", operation = " + operation;`

`}`

`}`

`Expression` 类被设计用于解析和计算以下形式的算术表达式：

`op1 operation op2`

这里，`op1` 和 `op2` 是两个操作数，可以是十进制格式的数字或变量，`operation` 可以是 `+`、`-`、`*` 或 `/`。

`Expression` 类的建议用法是：

`Expression exp = new Expression(expression, scriptContext);`

`Double value = exp.eval();`

让我们详细讨论一下 `Expression` 类的重要组件。



### 实例变量

名为 `exp` 和 `context` 的实例变量分别表示表达式和用于求值的 `ScriptContext`。它们会被传递给此类的构造方法。

名为 `op1` 和 `op2` 的实例变量分别表示表达式中的第一个和第二个操作数。实例变量 `op1Sign` 和 `op2Sign` 分别表示第一个和第二个操作数的符号，可以是 `'+'` 或 `'-'`。当使用 `parse()` 方法解析表达式时，会填充这些操作数及其符号。

名为 `operation` 的实例变量表示要对操作数执行的算术运算（`+`、`-`、`*` 或 `/`）。

名为 `parsed` 的实例变量用于跟踪表达式是否已被解析。`parse()` 方法会将其设置为 `true`。

### 构造方法

`Expression` 类的构造方法接受一个表达式和一个 `ScriptContext`。它会确保它们不为 `null`，并将它们存储在实例变量中。在将表达式存储到名为 `exp` 的实例变量之前，它会去除表达式开头和结尾的空白字符。

### parse() 方法

`parse()` 方法将表达式解析为操作数和运算。它使用正则表达式来解析表达式文本。该正则表达式期望表达式文本具有以下形式：

*   第一个操作数的可选符号 `+` 或 `-`
*   第一个操作数，可由字母数字、货币符号、下划线和十进制小数点的组合构成
*   任意数量的空白字符
*   一个运算符号，可以是 `+`、`-`、`*` 或 `/`
*   第二个操作数的可选符号 `+` 或 `-`
*   第二个操作数，可由字母数字、货币符号、下划线和十进制小数点的组合构成

正则表达式 `([+-]?)` 将匹配操作数的可选符号。正则表达式 `([\\p{Alnum}\\p{Sc}_.]+)` 将匹配一个操作数，它可以是十进制数或名称。正则表达式 `([\\s]*)` 将匹配任意数量的空白字符。正则表达式 `([+*/-])` 将匹配一个运算符号。所有正则表达式都用括号括起来以形成组，这样你就可以捕获表达式中匹配的部分。

如果表达式与正则表达式匹配，`parse()` 方法会将匹配的部分存储到各自的实例变量中。

请注意，用于匹配操作数的正则表达式并不完美。它会允许一些无效情况，例如操作数包含多个小数点等。不过，就本次演示而言，这已经足够了。

### getOperandValue() 方法

`getOperandValue()` 方法在表达式解析后，用于表达式求值过程中。如果操作数是一个 `double` 数字，它会应用操作数的符号后返回该值。否则，它会在 `ScriptContext` 中查找操作数的名称。如果在 `ScriptContext` 中未找到操作数的名称，它会抛出一个 `RuntimeException`。如果在 `ScriptContext` 中找到了操作数的名称，它会检查该值是否为数字。如果该值是数字，它会应用符号后返回该值；否则，它会抛出一个 `RuntimeException`。

该方法不支持十六进制、八进制和二进制格式的操作数。例如，像 “0x2A + 0b1011” 这样的表达式不会被当作包含两个 `int` 字面量的表达式来处理。留给读者去增强此方法，以支持十六进制、八进制和二进制格式的数字字面量。

### eval() 方法

`eval()` 方法对表达式进行求值并返回一个 `double` 值。首先，如果表达式尚未被解析，它会解析该表达式。请注意，多次调用 `eval()` 只会解析表达式一次。

它获取两个操作数的值，执行运算，并返回表达式的值。

## JKScriptEngine 类

清单 8-2 包含了 `JKScript` 脚本引擎的实现。其 `eval(String, ScriptContext)` 方法包含了主要逻辑，如下所示：

`Expression exp = new Expression(script, context);`

`Object result = exp.eval();`

它创建了一个 `Expression` 类的对象。它调用 `Expression` 对象的 `eval()` 方法，该方法对表达式求值并返回结果。

`eval(Reader`, `ScriptContext)` 方法从 `Reader` 中读取所有行，将它们连接起来，并将生成的 `String` 传递给 `eval(String, ScriptContext)` 方法以对表达式求值。请注意，`Reader` 中只能包含一个表达式。一个表达式可以跨越多行。`Reader` 中的空白字符会被忽略。

清单 8-2\. JKScript 脚本引擎的一个实现

`// JKScriptEngine.java`

`package com.jdojo.script;`

`import java.io.BufferedReader;`

`import java.io.IOException;`

`import java.io.Reader;`

`import javax.script.AbstractScriptEngine;`

`import javax.script.Bindings;`

`import javax.script.ScriptContext;`

`import javax.script.ScriptEngineFactory;`

`import javax.script.ScriptException;`

`import javax.script.SimpleBindings;`

`public class JKScriptEngine extends AbstractScriptEngine {`

`private ScriptEngineFactory factory;`

`public JKScriptEngine(ScriptEngineFactory factory) {`

`this.factory = factory;`

`}`

`@Override`

`public Object eval(String script, ScriptContext context)`

`throws ScriptException {`

`try {`

`Expression exp = new Expression(script, context);`

`Object result = exp.eval();`

`return result;`

`}`

`catch (Exception e) {`

`throw new ScriptException(e.getMessage());`

`}`

`}`

`@Override`

`public Object eval(Reader reader, ScriptContext context) throws ScriptException {`

`// 从 Reader 中读取所有行`

`BufferedReader br = new BufferedReader(reader);`

`String script = "";`

`String str = null;`

`try {`

`while ((str = br.readLine()) != null) {`

`script = script + str;`

`}`

`}`

`catch (IOException e) {`

`throw new ScriptException(e);`

`}`

`// 使用 eval() 的 String 版本`

`return eval(script, context);`

`}`

`@Override`

`public Bindings createBindings() {`

`return new SimpleBindings();`

`}`

`@Override`

`public ScriptEngineFactory getFactory() {`

`return factory;`

`}`

`}`



## JKScriptEngineFactory 类

清单 8-3 包含了 `JKScript` 引擎对 `ScriptEngineFactory` 接口的实现。其中一些方法返回 `"Not Implemented"` 字符串，因为你并不支持这些方法所暴露的功能。`JKScriptEngineFactory` 类中的代码不言自明。可以通过 `ScriptEngineManager` 使用 `getNames()` 方法中编码的名称 `jks`、`JKScript` 或 `jkscript` 来获取 `JKScript` 引擎的实例。

清单 8-3\. JKScript 脚本引擎的 ScriptEngineFactory 实现

`// JKScriptEngineFactory.java`

`package com.jdojo.script;`

`import java.util.ArrayList;`

`import java.util.Arrays;`

`import java.util.Collections;`

`import java.util.List;`

`import javax.script.ScriptEngine;`

`import javax.script.ScriptEngineFactory;`

`public class JKScriptEngineFactory implements ScriptEngineFactory {`

`@Override`

`public String getEngineName() {`

`return "JKScript Engine";`

`}`

`@Override`

`public String getEngineVersion() {`

`return "1.0";`

`}`

`@Override`

`public List<String> getExtensions() {`

`return Collections.unmodifiableList(Arrays.asList("jks"));`

`}`

`@Override`

`public List<String> getMimeTypes() {`

`return Collections.unmodifiableList(Arrays.asList("text/jkscript") );`

`}`

`@Override`

`public List<String> getNames() {`

`List<String> names = new ArrayList<>();`

`names.add("jks");`

`names.add("JKScript");`

`names.add("jkscript");`

`return Collections.unmodifiableList(names);`

`}`

`@Override`

`public String getLanguageName() {`

`return "JKScript";`

`}`

`@Override`

`public String getLanguageVersion() {`

`return "1.0";`

`}`

`@Override`

`public Object getParameter(String key) {`

`switch (key) {`

`case ScriptEngine.ENGINE:`

`return getEngineName();`

`case ScriptEngine.ENGINE_VERSION:`

`return getEngineVersion();`

`case ScriptEngine.NAME:`

`return getEngineName();`

`case ScriptEngine.LANGUAGE:`

`return getLanguageName();`

`case ScriptEngine.LANGUAGE_VERSION:`

`return getLanguageVersion();`

`case "THREADING":`

`return "MULTITHREADED";`

`default:`

`return null;`

`}`

`}`

`@Override`

`public String getMethodCallSyntax(String obj, String m, String[] p) {`

`return "Not implemented";`

`}`

`@Override`

`public String getOutputStatement(String toDisplay) {`

`return "Not implemented";`

`}`

`@Override`

`public String getProgram(String[] statements) {`

`return "Not implemented";`

`}`

`@Override`

`public ScriptEngine getScriptEngine() {`

`return new JKScriptEngine(this);`

`}`

`}`

## 准备部署

在打包 `JKScript` 脚本引擎的类之前，你还需要执行一步：创建一个名为 `META-INF` 的目录。在该目录下，创建一个名为 `services` 的子目录。在 `services` 目录中，创建一个名为 `javax.script.ScriptEngineFactory` 的文本文件。请注意，文件名必须完全按照上述方式命名，且不应包含任何扩展名（如 `.txt`）。

编辑 `javax.script.ScriptEngineFactory` 文件，并输入清单 8-4 所示的内容。文件中的第一行是以 `#` 号开头的注释。第二行是 `JKScript` 脚本引擎工厂类的完全限定名。

清单 8-4\. 名为 javax.script.ScriptEngineFactory 的文件内容

`#JKScript 引擎的工厂类`

`com.jdojo.script.JKScriptEngineFactory`

为什么必须执行这一步？你将把 `javax.script.ScriptEngineFactory` 文件与 `JKScript` 引擎的类文件一起打包到一个 JAR 文件中。脚本引擎的发现机制会在 CLASSPATH 中所有 JAR 文件的 `META-INF/services` 目录下搜索此文件。如果找到该文件，则会读取其内容，并实例化该文件中的所有脚本工厂类，将其包含在脚本引擎工厂列表中。因此，这一步对于使你的 `JKScript` 引擎能够被 `ScriptEngineManager` 自动发现是必要的。

## 打包 JKScript 文件

你需要将 `JKScript` 脚本引擎的所有文件打包到一个名为 `jkscript.jar` 的 JAR 文件中。你也可以将其命名为其他任何名称。以下是包含其目录的文件列表。请注意，在这种情况下，一个空的 `manifest.mf` 文件即可正常工作：

*   `com\jdojo\script\Expression.class`
*   `com\jdojo\script\JKScriptEngine.class`
*   `com\jdojo\script\JKScriptEngineFactory.class`
*   `META-INF\manifest.mf`
*   `META-INF\services\javax.script.ScriptEngineFactory`

你可以手动创建 `jkscript.jar` 文件，方法是将除 `manifest.mf` 文件之外的所有这些文件复制到一个目录中（例如 Windows 下的 `C:\build`），然后从 `C:\build` 目录执行以下命令：

`C:\build> jar cf jkscript.jar com\jdojo\script\*.class META-INF\services\*.*`

`jkscript.jar` 文件位于本书可下载包中的 `src\JavaScripts` 目录下。可下载的源代码包含一个 NetBeans 8.0 项目，并且 `jkscript.jar` 文件已添加到该项目的 CLASSPATH 中。如果你从附带的 NetBeans IDE 中运行该引擎，则无需执行打包和部署步骤即可使用 `JKScript` 引擎。



## 使用 JKScript 脚本引擎

现在是时候测试你的 `JKScript` 脚本引擎了。首要且最重要的一步是将你在上一节创建的 `jkscript.jar` 文件包含到应用程序的 CLASSPATH 中。一旦你将 `jkscript.jar` 文件包含到应用程序的 CLASSPATH 中，使用 `JKScript` 就与使用任何其他脚本引擎没有区别。

以下代码片段使用 `JKScript` 作为其名称创建了一个 `JKScript` 脚本引擎的实例。你也可以使用它的其他名称，例如 `jks` 和 `jkscript`：

`// 创建 JKScript 引擎`

`ScriptEngineManager manager = new ScriptEngineManager();`

`ScriptEngine engine = manager.getEngineByName("JKScript");`

`if (engine == null) {`

`System.out.println("JKScript 引擎不可用。" +`

`"请将 jkscript.jar 添加到 CLASSPATH。");`

`}`

`else {`

`// 评估你的 JKScript`

`}`

清单 8-5 包含一个使用 `JKScript` 脚本引擎评估不同类型表达式的程序。存储在 `String` 对象和文件中的表达式会被执行。一些表达式使用数字字面量，另一些则使用绑定变量，这些变量的值通过引擎作用域和引擎默认 `ScriptContext` 的全局作用域中的绑定传入。请注意，该程序期望在当前目录下存在一个名为 `jkscript.txt` 的文件，该文件包含一个 `JKScript` 脚本引擎可以理解的算术表达式。如果脚本文件不存在，程序会在标准输出上打印一条消息，其中包含预期脚本文件的路径。最后一行输出可能有所不同。

清单 8-5. 使用 JKScript 脚本引擎

`// JKScriptTest.java`

`package com.jdojo.script;`

`import java.io.FileNotFoundException;`

`import java.io.IOException;`

`import java.io.Reader;`

`import java.nio.file.Files;`

`import java.nio.file.Path;`

`import java.nio.file.Paths;`

`import javax.script.ScriptEngine;`

`import javax.script.ScriptEngineManager;`

`import javax.script.ScriptException;`

`public class JKScriptTest {`

`public static void main(String[] args) throws FileNotFoundException, IOException {`

`// 创建 JKScript 引擎`

`ScriptEngineManager manager = new ScriptEngineManager();`

`ScriptEngine engine = manager.getEngineByName("JKScript");`

`if (engine == null) {`

`System.out.println("JKScript 引擎不可用。" +`

`"请将 jkscript.jar 添加到 CLASSPATH。");`

`return;`

`}`

`// 测试作为字符串的脚本`

`testString(manager, engine);`

`// 测试作为读取器的脚本`

`testReader(manager, engine);`

`}`

`public static void testString(ScriptEngineManager manager, ScriptEngine engine) {`

`try {`

`// 使用带数字字面量的简单表达式`

`String script = "12.8 + 15.2";`

`Object result = engine.eval(script);`

`System.out.println(script + " = " + result);`

`script = "-90.0 - -10.5";`

`result = engine.eval(script);`

`System.out.println(script + " = " + result);`

`script = "5 * 12";`

`result = engine.eval(script);`

`System.out.println(script + " = " + result);`

`script = "56.0 / -7.0";`

`result = engine.eval(script);`

`System.out.println(script + " = " + result);`

`// 使用全局作用域绑定变量`

`manager.put("num1", 10.0);`

`manager.put("num2", 20.0);`

`script = "num1 + num2";`

`result = engine.eval(script);`

`System.out.println(script + " = " + result);`

`// 使用全局和引擎作用域绑定。将使用来自`
`// 引擎作用域的 num1 和来自全局作用域的 num2。`

`engine.put("num1", 70.0);`

`script = "num1 + num2";`

`result = engine.eval(script);`

`System.out.println(script + " = " + result);`

`// 尝试混合数字字面量和绑定。将使用来自引擎作用域绑定的 num1`

`script = "10 + num1";`

`result = engine.eval(script);`

`System.out.println(script + " = " + result);`

`}`

`catch (ScriptException e) {`

`e.printStackTrace();`

`}`

`}`

`public static void testReader(ScriptEngineManager manager, ScriptEngine engine) {`

`try {`

`Path scriptPath = Paths.get("jkscript.txt").toAbsolutePath();`

`if (!Files.exists(scriptPath)) {`

`System.out.println(scriptPath +`

`" 脚本文件不存在。");`

`return;`

`}`

`try(Reader reader = Files.newBufferedReader(scriptPath);) {`

`Object result = engine.eval(reader);`

`System.out.println("结果 " +`

`scriptPath + " = " + result);`

`}`

`}`

`catch(ScriptException | IOException e) {`

`e.printStackTrace();`

`}`

`}`

`}`

`12.8 + 15.2 = 28.0`

`-90.0 - -10.5 = -79.5`

`5 * 12 = 60.0`

`56.0 / -7.0 = -8.0`

`num1 + num2 = 30.0`

`num1 + num2 = 90.0`

`10 + num1 = 80.0`

`结果 C:\jkscript.txt = 190.0`

## 总结

你可以使用 Java 脚本 API 实现一个脚本引擎。你需要为 `ScriptEngine` 和 `ScriptEngineFactory` 接口提供实现。你需要以特定方式打包你的脚本引擎代码，以便 `ScriptManager` 在运行时能够发现该引擎。引擎的 JAR 文件应包含一个名为 `META-INF\services\javax.script.ScriptEngineFactory` 的文件，该文件应包含所有脚本引擎工厂类的完全限定名；Java 脚本 API 会自动发现这些脚本引擎工厂。一旦你打包并部署了脚本引擎代码，你就可以像访问 Nashorn 和其他脚本引擎一样访问它。

