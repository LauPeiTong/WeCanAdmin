<template lang="pug">
v-row.justify-center.mx-auto.mt-4
    v-row.fill-width
      v-col(:cols="6" v-if="overall_total_amount")
        v-card.shadow.rounded-lg.fill-height
          v-card-title
            h3.mb-0.font-weight-medium.tertiary--text Donation Trends Over Time
          v-card-text
            ApexCharts(type="line" :options="lineChartCategory" :series="lineChartItems")
      v-col(:cols="6" v-if="overall_total_amount")
        v-card.shadow.rounded-lg.fill-height
          v-card-title
            h3.mb-0.font-weight-medium.tertiary--text Total Donations based on Type
          v-card-text
            ApexCharts(type="donut" :options="donutChartOptions" :series="total_type" height="300")

</template>

<script>
import { mapGetters } from 'vuex'
import VueApexCharts from 'vue-apexcharts'

export default {
  name: 'DonationSummary',
  components: { VueApexCharts },
  data () {
    return {
      overall_total_amount: 0,
      total_type: [],
      days: [],
      total_days: [],
      lineChartItems: null,
      lineChartCategory: null,
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
      const response = await this.$axios.$get('/api/donations/?total=true&day=true')
      console.log('Donations summary: ', response)

      this.sortedData(response.daily_totals)

      this.overall_total_amount = response.overall_total_amount
      this.total_type = [response.points_total_amount, response.round_up_total_amount]

      this.donutChartOptions = {
        chart: {
          type: 'donut'
        },
        legend: {
          show: true
        },
        labels: ['Points', 'Round-up'],
        colors: [
          '#FAAF08', // Orange
          '#FA812F'
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
                    return 'RM' + t.toString()
                  }
                },
                value: {
                  color: '#333333',
                  fontSize: '25px',
                  fontWeight: '600',
                  formatter: function (value) {
                    return 'RM' + value.toString()
                  }
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
          max: 300
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
