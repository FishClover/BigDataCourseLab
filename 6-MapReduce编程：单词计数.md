## MapReduce编程：单词计数

## 1、实验描述

- 使用mapreduce编程，完成单词计数

## 2、实验环境

- 虚拟机数量：3
- 系统版本：Centos 7.5
- Hadoop版本：Apache Hadoop 2.7.3
- Eclipse版本：Neon.3 4.6.3

## 3、相关技能

- MapReduce编程

## 4、相关知识点

- 配置开发环境
- 编写Mapper类
- 编写Reducer类
- 编写main函数
- 程序打jar包
- 将jar包运行在hadoop集群上

## 5、实现效果

![Image 10](http://10.105.222.200/upload/image/document/init/06bc6a67-ca77-4c86-aba1-75e16fcae906/50ff3a5f6fff8d4d90b4a6085e1b84a6.png)

图 1

## 6、实验步骤

##### 6.1打开虚拟机并启动Hadoop集群

6.1.1在master启动Hadoop集群

```
[zkpk@master ~]$ start-all.sh
```

6.1.2在master上运行jps，确认NameNode, SecondaryNameNode, ResourceManager进程启动

6.1.3在slave01上运行jps，确认DataNode, NodeManager进程启动

6.1.4在slave02上运行jps，确认DataNode, NodeManager进程启动

##### 6.2启动Eclipse客户端

```
[zkpk@master ~]$ cd eclipse/[zkpk@master eclipse]$ ./eclipse
```

##### 6.3创建工程并导入依赖jar包

6.3.1新建java工程，名字hadoop

6.3.2导入工程所需要的jar包，

​	将hadoop主目录中的share/hadoop/common中的jar包及其子目录lib中的jar包导入

​	将hadoop主目录中的share/hadoop/hdfs中的jar包及其子目录lib中的jar包导入

​	将hadoop主目录中的share/hadoop/mapreduce中的jar包及其子目录lib中的jar包导入

​	将hadoop主目录中的share/hadoop/yarn中的jar包及其子目录lib中的jar包导入

##### 6.4创建Wordcount的Mapper类

6.4.1在工程src源码包上创建名字是org.zkpk.hadoop.wordcount的package

6.4.2.1名字WordMap，继承Mapper类

​	6.4.2.2Mapper类的输入键值对中键是Object类型，值是Text类型

​	6.4.2.3输出键值对中键是Text类型,值是IntWritable类型

6.4.3覆写Mapper类中的map方法

​	6.4.3.1map方法第一个参数key表示当前所读的这一行数据行首的文本偏移量

​	6.4.3.2第二个参数value表示当前所读的这一行文本

​	6.4.3.3读取数据的每一行，将value转换成string类型，再按照字段之间的分隔符进行切分，得到一个字符串数组

​	6.4.3.4对单词数组进行遍历，将每次遍历得到的单词包装成Text类型，1包装成Intwritable类型，分别作为输出的key和value

##### 6.5创建Wordcount中的Reduce类

6.5.1在名为org.zkpk.hadoop.wordcount的package中创建reducer类

​	6.5.1.1名字是WordReduce，继承Reducer类

​	6.5.1.2Reducer类的输入键值对中键是Text类型，值是IntWritable类型（与Mapper输出的键值对类型分别一致）

​	6.5.1.3输出键值对中键是Text类型,值是IntWritable类型

6.5.2覆写Reducer类中的reduce方法

![img](http://10.105.222.200/upload/image/document/init/06bc6a67-ca77-4c86-aba1-75e16fcae906/f07398e6ceaee5e7de496feda4b4a835.png)

图 8

​	6.5.2.1reduce会将相同key的键值对汇聚到一个reduce task中

​	6.5.2.2第一个参数key表示单词，类型对应Mapper的输出key类型

​	6.5.2.3第二个参数Values表示当前key对应的所有value集合，集合元素类型对应于Mapper的输出value类型

​	6.5.2.4对values进行遍历，将每次遍历的数值进行累加到一个变量，例如：sum

​	6.5.2.5结束遍历，将参数key作为输出的键值对中的键，将sum包装成IntWritable类型作为输出键值对中的值

6.6创建Wordcount中包含main方法的类

​	6.6.1在名为org.zkpk.hadoop.wordcount的package中创建包含main方法的类

​	6.6.1.1名字是WordMain

​	6.6.1.2生成Configuration类型对象

​	6.6.1.3利用Configuration对象生成job对象

​	6.6.1.4调用job的setJarByClass方法设置job运行主类

​	6.6.1.5通过setInputFormatClass方法设置job输入格式，setOutputFormatClass方法设置job输出格式

​	6.6.1.6通过setInputPaths、setOutputPath方法设置job的输入、输出路径

​	6.6.1.7通过setMapperClass、setReducerClass方法设置Map/Reduce阶段的类

​	6.6.1.8通过setOutputKeyClass、setOutputValueClass方法设置最终输出key/value的类型

​	6.6.1.9通过waitForCompletion方法提交job

##### 6.7程序打jar包

##### 6.8运行jar包

6.8.1上传输入数据

​	6.8.1.1从Hadoop的公共目录下拷贝数据文件test.txt到/home/zkpk

​	6.8.1.2将上一步拷贝的数据文件test.txt上传到HDFS的根目录

![img](http://10.105.222.200/upload/image/document/init/06bc6a67-ca77-4c86-aba1-75e16fcae906/c5e078adf94607fa3bbde3a3afbb3494.png)

6.8.2运行jar包，记得要提交输入路径和输出路径

6.8.3查看输出目录

![img](http://10.105.222.200/upload/image/document/init/06bc6a67-ca77-4c86-aba1-75e16fcae906/67bbc461141b739b9fe4b82f9640129c.png)

6.8.4查看输出结果

![img](http://10.105.222.200/upload/image/document/init/06bc6a67-ca77-4c86-aba1-75e16fcae906/d5eb166b7db3073b8ce7d97af7549c0d.png)

