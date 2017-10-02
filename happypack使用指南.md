---
title: 为什么看起来正常的ajax会error
date: 2017-08-11
tags: webpack
---

happypack是一个webpack插件，用于加快打包速度

# 第一步

首先把webpack的modules.rules的babel-loader给改成happypack的loader

<!--more-->

```js
// use happypack
{
    test: /.js$/,
    loaders: ['happypack/loader']
},
```

# 第二步
然后在happypack的插件里面再配置babel-loader就行了

```
new HappyPack({
    // loaders is the only required parameter:
    loaders: ['babel-loader'],
    // customize as needed, see Configuration below
    threadPool: happyThreadPool
})
```

# option

上面有个额外的配置threadPool，这个是线程池，在这里配置了系统的最大线程

```js
const HappyPack = require('happypack');
const os = require('os');
const happyThreadPool = HappyPack.ThreadPool({ size: os.cpus().length });
```

