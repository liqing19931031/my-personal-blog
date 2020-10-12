---
title: 关于Object方法的一些整合
date: 2016-08-14 12:21:27
tags:
  - js
categories: 学习
---

# Object.assign()

对象的合并，将所有<strong>可枚举属性</strong>合并到一个对象中并返回
```
let a = { color: 'red' };
let b = { size: '100*100' };
let c = { weight: '200G' };
d = Object.assign(a, b, c);
return d;

```
<!--more-->
，如果有相同键名的话，后者覆盖前者。
对于目标对象，Object.assign()使用[[set]],对于源对象，Object.assign()使用[[get]]，即使用目标对象的setter方法和源对象getter方法，其本质就是定义或者复制一个新的属性。
因此等待合并的源对象中包含了getters，就不太适合用来将源对象合并到原型中。

<i>假如复制的属性到原型里，包括它们的可枚举属性，那么应该使用 Object.getOwnPropertyDescriptor() 和 Object.defineProperty() 。String 和 Symbol 属性都是会被复制的。</i>

# Object.create()

通过指定的原型对象和属性，创建一个新的对象。Object.create(proto, [,. propertiesObject])。
```
function Car (desc) {
    this.desc = desc;
    this.color = "red";
}
Car.prototype = {
    getInfo: function() {
      return 'A ' + this.color + ' ' + this.desc + '.';
    }
};
var car =  Object.create(Car.prototype);
car.color = "blue";
alert(car.getInfo());
```
car 方法继承了来自Car的方法getInfo 
## js 创建对象的方式

### 对象字面量

```
1、const ob = {name: 'HAHA'};
ob 这个对象继承了Object.prototype上面的所有属性。因此可以使用ob.hasOwnProperty(‘a’), hasOwnProperty是Object.prototype的自身属性。Object.prototype的原型为null。
原型链：ob ---> Object.prototype ---> null
2、const arr = ['note', 'notice'];
arr 数组继承与 Array.prototype, indexof,forEach等方法都是从它继承来的。
原型链: arr ---> Array.prototype ---> Object.prototype --> null
3、function func() { return 2 };
函数都继承于Function.prototype, (call, bind等方法都是从它继承而来的)。
原型链: func ---> Function.prototype ---> Object.prototype ---> null
```

### 构造函数

在JS中，构造器其实就是一个普通函数，当使用new操作符来作用这个函数时，它就被称为构造方法(构造函数)。如果没有new关键字而是直接调用的话，相当于在当前作用域上调用，此时函数中如果this的话，this指向当前作用域
```
function Animal() {
  this.color = 'orange';
  console.log(this);
}
var Lucy = new Animal(); // Animal{}
Animal(); // window{}
```

### ES6中的class关键字

```
class Animal {
  constructor(name, age) {
    this.name = name;
    this.age = age;
  }
}
class Dog extends Animal {
  constructor(miniName, age) {
    super(miniName, age);
  }
  get info() {
    return this.name + 'and' + this.age
  }
  set miniName(newName) {
    this.name = newName;
  }
  set miniAge (age) {
    this.age = age;
  }
}
const tidy = new Dog('yo', 2);
```
### 与new关键字相比

Object.create实际上是将第一个参数的属性添加到新创建对象的原型中。即，使用构造函数new Function()定义的属性和方法是无法通过create()方法添加到新创建的对象中的。
如果Object.create第一个参数是null，新创建的对象不会’继承’自任何地方，没有原型，也没有往上追溯的原型。而使用构造函数时，将其原型设置为null，SomeConstructor.prototype = null;新创建的对象将会‘继承’自Object的prototype。

# Object.freeze 对象冻结

待补充

# Object.defineProperty(obj, prop, descriptor)

ES5中新增的一个方法，可以给对象的属性增加更多的控制。三个参数分别是 要修改的对象，修改/新增的属性，属性描述符。

ES5中定义了一个名叫‘属性描述符’的对象，用于描述各种特征，其本身就是一个对象。只能在Object.defineProperty或Object,defineProperties中使用。有4个属性：
<ul>
	<li>configurable: 可配置属性</li>
	<li>enumerable: 可枚举属性</li>
	<li>writable: 可写性</li>
	<li>value: 数据属性</li>
	以及两个存取属性，分别是get和set，可以代替value和writable，且get／set和value／writable不能混用。值默认都为undefined
	<li>get: 读取属性时调用的函数。只指定get则表示属性为只读属性。</li>
	<li>set: 写入时调用的函数。只指定set则表示属性为只写属性。</li>
	```
	let zoo = {};
	Object.defineProperty(zoo, 'number', {
	  value: 37,
	  writable: true,
	  enumerable: true,
	  configurable: true
	});
	```
</ul>

# Object.defineProperties()

```
let zoo = {};
Object.defineProperties(zoo, {
  'prop1': {
    value: '123',
    writable: true
  },
  'prop2': {
    get: function() {
      return 'Hello, Object.defineProperties';
    },
    set: function() {
      this.property2 = 'xxxxxx';
    }
  }
})

```