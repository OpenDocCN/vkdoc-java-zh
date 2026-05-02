# 5. 过程与编译脚本

在本章中，你将学习：

*   如何从 Java 程序调用用脚本编写的过程
*   如何在 Nashorn 脚本中实现 Java 接口
*   如何编译 Nashorn 脚本并重复执行它们



## 在脚本中调用过程

在前面的章节中，你已经了解了如何使用 `ScriptEngine` 的 `eval()` 方法来调用脚本。如果 `ScriptEngine` 支持过程调用，也可以直接从 Java 程序调用用脚本语言编写的过程。

脚本语言可能允许创建过程、函数和方法。Java 脚本 API 允许你从 Java 应用程序中调用这些过程、函数和方法。在本节中，我将使用术语“过程”来指代过程、函数和方法。当讨论的上下文需要时，我会使用具体的术语。

并非所有脚本引擎都需要支持过程调用。Nashorn JavaScript 引擎支持过程调用。如果脚本引擎支持，那么该脚本引擎类的实现必须实现 `Invocable` 接口。`Invocable` 接口包含以下四个方法：

*   `<T> T getInterface(Class<T> cls)`
*   `<T> T getInterface(Object obj, Class<T> cls)`
*   `Object invokeFunction(String name, Object... args) throws ScriptException, NoSuchMethodException`
*   `Object invokeMethod(Object obj, String name, Object... args) throws ScriptException, NoSuchMethodException`

`getInterface()` 方法的两个版本允许你获取用脚本语言实现的 Java 接口的实例。我将在下一节详细讨论这些函数。`invokeFunction()` 方法允许你调用用脚本语言编写的顶层函数。`invokeMethod()` 方法允许你调用用脚本语言编写的对象的方法。

在调用过程之前，开发者有责任检查脚本引擎是否实现了 `Invocable` 接口。调用过程是一个四步流程：

*   检查脚本引擎是否支持过程调用
*   将引擎引用转换为 `Invocable` 类型
*   评估包含过程源代码的脚本，以便引擎编译并缓存该脚本
*   使用 `Invocable` 接口的 `invokeFunction()` 方法来调用过程和函数；使用 `invokeMethod()` 方法来调用在脚本语言中创建的对象的各个方法

以下代码片段执行了检查脚本引擎实现类是否实现了 `Invocable` 接口的操作：

`// 获取 Nashorn 引擎`

`ScriptEngineManager manager = new ScriptEngineManager();`

`ScriptEngine engine = manager.getEngineByName("JavaScript");`

`// 确保脚本引擎实现了 Invocable 接口`

`if (engine instanceof Invocable) {`

`System.out.println("支持调用过程。");`

`}`

`else  {`

`System.out.println("不支持调用过程。");`

`}`

第二步是将引擎引用转换为 `Invocable` 接口类型：

`if (engine instanceof Invocable) {`

`Invocable inv = (Invocable)engine;`

`// 此处添加更多代码`

`}`

第三步是评估脚本，以便脚本引擎编译并存储过程的编译形式以供后续调用。以下代码片段执行此步骤：

`// 声明一个名为 add 的函数，用于将两个数字相加`

`String script = "function add(n1, n2) { return n1 + n2; }";`

`// 评估该函数。调用 eval() 并不会调用该函数。 // 它只是编译它。`

`engine.eval(script);`

最后一步是调用该过程，如下所示：

`// 以 30 和 40 作为函数参数调用 add 函数。`

`// 这就像在脚本中调用了 add(30, 40) 一样。`

`Object result = inv.invokeFunction("add", 30, 40);`

`invokeFunction()` 的第一个参数是过程的名称。第二个参数是一个可变参数，用于指定传递给过程的参数。`invokeFunction()` 方法返回过程返回的值。



清单 5-1 展示了如何调用函数。它调用了一个用 Nashorn JavaScript 编写的函数，并加载了名为`factorial.js`和`avg.js`的文件中的脚本。这些文件包含了名为`factorial()`和`avg()`函数的 Nashorn 代码。随后，程序通过`Invocable`接口的`invokeFunction()`方法调用了这些函数。

清单 5-1\. 调用用 Nashorn JavaScript 编写的函数

`// InvokeFunction.java`

`package com.jdojo.script;`

`import javax.script.Invocable;`

`import javax.script.ScriptEngine;`

`import javax.script.ScriptEngineManager;`

`import javax.script.ScriptException;`

`public class InvokeFunction {`

`public static void main(String[] args) {`

`ScriptEngineManager manager = new ScriptEngineManager();`

`ScriptEngine engine = manager.getEngineByName("JavaScript");`

`// 确保脚本引擎实现了 Invocable 接口`

`if (!(engine instanceof Invocable)) {`

`System.out.println("不支持调用过程。");`

`return;`

`}`

`// 将引擎引用转换为 Invocable 类型`

`Invocable inv = (Invocable) engine;`

`try {`

`String scriptPath1 = "factorial.js";`

`String scriptPath2 = "avg.js";`

`// 首先执行脚本，以便编译 factorial()和 avg()函数`

`// 并使其可供调用`

`engine.eval("load('" + scriptPath1 + "');");`

`engine.eval("load('" + scriptPath2 + "');");`

`// 调用 add 函数两次`

`Object result1 = inv.invokeFunction("factorial", 10);`

`System.out.println("factorial(10) = " + result1);`

`Object result2 = inv.invokeFunction("avg", 10, 20, 30);`

`System.out.println("avg(10, 20, 30) = " + result2);`

`}`

`catch (ScriptException | NoSuchMethodException e) {`

`e.printStackTrace();`

`}`

`}`

`}`

`factorial(10) = 3628800.0`

`avg(10, 20, 30) = 20.0`

面向对象或基于对象的脚本语言可能允许你定义对象及其方法。你可以使用`Invocable`接口的`invokeMethod(Object obj, String name, Object... args)`方法调用此类对象的方法。第一个参数是对象的引用，第二个参数是你要在对象上调用的方法名称，第三个参数是一个可变参数，用于向被调用的方法传递参数。

清单 5-2 包含一个 Nashorn 脚本，该脚本创建了一个名为`calculator`的对象，并添加了四个方法用于两个数的加、减、乘、除。请注意，我使用了 Nashorn 语法扩展来定义函数表达式，其中未指定花括号和`return`语句。

清单 5-2\. 在 Nashorn 脚本中创建的 Calculator 对象

`// calculator.js`

`// 创建一个对象`

`var calculator = new Object();`

`// 向 calculator 对象的原型添加四个方法`

`calculator.add = function (n1, n2) n1 + n2;`

`calculator.subtract = function (n1, n2) n1 - n2;`

`calculator.multiply = function (n1, n2) n1 * n2;`

`calculator.divide = function (n1, n2) n1 / n2;`

清单 5-3 演示了在 Nashorn 中创建的`calculator`对象上调用方法的过程。请注意，该对象是在 Nashorn 脚本内部创建的。要从 Java 调用该对象的方法，你需要通过脚本引擎获取该对象的引用。程序执行了`calculator.js`文件中的脚本，该脚本创建了`calculator`对象并将其引用存储在一个名为`calculator`的变量中。`engine.get("calculator")`方法将`calculator`对象的引用返回给 Java 代码。

清单 5-3\. 在 Nashorn 创建的对象上调用方法

`// InvokeMethod.java`

`package com.jdojo.script;`

`import javax.script.Invocable;`

`import javax.script.ScriptEngine;`

`import javax.script.ScriptEngineManager;`

`import javax.script.ScriptException;`

`public class InvokeMethod {`

`public static void main(String[] args) {`

`// 获取 Nashorn 引擎`

`ScriptEngineManager manager = new ScriptEngineManager();`

`ScriptEngine engine = manager.getEngineByName("JavaScript");`

`// 确保脚本引擎实现了 Invocable 接口`

`if (!(engine instanceof Invocable)) {`

`System.out.println("不支持调用方法。");`

`return;`

`}`

`// 将引擎引用转换为 Invocable 类型`

`Invocable inv = (Invocable) engine;`

`try {`

`// 声明一个带有 add()方法的全局对象`

`String scriptPath = "calculator.js";`

`// 首先执行脚本`

`engine.eval("load('" + scriptPath + "')");`

`// 获取脚本中创建的 calculator 对象引用`

`Object calculator = engine.get("calculator");`

`// 在 calculator 对象上调用方法`

`int x = 30;`

`int y = 40;`

`Object addResult = inv.invokeMethod(calculator, "add", x, y);`

`Object subResult = inv.invokeMethod(calculator, "subtract", x, y);`

`Object mulResult = inv.invokeMethod(calculator, "multiply", x, y);`

`Object divResult = inv.invokeMethod(calculator, "divide", x, y);`

`System.out.printf("calculator.add(%d, %d) = %s%n", x, y, addResult);`

`System.out.printf("calculator.subtract(%d, %d) = %s%n", x, y, subResult);`

`System.out.printf("calculator.multiply(%d, %d) = %s%n", x, y, mulResult);`

`System.out.printf("calculator.divide(%d, %d) = %s%n", x, y, divResult);`

`}`

`catch (ScriptException | NoSuchMethodException e) {`

`e.printStackTrace();`

`}`

`}`

`}`

`calculator.add(30, 40) = 70`

`calculator.subtract(30, 40) = -10.0`

`calculator.multiply(30, 40) = 1200.0`

`calculator.divide(30, 40) = 0.75`

提示

使用`Invocable`接口可以重复执行过程、函数和方法。执行包含过程、函数和方法的脚本时，会将中间代码存储在引擎中，从而在重复执行时提升性能。



## 在脚本中实现 Java 接口

Java 脚本 API 允许你在脚本语言中实现 Java 接口。在脚本语言中实现 Java 接口的好处是，你可以在 Java 代码中使用该接口的实例，就像该接口是用 Java 实现的一样。你可以将接口实例作为参数传递给 Java 方法。Java 接口的方法可以在脚本中使用顶层过程或对象的方法来实现。

`Invocable` 接口的 `getInterface()` 方法用于获取在脚本中实现的 Java 接口的实例。该方法有两个版本：

*   `<T> T getInterface(Class<T> cls)`
*   `<T> T getInterface(Object obj, Class<T> cls)`

第一个版本用于获取一个 Java 接口的实例，该接口的方法在脚本中作为顶层过程实现。接口类型作为参数传递给此方法。假设你有一个 `Calculator` 接口，如清单 5-4 所示，其中包含四个方法：`add()`、`subtract()`、`multiply()` 和 `divide()`。

**清单 5-4. 一个计算器接口**

`// Calculator.java`

`package com.jdojo.script;`

`public interface Calculator {`

`double add (double n1, double n2);`

`double subtract (double n1, double n2);`

`double multiply (double n1, double n2);`

`double divide (double n1, double n2);`

`}`

考虑用 Nashorn 编写的顶层函数，如清单 5-5 所示。该脚本包含四个函数，分别对应 `Calculator` 接口中的函数。

**清单 5-5. calculatorasfunctions.js 文件的内容**

`// calculatorasfunctions.js`

`function add(n1, n2) {`

`n1 + n2;`

`}`

`function subtract(n1, n2) {`

`n1 - n2;`

`}`

`function multiply(n1, n2) {`

`n1 * n2;`

`}`

`function divide(n1, n2) {`

`n1 / n2;`

`}`

这两个函数为 `Calculator` 接口的四个方法提供了实现。在函数被 JavaScript 引擎编译后，你可以获取 `Calculator` 接口的一个实例，如下所示：

`// 将引擎引用转换为 Invocable 类型`

`Invocable inv = (Invocable)engine;`

`// 获取 Calculator 接口的引用`

`Calculator calc = inv.getInterface(Calculator.class);`

`if (calc == null) {`

`System.err.println("未找到 Calculator 接口的实现。");`

`}`

`else {`

`// 使用 calc 调用 Calculator 接口的方法`

`}`

你可以像这样对两个数进行加法运算：

`int sum = calc.add(15, 10);`

清单 5-6 展示了如何使用 Nashorn 中的顶层过程实现 Java 接口。请查阅脚本语言（非 Nashorn）的文档，了解其如何支持此功能。

**清单 5-6. 在脚本中使用顶层函数实现 Java 接口**

`// UsingInterfaces.java`

`package com.jdojo.script;`

`import javax.script.Invocable;`

`import javax.script.ScriptEngine;`

`import javax.script.ScriptEngineManager;`

`import javax.script.ScriptException;`

`public class UsingInterfaces {`

`public static void main(String[] args) {`

`// 获取 Nashorn 引擎`

`ScriptEngineManager manager = new ScriptEngineManager();`

`ScriptEngine engine = manager.getEngineByName("JavaScript");`

`// 确保脚本引擎实现了 Invocable 接口`

`if (!(engine instanceof Invocable)) {`

`System.out.println("脚本中的接口实现不受支持。");`

`return;`

`}`

`// 将引擎引用转换为 Invocable 类型`

`Invocable inv = (Invocable) engine;`

`// 为 add() 和 subtract() 函数创建脚本`

`String scriptPath  = "calculatorasfunctions.js";`

`try {`

`// 编译脚本，该脚本将存储在引擎中`

`engine.eval("load('" + scriptPath + "')");`

`// 获取接口实现`

`Calculator calc = inv.getInterface(Calculator.class);`

`if (calc == null) {`

`System.err.println("未找到 Calculator 接口的实现。");`

`return;`

`}`

`double x = 15.0;`

`double y = 10.0;`

`double addResult = calc.add(x, y);`

`double subResult = calc.subtract(x, y);`



`double mulResult = calc.multiply(x, y);`

`double divResult = calc.divide(x, y);`

`System.out.printf(                           "calc.add(%.2f, %.2f) = %.2f%n", x, y, addResult);`

`System.out.printf(                           "calc.subtract(%.2f, %.2f) = %.2f%n", x, y, subResult);`

`System.out.printf(                           "calc.multiply(%.2f, %.2f) = %.2f%n", x, y, mulResult);`

`System.out.printf(                           "calc.divide(%.2f, %.2f) = %.2f%n", x, y, divResult);`

`}`

`catch (ScriptException e) {`

`e.printStackTrace();`

`}`

`}`

`}`

`calc.add(15.00, 10.00) = 25.00`

`calcr.subtract(15.00, 10.00) = 5.00`

`calcr.multiply(15.00, 10.00) = 150.00`

`calc.divide(15.00, 10.00) = 1.50`

Nashorn 引擎是如何找到 `Calculator` 接口的实现呢？当你调用 `Invocable` 的 `getInterface(Class<T> cls)` 方法时，引擎会在已编译的函数中查找与指定类中抽象方法名称相匹配的函数。在我们的例子中，引擎会在其中查找名为 `add`、`subtract`、`multiply` 和 `divide` 的已编译函数。请注意，必须调用引擎的 `eval()` 方法来编译 `calculatorasfunctions.js` 文件中的函数。Nashorn 引擎不会匹配 Java 接口方法中的形式参数数量与引擎中脚本函数的参数数量。

`getInterface()` 方法的第二个版本用于获取一个 Java 接口的实例，该接口的方法被实现为某个对象的实例方法。它的第一个参数是在脚本语言中创建的对象的引用。该对象的实例方法实现了作为第二个参数传入的接口类型。清单 5-2 中的代码创建了一个名为 `calculator` 的对象，其实例方法实现了 `Calculator` 接口。你将把 `calculator` 对象的方法作为 Java 中 `Calculator` 接口的实现来使用。

当脚本对象的实例方法实现了 Java 接口的方法时，你需要额外执行一个步骤。在获取接口的实例之前，你需要先获取脚本对象的引用，如下所示：

`// 在引擎中加载 calculator 对象`

`engine.load('calculator.js');`

`// 获取全局脚本对象 calculator 的引用`

`Object calc = engine.get("calculator");`

`// 获取 Calculator 接口的实现`

`Calculator calculator = inv.getInterface(calc, Calculator.class);`

清单 5-7 展示了如何使用 Nashorn 将 Java 接口的方法实现为对象的实例方法。

清单 5-7. 在脚本中将 Java 接口的方法实现为对象的实例方法

`// ScriptObjectImplInterface.java`

`package com.jdojo.script;`

`import javax.script.Invocable;`

`import javax.script.ScriptEngine;`

`import javax.script.ScriptEngineManager;`

`import javax.script.ScriptException;`

`public class ScriptObjectImplInterface {`

`public static void main(String[] args) {`

`// 获取 Nashorn 引擎`

`ScriptEngineManager manager = new ScriptEngineManager();`

`ScriptEngine engine = manager.getEngineByName("JavaScript");`

`// 确保引擎实现了 Invocable 接口`

`if (!(engine instanceof Invocable)) {`

`System.out.println("脚本中不支持接口实现。");`

`return;`

`}`

`// 将引擎引用转换为 Invocable 类型`

`Invocable inv = (Invocable)engine;`

`String scriptPath  = "calculator.js";`

`try {`

`// 编译并将脚本存储在引擎中`

`engine.eval("load('" + scriptPath + "')");`

`// 获取全局脚本对象 calc 的引用`

`Object scriptCalc = engine.get("calculator");`

`// 获取 Calculator 接口的实现`

`Calculator calc = inv.getInterface(scriptCalc, Calculator.class);`

`if (calc == null) {`

`System.err.println("未找到 Calculator 接口的实现。");`

`return;`

`}`

`double x = 15.0;`

`double y = 10.0;`

`double addResult = calc.add(x, y);`

`double subResult = calc.subtract(x, y);`

`double mulResult = calc.multiply(x, y);`

`double divResult = calc.divide(x, y);`

`System.out.printf("calc.add(%.2f, %.2f) = %.2f%n", x, y, addResult);`

`System.out.printf("calc.subtract(%.2f, %.2f) = %.2f%n", x, y, subResult);`

`System.out.printf("calc.multiply(%.2f, %.2f) = %.2f%n", x, y, mulResult);`

`System.out.printf("calc.divide(%.2f, %.2f) = %.2f%n", x, y, divResult);`

`}`

`catch (ScriptException e) {`

`e.printStackTrace();`

`}`

`}`

`}`

`calc.add(15.00, 10.00) = 25.00`

`calcr.subtract(15.00, 10.00) = 5.00`

`calcr.multiply(15.00, 10.00) = 150.00`

`calc.divide(15.00, 10.00) = 1.50`



## 使用编译脚本

脚本引擎可能允许编译脚本并重复执行。执行编译后的脚本可以提高应用程序的性能。脚本引擎可以以 Java 类、Java 类文件或特定于语言的形式编译和存储脚本。

并非所有脚本引擎都需要支持脚本编译。支持脚本编译的脚本引擎必须实现 `Compilable` 接口。Nashorn 引擎支持脚本编译。以下代码片段检查脚本引擎是否实现了 `Compilable` 接口：

`// 获取脚本引擎引用`

`ScriptEngineManager manager = new ScriptEngineManager();`

`ScriptEngine engine = manager.getEngineByName("YOUR_ENGINE_NAME");`

`if (engine instanceof Compilable) {`

`System.out.println("支持脚本编译。");`

`}`

`else {`

`System.out.println("不支持脚本编译。");`

`}`

一旦知道脚本引擎实现了 `Compilable` 接口，就可以将其引用转换为 `Compilable` 类型，如下所示：

`// 将引擎引用转换为 Compilable 类型`

`Compilable comp = (Compilable)engine;`

`Compilable` 接口包含两个方法：

*   `CompiledScript compile(String script) throws ScriptException`
*   `CompiledScript compile(Reader script) throws ScriptException`

这两个版本的方法仅在脚本来源的类型上有所不同。第一个版本接受一个 `String` 类型的脚本，第二个版本接受一个 `Reader` 类型的脚本。

`compile()` 方法返回一个 `CompiledScript` 类的对象。`CompiledScript` 是一个抽象类。脚本引擎的提供者提供了该类的具体实现。`CompiledScript` 与创建它的 `ScriptEngine` 相关联。`CompiledScript` 类的 `getEngine()` 方法返回与之关联的 `ScriptEngine` 的引用。

要执行编译后的脚本，需要调用 `CompiledScript` 类中的以下 `eval()` 方法之一：

*   `Object eval() throws ScriptException`
*   `Object eval(Bindings bindings) throws ScriptException`
*   `Object eval(ScriptContext context) throws ScriptException`

不带任何参数的 `eval()` 方法使用脚本引擎的默认脚本上下文来执行编译后的脚本。其他两个版本的工作方式与 `ScriptEngine` 接口的 `eval()` 方法在向其传递 `Bindings` 或 `ScriptContext` 时相同。

提示

当使用 `CompiledScript` 类的 `eval()` 方法评估脚本时，在编译脚本执行期间对引擎状态所做的更改可能会在引擎后续执行脚本时可见。

清单 5-8 展示了如何编译脚本并执行它。它使用不同的参数两次执行相同的编译脚本。

清单 5-8. 使用编译脚本

`// CompilableTest .java`

`package com.jdojo.script;`

`import javax.script.Bindings;`

`import javax.script.Compilable;`

`import javax.script.CompiledScript;`

`import javax.script.ScriptEngine;`

`import javax.script.ScriptEngineManager;`

`import javax.script.ScriptException;`

`public class CompilableTest {`

`public static void main(String[] args) {`

`// 获取 Nashorn 引擎`

`ScriptEngineManager manager = new ScriptEngineManager();`

`ScriptEngine engine = manager.getEngineByName("JavaScript");`

`if (!(engine instanceof Compilable)) {`

`System.out.println("不支持脚本编译。");`

`return;`

`}`

`// 将引擎引用转换为 Compilable 类型`

`Compilable comp = (Compilable)engine;`

`try {`

`// 编译一个脚本`

`String script = "print(n1 + n2)";`

`CompiledScript cScript = comp.compile(script);`

`// 将 n1 和 n2 脚本变量存储在 Bindings 中`

`Bindings scriptParams = engine.createBindings();`

`scriptParams.put("n1", 2);`

`scriptParams.put("n2", 3);`

`cScript.eval(scriptParams);`

`// 使用不同的 n1 和 n2 值再次执行脚本`

`scriptParams.put("n1", 9);`

`scriptParams.put("n2", 7);`

`cScript.eval(scriptParams);`

`}`

`catch (ScriptException e) {`

`e.printStackTrace();`

`}`

`}`

`}`

`5`

`16`

## 总结

Java 脚本 API 支持直接从 Java 调用以脚本语言编写的过程、函数和方法。这通过 `Invocable` 接口实现。如果脚本引擎支持过程调用，则它实现 `Invocable` 接口。Nashorn 引擎支持过程调用。被调用的过程可以实现为顶层函数或对象的方法。`Invocable` 接口的 `invokeFunction()` 方法用于调用脚本中的顶层函数。`Invocable` 接口的 `invokeMethod()` 方法用于调用对象的方法。在调用它们之前，必须由引擎评估顶层函数和其方法被调用的对象。

Java 脚本 API 还允许您在脚本语言中实现 Java 接口。Java 接口的方法可以实现为顶层函数或对象的方法。`Invocable` 接口的 `getInterface()` 方法用于获取 Java 接口的实现。

Java 脚本 API 还允许您一次编译脚本，将其存储在脚本引擎中，并多次执行该脚本。这通过 `Compilable` 接口支持。支持脚本编译的脚本引擎需要实现 `Compilable` 接口。您需要调用 `Compilable` 接口的 `compile()` 方法来编译脚本。`compile()` 方法返回一个 `CompiledScript` 实例，调用其 `eval()` 方法来执行脚本。

请注意，脚本引擎实现 `Invocable` 和 `Compilable` 接口是可选的。在调用过程和编译脚本之前，您需要检查脚本引擎是否是这些接口的实例，将引擎转换为这些类型，然后执行这些接口的方法。

