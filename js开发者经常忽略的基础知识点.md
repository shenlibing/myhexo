---
title: js开发者经常忽略的基础知识点
date: 2019-01-03 12:58:55
tags:
---

1、字符串匹配

匹配所有

```js

// Mistake
// 踩到坑了
var str = "David is an Arsenal fan, which means David is great";
str.replace("David", "Darren"); 
// "Darren is an Arsenal fan, which means David is great"

// Desired
// 符合预期
str.replace(/David/g, "Darren"); 
// "Darren is an Arsenal fan, which means Darren is great"

```

忽略大小写

```js

str.replace(/david/gi, "Darren"); 
// "Darren will always be an Arsenal fan, which means Darren will always be great"


```

2、将“类数组”元素（比如 arguments 参数列表、节点列表和属性列表）转换成真正的数组

```js

var nodesArr = Array.prototype.slice.call(document.querySelectorAll("div"));
// "true" array of DIVs
// 得到一个由 div 元素组成的“真正的”数组

var argsArr = Array.prototype.slice.call(arguments);
// changes arguments to "true" array
// 把 arguments 转换成一个“真正的”数组

```

克隆数组

```js

var clone = myArray.slice(0); 
// naive clone
// 浅克隆

```

3、数组的sort方法

简单排序

```js

[1, 3, 9, 2].sort();
// Returns: [1, 2, 3, 9]
// 返回 [1, 2, 3, 9]

```

复杂排序

```js
	
[
    { name: "Robin Van PurseStrings", age: 30 },
    { name: "Theo Walcott", age: 24 },
    { name: "Bacary Sagna", age: 28  }
].sort(function(obj1, obj2) {
    // Ascending: first age less than the previous
    // 实现增序排列：前者的 age 小于后者
    return obj1.age - obj2.age;
});
    // Returns:  
    // [
    //    { name: "Theo Walcott", age: 24 },
    //    { name: "Bacary Sagna", age: 28  },
    //    { name: "Robin Van PurseStrings", age: 30 }
    // ]

```

4、push合并数组

```js

var mergeTo = [4,5,6];
var mergeFrom = [7,8,9];

Array.prototype.push.apply(mergeTo, mergeFrom);

mergeTo; // is: [4, 5, 6, 7, 8, 9]

```

5、join拼接字符串

```js

var str = [
    '<div>',
    '<button id="lucky-draw">Lucky Draw</button>',
    '</div>'
].join('')
log(str)

```


参考

[https://github.com/cssmagic/blog/issues/21](https://github.com/cssmagic/blog/issues/21)