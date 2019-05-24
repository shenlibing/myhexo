---

title: websocket原理
date: 2019-05-12 14:21:06
categories:
tags:
typora-root-url: websocket原理
---



# websocket

- 双向通信

- 性能高

- WebSocket是前台的东西，是HTML5带的一种东西

  - 只有前台有WebSocket这个东西

  - 后台没有，后台有Socket



# 使用socket.io实现WebSocket

## 引入依赖

```json
{
  "name": "2018-2-3",
  "version": "1.0.0",
  "description": "",
  "main": "server.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "start": "node server.js"
  },
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "socket.io": "^2.0.4"
  }
}

```

## 前台

1.html

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title></title>
    <style media="screen">
    #ul1 {width:400px; height:300px; border:1px solid black; overflow:auto;}
    #ul1 li.me {color:green}
    .err_box {width:100%; height:20px; line-height: 20px; text-align:center; color:red; display:none}
    </style>
    <script src="http://localhost:8080/socket.io/socket.io.js" charset="utf-8"></script>
    <script>
    let sock=io.connect('ws://localhost:8080/');

    sock.on('connect', ()=>{
      console.log('已连接');
      document.getElementsByClassName('err_box')[0].style.display='none';
    });
    sock.on('disconnect', ()=>{
      console.log('已断开');
      document.getElementsByClassName('err_box')[0].style.display='block';
    });

    //聊天室
    window.onload=function (){
      let oTxt=document.getElementById('txt1');
      let oBtn=document.getElementById('btn1');
      let oUl=document.getElementById('ul1');

      oBtn.onclick=function (){
        sock.emit('msg', oTxt.value);

        let oLi=document.createElement('li');
        oLi.innerHTML=oTxt.value;
        oLi.className='me';

        oTxt.value='';

        oUl.appendChild(oLi);
      };

      sock.on('msg', str=>{
        let oLi=document.createElement('li');

        oLi.innerHTML=str;
        oUl.appendChild(oLi);
      });
    };
    </script>
  </head>
  <body>
    <div class="err_box">
      无法连接到服务器，请检查网络
    </div>
    <ul id="ul1"></ul>
    <textarea rows="4" cols="60" id="txt1"></textarea>
    <input type="button" value="发送" id="btn1">
  </body>
</html>

```

## 后台

server.js

```js
const http=require('http');
const io=require('socket.io');

let httpServer=http.createServer((req, res)=>{

});
httpServer.listen(8080);

//
let wsServer=io.listen(httpServer);

let aSock=[];
wsServer.on('connection', sock=>{
  aSock.push(sock);

  //断开连接
  sock.on('disconnect', ()=>{
    let n=aSock.indexOf(sock);

    if(n!=-1){
      aSock.splice(n, 1);
    }
  });

  sock.on('msg', str=>{
    aSock.forEach(s=>{
      if(s!=sock){
        s.emit('msg', str);
      }
    });
  });
});

setInterval(function (){
  console.log(aSock.length);
}, 500);

```

# 原生WebSocket实现

## 前台

raw_client.html

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title></title>
    <script>
    let sock=new WebSocket('ws://localhost:8080/');

    sock.emit=function (name, ...args){
      alert(JSON.stringify({name, data: [...args]}));
      sock.send(JSON.stringify({name, data: [...args]}));
    };

    //连上了
    sock.onopen=function (){
      alert('连接上了');

	  //发送数据
      sock.emit('msg', 12, 5);
    };

    //有数据
    sock.onmessage=function (){
      alert('有消息过来');
    };

    //断开了
    sock.onclose=function (){
      alert('断了');
    };
    </script>
  </head>
  <body>

  </body>
</html>

```



## 后台

### 建立连接

#### 原始数据

```
GET / HTTP/1.1
Host: localhost:8080
Connection: Upgrade
Pragma: no-cache
Cache-Control: no-cache
Upgrade: websocket
Origin: file://
Sec-WebSocket-Version: 13
User-Agent: Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like
Gecko) Chrome/65.0.3315.4 Safari/537.36
Accept-Encoding: gzip, deflate, br
Accept-Language: zh-CN,zh;q=0.9,en;q=0.8,zh-TW;q=0.7,es;q=0.6,fr;q=0.5,pt;q=0.4
Sec-WebSocket-Key: +0jgXtYyVeG28Gn1CLUKIg==
Sec-WebSocket-Extensions: permessage-deflate; client_max_window_bits
```

#### 分析原始数据

- 第一行删掉

```
Host: localhost:8080
Connection: Upgrade
Pragma: no-cache
Cache-Control: no-cache
Upgrade: websocket
Origin: file://
Sec-WebSocket-Version: 13
User-Agent: Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like
Gecko) Chrome/65.0.3315.4 Safari/537.36
Accept-Encoding: gzip, deflate, br
Accept-Language: zh-CN,zh;q=0.9,en;q=0.8,zh-TW;q=0.7,es;q=0.6,fr;q=0.5,pt;q=0.4
Sec-WebSocket-Key: +0jgXtYyVeG28Gn1CLUKIg==
Sec-WebSocket-Extensions: permessage-deflate; client_max_window_bits
```

- 舍弃第一行和最后两行

` lines=lines.slice(1, lines.length-2);`

![1557643527632](1557643527632.png)

- 每行数据用": "切开

#### 代码

raw_server.js

引入net模块

```js
const http=require('http');
const net=require('net');         //TCP     原生Socket
const crypto=require('crypto');

/*
let server=http.createServer((req, res)=>{
  console.log('连接');
});
server.listen(8080);
*/

let server=net.createServer(sock=>{
  console.log('连接');

  //数据过来——握手只有一次
  sock.once('data', data=>{
    console.log('hand shake start...');

    let str=data.toString();
    let lines=str.split('\r\n');

    //舍弃第一行和最后两行
    lines=lines.slice(1, lines.length-2);

    //切开
    let headers={};
    lines.forEach(line=>{
      let [key, val]=line.split(': ');

      headers[key.toLowerCase()]=val;
    });

    //console.log(headers);

    if(headers['upgrade']!='websocket'){
      console.log('其他协议', headers['upgrade']);

      sock.end();
    }else if(headers['sec-websocket-version']!=13){
      console.log('版本不对', headers['sec-websocket-version']);

      sock.end();
    }else{
      let key=headers['sec-websocket-key'];
      let mask='258EAFA5-E914-47DA-95CA-C5AB0DC85B11';

      //sha1(key+mask)->base64=>client

      let hash=crypto.createHash('sha1');
      hash.update(key+mask);
      let key2=hash.digest('base64');

      sock.write(`HTTP/1.1 101 Switching Protocols\r\nUpgrade: websocket\r\nConnection: Upgrade\r\nSec-WebSocket-Accept: ${key2}\r\n\r\n`);

      console.log('hand shake end');

      //真正的数据
      sock.on('data', data=>{
        console.log('有数据');

        console.log(data);
        
      });
    }
  });

  //断开了
  sock.on('end', ()=>{
    console.log('客户端已断开');
  });
});
server.listen(8080);

```

### 解析真正数据

#### 真正数据

![1557643770967](1557643770967.png)

```
81       9c       11       2d       f8       bd         数据.....
10000001 10011100 00010001 00101101 11111000 10111101

11 2d f8 bd
6a 0f 96 dc 7c 48 da 87 33 40 8b da 33 01 da d9 70 59 99 9f 2b 76 c9 8f 3d 18 a5 c0



1 000 0001   1 0011100 00010001 00101101 11111000 10111101
F RSV opcode M payload masking-key
I            A 28个字
N            S
             K
```

#### 帧结构

```
 0 1 2 3 4 5 6 7 0 1 2 3 4 5 6 7 0 1 2 3 4 5 6 7 0 1 2 3 4 5 6 7
+-+-+-+-+-------+-+-------------+-------------------------------+
|F|R|R|R| opcode|M| Payload len |    Extended payload length    |
|I|S|S|S|  (4)  |A|     (7)     |             (16/64)           |
|N|V|V|V|       |S|             |   (if payload len==126/127)   |
| |1|2|3|       |K|             |                               |
+-+-+-+-+-------+-+-------------+-------------------------------+
|     Extended payload length continued, if payload len == 127  |
+-------------------------------+-------------------------------+
|                               |Masking-key, if MASK set to 1  |
+-------------------------------+-------------------------------+
| Masking-key (continued)       |          Payload Data         |
+-------------------------------+-------------------------------+
|                     Payload Data continued ...                |
+---------------------------------------------------------------+
|                     Payload Data continued ...                |
+---------------------------------------------------------------+



FIN               1bit 是否最后一帧
RSV               3bit 预留
Opcode            4bit 帧类型
Mask              1bit 掩码，是否加密数据，默认必须置为1
Payload           7bit 长度
Masking-key       1 or 4 bit 掩码
Payload data      (x + y) bytes 数据
Extension data    x bytes  扩展数据
Application data  y bytes  程序数据

```

#### 传递数据单位

1位(bit)
8位等于1字节(byte)

#### 代码

raw_server2.js

```js
const http=require('http');
const net=require('net');         //TCP     原生Socket
const crypto=require('crypto');

/*
let server=http.createServer((req, res)=>{
  console.log('连接');
});
server.listen(8080);
*/

let server=net.createServer(sock=>{
  console.log('连接');

  //数据过来——握手只有一次
  sock.once('data', data=>{
    console.log('hand shake start...');

    let str=data.toString();
    let lines=str.split('\r\n');

    //舍弃第一行和最后两行
    lines=lines.slice(1, lines.length-2);

    //切开
    let headers={};
    lines.forEach(line=>{
      let [key, val]=line.split(': ');

      headers[key.toLowerCase()]=val;
    });

    //console.log(headers);

    if(headers['upgrade']!='websocket'){
      console.log('其他协议', headers['upgrade']);

      sock.end();
    }else if(headers['sec-websocket-version']!=13){
      console.log('版本不对', headers['sec-websocket-version']);

      sock.end();
    }else{
      let key=headers['sec-websocket-key'];
      let mask='258EAFA5-E914-47DA-95CA-C5AB0DC85B11';

      //sha1(key+mask)->base64=>client

      let hash=crypto.createHash('sha1');
      hash.update(key+mask);
      let key2=hash.digest('base64');

      sock.write(`HTTP/1.1 101 Switching Protocols\r\nUpgrade: websocket\r\nConnection: Upgrade\r\nSec-WebSocket-Accept: ${key2}\r\n\r\n`);

      console.log('hand shake end');

      //真正的数据
      sock.on('data', data=>{
        console.log('有数据');

        let FIN=data[0]&0x001;
        let opcode=data[0]&0x0F0;
        let mask=data[1]&0x001;
        let payload=data[1]&0x0FE;


        console.log(FIN, opcode);
        console.log(mask, payload);
      });
    }
  });

  //断开了
  sock.on('end', ()=>{
    console.log('客户端已断开');
  });
});
server.listen(8080);

```



## 总结

### 流程

#### 引入net模块

### 握手

```
  客户端:version:13、sec-websocket-key:xxxxx、sha1(key+mask)=>base64
  服务端:101 Switching Protocols、sec-websocket-accept: base64
```



#### 客户端

- onopen 
- onmessage
- onclose

#### 服务端

-   net.createServer(sock=>{});
- sock.once('data', 握手);
- sock.on('data', 数据请求);
- sock.on('end');

### 数据帧解析

# 参考

[WebSocket 实战](https://www.ibm.com/developerworks/cn/java/j-lo-WebSocket/)

[WebSocket - Wikipedia](https://en.wikipedia.org/wiki/WebSocket#External_links)