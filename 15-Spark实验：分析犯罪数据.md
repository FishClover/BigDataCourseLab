Spark实验：分析犯罪数据
--
1、实验描述
--------

-   使用SparkSQL分析芝加哥市犯罪数据

-   实验时长：90分钟


2、实验环境
--------

-   虚拟机数量：1

-   系统版本：Centos 7.5

-   Spark版本：Apache Spark 2.1.1

## 3、相关技能

-   pyspark客户端的使用
-   使用spark sql进行数据统计

4、知识点
------

-   常见linux命令的使用

-   SQL


## 5、实验步骤

5.1数据集介绍

5.1.1数据包含22个字段

```shell
`id：`unique  id
`Case_Number：`案件号码
`Date：`犯罪日期
`Block：`犯罪地点的街区地址
`IUCR：`国际自然保护联盟的案件ID
`Primary_type：`犯罪类型
`Description：`犯罪描述
`Location_description：`犯罪地点描述
`Arrest ：`是否被捕
`Domestic：`犯罪类型
`Beat ：`是否被警察击毙
`District ： `区代码
`Ward ： `病房的犯罪地点
`community ：` 社区服务
`Fbicode ： `联邦调查局的犯罪代码
`X-Corordinate：`X坐标 
`Y-Corordinate：`Y坐标 
`Year：`犯罪年
`Updated on：`最后更新的日期
`lattitude：`维度
`longitude：`经度
`location：`具有经纬度的完整位置

```

5.2启动pyspark。

```shell
[zkpk@master ~]$ cd spark-2.1.1-bin-hadoop2.7/bin
[zkpk@master bin]$ ./pyspark

```

5.3导入数据并进行转换数据

5.3.1导入依赖

```shell
>>>from pyspark.sql import SQLContext,Row
```

5.3.2加载数据集

```shell
>>>cli =sc.textFile("/home/zkpk/experiment/Crime_data")
```

5.3.3转换数据。

```shell
>>>tran = cli.map(lambda l: l.split(","))
>>>Crimes = tran.map(lambda p:Row(id=p[0],case_number =p[1],date=p[2],block=p[3],IUCR=p[4],primary_type=p[5],description=p[6],loc_desc=p[7],arrest=p[8],domestic=p[9],beat=p[10],district=p[11],ward=p[12],community=p[13],fbicode=p[14],xcor=p[15],ycor=p[16],year=p[17],updated_on=p[18],lattitude=p[19],longitude =p[20], location=p[21]))

```

5.3.4创建 DataFrame 并注册成表，表名为”crimes”

```shell
>>>sqlContext.createDataFrame(Crimes).registerTempTable("crimes")
```

5.4使用 spark sql 查询分析犯罪数据。

5.4.1统计每个 FBI 代码发生的犯罪次数。

```python
>>>sqlContext.sql("select fbicode,count(fbicode) as count from crimes group by fbicode").show()
```

5.4.2统计2015年芝加哥市各种类型的犯罪次数

```shell
>>>#TODO 参考5.4.1中的sql代码完成需求
```

5.4.4统计芝加哥市每个区域发生的各种犯罪行为的逮捕次数

```shell
>>>#TODO 参考5.4.1中的sql代码完成需求
```

