---
title: es6的类与es5的继承
date: 2017-03-16 15:31:43
tags:
  - js
categories: 学习
---

在ES5中，由于没有类的概念，所以如果要使用面向对象编程的方式，就需要利用原型继承的方式。通常是创建一个构造器，然后将方法指派到该构造器的原型上。

# ES5的继承方式

## 构造函数绑定
```
function Animal(name) {
  	this.species = "动物";
  	this.getData = function() {
  		return this
  	}
}

function Cat(name,color){
	Animal.apply(this, arguments);
　　this.name = name;
　　this.color = color;
}
```
相当于用call和apply将需要继承的方法Animal构造函数绑定在子对象上

## 原型模式
```
function Animal(name) {
  	this.species = "动物";
  	this.getData = function() {
  		return this
  	}
}
Cat.prototype = new Animal();
Cat.prototype.constructor = Cat;
var cat1 = new Cat("大毛","黄色");
```
在Cat的prototype对象，指向Animal 的实例。
而我们知道每个function 对象中都有prototype 原型这个属性，而他们的<strong>prototype</strong>指向的对象都包含唯一一个不可枚举属性，也就是<strong>constructor</strong>，该属性的值是这么一个对象：它指向了它所在的构造函数。也就是说<strong>Animal.prototype.constructor = Animal</strong>
<strong>Cat.prototype.constructor = Cat;</strong>这句话让Cat的原型对象的引用又指向了自身
如果没有上面这句话，所有通过构造函数Cat实例化得到的对象的实际上指向的引用对象变成了Animal <strong>因为（之前相当于直接在原型上赋值 = Animal的实例对象）</strong>这样就会造成继承链的紊乱（cat1明明是用构造函数Cat生成的），因此我们必须手动纠正。

## 直接继承prototype

我们先将Animal对象改写
```
function Animal(){}
Animal.prototype.species = '动物';
```
然后，将Cat的prototype对象，然后指向Animal的prototype对象，这样就完成了继承。
```
Cat.prototype = Animal.prototype;
　　Cat.prototype.constructor = Cat;
　　var cat1 = new Cat("大毛","黄色");
　　alert(cat1.species); // 动物
```

## 利用空对象作为中介
```
var F = function(){};
F.prototype = Animal.prototype;
Cat.prototype = new F();
Cat.prototype.constructor = Cat;
```
我们将上面的方法，封装成一个函数，便于使用。
```
function extend(Child, Parent) {
　var F = function(){};
　F.prototype = Parent.prototype;
　Child.prototype = new F();
　Child.prototype.constructor = Child;
　Child.uber = Parent.prototype;
}
```
## 拷贝继承

上面是采用prototype对象，实现继承。我们也可以换一种思路，纯粹采用"拷贝"方法实现继承。简单说，如果把父对象的所有属性和方法，拷贝进子对象，不也能够实现继承吗？这样我们就有了第五种方法。
首先，还是把Animal的所有不变属性，都放到它的prototype对象上。
```
function Animal(){}
Animal.prototype.species = "动物";
function extend2(Child, Parent) {
　　　　var p = Parent.prototype;
　　　　var c = Child.prototype;
　　　　for (var i in p) {
　　　　　　c[i] = p[i];
　　　　　　}
　　　　c.uber = p;
　　}
```
