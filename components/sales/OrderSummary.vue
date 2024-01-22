<template lang="pug">
v-row.justify-center.mx-auto.mt-4
    v-row.fill-width
      v-col(:cols="4" v-if="order_item_quantity")
        v-card.shadow.rounded-lg.fill-height
          v-card-title
            h3.mb-0.font-weight-medium.tertiary--text Summary
          v-card-text
            v-card.px-3.py-2.mb-3.fill-width.rounded-lg(outlined)
              .d-flex.flex-no-wrap.justify-space-between
                .d-flex.flex-column.justify-start
                  .mb-0.text-h5.pr-2.primary--text.font-weight-bold {{order_item_quantity}} Food
                  p.font-weight-medium Total Surplus Food Saved
                  p.mb-0.green--text From Dec 2023 to Jan 2024

                .d-flex.justify-end
                  v-card.rounded-circle.lightOrange.pa-3.mx-auto.justify-center.align-center.d-flex(flat width="60" height="60")
                    v-icon.tertiary--text(large) mdi-food-variant

            v-card.px-3.py-2.mb-3.fill-width.rounded-lg(outlined)
              .d-flex.flex-no-wrap.justify-space-between
                .d-flex.flex-column.justify-start
                  .mb-0.text-h5.pr-2.primary--text.font-weight-bold {{$formatCurrency(total_amount)}}
                  p.font-weight-medium Total Surplus Food Sales
                  p.mb-0.green--text From Dec 2023 to Jan 2024

                .d-flex.justify-end
                  v-card.rounded-circle.lightOrange.pa-3.mx-auto.justify-center.align-center.d-flex(flat width="60" height="60")
                    v-icon.tertiary--text(large) mdi-cash-check

            v-card.px-3.py-2.mb-3.fill-width.rounded-lg(outlined)
              .d-flex.flex-no-wrap.justify-space-between
                .d-flex.flex-column.justify-start
                  .mb-0.text-h5.pr-2.primary--text.font-weight-bold {{total_count}} Orders
                  p.font-weight-medium Total Orders
                  p.mb-0.green--text From Dec 2023 to Jan 2024

                .d-flex.justify-end
                  v-card.rounded-circle.lightOrange.pa-3.mx-auto.justify-center.align-center.d-flex(flat width="60" height="60")
                    v-icon.tertiary--text(large) mdi-file-document

      v-col(:cols="8" v-if="order_item_quantity")
        v-card.shadow.rounded-lg.fill-height
          v-card-title
            h3.mb-0.font-weight-medium.tertiary--text Surplus Food Sales Trends Over Time
          v-card-text
            ApexCharts(type="line" :options="lineChartCategory" :series="lineChartItems")

</template>

<script>
import { mapGetters } from 'vuex'
import VueApexCharts from 'vue-apexcharts'

export default {
  name: 'OrderSummary',
  components: { VueApexCharts },
  data () {
    return {
      order_item_quantity: 0,
      total_amount: 0,
      total_count: 0,
      days: [],
      total_days: [],
      lineChartItems: null,
      lineChartCategory: null
    }
  },
  computed: {
    ...mapGetters({
      username: 'auth/getAuthUsername'
    })
  },
  async mounted () {
    try {
      const response = await this.$axios.$get('/api/orders/summary/?day=true&totalorderitem=true&total=true')
      console.log('Sales summary: ', response)

      this.sortedData(response.daily_totals)

      this.order_item_quantity = response.order_item_quantity
      this.total_amount = response.total_amount
      this.total_count = response.total_count

      this.lineChartCategory = {
        chart: {
          height: 350,
          type: 'line'
        },
        stroke: {
          width: 5,
          curve: 'smooth'
        },
        xaxis: {
          type: 'datetime',
          categories: this.days,
          tickAmount: 10
        },
        fill: {
          type: 'gradient',
          gradient: {
            shade: 'dark',
            gradientFromColors: ['#FAAF08'],
            gradientToColors: ['#FAAF08'],
            shadeIntensity: 1,
            type: 'horizontal',
            opacityFrom: 1,
            opacityTo: 0.5,
            stops: [0, 100, 100, 100]
          }
        },
        yaxis: {
          min: -10,
          max: 1200
        }
      }
      this.lineChartItems = [{ name: 'Donations', data: this.total_days }]
    } catch (e) {
      console.log('Fail to get order summary: ', e)
    }
  },
  methods: {
    goToPath (link) {
      this.$router.push({
        path: link
      })
    },
    sortedData (data) {
      // Convert the object to an array of key-value pairs
      const dataArray = Object.entries(data)

      // Sort the array based on the datetime of the keys
      dataArray.sort((a, b) => new Date(a[0]) - new Date(b[0]))

      // Separate keys and values into separate arrays
      const keysArray = dataArray.map(item => item[0])
      const valuesArray = dataArray.map(item => item[1])

      this.days = keysArray
      this.total_days = valuesArray
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

.chip-small {
  height: 22px;
}
</style>
