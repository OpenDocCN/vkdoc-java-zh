# 12. 使用数据库

几乎所有重要的应用程序都包含某种数据库。有些应用程序使用内存数据库，而另一些则使用传统的关系型数据库管理系统（RDBMS）。无论哪种情况，每个 Java 开发人员都必须具备一些使用数据库的技能。多年来，Java 数据库连接（JDBC）API 已经发生了很大的变化，并且在过去的几个版本中，出现了一些重大的进步。

本章介绍使用 JDBC 进行数据库操作的基础知识。你将学习如何执行所有标准数据库操作以及一些用于操作数据的高级技术。

## 12.1 安装 MySQL

### 问题

你想在个人电脑上安装一个数据库。

### 解决方案

安装 MySQL。选择手动安装，因为它提供了一些好处和更多的控制权。你可以在几秒钟内备份或移动数据库，并且可以在任何地方（包括 U 盘）安装和重新安装 MySQL。

要安装 MySQL，请执行以下步骤。

1.  访问 [`https://dev.mysql.com/downloads/mysql/`](https://dev.mysql.com/downloads/mysql/)（MySQL 社区服务器网页）并同意隐私政策。

2.  确保所选平台是你的操作系统（我们使用的是 Windows 操作系统），然后点击下载按钮。对于 Windows，在“其他下载”部分下选择不带安装程序的版本——Windows (x86, 64-bit), ZIP Archive。

3.  以用户身份登录，或注册，或者，如果你愿意，可以跳过此步骤。

4.  下载不带安装程序的版本。（我们下载时的最新版本是 `mysql-8.0.27-winx64.zip`；大小为 209 MB。）此软件包包含 MySQL 数据库服务器。

5.  将 zip 文件解压到你的根文件夹驱动器。将文件夹名称从 `mysql-8.0.27-winx64` 重命名为 `mysql`。

6.  创建“data”文件夹。我选择在同一个 `mysql` 文件夹中，尽管我们建议始终将数据文件夹放在另一个驱动器上。这可以防止数据丢失，并允许轻松重新安装。

7.  在 MySQL 可以启动之前，手动初始化数据目录。打开一个命令窗口，并在 bin 文件夹下输入以下内容。

```
C:\mysql\bin>mysqld --console --initialize
```

`--console` 选项允许将诊断输出写入控制台（如果你省略此选项，服务器会将输出写入数据文件夹中的错误日志）。

清单 12-1 是控制台上显示的结果。

```
2021-10-31T19:30:01.391296Z 0 [System] [MY-013169] [Server] C:\mysql\bin\mysqld.exe (mysqld 8.0.27) initializing of server in progress as process 3816
2021-10-31T19:30:01.599643Z 1 [System] [MY-013576] [InnoDB] InnoDB initialization has started.
2021-10-31T19:30:19.281112Z 1 [System] [MY-013577] [InnoDB] InnoDB initialization has ended.
2021-10-31T19:30:49.726138Z 6 [Note] [MY-010454] [Server] A temporary password is generated for root@localhost: )r%Crfs1reOS
清单 12-1
数据库初始化日志
```

以粗体突出显示的字符串是为 root 用户（MySQL 授权系统中的默认管理帐户）生成的密码，该密码在初始化期间显示在流中。现在，你可以开始使用你的数据库了。

打开两个控制台窗口。在第一个窗口中，使用以下命令启动数据库。

```
C:\mysql\bin>mysqld.exe --console
```

清单 12-2 显示了服务器的启动过程。`--console` 命令让你可以看到正在打印的启动日志。

```
C:\mysql\bin>mysqld.exe --console
2021-10-31T19:39:25.558678Z 0 [System] [MY-010116] [Server] C:\mysql\bin\mysqld.exe (mysqld 8.0.27) starting as process 5500
2021-10-31T19:39:25.597447Z 1 [System] [MY-013576] [InnoDB] InnoDB initialization has started.
2021-10-31T19:39:29.188084Z 1 [System] [MY-013577] [InnoDB] InnoDB initialization has ended.
2021-10-31T19:39:31.625112Z 0 [Warning] [MY-010068] [Server] CA certificate ca.pem is self signed.
2021-10-31T19:39:31.842513Z 0 [System] [MY-011323] [Server] X Plugin ready for connections. Bind-address: '::' port: 33060
2021-10-31T19:39:31.842945Z 0 [System] [MY-010931] [Server] C:\mysql\bin\mysqld.exe: ready for connections. Version: '8.0.27'  socket: ''  port: 3306  MySQL Community Server - GPL.
清单 12-2
数据库启动日志
```

在第二个控制台窗口中，使用用户名和密码连接到数据库，并使用以下命令启动 MySQL 控制台。

```
C:\mysql\bin>mysql -u root -p
```

在提示时输入密码后（Enter password: ************），你会在控制台上看到欢迎消息，如清单 12-3 所示。

```
C:\mysql\bin>mysql -u root -p
Enter password: ************
Enter password: ************
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 8
Server version: 8.0.27
Copyright (c) 2000, 2021, Oracle and/or its affiliates.
Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.
Type 'help;' or '\h' for help. Type '\c' to clear the current input statement. mysql>
清单 12-3
数据库连接日志
```

现在*命令行界面*（CLI）已激活，请注意突出显示的提示命令的变化。

我们建议使用以下命令更改密码。

```
mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY 'root';
```

以粗体突出显示的字符串是新密码。

然后，运行以下命令。

```
mysql> FLUSH PRIVILEGES;
```

它告诉服务器重新加载授权表，从而使你的新更改生效。

最后，如果你想停止数据库，请在提示符中输入 exit 来停止命令行客户端，然后输入以下内容。

```
C:\mysql\bin>mysqladmin.exe -u root -p shutdown
Enter password: ****
```

`mysqladmin` 命令调用管理实用程序，以 root 用户身份连接到 MySQL 服务器并告诉它关闭。

清单 12-4 是控制台上显示的消息。

```
2021-10-31T19:46:01.347489Z 8 [System] [MY-013172] [Server] Received SHUTDOWN from user root. Shutting down mysqld (Version: 8.0.27).
2021-10-31T19:46:01.347680Z 0 [System] [MY-013105] [Server] C:\mysql\bin\mysqld.exe: Normal shutdown.
2021-10-31T19:46:01.970912Z 0 [System] [MY-010910] [Server] C:\mysql\bin\mysqld.exe: Shutdown complete (mysqld 8.0.27)  MySQL Community Server - GPL.
清单 12-4
服务器关闭日志
```

现在你可以创建并填充你的数据库，并按照本章中的示例进行操作。运行 `ApressBookDatabase_create.sql` 脚本来创建数据库用户模式并填充它。

我们建议你使用我们在 MySQL 中创建的数据库，即使你能够开发适用于 Oracle 数据库、PostgreSQL 和所有传统 RDBMS 的 Java 应用程序。

首先，定义一个名为 `apressBooks` 的简单数据库，其中包含 recipes 和 publications 表。数据库的逻辑设计如表 12-1 和表 12-2 所示，其特征包括名称、长度、数据类型和约束（例如，一个字段是主键，另一个字段必须非空）。

表 12-2
Publications

| 字段名 | 长度 | 类型 | 约束 |
| --- | --- | --- | --- |
| id | - | INT | 主键非空 |
| book_title | 500 | VARCHAR | 非空 |
| publish_date | - | DATE | 默认为空 |
| publish_co | 100 | VARCHAR | 默认为空 |

表 12-1
Recipes

| 字段名 | 长度 | 类型 | 约束 |
| --- | --- | --- | --- |
| id | - | INT | 主键非空 |
| recipe_number | 10 | VARCHAR | 非空 |
| recipe_name | 100 | VARCHAR | 非空 |
| description | 500 | VARCHAR | 默认为空 |

清单 12-5 显示了用于创建 apressBooks 数据库的 SQL 脚本。



```
01 DROP DATABASE IF EXISTS `apressBooks`;
02 CREATE DATABASE `apressBooks`;
03 CREATE TABLE `apressBooks`.`recipes` (
04  `id` int NOT NULL AUTO_INCREMENT,
05  `recipe_number` varchar(10) NOT NULL,
06  `recipe_name` varchar(100) NOT NULL,
07  `description` varchar(500) DEFAULT NULL,
08  PRIMARY KEY (`id`)
09 ) ;
10 insert into apressBooks.recipes values(1,
11 '12-1',
12 'Installing MySQL',
13 'Downloading and installation of a MySQL Database');
14 insert into apressBooks.recipes values(2,
15 '12-2',
16 'Connecting to a Database',
17 'DriverManager and DataSource Implementations');
18 insert into apressBooks.recipes values(3,
19 '12-3',
20 'Handling SQL Exceptions',
21 'Using SQLException');
22 insert into apressBooks.recipes values(4,
23 '12-4',
24 'Querying a Database and Retrieving Results',
25 'Obtaining and using data from a DBMS');
26 CREATE TABLE `apressBooks`.`publication` (
27   `id` int NOT NULL AUTO_INCREMENT,
28   `book_title` varchar(500) NOT NULL,
29  `publish_date` date DEFAULT NULL,
30  `publish_co` varchar(100) DEFAULT NULL,
31  PRIMARY KEY (`id`)
32 );
33 insert into apressBooks.publication values (
34 1,
35 'Java EE 17 Recipes',
36 date('2021-12-01'),
37 'APRESS');
38 insert into apressBooks.publication values (
39 2,
40 ‘Beginning Jakarta EE Web Development’,
41 date('2020-04-03'),
42 'APRESS');
Listing 12-5
ApressBookDatabase_create.sql
```

第 01 行用于删除数据库。`IF EXISTS`选项允许你仅在数据库已存在时才执行删除操作。该选项可以防止在首次运行创建脚本时，因数据库不存在而报错。`DROP`语句会删除数据库及其物理磁盘文件，因此如果你将来需要恢复该数据库，应事先做好备份。

第 02 行创建一个名为`apressBooks`的空白数据库。

第 03 行至第 09 行创建一个名为*recipes*的表，用于存储配方。

第 10 行至第 25 行向*recipes*表中插入数据。

第 26 行至第 32 行创建一个名为*publication*的表。

第 33 行至第 42 行向*publication*表中插入数据。

要执行该 SQL 脚本，你可以使用刚才看到的命令行客户端。你可以在本章的软件包中找到该脚本。打开命令行客户端。用文本编辑器打开`ApressBookDatabase_create.sql`，复制所有内容并粘贴到命令行客户端中，以创建并填充这些表。

### 工作原理

数据库由组织化的数据构成，即数据本身以及提供数据结构的模式。如今，大多数数据库都组织成由行和列组成的表。这是一种自然的数据组织方式，你可能通过使用电子表格对此已有所了解。你可以独立于将要存储的实际数据来定义表的特性。字段是表中的一个独立数据项，对应于行与列的交集。可以将一个或多个列指定为唯一键，用于标识每条记录。为此，你可以使用前面提到的某一列，或者使用名字、姓氏和出生日期的组合。优先于其他键使用的唯一键称为表的主键。

数据库管理系统（DBMS），例如 MySQL，是一个软件包，它允许你创建、读取、更新和删除（CRUD）数据项以及模式元素。

因此，在谈论数据库时，你需要区分三个方面。

*   它所包含的数据。
*   你为高效执行 CRUD 操作而施加于数据之上的结构。
*   允许你操作数据本身和数据库结构的软件（即 DBMS）。

使用数据库意味着你正在与其 DBMS 进行交互。你可以通过命令行界面（CLI）、DBMS 供应商和第三方提供的图形用户界面（GUI），或者通过 API 以编程方式来实现。

DBMS 可以为每个键构建一个*索引*，以便更快地检索数据。这会减慢行的插入和删除（即新记录）速度，因为 DBMS 需要花费时间来更新索引。大多数数据库被查询的频率高于被修改的频率。因此，定义索引通常是值得的，至少那些能够加速最常见查询的索引是如此。

结构化查询语言（SQL）是与 DBMS 交互时使用最广泛的语言。大多数 DBMS 并不支持完整的 SQL 标准。此外，供应商有时会添加非标准元素，这实际上阻碍了不同 DBMS 之间的完全可移植性。总的来说，无论我们谈论的是数据库组织、表结构还是实际数据，你都需要执行四种 CRUD 操作。相应的 SQL 语句以标识操作的关键字开头（例如，INSERT、SELECT、UPDATE 或 DELETE），必要时后跟指定操作针对何种实体类型的关键字（例如，DATABASE、TABLE 或 INDEX），以及附加元素。你使用 SELECT 语句来检索信息。

你可以使用 CREATE 语句创建数据库、表和索引，使用 ALTER 语句更新它们，使用 DROP 语句删除它们。类似地，你可以使用 CREATE 和 DROP 创建和删除视图，但一旦创建了视图，就无法更新它们。你使用 INSERT 在表中创建新行，使用 DELETE 删除它们。UPDATE 语句允许你修改整行或行中的一个或多个字段。

用于修改结构的语句统称为数据定义语言（DDL）。用于修改内容的语句称为数据操作语言（DML）。

在许多应用程序中，数据库、表、索引和视图的结构一旦初始定义后便保持不变。因此，在你的应用程序中，你通常只需要操作行和字段的语句。无论如何，你肯定需要 SELECT 语句，它用于查询数据库的结构及其包含的数据。最后，为了完整列出开发应用程序时可能需要的语句，还有 START TRANSACTION、COMMIT 和 ROLLBACK，这些语句用于处理事务。

当你想要检索、更新或删除行时，必须标识它们。你可以使用 WHERE 关键字后跟`<where_condition>`来实现。

在本章的示例中，我们选择使用*MySQL*作为 DBMS，首先是因为它是免费提供的，其次是因为它是免费 DBMS 中使用最广泛的一个。因此，它已被证明能够在各种环境中可靠运行。不过，最终你可以开发能够与其他传统关系型数据库管理系统（RDBMS）配合使用的 Java 应用程序，例如 Oracle Database 或 PostgreSQL。

## 12.2 连接数据库

### 问题

你希望从桌面 Java 应用程序中连接到数据库。



### 解决方案

使用 JDBC 的 `Connection` 对象来获取连接。具体做法是创建一个新的连接对象，然后加载特定数据库供应商所需的驱动程序。连接对象准备就绪后，调用其 `getConnection()` 方法。以下代码演示了如何获取与 MySQL 数据库的连接：通常，连接取决于指定的驱动程序。

```
package org.java17recipes.chapter12.recipe12_02;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;
import java.util.Properties;
public class CreateConnection {
static Properties props = new Properties();
String hostname = "localhost";
String port = "3306";
String database = "apressbooks";
String username = "root";
String password = "root";
public Connection getConnection() throws SQLException {
Connection conn = null;
String jdbcUrl;
jdbcUrl = "jdbc:mysql://" + this.hostname + ":" +
this.port  + "/" + this.database;
System.out.println(jdbcUrl);
conn = DriverManager.getConnection(jdbcUrl, this.username, this.password);
System.out.println("Successfully connected");
return conn;
}
public static void main(String[] args) {
CreateConnection createConnection = new CreateConnection();
try {
createConnection.getConnection();
} catch (SQLException e){
e.printStackTrace();
}
}
}
```

此示例中展示的方法返回一个 `Connection` 对象，该对象已准备好用于数据库访问。

### 工作原理

创建 JDBC 连接涉及几个步骤。首先，你需要确定需要哪个数据库驱动程序。确定所需驱动程序后，下载包含该驱动程序的 JAR 文件并将其放入类路径中。每个数据库供应商都提供不同的 JDBC 驱动程序，这些驱动程序打包在名称各异的 JAR 文件中；请查阅数据库文档以获取更多信息。对于本方案，你可以从 [`https://dev.mysql.com/downloads/connector/j/`](https://dev.mysql.com/downloads/connector/j/) 下载连接器。确保所选平台为“平台无关”：这对于在不同系统上分发软件非常重要。接下来，使用 JDBC 的 `DriverManager` 类来获取与数据库的连接。

要使用 `DriverManager` 类获取与数据库的连接，你需要向其传递一个包含 JDBC URL 的字符串。JDBC URL 由数据库供应商名称、托管数据库的服务器名称、数据库名称、数据库端口号以及有权访问你要使用的模式或数据库对象的有效数据库用户名和密码组成。创建 JDBC URL 的值通常从 `Properties` 文件中获取，以便在需要时可以轻松更改。为解决方案 1 创建 MySQL 数据库 JDBC URL 的代码如下所示。

```
String jdbcUrl = "jdbc:mysql:thin:@" + this.hostname + ":" +
this.port  + ":" + this.database;
```

将所有变量替换到字符串中后，它看起来如下所示。

```
jdbc:mysql://localhost:3306/apressbooks
```

创建 JDBC URL 后，可以将其传递给 `DriverManager.getConnection()` 方法以获取 `java.sql.Connection` 对象。如果传递给 `getConnection()` 方法的信息不正确，则会抛出 `java.sql.SQLException`；否则，将返回一个有效的 `Connection` 对象。

## 12.3 处理连接和 SQL 异常

### 问题

应用程序中的数据库活动引发了异常。你需要处理 SQL 异常，以免应用程序崩溃。

### 解决方案

使用 `try-catch` 块来捕获并处理 JDBC 连接或 SQL 查询抛出的任何 SQL 异常。以下代码演示了如何实现 `try-catch` 块来捕获 SQL 异常。

```
try {
// 执行数据库任务
} catch (java.sql.SQLException){
// 执行异常处理
}
```

### 工作原理

标准的 `try-catch` 块可以捕获 `java.sql.SQLException` 异常。如果这些异常未被处理，你的代码将无法编译。妥善处理这些异常是一个好主意，以防止在抛出此类异常时应用程序崩溃。几乎任何针对 `java.sql.Connection` 对象执行的工作都需要包含错误处理，以确保正确处理数据库异常。通常需要嵌套的 `try-catch` 块来处理所有可能的异常。你需要确保在完成工作且不再使用 `Connection` 对象后关闭连接。同样，关闭 `java.sql.Statement` 对象以清理内存分配也是一个好主意。

由于 `Statement` 和 `Connection` 对象需要关闭，因此通常会看到 `try-catch-finally` 块确保所有资源都按需得到处理。你可能会看到类似以下风格的旧版 JDBC 代码。

```
try {
// 执行数据库任务
} catch (java.sql.SQLException ex) {
// 执行异常处理
} finally {
try {
// 关闭 Connection 和 Statement 对象
} catch (java.sql.SQLException ex){
// 执行异常处理
}
}
```

较新的代码应利用 `try-with-resources` 语句编写，该语句允许将资源管理委托给 Java，而不是手动关闭。以下代码演示了如何使用 `try-with-resources` 打开连接、创建语句，然后在完成后关闭连接和语句。

```
try (Connection conn = createConn.getConnection();
Statement stmt = conn.createStatement();) {
ResultSet rs = stmt.executeQuery(qry);
while (rs.next()) {
// 执行一些工作
}
} catch (SQLException e) {
e.printStackTrace();
}
```

如前面的伪代码所示，通常需要嵌套的 `try-catch` 块来清理未使用的资源。正确的异常处理有时会使 JDBC 代码编写起来相当繁琐，但它也能确保需要数据库访问的应用程序不会失败，从而导致数据丢失。

## 12.4 查询数据库并检索结果

### 问题

应用程序中的某个进程需要查询数据库表中的数据。

### 解决方案

获取 JDBC 连接，然后使用 `java.sql.Connection` 对象创建一个 `Statement` 对象。`java.sql.Statement` 对象包含 `executeQuery()` 方法，该方法解析文本字符串并使用它来查询数据库。执行查询后，你可以在 `ResultSet` 对象中检索结果。以下示例查询名为 `RECIPES` 的数据库表并打印结果。

```
public class QueryDatabase {
private static CreateConnection createConn;
public static void main(String[] args) {
createConn = new CreateConnection();
queryDatabase();
}
public static void queryDatabase() {
String qry = "select recipe_num, recipe_name, description from recipes";
try (Connection conn = createConn.getConnection();
Statement stmt = conn.createStatement();) {
ResultSet rs = stmt.executeQuery(qry);
while (rs.next()) {
String recipe = rs.getString("RECIPE_NUM");
String name = rs.getString("RECIPE_NAME");
String desc = rs.getString("DESCRIPTION");
System.out.println(recipe + "\t" + name + "\t" + desc);
}
} catch (SQLException e) {
e.printStackTrace();
}
}
}
```

如果你使用第 12 章附带的数据库脚本执行此代码，你将收到以下结果。

```
12-1 安装 MySQL 下载并安装 MySQL 数据库
12-2 连接到数据库 DriverManager 和 DataSource 实现
12-3 处理 SQL 异常 使用 SQLException
12-4 查询数据库并检索结果 从 DBMS 获取和使用数据
```



### 工作原理

对数据库执行的最常见操作之一就是查询。使用 JDBC 执行数据库查询相当简单，尽管每次执行查询时都需要使用一些样板代码。首先，你需要为要查询的数据库和模式获取一个 `Connection` 对象。接下来，你需要构建一个查询并将其存储为字符串格式。然后，`Connection` 对象会创建一个 `Statement` 对象。你的查询字符串被传递给 `Statement` 对象的 `executeQuery()` 方法来查询数据库。下面展示了在不使用 `try-with-resources` 进行资源管理时的代码样式。

```
String qry = "select recipe_num, recipe_name, description from recipes";
Connection conn;
Statement stmt = null;
try {
conn = createConn.getConnection()
stmt = conn.createStatement();
ResultSet rs = stmt.executeQuery(qry);
...
```

同样的代码可以更高效地编写如下。

```
try (Connection conn = createConn.getConnection();
Statement stmt = conn.createStatement();) {
ResultSet rs = stmt.executeQuery(qry);
...
```

如你所见，`Statement` 对象的 `executeQuery()` 方法接受一个字符串并返回一个 `ResultSet` 对象。`ResultSet` 对象使得处理查询结果、以任意顺序获取所需信息变得容易。如果你查看示例中的下一行代码，会在 `ResultSet` 对象上创建一个 `while` 循环。这个循环持续调用 `ResultSet` 对象的 `next()` 方法，每次迭代获取查询返回的下一行数据。在这个例子中，`ResultSet` 对象被命名为 `rs`。因此，只要 `rs.next()` 返回 true，循环就会继续处理。一旦所有返回的行都被处理完毕，`rs.next()` 返回 false，表示没有更多行需要处理了。

在 `while` 循环内部，每一行返回的数据都会被处理。每次遍历时，都会解析 `ResultSet` 对象以获取指定列名的值。请注意，如果预期列返回的是字符串，你必须调用 `ResultSet getString()` 方法，并以字符串格式传递列名。类似地，如果预期列返回的是整数，你需要调用 `ResultSet getInt()` 方法，并以字符串格式传递列名。其他数据类型也是如此。这些方法返回相应的列值。在本节解决方案的示例中，这些值被存储到局部变量中。

```
String recipe = rs.getString("RECIPE_NUM");
String name = rs.getString("RECIPE_NAME");
String desc = rs.getString("DESCRIPTION");
```

一旦获取了列值，你就可以对存储在局部变量中的值进行任何操作。在这个例子中，它们通过 `System.out()` 方法被打印出来。

```
System.out.println(recipe + "\t" + name + "\t" + desc);
```

尝试查询数据库时可能会抛出 `java.sql.SQLException`（例如，如果 `Connection` 对象没有正确获取，或者你试图查询的数据库表不存在）。你必须提供异常处理来处理这些情况下的错误。因此，所有数据库处理代码都应放在 `try` 块内。然后 `catch` 块处理 `SQLException`；因此，如果抛出异常，将使用 `catch` 块中的代码进行处理。听起来很简单，对吧？确实如此，但每次执行数据库查询时都必须这样做——这会产生大量样板代码。

如果语句和连接处于打开状态，关闭它们总是一个好主意。使用 `try-with-resources` 结构是资源管理最高效的解决方案。完成后关闭资源有助于确保系统能够根据需要重新分配资源，并对数据库保持尊重。尽快关闭连接非常重要，以便其他进程可以使用它们。

## 12.5 执行 CRUD 操作

### 问题

你需要在应用程序中具备执行标准数据库操作的能力。也就是说，你需要能够对数据库记录进行 CRUD 操作。

### 解决方案

创建一个 `Connection` 对象并获取数据库连接，然后使用从 `java.sql.Connection` 对象获得的 `java.sql.Statement` 对象执行 CRUD 操作。用于这些操作的数据库表格式如下。

```
RECIPES (
id              int not null,
recipe_number   varchar(10) not null,
recipe_name     varchar(100) not null,
description     varchar(500),
constraint recipes_pk primary key (id) enable
);
```

以下代码片段演示了如何使用 JDBC 执行每个 CRUD 操作。

```
import java.sql.Connection;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;
import org.java17recipes.chapter12.recipe12_01.CreateConnection;
public class CrudOperations {
static CreateConnection createConn;
public static void main(String[] args) {
createConn = new CreateConnection();
performCreate();
performRead();
performUpdate();
performDelete();
System.out.println("-- 最终状态 --");
performRead();
}
private static void performCreate(){
String sql = "INSERT INTO RECIPES VALUES(" +
"NULL, " +
"'12-5', " +
"'Performing CRUD Operations', " +
"'How to perform create, read, update, delete functions')";
try (Connection conn = createConn.getConnection();
Statement stmt = conn.createStatement();) {
// 返回行数，如果不成功则返回 0
int result = stmt.executeUpdate(sql);
if (result == 1{
System.out.println("-- 记录已创建 --");
} else {
System.err.println("!! 记录未创建 !!");
}
} catch (SQLException e) {
e.printStackTrace();
}
}
private static void performRead(){
String qry = "select recipe_number, recipe_name, description from recipes";
try (Connection conn = createConn.getConnection();
Statement stmt = conn.createStatement();) {
ResultSet rs = stmt.executeQuery(qry);
while (rs.next()) {
String recipe = rs.getString("RECIPE_NUMBER");
String name = rs.getString("RECIPE_NAME");
String desc = rs.getString("DESCRIPTION");
System.out.println(recipe + "\t" + name + "\t" + desc);
}
} catch (SQLException e) {
e.printStackTrace();
}
}
private static void performUpdate(){
String sql = "UPDATE RECIPES " +
"SET RECIPE_NUMBER = '12-5' " +
"WHERE RECIPE_NUMBER = '12-4'";
try (Connection conn = createConn.getConnection();
Statement stmt = conn.createStatement();) {
int result = stmt.executeUpdate(sql);
if (result  == 1){
System.out.println("-- 记录已更新 --");
} else {
System.out.println("!! 记录未更新 !!");
}
} catch (SQLException e) {
e.printStackTrace();
}
}
private static void performDelete(){
String sql = "DELETE FROM RECIPES WHERE RECIPE_NUMBER = '12-5'";
try (Connection conn = createConn.getConnection();
Statement stmt = conn.createStatement();) {
int result = stmt.executeUpdate(sql);
if (result > 0){
System.out.println("-- 记录已删除 --");
} else {
System.out.println("!! 记录未删除 !!");
}
} catch (SQLException e) {
e.printStackTrace();
}
}
}
```

以下是运行代码的结果。

```
-- 记录已创建 –
12-1 Installing MySQL 下载并安装 MySQL 数据库
12-2 Connecting to a Database DriverManager 和 DataSource 实现
12-3 Handling SQL Exceptions 使用 SQLException
12-4 Querying a Database and Retrieving Results 从 DBMS 获取和使用数据
12-5 Performing CRUD Operations 如何执行创建、读取、更新、删除功能
-- 记录已更新 --
-- 记录已删除 –
-- 最终状态 --
12-1 Installing MySQL  下载并安装 MySQL 数据库
12-2 Connecting to a Database DriverManager 和 DataSource 实现
12-3 Handling SQL Exceptions 使用 SQLException
```



### 工作原理

执行几乎所有数据库任务都使用相同的基本代码格式。格式如下：

1.  获取数据库连接。
2.  从连接创建语句对象。
3.  使用语句对象执行数据库任务。
4.  处理数据库任务的结果。
5.  关闭语句对象（如果不再使用，也关闭数据库连接）。

使用 JDBC 执行查询与执行 DML 的主要区别在于，根据要执行的操作，需要在 `Statement` 对象上调用不同的方法。要执行查询，需要调用 `Statement` 对象的 `executeQuery()` 方法。要执行插入、更新和删除等 DML 任务，则调用 `executeUpdate()` 方法。

本方案中的 `performCreate()` 方法演示了如何向数据库插入一条记录。要向数据库插入记录，需要以字符串形式构建一条 SQL `INSERT` 语句。要执行插入操作，请将 SQL 字符串传递给 `Statement` 对象的 `executeUpdate()` 方法。如果 `INSERT` 执行成功，将返回一个 int 值，指定已插入的行数。如果 `INSERT` 操作未成功执行，则返回零，或者抛出 `SQLException`，表明语句或数据库连接存在问题。

本方案中的 `performRead()` 方法演示了如何查询数据库。要执行查询，请调用 `Statement` 对象的 `executeQuery()` 方法，并以字符串形式传递一条 SQL 语句。结果是一个 `ResultSet` 对象，可用于处理返回的数据。有关执行查询的更多信息，请参见方案 12-4。

本方案中的 `performUpdate()` 方法演示了如何更新数据库表中的记录。首先，以字符串形式构建一条 SQL `UPDATE` 语句。然后，要执行更新操作，请将 SQL 字符串传递给 `Statement` 对象的 `executeUpdate()` 方法。如果 `UPDATE` 成功执行，将返回一个 int 值，指定已更新的记录数。如果 `UPDATE` 操作未成功执行，则返回零，或者抛出 `SQLException`，表明语句或数据库连接存在问题。

最后一个需要介绍的数据库操作是 `DELETE` 操作。本方案中的 `performDelete()` 方法演示了如何从数据库中删除记录。首先，以字符串形式构建一条 SQL `DELETE` 语句。然后，要执行删除操作，请将 SQL 字符串传递给 `Statement` 对象的 `executeUpdate()` 方法。如果删除成功，将返回一个 int 值，指定已删除的行数。否则，如果删除失败，则返回零，或者抛出 `SQLException`，表明语句或数据库连接存在问题。

几乎每个数据库应用程序在某个时候都会至少使用一种 CRUD 操作。这是在 Java 应用程序中处理数据库时需要了解的基础 JDBC 知识。即使你不直接使用 JDBC API，了解这些基础知识也是有益的。

## 12.6 简化连接管理

### 问题

你的应用程序需要使用数据库，并且每次交互都需要打开一个连接。你希望使用一个单一的类来执行此任务，而不是每次需要访问数据库时都编写打开数据库连接的逻辑。

### 解决方案

编写一个类来处理应用程序中的所有连接管理。这样做允许你调用该类来获取连接，而不是每次需要访问数据库时都设置一个新的 `Connection` 对象。请执行以下步骤为你的 JDBC 应用程序设置连接管理环境。

1.  创建一个名为 `CreateConnection.java` 的类，该类封装了应用程序的连接逻辑。
2.  创建一个 `Properties` 文件来存储你的连接信息。将该文件放在类路径上的某个位置，以便 `CreateConnection` 类可以加载它。
3.  使用 `CreateConnection` 类获取你的数据库连接。

以下 `CreateConnection` 类的代码可用于集中式连接管理。

```
import java.io.File;
import java.io.IOException;
import java.io.InputStream;
import java.nio.file.FileSystems;
import java.nio.file.Files;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;
import java.util.Properties;
public class CreateConnection {
static Properties props = new Properties();
String hostname = null;
String port = null;
String database = null;
String username = null;
String password = null;
public CreateConnection(){
try (InputStream in = Files.newInputStream(FileSystems.getDefault().
getPath(System.getProperty("user.dir") +
File.separator + "db_props.properties")); ) {
props.load(in);
in.close();
} catch (IOException ex) {
ex.printStackTrace();
}
loadProperties();
}
public final void loadProperties(){
this.hostname = props.getProperty("host_name");
port = props.getProperty("port_number");
database = props.getProperty("db_name");
username = props.getProperty("username");
password = props.getProperty("password");
}
public Connection getConnection() throws SQLException {
Connection conn = null;
String jdbcUrl;
jdbcUrl = "jdbc:mysql://" + this.hostname + ":" +
this.port  + "/" + this.database;
System.out.println(jdbcUrl);
conn = DriverManager.getConnection(jdbcUrl, this.username, this.password);
System.out.println("Successfully connected");
return conn;
}
public static void main(String[] args) {
CreateConnection createConnection = new CreateConnection();
try {
Connection conn = createConnection.getConnection();
if (conn != null) {
System.out.println("Closing Connection...");
conn.close();
}
} catch (SQLException e){
e.printStackTrace();
}
}
}
```

以下是输出结果。

```
jdbc:mysql://localhost:3306/apressbooks
Successfully connected
Closing Connection...
```

接下来，以下文本行是用于获取数据库连接的属性文件内容的示例。在此示例中，属性文件名为 `db_props.properties`。

```
host_name=your_db_server_name
db_name=your_db_name
username=db_username
password=db_username_password
port_number=db_port_number
```

最后，使用 `CreateConnection` 类为你的应用程序获取连接。以下代码演示了这一概念。

```
CreateConnection createConn = new CreateConnection();
try(Connection conn = createConn.getConnection()) {
performDbTask();
} catch (java.sql.SQLException ex) {
ex.printStackTrace();
}
```

此代码使用了 `try-with-resources` 语句，以便在执行数据库任务后自动关闭连接。



### 工作原理

在数据库应用程序中获取连接可能涉及大量代码。此外，如果每次获取连接时都重新输入代码，这个过程很容易出错。通过将数据库连接逻辑封装在单个类中，你可以在需要连接数据库时复用相同的连接代码。这能提高你的工作效率，减少输入错误的机会，并增强可维护性。如果需要进行更改，只需在一个地方修改，而不是在多个不同位置。

创建一种策略性的连接方法对你以及未来可能需要维护你代码的其他人都有好处。虽然在使用应用服务器时，数据源是管理数据库连接的首选技术，但本解决方案演示了如何使用标准 JDBC `DriverManager` 连接。使用 `DriverManager` 的一个安全问题是，你需要将数据库凭据存储在某个地方供应用程序使用。以纯文本形式存储这些凭据在任何地方都不安全，将其嵌入应用程序代码中也不安全，因为代码将来可能会被反编译。如解决方案所示，一个属性文件在磁盘上存储了数据库凭据。假设该属性文件在部署到服务器之前的某个时刻已被加密，并且应用程序能够处理解密。

如解决方案所示，代码从属性文件中读取数据库凭据、主机名、数据库名称和端口号。然后将这些信息拼接起来，形成一个 JDBC URL，`DriverManager` 可以使用该 URL 获取与数据库的连接。一旦获取到连接，就可以在任何地方使用它，然后关闭。

你可以开发一个 JDBC 应用程序，使得获取连接所需的代码在整个程序中硬编码。相反，本解决方案将所有获取连接的代码封装在单个类中，这样开发人员就不必为此担心。这种技术还使代码更易于维护。例如，如果应用程序最初使用 `DriverManager` 部署，但后来具备了使用 `DataSource` 的能力，那么只需更改很少的代码。

## 12.7 防范 SQL 注入

### 问题

你的应用程序执行数据库任务。为了降低 SQL 注入攻击的风险，你需要确保没有未经过滤的文本字符串被附加到 SQL 语句中并针对数据库执行。

提示

虽然预编译语句是本解决方案的核心，但它们的用途不仅限于防范 SQL 注入。它们还提供了一种集中化并更好地控制应用程序中使用的 SQL 的方法。例如，与其创建同一查询的多个可能不同的版本，不如将查询创建为预编译语句一次，然后在代码中的许多不同位置调用它。对查询逻辑的任何更改只需在准备该语句时进行。

### 解决方案

使用 `PreparedStatement` 对象来执行数据库任务。它们向 DBMS 发送预编译的 SQL 语句，而不是字符串。以下代码演示了如何使用 `java.sql.PreparedStatement` 对象执行数据库查询和数据库更新。

在以下代码示例中，`PreparedStatement` 查询数据库中给定的记录。假设一个包含配方编号的 `String[]` 数组作为变量传递给此代码。

```
private static void queryDbRecipe(String[] recipeNumbers) {
String sql = "SELECT ID, RECIPE_NUMBER, RECIPE_NAME, DESCRIPTION "
+ "FROM RECIPES "
+ "WHERE RECIPE_NUMBER = ?";
try (PreparedStatement pstmt = conn.prepareStatement(sql)) {
for (String recipeNumber : recipeNumbers) {
pstmt.setString(1, recipeNumber);
ResultSet rs = pstmt.executeQuery();
while (rs.next()) {
System.out.println(rs.getString(2) + ": " + rs.getString(3)
+ " - " + rs.getString(4));
}
}
} catch (SQLException ex) {
ex.printStackTrace();
}
}
```

下一个示例演示了一个向数据库插入记录的 `PreparedStatement` 对象。假设配方编号、标题和描述作为变量传递给此代码。`ID` 列被声明为 NOT NULL 和 AUTO-INCREMENT，因此也可以为该列分配 NULL 以生成序列号。

```
private static void insertRecord(String recipeNumber,
String title,
String description) {
String sql = "INSERT INTO RECIPES VALUES(" +
"null, ?,?,?)";
try(PreparedStatement pstmt = conn.prepareStatement(sql);) {
pstmt.setString(1, recipeNumber);
pstmt.setString(2, title);
pstmt.setString(3, description);
pstmt.executeUpdate();
System.out.println("Record successfully inserted.");
} catch (SQLException ex){
ex.printStackTrace();
}
}
```

在最后一个示例中，一个 `PreparedStatement` 对象从数据库中删除一条记录。同样，假设 `recipeNumber` 字符串作为变量传递给此代码。

```
private static void deleteRecord(String recipeNumber) {
String sql = "DELETE FROM RECIPES WHERE " +
"RECIPE_NUMBER = ?";
try(PreparedStatement pstmt = conn.prepareStatement(sql);) {
pstmt.setString(1, recipeNumber);
pstmt.executeUpdate();
System.out.println("Recipe " + recipeNumber + " successfully deleted.");
} catch (SQLException ex){
ex.printStackTrace();
}
}
main 方法如下：
public static void main(String[] args) {
try {
CreateConnection createConn = new CreateConnection();
conn = createConn.getConnection();
String[] recipeArr = new String[1];
recipeArr[0] ="12-1";
queryDbRecipe(recipeArr);
insertRecord(
"12-6",
"Simplifying and Adding Security with Prepared Statements",
"Working with Prepared Statements");
recipeArr[0] ="12-6";
queryDbRecipe(recipeArr);
deleteRecord("12-6");
} catch (java.sql.SQLException ex) {
System.out.println(ex);
} finally {
if (conn != null) {
try {
conn.close();
} catch (SQLException ex) {
ex.printStackTrace();
}
}
}
}
```

以下是输出结果。

```
12-1: Installing MySQL - Downloading and installation of a MySQL Database
Record successfully inserted.
12-6: Simplifying and Adding Security with Prepared Statements - Working with Prepared Statements
Recipe 12-6 successfully deleted.
```

如你所见，`PreparedStatement` 对象与标准的 JDBC 语句对象非常相似，但它向 DBMS 发送的是预编译的 SQL，而不是文本字符串。



### 工作原理

虽然标准的 JDBC 语句能够完成任务，但残酷的现实是，它们有时可能不安全且使用起来很繁琐。例如，如果动态 SQL 语句查询数据库，就可能发生糟糕的情况。用户输入的字符串被赋值给一个变量，并与预期的 SQL 字符串拼接在一起。在大多数普通情况下，用户输入的字符串会被拼接，SQL 字符串会按预期查询数据库。然而，攻击者可能会决定在字符串中放置恶意代码（即 SQL 注入），然后这些代码会通过标准的 `Statement` 对象被无意中发送到数据库。

使用 `PreparedStatement` 可以防止此类恶意字符串被拼接到 SQL 字符串中并传递给 DBMS，因为它们采用了不同的方法。`PreparedStatement` 使用替换变量而非拼接来使 SQL 字符串动态化。它们还会被预编译，这意味着在 SQL 发送到 DBMS 之前，会先形成一个有效的 SQL 字符串。此外，`PreparedStatement` 可以帮助你的应用程序获得更好的性能，因为如果相同的 SQL 需要运行多次，它只需编译一次。之后，替换变量可以互换，但整个 SQL 可以被 `PreparedStatement` 非常快速地执行。

让我们看看 `PreparedStatement` 对象在实际中是如何工作的。如果你查看本方案解决方案中的第一个示例，可以看到正在查询数据库表 `RECIPES`，传入一个 `RECIPE_NUMBER` 并检索匹配记录的结果。SQL 字符串如下所示。

```
String sql = "SELECT ID, RECIPE_NUMBER, RECIPE_NAME, DESCRIPTION " +
"FROM RECIPES " +
"WHERE RECIPE_NUM = ?";
```

除了字符串末尾的问号（`?`）之外，SQL 文本看起来一切标准。在 SQL 字符串中放置问号表示在执行 SQL 时，会使用一个替换变量来代替该问号。使用 `PreparedStatement` 对象的下一步是声明一个 `PreparedStatement` 类型的变量。这可以通过以下代码行看到。

```
PreparedStatement pstmt = null;
```

`PreparedStatement` 实现了 `AutoCloseable`，因此它可以在 `try-with-resources` 块的上下文中使用。一旦声明了 `PreparedStatement`，就可以使用它了。然而，使用 `PreparedStatement` 可能不会抛出异常。因此，如果没有使用 `try-with-resources`，`PreparedStatement` 应该出现在 `try-catch` 块中，以便能够优雅地处理任何异常。例如，如果数据库连接因某种原因不可用，或者 SQL 字符串无效，就可能发生异常。与其让应用程序因此类问题崩溃，不如在 `catch` 块中明智地处理异常。下面的 `try-catch` 块包含了将 SQL 字符串发送到数据库并检索结果所需的代码。

```
try(PreparedStatement pstmt = conn.prepareStatement(sql);) {
pstmt.setString(1, recipeNumber);
ResultSet rs = pstmt.executeQuery();
while(rs.next()){
System.out.println(rs.getString(2) + ": " + rs.getString(3) +
" - " + rs.getString(4));
}
} catch (SQLException ex) {
ex.printStackTrace();
}
```

首先，可以看到 `Connection` 对象实例化了一个 `PreparedStatement` 对象。SQL 字符串在创建时被传递给 `PreparedStatement` 对象的构造函数。由于 `PreparedStatement` 是在 `try-with-resources` 结构中实例化的，当它不再使用时会被自动关闭。接下来，`PreparedStatement` 对象为已放入 SQL 字符串中的任何替换变量设置值。如你所见，示例中使用了 `PreparedStatement setString()` 方法来将位置 1 的替换变量设置为 `recipeNumber` 变量的内容。替换变量的位置与 SQL 字符串中问号（`?`）的位置相关联。字符串中的第一个问号被分配给第一个位置，第二个问号分配给第二个位置，依此类推。如果分配了多个替换变量，则会对 `PreparedStatement` 对象进行多次调用，依次分配每个变量，直到所有变量都被处理完毕。`PreparedStatement` 对象可以接受多种不同数据类型的替换变量。例如，如果将一个 int 值分配给替换变量，则需要调用 `setInt(position, variable)` 方法。有关使用 `PreparedStatement` 对象分配替换变量的完整方法集，请参阅在线文档或 IDE 的代码补全功能。

一旦所有变量都分配完毕，就可以执行 SQL 字符串了。`PreparedStatement` 对象包含一个 `executeQuery()` 方法，该方法执行一个代表查询的 SQL 字符串。`executeQuery()` 方法返回一个 `ResultSet` 对象，其中包含针对特定 SQL 查询从数据库获取的结果。接下来，可以遍历 `ResultSet` 来获取从数据库中检索到的值。同样，通过调用 `ResultSet` 对象相应的 getter 方法并传入你想要获取的列值的位置，来进行位置分配以检索结果。位置由列名在 SQL 字符串中出现的顺序决定。在示例中，第一个位置对应 `RECIPE_NUMBER` 列，第二个位置对应 `RECIPE_NAME` 列，依此类推。如果 `recipeNumber` 字符串变量等于 12-1，那么执行示例中查询的结果将如下所示。

```
12-1: Installing MySQL - downloading and installation of a MySQL Database
```

当然，如果替换变量设置不正确，或者 SQL 字符串有问题，就会抛出异常。这将导致执行 `catch` 块中包含的代码。你还应该确保在使用完 `PreparedStatement` 后进行清理，即在使用完毕后关闭该语句。如果你没有使用 `try-with-resources` 结构，最佳实践是将所有清理代码放在 `finally` 块中，以确保 `PreparedStatement` 对象被正确关闭，即使抛出了异常也是如此。在示例中，`finally` 块如下所示。

```
finally {
if (pstmt != null){
try {
pstmt.close();
} catch (SQLException ex) {
ex.printStackTrace();
}
}
}
```

你可以看到，会检查已实例化的 `PreparedStatement` 对象 `pstmt` 是否为 `NULL`。如果不是，则通过调用 `close()` 方法将其关闭。

通过处理本方案解决方案中的代码，你可以看到类似的代码处理数据库的 `INSERT`、`UPDATE` 和 `DELETE` 语句。在这些情况下，唯一的区别是调用的是 `PreparedStatement executeUpdate()` 方法，而不是 `executeQuery()` 方法。`executeUpdate()` 方法返回一个 int 值，表示受 SQL 语句影响的行数。

使用 `PreparedStatement` 对象优于使用 JDBC `Statement` 对象。这是因为它们更安全且性能更好。它们还可以使你的代码更易于理解和维护。

## 12.8 执行事务

### 问题

你的应用程序的结构要求按顺序处理任务。一个任务依赖于另一个任务，并且每个进程执行不同的数据库操作。如果途中的某个任务失败，则需要撤销已经发生的数据库处理。



### 解决方案

将你的`Connection`对象的自动提交设置为`false`，然后执行你想要完成的事务。成功执行每个事务后，手动提交`Connection`对象；否则，回滚所有已发生的事务。以下代码示例演示了事务管理。如果你查看`TransactionExample`类的`main()`方法，会发现`Connection`对象的`autoCommit()`偏好已被设置为`false`，以便将数据库语句分组形成一个事务。如果事务中的所有语句都成功执行，则通过调用`commit()`方法手动提交`Connection`对象；否则，通过调用`rollback()`方法回滚所有语句。默认情况下，`autoCommit`设置为`true`，这会自动将每条语句视为一个单独的事务。

```
import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import org.java17recipes.chapter12.recipe12_01.CreateConnection;
public class TransactionExample {
public static Connection conn = null;
public static void main(String[] args) {
boolean successFlag = false;
try {
CreateConnection createConn = new CreateConnection();
conn = createConn.getConnection();
conn.setAutoCommit(false);
queryDbRecipes();
successFlag = insertRecord(
"12-6",
"Simplifying and Adding Security with Prepared Statements",
"Working with Prepared Statements");
if (successFlag == true){
successFlag = insertRecord(
null,
"Simplifying and Adding Security with Prepared Statements",
"Working with Prepared Statements");
}
// 提交事务
if (successFlag == true)
conn.commit();
else
conn.rollback();
conn.setAutoCommit(true);
queryDbRecipes();
} catch (java.sql.SQLException ex) {
System.out.println(ex);
} finally {
if (conn != null) {
try {
conn.close();
} catch (SQLException ex) {
ex.printStackTrace();
}
}
}
}
private static void queryDbRecipes(){
String sql = "SELECT ID, RECIPE_NUMBER, RECIPE_NAME, DESCRIPTION " +
"FROM RECIPES";
try(PreparedStatement pstmt = conn.prepareStatement(sql);) {
ResultSet rs = pstmt.executeQuery();
while(rs.next()){
System.out.println(rs.getString(2) + ": " + rs.getString(3) + " - " + rs.getString(4));
}
} catch (SQLException ex) {
ex.printStackTrace();
}
}
private static boolean insertRecord(String recipeNumber,
String title,
String description){
String sql = "INSERT INTO RECIPES VALUES(" +
"NULL, ?,?,?)";
boolean success = false;
try(PreparedStatement pstmt = conn.prepareStatement(sql);) {
pstmt.setString(1, recipeNumber);
pstmt.setString(2, title);
pstmt.setString(3, description);
pstmt.executeUpdate();
System.out.println("Record successfully inserted.");
success = true;
} catch (SQLException ex){
success = false;
ex.printStackTrace();
}
return success;
}
}
```

以下是输出结果。

```
12-2: Connecting to a Database - DriverManager and DataSource Implementations
12-3: Handling SQL Exceptions - Using SQLException
Record successfully inserted.
java.sql.SQLIntegrityConstraintViolationException: Column 'recipe_number' cannot be null
at mysql.connector.java@8.0.27/com.mysql.cj.jdbc.exceptions.SQLError.createSQLException(SQLError.java:117)
at mysql.connector.java@8.0.27/com.mysql.cj.jdbc.exceptions.SQLExceptionsMapping.translateException(SQLExceptionsMapping.java:122)
at mysql.connector.java@8.0.27/com.mysql.cj.jdbc.ClientPreparedStatement.executeInternal(ClientPreparedStatement.java:953)
at mysql.connector.java@8.0.27/com.mysql.cj.jdbc.ClientPreparedStatement.executeUpdateInternal(ClientPreparedStatement.java:1098)
at mysql.connector.java@8.0.27/com.mysql.cj.jdbc.ClientPreparedStatement.executeUpdateInternal(ClientPreparedStatement.java:1046)
at mysql.connector.java@8.0.27/com.mysql.cj.jdbc.ClientPreparedStatement.executeLargeUpdate(ClientPreparedStatement.java:1371)
at mysql.connector.java@8.0.27/com.mysql.cj.jdbc.ClientPreparedStatement.executeUpdate(ClientPreparedStatement.java:1031)
at org.java17recipes.chapter12.recipe12_08.TransactionExample.insertRecord(TransactionExample.java:83)
at org.java17recipes.chapter12.recipe12_08.TransactionExample.main(TransactionExample.java:29)
12-1: Installing MySQL - Downloading and installation of a MySQL Database
12-2: Connecting to a Database - DriverManager and DataSource Implementations
12-3: Handling SQL Exceptions - Using SQLException
```

最终，如果任何一条语句执行失败，所有事务都会被回滚。但如果所有语句都成功执行，则一切都会被提交。

### 工作原理

事务管理在应用程序中扮演着重要角色。这一点对于执行相互依赖的不同任务的应用程序尤其如此。在许多情况下，如果事务中执行的任务之一失败，那么整个事务失败比仅部分完成更可取。例如，假设你正在向应用程序数据库添加数据库用户记录。现在，假设为你的应用程序添加一个用户需要修改几个不同的数据库表，可能是一个角色表等等。如果你的第一个表修改正确，而第二个表修改失败，会发生什么？你会得到一个部分完成的应用程序用户添加操作，你的用户很可能无法按预期访问应用程序。在这种情况下，如果其中一个更新失败，最好回滚所有已完成的数据库修改，以便数据库保持干净状态，并可以再次尝试该事务。

默认情况下，`Connection`对象设置为自动提交开启。这意味着每个数据库的`INSERT`、`UPDATE`或`DELETE`语句都会立即提交。通常，这是你希望应用程序运行的方式。但是，当你有许多相互依赖的数据库语句时，关闭自动提交以便所有语句可以一次性提交就变得很重要。为此，调用`Connection`对象的`setAutoCommit()`方法并传入`false`值。正如你在本解决方案中看到的，调用了`setAutoCommit()`方法并传入`false`值，然后执行数据库语句。这样做会使所有数据库语句的更改变为临时性的，直到调用`Connection`对象的`commit()`方法。这允许你在发出`commit()`之前确保所有语句都正确执行。请查看本解决方案中`TransactionExample`类的`main()`方法中包含的事务管理代码。

```
boolean successFlag = false;
...
CreateConnection createConn = new CreateConnection();
conn = createConn.getConnection();
conn.setAutoCommit(false);
queryDbRecipes();
successFlag = insertRecord(
"12-6",
"Simplifying and Adding Security with Prepared Statements",
"Working with Prepared Statements");
if (successFlag == true){
successFlag = insertRecord(
null,
"Simplifying and Adding Security with Prepared Statements",
"Working with Prepared Statements");
}
// 提交事务
if (successFlag == true)
conn.commit();
else
conn.rollback();
conn.setAutoCommit(true);
```

请注意，仅当所有事务语句都成功处理时，才会调用`commit()`方法。如果其中任何一个失败，`successFlag`将等于`false`，这将导致调用`rollback()`方法。在本解决方案中，第二次调用`insertRecord()`尝试向`RECIPE_NUMBER`列插入一个`NULL`值，这是不允许的。因此，该插入操作失败，并且所有内容（包括之前的插入操作）都会被回滚。

## 12.9 创建可滚动的 ResultSet



### 问题

你已查询数据库并获得了一些结果。你想将这些结果存储在一个对象中，以便能够向前和向后遍历结果，并根据需要更新值。

### 解决方案

创建一个可滚动的 `ResultSet` 对象，然后你就可以读取下一条、第一条、最后一条和上一条记录。使用可滚动的 `ResultSet` 对象允许以任意方向获取查询结果，从而按需检索数据。下面的示例方法演示了如何创建一个可滚动的 `ResultSet` 对象。

```
private static void queryDbRecipes(){
String sql = "SELECT ID, RECIPE_NUMBER, RECIPE_NAME, DESCRIPTION " +
"FROM RECIPES";
try(PreparedStatement pstmt =conn.prepareStatement(sql,
ResultSet.TYPE_SCROLL_INSENSITIVE, ResultSet.CONCUR_READ_ONLY);
ResultSet rs = pstmt.executeQuery()) {
rs.first();
System.out.println(rs.getString(2) + ": " + rs.getString(3) +
" - " + rs.getString(4));
rs.next();
System.out.println(rs.getString(2) + ": " + rs.getString(3) +
" - " + rs.getString(4));
rs.previous();
System.out.println(rs.getString(2) + ": " + rs.getString(3) +
" - " + rs.getString(4));
rs.last();
System.out.println(rs.getString(2) + ": " + rs.getString(3) +
" - " + rs.getString(4));
} catch (SQLException ex) {
ex.printStackTrace();
}
}
主方法如下：
public static void main(String[] args) {
boolean successFlag = false;
try {
CreateConnection createConn = new CreateConnection();
conn = createConn.getConnection();
// 执行可滚动查询
queryDbRecipes();
} catch (java.sql.SQLException ex) {
System.out.println(ex);
} finally {
if (conn != null) {
try {
conn.close();
} catch (SQLException ex) {
ex.printStackTrace();
}
}
}
}
```

执行此方法，使用本章最初加载的数据将产生以下输出。

```
Successfully connected
12-1: Connecting to a Database - DriverManager and DataSource Implementations - More to Come
12-2: Querying a Database and Retrieving Results - Obtaining and Using Data from a DBMS
12-1: Connecting to a Database - DriverManager and DataSource Implementations - More to Come
12-3: Handling SQL Exceptions - Using SQLException
```

### 工作原理

普通的 `ResultSet` 对象只允许向前获取结果。应用程序可以从检索到的第一条记录开始，一直处理到默认 `ResultSet` 对象的最后一条记录。有时，应用程序在遍历 `ResultSet` 对象时需要更多功能。例如，假设你想编写一个应用程序，允许用户显示检索到的第一条或最后一条记录，或者通过结果进行向前或向后翻页。使用标准的 `ResultSet` 很难做到这一点。然而，通过创建一个可滚动的 `ResultSet`，你可以轻松地在结果中向后和向前移动。

要创建可滚动的 `ResultSet` 对象，你必须首先创建一个能够生成可滚动 `ResultSet` 的 `Statement` 或 `PreparedStatement` 实例。创建 `Statement` 时，必须将 `ResultSet` 滚动类型常量值传递给 `Connection` 对象的 `createStatement()` 方法。同样，在使用 `PreparedStatement` 时，必须将滚动类型常量值传递给 `Connection` 对象的 `prepareStatement()` 方法。

你还必须传递一个 `ResultSet` 并发常量，以告知 `ResultSet` 是否可更新。默认值是 `ResultSet.CONCUR_READ_ONLY`，这意味着 `ResultSet` 对象不可更新。另一种并发类型是 `ResultSet.CONCUR_UPDATABLE`，表示一个可更新的 `ResultSet` 对象。

在本技巧的解决方案中，使用了 `PreparedStatement` 对象。创建一个能够生成可滚动 `ResultSet` 的 `PreparedStatement` 对象，代码如下所示。

```
pstmt = conn.prepareStatement(sql, ResultSet.TYPE_SCROLL_INSENSITIVE,
ResultSet.CONCUR_READ_ONLY);
```

一旦创建了 `PreparedStatement` 对象，就会返回一个可滚动的 `ResultSet`。你可以通过调用 `ResultSet` 的方法来指示要移动的方向或要定位的位置，从而使用可滚动的 `ResultSet` 在多个方向上进行遍历。以下代码行检索 `ResultSet` 中的第一条记录。

```
ResultSet rs = pstmt.executeQuery();
rs.first();
```

本技巧的解决方案演示了几种不同的滚动方向。具体来说，你可以看到调用了 `ResultSet` 的 `first()`、`next()`、`last()` 和 `previous()` 方法，以移动到 `ResultSet` 中的不同位置。

可滚动的 `ResultSet` 对象在应用程序开发中有一席之地。它们是那种在你需要时就能派上用场的便利功能，但也是你可能并不经常需要的东西。

## 12.10 创建可更新的 ResultSet

### 问题

一个应用程序任务已查询数据库并获得了结果。你将这些结果存储到了一个 `ResultSet` 对象中，并且想要更新 `ResultSet` 中的某些值，然后将它们提交回数据库。

### 解决方案

让你的 `ResultSet` 对象可更新，然后在遍历结果时根据需要更新行。下面的示例方法演示了如何使 `ResultSet` 可更新，以及如何更新该 `ResultSet` 中的内容，并最终将其持久化到数据库中。

```
private static void queryAndUpdateDbRecipes(String recipeNumber){
String sql = "SELECT ID, RECIPE_NUMBER, RECIPE_NAME, DESCRIPTION " +
"FROM RECIPES " +
"WHERE RECIPE_NUMBER = ?";
ResultSet rs = null;
try (PreparedStatement pstmt =
conn.prepareStatement(sql, ResultSet.TYPE_SCROLL_SENSITIVE, ResultSet.CONCUR_UPDATABLE);){
pstmt.setString(1, recipeNumber);
rs = pstmt.executeQuery();
while(rs.next()){
String desc = rs.getString(4);
System.out.println("Updating row" + desc);
rs.updateString(4, desc + " -- More to come");
rs.updateRow();
}
} catch (SQLException ex) {
ex.printStackTrace();
} finally {
if (rs != null){
try {
rs.close();
} catch (SQLException ex) {
ex.printStackTrace();
}
}
}
主方法如下：
public class UpdateResultSetExample {
public static Connection conn = null;
public static void main(String[] args) {
boolean successFlag = false;
try {
CreateConnection createConn = new CreateConnection();
conn = createConn.getConnection();
// 执行初始查询
queryDbRecipes();
// 更新 ResultSet 行
queryAndUpdateDbRecipes("12-1");
// 查询以查看最终结果
queryDbRecipes();
} catch (java.sql.SQLException ex) {
System.out.println(ex);
} finally {
if (conn != null) {
try {
conn.close();
} catch (SQLException ex) {
ex.printStackTrace();
}
}
}
}
```

调用此方法时，可以传递一个包含配方编号的字符串值。假设将配方编号 `"12-1"` 传递给此方法，将得到以下输出结果。

```
Successfully connected
12-1: Installing MySQL - Downloading and installation of a MySQL Database
12-2: Connecting to a Database - DriverManager and DataSource Implementations
12-3: Handling SQL Exceptions - Using SQLException
12-4: Querying a Database and Retrieving Results - Obtaining and using data from a DBMS
Updating row:Downloading and installation of a MySQL Database
12-1: Installing MySQL - Downloading and installation of a MySQL Database -- More to come
12-2: Connecting to a Database - DriverManager and DataSource Implementations
12-3: Handling SQL Exceptions - Using SQLException
12-4: Querying a Database and Retrieving Results - Obtaining and using data from a DBMS
```



### 工作原理

有时你需要在解析数据的同时更新数据。通常，这种技术涉及测试从数据库返回的值，并在与另一个值进行比较后对其进行更新。最简单的方法是通过将 `ResultSet.CONCUR_UPDATABLE` 常量传递给 `Connection` 对象的 `createStatement()` 或 `prepareStatement()` 方法，使 `ResultSet` 对象可更新。这样做会使 `Statement` 或 `PreparedStatement` 生成一个可更新的 `ResultSet`。

注意

某些数据库 JDBC 驱动程序不支持可更新的 `ResultSet`。有关更多信息，请参阅你的 JDBC 驱动程序文档。此代码是在 Oracle 数据库 11.2 版本上使用 Oracle 的 `ojdbc6.jar` JDBC 驱动程序运行的。

创建生成可更新 `ResultSet` 的 `Statement` 对象的格式是：将 `ResultSet` 类型作为第一个参数传递，将 `ResultSet` 并发模式作为第二个参数传递。滚动类型必须为 `TYPE_SCROLL_SENSITIVE`，以确保 `ResultSet` 对任何更新都敏感。以下代码通过创建一个生成可滚动且可更新的 `ResultSet` 对象的 `Statement` 对象来演示此技术。

```
Statement stmt = conn.createStatement(ResultSet.TYPE_SCROLL_SENSITIVE,
ResultSet.CONCUR_UPDATABLE);
```

创建生成可更新 `ResultSet` 的 `PreparedStatement` 对象的格式是：将 SQL 字符串作为第一个参数传递，将 `ResultSet` 类型作为第二个参数传递，将 `ResultSet` 并发模式作为第三个参数传递。本技巧的解决方案通过以下代码行演示了此技术。

```
pstmt = conn.prepareStatement(sql, ResultSet.TYPE_SCROLL_SENSITIVE,
ResultSet.CONCUR_UPDATABLE);
```

本节讨论的两行代码都会生成可滚动且可更新的 `ResultSet` 对象。一旦你获得了可更新的 `ResultSet`，就可以像使用普通 `ResultSet` 一样用它来获取从数据库中检索到的值。此外，你可以调用 `ResultSet` 对象的某个 `updateXXX()` 方法来更新 `ResultSet` 中的任何值。在本技巧的解决方案中，调用了 `updateString()` 方法，将查询中值的位置作为第一个参数传递，将更新后的文本作为第二个参数传递。在此例中，更新的是 SQL 查询中列出的第四个元素列。

```
rs.updateString(4, desc + " -- More to come");
```

最后，要持久化你所更改的值，请调用 `ResultSet` 的 `updateRow()` 方法，如本技巧的解决方案所示。

```
rs.updateRow();
```

创建可更新的 `ResultSet` 并非每天都需要做的事情。事实上，你可能永远都不需要创建可更新的 `ResultSet`。然而，在需要这种策略的情况下，此技术会非常有用。

## 12.11 缓存数据以便在断开连接时使用

### 问题

你希望在断开连接的状态下处理来自数据库管理系统（DBMS）的数据。也就是说，你正在一台未连接到数据库的设备上工作，但仍希望能够像连接时一样处理一组数据。例如，你正在便携式设备上处理数据，并且你离开了办公室，没有网络连接。你希望能够查询、插入、更新和删除数据，即使没有连接。一旦连接可用，你希望你的设备能够同步在断开连接期间所做的任何数据库更改。

### 解决方案

使用 `CachedRowSet` 对象来存储你希望在离线时处理的数据。这允许你的应用程序像连接到数据库一样处理数据。一旦你的连接恢复，或者你重新连接到数据库，将 `CachedRowSet` 中更改的数据与数据库存储库进行同步。以下示例类演示了 `CachedRowSet` 的使用。在此场景中，`main()` 方法执行该示例。不过，假设没有 `main()` 方法，而是便携式设备上的另一个应用程序调用此类的各个方法。请跟随示例中的代码，并考虑在未连接到数据库的情况下处理存储在 `CachedRowSet` 中的结果。例如，假设你在办公室时已连接到网络并开始了一些工作，现在你离开了办公室，网络信号不稳定，无法保持与数据库的持续连接。



```
package org.java17recipes.chapter12.recipe12_10;
import java.sql.Connection;
import java.sql.SQLException;
import javax.sql.rowset.CachedRowSet;
import javax.sql.rowset.RowSetFactory;
import javax.sql.rowset.RowSetProvider;
import javax.sql.rowset.spi.SyncProviderException;
import org.java17recipes.chapter12.recipe12_01.CreateConnection;
public class CachedRowSetExample {
public static Connection conn = null;
public static CreateConnection createConn;
public static CachedRowSet crs = null;
public static void main(String[] args) {
boolean successFlag = false;
try {
createConn = new CreateConnection();
conn = createConn.getConnection();
// 执行可滚动查询
queryWithRowSet();
// 更新 CachedRowSet
updateData();
// 同步更改
syncWithDatabase();
} catch (java.sql.SQLException ex) {
System.out.println(ex);
} finally {
if (conn != null) {
try {
conn.close();
} catch (SQLException ex) {
ex.printStackTrace();
}
}
}
}
/**
* 调用此方法将 CachedRowSet 中使用的数据与数据库同步
*/
public static void syncWithDatabase() {
try {
crs.acceptChanges(conn);
} catch (SyncProviderException ex) {
// 如果同步时发生冲突，将抛出此异常
ex.printStackTrace();
} finally {
// 通过关闭 CachedRowSet 清理资源
if (crs != null) {
try {
crs.close();
} catch (SQLException ex) {
ex.printStackTrace();
}
}
}
}
public static void queryWithRowSet() {
RowSetFactory factory;
try  {
// 创建一个新的 RowSetFactory
factory = RowSetProvider.newFactory();
// 使用工厂创建一个 CachedRowSet 对象
crs = factory.createCachedRowSet();
// 或者填充 CachedRowSet 的连接设置
// crs.setUsername(createConn.getUsername());
// crs.setPassword(createConn.getPassword());
// crs.setUrl(createConn.getJdbcUrl());
// 填充一个查询，用于获取将要使用的数据
crs.setCommand("select id, recipe_number, recipe_name, description from recipes");
// 设置键列
int[] keys = {1};
crs.setKeyColumns(keys);
crs.execute(conn);
// 现在可以在断开连接的状态下处理对象内容
while (crs.next()) {
System.out.println(crs.getString(2) + ": " + crs.getString(3) + " - " + crs.getString(4));
}
} catch (SQLException ex) {
ex.printStackTrace();
}
}
public static boolean updateData() {
boolean returnValue = false;
try {
// 移动到结果集中第一行之前的位置
crs.beforeFirst();
// 遍历结果集
while (crs.next()) {
// 如果 recipe_num 等于 11-2，则进行更新
if (crs.getString("RECIPE_NUMBER").equals("12-2")) {
System.out.println("正在更新配方 12-2");
crs.updateString("description", "Subject to change");
crs.updateRow();
}
}
returnValue = true;
// 移动到结果集中第一行之前的位置
crs.beforeFirst();
// 遍历结果集以查看更改
while (crs.next()) {
System.out.println(crs.getString(2) + ": " + crs.getString(3) + " - " + crs.getString(4));
}
} catch (SQLException ex) {
returnValue = false;
ex.printStackTrace();
}
return returnValue;
}
}
```

运行此示例代码将显示类似于以下代码的输出，但文本可能因数据库中的值而异。请注意，在更新 `CachedRowSet` 后，配方 12-2 的数据库记录描述已更改。

```
成功连接
12-1: 安装 MySQL - MySQL 数据库的下载与安装 -- 更多内容待续
12-2: 连接数据库 - DriverManager 和 DataSource 实现
12-3: 处理 SQL 异常 - 使用 SQLException
12-4: 查询数据库并检索结果 - 从 DBMS 获取和使用数据
正在更新配方 12-2
12-1: 安装 MySQL - MySQL 数据库的下载与安装 -- 更多内容待续
12-2: 连接数据库 - 可能会更改
12-3: 处理 SQL 异常 - 使用 SQLException
12-4: 查询数据库并检索结果 - 从 DBMS 获取和使用数据
```



### 工作原理

在移动设备上旅行时，不可能始终保持互联网连接。如今，有些设备允许你在移动中完成大量工作，即使没有直接连接到数据库。在这种情况下，像 `CachedRowSet` 对象这样的解决方案就可以派上用场。`CachedRowSet` 与常规的 `ResultSet` 对象相同，区别在于它无需维持与数据库的连接即可保持可用状态。你可以查询数据库、获取结果，并将它们放入 `CachedRowSet` 对象中；然后在未连接数据库的情况下处理这些数据。如果数据在任何时候发生了更改，这些更改稍后可以与数据库同步。

创建 `CachedRowSet` 有几种方法。本方案使用 `RowSetFactory` 来实例化一个 `CachedRowSet`。不过，你也可以使用 `CachedRowSet` 的默认构造函数来创建新实例。具体代码如下所示。

```
CachedRowSet crs = new CachedRowSetImpl();
```

实例化后，你需要建立与数据库的连接。同样有几种方法可以实现这一点。可以设置所用连接的属性，本方案在注释中演示了这种技术。以下摘自方案的代码片段使用 `CachedRowSet` 对象的 `setUsername()`、`setPassword()` 和 `setUrl()` 方法设置连接属性。每个方法都接受一个字符串值，在示例中，该字符串从 `CreateConnection` 类获取。

```
// 或者填充 CachedRowSet 的连接设置
// crs.setUsername(createConn.getUsername());
// crs.setPassword(createConn.getPassword());
// crs.setUrl(createConn.getJdbcUrl());
```

另一种设置连接的方法是等到执行查询时，将 `Connection` 对象传递给 `executeQuery()` 方法。这是本方案中使用的方法。但在执行查询之前，必须使用 `setCommand()` 方法设置查询，该方法接受一个字符串值。在本例中，该字符串是你需要执行的 SQL 查询。

```
crs.setCommand("select id, recipe_number, recipe_name, description from recipes");
```

接下来，如果 `CachedRowSet` 用于更新，则应使用 `setKeys()` 方法记录主键值。该方法接受一个包含键列位置索引的 int 数组。这些键用于标识唯一列。在本例中，查询中列出的第一列 `ID` 是主键。

```
int[] keys = {1};
crs.setKeyColumns(keys);
```

最后，使用 `execute()` 方法执行查询并填充 `CachedRowSet`。如前所述，`execute()` 方法可选地接受一个 `Connection` 对象，这允许 `CachedRowSet` 获取数据库连接。

```
crs.execute(conn);
```

一旦查询执行完毕，`CachedRowSet` 被填充后，它就可以像任何其他 `ResultSet` 一样使用。你可以使用它向前或向后获取记录，或者通过指定要检索行的绝对位置来获取。

可以在 `CachedRowSet` 中插入和更新行。要插入行，请使用 `moveToInsertRow()` 方法移动到新的行位置。然后使用与行中正在填充的列数据类型相对应的各种方法（`CachedRowSet`、`updateString()`、`updateInt()` 等）来填充一行。填充完行中所有必需的列后，调用 `insertRow()` 方法，接着调用 `moveToCurrentRow()` 方法。以下代码行演示了向 `RECIPES` 表中插入一条记录。

```
crs.moveToInsertRow();
crs.updateInt(1, sequenceValue); // 通过先前的查询获取当前序列值
crs.updateString(2, "12-x");
crs.updateString(3, "This is a new recipe title");
crs.insertRow();
crs.moveToCurrentRow();
```

更新行类似于使用可更新的 `ResultSet`。只需使用 `CachedRowSet` 对象中与行中正在更新的列数据类型相对应的方法 [`updateString()`、`updateInt()` 等] 来更新值。更新完行中的一列或多列后，调用 `updateRow()` 方法。本方案中演示了这种技术。

```
crs.updateString("description", "Subject to change");
crs.updateRow();
```

要将任何更新或插入传播到数据库，必须调用 `acceptChanges()` 方法。此方法可以接受一个可选的 `Connection` 参数来连接数据库。调用后，所有更改都会刷新到数据库。不幸的是，由于自上次为 `CachedRowSet` 检索数据以来可能已经过了一段时间，因此可能会出现冲突。如果发生此类冲突，则会抛出 `SyncProviderException`。你可以捕获这些异常，并使用 `SyncResolver` 对象手动处理冲突。但是，解决冲突超出了本方案的范围。

有关更多信息，请参阅 [`http://download.oracle.com/javase/tutorial/jdbc/basics/cachedrowset.html`](http://download.oracle.com/javase/tutorial/jdbc/basics/cachedrowset.html) 上的文档。

`CachedRowSet` 对象为处理数据提供了极大的灵活性，尤其是在使用并非始终连接到数据库的设备时。但是，在可以简单地使用标准 `ResultSet` 甚至可滚动的 `ResultSet` 的情况下，它们也可能显得大材小用。

## 12.12 获取用于数据库的日期

### 问题

你想要正确地将 `LocalDate` 对象转换后插入到数据库记录中。

### 解决方案

利用静态方法 `java.sql.Date.valueOf(LocalDate)` 将 `LocalDate` 对象转换为 `java.sql.Date` 对象，JDBC 可以使用该对象进行数据库的插入或查询。在以下示例中，当前日期被插入到类型为 `Date` 的数据库列中。

```
public class DatesTimes {
static CreateConnection createConn = new CreateConnection();
public static void main(String[] args) {
insertRecord(
"Java 17 Recipes",
"APRESS");
}
private static void insertRecord(
String title,
String publisher) {
String sql = "INSERT INTO PUBLICATION VALUES("
+ "null, ?,?,?)";
LocalDate pubDate = LocalDate.now();
try (Connection conn = createConn.getConnection();
PreparedStatement pstmt = conn.prepareStatement(sql);) {
pstmt.setString(1, title);
pstmt.setDate(2,  java.sql.Date.valueOf(pubDate));
pstmt.setString(3, publisher);
pstmt.executeUpdate();
System.out.println("Record successfully inserted.");
} catch (SQLException ex) {
ex.printStackTrace();
}
}
}
```

### 工作原理

在 Java 8 中，新的日期时间 API（第 4 章）是处理日期和时间的首选 API。因此，在处理日期值和数据库时，JDBC API 必须在 SQL 日期和新的日期时间 `LocalDate` 对象之间进行转换。本方案的解决方案演示了要从 `LocalDate` 对象获取 `java.sql.Date` 实例，只需调用静态方法 `java.sql.Date.valueOf()`，并传入相关的 `LocalDate` 对象即可。

## 12.13 自动关闭资源

### 问题

你希望应用程序为你处理此类样板代码，而不是在每次数据库调用时手动打开和关闭资源。



### 解决方案

使用 `try-with-resources` 语法自动关闭已打开的资源。以下代码块在完成资源使用后，利用此策略自动关闭 `Connection`、`Statement` 和 `ResultSet` 资源。

```
public class TryWithResourcesExample {
public static CreateConnection createConn;
public static void main(String[] args) {
createConn = new CreateConnection();
queryDatabase();
}
public static void queryDatabase() {
String qry = "select recipe_number, recipe_name, description from recipes";
try (Connection conn = createConn.getConnection();
Statement stmt = conn.createStatement();
ResultSet rs = stmt.executeQuery(qry);) {
while (rs.next()) {
String recipe = rs.getString("RECIPE_NUMBER");
String name = rs.getString("RECIPE_NAME");
String desc = rs.getString("DESCRIPTION");
System.out.println(recipe + "\t" + name + "\t" + desc);           }
} catch (SQLException e) {
e.printStackTrace();
}
}
}
```

运行此代码的输出结果应类似于以下内容。

```
Successfully connected
12-1 Connecting to a Database DriverManager and DataSource Implementations - More to Come
12-2 Querying a Database and Retrieving Results Subject to Change
12-3 Handling SQL Exceptions Using SQLException
```

### 工作原理

Java 7 引入了使用 `try-with-resources` 的自动资源管理。通过这种技术，开发者不再需要手动关闭每个资源，这一改变可以减少许多行代码。

要使用此技术，您必须在 `try` 子句后的一对圆括号内实例化所有希望启用自动处理的资源。在本方案的解决方案中，声明的资源是 `Connection`、`Statement` 和 `ResultSet`。

```
try (Connection conn = createConn.getConnection();
Statement stmt = conn.createStatement();
ResultSet rs = stmt.executeQuery(qry);) {
```

一旦这些资源超出作用域，它们就会被自动关闭。这意味着不再需要编写 `finally` 块来确保资源被关闭。自动资源处理适用于数据库操作以及任何符合新 `java.lang.Autocloseable` API 的资源。其他操作，如文件 I/O，也遵循新的 API。`java.lang.Autoclosable` 中有一个单一的 `close()` 方法来管理资源的关闭。实现了 `java.io.Closeable` 接口的类可以遵循此 API。

## 12.14 总结

在许多应用程序中，数据库已成为存储重要信息的关键。充分理解如何在应用程序中使用数据库至关重要。本章从基础开始，涵盖了数据库访问入门的方案。接着讨论了重要主题，如安全访问和修改数据、事务管理以及离线时的数据访问。现在，您应该对在 Java 解决方案中处理数据的一些技术有了扎实的理解。请记住，数据访问解决方案有很多，本章中的方案仅涵盖了应对信息管理这一难题的部分方法。

