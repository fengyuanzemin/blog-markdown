---
title: PHP/JS 正则匹配中文字符UTF-8
date: 2015-05-06
tag: [PHP, Javascript]
---


# 前言

本人今天一直想实现正则表达式匹配中文字符。无奈之前没得到大神的指导，一直以为JS和php匹配中文字符的正则表达式是一样的写法。

结果到后来一询问才发现自己大错特错。

<!--more-->

# JS写法
下面先说JS的写法吧：

```javascript
<script>
        var str="ftgfg风缘择敏hjkhj";
        var reg=/[\u4e00-\u9fa5]+/;
        if (reg.exec(str)) {
            alert('有中文');
        }
        else {
            alert('没有中文');
        }           
</script>
```
这段代码会弹窗输出为`有中文`。

## 注意

`^`和`$`有这样的作用

|正则表达式|匹配情况|
|-----|------|
|/^···$/|完全匹配|
|/^···/|以前面开头|
|/···$/|以后面结尾|
|/···/|匹配到即可|

# php写法
下面是php的写法：

当然是在UTF-8的编码条件下

```php
header("Content-type:text/html; charset=utf-8");

$str = "fhsjafh风缘择敏jkjk";
if(preg_match("/[\x{4e00}-\x{9fa5}]+/u", $str, $nameid)){
	echo $nameid[0];
}
else{
	echo "里面不含中文";
}
```
以上代码输出为`风缘择敏`

## 注意
若字符串为`$str = "fhsjafh风缘择敏jkjk敏择缘风ertry";`的话，代码只会输出`风缘择敏`，而后面那个字符串不会输出，这个具体怎么实现本人还不知道，所以说这是一次不完整的尝试。

倘若有读者知道也可以评论告诉我。我将它完善。

