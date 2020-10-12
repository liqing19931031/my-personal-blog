---
title: js设计模式(2)
date: 2017-04-05 10:09:47
tags:
  - js
categories: 学习
---
# 模块设计模式(Module Design Pattern)

JavaScript 模块是最被经常使用的设计模式，它可以使特定部分的代码与其他部分相独立。其提供的松散耦合支撑了良好的代码结构。
对于那些熟悉面向对象语言的开发者额，模块就是JavaScript中的”类”。类的好处之一就是”封装”(encapsulation)——避免自身的状态和行为被其他”类”所获取。模块模式允许公有和私有(加上更少知道的受保护的和特权)访问级别。
```
var HTMLChanger = (function() {
  var contents = 'contents'
  var changeHTML = function() {
    var element = document.getElementById('attribute-to-change');
    element.innerHTML = contents;
  }
  return {
    callChangeHTML: function() {
      changeHTML();
      console.log(contents);
    }
  };
})();
HTMLChanger.callChangeHTML();       // Outputs: 'contents'
```
注意到callChangeHTML绑定到了返回的对象向上，并且能够在HTMLChanger命名空间内被引用。然而，在模块之外，内容就不能被引用了。
<!--more-->
# 揭示模块模式(Revealing Module Pattern) (完美解耦，优良封装)

```
var Exposer = (function() {
  var privateVariable = 10;
  var privateMethod = function() {
    console.log('Inside a private method!');
    privateVariable++;
  }
  var methodToExpose = function() {
    console.log('This is a method I want to expose!');
  }
  var otherMethodIWantToExpose = function() {
    privateMethod();
  }
  return {
      first: methodToExpose,
      second: otherMethodIWantToExpose
  };
})();
Exposer.first();        // Output: This is a method I want to expose!
Exposer.second();       // Output: Inside a private method!
Exposer.methodToExpose; // undefined
```
向外抛出了 first 和second 两个方法 （但是依然不能访问内部实例化的方法，因为他们不在一个同作用域内，且不能应引用私有的方法。且公共的行为不可覆盖重绘）

# 原型设计模式(Prototype Design Pattern)

任何JavaScript开发者都看到了关键字prototype，他们被原型继承所困扰，或者在代码中实现原型。原型设计模式依赖于JavaScript原型继承。原型模型主要被用来在性能密集的条件下创建对象。
通过传递原始对象的克隆(浅克隆)来创建对象。原型模式的一个使用案例是执行一个广泛的数据库操作去创建一个对象，被用作应用的其他部分。如果另一个进程需要使用这个对象，它可以直接去克隆之前的对象而不需要再执行一遍整个数据库操作。
为了克隆一个对象，构造器必须去实例化第一个对象。然后通过使用关键字prototype，变量和方法被绑定到对象的结构中。让我们看一个基本的例子:
```
var init = function mpbUrlInit() {
  var str = this.ourl;
  var parts;
  var i;
  var index;
  var kv;
  index = str.indexOf('?');
  if (index > 0) {
    this.href = str.substring(0, index);
    str = str.substr(index + 1);
    parts = str.split('&');
    for (i = 0; i < parts.length; i++) {
      kv = parts[i].split('=');
      this.params[kv[0]] = kv[1];
    }
  } else {
    this.href = this.ourl;
    this.params = {};
  }
  return this;
};
var MpbUrl = function construct(url) {
  this.ourl = url || window.location.href;
  this.href = ''; // ?前面部分
  this.params = {}; // url参数对象
  return init.call(this);
};
MpbUrl.prototype.get = function getParam(key) {
  return this.params[key];
};

exports = module.exports = MpbUrl;
我是华丽丽的分割线————————————————————————————————————————
var MPURL = new mpUrl()

```
使用的时候只要用构造函数实例化对象就行。可以随意在需要的地方改造get方法。可扩展性极强。也是没目前实用性最强的设计模式。

# 示原型模式(Revealing prototype pattern)

与模块模式相似，原型模式也有揭示的变体。揭示原型模式在它返回一个对象字面量的时候，提空了公有和私有成员的封装。
在我们返回一个对象的时候，我们将会在原型对象前加一个function。通过扩展我们的上一个例子，我们能够选择我们在当前原型中希望暴露的内容，以保持其访问级别。
```
var init = function mpbUrlInit() {
  var str = this.ourl;
  var parts;
  var i;
  var index;
  var kv;
  index = str.indexOf('?');
  if (index > 0) {
    this.href = str.substring(0, index);
    str = str.substr(index + 1);
    parts = str.split('&');
    for (i = 0; i < parts.length; i++) {
      kv = parts[i].split('=');
      this.params[kv[0]] = kv[1];
    }
  } else {
    this.href = this.ourl;
    this.params = {};
  }
  return this;
};
var MpbUrl = function construct(url) {
  this.ourl = url || window.location.href;
  this.href = ''; // ?前面部分
  this.params = {}; // url参数对象
  return init.call(this);
};
MpbUrl.prototype = function () {
	var getParams = function() {
		return this.params[key];
	}
	return {
		getP: getParams
	}
};

exports = module.exports = MpbUrl;
我是华丽丽的分割线————————————————————————————————————————
var MPURL = new mpUrl()
```
留意getParams函数将在返回的对象中被屏蔽，由于在返回的对象作用域之外。因为JavaScript原生就支持对象的继承，因此不需要再重写底层细节了

# 观察者设计模式(现在主流的前端MVVM框架都有用到哟~)(待理解消化)
有很多次当我们的应用的一部分改变的时候，另一部分需要被更新。在AngularJS里面，如果$scope对象更新了，一个事件就能被触发，同时去提醒另一个部分。观察者模式就是对这种情况的合并——如果一个对象被更新，它会对依赖他的对象进行广播，告知它有变化发生了。
还有例如react和vue等MVC框架；当数据模型更新时，视图发生改变。而你只需要考虑的就是数据如何变化，从数据模型中很好的将视图解耦出来。
```
var Subject = function() {
  this.observers = [];
};
Subject.prototype.subscribeObserver = function(observer) {
  this.observers.push(observer);
};
Subject.prototype.unsubscribeObserver = function(observer) {
  var index = this.observers.indexOf(observer);
  if(index > -1) {
    this.observers.splice(index, 1);
  }
};
Subject.prototype.notifyObserver = function(observer) {
  var index = this.observers.indexOf(observer);
  if(index > -1) {
    this.observers[index].notify(index);
  }
};
Subject.prototype.notifyAllObservers = function() {
  for(var i = 0; i < this.observers.length; i++){
    this.observers[i].notify(i);
  }
};
var Observer = function() {
  return {
    notify: function(index) {
      console.log("Observer " + index + " is notified!");
    }
  }
}
var subject = new Subject();
var observer0 = new Observer();
var observer1 = new Observer();
var observer2 = new Observer();
var observer3 = new Observer();
subject.subscribeObserver(observer0);
subject.subscribeObserver(observer1);
subject.subscribeObserver(observer2);
subject.subscribeObserver(observer3);
subject.notifyObserver(observer2); // Observer 2 is notified!
subject.notifyAllObservers();
```

# 单例模式(Singleton)(待理解消化)
单例模式只允许单次实例化，但是同一个对象可以有许多实例。单例模式阻止了客户端创建多个实例，在第一个对象被创建之后，他将会返回它自己的实例。

对于大多数之前还没有使用过单例模式的人来说，找到单例模式的用例是不容易的。一个例子是使用一个办公室的打印机。如果有十个人在办公室里，并且他们都要用一台打印机，十台电脑共享了打印机(实例)。通过共享一个打印机，他们分享了相同的资源。
```
  function create () {
    function print() {
      // underlying printer mechanics
    }
    function turnOn() {
      // warm up
      // check for paper
    }
    return {
      // public + private states and behaviors
      print: print,
      turnOn: turnOn
    };
  }
  return {
    getInstance: function() {
      if(!printerInstance) {
        printerInstance = create();
      }
      return printerInstance;
    }
  };
  function Singleton () {
    if(!printerInstance) {
      printerInstance = intialize();
    }
  };
})();
```
create方法是私有的，因为我们不想让客户端去访问他，然而，getInstance方法是公开的。每一个办公室的员工都能通过与getInstance方法进行交互从而生成一个打印机的实例。向下面这样:
```
var officePrinter = printer.getInstance();
```