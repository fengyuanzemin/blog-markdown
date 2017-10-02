---
title: webpack 配置增加HMR
date: 2017-07-29
tags: webpack
---


根据官网教程
[https://webpack.js.org/api/hot-module-replacement/](https://webpack.js.org/api/hot-module-replacement/)

# 开启 devServer.hot

首先需要在 webpack 中开启 devServer.hot = true

```js
devServer: {
     hot: true
}
```

<!--more-->

然后插件中添加 HotModuleReplacementPlugin

```js
plugins: [
  new webpack.HotModuleReplacementPlugin()
],
```

# 安装 react-hot-loader

我这里使用的是 react，需要 react-hot-loader

首先安装它的 next 版本

```sh
yarn add -D react-hot-loader@next
```

添加之后根据它的官网教程 [https://github.com/gaearon/react-hot-loader/tree/master/docs#migration-to-30](https://github.com/gaearon/react-hot-loader/tree/master/docs#migration-to-30)

在 webpack 的 entry 中添加 react-hot-loader/patch
注意要添加到入口文件之前

将.babelrc 增加一项

```json
"plugins": ["react-hot-loader/babel"]
```

# 加入 module.hot.accept

最后将入口文件加载入 module.hot.accept 里

```js
import React from 'react'
import ReactDOM from 'react-dom'
import { AppContainer } from 'react-hot-loader'

import App from './containers/App'

const render = Component => {
  ReactDOM.render(
    <AppContainer>
      <Component />
    </AppContainer>,
    document.getElementById('root')
  )
}

render(App)
// 最重要的是这里
if (module.hot) {
  module.hot.accept('./containers/App', () => { render(App) })
}
```








