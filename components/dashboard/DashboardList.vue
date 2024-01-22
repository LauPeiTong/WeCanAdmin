<template lang="pug">
.borrowerList
  v-tabs(
    v-model="tab"
    align-with-title
  )
    v-tabs-slider(color="primary")
    v-tab.rounded-t-xl.text-capitalize(
      v-for="t in tabs"
      :key="t"
      @click="changeList(t)"
    ) {{ t }}

  v-card.shadow.pa-4.rounded-b-lg.rounded-tr-lg(elevation="1")
    v-tabs-items(v-model="tab")
      v-tab-item(
        v-for="t in tabs"
        :key="t"
      )
        p.font-weight-medium {{t}} List
        order-table-vue(v-if="t == 'Orders'")
        vendor-table-vue(v-if="t == 'Vendors'")
        customer-table-vue(v-if="t == 'Customers'")
        donation-table-vue(v-if="t == 'Donations'")
</template>

<script>
import { mapGetters, mapActions } from 'vuex'
import CustomerTableVue from './CustomerTable.vue'
import DonationTableVue from './DonationTable.vue'
import OrderTableVue from './OrderTable.vue'
import VendorTableVue from './VendorTable.vue'

export default {
  name: 'TableLayout',
  components: {
    OrderTableVue,
    VendorTableVue,
    CustomerTableVue,
    DonationTableVue
  },
  data () {
    return {
      tab: null,
      tabs: ['Orders', 'Donations', 'Customers', 'Vendors']
    }
  },
  computed: {
    ...mapGetters({
    })
  },
  methods: {
    ...mapActions({
    }),
    changeList (item) {
      console.log(item)
    },
    getColor (status) {
      const result = this.colors.find((c) => { return c.name === status })
      if (result) {
        return result.color
      } else {
        return this.$vuetify.theme.themes.primary
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
  min-width: 200px;
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
