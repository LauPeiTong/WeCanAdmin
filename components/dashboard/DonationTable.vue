<template lang="pug">
v-data-table.mt-18(
  :headers="headers"
  :items="donations"
  multi-sort
  @click:row="onRowClick"
  :items-per-page="perPage"
  :page="currentPage"
  :server-items-length="totalItems"
  @update:page="loadData"
  v-if="donations"
)

    template(v-slot:body.prepend)
      tr
        td.py-4
          v-text-field(v-model="id" type="number" label="ID" hide-details="auto" dense outlined)
        td.py-4
          v-text-field(v-model="customer" type="text" label="Customer Name" hide-details="auto" dense outlined)
        td.py-4
          v-text-field(v-model="organization_name" type="text" label="Organization Name" hide-details="auto" dense outlined)
        td.py-4
          v-text-field(v-model="amount" type="number" label="More than" hide-details="auto" dense outlined)
        td.py-4
          v-select.select-category(:items="typeList" label="Select a type" v-model="type" hide-details="auto" multiple chips dense outlined)
            template(v-slot:selection="{ item, index }")
              v-chip(small :color="getChipColor(item)" :textColor="getColor(item)")
                span {{ item }}
        td.py-4
          v-text-field(v-model="created_at" type="text" label="Created At" hide-details="auto" dense outlined)
        td.py-4.action-field
        td.py-4.action-field

    template(v-slot:item.id="{ item }")
      p.mb-0 {{ '#' + item.id }}

    template(v-slot:item.customer="{ item }")
      .d-flex.align-center
        v-avatar.profile-pic(size="24")
          img(v-if="item.customer.image_url == '' || item.customer.image_url == null" :src="require('../../assets/employee/1.png')" :alt="item.customer.username")
          img(v-else :src="item.customer.image_url" :alt="item.customer.username")
        span.ml-2.body-2 {{item.customer.username}}

    template(v-slot:item.organization_name="{ item }")
      p.mb-0 {{$strLimit(item.organization_name, 40)}}

    template(v-slot:item.amount="{ item }")
      p.mb-0 {{ $formatCurrency(item.amount) }}

    template(v-slot:item.type="{ item }")
      v-chip.my-1(
        :color="getChipColor(item.type)"
        :textColor="getColor(item.type)"
        pill
      )
        p.mb-0 {{ item.type }}

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
      donations: null,
      loading: false,
      perPage: 10,
      currentPage: 1,
      totalItems: 0,
      id: '',
      organization_name: '',
      customer: '',
      delivery_or_pickup: '',
      amount: '',
      type: '',
      purpose: '',
      created_at: '',
      typeList: ['Round-up', 'Points'],
      colors: [
        { name: 'Round-up', color: '#CD6200', background: '#FEF2E5' },
        { name: 'Points', color: '#1F9254', background: '#EBF9F1' }
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
          text: 'Customer Name',
          align: 'start',
          value: 'customer',
          filter: (f) => { return (f.username + '').toLowerCase().includes(this.customer.toLowerCase()) }
        },
        {
          text: 'Organization Name',
          align: 'start',
          value: 'organization_name',
          filter: (f) => { return (f + '').toLowerCase().includes(this.organization_name.toLowerCase()) }
        },
        {
          text: 'Amount',
          align: 'end',
          value: 'amount',
          filter: (value) => {
            if (!this.amount) { return true }
            return value > parseInt(this.amount)
          }
        },
        {
          text: 'Type',
          align: 'center',
          sortable: false,
          value: 'type',
          filter: (f) => {
            if (f !== '') {
              if (this.type.length === 0 || this.type.includes('All')) {
                return true
              }
              const result = this.type.filter(value => f.includes(value))
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
          `/api/donations/?page=${this.currentPage}&per_page=${this.perPage}`
        )
        this.donations = await response.donations?.results
        this.totalItems = response.donations?.count

        console.log('Donations list: ', this.donations)
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
