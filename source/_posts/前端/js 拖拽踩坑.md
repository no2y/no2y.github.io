---
title: js 拖拽踩坑
date: 2023-12-27 15:54:24
updated: 2023-12-27 15:54:24
tags:
  - JavaScript
categories:
  - 前端
---
## 背景
部分平台的页面右下角都接入了客服按钮，一些情况下这个按钮会把页面其他元素遮挡住，影响部分操作。
[![image.md.png](https://npy.icu/images/2023/12/27/image.md.png)](https://npy.icu/image/GEK)
所以计划调整一下按钮，暂定有两种方案：
1. 按钮静止时贴边隐藏
2. 按钮可纵向调整位置
>`1` 的话贴边隐藏也有可能会有某些极端边缘元素会被影响到，所以考虑 `1` 结合 `2` 做成可拖拽移动的按钮。因为时间问题，暂时只记录一下实现 2 的过程。
## 实现
在原有按钮基础上增加拖拽事件（心想那不是小菜一碟），马上想到我的好朋友 ChatGPT 并整理了一下需求描述向他倾诉：

`请使用 JavaScript 实现可在屏幕范围内纵向拖拽移动的按钮
`
他的回复是这样的（省略HTML）：
```javascript
const btn = document.getElementById('drag-btn');
let posY;

btn.onmousedown = function(e) {
// 获取鼠标在元素内的位置
posY = e.clientY - btn.getBoundingClientRect().top;

document.onmousemove = function(e) {
  // 计算按钮新的位置
  let newTop = e.clientY - posY;

  // 限制按钮不超出屏幕顶部和底部
  newTop = Math.max(newTop, 0);
  newTop = Math.min(newTop, window.innerHeight - btn.offsetHeight);

  // 应用新的位置
  btn.style.top = newTop + 'px';
  btn.style.bottom = 'auto'; // 防止和bottom属性冲突
};

document.onmouseup = function() {
  // 停止拖动
  document.onmousemove = null;
  document.onmouseup = null;
};
};

// 防止屏幕大小变化时按钮位置不正确
window.onresize = function() {
btn.style.bottom = '20px';
btn.style.top = 'auto';
};

```
完美~ CTRL + C 复制到上下文代码中，并给按钮绑定上这些事件，打开 Chrome 看看效果

[![20231227170807_rec_.md.gif](https://npy.icu/images/2023/12/27/20231227170807_rec_.md.gif)](https://npy.icu/image/NqN)

拖动效果是没有问题，但是鼠标在按钮上松开，就触发原有按钮的点击事件了，这不是想要的效果，还要做一下兼容：
```javascript
// 省略其他

// 拖动标志
let isDragging = false;
// 复制原有点击事件
const clickEventClone = btn.onclick;
document.onmousemove = function (e) {
   isDragging = true;
}
// 在当前按钮松开鼠标时
btn.onmouseup = function (e) {
	if (isDragging) {
	  this.onclick = null;
	} else {
	  clickEventClone && clickEventClone.call(this, e);
	}
};
```
保存，浏览器再打开看看，拖动结束不会再触发原有点击事件了，正常点击则触发原有点击事件。
写到这里，本地调试基本没有问题了，Commit 一下部署到生产环境准备开开心心下班。

## Bug
部署到生产环境之后，在某个平台下发现了 Bug：
拖动过程中鼠标移入 iframe 之后松开，再回到父级 Document，在松开状态下按钮随鼠标移动（文档的鼠标松开事件没有触发）

[![20231227173112_rec_.md.gif](https://npy.icu/images/2023/12/27/20231227173112_rec_.md.gif)](https://npy.icu/image/PTl)

这个问题其实也好解决：
1. 将 mouse 事件全部绑定在按钮上，鼠标移除按钮时将 move 事件移除
2. 拖拽时禁用所有子 iframe 的鼠标事件，让原有文档正确释放 mouseup
3. 拖拽时添加全局遮罩，让鼠标事件停留在当前 Document

使用 `1` 的话会有一个体验问题，因为这次功能是在竖直方向上移动，如果用户鼠标脱离了垂直方向的惯性，用会导致按钮脱离鼠标箭头骤停。说人话就是 `不跟手`。

[![20231227175133_rec_.md.gif](https://npy.icu/images/2023/12/27/20231227175133_rec_.md.gif)](https://npy.icu/image/D0V)

经过深思熟虑之后打算使用 `方案3`，最终实现的代码：
```typescript
class DragCover {
  private coverElmentId = `drag_cover_${Date.now()}`;
  private coverElement: HTMLElement;
  private coverStyle = `
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    z-index:9998;
    user-select: none;
    visibility: hidden;
    `;
  constructor(coverElmentId?: string) {
    this.coverElmentId = coverElmentId ?? this.coverElmentId;
  }

  public createCover() {
    if (this.coverElement) return;
    this.coverElement = document.createElement("div");
    this.coverElement.id = this.coverElmentId;
    this.coverElement.style.cssText = this.coverStyle;
    document.body.appendChild(this.coverElement);
  }

  public hideCover() {
    this.coverElement.style.visibility = "hidden";
  }

  public showCover() {
    this.coverElement.style.visibility = "visible";
  }

  public removeCover() {
    if (!this.coverElement) return;
    document.body.removeChild(this.coverElement);
    this.coverElement = null;
  }
}

enum DragMode {
  Horizontal,
  Vertical,
  Both
}
class Draggable {
  private dragElement: HTMLElement;
  private dragMode: DragMode = DragMode.Both;
  private active = false;
  private xOffset = 0;
  private yOffset = 0;
  private dragCover: DragCover = new DragCover();
  private callback: () => void;
  private isDragging = false;
  private initX: number;
  private initY: number;
  private criticalValue = 0.1;

  constructor(elementId: string, dragMode?: DragMode, callback?: () => void) {
    this.dragElement = document.getElementById(elementId) as HTMLElement;
    this.dragMode = dragMode;
    this.callback = callback ?? this.callback;

    this.dragElement.addEventListener("mousedown", e => this.dragStart(e));
    this.dragElement.addEventListener("mouseup", e => this.handleCallback(e));
    document.addEventListener("mouseup", e => this.dragEnd(e));
    document.addEventListener("mousemove", e => this.drag(e));
    this.dragCover.createCover();
  }

  private handleCallback(e: MouseEvent) {
    if (!this.isDragging && this.callback) {
      e.preventDefault();
      this.callback();
    }
  }

  private dragStart(e: MouseEvent): void {
    this.isDragging = false;
    this.dragCover.showCover();
    this.xOffset = e.clientX - this.dragElement.getBoundingClientRect().left;
    this.yOffset = e.clientY - this.dragElement.getBoundingClientRect().top;

    this.initX = e.clientX;
    this.initY = e.clientY;

    if (e.target === this.dragElement) {
      this.active = true;
    }
  }

  private dragEnd(e: MouseEvent): void {
    this.active = false;
    this.dragCover.hideCover();
  }

  private drag(e: MouseEvent): void {
    if (this.active) {
      e.preventDefault();
      if (
        Math.abs(e.clientX - this.initX) > this.criticalValue &&
        Math.abs(e.clientY - this.initY) > this.criticalValue
      ) {
        this.isDragging = true;
      }
      const viewportWidth = window.innerWidth;
      const viewportHeight = window.innerHeight;

      let xPos = e.clientX - this.xOffset;
      let yPos = e.clientY - this.yOffset;

      xPos = Math.max(xPos, 0);
      xPos = Math.min(xPos, viewportWidth - this.dragElement.offsetWidth);

      yPos = Math.max(yPos, 0);
      yPos = Math.min(yPos, viewportHeight - this.dragElement.offsetHeight);

      switch (this.dragMode) {
        case DragMode.Horizontal:
          this.setPos(xPos, null);
          break;
        case DragMode.Vertical:
          this.setPos(null, yPos);
          break;
        case DragMode.Both:
          this.setPos(xPos, yPos);
          break;
        default:
          this.setPos(xPos, yPos);
          break;
      }
    }
  }

  private setPos(xPos: number, yPos: number): void {
    if (xPos) {
      this.dragElement.style.left = `${xPos}px`;
      this.dragElement.style.right = "auto";
    }
    if (yPos) {
      this.dragElement.style.top = `${yPos}px`;
      this.dragElement.style.bottom = "auto";
    }
  }
}
```
## 总结一下
1. 注意按钮 `z-index` 要大于遮罩的 `z-index` 
2. 拓展了一个 `dragMode` 以设置横向、纵向、360° 方向的移动
3. 为什么不使用拖拽的轮子（？
	1. 起初以为是简单的功能，还是有坑踩
	2. 考虑到 JSSDK 体积，本身也不是特别复杂的功能
4. 还可以继续优化，譬如 `setPos()` 可以使用 `transform: translate3d(x, x, 0)` 来调动 `GPU` 处理可能会更加高效
	1. 需要重新考虑边界计算问题
5. 增加了一个边缘值 `criticalValue` 以降低误触概率
6. `handleCallback` 方法代替了原有元素的点击事件
	1. 不需要 `btn.onclick` 了，（也很难兼容
7. 下班