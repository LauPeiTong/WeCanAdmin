<template lang="pug">
.vendor-page.pa-0.ma-0.fill-width
  upper-title.ma-0(:title="'Donations'" :icon="'bell'" :rightIconColor="$vuetify.theme.themes.light.primary")
  v-row.ma-0.pt-14.fill-width.px-2
    v-col.pb-6(cols="12")
      donation-summary-vue
    v-col(cols="12")
      donation-list-vue

</template>

<script>
import { mapGetters } from 'vuex'
import DonationListVue from '~/components/donations/DonationList.vue'
import DonationSummaryVue from '~/components/donations/DonationSummary.vue'

export default {
  name: 'DonationPage',
  components: {
    DonationListVue,
    DonationSummaryVue
  },
  layout: 'default',
  data () {
    return {
    }
  },
  computed: {
    ...mapGetters({
    })
  },
  methods: {
  }
}
</script>

<style scoped>
.shadow {
  box-shadow: 0px 0px 4px 0px rgba(0, 0, 0, 0.12) !important;
}
</style>
