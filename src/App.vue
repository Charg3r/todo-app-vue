<template>
  <v-app id="inspire">
    <v-navigation-drawer
      v-model="drawer"
      app 
    >
      <v-list-item>
        <v-list-item-content>
          <v-list-item-title v-if="loggedUser.name == null" class="text-h6">
            No user
          </v-list-item-title>
          <v-list-item-title v-else class="text-h6">
            {{ loggedUser.name }}
          </v-list-item-title>
          <v-list-item-subtitle v-if="loggedUser.email != null">
            {{ loggedUser.email }}
          </v-list-item-subtitle>
        </v-list-item-content>
      </v-list-item>

      <v-divider></v-divider>

      <v-list
        dense
        nav 
      >
        <v-list-item
          v-for="item in items"
          :key="item.title"
          :to="item.to"
          link >
          <v-list-item-icon>
            <v-icon> {{ item.icon }} </v-icon>
          </v-list-item-icon>
          <v-list-item-content>
            <v-list-item-title> {{ item.title }} </v-list-item-title>
          </v-list-item-content>
        </v-list-item>
      </v-list>
      <template v-slot:append>
        <v-row flex>
          <v-spacer></v-spacer>
          <v-btn icon large class="ma-8 pa-5" @click="toggleDark()">
            <v-icon > mdi-brightness-6 </v-icon>
          </v-btn>
        </v-row>
      </template>
    </v-navigation-drawer>

    <v-app-bar
      color="indigo"
      app
      dark 
    >
      <v-app-bar-nav-icon @click="drawer = !drawer"></v-app-bar-nav-icon>
      <v-toolbar-title>Todo App</v-toolbar-title>

      <v-spacer></v-spacer>

      <v-menu
        offset-y 
        transition="slide-y-transition"
      >
        <template v-slot:activator="{ on, attrs }">
          <v-btn
            text
            v-bind="attrs"
            v-on="on"
          >
            <v-icon class="pr-2">
              mdi-account
            </v-icon>
            <span v-if="loggedUser.name == null"> No user </span>
            <span v-else> {{ loggedUser.name }} </span>
          </v-btn>
        </template>
        <v-list>
          <v-list-item
            v-for="user in users"
            :key="user.id"
            link
            @click="changeUser(user)"
          >
            <v-list-item-title> {{ user.name }} </v-list-item-title>
          </v-list-item>
        </v-list>
      </v-menu>
    </v-app-bar>
    <v-main>
      <router-view></router-view>
    </v-main>
  </v-app>
</template>

<script>
  import axios from 'axios'

  export default {

    data () {
      return {
        items: [
          { 
            title: 'Tasks', 
            icon: 'mdi-check-bold',
            to: '/'
          },
          { 
            title: 'About', 
            icon: 'mdi-help-box',
            to: '/about'
          },
        ],
        right: null,
        drawer: null
      }
    },
    computed: {
      loggedUser: {
        get() { // Return current logged User
          return this.$store.state.user
        }
      },
      users: {
        get() { // Return all stored users
          return this.$store.state.users
        }
      }
    },
    methods: {
      // Method that toggles the current theme and stores it in local storage
      toggleDark() {
        this.$vuetify.theme.dark == false ? this.$vuetify.theme.dark = true : this.$vuetify.theme.dark = false
        localStorage.setItem("dark_theme", this.$vuetify.theme.dark.toString());
      },
      // Method that makes an API call and returns all users, to then store them in memory
      getUsers() {
        axios.get('http://127.0.0.1:8000/api/users')
        .then((response) => {
          this.$store.state.users = response.data // vuex store all users
        })
        .catch(function (error) {
          console.log(error.response.data)
        })
      },
        // Method that makes an API call and changes currently logged user, to then store it in memory
      changeUser(user) {
        axios.get('http://127.0.0.1:8000/api/user/' + user.id)
        .then((response) => {
          this.$store.state.user = response.data // vuex store logged user
        })
        .catch(function (error) {
          console.log(error.response.data)
        })
      }
    },
    mounted() {
      // This code checks in local storage to see if dark mode has been already enabled.
      const darkMode = localStorage.getItem("dark_theme");
      if (darkMode) {
          if (darkMode === "true") {
              this.$vuetify.theme.dark = true;
          } else {
              this.$vuetify.theme.dark = false;
          }
      } else if (window.matchMedia && window.matchMedia("(prefers-color-scheme: dark)").matches) {
          this.$vuetify.theme.dark = true;
          localStorage.setItem("dark_theme", this.$vuetify.theme.dark.toString());
      }
    },
    created() {
      this.getUsers()
    }
  }
</script>