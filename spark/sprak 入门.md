# spark 环境安装

spark的存储需要使用hdfs,所以这里先安装hadoop2.7,安装配置完成之后再安装spark

## 1. hadoop2.7安装

为了防止网速过慢,这里推荐一个网址 [hadoop2.7.7](http://mirror.bit.edu.cn/apache/hadoop/common/hadoop-2.7.7/hadoop-2.7.7.tar.gz)

### 1.1 获取安装包

````shell
wget http://archive.apache.org/dist/hadoop/common/hadoop-2.7.5/hadoop-2.7.5.tar.gz
````

### 1.2  解压到自己想放的目录

```shell
tar -zxvf hadoop-2.7.7.tar.gz -C /export/servers/
```

### 1.3 配置hadoop环境变量

```shell
export HADOOP_HOME=/export/servers/hadoop-2.7.7
export PATH=:$HADOOP_HOME/bin:$HADOOP_HOME/sbin:$PATH
```

### 1.4 使配置生效

```shell
 source /etc/profile
```

### 1.5 创建目录用于hadoop配置

```shell
mkdir  /mnt/hadoop  
mkdir  /mnt/hadoop/tmp  
mkdir  /mnt/hadoop/var  
mkdir  /mnt/hadoop/dfs  
mkdir  /mnt/hadoop/dfs/name  
mkdir  /mnt/hadoop/dfs/data
```

### 1.6 修改hadoop配置文件

#### 1.6.1  core-site.xml 

```xml
<?xml version="1.0" encoding="UTF-8"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
<configuration>
	<!-- 临时目录 -->
	<property>
        <name>hadoop.tmp.dir</name>
        <value>/mnt/hadoop/tmp</value>
        <description>Abase for other temporary directories.</description>
   </property>
   <!-- hdfs地址 -->
   <property>
        <name>fs.default.name</name>
        <value>hdfs://192.168.204.104:9000</value>
   </property>
</configuration>
```

#### 1.6.2  hadoop-env.sh

```shell
export JAVA_HOME=/export/servers/jdk1.8.0_241
```

#### 1.6.3  hdfs-site.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
<configuration>
	<property>
		<name>dfs.name.dir</name>
		<value>/mnt/hadoop/dfs/name</value>
		<description>Path on the local filesystem where theNameNode stores the namespace and transactions logs persistently.</description>
	</property>
	<property>
		<name>dfs.data.dir</name>
		<value>/mnt/hadoop/dfs/data</value>
		<description>Comma separated list of paths on the localfilesystem of a DataNode where it should store its blocks.</description>
	</property>
	<property>
		<name>dfs.replication</name>
		<value>2</value>
	</property>
	<property>
		<name>dfs.permissions</name>
		<value>false</value>
		<description>need not permissions</description>
	</property>
</configuration>
```

#### 1.6.4  mapred-site.xml

```xml
<?xml version="1.0"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
<configuration>
	<property>
		<name>mapred.job.tracker</name>
		<value>192.168.204.104:9001</value>
	</property>
	<property>
		  <name>mapred.local.dir</name>
		   <value>/mnt/hadoop/var</value>
	</property>
	<property>
		   <name>mapreduce.framework.name</name>
		   <value>yarn</value>
	</property>
</configuration>
```

### 1.7 Hadoop启动

#### 1.7.1 初始化hadoop

只需要第一次初始化

```shell
bin/hadoop  namenode  -format
```

#### 1.7.2 启动hadoop

```shell
sbin/start-dfs.sh 
sbin/start-yarn.sh 
```

#### 1.7.3 检查是否输出成功

输入：

```shell
jps
```

输出：

```log
2050 SecondaryNameNode
2371 NodeManager
2277 ResourceManager
1850 NameNode
1935 DataNode
2655 Jps
```

#### 1.7.4 浏览器输出测试

集群：http://192.168.204.104:8088/cluster

hdfs存储： http://192.168.204.104:50070

## 2. spark安装

### 2.1 获取安装包

spark-2.1.1-bin-hadoop2.7.zip

官网太慢这里推荐使用 链接：https://pan.baidu.com/s/1CPGHRMJKB5ulZwL78S6R3g 提取码：rd0l 

也可以加qq群: 1059788876

### 2.2 解压安装包

```shell
 tar -zxvf spark-2.1.1-bin-hadoop2.7.tgz -C /export/servers/
 #修改包名,原来的太长了,当然也可以不修改
 mv spark-2.1.1-bin-hadoop2.7 spark
```

### 2.3 安装测试

```shell
bin/spark-submit \
--class org.apache.spark.examples.SparkPi \
--executor-memory 1G \
--total-executor-cores 2 \
./examples/jars/spark-examples_2.11-2.1.1.jar \
100
```

## 3.spark wordCount

### 3.1 新建三个文件

在spark下新建input目录,新建三个文件

```shell
1.txt 	hello scala hello spark
2.txt	hello spark
3.txt	hello java hello spark
```

### 3.2  加载数据

```shell
sc.textFile("input") #读取本地文件input文件夹数据
	.flatMap(_.split(" ")) #压平操作,按照空格将一行数据使用空格分隔
	.map((_,1)) #对每一个元素操作,将单词映射为元组
	.reduceByKey(_+_) #按照key值进行聚合相加
	.collect #将数据收集到driver端展示
```

输出结果

```shell
res0: Array[(String, Int)] = Array((scala,1), (hello,5), (java,1), (spark,3))
```



