<template>
  <div class="post-meta">
    <time>{{ getDate(page.lastUpdated) }}</time>
    <span class="tags" v-if="shouldRenderTags(page)">
    • 于 
      <span class="tag" v-for="tag in page.frontmatter.tags">
        <a class="tag-link">{{tag}}</a>
      </span>
    </span>
    <a v-if="showContinue" :href="page.regularPath" class="btn-continue">继续阅读</a>
  </div>
</template>

<script>
export default {
  props: {
    showContinue: {
      type: Boolean,
      default: true,
    },
    page: {
      type: Object,
      required: true,
    },
  },
  methods: {
    shouldRenderTags(page) {
      return page.frontmatter.tags && page.frontmatter.tags.length;
    },
  },
}
</script>

<style lang="stylus">
.post-meta
  color: #c7c7c7
  font-size: 0.9em
  margin-top: 10px
  .tag-link
    color: #666
    margin-right: 5px
  .btn-continue
    border: 1px solid #4e97d8
    color: #4e97d8
    padding: 7px 7px
    font-size: 0.8em
    border-radius: 14px
    transition: all 0.5s linear
    margin-left: 5px
    &:hover
      border-color: #1461a7
      color: #1461a7
</style>