# 7. 数据源与集合

应用程序使用数据结构来存储数据，这些数据可在应用实例的整个生命周期中使用。Java 语言包含几种称为集合类型的数据结构，可用于此目的。这些数据结构实现了 `java.util.Collection<E>` 接口，该接口提供了多种方法，用于对集合中的数据进行添加、删除和执行操作。本章介绍了一些可在 Java 应用程序中用于存储用户数据的数据结构。它详细讨论了其中一些数据结构，并介绍了对数据执行的操作。本章还介绍了管道和流的概念，并提供了演示其用法的示例。

## 7.1 定义一组固定的相关常量

### 问题

你需要一种能够表示一组固定相关常量的类型。

### 解决方案

使用枚举类型。以下示例定义了一个名为 `FieldType` 的枚举类型，用于表示应用程序图形用户界面上可能出现的各种表单字段。

```
public enum FieldType { PASSWORD, EMAIL_ADDRESS, PHONE_NUMBER, SOCIAL_SECURITY_NUMBER }
```

这是枚举类型最简单的形式，当需要一组相关的命名常量时，这种形式通常就足够了。在以下代码中，声明了一个 `FieldType` 类型的 `field` 变量，并将其初始化为 `FieldType.EMAIL_ADDRESS` 枚举常量。接着，代码打印了调用所有枚举类型都定义的各个方法的结果。

```
public static void main(String[] args) {
FieldType field = FieldType.EMAIL_ADDRESS;
System.out.println("field.name(): " + field.name());
System.out.println("field.ordinal(): " + field.ordinal());
System.out.println("field.toString(): " + field.toString());
System.out.println("field.isEqual(EMAIL_ADDRESS): " +
field.equals(FieldType.EMAIL_ADDRESS));
System.out.println("field.isEqual(\"EMAIL_ADDRESS\"'): " + field.equals("EMAIL_ADDRESS"));
System.out.println("field == EMAIL_ADDRESS: " + (field == FieldType.EMAIL_ADDRESS));
// 无法编译——说明枚举的类型安全性
// System.out.println("field == \"EMAIL_ADDRESS\": " + (field == "EMAIL_ADDRESS"));
System.out.println("field.compareTo(EMAIL_ADDRESS): " +
field.compareTo(FieldType.EMAIL_ADDRESS));
System.out.println("field.compareTo(PASSWORD): " + field.compareTo(FieldType.PASSWORD));
System.out.println("field.valueOf(\"EMAIL_ADDRESS\"): " + field.valueOf("EMAIL_ADDRESS"));
try {
System.out.print("field.valueOf(\"email_address\"): ");
System.out.println(FieldType.valueOf("email_address"));
} catch (IllegalArgumentException e) {
System.out.println(e.toString());
}
System.out.println("FieldType.values(): " + Arrays.toString(FieldType.values()));
}
```

运行此代码将产生以下输出。

```
field.name(): EMAIL_ADDRESS
field.ordinal(): 1
field.toString(): EMAIL_ADDRESS
field.isEqual(EMAIL_ADDRESS): true
field.isEqual("EMAIL_ADDRESS"'): false
field == EMAIL_ADDRESS: true
field.compareTo(EMAIL_ADDRESS): 0
field.compareTo(PASSWORD): 1
field.valueOf("EMAIL_ADDRESS"): EMAIL_ADDRESS
field.valueOf("email_address"): java.lang.IllegalArgumentException: No enum constant org.java17recipes.chapter07.recipe07_01.BasicEnumExample.FieldType.email_address
FieldType.values(): [PASSWORD, EMAIL_ADDRESS, PHONE_NUMBER, SSN]
```



### 工作原理

表示一组固定相关常量的常见模式是将每个常量定义为 int、string 或其他数据类型。通常，这些常量定义在专门用于封装常量的类或接口中。无论如何，常量有时会使用 `static` 和 `final` 修饰符进行定义，如下所示。

```
// 输入字段常量
public static final int PASSWORD = 0;
public static final int EMAIL_ADDRESS = 1;
public static final int PHONE_NUMBER = 2;
public static final int SOCIAL_SECURITY_NUMBER = 3;
```

这种模式存在多个问题，主要问题在于缺乏类型安全性。通过将这些常量定义为 int 类型，可能会将一个无效值赋给本应只允许持有其中一个常量值的变量。

```
int inputField = PHONE_NUMBER;  // 正确
inputField = 4;  // 错误 - 没有值为 4 的输入字段常量；编译时无错误
```

如你所见，编译器不会产生任何错误或警告来告知你这种无效赋值。很可能，你会在运行时，当应用程序尝试使用 `inputField` 且为其分配了错误值时才发现这个问题。相比之下，Java 枚举类型提供了编译时的类型安全性。也就是说，如果试图将错误类型的值赋给枚举变量，会导致编译错误。在本方案的解决方案中，`FieldType.EMAIL_ADDRESS` 枚举常量被赋给了 `field` 变量。试图赋一个非 `FieldType` 类型的值自然会导致编译错误。

```
FieldType field = FieldType.EMAIL_ADDRESS;  // 正确
field = "EMAIL_ADDRESS"; // 类型错误 - 编译错误
```

枚举本质上是一种特殊的类。在底层，Java 将枚举类型实现为 `abstract` 和 `final` 的 `java.lang.Enum` 类的子类。因此，枚举类型不能直接实例化（在枚举类型外部）或继承。枚举类型定义的常量是其自身的实例。`java.lang.Enum` 类定义了几个所有枚举类型都继承的 `final` 方法。此外，所有枚举类型都有两个隐式声明的 `static` 方法：`values()` 和 `valueOf(String)`。解决方案代码演示了这些 `static` 方法以及一些更常用的实例方法。

这些方法大多不言自明，但你应该牢记以下几点。

*   每个枚举常量都有一个序数值，表示其在枚举声明中的相对位置。声明中的第一个常量被分配序数值 0。`ordinal()` 方法可以获取枚举常量的序数值；但出于可维护性原因，不建议应用程序依赖此值。

*   `name()` 方法和 `toString()` 方法的默认实现都返回枚举常量的字符串表示形式（`toString()` 实际上调用了 `name()`）。通常，`toString()` 会被重写，以提供更友好的枚举常量字符串表示形式。出于此原因以及可维护性原因，建议优先使用 `toString()` 而非 `name()`。

*   在测试相等性时，请注意 `equals()` 方法和 == 都执行引用比较。它们可以互换使用。然而，建议使用 == 以利用编译时类型安全性。解决方案代码中对此进行了说明。例如，使用字符串参数执行 `equals()` 比较可能会使错误被忽略；它能编译通过，但始终返回 false。相反，使用 == 比较枚举和字符串会在编译时导致错误。当你选择更早地捕获错误（在编译时而非运行时）时，请选择前者。

*   隐式声明的 `values()` 和 `valueOf(String)` 静态方法不会出现在 Java 文档或 `java.lang.Enum` 类的源代码中。然而，Java 语言规范确实详细说明了它们所需的实现。总结这些方法：`values()` 返回一个数组，其中包含按声明顺序排列的枚举常量。`valueOf(String)` 方法返回名称（区分大小写）与字符串参数值匹配的枚举常量，如果没有指定名称的枚举常量，则抛出 `IllegalArgumentException`。

有关 `java.lang.Enum` 及其每个方法的更多信息，请参阅 Java 文档（[`https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/Enum.html`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/Enum.html)）。正如下一个方案所展示的，枚举类型作为完整的 Java 类，可以构建更智能的常量。

## 7.2 设计智能常量

### 问题

你需要一个能够表示一组固定相关常量的类型，并且希望以面向对象的方式围绕常量构建一些状态和行为（逻辑）。

### 解决方案

使用枚举类型并利用类型安全性，因为枚举类型是完整的 Java 类。枚举类型可以像任何其他类一样拥有状态和行为，而枚举常量本身作为枚举类型的实例，会继承这些状态和行为。一个例子最能说明这一点。让我们扩展上一个方案中的示例。假设你需要处理并验证提交的 HTML 表单中的所有字段。每个表单字段根据其字段类型，都有一套独特的验证规则。对于每个表单字段，你都有字段的“名称”以及在该表单字段中输入的值。`FieldType` 枚举可以很容易地扩展来处理这种情况。



```
public enum FieldType {
PASSWORD(FieldType.passwordFieldName) {
// 密码必须包含至少一个数字、一个小写字母、一个大写字母，且长度至少为 6 个字符。
public boolean validate(String fieldValue) {
return Pattern.matches("((?=.*\\d)(?=.*[a-z])(?=.*[A-Z]).{6,})",
fieldValue);
}
},
EMAIL_ADDRESS(FieldType.emailFieldName) {
// 电子邮件地址以字母数字字符、句点和连字符的组合开头，后跟一个必需的
// '@' 字符，再后跟字母数字字符（允许连字符）的组合，接着是一个或多个
// 句点（用于分隔域名和子域名），最后以 2-4 个代表域名的字母字符结尾。
public boolean validate(String fieldValue) {
return Pattern.matches("^[\\w\\.-]+@([\\w\\-]+\\.)+[A-Z|a-z]{2,4}$",
fieldValue);
}
},
PHONE_NUMBER(FieldType.phoneFieldName) {
// 电话号码必须至少包含 7 位数字。主号码前可包含 3 位可选数字作为区号。
// 区号可选地括在括号中。如果包含区号，则号码前可加'1'表示长途。
// 在国家代码('1')、区号和 7 位号码的前 3 位之后，可选地出现连字符。
public boolean validate(String fieldValue) {
return Pattern.matches("¹?[- ]?\\(?(\\d{3})\\)?[- ]?(\\d{3})[- ]?(\\d{4})$",
fieldValue);
}
},
SOCIAL_SECURITY_NUMBER(FieldType.ssnFieldName) {
// 社会安全号码必须包含 9 位数字，在第 3 位和第 5 位之后可选地包含连字符。
public boolean validate(String fieldValue) {
return Pattern.matches("^\\d{3}[- ]?\\d{2}[- ]?\\d{4}$",
fieldValue);
}
};  // 枚举常量定义结束
// 实例成员
//
private String fieldName;
// 定义静态常量以提高类型安全性
static final String passwordFieldName = "password";
static final String emailFieldName = "email";
static final String phoneFieldName = "phone";
static final String ssnFieldName = "ssn";
private FieldType(String fieldName) {
this.fieldName = fieldName;
}
public String getFieldName() {
return this.fieldName;
}
abstract boolean validate(String fieldValue);
// 静态类成员
//
private static final Map nameToFieldTypeMap = new HashMap();
static {
for (FieldType field : FieldType.values()) {
nameToFieldTypeMap.put(field.getFieldName(), field);
}
}
public static FieldType lookup(String fieldName) {
return nameToFieldTypeMap.get(fieldName.toLowerCase());
}
private static void printValid(FieldType field, String fieldValue, boolean valid) {
System.out.println(field.getFieldName() +
"(\"" + fieldValue + "\") valid: " + valid);
}
public static void main(String... args) {
String fieldName = FieldType.passwordFieldName;
String fieldValue = "1Cxy9";  // 无效 - 必须至少 6 个字符
FieldType field = lookup(fieldName);
printValid(field, fieldValue, field.validate(fieldValue));
fieldName = FieldType.phoneFieldName;
fieldValue = "1-800-555-1234";  // 有效
field = lookup(fieldName);
printValid(field, fieldValue, field.validate(fieldValue));
fieldName = FieldType.emailFieldName;
fieldValue = "john@doe";  // 无效 - 缺少句点
field = lookup(fieldName);
printValid(field, fieldValue, field.validate(fieldValue));
fieldName = FieldType.ssnFieldName;
fieldValue = "111-11-1111";  // 有效
field = lookup(fieldName);
printValid(field, fieldValue, field.validate(fieldValue));
}
}
```

运行上述代码将产生以下输出。

```
password("1Cxy9") valid: false
phone("1-800-555-1234") valid: true
email("john@doe") valid: false
ssn("111-11-1111") valid: true
```

### 工作原理

请注意，增强后的 `FieldType` 枚举定义了一个 `fieldName` 实例变量和一个带有 `fieldName` 字符串参数的构造函数，用于初始化该实例变量。每个枚举常量（每个常量都是 `FieldType` 的一个实例）都必须使用 `fieldName` 进行实例化。`FieldType` 还定义了一个 `abstract validate(String)` 方法，每个枚举常量都必须实现该方法以执行字段验证。在这里，每个 `FieldType` 的 `validate()` 方法都对字段值应用正则表达式匹配，并返回匹配的 `Boolean` 结果。想象一下，以下表单输入字段对应于我们的 `FieldType` 实例。

输入字段的 `name` 属性值标识了 `FieldType`；在实例化每个 `FieldType` 枚举常量时，你使用了相同的名称。当提交表单时，你可以访问每个输入字段的名称和字段中输入的值。你需要将字段名称映射到字段类型，并使用输入值调用 `validate()` 方法。为此，声明并初始化了类变量 `nameToFieldTypeMap`。对于每个 `FieldType` 枚举常量，`nameToFieldTypeMap` 存储一个条目，其中字段名称为键，字段类型为值。`lookup(String)` 类方法使用此映射从字段名称查找字段类型。验证输入值为 `john@doe.com` 的电子邮件输入字段的代码非常简洁。

```
// 
String fieldName = FieldType.emailFieldName;
String fieldValue = "john@doe.com";
boolean valid = FieldType.lookup(fieldName).validate(fieldValue);
```

`main()` 方法展示了每个 `FieldType` 的验证示例。`printValid()` 方法打印字段名称、字段值以及字段的验证结果。

本技巧展示了枚举类型远不止定义一组命名常量的能力。枚举类型拥有普通类的所有功能，外加一些额外特性，允许你创建封装良好且智能的常量。

## 7.3 根据指定值执行代码

### 问题

你想根据单个表达式的值执行不同的代码块。



### 解决方案

如果你的变量或表达式结果属于允许的 `switch` 类型之一，并且你想与类型兼容的常量进行相等性测试，可以考虑使用 `switch` 语句。以下示例展示了 `switch` 语句的多种用法，包括 Java 7 中新增的一项功能：对字符串进行 `switch` 操作。首先，我们来玩一局石头剪刀布游戏！`RockPaperScissors` 类展示了两种不同的 `switch` 语句：一种以 `int` 作为 `switch` 表达式类型，另一种以枚举类型作为表达式类型。

```
public class RockPaperScissors {
enum Hand { ROCK, PAPER, SCISSORS, INVALID };
private static void getHand(int handVal) {
Hand hand;
try {
hand = Hand.values()[handVal - 1];
}
catch (ArrayIndexOutOfBoundsException ex) {
hand = Hand.INVALID;
}
switch (hand) {
case ROCK:
System.out.println("Rock");
break;
case PAPER:
System.out.println("Paper");
break;
case SCISSORS:
System.out.println("Scissors");
break;
default:
System.out.println("Invalid");
}
}
private static void playHands(int yourHand, int myHand) {
// Rock = 1
// Paper = 2
// Scissors = 3
// Hand combinations:
// 1,1; 2,2; 3,3 => Draw
// 1,2 => sum = 3 => Paper
// 1,3 => sum = 4 => Rock
// 2,3 => sum = 5 => Scissors
//
switch ((yourHand == myHand) ? 0 : (yourHand + myHand)) {
case 0:
System.out.println("Draw!");
break;
case 3:
System.out.print("Paper beats Rock. ");
printWinner(yourHand, 2);
break;
case 4:
System.out.print("Rock beats Scissors. ");
printWinner(yourHand, 1);
break;
case 5:
System.out.print("Scissors beats Paper. ");
printWinner(yourHand, 3);
break;
default:
System.out.print("You cheated! ");
printWinner(yourHand, myHand);
}
}
private static void printWinner(int yourHand, int winningHand) {
if (yourHand == winningHand) {
System.out.println("You win!");
}
else {
System.out.println("I win!");
}
}
public static void main(String[] args) {
Scanner input = new Scanner(System.in);
System.out.println("Let's Play Rock, Paper, Scissors");
System.out.println("  Enter 1 (Rock)");
System.out.println("  Enter 2 (Paper)");
System.out.println("  Enter 3 (Scissors)");
System.out.print("> ");
int playerHand = input.hasNextInt() ? input.nextInt() : -99;
int computerHand = (int)(3*Math.random()) + 1;
System.out.print("Your hand: (" + playerHand + ") ");
getHand(playerHand);
System.out.print("My hand: (" + computerHand + ") ");
getHand(computerHand);
playHands(playerHand, computerHand);
}
}
```

当 `RockPaperScissors` 类被执行时，一个交互式游戏便开始了，允许用户在键盘上输入内容。用户可以输入与所选选项对应的数字，而计算机会利用随机数计算来击败用户的选择。

Java 7 增加了对字符串进行 `switch` 操作的能力。`SwitchTypeChecker` 类演示了以字符串作为 `switch` 表达式类型。`isValidSwitchType()` 方法接收一个 `Class` 对象，并判断相应类型是否为 `switch` 表达式中使用的有效类型。因此，`SwitchTypeChecker` 使用 `switch` 语句同时演示了对字符串进行 `switch` 操作，并展示了 `switch` 表达式中可用的有效类型：

```
public class SwitchTypeChecker {
public static Class varTypeClass(Object o) { return o.getClass(); };
public static Class varTypeClass(Enum e) { return e.getClass().getSuperclass(); };
public static Class varTypeClass(char c) { return char.class; };
public static Class varTypeClass(byte b) { return byte.class; };
public static Class varTypeClass(short s) { return short.class; };
public static Class varTypeClass(int i) { return int.class; };
public static Class varTypeClass(long l) { return long.class; };
public static Class varTypeClass(float f) { return float.class; };
public static Class varTypeClass(double d) { return double.class; };
public static Class varTypeClass(boolean d) { return boolean.class; };
public void isValidSwitchType(Class typeClass) {
String switchType = typeClass.getSimpleName();
boolean valid = true;
switch (switchType) {
case "char":
case "byte":
case "short":
case "int":
System.out.print("Primitive type " + switchType);
break;
case "Character":
case "Byte":
case "Short":
case "Integer":
System.out.print("Boxed primitive type " + switchType);
break;
case "String":
case "Enum":
System.out.print(switchType);
break;
default:  // invalid switch type
System.out.print(switchType);
valid = false;
}
System.out.println(" is " + (valid ? "" : "not ") + "a valid switch type.");
}
public static void main(String[] args) {
SwitchTypeChecker check = new SwitchTypeChecker();
check.isValidSwitchType(varTypeClass('7'));
check.isValidSwitchType(varTypeClass(7));
check.isValidSwitchType(varTypeClass(777.7d));
check.isValidSwitchType(varTypeClass((short)7));
check.isValidSwitchType(varTypeClass(new Integer(7)));
check.isValidSwitchType(varTypeClass("Java 8 Rocks!"));
check.isValidSwitchType(varTypeClass(new Long(7)));
check.isValidSwitchType(varTypeClass(true));
check.isValidSwitchType(varTypeClass(java.nio.file.AccessMode.READ));
}
}
```

以下是执行 `SwitchTypeChecker` 的结果。

```
Primitive type char is a valid switch type.
Primitive type int is a valid switch type.
double is not a valid switch type.
Primitive type short is a valid switch type.
Boxed primitive type Integer is a valid switch type.
String is a valid switch type.
Long is not a valid switch type.
boolean is not a valid switch type.
Enum is a valid switch type.
```



### 工作原理

`switch` 语句是一种控制流语句，允许你根据 `switch` 表达式的值执行不同的代码块。它类似于 `if-then-else` 语句，区别在于 `switch` 语句只能有一个测试表达式。该表达式的类型被限制为几种特定类型之一。当 `switch` 语句执行时，它会将表达式与 `switch` 语句的 `case` 标签中包含的常量进行比较。这些 `case` 标签是代码中的分支点。如果表达式的值等于某个 `case` 标签常量的值，控制权就会转移到与该匹配 `case` 标签对应的代码段。从该点开始的所有代码语句都会被执行，直到遇到 `switch` 语句的结尾或 `break` 语句。`break` 语句会导致 `switch` 语句终止，控制权转移到 `switch` 语句之后的语句。可选地，`switch` 语句可以包含一个 `default` 标签，当没有 `case` 标签常量等于 `switch` 表达式的值时，它提供一个分支点。

`SwitchTypeChecker isValidSwitchType()` 方法演示了如何使用字符串作为 `switch` 测试表达式。如果你仔细研究 `isValidSwitchType()` 方法，你会发现它测试一个 `Class` 对象是否代表与有效 `switch` 表达式类型之一相对应的类型。该方法还演示了如何将 `case` 标签分组以实现逻辑或条件测试。如果一个 `case` 标签没有关联的代码可执行，也没有 `break` 语句，执行流程会落入下一个最近的包含可执行语句的 `case` 标签，从而允许在 `switch` 表达式的结果与分组 `case` 常量中的任何一个匹配时执行公共代码。

`RockPaperScissors` 类实现了一个命令行版的石头剪刀布游戏，与计算机对战。该类中有两个方法演示了 `switch` 语句。`getHand()` 方法展示了在 `switch` 表达式中使用枚举变量。`playHands()` 方法旨在说明，尽管 `switch` 表达式通常是一个变量，但它可以是任何结果属于允许的 `switch` 类型之一的表达式。在这个例子中，表达式使用了一个返回 `int` 值的三元运算符。

## 7.4 使用固定大小的数组

### 问题

你需要一个简单的数据结构，能够存储固定（且可能很大）数量的同类型数据，并提供快速的顺序访问。

### 解决方案

考虑使用数组。虽然 Java 提供了更复杂、更灵活的 `Collection` 类型，但数组类型对于许多应用来说仍然是一种有用的数据结构。下面的例子演示了使用数组的简便性。`GradeAnalyzer` 类提供了一种计算各种与成绩相关的统计数据的方法，例如平均分、最低分和最高分。

```
public class GradeAnalyzer {
// 内部成绩数组
private int[] grades;
public void setGrades(int[] grades) {
this.grades = grades;
}
// 返回克隆的成绩数组，以防止调用者修改我们内部的成绩数组
public int[] getGrades() {
return grades != null ? grades.clone() : null;
}
public int meanGrade() {
int mean = 0;
if (grades != null && grades.length > 0) {
int sum = 0;
for (int i = 0; i < grades.length; i++) {
sum += grades[i];
}
mean = sum / grades.length;
}
return mean;
}
public int minGrade() {
int min = grades[0];
for (int i = 1; i < grades.length; i++) {
if (grades[i] < min) {
min = grades[i];
}
}
return min;
}
public int maxGrade() {
int max = grades[0];
for (int index = 1; index < grades.length; index++) {
if (grades[index] > max) {
max = grades[index];
}
}
return max;
}
static int[] initGrades1() {
int[] grades = new int[5];
grades[0] = 77;
grades[1] = 48;
grades[2] = 69;
grades[3] = 92;
grades[4] = 87;
return grades;
}
static int[] initGrades2() {
int[] grades = { 57, 88, 67, 95, 99, 74, 81 };
return grades;
}
static int[] initGrades3() {
return new int[]{ 100, 70, 55, 89, 97, 98, 82 };
}
public static void main(String... args) {
GradeAnalyzer ga = new GradeAnalyzer();
ga.setGrades(initGrades1());
System.out.println("成绩组 1:");
System.out.println("所有成绩的平均分是 " + ga.meanGrade());
System.out.println("最低分是 " + ga.minGrade());
System.out.println("最高分是 " + ga.maxGrade());
ga.setGrades(initGrades2());
System.out.println("成绩组 2:");
System.out.println("所有成绩的平均分是 " + ga.meanGrade());
System.out.println("最低分是 " + ga.minGrade());
System.out.println("最高分是 " + ga.maxGrade());
ga.setGrades(initGrades3());
System.out.println("成绩组 3:");
System.out.println("所有成绩的平均分是 " + ga.meanGrade());
System.out.println("最低分是 " + ga.minGrade());
System.out.println("最高分是 " + ga.maxGrade());
Object testArray = ga.getGrades();
Class testClass = testArray.getClass();
System.out.println("isArray: " + testClass.isArray());
System.out.println("getClass: " + testClass.getName());
System.out.println("getSuperclass: " + testClass.getSuperclass().getName());
System.out.println("getComponentType: " + testClass.getComponentType());
System.out.println("Arrays.toString: " + Arrays.toString((int[])testArray));
}
}
```

运行这段代码会产生以下输出。

```
成绩组 1:
所有成绩的平均分是 74
最低分是 0
最高分是 92
成绩组 2:
所有成绩的平均分是 80
最低分是 0
最高分是 99
成绩组 3:
所有成绩的平均分是 84
最低分是 0
最高分是 100
isArray: true
getClass: [I
getSuperclass: class java.lang.Object
getComponentType: int
Arrays.toString: [100, 70, 55, 89, 97, 98, 82]
```



### 工作原理

Java 的 `array` 类型与 Java 的 `ArrayList`（Java 集合框架的一部分）的工作方式不同。Java 数组保存固定数量的数据。创建数组时，必须指定它可以容纳多少数据。一旦创建了数组，就不能插入或删除数组项，也不能改变数组的大小。然而，如果你有固定数量（尤其是非常大量）且需要按顺序迭代处理的数据，数组可能是一个不错的选择。

关于 Java 的 `array` 类型，你需要了解的第一件事是它是一个 `Object` 类型。无论它们包含的数据类型是什么，所有数组都以 `Object` 类型作为其超类。如果所有元素类型相同（无论是基本类型还是对象引用），数组的元素可以是任何类型。无论数组类型如何，数组的内存总是从应用程序的堆空间中分配。堆是 JVM 用于动态内存分配的内存区域。

注意

可以创建一个 `Objects` 数组（`Object[]`），它可以保存对不同类型对象的引用；但是，不建议这样做。它要求你在从数组中检索元素时检查元素类型并执行显式类型转换。

在 Java 中完全定义一个数组对象有两个步骤：数组变量声明（指定数组元素类型）和数组创建（为数组分配内存）。一旦声明了数组并分配了内存，就可以对其进行初始化。有多种初始化数组的方法，本方案中展示了这些方法。如果你事先知道需要存储在数组中的数据，可以使用解决方案中演示的快捷语法，将数组声明、创建和初始化合并为一步。

让我们逐步了解 `GradeAnalyzer` 类，并检查声明、创建、初始化和访问数组的各种方法。首先，注意该类有一个实例变量来保存要分析的成绩。

```
private int[] grades;
```

与所有未初始化的 `Object` 引用实例变量一样，`grades` 数组实例变量会自动初始化为 `null`。在开始分析成绩之前，你必须设置 `grades` 实例变量以引用你想要分析的成绩数据。这是通过 `setGrades(int[])` 方法完成的。一旦 `GradeAnalyzer` 有了要分析的成绩集合，就可以调用 `meanGrade()`、`minGrade()` 和 `maxGrade()` 方法来计算各自的统计数据。这三个方法共同演示了如何迭代数组元素、如何访问数组元素以及如何确定数组可以容纳的元素数量。要确定数组可以容纳的元素数量，只需访问隐式定义的最终实例变量 `length`，该变量对所有数组都可用。

```
grades.length
```

要迭代数组的元素，只需使用一个 `for` 循环，其索引变量遍历数组的所有可能索引。数组索引从 0 开始，因此最后一个数组索引始终是 (`grades.length - 1`)。在迭代数组时，你可以通过使用数组变量名称后跟方括号括起来的当前索引（通常称为*数组下标*）来访问当前索引处的数组元素。

```
// 来自 meanGrade() 方法：
for (int i = 0; i < grades.length; i++) {
sum += grades[i];
}
```

或者，增强型 `for` 循环（也称为 `foreach` 循环）也可以迭代数组。

```
for (int grade : grades) {
sum += grade;
}
```

请注意，为了确定最小和最大成绩，首先使用 `java.util.Arrays` 类中的实用排序方法按自然（升序）顺序对成绩进行排序。排序后，最小成绩就是数组的第一个元素（索引 0），最大成绩就是数组的最后一个元素（索引 length – 1）。

解决方案中的三个静态类方法——`initGrades1()`、`initGrades2()` 和 `initGrades3()`——演示了创建和初始化用于“播种”`GradeAnalyzer` 类的数组数据的三种不同方式。`initGrades1()` 方法声明并创建一个可以容纳五个成绩的数组（使用 new），然后手动将每个元素索引处的值设置为一个整数成绩值。`initGrades2()` 方法使用特殊的数组初始化器语法将数组创建和初始化合并为一行。

```
int[] grades = { 57, 88, 67, 95, 99, 74, 81 };
```

此语法创建一个长度为 7 的数组，并使用显示的整数值初始化索引 0 到索引 6 的元素。请注意，此语法只能在数组声明中使用，因此以下操作是不允许的。

```
int[] grades;
grades = { 57, 88, 67, 95, 99, 74, 81 }; // 无法编译
```

`initGrades3()` 方法看起来与 `initGrades2()` 非常相似，但略有不同。此代码创建并返回一个匿名（未命名）数组。

```
return new int[]{ 100, 70, 55, 89, 97, 98, 82 };
```

使用此语法，你将 new 关键字与数组元素类型一起使用，但未显式指定数组的大小。与 `initGrades2()` 方法中显示的数组初始化器语法类似，初始化器括号内给出的元素数量隐含了数组大小。因此，这段代码再次创建并返回一个长度为 7 的数组。

在计算了三组成绩数据的成绩统计数据之后，`GradeAnalyzer` 类的 `main()` 方法的其余部分演示了确定数组类型信息并将数组转换为可打印字符串的各种方法。你会看到代码首先将从调用 `getGrades()` 实例方法返回的数组赋值给一个名为 `testArray` 的 `Object` 变量。

```
Object testArray = ga.getGrades();
```

你可以进行此赋值，因为如前所述，数组是一个 Object。你也可以从对 `testArray.getSuperclass()` 的调用中看到这一点。对 `testArray.getClass().getName()` 的调用也很有趣；它返回“I”。左括号表示“我是一个数组类型”，而“I”表示“组件类型为整数”。这也得到了对 `testArray.getComponentType()` 调用的支持。最后，你调用 `Arrays.toString(int[])` 方法，该方法返回数组及其内容的格式良好的字符串表示形式。请注意，`testArray` 是一个 Object 引用，因此必须将其转换为 int 数组才能用于 `Arrays.toString(int[])` 方法。（有关可与数组一起使用的其他实用方法，请参阅 `java.util.Arrays` 类的 Java 文档。）

## 7.5 安全地使类型或方法能够操作各种类型的对象

### 问题

你的应用程序使用许多不同的对象类型，并且你的类中有可用于保存这些不同类型中每一种的容器。你希望确保应用程序保持无错误，同时又希望动态更改特定容器可能保存的对象类型。换句话说，你想定义一个泛型容器，但可以在每次实例化容器的新实例时指定其类型。



### 解决方案

使用泛型将类型与容器解耦。泛型是一种对对象类型进行抽象的方式，无需显式声明对象或容器的类型。所有集合类型都经过参数化，允许你在实例化时指定集合可容纳的元素类型。以下示例代码演示了如何在不同场景中使用泛型。代码中的注释标明了泛型的使用位置。

```
public class MainClass {
static List team;
private static void loadTeam() {
System.out.println("Loading team...");
// 使用菱形运算符
team = new ArrayList();
Player player1 = new Player("Josh", "Juneau", 5);
Player player2 = new Player("Duke", "Java", 15);
Player player3 = new Player("Jonathan", "Gennick", 1);
Player player4 = new Player("Bob", "Smith", 18);
Player player5 = new Player("Steve", "Adams", 7);
team.add(player1);
team.add(player2);
team.add(player3);
team.add(player4);
team.add(player5);
}
public static void main(String[] args) {
loadTeam();
// 创建一个未指定类型的列表
List objectList = new ArrayList();
Object obj1 = "none";
objectList.add(obj1);
// 创建一个类型为 Player 的任意超类（或仅 Player 类型本身）的列表
List myTeam = objectList;
for (Object p : myTeam) {
System.out.println("Printing the objects...");
System.out.println(p.toString());
}
// 创建一个键和值均为 String 类型的 Map
Map strMap = new HashMap();
strMap.put("first", "Josh");
strMap.put("last", "Juneau");
System.out.println(strMap.values());
}
}
```

注意

当我们通常谈论*集合*或*集合类型*时，你可以将其理解为构成 Java 集合框架的那些类型。这包括来自 `Collection` 和 `Map` 接口的所有类和接口。集合类型通常指那些继承自 `Collection` 接口的类型。



### 工作原理

解决方案代码演示了泛型的一些基本用例。配方源码中包含的 `GenericsDemo.java` 文件中的示例更详细地展示了泛型在 Java 集合中的使用，而不是教你如何创建泛型类型。除非你开发库 API，否则你可能不需要创建自己的泛型类型。然而，如果你理解了泛型如何与 `Collection` 接口和类一起使用，你就可以创建自己的泛型类型了。

关于 Java 泛型，首先要理解并记住的是：它们严格来说是一种编译时特性，旨在帮助开发者创建类型更安全的代码。当你参数化一个泛型类型时指定的所有类型信息，在代码编译成字节码时都会被编译器“擦除”。你会看到这被称为*类型擦除*。让我们看一个泛型 `Collection` 类型的例子：`List`。`List` 是一个定义如下的接口。

```
public interface List extends Collection { ... };
```

这个语法很奇怪，特别是因为没有对象或类型被标识为 `E`。事实证明，`E` 被称为*类型参数*，它是一个占位符，用于向编译器指示在运行时将某个类型分配给该对象。类型参数通常是大写字母，表示正在定义的参数的类型。有各种不同的类型参数需要注意，但请记住，这些仅适用于定义泛型类型时。在大多数情况下，只有在开发库或 API 时才会定义泛型类型。

*   E – 元素
*   K – 键
*   N – 数字
*   T – 类型
*   V – 值
*   S、U、V 等——第二、第三、第四种类型

要为 `List`（或任何 `Collection` 类型）指定元素类型，只需在声明和实例化对象时，将类型名称放在尖括号中。当你这样做时，你就是在指定一个*参数化类型*。以下代码声明了一个整数列表。声明了一个参数化类型 `List<Integer>` 的变量 `aList`，然后用从参数化类型 `LinkedList<Integer>`（也称为*具体参数化类型*）的实例化中获得的引用对其进行初始化。

```
List aList = new LinkedList();
```

现在你已经将这些类型参数化，将元素类型限制为 `Integer`，`List add(E e)` 方法就变成了：

```
boolean add(Integer e);
```

如果你尝试向 `aList` 添加除整数之外的任何内容，编译器会生成一个错误。

```
aList.add(new Integer(121));
aList.add(42);      // 由于自动装箱，42 等同于 new Integer(42)。
aList.add("Java");  // 无法编译，类型错误
```

需要注意的是，编译时检查的是引用类型，因此以下代码也会导致编译器错误。

```
Number aNum = new Integer("7");
aList.add(aNum);  // 无法编译，类型错误
```

这是一个编译错误，因为 `aNum` 可以引用任何 `Number` 对象。如果编译器允许这样做，你最终可能会得到一个包含 double、float 等类型的集合，这将违反你在创建 `aList` 时指定的 Integer 参数约束。当然，简单的类型转换可以让你绕过编译器错误，但在不兼容的 `Number` 对象之间进行转换时，这肯定会导致意想不到的后果。泛型旨在减少你必须在代码中进行的显式类型转换的数量，因此，如果你发现在使用参数化类型的方法时使用了显式类型转换，这表明你的代码可能存在潜在危险。

```
aList.add((Integer)aNum);  // 可以编译，但不要这样做。
```

使用泛型类型时需要注意的其他事情是编译器警告。它们可能表明你正在做一些不推荐的事情，并且通常表明你的代码存在潜在的运行时错误。一个例子可以帮助说明这一点。以下代码可以编译，但会产生两个编译器警告。

```
List rawList = new LinkedList();
aList = rawList;
```

首先，你创建了 `rawList`，它是一个*原始类型*，即未参数化的泛型类型。当泛型被引入该语言时，语言设计者决定为了保持与泛型之前代码的兼容性，他们需要使用原始类型。然而，强烈不鼓励在新代码（Java 5 之后）中使用原始类型，因此如果你使用它们，编译器会生成原始类型警告。接下来，`rawList` 被赋值给使用参数化类型创建的 `aList`。同样，编译器允许这样做（由于泛型类型擦除和向后兼容性）。会为这个赋值生成一个“未检查的转换”警告，以标记潜在的运行时类型不兼容。想象一下，如果 `rawList` 包含字符串。稍后，如果你尝试从 `aList` 中检索整数元素，你将收到一个运行时错误。

关于类型兼容性，它不适用于泛型类型参数。例如，以下不是有效的赋值。

```
List bList = new LinkedList();  // 无法编译；类型不兼容
```

尽管整数是数字（`Integer` 是 `Number` 的子类型），并且 `LinkedList` 是 `List` 的子类型，但 `LinkedList<Integer>` 不是 `List<Number>` 的子类型。幸运的是，如果你不小心编写了这样的代码，它不会逃过你的眼睛。编译器会生成一个“类型不兼容”的警告。

所以你可能会想，是否有办法实现类似于我们在上一行代码中尝试的变体子类型关系。答案是肯定的，通过使用一个称为*通配符*的泛型特性。通配符通过在类型参数尖括号内使用问号（`?`）来表示。通配符声明了有界或无界的参数化类型。以下是一个有界参数化类型的声明示例。

```
List cList;
```

当通配符与 `extends` 关键字一起使用时，就为类型参数建立了一个上界。在这个例子中，`? extends Number` 表示任何是 `Number` 或 `Number` 子类型的类型。因此，以下赋值是有效的，因为 `Integer` 和 `Double` 都是 `Number` 的子类型。

```
cList = new LinkedList();
cList = new LinkedList();
cList = new LinkedList();
```

所以，`cList` 可以持有对任何元素类型与 `Number` 兼容的 `List` 实例的引用。`cList` 甚至可以引用一个原始类型。这使得编译器很难强制执行类型安全以允许向 `cList` 添加元素。因此，编译器不允许向使用 `? extends` 参数化的集合类型添加元素（除了 null）。以下代码会导致编译器错误。

```
cList.add(new Integer(5));  // 不允许 add()；cList 可能是 LinkedList
```

但是，你可以毫无问题地从列表中获取元素。

```
Number cNum = cList.get(0);
```

这里唯一的限制是，你从列表中获取的引用必须被视为一个数字。请记住，`cList` 可能指向一个整数列表、一个双精度浮点数列表或任何其他 `Number` 子类型的列表。

通配符也可以与 `super` 关键字一起使用。在这种情况下，为类型参数建立了一个下界。

```
List dList;
```

在这个例子中，`? super Integer` 表示任何是 `Integer` 或 `Integer` 的任何超类型的类型。因此，以下赋值是有效的，因为 `Number` 和 `Object` 是 `Integer` 仅有的超类型。

```
dList = new LinkedList();
dList = new LinkedList();
dList = new LinkedList();
```

所以，你看 `Integer` 是下界。这个下界现在限制了你从列表中检索元素。因为 `dList` 可以引用前面任何一个参数化类型，如果对正在检索的元素类型做出假设，编译器将无法强制执行类型安全。因此，编译器不得允许对使用 `? super` 参数化的集合类型调用 `get()`，以下代码会导致编译器错误。

```
Integer n = dList.get(0);  // 不允许 get()；dList.get(0) 可能是 Number 或 Object
```

然而，现在你可以向列表中添加元素，但下界 `Integer` 仍然适用。只能添加 `Integer`，因为 `Integer` 与 `Number` 和 `Object` 兼容。

```
dList.add(new Integer(5));  // 可以
Number dNum = new Double(7);
dList.add(dNum);  // 无法编译；dList 可能是 LinkedList
```

你会在整个集合类型中看到通配符与 `extends` 和 `super` 一起使用。你经常看到它们用于方法参数类型，例如为所有集合定义的 `addAll()` 方法。有时你会看到集合类型单独使用通配符（`?`）作为类型参数，这被称为*无界通配符*。集合的 `removeAll()` 方法就是这样一个例子。在大多数情况下，这种用法是不言自明的。你可能不会（可能不应该）使用无界通配符定义自己的参数化类型。如果你尝试这样做，你很快就会了解到你无法用它做太多事情。如果你理解了具体参数化类型、通配符参数化类型以及有界和无界类型的概念（如本配方所述），你就掌握了使用泛型集合类型所需的大部分知识，并且如果你愿意，也可以创建自己的泛型类型。

既然我们已经讨论了很多关于参数化类型的内容，我们打算让你忘记其中的一些。当 Java 7 发布时，引入了一个称为*菱形*（有时称为菱形运算符，尽管它在 Java 中不被视为运算符）的新特性。菱形允许编译器从参数化类型使用的上下文中推断类型参数。以下是一个简单的菱形使用示例。

```
List eList = new ArrayList();
```

注意，在实例化 `ArrayList` 时，尖括号之间没有指定类型参数。编译器可以根据赋值或初始化器的上下文轻松推断出类型是整数。Integer 是此上下文中唯一可行的类型。事实上，Java 编译器（以及大多数兼容的 IDE）会在你可以使用菱形但未使用时发出警告。另一个更复杂的例子更好地展示了其优势。

```
Map> aMap = new HashMap();  // 很好！
```

菱形同样可以用于 `return` 语句以及方法参数中。

```
// 方法返回中的菱形
public static List getEmptyList() {
return new ArrayList();
}
// 方法参数中的菱形
List> gList = new ArrayList();
gList.set(0, new ArrayList(Arrays.asList("a", "b")));
```

请注意，此处使用菱形与使用原始类型不同。以下代码不等同于使用菱形的 `aMap` 声明；它会导致编译器发出“未检查的转换”警告，甚至可能是原始类型警告。

```
Map> bMap = new HashMap();   // 编译器警告；避免原始类型
```

关于为什么这与菱形示例不同的讨论超出了本配方的范围。如果你记住要避免使用原始类型，你就不必担心这个。尽可能使用菱形，以节省一些输入，并使你的代码更健壮、更易读、更简洁。



## 7.6 使用动态数组

### 问题

你需要一种灵活的数据结构，能够存储可变数量的数据，并支持便捷的插入和删除操作。

### 解决方案

考虑使用 `ArrayList<E>`。以下示例代码是 `StockScreener` 类，它允许你根据特定的筛选参数（市盈率、收益率和贝塔系数）和筛选值来筛选股票列表或单只股票。该类使用 `ArrayList<E>` 来存储股票字符串。一个示例筛选条件可能是：“告诉我这个列表中哪些股票的市盈率（P/E）小于等于 15。”如果你不熟悉这些股市术语，不必担心。但无论如何，请**不要**使用这个类来做出你的股票投资决策！

```
public class StockScreener {
enum Screen { PE, YIELD, BETA };
public static boolean screen(String stock, Screen screen, double threshold) {
double screenVal = 0;
boolean pass = false;
switch (screen) {
case PE:
screenVal = Math.random() * 25;
pass = screenVal = threshold;
break;
case BETA:
screenVal = Math.random() * 2;
pass = screenVal  stocks, Screen screen, double threshold) {
Iterator iter = stocks.iterator();
while (iter.hasNext()) {
String stock = iter.next();
if (!screen(stock, screen, threshold)) {
iter.remove();
}
}
}
public static void main(String[] args) {
List stocks = new ArrayList();
stocks.add("ORCL");
stocks.add("AAPL");
stocks.add("GOOG");
stocks.add("IBM");
stocks.add("MCD");
System.out.println("Screening stocks: " + stocks);
if (stocks.contains("GOOG") &&
!screen("GOOG", Screen.BETA, 1.1)) {
stocks.remove("GOOG");
}
System.out.println("First screen: " + stocks);
StockScreener.screen(stocks, Screen.YIELD, 3.5);
System.out.println("Second screen: " + stocks);
StockScreener.screen(stocks, Screen.PE, 22);
System.out.println("Third screen: " + stocks);
System.out.println("Buy List: " + stocks);
}
}
```

运行这段代码的输出会有所不同，因为它会随机分配股票的筛选结果值。以下是运行该类的一个输出示例。

```
Screening stocks: [ORCL, AAPL, GOOG, IBM, MCD]
GOOG: BETA = 1.9223516769748348
First screen: [ORCL, AAPL, IBM, MCD]
ORCL: YIELD = 6.140018494585904
AAPL: YIELD = 7.875759429097191
IBM: YIELD = 7.715436753622726
MCD: YIELD = 2.419792753509281
Second screen: [ORCL, AAPL, IBM]
ORCL: PE = 4.396013965331994
AAPL: PE = 14.200385457743778
IBM: PE = 7.6981860501796175
Third screen: [ORCL, AAPL, IBM]
Buy List: [ORCL, AAPL, IBM]
```

### 工作原理

`ArrayList<E>` 是 Java 集合框架中最常用的类之一。`ArrayList<E>` 类实现了 `List<E>` 接口，而 `List<E>` 接口又实现了 `Collection<E>` 接口。`Collection<E>` 接口定义了所有集合类型的通用操作集，`List<E>` 接口则定义了面向列表的 `Collection<E>` 类型特有的操作集。集合框架大量使用了 Java 泛型。

`StockScreener main()` 方法首先声明了一个 `List<E>` 类型的股票列表，并通过泛型类型参数指定该列表的元素类型为 `String`。请注意，实际的列表类型是使用菱形语法创建的 `ArrayList<E>`。该股票列表存储了数量可变的股票，用它们的股票市场代码（字符串）表示。

```
List stocks = new ArrayList();
```

既然你已经指定了股票列表只能包含字符串，那么所有 `List<E>` 方法也会相应地参数化，只允许操作字符串。因此，接下来代码多次调用 `ArrayList<E>` 的 `add(String)` 方法向列表中添加股票。之后，根据 GOOG（谷歌）的贝塔系数（衡量股票风险的指标）对其进行筛选；如果未通过筛选，则调用 `List remove(String)` 方法将该股票从列表中移除。接着，对整个股票列表再进行两次筛选，以获取市盈率小于等于 22.0 且收益率大于等于 3.5% 的股票列表。用于这些筛选的 `screen()` 方法接受一个 `List<String>` 类型的参数。它需要遍历列表，对每只股票执行筛选，并移除那些未通过筛选的股票。请注意，为了在遍历 `Collection<E>` 时安全地移除元素，你必须使用 `Collection<E>` 的 `Iterator<E>` 进行迭代，该迭代器可以通过调用其 `iterator()` 方法获得。这里，我们展示了使用 `while` 循环来遍历股票列表（也可以类似地使用 `for` 循环）。只要没有到达列表末尾（`iter.hasNext()`），你就可以从列表中获取下一个股票（`iter.next()`），执行筛选，如果筛选未通过，则从列表中移除该元素（`iter.remove()`）。

注意

你可能会发现，在遍历列表时直接调用列表的 `remove()` 方法似乎也能工作。问题在于这并不能保证总是有效，并且可能会产生意外结果。在某些情况下，即使没有多个线程访问同一个列表，代码也会抛出 `ConcurrentModificationException`。请记住，在遍历任何 `Collection<E>` 时，始终通过迭代器来移除元素。

`ArrayList<E>` 是一种非常有用的数据结构，通常应替代数组类型使用。它比简单的数组提供了更大的灵活性，因为可以轻松地动态添加和移除元素。虽然 `ArrayList<E>` 内部确实使用了数组，但你可以受益于其为你实现的经过优化的 `add()` 和 `remove()` 操作。此外，`ArrayList<E>` 还实现了许多其他非常有用的方法。

更多信息请参考 Java 文档（[`https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/ArrayList.html`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/ArrayList.html)）。

## 7.7 让你的对象可迭代

### 问题

你创建了一个自定义的基于集合的类，该类包装（而非继承）了底层的集合类型。在不暴露类内部实现细节的情况下，你希望该类的对象变得可迭代，特别是能够使用 `foreach` 语句。



### 解决方案

让你的类实现 `Iterable<T>` 接口，其中 `T` 是待遍历集合的元素类型。实现 `iterator()` 方法以返回该集合的 `Iterator<E>` 对象。本方案中的示例是 `StockPortfolio` 类。在内部，`StockPortfolio` 管理着一个 `Stock` 对象的集合。我们希望类的使用者能够通过 `foreach` 语句将 `StockPortfolio` 对象视为可迭代对象。`StockPortfolio` 类的代码如下。

```
public class StockPortfolio implements Iterable {
Map portfolio = new HashMap();
public void add(Stock stock) {
portfolio.put(stock.getSymbol(), stock);
}
public void add(List stocks) {
for (Stock s : stocks) {
portfolio.put(s.getSymbol(), s);
}
}
@Override
public Iterator iterator() {
return portfolio.values().iterator();
}
public static void main(String[] args) {
StockPortfolio myPortfolio = new StockPortfolio();
myPortfolio.add(new Stock("ORCL", "Oracle", 500.0));
myPortfolio.add(new Stock("AAPL", "Apple", 200.0));
myPortfolio.add(new Stock("GOOG", "Google", 100.0));
myPortfolio.add(new Stock("IBM", "IBM", 50.0));
myPortfolio.add(new Stock("MCD", "McDonalds", 300.0));
// foreach 循环（使用从 iterator() 方法返回的 Iterator）
System.out.println("====使用传统 for-each 循环打印====");
for (Stock stock : myPortfolio) {
System.out.println(stock);
}
System.out.println("====使用 Java 8 foreach 实现打印====");
myPortfolio.forEach(s->System.out.println(s));
}
}
```

以下是 `Stock` 类的代码。

```
public class Stock {
private String symbol;
private String name;
private double shares;
public Stock(String symbol, String name, double shares) {
this.symbol = symbol;
this.name = name;
this.shares = shares;
}
public String getSymbol() {
return symbol;
}
public String getName() {
return name;
}
public double getShares() {
return shares;
}
public String toString() {
return shares + " shares of " + symbol + " (" + name + ")";
}
}
```

`main()` 方法创建了一个 `StockPortfolio` 对象，然后调用 `add()` 方法向投资组合中添加了几只股票。两种 `foreach` 循环变体（传统循环和 `forEach` 实现）随后遍历并打印投资组合中的所有股票。运行 `StockPortfolio` 类将产生以下输出。

```
100.0 shares of GOOG (Google)
200.0 shares of AAPL (Apple)
50.0 shares of IBM (IBM)
500.0 shares of ORCL (Oracle)
300.0 shares of MCD (McDonalds)
```

注意

在你的环境中运行 `StockPortfolio` 类时，输出行的顺序可能不同，因为底层实现使用了 `HashMap<K,V>`。`HashMap<K,V>` 不保证映射中存储元素的顺序，这同样适用于其迭代器。如果你希望迭代器按股票代码返回排序后的元素，可以使用排序集合，例如 `TreeMap<K,V>` 或 `TreeSet<E>`，来代替 `HashMap<K,V>`。

### 工作原理

`Iterable<T>` 接口是在 Java 5 中引入的，用于支持同时期引入的增强型 `for` 循环。随着这些语言增强，所有 `Collection<E>` 类都被改造以实现 `Iterable` 接口，从而允许 `Collection<E>` 类通过 `foreach` 循环进行迭代。`Iterable<T>` 接口是一个泛型类型，定义如下。

```
public interface Iterable {
Iterator iterator();
}
```

任何实现 `Iterable<T>` 的类都必须实现 `iterator()` 方法以返回一个 `Iterator<T>` 对象。通常，返回的迭代器是底层集合的默认迭代器；然而，它也可以返回一个自定义 `Iterator<E>` 的实例。在 `StockPortfolio` 类中，`Map<K,V>` 代表了股票投资组合。每个映射条目的键是股票代码，与每个键关联的值是一个 `Stock` 对象。Java 中的 `Map` 是不可迭代的；它们不是 `Collection<E>` 类。因此，它们不实现 `Iterable<T>`。然而，映射的键和值都是集合，因此是 `Iterable` 的。我们希望 `Iterable<T> iterator()` 方法的实现返回一个遍历投资组合映射中值（股票引用）的 `Iterator<E>`；因此，我们的 `Iterable<T>` 实现以股票类型进行参数化。

```
public class StockPortfolio implements Iterable
```

`Map<K,V> values()` 方法返回映射值的 `Collection<E>`；在本例中，是一个 `Stock` 的 `Collection<E>`。然后，`iterator()` 方法的实现可以简单地返回此 `Collection<T>` 的 `Iterator<E>`。

```
@Override
public Iterator iterator() {
return portfolio.values().iterator();
}
```

通过这种 `Iterable<Stock>` 的实现，无论是传统的 `foreach` 循环还是 `forEach` 实现，都可以迭代 `StockPortfolio` 实例并打印每只股票。

```
myPortfolio.forEach(s->System.out.println(s));
```

`forEach` 方法是 Java 8 发布时新增到 `Iterable<T>` 接口中的。该方法对 `Iterable` 中的每个元素执行指定的操作，直到所有元素都被处理完毕，或者指定的操作抛出异常。在本解决方案中，指定的操作是一个 lambda 表达式（参见第 6 章），它打印 `myPortfolio` 可迭代对象中每个元素的值。

你会注意到 `StockPortfolio` 还包含 `add(List<Stock>)` 方法，该方法允许从 `List<E>` 填充投资组合。此方法也使用 `foreach` 循环来迭代输入的 `List<T>`。同样，这是可行的，因为 `List` 是 `Iterable` 的。（请注意，此方法在代码中从未被调用；它仅用于说明目的。）

注意

我们的 `StockPortfolio` 实现存在一个问题。我们费了很大力气不暴露类的内部实现细节（投资组合映射）。这允许我们在不影响 `StockPortfolio` 客户端代码的情况下更改实现。然而，当我们实现 `Iterable<T>` 时，我们实际上通过 `iterator()` 方法导出了底层的投资组合映射。不幸的是，Java 没有提供 `UnmodifiableIterator` 类来包装迭代器并防止修改底层集合。不过，实现这样一个类很简单，它将 `hasNext()` 和 `next()` 调用转发给被包装的迭代器，但让 `remove()` 方法保持未实现状态（根据迭代器 Java 文档，应抛出 `UnsupportedOperationException`）。或者，你的 `iterator()` 方法可以返回一个通过调用 `Collections.unmodifiableCollection()` 类方法获得的不可修改 `Collection<E>` 的 `Iterator<E>`。我们鼓励你探索这两种选择。为了给你一个起点，在源代码下载中提供了一个可能的 `UnmodifiableIterator` 实现（参见 `UnmodifiableIterator.java`）。

正如你在本方案中所见，`Iterable<T>` 接口允许你创建与 `foreach` 实现兼容的可迭代对象。这在设计封装了实现细节的自定义基于集合的类时非常有用。请记住，为了强制封装并防止修改底层集合，你应该实现前面注意事项中提到的解决方案之一。

## 7.8 迭代集合

### 问题

你的应用程序包含 `Collection<E>` 类型，并且你想要迭代其中的元素。



### 解决方案

在任何扩展或实现了 `java.util.Collection` 的类型上生成一个流，然后对集合中的每个元素执行所需的任务。在以下代码中，一个装载了 `Stock` 对象的 `ArrayList` 演示了流的概念。

```
public class StreamExample {
static List myStocks = new ArrayList();
private static void createStocks(){
myStocks.add(new Stock("ORCL", "Oracle", 500.0));
myStocks.add(new Stock("AAPL", "Apple", 200.0));
myStocks.add(new Stock("GOOG", "Google", 100.0));
myStocks.add(new Stock("IBM", "IBM", 50.0));
myStocks.add(new Stock("MCD", "McDonalds", 300.0));
}
public static void main(String[] args){
createStocks();
// 遍历每个元素并打印股票名称
myStocks.stream()
.forEach(s->System.out.println(s.getName()));
boolean allGt = myStocks.stream()
.allMatch(s->s.getShares() > 100.0);
System.out.println("所有股票股数均大于 100.0？ " + allGt);
// 打印所有股数超过 100 的股票
System.out.println("== 我们持有超过 100 股的股票如下：");
myStocks.stream()
.filter(s -> s.getShares() > 100.0)
.forEach(s->System.out.println(s.getName()));
System.out.println("== 以下股票按股数排序：");
Comparator byShares = Comparator.comparing(Stock::getShares);
Stream sortedByShares = myStocks.stream()
.sorted(byShares);
sortedByShares.forEach(s -> System.out.println("股票: " + s.getName() + " - 股数: " + s.getShares()));
// 可能返回一个值，也可能不返回
Optional maybe = myStocks.stream()
.findFirst();
System.out.println("第一支股票: " + maybe.get().getName());
List newStocks = new ArrayList();
Optional maybeNot = newStocks.stream()
.findFirst();
Consumer myConsumer = (s) ->
{
System.out.println("第一支股票 (Optional): " + s.getName());
};
maybeNot.ifPresent(myConsumer);
if(maybeNot.isPresent()){
System.out.println(maybeNot.get().getName());
}
newStocks.add(new Stock("MCD", "McDonalds", 300.0));
Optional maybeNow = newStocks.stream()
.findFirst();
maybeNow.ifPresent(myConsumer);
}
}
输出结果为：
Oracle
Apple
Google
IBM
McDonalds
所有股票股数均大于 100.0？ false
== 我们持有超过 100 股的股票如下：
Oracle
Apple
McDonalds
== 以下股票按股数排序：
股票: IBM - 股数: 50.0
股票: Google - 股数: 100.0
股票: Apple - 股数: 200.0
股票: McDonalds - 股数: 300.0
股票: Oracle - 股数: 500.0
第一支股票: Oracle
第一支股票 (Optional): McDonalds
```

执行这段代码的结果演示了使用流的概念。遍历数据集合不再需要外部迭代（`for` 循环）。

### 工作原理

在 Java 8 之前，遍历集合需要某种循环块。这被称为*外部迭代*，也称为按顺序的*程序化循环*。在大多数情况下，`for` 循环会遍历集合中的每个元素，并根据应用程序的需求处理每个元素。虽然 `for` 循环是执行迭代的合理解决方案，但它既不够直观，又过于冗长。自 Java 8 发布以来，遍历集合的样板代码被移除，同时也不再需要详细说明迭代是如何完成的。编译器已经知道如何迭代集合，那么为什么还要精确地告诉它怎么做呢？为什么不直接告诉编译器你想要迭代集合并对每个元素执行一个任务呢？流的概念实现了这种放手式的迭代方法。

让编译器来处理不直观的循环，只需将任务交给编译器，并告诉它对每个元素执行什么操作。这个概念被称为*内部迭代*。通过内部迭代，你的应用程序决定需要迭代什么，而 JDK 决定如何执行迭代。内部迭代不仅减轻了编写循环逻辑的需求，还具有其他优势。其中一个优势是内部迭代不限于按顺序迭代元素。因此，JDK 决定如何迭代，为手头的任务选择最佳算法。内部迭代也更容易利用并行计算。这个概念涉及将任务细分为更小的问题，同时解决每个问题，然后合并结果。

流是在所有集合类型上生成的对象引用序列。Stream API 使得对这些对象引用执行一系列聚合操作成为可能，并返回结果或内联地将更改应用于对象。这也被称为*管道*。生成和使用流的伪代码如下所示。

```
Collection -> (Stream) -> (零个或多个中间操作) -> (终端操作)
```

让我们把这个伪代码放到一个真实的例子中。在解决方案中，一个 `Stock` 对象列表用于演示流迭代。假设你想打印出每个股数超过指定阈值（本例中为 100 股）的股票。你可以使用以下代码来执行此任务。

```
myStocks.stream()
.filter(s -> s.getShares() > 100.0)
.forEach(s->System.out.println(s.getName()));
```

在前面的例子中，一个称为 `filter()` 的*中间*操作对元素施加了一个限制，从而过滤掉所有不匹配所提供谓词的元素。该谓词以 lambda 表达式形式编写；它测试每个元素并返回一个布尔结果。该示例中的*终端*操作使用 `forEach()` 来打印每个匹配的元素。终端操作是管道中的最后一个操作，它产生一个非流的结果，例如一个原始值、集合或根本不产生值。在示例中，没有返回任何结果。

要在 `Collection` 类型上生成流，请调用 `stream()` 方法，该方法返回一个 `Stream` 类型。在大多数情况下，`Stream` 类型不是期望的结果，因此 Stream API 使得可以在流上调用零个或多个中间操作，形成一个操作管道。例如，在解决方案中，`Stock` 对象列表使用以下代码按股数排序。请注意，`Comparator byShares` 被应用于流中的每个对象，并返回 `Stream<Stock>` 作为结果。

```
Stream sortedByShares = myStocks.stream()
.sorted(byShares);
```

在前面的示例中，对流执行了一个单一的中间操作 `sorted()`。如前所述，可以有多个中间操作链接到这个管道，从而对满足前一个操作条件的对象执行下一个操作。每个中间操作都返回一个 `Stream` 类型。每个管道可以包含一个终端操作，从而将终端操作应用于每个结果流对象。如前所述，终端操作可能会也可能不会返回结果。在前面的示例中，没有应用终端操作。

注意

Stream 的文档（[`https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/stream/Stream.html`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/stream/Stream.html)）列出了流上所有可用的中间操作和终端操作。

流对 Java 编程语言来说是一个革命性的变化。它们改变了开发者对程序的思考方式，使开发者更高效，代码更高效。虽然像 `for` 循环这样的传统迭代技术仍然被认为是有效的过程，但在使用 Java 8 或更高版本时，流是首选的迭代技术。

## 7.9 遍历 Map

### 问题

你使用了某个 `Map` 类，例如 `HashMap` 或 `TreeMap`，并且需要遍历其键、值或两者。你还希望在遍历映射的同时从中移除元素。



### 解决方案

有多种方式可以遍历 `Map` 类。选择哪种方法取决于你需要访问 Map 的哪些部分，以及是否需要在遍历时从 Map 中移除元素。`StockPortfolio1` 类是上一个配方中 `StockPortfolio` 类的延续。它新增了三个方法——`summary()`、`alertList()` 和 `remove(List<String>)`——用于演示遍历投资组合 Map 的替代方法：`Map<String, Stock> portfolio = new HashMap<>();`。

以下是该类的代码。

```
import org.java17recipes.chapter07.recipe07_07.Stock;
import org.java17recipes.chapter07.recipe07_06.StockScreener;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.Iterator;
import java.util.List;
import java.util.Map;
import java.util.stream.Collectors;
public class StockPortfolio1 implements Iterable {
Map portfolio = new HashMap();
public void add(Stock stock) {
portfolio.put(stock.getSymbol(), stock);
}
public void add(List stocks) {
for (Stock s : stocks) {
portfolio.put(s.getSymbol(), s);
}
}
public void remove(String stock) {
portfolio.remove(stock);
}
public void remove(List sellList) {
Iterator keyIter = portfolio.keySet().iterator();
while (keyIter.hasNext()) {
if (sellList.contains(keyIter.next())) {
keyIter.remove();
}
}
}
/**
* 利用 for 循环遍历 Map 键，并应用过滤器获取所需股票
* @return
*/
public List alertListLegacy() {
System.out.println("==用于过滤和收集的传统技术==");
List alertList = new ArrayList();
for (Stock stock : portfolio.values()) {
if (!StockScreener.screen(stock.getSymbol(), StockScreener.Screen.PE, 20)) {
alertList.add(stock);
}
}
return alertList;
}
/**
* 利用流和过滤器获取所需股票
* @return
*/
public List alertList(){
return
portfolio.values().stream()
.filter(s->!StockScreener.screen(s.getSymbol(), StockScreener.Screen.PE, 20))
.collect(Collectors.toList());
}
public void summary() {
System.out.println("==用于遍历 Map.Entry 的传统技术==");
for (Map.Entry entry : portfolio.entrySet()) {
System.out.println("股票 = " + entry.getKey() + ", 股数 = " + entry.getValue().getShares());
}
System.out.println("==使用新的 foreach 和 lambda 组合==");
portfolio.forEach((k,v)->System.out.println("股票 = " + k + ", 股数 = " + v.getShares()));
}
@Override
public Iterator iterator() {
return portfolio.values().iterator();
}
public static void main(String[] args) {
StockPortfolio1 myPortfolio = new StockPortfolio1();
myPortfolio.add(new Stock("ORCL", "Oracle", 500.0));
myPortfolio.add(new Stock("AAPL", "Apple", 200.0));
myPortfolio.add(new Stock("GOOG", "Google", 100.0));
myPortfolio.add(new Stock("IBM", "IBM", 50.0));
myPortfolio.add(new Stock("MCD", "McDonalds", 300.0));
// foreach 循环（使用从 iterator() 方法返回的 Iterator）
for (Stock stock : myPortfolio) {
System.out.println(stock);
}
myPortfolio.forEach((stock)->System.out.println(stock));
List sellList = new ArrayList();
sellList.add("IBM");
sellList.add("GOOG");
myPortfolio.remove(sellList);
System.out.println("投资组合摘要:");
myPortfolio.summary();
System.out.println("警报:");
for (Stock stock : myPortfolio.alertList()) {
System.out.println("警报: " + stock.getSymbol());
}
}
}
输出结果为：
100.0 shares of GOOG (Google)
200.0 shares of AAPL (Apple)
50.0 shares of IBM (IBM)
500.0 shares of ORCL (Oracle)
300.0 shares of MCD (McDonalds)
100.0 shares of GOOG (Google)
200.0 shares of AAPL (Apple)
50.0 shares of IBM (IBM)
500.0 shares of ORCL (Oracle)
300.0 shares of MCD (McDonalds)
投资组合摘要:
==用于遍历 Map.Entry 的传统技术==
股票 = AAPL, 股数 = 200.0
股票 = ORCL, 股数 = 500.0
股票 = MCD, 股数 = 300.0
==使用新的 foreach 和 lambda 组合==
股票 = AAPL, 股数 = 200.0
股票 = ORCL, 股数 = 500.0
股票 = MCD, 股数 = 300.0
警报:
AAPL: PE = 6.152926256441124
ORCL: PE = 13.539727307058891
MCD: PE = 20.073565345827422
警报: MCD
```



### 工作原理

`Map` 对象包含一组键/值对。当你需要存储一个索引（键）并将其与特定值关联时，`Map` 会非常有用。`Map` 对象不能包含任何重复的键，且每个键只能映射到一个值。解决方案的源代码（`StockPortfolio1.java`）演示了如何向映射中添加和删除条目。它还包含了本方案中列出的源代码，演示了如何使用传统技术以及利用 lambda 表达式和流的新语法来迭代映射条目。

`summary()` 方法使用 `foreach` 循环实现来迭代投资组合映射的 `Entry` 集合。要使用传统代码进行迭代，`Map entrySet()` 方法会返回一个 `Map.Entry` 对象的 `Set`。在循环内部，你可以通过在该条目上分别调用 `key()` 和 `value()` 方法来访问当前 `Map.Entry` 的键和值。当你需要在迭代时同时访问映射的键和值，并且不需要从映射中删除元素时，请使用此迭代方法。观察新语法，你会发现同样的迭代可以用一行代码完成。新语法利用了 `forEach()` 方法，该方法是在 Java 8 中添加到 `Map` 接口的。它将一个 lambda 表达式应用于列表中的每个条目。该 lambda 表达式将键和值都作为参数，并将它们打印出来。

`alertListLegacy()` 方法使用 `foreach` 循环实现来迭代投资组合映射的值。`Map values()` 方法返回映射值的集合；在本例中，是一个股票集合。当你只需要访问映射值，并且不需要从列表中删除元素时，请使用此迭代方法。类似地，如果你只需要访问映射键（同样，无需删除元素），你可以使用 `keySet()` 方法进行迭代。

```
for (String symbol : portfolio.keySet()) {
...
}
```

如果你在通过键集迭代时还需要访问映射值，请避免以下做法，因为它效率非常低。相反，应使用 `summary()` 方法中展示的迭代方式。

```
for (String symbol : portfolio.keySet()) {
Stock stock = portfolio.get(symbol);
...
}
```

观察解决方案中的 `alertList()` 方法，你会发现使用流、过滤器和收集器的组合，可以用更少的工作完成同样的迭代。在 `alertList()` 中，首先生成一个流，然后以 lambda 表达式的形式对该流应用一个过滤器。最后，对过滤器应用一个收集器，创建一个 `List<Stock>` 作为返回值。

`remove(List<String>)` 方法接收一个代表要从投资组合中移除的股票符号列表。该方法使用 `keySet()` 迭代器遍历投资组合映射的键，如果当前映射条目中的股票在待移除列表中，则将其移除。请注意，映射元素是通过迭代器的 `remove()` 方法移除的。这是可行的，因为映射支持键集，因此通过键集迭代器所做的更改会反映在映射中。你也可以使用其 `values()` 迭代器来遍历投资组合映射。

```
Iterator valueIter = portfolio.values().iterator();
while (valueIter.hasNext()) {
if (sellList.contains(valueIter.next().getSymbol())) {
valueIter.remove();
}
}
```

与键集一样，值集合也由映射支持，因此通过值迭代器调用 `remove()` 会导致从投资组合映射中移除当前条目。

总之，如果你需要在遍历映射时从中删除元素，请使用映射的某个集合迭代器进行遍历，并通过该迭代器删除映射元素，如 `remove(List<String>)` 方法所示。这是在迭代期间安全删除映射元素的唯一方法。否则，如果你不需要删除映射元素，你可以使用 `foreach` 循环以及本方案解决方案中展示的迭代方法之一。

## 7.10 并行执行流

### 问题

你想要并行迭代一个集合，以便将工作分布到多个 CPU 上。

### 解决方案

在集合上使用流结构，并调用 `parallelStream()` 作为第一个中间操作，以利用多 CPU 处理能力。以下类演示了 `parallelStream()` 操作的多种用法。

```
public class StockPortfolio2 {
static List myStocks = new ArrayList();
private static void createStocks(){
myStocks.add(new Stock("ORCL", "Oracle", 500.0));
myStocks.add(new Stock("AAPL", "Apple", 200.0));
myStocks.add(new Stock("GOOG", "Google", 100.0));
myStocks.add(new Stock("IBM", "IBM", 50.0));
myStocks.add(new Stock("MCD", "McDonalds", 300.0));
}
public static void main(String[] args){
createStocks();
// 遍历每个元素并打印股票名称
myStocks.stream()
.forEach(s->System.out.println(s.getName()));
boolean allGt = myStocks.parallelStream()
.allMatch(s->s.getShares() > 100.0);
System.out.println("所有股票持股数均大于 100 股？ " + allGt);
// 打印所有持股数超过 100 股的股票
System.out.println("== 我们持有超过 100 股的股票如下：");
myStocks.parallelStream()
.filter(s -> s.getShares() > 100.0)
.forEach(s->System.out.println(s.getName()));
System.out.println("== 以下股票按持股数排序：");
Comparator byShares = Comparator.comparing(Stock::getShares);
Stream sortedByShares = myStocks.parallelStream()
.sorted(byShares);
sortedByShares.forEach(s -> System.out.println("股票: " + s.getName() + " - 持股数: " + s.getShares()));
// 可能返回一个值，也可能不返回
Optional maybe = myStocks.parallelStream()
.findFirst();
System.out.println("第一只股票: " + maybe.get().getName());
List newStocks = new ArrayList();
Optional maybeNot = newStocks.parallelStream()
.findFirst();
Consumer myConsumer = (s) ->
{
System.out.println("第一只股票 (Optional): " + s.getName());
};
maybeNot.ifPresent(myConsumer);
if(maybeNot.isPresent()){
System.out.println(maybeNot.get().getName());
}
newStocks.add(new Stock("MCD", "McDonalds", 300.0));
Optional maybeNow = newStocks.stream()
.findFirst();
maybeNow.ifPresent(myConsumer);
}
}
```

### 工作原理

默认情况下，操作是在串行流中执行的。但是，你可以指定 Java 运行时将操作拆分为多个子任务，从而利用多个 CPU 来提高性能。当操作以这种方式执行时，它们就是“并行”执行的。Java 运行时可以通过调用 `parallelStream()` 中间操作将流分区为多个子流。调用此操作后，聚合操作可以处理多个子流，最后再将结果合并。你也可以通过调用 `BaseStream.parallel()` 操作来并行执行流。自然，并行流比串行流有更高的开销，因此它们只在需要解决性能问题时才使用。

## 7.11 总结

本章探讨了各种数据结构以及如何使用它们。首先，我们了解了 `Enums` 并学习了如何有效地使用它们。接下来，我们介绍了 `Arrays` 和 `ArrayList` 的基础知识，并学习了如何在这些结构中迭代元素。本章还涵盖了 Java 泛型，它允许你将对象类型与容器类型解耦，从而提供更类型安全和高效的代码。最后，本章介绍了 Streams API，这是 Java 8 中为处理集合而引入的最重要的更新之一。



