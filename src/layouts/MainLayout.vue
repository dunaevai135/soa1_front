<template>
  <q-layout view="lHh Lpr lFf">
    <h1> Hello </h1>
    <q-page-container>
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
              <div v-if="$q.screen.gt.xs" class="q-pa-md q-gutter-sm">
                <q-btn color="dark" label="Create" @click="create = true"/>
                <q-btn color="secondary" label="Edit" @click="edited = selected[0]; edit = true" :disabled="(selected.length == 0)"/>
                <q-btn color="secondary" label="Delete" :disabled="(selected.length == 0)"/>
              </div>
              <q-input borderless dense debounce="300" v-model="filter" placeholder="Search">
                <template v-slot:append>
                  <q-icon name="search"></q-icon>
                </template>
              </q-input>
            </template>

          </q-table>
<!--          <div class="q-mt-md">-->
<!--            Selected: {{ JSON.stringify(selected) }}-->
<!--          </div>-->
        </div>
      </div>

      <q-dialog v-model="edit" persistent>
        <q-card style="min-width: 500px">

          <q-card-section>
            <q-input
              filled
              v-model="edited.name"
              label="name"
              lazy-rules
              :rules="[ val => val && val.length > 0 || 'Please type something']"
            />
            <q-input
              filled
              v-model="edited.x"
              label="coordinate X"
              lazy-rules
              type="number"
              :rules="[val => !!val || 'Field is required']"
            />
            <q-input
              filled
              v-model="edited.y"
              label="coordinate Y"
              lazy-rules
              type="number"
              :rules="[val => !!val || 'Field is required']"
            />

            <q-input
              filled
              v-model="edited.manufacturecost"
              label="manufacture cost"
              lazy-rules
              type="number"
            />

            <q-input
              filled
              v-model="edited.price"
              label="price"
              lazy-rules
              type="number"
              :rules="[val => !!val || 'Field is required']"
            />

            <q-input
              filled
              v-model="edited.unitofmeasure"
              label="unit of measure"
              lazy-rules
            />

            <q-input
              filled
              v-model="edited.eyecolor"
              label="eye color"
              lazy-rules
            />

            <q-input
              filled
              v-model="edited.location_name"
              label="location name"
              lazy-rules
            />

            <q-input
              filled
              v-model="edited.location_x"
              label="location x"
              lazy-rules
              type="number"
            />

            <q-input
              filled
              v-model="edited.location_y"
              label="location y"
              lazy-rules
              type="number"
            />

            <q-input
              filled
              v-model="edited.nationality"
              label="nationality"
              lazy-rules
            />

            <q-input
              filled
              v-model="edited.owner_name"
              label="owner name"
              lazy-rules
            />

          </q-card-section>

          <q-card-actions align="right" class="text-primary">
            <q-btn flat label="Cancel" v-close-popup></q-btn>
            <q-btn flat label="Save" v-close-popup></q-btn>
          </q-card-actions>
        </q-card>
      </q-dialog>

      <q-dialog v-model="create" persistent>
        <q-card style="min-width: 500px">

          <q-card-section>
            <q-input
              filled
              v-model="created.name"
              label="name"
              lazy-rules
              :rules="[ val => val && val.length > 0 || 'Please type something']"
            />
            <q-input
              filled
              v-model="created.x"
              label="coordinate X"
              lazy-rules
              type="number"
              :rules="[val => !!val || 'Field is required']"
            />
            <q-input
              filled
              v-model="created.y"
              label="coordinate Y"
              lazy-rules
              type="number"
              :rules="[val => !!val || 'Field is required']"
            />

            <q-input
              filled
              v-model="created.manufacturecost"
              label="manufacture cost"
              lazy-rules
              type="number"
            />

            <q-input
              filled
              v-model="created.price"
              label="price"
              lazy-rules
              type="number"
              :rules="[val => !!val || 'Field is required']"
            />

            <q-input
              filled
              v-model="created.unitofmeasure"
              label="unit of measure"
              lazy-rules
            />

            <q-input
              filled
              v-model="created.eyecolor"
              label="eye color"
              lazy-rules
            />

            <q-input
              filled
              v-model="created.location_name"
              label="location name"
              lazy-rules
            />

            <q-input
              filled
              v-model="created.location_x"
              label="location x"
              lazy-rules
              type="number"
            />

            <q-input
              filled
              v-model="created.location_y"
              label="location y"
              lazy-rules
              type="number"
            />

            <q-input
              filled
              v-model="created.nationality"
              label="nationality"
              lazy-rules
            />

            <q-input
              filled
              v-model="created.owner_name"
              label="owner name"
              lazy-rules
            />

          </q-card-section>

          <q-card-actions align="right" class="text-primary">
            <q-btn flat label="Cancel" v-close-popup></q-btn>
            <q-btn flat label="Create" v-close-popup></q-btn>
          </q-card-actions>
        </q-card>
      </q-dialog>

    </q-page-container>
  </q-layout>
</template>

<script>
import axios from 'axios'

const api = axios.create({ baseURL: 'http://localhost:8080/soa1_1-1.0-SNAPSHOT/product' })

export default {
  name: 'MainLayout',
  axios,
  api,
  data () {
    return {
      edit: false,
      edited: { name: 'name', x: 237, y: 9.0, manufacturecost: 4, price: 129, unitofmeasure: 'KILOGRAMS', eyecolor: 'BROWN', location_name: 'asd', location_x: 1, location_y: 2, nationality: 'RUSSIA', owner_name: 'asd' },
      create: false,
      created: { name: 'name', x: 237, y: 9.0, manufacturecost: 4, price: 129, unitofmeasure: 'KILOGRAMS', eyecolor: 'BROWN', location_name: 'asd', location_x: 1, location_y: 2, nationality: 'RUSSIA', owner_name: 'asd' },
      selected: [],
      filter: '',
      loading: false,
      pagination: {
        sortBy: 'desc',
        descending: false,
        page: 1,
        rowsPerPage: 5,
        rowsNumber: 10
      },
      columns: [
        { name: 'id', label: 'id', field: 'id', sortable: false },
        {
          name: 'name',
          required: true,
          label: 'name',
          align: 'left',
          field: row => row.name,
          sortable: false
        },
        { name: 'x', label: 'x', field: 'x', sortable: false },
        { name: 'y', label: 'y', field: 'y', sortable: false },
        { name: 'creationdate', label: 'creationdate', field: 'creationdate', sortable: false },
        { name: 'manufacturecost', label: 'manufacturecost', field: 'manufacturecost', sortable: false },
        { name: 'price', label: 'price', field: 'price', sortable: false },
        { name: 'unitofmeasure', label: 'unitofmeasure', field: 'unitofmeasure', sortable: false },
        { name: 'eyecolor', label: 'eyecolor', field: 'eyecolor', sortable: false },
        { name: 'location_name', label: 'location_name', field: 'location_name', sortable: false },
        { name: 'location_x', label: 'location_x', field: 'location_x', sortable: false },
        { name: 'location_y', label: 'location_y', field: 'location_y', sortable: false },
        { name: 'nationality', label: 'nationality', field: 'nationality', sortable: false },
        { name: 'owner_name', label: 'owner_name', field: 'owner_name', sortable: false }
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
          owner_name: 'asd'
        }
      ],
      original: [
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
          owner_name: 'asd'
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
