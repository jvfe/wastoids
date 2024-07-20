<template>
  <section id="statsSection">
    <ul class="attrs">
      <template v-for="(value, key) in attributes" :key="key">
        <li class="attrsElem" v-if="value != 0">
          <p class="attrName">{{ key }}</p>
          <p class="attrVal">
            <template v-if="value > 0">+</template>{{ value }}
          </p>
        </li>
      </template>
    </ul>
    <div id="hpAp">
      <p>
        HP <span class="resourceVal">{{ HP }}/{{ HP }}</span>
      </p>
      <p>
        AP <span class="resourceVal">{{ AP }}/{{ AP }}</span>
      </p>
    </div>
  </section>
</template>

<script>
export default {
  name: "StatsSection",
  props: {
    attributes: {
      type: Object,
      required: true,
    },
    HP: {
      type: Number,
      required: true,
    },
    AP: {
      type: Number,
      required: true,
    },
  },
};
</script>

<style scoped>
#statsSection {
  display: flex;
  justify-content: space-between;
  flex-wrap: wrap;
  border-top: 1px solid #b75b4f;
  border-bottom: 1px solid #b75b4f;
}

#hpAp {
  display: flex;
  justify-content: space-around;
  align-items: center;
  padding-left: 0.25em;
  border-left: 1px solid #b75b4f;
  flex: 1 0 15%;
}

#hpAp > p {
  font-weight: bold;
  margin: 0 1em;
}

.attrs {
  display: flex;
  justify-content: space-around;
  align-items: stretch;
  flex: 1 0 60%;
  flex-wrap: wrap;
}

.attrVal {
  display: flex;
  margin: 0 0.2em;
  color: #b87168;
}

.attrsElem {
  display: flex;
  justify-content: space-between;
  margin: 0.25em;
}

.attrName {
  font-weight: bold;
}

.resourceVal {
  color: #b87168;
}

@media (max-width: 600px) {
  .attrs {
    justify-content: center;
    width: 100%;
    flex: 1 0 100%;
  }

  #hpAp {
    border: 0;
    padding: 0;
    width: 100%;
    padding: 0.2em 0;
    border-top: 1px solid #b75b4f;
    justify-content: center;
  }
}
</style>
