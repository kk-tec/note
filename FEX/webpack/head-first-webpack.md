# 《深入浅出webpack》笔记
## 1. 入门
###     1. 前端的发展
####         1. 模块化
1. **命名空间**：如jQuery的window.$，这种方式存在的问题：
    * 命名空间冲突
    * 无法合理地管理项目的依赖和版本
    * 无法方便地控制依赖的加载顺序
2. **CommonJs**：如Node.js，通过 **require** 方法来**同步**地加载依赖的其他模块，通过 **module.exports** 导出需要暴露的接口。
    ```
    // 导入
    const moduleA = require('./moduleA');
    // 导出
    module.exports = moduleA.someFunc;
    ```
    
    **优点**：
    * 代码可复用于 Node.js 环境下并运行，例如做同构应用；
    * 通过 NPM 发布的很多第三方模块都采用了 CommonJS 规范   
   
   **缺点**：这样的代码无法直接运行在浏览器环境下，必须通过工具转换成标准的 ES5。
3. **AMD**：如requirejs，与 CommonJS 最大的不同在于它采用**异步**的方式去加载依赖的模块。 AMD 规范主要是为了解决针对浏览器环境的模块化问题。
    ```
    // 定义一个模块
    define('module', ['dep'], function(dep) {
        return exports;
    });
    // 导入和使用
    require(['module'], function(module) {
    });
    ```
    **优点**：  
    * 可在不转换代码的情况下直接在浏览器中运行；
    * 可异步加载依赖；
    * 可并行加载多个依赖；
    * 代码可运行在浏览器环境和 Node.js 环境下。
    
    **缺点**：JavaScript 运行环境没有原生支持 AMD，需要先导入实现了 AMD 的库后才能正常使用。 
4. **ES6 模块化**：ECMA 提出的 JavaScript 模块化规范，它在语言的层面上实现了模块化。浏览器厂商和 Node.js 都宣布要原生支持该规范。**它将逐渐取代 CommonJS 和 AMD 规范，成为浏览器和服务器通用的模块解决方案。**
    ```
    // 导入
    import { readFile } from 'fs';
    import React from 'react';
    // 导出
    export function hello() {};
    export default {
      // ...
    };
    ```
    **缺点**：ES6模块虽然是终极模块化方案，但它的缺点在于目前无法直接运行在大部分 JavaScript 运行环境下，必须通过工具转换成标准的 ES5 后才能正常运行。
5. **样式文件中的模块化**：如在less/scss中使用@import导入样式
#### 2. 前端框架
* **react**：引入 JSX 语法到 JavaScript 语言层面中，以更灵活地控制视图的渲染逻辑。无法直接在任何现成的 JavaScript 引擎里运行，必须经过babel转换。
* **vue**：Vue 框架把一个组件相关的 HTML 模版、JavaScript 逻辑代码、CSS 样式代码都写在一个文件里，这非常直观。
* **Angular2**：推崇采用 TypeScript 语言去开发应用，并且可以通过注解的语法描述组件的各种属性。
#### 3. 新语言
1. **ES6**：它在语言层面为 JavaScript 引入了很多新语法和 API ，使得 JavaScript 语言可以用来编写复杂的大型应用程序。例如：
    * 规范模块化；
    * Class 语法；
    * 用 let 声明代码块内有效的变量 ，用 const 声明常量；
    * 箭头函数；
    * async 函数；
    * Set 和 Map 数据结构。

    注意：不同浏览器对这些特性的支持不一致，使用了这些特性的代码可能会在部分浏览器下无法运行。为了解决兼容性问题，需要把 ES6 代码转换成 ES5 代码，Babel 是目前解决这个问题最好的工具。 **Babel 的插件机制让它可灵活配置，支持把任何新语法转换成 ES5 的写法。**
2. **TypeScript**：**是 JavaScript 的一个超集**，由 Microsoft 开发并开源，除了支持 ES6 的所有功能，还提供了**静态类型检查**。
    **优点**：将 TypeScript 用于开发**大型项目**时，其优点才能体现出来，因为大型项目由多个模块组合而成，不同模块可能又由不同人编写，在对接不同模块时**静态类型检查会在编译阶段找出可能存在的问题**。 
    **缺点**：语法相对于 JavaScript 更加啰嗦，并且无法直接运行在浏览器或 Node.js 环境下。
3. **Flow**：**也是 JavaScript 的一个超集**，它的主要特点是为 JavaScript 提供静态类型检查，和 TypeScript 相似但更灵活，**可以让你只在需要的地方加上类型检查**。
4. **SCSS**：可以让你用程序员的方式写 CSS。它是一种 **CSS 预处理器**，基本思想是用和 CSS 相似的编程语言写完后再编译成正常的 CSS 文件。
    **优点**：可以方便地管理代码，抽离公共的部分，通过逻辑写出更灵活的代码。 和 SCSS 类似的 CSS 预处理器还有 LESS 等。
### 2. 常见的构建工具及对比
**构建**就是把源代码转换成发布到线上的可执行 JavaScrip、CSS、HTML 代码，包括如下内容。

* **代码转换**：TypeScript 编译成 JavaScript、SCSS 编译成 CSS 等。
* **文件优化**：压缩 JavaScript、CSS、HTML 代码，压缩合并图片等。
* **代码分割**：提取多个页面的公共代码、提取首屏不需要执行部分的代码让其异步加载。
* **模块合并**：在采用模块化的项目里会有很多个模块和文件，需要构建功能把模块分类合并成一个文件。
* **自动刷新**：监听本地源代码的变化，自动重新构建、刷新浏览器。
* **代码校验**：在代码被提交到仓库前需要校验代码是否符合规范，以及单元测试是否通过。
* **自动发布**：更新完代码后，自动构建出线上发布代码并传输给发布系统。

构建其实是**工程化、自动化**思想在前端开发中的体现，把一系列流程用代码去实现，让代码自动化地执行这一系列复杂的流程。 
#### Npm Script 
**Npm Script 是一个任务执行者**。Npm 是在安装 Node.js 时附带的包管理器，Npm Script 则是 Npm 内置的一个功能，允许在 package.json 文件里面使用 scripts 字段定义任务：
```
{
  "scripts": {
    "dev": "node dev.js",
    "pub": "node build.js"
  }
}
```
**原理**：通过调用 Shell 去运行脚本命令
**优点**：内置，无须安装其他依赖。其缺点是功能太简单，虽然提供了 pre 和 post 两个钩子，但不能方便地管理多个任务之间的依赖。
#### Grunt
Grunt 和 Npm Script 类似，**也是一个任务执行者**。Grunt 有大量现成的插件封装了常见的任务，也能管理任务之间的依赖关系，自动化执行依赖的任务，每个任务的具体执行代码和依赖关系写在配置文件 Gruntfile.js 里，例如：
```
module.exports = function(grunt) {
  // 所有插件的配置信息
  grunt.initConfig({
    // uglify 插件的配置信息
    uglify: {
      app_task: {
        files: {
          'build/app.min.js': ['lib/index.js', 'lib/test.js']
        }
      }
    },
    // watch 插件的配置信息
    watch: {
      another: {
          files: ['lib/*.js'],
      }
    }
  });

  // 告诉 grunt 我们将使用这些插件
  grunt.loadNpmTasks('grunt-contrib-uglify');
  grunt.loadNpmTasks('grunt-contrib-watch');

  // 告诉grunt当我们在终端中启动 grunt 时需要执行哪些任务
  grunt.registerTask('dev', ['uglify','watch']);
};
```
**优点**：
* 灵活，它只负责执行你定义的任务；
* 大量的可复用插件封装好了常见的构建任务。

**缺点**：集成度不高，要写很多配置后才可以用，无法做到开箱即用。
#### Gulp
Gulp 是**一个基于流的自动化构建工具**。 除了可以管理和执行任务，还支持监听文件、读写文件。Gulp 被设计得非常简单，只通过下面5个方法就可以胜任几乎所有构建场景：
* 通过 gulp.task 注册一个任务；
* 通过 gulp.run 执行任务；
* 通过 gulp.watch 监听文件变化；
* 通过 gulp.src 读取文件；
* 通过 gulp.dest 写文件。

Gulp 的最大特点是引入了流的概念，同时提供了一系列常用的插件去处理流，流可以在插件之间传递，大致使用如下：
```
// 引入 Gulp
var gulp = require('gulp'); 
// 引入插件
var jshint = require('gulp-jshint');
var sass = require('gulp-sass');
var concat = require('gulp-concat');
var uglify = require('gulp-uglify');

// 编译 SCSS 任务
gulp.task('sass', function() {
  // 读取文件通过管道喂给插件
  gulp.src('./scss/*.scss')
    // SCSS 插件把 scss 文件编译成 CSS 文件
    .pipe(sass())
    // 输出文件
    .pipe(gulp.dest('./css'));
});

// 合并压缩 JS
gulp.task('scripts', function() {
  gulp.src('./js/*.js')
    .pipe(concat('all.js'))
    .pipe(uglify())
    .pipe(gulp.dest('./dist'));
});

// 监听文件变化
gulp.task('watch', function(){
  // 当 scss 文件被编辑时执行 SCSS 任务
  gulp.watch('./scss/*.scss', ['sass']);
  gulp.watch('./js/*.js', ['scripts']);    
});
```
**优点**：好用又不失灵活，既可以单独完成构建也可以和其它工具搭配使用。
**缺点**：和 Grunt 类似，集成度不高，要写很多配置后才可以用，无法做到开箱即用。
**可以将Gulp 看作 Grunt 的加强版。相对于 Grunt，Gulp增加了监听文件、读写文件、流式处理的功能。**
#### Webpack
Webpack 是**一个打包模块化 JavaScript 的工具，在 Webpack 里一切文件皆模块，通过 Loader 转换文件，通过 Plugin 注入钩子，最后输出由多个模块组合成的文件**。Webpack 专注于构建模块化项目。

Webpack 具有很大的灵活性，能配置如何处理文件，大致使用如下：
```
module.exports = {
  // 所有模块的入口，Webpack 从入口开始递归解析出所有依赖的模块
  entry: './app.js',
  output: {
    // 把入口所依赖的所有模块打包成一个文件 bundle.js 输出 
    filename: 'bundle.js'
  }
}
```
**优点**：
* 专注于处理模块化的项目，能做到开箱即用一步到位；
* 通过 Plugin 扩展，完整好用又不失灵活；
* 使用场景不仅限于 Web 开发；
* 社区庞大活跃，经常引入紧跟时代发展的新特性，能为大多数场景找到已有的开源扩展；
* 良好的开发体验。

**缺点**：只能用于采用模块化开发的项目。
#### Rollup
Rollup 是一个和 Webpack 很类似但专注于 ES6 的模块打包工具。 
Rollup 的亮点在于能**针对 ES6 源码进行 Tree Shaking** 以去除那些已被定义但没被使用的代码，以及 Scope Hoisting 以减小输出文件大小提升运行性能。 