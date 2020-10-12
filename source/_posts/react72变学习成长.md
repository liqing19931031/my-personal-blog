---
title: react72变学习成长
date: 2017-07-12 10:24:06
tags:
  - react.js
categories: 学习
---

# JSX
JSX 的基本语法规则：遇到 HTML 标签（以 < 开头），就用 HTML 规则解析；遇到代码块（以 { 开头），就用 JavaScript 规则解析。

# setState
因为react的setState 只能修改第一及的变量不能修改某一个属性所以在修改其中一个属性的时候需要将单独属性和整体对象合并以达到覆盖的目的。
state 设置
```
this.state = {
    slide:{
		height: '11.2rem',
		slideData: []
	}
}
```
state 修改
```
let newValue = {...this.state.slide, slideData: data.result} // 利用es6的展开运算合并对象
this.setState({'slide': newValue})
```

# 事件的添加与参数设置

```
{this.props.data.slideData.map((item, i) => ( // 胖箭头写法
<div className='slide-one' key={i} onTouchMove={_this.changeMove.bind(_this)} onTouchStart={_this.changeStart.bind(_this)}>
  <a>
    <img src={`http://127.0.92.29:3000/images/${item.pic}`} />
  </a>
</div>
))}
```

# 定时事件添加

由于react的 生命周期问题，当state改变的时候render函数会被触发三次，所以定时操作只能放在只在组件实例化调用一次的方法中调用，也就是
<strong>componentDidMount</strong>，并且在<strong>componentWillUnmount</strong>对定时器进行清除
demo
```
var _this = this // 由于这里我要在自身的基础上增加 本来定时器写在render函数里面 发现调用次数越来越多后发现问题所在
    this.timer = setInterval(function(){
      _this.setState({
        'slideSecond': 0.3,
        'groupTop': _this.state.groupTop + 2
      })
    }, 3000)
```
```
this.timer && clearInterval(this.timer)
```

# react slide 移动端组件
```
import React from 'react'; // TODO: 根据swiper插件的思路自己写的移动端轮播组件 待优化

class Slide extends React.Component{
  constructor (props) {
    super(props);
    this.state = {
      groupLeft: -document.documentElement.getBoundingClientRect().width, // 轮播组左偏移距离
      startX: 0, // 触摸开始时的位置
      endX: 0,
      index: 1,
      allWidth: document.documentElement.getBoundingClientRect().width,
      lastTouchmove: 0, // touchMove 过程中的位置（用于计算偏移量）
      slideSecond: 0.3 // 轮播过渡动画时间
    }
  }
  componentWillMount() {
  }

  componentWillReceiveProps(nextProps) { //图片数组做二次处理
    nextProps.data.slideData.unshift(nextProps.data.slideData[nextProps.data.slideData.length - 1]);
    nextProps.data.slideData.push(nextProps.data.slideData[1]);
  }

  handleStart (event) { // touch 事件开始
    this.setState({'slideSecond': 0}) // 去除过渡动画
    this.setState({'startX': event.touches[0].pageX}) // 设置 触摸事件的起始位置
  }

  handleMove (event) { // tocuh Move 触发
    var pianyi = this.state.lastTouchmove === 0 ? 0 : event.touches[0].pageX - this.state.lastTouchmove // 计算偏移量
    this.setState({'groupLeft': pianyi + this.state.groupLeft}) // 宽度等于 偏移量加上当前的left值
    this.setState({'lastTouchmove': event.touches[0].pageX}) // touch Move上一次触发的位置用于计算偏移量
    this.setState({'endX': event.touches[0].pageX}) // touch Move 当前触发的距离用于计算偏移量
  }

  handleEnd (event) { // touch时间结束
    this.setState({'slideSecond': 0.3})
    this.state.lastTouchmove !== 0 && this.endSlide()
    this.setState({'lastTouchmove': 0})
  }

  endSlide () {
    if (Math.abs(this.state.startX - this.state.endX) > (this.state.allWidth / 3)){ // 拖动距离大于整体宽度三分之一时 执行滑动动作
      let number = this.state.startX > this.state.endX ? parseInt(this.state.groupLeft / this.state.allWidth) - 1 : parseInt(this.state.groupLeft / this.state.allWidth)
      this.setState({'groupLeft': number * this.state.allWidth})
      if (number === 0 || number === -(this.props.data.slideData.length - 1)) {
        setTimeout(() => {
          this.setState({'slideSecond': 0})
          if (number === 0) {
            this.setState({'groupLeft': -(this.props.data.slideData.length - 2) * this.state.allWidth})
          } else {
            this.setState({'groupLeft': -this.state.allWidth})
          }
        }, 300)
      }
      this.setDot(number)
      // this.setState({'index': number === this.props.data.slideData.length - 2 ? 1 : number})
    } else { // 拖动距离小于三分之一时 回到当前位置
      let number = this.state.startX > this.state.endX ? parseInt(this.state.groupLeft / this.state.allWidth) : parseInt(this.state.groupLeft / this.state.allWidth) - 1
      this.setState({'groupLeft': number * this.state.allWidth})
      this.setDot(number)
    }
  }

  setDot (num) { // 圆点设置方法
    if (num === 0 || num === -(this.props.data.slideData.length - 1)) {
      this.setState({'index': num === 0 ? this.props.data.slideData.length - 2 : 1})
    } else {
      this.setState({'index': -num})
    }
  }

  render() {
    var _this = this
    return (
      <div className='slide-group' style={{height: this.props.data.height}}>
        <div className='slide-box' style={{
          width: this.props.data.slideData.length * 25 + 'rem',
          height: this.props.data.height,
          transition: `all ease ${this.state.slideSecond}s`,
        left: this.state.groupLeft + 'px'}}>
          {this.props.data.slideData.map((item, i) => ( // 胖箭头写法
            <div className='slide-one' key={i}
              onTouchMove={_this.handleMove.bind(_this)}
              onTouchStart={_this.handleStart.bind(_this)}
              onTouchEnd={_this.handleEnd.bind(_this)}>
              <a>
                <img src={`http://127.0.92.29:3000/images/${item.pic}`} />
              </a>
            </div>
          ))}
        </div>
        <div className='slide-dots'>
          {this.props.data.slideData.map((item, i) => {
            if (i >= 1 && i < _this.props.data.slideData.length - 1) {
              return <div className={`slide-dot ${i === this.state.index && 'dot-active'}`} data-index={i} key={i}></div>
            }
          })}
        </div>
      </div>
    )
  }
}

export default Slide;

```

# 关于react-redux

actions 

# react 报错 （伊丽莎白 这辈子都不会忘记）

静态目录报错 X-Content-Type-Options: nosniff 
可能是由于引入的问题

