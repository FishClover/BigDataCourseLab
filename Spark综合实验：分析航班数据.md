# Spark综合实验：分析航班数据

## 1、实验描述

* 基于航班记录数据，使用Spark SQL对特定指标进行统计分析

* 实验时长：45分钟

* 主要步骤：
    * 使用IDEA创建maven工程编写Scala代码进行数据清洗
    * 启动Spark on yarn集群
    * 利用Spark SQL完成统计分析需求

## 2、实验环境

-   虚拟机数量：3
-   系统版本：Centos 7.5
-   Hadoop版本：Apache Hadoop 2.7.3
-   Scala版本：2.11.11
-   spark版本：2.1.1

## 3、相关知识点

-   maven项目管理软件使用
-   Scalar基础语法
-   Spark SQL用户接口

## 4、实验步骤

### 4.1 数据清洗

航班数据集（文件路径/home/zkpk/experiment/spark/2000.csv）格式如下：

|      | **Name**          | **Description**                                              | 字段描述                                         |
| ---- | ----------------- | ------------------------------------------------------------ | ------------------------------------------------ |
| 1    | Year              | 1987-2008                                                    | 此飞行记录所属年份                               |
| 2    | Month             | 1\~12                                                        | 此飞行记录所属月份                               |
| 3    | DayofMonth        | 1\~31                                                        | 此飞行记录所属当月的第几天                       |
| 4    | DayOfWeek         | 1 (Monday) - 7 (Sunday)                                      | 此飞行发生在本周第几天                           |
| 5    | DepTime           | actual departure time (local, hhmm)                          | 实际飞离机场时间（小时分钟，如1940表示19点40分） |
| 6    | CRSDepTime        | scheduled departure time (local, hhmm)                       | 计划分离机场时间（小时分钟，如1940表示19点40分） |
| 7    | ArrTime           | actual arrival time (local, hhmm)                            | 实际抵达机场时间（小时分钟，如1940表示19点40分） |
| 8    | CRSArrTime        | scheduled arrival time (local, hhmm)                         | 计划抵达机场时间（小时分钟，如1940表示19点40分） |
| 9    | UniqueCarrier     | unique carrier code                                          | 航空公司代码                                     |
| 10   | FlightNum         | flight number                                                | 航班号                                           |
| 11   | TailNum           | plane tail number                                            | 机尾编号                                         |
| 12   | ActualElapsedTime | in minutes                                                   | 航程实际耗时（单位分钟）                         |
| 13   | CRSElapsedTime    | in minutes                                                   | 航程计划耗时（单位分钟）                         |
| 14   | AirTime           | in minutes                                                   | 飞机空中飞行时长（单位分钟）                     |
| 15   | ArrDelay          | arrival delay, in minutes                                    | 抵达晚点时长（单位分钟）                         |
| 16   | DepDelay          | departure delay, in minutes                                  | 出发延误时长（单位分钟）                         |
| 17   | Origin            | origin IATA airport code                                     | 出发所在机场编号（国际航空运输协会指定）         |
| 18   | Dest              | destination IATA airport code                                | 抵达机场编号（国际航空运输协会指定）             |
| 19   | Distance          | in miles                                                     | 航程（单位英里）                                 |
| 20   | TaxiIn            | taxi in time, in minutes                                     |                                                  |
| 21   | TaxiOut           | taxi out time in minutes                                     |                                                  |
| 22   | Cancelled         | was the flight cancelled?                                    | 航班是否取消                                     |
| 23   | CancellationCode  | reason for cancellation (A = carrier, B = weather, C = NAS, D = security) | 航班取消代码                                     |
| 24   | Diverted          | 1 = yes, 0 = no                                              | 是否改道（1是，0否）                             |
| 25   | CarrierDelay      | in minutes                                                   |                                                  |
| 26   | WeatherDelay      | in minutes                                                   | 天气原因延迟                                     |
| 27   | NASDelay          | in minutes                                                   |                                                  |
| 28   | SecurityDelay     | in minutes                                                   | 安全原因延迟                                     |
| 29   | LateAircraftDelay | in minutes                                                   |                                                  |

机场数据集（文件路径/home/zkpk/experiment/spark/airports.csv）格式如下：

|   | Name | 字段描述   |
| - |  -----  | ------      |
| 1 | iata    | 到达目的地 |
| 2 | airport | 机场       |
| 3 | city    | 城市       |
| 4 | state   | 州         |
| 5 | country | 国家       |
| 6 | lat     | 纬度       |
| 7 | long    | 经度       |

数据清洗数据需求描述：将机场数据集中的iata、airport、city、state、country字段取出，存为一个独立的csv文件/home/zkpk/experiment/spark/part.csv。

实验步骤提示如下：

1.  打开IDEA，配置软件包依赖，创建工程 
2.  编写scala程序完成机场文件的清洗
    1.  创建Scala object
    2.  在main方法中编写主要的数据清洗逻辑
        1.  创建SparkSession.Builder对象
        2.  调用SparkSession对象的read方法，读取csv文件，返回Dataframe对象
        3.  将Dataframe注册为一个临时视图
        4.  从临时视图中使用Spark SQL查询选择要求的字段，以生成新的Dataframe对象
        5.  将新的Dataframe对象写入为新的csv文件/home/experiment/spark/airport-part.csv 
        6.  调用SparkSession的close方法，释放资源
    3.  直接在IDEA中运行程序代码
3.  检查相应路径是否成功生成经过清洗的csv数据文件



### 4.2 完成统计分析需求

​	实验步骤提示如下：

1.  由于此次实验我们需要用到Spark on yarn模式，因此首先启动Hadoop集群，并使用jps命令确认相关进程启动状态
2.  设置hadoop退出安全模式
3.  将需要用到的两个csv数据文件2000.csv和airport-part.csv放入HDFS
4.  启动Spark shell
5.  在Spark shell中实现以下需求：
    1.  分别读入航班数据集2000.csv和机场数据集airport-part.csv为dataframe
    2.  将以上两个dataframe分别注册成临时视图
    3.  分析出航班数据集中共有哪些航班（找出unique或是distinct的航班号）
    4.  统计最繁忙的5条航线
    5.  统计最空闲的5条航线
    6.  统计最繁忙的机场
    7.  统计最繁忙的航班，及其飞行次数
    8.  统计延误最少的航班
    9.  统计起飞延误最少的5个机场
    10.  统计到达准点率最高的航班
    11.  统计抵达准点率最高的机场