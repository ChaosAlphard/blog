---
title: 配置Firefox浏览器书签打开方式
date: 2018-08-03 16:28:24
tags: [Browser,Firefox]
categories:
- [小技巧]
- [浏览器]
---

# 配置Firefox浏览器书签打开方式
在Firefox浏览器中，书签和地址栏链接的打开方式默认是覆盖当前标签页的，那么，怎样让Firefox浏览器默认在新标签页打开书签和地址栏链接呢？  
其实很简单。

<!-- more -->

首先，在浏览器地址栏输入
```
about:config
```
然后搜索"intab"  
将以下两个选项的值
```
# 在新标签页打开书签
browser.tabs.loadBookmarksInTabs
# 在新标签页打开url 地址
browser.urlbar.openintab
```
由false改为true即可

适用版本61.0+