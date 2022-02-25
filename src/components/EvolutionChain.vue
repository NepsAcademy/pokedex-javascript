<template>
  <div>
    <v-row class="mx-0 d-flex align-center">
      <template v-for="(evolution_detail, index) in evolutions()">
        <v-col
          cols="12"
          md="3"
          :key="`evolution-${index}`"
          v-if="typeof evolution_detail == 'object'"
        >
          <PokemonCard :pokemon="evolution_detail" flat />
        </v-col>
        <v-col md="1" :key="`evolution-${index}`" v-else>
          <h3 class="text-center">L {{ evolution_detail }}</h3>
        </v-col>
      </template>
    </v-row>
  </div>
</template>

<script>
import axios from "axios";
import PokemonCard from "./PokemonCard.vue";

export default {
  components: {
    PokemonCard,
  },
  data() {
    return {
      evolution: null,
    };
  },
  props: {
    pokemon: Object,
  },
  mounted() {
    this.fetch_evolution();
  },
  methods: {
    fetch_evolution() {
      axios
        .get(`https://pokeapi.co/api/v2/pokemon-species/${this.pokemon.id}`)
        .then((response) => {
          axios.get(response.data.evolution_chain.url).then((response) => {
            this.evolution = response.data.chain;
          });
        });
    },
    evolutions() {
      let chain = [];
      let evolution = this.evolution;
      if (evolution.species) {
        chain.push(evolution.species);
      }

      while (evolution.species) {
        if (evolution.evolves_to.length > 0) {
          evolution = evolution.evolves_to[0];

          if (
            evolution.evolution_details.length > 0 &&
            evolution.evolution_details[0].min_level
          ) {
            chain.push(evolution.evolution_details[0].min_level);
          }

          if (evolution.species) {
            chain.push(evolution.species);
          }
        } else {
          break;
        }
      }

      return chain;
    },
  },
  watch: {
    pokemon() {
      this.fetch_evolution();
    },
  },
};
</script>

<style lang="scss" scoped></style>
