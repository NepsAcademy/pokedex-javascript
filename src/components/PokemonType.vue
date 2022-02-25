<template>
  <v-chip :color="color" label class="mr-2 white--text"> {{ name }}</v-chip>
</template>

<script>
export default {
  props: {
    type: String,
  },
  computed: {
    name() {
      return this.type.charAt(0).toUpperCase() + this.type.slice(1);
    },
    color() {
      switch (this.type) {
        case "grass":
          return "green";
        case "poison":
          return "purple";
        case "water":
          return "blue";
        case "bug":
          return "light-green";
        case "ground":
          return "lime darken-3";
        case "fairy":
          return "pink lighten-4";
        case "fighting":
          return "red darken-4";

        case "fire":
          return "red";
        case "flying":
          return "teal";
        case "psychic":
          return "pink";
        case "electric":
          return "yellow darken-2";
        case "rock":
          return "brown";
        case "ice":
          return "cyan";
        case "dragon":
          return "purple";
        case "normal":
          return "grey";
        default:
          return "grey";
      }
    },
  },
};
</script>

<style lang="scss" scoped></style>
