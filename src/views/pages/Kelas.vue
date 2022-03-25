<template>
  <!--Start Data Table -->
  <v-data-table :headers="headers" :items="items">
    <template v-slot:top>
      <v-toolbar flat>
        <v-toolbar-title>Class</v-toolbar-title>
        <v-divider class="mx-4" inset vertical></v-divider>
        <v-spacer></v-spacer>

        <v-dialog v-model="dialog" max-width="500px">
          <template v-slot:activator="{ on, attrs }">
            <v-btn color="primary" dark v-bind="attrs" v-on="on"> Create</v-btn>
          </template>
          <v-card>
            <v-card-title>
              <span>Create Kelas</span>
            </v-card-title>
            <v-card-text>
              <v-container>
                <v-row>
                  <v-col cols="12">
                    <v-text-field label="Nama Kelas*" v-model="classname" required></v-text-field>
                  </v-col>
                  <v-col cols="12">
                    <v-text-field label="Jurusan*" v-model="jurusan" required></v-text-field>
                  </v-col>
                  <v-col cols="12">
                    <v-text-field label="Angkatan*" v-model="angkatan" required></v-text-field>
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

        <v-dialog v-model="dialogEdit" max-width="500px">
          <v-card>
            <v-card-title>
              <span>Edit Kelas</span>
            </v-card-title>
            <v-card-text>
              <v-container>
                <v-row>
                  <v-col cols="12">
                    <v-text-field label="Nama Kelas*" v-model="classname" required></v-text-field>
                  </v-col>
                  <v-col cols="12">
                    <v-text-field label="Jurusan*" v-model="jurusan" required></v-text-field>
                  </v-col>
                  <v-col cols="12">
                    <v-text-field label="Angkatan*" v-model="angkatan" required></v-text-field>
                  </v-col>
                </v-row>
              </v-container>
            </v-card-text>

            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn color="blue darken-1" text @click="dialogEdit = false"> Cancel</v-btn>
              <v-btn color="blue darken-1" text @click="saveEdit"> Save</v-btn>
            </v-card-actions>
          </v-card>
        </v-dialog>

        <v-dialog v-model="dialogDelete" max-width="500px">
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
import { mdiDeleteOutline, mdiPencil } from '@mdi/js'

export default {
  name: 'class',
  data() {
    return {
      pagination: {
        rowsPerPage: 10,
      },

      // header table
      items: [],
      headers: [
        {
          text: 'ID Kelas',
          value: 'id_kelas',
          align: 'left',
          width: '20px',
        },
        {
          text: 'Nama Kelas',
          value: 'nama_kelas',
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
          text: 'Angkatan',
          value: 'angkatan',
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
      dialogDelete: false,
      dialogEdit: false,

      id: '',
      classname: '',
      jurusan: '',
      angkatan: '',

      icons: {
        mdiDeleteOutline,
        mdiPencil,
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

    isSave() {
      if (this.isEdit === true) {
        this.saveEdit
        console.log('masukk edit')
      } else {
        this.save
        console.log('masukk create')
      }
    },

    async list() {
      try {
        let conf = { headers: { Authorization: 'Bearer ' + this.key } }
        const res = await this.axios.get('http://127.0.0.1:8000/api/dapatkankelas', conf)
        console.log(res.data)
        this.items = res.data.data
      } catch (err) {
        console.log(err)
      }
    },

    async save() {
      try {
        const body = {
          nama_kelas: this.classname,
          jurusan: this.jurusan,
          angkatan: this.angkatan,
        }

        let conf = { headers: { Authorization: 'Bearer ' + this.key } }
        await this.axios.post('http://127.0.0.1:8000/api/inputkelas', body, conf)
        this.list()
        this.dialog = false
        this.snackbar = true
        this.message = 'Created Successfully'
      } catch (err) {
        console.log(err)
      }
    },

    async saveEdit() {
      try {
        const body = {
          nama_kelas: this.classname,
          jurusan: this.jurusan,
          angkatan: this.angkatan,
        }

        let conf = { headers: { Authorization: 'Bearer ' + this.key } }
        await this.axios.put(`http://127.0.0.1:8000/api/updatekelas/${this.id}`, body, conf)
        this.list()
        this.dialogEdit = false
        this.snackbar = true
        this.message = 'Edit Successfully'
      } catch (err) {
        console.log(err)
      }
    },

    setEditValue(record) {
      this.id = record.id_kelas
      this.classname = record.nama_kelas
      this.jurusan = record.jurusan
      this.angkatan = record.angkatan

      this.dialogEdit = true
    },

    setDelete(record) {
      this.id = record.id_kelas
      this.dialogDelete = true
    },

    async deleteItem() {
      try {
        let conf = { headers: { Authorization: 'Bearer ' + this.key } }
        await this.axios.delete(`http://127.0.0.1:8000/api/deletekelas/${this.id}`, conf)
        this.list()
        this.dialogDelete = false
      } catch (err) {
        console.log(err)
      }
    },
  },
}
</script>
