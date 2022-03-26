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
    close() {
      this.dialog = false
    },

    async list() {
      try {
        let nisn = localStorage.getItem('nisn')
        let conf = { headers: { Authorization: 'Bearer ' + this.key } }
        const res = await this.axios.get(`http://127.0.0.1:8000/api/kurang_bayar/${nisn}`, conf)
        console.log(res)
        this.items = res.data
      } catch (err) {
        console.log(err)
      }
    },

    async save() {
      try {
        const body = {
          nisn: this.nisn,
          nis: this.nis,
          nama: this.nama,
          id_kelas: this.id_kelas,
          alamat: this.alamat,
          no_telp: this.phone,
          username: this.username,
          password: this.password,
        }

        let conf = { headers: { Authorization: 'Bearer ' + this.key } }
        await this.axios.post('http://127.0.0.1:8000/api/inputsiswa', body, conf)
        this.dialog = false
        this.list()
        this.snackbar = true
        this.message = 'Created Successfully'
      } catch (err) {
        console.log(err)
      }
    },

    setEditValue(record) {
      this.id = record.id
      this.nisn = record.nisn
      this.nis = record.nis
      this.id_kelas = record.id_kelas
      this.nama = record.nama
      this.alamat = record.alamat
      this.phone = record.no_telp
      this.username = record.username
      this.password = record.password

      this.dialogEdit = true
    },

    async saveEdit() {
      try {
        const body = {
          nisn: this.nisn,
          nis: this.nis,
          nama: this.nama,
          id_kelas: this.id_kelas,
          alamat: this.alamat,
          no_telp: this.phone,
          username: this.username,
          password: this.password,
        }

        let conf = { headers: { Authorization: 'Bearer ' + this.key } }
        await this.axios.put(`http://127.0.0.1:8000/api/updatesiswa/${this.nisn}`, body, conf)
        this.dialog = false
        this.list()
        this.snackbar = true
        this.message = 'Update Successfully'
        this.dialogEdit = false
      } catch (err) {
        console.log(err)
      }
    },

    setDelete(record) {
      this.nisn = record.nisn
      this.dialogDelete = true
    },

    async deleteItem() {
      try {
        let conf = { headers: { Authorization: 'Bearer ' + this.key } }
        await this.axios.delete(`http://127.0.0.1:8000/api/deletesiswa/${this.nisn}`, conf)
        this.list()
        this.dialogDelete = false
      } catch (err) {
        console.log(err)
      }
    },
  },
}
</script>
