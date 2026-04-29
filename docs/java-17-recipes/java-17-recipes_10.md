# 10. 并发

*并发*是程序同时执行不同（或相同）指令的能力。一个并发程序可以被拆分并在多个 CPU 上运行。通过编写并发程序，您可以充分利用当今的多核 CPU。即使在 I/O 密集型的单核 CPU 上，您也能看到其好处。并发是现代计算机编程中最难处理的主题之一；理解并发需要具备抽象思考的能力，而调试并发问题就像试图通过航位推测法驾驶飞机一样困难。即便如此，随着 Java 的现代版本发布，编写无错误的并发代码已经变得更容易（也更普及）。

本章介绍了并发任务最常见的需求——从运行后台任务到将计算拆分为工作单元。在本章中，您将找到在 Java 中实现并发的最新方法。

## 10.1 启动后台任务

### 问题

您有一个需要在主线程之外运行的任务。

### 解决方案

创建一个包含需要在不同线程中运行的任务的类实现。在任务实现类中实现一个 `Runnable` 函数式接口，并启动一个新线程。在以下示例中，一个计数器模拟活动，同时一个独立的任务在后台运行。

```
public class Recipe10_1 {
public static void main(String[] args) {
Recipe10_1 recipe = new Recipe10_1();
recipe.startProcess();
}
private void startProcess() {
Thread backgroundThread = new Thread(new Runnable() {
public void run() {
doSomethingInBackground();
}
},"Background Thread");
System.out.println("Start");
backgroundThread.start();
for (int i= 0;i < 10;i++) {
System.out.println(Thread.currentThread().getName()+": is counting "+i);
}
System.out.println("Done");
}
private void doSomethingInBackground() {
System.out.println(Thread.currentThread().getName()+
": is Running in the background");
}
}
```

如果代码被执行多次，输出结果会因时而异。后台线程独立执行，因此其消息在每次运行时的打印顺序都不同。

如果您使用 lambda 表达式，创建后台线程的相同代码可以编写如下。

```
Thread backgroundThread = new Thread(this::doSomethingInBackground, "Background Thread");
```

以下是输出结果。

```
Start
main: is counting 0
main: is counting 1
main: is counting 2
main: is counting 3
main: is counting 4
main: is counting 5
main: is counting 6
main: is counting 7
main: is counting 8
main: is counting 9
Done
Background Thread: is Running in the background
```



### 工作原理

`Thread` 类允许在与当前线程不同的新线程（执行路径）中执行代码。`Thread` 构造函数需要一个实现了 `Runnable` 接口的类作为参数。`Runnable` 接口只需要实现一个方法：`public void run()`。因此，它是一个函数式接口，这便于使用 lambda 表达式。当调用 `Thread.start()` 方法时，它会创建一个新线程，并调用 `Runnable` 接口中的 `run()` 方法。

JVM 中有两种类型的线程：用户线程和守护线程。用户线程会一直执行，直到其 `run()` 方法完成，而守护线程在应用程序需要退出时可以被终止。如果 JVM 中只有守护线程在运行，应用程序就会退出。当你开始创建多线程应用程序时，必须了解这些区别，并理解何时使用每种类型的线程。

通常，守护线程的 `Runnable` 接口不会完成；例如，一个 `while (true)` 循环。这使得这些线程能够在程序整个生命周期内定期检查或执行某个条件，并在程序执行完毕时被丢弃。如果你的程序没有按预期关闭和/或退出，你可能需要检查正在活跃运行的线程。要将线程设置为守护线程，请在调用 `thread.start()` 方法之前使用 `thread.setDaemon(true)`。

## 10.2 更新（和迭代）Map

### 问题

你需要从多个线程更新一个 `Map<K,V>` 对象，并且希望确保更新不会破坏 `Map` 对象的内容，且 `Map` 对象始终处于一致状态。你还希望在其他线程更新 `Map` 对象的同时，遍历（查看）`Map` 对象的内容。

### 解决方案

使用 `ConcurrentMap<K,V>` 来更新 map 条目。以下示例创建了 1000 个线程。然后每个线程同时尝试修改 map。主线程等待一秒钟，然后开始迭代 map（即使其他线程仍在修改 map）。

```
public class Recipe10_2 {
Set updateThreads = new HashSet();
public static void main(String[] args) {
Recipe10_2 recipe = new Recipe10_2();
recipe.startProcess();
}
private void startProcess() {
ConcurrentMap concurrentMap = new ConcurrentHashMap();
for (int i =0;i  {
System.out.println("Key :"+entry.getKey()+" Value:"+entry.getValue());
});
updateThreads.stream().forEach((thread) -> {
thread.interrupt();
});
}
Random random = new Random();
private void startUpdateThread(int i, final ConcurrentMap concurrentMap) {
Thread thread = new Thread(() -> {
while (!Thread.interrupted()) {
int randomInt = random.nextInt(20);
concurrentMap.put(randomInt, UUID.randomUUID().toString());
}
});
thread.setName("Update Thread "+i);
updateThreads.add(thread);
thread.start();
}
}
```

以下是输出结果。

```
Key :0 Value:d4476b69-a28f-44d6-b1ce-f47128ed5a59
Key :1 Value:1f47b9ee-5b01-4ea3-ac2a-b1a2231187ad
Key :2 Value:95322e14-73a5-449e-87e2-d7d0dafdbabd
...
```

### 工作原理

为了以并发方式对哈希表执行操作，`ConcurrentHashMap` 允许多个线程安全地并发修改哈希表。`ConcurrentHashMap` 是一个哈希表，支持检索的完全并发性和更新的可调预期并发性。在示例中，1000 个线程在短时间内对 map 进行了修改。`ConcurrentHashMap` 的迭代器以及在 `ConcurrentHashMap` 上生成的流，都允许安全地迭代其内容。使用 `ConcurrentMap` 的迭代器时，你无需担心在迭代过程中锁定 `ConcurrentMap` 的内容。

## 10.3 仅当键不存在时才向 Map 中插入键

### 问题

应用程序中的 map 会持续更新，如果键不存在，你需要向其中插入一个键/值对。因此，你需要检查键是否存在，并且需要确保在此期间没有其他线程插入相同的键。

### 解决方案

使用 `ConcurrentMap.putIfAbsent()` 方法，你可以原子性地判断 map 是否被修改。例如，以下代码使用此方法在单一步骤中完成检查和插入，从而避免了并发问题。

```
public class Recipe10_3 {
Set updateThreads = new HashSet();
Random random = new Random();
public static void main(String[] args) {
Recipe10_3 recipe = new Recipe10_3();
recipe.startProcess();
}
private void startProcess() {
ConcurrentMap concurrentMap = new ConcurrentHashMap();
for (int i = 0; i  {
System.out.println("Key :" + entry.getKey() + " Value:" + entry.getValue());
});
}
private void startUpdateThread(final int i, final ConcurrentMap concurrentMap) {
Thread thread = new Thread(() -> {
int randomInt = random.nextInt(20);
String previousEntry = concurrentMap.putIfAbsent(randomInt, "Thread # " + i + " has made it!");
if (previousEntry != null) {
System.out.println("Thread # " + i + " tried to update it but guess what, we're too late!");
} else {
System.out.println("Thread # " + i + " has made it!");
}
});
thread.start();
}
}
```

以下是输出结果。

```
Thread # 1 has made it!
Thread # 0 has made it!
Thread # 3 has made it!
Thread # 2 has made it!
Thread # 4 has made it!
Thread # 5 has made it!
Thread # 9 has made it!
Thread # 8 tried to update it but guess what, we're too late!
Thread # 10 tried to update it but guess what, we're too late!
Thread # 7 has made it!
Thread # 12 has made it!
Thread # 6 tried to update it but guess what, we're too late!
Thread # 13 tried to update it but guess what, we're too late!
Thread # 11 tried to update it but guess what, we're too late!
```

运行程序时，一些条目会成功插入，而另一些则不会，因为其他线程已经插入了该键。请注意，在示例中，`startUpdateThread()` 接受一个 `final int i` 参数。将方法参数标记为 final 可以确保该方法无法更改 `i` 变量的值。如果 `i` 的值在方法内部发生变化，则从方法外部是不可见的。



### 工作原理

并发更新映射（Map）之所以困难，是因为它涉及两个操作：一种*检查-然后-执行*类型的操作。首先，必须检查映射中是否已存在某个条目。如果条目不存在，则可以将键和值放入映射中。另一方面，如果键已存在，则检索该键对应的值。为此，我们使用`ConcurrentMap`的`putIfAbsent`原子操作。这确保了要么键已存在，因此值不会被覆盖；要么键不存在，因此值被设置。对于`ConcurrentMap`的 JDK 实现，如果键没有对应的值，`putIfAbsent()`方法返回`null`；如果键有值，则返回当前值。通过断言`putIfAbsent()`方法返回`null`，你可以确信操作成功，并且映射中已创建了一个新条目。

在某些情况下，执行`putIfAbsent()`可能效率不高。例如，如果结果是一个大型数据库查询，每次都执行数据库查询然后调用`putIfAbsent()`效率会很低。在这种场景下，你可以先调用映射的`containsKey()`方法来确保键不存在。如果键不存在，再使用昂贵的数据库查询调用`putIfAbsent()`。虽然可能存在`putIfAbsent()`未能放入条目的情况，但这种检查减少了潜在昂贵值创建的次数。

请注意以下代码片段。

```
keyPresent = concurrentMap.containsKey(randomInt);
if (!keyPresent)  {
concurrentMap.putIfAbsent(randomInt, "Thread # " + i + " has made it!");
}
```

在这段代码中，第一个操作是检查键是否已存在于映射中。如果存在，则不执行`putIfAbsent()`操作。如果键不存在，则执行`putIfAbsent()`操作。

如果你从不同线程访问映射的值，应确保这些值是线程安全的。当使用集合作为值时，这一点最为明显，因为它们可能被不同线程访问。确保主映射是线程安全的可以防止对映射的并发修改。然而，一旦你获得了对映射值的访问权限，就必须对映射的值实施良好的并发实践。

## 10.4 遍历一个不断变化的集合

### 问题

你需要遍历集合中的每个元素。然而，其他线程正在不断地更新该集合。

### 解决方案 1

通过使用`CopyOnWriteArrayList<E>`，你可以安全地遍历集合，而无需担心并发问题。在以下解决方案中，`startUpdatingThread()`方法创建了一个新线程，该线程会主动修改传递给它的列表。当`startUpdatingThread()`修改列表时，同时使用`stream forEach()`函数对其进行并发遍历。

```
public class Recipe10_4 {
public static void main(String[] args) {
Recipe10_4 recipe = new Recipe10_4();
recipe.start();
}
private void start() {
System.out.println("Using CopyOnWrite");
copyOnWriteSolution();
}
private void stopUpdatingThread() {
updatingThread.interrupt();
}
Random random = new Random();
Thread updatingThread ;
private void startUpdatingThread(final List list) {
updatingThread = new Thread(() -> {
long counter =0;
while (!Thread.interrupted()) {
int size = list.size();
if (random.nextBoolean()) {
if (size > 1) {
list.remove(random.nextInt(size - 1));
}
} else {
if (size  list = new CopyOnWriteArrayList();
startUpdatingThread(list);
list.stream().forEach((element) -> {
System.out.println("Element :" + element);
});
stopUpdatingThread();
}
}
```

### 解决方案 2

使用`synchronizedList()`方法可以原子性地更改集合。它还提供了一种在遍历列表时（在 stream 中完成）安全同步列表的方法。以下是一个示例。

```
public class Recipe10_4 {
public static void main(String[] args) {
Recipe10_4 recipe = new Recipe10_4();
recipe.start();
}
private void start() {
System.out.println("Using SynchronizedList");
synchronizedListSolution();
}
private void stopUpdatingThread() {
updatingThread.interrupt();
}
Random random = new Random();
Thread updatingThread ;
private void startUpdatingThread(final List list) {
updatingThread = new Thread(() -> {
long counter =0;
while (!Thread.interrupted()) {
int size = list.size();
if (random.nextBoolean()) {
if (size > 1) {
list.remove(random.nextInt(size - 1));
}
} else {
if (size  list = Collections.synchronizedList(new ArrayList());
startUpdatingThread(list);
synchronized (list) {
list.stream().forEach((element) -> {
System.out.println("Element :" + element);
});
}
stopUpdatingThread();
}
}
```

以下是输出结果。

```
Using CopyOnWrite
Element :Random string 731774
Using SynchronizedList
Element :Random string 1100164
Element :Random string 1100177
Element :Random string 1100180
```

### 工作原理

Java 提供了许多并发集合选项。选择哪种集合取决于在你的应用程序上下文中，读取操作与写入操作的比较情况。如果写入操作相比读取操作发生得很少且间隔时间长，那么使用`CopyOnWriteArrayList<E>`实例效率最高，因为它不会*阻塞*（停止）其他线程读取列表，并且遍历它是线程安全的（遍历时不会抛出`ConcurrentModificationException`）。如果写入和读取的次数相同，那么`Collections`类中的`synchronizedList`方法是首选。

在解决方案 1 中，`CopyOnWriteArrayList<E>`在你遍历列表时被更新。由于该方案使用了`CopyOnWriteArrayList`实例，因此在遍历集合时无需担心线程安全问题（如本方案中使用 stream 所做的那样）。值得注意的是，`CopyOnWriteArrayList<E>`在遍历时提供的是某个时间点的快照。如果你在遍历列表时另一个线程修改了它，那么在遍历过程中很可能看不到修改后的列表。

注意

正确的锁定取决于所使用的集合类型。任何通过使用`Collections.synchronized`返回的集合都可以通过集合本身进行锁定（`synchronized (collectionInstance)`）。然而，一些更高效（更新）的并发集合，如`ConcurrentMap`，不能以这种方式使用，因为它们的内部实现不会在对象本身上锁定。

解决方案 2 创建了一个同步列表，该列表是通过使用`Collections`辅助类创建的。`Collection.synchronizedList()`方法将一个`List`对象（可以是`ArrayList<E>`、`LinkedList<E>`或其他`List<E>`实现）包装成一个同步列表操作的列表。每次你需要遍历一个列表（无论是使用`stream`、`for`循环还是迭代器），都必须注意该列表迭代器的并发影响。`CopyOnWriteArrayList<E>`是安全可遍历的（如 JavaDoc 中所述）。但是，`Collections`类中`synchronizedList`方法的迭代器必须手动同步（同样在 JavaDoc `Collections.synchronizedList`迭代器中指定）。在解决方案中，在`synchronized(list)`块内部可以安全地遍历列表。当在列表上同步时，在`synchronized(list)`块完成之前，不能进行任何读取/更新/其他迭代操作。

## 10.5 协调不同的集合

### 问题

你需要同时修改不同但相关的集合，并且希望确保在这些修改完成之前，其他线程无法看到这些修改。



### 解决方案 1

通过对主集合进行同步，可以确保集合能够被同时更新。在以下示例中，`fulfillOrder` 方法需要检查待处理订单的库存。如果库存充足，它需要将订单添加到 `customerOrders` 列表中。`fulfillOrder()` 方法在 `inventoryMap` 映射上进行同步，并在同步块结束前同时修改 `inventoryMap` 和 `customerOrders` 列表。

```
Set orderingThreads = new HashSet();
public static void main(String[] args) throws InterruptedException {
Recipe10_5_1 recipe = new Recipe10_5_1();
recipe.start();
}
final Map inventoryMap = new LinkedHashMap();
List customerOrders = new ArrayList();
Random random = new Random();
private void start() throws InterruptedException {
loadLoggingConfiguration();
// 让我们用物品填充库存。
// 最多有 20 本书
for (int i =0;i  {
thread.interrupt();
});
Thread.sleep(1000);
// 再次检查库存水平
checkInventoryLevels();
// 打印订单。
displayOrders();
}
Logger logger = Logger.getLogger("recipeLogger");
private void createOrderingThread() {
Thread orderingThread = new Thread(() -> {
while (!Thread.interrupted()) {
createRandomOrder();
}
});
orderingThread.start();
orderingThreads.add(orderingThread);
}
private void createRandomOrder() {
String itemOrdered = "Apress Book #"+random.nextInt(100);
int quantityOrdered = random.nextInt(2)+1;
String customerName = "Customer :"+UUID.randomUUID().toString();
fulfillOrder(itemOrdered, quantityOrdered, customerName);
}
class CustomerOrder {
String itemOrdered;
int quantityOrdered;
String customerName;
CustomerOrder(String itemOrdered, int quantityOrdered, String customerName) {
this.itemOrdered = itemOrdered;
this.quantityOrdered = quantityOrdered;
this.customerName = customerName;
}
public String getItemOrdered() {
return itemOrdered;
}
public int getQuantityOrdered() {
return quantityOrdered;
}
public String getCustomerName() {
return customerName;
}
}
private boolean fulfillOrder(String itemOrdered, int quantityOrdered, String customerName) {
synchronized (inventoryMap) {
int currentInventory  = 0;
if (inventoryMap != null) {
currentInventory = inventoryMap.get(itemOrdered);
}
if (currentInventory  {
System.out.println("Inventory Level :"+inventoryEntry.getKey()+" "+inventoryEntry.getValue());
});
System.out.println("------------------------------------");
}
}
private void displayOrders() {
synchronized (inventoryMap) {
customerOrders.stream().forEach((order) -> {
System.out.println(order.getQuantityOrdered()+" "+order.getItemOrdered()+" for "+order.getCustomerName());
});
}
}
输出类似于：

Inventory Level :Apress Book #0 100
Inventory Level :Apress Book #1 100
Inventory Level :Apress Book #2 100
Inventory Level :Apress Book #3 100
Inventory Level :Apress Book #4 100
Inventory Level :Apress Book #5 100
Inventory Level :Apress Book #6 100
Inventory Level :Apress Book #7 100
Inventory Level :Apress Book #8 100
Inventory Level :Apress Book #9 100
Inventory Level :Apress Book #10 100
Inventory Level :Apress Book #11 100
Inventory Level :Apress Book #12 100
Inventory Level :Apress Book #13 100
Inventory Level :Apress Book #14 100
Inventory Level :Apress Book #15 100
...

Order fulfilled for Customer :70a697d1-08d2-4ad7-b00c-17558930e0ab of Apress Book #64 (2)
Order fulfilled for Customer :4db86e58-a895-4992-9090-84a52f9314f6 of Apress Book #44 (2)
Order fulfilled for Customer :cc278024-0630-424f-99bb-091d4bb73d25 of Apress Book #20 (1)
Order fulfilled for Customer :f43f9edd-17f3-403c-b3b6-b91521b9f970 of Apress Book #24 (1)
Order fulfilled for Customer :a1c94246-3671-4373-bbbb-968d32fca100 of Apress Book #53 (2)
Order fulfilled for Customer :110382cf-79ff-4042-9ad4-bb4316764281 of Apress Book #31 (2)
Order fulfilled for Customer :e3255a74-dfdc-4ef4-b388-02745249c0a9 of Apress Book #38 (1)
Order fulfilled for Customer :fd2fa1fd-b9b8-4bca-92a8-6023a1fbde3e of Apress Book #40 (2)
Order fulfilled for Customer :5cead5b3-70bf-4f98-82c4-5ee34e90f02d of Apress Book #53 (2)
Order fulfilled for Customer :c0e0b2d3-4d58-41ef-9127-8e3d5665d976 of Apress Book #38 (1)
Order fulfilled for Customer :38f5ea73-d22b-4714-a319-7599d30a1b9a of Apress Book #86 (1)
...
```

### 解决方案 2

使用可重入锁，可以防止多个线程同时访问代码的同一临界区。在此解决方案中，通过调用 `inventoryLock.lock()` 来获取 `inventoryLock`。任何其他试图获取 `inventoryLock` 锁的线程都必须等待，直到该锁被释放。在 `fulfillOrder()` 方法的末尾（在 `finally` 块中），通过调用 `inventoryLock.unlock()` 方法来释放 `inventoryLock`。

```
Lock inventoryLock = new ReentrantLock();
private boolean fulfillOrder(String itemOrdered, int quantityOrdered, String customerName) {
try {
inventoryLock.lock();
int currentInventory = inventoryMap.get(itemOrdered);
if (currentInventory  {
System.out.println("Inventory Level :" + inventoryEntry.getKey() + " " + inventoryEntry.getValue());
});
System.out.println("------------------------------------");
} finally {
inventoryLock.unlock();
}
}
private void displayOrders() {
try {
inventoryLock.lock();
customerOrders.stream().forEach((order) -> {
System.out.println(order.getQuantityOrdered() + " " +
order.getItemOrdered() + " for " + order.getCustomerName());
});
} finally {
inventoryLock.unlock();
}
}
```



### 工作原理

如果你有多个结构需要同时修改，就必须确保这些结构以原子方式更新。*原子*操作指的是一组要么全部执行、要么全部不执行的指令。原子操作只有在完成后才对程序的其他部分可见。

在方案 1（原子地同时修改`inventoryMap`映射和`customerOrders`列表）中，你需要选择一个“主”集合来加锁（`inventoryMap`）。对主集合加锁可以保证：如果另一个线程试图对同一个主集合加锁，它必须等待当前执行线程释放该集合上的锁。

方案 2 则更加明确，它提供了一个独立的锁来协调原子操作，而不是选择一个主集合。*加锁*指的是 JVM 限制某些代码路径只能被一个线程执行的能力。线程会尝试获取锁（例如，示例中通过`ReentrantLock`实例提供锁），而锁一次只能分配给一个线程。如果其他线程试图获取同一个锁，它们会被挂起（`WAIT`），直到锁可用。当当前持有锁的线程释放锁时，该锁才变为可用。锁被释放后，可以被一个（且仅一个）等待该锁的线程获取。

锁默认不是“公平”的。换句话说，请求锁的线程顺序不会被保留；这使得 JVM 能够实现非常快速的加锁/解锁操作，在大多数情况下，使用非公平锁通常是可以接受的。在竞争非常激烈的锁上，如果有均匀分配锁（使其公平）的需求，可以通过设置锁的`setFair`属性来实现。

在方案 2 中，调用`inventoryLock.lock()`方法，要么获取锁并继续执行，要么挂起执行（`WAIT`）直到锁可以被获取。一旦获取了锁，其他线程就无法在锁定的代码块内执行。在代码块结束时，通过调用`inventoryLock.unlock()`释放锁。

在使用`Lock`对象（`ReentrantLock`、`ReadLock`和`WriteLock`）时，通常的做法是将这些`Lock`对象的使用包裹在`try/finally`子句中。在打开`try`块之后，第一条指令应该是调用`lock.lock()`方法。这保证了执行的第一条指令就是获取锁。释放锁（通过调用`lock.unlock()`）则在对应的`finally`块中完成。如果在持有锁时发生了`RuntimeException`，在`finally`子句中解锁可以确保不会“一直持有”锁，从而阻止其他线程获取它。

使用`ReentrantLock`对象提供了`synchronized`语句所不具备的额外功能。例如，`ReentrantLock`有`tryLock()`函数，它仅在没有任何其他线程持有锁时尝试获取锁（该方法不会使调用线程等待）。如果另一个线程持有锁，该方法返回`false`，但会继续执行。最好使用`synchronized`关键字进行同步，并且只在需要其特性时才使用`ReentrantLock`。

提示

虽然这只是一本食谱书，且正确的线程技术本身就有很多内容，但提高对死锁的认识非常重要。*死锁*发生在涉及两个锁时（在另一个线程中以相反的顺序获取）。避免死锁最简单的方法是不要让锁“逃逸”。这意味着，当锁被获取时，不应执行可能调用其他方法（这些方法可能获取另一个锁）的代码。如果无法做到这一点，则在调用此类方法之前释放锁。

请注意：任何涉及一个或两个集合的操作都需要受到同一个锁的保护。依赖于一个集合的结果来查询第二个集合的操作需要原子地执行；它们必须作为一个单元完成，在该单元中，直到操作完成，两个集合都不能被更改。

## 10.6 将工作拆分为独立线程

### 问题

你有可以拆分为独立线程的工作，并且希望最大化利用可用的 CPU 资源。

### 解决方案

使用`ThreadpoolExecutor`实例，它允许你将任务分解为离散的单元。在下面的示例中，创建了一个`BlockingQueue<E>`接口，其中包含一个`Runnable`对象。然后将其传递给`ThreadPoolExecutor`实例，该实例通过调用`prestartAllCoreThreads()`方法进行初始化和启动。接下来，执行有序关闭，通过调用`shutdown()`方法执行所有先前提交的任务，然后调用`awaitTermination()`方法。

```
private void start() throws InterruptedException {
BlockingQueue queue = new LinkedBlockingQueue();
for (int i =0;i  {
doExpensiveOperation(localI);
});
}
ThreadPoolExecutor executor = new ThreadPoolExecutor(10,10,1000,
TimeUnit.MILLISECONDS, queue);
executor.prestartAllCoreThreads();
executor.shutdown();
executor.awaitTermination(100000,TimeUnit.SECONDS);
System.out.println("看啊！所有操作都已完成");
}
public static void main (String [] args) throws InterruptedException {
Recipe10_6 recipe = new Recipe10_6();
recipe.start();
}
private void doExpensiveOperation(int index) {
System.out.println("开始执行耗时操作 "+index);
try {
Thread.sleep(index * 1000);
} catch (InterruptedException e) {
e.printStackTrace();
}
System.out.println("结束执行耗时操作 " + index);
}
```

以下是输出结果。

```
开始执行耗时操作 1
开始执行耗时操作 3
开始执行耗时操作 2
开始执行耗时操作 0
开始执行耗时操作 4
结束执行耗时操作 0
开始执行耗时操作 6
开始执行耗时操作 5
开始执行耗时操作 7
开始执行耗时操作 8
开始执行耗时操作 9
结束执行耗时操作 1
结束执行耗时操作 2
结束执行耗时操作 3
结束执行耗时操作 4
结束执行耗时操作 5
结束执行耗时操作 6
结束执行耗时操作 7
结束执行耗时操作 8
结束执行耗时操作 9
看啊！所有操作都已完成
```



### 工作原理

`ThreadPoolExecutor` 由两个组件构成：`Queue<E>`（待执行的任务队列）和 `Executor`（定义任务执行方式）。`Queue<E>` 中填充了 `Runnable` 对象，其 `run()` 方法包含了待执行的代码。

`ThreadPoolExecutor` 使用的 `Queue<E>` 组件实现了 `BlockingQueue<E>` 接口，该接口表示一种队列：当 `Queue<E>` 中没有元素时，队列的消费者会等待（被挂起）。这对于 `ThreadPoolExecutor` 的高效运行是必要的。

第一步是将需要并行执行的任务填充到 `Queue<E>` 中。这通过调用 `Queue<E>` 的 `add()` 方法并传入一个实现了 `Runnable` 接口的类来完成。完成后，执行器（executor）即被初始化。

`ThreadPoolExecutor` 初始化后，调用 `prestartAllCoreThreads()` 方法，该方法会创建 `CorePoolSize` 指定数量的线程来“预热” `ThreadPoolExecutor`，并在队列非空时主动开始从 `Queue<E>` 中消费任务。

调用 `ThreadPoolExecutor` 的 `shutdown()` 方法以等待所有任务完成。通过调用此方法，`ThreadPoolExecutor` 被指示不再接受队列中的新事件（之前提交的事件会继续处理完毕）。这是 `ThreadPoolExecutor` 有序终止的第一步。调用 `awaitTermination()` 方法以等待 `ThreadPoolExecutor` 中的所有任务完成。该方法会强制主线程等待，直到 `ThreadPoolExecutor` 队列中的所有 `Runnable` 对象执行完毕。所有 `Runnable` 对象执行完成后，主线程被唤醒并继续执行。

## 10.7 协调线程

### 问题

你的应用程序需要协调两个或更多线程，使其同步工作。

### 解决方案 1

通过使用 wait/notify 进行线程同步，可以协调线程。在此方案中，主线程等待 `objectToSync` 对象，直到数据库加载线程执行完毕。数据库加载线程完成后，它会通知 `objectToSync`，告知所有等待该对象的线程可以继续执行。将订单加载到系统中时也采用相同流程。主线程等待 `objectToSync`，直到订单加载线程通过调用 `objectToSync.notify()` 方法通知 `objectToSync` 继续执行。确保库存和订单都加载完成后，主线程执行 `processOrder()` 方法来处理所有订单。

```
public static void main(String[] args) {
Recipe10_7_1 recipe = new Recipe10_7_1();
recipe.start();
}
private final Object objectToSync = new Object();
private void start() {
loadItems();
Thread inventoryThread = new Thread(() -> {
System.out.println("Loading Inventory from Database...");
loadInventory();
synchronized (objectToSync) {
objectToSync.notify();
}
});
synchronized (objectToSync) {
inventoryThread.start();
try {
objectToSync.wait();
} catch (InterruptedException e) {
e.printStackTrace();
}
}
Thread ordersThread = new Thread(() -> {
System.out.println("Loading Orders from XML Web service...");
loadOrders();
synchronized (objectToSync) {
objectToSync.notify();
}
});
synchronized (objectToSync) {
ordersThread.start();
try {
objectToSync.wait();
} catch (InterruptedException e) {
e.printStackTrace();
}
}
processOrders();
}
private void processOrders() {
orderList.stream().forEach((order) -> {
boolean fulfillable = canFulfill(order);
if (fulfillable) {
doFulfill (order);
System.out.println("Order # " + String.valueOf(order.getOrderId()) + " has been fulfilled");
} else {
System.out.println("Order # "+String.valueOf(order.getOrderId())+" CANNOT be fulfilled");
}
});
}
```

输出如下：

```
Loading Inventory from Database...
Loading Orders from XML Web service...
Order # 0 has been fulfilled
Order # 1 has been fulfilled
Order # 2 has been fulfilled
Order # 3 has been fulfilled
Order # 4 has been fulfilled
Order # 5 CANNOT be fulfilled
Order # 6 has been fulfilled
Order # 7 has been fulfilled
Order # 8 has been fulfilled
Order # 9 has been fulfilled
Order # 10 has been fulfilled
Order # 11 has been fulfilled
Order # 12 has been fulfilled
Order # 13 CANNOT be fulfilled
Order # 14 has been fulfilled
Order # 15 CANNOT be fulfilled
Order # 16 CANNOT be fulfilled
Order # 17 has been fulfilled
Order # 18 has been fulfilled
Order # 19 has been fulfilled
Order # 20 has been fulfilled
Order # 21 has been fulfilled
Order # 22 has been fulfilled
...
```

### 解决方案 2

你可以使用 `CountDownLatch` 对象来控制主线程何时继续执行。在以下代码中，创建了一个初始值为 2 的 `CountDownLatch`；然后创建并启动两个线程，分别用于加载库存和加载订单信息。当这两个线程各自执行完毕后，它们会调用 `CountDownLatch` 的 `countDown()` 方法，该方法将锁存器的值减一。主线程会一直等待，直到 `CountDownLatch` 的值变为 0，此时主线程恢复执行。

```
public static void main(String[] args) {
Recipe10_7_2 recipe = new Recipe10_7_2();
recipe.start();
}
CountDownLatch latch = new CountDownLatch(2);
private void start() {
loadItems();
Thread inventoryThread = new Thread(() -> {
System.out.println("Loading Inventory from Database...");
loadInventory();
latch.countDown();
});
inventoryThread.start();
Thread ordersThread = new Thread(() -> {
System.out.println("Loading Orders from XML Web service...");
loadOrders();
latch.countDown();
});
ordersThread.start();
try {
latch.await();
} catch (InterruptedException e) {
e.printStackTrace();
}
processOrders();
}
```

输出如下：

```
Loading Inventory from Database...
Loading Orders from XML Web service...
Order # 0 has been fulfilled
Order # 1 has been fulfilled
Order # 2 has been fulfilled
Order # 3 has been fulfilled
Order # 4 has been fulfilled
Order # 5 has been fulfilled
Order # 6 has been fulfilled
Order # 7 has been fulfilled
Order # 8 has been fulfilled
Order # 9 has been fulfilled
Order # 10 has been fulfilled
Order # 11 has been fulfilled
Order # 12 has been fulfilled
Order # 13 has been fulfilled
...
```

### 解决方案 3

`Thread.join()` 允许你等待一个线程执行完毕。以下示例中有一个用于加载库存的线程和另一个用于加载订单的线程。每个线程启动后，调用 `inventoryThread.join()` 会使主线程等待 `inventoryThread` 执行完毕后再继续。

```
public static void main(String[] args) {
Recipe10_7_3 recipe = new Recipe10_7_3();
recipe.start();
}
private void start() {
loadItems();
Thread inventoryThread = new Thread(() -> {
System.out.println("Loading Inventory from Database...");
loadInventory();
});
inventoryThread.start();
try {
inventoryThread.join();
} catch (InterruptedException e) {
e.printStackTrace();
}
Thread ordersThread = new Thread(() -> {
System.out.println("Loading Orders from XML Web service...");
loadOrders();
});
ordersThread.start();
try {
ordersThread.join();
} catch (InterruptedException e) {
e.printStackTrace();
}
processOrders();
}
```

输出如下：

```
Loading Inventory from Database...
Loading Orders from XML Web service...
Order # 0 has been fulfilled
Order # 1 has been fulfilled
Order # 2 has been fulfilled
Order # 3 has been fulfilled
Order # 4 has been fulfilled
Order # 5 has been fulfilled
Order # 6 has been fulfilled
Order # 7 has been fulfilled
Order # 8 has been fulfilled
Order # 9 has been fulfilled
Order # 10 has been fulfilled
Order # 11 has been fulfilled
Order # 12 has been fulfilled
Order # 13 has been fulfilled
...
```



### 工作原理

在 Java 中，协调线程的方式有很多种，而这些协调工作都依赖于让线程等待。当线程等待时，它会暂停执行（不会继续执行下一条指令，并从 JVM 的线程调度器中移除）。如果线程正在等待，可以通过通知来唤醒它。在 Java 的并发术语中，*通知*一词意味着线程恢复执行（JVM 将该线程重新加入线程调度器）。因此，在线程协调的自然流程中，最常见的事件序列是主线程等待，然后辅助线程通知主线程继续（或唤醒）。即便如此，等待中的线程仍有可能被其他事件中断。当线程被中断时，它不会继续执行下一条指令，而是抛出 `InterruptedException`，这表示即使线程正在等待某事发生，但发生了其他需要线程关注的事件。下面的例子能更好地说明这一点。

```
BlockingQueue queue = new LinkedBlockingQueue();
while (true) {
synchronized (this) {
Object itemToProcess = queue.take();
processItem (itemToProcess);
}
}
```

如果你查看上述代码，执行它的线程将永远不会终止，因为它会无限循环并等待处理某个项目。如果 `Queue<E>` 中没有项目，主线程会一直等待，直到另一个线程向 `Queue<E>` 中添加了内容。你无法优雅地关闭上述代码（特别是当运行该循环的线程不是守护线程时）。

```
BlockingQueue queue = new LinkedBlockingQueue();
while (true) {
synchronized (this) {
Object itemToProcess = null;
try {
itemToProcess = queue.take();
} catch (InterruptedException e) {
return;
}
processItem (itemToProcess);
}
}
```

新的代码现在可以“跳出”无限循环。你可以从另一个线程调用 `thread.interrupt()`，这会抛出 `InterruptedException`，然后被主线程的 `catch` 子句捕获。在这个子句中，可以退出无限循环。

`InterruptedExceptions` 是一种向等待（或睡眠）中的线程发送额外信息的方式，以便它们能够处理不同的场景（例如，有序的程序关闭）。因此，每个将线程状态更改为睡眠/等待的操作都必须被一个能够捕获 `InterruptedException` 的 `try/catch` 块包围。这是 `InterruptedException` 异常并非错误，而更像是一种在线程之间传递信号、表明发生了需要关注的事情的情况之一。

解决方案 1 展示了最常见（最古老）的协调形式。该方案需要让一个线程等待并暂停执行，直到该线程被另一个线程通知（或唤醒）。

要使解决方案 1 生效，发起线程需要获取一个锁。这个锁随后将成为另一个线程可以用来通知发起线程唤醒的“电话号码”。发起线程获取锁（电话号码）后，便开始等待。一旦调用了 `wait()` 方法，锁就会被释放，从而允许其他线程获取同一个锁。然后，辅助线程获取该锁（电话号码）并通知（这就像拨打唤醒电话）发起线程。通知之后，发起线程恢复执行。

在解决方案 1 的代码中，锁是一个被标识为 `objectToSync` 的虚拟对象。在实践中，用于等待和通知的锁对象可以是 Java 中任何有效的实例对象；例如，我们可以使用 `this` 引用来让主线程等待。

使用这种技术的主要优势在于，可以明确地控制等待谁以及何时通知（并且能够通知所有等待在同一对象上的线程；请参见下面的提示）。

提示

多个线程可以等待同一个锁（同一个被唤醒的电话号码）。当辅助线程调用 `notify()` 方法时，它会唤醒其中一个“等待”线程（不保证哪个线程被唤醒）。有时你需要通知所有线程；可以调用 `notifyAll()` 方法代替 `notify()` 方法。这通常用于准备让多个线程执行某些工作，但工作尚未完成设置的情况。

解决方案 2 采用了一种更现代的通知方法，因为它涉及 `CountDownLatch`。在设置时，指定锁存器将拥有的“计数”数量。然后，主线程通过调用 `CountDownLatch` 的 `await()` 方法来等待（停止执行），直到锁存器计数到 0。当锁存器达到 0 时，主线程被唤醒并继续执行。当工作线程完成时，调用 `latch.countdown()` 方法，该方法会递减锁存器的当前计数值。如果锁存器的当前值达到 0，等待在 `CountDownLatch` 上的主线程就会被唤醒并继续执行。

使用 `CountDownLatches` 的主要优势在于，可以同时生成多个任务并等待它们全部完成。（在解决方案示例中，我们不需要等待其中一个或另一个线程完成后再继续；它们都被启动了，当锁存器为 0 时，主线程继续执行。）

解决方案 3 允许你访问想要等待的线程。对于主线程来说，只需调用辅助线程的 `join()` 方法即可。然后主线程等待（停止执行），直到辅助线程完成。

这种方法的优势在于，它不需要辅助线程了解任何同步机制。只要辅助线程终止执行，主线程就可以等待它们。

## 10.8 创建线程安全对象

### 问题

你需要创建一个从多个线程访问的线程安全对象。

### 解决方案 1

使用同步的 getter 和 setter，并保护改变状态的临界区域。在下面的示例中，创建了一个对象，其每个内部变量都有同步的 getter 和 setter。临界区域通过使用 `synchronized(this)` 锁来保护。

```
public class CustomerOrder {
private String itemOrdered;
private int quantityOrdered;
private String customerName;
public CustomerOrder() {
}
public double calculateOrderTotal (double price) {
synchronized (this) {
return getQuantityOrdered()*price;
}
}
public synchronized String getItemOrdered() {
return itemOrdered;
}
public synchronized int getQuantityOrdered() {
return quantityOrdered;
}
public synchronized String getCustomerName() {
return customerName;
}
public synchronized void setItemOrdered(String itemOrdered) {
this.itemOrdered = itemOrdered;
}
public synchronized void setQuantityOrdered(int quantityOrdered) {
this.quantityOrdered = quantityOrdered;
}
public synchronized void setCustomerName(String customerName) {
this.customerName = customerName;
}
}
```

### 解决方案 2

创建一个不可变对象（一个一旦创建，其内部状态就不会改变的对象）。在下面的代码中，对象的内部变量被声明为 final，并在构造时赋值。通过这样做，保证了对象是不可变的。

```
public class ImmutableCustomerOrder {
final private String itemOrdered;
final private int quantityOrdered;
final private String customerName;
ImmutableCustomerOrder(String itemOrdered, int quantityOrdered, String customerName) {
this.itemOrdered = itemOrdered;
this.quantityOrdered = quantityOrdered;
this.customerName = customerNam e;
}
public String getItemOrdered() {
return itemOrdered;
}
public int getQuantityOrdered() {
return quantityOrdered;
}
public String getCustomerName() {
return customerName;
}
public double calculateOrderTotal (double price) {
return getQuantityOrdered()*price;
}
}
```



### 工作原理

解决方案 1 依赖于锁保护对象任何变更的原则。使用 `synchronized` 关键字是编写 `synchronized (this)` 表达式的快捷方式。通过同步 getter 和 setter（以及任何其他改变对象内部状态的操作），可以确保对象的一致性。此外，任何应作为整体执行的操作（例如同时修改两个集合的操作）都必须在对象的方法内完成，并通过 `synchronized` 关键字进行保护。

例如，如果一个对象提供了 `getSize()` 方法和 `getItemNumber(int index)` 方法，那么编写以下代码 `object.getItemNumber (object.getSize()-1)` 是不安全的。尽管这条语句看起来简洁，但另一个线程可能在获取大小和获取元素编号之间修改对象的内容。相反，创建一个 `object.getLastElement()` 方法更为安全，该方法可以原子性地计算出大小和最后一个元素。

解决方案 2 依赖于不可变对象的特性。不可变对象不能改变其内部状态，而无法改变内部状态的对象（即*不可变*对象）是线程安全的。如果由于某个事件需要修改不可变对象，请创建一个具有更改后属性的新对象，而不是显式地更改其属性。然后，这个新对象将取代旧对象，在后续对该对象的请求中，将返回这个新的不可变对象。这是创建线程安全代码最简单（尽管可能冗长）的方法。

## 10.9 实现线程安全计数器

### 问题

你需要一个线程安全的计数器，以便能够在不同的执行线程中对其进行递增操作。

### 解决方案

使用天生线程安全的对象可以创建一个保证线程安全且具有优化同步策略的计数器。在以下代码中，创建了一个 `Order` 对象，它需要一个使用 `AtomicLong incrementAndGet()` 方法生成的唯一订单 ID。

```
AtomicLong orderIdGenerator = new AtomicLong(0);
for (int i =0;i  {
for (int i1 = 0; i1 < 10; i1++) {
createOrder(Thread.currentThread().getName());
}
});
orderCreationThread.setName("订单创建线程 "+i);
orderCreationThread.start();
}
private void createOrder(String name) {
long orderId = orderIdGenerator.incrementAndGet();
Order order = new Order(name, orderId);
orders.add(order);
}
public  class Order {
String orderName;
long orderId;
Order(String orderName, long orderId) {
this.orderName = orderName;
this.orderId = orderId;
}
public String getOrderName() {
return orderName;
}
public long getOrderId() {
return orderId;
}
}
```

以下是输出结果。

```
订单 id:9
订单 id:2
订单 id:8
订单 id:11
订单 id:12
订单 id:13
订单 id:14
订单 id:15
订单 id:16
订单 id:18
订单 id:19
订单 id:21
订单 id:22
订单 id:7
订单 id:23
订单 id:24
...
```

### 工作原理

`AtomicLong`（及其同类 `AtomicInteger`）被设计用于在并发环境中安全使用。它们提供了原子性地递增（并获取）更改后值的方法。即使有成百上千个线程调用 `AtomicLong increment()` 方法，返回的值也是唯一的。

如果你需要做出决策并更新变量，请始终使用 `AtomicLong` 提供的原子操作；例如 `compareAndSet`。否则，你的代码就不是线程安全的（因为任何检查后执行的操作都需要是原子性的），除非你使用自己的锁从外部保护该原子引用。

更改一个 long 值可能通过两次内存写操作完成（Java 内存模型允许这样做）。因此，两个线程可能会在看似线程安全的代码中重叠执行这两次操作。结果将得到一个完全出乎意料（并且很可能是错误）的 long 值。

```
long counter = 0;
public long incrementCounter() {
return counter++;
}
```

尽管看起来只有一次递增计数器的操作，但实际上，在机器语言层面发生了两次操作（先读取变量，然后递增）。两个或多个线程可能获得相同的值，因为它们都读取了变量但尚未递增它。然后所有线程都将计数器递增到相同的数值。

## 10.10 跨多个线程更新公共值

### 问题

你的应用程序需要安全地维护一个跨多个线程的单一求和值。

### 解决方案

利用 `DoubleAdder` 或 `LongAdder` 来保存跨多个线程求和的值，以确保安全处理。在以下示例中，两个线程同时向 `DoubleAdder` 添加值。最后，对值进行求和并显示。

```
public static void main(String[] args) {
Recipe10_10 recipe10_10 = new Recipe10_10();
recipe10_10.start();
}
DoubleAdder da = new DoubleAdder();
private void start() {
Thread thread1 = new Thread(() -> {
for (int i1 = 0; i1  {
for (int i1 = 0; i1 < 10; i1++) {
da.add(i1);
System.out.println("添加 " + i1);
}
});
thread1.start();
thread2.start();
try {
System.out.println("求和时休眠....");
Thread.sleep(10000);
} catch (InterruptedException e) {
e.printStackTrace();
}
System.out.println("总和是: " + da.doubleValue());
}
}
```

以下是结果。

```
添加 0
添加 1
添加 2
添加 3
添加 4
添加 5
添加 6
添加 7
添加 0
添加 8
添加 9
添加 1
添加 2
添加 3
添加 4
添加 5
添加 6
添加 7
添加 8
添加 9
总和是: 90.0
```

### 工作原理

在 Java 8 之前，跨多个线程处理值时，使用原子数字非常重要。原子变量可以防止线程干扰，而不会像同步访问在某些情况下可能造成阻塞那样。Java 8 引入了一系列新的原子变量，它们比标准原子变量提供更高的吞吐量。在大多数情况下，当值可能被跨多个线程访问和更新时，`java.util.concurrent.atomic.DoubleAdder` 和 `java.util.concurrent.atomic.LongAdder` 类优于 `AtomicDouble` 和 `AtomicLong`。`DoubleAdder` 和 `LongAdder` 都扩展了 Number 类，它们在跨线程求和时非常有用，尤其是在高竞争情况下。

在解决方案中，`DoubleAdder` 对来自两个不同线程的数字进行求和。使用 `add()` 方法，将各种数字“添加”到 `DoubleAdder` 的值中。在线程有足够时间完成其工作后，调用 `doubleValue()` 方法以 double 形式返回所有值的总和。

`DoubleAdder` 和 `LongAdder` 类都包含类似的方法，尽管 `LongAdder` 确实包含一些额外的辅助方法，用于递增和递减 `adder` 的值。

## 10.11 异步执行多个任务

### 问题

你的应用程序需要以异步方式同时执行多个任务，使得这些任务之间互不阻塞。



### 解决方案

利用 `CompletableFuture<T>` 对象来表示当前正在执行的每个任务的状态。每个 `CompletableFuture` 对象在指定或由应用程序决定的后台线程上运行，完成后向原始调用方法发出回调。

在以下解决方案中，调用方法调用了两个长时间运行的任务，它们各自利用 `CompletableFuture` 在任务完成后报告状态。

```
public class Recipe10_11 {
public static void main(String[] args) {
try {
CompletableFuture tasks = performWork()
.thenApply(work -> {
String newTask = work + " 第二个任务完成！";
System.out.println(newTask);
return newTask;
}).thenApply(finalTask -> finalTask + " 最终任务完成！");
CompletableFuture future = performSecondWork("Java 9 太棒了！ ");
while(!tasks.isDone()){
System.out.println(future.get());
}
System.out.println(tasks.get());
} catch (ExecutionException | InterruptedException ex) {
}
}
/**
* 返回一个 CompletableFuture 对象。
* @return
*/
public static CompletableFuture performWork() {
CompletableFuture resultingWork = CompletableFuture.supplyAsync(
() -> {
String taskMessage = "第一个任务完成！";
try {
Thread.sleep(1000);
} catch (InterruptedException ex) {
System.out.println(ex);
}
System.out.println(taskMessage);
return taskMessage;
});
return resultingWork;
}
/**
* 接受一个字符串并返回一个 CompletableFuture。
* @param message
* @return
*/
public static CompletableFuture performSecondWork(String message) {
CompletableFuture resultingWork = CompletableFuture.supplyAsync(
() -> {
String taskMessage = message + " 另一个任务完成！";
try {
Thread.sleep(1000);
} catch (InterruptedException ex) {
System.out.println(ex);
}
return taskMessage;
});
return resultingWork;
}
}
```

以下是运行结果。

```
第一个任务完成！
第一个任务完成！第二个任务完成！
Java 9 太棒了！另一个任务完成！
第一个任务完成！第二个任务完成！最终任务完成！
```

### 工作原理

`CompletableFuture<T>` 是在 Java 8 中新增的，用于支持异步任务。`CompletableFuture<T>` 是 `Future<T>` 的扩展，它增加了许多方法来促进异步、事件驱动的编程模型，并且允许随时设置值。后一项功能意味着，如果应用程序将来需要使用 `CompletableFuture` 对象，可以在需要之前创建它。

创建 `CompletableFuture` 对象有几种选择，可以手动创建，也可以通过工厂方法创建。手动创建可以不绑定到任何线程。当应用程序需要为将来发生的事件设置占位符时，这种策略非常有用。以下代码演示了如何手动创建 `CompletableFuture` 对象。

```
final CompletableFuture completableFuture = new CompletableFuture();
```

可以使用工厂生成一个 `CompletableFuture`，以返回一个面向特定任务或结果的对象。有几种不同的工厂方法可以调用来返回这样的对象。有些工厂方法接受参数，有些则不接受。例如，`CompletableFuture.runAsync(Runnable)` 方法返回一个 `CompletableFuture`，它首先执行提供的 `Runnable`，然后由在 `ForkJoinPool.commonPool()` 中运行的任务异步完成。`runAsync()` 方法的另一个变体同时接受 `Runnable` 和 `Executor`，它首先执行提供的 `Runnable`，然后由给定 `Executor` 中的任务异步完成。

`CompletableFuture` 对象还包含几个类似于标准 `Future<T>` 对象的方法。例如，`isDone()`、`cancel()` 和 `isCompletedExceptionally()` 方法都返回 `boolean` 以指示对象的状态。还可以通过调用 `thenApply()` 方法（该方法接受 lambda 表达式和方法引用）来使用 `CompletableFuture` 对象堆叠异步任务。本配方的解决方案演示了如何利用 `thenApply()` 方法从一个异步任务调用另一个异步任务。首先，执行名为 `performWork()` 的 `CompletableFuture` 对象，然后执行一个 lambda 表达式，基于 `performWork()` 中生成的字符串创建一个拼接字符串。第二个任务完成后，会调用另一个任务向字符串追加更多文本。然后在循环中调用 `future.get()` 方法，以查看字符串随时间被应用程序转换的过程。最后，打印完全完成的任务的结果。

## 10.12 总结

在开发应用程序时，理解并发的基础知识非常重要。没有什么比成功测试应用程序，然后一旦发布到生产环境就因死锁而失败更糟糕的了。本章从基础开始，演示了如何生成后台任务。

