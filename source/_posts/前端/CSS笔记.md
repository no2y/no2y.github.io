---
title: CSS笔记
date: 2024-01-16 19:31:22
updated: 2024-01-16 19:31:22
tags:
  - 笔记
---
## Transform 和 Animation
**场景：**
当动画的`animation-fill-mode`为`forwards`时，属性`transform: translate3D(1rem, 0, 0);`无效，修改为`backwardS`生效
```less
.test1 {
  transform: translate3D(100px, 0, 0);
}

.test1.animate {
  .move();
}

.move {
  animation: shiftSlideLeft 3000ms ease-out forwards;
}

@keyframes shiftSlideLeft {
  0% {
    transform: translateX(200px);
  }
  100% {
    transform: translateX(0);
  }
}
```

## 翻转效果
常规的做法就是用`transform: rotateY(180deg)`和两个正反面`div`实现，看看效果和实现代码：
![20240120150201_rec_.gif](https://npy.icu/images/2024/01/20/20240120150201_rec_.gif)
```html
<div class="card">
	<div class="back">背面</div>
	<div class="front">正面</div>
</div>
```

```css
.card {
	height: 200px;
	width: 200px;
	position: relative;
	border: 1px solid gold;
	transform-style: preserve-3d;
	perspective: 150rem; /* 这个值越小翻转效果越逼真 */
}

.front,
.back {
	width: 100%;
	height: 100%;
	position: absolute;
	font-size: 40px;
	color: #fff;
	box-sizing: border-box;
	transition: all 0.5s;
	backface-visibility: hidden;
	display: flex;
	justify-content: center;
	align-items: center;
}
.front {
	background: red;
}
.back {
	background: blue;
	transform: rotateY(-180deg);
}
.card:hover .front {
	transform: rotateY(180deg);
}
.card:hover .back {
	transform: rotateY(0deg);
}
```
**但是如果旋转的对象为`card`会出现这种都抖动的现象：**
```css
.card:hover {
	transform: rotateY(180deg);
}
```
注意黄色边框跟着一起翻转了
![20240120150553_rec_.gif](https://npy.icu/images/2024/01/20/20240120150553_rec_.gif)