# 输出
Practice03: Hello World from HelloProvider in the lib jar!
清单 2-25
运行从库中调用不同类方法的 Java 文件
```

这些特性为进修学生和有抱负的开发者提供了尝试 Java 代码的机会，而不会因为需要安装并熟悉大量工具（仅仅为了在控制台打印一条消息）而感到不知所措。

Java 21 引入了使用简化 Java 方言运行单个 Java 文件的可能性。在编写本章时，该功能仍处于预览阶段，但在 Java 23 发布时，它可能会成为正式功能。

Java 22 增加了运行多个 Java 文件的可能性。包含 `main(..)` 方法的类可以引用其他文件中的类，只要它们与主类位于同一目录（同级或嵌套目录）中，就可以正常工作。当一个类位于目录或嵌套目录结构中时，该类被视为位于一个包中，因此 Java 文件必须包含包声明。例如，让我们考虑清单 2-26 中的两个 Java 文件。

```
├── Practice04.java
└──  com
└── provider
└── HelloProvider.java
清单 2-26
目录层次结构中的 HelloProvider.java Java 文件
```

`HelloProvider.java` 文件位于 `com/provider` 下，并在 `Practice04.java` 文件中被引用。`HelloProvider.java` 的主体以 `package com.provider;` 语句开头（你将在**第** **3** **章**中了解包），如清单 2-27 所示。

```
package com.provider;
public class HelloProvider {
public static String get(){
return "Hello World from com.provider.HelloProvider!";
}
}
清单 2-27
HelloProvider.java 内容
```

在 `Practice04.java` 文件中引用它时，必须使用包名，如清单 2-28 所示。

```
class Practice04 {
public static void main(String[] args) {
System.out.println("Practice04: " +  com.provider.HelloProvider.get());
}
}
清单 2-28
目录层次结构中的 HelloProvider.java Java 文件
```

运行 `Practice04.java` 文件的命令及其输出如清单 2-29 所示。请注意，无需任何参数即可确保 Java 能找到 `com/provider/HelloProvider.java`。

```
> java Practice04.java
