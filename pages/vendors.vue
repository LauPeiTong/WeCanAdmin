<template lang="pug">
.vendor-page.pa-0.ma-0.fill-width
  upper-title.ma-0(:title="'Vendors'" :icon="'bell'" :rightIconColor="$vuetify.theme.themes.light.primary")
  v-row.ma-0.pt-14.fill-width.px-2
    v-col.pb-6(cols="12")
      vendor-summary
    v-col(cols="12")
      vendor-list-vue
    //- v-col(cols="4")
      v-card.fill-height.shadow.pa-3.pb-6.rounded-lg(elevation="0")
        p.font-weight-medium Number of Approved Loans
        ApexCharts(type="donut" :options="donutChartOptions" :series="series")
</template>

<script>
import { mapGetters } from 'vuex'
import VendorSummary from '../components/vendors/VendorSummary.vue'
import VendorListVue from '../components/vendors/VendorList.vue'

export default {
  name: 'VendorPage',
  components: {
    VendorSummary,
    VendorListVue
  },
  layout: 'default',
  data () {
    return {
      series: [20, 50, 100],
      donutChartOptions: {
        chart: {
          type: 'donut'
        },
        legend: {
          show: true
        },
        labels: ['Rejected', 'Processing', 'Approved'],
        colors: ['#BB0000', '#848484', '#002147'],
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
                    return t.toString() + ' loan'
                  }
                },
                value: {
                  color: '#0053B3',
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
              width: 300
            },
            legend: {
              position: 'bottom'
            }
          }
        }],
        dataLabels: {
          enabled: false
        }
      },
      pieChartOptions: {
        labels: ['Penang', 'Kuala Lumpur', 'Johor', 'Selangor', 'Other States'],
        colors: ['#002147', '#004594', '#9197B8', '#848484', '#BB0000'],
        responsive: [
          {
            breakpoint: 480,
            options: {
              chart: {
                width: 200
              },
              legend: {
                position: 'bottom'
              }
            }
          }
        ]
      },
      series1: [40, 20, 25, 12, 3],
      barChartOptions: {
        // Add your chart options here
        chart: {
          id: 'apexchart-example'
        },
        colors: ['#002147', '#004594', '#9197B8'],
        xaxis: {
          categories: ['Type of Loans']
        },
        responsive: [
          {
            breakpoint: 480,
            options: {
              chart: {
                width: 200
              },
              legend: {
                position: 'bottom'
              }
            }
          }
        ]
      },
      series2: [{
        name: 'Personal Loans',
        data: [100]
      },
      {
        name: 'Business Loans',
        data: [20]
      },
      {
        name: 'Mortgage',
        data: [20]
      }]
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
