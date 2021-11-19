<template>
  <Sentences/>
  <Cards/>
</template>

<script>
import Sentences from './components/Sentences.vue'
import Cards from './components/Cards.vue'

export default {
  name: 'App',
  components: {
    Sentences,
    Cards
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: left;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
