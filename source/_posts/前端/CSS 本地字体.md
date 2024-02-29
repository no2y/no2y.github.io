---
title: CSS 本地字体
date: 2024-02-29 09:48:58
updated: 2024-02-29 09:48:58
tags:
  - CSS
  - 前端
---
# 导入字体包
![image.png](https://npy.icu/images/2024/02/29/image.png)

字体文件命名不是非常规范，通过在[中文网字计划](https://chinese-font.netlify.app/analyze/)站点上传字体包来查看具体元信息：

![image1538432f39a757a8.png](https://npy.icu/images/2024/02/29/image1538432f39a757a8.png)
## font-size
|取值|描述|
|---|---|
|normal|默认的字体粗细。相当于`400`。|
|bold|粗体。相当于`700`。|
|bolder|比父元素更粗。|
|lighter|比父元素更细。|
|100|最细的字体粗细。|
|200|比`100`稍粗一点的字体粗细。|
|300|比`200`稍粗一点的字体粗细。|
|400|相当于`normal`。默认的字体粗细。|
|500|比`400`稍粗一点的字体粗细。|
|600|比`500`稍粗一点的字体粗细。|
|700|相当于`bold`。粗体。|
|800|比`700`稍粗一点的字体粗细。|
|900|最粗的字体粗细。|

|`font-weight`数值|字体粗细类型描述|
|---|---|
|100|Thin|
|200|Extra Light (Ultra Light)|
|300|Light|
|400|Regular (Normal, Book, Roman)|
|500|Medium|
|600|Semi Bold (Demi Bold)|
|700|Bold|
|800|Extra Bold (Ultra Bold)|
|900|Black (Heavy)|

| 字体粗细类型 | 描述 |
| ---- | ---- |
| Thin | 最细的字体粗细。 |
| Extra Light | 比Thin稍粗，非常细的字体粗细。 |
| Light | 细字体粗细，适合非正式文档。 |
| Regular | 标准的字体粗细，用于正文。 |
| Medium | 中等粗细，比Regular稍粗，用于强调文本。 |
| Semi Bold | 半粗字体，比Medium更明显，常用于小标题。 |
| Bold | 粗体，用于标题或需要强调的文字。 |
| Extra Bold | 比Bold更粗，用于大标题或重要文字。 |
| Black | 最粗的字体粗细，用于特别强调的场合。 |

## font-style
| 取值 | 描述 |
| ---- | ---- |
| normal | 默认值。字体以正常风格显示。 |
| italic | 字体以斜体显示。如果没有斜体版本，浏览器会模拟斜体效果。 |
| oblique | 字体以倾斜显示。角度可以通过后面的角度值进行调整，如果没有指定角度或字体没有倾斜版本，效果类似于`italic`。 |
|  |  |
## 重新命名ttf
`FZLANTYJW_XIAN.TTF` -> `FZLanTingYuanS-EL-GB.ttf`
## CSS导入字体定义
```css
@font-face {
  font-family: "方正兰亭圆简体 Extra Light";
  src: url("~@/assets/fonts/FZLanTingYuanS-EL-GB.ttf") format("truetype");
  font-weight: 200;
  font-style: normal;
}
.font-size--extra-light  {
  font-family: "方正兰亭圆简体 Extra Light";
  font-weight: 200;
}
```
`CSS` 书写规范参考[这里](https://github.com/Kozea/WeasyPrint/issues/1028)
# 问题排查
导入其中一个艺术字体包时，控制台警告：
![image2632ec03b4365a97.png](https://npy.icu/images/2024/02/29/image2632ec03b4365a97.png)
序列化文件失败，猜测可能是艺术字体格式不支持：
1. https://github.com/foliojs/fontkit/issues/69
2. https://zhuanlan.zhihu.com/p/360490466
使用[transfonter.org](https://transfonter.org/)转换一次ttf包，再次导入则正常。