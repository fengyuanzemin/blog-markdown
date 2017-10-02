---
title: 为什么看起来正常的ajax会error
date: 2017-07-07
tags: bug记录
---

今天做项目时，有个ajax mock数据，数据看起来是正常的json，却一直返回到error回调里。

<!--more-->
 
找了很久发现有这句话
 
> “Ensure that what you are returning is valid json. If its not, and everything goes correct on the server, $.ajax will go to error rather than success.”
 
> “保证你返回的是合法的json。如果不是，而服务器也一切正常，那么$.ajax会返回error。”
 
最后发现返回的数据是不合法的json，里面有个中文括号，气哭。