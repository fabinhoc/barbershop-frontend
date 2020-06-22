<template>
  <div>
    <v-card class="pa-0">
      <v-breadcrumbs :items="items" class="ma-0 pa-2">
        <template v-slot:divider>
          <v-icon>mdi-forward</v-icon>
        </template>
      </v-breadcrumbs>
    </v-card>
  </div>
</template>

<script>
export default {
  name: 'BsBreadCrumb',
  props: {
    items: {
      type: Array,
      default: Array
    }
  }
}
</script>
