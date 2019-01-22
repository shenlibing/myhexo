
---
title: js面向对象_箭头函数this_数组常用方法
date: 2019-01-04 12:58:55
tags:
---

1、js面向对象

1)传统的面向对象

```html

<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title></title>
    <script>
    function Person(name, age){
      this.name=name;
      this.age=age;
    }

    Person.prototype.showName=function (){
      alert('我叫'+this.name);
    };
    Person.prototype.showAge=function (){
      alert('我'+this.age+'岁');
    };




    let p=new Person('blue', 18);

    p.showName();
    p.showAge();
    </script>
  </head>
  <body>

  </body>
</html>


```

继承

```html

<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title></title>
    <script>
    function Person(name, age){
      this.name=name;
      this.age=age;
    }

    Person.prototype.showName=function (){
      alert('我叫'+this.name);
    };
    Person.prototype.showAge=function (){
      alert('我'+this.age+'岁');
    };

    //------------------------------------------------
    function Worker(name, age, job){
	  //通过call调用父类构造函数	
      Person.call(this, name, age);
      this.job=job;
    }

	//使得子类实例对象共享父类原型对象上的方法
    Worker.prototype=new Person();
	//重写子类原型对象的构造，特别恶心
    Worker.prototype.constructor=Worker;
    Worker.prototype.showJob=function (){
      alert('我是做：'+this.job);
    };

    let w=new Worker('blue', 18, '打杂的');

    w.showName();
    w.showAge();
    w.showJob();
    </script>
  </head>
  <body>

  </body>
</html>


```

2）js标准的面向对象

```html

<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title></title>
    <script>
    /*function Person(name, age){
      this.name=name;
      this.age=age;
    }

    Person.prototype.showName=function (){
      alert('我叫'+this.name);
    };
    Person.prototype.showAge=function (){
      alert('我'+this.age+'岁');
    };*/

	//标准面向对象书写方式
    class Person{
      constructor(name, age){
        this.name=name;
        this.age=age;
      }

      showName(){
        alert('我叫'+this.name);
      }
      showAge(){
        alert('我'+this.age+'岁');
      }
    }

    let p=new Person('blue', 18);

    p.showName();
    p.showAge();

    //------------------------------------------------
    function Worker(name, age, job){
      Person.call(this, name, age);
      this.job=job;
    }

    Worker.prototype=new Person();
    Worker.prototype.constructor=Worker;
    Worker.prototype.showJob=function (){
      alert('我是做：'+this.job);
    };
    </script>
  </head>
  <body>

  </body>
</html>


```

标准的继承方式

```html

<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title></title>
    <script>
    /*function Person(name, age){
      this.name=name;
      this.age=age;
    }

    Person.prototype.showName=function (){
      alert('我叫'+this.name);
    };
    Person.prototype.showAge=function (){
      alert('我'+this.age+'岁');
    };*/

	//标准面向对象书写方式
    class Person{
      constructor(name, age){
        this.name=name;
        this.age=age;
      }

      showName(){
        alert('我叫'+this.name);
      }
      showAge(){
        alert('我'+this.age+'岁');
      }
    }

    /*let p=new Person('blue', 18);

    p.showName();
    p.showAge();*/

    //------------------------------------------------
    /*function Worker(name, age, job){
      Person.call(this, name, age);
      this.job=job;
    }

    Worker.prototype=new Person();
    Worker.prototype.constructor=Worker;
    Worker.prototype.showJob=function (){
      alert('我是做：'+this.job);
    };*/

	//标准继承方式，子类通过extends实例化的子类可以访问到父类原型对象的方法
    class Worker extends Person{
      constructor(name, age, job){
        //super-超类(父类)，可以访问到构造方法，不需要通过call的方式调用
        super(name, age);
        this.job=job;
      }

      showJob(){
        alert('我是做：'+this.job);
      }
    }

    let w=new Worker('blue', 18, '打杂的');

    w.showName();
    w.showAge();
    w.showJob();
    </script>
  </head>
  <body>

  </body>
</html>


```


2、箭头函数this

普通函数：根据调用我的人  this老变

箭头函数：根据所在的环境  this恒定

bind——给函数定死一个this

例1：

```html

<!DOCTYPE html>
<html>

<head>
  <meta charset="utf-8">
  <title></title>
  <script>
    let arr = [1, 2, 3];
	
	//箭头函数所处的环境是window
    arr.a = () => {
      console.log(this);
    };

    arr.a();
  </script>
</head>

<body>

</body>

</html>
	

```

输出

![](https://i.imgur.com/qtX6dUP.png)

例2：

```html

<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title></title>
    <script>
    document.onclick=function (){
      let arr=[1,2,3];
	  
	  //箭头函数所处的环境是document	
      arr.a=()=>{
        console.log(this);
      };

      arr.a();
    };
    </script>
  </head>
  <body>

  </body>
</html>


```

输出

![](https://i.imgur.com/k2XFX4S.png)


例3：

箭头函数所处的环境是document，尽管通过bind绑定死了12，但还是输出document，说明箭头函数的this优先级高于bind方式绑定的this

```html

<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title></title>
    <script>
    document.onclick=function (){
	
      let a=()=>{
        console.log(this);
      };

      let oBtn=document.getElementById('btn1');
      oBtn.onclick=a.bind(12);
    };
    </script>
  </head>
  <body>
    <input type="button" value="aaa" id="btn1">
  </body>
</html>


```

输出

![](https://i.imgur.com/kJzVa09.png)


3、数组常用方法

1）map 映射

```html

<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title></title>
    <script>
    let arr=[62, 55, 82, 37, 26];

    let arr2=arr.map(function (item){
      if(item>=60){
        return true;
      }else{
        return false;
      }
    });

	//true,false,true,false,false
    alert(arr2);
    </script>
  </head>
  <body>

  </body>
</html>


```


改进1

map参数：回调函数使用箭头函数书写方式

```html

<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title></title>
    <script>
    let arr=[62, 55, 82, 37, 26];

    let arr2=arr.map((item)=>{
      if(item>=60){
        return true;
      }else{
        return false;
      }
    });

    alert(arr2);
    </script>
  </head>
  <body>

  </body>
</html>


```

改进2

如果有且仅有1个参数，()可以省

```html

<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title></title>
    <script>
    let arr=[62, 55, 82, 37, 26];

    let arr2=arr.map(item=>{
      if(item>=60){
        return true;
      }else{
        return false;
      }
    });

    alert(arr2);
    </script>
  </head>
  <body>

  </body>
</html>


```

改进3

如果函数体只有一句话，而且是return，{}可以省

```html

<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title></title>
    <script>
    let arr=[62, 55, 82, 37, 26];

    let arr2=arr.map(item=>item>=60);

    alert(arr2);
    </script>
  </head>
  <body>

  </body>
</html>


```

2）filter过滤

```html

<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title></title>
    <script>
    let arr=[12,5,88,37,21,91,17,24];

    let arr2=arr.filter(item=>item%2);

	//5,37,21,91,17
    alert(arr2);
    </script>
  </head>
  <body>

  </body>
</html>


```


3）forEach 遍历

求和

```html

<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title></title>
    <script>
    let arr=[12,5,88,37,21,91,17,24];

    let sum=0;
    arr.forEach(item=>{
      sum+=item;
    });

	//结果295
    alert(sum);
    </script>
  </head>
  <body>

  </body>
</html>
	

```


4）reduce  汇总

参数

tmp:中间结果

item：元素项

index：元素索引

求和

```html

<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title></title>
    <script>
    let arr=[12,5,88,37,21,91,17,24];

    let sum=arr.reduce((tmp,item,index)=>{
      console.log(tmp, item, index);

      return tmp+item;
    });

    console.log(sum);
    </script>
  </head>
  <body>

  </body>
</html>


```

输出

![](https://i.imgur.com/ko7C3rr.png)

求平均

```html

<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title></title>
    <script>
    let arr=[12,5,88,37,21,91,17,24];

    let sum=arr.reduce((tmp,item,index)=>{
      return tmp+item;
    });

	//结果36.875
    console.log(sum/arr.length);
    </script>
  </head>
  <body>

  </body>
</html>


```

5）from

将类数组转换成数组

```html

<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title></title>
    <style media="screen">
      div {width:200px; height:200px; background:#CCC; float:left; margin:10px;}
    </style>
    <script>
    window.onload=function (){
	  
	  //DOM对象	
      let aDiv=document.getElementsByTagName('div');
      console.log(aDiv);
	
	  //数组	
      let aDiv2=Array.from(aDiv);
      console.log(aDiv2);

      Array.from(aDiv).forEach(div=>{
        div.style.background='yellow';
      });

      Array.prototype.slice.call(aDiv).forEach(div=>{
        div.style.background='blue';
      });


    };
    </script>
  </head>
  <body>
    <div class=""></div>
    <div class=""></div>
    <div class=""></div>
    <div class=""></div>
    <div class=""></div>
  </body>
</html>


```

输出

![](https://i.imgur.com/VQgqT0r.png)