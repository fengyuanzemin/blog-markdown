---
title: babel 升级 7（译）
tags: [翻译]
date: 2019-01-26
---



原文地址：[https://babeljs.io/docs/en/v7-migration](https://babeljs.io/docs/en/v7-migration)

-- Translated by youdao

当用户升级到Babel 7时，请参阅此文档。

<!--more-->

因为并不是每个中断更改都会影响到每个项目，所以我们已经根据升级时中断更改测试的可能性对这些部分进行了排序。

## babel 

> 已删除对 Node.js 0.10, 0.12, 4, 5 的支持。

我们强烈建议您使用 Node 的新版本（LTS v8）。因为之前的版本没有维护。有关更多信息，请参见 [nodejs/LTS](https://github.com/nodejs/LTS)。

这仅仅意味着 Babel 本身不会在 Node 的旧版本上运行。它仍然可以输出运行在旧节点版本上的代码。

## 配置查找变化

更多信息，请阅读我们的 [6.x vs 7.x comparison](https://babeljs.io/docs/en/config-files#6x-vs-7x-babelrc-loading)。

Babel以前在处理 `node_modules`、`symlinks` 和 `monorepos` 时遇到过问题。我们对此做了一些更改: Babel将停止查找 `package.json` 边界，而不是查找链。对于monorepo，我们添加了一个新的 `babel.config.js` 。将我们的配置集中在所有包上的js文件(或者您可以为每个包做一个配置)。在 7.1 中，我们引入了一个 `rootMode` 选项，以便在必要时进行进一步查找。

## [废弃 Yearly Preset](https://babeljs.io/blog/2017/12/27/nearing-the-7.0-release.html#deprecated-yearly-presets-eg-babel-preset-es20xx)

`preset-env` 已经发布一年多了，它完全取代了我们之前提出的一些预置。

* babel-preset-es2015
* babel-preset-es2016
* babel-preset-es2017
* babel-preset-latest
* 以上的组合。

这些都应该用 `preset-env` 代替。

## [废弃 Stage Preset](https://babeljs.io/blog/2018/07/27/removing-babels-stage-presets)

我们正在删除 stage preset，以支持明确提案的使用。可以查看 [stage-0 README](https://github.com/babel/babel/tree/master/packages/babel-preset-stage-0#babelpreset-stage-0) 了解更多迁移步骤。

要自动做到这一点，您可以运行 [npx babel-upgrade](https://github.com/babel/babel-upgrade)

## [移除 @babel/polyfill 里面的提案性 polyfill](https://github.com/babel/babel/issues/8416)

基于类似的想法，我们已经从 @babel/polyfill 中删除了提案性 polyfill 。

目前@babel/polyfill主要是core-js v2的别名。[Source](https://github.com/babel/babel/blob/master/packages/babel-polyfill/src/index.js)

以前只有2个导入:

```js
import "core-js/shim"; // included < Stage 4 proposals
import "regenerator-runtime/runtime";
```

如果您想使用提案，则需要独立导入这些提案。您应该直接从 [core-js](https://github.com/zloirock/core-js/tree/v2#usage) 包或 npm 上的另一个包导入它们。

例子：

```js
import "core-js/fn/array/flatMap";
```

下面是core-js v2中 stage < 3  的 提案 polyfill 列表。

```js
// core-js v2

// Stage 3
import "core-js/fn/string/trim-left";
import "core-js/fn/string/trim-right";
import "core-js/fn/string/match-all";
import "core-js/fn/array/flat-map";
import "core-js/fn/array/flatten"; // RENAMED
import "core-js/fn/global";

// Stage 1
import "core-js/fn/symbol/observable";
import "core-js/fn/promise/try";
import "core-js/fn/observable";

// Stage 1 Math Extensions
import "core-js/fn/math/clamp";
import "core-js/fn/math/deg-per-rad";
import "core-js/fn/math/degrees";
import "core-js/fn/math/fscale";
import "core-js/fn/math/iaddh";
import "core-js/fn/math/isubh";
import "core-js/fn/math/imulh";
import "core-js/fn/math/rad-per-deg";
import "core-js/fn/math/radians";
import "core-js/fn/math/scale";
import "core-js/fn/math/umulh";
import "core-js/fn/math/signbit";

// Stage 1 "of and from on collection constructors"
import "core-js/fn/map/of";
import "core-js/fn/set/of";
import "core-js/fn/weak-map/of";
import "core-js/fn/weak-set/of";
import "core-js/fn/map/from";
import "core-js/fn/set/from";
import "core-js/fn/weak-map/from";
import "core-js/fn/weak-set/from";

// Stage 0
import "core-js/fn/string/at";

// Nonstandard
import "core-js/fn/object/define-getter";
import "core-js/fn/object/define-setter";
import "core-js/fn/object/lookup-getter";
import "core-js/fn/object/lookup-setter";
// import "core-js/fn/map/to-json"; // Not available standalone
// import "core-js/fn/set/to-json"; // Not available standalone

import "core-js/fn/system/global";
import "core-js/fn/error/is-error";
import "core-js/fn/asap";

// Decorator metadata? Not sure of stage/proposal
import "core-js/fn/reflect/define-metadata";
import "core-js/fn/reflect/delete-metadata";
import "core-js/fn/reflect/get-metadata";
import "core-js/fn/reflect/get-metadata-keys";
import "core-js/fn/reflect/get-own-metadata";
import "core-js/fn/reflect/get-own-metadata-keys";
import "core-js/fn/reflect/has-metadata";
import "core-js/fn/reflect/has-own-metadata";
import "core-js/fn/reflect/metadata";
```

## [版本/依赖关系](https://babeljs.io/blog/2017/12/27/nearing-the-7.0-release.html#peer-dependencies-integrations)

大多数插件/顶级包现在都有一个 `@babel/core` 的 `peerDepenency` 配置。

## 包名更改

* `babylon` 改名为 `@babel/parser`

您仍然可以在配置中使用包名的简写版本(删除 `preset-` 或 `plugin-`)，但是为了清晰起见，我选择使用整个包名(也许我们应该删除它，因为它不会节省那么多输入)。

```diff
{
-  "presets": ["@babel/preset-react"],
+  "presets": ["@babel/react"], // this is equivalent
-  "plugins": ["@babel/transform-runtime"],
+  "plugins": ["@babel/plugin-transform-runtime"], // same
}
```

### 限定范围的包

最重要的更改是最终将所有包切换到限定范围的包([monorepo](https://github.com/babel/babel/tree/master/packages)中的文件夹名称没有更改，但是改了它在 package.json 里面的名字)。

这意味着将不再存在 意外/故意 名称占用，与社区插件的明显分离以及更简单的命名约定。 

您的依赖关系将需要修改如下:

`babel-cli` - > `@babel/cli`。对我们来说，我们基本上是从用 `@babel/` 替换 `babel-` 开始的。

#### 在配置中使用

您仍然可以使用指定 preset 或 plugin 的简写方式。但是，由于切换到限定范围的包，您仍然必须指定`@babel/`，就像您有自己的 preset 要添加到配置中一样。

```js
module.exports = {
  presets: ["@babel/env"], // "@babel/preset-env"
  plugins: ["@babel/transform-arrow-functions"], // same as "@babel/plugin-transform-arrow-functions"
};
```

### [为 TC39提案 切换为 `-proposal-`](https://babeljs.io/blog/2017/12/27/nearing-the-7.0-release.html#renames-proposal)

这意味着任何不在年度发行版(ES2015, ES2016等等)中的插件都应该改名为-proposal。这样我们就可以更好地表示提案不是用正式的 JavaScript 编写的。

例子： 

* `@babel/plugin-transform-function-bind` 变成 `@babel/plugin-proposal-function-bind` (Stage 0)
* `@babel/plugin-transform-class-properties` 变成 `@babel/plugin-proposal-class-properties` (Stage 3)

这个同样也意味着当一个提案移动到 Stage 4 后，我们应该改这个包的名字。

### 从包名里面移除年份

一些插件的名称中有`-es3`或`-es2015`，但这些都是不必要的。

`@babel/plugin-transform-es2015-classes` 变成 `@babel/plugin-transform-classes`

## CommonJS 里的 `"use strict"` 和 `this`

Babel 6 对 ES6 模块的转换不分皂白地在它被告知要处理的任何文件上运行，从不考虑文件中是否包含ES6导入/导出。它有重写文件范围引用的效果，将 `this` 变成了 `undefined` ，并在 Babel 处理的所有 CommonJS 模块的顶部插入 `"use strict"`。

```js
// input.js
this;
```

```js
// output.js v6
"use strict"; // assumed strict modules
undefined; // changed this to undefined
```

v7 中输出 this

```js
// output.js v7
this;
```

这种行为在 Babel 7 中受到了限制，因此对于 `transform-es2015-modules-commonjs` 转换，只有在文件中包含ES6 的 import 或 export 时才会更改文件。(编辑注:https://github.com/babel/babel/issues/6242，它可能还会变)。

```js
// input2.js
import "a";
```
v6 和 v7 都会有 "use strict"

```js
// output.js v6 and v7
"use strict";
require("a");
```

如果您依赖Babel自动将`"use strict"`注入到所有的CommonJS模块中，那么您需要在Babel配置中显式地使用`transform-strict-mode`插件。

## 分开 React preset 与 Flow preset

`babel-preset-react` 始终包含 flow 插件。这给用户带来了很多问题，这些用户由于输入错误而无意中使用了 flow 语法，或者在没有使用 flow 本身进行类型检查的情况下添加了 flow 语法，从而导致错误。

当我们决定支持 `TypeScript` 时，这个问题变得更加复杂。如果您想使用React和TypeScript 预置，我们必须想办法通过文件类型或指令自动打开/关闭语法。最后，更容易将预置完全分离。

preset 使 Babel 能够解析 flow/ TypeScript(以及其他方言/语言)提供的类型，然后在编译成JavaScript时去掉它们。

```diff
{
-  "presets": ["@babel/preset-react"]
+  "presets": ["@babel/preset-react", "@babel/preset-flow"] // parse & remove flow types
+  "presets": ["@babel/preset-react", "@babel/preset-typescript"] // parse & remove typescript types
}
```

## 选项解析

Babel 的配置选项比 Babel 6 中更严格。其中逗号分隔的预置列表，例如。“预置”:“es2015, es2016”以前在技术上工作过，现在会失败，需要更改为数组。

注意，这并不适用于CLI，在CLI中`——presets es2015,es2016` 肯定仍然可以工作。

```diff
{
-  "presets": "@babel/preset-env, @babel/preset-react"
+  "presets": ["@babel/preset-env", "@babel/preset-react"]
}
```

## Plugin/Preset 的导出

为了一致，所有的 plugin/preset 现在应该导出一个函数而不是一个对象。这将帮助我们进行缓存。

## 解析基于字符串的配置值

在Babel 6中，直接传递给Babel的值(不是来自配置文件)相对于正在编译的文件被解析，这导致了很多混乱。

在Babel 7中，值是相对于加载它们的配置文件或相对于工作目录一致解析的。

对于 preset 值和 plugin 值，这一更改意味着CLI将在以下情况下表现良好

```sh
babel --presets @babel/preset-es2015 ../file.js
```

## 基于路径的 only 和 ignore 模式 

在Babel 6中，only 和 ignore 被视为通用匹配字符串，而不是 `filepath glob`。这意味着，例如 `*.foo。js` 将匹配`./**/*.foo.js`。这让大多数用户感到困惑和惊讶。

在Babel 7中，这些模式现在被视为基于路径的glob模式，可以是相对路径，也可以是绝对路径。这意味着，如果您正在使用这些模式，那么您现在可能至少需要向它们添加一个`**/`前缀，以确保您的模式在目录中进行深度匹配。

only和ignore模式仍然适用于目录，所以您也可以使用 `only: './tests'` 来只编译`tests`目录中的文件，不需要使用`**/*.js`来匹配所有嵌套文件。

## babel CLI 命令

babel 命令的 `--copy-files`参数告诉babel复制babel不知道如何处理的目录中的所有文件，它现在还将复制未通过 `only/ignore` 检查的文件，在此之前它将无声地跳过所有忽略的文件。

### `@babel/node`

Babel 6中的 `babel-node` 是 `babel-cli` 包的一部分。在Babel 7中，这个命令被分割成它自己的`@babel/node`包，因此如果您正在使用这个命令，您将希望添加这个新的依赖项。

### `@babel/runtime`, `@babel/plugin-transform-runtime`

我们已经将Babel helps 从运行时的 "polyfilling" 行为中分离出来。更多细节在[PR](https://github.com/babel/babel/pull/8266)中。

`@babel/runtime`现在只包含 helps ，如果需要 core-js，可以使用`@babel/runtime-corejs2`和 transfrom 中提供的选项。对于这两种情况，您仍然需要`@babel/plugin-transform-runtime`

#### 只有 helps

```sh
# install the runtime as a dependency
npm install @babel/runtime
# install the plugin as a devDependency
npm install @babel/plugin-transform-runtime --save-dev
```

```json
{
  "plugins": ["@babel/plugin-transform-runtime"]
}
```

#### Helpers + polyfilling from `core-js`

因此，如果您需要使用`transform-runtime`的`core-js`支持，那么现在您将传递`corejs`选项，并使用`@babel/runtime-corejs2`依赖项，而不是`@babel/runtime`。

```sh
# install the runtime as a dependency
npm install @babel/runtime-corejs2
# install the plugin as a devDependency
npm install @babel/plugin-transform-runtime --save-dev
```

```diff
{
  "plugins": [
-   ["@babel/plugin-transform-runtime"],
+   ["@babel/plugin-transform-runtime", {
+     "corejs": 2,
+   }],
  ]
}
```

### Spec Compliancy

### `@babel/plugin-proposal-object-rest-spread`

> 在对象的rest时，逗号不能尾随后面

```diff
var {
-  ...y, // trailing comma is a SyntaxError
+  ...y
} = { a: 1 };
```

> 由于Object Spread定义了新属性而Object.assign只设置了它们，因此Babel已将默认行为更改为更符合规范。

* objectSpread helper function
* extends helper function

```js
// input
z = { x, ...y };
```

```js
// v7 default behavior: ["proposal-object-rest-spread"]
function _objectSpread(target) { ... }

z = _objectSpread({
  x
}, y);
```

```js
// Old v6 behavior: ["proposal-object-rest-spread", { "loose": true }]
function _extends(target) { ... }

z = _extends({
  x
}, y);
```

```js
// Substitute for Object.assign: ["proposal-object-rest-spread", { "loose": true, "useBuiltIns": true }]
z = Object.assign(
  {
    x,
  },
  y
);
```

### `@babel/plugin-proposal-class-properties`

默认情况下，默认行为更改为以前的“spec”

```js
// input
class Bork {
  static a = "foo";
  y;
}
```


```js
// v7 default behavior: ["@babel/plugin-proposal-class-properties"]
var Bork = function Bork() {
  Object.defineProperty(this, "y", {
    enumerable: true,
    writable: true,
    value: void 0,
  });
};

Object.defineProperty(Bork, "a", {
  enumerable: true,
  writable: true,
  value: "foo",
});
```

```js
// old v6 behavior: ["@babel/plugin-proposal-class-properties", { "loose": true }]
var Bork = function Bork() {
  this.y = void 0;
};

Bork.a = "foo";
```

### `@babel/plugin-transform-export-extensions` 分成两个重命名提案

这很长时间了，但最后还是改变了。

`@babel/plugin-proposal-export-default-from`

```js
export v from "mod";
```

`@babel/plugin-proposal-export-namespace-from`

```js
export * as ns from "mod";
```

### `@babel/plugin-transform-template-literals`

> 模板字面修订已更新

请参阅[Template Literals Revision.](https://tc39.github.io/proposal-template-literal-revision/)提案。 

它导致Babel 6抛出 `Bad character escape sequence (5:6).`。

```js
tag`\unicode and \u{55}`;
```

它在 babel 7 中已修复，并产生以下内容。

```js
// default
function _taggedTemplateLiteral(strings, raw) {
  return Object.freeze(
    Object.defineProperties(strings, { raw: { value: Object.freeze(raw) } })
  );
}
var _templateObject = /*#__PURE__*/ _taggedTemplateLiteral(
  [void 0],
  ["\\unicode and \\u{55}"]
);
tag(_templateObject);
```

```js
// loose mode
function _taggedTemplateLiteralLoose(strings, raw) {
  strings.raw = raw;
  return strings;
}
var _templateObject = /*#__PURE__*/ _taggedTemplateLiteralLoose(
  [void 0],
  ["\\unicode and \\u{55}"]
);
tag(_templateObject);
```

> Default to previous "spec" mode for regular template literals

```js
// input
`foo${bar}`;
```

```js
// default v7 behavior: ["@babel/plugin-transform-template-literals"]
"foo".concat(bar);
```

```js
// old v6 behavior: ["@babel/plugin-transform-template-literals", { "loose": true }]
"foo" + bar;
```


### `@babel/plugin-proposal-decorators`

对于新的decorator建议实现，我们决定将其作为新的默认行为。这意味着要继续使用当前的装饰器语法/行为，必须将legacy选项设置为true。


```diff
 {
   "plugins": [
-    "@babel/plugin-proposal-decorators"
+    ["@babel/plugin-proposal-decorators", { "legacy": true }]
   ]
 }
```

> 注意：如果您使用 `@babel/preset-stage-0`或 `@babel/preset-stage-1`（包含此插件），则必须将`decoratorsLegacy`选项传递给它们。


### `@babel/plugin-proposal-pipeline-operator`

在不断变化的新提案中，默认情况下会出现错误，并要求每个人在事情还处于<阶段2时选择一个特定的提案。[这篇文章](https://babeljs.io/blog/2018/07/19/whats-happening-with-the-pipeline-proposal)将对此进行更多解释。


```diff
{
  "plugins": [
-   "@babel/plugin-proposal-pipeline-operator"
+   ["@babel/plugin-proposal-pipeline-operator", { "proposal": "minimal" }]
  ]
}
```

### Removed `babel-plugin-transform-class-constructor-call`


>  `babel-plugin-transform-class-constructor-call`  已经被删除 

TC39决定放弃这个提议。您可以将逻辑移动到构造函数或静态方法中。

See [/docs/plugins/transform-class-constructor-call/](https://babeljs.io/docs/plugins/transform-class-constructor-call/) for more information.

```diff
  class Point {
    constructor(x, y) {
      this.x = x;
      this.y = y;
    }

-  call constructor(x, y) {
+  static secondConstructor(x, y) {
      return new Point(x, y);
    }
  }

  let p1 = new Point(1, 2);
- let p2 = Point(3, 4);
+ let p2 = Point.secondConstructor(3, 4);
```

### `@babel/plugin-async-to-generator`


我们将 `babel-plugin-transform-async-to-module-method` 合并到常规异步插件中，只需将其作为一个选项即可。

```diff
{
  "plugins": [
-    ["@babel/transform-async-to-module-method"]
+    ["@babel/transform-async-to-generator", {
+      "module": "bluebird",
+      "method": "coroutine"
+    }]
  ]
}
```

## `babel`

>  `babel` 包被移除

这个包目前给您一个错误消息，要在v6中安装babel-cli。我想我们可以用这个名字做一些有趣的事情。

## `@babel/register`

> `babel-core/register.js` 被移除

在 babel 7 中移除 `babel-core/register` ，而使用独立的包 `@babel/register`

安装 @babel/register 作为新依赖

```sh
npm install --save-dev @babel/register
```

使用 `Mocha` 升级

```diff
- mocha --compilers js:babel-core/register
+ mocha --compilers js:@babel/register
```

`@babel/register` 现在也只能直接编译当前工作中的文件(用来修复符号链接的问题)。

`@babel/register` 选项现在被替换，而不是合并


## `@babel/generator`

> 删除 quotes 选项

如果您想对编译后的输出进行格式化，可以使用recast/ pretty - tier/escodegen/fork babel-generator。

只有在v6.18.0我们公开`parserOpts`和`generatorOpts`参数之前，这个选项才可以通过`babel-generator`显式地使用。因为在那个版本中有一个`bug`，所以没有人应该在Babel本身中使用这个选项。

> 删除 flowUseCommas 选项

现在在 Flow 对象类型里面支持两种语法： `,` 和 `;`

这个变化仅让 babel-generator 输出 `,` 而不是 `;`

## @babel/core

> 移除  `babel-core/src/api/browser.js`

`babel-browser` 在6.0中已经被删除。如果您需要在浏览器或非Node环境中使用Babel，请使用 `@babel/standalone`。

Babel将以绝对路径返回 `filename`

## `@babel/preset-env`

现在，松散模式将自动排除 `typeof-symbol` 转换(许多使用松散模式的项目都在这样做)。


