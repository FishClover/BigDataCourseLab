# Hive实验：索引和视图
## 1、实验描述

* 本实验涉及Hive的索引操作，了解Hive索引的语法，以及视图的常见操作，如创建视图、查看视图结构、查看视图数据、删除视图等。

* 实验时长：45分钟

* 主要步骤：
    * 启动hadoop集群
    * 进入Hive命令行
    * 创建数据库、数据表
    * 加载或导入数据
    * 创建数据索引
    * 创建视图、查看视图、删除视图

## 2、实验环境

* 虚拟机数量：3
* 系统版本：Centos 7.5
* Hadoop版本：Apache Hadoop 2.7.3
* MySQL版本：MySQL 5.7.18
* Hive版本：Apache Hive 2.1.1

## 3、相关知识点
* CREATE VIEW
* DROP VIEW
* CREATE INDEX
* ALTER INDEX

## 4、实验步骤
### 4.1 启动Hadoop集群

4.1.1在master启动Hadoop集群

4.1.2在master上运行jps，确认NameNode, SecondaryNameNode, ResourceManager进程启动

4.2.3 分别在slave01、slave02上运行jps，确认DataNode, NodeManager进程启动

### 4.2 导入数据操作

4.2.1 启动Hive

4.2.2 创建数据库hive_test，并use此数据库

4.2.3 创建person表，person表有2个字段：字段id类型为int；字段name类型string，指定字段分割符为 ’\t’

```
create table person(
id int,
name string)  
ROW FORMAT DELIMITED  
FIELDS TERMINATED BY '\t'
STORED AS TEXTFILE;
```

4.2.4 加载数据到表并验证

4.2.4.1 从Hive的公共目录下拷贝数据文件person.txt到/home/zkpk

4.2.4.2 使用load将上一步拷贝的数据文件person.txt加载到P1表

4.2.4.3 验证导入数据是否成功

### 4.3 索引操作

4.3.1 创建索引person_index

```
create index person_index 
on table person(id) 
as  'org.apache.hadoop.hive.ql.index.compact.CompactIndexHandler'
with deferred rebuild
idxproperties ('creator'='you','created_at'='company')
in table person_index_table;
```

以id建立索引，索引名为person_index，创建索引需要的实现类'org.apache.hadoop.ql.index.compact.CompactIndexHandler'，延时重建功能with deferred rebuild，创建索引后的表名为person_index_table

4.3.2 查看索引

4.3.3 加载索引数据

4.4.4 查询索引表数据

4.4.5 删除索引

### 4.4 视图操作

4.2.5 任选规则，创建一个视图，视图名为p1_view

4.2.6 查看视图的结构和数据（desc 和 select from 命令） 

4.2.7 删除视图

## 5、总结

Hive支持索引，但是Hive的索引与关系型数据库中的索引并不相同，比如，Hive不支持主键或者外键。Hive索引可以建立在表中的某些列上，以提升一些操作的效率，例如减少MapReduce任务中需要读取的数据块的数量。在可以预见到分区数据非常庞大的情况下，索引常常是优于分区的。

视图允许保存一个查询并像对待表一样对这个查询进行操作。视图（逻辑视图，享用基本表的数据不会另生成一份数据）；删除视图不会对基本表数据造成影响。