<script setup>
import TitleSection from "./components/TitleSection.vue";
import CharacterGenerator from "./components/CharacterGenerator.vue";
import FooterSection from "./components/FooterSection.vue";
</script>

<!-- src/App.vue -->
<template>
  <div id="app">
    <TitleSection />
    <CharacterGenerator />
    <FooterSection />
  </div>
</template>

<script>
export default {
  name: "App",
  components: {
    TitleSection,
    CharacterGenerator,
    FooterSection,
  },
};
</script>

<style>
#app {
  display: flex;
  flex-direction: column;
  gap: 1.5em;
  place-items: center;
  width: max-content;
  font-family: "Work Sans", Avenir, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  margin-top: 40px;
  min-height: calc(100vmin - 10px);
}

.appBtn {
  height: 3em;
  width: 10rem;
  appearance: none;
  text-decoration: none;
  color: #be4537;
  border: 1px solid #313d5a;
  border-radius: 0;
  background-color: transparent;
  cursor: pointer;
  font-weight: bold;
  text-transform: uppercase;
  transition: 0.4s;
}

.appBtn:hover {
  background-color: #313d5a;
  color: var(--vt-c-white);
}

.sectionTitle {
  text-transform: uppercase;
  color: #be4537;
  font-weight: 500;
  font-family: Verdana, Geneva, Tahoma, sans-serif;
}

ul {
  list-style-type: none;
  padding: 0;
}

@media (max-width: 600px) {
  #app {
    width: 100%;
    padding: 0 0.5em;
    margin-top: 1em;
  }
}

@media print {
  #appLogo,
  .appBtn,
  #footer {
    display: none;
  }
}
</style>
