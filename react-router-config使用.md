---
title: react-router-config 使用
date: 2017-08-09
tags: [react, react-router]
---

# react-router@4

react-router升级4之后，要想使用config方式的路由模式，要么自己写一个函数遍历config，要么使用react-router-config

<!--more-->

# react-router-config

[https://github.com/ReactTraining/react-router/tree/master/packages/react-router-config](https://github.com/ReactTraining/react-router/tree/master/packages/react-router-config)

## renderRoutes

react-router-config还是beta版本，其中有 renderRoutes 方法，其作用就是遍历你的routes，返回<Router>和<Switch>

```js
import React from 'react'

import Switch from 'react-router/Switch'

import Route from 'react-router/Route'

const renderRoutes = (routes, extraProps = {}) => routes ? (

  <Switch>

    {routes.map((route, i) => (

      <Route key={i} path={route.path} exact={route.exact} strict={route.strict} render={(props) => (

        <route.component {...props} {...extraProps} route={route}/>

      )}/>

    ))}

  </Switch>

) : null

export default renderRoutes

```

## config文件

而对于config文件，是这样的写法

```js
const routes = [
    {
        component: Layout,
        routes: [
            {
                path: '/',
                exact: true,
                component: IndexContainer,
            },
            {
                path: '/*',
                component: NotFound,
            },
        ],
    },
];

```

在使用时，需要用renderRoutes(routes)

```js
import {
    BrowserRouter as Router,
} from 'react-router-dom';
import { renderRoutes } from 'react-router-config';
 
<Router>
    {renderRoutes(routes)}
</Router>
```

## 嵌套路由

而且，如果是嵌套路由，那么父路由处也需要写这个渲染函数
比如在 Layout 组件里

```js
import React from 'react';
import { renderRoutes } from 'react-router-config';

export default class Layout extends React.PureComponent {

    render() {
        return (
            <div className="layout-page">
                {renderRoutes(this.props.route.routes)}
            </div>
        );
    }
}
```


