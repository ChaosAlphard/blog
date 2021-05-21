---
title: Webpack配置HTML template
date: 2019-08-03 19:25:57
tags: [Webpack]
categories: [打包工具]
---

# Webpack配置HTML template
安装html-webpack-plugin

```batch
yarn add html-webpack-plugin -D
:: or
npm install html-webpack-plugin -D
```

配置webpack

```javascript
// 引入html-webpack-plugin模块
const HtmlWebpackPlugin = require('html-webpack-plugin')

module.exports = {
  mode: 'development',
  entry: './src/index.js',
  output: {
    // bundle.[hash].js: 给打包的文件追加hash
    // 可指定长度: [hash:<length>]
    filename: 'bundle.[hash:4].js',
    path: path.resolve(__dirname, 'dist')
  },
  devServer: {
    port: 3000,
    progress: true,
    contentBase: './dev',
    compress: true
  },
  // 插件
  plugins: [
    new HtmlWebpackPlugin({
      // 模版文件路径
      template: './src/index.html',
      // 打包后的文件名字
      filename: 'index.html',
      minify: {
        // 去除html中的双引号
        removeAttributeQuotes: true,
        // 合并为一行
        collapseWhitespace: true,
      },
      // 给打包的文件加上hash戳
      // <script src=bundle.js?5c5c6o9e></script>
      hash: true
    })
  ]
}
```

创建index.html文件

```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Document</title>
</head>
<body>
  <!-- template -->
</body>
</html>
```