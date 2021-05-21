---
title: 初识SCSS
date: 2018-08-26 20:25:51
tags: [CSS,Sass]
categories: [CSS预处理]
---

# 初识SCSS
## Sass/SCSS 的区别
SCSS是Sass3 引入的新语法，其语法完全兼容CSS3，并且继承了Sass的功能。

Sass和SCSS的区别主要有以下两点:

- 文件拓展名不同
    Sass文件以 .sass 后缀为拓展名，SCSS文件以 .scss 后缀为拓展名。

- 语法不同
    Sass以缩进式语法规则来书写，不带`{}`和`;`，而SCSS的语法书写则与CSS相似。

<!-- more -->

Sass语法:
```sass
body
    margin: 0
    background: #3694FA
    div
        width: 240px
        heigit: 240px
        border: 
            width: 1px
            style: solid
            color: #3694FA
```
**注意冒号后面一定要有空格**

SCSS语法:
```scss
body{
    margin:0;
    background:#3694FA;
    div{
        width:240px;
        height:240px;
        border:{
            width: 1px;
            style: solid;
            color: #3694FA;
        }
    }
}
```

最终输出CSS文件
```css
body {
    margin: 0;
    background: #3694FA;
}
body div {
    width: 240px;
    height: 240px;
    border-width: 1px;
    border-style: solid;
    border-color: #3694FA;
}
```

## SCSS的安装
### 安装Ruby
Sass基于Ruby语言开发而成，因此需要先安装[Ruby](https://rubyinstaller.org/downloads/)。  
或者使用node安装node-sass。

**注意需要将Ruby添加到环境变量中**

安装完成后输入以下指令来测试是否安装成功
```bash
ruby -v
```

### 更换Ruby源
因为国内网络原因导致gem无法正常访问，需要更换为国内源
```bash
#删除原来的gem源
gem sources --remove https://rubygems.org/
#或者使用下面的指令移除所有的gem源
gem sources --clear-all

#替换为国内的gem源(2018.8.26), 注意检查源是否失效
gem sources --add https://gems.ruby-china.com/

#查看是否替换成功
gem sources --list
```

### 安装Sass和Compass
```bash
#安装Sass
gem install sass

#安装Compass
gem install compass
```
测试是否安装成功
```bash
sass -v
compass -v
```

## 编译Sass/SCSS
### 命令行编译
```bash
#编译Sass文件
sass input.sass output.css

#编译SCSS文件
sass input.scss output.css
```

### 监听文件变换，自动编译文件
```bash
#监听单个文件
sass --watch input-file:output-file

#监听文件夹
sass --watch input-dir:output-dir
```

### 常用编译选项
```bash
#编译格式
sass --watch scss:css --style <nested|expanded|compact|compressed>

#添加调试map
sass --watch scss:css --sourcemap=<auto|file|inline|none>

#开启debug信息
sass --watch scss:css --debug-info

#使用CSS-superset SCSS 语法
sass --watch scss:css --scss

#不使用编译缓存
sass --watch scss:css --no-cache
```

**编译格式**

Sass文件
```scss
body{
    margin:0;
    background:#3694FA;
    div{
        width:240px;
        height:240px;
        border:{
            width: 1px;
            style: solid;
            color: #3694FA;
        }
    }
}
```

nested编译格式
```css
body {
  margin: 0;
  background: #3694FA; }
  body div {
    width: 240px;
    height: 240px;
    border-width: 1px;
    border-style: solid;
    border-color: #3694FA; }
```

expanded编译格式
```css
body {
  margin: 0;
  background: #3694FA;
}
body div {
  width: 240px;
  height: 240px;
  border-width: 1px;
  border-style: solid;
  border-color: #3694FA;
}
```

compact编译格式
```css
body { margin: 0; background: #3694FA; }
body div { width: 240px; height: 240px; border-width: 1px; border-style: solid; border-color: #3694FA; }
```

compressed编译格式
```css
body{margin:0;background:#3694FA}body div{width:240px;height:240px;border-width:1px;border-style:solid;border-color:#3694FA}
```