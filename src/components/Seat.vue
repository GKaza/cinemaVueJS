<template id="seat-check">
  <td>
    <label class="checkbox" @click="select">
      <input type="checkbox" />
    </label>
  </td>
</template>

<script>
export default {
  name: "Seat",
  template: "#seat-check",
  props: ["id", "row", "number", "available"],
  methods: {
    select() {
      this.$emit("seat-check", this.id);
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>
