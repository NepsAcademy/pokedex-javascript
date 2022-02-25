<template>
  <v-dialog v-model="show" width="800">
    <v-card v-if="selected_pokemon" class="px-4">
      <v-container>
        <v-row class="d-flex align-center">
          <v-col cols="12" md="4">
            <img
              :src="`https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/${selected_pokemon.id}.png`"
              :alt="selected_pokemon.name"
              width="80%"
            />
          </v-col>
          <v-col cols="12" md="8">
            <h1>{{ get_name(selected_pokemon) }}</h1>
            <PokemonType
              v-for="type in selected_pokemon.types"
              :key="type.slot"
              :type="type.type.name"
            />

            <v-divider class="my-4"> </v-divider>
            <v-chip label
              >Height {{ selected_pokemon.height * 2.54 }} cm</v-chip
            >
            <v-chip label class="ml-2"
              >Weight
              {{ (selected_pokemon.weight * 0.45359237).toFixed(0) }}
              kgs</v-chip
            >
          </v-col>
        </v-row>

        <h2 class="mt-4">Stats</h2>

        <Stats class="mt-2" :pokemon="selected_pokemon" />

        <h2 class="mt-6 mb-4">Moves</h2>

        <v-expansion-panels class="mb-4">
          <v-expansion-panel
            v-for="move_type in ['level-up', 'egg', 'machine', 'tutor']"
            :key="move_type"
          >
            <v-expansion-panel-header>
              {{ move_type }}
            </v-expansion-panel-header>
            <v-expansion-panel-content>
              <v-simple-table>
                <template>
                  <thead>
                    <tr>
                      <th class="text-left">Level</th>
                      <th class="text-left">Method</th>
                      <th class="text-left">Name</th>
                    </tr>
                  </thead>
                  <tbody>
                    <tr v-for="move in moves[move_type]" :key="move.name">
                      <td>
                        <span v-show="move.level != 0">{{ move.level }}</span>
                      </td>
                      <td><MoveMethodImage :method="move.method" /></td>
                      <td>{{ move.name }}</td>
                    </tr>
                  </tbody>
                </template>
              </v-simple-table>
            </v-expansion-panel-content>
          </v-expansion-panel>
        </v-expansion-panels>

        <!-- <v-simple-table>
          <template>
            <thead>
              <tr>
                <th class="text-left">Level</th>
                <th class="text-left">Method</th>
                <th class="text-left">Name</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="move in moves" :key="move.name">
                <td>
                  <span v-show="move.level != 0">{{ move.level }}</span>
                </td>
                <td><MoveMethodImage :method="move.method" /></td>
                <td>{{ move.name }}</td>
              </tr>
            </tbody>
          </template>
        </v-simple-table> -->

        <h2>Evolution</h2>

        <EvolutionChain :pokemon="selected_pokemon" />
      </v-container>
    </v-card>
  </v-dialog>
</template>

<script>
import MoveMethodImage from "./MoveMethodImage.vue";
import EvolutionChain from "./EvolutionChain.vue";
import Stats from "./Stats.vue";
import PokemonType from "./PokemonType.vue";

export default {
  components: {
    MoveMethodImage,
    EvolutionChain,
    Stats,
    PokemonType,
  },
  props: {
    show: Boolean,
    selected_pokemon: Object,
  },
  methods: {
    get_name(pokemon) {
      return pokemon.name.charAt(0).toUpperCase() + pokemon.name.slice(1);
    },
    transform_move_name(name) {
      let response = "";
      for (let part of name.split("-")) {
        response += part.charAt(0).toUpperCase() + part.slice(1) + " ";
      }
      return response;
    },
  },
  computed: {
    moves() {
      let response = { "level-up": [], egg: [], machine: [], tutor: [] };
      for (let move of this.selected_pokemon.moves) {
        for (let details of move.version_group_details) {
          if (details.version_group.name == "sword-shield") {
            response[details.move_learn_method.name].push({
              level: details.level_learned_at,
              method: details.move_learn_method.name,
              name: this.transform_move_name(move.move.name),
            });
            break;
          }
        }
      }

      for (let move_type in response) {
        response[move_type].sort((a, b) => {
          let level_a = a.level;
          let level_b = b.level;

          if (level_a == 0) {
            level_a = 1000;
          }

          if (level_b == 0) {
            level_b = 1000;
          }

          if (level_a != level_b) {
            if (level_a < level_b) {
              return -1;
            } else {
              return 1;
            }
          } else {
            if (a.method < b.method) {
              return -1;
            } else {
              return 1;
            }
          }
        });
      }

      return response;
    },
  },
  watch: {
    show() {
      this.$emit("update:show", this.show);
    },
  },
};
</script>

<style lang="scss" scoped></style>
