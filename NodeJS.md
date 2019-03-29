---
title: NodeJS
date: 2019-03-20 10:02:01
categories:
tags:
 - NodeJS
---

> ## NodeJS入门 ##

> ### NodeJS模块 ###

> #### http模块 ####

server.js

```js
const http=require('http');

let server=http.createServer((req, res)=>{
  switch(req.url){
    case '/aaa':
      res.write('abc');
      break;
    case '/bbb':
      res.write('dddd');
      break;
    case '/1.html':
      res.write('<html><head></head><body>sdfasfasf</body></html>');
      break;
  }

  res.end();
});
server.listen(8080);

```

server2.js

```js
const http=require('http');
const fs=require('fs');

let server=http.createServer((req, res)=>{
  fs.readFile(`www${req.url}`, (err, data)=>{
    if(err){
      res.write('404');     //?
    }else{
      res.write(data);
    }
    res.end();
  });
});
server.listen(8080);

```

> #### 断言——assert ####

```js
const assert=require('assert');

function sum(a, b){
  assert(arguments.length==2, '必须传2个参数');
  assert(typeof a=='number', '第一个参数必须是数字');
  assert(typeof b=='number', '第二个参数必须是数字');

  return a+b;
}

console.log(sum(12, 5));

```

> #### File System ####

读写文件

fs.js

```js
const fs=require('fs');

fs.readFile('1.txt', (err, data)=>{
  if(err){
    console.log('有错');
  }else{
    //'ads'.charCodeAt(0)
    console.log(data.toString());
  }
});

/*fs.writeFile('3.txt', 'erqwreqwere', err=>{
  if(err){
    console.log(err);
  }else{
    console.log('成功');
  }
});*/

```
fs2.js

```js
const fs=require('fs');

fs.readFile('ofo.png', (err, data)=>{
  fs.writeFile('ofo2.png', data, (err)=>{
    if(err){
      console.log(err);
    }else{
      console.log('成功');
    }
  });
});

```
> #### 多进程 ####

进程和线程的区别：

进程拥有独立的执行空间、存储
同一个进程内的所有线程共享一套空间、代码

多进程(PHP、Node)   成本高(慢)；安全(进程间隔离)；进程间通信麻烦；写代码简单
多线程(Java、C)   成本低(快)；不安全(线程要死一块死)；线程间通信容易；写代码复杂

进程间通信的几种方式
管道
共享内存
socket

> #### Crypto——签名 ####

MD5是单向散列生成hash值，不可逆破解

md5.js

```js
const crypto=require('crypto');

let obj=crypto.createHash('md5');

obj.update('123456');

// obj.update('123');
// obj.update('4');
// obj.update('56');

console.log(obj.digest('hex'));

```

双重加密

md5_2.js

```js
const crypto=require('crypto');

function md5(str){
  let obj=crypto.createHash('md5');
  obj.update(str);

  return obj.digest('hex');
}

console.log(md5(md5('123456')+'se32ssdfsd43'));

```

> #### OS ####

获取系统信息

```js
const os=require('os');

console.log(os.cpus());

```

> #### Path ####

```js
const path=require('path');

let str='/var/local/www/aaa/1.png';

//dirname
//basename
//extname

console.log(path.extname(str));

```

> #### Events事件队列 ####

和普通js函数的定义调用区别：解耦

```js
const Event=require('events').EventEmitter;


let ev=new Event();

//1.监听(接收)
ev.on('msg', function (a, b, c){
  console.log('收到了msg事件：', a, b, c);
});

//2.派发(发送)
ev.emit('msg', 12, 5, 88);


/*function msg(a, b, c){
  console.log('收到了msg事件：', a, b, c);
}

msg(12, 5, 88);*/

```


> #### Query Strings、URL ####

地址解析

querystring.js

```js
const querystring=require('querystring');

let obj=querystring.parse('ie=utf-8&f=8&rsv_bp=0&rsv_idx=1&tn=baidu&wd=aa&rsv_pq=f80d982000063ffb&rsv_t=6498LAZdRZjq9v4v0hs88kZItnCjDpT6UNBKr%2FF83%2F%2Bg4eiPURW2eQl9Iwc&rqlang=cn&rsv_enter=1&rsv_sug2=0&inputT=10&rsv_sug4=10');

console.log(obj);

```

url.js

```js
const url=require('url');

let obj=url.parse('https://www.baidu.com:8080/s?ie=utf-8&f=8&rsv_bp=0&rsv_idx=1&tn=baidu&wd=aa&rsv_pq=f80d982000063ffb&rsv_t=6498LAZdRZjq9v4v0hs88kZItnCjDpT6UNBKr%2FF83%2F%2Bg4eiPURW2eQl9Iwc&rqlang=cn&rsv_enter=1&rsv_sug2=0&inputT=10&rsv_sug4=10',true);

console.log(obj);

```
> #### 域名解析 ####

DNS、Domain

```js
const dns=require('dns');

dns.resolve('www.goole.com', (err, res)=>{
  if(err){
    console.log('解析失败');
  }else{
    console.log(res);
  }
});


```
> #### 流操作——Stream ####

连续数据都是流——视频流、网络流、文件流、语音流

> #### TLS/SSL ####

加密、安全

> #### ZLIB——gz ####

压缩

> ## NodeJS数据交互 ##

> ### 设置响应头 ###

![](设置响应头.png)

> ### get请求 ###

数据放在url地址上，存放的数量小(32k)

![](get请求.png)

> ### post请求 ###

在body里面,存放的数据量大(1G),一个大数据包切成一堆小包传输,容错性强

![](post请求.png)

> ### 安全性 ###

一切来自前台的数据都不可信
前后台都得进行数据校验
  前台校验：提高用户体验
  后台校验：提高安全性

> ### 数据库 ###

> #### 关系型数据库——MySQL、Oracle ####
    
> ##### 特点 #####

最常见、最常用,数据之间是有关系的
MySQL使用占比80%，免费，绝大多数普通应用，性能很高、安全性很高，容灾略差
Oracle收费，应用在金融、医疗，容灾特别强


> ##### SQL #####

> ###### 增 ######

INSERT INTO 表 (字段列表) VALUES(值列表)

```sql
INSERT INTO user_table (ID, name, gender, chinese, math, english) VALUES(0, 'blue', '男', 35, 18, 29);

```
> ###### 删 ######

DELETE FROM 表 WHERE 条件

```sql

DELETE FROM user_table WHERE ID=3;

```


> ###### 改 ######

UPDATE 表 SET 字段=值, 字段2=值2, ... WHERE 条件

```sql
UPDATE user_table SET chinese=100 WHERE ID=2;

```

> ###### 查 ######

SELECT 字段列表 FROM 表 WHERE 条件

```sql
SELECT name, gender FROM user_table WHERE ID=2;

```

> #### 文件型数据库——sqlite ####

使用简单、存储数据量小

> #### 文档型数据库——MongoDB ####

直接存储异构数据，使用方便

> #### NoSQL ####   

没有复杂的关系、对性能有极高的要求常见的有redis、memcached、hypertable、bigtable

> ## NodeJS进阶上 ##

> ### 文件数据解析 ###

> #### 表单的三种POST ####

`text/plain`,用的很少，纯文字                                            
`application/x-www-form-urlencoded`,默认,以url编码方式,`xxx=xxx&xxx=xx...`
`multipart/form-data`上传文件内容

![](表单post请求3种方式.png)

file上传，是post请求方式


> #### 普通纯文本表单文件上传 ####

普通纯文本文件上传可以用字符串拼接接收，有弊端，如果是图片文件上传，用字符串接收会出现数据错乱

![](普通纯文本表单文件上传.png)

描述：可以看到纯文本文件上传请求了2个资源

浏览器
![](普通纯文本表单文件上传2.png)

后台结果输出

包含了前台表单用户名、密码和纯文本文件的描述和内容

```
/upload
------WebKitFormBoundaryNIoabcKEXajTUMAI
Content-Disposition: form-data; name="user"

shenlibing
------WebKitFormBoundaryNIoabcKEXajTUMAI
Content-Disposition: form-data; name="pass"

000000
------WebKitFormBoundaryNIoabcKEXajTUMAI
Content-Disposition: form-data; name="f1"; filename="1.txt"
Content-Type: text/plain

abc
------WebKitFormBoundaryNIoabcKEXajTUMAI--

/favicon.ico

```

> #### `Buffer`接收文件上传的原始二进制数据 ####

![](文件上传post数据存储.png)

```
/upload
<Buffer 2d 2d 2d 2d 2d 2d 57 65 62 4b 69 74 46 6f 72 6d 42 6f 75 6e 64 61 72 79 75 42 6d 6b 78 4a 65 51 6d 35 6b 4a 4f 68 59 65 0d 0a 43 6f 6e 74 65 6e 74 2d ... >
/favicon.ico
<Buffer >

```

> #### Buffer数据进行查找、截取、切分 ####

如果是非纯文本文件上传，用字符串接收会破坏数据的完整性，需要用`Buffer`接收二进制数据


> ##### `Buffer`数据查找 #####

```js
let b=new Buffer('abccc-=-dddder-=-qwerqwer');

console.log(b.indexOf('-=-'));

```

> ##### `Buffer`数据截取 #####

```js
let b=new Buffer('abccc-=-dddder-=-qwerqwer');

console.log(b.slice(17).toString());

```

> ##### `Buffer`数据切分 #####

`Buffer`本身不具有`split`方法

```js
let b=new Buffer('abccc-=-dddder-=-qwerqwer');

Buffer.prototype.split=Buffer.prototype.split||function (b){
  let arr=[];

  let cur=0;
  let n=0;
  while((n=this.indexOf(b, cur))!=-1){
    arr.push(this.slice(cur, n));
    cur=n+b.length;
  }

  arr.push(this.slice(cur));

  return arr;
};

let arr=b.split('-=-');
console.log(arr);

console.log(arr.map(buffer=>buffer.toString()));

```


> #### 解析数据 ####

> ##### 数据化简 #####

先对纯文本文件上传用字符串拼接的结果进行分析
化简版本一

```
分隔符
Content-Disposition: form-data; name="user"

shenlibing
分隔符
Content-Disposition: form-data; name="pass"

000000
分隔符
Content-Disposition: form-data; name="f1"; filename="1.txt"
Content-Type: text/plain

abc
分隔符--

```

化简版本二：每一行末尾会自动加上`\r\n`

```
分隔符\r\n
Content-Disposition: form-data; name="user"\r\n
\r\n
shenlibing\r\n
分隔符\r\n
Content-Disposition: form-data; name="pass"\r\n
\r\n
000000\r\n
分隔符\r\n
Content-Disposition: form-data; name="f1"; filename="1.txt"\r\n
Content-Type: text/plain\r\n
\r\n
abc\r\n
分隔符--\r\n

```
化简版本三

```
分隔符\r\n数据描述\r\n\r\n数据值\r\n
分隔符\r\n数据描述\r\n\r\n数据值\r\n
分隔符\r\n数据描述1\r\n数据描述2\r\n\r\n文件内容\r\n
分隔符--\r\n

```

化简到版本三的时候，就可以开始解析数据了

> ##### 用分隔符切开数据 #####

```
[
  空,
  \r\n数据描述\r\n\r\n数据值\r\n,
  \r\n数据描述\r\n\r\n数据值\r\n,
  \r\n数据描述1\r\n数据描述2\r\n\r\n<文件内容>\r\n,
  --\r\n
]

```

> ##### 丢弃头尾元素 #####

```
[
  \r\n数据描述\r\n\r\n数据值\r\n,
  \r\n数据描述\r\n\r\n数据值\r\n,
  \r\n数据描述1\r\n数据描述2\r\n\r\n<文件内容>\r\n,
]

```

> ##### 丢弃每一项的头尾`\r\n` #####

```
[
  数据描述\r\n\r\n数据值,
  数据描述\r\n\r\n数据值,
  数据描述1\r\n数据描述2\r\n\r\n<文件内容>,
]

```

> ##### 用第一次出现的`\r\n\r\n`切分 #####

 普通数据：[数据描述, 数据值]
 文件数据：[数据描述1\r\n数据描述2, <文件内容>]

> ##### 判断描述的里面有没有`\r\n` #####

有的话就是文件数据：[数据描述1\r\n数据描述2, <文件内容>]
没有的话就是普通数据：[数据描述, 数据值]

> ##### 分析数据描述 #####

> #### 文件上传代码实现 ####

```js
const http=require('http');
const common=require('./libs/common');
const fs=require('fs');
const uuid=require('uuid/v4');

let server=http.createServer((req, res)=>{
  let arr=[];

  req.on('data', data=>{
    arr.push(data);
  });
  req.on('end', ()=>{
    let data=Buffer.concat(arr);

    //data
    //解析二进制文件上传数据
    let post={};
    let files={};
    if(req.headers['content-type']){
      let str=req.headers['content-type'].split('; ')[1];
      if(str){
        let boundary='--'+str.split('=')[1];

        //1.用"分隔符切分整个数据"
        let arr=data.split(boundary);

        //2.丢弃头尾两个数据
        arr.shift();
        arr.pop();

        //3.丢弃掉每个数据头尾的"\r\n"
        arr=arr.map(buffer=>buffer.slice(2,buffer.length-2));

        //4.每个数据在第一个"\r\n\r\n"处切成两半
        arr.forEach(buffer=>{
          let n=buffer.indexOf('\r\n\r\n');

          let disposition=buffer.slice(0, n);
          let content=buffer.slice(n+4);

          disposition=disposition.toString();

          if(disposition.indexOf('\r\n')==-1){
            //普通数据
            //Content-Disposition: form-data; name="user"
            content=content.toString();

            let name=disposition.split('; ')[1].split('=')[1];
            name=name.substring(1, name.length-1);

            post[name]=content;
          }else{
            //文件数据
            /*Content-Disposition: form-data; name="f1"; filename="a.txt"\r\n
            Content-Type: text/plain*/
            let [line1, line2]=disposition.split('\r\n');
            let [,name,filename]=line1.split('; ');
            let type=line2.split(': ')[1];

            name=name.split('=')[1];
            name=name.substring(1,name.length-1);

            filename=filename.split('=')[1];
            filename=filename.substring(1,filename.length-1);

            let path=`upload/${uuid().replace(/\-/g, '')}`;

            fs.writeFile(path, content, err=>{
              if(err){
                console.log('文件写入失败', err);
              }else{
                files[name]={filename, path, type};
                console.log(files);
              }
            });
          }
        });

        //5.完成
        console.log(post);
      }
    }

    res.end();
  });
});
server.listen(8080);

```

> ### 流操作 ###


> #### `fs.readFile`和`fs.writeFile`的弊端 ####

描述：以上文件上传的一个瑕疵就是会等到所有数据都到达了才开始处理，然后通过`fs.writeFile`上传文件

```js
http.createServer((req, res)=>{
  fs.readFile(`www${req.url}`, (err, data)=>{
    res.write(data);
  });
});

```
`fs.readFile`先把所有数据全读到内存中，然后回调,这种方式极其占用内存且资源利用极其不充分，读取文件的过程中网络传输一直空闲，等到文件IO读取完毕，IO一直空闲，网络传输变得繁忙

解决：收到一部分就解析一部分，极大节约内存,使用流读取文件，读一点、发一点

> #### 分类 ####

读取流`fs.createReadStream`
写入流`fs.createWriteStream`

```js
const http=require('http');
const fs=require('fs');

let server=http.createServer((req, res)=>{
  let rs=fs.createReadStream(`www${req.url}`);

  rs.pipe(res);

  rs.on('error', err=>{
    res.writeHeader(404);
    res.write('Not Found');

    res.end();
  });
});
server.listen(8080);

```

> ### gz压缩 ###


> #### 无gz压缩传输 ####

没有通过`gz`压缩传输,请求资源`1.html`文件大小`321B`，`jquery.js`文件大小`262KB`

```js
const http=require('http');
const fs=require('fs');
const zlib=require('zlib');

let server=http.createServer((req, res)=>{
  let rs=fs.createReadStream(`www${req.url}`);

  rs.pipe(res);

  /*res.setHeader('content-encoding', 'gzip');

  let gz=zlib.createGzip();
  rs.pipe(gz).pipe(res);*/

  rs.on('error', err=>{
    res.writeHeader(404);
    res.write('Not Found');

    res.end();
  });
});
server.listen(8080);

```
![](没有通过gz压缩传输.png)

> #### gz压缩传输 ####

读写流,通过`gz`压缩传输,请求资源`1.html`文件大小`292B`，`jquery.js`文件大小`77.8KB`

创建读取流读取`www${req.url}`文件，通过`gz`压缩、加密该文件然后返回给浏览器，需要设置响应头`res.setHeader('content-encoding', 'gzip')`,让浏览器识别该资源是通过`gz`压缩的文件

```js
const http=require('http');
const fs=require('fs');
const zlib=require('zlib');

let server=http.createServer((req, res)=>{
  let rs=fs.createReadStream(`www${req.url}`);

  //rs.pipe(res);

  res.setHeader('content-encoding', 'gzip');

  let gz=zlib.createGzip();
  rs.pipe(gz).pipe(res);

  rs.on('error', err=>{
    res.writeHeader(404);
    res.write('Not Found');

    res.end();
  });
});
server.listen(8080);

```
![](通过gz压缩传输.png)

![](通过gz压缩传输2.png)

> ## NodeJS进阶下 ##

> ### 缓存 ###

> #### 标记文件修改时间实现缓存 ####

> ##### 获得文件修改时间 #####

```js
const fs=require('fs');

fs.stat('./www/1.html', (err, stat)=>{
  if(err){
    console.log('获取文件信息失败');
  }else{
    console.log(stat.mtime.toGMTString());
  }
});

```

> ##### 服务器设置响应头`Last-Modified` #####

标记文件最后一次修改时间

![](服务器设置响应头文件修改时间.png)


> ##### 缓存实现过程 #####

```js
const http=require('http');
const fs=require('fs');
const url=require('url');

http.createServer((req, res)=>{
  let {pathname}=url.parse(req.url);

  //获取文件日期
  fs.stat(`www${pathname}`, (err, stat)=>{
    if(err){
      res.writeHeader(404);
      res.write('Not Found');
      res.end();
    }else{
      // console.log(req)
      if(req.headers['if-modified-since']){
        let oDate=new Date(req.headers['if-modified-since']);
        let time_client=Math.floor(oDate.getTime()/1000);

        let time_server=Math.floor(stat.mtime.getTime()/1000);

        if(time_server>time_client){      //服务器的文件时间>客户端手里的版本
          sendFileToClient();
        }else{
          res.writeHeader(304);
          res.write('Not Modified');
          res.end();
        }
      }else{
        sendFileToClient();
      }

      function sendFileToClient(){
        //发送
        let rs=fs.createReadStream(`www${pathname}`);

        res.setHeader('Last-Modified', stat.mtime.toGMTString());

        //输出
        rs.pipe(res);

        rs.on('error', err=>{
          res.writeHeader(404);
          res.write('Not Found');
          res.end();
        });
      }
    }
  });
}).listen(8080);

```

第一次请求,响应状态码200,浏览器没有缓存

服务器响应头带了`Last-Modified`标记该资源文件最后一次修改时间

![](浏览器请求服务器.png)
![](浏览器请求服务器2.png)

第二次请求,响应状态码304，浏览器有缓存

请求头带了`if-modified-since`标记该资源文件的最后一次修改时间,通过该标记去告诉服务器我本地有这个文件及这个文件最后一次修改时间，服务器收到请求通过`if-modified-since`标记的时间和服务器上该文件的时间进行比较，如果服务器的文件等于`if-modified-since`的时间，说明该资源文件没有被修改过，浏览器决定从不从缓存中取出

![](浏览器请求服务器3.png)

> #### 缓存策略 ####

服务器设置响应头`cache-control`和`expires`

> ### 多进程 ###

主进程负责派生子进程，子进程负责干活

> #### 特点 ####

普通程序不能“创建”进程，只有系统进程才能创建进程；只有主进程能分裂
进程是分裂出来
分裂出来的两个进程执行的是同一套代码
父子进程之间可以共享"句柄"(如：8080端口)

> #### 进程分裂实现 ####

通过`cluster.isMaster`判断是否是主进程，如果是主进程则`cluster.fork()`分裂子进程

```js
const http=require('http');
const cluster=require('cluster');
const os=require('os');
const process=require('process');

if(cluster.isMaster){
  for(let i=0;i<os.cpus().length;i++){
    cluster.fork();
  }

  console.log('主进程');
}else{
  console.log('子进程');
  let server=http.createServer((req, res)=>{
    //进程id
    console.log('子进程id',process.pid);

    res.write('aaaa');
    res.end();
  });

  server.listen(8080);

  console.log('服务器开好了，在8080上');
}

```
```
主进程
子进程
服务器开好了，在8080上
子进程
服务器开好了，在8080上
子进程
服务器开好了，在8080上
子进程
服务器开好了，在8080上
子进程
服务器开好了，在8080上
子进程
服务器开好了，在8080上
子进程
服务器开好了，在8080上
子进程
服务器开好了，在8080上
子进程 26956

```
> ##### 进程调度 #####

主进程通过系统的CPU核数分裂了8个子进程，浏览器发起请求的时候只有一个`子进程 26956`干活

多个进程同时存在时，进程的调度原则是第一个进程满了才开启第二个进程，前面两个进程满了才开启第三个进程，这样做的原因是因为进程调度即进程切换是需要花费开销的

> ##### 坑 #####


> ###### 主进程能否分裂100个进程，有必要么 ######


可以分裂100个进程，但是没有必要，进程调度需要花费开销，况且进程的实际工作计算能力需要根据计算机本身硬件的限制

> ## NodeJS使用MySQL ##

> ### 连接 ###

> #### 一条连接 ####

```js
let db=mysql.createConnection({host: 'localhost', user: 'root', password: '', port: 3309, database: '20180127'});

```

> #### 连接池获取连接 ####

```js
//连接池
let db=mysql.createPool({host: 'localhost', user: 'root', password: '', port: 3309, database: '20180127'});

```

> ### 查询 ###

```js
const mysql=require('mysql');

let db=mysql.createConnection({host: 'localhost', user: 'root', password: '', port: 3309, database: '20180127'});

db.query(`INSERT INTO user_table (ID, name, gender, chinese, math, english) VALUES(0, '小明', '男', 98, 5, 3);`, (err, data)=>{
  if(err){
    console.log('错了', err);
  }else{
    console.log(data);
  }
});


```

> ### 用户登录注册 ###


> #### 数据库结构(数据字典) ####

> #### 接口格式(接口文档) ####

```
注册：
  /reg?user=xxx&pass=xxx
  =>{err: 0, msg: '原因'}

```

```
登陆：
  /login?user=xxx&pass=xxx
  =>{err: 0, msg: '原因'}
```
> #### 代码实现 ####

前台

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title></title>
    <script src="jquery.js" charset="utf-8"></script>
    <script>
    $(function (){
      //注册
      $('#btn_reg').click(function (){
        $.ajax({
          url: '/reg',
          data: {user: $('#user').val(),pass: $('#pass').val()},
          dataType: 'json',
          success(json){
            if(json.err){
              alert('注册失败'+json.msg);
            }else{
              alert('注册成功');
            }
          },
          error(err){
            alert('失败');
          }
        });
      });

      //登陆
      $('#btn_login').click(function (){
        $.ajax({
          url: '/login',
          data: {user: $('#user').val(),pass: $('#pass').val()},
          dataType: 'json',
          success(json){
            if(json.err){
              alert('登陆失败'+json.msg);
            }else{
              alert('登陆成功');
            }
          },
          error(err){
            alert('失败');
          }
        });
      });
    });
    </script>
  </head>
  <body>
    用户：<input type="text" id="user" /><br>
    密码：<input type="password" id="pass" /><br>
    <input type="button" value="注册" id="btn_reg">
    <input type="button" value="登陆" id="btn_login">
  </body>
</html>


```

后台

```js
const http=require('http');
const mysql=require('mysql');
const fs=require('fs');
const url=require('url');
const zlib=require('zlib');
const crypto=require('crypto');

//盐值
const _key='sadfslekrtuew5iutoselgdtjiypoydse4ufhs.edtyo;s8te4arfeliawkfhtsie5tlfia;sefdshroiupeoutwyeli5gurse;ihf';

//MD5加密
function md5(str){
  let obj=crypto.createHash('md5');

  obj.update(str);

  return obj.digest('hex');
}

//MD5盐值加密
function md5_2(str){
  return md5(md5(str)+_key);
}

//从连接池获取连接
let db=mysql.createPool({host: 'localhost', port: 3306, user: 'root', password: '000000', database: 'mytest'});


let server=http.createServer((req, res)=>{
  let {pathname, query}=url.parse(req.url, true);
  let {user, pass}=query;

  switch(pathname){
    //接口
    case '/reg':
      //校验
      if(!user){
        res.write('{"err": 1, "msg": "username can\'t be null"}');
        res.end();
      }else if(!pass){
        res.write('{"err": 1, "msg": "password can\'t be null"}');
        res.end();
      }else if(!/^\w{4,16}$/.test(user)){
        res.write('{"err": 1, "msg": "username is invaild"}');
        res.end();
      }else if(/['|"]/.test(pass)){
        res.write('{"err": 1, "msg": "password is invaild"}');
        res.end();
      }else{
        db.query(`SELECT * FROM user_table WHERE username='${user}'`, (err, data)=>{
          if(err){
            res.write('{"err": 1, "msg": "database error"}');
            res.end();
          }else if(data.length>0){
            res.write('{"err": 1, "msg": "this username exsits"}');
            res.end();
          }else{
            db.query(`INSERT INTO user_table (ID,username,password) VALUES(0,'${user}','${md5_2(pass)}')`, (err, data)=>{
              if(err){
                res.write('{"err": 1, "msg": "database error"}');
                res.end();
              }else{
                res.write('{"err": 0, "msg": "success"}');
                res.end();
              }
            });
          }
        });
      }
      break;
    case '/login':
      //校验
      if(!user){
        res.write('{"err": 1, "msg": "username can\'t be null"}');
        res.end();
      }else if(!pass){
        res.write('{"err": 1, "msg": "password can\'t be null"}');
        res.end();
      }else if(!/^\w{4,16}$/.test(user)){
        res.write('{"err": 1, "msg": "username is invaild"}');
        res.end();
      }else if(/['|"]/.test(pass)){
        res.write('{"err": 1, "msg": "password is invaild"}');
        res.end();
      }else{
        db.query(`SELECT * FROM user_table WHERE username='${user}'`, (err, data)=>{
          if(err){
            res.write('{"err": 1, "msg": "database error"}');
            res.end();
          }else if(data.length==0){
            res.write('{"err": 1, "msg": "no this user"}');
            res.end();
          }else if(data[0].password!=md5_2(pass)){
            res.write('{"err": 1, "msg": "username or password is incorrect"}');
            res.end();
          }else{
            res.write('{"err": 0, "msg": "success"}');
            res.end();
          }
        });
      }
      break;
    default:
      //缓存      TODO
      //静态文件
      let rs=fs.createReadStream(`www${pathname}`);
      let gz=zlib.createGzip();

      res.setHeader('content-encoding', 'gzip');
      rs.pipe(gz).pipe(res);

      rs.on('error', err=>{
        res.writeHeader(404);
        res.write('Not Found');
        res.end();
      });
  }
});
server.listen(8080);


```