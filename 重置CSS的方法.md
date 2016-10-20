---
title: 重置CSS的方法
date: 2015-10-07 10:58:37 
tags: CSS
---

使用重置样式表让不同的浏览器渲染行为保持一致

# 来自 ‘最烂的reset.css’

```css
 * { 
 	margin: 0; 
 	padding: 0; 
 }
```

<!--more-->

# 来自《Web站点优化》

```css
html, body, div, span, applet, object, iframe, h1, h2, h3, h4, h5, h6, p, blockquote, pre, a, abbr, acronym,
address, big, cite, code, del, dfn, em, font, img, ins, kbd, q, s, samp, small, strike, strong, sub, sup, tt, 
var, b, u, i, center, dl, dt, dd, ol, ul, li, fieldset, form, label, legend, table, caption, tbody, tfoot, thead, tr, th, td {
    margin:0;
    padding:0;
    border:0;
    outline:0;
    font-size:100%;
    vertical-align:baseline;
    background:transparent;    
}
body {
    line-height:1;
}
ol,ul {
    list-style:none;
}
blockquote, q {
    quotes:none;
}
blockquote:before, blockquote:after, q:before, q:after {
    content: '';
    content:none;
}
/* remeber to define focus styles! */
:focus {
    outline:0;
}
/* remeber to highlight inserts somehow */
ins {
    text-decoration:none;
}
del {
    text-decoration:line-through;
}

/* tables still need 'cellspacing="0" in the markup' */
table {
    border-collapse:collapse;
    border-spacing:0;
}
```

# 来自 张鑫旭

张大大告诉我们要用合适的，不用的就删掉

http://www.zhangxinxu.com/wordpress/2010/08/html5-css-reset/



```css
/* 
html5doctor.com Reset Stylesheet
v1.4.1 
2010-03-01
Author: Richard Clark - http://richclarkdesign.com
*/

html, body, div, span, object, iframe,
h1, h2, h3, h4, h5, h6, p, blockquote, pre,
abbr, address, cite, code,
del, dfn, em, img, ins, kbd, q, samp,
small, strong, sub, sup, var,
b, i,
dl, dt, dd, ol, ul, li,
fieldset, form, label, legend,
table, caption, tbody, tfoot, thead, tr, th, td,
article, aside, canvas, details, figcaption, figure, 
footer, header, hgroup, menu, nav, section, summary,
time, mark, audio, video {
    margin:0;
    padding:0;
    border:0;
    outline:0;
    font-size:100%;
    vertical-align:baseline;
    background:transparent;
}

body {
    line-height:1;
}

:focus {
	outline: 1;
}

article,aside,canvas,details,figcaption,figure,
footer,header,hgroup,menu,nav,section,summary { 
    display:block;
}

nav ul {
    list-style:none;
}

blockquote, q {
    quotes:none;
}

blockquote:before, blockquote:after,
q:before, q:after {
    content:'';
    content:none;
}

a {
    margin:0;
    padding:0;
    border:0;
    font-size:100%;
    vertical-align:baseline;
    background:transparent;
}

ins {
    background-color:#ff9;
    color:#000;
    text-decoration:none;
}

mark {
    background-color:#ff9;
    color:#000; 
    font-style:italic;
    font-weight:bold;
}

del {
    text-decoration: line-through;
}

abbr[title], dfn[title] {
    border-bottom:1px dotted #000;
    cursor:help;
}

table {
    border-collapse:collapse;
    border-spacing:0;
}

hr {
    display:block;
    height:1px;
    border:0;   
    border-top:1px solid #cccccc;
    margin:1em 0;
    padding:0;
}

input, select {
    vertical-align:middle;
}
```

# 来自 玉伯和正淳

玉伯和正淳一起整理的一份 reset.css

```css
/*
KISSY CSS Reset
理念：清除和重置是紧密不可分的
特色：1.适应中文 2.基于最新主流浏览器
维护：玉伯(lifesinger@gmail.com), 正淳(ragecarrier@gmail.com)
*/

/* 清除内外边距 */
body, h1, h2, h3, h4, h5, h6, hr, p, blockquote, /* structural elements 结构元素 */
dl, dt, dd, ul, ol, li, /* list elements 列表元素 */
pre, /* text formatting elements 文本格式元素 */
fieldset, lengend, button, input, textarea, /* form elements 表单元素 */
th, td { /* table elements 表格元素 */
    margin: 0;
    padding: 0;
}

/* 设置默认字体 */
body,
button, input, select, textarea { /* for ie */
    /*font: 12px/1 Tahoma, Helvetica, Arial, "宋体", sans-serif;*/
    font: 12px/1 Tahoma, Helvetica, Arial, "\5b8b\4f53", sans-serif; /* 用 ascii 字符表示，使得在任何编码下都无问题 */
}

h1 { font-size: 18px; /* 18px / 12px = 1.5 */ }
h2 { font-size: 16px; }
h3 { font-size: 14px; }
h4, h5, h6 { font-size: 100%; }

address, cite, dfn, em, var { font-style: normal; } /* 将斜体扶正 */
code, kbd, pre, samp, tt { font-family: "Courier New", Courier, monospace; } /* 统一等宽字体 */
small { font-size: 12px; } /* 小于 12px 的中文很难阅读，让 small 正常化 */

/* 重置列表元素 */
ul, ol { list-style: none; }

/* 重置文本格式元素 */
a { text-decoration: none; }
a:hover { text-decoration: underline; }

abbr[title], acronym[title] { /* 注：1.ie6 不支持 abbr; 2.这里用了属性选择符，ie6 下无效果 */
    border-bottom: 1px dotted;
    cursor: help;
}

q:before, q:after { content: ''; }

/* 重置表单元素 */
legend { color: #000; } /* for ie6 */
fieldset, img { border: none; } /* img 搭车：让链接里的 img 无边框 */
/* 注：optgroup 无法扶正 */
button, input, select, textarea {
    font-size: 100%; /* 使得表单元素在 ie 下能继承字体大小 */
}

/* 重置表格元素 */
table {
    border-collapse: collapse;
    border-spacing: 0;
}

/* 重置 hr */
hr {
    border: none;
    height: 1px;
}

/* 让非ie浏览器默认也显示垂直滚动条，防止因滚动条引起的闪烁 */
html { overflow-y: scroll; }
```

# 来自 sofish

sofish提到的Yahoo的YUI提供的CSS重置文件

```css
body,div,dl,dt,dd,ul,ol,li,h1,h2,h3,h4,h5,h6,pre,form,fieldset,input,textarea,p,blockquote,th,td {
	margin:0;
   padding:0;
}
table {
	border-collapse:collapse;
	border-spacing:0;
}
fieldset,img { 
	border:0;
}
address,caption,cite,code,dfn,em,strong,th,var {
	font-style:normal;
	font-weight:normal;
}
ol,ul {
	list-style:none;
}
caption,th {
	text-align:left;
}
h1,h2,h3,h4,h5,h6 {
	font-size:100%;
	font-weight:normal;
}
q:before,q:after {
	content:'';
}
abbr,acronym { 
	border:0;
}
```

# 来自 NETTUTS

NETTUTS上的 Jeffrey Way写了篇文章Weekend Quick Tip: 

Create Your Own Simple Reset.css File，释出自己用来重置CSS样式表的方法


```css
body, html, div, blockquote, img, label, p, h1, h2, h3, h4, h5, h6, pre, ul, ol,
li, dl, dt, dd, form, a, fieldset, input, th, td {
	margin: 0; 
	padding: 0; 
	border: 0; 
	outline: none;
}
body {
	line-height: 1;
	font-size: 88% /* Decide for yourself if you want to include this. */;
}
h1, h2, h3, h4, h5, h6 {
	font-size: 100%;
	padding: .6em 0;
	margin: 0 15px;
}
ul, ol {
	list-style: none;
}
a {
	color: black;
	text-decoration: none;
}
a:hover {
	text-decoration: underline;
}
.floatLeft {
	float: left;
	padding: .5em .5em .5em 0;
}
.floatRight {
	float: right;
	padding: .5em 0 .5em .5em;
}
```