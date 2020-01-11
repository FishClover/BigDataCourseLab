Spark实验：Java Word Count
-----

1、实验描述
--------

-   利用Java语言开发Spark Word Count程序

-   实验时长：

    -   45分钟

-   主要步骤：

    -   创建spark项目

    -   使用Java API 编写Word Count程序

    -   打印结果

2、实验环境
--------

-   虚拟机数量：3

-   系统版本：Centos 7.5

-   Spark版本：Apache Spark 2.1.1

-   JDK版本： jdk-1.8.0

-   IDEA版本：ideaIC-2017.2.7

3、相关技能
--------

-   Spark core Java API


4、知识点
--------

-   常见linux命令的使用

-   Java IDEA编程

-   Spark core

-   SparkSession

-   Spark RDD

-   Tuple2

5、实验步骤
--------

1. 创建数据文件
   在zkpk家目录下创建进行单词计数的数据文件words.txt，包含以下内容：

   ```txt
   hello hi hi spark
   hello spark hello hi sparksql
   hello hi hi sparkstreaming
   hello hi sparkgraphx
   ```

2. 打开IDEA，创建并配置Java工程。

   1. 打开IDEA：

      ```shell
      /home/zkpk/idea-IC-172.4574.19/bin/idea.sh &
      ```

   2. 创建Maven项目：
      1. 可以选择`create from archetype`，并选用`org.apache.maven.archetypres:maven-archetype-quickstart`
      2. 重载Maven的User setting file路径为`/home/zkpk/apache-maven-3.5.0/conf/settings.xml`
      3. 重载Maven的Local repository路径为`/home/zkpk/apache-maven-3.5.0/warehouse`
      4. 配置`pom.xml`文件
         添加依赖项`spark-sql`,和`spark-core`，版本号为2.1.1
      5. 完成后使用maven自动导入依赖
   3. 设置项目的language level
      顺序进入以下选项卡`File` -> `Project Structure` -> `Modules` ，然后将language level设为8。
   4. 配置Java Compiler环境
      1. 顺序进入以下选项卡`File` -> `Setting` -> `Build, Execution, Deployment` -> `Compiler` -> `Java Compiler`
      2. 设置`Project bytecode version`为1.8
      3. 设置`Target bytecode version`为1.8
   5. 调整项目结构
      1. 删除src/test/目录下默认创建的文件
      2. 删除src/main/目录下默认创建的文件
      3. 对src/main/java目录右键，`Mark directory as` -> `Source Root`
      4. 在src/main/java目录下创建新的名为WordCount的Java Class

3. 编写Word Count程序
   *以下为供参考的大致实现思路

   1. 在main方法中创建spark session
   2. 使用`read().textFile()`方法将文档内容读入为`JavaRDD<String>`
   3. 使用`flatMap()`方法将文档内容转化为词序列
   4. 使用`mapToPair()`方法将词序列转换为键值对形式
   5. 使用`reduceByKey()`方法对词序列键值对进行分组和词频统计
   6. 使用`collect()`方法获得以上结果，将其存为`List<Tuple2<String, Integer>>`的形式
   7. 最后对其进行遍历并输出词频统计结果