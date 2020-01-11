Spark 实践：分析电商产品关注度 
--

1、实验描述
--------

-   使用Spark
    Streaming构建实时数据处理系统，来计算当前电商平台最受人们关注的商品是哪些。

-   实验时长：90分钟

-   主要步骤：

    -   编写数据模拟代码

    -   编写Socket模拟程序

    -   编写Streaming程序

    -   运行程序

2、实验环境
--------

-   虚拟机数量：1

-   系统版本：Centos 7.5

-   Spark版本：Apache Spark 2.1.1

-   Eclipse版本：Neno.3 （4.6.3）

## 3、相关技能

-   Spark原理

-   Shell

-   Spark Streaming

4、知识点
------

-   Java Scoket编程的基本原理

-   Spark Streaming 应用的基本实现

-   DStream 的基本操作

-   UpdateStateByKey的使用

## 5、实验效果

-   Streaming接受到的数据

![](http://10.105.222.200/upload/image/document/init/cf12ea7b-ce6f-47e1-b1f4-1d449fcb5e46/53ccd53cb5ffd27c02003e2029171e66.png)

<center>图 1</center>
-   Streaming计算的结果

![](http://10.105.222.200/upload/image/document/init/cf12ea7b-ce6f-47e1-b1f4-1d449fcb5e46/fb2eaf971fb84957aa5a27536e8a917b.png)

<center>图 2</center>
6、实验步骤
--------

6.1启动Eclipse客户端

```shell
[zkpk@master ~]$ cd eclipse/
[zkpk@master eclipse]$ ./eclipse  &

```

6.2新建java工程并导入依赖jar包

6.2.1新建java工程，名字hive，点击Finish（询问是否将Java
Project设为常用项目，选择yes，下次创建Java项目，可直接通过New-\>Java
Project创建

![](http://10.105.222.200/upload/image/document/init/cf12ea7b-ce6f-47e1-b1f4-1d449fcb5e46/59d9259a2795df012972f167760969d3.png)

<center>图 3</center>
![](http://10.105.222.200/upload/image/document/init/cf12ea7b-ce6f-47e1-b1f4-1d449fcb5e46/6be07a13b530d8364509784273252d7e.png)

<center>图 4</center>
6.2.2命名为EC，点击finish

![](http://10.105.222.200/upload/image/document/init/cf12ea7b-ce6f-47e1-b1f4-1d449fcb5e46/68e8ab19458c55165ef902e08caadf6c.png)

<center>图 5</center>
![](http://10.105.222.200/upload/image/document/init/cf12ea7b-ce6f-47e1-b1f4-1d449fcb5e46/7311c47b6e134de94c9be003c81f4a03.png)

<center>图 6</center>
6.2.3创建包org.zkpk.spark

![](http://10.105.222.200/upload/image/document/init/cf12ea7b-ce6f-47e1-b1f4-1d449fcb5e46/fa2c317fc73abc26ff9212506635debd.png)

<center>图 7</center>
![](http://10.105.222.200/upload/image/document/init/cf12ea7b-ce6f-47e1-b1f4-1d449fcb5e46/db87eda2c9c84c22f73c9e70e604ef8d.png)

<center>图 8</center>
6.2.4在工程名上单击鼠标右键，点击Build Path，点击Configure Build
Path，弹出窗口，选择Libraries，选择Add External Jars

![](http://10.105.222.200/upload/image/document/init/cf12ea7b-ce6f-47e1-b1f4-1d449fcb5e46/9d4ebb77c086f754eefe8fadb9138868.png)

<center>图 9</center>
![](http://10.105.222.200/upload/image/document/init/cf12ea7b-ce6f-47e1-b1f4-1d449fcb5e46/d57bb050a815b9e9f41f0b30b68cfdb6.png)

<center>图 10</center>
6.2.5将spark目录中的lib子目录的jar包全部导入

![](http://10.105.222.200/upload/image/document/init/cf12ea7b-ce6f-47e1-b1f4-1d449fcb5e46/dca8147f8477098c13cd43ab6a9c785a.png)

<center>图 11</center>
![](http://10.105.222.200/upload/image/document/init/cf12ea7b-ce6f-47e1-b1f4-1d449fcb5e46/00320717a6b860842f6ad291a5b99d08.png)

<center>图 12</center>
6.3创建数据模拟器

6.3.1在package中创建类，名为SocketSimulator

![](http://10.105.222.200/upload/image/document/init/cf12ea7b-ce6f-47e1-b1f4-1d449fcb5e46/5c89435af7315107b1cf664e65fd19e1.png)

<center>图 13</center>
![](http://10.105.222.200/upload/image/document/init/cf12ea7b-ce6f-47e1-b1f4-1d449fcb5e46/6b71ffbe5fdd59be79d2c4fbc8540be5.png)

<center>图 14</center>
6.3.2代码如下

```shell
package org.zkpk.spark;
//导入需要的包
import java.io.IOException;
import java.io.OutputStream;
import java.io.PrintWriter;
import java.net.ServerSocket;
import java.net.Socket;
import java.util.Random;

public class SocketSimulator {
	public static void main(String[] args) throws Exception {
		// 创建一个线程来启动模拟器
		new Thread(new SimulatorSocketLog()).start();
	}
}

class SimulatorSocketLog implements Runnable {
	// 假设一共有500个商品
	private int GOODSID = 500;
	// 随机发送消息的条数
	private int MSG_NUM = 20;
	// 模拟用户浏览该商品的次数
	private int BROWSE_NUM = 10;
	// 模拟用户浏览商品停留的时间
	private int STAY_TIME = 20;
	// 用来体现用户是否收藏，收藏为1，不收藏为0，差评为-1
	int[] COLLECTION = new int[] { -1, 0, 1 };
	// 用来模拟用户购买商品的件数，0比较多是为了增加没有买的概率，因为很多用户都只是看看
	private int[] BUY_NUM = new int[] { 0, 1, 0, 3, 1, 0, 2, 1, 0, 3, 0, 0, 0 };

	public void run() {

		Random r = new Random();

		try {
			/**
			 * 创建一个服务器端，监听9999端口，客户端就是Streaming，通过看源码才知道，Streaming
			 * *socketTextStream 其实就是相当于一个客户端
			 */
			@SuppressWarnings("resource")
			ServerSocket serverSocket = new ServerSocket(9999);
			System.out.println("数据模拟模块已经开启，请运行Streaming程序！");
			while (true) {
				// 随机消息数
				int msgNum = r.nextInt(MSG_NUM) + 1;
				// 开始监听
				Socket socket = serverSocket.accept();
				// 创建输出流
				OutputStream os = socket.getOutputStream();
				// 包装输出流
				PrintWriter pw = new PrintWriter(os);
				for (int i = 0; i < msgNum; i++) {
					// 消息格式--商品ID--浏览次数--停留时间--是否收藏--购买件数
					StringBuffer strBuf = new StringBuffer();
					strBuf.append("goodsID:" + (r.nextInt(GOODSID) + 1));
					strBuf.append("\t");
					strBuf.append(r.nextInt(BROWSE_NUM) + 1);
					strBuf.append("\t");
					strBuf.append(r.nextInt(STAY_TIME) + r.nextFloat());
					strBuf.append("\t");
					strBuf.append(COLLECTION[r.nextInt(2)]);
					strBuf.append("\t");
					strBuf.append(BUY_NUM[r.nextInt(9)]);
					System.out.println(strBuf.toString());
					// 发送消息
					pw.write(strBuf.toString() + "\n");
				}
				pw.flush();
				pw.close();
				try {
					Thread.sleep(5000);
				} catch (InterruptedException e) {
					// TODO Auto-generated catch block
					System.out.println("thread sleep failed");
				}
			}
		} catch (IOException e) {
			// TODO Auto-generated catch block
			System.out.println("port used");
		}
	}
}

```

6.4流式计算，Streaming 程序

6.4.1在package中创建类，名为Streaming

![](http://10.105.222.200/upload/image/document/init/cf12ea7b-ce6f-47e1-b1f4-1d449fcb5e46/a827dc3f0cab6edca2b5cb397224312f.png)

<center>图 15</center>
6.4.1.1初始化StreamingContext：使用Java初始化Streaming程序，要先定义一个JavaStreamingContext。

```shell
// AppName 是当前Job的名字，Master是Spark的主机地址，这里采用的是本地模式
		SparkConf sparkConf = new SparkConf().setAppName("Streaming").setMaster("local[2]");
		// 初始化StreamingContext：使用Java初始化Streaming程序，要先定义一个JavaStreamingContext。
		JavaStreamingContext jsc = new JavaStreamingContext(sparkConf, new Duration(5000));
		// new Duration(5000) 窗口时间，单位毫秒

```

6.4.1.2获取模拟数据

```shell
JavaReceiverInputDStream<String> jds = jsc.socketTextStream("127.0.0.1", 9999);
```

6.4.1.3消息处理：定义浏览次数的权重为0.8，浏览时间的权重为0.6,是否收藏和购买力度为1。

```shell
// mapToPair是将rdd转换为键值对rdd,与map不同的是添加了一个key
		JavaPairDStream<String, Double> splitMess = jds.mapToPair(new PairFunction<String, String, Double>() {
			private static final long serialVersionUID = 1L;

			public Tuple2<String, Double> call(String line) throws Exception {
				// TODO Auto-generated method stub.
				String[] lineSplit = line.toString().split("\t");
				//消息处理：定义浏览次数的权重为0.8，浏览时间的权重为0.6,是否收藏和购买力度为1。
				Double followValue = Double.parseDouble(lineSplit[1]) * 0.8 + Double.parseDouble(lineSplit[2]) * 0.6
						+ Double.parseDouble(lineSplit[3]) * 1 + Double.parseDouble(lineSplit[4]) * 1;
				return new Tuple2<String, Double>(lineSplit[0], followValue);
			}
		});

```

6.4.1.4更新关注度值：由于是流式数据，数据每分每秒都在产生，计算你的关注值在变化，状态值就需要更新，使用updateStateByKey来操作。本类是通过updateStateByKey来以Batch
Interval为单位来对历史状态进行更新，在这里需要使用checkPoint，用于保存父RDD的值。

```shell
// 更新关注度值：由于是流式数据，数据每分每秒都在产生，计算的关注值在变化，状态值就需要更新，以下方法用来保存关注值变化
		Function2<List<Double>, Optional<Double>, Optional<Double>> updateFunction = new Function2<List<Double>, Optional<Double>, Optional<Double>>() {
			@Override
			public Optional<Double> call(List<Double> newValues, Optional<Double> statValue) {
				Double updateValue = statValue.or(0.0);
				for (Double values : newValues) {
					updateValue += values;
				}
				return Optional.of(updateValue);
			}
		};
		// 使用updateStateByKey来操作。本类是通过updateStateByKey来以BatchInterval为单位来对历史状态进行更新，在这里需要使用checkPoint，用于保存父RDD的值。
		JavaPairDStream<String, Double> UpdateFollowValue = splitMess.updateStateByKey(updateFunction);

```

6.4.1.5输出关注度值：结果输出，并将里面的商品进行关注度排序，降序排序，只显示关注度最高的10个商品。

```shell
	//输出关注度值：结果输出，并将里面的商品进行关注度排序，降序排序，只显示关注度最高的10个商品
		UpdateFollowValue.foreachRDD(new VoidFunction<JavaPairRDD<String, Double>>() {
			private static final long serialVersionUID = 1L;
			public void call(JavaPairRDD<String, Double> followValue) throws Exception {
				// TODO Auto-generated method stub
				JavaPairRDD<Double, String> followValueSort = followValue
						.mapToPair(new PairFunction<Tuple2<String, Double>, Double, String>() {

							public Tuple2<Double, String> call(Tuple2<String, Double> valueToKey) throws Exception {
								// TODO Auto-generated method stub
								return new Tuple2<Double, String>(valueToKey._2, valueToKey._1);
							}
						}).sortByKey(false);
				List<Tuple2<String, Double>> list = followValueSort
						.mapToPair(new PairFunction<Tuple2<Double, String>, String, Double>() {

							public Tuple2<String, Double> call(Tuple2<Double, String> arg0) throws Exception {
								// TODO Auto-generated method stub
								return new Tuple2<String, Double>(arg0._2, arg0._1);
							}
						}).take(10);
				for (Tuple2<String, Double> tu : list) {
					System.out.println("商品ID: " + tu._1 + "  关注度: " + tu._2);
				}
			}
		});

```

6.4.1.6最终的Streaming.java的代码

```shell
package org.zkpk.spark;

import java.io.Serializable;
import java.util.List;

import org.apache.spark.SparkConf;
import org.apache.spark.api.java.JavaPairRDD;
import org.apache.spark.api.java.Optional;
import org.apache.spark.api.java.function.Function;
import org.apache.spark.api.java.function.Function2;
import org.apache.spark.api.java.function.PairFunction;
import org.apache.spark.api.java.function.VoidFunction;
import org.apache.spark.streaming.Duration;
import org.apache.spark.streaming.api.java.JavaDStream;
import org.apache.spark.streaming.api.java.JavaPairDStream;
import org.apache.spark.streaming.api.java.JavaReceiverInputDStream;
import org.apache.spark.streaming.api.java.JavaStreamingContext;

import scala.Tuple2;

public class Streaming implements Serializable {
	private static final long serialVersionUID = 1L;
	// 定义一个文件夹，用于保存上一个RDD的数据。该文件夹会自动创建，不需要提前创建
	private static String RDDFolder = "RFolder";

	@SuppressWarnings({ "serial", "resource" })
	public static void main(String[] args) throws InterruptedException {
		// AppName 是当前Job的名字，Master是Spark的主机地址，这里采用的是本地模式
		SparkConf sparkConf = new SparkConf().setAppName("Streaming").setMaster("local[2]");
		// 初始化StreamingContext：使用Java初始化Streaming程序，要先定义一个JavaStreamingContext。
		JavaStreamingContext jsc = new JavaStreamingContext(sparkConf, new Duration(5000));
		// new Duration(5000) 窗口时间，单位毫秒
		jsc.checkpoint(RDDFolder);
		// 获取模拟数据
		JavaReceiverInputDStream<String> jds = jsc.socketTextStream("127.0.0.1", 9999);
		JavaDStream<String> JDsocket = jds.map(new Function<String, String>() {
			private static final long serialVersionUID = 1L;

			public String call(String arg0) throws Exception {
				// TODO Auto-generated method stub
				return arg0;
			}
		});
		JDsocket.print();
		// mapToPair是将rdd转换为键值对rdd,与map不同的是添加了一个key
		JavaPairDStream<String, Double> splitMess = jds.mapToPair(new PairFunction<String, String, Double>() {
			private static final long serialVersionUID = 1L;

			public Tuple2<String, Double> call(String line) throws Exception {
				// TODO Auto-generated method stub.
				String[] lineSplit = line.toString().split("\t");
				// 消息处理：定义浏览次数的权重为0.8，浏览时间的权重为0.6,是否收藏和购买力度为1。
				Double followValue = Double.parseDouble(lineSplit[1]) * 0.8 + Double.parseDouble(lineSplit[2]) * 0.6
						+ Double.parseDouble(lineSplit[3]) * 1 + Double.parseDouble(lineSplit[4]) * 1;
				return new Tuple2<String, Double>(lineSplit[0], followValue);
			}
		});
		// 更新关注度值：由于是流式数据，数据每分每秒都在产生，计算的关注值在变化，状态值就需要更新，以下方法用来保存关注值变化
		Function2<List<Double>, Optional<Double>, Optional<Double>> updateFunction = new Function2<List<Double>, Optional<Double>, Optional<Double>>() {
			@Override
			public Optional<Double> call(List<Double> newValues, Optional<Double> statValue) {
				Double updateValue = statValue.or(0.0);
				for (Double values : newValues) {
					updateValue += values;
				}
				return Optional.of(updateValue);
			}
		};
		// 使用updateStateByKey来操作。本类是通过updateStateByKey来以BatchInterval为单位来对历史状态进行更新，在这里需要使用checkPoint，用于保存父RDD的值。
		JavaPairDStream<String, Double> UpdateFollowValue = splitMess.updateStateByKey(updateFunction);
		//输出关注度值：结果输出，并将里面的商品进行关注度排序，降序排序，只显示关注度最高的10个商品
		UpdateFollowValue.foreachRDD(new VoidFunction<JavaPairRDD<String, Double>>() {
			private static final long serialVersionUID = 1L;
			public void call(JavaPairRDD<String, Double> followValue) throws Exception {
				// TODO Auto-generated method stub
				JavaPairRDD<Double, String> followValueSort = followValue
						.mapToPair(new PairFunction<Tuple2<String, Double>, Double, String>() {

							public Tuple2<Double, String> call(Tuple2<String, Double> valueToKey) throws Exception {
								// TODO Auto-generated method stub
								return new Tuple2<Double, String>(valueToKey._2, valueToKey._1);
							}
						}).sortByKey(false);
				List<Tuple2<String, Double>> list = followValueSort
						.mapToPair(new PairFunction<Tuple2<Double, String>, String, Double>() {
							public Tuple2<String, Double> call(Tuple2<Double, String> arg0) throws Exception {
								// TODO Auto-generated method stub
								return new Tuple2<String, Double>(arg0._2, arg0._1);
							}
						}).take(10);
				for (Tuple2<String, Double> tu : list) {
					System.out.println("商品ID: " + tu._1 + "  关注度: " + tu._2);
				}
			}
		});
		jsc.start();
		jsc.awaitTermination();
	}
}

```

6.4.2运行程序：

6.4.2.1先启动模拟器，模拟数据。相当于服务端

6.4.2.2右键数据模拟程序SocketSimulator.java - - \>Run As - - \> Java
Application

![](http://10.105.222.200/upload/image/document/init/cf12ea7b-ce6f-47e1-b1f4-1d449fcb5e46/a4cf863e0eed7a2ea2b0ca3edb9771e5.png)

<center>图 16</center>
6.4.2.3当终端显示“数据模拟模块已经开启，请运行Streaming程序！”，在运行Streaming程序。

6.4.3Streaming接受到的数据

![](http://10.105.222.200/upload/image/document/init/cf12ea7b-ce6f-47e1-b1f4-1d449fcb5e46/53ccd53cb5ffd27c02003e2029171e66.png)

<center>图 17</center>
6.4.4Streaming计算的结果

![](http://10.105.222.200/upload/image/document/init/cf12ea7b-ce6f-47e1-b1f4-1d449fcb5e46/fb2eaf971fb84957aa5a27536e8a917b.png)

<center>图 18</center>
6.4.5刷新工程，查看是否创建RFolder文件

![](http://10.105.222.200/upload/image/document/init/cf12ea7b-ce6f-47e1-b1f4-1d449fcb5e46/e128158ace947321f613e3170549abef.png)

<center>图 19</center>
 总结
-----

本节课主要介绍如何Spark流式计算电商产品的关注度，模拟了用户操作，用程序计算了关注度