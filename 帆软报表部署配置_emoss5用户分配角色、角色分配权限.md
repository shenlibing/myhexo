
---
title: 帆软报表部署配置_emoss5用户分配角色、角色分配权限
date: 2019-01-12 12:58:55
categories: 日常记录
tags:
	-	emoss
	- finereport
---

1、帆软报表部署

客户端工具sftp下载链接

[https://www.netsarang.com/download/down_form.html?code=623](https://www.netsarang.com/download/down_form.html?code=623)

使用sftp

[https://www.netsarang.com/download/down_xfp6.html?token=dEFiMWxoUEhuNU1hQlMrRG9qU20yZ0Bnc1hKVGJhaHpkY3hBUUNMNFBoaXVn](https://www.netsarang.com/download/down_xfp6.html?token=dEFiMWxoUEhuNU1hQlMrRG9qU20yZ0Bnc1hKVGJhaHpkY3hBUUNMNFBoaXVn)


1）用客户端工具sftp登录到135.125.60.122机子

文件--新建

![](https://i.imgur.com/2BmJwbi.png)

![](https://i.imgur.com/v4wyabN.png)


报表存放路径

```

	/opt/FineBI/webapps/WebReport/WEB-INF/reportlets

```


2）菜单配置

基础应用--权限模型--菜单管理

![](https://i.imgur.com/wIRwxey.png)

建立菜单层级关系

在资源评价菜单下-建立杜邦报表分析文件夹--建立2018杜邦报表3层目录结构

在2018杜邦报表(1)下配置报表位置引用--填写**菜单编码(表名)、排序号、菜单名称(表名中文描述)、菜单URL**

![](https://i.imgur.com/OpkMozW.png)


菜单URL的配置

![](https://i.imgur.com/sWq1soL.png)

![](https://i.imgur.com/dFluLNT.png)



2、建员工、建用户、为用户分配角色、为角色分配权限

1）建员工--基础应用--权限模型--员工管理

![](https://i.imgur.com/3Dy5jZ7.png)


2）建用户--基础应用--权限模型--用户管理--新增

![](https://i.imgur.com/P72nX6J.png)

![](https://i.imgur.com/m5MJU1X.png)

**新建用户之前必须先建员工、角色、部门，在建用户的时候可以为该用户分配角色和菜单权限**

![](https://i.imgur.com/foNIVta.png)

3）为用户分配角色--基础应用--权限模型--角色用户设置

![](https://i.imgur.com/aXEslSF.png)

先选中角色，然后为该角色添加用户

![](https://i.imgur.com/OTsxbsS.png)

4）为角色分配权限--基础应用--权限模型--角色管理

![](https://i.imgur.com/DzlEsPW.png)

先选中角色，然后为该角色勾选拥有的菜单权限

![](https://i.imgur.com/ssEptko.png)
