# 3. 初步分析——从朴素到灵活

在本节中，我将针对各种过滤需求开发传统的解决方案。我避免使用 Lambda 或 Stream，以便此处展示的技术可以与第 4 章中开发的解决方案进行比较。

## 固定过滤

第一个任务是列出所有年龄小于 20 岁的客户。这很容易做到；我们只需要一个带有过滤条件的循环来选择年轻客户，以及一个目标列表来收集他们（见清单 3-1）。

```
1   private List getPersonsLessThan20Years(List persons){
2     List result = new ArrayList();
3     for (Person person : persons) {
4       if (person.getAge() < 20) {
5         result.add(person);
6       }
7     }
8     return result;
9   }
清单 3-1.
选择并收集年龄小于 20 岁人员的简单实现
```

## 简单参数化

下一个需求是收集年龄在 30 到 40 岁之间的人群。当然，我们意识到将来可能需要查询不同的年龄段；因此，最好将方法参数化，而不是硬编码条件（见清单 3-2）。

```
1   private List getPersonsByAgeRange(
2                                   List persons,
3                                   int from,
4                                   int to) {
5   List result = new ArrayList();
6     for (Person person : persons) {
7       if (person.getAge() >= from && person.getAge() <= to) {
8         result.add(person);
9       }
10     }
11     return result;
12   }
清单 3-2.
选择指定年龄段所有人员的参数化实现
```

在这里，开发者引入了一些灵活性。然而，此方法仍然只能选择指定年龄段的人员。如果需要额外的条件，例如查询性别，此方法就无能为力了。新手程序员可能会尝试通过为性别、卖家、状态等添加额外参数来解决问题（见清单 3-3）。

```
1   private List getPersonsByDiverseCriteria(
2                                     List persons,
3                                     int ageFrom,
4                                     int ageTo,
5                                     Gender gender,
6                                     boolean isCustomer,
7                                     boolean isVendor) {
8     [循环省略]
9   }
清单 3-3.
使用（过多）参数重载方法
```

资深开发者可能会对这种天真的代码摇头；他们的经验告诉他们，总有一天你将无法进行分析，因为你至少还需要一个参数。

## 行为参数化

迈向更好解决方案的下一个演进步骤是，将条件或过滤器创建为一个独立的对象，并将其传递给现在更通用的方法。这允许使用不同的行为或不同的算法对方法进行参数化，这让我们想起了策略模式。

对于我们的任务，此行为将实现一个接口，该接口包含一个根据指定条件选择人员的测试。让我们将此接口称为 `Condition`（见清单 3-4）。

```
1   public interface Condition {
2     boolean test(T t);
3   }
清单 3-4.
接口 Condition
```

现在，我们的方法（循环）只需要两个参数：人员列表和条件（清单 3-5）。

```
1   private List getPersonsByCondition(List persons, \

3                         Condition condition){
4     List result = new ArrayList();
5     for (Person person : persons) {
6       if (condition.test(person)) {
7         result.add(person);
8       }
9     }
10     return result;
11   }
清单 3-5.
由于可注入的条件而实现的灵活过滤
```

条件被抽离出来，并通过参数注入。因此，当我们需要不同的过滤器时，无需更改方法的实现。现在，让我们重构我们的第一个分析，以获取所有年龄小于 20 岁的人（见清单 3-6）。

```
1   class YoungerThanCondition implements Condition{
2     private final int _age;
3     YoungerThanCondition(int age){
4       _age = age;
5     }

7     @Override
8     public boolean test(Person person) {
9       return person.getAge() < _age;
10     }
11   }
清单 3-6.
根据接口 Condition 实现 Condition
```

现在，调用我们的循环并注入过滤器的代码看起来更加简洁明了（清单 3-7）。

```
1   persons = getPersonsByCondition(persons, new YoungerThanCondition(20));
清单 3-7.
使用过滤器调用循环
```

遵循面向对象范式，我们将条件作为对象传递给方法。现在，如果我们需要其他过滤条件，我们只需创建不同的过滤器类作为 `Condition` 接口的实现。用于收集感兴趣人员的循环保持不变。



## 匿名类

然而，为每个不同的条件创建一个单独的类仍然显得过于笨重。问题在于：“如果我们只需要在某个地方使用这个条件，能否就在需要它的地方创建这个类？”这正是匿名类发挥作用的地方（参见代码清单 3-8）。

```
1   persons = getPersonsByCondition(persons, new Condition(){
2         @Override
3         public boolean test(Person person) {
4           return person.getAge() < 20;
5         }
6       });
代码清单 3-8.
使用匿名类进行参数化
```

由于匿名类仅在需要它的地方创建，因此无法复用。将年龄作为参数传递没有意义；我们只需将其直接写入条件中。

与功能完备的过滤器类相比，匿名类要简短得多。但是，我们不是传递一个简短的类名作为参数，而是必须重写 `test` 方法并编写几行代码。匿名类比功能完备的类更短，但将代码移到了参数中；这似乎并不理想。顺便说一句——很多程序员不喜欢匿名类。

