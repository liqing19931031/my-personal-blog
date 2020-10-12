---
title: 让js变的更优雅
date: 2016-11-03 20:47:16
tags:
  - js
categories: 学习
---

# 函数式编程
假设我们有这样的需求，需要先把数组foo中的对象结构更改，然后从中挑选出一些符合条件的对象，并且把这些对象放进新数组result里。
```
let foo = [{
    name: 'Stark',
    age: 21
},{ 
    name: 'Jarvis',
    age: 20
},{
    name: 'Pepper',
    age: 16
}]
```
我们希望得到结构稍微不同，age大于16的对象：
```
let result = [{
    person: {
        name: 'Stark',
        age: 21
    },
    friends: []
},{
    person: {
        name: 'Jarvis',
        age: 20
    },
    friends: []
}]
```
<!--more-->
直接上代码(活用map和filter方法)
```
let result = foo
.filter(person => person.age > 16)
.map(person => ({
    person: person,
    friends: []
}))
```
整型代码求和
```
let foo = [1, 2, 3, 4, 5]
foo.reduce((a, b) => a + b)
```

# Generator + Promise改善异步流程

<a href="http://www.ruanyifeng.com/blog/2015/05/co.html">阮一峰老师的generator解析</a>
```
import co from 'co';

function* foo(){
    var a = yield getSomethingAsync();
    var b = yield method1(a);
    var c = yield method2(b);
    var result = yield method3(a,b,c);
    console.log(result);
}

co(foo());
```

# 图片的懒加载

实现原理 -> document.documentElement.clientHeight 一屏的高度 + window.scrollY 轮播滚过的高度 > offsetTop + 1/2 * offsetHeight 节点到页面顶端的高度加上自身的高度的一半(可算为需加载部分)
```
lazyLoad(imgs, Cheight) {
    for (let img of imgs) {
      if ((img.offsetTop + img.offsetHeight * 0.5 - Cheight) < window.scrollY) {
        img.className = 'on'
        img.setAttribute('src', img.getAttribute('data-url'))
      }
    }
  }
```

先在data-url中存储图片地址 实际上的img中填入加载的图片或者1px*1px的图片
```
this.props.data.map((item, i) =>(
<div key={i} className='shop'>
  <img src='/images/logo.png' className='lazyImg in' data-url={`http://127.0.92.29:3000/images/${item.pic}`} />
  {item.singer}
</div>
))
```

# dom数组节点循环
nodes为类数组类型 不能调用数组当中的nodes.forEach();
将(NodeList)类数组对象转换为数组对象
ps: 类数组对象<strong>如果它的所有property名均为正整数，同时也有相应的length属性，那么虽然该对象并不是由Array构造函数所创建的，它依然呈现出数组的行为，在这种情况下，这些对象被称为“类数组对象”</strong>
例：
    document.getElementsByTagName(),
    arguments
```
nodes = document.getElementsByClassName('.lazyLoad')
nodes = Array.prototype.slice.call(nodes,0)
nodes.forEach(() => {
    
})
```
ES6添加的新方法
```
Array.from(arguments)
```

# 字符数组与数组的区别

```
var a = 'foo'
var b = ['f', 'o', 'o']

a[1] = '0' // 字符串是不可变的故无效
b[1] = '1'
a.charAt(0) === f // true
```

# 数字类型中的点
42.toFixed(3) // Error
42..toFixed(3) // '42.000'