## HDFS实验

#### 一、实验目的

1. 理解Hadoop分布式文件系统（HDFS）的体系结构。

2. 学会使用操作HDFS常用的FileSystem Shell命令。


#### 二、实验要求

1. Centos 7.5
2. JDK 1.8
3. Hadoop 2.7.3

#### 三、预备知识

HDFS为大数据平台其它所有组件提供了最基本的存储功能，具有高容错、高可靠、可扩展、高吞吐率等特征，为大数据存储和处理提供了强大的底层存储架构。

HDFS采用主/从体系结构模型，一个HDFS集群拥有一个NameNode和多个DataNode。NameNode管理文件系统的元数据，DataNode存储实际的数据。客户端通过NameNode和DataNode的交互访问文件系统，联系NameNode以获取文件的元数据，而真正的文件I/O操作是直接和DataNode进行交互。

Hadoop包含各种类似于Shell的命令，这些命令可直接与HDFS和其他Hadoop所支持的文件系统进行交互。 命令`bin/hdfs dfs -help`列出了Hadoop shell支持的命令 ，这些命令支持大多数常规文件系统操作，例如复制文件，更改文件权限等。 此次实验我们将使用Hadoop的Filesystem Shell来完成实验内容中指定的需求。

参考链接：

1.  Hadoop文件系统shell命令参考
    https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-common/FileSystemShell.html
2.  Hadoop集群搭建指南
    https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-common/ClusterSetup.html

#### 四、注意事项

1.  尽管本实验无需root权限，但如果你要用到，root密码是zkpk
2.  官方文档对应最新的版本，与实验环境中的版本不一致，因此可能会有一些问题

#### 五、实验内容

1.  启动HDFS服务。本实验已经配置好HDFS集群环境和jdk，首先启动HDFS服务，然后查看HDFS是否启动成功。
2. 在HDFS根目录下创建user1和user2目录。
3.  在user1目录中创建remote.txt文件，查看user1目录下文件。
4.  在本地创建local.txt文件，文件中写入`“Hello,Hadoop! Hello,HDFS!”`然后上传至HDFS user2目录
5.  将user1目录下的remote.txt文件移动至user2目录下，查看源目录下的文件是否存在。
6.  将user2目录下的local.txt文件复制到user1目录下。
7.  查看user1目录下local.txt文件的内容。
8.  删除user2目录下的remote.txt文件。
9.  关闭HDFS服务。
