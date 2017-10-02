---
title: CSS显示滚动条
date: 2017-07-14
tags: CSS
---

# 一般使用

一般来说，使用
 
```css
overflow-y: scroll;
```

<!--more-->

就可以显示了
 
但是在Mac上，只有滚动时才会显示滚动条，不滚动时会隐藏。
 
> In OS X Lion, overflow set to "scroll" behaves more like auto in that scrollbars will only show when being used. They will disappear when not in use.     – stackoverflow

# 解决方案

研究之后，加载下面的CSS可以一直显示


```css 
::-webkit-scrollbar { -webkit-appearance: none; width: 7px; }

::-webkit-scrollbar-thumb { border-radius: 4px; background-color: rgba(0, 0, 0, .5); -webkit-box-shadow: 0 0 1px rgba(255, 255, 255, .5); }
```