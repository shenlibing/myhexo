
---
title: svn版本控制
date: 2019-01-09 12:58:55
tags:
---

1、svn下载

[https://sourceforge.net/projects/win32svn/](https://sourceforge.net/projects/win32svn/)

2、验证是否安装成功

```

	C:\Users\libingshen>svn --version

```

3、创建版本库

```

	D:\mytest\mysvn\OA>svnadmin create D:\mytest\mysvn\OA

```
![](https://i.imgur.com/v4aUp1n.png)


4、启动svn服务

![](https://i.imgur.com/CStIBx9.png)

5、验证svn服务是否启动

svn服务监听3690端口

![](https://i.imgur.com/sogDN9t.png)

6、svn注册为Windows服务

tip:等号左边没有空格，等号右边有一个空格。

```

	C:\WINDOWS\system32>sc create MySVNService binpath= "C:\Pmyprogram\svn\bin\svnserve.exe --service -r D:\mytest\mysvn" start= auto depend= Tcpip

```

原因：每次启动svn服务时必须启动一个cmd窗口，cmd窗口一关闭，svn服务就关闭。

非管理员运行时会失败。
![](https://i.imgur.com/rPliKES.png)

管理员运行
![](https://i.imgur.com/gBmdKhJ.png)

![](https://i.imgur.com/whIFJo4.png)


启动、停止、删除svn服务（管理员身份运行cmd）

```

	//启动svn服务
	C:\WINDOWS\system32>sc start MySVNService

	//停止服务
	C:\WINDOWS\system32>sc stop MySVNService

	//删除服务
	C:\WINDOWS\system32>sc delete MySVNService


```

7、检出项目

```

	D:\mytest\mycheckout>svn checkout svn://localhost/OA MyOA
	
```

![](https://i.imgur.com/vvRuF4T.png)

8、提交文件

--开启匿名权限访问

![](https://i.imgur.com/hBW5DuW.png)

--先将文件加入版本库，然后提交（需添加提交日志信息，不然报错）

svn commit 命令最后可以不指定具体文件，此时表示提交当前工作副本中
的所有修改

![](https://i.imgur.com/r1IpUsZ.png)

9、更新

另一个客户端检出项目、更新并提交文件

```

	//远程版本库具体位置  svn://localhost/OA
	//将OA检出到本地的目录 MyOA2
	D:\mytest\mycheckout>svn checkout svn://localhost/OA MyOA2

```

![](https://i.imgur.com/u4zFEcf.png)

![](https://i.imgur.com/67qMr6A.png)


10、授权访问版本库

--单版本库开启授权访问

![](https://i.imgur.com/emwZBDY.png)

--多版本库开启授权访问

```

	在版本库根目录 D:\mytest\mysvn 下创建 commConf 目录
	将未修改的 authz 和 passwd 文件拷贝到 commConf 目录下
	修改需要设置权限的版本库的 svnserve.conf 文件
	①password-db = ../../commConf/passwd
	②authz-db = ../../commConf/authz

```
![](https://i.imgur.com/QjqreHq.png)

passwd：设置访问版本库的用户信息

![](https://i.imgur.com/7TQZlnl.png)

authz：设置用户访问版本库的权限

![](https://i.imgur.com/XIZ9z9v.png)

---