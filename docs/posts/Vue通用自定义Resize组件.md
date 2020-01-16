---
title: Vue通用自定义Resize组件
tags: ['Vue']
time: '2020-01-16'
---
# 1.背景
项目基于Vue+Element-UI，新接到的需求是点击按钮能够从右侧弹出一个页面，并能控制该弹出页面的宽度。在Element-UI中有一个抽屉(Drawer)组件，支持点击从浏览器侧边弹出，但不能实现宽度的调整，于是自己想到实现一个通用的Resize组件，以后如果要使用时，可以直接引入。

# 2.具体实现

```Vue

<template>
  <div
    :class="`resizer-${position}`"
    :style="{
      width: width,
      height: height,
      position: 'absolute',
      right: right,
      left: left,
      top: top,
      bottom: bottom,
      cursor: cursor,
    }"
    :id="uuid"
  />
</template>

<script>
// 定义每个方位的边框样式
const directionMap = {
  top: {
    width: '100%',
    height: '5px',
    top: 0,
    right: 'unset',
    bottom: 'unset',
    left: 0,
    cursor: 'ns-resize',
  },
  right: {
    width: '5px',
    height: '100%',
    top: 0,
    right: 0,
    bottom: 'unset',
    left: 'unset',
    cursor: 'ew-resize',
  },
  bottom: {
    width: '100%',
    height: '5px',
    top: 'unset',
    right: 'unset',
    bottom: 0,
    left: 0,
    cursor: 'ns-resize',
  },
  left: {
    width: '5px',
    height: '100%',
    top: 0,
    right: 'unset',
    bottom: 'unset',
    left: 0,
    cursor: 'ew-resize',
  },
  topLeft: {
    width: '12px',
    height: '12px',
    top: 0,
    right: 'unset',
    bottom: 'unset',
    left: 0,
    cursor: 'nw-resize',
  },
  topRight: {
    width: '12px',
    height: '12px',
    top: 0,
    right: 0,
    bottom: 'unset',
    left: 'unset',
    cursor: 'ne-resize',
  },
  bottomLeft: {
    width: '12px',
    height: '12px',
    top: 'unset',
    right: 'unset',
    bottom: 0,
    left: 0,
    cursor: 'sw-resize',
  },
  bottomRight: {
    width: '12px',
    height: '12px',
    top: 'unset',
    right: 0,
    bottom: 0,
    left: 'unset',
    cursor: 'se-resize',
  },
};
export default {
  props: {
    // 用于指定需要调整的节点
    target: {
      type: String,
    },
    // 用于指明组件是哪个方向的边框
    position: {
      type: String,
    },
    // 是否手动控制边框调整
    // 这里因为ElementUI的drawer通过size控制了宽度，需要手动计算size，也许是我不会用
    manual: {
      type: Boolean,
      default: false,
    },
    // 用于禁用某个方向的resize，防止组件自动调整宽度，影响布局
    disabledAxis: {
      type: String,
      default: '',
    },
  },
  computed: {
    isLeft() {
      return ['left', 'topLeft', 'bottomLeft'].includes(this.position);
    },
    isRight() {
      return ['right', 'topRight', 'bottomRight'].includes(this.position);
    },
    isAxisX() {
      return this.isLeft || this.isRight;
    },
    isTop() {
      return ['top', 'topLeft', 'topRight'].includes(this.position);
    },
    isBottom() {
      return ['bottom', 'bottomLeft', 'bottomRight'].includes(this.position);
    },
    isAxisY() {
      return this.isTop || this.isBottom;
    },
    isCorner() {
      return ['topLeft', 'topRight', 'bottomLeft', 'bottomRight'].includes(this.position);
    },
    enabledAxisX() {
      return !['x', 'all'].includes(this.disabledAxis);
    },
    enabledAxisY() {
      return !['y', 'all'].includes(this.disabledAxis);
    },
  },
  data() {
    return {
      ...directionMap[this.position],

      offset: {
        positionOnClickX: undefined,
        positionOnClickY: undefined,
      },
      targetDom: undefined,
      domData: {
        width: undefined,
        height: undefined,
        isPercentWidth: undefined,
        isPercentHeight: undefined,
      },
      modal: undefined,
      // 用于保证获取节点时，获取到的是组件内部的div
      uuid: this._.uniqueId('drawer-resizer'),
    };
  },
  mounted() {
    this.modal = this.generateModal();
    this.resizer = document.querySelector(`#${this.uuid}`);
    this.targetDom = document.querySelector(this.target);
    this.addListener();
  },
  methods: {
    generateModal() {
      const modal = document.createElement('div');
      modal.style.width = '100vw';
      modal.style.height = '100vh';
      modal.style.position = 'fixed';
      modal.style.top = '0';
      modal.style.left = '0';
      modal.style.cursor = this.cursor;
      modal.style['z-index'] = 9998;
      return modal;
    },
    mouseMoveHanlder(e) {
      this.calculateOffset(e);
    },
    mouseDownHanlder(e) {
      this.offset.positionOnClickX = e.clientX;
      this.offset.positionOnClickY = e.clientY;
      if (this.targetDom) {
        this.domData.width = this.targetDom.style.width.replace(/%|px/g, '');
        this.domData.height = this.targetDom.style.height.replace(/%|px/g, '');
      }
      this.resizer.style['z-index'] = 9999;
      this.modal.addEventListener('mousemove', this.mouseMoveHanlder);
      document.body.appendChild(this.modal);
    },
    mouseUpHanlder(e) {
      this.calculateOffset(e);
      this.resizer.style['z-index'] = undefined;
      this.offset = {
        positionOnClickX: undefined,
        positionOnClickY: undefined,
      };
      this.modal.removeEventListener('mousemove', this.mouseMoveHanlder);
      document.body.removeChild(this.modal);
    },
    calculateOffset(e) {
      const {
        positionOnClickX,
        positionOnClickY,
      } = this.offset;

      // 处理X轴
      // 当前鼠标的坐标
      const coordX = e.clientX;
      // 浏览器的宽度
      const windowWidth = window.innerWidth;
      let offsetX;
      // 鼠标移动的距离
      offsetX = positionOnClickX - coordX;
      // 向右移动鼠标，offsetX为负值，处理右边框的resize时，应取反
      if (this.isRight) {
        offsetX = -offsetX;
      }
      // 鼠标移动的距离与浏览器宽度的百分比
      const offsetPercentX = (offsetX / windowWidth) * 100;
      // 当前鼠标相对浏览器最左侧的百分比
      let coordPercentX;
      coordPercentX = (coordX / windowWidth) * 100;
      if (this.isLeft) {
        coordPercentX = 100 - coordPercentX;
      }


      let offsetY;
      let coordPercentY;
      const windowHeight = window.innerHeight;
      const coordY = e.clientY;
      offsetY = positionOnClickY - coordY;
      coordPercentY = (coordY / windowHeight) * 100;
      // 处理Y轴
      if (this.isBottom) {
        offsetY = -offsetY;
      }
      const offsetPercentY = (offsetY / windowHeight) * 100;

      const {
        position,
      } = this;

      const offset = {
        coordX,
        coordPercentX,
        offsetX,
        offsetPercentX,

        coordY,
        coordPercentY,
        offsetY,
        offsetPercentY,

        position,

      };
      if (this.target) {
        this.handlerDomStyle(offset);
      }
      if (this.manual) {
        this.$emit('offset', offset);
      }
    },
    handlerDomStyle(offset) {
      if (this.targetDom) {
        const {
          offsetX,
          offsetPercentX,
          offsetY,
          offsetPercentY,
        } = offset;
        const isPercentWidth = this.targetDom.style.width.includes('%');
        const isPercentHeight = this.targetDom.style.height.includes('%');
        const {
          width,
          height,
        } = this.domData;
        if (this.isAxisX && this.enabledAxisX) {
          if (isPercentWidth) {
            this.targetDom.style.width = `${Number(width) + Number(offsetPercentX)}%`;
          } else {
            this.targetDom.style.width = `${Number(width) + Number(offsetX)}px`;
          }
        }
        if (this.isAxisY && this.enabledAxisY) {
          if (isPercentHeight) {
            this.targetDom.style.height = `${Number(height) + Number(offsetPercentY)}%`;
          } else {
            this.targetDom.style.height = `${Number(height) + Number(offsetY)}px`;
          }
        }
      }
    },
    addListener() {
      if (this.resizer) {
        this.resizer.addEventListener('mousedown', this.mouseDownHanlder);
        this.modal.addEventListener('mouseup', this.mouseUpHanlder);
      }
    },
  },
};
</script>
```

# 3.改进思路
后来在npm上搜索了一下resize相关的包，发现可以将整个边框封入一个div中，直接做一个可以调整的div出来，也是一个不错的想法。另外，在target对象获取的时候，还可以使用$parent来获取父节点，这样是不是会减少dom获取带来的性能开销？还有组件销毁时，需要移除相应的事件。

一直在注重功能的实现，欠缺性能优化相关的知识点，如果你有好的意见，或发现其中的不足，还望指出。
