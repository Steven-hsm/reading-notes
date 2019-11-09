推荐阅读

​	[mysql索引及慢查询优化](https://tech.meituan.com/2014/06/30/mysql-index.html,"美团")	



## 存储ip

> INET_ATON()	 // address to number
>
> INET_NTOA()	//number to address

## 范式

1. 字段原子性

   > 字段原子性,不可分割

2. 消除对主键的部分依赖

   > 在表中增加一个与业务无关的字段作为主键.

3. 消除对主键的传递依赖

## 索引为什么快

关键字就是数据,数据量小

关键字使用二分查找,有序查找很快