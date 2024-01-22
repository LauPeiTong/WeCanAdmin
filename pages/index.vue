<template lang="pug">
.welcome-page.text-center.mx-auto
  v-img.welcome-image.my-12(:src="logoPath" width="100")
</template>

<script>
export default {
  name: 'IndexPage',
  layout: 'default',
  data () {
    return {
      logoPath: require('../assets/logo/Logo.jpg')
    }
  },
  created () {
    setTimeout(() => this.$router.push({ path: '/dashboard' }), 1000)
  },
  methods: {

  }
}
</script>
