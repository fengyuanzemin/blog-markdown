---
title: PHP判断用户当天第一次登录
date: 2015-06-09
tag: PHP
---


#前言

>前段时间在做一个项目，人家要求每天第一次登录时自动送积分。

居然不是签到送积分，我郁闷了几秒钟。

在百度了一会无果后，我思前想后终于想出这种方法。

也就是说，积分是默默送的，当别人查看积分时才知道有这回事，不然不知道。

# 扯皮

由于每次那个APP点开用户界面都需要登录，所以这就好办了，我只需要在登录的时候顺便判断是不是每天第一次登录就行。

但是有些事说起来比做起来简单。怎么才能判断出来呢。

通过查询`users`数据库，我默默的了解到数据库中有`last_login`这个字段。记录的是用户每次的登录时间的格林威治时间的时间戳。

## 格林威治时间
>格林尼治标准时间（旧译格林尼治平均时间或格林威治标准时间；英语：Greenwich Mean Time，GMT）是指位于英国伦敦郊区的皇家格林尼治天文台的标准时间，因为本初子午线被定义在通过那里的经线。自1924年2月5日开始，格林尼治天文台每隔一小时会向全世界发放调时信息。

## 时间戳
>时间戳（timestamp），通常是一个字符序列，唯一地标识某一刻的时间。数字时间戳技术是数字签名技术一种变种的应用。

于是，只要登录的时候后，先查询到上一次的时间戳（此时还没修改数据），记录下来；然后修改时间戳为最新，记录下来，两个时间戳相比较。这里就用到`getdate()`函数

## getdate()

> getdate() 函数取得日期／时间信息。


| 键名 | 说明 | 返回值例子 |
| ------ | -----: | ------: |
| "year"| 4 位数字表示的完整年份	| 例如：1999 或 2003 |
| "yday" | 一年中第几天的数字表示 	| 0 到 365 |


相比较，当year和yday不全相同时，就说明上一次的登录时间不是今天，也就是说今天是第一次登录。

然后就判断出来了，送积分是后面的事


# 实现

上面说了这么多，下面就贴代码好了。

这段代码放在登录的函数里面。

假设$user_id已定，那么可以有下面的方法来

```php
/*查询到上一次登录的last_login*/
$sql = "SELECT last_login FROM users
	WHERE user_id = '$user_id'";

/*获取上一次登录的格林威治时间的时间戳*/
$last_login_old = $GLOBALS['db']->getOne($sql);

/*获得上一次登录日期*/
$last_login_date_old = getdate($last_login_old);

/*更新用户最后一次登录时间last_login*/    
$sql = "UPDATE users SET last_login = '" .gmtime(). "'
			WHERE user_id = '$user_id'";

/*执行UPDATE*/
$GLOBALS['db']->query($sql);

/*查询到最新的登录的格林威治时间的时间戳*/
$last_login_new = gmtime();

/*获得最新日期*/
$last_login_date_new = getdate($last_login_new);

/*进行比较，yday是一年的第几天*/
/*若出现不等于的情况，就说明是当天第一次登录*/
if($last_login_date_old['year'] != $last_login_date_new['year'] || $last_login_date_old['yday'] != $last_login_date_new['yday'])
    {
        /*判断成功，doSomething*/
    }	
```

还需要这个函数支持

```php
	/**
 	 * 获得当前格林威治时间的时间戳
	 * @return  integer
 	 */
	function gmtime()
	{
    	return (time() - date('Z'));
	}

```
