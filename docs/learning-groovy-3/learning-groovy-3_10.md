# 8. 特质（Traits）

特质类似于带有默认实现和状态的接口。

熟悉 Java 8 的人都知道，它为接口添加了默认方法特性。特质与 Java 8 的接口类似，但额外具备了拥有状态（字段）的能力。这提供了更大的灵活性，但应谨慎使用。

## 定义特质

使用 `trait` 关键字定义特质：

```
1   trait Animal {
2       int hunger = 100
3       def  eat() { println "eating"; hunger -= 1 }
4       abstract int getNumberOfLegs()
5   }
```

正如这段代码所示，特质可以拥有属性、方法和抽象方法。如果一个类实现了某个特质，它必须实现该特质的抽象方法。

## 使用特质

要使用特质，你需要使用 `implements` 关键字。例如：

```
1   class Rocket  {
2       String name
3       def  launch() { println(name + "Take off!") }
4   }
5   trait MoonLander {
6       def land() { println("${getName()} Landing!") }
7       abstract String getName()
8   }
9   class  Apollo  extends  Rocket implements  MoonLander {
10   }
```

请注意，由于 `Rocket` 类拥有 `name` 属性，Groovy 自动实现了抽象的 `getName()` 方法。

所以现在你可以执行以下操作：

```
1   def  apollo = new  Apollo(name: "Apollo 12")
2   apollo.launch()
3   apollo.land()
```

这将生成以下输出：

```
1   Apollo 12 Take off!
2   Apollo 12 Landing!
```

与超类不同，你可以在一个类中使用多个特质。以下是一个示例：

```
1   trait Shuttle {
2       boolean canFly() { true }
3       abstract int getCargoBaySize()
4   }
5   class MoonShuttle  extends  Rocket
6       implements  MoonLander, Shuttle {
7       int getCargoBaySize() { 100 }
8   }
```

这将允许你执行以下操作：

```
1   MoonShuttle m = new  MoonShuttle(name: 'Taxi')
2   println "${m.name} can fly? ${m.canFly()}"
3   println "cargo bay: ${m.getCargoBaySize()}"
4   m.launch()
5   m.land()
```

给定上述代码，你将得到以下输出：

```
1   Taxi can fly? true
2   cargo bay: 100
3   Taxi Take off!
4   Taxi Landing!
```

### ![../images/426440_2_En_8_Chapter/426440_2_En_8_Figa_HTML.gif](img/426440_2_En_8_Figa_HTML.gif) 练习

看看当你在两个不同的特质中拥有相同的字段或方法，然后尝试将它们混合时会发生什么。



## 总结

在本章中，你学习了以下 Groovy 特性：

*   什么是特质（Traits），它与 Java 8 的接口类似。

*   如何定义包含字段和方法的特质。

*   如何在一个类中使用多个特质。

