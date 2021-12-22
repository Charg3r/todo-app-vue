<template>
  <v-container>
    <v-row class="text-center pa-2">
      <v-col cols="4">
        <v-img
          :src="require('../assets/vue.png')"
          class="mt-16"
          contain
          height="200"
        />
        
      </v-col>
      <v-col cols="4">
        <v-img
          :src="require('../assets/vuetify.svg')"
          class="mt-16"
          contain
          height="200"
        />
      </v-col>
      <v-col cols="4">
        <v-img
          :src="require('../assets/laravel.png')"
          class="mt-16"
          contain
          height="200"
        />
      </v-col>
      <v-col col="12" class="mt-12">
        <h1> App developed with Vue + Vuetify + Laravel </h1>
        <p> <a href="https://github.com/Charg3r" target="_blank"> @Charg3r </a></p>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
  export default {
    name: 'About',
  }
</script>

<style>
  a, a:link, a:visited, a:active{
    text-decoration: none;
    color: black;
    font-size: 1.5em;
    font-weight: 500;
  }
</style>