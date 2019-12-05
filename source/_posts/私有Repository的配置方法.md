---
title: 私有Repository的配置方法
copyright: true
date: 2019-09-09 09:37:22
categories: nexus
tags:
	- 私有仓库
  - Repository
---



### 前言
以下配置方法适用于使用Nexus3.x搭建的私用仓库，包括npm、maven、bower本地使用时的配置方法。
Nexus3.x的下载和安装以及Nexus3.x服务的配置请参考相关资料

### npm配置

1. 电脑已经安装node.js，打开cmd控制台，执行以下指令

   ```shell
   npm config get registry
   
   #返回当前使用的仓库地址：
   >https://registry.npmjs.org/
   ```

2. 执行以下指令，将仓库地址切换为本地仓库

   ```shell
   npm config set registry http://<host>:<port>/repository/npm-group/
   ```

3. 执行以下命令，确认仓库切换成功

   ```shell
   npm config get registry
   
   #返回当前使用的仓库地址：
   >http://<host>:<port>/repository/npm-group/
   ```



### maven配置

1. 访问文件夹目录：`C:\Users\<user_name>\.m2`

2. 在该目录下创建`setting.xml`文件，文件内容如下：
    [点击下载](settings.zip) 

   ```xml
   <settings>
     <mirrors>
       <mirror>
       <!--This sends everything else to /public -->
       <id>nexus</id>
       <mirrorOf>*</mirrorOf>
       <url>http://<host>:<port>/repository/maven-public/</url>
       </mirror>
     </mirrors>
     <profiles>
       <profile>
       <id>nexus</id>
       <!--Enable snapshots for the built in central repo to direct -->
       <!--all requests to nexus via the mirror -->
       <repositories>
           <repository>
           <id>central</id>
           <url>http://central</url>
           <releases><enabled>true</enabled></releases>
           <snapshots><enabled>true</enabled></snapshots>
           </repository>
       </repositories>
       <pluginRepositories>
           <pluginRepository>
           <id>central</id>
           <url>http://central</url>
           <releases><enabled>true</enabled></releases>
           <snapshots><enabled>true</enabled></snapshots>
           </pluginRepository>
       </pluginRepositories>
       </profile>
     </profiles>
     <activeProfiles>
       <!--make the profile active all the time -->
       <activeProfile>nexus</activeProfile>
     </activeProfiles>
   </settings>
   ```

2. 打开ecilpse，选择以下设置选项：

   Preferences>Maven>User Settings>User Settings>Browser...

   选择<1>中创建的文件，点击`Update Settings`按钮

3. 点击`Apply and Close`保存退出

5. 选择maven项目，`Alt+F5`确认maven仓库切换成功

   

### bower配置

1. 执行以下命令，安装bower的nexus3依赖组件

   ```shell
   npm i bower-nexus3-resolver -g
   ```

2. 访问文件夹目录：`C:\Users\<user_name>\`

3. 在该目录下创建`.bowerrc`文件，文件内容如下：
    [点击下载](bowerrc.zip) 
   
   ```json
   {
     "registry": {
       "search": [
         "http://<host>:<port>/repository/bower-group/"
       ]
     },
     "resolvers": [
       "bower-nexus3-resolver"
     ]
}
   ```
   
   

