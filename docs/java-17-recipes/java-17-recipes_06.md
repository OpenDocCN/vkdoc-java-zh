# 6. Lambda 表达式

创建匿名函数的一种便捷方式是使用 *lambda 表达式*，它通过一个表达式或一系列语句来实现。Lambda 表达式基于函数式接口构建，这类接口包含一个单一的抽象方法，且该方法没有实现体。

它们可以应用于多种场景，从简单的匿名函数到对集合进行排序和过滤。此外，lambda 表达式可以赋值给变量，然后传递给其他对象。

在本章中，你将学习如何创建 lambda 表达式，并会看到许多关于它们如何在常见场景中应用的示例。你还将学习如何生成 lambda 表达式的构建块，以便构建应用程序来促进其使用。阅读本章后，你将看到 lambda 表达式对 Java 语言的影响。它们通过让开发者更高效，并在许多领域开辟新的可能性，使语言现代化。Lambda 表达式翻开了 Java 的新篇章，使这门语言与其他一些早已拥有类似结构的语言一样，焕发出新的光彩。这些语言为 lambda 表达式在 Java 语言中的出现铺平了道路。毫无疑问，lambda 表达式将继续为许多优雅的解决方案铺平道路。

注意

函数式接口是在 Java 8 中引入的。它是一个恰好包含一个抽象方法的接口（即，任何只有一个抽象方法的接口都是函数式接口）。此外，建议函数式接口带有信息性的 `@FunctionalInterface` 注解。

## 6.1 编写一个简单的 Lambda 表达式

### 问题

你想要封装一个打印简单消息的功能。

### 解决方案

编写一个 lambda 表达式，它接受一个包含要打印消息的单一参数，并在 lambda 内部实现打印功能。在以下示例中，一个函数式接口 `HelloType` 通过 lambda 表达式实现，并赋值给 `helloLambda` 变量。最后，调用该 lambda，打印消息。

```
public class HelloLambda {
/**
* 函数式接口
*/
@FunctionalInterface
public interface HelloType {
/**
* 将在 lambda 内部实现的函数
* @param text
*/
void hello(String text);
}
public static void main(String[] args){
// 创建 lambda，向 hello() 方法传递一个名为 "text" 的参数，返回字符串。
// 该 lambda 被赋值给 helloLambda 变量。
HelloType helloLambda =
(String text) -> {System.out.println("Hello " + text);};
// 调用方法
helloLambda.hello("Lambda");
}
}
```

结果如下。

```
Hello Lambda
```

### 工作原理

Lambda 表达式是一个匿名的代码块，它封装了一个表达式或一系列语句，并返回一个结果。Lambda 表达式在其他一些语言中也称为*闭包*。它们可以接受零个或多个参数，这些参数可以带类型说明传递，也可以不带，因为类型可以从上下文中自动推导。

Lambda 表达式的语法包括一个参数列表、一个语言中新增的字符“箭头标记”（`->`）和一个主体。以下模型表示了 lambda 表达式的结构。

```
(argument list) -> { body }
```

Lambda 表达式的参数列表可以包含零个或多个参数。如果没有参数，则使用一对空括号。如果只有一个参数，则不需要括号。列表中的每个参数可以包含可选的类型说明。如果省略了参数的类型，则类型从当前上下文中推导。

在本配方的解决方案中，花括号包围了一个包含多个表达式的主体。如果主体只包含一个表达式，则不需要花括号。解决方案中的花括号本可以省略，但为了便于阅读而保留。主体被简单地求值然后返回。如果 lambda 的主体是一个表达式而不是语句，则隐式包含 `return`。相反，如果主体包含多个语句，则必须指定 `return`，它标志着将控制权返回给调用者。

以下代码演示了一个不包含任何参数的 lambda 表达式。

```
StringReturn msg = () ->  "This is a test";
```

lambda 使用的 `StringReturn` 接口也是一个函数式接口。

```
/**
* 返回字符串的函数式接口
*/
@FunctionalInterface
public interface StringReturn {
String returnMessage();
}
```

让我们看看这个 lambda 表达式是如何工作的。在上面的代码中，从 lambda 表达式返回了一个 `StringReturn` 类型的对象。空括号表示没有参数传递给表达式。`return` 是隐式的，字符串 `"This is a test"` 从 lambda 表达式返回给调用者。示例中的表达式被赋值给一个名为 `msg` 的变量。假设函数式接口 `StringReturn` 包含一个标识为 `returnMessage()` 的抽象方法，如代码所示。在这种情况下，可以调用 `msg.returnMessage()` 方法来返回该字符串。

Lambda 表达式的主体可以包含普通方法可能包含的任何 Java 构造。例如，假设一个字符串作为参数传递给 lambda 表达式，并且你想要返回一个依赖于该字符串参数的某个值。以下 lambda 表达式主体包含一个代码块，根据传递给表达式的参数（字符串值）返回一个 int。

```
ActionCode code = (codestr) -> {
switch(codestr){
case "ACTIVE": return 0;
case "INACTIVE": return 1;
default:
return -1;
}
};
```

在这个例子中，`ActionCode` 函数式接口推断出 lambda 表达式的返回类型。为了澄清，让我们看看这个接口的样子。

```
@FunctionalInterface
public interface ActionCode{
int returnCode(String codestr);
}
```

这段代码表明，lambda 表达式实现了 `ActionCode` 接口中定义的 `returnCode` 方法。该方法接受一个字符串参数（`codestr`），该参数被传递给 lambda 表达式，并返回一个 int。因此，从这个例子中，你可以看到 lambda 可以封装一个方法体的功能。

虽然不使用 lambda 表达式，用 Java 语言编写的代码也可以继续前进，但它们是重要的补充，极大地提高了整体的可维护性、可读性和开发者的生产力。Lambda 表达式是 Java 语言的一次进化性变革，因为它们朝着语言现代化的方向又迈进了一步，并有助于使其与其他语言保持同步。

注意

Lambda 表达式可以包含普通 Java 方法包含的任何语句。但是，`continue` 和 `break` 关键字在 lambda 表达式的主体中是不合法的。

## 6.2 启用 Lambda 表达式的使用

### 问题

你有兴趣编写能够启用 lambda 表达式使用的代码。



### 解决方案 1

编写可通过 lambda 表达式实现的自定义函数式接口。所有 lambda 表达式都实现一个函数式接口（即仅包含一个抽象方法声明）。以下代码演示了一个包含单个方法声明的函数式接口。

```
@FunctionalInterface
public interface ReverseType {
String reverse(String text);
}
```

该函数式接口包含一个抽象方法声明，标识为 `String reverse(String text)`。以下包含 lambda 表达式的代码演示了如何实现 `ReverseType`。

```
ReverseType newText = (testText) -> {
String tempStr = "";
for (String part : testText.split(" ")) {
tempStr += new StringBuilder(part).reverse().toString() + " ";
}
return tempStr;
};
```

以下代码可调用该 lambda 表达式。

```
System.out.println(newText.reverse("HELLO WORLD"));
```

这是运行结果。

```
OLLEH DLROW
```

### 解决方案 2

使用 `java.util.function` 包中包含的函数式接口来实现 lambda 表达式，以满足应用程序的需求。以下示例使用 `Function<T,R>`（其中 T 是输入类型，R 是结果类型）接口执行与解决方案 1 中演示的相同任务。此示例接受一个字符串参数并返回一个字符串结果。

```
Function newText2 = (testText) -> {
String tempStr = "";
for (String part : testText.split(" ")) {
tempStr += new StringBuilder(part).reverse().toString() + " ";
}
return tempStr;
};
```

此 lambda 表达式被赋值给 `newText2` 变量，其类型为 `Function<String,String>`。因此，一个字符串作为参数传入，并从 lambda 表达式返回一个字符串。`Function<T,R>` 的函数式接口包含一个名为 `apply()` 的抽象方法声明。要调用此 lambda 表达式，请使用以下语法。

```
System.out.println(newText2.apply("HELLO WORLD"));
```

以下是运行结果。

```
OLLEH DLROW
```

### 工作原理

lambda 表达式的基本构建块是函数式接口。这个标准的 Java 接口包含一个抽象方法声明，并为 lambda 表达式和方法引用提供了目标类型。函数式接口可以包含默认方法实现，但只能有一个抽象声明。然后，该抽象方法由 lambda 表达式隐式实现。因此，lambda 表达式可以赋值给与函数式接口类型相同的变量。之后可以在该赋值变量上调用该方法，从而调用 lambda 表达式。遵循此模式，lambda 表达式就是可以通过名称调用的方法实现。它们也可以作为参数传递给其他方法。

此时，您可能会想，是否必须为每个可能适合使用 lambda 表达式的情况都开发一个函数式接口。事实并非如此，因为许多函数式接口已经可供使用。一些示例包括 `java.lang.Runnable`、`javafx.event.EventHandler<T extends Event>` 和 `java.util.Comparator<T>`。请参阅本章中的其他技巧，了解使用实现这些接口的 lambda 表达式的示例。然而，还有更多函数式接口不那么具体，使它们能够根据特定需求进行定制。`java.util.function` 包包含几个在实现 lambda 表达式时可能有用的函数式接口。该包中包含的函数式接口在整个 JDK 中都有使用，也可以在开发人员应用程序中使用。利用 `java.util.function` 包中包含的函数式接口可以大大减少您需要编写的代码量。这些函数式接口针对大多数情况下执行的任务进行了优化。它们还使用泛型编写，可以在许多不同的上下文中应用。解决方案 2 演示了这样一个示例，其中 `Function<T,R>` 接口实现了一个接受字符串参数并返回字符串结果的 lambda 表达式。

## 6.3 按名称调用现有方法

### 问题

您正在开发一个 lambda 表达式，它仅仅调用传递给 lambda 的对象中已存在的方法。与其编写调用该方法的完整仪式代码，不如使用最少的代码。

### 解决方案

使用方法引用来调用现有方法，而不是编写 lambda 表达式。在以下场景中，`Player` 对象包含一个名为 `compareByGoal()` 的静态方法，该方法接受两个 `Player` 对象并比较每个对象的进球数。然后返回一个表示结果的整数。`compareByGoal()` 方法与 `Comparator<T>` 相同。

```
public class Player {
private String firstName ;
private String lastName ;
private String position ;
private int status = -1;
private int goals;
public Player(){
}
public Player(String position, int status){
this.position = position;
this.status = status;
}
public String findPlayerStatus(int status){
String returnValue = null;
switch(status){
case 0:
returnValue = "ACTIVE";
case 1:
returnValue = "INACTIVE";
case 2:
returnValue = "INJURY";
default:
returnValue = "ON_BENCH";
}
return returnValue;
}
public String playerString(){
return getFirstName() + " " + getLastName() + " - " + getPosition();
}
// ** 为简洁起见，省略了 getter 和 setter **
/**
* 如果玩家 A 的进球数多于玩家 B，则返回正整数
* 如果玩家 A 的进球数少于玩家 B，则返回负整数
* 如果玩家 A 和玩家 B 的进球数相同，则返回零
*/
public static int compareByGoal(Player a, Player b){
int eval;
if(a.getGoals() > b.getGoals()){
eval = 1;
} else if (a.getGoals() < b.getGoals()){
eval = -1;
} else {
eval = 0;
}
return eval;
}
}
```

`Player.compareByGoal()` 方法可以对 `Player` 对象数组进行排序。为此，将 `Player` 对象数组（`Player[]`）作为第一个参数传递给 `Arrays.sort()` 方法，并将方法引用 `Player::compareByGoal` 作为第二个参数传递。结果将得到一个按进球数升序排列的 `Player` 对象列表。以下代码行展示了如何完成此任务。

```
Arrays.sort(teamArray, Player::compareByGoal);
```



### 工作原理

假设你的 lambda 表达式将通过名称调用单个方法，并可能返回一个结果。如果 lambda 表达式符合这种情况，那么它就是使用方法引用的理想候选。方法引用是 lambda 表达式的一种简化形式，它指定类名或实例名，后跟要调用的方法，格式如下。

```
::
```

双冒号（`::`）运算符用于指定方法引用。由于方法引用是简化的 lambda 方法，因此它必须实现一个函数式接口。接口中的抽象方法必须与被引用的方法具有相同的参数列表和返回类型。任何参数随后都从方法引用的上下文中推导得出。例如，考虑与解决方案相同的场景，即希望通过调用 `Player.compareByGoal()` 方法来执行进球数比较，从而对 `Player` 对象数组进行排序。以下代码可以通过 lambda 表达式实现此功能。

```
Arrays.sort(teamArray, (p1, p2) -> Player.compareByGoal(p1,p2));
```

在这段代码中，数组作为第一个参数传递给 `Arrays.sort()`。第二个参数是一个 lambda 表达式，它将两个 `Player` 对象传递给 `Player.compareByGoal()` 方法。该 lambda 表达式使用了函数式接口 `Comparator<Player>.compare`，该接口利用了 `(Player, Player)` 参数列表。`compareByGoal()` 方法包含相同的参数列表。同样，`compareByGoal()` 的返回类型与函数式接口中的返回类型匹配。因此，参数列表无需在代码中显式指定。它可以从 `Player::compareByGoal` 方法引用的上下文中推断出来。

方法引用有四种不同的类型。表 6-1 列出了每一种类型。

表 6-1

方法引用类型

| 类型 | 描述 |
| --- | --- |
| 静态引用 | 使用对象的静态方法 |
| 实例引用 | 使用对象的实例方法 |
| 任意对象方法 | 用于特定类型的任意对象，而非特定对象 |
| 构造器引用 | 通过使用 `new` 关键字调用构造器来生成新对象 |

在解决方案中，演示了静态方法引用类型，因为 `compareByGoal()` 是 `Player` 类中的一个静态方法。也可以使用实例引用来调用对象实例的方法。考虑以下类，它包含一个用于比较 `Player` 对象中进球数的非静态方法。

```
public class PlayerUtility {
public int compareByGoal(Player a, Player b){
int eval;
if(a.getGoals() > b.getGoals()){
eval = 1;
} else if (a.getGoals() < b.getGoals()){
eval = -1;
} else {
eval = 0;
}
return eval;
}
}
```

可以实例化此类，新的实例引用 `compareByGoals()` 方法，类似于本方案中使用的技术。

```
Player[] teamArray2 = team.toArray(new Player[team.size()]);
PlayerUtility utility = new PlayerUtility();
Arrays.sort(teamArray2, utility::compareByGoal);
```

假设你的应用程序包含一个任意类型的列表，并且你想对该列表中的每个对象应用一个方法。在这种情况下，如果对象包含适合通过引用使用的方法，则可以使用方法引用。在以下示例中，`Arrays.sort()` 方法应用于一个整数值列表。一个方法引用将 `Integer compare()` 方法应用于列表中的元素。因此，结果列表被排序，并且方法引用自动传递整数参数并返回整数比较结果。

```
Integer[] ints = {3,5,7,8,51,33,1};
Arrays.sort(ints, Integer::compare);
```

最后一种方法引用类型可用于引用对象的构造器。当通过工厂创建新对象时，这种类型的方法引用尤其有用。让我们看一个例子。假设 `Player` 对象包含以下构造器。

```
public Player(String position, int status, String first, String last){
this.position = position;
this.status = status;
this.firstName = first;
this.lastName = last;
}
```

你想使用工厂模式动态生成 `Player` 对象。以下代码演示了一个函数式接口的示例，该接口包含一个名为 `createPlayer()` 的抽象方法，该方法接受与 `Player` 对象构造器相同的参数列表。

```
@FunctionalInterface
public interface PlayerFactory {
Player createPlayer(String position,
int status,
String firstName,
String lastName);
}
```

现在可以从 lambda 表达式创建工厂，然后创建新对象。以下几行代码演示了这一点。

```
PlayerFactory player1 = Player::new;
Player newPlayer = player1.createPlayer("CENTER", 0, "Constructor", "Referenceson");
```

Java 8 中引入的双冒号运算符（`::`）简化了新实例的创建。尽管 lambda 表达式有更多的用例，但方法引用可能是 Java 8 中引入的最重要的新特性之一。它们提供了一种易于阅读、简化的技术来生成 lambda 表达式，并且在大多数 lambda 仅通过名称调用单个方法的情况下都能很好地工作。

## 6.4 用更少的代码行进行排序

### 问题

你的应用程序包含一个曲棍球队的 `Player` 对象列表。你想按照进球数最多的球员对该列表进行排序，并且希望使用简洁且易于理解的代码来实现。

### 解决方案 1

使用 `Player` 对象中包含的访问器方法为要排序的字段创建一个比较器。在这种情况下，你想按进球数排序，因此比较器应基于 `getGoals()` 返回的值。以下代码行展示了如何使用 `Comparator` 接口和方法引用来创建这样的比较器。

```
public static void main(String[] args) {
loadTeam();
Comparator byGoals = Comparator.comparing(Player::getGoals);
```

接下来，结合使用 lambda 表达式和流（有关流的更多信息，请参见第 7 章）以及 `forEach()` 方法，对 `Player` 对象列表应用指定的排序。在以下代码行中，从列表中获取一个流，这允许你对元素应用函数式风格的操作。

```
team.stream().sorted(byGoals)
.map(p -> p.getFirstName() + " " + p.getLastName() + " - "
+ p.getGoals())
.forEach(element -> System.out.println(element));
```

假设由 `team` 引用的列表已加载了 `Player` 对象，则上一行代码首先按球员进球数对该列表进行排序，然后打印每个对象的信息。

以下是排序结果。

```
== 按进球数排序 ==
Jonathan Gennick - 1
Josh Juneau - 5
Steve Adams - 7
Duke Java - 15
Bob Smith - 18
```



### 解决方案 2

利用 `Collections.sort()` 方法，传入待排序的列表以及一个用于比较列表元素的 lambda 表达式。以下代码演示了如何使用 `Collections.sort()` 技术完成此任务。

```
public static void main(String[] args) {
loadTeam();
Collections.sort(team, (p1, p2)
-> p1.getLastName().compareTo(p2.getLastName()));
team.stream().forEach((p) -> {
System.out.println(p.getLastName());
});
```

以下是运行结果。

```
== 按姓氏排序 ==
Adams
Gennick
Java
Juneau
Smith
其中 loadTeam 函数为：
public class Sorter {
static List team;
private static void loadTeam() {
System.out.println("Loading team...");
team = new ArrayList();
Player player1 = new Player();
player1.setFirstName("Josh");
player1.setLastName("Juneau");
player1.setGoals(5);
Player player2 = new Player();
player2.setFirstName("Duke");
player2.setLastName("Java");
player2.setGoals(15);
Player player3 = new Player();
player3.setFirstName("Jonathan");
player3.setLastName("Gennick");
player3.setGoals(1);
Player player4 = new Player();
player4.setFirstName("Bob");
player4.setLastName("Smith");
player4.setGoals(18);
Player player5 = new Player();
player5.setFirstName("Steve");
player5.setLastName("Adams");
player5.setGoals(7);
team.add(player1);
team.add(player2);
team.add(player3);
team.add(player4);
team.add(player5);
}
...
}
```

### 工作原理

Java 8 引入了新特性，极大地提升了开发者对集合进行排序的效率。本解决方案中演示了三个特性：lambda 表达式、方法引用和流。我们将在本书的其他章节中更详细地探讨流，但此处也会简要介绍以帮助理解本方案。流可应用于数据集合，并允许对集合中的元素应用增强的函数式操作。流本身不存储任何数据，而是为从流中获取的集合提供更多功能。

在解决方案 1 中，生成了一个比较器，用于评估 `Player` 对象的进球数（`getGoals`）。然后，从作为团队引用的 `List<Player>` 生成一个流。该流提供了 `sorted()` 函数，该函数接受一个比较器来对数据流进行排序。最初生成的比较器被传递给 `sorted()` 函数，然后对结果调用 `map()` 函数。`map()` 函数可以对流中的每个元素应用一个表达式。因此，在 map 中，该解决方案利用 lambda 表达式创建了一个包含每个 `Player` 对象的 `firstName`、`lastName` 和 `goals` 字段的字符串。最后，由于 `List<Player>` 是可迭代的，它包含了 `forEach()` 方法。`forEach()` 方法允许对列表中的每个元素应用一个表达式或一组语句。在此例中，列表中的每个元素都会被打印到命令行。由于 `map()` 函数已应用于流，列表中的每个元素随后会根据 `map()` 中应用的算法进行打印。最终结果是，球员的名字、姓氏以及每个球员的进球数都会被打印在命令行上。

解决方案 2 使用了不同的技术来完成类似的任务。在列表上调用了 `Collections.sort()` 方法。`Collections.sort()` 的第一个参数是列表本身，第二个参数是以 lambda 表达式形式实现的比较逻辑。此例中的 lambda 表达式接收两个参数，均为 `Player` 对象，并比较第一个球员的姓氏与第二个球员的姓氏。因此，排序是基于 `Player` 对象的 `lastName` 字段按升序进行的。为了完成解决方案 2，排序后的列表被打印出来。为此，从排序后的列表生成一个流，然后对流数据调用 `forEach()` 方法，打印出每个球员的姓氏。

## 6.5 过滤数据集合

### 问题

你有一个数据列表，希望对其应用一些过滤，以便提取出符合指定条件的对象。

### 解决方案

从数据列表创建一个流，并应用过滤器，传入所需的谓词（也称为*条件表达式*）。最后，将每个符合指定过滤条件的对象添加到一个新列表中。在以下示例中，对一个 `Player` 对象列表进行过滤，仅捕获那些进球数达到十个或以上的球员。

```
public static void main(String[] args){
loadTeam();
// 创建数组存储匹配结果
List gteTenGoals = new ArrayList();
team.stream().filter(
p -> p.getGoals() >= 10
&& p.getStatus() == 0)
.forEach(element -> gteTenGoals.add(element));
System.out.println("符合筛选条件的球员数量: " + gteTenGoals.size());
其中 loadTeam 函数为：
public class Filter {
static List team;
private static void loadTeam() {
System.out.println("Loading team...");
team = new ArrayList();
Player player1 = new Player();
player1.setFirstName("Josh");
player1.setLastName("Juneau");
player1.setGoals(5);
player1.setStatus(0);
Player player2 = new Player();
player2.setFirstName("Duke");
player2.setLastName("Java");
player2.setGoals(15);
player2.setStatus(0);
Player player3 = new Player();
player3.setFirstName("Jonathan");
player3.setLastName("Gennick");
player3.setGoals(1);
player3.setStatus(0);
Player player4 = new Player();
player4.setFirstName("Bob");
player4.setLastName("Smith");
player4.setGoals(18);
player4.setStatus(0);
Player player5 = new Player();
player5.setFirstName("Steve");
player5.setLastName("Adams");
player5.setGoals(7);
player5.setStatus(1);
team.add(player1);
team.add(player2);
team.add(player3);
team.add(player4);
team.add(player5);
}
```

### 工作原理

本方案的解决方案利用了数据流，因为它包含一个易于使用的过滤函数。数据集合 `team` 生成一个流，然后对其调用过滤函数，该函数接受一个谓词来过滤集合中的数据。该谓词以 lambda 表达式形式编写，包含两个过滤条件。lambda 表达式将 `Player` 对象作为参数传入，然后根据进球数大于等于十以及活跃状态来过滤数据。

数据过滤完成后，`forEach()` 方法将每个符合过滤条件的元素添加到一个列表中。这也是使用 lambda 表达式完成的。要添加到列表中的元素作为参数传递给 lambda 表达式，随后在表达式主体内被添加到列表中。

Lambda 表达式非常适合在流函数中使用。它们不仅使业务逻辑的开发更加容易，还使集合过滤更易于阅读和维护。

## 6.6 实现 Runnable

### 问题

你想以简洁的方式创建一段可运行的代码。



### 解决方案

利用 lambda 表达式实现 `java.util.Runnable` 接口。`java.util.Runnable` 接口非常适合 lambda 表达式，因为它只包含一个抽象方法 `run()`。本方案对比了传统技术（创建新的 `Runnable` 接口）和使用 lambda 表达式的新技术。

以下代码演示了如何使用传统技术实现一段新的 `Runnable` 代码。

```
public static void main(String[] args) {
Runnable oldRunnable = new Runnable() {
@Override
public void run() {
int x = 5 * 3;
System.out.println("The variable using the old way equals: " + x);
}
};
Runnable lambdaRunnable = () -> {
int x = 5 * 3;
System.out.println("The variable using the lambda equals: " + x);
};
// 调用 runnable
oldRunnable.run();
lambdaRunnable.run();
}
```

以下是输出结果。

```
The variable using the old way equals: 15
The variable using the lambda equals: 15
```

现在来看看如何使用 lambda 表达式来编写这段代码。

```
Runnable lambdaRunnable = () -> {
int x = 5 * 3;
System.out.println("The variable using the lambda equals: " + x);
};
```

如你所见，实现 `Runnable` 的传统过程比使用 lambda 表达式实现 `Runnable` 需要多写几行代码。lambda 表达式也使 `Runnable` 的实现更易于阅读和维护。

### 工作原理

由于 `java.util.Runnable` 是一个函数式接口，实现 `run()` 方法的样板代码可以使用 lambda 表达式进行抽象。以下是使用 lambda 表达式实现 `Runnable` 的通用格式。

```
Runnable assignment = () -> {expression or statements};
```

`Runnable` 接口可以使用一个零参数的 lambda 表达式来实现，该表达式在 lambda 主体中包含一个表达式或一系列语句。关键在于该实现不接受任何参数且不返回任何内容。

## 6.7 从 Lambda 表达式访问类变量

### 问题

你正在编写的类包含实例变量，并且你希望这些变量能够通过类内的 lambda 表达式使用。

### 解决方案

根据需要，在 lambda 表达式中使用封闭类中包含的实例变量。在下面的类中，`VariableAccessInner.InnerClass.lambdaInMethod()` 方法中包含的 lambda 表达式可以访问所有封闭类的实例变量。因此，如果需要，它可以打印 `VariableAccessInner CLASSA` 变量。

```
public class VariableAccessInner {
public String CLASSA = "Class-level A";
class InnerClass {
public String CLASSA = "Class-level B";
void lambdaInMethod(String passedIn) {
String METHODA = "Method-level A";
Consumer l1 = x -> {
System.out.println(x);
System.out.println("CLASSA Value: " + CLASSA);
System.out.println("METHODA Value: " + METHODA);
};
l1.accept(CLASSA);
l1.accept(passedIn);
}
}
}
```

现在，使用以下代码执行 `lambdaInMethod`。

```
VariableAccessInner vai = new VariableAccessInner();
VariableAccessInner.InnerClass inner = vai.new InnerClass();
inner.lambdaInMethod("Hello");
```

主类如下所示。

```
public class MainClass {
public static void main(String[] args){
System.out.println("==VariableAccessInner==");
VariableAccessInner vai = new VariableAccessInner();
VariableAccessInner.InnerClass inner = vai.new InnerClass();
inner.lambdaInMethod("Hello");
}
}
```

以下是结果。

```
==VariableAccessInner==
Class-level B
CLASSA Value: Class-level B
METHODA Value: Method-level A
Hello
CLASSA Value: Class-level B
METHODA Value: Method-level A
```

注意

`CLASSA` 变量被 `InnerClass` 类中使用相同标识符的变量覆盖了。因此，属于 `VariableAccessInner` 的 `CLASSA` 实例变量不会在 lambda 表达式内被打印。

### 工作原理

Lambda 表达式可以访问位于封闭类中的变量。因此，类方法中包含的 lambda 表达式可以访问封闭类的任何实例变量。Lambda 表达式不会添加额外的作用域，因此它可以访问封闭作用域的字段、方法和局部变量。在解决方案中，`lambdaInMethod()` 方法中包含的 lambda 表达式可以访问在两个类中声明的所有字段。这是因为内部类和外部类都封闭了该 lambda。需要注意的一点是，如果内部类包含一个与外部类中声明的变量同名的实例变量，那么 lambda 会使用其封闭类的变量。因此，在解决方案中，从 lambda 表达式内部访问的是 `InnerClass CLASSA` 字段，而不是外部类的引用。

从 lambda 表达式内部引用的局部变量必须是 final 或 effectively final 的。当 lambda 表达式试图访问在封闭方法上下文中被更改的变量时，会发生错误。例如，假设解决方案中的方法被更改为以下内容。

```
void lambdaInMethod(String passedIn) {
String METHODA = "Method-level A";
passedIn = "test";
Consumer l1 = x -> {
System.out.println(x);
System.out.println("CLASSA Value: " + CLASSA);
System.out.println("METHODA Value: " + METHODA);
System.out.println(passedIn);
};
l1.accept(CLASSA);
l1.accept(passedIn);
}
```

请注意，在调用 lambda 表达式之前，传递给 `lambdaInMethod()` 的字符串被赋予了新值。因此，`passedIn` 变量不再是 effectively final 的，并且 lambda 表达式不能引入新的作用域级别。因此，lambda 表达式无法从表达式上下文内部访问 `passedIn` 变量。

## 6.8 将 Lambda 表达式传递给方法

### 问题

已经创建了一个 lambda 表达式来封装某些功能。你希望将该功能作为参数传递给一个方法，以便该方法实现可以利用该表达式。



### 解决方案

通过实现函数式接口并将 lambda 表达式赋值给与该接口类型相同的变量，可以创建可移植的函数。该变量可以作为参数传递给其他对象。

下面的 `PassingLambdaFunctions` 类包含一个 `calculate()` 方法，该方法可以对给定的值数组执行任意类型的计算。请注意，`calculate()` 方法接受一个 `Function<List<Double>,Double>` 和一个 `Double` 值数组作为参数。

```
public class PassingLambdaFunctions {
/**
* 根据传入的计算函数计算一个值。
* @param f1 Double 输入值
* @param args  Double 输入值
* @return
*/
public Double calculate(Function, Double> f1,
Double [] args){
Double returnVal;
List varList = new ArrayList();
int idx = 0;
while (idx < args.length){
varList.add(args[idx]);
idx++;
}
returnVal=f1.apply(varList);
return returnVal;
}
}
```

要使用 `calculate` 方法，必须将一个实现了 `Function<List<Double>,Double>` 的 lambda 表达式作为第一个参数传递给 `calculate()` 方法，同时传递一个包含计算所需值的 `Double` 参数数组。在下面的类中，使用 lambda 表达式生成了一个用于计算体积的函数。它被赋值给一个标识为 `volumeCalc` 的 `Function<List<Double>,Double>` 类型变量。另一个 lambda 表达式创建了一个用于计算面积的函数，并将其赋值给一个相同类型、标识为 `areaCalc` 的变量。在单独的调用中，这些变量与一个值数组一起被传递给 `PassingLambdaFunctions.calculate()` 方法，从而得到计算结果。

```
public class MainClass {
public static void main(String[] args){
double x = 16.0;
double y = 30.0;
double z = 4.0;
// 使用 lambda 创建体积计算函数。计算器会检查数组是否包含计算所需的三个必要元素。
Function, Double> volumeCalc = list -> {
if(list.size() == 3){
return list.get(0) * list.get(1) * list.get(2);
} else {
return Double.valueOf("-1");
}
};
Double[] argList = new Double[3];
argList[0] = x;
argList[1] = y;
argList[2] = z;
// 使用 lambda 创建面积计算函数。这个特定的计算器会检查数组是否只包含两个元素。
Function, Double> areaCalc = list -> {
if(list.size() == 2){
return list.get(0) * list.get(1);
} else {
return Double.valueOf("-1");
}
};
Double[] argList2 = new Double[2];
argList2[0] = x;
argList2[1] = y;
PassingLambdaFunctions p1 = new PassingLambdaFunctions();
// 将 lambda 表达式和参数列表传递给 calculate() 方法。
System.out.println("体积是: " + p1.calculate(volumeCalc, argList));
System.out.println("面积是: " + p1.calculate(areaCalc, argList2));
}
}
```

以下是输出结果。

```
体积是: 1920.0
面积是: 480.0
```

### 工作原理

Lambda 表达式可以赋值给与所实现的函数式接口类型相同的变量。此类表达式可以包含单行表达式或多语句体。由于 lambda 表达式可以接受参数，因此存在一些用例，可以将此类表达式赋值给变量，然后将这些变量传递给其他对象以修改功能。这种模式对于创建包含多个实现的解决方案非常有用。本技巧的解决方案演示了这一概念。

在解决方案中，`PassingLambdaFunctions` 类包含一个 `calculate()` 方法，该方法对作为参数传入的 `Double` 值执行计算。然而，`calculate()` 方法本身不包含任何计算功能。相反，计算功能是通过 lambda 表达式以 `Function<List<Double>,Double>` 类型的参数形式传递的。此类型是 `java.util.function` 包中的标准函数式接口之一，该接口可以由 lambda 表达式实现，然后通过调用其唯一的 `apply()` 方法来调用。查看 `calculate()` 方法中的代码，首先将 `Double[]` 中包含的参数添加到一个列表中。接着，调用 lambda 表达式的 `apply()` 方法，传入新的值列表，并将结果返回到 `returnVal` 中。最后，将 `returnVal` 返回给方法调用者。

```
returnVal=f1.apply(varList);
return returnVal;
```

为了在解决方案中实现计算功能，在另一个名为 `MainClass` 的类中创建了 lambda 表达式。每个表达式接受一个参数列表，然后计算列表中的值，并返回结果。例如，在 `MainClass` 中生成的第一个 lambda 通过将参数列表中的所有值相乘来计算体积并返回结果。然后，此功能被赋值给一个 `Function<List<Double>,Double>` 类型的变量，随后被传递到 `PassingLambdaFunctions.calculate()` 方法中。

任何功能都可以在 lambda 表达式中实现，然后传递给不同的对象使用。这是促进代码重用和高可维护性的绝佳方式。

## 6.9 局部变量

### 问题

您希望跳过参数类型，使用 `var` 局部变量。

### 解决方案

您可以跳过参数类型，将 lambda 从以下形式

```
(String str1, String str2) -> s1 + s2
```

重写为以下形式。

```
(var str1, var str2) -> str1 + str2
```

您可以使用 `var` 将技巧 6-1 重写为以下形式。

```
public static void main(String[] args){
// 创建 lambda，将一个名为 "text" 的参数传递给
// hello() 方法，返回该字符串。该 lambda 被赋值给
// helloLambda 变量。
HelloType helloLambda =
(var text) -> {System.out.println("Hello " + text);};
// 调用方法
helloLambda.hello("Lambda");
}
```

以下是输出结果。

```
Hello Lambda
```

在这种情况下，变量 `text` 的类型由编译器根据上下文推断，输出结果相同。它不会产生警告或错误。

### 工作原理

Java 11 引入了用于 lambda 参数的局部变量语法，以使隐式类型 lambda 表达式中的参数声明语法与局部变量声明语法保持一致。这无疑使我们的代码*更*具可读性。有关 `var` 关键字的更多信息，请参阅 [`https://openjdk.java.net/jeps/323`](https://openjdk.java.net/jeps/323) 上的文档。

## 6.10 Switch 表达式

### 问题

您希望高效地使用 switch 表达式，而无需使用 `break`。

### 解决方案

您可以简单地为您的表达式使用 lambda 风格的语法。因此，您可以将 lambda 从以下形式

```
switch(input_variable) {
case one:
returnValue = 12
break;
case two:
returnValue = 75
break;
default:
// default 块的代码
}
```

重写为以下形式：

```
int numLetters = switch (input_variable) {
case one -> 12;
case two -> 75;
default -> {// default 块的代码 }
};
```

您可以使用 lambda 风格的语法将技巧 6-4 从以下 switch 表达式

```
switch(status){
case 0:
returnValue = "ACTIVE";
case 1:
returnValue = "INACTIVE";
case 2:
returnValue = "INJURY";
default:
returnValue = "ON_BENCH";
}
```

重写为以下形式。

```
returnValue =switch(status){
case 0  -> "ACTIVE";
case 1  -> "INACTIVE";
case 2  -> "INJURY";
default -> "ON_BENCH";
};
```

输出结果相同。

### 工作原理

此增强功能在 Java 12 中提出，并在 Java 14 (JEP 361) 中正式发布。此功能引入了一种新形式的 switch 标签，如果匹配到该标签，则仅执行标签右侧（`->`）的代码。

有关 `var` 关键字的信息，请参阅 [`https://openjdk.java.net/jeps/361`](https://openjdk.java.net/jeps/361) 上的文档。



## 6.11 小结

Lambda 表达式在 Java 8 中引入，为 Java 语言注入了新的活力，提供了过去 Java 开发者无法获得的能力。无论是桌面、移动还是企业级应用的开发者，现在都可以利用 lambda 表达式来创建更健壮、更复杂的解决方案。Lambda 表达式是语言的一次革命性变革，对整个平台的开发产生了深远影响。

