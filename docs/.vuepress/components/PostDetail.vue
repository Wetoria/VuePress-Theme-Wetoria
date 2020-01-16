<template>
  <div class="post-detail">
    <h1><a :href="$page.regularPath">{{ $page.title }}</a></h1>
    <post-meta
      :page="$page"
      :showContinue="false"
    />
    <Content />
  </div>
</template>

<script>
export default {
  mounted() {
    console.log(this, this.$page);
  },
}
</script>

<style lang="stylus">
.post-detail
  padding: 50px 10%
  flex-grow: 1
p
  margin: 1.2em 0 -1.2em
  padding-bottom: 1.2em
h1, h2, h3, h4, h5, h6
  padding-top: 1.2em
</style>