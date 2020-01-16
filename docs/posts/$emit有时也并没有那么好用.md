---
title: $emit有时也并没有那么好用
tag: Vue
---
## 背景

在项目中，自定义了一个组件，在点击子组件时，触发选中事件，并通过`$emit`，将子组件的数据传递给父组件，另外有一个全选按钮来触发子组件的全部选中。本来按照设想，子组件的事情交给子组件处理，在修改子组件的状态时通过`$emit`来进行数据交互，但是在数据量超出一定程度时，`$emit`很影响性能。

其实这就是在组件开发中，如何处理`onSelect`以及`selectionChange`。

## 组件Demo

下面几段代码是父子组件的简单定义，在子组件中通过监听`data.isSelect`的变化，来触发选中事件，设想是在使用组件的过程中，依旧可以通过设置`data.isSelect`字段，来更新组件状态，以及触发选中。

父组件主要是监听子组件的事件，组装`selection`，并且将子组件的事件继续向外传递。

__父组件template__

```html
<template>
  <div>
    <button v-model="checkedAll" @click="onSelectAll">全选</button>
    <children
      v-for="item of children"
      @onSelect="onSelect"
      :data="item"
    ></children>
  </div>
</template>
```

__父组件script__

```js
export default {
  data() {
    return {
      checkedAll: false,
      children: [],
      selection: [],
    };
  },
  methods: {
    onSelectAll() {
      this.children.forEach((child) => {
        child.isSelect = true;
      });
    },
    onSelect(child, selectStatus) {
      if (selectStatus) {
        this.selection.push(child);
      } else {
        this.selection = this.selection.filter(item => item != child);
      }
      this.$emit('onSelect', child, selectStatus);
      this.$emit('selectionChange', this.selection);
    },
  },
}
```

子组件负责数据的展示，以及选中事件的处理，在 demo 中父组件只有一个，但是在实际项目中，有两个不同类型的父组件使用，也是将共同逻辑抽出的一个良好的例子。

__子组件template__

```html
<template>
  <div @click="onSelect">
    <span>{{ data.isSelect ? 'checked' : 'unchecked' }}</span>
    <span>{{ data.name }}</span>
  </div>
</template>
```

__子组件script__

```js
export default {
  props: ['data'],
  methods: {
    onSelect() {
      this.data.isSelect = !this.data.isSelect;
    },
  },
  watch: {
    'data.isSelect': function() {
      this.$emit('onSelect', this.data, this.data.isSelect);
    },
  },
}
```

## 冲突及处理

这个设计本来没有问题，但是在全选时，如果数据较多，如 `2000+`，全交给`watch`来处理`$emit`事件，每次`$emit`只有几百`ms`，但是整个加起来一共会耗时几分钟。

没有解决办法时，选择参考优秀的开源项目，在`el-table`的源码当中，`toggleRowSelection`的源码如下，通过控制参数`emitChange`来控制事件的触发与否，既能保证相同逻辑的复用，也能控制`$emit`所带来的性能影响。虽然不知道 Element-UI 本意是不是控制性能，但是在实际测试中，对于大数据量的表格，选中事件的响应效果还是很理想的，所以借鉴这个思路，在组件代码中增加控制事件相关的代码。

```js
toggleRowSelection(row, selected, emitChange = true) {
  const changed = toggleRowStatus(this.states.selection, row, selected);
  if (changed) {
    const newSelection = (this.states.selection || []).slice();
    // 调用 API 修改选中值，不触发 select 事件
    if (emitChange) {
      this.table.$emit('select', newSelection, row);
    }
    this.table.$emit('selection-change', newSelection);
  }
},
```

### 增加事件控制

由于父组件并不是调用子组件的 API 修改子组件的状态，选择在子组件`props`的`data`中增加字段来控制，修改后的代码如下：

__父组件 onSelectAll__

```js
onSelectAll() {
  this.children.forEach((child) => {
    child.updateByComponent = true;
    child.isSelect = true;
    this.$nextTick(() => {
      child.updateByComponent = false;
    });
  });
}
```

__子组件 watch__


```js
watch: {
  'data.isSelect': function() {
    if (this.data.updateByComponent) return;
    this.$emit('onSelect', this.data, this.data.isSelect);
  },
}
```

### 实际效果

修改后的代码，将原来的响应时间由4分多钟，降低到了400毫秒，带来的实际体验还是很好的。在同样的数据量情况下，选中效果基本上是及时生效。

## 总结

这是项目中的一次性能优化的反思，也是一次开源项目源码的阅读过程，虽然组件是我自己编写的，坑是自己挖的，但是这次经历也是自己在组件封装上的一个成长。

---

## 后续

在编写了最小 demo 以后，全选的响应在秒级，但是在项目中，更新`isSelect`只涉及单个dom的更新，不知道为什么会产生这样的影响。要了解透的话，得花时间一点一点的去尝试了。

[最小 demo 地址](https://codesandbox.io/s/gracious-meadow-9z2jy)