---
title: Webpack中CSS样式的处理
date: 2019-08-05 22:24:12
tags: [Webpack]
categories: [打包工具]
---

# CSS样式的处理
安装`css-loader`和`style-loader`
```batch
yarn add css-loader style-loader -D
```

在index.js中引入.css文件
```javascript
require('./index.css')
```

在webpack.config.js中配置css模块
```javascript
module.exports = {
  mode: 'development',
  entry: './src/index.js',
  output: {
    filename: 'bundle.[hash:4].js',
    path: path.resolve(__dirname, 'dist')
  },
  devServer: {
    // ...
  },
  plugins: [
    // ...
  ],
  // 模块
  module: {
    // 规则
    rules: [
      {
        // 匹配.css 结尾的文件
        test: /\.css$/,
        // 可用字符串, 也可用对象
        // loader的顺序默认是从 右往左/下到上 执行
        // css-loader, 解析@import语法
        // style-loader, 把css 插入到head标签中
        use: [
          {
            loader: 'style-loader',
            options: {
              // 在顶部插入
              insertAt: 'top'
            }
          },
          'css-loader'
        ]
      },
      {// 也可以处理less文件
        test: /\.less$/,
        use: [
          {
            loader: 'style-loader',
            options: {
              // 在顶部插入
              insertAt: 'top'
            }
          },
          'css-loader',  // @import 解析
          'less-loader'  // .less -> .css
        ]
      }
    ]
  }
}
```
