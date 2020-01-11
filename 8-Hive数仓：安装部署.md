# Hive数仓：安装部署
## 1、实验描述

* 学习hive安装部署
* 实验时长：45分钟
* 主要步骤：
    * 启动hadoop集群
    * 解压并安装Hive
    * 创建Hive的元数据库
    * 修改配置文件
    * 添加并生效环境变量
    * 初始化元数据

## 2、实验环境

* 虚拟机数量：3
* 系统版本：Centos 7.5
* Hadoop版本：Apache Hadoop 2.7.3
* MySQL版本：5.7.18

## 3、相关技能

Hive 安装

## 4、知识点

* 查看MySQL状态
* 解压并安装Hive
* 配置Hive
* 启动并验证Hive安装

## 5、实验步骤

### 5.1 启动Hadoop集群

5.1.1 在master启动Hadoop集群

```
[zkpk@master ~]$ start-all.sh
```

5.1.2 在master上运行jps命令，确认NameNode, SecondaryNameNode, ResourceManager进程启动。

5.1.3 分别在slave01、slave02上运行jps命令，确认DataNode, NodeManager进程启动。

### 5.2 解压并安装Hive

5.2.1 从Hive的公共目录/home/zkpk/tgz/hive下拷贝apache-hive-2.1.1-bin.tar.gz文件到/home/zkpk目录下。

5.2.2 使用[tar命令](https://man.linuxde.net/tar)解压Hive安装包。

5.2.3 查看解压后Hive根目录中包含的文件，结果应为下图。

![](http://10.105.222.200/upload/image/document/init/8ec4b7ea-f4cd-4b75-b1af-ad3a043eea74/04c38909c6959b519890529904cb5ccb.png)

### 5.3 在MySQL中添加用户和创建数据库

5.3.1 以root用户登录MySQL，密码为zkpk（注意：这里的root是指数据库的root用户，不是系统的root用户）。

5.3.2 创建hadoop用户（密码：hadoop）并授予权限。

```
mysql>grant all on *.* to hadoop@'%' identified by 'hadoop';
mysql>flush privileges;
```

5.3.3 创建名为hive的数据库，用于存放Hive元数据。

5.3.4 退出MySQL。

mysql> exit

### 5.4 配置hive

5.4.1 进入Hive安装目录下的 conf 配置目录。

5.4.2 新建 Hive 配置文件，名称为 hive-site.xml。

5.4.3 配置存放元数据的 MySQL 的地址、驱动、用户名和密码等信息，将下面的内容添加到 hive-site.xml 文件中。

> 下课前访问 `http://www.kuparty.com/g/RTWZF` ，快速复制配置文本。

```
<?xml version="1.0"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
<configuration>
    <!-- 控制hive是否连接一个远程metastore服务器还是开启一个本地客户端jvm，默认是true -->
    <property>
            <name>hive.metastore.local</name>
            <value>true</value>
    </property>
    <!-- 连接mysql地址及端口号 -->
    <property>
        <name>javax.jdo.option.ConnectionURL</name>
         <value>jdbc:mysql://master:3306/hive?characterEncoding=UTF-8</value>
    </property>
    <!-- mysql连接驱动 -->
    <property>
        <name>javax.jdo.option.ConnectionDriverName</name>
        <value>com.mysql.jdbc.Driver</value>
    </property>
    <!-- mysql用户名 -->
    <property>
        <name>javax.jdo.option.ConnectionUserName</name>
        <value>hadoop</value>
    </property>
    <!-- mysql密码 -->
    <property>
        <name>javax.jdo.option.ConnectionPassword</name>
        <value>hadoop</value>
    </property>
</configuration>
```

### 5.5 拷贝数据库驱动

5.5.1 复制MySQL连接驱动（已存放在/home/zkpk/tgz/下）到 Hive 根目录下的 lib 目录中。

### 5.6 配置系统zkpk用户环境变量

5.6.1 修改环境变量（用户目录下.bash_profile文件)。

5.6.2 将如下配置添加到环境变量中。

```
#HIVE
export HIVE_HOME=/home/zkpk/apache-hive-2.1.1-bin
export PATH=$PATH:$HIVE_HOME/bin
```

5.6.3 使环境变量生效。

### 5.7 启动并验证Hive安装

5.7.1 执行以下命令，尝试启动 Hive 客户端。

```
[zkpk@master ~]$ hive
```

会出现错误如下图，这是由于我们还未手动初始化元数据库。

![](http://10.105.222.200/upload/image/document/init/8ec4b7ea-f4cd-4b75-b1af-ad3a043eea74/ff860933ebc1452849781a1f930860be.png)

> 当使用 1.x 版本的 Hive 时，可以不进行初始化操作，其会在第一次启动的时候自动初始化必要的一部分元数据，并在之后的使用中自动创建其余表；当使用的 hive 是 2.x 版本时，必须手动初始化元数据库。

5.7.2 初始化 Hive 元数据库。该命令会将 Hive 的元数据都同步到 MySQL 中。

```
[zkpk@master ~]$ schematool -dbType mysql -initSchema
```

5.7.3 启动 Hive 客户端。

5.7.4 执行 `show databases` ，命令成功执行。至此，Hive 安装完成。