# 9. 异常与日志

异常是描述程序中特殊情况的一种方式。它们表明发生了意外（异常）情况。因此，异常能有效地中断当前程序流程，并提示有需要关注的问题。正因如此，合理使用异常的程序能获得更好的控制流，变得更健壮，并为用户提供更多信息。即便如此，不加区分地使用异常可能会导致性能下降。

在 Java 中，异常可以被*抛出*或*捕获*。抛出异常意味着向代码指示遇到了异常，使用 `throw` 关键字通知 JVM 在当前栈中寻找能够处理此异常情况的代码。捕获异常则是告诉编译器哪些异常可以被处理，以及应该监控代码的哪一部分以捕获这些异常。这通过 Java 的 `try`/`catch` 块（如配方 9-1 所述）来表示。

所有异常都继承自 `Throwable` 类，并且可以在 `try`/`catch` 语句的 `catch` 子句中定义。JVM 主要使用 `Error` 类来表示严重和/或致命的错误。根据 Java 文档，应用程序不应捕获 `Error` 异常，因为它们被认为是致命的（可以想象成计算机着火了）。Java 程序中的大多数异常都继承自 `Exception` 类。

在 JVM 中，有两种类型的异常：受检异常和非受检异常。方法强制要求受检异常。你可以在方法签名中指定该方法可能抛出的异常。这要求该方法的任何调用者都必须创建一个 `try`/`catch` 块来处理方法签名中声明的异常。非受检异常不需要严格的约定，可以在任何地方自由抛出，而无需实现 `try`/`catch` 块。即便如此，通常不鼓励使用非受检异常（如配方 9-7 所述），因为它们可能导致线程崩溃（如果没有捕获到异常）以及问题可见性差。继承自 `RuntimeException` 的异常类被视为非受检异常，而直接继承自 `Exception` 的异常类则被视为受检异常。

请注意，抛出异常的操作开销很大（与其他语言结构替代方案相比），因此，用异常来代替控制流是一种糟糕的做法。例如，你不应该抛出异常来表示方法调用的预期结果（例如，像 `isUsernameValid(String username)` 这样的方法）。更好的做法是调用该方法并返回一个布尔值作为结果，而不是抛出 `InvalidUsernameException` 来表示失败。

虽然异常在可靠的软件开发中扮演着重要角色，但记录异常也同样重要。应用程序中的日志记录有助于开发人员在不调试代码的情况下了解发生了哪些事件。这在无法进行实时调试的生产环境中尤其如此。从这个意义上说，日志记录收集了关于正在发生的事情（很可能是出了什么问题）的线索，并帮助你排查生产问题。许多开发人员使用结构化的日志框架来为应用程序提供更健壮的日志记录。一个可靠的日志框架配合扎实的方法论，可以省去许多深夜加班思考“到底发生了什么？”的烦恼。

Java 的日志记录非常成熟。许多开源项目被广泛接受为日志记录的事实标准。在本章的配方中，你将使用 Java 的 Logback 框架和 Java 简单日志门面（SLF4J）。这两个项目共同为大多数日志记录需求提供了一个足够好的解决方案。对于涉及 SLF4J 和 Log4j 的配方，请下载 SLF4J（[`www.slf4j.org`](http://www.slf4j.org)）并将其放入项目的依赖路径中。本章还会涉及 Java 9 中新增的低级别 JVM 日志记录。

## 9.1 捕获异常

### 问题

你希望优雅地处理代码中产生的任何异常。

### 解决方案

使用内置的 `try`/`catch` 语言结构来 `catch`（捕获）异常。具体做法是将任何可能抛出异常的代码块包裹在 `try/catch` 块中。下面的示例生成一个布尔值，用于指示指定字符串的长度是否大于五个字符。如果作为参数传入的字符串是 `null`，则 `length()` 方法会抛出 `NullPointerException`（`RuntimeException` 类型），并在 `catch` 块中被捕获。

```
public class Recipe9_1 {
public static void main(String[] args) {
Recipe9_1 recipe = new Recipe9_1();
recipe.start();
}
private void start() {
System.out.println("字符串 1234 长度是否大于 5 个字符？:"+
isStringShorterThanFiveCharacters("1234"));
System.out.println("字符串 12345 长度是否大于 5 个字符？:"+
isStringShorterThanFiveCharacters("12345"));
System.out.println("字符串 123456 长度是否大于 5 个字符？:"+
isStringShorterThanFiveCharacters("123456"));
System.out.println("字符串 null 长度是否大于 5 个字符？:"+
isStringShorterThanFiveCharacters(null));
}
private boolean isStringShorterThanFiveCharacters(String aString) {
try {
return aString.length() > 5;
} catch (NullPointerException e) {
System.err.println("发生异常：" + e);
return false;
}
}
}
```

### 工作原理

`try` 关键字指定了被包含的代码段可能引发异常。`catch` 子句放在 `try` 子句的末尾。每个 `catch` 子句指定了要捕获的异常。如果没有为受检异常提供 `catch` 子句，编译器会生成错误。两种可能的解决方案是：添加一个 `catch` 子句，或者将异常包含在封闭方法的 `throws` 子句中。任何被抛出但未被捕获的受检异常都会沿着调用栈向上传播。如果此方法没有捕获该异常，执行该代码的线程将终止。如果该线程是程序中唯一的线程，则程序将终止执行。

如果一个 `try` 子句需要捕获多个异常，可以指定多个异常，用竖线字符分隔。例如，以下 `try/catch` 块可用于同时捕获 `NumberFormatException`（一种 `RuntimeException` 类型）和 `NullPointerException`。

```
try {
// 此处代码
} catch (NumberFormatException|NullPointerException ex) {
// 日志记录
}
```

注意

抛出异常时要小心。如果抛出的异常未被捕获，它会沿着调用栈向上传播；如果没有能够处理该异常的 `catch` 子句，它将导致运行中的线程终止（也称为*崩溃*）。如果你的程序只有一个主线程，未捕获的异常将终止你的程序。

## 9.2 确保代码块被执行

### 问题

你希望编写一段代码，当控制权离开某个代码段时（即使是因为抛出错误或代码段异常终止而离开），这段代码也能执行。例如，你获取了一个锁，并希望确保能正确地释放它。你希望在发生错误或没有错误的情况下都能释放该锁。



### 解决方案

使用 `try`/`catch`/`finally` 代码块来正确释放在代码段中获取的锁和其他资源。将无论是否发生异常都希望执行的代码放入 `finally` 子句中。在示例中，`finally` 关键字指定了一个始终执行的代码块，无论 `try` 块中是否抛出了异常。在 `finally` 块中，通过调用 `lock.unlock()` 释放锁。

```
public class Recipe9_2 {
    Lock myLock = new ReentrantLock();
    Random random = new Random();
    public static void main(String[] args) {
        Recipe9_2 recipe = new Recipe9_2();
        recipe.start();
    }
    private void start() {
        for (int i = 0; i < 10; i++) {
            callFunctionThatHoldsLock();
        }
    }
    private void callFunctionThatHoldsLock() {
        myLock.lock();
        try {
            int number = random.nextInt(5);
            int result = 100 / number;
            System.out.println("A result is " + result);
            FileOutputStream file = new FileOutputStream("file.out");
            file.write(result);
            file.close();
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            myLock.unlock();
        }
    }
}
```

### 工作原理

放置在 `try`/`catch`/`finally` 块的 `finally` 子句中的代码始终会被执行。在此示例中，在函数开头获取锁并在 `finally` 块中释放它，可以确保无论是否抛出异常（已检查或未检查），锁都会在函数结束时被释放。总之，获取的锁应始终在 `finally` 块中释放。在示例中，假设 `mylock.unlock()` 函数调用不在 `finally` 块中（而是在 `try` 块的末尾）；如果在这种情况下发生异常，则不会执行 `mylock.unlock()` 调用，因为代码执行会在异常发生的位置中断。在这种情况下，锁将永远被持有而无法释放。

**注意**

如果需要在方法中返回值，请避免在 `finally` 块中返回。`finally` 块中的 `return` 语句始终会执行，无论 `try` 块中可能已经执行了任何其他 `return` 语句。

## 9.3 抛出异常

### 问题

当应用程序中出现特定情况时，希望通过抛出异常来中止当前代码路径的执行。

### 解决方案

当特定情况发生时，使用 `throw` 关键字抛出指定的异常。使用 `throw` 关键字，可以通知当前线程查找 `try`/`catch` 块（在当前层级及调用栈上层），这些块可以处理抛出的异常。在以下示例中，如果传入的参数为 `null`，`callSomeMethodThatMightThrow` 方法会抛出一个 `NullPointerException`（`RuntimeException` 类型）。

```
public class Recipe9_3 {
    public static void main(String[] args) {
        Recipe9_3 recipe = new Recipe9_3();
        recipe.start();
    }
    private void start() {
        try {
            callSomeMethodThatMightThrow(null);
        } catch (IllegalArgumentException e) {
            System.err.println("存在非法参数异常！");
        }
    }
    private void callSomeFunctionThatMightThrow(Object o) {
        if (o == null) throw new NullPointerException("对象为空");
    }
```

在此代码示例中，`callSomeMethodThatMightThrow` 方法检查是否传入了有效参数。如果参数为 `null`，则抛出 `NullPointerException（RuntimeException` 类型`）`，表示该方法的调用者使用了错误的参数调用它。

### 工作原理

`throw` 关键字允许显式生成异常条件。当当前线程抛出异常时，它不会执行 `throw` 语句之后的任何代码，而是将控制权转移到 `catch` 子句（如果有的话），或者终止线程。

**注意**

抛出异常时，请确保你确实有意这样做。如果异常在向上传播过程中未被捕获，它将终止正在执行的线程（也称为*展开*）。如果你的程序只有一个主线程，未捕获的异常将终止你的程序。

## 9.4 捕获多个异常

### 问题

应用程序中的代码块可能抛出多个异常。希望捕获 `try` 块中可能发生的每个异常。

### 解决方案 1

当同一个块中可能遇到多个异常时，可以指定多个 `catch` 子句。每个 `catch` 子句可以指定要处理的不同异常，以便对每个异常进行不同的处理。在以下代码中，两个 `catch` 子句分别处理 `IOException` 和 `ClassNotFoundException`。

```
public static void main(String[] args) {
    Recipe9_4 recipe = new Recipe9_4();
    recipe.startClassic();
}
private void startClassic() {
    try {
        Class stringClass = Class.forName("java.lang.String");
        FileInputStream in = new FileInputStream("myFile.log") ; // 可能抛出 IOException
        in.read();
    } catch (IOException e) {
        System.err.println("发生异常 "+e);
    } catch (ClassNotFoundException e) {
        System.err.println("发生异常 "+e);
    }
}
```

### 解决方案 2

如果应用程序倾向于在单个块中抛出多个异常，则可以使用竖线运算符（`|`）以相同的方式处理每个异常。在以下示例中，`catch` 子句指定了多个异常类型，用竖线（`|`）分隔，以相同的方式处理每个异常。

```
public static void main(String[] args) {
    Recipe9_4 recipe = new Recipe9_4();
    recipe.start();
}
private void start() {
    try {
        Class stringClass = Class.forName("java.lang.String");
        FileInputStream in = new FileInputStream("myFile.log") ;
        // 可能抛出 IOException
        in.read();
    } catch (IOException | ClassNotFoundException e) {
        System.out.println("抛出了类型为 "+e.getClass()+" 的异常！ "+e);
    }
}
```

### 工作原理

处理可能抛出多个异常的情况有多种不同的方式。可以指定单独的 `catch` 子句来分别处理每个异常。要以相同的方式处理每个异常，可以使用单个 `catch` 子句，并用竖线运算符分隔每个异常。

**注意**

如果在多个 `catch` 块中捕获异常（解决方案 1），请确保 `catch` 块的定义顺序是从最具体到最通用。不遵循此约定将导致异常无法处理更具体的块。当存在 `catch (Exception e)` 块（几乎捕获所有异常）时，这一点最为重要。

使用 `catch (Exception e)` 块——称为*捕获所有*或*宝可梦异常处理器*（必须捕获所有）——通常是糟糕的做法，因为这样的块会捕获所有异常类型并同等对待。这会产生问题，因为该块可能会捕获调用栈深处可能发生的、你本意并非要捕获的其他异常（例如 `OutOfMemoryException`）。最好指定每个可能的异常，而不是指定一个捕获所有异常的处理器。

## 9.5 捕获未捕获的异常

### 问题

希望在线程因未捕获的异常（例如 `NullPointerException`）而终止时得到通知。



### 方案 1

创建 Java 线程时，有时需要确保任何异常都能被捕获并妥善处理，以帮助确定线程终止的原因。以下代码演示了如何为每个线程注册异常处理器。

```
public static void main(String[] args) {
Recipe9_5 recipe = new Recipe9_5();
recipe.start();
recipe.startForCurrentThread();
}
private void start() {
Thread.setDefaultUncaughtExceptionHandler((Thread t, Throwable e) -> {
System.out.println("哇！某处抛出了异常！"+t.getName()+": "+e);
});
final Random random = new Random();
for (int j = 0; j < 10; j++) {
int divisor = random.nextInt(4);
System.out.println("200 / " + divisor + " 等于 " + (200 / divisor));
}
}
```

以下是输出结果。

```
200 / 2 等于 100
哇！某处抛出了异常！main: java.lang.ArithmeticException: / by zero
```

该线程中的 `for` 循环会正常执行，直到遇到异常时，`DefaultUncaughtExceptionHandler` 被调用。`UncaughtExceptionHandler` 是一个函数式接口，因此可以使用 lambda 表达式来实现异常处理器。

### 方案 2

可以在特定线程中注册 `UncaughtExceptionHandler`。注册后，该线程内任何未被捕获的异常都将由 `uncaughtException()` 方法处理。以下是一个示例。

```
private void startForCurrentThread() {
Thread.currentThread().setUncaughtExceptionHandler((Thread t, Throwable e) -> {
System.out.println("在线程 "+t.getName()+" 中抛出了异常 "+e);
});
Thread someThread = new Thread(() -> {
System.out.println(200/0);
});
someThread.setName("某个倒霉线程");
someThread.start();
System.out.println("在主线程中 "+ (200/0));
}
```

以下是输出结果。

```
在线程 main 中抛出了异常 java.lang.ArithmeticException: / by zero
```

在上述代码中，`UncaughtExceptionHandler` 被注册在 `currentThread` 上。与方案 1 相同，`UncaughtExceptionHandler` 是一个函数式接口，因此可以使用 lambda 表达式来实现异常处理器。

### 工作原理

对于每个未被捕获的非检查型异常，都会调用 `Thread.defaultUncaughtExceptionHandler()`。当 `UncaughtExceptionHandler()` 处理异常时，意味着没有 `try`/`catch` 块来捕获该异常。因此，异常会沿着线程栈向上冒泡。这是该线程终止前执行的最后一段代码。当异常被线程自身的或默认的 `UncaughtExceptionHandler()` 捕获时，线程就会终止，该处理器会记录异常信息，以帮助定位异常原因。

在方案 2 中，`UncaughtExceptionHandler()` 专门为当前线程设置。当线程抛出未被捕获的异常时，它会向上冒泡到线程中的 `UncaughtExceptionHandler()`。如果该处理器不存在，则会继续冒泡到 `defaultUncaughtExceptionHandler()`。同样，在这两种情况下，抛出异常的线程都会终止。

提示

在处理多线程时，明确地为线程命名始终是一个好习惯。这能让我们更容易地确定是哪个线程导致了异常，而不必去追踪一个名为 `Thread-##`（未命名线程的默认命名模式）的未知线程。

## 9.6 使用 try/catch 块管理资源

### 问题

当发生异常时，需要确保 `try`/`catch` 块中使用的所有资源都被释放。

### 方案

使用自动资源管理（ARM）特性，该特性可以通过 `try`-`with`-`resources` 语句来指定。使用该语句时，`try` 子句中指定的任何资源在块终止时都会自动释放。在以下代码中，`FileOutputStream`、`BufferedOutputStream` 和 `DataOutputStream` 资源由 `try`-`with`-`resources` 块自动管理。

```
public class Recipe9_6 {
public static void main(String []args) {
Recipe9_6 recipe = new Recipe9_6();
recipe.start();
}
private void start() {
try (
FileOutputStream fos = new FileOutputStream("out.log");
BufferedOutputStream bos = new BufferedOutputStream(fos);
DataOutputStream dos = new DataOutputStream(bos)
) {
dos.writeUTF("正在写入内容");
} catch (Exception e) {
System.out.println("发生了一些糟糕的异常");
}
}
```

### 工作原理

在大多数情况下，你希望在 `try`/`catch` 块执行完毕后，干净地关闭/释放块内获取的资源。如果程序没有关闭/释放其资源，或者操作不当，资源可能会被无限期占用，从而导致内存泄漏等问题。大多数资源都是有限的（如文件句柄或数据库连接），因此会导致性能下降（并引发更多异常）。为了避免这些情况，Java 提供了一种在 `try`/`catch` 块内发生异常时自动释放资源的方法。通过声明一个 `try-with-resources` 块，如果块内抛出异常，则 `try` 块所检查的资源会被关闭。Java 内置的大多数资源都能在 `try-with-resources` 语句中正常工作（完整列表请参见 `java.lang.AutoCloseable` 接口的实现者）。此外，第三方实现者可以通过实现 `AutoCloseable` 接口来创建能与 `try-with-resources` 语句配合使用的资源。

`try-with-resources` 语句的语法包括 `try` 关键字，后跟一个左括号，然后是所有希望在发生异常或块完成时释放的资源声明，最后以右括号结束。如果你尝试声明一个未实现 `AutoCloseable` 接口的资源/变量，编译器会报错。在右括号之后，`try`/`catch` 块的语法与普通块相同。

`try-with-resources` 特性的主要优势在于它允许更干净地释放资源。通常，在获取资源时，存在许多相互依赖关系（例如创建文件处理器，然后包装到输出流中，再包装到缓冲流中）。在异常情况下正确关闭和释放这些资源需要检查每个依赖资源的状态并小心处理，这需要编写大量代码。相比之下，`try-with-resources` 结构允许 JVM 即使在异常情况下也能正确地释放资源。

注意

`try-with-resources` 块始终会关闭定义的资源，即使没有抛出任何异常。

## 9.7 创建异常类

### 问题

你想要创建一种新的异常类型，用于指示特定事件。

### 方案 1

创建一个继承 `java.lang.RuntimeException` 的类，以创建一个可以随时抛出的异常类。在以下代码中，名为 `IllegalChatServerException` 的类继承了 `RuntimeException`，并在构造函数中接受一个字符串参数。当代码中发生指定事件时，就会抛出该异常。

```
class IllegalChatServerException extends RuntimeException {
IllegalChatServerException(String message) {
super(message);
}
}
private void disconnectChatServer(Object chatServer) {
if (chatServer == null) throw new IllegalChatServerException("聊天服务器为空");
}
```



### 解决方案 2

创建一个继承自 `java.lang.Exception` 的类，以生成一个受检异常类。受检异常必须在调用栈中被捕获或重新抛出。在以下示例中，一个名为 `ConnectionUnavailableException` 的类继承了 `java.lang.Exception`，并在构造函数中接受一个字符串作为参数。随后，该受检异常由代码中的一个方法抛出。

```
class ConnectionUnavailableException extends Exception {
ConnectionUnavailableException(String message) {
super(message);
}
}
private void sendChat(String chatMessage) throws ConnectionUnavailableException {
if (chatServer == null)
throw new ConnectionUnavailableException("Can't find the chat server");
}
```

主方法如下所示。

```
Object chatServer = null;
public static void main (String[] args) {
Recipe9_7 recipe = new Recipe9_7();
recipe.start();
}
private void start() {
try {
sendChat("Hello, how are you?");
} catch (ConnectionUnavailableException e) {
System.out.println("捕获到一个连接不可用异常！");
}
disconnectChatServer(chatServer);
}
```

### 工作原理

有时需要创建自定义异常，尤其是在创建 API 时。通常的建议是使用 JDK 提供的现有 `Exception` 类之一。例如，对于 I/O 相关问题使用 `IOException`，对于非法参数使用 `IllegalArgumentException`。如果没有完全匹配的 JDK 异常，你总是可以继承 `java.lang.Exception` 或 `java.lang.RuntimeException` 并实现自己的异常体系。

根据基类的不同，创建 `Exception` 类相当直接。继承 `RuntimeException` 允许你在任何时候抛出生成的异常，而无需在调用栈中捕获它。这样做的好处是 `RuntimeException` 是一种更宽松的契约，但如果异常未被捕获，抛出此类异常可能导致线程终止。而继承 `Exception` 则强制任何抛出该异常的代码在 `catch` 子句中处理它。受检异常通过契约强制实现 `catch` 处理器，从而可能避免线程终止。

在实践中，我们不鼓励继承 `RuntimeException`，因为它可能导致糟糕的异常处理。我们的经验法则是：如果可以从异常中恢复，你应该通过继承 `Exception` 来创建相关的异常类。如果开发者无法合理地从此异常中恢复（例如 `NullPointerException`），则继承 `RuntimeException`。

## 9.8 在应用程序中记录事件

### 问题

你希望在应用程序中记录事件、调试消息、错误条件以及其他事件。

### 解决方案

在你的应用程序中使用 Java 日志 API 来实现日志记录方案。以下示例首先创建了一个名为 `recipeLogger` 的日志记录器对象。在此示例中，Java 日志记录器记录了一条信息性消息、一条警告消息和一条错误消息。

```
private void loadLoggingConfiguration() {
FileInputStream ins = null;
try {
ins = new FileInputStream(new File("logging.properties"));
LogManager.getLogManager().readConfiguration(ins);
} catch (IOException e) {
e.printStackTrace();
}
}
private void start() {
loadLoggingConfiguration();
Logger logger = Logger.getLogger("recipeLogger");
logger.info("首次记录日志！");
logger.warn("一个需要发出的警告");
logger.severe("这是一个错误！");
}
public static void main (String[] args) {
Recipe9_8 recipe = new Recipe9_8();
recipe.start();
}
```

以下输出定义在 `logging.properties` 文件中。

```
dic 02, 2021 10:27:38 PM org.java17recipes.chapter09.recipe09_08.Recipe9_8 start
INFO: 首次记录日志！
dic 02, 2021 10:27:38 PM org.java17recipes.chapter09.recipe09_08.Recipe9_8 start
WARNING: 一个需要发出的警告
dic 02, 2021 10:27:38 PM org.java17recipes.chapter09.recipe09_08.Recipe9_8 start
SEVERE: 这是一个错误！
```

使用 JDK 在 conf 文件夹中的默认属性文件，并将其复制到你的项目文件夹中。

以下是项目主文件夹中的 `logging.properties` 文件。

```
handlers = java.util.logging.FileHandler
recipeLogger.level=INFO
.level=ALL
java.util.logging.FileHandler.formatter=java.util.logging.SimpleFormatter
java.util.logging.FileHandler.pattern=LogApplication%g.log
java.util.logging.FileHandler.limit=50000
java.util.logging.FileHandler.count=4
```

### 工作原理

在示例中，`loadLoggingConfiguration()` 函数打开一个指向 `logging.properties` 文件的流，并将其传递给 `java.util.logging.LogManager()`。这样做可以配置 `java.util.logging` 框架，使其使用 `logging.properties` 文件中指定的设置。然后，在解决方案的 start 方法中，代码获取一个名为 `recipeLogger` 的 `logger` 对象。该示例继续通过 `recipeLogger` 记录消息。

注意

`java.util.logging` 框架由属性日志文件配置。

## 9.9 日志轮转与清理

### 问题

你已经开始记录日志信息，但记录的信息持续增长，失去控制。你希望只保留日志文件中最后 250 KB 的日志条目。

### 解决方案

使用 `java.util.logging` 配置滚动日志。在此示例中，一个名为 `recipeLogger` 的日志记录器记录了许多消息。输出会生成滚动的日志文件，最新的日志信息保存在重要的 `Log0.log` 文件中。

```
public static void main (String[] args) throws IOException {
Recipe9_9 recipe = new Recipe9_9();
recipe.start();
}
private void start() {
loadLoggingConfiguration();
Logger logger = LoggerFactory.getLogger("recipeLogger");
logger.info("首次记录日志！");
logger.warn("一个需要发出的警告");
logger.severe("这是一个错误！");
Logger rollingLogger = Logger.getLogger("rollingLogger");
for (int i =0;i < 5000;i++) {
rollingLogger.info("记录事件，编号为："+i);
}
}
private void loadLoggingConfiguration() {
FileInputStream ins = null;
try {
ins = new FileInputStream(new File("logging.properties"));
LogManager.getLogManager().readConfiguration(ins);
} catch (IOException e) {
e.printStackTrace();
}
}
```

以下是项目主文件夹中的 `logging.properties` 文件。

```
handlers = java.util.logging.FileHandler
recipeLogger.level=INFO
.level=ALL
java.util.logging.FileHandler.formatter=java.util.logging.SimpleFormatter
java.util.logging.FileHandler.pattern=LogApplication%g.log
java.util.logging.FileHandler.limit=50000
java.util.logging.FileHandler.count=4
```



### 工作原理

要控制日志文件的大小，可配置 `java.util.logging` 框架并指定滚动日志文件。选择滚动日志文件选项后，最新信息将保存在 `LogApplication0.log` 中。较早的信息则依次保存在 `LogApplication1.log`、`LogApplication2.log` 等文件中。当 `LogApplication0.log` 填满您指定的上限（本例中为 50,000 字节）时，其文件名将轮转为 `LogApplicationLog1.log`，其他文件也会相应向下轮转。要保留的日志文件数量由 `java.util.logging.FileHandler.count` 属性决定，在本示例中该属性设置为 `4`。用于区分轮转日志的 `FileHandler` 配置参数是 `"%g"`（一个代际编号）。如果在属性文件中未指定该参数，且计数大于 1（本例中为 4），则代际编号会附加在文件名末尾（点号之后）。

`logging.properties` 文件首先定义了 `java.util.logging` 框架使用的处理器。处理器是负责处理日志消息的对象。本示例中指定了 `FileHandler`，它将日志消息写入文件。接下来定义了日志级别。在日志框架中，存在独立的 `logger` 对象这一概念。一个日志记录器可以携带不同的配置（例如不同的日志级别），并在日志文件中被标识。该示例将 `recipeLogger` 的级别设置为 `info`。相比之下，根 `logger` 的级别为 `ALL`（在 `java.util.logging` 框架中，根日志记录器通过属性前无前缀来表示）。另一方面，`OFF` 则禁用日志记录。

`logging.properties` 文件的下一部分定义了 `FileHandler` 的配置。格式化器指示日志信息如何写入磁盘。`simpleFormatter` 以纯文本形式写入信息，包含一行日期和时间、一行日志级别以及要记录的消息。在格式化器之后，定义了 `FileHandler` 的模式。这指定了日志文件的名称和位置（`%g` 会被替换为滚动日志编号 [0 ~ 3]）。`Limit` 属性定义了日志在轮转前可以拥有的字节数（50,000 字节 ~ 50kb）。`count` 定义了要保留的日志文件的最大索引（在本示例中为 4）。

## 9.10 记录异常

在之前的示例中，您学习了如何捕获异常并记录信息。本示例将这两者结合起来。

### 问题

您希望在日志文件中记录异常。

### 解决方案

配置您的应用程序以使用 SLF4J。利用 `try`/`catch` 块在错误日志中记录异常。在以下示例中，一个 SLF4J 日志记录器从异常处理器内部记录消息。

```
public static void main (String [] args) {
Recipe9_10 recipe = new Recipe9_10();
recipe.start();
}
static Logger rootLogger = Logger.getLogger("");
private void start() {
loadLoggingConfiguration();
Thread.setDefaultUncaughtExceptionHandler((Thread t, Throwable e) -> {
rootLogger.error("Error in thread "+t+" caused by "+e);
});
int c = 20/0;
}
private void loadLoggingConfiguration() {
FileInputStream ins = null;
try {
ins = new FileInputStream(new File("logging.properties"));
LogManager.getLogManager().readConfiguration(ins);
} catch (IOException e) {
e.printStackTrace();
}
}
```

### 工作原理

该示例演示了如何使用 `UncaughtExceptionHandler` 将异常记录到日志文件中。记录异常时，最好包含显示异常抛出位置的堆栈跟踪。在示例中，一个线程包含 `UncaughtExceptionHandler`，它使用了一个包含日志记录器的 lambda 表达式。该日志记录器将所有捕获到的异常写入日志文件。

## 9.11 总结

本章探讨了应用程序开发中最重要的阶段之一——异常处理。讨论了如何处理单个和多个异常，以及如何记录这些异常。

