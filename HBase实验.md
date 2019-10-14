## HBase实验

#### 一、实验目的

​	会使用HBase Shell 连接分布式数据库HBase进行建表及增删改查操作

#### 二、实验要求

1. Linux、MacOSX或其他类似Unix的操作系统

2. HBase

#### 三、预备知识

​	HBase (Hadoop Database)是一个高可靠性、高性能、面向列、可伸缩、 实时读写的分布式数据库。HBase不同于一般的关系数据库，它是一个适合于非结构化数据存储的数据库。另一个不同的是HBase基于列的而不是基于行的模式。HBase利用Hadoop HDFS作为其文件存储系统,利用Hadoop MapReduce来处理 HBase中的海量数据,利用Zookeeper作为其分布式协同服务。

HBase有三种安装模式：本地模式、伪分布模式和全分布模式。本地模式是HBase的默认模式，该模式是直接在本地文件系统中存放数据而不是基于HDFS，也不需要使用Zookeeper。注意，在本地文件系统上运行HBase一般不能确保数据的持久性，要想确保数据都被保存下来，需要在HDFS上运行HBase。但是在本地文件系统上运行HBase可以快速开始学习和熟悉HBase系统的运作，因而该模式通常用于HBase程序的测试。HBase的伪分布模式是在一台机器上模拟HBase的全分布环境，需要使用HDFS来保存数据，同时还需要运行Zookeeper。HBase的全分布模式通常应用于生产环境中，需要使用多台机器。本实验在本地模式下进行。

> 

#### 四、实验内容

	1. 启动HBase服务。
 	2. 使用Hbase Shell连接HBase。
 	3. 创建表student,且有5个列族，分别为`Sname,Sex,Sage,Sdept,course`。
 	4. 查看student表的基本信息。
 	5. 向表student中添加一条数据，`rowkey`为`001`，`Sname`为`Liming`  
 	6. 向表student中添加`rowkey `为`001`的学生的其他信息，`Sex`为`male`，`Sage`为`22`，`Sdept`为`CS`，向`couse`列族中增加一列`math`，并设置值为`80`
 	7. 查看student表中`rowkey` 为`001`学生的信息。
 	8. 用`scan`命令查询student表中的所有信息。  
 	9. 删除student表中rowkey为`001`的所有学生信息。
 	10. 删除表student。