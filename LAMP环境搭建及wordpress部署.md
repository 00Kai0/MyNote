#Ubuntu下手动搭建LAMP环境,并部署wordpress
Ubuntu服务器版可自动搭建
这里需要部署的环境为php7.0,apache2,mysql5.0以上版本
这里不测试安装后的效果，假定管理员掌握mysql相关技术
##更新Ubuntu软件包
```
sudo apt-get update && sudo apt-get upgrade
```
##下载mysql
基础的mysql数据库安装
```
sudo apt-get install mysql-client mysql-server
```
##下载apache2
安装apache2,以及服务器的php7模块
```
sudo apt-get install apache2 libapache2-mod-php7.0
```
##下载php
安装php7.0，以及php的mysql支持模块
```
sudo apt-get install php7.0 php7.0-mysql
```
##wordpress部署
官网下载wordpress
我这里默认下载在主文件夹的下载文件夹下
*进入下载目录
*解压wordpress压缩包
*复制文件到/var/www/html/目录下(apache2默认网络资源存放目录)
*修改wordpress文件夹权限为777,并将wordpress目录下的的wp-content子目录也修改为777
```
cd 下载
tar xf wordpress-4.9.4-zh_CN.tar.gz
sudo cp wordpress /var/www/html/
cd /var/www/html/
sudo chmod 777 wordpress
cd wordpress/
sudo chmod 777 wp-content/
```
##部署wordpress用的myql数据库及用户
###内容根据实际需要修改
数据库名：wordpress1
数据库用户名：web
数据库密码：123
主机：localhost 
```sql
create database wordpress1;
create user 'web'@'localhost' identified by '123';
grant all privileges on wordpress1.* to web@localhost;
```
##跟随wordpress提示完成接下来的表格
标题：
用户名：kai
密码:1o11234
