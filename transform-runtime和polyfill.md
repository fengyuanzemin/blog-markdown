---
title: babel transform-runtime 和 polyfill 与 webpack
tags: [babel, webpack]
date: 2019-01-27
---

本文将会提到 transform-runtime 与 polyfill 的使用方式及区别。

<!--more-->

## polyfill 有三种引入方式

1. webpack 的 entry 直接引入
2. 代码的 entry 中 通过 `import/require` 引入，@babel/preset-env 配置 `useBuiltIns: entry`
3. @babel/preset-env 配置 `useBuiltIns: usage`

### webpack 直接引入

```js
entry: {
    index: [
      '@babel/polyfill',
      `./src/index.js`
    ]
  },
```

输出结果：

![单入口](http://img.fengyuanzemin.com/blog/%E5%8D%95%E5%85%A5%E5%8F%A3babel-polyfill.png)

代码有 242k

如果这是一个多入口的项目。就会出现代码体积都会变大。

多入口配置

```js
const path = require('path');
const glob = require('glob');

const file = glob.sync('./src/entry/**/*.js');

const fileName = file.map(item => {
  const parent=path.basename(item);
  return path.basename(parent, '.js')
})

fileName.forEach(item => {
  webpackConfig.entry[item] = [
    '@babel/polyfill',
    `./src/entry/${item}.js`
  ];
})
```

运行一下可见：

![多入口](http://img.fengyuanzemin.com/blog/%E5%A4%9A%E5%85%A5%E5%8F%A3babel-polyfill.png)

就需要考虑把 polyfill 打入 common 里面。不然每个入口都引入没有必要。


优点：

1. 大而全。包含了所有的 polyfill，不用担心

缺点：

1. 文件过大。很容易造成代码冗余。
2. 多入口项目需要配置 common chunk


### `useBuiltIns: entry`

配置 babelrc 文件

```js
module.exports = {
  presets: [
    [
      '@babel/preset-env',
      {
      	 target: {
      	 	chrome: 1,
      	 },
        useBuiltIns: 'entry',
      }
    ]
  ],
};
```

在代码的 entry 中的开头引入

```js
import '@babel/polyfill';

const a = 1;
```

它会根据 preset-env 的 target 配置生成 polyfill。

它不会解析你的代码，生成的 polyfill 是适用于这个环境的。

按照文字理解，entry 就是入口，保证所有的代码都能运行，包括已有代码和未知代码。

chrome 71：

![chrome71](http://img.fengyuanzemin.com/blog/entry%20chrome71.png)

chrome 1：

![chrome1](http://img.fengyuanzemin.com/blog/entry%20chrome1.png)

babel 出来的代码大小是可大可小的。不像 babel-polyfill 固定大小。

### `useBuiltIns: usage`

它是 babel 7 的实验性属性。

```diff
module.exports = {
  presets: [
    [
      '@babel/preset-env',
      {
      	 target: {
      	 	chrome: 1,
      	 },
-                 useBuiltIns: 'entry',
+                useBuiltIns: 'usage',
      }
    ]
  ],
};
```

同时还需要把 代码里面引入的 polyfill 删除

```diff
- import '@babel/polyfill';

const a = 1;
```


它会根据使用到的代码，配合 preset-env 配置，转译成相应的代码。

首先它会检测代码中是否引入了 babel-polyfill 。若如此，则报出一个提示。

![提示](http://img.fengyuanzemin.com/blog/babel-polyfill%E4%B8%8Eusage%E5%90%8C%E6%97%B6%E4%BD%BF%E7%94%A8%E7%9A%84%E6%8F%90%E7%A4%BA.png)

chrome 71:

![chrome 71](http://img.fengyuanzemin.com/blog/usage%20chrome71.png)

chrome 1:

![chrome 1](http://img.fengyuanzemin.com/blog/entry%20chrome1.png)

按照文字其实很好理解，usage 就是用法。它会根据代码来生成。

## transform-runtime

transform-runtime 是 babel 插件。使用方式很简单。


```js
module.exports = {
  plugins: ['@babel/plugin-transform-runtime']
}
```

根据官方文档：

>Babel uses very small helpers for common functions such as _extend. By default this will be added to every file that requires it. This duplication is sometimes unnecessary, especially when your application is spread out over multiple files.
>
>Babel 使用了非常小的 helpers 来实现诸如 _extend 等常用功能。默认情况下，它将被添加到每个通过 require 引用它的文件中。这种重复（操作）有时是不必要的，特别是当你的应用程序被拆分为多个文件时。

>This is where the @babel/plugin-transform-runtime plugin comes in: all of the helpers will reference the module @babel/runtime to avoid duplication across your compiled output. The runtime will be compiled into your build.
>
>这时则需要使用 `@babel/plugin-transform-runtime`：所有的 `helper` 都会引用模块 `@babel/runtime`，以避免编译输出的重复问题。这个运行时会被编译到你的构建版本当中。
>
>Another purpose of this transformer is to create a sandboxed environment for your code. If you use @babel/polyfill and the built-ins it provides such as Promise, Set and Map, those will pollute the global scope. While this might be ok for an app or a command line tool, it becomes a problem if your code is a library which you intend to publish for others to use or if you can't exactly control the environment in which your code will run.
>
>这个转译器的另外一个目的就是为你的代码创建一个沙盒环境。如果你使用了 `@babel/polyfill`，它提供了诸如 `Promise`，`Set` 以及 `Map` 之类的内置插件，这些将污染全局作用域。虽然这对于应用程序或命令行工具来说可能是好事，但如果你的代码打算发布为供其他人使用的库，或你无法完全控制代码运行的环境，则会成为问题。
>
>The transformer will alias these built-ins to core-js so you can use them seamlessly without having to require the polyfill.
>
>转译器将这些内置插件起了别名 core-js，这样你就可以无缝的使用它们，并且无需使用 `polyfill`。


示例

假如我有如下代码，里面使用了 rest-spread

```js
const b = {
  qw: 1,
  er: 2,
  ty: 3,
}

const {
  qw,
  ...er
} = b;

console.log(qw);

console.log(er);
```


使用 transform-runtime。可以看到代码带了 `_babel_runtime_helper` 前缀

```js
"use strict";
/* harmony import */ var _babel_runtime_helpers_objectWithoutProperties__WEBPACK_IMPORTED_MODULE_2__ = __webpack_require__(6);
/* harmony import */ var _babel_runtime_helpers_objectWithoutProperties__WEBPACK_IMPORTED_MODULE_2___default = /*#__PURE__*/__webpack_require__.n(_babel_runtime_helpers_objectWithoutProperties__WEBPACK_IMPORTED_MODULE_2__);

var b = {
  qw: 1,
  er: 2,
  ty: 3
};

var qw = b.qw,
    er = _babel_runtime_helpers_objectWithoutProperties__WEBPACK_IMPORTED_MODULE_2___default()(b, ["qw"]);

console.log(qw);
console.log(er); // async/await

```

未使用 transform-runtime

```js
"use strict";
function _objectWithoutProperties(source, excluded) { if (source == null) return {}; var target = _objectWithoutPropertiesLoose(source, excluded); var key, i; if (Object.getOwnPropertySymbols) { var sourceSymbolKeys = Object.getOwnPropertySymbols(source); for (i = 0; i < sourceSymbolKeys.length; i++) { key = sourceSymbolKeys[i]; if (excluded.indexOf(key) >= 0) continue; if (!Object.prototype.propertyIsEnumerable.call(source, key)) continue; target[key] = source[key]; } } return target; }

function _objectWithoutPropertiesLoose(source, excluded) { if (source == null) return {}; var target = {}; var sourceKeys = Object.keys(source); var key, i; for (i = 0; i < sourceKeys.length; i++) { key = sourceKeys[i]; if (excluded.indexOf(key) >= 0) continue; target[key] = source[key]; } return target; }

var b = {
  qw: 1,
  er: 2,
  ty: 3
};

var qw = b.qw,
    er = _objectWithoutProperties(b, ["qw"]);

console.log(qw);
console.log(er);  
```

## transform-runtime 与 polyfill

### 新的 API 需要 polyfill
babel 只会对新的 JavaScript 语法进行转换，而新的 API ，比如 Promise、Set、Array.prototype.includes 等。这些是在 instance、 prototype 上定义的，就需要 polyfill 了。

### global scope

上文也有提到，polyfill 会污染全局，如果你是在项目里面使用，那么用 polyfill 是没有任何问题的。

但是如果是在 工具库中使用的话，建议是使用 transform-runtime。

### 这两根本不是一个东西

使用 polyfill 是为了兼容老版本浏览器，在全局上定义新的API

而使用 transform-runtime 最开始是为了减少重复性代码和避免污染全局作用域。

### 例子

假如我有这样的代码。里面有 Set 、 includes 、 Promise

```js
// Set
const setA = new Set();
setA.add(1);
console.log(setA);

// includes
'foobar'.includes('foo')

// Promise
const promistFunc = () => {
  return new Promise(resolve=>{
    setTimeout(()=>{
      resolve(1);
    }, 1000)
  })
};
```

转译后，可见 babel 不会转译这些新API

```js
// Set
var setA = new Set();
setA.add(1);
console.log(setA); 

// includes
'foobar'.includes('foo'); 

// Promise
var promistFunc = function promistFunc() {
  return new Promise(function (resolve) {
    setTimeout(function () {
      resolve(1);
    }, 1000);
  });
};
```

如果要使用，还得引入 polyfill

polyfill 里面的 Set 的部分。

```js
/***/ }),
/* 73 */
/***/ (function(module, exports, __webpack_require__) {

"use strict";

var strong = __webpack_require__(74);
var validate = __webpack_require__(76);
var SET = 'Set';

// 23.2 Set Objects
module.exports = __webpack_require__(77)(SET, function (get) {
  return function Set() { return get(this, arguments.length > 0 ? arguments[0] : undefined); };
}, {
  // 23.2.3.1 Set.prototype.add(value)
  add: function add(value) {
    return strong.def(validate(this, SET), value = value === 0 ? 0 : value, value);
  }
}, strong);

/***/ }),
```


讲到这里就差不多了，本人水平有限。欢迎随时指正。


