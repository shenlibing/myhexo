---
title: ssh无密登录_集群分发脚本xsync
date: 2019-02-18 14:48:50
categories: 日常记录
tags:
 - Linux
 - ssh
 - scp
 - rsync
---

1、ssh免密登录

**ssh ip地址**

```
	[root@192 ~]# ssh 192.168.1.102
	root@192.168.1.102's password: 
	Last login: Mon Feb 18 20:40:28 2019 from 192.168.1.101

```

**免密登录配置**

生成公钥和私钥

```
	[root@192 ~]# ssh-keygen -t rsa 
	Generating public/private rsa key pair.
	Enter file in which to save the key (/root/.ssh/id_rsa): 
	Enter passphrase (empty for no passphrase): 
	Enter same passphrase again: 
	Your identification has been saved in /root/.ssh/id_rsa.
	Your public key has been saved in /root/.ssh/id_rsa.pub.
	The key fingerprint is:
	d4:95:6c:a8:21:9b:27:62:79:43:76:e8:4b:32:6c:fe root@192.168.1.101
	The key's randomart image is:
	+--[ RSA 2048]----+
	|       .   o..   |
	|      = o...+    |
	|   . = =.o..     |
	|    O O.o        |
	|   + * =S        |
	|    . .          |
	|     .           |
	|      E          |
	|                 |
	+-----------------+
	[root@192 ~]# ls /root/.ssh/
	id_rsa  id_rsa.pub


```

将公钥拷贝到要免密登录的目标机器上

> .ssh文件夹下（~/.ssh）的文件功能解释
（1）known_hosts	：记录ssh访问过计算机的公钥(public key)
（2）id_rsa	：生成的私钥
（3）id_rsa.pub	：生成的公钥
（4）authorized_keys	：存放授权过得无密登录服务器公钥


```

	[root@192 ~]# hostname 
	192.168.1.101
	[root@192 ~]# ssh-copy-id 192.168.1.102
	The authenticity of host '192.168.1.102 (192.168.1.102)' can't be established.
	RSA key fingerprint is 56:57:4c:81:94:e0:47:fe:1e:aa:8c:9c:2a:87:a6:dc.
	Are you sure you want to continue connecting (yes/no)? yes
	Warning: Permanently added '192.168.1.102' (RSA) to the list of known hosts.
	root@192.168.1.102's password: 
	Now try logging into the machine, with "ssh '192.168.1.102'", and check in:
	
	  .ssh/authorized_keys
	
	to make sure we haven't added extra keys that you weren't expecting.
	
	[root@192 ~]# ls /root/.ssh/
	id_rsa  id_rsa.pub  known_hosts
	[root@192 ~]# ssh 192.168.1.102
	Last login: Mon Feb 18 22:50:53 2019 from 192.168.1.101
	[root@192 ~]# ls /root/.ssh/
	authorized_keys

```

2、集群分发脚本xsync

服务器与服务器数据拷贝

> rsync和scp区别：用rsync做文件的复制要比scp的速度快，rsync只对差异文件做更新。scp是把所有文件都复制过去。

**scp**

```
	[root@192 ~]# scp -r /tmp/1.txt  root@192.168.1.102:/tmp/
	1.txt   

```

**rsync**

> 选项	功能
-r	递归
-v	显示复制过程
-l	拷贝符号连接

```
	[root@192 ~]# rsync -rvl /tmp/2.txt root@192.168.1.102:/tmp/
	sending incremental file list
	2.txt
	
	sent 84 bytes  received 31 bytes  76.67 bytes/sec
	total size is 13  speedup is 0.11


```

**集群分发脚本xsync**

```
[root@192 ~]# mkdir bin
[root@192 ~]# cd bin/
[root@192 bin]# touch xsync
[root@192 bin]# vim xsync
[root@192 bin]# chmod 777 xsync 

```

```shell
	#!/bin/bash
	#1 获取输入参数个数，如果没有参数，直接退出
	pcount=$#
	if((pcount==0)); then
	echo no args;
	exit;
	fi
	
	#2 获取文件名称
	p1=$1
	fname=`basename $p1`
	echo fname=$fname
	
	#3 获取上级目录到绝对路径
	pdir=`cd -P $(dirname $p1); pwd`
	echo pdir=$pdir
	
	#4 获取当前用户名称
	user=`whoami`
	
	#5 循环
	for((host=102; host<104; host++)); do
	        echo --------------------- 192.168.1.$host ----------------
	        rsync -rvl $pdir/$fname $user@192.168.1.$host:$pdir
	        #echo  ------"rsync -rvl $pdir/$fname $user@192.168.1.$host:$pdir complete"------------
	
	
	done

```

**xsync调用**

```
	[root@192 bin]# xsync /tmp/1.txt /tmp/
	fname=1.txt
	pdir=/tmp
	--------------------- 192.168.1.102 ----------------
	sending incremental file list
	1.txt
	
	sent 516 bytes  received 31 bytes  364.67 bytes/sec
	total size is 445  speedup is 0.81
	--------------------- 192.168.1.103 ----------------
	The authenticity of host '192.168.1.103 (192.168.1.103)' can't be established.
	RSA key fingerprint is 56:57:4c:81:94:e0:47:fe:1e:aa:8c:9c:2a:87:a6:dc.
	Are you sure you want to continue connecting (yes/no)? yes
	Warning: Permanently added '192.168.1.103' (RSA) to the list of known hosts.
	root@192.168.1.103's password: 
	sending incremental file list
	1.txt
	
	sent 71 bytes  received 37 bytes  5.84 bytes/sec
	total size is 445  speedup is 4.12


```