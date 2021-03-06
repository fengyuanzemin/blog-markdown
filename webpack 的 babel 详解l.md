---
title: webpack 的 babel 详解
tags: [webpack, babel]
date: 2019-01-18
---

babel有很多工具，babel-cli、babel-loader、babel API 等。今天我们就讲 babel-loader。

babel 的官网有示例：

[https://babeljs.io/](https://babeljs.io/)

<!--more-->

目标就是让你用下一代的 JS。

>Use next generation JavaScript, today.

babel 只做语法层面的转译，不管能不能正常运行。


本次使用工具：

[http://tool.oschina.net/diff/](http://tool.oschina.net/diff/) 代码比对

## preset
### env

基于环境来转换。不包含 stage-X 的代码。是已经完成提案，添加入标准的代码。

```sh
yarn add @babel/preset-env -D
```

>`@babel/preset-env` 是一个智能的预置，它允许您使用最新的 `JavaScript`，而不需要对目标环境所需的语法转换(以及可选的浏览器填充)进行微观管理。这不仅使您的工作更轻松，而且JavaScript包更小!


```js
module.exports = {
    presets: [
        [
            '@babel/preset-env',
            {
                "modules": false,
                "targets": {
                    "browsers": ['last 2 versions', 'Firefox ESR', '> 1%', 'ie >= 9', 'iOS >= 8', 'Android >= 4']
                }
            }
        ]
    ]
}
```
### targets

设置目标

chrome 1 与 chrome 71 的区别

chrome 71:

![chrome 71](http://img.fengyuanzemin.com/blog/chrome%2071.png)

bundle 文件：

![bundle 文件](http://img.fengyuanzemin.com/blog/chrome%2071%20bundle.png)

chrome 1:

![chrome 1](http://img.fengyuanzemin.com/blog/chrome%201.png)

bundle 文件：

![bundle文件](http://img.fengyuanzemin.com/blog/chrome%201%20bundle.png)

大家可以看到，在不同的target下，生成的代码是不一样的。

### module
启用ES6模块语法到另一个模块类型的转换。

可以设置为 "amd" | "umd" | "systemjs" | "commonjs" | "cjs" | "auto" | false。

![amd](http://img.fengyuanzemin.com/blog/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202019-01-28%20%E4%B8%8B%E5%8D%8811.33.49.png)

设为 amd 之后，打包的文件多了 _WEBPACK_AMD_DEFINE_ARRAY

一般我们不设置或者设置为 false （使用 tree shaking）

### useBuiltIns
此配置是preset-env 用来设置 polyfill 的。

"usage" | "entry" | false

#### usage
此为 babel 7 的试验性属性。

使用它会根据 preset-env的 targets 配置 和 项目里面的代码 加入相应的 polyfill。不多余

![chrome 71](http://img.fengyuanzemin.com/blog/usage%20chrome71.png)

而且会检测代码里面已经有 @babel/polyfill 时会提醒你代码冗余。

![提示](http://img.fengyuanzemin.com/blog/babel-polyfill%E4%B8%8Eusage%E5%90%8C%E6%97%B6%E4%BD%BF%E7%94%A8%E7%9A%84%E6%8F%90%E7%A4%BA.png)


#### entry
使用 entry时，需要同时在在入口文件添加 import '@babel/polyfill'。

它会根据你的 targers 特性引入相应的 polyfill，不管你有没有使用

![chrome71](http://img.fengyuanzemin.com/blog/entry%20chrome71.png)

这会根据 browserslist 目标导入所有 polyfill，这样你就不用再担心依赖的 polyfill 问题了，但是因为包含了一些没有用到的 polyfill 所以最终的包大小可能会增加。


#### false
默认是 false。这适合在 库里使用。polyfill 让项目去加。

### stage-x（babel 7 废弃）
它的目的是允许用户使用试验性的代码。

与 preset-env 的区别：

env 是已经确定的语法并写入规范的语法，stage-x 还没写入规范。

分类
TC39 将提案分为以下几个阶段:

* Stage 0 - 稻草人: 只是一个想法，可能是 babel 插件。
* Stage 1 - 提案: 初步尝试。
* Stage 2 - 初稿: 完成初步规范。
* Stage 3 - 候选: 完成规范和浏览器初步实现。
* Stage 4 - 完成: 将被添加到下一年度发布。没有这个 babel preset

欲了解更多信息，请务必查看当前 TC39 提案及其文档流程.

然而在 babel 7 之后，它被废弃了。当我配置好运行之后就会报这个错。

![stage-0报错](http://img.fengyuanzemin.com/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202019-01-28%20%E4%B8%8B%E5%8D%8811.40.26.png)

废弃的目的其实是想让配置更清晰。

如果还想要以前的配置，就得配下面的：

```JSON
{
  "plugins": [
    // Stage 0
    "@babel/plugin-proposal-function-bind",
​
    // Stage 1
    "@babel/plugin-proposal-export-default-from",
    "@babel/plugin-proposal-logical-assignment-operators",
    ["@babel/plugin-proposal-optional-chaining", { "loose": false }],
    ["@babel/plugin-proposal-pipeline-operator", { "proposal": "minimal" }],
    ["@babel/plugin-proposal-nullish-coalescing-operator", { "loose": false }],
    "@babel/plugin-proposal-do-expressions",
​
    // Stage 2
    ["@babel/plugin-proposal-decorators", { "legacy": true }],
    "@babel/plugin-proposal-function-sent",
    "@babel/plugin-proposal-export-namespace-from",
    "@babel/plugin-proposal-numeric-separator",
    "@babel/plugin-proposal-throw-expressions",
​
    // Stage 3
    "@babel/plugin-syntax-dynamic-import",
    "@babel/plugin-syntax-import-meta",
    ["@babel/plugin-proposal-class-properties", { "loose": false }],
    "@babel/plugin-proposal-json-strings"
  ]
}
```

[https://github.com/babel/babel/blob/master/packages/babel-preset-stage-0/README.md](https://github.com/babel/babel/blob/master/packages/babel-preset-stage-0/README.md)

如果想用里面的属性，又忘记配置了，babel 会提醒你的



### react
在 preset 里面加入  `@babel/preset-react`

原代码：

```js
export default class A extends React.Component {
    constructor() {
        super();
        this.funcB = this.funcB.bind(this);
    }
    funcB () {
        console.log(2)
    }
    render() {
        return <div className="a-react">React示例</div>
    }
}
```

转译后：

```js
var A =
/*#__PURE__*/
function (_React$Component) {
  _inherits(A, _React$Component);
​
  function A() {
    var _this;
​
    _classCallCheck(this, A);
​
    _this = _possibleConstructorReturn(this, _getPrototypeOf(A).call(this));
    _this.funcB = _this.funcB.bind(_assertThisInitialized(_assertThisInitialized(_this)));
​
    return _this;
  }
​
  _createClass(A, [{
    key: "funcB",
    value: function funcB() {
      console.log(2);
    }
  }, {
    key: "render",
    value: function render() {
      return react__WEBPACK_IMPORTED_MODULE_0___default.a.createElement("div", {
        className: "a-react"
      }, "React\u793A\u4F8B");
    }
  }]);
​
  return A;
}(react__WEBPACK_IMPORTED_MODULE_0___default.a.Component);
```
​
### typescript

在preset里面加入 @babel/preset-typescript

原代码

```js
const a: number = 1;
let b: number = 2;
let c: number
​
const array: Number[] = [a, b, c];
​
// class
class A {
    private obj: any;
    constructor(obj) {
        this.obj = obj;
    }
}
```

转译后代码

```js
const a = 1;
let b = 2;
let c;
const array = [a, b, c];
​
// class
class A {
  constructor(obj) {
    this.obj = obj;
  }
}
```

`@babel/preset-typescript` 只能将 ts 转成 js，转换 ES5 的部分还是需要 env

而且 `@babel/preset-typescript`  必须先执行，不然就报错了。



## plugin

### transform-runtime

```sh
# 安装在 devDependencies
yarn add @babel/plugin-transform-runtime -D
# 依赖 @babel/runtime。且其为 dependencies
yarn add @babel/runtime
```

配置

```js
module.exports = {
  plugins: [
    ['@babel/plugin-transform-runtime']
  ],
}
```

#### transform-runtime 与 babel-polyfill

见 [babel transform-runtime 和 polyfill](http://blog.fengyuanzemin.com/2019/01/27/transform-runtime%E5%92%8Cpolyfill/)

## polyfill

上文有提，babel 只会对新语法进行转译，而新的API是不会处理。

这个时候就需要 polyfill 了。

Babel 包含一个可自定义 regenerator runtime 和 core-js 的 polyfill 。

这意味着你可以使用新的内置对象比如 Promise 或者 WeakMap, 静态方法比如 Array.from 或者 Object.assign, 实例方法比如 Array.prototype.includes 和生成器函数（提供给你使用 regenerator 插件）。

为了达到这一点， polyfill 添加到了全局范围，就像原生类型比如 String 一样。


## presets 与 plugins 的执行顺序

* Plugin 会运行在 Preset 之前。
* Plugin 会从第一个开始顺序执行。
* Preset 的顺序则刚好相反(从最后一个逆序执行)。

## 最后的配置文件

```js
module.exports = {
  presets: [
    [
      '@babel/preset-env',
      {
        modules: false,
        targets: {
          "browsers": ['last 2 versions', 'Firefox ESR', '> 1%', 'ie >= 9', 'iOS >= 8', 'Android >= 4']
        },
        useBuiltIns: 'usage'
      }
    ],
  ],
  plugins: [
    ['@babel/plugin-transform-runtime']
  ],
}
```

这个配置

1. transform-runtime 控制重复引用，
2. polyfill 处理新的API
3. 对于 treeshaking 有相应支持.
4. 有 targets 目标浏览器

如果有写 react 或者 ts 则需要自行加上相应的 preset

