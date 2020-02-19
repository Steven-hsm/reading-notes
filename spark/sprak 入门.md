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





