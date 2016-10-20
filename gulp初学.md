---
title: gulp初学篇
date: 2016-03-14 17:48:09
tags: gulp
---

当然首先要有node环境

# 创建package.json
没有package.json，gulp的组件是安装不了的。

```sh
npm init
```

<!--more-->

一直回车，如果你懒得填一些东西

# 安装gulp

```sh
npm i --save-dev gulp
```

# 安装gulp组件

	
### [gulp-clean-css](https://www.npmjs.com/package/gulp-clean-css/) CSS压缩并清除

```sh
npm i --save-dev gulp-clean-css 
```
	
### [gulp-sass](https://www.npmjs.com/package/gulp-sass/) sass编译

```sh
npm i --save-dev gulp-sass 
```
	
### [gulp-eslint ](https://www.npmjs.com/package/gulp-eslint/) JS代码检查 

```sh
npm i --save-dev gulp-eslint
```
	
### [gulp-autoprefixer](https://www.npmjs.com/package/gulp-autoprefixer/) CSS自动添加前缀

```sh
npm i --save-dev gulp-autoprefixer
```

### [gulp-uglify](https://www.npmjs.com/package/gulp-uglify/) 丑化文件，有压缩的功能

```sh
npm i --save-dev gulp-uglify
```

	
### [gulp-rename](https://www.npmjs.com/package/gulp-rename/) 重命名

```sh
npm i  --save-dev gulp-rename
```
	
### [gulp-babel](https://www.npmjs.com/package/gulp-babel) 使用ES6


```sh
npm i--save-dev gulp-babel babel-preset-es2015
```

### 删除文件工具del安装

```sh
npm i --save-dev gulp del
```
	

# 文件夹目录

	project(项目名称)
	|- .git   通过git管理项目会生成这个文件夹
	|- node_modules  组件目录
	|- dist 发布环境
    		|- css   样式文件(style.css style.min.css)
    		|- img   图片文件(压缩图片)
    		|- js    JS文件(main.js main.min.js)
    		|- index.html   静态文件(压缩html)
	|- src 生产环境
		|- css 样式文件
    			|- sass SASS文件
    				|- component 组件
    		|- img  图片文件
    		|- js   JS文件
    		|- index.html 静态文件
	|- .eslintrc     eslint配置文件
	|- gulpfile.js   gulp任务文件
	|- README.md
	|- package.json

# gulpfile.js
然后gulp需要有一个gulpfile.js的文件，一般在里面定义各种方法，去压缩、编译代码

```js
const gulp = require('gulp');
const cleanCSS = require("gulp-clean-css"),
    autoprefixer = require('gulp-autoprefixer'),
    rename = require('gulp-rename'),
    notify = require('gulp-notify'),
    del = require('del'),
    uglify = require('gulp-uglify'),
    eslint = require('gulp-eslint'),
    sass = require('gulp-sass'),
    babel = require('gulp-babel');

// 清理CSS
gulp.task('clean:css', function() {
    del(['dist/css/**']);
});

// sass
gulp.task('sass', function() {
    return gulp.src('src/css/sass/*.scss')
        .pipe(sass().on('error', sass.logError))
        .pipe(gulp.dest('src/css'));
});

// 将css/core的文件添加CSS前缀，增加.min，并压缩
gulp.task('mincss', ['clean:css', 'sass'], function() {
    return gulp.src('src/css/*.css')
        .pipe(autoprefixer('last 2 version', 'safari 5', 'ie 9', 'opera 12.1', 'ios 6', 'android 4'))
        // .pipe(rename({ suffix: '.min' }))
        .pipe(cleanCSS({ compatibility: 'ie8' }))
        .pipe(gulp.dest('dist/css/'));
});

// 对CSS总处理
gulp.task('css', ['mincss']);

// 清理JS
gulp.task('clean:js', function() {
    del(['dist/js/**']);
});

// 对ES6的处理
gulp.task('es6js', function() {
    return gulp.src('src/js/es6/*.js')
        .pipe(eslint())
        // .pipe(eslint.format())
        // .pipe(eslint.failAfterError())
        .pipe(babel({ presets: ['es2015'] }))
        .pipe(gulp.dest('src/js/es5/'));
});

// JS eslint、压缩、丑化
gulp.task('js', ['clean:js', 'es6js'], function() {
    return gulp.src('src/js/es5/*.js')
        .pipe(eslint())
        .pipe(eslint.format())
        // .pipe(eslint.failAfterError())
        .pipe(uglify())
        // .pipe(rename({ suffix: '.min' }))
        .pipe(gulp.dest('dist/js/'));
});

// 清理全部
gulp.task('clean:dist', function() {
    del(['css/dist/**']);
    del(['js/dist/**']);
});

// 默认总方法
gulp.task('default', ['css', 'js']);
```
	
