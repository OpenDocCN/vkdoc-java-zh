# 2. 执行脚本

在本章中，你将学习：

*   如何使用 `ScriptEngine` 的 `eval()` 方法执行脚本
*   如何将参数从 Java 代码传递给 Nashorn 引擎
*   如何将参数从 Nashorn 引擎传递给 Java

## 使用 eval() 方法

`ScriptEngine` 可以执行 `String` 和 `Reader` 中的脚本。使用 `Reader`，你可以执行存储在网络或文件中的脚本。使用 `ScriptEngine` 接口的 `eval()` 方法的以下版本之一来执行脚本：

*   `Object eval(String script)`
*   `Object eval(Reader reader)`
*   `Object eval(String script, Bindings bindings)`
*   `Object eval(Reader reader, Bindings bindings)`
*   `Object eval(String script, ScriptContext context)`
*   `Object eval(Reader reader, ScriptContext context)`

`eval()` 方法的第一个参数是脚本的源代码。第二个参数允许你将信息从宿主应用程序传递到脚本引擎，这些信息可以在脚本执行期间使用。

在第 1 章中，你看到了如何使用 `String` 对象通过 `eval()` 方法的第一个版本来执行脚本。在本章中，你将把脚本存储在文件中，并使用 `Reader` 对象作为脚本的源代码，这将使用 `eval()` 方法的第二个版本。下一节将讨论 `eval()` 方法的其他四个版本。通常，脚本文件的扩展名为 `.js`。

清单 2-1 显示了一个名为 `helloscript.js` 的文件的内容。它只包含一条 Nashorn 语句，用于在标准输出上打印一条消息。

清单 2-1. helloscript.js 文件的内容

`// helloscript.js`

`// 打印一条消息`

`print('来自 JavaScript 的问候！');`

清单 2-2 包含一个 Java 程序，该程序执行存储在 `helloscript.js` 文件中的脚本，该文件应存储在当前目录中。如果未找到脚本文件，程序将打印期望的 `helloscript.js` 文件的完整路径。如果在执行脚本文件时遇到问题，请尝试在 `main()` 方法中使用绝对路径，例如在 Windows 上使用 `C:\scripts\helloscript.js`，假设 `helloscript.js` 文件保存在 `C:\scripts` 目录中。

清单 2-2. 执行存储在文件中的脚本

`// ReaderAsSource.java`

`package com.jdojo.script;`

`import java.io.IOException;`

`import java.io.Reader;`

`import java.nio.file.Files;`

`import java.nio.file.Path;`

`import java.nio.file.Paths;`

`import javax.script.ScriptEngine;`

`import javax.script.ScriptEngineManager;`

`import javax.script.ScriptException;`

`public class ReaderAsSource {`

`public static void main(String[] args) {`

`// 构造脚本文件路径`

`String scriptFileName = "helloscript.js";`

`Path scriptPath = Paths.get(scriptFileName);`

`// 确保脚本文件存在。如果不存在，则打印脚本文件的完整路径并终止程序。`

`if (! Files.exists(scriptPath) ) {`

`System.out.println(scriptPath.toAbsolutePath() +`

`" 不存在。");`

`return;`

`}`

`// 获取 Nashorn 脚本引擎`

`ScriptEngineManager manager = new ScriptEngineManager();`

`ScriptEngine engine = manager.getEngineByName("JavaScript");`

`try {`

`// 为脚本文件获取一个 Reader`

`Reader scriptReader = Files.                        newBufferedReader(scriptPath);`

`// 执行文件中的脚本`

`engine.eval(scriptReader);`

`}`

`catch (IOException | ScriptException e) {`

`e.printStackTrace();`

`}`

`}`

`}`

`来自 JavaScript 的问候！`

在现实世界的应用程序中，你应该将所有脚本存储在文件中，这样可以在不修改和重新编译 Java 代码的情况下修改脚本。在本章的大多数示例中，你不会遵循此规则；为了保持代码简短和简单，你会将脚本存储在 `String` 对象中。

## 传递参数

Java 脚本 API 允许你将参数从宿主环境（Java 应用程序）传递到脚本引擎，反之亦然。在本节中，你将看到宿主应用程序和脚本引擎之间参数传递机制的技术细节。有多种方法可以从 Java 程序向脚本传递参数。在本章中，我将解释参数传递的最简单形式。我将在第 3 章中讨论所有其他形式。



### 从 Java 代码向脚本传递参数

Java 程序可以向脚本传递参数。Java 程序也可以在脚本执行后访问脚本中声明的全局变量。我们来讨论一个简单的例子，展示 Java 程序如何向脚本传递参数。请参考清单 2-3 中的程序，该程序向脚本传递了一个参数。

清单 2-3\. 从 Java 程序向脚本传递参数

`// PassingParam.java`

`package com.jdojo.script;`

`import javax.script.ScriptEngine;`

`import javax.script.ScriptEngineManager;`

`import javax.script.ScriptException;`

`public class PassingParam {`

`public static void main(String[] args) {`

`// 获取 Nashorn 引擎`

`ScriptEngineManager manager = new ScriptEngineManager();`

`ScriptEngine engine = manager.getEngineByName("JavaScript");`

`// 将脚本存储在一个字符串中。这里，msg 是一个`

`// 我们尚未在脚本中声明的变量`

`String script = "print(msg)";`

`try {`

`// 在引擎中存储一个名为 msg 的参数`

`engine.put("msg", "Hello from Java program");`

`// 执行脚本`

`engine.eval(script);`

`}`

`catch (ScriptException e) {`

`e.printStackTrace();`

`}`

`}`

`}`

`Hello from Java program`

该程序将脚本存储在一个 `String` 对象中，如下所示：

`// 将 Nashorn 脚本存储在一个 String 对象中`

`String script = "print(msg)";`

在这个语句中，脚本是：

`print(msg)`

请注意，`msg` 是在 `print()` 函数调用中使用的一个变量。该脚本并未声明 `msg` 变量或为其赋值。如果你尝试执行上述脚本而不告知引擎 `msg` 变量是什么，引擎将抛出一个异常，指出它无法理解变量 `msg` 的含义。这正是从 Java 程序向脚本引擎传递参数这一概念发挥作用的地方。

你可以通过多种方式向脚本引擎传递参数。最简单的方法是使用脚本引擎的 `put(String paramName, Object paramValue)` 方法，该方法接受两个参数：

*   第一个参数是参数的名称，需要与脚本中的变量名匹配。
*   第二个参数是参数的值。

在你的例子中，你想向脚本引擎传递一个名为 `msg` 的参数，其值是一个 `String`。调用 `put()` 方法的代码如下：

`// 在引擎中存储 msg 参数的值`

`engine.put("msg", "Hello from Java program");`

请注意，你必须在调用 `eval()` 方法之前调用引擎的 `put()` 方法。在你的例子中，当引擎尝试执行 `print(msg)` 时，它将使用你传递给引擎的 `msg` 参数的值。

大多数脚本引擎允许你将传递给它的参数名称用作脚本中的变量名。你在清单 2-3 中传递名为 `msg` 的参数值并将其用作脚本中的变量名时，就看到了这种例子。脚本引擎可能对在脚本中声明变量有特定要求，例如，在 PHP 中变量名必须以 `$` 前缀开头，在 JRuby 中全局变量名包含 `$` 前缀。如果你想向 JRuby 中的脚本传递一个名为 `msg` 的参数，你的代码将如下所示：

`// 获取 JRuby 脚本引擎`

`ScriptEngineManager manager = new ScriptEngineManager();`

`ScriptEngine engine = manager.getEngineByName("jruby");`

`// 在 JRuby 脚本中必须使用 $ 前缀`

`String script = "puts($msg)";`

`// 向 JRuby 引擎传递 msg 参数时未使用 $ 前缀`

`engine.put("msg", "Hello from Java");`

`// 执行脚本`

`engine.eval(script);`

传递给脚本的 Java 对象的属性和方法可以在脚本中访问，就像在 Java 代码中访问它们一样。不同的脚本语言使用不同的语法来访问脚本中的 Java 对象。例如，你可以在清单 2-3 所示的示例中使用表达式 `msg.toString()`，输出结果将相同。在这种情况下，你正在调用变量 `msg` 的 `toString()` 方法。将清单 2-3 中为 `script` 变量赋值的语句更改为以下内容并运行程序，将产生相同的输出：

`String script = "println(msg.toString())";`

### 从脚本向 Java 代码传递参数

脚本引擎可能使其全局作用域中的变量对 Java 代码可访问。`ScriptEngine` 的 `get(String variableName)` 方法用于在 Java 代码中访问这些变量。它返回一个 Java `Object`。全局变量的声明方式取决于脚本语言。以下代码片段在 JavaScript 中声明了一个全局变量并为其赋值：

`// 在 Nashorn 中声明一个名为 year 的变量`

`var year = 1969;`

清单 2-4 包含一个程序，展示了如何从 Java 代码访问 Nashorn 中的全局变量。

清单 2-4\. 在 Java 代码中访问脚本全局变量

`// AccessingScriptVariable.java`

`package com.jdojo.script;`

`import javax.script.ScriptEngine;`

`import javax.script.ScriptEngineManager;`

`import javax.script.ScriptException;`

`public class AccessingScriptVariable {`

`public static void main(String[] args) {`

`// 获取 Nashorn 引擎`

`ScriptEngineManager manager = new ScriptEngineManager();`

`ScriptEngine engine = manager.getEngineByName("JavaScript");`

`// 编写一个脚本，声明一个名为 year 的全局变量`

`// 并为其赋值 1969。`

`String script = "var year = 1969";`

`try {`

`// 执行脚本`

`engine.eval(script);`

`// 从引擎获取 year 全局变量`

`Object year = engine.get("year");`

`// 打印变量 year 的类名和值`

`System.out.println("year's class:" + year. getClass().getName());`

`System.out.println("year's value:" + year);`

`}`

`catch (ScriptException e) {`

`e.printStackTrace();`

`}`

`}`

`}`

`year's class:java.lang.Integer`

`year's value:1969`

该程序在脚本中声明了一个全局变量 `year` 并为其赋值 1969，如下所示：

`String script = "var num = 1969";`

当脚本执行时，引擎将 `year` 变量添加到其状态中。在 Java 代码中，使用引擎的 `get()` 方法来检索 `year` 变量的值，如下所示：

`Object year = engine.get("year");`

当在脚本中声明 `year` 变量时，你并未指定其数据类型。脚本变量值到适当 Java 对象的转换是自动执行的。如果你在 Java 7 中运行该程序，输出将显示 `java.lang.Double` 作为类名，1960.0 作为 `year` 变量的值。这是因为 Java 7 使用 Rhino 脚本引擎，它将 1969 解释为 `Double`，而 Java 8 使用 Nashorn 脚本引擎，将其解释为 `Integer`。

## 总结

`ScriptEngine` 可以执行 `String` 和 `Reader` 中的脚本。`ScriptEngine` 的 `eval()` 方法用于执行脚本。该方法已被重载，共有六个版本。它允许你将脚本作为第一个参数传递，并将参数作为第二个参数传递给引擎。

`ScriptEngine` 的 `eval()` 方法用于执行脚本。你可以向脚本传递参数，并从脚本中读取值返回给 Java 程序。有多种方式可以从 Java 程序向脚本传递参数。你可以使用 `ScriptEngine` 的 `put(String key, Object value)` 方法向脚本传递一个名为 key 的参数。你可以使用 `get(String key)` 方法获取脚本中存储的名为 `key` 的全局变量。



