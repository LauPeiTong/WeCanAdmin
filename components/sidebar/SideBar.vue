<template lang="pug">
sidebar-menu-akahon(
  :menuItems="menus"
  :isUsedVueRouter="true"
  :menuTitle="'WECAN'"
  :menuLogo="require(`../../assets/img/wecan-logo.png`)"
  :profileImg="require(`../../assets/employee/3.png`)"
  :profileName="user.username"
  :profileRole="'Admin'"
  :bgColor="'white'"
  :secondaryColor="$vuetify.theme.themes.light.primary"
  :logoTitleColor="$vuetify.theme.themes.light.primary"
  :iconsColor="$vuetify.theme.themes.light.primary"
  :searchInputTextColor="$vuetify.theme.themes.light.secondary"
  :menuItemsHoverColor="$vuetify.theme.themes.light.background"
  :menuItemsTextColor="$vuetify.theme.themes.light.secondary"
  @button-exit-clicked="logout"
  )
</template>

<script>
import { mapGetters, mapActions } from 'vuex'
import SidebarMenuAkahon from './SidebarManuAkahon.vue'

export default {
  name: 'SideBar',
  components: { SidebarMenuAkahon },
  data () {
    return {
      menus: [
        {
          link: '/dashboard',
          name: 'Dashboard',
          tooltip: 'Dashboard',
          icon: 'bx-grid-alt'
        },
        {
          link: '/donations',
          name: 'Donations',
          tooltip: 'Donations',
          icon: 'bx-money'
        },
        {
          link: '/sales',
          name: 'Surplus Food Sales',
          tooltip: 'Surplus Food Sales',
          icon: 'bx-line-chart'
        },
        {
          link: '/vendors',
          name: 'Vendors',
          tooltip: 'Vendors',
          icon: 'bx-user-check'
        },
        {
          link: '/',
          name: 'Setting',
          tooltip: 'Setting',
          icon: 'bx-cog'
        }
      ]
    }
  },
  computed: {
    ...mapGetters({
      user: 'auth/getAuthUser'
    })
  },
  methods: {
    ...mapActions({
      refresh: 'auth/logout'
    }),
    goToPath (link) {
      this.$router.push({
        path: link
      })
    },
    async logout () {
      const response = await this.$axios.$post('/api/users/logout/')
      console.log('Logout: ', response)
      window.document.body.style.paddingLeft = 0
      this.refresh()
      this.$axios.defaults.headers.common.Authorization = ''
      this.$router.push('/login')
    }
  }
}
</script>
