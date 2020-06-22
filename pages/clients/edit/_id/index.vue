<template>
  <div>
    <bs-bread-crumb :items="items" />
    <v-main class="mt-5">
      <v-row>
        <v-col md="12">
          <v-card class="col-md-12" elevation="4">
            <v-card-title>
              <v-icon>mdi-plus</v-icon>
              <span>Novo Cliente</span>
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
                      v-model="phone"
                      type="number"
                      label="Telefone"
                    />
                  </v-col>

                  <v-col v-if="isUserAdmin" cols="12" sm="12" md="6">
                    <v-select v-model="userSelected" :items="users" label="Profissionais" />
                    <small v-if="userError" class="red--text">
                      * Este campo é obrigatório
                    </small>
                  </v-col>
                </v-row>
                <v-btn type="submit" class="primary">
                  Salvar
                </v-btn>
                <v-btn class="default" to="/appointments">
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
      users: [],
      name: '',
      email: '',
      phone: '',
      userSelected: '',
      userError: false,
      alert: false,
      items: [
        {
          text: 'Home',
          disabled: false,
          to: '/home'
        },
        {
          text: 'Clientes',
          to: '/clients'
        },
        {
          text: 'Editar',
          disabled: true
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
    }
  },
  created () {
    this.$store.dispatch('retrieveLoggedUser').then(() => {
      this.isAdmin()
    })
    this.getClient()
  },
  methods: {
    isAdmin () {
      if (this.$store.state.me.accessType === 'admin') {
        this.isUserAdmin = true
        this.getUsers()
      }
    },
    async getUsers () {
      const token = localStorage.getItem('token')
      const config = {
        headers: { Authorization: `Bearer ${token}` }
      }

      await this.$axios.$get(process.env.baseUrl + '/users', config).then((response) => {
        this.users = response.map((res) => {
          return {
            text: res.name,
            value: res.id
          }
        })
      }).catch()
    },
    async getClient () {
      const token = localStorage.getItem('token')
      const config = {
        headers: { Authorization: `Bearer ${token}` }
      }

      const id = this.$route.params.id
      await this.$axios.$get(process.env.baseUrl + '/clients/' + id, config).then((response) => {
        this.name = response.name
        this.email = response.email
        this.phone = response.phone

        if (this.isUserAdmin) {
          this.userSelected = response.user_id
        }
      }).catch()
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
        phone: this.phone,
        user_id: this.isUserAdmin ? this.userSelected : this.$store.state.me.id
      }

      if (data.user_id) {
        this.userError = false
        const id = this.$route.params.id
        await this.$axios.$put(process.env.baseUrl + '/clients/' + id, data, config).then((response) => {
          if (response.id) {
            this.alert = true
            setTimeout(() => {
              this.alert = false
            }, 3000)
          }
        }).catch()
      } else if (this.isUserAdmin) {
        this.userError = true
      }
    }
  }
}
</script>
