---
title: upload和FileReade
date: 2019-05-24 16:13:24
categories: 日常记录
tags:
---

# 上传进度

## 前台

### 版本一

- `HTML5`标签自带的进度条标签`<meter>`

- 上传进度`oAjax.upload.onprogress`

上传进度.html

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title></title>
    <script>
    window.onload=function (){
      let oF=document.getElementById('f1');
      let oBtn=document.getElementById('btn1');

      oBtn.onclick=function (){
        let data=new FormData();

        Array.from(oF.files).forEach(file=>{
          data.append('f1', file);
        });

        //
        let oAjax=new XMLHttpRequest();



        //POST
        oAjax.open('POST', `http://localhost:8080/api`, true);

        oAjax.upload.onprogress=function (ev){
          let oM=document.getElementById('m1');

          oM.value=100*ev.loaded/ev.total;
        };

        oAjax.send(data);

        /*

        oAjax.onreadystatechange=function (){
          if(oAjax.readyState==4){
            if(oAjax.status>=200 && oAjax.status<300 || oAjax.status==304){
              alert('成功');
            }else{
              alert('失败');
            }
          }
        };*/
      };
    };
    </script>
  </head>
  <body>
    <meter id="m1" value="" min="0" max="100" style="width:500px;"></meter><br>
    <input type="file" id="f1" multiple /><br>
    <input type="button" value="提交" id="btn1">
  </body>
</html>

```

### 版本二

- 自己创建进度条标签

- 绑定事件监听`oAjax.upload.addEventListener`

上传进度2.html

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title></title>
    <style media="screen">
    .parent {width:500px; height:20px; border:1px solid black;}
    .child {width:0; height:100%; background:green;}
    </style>
    <script>
    window.onload=function (){
      let oF=document.getElementById('f1');
      let oBtn=document.getElementById('btn1');

      oBtn.onclick=function (){
        let data=new FormData();

        Array.from(oF.files).forEach(file=>{
          data.append('f1', file);
        });

        //
        let oAjax=new XMLHttpRequest();

        //POST
        oAjax.open('POST', `http://localhost:8080/api`, true);

        console.log(oAjax);

        oAjax.upload.addEventListener('progress', function (ev){
          /*let oM=document.getElementById('m1');

          oM.value=100*ev.loaded/ev.total;*/

          let oChild=document.getElementsByClassName('child')[0];

          oChild.style.width=100*ev.loaded/ev.total+'%';
        }, false);

        oAjax.send(data);

        /*

        oAjax.onreadystatechange=function (){
          if(oAjax.readyState==4){
            if(oAjax.status>=200 && oAjax.status<300 || oAjax.status==304){
              alert('成功');
            }else{
              alert('失败');
            }
          }
        };*/
      };
    };
    </script>
  </head>
  <body>
    <div class="parent">
      <div class="child">

      </div>
    </div>
    <input type="file" id="f1" multiple /><br>
    <input type="button" value="提交" id="btn1">
  </body>
</html>

```

### 版本三

- 下载进度`oAjax.onprogress`

下载进度.html

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title></title>
    <style media="screen">
    .parent {width:500px; height:20px; border:1px solid black;}
    .child {width:0; height:100%; background:green;}
    </style>
    <script>
    window.onload=function (){
      let oF=document.getElementById('f1');
      let oBtn=document.getElementById('btn1');

      oBtn.onclick=function (){
        let data=new FormData();

        Array.from(oF.files).forEach(file=>{
          data.append('f1', file);
        });

        //
        let oAjax=new XMLHttpRequest();

        //POST
        oAjax.open('POST', `http://localhost:8080/api`, true);

        oAjax.onprogress=function (ev){
          console.log(ev);
        };

        oAjax.upload.addEventListener('progress', function (ev){
          /*let oM=document.getElementById('m1');

          oM.value=100*ev.loaded/ev.total;*/

          let oChild=document.getElementsByClassName('child')[0];

          oChild.style.width=100*ev.loaded/ev.total+'%';
        }, false);

        oAjax.send(data);

        /*

        oAjax.onreadystatechange=function (){
          if(oAjax.readyState==4){
            if(oAjax.status>=200 && oAjax.status<300 || oAjax.status==304){
              alert('成功');
            }else{
              alert('失败');
            }
          }
        };*/
      };
    };
    </script>
  </head>
  <body>
    <div class="parent">
      <div class="child">

      </div>
    </div>
    <input type="file" id="f1" multiple /><br>
    <input type="button" value="提交" id="btn1">
  </body>
</html>

```

### 版本四

- 下载进度绑定事件监听`document.addEventListener`

下载进度2.html

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title></title>
    <style media="screen">
    .parent {width:500px; height:20px; border:1px solid black;}
    .child {width:0; height:100%; background:green;}
    </style>
    <script>
    document.addEventListener('progress', function (){
      console.log('a');
    }, false);
    </script>
  </head>
  <body>
    <form id="form1" action="http://localhost:8080/api" method="post" enctype="multipart/form-data">
      <input type="file" multiple>
      <input type="submit" value="上传">
    </form>
  </body>
</html>

```

## 后台

- 解决跨域，判断请求头中是否携带`req.headers['origin']`字段

express_server.js

```js
const express=require('express');     //主体
const body=require('body-parser');    //接收普通POST数据
const multer=require('multer');       //接收文件POST数据
const mysql=require('mysql');

let db=mysql.createPool({host: 'localhost', port: 3309, user: 'root', password: '', database: '20180208'});

let server=express();
server.listen(8080);

//中间件
server.use(body.urlencoded({extended: false}));

let multerObj=multer({dest: './upload/'});
server.use(multerObj.any());

//处理请求
server.use('/api', (req, res)=>{
  if(req.headers['origin']=='null' || req.headers['origin'].startsWith('http://localhost')){
    res.setHeader('Access-Control-Allow-Origin', '*');
  }

  let arr=[];
  req.files.forEach(file=>{
    arr.push(`('${file.originalname}', '${file.filename}', ${Math.floor(Date.now()/1000)})`);
  });

  let sql=`INSERT INTO image_table (originalname, filename, time) VALUES${arr.join(',')}`;

  //console.log(sql);
  db.query(sql, (err)=>{
    if(err){
      res.send('不OK');
    }else{
      res.send("OK");
    }
  });
});

//
server.use(express.static('./www/'));

```

## 上传进度条实现注意

- `oAjax.upload.onprogress`必须放在`oAjax.send(data)`前面
- 服务器必须能处理`OPTIONS`请求，`oAjax`加了`upload`会发送`OPTIONS`和`POST`2次请求
  - 对于通用服务器，我们不用担心，他们会自己进行处理
  - 对于NodeJS服务，使用`express`框架，使用`server.use`

# 拖拽上传

- 拖拽松手事件`drop`
  - 绑定事件监听阻止默认行为，`ev.preventDefault()`
  - 按钮阻止默认行为，`return false`
- 获取文件`ev.dataTransfer.files`
- 数据封装，使用`FormData`封装文件数据
- 发送，`oAjax.send(data)`

文件拖拽.html

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title></title>
    <style media="screen">
    * {margin:0; padding:0}
    .box {width:400px; height:200px; background:#CCC; border:1px solid black; line-height:200px; position:absolute; left:50%; top:50%; margin-left:-200px; margin-top:-100px; text-align:center; display:none;}
    </style>
    <script>
    window.onload=function (){
      let oBox=document.querySelector('.box');
      let timer;

      document.addEventListener('dragover', (ev)=>{
        clearTimeout(timer);

        oBox.style.display='block';

        timer=setTimeout(function (){
          oBox.style.display='none';
        }, 300);

        ev.preventDefault();
      }, false);

      oBox.addEventListener('dragenter', ()=>{
        oBox.innerHTML='请松手';
      }, false);
      oBox.addEventListener('dragleave', ()=>{
        oBox.innerHTML='请把文件拖到这儿';
      }, false);

      oBox.addEventListener('drop', (ev)=>{
        let data=new FormData();

        Array.from(ev.dataTransfer.files).forEach(file=>{
          data.append('f1', file);
        });

        let oAjax=new XMLHttpRequest();

        //POST
        oAjax.open('POST', `http://localhost:8080/api`, true);

        oAjax.upload.addEventListener('progress', function (ev){
          let oM=document.querySelector('#m1');

          oM.value=100*ev.loaded/ev.total;
        }, false);

        oAjax.send(data);

        ev.preventDefault();

        oAjax.onreadystatechange=function (){
          if(oAjax.readyState==4){
            if(oAjax.status>=200 && oAjax.status<300 || oAjax.status==304){
              alert('成功');
            }else{
              alert('失败');
            }
          }
        };
      }, false);
    };
    </script>
  </head>
  <body>
    <meter id="m1" min="0" max="100" style="width:100%;"></meter>
    <div class="box">
      请把文件拖到这儿
    </div>
  </body>
</html>

```



# 读取文件FileReader

## 前台

### 版本一

- 文件读取 方法`reader.readAsDataURL(file)`可以将图片文件转换为`base64`编码
- 传输数据可以通过二进制和`base64`来传输
  - `base64`可以把二进制数据表现成字符串
  - 只要能出现地址`src`的地方，都能用`Base64`
  - 一般小图标不要引用地址，直接放个`base64`可以优化网络性能
  - 维护麻烦，`base64`编码会把文件体积变大
- 当读取操作成功完成时调用`reader.onload`

读取文件.html

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title></title>
    <style media="screen">
    * {margin:0; padding:0}
    .box {width:400px; height:200px; background:#CCC; border:1px solid black; line-height:200px; position:absolute; left:50%; top:50%; margin-left:-200px; margin-top:-100px; text-align:center; display:none;}
    </style>
    <script>
    window.onload=function (){
      let oBox=document.querySelector('.box');
      let timer;

      document.addEventListener('dragover', (ev)=>{
        clearTimeout(timer);

        oBox.style.display='block';

        timer=setTimeout(function (){
          oBox.style.display='none';
        }, 300);

        ev.preventDefault();
      }, false);

      oBox.addEventListener('dragenter', ()=>{
        oBox.innerHTML='请松手';
      }, false);
      oBox.addEventListener('dragleave', ()=>{
        oBox.innerHTML='请把文件拖到这儿';
      }, false);

      oBox.addEventListener('drop', (ev)=>{
        let file=ev.dataTransfer.files[0];

        //读取文件
        let reader=new FileReader();

        reader.onload=function (){
          document.write(reader.result);
        };

        //base64
        reader.readAsDataURL(file);


        ev.preventDefault();
      }, false);
    };
    </script>
  </head>
  <body>
    <div class="box">
      请把文件拖到这儿
    </div>
  </body>
</html>

```

### 版本二

- 判断文件类型，`file.type.startsWith('image/')`

读取文件2.html

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title></title>
    <style media="screen">
    * {margin:0; padding:0; list-style: none}
    .box {width:400px; height:200px; background:#CCC; border:1px solid black; line-height:200px; position:absolute; left:50%; top:50%; margin-left:-200px; margin-top:-100px; text-align:center; display:none;}

    .img_list {overflow:hidden;}
    .img_list li {float:left; width:200px; height:200px; border:3px solid #666; margin:10px; position:relative;}
    .img_list li img {width:100%; height:100%;}
    .img_list li .del_btn {position:absolute; right:0; top:0;}
    </style>
    <script>
    window.onload=function (){
      let oUl=document.querySelector('.img_list');
      let oBox=document.querySelector('.box');
      let timer;

      document.addEventListener('dragover', (ev)=>{
        clearTimeout(timer);

        oBox.style.display='block';

        timer=setTimeout(function (){
          oBox.style.display='none';
        }, 300);

        ev.preventDefault();
      }, false);

      oBox.addEventListener('dragenter', ()=>{
        oBox.innerHTML='请松手';
      }, false);
      oBox.addEventListener('dragleave', ()=>{
        oBox.innerHTML='请把文件拖到这儿';
      }, false);

      oBox.addEventListener('drop', (ev)=>{
        Array.from(ev.dataTransfer.files).forEach(file=>{
          if(!file.type.startsWith('image/')){
            return;
          }

          let reader=new FileReader();

          reader.onload=function (){
            let oLi=document.createElement('li');
            oLi.file=file;
            oLi.innerHTML='<img src="a.png" alt=""><a href="javascript:;" class="del_btn">删除</a>';

            let oImg=oLi.children[0];
            oImg.src=this.result;

            let oBtnDel=oLi.children[1];
            oBtnDel.onclick=function (){
              oUl.removeChild(oLi);
            };

            oUl.appendChild(oLi);
          };

          reader.readAsDataURL(file);
        });

        ev.preventDefault();
      }, false);

      //真的上传
      let oBtnUpload=document.querySelector('#btn_upload');
      oBtnUpload.onclick=function (){
        let data=new FormData();

        Array.from(oUl.children).forEach(li=>{
          data.append('f1', li.file);
        });

        //
        let oAjax=new XMLHttpRequest();

        //POST
        oAjax.open('POST', `http://localhost:8080/api`, true);
        oAjax.send(data);

        oAjax.onreadystatechange=function (){
          if(oAjax.readyState==4){
            if(oAjax.status>=200 && oAjax.status<300 || oAjax.status==304){
              alert('成功');
            }else{
              alert('失败');
            }
          }
        };
      };
    };
    </script>
  </head>
  <body>
    <ul class="img_list">
      <!--<li>
        <img src="a.png" alt="">
        <a href="javascript:;" class="del_btn">删除</a>
      </li>
      <li>
        <img src="b.png" alt="">
        <a href="javascript:;" class="del_btn">删除</a>
      </li>-->
    </ul>
    <input type="button" name="" value="上传" id="btn_upload">
    <div class="box">
      请把文件拖到这儿
    </div>
  </body>
</html>

```

### 版本三

- 以二进制数据的形式存储数据，`reader.readAsArrayBuffer(file)`
- 以字符串形式存储的二进制数据，`readAsBinaryString`
- 图片(以及其他二进制数据)，`readAsDataURL`
- 将文件读取为文本，`readAsText`

读取文件3.html

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title></title>
    <style media="screen">
    * {margin:0; padding:0; list-style: none}
    .box {width:400px; height:200px; background:#CCC; border:1px solid black; line-height:200px; position:absolute; left:50%; top:50%; margin-left:-200px; margin-top:-100px; text-align:center; display:none;}

    .img_list {overflow:hidden;}
    .img_list li {float:left; width:200px; height:200px; border:3px solid #666; margin:10px; position:relative;}
    .img_list li img {width:100%; height:100%;}
    .img_list li .del_btn {position:absolute; right:0; top:0;}
    </style>
    <script>
    window.onload=function (){
      let oUl=document.querySelector('.img_list');
      let oBox=document.querySelector('.box');
      let timer;

      document.addEventListener('dragover', (ev)=>{
        clearTimeout(timer);

        oBox.style.display='block';

        timer=setTimeout(function (){
          oBox.style.display='none';
        }, 300);

        ev.preventDefault();
      }, false);

      oBox.addEventListener('dragenter', ()=>{
        oBox.innerHTML='请松手';
      }, false);
      oBox.addEventListener('dragleave', ()=>{
        oBox.innerHTML='请把文件拖到这儿';
      }, false);

      oBox.addEventListener('drop', (ev)=>{
        let file=ev.dataTransfer.files[0];
        let reader=new FileReader();

        console.log(reader);
        reader.onload=function (){
          console.log(this.result);
        };

        reader.readAsArrayBuffer(file);

        ev.preventDefault();
      }, false);
    };
    </script>
  </head>
  <body>
    <div class="box">
      请把文件拖到这儿
    </div>
  </body>
</html>

```

## 后台

将读取的文件入库

- 连接数据库`mysql.createPool`
- 批量插入

express_server.js

```js
const express=require('express');     //主体
const body=require('body-parser');    //接收普通POST数据
const multer=require('multer');       //接收文件POST数据
const mysql=require('mysql');

let db=mysql.createPool({host: 'localhost', port: 3309, user: 'root', password: '', database: '20180208'});

let server=express();
server.listen(8080);

//中间件
server.use(body.urlencoded({extended: false}));

let multerObj=multer({dest: './upload/'});
server.use(multerObj.any());

//处理请求
server.use('/api', (req, res)=>{
  if(req.headers['origin']=='null' || req.headers['origin'].startsWith('http://localhost')){
    res.setHeader('Access-Control-Allow-Origin', '*');
  }

  let arr=[];
  req.files.forEach(file=>{
    arr.push(`('${file.originalname}', '${file.filename}', ${Math.floor(Date.now()/1000)})`);
  });

  let sql=`INSERT INTO image_table (originalname, filename, time) VALUES${arr.join(',')}`;

  //console.log(sql);
  db.query(sql, (err)=>{
    if(err){
      res.send('不OK');
    }else{
      res.send("OK");
    }
  });
});

//
server.use(express.static('./www/'));

```

# ajax版本

- `ajax2.0`携带有`oAjax.upload`属性

ajax版本.html

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title></title>
    <style media="screen">
    .parent {width:500px; height:20px; border:1px solid black;}
    .child {width:0; height:100%; background:green;}
    </style>
    <script>
    window.onload=function (){
      var oBtn=document.getElementById('btn1');

      oBtn.onclick=function (){
        var oAjax=new XMLHttpRequest();

        if(oAjax.upload){
          alert('2.0');
        }else{
          alert('1.0');
        }
      };
    };
    </script>
  </head>
  <body>
    <input type="button" value="提交" id="btn1">
  </body>
</html>

```

