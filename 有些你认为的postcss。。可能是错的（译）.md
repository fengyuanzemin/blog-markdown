---
title: 有些你认为的PostCSS。。可能是错的（译）
date: 2017-10-20
tags: [css, postcss, 翻译]
---

原文链接  [http://julian.io/some-things-you-may-think-about-postcss-and-you-might-be-wrong/](http://julian.io/some-things-you-may-think-about-postcss-and-you-might-be-wrong/)

我最近在我不同的工作中尝试了 PostCSS - Meteor.js 、 简单的 React 、还有 Webpack 。我知道我需要尝试它，因为使用它每个人都很激动并且它不会令我失望。你将读到一些我在还没学习 PostCSS 前，对于它的一些想法。因此，这个也将适用于没使用过它的人。

<!--more-->

以下这些事可能是你现在对于 PostCSS 的想法：

1. 你可能觉得它是一种预处理器的替代品。
2. 你可能觉得它很难加到你的工作中。
3. 你可能不知道你已经在用它的插件 Autoprefixer 了。
4. 你可能觉得你用不到它。
5. 你可能会因为用它而爱上它。

我将一条条的澄清。这是我的个人主张，但是我觉得很多人应该有同样的看法。

# 你可能觉得它是一种预处理器的替代品。

当然，它不是。它是一个 Javascript 工具，在使用时，它会将会用特殊的语法读你的CSS，并且编译然后返回正常的 CSS 代码。这意味着什么？意味着你能继续用你喜欢的预处理器。并且你可以使用预处理器并不生效的部分，比如 linting 、 autoprefixing 和 CSS4 新特性。意味着你可以写你自己的逻辑，然后通过 PostCSS 插件会生成你想要的。在这个网址 [http://postcss.parts/](http://postcss.parts/) 有很多它的插件，但是最强大的是，你可以写自己的插件。这是一个伟大的部分，因为它非常的模块化。你可以只用你需要的部分。看看这个官方插件开发文档吧。

记住它不是预处理器的替代品，尽管它确实替代了。请看看著名的 PreCSS 插件包，它是一个很多 PostCSS 的集成工具，能替代你的 Sass 预处理器。

如果你习惯使用 Stylus 或者 Sass，你仍然能用它。在预处理之后你也能使用 PostCSS 插件再处理一次。

# 你可能觉得它很难加到你的工作中。

你可能在用一些打包工具，类似于 Gulp 或 Grunt 或 Webpack。如果这样，你只需安装适当的 PostCSS 插件。你将会在 Grunt 和 Gulp 的 tasks 中或者在 Webpack 的 loaders 中找到它。当然你也可以在 NodeJS 的基本 API 中使用它。

它也被很多大公司使用。在很多框架和工作流中它是无不协调的。看看使用者名单吧。（我喜欢的 Meteor.js 也在里面。你检查一下就知道了。）


它的用法和每个预处理器一样简单，取决于你现在的工作和工具栈。它非常的模块化和弹性化。你可以仅仅使用一些需要的。不必像预处理器一样安装所有的特性。

# 你可能不知道你已经在用它的插件 Autoprefixer 了。

最有趣的是，很多人在用 Autoprefixer ，但是他们并不知道实际上在用 PostCSS 。这是一个非常普遍的情况。 AutoPrefixer 已经被很多构建系统和工作流使用，比如 Grunt 和 Gulp。它只不过是另一个 PostCSS 插件，它负责获取你的 CSS ，检查它与浏览器的兼容性，并在需要的情况下为你的 CSS 声明添加特殊的前缀。以上是 PostCSS 插件功能的一个好例子。

有几个扩展了 Autoprefixer 的预处理器。最常使用的是 autoprefixer-stylus 和 less-plugin-autoprefix，它们只是在它们的场景中使用了 PostCSS 。你可以检查 package.json 文件，然后你会找到 PostCss 和 Autoprefixer 依赖。

有一些调查很有趣，人们不知道 Autoprefixer 是 PostCSS 插件，他们只是觉得它很酷。但是他们仍然不知道能用 PostCSS 和它的一些插件做什么。


# 你可能觉得你用不到它。

很多人觉得他们用不到它。他们认为用 Sass 或者 Stylus 就够了。我理解是因为平常预处理器是够用的。但是下面我将列举一些不够用的例子。

第一，来谈谈 Autoprefixer 插件。这是一个极好的工具，每个人都在用。他们不知道他们在使用 PostCSS ，这是可以的，但这是一个很好的例子，说明了 PostCSS 是多么强大，以及这些工具现在是如何被需要的。

第二， Stylelint 。它是一个提供 CSS 检查的 PostCSS 工具，并且它有很多的配置选项。你可以配置很多的规则，例如不使用 id 或者特殊的 class 名，比如正则表达式。下面是文档 [http://stylelint.io/](http://stylelint.io/)。

第三，[Lost Grid System](https://github.com/peterramsing/lost)。这是一个极有用的网格系统。作为一个 PostCSS 的插件。你能在它的文档里读到它的使用方法。[https://github.com/peterramsing/lost](https://github.com/peterramsing/lost) 这会将告诉你扩展你的 CSS 语法有多简单、有用。

最后一个例子是 CSSNext 。这是一个非常酷的工具。可以在你的 app 中使用未来 CSS4 的语法。在下面的文档中能找到这些新特性：[http://cssnext.io/features/](http://cssnext.io/features/)。

同样如果你使用 Sublime Text 或者 Atom editors ，还有一些优秀的例子，关于怎么在编辑器中使用 PostCSS 。例如 Sublime Text / Atom editor 的 PostCSS Sorting package 。这会是个非常有用的 CSS 规则分类器。

如果你只是想使用预处理器，当然可以。但是有时候你需要更多的功能。我觉得每一个开发者都至少知道 PostCSS 的基础知识。

# 你可能会因为用它而爱上它。

总结下， PostCSS 是一个基于 Javascript 的 CSS 解析器，同时它基于插件系统。这意味着你可以只使用你真正需要的插件。

我觉得我们可以停止使用预处理器，因为很多预处理器的特性已经在 PostCSS 中被扩展。当然我知道很多人超级喜欢预处理器。我是 Stylus 的超级粉丝。但是当我越来越多的使用 PostCSS 之后，我觉得我可以完全停止使用预处理器了。现在我喜欢在工作中同时使用 Stylus 和 PostCSS。这真的很酷！我不确定但是很有可能在不久的将来我会放弃 Stylus。

如果你想知道更多的 PostCSS ，下面是一些有用的链接：

* [PostCSS GitHub repo](https://github.com/postcss/postcss)
* [PostCSS official Twitter account](https://twitter.com/PostCSS)
* [PostCSS Deep Dive](http://webdesign.tutsplus.com/series/postcss-deep-dive--cms-889)
* [An Introduction to PostCSS](http://www.smashingmagazine.com/2015/12/introduction-to-postcss/)
* [PostCSS Tutorials](http://leveluptuts.com/tutorials/postcss-tutorials)
* [Kick your CSS up a notch with PostCSS | ViennaJS 2015](https://youtu.be/-_gIKdHYP3E)
* [How to use PostCSS in Meteor](http://julian.io/how-to-use-postcss-in-meteor/)
* [How to use CSS linter in Meteor](https://medium.com/@juliancwirko/how-to-use-css-linter-in-meteor-c60b2f24f969)
* [Is PostCSS a Game Changer?](http://articles.dappergentlemen.com/2015/07/24/postcss/)
* [PostCSS plugins list](http://postcss.parts/)
