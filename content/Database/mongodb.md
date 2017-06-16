---
title: "mongodb"
layout: page
date: 2017-04-28 15:00
---


# Mongodb

适用于DB 3.0

mongodb 教程[http://www.yiibai.com/mongodb/mongodb_data_modeling.html](http://www.yiibai.com/mongodb/mongodb_data_modeling.html)

mongodb 官方doc [https://docs.mongodb.com/manual](https://docs.mongodb.com/manual)

help命令，进入mongodb后查看help命令
```
>help 
```

mongodb中可以直接执行js

## install

该步骤掠过


## mongod

```
mongod 启动
mongod -auth 增加验证权限启动
```

## mongo

连接本地mongodb
```
mongo
```

### 增加用户  验证权限

mogond启动未加-auth权限验证,增加用户，增加权限，开启权限验证后,使用该帐号验证权限，即有此仓库的读写权限
```
>use database 进入数据库
>db.createUser({name:'myname',pwd:'mypasswd',roles[{role:'readWrite',db:'local'},{role:'read,db:'test}]})
```
创建myname用户，密码为mypasswd，拥有数据库local的读写权限，数据库test的读权限

创建root超级用户,role赋值为root即可
```
>use database 进入数据库
>db.createUser({name:'myname',pwd:'mypasswd',roles[{role:'root',db:'database}]})
```

验证用户权限
```
>use database
>db.auth('myname','mypasswd')
```
验证成功返回1,验证失败返回0,当创建用户拥有这个数据库读写权限时及可读写，否则无

### db

```
>show dbs 查看数据库
>use test 进入test数据库
```

删除数据库
```
>use test 
>db.dropDatabase()
```

### collection 

```
>db.createCollection('mycollection') 创建集合
>show collections 查看集合
>db.collections.find() 查看集合所有数据
>db.collection.drop() 删除集合

```

### operation

```
>db.collection.insert({'name:'myname','passwd':'mypasswd'}) 插入数据
>db.collection.find() 非结构显示所有数据
>db.collection.findOne() 非结构显示一条数据
>db.collection.find().pretty() 格式化显示所有数据
```

查询语句
```
db.collection.find({"name":"myname"})
等同
where name = "myname"

db.collection.find({"number":{$lt:50}})
等同
where number < 50
```

```
<  lt
<= lte
>  gt
>= gte
ne !=
```

AND语法
```
>db.mycol.find({key1:value1, key2:value2})
```

OR语法
```
>db.mycol.find(
    {
        $or: [
            {key1: value1}, {key2:value2}
        ]
    }
)
```

更新数据
```
>db.database.update({'title':'name'},{$set:{'title':'New Name'}})
>db.database.update({'title':'name'},{$set:{'title':'New Name'}},{multi:true})
```
更新,title的 ’name' 更新为 'New Name',默认只会更新一条，设置参数mutlti为true 可以更新所有

save()可以替换现有文档
```
>db.COLLECTION_NAME.save({_id:ObjectId(),NEW_DATA})
>db.COLLECTION_NAME.save({_id:ObjectId(5983548781331adf45ec7),"title":"Yiibai New Topic", "by":"Yiibai"})
```
使用save第一个参数必须为_id


删除文件
```
>db.collection.remove(DELLETION_CRITTERIA)
```
删除一个文件
```
>db.collection.remove(DELLETION_CRITTERIA,1)
```
删除所有文件
```
>db.collection.remove()
```

### renameCollection

修改 database 或者 collections 名字

```
>db.adminCommand({renameCollection: "db1.test1", to: "db2.test2"})
```

db2未创建时自动创建,迁移db1所有collection，使用如下js脚本即可

```
var source = "source";
var dest = "dest";
var colls = db.getSiblingDB(source).getCollectionNames();
for (var i = 0; i < colls.length; i++) {
    var from = source + "." + colls[i];
    var to = dest + "." + colls[i];
    db.adminCommand({renameCollection: from, to: to});
}
```

## mongodump

备份数据

```
> mongodump --host 127.0.0.1 --port 27017 -u root --password root@mongodb --authenticationDatabase admin -o /root/backups/ -d local
```

--authenticationDatabase 制定帐号认证仓库

-o 保存路径，不设置默认当前路径

-d 备份仓库,不设置默认全部仓库;同时不设置路径情况下，会在当前路径下创建dump文件夹







