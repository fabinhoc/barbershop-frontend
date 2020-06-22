<template>
  <v-card class="pa-0">
    <v-card-text class="pa-2">
      <v-row height="250">
        <v-col md="4">
          <div class="text-center">
            <v-icon size="50">
              mdi-calendar
            </v-icon>
            <v-spacer />
            <div>
              <h3 class="font-weight-light">{{ detail.dateExecution }}</h3>
            </div>
            <v-chip>
              <v-icon size="18" class="mt-n1">
                mdi-timer
              </v-icon>
              <span> {{ detail.initialHour }} - {{ detail.endHour }} </span>
            </v-chip>
          </div>
        </v-col>
        <v-divider md="1" vertical />
        <v-col md="6">
          <h2 class="mb-1 ml-1">
            {{ detail.client.name }}
          </h2>
          <div class="mb-3 font-weight-light">
            <v-icon size="16">
              mdi-phone
            </v-icon>
            <span>{{ detail.client.phone }}</span>
          </div>
          <div>
            <h2 class="font-weight-light">
              {{ detail.serviceName }}
            </h2>
          </div>
          <v-btn icon class="mt-4" @click="remove(detail.id)">
            <v-icon title="remover agendamento" class="red--text">
              mdi-trash-can-outline
            </v-icon>
          </v-btn>
          <v-btn :to="'appointments/edit/' + detail.id" icon class="mt-4">
            <v-icon title="Editar" class="blue--text">
              mdi-pencil
            </v-icon>
          </v-btn>
          <v-btn icon class="mt-4" @click="confirmAppointment(detail.id)">
            <v-icon title="confirmado? SIM/NÃO" :class="detail.isConfirmed ? 'green--text' :'gray--text'">
              mdi-check-all
            </v-icon>
          </v-btn>
        </v-col>
      </v-row>
    </v-card-text>
  </v-card>
</template>

<script>
export default {
  name: 'BsCardAppointment',
  props: {
    detail: {
      type: Object,
      default: Object
    }
  },
  methods: {
    async remove (id) {
      const token = localStorage.getItem('token')
      const config = {
        headers: { Authorization: `Bearer ${token}` }
      }

      await this.$axios.$delete(process.env.baseUrl + '/appointments/' + id, config).then(() => {
        this.$emit('list')
      })
    },
    async confirmAppointment (id) {
      const token = localStorage.getItem('token')
      const config = {
        headers: { Authorization: `Bearer ${token}` }
      }

      const isConf = !this.detail.isConfirmed

      await this.$axios.$put(process.env.baseUrl + '/appointments/' + id, { isConfirmed: isConf }, config).then(() => {
        this.$emit('list')
      })
    }
  }
}
</script>
