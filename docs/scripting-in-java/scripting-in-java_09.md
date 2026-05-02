# 9. jrunscript 命令行 Shell

在本章中，你将学习：

*   什么是 `jrunscript` 命令行 Shell
*   如何调用 `jrunscript` 命令行 Shell
*   `jrunscript` 命令行 Shell 可以调用的不同模式
*   如何使用 `jrunscript` 命令行 Shell 列出可用的脚本引擎
*   如何向 `jrunscript` 命令行 Shell 添加脚本引擎
*   如何向 `jrunscript` 命令行 Shell 传递参数
*   `jrunscript` 命令行 Shell 提供的全局函数

JDK 包含一个名为 `jrunscript` 的命令行脚本 Shell。它与脚本引擎无关，可用于评估任何脚本，包括你在第 8 章中开发的 `JKScript`。你可以在 `JAVA_HOME\bin` 目录中找到此 Shell，其中 `JAVA_HOME` 是你安装 JDK 的目录。在本节中，我将讨论如何使用 `jrunscript` Shell 来使用不同的脚本引擎评估脚本。



## 语法

使用 `jrunscript` shell 的语法如下：

`jrunscript [options] [arguments]`

`[options]` 和 `[arguments]` 都是可选的。但是，如果同时指定了这两者，`[options]` 必须位于 `[arguments]` 之前。表 9-1 列出了 `jrunscript` shell 的所有可用选项。

表 9-1.

jrunscript Shell 的选项列表

| 选项 | 描述 |
| --- | --- |
| `-classpath <path>` | 用于指定 CLASSPATH。 |
| `-cp <path>` | 与 `-classpath` 选项相同。 |
| `-D<name>=<value>` | 为 Java 运行时设置系统属性。 |
| `-J<flag>` | 将指定的 `<flag>` 传递给运行 `jrunscript` 的 JVM。 |
| `-l <language>` | 允许你指定要与 `jrunscript` 一起使用的脚本语言。默认情况下，JDK 6 和 JDK 7 使用 Rhino JavaScript。在 JDK 8 中，Nashorn 是默认引擎。如果你想使用 JavaScript 以外的语言，例如 `JKScript`，则需要使用 `-cp` 或 `-classpath` 选项来包含包含该脚本引擎的 JAR 文件。 |
| `-e <script>` | 执行指定的脚本。通常用于执行单行脚本。 |
| `-encoding <encoding>` | 指定读取脚本文件时使用的字符编码。 |
| `-f <script-file>` | 以批处理模式执行指定的 `script-file`。 |
| `-f -` | 允许你在交互模式下执行脚本。它从标准输入读取脚本并执行。 |
| `-help` | 输出帮助信息并退出。 |
| `-?` | 输出帮助信息并退出。 |
| `-q` | 列出所有可用的脚本引擎并退出。请注意，只有当你使用 `-cp` 或 `-classpath` 选项包含了它们的 JAR 文件时，JavaScript 以外的脚本引擎才可用。 |

命令的 `[arguments]` 部分是一个参数列表，其解释方式取决于是否使用了 `-e` 或 `-f` 选项。传递给脚本的参数在脚本内部作为一个名为 `arguments` 的对象可用。关于在脚本中使用 `arguments` 对象的更多细节，请参考第 4 章。表 9-2 列出了当参数与 `-e` 或 `-f` 选项一起使用时，这些参数的解释方式。

表 9-2.

结合 -e 或 -f 选项时 [arguments] 的解释

| -e 或 -f 选项 | 参数 | 解释 |
| --- | --- | --- |
| `是` | `是` | 如果指定了 `-e` 或 `-f` 选项，所有参数都将作为脚本参数传递给脚本。 |
| `否` | `是` | 如果指定了参数但没有 `-e` 或 `-f` 选项，则第一个参数被视为要运行的脚本文件。其余参数（如果有）将作为脚本参数传递给脚本。 |
| `否` | `否` | 如果缺少参数和 `-e` 或 `-f` 选项，shell 将以交互模式工作，在该模式下，shell 会交互式地执行在标准输入中输入的脚本。 |

## Shell 的执行模式

你可以在以下三种模式下使用 `jrunscript` shell：

*   单行模式
*   批处理模式
*   交互模式

### 单行模式

`-e` 选项允许你在单行模式下使用 shell。它执行一行脚本。以下命令使用 Nashorn 引擎在标准输出上打印一条消息：

`C:\>jrunscript -e "print('Hello Nashorn!');"`

`Hello Nashorn!`

`C:\>`

在单行模式下，整个脚本必须在一行内输入。但是，一个单行脚本可以包含多条语句。

### 批处理模式

`-f` 选项允许你在批处理模式下使用 shell。它执行一个脚本文件。考虑一个名为 `jrunscripttest.js` 的脚本文件，如清单 9-1 所示。

清单 9-1. 用 Nashorn JavaScript 编写的 jrunscripttest.js 脚本文件

`// jrunscripttest.js`

`// 打印一条消息`

`print("Hello Nashorn!");`

`// 将两个整数相加并打印结果`

`var x = 10;`

`var y = 20;`

`var z = x + y;`

`printf("x + y = z", x, y, z);`

以下命令以批处理模式运行 `jrunscripttest.js` 文件中的脚本。如果 `jrunscripttest.js` 文件不在当前目录中，你可能需要指定其完整路径。

`C:\>jrunscript -f jrunscripttest.js`

`Hello Nashorn!`

`10 + 20 = 30`

`C:\>`

### 交互模式

在交互模式下，shell 会读取并执行在标准输入中输入的脚本。有两种方法可以在交互模式下使用 shell：

*   不使用 `-e` 或 `-f` 选项，也不使用参数
*   使用 "`-f -`" 选项

以下命令不使用任何选项和参数进入交互模式。按 Enter 键会使 shell 执行输入的脚本。请注意，你需要执行 `exit()` 或 `quit()` 函数来退出交互模式：

`c:\>jrunscript`

`nashorn> print("Hello Interactive mode!");`

`Hello Interactive mode!`

`nashorn> var num = 190;`

`nashorn> print("num is " + num);`

`num is 190`

`nashorn> exit();`

`C:\>`

## 列出可用的脚本引擎

`jrunscript` shell 是一个脚本语言中立的 shell。你可以使用它来运行任何脚本语言的脚本，只要该脚本引擎的 JAR 文件可用。默认情况下，Nashorn JavaScript 引擎是可用的。要列出所有可用的脚本引擎，请使用 `-q` 选项，如下所示：

`c:\>jrunscript -q`

`Language ECMAScript ECMA - 262 Edition 5.1 implementation "Oracle Nashorn" 1.8.0_05`

请参考下一节，了解如何向 shell 添加脚本引擎。

## 向 Shell 添加脚本引擎

如何使 Nashorn 引擎以外的脚本引擎对 shell 可用？要使脚本引擎对 shell 可用，你需要使用 `-classpath` 或 `-cp` 选项提供该脚本引擎的 JAR 文件列表。以下命令通过为 `Jython` 和 `JKScript` 引擎提供 JAR 文件列表，使 `JKScript` 和 `jython` 脚本引擎对 shell 可用。请注意，Nashorn 引擎默认始终可用。该命令使用 `-q` 选项列出所有可用的脚本引擎：

`c:\> jrunscript -cp C:\jython-standalone-2.5.3.jar;C:\jkscript.jar -q`

`Language python 2.5 implementation "jython" 2.5.3`

`Language ECMAScript ECMA - 262 Edition 5.1 implementation "Oracle Nashorn" 1.8.0_05`

`Language JKScript 1.0 implementation "JKScript Engine" 1.0`

提示

使用 `-cp` 或 `-classpath` 选项设置的 CLASSPATH 仅对使用该选项的命令有效。如果你在交互模式下运行 shell，则该 CLASSPATH 在整个交互会话期间有效。

## 使用其他脚本引擎

你可以通过使用 `-l` 选项指定脚本引擎名称来使用其他脚本引擎。你必须使用 `-cp` 或 `-classpath` 选项来指定该脚本引擎的 JAR 文件，以便 shell 能够访问该引擎。以下命令在交互模式下使用 `JKScript` 引擎：

`C:\>jrunscript -cp C:\jkscript.jar -l JKScript`

`jks> 10 + 30`

`40.0`

`jks> +89.7 + -9.7`

`80.0`

`jks>`



## 向脚本传递参数

`jrunscript` shell 允许向脚本传递参数。这些参数通过一个名为 `arguments` 的类数组对象提供给脚本。你可以使用特定于语言的方式在脚本内部访问 `arguments` 数组。以下命令传递了三个参数 10、20 和 30，并打印第一个参数的值：

`C:\>jrunscript -e "print('第一个参数是 ' + arguments[0])" 10 20 30`

`第一个参数是 10`

请考虑清单 9-2 中所示的 Nashorn JavaScript 文件 `nashornargstest.js`，该文件会打印传递给脚本的参数数量及其值。

清单 9-2\. 一个用 Nashorn JavaScript 编写的 nashornargstest.js 文件，用于打印命令行参数

`// nashornargstest.js`

`print("参数数量:" + arguments.length);`

`print("参数为 ") ;`

`for(var i = 0; i < arguments.length; i++) {`

`print(arguments[i]);`

`}`

以下命令使用 `jrunscript` shell 运行 `nashornargstest.js` 文件：

`C:\>jrunscript nashornargstest.js`

`参数数量:0`

`参数为`

`C:\>jrunscript nashornargstest.js 10 20 30`

`参数数量:3`

`参数为`

`10`

`20`

`30`

如果你想从 Java 应用程序运行 `nashornargstest.js` 文件，则需要向引擎传递一个名为 `arguments` 的参数。名为 `arguments` 的参数是由 shell 自动传递给脚本的，而不是由 Java 应用程序传递的。

## 全局函数

`jrunscript` 命令行 shell 提供了几个可用的全局函数，如表 9-3 所列。

表 9-3.

jrunscript 命令行 shell 加载的全局对象列表

| 函数 | 描述 |
| --- | --- |
| `cat(path, pattern)` | 显示由 `path` 指定的文件、URL 或 InputStream 的内容。可选地，你可以指定 `pattern` 来仅显示匹配的内容。 |
| `cd(target)` | 将当前工作目录更改为 `target` 目录。 |
| `cp(from, to)` | 将文件、URL 或流复制到另一个文件或流。 |
| `date()` | 使用当前区域设置打印当前日期。 |
| `del(pathname)` | `rm` 命令的同义词。 |
| `dir(d, filter)` | `ls` 命令的同义词。 |
| `dirname(pathname)` | 返回指定 `pathname` 的目录部分。 |
| `echo(str)` | 回显指定的字符串参数。 |
| `exec(cmd)` | 启动一个子进程，执行指定的命令，等待其完成，并返回退出码。 |
| `exit(code)` | 以指定的 `code` 作为退出码退出 shell 程序。 |
| `find(dir, pattern, callback)` | 在 `dir` 中查找文件名与指定 `pattern` 匹配的文件。找到匹配项时，会调用 `callback` 函数并传递找到的文件。搜索会递归地在所有子目录中执行。你可以将本表中列出的一些函数作为 `callback` 传递。如果未指定 `callback`，则默认打印找到的文件路径。如果未指定 `pattern`，则打印所有文件。 |
| `grep(pattern, files)` | 类 Unix 的 `grep`，但接受 JavaScript 正则表达式模式。 |
| `ip(name)` | 打印给定域名的 IP 地址。 |
| `load(path)` | 从流、文件或 URL 加载并评估 JavaScript 代码。 |
| `ls(dir, filter)` | 列出 `dir` 中与 `filter` 正则表达式匹配的文件。 |
| `mkdir(dir)` | 创建一个名为 `dir` 的新目录。 |
| `mkdirs(dir)` | 创建一个名为 `dir` 的目录，包括任何必要但尚不存在的父目录。 |
| `mv(from, to)` | 将文件移动到另一个目录。 |
| `printf(format, args)` | 类 C 的 printf。 |
| `pwd()` | 打印工作目录。 |
| `quit(code)` | `exit(code)` 的同义词。 |
| `read(prompt, multiline)` | 在打印指定的 `prompt` 后，从标准输入读取并返回一行或多行。默认提示符是 `>`。如果 `multiline` 为 0，则读取一行。如果 `multiline` 不为 0，则读取多行。你需要按 `Enter` 键停止输入文本。 |
| `ren(from, to)` | `mv` 的同义词。 |
| `rm(filePath)` | 删除具有指定 `filePath` 的文件。 |
| `rmdir(dirPath)` | 删除具有指定 `dirPath` 的目录。 |
| `which(cmd)` | 类 Unix 的 `which` 命令，用于根据 PATH 环境变量打印指定 `cmd` 命令的路径。 |
| `XMLDocument(input)` | 将可以是文件路径或 `Reader` 的 `input` 转换为 DOM Document 对象。如果未指定 `input`，则返回一个空的 DOM 文档。 |
| `XMLResult(input)` | 将任意流或文件转换为 `XMLResult`。如果 `input` 是 `javax.xml.transform.Result` 的实例，则返回 `input`；如果 `input` 是 `org.w3c.dom.Document` 的实例，则返回 `javax.xml.transform.dom.DOMResult`；否则，返回 `javax.xml.transform.stream.StreamResult`。 |
| `XMLSource(input)` | 将任意流、文件、URL 转换为 `XMLSource`。如果 `input` 是 `javax.xml.transform.Source` 的实例，则返回 `input`；如果 `input` 是 `org.w3c.dom.Document` 的实例，则返回 `javax.xml.transform.dom.DOMSource`；否则，返回 `javax.xml.transform.stream.StreamSource`。 |
| `XSLTransform(input, style, output)` | 执行 XSLT 转换；`input` 是输入的 XML；`style` 是 XML 样式表；`output` 是输出的 XML。`Input` 和 `style` 可以是 `URL`、`File` 或 `InputStream`；output 可以是 `File` 或 `OutputStream`。 |

以下是使用 `jrunscript` 提供的一些实用函数输出的结果：

`C:\>jrunscript`

`nashorn> cat("` [`http://jdojo.com/about`](http://jdojo.com/about) `", "ksharan")`



`68      : <p>您可以通过电子邮件联系 Kishori Sharan，邮箱地址为 <a href="mailto:ksharan@jdojo.com">ksharan@jdojo.com</a>。</p>`

`nashorn> var addr = read("请输入您的地址: ", 1);`

`请输入您的地址: 9999 Main St.`

`请输入您的地址: Dreamland, HH 11111`

`请输入您的地址:`

`nashorn> print(addr)`

`9999 Main St.`

`Dreamland, HH 11111`

`nashorn> which("jrunscript.exe");`

`c:\JAVA8\BIN\jrunscript.exe`

`nashorn>pwd()`

`C:\`

`nashorn>`

这些实用函数大多是利用 Java 类库编写的 Nashorn 脚本。了解这些函数工作原理的最佳方式是阅读其源代码。您可以通过在 `nashorn` 命令提示符下直接输入函数名来打印非原生函数的源代码。以下命令序列展示了如何打印 `exec(cmd)` 函数的源代码。输出显示该函数在内部使用了 Java 的 `Runtime` 类来运行命令：

`c:\>jrunscript`

`nashorn> exec`

`function exec(cmd) {`

`var process = java.lang.Runtime.getRuntime().exec(cmd);`

`var inp = new DataInputStream(process.getInputStream());`

`var line = null;`

`while ((line = inp.readLine()) != null) {`

`println(line);`

`}`

`process.waitFor();`

`$exit = process.exitValue();`

`}`

`nashorn> exit()`

`c:\>`

`jrunscript` 还提供了另外三个值得提及的全局函数。这些函数既可以作为函数使用，也可以作为构造函数使用：

*   `jlist(javaList)`
*   `jmap(javaMap)`
*   `JSInvoker(object)`

`jlist()` 函数接收一个 `java.util.List` 实例，并返回一个 JavaScript 对象，您可以使用该对象像访问数组一样访问 `List`。您可以使用带索引的方括号表示法来访问 `List` 的元素。返回的对象包含一个 `length` 属性，用于提供 `List` 的大小。清单 9-3 包含了展示如何使用 `jlist()` 函数的代码。

清单 9-3\. 使用 jlist() 函数

`// jlisttest.js`

`// 创建一个 ArrayList 并向其中添加两个元素`

`var ArrayList = Java.type("java.util.ArrayList");`

`var list = new ArrayList();`

`list.add("Ken");`

`list.add("Li");`

`// 将 ArrayList 转换为 Nashorn 数组`

`var names = jlist(list);`

`print("将 ArrayList 作为 Nashorn 数组访问...");`

`for(var i = 0; i < names.length; i++) {`

`printf("names[%d] = %s", i, names[i]);`

`}`

以下命令使用 `jrunscript` 命令行 shell 执行了清单 9-3 中的代码：

`C:\>jrunscript -f jlisttest.js`

`将 ArrayList 作为 Nashorn 数组访问...`

`names[0] = Ken`

`names[1] = Li`

`jmap()` 函数接收一个 `java.util.Map` 实例，并返回一个 JavaScript 对象，您可以使用该对象来访问 `Map`。`Map` 中的键将成为 JavaScript 对象的属性。清单 9-4 包含了展示如何使用 `jmap()` 函数的代码。

清单 9-4\. 使用 jmap() 函数

`// jmaptest.js`

`// 创建一个 HashMap 并向其中添加两个元素`

`var HashMap = Java.type("java.util.HashMap");`

`var map = new HashMap();`

`map.put("Ken", "(999) 777-3331");`

`map.put("Li", "(888) 444-1111");`

`// 将 HashMap 转换为 Nashorn 对象`

`var phoneDir = jmap(map);`

`print("将 HashMap 作为 Nashorn 对象访问...");`

`for(var prop in phoneDir) {`

`printf("phoneDir['%s'] = %s", prop, phoneDir[prop]);`

`}`

`// 使用点号表示法访问属性`

`var kenPhone = phoneDir.Ken; // 等同于 phoneDir["Ken"]`

`printf("phoneDir.Ken = %s", kenPhone)`

以下命令使用 `jrunscript` 命令行 shell 执行了清单 9-4 中的代码：

`C:\>jrunscript -f jmaptest.js`

`将 HashMap 作为 Nashorn 对象访问...`

`phoneDir['Ken'] = (999) 777-3331`

`phoneDir['Li'] = (888) 444-1111`

`phoneDir.Ken = (999) 777-3331`

`JSInvoker()` 函数接收一个委托对象作为参数。当在 `JSInvoker` 对象上调用一个函数时，会在委托对象上调用 `invoke(name, args)` 方法。被调用函数的名称作为第一个参数传递给 `invoke()` 方法；传递给函数调用的参数作为第二个参数传递给 `invoke()` 方法。清单 9-5 展示了如何使用 `JSInvoker` 对象。

清单 9-5\. 使用 JSInvoker 对象

`// jsinvokertest.js`

`var calcDelegate = { invoke: function(name, args) {`

`if (args.length !== 2) {`

`throw new Error("必须向 " + name + " 传递 2 个参数");`

`}`

`var value = 0;`

`if (name === "add")`

`value = args[0] + args[1];`

`else if (name === "subtract")`

`value = args[0] - args[1];`

`else if (name === "multiply")`

`value = args[0] * args[1];`

`else if (name === "divide")`

`value = args[0] / args[1];`

`else`

`throw new Error("不支持 " + name + " 操作。");`

`return value;`

`}`

`};`

`var calc = new JSInvoker(calcDelegate);`

`var x = 20.44, y = 30.56;`

`var addResult = calc.add(x, y); // 将调用 calcDelegate.invoke("add", [x, y])`

`var subResult = calc.subtract(x, y);`

`var mulResult = calc.multiply(x, y);`

`var divResult = calc.divide(x, y);`

`printf("calc.add(%.2f, %.2f) = %.2f%n", x, y, addResult);`

`printf("calc.sub(%.2f, %.2f) = %.2f%n", x, y, subResult);`

`printf("calc.mul(%.2f, %.2f) = %.2f%n", x, y, mulResult);`

`printf("calc.div(%.2f, %.2f) = %.2f", x, y, divResult);`

该代码创建了一个名为 `calcDelegate` 的对象，其中包含一个 `invoke()` 方法。`JSInvoker` 对象包装了 `calcDelegate` 对象。当在 `calc` 对象上调用一个函数时，`calcDelegate` 对象的 `invoke()` 方法会被调用，函数名作为其第一个参数，函数参数作为数组作为其第二个参数。`invoke()` 函数对参数执行加法、减法、乘法和除法运算。以下命令展示了如何执行该代码：

`c:\>jrunscript -f jsinvokertest.js`

`calc.add(20.44, 30.56) = 51.00`

`calc.sub(20.44, 30.56) = -10.12`

`calc.mul(20.44, 30.56) = 624.65`

`calc.div(20.44, 30.56) = 0.67`

`JSInvoker` 对象在 Java 7 中可以正常工作，但在 Java 8 中运行此示例时会产生以下错误。这似乎是 Java 8 中引入的一个错误：

`c:\>jrunscript -f jsinvokertest.js`

`script error in file jsinvoker.js : TypeError: [object JSAdapter] has no such function "add" in jsinvoker.js at line number 25`

`jrunscript` shell 还为几个 Java 类创建了别名，例如 `java.io.File`、`java.io.Reader`、`java.net.URL` 等，因此您可以通过它们的简单名称来引用它们。`jrunscript` 还将其他几个对象暴露为全局对象。您可以使用以下命令在命令行上打印全局对象及其类型的完整列表。这里仅显示了部分输出。请注意，输出还将包含一个名为 `p` 的属性，这是在 `for` 循环中声明的变量名。

`c:\>jrunscript`

`nashorn> for(var p in this) print(p, typeof this[p]);`

`engine object`

`JSInvoker function`

`jmap function`

`jlist function`

`inStream function`

`outStream function`

`streamClose function`

`javaByteArray function`

`pwd function`

`...`

`nashorn>exit()`

`c:\`



## 摘要

JDK 包含一个独立于脚本引擎的命令行 shell，名为 `jrunscript`。它可用于评估在命令行或文件中输入的脚本。你可以在 `JAVA_HOME\bin` 目录中找到此 shell，其中 `JAVA_HOME` 是你安装 JDK 的目录。

`jrunscript` 命令行 shell 可以运行由 Java 支持的任何脚本语言编写的脚本。默认情况下，它运行 Nashorn 脚本。要使用 Nashorn 以外的脚本语言，你需要使用 `–cp` 或 `–classpath` 选项将语言的 JAR 文件与 `jrunscript` 一起包含。`–l` 选项允许你选择要使用的脚本语言。

你可以在单行模式、批处理模式和交互模式下使用 `jrunscript`。单行模式允许你执行一行脚本。使用 `–e` 选项调用单行模式。批处理模式允许你执行存储在文件中的脚本。使用 `–f` 选项调用批处理模式。交互模式允许你交互式地执行在命令行输入的脚本。通过不使用 `–e` 和 `–f` 选项，或使用 `–f –` 选项（注意 `–f` 后跟一个 `–`）来调用交互模式。

你可以使用 `–q` 选项列出 `jrunscript` 所有可用的脚本引擎。请注意，你必须包含 Nashorn 以外的语言的脚本引擎 JAR 文件，才能使它们与 `jrunscript` 一起使用。`jrunscript` shell 提供了几个有用的全局函数和对象。例如，`cat()` 函数可用于打印文件或 URL 的内容，并可选择应用过滤器。

