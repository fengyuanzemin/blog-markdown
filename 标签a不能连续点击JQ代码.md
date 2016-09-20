---
title: 按钮不能重复点击
date: 2015-10-01 23:52:01
tags: [JavaScript, Event Listener]
---

# 前言

今天要实现一个功能，你们知道的，有时候一个链接或者按钮 ，当点击一次无反应或者反应没那么快的时候，用户就有可能多次点击，这样浏览器就识别出点击多次，重复触发事件。

所以，要用JS来防止重复点击。


<!--more-->

# 原理

点击的时候，判断有没有某个class，如果有就不能点击。没有就可以点击。





# 实现
```js
// js
var btn = document.querySelector('.next');
btn.addEventListener('click', function(){
	if (btn.classList.contain('disabled')) {
		return;
	} else {
		btn.classList.add('disabled');
	}
	// if success
	btn.classList.remove('disabled');
});

// jQuery
var btn = $('.next');
btn.on("click", clickEvent);
function clickEvent() {
	if (btn.hasClass('disabled')){
		return;
	}else {
		btn.addClass('disabled');
	}
	// if success
	btn.removeClass('disabled');
}
```

