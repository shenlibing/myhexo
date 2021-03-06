---
title: Tomcat集群与Redis分布式
date: 2019-03-25 09:58:28
categories: 日常记录
tags:
   - Tomcat
   - Redis
   - Maven
---


> ## Lombok原理及使用 ##

> ### 下载 ###

[官网](https://projectlombok.org/)

[插件下载](https://plugins.jetbrains.com/files/6317/53293/lombok-plugin-0.23-2018.3.zip?updateId=53293&pluginId=6317&family=intellij)


> ### IDEA安装Lombok插件 ###

Add the **Lombok IntelliJ plugin** to add lombok support for IntelliJ:
Go to **File** > **Settings** > **Plugins**
Click on **Browse repositories**...
Search for **Lombok Plugin**
Click on **Install plugin**
Restart IntelliJ IDEA

> ### Maven引入Lombok ###

```xml
<dependencies>
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
        <version>1.18.4</version>
        <scope>provided</scope>
    </dependency>
</dependencies>

```


> ### Java Decompiler ###

[官网](http://java-decompiler.github.io/)

[下载](https://github-production-release-asset-2e65be.s3.amazonaws.com/32844456/ce9b5e00-42a0-11e9-9001-1d805c82cb1e?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAIWNJYAX4CSVEH53A%2F20190325%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20190325T025102Z&X-Amz-Expires=300&X-Amz-Signature=6b28f4eb01b049712bf9b7fc4d2ebf7dc0c55f83ceb82e1159994c7c9f8c37a3&X-Amz-SignedHeaders=host&actor_id=26431704&response-content-disposition=attachment%3B%20filename%3Djd-gui-windows-1.4.1.zip&response-content-type=application%2Foctet-stream)

> ### Lombok验证 ###

通过Java Decompiler验证class文件

> ### Lombok原理 ###

![](Lombok原理.png)


> ## Maven环境隔离 ##

> ### 项目环境 ###

本地开发环境(Local)

开发环境(Dev)

测试环境(Beta)

线上环境(Prod)


> ### 目录初始化 ###

![](Maven环境隔离目录初始化.png)

> ### 配置pom.xml ###

`build`节点中的`resources`中增加`resource`节点

```xml
<resources>
  <resource>
    <directory>src/main/resources.${deploy.type}</directory>
    <excludes>
      <exclude>*.jsp</exclude>
    </excludes>
  </resource>
  <resource>
    <directory>src/main/resources</directory>
  </resource>
</resources>

```
`project`节点中增加`profiles`节点

```xml
 <profiles>
    <profile>
      <id>dev</id>
      <activation>
        <activeByDefault>true</activeByDefault>
      </activation>
      <properties>
        <deploy.type>dev</deploy.type>
      </properties>
    </profile>
    <profile>
      <id>beta</id>
      <properties>
        <deploy.type>beta</deploy.type>
      </properties>
    </profile>
    <profile>
      <id>prod</id>
      <properties>
        <deploy.type>prod</deploy.type>
      </properties>
    </profile>
 </profiles>

```

> ### IDEA中设置默认环境 ###

在IDEA右侧`Maven Projects`,选中本地开发环境对应的的环境，点击右下角出现`import changes`进行更新

![](Maven环境隔离IDEA中设置默认环境.png)

这里设置的默认环境作用于IDEA中配置的Tomcat启动时发布部署的`war`包

![](Maven环境隔离IDEA中设置默认环境2.png)

> ### 编译打包命令 ###


> #### 开发环境 ####

手动打`war`包

```
mvn clean package -Dmaven.test.skip=true -Pdev

```
> #### 线上环境 ####

```
mvn clean package -Dmaven.test.skip=true -Pprod

```

> ### 验证 ###

![](Maven环境隔离验证2.png)

![](Maven环境隔离验证.png)


> ## Tomcat集群搭建 ##

> ### 原理 ###

通过Nginx负载均衡进行请求转发

> ### 单机部署多应用 ###

单机部署多个Tomcat，第一个Tomcat不变,修改第二个Tomcat

> #### 配置环境变量 ####

修改/etc/profile增加Tomcat环境变量

```
#tomcat
export CATALINA_BASE=/opt/module/tomcat1
export CATALINA_HOME=/opt/module/tomcat1
export TOMCAT_HOME=/opt/module/tomcat1

export CATALINA_BASE_2=/opt/module/tomcat2
export CATALINA_HOME_2=/opt/module/tomcat2
export TOMCAT_HOME_2=/opt/module/tomcat2
#export PATH=$PATH:$CATALINA_HOME/bin

```
`source /etc/profile `使配置文件立即生效

> #### 编辑`catalina.sh` ####


进入`/opt/module/tomcat2/bin`目录,编辑`catalina.sh`

```
# OS specific support.  $var _must_ be set to either true or false.
export CATALINA_BASE=$CATALINA_BASE_2
export CATALINA_HOME=$CATALINA_HOME_2

```

> #### 编辑`server.xml` ####

进入`/opt/module/tomcat2/conf`目录，编辑`server.xml`，修改3个端口，为了方便，每个端口号加上1000

![](修改server.xml.png)
![](修改server.xml2.png)
![](修改server.xml3.png)

> #### 启动 ####

启动`Tomcat2`

```
[root@192 bin]# pwd
/opt/module/tomcat2/bin
[root@192 bin]# ./startup.sh 
Using CATALINA_BASE:   /opt/module/tomcat2
Using CATALINA_HOME:   /opt/module/tomcat2
Using CATALINA_TMPDIR: /opt/module/tomcat2/temp
Using JRE_HOME:        /usr/java/jdk1.7.0_80
Using CLASSPATH:       /opt/module/tomcat2/bin/bootstrap.jar:/opt/module/tomcat2/bin/tomcat-juli.jar
Tomcat started.

```

启动`Tomcat1`

```
[root@192 bin]# pwd
/opt/module/tomcat1/bin
[root@192 bin]# ./startup.sh 
Using CATALINA_BASE:   /opt/module/tomcat1
Using CATALINA_HOME:   /opt/module/tomcat1
Using CATALINA_TMPDIR: /opt/module/tomcat1/temp
Using JRE_HOME:        /usr/java/jdk1.7.0_80
Using CLASSPATH:       /opt/module/tomcat1/bin/bootstrap.jar:/opt/module/tomcat1/bin/tomcat-juli.jar
Tomcat started.

```


> ### 多机部署多应用 ###

多个服务器并且每个服务器只安装一个`Tomcat`，要保证它们之间的网络是互通的，方可集群，`Nginx`在任意一台服务器上即可，也可单独把`Nginx`服务独立出来一台。

> ## Nginx负载均衡实现 ##

> ### 负载均衡常用策略 ###

> #### 轮询 ####

默认
优点：实现简单
缺点：不考虑每台服务器处理能力

> #### 权重 ####

优点：
考虑了每台服务器处理能力的不同，`weight`默认是1

> #### ip hash ####

优点：
能实现同一个用户访问同一个服务器,可以不改变现有技术架构，直接实现横向拓展
缺点：
导致服务器请求(负载)不平均(完全依赖`ip hash`的结果)
在`ip`变化的环境下无法服务

> #### url hash(第三方) ####

优点：
能实现同一个服务器访问同一个服务器
缺点：
根据`url hash`分配请求会不平均，请求频繁的url会请求到同一个服务器上的

> #### fair(第三方) ####

缺点：
按后端服务器的响应时间来分配请求，响应时间短的优先分配

> ### 负载均衡策略权重配置 ###

编辑`/usr/local/nginx/conf/nginx.conf`文件，追加

```
 ###########################vhost##############################################
    include vhost/*.conf;

```

在`/usr/local/nginx/conf`目录下，新建`vhost`文件夹

在`/usr/local/nginx/conf/vhost`目录下，编辑`www.mytest.com.conf`配置文件

```
[root@192 sbin]# cat ../conf/vhost/www.mytest.com.conf

upstream 127.0.0.1{
	server 127.0.0.1:8080 weight=1;
	server 127.0.0.1:9080 weight=2;

}
#Start www.mytest.com
server {
    listen 80;
    server_name  www.mytest.com;
    access_log  /usr/local/nginx/logs/access.log combined;
    index  index.html index.htm index.php;


    # send request back to apach
    location / {
         proxy_pass http://127.0.0.1; 
   }
}


```

> ### 验证 ###

替换`/opt/module/tomcat2/webapps/ROOT`目录下`tomcat.png`

![](验证负载均衡策略权重.png)

访问`www.mytest.com`,请求分流一下打到`Tomcat1`，一下打到`Tomcat2`，`Nginx`负载均衡策略权重`Tomcat2`配置**`weight=2`**，Tomcat1配置**`weight=1`**，所以访问到`Tomcat2`的概率是`Tomcat1`的2倍

![](验证负载均衡策略权重2.png)

![](验证负载均衡策略权重3.png)

> ### 坑 ###

> #### `Session`登录信息存储及读取的问题 ####


> ##### 轮询 #####

登录的时候登录了A服务器，session信息存储到A服务器上了
Nginx负载均衡策略使用**轮询**或者**最小连接**会导致，第一次访问A服务器，第二次可能访问到B服务器，这个时候存储在A服务器上的session信息在B服务器上读取不到。

> ##### ip hash #####

Nginx负载均衡策略使用**`ip hash`**，那么登录信息还可以从A服务器上访问，但是这个有可能造成某些服务器压力过大，某些服务器又没有什么压力，这个时候压力过大的机器(包括网卡带宽)有可能成为瓶颈，并且请求不够分散。

> #### 服务器定时任务并发的问题 ####

假设有定时关单的`Job`，单个`Tomcat`没有任何问题，但是在集群环境下，`Spring Schedule`定时执行的时候，会都一起执行，会导致数据错乱和资源浪费


> ## Redis ##

> ### 简介 ###

高性能的`key-value`数据库

内存数据库，支持数据持久化

> ### 安装 ###

[linux下载redis-2.8.0.tar.gz](http://download.redis.io/releases/)
[windows](https://github.com/MicrosoftArchive/redis/releases)

解压

```
[root@192 soft]# tar -zxvf redis-2.8.0.tar.gz -C /opt/module/

```

> ### 服务端 ###


> #### 启动 ####

> ##### 直接启动 #####

默认是6379端口

```
[root@192 src]# pwd
/opt/module/redis-2.8.0/src
[root@192 src]# ./redis-server 
[6448] 26 Mar 18:30:37.044 # Warning: no config file specified, using the default config. In order to specify a config file use ./redis-server /path/to/redis.conf
[6448] 26 Mar 18:30:37.045 * Max number of open files set to 10032
                _._                                                  
           _.-``__ ''-._                                             
      _.-``    `.  `_.  ''-._           Redis 2.8.0 (00000000/0) 64 bit
  .-`` .-```.  ```\/    _.,_ ''-._                                   
 (    '      ,       .-`  | `,    )     Running in stand alone mode
 |`-._`-...-` __...-.``-._|'` _.-'|     Port: 6379
 |    `-._   `._    /     _.-'    |     PID: 6448
  `-._    `-._  `-./  _.-'    _.-'                                   
 |`-._`-._    `-.__.-'    _.-'_.-'|                                  
 |    `-._`-._        _.-'_.-'    |           http://redis.io        
  `-._    `-._`-.__.-'_.-'    _.-'                                   
 |`-._`-._    `-.__.-'    _.-'_.-'|                                  
 |    `-._`-._        _.-'_.-'    |                                  
  `-._    `-._`-.__.-'_.-'    _.-'                                   
      `-._    `-.__.-'    _.-'                                       
          `-._        _.-'                                           
              `-.__.-'                                               

[6448] 26 Mar 18:30:37.046 # Server started, Redis version 2.8.0


```

> ##### 指定端口启动 #####

```
[root@192 src]# ./redis-server --port 6380
[6457] 26 Mar 18:32:55.317 * Max number of open files set to 10032
                _._                                                  
           _.-``__ ''-._                                             
      _.-``    `.  `_.  ''-._           Redis 2.8.0 (00000000/0) 64 bit
  .-`` .-```.  ```\/    _.,_ ''-._                                   
 (    '      ,       .-`  | `,    )     Running in stand alone mode
 |`-._`-...-` __...-.``-._|'` _.-'|     Port: 6380
 |    `-._   `._    /     _.-'    |     PID: 6457
  `-._    `-._  `-./  _.-'    _.-'                                   
 |`-._`-._    `-.__.-'    _.-'_.-'|                                  
 |    `-._`-._        _.-'_.-'    |           http://redis.io        
  `-._    `-._`-.__.-'_.-'    _.-'                                   
 |`-._`-._    `-.__.-'    _.-'_.-'|                                  
 |    `-._`-._        _.-'_.-'    |                                  
  `-._    `-._`-.__.-'_.-'    _.-'                                   
      `-._    `-.__.-'    _.-'                                       
          `-._        _.-'                                           
              `-.__.-'                                               

[6457] 26 Mar 18:32:55.318 # Server started, Redis version 2.8.0

```

> ##### 指定配置文件配置启动 #####

配置文件修改端口,登录密码

```
[root@192 redis-2.8.0]# pwd
/opt/module/redis-2.8.0
[root@192 redis-2.8.0]# vim redis.conf 

```
![](Redis指定配置文件启动.png)

![](Redis指定配置文件启动2.png)

```
[root@192 src]# ./redis-server ../redis.conf 
[6468] 26 Mar 18:37:12.957 * Max number of open files set to 10032
                _._                                                  
           _.-``__ ''-._                                             
      _.-``    `.  `_.  ''-._           Redis 2.8.0 (00000000/0) 64 bit
  .-`` .-```.  ```\/    _.,_ ''-._                                   
 (    '      ,       .-`  | `,    )     Running in stand alone mode
 |`-._`-...-` __...-.``-._|'` _.-'|     Port: 6380
 |    `-._   `._    /     _.-'    |     PID: 6468
  `-._    `-._  `-./  _.-'    _.-'                                   
 |`-._`-._    `-.__.-'    _.-'_.-'|                                  
 |    `-._`-._        _.-'_.-'    |           http://redis.io        
  `-._    `-._`-.__.-'_.-'    _.-'                                   
 |`-._`-._    `-.__.-'    _.-'_.-'|                                  
 |    `-._`-._        _.-'_.-'    |                                  
  `-._    `-._`-.__.-'_.-'    _.-'                                   
      `-._    `-.__.-'    _.-'                                       
          `-._        _.-'                                           
              `-.__.-'                                               

[6468] 26 Mar 18:37:12.959 # Server started, Redis version 2.8.0

```

> #### 关闭 ####

> ##### 直接关闭 #####

```
[root@192 src]# ./redis-cli shutdown

```

> ##### 指定端口关闭 #####

启动如果指定了端口，关闭必须指定端口

```
[root@192 src]# ./redis-cli -p 6379 shutdown 

```

> ##### 指定端口、ip地址、密码关闭 #####

启动指定配置文件启动，配置文件修改了端口、密码，关闭的时候必须指定端口、密码

```
[root@192 src]# ./redis-cli -p 6380 shutdown
(error) NOAUTH Authentication required.
[root@192 src]# ./redis-cli -p 6380 -h 127.0.0.1 -a 000000 shutdown
[root@192 src]# ps -ef | grep redis
root       6526   6337  0 18:51 pts/6    00:00:00 grep redis
[root@192 src]# 

```

> ### 客户端 ###

> #### 连接服务端 ####

> ##### 直接连接 #####

服务端默认启动，客户端可以直接默认连接

```
[root@192 src]# ./redis-cli 
127.0.0.1:6379> keys *
1) "b"
2) "hash"
3) "a2"
4) "a1"
5) "1"
6) "hash2"
7) "word"
8) "a"
9) "a3"
127.0.0.1:6379> quit

```

> ##### 指定端口连接 #####

服务端指定端口启动，客户端启连接必须指定端口

```
[root@192 src]# ./redis-cli -p 6379

```

> ##### 指定端口、ip连接 #####


```
[root@192 src]# ./redis-cli -p 6379 -h 127.0.0.1

```

> ##### 指定端口、ip、密码连接 #####

服务端指定配置文件启动，配置文件修改了端口、密码，客户端连接必须指定端口、密码
```
[root@192 src]# pwd
/opt/module/redis-2.8.0/src
[root@192 src]# ./redis-cli -p 6380
127.0.0.1:6380> keys *
(error) NOAUTH Authentication required.
127.0.0.1:6380> quit
[root@192 src]# ./redis-cli -p 6380 -h 127.0.0.1 -a 000000
127.0.0.1:6380> keys *
1) "1"
2) "a"
3) "a2"
4) "hash2"
5) "word"
6) "b"
7) "hash"
8) "a3"
9) "a1"
127.0.0.1:6380> 

```

> #### 关闭 ####

```
127.0.0.1:6379> quit

```

> ### 数据结构 ###

> #### 系统命令 ####

> ##### 查看键 #####

```
127.0.0.1:6379> keys *

```
> ##### 查看基本信息 #####

```
127.0.0.1:6379> info

```

> ##### 退出 #####


```
127.0.0.1:6379> exit

```
```
127.0.0.1:6379> quit

```

> ##### 切换库 #####


默认使用0库

```
127.0.0.1:6379> select 2

```

> ##### 清除当前库数据 #####


```
127.0.0.1:6379> flushdb

```

> ##### 清除所有库数据 #####


```
127.0.0.1:6379> flushall

```

> ##### 查看键的数量 #####


```
127.0.0.1:6379> dbsize

```
> ##### 查看键生命时间 #####

-1代表永久有效

```
127.0.0.1:6379[1]> keys  *
1) "a"
2) "b"
127.0.0.1:6379[1]> ttl a
(integer) -1

```
> ##### 查看类型 #####

```
127.0.0.1:6379[1]> type a
string

```

> ##### 日志监听 #####

```
127.0.0.1:6379> monitor
OK

```

> #### String字符串 ####


> ##### 设置 #####

> ###### 设置单个 ######

```
127.0.0.1:6379[1]> set c c
OK

```
> ###### 设置指定生命时间(秒) ######

```
127.0.0.1:6379[1]> setex d 10 d
OK
127.0.0.1:6379[1]> ttl d
(integer) 7
127.0.0.1:6379[1]> ttl d
(integer) 6

```

> ###### 设置指定生命时间(毫秒秒) ######

```
127.0.0.1:6379[1]> psetex e 10000 e
OK
127.0.0.1:6379[1]> ttl e
(integer) 8
127.0.0.1:6379[1]> ttl e
(integer) 6
127.0.0.1:6379[1]> ttl e
(integer) 5

```

> ###### 重置单个 ######


```
127.0.0.1:6379[1]> get a
"a"
127.0.0.1:6379[1]> getset a aaa
"a"
127.0.0.1:6379[1]> get a
"aaa"

```

> ###### 设置多个 ######

```
127.0.0.1:6379[1]> mset a1 a1_value a2 a2_value a3 a3_value
OK
127.0.0.1:6379[1]> keys *
1) "a"
2) "b"
3) "a1"
4) "a3"
5) "hello"
6) "a2"
7) "c"
127.0.0.1:6379[1]>

```

> ###### 设置前判断键是否存在`setnx` ######


```
127.0.0.1:6379[1]> keys *
1) "a"
2) "b"
3) "a1"
4) "a3"
5) "hello"
6) "a2"
7) "c"
127.0.0.1:6379[1]> get a
"aaa"
127.0.0.1:6379[1]> setnx a a
(integer) 0
127.0.0.1:6379[1]> get a
"aaa"

```

> ###### 设置多个，判断键是否存在，只要一个失败就失败 ######

```
127.0.0.1:6379[1]> keys *
(empty list or set)
127.0.0.1:6379[1]> msetnx a a b b c c
(integer) 1
127.0.0.1:6379[1]> keys *
1) "a"
2) "b"
3) "c"

```
> ###### 追加 ######

```

127.0.0.1:6379[1]> append a aaa
(integer) 4
127.0.0.1:6379[1]> get a
"aaaa"

```

> ###### 默认增长 ######

只能是数字增长，默认步长是1

```
127.0.0.1:6379[1]> set 1 1
OK
127.0.0.1:6379[1]> incr 1 
(integer) 2
127.0.0.1:6379[1]> incr 1 
(integer) 3
127.0.0.1:6379[1]> get 1
"3"

```
> ###### 指定步长增长 ######

```
127.0.0.1:6379[1]> get 1
"3"
127.0.0.1:6379[1]> incrby 1 100
(integer) 103
127.0.0.1:6379[1]> incrby 1 100
(integer) 203
127.0.0.1:6379[1]> get 1
"203"

```
> ###### 默认减值 ######

```
127.0.0.1:6379[1]> get 1
"203"
127.0.0.1:6379[1]> decr 1
(integer) 202
127.0.0.1:6379[1]> decr 1
(integer) 201
127.0.0.1:6379[1]> decr 1
(integer) 200
127.0.0.1:6379[1]> get 1
"200"

```
> ###### 指定步长减值 ######

```
127.0.0.1:6379[1]> get 1
"200"
127.0.0.1:6379[1]> decrby 1 20
(integer) 180
127.0.0.1:6379[1]> decrby 1 20
(integer) 160
127.0.0.1:6379[1]> decrby 1 20
(integer) 140
127.0.0.1:6379[1]> get 1
"140"

```

> ##### 获取 #####

> ###### 获取单个 ######


```
127.0.0.1:6379[1]> get a
"a"
127.0.0.1:6379[1]> get b
"b"

```
> ###### 截取 ######


```
127.0.0.1:6379[1]> set hello hello
OK
127.0.0.1:6379[1]> getrange hello 0 2
"hel"

```

> ###### 获取多个 ######

```
127.0.0.1:6379[1]> mget a1 a2 a3 
1) "a1_value"
2) "a2_value"
3) "a3_value"
127.0.0.1:6379[1]>

```
> ###### 长度 ######

```
127.0.0.1:6379[1]> get hello
"hello"
127.0.0.1:6379[1]> strlen hello
(integer) 5

```




> #### 哈希hash ####

> ##### 设置 #####

> ###### 设置单个 ######

```
127.0.0.1:6379[1]> hset hash username shenlibing
(integer) 1

```
> ###### 设置多个 ######

```
127.0.0.1:6379[1]> hmset hash address haikou phone 15501892660
OK
127.0.0.1:6379[1]> hgetall hash
1) "username"
2) "shenlibing"
3) "age"
4) "18"
5) "address"
6) "haikou"
7) "phone"
8) "15501892660"
127.0.0.1:6379[1]> hkeys hash
1) "username"
2) "age"
3) "address"
4) "phone"

```
> ###### 删除多个 ######

```
127.0.0.1:6379[1]> hkeys hash
1) "username"
2) "age"
3) "address"
4) "phone"
127.0.0.1:6379[1]> hdel hash address phone
(integer) 2
127.0.0.1:6379[1]> hkeys hash
1) "username"
2) "age"

```
> ###### 设置单个前判断键是否存在 ######

```
127.0.0.1:6379[1]> hsetnx hash username xiaobingbing
(integer) 0
127.0.0.1:6379[1]> hget hash username
"shenlibing"

```


> ##### 获取 #####

> ###### 获取单个 ######
 
```
127.0.0.1:6379[1]> hget hash username
"shenlibing"

```
> ###### 判断键是否存在 ######

```
127.0.0.1:6379[1]> hexists hash username
(integer) 1

```
> ###### 获取整个 ######

```
127.0.0.1:6379[1]> hgetall hash
1) "username"
2) "shenlibing"
3) "age"
4) "18"

```
> ###### 获取键 ######

```
127.0.0.1:6379[1]> hkeys hash
1) "username"
2) "age"

```
> ###### 获取值 ######

```
127.0.0.1:6379[1]> hvals hash
1) "shenlibing"
2) "18"

```
> ###### 获取长度 ######

```
127.0.0.1:6379[1]> hlen hash
(integer) 2

```
> ###### 获取多个 ######

```
127.0.0.1:6379[1]> hmget hash username age
1) "shenlibing"
2) "18"

```


> #### 列表list ####

> ##### 设置 #####

> ###### 从左往右进 ######

```
127.0.0.1:6379> lpush list 1 2 3 4 5 6 7 8 9 10
(integer) 10

```
> ###### 重置 ######

根据索引重置某个元素

```
127.0.0.1:6379> lset list 0 100
OK

```

> ###### 向左弹出 ######


```
127.0.0.1:6379> lpop list
"100"

```
> ###### 向右弹出 ######

```
127.0.0.1:6379> rpop list
"1"

```

> ##### 获取 #####

> ###### 获取多个 ######

根据索引截取`list`元素，获取多个元素

```
127.0.0.1:6379> lrange list 0 2
1) "10"
2) "9"
3) "8"

```
> ###### 获取单个 ######

根据索引查找`list`中某个元素，获取单个元素

```
127.0.0.1:6379> lindex list 0
"100"

```

> ###### 长度 ######

```
127.0.0.1:6379> llen list
(integer) 10

```


> ###### 获取所有 ######

```
127.0.0.1:6379> llen list
(integer) 8
127.0.0.1:6379> lrange list 0 7
1) "9"
2) "8"
3) "7"
4) "6"
5) "5"
6) "4"
7) "3"
8) "2"

```


> #### 集合set ####

无序，不允许重复

> ##### 设置 #####

> ###### 设置多个 ######


```
127.0.0.1:6379> sadd set a b c d
(integer) 4

```

> ###### 重命名 ######

```
127.0.0.1:6379> rename set set1
OK

```

> ###### 删除指定元素,一个或者多个 ######

```
127.0.0.1:6379> srem set1 a b
(integer) 2
127.0.0.1:6379> srem set1 c
(integer) 1

```

> ###### 随机删除一个元素 ######

```
127.0.0.1:6379> spop set2
"f"

```

> ##### 获取 #####

> ###### 长度 ######

```
127.0.0.1:6379> scard set1
(integer) 4

```

> ###### 获取所有 ######

```
127.0.0.1:6379> smembers set2
1) "d"
2) "e"
3) "c"
4) "f"

```

> ###### 差集 ######

```
127.0.0.1:6379> smembers set1 
1) "d"
2) "b"
3) "c"
4) "a"
127.0.0.1:6379> smembers set2
1) "d"
2) "e"
3) "c"
4) "f"
127.0.0.1:6379> sdiff set1 set2
1) "b"
2) "a"

```

```
127.0.0.1:6379> sdiff set2 set1
1) "e"
2) "f"

```

> ###### 交集 ######

```
127.0.0.1:6379> sinter set1 set2
1) "c"
2) "d"

```

> ###### 并集 ######

```
127.0.0.1:6379> sunion set1 set2
1) "c"
2) "e"
3) "f"
4) "a"
5) "b"
6) "d"

```

> ###### 是否存在某个元素 ######

```
127.0.0.1:6379> sismember set1 a
(integer) 1

```

> #### 有序集合sortedset ####


> ##### 设置 #####

> ###### 根据分数设置 ######

```
127.0.0.1:6379> zadd sortedset1 100 a 200 b 300 c
(integer) 3

```
> ###### 重命名 ######

```
127.0.0.1:6379> rename sortedset1 sortedset
OK

```

> ###### 加分数 ######

```
127.0.0.1:6379> zincrby sortedset 1000 a
"1100"
127.0.0.1:6379> zrank sortedset a
(integer) 2

```

> ##### 获取 #####

> ###### 长度 ######

```
127.0.0.1:6379> zcard sortedset
(integer) 3

```
> ###### 获取分数 ######

获取元素的分数

```
127.0.0.1:6379> zscore sortedset a
"100"

```

> ###### 根据分数范围返回成员个数 ######

```
127.0.0.1:6379> zcount sortedset 0 200
(integer) 2
127.0.0.1:6379> zcount sortedset 0 300
(integer) 3

```

> ###### 获取元素索引 ######

```
127.0.0.1:6379> zrank sortedset a
(integer) 0
127.0.0.1:6379> zrank sortedset b
(integer) 1

```
> ###### 根据索引区间返回元素 ######

可以带分数显示

```
127.0.0.1:6379> zcard sortedset
(integer) 3
127.0.0.1:6379> zrange sortedset 0 2
1) "b"
2) "c"
3) "a"
127.0.0.1:6379> zrange sortedset 0 2 withscores
1) "b"
2) "200"
3) "c"
4) "300"
5) "a"
6) "1100"

```

> ### Redis_Desktop_Manager工具 ###

[下载](https://redisdesktop.com/download)

[下载2](http://learning.happymmall.com/%E4%BA%8C%E6%9C%9F%E9%9B%86%E7%BE%A4%E5%8F%8A%E7%BC%93%E5%AD%98%E5%88%86%E5%B8%83%E5%BC%8FJava%E7%AB%AF/redis-desktop-manager-088-windows.exe)

[Quick Start](http://docs.redisdesktop.com/en/latest/quick-start/)

> ## 原生单点登录 ##

原生Redis+Cookie+Jackson+Filter解决session共享问题实现单点登录

> ### java使用Jedis客户端 ###


> #### 编辑`pom.xml` ####

```xml
<dependency>
  <groupId>redis.clients</groupId>
  <artifactId>jedis</artifactId>
  <version>2.6.0</version>
</dependency>

```

> #### 获取连接 ####

从连接池获取连接

```java
package com.mmall.common;

import com.mmall.util.PropertiesUtil;
import redis.clients.jedis.Jedis;
import redis.clients.jedis.JedisPool;
import redis.clients.jedis.JedisPoolConfig;

/**
 * Created by geely
 */
public class RedisPool {
    //jedis连接池
    private static JedisPool pool;
    //最大连接数
    private static Integer maxTotal = Integer.parseInt(PropertiesUtil.getProperty("redis.max.total", "20"));
    //在jedispool中最大的idle状态(空闲的)的jedis实例的个数
    private static Integer maxIdle = Integer.parseInt(PropertiesUtil.getProperty("redis.max.idle", "20"));
    //在jedispool中最小的idle状态(空闲的)的jedis实例的个数
    private static Integer minIdle = Integer.parseInt(PropertiesUtil.getProperty("redis.min.idle", "20"));

    //在borrow一个jedis实例的时候，是否要进行验证操作，如果赋值true。则得到的jedis实例肯定是可以用的。
    private static Boolean testOnBorrow = Boolean.parseBoolean(PropertiesUtil.getProperty("redis.test.borrow", "true"));
    //在return一个jedis实例的时候，是否要进行验证操作，如果赋值true。则放回jedispool的jedis实例肯定是可以用的。
    private static Boolean testOnReturn = Boolean.parseBoolean(PropertiesUtil.getProperty("redis.test.return", "true"));

    private static String redisIp = PropertiesUtil.getProperty("redis.ip");
    private static Integer redisPort = Integer.parseInt(PropertiesUtil.getProperty("redis.port"));


    private static void initPool() {
        JedisPoolConfig config = new JedisPoolConfig();

        config.setMaxTotal(maxTotal);
        config.setMaxIdle(maxIdle);
        config.setMinIdle(minIdle);

        config.setTestOnBorrow(testOnBorrow);
        config.setTestOnReturn(testOnReturn);


        //连接耗尽的时候，是否阻塞，false会抛出异常，true阻塞直到超时。默认为true。
        config.setBlockWhenExhausted(true);

        pool = new JedisPool(config, redisIp, redisPort, 1000 * 2);
    }

    static {
        initPool();
    }

    public static Jedis getJedis() {
        return pool.getResource();
    }


    public static void returnBrokenResource(Jedis jedis) {
        pool.returnBrokenResource(jedis);
    }


    public static void returnResource(Jedis jedis) {
        pool.returnResource(jedis);
    }


    public static void main(String[] args) {
        Jedis jedis = pool.getResource();
        jedis.set("geelykey", "geelyvalue");
        returnResource(jedis);
        //临时调用，销毁连接池中的所有连接
        pool.destroy();
        System.out.println("program is end");
    }
}

```

> #### Jedis API封装 ####

读写数据


```java

package com.mmall.util;

import com.mmall.common.RedisPool;
import lombok.extern.slf4j.Slf4j;
import redis.clients.jedis.Jedis;

/**
 * Created by geely
 */
@Slf4j
public class RedisPoolUtil {


    /**
     * 设置key的有效期，单位是秒
     * @param key
     * @param exTime
     * @return
     */
    public static Long expire(String key,int exTime){
        Jedis jedis = null;
        Long result = null;
        try {
            jedis = RedisPool.getJedis();
            result = jedis.expire(key,exTime);
        } catch (Exception e) {
            log.error("expire key:{} error",key,e);
            RedisPool.returnBrokenResource(jedis);
            return result;
        }
        RedisPool.returnResource(jedis);
        return result;
    }

    //exTime的单位是秒
    public static String setEx(String key,String value,int exTime){
        Jedis jedis = null;
        String result = null;
        try {
            jedis = RedisPool.getJedis();
            result = jedis.setex(key,exTime,value);
        } catch (Exception e) {
            log.error("setex key:{} value:{} error",key,value,e);
            RedisPool.returnBrokenResource(jedis);
            return result;
        }
        RedisPool.returnResource(jedis);
        return result;
    }

    public static String set(String key,String value){
        Jedis jedis = null;
        String result = null;

        try {
            jedis = RedisPool.getJedis();
            result = jedis.set(key,value);
        } catch (Exception e) {
            log.error("set key:{} value:{} error",key,value,e);
            RedisPool.returnBrokenResource(jedis);
            return result;
        }
        RedisPool.returnResource(jedis);
        return result;
    }

    public static String get(String key){
        Jedis jedis = null;
        String result = null;
        try {
            jedis = RedisPool.getJedis();
            result = jedis.get(key);
        } catch (Exception e) {
            log.error("get key:{} error",key,e);
            RedisPool.returnBrokenResource(jedis);
            return result;
        }
        RedisPool.returnResource(jedis);
        return result;
    }

    public static Long del(String key){
        Jedis jedis = null;
        Long result = null;
        try {
            jedis = RedisPool.getJedis();
            result = jedis.del(key);
        } catch (Exception e) {
            log.error("del key:{} error",key,e);
            RedisPool.returnBrokenResource(jedis);
            return result;
        }
        RedisPool.returnResource(jedis);
        return result;
    }

    public static void main(String[] args) {
        Jedis jedis = RedisPool.getJedis();

        RedisPoolUtil.set("keyTest","value");

        String value = RedisPoolUtil.get("keyTest");

        RedisPoolUtil.setEx("keyex","valueex",60*10);

        RedisPoolUtil.expire("keyTest",60*20);

        RedisPoolUtil.del("keyTest");


        String aaa = RedisPoolUtil.get(null);
        System.out.println(aaa);

        System.out.println("end");


    }


}

```

> ### Jackson封装JacksonUtil ###

> #### 编辑`pom.xml` ####

```xml
<dependency>
    <groupId>org.codehaus.jackson</groupId>
    <artifactId>jackson-mapper-asl</artifactId>
    <version>1.9.12</version>
</dependency>

```

> #### 多泛型序列化和反序列化 ####

```java

package com.mmall.util;

import com.google.common.collect.Lists;
import com.mmall.pojo.Category;
import com.mmall.pojo.TestPojo;
import com.mmall.pojo.User;
import lombok.extern.slf4j.Slf4j;
import org.apache.commons.lang.StringUtils;
import org.codehaus.jackson.map.DeserializationConfig;
import org.codehaus.jackson.map.ObjectMapper;
import org.codehaus.jackson.map.SerializationConfig;
import org.codehaus.jackson.map.annotate.JsonSerialize.Inclusion;
import org.codehaus.jackson.type.JavaType;
import org.codehaus.jackson.type.TypeReference;

import java.io.IOException;
import java.text.SimpleDateFormat;
import java.util.Date;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

/**
 * Created by geely
 */
@Slf4j
public class JsonUtil {

    private static ObjectMapper objectMapper = new ObjectMapper();
    static{
        //对象的所有字段全部列入
        objectMapper.setSerializationInclusion(Inclusion.ALWAYS);

        //取消默认转换timestamps形式
        objectMapper.configure(SerializationConfig.Feature.WRITE_DATES_AS_TIMESTAMPS,false);

        //忽略空Bean转json的错误
        objectMapper.configure(SerializationConfig.Feature.FAIL_ON_EMPTY_BEANS,false);

        //所有的日期格式都统一为以下的样式，即yyyy-MM-dd HH:mm:ss
        objectMapper.setDateFormat(new SimpleDateFormat(DateTimeUtil.STANDARD_FORMAT));

        //忽略 在json字符串中存在，但是在java对象中不存在对应属性的情况。防止错误
        objectMapper.configure(DeserializationConfig.Feature.FAIL_ON_UNKNOWN_PROPERTIES,false);
    }

    public static <T> String obj2String(T obj){
        if(obj == null){
            return null;
        }
        try {
            return obj instanceof String ? (String)obj :  objectMapper.writeValueAsString(obj);
        } catch (Exception e) {
            log.warn("Parse Object to String error",e);
            return null;
        }
    }

    public static <T> String obj2StringPretty(T obj){
        if(obj == null){
            return null;
        }
        try {
            return obj instanceof String ? (String)obj :  objectMapper.writerWithDefaultPrettyPrinter().writeValueAsString(obj);
        } catch (Exception e) {
            log.warn("Parse Object to String error",e);
            return null;
        }
    }

    public static <T> T string2Obj(String str,Class<T> clazz){
        if(StringUtils.isEmpty(str) || clazz == null){
            return null;
        }

        try {
            return clazz.equals(String.class)? (T)str : objectMapper.readValue(str,clazz);
        } catch (Exception e) {
            log.warn("Parse String to Object error",e);
            return null;
        }
    }

    public static <T> T string2Obj(String str, TypeReference<T> typeReference){
        if(StringUtils.isEmpty(str) || typeReference == null){
            return null;
        }
        try {
            return (T)(typeReference.getType().equals(String.class)? str : objectMapper.readValue(str,typeReference));
        } catch (Exception e) {
            log.warn("Parse String to Object error",e);
            return null;
        }
    }


    public static <T> T string2Obj(String str,Class<?> collectionClass,Class<?>... elementClasses){
        JavaType javaType = objectMapper.getTypeFactory().constructParametricType(collectionClass,elementClasses);
        try {
            return objectMapper.readValue(str,javaType);
        } catch (Exception e) {
            log.warn("Parse String to Object error",e);
            return null;
        }
    }

    public static void main(String[] args) {
        TestPojo testPojo = new TestPojo();
        testPojo.setName("Geely");
        testPojo.setId(666);

        //{"name":"Geely","id":666}
        String json = "{\"name\":\"Geely\",\"color\":\"blue\",\"id\":666}";
        TestPojo testPojoObject = JsonUtil.string2Obj(json,TestPojo.class);
//        String testPojoJson = JsonUtil.obj2String(testPojo);
//        log.info("testPojoJson:{}",testPojoJson);
        log.info("end");


//        User user = new User();
//        user.setId(2);
//        user.setEmail("geely@happymmall.com");
//        user.setCreateTime(new Date());
//        String userJsonPretty = JsonUtil.obj2StringPretty(user);
//        log.info("userJson:{}",userJsonPretty);


//        User u2 = new User();
//        u2.setId(2);
//        u2.setEmail("geelyu2@happymmall.com");
//
//
//
//        String user1Json = JsonUtil.obj2String(u1);
//
//        String user1JsonPretty = JsonUtil.obj2StringPretty(u1);
//
//        log.info("user1Json:{}",user1Json);
//
//        log.info("user1JsonPretty:{}",user1JsonPretty);
//
//
//        User user = JsonUtil.string2Obj(user1Json,User.class);
//
//
//        List<User> userList = Lists.newArrayList();
//        userList.add(u1);
//        userList.add(u2);
//
//        String userListStr = JsonUtil.obj2StringPretty(userList);
//
//        log.info("==================");
//
//        log.info(userListStr);
//
//
//        List<User> userListObj1 = JsonUtil.string2Obj(userListStr, new TypeReference<List<User>>() {
//        });
//
//
//        List<User> userListObj2 = JsonUtil.string2Obj(userListStr,List.class,User.class);

        System.out.println("end");

    }

}

```


> ### Cookie封装 ###

其中`COOKIE_NAME`和`COOKIE_DOMAIN`是根据实际项目，线上的域名来配置的，如果扩展开来讲，对于里面每个属性，在二级/三级域名下的读写问题是必须要细化的

Cookie的读、写、删

```java
package com.mmall.util;

import lombok.extern.slf4j.Slf4j;
import org.apache.commons.lang.StringUtils;

import javax.servlet.http.Cookie;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

/**
 * Created by geely
 */
@Slf4j
public class CookieUtil {

    private final static String COOKIE_DOMAIN = ".happymmall.com";
    private final static String COOKIE_NAME = "mmall_login_token";


    public static String readLoginToken(HttpServletRequest request){
        Cookie[] cks = request.getCookies();
        if(cks != null){
            for(Cookie ck : cks){
                log.info("read cookieName:{},cookieValue:{}",ck.getName(),ck.getValue());
                if(StringUtils.equals(ck.getName(),COOKIE_NAME)){
                    log.info("return cookieName:{},cookieValue:{}",ck.getName(),ck.getValue());
                    return ck.getValue();
                }
            }
        }
        return null;
    }

    //X:domain=".happymmall.com"
    //a:A.happymmall.com            cookie:domain=A.happymmall.com;path="/"
    //b:B.happymmall.com            cookie:domain=B.happymmall.com;path="/"
    //c:A.happymmall.com/test/cc    cookie:domain=A.happymmall.com;path="/test/cc"
    //d:A.happymmall.com/test/dd    cookie:domain=A.happymmall.com;path="/test/dd"
    //e:A.happymmall.com/test       cookie:domain=A.happymmall.com;path="/test"

    public static void writeLoginToken(HttpServletResponse response,String token){
        Cookie ck = new Cookie(COOKIE_NAME,token);
        ck.setDomain(COOKIE_DOMAIN);
        ck.setPath("/");//代表设置在根目录
        ck.setHttpOnly(true);
        //单位是秒。
        //如果这个maxage不设置的话，cookie就不会写入硬盘，而是写在内存。只在当前页面有效。
        ck.setMaxAge(60 * 60 * 24 * 365);//如果是-1，代表永久
        log.info("write cookieName:{},cookieValue:{}",ck.getName(),ck.getValue());
        response.addCookie(ck);
    }


    public static void delLoginToken(HttpServletRequest request,HttpServletResponse response){
        Cookie[] cks = request.getCookies();
        if(cks != null){
            for(Cookie ck : cks){
                if(StringUtils.equals(ck.getName(),COOKIE_NAME)){
                    ck.setDomain(COOKIE_DOMAIN);
                    ck.setPath("/");
                    ck.setMaxAge(0);//设置成0，代表删除此cookie。
                    log.info("del cookieName:{},cookieValue:{}",ck.getName(),ck.getValue());
                    response.addCookie(ck);
                    return;
                }
            }
        }
    }

}

```


> ### SessionExpireFilter构建Session时间重置过滤器 ###

> #### 编辑`web.xml` ####

```xml
<filter>
    <filter-name>sessionExpireFilter</filter-name>
    <filter-class>com.mmall.controller.common.SessionExpireFilter</filter-class>
</filter>
<filter-mapping>
    <filter-name>sessionExpireFilter</filter-name>
    <url-pattern>*.do</url-pattern>
</filter-mapping>

```

> #### 时间重置过滤器类 ####

SessionExpireFilter.java

```java
package com.mmall.controller.common;


import com.mmall.common.Const;
import com.mmall.pojo.User;
import com.mmall.util.CookieUtil;
import com.mmall.util.JsonUtil;
import com.mmall.util.RedisShardedPoolUtil;
import org.apache.commons.lang.StringUtils;

import javax.servlet.Filter;
import javax.servlet.FilterChain;
import javax.servlet.FilterConfig;
import javax.servlet.ServletException;
import javax.servlet.ServletRequest;
import javax.servlet.ServletResponse;
import javax.servlet.http.HttpServletRequest;
import java.io.IOException;

/**
 * Created by geely
 */
public class SessionExpireFilter implements Filter {
    @Override
    public void init(FilterConfig filterConfig) throws ServletException {

    }

    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        HttpServletRequest httpServletRequest = (HttpServletRequest)servletRequest;

        String loginToken = CookieUtil.readLoginToken(httpServletRequest);

        if(StringUtils.isNotEmpty(loginToken)){
            //判断logintoken是否为空或者""；
            //如果不为空的话，符合条件，继续拿user信息

            String userJsonStr = RedisShardedPoolUtil.get(loginToken);
            User user = JsonUtil.string2Obj(userJsonStr,User.class);
            if(user != null){
                //如果user不为空，则重置session的时间，即调用expire命令
                RedisShardedPoolUtil.expire(loginToken, Const.RedisCacheExtime.REDIS_SESSION_EXTIME);
            }
        }
        filterChain.doFilter(servletRequest,servletResponse);
    }


    @Override
    public void destroy() {

    }
}

```

> ### Guava Cache迁移Redis分布式缓存 ###

描述：修改密码时需要验证`token`，`token`的生成是在校验忘记密码的问题答案是正确的时候生成，如果答案是正确的话，返回给前台。重置密码发起请求携带该`token`到后台校验是否一致。

> #### 集群后Guava Cache的不足 ####

`Tomcat`之前使用的`guava cache`存储`token`，它只存在于`tomcat`实例上，`tomcat`及`tomcat`之间并不共享，所以必须迁移。否则负载均衡就`TomcatA`存储了`guava cache`，`TomcatB`想拿就拿不到了

> #### Guava Cache迁移Redis缓存 ####

> ##### 修改前 #####

`guava cache`存储`token`

```java
public ServerResponse<String> checkAnswer(String username,String question,String answer){
    int resultCount = userMapper.checkAnswer(username,question,answer);
    if(resultCount>0){
        //说明问题及问题答案是这个用户的,并且是正确的
        String forgetToken = UUID.randomUUID().toString();
        TokenCache.setKey(TokenCache.TOKEN_PREFIX+username,forgetToken);
        return ServerResponse.createBySuccess(forgetToken);
    }
    return ServerResponse.createByErrorMessage("问题的答案错误");
}

public ServerResponse<String> forgetResetPassword(String username,String passwordNew,String forgetToken){
    if(org.apache.commons.lang3.StringUtils.isBlank(forgetToken)){
        return ServerResponse.createByErrorMessage("参数错误,token需要传递");
    }
    ServerResponse validResponse = this.checkValid(username,Const.USERNAME);
    if(validResponse.isSuccess()){
        //用户不存在
        return ServerResponse.createByErrorMessage("用户不存在");
    }
    String token = TokenCache.getKey(TokenCache.TOKEN_PREFIX+username);
    if(org.apache.commons.lang3.StringUtils.isBlank(token)){
        return ServerResponse.createByErrorMessage("token无效或者过期");
    }

    if(org.apache.commons.lang3.StringUtils.equals(forgetToken,token)){
        String md5Password  = MD5Util.MD5EncodeUtf8(passwordNew);
        int rowCount = userMapper.updatePasswordByUsername(username,md5Password);

        if(rowCount > 0){
            return ServerResponse.createBySuccessMessage("修改密码成功");
        }
    }else{
        return ServerResponse.createByErrorMessage("token错误,请重新获取重置密码的token");
    }
    return ServerResponse.createByErrorMessage("修改密码失败");
}
```

> ##### 修改后 #####

后台`token`保存在`Redis`上

```java
public ServerResponse<String> checkAnswer(String username,String question,String answer){
    int resultCount = userMapper.checkAnswer(username,question,answer);
    if(resultCount>0){
        //说明问题及问题答案是这个用户的,并且是正确的
        String forgetToken = UUID.randomUUID().toString();
        RedisShardedPoolUtil.setEx(Const.TOKEN_PREFIX+username,forgetToken,60*60*12);
        return ServerResponse.createBySuccess(forgetToken);
    }
    return ServerResponse.createByErrorMessage("问题的答案错误");
}

public ServerResponse<String> forgetResetPassword(String username,String passwordNew,String forgetToken){
    if(org.apache.commons.lang3.StringUtils.isBlank(forgetToken)){
        return ServerResponse.createByErrorMessage("参数错误,token需要传递");
    }
    ServerResponse validResponse = this.checkValid(username,Const.USERNAME);
    if(validResponse.isSuccess()){
        //用户不存在
        return ServerResponse.createByErrorMessage("用户不存在");
    }
    String token = RedisShardedPoolUtil.get(Const.TOKEN_PREFIX+username);
    if(org.apache.commons.lang3.StringUtils.isBlank(token)){
        return ServerResponse.createByErrorMessage("token无效或者过期");
    }

    if(org.apache.commons.lang3.StringUtils.equals(forgetToken,token)){
        String md5Password  = MD5Util.MD5EncodeUtf8(passwordNew);
        int rowCount = userMapper.updatePasswordByUsername(username,md5Password);

        if(rowCount > 0){
            return ServerResponse.createBySuccessMessage("修改密码成功");
        }
    }else{
        return ServerResponse.createByErrorMessage("token错误,请重新获取重置密码的token");
    }
    return ServerResponse.createByErrorMessage("修改密码失败");
}
```


> ## Redis分布式环境搭建 ##

第一个Redis不变，修改第二个Redis


> ### 编辑`redis.conf` ###

![](Redis分布式缓存修改端口.png)

> ### 启动 ###

第一个默认启动,默认端口6379

```
[root@192 src]# ./redis-server &

```

第二个指定配置文件启动，修改后的端口6380

```
[root@192 src]# ./redis-server ../redis.conf &

```

> ### java代码连接Redis分布式缓存 ###

一致性算法

> #### 获取连接 ####

```java
package com.mmall.common;

import com.mmall.util.PropertiesUtil;
import redis.clients.jedis.JedisPoolConfig;
import redis.clients.jedis.JedisShardInfo;
import redis.clients.jedis.ShardedJedis;
import redis.clients.jedis.ShardedJedisPool;
import redis.clients.util.Hashing;
import redis.clients.util.Sharded;

import java.util.ArrayList;
import java.util.List;

/**
 * Created by geely
 */
public class RedisShardedPool {
    private static ShardedJedisPool pool;//sharded jedis连接池
    private static Integer maxTotal = Integer.parseInt(PropertiesUtil.getProperty("redis.max.total","20")); //最大连接数
    private static Integer maxIdle = Integer.parseInt(PropertiesUtil.getProperty("redis.max.idle","20"));//在jedispool中最大的idle状态(空闲的)的jedis实例的个数
    private static Integer minIdle = Integer.parseInt(PropertiesUtil.getProperty("redis.min.idle","20"));//在jedispool中最小的idle状态(空闲的)的jedis实例的个数

    private static Boolean testOnBorrow = Boolean.parseBoolean(PropertiesUtil.getProperty("redis.test.borrow","true"));//在borrow一个jedis实例的时候，是否要进行验证操作，如果赋值true。则得到的jedis实例肯定是可以用的。
    private static Boolean testOnReturn = Boolean.parseBoolean(PropertiesUtil.getProperty("redis.test.return","true"));//在return一个jedis实例的时候，是否要进行验证操作，如果赋值true。则放回jedispool的jedis实例肯定是可以用的。

    private static String redis1Ip = PropertiesUtil.getProperty("redis1.ip");
    private static Integer redis1Port = Integer.parseInt(PropertiesUtil.getProperty("redis1.port"));
    private static String redis2Ip = PropertiesUtil.getProperty("redis2.ip");
    private static Integer redis2Port = Integer.parseInt(PropertiesUtil.getProperty("redis2.port"));




    private static void initPool(){
        JedisPoolConfig config = new JedisPoolConfig();

        config.setMaxTotal(maxTotal);
        config.setMaxIdle(maxIdle);
        config.setMinIdle(minIdle);

        config.setTestOnBorrow(testOnBorrow);
        config.setTestOnReturn(testOnReturn);

        //连接耗尽的时候，是否阻塞，false会抛出异常，true阻塞直到超时。默认为true。
        config.setBlockWhenExhausted(true);

        JedisShardInfo info1 = new JedisShardInfo(redis1Ip,redis1Port,1000*2);

        JedisShardInfo info2 = new JedisShardInfo(redis2Ip,redis2Port,1000*2);

        List<JedisShardInfo> jedisShardInfoList = new ArrayList<JedisShardInfo>(2);

        jedisShardInfoList.add(info1);
        jedisShardInfoList.add(info2);

        pool = new ShardedJedisPool(config,jedisShardInfoList, Hashing.MURMUR_HASH, Sharded.DEFAULT_KEY_TAG_PATTERN);
    }

    static{
        initPool();
    }

    public static ShardedJedis getJedis(){
        return pool.getResource();
    }


    public static void returnBrokenResource(ShardedJedis jedis){
        pool.returnBrokenResource(jedis);
    }



    public static void returnResource(ShardedJedis jedis){
        pool.returnResource(jedis);
    }


    public static void main(String[] args) {
        ShardedJedis jedis = pool.getResource();

        for(int i =0;i<10;i++){
            jedis.set("key"+i,"value"+i);
        }
        returnResource(jedis);

//        pool.destroy();//临时调用，销毁连接池中的所有连接
        System.out.println("program is end");


    }
}

```

> #### 读写数据 ####

```java
package com.mmall.util;

import com.mmall.common.RedisShardedPool;
import lombok.extern.slf4j.Slf4j;
import redis.clients.jedis.ShardedJedis;

/**
 * Created by geely
 */
@Slf4j
public class RedisShardedPoolUtil {

    /**
     * 设置key的有效期，单位是秒
     * @param key
     * @param exTime
     * @return
     */
    public static Long expire(String key,int exTime){
        ShardedJedis jedis = null;
        Long result = null;
        try {
            jedis = RedisShardedPool.getJedis();
            result = jedis.expire(key,exTime);
        } catch (Exception e) {
            log.error("expire key:{} error",key,e);
            RedisShardedPool.returnBrokenResource(jedis);
            return result;
        }
        RedisShardedPool.returnResource(jedis);
        return result;
    }

    //exTime的单位是秒
    public static String setEx(String key,String value,int exTime){
        ShardedJedis jedis = null;
        String result = null;
        try {
            jedis = RedisShardedPool.getJedis();
            result = jedis.setex(key,exTime,value);
        } catch (Exception e) {
            log.error("setex key:{} value:{} error",key,value,e);
            RedisShardedPool.returnBrokenResource(jedis);
            return result;
        }
        RedisShardedPool.returnResource(jedis);
        return result;
    }

    public static String set(String key,String value){
        ShardedJedis jedis = null;
        String result = null;

        try {
            jedis = RedisShardedPool.getJedis();
            result = jedis.set(key,value);
        } catch (Exception e) {
            log.error("set key:{} value:{} error",key,value,e);
            RedisShardedPool.returnBrokenResource(jedis);
            return result;
        }
        RedisShardedPool.returnResource(jedis);
        return result;
    }

    public static String getSet(String key,String value){
        ShardedJedis jedis = null;
        String result = null;

        try {
            jedis = RedisShardedPool.getJedis();
            result = jedis.getSet(key,value);
        } catch (Exception e) {
            log.error("getset key:{} value:{} error",key,value,e);
            RedisShardedPool.returnBrokenResource(jedis);
            return result;
        }
        RedisShardedPool.returnResource(jedis);
        return result;
    }

    public static String get(String key){
        ShardedJedis jedis = null;
        String result = null;
        try {
            jedis = RedisShardedPool.getJedis();
            result = jedis.get(key);
        } catch (Exception e) {
            log.error("get key:{} error",key,e);
            RedisShardedPool.returnBrokenResource(jedis);
            return result;
        }
        RedisShardedPool.returnResource(jedis);
        return result;
    }

    public static Long del(String key){
        ShardedJedis jedis = null;
        Long result = null;
        try {
            jedis = RedisShardedPool.getJedis();
            result = jedis.del(key);
        } catch (Exception e) {
            log.error("del key:{} error",key,e);
            RedisShardedPool.returnBrokenResource(jedis);
            return result;
        }
        RedisShardedPool.returnResource(jedis);
        return result;
    }

    public static Long setnx(String key,String value){
        ShardedJedis jedis = null;
        Long result = null;

        try {
            jedis = RedisShardedPool.getJedis();
            result = jedis.setnx(key,value);
        } catch (Exception e) {
            log.error("setnx key:{} value:{} error",key,value,e);
            RedisShardedPool.returnBrokenResource(jedis);
            return result;
        }
        RedisShardedPool.returnResource(jedis);
        return result;
    }


    public static void main(String[] args) {
        ShardedJedis jedis = RedisShardedPool.getJedis();

        RedisPoolUtil.set("keyTest","value");

        String value = RedisPoolUtil.get("keyTest");

        RedisPoolUtil.setEx("keyex","valueex",60*10);

        RedisPoolUtil.expire("keyTest",60*20);

        RedisPoolUtil.del("keyTest");


        String aaa = RedisPoolUtil.get(null);
        System.out.println(aaa);

        System.out.println("end");


    }

}

```



> ## Spring Session单点登录 ##

> ### 参考 ###


[官网](https://spring.io/projects/spring-session)

[官方文档](https://docs.spring.io/spring-session/docs/current/reference/html5/)

[GitHub地址](https://github.com/spring-projects/spring-session/tree/1.2.x)

[Quick Start](https://docs.spring.io/spring-session/docs/current/reference/html5/guides/java-redis.html)

> ### 引入依赖 ###

```xml
<!-- spring session 单点登录 -->
<dependency>
  <groupId>org.springframework.session</groupId>
  <artifactId>spring-session-data-redis</artifactId>
  <version>1.2.0.RELEASE</version>
</dependency>

```

> ### Spring Session整合Redis ###

`applicationContext.xml`引入整合配置文件

```xml
<import resource="applicationContext-spring-session.xml"/>

```
新建`applicationContext-spring-session.xml`资源文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:tx="http://www.springframework.org/schema/tx" xmlns:jdbc="http://www.springframework.org/schema/jdbc"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="
     http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd
     http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
     http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx.xsd
     http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd">


    <bean id="redisHttpSessionConfiguration" class="org.springframework.session.data.redis.config.annotation.web.http.RedisHttpSessionConfiguration">
        <property name="maxInactiveIntervalInSeconds" value="1800" />
    </bean>

    <bean id="defaultCookieSerializer" class="org.springframework.session.web.http.DefaultCookieSerializer">
        <property name="domainName" value="localhost" />
        <property name="useHttpOnlyCookie" value="true" />
        <property name="cookiePath" value="/" />
        <property name="cookieMaxAge" value="31536000" />
    </bean>

    <bean id="jedisPoolConfig" class="redis.clients.jedis.JedisPoolConfig">
        <property name="maxTotal" value="20"/>
    </bean>

    <bean id="jedisConnectionFactory" class="org.springframework.data.redis.connection.jedis.JedisConnectionFactory">
        <property name="hostName" value="192.168.1.104" />
        <property name="port" value="6379" />
        <property name="poolConfig" ref="jedisPoolConfig" />
    </bean>
</beans>


```

> ### 配置`web.xml` ###

```xml
<filter>
    <filter-name>springSessionRepositoryFilter</filter-name>
    <filter-class>org.springframework.web.filter.DelegatingFilterProxy</filter-class>
</filter>
<filter-mapping>
    <filter-name>springSessionRepositoryFilter</filter-name>
    <url-pattern>*.do</url-pattern>
</filter-mapping>

```

> ### 使用 ###

这里的`session`是经过包装过的代理类

![](包装代理类.png)

```
session.setAttribute(Const.CURRENT_USER,response.getData());
session.removeAttribute(Const.CURRENT_USER);
User user = (User)session.getAttribute(Const.CURRENT_USER);

```

> ### 坑 ###

> #### Caused by: org.springframework.beans.factory.NoSuchBeanDefinitionException: No qualifying bean of type [org.springframework.session.SessionRepository] found for dependency: expected at least 1 bean which qualifies as autowire candidate for this dependency. Dependency annotations: {} ####

描述： 启动报错

解决：修改`<org.springframework.version>4.0.0.RELEASE</org.springframework.version>`为
 `<org.springframework.version>4.0.3.RELEASE</org.springframework.version>`


> #### No bean named 'springSessionRepositoryFilter' is defined ####

描述：启动报错，容器找不到该`bean`

```xml
<filter>
    <filter-name>springSessionRepositoryFilter</filter-name>
    <filter-class>org.springframework.web.filter.DelegatingFilterProxy</filter-class>
</filter>
<filter-mapping>
    <filter-name>springSessionRepositoryFilter</filter-name>
    <url-pattern>*.do</url-pattern>
</filter-mapping>

```
解决：

`spring`配置文件没有引入`spring-session`整合配置文件

```xml
<import resource="applicationContext-spring-session.xml"/>

```

> ## SpringMVC全局异常控制 ##


> ### Spring及SpringMVC包扫描隔离 ###


> #### Spring扫描 ####

排除`controller`扫描注解

```xml
<context:component-scan base-package="com.mmall" annotation-config="true">
    <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller" />
</context:component-scan>

```
> #### SpringMVC扫描 ####

```xml
<!--springmvc扫描包指定到controller，防止重复扫描
    use-default-filters="false" 关闭默认扫描
 -->
<context:component-scan base-package="com.mmall.controller" annotation-config="true" use-default-filters="false">
    <context:include-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
</context:component-scan>

```
> ### @Component注解 ###

异常包装类`ExceptionResolver.java`,必须要加上`@Component`注解，使其成为容器中的`bean`，`@Component`类似于`@Controller、@Service、@Repository`

`dao`层用`@Repository`
`service`层用`@Service`
`controller`层用`@Controller`
其它的用`@Component`

```java
package com.mmall.common;

import lombok.extern.slf4j.Slf4j;
import org.springframework.stereotype.Component;
import org.springframework.stereotype.Repository;
import org.springframework.stereotype.Service;
import org.springframework.web.servlet.HandlerExceptionResolver;
import org.springframework.web.servlet.ModelAndView;
import org.springframework.web.servlet.view.json.MappingJacksonJsonView;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

/**
 * Created by geely
 */
@Slf4j
@Component
public class ExceptionResolver implements HandlerExceptionResolver{

    @Override
    public ModelAndView resolveException(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Object o, Exception e) {
        log.error("{} Exception",httpServletRequest.getRequestURI(),e);
        ModelAndView modelAndView = new ModelAndView(new MappingJacksonJsonView());

        //当使用是jackson2.x的时候使用MappingJackson2JsonView，本项目使用的是1.9。
        modelAndView.addObject("status",ResponseCode.ERROR.getCode());
        modelAndView.addObject("msg","接口异常,详情请查看服务端日志的异常信息");
        modelAndView.addObject("data",e.toString());
        return modelAndView;
    }

}

```

> ## SpringMVC拦截器 ##

> ### springmvc配置拦截器 ###

`<mvc:mapping path="/manage/**"/>`代表请求经过`/manage`目录下的**子目录**下的`controller`也会被拦截

`<mvc:mapping path="/manage/*"/>`代表请求经过`/manage`目录下的`controller`会被拦截，而子目录下的`controller`不会被拦截

`<mvc:exclude-mapping path="/manage/user/login.do"/>`配置不拦截某些请求

```xml
<mvc:interceptors>
    <!-- 定义在这里的，所有的都会拦截-->
    <mvc:interceptor>
        <!--manage/a.do  /manage/*-->
        <!--manage/b.do  /manage/*-->
        <!--manage/product/save.do /manage/**-->
        <!--manage/order/detail.do /manage/**-->
        <mvc:mapping path="/manage/**"/>
        <!--<mvc:exclude-mapping path="/manage/user/login.do"/>-->
        <bean class="com.mmall.controller.common.interceptor.AuthorityInterceptor" />
    </mvc:interceptor>

</mvc:interceptors>

```
> ### 定义拦截器处理类 ###

**preHandle**请求到达`controll`之前会调用该方法

**postHandle**请求到达`controller`处理后会调用该方法

**afterCompletion**请求到达`controller`处理后返回`ModelAndView`后会调用该方法
			

```java
package com.mmall.controller.common.interceptor;

import com.google.common.collect.Maps;
import com.mmall.common.Const;
import com.mmall.common.ServerResponse;
import com.mmall.pojo.User;
import com.mmall.util.CookieUtil;
import com.mmall.util.JsonUtil;
import com.mmall.util.RedisShardedPoolUtil;
import lombok.extern.slf4j.Slf4j;
import org.apache.commons.lang.StringUtils;
import org.springframework.web.method.HandlerMethod;
import org.springframework.web.servlet.HandlerInterceptor;
import org.springframework.web.servlet.ModelAndView;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.PrintWriter;
import java.util.Arrays;
import java.util.Iterator;
import java.util.Map;

/**
 * Created by geely
 */
@Slf4j
public class AuthorityInterceptor implements HandlerInterceptor{

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        log.info("preHandle");
        //请求中Controller中的方法名
        HandlerMethod handlerMethod = (HandlerMethod)handler;

        //解析HandlerMethod

        String methodName = handlerMethod.getMethod().getName();
        String className = handlerMethod.getBean().getClass().getSimpleName();

        //解析参数,具体的参数key以及value是什么，我们打印日志
        StringBuffer requestParamBuffer = new StringBuffer();
        Map paramMap = request.getParameterMap();
        Iterator it = paramMap.entrySet().iterator();
        while (it.hasNext()){
            Map.Entry entry = (Map.Entry)it.next();
            String mapKey = (String)entry.getKey();

            String mapValue = StringUtils.EMPTY;

            //request这个参数的map，里面的value返回的是一个String[]
            Object obj = entry.getValue();
            if(obj instanceof String[]){
                String[] strs = (String[])obj;
                mapValue = Arrays.toString(strs);
            }
            requestParamBuffer.append(mapKey).append("=").append(mapValue);
        }

        if(StringUtils.equals(className,"UserManageController") && StringUtils.equals(methodName,"login")){
            log.info("权限拦截器拦截到请求,className:{},methodName:{}",className,methodName);
            //如果是拦截到登录请求，不打印参数，因为参数里面有密码，全部会打印到日志中，防止日志泄露
            return true;
        }

        log.info("权限拦截器拦截到请求,className:{},methodName:{},param:{}",className,methodName,requestParamBuffer.toString());


        User user = null;

        String loginToken = CookieUtil.readLoginToken(request);
        if(StringUtils.isNotEmpty(loginToken)){
            String userJsonStr = RedisShardedPoolUtil.get(loginToken);
            user = JsonUtil.string2Obj(userJsonStr,User.class);
        }

        if(user == null || (user.getRole().intValue() != Const.Role.ROLE_ADMIN)){
            //返回false.即不会调用controller里的方法
            response.reset();//geelynote 这里要添加reset，否则报异常 getWriter() has already been called for this response.
            response.setCharacterEncoding("UTF-8");//geelynote 这里要设置编码，否则会乱码
            response.setContentType("application/json;charset=UTF-8");//geelynote 这里要设置返回值的类型，因为全部是json接口。

            PrintWriter out = response.getWriter();

            //上传由于富文本的控件要求，要特殊处理返回值，这里面区分是否登录以及是否有权限
            if(user == null){
                if(StringUtils.equals(className,"ProductManageController") && StringUtils.equals(methodName,"richtextImgUpload")){
                    Map resultMap = Maps.newHashMap();
                    resultMap.put("success",false);
                    resultMap.put("msg","请登录管理员");
                    out.print(JsonUtil.obj2String(resultMap));
                }else{
                    out.print(JsonUtil.obj2String(ServerResponse.createByErrorMessage("拦截器拦截,用户未登录")));
                }
            }else{
                if(StringUtils.equals(className,"ProductManageController") && StringUtils.equals(methodName,"richtextImgUpload")){
                    Map resultMap = Maps.newHashMap();
                    resultMap.put("success",false);
                    resultMap.put("msg","无权限操作");
                    out.print(JsonUtil.obj2String(resultMap));
                }else{
                    out.print(JsonUtil.obj2String(ServerResponse.createByErrorMessage("拦截器拦截,用户无权限操作")));
                }
            }
            out.flush();
            out.close();//geelynote 这里要关闭

            return false;

        }
        return true;
    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        log.info("postHandle");
    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        log.info("afterCompletion");
    }
}

```

> ### 登录死循环 ###

描述：`springmvc`配置拦截器，如果登录请求也拦截的话，会导致一直登录不上，陷入死循环当中

解决：
方式一：可以在配置拦截器的时候过滤掉登录请求`<mvc:exclude-mapping path="/manage/user/login.do"/>`

方式二：拦截器处理类的`preHandle`方法会在到达`controller`调用该方法,因此可以在该方法中过滤掉登录请求不拦截

```java
if(StringUtils.equals(className,"UserManageController") && StringUtils.equals(methodName,"login")){
    log.info("权限拦截器拦截到请求,className:{},methodName:{}",className,methodName);
    //如果是拦截到登录请求，不打印参数，因为参数里面有密码，全部会打印到日志中，防止日志泄露
    return true;
}

```

> ### 重置响应对象 ###

描述：拦截器处理类的三个方法都是返回布尔值，而`controller`都是返回`json`数据，请求如果被拦截到没有到达`controller`，那么在拦截器处理类的`preHandle`方法中必须重置响应对象

解决：

```java
if(user == null || (user.getRole().intValue() != Const.Role.ROLE_ADMIN)){
    //返回false.即不会调用controller里的方法
    response.reset();//geelynote 这里要添加reset，否则报异常 getWriter() has already been called for this response.
    response.setCharacterEncoding("UTF-8");//geelynote 这里要设置编码，否则会乱码
    response.setContentType("application/json;charset=UTF-8");//geelynote 这里要设置返回值的类型，因为全部是json接口。

    PrintWriter out = response.getWriter();

    //上传由于富文本的控件要求，要特殊处理返回值，这里面区分是否登录以及是否有权限
    if(user == null){
        if(StringUtils.equals(className,"ProductManageController") && StringUtils.equals(methodName,"richtextImgUpload")){
            Map resultMap = Maps.newHashMap();
            resultMap.put("success",false);
            resultMap.put("msg","请登录管理员");
            out.print(JsonUtil.obj2String(resultMap));
        }else{
            out.print(JsonUtil.obj2String(ServerResponse.createByErrorMessage("拦截器拦截,用户未登录")));
        }
    }else{
        if(StringUtils.equals(className,"ProductManageController") && StringUtils.equals(methodName,"richtextImgUpload")){
            Map resultMap = Maps.newHashMap();
            resultMap.put("success",false);
            resultMap.put("msg","无权限操作");
            out.print(JsonUtil.obj2String(resultMap));
        }else{
            out.print(JsonUtil.obj2String(ServerResponse.createByErrorMessage("拦截器拦截,用户无权限操作")));
        }
    }
    out.flush();
    out.close();//geelynote 这里要关闭

    return false;

}
```

> ## SpringMVC RESTful改造 ##

> ### 编辑`web.xml` ###

修改前

```xml
<servlet>
    <servlet-name>dispatcher</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <load-on-startup>1</load-on-startup>
</servlet>

<servlet-mapping>
    <servlet-name>dispatcher</servlet-name>
    <url-pattern>*.do</url-pattern>
</servlet-mapping>

```

修改后

```xml
<servlet>
    <servlet-name>dispatcher</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <load-on-startup>1</load-on-startup>
</servlet>

<servlet-mapping>
    <servlet-name>dispatcher</servlet-name>
    <url-pattern>/</url-pattern>
</servlet-mapping>

```

> ### controller使用RESTful风格 ###

> #### 根据id查询产品 ####

修改前

```java
@RequestMapping("detail.do")
@ResponseBody
public ServerResponse<ProductDetailVo> detail(Integer productId){
    return iProductService.getProductDetail(productId);
}

```
修改后

```java
@RequestMapping(value = "/{productId}", method = RequestMethod.GET)
@ResponseBody
public ServerResponse<ProductDetailVo> detailRESTful(@PathVariable Integer productId){
    return iProductService.getProductDetail(productId);
}

```

> #### 搜索产品 ####

> ##### `keyword、categoryId`不为空 #####

修改前

```java
@RequestMapping("list.do")
@ResponseBody
public ServerResponse<PageInfo> list(@RequestParam(value = "keyword",required = false)String keyword,
                                     @RequestParam(value = "categoryId",required = false)Integer categoryId,
                                     @RequestParam(value = "pageNum",defaultValue = "1") int pageNum,
                                     @RequestParam(value = "pageSize",defaultValue = "10") int pageSize,
                                     @RequestParam(value = "orderBy",defaultValue = "") String orderBy){
    return iProductService.getProductByKeywordCategory(keyword,categoryId,pageNum,pageSize,orderBy);
}

```

修改后

```java
//http://www.happymmall.com/product/手机/100012/1/10/price_asc
@RequestMapping(value = "/{keyword}/{categoryId}/{pageNum}/{pageSize}/{orderBy}",method = RequestMethod.GET)
@ResponseBody
public ServerResponse<PageInfo> listRESTful(@PathVariable(value = "keyword")String keyword,
                                     @PathVariable(value = "categoryId")Integer categoryId,
                                     @PathVariable(value = "pageNum") Integer pageNum,
                                     @PathVariable(value = "pageSize") Integer pageSize,
                                     @PathVariable(value = "orderBy") String orderBy){
    if(pageNum == null){
        pageNum = 1;
    }
    if(pageSize == null){
        pageSize = 10;
    }
    if(StringUtils.isBlank(orderBy)){
        orderBy = "price_asc";
    }

    return iProductService.getProductByKeywordCategory(keyword,categoryId,pageNum,pageSize,orderBy);
}

```

> ##### `keyword、categoryId`有一个为空 #####

> ###### 修改后版本一 ######

`keyword`为空

```java
//    http://www.happymmall.com/product/100012/1/10/price_asc
@RequestMapping(value = "/{categoryId}/{pageNum}/{pageSize}/{orderBy}",method = RequestMethod.GET)
@ResponseBody
public ServerResponse<PageInfo> listRESTfulBadcase(@PathVariable(value = "categoryId")Integer categoryId,
                                            @PathVariable(value = "pageNum") Integer pageNum,
                                            @PathVariable(value = "pageSize") Integer pageSize,
                                            @PathVariable(value = "orderBy") String orderBy){
    if(pageNum == null){
        pageNum = 1;
    }
    if(pageSize == null){
        pageSize = 10;
    }
    if(StringUtils.isBlank(orderBy)){
        orderBy = "price_asc";
    }

    return iProductService.getProductByKeywordCategory("",categoryId,pageNum,pageSize,orderBy);
}
```

`categoryId`为空

```java
@RequestMapping(value = "/{keyword}/{pageNum}/{pageSize}/{orderBy}",method = RequestMethod.GET)
@ResponseBody
public ServerResponse<PageInfo> listRESTfulBadcase(@PathVariable(value = "keyword")String keyword,
                                            @PathVariable(value = "pageNum") Integer pageNum,
                                            @PathVariable(value = "pageSize") Integer pageSize,
                                            @PathVariable(value = "orderBy") String orderBy){
    if(pageNum == null){
        pageNum = 1;
    }
    if(pageSize == null){
        pageSize = 10;
    }
    if(StringUtils.isBlank(orderBy)){
        orderBy = "price_asc";
    }

    return iProductService.getProductByKeywordCategory(keyword,null,pageNum,pageSize,orderBy);
}

```

浏览器请求http://localhost:8088/mmall_war_exploded/product/100012/1/10/price_asc

![](controller使用RESTful风格.png)

发生了歧义，不知道要走哪一个方法，所以报错了


> ###### 修改后版本二 ######

`categoryId`为空

```java
//http://www.happymmall.com/product/keyword/手机/1/10/price_asc
@RequestMapping(value = "/keyword/{keyword}/{pageNum}/{pageSize}/{orderBy}",method = RequestMethod.GET)
@ResponseBody
public ServerResponse<PageInfo> listRESTful(@PathVariable(value = "keyword")String keyword,
                                                   @PathVariable(value = "pageNum") Integer pageNum,
                                                   @PathVariable(value = "pageSize") Integer pageSize,
                                                   @PathVariable(value = "orderBy") String orderBy){
    if(pageNum == null){
        pageNum = 1;
    }
    if(pageSize == null){
        pageSize = 10;
    }
    if(StringUtils.isBlank(orderBy)){
        orderBy = "price_asc";
    }

    return iProductService.getProductByKeywordCategory(keyword,null,pageNum,pageSize,orderBy);
}

```

`keyword`为空

```java
//http://www.happymmall.com/product/category/100012/1/10/price_asc
@RequestMapping(value = "/category/{categoryId}/{pageNum}/{pageSize}/{orderBy}",method = RequestMethod.GET)
@ResponseBody
public ServerResponse<PageInfo> listRESTful(@PathVariable(value = "categoryId")Integer categoryId,
                                                   @PathVariable(value = "pageNum") Integer pageNum,
                                                   @PathVariable(value = "pageSize") Integer pageSize,
                                                   @PathVariable(value = "orderBy") String orderBy){
    if(pageNum == null){
        pageNum = 1;
    }
    if(pageSize == null){
        pageSize = 10;
    }
    if(StringUtils.isBlank(orderBy)){
        orderBy = "price_asc";
    }

    return iProductService.getProductByKeywordCategory("",categoryId,pageNum,pageSize,orderBy);
}
```

浏览器访问`http://localhost:8088/mmall_war_exploded/product/keyword/手机/1/10/price_asc`和`http://localhost:8088/mmall_war_exploded/product/category/100002/1/10/price_asc`

这样子就可以避免歧义

> ## Spring Schedul定时任务 ##

> ### Cron生成器 ###

[在线Cron表达式生成器](http://cron.qqe2.com/)

> ### 定时任务配置 ###

注解方式配置定时任务

> #### 编辑spring配置文件 ####

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans
xmlns:task="http://www.springframework.org/schema/task"
xsi:schemaLocation="http://www.springframework.org/schema/task
     http://www.springframework.org/schema/task/spring-task.xsd"

<task:annotation-driven/>
</beans>

```

> #### 创建定时任务类 ####

```java
package com.mmall.task;

import com.mmall.service.IOrderService;
import com.mmall.util.PropertiesUtil;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.scheduling.annotation.Scheduled;
import org.springframework.stereotype.Component;

/**
 * Created by geely
 */
@Component
@Slf4j
public class CloseOrderTask {

    @Autowired
    private IOrderService iOrderService;

    //每隔5秒执行一次
    @Scheduled(cron="*/5 * * * * ?")
    public void closeOrderTaskV1(){
        log.info("关闭订单定时任务启动");
        int hour = Integer.parseInt(PropertiesUtil.getProperty("close.order.task.time.hour","2"));
//        iOrderService.closeOrder(hour);
        log.info("关闭订单定时任务结束");
    }
}

```

> ## MySQL行锁、表锁 ##

> ### 行锁 ###

> #### 明确的主键 ####

> ##### 明确指定主键`id`，并且有结果集,产生行锁 #####

```sql
SELECT
	*
FROM
	mmall_product
WHERE
	id = '26' FOR UPDATE;

```

> ##### 明确指定主键`id`，并且无结果集,无锁 #####

```sql
SELECT
	*
FROM
	mmall_product
WHERE
	id = '66' FOR UPDATE;

```

> ### 表锁 ###

> #### 无明确的主键 ####

> ##### 无主键,产生表锁 #####

```sql
SELECT
	*
FROM
	mmall_product
WHERE
	NAME = 'Apple iPhone 7 Plus (A1661) 128G 玫瑰金色 移动联通电信4G手机' FOR UPDATE;
```

> ##### 主键不明确产生表锁 #####

```sql
SELECT
	*
FROM
	mmall_product
WHERE
	id <> '66' FOR UPDATE;

```

```sql
SELECT
	*
FROM
	mmall_product
WHERE
	id LIKE '66' FOR UPDATE;

```

> ### 使用 ###

关单:查询订单的时候，订单包含了子订单，根据子订单号查询产品

```xml
<select id="selectStockByProductId" resultType="int" parameterType="java.lang.Integer">
select
stock
from mmall_product
where id = #{id}
for update
</select>

```

> ### xml转义 ###

用`<![CDATA[]]>`包裹住有转义的字符即可

```xml
<select id="selectOrderStatusByCreateTime" resultMap="BaseResultMap" parameterType="map">
SELECT
<include refid="Base_Column_List"/>
from mmall_order
where status = #{status}
<![CDATA[
and create_time <= #{date}
]]>
order by create_time desc
</select>

```

> ## 原生分布式锁 ##

Spring Schedule+Redis分布式锁构建分布式任务调度

> ### 简单版 ###

获取到锁，锁住时间5秒，如果此期间发生中断，会导致死锁

```java
//    @Scheduled(cron="0 */1 * * * ?")
    public void closeOrderTaskV2(){
        log.info("关闭订单定时任务启动");
        long lockTimeout = Long.parseLong(PropertiesUtil.getProperty("lock.timeout","5000"));

        Long setnxResult = RedisShardedPoolUtil.setnx(Const.REDIS_LOCK.CLOSE_ORDER_TASK_LOCK,String.valueOf(System.currentTimeMillis()+lockTimeout));
        if(setnxResult != null && setnxResult.intValue() == 1){
            //如果返回值是1，代表设置成功，获取锁
            closeOrder(Const.REDIS_LOCK.CLOSE_ORDER_TASK_LOCK);
        }else{
            log.info("没有获得分布式锁:{}",Const.REDIS_LOCK.CLOSE_ORDER_TASK_LOCK);
        }
        log.info("关闭订单定时任务结束");
    }

    private void closeOrder(String lockName){
        //有效期50秒，防止死锁
        RedisShardedPoolUtil.expire(lockName,5);
        log.info("获取{},ThreadName:{}",Const.REDIS_LOCK.CLOSE_ORDER_TASK_LOCK,Thread.currentThread().getName());
        int hour = Integer.parseInt(PropertiesUtil.getProperty("close.order.task.time.hour","2"));
        iOrderService.closeOrder(hour);
        RedisShardedPoolUtil.del(Const.REDIS_LOCK.CLOSE_ORDER_TASK_LOCK);
        log.info("释放{},ThreadName:{}",Const.REDIS_LOCK.CLOSE_ORDER_TASK_LOCK,Thread.currentThread().getName());
        log.info("===============================");
    }


```

> ### 安全版 ###

双重防死锁

未获取到锁，继续判断，判断时间戳，看是否可以重置并获取到锁

```java
    @Scheduled(cron="0 */1 * * * ?")
    public void closeOrderTaskV3(){
        log.info("关闭订单定时任务启动");
        long lockTimeout = Long.parseLong(PropertiesUtil.getProperty("lock.timeout","5000"));
        Long setnxResult = RedisShardedPoolUtil.setnx(Const.REDIS_LOCK.CLOSE_ORDER_TASK_LOCK,String.valueOf(System.currentTimeMillis()+lockTimeout));
        if(setnxResult != null && setnxResult.intValue() == 1){
            closeOrder(Const.REDIS_LOCK.CLOSE_ORDER_TASK_LOCK);
        }else{
            //未获取到锁，继续判断，判断时间戳，看是否可以重置并获取到锁
            String lockValueStr = RedisShardedPoolUtil.get(Const.REDIS_LOCK.CLOSE_ORDER_TASK_LOCK);
            if(lockValueStr != null && System.currentTimeMillis() > Long.parseLong(lockValueStr)){
                String getSetResult = RedisShardedPoolUtil.getSet(Const.REDIS_LOCK.CLOSE_ORDER_TASK_LOCK,String.valueOf(System.currentTimeMillis()+lockTimeout));
                //再次用当前时间戳getset。
                //返回给定的key的旧值，->旧值判断，是否可以获取锁
                //当key没有旧值时，即key不存在时，返回nil ->获取锁
                //这里我们set了一个新的value值，获取旧的值。
                if(getSetResult == null || (getSetResult != null && StringUtils.equals(lockValueStr,getSetResult))){
                    //真正获取到锁
                    closeOrder(Const.REDIS_LOCK.CLOSE_ORDER_TASK_LOCK);
                }else{
                    log.info("没有获取到分布式锁:{}",Const.REDIS_LOCK.CLOSE_ORDER_TASK_LOCK);
                }
            }else{
                log.info("没有获取到分布式锁:{}",Const.REDIS_LOCK.CLOSE_ORDER_TASK_LOCK);
            }
        }
        log.info("关闭订单定时任务结束");
    }

    private void closeOrder(String lockName){
        //有效期50秒，防止死锁
        RedisShardedPoolUtil.expire(lockName,5);
        log.info("获取{},ThreadName:{}",Const.REDIS_LOCK.CLOSE_ORDER_TASK_LOCK,Thread.currentThread().getName());
        int hour = Integer.parseInt(PropertiesUtil.getProperty("close.order.task.time.hour","2"));
        iOrderService.closeOrder(hour);
        RedisShardedPoolUtil.del(Const.REDIS_LOCK.CLOSE_ORDER_TASK_LOCK);
        log.info("释放{},ThreadName:{}",Const.REDIS_LOCK.CLOSE_ORDER_TASK_LOCK,Thread.currentThread().getName());
        log.info("===============================");
    }


```

> ## Redisson分布式锁 ##

> ### 编辑pom.xml ###

```xml
<dependency>
  <groupId>org.redisson</groupId>
  <artifactId>redisson</artifactId>
  <version>2.9.0</version>
</dependency>
<dependency>
  <groupId>com.fasterxml.jackson.dataformat</groupId>
  <artifactId>jackson-dataformat-avro</artifactId>
  <version>2.9.0</version>
</dependency>

```

> ### 使用Redisson分布式锁 ###

```java
//    @Scheduled(cron="0 */1 * * * ?")
    public void closeOrderTaskV4(){
        RLock lock = redissonManager.getRedisson().getLock(Const.REDIS_LOCK.CLOSE_ORDER_TASK_LOCK);
        boolean getLock = false;
        try {
            if(getLock = lock.tryLock(0,50, TimeUnit.SECONDS)){
                log.info("Redisson获取到分布式锁:{},ThreadName:{}",Const.REDIS_LOCK.CLOSE_ORDER_TASK_LOCK,Thread.currentThread().getName());
                int hour = Integer.parseInt(PropertiesUtil.getProperty("close.order.task.time.hour","2"));
//                iOrderService.closeOrder(hour);
            }else{
                log.info("Redisson没有获取到分布式锁:{},ThreadName:{}",Const.REDIS_LOCK.CLOSE_ORDER_TASK_LOCK,Thread.currentThread().getName());
            }
        } catch (InterruptedException e) {
            log.error("Redisson分布式锁获取异常",e);
        } finally {
            if(!getLock){
                return;
            }
            lock.unlock();
            log.info("Redisson分布式锁释放锁");
        }
    }

```


