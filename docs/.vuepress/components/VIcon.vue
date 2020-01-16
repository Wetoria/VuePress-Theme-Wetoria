<template>
  <!-- <i :class="iconClass" :style="{ height: height, lineHeight: height }"></i> -->
  <svg class="icon iconfont" aria-hidden="true">
    <use :xlink:href="iconName"></use>
  </svg>
</template>

<script>
export default {
  name: 'vIcon',
  props: {
    iconClass: {
      type: String,
      required: true,
    },
  },
  computed: {
    iconName() {
      return `#${this.iconClass}`;
    },
  },
};
</script>
