---
title: vue生命周期
date: 2017-03-17 09:48:18
tags:
  - vue.js
categories: 学习
---
先上图
<img src="https://cn.vuejs.org/images/lifecycle.png" alt="">
# beforeCreate
在实例初始化之后，event/watcher时间配置之前被调用。

# created
实例已经创建完成之后被调用。watch/event事件回调执行。（watch的很主要的一个用途用来判断当前路由状态，和新老路由的交替）但是在这个阶段挂在还没开始所以$el实际上是不可见的（主要用来进行一些初始化操作？个人理解）

# beforeMount
在挂载开始之前被调用：相关的 render 函数首次被调用。<strong>该钩子在服务器端渲染期间不被调用。</strong>

# mounted (关键)
el 被新创建的 vm.$el 替换，并挂载到实例上去之后调用该钩子。如果 root 实例挂载了一个文档内元素，当 mounted 被调用时 vm.$el 也在文档内。
这个时候你可以调用一些从action分发的异步请求来获取数据并且对页面进行渲染 （数据初始化，页面形成）

# beforeUpdate
数据更新时调用，发生在虚拟 DOM 重新渲染和打补丁之前。
你可以在这个钩子中进一步地更改状态，这不会触发附加的重渲染过程。

# updated
由于数据更改导致的虚拟 DOM 重新渲染和打补丁，在这之后会调用该钩子。
当这个钩子被调用时，组件 DOM 已经更新，所以你现在可以执行依赖于 DOM 的操作。然而在大多数情况下，你应该避免在此期间更改状态，因为这可能会导致更新无限循环。

# activated
keep-alive 组件激活时调用。

# deactivated
keep-alive 组件停用时调用。

# beforeDestroy
实例销毁之前调用。在这一步，实例仍然完全可用。

# destroyed
Vue 实例销毁后调用。调用后，Vue 实例指示的所有东西都会解绑定，所有的事件监听器会被移除，所有的子实例也会被销毁。
自己在实际项目开发中用到的最多的地方应该是mounted的钩子函数。一些数据的初始化获取和设置都是在其中完成的。