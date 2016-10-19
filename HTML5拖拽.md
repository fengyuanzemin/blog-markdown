---
title: HTML5拖拽
date: 2016.10.18
tag: HTML5
---

# 事件
图片的拖拽，首先有几个`event`要普及下

* dragstart
	* 当一个元素开始被拖拽的时候触发。
* dragenter
	* 当拖拽中的鼠标第一次进入一个元素的时候触发。
* dragover
	* 当拖拽中的鼠标移动经过一个元素的时候触发。
* dragleave
	* 当拖拽中的鼠标离开元素时触发。
* drag
	* 这个事件在拖拽源触发。即在拖拽操作中触发dragstart事件的元素。
* drop
	* 这个事件在拖拽操作结束释放时于释放元素上触发。
* dragend
	* 拖拽源在拖拽操作结束将得到dragend事件对象，不管操作成功与否。

# div拖拽

div拖拽是把div从一个位置拖到另一个位置

**draggable**属性:
标签元素要设置这个属性，`draggable="true"`，不然不会有拖拽的效果

```html	
<div class="drop-zone"><p>把div放到这里谢谢</p></div>
<div class="drag-item" draggable="true"></div>
```

```css
.drop-zone {
	width: 100%;
	height: 300px;
	position: relative;
	border: 1px solid #333333;
	border-radius: 10px;
}
.drop-zone p {
	font-size: 16px;
	position: absolute;
	top: 50%;
	left: 50%;
	margin-left: -72px;
	margin-top: -8px;
}
.drag-item {
	width: 100%;
	height: 100px;
	background-color: #333333;
}
```

```js
var dropZone = document.querySelector('.drop-zone');
var dragItem = document.querySelector('.drag-item')
dragItem.addEventListener('dragstart', function(e) {
	e.dataTransfer.setData('Text', e.target.className);
});
dropZone.addEventListener('dragover', function(e) {
	e.preventDefault();
});
dropZone.addEventListener('drop', function(e) {
	e.preventDefault();
	var data = e.dataTransfer.getData('Text');
	e.target.appendChild(document.querySelector('.' + data))
});
```

# 文件拖拽

从操作系统拖到浏览器内

这里需要dragover、drop两个事件



```html
<!--首先得有一块区域放置文件-->
<div class="drop-zone">把文件放到这里谢谢</div>
<ul class="file-list"></ul>
```

```css
/* 有个高度适合拖放 */
.drop-zone {
	width: 100%;
	height: 300px;
	position: relative;
	border: 1px solid #333333;
	border-radius: 10px;
}
.drop-zone p {
	font-size: 16px;
	position: absolute;
	top: 50%;
	left: 50%;
	margin-left: -72px;
	margin-top: -8px;
}
```

```js
var dropZone = document.querySelector('.drop-zone');
// 拖到区域时触发
dropZone.addEventListener('dragover', function(e) {
	// 阻止默认事件，不然浏览器会跳转到本地目录
	e.preventDefault();
	e.dataTransfer.dropEffect = 'copy';
});
// 释放鼠标时触发
dropZone.addEventListener('drop', function(e) {
	e.preventDefault();
	var file = e.dataTransfer.files;
	var output = [];
	for(var i =0, f; f = file[i]; i += 1){
		output.push('<li><strong>', f.name, '</strong></li>');
	}
	document.querySelector('.file-list').innerHTML = output.join('');
});
```

# 图片拖拽

图片拖拽的原理和文件拖拽一样，只不过是drop事件触发后，需要把图片的url赋值进去，这里我们需要FileReader对象，同时也有两种方法

1. new一个image，然后给它的src赋值
2. 设置div的background-image


```html
<div class="drop-zone">
	<p>把图片放到这里谢谢</p>
	<div class="img-zone"></div>
</div>
```
```css
.drop-zone {
	width: 100%;
	height: 300px;
	position: relative;
	border: 1px solid #333333;
	border-radius: 10px;
}

.drop-zone p {
	font-size: 16px;
	position: absolute;
	top: 50%;
	left: 50%;
	margin-left: -72px;
	margin-top: -8px;
}

.img-zone img {
	width: 100px;
	height: 100px;
}
```

```js
var dropZone = document.querySelector('.drop-zone');
// 拖到区域时触发
dropZone.addEventListener('dragover', function(e) {
	// 阻止事件冒泡和默认事件，不然浏览器会跳转到本地目录
	e.preventDefault();
	e.dataTransfer.dropEffect = 'copy';
});
// 释放鼠标时触发
dropZone.addEventListener('drop', function(e) {
	e.preventDefault();
	var files = e.dataTransfer.files;
	var reader;
	for (var i = 0, f; f = files[i]; i += 1) {
		reader = new FileReader();
		reader.readAsDataURL(f);
		reader.onload = function(e) {
			var img = new Image();
			img.src = e.target.result;
			document.querySelector('.img-zone').appendChild(img);
		}
	}
});
```

# 相关链接

[https://developer.mozilla.org/zh-CN/docs/DragDrop/Drag_and_Drop](https://developer.mozilla.org/zh-CN/docs/DragDrop/Drag_and_Drop)

[HTML5上传图片文件（含拖拽、预览、上传、美化）](http://laker.me/blog/2016/03/04/16_0406_html5_upload_img/)

[HTML5 drag & drop 拖拽与拖放简介](http://www.zhangxinxu.com/wordpress/2011/02/html5-drag-drop-%E6%8B%96%E6%8B%BD%E4%B8%8E%E6%8B%96%E6%94%BE%E7%AE%80%E4%BB%8B/)