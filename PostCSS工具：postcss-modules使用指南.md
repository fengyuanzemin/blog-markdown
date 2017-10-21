---
title: PostCSS工具：postcss-modules使用指南
date: 2017-10-21
tags: [PostCSS, CSS, Plugin]
---

# 前言

PostCSS 作为一个强大的样式转化工具，使用JS编写，能运行在 Webpack、Gulp、Grunt 以及 Node 上。这些插件可以检查（lint）你的 CSS，支持 CSS Variables 和 Mixins， 编译尚未被浏览器广泛支持的先进的 CSS 语法，内联图片，以及其它很多优秀的功能。

根据[官网](https://github.com/postcss/postcss/blob/master/README.cn.md)统计，已经有200+的插件。而 [postcss-modules](https://github.com/css-modules/postcss-modules) 就是其中之一。今天我们来介绍 postcss-modules 的使用。

# 安装

```sh
yarn install postcss-modules -D
```

# 使用

首先得有个 postcss.config.js 文件（可能大家都有），然后将配置写在代码里即可。

```js
const path = require('path');
const fs = require('fs');
                    
module.exports = () => {
    return {
        plugins: [
            require('postcss-modules')({
                getJSON: (cssFileName, json) => {
                    const cssName = path.basename(cssFileName, '.pcss');
                    const jsonFileName = path.resolve(`./postcss/json/${cssName}.json`);
                    fs.writeFileSync(jsonFileName, JSON.stringify(json));
                },
                generateScopedName: '[name]__[local]___[hash:base64:5]',
            }),
        ],
    };
};
```

解释一下上面配置的含义，在 getJSON 方法中，会生成相应 .pcss 文件的 .json 文件。比如有个 index.pcss ，在执行之后就会出现 index.json 。

在 generateScopedName 方法中，是为调整生成 CSS 模块的具体名字。 [name] 是文件名，[local]是 CSS 属性名，[hash:base64:5] 表示文件用 base64 的 hash 取值方式，并只取前 5 位。

## 生成文件示例

假如有这样的 pcss 文件，index.pcss

```scss
.content-wrap {
    a {
        color: #000;
    }
    .title {
        color: #333;
    }
}
```

执行完毕后将得到文件 index.json

```json
{
    "content-wrap": "layout__content-wrap___3B52N",
    "title": "layout__title___3_Dh4"
}
```

于此同时，打包转换出来的 CSS 也进行了模块化的更新。

```css
.layout__content-wrap___3B52N a {
	color: #000;
}

.layout__content-wrap___3B52N .layout__title___3_Dh4 {
	color: #333;
}
```

## JS 使用示例
上面生成了文件，但是归根到底，如果类名没换成它生成的 JSON 里的类名，是不起作用的。

下面我们用 React 来演示怎么把类名换掉。

### 没换之前

```jsx
import React, { PureComponent } from 'react';

export default class Demo extends PureComponent {
    render() {
        return (
        	<div className="content-wrap">
        		<a>我是 a 标签</a>
        		<p className="title">我是 .title 标签</p>
        	</div>
        );
    }
}
```

### 更换之后

```jsx
import React, { PureComponent } from 'react';
import styleNames form './postcss/json/index.json';

export default class Demo extends PureComponent {
    render() {
        return (
        	<div className={styleNames['content-wrap']}>
        		<a>我是 a 标签</a>
        		<p className={styleNames.title}>我是 .title 标签</p>
        	</div>
        );
    }
}
```

这样的话，css 的模块化就可以生效了。
