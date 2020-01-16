<template>
  <div class="post-container">
    <div class="post-list">
      <div class="post-item" v-for="(page, index) in posts" :key="page.key">
        <h2 class="post-title">
          <a :href="page.regularPath">{{ page.title }}</a>
        </h2>
        <p class="post-excerpt" v-if="page.excerpt" v-html="page.excerpt"></p>
        <post-meta
          :page="page"
        />
        <hr class="post-divider" v-if="index !== posts.length - 1" />
      </div>
    </div>
  </div>
</template>

<script>
export default {
  mounted() {
    this.$site.pages.sort((a, b) => {
      const dateA = new Date(a.lastUpdated);
      const dateB = new Date(b.lastUpdated);
      return dateB.getTime() - dateA.getTime();
    });
  },
  computed: {
    posts() {
      return this.$site.pages.filter(item => item.path !== '/');
    },
  },
  methods: {
  },
}
</script>

<style lang="stylus">
.post-container
  .post-list
    padding: 50px 10%
    .post-item
      flex: 1
    .post-title
      margin-bottom: 5px
      font-size: 1.5em
      border-bottom: none
    .post-excerpt
      margin: 0;
      font-size: 0.9em
      color: #999;
    .post-divider
      width: 30%
      margin: 2.2em 0 2.1em 0
      border-top: 1px solid #ddd

</style>