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