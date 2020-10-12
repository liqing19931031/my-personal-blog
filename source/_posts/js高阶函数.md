---
title: js高阶函数
date: 2017-10-20 15:38:32
tags:
  - js
categories: 学习
---

# 何为高阶函数

# new 操作符到底做了一些什么事情
例：var obj = new Base(); 

该步一共做了三件事：即 
   ```
   var obj  = {};
   obj.__proto__ = Base.prototype;
   Base.call(obj);
   ```  
第一行，我们创建了一个空对象obj
第二行，我们将这个空对象的__proto__成员指向了Base函数对象prototype成员对象
第三行，我们将Base函数对象的this指针替换成obj。