---
title: linux系统下开发环境安装与配置
date: 2019-03-07 11:18:30
categories:
tags:
	- linux
	- jdk
	- tomcat
	- maven
	- vsftpd
	- nginx
	- mysql
	- git
---


> **安装系统环境**

CentOS 6.8 64位


> **jdk版本**

7u80 64位


> **Tomcat版本**

Tomcat7



> **maven版本**

Apache Maven 3.6.0 


> **vsftpd版本**

vsftpd-2.2.2-24.el6.x86_64



> **Nginx版本**

nginx-1.14.2


> **mysql版本**

mysql-server-5.1.73-8.el6_8.x86_64


> **git版本**

git version 2.18.0


> [**阿里源配置**](https://opsx.alibaba.com/mirror?lang=zh-CN)

```
#备份
[root@192 yum.repos.d]# mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup
#下载新的CentOS-Base.repo 到/etc/yum.repos.d/
[root@192 yum.repos.d]# wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-6.repo
#生成缓存
[root@192 yum.repos.d]# yum makecache

```


> ## 设置开机时关闭防火墙 ##

```
[root@192 vsftpd]# chkconfig iptables off

```


> ## jdk安装 ##

[下载](https://download.oracle.com/otn/java/jdk/7u80-b15/jdk-7u80-linux-x64.rpm?AuthParam=1551932562_fbefc7bf2ff59b8dd7dbb54130a3442a)

清理系统默认自带jdk

```
[root@192 yum.repos.d]# rpm -qa | grep jdk
java-1.6.0-openjdk-1.6.0.38-1.13.10.4.el6.x86_64
java-1.7.0-openjdk-1.7.0.99-2.6.5.1.el6.x86_64
[root@192 yum.repos.d]# yum remove java-1.6.0-openjdk-1.6.0.38-1.13.10.4.el6.x86_64
[root@192 yum.repos.d]# yum remove java-1.7.0-openjdk-1.7.0.99-2.6.5.1.el6.x86_64

```

授权

```
[root@192 soft]# chmod 777 jdk-7u80-linux-x64.rpm 

```

安装

```
[root@192 soft]# rpm -ivh jdk-7u80-linux-x64.rpm 

```

默认安装路径`/user/java`

jdk配置环境变量,编辑`/etc/profile`文件，末尾追加

```
#jdk
export JAVA_HOME=/usr/java/jdk1.7.0_80
export PATH=$PATH:$JAVA_HOME/bin
export CLASSPATH=.:$JAVA_HOME/jre/lib/rt.jar:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar

```

使得`/etc/profile`文件立即生效

```
[root@192 apache-maven-3.6.0]# source /etc/profile

```

> ## Tomcat安装 ##

[下载](https://mirrors.tuna.tsinghua.edu.cn/apache/tomcat/tomcat-7/v7.0.93/bin/apache-tomcat-7.0.93.tar.gz)


解压

```

[root@192 soft]# tar -zxvf apache-tomcat-7.0.93.tar.gz -C /opt/module/

```

Tomcat配置环境变量,编辑`/etc/profile`文件，末尾追加

```
#tomcat
export CATALINA_HOME=/opt/module/apache-tomcat-7.0.93
export PATH=$PATH:$CATALINA_HOME/bin

```

使得`/etc/profile`文件立即生效

```
[root@192 apache-maven-3.6.0]# source /etc/profile

```

配置UTF-8字符集，进入Tomcat安装的`conf`文件夹，编辑`server.xml`文件，找到配置8080默认端口的位置，在xml节点末尾增加`URIEncoding="UTF-8"`

```
<Connector port="8080" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8443" URIEncoding="UTF-8" />

```

启动Tomcat，进入`/opt/module/apache-tomcat-7.0.93/bin`目录，执行`./startup.sh`

访问[http://192.168.1.104:8080/](http://192.168.1.104:8080/)




> ## Maven安装 ##

[下载](https://mirrors.tuna.tsinghua.edu.cn/apache/maven/maven-3/3.6.0/binaries/apache-maven-3.6.0-bin.tar.gz)

解压

```
[root@192 soft]# tar -zxvf apache-maven-3.6.0-bin.tar.gz -C /opt/module/

```


Maven配置环境变量,编辑`/etc/profile`文件，末尾追加

```
#maven
export MAVEN_HOME=/opt/module/apache-maven-3.6.0
export PATH=$PATH:$MAVEN_HOME/bin

```

使得`/etc/profile`文件立即生效

```
[root@192 apache-maven-3.6.0]# source /etc/profile

```

验证

```
[root@192 apache-maven-3.6.0]# mvn -version

```

> ## vsftpd安装 ##


安装

```
[root@192 apache-maven-3.6.0]# yum -y install vsftpd

```

查看是否已安装

```
[root@192 apache-maven-3.6.0]# rpm -qa | grep vsftpd
vsftpd-2.2.2-24.el6.x86_64

```

默认配置文件`/etc/vsftpd/vsftpd.conf`



创建虚拟用户

根目录下创建ftp文件夹

```
[root@192 vsftpd]# cd /
[root@192 /]# mkdir ftpfile

```

添加匿名用户

```
[root@192 /]# useradd ftpuser -d /ftpfile/ -s /sbin/nologin 

```

修改ftpfile权限

```
[root@192 /]# chown -R ftpuser.ftpuser /ftpfile/

```

重设ftpuser密码

```
[root@192 /]# passwd ftpuser

```

将刚刚新增的虚拟用户添加到此配置文件中

```
[root@192 ~]# cd /etc/vsftpd/
[root@192 vsftpd]# vim chroo_list 
[root@192 bin]# cat /etc/vsftpd/chroo_list 
ftpuser

```

编辑配置文件`/etc/vsftpd/vsftpd.conf`，末尾追加

```
local_root=/ftpfile
anon_root=/ftpfile
use_localtime=YES
anonymous_enable=no

```

登录验证的时候出现500提示,编辑`/etc/selinux/config`文件，设置`SELINUX=disabled`

```
[root@192 vsftpd]# vim /etc/selinux/config

```
如果还报500，执行

```
[root@192 vsftpd]# setsebool -P ftp_home_dir 1

```

设置开启启动vsftpd服务

```
[root@192 vsftpd]# chkconfig vsftpd on

```

访问[ftp://192.168.1.104/](ftp://192.168.1.104/)


> ## 安装Nginx ##

[下载](http://nginx.org/download/nginx-1.14.2.tar.gz)

安装依赖gcc、pcre、zlib、openssl

```
[root@192 vsftpd]# yum -y install gcc zlib zlib-devel pcre-devel openssl openssl-devel

```

解压

```
[root@192 soft]# tar -zxvf nginx-1.14.2.tar.gz -C /opt/module/

```

编译安装

```
[root@192 nginx-1.14.2]# pwd
/opt/module/nginx-1.14.2
[root@192 nginx-1.14.2]# ./configure
[root@192 nginx-1.14.2]# make
[root@192 nginx-1.14.2]# make install

```

查看Nginx安装目录、版本

```
[root@192 nginx-1.14.2]# whereis nginx
nginx: /usr/local/nginx
[root@192 nginx-1.14.2]# /usr/local/nginx/sbin/nginx -v
nginx version: nginx/1.14.2

```

测试配置文件`nginx.conf`的正确性

```
[root@192 nginx-1.14.2]# /usr/local/nginx/sbin/nginx  -t

```

启动Nginx

```
[root@192 nginx-1.14.2]# /usr/local/nginx/sbin/nginx
[root@192 nginx-1.14.2]# ps -ef | grep nginx
root       5606      1  0 01:02 ?        00:00:00 nginx: master process /usr/local/nginx/sbin/nginx
nobody     5607   5606  0 01:02 ?        00:00:00 nginx: worker process      
root       5626   2868  0 01:09 pts/0    00:00:00 grep nginx


```

停止Nginx

```
[root@192 nginx-1.14.2]# /usr/local/nginx/sbin/nginx -s stop

```

访问[http://192.168.1.104/](http://192.168.1.104/)



> ### Nginx配置虚拟域名 ###

编辑`/usr/local/nginx/conf/nginx.conf`文件，追加

```
 ###########################vhost##############################################
    include vhost/*.conf;

```

在`/usr/local/nginx/conf`目录下，新建`vhost`文件夹

在`/usr/local/nginx/conf/vhost`目录下，新建域名转发`www.test.com.conf`配置文件

```
[root@192 vhost]# cat /usr/local/nginx/conf/vhost/www.test.com.conf 
#Start www.test.com
server {
    listen 80;
    server_name  www.test.com;
 
    access_log  /usr/local/nginx/logs/access.log combined;
    index  index.html index.htm index.php;

    if ( $query_string ~* ".*[\;'\<\>].*" ){ 
	return 404;
     }

    # send request back to apach
    location / {
       proxy_pass http://127.0.0.1:8080/; 
   }
}

```

访问[www.test.com](www.test.com)



> ## mysql安装 ##

安装

```
[root@192 ~]# yum -y install mysql-server

```

配置字符集,编辑`/etc/my.cnf`文件，追加

```
#utf8
default-character-set=utf8

```

设置`mysql`服务随系统自动启动

```
[root@192 ~]# chkconfig mysqld on
[root@192 ~]# chkconfig --list mysqld
mysqld         	0:关闭	1:关闭	2:启用	3:启用	4:启用	5:启用	6:关闭

```
启动`mysql`服务

```
[root@192 ~]# service mysqld start
正在启动 mysqld：                                          [确定]

```

登录，使用非密码登录

```
[root@192 mysql]# mysql -uroot 
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 5
Server version: 5.1.73 Source distribution

Copyright (c) 2000, 2013, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> 

```

查看`mysql`的用户

```
mysql> select user,host,password from  mysql.user;
+------+---------------+----------+
| user | host          | password |
+------+---------------+----------+
| root | localhost     |          |
| root | 192.168.1.104 |          |
| root | 127.0.0.1     |          |
|      | localhost     |          |
|      | 192.168.1.104 |          |
+------+---------------+----------+
5 rows in set (0.00 sec)

```
删除匿名用户

```
mysql> delete from mysql.user where user='';
Query OK, 2 rows affected (0.00 sec)

```
刷新，立即生效

```
mysql> flush privileges;
Query OK, 0 rows affected (0.00 sec)

```
修改`root`密码

```
mysql> set password for root@localhost=password("000000");
Query OK, 0 rows affected (0.00 sec)

```

插入mysql新用户

```
mysql> insert into mysql.user(Host,User,Password) values("localhost","admin",password("000000"));
Query OK, 1 row affected, 3 warnings (0.01 sec)

```
查看用户权限

```
mysql> select * from mysql.user \G
*************************** 1. row ***************************
                 Host: localhost
                 User: root
             Password: *032197AE5731D4664921A6CCAC7CFCE6A0698693
          Select_priv: Y
          Insert_priv: Y
          Update_priv: Y
          Delete_priv: Y
          Create_priv: Y
            Drop_priv: Y
          Reload_priv: Y
        Shutdown_priv: Y
         Process_priv: Y
            File_priv: Y
           Grant_priv: Y
      References_priv: Y
           Index_priv: Y
           Alter_priv: Y
         Show_db_priv: Y
           Super_priv: Y
Create_tmp_table_priv: Y
     Lock_tables_priv: Y
         Execute_priv: Y
      Repl_slave_priv: Y
     Repl_client_priv: Y
     Create_view_priv: Y
       Show_view_priv: Y
  Create_routine_priv: Y
   Alter_routine_priv: Y
     Create_user_priv: Y
           Event_priv: Y
         Trigger_priv: Y
             ssl_type: 
           ssl_cipher: 
          x509_issuer: 
         x509_subject: 
        max_questions: 0
          max_updates: 0
      max_connections: 0
 max_user_connections: 0
*************************** 2. row ***************************
                 Host: 192.168.1.104
                 User: root
             Password: 
          Select_priv: Y
          Insert_priv: Y
          Update_priv: Y
          Delete_priv: Y
          Create_priv: Y
            Drop_priv: Y
          Reload_priv: Y
        Shutdown_priv: Y
         Process_priv: Y
            File_priv: Y
           Grant_priv: Y
      References_priv: Y
           Index_priv: Y
           Alter_priv: Y
         Show_db_priv: Y
           Super_priv: Y
Create_tmp_table_priv: Y
     Lock_tables_priv: Y
         Execute_priv: Y
      Repl_slave_priv: Y
     Repl_client_priv: Y
     Create_view_priv: Y
       Show_view_priv: Y
  Create_routine_priv: Y
   Alter_routine_priv: Y
     Create_user_priv: Y
           Event_priv: Y
         Trigger_priv: Y
             ssl_type: 
           ssl_cipher: 
          x509_issuer: 
         x509_subject: 
        max_questions: 0
          max_updates: 0
      max_connections: 0
 max_user_connections: 0
*************************** 3. row ***************************
                 Host: 127.0.0.1
                 User: root
             Password: 
          Select_priv: Y
          Insert_priv: Y
          Update_priv: Y
          Delete_priv: Y
          Create_priv: Y
            Drop_priv: Y
          Reload_priv: Y
        Shutdown_priv: Y
         Process_priv: Y
            File_priv: Y
           Grant_priv: Y
      References_priv: Y
           Index_priv: Y
           Alter_priv: Y
         Show_db_priv: Y
           Super_priv: Y
Create_tmp_table_priv: Y
     Lock_tables_priv: Y
         Execute_priv: Y
      Repl_slave_priv: Y
     Repl_client_priv: Y
     Create_view_priv: Y
       Show_view_priv: Y
  Create_routine_priv: Y
   Alter_routine_priv: Y
     Create_user_priv: Y
           Event_priv: Y
         Trigger_priv: Y
             ssl_type: 
           ssl_cipher: 
          x509_issuer: 
         x509_subject: 
        max_questions: 0
          max_updates: 0
      max_connections: 0
 max_user_connections: 0
*************************** 4. row ***************************
                 Host: localhost
                 User: admin
             Password: *032197AE5731D4664921A6CCAC7CFCE6A0698693
          Select_priv: N
          Insert_priv: N
          Update_priv: N
          Delete_priv: N
          Create_priv: N
            Drop_priv: N
          Reload_priv: N
        Shutdown_priv: N
         Process_priv: N
            File_priv: N
           Grant_priv: N
      References_priv: N
           Index_priv: N
           Alter_priv: N
         Show_db_priv: N
           Super_priv: N
Create_tmp_table_priv: N
     Lock_tables_priv: N
         Execute_priv: N
      Repl_slave_priv: N
     Repl_client_priv: N
     Create_view_priv: N
       Show_view_priv: N
  Create_routine_priv: N
   Alter_routine_priv: N
     Create_user_priv: N
           Event_priv: N
         Trigger_priv: N
             ssl_type: 
           ssl_cipher: 
          x509_issuer: 
         x509_subject: 
        max_questions: 0
          max_updates: 0
      max_connections: 0
 max_user_connections: 0
4 rows in set (0.00 sec)

```
查库，新建`database`数据库

```
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| test               |
+--------------------+
3 rows in set (0.01 sec)
mysql> create database mytest default character set utf8 collate utf8_general_ci;
Query OK, 1 row affected (0.00 sec)
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| mytest             |
| test               |
+--------------------+
4 rows in set (0.00 sec)


```
本地用户赋予所有权限、给账号开通外网所有权限

```
mysql> grant all privileges on mytest.* to admin@'%' identified by '000000' with grant option;
Query OK, 0 rows affected (0.00 sec)
mysql> grant select,delete,create on mytest.* to admin@'192.168.1.104' identified by '000000' with grant option;
Query OK, 0 rows affected (0.00 sec)

```

导入`sql`文件

```
mysql> source /opt/module/mmall.sql

```

> ## git安装 ##

[下载](https://codeload.github.com/git/git/tar.gz/v2.18.0)

解压

```
[root@192 ~]# tar -zxvf git-2.18.0.tar.gz -C /opt/module/

```

安装依赖

```
[root@192 ~]# yum -y install curl-devel expat-devel gettext-devel openssl-devel zlib-devel gcc perl-ExtUtils-MakeMaker cpio

```

编译安装

```
[root@192 git-2.18.0]# make prefix=/usr/local/ install

```
验证

```
[root@192 ~]# git --version
git version 2.18.0

```

生成公私密钥对

```
[root@192 ~]# ssh-keygen -t rsa -C "782125244@qq.com"
Generating public/private rsa key pair.
Enter file in which to save the key (/root/.ssh/id_rsa): 
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /root/.ssh/id_rsa.
Your public key has been saved in /root/.ssh/id_rsa.pub.
The key fingerprint is:
85:1e:ef:5a:33:ab:e7:79:a4:46:f6:8e:8f:7f:af:cd 782125244@qq.com
The key's randomart image is:
+--[ RSA 2048]----+
|                 |
|         .       |
|        o .      |
|       . +       |
|        S .      |
|         .o .    |
|         o=+     |
|         o+Bo .o |
|        o=*=+..oE|
+-----------------+

```

复制公钥粘贴到码云或者GitHub上SSH公钥上

```
[root@192 .ssh]# cat id_rsa.pub 
ssh-rsa AAAAB3NzaC1yc2EAAAABIwAAAQEA1sZvTnjQyVn7r2C7G9Q/WyVTTmJYqWhQdVfh3tBCUX8kJHaAcThwR4sl8ROz6xXl/22wbzlsKtfsiyr5zrV0Ifnq7KuCeNVNUYjOQCTn+ODaRzdj7DYC4Mz9BvxZAr0MDfSbgpLp6ZLAvZlkP3DioOPda7VnfJSAHGEYztOVPITj31pVnP1nXkPZRQlsTwImXEGJpuU+zOaurMShpaukrY/ONxWHR6xlG5M1FgOLdBvEnlbhOFoME1HHziI4/08Xw/NrHIUhvjZZgkAzWUo5NvYZLEwrUjOPtlVxHbYwBEtgBWRgWLFMfTlxQnEJVKzbwUDPLvZWac9WSpGLx5xiww== 782125244@qq.com

```

配置用户名、邮箱，提交时会引用

```
[root@192 mytest]# git config --global user.name "shenlibng"
[root@192 mytest]# git config --global user.email "782125244@qq.com"
[root@192 mytest]# git config --global core.autocrlf false
[root@192 mytest]# git config --global core.quotepath off
[root@192 mytest]# git config --global gui.encoding utf-8

```






