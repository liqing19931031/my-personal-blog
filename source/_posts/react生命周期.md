---
title: react生命周期
date: 2017-04-13 15:12:52
tags:
  - react.js
categories: 学习
---
# 用法
```
class Index extends React.Component{
	constructor(props) {
		super(props);
		this.state = {date: new Date()};
		this.user = 'psgn'
	}
```
两种写法 一种是React.createClass() 创造一个类，继承了react的生命周期，第二种利用es6的class 类创建 并继承React.compontent的所有生命周期。constructor是构造器，在实例化对象时调用，super调用了父类的constructor创造了父类的实例对象this，然后用子类的构造函数进行修改。这和es5的原型继承是不同的，原型继承是先创造一个实例化对象this，然后再继承父级的原型方法。了解了这些之后我们在看组件的时候就清楚很多。
有三点值得注意：
	1、定义类名字的首字母必须大写 
	2、因为class变成了关键字，类选择器需要用className来代替。 
	3、类和模块内部默认使用严格模式，所以不需要用use strict指定运行模式。
# 生命周期
先上图
<img src="https://raw.githubusercontent.com/bailicangdu/react-pxq/master/src/images/react-lifecycle.png" alt="">
## 详细生命周期
### getDefaultProps()

设置默认的props，也可以用dufaultProps设置组件的默认属性。
### getInitialState()

在使用es6的class语法时是没有这个钩子函数的，可以直接在constructor中定义this.state。此时可以访问this.props。
### componentWillMount()

组件初始化时只调用，以后组件更新不调用，整个生命周期只调用一次，此时可以修改state。
### render()

react最重要的步骤，创建虚拟dom，进行diff算法，更新dom树都在此进行。此时就不能更改state了。
### componentDidMount()

组件渲染之后调用，可以通过this.getDOMNode()获取和操作dom节点，只调用一次。
在更新时也会触发5个钩子函数：

### componentWillReceivePorps(nextProps)

组件初始化时不调用，组件接受新的props时调用。
### shouldComponentUpdate(nextProps, nextState)

react性能优化非常重要的一环。组件接受新的state或者props时调用，我们可以设置在此对比前后两个props和state是否相同，如果相同则返回false阻止更新，因为相同的属性状态一定会生成相同的dom树，这样就不需要创造新的dom树和旧的dom树进行diff算法对比，节省大量性能，尤其是在dom结构复杂的时候。不过调用this.forceUpdate会跳过此步骤。
### componentWillUpdata(nextProps, nextState)

组件初始化时不调用，只有在组件将要更新时才调用，此时可以修改state
### render()

虚拟dom的渲染，也可以说是组件的主体和模块构建部分（虚拟dom，之间的差异用的是diff运算检测虚拟dom之间的差异）
### componentDidUpdate()

组件初始化时不调用，组件更新完成后调用，此时可以获取dom节点。
还有一个卸载钩子函数

### componentWillUnmount()

组件将要卸载时调用，一些事件监听和定时器需要在此时清除。
以上可以看出来react总共有10个周期函数（render重复一次），这个10个函数可以满足我们所有对组件操作的需求，利用的好可以提高开发效率和组件性能。
