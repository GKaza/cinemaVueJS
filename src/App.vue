<template>
  <div id="app">
    <Seat msg="Welcome to Vue Cinema" />

    <button id="bookSeats" onclick="bookSeats()">Book your seats</button>
    <div class="displayerBoxes">
      <table class="Displaytable">
        <tr>
          <th>Name</th>
          <th>Number of Seats</th>
          <th>Seats</th>
        </tr>
        <tr>
          <td id="nameDisplay"></td>
          <td id="NumberDisplay"></td>
          <td id="seatsDisplay"></td>
        </tr>
      </table>
    </div>
    <div class="endBtns">
      <button class="btn2" onclick="location.href = 'https://www.netflix.com/'">Go</button>
      <button class="btn2" onclick="window.location.reload();">Make new booking</button>
    </div>
  </div>
</template>

<script>
import Seat from "./components/Seat.vue";
// import swal from "sweetalert";

export default {
  name: "App",
  components: {
    Seat
  }
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  text-align: center;
  color: #ffffff;
  margin: 0px;
  height: 100%;
  background-image: url(./assets/cinemabg.jpg);
  background-position: center;
  background-repeat: no-repeat;
  background-size: cover;

}
</style>
