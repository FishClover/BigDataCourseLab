## MapReduce编程：数据过滤及保存

## 1、实验描述

- 使用mapreduce编程，输入文件为搜狗日志文件，对输入的每行数据做判断，只把搜索的关键词中包含数字的用户uid和关键词输出到HDFS上。

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

![img](http://10.105.222.200/upload/image/document/init/e320ac9f-1959-48f5-a3a9-8523fa97e05b/0d5df47d08f569a6d7bed68bdc69a04a.png)

图 1

## 6、实验步骤

##### 6.1打开虚拟机并启动Hadoop集群

6.1.1在master启动Hadoop集群

6.1.2确认集群是否启动成功

##### 6.2启动Eclipse客户端

##### 6.3创建工程并导入依赖jar包

6.3.1新建java工程，名字hadoop

6.3.2导入工程需要的jar包

​	将hadoop主目录中的share/hadoop/common中的jar包及其子目录lib中的jar包导入

​	将hadoop主目录中的share/hadoop/hdfs中的jar包及其子目录lib中的jar包导入

​	将hadoop主目录中的share/hadoop/mapreduce中的jar包及其子目录lib中的jar包导入

​	将hadoop主目录中的share/hadoop/yarn中的jar包及其子目录lib中的jar包导入

##### 6.4创建UIDFilter的Mapper类

6.4.1在工程src源码包上创建名字是org.zkpk.hadoop.uidfilter的package

6.4.2在此package中创建mr编程中的mapper类

​	6.4.2.1名字UIDFilterMapper，继承Mapper类

​	6.4.2.2Mapper类的输入键值对中键是Object类型，值是Text类型

​	6.4.2.3输出键值对中键是Text类型,值是Text类型

6.4.3覆写Mapper类中的map方法

![img](http://10.105.222.200/upload/image/document/init/e320ac9f-1959-48f5-a3a9-8523fa97e05b/49043be8f59dcd0bd108014f71fe3266.png)

​	6.4.3.1map方法第一个参数key表示当前所读的这一行数据行首的文本偏移量

​	6.4.3.2第二个参数value表示当前所读的这一行文本

​	6.4.3.3读取数据的每一行，将value转换成string类型，再按照字段之间的分隔符进行切分，得到一个字符串数组

​	6.4.3.4如果数组的长度等于字段数，则用对关键词用正则匹配；将包含数字的关键词对应的用户uid包装成Text类型，关键词包装成Text类型，分别作为输出的key和value

##### 6.5创建UIDFilter中的Reduce类

6.5.1在名为org.zkpk.hadoop.uidfilter的package中创建reducer类

6.5.1.1名字是UIDFilterReducer，继承Reducer类

6.5.1.2Reducer类的输入键值对中键是Text类型，值是Text类型（与Mapper输出的键值对类型分别一致）

6.5.1.3输出键值对中键是Text类型,值是Text类型

6.5.2覆写Reducer类中的reduce方法

![img](http://10.105.222.200/upload/image/document/init/e320ac9f-1959-48f5-a3a9-8523fa97e05b/db0a6ee54554cd4ebf5bf55650fcc634.png)

​	6.5.2.1reduce会将相同key的键值对汇聚到一个reduce task中

​	6.5.2.2第一个参数key表示用户uid，类型对应Mapper的输出key类型

​	6.5.2.3第二个参数Values表示当前key对应的所有value集合，集合元素类型对应于Mapper的输出value类型

​	6.5.2.4将参数key作为输出的键值对中的键，遍历value集合将每一次得到的值包装成Text类型作为输出键值对中的值

##### 6.6创建UIDFilter中包含main方法的类

6.6.1在名为org.zkpk.hadoop.uidfilter的package中创建包含main方法的类

​	6.6.1.1名字是UIDFilterMain

​	6.6.1.2生成Configuration类型对象

​	6.6.1.3利用Configuration对象生成job对象

​	6.6.1.4调用job的setJarByClass方法设置job运行主类

​	6.6.1.5通过addInputPath、setOutputPath方法设置job的输入、输出路径

​	6.6.1.6通过setMapperClass、setReducerClass方法设置Map/Reduce阶段的类

​	6.6.1.7通过setOutputKeyClass、setOutputValueClass方法设置最终输出key/value的类型

​	6.6.1.8通过waitForCompletion方法提交job

##### 6.7程序打jar包

##### 6.8运行jar包

6.8.1上传输入数据

​	6.8.1.1从Hadoop的公共目录下拷贝数据文件sogou.txt到/home/zkpk

​	6.8.1.2将上一步拷贝的数据文件sogou.txt上传到HDFS的根目录

6.8.2运行jar包，传入输入路径和输出路径

6.8.3查看输出目录

6.8.4查看输出结果



