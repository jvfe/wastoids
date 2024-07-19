<script setup>
import CharacterGenerator from "./components/CharacterGenerator.vue";
</script>

<!-- src/App.vue -->
<template>
  <div id="app">
    <CharacterGenerator />
  </div>
</template>

<script>
export default {
  name: "App",
  components: {
    CharacterGenerator,
  },
};
</script>

<style>
#app {
  display: flex;
  place-items: center;
  width: max-content;
  font-family: "Work Sans", Avenir, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  margin-top: 60px;
}

@media (max-width: 600px) {
  #app {
    width: 100%;
    padding: 0 0.5em;
  }
}
</style>
