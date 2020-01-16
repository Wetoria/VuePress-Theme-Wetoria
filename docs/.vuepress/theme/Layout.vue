<template>
  <div class="container">
    <div class="user-info-main" v-if="isHome || isPostDetail">
      <div class="info-container">
        <a href="/" title="前往 Wetoria 的主页">
          <img src="/portrait.jpg" />
        </a>
        <h1>{{ $site.title }}</h1>
        <hr class="divider" />
        <p>{{ $site.description }}</p>
        <hr class="divider divider--secondary" />
        <div class="navigation-wrapper">
          <div class="inside">
            <ul class="navigation-list">
              <li class="navigation-item">
                <a>博客</a>
              </li>
            </ul>
          </div>
          <div class="outside">
            <a @click="jumpToGitHub">
              <v-icon icon-class="v-github" />
            </a>
          </div>
        </div>
      </div>
      <div class="cover"></div>
    </div>
    <div class="main">
      <div class="post-container">
        <div class="post-list" v-if="isHome">
          <div class="post-item" v-for="(page, index) in posts" :key="page.key">
            <h2 class="post-title">
              <a :href="page.regularPath">{{ page.title }}</a>
            </h2>
            <p class="post-excerpt" v-if="page.excerpt" v-html="page.excerpt"></p>
            <div class="post-meta">
              <time>{{ getDate(page.lastUpdated) }}</time>
              <span class="tags" v-if="shouldRenderTags(page)">
              • 于 
                <span class="tag" v-for="tag in page.frontmatter.tags">
                  <a class="tag-link">{{tag}}</a>
                </span>
              </span>
              <a :href="page.regularPath" class="btn-continue">继续阅读</a>
            </div>
            <hr class="post-divider" v-if="index !== posts.length - 1" />
          </div>
        </div>
        <div class="post-detail" v-if="isPostDetail">
          <h1><a :href="$page.regularPath">{{ $page.title }}</a></h1>
          <Content />
        </div>
      </div>
      <div class="footer">
        <span>&copy; 2020 - Power by <a @click="jumpToGitHub">Wetoria</a></span>
      </div>
    </div>
  </div>
</template>

<script>
import '../public/iconfont/iconfont.js';
export default {
  mounted() {
    console.log(this.$router, this.$route, this.$page, this.$site);
    this.$site.pages.sort((a, b) => {
      const dateA = new Date(a.lastUpdated);
      const dateB = new Date(b.lastUpdated);
      return dateB.getTime() - dateA.getTime();
    });
  },
  computed: {
    isHome() {
      return this.$route.path === '/';
    },
    isPostDetail() {
      return this.$route.path.includes('posts');
    },
    posts() {
      return this.$site.pages.filter(item => item.path !== '/');
    },
  },
  methods: {
    getDate(time) {
      const date = new Date(time);
      return `${date.getFullYear()}-${date.getMonth() + 1}-${date.getDate()}`;
    },
    shouldRenderTags(page) {
      return page.frontmatter.tags && page.frontmatter.tags.length;
    },
    jumpToGitHub() {
      window.open(this.$site.themeConfig.github, '_blank');
    },
  }
}
</script>

<style lang="stylus" scoped>
@import '../public/iconfont/iconfont.css'
</style>
