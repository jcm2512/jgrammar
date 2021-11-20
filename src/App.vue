<template>
  <v-app>
    <v-main>
      <Cards />
    </v-main>
  </v-app>
</template>

<script>
// import Sentences from './components/Sentences.vue'
import Cards from './components/Cards.vue'
export default {
  name: 'App',
  components: {
    // Sentences,
    Cards,
  },
  data: () => ({
    //
  }),
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: left;
  color: #2c3e50;
  padding: 60px;
  background-color: #121212;
}
</style>
