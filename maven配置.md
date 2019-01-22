
---
title: maven配置
date: 2019-01-22 12:58:55
tags:
---

1、下载：

https://maven.apache.org/download.cgi

![](https://i.imgur.com/S3Dxltk.png)

2、配置path环境变量

![](https://i.imgur.com/rIATqaf.png)

3、验证安装是否成功

![](https://i.imgur.com/4ejsorP.png)

4、配置本地仓库、阿里镜像、jdk编译版本

配置本地仓库位置

![](https://i.imgur.com/52z9nU5.png)

```

	<localRepository>C:\greensoft\apache-maven-3.5.2\repository</localRepository>

```


配置jar包下载阿里远程镜像

![](https://i.imgur.com/JIiS5Ss.png)

```

	<mirror>      
	  <id>aliyun-maven</id>    
	  <name>aliyun-maven</name>  
	  <url>http://maven.aliyun.com/nexus/content/groups/public</url>    
	  <mirrorOf>central</mirrorOf>      
	</mirror>  

```



配置jdk编译版本

![](https://i.imgur.com/jOArlWO.png)

```
			
	<profile>
		<id>jdk-1.7</id>
		<activation>
			<activeByDefault>true</activeByDefault>
			<jdk>1.7</jdk>
		</activation>
		<properties>
			<maven.compiler.source>1.7</maven.compiler.source>
			<maven.compiler.target>1.7</maven.compiler.target>
			<maven.compiler.compilerVersion>1.7</maven.compiler.compilerVersion>
		</properties>
	</profile>

```


5、eclipse整合maven

使用自己的maven安装包

![](https://i.imgur.com/Uf2A5ql.png)

![](https://i.imgur.com/DelPSwV.png)


6、IDEA整合maven

![](https://i.imgur.com/G2sHbyp.png)