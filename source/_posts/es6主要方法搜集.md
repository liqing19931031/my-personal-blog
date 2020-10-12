---
title: es6主要方法搜集
date: 2017-05-26 09:53:30
tags:
  - js
categories: 学习
---

# promise 对象 （回调地狱的解决方案）

# proxy 事件代理

# es6解构的集中日常活用

## 数组的合并
```
[...list, { id: '', name: ''}]

```

## async 和 awite的 作用和使用方法

<a href="https://segmentfault.com/a/1190000007535316#articleHeader6">https://segmentfault.com/a/1190000007535316#articleHeader6</a>

## 多参数获取的es6  Promise写法
promise的写法
```
getMusicList().then(res1 => { // 普通 promise 解决多个异步需要上级参数的方案
	return getNewmusicList().then(res2 => [res1, res2])
}).then(ress => {
	const [res1, res2] = ress
})
```
es7的async的写法
```
async as_getMusicList () {
	const res1 = await a_getMusicList()
	const res2 = await a_getMusicList()
}
```

## 并行请求promise all 基于 axios的进一步函数封装
```
fetchAll (params) {
    return new Promise((resolve, reject) => {
      axios.all([...params]).then(axios.spread((...results) => {
        resolve(results)
      })).catch(error => {
        reject(error)
      })
    })
}
```
使用
```
ajax.fetchAll(放置你所有的异步请求promise对象).then(res => {
	ajax请求列表
})
```