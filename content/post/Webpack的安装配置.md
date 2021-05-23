---
title: Webpack的安装与配置
date: 2019-06-21 23:22:15
tags: [JavaScript,Webpack]
categories: [编程,前端,Webpack]
---

> webpack 是一个现代 JavaScript 应用程序的静态模块打包器(module bundler)。当 webpack 处理应用程序时，它会递归地构建一个依赖关系图(dependency graph)，其中包含应用程序需要的每个模块，然后将所有这些模块打包成一个或多个 bundle。

# 安装Webpack

```bash
:: 初始化
yarn init

:: 安装webpack 和 webpack-cli
:: -D表示是开发依赖, 上线时不需要
yarn add webpack webpack-cli -D
```

# 配置Webpack

> 从 Webpack v4.0.0 开始，可以不用引入配置文件。然而，webpack 仍然还是高度可配置的。  
> Webpack 默认配置文件名为 `webpack.config.js`或`webpackfile.js`。  
> 也可以在启动时使用 --config 参数指定配置文件名称

```javascript
// 引入path模块 (自带, 无需安装)
const path = require('path')

// 导出配置模块
module.exports = {
  // 打包模式 默认有两种 production|development
  // production: 生产模式
  // development: 开发模式
  mode: 'development',
  // 入口文件
  entry: './src/index.js',
  // 输出
  output: {
    // 打包后的文件名
    filename: 'bundle.js',
    // 打包后的路径 (要求必须是绝对路径)
    path: path.resolve(__dirname/*指当前路径*/, 'dist'/*指打包到的文件夹名称*/)
  },
  // 开发服务器的配置
  devServer: {
    // 端口
    port: 3000,
    // 打包进度
    progress: true,
    // 打包后文件的放置位置
    contentBase: './dev',
    // 启用压缩
    compress: true
  }
}
```

<!-- more -->

# Webpack打包指令
```batch
:: 因为安装在devDependencies 中, 所以需要用npx 来执行
npx webpack
```

# Webpack打包后的文件
Webpack 打包后的是一个自执行函数(IIFE), 该函数接收一个对象作为参数:
```javascript
(function(modules) {
  // ...
}) ({
  // key: 模块的路径
  "./src/index.js":
  // value: 函数
  (function(module, exports, __webpack_require__) {
    eval("const str = __webpack_require__(/*! ./str.js */ \"./src/str.js\")\r\n\r\nconsole.log(`Hello ${str}`)\n\n//# sourceURL=webpack:///./src/index.js?");
  }),

  "./src/str.js":
  (function(module, exports) {
    eval("module.exports = 'World'\n\n//# sourceURL=webpack:///./src/str.js?");
  })
})
```

函数内的实现
```javascript
// webpackBootstrap
// webpack 启动函数
(function(modules) {
  // The module cache
  // module 缓存
  var installedModules = {};

  // The require function
  // 配置/实现require 方法
  // (moduleId = "./src/index.js")
  function __webpack_require__(moduleId) {

    // Check if module is in cache
    // 检查module是否有缓存
    if(installedModules[moduleId]) {
      return installedModules[moduleId].exports;
    }
    // Create a new module (and put it into the cache)
    // 创建module
    var module = installedModules[moduleId] = {
      i: moduleId,
      l: false,
      exports: {}
    };

    // Execute the module function
    // 从module中的id(key), 找到value(函数), 用过call()来执行函数
    modules[moduleId].call(module.exports, module, module.exports, __webpack_require__);

    // Flag the module as loaded
    // 将module 标记为已加载(loaded)
    module.l = true;

    // Return the exports of the module
    // 返回module 的导出
    return module.exports;
  }


  // expose the modules object (__webpack_modules__)
  __webpack_require__.m = modules;

  // expose the module cache
  __webpack_require__.c = installedModules;

  // define getter function for harmony exports
  __webpack_require__.d = function(exports, name, getter) {
    if(!__webpack_require__.o(exports, name)) {
      Object.defineProperty(exports, name, { enumerable: true, get: getter });
    }
  };

  // define __esModule on exports
  __webpack_require__.r = function(exports) {
    if(typeof Symbol !== 'undefined' && Symbol.toStringTag) {
      Object.defineProperty(exports, Symbol.toStringTag, { value: 'Module' });
    }
    Object.defineProperty(exports, '__esModule', { value: true });
  };

  // create a fake namespace object
  // mode & 1: value is a module id, require it
  // mode & 2: merge all properties of value into the ns
  // mode & 4: return value when already ns object
  // mode & 8|1: behave like require
  __webpack_require__.t = function(value, mode) {
    if(mode & 1) value = __webpack_require__(value);
    if(mode & 8) return value;
    if((mode & 4) && typeof value === 'object' && value && value.__esModule) return value;
    var ns = Object.create(null);
    __webpack_require__.r(ns);
    Object.defineProperty(ns, 'default', { enumerable: true, value: value });
    if(mode & 2 && typeof value != 'string') for(var key in value) __webpack_require__.d(ns, key, function(key) { return value[key]; }.bind(null, key));
    return ns;
  };

  // getDefaultExport function for compatibility with non-harmony modules
  __webpack_require__.n = function(module) {
    var getter = module && module.__esModule ?
      function getDefault() { return module['default']; } :
      function getModuleExports() { return module; };
    __webpack_require__.d(getter, 'a', getter);
    return getter;
  };

  // Object.prototype.hasOwnProperty.call
  __webpack_require__.o = function(object, property) { return Object.prototype.hasOwnProperty.call(object, property); };

  // __webpack_public_path__
  __webpack_require__.p = "";


  // Load entry module and return exports
  // 入口模块
  return __webpack_require__(__webpack_require__.s = "./src/index.js");
})
```