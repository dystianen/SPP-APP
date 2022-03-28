<template>
  <!--Start Data Table -->
  <div>
    <v-data-table :headers="headers" :items="items">
      <template v-slot:top>
        <v-toolbar flat>
          <v-toolbar-title>HISTORY PEMBAYARAN</v-toolbar-title>
          <v-divider class="mx-4" inset vertical></v-divider>
          <v-spacer></v-spacer>
        </v-toolbar>
      </template>
    </v-data-table>
    <!--End Data Table -->

    <v-snackbar v-model="snackbar">
      {{ message }}

      <template v-slot:action="{ attrs }">
        <v-btn color="pink" text v-bind="attrs" @click="snackbar = false"> Close </v-btn>
      </template>
    </v-snackbar>
  </div>
</template>

<script>
import { mdiCreditCardOutline, mdiPencil } from '@mdi/js'
import { appConfig } from '../../Config/app'

export default {
  name: 'student',
  data() {
    return {
      key: '',
      pagination: {
        rowsPerPage: 10,
      },

      // header table
      items: [],
      headers: [
        {
          text: 'NISN',
          value: 'nisn',
          align: 'left',
          width: '20px',
        },
        {
          text: 'Kelas',
          value: 'nama_kelas',
          align: 'left',
          width: '20px',
        },
        {
          text: 'Nama',
          value: 'nama',
          align: 'left',
          width: '20px',
        },
        {
          text: 'Jurusan',
          value: 'jurusan',
          align: 'left',
          width: '20px',
        },
        {
          text: 'Nominal',
          value: 'nominal',
          align: 'left',
          width: '80px',
        },
      ],

      dialog: false,
      dialogEdit: false,
      dialogDelete: false,

      // Form
      id: '',
      nisn: '',
      nis: '',
      id_kelas: '',
      nama: '',
      alamat: '',
      phone: '',
      username: '',
      password: '',

      // kelas
      items_kelas: [],

      snackbar: false,
      message: '',

      icons: {
        mdiCreditCardOutline,
        mdiPencil,
      },
    }
  },

  mounted() {
    this.key = localStorage.getItem('Authorization')
    this.list()
    this.listKelas()
  },

  methods: {
    async list() {
      try {
        let nisn = localStorage.getItem('nisn')
        let conf = { headers: { Authorization: 'Bearer ' + this.key } }
        const res = await this.axios.get(appConfig.apiUrl + `/kurang_bayar/${nisn}`, conf)
        console.log(res)
        this.items = res.data
      } catch (err) {
        console.log(err)
      }
    },
  },
}
</script>
