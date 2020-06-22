<template>
  <div>
    <v-hover v-slot:default="{ hover }" open-delay="100">
      <v-card
        :to="data.url"
        :color="data.color"
        link
        :elevation="hover ? 20 : 8"
        height="250px"
        dark
      >
        <v-card-text>
          <div class="mb-5">
            <v-icon size="90">
              {{ data.icon }}
            </v-icon>
          </div>
          <v-divider />
          <h2 class="ma-12 font-weight-light">
            {{ data.title }}
          </h2>
        </v-card-text>
      </v-card>
    </v-hover>
  </div>
</template>

<script>
export default {
  name: 'BsCardNav',
  props: {
    data: {
      type: Object,
      default: Object
    }
  }
}
</script>
