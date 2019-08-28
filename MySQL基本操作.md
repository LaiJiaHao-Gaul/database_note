# MySQL基本操作

## 一、数据库模式定义

数据库模式的定义包括数据库的创建、选择、修改、删除、查看等操作。

“ [ ] ”

标示内容为可选项，也就是可省略的。

“ | ”

表示可任选其中一项来与花括号外的语法成分共同组成 SQL 语句命令，意为 或。

“ db_name ”

表示数据库名 在MySQL中不区分大小写。

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

如果再次使用一样的操作创建同一个数据库，则会报错，但加上IF NOT EXISTS则可避免错误的发生。

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
CREATE [TEMPORARY] TABLE tbl_name
(
字段名1 数据类型 [列级完整性约束条件][默认值]
[,字段名2 数据类型 [列级完整性约束条件][默认值]]
[,···]
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

在MySQL中，每个列要么是NULL列，要么是NOT NULL列，这种状态在创建时由表的定义所规定。

NULL为默认设置，如果不指定NOT NULL，则认为指定的是NULL.

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
[,table2 TO new_table2] ···
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
    tbl_name [,tbl_name] ···
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
{DESCRIBE|DESC} tbl_name [col_name|wild]
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
    ON tbl_name(index_col_name,···)
```

其中，index_col_name 的格式为：

```MySQL
col_name [(length)[ASC|DESC]]
```

可选项 “UNIQUE” 关键字用于指定创建唯一性索引

“index_name”用于指定索引名。

注意：**一个表可以创建多个索引，但每个索引在该表中的名称必须是唯一的；**

tbl_name 用于指定要建立索引的表名，

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

1、语法项[CONSTRAINT [symbol]] PRIMARY KEY (index_col_name,···),用于表示在创建新表的同时创建该表的主键；

2、语法项{INDEX|KEY} [index_name] (index_col_name,···),用于表示在创建信标的同时创建该表的索引。

3、语法项[CONSTRAINT [symbol]] UNIQUE [INDEX|KEY] [index_name] (index_col_name,···),用于表示在创建新表的同时创建该表的唯一性索引。

4、语法项[CONSTRAINT[symbol]]FOREIGN KEY [index_name] (index_col_name,···),用于表示在创建新表的同时创建该表的外键。

第三种创建索引的方法

（3）使用ALTER TABLE语句创建索引

ALTER在修改表的同时，可以向已有的表中添加索引。具体使用方法时，可在ALTER TABLE语句语法中添加以下语法成分中的某一项或几项。

1、语法项 ADD {INDEX | KEY} [index_name] (index_col_name,···)用于表示在修改表的同时为该表添加索引；

2、语法项 ADD [CONSTRAINT [symbol]] PRIMARY KEY (index_col_name,···),用于表示在修改表的同时为该表**添加主键**(ADD PRIMARY KEY(index_col_name,···))

3、语法项 ADD [CONSTRAINT [symbol]]UNIQUE [INDEX|KEY] [index_name] (index_col_name,..),用于表示在修改表的同时为该表添加外键。

4、语法项 ADD [CONSTRAINT [symbol]]FOREIGN KEY [index_ name] (index_col_name,···), 用于表示在修改表的同时为该表添加外键。

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
    {FROM|IN}tbl_name
    [{FROM|IN} db_name]
    [WHERE expr]
```

### 删除索引

删除索引有两种方法

第一种删除索引的方法：
使用 DROP INDEX 语句删除索引
格式：

```MySQL
DROP INDEX index_name ON tbl_name
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

## 四、数据更新

### 插入数据

#### INSERT 语句

向数据库中一个已有的表插入一行或多行元组数据。

INSERT 语句有三种语法形式，分别对应的是：

INSERT ··· VALUES 语句

INSERT ··· SET 语句

INSERT ··· SELECT 语句

在 MySQL 中，使用 INSERT···VALUES 语句插入单行或多行元组数据的语法格式是：

```MySQL
INSERT [INTO] tbl_name [(col_name,···)]
    {VALUES|VALUE} ({expr|DEFAULT},···),(···),···
```

此语法中：

tbl_name 指定欲被插入数据的表名。

col_name 指定需要插入数据的列名。如果向表中所有列插入数据，则全部列名都可以省略

对于没有指定数据的列，它们的值可根据列的默认值或相关属性来确定，通常MySQL是按照以下原则进行的：

i)对于具有标志（IDENTITY）属性的列，系统会自动生成序号之来唯一标志该列；

ii)具有默认值的列，其值可通过在 INSERT 语句中指定关键字“DEFAULT”将其设为默认值；

iii)没有默认值的列，若允许为空值，则其值可通过在 INSERT 语句中指定关键字“NULL”将其设为空值，若不允许为空值，则INSERT语句执行出错；

iv)对于类型为 TIMESTAMP 的列，系统会为其自动赋值；

v)由于 AUTO_ INCREMENT 属性列的值是在表中其他列被赋值之后生成的，所以在对表中其他列做任何赋值操作（如 INSERT 语句）时，对该AUTO_INCREMENT 属性列的引用只会返回数字 0。

3）通过关键字“VALUES”或“VALUE”引导的子句，其包含各列需要插入的数据清单。数据清单中数据的顺序必须与列的顺序相对应，同时该子句中的值可以是：

i）“expr”，表示一个常量、变量或一个表达式，也可以是空值NULL，其值的数据类型要与列的数据类型一只，如果表达式的类型与列值不匹配，会造成类型转换或插入语句出错，另外当列值为字符型时，需要用单引号括起；

ii）关键字“DEFAULT”，即用于指定此列值为该列的默认值，前提是该列之前已经明确指定了默认值，否则插入语句会报错。

例：使用 INSERT ··· VALUES 语句相数据库 nunuDB 的表 user 中插入数据：

user_name = nunu

user_age = 18

user_sex = 0

则MySQL输入如下：

```MySQL
mysql> INSERT nunuDB.user(user_name,user_age,user_sex)
    -> VALUES('nunu',18,1)
```

第三个插入数据的方法会更加**灵活且常用**

#### INSERT···SELECT

语法格式：

```MySQL
INSERT tbl_name [(col_name,···)]
SELECT ···
```

在此语法中，SELECT 子句用于快速地从一个或多个表中取出数据，并将这些数据作为行数据插入到另一个表中，SELECT 子句返回的是一个查询到的结果集，INSERT语句将这个结果集插入到指定表中，其中结果集里每行数据的字段数、字段的数据类型必须与被操作的表完全一致。

### 删除数据

#### DELETE 语句

语法格式：

```MySQL
DELETE FROM tbl_name
    [WHERE where_condition]
    [ORDER BY ···]
    [LIMIT row_count]
```

在此语法中：

“tbl_name”指定要删除数据的表名；

可选项 WHERE 子句表示为删除操作限定删除条件，从而删除特定的行，若省略 WHERE 子句，则表示删除该表中的所有行，但表的定义仍在数据字典中，即 DELETE 语句删除的是表中的数据，而不是关于表的定义；

可选项 ORDER BY 子句表示各行将按照子句中指定的顺序进行删除；

可选项 LIMIT 子句用于告知服务器在控制命令被返回到客户端前被删除的行的最大值。

例：使用DELETE语句删除数据库 nunuDB 的表 user 中客户名为 "nunu" 的客户信息。

```MySQL
mysql> DELETE FROM nunuDB.user
    ->      WHERE user_name = 'nunu'
```

### 修改数据

在MySQL中，可以使用 UPDATE 语句来修改更新一个表中的数据，实现对表中行的列数据进行修改，其语法格式是：

```MySQL
UPDATE tbl_name
    SET col_name1={expr1|DEFAULT}[,col_name2={expr2|DEFAULT}] ···
    [WHERE where_condition]
    [ORDER BY ···]
    [LIMIT row_count]
```

在此语法中：

“tbl_name"指定要修改的表的名称；

SET 子句用于指定表中要修改的列名及其列值，其中每个指定的列值可以是表达式，也可以是该列所对应的默认值，如果指定的是默认值，则用关键字”DEFAULT“表示列值；

可选项 WHERE 子句用于限定表中要修改的行，若不指定此子句，则 UPDATE 语句会修改表中所有的行；

可选项 ORDER BY 子句用于限定表中的行被修改的次序；可选项LIMIT子句用于限定被修改的行数。

例： 使用 UPDATE 语句将数据库 nunuDB 的表 user 中姓名为 ljh 的客户的年龄更新为23.

命令行输入如下：

```MySQL
mysql>UPDATE nunuDB.user
    ->SET user_age = 23
    ->WHERE user_name = 'ljh';
```

注意：使用 UPDATE 语句可同时修改数据行的多个列值，只需其彼此间用逗号分隔即可；如若删除表中某个列的值，在允许该列为空值的前提下，可将它设置为NULL来实现。

## 五、数据查询

### SELECT语句

SELECT语句的语法格式：

```MySQL
SELECT
[ ALL | DSITINCT | DISTINCTROW ]
select_expr[,select_expr]
FROM table_references
[WHERE where_condition]
[GROUP BY {col_name | expr | position }
[ASC|DESC], ··· [WITH ROLLUP]]
[HAVING where_condition]
[ORDER BY { col_name | expr | position }
    [ASC | DESC],···]
[LIMIT {[offset,] row_count|row_count OFFSET offset}]
```

在此语法结构中：

SELECT 子句用于指定输出的字段；

FROM 子句用于指定数据的来源

WHERE 子句用于指定组的选择条件

GROUP BY 子句用于对检索到的记录进行分组

HAVING 子句用于指定组的选择条件

ORDER BY 子句用于对查询的结果进行排序。

以上子句中，SELECT 子句和 FROM 子句是必需的，其他子句都是可选的，并且在 SELECT 语句的使用中，所有被添加选用的子句必须依照SELECT语句的语法格式所罗列的顺序来使用，

子句|说明|是否必须使用
---|:--:|---:
SELECT|要返回的列或表达式|是
FROM  |从中检索数据的表|仅在从表选择数据时使用
WHERE |行级过滤       | 否
GROUP BY |分组说明    | 仅在按组计算聚合时使用
HAVING|组级过滤       | 否
ORDER BY |输出排序顺序| 否
LIMIT | 要检索的行数  |否

在SELECT语句的语法结构中，三个关键字“ALL”“DISTINCT”“DISTINC TROW”为可选项，用于指定是否应返回结果集里的重复行。若没有指定则为ALL，即SELECT操作中所有匹配的行，包括可能存在的重复行，都将被返回；若指定DISTINCT或DISTINCETROW，则会消除结果集里的重复行，其中 DISTINT 或 DISTINCETROW 为同义词，且这两个关键字应用于 SELECT 语句中所指定的所有列，而不仅仅是前置某个列。

在SELECT语句中，语法项 “select_expr” 主要用于指定需要查询的内容，指定方法有如下几种：

#### 1.选择指定的列

查询多个时，列名之间用逗号分隔，查询结果返回时，结果集里各列的次序是按照SELECT语句中指定列的次序给出的；

查询一个表中的所有列，则用*号通配符，结果集里各列的次序是按照这些列在表定义中出现的次序。

列名的指定可以采用直接给出该列的名称的方式，也可以使用完全限定的列名方式，如“user.user_name”的列名格式

例：查询数据库 nunuDB 的表 user 中各个客户的姓名、性别和年龄。

```MySQL
mysql> SELECT user_name,user_sex,user_age
    -> FROM nunuDB.user;
```

例：查询数据库 nunuDB 的表 user 中各个客户的所有信息。

```MySQL
mysql> SELECT *
    -> FROM nunuDB.user
```

#### 2.定义并使用列的别名 AS

语法格式：

```MySQL
column_name [AS] 别名
```

例如：查询数据库 nunuDB 的表 user 中客户的 user_name、user_sex、user_age，要求将结果集的user_name列 的名称使用别名“姓名”替代。

```MySQL
mysql> SELECT user_name AS 姓名,user_sex,user_age
    -> FROM nunuDB.user
```

#### 3.替换查询结果集里的数据 CASE

在对表进行查询时，若希望得到对某些列的查询分析结果，而不是由查询得到的原始具体数据，则可以在SELECT语句中替换这些列，其中需要用到CASE表达式。

语法格式：

```MySQL
CASE
WHEN 条件1 THEN 表达式1
WHEN 条件2 THEN 表达式2
···
ELSE 表达式
END [AS] column_alias
```

例：查询数据库 nunuDB 的表 user 中客户的 user_name 列 和 user_sex 列，要求判断结果集里 user_sex 列的值，如果该列的值为 1，则显示输出“男”，否则为“女”，同时在结果集里的显示中将user_sex列用别名“性别”标注。

```MySQL
mysql> SELECT user_name 姓名,
    -> CASE
    -> WHEN user_sex = 1 THEN '男'
    -> ELSE '女'
    -> END 性别
    -> FROM nunuDB.user;
```

#### 4.计算列值

使用 SELECT 语句对列进行查询时，在结果集里可以输出对列值计算后的值。
其具体使用方法是：将 SELECT 语句的语法项“select_expr”指定为对应列参与计算的表达式。

例： 查询数据库 nunuDB 的表 user 中 每个客户的 user_name 列、user_sex 列，以及对user_id列加上数字100后的值。

```MySQL
mysql> SELECT user_id + 100 用户ID,user_name 姓名,user_sex 性别，
    -> FROM nunuDB.user;
```

5.聚合函数

SELECT 语句的语法项“select_expr”也可以指定为聚合函数。

聚合函数通常是数据库系统中一类内置函数，常用于对一组值进行计算，然后返回单个值。

它通常与 GROUP BY 子句一起使用。

如果 SELECT 语句中有一个 GROUP BY 子句，则这个聚合函数对所有列起作用。

如果没有，则SELECT语句只产生一行作为结果。另外，除COUNT函数外聚合函数都会忽略空值。

以下是一些常用的聚合函数

函数名|说明
---|:--:|
COUNT|求组中项数，返回INT类型整数
MAX |求最大值
MIN |求最小值
SUM |返回表达式中所有值的和
AVG |求组中值的平均值
STD或STDDEV |返回给定表达式中所有值的标准值
VARIANCE| 返回给定表达式中所有值得方差
GROUP_CONCAT | 返回由属于一组得列值连接组合而成的结果
BIT_AND | 逻辑或
BIR_OR | 逻辑与
BIT_XOR | 逻辑异或

### 三、FROM 子句与多表连接查询

SELECT 语句的查询对象是由 FROM 子句指定的，其可根据用户的查询需求实现单表或多表查询。

之前的表查询都是针对一个表进行的，但在实际查询中往往需要从多个表中获取信息，此时的查询就会设计多张表。

若一个查询同时涉及两个或两个以上的表，则称之为多表连接查询，也称多表查询或连接查询。

#### 为什么要用多表连接查询

在关系数据库的设计中，为了**减少数据的冗余**，以及**增强数据库的稳定性和灵活性**，通常会**基于关系规范化原则**，将物理世界中的数据信息分解成多个表，实现**一类数据一个表**，并且在**表与表之间通过设置“键”（如主键、外键）的方式来保持多表之间的关联关系**。与此同时，在关系数据库的应用中，需要通过对表对象的连接运算，实现数据库中数据信息的组合，而这种**数据组合的需求**，在SQL中正是通过**多表连接查询来实现**的，这种分解与组合数据的方法，使得数据库系统能够**更有效地存储数据，更方便地处理数据，且可获得更大的可伸缩性。**

下面介绍交叉连接、内连接和外连接。

### 1.交叉连接  关键字 CROSS JOIN

交叉连接、又称笛卡尔积，在MySQL中，它是通过在FROM子句中使用关键字“CROSS JOIN”来连接两张表，从而实现一张表的每一行与另一张表的每一行的笛卡尔乘积，并返回两张表的每一行相乘的所有可能的搭配结果，供 SELECT 语句中其他语法元素（如 WHERE 子句、 GROUP BY 子句等）进行过滤和筛选操作。

例： 输出数据库 nunuDB 中的表 user 和 model 执行交叉连接后的所有数据集。

```MySQL
mysql> SELECT * FROM nunuDB.user CROSS JOIN nunuDB.model
```

以上是完整语法，但是一般省略如下

```MySQL
mysql> SELECT * FROM nunuDB.user,nunuDB.model
```

省略CROSS JOIN的同时用逗号分隔多个表。

注意：交叉连接返回的查询结果集的记录行数，等于其所连接的两张表记录行数的乘积，因此，对于存在大量数据的表，应该避免使用交叉连接。

### 2.内连接   关键字 INNER JOIN

内连接是最常用的连接类型，它是通过在查询中设置连接条件的方式，来移除查询结果集中某些数据行之后的交叉连接

具体而言，内连接就是利用条件判断表达式中的比较运算符来组合两张表中的记录，其目的是为了**消除交叉连接中的某些数据行**。

在MySQL中，可以通过在FROM子句中使用关键字“INNER JOIN”连接两张表，并使用ON子句设置连接的方式来实现内连接，其语法格式是：

```MySQL
SELECT some_columns
FROM table1
INNER JOIN
    table2
ON some_conditions;
```

在此语法中：

“some_columns”用于指定需要检索的列的名称或列别名；

“table1” 和 “table2”用于指定进行内连接的两张表的表名或表别名；

ON 子句通过事先设定的连接条件“some_conditions”来指定两张表按什么条件进行连接，且连接条件中可采用任何一种比较运算符。

连接条件“some_conditions”一般使用的语法格式：

```MySQL
[<table1>.]<列名或列别名><比较运算符>[<table2>.]<列名或列别名>
```

例：

```MySQL
mysql> SELECT *
    -> FROM user INNER JOIN model
    -> ON user_id = model_id;
```

可省略如下

```MySQL
mysql> SELECT *
    -> FROM user JOIN model
    -> ON user_id = model_id;
```

内连接中的INNER是可省略而只用JOIN。

在FROM子句中，也可以在多个表之间连续使用关键字“INNER JOIN”或关键字“JOIN”实现多个表的内连接。

内连接的使用通常有如下三种情形。

（1）等值连接

特征就是在ON子句中使用比较运算符“=”进行**相等性测试**，这称作**等值连接**或**相等连接**。

通常，在等值连接的条件设置中会**包含一个主键和一个外键**。

（2）非等值连接

特征就是在ON子句中使用除了比较运算符“=”之外的其他比较运算符进行不相等性测试，称作非等值连接或不等连接。

（3）自连接

自连接是一个表与它自身进行连接，是一种特殊的内连接，若需要在一个表中查找具有相同列值的行，则可以考虑使用自连。

使用自连接时，需要为表指定两个不同的别名，且对所有查询列的引用均必须使用表别名限定，否则SELECT操作会失败。

### 3.外连接

内连接事在交叉连接的结果集上返回值满足条件的记录，但有时也会存在输出那些不满足连接条件的元组信息的查询需求，这种情况就需要使用外连接来实现。

外连接是首先将连接的两张表分为**基表**和**参考表**，然后再**以基表为依据**返回满足和不满足条件的记录。

外连接可以在表中没有匹配记录的情况下仍返回记录。

外连接要比内连接更加注重两张表之间的关系，其根据连接表的顺序，可分为左外连接和右外连接两种。

（1）左外连接（左连接） 关键字 LEFT OUTER JOIN 或 LEFT JOIN

左外连接大概意思是：**保留左表的每一行和右表符合条件的行**，对于坐标中有的，但在右表中不匹配的行，从右表中被选择的列的值被设置为NULL。

右外连接与其相反

### 四、WHERE 子句与条件查询

在 SELECT 语句中，可以使用 WHERE 子句指定过滤条件（也成为搜索条件或查询条件），从 FROM 子句的中间结果中选取适当的数据行，实现数据的过滤。

WHERE子句中设置过滤条件的几个常用方法：

#### 1.比较运算

例： 在数据库 nunuDB 的表 user 中查找所有男性客户的信息。

```Mysql
mysql>SELECT * FROM nunuDB.user
    ->WHERE user_sex = 1;
```

#### 2.判定范围

在 WHERE 子句中，用于范围判定的关键字有 “BETWEEN” 和 “IN” 两个。

（1）BETWEEN···AND

当查询的过滤条件被限定在值得某个范围时，可以使用关键字“BETWEEN”。

语法格式：

```MySQL
expression [ NOT ] BETWEEN expression1 AND expression2
```

其中，表达式 expression1 的值不能大于表达式 expression2 的值。

例如：在数据库 nunuDB 的表 user 中，查询客户id号在4-8（包含4和8）之间的5个客户的信息。

```MySQL
mysql> SELECT * FROM user
    -> WHERE user_id BETWEEN 4 AND 8;
```

（2）IN

使用关键字 IN 可以指定一个值的枚举表，该表中会列出所有可能的值，其使用语法格式时：

```MySQL
expression IN (expression [,···n])
```

当要判定的值能与该表中任意一个值匹配时，会返回结果 TRUE，否则返回 FALSE。

注意，尽管关键字“IN”可用于范围判定，但其最主要的作用是表达子查询。

例： 在数据库 nunuDB 的表 user 中，查询用户id号分别为 3,5,7 三个客户的信息。

```MySQL
mysql> SELECT * FROM nunuDB.user WHERE user_id IN (3,5,7);
```

#### 3.判定空值

当需要判定一个表示的值是否为空值时，可以使用关键字“IS NULL” 来实现。

语法格式：

```MySQL
expression IS [NOT] NULL
```

#### 4.子查询

通常，可以使用 SELECT 语句创造子查询，即可嵌套在其他 SELECT 查询中的 SELECT 查询。

在MySQL中，区分以下四类子查询：

1、表子查询，即子查询返回的结果集是一个表。

2、行子查询，即子查询返回的结果集是带有一个或多个值的一行数据。

3、列子查询，即子查询返回的结果集是一列数据，该列可以有一行或多行，但每行只有一个值。

4、标量子查询，即子查询返回的结果集仅仅是一个值。

在指定查询条件的 WHERE 子句中，可以使用子查询的结果集作为过滤条件的一部分，并且子查询通常会与关键字“IN”“EXIST”和比较运算符结合使用。

（1）结合关键字 “IN” 使用的子查询

结合关键字 “IN” 所使用的子查询主要用于判定一个给定值是否存在于子查询的结果集里。

语法格式：

```MySQL
expression [NOT] IN ( subquery )
```

在此语法中：

subquery 用于指定子查询，通常这里的子查询只能返回一列数据，而对于比较复杂的查询要求，则可以使用 SELECT 语句实现子查询的多层嵌套；

expression 用于指定表达式，当表达式与子查询返回的结果集里某个值相等时，过滤条件返回 TRUE，否则返回FALSE，若使用了关键字“NOT”，则返回的值正好相反。

例：查询数据库 nunuDB 中表 user 中 年龄大于21 的用户的id和姓名

```MySQL
mysql> SELECT user_id,user_name
    -> FROM user
    -> WHERE user_id IN (SELECT user_id FROM user WHERE user_age > 21)
```

（2）结合比较运算使用的子查询

结合比较运算符所使用的子查询主要用于将表达式的值与子查询的结果进行比较运算，其使用语法格式是：

```MySQL
expression {关系运算符} {ALL|SOME|ANY} (subquery)
```

在此语法中：

三个关键字 “ALL” “SOME” “ANY” 为选择项，用于**指定对比较运算的限制**。

其中，关键字 “ALL” 用于指定表达式需要与子查询结果集里的每个值都进行比较，当表达式与每个值都满足比较关系时，会返回 TRUE，否则返回 FALSE；

关键字“SOME” 和 “ANY” 是同义词，表示表达式只要与子查询结果集里的某个值满足比较关系时，就返回TRUE，否则返回 FALSE。

（3）结合关键字 “EXIST” 使用的子查询

结合关键字 “EXIST” 所使用的子查询主要用于判定子查询的结果集是否为空。

语法格式：

```MySQL
EXIST(subquery)
```

其中，如果子查询的结果集不为空，则返回TRUE，否则返回FALSE。

注意：子查询通常可以改写成通过表的连接方式来实现，只是两者的执行性能会有所差异。

### 五、GROUP BY 子句与分组数据

在 SELECT 语句中，允许使用 GROUP BY 子句，将结果集里的数据行根据选择列的值进行逻辑分组，以便能汇总表内容的子集，即实现对每个组的聚集计算。因而，GROUP BY 子句可指示 DBMS 分组数据，然后对每个组而不是对整个结果集进行聚合。

语法格式

```MySQL
GROUP BY {col_name|expr|position} [ASC|DESC], ··· [WITH ROLLUP]
```

在此语法格式中：

col_name:指定用于分组的选择列。可以指定多个列，彼此间用逗号分隔。 注意：**GROUP BY 子句中的各选择列必须也是SELECT语句的选择列表清单中的一项。

expr: 指定分组的表达式。该表达式通常与聚合函数一块使用，例如可将表达式“COUNT(*)AS '人数'” 作为SELECT语句的选择列表清单中的一项。

position: 指定用于分组的选择列在SELECT语句结果集中的文职，通常是一个正整数。例如，使用GROUP BY 3 表示根据SELECT语句中 列清单上的第三列的值进行逻辑分组。

ASC | DESC：关键字“ASC”表示按升序分组；关键字 “DESC” 表示按降序分组。默认值为ASC。这两个关键字必须位于对应的列名、表达式、列的位置后。

WITH ROLLUP: 此关键字为可选项，用于指定在结果集中不仅包含由 GROUP BY 子句分组后的数据行，还包含各分组的汇总行，以及所有分组的整体汇总行。因此，使用该关键字，可以得到每个分组以及每个分组汇总级别的值。

例：在数据库 nunuDB 的表 user 中获取一个数据结果集，要求该结果集里分别包含 用户性别、用户年龄和每个用户年龄中每个性别的人数。

```MySQL
mysql> SELECT user_sex,user_age,COUNT(*) '人数'
    -> FROM user
    -> GROUP BY user_sex,user_age;
```

例：在数据库 nunuDB 的表 user 中获取一个数据结果集，要求改结果集里分别包含 用户性别、用户年龄和每个用户年龄中每个性别的人数，不同性别的总人数还有总人数。

```MySQL
mysql> SELECT user_sex,user_age,COUNT(*) '人数'
    -> FROM user
    -> GROUP BY user_sex,user_age
    -> WITH ROLLUP
```

在 GROUP BY 子句中添加可选项“WITH ROLLUP”后，将对 GROUP BY 子句中所指定的各列再次生成汇总行，其汇总规则是： 按列的排列的逆序依次进行汇总，并且在生成的统一逻辑组的汇总行中，对于具有不同列值的字段值将被设置为NULL。

对于 GROUP BY 的使用需要注意以下几点：

1、GROUP BY 子句可以包含任意数目的列，使得其可对分组进行嵌套，为数据分组提供更加细致的控制。

2、如果在 GROUP BY 子句中嵌套了分组，那么将按GROUP BY 子句中列的排列顺序的逆序方式依次进行汇总，并将在最后规定的分组上进行一个完全汇总。

3、GROUP BY 子句中列出的每个列都必须在 GROUP BY 子句中给出。

4、除聚合函数外，SELECT 语句中的梅格列都必须在 GROUP BY 子句中给出。

5、如果用于分组的列中含有NULL值，则NULL将最为一个单独的分组返回；如果该列中存在多个NULL值，则将这些NULL值所在的行分为一组。

### HAVING 子句  过滤分组

在 SELECT 语句中，除了能使用 GROUP BY 子句分组数据之外，还可以使用 HAVING 子句来**过滤分组**，即在结果集里规定包含哪些**分组**和排除哪些**分组**。

语法格式：

```MySQL
HAVING where_condition
```

where_condition 用于指定过滤条件。

HAVING 子句 于 WHERE 子句非常相似，HAVING 子句支持 WHERE 子句中所有的操作符和句法，但两者之间仍存在以下几点差异。

1、WHERE主要用于**过滤数据行**，而 HAVING 子句主要用于**过滤分组**，即HAVING子句可**基于分组的聚合值而不是特定行的值来过滤数据**。

2、HAVING 子句中的条件可以包含聚合函数，而WHERE子句中则不可以。

3、WHERE 子句会在**数据分组前**进行过滤，HAVING 子句则会在**数据分组后**进行过滤。因而，WHERE 子句排除的行不包含在分组中，这就会可能改变计算值，从而影响HAVING子句基于这些值过滤掉的分组。

例：在数据库 nunuDB 的表 user 中查找这样一类客户信息：要求在返回的结果集里，列出满足相同年龄和相同性别大于两人的年龄数。

```MySQL
mysql>SELECT user_sex，user_age
    ->FROM user
    ->GROUP BY user_sex,user_age
    ->HAVING COUNT(*)>=2;
```

### ORDER BY 子句 结果集排序

在 SELECT 语句中，可以使用 ORDER BY 子句将结果集中的数据行按一定的顺序进行排列，否则结果集里数据行的顺序是不可预料的。

语法格式；

```MySQL
ORDER BY {col_name|expr|position}[ASC|DESC],···
```

在此语法中：

col_name: 指定用于排序的列。可以同时指定多个列，列名彼此间用逗号分隔。

expr：指定用于排序的表达式

position：指定用于排序的列在SELECT语句结果集里的位置，通常是一个正整数。例如，使用 ORDER BY 2 表示对 SELECT 语句中列清单上的第2列进行排序。

ASC | DESC：关键字“ASC”表示按升序排列；关键字“DESC”表示按降序排列。其中，默认值为ASC。这两个关键字必须位于对应的列名、表达式、列的位置之后。

例：在数据库 nunuDB 的表 user 中 用对数据“年龄”升序的方式列出用户的id和姓名。

```MySQL
mysql>SELECT user_id,user_name
    ->FROM user
    ->ORDER BY user_age ASC;
```

对于 ORDER BY 子句的使用，需要注意以下几点：

1、ORDER BY 子句中可以包含子查询。

2、当对空值进行排序时，ORDER BY 子句会将该空值作为最小值来对待。即，若按升序排列结果集，则 ORDER BY 子句会将该空值所在的数据行置于结果集的最上方；若是使用降序排序，则会将其置于结果集的最下方。

3、若在 ORDER BY 子句中指定多个列进行排序，则在MySQL中会按照这些列从左至右所罗列的次序依次进行排序。

4、在使用 GROUP BY 子句时，通常也会同时使用 ORDER BY 子句。

以下列出 ORDER BY 子句 与 GROUP BY 子句的差别：

ORDER BY 子句|GROUP BY 子句
---|:--:|
排序产生的输出|分组可以，但输出可能不是分组的排序
任意列都可以使用|值可能使用选择列或表达式列
不一定需要| 若与聚合函数一起使用列或表达式，则必须使用

### LIMIT 子句 限制返回的行数

当使用 SELECT 语句返回的结果集中行数很多时，为了便于用户对结果数据的浏览和操作，可以使用LIMIT子句来限制被SELECT语句返回的行数。

语法格式：

```MySQL
LIMIT {[offset,] row_count|row_count OFFSET offset}
```

在此语法中：

offset：为可选项，默认为数字0，用于指定返回数据的第一行在 SELECT 语句结果集里的**偏移量**，其**必须是非负的整数**常量。注意，SELECT 语句结果集里的**第一行（初始行）的偏移量为0，而不是1**。

row_count：用于指定返回数据的行数，其也必须是非负的整数常量，若这个指定行数大于实际能返回的行数时，在MySQL只返回能返回的数据行。

row_count OFFSET offset:从第 offset+1 行开始，取 row_count 行。

例： 在数据库 nunuDB 的表 user 中查找从第2位客户开始的3位客户的id号和姓名信息。

```MySQL
mysql> SELECT user_id,user_name FROM user
    -> ORDER BY user_id
    -> LIMIT 1,3 //第二位客户的偏移量为1，展示3位。
```

也可以写成

```MySQL
mysql> SELECT user_id,user_name FROM user
    -> ORDER BY user_id
    -> LIMIT 3 OFFSET 1
```

## 视图

### 创建视图

#### CREATEVIEW 语句 创建视图

语法格式：

```MySQL
CREATEVIEW view_name [(column_list)]
    AS select_statement
    [WITH [CASCADED|LOCAL] CHECK OPTION]
```

在此语法结构中：

view_name 视图名 用于指定视图的名称，且该名称在数据库中必须是唯一的，不能与其他表或视图同名。

column_list 列名  是可选项，用于为视图的每个列指定明确的名称，且**列名的数目必须等于 SELECT 语句检索出的结果数据集的列数**，同时每个列名间用逗号分隔。如若省略 column_list ，则新建视图使用与基本表或源视图中相同的列名。

select_statement  SELECT语句 用于指定创建视图的 SELECT 语句。这个 SELECT 语句给出了视图的定义，它可用于查询多个基本表或源视图。

WITH CHECK OPTION 是可选项，用于指定在可更新视图上所进行的修改都需要符合 select_statement 中所指定的限制条件，这样可以确保数据修改后，仍可以通过视图看到修改后的数据。当视图是根据另一个视图定义时，关键字 “WITH CHECK OPTION” 给出两个参数，即 CASCADED 和 LOCAL，它们决定检查测试的范围。其中，关键字 “CASCADED” 为选项默认值，它会对所有视图进行检查，而关键字 “LOCAL” 则使 CHECK OPTION 只对定义的视图进行检查。

例： 在数据库 nunuDB 中创建 视图 user_view，该视图包含user表中所有性别为男的用户信息，并且要求保证今后对该试图数据的修改都必须符合客户性别为男性这个条件。

```MySQL
mysql> CREATE VIEW Man_view AS SELECT * FROM nunuDB.user WHERE user_sex = 1
    -> WITH CHECK OPTION;
```

如果视图使用WITH LOCAL CHECK OPTION，MySQL会检查WITH LOCAL CHECK OPTION和WITH CASCADED CHECK OPTION选项的视图规则。

与使用WITH CASCADED CHECK OPTION的视图不同，MySQL检查所有依赖视图的规则。

请注意，在MySQL 5.7.6之前，如果您使用带有WITH LOCAL CHECK OPTION的视图，MySQL只会检查当前视图的规则，并且不会检查底层视图的规则。

### 删除视图

#### DROP VIEW 语句 删除视图

语法格式：

```MySQL
DROP VIEW [IF EXISTS]
    view_name[,view_name] ···
    [RESTRICT|CASCADE]
```

使用DROP VIEW 可以一次性删除多个视图，但必须在每个视图上拥有DROP权限。

同时，为了防止因删除不存在的视图而出错，需要在 DROP VIEW 语句中添加关键字 “IF EXISTS”。

### 修改视图定义

#### ALTER VIEW  修改视图定义

```MySQL
ALTER VIEW view_name [(column_list)]
    AS select_statement
    [WITH [CASCADED|LOCAL] CHECK OPTION]
```

可见 ALTER VIEW 语句 与 CREATE VIEW 语句的语法是相似的。

此外，修改视图的定义，也可以通过先 DROP VIEW 再 CREATE VIEW 语句的过程来实现。

### 查看视图定义

#### SHOW CREATE VIEW 语句 查看已有视图的定义（结构）

语法：

```MySQL
SHOW CREATE VIEW view_name
```

view_name 指定要查看视图的名称

### 更新视图数据

更新视图数据总共有三种方法。

需要注意的是： 视图的更新操作是受一定限制的，并非所有的视图都可以进行 INSERT、UPDATE 或 DELETE 等更新操作，只有满足可更新条件的视图才能更新，否则可能会导致系统出现不可预期的结果。

对于可更新的视图，需要该视图中的行于基本表中的行之间具有一对一的关系。

第一种更新视图的方法

#### INSERT 语句 通过视图向基本表插入数据

例：

```MySQL
mysql> INSERT INTO nunuDB.user_view
    ->  VALUES(4,'bb',3,1);
```

这条插入语句能够成功执行，是因为在创建视图 customers_view 的语句中添加了 WITH CHECK OPTION 子句，该子句会在更新数据时，检查新数据是否符合视图定义中 WHERE 子句的条件，且 WITH CHECK OPTION 子句只能和可更新视图一起使用。若新数据不符合where子句的条件，则操作无法成功。

另外，当视图所依赖的基本表有多个时，也不能向该视图插入数据，这是因为在MySQL中不能正确地确定要被更新地基本表。

#### UPDATE 语句 通过视图修改基本表的数据

例：将视图 new_view 中所有客户的 user_address 列更新为'深圳市'

```MySQL
mysql>UPDATE new_view
    ->SET user_address = '深圳市';
```

注意：若一个视图依赖于多个基本表，则依次视图数据修改操作只能改变一个基本表中的数据。

#### 使用 DELETE 语句 通过视图删除基本表的数据

例： 删除视图 new_view 中 姓名为 fff 的客户信息。

```MySQL
mysql>DELETE FROM new_view
    ->  WHERE user_name = 'fff';
```

#### 查询视图数据

例： 在视图 new_view 中查找客户 ID 号为 15 的客户姓名及其地址信息

```MySQL
mysql> SELECT user_name,user_address
    ->  FROM new_view
    ->  WHERE user_id = 15;
```

## 存储过程

### 修改结束标志

#### DELIMITER 命令

语法格式：

```MySQL
DELIMITER $$
```

其中 $$是用户定义的结束符，避免使用反斜杠\

例：把MySQL结束符修改为两个感叹号 !!

```MySQL
mysql> DELIMITER !!
```

### 创建存储过程

#### CREATE PROCEDURE 语句 创建存储过程

语法格式：

```MySQL
CREATE PROCEDURE sp_name ([PROC_PARAMETER[,···]])
    routine_body
```

其中，语法项“proc_parameter”的语法格式是：

```MySQL
[ IN | OUT | INOUT ] param_name type
```

在此语法格式中：

sp_name 指定 存储过程的名称，且默认在当前数据库中创建。

proc_parameter 指定存储过程的参数列表。

在proce_parameter 中：

param_name 为参数名

type为参数的类型（可以是任何有效的MySQL数据类型）。当有多个参数是，参数列表中彼此间用逗号分隔。

存储过程可以没有参数（此时存储过程的名称后仍需加上一对括号），也可以有一个或多个参数。

MySQL 存储过程支持三种类型的参数，即输入参数，输出参数和输入/输出参数，分别用“IN”“OUT”和“INOUT”三个关键字表示。

输入参数是使数据可以传递给一个存储过程；

输出参数用于存储过程需要返回一个操作结果的情形。

输入/输出参数既可以充当输入参数也可以充当输出参数。

注意：参数的取名不要于数据表的列名相同，否则尽管不会出错，但是存储过程中的SQL语句会将参数名看作是列名，而而发不可预知的结果。

routine_body 表示存储过程的主体部分，也成为存储过程体，其包含了在过程调用的时候必须执行的 SQL 语句。以关键字BEGIN开始，以关键字END结束，如果存储过程体只有一条SQL语句时，可以省略BEGIN···END标志。另外，在存储过程体中，BEGIN···END复合语句可以嵌套使用。

例如：在数据库 nunuDB 中创建一个存储过程，用于实现对 调用该存储过程并给出用户id号 与 指定性别 即可修改 user 表中该用户的性别。

```MySQL
mysql> use nunuDB
DATAbase changed
mysql> DELIMITER $$
mysql> CREATE PROCEDURE change_sex(IN userid INT,IN usersex INT)
    -> BEGIN
    ->  UPDATE user SET user_sex = usersex WHERE user_id = userid;
    -> END $$
```

### 存储过程体

存储过程体的几个常用语法元素：

1.局部变量

在存储过程提中可以声明局部变量，用来存储存储过程体中的临时结果。在MySQL中，额可以使用 DECLARE 语句来声明局部变量，并且同时还可以对该局部变量赋予一个初始值。

语法格式是：

```MySQL
DECLARE var_name[,···] type [DEFAULT value]
```

var_name 用于指定局部变量的名称

type 用于声明局部变量的数据类型

DEFAULT 子句用于为局部变量指定一个默认值，若没有指定，则默认为 NULL。

例：声明一个整型局部变量 userid。

```MySQL
DECLARE userid INT(10)
```

注意：

局部变量只能在**存储过程体的 BEGIN ··· END 语句块中声明**。

局部变量**必须在存储过程体的开头处声明**。

局部变量的**作用范围仅限于声明它的 BEGIN ··· 语句块**，其他语句块中的语句不可以使用它。

局部变量不同于用户变量，两者的区别在于：局部变量声明时，在其前面没有使用@符号，并且它只能被声明它的 BEGIN···END语句块中的语句所使用；而**用户变量在声明时，会在其名称前面使用@符号，同时已声明的用户变量存在于整个会话之中**。

2.SET 语句

使用 SET 语句为局部变量赋值。

语法格式:

```MySQL
SET var_name = expr [, var_name = expr] ···
```

例： 为 已声明的userid 局部变量赋予一个整数值 910。 在存储过程（BEGIN 与 END）中

```MySQL
SET userid=910
```

3.SELECT···INTO 语句

SELECT···INTO 语句把选定列的值直接存储到局部变量中。

语法格式：

```MySQL
SELECT col_name[,···] INTO var_name[,···] table_expr
```

col_name 指定列名

var_name 指定要赋值的变量名

table_expr 表示 SELECT 语句中的 FROM 子句及后面的语法部分。

注意： 存储过程提中的 SELECT···INTO 语句返回的结果集只能有一行数据。

4.流程控制语句

MySQL在存储过程体中，可以使用条件判断语句和循环语句这样两类用于控制语句流程的过程式SQL语句。

1、条件判断语句： 常用的条件判断语句有 IF···THEN···ELSE 语句和 CASE 语句。使用语法和方式类似于高级程序设计语言。

2、循环语句： 常用的循环语句有 WHILE 语句、 REPEAT 语句和 LOOP 语句。使用语法和方式同样类似于高级程序设计语言。此外，循环语句中还可以使用 ITERATE 语句，但它只能出现在循环语句的 LOOP、REPEAT 和 WHILE 子句中，用于表示退出当前循环，且重新开始一个循环。

5.游标

在SELECT···语句成功执行后，会返回带有值的一行数据，这行数据可以被读取到存储过程中进行处理。

然而，在使用 SELECT 语句进行数据检索会返回一组称为结果集的数据行，该结果集可能拥有多行数据，这些数据无法直接被一行一行的进行处理，此时就需要使用游标。（在高级程序设计语言中，称之为迭代器）

**游标是一个被 SELECT 语句检索出来的结果集**。在存储了游标后，应用程序或用户就可以根据需要滚动或浏览其中的数据。

步骤如下：

#### 1、声明游标 DECLARE CURSOR 语句

在能够使用游标之前，必须先声明（定义）它。**这个过程实际上没有检索数据**，只是定义要使用的 SELECT 语句。

语法格式：

```MySQL
DECLARE cursor_name CURSOR FOR select_statement
```

cursor_name 指定要创建的游标的名称，命名规则与表名相同；

select_statement 指定一个 SELECT 语句，会返回一行或多行的数据，需要**注意此处的SELECT语句不能有INTO子句**。

#### 2、打开游标 OPEN 语句

定义了游标之后，必须打开该游标才能使用，这个过程实际上是将游标连接到SELECT语句返回的结果集中。

注意:定义的时候SELECT语句并没有执行，但是在OPEN语句会执行游标内的SELECT语句。

语法格式：

```MySQL
OPEN cursor_name
```

cursor_name 指定要打开的游标

一个游标可以被多次打开，因为其他用户或应用程序可能随时更新了数据表，因此**每次打开游标的结果集可能会不同**。

#### 3、读取数据 FETCH···INTO 语句读取游标数据

语法格式：

```MySQL
FETCH cursor_name INTO var_name [,var_name] ···
```

cursor_name 指定**已打开的游标**

var_name 指定存放数据的变量名

FETCH ··· INTO 语句 与 SELECT···INTO 语句具有相同的意义，FETCH语句是将游标指向的一行数据赋给一些变量，这些变量的数目必须等于声明游标时 SELECT 子句中选择列的数目。

**游标相当于一个指针，它指向当前的一行数据。**

4、关闭游标 CLOSE 语句关闭游标

在游标使用结束时，必须关闭游标。

语法格式：

```MySQL
CLOSE cursor_name
```

cursor_name 指 要关闭的游标

每个游标不再需要时都应该被关闭，使用Close语句将会释放游标所使用的全部资源。在一个游标被关闭后，如果没有重新被打开，则不能被使用。对于声明过的游标，则不需要再次声明，可直接使用 OPEN 语句打开。另外，如果没有明确关闭游标，MySQL 将会在到达END语句时自动关闭它。

例：在 nunuDB 中创建一个存储过程 userlen，用于计算表user中数据行的行数。

(以下内容是在编辑器中写的)

```MySQL
CREATE PROCEDURE userlen(OUT len INT)
begin
declare uid int;
declare hasID boolean default true;
declare cursor_id cursor for select user_id from user;
declare continue handler for not found set hasID = false;
set len = 0;
open cursor_id;
fetch cursor_id into uid;
while hasID do set len = len+1;
fetch cursor_id into uid;
end while;
close cursor_id;
end;
```

调用：

```MySQL
mysql> CALL userlen(@len);
```

查看结果：

```MySQL
mysql> SELECT @len;
```

此例中定义了CONTINUE HANDLER 句柄，它事在条件出现时被执行的代码，用于控制循环语句，以实现游标的下移；

注意：

1.**DECLARE语句的使用存在特定的次序， 先局部变量，后游标，再句柄。否则会报错**

2.游标只能用于存储过程或存储函数中，不能单独在查询操作中使用。

3.在存储过程或存储函数中可以定义多个游标，但是在一个BEGIN···END语句块中每一个游标的名字必须是唯一的。

4.游标不是一条SELECT语句，是被SELECT语句检索出来的结果集。

### 调用存储过程 CALL

创建好存储过程后，可以使用 CALL语句在程序或者其他存储过程中调用它。

语法格式：

```MySQL
CALL sp_name([parameter[,···]])
CALL sp_name[()]
```

在此语法格式中：

sp_name 用于指定被调用的存储过程的名称。如果要调用某个特定数据库的存储过程，则需要在前面加上该数据库的名称。

parameter 用于指定调用存储过程所要使用的参数。调用语句中参数的个数必须等于存储过程的参数个数。

当调用没有参数的存储过程时，使用CALL sp_name()语句与使用 CALL sp_name 语句是相同的。

例：调用数据库nunuDB中的存储过程 change_sex，将用户id号为5的用户性别改为 1.

```MySQL
mysql> CAll change_sex(5,1)
```

### 删除存储过程 DROP PROCEDURE

存储过程在被创建后，会被保存在服务器上以供使用，直至被删除。在 MySQL 中，可以使用DROPPROCEDURE 语句删除数据库中已创建的存储过程。

语法：

```MySQL
DROP PROCEDURE[IF EXISTS] sp_name
```

sp_name 指要删除的存储过程的名称。

在删除之前，必须确认该存储过程没有任何依赖关系，否则会导致其他与之关联的存储过程无法运行。

例： 删除数据库 nunuDB 中的存储过程 change_sex

```MySQL
mysql>DROP PROCEDURE change_sex;
```

## 存储函数

存储函数与存储过程一样，都是由SQL语句和过程式语句所组成的代码片段，并且可以被应用程序和其他 SQL 语句调用。然而，它们之间存在如下几点区别：

1、存储函数不能拥有输出参数，这是因为存储函数自身就是输出参数；而存储过程可以拥有输出参数。

2、可以直接对存储函数进行调用，而不需要使用 CALL 语句。 对存储过程的调用则需要CALL 语句。

3、存储函数中必须包含一条 RETURN 语句，而这条特殊的 SQL 语句不允许包含于存储过程中。

### 创建存储函数 CREATE FUNCTION

语法：

```MySQL
CREATE FUNCTION sp_name ([func_parameter[,···]])
    RETURNS type
    routine_body
```

其中 func_parameter 的语法格式是：

```MySQL
param_name type
```

在此语法格式中：

sp_name 指定存储函数的名称，存储函数不能与存储过程具有相同的名字。

func_parameter 指定存储函数的参数，这里的参数只有名称和类型，不能指定关键字“IN”“OUT”和“INOUT”。

RETURNS 子句用于声明存储函数返回值的数据类型，其中 type 用于指定返回值的数据类型。

rowtine_body 指定存储函数的主题部分，也成为存储函数体。所有在存储过程中使用的SQL语句在存储函数中同样也使用，包括前面所介绍的局部变量、SET语句、流程控制语句、游标等。但是，存储函数体中还必须包含一个 RETURN value 语句，其中value用于指定存储函数的返回值。

例：在数据库 user 中创建一个存储函数，要求改函数能根据给定用户的id号返回该用户的性别，如果数据库中没有给定的客户id号，则返回“没有该客户”。

```MySQl
create function search_sex(userid int)
    returns char(2)
    deterministic
begin
    declare sex char(2);
select userid into sex from user
    where user_id = userid;
if sex is null
    then return(select '无该用户');
else if sex = '男'
    then return(select '男');
    else return(select '女');
    end if;
end if;
end;
```

## 调用存储函数 SELECT

语法：

```MySQL
SELECT sp_name([func_parameter[,···]])
```

例： 调用数据库 nunuDB 中的存储函数 search_sex搜索ID号为5的用户性别

```MySQL
SELECT search_sex(5)
```

## 删除存储函数 DROP FUNCITON

语法：

```MySQL
DROP FUNCTION [IF EXISTS] sp_name
```

sp_name 指定要删除的存储函数的名称。

在删除之前，必须确认该存储函数没有任何依赖关系，否则会导致其他与之关联的存储函数无法运行。为了不出错，最好能添加 IF EXISTS。

例：删除数据库 nunuDB 中的存储函数 search_sex.

```MySQL
mysql>drop function if exists search_sex;  
```

--------------------

## 主键候选键相关知识

关系模型有三类完整性约束：实体完整性，参照完整性，用户定义的完整性。

### 实体完整性

实体完整性则是通过 **主键约束** 和 **候选键约束** 实现的。

#### 主键约束 （PRIMARY）

主键可以是表中的某一列，也可以是表中多个列所构成的一个组合。其中，由多个列组合而成的主键也称为复合主键。在MySQL中，主键列必须遵守如下一些规则：

1.**每个表只能定义一个主键。**

2.主键的值（键值），必须能够**唯一标志表中的每一行记录**，且不能为null。——**唯一性原则**

3.复合主键不能包含不必要的多余列。——**最小化原则**

4.一个列名在复合主键的列表中只能出现一次。

定义主键约束后，MySQL会自动为主键创建一个唯一性索引，用于在查询中使用主键对数据进行快速检索，该索引名默认为 PRIMARY，也可以重新自定义命名。

#### 候选键约束 （UNIQUE）

大多数与主键是一样的。

但有以下几点区别：

1.一个表中只能创建一个主键，但可以定义若干个候选键。

2.定义主键约束时，系统会自动产生 PRIMARY KEY 索引，而定义候选键约束时，系统会自动产生UNIQUE索引。

### 参照完整性

在 MySQL 中，参照完整性是通过在创建表或更新表的同时定义一个外键声明来实现的。外键声明有如下两种方式：

在表中某个列的属性定义后直接加上 “reference_definition” 语法项。

在表中所有列的属性定义后添加 “FOREIGN KEY (index_col_name,···) reference_definition 子句的语法项。

reference_definition语法项的定义：

```MySQL
REFERENCES tbl_name(index_col_name,···)
    [ON DELETE reference_option]
    [ON UPDATE reference_option]
```

上面的index_col_name的语法格式是：

```MySQL
col_name[(length)] [ASC|DESC]
```

reference_option 的语法格式是：

```MySQL
RESTRICT|CASCADE|SET NULL|NOCATION
```

可见，语法项“reference_definition”的语法定义主要包含外键所参照的表和列、参照动作的声明、实施策略等四部分内容。

tbl_name 指定外键所参照的表名。这个表称为“被参照表”（或“父表”），  而外键所在的表称作参照表（或子表）。

col_name 指定被参照的列名。外键可以引用**被参照表中的主键或候选键，也可以引用被参照表中某些列的一个组合**，但这个组合不能是被参照表中随机的一组列，**必须保证该组合的取值在被参照表中是唯一的**。外键中的所有列值在被参照表的列中必须全部存在，也就是通过外键来对参照表中某些列（外键）的取值进行限定或约束。

ON DELETE 或 ON UPDATE 指定参照动作相关的 SQL 语句，这里可为每个外键指定的参照动作分别对应于 DELETE 语句和 UPDATE 语句。

reference_option 指定参照完整性约束的实现策略，默认为RESTRICT。具体策略如下：

RESTRICT 表示限制策略，即当要删除或更新被参照表中被参照列上（的值），并在外键中出现的值时，系统拒绝对被参照表的删除或更新操作。；

CASCADE 表示级联策略，即从被参照表中删除或更新记录行时，自动删除或更新参照表中匹配的记录行；

SET NULL 表示置空策略，即当从被参照表中删除或更新记录行时，设置参照表中与之对应的外键列的值为 NULL，这个策略需要被参照表中的外键列没有声明限定词 NOT NULL；

NO ACTION 表示不采取实施策略，即当一个相关的外键值在被参照表中时，删除或更新被参照表中键值的动作不被允许，该策略的动作语义与 RESTRICT 相同。

当指定一个外键时：

1、被参照表必须已经用一条 CREATE TABLE 语句创建了，或者必须是当前正在创建的表。如若时后一种情形，则被参照表与参照表时同一个表，这样的表称为自参照表（self-referencing table），这种结构称为自参照完整性（self-referential integrity）。

2、必须为被参照表定义主键。

3、必须在被参照表的表名后面指定列名或列名的组合。这个列或列组合必须是这个被参照表的主键或候选键。

4、尽管主键时不能够包含空值的，但允许在外键中出现一个空值。这意味着，只要外键的每个非空值出现在指定的主键中，这个外键的内容就是正确的。

5、外键中的列的数目必须和被参照表的主键中的列的数目相同

6、外键中的列的数据类型必须和被参照表的主键中的对应列的数据类型相同。

### 用户定义的完整性

MySQL支持三种用户自定义完整性约束，分别是非空约束、CHECK 约束 和 触发器。

非空约束 也就是 NOT NULL 不做介绍。

#### CHECK 约束

CHECK 约束事在创建表（CREATE TABLE）或 更新表（ALTER TABLE）的同时，根据用户的实际完整性要求来定义的。它可以分别对列或表实施 CHECK 约束。

语法格式：

```MySQL
CHECK (expr)
```

expr 是一个 SQL 表达式，用于指定需要检查的限定条件。在更新表数据时，MySQL会检查更新后的数据行是否满足 CHECK 约束中的限定条件。MySQL 可以使用简单的表达式来实现 CHECK约束，也允许私用复杂的表达式作为限定条件，例如，在限定条件中加入子查询。若将CHECK 约束子句置于表中某个列的定义之后，则这种约束称为基于列的CHECK约束；若将CHECK语句置于表中所有列的定义以及主键约束和外键定义之后，则称为基于表的CHECK 约束，该约束可以同时对表中多个列设置限定条件。

### 对 完整性约束 命名

完整性约束是可以进行添加、删除和修改等操作的。为了删除和修改完整性约束，首先需要在定义约束的同时对其进行命名。命名完整性约束的方法是在各种完整性约束的定义说明之前加上关键字 CONSTRAINT 和该约束的名字。

语法：

```MySQL
CONSTRAINT [symol]
```

symbol 是指定的约束名字，这个名字是在完整性约束说明的前面被定义，其在数据库里必须是唯一的。若不明确给出约束的名字，则 MySQL自动创建一个约束名字。

定义完整性约束时，应尽可能地为其指定名字，以便在需要对完整性约束进行修改或删除操作时，可以更加容易地引用它们。

注意：只能给基于表地完整性约束指定名字，而无法给基于列地完整性约束指定名字。因此，基于表地完整性约束必基于列地完整性约束更受欢迎。

### 对 完整性约束 更新

当对各种约束进行命名后，就可以使用 ALTER TABLE 语句来更新与列或表有关的各种约束。例如，若要添加约束，可在ALTER TABLE 语句中使用 ADD CONSTRAINT 子句，实际上这也是定义约束的一种形式。此外，需要注意以下两点。

1、完整性约束不能直接被修改。若要修改某个约束，实际上使用 ALTER TABLE 语句先删除该约束，然后在增加一个与该约束同名的新约束。

2、使用ALTER TABLE 语句，可以独立地删除完整性约束，而不会删除表本身。若使用 DROP TABLE 语句删除一个表，则表中所有的完整性约束都会自动被删除。

## 触发器

触发器（Trigger）是用户**定义在关系表上**的一类由**事件**驱动的数据库对象，也是一种保证数据完整性的方法。

触发器一旦定义，无需用户调用，任何对表的修改操作均由数据库服务器自动激活相应的触发器。

触发器与表的关系十分密切，其主要作用是实现主键和外键不能保证的复杂的参照完整性和数据的一致性，从而有效地保护表中的数据。

### 创建触发器 CREATE TRIGGER 语句

语法：

```MySQL
CREATE TRIGGER trigger_name trigger_time trigger_event
    ON tbl_name FOR EACH ROW trigger_body
```

trigger_name 用于指定触发器的名称，触发器在**当前数据库中**必须具有**唯一的名称**。

trigger_time 用于指定触发器被触发的时刻，有两个选项，即关键字“BEFORE”和关键字“AFTER”，用于表示触发器是在激活它的语句之前还是之后触发。一般验证新数据是否满足使用的限制时使用BEFORE，如果希望在激活触发器的语句执行之后完成几个或更多的改变，通常使用 AFTER 选项。

trigger_event 用于指定触发事件，也就是指定激活触发器的语句的**种类**，可以是以下几种值：

INSERT 表示 在 新的数据行插入到表时 激活 触发器。

UPDATE 表示 更改 表中某一行数据时激活 触发器。

DELETE 表示 从表中删除某一行数据时激活 触发器。

tbl_name 用于指定与触发器相关联的表名，必须引用永久性表，不能将触发器与临时表或视图关联起来，且**同一个表不能拥有两个具有相同触发时刻（BEFORE，AFTER）和事件的触发器**。

FOR EACH ROW 用于指定对于受触发事件影响的每一行都要激活触发器的动作。例如，使用一条INSERT 语句 向一个表中插入多行数据时，触发器会对每一行数据的插入都执行相应的触发器动作。

trigger_body 指定触发器动作**主体**，即包含触发器激活时将要执行的MySQL语句。如果要执行多个语句，可使用BEGIN...END 复合语句结构。

注意：

在触发器的创建中，每个表每个事件每次只允许一个触发器。因此，每个表最多支持6个触发器，即每条 INSERT、UPDATE 和 DELETE 的”之前“与”之后“。单一触发器不能与多个事件或多个表关联，例如，需要一个对INSERT 和 UPDATE 操作执行的触发器，则应该定义两个触发器。

例： 在数据库 mysql_test 的表 customers 中创建一个触发器 customers_insert_trigger，用于每次向表 customers 插入一行数据表，将用于变量 str 的值设置为“one customer added!”

```MySQL
mysql> CREATE TRIGGER mysql_test.customers_insert_trigger AFTER INSERT
    ->      ON mysql_test.customers FOR EACH ROW SET @str='one customer added!';
```

```MySQL
mysql> INSERT INTO mysql_test.customers
    -> VALUES(NULL,'万华','F','长沙市','芙蓉区');
```

最后能用如下方法验证触发器

```Mysql
mysql> SELECT @str
```

## 删除触发器

语法：

```MySQL
DROP TRIGGER [IF EXISTS] [schema_name.]trigger_name
```

IF EXISTS 用于避免在没有触发器的情况下删除触发器

schema_name 指定触发器所在的数据库的名称，若没有指定，则为当前默认数据库

trigger_name 指定要删除的触发器名称

例： 删除数据库 mysql_test 中的触发器 customers_insert_trigger

```MySQL
mysql> DROP TRIGGER IF EXISTS mysql_test.customers_insert_trigger;
```

## 使用触发器

MySQL 所支持的触发器有三种，分别是 INSERT 触发器、 DELETE 触发器和 UPDATE 触发器。

### 1、INSERT 触发器

INSERT 触发器可在 INSERT 语句执行之前或之后之后 执行。

使用该触发器时，需要注意：

1、在 INSERT 触发器 代码内，可引用一个名为 NEW（不区分大小写） 的虚拟表，来访问被插入的行。

2、在 BEFORE ISNERT 触发器中，NEW 中的值也可以被更新，即允许更改被插入的值（只要具有对应的操作权限）

3、对于 AUTO_INCREMENT 列，NEW 在 INSERT 执行之前的值为0，在INSERT 执行之后将包含新的自动生成值。

例： 在数据库 mysql_test 的表 customers 中重新创建触发器 customers_insert_trigger，用于每次向表 customers 插入一行数据时，将用户变量str的值设置为新插入客户的 id 号。

```MySQL
mysql> CREATE TRIGGER mysql_test.customers_insert_trigger AFTER INSERT
    -> ON mysql_test.customers FOR EACH ROW SET @str=NEW.cust_id;
```

### 2、DELETE 触发器

DELETE 触发器可在 DELETE 语句执行之前或之后执行。

使用该触发器时，需要注意：

1、 在DELETE 触发器代码内，可以引用一个名为OLD（不区分大小写）的虚拟表，来访问被删除的行。

2、 OLD 中的值 全部是 **只读** 的，不能被更新。

### 3、UPDATE 触发器

UPDATE 触发器可在 UPDATE 语句执行之前或之后执行。

使用该触发器时，需要注意：

1、在 UPDATE触发器 代码内，可以**引用一个名为OLD的虚拟表访问update语句执行前的值**，也可以**引用一个名为new的虚拟表访问UPDATE语句执行后新更新的值**。

2、在BEFORE UPDATE 触发器中，NEW 中的值可能也被更新，即允许更改 将要用于 UPDATE语句 中的值（只要具有权限）

3、 OLD 中的值 全部是**只读**的，不能被更新。

4、 当触发器涉及对触发表自身的更新操作时，只能使用 BEFORE UPDATE 触发器，而 AFTER UPDATE 触发器将不被允许。

--------------------

## 安全性与访问控制

### 用户账号管理

#### 创建用户账号 CREATE USER

格式：

```MySQL
CREATE USER user[IDENTIFIED BY [PASSWORD] 'password']
```

user 指定 创建用户账号，其格式为'user_name'@'host name'。

user_name 表示用户名

host_name 表示主机名 即用户连接MySQL时所在主机的名字。如果在创建的过程中，只给出了账户中的用户名，而没指定主机名，则主机名会默认为是”%“，其表示一组主机。

IDENTIFIED BY 子句是可选项，指定用户账号对应的口令，若该用户账号无口令，则可省略此子句。

PASSWORD 是可选项，用于指定散列口令，即若使用明文设置口令时，需忽略 PASSWORD 关键字；如果不想以明文设置口令，且知道 PASSWORD() 函数返回给密码的散列值，则可以在此口令设置语句中指定此该散列值，但需要加上关键字PASSWORD。

password 指定用户账号的口令，其在 IDENTIFIED BY 关键字 或 PASSWORD 关键字之后。设定的口令值可以是只由字母和数字组成的明文，也可以是通过PASSWORD()函数得到的散列值。

例： 在 MySQL 服务器中添加两个新的用户，其用户名分别为 zhangsan 和 lisi，他们的主机名均为 localhost，用户 zhangsan 的口令设置为明文 123，lisi的口令设置为 明文456.

```MySQL
mysql> CREATE USER 'ZHANGSAN'@'LOCALHOST' IDENTIFIED BY '123',
    ->      'LISI'@'LOCALHOST' IDENTIFIED BY '456'
```

CREATE USER 语句注意事项：

1、要使用 CREATE USER 语句，必须拥有 MySQL 中 mysql 数据库的 INSERT 权限或全局 CREATE USER 权限。

2、使用 CREATE USER 语句创建一个用户账号后，会在系统自身的 mysql 数据库的user 表中台南佳一条新记录。如果创建的账户已经存在，则语句执行会出现错误。

3、如果两个用户具有相同的用户名和不同的主机名，MySQL会将他们视为不同的用户，并允许为这两个用户分配不同的权限集合。

4、如果在 CREATE USER 语句的使用中，没有为用户指定口令，那么 MySQL 允许该用户可以不使用口令登录系统，然而从安全的角度而言，不推荐这种做法。

5、新创建的用户拥有的权限很少。他们可以登录到 MySQL，只允许进行不需要权限的操作，比如使用 SHOW 语句查询所有存储引擎和字符集的列表等，不能使用 USE 语句来让其他用户已经创建了的任何数据库成为当前数据库，因而无法访问那些数据库的表。

#### 删除用户 DROP USER

语法格式：

```MySQL
DROP USER user [,user] ···
```

例： 删除用户lisi

```MySQL
DROP USER lisi@localhost;
```

DROP USER 语句注意事项:

1、DROP USER 语句可用于删除一个或多个MySQL账户，并消除其权限。

2、要使用 DROP USER 语句，必须拥有 MySQL 中 mysql 数据库的 DELETE 权限或全局 CREATE USER 权限。

3、在 DROP USER 语句的使用中，如果没有明确的给出账户的主机名，则该主机名会默认是 %

4、用户的删除不会影响到他们之前所创建的表、索引或其他数据库对象，这是因为 MySQL 并没有记录是谁创建了这些对象。

#### 修改用户账号 RENAME USER

RENAME USER 可以修改一个或多个已经存在的MySQL用户账号。

语法：

```MySQL
RENAME USER old_user TO new_user [,old_user TO new_user] ···
```

old_user 指定系统中已经存在的 MySQL 用户账号

new_user 指定新的MySQL 用户账号

例： 将用户 zhangsan 的名字 修改成 wangwu。

```MySQL
mysql> RENAME USER 'zhangsan'@'localhost' TO 'wangwu'@'localhost';
```

RENAME 语句的使用中，需要注意以下几点：

1、RENAME USER 语句用于对原有MySQL账户进行重命名。

2、要使用RENAME 语句，必须拥有MySQL中mysql数据库的 UPDATE 权限 或 全局 CREATE USER 权限。

3、倘若系统中旧帐户不存在或新账户已存在，则语句执行会出现错误。

#### 修改用户口令 SET PASSWORD

SET PASSWORD 语句可以修改一个用户的登录口令。

语法：

```MySQL
SET PASSWORD [FOR user] =
    {
        PASSWORD('new_password')
        | 'encrypted password'
    }
```

FOR 子句为可选项 ，指定与修改口令的用户；

PASSWORD('NEW_PASSWORD') 表示使用函数 PASSWORD() 设置新口令 new_password，即新口令必须加密；

encrypted password 表示已被加密的口令值。

SET PASSWORD 语句 注意事项：

1、若不加上 FOR 子句，表示i需改当前用户的口令；若加上FOR子句，则表示修改账户为user的用户口令，user的格式必须以'user_name'@'host_name'的格式给定。该账户必须在系统中存在，否则语句执行会出现错误。

2、只能使用 PASSWORD('new_password') 和 'encrypted password' 中的一项，且必须使用其中的某一项。

### 账户权限管理

#### 权限的授予

新建的 MySQL用户必须被授权，可以使用 GRANT 语句来实现。

语法：

```MySQL
GRANT
  priv_type [(column_list)]  
    [,priv_type [(column_list)]] ···
  ON [object_type] priv_level
  TO user_specification [,user_specification] ···
  [WITH GRANT OPTION]
```

priv_type 指定权限的名称 例如 SELECT、UPDATE、DELETE 等数据库操作。

column_list 可选语法项 指定权限要授予给表中哪些具体的列。

ON 子句 指定权限授予的**对象和级别**，例如可在关键字 ON 后面给出要授予权限的数据库名或表名等。

object_type 用来指定**权限授予的对象类型**，包括表、函数和存储过程，分别用关键字 TABLE,FUNCTION,PROCEDURE 表示

priv_level 指定**权限的级别**，其可以授予的权限有这样几个：列权限、表权限、数据库权限和用户权限。

关于权限，在GRANT语句中可用于指定权限级别的值有这样几类格式：

\*    : 当前数据库中的所有表

\*.\*     : 所有数据库中的所有表

db_name.\* :  某个数据库中的所有表， db_name 指定数据库名

db_name.tbl_name : 某个数据库中的某个表或视图 db_name 指定数据库名， tbl_name指定表名或视图名

tbl_name : 表示某个表或视图

db_name.routine_name 表示某个数据库中的某个存储过程或函数

TO 子句 设定用户的口令，以及指定被授予权限的用户 user。 若在 TO 子句中给系统中存在的用户指定口令，则新密码会将原密码覆盖；如果权限被授予给一个不存在的用户，MySQL会**自动执行一条CREATE USER 语句来创建这个用户**，但同时必须为该用户指定口令。由此可见，**GRANT 语句也可以用于创建用户账号**

user_specification 是子句中的具体描述部分，语法格式：

```MySQL
user[IDENTIFIED BY [PASSWORD] 'password']
```

WITH 子句 是可选项，用于实现权限的转移或限制。

例： 授予用户 zhangsan 在数据库 mysql_test 的表 customers 上拥有对列 cust_id 和 列cust_name 的 SELECT 权限。

```MySQL
mysql> GRANT SELECT (cust_id,cust_name)
    ->      ON mysql_test.customers
    ->      TO 'zhangsan'@'localhost';
```

例： 当前系统中不存在用户 liming 和用户 huang，要求创建这两个用户，并设置对应的系统登录口令，同时授予他们在数据库 mysql_test 的表 customers 上拥有 SELECT 和 UPDATE 的权限。

```MySQL
mysql> GRANT SELECT,UPDATE
    ->  ON mysql_test.customers
    ->  TO 'liming'@'localhost' IDENTIFIED BY '123',
    ->      'huang'@'localhost' IDENTIFIED BY '789';
```

例： 授予系统中已存在用户 wangwu 可以在数据库 mysql_test 中执行所有数据库操作的权限。

```MySQL
mysql> GRANT ALL
    ->  ON mysql_test.*
    -> TO 'wangwu'@'localhost';
```

例： 授予系统中已存在用户 wangwu 拥有创建用户的权限。

```MySQL
mysql> GRANT CREATE USER
    ->  ON *.*
    ->  TO 'wangwu'@'localhost'
```

#### GRANT 语句中语法项“priv_type”注意事项：

1、授予**表权限**时，语法项“priv_type"可以指定为这些值：

SELECT：SELECT语句访问特定表的权限

INSERT：INSERT语句向一个特定表中添加数据行的权限

DELETE：DELETE语句向一个特定表中删除数据行的权限

UPDATE：UPDATE语句修改特定数据表中值的权限

REFERENCES：创建一个外键来参照特定数据表的权限

CREATE：使用特定名字创建一个数据表的权限

ALTER：ALTER TABLE语句修改数据表的权限。

INDEX：在表上定义索引的权限。

DROP：删除数据表的权限

ALL 或 ALL PRIVILEGES：所有的权限名

2、授予**列权限**时，语法项“priv_type”的值只能指定为 SELECT、INSERT 和 UPDATE，同时权限的后面需要加上列名列表 column_list

3、授予**数据库权限**时，语法项“priv_type”的值可以指定为以下值：

SELECT： 对特定数据库中所有表和视图 **SELECT** 的权限

INSERT： 对特定数据库中所有表 **添加数据行** 的权限

DELETE：对特定数据库中所有表的数据行 **删除** 的权限

UPDATE： 对特定数据库中所有数据表的值 **更新（修改）** 的权限

REFERENCES： **创建**指向特定的数据库中的表 **外键** 的权限

CREATE： 在特定数据库中 **创建新表** 的权限

ALTER：特定数据库中 **修改所有数据表** 的权限

INDEX：在特定数据库中的所有表上 **定义和删除索引** 的权限

DROP：特定数据库中所有表和视图的 **删除表和视图** 权限

CREATE TEMPORARY TABLE： 在特定数据库中 **创建临时表** 的权限

CREATE VIEW： 特定数据库中 **创建新的视图** 的权限

SHOW VIEW： 特定数据库中 **查看已有视图的视图定义** 的权限

CREATE ROUTINE： 为特定数据库 **创建存储过程 和 存储函数 等** 权限

ALTER ROUTINE： **更新和删除数据库中已有的存储过程和存储函数**等权限

EXECUTE ROUTINE： **调用**特定数据库的**存储过程和存储函数**的权限

LOCK TABLES： 可以**锁定**特定数据库的**已有数据表**的权限

ALL 或 ALL PRIVILEGES： 以上所有的权限名。

### 权限的转移 WITH 子句

权限的转移可以通过在 GRANKT 语句中使用 WITH 子句来实现。如果将 WITH子句指定为关键字 “WITH GRANT OPTION”，则表示 **TO 子句中**所指定的所有用户都具有**把自己所拥有的权限赋予其他用户的权利**，而无论那些其他用户是否拥有该权限。

例：授予当前系统中一个不存在的用户 zhou 在数据库 mysql_test 的表 customers 上拥有 SELECT 和 UPDATE 的权限，并允许其可以将自身的这个权限授予给其他用户

```MySQL
mysql> GRANT SELECT,UPDATE
    ->  ON mysql_test.customers
    ->  TO 'zhou'@'localhost' IDENTIFIED BY '123'
    ->  WITH GRANT OPTION
```

### 权限的撤销 REVOKE

REVOKE 语句可以回收某些特定权限而不删除用户

语法格式：

```MySQL
REVOKE
    priv_type [(column_list)]
        [,priv_type [(column_list)]] ···
    ON [object_type] priv_level
    FROM user [,user] ···
```

当需要收回特定用户的所有权限时，可使用的语法格式是：

```MySQL
REVOKE ALL PRIVILEGES,GRANT OPTION FROM user [,user] ···
```

例： 回收系统中已存在的用户 zhou 在数据库 mysql_test 的表 customers 上的 SELECT 权限。

```MySQL
mysql> REVOKE SELECT
    ->      ON mysql_test.customers
    ->      FROM 'ZHOU'@'LOCALHOST';
```

## 备份

### 备份数据 SELECT INTO...OUTFILE

在MySQL中，导出备份语句 SELECT INTO...OUTFILE 是通过 SELECT 语句将表中所有数据行写入到一个文件中。

语法：

```MySQL
SELECT *INTO OUTFILE 'file_name' export_options
    | INTO DUMPFILE 'file_name'
```

其中，语法项 “export_options”的格式是：

```MySQL
[FIELDS
        [TERMINATED BY 'string']
        [[OPTIONALLY] ENCLOSED BY 'char']
        [ESCAPED BY 'char']
]
[LINES TERMINATED BY 'string']
```

file_name 指定数据备份文件的名称。 文件默认在服务器主机上创建，并且文件名不能是已经存在的，否则可能会将源文件覆盖。如果要将该文件写入到一个特定的位置，则要在文件名前加上具体的路径。在文件中，导出的数据行会以一定的形式存储，其中空值是用 “\N” 表示。

导出语句中使用关键字 OUTFILE 时，可以在语法项 export_options 中加入以下两个自选的子句，即 FIELDS 子句 和 LINES 子句，它们的作用是决定数据行在备份文件中存储的格式。如果 FIELDS 和 LINES 子句都不指定，则默认声明的是子句 “FIELDS TERMINATED BY '\T' ENCLOSED BY " ESCAPED BY '\\n'”

FIELDS 子句中有三个亚子句，分别是 “TERMINATED BY 子句” “[OPTIONALLY] ENCLOSED BY子句” 和 “ESCAPED BY 子句”。如果指定了 FIELDS 子句，则这三个亚子句中至少要指定一个。其中， TERMINATED BY 子句用来指定字段值之间的符号，例如 “TERMINATED BY ','” 指定逗号作为两个字段值之间的表只； ENCLOSED BY 子句用来指定包裹文件中字符值放在双引号之间，若加上关键字“OPTIONALLY”则表示所有的值都放在双引号之间；ESCAPED BY 子句用来指定转义字符，例如 “ESCAPED BY '\*'” 将 “\*” 指定为转义字符，取代“\\”，如空格将表示为 “*N”。

在 LINES 子句中 使用关键字 “TERMINATED BY” 指定一个数据行结束的标志，如 “LINES TERMINATED BY '?'” 表示一个数据行以 ？ 作为结束标志。

导出语句中使用的是关键字“DUMPFILE”，而非 “OUTFILE” 时，导出的备份文件里面所有的数据行都会彼此紧挨着放置，即 值 和 行 之间没有任何标记。

使用 LOAD DATE···INFILE 语句恢复数据

```MySQL
LOAD DATA INFILE 'file_name.txt'
    INTO TABLE tbl_name
    [FIELDS
        [TERMINATED BY 'string']
        [[OPTIONALLY] ENCLOSED BY 'char']
        [ESCAPED BY 'char']
    ]
    [LINES
        [STARTING BY 'string']
        [TERMINATED BY 'string']
    ]
```

file_name 指定待导入的数据库备份文件，文件中保存了待载入数据库的所有数据行。输入文件可以手动创建，也可以使用使用其他的程序创建。导入文件时可以指定文件的绝对路径，则服务器会根据该路径搜索文件；若不指定路径，则服务器在默认数据库的数据库目录中读取。

tb_name 指定需要导入数据的表名，该表在数据库中必须存在，表结构必须与导入文件的数据行一致。

此处的 FIELDS 子句 和 SELECT···INTO OUTFILE 语句中的 FIELDS 子句类似，用于判断字段之间和数据行之间的符号。

LINES 子句中的 TERMINATED BY 亚子句用来指定一行结束的标志； STARTING BY 亚子句则指定一个前缀，导入数据行时，忽略数据行中的该前缀和前缀之前的内容。弱国某行不包括该前缀，则整个数据行被跳过。

例： 备份数据库 mysql_test 中表 customres 的全部数据到 C 盘 的 BACKUP 目录下一个名为 backupfile.txt 的文件中，要求字段值如果是字符则用双引号标注，字段值之间用逗号隔开，每行以问号为结束标志。然后，将备份后的数据导入到一个和 customers 表结构相同的空表 customers_copy 中。

```MySQL
mysql> SELECT * FROM mysql_test.customers
    -> INTO OUTFILE 'C:/BACKUP/backupfile.txt'
    -> FIELDS TERMINATED BY ','
    -> OPTIONALLY ENCLOSED BY ""
    -> LINES TERMINATED BY '?';
```

然后，将备份的数据导入到数据库 mysql_test 中要给和 Customers 表结构相同的空表 customers_copy 中：

```MySQL
mysql> LOAD DATA INFILE 'C:/BACKUP/backupfile.txt'
    -> INTO TABLE mysql_test.customers_copy
    -> FIELDS TERMINATED BY ','
    -> OPTIONALLY ENCLOSED BY ""
    -> LINES TERMINATED BY '?';
```

在导入数据时需要特别注意：

必须根据数据备份文件中数据行的格式来指定判断的符号。例如，在 backupfile.txt 文件中字段值是以逗号隔开的，导入数据时就一定要使用 “TERMINATED BY ','”子句指定逗号为字段值之间的分隔符，即与 SELECT ··· INTO OUTFILE 语句相对应。

另外需要注意：

在多个用户同时使用 MySQL 数据库的情况下，为了得到一个一致的备份，需要在指定的表上使用 LOCK TABLES table_name READ 语句做一个读锁定，以防止在备份过程中表被其他用户更新；而当恢复数据时，则需要使用 LOCK TABLES table_name WRITE 语句做一个写锁定，以避免发生数据冲突。在数据库备份或恢复完毕之后需要使用 UNLOCK TABLES 语句对该表进行解锁。



