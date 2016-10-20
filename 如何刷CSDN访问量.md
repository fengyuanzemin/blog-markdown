---
title: 如何刷CSDN访问量
date: 2015-11-22 09:21:47
tags: 后端
---

这两天研究了一下，怎么去增加CSDN的访问量

在研究了一会，我发现了三种方法可以增加访问量。

先说说它的原理吧，在CSDN里面呢，当浏览器打开它的一个页面的时候，就会主动记录下浏览器的session，然后访问量+1。

这个时候，当页面刷新，访问量并不会增加。而是要打开另一个页面，访问量才会+1。

所以想刷访问量，刷新当前页面是不行的。不断要打开另一个页面，然后定时。

在无数次尝试之后，我发现了一种方法，那就是。。。

<!--more-->

# 第一种
下载20个浏览器，每个浏览器打开20个页面，然后一直刷新，一直更换页面，这样就可以刷到很高很高的访问量了呢。

# 第二种
终于讲到用编程的方法了，大家一定很期待吧

首先是用shell命令：`crontab`

然后呢，当然是需要一个能运行linux命令的机器，是服务器最好，这样就可以放在上面跑，根本不用管它了。

先新建一个文件，比如csdncron文件吧

```sh
$ vi csdncron
```
然后进入vim编辑器，在里面写下：

`#`为注释

```sh
#每分钟定时执行命令，刷
* * * * * curl 
```
最后执行：

```sh
$ crontab csdncron
```
这样可以查看正在执行的定时任务

```sh
$ crontab -l
```


# 第三种
用PHP来写也是一样的，当能用PHP之后，我发现，其实只要是爬数据，爬那个网页，就能让访问量加一了。

于是还是需要一个PHP的运行环境

然后写下如下PHP代码，假设保存为csdn.php：

```php
<?php
header("Content-Type: text/html;charset=utf-8");

$url = array();
$url[0] = "http://blog.csdn.net/fengyuanzemin/article/details/50912621";

while(true) {
	// 随机文章
	$rand = rand(0,count($url) - 1);
	// 初始化
	$ch = curl_init();
	//设置选项，包括URL
	curl_setopt($ch, CURLOPT_URL, $url[$rand]);
	curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
	curl_setopt($ch, CURLOPT_HEADER, 0);
	//执行并获取HTML文档内容
	$output = curl_exec($ch);
	//释放curl句柄
	curl_close($ch);
	//打印获得的数据
	// print_r($output);
	// echo $output;
	// 延迟10秒
	usleep(10 * 1000 * 1000);
}
 ?>
```

最后在终端运行，或者浏览器打开这个PHP脚本

```sh
$ php csdn.php &
```
然后终端会出现一个进程的PID，假如是9999，如果不想刷了，kill掉它就行了

```sh
$ kill 9999
```
如果找不到PID，可以用命令来找

```sh
$ ps aux | grep php
```

至于浏览器的，关闭浏览器就行了。


# 总结
说白了，就是去加载页面，把HTML内容爬下来，访问量自然就增加了。

没啥技术含量，就图个好玩儿。