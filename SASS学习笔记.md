---
title: SASS学习笔记
date: 2016-03-16 10:23:11
tags: SASS
---
# 安装
## MacOS下
当然，这需要ruby环境

	sudo gem install sass
	

## Sublime插件
Sass 和 SASS Build 两个插件。

安装成功后，.scss文件就可以正常使用代码高亮。如果文件名后缀是.css，也可以在控制台中搜索SASS选择 Set Syntax: SASS 以切换模式。可以使用 工具 > 编译 或者快捷键` Ctrl + B `编译文件，会自动在当前目录生成一个同名的编译后的CSS文件和CSS.MAP文件。

<!--more-->

# 运行
	sass 文件名.scss
	
这会在控制台输出编译产生的CSS，如果用.sass后缀，就会出错
# 学习

## 变量

SASS的变量命名是以`$`开头的，从下面的例子中可以看出，SASS拥有块状作用域，并且不存在变量提升。

### 拥有块状作用域

```sass
$nav-color: #F90;
nav {
  $width: 100px;
  width: $width;
  color: $nav-color;
}
$width:200px;
div {
	width: $width;
}

// 编译后
nav {
  width: 100px;
  color: #F90; }

div {
  width: 200px; }
```

### 不存在变量提升

也就是，先命名，再使用

```sass
div {
	width: $width;
}
$width:200px;

// 编译
// 报错
// Undefined variable: "$width".
```

### 全局变量

可以用`!global`定义全局变量，但是还是得先声明后使用。

```sass
#main {
  $width: 5em !global;
  width: $width;
}
#sidebar {
  width: $width;
}

// 编译后
#main {
  width: 5em; }
#sidebar {
  width: 5em; }

```

### 嵌套引用

字符串插值，需要用#{}进行包裹

```sass
$left:left;
.div1{
    border-#{$left}-width:5px;
}
// 编译后
.div1 {
  border-left-width: 5px; }
```

### 变量计算
还支持简单的变量计算

```sass
$left:20px;
.div1{
    margin-left:$left+12px;
}
// 编译后
.div1 {
  margin-left: 32px; }
```

多种运算方式

```sass
p {
  font: 10px/8px;             // Plain CSS, no division
  $width: 1000px;
  width: $width/2;            // Uses a variable, does division
  width: round(1.5)/2;        // Uses a function, does division
  height: (500px/2);          // Uses parentheses, does division
  margin-left: 5px + 8px/2px; // Uses +, does division
  font: (italic bold 10px/8px); // In a list, parentheses don't count
}

// 编译后
p {
  font: 10px/8px;
  width: 500px;
  width: 1;
  height: 250px;
  margin-left: 9px;
  font: italic bold 10px/8px; }
```


## 嵌套

### 一般嵌套

CSS中重复写选择器是非常恼人的。需要写一遍又一遍地写同一个ID，比如这样

```css
#content article h1 { color: #333 }
#content article p { margin-bottom: 1.4em }
#content aside { background-color: #EEE }
#content aside a { color: #000; }
```

所以我们得用嵌套的规则，这样就能少写一些东西了

```sass
#content {
	article {
		h1 { color:#333; }
		p { margin-bottom: 1.4em; }
	}
	aside { background-color: #EEE; 
		a { color: #000; }
	}
}

// 编译后
#content article h1 {
    color: #333;
}

#content article p {
    margin-bottom: 1.4em;
}

#content aside {
    background-color: #EEE;
}

#content aside a { color: #000;

}
```


### 代替父选择器的标识符&

```sass
article {
  nav + & { margin-top: 0 }
}

// 编译后
nav + article { 
	margin-top: 0; 
}
```

### 嵌套属性


```sass
nav {
  border: {
  style: solid;
  width: 1px;
  color: #ccc;
  }
}

// 编译后
nav {
  border-style: solid;
  border-width: 1px;
  border-color: #ccc; }
```
甚至可以这样

```scss
nav {
  border: 1px solid #ccc {
  left: 0px;
  right: 0px;
  }
}
// 编译后
nav {
  border: 1px solid #ccc;
    border-left: 0px;
    border-right: 0px; }
```


## 导入SASS文件

CSS 的@import规则允许在一个CSS文件中导入其他的CSS文件，但是这样会影响性能，只有执行到@import时，浏览器才会去下载其他CSS文件，导致页面加载起来特别慢。

而SASS的@import就会在生成CSS文件时将相关文件导入，不用发起额外的下载请求。

### 局部文件

有些SASS文件是不需要生成对应独立的CSS文件的，这些文件我们叫做局部文件。

局部文件以下划线开头，比如`_a.scss`，然后导入的时候省略下划线，.scss也可以省略，所以直接`@import "a"`，这样就行了。

### 默认变量值

我们用`!default`表示默认变量值。如果这个变量被声明赋值了，那就用它声明的值，否则就用这个默认值。

```scss
$fancybox-width: 200px;
$fancybox-width: 400px !default;
.fancybox {
	width: $fancybox-width;
}

// 编译后
.fancybox {
  width: 200px; }
```

### 嵌套导入

SASS允许@import写在CSS规则内。

比如`_a.scss`

```sass
aside {
  background: blue;
  color: white;
}
```

然后`b.scss`

```scss
.b {@import "a"}
```

生成的`b.css`

```css
.b aside {
  background: blue;
  color: white; }
```

### 原生的CSS导入
如果用SASS导入原生的CSS，SASS会认为你想直接使用CSS的@import属性。

但是由于SASS的语法完全兼容CSS，所以直接将.css改为.scss即可

## 静默注释

在CSS中，有些注释不希望别人看到。于是就有了SASS的静默注释

`//`的注释是在CSS中不可见的，而`/**/`是在CSS中可见的。

编译前

```sass
/* hello world */
// hi SASS

//编译后
/* hello world */
```

如果是中文字符

```sass
/* 这是可见的注释 */
// 这是不可见的注释

// 编译后
@charset "UTF-8";
/* 这是可见的注释 */
```

当注释出现在原生CSS不允许的地方时，也是会被去掉的

```sass
body {
  color /* 这块注释内容不会出现在生成的css中 */: #333;
  padding: 1; /* 这块注释内容会出现在生成的css中 */
}

// 编译后
@charset "UTF-8";
body {
  color: #333;
  padding: 1;
  /* 这块注释内容会出现在生成的css中 */ }
```

## 混合器

`@mixin`能定义一段代码，在不同的地方通过`@include`引用

```sass
@mixin rounded-corners {
  -moz-border-radius: 5px;
  -webkit-border-radius: 5px;
  border-radius: 5px;
}

notice {
  background-color: green;
  border: 2px solid #00aa00;
  @include rounded-corners;
}

// 编译后
notice {
  background-color: green;
  border: 2px solid #00aa00;
  -moz-border-radius: 5px;
  -webkit-border-radius: 5px;
  border-radius: 5px; }
```

### 何时应该使用混合器

利用混合器，可以很容易地在样式表的不同地方共享样式。如果你发现自己在不停地重复一段样式，那就应该把这段样式构造成优良的混合器，尤其是这段样式本身就是一个逻辑单元，比如说是一组放在一起有意义的属性。

### 混合器中的CSS规则

混合器中不仅可以包含属性，也可以包含CSS规则，包含选择器和选择器中的属性。

```sass
@mixin no-bullets {
  list-style: none;
  li {
    list-style-image: none;
    list-style-type: none;
    margin-left: 0px;
  }
}
ul.plain {
  color: #444;
  @include no-bullets;
}

// 编译后
ul.plain {
  color: #444;
  list-style: none; }
  ul.plain li {
    list-style-image: none;
    list-style-type: none;
    margin-left: 0px; }
```

### 给混合器传参
就像函数一样引用

```sass
@mixin link-colors($normal, $hover, $visited) {
  color: $normal;
  &:hover { color: $hover; }
  &:visited { color: $visited; }
}
a {
  @include link-colors(blue, red, green);
}

// 编译后
a {
  color: blue; }
  a:hover {
    color: red; }
  a:visited {
    color: green; }
```

这是另一种方法

```sass
@mixin link-colors($normal, $hover, $visited) {
  color: $normal;
  &:hover { color: $hover; }
  &:visited { color: $visited; }
}
a {
    @include link-colors(
      $normal: blue,
      $visited: green,
      $hover: red
  );
}
// 编译后
a {
  color: blue; }
  a:hover {
    color: red; }
  a:visited {
    color: green; }
```

### 默认参数

还是像函数一样，可以有默认参数

```sass
 @mixin link-colors(
    $normal,
    $hover: $normal,
    $visited: $normal
  )
{
  color: $normal;
  &:hover { color: $hover; }
  &:visited { color: $visited; }
}
a {
	@include link-colors(red);
}

// 编译后
a {
  color: red; }
  a:hover {
    color: red; }
  a:visited {
    color: red; }
```


## 使用选择器继承来精简CSS

.seriousError会继承.error的所有样式，包括跟.error有关的。

```sass
.error {
  border: 1px red;
  background-color: #fdd;
}
.error a{
  color: red;
  font-weight: 100;
}
h1.error {
  font-size: 1.2rem;
}
.seriousError {
  @extend .error;
  border-width: 3px;
}

//编译后
.error, .seriousError {
  border: 1px red;
  background-color: #fdd; }

.error a, .seriousError a {
  color: red;
  font-weight: 100; }

h1.error, h1.seriousError {
  font-size: 1.2rem; }

.seriousError {
  border-width: 3px; }
```

### 继承的高级用法

高级用法就是继承一个HTML元素的样式

如下就是继承了`<a>`的样式

```sass
.disabled {
	color: grey;
	@extend a;
}
```

### 继承的工作细节

关于`@extend`有两个要点你应该知道。

* 跟混合器相比，继承生成的css代码相对更少。因为继承仅仅是重复选择器，而不会重复属性，所以使用继承往往比混合器生成的css体积更小。如果你非常关心你站点的速度，请牢记这一点。
* 继承遵从css层叠的规则。当两个不同的css规则应用到同一个html元素上时，并且这两个不同的css规则对同一属性的修饰存在不同的值，css层叠规则会决定应用哪个样式。相当直观：通常权重更高的选择器胜出，如果权重相同，定义在后边的规则胜出。

