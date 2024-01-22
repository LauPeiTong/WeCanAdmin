<template lang="pug">
v-data-table.mt-18(
  :headers="headers"
  :items="customers"
  multi-sort
  @click:row="onRowClick"
  v-if="customers"
)

    template(v-slot:body.prepend)
      tr
        td.py-4
          v-text-field(v-model="id" type="number" label="ID" hide-details="auto" dense outlined)
        td.py-4
          v-text-field(v-model="username" type="text" label="Vendor Name" hide-details="auto" dense outlined)
        td.py-4
          v-text-field(v-model="points" type="number" label="More than" hide-details="auto" dense outlined)
        td.py-4
          v-text-field(v-model="email" type="text" label="Email" hide-details="auto" dense outlined)
        td.py-4
          v-text-field(v-model="phone" type="text" label="Email" hide-details="auto" dense outlined)
        td.py-4
          v-text-field(v-model="address" type="text" label="Address" hide-details="auto" dense outlined)

        td.py-4.action-field
        td.py-4.action-field

    template(v-slot:item.id="{ item }")
      p.mb-0 {{ '#' + item.id }}

    template(v-slot:item.username="{ item }")
      .d-flex.align-center
        v-avatar.profile-pic(size="24")
          img(v-if="item.image_url == '' || item.image_url == null" :src="require('../../assets/employee/1.png')" :alt="item.username")
          img(v-else :src="item.image_url" :alt="item.username")
        span.ml-2.body-2.text-capitalize {{$strLimit(item.username, 40)}}

    template(v-slot:item.points="{ item }")
      p.mb-0.primary--text.font-weight.medium {{ item.points }}

    template(v-slot:item.email="{ item }")
      p.mb-0 {{$strLimit(item.email, 40)}}

    template(v-slot:item.phone="{ item }")
      p.mb-0 {{$strLimit(item.phone, 40)}}

    template(v-slot:item.address="{ item }")
      p.mb-0 {{$strLimit(item.address, 40)}}

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
      customers: null,
      loading: false,
      id: '',
      username: '',
      address: '',
      points: '',
      email: '',
      phone: '',
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
          value: 'username',
          filter: (f) => { return (f + '').toLowerCase().includes(this.username.toLowerCase()) }
        },
        {
          text: 'Points',
          align: 'center',
          value: 'points',
          filter: (value) => {
            if (!this.points) { return true }
            return value > parseInt(this.points)
          }
        },
        {
          text: 'Email',
          align: 'start',
          value: 'email',
          filter: (f) => { return (f + '').toLowerCase().includes(this.email.toLowerCase()) }
        },
        {
          text: 'Phone',
          align: 'start',
          value: 'phone',
          filter: (f) => { return (f + '').toLowerCase().includes(this.phone.toLowerCase()) }
        },
        {
          text: 'Address',
          align: 'start',
          value: 'address',
          filter: (f) => { return (f + '').toLowerCase().includes(this.address.toLowerCase()) }
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
    this.loadData()
  },
  methods: {
    ...mapActions({
    }),
    onRowClick (item) {
    },
    async loadData () {
      // Fetch data from the server based on the current page and items per page
      this.loading = true
      try {
        // Call your API to fetch data
        const response = await this.$axios.$get(
          '/api/users/user/?customers=true'
        )
        this.customers = await response.customers ?? null
        this.totalItems = response.count

        console.log('Customer list: ', this.customers)
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
