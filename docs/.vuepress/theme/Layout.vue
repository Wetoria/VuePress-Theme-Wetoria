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
            <v-link>
              <v-icon icon-class="v-github" />
            </v-link>
          </div>
        </div>
      </div>
      <div class="cover"></div>
    </div>
    <div class="main">
      <post-list v-if="isHome" />
      <post-detail v-if="isPostDetail" />
      <div class="footer">
        <span>&copy; 2020 - Power by <v-link>Wetoria</v-link></span>
      </div>
    </div>
  </div>
</template>

<script>
import '../public/iconfont/iconfont.js';

export default {
  computed: {
    isHome() {
      return this.$route.path === '/';
    },
    isPostDetail() {
      return this.$route.path.includes('posts');
    },
  },
  methods: {
  }
}
</script>

<style lang="stylus" scoped>
@import '../public/iconfont/iconfont.css'
</style>
