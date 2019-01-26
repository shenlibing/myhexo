---
title: es6异步
date: 2019-01-25 17:28:47
categories: 日常记录
tags:	es6
---

1、promise-解决异步操作

同步——串行    简单、方便

异步——并发    性能高、体验好

jQuery异步问题：回到地狱

```js
	$.ajax({
	  url: '/banner_data',
	  success(banners){
	    $.ajax({
	      url: '/user_data',
	      success(user){
	        $.ajax({
	          url: '/item_data',
	          success(items){
	            $.ajax({
	              url: '/news_data',
	              success(news){
	
	              },
	              error(){
	                alert('数据获取失败');
	              }
	            })
	          },
	          error(){
	            alert('数据获取失败');
	          }
	        })
	      },
	      error(){
	        alert('数据获取失败');
	      }
	    })
	  },
	  error(){
	    alert('数据获取失败');
	  }
	})

```

使用Promise

Promise.all()     与：所有的都成功

Promise.race()     或：只要有一个完成

```js
	//Promise
	Promise.all([
	  $.ajax('/banner_data'),
	  $.ajax('/item_data'),
	  $.ajax('/user_data'),
	  $.ajax('/news_data'),
	]).then(arr=>{
	  let [banners, items, user, news]=arr;
	}, ()=>{})

```

1.txt

```
	{"a": 3, "b": 5}

```
2.txt

```
	[12,5,8,3]

```
3.txt

```
	{"name": "blue", "age": 18}

```

promise怎么用.html

```html
	<!DOCTYPE html>
	<html>
	
	<head>
	  <meta charset="utf-8">
	  <title></title>
	  <script src="jquery.js" charset="utf-8"></script>
	  <script>
	    let p = new Promise((resolve, reject) => {
	      //resolve       解决->成功
	      //reject        拒绝->失败
	      $.ajax({
	        url: '1.txt',
	        dataType: 'json',
	        success(json) {
	          resolve(json)
	        },
	        error(err) {
	          reject(err)
	        }
	      })
	    })
	
	    debugger
	    p.then(json => {
	      alert('成功')
	      console.log(json)
	    }, err => {
	      alert('失败')
	    })
	  </script>
	</head>
	
	<body>
	
	</body>
	
	</html>


```
promise怎么用2.html

```html
	<!DOCTYPE html>
	<html>
	
	<head>
	  <meta charset="utf-8">
	  <title></title>
	  <script src="jquery.js" charset="utf-8"></script>
	  <script>
	    let p = new Promise((resolve, reject) => {
	      //resolve       解决->成功
	      //reject        拒绝->失败
	      $.ajax({
	        url: '1.txt',
	        dataType: 'json',
	        success(json) {
	          resolve(json)
	        },
	        error(err) {
	          reject(err)
	        }
	      })
	    })
	
	    let p2 = new Promise((resolve, reject) => {
	      //resolve       解决->成功
	      //reject        拒绝->失败
	      $.ajax({
	        url: '2.txt',
	        dataType: 'json',
	        success(json) {
	          resolve(json)
	        },
	        error(err) {
	          reject(err)
	        }
	      });
	    });
	
	    let p3 = new Promise((resolve, reject) => {
	      //resolve       解决->成功
	      //reject        拒绝->失败
	      $.ajax({
	        url: '3.txt',
	        dataType: 'json',
	        success(json) {
	          resolve(json)
	        },
	        error(err) {
	          reject(err)
	        }
	      })
	    })
	
	    Promise.all([p, p2, p3]).then(arr => {
	      let [j1, a, j2] = arr;
	
	      alert('成功')
	      console.log(j1, a, j2)
	    }, err => {
	      alert('失败')
	    })
	  </script>
	</head>
	
	<body>
	
	</body>
	
	</html>


```
promise怎么用3.html

```html
	<!DOCTYPE html>
	<html>
	  <head>
	    <meta charset="utf-8">
	    <title></title>
	    <script src="jquery.js" charset="utf-8"></script>
	    <script>
	    $.ajax({
	      url: '1.txt',
	      dataType: 'json'
	    }).then(json=>{
	      alert('成了');
	    }, err=>{
	      alert('错了');
	    });
	
	    debugger
	
	    /*
	    Promise.all([p, p2, p3]).then(arr=>{
	      let [j1, a, j2]=arr;
	
	      alert('成功');
	      console.log(j1, a, j2);
	    }, err=>{
	      alert('失败');
	    });
	    */
	    </script>
	  </head>
	  <body>
	
	  </body>
	</html>


```
promise怎么用4.html

```html
	<!DOCTYPE html>
	<html>
	  <head>
	    <meta charset="utf-8">
	    <title></title>
	    <script src="jquery.js" charset="utf-8"></script>
	    <script>
	    Promise.all([
	      $.ajax({url: '1.txt', dataType: 'json'}),
	      $.ajax({url: '2.txt', dataType: 'json'}),
	      $.ajax({url: '3.txt', dataType: 'json'}),
	    ]).then(arr=>{
	      let [j1, a, j2]=arr;
	
	      console.log(j1, a, j2);
	    }, err=>{
	      alert('失败');
	    });
	    </script>
	  </head>
	  <body>
	
	  </body>
	</html>

```

结论

- Proimse有用——解除异步操作
- Promise有局限——带逻辑的异步操作麻烦


2、generator-生成器（过渡）

可以暂停

generator函数.html

```html
	<!DOCTYPE html>
	<html>
	
	<head>
	  <meta charset="utf-8">
	  <title></title>
	  <script>
	    function* show() {
	      alert('aaa')
	
	      yield
	
	      alert('bbb')
	    }
	
	    let gen = show()
	
	    gen.next() //aaa
	
	    setTimeout(function() {
	      gen.next() //bbb
	    }, 5000)
	  </script>
	</head>
	
	<body>
	
	</body>
	
	</html>


```
generator函数3.html

传参

```html
	<!DOCTYPE html>
	<html>
	  <head>
	    <meta charset="utf-8">
	    <title></title>
	    <script>
	    function *show(){
	      alert('aaa');
	
	      let a=yield;
	
	      alert('bbb'+a);
	    }
	
	    let gen=show();
	
	    gen.next();
	
	    gen.next(12);
	    </script>
	  </head>
	  <body>
	
	  </body>
	</html>


```

generator函数4.html

返回值

```html
	<!DOCTYPE html>
	<html>
	
	<head>
	  <meta charset="utf-8">
	  <title></title>
	  <script>
	    function* show() {
	      alert('aaa')
	
	      yield 55
	
	      alert('bbb')
	
	      return 89
	    }
	
	    let gen = show()
	
	    let res1 = gen.next()
	    // debugger
	    console.log(res1) //{value: 55, done: false}
	
	    let res2 = gen.next()
	    console.log(res2) //{value: 89, done: true}
	  </script>
	</head>
	
	<body>
	
	</body>
	
	</html>


```

3、async/await（推荐，es7）

可以暂停且可以处理带业务逻辑、异常

async和await.html

```html
	<!DOCTYPE html>
	<html>
	
	<head>
	  <meta charset="utf-8">
	  <title></title>
	  <script>
	    function sleep(sec) {
	      return new Promise((resolve, reject) => {
	        setTimeout(function() {
	          resolve()
	        }, sec * 1000)
	      })
	    }
	
	    async function show() {
	      alert('a')
	
	      await sleep(1)
	
	      alert('b')
	
	      await sleep(2)
	
	      alert('c')
	    }
	
	    show()
	  </script>
	</head>
	
	<body>
	
	</body>
	
	</html>

```

处理带业务逻辑

async和await2.html

```html
	<!DOCTYPE html>
	<html>
	
	<head>
	  <meta charset="utf-8">
	  <title></title>
	  <script src="jquery.js" charset="utf-8"></script>
	  <script>
	    (async () => {
	      let data1 = await $.ajax({
	        url: '1.txt',
	        dataType: 'json'
	      })
	      console.log('data1=========>', data1)
	
	      if (data1.a + data1.b < 10) {
	        let data2 = await $.ajax({
	          url: '2.txt',
	          dataType: 'json'
	        })
	
	        alert(data2[0])
	      } else {
	        let data3 = await $.ajax({
	          url: '3.txt',
	          dataType: 'json'
	        })
	
	        alert(data3.name)
	      }
	    })()
	  </script>
	</head>
	
	<body>
	
	</body>
	
	</html>

```

处理异常

async和await3.html

```html
	<!DOCTYPE html>
	<html>
	
	<head>
	  <meta charset="utf-8">
	  <title></title>
	  <script src="jquery.js" charset="utf-8"></script>
	  <script>
	    async function show() {
	      try {
	        let data1 = await $.ajax({
	          url: '1.txt',
	          dataType: 'json'
	        })
	
	        let data2 = await $.ajax({
	          url: '33.txt',
	          dataType: 'json'
	        })
	
	        let data3 = await $.ajax({
	          url: '3.txt',
	          dataType: 'json'
	        })
	
	        console.log(data1, data2, data3)
	      } catch (e) {
	        alert('有问题')
	
	        throw new Error('我错了....')
	      }
	    }
	
	    show()
	  </script>
	</head>
	
	<body>
	</body>
	
	</html>

```