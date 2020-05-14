<template id="seat-check">
  <td>
    <div>{{ key }}</div>
  </td>
</template>

<script>
export default {
  name: 'Seat',
  template: '#seat-check',
  props: [ 'key', 'row', 'number', 'available'],
  methods: {
    select () {
      this.$emit('click', this.key)
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
 