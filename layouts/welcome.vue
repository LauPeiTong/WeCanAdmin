<template lang="pug">
v-app.welcome-layout
  v-main
    v-container.fill-height.pa-0(fluid)
      .orange-bar
      Nuxt
</template>

<script>

export default {
  name: 'WelcomeLayout',
  data () {
    return {
    }
  },
  mounted () {
    if (!this.$store.getters['auth/isAuthenticated']) {
      console.log('No Authentication')
      this.$router.push({ path: '/login' })
    }
  }
}
</script>

<style scoped>
.orange-bar {
  position: absolute;
  bottom: 0;
  right: 0;
  width: 100%;
  min-height: 20px;
  background-color: #FAAF08;
}
</style>
