---
title: react-router 4 按需加载
date: 2017-08-20
tags: [Javascript, react-router, react]
---

根据官网文档：[https://reacttraining.com/react-router/web/guides/code-splitting](https://reacttraining.com/react-router/web/guides/code-splitting)

首先得安装bundle-loader

```sh
yarn add bundle-loader
```
<!--more--> 

根据官网文档，定义了一个Bundle的组件

```jsx
import React, { Component } from 'react'; 
 
export default class Bundle extends Component { 
    state = { 
            // short for "module" but that's a keyword in js, so "mod" 
        mod: null, 
    }; 
 
    componentWillMount() { 
        this.load(this.props); 
    } 
 
    componentWillReceiveProps(nextProps) { 
        if (nextProps.load !== this.props.load) { 
            this.load(nextProps); 
        } 
    } 
 
    load(props) { 
        this.setState({ 
            mod: null, 
        }); 
        props.load((mod) => { 
            this.setState({ 
                    // handle both es imports and cjs 
                mod: mod.default ? mod.default : mod, 
            }); 
        }); 
    } 
 
    render() { 
        return this.state.mod ? this.props.children(this.state.mod) : null; 
    } 
}
```
加载组件的时候使用bundle-loader加载，并且将组件传入Bundle中，最后渲染使用renderRoutes方法就可以了

```js
import React from 'react'; 
import { 
    BrowserRouter as Router, 
} from 'react-router-dom'; 
import { renderRoutes } from 'react-router-config'; 
 
/* eslint-disable */ 
import Index from 'bundle-loader?lazy&name=index!app/components/Demo/Index'; 
import IndexContainer from 'bundle-loader?lazy&name=index!app/components/Demo/IndexContainer'; 
import ListContainer from 'bundle-loader?lazy&name=list!app/components/Demo/ListContainer'; 
import NotFound from 'bundle-loader?lazy&name=notfound!app/components/Demo/NotFound'; 
 
/* eslint-enable */ 
 
import Bundle from 'app/components/Demo/Bundle'; 
 
 
const routes = [ 
    { 
        component: (props) => ( 
            <Bundle load={Index}> 
                {(Component) => <Component {...props}/>} 
            </Bundle> 
        ), 
        routes: [ 
            { 
                path: '/', 
                exact: true, 
                component: (props) => ( 
                    <Bundle load={IndexContainer}> 
                        {(Component) => <Component {...props}/>} 
                    </Bundle> 
                ), 
            }, 
            { 
                path: '/list/:moduleName', 
                exact: true, 
                component: (props) => ( 
                    <Bundle load={ListContainer}> 
                        {(Component) => <Component {...props}/>} 
                    </Bundle> 
                ), 
            }, 
            { 
                path: '/form/:moduleName/:id', 
                exact: true, 
                component: (props) => ( 
                    <Bundle load={FormContainer}> 
                        {(Component) => <Component {...props}/>} 
                    </Bundle> 
                ), 
            }, 
            { 
                path: '/*', 
                component: (props) => ( 
                    <Bundle load={NotFound}> 
                        {(Component) => <Component {...props}/>} 
                    </Bundle> 
                ), 
            }, 
        ], 
    }, 
]; 
 
export default ( 
    <Router> 
        {renderRoutes(routes)} 
    </Router> 
);
```
