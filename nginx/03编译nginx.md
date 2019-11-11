## 下载nginx

[官网](nginx.org)

## 介绍各个目录

1. auto

   > cc 
   >
   > lib
   >
   > os
   >
   > types

2. CHANGES

   > 每个版本的改变和bug修复等

3. conf

   > 示例文件

4. conrtib

   > cp -r contrib/vim/* ~/.vim/ #显示配置文件的语法

5. html

   > 50x.html
   >
   > index.html

6. src 

   > 源代码目录

## configure

> ./configure --help | more 
>
> ./configure --prefix=/home/smile/nginx $ 指定安装目录

## 中间文件介绍

> 生成的中间文件会放在objs文件.ngx_moudles.c

## 编译

>  make 编译

## 安装

> make install

