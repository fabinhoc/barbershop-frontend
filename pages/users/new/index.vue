<template>
  <div>
    <bs-bread-crumb :items="items" />
    <v-main class="mt-5">
      <v-row>
        <v-col md="12">
          <v-card class="col-md-12" elevation="4">
            <v-card-title>
              <v-icon>mdi-plus</v-icon>
              <span>Novo Professional</span>
            </v-card-title>
            <v-card-text>
              <v-row>
                <v-col>
                  <v-alert
                    :value="alert"
                    color="primary"
                    dark
                    border="top"
                    icon="mdi-check-all"
                    transition="scale-transition"
                  >
                    Salvo com sucesso!
                  </v-alert>
                </v-col>
              </v-row>
              <v-form id="check-login-form" @submit.prevent="save">
                <v-row>
                  <v-col cols="12" sm="12" md="6">
                    <v-text-field
                      v-model.trim="$v.name.$model"
                      label="Nome"
                    />
                    <small v-if="!$v.name.required && $v.name.$dirty" class="red--text">
                      * Este campo é obrigatório
                    </small>
                  </v-col>

                  <v-col cols="12" sm="12" md="6">
                    <v-text-field
                      v-model.trim="$v.email.$model"
                      label="Email"
                    />
                    <small v-if="!$v.email.required && $v.email.$dirty" class="red--text">
                      * Este campo é obrigatório
                    </small>
                    <small v-if="!$v.email.email && $v.email.$dirty" class="red--text">
                      * Este campo deve ser um e-mail válido
                    </small>
                  </v-col>
                </v-row>

                <v-row>
                  <v-col cols="12" sm="12" md="6">
                    <v-text-field
                      v-model.trim="$v.password.$model"
                      type="password"
                      label="Password"
                    />
                    <small v-if="!$v.password.required && $v.password.$dirty" class="red--text">
                      * Este campo é obrigatório
                    </small>
                  </v-col>

                  <v-col v-if="isUserAdmin" cols="12" sm="12" md="6">
                    <v-select v-model.trim="$v.accessType.$model" :items="permissions" label="Perfil" />
                    <small v-if="!$v.accessType.required && $v.accessType.$dirty" class="red--text">
                      * Este campo é obrigatório
                    </small>
                  </v-col>
                </v-row>

                <v-btn type="submit" class="primary">
                  Salvar
                </v-btn>
                <v-btn class="default" to="/">
                  Cancelar
                </v-btn>
              </v-form>
            </v-card-text>
          </v-card>
        </v-col>
      </v-row>
    </v-main>
  </div>
</template>

<script>
import { required, email } from 'vuelidate/lib/validators'
import BsBreadCrumb from '~/components/BsBreadCrumb'

export default {
  layout: 'main',
  components: {
    BsBreadCrumb
  },
  data () {
    return {
      isUserAdmin: false,
      name: '',
      email: '',
      password: '',
      accessType: '',
      alert: false,
      items: [
        {
          text: 'Home',
          disabled: false,
          to: '/home'
        },
        {
          text: 'Profissionais',
          to: '/users'
        },
        {
          text: 'Novo',
          disabled: true
        }
      ],
      permissions: [
        {
          text: 'Profissional',
          value: 'employee'
        },
        {
          text: 'Admin',
          value: 'admin'
        }
      ]
    }
  },
  validations: {
    name: {
      required
    },
    email: {
      required,
      email
    },
    password: {
      required
    },
    accessType: {
      required
    }
  },
  created () {
    this.$store.dispatch('retrieveLoggedUser').then(() => {
      this.isAdmin()
    })
  },
  methods: {
    isAdmin () {
      if (this.$store.state.me.accessType === 'admin') {
        this.isUserAdmin = true
      }
    },
    async save (event) {
      this.$v.$touch()

      if (this.$v.$invalid) {
        this.loading = false
        return false
      }

      const token = localStorage.getItem('token')
      const config = {
        headers: { Authorization: `Bearer ${token}` }
      }

      const data = {
        name: this.name,
        email: this.email,
        password: this.password,
        accessType: this.accessType
      }

      await this.$axios.$post(process.env.baseUrl + '/users', data, config).then((response) => {
        if (response.id) {
          this.alert = true
          setTimeout(() => {
            this.alert = false
          }, 3000)
          event.target.reset()
        }
      }).catch()
    }
  }
}
</script>
