# 5. 面向对象的 Java

面向对象的程序由许多不同的代码片段组成，它们协同工作。面向对象的理念不是编写一个包含长串语句和命令的程序，而是将功能分解到各个独立组织的对象中。每个对象都包含与其自身相关的功能，当这些对象组合在一起时，就可以用来开发复杂的解决方案。在本章中，我们将探讨 Java 语言的一些关键面向对象特性。从涵盖访问修饰符的基础方案，到处理内部类的高级方案，本章包含的方案将帮助你理解 Java 的面向对象方法论。

## 5.1 控制类成员的访问

### 问题

你想创建一些其他类无法访问的类成员。

### 解决方案

创建 `private` 实例成员，而不是让它们对其他类可用（`public` 或 `protected`）。例如，假设你正在创建一个应用程序来管理一支运动队的球员。你创建了一个名为 `Player` 的类来表示球队中的一名球员。你不希望该类的字段能被任何其他类访问。以下代码演示了如何声明一些实例成员，使其仅能在定义它们的类内部被访问。

```
private String firstName;
private String lastName;
private String position;
private int status = -1;
```

### 工作原理

要将类成员指定为 `private`，请在其声明或签名前使用 `private` 关键字。`private` 访问修饰符会隐藏类的成员，使得外部类无法访问它们。任何标记为 `private` 的类成员仅对同一类的其他成员可用。任何外部类都不能访问被指定为 `private` 的字段或方法，并且使用代码补全功能的 IDE 也无法看到它们。

如本方案解决方案中所述，声明类成员时可以使用三种不同的访问修饰符。这些修饰符是 `public`、`protected` 和 `private`。声明为 `public` 的成员对任何其他类都可用。声明为 `protected` 的成员对同一包内的任何其他类以及子类都可用。最好只将那些需要从另一个类直接访问的类成员声明为 `public` 或 `protected`。使用 `private` 访问修饰符隐藏类成员有助于强化更好的面向对象设计。此外，当类元素未被指定作用域时，会存在一个默认作用域，该作用域对包和类本身是可访问的。

## 5.2 使私有字段对其他类可访问

### 问题

你想创建 `private` 实例成员，以便外部类不能直接访问它们。但是，你也希望以受控的方式使这些 `private` 成员可访问。

### 解决方案

通过为 `private` 字段创建 getter 和 setter 方法来封装它们。以下代码演示了如何声明一个 `private` 字段，然后定义访问器（getter）和修改器（setter）方法，这些方法允许外部类获取或设置该字段的值。

```
private String firstName;
/**
* @return firstName
*/
public String getFirstName() {
return firstName;
}
/**
* @param firstName 要设置的 firstName
*/
public void setFirstName(String firstName) {
this.firstName = firstName;
}
```

外部类可以使用 `getFirstName()` 方法来获取 `firstName` 字段的值。同样，外部类可以使用 `setFirstName(String firstName)` 方法来设置 `firstName` 字段的值。

### 工作原理

通常，当类中的字段被标记为 `private` 时，仍然需要让外部类能够设置或检索它们的值。那么为什么不直接使用这些字段并将它们设为 `public` 呢？直接操作其他类的字段并不是良好的编程实践，因为通过使用访问器（getter）和修改器（setter）可以以受控的方式授予访问权限。通过不直接针对另一个类的成员进行编码，你还有助于解耦代码，这有助于确保如果一个对象发生变化，依赖它的其他对象不会受到不利影响。正如本方案解决方案中的示例所示，隐藏字段并使用 `public` 方法来访问这些字段相当容易。只需创建两个方法：一个用于获取 `private` 字段的值，即 *getter* 或访问器方法；另一个用于设置 `private` 字段的值，即 *setter* 或修改器方法。在本方案的解决方案中，getter 返回 `private` 字段中包含的未经修改的值。类似地，setter 通过接受一个与 `private` 字段相同数据类型的参数，然后将 `private` 字段的值设置为该参数的值。

使用 getter 或 setter 访问字段的类不需要了解这些方法背后的任何细节。例如，如果需要，getter 或 setter 方法可以包含更多功能。此外，这些方法的细节可以在不更改任何访问它们的代码的情况下进行修改。

注意

使用 getter 和 setter 并不能完全解耦代码。事实上，许多人认为使用 getter 和 setter 并不是一种良好的编程实践。使用访问器方法的对象仍然需要知道它们正在操作的实例字段的类型。话虽如此，getter 和 setter 是向外部提供对对象 `private` 实例字段访问的标准技术。为了以更面向对象的方式使用访问器方法，可以在接口中声明它们，并针对接口而不是对象本身进行编码。

## 5.3 创建只有一个实例的类

### 问题

你想创建一个在整个应用程序中只能存在一个实例的类，以便所有应用程序用户都与该类的同一个实例进行交互。



### 解决方案 1

使用单例模式创建类。实现单例模式的类只允许存在一个实例，并提供对该实例的单一访问点。假设你想创建一个 `Statistics` 类，用于计算有组织运动中每个队伍和球员的统计数据。在应用程序中拥有该类的多个实例是没有意义的，因此你希望将 `Statistics` 类创建为单例，以防止生成多个实例。以下类展示了单例模式。

```
package org.java17recipes.chapter05.recipe05_03;
import java.util.ArrayList;
import java.util.List;
import java.io.Serializable;
public class Statistics implements Serializable {
private static final long serialVersionUID = 1L;
// 类实例的定义
private static final Statistics INSTANCE = new Statistics();
private List teams = new ArrayList ();
/**
* 构造函数已被设为私有，以防止外部类访问并实例化更多的 Statistics 实例。
*/
private Statistics(){
}
/**
* Statistics 类的访问器。只允许创建该类的一个实例。
* @return
*/
public static Statistics getInstance(){
return INSTANCE ;
}
/**
* @return the teams
*/
public List getTeams() {
return teams;
}
/**
* @param teams the teams to set
*/
public void setTeams(List teams) {
this.teams = teams;
}
}
```

如果另一个类试图创建该类的实例，它会使用 `getInstance()` 访问器方法来获取单例实例。需要注意的是，解决方案代码演示了**饿汉式**实例化，这意味着实例会在单例被加载时立即创建。对于**懒汉式**实例化（即在首次请求时创建实例），你必须注意同步 `getInstance()` 方法，使其成为线程安全的。以下代码演示了懒汉式实例化的示例。

```
public static Statistics getInstance(){
synchronized(Statistics.class){
if (instance == null){
instance = new Statistics();
}
}
return instance;
}
```

### 解决方案 2

首先，创建一个 `enum`，并在其中声明一个名为 `INSTANCE` 的单一元素。接着，在 `enum` 中声明其他字段，用于存储应用程序所需的值。以下 `enum` 表示一个与解决方案 1 具有相同能力的单例。

```
import java.util.ArrayList;
import java.util.List;
public enum StatisticsSingleton {
INSTANCE;
private List teams = new ArrayList ();
/**
* @return the teams
*/
public List getTeams() {
return teams;
}
/**
* @param teams the teams to set
*/
public void setTeams(List teams) {
this.teams = teams;
}
}
主类是：
public static void main(String[] args){
StatisticsSingleton stats = StatisticsSingleton.INSTANCE;
System.out.println("使用 stats 对象向列表添加对象");
List mylist = stats.getTeams();
mylist.add("One");
mylist.add("Two");
System.out.println("使用 stats2 对象从列表读取对象");
StatisticsSingleton stats2 = StatisticsSingleton.INSTANCE;
List mylist2 = stats2.getTeams();
for(Object name : mylist2){
System.out.println(name.toString());
}
输出是：
Adding objects to the list using stats object
Reading objects from the list using stats2 object
One
Two
```

注意

在 `recipe05_03` 包中有一个测试类，你可以用它来使用 `enum` 单例解决方案。

### 工作原理

单例模式创建的类不能被任何其他类实例化。当你希望整个应用程序只使用一个类的实例时，这会非常有用。可以通过以下三个步骤将单例模式应用于一个类。首先，将类的构造函数设为 `private`，这样外部类就无法实例化它。接着，定义一个 `private static volatile` 字段，用于表示该类的一个实例。`volatile` 关键字保证了每个线程都使用同一个实例。创建该类的一个实例并将其赋值给该字段。在本方案的解决方案中，类名为 `Statistics`，字段定义如下。

```
private static volatile Statistics instance = new Statistics();
```

最后，实现一个名为 `getInstance()` 的访问器方法，该方法简单地返回实例字段。以下代码演示了这样一个访问器方法。

```
public static Statistics getInstance(){
return instance;
}
```

要从另一个类中使用该单例，请调用单例的 `getInstance()` 方法。这将返回该类的一个实例。以下代码展示了另一个类获取本方案解决方案 1 中定义的 `Statistics` 单例实例的示例。

```
Statistics statistics = Statistics.getInstance();
List teams = statistics.getTeams();
```

任何调用 `getInstance()` 方法的类都会获得同一个实例。因此，在整个应用程序中，每次调用 `getInstance()` 时，单例中包含的字段都具有相同的值。

如果单例被序列化然后反序列化，会发生什么？这种情况可能会导致反序列化时返回另一个对象实例。为了防止此问题发生，请务必实现 `readResolve()` 方法，如解决方案 1 所示。该方法在对象被反序列化时被调用，简单地返回该实例即可确保不会生成另一个实例。

解决方案 2 演示了创建单例的另一种方式，即使用 Java `enum` 而不是类。使用这种方法是有益的，因为 `enum` 提供了序列化支持，禁止了多次实例化，并且允许你更简洁地编写代码来实现 `enum` 单例：创建一个 `enum` 并声明一个 `INSTANCE` 元素。这是一个静态常量，它会向引用它的类返回 `enum` 的一个实例。然后，你可以向 `enum` 中添加元素，这些元素可以被应用程序中的其他类用来存储值。

与任何编程解决方案一样，实现目标的方法不止一种。有些人认为解决方案 1 中演示的标准单例模式并非最理想的解决方案。另一些人则出于不同原因不喜欢 `enum` 解决方案。尽管你可能会发现在某些情况下其中一种比另一种效果更好，但两者都是可行的。

## 5.4 生成类的实例

### 问题

在你的一个应用程序中，你希望提供动态生成对象实例的能力。对象的每个实例都应该立即可用，并且对象的创建者不需要了解对象创建的细节。



### 解决方案

使用工厂方法模式来实例化类的实例，同时将创建过程与对象创建者解耦。创建工厂后，可以在调用时返回类的新实例。以下类代表一个简单工厂，每次调用其 `createPlayer(String)` 方法时，都会返回一个 `Player` 子类的新实例。返回的 `Player` 子类取决于传递给 `createPlayer` 方法的字符串值。

```
public class PlayerFactory {
public static Player createPlayer(String playerType){
Player returnType;
switch(playerType){
case "GOALIE":
returnType = new Goalie();
break;
case "LEFT":
returnType = new LeftWing();
break;
case "RIGHT":
returnType = new RightWing();
break;
case "CENTER":
returnType = new Center();
break;
case "DEFENSE":
returnType = new Defense();
break;
default:
returnType = new AllPlayer();
}
return returnType;
}
}
```

如果某个类想使用该工厂，只需调用静态的 `createPlayer` 方法，并传入一个代表 `Player` 新实例的字符串值。以下代码展示了其中一个 `Player` 子类；其他子类可以非常类似。

```
public class Goalie extends Player {
private int totalSaves;
public Goalie(){
this.setPosition("GOALIE");
}
/**
* @return the totalSaves
*/
public int getTotalSaves() {
return totalSaves;
}
/**
* @param totalSaves the totalSaves to set
*/
public void setTotalSaves(int totalSaves) {
this.totalSaves = totalSaves;
}
}
Player 类如下：
package org.java17recipes.chapter05.recipe05_04;
public abstract class Player implements PlayerType {
private String firstName;
private String lastName;
private String position;
private int status = -1;
public Player(){
}
public Player (String position, int status){
this.position = position;
this.status = status;
}
protected String playerStatus(){
String returnValue = null;
switch(getStatus()){
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
/**
* @return the firstName
*/
public String getFirstName() {
return firstName;
}
/**
* @param firstName the firstName to set
*/
public void setFirstName(String firstName) {
if (firstName.length() > 30){
this.firstName = firstName.substring(0, 29);
} else {
this.firstName = firstName;
}
}
/**
* @return the lastName
*/
public String getLastName() {
return lastName;
}
/**
* @param lastName the lastName to set
*/
public void setLastName(String lastName) {
this.lastName = lastName;
}
/**
* @return the position
*/
@Override
public String getPosition() {
return position;
}
/**
* @param position the position to set
*/
public void setPosition(String position) {
this.position = position;
}
/**
* @return the status
*/
public int getStatus() {
return status;
}
/**
* @param status the status to set
*/
public void setStatus(int status) {
this.status = status;
}
}
PlayerType 接口如下：
public interface PlayerType {
public String position=””;
public String getPosition();
}
```

其他每个 `Player` 子类都与 `Goalie` 类非常相似。最值得注意的代码是工厂方法 `createPlayer`，它可以创建 `Player` 类的新实例。

注意

要进一步扩展此示例，可以限制可访问的方法。通过返回 `PlayerType` 类型的对象，并仅在该接口中声明可访问的方法来实现这一点。

### 工作原理

工厂用于生成对象。它们通常用于将对象的创建与其创建者解耦。当创建者不需要了解生成新对象的实际实现细节时，这会非常有用。当需要对对象的创建进行受控访问时，工厂模式也很有用。要实现工厂，请创建一个包含至少一个用于返回新创建对象的方法的类。

在本解决方案中，`PlayerFactory` 类包含一个 `createPlayer(String)` 方法，该方法返回一个新创建的 `Player` 对象。此方法在幕后并没有做任何特殊处理；它只是根据传递给该方法的字符串值实例化一个新的 `Player` 实例。另一个可以访问 `PlayerFactory` 类的对象可以使用 `createPlayer` 返回新的 `Player` 对象，而无需了解对象是如何创建的。虽然这在 `createPlayer` 方法的情况下并没有隐藏太多内容，但 `PlayerFactory` 抽象了正在实例化的类的细节，因此开发人员只需关心获取一个新的 `Player` 对象即可。

工厂模式有效地控制了对象的创建方式，并使创建特定类型的对象变得更加容易。想象一下，如果一个对象的构造函数需要的参数不止几个；创建需要多个参数的新对象可能会变得很麻烦。生成一个工厂来创建这些对象，这样你就不必在每次实例化时硬编码所有参数，这可以大大提高你的工作效率！

## 5.5 创建可重用对象

### 问题

你想要生成一个可以代表应用程序中某些内容的对象。此外，你还希望重用该对象来表示多个实例。例如，假设你创建了一个为不同运动队生成统计数据和联赛信息的应用程序。在这种情况下，你想要创建一个代表球队的对象。

### 解决方案

创建一个代表你想要创建的对象的 JavaBean。JavaBean 对象提供了将对象字段声明为 `private` 的能力，并且还允许读取和更新属性，以便对象可以在应用程序中传递和使用。本解决方案演示了如何创建一个名为 `Team` 的 JavaBean。`Team` 对象包含几个可以包含信息的字段。

```
public class Team implements TeamType {
private List players;
private String name;
private String city;
/**
* @return the players
*/
public List getPlayers() {
return players;
}
/**
* @param players the players to set
*/
@Override
public void setPlayers(List players) {
this.players = players;
}
/**
* @return the name
*/
public String getName() {
return name;
}
/**
* @param name the name to set
*/
@Override
public void setName(String name) {
this.name = name;
}
/**
* @return the city
*/
public String getCity() {
return city;
}
/**
* @param city the city to set
*/
@Override
public void setCity(String city) {
this.city = city;
}
}
TeamType 接口如下：
public interface TeamType {
void setPlayers(List players);
void setName(String name);
void setCity(String city);
}
```

如你所见，此解决方案中的对象包含三个字段，并且每个字段都被声明为 `private`。然而，每个字段都有两个访问器方法——getter 和 setter——使得这些字段可以间接访问。



### 工作原理

JavaBean 是一个持有信息的对象，以便在应用程序中传递和使用。JavaBean 最重要的方面之一是其字段被声明为 `private`。这禁止了其他类直接访问这些字段。相反，每个字段都应通过定义的方法进行封装，使其能够被其他类访问。这些方法必须遵循以下命名约定。

*   用于访问字段数据的方法应使用 `get` 前缀命名，后跟字段名。

*   用于设置字段数据的方法应使用 `set` 前缀命名，后跟字段名。

例如，在本节方案的解决方案中，`Team` 对象包含一个存储球员姓名的字段。要访问该字段，应声明一个 `getPlayers` 方法。它应返回 `players` 字段中包含的数据。同样，应声明一个 `setPlayers` 方法来填充 `players` 字段。它应接受一个与 `players` 字段类型相同的参数，并将 `players` 字段的值设置为该参数。这可以在以下代码中看到。

```
public List getPlayers() {
return players;
}
void setPlayers(List players) {
this.players = players;
}
```

JavaBean 可以填充写入数据库记录的数据列表，或用于其他各种功能。使用 JavaBean 使代码更易于阅读和维护。它还有助于增加未来代码增强的可能性，因为只需要很少的代码实现。使用 JavaBean 的另一个好处是，大多数主流 IDE 会自动为你完成字段的封装。

## 5.6 为类定义接口

### 问题

你想要创建一组方法签名和字段，它们可以作为通用模板，用来暴露一个类所实现的方法和字段。

### 解决方案

生成一个 Java 接口，声明一个类必须实现的每个字段和方法。这样的接口随后可以被一个类实现，并代表一种对象类型。以下代码是一个接口，声明了 `Team` 对象必须实现的方法。

```
public interface TeamType {
void setPlayers(List players);
void setName(String name);
void setCity(String city);
String getFullName();
}
```

接口中的所有方法都是隐式抽象的。也就是说，只提供了方法签名。在接口中也可以包含 `static final` 字段声明。

### 工作原理

Java 接口是一种构造，它定义了类必须实现的结构，无论是字段还是方法。在大多数情况下，接口不包含方法实现；相反，它们只包含方法签名。接口可以包含隐式为 `static` 和 `final` 的变量。

在本节方案的解决方案中，该接口不包含任何常量字段声明。然而，它包含了四个方法签名。所有方法签名都没有指定访问修饰符，因为接口内的所有声明都是隐式 `public` 的。接口暴露了一组功能；因此，接口内暴露的所有方法都必须是隐式 `public` 的。任何实现接口的类都必须实现接口中声明的所有方法签名，除非是默认方法和抽象类，在这种情况下，接口可以将实现留给其子类之一。

虽然 Java 语言不允许多重继承，但一个 Java 类可以实现多个接口，从而允许一种受控形式的多重继承。抽象类也可以实现接口。以下代码演示了一个类实现接口：`Team` 对象声明实现了 `TeamType` 接口。

```
public class Team implements TeamType {
private List players;
private String name;
private String city;
/**
* @return the players
*/
public List getPlayers() {
return players;
}
/**
* @param players the players to set
*/
@Override
public void setPlayers(List players) {
this.players = players;
}
/**
* @return the name
*/
public String getName() {
return name;
}
/**
* @param name the name to set
*/
@Override
public void setName(String name) {
this.name = name;
}
/**
* @return the city
*/
public String getCity() {
return city;
}
/**
* @param city the city to set
*/
@Override
public void setCity(String city) {
this.city = city;
}
public String getFullName() {
return this.name + " - " + this.city;
}
}
```

接口可以声明对象的类型。任何声明为具有接口类型的对象都必须遵守接口中声明的所有实现，除非存在默认实现。例如，以下字段声明定义了一个包含 `TeamType` 接口中声明的所有属性的对象。

```
TeamType team;
```

接口也可以扩展其他接口（因此提供了与多重继承相同的理论）。然而，由于接口中没有方法实现，在 Java 类中实现多个接口比在 C++ 中扩展多个类要安全得多。

接口是 Java 语言中最重要的构造之一。它们提供了用户与类实现之间的接口。虽然可以在不使用接口的情况下创建整个应用程序，但它们有助于促进面向对象，并隐藏其他类的方法实现。

## 5.7 在不破坏现有代码的情况下修改接口

### 问题

你有一个实现了某个接口的工具类，并且工具库中的许多不同类都实现了该接口。假设你想向该工具类添加一个新方法，并使其通过其接口可供其他类使用。然而，更改接口很可能会破坏一些已经实现了该接口的现有类。

### 解决方案

将新方法及其实现作为默认方法添加到工具类接口中。这样做之后，每个实现该接口的类都会自动使用这个新方法，并且由于存在默认实现，它们不会被强制要求实现它。以下类接口包含一个默认方法，任何实现该接口的类都可以使用它。

```
public interface TeamType {
List getPlayers();
void setPlayers(List players);
void setName(String name);
void setCity(String city);
String getFullName();
default void listPlayers() {
getPlayers().stream().forEach((player) -> {
System.out.println(player.getFirstName() + " " + player.getLastName());
});
}
}
```

接口 `TeamType` 包含一个名为 `listPlayers()` 的默认方法。任何实现 `TeamType` 的类都不需要实现此方法，因为接口内部已经提供了默认实现。



### 工作原理

在 Java 的早期版本中，接口只能包含方法签名和常量变量，无法在接口内定义方法实现。这在大多数情况下是可行的，因为接口是一种旨在强制类型安全并抽象实现细节的构造。然而，在某些情况下，允许接口包含默认方法实现是有益的。例如，如果许多类实现了一个现有接口，那么当该接口发生变更时，大量代码可能会被破坏。这将导致无法实现向后兼容。在这种情况下，将默认方法实现放入接口中，而不是强制所有类实现接口中的新方法，是更合理的做法。这就是默认方法成为必要特性并被纳入 Java 8 版本的原因。

要在接口中创建默认方法（也称为*防御方法*），请在方法签名中使用关键字 `default`，并包含方法实现。一个接口可以包含零个或多个默认方法。在本解决方案中，`listPlayers()` 方法是 `TeamType` 接口中的一个默认方法，任何实现 `TeamType` 的类都会自动继承该默认实现。理论上，任何实现 `TeamType` 的类都不会受到添加 `listPlayers()` 默认方法的影响。这使你能够在不破坏向后兼容性的情况下修改接口，这具有极大的价值。

## 5.8 使用不同值构造同一类的实例

### 问题

你的应用程序需要能够构造同一对象的多个实例，但每个实例必须包含不同的值，从而创建同一对象的不同类型。

### 解决方案

使用构建器模式，按照逐步流程构建同一对象的不同类型。例如，假设你希望为一个体育联盟创建不同的队伍。每支队伍必须包含相同的属性，但这些属性的值因队伍而异。因此，你需要创建许多相同类型的对象，但每个对象都是独一无二的。以下代码演示了构建器模式，用于创建所需的队伍。

首先，你需要定义每支队伍需要包含的一组属性。为此，应创建一个 Java 接口，其中包含需要应用于每个 `Team` 对象的不同属性。以下是此类接口的一个示例。

```
public interface TeamType {
public void setPlayers(List players);
public void setName(String name);
public void setCity(String city);
public String getFullName();
}
```

接下来，定义一个类来表示一支队伍。该类需要实现刚刚创建的 `TeamType` 接口，以确保其符合构建队伍所需的格式。

```
public class Team implements TeamType {
private List players;
private String name;
private String city ;
private int wins
private int losses
private int ties
/**
* @return the players
*/
public List getPlayers() {
return players;
}
/**
* @param players the players to set
*/
@Override
public void setPlayers(List players) {
this.players = players;
}
/**
* @return the name
*/
public String getName() {
return name;
}
/**
* @param name the name to set
*/
@Override
public void setName(String name) {
this.name = name;
}
/**
* @return the city
*/
public String getCity() {
return city;
}
/**
* @param city the city to set
*/
@Override
public void setCity(String city) {
this.city = city;
}
public String getFullName(){
return this.name + "  – "  + this.city;
}
}
```

现在 `Team` 类已经定义完毕，需要创建一个构建器。构建器对象的目的是允许逐步创建 `Team` 对象。应创建一个构建器类接口来抽象构建对象的细节。该接口应定义构建对象所需的所有方法，以及一个返回完整构建对象的方法。在本例中，该接口定义了构建新 `Team` 对象所需的每个方法，然后由构建器实现类来实现此接口。

```
public interface TeamBuilder {
public void buildPlayerList();
public void buildNewTeam(String teamName);
public void designateTeamCity(String city);
public Team getTeam();
}
```

以下代码演示了一个构建器类的实现。虽然以下代码不会创建自定义的球员列表，但它包含了实现构建器模式所需的所有功能。创建更自定义的球员列表的细节可以稍后完善，可能通过允许用户通过键盘输入来创建球员。此外，`TeamBuilder` 接口可以为不同的运动项目实现队伍。以下类名为 `HockeyTeamBuilder`，但类似的实现 `TeamBuilder` 的类可以命名为 `FootballTeamBuilder`，以此类推。

```
public class HockeyTeamBuilder implements TeamBuilder {
private Team team;
public HockeyTeamBuilder(){
this.team = new Team();
}
@Override
public void buildPlayerList() {
List players = new ArrayList();
for(int x = 0; x <= 10; x++){
players.add(PlayerFactory.getPlayer());
}
team.setPlayers(players);
}
@Override
public void buildNewTeam(String teamName) {
team.setName(teamName);
}
@Override
public void designateTeamCity(String city){
team.setCity(city);
}
@Override
public Team getTeam(){
return this.team;
}
}
```

最后，通过调用构建器接口中定义的方法来使用构建器创建队伍。以下代码演示了该构建器如何创建一支队伍。你可以使用本解决方案源码中的 `Roster` 类来测试此代码。

```
public Team createTeam(String teamName, String city){
TeamBuilder builder = new HockeyTeamBuilder();
builder.buildNewTeam(teamName);
builder.designateTeamCity(city);
builder.buildPlayerList();
return builder.getTeam();
}
```

尽管这个构建器模式的演示相对简短，但它展示了如何隐藏对象的实现细节，从而使对象更易于构建。你无需了解构建器内部方法的具体实现，只需调用它们即可。



### 工作原理

建造者模式提供了一种以程序化方式生成对象新实例的方法。它抽象了对象创建的细节，因此创建者无需执行任何特定工作即可生成新实例。通过将工作分解为一系列步骤，建造者模式允许对象以不同方式实现其建造方法。由于对象创建者只能访问建造方法，这使得创建不同类型的对象变得更加容易。

使用建造者模式需要几个类和接口。首先，你需要定义一个类及其不同的属性。正如本方案解决方案所展示的，该类可以遵循 JavaBean 模式。你可以通过创建 JavaBean，使用其 setter 和 getter 方法来填充对象。接下来，你应该创建一个接口，用于访问你所创建对象的 setter 方法。每个 setter 方法都应在接口中定义，然后对象本身应实现该接口。如解决方案所示，`Team` 对象包含以下 setter 方法，并且每个方法都在 `TeamType` 接口中定义。

```
public void setPlayers(List players);
public void setName(String name);
public void setCity(String city);
```

在现实生活中，一个团队可能包含更多属性。例如，你可能希望设置吉祥物以及主场体育馆的名称和地址。此示例中的代码可以被认为是简化的，因为它演示了创建通用“团队对象”的过程，而不是向你展示创建真实团队所需的所有代码。由于 `Team` 类实现了在 `TeamType` 接口中定义的这些 setter 方法，因此可以调用接口方法与 `Team` 类的实际方法进行交互。

在对象及其接口编码完成后，需要创建实际的建造者。建造者由一个接口及其实现类组成。首先，你必须定义你希望其他类在构建对象时调用的方法。例如，在本方案的解决方案中，`buildNewTeam()`、`designateTeamCity()` 和 `buildPlayerList()` 方法在名为 `TeamBuilder` 的建造者接口中定义。当某个类稍后想要构建这些对象之一时，它只需要调用这些定义好的方法。接下来，定义一个建造者类的实现。实现类实现了建造者接口中定义的方法，向对象创建者隐藏了这些实现的所有细节。在本方案的解决方案中，建造者类 `HockeyTeamBuilder` 实现了 `TeamBuilder` 接口。当某个类想要创建一个新的 `Team` 对象时，它只需实例化一个新的建造者类。

```
TeamBuilder builder = new HockeyTeamBuilder();
```

为了填充新创建的类对象，需要调用其建造者方法。

```
builder.buildNewTeam(teamName);
builder.designateTeamCity(city);
builder.buildPlayerList();
```

使用这种技术可以为对象提供逐步创建的过程。构建该对象的实现细节对对象创建者是隐藏的。不同的建造者实现可以轻松地使用相同的 `TeamBuilder` 接口来为不同类型构建 `Team` 对象。例如，可以编写一个建造者实现来生成足球队的 `Team` 对象，另一个可以定义来生成棒球队的 `Team` 对象。每个 `Team` 对象的实现都会不同。然而，两者都可以实现相同的接口——`TeamBuilder`——并且创建者只需调用建造者方法，而无需关心细节。

## 5.9 通过接口与类交互

### 问题

你已经创建了一个实现接口或类类型的类。你想通过调用接口中声明的方法来与该类的方法进行交互，而不是直接操作该类。

### 解决方案

声明一个与接口类型相同的字段。然后，你可以将实现该接口的类赋值给你声明的字段，并调用接口中声明的方法来执行工作。在以下示例中，一个字段被声明为 `TeamType` 类型。使用与方案 5-8 中相同的类，你可以看到 `Team` 类实现了 `TeamType` 接口。在以下示例中创建的字段持有一个指向新 `Team` 对象的引用。

由于 `Team` 类实现了 `TeamType` 接口，因此可以使用接口中暴露的方法。

```
public class InterfaceTester {
static TeamType team = new Team();
public static void main(String[] args){
team.setCity("SomeCity");
team.setName("SomeName");
team.setPlayers(null);
System.out.println(team.getFullName());
}
}
```

其中接口为

```
public interface TeamType {
void setPlayers(List players);
void setName(String name);
void setCity(String city);
String getFullName();
}
```

产生的输出如下。

```
SomeName - SomeCity
```

### 工作原理

接口之所以有用，原因有很多。接口最重要的两个用例是一致性和抽象。接口定义了一个模型，任何实现该接口的类都必须符合该模型。因此，如果在接口中定义了一个常量，则该常量会自动在类中可用。如果在接口中定义了一个方法，除非已定义了默认实现，否则该类必须实现该方法。接口提供了一种很好的方式，让类能够符合标准。

接口向任何不需要看到它的类隐藏了不必要的信息。接口中定义的任何方法都被设置为 `public`，并且对任何类都是可访问的。如本方案解决方案所示，创建了一个对象并声明了接口类型。示例中的接口 `TeamType` 仅包含 `Team` 对象中可用方法的一小部分。因此，对于任何针对声明为 `TeamType` 类型的对象进行操作的类来说，唯一可访问的方法就是接口中定义的那些方法。使用此接口类型的类无法访问任何其他方法或常量，也不需要访问。接口是隐藏其他类不需要使用的逻辑的好方法。另一个很好的副作用：实现接口的类可以被更改和重新编译，而不会影响针对该接口编写的代码。但是，如果接口被更改，则可能会影响任何实现它的类。因此，如果 `getFullName()` 方法的实现发生了变化，任何针对 `TeamType` 接口编写的类都不会受到影响，因为接口没有改变。实现会在幕后发生变化，任何针对该接口工作的类都会开始使用新的实现，而无需知晓。

最后，接口有助于提升安全性。它们向任何可能使用接口调用该方法的类隐藏了接口中声明的方法的实现细节。如上一段所述，如果一个类针对 `TeamType` 接口调用了 `getFullName()` 方法，只要按预期返回了结果，它就不需要知道该方法的实现细节。

较旧的 Enterprise JavaBean (EJB) 模型使用接口与执行数据库工作的方法进行交互。这种模型非常适用于隐藏其他类使用时不必要或不关键的细节和逻辑。其他框架也使用类似的模型，通过 Java 接口暴露功能。接口的使用已被证明是一种编写软件的明智方式，因为它促进了可重用性、灵活性和安全性。

## 5.10 使类可克隆

### 问题

你想让一个类能够被另一个类克隆或复制。



### 解决方案

在需要克隆的类中实现 `Cloneable` 接口，然后调用该对象的 clone 方法进行复制。以下代码演示了如何使 `Team` 类可克隆。

```
public class Team implements TeamType, Cloneable, Serializable {
private String name;
private String city;
/**
* @return the name
*/
public String getName() {
return name;
}
/**
* @param name the name to set
*/
@Override
public void setName(String name) {
this.name = name;
}
/**
* @return the city
*/
public String getCity() {
return city;
}
/**
* @param city the city to set
*/
@Override
public void setCity(String city) {
this.city = city;
}
public String getFullName() {
return this.name + " - " + this.city;
}
/**
* 重写 Object 的 clone 方法以创建深拷贝
*
* @return
*/
@Override
public Team clone() {
Team obj = null;
try {
ByteArrayOutputStream baos = new ByteArrayOutputStream();
ObjectOutputStream oos = new ObjectOutputStream(baos);
oos.writeObject(this);
oos.close();
ByteArrayInputStream bais = new ByteArrayInputStream(baos.toByteArray());
ObjectInputStream ois = new ObjectInputStream(bais);
obj = (Team) ois.readObject();
ois.close();
} catch (IOException e) {
e.printStackTrace();
} catch (ClassNotFoundException cnfe) {
cnfe.printStackTrace();
}
return obj;
}
/**
* 重写 Object 的 clone 方法以创建浅拷贝
*
* @return
*/
public Team shallowCopyClone() {
try {
return (Team) super.clone();
} catch (CloneNotSupportedException ex) {
return null;
}
}
@Override
public boolean equals(Object obj) {
if (this == obj) {
return true;
}
if (obj instanceof Team) {
Team other = (Team) obj;
return other.getName().equals(this.getName())
&& other.getCity().equals(this.getCity());
} else {
return false;
}
}
}
```

要对 `Team` 对象进行深拷贝，需要调用该对象的 `clone()` 方法。要对对象进行浅拷贝，则必须调用 `shallowCopyClone()` 方法。以下代码演示了这些技术。

```
public static void main(String[] args){
Team team1 = new Team();
Team team2 = new Team();
team1.setCity("Boston");
team1.setName("Bandits");
team2.setCity("Chicago");
team2.setName("Wildcats");
Team team3 = team1;
Team team4 = team2.clone();
Team team5 = team1.shallowCopyClone();
System.out.println("Team 3:");
System.out.println(team3.getCity());
System.out.println(team3.getName());
System.out.println("Team 4:");
System.out.println(team4.getCity());
System.out.println(team4.getName());
// 球队迁至不同城市
team1.setCity("St. Louis");
team2.setCity("Orlando");
System.out.println("Team 3:");
System.out.println(team3.getCity());
System.out.println(team3.getName());
System.out.println("Team 4:");
System.out.println(team4.getCity());
System.out.println(team4.getName());
System.out.println("Team 5:");
System.out.println(team5.getCity());
System.out.println(team5.getName());
if (team1 == team3){
System.out.println("team1 和 team3 相等");
} else {
System.out.println("team1 和 team3 不相等");
}
if (team1 == team5){
System.out.println("team1 和 team5 相等");
} else {
System.out.println("team1 和 team5 不相等");
}
}
```

此代码演示了如何克隆一个对象。运行结果如下所示。

```
Team 3:
Boston
Bandits
Team 4:
Chicago
Wildcats
Team 3:
St. Louis
Bandits
Team 4:
Chicago
Wildcats
Team 5:
Boston
Bandits
team1 和 team3 相等
team1 和 team5 不相等
```

### 工作原理

复制对象有两种不同的策略：浅拷贝和深拷贝。*浅拷贝*可以在不复制对象任何内容或数据的情况下复制对象。相反，所有变量都通过引用传递给复制的对象。创建对象的浅拷贝后，原始对象和复制对象中的对象引用相同的数据和内存。因此，修改原始对象的内容也会修改复制的对象。默认情况下，调用对象的 `super.clone()` 方法会执行浅拷贝。本方案中的 `shallowCopyClone()` 方法演示了此技术。

第二种复制类型是*深拷贝*，它会复制对象及其所有内容。因此，每个对象引用内存中不同的空间，修改一个对象不会影响另一个对象。深拷贝和浅拷贝之间的区别在本方案的解决方案中得到了演示。首先，创建 `team1` 和 `team2` 对象。接着，为它们填充一些值。然后，将 `team3` 对象设置为等于 `team1` 对象，并将 `team4` 对象设置为 `team2` 对象的克隆。当 `team1` 对象中的值发生更改时，`team3` 对象中的值也会更改，因为两个对象的内容引用相同的内存空间。这是对象浅拷贝的一个示例。当 `team2` 对象中的值发生更改时，`team4` 对象中的值保持不变，因为每个对象都有自己的变量，这些变量引用不同的内存空间。这是深拷贝的一个示例。

要制作对象的精确副本（深拷贝），必须序列化该对象，以便将其写入磁盘。基础 `Object` 类实现了 `clone()` 方法。默认情况下，`Object` 类的 `clone()` 方法是 `protected` 的。要使对象可克隆，它必须实现 `Cloneable` 接口并重写默认的 `clone()` 方法。您可以通过一系列步骤序列化对象来制作深拷贝，例如将对象写入输出流，然后通过输入流将其读回。本方案解决方案的 `clone()` 方法中显示的步骤正是这样做的。对象被写入 `ByteArrayOutputStream`，然后使用 `ByteArrayInputStream` 读取。完成后，对象已被序列化，从而创建了深拷贝。本方案解决方案中的 `clone()` 方法已被重写以创建深拷贝。

完成这些步骤并且对象实现了 `Cloneable` 并重写了默认的 `clone()` 方法后，就可以克隆该对象了。要制作对象的深拷贝，只需调用该对象重写的 `clone()` 方法，如解决方案所示。如果您只是从 `clone()` 方法返回对象，则需要进行类型转换，如下所示。

```
Team team4 = (Team) team2.clone();
```

克隆对象并不困难，但理解对象复制可能产生的差异非常重要。

## 5.11 比较对象

### 问题

您的应用程序需要比较两个或多个对象以查看它们是否相同。

### 解决方案 1

要确定两个对象引用是否指向同一个对象，请使用 `==` 和 `!=` 运算符。以下解决方案演示了比较两个对象引用以确定它们是否引用同一个对象。

```
public static void main(String[] args){
// 比较两个对象是否包含相同的值
Team team1 = new Team();
Team team2 = new Team();
team1.setName("Jokers");
team1.setCity("Crazyville");
team2.setName("Jokers");
team2.setCity("Crazyville");
if (team1 == team2){
System.out.println("这些对象引用指向同一个对象。");
} else {
System.out.println("这些对象引用不指向同一个对象。");
}
// 比较两个对象以查看它们是否引用同一个对象
Team team3 = team1;
Team team4 = team1;
if (team3 == team4){
System.out.println("这些对象引用指向同一个对象。");
} else {
System.out.println("这些对象引用不指向同一个对象。");
}
}
```

以下是运行代码的结果。

```
这些对象引用不指向同一个对象。
这些对象引用指向同一个对象。
```



### 解决方案 2

要判断两个对象是否包含相同的值，请使用 `equals()` 方法。被比较的对象必须实现 `equals()` 和 `hashCode()` 方法，此方案才能正常工作。以下是重写了这两个方法的 `Team` 类的代码。

```
public class Team implements TeamType, Cloneable {
private List players;
private String name;
private String city;
// 出于性能考虑，由 hashCode 方法使用
private volatile int cachedHashCode = 0;
/**
* @return 球员列表
*/
public List getPlayers() {
return players;
}
/**
* @param players 要设置的球员列表
*/
public void setPlayers(List players) {
this.players = players;
}
/**
* @return 队名
*/
public String getName() {
return name;
}
/**
* @param name 要设置的队名
*/
@Override
public void setName(String name) {
this.name = name;
}
/**
* @return 城市
*/
public String getCity() {
return city;
}
/**
* @param city 要设置的城市
*/
@Override
public void setCity(String city) {
this.city = city;
}
public String getFullName() {
return this.name + " - " + this.city;
}
/**
* 重写 Object 的 clone 方法
*
* @return
*/
public Object clone() {
try {
return super.clone();
} catch (CloneNotSupportedException ex) {
return null;
}
}
@Override
public boolean equals(Object obj) {
if (this == obj) {
return true;
}
if (obj instanceof Team) {
Team other = (Team) obj;
return other.getName().equals(this.getName())
&& other.getCity().equals(this.getCity())
&& other.getPlayers().equals(this.getPlayers());
} else {
return false;
}
}
@Override
public int hashCode() {
int hashCode = cachedHashCode;
if (hashCode == 0) {
String concatStrings = name + city;
if (players.size() > 0) {
for (Player player : players) {
concatStrings = concatStrings
+ player.getFirstName()
+ player.getLastName()
+ player.getPosition()
+ String.valueOf(player.getStatus());
}
}
hashCode = concatStrings.hashCode();
}
return hashCode;
}
}
```

以下方案演示了如何比较两个包含相同值的对象。

```
public static void main(String[] args){
// 比较两个对象是否包含相同的值
Team team1 = new Team();
Team team2 = new Team();
// 构建球员列表
Player newPlayer = new Player("Josh", "Juneau");
playerList.add(0, newPlayer);
newPlayer = new Player("Jonathan", "Gennick");
playerList.add(1, newPlayer);
newPlayer = new Player("Joe", "Blow");
playerList.add(1, newPlayer);
newPlayer = new Player("John", "Smith");
playerList.add(1, newPlayer);
newPlayer = new Player("Paul", "Bunyan");
playerList.add(1, newPlayer);
team1.setName("Jokers");
team1.setCity("Crazyville");
team1.setPlayers(playerList);
team2.setName("Jokers");
team2.setCity("Crazyville");
team2.setPlayers(playerList);
if (team1.equals(team2)){
System.out.println("这些对象引用包含相同的值。");
} else {
System.out.println("这些对象引用不包含相同的值。");
}
}
```

以下是运行此代码的结果。

```
这些对象引用不指向同一个对象。
这些对象引用包含相同的值。
这些对象引用指向同一个对象。
```

### 工作原理

比较运算符 `(==)` 可以判断两个对象是否相等。这种相等性不涉及对象的值，而是指对象的引用。应用程序通常更关心对象的值；在这种情况下，`equals()` 方法是首选，因为它比较的是对象中包含的值，而不是对象的引用。

比较运算符查看对象引用，并判断它是否指向与被比较的对象引用相同的对象。如果两个对象相等，则返回布尔值 true；否则，返回布尔值 false。在解决方案 1 中，`team1` 对象引用与 `team2` 对象引用之间的第一次比较返回 false，因为这两个对象在内存中是分开的，即使它们包含相同的值。解决方案 1 中 `team3` 对象引用与 `team4` 对象引用之间的第二次比较返回 true，因为这两个引用都指向 `team1` 对象。

`equals()` 方法测试两个对象是否包含相同的值。要使用 `equals()` 方法进行比较，被比较的对象应重写 `Object` 类的 `equals()` 和 `hashCode()` 方法。`equals()` 方法应实现对对象中包含的值的比较，这些值应能产生 true 的比较结果。以下代码是重写后的 `equals()` 方法示例，已放入 `Team` 对象中。

```
@Override
public boolean equals(Object obj) {
if (this == obj) {
return true;
}
if (obj instanceof Team) {
Team other = (Team) obj;
return other.getName().equals(this.getName())
&& other.getCity().equals(this.getCity())
&& other.getPlayers().equals(this.getPlayers());
} else {
return false;
}
}
```

如您所见，重写后的 `equals()` 方法首先检查作为参数传入的对象是否引用了与它进行比较的同一个对象。如果是，则返回 `true` 结果。如果两个对象在内存中不引用同一个对象，`equals()` 方法会检查字段是否相等。在这种情况下，任何在 name 和 city 字段中包含相同值的两个 `Team` 对象都将被视为相等。一旦重写了 `equals()` 方法，就可以执行两个对象的比较，如本技巧的解决方案 2 所示。

`hashCode()` 方法返回一个 int 值，该值始终返回相同的整数。计算对象的 `hashCode` 有多种方法。在网上搜索这个主题，您会发现各种技术。实现 `hashCode()` 方法最基本的方法之一是将对象的所有变量连接成字符串格式，并返回结果字符串的 `hashCode()`。最好缓存 `hashCode` 的值以备后用，因为初始计算可能需要一些时间。解决方案 2 中的 `hashCode()` 方法演示了这种策略。

考虑到有多种方法可以比较 Java 对象，这可能会变得令人困惑。如果您要进行的比较是针对对象标识的，请使用比较 (`==`) 运算符。但是，如果您想比较对象内部的值或对象的状态，那么 `equals()` 方法是正确的选择。

## 5.12 扩展类的功能

### 问题

您的某个应用程序包含一个类，您希望将其用作另一个类的基类。您希望新类包含与该基类相同的功能，但同时包含额外的功能。



### 解决方案

通过使用 `extends` 关键字，后跟要扩展的类名，来扩展基类的功能。以下示例展示了两个类。第一个类名为 `HockeyStick`，代表一个曲棍球杆对象。第二个类名为 `WoodenStick`，它扩展了第一个类。通过这种方式，`WoodenStick` 类继承了 `HockeyStick` 类中的所有属性和功能，但私有变量和具有默认访问级别的变量除外。`WoodenStick` 类成为 `HockeyStick` 的子类。首先，让我们看一下 `HockeyStick` 类，它包含标准曲棍球杆的基本属性。

```
public class HockeyStick {
private int length;
private boolean curved;
private String material;
public HockeyStick(int length, boolean curved, String material){
this.length = length;
this.curved = curved;
this.material = material;
}
/**
* @return the length
*/
public int getLength() {
return length;
}
/**
* @param length the length to set
*/
public void setLength(int length) {
this.length = length;
}
/**
* @return the curved
*/
public boolean isCurved() {
return curved;
}
/**
* @param curved the curved to set
*/
public void setCurved(boolean curved) {
this.curved = curved;
}
/**
* @return the material
*/
public String getMaterial() {
return material;
}
/**
* @param material the material to set
*/
public void setMaterial(String material) {
this.material = material;
}
}
```

接下来，看一下 `HockeyStick` 的子类，一个名为 `WoodenStick` 的类。

```
public class WoodenStick extends HockeyStick {
private static final String material = "WOOD";
private int lie;
private int flex;
public WoodenStick(int length, boolean isCurved){
super(length, isCurved, material);
}
public WoodenStick(int length, boolean isCurved, int lie, int flex){
super(length, isCurved, material);
this.lie = lie;
this.flex = flex;
}
/**
* @return the lie
*/
public int getLie() {
return lie;
}
/**
* @param lie the lie to set
*/
public void setLie(int lie) {
this.lie = lie;
}
/**
* @return the flex
*/
public int getFlex() {
return flex;
}
/**
* @param flex the flex to set
*/
public void setFlex(int flex) {
this.flex = flex;
}
}
```

注意

在此示例中，我们假设可能存在多种类型的 `HockeyStick`。在这种情况下，我们扩展 `HockeyStick` 来创建 `WoodenStick`，但我们也可以扩展 `HockeyStick` 来创建其他类型的 `HockeyStick`，例如 `AluminumStick` 或 `GraphiteStick`。

### 工作原理

对象继承是任何面向对象语言中的基本技术。从基类继承很有价值，因为它允许代码在多个地方重用。这有助于使代码管理更加容易。如果在基类中进行了更改，它会自动被子类继承。另一方面，如果你的应用程序中散布着重复的功能，一个微小的更改可能意味着你必须在许多地方更改代码。对象继承还可以轻松地将一个基类指定给一个或多个子类，以便每个类可以包含相似的字段和功能。

Java 语言允许一个类仅扩展另一个类。这在概念上不同于其他语言，例如 C++，后者包含多重继承。尽管有些人将单类继承视为对语言的阻碍，但它的设计目的是为语言增加安全性和易用性。当一个子类包含多个超类时，可能会产生混淆。

## 5.13 定义供类扩展的模板

### 问题

你想要定义一个模板，用于生成包含类似功能的对象。

### 解决方案

定义一个 `abstract` 类，其中包含可在其他类中使用的字段和功能。`abstract` 类还可以包含未实现的方法，称为 *抽象方法*，这些方法需要由 `abstract` 类的子类来实现。以下示例演示了 `abstract` 类的概念。示例中的 `abstract` 类代表一个团队日程表，它包含每个团队日程表都需要使用的一些基本字段声明和功能。然后，`TeamSchedule` 类扩展了 `Schedule` 类，该类为每个团队实现了特定的功能。首先，让我们看一下 `abstract Schedule` 类。

```
public abstract class Schedule {
public String scheduleYear;
public String teamName;
public List teams;
public Map gameMap;
public Schedule(){}
public Schedule(String teamName){
this.teamName = teamName;
}
abstract void calculateDaysPlayed(int month);
}
```

接下来，`TeamSchedule` 扩展了 `abstract` 类的功能。

```
public class TeamSchedule extends Schedule {
public TeamSchedule(String teamName) {
super(teamName);
}
@Override
void calculateDaysPlayed(int month) {
int totalGamesPlayedInMonth = 0;
for (Map.Entry entry : gameMap.entrySet()) {
if (entry.getKey().equals(teamName)
&& entry.getValue().getMonth().equals(month)) {
totalGamesPlayedInMonth++;
}
}
System.out.println("Games played in specified month: " + totalGamesPlayedInMonth);
}
}
```

如你所见，`TeamSchedule` 类可以使用 `abstract Schedule` 类中包含的所有字段和方法。它还实现了 `Schedule` 类中包含的 `abstract` 方法。

### 工作原理

抽象类被如此标记，它们包含可在子类中使用的字段声明和方法。它们与常规类的不同之处在于，它们包含 `abstract` 方法，这些方法是只有声明而没有实现的方法。本方案中的解决方案包含一个名为 `calculateDaysPlayed()` 的 `abstract` 方法。抽象类可以包含也可以不包含 `abstract` 方法。它们也可以包含字段和完全实现的方法。抽象类不能被实例化；其他类只能扩展它们。当一个类扩展一个 `abstract` 类时，它会获得 `abstract` 类的所有字段和功能。但是，在 `abstract` 类中声明的任何 `abstract` 方法都必须由子类实现。

你可能会想，为什么 `abstract` 类不直接包含该方法的实现，以便其所有子类都可以使用它。如果你思考一下这个概念，就会发现这完全合理。一种类型的对象可能以不同于另一种对象的方式执行任务。使用 `abstract` 方法强制扩展 `abstract` 类的类实现它，但它允许自定义实现方式的能力。

## 5.14 增强类的封装性

### 问题

你的一个类需要使用另一个类的功能。但是，没有其他类需要使用相同的功能。与其创建一个包含此额外功能的单独类，不如生成一个只能由需要它的类使用的实现，同时将代码放在一个逻辑位置。



### 解决方案

在需要其功能的类内部创建一个*内部类*。

```
import java.util.ArrayList;
import java.util.List;
/**
* 内部类示例。此示例演示了如何使用内部类对象构建团队对象。
*
* @author juneau
*/
public class TeamInner {
private Player player;
private List playerList;
private int size = 4;
/**
* 表示 Player 对象的内部类
*/
class Player {
private String firstName ;
private String lastName ;
private String position ;
private int status = -1;
public Player() {
}
public Player(String position, int status) {
this.position = position;
this.status = status;
}
protected String playerStatus() {
String returnValue = null;
switch (getStatus()) {
case 0:
returnValue = "ACTIVE";
break;
case 1:
returnValue = "INACTIVE";
break;
case 2:
returnValue = "INJURY";
break;
default:
returnValue = "ON_BENCH";
break;
}
return returnValue;
}
public String playerString() {
return getFirstName() + " " + getLastName() + " - " + getPosition();
}
/**
* @return the firstName
*/
public String getFirstName() {
return firstName;
}
/**
* @param firstName the firstName to set
*/
public void setFirstName(String firstName) {
this.firstName = firstName;
}
/**
* @return the lastName
*/
public String getLastName() {
return lastName;
}
/**
* @param lastName the lastName to set
*/
public void setLastName(String lastName) {
this.lastName = lastName;
}
/**
* @return the position
*/
public String getPosition() {
return position;
}
/**
* @param position the position to set
*/
public void setPosition(String position) {
this.position = position;
}
/**
* @return the status
*/
public int getStatus() {
return status;
}
/**
* @param status the status to set
*/
public void setStatus(int status) {
this.status = status;
}
@Override
public String toString(){
return this.firstName + " " + this.lastName + " - "+
this.position + ": " + this.playerStatus();
}
}
public TeamInner() {
final int ACTIVE = 0;
// 实际上，这里可能会使用循环从数据库读取记录……但在此示例中，我们将手动输入球员数据。
playerList = new ArrayList();
playerList.add(constructPlayer("Josh", "Juneau", "Right Wing", ACTIVE));
playerList.add(constructPlayer("Joe", "Blow", "Left Wing", ACTIVE));
playerList.add(constructPlayer("John", "Smith", "Center", ACTIVE));
playerList.add(constructPlayer("Bob","Coder", "Defense", ACTIVE));
playerList.add(constructPlayer("Jonathan", "Gennick", "Goalie", ACTIVE));
}
public Player constructPlayer(String first, String last, String position, int status){
Player player = new Player();
player.firstName = first;
player.lastName = last;
player.position = position;
player.status = status;
return player;
}
public List getPlayerList() {
return this.playerList;
}
public static void main(String[] args) {
TeamInner inner = new TeamInner();
System.out.println("Team Roster");
System.out.println("===========");
for(Player player:inner.getPlayerList()){
System.out.println(player.playerString());
}
}
}
```

运行此代码的结果会列出团队中的球员。

```
Team Roster
===========
Josh Juneau - Right Wing
Joe Blow - Left Wing
John Smith - Center
Bob Coder - Defense
Jonathan Gennick - Goalie
```

### 工作原理

有时，将功能封装在单个类中非常重要。为仅在一个其他类中使用的功能单独创建一个类是没有意义的。想象一下，你正在开发一个 GUI，并且需要使用一个类来支持一个按钮的功能。如果该按钮类中没有可重用的代码，那么创建一个单独的类并将该功能暴露给其他类使用是没有意义的。相反，将该类封装在需要该功能的类内部才是有意义的。这种理念就是内部类（也称为*嵌套类*）的一个用例。

内部类是一个包含在另一个类内部的类。内部类可以像其他任何类一样被声明为 `public`、`private` 或 `protected`。它可以包含与普通类相同的功能；唯一的区别在于内部类包含在一个封闭类中，该封闭类也被称为*外部类*。本方案中的示例演示了这种技术。`TeamInner` 类包含一个名为 `Player` 的内部类。`Player` 类是一个表示 `Player` 对象的 JavaBean 类。`Player` 对象可以继承其包含类的功能，包括其 `private` 字段。这是因为内部类包含对外部类的隐式引用。它也可以通过包含 `TeamInner` 类来访问，正如 `constructPlayer()` 方法中所演示的那样。

```
public Player constructPlayer(String first, String last, String position, int status){
Player player = new Player();
player.firstName = first;
player.lastName = last;
player.position = position;
player.status = status;
return player;
}
```

外部类可以根据需要多次实例化内部类。在示例中，`constructPlayer()` 方法可以被调用任意次数，每次都会实例化一个新的内部类实例。然而，当外部类被实例化时，并不会实例化任何内部类实例。类似地，当外部类不再使用时，所有内部类实例都会被销毁。

内部类可以通过引用外部类及其想要调用的方法来引用外部类的方法。以下代码行使用本方案示例中表示的相同对象演示了这种引用。假设 `Player` 类需要从外部类获取球员列表，你可以编写类似如下的代码。

```
TeamInner.this.getPlayerList();
```

虽然不常用，但外部类以外的类可以通过以下语法访问 `public` 内部类。

```
TeamInner outerClass = new TeamInner();
outerClass.player = outerClass.new Player();
```

静态内部类略有不同，它们不能直接引用其封闭类的任何实例变量或方法。以下是一个静态内部类的示例。

```
public class StaticInnerExample {
static String hello = "Hello";
public static void sayHello(){
System.out.println(hello);
}
static class InnerExample {
String goodBye = "Good Bye";
public void sayGoodBye(){
System.out.println(this.goodBye);
}
}
public static void main (String[] args){
StaticInnerExample.sayHello();
StaticInnerExample.InnerExample inner =
new StaticInnerExample.InnerExample();
inner.sayGoodBye();
}
}
```

内部类有助于提供逻辑封装。此外，它们允许继承 `private` 字段，这是使用标准类无法实现的。

## 5.15 总结

Java 是一种面向对象的语言。要充分利用该语言的能力，你必须学会精通面向对象编程。本章涵盖了诸如类创建和访问修饰符等基础知识。还涵盖了封装、接口以及帮助开发者利用面向对象强大功能的相关方案。



