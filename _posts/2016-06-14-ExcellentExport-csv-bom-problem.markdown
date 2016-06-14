---
layout:     post
title:      "ExcellentExport导出csv中文乱码问题解决方法"
subtitle:   ""
date:       2016-06-14
author:     "lixuanbin"
header-img: "img/bg4.jpg"
tags:
    - 前端开发
    - JavaScript
---

**问题：**
使用ExcellentExport把HTML table导出为excel时候，如果表格行数过多，会导致无法导出。改为csv可以导出成功，但是下载下来的csv文件在Windows显示乱码。

**原因：**
在简体中文环境下，EXCEL打开的CSV文件默认是ANSI编码，如果CSV文件的编码方式为utf-8、Unicode等编码需要在文件头加入BOM，否则可能就会出现文件乱码的情况。

**解决方法：**
每次写入数据到csv前先加入一个BOM。
打开ExcellentExport.js文件，把

```javascript
var hrefvalue = uri.csv + base64(csvData);
```

修改为：

```javascript
var hrefvalue = uri.csv + '77u/' + base64(csvData);
```
保存后重新导出即可。