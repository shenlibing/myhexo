---
title: hexo博客搭建
date: 2019-01-23 09:07:30
categories: 日常记录
tags: 
  - hexo
  - git
  - github
  - node
  - markdown
---

1、安装Node.js

[https://nodejs.org/en/](https://nodejs.org/en/)


2、安装Git

[https://git-scm.com/](https://git-scm.com/)


3、安装Hexo

```
	//全局安装
	$ npm install -g hexo-cli

```

存放位置

``` 
	C:\Users\libingshen\AppData\Roaming\npm\node_modules

```

验证安装是否成功

```
	C:\Users\libingshen>node --version
	v10.15.0
	
	C:\Users\libingshen>npm --version
	6.4.1
	
	C:\Users\libingshen>git --version
	git version 2.16.2.windows.1
	
	C:\Users\libingshen>hexo --version
	hexo-cli: 1.1.0
	os: Windows_NT 10.0.17134 win32 x64
	http_parser: 2.8.0
	node: 10.15.0
	v8: 6.8.275.32-node.45
	uv: 1.23.2
	zlib: 1.2.11
	ares: 1.15.0
	modules: 64
	nghttp2: 1.34.0
	napi: 3
	openssl: 1.1.0j
	icu: 62.1
	unicode: 11.0
	cldr: 33.1
	tz: 2018e

```


4、Hexo博客初始化

```
	$ cd D:\mytest\myhexo
	$ hexo init

	//安装依赖
	$ npm install

	//或者hexo generate生成静态页
	$ hexo g 

	//或者hexo server，启动服务器，可以在http://localhost:4000/ 查看
	$ hexo s 

```


5、Hexo博客部署到GitHub

1）本地客户端与GitHub建立授权连接生成公私钥

```
    ssh-keygen -t rsa -C "782125244@qq.com"

```

公私钥存放位置

```
    C:\Users\libingshen\.ssh

```

将公钥内容上传到GitHub

![](https://i.imgur.com/o3BsfxD.png)


2)GitHub创建仓库作为hexo博客的站点


3）hexo配置GitHub连接

安装 hexo-deployer-git

```
    $ npm install hexo-deployer-git --save

```

修改配置D:\mytest\myhexo\_config.yml

```
	#配置GitHub博客站址访问路径
	# URL这里要是没有配置，GitHub样式显示不出来
	## If your site is put in a subdirectory, set url as 'http://yoursite.com/child' and root as '/child/'
	url: https://shenlibing.github.io/myhexo/
	root: /myhexo
	permalink: :year/:month/:day/:title/
	permalink_defaults:
	
	#博客主题样式
	# Extensions
	## Plugins: https://hexo.io/plugins/
	## Themes: https://hexo.io/themes/
	theme: hexo-theme-next-master

	#博客推送到GitHub地址配置
	# Deployment
	## Docs: https://hexo.io/docs/deployment.html
	deploy:
	  type: git
	  repo: https://github.com/shenlibing/myhexo.git
	  branch: [master]

```


6、备份博客源文件

hexo部署到GitHub只上传.deploy_git文件夹的内容，并不会上传_posts文件夹下的博客源文件

```
	D:\mytest\myhexo\.deploy_git
	
	D:\mytest\myhexo\source\_posts

```

解决：

将_posts文件夹作为git仓库，上传到GitHub，使得其作为另一个分支

创建并切换到新分支

```
	libingshen@DESKTOP-7V287SK MINGW64 /d/mytest/myhexo/source/_posts (master)
	$ git checkout -b src
	Switched to a new branch 'src'

```

将新分支推送到GitHub

```
	git add .
	git commit -m 'add_v1:-新增hello-world笔记初始化'
	git push origin src

```

将博客源文件分支设为推送的默认分支，git push推送直接推送到src分支

![](https://i.imgur.com/4glUdGW.png)


补充：
	
删除本地分支

```
    $ git branch -d src
```

删除远程分支

```
    $ git push origin -d src
```

切换分支

```
    $ git checkout master
```