---
title: js算法
date: 2016-10-15 21:03:42
tags:
  - js
categories: 学习
---

# 排序 

## 冒泡排序

<blockquote>
	它重复地走访过要排序的数列，一次比较两个元素，如果他们的顺序错误就把他们交换过来，走访数列的工作是重复进行直到直到没有在需要交换的元素。这个算法名字的由来是因为越小的元素会经交换慢慢“浮”数列的顶端
</blockquote>

```
Array.prototype.bubble_sort = function () {
  let i, j, temp;
  for (i = 0; i < this.length - 1; i++) {
    for (j = 0; j < this.length - 1 - i; j++) {
      if (this[j] > this[j + 1]) {
        temp = this[j];
        this[j] = this[j + 1];
        this[j + 1] = temp;
      }
    }
  }
  return this;
};
const arr = [8, 6, 2, 0];
arr.bubble_sort();  // [0, 2, 6, 8]
```
<!--more-->

## 插入排序
```
Array.prototype.insertion_sort = function () {
  let i, j, temp;
  for (i = 1; i < this.length; i++) { // 第一个元素默认已排序，所以从1开始
    temp = this[i];  // 取出未排序的新元素
    j = i - 1;  // 和已排序元素进行比较
    for (; j >= 0 && this[j] > temp; j--) {   // 从后往前扫描
      this[j + 1] = this[j]  // 将大于新元素的已排序元素位置靠后
    }
    this[j + 1] = temp;  // 直到遇到小于等于新元素的已排序，就将其插入到该元素之后
  }
}
```
## 快速排序
```
Array.prototype.quick_sort = function () {
  const len = this.length;
  const mid = [this[0]];
  let left = [];
  let right = [];
  if (len <= 1) {
    return this.slice(0);
  }
  for (i = 1; i < len; i++) {
    if (this[i] < mid[0]) {
      right.push(this[i]);
    } else {
      left.push(this[i]);
    }
  }
  return left.quick_sort().concat(mid, right.quick_sort());
}
```
## 希尔排序

<a href="http://math.hws.edu/eck/jsdemo/sortlab.html">希尔算法</a>

