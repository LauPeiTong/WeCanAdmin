<template lang="pug">
v-data-table.mt-18(
  :headers="headers"
  :items="vendors"
  multi-sort
  @click:row="onRowClick"
  v-if="vendors"
)

    template(v-slot:body.prepend)
      tr
        td.py-4
          v-text-field(v-model="id" type="number" label="ID" hide-details="auto" dense outlined)
        td.py-4
          v-text-field(v-model="display_name" type="text" label="Vendor Name" hide-details="auto" dense outlined)
        td.py-4
          v-text-field(v-model="address" type="text" label="Address" hide-details="auto" dense outlined)
        td.py-4
          v-text-field(v-model="rating" type="number" label="More than" hide-details="auto" dense outlined)
        td.py-4
          v-select.select-category(:items="categoryList" label="Select a category" v-model="category" hide-details="auto" multiple chips dense outlined)
            template(v-slot:selection="{ item, index }")
              v-chip(small :color="getChipColor(item)" :textColor="getColor(item)")
                span {{ item }}
        td.py-4
          v-select.select-category(:items="tagList" label="Select a category" v-model="tags" hide-details="auto" multiple chips dense outlined)
            template(v-slot:selection="{ item, index }")
              v-chip(small :color="getChipColor(item)" :textColor="getColor(item)")
                span {{ item }}
        td.py-4.action-field
        td.py-4.action-field

    template(v-slot:item.id="{ item }")
      p.mb-0 {{ '#' + item.id }}

    template(v-slot:item.display_name="{ item }")
      .d-flex.align-center
        v-avatar.profile-pic(size="24")
          img(v-if="item.image_url == '' || item.image_url == null" :src="require('../../assets/employee/1.png')" :alt="item.display_name")
          img(v-else :src="item.image_url" :alt="item.display_name")
        span.ml-2.body-2 {{$strLimit(item.display_name, 40)}}

    template(v-slot:item.address="{ item }")
      p.mb-0 {{$strLimit(item.address, 40)}}

    template(v-slot:item.rating="{ item }")
      .d-flex.align-center.justify-center
        img.ml-1(width="14" height="14" :src="require(`../../assets/img/star.jpg`)")
        span.ml-2.body-2 {{ item.rating }}

    template(v-slot:item.category="{ item }")
      v-chip.my-1(
        :color="getChipColor(item.category)"
        :textColor="getColor(item.category)"
        pill
      )
        p.mb-0 {{ item.category }}

    template(v-slot:item.tags="{ item }")
      .d-flex.align-center
        template(v-for="t in item.tags")
          v-chip.my-1(
            :color="getChipColor(t)"
            :textColor="getColor(t)"
            pill
          )
            p.mb-0 {{ t }}

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
      vendors: null,
      loading: false,
      id: '',
      display_name: '',
      address: '',
      rating: '',
      category: [],
      categoryList: ['All', 'Restaurant', 'Supermarket', 'Grocery', 'Bakery'],
      colors: [
        { name: 'All', color: '#CD6200', background: '#FEF2E5' },
        { name: 'Restaurant', color: '#CD6200', background: '#FEF2E5' },
        { name: 'Supermarket', color: '#1F9254', background: '#EBF9F1' },
        { name: 'Grocery', color: '#1F9254', background: '#EBF9F1' },
        { name: 'Bakery', color: '#BB0000', background: '#FBE7E8' },
        { name: 'Halal', color: '#CD6200', background: '#FEF2E5' },
        { name: 'Western', color: '#CD6200', background: '#FEF2E5' },
        { name: 'Chinese', color: '#1F9254', background: '#EBF9F1' },
        { name: 'Menu Rahmah', color: '#1F9254', background: '#EBF9F1' },
        { name: 'Free Delivery', color: '#BB0000', background: '#FBE7E8' }
      ],
      tags: [],
      tagList: ['Halal', 'Western', 'Chinese', 'Menu Rahmah', 'Free Delivery'],
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
          value: 'display_name',
          filter: (f) => { return (f + '').toLowerCase().includes(this.display_name.toLowerCase()) }
        },
        {
          text: 'Address',
          align: 'start',
          value: 'address',
          filter: (f) => { return (f + '').toLowerCase().includes(this.address.toLowerCase()) }
        },
        {
          text: 'Rating',
          align: 'center',
          value: 'rating',
          filter: (value) => {
            if (!this.rating) { return true }
            return value > parseInt(this.rating)
          }
        },
        {
          text: 'Category',
          align: 'center',
          sortable: false,
          value: 'category',
          filter: (f) => {
            if (f !== '') {
              if (this.category.length === 0 || this.category.includes('All')) {
                return true
              }
              const result = this.category.filter(value => f.includes(value))
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
          text: 'Tags',
          align: 'center',
          sortable: false,
          value: 'tags',
          filter: (f) => {
            if (f !== '') {
              if (this.tags.length === 0 || this.tags.includes('All')) {
                return true
              }
              const result = this.tags.filter(value => f.includes(value))
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
    async loadData () {
      // Fetch data from the server based on the current page and items per page
      this.loading = true
      try {
        // Call your API to fetch data
        const response = await this.$axios.$get(
          '/api/users/user/?vendors=true'
        )
        this.vendors = await response.vendors ?? null
        this.totalItems = response.count

        console.log('Vendor list: ', this.vendors)
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
