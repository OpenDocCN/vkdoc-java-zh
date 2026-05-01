# 7. 领域特定语言

Groovy 拥有许多特性，使其非常适合编写 DSL（领域特定语言）：

*   带有委托的闭包。

*   括号和点号 (.) 是可选的（命令链）。

*   能够使用类别和扩展模块向标准类添加方法。

*   能够重写许多运算符（加、减等）。

*   `methodMissing` 和 `propertyMissing` 方法。

领域特定语言可用于多种目的，例如允许领域专家阅读和编写代码，或阐明业务逻辑的含义。它们允许业务专家无需成为编程专家即可阅读或编写代码。



## 闭包与委托

在 Groovy 中，你可以将一段代码块（闭包）作为参数，然后通过局部变量作为委托来调用它。例如，假设你有以下用于发送短信的代码：

```
1   class SMS {
2           String from, to, body;
3           def  from(String fromNumber) {
4                   from = fromNumber
5           }
6           def  to(String toNumber) {
7                   to = toNumber
8           }
9           def  body(String body) {
10                  this.body = body
11           }
12           def  send() {
13                   // 发送短信。
14           }
15   }
```

在 Java 中，你需要按以下方式使用它：

```
1   SMS m = new  SMS();
2   m.from("555-432-1234");
3   m.to("555-678-4321");
4   m.body("Hey there!");
5   m.send();
```

在 Groovy 中，你可以向 `SMS` 类添加以下静态方法，以实现类似 DSL 的用法（`block` 应为一个闭包）：

```
1   def static send(@DelegatesTo(SMS) Closure block) {
2           SMS m = new  SMS()
3           block.delegate = m
4           block()
5           m.send()
6   }
```

这将 `SMS` 对象设置为该代码块的委托，从而将方法调用转发给它。（可选的）`@DelegatesTo(SMS)` 注解会告知编译器和 IDE 哪个类被用作该闭包的委托。现在你可以这样操作：

```
1   SMS.send {
2           from '555-432-1234'
3           to '555-678-4321'
4           body 'Hey there!'
5   }
```

这消除了代码中的大量重复。

### ![../images/426440_2_En_7_Chapter/426440_2_En_7_Figa_HTML.gif](img/426440_2_En_7_Figa_HTML.gif) 提示

如上所示，在进行简单的方法调用时，你可以省略括号。

## 命令链

如前所述，Groovy 支持*命令链*，它允许你在调用带有一个或多个参数的方法时完全省略括号和点号。

例如，我们沿用之前用于发送短信的类，但将其改造为支持命令链语法。

```
1   class SMS {
2           String from, to, body;
3           SMS  from(String fromNumber) {
4                   from = fromNumber; return this
5           }
6           SMS  to(String toNumber) {
7                   to = toNumber; return this
8           }
9           SMS  body(String body) {
10                   this.body = body; return this
11           }
12           def  send() { /* 发送短信 */ }
13           def static send(@DelegatesTo(SMS) Closure block) {
14           /* 与之前相同 */ }
15   }
```

现在，我们的 DSL 语法可以像下面这样使用：

```
1   SMS.send {
3           from '555-432-1234' to '555-678-4321'
4              body 'Hey there!'
5   }
```

## 重载运算符

在 Groovy 中，你只需使用运算符对应的英文单词来命名方法，即可重载运算符。例如，`plus` 对应 `+`，`minus` 对应 `-`。更多运算符请参见下表：

| 运算符 | 方法名 |
| --- | --- |
| `+` | plus |
| `-` | minus |
| `*` | multiply |
| `/` | div |
| `%` | mod |
| `**` | power |
| `&#124;` | or |
| `&` | and |
| `ˆ` | xor |
| `<<` | leftShift |
| `>>` | rightShift |
| `++` | next |
| `--` | previous |

对于更复杂的运算符，下表使用变量 “a”、“b” 和 “c” 来演示其用法（其中 “a” 是定义该方法的类的实例）：

| 示例 | 方法声明 |
| --- | --- |
| `a()` | call() |
| `a as b` | asType(b) |
| `a[b]` | getAt(b) |
| `a[b] = c` | putAt(b,c) |
| +a | positive() |
| -a | negative() |
| ~a | bitwiseNegate() |
| b in a | isCase(b) |

例如，让我们创建一个名为 `Logic` 的类，它包含一个布尔值，并定义 `and` 和 `or` 方法。

```
1   class  Logic  {
2       boolean value
3       Logic(v) {this.value = v}
4       def and(Logic other) {
5           this.value && other.value
6       }
7       def or(Logic other) {
8           this.value || other.value
9       }
10   }
```

然后，让我们使用这些方法，看看它们是否按预期工作：

```
4   println "groovy truth: ${pale && old}" //true
5   println "using and: ${pale & old}" // false
6   println "using or: ${pale | old}" // true
```

注意，使用内置的 `&&` 运算符会应用 “Groovy 真值” 规则，由于两个变量都非空，因此返回 true。

接下来，让我们尝试在一个类上定义 `leftShift` 和 `minus` 运算符：

```
1   class Wizards {
2       def list = []
3       def leftShift(person) { list.add person }
4       def  minus(person) { list.remove person }
5       String toString() { "Wizards: $list" }
6   }
7   def  wiz = new  Wizards()
8   wiz << 'Gandolf'
9   println wiz // Wizards: [Gandolf]
10   wiz << 'Harry'
11   println wiz // Wizards: [Gandolf, Harry]
12   wiz - 'Harry'
13   println wiz // Wizards: [Gandolf]
```

你还可以实现 `putAt` 和 `getAt` 方法，从而允许使用方括号语法。例如：

```
1   def value = wiz[1] // 使用 getAt(1)
2   wiz[1] = value // 使用 putAt(1, value)
```

这在编写使用方括号表示法的领域特定语言时非常有用。



## 缺失的方法与属性

如前所述，Groovy 提供了一种通过 `methodMissing` 方法在运行时实现功能的方式：

```
1   def methodMissing(String name, args)
```

然而，Groovy 还提供了一种拦截缺失属性的方式，这些属性通过 Groovy 的属性语法进行访问。属性访问通过 `propertyMissing(String name)`（返回一个值）实现，属性修改则通过 `propertyMissing(String name, Object value)`（设置属性的值）实现。

例如，以下是一个用于化学化合物的 DSL 的摘录：

```
1   class  Chemistry  {
2     public static void exec(Closure block) {
3       block.delegate = new  Chemistry()
4       block()
5     }
6     def propertyMissing(String name) {
7       def  comp = new  Compound(name)
8       (comp.elements.size() == 1 && comp.elements.values()[0]==1) ?
9         comp.elements.keySet()[0] : comp
10     }
11   }
```

给定以下用于 `Compound` 和 `Element` 的代码：

```
// 表示一种化学元素
class Element  {
String symbol
Element(s) { symbol = s }
double getWeight() {symbol=='H' ? 1.00794 : 15.9994}
String toString() { symbol }
}
// 表示一种化学化合物
class Compound {
final Map elements = [:]
Compound(String str) {
def matcher = str =~ /([A-Z][a-z]∗)([0-9]+)?/
while (matcher.find()) add(
new Element(matcher.group(1)),
(matcher.group(2) ?: 1) as Integer)
}
void add(Element e, int num) {
if (elements[e]) elements[e] += num
else elements[e] = num
}
double getWeight() {
elements.keySet().inject(0d) { sum, key ->
sum + (key.weight * elements[key])
}
}
String toString() { "$elements" }
}
```

在这个例子中，`propertyMissing`（第 6-9 行）创建了一个新的 `Compound` 对象，并返回该 `Compound` 对象，或者如果 `Compound` 中只有一个元素，则返回一个 `Element` 对象。这使得可以根据一个`缺失`属性的名称来创建 `Compound` 对象。例如：

```
1   def  c = new  Chemistry()
2   def water = c.H2O
3   println water // [H: 2, O: 1]
4   println water.weight // 18.01528
```

这被解释为尝试访问一个名为 `H2O` 的属性，从而触发了 `propertyMissing` 方法。

### ![../images/426440_2_En_7_Chapter/426440_2_En_7_Figb_HTML.gif](img/426440_2_En_7_Figb_HTML.gif) 信息

`H2O` 指的是水的化学成分，即两个氢原子和一个氧原子。

通过使用静态的 `exec` 方法，这个 DSL 通过将 `Chemistry` 的一个实例暴露为闭包的委托，充分发挥了其潜力，从而允许以下示例：

```
1   Chemistry.exec {
2           def water = H2O
3           println water
4           println water.weight
5   }
```

这与通过调用 `Chemistry` 的 `propertyMissing` 方法来创建 `H2O` 化合物的效果相同。

Groovy Chemistry^(¹⁷) 的完整代码已在 GitHub 上提供。它能够计算化学化合物的原子量以及按原子量计算的百分比。它包含了所有已知的元素、它们的名称和原子量。

如果没有 Groovy 的帮助，这个 DSL 将很难实现。例如，在 Java 中，你需要使用字符串来表示化合物，这会使语法充斥着大量的引号和括号。

## 扩展模块

Groovy 中的扩展模块允许你通过包含一个库来向项目中的现有类添加功能。它的工作方式很像类别（Categories），但可以在任何地方使用，无需 `use` 子句。

要创建一个扩展模块，请在 `META-INF/services/` 目录下创建一个名为 `org.codehaus.groovy.runtime.ExtensionModule` 的文件。在该文件中，列出你所有的 `extensionClasses` 和 `staticExtensionClasses`。例如，创建一个包含以下内容的文件：

```
moduleName=adamldavis-groovy-dsl-example
moduleVersion=0.1-beta1
extensionClasses=com.adamldavis.gdsl.MyDSLExtension
staticExtensionClasses=com.adamldavis.gdsl.MyStaticDSLExtension
```

当包含在一个项目中时，Groovy 会查看 `MyDSLExtension` 类（位于 `com.adamldavis.gdsl` 包中）的所有静态方法，并使用它们向每个方法的第一个参数所属类的实例添加功能。`moduleName` 和 `moduleVersion` 属性仅用于确保不会加载多个冲突版本的库。

例如，给定以下代码，`String` 类将实质上获得添加的 `upper()` 方法。

```
class MyDSLExtension {
static String upper(String it) { it.toUpperCase() }
}
```

同样，Groovy 会查看 `MyStaticDSLExtension` 类的所有静态方法，并使用它们向每个方法的第一个参数所属的类添加功能。例如，以下代码将为 `String` 类添加一个 `boom` 方法，允许 `String.boom()` 返回 “!”：

```
class MyStaticDSLExtension {
static String boom(String it) { "!" }
}
```

请注意，对于 `staticExtensionClasses`，给定的参数（前面代码中的 `it`）不能在方法内部使用；它仅用于类型推断。

### ![../images/426440_2_En_7_Chapter/426440_2_En_7_Figc_HTML.gif](img/426440_2_En_7_Figc_HTML.gif) 练习

为你感兴趣的事物（无论是体育、数学、电影还是天体物理学）创建一个 Groovy DSL。

脚注 1

