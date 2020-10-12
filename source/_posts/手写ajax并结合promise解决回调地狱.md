---
title: 手写ajax并结合promise解决回调地狱
date: 2017-06-02 11:03:41
tags:
  - js
categories: 学习
---
# 异步请求的封装
```
let createXHR = function () { // 实例化XMLHttpRequest 对象
	if (window.XMLHttpRequest) {
		return new XMLHttpRequest()
	} else {
	}
}
let params = function (data) { // 序列化请求参数 (目前只能应用一级参数请求) 序列化数据
	let arr = []
	for (let i in data) {
		if (typeof data[i] === 'object') {
			for (let j in data[i]) {
				arr.push(encodeURIComponent(`${i}[${j}]`) + '=' + encodeURIComponent(data[i][j]))
			}
		} else {
			arr.push(encodeURIComponent(i) + '=' + encodeURIComponent(data[i]))
		}
	}
	return arr.join('&')
}
let ajax = function (obj) {
	return new Promise(function (resolve, reject) { // 返回promise 对象实例 并接受resolve 和reject 两个参数
		let xhr = createXHR()
		obj.url = obj.url //  + '?rand=' + Math.random()
		obj.data = params(obj.data)
		if (obj.method === 'get') {
      obj.url += obj.url.indexOf('?') === -1 ? '?' + obj.data : '&' + obj.data
	  }
	  if (obj.async === true) {
			xhr.withCredentials = true // 带cookie请求
	  	xhr.onreadystatechange = function () {
	  		if (xhr.readyState === 4) {
	  			resolve(JSON.parse(xhr.responseText)) // 成功分支
	  		}
	  	}
	  }
		xhr.open(obj.method, obj.url, obj.async)
	  if (obj.method === 'post') {
	      xhr.setRequestHeader('Content-Type', 'application/x-www-form-urlencoded')
	      xhr.send(obj.data)
	  } else {
	      xhr.send(null)
	  }
	  if (obj.async === false) {
	      resolve(JSON.parse(xhr.responseText))
	  }
	})
}

export default ajax

```
调用
```
ajax({
	method: method,
    url: url,
    data: data,
    async: true
}).then((data) =>{
	// TO DO
	return ajax({
		method: method,
	    url: url,
	    data: data,
	    async: true
	})
}).then((data) => {
	// TO DO
	return ajax({
		method: method,
	    url: url,
	    data: data,
	    async: true
	})
})
```