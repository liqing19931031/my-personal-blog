---
title: css小知识点
date: 2017-01-15 14:47:58
tags:
  - css
categories: 学习
---
# css优先级汇总
```
I——Id；（id选择器）
C——Class；（类选择器）
E——Element；（派生选择器）
E = 1
C = 10
I = 100
important = 正无穷 秒杀所有
* = 负无穷 低于所有 
外联 < 内联 < 内嵌
```

# 盒模型

## 一般盒模型
标准盒模型，内容为实际内容；padding、border不算在整体宽高之内；外边距margin合并
所有行内元素设置宽高没有效果
<img src='http://image79.360doc.com/DownloadImg/2014/10/1001/45984178_1.jpeg' style="height: 300px"></img>
ps: 当内部的padding超过整体的高度的时候 总体高度会以padding的高度来计算
## 怪异盒模型
怪异盒模型，内容的实际宽高为： 主题内容宽高 + padding + border；外边距margin合并
行内元素宽高设置有效果
<img src='http://image79.360doc.com/DownloadImg/2014/10/1001/45984178_2.jpeg' style='height: 300px'></img>
<!--more-->
# CSS（css3）垂直居中的方法

## 已知高度的情况
有个属性叫line-height 这个时候只要把行高设置成和本身块级的高度一致就能达到居中效果

## 未知容器高度
transform 中的translate 属性
```
chuizhijuzhong{
	position: relative; /*脱离文档流*/
    top: 50%; /*偏移*/
    transform: translateY(-50%);
}
```
没错就是利用脱离文档流上下偏移自身百分之50%的方式来做 （做法和margin-top: -50%原理相同）

## 弹性布局felx
```
body {
        display: flex;
        align-items: center; /*定义body的元素垂直居中*/
        justify-content: center; /*定义body的里的元素水平居中*/
    }
```
如果你确保不会被你的领导命令要兼容各种 浏览器的话，兄弟放心的去用它吧~

# cookies sessionStorage和localStorage 的区别
## sessionStorage
sessionStorage用于本地存储一个会话（session）中的数据，这些数据只有在同一个会话中的页面才能访问并且当会话结束后数据也随之销毁。因此sessionStorage不是一种持久化的本地存储，仅仅是会话级别的存储。而localStorage用于持久化的本地存储，除非主动删除数据，否则数据是永远不会过期的。
## Web Storage
Web Storage的概念和cookie相似，区别是它是为了更大容量存储设计的。Cookie的大小是受限的，并且每次你请求一个新的页面的时候Cookie都会被发送过去，这样无形中浪费了带宽，另外cookie还需要指定作用域，不可以跨域调用。
除此之外，Web Storage拥有setItem,getItem,removeItem,clear等方法，不像cookie需要前端开发者自己封装setCookie，getCookie。
## Cookie 
Cookie的作用是与服务器进行交互，作为HTTP规范的一部分而存在 ，而Web Storage仅仅是为了在本地“存储”数据而生。

# 缓存的种类
dns缓存，cdn缓存，浏览器缓存，服务器缓存。

# content 属性的应用 对checkbox 和radio 的重绘
radio
```
input[type="radio"]{
  width: 16px;
  height: 16px;
}
input[type="radio"]:before{
  content: '\e6df';
  display: inline-block;
  width: 16px;
  height: 16px;
  cursor: pointer;
  position: absolute;
  top: 0;
  left: 0;
  font-size: 17px;
  text-indent: -1px;
  border-radius: 50%;
  border: 1px solid silver;
  vertical-align: middle;
  line-height: .87;
  font-family: 'Glyphicons Halflings';
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  background: white;
  color: white;
  transition: all 0.15s;
}
input[type="radio"]:hover:before{
  content: '\e6df';
  color: #007be3;
}
input[type="radio"]:checked:before{
  content: '\e6df';
  color: #007be3;
}
input[type="radio"]{
  position: relative;
  clip: rect(0, 0, 0, 0);
}
```
checkbox
```
input[type="checkbox"]{
  width: 16px;
  height: 16px;
}
input[type="checkbox"]:before{
  content: '\e60b';
  display: inline-block;
  position: absolute;
  top: 0;
  left: 0;
  width: 16px;
  height: 16px;
  cursor: pointer;
  text-indent: 1px;
  border-radius: 4px;
  vertical-align: middle;
  font-family: 'Glyphicons Halflings';
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  background: silver;
  line-height: 1.4;
  transition: all 0.15s;
  color: silver;
}
input[type="checkbox"]:hover:before{
  content: '\e60b';
  color: white;
}
input[type="checkbox"]:checked:before{
  content: '\e60b';
  color: white;
  background: #43cb83;
}
input[type="checkbox"]{
  position: relative;
  clip: rect(0, 0, 0, 0);
}
```
# css3图片处理
```
img {
    filter: grayscale(100%); //灰度
    filter: blur(5px); //模糊
    filter:brightness(200%); //高亮
    filter:saturate(8); //饱和
    filter:sepia(100%); //怀旧
    ...
}
```
# 创建三角形
```
div {
    border-bottom: 10px solid white;
    border-right: 10px solid transparent;
    border-left: 10px solid transparent;
    height: 0px; 
    width: 0px; 
}
```

# 清除浮动的方式和优缺点（内容塌陷）

## 伪元素
```
.clearfix {
    zoom: 1;
}

.clearfix:after {
     visibility: hidden;
     display: block;
     font-size: 0;
     content: " ";
     clear: both;
     height: 0;
 }
```
利用伪元素的特性在子元素中生成一个空的div清除浮动（bootstrap所用的清除浮动的方式就是这个）
优点：浏览器支持好，不容易出现怪问题

## overflow: hidden
父级div定义overflow:hidden
相当于形成了一个新的块级但是不能和position配合使用，因为超出的尺寸的会被隐藏（新手常用老司机不建议）

## height
既然我们要解决的是内容塌陷的问题那我们就在父级加上高让他变成有自己的高（当高度需要自适应的时候此法自然不可用了）

<a href="http://elrumordelaluz.github.io/csshake/">csshade 抖动</a>
<a href="https://daneden.github.io/animate.css/">animate 动画库</a>

# css3中calc计算属性的活用(兼容ie9+ 火狐谷歌)

各种预编译的兼容写法
```
sass: {
  $a: 4em
  height: calc(#{$a} + 7px)
}

less: {
  @a: 4em;
  height: ~"calc(@{a} + 7px)";
}

stylus: {
  a = 4em
  height: "calc(%s + 7px)" % a
}

css原生变量: {
  --a: 4em;
  height: calc(var(--a) + 7px);
}

```
<div style="color: red">
无效写法:
```
calc(50% / 0)  //被0除
calc (1em + 7px)  //calc 与 ( 间有空隙
calc(2rem+2vmin)  //+ 两侧没有空格
calc(2vw-2vh)  //- 两侧没有空格
```
</div>
## 上中下布局

```
min-height: {
  100hv - (topHeight + footerHeight)
}
```
通过计算获得主内容区域的高内容不足一屏时撑满一屏

# scroll重绘
基于webkit内核
```
::-webkit-scrollbar 滚动条整体部分
::-webkit-scrollbar-button 滚动条两端按钮
::-webkit-scrollbar-track 外层轨道
::-webkit-scrollbar-track-piece 内层轨道
```
基于ie内核
```
scrollbar-arrow-color /*三角箭头的颜色*/
scrollbar-face-color  /*立体滚动条的颜色*/
scrollbar-3dlight-color /*立体滚动条亮边的颜色*/
scrollbar-highlight-color  /*滚动条的高亮颜色（左阴影？）*/
scrollbar-shadow-color /*立体滚动条阴影的颜色*/
scrollbar-darkshadow-color /*立体滚动条外阴影的颜色*/ 
scrollbar-track-color /*立体滚动条背景颜色*/
scrollbar-base-color  /*滚动条的基色*/
```

# 用原生的方式获得同时含有两个class的dom

ps: 先得到节点的类数组对象，关键在于classList 属性 进行循环遍历后输出

# flex布局用法详解

<img src="https://dn-mhke0kuv.qbox.me/0dd26d8e99257ff36443.png" alt="">
<a href="http://blog.csdn.net/magneto7/article/details/70854472">详情移步观看</a>

# CSS的渐变色过度

由于background-image 属性是无法过度的由此引用别的技巧
```
.cloud_btn{
  cursor: pointer;
  padding: 8px 52px;
  border-radius: 23px;
  font-size: 24px;
  transition: all ease 1s;
  -webkit-transition: all ease 1s;
  background-image: linear-gradient(to right, #356df6, #00b7ef, #356df6);
  background-position: 0 0;
  background-size: 200% 100%;
}
.cloud_btn:hover{
  background-position: 200px 0;
}
```
过度backgrund-position属性来达到想要的效果