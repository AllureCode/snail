---
title: Ubuntu20.04安装mysql、redis、jdk
tags:
 - Linux
categories:
 - Linux
---

# Ubuntu 20.04 LTS 安装mysql、redis、jdk
## 使用apt命令

> Debian Linux发行版中的APT软件包管理工具
>
> **apt-get命令** 是Debian Linux发行版中的APT软件包管理工具。所有基于Debian的发行都使用这个包管理系统。deb包可以把一个应用的文件包在一起，大体就如同Windows上的安装文件。

## 安装MySQL服务端

```sh
sudo apt-get install mysql-server

选择y即可
```

### 安装mysql客户端

```sh
 sudo apt install mysql-client
```

### 安装mysql其他依赖

```sh
sudo apt install libmysqlclient-dev
```

### 查看是否安装成功

```sh
sudo netstat -tap | grep mysql

# mysql版本查看
mysql -V 
```

### 登陆mysql

>查询mysql安装目录  使用which mysql
>
>mysql默认配置文件地址：/etc/mysql
>
>找到debian.cnf文件 里面存储了mysql登陆用户及密码

```sh
mysql -u xxx(debian.cnf文件中的用户名) -p  

//进入mysql之后切换mysql库
use mysql;
// 修改密码
ALTER USER 'root'@'localhost' IDENTIFIED WITH caching_sha2_password BY '你自己的密码';
//刷新权限
flush privileges;
//退出
quit;
//重新启动mysql
service mysql restart
```
### 远程无法连接mysql解决方案

**1.**`进入到mysql配置文件mysqld.conf中 路径： /etc/mysql/mysql.conf.d`

**2.**`找到bind-address = 127.0.0.1 改为bind-address = 0.0.0.0`

**3.**`重启mysql  sudo systemctl restart mysql.service`

`注意：`如果上述步骤配置后仍无法访问则进行下面步骤

**1.**关闭系统防火墙：

```bash
systemctl start firewalld # 开启防火墙
systemctl stop firewalld  # 关闭防火墙
systemctl status firewalld  #检查防火墙状态
```

**2.**防火墙关闭后如果还是无法访问，则需要在配置腾讯云服务器的安全组开放3306端口，找到你自己的服务器，进入之后找到防火墙进行添加规则，选择MYSQL即可。
## 安装JDK

```
sudo apt install openjdk-8-jdk
```

### 查看jdk版本

```
java -version
```

## 安装Redis

```sh
sudo apt install redis-server
```

### 查看redis状态

```sh
sudo systemctl status redis-server
```

### 配置 Redis 远程访问

```sh
//打开redis配置文件 使用apt安装 配置文件默认在 /etc下面
sudo  /etc/redis.conf
// 找到# bind 0.0.0.0 ::1
打开 bind 0.0.0.0 ::1 
// 重启生效
sudo systemctl restart redis-server

//ubuntu 20.04 启动redis 使用sudo systemctl start redis-server
//ubuntu 20.04 关闭redis 使用sudo systemctl stop  redis-server
//ubuntu 20.04 启动redis cli 使用 redis-cli
```