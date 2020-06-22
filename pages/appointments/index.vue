<template>
  <div>
    <bs-bread-crumb :items="items" />
    <v-main class="mt-5">
      <v-row>
        <v-col md="12">
          <v-btn class="success" to="/appointments/new">
            <v-icon>mdi-plus</v-icon>
            Novo Agendamento
          </v-btn>
        </v-col>
      </v-row>
      <v-row v-if="appointments != ''">
        <v-col v-for="appointment in appointments" :key="appointment.id" md="6">
          <bs-card-appointment @list="getAppointments" :detail="appointment" />
        </v-col>
      </v-row>
      <v-row v-else>
        <v-col md="12">
          <p>Nenhum agendamento registrado!</p>
        </v-col>
      </v-row>
    </v-main>
  </div>
</template>

<script>
import BsBreadCrumb from '~/components/BsBreadCrumb'
import BsCardAppointment from '~/components/BsCardAppointment'

export default {
  layout: 'main',
  components: {
    BsBreadCrumb,
    BsCardAppointment
  },
  data () {
    return {
      items: [
        {
          text: 'Home',
          disabled: false,
          to: '/home'
        },
        {
          text: 'Agendamentos',
          disabled: true
        }
      ],
      appointments: {}
    }
  },
  created () {
    this.getAppointments()
  },
  methods: {
    async getAppointments () {
      const token = localStorage.getItem('token')
      const config = {
        headers: { Authorization: `Bearer ${token}` }
      }
      await this.$axios.$get(process.env.baseUrl + '/appointments', config).then((response) => {
        this.appointments = response
      })
    }
  }
}
</script>
