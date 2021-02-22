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
            :loading="loading"
            :filter="filter"
            @request="onRequest"
            binary-state-sort
          >
            <template v-slot:top-right>
              <div v-if="$q.screen.gt.xs" class="q-pa-md q-gutter-sm">
                <q-btn color="dark" label="Create" @click="create = true"/>
                <q-btn color="secondary" label="Edit" @click="edited = selected[0]; edit = true" :disabled="(selected.length == 0)"/>
                <q-btn color="secondary" label="Delete" @click="sendDelete(selected[0])" :disabled="(selected.length == 0)"/>
              </div>
              <q-input borderless dense debounce="300" v-model="filter" placeholder="Search">
                <template v-slot:append>
                  <q-icon name="search"></q-icon>
                </template>
              </q-input>
            </template>

          </q-table>
          <div class="q-mt-md">
            Selected: {{ JSON.stringify(error) }}
          </div>
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
            <q-btn flat label="Create" @click="sendCreate(created)" v-close-popup></q-btn>
          </q-card-actions>
        </q-card>
      </q-dialog>

      <q-dialog v-model="alert">
        <q-card>
          <q-card-section>
            <div class="text-h6">Alert</div>
          </q-card-section>

          <q-card-section class="q-pt-none">
            {{ error }}
            <img :src="url" alt="">
          </q-card-section>

          <q-card-actions align="right">
            <q-btn flat label="OK" color="primary" v-close-popup></q-btn>
          </q-card-actions>
        </q-card>
      </q-dialog>

    </q-page-container>
  </q-layout>
</template>

<script>
import axios from 'axios'
import xml2js from 'xml2js'

const api = axios.create({ baseURL: 'http://localhost:8080/soa1_1-1.0-SNAPSHOT/product' })

export default {
  name: 'MainLayout',
  axios,
  api,
  xml2js,
  data () {
    return {
      edit: false,
      alert: false,
      error: '',
      url: '',
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
          eyecolor: undefined,
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
        },
        {
          id: 2,
          name: undefined,
          x: undefined,
          y: 9.0,
          creationdate: '37',
          manufacturecost: undefined,
          price: 129,
          unitofmeasure: 'KILOGRAMS',
          eyecolor: undefined,
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
    this.initTable()
  },
  methods: {
    initTable () {
      this.onRequest({
        pagination: this.pagination,
        filter: ''
      })
    },

    onRequest (props) {
      // const { page, rowsPerPage, sortBy, descending } = props.pagination
      const filter = props.filter

      this.loading = true
      const self = this
      // eslint-disable-next-line no-unused-vars
      const newData = []
      api.get('/?' + filter).catch((error) => {
        self.customErr(error)
        console.log(error.toJSON())
      }).then((response) => {
        const a = response.data
        xml2js.parseString(a, function (err, result) {
          console.log(err)
          // console.log(result.root.element)
          const wtf = result.root.element
          for (var j in result.root.element) {
            const i = wtf[j]
            // console.log(i.id[0]._)
            var newObj = {}
            try {
              newObj.id = i.id[0]._
            } catch {}
            try {
              newObj.name = i.name[0]
            } catch {}
            try {
              newObj.x = i.coordinate[0].x[0]._
            } catch {}
            try {
              newObj.y = i.coordinate[0].y[0]._
            } catch {}
            try {
              newObj.creationdate = i.creationdate[0]
            } catch {}
            try {
              newObj.manufacturecost = i.manufacturecost[0]._
            } catch {}
            try {
              newObj.price = i.price[0]._
            } catch {}
            try {
              newObj.unitofmeasure = i.unitofmeasure[0]
            } catch {}
            try {
              newObj.eyecolor = i.person[0].eyecolor[0]
            } catch {}
            try {
              newObj.location_name = i.person[0].location_name[0]
            } catch {}
            try {
              newObj.location_x = i.person[0].location_x[0]._
            } catch {}
            try {
              newObj.location_y = i.person[0].location_y[0]._
            } catch {}
            try {
              newObj.nationality = i.person[0].nationality[0]
            } catch {}
            try {
              newObj.owner_name = i.person[0].owner_name[0]
            } catch {}
            newData.push(newObj)
          }
          self.data = JSON.parse(JSON.stringify(newData))
          self.loading = false
        })
      })
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
    },

    customErr (err) {
      this.error = err.message
    },

    customAlert (err) {
      this.error = err.message
      this.url = 'https://http.cat/' + err.message.slice(-3) + '.jpg'
      this.alert = true
    },

    sendDelete (todel) {
      const id = todel.id
      const self = this
      api.delete('/' + id).catch((error) => {
        self.customAlert(error)
        console.log(error.toJSON())
      }).then(function (response) {
        self.initTable()
      })
    },

    sendCreate (created) {
      let createStr = '/?'
      for (var prop in created) {
        if (created[prop] !== '') {
          createStr += prop + '=' + created[prop] + '&'
        }
      }
      const self = this
      api.put(createStr).catch((error) => {
        self.customAlert(error)
        console.log(error.toJSON())
      }).then(function (response) {
        self.initTable()
      })
    }
  }
}
</script>
