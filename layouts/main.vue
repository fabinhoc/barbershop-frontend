<template>
  <v-app>
    <bs-app-bar />
    <v-main>
      <v-container>
        <nuxt />
      </v-container>
    </v-main>
  </v-app>
</template>

<script>
import BsAppBar from '~/components/BsAppBar'

export default {
  components: {
    BsAppBar
  },
  data () {
    return {
      fixed: false
    }
  },
  created () {
    this.loggedUser()
    this.loggedIn()
  },
  methods: {
    loggedUser () {
      this.$store.dispatch('retrieveLoggedUser').then((response) => {
        if (!response) {
          this.$router.push('/')
        }
      }).catch((error) => {
        // eslint-disable-next-line no-console
        console.log(error)
        this.$router.push('/')
      })
    },
    loggedIn () {
      if (!this.$store.getters.loggedIn) {
        // eslint-disable-next-line vue/no-side-effects-in-computed-properties
        this.$router.push('/')
      }
      return true
    }
  }
}
</script>
