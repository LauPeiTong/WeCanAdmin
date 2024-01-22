<template lang="pug">
v-data-table.mt-18(
  :headers="headers"
  :items="orders"
  multi-sort
  @click:row="onRowClick"
  :items-per-page="perPage"
  :page="currentPage"
  :server-items-length="totalItems"
  @update:page="loadData"
  v-if="orders"
)

    template(v-slot:body.prepend)
      tr
        td.py-4
          v-text-field(v-model="id" type="number" label="ID" hide-details="auto" dense outlined)
        td.py-4
          v-text-field(v-model="vendor" type="text" label="Vendor Name" hide-details="auto" dense outlined)
        td.py-4
          v-text-field(v-model="customer" type="text" label="Customer Name" hide-details="auto" dense outlined)
        td.py-4
          v-text-field(v-model="total_price" type="number" label="More than" hide-details="auto" dense outlined)
        td.py-4
          v-select.select-category(:items="statusList" label="Select a status" v-model="status" hide-details="auto" multiple chips dense outlined)
            template(v-slot:selection="{ item, index }")
              v-chip(small :color="getChipColor(item)" :textColor="getColor(item)")
                span {{ item }}
        td.py-4
          v-text-field(v-model="created_at" type="text" label="Created At" hide-details="auto" dense outlined)
        td.py-4
          v-text-field(v-model="delivery_or_pickup" type="text" label="Method" hide-details="auto" dense outlined)
        td.py-4.action-field
        td.py-4.action-field

    template(v-slot:item.id="{ item }")
      p.mb-0 {{ '#' + item.id }}

    template(v-slot:item.vendor="{ item }")
      .d-flex.align-center
        v-avatar.profile-pic(size="24")
          img(v-if="item.vendor.image_url == '' || item.vendor.image_url == null" :src="require('../../assets/employee/1.png')" :alt="item.vendor.display_name")
          img(v-else :src="item.vendor.image_url" :alt="item.vendor.display_name")
        span.ml-2.body-2 {{$strLimit(item.vendor.display_name, 40)}}

    template(v-slot:item.customer="{ item }")
      .d-flex.align-center
        v-avatar.profile-pic(size="24")
          img(v-if="item.customer.image_url == '' || item.customer.image_url == null" :src="require('../../assets/employee/1.png')" :alt="item.customer.username")
          img(v-else :src="item.customer.image_url" :alt="item.customer.username")
        span.ml-2.body-2 {{item.customer.username}}

    template(v-slot:item.total_price="{ item }")
      p.mb-0 {{ $formatCurrency(item.total_price) }}

    template(v-slot:item.status="{ item }")
      v-chip.my-1(
        :color="getChipColor(item.status)"
        :textColor="getColor(item.status)"
        pill
      )
        p.mb-0 {{ item.status }}

    template(v-slot:item.delivery_or_pickup="{ item }")
      span.ml-2.body-2 {{item.delivery_or_pickup}}

    template(v-slot:item.created_at="{ item }")
      p.mb-0 {{ formatDate(new Date(item.created_at)) }}

    //- Placeholder
    template(v-slot:item.actions="{ item }")
      .d-flex.align-center.mt-4
        v-icon.white--text.mr-2 mdi-magnify
        v-icon.white--text mdi-pencil-box-outline

    template(v-slot:item.actions="{ item }")
      .d-flex.align-center.mt-3
        v-icon.primary--text.mr-2(@click="onRowClick(item)") mdi-magnify
        v-icon.primary--text(@click="") mdi-pencil-box-outline
</template>

<script>
import { mapGetters, mapActions } from 'vuex'

export default {
  name: 'TableLayout',
  components: {
  },
  data () {
    return {
      orders: null,
      loading: false,
      perPage: 10,
      currentPage: 1,
      totalItems: 0,
      id: '',
      vendor: '',
      customer: '',
      delivery_or_pickup: '',
      total_price: '',
      status: '',
      purpose: '',
      created_at: '',
      statusList: ['Pending', 'Processing', 'To Receive', 'Completed', 'Cancelled'],
      colors: [
        { name: 'Pending', color: '#CD6200', background: '#FEF2E5' },
        { name: 'Processing', color: '#CD6200', background: '#FEF2E5' },
        { name: 'Completed', color: '#1F9254', background: '#EBF9F1' },
        { name: 'To Receive', color: '#1F9254', background: '#EBF9F1' },
        { name: 'Cancelled', color: '#BB0000', background: '#FBE7E8' }
      ],
      headers: [
        {
          text: 'ID',
          align: 'start',
          value: 'id',
          filter: (value) => {
            if (!this.id) { return true }
            return value === parseInt(this.id)
          }
        },
        {
          text: 'Vendor Name',
          align: 'start',
          value: 'vendor',
          filter: (f) => { return (f.display_name + '').toLowerCase().includes(this.vendor.toLowerCase()) }
        },
        {
          text: 'Customer Name',
          align: 'start',
          value: 'customer',
          filter: (f) => { return (f.username + '').toLowerCase().includes(this.customer.toLowerCase()) }
        },
        {
          text: 'Total Price',
          align: 'end',
          value: 'total_price',
          filter: (value) => {
            if (!this.total_price) { return true }
            return value > parseInt(this.total_price)
          }
        },
        {
          text: 'Status',
          align: 'center',
          sortable: false,
          value: 'status',
          filter: (f) => {
            if (f !== '') {
              if (this.status.length === 0 || this.status.includes('All')) {
                return true
              }
              const result = this.status.filter(value => f.includes(value))
              if (result.length > 0) {
                return true
              } else {
                return false
              }
            } else {
              return false
            }
          }
        },
        {
          text: 'Created At',
          align: 'center',
          value: 'created_at',
          filter: (value) => {
            if (!this.created_at) { return true }
            return value > parseInt(this.created_at)
          }
        },
        {
          text: 'Method',
          align: 'start',
          value: 'delivery_or_pickup',
          filter: (f) => { return (f + '').toLowerCase().includes(this.delivery_or_pickup.toLowerCase()) }
        },
        { text: '', align: 'center', sortable: false, value: 'actions' },
        { text: 'Action', align: 'center', sortable: false, value: 'actions' }
      ]
    }
  },
  computed: {
    ...mapGetters({
    })
  },
  mounted () {
    this.loadData(1)
  },
  methods: {
    ...mapActions({
    }),
    formatDate (date) {
      const todayFormat = 'HH:mm a'
      const otherDayFormat = 'dd MMM yyyy, HH:mm a'

      const formattedDate = this.$dateFns.isToday(date)
        ? 'Today, ' + this.$dateFns.format(date, todayFormat)
        : this.$dateFns.format(date, otherDayFormat)

      return formattedDate
    },
    getColor (status) {
      const result = this.colors.find((c) => { return c.name === status })
      if (result) {
        return result.color
      } else {
        return this.$vuetify.theme.themes.primary
      }
    },
    getChipColor (status) {
      const result = this.colors.find((c) => { return c.name === status })
      if (result) {
        return result.background
      } else {
        return this.$vuetify.theme.themes.background
      }
    },
    onRowClick (item) {
    },
    async loadData (newPage) {
      // Fetch data from the server based on the current page and items per page
      this.currentPage = newPage
      this.loading = true
      try {
        // Call your API to fetch data
        const response = await this.$axios.$get(
          `/api/orders/?page=${this.currentPage}&per_page=${this.perPage}`
        )
        this.orders = await response.results ?? null
        this.totalItems = response.count

        console.log('Order list: ', this.orders)
      } catch (error) {
        console.error('Error fetching data:', error)
      } finally {
        this.loading = false
      }
    }
  }
}
</script>
<style scoped>
:deep(.v-tabs .v-tabs-bar) {
  background-color: transparent;
}

.v-tab {
  margin: 0 !important;
  font-size: 12px;
}

.v-tab:not(.v-tab--active) {
  border: solid 4px white;
  border-bottom-style: none;
  background-color: #fff4df;
  color: #FAAF08;
  padding: 0px 12px;
  margin-top: 8px !important;
}

.v-tab--active {
  background-color: #FAAF08;
  color: white;
  padding: 0px 16px;
  margin-top: 4px !important;
  box-shadow: 0px 0px 8px 0px rgba(0, 0, 0, 0.30) !important;
}

:deep(thead)  {
  background-color: #fff4df;
}

:deep(.v-data-table)  {
  overflow: auto !important;
  max-width: 100% !important;
}

:deep(.v-data-table td:nth-child(1)) {
  min-width: 100px;
}

:deep(.v-data-table td:nth-child(2)) {
  min-width: 220px;
}

:deep(.v-data-table td:nth-child(3)) {
  min-width: 200px;
}

:deep(.v-data-table td:nth-child(4)) {
  min-width: 200px;
}

:deep(.v-data-table td:nth-child(5)) {
  min-width: 200px;
}

:deep(.v-data-table td:nth-child(6)) {
  min-width: 300px;
}

:deep(.v-data-table td:nth-child(7)) {
  min-width: 150px !important;
}

:deep(.v-data-table td:last-child) {
  background-color: white;
  position: absolute !important;
  right: 0%;
  width: 88px;
  box-shadow: 0px 0px 2px 0px rgba(0, 0, 0, 0.12) !important;
}

:deep(thead th:last-child)  {
  background-color: #fff4df;
  position: absolute !important;
  right: 0%;
  width: 88px;
  box-shadow: 0px 0px 2px 0px rgba(0, 0, 0, 0.12) !important;
  padding-top: 12px !important;
}

:deep(.v-data-table td:nth-last-child(2),
thead th:nth-last-child(2)) {
  min-width: 88px;
}

.action-field {
  height: 20% !important;
  width: 88px;
}

:deep(.select-category .v-chip .v-chip__content) {
  font-size: 12px !important;
}

:deep(.select-category .v-chip .v-chip__content) {
  font-size: 12px !important;
}
</style>
