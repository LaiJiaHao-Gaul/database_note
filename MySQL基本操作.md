# MySQL基本操作

## 一、数据库模式定义

数据库模式的定义包括数据库的创建、选择、修改、删除、查看等操作。

“ [ ] ”
标示内容为可选项，也就是可省略的。

“ | ”
表示可任选其中一项来与花括号外的语法成分共同组成 SQL 语句命令，意为 或。

“ db_name ”
标识数据库名 在MySQL中不区分大小写。

“ DEFAULT ” 关键字
指定默认值

“ CHARACTER SET” 关键字
指定数据库字符集（Charset）

“ COLLATE ” 关键字
指定字符集的校对规则

“ IF NOT EXISTS ” 关键字
用于在创建数据库钱进行判断，只有该数据库目前尚不存在时才执行 CREATE DATABASE 操作，可避免数据库存在而再次新建的错误。

### 1.创建数据库

#### CREATE DATABASE 或 CREATE SCHEMA 语句

格式：

```mysql
CREATE {DATABASE|SCHEMA}[IF NOT EXISTS] db_name
[DEFAULT] CHARACTER SET [=] charset_name
|[DEFAULT] COLLATE[=] collation_name
```

例：

```mysql
mysql> CREATE DATABASE nunuDB;
Query OK,1 row effected(0.01 sec)
```

如果再次使用一样的操作创建同一个数据库，则会报错，
但加上IF NOT EXISTS则可避免错误的发生。

### 2.选择数据库

#### USE DATABASE

例：

```mysql
USE nunuDB;
```

### 3.修改数据库

#### ALTER DATABASE 语句 或 ALTER SCHEMA 语句

```MYSQL
ALTER {DATABASE|SCHEMA}[db_name]
```

例：修改已有数据库 nunuDB 的默认字符集和校对规则。

```mysql
mysql> ALTER DATABASE mysql_test
-> DEFAULT CHARACTER SET gb2312
-> DEFAULT COLLATE gb2312_chinese_ci;
```

### 4.删除数据库

#### DROP DATABASE 语句 或 DROP SCHEMA 语句

格式：

```MYSQL
DROP { DATABASE | SCHEMA } [IF EXISTS] db_name
```

例：

```MYSQL
mysql> DROP DATABASE nunuDB;
```

IF EXISTS 可以避免删除不存在的数据库时出现的 MySQL 错误信息。

### 5.查看数据库

#### SHOW DATABASES 语句 或 SHOW CHEMAS 语句查看可用数据库列表（基于用户权限范围）

格式：

```MYSQL
SHOW { DATABASES | SCHEMAS }
[LIKE 'PATTERN'| WHERE expr]
```

在此语法中：可选项“ LIKE ”关键字用于匹配指定的数据库名称；可选项“WHERE”从句用于指定数据库名称查询范围的条件。

例：

```mysql
mysql> SHOW DATABASES;
```

## 二、表定义

### 1.创建表

#### CREATE TABLE 语句

该语句的语法内容较多，主要由表创建定义（create definition）、表选项（table options）和分区选项（partition options）等内容所构成。

表创建定义包含：表列的名字、列的定义、以及可能的一个空值声明、一个完整性约束或表索引项等。

表索引项定义：表的索引、主键、外键等。

实际应用时，表定义均被括在圆括号中，各列彼此间用逗号分隔，每列的定义以列名（在该表中必须是唯一的）开始，后跟列的数据类型以及可选参数。

语法格式：

```mysql
CREATE [TEMPORARY] TABLE tb1_name
(
字段名1 数据类型 [列级完整性约束条件][默认值]
[,字段名2 数据类型 [列级完整性约束条件][默认值]]
[,...]
[,表级完整性约束条件]
)[ENGINE=引擎类型];
```

例如：在一个已有数据库 nunuDB 中新建一个包含用户姓名、性别、年龄等内容的用于基本信息表，要求将客户的id号指定为该表的主键。

则MySQL命令行客户端输入如下：

```MySQL
mysql> USE mysql_test;
Database changed
mysql> CREATE TABLE customers
    -> (
    -> user_id INT NOT NULL AUTO_INCREMENT,
    -> user_name CHAR(50) NOT NULL,
    -> user_sex CHAR(1) NOT NULL DEFAULT 0,
    -> user_age INT NOT NULL,
    -> PRIMARY KEY(user_id)
    -> );
Query OK, 0 orws affexted(0.01 sec)
```

### 临时表与持久表

#### TEMPORARY 关键字 临时表

使用该关键字则创建临时表，不使用则创建持久表
临时表和持久表的区别在于临时表的生命周期较短，而且只能对创建它的用户可见，当断开与该数据库的连接时，MySQL会自动删除临时表，这意味着两个不同的连接可以使用相同的临时表名称，不会冲突，也不会与同名的非临时表冲突。

### 数据类型

MySQL中主要的数据类型包括：
数值类型： 整型int、浮点型double、 布尔型bool。
日期和时间类型： 日期型data、时间戳timestamp、时间型time。
字符串类型： 定长字符串类型char、可变长字符类型varchar。
空间数据类型等。

#### AUTO_INCREMENT 关键字 自增属性

在CREATE TABLE语句中，使用“AUTO_INCREMENT”，可以为表中数据类型为整型的列设置**自增属性**，从而能实现当插入 NULL 值或数字 0 到一个 AUTO_INCREMENT 列中时，该列的值会被自动设置为“此前表中该列的最大值加 1”。

注意：**每个表只能有一个 AUTO_INCREMENT 列，并且它必须被索引**。

### 默认值

#### DEFAULT 关键字

这个关键字是在 CREATE TABLE 语句的列定义中的。
如果没有为列指定默认值，MySQL会自动地为其分配一个。如果该列可以去NULL值，则默认值为NULL，而如果该列被设置为NOT NULL，则默认值取决于该列的类型：对于没有声明 AUTO_INCREMENT 属性的数字类型，默认值是 0 ；对于一个AUTO_INCREMENT列，默认值是在顺序中的下一个值；对于除 TIMESTAMP 以外的日期和时间类型，默认值是该类型适当的“零”值；对于表中第一个TIMESTAMP，默认值是当前的日期和时间。

#### NULL 关键字

在MySQL中，每个列要么是NULL列，要么是NOT NULL列，这种状态在创建时由表的定义所规定。NULL为默认设置，如果不指定NOT NULL，则认为指定的是NULL.

注意：**NULL!=''**

### 主键

#### PRIMARY KEY 关键字

在CREATE TABLE语句中，主键是通过PRIMARY KEY 关键字来指定的。

## 2.更新表

通过在 ALTER TABLE 语句中使用不同的子句，分别介绍在MySQL数据库中更新表的相关操作。

### ADD [COLUNMN] 子句

如果需要向表中增加新列，可在 ALTER TABLE 语句中添加 ADD[COLUMN]子句来实现，并且该子句可同时增加多个列。

```MySQL
mysql> ALTER TABLE nunuDB.try
    -> ADD COLUMN user_address char(50) NOT NULL DEFAULT 'SZ'
```

#### CHANGE [COLUMN] 子句

如果需要修改表中列的名称或数据类型，可在 ALTER TABLE 语句中添加 CHANGE[COLUMN] 子句。
CHANGE[COLUMN]子句可同时修改表中指定列的名称和数据类型，且在ALTER TABLE 语句中可以同时放入多个CHANGE [COLUMN] 子句，只需彼此间用逗号分隔。

例：

```MySQL
mysql> ALTER TABLE nunuDB.try
    -> CHANGE COLUMN user_sex sex char(1) NULL DEFAULT 'M';
Query OK, 0rows affected(0.01 sec)
Records:0   Duplicates: 0   Warnings:0
```

#### ALTER [COLUMN] 子句

如果需要**修改或删除表中指定列的默认值**，可在 ALTER TABLE 语句中添加 ALTER [COLUMN] 子句。

例：

```MySQL
mysql> ALTER TABLE nunuDB.try
    -> ALTER COLUMN user_address SET DEFAULT 'Beijing'
Query OK, 0 rows affected(0.36 sec)
Records: 0   Dupicates: 0   Warnings: 0
```

#### MODIFY [COLUMN] 子句

与 CHANGE [COLUMN] 子句有所不同，在 ALTER TABLE 语句中添加 MODIFY [COLUMN]子句只会**修改指定列的数据类型，而不会干涉它的列名**。该子句还可以通过关键字“FIRST”或“AFTER”**修改指定列在表中的位置**

例：

```MySQL
mysql> ALTER TABLE nunuDB.try
    -> MODIFY COLUMN user_name char(20) FIRST;
Query OK, 0 rows affected(0.02 sec)
Record: 0   Duplicates: 0    Warnings: 0
```

#### DROP [COLUMN]子句 删除指定列

例：

```MySQL
mysql> ALTER TABLE nunuDB.try
    -> DROP COLUMN user_address
```

#### DROP PRIMARY KEY 子句 删除主键

#### DROP FOREIGN KEY 子句 删除外键

#### DROP INDEX 删除索引

### 3.重命名表

#### RENAME TABLE 语句

更改表的名字，可同时重命名多个表，语法格式：

```MySQL
RENAME TABLE nunuDB TO nunuDatabase
[,table2 TO new_table2] ...
```

例：

```MySQL
mysql> RENAME TABLE nunuDB TO new_nunuDB
Query OK,0 rows affected (1 sec)
```

### 4.删除表

#### DROP TABLE 语句

语法格式：

```MySQL
DROP [TEMPORARY] TABLE [IF EXISTS]
    tb1_name [,tb1_name] ...
    [RESTRICT|CASCADE]
```

### 5.查看表

#### SHOW TABLES 语句

功能：**显示表的名字**

#### SHOW COLUMNS 语句

功能：**显示表的结构**

显示表的结构还有另一个语句 **DESCRIBE**

语法格式：

```MySQL
{DESCRIBE|DESC} tb1_name [col_name|wild]
```

例：

```MySQL
mysql> DESC nunuDB.try;
```

## 三、索引定义

### 1.索引的创建

三种方式创建索引：

（1）使用CREATE INDEX 语句创建索引
可以使用专门用与创建索引的 CREATE INDEX 语句在一个已有的表上创建索引，但该语句不能创建主键。
语法：

```MySQL
CREATE [UNIQUE] INDEX index_name
    ON tb1_name(index_col_name,...)
```

其中，index_col_name 的格式为：

```MySQL
col_name [(length)[ASC|DESC]]
```

可选项 “UNIQUE” 关键字用于指定创建唯一性索引
“index_name”用于指定索引名。
注意：**一个表可以创建多个索引，但每个索引在该表中的名称必须是唯一的；**
tb1_name 用于指定要建立索引的表名，
index_col_name 是关于索引列的描述。

关于索引的描述可包含三个语法要素：
col_name指定要创建索引的列名，通常可考虑将查询语句中在 WHERE 子句和 JION 子句里出现的列来作为索引列；
可选项“length”，用于指定使用列的前 length 个字符来创建索引，使用列的一部分创建索引有利于减小索引文件的大小，节省磁盘空间；
关键字“ASC”或“DESC”是可选项，用于指定索引按升序（ASC）还是降序（DESC）来排列，默认时为ASC。

例：在数据库 mysql_test 的表 customers 上，根据客户姓名列的前三个字符创建一个升序索引 index_customers。

```MySQL
mysql> CREATE INDEX index_customers
    ->    ON mysql_test.customers(cust_name(3) ASC);
```

例：在数据库 mysql_test 的表 customers 上，根据客户姓名列和客户 id 号 创建一个组合索引 index_cust.

```MySQL
mysql> CREATE INDEX index_cust
    ->      ON mysql_test.customers(cust_name,cust_id);
Query OK, 0 rows affected(0.20 sec)
```

第二种创建索引的方法：
（2）使用 CREATE TABLE 语句创建索引
索引可以在创建表的同时一起被创建。具体使用方法时，可在 CREATE TABLE 语句语法中的表创建定义（create difinition）部分添加以下语法成分中的某一项或几项：
1、语法项[CONSTRAINT [symbol]] PRIMARY KEY (index_col_name,...),用于表示在创建新表的同时创建该表的主键；
2、语法项{INDEX|KEY} [index_name] (index_col_name,...),用于表示在创建信标的同时创建该表的索引。
3、语法项[CONSTRAINT [symbol]] UNIQUE [INDEX|KEY] [index_name] (index_col_name,...),用于表示在创建新表的同时创建该表的唯一性索引。
4、语法项[CONSTRAINT[symbol]]FOREIGN KEY [index_name] (index_col_name,...),用于表示在创建新表的同时创建该表的外键。

第三种创建索引的方法
（3）使用ALTER TABLE语句创建索引
ALTER在修改表的同时，可以向已有的表中添加索引。具体使用方法时，可在ALTER TABLE语句语法中添加以下语法成分中的某一项或几项。
1、语法项 ADD {INDEX | KEY} [index_name] (index_col_name,...)用于表示在修改表的同时为该表添加索引；
2、语法项 ADD [CONSTRAINT [symbol]] PRIMARY KEY (index_col_name,...),用于表示在修改表的同时为该表添加主键(ADD PRIMARY KEY(index_col_name,...))
3、语法项 ADD [CONSTRAINT [symbol]]UNIQUE [INDEX|KEY] [index_name] (index_col_name,..),用于表示在修改表的同时为该表添加外键。
4、语法项 ADD [CONSTRAINT [symbol]]FOREIGN KEY [index_ name] (index_col_name,...), 用于表示在修改表的同时为该表添加外键。

例： 使用 ALTER TABLE 语句在数据库 mysql_test 中表 seller 的姓名列上添加一个非唯一的索引，取名为 index_seller_name 。

在 MYSQL的命令行客户端上输入如下SQL语句即可：

```MySQL
mysql> ALTER TABLE mysql_test.seller
    ->  ADD INDEX index_seller_name(seller_name);
```

### 查询某个表已建立的索引

#### SHOW INDEX 语句

格式：
```MySQL
SHOW {INDEX|INDEXES|KEYS}
    {FROM|IN}tb1_name
    [{FROM|IN} db_name]
    [WHERE expr]
```

### 删除索引

删除索引有两种方法

第一种删除索引的方法：
使用 DROP INDEX 语句删除索引
格式：

```MySQL
DROP INDEX index_name ON tb1_name
```

第二种删除索引的方法：
使用 ALTER TABLE 语句删除索引
ALTER TABLE 语句也可以用于对索引的删除，使用方法是，在 ALTER TABLE 语句中指定以下子句中的某一项：
i)选用 DROP PRIMARY KEY 子句用于删除表中的主键，由于一个表中只有一个主键，其也是一个索引；
ii)选用 DROP INDEX 子句用于删除各种类型的索引；
iii)选用 DROP FOREIGN KEY 子句用于删除外键。

例： 使用 ALTER TABLE 语句删除数据库 mysql_test 中表 customers 的主键和索引 index_customers。

```MySQL
mysql>ALTER TABLE mysql_test.customers
    ->  DROP PRIMARY KEY,
    ->  DROP INDEX index_customers;
```
