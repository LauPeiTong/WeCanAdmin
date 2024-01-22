<template lang="pug">
v-row.justify-center.mx-auto.mt-4
    v-row.fill-width
      v-col(:cols="4" v-if="topVendors")
        v-card.shadow.rounded-lg
          v-card-title
            h3.mb-0.font-weight-medium.tertiary--text Top 3 Vendors
          v-card-text
            template(v-for="v in topVendors")
              v-card.px-3.py-2.mb-3.fill-width.rounded-lg(outlined)
                .d-flex.flex-no-wrap.justify-space-between
                  .company
                    .d-flex.flex-column.justify-start
                      p.mb-0.text-h6.pr-2 {{v.display_name}}
                      .d-flex.flex-wrap
                        template(v-for="t in v.tags")
                          v-chip.chip-small.mb-1.mr-2(
                            :color="$vuetify.theme.themes.light.tertiary"
                            outlined
                            pill
                          )
                            p.mb-0.caption {{t}}
                    p.mb-0 Food Sales: {{$formatCurrency(v.total_sales)}}
                    p.mb-0.green--text From Dec 2023 to Jan 2024

                  v-avatar.shadow.rounded-lg(
                    class="ma-1"
                    size="60"
                    tile
                  )
                    v-img(v-if="v.image_url == '' || v.image_url == null" :src="require('../../assets/company/1.png')" :alt="v.display_name")
                    v-img(v-else :src="v.image_url" :alt="v.display_name")

      v-col(:cols="4" v-if="topCities")
        v-card.shadow.rounded-lg.fill-height
          v-card-title
            h3.mb-0.font-weight-medium.tertiary--text City with Highest Sales
          v-card-text
            template(v-for="v in topCities")
              v-card.px-3.py-2.mb-3.fill-width.rounded-lg(outlined)
                .d-flex.flex-column.justify-start
                  p.mb-0.text-h6.pr-2 {{v.city}}
                  p.mb-0 Food Sales: {{$formatCurrency(v.total_sales)}}
                  p.mb-0.green--text From Dec 2023 to Jan 2024

      v-col(:cols="4" v-if="topCities")
        v-card.shadow.rounded-lg.fill-height
          v-card-title
            h3.mb-0.font-weight-medium.tertiary--text No of Vendor in each City
          v-card-text
            ApexCharts(type="donut" :options="donutChartOptions" :series="numVendors")

</template>

<script>
import { mapGetters } from 'vuex'
import VueApexCharts from 'vue-apexcharts'

export default {
  name: 'VendorSummary',
  components: { VueApexCharts },
  data () {
    return {
      topVendors: null,
      topCities: null,
      vendorsByCity: null,
      numVendors: [],
      donutChartOptions: null
    }
  },
  computed: {
    ...mapGetters({
      username: 'auth/getAuthUsername'
    })
  },
  async mounted () {
    try {
      const response = await this.$axios.$get('/api/users/user/?vendorsummary=true')

      this.topVendors = response.top_vendors
      this.topCities = response.top_cities
      this.vendorsByCity = response.vendors_by_city
      console.log('Top vendors: ', this.topVendors)
      console.log('Top cities: ', this.topCities)
      console.log('Vendor by city: ', this.vendorsByCity)

      const cities = this.vendorsByCity.map(item => item.city)
      this.numVendors = this.vendorsByCity.map(item => item.num_vendors)

      this.donutChartOptions = {
        chart: {
          type: 'donut'
        },
        legend: {
          show: true
        },
        labels: cities,
        colors: [
          '#ffcc00', // Orange
          '#ffba00', // Slightly lighter orange
          '#ffa800', // Lighter orange
          '#ff9600', // Medium orange
          '#ff8400', // Slightly darker orange
          '#ff7200', // Darker orange
          '#ff6000', // Another shade between orange and red
          '#ff4e00', // Another shade between orange and red
          '#ff3c00', // Another shade between orange and red
          '#ff2a00', // Another shade between orange and red
          '#ff1800', // Another shade between orange and red
          '#00aa66', // Smooth transition to green
          '#008855', // Slightly darker green
          '#006644', // Darker green
          '#004433' // Darkest green
        ],
        plotOptions: {
          pie: {
            expandOnClick: true,
            donut: {
              labels: {
                show: true,
                total: {
                  showAlways: true,
                  show: true,
                  color: '#333333',
                  fontSize: '20px',
                  fontWeight: '600',
                  formatter: function (value) {
                    const t = value.globals.series.reduce((a, b) => a + b, 0)
                    return t.toString() + ' City'
                  }
                },
                value: {
                  color: '#333333',
                  fontSize: '25px',
                  fontWeight: '600'
                }
              }
            }
          }
        },
        responsive: [{
          breakpoint: 480,
          options: {
            chart: {
              width: 600
            },
            legend: {
              position: 'bottom'
            }
          }
        }],
        dataLabels: {
          enabled: false
        }
      }
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

.chip-small {
  height: 22px;
}
</style>
