# database_note

## 数据库初始化语法

### use mongo

如果数据库不存在，则创建数据库，否则切换到指定数据库。

----------

### db.dropDatabase()

删除当前数据库

----------

### db

显示当前控制的数据库

----------

### show dbs

查看所有的数据库

----------

### use databaseA  switched to db databaseB

切换数据库

----------

### db.collection.drop()  

删除集合

----------

## 插入文档

### db.abc.insert( {aaa:'bbb'} ) 

该操作可以往abc集合中插入文档，如果abc集合不存在该数据库中，MongoDB会自动创建该集合并插入文档。

### db.col.find()

查看已插入文档

### 变量名 = ( { aaa : 'bbb' } )

可以将数据定义为一个变量

### db.col.save(document)命令

类似于insert()方法，如果不指定_id字段，则save()方法与insert方法行为一致，如果指定_id字段，则会更新该_id的数据。

----------

## MongoDB 更新文档

### update()方法

用于更新已存在的文档。

语法格式如下：

```javascript
db.collection.update(
    <query>,
    <update>,
    {
        upsert: <boolean>,
        multi: <boolean>,  
        writeConcern: <document>
    }
)
```

参数说明：

**query** : update的查询条件，类似sql update查询内where后面的。

**update** : update的对象和一些更新的操作符（如$,$inc...）等，也可以理解为sql update查询内set后面的

**upsert** : 可选，这个参数的意思是，如果不存在update的记录，是否插入objNew,true为插入，默认是false，不插入。

**multi** : 可选，mongodb 默认是false,只更新找到的第一条记录，如果这个参数为true,就把按条件查出来多条记录全部更新。

**writeConcern** :可选，抛出异常的级别。

----------

### save() 方法

save() 方法**通过传入的文档来替换已有文档**。语法格式如下：

```javascript

db.collection.save(
    <document>,
    {
        writeConcern: <document>
    }  
)  
```

#### 参数说明

1. document : 文档数据。

2. writeConcern :可选，抛出异常的级别。



问题：update操作匹配的是具有该键值对的对象，还是该键值对本身？

假设我的数据：
{
    "_id":"111222333",
    "title":"MongoDB",
    "true":1,
    "likes":110
}

我进行update操作

```javascript

xxx.update( { "title" : "MongoDB" } , { $set : { "test" : "ok" } } , true,false)

```

在我对数据进行更新的第一步一定是查询 title = MongoDB的数据，那么我查询到的是：

```js
{
    "_id":"111222333",
    "title":"MongoDB",
    "true":1,
    "like":110
}
```

还是：

``` js
{
    "title" : "MongoDB"
}
```

实验：
首先我创建了数据项,内容如下：

```javascript
{
    "test" : "aaa",
    "val"  : "111"
}
```

第二步 进行update操作

```js
db.col.update({"test":"aaa"},{"val2":"333"})
```

第三步 查看我的数据库

```js
db.col.fine().pretty()

返回如下：

{"_id":'xxx',"val2":"333"}
```

我存储在里面的test以及val都不见了...只剩下val2
