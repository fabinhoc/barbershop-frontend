<template>
  <div>
    <bs-bread-crumb :items="items" />
    <v-btn class="success mt-5" to="/users/new">
      <v-icon>mdi-plus</v-icon>
      Novo Profissional
    </v-btn>
    <v-data-table
      :headers="headers"
      :items="users"
      :items-per-page="5"
      class="elevation-6 mt-6"
    >
      <template v-slot:item.action="{ item }">
        <v-btn icon :to="'/users/edit/' + item.id">
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
      users: [],
      items: [
        {
          text: 'Home',
          disabled: false,
          to: '/home'
        },
        {
          text: 'Profissionais',
          to: '/users',
          disabled: true
        }
      ]
    }
  },
  created () {
    this.getUsers()
  },
  methods: {
    async remove (id) {
      const token = localStorage.getItem('token')
      const config = {
        headers: { Authorization: `Bearer ${token}` }
      }

      await this.$axios.$delete(process.env.baseUrl + '/users/' + id, config).then((response) => {
        this.getUsers()
      })
    },
    async getUsers () {
      const token = localStorage.getItem('token')
      const config = {
        headers: { Authorization: `Bearer ${token}` }
      }

      await this.$axios.$get(process.env.baseUrl + '/users', config).then((response) => {
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
            text: 'Perfil',
            align: 'start',
            sortable: true,
            value: 'accessType'
          },
          {
            text: 'Ações',
            align: 'start',
            sortable: true,
            value: 'action'
          }
        ]
        this.users = response.map((res) => {
          return {
            id: res.id,
            name: res.name,
            email: res.email,
            accessType: res.accessType
          }
        })
      })
    }
  }
}
</script>
