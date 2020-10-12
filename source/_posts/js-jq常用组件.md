---
title: js常用组件
date: 2016-9-27 23:04:34
tags:
  - js
categories: 学习
---

# 关于url获取的组件
```
let init = function mpbUrlInit() {
	let str = this.ourl;
	let parts;
	let i;
	let index;
	let kv;
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
}
let MpbUrl = function construct(url) {
  this.ourl = url || window.location.href;
  this.href = ''; // ?前面部分
  this.params = {}; // url参数对象
  return init.call(this);
};
MpbUrl.prototype.get = function getParam(key) { //获取url之后指定的参数对象
  return this.params[key];
};

exports = module.exports = MpbUrl;
```
```
GetQueryString (name) { // 正则方法
  var reg = new RegExp("(^|&)"+ name +"=([^&]*)(&|$)");
  var r = window.location.search.substr(1).match(reg);
  if(r!=null)return  unescape(r[2]); return null;
},
```
<!--more-->
# 表单回填组件（基于jq懒癌犯了本来想写个原生的=-= 啦啦啦~）
```
var $ = require('jquery');
const myData = {
	"name": "夜未央",
	"job": [0, 1, 3],
	"age": 23,
	"state": 4,
	"sex": 1,
	"logo": "1.jpg"
}
let reWriteForm = function reWriteForm($form, data, special) {
	$.each(data, function(k,v) {
		var node = $form.find('[name=' + k + ']');
		if (special && special[k]) {
	        if (typeof special[k] === 'function') {
	          	special[k].call(node, d);
	        } else {
	          	special[k].val(d, node);
	        }
	    } else {
	    	if (node.length > 1) {
		        if ($(node[0]).attr('type') === 'checkbox' || $(node[0]).attr('type') === 'CHECKBOX') {
		          	if ($.isArray(data[k])) {
		            	$.each(data[k], function (j, _n) {
		              		node.filter('[value=' + _n + ']').attr('checked', 'checked');
		            	});
		          	}
		        }
		        if ($(node[0]).attr('type') === 'radio' || $(node[0]).attr('type') === 'RADIO') {
		          	node.filter('[value=' + data[k] + ']').attr('checked', 'checked');
		        }
		    } else if (node.length === 1) {
		        if (node[0].nodeName === 'input' || node[0].nodeName === 'INPUT') {
		          	if (node.attr('type') === 'email' || node.attr('type') === 'text') {
		            	node.val(data[k]);
		        	}
			        if (node.attr('type') === 'checkbox' || node.attr('type') === 'CHECKBOX') {
			            node.attr('checked', 'checked');
			        }
			        if (node.attr('type') === 'radio' || node.attr('type') === 'RADIO') {
			            node.attr('checked', 'checked');
			        }
		        }
		        if (node[0].nodeName === 'select' || node[0].nodeName === 'SELECT') {
		          	node.val(data[k]);
		        }
		        if (node[0].nodeName === 'img' || node[0].nodeName === 'IMG') {
		          	node.attr('src', data[k]);
		        }
		        if (node[0].nodeName === 'textarea' || node[0].nodeName === 'TEXTAREA') {
		          	node.val(k);
		        }
	      	}
	    }
	})
}
let unique = function unique(arr) {
  var i;
  var j;
  var repeat;
  var res = [arr[0]];
  for (i = 1; i < arr.length; i++) {
    repeat = false;
    for (j = 0; j < res.length; j++) {
      if (arr[i] === res[j]) {
        repeat = true;
        break;
      }
    }
    if (!repeat) {
      res.push(arr[i]);
    }
  }
  return res;
};
let submitParam = function submitParam($form) {
  var inputs = $form.find('input');
  var selects = $form.find('select');
  var textarea = $form.find('textarea');
  var nodes = [];
  inputs.each(function (index, value) {
    if ($(value).attr('name')) {
      nodes.push($(value).attr('name'));
    }
  });
  selects.each(function (index, value) {
    if ($(value).attr('name')) {
      nodes.push($(value).attr('name'));
    }
  });
  textarea.each(function (index, value) {
    if ($(value).attr('name')) {
      nodes.push($(value).attr('name'));
    }
  });
  nodes = unique(nodes);
  return nodes;
};
let getParam = function getParam($form) {
  var param = {};
  var nodes = submitParam($form);
  nodes.forEach(function (n) {
    var node = $form.find('[name=' + n + ']');
    if (node.length > 1) {
      if ($(node[0]).attr('type') === 'checkbox' || $(node[0]).attr('type') === 'CHECKBOX') {
        param[n] = [];
        node.each(function () {
          if (this.checked) {
            param[n].push($(this).val());
          }
        });
      }
      if ($(node[0]).attr('type') === 'radio' || $(node[0]).attr('type') === 'RADIO') {
        node.each(function () {
          if (this.checked) {
            param[n] = $(this).val();
          }
        });
      }
    } else if (node.length === 1) {
      if (node[0].nodeName === 'input' || node[0].nodeName === 'INPUT') {
        param[n] = node.val();
      }
      if (node[0].nodeName === 'select' || node[0].nodeName === 'SELECT') {
        param[n] = node.val();
      }
      if (node[0].nodeName === 'textarea' || node[0].nodeName === 'TEXTAREA') {
        param[n] = node.val();
      }
    }
  });
  console.log(param);
  return param;
};
export {myData, reWriteForm, getParam}

```
接受3个参数$form 之后会在该dom对象下寻找 各种input select 和textrea 第二个参数 回填的对象 第三个参数 一些特殊的处理

# 轮播组件 
```
var $ = require('jquery');

var slide = function(form){
  this.$element = form; // 当前节点
  this.index = 1; // 初始化当前图片显示
  this.length = this.$element.children().length - 1;
  this.dothtml = []; // 点生成
}
var hideImg = function(item) {
  this.$element.find('[index-data]').css('opacity', '0').removeClass('on');
  this.$element.find('[index-data=' + item + ']').css('opacity', '1').addClass('on');
}
slide.prototype.render = function() {
  for(let a = 1; a <= this.length; a++) {
    this.dothtml.push('<a href="" class="dot-item" data-index=' + a + '></a>')
  }
  this.$element.find('.slide-dot').css('margin-left', -(12*this.length + 6) + 'px').html(this.dothtml);
  return this;
};
slide.prototype.init = function() {
  var that = this;
  this.$element.on('mouseover', '.dot-item', function() {
    $(this).addClass('on');
    $(this).siblings().removeClass('on');
    that.index = $(this).attr('data-index');
    hideImg.call(that, that.index);
  })
  that.$element.find('[data-index=1]').trigger('mouseover');
  return this;
}
slide.prototype.startSlide = function() {
  var slidego = function(){
    setTimeout(function(){
      this.index = this.index > this.length ? 1 : this.index;
      this.$element.find('[data-index=' + this.index + ']').trigger('mouseover');
      this.index ++;
      slidego();
    }.bind(this), 3000)
  }.bind(this);
  slidego();
}
export { slide }
```

```
<div class="plate-midle">
  <a href="" index-data='1'><img src="./images/banner/1.png" alt=""></a>
  <a href="" index-data='2'><img src="./images/banner/2.png" alt=""></a>
  <a href="" index-data='3'><img src="./images/banner/3.png" alt=""></a>
  <a href="" index-data='4'><img src="./images/banner/4.png" alt=""></a>
  <a href="" index-data='5'><img src="./images/banner/5.png" alt=""></a>
  <div class="slide-dot clearfix"></div>
</div>
html 结构 利用css3 的transition 实现图片的过渡
```
样式表
``` 
.plate-midle{
  margin-top: 10px;
  width: 680px;
  height: 360px;
  float: left;
  position: relative;
  box-sizing: border-box;
  a{
    height: 680px;
    width: 360px;
    transition: all 1s;
    &.on{
      z-index: 2;
    }
  }
  img{
    position: absolute;
    top: 0;
    width: 680px;
    height: 360px;
    display: block;
  }
  .slide-dot{
    background-color: rgba(170, 170, 170, 0.5);
    padding: 7px 10px;
    position: absolute;
    bottom: 30px;
    left: 50%;
    border-radius: 15px;
    a.dot-item{
      width: 16px;
      height: 16px;
      float: left;
      border-radius: 50%;
      background-color: white;
      transition: all 1s;
      & + .dot-item{
        margin-left: 8px;
      }
      &.on{
        background-color: #7373f7;
      }
    }
  }
}
```

# 电商平台 图片预览
```
var $ = require('jquery'); // 美猴王 图片预览组件

var preview = function(img1, img2, img2_img, wrap, mousebg){
  this.img1 = img1;
  this.img2 = img2;
  this.img2_img = img2_img;
  this.wrap = wrap;
  this.mousebg = mousebg;
}

preview.prototype.init = function() {
  this.img1.on('mousemove', function(event){
    var mouseX = event.offsetX; // 获取相对位置
    var mouseY = event.offsetY; // 获取相对位置
    if(mouseX < this.mousebg[0].offsetWidth / 2){
            mouseX = this.mousebg[0].offsetWidth / 2;  
        }  
        if(mouseX > this.img1[0].offsetWidth - this.mousebg[0].offsetWidth / 2){  
            mouseX = this.img1[0].offsetWidth - this.mousebg[0].offsetWidth / 2;  
        }  
        if(mouseY < this.mousebg[0].offsetHeight / 2){  
            mouseY = this.mousebg[0].offsetHeight / 2;  
        }  
        if(mouseY > this.img1[0].offsetHeight - this.mousebg[0].offsetHeight / 2){  
            mouseY = this.img1[0].offsetHeight - this.mousebg[0].offsetHeight / 2;  
        }
        this.img2_img.css('left', -2 * mouseX + 190 + 'px');  
        this.img2_img.css('top', -2 * mouseY + 190 + 'px');
    this.mousebg.css('left', mouseX - this.mousebg[0].offsetWidth / 2 + 'px'); 
    this.mousebg.css('top', mouseY - this.mousebg[0].offsetHeight / 2 + 'px');
  }.bind(this))
}

export { preview }
```

```
<div class="img-group">
  <div class="min-pic">
    <div class="hover-bg"></div>
    <div class="mouse-bord"></div>
    <img src="" alt="">
  </div>
  <div class="goods-preview">
    <img src="" alt="">
  </div>
</div>
```

```
.min-pic{
  position: relative;
  width: 380px;
  height: 380px;
  .hover-bg{
    width: 190px;  
      height: 190px;
      position: absolute;
      display: none;
      background-color: rgba(0 , 0, 0,.5);
  }
  &:hover{
    .hover-bg{
      display: block;
    }
    & + .goods-preview{
      display: block;
    }
  }
  .mouse-bord{
    width: 380px;
    height: 380px;
    position: absolute;
    cursor: pointer;
    background-color: transparent;
  }
}
.goods-preview{
      position: absolute;
      top: 0;
      width: 380px;
      height: 380px;
      left: 390px;
      overflow: hidden;
      display: none;
      img{
        left: 0;
        top: 0;
        position: absolute;
        width: 760px;
        height: 760px;
      }
    }
```

# 数字转换成金额
Number.prototype.formatMoney = function (places, symbol, thousand, decimal) {
    places = !isNaN(places = Math.abs(places)) ? places : 2;
    symbol = symbol !== undefined ? symbol : "$";
    thousand = thousand || ",";
    decimal = decimal || ".";
    var number = this,
        negative = number < 0 ? "-" : "",
        i = parseInt(number = Math.abs(+number || 0).toFixed(places), 10) + "",
        j = (j = i.length) > 3 ? j % 3 : 0;
    return symbol + negative + (j ? i.substr(0, j) + thousand : "") + i.substr(j).replace(/(\d{3})(?=\d)/g, "$1" + thousand) + (places ? decimal + Math.abs(number - i).toFixed(places).slice(2) : "");
};