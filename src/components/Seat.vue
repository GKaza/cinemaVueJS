<template id="seat-check">
  <label class="checkbox" @click="select">
    <input type="checkbox" />
  </label>
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
.checkbox {
  display: inline-flex;
  cursor: pointer;
  position: relative;
}

.checkbox > input {
  height: 25px;
  width: 25px;
  -webkit-appearance: none;
  -moz-appearance: none;
  -o-appearance: none;
  appearance: none;
  border: 1px solid #000000;
  border-radius: 4px;
  outline: none;
  transition-duration: 0.3s;
  background-color: #ffffff;
  cursor: pointer;
}

.seatTaken > input {
  background-color: #ff0000;
  cursor: not-allowed;
}

.seatSelected > input {
  background-color: rgb(113, 226, 0);
  border: 2px solid #555555;
}
</style>
