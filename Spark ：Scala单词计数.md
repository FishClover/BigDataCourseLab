## Spark ：Scala单词计数

## 1、实验描述

- 利用Scala语言开发Spark WordCount程序
- 实验时长：
- 45分钟
- 主要步骤：
- 创建spark项目
- 编写wordcount 示例程序
- 运行Scala 程序
- 查看实验结果

## 2、实验环境

- 虚拟机数量：3(一主两从，主机名分别为：master、slave01、slave02)
- 系统版本：Centos 7.5
- Hadoop版本：Apache Hadoop 2.7.3
- Spark版本：Apache Spark 2.1.1
- JDK 版本：1.8.0_131
- Scala版本： scala2.11.11
- IDEA版本：ideaIC-2017.2.7

## 3、相关技能

- Spark集成开发环境IDEA开发程序
- Scala编写Spark程序

## 4、知识点

- 常见linux命令的使用
- Scala IDEA编程
- Spark RDD
- Scala原理

## 5、实验效果

spark RDD 单词计数结果查看操作最终效果如下图:

![img](http://10.105.222.200/upload/image/document/init/49a83f2b-4923-424c-bc23-9dace311fa2c/d20755c51adc7383e7358ccfdbb4a93b.png)

![img](http://10.105.222.200/upload/image/document/init/49a83f2b-4923-424c-bc23-9dace311fa2c/4f294b2da235af26f2a2c5367b050c7c.png)

## 6、实验步骤

##### 6.1打开虚拟机并启动Hadoop集群，并确认集群是否启动成功（注意要先进入hadoop的sbin目录下）

##### 6.2打开IDEA，配置软件包依赖，创建工程

​	6.2.1创建maven项目。这里我们选择`create from archetype`,能帮助我们节省配置`xml`的时间。

![img](http://10.105.222.200/upload/image/document/init/49a83f2b-4923-424c-bc23-9dace311fa2c/acf87589e0a6230a3172a9ea11ef124c.png)

​	6.2.4进入如下界面，设置本地Maven项目的`setting.xml`文件和`warehouse`仓库，点击next按钮

​		6.2.4.1本地`setting.xml`文件在`/home/zkpk/apache-maven-3.5.0/conf`目录下

​		6.2.4.2本地仓库文件夹`warehouse`在`/home/zkpk/apache-maven-3.5.0/warehouse`

![img](http://10.105.222.200/upload/image/document/init/49a83f2b-4923-424c-bc23-9dace311fa2c/77acefd5fe1336340f8e02e5b286ab42.png)

6.2.5修改`pom.xml`中的部分配置

​	6.2.5.1删除项目不需要的依赖，如下图标红部分的依赖

![img](http://10.105.222.200/upload/image/document/init/49a83f2b-4923-424c-bc23-9dace311fa2c/ae6e16298cbbdb1a70c75cd47381eb92.png)

​	6.2.5.2在xml文件中的properties配置项中，修改scala版本号：2.11.11，并添加spark版本号：2.1.1

​	6.2.5.3找到dependency配置项，添加如下图标红部分的配置，分别是scala依赖和spark依赖,${scala.version}表示上述配置的scala.version变量.

![img](http://10.105.222.200/upload/image/document/init/49a83f2b-4923-424c-bc23-9dace311fa2c/236fb82177e30fc0f2ab50389377935f.png)

​	6.2.5.4一般修改pom.xml文件后，会提示enable auto-import，点击即可，如果没有提示，则可以点击工程名，依次选择Maven—->Reimport，即可根据pom.xml文件导入依赖包

6.2.6设置Java Compiler环境。

​	设置方法为：file->Setting->Build，Execution->Compiler—->Java Compiler，设置Project bytecode version为1.8，设置Target bytecode version为1.8，然后依次点击Apply和OK

6.2.7如下图删除test目录中的`AppTest`和`MySpec`

6.2.8删除main文件夹中，包名下的App文件

##### 6.3编写Scala程序完成Spark单词计数

6.3.1如下图依次打开src—>main—>scala，在org.zkpk.lab上点击右键，创建Scala Class

6.3.2在类中新建伴生对象object ScalaWordCount.

![img](http://10.105.222.200/upload/image/document/init/49a83f2b-4923-424c-bc23-9dace311fa2c/611bab2c04cc235d1332714529d98f13.png)

6.3.3在伴生对象object ScalaWordCount中创建main方法

6.3.4在main方法中编写wordcount程序。

##### 6.4在终端界面查看结果

![img](http://10.105.222.200/upload/image/document/init/e1b24560-72bb-4e13-aa7a-00aea64ffdec/08382d1dc140ed73ae9ad78a2bf96440.png)

图 18

