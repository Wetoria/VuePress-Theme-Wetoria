<template>
  <a @click="jumpToGitHub">
    <slot></slot>
  </a>
</template>

<script>
export default {
  methods: {
    jumpToGitHub() {
      window.open(this.$site.themeConfig.github, '_blank');
    },
  },
}
</script>