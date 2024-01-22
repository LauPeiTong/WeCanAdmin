<template lang="pug">
v-app
  v-main
    v-container.h-100.pa-0(fluid)
      v-row.ma-0.h-100.w-100
        side-bar(v-if="isAuthenticated")
        Nuxt
</template>

<script>
import { mapGetters } from 'vuex'
import SideBar from '../components/sidebar/SideBar'

export default {
  name: 'DefaultLayout',
  components: {
    SideBar
  },
  data () {
    return {
    }
  },
  computed: {
    ...mapGetters({
      isAuthenticated: 'auth/isAuthenticated'
    })
  },
  mounted () {
    if (!this.isAuthenticated) {
      console.log('No Authentication')
      this.$router.push({ path: '/login' })
    }
  }
}
</script>
