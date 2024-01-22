<template lang="pug">
.vendor-page.pa-0.ma-0.fill-width
  upper-title.ma-0(:title="'Surplus Food Sales'" :icon="'bell'" :rightIconColor="$vuetify.theme.themes.light.primary")
  v-row.ma-0.pt-14.fill-width.px-2
    v-col.pb-6(cols="12")
      order-summary-vue
    v-col(cols="12")
      order-list-vue

</template>

<script>
import { mapGetters } from 'vuex'
import OrderListVue from '~/components/sales/OrderList.vue'
import OrderSummaryVue from '~/components/sales/OrderSummary.vue'
export default {
  name: 'SalesPage',
  components: {
    OrderListVue,
    OrderSummaryVue
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
