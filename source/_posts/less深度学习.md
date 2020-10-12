---
title: less深度学习
date: 2017-04-11 10:12:39
tags:
  - css
  - less
categories: 学习
---

# 根据bootstrap 栅栏布局实现 生成无限个数不同位置流星
// 全局申明 星星的颜色和尾巴的颜色
```
@color-star: rgb(2,124,210);
```
// 流星公共mix的编写（待抽象）
```
.star-point(@top;@left;@color) {
	transition: all 4s ease-in-out;
	transform: rotate(180deg);
	display: block;  
    width: 6px;  
    height: 6px;
    background: rgb(2,124,210);
    border-radius: 50%;
    top: @top;
    left: @left;
    position: absolute;
    box-shadow: 0 0 5px 0px rgba(2,124,210, 0.3); 
}
.star-line(@width; @color) {
	content: '';  
    display: block;  
    top: 1px;  
    left: 4px;
    border: 0px solid #fff; 
    border-width: 2px @width;
    position: relative;
	border-color: transparent transparent transparent rgba(2,124,210, 0.3);  
}
```
// 循环生成class方法
```
.make-stars-point() {
	.col(@index) {
		@item: ~".star-@{index}"; // 初始化 申明list
		.col((@index + 1), @item);// index + 1并调用自身函数此时所传第二个参数就是classname（精髓所在）
	}
	.col(@index, @list) when (@index =< 2) {
		@item: ~".star-@{index}";
        .col((@index + 1), ~"@{list}, @{item}");
	}
    .col(@index, @list) when (@index > 2) { // 大于时 做classname样式生成
        @{list} {
            float: left;
        }
    }
    .col(1);
}
.make-stars-common(@index) when (@index > 0){
    .star-@{index} { // 与上一步classname对应添加样式
        .star-point(@index * 180px, @index * 150px, @color-star); // 调用之前申明（less支持各种计算，可生成随机数，less拥有自己的内置函数）
        &:before{
            .star-line(@index * 200px, @color-star); // 伪类调用生成流行尾巴mix
        }
    }
    .make-stars-common((@index - 1)); // index-1并调用自身知道index = 0
}
.make-stars-common(2);
```