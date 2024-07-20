<!-- src/components/CharacterGenerator.vue -->
<template>
  <div id="componentPage">
    <button @click="generateCharacter" id="generatorBtn" class="appBtn">
      Get me a new one!
    </button>
    <div id="characterSheet">
      <div v-if="generated">
        <StatsSection :attributes="attributes" :HP="HP" :AP="AP" />
        <div id="boxedComps">
          <FlavorSection
            :name="name"
            :quirk="quirk"
            :trinket="trinket"
            :outfit="outfit"
          />
          <InventorySection
            :inventorySize="inventorySize"
            :outfit="outfit"
            :item="item"
            :weapon="weapon"
          />
          <TrickSection :tricks="tricks" />
        </div>
      </div>
      <button class="appBtn" id="printBtn" onclick="window.print()">
        Print this one
      </button>
    </div>
  </div>
</template>

<script>
// Components
import StatsSection from "./SheetSections/Stats.vue";
import FlavorSection from "./SheetSections/Flavor.vue";
import InventorySection from "./SheetSections/Inventory.vue";
import TrickSection from "./SheetSections/Tricks.vue";

// Data
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
  components: {
    StatsSection,
    FlavorSection,
    InventorySection,
    TrickSection,
  },
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

#characterSheet {
  margin-top: 0.5em;
}

#boxedComps {
  display: flex;
  flex-wrap: wrap;
}

@media (max-width: 600px) {
  #componentPage {
    width: 100vw;
    font-size: 14px;
  }

  #characterSheet {
    width: 100%;
  }
}

/* Media query for tablets */
@media (min-width: 600px) and (max-width: 1024px) {
  #componentPage {
    width: 80vw;
  }
}
</style>
