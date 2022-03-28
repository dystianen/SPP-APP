<template>
  <!--Start Data Table -->
  <div>
    <v-data-table :headers="headers" :items="items" :search="search">
      <template v-slot:top>
        <v-toolbar flat>
          <v-toolbar-title>SISWA</v-toolbar-title>
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
              <v-btn color="primary" dark v-bind="attrs" v-on="on"> Create </v-btn>
            </template>
            <v-card>
              <v-card>
                <v-card-title>
                  <span class="text-h5">Create Student</span>
                </v-card-title>
                <v-card-text>
                  <v-container>
                    <v-row>
                      <v-col cols="12">
                        <v-text-field label="NISN*" v-model="nisn" required></v-text-field>
                      </v-col>
                      <v-col cols="12">
                        <v-text-field label="NIS*" v-model="nis" required></v-text-field>
                      </v-col>
                      <v-col cols="12">
                        <v-select label="Kelas*" v-model="id_kelas" :items="items_kelas" required> </v-select>
                      </v-col>
                      <v-col cols="12">
                        <v-text-field label="Nama*" v-model="nama" required></v-text-field>
                      </v-col>
                      <v-col cols="12">
                        <v-text-field label="Alamat*" v-model="alamat" required></v-text-field>
                      </v-col>
                      <v-col cols="12">
                        <v-text-field label="No. Telp*" v-model="phone" required></v-text-field>
                      </v-col>
                      <v-col cols="12">
                        <v-text-field label="Username*" v-model="username" required></v-text-field>
                      </v-col>
                      <v-col cols="12">
                        <v-text-field type="password" label="Password*" v-model="password" required></v-text-field>
                      </v-col>
                    </v-row>
                  </v-container>
                  <small>*indicates required field</small>
                </v-card-text>
              </v-card>

              <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn color="blue darken-1" text @click="close"> Cancel </v-btn>
                <v-btn color="blue darken-1" text @click="save"> Save </v-btn>
              </v-card-actions>
            </v-card>
          </v-dialog>

          <v-dialog v-model="dialogEdit" max-width="500px" persistent>
            <v-card>
              <v-card>
                <v-card-title>
                  <span class="text-h5">Edit Sstudent</span>
                </v-card-title>
                <v-card-text>
                  <v-container>
                    <v-row>
                      <v-col cols="12">
                        <v-text-field label="NISN*" v-model="nisn" required></v-text-field>
                      </v-col>
                      <v-col cols="12">
                        <v-text-field label="NIS*" v-model="nis" required></v-text-field>
                      </v-col>
                      <v-col cols="12">
                        <v-select label="Kelas*" v-model="id_kelas" :items="items_kelas" required> </v-select>
                      </v-col>
                      <v-col cols="12">
                        <v-text-field label="Nama*" v-model="nama" required></v-text-field>
                      </v-col>
                      <v-col cols="12">
                        <v-text-field label="Alamat*" v-model="alamat" required></v-text-field>
                      </v-col>
                      <v-col cols="12">
                        <v-text-field label="No. Telp*" v-model="phone" required></v-text-field>
                      </v-col>
                      <v-col cols="12">
                        <v-text-field label="Username*" v-model="username" required></v-text-field>
                      </v-col>
                      <v-col cols="12">
                        <v-text-field type="password" label="Password*" v-model="password" required></v-text-field>
                      </v-col>
                    </v-row>
                  </v-container>
                  <small>*indicates required field</small>
                </v-card-text>
              </v-card>

              <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn color="blue darken-1" text @click="reset"> Cancel </v-btn>
                <v-btn color="blue darken-1" text @click="saveEdit"> Save </v-btn>
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

    <v-snackbar v-model="snackbar">
      {{ message }}

      <template v-slot:action="{ attrs }">
        <v-btn color="pink" text v-bind="attrs" @click="snackbar = false"> Close </v-btn>
      </template>
    </v-snackbar>
  </div>
</template>

<script>
import { mdiDeleteOutline, mdiPencil } from '@mdi/js'

export default {
  name: 'student',
  data() {
    return {
      search: '',
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
          value: 'id_kelas',
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
          text: 'Alamat',
          value: 'alamat',
          align: 'left',
          width: '20px',
        },
        {
          text: 'No. Telp',
          value: 'no_telp',
          align: 'left',
          width: '80px',
        },
        {
          text: 'Username',
          value: 'username',
          align: 'left',
          width: '20px',
        },
        {
          text: 'Actions',
          value: 'actions',
          align: 'left',
          width: '150px',
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
        mdiDeleteOutline,
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
      this.$nextTick(() => {
        this.editedIndex = -1
      })
    },

    async listKelas() {
      try {
        let conf = { headers: { Authorization: 'Bearer ' + this.key } }
        const res = await this.axios.get('http://127.0.0.1:8000/api/lihatkelas', conf)
        console.log(res.data)
        const result = res.data.data

        if (result.length > 0) {
          for (let i = 0; i < result.length; i++) {
            const dataKelas = {
              value: result[i].id_kelas,
              text: result[i].nama_kelas,
            }
            this.items_kelas.push(dataKelas)
          }
        }
      } catch (err) {
        console.log(err)
      }
    },

    async list() {
      try {
        let conf = { headers: { Authorization: 'Bearer ' + this.key } }
        const res = await this.axios.get('http://127.0.0.1:8000/api/getsiswa', conf)
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

    reset() {
      this.id = ''
      this.nisn = ''
      this.nis = ''
      this.id_kelas = ''
      this.nama = ''
      this.alamat = ''
      this.phone = ''
      this.username = ''
      this.password = ''

      this.dialogEdit = false
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
