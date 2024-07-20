<!-- src/components/CharacterGenerator.vue -->
<template>
  <div id="componentPage">
    <button @click="generateCharacter" id="generatorBtn" class="appBtn">
      Get me a new one!
    </button>
    <div id="characterSheet">
      <div v-if="generated">
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
        <div id="boxedComps">
          <section id="flavorSection">
            <h1 id="charName" class="sectionTitle">You are {{ name }}</h1>
            <div id="charDetails">
              <p id="quirk">☣ {{ quirk }}</p>
              <p>☣ Has {{ trinket }}</p>
              <p>☣ Wears {{ outfit }}</p>
            </div>
          </section>
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
          <section id="trickSection">
            <h1 class="sectionTitle">Tricks:</h1>
            <ul class="trickList">
              <li
                class="trickElem"
                v-for="(value, trick) in tricks"
                :key="trick"
              >
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
        </div>
      </div>
      <button class="appBtn" id="printBtn" onclick="window.print()">
        Print this one
      </button>
    </div>
  </div>
</template>

<script>
import { attributes } from "../tables/attributes";
import { tricks } from "../tables/tricks";
import { mutations } from "../tables/mutations";
import { modifications } from "../tables/modifications";
import { quirks } from "../tables/quirks";
import { trinkets } from "../tables/trinkets";
import { outfits } from "../tables/outfits";
import { weapons } from "../tables/weapons";
import { items } from "../tables/items";
import { names } from "../tables/names";

export default {
  data() {
    return {
      name: {},
      quirk: {},
      trinket: {},
      outfit: {},
      weapon: {},
      item: {},
      attributes: {},
      tricks: {},
      generated: false,
    };
  },
  mounted() {
    this.generateCharacter();
  },
  methods: {
    generateCharacter() {
      // Reset the attributes and tricks
      this.attributes = {};
      let trickList = [];
      this.tricks = {};

      // Initialize attributes
      for (const attr in attributes) {
        this.attributes[attr] = 0;
      }

      // Randomly pick 3 attributes to increase
      const attributeKeys = Object.keys(attributes);
      const selectedAttributes = this.getRandomElementList(attributeKeys, 3);
      selectedAttributes.forEach((attr) => {
        this.attributes[attr] += 1;
      });

      trickList = this.getTrickfromAttribute(selectedAttributes);
      this.tricks = this.getTrickText(trickList, tricks);

      // Calculate Hulkification
      if (Object.keys(this.tricks).includes("Hulkification (Mutation)")) {
        // increase strength and toughness by 3, decrease a random stat by 3
        this.attributes["Strength"] = 3;
        this.attributes["Toughness"] = 3;
        const randomAttr = this.getRandomElement([
          "Observation",
          "Intelligence",
          "Demeanor",
          "Agility",
        ]);
        this.attributes[randomAttr] = -3;
        // Reset every other to 0 if not in special list
        for (const attr in attributes) {
          if (
            !["Strength", "Toughness", randomAttr, "Weirdness"].includes(attr)
          ) {
            this.attributes[attr] = 0;
          }
        }
      }

      // Calculate slots
      this.inventorySize =
        this.attributes["Strength"] + 5 + this.calculateSlots(this.tricks);

      // Calculate HP
      // If grit is picked then base is tough + 10
      this.HP = this.attributes["Toughness"] + 5 + this.calculateHP(trickList);

      // Set flavour info and items
      this.name = this.getRandomElement(names);
      this.quirk = this.getRandomElement(quirks);
      this.trinket = this.getRandomElement(trinkets);
      this.outfit = this.getRandomElement(outfits);
      this.weapon = this.getRandomElement(weapons);
      this.item = this.getRandomElement(items);

      // Set AP
      this.AP = this.calculateAP(this.tricks, this.outfit);

      this.generated = true;
    },
    getRandomElement(array) {
      const randomIndex = Math.floor(Math.random() * array.length);
      return array[randomIndex];
    },
    getRandomElementList(array, count) {
      const result = [];
      for (let i = 0; i < count; i++) {
        result.push(this.getRandomElement(array));
      }
      return result;
    },
    getTrickText(trickList, fullList) {
      const tricksObj = {};
      trickList.forEach((trick) => {
        let cleanName = trick.replace(/\*/g, "");
        if (cleanName == "Mutation") {
          let mutation = this.getRandomElement(Object.keys(mutations));
          let trickName = `${mutation} (Mutation)`;
          tricksObj[trickName] = [mutations[mutation]];
          return;
        }
        if (cleanName == "Modification") {
          let mod = this.getRandomElement(Object.keys(modifications));
          let trickName = `${mod} (Modification)`;
          tricksObj[trickName] = [modifications[mod]];
          return;
        }
        if (!tricksObj[cleanName]) {
          tricksObj[cleanName] = [fullList[cleanName]];
        } else {
          tricksObj[cleanName].push(fullList[cleanName]);
        }
      });

      return tricksObj;
    },
    getTrickfromAttribute(attributeList) {
      const trickList = [];
      const selectedTricks = new Set();

      attributeList.forEach((attr) => {
        const sublist = attributes[attr];

        // Filter out already selected non-asterisk tricks
        const filteredSublist = sublist.filter((trick) => {
          return !selectedTricks.has(trick) || trick.includes("*");
        });

        // Get a random element from the filtered sublist
        let trick = this.getRandomElementList(filteredSublist, 1)[0];

        trickList.push(trick);
        selectedTricks.add(trick);
      });

      return trickList;
    },
    calculateHP(trickList) {
      let hp = 0;
      const hp_modifiers = {
        "Grit*": 5,
      };
      trickList.forEach((trick) => {
        if (hp_modifiers.hasOwnProperty(trick)) {
          hp += hp_modifiers[trick];
        }
      });
      return hp;
    },
    calculateAP(tricks, outfit) {
      let ap = 0;
      const ap_modifiers = {
        "Natural Armor - Subtle Hide/Scales (Mutation)": 4,
        "Natural Armor - Thick Shell (Mutation)": 8,
        "Armored Plating* (Modification)": 4,
        "Thick Skin": 3,
      };
      Object.keys(tricks).forEach((trick) => {
        if (ap_modifiers.hasOwnProperty(trick)) {
          // Thick Skin stacks with other armor
          if (trick.includes("Thick Skin")) {
            ap = outfit.includes("Biker")
              ? ap_modifiers[trick] + 4
              : ap_modifiers[trick];
            return ap;
          }
          ap += ap_modifiers[trick];
        }
      });
      // if using Biker Leather and ap hasn't been modified
      if (outfit.includes("Biker") && ap == 0) {
        return 4;
      }
      return ap;
    },
    calculateSlots(tricks) {
      let slots = 0;
      const slot_modifiers = {
        Overpacking: 3,
        "Compartment (Modification)": 3,
      };
      Object.keys(tricks).forEach((trick) => {
        if (slot_modifiers.hasOwnProperty(trick)) {
          slots += slot_modifiers[trick];
        }
      });
      return slots;
    },
  },
};
</script>

<style scoped>
#componentPage {
  display: grid;
  grid-auto-flow: column;
  grid-gap: 10px;
  grid-template-rows: 0.1fr 2fr;
  width: 45vw;
  flex: 1;
  justify-items: center;
  font-size: 16px;
  flex-grow: 1;
}

#printBtn {
  margin-top: 1.5em;
}

.sectionTitle {
  text-transform: uppercase;
  color: #be4537;
  font-weight: 500;
  font-family: Verdana, Geneva, Tahoma, sans-serif;
}

.resourceVal {
  color: #b87168;
}

#characterSheet {
  margin-top: 0.5em;
}

ul {
  list-style-type: none;
  padding: 0;
}
#boxedComps {
  display: flex;
  flex-wrap: wrap;
}

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

#trickSection {
  text-align: left;
  margin-top: 0.5em;
  padding: 1em 1em;
  flex: 1 0 100%;
  border-bottom: 1px solid #b75b4f;
}

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

.trickList {
  display: grid;
}

.trickElem {
  display: flex;
  width: 100%;
}

.category {
  display: grid;
  grid-auto-flow: column;
  grid-template-rows: auto auto;
  text-align: left;
  margin: 0.5em 0.2em;
  grid-template-columns: 1fr auto;
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

@media (max-width: 600px) {
  #componentPage {
    width: 100vw;
    font-size: 14px;
  }
  #characterSheet {
    width: 100%;
  }

  #inventorySection {
    flex: 1 0 100%;
  }

  #flavorSection {
    flex: 1 0 100%;
    border-right: 0;
  }

  .trickDescription {
    padding: 0 1em;
  }

  .trickElem {
    width: 100%;
  }
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

/* Media query for tablets */
@media (min-width: 600px) and (max-width: 1024px) {
  #componentPage {
    width: 80vw;
  }
}
</style>
