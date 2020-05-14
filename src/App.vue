<template>
  <div id="app">
    <h1>Welcome to Vue Cinema</h1>

    <table>
      <tr>
        <Seat
          v-for="seat in seats"
          :key="seat.id"
          :row="seat.row"
          :number="seat.number"
          :available="seat.available"
          @click="checkSeat"
        ></Seat>
      </tr>
    </table>

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

const rows = ["A", "B", "C", "D", "E", "F", "G", "H", "I", "J"];
let seats = new Map();
// let set = [];
let reserved = JSON.parse(localStorage.getItem("reserved"));
let max = new Map();
let maxRow = 0;

// localStorage.removeItem("reserved")

for (let i = 0; i < rows.length; i += 1) {
  max.set(rows[i], 20);
}

for (let r = 0; r < 10; r += 1) {
  for (let x = 1; x < 21; x += 1) {
    let obj = {
      id: rows[r] + x,
      row: rows[r],
      number: x,
      available: true
    };

    seats.set(obj["id"], obj);
  }
}

if (reserved) {
  for (let i = 0; i < reserved.length; i += 1) {
    let sget = seats.get(reserved[i]);
    sget.available = false;
    max.set(sget.row, max.get(sget.row) - 1);
  }
} else {
  reserved = [];
}

for (let i = 0; i < rows.length; i++) {
  if (max.get(rows[i]) > maxRow) {
    maxRow = max.get(rows[i]);
  }
}

//

export default {
  name: "App",
  components: {
    Seat
  },
  data: {
    rows: ["A", "B", "C", "D", "E", "F", "G", "H", "I", "J"],
    seats: [
      {
        id: "A1",
        row: "A",
        number: 1,
        available: true
      }
    ]
  },
  methods: {
    checkSeat(id) {
      let row = seats.get(id).row;
      let number = seats.get(id).number;
      let firstSeat = number - (wantedSeats - 1);
      let allSeatsAvailable = false;

      if (seats.get(id).available) {
        if (firstSeat < 1) {
          firstSeat = 1;
        }

        while (allSeatsAvailable == false && firstSeat <= number) {
          if (firstSeat + wantedSeats > 21) {
            break;
          }
          let setAvailable = true;
          for (let i = 0; i < wantedSeats; i += 1) {
            let seatId = row + (firstSeat + i);
            if (!seats.get(seatId).available) {
              setAvailable = false;
              break;
            } else {
              set[i] = seatId;
            }
          }
          allSeatsAvailable = setAvailable;
          firstSeat += 1;
        }
      }
    }
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
