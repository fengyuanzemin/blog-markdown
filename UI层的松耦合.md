---
title: 读书笔记：《编写可维护的JavaScript》--UI层的松耦合
date: 2015-10-12 18:46:23
tags: 读书笔记
---

# UI层的松耦合 

* HTML用来定义页面的数据和语义
* CSS用来给页面添加样式，创建视觉特征
* JavaScript用来给页面添加行为，使其更具交互性

UI关系是这样的：
HTML在底层，CSS和JavaScript在高层

|Javascript|
|:---:|
|CSS|
|HTML|

当然也可以是这样的

<table>
	<tr>
		<td>Javascript</td>
			<td>CSS</td>
	</tr>
	<tr style="text-align: center;">
		<td colSpan="2">HTML</td>
	</tr>
</table>

## 松耦合

如果两个组件耦合太紧，当修改一个组件的逻辑时，那么另外一个组件的逻辑也要修改。

> 比如，假设有个名为error的CSS类名，它是贯穿整个站点的，它被嵌入到HTML之中。如果有一天你觉得error的取名并不合适，想将它改为warning，你不仅需要修改CSS还要修改用到这个className的HTML.HTML和CSS紧耦合在一起。这只是一个简单的例子。想象一下，如果一个系统包含上百个组件，那简直就是一场噩梦。

> 还有一点要注意：在一起工作的组件无法达到“无耦合”。在所有系统中，组件之间总要共享一些信息来完成各自的工作。

## 将Javascript从CSS中抽离
> 在IE8和更早版本的浏览器中有一个特性让人爱少恨多，即CSS表达式（CSS expression）。CSS表达式允许你将Javascript直接插入到CSS中，这样可以在CSS代码中直接执行运算或其他操作。比如，下面这段代码设置了元素宽度以匹配浏览器宽度。

```css
/* 不好的写法 */
.box {
	width: expression(document.body.offsetWidth + "px");
}
```

> CSS表达式包裹在一个特殊的expression()函数中，可以给它传入任意Javascript代码。浏览器会以高频率重复计算CSS表达式，这严重影响了性能。
> 
> 除了性能问题以外，在CSS中嵌入Javascript代码对于代码维护来说亦是一场噩梦。

样式出了问题，却在样式表里找不到答案。这就算代码维护的噩梦。


## 将CSS从Javascript中抽离

> 有时候，保持CSS和Javascript之间的清晰的分离是很有挑战的。

```js
//不好的写法
element.style.color = "red";
element.style.left = "10px";
element.style.top = "100px";
```
或者这样，用cssText属性赋值整个CSS字符串。

```js
//不好的写法
element.style.cssText = "color: red; left: 10px; top: 100px; visibility: hidden;"
```
将CSS从Javascript中抽离意味着所有的样式信息都应当保持在CSS中。当需要通过Javascript来修改元素样式的时候，最佳方法是操作CSS的className。

```css
.reveal {
	color: red;
	left: 10px;
	top: 100px;
	visibility: visible;
}
```

然后，在Javascript中像这样将样式添加至元素

```js
// 好的写法 - 原生方法
element.className += "reveal";

// 好的写法 - HTML5
element.classList.add("reveal");

// 好的写法 - JQuery
$(element).addClass("reveal");
```
## 将Javascript从HTML中抽离
```html
<!--不好的写法-->
<button onclick="doSomething()" id="action-btn">Click Me</button>
```
> 首先，当按钮上发生点击事件时，doSomething()函数必须存在。如果不存在，就会报错。

报错这种事情，我们当然不希望发生。

> 第二，如果你修改了doSomething()的函数名。

这时就需要把所有用到这个函数的地方给改名字。需要同时修改HTML和Javascript的代码。

所以我们只需要添加给元素添加一个事件处理程序

```js
function doSomething() {
	//TODO
}

var btn = document.getElementById("action-btn");
addListener(btn, "click", doSomething);
```
或者

```js
// JQuery
$("#action-btn").on("click", doSomething);
```
> 最好将所有的Javascript代码都放入外置文件中，以确保在HTML代码中不会有内联的Javascript代码。这样做的原因是出于紧急调试的考虑。当Javascript报错，你的下意识的行为应当是去Javascript文件中查找原因。如果在HTML中包含Javascript代码，则会阻断你的工作流。



## 将HTML从Javascript中抽离
在Javascript中使用HTML的情形往往是给innerHTML属性赋值时，比如：

```js
var div = document.getElementById("my-div");
div.innerHTML = "<h3>Error</h3><p>Invalid e-mail address</p>"
```

> 将HTML嵌入在Javascript代码中是非常不好的时间。原因如下：
> 
> 1. 正如上文提到的，它增加了跟踪文本和结构性问题的复杂度。一旦Javascript做了除简单DOM操作之外的事情，追踪bug就变得很有问题了。
> 
> 2. 通常你下意识地的想到：大部分标签都放置于一个目录下的模板文件里，谁又会去Javascript中改HTML标签呢？
> 
> 3. 相比于修改Javascript代码，修改标签通常不会引发太多错误。

下面本书介绍了三种低耦合的方法：
### 方法1：从服务器加载
将模板放置于远程服务器，使用XMLHttpRequest对象来获取外部标签。

```js
function loadDialog(name, oncomplete) {
	var xhr = new XMLHttpRequest();
	xhr.open("get", "/js/dialog" + name, true);
	
	xhr.onreadystatechange = function() {
		if(xhr.readyState == 4 && xhr.status == 200) {
			var div = document.getElementById("dlg-hoider");
			oncomplete();
		} else {
			// 处理错误
		}
	}
	
	xhr.send(null);
}
```

或者

```js
function loadDialog(name, oncomplete) {
	$("#dlg-holder").load("/js/dialog/" + name, oncomplete);
}
```


### 方法2：简单客户端模板
>客户端模板是一些带“插槽”的标签片段，这些“插槽”会被Javascript程序替换为数据以保证模板的完整使用。比如：一段用来添加数据项的模板看起来就像这样。

```html
<li><a href="%s">%s</a></li>
```

>这段模板中包含%s占位符，这个位置的文本会被程序替换掉（这个格式和C语言中的sprintf()一模一样）。

```js
function sprintf(text) {
	var i = 1,args = arguments;
	return text.replace(/%s/g, function() {
		return (i < args.length) ? args[i++] : "";
	});
}

// 用法
var result = sprintf(templateText, "/item/4", "Fourth item");
```
#### 第一种方法
> 第一种方法是在HTML注释中包含模板文本。注释是和元素及文本一样的DOM节点，因此可以通过Javascript将其提取出来。

```html
<ul id="mylist"><!--<li id="item%s"><a href="%s">%s</a></li>-->
	<li><a href="/item/1">First item</a></li>
	<li><a href="/item/2">Second item</a></li>
	<li><a href="/item/3">Third item</a></li>
</ul>
```

> 下面这段Javascript代码则将模板文本从注释中提取出来

```js
var mylist = document.getElementById("mylist"),
	templateText = mylist.firstChild.nodeValue;
```

> 一旦提取出模板文本，紧接着需要将它格式化，并插入DOM。

```js
function addItem(url, text) {
	var mylist = document.getElementById("mylist");
		templateText = mylist.firstChild.nodeValue;
		result = sprintf(template, url, text);
	div.innerHTML = result;
	mylist.insertAdjacentHTML("beforeend", result);
}

// 用法
addItem("/item/4", "Fourth item");
```
#### 第二种方法
> 使用一个带有自定义type属性的`<script>`元素。浏览器会默认将`<script>`元素中的内容识别为Javascript代码，但你可以通过给type赋值为浏览器不识别的类型，来告诉浏览器这不是一个Javascript脚本。

```js
<script type="text/x-my-template" id="list-item">
	<li><a href="%s">%s</a></li>
</script>
```

> 可以通过`<script>`标签的text属性来提取模板文本。

```js
var script = document.getElementById("list-item"),
	templateText = script.text;
```

> 这样addItem()函数就会变成这样。

```js
function addItem(url, text) {
	var mylist = document.getElementById("mylist"),
		script = document.getElementById("list-item"),
		templateText = script.text,
		result = sprintf(template, url, text),
		div = document.createElement("div");
	
	div.innerHTML = result.replace(/^\s*/, "");
	list.appendChild(div.firstChild);
}

// 用法
addItem("/item/4", "Fourth item");
```
> 这个版本的函数有一处修改，即去掉了模板文件中的前导空格。之所以会出现这个多余的前导空格，是因为模板文本总是在`<script>`起始标签的下一行。如果将模板文本原样注入则会在`<div>`里创建一个文本节点，这个文本节点的内容是一个空格。而这个文本节点最终会代替`<div>`被添加进列表之中。 

### 方法3：复杂客户端模板

> 如果想用一些更健壮的模板，则可以考虑诸如Handlebars（http://handlebarsjs.com）所提供的解决方案。Handlebars是专为浏览器端Javascript设计的完整的客户端模板系统。

