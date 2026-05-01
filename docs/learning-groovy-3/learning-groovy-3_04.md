# 3. 工具

除了 `groovy` 之外，Groovy 安装包还附带了一些有用的工具，本章将介绍这些工具。



## 控制台

```
1   groovyConsole
```

Groovy 控制台是一种快速简便的方式，让你无需启动完整的 IDE，就能以可视化方式尝试 Groovy 代码。

每当你有一个想法想快速验证时，打开 Groovy 控制台，输入一些代码，然后按 Ctrl+R 运行它。查看输出并修改代码后，按 Ctrl+W 清除输出，再按 Ctrl+R 再次运行代码。一旦你习惯了这两个快捷键，Groovy 控制台可能会成为你不可或缺的开发工具。

它还具有（除其他功能外）检查代码 AST（抽象语法树）的能力，即编译器使用的代码内部表示形式。使用 `Script - Inspect Ast` 或 Ctrl+T 打开 Groovy AST 浏览器。

你可以使用 `-cp` 选项为 Groovy 控制台提供一个在运行时可用的类路径。当你想要引用已编译的其他类时，这非常有用。例如，在 Linux/OSX 环境中，你可以使用以下命令（其中“:”用于分隔路径）：

```
1   groovyConsole -cp src/main/groovy/:src/main/resources/ example.groovy
```

## 编译

```
1   groovyc
```

与 javac 非常相似，groovyc 将 Groovy 代码编译为 JVM 字节码（`*.class` 文件）。在运行之前并非必须编译。你可以使用 `groovy` 命令来运行一个 Groovy 脚本文件，这些文件通常以 `.groovy` 结尾，但也可以有任何扩展名。

要利用 JDK 7+ 的 invoke-dynamic 指令，请使用 `--indy` 标志。^((14)) 这也适用于 `groovy` 命令。

Invoke-dynamic 帮助编译器提高诸如鸭子类型、元编程和方法缺失调用等操作的性能。

## Shell

```
1   groovysh
```

Groovy shell 可用于在交互式命令 shell 中执行 Groovy 代码。

### ![../images/426440_2_En_3_Chapter/426440_2_En_3_Figa_HTML.gif](img/426440_2_En_3_Figa_HTML.gif) 练习

试试看！

## 文档

```
1   groovydoc
```

此工具从你的 Groovy 代码生成文档。

Groovy 使用与 Java 相同的注释语法，包括记录代码的约定。

```
1   /** 这是一个文档注释。 */
2   /* 这不是 */
3   // 这是一行注释。
```

甚至可以使用 `groovy.attach.groovydoc` 命令行选项将 Groovy 文档作为元数据访问。^((15)) 例如，在命令行中使用以下命令：

```
groovyc -Dgroovy.attach.groovydoc=true Test.groovy
```

假设你将以下代码放入 Test.groovy 中：

```
/** 我的类 */ class Test { /** 我的方法 */ def method() {} }
```

你可以在编译时从 AST 访问这些文档（例如，在编写 AST 转换时，这可能很有用）。

此外，可以使用 `groovy.attach.runtime.groovydoc` 命令行选项在运行时访问 Groovy 文档。例如，在命令行中使用以下命令：

```
groovyc -Dgroovy.attach.runtime.groovydoc=true Test.groovy
```

假设“Test.groovy”中有以下代码：

```
class Test { /**@ 我的元数据 */ def method() {} }
```

现在，你可以使用以下代码（在 Groovy 3 中）访问前面的注释元数据：

```
def data = Test.methods.find{ it.name=='method' }.groovydoc.content
assert data == '我的元数据'
```

脚注 1   2

