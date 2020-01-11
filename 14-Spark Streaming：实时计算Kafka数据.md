# 实验名称：Spark Streaming：实时计算Kafka数据

## 1、实验描述

- 通过创建Kafka topic，使用Kafka Producer产生消息，然后通过编写spark
  Streaming程序处理这些消息。
- 实验时长：
  - 45分钟
- 主要步骤：
  - 创建Spark Streaming项目工程
  - 编写streaming程序
  - 启动Zookeeper，Kafka集群
  - 创建topic
  - 启动Kafka生产者
  - 准备作业环境
  - 提交作业

## 2、实验环境

- 虚拟机数量：3(一主两从，主机名分别为：master、slave01、slave02)
- 系统版本：Centos 7.5
- Zookeeper版本： Apache Zookeeper 3.4.10
- Kafka版本：kafka_2.11-0.10.2.1
- Spark版本：Apache Spark 2.1.1

## 3、相关技能

- 使用IDEA开发spark Streaming程序
- Spark Streaming 编程
- Kafka topic的创建
- 使用Kafka生产者产生消息
- Linux 命令使用

## 4、知识点

- 常见Linux命令的使用
- IDEA的使用
- Spark Streaming编程方法
- spark jar 包的提交

## 5、实验效果

spark Streaming 实时计算Kafka生产者消息效果如下图所示

![img](http://10.105.222.200/upload/image/document/init/d0ebfe69-aa4d-40dc-a474-8b629ef8c2bb/8a68f7b753a2ec62aff4c6c02b37f3aa.png)

图 1

![img](http://10.105.222.200/upload/image/document/init/d0ebfe69-aa4d-40dc-a474-8b629ef8c2bb/1b8be253890d20367189b8bdb643c639.png)

图 2

## 6、实验步骤

#### 6.1创建maven项目

6.1.1打开IDEA

6.1.2进入如下界面，点击 Create New Project

![img](http://10.105.222.200/upload/image/document/init/49a83f2b-4923-424c-bc23-9dace311fa2c/704a196c37316bea7517c18864e05a1b.png)

6.1.3进入如下图界面，按照图标依次点击，最后点击next

![img](http://10.105.222.200/upload/image/document/init/49a83f2b-4923-424c-bc23-9dace311fa2c/acf87589e0a6230a3172a9ea11ef124c.png)

6.1.4依次输入GroupId和ArtifactId和Version的值。在maven中，这几个字段能唯一标识一个项目。

6.1.5进入如下界面，设置本地Maven项目的setting.xml文件和warehouse仓库，点击next按钮。这两个文件用来配置maven从何处获取所需要的dependency。

- 本地setting.xml文件在/home/zkpk/apache-maven-3.5.0/conf目录下

- 本地仓库文件夹warehouse在/home/zkpk/apache-maven-3.5.0/warehouse

![img](http://10.105.222.200/upload/image/document/init/49a83f2b-4923-424c-bc23-9dace311fa2c/77acefd5fe1336340f8e02e5b286ab42.png)

6.1.6进入如下界面，输入工程名称spark_test，然后点击next

![img](http://10.105.222.200/upload/image/document/init/49a83f2b-4923-424c-bc23-9dace311fa2c/e98029bafe780cbbb8ba6e10614cd52b.png)

6.1.7进入如下界面，即表示工程spark_test创建成功

![img](http://10.105.222.200/upload/image/document/init/49a83f2b-4923-424c-bc23-9dace311fa2c/12bfcaad7c8086cea49d841a274a9a07.png)

6.1.8工程创建完成后会自动打开一个名为zkpk的xml文件，删除如下图标红部分的依赖。

![img](http://10.105.222.200/upload/image/document/init/49a83f2b-4923-424c-bc23-9dace311fa2c/ae6e16298cbbdb1a70c75cd47381eb92.png)

6.1.9在xml文件中找到properties配置项，修改scala版本号（此处对应scala安装版本），并添加spark版本号（此处对应spark安装版本）

![img](http://10.105.222.200/upload/image/document/init/49a83f2b-4923-424c-bc23-9dace311fa2c/439fe693386862a0ab62b04e318f83da.png)6.1.10找到dependency配置项，添加如下图标红部分的配置，分别是scala依赖和spark依赖

![img](http://10.105.222.200/upload/image/document/init/49a83f2b-4923-424c-bc23-9dace311fa2c/236fb82177e30fc0f2ab50389377935f.png)

6.1.11在Project Structure，选择Modules，选择Language level为8。目的是为了支持程序中所写的Lambda函数。

![img](http://10.105.222.200/upload/image/document/init/49a83f2b-4923-424c-bc23-9dace311fa2c/6b5191e5984c46e7f389ee23616d7f48.png)

6.1.12设置Java Compiler环境，进入Setting，依次选择Build，Execution—->Compiler—->Java Compiler，设置图中的Project bytecode version为1.8。

![img](http://10.105.222.200/upload/image/document/init/49a83f2b-4923-424c-bc23-9dace311fa2c/ad70e42933a01a9c0c2cd1a944ab4f94.png)

6.1.13如下图删除测试环境test中的测试类，（测试时使用，用于编译和运行测试代码）

![img](http://10.105.222.200/upload/image/document/init/49a83f2b-4923-424c-bc23-9dace311fa2c/6abee74807e975e10079a15e93f12850.png)

6.1.14如下图删除，main文件夹中，包名下的App文件

![img](http://10.105.222.200/upload/image/document/init/49a83f2b-4923-424c-bc23-9dace311fa2c/3224a394aedee0fdc520fef75303829e.png)

6.1.15增如下依赖，等待依赖导入完成。

![img](http://10.105.222.200/upload/image/document/init/d0ebfe69-aa4d-40dc-a474-8b629ef8c2bb/d7a0668ed86592d0ba7955948708a4bc.png)

> 图 3

![img](http://10.105.222.200/upload/image/document/init/d0ebfe69-aa4d-40dc-a474-8b629ef8c2bb/fa15f1440b0f872e0d9988e2dab361f3.png)

####  6.2 Spark Streaming 编程

6.2.1 在项目的scala文件夹下的文件夹中新建名为`kafka_streaming`的object。

6.2.2导入Object所需的 jar包,也可以在写代码的时候导入，不过要注意选择正确的jar包。

```java
import org.apache.kafka.common.serialization.StringDeserializer
import org.apache.spark.streaming.{Seconds, StreamingContext}
import org.apache.spark.SparkConf
import org.apache.spark.streaming.kafka010._
import org.apache.spark.streaming.kafka010.LocationStrategies.PreferConsistent
import org.apache.spark.streaming.kafka010.ConsumerStrategies.Subscribe
```

6.2.3在 object中新建main 方法

```scala
  def main(args: Array[String]): Unit = {}
```

6.2.4在main方法中创建本地的sparkconf对象，开启了两个本地线程来工作，APP名字命名为“kafka_test”。

```scala
val conf =new SparkConf().setMaster("local[2]").setAppName("kafka_test")
```

6.2.5创建本地的StreamingContext对象，第一参数为上一步创建spark
conf对象，第二个参数为处理的时间片间隔时间，设置为3秒

```scala
val ssc =new StreamingContext(conf, Seconds(3))
```

6.2.6Kafka消费者配置，**请同学们自行查询kafka配置中参数的含义，并写在报告中**

```scala
val kafkaParams = Map[String, Object](
      "bootstrap.servers" -> "master:9092",
      "key.deserializer" -> classOf[StringDeserializer],
      "value.deserializer" -> classOf[StringDeserializer],
      "group.id" -> "example",
      "auto.offset.reset" -> "latest",
      "enable.auto.commit" -> (false: java.lang.Boolean)
)
```

6.2.7Kafka主题设置

```scala
val topics = List("test_kafka").toSet
```

6.2.8创建DStream，返回接收到的输入数据

```scala
 val  stream = KafkaUtils.createDirectStream      [String, String](ssc,PreferConsistent, Subscribe[String,String](topics,kafkaParams) 		)
```

6.2.9每一个stream都是一个ConsumerRecord，获取每一个stream的value.`_`代表前面的变量`lines`

```scala
val lines = stream.map(_.value)
```

6.2.10使用flatMap和Split对收到的字符串进行分割

```scala
val words = lines.flatMap(_.split(" "))
```

6.2.11map操作将独立的单词映射到(word，1)元组，reduceByKey操作对pairs执行reduce操作获得（单词，词频）元组。

```scala
val wordcounts = words.map((_,1)).reduceByKey(_+_)
```

6.2.12输出结果

```scala
wordcounts.print()
```

6.2.13Streaming启动的流程，调用`start()`函数启动，`awaitTermination()`函数表示等待处理结束的信号。

```scala
ssc.start()  
ssc.awaitTermination()
```

#### 6.3使用maven打包程序

6.3.1使用鼠标依次点击如下位置，在控制台中观察生成的jar包的位置

![img](http://10.105.222.200/upload/image/document/init/d0ebfe69-aa4d-40dc-a474-8b629ef8c2bb/b64ae77f18f849476636a661f0e3c9eb.png)

![img](http://10.105.222.200/upload/image/document/init/d0ebfe69-aa4d-40dc-a474-8b629ef8c2bb/78f16097e43a8fe255f8e44057c27ac9.png)

#### 6.4启动Zookeeper集群

#### 6.5启动Kafka集群，创建topic

6.5.1在 master 和 slave01、slave02节点分别启动 Kafka，代码如下：

```shell
[zkpk@master kafka_2.11-0.10.2.1]$ bin/kafka-server-start.sh -daemon config/server.properties
```

6.5.2在master创建一个名为 test的主题topic

```shell
[zkpk@master kafka_2.11-0.10.2.1]$ bin/kafka-topics.sh --create --zookeeper master:2181 --replication-factor 1 --partitions 3 --topic test_kafka
```

- create表示创建一个topic
- zookeeper master:2181 表示连接zookeeper 的服务和端口号
- replication-factor 1表示创建副本数量是1
- partitions 3表示分区数量是3
- topic test_kafka表示创建一个名为“test_kafka”的topic主题

6.5.3在一个终端上启动一个生产者,准备生产。

```
[zkpk@master kafka_2.11-0.10.2.1]$ bin/kafka-console-producer.sh --broker-list master:9092 --topic test_kafka
```

- kafka-console-producer.sh表示启动一个生产者
- broker-list master:9092表示broker服务列表中的master服务和端口号
- topic test_kafka表示向名为test的topic中生产数据

6.5.4 上一条命令执行后，生产者已经启动，并且在等待输入。

#### 6.6使用spark-submit 提交spark 应用

6.6.1打开新的终端界面，将生成的jar包文件复制到/home/zkpk下

```shell
[zkpk@master ~]$cp  /home/zkpk/spark_test/target/zkpk-1.0.jar  /home/zkpk
```

6.6.2提交spark
job之前需要将spark-streaming-kafka-0-10_2.11-2.1.0，kafka-clients-0.10.2.这两个jar添加到
spark_home/jars/路径下，否则程序提交会报错

```shell
[zkpk@master ~]$ cp apache-maven-3.5.0/warehouse/org/apache/spark/spark-streaming-kafka-0-10_2.11/2.1.1/spark-streaming-kafka-0-10_2.11-2.1.1.jar   spark-2.1.1-bin-hadoop2.7/jars/[zkpk@master ~]$ cp kafka_2.11-0.10.2.1/libs/kafka-clients-0.10.2.1.jar   spark-2.1.1-bin-hadoop2.7/jars/
```

6.6.3新开一个终端，在/home/zkpk目录下提交程序，命令如下：（注意要写自己项目中的类的路径）

```bash
[zkpk@master ~]$ spark-submit --class yuejie.kafka_streaming spark_kafaka-1.0-SNAPSHOT.jar 
```

6.6.4切换到之前的Kafka生产者终端中输入如下内容，然后切换到spark
Streaming任务界面查看结果。

![img](http://10.105.222.200/upload/image/document/init/d0ebfe69-aa4d-40dc-a474-8b629ef8c2bb/8a68f7b753a2ec62aff4c6c02b37f3aa.png)

![img](http://10.105.222.200/upload/image/document/init/d0ebfe69-aa4d-40dc-a474-8b629ef8c2bb/1b8be253890d20367189b8bdb643c639.png)

6.6.5运行结束后分别使用Ctrl+C 关闭对应的终端即可。
