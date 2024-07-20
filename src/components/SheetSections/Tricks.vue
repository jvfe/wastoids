<template>
  <section id="trickSection">
    <h1 class="sectionTitle">Tricks:</h1>
    <ul class="trickList">
      <li class="trickElem" v-for="(value, trick) in tricks" :key="trick">
        <div class="category">
          <h4 class="trickName">
            ☣ {{ trick }}
            <span v-if="value.length > 1" class="quantity"
              >(x{{ value.length }})</span
            >
          </h4>
          <p class="trickDescription">{{ value[0] }}</p>
        </div>
      </li>
    </ul>
  </section>
</template>

<script>
export default {
  name: "TrickSection",
  props: {
    tricks: {
      type: Object,
      required: true,
    },
  },
};
</script>

<style scoped>
#trickSection {
  text-align: left;
  margin-top: 0.5em;
  padding: 1em 1em;
  flex: 1 0 100%;
  border-bottom: 1px solid #b75b4f;
}

.trickList {
  display: grid;
}

.trickElem {
  display: flex;
  width: 100%;
}

.trickName {
  font-weight: bold;
}

.trickDescription {
  text-align: left;
  padding: 0 1.5em;
}

.quantity {
  font-style: italic;
}

.category {
  display: grid;
  grid-auto-flow: column;
  grid-template-rows: auto auto;
  text-align: left;
  margin: 0.5em 0.2em;
  grid-template-columns: 1fr auto;
}

@media (max-width: 600px) {
  .trickDescription {
    padding: 0 1em;
  }

  .trickElem {
    width: 100%;
  }
}
</style>
