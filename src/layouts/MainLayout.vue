<template>
  <q-layout view="lHh Lpr lFf">
    <h1> SOA1 </h1>
    <q-page-container>
      <div id="q-app">
        <div class="q-pa-md">

          <q-table
            title="Products"
            selection="single"
            :selected.sync="selected"
            :data="data"
            :columns="columns"
            row-key="id"
            :loading="loading"
            @request="onRequest"
            binary-state-sort
          >
            <template v-slot:top-right>
              <div v-if="$q.screen.gt.xs" class="q-pa-md q-gutter-sm">
                <q-btn color="dark" label="Create" @click="create = true"/>
                <q-btn color="secondary" label="Edit" @click="edited = selected[0]; edit = true" :disabled="(selected.length == 0)"/>
                <q-btn color="secondary" label="Delete" @click="sendDelete(selected[0])" :disabled="(selected.length == 0)"/>
              </div>
              <q-input dense debounce="300" v-model="sortFields" placeholder="sort Fields"></q-input>
              <q-input borderless dense debounce="300" v-model="filter" placeholder="Search">
                <template v-slot:append>
                  <q-btn icon="search" @click="onRequest({ filter: filter })"/>
                </template>
              </q-input>
            </template>

          </q-table>
          <div class="q-mt-md">
            Status: {{ JSON.stringify(error) }}
          </div>
          <q-card>
            <q-card-section>
              <div class="q-mt-md">
                Cумма значений поля price для всех объектов: {{ priceSum }}
              </div>
              <q-btn color="secondary" label="Рассчитать" @click="sendPriceSum()"></q-btn>
            </q-card-section>
            <q-card-section>
              <div class="q-mt-md">
                Cреднее значение поля manufactureCost для всех объектов: {{ manufactureCostAvr }}
              </div>
              <q-btn color="secondary" label="Рассчитать" @click="sendManufactureCostAvr()"></q-btn>
            </q-card-section>

              <q-table
                title="массив объектов, значение поля name которых начинается с заданной подстроки"
                :data="dataSubs"
                :columns="columns"
                row-key="id"
              >
                <template v-slot:top-right>
                  <div v-if="$q.screen.gt.xs" class="q-pa-md q-gutter-sm">
                    <q-btn color="secondary" label="Рассчитать" @click="sendSubs()"></q-btn>
                  </div>
                  <q-input borderless dense debounce="300" v-model="subsFilter" placeholder="подстрока">
                    <template v-slot:append>
                      <q-icon name="search"></q-icon>
                    </template>
                  </q-input>
                </template>

              </q-table>

          </q-card>
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
              v-model="edited.manufactureCost"
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
              v-model="edited.unitOfMeasure"
              label="unit of measure"
              lazy-rules
            />

            <q-input
              filled
              v-model="edited.eyeColor"
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
              v-model="edited.owner_x"
              label="location x"
              lazy-rules
              type="number"
            />

            <q-input
              filled
              v-model="edited.owner_y"
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
            <q-btn flat label="Cancel" @click="initTable()" v-close-popup></q-btn>
            <q-btn flat label="Save" @click="sendEdit(edited)" v-close-popup></q-btn>
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
              v-model="created.manufactureCost"
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
              v-model="created.unitOfMeasure"
              label="unit of measure"
              lazy-rules
            />

            <q-input
              filled
              v-model="created.eyeColor"
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
              v-model="created.owner_x"
              label="location x"
              lazy-rules
              type="number"
            />

            <q-input
              filled
              v-model="created.owner_y"
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
          </q-card-section>

          <q-card-section>
<!--            <q-img-->
<!--              :src="url"-->
<!--              spinner-color="white"-->
<!--            ></q-img>-->
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

// const api = axios.create({ baseURL: 'http://localhost:8642/soa1_1-1.0-SNAPSHOT/product' })
const api = axios.create({ baseURL: '/soa1_1-1.0-SNAPSHOT/product' })
// const api = axios.create({ baseURL: 'http://localhost:8080/soa1_1-1.0-SNAPSHOT/product' })

export default {
  name: 'MainLayout',
  axios,
  api,
  xml2js,
  data () {
    return {
      edit: false,
      alert: false,
      priceSum: null,
      manufactureCostAvr: null,
      error: '',
      url: '',
      edited: { name: 'name', x: 237, y: 9.0, manufactureCost: 4, price: 129, unitOfMeasure: 'KILOGRAMS', eyeColor: 'BROWN', location_name: 'asd', owner_x: 1, owner_y: 2, nationality: 'RUSSIA', owner_name: 'asd' },
      create: false,
      created: { name: 'name', x: 237, y: 9.0, manufactureCost: 4, price: 129, unitOfMeasure: 'KILOGRAMS', eyeColor: 'BROWN', location_name: 'asd', owner_x: 1, owner_y: 2, nationality: 'RUSSIA', owner_name: 'asd' },
      selected: [],
      filter: '',
      subsFilter: '',
      sortFields: '',
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
        { name: 'creationDate', label: 'creationDate', field: 'creationDate', sortable: false },
        { name: 'manufactureCost', label: 'manufactureCost', field: 'manufactureCost', sortable: false },
        { name: 'price', label: 'price', field: 'price', sortable: false },
        { name: 'unitOfMeasure', label: 'unitOfMeasure', field: 'unitOfMeasure', sortable: false },
        { name: 'eyeColor', label: 'eyeColor', field: 'eyeColor', sortable: false },
        { name: 'location_name', label: 'location_name', field: 'location_name', sortable: false },
        { name: 'owner_x', label: 'owner_x', field: 'owner_x', sortable: false },
        { name: 'owner_y', label: 'owner_y', field: 'owner_y', sortable: false },
        { name: 'nationality', label: 'nationality', field: 'nationality', sortable: false },
        { name: 'owner_name', label: 'owner_name', field: 'owner_name', sortable: false }
      ],
      data: [
      ],
      dataSubs: [],
      original: [
        {
          id: 2,
          name: 'name',
          x: 237,
          y: 9.0,
          creationDate: '37',
          manufactureCost: 4,
          price: 129,
          unitOfMeasure: 'KILOGRAMS',
          eyeColor: 'BROWN',
          location_name: 'asd',
          owner_x: 1,
          owner_y: 2,
          nationality: 'RUSSIA',
          owner_name: 'asd'
        },
        {
          id: 2,
          name: undefined,
          x: undefined,
          y: 9.0,
          creationDate: '37',
          manufactureCost: undefined,
          price: 129,
          unitOfMeasure: 'KILOGRAMS',
          eyeColor: undefined,
          location_name: 'asd',
          owner_x: 1,
          owner_y: 2,
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
      console.log(filter)
      this.loading = true
      const self = this
      // eslint-disable-next-line no-unused-vars
      var myObj = { todata: self.data, toLoad: self.loading }
      api.get('/?' + filter + (this.sortFields === '' ? '' : '&sortFields=' + this.sortFields)).catch((error) => {
        self.customAlert(error)
        self.customErr(error)
        console.log(error.toJSON())
      }).then((response) => {
        const newData = []
        const a = response.data
        xml2js.parseString(a, function (err, result) {
          console.log(err)
          console.log(result.root.element)
          const wtf = result.root.element
          for (var j in result.root.element) {
            const i = wtf[j]
            // console.log(i.id[0])
            var newObj = {}
            try {
              newObj.id = i.id[0]._
            } catch {
            }
            try {
              newObj.name = i.name[0]
            } catch {
            }
            try {
              newObj.x = i.coordinate[0].x[0]
            } catch {
            }
            try {
              newObj.y = i.coordinate[0].y[0]
            } catch {
            }
            try {
              newObj.creationDate = i.creationdate[0]
            } catch {
            }
            try {
              newObj.manufactureCost = i.manufacturecost[0]
            } catch {
            }
            try {
              newObj.price = i.price[0]
            } catch {
            }
            try {
              newObj.unitOfMeasure = i.unitofmeasure[0]
            } catch {
            }
            try {
              newObj.eyeColor = i.person[0].eyecolor[0]
            } catch {
            }
            try {
              newObj.location_name = i.person[0].location_name[0]
            } catch {
            }
            try {
              newObj.owner_x = i.person[0].location_x[0]
            } catch {
            }
            try {
              newObj.owner_y = i.person[0].location_y[0]
            } catch {
            }
            try {
              newObj.nationality = i.person[0].nationality[0]
            } catch {
            }
            try {
              newObj.owner_name = i.person[0].owner_name[0]
            } catch {
            }
            for (const propsKey in newObj) {
              // eslint-disable-next-line no-prototype-builtins
              if (newObj[propsKey].hasOwnProperty('$')) {
                newObj[propsKey] = ''
              }
            }
            newData.push(newObj)
          }
          self.data = JSON.parse(JSON.stringify(newData))
          self.loading = false
          console.log(self.data)
        })
      })
    },

    sendSubs () {
      const filter = this.subsFilter
      const self = this
      api.post('/name_starts/' + filter).catch((error) => {
        self.customErr(error)
        console.log(error.toJSON())
      }).then((response) => {
        const newData = []
        const a = response.data
        xml2js.parseString(a, function (err, result) {
          console.log(err)
          // console.log(result.root.element)
          const wtf = result.root.element
          for (var j in result.root.element) {
            const i = wtf[j]
            // console.log(i.id[0])
            var newObj = {}
            try {
              newObj.id = i.id[0]._
            } catch {
            }
            try {
              newObj.name = i.name[0]
            } catch {
            }
            try {
              newObj.x = i.coordinate[0].x[0]
            } catch {
            }
            try {
              newObj.y = i.coordinate[0].y[0]
            } catch {
            }
            try {
              newObj.creationDate = i.creationdate[0]
            } catch {
            }
            try {
              newObj.manufactureCost = i.manufacturecost[0]
            } catch {
            }
            try {
              newObj.price = i.price[0]
            } catch {
            }
            try {
              newObj.unitOfMeasure = i.unitofmeasure[0]
            } catch {
            }
            try {
              newObj.eyeColor = i.person[0].eyecolor[0]
            } catch {
            }
            try {
              newObj.location_name = i.person[0].location_name[0]
            } catch {
            }
            try {
              newObj.owner_x = i.person[0].location_x[0]
            } catch {
            }
            try {
              newObj.owner_y = i.person[0].location_y[0]
            } catch {
            }
            try {
              newObj.nationality = i.person[0].nationality[0]
            } catch {
            }
            try {
              newObj.owner_name = i.person[0].owner_name[0]
            } catch {
            }
            newData.push(newObj)
          }
          self.dataSubs = JSON.parse(JSON.stringify(newData))
          console.log(self.dataSubs)
        })
      })
    },

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
      const createStr = '/'
      let bodyXml = '<?xml version="1.0" encoding="UTF-8"?><root>'
      for (var prop in created) {
        if (created[prop] !== '') {
          bodyXml += '<' + prop + '>' + created[prop] + '</' + prop + '>'
        }
      }
      bodyXml += '</root>'
      const self = this
      api.post(createStr, bodyXml).catch((error) => {
        self.customAlert(error)
        console.log(error.toJSON())
      }).then(function (response) {
        self.initTable()
      })
    },

    sendEdit (edt) {
      const edtStr = '/' + edt.id

      let bodyXml = '<?xml version="1.0" encoding="UTF-8"?><root>'
      for (var prop in edt) {
        if (prop !== 'id') {
          bodyXml += '<' + prop + '>' + edt[prop] + '</' + prop + '>'
        }
      }
      bodyXml += '</root>'

      // for (var prop in edt) {
      //   if (edt[prop] !== '' && prop !== 'id') {
      //     edtStr += prop + '=' + edt[prop] + '&'
      //   }
      // }

      const self = this
      api.patch(edtStr, bodyXml).catch((error) => {
        self.customAlert(error)
        console.log(error.toJSON())
      }).then(function (response) {
        self.initTable()
      })
    },

    // /soa1_1-1.0-SNAPSHOT/product/sum_price
    sendPriceSum () {
      const self = this
      api.post('/sum_price').catch((error) => {
        self.customErr(error)
        console.log(error.toJSON())
      }).then((response) => {
        const a = response.data
        xml2js.parseString(a, function (err, result) {
          console.log(err)
          // console.log(result.root.element)
          self.priceSum = result.root.element[0]._
        })
      })
    },

    // /soa1_1-1.0-SNAPSHOT/product/avg_manufacture_cost
    sendManufactureCostAvr () {
      const self = this
      api.post('/avg_manufacture_cost').catch((error) => {
        self.customErr(error)
        console.log(error.toJSON())
      }).then((response) => {
        const a = response.data
        xml2js.parseString(a, function (err, result) {
          console.log(err)
          // console.log(result.root.element)
          self.manufactureCostAvr = result.root.element[0]._
        })
      })
    }
  }
}
</script>
