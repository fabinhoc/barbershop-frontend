<template>
  <div>
    <bs-bread-crumb :items="items" />
    <v-btn class="success mt-5" to="/clients/new">
      <v-icon>mdi-plus</v-icon>
      Novo Cliente
    </v-btn>
    <v-data-table
      :headers="headers"
      :items="clients"
      :items-per-page="5"
      class="elevation-6 mt-6"
    >
      <template v-slot:item.action="{ item }">
        <v-btn icon :to="'/clients/edit/' + item.id">
          <v-icon class="primary--text">mdi-pencil</v-icon>
        </v-btn>
        <v-btn icon>
          <v-icon class="red--text" @click="remove(item.id)">
            mdi-trash-can-outline
          </v-icon>
        </v-btn>
      </template>
    </v-data-table>
  </div>
</template>

<script>
import BsBreadCrumb from '~/components/BsBreadCrumb'

export default {
  layout: 'main',
  components: {
    BsBreadCrumb
  },
  data () {
    return {
      headers: [],
      clients: [],
      items: [
        {
          text: 'Home',
          disabled: false,
          to: '/home'
        },
        {
          text: 'Clientes',
          to: '/clients',
          disabled: true
        }
      ]
    }
  },
  created () {
    this.getClients()
  },
  methods: {
    async remove (id) {
      const token = localStorage.getItem('token')
      const config = {
        headers: { Authorization: `Bearer ${token}` }
      }

      await this.$axios.$delete(process.env.baseUrl + '/clients/' + id, config).then((response) => {
        this.getClients()
      })
    },
    async getClients () {
      const token = localStorage.getItem('token')
      const config = {
        headers: { Authorization: `Bearer ${token}` }
      }

      await this.$axios.$get(process.env.baseUrl + '/clients', config).then((response) => {
        this.headers = [
          {
            text: '#',
            align: 'start',
            sortable: true,
            value: 'id'
          },
          {
            text: 'Nome',
            align: 'start',
            sortable: true,
            value: 'name'
          },
          {
            text: 'Email',
            align: 'start',
            sortable: true,
            value: 'email'
          },
          {
            text: 'Telefone',
            align: 'start',
            sortable: true,
            value: 'phone'
          },
          {
            text: 'Ações',
            align: 'start',
            sortable: true,
            value: 'action'
          }
        ]
        this.clients = response.map((res) => {
          return {
            id: res.id,
            name: res.name,
            email: res.email,
            phone: res.phone
          }
        })
      })
    }
  }
}
</script>
