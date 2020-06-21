<template>
  <div class="centered-div">
    <v-card class="col-md-4" elevation="12">
      <div class="text-center">
        <logo />
      </div>
      <h1 class="text-center font-weight-light">
        Barbershop
      </h1>
      <div class="text-center">
        <small>Bem vindo!</small>
      </div>
      <v-divider />
      <v-card-text>
        <v-row>
          <v-col cols="12" sm="12" md="12">
            <div class="text-center">
              <span v-if="authError" class="red--text">
                {{ authError }}
              </span>
            </div>
            <v-form id="check-login-form" @submit.prevent="login">
              <v-col cols="12" sm="12" md="12">
                <v-text-field
                  v-model.trim="$v.email.$model"
                  label="Email"
                />
                <small v-if="!$v.email.required && $v.email.$dirty" class="red--text">
                  * Este campo é obrigatório
                </small>
              </v-col>
              <v-col cols="12" sm="12" md="12">
                <v-text-field
                  v-model.trim="$v.password.$model"
                  type="password"
                  label="Senha"
                />
                <small v-if="!$v.password.required && $v.password.$dirty" class="red--text">
                  * Este campo é obrigatório
                </small>
              </v-col>
            </v-form>
          </v-col>
        </v-row>
      </v-card-text>
      <v-card-actions>
        <v-btn
          color="primary"
          type="submit"
          block
          :loading="loading"
          :disabled="loading"
          form="check-login-form"
        >
          Entrar
        </v-btn>
      </v-card-actions>
    </v-card>
  </div>
</template>

<script>
import { required, email } from 'vuelidate/lib/validators'
import Logo from '~/components/Logo.vue'

export default {
  components: {
    Logo
  },
  data () {
    return {
      email: '',
      password: '',
      authError: '',
      loading: false
    }
  },
  validations: {
    email: {
      required,
      email
    },
    password: {
      required
    }
  },
  methods: {
    login () {
      this.$v.$touch()
      this.authError = null
      this.loading = true

      if (this.$v.$invalid) {
        this.loading = false
        return false
      }

      this.$store.dispatch('retrieveToken', { email: this.email, password: this.password })
        .then((response) => {
          this.loading = false
          window.location.href = '/home'
        })
        .catch(() => {
          this.authError = 'Failed Login'
          this.loading = false
        })
    }
  }
}
</script>

<style lang="css">
  .centered-div {
    display:flex;
    justify-content:center;
    align-items:center;
    position:absolute;
    width:90%;
    height:100%;
  }
  html, body #app {
    height: 100%;
    width: 100%;
    background-color: #7B1FA2;
    background-image: linear-gradient(322, 158, 12, 179, 100);
  }
  .v-footer .v-sheet .theme--light .v-footer--absolute {
    background-color: #7B1FA2;
  }
</style>
