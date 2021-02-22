<template>
  <q-layout view="lHh Lpr lFf">
    <h1> Hello </h1>
    <div id="q-app">
      <div class="q-pa-md">
        <q-table
          title="Treats"
          selection="single"
          :selected.sync="selected"
          :data="data"
          :columns="columns"
          row-key="id"
          :pagination.sync="pagination"
          :loading="loading"
          :filter="filter"
          @request="onRequest"
          binary-state-sort
        >
          <template v-slot:top-right>
            <q-input borderless dense debounce="300" v-model="filter" placeholder="Search">
              <template v-slot:append>
                <q-icon name="search"></q-icon>
              </template>
            </q-input>
          </template>

        </q-table>
      </div>
    </div>
  </q-layout>
</template>

<script>
// import EssentialLink from 'components/EssentialLink.vue'

const linksData = [
]

export default {
  name: 'MainLayout',
  // components: { EssentialLink },
  data () {
    return {
      selected: [],
      leftDrawerOpen: false,
      essentialLinks: linksData,
      columns: [
        {
          name: 'name',
          required: true,
          label: 'name',
          align: 'left',
          field: row => row.name,
          format: val => `${val}`,
          sortable: true
        },
        { name: 'x', label: 'x', field: 'x', sortable: true },
        { name: 'y', label: 'y', field: 'y', sortable: true },
        { name: 'creationdate', label: 'creationdate', field: 'creationdate', sortable: true },
        { name: 'manufacturecost', label: 'manufacturecost', field: 'manufacturecost', sortable: true },
        { name: 'price', label: 'price', field: 'price', sortable: true },
        { name: 'unitofmeasure', label: 'unitofmeasure', field: 'unitofmeasure', sortable: true },
        { name: 'eyecolor', label: 'eyecolor', field: 'eyecolor', sortable: true },
        { name: 'location_name', label: 'location_name', field: 'location_name', sortable: true },
        { name: 'location_x', label: 'location_x', field: 'location_x', sortable: true },
        { name: 'location_y', label: 'location_y', field: 'location_y', sortable: true },
        { name: 'nationality', label: 'nationality', field: 'nationality', sortable: true },
        { name: 'owner_name', label: 'owner_name', field: 'owner_name', sortable: true },
        { name: 'passportid', label: 'passportid', field: 'passportid', sortable: true }
      ],
      data: [
        {
          id: 2,
          name: 'name',
          x: 237,
          y: 9.0,
          creationdate: '37',
          manufacturecost: 4,
          price: 129,
          unitofmeasure: 'KILOGRAMS',
          eyecolor: 'BROWN',
          location_name: 'asd',
          location_x: 1,
          location_y: 2,
          nationality: 'RUSSIA',
          owner_name: 'asd',
          passportid: 2
        }
      ]
    }
  },
  mounted () {
    // get initial data from server (1st page)
    this.onRequest({
      pagination: this.pagination,
      filter: undefined
    })
  },
  methods: {
    onRequest (props) {
      const { page, rowsPerPage, sortBy, descending } = props.pagination
      const filter = props.filter

      this.loading = true

      // emulate server
      setTimeout(() => {
        // update rowsCount with appropriate value
        this.pagination.rowsNumber = this.getRowsNumberCount(filter)

        // get all rows if "All" (0) is selected
        const fetchCount = rowsPerPage === 0 ? this.pagination.rowsNumber : rowsPerPage

        // calculate starting row of data
        const startRow = (page - 1) * rowsPerPage

        // fetch data from "server"
        const returnedData = this.fetchFromServer(startRow, fetchCount, filter, sortBy, descending)

        // clear out existing data and add new
        this.data.splice(0, this.data.length, ...returnedData)

        // don't forget to update local pagination object
        this.pagination.page = page
        this.pagination.rowsPerPage = rowsPerPage
        this.pagination.sortBy = sortBy
        this.pagination.descending = descending

        // ...and turn of loading indicator
        this.loading = false
      }, 1500)
    },

    // emulate ajax call
    // SELECT * FROM ... WHERE...LIMIT...
    fetchFromServer (startRow, count, filter, sortBy, descending) {
      const data = filter
        ? this.original.filter(row => row.name.includes(filter))
        : this.original.slice()

      // handle sortBy
      if (sortBy) {
        const sortFn = sortBy === 'desc'
          ? (descending
            ? (a, b) => (a.name > b.name ? -1 : a.name < b.name ? 1 : 0)
            : (a, b) => (a.name > b.name ? 1 : a.name < b.name ? -1 : 0)
          )
          : (descending
            ? (a, b) => (parseFloat(b[sortBy]) - parseFloat(a[sortBy]))
            : (a, b) => (parseFloat(a[sortBy]) - parseFloat(b[sortBy]))
          )
        data.sort(sortFn)
      }

      return data.slice(startRow, startRow + count)
    },

    // emulate 'SELECT count(*) FROM ...WHERE...'
    getRowsNumberCount (filter) {
      if (!filter) {
        return this.original.length
      }
      let count = 0
      this.original.forEach((treat) => {
        if (treat.name.includes(filter)) {
          ++count
        }
      })
      return count
    }
  }
}
</script>
