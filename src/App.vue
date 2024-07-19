<script setup>
import CharacterGenerator from "./components/CharacterGenerator.vue";
</script>

<!-- src/App.vue -->
<template>
  <div id="app">
    <div id="titleContainer">
      <img
        src="./assets/GasMask_by_MugenB16.png"
        alt="Gas Mask Icon"
        id="appLogo"
      />
      <h1 id="appTitle">WASTOIDS!</h1>
    </div>
    <CharacterGenerator />
    <div id="footer">
      <p>
        <a
          href="https://github.com/jvfe/wastoids"
          target="_blank"
          rel="noopener noreferrer"
          >Open source</a
        >
        app made by jvfe
      </p>
      <p>
        <a
          href="https://jasontocci.itch.io/wastoid"
          target="_blank"
          rel="noopener noreferrer"
          >Wastoid</a
        >
        by
        <a
          href="https://jasontocci.itch.io/"
          target="_blank"
          rel="noopener noreferrer"
          >Jason Tocci</a
        >
        @
        <a
          href="https://pretendo.games/"
          target="_blank"
          rel="noopener noreferrer"
          >Pretendo Games</a
        >
      </p>
      <p>
        Gas Mask by
        <a
          href="https://www.iconarchive.com/show/the-attic-icons-by-mugenB16/Gas-Mask-icon.html"
          target="_blank"
          rel="noopener noreferrer"
          >MugenB16
        </a>
      </p>
    </div>
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
  flex-direction: column;
  gap: 1.5em;
  place-items: center;
  width: max-content;
  font-family: "Work Sans", Avenir, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  margin-top: 60px;
}

#titleContainer {
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 1em;
  flex-wrap: wrap;
}

#appTitle {
  font-size: xx-large;
  font-weight: bolder;
  color: #be4537;
}

#footer {
  padding: 1em;
}

@media (max-width: 600px) {
  #app {
    width: 100%;
    padding: 0 0.5em;
  }

  #appLogo {
    width: 20%;
  }
}
</style>
