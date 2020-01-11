Spark 实践：分析流量日志实验描述
--

1、实验描述
--------

-   使用Spark Java API完成流量日志数据的排序

-   实验时长：90分钟


2、实验环境
--------

-   虚拟机数量：1

-   系统版本：Centos 7.5

-   Spark版本：Apache Spark 2.1.1

-   Eclipse版本：Neno.3 （4.6.3）

## 3、相关技能

-   Spark原理

-   Hadoop原理

-   Shell

-   Spark Java API

4、知识点
------

-   二次排序

-   序列化

-   Spark RDD

## 5、实验效果

-   以下是分析后的流量日志结果：

![](http://10.105.222.200/upload/image/document/init/4117b74b-5d12-48fa-b2ae-11c3d51e255d/7b6022296ee8fef49cd725321a76c1da.png)

<center>图 1</center>
6、实验步骤
--------

6.1数据日志介绍

6.1.1数据字段

6.1.1.1reportTime（报告时间戳）

6.1.1.2telNum（手机号）

6.1.1.3upwardflow（上行总流量）

6.1.1.4downwardflow（下行总流量）

6.2需求分析

6.2.1对文本中数据记录进行排序，排序规则如下：

```shell
以telNum为基准，分别按照 upwardflow，downwardflow，reportTime进行降序排序，即先按照upwardflow排序，如果upwardflow相同，再比较downwardflow，如果downwardflow相同，再比较reportTime，最后选择前10条记录输出。
```

6.2.2具体实现步骤：

6.2.2.1按照 Serrializable 接口实现自定义排序的 Key

6.2.2.2将要进行二次排序的文件加载进来生成 key-value 类型的 RDD

6.2.2.3使用 sortByKey 基于自定义的 Key 进行二次排序

6.2.2.4去除掉排序的 key，只保留排序结果

6.3代码实现：

6.3.1创建maven项目
*tips：解决jar包下载过慢问题，按以下两步设置使用本地的maven

*1. 进入Eclipse-->window-->Prefrences-->Maven---->Installations 加入家目录下的maven安装文件夹 

*2. 进入window---->Preferences --->Maven---->User Settings 将usersetting重置成家目录下的maven安装文件夹里的conf/settings.xml

6.3.1.1打开Eclipse IDE

```shell
[zkpk@master ~]$ cd eclipse/
[zkpk@master eclipse]$ ./eclipse &

```

6.3.1.2点击 File -\> New -\>Other

![](http://10.105.222.200/upload/image/document/init/4117b74b-5d12-48fa-b2ae-11c3d51e255d/598671d9a5e2c4e4a599e0556789b6ed.png)

<center>图 2</center>
6.3.1.3搜索 maven -\> 点击 Maven Project -\> Next-\>Next

![](http://10.105.222.200/upload/image/document/init/4117b74b-5d12-48fa-b2ae-11c3d51e255d/f858d6b77d583042b493246f4b791d02.png)

<center>图 3</center>
![](http://10.105.222.200/upload/image/document/init/4117b74b-5d12-48fa-b2ae-11c3d51e255d/61bb227dbe9156baa2573e4ac3669f42.png)

<center>图 4</center>
6.3.1.4选中maven-archetype-quickstart -\>Next

![](http://10.105.222.200/upload/image/document/init/4117b74b-5d12-48fa-b2ae-11c3d51e255d/7f594897efa46a9a8108130af882c744.png)

<center>图 5</center>
6.3.1.5输入Group Id 为 org.zkpk；Artifact Id 为 spark ;Package为org.zkpk.spark
-\>Finish

![](http://10.105.222.200/upload/image/document/init/4117b74b-5d12-48fa-b2ae-11c3d51e255d/713f31d4b0a142495863985821ea9ef1.png)

<center>图 6</center>
6.3.1.6升级maven工程

![](http://10.105.222.200/upload/image/document/init/4117b74b-5d12-48fa-b2ae-11c3d51e255d/403cb9c9705e90c9deedc19fb34c8f13.png)

<center>图 7</center>
6.3.1.7更改JRE，右键JRE System Library-\> Properties

![](http://10.105.222.200/upload/image/document/init/4117b74b-5d12-48fa-b2ae-11c3d51e255d/56f467f682c3a1ef4da6ace998859fd8.png)

<center>图 8</center>
6.3.1.8选中jdk1.8.0_131-\> OK

![](http://10.105.222.200/upload/image/document/init/4117b74b-5d12-48fa-b2ae-11c3d51e255d/c30e2c77d03bcd82da283a74dd2445ed.png)

<center>图 9</center>
6.3.1.9点击spark项目-\>修改pom.xml-\> 保存会自动下载jar包

![](http://10.105.222.200/upload/image/document/init/4117b74b-5d12-48fa-b2ae-11c3d51e255d/242ad0d12618bb7d45aade0959be23e7.png)

<center>图 10</center>
![](http://10.105.222.200/upload/image/document/init/4117b74b-5d12-48fa-b2ae-11c3d51e255d/6e9eb8f294c06d1681b8b9bbae636000.png)

<center>图 11</center>
```shell
#添加如下内容并保存
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>3.8.1</version>
      <scope>test</scope>
    </dependency>
    <dependency>
      <groupId>org.apache.spark</groupId>
      <artifactId>spark-core_2.11</artifactId>
      <version>2.1.1</version>
    </dependency>
    <dependency>
      <groupId>org.apache.spark</groupId>
      <artifactId>spark-sql_2.11</artifactId>
      <version>2.1.1</version>
      </dependency>
    <dependency>
      <groupId>org.apache.spark</groupId>
      <artifactId>spark-hive_2.11</artifactId>
      <version>2.1.1</version>
    </dependency>
    <dependency>
      <groupId>org.apache.spark</groupId>
      <artifactId>spark-streaming_2.11</artifactId>
      <version>2.1.1</version>
    </dependency>
    <dependency>
      <groupId>org.apache.hadoop</groupId>
      <artifactId>hadoop-client</artifactId>
      <version>2.7.3</version>
    </dependency>
    <dependency>
      <groupId>org.apache.spark</groupId>
      <artifactId>spark-streaming-flume_2.11</artifactId>    
      <version>2.1.1</version>
    </dependency>
    <dependency>
      <groupId>org.apache.httpcomponents</groupId>
      <artifactId>httpclient</artifactId>
      <version>4.3.5</version>
    </dependency>
    <dependency>
      <groupId>org.apache.httpcomponents</groupId>
      <artifactId>httpcore</artifactId>
      <version>4.3.1</version>
    </dependency>

```


![](http://10.105.222.200/upload/image/document/init/4117b74b-5d12-48fa-b2ae-11c3d51e255d/ce49d93bcc863044ae9f2557a605ee3a.png)

<center>图 12</center>

```shell
<build>
    <sourceDirectory>src/main/java</sourceDirectory>
    <testSourceDirectory>src/main/test</testSourceDirectory>
    <plugins>
      <plugin>
        <artifactId>maven-assembly-plugin</artifactId>
        <configuration>
          <descriptorRefs>
            <descriptorRef>jar-with-dependencies</descriptorRef>
          </descriptorRefs>
          <archive>
            <manifest>
              <mainClass></mainClass>
            </manifest>
          </archive>
        </configuration>
        <executions>
          <execution>
            <id>make-assembly</id>
            <phase>package</phase>
            <goals>
              <goal>single</goal>
            </goals>
          </execution>
        </executions>
      </plugin>
      <plugin>
        <groupId>org.codehaus.mojo</groupId>
        <artifactId>exec-maven-plugin</artifactId>
        <version>1.2.1</version>
        <executions>
          <execution>
            <goals>
              <goal>exec</goal>
            </goals>
          </execution>
        </executions>
        <configuration>
          <executable>java</executable>
          <includeProjectDependencies>true</includeProjectDependencies>
          <includePluginDependencies>false</includePluginDependencies>
          <classpathScope>compile</classpathScope>
          <mainClass>cn.com.syl.spark.App</mainClass>
        </configuration>
      </plugin>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-compiler-plugin</artifactId>
        <configuration>
          <source>1.6</source>
          <target>1.6</target>
        </configuration>
      </plugin>
    </plugins>
  </build>

```

6.3.1.10最终 pom.xml 如下：

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <groupId>org.zkpk</groupId>
  <artifactId>spark</artifactId>
  <version>0.0.1</version>
  <packaging>jar</packaging>

  <name>spark</name>
  <url>http://maven.apache.org</url>

  <properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
  </properties>

  <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>3.8.1</version>
      <scope>test</scope>
    </dependency>
    
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>3.8.1</version>
      <scope>test</scope>
    </dependency>
    <dependency>
      <groupId>org.apache.spark</groupId>
      <artifactId>spark-core_2.11</artifactId>
      <version>2.1.1</version>
    </dependency>
    <dependency>
      <groupId>org.apache.spark</groupId>
      <artifactId>spark-sql_2.11</artifactId>
      <version>2.1.1</version>
      </dependency>
    <dependency>
      <groupId>org.apache.spark</groupId>
      <artifactId>spark-hive_2.11</artifactId>
      <version>2.1.1</version>
    </dependency>
    <dependency>
      <groupId>org.apache.spark</groupId>
      <artifactId>spark-streaming_2.11</artifactId>
      <version>2.1.1</version>
    </dependency>
    <dependency>
      <groupId>org.apache.hadoop</groupId>
      <artifactId>hadoop-client</artifactId>
      <version>2.7.3</version>
    </dependency>
    <dependency>
      <groupId>org.apache.spark</groupId>
      <artifactId>spark-streaming-flume_2.11</artifactId>    
      <version>2.1.1</version>
    </dependency>
    <dependency>
      <groupId>org.apache.httpcomponents</groupId>
      <artifactId>httpclient</artifactId>
      <version>4.3.5</version>
    </dependency>
    <dependency>
      <groupId>org.apache.httpcomponents</groupId>
      <artifactId>httpcore</artifactId>
      <version>4.3.1</version>
    </dependency>
        
  </dependencies>
 <build>
    <sourceDirectory>src/main/java</sourceDirectory>
    <testSourceDirectory>src/main/test</testSourceDirectory>

    <plugins>
      <plugin>
        <artifactId>maven-assembly-plugin</artifactId>
        <configuration>
          <descriptorRefs>
            <descriptorRef>jar-with-dependencies</descriptorRef>
          </descriptorRefs>
          <archive>
            <manifest>
              <mainClass></mainClass>
            </manifest>
          </archive>
        </configuration>
        <executions>
          <execution>
            <id>make-assembly</id>
            <phase>package</phase>
            <goals>
              <goal>single</goal>
            </goals>
          </execution>
        </executions>
      </plugin>

      <plugin>
        <groupId>org.codehaus.mojo</groupId>
        <artifactId>exec-maven-plugin</artifactId>
        <version>1.2.1</version>
        <executions>
          <execution>
            <goals>
              <goal>exec</goal>
            </goals>
          </execution>
        </executions>
        <configuration>
          <executable>java</executable>
          <includeProjectDependencies>true</includeProjectDependencies>
          <includePluginDependencies>false</includePluginDependencies>
          <classpathScope>compile</classpathScope>
          <mainClass>cn.com.syl.spark.App</mainClass>
        </configuration>
      </plugin>

      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-compiler-plugin</artifactId>
        <configuration>
          <source>1.6</source>
          <target>1.6</target>
        </configuration>
      </plugin>

    </plugins>
  </build>
 
</project>

```

6.3.1.11删除工程生成的App.java和AppTest.java

![](http://10.105.222.200/upload/image/document/init/4117b74b-5d12-48fa-b2ae-11c3d51e255d/98fc810e196755051e7d10acb624c613.png)

<center>图 14</center>
![](http://10.105.222.200/upload/image/document/init/4117b74b-5d12-48fa-b2ae-11c3d51e255d/ce53084d2c1ea9d4a30cb498721ba92e.png)

<center>图 15</center>
6.3.2编写代码

```java
// TODO 根据以上需求分析与具体实现的提示 编写代码达成最终实验效果
// 实验数据位于路径/home/zkpk/experiment/data.log
```