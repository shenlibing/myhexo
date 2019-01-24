---
title: hexo博客搭建
date: 2019-01-23 09:07:30
categories: 日常记录
tags: 
  - hexo
  - github
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


	#博客推送到GitHub地址配置
	# Deployment
	## Docs: https://hexo.io/docs/deployment.html
	deploy:
	  type: git
	  repo: https://github.com/shenlibing/myhexo.git
	  branch: [master]

```

4)配置GitHub Pages

![](https://i.imgur.com/qSFHbX3.png)

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

查看提交日志

```
	$ git log --oneline 

```

7、拓展

1)更换hexo主题

下载

[https://github.com/theme-next/hexo-theme-next](https://github.com/theme-next/hexo-theme-next)

解压放到D:\mytest\myhexo\themes

![](https://i.imgur.com/hfH4rEM.png)


修改D:\mytest\myhexo\_config.yml配置文件

```
	# Extensions
	## Plugins: https://hexo.io/plugins/
	## Themes: https://hexo.io/themes/
	theme: hexo-theme-next-master

```

2）开启搜索、字数统计和阅读时长统计

安装依赖hexo-symbols-count-time、hexo-generator-searchdb，安装依赖不成功时先删除D:\mytest\myhexo\node_modules文件夹，重新安装依赖

```
	
	npm install hexo-symbols-count-time --save

	npm install hexo-generator-searchdb --save

```

配置主题样式文件

D:\mytest\myhexo\themes\hexo-theme-next-master\_config.yml

```
	# Local search
	# Dependencies: https://github.com/theme-next/hexo-generator-searchdb
	local_search:
	  enable: true

	# Dependencies: https://github.com/theme-next/hexo-symbols-count-time
	symbols_count_time:
	  separated_meta: true
	  item_text_post: true
	  item_text_total: false
	  awl: 4
	  wpm: 275

```

配置站点文件

D:\mytest\myhexo\_config.yml

```
	#阅读时长和本文字数
	symbols_count_time:
	  symbols: true
	  time: true
	  total_symbols: true
	  total_time: true

	#搜索功能
	search:
	  path: search.xml
	  field: post
	  format: html
	  limit: 10000

```

3）添加分类和标签

```
	$ hexo new page categories

	$ hexo new page tags

```

编辑D:\mytest\myhexo\source\categories\index.md

```

	---
	title: categories
	date: 2019-01-22 16:37:58
	type: "categories"   #这部分是新添加的
	---

```

编辑D:\mytest\myhexo\source\tags\index.md

```

	---
	title: tags
	date: 2019-01-22 16:38:40
	type: "tags" #新添加的内容
	---

```

修改菜单，添加categories和tags到menu中

```
	menu:
	  home: / || home
	  #about: /about/ || user
	  tags: /tags/ || tags
	  categories: /categories/ || th
	  archives: /archives/ || archive
	  #schedule: /schedule/ || calendar
	  #sitemap: /sitemap.xml || sitemap
	  #commonweal: /404/ || heartbeat

```

新增文章，添加categories、tags

```

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

```

4）更换页面菜单布局位置

```
	# Schemes
	#scheme: Muse
	#scheme: Mist
	scheme: Pisces
	#scheme: Gemini

```

5）配置文章浏览量

注册

[https://leancloud.cn/](https://leancloud.cn/)

创建应用

![](https://i.imgur.com/NHal0S7.png)

创建Class

![](https://i.imgur.com/eXGym94.png)

配置web安全域名

![](https://i.imgur.com/FEbZt23.png)



获取app_id、app_key

![](https://i.imgur.com/SE6pqQj.png)

编辑D:\mytest\myhexo\themes\hexo-theme-next-master\_config.yml配置文件，填app_id、

app_key;security设置为false

```

	# Show number of visitors to each article.
	# You can visit https://leancloud.cn get AppID and AppKey.
	leancloud_visitors:
	  enable: true
	  app_id: BedsOkBLOBR4nM4W52xTcBhb-gzGzoHsz #<app_id>
	  app_key: EEaRfb6dCMaS38laDCkSYhM9 #<app_key>
	  # Dependencies: https://github.com/theme-next/hexo-leancloud-counter-security
	  # If you don't care about security in leancloud counter and just want to use it directly
	  # (without hexo-leancloud-counter-security plugin), set `security` to `false`.
	  security: false
	  betterPerformance: false

```

参考：

[【持续更新】Github Pages + Hexo 博客搭建，Next主题个性化修改](https://www.lixint.me/hexo-blog.html)

[hexo史上最全搭建教程](https://blog.csdn.net/sinat_37781304/article/details/82729029)