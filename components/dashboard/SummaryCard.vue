<template lang="pug">
v-row.dashboard.justify-center.mx-auto.mt-4.shadow.rounded-lg
  v-card.pa-6.pb-6.rounded-lg.fill-width(elevation="0")
    v-row
      v-col(:cols="9")
        v-card-text.px-0.pb-0
          p.text-h4.mb-0.font-weight-medium.tertiary--text Good Morning, {{ username }}
          p.body-2.mb-1.mr-2 Here's the overviewof the surplus food sales and donations of WeCan platform.
        v-card-text
          v-row.pt-4
            v-col(:cols="3")
              v-card.fill-height.primary--border.rounded-xl.pa-3.primary--text.text-center(outlined @click="goToPath('/')")
                v-card.rounded-circle.lightOrange.pa-3.mx-auto.justify-center.align-center.d-flex(flat width="60" height="60")
                  v-icon.tertiary--text(large) mdi-account-search
                .text-h5.mt-1.mb-0.font-weight-medium.secondary--text {{ customersummary ? customersummary.total_customers : 0 }}
                p.font-weight-medium.font-14.mb-0 Total Customer
            v-col(:cols="3")
              v-card.fill-height.primary--border.rounded-xl.pa-3.primary--text.text-center(outlined @click="goToPath('/')")
                v-card.rounded-circle.lightOrange.pa-3.mx-auto.justify-center.align-center.d-flex(flat width="60" height="60")
                  v-icon.tertiary--text(large) mdi-cash-check
                .text-h5.mt-1.mb-0.font-weight-medium.secondary--text {{ orderSummary ? $formatCurrency(orderSummary.total_amount) : 0 }}
                p.font-weight-medium.font-14.mb-0 Total Sales
            v-col(:cols="3")
              v-card.fill-height.primary--border.rounded-xl.pa-3.primary--text.text-center(outlined @click="goToPath('/')")
                v-card.rounded-circle.lightOrange.pa-3.mx-auto.justify-center.align-center.d-flex(flat width="60" height="60")
                  v-icon.tertiary--text(large) mdi-food-variant
                .text-h5.mt-1.mb-0.font-weight-medium.secondary--text {{ orderSummary ? orderSummary.order_item_quantity : 0 }}
                p.font-weight-medium.font-14.mb-0 Total Surplus Food Saved
            v-col(:cols="3")
              v-card.fill-height.primary--border.rounded-xl.pa-3.primary--text.text-center(outlined @click="goToPath('/')")
                v-card.rounded-circle.lightOrange.pa-3.mx-auto.justify-center.align-center.d-flex(flat width="60" height="60")
                  v-icon.tertiary--text(large) mdi-hand-coin
                .text-h5.mt-1.mb-0.font-weight-medium.secondary--text {{ donationSummary ? $formatCurrency(donationSummary.overall_total_amount) : 0 }}
                p.font-weight-medium.font-14.mb-0 Total Donations
      v-col(:cols="3")
        v-img(:src="require('../../assets/img/dashboard2.png')" max-height="300" max-width="300")
</template>

<script>
import { mapGetters } from 'vuex'

export default {
  name: 'SummaryCard',
  components: { },
  data () {
    return {
      orderSummary: null,
      donationSummary: null,
      customersummary: null
    }
  },
  computed: {
    ...mapGetters({
      username: 'auth/getAuthUsername'
    })
  },
  async mounted () {
    try {
      this.orderSummary = await this.$axios.$get('/api/orders/summary/?totalorderitem=true&total=true')
      console.log('Order Summary: ', this.orderSummary)

      this.donationSummary = await this.$axios.$get('/api/donations/?total=true')
      console.log('Donation Summary: ', this.donationSummary)

      this.customersummary = await this.$axios.$get('/api/users/user/?customersummary=true')
      console.log('Customer Summary: ', this.customersummary)
    } catch (e) {
      console.log('Fail to get order summary: ', e)
    }
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

<style scoped>
.wrapped-text {
  white-space: pre-wrap;
}

.font-14 {
  font-size: 14px;
}

.primary--border{
  border: solid 1px #FA812F;
}
</style>
