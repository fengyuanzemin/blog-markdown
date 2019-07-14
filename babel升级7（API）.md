---
title: babel 升级 7（API）（译）
tags: [翻译]
date: 2019-07-14
---

## 所有的 babel 包

### NodeJS Support

不再支持 NodeJS 0.10 和 0.12，因为它们都没有维护

<!--more-->

### Export Change

取消 Babel 包里面对于 `add-module-exports` 插件的使用。为了避免我们的 exports 有重大更新，这个必须早点改变。

如果你在库里导入 babel 包，你可能需要使用带 `.default` 的 `require` 方式，而不是使用 `import`。

##`@babel/core`

为提高性能，`ast` 默认配置改为 `false`，（大多数工具都不会用它）

删除公开但是没有文档的 `Pipeline` 类，最好直接使用 `@babel/core` 暴露的转换方法。

`babel.util.*` 帮助工具被移除， `util.EXTENSIONS` 被移动到 `babel.DEFAULT_EXTENSIONS`。

调用 `babel.transfrom` 或者其他转换函数可能会返回 `null`，如果文件匹配上了 `ignore` 模式，或者在匹配 `only` 模式的时候失败。

state.file.opts 暴露出来的 `opts.basename` 参数已经被移除，如果你需要它，最好是构建时使用 opts.filename

移除 resolveModuleSource。我们推荐使用 babel-plugin-module-resolver@3 的 resolvePath 选项。

移除 babel.analyse. 因为它只是 babel.transform 的别名。

移除 path.mark() 因为我们不使用它，而且它可以在你的插件中被扩展

移除 babel.metadata ，生成插件时，metadata 永远被包含在输出里面。

移除 path.hub.file.addImport，你可以使用 @babel/helper-module-imports 模块

```diff
+  import { addDefault } from "@babel/helper-module-imports";
function importModule(pkgStore, name, path) {
-  return path.hub.file.addImport(resolvePath(pkgStore, name, path), 'default', name);
+  return addDefault(path, resolvePath(pkgStore, name, path), { nameHint: name });
}
```

### 配置改变

我们对配置查找的工作方式做了一些主要的改变:

> 默认情况下，当搜索到 `.babelrc` 时，会停在第一个匹配到的 `package.json` 那里，不会继续向上查找。

对于任何特定的文件， `Babel V6` 都会一直查找目录层次结构，直到找到配置文件为止。这意味着你的项目可能会中断，因为它使用了位于项目根目录的配置文件，就像在主目录一样。

> 按照 Webpack 那样添加对于 `babel.config.js` 的支持。

因为这会破坏 `monorepo` 的工作方式(包括 Babel 本身)，所以我们引入了一个新的配置文件，它基本上消除了配置的层次结构。

包含一个跟路径，默认为当前工作目录，以便查找文件。它同时也没有相对地加载，所以它将正确的处理符号链接。在这之前，你可能在 `webpack` 里面写死了路径。

检查 babel.config.js 的配置文档以获得更多的信息。![https://babeljs.io/docs/en/config-files#project-wide-configuration](https://babeljs.io/docs/en/config-files#project-wide-configuration)

这个文件结合了新的 `overrides` 属性 和 `env` ，让您有一个简单的配置文件。它可以工作于一个项目中的所有文件，而不是每个文件夹中的多个配置文件。

默认情况下，我们还排除 node_modules，只查看根目录，除非你设置  `.babelrcRoots` 选项，例如：`"babelrcRoots": [".", "node_modules/pkgA"]`

## 判断 Babel 版本

插件可以检查你加载的 Babel 主版本，这个 API 会暴露一个名为 assertVersion 的方法。你可以使用它来判断。

这个申明的 helper 被用来与 V6 保持向后比较。

```js
import { declare } from "@babel/helper-plugin-utils";

export default declare(api => {
  api.assertVersion(7);
  // ...
});
```

## Babel plugins/presets 

它当前有三个参数，第一个参数 babel 对象，接下来是 plugin/preset 设置 和 文件夹名

```js
module.exports = function(api, options, dirname) {};
```

## babel-parser (被称为 Babylon)

> 移除 * 插件选项

这是在v6.14.1(2016年11月17日)中首次添加的，所以不太可能有人使用它。

删除了这个全面性选项;相反，您应该明确地决定要激活哪些插件。

我们认为这对于工具来说是一个好主意，这样他们就不必不断地更新配置，但这也意味着我们不能轻易地做出破坏性的改变。

之前：

```js
babelParser.parse(code, {
  plugins: ["*"],
});
```

之后：

```js
babelParser.parse(code, {
  plugins: [
    "asyncGenerators",
    "classProperties",
    "decorators",
    "doExpressions",
    "dynamicImport",
    "exportExtensions",
    "flow",
    "functionBind",
    "functionSent",
    "jsx",
    "objectRestSpread",
  ],
});
```

查看 Babylon 插件配置 

> decorators 重命名为 decorators-legacy

它被重命名为与 @babel/plugin-proposal-decorator 的遗留选项对齐。一个新的`decorators ` 插件已经实现了新的`decorators` 提案。

这两个版本的提案有不同的语法，所以在 Babel 实现新的语义之前，强烈建议使用 `decorator-legacy`。
 
> 删除 classConstructorCall 插件

## @babel/traverse

> 移除对 flow 绑定的支持

这个改变的原因是， `declare` 不会引入新的本地绑定，但是它代表了一个全局绑定。

> getFunctionParent 不再返回 Program ，请使用 getProgramParent 代替

一个名为 getFunctionParent 的函数也返回 Program 是没有意义的，所以它被删除了。

要获得相同的行为，您需要进行如下更改：

```diff
- path.scope.getFunctionParent()
+ path.scope.getFunctionParent() || path.scope.getProgramParent()
```

> 路径替换/删除 api 现在返回一个新路径数组

例如，使用 `Path#insertBefore` 或 `Path#replaceWith` 现在将始终返回新插入/替换路径的数组。

```js
const node = t.nullLiteral();
const [replaced] = path.replaceWith(node);
replace.node === node; // => true
```

这个非常有用，当插入几个节点进更高级别的范围时。因为你可以立即在 节点的新路径上调用 Path API。

```js
const parent = path.findParent(() => /* some selection criteria */);
const helperPaths = path.unshiftContainer("body", helpers);
// helperPaths can now be referenced, manipulated, etc.
```

## AST 改变

### 增加 InterpreterDirective 节点

Babylon 已经解析了 “shebagns”（#!env node）。但是把它放进了 Program 节点的注释里面。现在我们仅仅是为它创建了一个实际节点。

添加一个 interpreter 在 Program 节点

```js
extend interface Program {
  interpreter: InterpreterDirective;
}
```

添加一个 InterpreterDirective 节点

```js
interface InterpreterDirective <: Node {
    type: "InterpreterDirective";
    value: string;
}
```

### JSX* and TS* node builders (from @babel/types 包) 重新命名

jsx 和 ts 转成小写。

```diff
- t.jSXIdentifier()
+ t.jsxIdentifier()
```

通常，我们有对于 TypeAnnotation（Flow） 和 TSTypeAnnotation（TypeScript） 不同的节点类型。所以对于共享节点，Typescript 有 TS 前缀。

### ArrowFunctionExpression 移除 .expression 字段

expression 字段被移除，用来消除两个不同真实来源。并需要插件手动保持他们同步。你现在可以简单的检查函数体是否是 `BlockStatement`

```diff
  return {
    visitor: {
      ArrowFunctionExpression({ node }) {
-       if (node.expression) {
+       if (node.body.type !== "BlockStatement") {
          // () => foo;
        }
      }
    }
  };
```

### Tokens 移除

在之前的版本，token 一直在 AST 的顶层。在最新的 @babel/parser 中，我们移除了这个行为，并且为了提高解析器的性能，默认禁止了它。

babel 里面所有的用法都被移除，并且 @babel/generator 不再使用 tokens 作为输出。

如果你的babel 插件现在在使用 tokens，评估下它是否必须。并且如果可能的话，尝试移除它的使用。如果你的插件真的依赖 tokens，你可以重新激活它，但是请仅考虑在没有其他方法的情况下。因为这个会伤害用户性能。

如果要激活，你需要设置 babylon 的 tokens 选项为true。你可以在你的插件里直接这么做。

```js
export default function() {
  return {
    manipulateOptions(opts, parserOpts) {
      parserOpts.tokens = true;
    },
    ...
  };
}
```

### 重命名

下面的节点被重命名了：

|Name 6.x|Name 7.x|Example|
|---|---|---|
|ExistentialTypeParam|ExistsTypeAnnotation|type A = B<*>;|
|NumbericLiteralTypeAnnotation|NumberLiteralTypeAnnotation|type T = 0;|


除了ast节点之外，@babel/types中所有对应的函数都已重命名。

```diff
 import * as t from "@babel/types";

 return {
-  ExistentialTypeParam(path) {
-    const parent = path.findParent((path) => path.isExistentialTypeParam());
-    t.isExistentialTypeParam(parent);
+  ExistsTypeAnnotation(path) {
+    const parent = path.findParent((path) => path.isExistsTypeAnnotation());
+    t.isExistsTypeAnnotation(parent);

-    return t.existentialTypeParam();
+    return t.existsTypeAnnotation();
   },
-  NumericLiteralTypeAnnotation(path) {
-    const parent = path.findParent((path) => path.isNumericLiteralTypeAnnotation());
-    t.isNumericLiteralTypeAnnotation(parent);
+  NumberLiteralTypeAnnotation(path) {
+    const parent = path.findParent((path) => path.isNumberLiteralTypeAnnotation());
+    t.isNumberLiteralTypeAnnotation(parent);

-    return t.numericLiteralTypeAnnotation();
+    return t.numberLiteralTypeAnnotation();
   }
 };
```

### 替换

在以下 AST-Nodes ，字段 variance 的值被改变了。它从一个简单的字符串变成了自有的 AST-Node 类型，叫作 Variance。

下面字段当且仅当开启 flow 的babylon插件时才有效。

* ObjectProperty
* ObjectMethod
* AssignmentProperty
* ClassMethod
* ClassProperty
* Property

新 Variance 节点的类型如下：

```js
type VarianceNode = {
  type: "Variance",
  kind: "plus" | "minus",
};
```

```diff
 return {
   Property({ node }) {
-    if (node.variance === "plus") {
+    if (node.variance.kind === "plus") {
       ...
-    } else if (node.variance === "minus") {
+    } else if (node.variance.kind === "minus") {
       ...
     }
   }
 };
```

### 位置更改

ObjectTypeIndexer 的位置信息不再包含分号。这个操作是为了与 flow-parser 对其，两个有相同的位置信息。


```js
var a: { [a: number]: string };
```

```diff
 {
   "type": "ObjectTypeIndexer",
   "start": 9,
-  "end": 29,
+  "end": 28,
   "loc": {
     "start": {
       "line": 1,
       "column": 9,
     },
     "end": {
       "line": 1,
-      "column": 29
+      "column": 28
     }
   }
 }
```

### 移除

#### ForAwaitStatement

AST-Node ForAwaitStatement 已经被移除，并且被 ForOfStatement 的 await 字段代替。

```diff
 interface ForOfStatement <: ForInStatement {
   type: "ForOfStatement";
+  await: boolean;
 }
```

```diff
 return {
-  ForAwaitStatement(path) {
-    ...
+  ForOfStatement(path) {
+    if (path.node.await) {
+      ...
+    }
   }
 };
```

#### RestProperty & SpreadProperty

两个 AST-Node RestProperty 与 SpreadProperty 已经被移除。代替的是重新使用 RestElement 与 SpreadElement。

```diff
 return {
   SpreadElement(path) {
-    ...
-  },
-  SpreadProperty(path) {
-    ...
+    if (path.parentPath.isObjectExpression()) {
+      ...
+    } else if (path.parentPath.isArrayExpression()) {
+      ...
+    }
   },
   RestElement(path) {
-    ...
-  },
-  RestProperty(path) {
-    ...
+    if (path.parentPath.isObjectPattern()) {
+      ...
+    } else if (path.parentPath.isArrayPattern()) {
+      ...
+    }
   }
 };
```