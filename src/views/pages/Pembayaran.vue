<template>
  <!--Start Data Table -->
  <v-data-table :headers="headers" :items="items" :search="search">
    <template v-slot:top>
      <v-toolbar flat>
        <v-toolbar-title>Pembayaran SPP</v-toolbar-title>
        <v-divider class="mx-4" inset vertical></v-divider>
        <v-spacer></v-spacer>
        <v-text-field
          v-model="search"
          label="Search"
          style="margin-right: 20px; width: 10px"
          single-line
          hide-details
        ></v-text-field>

        <v-dialog v-model="dialog" max-width="500px" persistent>
          <template v-slot:activator="{ on, attrs }">
            <v-btn color="primary" dark v-bind="attrs" v-on="on"> Create</v-btn>
          </template>
          <v-card>
            <v-card-title>
              <span>Input Pembayaran</span>
            </v-card-title>
            <v-card-text>
              <v-container>
                <v-row>
                  <v-col cols="12">
                    <v-text-field label="NISN*" v-model="nisn" required></v-text-field>
                  </v-col>
                  <v-col cols="12">
                    <v-menu
                      v-model="menu2"
                      :close-on-content-click="false"
                      :nudge-right="40"
                      transition="scale-transition"
                      offset-y
                      min-width="auto"
                    >
                      <template v-slot:activator="{ on, attrs }">
                        <v-text-field
                          v-model="date"
                          label="Tanggal Bayar"
                          readonly
                          v-bind="attrs"
                          v-on="on"
                        ></v-text-field>
                      </template>
                      <v-date-picker v-model="date" @input="menu2 = false"></v-date-picker>
                    </v-menu>
                  </v-col>
                  <v-col cols="12">
                    <v-text-field label="Bulan SPP*" v-model="bulan_spp" required></v-text-field>
                  </v-col>
                  <v-col cols="12">
                    <v-text-field label="Tahun SPP*" v-model="tahun_spp" required></v-text-field>
                  </v-col>
                </v-row>
              </v-container>
            </v-card-text>

            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn color="blue darken-1" text @click="close"> Cancel</v-btn>
              <v-btn color="blue darken-1" text @click="save"> Save</v-btn>
            </v-card-actions>
          </v-card>
        </v-dialog>

        <v-dialog v-model="dialogEdit" max-width="500px" persistent>
          <v-card>
            <v-card-title>
              <span>Edit Pembayaran</span>
            </v-card-title>
            <v-card-text>
              <v-container>
                <v-row>
                  <v-col cols="12">
                    <v-text-field label="NISN*" v-model="nisn" required></v-text-field>
                  </v-col>
                  <v-col cols="12">
                    <v-menu
                      v-model="menu2"
                      :close-on-content-click="false"
                      :nudge-right="40"
                      transition="scale-transition"
                      offset-y
                      min-width="auto"
                    >
                      <template v-slot:activator="{ on, attrs }">
                        <v-text-field
                          v-model="date"
                          label="Tanggal Bayar"
                          readonly
                          v-bind="attrs"
                          v-on="on"
                        ></v-text-field>
                      </template>
                      <v-date-picker v-model="date" @input="menu2 = false"></v-date-picker>
                    </v-menu>
                  </v-col>
                  <v-col cols="12">
                    <v-text-field label="Bulan SPP*" v-model="bulan_spp" required></v-text-field>
                  </v-col>
                  <v-col cols="12">
                    <v-text-field label="Tahun SPP*" v-model="tahun_spp" required></v-text-field>
                  </v-col>
                </v-row>
              </v-container>
            </v-card-text>

            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn color="blue darken-1" text @click="reset"> Cancel</v-btn>
              <v-btn color="blue darken-1" text @click="saveEdit"> Save</v-btn>
            </v-card-actions>
          </v-card>
        </v-dialog>

        <v-dialog v-model="dialogDelete" max-width="500px" persistent>
          <v-card>
            <v-card-title>Are you sure you want to delete this item?</v-card-title>
            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn color="blue darken-1" text @click="dialogDelete = false">Cancel</v-btn>
              <v-btn color="blue darken-1" text @click="deleteItem">OK</v-btn>
              <v-spacer></v-spacer>
            </v-card-actions>
          </v-card>
        </v-dialog>
      </v-toolbar>
    </template>

    <template v-slot:[`item.actions`]="{ item }">
      <v-btn small icon @click="setEditValue(item)">
        <v-icon>
          {{ icons.mdiPencil }}
        </v-icon>
      </v-btn>
      <v-btn small icon @click="setDelete(item)">
        <v-icon>
          {{ icons.mdiDeleteOutline }}
        </v-icon>
      </v-btn>
    </template>
  </v-data-table>
  <!--End Data Table -->
</template>

<script>
import { mdiDeleteOutline, mdiPencil, mdiCalendar } from '@mdi/js'

export default {
  name: 'class',
  data() {
    return {
      search: '',
      pagination: {
        rowsPerPage: 10,
      },

      // header table
      items: [],
      headers: [
        {
          text: 'ID Pembayaran',
          value: 'id_pembayaran',
          align: 'left',
          width: '20px',
        },
        {
          text: 'NISN',
          value: 'nisn',
          align: 'left',
          width: '100px',
        },
        {
          text: 'Nama',
          value: 'nama',
          align: 'left',
          width: '20px',
        },
        {
          text: 'No. Telp',
          value: 'no_telp',
          align: 'left',
          width: '20px',
        },
        {
          text: 'Tanggal Bayar',
          value: 'tgl_bayar',
          align: 'left',
          sortable: false,
          width: '20px',
        },
        {
          text: 'Alamat',
          value: 'alamat',
          align: 'left',
          width: '20px',
        },
        {
          text: 'Actions',
          value: 'actions',
          align: 'left',
          width: '50px',
        },
      ],
      dialog: false,
      dialogDelete: false,
      dialogEdit: false,

      id: '',
      nisn: '',
      tgl_bayar: '',
      bulan_spp: '',
      tahun_spp: '',

      date: new Date(Date.now() - new Date().getTimezoneOffset() * 60000).toISOString().substr(0, 10),
      menu: false,
      modal: false,
      menu2: false,

      // Tanggal Mulai
      startDateMenu: false,
      startDate: '',
      startDateRules: [v => !!v || 'Kolom ini harus diisi.'],
      date: '',

      icons: {
        mdiDeleteOutline,
        mdiPencil,
        mdiCalendar,
      },
    }
  },

  mounted() {
    this.key = localStorage.getItem('Authorization')
    this.list()
  },

  methods: {
    close() {
      this.dialog = false
    },

    async list() {
      try {
        let conf = { headers: { Authorization: 'Bearer ' + this.key } }
        const res = await this.axios.get('http://127.0.0.1:8000/api/getpembayaran', conf)
        console.log(res.data)
        this.items = res.data.data
      } catch (err) {
        console.log(err)
      }
    },

    async save() {
      try {
        const body = {
          id_petugas: localStorage.getItem('id_petugas'),
          nisn: this.nisn,
          tgl_bayar: this.date,
          bulan_spp: this.bulan_spp,
          tahun_spp: this.tahun_spp,
        }

        let conf = { headers: { Authorization: 'Bearer ' + this.key } }
        await this.axios.post('http://127.0.0.1:8000/api/inputpembayaran', body, conf)
        this.list()
        this.dialog = false
        this.snackbar = true
        this.message = 'Created Successfully'
      } catch (err) {
        console.log(err)
      }
    },

    setEditValue(record) {
      this.id = record.id_pembayaran
      this.nisn = record.nisn
      this.date = record.tgl_bayar
      this.bulan_spp = record.bulan_spp
      this.tahun_spp = record.tahun_spp

      this.dialogEdit = true
    },

    reset() {
      this.id = ''
      this.nisn = ''
      this.date = ''
      this.bulan_spp = ''
      this.tahun_spp = ''

      this.dialogEdit = false
    },

    async saveEdit() {
      try {
        const body = {
          id_petugas: localStorage.getItem('id_petugas'),
          nisn: this.nisn,
          tgl_bayar: this.date,
          bulan_spp: this.bulan_spp,
          tahun_spp: this.tahun_spp,
        }

        let conf = { headers: { Authorization: 'Bearer ' + this.key } }
        await this.axios.put(`http://127.0.0.1:8000/api/updatepembayaran/${this.id}`, body, conf)
        this.list()
        this.dialogEdit = false
        this.snackbar = true
        this.message = 'Edit Successfully'
      } catch (err) {
        console.log(err)
      }
    },

    setDelete(record) {
      this.id = record.id_pembayaran
      this.dialogDelete = true
    },

    async deleteItem() {
      try {
        let conf = { headers: { Authorization: 'Bearer ' + this.key } }
        await this.axios.delete(`http://127.0.0.1:8000/api/hapuspembayaran/${this.id}`, conf)
        this.list()
        this.dialogDelete = false
      } catch (err) {
        console.log(err)
      }
    },
  },
}
</script>
