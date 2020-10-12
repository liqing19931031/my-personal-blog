---
title: 关于Vue的一些小技巧
tags: 
  - vue.js
categories: 学习
date: 2017-01-17 10:48:14
---

# 关于申明一些公共的方法和常量。（这个方法不一定好但是确实是实用，和根store有异曲同工之妙,不过这边一般是放置一些常量）

``` bash
Vue.prototype.xx = xx;
```
<!--more-->

# 在Vue中添加其他事件
在vue中监听各种各样的事件：这边以滚动事件为例子 键盘和鼠标的其他事件也同理（貌似现在原生Vue中只支持点击事件）

``` bash
data () {
  return {
    scrolled: false
  };
},
methods: {
  handleScroll () {
    this.scrolled = window.scrollY > 0;
  }
},
ready () {
  window.addEventListener('scroll', this.handleScroll);
}
```

# 关于vue的post请求的参数不能直接在后面添加的问题 
（1）一种是序列化对象当中字符串的形式直接拼接在url后面当然这条就是为了解决这个问题的所以第一种我不建议因为感觉好麻烦而且也不够灵活 
（2）

``` bash
this.$http.get(`${this.API_ROOT}/backendx/activity/list`, {params: this.searchParams}).then((response) => {

})
```
必须要在前面加上params参数才能让后端正确的获取到参数 

# vue2.0子组件修改传入内部的props
```
<child :bindData='this.bindData'></child>
data () {
	return {
		bindData: {
			key: value
		}
	}
}
```
子组件修改父组件的值 （对象引用类型）

```
<child :bindData='this.bindData'></child>
data () {
	return {
		bindData: ''
	}
}
```
子组件无法修改父组件的值 （字符串基本类型）

当然还有一种传值的方式，比如说表单的某个字段
```
<template>
  <div>
    <input type="text" v-model="msg">
    <br>
    //将子控件属性inputValue与父组件msg属性绑定
    <child :inputValue="msg"></child>
  </div>
</template>
<style>

</style>
<script>
  export default{
    data(){
      return {
        msg: '请输入'
      }
    },
    components: {
      child: require('./Child.vue')
    }
  }
</script
复制代码
```

如果父组件传过来的不是一个对象或者数组（比如说是单纯的字符串或者是数字）由于数据类型是基本类型而并非引用类型而导致不能修改，而传入的是一个完整对象的时候由于是引用类型他是可修改的。（原有的分发$dispatch 和 广播的$brodcost已被删除）

# @change事件 接受多个参数

```
<div v-if="item.name == key && item.dataType == 'Date'">
    <el-date-picker v-model="editForm[item.name]" placeholder="选择日期"
        :editable="false" style="width:100%"
        @change="(value) => changeHandler(value, item.name)">
    </el-date-picker>
</div>
```

```
methods: {
    // value是空间选择的值，itemName是你要的值
    changeHandler(value, itemName) {
        console.log('value', value)
        console.log('itemName', itemName)
    }
}
```

# 子组件给父组件传值 第二次失效的问题

```
changeData (images) {
  this.imgs = []
  this.$set(this, 'imgs', images)
}
```

# vue数组的监听

需要注意的是，Vue之所以能够监听Model状态的变化，是因为JavaScript语言本身提供了Proxy或者Object.observe()机制来监听对象状态的变化。但是，对于数组元素的赋值，却没有办法直接监听，因此，如果我们直接对数组元素赋值：vm.todos[0] = {
    name: 'New name',
    description: 'New description'
};
会导致Vue无法更新View。正确的方法是不要对数组元素赋值，而是更新：vm.todos[0].name = 'New name';
vm.todos[0].description = 'New description';
或者，通过splice()方法，删除某个元素后，再添加一个元素，达到“赋值”的效果：var index = 0;
var newElement = {...};
vm.todos.splice(index, 1, newElement);
Vue可以监听数组的splice、push、unshift等方法调用，所以，上述代码可以正确更新View。
