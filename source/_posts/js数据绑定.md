---
title: js数据绑定
date: 2017-06-29 10:01:42
tags:
  - js
categories: 学习
---
# 数据绑定的几种方式
发布者-订阅者模式（backbone.js）

脏值检查（angular.js）

数据劫持（vue.js）
# es7 observe （已在es7提案中确认被移除 /(ㄒoㄒ)/~~）

<a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/observe">移步观看</a>

# 改写原型中的get和 set方法 (目前最实用的办法)

## 最简单的数据与数据之间的双向绑定
```
user = {};
    nameValue = '';
    var bindData = function(user, name, bindData){
      Object.defineProperty(user, name, {
        get: function() { return nameValue },
        set: function(newValue) { nameValue = newValue; },
        configurable: true // 让我们可以在之后重新定义这个属性
      })
    }
    bindData(user, 'name', nameValue)
```

## input输入框和数据的绑定
```
obj = {}
var bindInput = function(obj, property, domElem, bindDom) { // 双向绑定全局变量 和 input的值
  Object.defineProperty(obj, property, {
    get: function() { return domElem.value;},
    set: function(newValue) {
      domElem.value = newValue;
    },
    configurable: true // 让我们可以在之后重新定义这个属性 默认为true
  })
}
bindInput(obj, 'name', document.getElementById('myInput'), document.getElementById('bindInput'));
document.getElementById('myInput').onkeydown = function() { // 绑定输入框中的值和dom文本 （要实现这里好像没必要这么做=-=，仅是做一个数据双向绑定说明）
  document.getElementById('bindInput').innerHTML = obj.name;
}
```

## 变量和函数绑定
```
object = {}
    var bindFunction = function(obj, property, callBack) {
      Object.defineProperty(obj, property, {
        set: function(newValue) {
          callBack(newValue)
        },
        configurable: true
      })
    }
    bindFunction(object, 'name', function(data){
      console.log(data)
    })
```