<template>
  <div class="auth-wrapper auth-v1">
    <template v-if="isLogin === ''">
      <v-btn color="primary" class="mr-3" @click="isLogin = 'admin'">Admin</v-btn>
      <v-btn color="primary" class="mr-3 ml-3" @click="isLogin = 'admin'">Petugas</v-btn>
      <v-btn color="primary" class="ml-3" @click="isLogin = 'siswa'">Siswa</v-btn>
    </template>
    <template v-if="isLogin !== ''">
      <div class="auth-inner">
        <v-card class="auth-card">
          <!-- logo -->
          <v-card-title class="d-flex align-center justify-center py-7">
            <router-link to="/" class="d-flex align-center">
              <v-img
                :src="require('@/assets/images/logos/logo.svg')"
                max-height="30px"
                max-width="30px"
                alt="logo"
                contain
                class="me-3"
              ></v-img>

              <h2 class="text-2xl font-weight-semibold">MY SPP</h2>
            </router-link>
          </v-card-title>

          <!-- title -->
          <v-card-text>
            <p class="text-2xl font-weight-semibold text--primary mb-2">Welcome to MY SPP! 👋🏻</p>
            <p class="mb-2">Please sign-in to your account and start the adventure</p>
          </v-card-text>

          <!-- login form -->
          <v-card-text>
            <v-form>
              <v-text-field
                v-model="email"
                outlined
                label="Email"
                placeholder="john@example.com"
                hide-details
                class="mb-3"
              ></v-text-field>

              <v-text-field
                v-model="password"
                outlined
                :type="isPasswordVisible ? 'text' : 'password'"
                label="Password"
                placeholder="············"
                :append-icon="isPasswordVisible ? icons.mdiEyeOffOutline : icons.mdiEyeOutline"
                hide-details
                @click:append="isPasswordVisible = !isPasswordVisible"
              ></v-text-field>

              <div class="d-flex align-center justify-space-between flex-wrap">
                <v-checkbox label="Remember Me" hide-details class="me-3 mt-1"> </v-checkbox>

                <!-- forgot link -->
                <a href="javascript:void(0)" class="mt-1"> Forgot Password? </a>
              </div>

              <v-btn block color="primary" class="mt-6" @click="login"> Login </v-btn>
            </v-form>
          </v-card-text>
        </v-card>
      </div>
    </template>

    <!-- background triangle shape  -->
    <img
      class="auth-mask-bg"
      height="173"
      :src="require(`@/assets/images/misc/mask-${$vuetify.theme.dark ? 'dark' : 'light'}.png`)"
    />

    <!-- tree -->
    <v-img class="auth-tree" width="247" height="185" src="@/assets/images/misc/tree.png"></v-img>

    <!-- tree  -->
    <v-img class="auth-tree-3" width="377" height="289" src="@/assets/images/misc/tree-3.png"></v-img>

    <v-snackbar v-model="snackbar">
      {{ message }}

      <template v-slot:action="{ attrs }">
        <v-btn color="pink" text v-bind="attrs" @click="snackbar = false"> Close </v-btn>
      </template>
    </v-snackbar>
  </div>
</template>

<script>
// eslint-disable-next-line object-curly-newline
import { mdiFacebook, mdiTwitter, mdiGithub, mdiGoogle, mdiEyeOutline, mdiEyeOffOutline } from '@mdi/js'
import { ref } from '@vue/composition-api'
import { appConfig } from '../../Config/app'

export default {
  setup() {
    const isPasswordVisible = ref(false)
    const email = ref('')
    const password = ref('')
    const socialLink = [
      {
        icon: mdiFacebook,
        color: '#4267b2',
        colorInDark: '#4267b2',
      },
      {
        icon: mdiTwitter,
        color: '#1da1f2',
        colorInDark: '#1da1f2',
      },
      {
        icon: mdiGithub,
        color: '#272727',
        colorInDark: '#fff',
      },
      {
        icon: mdiGoogle,
        color: '#db4437',
        colorInDark: '#db4437',
      },
    ]

    return {
      isLogin: '',
      isPasswordVisible,
      email,
      password,
      socialLink,

      message: '',
      snackbar: false,

      icons: {
        mdiEyeOutline,
        mdiEyeOffOutline,
      },
    }
  },

  methods: {
    async login() {
      try {
        if (this.isLogin == 'siswa') {
          let body = {
            username: this.email,
            password: this.password,
          }
          let res = await this.axios.post(appConfig.apiUrl + '/login_siswa', body)
          console.log({ res })
          localStorage.setItem('Authorization', res.data.data.token)
          localStorage.setItem('isSiswa', 'true')
          localStorage.setItem('name', res.data.data.user.nama)
          localStorage.setItem('nisn', res.data.data.user.nisn)
          this.$router.push('/dashboard')
        } else {
          let body = {
            email: this.email,
            password: this.password,
          }
          let res = await this.axios.post(appConfig.apiUrl + '/login', body)

          localStorage.setItem('Authorization', res.data.data.token)
          localStorage.setItem('isSiswa', 'false')
          localStorage.setItem('name', res.data.data.user.name)
          localStorage.setItem('role', res.data.data.user.role)
          localStorage.setItem('id_petugas', res.data.data.user.id_petugas)
          this.$router.push('/dashboard')
        }
        console.log({ res })
      } catch (err) {
        this.snackbar = true
        this.message = 'Username or Password is incorrect!'
        // console.log(err.response.data.error)
      }
    },
  },
}
</script>

<style lang="scss">
@import '~@/plugins/vuetify/default-preset/preset/pages/auth.scss';
</style>
