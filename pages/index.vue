<template>
  <b-container fluid>
    <b-row>
      <b-col class="col-3">
        <b-list-group>
          <b-list-group-item
            v-for="(pokeColor, index) in pokeColors"
            :key="index"
          >
            <b-link @click="colorListing(pokeColor.url)">
              {{ pokeColor.name }}
            </b-link>
          </b-list-group-item>
        </b-list-group>
      </b-col>
      <b-col class="col-9">
        <b-row>
          <b-list-group>
            <b-list-group-item
              v-for="(speci, index) in pokeSpecies"
              :key="index"
            >
              <b-row>
                <b-col>
                  <b-jumbotron class="text-center">
                    <h3>
                      {{ speci.speci.names[0].name }}
                    </h3>
                    <p>
                      {{
                        (() => {
                          //console.log(speci.speci.flavor_text_entries);
                          const filed = speci.speci.flavor_text_entries.filter(
                            (elm) => {
                              return elm.language.name == "ja";
                            }
                          );
                          return filed.length > 0
                            ? filed[0].flavor_text
                            : "No Data";
                        })()
                      }}
                    </p>
                  </b-jumbotron>
                </b-col>
                <b-col>
                  <b-list-group>
                    <b-list-group-item
                      v-for="(name, index) in speci.speci.names"
                      :key="index"
                    >
                      {{ name.name }} ({{ name.language.name }})
                    </b-list-group-item>
                  </b-list-group>
                </b-col>
              </b-row>
            </b-list-group-item>
          </b-list-group>
        </b-row>
      </b-col>
    </b-row>
  </b-container>
</template>

<script lang="ts">
import Vue from "vue";
import axios from "axios";
const pokeColorsURL = "https://pokeapi.co/api/v2/pokemon-color";

class PokeColor {
  name: string = "";
  url: string = "";
}

class PokeSpecies {
  name: string = "";
  url: string = "";
  speci: object = {};
}

export default Vue.extend({
  data() {
    const pokeSpecies: PokeSpecies[] = [];
    return {
      pokeSpecies: pokeSpecies,
    };
  },
  methods: {
    colorListing(url: string) {
      this.pokeSpecies.splice(0, this.pokeSpecies.length - 1);
      if (url == null) return;
      axios.get(url).then((res) => {
        const species = res.data.pokemon_species;
        if (species == null) return;
        species.forEach((elm: PokeSpecies) => {
          axios.get(elm.url).then((reso) => {
            const speci = reso.data;
            elm.speci = speci;
            this.pokeSpecies.push(elm);
          });
        });
        console.log("species", this.pokeSpecies);
      });
    },
  },
  asyncData() {
    const pokeColors: PokeColor[] = [];

    axios.get(pokeColorsURL).then((res) => {
      const results = res.data.results;
      if (results == null) return;
      results.forEach((elm: PokeColor) => {
        pokeColors.push(elm);
      });
    });
    console.log("pokeColors", pokeColors);
    return {
      pokeColors: pokeColors,
    };
  },
});
</script>

<style>
.container {
  margin: 0 auto;
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  text-align: center;
}

.title {
  font-family: "Quicksand", "Source Sans Pro", -apple-system, BlinkMacSystemFont,
    "Segoe UI", Roboto, "Helvetica Neue", Arial, sans-serif;
  display: block;
  font-weight: 300;
  font-size: 100px;
  color: #35495e;
  letter-spacing: 1px;
}

.subtitle {
  font-weight: 300;
  font-size: 42px;
  color: #526488;
  word-spacing: 5px;
  padding-bottom: 15px;
}

.links {
  padding-top: 15px;
}
</style>
