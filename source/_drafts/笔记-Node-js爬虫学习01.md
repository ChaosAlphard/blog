---
title: '[笔记]Node.js爬虫学习01'
date: 2018-09-29 20:19:38
tags: [JavaScript,Node.js]
---

# Node.js爬虫

`npm install request --save`

```javascript
const request = require("request");

request(url,(err,res,txt)=>{
	console.timeEnd("解析成功, 用时");
	if(!err&&res.statusCode===200){
		DownloadPictures(JSON.parse(txt));
	}
}
```

<!-- more -->

待补...