<template id="seat-check">
  <div @click="select">{{ key }}</div>
</template>

<script>
export default {
  name: "Seat",
  template: "#seat-check",
  props: ["key", "row", "number", "available"],
  methods: {
    select() {
      this.$emit("seat-check", this.key);
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>
 