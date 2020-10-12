---
title: js性能优化
date: 2016-12-15 21:43:42
tags:
  - js
categories: 学习
---

# 定时器
 如果针对的是不断运行的代码，不应该使用
 ，而应该是用setInterval，因为setTimeout每一次都会初始化一个定时器，而setInterval只会在开始的时候初始化一个定时器
 ```
var timeoutTimes = 0;
function timeout() {
    timeoutTimes++;
    if (timeoutTimes < 10) {
        setTimeout(timeout, 10);
    }
}
timeout();
//可以替换为：
var intervalTimes = 0;
function interval() {
    intervalTimes++;
    if (intervalTimes >= 10) {
        clearInterval(interv);
    }
}
var interv = setInterval(interval, 10);
 ```

<!-- more -->
# 字符串拼接
少用+=，最理想的方式是用join 的方法做数组拼接。
```
s += a + b + c
s = [s, a, b, c].join()
```
# 数字转换成字符串
```
String() > .toString() 
```
# 字符串转换成数字
```
( +item) > parseInt()
```

# 浮点数转换成整型
很多人喜欢使用parseInt()，其实parseInt()是用于将字符串转换成数字，而不是浮点数和整型之间的转换，我们应该使用Math.floor()或者Math.round()

# 各种类型转换
```
var myVar = "3.14159",
str = "" + myVar, //  to string  
i_int = ~ ~myVar,  //  to integer  
f_float = 1 * myVar,  //  to float  
b_bool = !!myVar,  /*  to boolean - any string with length 
                        and any number except 0 are true */
array = [myVar];  //  to array 
```

# 使用一次innerHTML赋值代替构建dom元素
```
var frag = document.createDocumentFragment();
        for (var i = 0; i < 1000; i++) {
            var el = document.createElement('p');
            el.innerHTML = i;
            frag.appendChild(el);
        }
        document.body.appendChild(frag);
        //可以替换为：
        var html = [];
        for (var i = 0; i < 1000; i++) {
            html.push('<p>' + i + '</p>');
        }
        document.body.innerHTML = html.join('');
```

# 删除DOM 节点
删除dom节点之前,一定要删除注册在该节点上的事件,不管是用observe方式还是用attachEvent方式注册的事件,否则将会产生无法回收的内存。另外，在removeChild和innerHTML=’’二者之间,尽量选择后者. 因为在sIEve(内存泄露监测工具)中监测的结果是用removeChild无法有效地释放dom节点

# 事件代理
任何可以冒泡的事件都不仅仅可以在事件目标上进行处理，目标的任何祖先节点上也能处理，使用这个知识就可以将事件处理程序附加到更高的地方负责多个目标的事件处理，同样，对于内容动态增加并且子节点都需要相同的事件处理函数的情况，可以把事件注册提到父节点上，这样就不需要为每个子节点注册事件监听了。另外，现有的js库都采用observe方式来创建事件监听,其实现上隔离了dom对象和事件处理函数之间的循环引用,所以应该尽量采用这种方式来创建事件监听

# 活用&& || 
如果存在则执行
```
callback.reject && callback.reject()

function a(x) {
    let b = x || '默认'
}
```
# es6展开运算符的活用
## 合并数组
```
var arr1 = ['two', 'three'];
var arr2 = ['one', ...arr1, 'four', 'five'];
```
## 使用Math函数
```
let numbers = [9, 4, 7, 1];
Math.min(...numbers); // 1
```
## 解构
```
let { x, y, ...z } = { x: 1, y: 2, a: 3, b: 4 };
console.log(x); // 1
console.log(y); // 2
console.log(z); // { a: 3, b: 4 }
```