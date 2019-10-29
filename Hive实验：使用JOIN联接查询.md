# Hive实验：使用JOIN联接查询
## 1、实验描述

* Hive的JOIN操作；本实验使用sogou日志文件，创建两个表sogou和sogou_limit，对两张表分别进行inner join、left outer join、right outer join、full outer join操作

* 实验时长：45分钟

* 主要步骤：
    * 启动hadoop集群
    * 进入Hive命令行
    * 创建数据库、数据表
    * 加载或导入数据
    * 练习SQL语句

## 2、实验环境

* 虚拟机数量：3
* 系统版本：Centos 7.5
* Hadoop版本：Apache Hadoop 2.7.3
* MySQL版本：MySQL 5.7.18
* Hive版本：Apache Hive 2.1.1

## 3、相关知识点

* INNER JOIN
* LEFT OUTER JOIN
* RIGHT OUTER JOIN
* FULL OUTER JOIN

## 4、实验步骤
### 4.1 启动Hadoop集群

4.1.1在master启动Hadoop集群

4.1.2在master上运行jps，确认NameNode, SecondaryNameNode, ResourceManager进程启动

4.2.3 分别在slave01、slave02上运行jps，确认DataNode, NodeManager进程启动

### 4.2 JOIN操作

4.2.1 启动Hive

4.2.2 如果hive_test数据库不存在则创建，并use数据库;

4.2.3 创建sogou表并加载数据

4.2.3.1 sogou表有6个字段：ts，时间戳； uid，用户ID；
keyword，关键字；rank，搜索排名；order，次数；URL，网址。使用‘\t’分隔符分割字段。

4.2.3.2 从Hive的公共目录下拷贝数据文件sogou_1w.txt到/home/zkpk，使用load将上一步拷贝的数据文件sogou_1w.txt加载到表里。

4.2.4 创建sogou_limit表

4.2.4.1 sogou_limit表有6个字段：ts，时间戳； uid，用户ID；
keyword，关键字；rank，搜索排名；order，次数；URL，网址。使用‘\t’分隔符分割字段。

4.2.4.2 向表中加载数据：使用insert overwrite 语句。

4.2.5 使用INNER JOIN联结两个表

```
hive> select * from sogou m join sogou_limit n on m.uid = n.uid;
hive> select * from sogou_limit;
```

内连接中只有进行连接的两个表中都存在与连接标准相匹配的数据才会被保留下来。

4.2.6 LEFT OUTER JOIN

```
hive> select m.uid,m.keyword,n.uid,n.keyword from sogou m left outer join sogou_limit n on m.uid = n.uid limit 10;
```

在LEFT OUTER JOIN操作符中，JOIN操作符左边表中的符合WHERE子句的所有记录将会被返回。JOIN右边表中如果没有符合ON后面连接条件的记录时，那么从右边表选择的列的值将会是NULL

4.2.7 RIGHT OUTER JOIN

```
hive> select m.uid, m.keyword, n.uid, n.keyword from sogou m right outer join sogou_limit n on m.uid = n.uid limit 10;
```

右外连接会返回右边表所有符合WHERE语句的记录。左表中匹配不上的字段值用NULL代替

4.2.8 FULL OUTER

```
hive> select m.uid,m.keyword, n.uid, n.keyword from sogou m full outer join sogou_limit n on m.uid = n.uid limit 10;
```

JOIN，全外连接，包括两个表的JOIN结果，左边在右边没有找到的结果为NULL，右边在左边没有找到的结果为NULL。

## 5、总结

Hive的JOIN操作中的左外连接和右外连接功能相当，只是左表和右表的角色刚好相反。