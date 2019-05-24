---
title: hexo
date: 2019-01-23 09:07:30
categories: 日常记录
tags: 
  - hexo
  - github
typora-root-url: hexo/
---

# 安装Node.js #

[https://nodejs.org/en/](https://nodejs.org/en/)



# 安装Git #

[https://git-scm.com/](https://git-scm.com/)



# 安装Hexo #

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

# Hexo博客初始化 #

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

# Hexo博客部署到GitHub #

## 本地客户端与GitHub建立授权连接生成公私钥 ##

```
    ssh-keygen -t rsa -C "782125244@qq.com"

```

公私钥存放位置

```
    C:\Users\libingshen\.ssh

```

将公钥内容上传到GitHub

![](上传公钥到GitHub.png)

## GitHub创建仓库作为hexo博客的站点 ##



## hexo配置GitHub连接 ##

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

## 配置GitHub Pages ##

![](配置GitHub page.png)

# 备份博客源文件 #

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

![](设置上传默认分支.png)


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

# 拓展 #

## 更换hexo主题 ##

下载

[https://github.com/theme-next/hexo-theme-next](https://github.com/theme-next/hexo-theme-next)

解压放到D:\mytest\myhexo\themes

![](下载解压主题后存放位置.png)


修改`D:\mytest\myhexo\_config.yml`配置文件

```
	# Extensions
	## Plugins: https://hexo.io/plugins/
	## Themes: https://hexo.io/themes/
	theme: hexo-theme-next-master

```

## 修改站点语言 ##

编辑`D:\mytest\myhexo\_config.yml`文件

```
	# Site
	title: 小兵兵
	subtitle:
	description:
	keywords:
	author: shenlibing
	language: zh-CN
	timezone:

```

## 开启搜索、字数统计和阅读时长统计 ##

安装依赖hexo-symbols-count-time、hexo-generator-searchdb，安装依赖不成功时先删除D:\mytest\myhexo\node_modules文件夹，重新安装依赖

```
	
	npm install hexo-symbols-count-time --save

	npm install hexo-generator-searchdb --save

```

配置主题样式文件


进入`D:\mytest\myhexo\themes\hexo-theme-next-master\`文件夹，编辑_config.yml文件

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

编辑`D:\mytest\myhexo\_config.yml`文件

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

## 添加分类和标签 ##

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

## 更换页面菜单布局位置 ##

```
	# Schemes
	#scheme: Muse
	#scheme: Mist
	scheme: Pisces
	#scheme: Gemini

```

## 配置文章浏览量 ##

注册

[https://leancloud.cn/](https://leancloud.cn/)

创建应用

![](leancloud创建应用.png)

创建Class

![](创建Class.png)

配置web安全域名

![](配置web安全域名.png)



获取app_id、app_key

![](获取app_id和app_key.png)

编辑`D:\mytest\myhexo\themes\hexo-theme-next-master\_config.yml`配置文件，填app_id、

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

## 插入本地图片 ##

安装依赖

```
	npm install hexo-asset-image --save

```

编辑`D:\mytest\myhexo\_config.yml`配置文件

```
	post_asset_folder: true

```

命令hexo new post test来生成博文时，/source/_post文件夹中除了test.md外，还有一个同名test文件夹

## typora插入本地图片

### typora设置

文件--偏好设置--图片插入

![1558400020100](1558400020100.png)

编辑--图片工具--设置图片根目录

```
typora-root-url: hexo/
```

## 设置动态背景 ##

进入D:\mytest\myhexo\themes\hexo-theme-next-master\layout\文件夹，编辑_layout.swig文件，在`</body>`之前添加

```
	<script type="text/javascript" src="//cdn.bootcss.com/canvas-nest.js/1.0.0/canvas-nest.min.js"></script>

```

## 添加Fork me on GitHub ##

进入D:\mytest\myhexo\themes\hexo-theme-next-master\layout\文件夹，编辑_layout.swig文件，在
`<div class="headband"></div>`下面添加选择的[图标样式代码](http://tholman.com/github-corners/)

```
	<a href="https://github.com/shenlibing/" class="github-corner" aria-label="View source on GitHub"><svg width="80" height="80" viewBox="0 0 250 250" style="fill:#151513; color:#fff; position: absolute; top: 0; border: 0; right: 0;" aria-hidden="true"><path d="M0,0 L115,115 L130,115 L142,142 L250,250 L250,0 Z"></path><path d="M128.3,109.0 C113.8,99.7 119.0,89.6 119.0,89.6 C122.0,82.7 120.5,78.6 120.5,78.6 C119.2,72.0 123.4,76.3 123.4,76.3 C127.3,80.9 125.5,87.3 125.5,87.3 C122.9,97.6 130.6,101.9 134.4,103.2" fill="currentColor" style="transform-origin: 130px 106px;" class="octo-arm"></path><path d="M115.0,115.0 C114.9,115.1 118.7,116.5 119.8,115.4 L133.7,101.6 C136.9,99.2 139.9,98.4 142.2,98.6 C133.8,88.0 127.5,74.4 143.8,58.0 C148.5,53.4 154.0,51.2 159.7,51.0 C160.3,49.4 163.2,43.6 171.4,40.1 C171.4,40.1 176.1,42.5 178.8,56.2 C183.1,58.6 187.2,61.8 190.9,65.4 C194.5,69.0 197.7,73.2 200.1,77.6 C213.8,80.2 216.3,84.9 216.3,84.9 C212.7,93.1 206.9,96.0 205.4,96.6 C205.1,102.4 203.0,107.8 198.3,112.5 C181.9,128.9 168.3,122.5 157.7,114.1 C157.9,116.9 156.7,120.9 152.7,124.9 L141.0,136.5 C139.8,137.7 141.6,141.9 141.8,141.8 Z" fill="currentColor" class="octo-body"></path></svg></a><style>.github-corner:hover .octo-arm{animation:octocat-wave 560ms ease-in-out}@keyframes octocat-wave{0%,100%{transform:rotate(0)}20%,60%{transform:rotate(-25deg)}40%,80%{transform:rotate(10deg)}}@media (max-width:500px){.github-corner:hover .octo-arm{animation:none}.github-corner .octo-arm{animation:octocat-wave 560ms ease-in-out}}</style>

```

## 添加友情链接 ##

方式一
进入`D:\mytest\myhexo\themes\hexo-theme-next-master\layout\_partials`文件夹，在`footer.swig`文件末尾追加

```
 <div>友情链接：
  <a class="theme-link"  href="http://collect.w3ctrain.com/"> 前端收藏夹 </a><span>&nbsp;&nbsp;</span>
  <a class="theme-link" href="http://www.alloyteam.com/nav/"> Web前端导航 </a><span>&nbsp;&nbsp;</span>
  <a class="theme-link"  href="http://www.runoob.com/"> 菜鸟教程 </a><span>&nbsp;&nbsp;</span>
  <a class="theme-link" href="https://mccxj.github.io/"> 小毛的胡思乱想 </a><span>&nbsp;&nbsp;</span>
 </div>

```

方式二
进入`D:\mytest\myhexo\themes\hexo-theme-next-master`文件夹，编辑`_config.yml`文件

```
	# Blog rolls
	links_icon: link
	links_title: Links
	links_layout: block
	#links_layout: inline
	#links:
	  #Title: http://example.com
	links:
	  美团技术团队: https://tech.meituan.com/
	  百度FEX: http://fex.baidu.com/
	  淘宝FED: http://taobaofed.org/

```

## 设置阅读全文 ##

进入`D:\mytest\myhexo\themes\hexo-theme-next-master`文件夹，编辑`_config.yml`文件

```
	# Automatically Excerpt. Not recommend.
	# Use <!-- more --> in the post to control excerpt accurately.
	auto_excerpt:
	  enable: true
	  length: 150

```



# 参考：

[【持续更新】Github Pages + Hexo 博客搭建，Next主题个性化修改](https://www.lixint.me/hexo-blog.html)

[Hexo+NexT 打造一个炫酷博客](https://juejin.im/post/5bcd2d395188255c3b7dc1db)

[hexo史上最全搭建教程](https://blog.csdn.net/sinat_37781304/article/details/82729029)

[用 Typora 写 Hexo 博客 | zhangnew](https://zhangnew.com/Typora-Hexo.html)

[使用Typora+Hexo+Google Drive打造自己的云笔记 | 情起·而深](https://www.liu.app/2018/07/27/Hexo/%E4%BD%BF%E7%94%A8Typora+Hexo+Google%20Drive%E6%89%93%E9%80%A0%E8%87%AA%E5%B7%B1%E7%9A%84%E4%BA%91%E7%AC%94%E8%AE%B0/)

[我的云端硬盘 - Google 云端硬盘](https://drive.google.com/drive/my-drive)

