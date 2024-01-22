<template lang="pug">
.login-page.text-center.mx-auto
  v-card.pa-1.rounded-xl.background4(outlined)
    v-card.pa-1.rounded-xl.background3(outlined)
      v-card.pa-1.rounded-xl.background2(outlined)
        v-card.pa-5.rounded-xl.background(outlined width="600")
          v-img.welcome-image.mx-auto(:src="logoPath" width="150")
          h2.pt-6 Login as Admin
          p Do not have account?
            |
            nuxt-link(to="register").pa-1 Sign up here
          v-text-field(
            label="Email"
            placeholder="example1@gmail.com"
            v-model="email"
            filled
            rounded
            dense
          )
          v-text-field(
            :append-icon="show ? 'mdi-eye' : 'mdi-eye-off'"
            :rules="[rules.required, rules.min]"
            :type="show ? 'text' : 'password'"
            name="password"
            label="Password"
            hint="At least 8 characters"
            v-model="password"
            :error-messages="passwordErrorMessages"
            filled
            rounded
            dense
            @click:append="show = !show"
          )
          v-btn(
            @click="login"
            depressed
            rounded
            color="primary"
          ) Login
</template>

<script>
import { mapGetters, mapActions } from 'vuex'

export default {
  name: 'LoginPage',
  components: {
  },
  layout: 'welcome',
  data () {
    return {
      logoPath: require('../assets/user/login.png'),
      show: false,
      email: null,
      password: null,
      passwordErrorMessages: [],
      rules: {
        required: value => !!value || 'Required.',
        min: v => (v && v.length >= 8) || 'Min 8 characters'
      }
    }
  },
  computed: {
    ...mapGetters({
      authUser: 'auth/getAuthUser'
    })
  },
  methods: {
    ...mapActions({
      addTokenToAxiosHeader: 'auth/addTokenToAxiosHeader',
      setAuthUser: 'auth/setAuthUser',
      storeTokenInLocalStorage: 'auth/storeTokenInLocalStorage'
    }),
    async login () {
      // Clear previous error messages
      this.passwordErrorMessages = []

      try {
        const response = await this.$axios.post('/api/users/login/', {
          email: this.email,
          password: this.password
        })

        const user = {
          id: response.data.id,
          token: response.data.token,
          username: response.data.username
        }

        this.addTokenToAxiosHeader(user.token)

        this.setAuthUser(user)
        this.storeTokenInLocalStorage(user.token)

        console.log('Login successful: ', response)
        this.$router.push({ path: '/dashboard' })
      } catch (error) {
        if (error.response && error.response.data && error.response.data.errors) {
          // If the server responds with error messages
          this.passwordErrorMessages.push(...error.response.data.errors.password)
        } else {
          // Generic error message for other errors
          this.passwordErrorMessages.push('Login failed. Please try again.')
        }
      }
    }
  }
}
</script>

<style scoped>
:deep(.scroll) {
  overflow-x: hidden;
  overflow-y: auto;
  width: 100% !important;
}

:deep(.v-input__slot) {
  background-color: rgba(250, 175, 8, 0.2) !important;
}

.background{
  background-color: #fff9ec !important;
  border: solid 4px white;
}

.background2{
  background-color: #ffda8a !important;
  border: solid 4px white;
}

.background3{
  background-color: #ffc954 !important;
  border: solid 4px white;
}

.background4{
  background-color: #FAAF08 !important;
  border: solid 4px white;
}

:deep(input:-internal-autofill-selected) {
  background-color: transparent !important;
}

</style>
