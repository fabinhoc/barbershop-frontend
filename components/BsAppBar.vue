<template>
  <div>
    <v-app-bar
      color="purple darken-3"
      dense
      dark
      height="70"
    >
      <v-toolbar-title class="font-weight-light">
        BARBERSHOP
        <small>Sistema de agendamentos</small>
      </v-toolbar-title>
      <v-spacer />

      <v-btn icon>
        <v-icon size="30" title="Sair" @click="logout">mdi-logout</v-icon>
      </v-btn>

      <v-btn icon :to="'/users/edit/' + this.$store.state.me.id">
        <v-icon size="40">mdi-account-circle</v-icon>
      </v-btn>
      <div>
        <p class="font-weight-light ma-0 pa-0 mb-n2" style="font-size:16px">Olá</p>
        <span>
          {{ this.$store.state.me.name }}
        </span>
      </div>
    </v-app-bar>
  </div>
</template>

<script>
export default {
  name: 'BsAppBar',
  methods: {
    logout () {
      this.$store.dispatch('logoutUser').then((response) => {
        if (response) {
          this.$router.push('/')
        }
      }).catch((error) => {
        // eslint-disable-next-line no-console
        console.log(error)
        this.$router.push('/')
      })
    }
  }
}
</script>
