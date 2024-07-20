<template>
  <section id="inventorySection">
    <h1 class="sectionTitle">
      Gear
      <small id="slots">({{ inventorySize }} slots)</small>
    </h1>
    <ul id="inventoryItems">
      <li class="item">☣ {{ outfit }} (Outfit)</li>
      <li class="item">☣ {{ item }}</li>
      <li class="item">☣ {{ weapon }}</li>
    </ul>
  </section>
</template>

<script>
export default {
  name: "InventorySection",
  props: {
    inventorySize: {
      type: Number,
      required: true,
    },
    outfit: {
      type: String,
      required: true,
    },
    item: {
      type: String,
      required: true,
    },
    weapon: {
      type: String,
      required: true,
    },
  },
};
</script>

<style scoped>
#inventorySection {
  text-align: left;
  border-bottom: 1px solid #b75b4f;
  padding: 1em 1em;
  flex: 1 0 50%;
}

#inventoryItems {
  width: fit-content;
  padding: 0 0.5em;
}

#slots {
  font-size: small;
}

@media (max-width: 600px) {
  #inventorySection {
    flex: 1 0 100%;
  }
}
</style>
