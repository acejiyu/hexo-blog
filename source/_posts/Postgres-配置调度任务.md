---
title: Postgres 配置调度任务
copyright: true
date: 2019-01-16 21:04:52
categories: 数据库
tags:
	- postgres
	- pgAgent
---
postgres 数据库实现定时任务的方式之一是使用pgAgent插件，下面是pgAgent在Windows平台的配置步骤



### 安装相关软件

1. #### 安装postgres数据库

   [点此下载](https://www.postgresql.org/download/)

2. #### 安装pgAgent

   [点此下载](https://www.pgadmin.org/download/pgagent-source-code/)，也可以使用pgAdmin自带的安装工具安装

   安装成功后，pgAdmin界面会显示**pgAgentJobs**选项

![1547642544920](1547642544920.png)



### 设置调度任务

1. #### 创建调度任务

![1547642907046](1547642907046.png)

![1547642941160](1547642941160.png)

2. #### 创建调度

![1547643085395](1547643085395.png)

![1547643095867](1547643095867.png)

![1547643103115](1547643103115.png)

3. #### 创建任务

![1547643146469](1547643146469.png)

![1547643155007](1547643155007.png)

设置连接数据库必要的参数

```properties
host=localhost #数据库地址
port=5432 #数据库端口
dbname=demodb #数据库名
user=postgres #数据库用户名
password=000000	#数据库密码
connect_timeout=10 #超时时间
```

![1547644829747](1547644829747.png)

输入任务定时执行的SQL语句

```sql
INSERT INTO demot VALUES('test')
```



4. #### 查看执行履历

![1547644920317](1547644920317.png)