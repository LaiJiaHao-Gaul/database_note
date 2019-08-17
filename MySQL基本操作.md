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

## 四、数据更新

### 插入数据

#### INSERT 语句

向数据库中一个已有的表插入一行或多行元组数据。

INSERT 语句有三种语法形式，分别对应的是：

INSERT ... VALUES 语句

INSERT ... SET 语句

INSERT ... SELECT 语句

在 MySQL 中，使用 INSERT...VALUES 语句插入单行或多行元组数据的语法格式是：

```MySQL
INSERT [INTO] tb1_name [(col_name,...)]
    {VALUES|VALUE} ({expr|DEFAULT},...),(...),...
```

此语法中：

tb1_name 指定欲被插入数据的表名。

col_name 指定需要插入数据的列名。如果向表中所有列插入数据，则全部列名都可以省略

对于没有指定数据的列，它们的值可根据列的默认值或相关属性来确定，通常MySQL是按照以下原则进行的：

i)对于具有标志（IDENTITY）属性的列，系统会自动生成序号之来唯一标志该列；

ii)具有默认值的列，其值可通过在 INSERT 语句中指定关键字“DEFAULT”将其设为默认值；

iii)没有默认值的列，若允许为空值，则其值可通过在 INSERT 语句中指定关键字“NULL”将其设为空值，若不允许为空值，则INSERT语句执行出错；

iv)对于类型为 TIMESTAMP 的列，系统会为其自动赋值；

v)由于 AUTO_ INCREMENT 属性列的值是在表中其他列被赋值之后生成的，所以在对表中其他列做任何赋值操作（如 INSERT 语句）时，对该AUTO_INCREMENT 属性列的引用只会返回数字 0。

3）通过关键字“VALUES”或“VALUE”引导的子句，其包含各列需要插入的数据清单。数据清单中数据的顺序必须与列的顺序相对应，同时该子句中的值可以是：

i）“expr”，表示一个常量、变量或一个表达式，也可以是空值NULL，其值的数据类型要与列的数据类型一只，如果表达式的类型与列值不匹配，会造成类型转换或插入语句出错，另外当列值为字符型时，需要用单引号括起；

ii）关键字“DEFAULT”，即用于指定此列值为该列的默认值，前提时该列之前已经明确指定了默认值，否则插入语句会报错。

例：使用 INSERT ... VALUES 语句相数据库 nunuDB 的表 user 中插入数据：

user_name = nunu

user_age = 18

user_sex = 0

则MySQL输入如下：

```MySQL
mysql> INSERT nunuDB.user(user_name,user_age,user_sex)
    -> VALUES('nunu',18,1)
```

第三个插入数据的方法会更加**灵活且常用**

#### INSERT...SELECT

语法格式：

```MySQL
INSERT tb1_name [(col_name,...)]
SELECT ...
```

在此语法中，SELECT 子句用于快速地从一个或多个表中取出数据，并将这些数据作为行数据插入到另一个表中，SELECT 子句返回地时一个查询到的结果集，INSERT语句将这个结果集插入到指定表中，其中结果集里每行数据的字段数、字段的数据类型必须与被操作的表完全一致。

### 删除数据

#### DELETE 语句

语法格式：

```MySQL
DELETE FROM tb1_name
    [WHERE where_condition]
    [ORDER BY ...]
    [LIMIT row_count]
```

在此语法中：“tb1_name”指定要删除数据的表明；可选项 WHERE 子句表示为删除操作限定删除条件，从而删除特定的行，若省略 WHERE 子句，则表示删除该表中的所有行，但表的定义仍在数据字典中，即 DELETE 语句删除的时表中的数据，而不是关于表的定义； 可选项 ORDER BY 子句表示各行将按照子句中指定的顺序进行删除；可选项 LIMIT 子句用于告知服务器在控制民工被返回到客户端前被删除的行的最大值。

例：使用DELETE语句删除数据库 nunuDB 的表 user 中客户名为 "nunu" 的客户信息。

```MySQL
mysql> DELETE FROM nunuDB.user
    ->      WHERE user_name = 'nunu'
```

### 修改数据

在MySQL中，可以使用 UPDATE 语句来修改更新一个表中的数据，实现对表中行的列数据进行修改，其语法格式是：

```MySQL
UPDATE tb1_name
    SET col_name1={expr1|DEFAULT}[,col_name2={expr2|DEFAULT}] ...
    [WHERE where_condition]
    [ORDER BY ...]
    [LIMIT row_count]
```

在此语法中：

“tb1_name"指定要修改的表的名称；

SET 子句用于指定表中要修改的列名及其列值，其中每个指定的列值可以是表达式，也可以是该列所对应的默认值，如果指定的是默认值，则用关键字”DEFAULT“表示列值；可选项 WHERE 子句用于限定表中要修改的行，若不指定此子句，则 UPDATE 语句会修改表中所有的行；可选项 WHERE 子句用于限定表中要修改的行，若不指定此子句，则 UPDATE 语句会修改表中所有的行；可选项 ORDER BY 子句用于限定表中的行被修改的次序；可选项LIMIT子句用于限定被修改的行数。

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
[ASC|DESC], ... [WITH ROLLUP]]
[HAVING where_condition]
[ORDER BY { col_name | expr | position }
    [ASC | DESC],...]
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
...
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

“table1” 和 “table2”用于指定进行内连接的两张表哦的表明或表别名；

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

（1）BETWEEN...AND

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
expression IN (expression [,...n])
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
GROUP BY {col_name|expr|position} [ASC|DESC], ... [WITH ROLLUP]
```

在此语法格式中：

col_name:指定用于分组的选择列。可以指定多个列，彼此间用逗号分隔。 注意：**GROUP BY 子句中的各选择列必须也是SELECT语句的选择列表清单中的一项。

expr: 指定分组的表达式。该表达式通常与聚合函数一块使用，例如可将表达式“COUNT(*)AS '人数'” 作为SELECT语句的选择列表清单中的一项。

position: 指定用于分组的选择列在SELECT语句结果集中的文职，通常时一个正整数。例如，使用GROUP BY 3 表示根据SELECT语句中 列清单上的第三列的值进行逻辑分组。

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

在 SELECT 语句中，可以使用 ORDER BY 子句将结果集中的数据行按一定的顺序进行排列，否则结果集里数据行的顺序时不可预料的。

语法格式；

```MySQL
ORDER BY {col_name|expr|position}[ASC|DESC],...
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







