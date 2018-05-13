---
title: react-router 4 嵌套路由实现
date: 2018-05-13
tags: Javascript
---

# 问题

我们没用使用官方提供的react-router-config，而是自己实现了一套。问题在于，自己实现的不支持嵌套路由，于是我周末想了想，进行了改造

<!--more--> 

## route
想支持却没有嵌套功能的路由：

```js
const RoutesConfig = {
    '/index': {
        component: asyncComponent(() => import('../containers/Home')),
    },
    '/a': {
        component: asyncComponent(() => import('../containers/A')),
    },
};
```
## usage

```jsx
<Switch>
	{
		getRoutes(match.path, routesConfig).map((item) => {
			return (<Route
				path={item.path}
				key={item.key}
	          component={item.component}
	        />
       );
      })
   }
</Switch>
```

## map

```js
export function getRoutes(path, routesConfig) {
    const routes = Object.keys(routesConfig).filter((routePath) =>
        routePath.indexOf(path) === 0 && routePath !== path);
    const routesArr = routes.map((item, key) => {
        return {
            path: item,
            key,
            component: routesConfig[item].component,
        };
    });
    return routesArr;
}
```

# 解决
首先，我觉得router的配置文件应该是一个数组，这样有顺序。其次，我看了上面 map 部分的代码，其实也是把它转化成数组来操作的。（我在尝试之后觉得用对象实在太麻烦，每用一次就要转义一次）。既然如此，我就直接用数组了。

## route

```js
const RoutesConfig = [
    {
        path: '/index',
        component: asyncComponent(() => import('../containers/Home')),
    },
    {
        path: '/a',
        component: asyncComponent(() => import('../containers/A')),
        children: [
            {
                path: '/a/b',
                component: asyncComponent(() => import('../containers/A/B')),
            },
        ],
    },
];
```
既然要实现嵌套路由，所以嵌套的部分必然也是通用的数据结构，这样才能进行调用。

## check

原先的 map 我发现和 usage 部分有点重复，所以只保留一个校验功能

```js
export const routeCheck = (path, routesConfig) => routesConfig.filter((item) =>
    item.path.indexOf(path) === 0 && item.path !== path);
```

## Usage

```js
renderRoute = (routes) => {
        if (!Array.isArray(routes)) {
            return null;
        }
        return routes.map((item) => {
            if (item.children && item.children.length > 0) {
                return (<div
                    key={`${item.key}-has-children`}
                >
                    <Route
                        path={item.path}
                        key={item.key || item.path}
                        component={item.component}
                    />
                    <Switch>
                        {
                            this.renderRoute(item.children)
                        }
                    </Switch>
                </div>);
            }
            return (<Route
                path={item.path}
                key={item.key || item.path}
                component={item.component}
            />);
        });
    };
```

然后，问题就顺利解决了

