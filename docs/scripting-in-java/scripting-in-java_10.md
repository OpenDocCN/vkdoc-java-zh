# 10. jjs 命令行工具

在本章中，你将学习：

*   什么是 `jjs` 命令行工具
*   如何调用 `jjs` 命令行工具
*   如何向 `jjs` 命令行工具传递参数
*   如何在交互模式和脚本模式下使用 `jjs` 命令行工具

为了使用 Nashorn 脚本引擎，JDK 8 包含了一个名为 `jjs` 的新命令行工具。如果你想知道 `jjs` 代表什么，它代表 Java JavaScript。该命令位于 `JDK_HOME\bin` 目录中。该命令可用于运行文件中的脚本或在交互模式下在命令行输入的脚本。它也可用于执行 shell 脚本。

## 语法

调用该命令的语法是：

`jjs [options] [script-files] [-- arguments]`

其中：

*   `[options]` 是 `jjs` 命令的选项。多个选项用空格分隔
*   `[script-files]` 是要由 Nashorn 引擎解释的脚本文件列表
*   `[-- arguments]` 是要作为参数传递给脚本或交互式 shell 的参数列表。参数在双连字符后指定，并且可以在脚本内部使用 `arguments` 属性访问

## 选项

`jjs` 工具支持多个选项。表 10-1 列出了 `jjs` 工具的所有选项。某些选项具有功能相同的变体；例如，选项 `-classpath` 和 `-cp` 是同义词。两者都用于设置 CLASSPATH。请注意，某些选项以两个连字符开头。要打印所有选项的列表，请在命令提示符下使用 `–xhelp` 选项运行该工具：

`jjs –xhelp`

表 10-1.

jjs 命令行工具的选项



| 选项 | 描述 |
| --- | --- |
| `-D<name>=<value>` | 为 Java 运行时设置系统属性。可重复使用此选项以设置多个运行时属性值。 |
| `-ccs=<size>` `--class-cache-size=<size>` | 设置类缓存大小（以字节为单位）。默认情况下，类缓存大小设置为 50 字节。你可以使用 k/K、m/M 和 g/G 与大小值配合，分别表示 KB、MB 和 GB。选项 `-css=200` 和 `-css=2M` 分别将类缓存大小设置为 200 字节和 2 MB。 |
| `-classpath <path>` `-cp <path>` | 指定 CLASSPATH。 |
| `-co` `--compile-only` | 仅编译脚本而不运行。默认情况下，此选项处于禁用状态，这意味着脚本会被编译并运行。 |
| `--const-as-var` | 将脚本中的关键字 `const` 替换为关键字 `var`。如果脚本使用了 Nashorn 无法识别的 `const` 关键字，则可使用此选项。默认情况下，此选项处于禁用状态。 |
| `-d=<path>` `--dump-debug-dir=<path>` | 指定用于转储脚本类文件的目标目录。 |
| `--debug-lines` | 在 `.class` 文件中生成行号表。默认情况下，此选项处于启用状态。指定 `--debug-lines=false` 可禁用此功能。 |
| `--debug-locals` | 在 `.class` 文件中生成局部变量表。默认情况下，此选项设置为 `false`。 |
| `-doe` `––dump-on-error` | 指定此选项时，会打印错误的完整堆栈跟踪。默认情况下，仅打印简短的错误消息。 |
| `--early-lvalue-error` | 在解析代码时，将无效的 `lvalue` 表达式报告为早期错误。默认情况下，此选项设置为 `true`。当设置为 false 时，会在代码执行时报告无效的左值表达式。 |
| `--empty-statements` | 在 Java 抽象语法树（AST）中保留空语句。默认情况下，此选项设置为 `false`。 |
| `-fv` `–fullversion` | 打印 Nashorn 引擎的完整版本。 |
| `--function-statement-error` | 当函数声明用作语句时打印错误消息。默认设置为 `false`。 |
| `--function-statement-warning` | 当函数声明用作语句时打印警告消息。默认设置为 `false`。 |
| `-fx` | 将脚本作为 JavaFX 应用程序启动。 |
| `--global-per-engine` | 每个脚本引擎实例使用单个全局实例。默认值为 `false`。 |
| `-help` `-h` | 输出帮助消息并退出。 |
| `-J<flag>` | 将指定的 `<flag>` 传递给 JVM。 |
| `-language` | 指定 ECMAScript 语言版本。有效值为 `es5` 和 `es6`。默认值为 `es5`。 |
| `--lazy-compilation` | 通过不一次性编译整个脚本来启用惰性代码生成策略。默认值为 `true`。 |
| `--loader-per-compile` | 每次编译时创建一个新的类加载器。默认值为 `true`。 |
| `-l` `--locale` | 设置脚本执行的语言环境。默认值为 `en-US`。 |
| `--log=subsystem:lebel` | 为给定数量的子系统启用指定级别的日志记录，例如 `--log=fields:finest,codegen:info`。多个子系统的日志记录用逗号分隔。 |
| `-nj` `--no-java` | 禁用 Java 支持。默认值为 `false`，表示允许在脚本中使用 Java 类。 |
| `-nse` `--no-syntax-extensions` | 不允许非标准语法扩展。默认值为 `false`。 |
| `-nta` `--no-typed-arrays` | 禁用类型化数组支持。默认值为 `false`。 |
| `--optimistic-types` | 使用乐观类型假设并配合去优化重新编译。默认值为 `true`。 |
| `--parse-only` | 仅解析代码而不编译。默认值为 `false`。 |
| `-pcc` `--persistent-code-cache` | 为编译后的脚本启用磁盘缓存。默认值为 `false`。 |
| `--print-ast` | 打印抽象语法树。默认值为 `false`。 |
| `-pc` `--print-code` | 将生成的字节码打印到标准错误输出或指定目录。你可以指定要打印字节码的函数名称。指定目录和函数的语法为：`-pc=dir:<output-directory-path>,function:<function-name>` |
| `--print-lower-ast` | 打印降低后的抽象语法树。默认值为 `false`。 |
| `-plp` `--print-lower-parse` | 打印降低后的解析树。默认值为 `false`。 |
| `--print-mem-usage` | 在每个编译阶段后打印指令寄存器（IR）的内存使用情况。默认值为 `false`。 |
| `--print-no-newline` | `print()` 函数在打印其参数后不会打印换行符。默认值为 `false`。 |
| `-pp` `--print-parse` | 打印解析树。默认值为 `false`。 |
| `--print-symbols` | 打印符号表。默认值为 `false`。 |
| `-pcs` `--profile-callsites` | 转储调用点性能分析数据。默认值为 `false`。 |
| `-scripting` | 启用 shell 脚本功能。默认值为 `false`。 |
| `--stderr=<filename&#124;stream&#124;tty>` | 将 `stderr` 重定向到指定的文件名、流或文本终端。 |
| `--stdout=<filename&#124;stream&#124;tty>` | 将 `stdout` 重定向到指定的文件名、流或文本终端。 |
| `-strict` | 启用严格模式，脚本将使用 ECMAScript 5.1 标准执行。默认值为 `false`。 |
| `-t=<timezone>` `–timezone=<timezone>` | 设置脚本执行的时区。默认时区为 Chicago/America。 |
| `-tcs=<option>` `--trace-callsites=<option>` | 启用调用点跟踪模式。有效选项为 `miss`（跟踪调用点未命中）、`enterexit`（跟踪调用点进入/退出）和 `objects`（打印对象属性）。通过逗号分隔指定多个选项：`-tcs=miss,enterexit,objects` |
| `--verify-code` | 在运行前验证字节码。默认值为 `false`。 |
| `-v` `–version` | 打印 Nashorn 引擎的版本。默认值为 `false`。 |
| `-xhelp` | 打印扩展帮助。默认值为 `false`。 |

## 在交互模式下使用 jjs

如果你在未指定任何选项或脚本文件的情况下运行 `jjs`，它将以交互模式运行。脚本在你输入时被解释执行。请记住，Nashorn 中的字符串可以用单引号或双引号括起来。

以下是在交互模式下使用 `jjs` 工具的一些示例。假设你已将 `jjs` 工具的路径包含在机器的 PATH 环境变量中。如果尚未包含，你可以在以下命令中将 `jjs` 替换为 `JDK_HOME\bin\jjs`。请记住执行 `quit()` 或 `exit()` 函数以退出 `jjs` 工具：

`c:\>jjs`

`jjs> "Hello Nashorn"`

`Hello Nashorn`

`jjs> "Hello".toLowerCase();`

`hello`

`jjs> var list = [1, 2, 3, 4, 5]`

`jjs> var sum = 0;`

`jjs> for each (x in list) { sum = sum + x};`

`15`

`jjs> quit()`

`c:\>`



## 向 jjs 传递参数

以下是一个向 `jjs` 工具传递参数的示例。前五个自然数作为参数传递给 `jjs` 工具，随后通过 `arguments` 属性访问它们。请注意，两个连字符与第一个参数之间必须添加一个空格：

`c:\>jjs -- 1 2 3 4 5`

`jjs> for each (x in` `arguments` `) print(x)`

`1`

`2`

`3`

`4`

`5`

`jjs> quit()`

`c:\>`

请参考清单 10-1 中的脚本。该脚本已保存至名为 `stream.js` 的文件中。该脚本处理一个整数列表。该列表可以作为命令行参数传递给脚本。如果未以参数形式传递列表，则默认使用前五个自然数作为列表。脚本会计算列表中奇数的平方和，并打印列表及该和值。

**清单 10-1. 计算列表中奇数平方和的脚本**

`// stream.js`

`var list;`

`if (arguments.length == 0) {`

`list = [1, 2, 3, 4, 5];`

`}`

`else {`

`list = arguments;`

`}`

`print("List of numbers: " + list);`

`var sumOfSquaredOdds = list.filter(function(n) {return n % 2 == 1;})`

`.map(function(n) {return n * n;})`

`.reduce(function(sum, n) {return sum + n;}, 0);`

`print("Sum of the squares of odd numbers: " + sumOfSquaredOdds);`

使用 `jjs` 工具，可以按如下方式运行 `stream.js` 文件中的脚本。假设 `stream.js` 文件位于当前目录下。否则，需要指定文件的完整路径：

`c:\>jjs stream.js`

`List of numbers: 1,2,3,4,5`

`Sum of the squares of odd numbers: 35`

`c:\>jjs stream.js -- 10 11 12 13 14 15`

`List of numbers: 10,11,12,13,14,15`

`Sum of the squares of odd numbers: 515`

`c:\>`

## 在脚本模式下使用 jjs

`jjs` 工具可以在脚本模式下调用，该模式允许您运行 shell 命令。您可以使用 `–scripting` 选项在脚本模式下启动 `jjs` 工具。shell 命令需用反引号括起来，而非单引号或双引号。以下是在脚本模式下使用 `jjs` 工具执行 `date` 和 `ls` shell 命令的示例：

`c:\>jjs -scripting`

`` jjs> `date` ``

`Wed Oct 15 15:27:07 CDT 2014`

`` jjs> `ls -l` ``

`total 3102`

`drwxr-xr-x  4 ksharan Administrators       0 Jan 11  2014 $AVG`

`drwxr-xr-x  5 ksharan Administrators       0 Jan 22  2014 $Recycle.Bin`

`-rw-r--r--  1 ksharan Administrators       1 Jun 18  2013 BOOTNXT`

`-rw-r--r--  1 ksharan Administrators      94 May 23  2013 DBAR_Ver.txt`

`More output goes here...`

`jjs> exit()`

`c:\>`

Nashorn 在脚本模式下定义了几个全局对象和函数，如表 10-2 所示。

**表 10-2. 脚本模式下可用的全局对象和函数**

| 全局对象 | 描述 |
| --- | --- |
| `$ARG` | 存储传递给脚本的参数。工作方式与 `arguments` 相同。 |
| `$ENV` | 将所有环境变量映射到一个对象。 |
| `$EXEC(cmd, input)` | 一个全局函数，用于在新进程中运行命令，并将 `input` 传递给 `cmd`。两个参数都可以是命令，在这种情况下，`input` 的输出将作为输入传递给 `cmd`。 |
| `$OUT` | 存储进程的最新标准输出。例如，执行 `$EXEC()` 的结果保存在 `$OUT` 中。 |
| `$ERR` | 存储进程的最新标准错误输出。 |
| `$EXIT` | 存储进程的退出代码。非零值表示进程失败。 |
| `echo(arg1, arg2,...)` | `echo()` 函数的工作方式与 `print()` 函数相同，但仅在脚本模式下可用。 |
| `readLine(prompt)` | 从标准输入读取一行输入。指定的参数将作为提示符显示。默认情况下，读取的输入会显示在标准输出上。该函数返回读取的输入。 |
| `readFully(filePath)` | 读取指定文件的全部内容。默认情况下，内容会显示在标准输出上。您可以将函数的返回值赋给一个变量。 |

以下脚本展示了如何使用 `$ARG` 全局对象：

`c:\>jjs -scripting -- 10 20 30`

`jjs> for each(var arg in $ARG) print(arg);`

`10`

`20`

`30`

`jjs>`

以下脚本展示了如何使用 `$ENV` 全局对象。它打印 Windows 上 OS 环境变量的值，并列出所有环境变量：

`jjs> print($ENV.OS);`

`Windows_NT`

`jjs> for(var x in $ENV) print(x);`

`LOCALAPPDATA`

`PROCESSOR_LEVEL`

`FP_NO_HOST_CHECK`

`USERDOMAIN`

`LOGONSERVER`

`PROMPT`

`OS`

`...`

以下脚本使用 `$EXEC()` 全局函数列出所有包含 `ksharan` 且扩展名为 `txt` 的文件：

`jjs> $EXEC("grep -l ksharan *.txt");`

`test.txt`

您可以将 shell 命令的输出捕获到一个变量中。脚本模式允许在用双引号括起来的字符串中进行表达式替换。请注意，表达式替换功能在用单引号括起来的字符串中不可用。表达式指定为 `${expression}`。以下命令将 `date` shell 命令的值捕获到一个变量中，并使用表达式替换将日期值嵌入到字符串中。请注意，在示例中，当字符串用单引号括起来时，表达式替换不起作用：

`c:\ >jjs -scripting`

`` jjs> var today = `date` ``

`jjs> "Today is ${today}"`

`Today is Mon Jul 14 22:48:26 CDT 2014`

`jjs> 'Today is ${today}'`

`Today is ${today}`

`jjs> quit()`

`c:\>`

您还可以使用脚本模式执行存储在文件中的 shell 脚本：

`C:\> jjs –scripting myscript.js`



`jjs` 工具支持在脚本文件中使用 heredoc，这些文件可以在脚本模式下运行。heredoc 也被称为 here document、here-string 或 here-script。它是一种多行字符串，其中的空白字符会被保留。heredoc 以双尖括号（`<<`）和一个分隔标识符开头。通常使用 `EOF` 或 `END` 作为分隔标识符。不过，你也可以使用脚本中其他地方未用作标识符的任何其他标识符。多行字符串从结束行开始，并以相同的分隔标识符结束。以下是在 Nashorn 中使用 heredoc 的示例：

`var str = <<EOF`

`This is a multi-line string using the heredoc syntax.`

`Bye Heredoc!`

`EOF`

清单 10-2 包含了一个在 Nashorn 中使用 heredoc 的脚本。`$ARG` 属性仅在脚本模式下定义，其值是通过 `jjs` 工具传递给脚本的参数：

清单 10-2\. 使用 Heredoc 风格多行字符串的 heredoc.js 文件内容

`// heredoc.js`

`var str = <<EOF`

`This is a multiline string.`

`Number of arguments passed to this`

`script is ${$ARG.length}`

`Arguments are ${$ARG}`

`Bye Heredoc!`

`EOF`

`print(str);`

你可以按如下方式执行 `heredoc.js` 脚本文件：

`c:\> jjs -scripting heredoc.js`

`This is a multi-line string.`

`Number of arguments passed to this`

`script is 0`

`Arguments are`

`Bye Heredoc!`

`c:\> jjs -scripting heredoc.js -- Kishori Sharan`

`This is a multi-line string.`

`Number of arguments passed to this`

`script is 2`

`Arguments are Kishori,Sharan`

`Bye Heredoc!`

除了 Nashorn 支持的两种注释风格（`//` 和 `/* */`）之外，`jjs` 工具还支持一种额外的单行注释风格，它以井号（`#`）开头。如果由 `jjs` 工具运行的脚本文件以 `#` 开头，`jjs` 工具会自动启用脚本模式，并在脚本模式下执行整个脚本。请参考清单 10-3 中的脚本。

清单 10-3\. jjs 工具的特殊注释。内容存储在 jjscomments.js 文件中

`# This script will run in scripting mode by the jjs tool`

`# because it starts with a number sign`

`// Set the current directory to C:\kishori`

`$ENV.PWD = "C:\\kishori";`

`// Get the list of files and directories in the current directory`

``var str = `ls -F`;``

`print(str);`

以下命令运行 `jjscomments.js` 文件中的脚本。该脚本以 `#` 符号开头，因此 `jjs` 工具会自动启用脚本模式：

`c:\>jjs` `jjscomments.js`

`books/`

`ejb/`

`hello.txt`

`important/`

`programs/`

`rmi.log`

`rmi.policy`

`scripts/`

`c:\>`

通过让 `jjs` 工具将脚本文件开头的 `#` 符号解释为 shell 可执行文件，你可以在脚本文件开头使用 shebang（`#!`）来将其作为脚本可执行文件运行。请注意，shebang 在类 Unix 操作系统上直接受支持。你需要在 shebang 中包含 `jjs` 工具的路径，这样脚本将由 `jjs` 工具执行。脚本文件将被传递给 `jjs` 工具来执行。由于脚本文件以 `#` 符号（shebang `#!` 的一部分）开头，`jjs` 工具会自动启用脚本模式。以下是一个使用 shebang 的脚本示例，假设 `jjs` 工具位于 `/usr/bin` 目录：

`#!/usr/bin/jjs`

``var str = `ls -F`;``

`print(str);`

## 总结

Java 8 附带了一个名为 `jjs` 的命令行工具。它位于 `JDK_HOME\bin` 目录中。它用于在命令行上运行 Nashorn 脚本。`jjs` 工具支持许多选项。它可以被调用来运行文件中的脚本、交互模式以及脚本模式。

如果你不指定任何选项或脚本文件，`jjs` 将以交互模式运行。在交互模式下，脚本会在输入时被解释执行。

如果你使用 `–scripting` 选项调用 `jjs`，它将以脚本模式运行，允许你使用任何操作系统特定的 shell 命令。Shell 命令用反引号括起来。在脚本模式下，`jjs` 工具支持脚本文件中的 heredoc。如果脚本文件以 `#` 符号开头，运行该脚本文件会自动启用脚本模式。这支持执行包含 shebang（脚本开头的 `#!`）的脚本。

