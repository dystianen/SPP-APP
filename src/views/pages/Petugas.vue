<template>
  <!--Start Data Table -->
  <v-data-table :headers="headers" :items="items">
    <template v-slot:top>
      <v-toolbar flat>
        <v-toolbar-title>Petugas</v-toolbar-title>
        <v-divider class="mx-4" inset vertical></v-divider>
        <v-spacer></v-spacer>

        <v-dialog v-model="dialog" max-width="500px" persistent>
          <template v-slot:activator="{ on, attrs }">
            <v-btn color="primary" dark v-bind="attrs" v-on="on"> Create</v-btn>
          </template>
          <v-card>
            <v-card-title>
              <span>Create Petugas</span>
            </v-card-title>
            <v-card-text>
              <v-container>
                <v-row>
                  <v-col cols="12">
                    <v-text-field label="Username*" v-model="username" required></v-text-field>
                  </v-col>
                  <v-col cols="12">
                    <v-text-field type="password" label="Password*" v-model="password" required></v-text-field>
                  </v-col>
                  <v-col cols="12">
                    <v-text-field label="Nama Petugas*" v-model="nama_petugas" required></v-text-field>
                  </v-col>
                  <v-col cols="12">
                    <v-select label="Level/Role*" v-model="role" :items="items_role" required> </v-select>
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
              <span>Edit Petugas</span>
            </v-card-title>
            <v-card-text>
              <v-container>
                <v-row>
                  <v-col cols="12">
                    <v-text-field label="Username*" v-model="username" required></v-text-field>
                  </v-col>
                  <v-col cols="12">
                    <v-text-field type="password" label="Password*" v-model="password" required></v-text-field>
                  </v-col>
                  <v-col cols="12">
                    <v-text-field label="Nama Petugas*" v-model="nama_petugas" required></v-text-field>
                  </v-col>
                  <v-col cols="12">
                    <v-select label="Level/Role*" v-model="role" :items="items_role" required> </v-select>
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
import { mdiDeleteOutline, mdiPencil } from '@mdi/js'
import { appConfig } from '../../Config/app'

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
          text: 'ID Petugas',
          value: 'id_petugas',
          align: 'left',
          width: '20px',
        },
        {
          text: 'Nama Petugas',
          value: 'nama_petugas',
          align: 'left',
          width: '100px',
        },
        {
          text: 'Email',
          value: 'email',
          align: 'left',
          width: '20px',
        },
        {
          text: 'Role',
          value: 'level',
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
      username: '',
      password: '',
      nama_petugas: '',
      role: '',
      items_role: ['admin', 'petugas'],

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

    async list() {
      try {
        let conf = { headers: { Authorization: 'Bearer ' + this.key } }
        const res = await this.axios.get('http://127.0.0.1:8000/api/getpetugas', conf)
        console.log(res.data)
        this.items = res.data.data
      } catch (err) {
        console.log(err)
      }
    },

    async save() {
      try {
        const body = {
          email: this.username,
          password: this.password,
          nama_petugas: this.nama_petugas,
          level: this.role,
        }

        let conf = { headers: { Authorization: 'Bearer ' + this.key } }
        await this.axios.post(appConfig.apiUrl + '/inputpetugas', body, conf)
        this.list()
        this.dialog = false
        this.snackbar = true
        this.message = 'Created Successfully'
      } catch (err) {
        console.log(err)
      }
    },

    setEditValue(record) {
      this.id = record.id_petugas
      this.username = record.email
      this.password = record.password
      this.nama_petugas = record.nama_petugas
      this.role = record.level

      this.dialogEdit = true
    },

    reset() {
      this.id = ''
      this.username = ''
      this.password = ''
      this.nama_petugas = ''
      this.role = ''

      this.dialogEdit = false
    },

    async saveEdit() {
      try {
        const body = {
          email: this.username,
          password: this.password,
          nama_petugas: this.nama_petugas,
          level: this.role,
        }

        let conf = { headers: { Authorization: 'Bearer ' + this.key } }
        await this.axios.put(appConfig.apiUrl + `/updatepetugas/${this.id}`, body, conf)
        this.list()
        this.dialogEdit = false
        this.snackbar = true
        this.message = 'Edit Successfully'
      } catch (err) {
        console.log(err)
      }
    },

    setDelete(record) {
      this.id = record.id_petugas
      this.dialogDelete = true
    },

    async deleteItem() {
      try {
        let conf = { headers: { Authorization: 'Bearer ' + this.key } }
        await this.axios.delete(appConfig.apiUrl + `/deletepetugas/${this.id}`, conf)
        this.list()
        this.dialogDelete = false
      } catch (err) {
        console.log(err)
      }
    },
  },
}
</script>
