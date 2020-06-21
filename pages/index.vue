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
            <v-text-field
              label="Email"
            />
          </v-col>
          <v-col cols="12" sm="12" md="12">
            <v-text-field
              label="Senha"
            />
          </v-col>
        </v-row>
      </v-card-text>
      <v-card-actions>
        <v-spacer />
        <v-btn
          color="primary"
          nuxt
          to="/inspire"
          block
        >
          Entrar
        </v-btn>
      </v-card-actions>
    </v-card>
  </div>
</template>

<script>
import Logo from '~/components/Logo.vue'

export default {
  components: {
    Logo
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
