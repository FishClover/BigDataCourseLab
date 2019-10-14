## HDFS实验

#### 一、实验目的

1. 理解Hadoop分布式文件系统（HDFS）的体系结构。

2. 学会使用HDFS操作常用的Shell命令。

3. 学会编写读写HDFS文件的Java程序。

#### 二、实验要求

1. Linux、MacOSX或其他类似Unix的操作系统;

2. JDK 1.8;

3. Hadoop 3.2.0。

#### 三、预备知识

​	HDFS为大数据平台其它所有组件提供了最基本的存储功能，具有高容错、高可靠、可扩展、高吞吐率等特征，为大数据存储和处理提供了强大的底层存储架构。

​	HDFS采用主/从体系结构模型，一个HDFS集群拥有一个NameNode和多个DataNode。NameNode管理文件系统的元数据，DataNode存储实际的数据。客户端通过NameNode和DataNode的交互访问文件系统，联系NameNode以获取文件的元数据，而真正的文件I/O操作是直接和DataNode进行交互[1]。

> 启动HDFS服务：先进入Hadoop的sbin目录，然后运行 `./start-dfs.sh`, 关闭将start换成stop即可。
>
> 创建目录`hadoop fs -mkdir [-p] <paths> 	example： hadoop fs -mkdir /user/hadoop/dir1` 
>
> 创建空文件： `hadoop fs -touchz URI  	example： hadoop fs -touchz pathname`
>
> 显示目录中的文件：   `hadoop fs -ls [-C] [-d] [-h] [-q] [-R] [-t] [-S] [-r] [-u] [-e] ` 
>
> 上传文件到HDFS：` hadoop fs -put localfile /user/hadoop/hadoopfile `
>
> 移动目录：`hadoop fs -mv URI [URI ...]  <dest> `
>
> 删除HDFS中的目录或文件：  `hadoop fs -rm  URI `

#### 四、实验内容

​	1.启动HDFS服务。本实验已经配置好HDFS集群环境和jdk，首先启动HDFS服务，然后查看HDFS是否启动成功。

2. 创建user1和user2目录，在user1目录中创建remote.txt文件，查看user1目录下文件。
3. 在本地创建local.txt文件，文件中写入`“Hello,Hadoop! Hello,HDFS!”`然后上传至HDFS user2目录
4. 将user1目录下的remote.txt文件移动至user2目录下，查看源目录下的文件是否存在。
5. 将user2目录下的local.txt文件复制到user1目录下。
6. 查看user1目录下local.txt文件的内容。
7. 删除user2目录下的remote.txt文件。
8. 关闭HDFS服务。
