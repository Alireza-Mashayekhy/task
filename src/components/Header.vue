<template>
  <div class="header">
    <img src="@/assets/images/logo.png" class="logo" alt="logo" />
    <div class="links">
      <router-link :class="{ active: activePage == 1 }" to="/">ثبت آدرس</router-link>
      <router-link :class="{ active: activePage == 2 }" to="/addresses">مشاهده آدرس ها</router-link>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      activePage: 0
    }
  },
  mounted() {
    // select active link after mounted
    if (this.$route.path == '/') {
      this.activePage = 1
    } else {
      this.activePage = 2
    }
  },
  watch: {
    // watch route for change active link when routing
    '$route.path'() {
      if (this.$route.path == '/') {
        this.activePage = 1
      } else {
        this.activePage = 2
      }
    }
  }
}
</script>
