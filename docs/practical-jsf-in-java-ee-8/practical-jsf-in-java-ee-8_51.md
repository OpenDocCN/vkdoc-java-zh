# 附录 D：编程风格

你可能已经注意到，我始终使用下划线作为字段的前缀。Oracle 建议不要使用下划线或美元符号作为变量名的开头（你可以在 [`docs.oracle.com/javase/tutorial/java/nutsandbolts/variables.html`](http://docs.oracle.com/javase/tutorial/java/nutsandbolts/variables.html) 自行阅读该建议），尽管它指出这种做法在技术上是合法的。我的一位技术审阅者要求我严格遵守 Java 命名规范，认为这是一本面向初学者的书，应该遵循惯例。

但这本书并非面向编程初学者。它面向的是那些刚接触 JSF，或许也刚接触 Web 开发的、有经验的 Java 开发者。命名规范并非法律，而且，我们讨论的这个规范只是一个教程中的建议。如果你查看一下 Java 命名规范网站（[www.oracle.com/technetwork/java/codeconventions-135099.html](http://www.oracle.com/technetwork/java/codeconventions-135099.html)），你会发现它已经不再维护了：“此页面未得到积极维护……本文档的最后一次修订是在 1999 年 4 月 20 日。”所以，它已经相当陈旧了。

我使用下划线作为字段前缀是有一些原因的，我接下来会解释。我不想引发关于其优缺点的讨论。我只是想解释我为什么这样做。作为一名有经验的 Java 开发者，你应该能够将我的源代码转换成不同的风格。使用现代的 IDE，这只不过是一次小小的重命名操作。

让我们回到几年前，那时所谓的*匈牙利命名法*还很流行且有用。使用这种命名法，每个变量都以它的类型作为前缀，例如 `intNumber`，这表示其底层类型是整数。这对于那些非强类型语言非常有帮助。`intNumber = 3.142` 可能是一个有效的赋值。在这种情况下，前缀应该提醒程序员只使用整数值。

对于拥有强类型系统的 Java 来说，没有必要使用这种匈牙利命名法。如果你声明 `int number`，编译器会拒绝 `number = 3.142`。

类似地，第二种前缀用于指示变量的更广泛作用域。例如，前缀 `mb_` 表示一个*成员变量*，我们称之为*字段*。毫无疑问，`mb_intNumber` 是一个非常丑陋的变量名。所以，让我们抛弃这些过时的前缀。对于现代编程语言来说，它们是不必要的。

但是属性（properties）怎么办呢？如果我们有一个字段 `number`，并且想通过 getter 为其赋一个新值呢？由于变量隐藏，你不能这样做：

```
 1                     private int                  number;                
                  2                     public void                  setNumber(                  int                  number){                
3     number = number;
4   }
```

为了区分字段和局部变量，你需要使用不同的名称。这是 C# 的方式，翻译成 Java 如下：

```
 1                     private int                  number;                
                  2                     public void                  setNumber(                  int                  value){                
3     number = value;
4   }
```

现在，方法签名中总是包含 `value`，这可能毫无意义。下一个方法：

```
 1                     private int                  fNumber;                
                  2                     public void                  setNumber(                  int                  number){                
3     fNumber = number;
4   }
```

现在我们对字段和局部变量使用了不同的名称，但这使得代码更难阅读。而且根据驼峰命名法的“规则”，变量名变成了大写。让我们试试下划线：

```
 1                     private int                  _number;                
                  2                     public void                  setNumber(                  int                  number){                
3     _number = number;
4   }
```

下划线比其他任何前缀都更不引人注目。字段名会像往常一样以小写形式书写。唯一的缺点是它不完全遵循完整的命名规范。

对于这个问题，还有另一种常见的解决方案：使用 `this`：

```
 1                     private int                  number;                
                  2                     public void                  setNumber(                  int                  number){                
                  3                       this                  .number = number;                
4   }
```

在我看来，这符合命名规范，但却是对 `this` 的误用。`this` 指的是对象本身（其他语言使用 `self`）。当对象需要将自身传递给某个方法或创建流畅接口（如构建器）以返回自身时，它非常有用。但在这里，它仅仅被（误）用来区分字段和变量，而通过选择不同的名称可以更好地做到这一点。

让我们看看在文件夹内复制文件的操作：

```
copy file1 file2
```

如果你需要复制文件夹，你可能会使用一个点：

```
copy .\otherFolder
```

尽管在技术上可以使用点来表示当前目录中的文件，但你通常不会使用

```
copy .\file1 .\file2
```

或者 UNIX 方式的 `cp ./file1 ./file2`。

这看起来奇怪又丑陋——就像 `this.field` 在我看来奇怪又丑陋一样。

如果你是一位多语言程序员，也使用 C#，你可能知道 `this` 前缀在 Java 的这个“兄弟”语言（两者都是类 C 语言）中非常常见。在极少数情况下，可能需要在没有语法高亮的 IDE 外部，使用简单的编辑器编辑源代码。在这种情况下，下划线作为副作用，对于识别字段非常有帮助。

现在你知道了为什么我在所有项目（包括本书）中都使用下划线作为字段前缀。当然，欢迎你在下载的源代码版本中更改它。

