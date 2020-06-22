<template>
  <div>
    <bs-bread-crumb :items="items" />
    <v-main class="mt-5">
      <v-row>
        <v-col v-for="cardValue in cardValues" :key="cardValue.key" md="3" class="md-3 text-center">
          <bs-card-nav :data="cardValue" />
        </v-col>
      </v-row>
    </v-main>
  </div>
</template>

<script>
import BsBreadCrumb from '~/components/BsBreadCrumb'
import BsCardNav from '~/components/BsCardNav'

export default {
  layout: 'main',
  components: {
    BsBreadCrumb,
    BsCardNav
  },
  data () {
    return {
      items: [
        {
          text: 'Home',
          disabled: true
        }
      ],
      cardValues: [
        {
          key: 1,
          title: 'Agendamentos',
          url: '/appointments',
          icon: 'mdi-timer',
          color: '#e8578e'
        },
        {
          key: 2,
          title: 'Clientes',
          url: '/clients',
          icon: 'mdi-account-circle',
          color: 'blue-grey darken-2'
        },
        {
          key: 3,
          title: 'Novo Agendamento',
          url: '/appointments/new',
          icon: 'mdi-calendar-plus',
          color: 'deep-orange darken-3'
        }
      ]
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
        const cardAdmin = {
          key: 4,
          title: 'Profissionais',
          url: '/users',
          icon: 'mdi-account-tie',
          color: 'light-blue darken-3'
        }
        this.cardValues.push(cardAdmin)
      }
    }
  }
}
</script>
