---
title: Rollup插件 plugin-replace 使用记录
date: 2023-12-13 19:28:27
updated: 2023-12-13 19:28:27
tags:
  - rollup
---
使用[@rollup/plugin-replace](https://www.npmjs.com/package/@rollup/plugin-replace)插件，需要注意目标替换处的写法

```javascript
...
replace({
    'PROCESS.NODE.ENV': JSON.stringfy(PROCESS.NODE.ENV)
})
...
```

以下代码会被正确替换

```javascript
// 源代码
PROCESS.NODE.ENV === 'test' ? 'test.com' : 'prod.com'
// iife打包产物
'test' === 'test' ? 'test.com' : 'prod.com'
```

以下代码不会被正确替换

```javascript
// 源代码
PROCESS.NODE.ENV.includes('test') ? 'test.com' : 'prod.com'
PROCESS.NODE.ENV.indexOf('test') !== -1 ? 'test.com' : 'prod.com'
// iife打包产物
PROCESS.NODE.ENV.includes('test') ? 'test.com' : 'prod.com'
PROCESS.NODE.ENV.indexOf('test') !== -1 ? 'test.com' : 'prod.com'
```