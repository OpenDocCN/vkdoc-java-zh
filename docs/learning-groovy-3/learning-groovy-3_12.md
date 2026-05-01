# 10. Groovy GPars

GPars^(²¹) 是一个开源项目，旨在为 Java 和/或 Groovy 带来多种并发抽象。它曾短暂地捆绑在 Groovy 中，后来被分离出来。

它包含了并行 map/reduce、Actor、Dataflow 以及许多其他并发模型。尽管任何为 Java 编写的并发库（如 RxJava、Project Reactor 或 Akka）都可以与 Groovy 一起使用，但 GPars 是专门为 Groovy 设计的。

## 入门指南

首先，你需要在项目中引入 GPars。例如，在 Gradle 构建文件中，将以下内容添加到 `dependencies` 块中：

```
compile "org.codehaus.gpars:gpars:1.2.1"
```

对于 Maven 构建，将以下内容添加到依赖项中：

```
<dependency>
    <groupId>org.codehaus.gpars</groupId>
    <artifactId>gpars</artifactId>
    <version>1.2.1</version>
</dependency>
```

## 并行 Map Reduce

在本节中，我们将使用一个包含毕业年份和 GPA 的学生列表。

```
class Student { int graduationYear; double gpa; }
// 创建一个学生列表
Collection<Student> students = new ArrayList<>()
```

你可以通过以下方式使用 GPars 库执行并行 map/reduce：

```
1   GParsPool.withPool {
2       // 一种 map-reduce 函数式风格
3       def bestGpa = students.parallel
4           .filter{ s -> s.graduationYear==Student.THIS_YEAR }
5           .map{ s -> s.gpa }
6           .max()
7   }
```

静态方法 `GParsPool.withPool` 接收一个闭包，并使用多种方法（通过 Groovy 的 Category 机制）增强任何集合。`parallel` 方法实际上从给定的集合创建了一个 `ParallelArray`（JSR-166^(²²)），并对其进行了轻量封装。^(²³)

## Actors

*Actor 设计模式* 是开发并发软件的一种有用模式。在该模式中，每个 Actor 在自己的线程中执行并操作自己的数据。数据不能被任何其他线程操作。消息（由 GPars 内部）在 Actor 之间传递，以促使它们更改数据。你也可以创建无状态的 Actor。

当数据一次只能被一个线程更改时，这被称为*线程安全*。

```
0   @Grab("org.codehaus.gpars:gpars:1.2.1")
1   import groovyx.gpars.actor.Actor
2   import groovyx.gpars.actor.DefaultActor

4   class Dragon extends DefaultActor {
5       int age

7       void afterStart() {
8           age = new Random().nextInt(1000) + 1
9       }
11       void act() {
12           loop {
13               react { int num ->
14                 if (num > age)
15                 reply 'too old'
16                 else if (num < age)
17                 reply 'too young'
18                 else  {
19                   reply 'you guessed right!'
20                   terminate()
21                 }
22               }
23           }
24       }
25   }
26   // 猜测龙的年龄
27   class Guesser extends DefaultActor {
28       String name
29       Actor server
30       int myNum

32       void act() {
33           loop {
34             myNum = new Random().nextInt(1000) + 1
35             server.send myNum
36             react {
37               switch (it) {
38                 case 'too old': println "$name: $myNum was too old"; break
39                 case 'too young': println "$name: $myNum was too young"; break
40                 default: println "$name: I won $myNum"; terminate(); break
41               }
42             }
43           }
44         }
45   }

47   def  master = new  Dragon().start()
48   def player = new Guesser(name: 'Guesser', server: master).start()

50   // 这强制主线程保持活跃，直到两个 Actor 都停止
51   [master, player]*.join()
```

这里，`Dragon` 类以一个介于 1 到 1000 之间的随机年龄开始。然后它对给定的数字做出响应，回复该数字是太大、太小还是与其年龄相同。`Guesser` 类循环执行，每次循环生成一个随机猜测，并将其发送给 `Dragon`（称为 `server`）。然后 `Guesser` 对来自 `Dragon` 的消息做出响应，并在猜对年龄时终止。

输出将类似于以下内容：

```
Guesser: 236 was too young
... 许多其他猜测
Guesser: 819 was too old
Guesser: I won 527
```

## 更多 GPars 特性

有关 GPars 其他部分的更多信息，例如 Agents（与 Actor 非常相似，但以函数作为消息）、Dataflow（一种具有确定性行为的替代并发模型）和 STM（软件事务内存，为开发者提供处理内存状态的事务语法），请参阅 GPars 优秀的在线指南。^(²⁴)

脚注 1   2   3   4

