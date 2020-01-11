Hadoop安装部署
--------

1、实验描述
--------

-   在若干节点中，安装部署hadoop分布式集群

2、实验环境
--------

-   虚拟机数量：3

-   系统版本：Centos 7.5

-   Hadoop版本： Apache Hadoop 2.7.3

3、相关技能
--------

-   熟悉Linux操作系统

-   Hadoop原理

4、知识点
------

-   linux系统基础配置

-   配置JDK

-   配置hadoop的相关配置文件

-   掌握hadoop操作指令

5、效果图
------

hadoop集群安装部署后，启动集群，效果如下：

效果图1：master节点上，这4个进程表示主节点进程启动成功。

![](http://10.105.222.200/upload/image/document/init/9d58307a-32cc-429d-aa68-30e52963ec68/7c368ac4eb5fe05bcd17d89a115b0d9a.png)

<center>图 1</center>
效果图2：slave01节点上出现了这3个进程表示从节点进程启动成功。

![](http://10.105.222.200/upload/image/document/init/9d58307a-32cc-429d-aa68-30e52963ec68/5d7b51da005e1d16fcca209b90632b04.png)

<center>图 2</center>
效果图3：slave02节点上出现了这3个进程表示从节点进程启动成功。

![](http://10.105.222.200/upload/image/document/init/9d58307a-32cc-429d-aa68-30e52963ec68/f63fee1028bd6d9502e9e13f370809e6.png)

<center>图 3</center>
6、实验步骤
--------

6.1配置主机名（“界面各虚拟机”分别操作此步骤，主机名分别设置为master, slave01, slave02）
>   说明：此处以界面虚拟机master的操作为例（默认已经配置好主机名称）

6.1.1以下操作需要root权限，所以先切换成root用户，密码：zkpk

```shell
[zkpk@localhost ~]$ su root
```
![](http://10.105.222.200/upload/image/document/init/9d58307a-32cc-429d-aa68-30e52963ec68/c7ed132ff868ca2fefe1a0a29123619b.png)
6.1.2使用vim编辑主机名（或使用gedit）

6.1.3编辑主机名文件

```shell
[root@localhost ~]# vim /etc/hostname
```

将原来内容替换为master

```shell
master
```

保存并退出

6.1.4 临时设置主机名为master：

```shell
[root@localhost ~]# hostname master
```

检测主机名是否修改成功：

说明：bash命令让上一步操作生效

```shell
[root@localhost zkpk]# bash
[root@master zkpk]# hostname
master
```


![](http://10.105.222.200/upload/image/document/init/9d58307a-32cc-429d-aa68-30e52963ec68/e2321113c8c1b7945ccb6516a433789b.png)


6.2配置时钟同步（使用root权限；“界面各虚拟机”分别操作此步骤；在本实验中需要配置）
####需要登陆校园网

>   说明：此处以master节点的操作为例

6.2.1配置自动时钟同步

```shell
[root@master zkpk]# crontab -e
```

输入下面的内容（星号之间和前后都有空格）

```shell
0 1 * * * /usr/sbin/ntpdate cn.pool.ntp.org
```
保存并退出

6.2.2手动同步时间，直接在Terminal运行下面的命令：

```shell
[root@master zkpk]# /usr/sbin/ntpdate cn.pool.ntp.org
```

6.3关闭防火墙(使用root权限；“界面各虚拟机”分别操作此步骤；本实验默认关闭)

6.3.1查看防火墙状态（默认已经关闭防火墙）

```shell
[root@master ~]# systemctl status firewalld.service
```


![](http://10.105.222.200/upload/image/document/init/9d58307a-32cc-429d-aa68-30e52963ec68/25831a21500cc5fad4014906f9c9fc3b.png)


6.3.2在终端中执行下面命令：

说明：两条命令分别是临时关闭防火墙和禁止开机启动防火墙

```shell
[root@master ~]# systemctl stop firewalld.service
[root@master ~]# systemctl disable firewalld.service
```

6.4配置hosts列表(使用root权限；“界面各虚拟机”分别操作此步骤；本实验已经配置)

6.4.1先分别在各虚拟机中运行ifconfig命令，获得当前节点的ip地址，如下图是master的ip地址

![](http://10.105.222.200/upload/image/document/init/9d58307a-32cc-429d-aa68-30e52963ec68/074d3d953f9dbc02ea0c38ba91e56b78.png)

<center>图 10</center>
6.4.2编辑主机名列表文件：

```shell
[root@master zkpk]# vi /etc/hosts
```

6.4.3将下面三行添加到/etc/hosts文件中，保存退出：

注意：这里master节点对应IP地址是10.42.0.93，slave01对应的IP是10.42.0.94，slave02对应的IP是10.42.0.95，而自己在做配置时，需要将IP地址改成自己的master、slave01和slave02对应的IP地址。

```shell
10.42.0.93 master
10.42.0.94 slave01
10.42.0.95 slave02
```

6.5.4Ping主机名

```shell
[root@master ~]# ping master -c 3
[root@master ~]# ping slave01 -c 3 
[root@master ~]# ping slave02 -c 3
```

6.6免密钥登录配置（注意：使用zkpk用户）

6.6.1master节点上

6.6.1.1先从root用户，退回到普通用户zkpk

```shell
[root@master ~]$ su zkpk
[zkpk@master ~]$ 
```

6.6.1.2在终端生成密钥，命令如下（一路按回车完成密钥生成）

```shell
[zkpk@master ~]$ ssh-keygen -t rsa
```


![](http://10.105.222.200/upload/image/document/init/9d58307a-32cc-429d-aa68-30e52963ec68/8e7cafaea9f2d7aff4ae28e409a7c456.png)


6.6.1.3生成的密钥在用户根目录中的.ssh子目录中，进入.ssh目录，如下图操作：

![](http://10.105.222.200/upload/image/document/init/9d58307a-32cc-429d-aa68-30e52963ec68/08c1600fd6fe74b4d6f07dbee019353d.png)


6.6.1.4进行复制公钥文件

```shell
[zkpk@master .ssh]$ cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
```

6.6.1.5执行ls -l命令后会看到下图的文件列表：

![](http://10.105.222.200/upload/image/document/init/9d58307a-32cc-429d-aa68-30e52963ec68/89390baaac2a905008e189a187c9f08e.png)


6.6.1.6修改authorized_keys文件的权限，命令如下：

```shell
[zkpk@master .ssh]$ chmod 600 ~/.ssh/authorized_keys
```

6.6.1.7将专用密钥添加到 ssh-agent 的高速缓存中

```shell
[zkpk@master .ssh]$ ssh-add ~/.ssh/id_rsa
```

6.6.1.8将authorized_keys文件复制到slave01、slave02节点zkpk用户的根目录，命令如下：

说明：如果提示输入yes/no的时候，输入yes，回车。密码是：zkpk

```shell
[zkpk@master .ssh]$ scp ~/.ssh/authorized_keys zkpk@slave01:~/
[zkpk@master .ssh]$ scp ~/.ssh/authorized_keys zkpk@slave02:~/
```

6.6.2slave01节点

6.6.2.1在终端生成密钥，命令如下（一路点击回车生成密钥）

```shell
[zkpk@slave01 ~]$ ssh-keygen -t rsa
```

6.6.2.2将authorized_keys文件移动到.ssh目录

```shell
[zkpk@slave01 ~]$ mv authorized_keys ~/.ssh/
```

6.6.3slave02节点的设置同slave02一样


6.6.4验证免密钥登陆

6.6.4.1在master机器上远程登录slave01：

```shell
[zkpk@master ~]$ ssh slave01
```

6.6.4.2如果出现下图的内容表示免密钥配置成功：

![](http://10.105.222.200/upload/image/document/init/9d58307a-32cc-429d-aa68-30e52963ec68/cbbeae69deb430ed0833bf862f62a6c2.png)


6.6.4.3退出slave01远程登录

```shell
[zkpk@slave01 ~]$ exit
[zkpk@master ~]$
```

6.6.4.4在master机器上远程登录slave02，操作同slave02.


6.7安装JDK(在三台节点分别操作此步骤)

6.7.1删除系统自带的jdk(如若出现下图效果，说明系统自带java，需要先卸载)

6.7.1.1查看系统自带jdk

```shell
[zkpk@master ~]$ rpm -qa | grep java
```



6.7.1.2切换root用户，密码：zkpk

```shell
[zkpk@master ~]$ su root
```

6.7.1.3移除系统自带的jdk

```shell
[root@master zkpk]# yum remove java-1.*
```

6.7.1.4创建存放jdk文件目录

```shell
[root@master zkpk]# mkdir /usr/java
```

6.7.2将/home/zkpk/tgz下的JDK压缩包解压到/usr/java目录下

```shell
[root@master zkpk]# tar -xzvf /home/zkpk/tgz/jdk-8u131-linux-x64.tar.gz -C /usr/java
```

6.7.2.1退出root用户

```shell
[root@master zkpk]# exit
```

6.7.3配置zkpk用户环境变量

6.7.3.1使用vim修改“.bash_profile”

```shell
[zkpk@master ~]$ vim /home/zkpk/.bash_profile
```

6.7.3.2复制粘贴以下内容添加到到上面vim打开的文件中：

```shell
export JAVA_HOME=/usr/java/jdk1.8.0_131/
export PATH=$JAVA_HOME/bin:$PATH
```

6.7.4使环境变量生效：

```shell
[zkpk@master ~]$ source /home/zkpk/.bash_profile
```

6.7.5查看java是否配置成功：

```shell
[zkpk@master ~]$ java -version
```


6.8安装部署Hadoop集群（zkpk用户）

说明：每个节点上的Hadoop配置基本相同，在master节点操作，然后复制到slave01、slave02两个节点。

6.8.1将/home/zkpk/tgz/hadoop目录下的Hadoop压缩包解压到/home/zkpk目录下

```shell
[zkpk@master ~]$ tar -xzvf /home/zkpk/tgz/hadoop-2.7.3.tar.gz –C /home/zkpk
```

6.8.2配置hadoop-env.sh文件

6.8.2.1使用vim命令修改hadoop-env.sh文件

```shell
[zkpk@master ~]$ vim /home/zkpk/hadoop-2.7.3/etc/hadoop/hadoop-env.sh
```

6.8.2.2修改JAVA_HOME环境变量

```shell
export JAVA_HOME=/usr/java/jdk1.8.0_131/
```

6.8.3配置yarn-env.sh文件

6.8.3.1使用gedit命令修改yarn-env.sh文件

```shell
[zkpk@master ~]$ gedit ~/hadoop-2.7.3/etc/hadoop/yarn-env.sh
```

6.8.3.2修改JAVA_HOME环境变量

```shell
export JAVA_HOME=/usr/java/jdk1.8.0_131/
```

6.8.4配置core-site.xml 文件

6.9.4.1修改core-site.xml文件

```shell
[zkpk@master ~]$ vim  ~/hadoop-2.7.3/etc/hadoop/core-site.xml
```

6.9.4.2用下面的代码替换core-site.xml中的内容：

```shell
<?xml version="1.0" encoding="UTF-8"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
<!-- Put site-specific property overrides in this file. -->
<configuration>
        <property>
                <name>fs.defaultFS</name>
                <value>hdfs://master:9000</value>
        </property>
        <property>
                <name>hadoop.tmp.dir</name>
                <value>/home/zkpk/hadoopdata</value>
        </property>
</configuration>
```

6.8.5配置hdfs-site.xml文件

6.8.5.1修改hdfs-site.xml文件

```shell
[zkpk@master ~]$ vim ~/hadoop-2.7.3/etc/hadoop/hdfs-site.xml
```

6.8.5.2用下面的代码替换hdfs-site.xml中的内容：

```shell
<?xml version="1.0" encoding="UTF-8"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
<!-- Put site-specific property overrides in this file. -->
<configuration>
    <property>
        <name>dfs.replication</name>
        <value>2</value>
    </property>
</configuration>
```

6.8.6配置yarn-site.xml文件

6.8.6.1修改yarn-site.xml文件

```shell
[zkpk@master ~]$ vim  ~/hadoop-2.7.3/etc/hadoop/yarn-site.xml
```

6.8.6.2用下面的代码替换yarn-site.xml中的内容：

```shell
<?xml version="1.0"?>
<configuration>
	<property>
		<name>yarn.nodemanager.aux-services</name>
		<value>mapreduce_shuffle</value>
	</property>
	<property>
        <name>yarn.resourcemanager.address</name>
        <value>master:18040</value>
    </property>
	<property>
        <name>yarn.resourcemanager.scheduler.address</name>
        <value>master:18030</value>
    </property>
	<property>
        <name>yarn.resourcemanager.resource-tracker.address</name>
        <value>master:18025</value>
    </property>
	<property>
        <name>yarn.resourcemanager.admin.address</name>
        <value>master:18141</value>
    </property>
	<property>
        <name>yarn.resourcemanager.webapp.address</name>
        <value>master:18088</value>
    </property>
</configuration>
```

6.8.7配置mapred-site.xml文件

6.8.7.1复制mapred-site-template.xml文件：

```shell
[zkpk@master ~]$ cp ~/hadoop-2.7.3/etc/hadoop/mapred-site.xml.template  ~/hadoop-2.7.3/etc/hadoop/mapred-site.xml
```

6.8.7.2编辑mapred-site.xml文件：

```shell
[zkpk@master ~]$ vim ~/hadoop-2.7.3/etc/hadoop/mapred-site.xml
```

6.8.7.3用下面的代码替换mapred-site.xml中的内容

```shell
<?xml version="1.0"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
<configuration>
	<property>
		<name>mapreduce.framework.name</name>
		<value>yarn</value>
	</property>
</configuration>
```

6.8.8配置slaves文件

6.8.8.1编辑slaves文件：

```shell
[zkpk@master ~]$ vim ~/hadoop-2.7.3/etc/hadoop/slaves
```

6.8.8.2添加如下内容

```shell
slave01
slave02
```

6.8.9创建Hadoop数据目录

```shell
[zkpk@master ~]$ cd 
[zkpk@master ~]$ mkdir hadoopdata
```

6.8.10将配置好的hadoop文件夹复制到从节点

6.8.10.1使用scp命令将文件夹复制到slave01、slave02上：

说明：因为之前已经配置了免密钥登录，这里可以直接免密钥远程复制。

```shell
[zkpk@master ~]$ scp -r hadoop-2.7.3 zkpk@slave01:~/
[zkpk@master ~]$ scp -r hadoop-2.7.3 zkpk@slave02:~/
```

6.9配置Hadoop环境变量（在三台节点分别操作此步骤，zkpk用户）

6.9.1以master节点为例

```shell
[zkpk@master ~]$ vim ~/.bash_profile
```

6.9.2在.bash_profile末尾添加如下内容：

```shell
#HADOOP
export HADOOP_HOME=/home/zkpk/hadoop-2.7.3
export PATH=$HADOOP_HOME/bin:$HADOOP_HOME/sbin:$PATH
```

6.9.3使环境变量生效：

```shell
[zkpk@master ~]$ source ~/.bash_profile
```

6.10格式化Hadoop文件目录（在master上执行）

6.10.1格式化命令如下

```shell
[zkpk@master ~]$ hdfs namenode -format
```


6.11启动Hadoop集群（在master上执行）

6.11.1分别在master、slave01、slave02的终端执行jps命令，判断Hadoop集群是否启动成功。

6.11.2Web UI查看集群是否成功启动

在master上打开Firefox浏览器，在浏览器地址栏中输入<http://master:50070></http:>，检查
namenode 和 datanode 是否正常，如下图所示。

![](http://10.105.222.200/upload/image/document/init/9d58307a-32cc-429d-aa68-30e52963ec68/f5c716a84984d8d093020c5cbb8fc4f5.png)

打开浏览器新标签页，地址栏中输入<http://master:18088></http:>，检查Yarn是否正常，如下图所示。

![](http://10.105.222.200/upload/image/document/init/9d58307a-32cc-429d-aa68-30e52963ec68/718f928da4500be86905bb2e7113b8b2.png)


6.11.3运行PI实例检查集群是否成功
执行下面的命令：

```shell
[zkpk@master~]$ hadoop jar ~/hadoop-2.7.3/share/hadoop/mapreduce/hadoop-mapreduce-examples-2.7.3.jar pi 10 10
```


观察最后输出结果。