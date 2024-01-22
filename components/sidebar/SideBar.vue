<template lang="pug">
sidebar-menu-akahon(
  :menuItems="menus"
  :isUsedVueRouter="true"
  :menuTitle="'WECAN'"
  :menuLogo="require(`../../assets/img/wecan-logo.png`)"
  :profileImg="require(`../../assets/employee/1.png`)"
  :bgColor="'white'"
  :secondaryColor="$vuetify.theme.themes.light.primary"
  :logoTitleColor="$vuetify.theme.themes.light.primary"
  :iconsColor="$vuetify.theme.themes.light.primary"
  :searchInputTextColor="$vuetify.theme.themes.light.secondary"
  :menuItemsHoverColor="$vuetify.theme.themes.light.background"
  :menuItemsTextColor="$vuetify.theme.themes.light.secondary"
  )
</template>

<script>
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

  },
  methods: {
    goToPath (link) {
      this.$router.push({
        path: link
      })
    }
  }
}
</script>
