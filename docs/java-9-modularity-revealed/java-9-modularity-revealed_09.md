# 9. 新的模块 API

Project Jigsaw 在 JDK 9 中引入了一个新层——模块，并为 Java 平台增加了许多新特性。在这些新特性中，引入了一个新的模块 API 用于处理与模块相关的工作。新的模块 API 被添加在 `java.base` 模块的 `java.lang` 和 `java.lang.module` 包中。它包含一组类、接口、枚举和异常，可用于处理模块。

模块 API 允许我们对模块执行不同的操作，例如从模块描述符中提取信息、访问模块的资源、在模块路径上搜索模块或搜索所有系统模块、创建层等等。模块 API 还可用于在运行时动态地向其他模块添加 `reads`、`opens` 或 `exports` `directives`。本章首先简要介绍新模块 API 的结构：其接口、类、枚举和异常。

根据 JDK 9 API 规范，新增了两个用于处理模块的接口。表 9-1 展示了模块 API 的新接口。

**表 9-1. 模块 API 的接口**

| 名称 | 类型 | 描述 |
| --- | --- | --- |
| `ModuleFinder` | 接口 | 在解析或服务绑定期间查找模块 |
| `ModuleReader` | 接口 | 访问模块的内容 |

表 9-2 列出了新模块 API 的类。

**表 9-2. 模块 API 的类**

| 名称 | 类型 | 描述 |
| --- | --- | --- |
| `Configuration` | 类 | 包含可读性图的配置 |
| `Module` | 类 | 运行时的模块 |
| `ModuleDescriptor` | 类 | 模块描述符 |
| `ModuleDescriptor.Builder` | 类 | 用于创建模块描述符的构建器 |
| `ModuleDescriptor.Exports` | 类 | 由模块导出的包 |
| `ModuleDescriptor.Opens` | 类 | 由模块开放的包 |
| `ModuleDescriptor.Provides` | 类 | 由模块提供其实现的服务 |
| `ModuleDescriptor.Requires` | 类 | 对某个模块的依赖 |
| `ModuleDescriptor.Version` | 类 | 模块版本 |
| `ModuleReference` | 类 | 对模块的引用 |
| `ResolvedModule` | 类 | 已解析模块图中的模块 |

表 9-3 展示了模块 API 的枚举。

**表 9-3. 模块 API 的枚举**

| 名称 | 类型 | 描述 |
| --- | --- | --- |
| `ModuleDescriptor.Exports.Modifier` | 枚举 | 导出包上的修饰符 |
| `ModuleDescriptor.Modifier` | 枚举 | 模块上的修饰符 |
| `ModuleDescriptor.Opens. Modifier` | 枚举 | 开放包上的修饰符 |
| `ModuleDescriptor.Requires.Modifier` | 枚举 | 模块依赖上的修饰符 |

表 9-4 展示了模块 API 的异常。

**表 9-4. 模块 API 的异常**

| 名称 | 类型 | 描述 |
| --- | --- | --- |
| `FindException` | 异常 | 查找模块时发生错误时抛出 |
| `InvalidModuleDescriptorException` | 异常 | 模块描述符格式无效时抛出 |
| `ResolutionException` | 异常 | 解析一组模块的过程失败时抛出 |
| `LayerInstantiationException` | 异常 | 创建模块层时发生错误时抛出 |

接下来，我们将详细探讨新模块 API 中一些最重要的类和接口，从基础的 `Module` 类开始。

## Module 类

`Module` 类表示运行时的模块，它可以是一个命名模块或一个未命名模块。`Module` 类是在 Java 9 中引入的，位于 `java.base` 模块的 `java.lang` 包中，具体位置如下：

```
\java.base\share\clasess\java\lang\Module.java
```

让我们来探索 `Module` 类定义的属性、构造器和方法。

### 属性

以下是 `Module` 类定义的一些最重要的属性：

*   `ModuleLayer` `layer`：表示包含此模块的层。该层也可以为 null。
*   `String name`：表示此模块的名称。
*   `ClassLoader` `loader`：表示此模块的 `ClassLoader`。
*   `ModuleDescriptor` `descriptor`：表示此模块的 `ModuleDescriptor`。
*   `Map<String`, `Set<Module>> exportedPackages`：表示由此模块导出的包。
*   `Map<String`, `Set<Module>> openPackages`：表示对其他模块开放的包。
*   `Set<Module> reads`：表示此模块读取的模块。
*   `static final Module ALL_UNNAMED_MODULE`：表示一个特殊的模块，它定义了所有未命名模块的整个集合。未命名模块没有定义 `ClassLoader` 对象、`ModuleLayer` 对象和 `ModuleDescriptor` 对象。

### 构造器

根据 JDK 9 API 规范，`Module` 类有三种类型的构造器：

*   `Module(ClassLoader loader)`：此构造器为给定的 `ClassLoader` 创建未命名模块。`ClassLoader` 对象也可以为 null。未命名模块不包含 `ModuleDescriptor` 和 `ModuleLayer`。
*   `Module(ClassLoader loader, ModuleDescriptor descriptor)`：此构造器创建一个不在 `ModuleLayer` 内的命名模块。
*   `Module(ModuleLayer layer, ClassLoader loader, ModuleDescriptor descriptor, URI uri)`：此构造器在 `ModuleLayer` 内创建一个命名模块，这意味着该模块在虚拟机内部表示。



### 方法

根据 JDK 9 API 规范，`Module` 类定义的最重要方法如下：

*   `boolean isNamed()`：如果模块是具名模块则返回 true，否则返回 false。
*   `String getName()`：如果模块是具名模块，则返回模块名称。如果模块位于未命名模块中，则返回 null。
*   `ClassLoader getClassLoader()`：返回此模块的 `ClassLoader`。
*   `ModuleDescriptor getDescriptor()`：如果模块是具名模块，则返回此模块的 `ModuleDescriptor`。如果模块是未命名模块，则返回 null。
*   `ModuleLayer getLayer()`：返回包含此模块的 `ModuleLayer`。如果此模块不在模块层中，则返回 null。如果模块位于未命名模块中，则返回 null。
*   `boolean canRead(Module other)`：如果此模块读取了作为参数给定的模块，则返回 true，否则返回 false。如果我们的模块是未命名模块，则始终返回 true，因为未命名模块会读取所有模块。
*   `Module addReads(Module other)`：更新此模块以使其读取给定的模块。
*   `boolean isExported(String packageName)`：如果此模块导出了给定的包，则返回 true。如果我们的模块位于未命名模块中，则默认返回 true。如果包是开放的，则此方法返回 true，因为开放的包在运行时也会被导出。
*   `boolean isExported(String packageName, Module other)`：如果此模块至少向给定的模块导出了给定的包，则返回 true。
*   `boolean isOpen(String packageName)`：如果此模块已开放给定的包，则返回 true。如果模块位于未命名模块中，则始终返回 true。
*   `boolean isOpen(String packageName, Module other)`：如果此模块至少向作为第二个参数指定的给定模块开放了给定的包，则返回 true。
*   `Module addExports(String packageName, Module other)`：更新此模块以将给定的包导出到给定的模块。
*   `Module addOpens(String packageName, Module other)`：更新此模块以将给定的包开放给给定的模块。
*   `Module addUses(Class<?> service)`：更新此模块以添加对给定服务类型的服务依赖。
*   `boolean canUse(Class<?> service)`：如果此模块具有对给定服务类型的服务依赖，则返回 true。如果我们的模块是自动模块或位于未命名模块中，则此方法默认返回 true。
*   `Set<String> getPackages()`：返回此模块中所有包的包名称集合。
*   `InputStream getResourceAsStream(String name)`：返回一个 `InputStream` 对象，用于读取此模块中的资源。该资源由给定的名称标识。

## java.lang.Class 中的变更

为了更好地适配新添加的模块系统，`java.lang.Class` 类通过三个方法得到了增强。根据 JDK 9 API 规范，以下是 Java 9 中在 `Class` 类内部添加的方法：

*   `Class<?> forName(Module module, String className)`：从给定的模块返回给定类的 `Class` 对象。
*   `Module getModule()`：返回此类或接口所属的模块。
*   `String getPackageName()`：返回一个表示此类包名称的 `String`。

## ModuleDescriptor 类

`ModuleDescriptor` 类位于 java.base 模块的 java.lang.module 包中。此类表示具名模块的模块描述符。此类的实例表示从 `module-info.class` 文件获取的模块描述符。

由于 `ModuleDescriptor` 对象是不可变的，因此获取 `ModuleDescriptor` 对象很简单。可以通过在相应的 `Module` 对象上调用 `getDescriptor()` 方法来获取它：

```
java.lang.Module myModule = MyClass.class.getModule();
java.lang.ModuleDescriptor myModuleDescriptor = myModule.getDescriptor();
```

但这并不是我们获取 `ModuleDescriptor` 对象的唯一方法。我们可以使用 `Builder` 类的 `build()` 方法来创建 `ModuleDescriptor`，`Builder` 类是 `ModuleDescriptor` 类的内部类。使用 `Builder` 类创建 `ModuleDescriptor` 并非常用方法，因此本书不对此进行介绍。

注意

模块描述符不能描述未命名模块。它描述了除未命名模块之外的所有现有模块类型：普通模块、开放模块和自动模块。

`ModuleDescriptor` 类包含一组嵌套类，本节稍后将讨论这些类：

*   `ModuleDescriptor.Builder` 类
*   `ModuleDescriptor.Exports` 类
*   `ModuleDescriptor.Modifier` 类
*   `ModuleDescriptor.Opens` 类
*   `ModuleDescriptor.Provides` 类
*   `ModuleDescriptor.Requires` 类
*   `ModuleDescriptor.Version` 类

其中四个嵌套类（`ModuleDescriptor.Exports`、`ModuleDescriptor.Opens`、`ModuleDescriptor.Provides` 和 `ModuleDescriptor.Requires`）的实例表示可以位于模块声明文件 `module-info.java` 中的语句：`exports`、`opens`、`provides` 和 `requires`。

接下来，我们将查看 `ModuleDescriptor` 类内部定义的一些属性。

### ModuleDescriptor 属性

`ModuleDescriptor` 类定义的最重要属性如下，它们将在下一小节中更详细地描述：

*   `String name`
*   `Version version`
*   `Set<Modifier> modifiers`
*   `boolean open`
*   `boolean automatic`
*   `Set<Requires> requires`
*   `Set<Exports> exports`
*   `Set<Opens> opens`
*   `Set<String> uses`
*   `Set<Provides> provides`
*   `Set<String> packages`
*   `String mainClass`
*   `static enum Modifier {OPEN, AUTOMATIC, SYNTHETIC, MANDATED}`

接下来是：`ModuleDescriptor` 类最重要的方法。

### ModuleDescriptor 方法

JDK 9 API 规范为 `ModuleDescriptor` 类定义了几个方法。`ModuleDescriptor` 类最重要的方法如下：

*   `String name()`：返回模块的名称。
*   `Set<Modifier> modifiers()`：返回表示模块修饰符的 `Modifier` 枚举集合。`Modifier` 枚举包含以下值：`OPEN`、`AUTOMATIC`、`SYNTHETHIC` 和 `MANDATE`。`Modifier.OPEN` 表示开放模块。`Modifier.AUTOMATIC` 表示自动模块。`Modifier.SYNTHETIC` 指定该模块既不是显式声明的，也不是隐式声明的。`Modifier.MANDATED` 表示该模块是隐式声明的。
*   `boolean isOpen()`：如果模块是开放的，则返回 true，否则返回 false。
*   `boolean isAutomatic()`：如果这是自动模块，则返回 true，否则返回 false。
*   `Set<Requires> requires()`：返回表示模块依赖关系的 `Requires` 对象集合。
*   `Set<Exports> exports()`：返回表示模块导出包的 `Exports` 对象集合。
*   `Set<Opens> opens()`：返回表示开放包的 `Opens` 对象集合。
*   `Set<String> uses()`：返回表示模块服务依赖关系的 `String` 集合。
*   `Set<Provides> provides()`：返回表示模块提供的服务的 `Provides` 对象集合。
*   `Optional<Version> version()`：返回模块的版本。
*   `String toNameAndVersion()`：返回格式为 `<module_name>@<version>` 的模块名称和版本。
*   `Optional<String> mainClass()`：返回模块的 `main` 类。
*   `Set<String> packages()`：返回表示模块中包的 `String` 集合。

以下部分将重点介绍 `ModuleDescriptor` 类的嵌套类。



### ModuleDescriptor.Requires 类

`ModuleDescriptor.Requires` 类的实例表示模块描述符中的 `requires` 子句，它包含一些属性和方法，下文将进行介绍。

它包含一个名为 `Modifier` 的枚举，其值如下：

*   `TRANSITIVE`：正如 JDK 9 官方文档所述，“此依赖关系会导致任何依赖当前模块的模块，隐式地声明对 `requires` 所指定模块的依赖。”
*   `STATIC`：此依赖关系在编译时是必需的，但在运行时是可选的。
*   `SYNTHETIC`：此依赖关系未在模块声明中声明。
*   `MANDATED`：此依赖关系已在模块声明中声明。

`ModuleDescriptor.Requires` 类定义的方法如下：

*   `Set<Modifier> modifiers()`：返回一组 `Modifier` 对象
*   `String name()`：返回模块的名称
*   `Optional<Version> compiledVersion()`：返回一个 `Version` 对象，表示模块的版本
*   `Optional<String> rawCompiledVersion()`：返回一个 `String`，表示模块的不可解析版本

### ModuleDescriptor.Exports 类

`ModuleDescriptor.Exports` 类可被实例化，用于表示模块声明中的 `exports` 子句。该类包含一个名为 `Modifier` 的枚举，其值如下：`SYNTHETIC` 和 `MANDATED`。

以下是该类定义的方法：

*   `Set<Modifier> modifiers()`：返回一组 `Modifier` 对象
*   `boolean isQualified()`：如果导出是限定性的，则返回 true，否则返回 false
*   `String source()`：返回一个 `String`，表示包的名称
*   `Set<String> targets()`：返回一个集合，表示包被导出到的模块名称。如果导出是非限定性的，则返回一个空集合。

### ModuleDescriptor.Opens 类

`ModuleDescriptor.Opens` 类可被实例化，用于表示模块声明中的 `opens` 子句。该类包含一个名为 `Modifier` 的枚举，其值如下：`Modifier.SYNTHETIC` 和 `Modifier.MANDATED`。在这种情况下，`MANDATED` 表示 `opens` 语句已在模块声明中声明，而 `SYNTHETIC` 表示 `opens` 语句未在模块声明中声明。

根据 JDK 9 API 规范，该类定义的方法如下：

*   `Set<Modifier> modifiers()`：返回一组 `Modifier` 对象。
*   `boolean isQualified()`：如果是限定性的 `opens` 操作，则返回 true，否则返回 false。限定性 `opens` 操作的特点是，包被开放到的模块在模块声明中指定。非限定性 `opens` 操作不在模块声明中指定任何模块，这意味着该包对所有模块开放。
*   `String source()`：以 `String` 形式返回包的名称。
*   `Set<String> targets()`：返回一个 `String` 集合，表示包被开放到的模块名称，但对于非限定性 `opens`，它返回一个空集合。

### ModuleDescriptor.Provides 类

可以说，`ModuleDescriptor.Provides` 类对应于 module-info.java 文件中的 `provides` 语句。回顾一下，`provides` 语句的作用是定义一个服务类型。`provides` 语句的语法如下：`provides <接口名称> with <类名称>`，其中 `<类名称>` 表示由名为 `<接口名称>` 的接口定义的服务类型的实现类。

`ModuleDescriptor.Provides` 类定义了以下变量：`String` service 和 `List<String>` providers。这意味着我们可以创建一个 `ModuleDescriptor.Provides` 类的实例来定义一个服务和一个或多个提供者。

`ModuleDescriptor.Provides` 类定义了两个方法：

*   `String service()`：此方法返回服务类型的完全限定类名。
*   `List<String> providers()`：此方法返回一个 `String` 列表，表示提供者或提供者工厂的完全限定类名。

此外，我们可以在模块描述符中定义三个 `provides` 语句：

```
module com.apress.myModule {
provides ServiceType1 with package1.Class1;
provides ServiceType1 with package1.Class2;
provides ServiceType1 with package2.Class3;
}
```

因为我们只有一个服务类型的实例，所以我们也只有一个 `ModuleDescriptor.Provides` 类的实例。我们可以通过调用 `providers()` 方法获取所有提供者类的名称，并通过调用 `service()` 方法获取服务类型的名称。

### ModuleDescriptor.Version 类

嵌套类 `ModuleDescriptor.Version` 表示模块的版本。模块的版本仅用于文档目的，因为 JPMS 不支持版本控制。`ModuleDescriptor.Version` 类提供的最常用的方法如下：

*   `Version parse(String)`：将给定的 `String` 解析为版本字符串
*   `int compareTo(Version version)`：将此模块版本与给定的模块版本进行比较

## ModuleFinder 接口

根据 JDK 9 API 规范，此接口“表示模块的查找器，用于在解析或服务绑定期间查找方法。”以下是此接口包含的方法：

*   `Optional<ModuleReference> find(String moduleName)`：查找并返回一个 `ModuleReference` 对象，该对象指向名称作为参数传递的模块
*   `Set<ModuleReference> findAll()`：返回一个集合，包含系统中可以定位的所有 `ModuleReference` 对象
*   `static ModuleFinder ofSystem()`：返回一个 `ModuleFinder` 对象，用于定位 Java 运行时中的所有系统模块
*   `static ModuleFinder of(Path… entries)`：返回一个 `ModuleFinder` 对象，通过搜索一系列目录或打包模块来定位文件系统上的模块

`ModuleFinder` 接口的作用是查找模块。一个 `ModuleFinder` 只能查找单个模块。它不能查找多个模块。如果我们在目录内搜索模块，`ModuleFinder` 将只检索找到的第一个模块。

例如，如果我们有两个目录，并且想要找到一个名为 myModule 的模块，我们可以首先通过将目录序列作为参数传递给 `ModuleFinder` 接口的 `of()` 方法来获取 `MethodFinder`：

```
ModuleFinder moduleFinder = ModuleFinder.of(directoryA, directoryB);
```

我们可以通过传递要搜索的模块名称来调用 `ModuleFinder` 对象上的 `find()` 方法：

```
Optional moduleReference = moduleFinder.find("myModule");
```

`find()` 方法返回一个指向名为 myModule 的模块的 `ModuleReference` 对象。

从 `ModuleReference` 对象，我们可以派生出一个 `ModuleDescriptor` 对象：

```
if(moduleReference.isPresent()) {
ModuleDescriptor moduleDescriptor = moduleReference.get().descriptor();
}
```

`findAll()` 方法返回一个集合，包含所有可以定位的模块引用。因此，我们可以找到位于这两个目录中的所有模块。



## ModuleReader 接口

`ModuleReader` 接口的作用是帮助访问模块的内容。正如 JDK 9 官方 API 文档所述：“模块读取器适用于需要访问模块中资源的情况，无论该模块是否已被加载。例如，一个扫描文件系统上打包模块集合的框架，可以使用模块读取器来访问每个模块中的特定资源。”

JDK 9 API 规范为 `ModuleReader` 接口定义了几个方法。`ModuleReader` 接口定义的最重要的方法如下：

*   `Optional<URI> find(String resourceName)`：此方法查找由名称 `resourceName` 标识的资源。它返回一个指向模块中资源的 URI 对象。如果模块读取器已关闭，则可能抛出 I/O 异常。
*   `Optional<InputStream> open(String resourceName)`：此方法打开一个名为 `resourceName` 的资源。它返回一个 `InputStream` 对象，用于读取模块中的资源。
*   `Optional<ByteBuffer> read(String resourceName)`：此方法读取给定的资源，并返回一个包含资源内容的 `ByteBuffer` 对象。
*   `Stream<String> list()`：此方法列出模块的内容。它返回一个 `String` 对象的 `Stream`，表示模块中所有资源的名称。与 `find(resourceName)` 和 `open(resourceName)` 方法一样，如果模块读取器已关闭，则可能抛出 I/O 异常。

下面的示例使用我们目前所学的内容从模块中读取一些信息。我们将在 java.base 模块内搜索所有实现类。一旦找到它们，我们就加载它们，然后打印它们的名称和包名。

清单 9-1 展示了 `ModuleReaderExample` 类，它从 java.base 模块加载所有实现类，然后打印它们的名称和包名：

```
package com.apress.apimodule;
import java.io.IOException;
import java.io.UncheckedIOException;
import java.lang.module.ModuleFinder;
import java.lang.module.ModuleReader;
import java.lang.module.ModuleReference;
import java.util.*;
public class ModuleReaderExample {
public static void main(String[] args) {
List> listClasses = getClassesByModuleName("java.base");
for(Class myClass : listClasses) {
System.out.println("Name of the class is: " + myClass.getName());
System.out.println("Name of the package is: " + myClass.getPackageName());
}
}
private static List> getClassesByModuleName(String moduleName) {
ModuleFinder finder = ModuleFinder.ofSystem();
Optional optionalModuleReference = finder.find(moduleName);
ModuleReference moduleReference = optionalModuleReference.get();
try (ModuleReader moduleReader = moduleReference.open()) {
return moduleReader.list()
.filter(name -> name.endsWith("Impl.class"))
.map(ModuleReaderExample::classLoadByFileName)
.collect(Collectors.toList());
} catch(IOException ioException) {
throw new UncheckedIOException(ioException);
}
}
private static Class classLoadByFileName(String classFileName) {
ClassLoader classLoader = ModuleReaderExample.class.getClassLoader();
String nameOfClass = classFileName.substring(0, classFileName.length() - ".class".length());
try {
nameOfClass = nameOfClass.replace('/','.');
return classLoader.loadClass(nameOfClass);
}
catch (ClassNotFoundException classNotFoundException) {
throw new UncheckedIOException(new IOException(classNotFoundException));
}
}
}
清单 9-1.
类 ModuleReaderExample
```

方法 `static Class<?> classLoadByFileName(String classFileName)` 很简单。它使用 `ClassLoader` 对象加载给定的类，并返回一个 `Class` 对象的实例。方法 `List<Class<?>> getClassesByModuleName(String moduleName)` 很有趣。它返回位于给定模块中的 `Class` 对象列表。首先，我们通过调用 `ofSystem()` 方法获取 `ModuleFinder` 的实例。此方法返回一个定位系统模块的模块查找器。然后，我们通过在 `finder` 对象上调用 `find(moduleName)` 方法来查找名为 moduleName 的模块的引用：

```
Optional optionalModuleReference = finder.find(moduleName);
ModuleReference moduleReference = optionalModuleReference.get();
```

`ModuleReference` 对象是对模块内容的引用——在我们的例子中，是 java.base 模块。为了打开模块进行读取，我们在 `ModuleReference` 对象上调用 `open()` 方法。这样我们就获得了一个 `ModuleReader` 对象的实例：

```
ModuleReader moduleReader = moduleReference.open()
```

接下来，我们在 `ModuleReader` 对象上调用 `list()` 方法，并通过仅搜索名称以 `Impl.class` 结尾的类来过滤结果。最后，我们调用 `classLoadByFileName()` 方法来获取相应类的 `Class` 对象。在 `main` 方法中，我们加载 java.base 模块的所有实现类，并打印它们的名称及其包名。

输出内容非常庞大，因此我们这里只展示一小部分：

```
Name of the class is: sun.util.locale.provider.BreakIteratorProviderImpl
Name of the package is: sun.util.locale.provider
Name of the class is: java.lang.ProcessImpl
Name of the package is: java.lang
Name of the class is: java.util.jar.JavaUtilJarAccessImpl
Name of the package is: java.util.jar
...
```

## 对模块执行操作

本节介绍了一些我们可以通过编程方式对模块执行的操作，例如获取类的模块、访问模块的资源、搜索模块路径中的所有模块或获取模块信息。

### 获取类的模块

正如我们在本章中已经学到的，运行时的模块由定义在 java.base 模块的 java.lang 包中的 `Module` 类表示。`Module` 类可以表示命名模块或未命名模块。为了为我们名为 `ModuleCore` 的类返回一个 `Module` 对象，我们调用 `Class` 类的 `getModule()` 方法：

```
Class myClass = ModuleCore.class;
Module module = myClass.getModule();
```

此方法返回 `ModuleCore` 类所属的模块。如果该类位于未命名模块中，则会调用 `ClassLoader.java` 中的 `getUnnamedModule()` 方法。

### 访问模块的资源

清单 9-2 展示了如何使用 `getResourceAsStream()` 方法访问模块的资源，该方法返回一个 `InputStream` 对象。

```
this.class.getModule().getResourceAsStream("file.properties");
清单 9-2.
使用 getResourceAsStream() 方法访问模块的资源
```

我们也可以使用 `getResource()` 方法访问模块的资源，该方法返回一个 URL 对象，如清单 9-3 所示。

```
this.getClass().getResource("file.properties")
ClassLoader.getPlatformClassLoader().getResource("file.properties")
清单 9-3.
使用 getResource() 方法访问模块的资源
```

### 搜索模块路径中的所有模块

使用新的模块 API，我们甚至可以找到模块路径中的所有模块。清单 9-4 展示了如何在系统环境变量 `jdk.module.path` 中搜索模块路径中的所有模块，获取它们的模块描述符，并打印它们的模块名称。

```
ModuleFinder.of(Paths.get(System.getProperty("jdk.module.path"))).
.findAll()
.stream()
.forEach(ref -> {
System.out.println(moduleReference.descriptor().name());
});
清单 9-4.
搜索模块路径中的所有模块
```



### 获取模块信息

利用本章介绍的方法和类，我们可以获取模块的完整信息。清单 9-5 展示了一个示例，该示例使用新的类和接口来获取 `java.base` 模块的所有可用信息。

```
import java.lang.module.ModuleDescriptor;
import java.lang.module.ModuleFinder;
import java.lang.module.ModuleReference;
import java.util.NoSuchElementException;
import java.util.Optional;
import java.util.Set;
public class BaseModule {
public static void main(String[] args) {
String moduleName;
Optional mainClass;
Set exports;
boolean isAutomatic;
boolean isOpen;
Set allPackagesNames;
Set provides;
Set dependencies;
String moduleNameVersion;
Set serviceDependencies;
ModuleDescriptor.Version version;
ModuleFinder finder = ModuleFinder.ofSystem();
Optional moduleReference = finder.find("java.base");
if(moduleReference.isPresent()) {
ModuleDescriptor moduleDescriptor = moduleReference.get().descriptor();
// 获取模块名称
moduleName = moduleDescriptor.name();
// 获取模块的主类
mainClass = moduleDescriptor.mainClass();
exports = moduleDescriptor.exports();
isAutomatic = moduleDescriptor.isAutomatic();
isOpen = moduleDescriptor.isOpen();
allPackagesNames = moduleDescriptor.packages();
provides = moduleDescriptor.provides();
dependencies = moduleDescriptor.requires();
moduleNameVersion = moduleDescriptor.toNameAndVersion();
serviceDependencies = moduleDescriptor.uses();
try {
Optional versionOptional = moduleDescriptor.version();
version = versionOptional.get();
}
catch (NoSuchElementException exception) {
version = null;
}
System.out.println("模块名称是: " + moduleName);
System.out.println();
System.out.println("主类是: ");
if(mainClass.isPresent())  {
System.out.println(mainClass);
}
else {
System.out.println("不存在");
}
System.out.println();
System.out.println("该模块导出了以下名称的包: ");
for(ModuleDescriptor.Exports moduleExport : exports) {
System.out.print(moduleExport.source());
System.out.print(", ");
}
System.out.println();
System.out.println();
System.out.println("是否为自动模块: " + isAutomatic);
System.out.println();
System.out.println("是否为开放模块: " + isOpen);
System.out.println();
System.out.println("所有包名称: ");
for(String packageName : allPackagesNames) {
System.out.print(packageName);
System.out.print(", ");
}
System.out.println();
System.out.println();
System.out.println("该模块提供的服务: ");
for(ModuleDescriptor.Provides provide : provides) {
System.out.print("服务 " + provide.service());
for(String p : provide.providers()) {
System.out.print(" 提供者: " + p);
System.out.print(", ");
}
}
System.out.println();
System.out.println("该模块依赖项的名称: ");
for(ModuleDescriptor.Requires dependency : dependencies) {
System.out.print(dependency.name());
System.out.print(", ");
}
System.out.println();
System.out.println("模块名称和版本: " + moduleNameVersion);
System.out.println();
System.out.println("该模块的服务依赖项: ");
for(String serviceDependency : serviceDependencies) {
System.out.print(serviceDependency);
System.out.print(", ");
}
System.out.println();
System.out.println("该模块的版本: " + version);
}
}
}
清单 9-5.
打印来自模块 java.base 的详细信息
```

我们使用 `ModuleFinder` 接口来定位所有系统模块。在返回的对象上，我们调用 `find()` 方法，并传入代表模块名称的字符串 `"java.base"`。这将返回一个指向 `java.base` 模块的 `ModuleReference`。我们使用 `isPresent()` 方法验证是否找到了 `ModuleReference`。接着，我们在 `ModuleReference` 对象上调用 `descriptor()` 方法来获取模块描述符。`ModuleDescriptor` 对象包含模块的全面信息，例如其名称、`main` 类、包、依赖项、服务依赖项、提供的服务、版本名称等。我们检索这些信息并将其打印出来。清单 9-6 仅展示了输出中最重要的部分。

```
模块名称是: java.base
主类是:
不存在
该模块导出了以下名称的包:
jdk.internal.module, javax.net.ssl, java.time.format, java.nio.charset.spi, sun.security.ssl, sun.security.pkcs, sun.security.internal.interfaces, jdk.internal.util.jar, java.security.interfaces, sun.util.logging, jdk.internal.perf, java.util.function, sun.net.util, jdk.internal.misc, javax.security.auth.login, sun.security.x509, sun.security.rsa, jdk.internal.util.xml, jdk.internal, java.util.jar, java.util.regex, sun.security.action, jdk.internal.jmod, java.util.stream,
.........................................................
是否为自动模块: false
是否为开放模块: false
所有包名称:
jdk.internal.org.objectweb.asm.signature, sun.text.bidi, sun.text.normalizer, sun.security.action, sun.util.logging, sun.security.internal.interfaces, jdk.internal.jimage.decompressor, jdk.internal.util.jar, java.net.spi, sun.reflect.generics.factory, sun.util.resources.cldr, sun.security.tools, com.sun.java.util.jar.pack, java.text.spi, java.nio, jdk.internal.ref, sun.security.tools.keytool, java.security.spec, sun.security.util, java.nio.channels.spi, sun.net.www.protocol.ftp, java.util, sun.util.cldr, sun.reflect.generics.reflectiveObjects, java.util.spi, java.lang.ref,
..............................
该模块提供的服务:
服务 java.nio.file.spi.FileSystemProvider 提供者: jdk.internal.jrtfs.JrtFileSystemProvider,
该模块依赖项的名称:
模块名称和版本: java.base@9
该模块的服务依赖项:
java.util.spi.LocaleNameProvider, jdk.internal.logger.DefaultLoggerFinder, java.lang.System$LoggerFinder, sun.util.resources.LocaleData$SupplementaryResourceBundleProvider, java.text.spi.NumberFormatProvider, java.time.chrono.Chronology, java.util.spi.CalendarNameProvider, java.text.spi.DateFormatSymbolsProvider, java.time.zone.ZoneRulesProvider, sun.text.spi.JavaTimeDateTimePatternProvider, java.text.spi.DecimalFormatSymbolsProvider
..........................
该模块的版本: 9
清单 9-6.
运行上述打印模块 java.base 信息的代码后的输出
```

注意

前述示例的源代码可以在文件夹 `/ch09/moduleDescriptorJavaBase` 中找到。



## 摘要

本章讨论了 Java 9 中引入的新模块 API，它为我们提供了访问模块及模块内部信息的手段。

你了解了新模块 API 中包含的类、接口、枚举和异常类型。我们详细介绍了 `java.lang.Module` 类及其属性、构造器和方法。接着展示了 `java.lang.Class` 类如何通过三个实用的方法得到增强。此外，还深入讲解了新的 `ModuleDescriptor` 类，不仅说明了其属性和方法，还介绍了它的嵌套类，如 `ModuleDescriptor.Requires`、`ModuleDescriptor.Exports`、`ModuleDescriptor.Opens`、`ModuleDescriptor.Provides` 和 `ModuleDescriptor.Version`。我们讨论了新的 `ModuleReader` 和 `ModuleFinder` 接口，并通过示例演示了如何读取模块的内容。借助 `ModuleFinder` 接口，我们搜索了 java.base 模块的所有实现类。找到这些类后，我们使用 `ClassLoader` 类的 `loadClass()` 方法加载它们，然后显示了已加载的类名及其所属包名。

随后，我们通过一些示例演示了如何对模块执行操作，例如获取某个类的模块、访问模块的资源，或在模块路径中搜索所有模块。本章最后讨论了一个 Java 类，它从 java.base 模块中读取所有属性并将其打印到系统控制台。

第 10 章将涵盖与 Jigsaw 相关的一些高级主题，包括层、类加载器、多版本 JAR 文件、JMOD 文件以及可升级模块。层也是模块 API 的一部分。

