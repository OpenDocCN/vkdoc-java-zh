# 11. 分组收集器

假设我们要计算按年龄分组的人的平均支出。我们希望创建一个收集器，返回一个 `Map<Integer, Double>`，其中 Integer 代表年龄组，Double 代表平均支出。

为了验证这个收集器，我们使用一个简单的输出：

```
1   [. . .]

3   [省略：按之前所示创建 persons]

5   printResults(persons.getPersons().parallelStream()
6     .collect(new GroupAverageCollector()));

8   [. . .]

10   private static void printResults(Map results) {
11     System.out.println("");
12     for (int group : results.keySet()) {
13       System.out.println("Group: " + group + ";  avg: " + results.get(group));
14     }
15   }
清单 11-1.
准备输出
```

回想一下本书开头介绍的数据结构。所有人员都包含一个购买列表。价格内部以“分”为单位存储，以避免浮点运算。为了使收集器保持精简，我们使用一个辅助类来累加“分”和人数，并计算平均值 = sum(分) / 100 / 人数。

```
1   public class AverageBuilder {

3     private int _count;
4     private long _cents;

6     public int getCount() {
7       return _count;
8     }

10    public long getCents() {
11      return _cents;
12    }

14    public void add(long cents) {
15      _count++;
16      _cents += cents;
17    }

19    public void add(AverageBuilder other) {
20      _count += other.getCount();
21      _cents += other.getCents();
22    }

24    public double getAverage() {
25      return _cents / 100D / _count;
26    }
27   }
清单 11-2.
用于计算平均值的辅助类
```

这个类包含两个 `add` 方法：一个用于添加一个人的支出，另一个用于合并两个 `AverageBuilder`。

在第 10 章中，我们从 `SummingUnit` 开始，它在收集器中被重用。本章使用的收集器并非基于这样的现有类。相反，它包含了用于累加、合并和完成转换的函数：

```
1   public class GroupAverageCollector implements
2       Collector, Map> {

4     @Override
5     public Supplier> supplier() {
6       return () -> new HashMap();
7     }

9     @Override
10    public BiConsumer, Person> accumulator() {
11      return (m, p) -> add(m, p);
12    }

14    @Override
15    public BinaryOperator> combiner() {
16      return (left, right) -> {
17        combine(left, right);
18        return left;
19      };
20    }

22    @Override
23    public Function, Map> finisher() {
24      return m -> finish(m);
25    }

27    @Override
28    public Set characteristics() {
29      return EnumSet.of(Characteristics.UNORDERED);
30      // 没有 Characteristics.CONCURRENT!
31    }

33    private static void add(Map map, Person person) {
34      int group = person.getAge() / 10;
35      if (!map.containsKey(group)) {
36        map.put(group, new AverageBuilder());
37      }
38      long cents = person.getBuying().values()
39              .stream().mapToLong(a -> a.getAmount().getCents()).sum();
40      map.get(group).add(cents);
41    }

43    private static void combine(Map left,
44            Map right) {
45      for (int group : right.keySet()) {
46        if (!left.containsKey(group)) {
47          left.put(group, right.get(group));
48        } else {
49          left.get(group).add(right.get(group));
50        }
51      }
52    }

54    private static Map finish(Map map) {
55      Map result = new HashMap();
56      for (int group : map.keySet()) {
57        result.put(group, map.get(group).getAverage());
58      }
59      return result;
60    }

62 }
清单 11-3.
实现 accumulator、combiner 和 finisher 的 GroupAverageCollector
```

第 2 行。此收集器接收 `Person` 类型的对象。它将值累加到 `Map<Integer, AverageBuilder>` 中，并返回 `Map<Integer, Double>`。

第 6 行。`supplier` 为每个线程生成一个新的 `HashMap`，因为没有设置 `Characteristics.CONCURRENT`。

第 11 行。请记住，`accumulator`、`combiner` 和 `finisher` 不返回值，而是返回指向在此收集器中作为静态函数（方法）实现的函数的指针。

第 3 行。在此示例中，年龄组简单地通过整数除法计算。

第 39 行。所有购买记录都存储在一个列表中。我们使用流来累加“分”。

`combine` 函数将两个映射合并为一个，而 `finish` 函数将中间结果转换为所需的输出格式。作为 Java 开发人员，您应该能够理解所有这些函数。

由于我们没有使用 `Characteristics.CONCURRENT`，流框架会为每个线程创建一个新的映射。尽管是并行处理，但这些函数的实现方式与过去简单的顺序编程时代相同。

如果我们使用 `Characteristics.CONCURRENT` 会发生什么？在继续阅读之前，请思考一下这个问题。希望您能认识到所有的影响。

（花点时间思考一下）

如前所述，框架只会调用一次 `supplier`。它创建一个映射，供所有线程使用。首先，`HashMap` 不是线程安全的。这可以通过将其替换为 `ConcurrentHashMap` 轻松解决。但是，如果您运行该程序，将会得到不确定的结果。

`add` 方法是一个临界区。它可能随时被中断。并且它包含一个共享状态。这两者都可能导致如第 10 章所述的错误值。一个简单的解决方案是使 `add` 同步：

```
1   private static synchronized void add(Map map, Person person) {...}
清单 11-4.
通过 synchronized 保护的临界区
```

由于同步会降低性能，这通常不是一个好的选择。

## 总结

在本章中，我讨论了一个真正“收集”数据的收集器。根据收集器的设计和特性，可以避免特殊的多线程处理。因此，无需显式同步、锁定等即可实现并行编程。但是，为了创建适合并行处理环境的类似顺序的程序，了解多线程陷阱的基础知识是必要的。

## 最后的话

这本精简书籍的目的是向您展示 Java Lambda 和并行流的原理。它没有解释每一个细节，例如每个中间或终端操作。掌握了其原理后，通过查阅 API 文档来理解这些操作应该没有问题。一个很好的起点是 `java.util.stream` 包的文档（ [`https://docs.oracle.com/javase/8/docs/api/index.html?java/util/stream/package-summary.html`](https://docs.oracle.com/javase/8/docs/api/index.html?java/util/stream/package-summary.html) ）。

开始您的旅程的最后一个提示：学习使用流的一个好方法是交互式地进行。Java 9 附带了一个交互式 shell，非常适合此目的。在撰写本文时，Java 9 已作为早期访问版本提供（ [`https://jdk9.java.net`](https://jdk9.java.net) ）。我不定期地撰写关于这个 shell 的博客（ [`http://blog.mueller-bruehl.de`](http://blog.mueller-bruehl.de) ）。到目前为止的主要文章是“使用 jshell 进行交互式 Java 编程”（ [`http://blog.mueller-bruehl.de/netbeans/interactive-java-with-jshell`](http://blog.mueller-bruehl.de/netbeans/interactive-java-with-jshell) ）。

现在是时候让您自己去探索 Lambda 和流的精彩世界了。我希望这本书对您来说相当有趣、易于理解且有价值。请让我知道！



## 附录 A：创建演示数据的程序

要执行本书中提到的分析，你需要一些演示数据。这些数据是通过一个程序创建的，该程序将在后续页面中展示。此软件的开发初衷是快速生成数据，而非用于教学目的。因此，其设计比具有教学价值的程序更为简化。

### 基础数据

该程序需要一些基础信息来创建数据。这些数据存储在四个 CSV（逗号分隔值）文件中（列表 A-1 至 A-4）。

```
1   Andersson
2   Angelopoulos
3   Athanasiadis
4   Bakker
5   Bauer
6   Beck
7   Becker
8   Bernard
9   Bianchi
10   Borg
11   [...]演示数据 Surnames.csv
列表 A-1.
Surnames.csv
```

```
1   Aadhya
2   Aisha
3   Aline
4   Amelia
5   Ananya
6   Anette
7   Anika
8   Anja
9   Antje
10   Ashraqat
11   [...]
列表 A-2.
GivenNamesFemale.csv
```

```
1   Adam
2   Ahmet
3   Ali
4   Andreas
5   Aron
6   Ben
7   Bilal
8   Bram
9   Charlie
10   Christoph
11   [...]
列表 A-3.
GivenNamesMale.csv
```

```
1   CarSmall;15000;1;5
2   CarBig;80000;1;3
3   Bred;2.5;5;1000
4   Water;0.3;10;1000
5   Shirt;20;3;100
6   Butter;3;5;250
7   Milk;0.7;5;500
8   Book;30;5;80
9   Beer;1.5;10;600
10   Potatoes;1.90;10;900
11   [...]演示数据 Articles.csv
列表 A-4.
Articles.csv
```

前三个文件仅包含姓名，而文章文件则需要稍作解释。每篇文章由四列组成：

*   名称
*   价格
*   单次购买最大数量
*   购买概率

这些（虚构的）数据用于模拟一种“真实”的购买行为。例如，汽车的价格远高于牛奶。另一方面，人们通常更频繁地购买牛奶，且购买量更大。

### 文章

文件 `Articles.csv` 的每一行都将传递给 `Article` 类的构造函数。该行将被拆分为四个值，并分配给内部属性。除此之外，每篇文章都由一个文章编号定义，该编号在数据创建时分配。与生产环境中的软件不同，我们假设所有数据都是正确的。因此，此演示未实现任何检查。

![A435427_1_En_BookBackmatter_Figa_HTML.jpg](img/A435427_1_En_BookBackmatter_Figa_HTML.jpg) 为简洁起见，以下类中省略了 `package` 和 `import` 指令（列表 A-5）。所需的导入将由现代 IDE 自动添加。例如，使用 NetBeans，你可以通过按 {Ctrl+Shift+I} 来执行此任务。

```
1   public class Article {

3     private final int _articleNo;
4     private String _name;
5     private Money _price;
6     private int _maxSell;
7     private int _probability;

9     public Article(String articleData, int articleNo) {
10       _articleNo = articleNo;
11       try {
12         String[] parts = articleData.split(";");
13         _name = parts[0];
14         _price = new Money(parts[1]);
15         _maxSell = Integer.parseInt(parts[2]);
16         _probability = Integer.parseInt(parts[3]);
17       } catch (NumberFormatException e) {
18         System.out.println(e.getMessage());
19         throw e;
20       }
21     }

23     public String getName() {
24       return _name;
25     }

27     public void setName(String name) {
28       _name = name;
29     }

31     public Money getPrice() {
32       return _price;
33     }

35     public void setPrice(Money price) {
36       _price = price;
37     }

39     public int getMaxSells() {
40       return _maxSell;
41     }

43     public void setMaxSell(int maxSell) {
44       _maxSell = maxSellArticle.java;
45     }

47     public int getProbability() {
48       return _probability;
49     }

51     public void setProbability(int probability) {
52       _probability = probability;
53     }

55   }
列表 A-5.
Article.java
```

在列表 A-5 的第 5 行，变量 `_price` 被声明为 `Money` 类型。此类将在下文展示。除此之外，该类主要由 getter 和 setter 方法组成。

如果文章被购买或出售，其数量和价格信息将被存储。价格不能通过数量乘以单价得出，因为价格可能因折扣而改变。

此信息存储在 ArticleInfo 中（列表 A-6）。

```
1   public class ArticleInfo {
2     private final int _articleNo;
3     private long _quantity;
4     private Money _amount;

6     public ArticleInfo (int articleNo){
7       _articleNo = articleNo;
8       _amount = new Money();
9     }
10     public int getArticleNo() {
11       return _articleNo;
12     }

14     public long getQuantity() {
15       return _quantity;
16     }

18     public void setQuantity(long quantity) {
19       _quantity = quantity;
20     }

22     public Money getAmount() {
23       return _amount;
24     }

26     public void setAmount(Money amount) {
27       _amount = amountArticleInfo.java;
28     }

30     public void addQuantity (long quantity){
31       _quantity += quantity;
32     }
33     public void addPrice(long cents){
34       _amount.add(cents);
35     }
36   }
列表 A-6.
ArticleInfo.java
```



### 人员

在 `Person` 类中，不仅存储了姓名和年龄，还存储了购买记录——以及（如果是供应商）销售记录（清单 A-7）。对于后者，使用了 `ConcurrentHashMap`（而不是简单的 `HashMap`）。这是实现并行化的一个小前提（参见正文中的描述）。

```
1   public class Person {

3     private String _givenName;
4     private String _surname;
5     private Gender _gender;
6     private int _age;
7     private Map _selling = new ConcurrentHashMap();
8     private Map _buying = new ConcurrentHashMap();
9     private int _discount;

11     public String getGivenName() {
12       return _givenName;
13     }

15     public void setGivenName(String givenName) {
16       _givenName = givenName;
17     }

19     public String getSurname() {
20       return _surname;
21     }

23     public void setSurname(String surname) {
24       _surname = surname;
25     }

27     public Gender getGender() {
28       return _gender;
29     }

31     public void setGender(Gender gender) {
32       _gender = genderPerson.java;
33     }

35     public boolean isFemale(){
36       return _gender == Gender.Female;
37     }

39     public int getAge() {
40       return _age;
41     }

43     public void setAge(int age) {
44       _age = age;
45     }

47     public boolean isVendor() {
48       return _selling.size() > 0;
49     }

51     public int getDiscount() {
52       return _discount;
53     }

55     public void setDiscount(int discount) {
56       _discount = discount;
57     }

59     public Map getSelling() {
60       return _sellingPerson.java;
61     }

63     public void setSelling(Map selling) {
64       _selling = selling;
65     }

67     public Map getBuying() {
68       return _buying;
69     }

71     public void setBuying(Map buying) {
72       _buying = buying;
73     }

75   }
清单 A-7.
Person.java
```

所有这些人员都保存在 `Persons` 类的一个实例中。该类还包含一些用于创建所有人员的特性（清单 A-8）。

```
1   public class Persons {

3     private final static int PersonCount = 50000;
4     private final Random _random = new SecureRandom();
5     private final static Persons _instance = new Persons();
6     private final List _persons = new ArrayList();

8     public List getPersons() {
9       return _persons;
10     }

12     private final List _sellers = new ArrayList();

14     private Persons() {
15       for (int i = 0; i  surNames = DataProvider.getInstance().getSurNames();
35       person.setSurname(surNames.get(_random.nextInt(surNames.size())));
36       Map givenNameInfos = DataProvider.getInstance()
37           .getGivenNames();
38       List givenNames = givenNameInfos.keySet()
39               .stream().collect(Collectors.toList());
40       person.setGivenName(givenNames.get(_random.nextInt(givenNames.size())));
41       person.setGender(givenNameInfos.get(person.getGivenName()));
42       person.setAge(15 + _random.nextInt(80));
43       if (_random.nextInt(100) == 0) {
44         makeVendor(person);
45       }
46       return person;
47     }

49     private void makeVendor(Person person) {
50       person.setDiscount(_random.nextInt(5) * 5);
51       Map selling = person.getSelling()Persons.java;

53       for (int i = 0; i  selling = seller.getSelling();
72       Map buying = buyer.getBuying();
73       Object[] articleNumbers = selling.keySet().toArray();
74       int index = _random.nextInt(articleNumbers.length);
75       int articleNo = (int) articleNumbers[index];
76       Article article = DataProvider.getInstance()
77               .getArticles().get(articleNo);

79       if (_random.nextInt(1000) < article.getProbability()
80                                  + seller.getDiscount() / 5)Persons.java {
81         int quantity = 1 + _random.nextInt(article.getMaxSells());
82         long price = quantity * article.getPrice().getCents()
83                       * (100 - seller.getDiscount()) / 100;
84         ArticleInfo infoSelling = selling.get(articleNo);
85         infoSelling.setQuantity(infoSelling.getQuantity() + quantity);
86         infoSelling.getAmount().add(price);
87         ArticleInfo infoBuying = buying.containsKey(articleNo)
88                 ? buying.get(articleNo)
89                 : new ArticleInfo(articleNo);
90         infoBuying.addQuantity(quantity);
91         infoBuying.addPrice(price)Persons.java;
92         buying.put(articleNo, infoBuying);
93       }
94     }
95   }
清单 A-8.
Persons.java
```



### 其他类

```
1   public enum Gender {
2     Male,
3     Female;
4   }
代码清单 A-9.
Gender.java
```

金额由欧元和分（美元和分……）组成。浮点数或双精度浮点数在表示此类精确数据时存在问题，且对于我们的需求来说过于繁琐。为了演示 Lambda 表达式和流，我们创建了一个简单的 `Money` 类，该类内部使用分来存储所有金额（参见代码清单 A-10）。因此，它可以利用整数（更精确的 long 值）进行计算。

```
1   public class Money {

3     private long _cents;

5     Money() {
6       _cents = 0;
7     }

9     Money(String value) {
10       setValue(value);
11     }

13     public long getCents() {
14       return _cents;
15     }

17     public void setCents(long cents) {
18       _cents = cents;
19     }

21     public String getValue() {
22       return _cents / 100 + "." + _cents % 100;
23     }

25     public void setValue(String value) {
26       int pos = value.indexOf(".");
27       if (pos == -1) {
28         _cents = 100 * Long.parseLong(value);
29       } else {
30         _cents = 100 * Long.parseLong(value.substring(0, pos));
31         String decimals = value.substring(pos + 1) + "00";
32         _cents += Long.parseLong(decimals.substring(0, 2));
33       }
34     }

36     public void add (long cents){
37       _cents += centsMoney.java;
38     }
39   }
代码清单 A-10.
Money.java
```

`DataProvider` 用于从四个文件中读取示例数据（代码清单 A-11）。

```
1   public class DataProvider {

3     private static final DataProvider _instance = new DataProvider();
4     private List _surNames = new ArrayList();
5     private final Map _givenNames = new HashMap();
6     private final Map _articles = new HashMap();

8     public static DataProvider getInstance() {
9       return _instance;
10     }

12     private DataProvider() {
13       init();
14     }

16     public List getSurNames() {
17       return _surNames;
18     }

20     public Map getGivenNames() {
21       return _givenNames;
22     }

24     public Map getArticles() {
25       return _articles;
26     }

28     private void init() {
29       try {
30         _surNames = readFile("Surnames.csv");
31         readFile("GivenNamesFemale.csv").stream()
32                   .forEach(n -> _givenNames.put(n, Gender.Female));
33         readFile("GivenNamesMale.csv").stream()
34                   .forEach(n -> _givenNames.put(n, Gender.Male));
35         int articleNo = 0;
36         for (String line : readFile("Articles.csv")) {
37           if (!line.trim().isEmpty()) {
38             articleNo++;
39             Article article = new Article(line, articleNo);
40             _articles.put(articleNo, article);
41           }
42         };
43       } catch (IOException ex) {
44         Logger.getLogger(DataProvider.class.getName())
45                 .log(Level.SEVERE, null, ex);
46       }
47     }DataProvider.java

49     private List readFile(String fileName) throws IOException {
50       List lines = new ArrayList();
51       try (InputStream is = getClass().getResourceAsStream(fileName);
52               BufferedReader reader = new BufferedReader(new InputStreamReader\
53   (is));) {
54         String line;
55         while ((line = reader.readLine()) != null) {
56           lines.add(line);
57         }
58       }
59       return linesDataProvider.java;
60     }
61   }
代码清单 A-11.
DataProvider.java
```

### 演示程序

以下 main 方法展示了如何调用数据生成并执行分析（参见代码清单 A-12）。你可以使用此框架来执行你自己的 Lambda 表达式和流实验。

```
1   public class StreamsDemo {

3     public static void main(String[] args) {
4       System.out.println("started");
5       Persons persons = Persons.getInstance();
6       System.out.println("created " + persons.getPersons().size()
7                           + " persons.");

9       showAverage(persons.getPersons());

11       [... many other show cases ...]
12     }

14     private static void showAverage(List persons) {
15       double averageAge = persons.stream()
16               .filter(p -> p.getAge() < 20 && p.isFemale())
17               .mapToInt(Person::getAge)
18               .average()
19               .getAsDouble();
20       System.out.println("averageAge: " + averageAge);
21     }
22   }
代码清单 A-12.
StreamsDemo.java
```

此演示的源代码可在 [webdevelopment-java.​info](http://webdevelopment-java.info) 获取。¹

索引 A 匿名类 API 文档 ArticleInfo.java Article.java Articles.csv AverageBuilders B 行为参数化 C 中央处理器 (CPU) 类 Money collect() 方法 特性 CONCURRENT 函数 lambda 参数 重载 SummingCollector SummingUnit 技术领域 类型 Collector 与并发 Java 并发 数字生成器 ConcurrentHashMap ctor SummingUnit D, E 数据 销售与购买 结构 DataProvider.java 数据转换 默认方法 二进制兼容性 “同名”方法 接口 Java 虚拟机 规则 演示数据 Articles.csv 列 CSV 逗号 GivenNamesFemale.csv GivenNamesMale.csv Surnames.csv F 过滤 函数式接口 G get() getAsDouble() GroupingCollector 平均支出 特性 数据结构 函数 交互式 shell Java 开发者 输出 Person 人员计数 共享状态 流框架 流 同步 H HashMap hasNext() I 整数 J, K Java 并发 计算 类 combine() CPU 执行顺序 中间操作 Java 的 限定符 并行流 并行求和 参数 序列 语法糖 技术类 用法 线程 Java 开发工具包 (JDK) Java lambda 表达式 L Lambda 表达式 条件 接口 执行时间 女性人员 Java 8 测量 方法 Lambdas 表达式 表示法 Lambdas 与 (并行) 流 语言集成查询 M .mapToLong(…)) 方法 get() Money.java N 空值 O Optional 类 OptionalDouble OptionalInt OptionalLong Optional<T> orElse() P, Q parallel() 并行处理 并行编程 并行流 二元函数 计算 演示 元素 函数式编程 lambda 表达式。 处理 编程 顺序变体 参数化 Person.java person.stream() 流水线 R reduce() 方法 run() 方法 S someCondition() someOther-Condition() Spliterator hasNext() 实现 next()。 线程 tryAdvance trySplit start() 方法 stream() 流 lambda 表达式 mapToLong(…)) 流处理 辅助条件 计算 收集到列表 具体解决方案 显式中间结果 中间操作 内部迭代器 映射 处理链 单个处理步骤 中间与终端操作。 传统方法 变体 供应商 StreamsDemo.java StreamSupport.stream SummingUnit T, U 目标列表 V, W, X, Y, Z 变量 _price 脚注 1

[`http://webdevelopment-java.info`](http://webdevelopment-java.info)
