<template>
  <section id="flavorSection">
    <h1 id="charName" class="sectionTitle">You are {{ name }}</h1>
    <div id="charDetails">
      <p id="quirk">☣ {{ quirk }}</p>
      <p>☣ Has {{ trinket }}</p>
      <p>☣ Wears {{ outfit }}</p>
    </div>
  </section>
</template>

<script>
export default {
  name: "FlavorSection",
  props: {
    name: {
      type: String,
      required: true,
    },
    quirk: {
      type: String,
      required: true,
    },
    trinket: {
      type: String,
      required: true,
    },
    outfit: {
      type: String,
      required: true,
    },
  },
};
</script>

<style scoped>
#flavorSection {
  display: grid;
  align-items: center;
  text-align: left;
  padding: 1em 1em;
  border-bottom: 1px solid #b75b4f;
  border-right: 1px solid #b75b4f;
  flex: 1 0 50%;
}

#charDetails {
  padding: 0 0.5em;
}

#quirk {
  font-style: italic;
}

@media (max-width: 600px) {
  #flavorSection {
    flex: 1 0 100%;
    border-right: 0;
  }
}
</style>
