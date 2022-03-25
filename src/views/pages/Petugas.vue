<template>
  <!--Start Data Table -->
  <div>
    <v-data-table :headers="headers" :items="items">
      <template v-slot:top>
        <v-toolbar flat>
          <v-toolbar-title>Student</v-toolbar-title>
          <v-divider class="mx-4" inset vertical></v-divider>
          <v-spacer></v-spacer>
          <v-dialog v-model="dialog" max-width="500px">
            <template v-slot:activator="{ on, attrs }">
              <v-btn color="primary" dark v-bind="attrs" v-on="on"> Create </v-btn>
            </template>
            <v-card>
              <v-card-title>
                <span class="text-h5">{{ formTitle }}</span>
              </v-card-title>

              <v-card>
                <v-card-title>
                  <span class="text-h5">User Profile</span>
                </v-card-title>
                <v-card-text>
                  <v-container>
                    <v-row>
                      <v-col cols="12">
                        <v-text-field label="NISN*" v-model="nisn" required></v-text-field>
                      </v-col>
                      <v-col cols="12">
                        <v-text-field label="Kelas*" v-model="kelas" required></v-text-field>
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
          <v-dialog v-model="dialogDelete" max-width="500px">
            <v-card>
              <v-card-title class="text-h5">Are you sure you want to delete this item?</v-card-title>
              <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn color="blue darken-1" text @click="closeDelete">Cancel</v-btn>
                <v-btn color="blue darken-1" text @click="deleteItemConfirm">OK</v-btn>
                <v-spacer></v-spacer>
              </v-card-actions>
            </v-card>
          </v-dialog>
        </v-toolbar>
      </template>

      <template>
        <td style="font-weight: 500"></td>
        <td></td>
        <td></td>
        <td></td>
        <td></td>
        <td></td>
        <td class="text-center"></td>
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
export default {
  name: 'student',
  data() {
    return {
      pagination: {
        rowsPerPage: 10,
      },

      // header table
      items: [],
      headers: [
        {
          text: 'No',
          value: 'no',
          align: 'left',
          width: '20px',
        },
        {
          text: 'NISN',
          value: 'nisn',
          align: 'left',
          width: '20px',
        },
        {
          text: 'Kelas',
          value: 'class',
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
          text: 'Alamat',
          value: 'alamat',
          align: 'left',
          width: '20px',
        },
        {
          text: 'No. Telp',
          value: 'no tlp',
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
          align: 'left',
          width: '150px',
        },
      ],

      dialog: false,
      dialogDelete: false,

      // Form
      nisn: '',
      kelas: '',
      nama: '',
      alamat: '',
      phone: '',
      username: '',

      snackbar: false,
      message: '',
    }
  },

  mounted() {
    this.list()
  },

  methods: {
    close() {
      this.dialog = false
      this.$nextTick(() => {
        this.editedIndex = -1
      })
    },

    async list() {
      try {
        await this.axios.get('http://127.0.0.1:8000/getpetugas')
      } catch (err) {
        console.log(err)
      }
    },

    async save() {
      try {
        const body = {
          nisn: this.nisn,
          class: this.kelas,
          name: this.name,
          alamat: this.alamat,
          phone: this.phone,
          username: this.username,
        }

        await this.axios.post('http://127.0.0.1:8000', body)
        this.snackbar = true;
        this.message = 'Created Successfully'
      } catch (err) {
        console.log(err)
      }
    },
  },
}
</script>
