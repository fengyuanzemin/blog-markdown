---
title: 用CSS画一个红绿灯
date: 2018-02-10
tags: CSS
---

几天前看到一个面试文，说自己面试时不会用CSS手写红绿灯，我想试试。

<!--more-->

## 基本
首先得有红绿灯的模型是吧。

```html
<!DOCTYPE html>
<html>
<head>
	<title>我是一个红绿灯</title>
	<style type="text/css">
		.container {
			width: 200px;
			height: 250px;
			padding: 50px 0;
			background: #333;
			display: flex;
			flex-direction: column;
			align-items: center;
			justify-content: space-between;
			border-radius: 8px;
		}
		.red, .green {
			width: 100px;
			height: 100px;
			border-radius: 50%;
		}
		.red {
			background: red;
		}
		.green {
			background: green;
		}
	</style>
</head>
<body>
	<div class="container">
		<div class="red"></div>
		<div class="green"></div>
	</div>
</body>
</html>
``` 

画好了

![基本红绿灯](http://7xn2lr.com1.z0.glb.clouddn.com/light%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202018-02-10%20%E4%B8%8B%E5%8D%882.19.26.png?imageView2/2/w/200/h/200/q/75|watermark/2/text/RmVuZw==/font/5b6u6L2v6ZuF6buR/fontsize/240/fill/I0ZGRkZGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

## 不会动的红绿灯是红绿灯吗
接下来就是增加动画了。假设我们的红灯（red）是3秒，绿灯（green）是1秒。没到时间灯应该是黑的（#222）。

那么一个周期就是4s，结束后又重新开始。

考虑到红绿灯是突然变化，而不是渐变，于是 在 0% ~ 74% 保证一个颜色，75% 之后开始突变，直到 100%。如下图：

![动画介绍](http://7xn2lr.com1.z0.glb.clouddn.com/light%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202018-02-10%20%E4%B8%8B%E5%8D%883.55.17.png?imageView2/2/w/800/h/400/q/75|watermark/2/text/RmVuZw==/font/5b6u6L2v6ZuF6buR/fontsize/240/fill/I0ZGRkZGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

```css
.red {
	animation: light_red 4s infinite;
}
.green {
	animation: light_green 4s infinite;
}
@keyframes light_red {
	0%, 74% {
		background-color: red;
	}
	75%, 100% {
		background-color: #222;
	}
}
@keyframes light_green {
	0%, 74% {
		background-color: #222;
	}
	75%, 100% {
		background-color: green;
	}
}
```
只要是 animation-duration 是 4 的倍数就能保证时间是整数秒。

## 结束的太突然了吧

至少快结束了我得知道是吧。加一个闪烁的效果。为了好看，我把 animation-duration 设置为 4 * 4 = 16（s）。

![加闪烁动画](http://7xn2lr.com1.z0.glb.clouddn.com/light%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202018-02-10%20%E4%B8%8B%E5%8D%886.08.06.png?imageView2/2/w/1200/h/800/q/75|watermark/2/text/QEZlbmc=/font/5b6u6L2v6ZuF6buR/fontsize/520/fill/I0ZGRkZGRg==/dissolve/77/gravity/SouthEast/dx/10/dy/10|imageslim)

以红灯为例。绿灯进行相应修改就行。

```css
.red {
	animation: light_red 16s infinite;
}
@keyframes light_red {
	0%, 56%, 58%, 62%, 64%, 68%, 70%, 74% {
		background-color: red;
	}
	57%, 63%, 69%, 75%, 100% {
		background-color: #222;
	}
}
```

## 黄灯呢

sorry 我给忘了，现在给加一个。但是动画也需要改动。考虑再三，红灯和绿灯再也不是 `3:1` 的关系了，而是跟着情况变动，尽量时间为整数。黄灯给了 3s 的时间。换算下来，总体给了 20s 的时间。

修改为：红灯占有 0% ～ 60% ，黄灯占有 60% ～ 75%，绿灯占有 75% ～ 100%。


```css
.yellow {
	animation: light_yellow 20s infinite;
}
@keyframes light_yellow {
	0%, 60%, 65%, 70%, 76%, 100% {
		background-color: #222;
	}
	61%, 64%, 66%, 69%, 71%, 75% {
		background-color: yellow;
	}
}
```

![黄灯](http://7xn2lr.com1.z0.glb.clouddn.com/light%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202018-03-04%20%E4%B8%8B%E5%8D%883.54.11.png?imageView2/2/w/200/h/200/q/75|watermark/2/text/RmVuZw==/font/5b6u6L2v6ZuF6buR/fontsize/240/fill/I0ZGRkZGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

## 我想要那种小人在走的样子

绿灯的时候是一个人在走，红灯是一个人在站着。

首先我们来画个人。头部、躯干、四肢，就是一个人的组成了。

### 站立

```css
.human > div {
	background: red;
}
.human-head {
	height: 17px;
	width: 17px;
	border-radius: 50%;
	position: absolute;
	top: 9px;
	left: 40px;
}

.human-body {
	height: 25px;
	width: 17px;
	left: 40px;
	top: 28px;
	border-radius: 3px;
	position: absolute;
}

.human-arms {
	width: 30px;
	height: 6px;
	position: absolute;
	top: 26px;
	border-radius: 5px;
}

.human-left-arms {
	left: 7px;
	transform-origin: right;
	transform: rotate(-60deg);
}

.human-right-arms {
	left: 60px;
	transform-origin: left;
	transform: rotate(60deg);
}

.human-leg {
	position: absolute;
	top: 54px;
	border-radius: 5px;
}

.human-left-leg {
	width: 8px;
	height: 40px;
	left: 40px;
	transform-origin: 0 1 0;
}

.human-right-leg {
	width: 8px;
	height: 40px;
	left: 50px;
	transform-origin: 0 0 0;
}
```

```html
<div class="human">
	<div class="human-head"></div>
	<div class="human-body"></div>
	<div class="human-arms human-left-arms"></div>
	<div class="human-arms human-right-arms"></div>
	<div class="human-leg human-left-leg"></div>
	<div class="human-leg human-right-leg"></div>
</div>
```

![](http://7xn2lr.com1.z0.glb.clouddn.com/light%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202018-03-03%20%E4%B8%8B%E5%8D%888.01.49.png?imageView2/2/w/200/h/200/q/75|watermark/2/text/RmVuZw==/font/5b6u6L2v6ZuF6buR/fontsize/240/fill/I0ZGRkZGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

### 行走

只需要在 站 的基础上改一下 transform: rotate ，一个静态行走的人就出现了。

![](http://7xn2lr.com1.z0.glb.clouddn.com/light%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202018-03-03%20%E4%B8%8B%E5%8D%888.20.03.png?imageView2/2/w/200/h/200/q/75|watermark/2/text/RmVuZw==/font/5b6u6L2v6ZuF6buR/fontsize/240/fill/I0ZGRkZGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

#### 动画：手臂、腿

以左手为例，是 -60 度到 -120 度的摆动，写的时候要结合自己 20s 的总时间进行计算，其中还要注意后三秒还得闪一下。

```css
@keyframes green_left_arms {
	0%,
	75%,
	85%,
	90%,
	95% {
		background-color: #222;
	}
	76%,
	80.8%,
	85.6%,
	90.4%,
	95.2%,
	100% {
		background-color: green;
		transform: rotate(-60deg);
	}
	78.4%,
	83.2%,
	88%,
	92.8%,
	97.6% {
		transform: rotate(-120deg);
	}
	84%,
	86%,
	89%,
	91%,
	94%,
	96% {
		background-color: green;
	}
}
```

## 结束

最后贴一个完成稿。[http://jsrun.net/XrqKp](http://jsrun.net/XrqKp)

### 感想

这个文从年前就开始写，后来去过年，后来又打游戏。反反复复才写完。感觉自己最近在舒适区，太安逸了。要想办法改一下。
