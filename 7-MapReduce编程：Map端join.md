## MapReduce编程：Map端join

## 1、实验描述

- 使用MapReduce编程，如果涉及到join操作，一般使用的是reduce端的join；但如果其中一个文件较小，可以将其添加到分布式缓存当中去，在map阶段时，每一个map
  task开始运行前，先从分布式缓存中取出此小文件，在map方法中对数据进行join操作，即map
  join操作。

> 本实验有两个输入文件，其中一个是uid-list，保存着搜索过“电影”的用户的UID（比较小），另外一个文件是sogou日志文件；对于处于uid-list中的用户，把他们在sogou日志文件中的uid及搜索关键词输出到HDFS

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
- map join
- 程序打jar包
- 将jar包运行在hadoop集群上

## 5、实现效果

![img](http://10.105.222.200/upload/image/document/init/d7ccd684-e41f-432a-9f0c-98bca2f3850f/2a3202d1d981644b78fec76f5908da9b.png)

## 6、实验步骤

##### 6.1打开虚拟机并启动Hadoop集群，并确认集群是否启动成功。

##### 6.2启动Eclipse客户端

##### 6.3创建工程并导入依赖jar包

​	6.3.1新建java工程，名字hadoop

​	6.3.2添加项目所需的jar包

​	将hadoop主目录中的share/hadoop/common中的jar包及其子目录lib中的jar包导入

​	将hadoop主目录中的share/hadoop/hdfs中的jar包及其子目录lib中的jar包导入

​	将hadoop主目录中的share/hadoop/mapreduce中的jar包及其子目录lib中的jar包导入

​	将hadoop主目录中的share/hadoop/yarn中的jar包及其子目录lib中的jar包导入

##### 6.4创建工程的Mapper类

​	6.4.1 在工程src源码包上创建名字是org.zkpk.hadoop.mapjoin的package

​	6.4.2 在此package中创建工程中的Mapper类

​	6.4.2.1 名字MapjoinMapper，继承Mapper类

​	6.4.2.2 Mapper类的输入键值对中键是LongWritable类型，值是Text类型

​	6.4.2.3 输出键值对中键是Text类型,值是Text类型

​	6.4.3 为该Mapper类创建几个变量

​		6.4.3.1 创建HashMap\类型对象map，用于存储分布式缓存文件uid-list中的uid

​		6.4.3.2 创建Text类型对象uid

​	6.4.4编写Mapper中的setup方法

​		6.4.4.1setup方法传入的参数context代表上下文环境对象

​		6.4.4.2setup方法在每个map task的开始，只执行一次，在执行Map任务前，进行相关变量或者资源的集中初始化工作，此处用于将小文件读取到缓存中

​		6.4.4.3构造一个FileReader类型对象，构造方法的参数是以”uuid”（它是在main方法中，将本实验小文件uid-list添加到分布式缓存时，设置的“文件的符号链接”）；再将此FileReader对象包装成BufferedReader类型对象br

​		6.4.4.4使用BufferedReader对象br循环读取其中的数据；每读一行数据将其作为key，1作为value添加到MapjoinMapper类的HashMap类型的成员map中

6.4.5覆写Mapper类中的map方法

![img](http://10.105.222.200/upload/image/document/init/d7ccd684-e41f-432a-9f0c-98bca2f3850f/a8010a5d0c6ca0333178f42cc890f15f.png)

​	6.4.5.1map方法第一个参数key表示当前所读的这一行数据行首的文本偏移量

​	6.4.5.2第二个参数value表示当前所读的这一行文本

​	6.4.5.3读取数据的每一行，将value转换成string类型，再按照字段之间的分隔符进行切分，得到一个字符串数组arr

​	6.4.5.4如果字符串数组arr下标为1的元素即uid不为null，并且map对象中存在以uid作为键的键值对，则表示sogou日志中当前uid的记录符合要求，把该uid赋值给Text类型对象uid，字符串数组arr下标为2的元素即搜索关键词包装成Text类型对象，分别作为key和value输出出去

##### 6.5创建工程包含main方法的类

​	6.5.1在名为org.zkpk.hadoop.mapjoin.newapi的package中创建包含main方法的类

​	6.5.1.1名字是Main

​	6.5.1.2生成Configuration类型对象

​	6.5.1.3利用Configuration对象生成job对象

​	6.5.1.4调用job的setJarByClass方法设置job运行主类

​	6.5.1.5调用job的setMapperClass方法设置Map阶段的类

​	6.5.1.6调用job的setNumReduceTasks设置reduce的个数，此处为0，因为不需要reduce阶段

​	6.5.1.7调用job的setOutputKeyClass、setOutputValueClass方法设置最终输出key/value的类型

​	6.5.1.8创建一个Path类型对象cache，参数为命令行的第一个参数，即文件uid-list

​	6.5.1.9将cache转换成URI类型，再转换成字符串形式，再与”#uuid”拼接，最终值赋给cacheLink（说明”#uuid”是给args[0]文件即uid-list增加一个“文件的符号链接”，根据此符号链接就能读取此文件）

​	6.5.1.10调用job的addCacheFile方法将小文件添加到分布式缓存中，传入的唯一参数是以cacheLink的URI形式

​	6.5.1.11通过addInputPath、setOutputPath方法设置job的输入、输出路径

​	6.5.1.12通过waitForCompletion方法提交job，并等待执行完成

##### 6.6将程序打成jar包

##### 6.7运行jar包

​	6.7.1上传输入数据

​		6.7.1.1从experiment目录下的uid-list 和sogou.500w.utf8拷贝到/home/zkpk

​		6.7.1.2将上一步拷贝的两个数据文件上传到HDFS的根目录

​	6.7.2运行jar包，传入程序需要的参数。

​	6.7.4查看输出目录

​	6.7.5查看输出结果

![img](http://10.105.222.200/upload/image/document/init/d7ccd684-e41f-432a-9f0c-98bca2f3850f/2a3202d1d981644b78fec76f5908da9b.png)