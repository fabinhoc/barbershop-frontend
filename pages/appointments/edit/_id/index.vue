<template>
  <div>
    <bs-bread-crumb :items="items" />
    <v-main class="mt-5">
      <v-row>
        <v-col md="12">
          <v-card class="col-md-12" elevation="4">
            <v-card-title>
              <v-icon>mdi-plus</v-icon>
              <span>Editar Agendamento</span>
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
                  <v-col v-if="isUserAdmin" cols="12" sm="12" md="6">
                    <v-select v-model="userSelected" :items="users" label="Profissionais" />
                    <small v-if="userError" class="red--text">
                      * Este campo é obrigatório
                    </small>
                  </v-col>
                </v-row>
                <v-row>
                  <v-col cols="12" sm="12" md="6">
                    <v-text-field
                      v-model.trim="$v.serviceName.$model"
                      label="Serviço"
                    />
                    <small v-if="!$v.serviceName.required && $v.serviceName.$dirty" class="red--text">
                      * Este campo é obrigatório
                    </small>
                  </v-col>

                  <v-col cols="12" sm="12" md="6">
                    <v-select v-model.trim="$v.client.$model" :items="clients" label="Clientes" />
                    <small v-if="!$v.client.required && $v.client.$dirty" class="red--text">
                      * Este campo é obrigatório
                    </small>
                  </v-col>
                </v-row>

                <v-row>
                  <v-col cols="12" sm="12" md="6">
                    <v-text-field
                      v-model.trim="$v.dateExecution.$model"
                      type="date"
                      label="Data"
                    />
                    <small v-if="!$v.dateExecution.required && $v.dateExecution.$dirty" class="red--text">
                      * Este campo é obrigatório
                    </small>
                  </v-col>

                  <v-col cols="12" sm="12" md="3">
                    <v-text-field
                      v-model.trim="$v.initialHour.$model"
                      type="time"
                      label="Hora inicio"
                    />
                    <small v-if="!$v.initialHour.required && $v.initialHour.$dirty" class="red--text">
                      * Este campo é obrigatório
                    </small>
                  </v-col>

                  <v-col cols="12" sm="12" md="3">
                    <v-text-field
                      v-model.trim="$v.endHour.$model"
                      type="time"
                      label="Hora fim"
                    />
                    <small v-if="!$v.endHour.required && $v.endHour.$dirty" class="red--text">
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
import { required } from 'vuelidate/lib/validators'
import BsBreadCrumb from '~/components/BsBreadCrumb'

export default {
  layout: 'main',
  components: {
    BsBreadCrumb
  },
  data () {
    return {
      isUserAdmin: false,
      alert: false,
      users: [],
      userSelected: '',
      userError: false,
      clients: [],
      client: '',
      serviceName: '',
      dateExecution: '',
      initialHour: '',
      endHour: '',
      items: [
        {
          text: 'Home',
          disabled: false,
          to: '/home'
        },
        {
          text: 'Agendamento',
          to: '/appointments'
        },
        {
          text: 'Novo',
          disabled: true
        }
      ]
    }
  },
  validations: {
    serviceName: {
      required
    },
    client: {
      required
    },
    dateExecution: {
      required
    },
    initialHour: {
      required
    },
    endHour: {
      required
    }
  },
  created () {
    this.$store.dispatch('retrieveLoggedUser').then(() => {
      this.isAdmin()
    })
    this.getAppointment()
  },
  methods: {
    async getAppointment () {
      const id = this.$route.params.id
      const token = localStorage.getItem('token')
      const config = {
        headers: { Authorization: `Bearer ${token}` }
      }

      await this.$axios.$get(process.env.baseUrl + '/appointments/' + id, config).then((response) => {
        this.serviceName = response.serviceName
        this.client = response.client_id
        this.dateExecution = response.dateExecution
        this.initialHour = response.initialHour
        this.endHour = response.endHour
        if (this.isUserAdmin) {
          this.userSelected = response.user_id
        }
      })
    },
    isAdmin () {
      if (this.$store.state.me.accessType === 'admin') {
        this.isUserAdmin = true
        this.getUsers()
      }
      this.getClients()
    },
    async getClients () {
      const token = localStorage.getItem('token')
      const config = {
        headers: { Authorization: `Bearer ${token}` }
      }

      await this.$axios.$get(process.env.baseUrl + '/clients', config).then((response) => {
        this.clients = response.map((res) => {
          return {
            text: res.name,
            value: res.id
          }
        })
      }).catch()
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
        serviceName: this.serviceName,
        client_id: this.client,
        dateExecution: this.dateExecution,
        initialHour: this.initialHour,
        endHour: this.endHour,
        user_id: this.isUserAdmin ? this.userSelected : this.$store.state.me.id
      }

      if (data.user_id) {
        this.userError = false
        const id = this.$route.params.id
        await this.$axios.$put(process.env.baseUrl + '/appointments/' + id, data, config).then((response) => {
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
