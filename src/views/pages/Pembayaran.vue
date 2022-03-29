<template>
  <div>
    <vue-html2pdf
      :show-layout="false"
      :float-layout="true"
      :enable-download="true"
      :preview-modal="true"
      :paginate-elements-by-height="1400"
      filename="hee hee"
      :pdf-quality="2"
      :manual-pagination="false"
      pdf-format="a4"
      pdf-orientation="landscape"
      pdf-content-width="800px"
      @progress="onProgress($event)"
      @hasStartedGeneration="hasStartedGeneration()"
      @hasGenerated="hasGenerated($event)"
      ref="html2Pdf"
    >
      <section slot="pdf-content" style="text-align: center">
        <!-- PDF Content Here -->
        <v-img
          :src="require('@/assets/images/logos/logo.svg')"
          max-height="30px"
          max-width="30px"
          alt="logo"
          contain
          class="me-3"
        ></v-img>

        <h2 class="text-2xl font-weight-semibold">MY SPP</h2>

        <div class="h2 grey--text text-center text-darken-1" style="padding-top: 5px; margin-bottom: 50px">
          Laporan Pembayaran Siswa
        </div>
        <v-data-table class="mt-5" hide-default-footer :headers="headersInvoice" :items="items"></v-data-table>
      </section>
    </vue-html2pdf>

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
          <spacer></spacer>
          <v-btn color="primary" dark @click="generateLaporan">Cetak</v-btn>

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
  </div>
</template>

<script>
import { mdiDeleteOutline, mdiPencil, mdiCalendar } from '@mdi/js'
import VueHtml2pdf from 'vue-html2pdf'

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

      headersInvoice: [
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

  components: {
    VueHtml2pdf,
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

    generateLaporan() {
      this.$refs.html2Pdf.generatePdf()
    },
  },
}
</script>

<style scoped>
body {
  font-family: 'Helvetica Neue', 'Helvetica', Helvetica, Arial, sans-serif;
  text-align: center;
  color: #777;
}
body h1 {
  font-weight: 300;
  margin-bottom: 0px;
  padding-bottom: 0px;
  color: #000;
}
body h3 {
  font-weight: 300;
  margin-top: 10px;
  margin-bottom: 20px;
  font-style: italic;
  color: #555;
}
body a {
  color: #06f;
}
.invoice-box {
  max-width: 800px;
  margin: auto;
  padding: 30px;
  border: 1px solid #eee;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.15);
  font-size: 16px;
  line-height: 24px;
  font-family: 'Helvetica Neue', 'Helvetica', Helvetica, Arial, sans-serif;
  color: #555;
}
.invoice-box table {
  width: 100%;
  line-height: inherit;
  text-align: left;
  border-collapse: collapse;
}
.invoice-box table td {
  padding: 5px;
  vertical-align: top;
}
.invoice-box table tr td:nth-child(2) {
  text-align: right;
}
.invoice-box table tr.top table td {
  padding-bottom: 20px;
}
.invoice-box table tr.top table td.title {
  font-size: 45px;
  line-height: 45px;
  color: #333;
}
.invoice-box table tr.information table td {
  padding-bottom: 40px;
}
.invoice-box table tr.heading td {
  background: #eee;
  border-bottom: 1px solid #ddd;
  font-weight: bold;
}
.invoice-box table tr.details td {
  padding-bottom: 20px;
}
.invoice-box table tr.item td {
  border-bottom: 1px solid #eee;
}
.invoice-box table tr.item.last td {
  border-bottom: none;
}
.invoice-box table tr.total td:nth-child(2) {
  border-top: 2px solid #eee;
  font-weight: bold;
}
@media only screen and (max-width: 600px) {
  .invoice-box table tr.top table td {
    width: 100%;
    display: block;
    text-align: center;
  }
  .invoice-box table tr.information table td {
    width: 100%;
    display: block;
    text-align: center;
  }
}
</style>
