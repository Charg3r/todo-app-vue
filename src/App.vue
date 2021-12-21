<template>
  <v-app id="inspire">
    <v-navigation-drawer
      v-model="drawer"
      app 
    >
      <v-list-item>
        <v-list-item-content>
          <v-list-item-title class="text-h6">
            John Doe
          </v-list-item-title>
          <v-list-item-subtitle>
            johndoe@mail.com
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
      color="blue"
      app
      dark 
    >
      <v-app-bar-nav-icon @click="drawer = !drawer"></v-app-bar-nav-icon>
      <v-toolbar-title>Todo App</v-toolbar-title>

      <v-spacer></v-spacer>

      <v-btn text>
        User
        <v-icon
          right
          dark
        >
          mdi-menu-down
        </v-icon>
      </v-btn>
    </v-app-bar>

    <v-main>
      <router-view></router-view>
    </v-main>
  </v-app>
</template>

<script>

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
    methods: {
      toggleDark() {
        this.$vuetify.theme.dark == false ? this.$vuetify.theme.dark = true : this.$vuetify.theme.dark = false
        localStorage.setItem("dark_theme", this.$vuetify.theme.dark.toString());
      }
    },
    mounted() {
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
    }
  }
</script>