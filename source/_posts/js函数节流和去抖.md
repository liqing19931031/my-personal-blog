---
title: js函数节流和去抖
date: 2016-12-21 13:39:21
tags:
  - js
categories: 学习
---
# 函数去抖(debounce)，应用场景（用户的信息提交等，防止短时间内的多次请求）
```
let debounced = (func, wait, immediate) => { //防抖函数，等待时间，是否立即执行一次
	var timeout, args, context, timestamp, result;
  	var later = function() {
    	var last = _.now() - timestamp;
	    if (last < wait && last >= 0) {
	      timeout = setTimeout(later, wait - last);
	    } else {
	      timeout = null;
	      if (!immediate) {
	        result = func.apply(context, args);
	        if (!timeout) context = args = null;
	      }
	    }
  	};
  	return function() {
    	context = this;
    	args = arguments;
    	timestamp = _.now();
    	var callNow = immediate && !timeout;
    	if (!timeout) timeout = setTimeout(later, wait);
    	if (callNow) {
      		result = func.apply(context, args);
      		context = args = null;
    	}
    return result;
    };
}
```

# 函数节流(throttle)，应用场景（滚动触发事件，搜索等。）
原理：某些代码不可以在没有间断的情况连续重复执行。第一次调用函数，创建一个定时器，在指定的时间间隔之后运行代码。当第二次调用该函数时，它会清除前一次的定时器并设置另一个。如果前一个定时器已经执行过了，这个操作就没有任何意义。然而，如果前一个定时器尚未执行，其实就是将其替换为一个新的定时器。目的是只有在执行函数的请求停止了一段时间之后才执行。
```
function queryData(text){
    console.log("搜索：" + text); // 可能你要进行的异步请求
}
var input = document.getElementById("search");
  input.addEventListener("keyup", function(event){
      throttle(queryData, null, 500, this.value,1000);
      // throttle(queryData, null, 500, this.value);
      // queryData(this.value);
});
  
function throttle(fn,context,delay,text,mustApplyTime){
  clearTimeout(fn.timer);
  fn._cur=Date.now();  //记录当前时间
  if(!fn._start){  
    fn._start=fn._cur;
  }   //若该函数是第一次调用，则直接设置_start,即开始时间，为_cur，即此刻的时间
  if(fn._cur-fn._start>mustApplyTime){ //当前时间与上一次函数被执行的时间作差，与mustApplyTime比较，若大于，则必须执行一次函数，若小于，则重新设置计时器
    fn.call(context,text);
    fn._start=fn._cur;
  }else{
    fn.timer=setTimeout(function(){
      fn.call(context,text);
    },deley);
  }
}
```
option有两个选项，throttle第一次调用时默认会立刻执行一次function，如果传入{leading: false}，则第一次调用时不执行function。{trailing: false}参数则表示禁止最后那一次延迟的调用。

