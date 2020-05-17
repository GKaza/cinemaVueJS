<template>
  <div id="app">
    <h1>Welcome to Vue Cinema</h1>

    <form :key="inputForm" @submit="formInfo">
      <label for="fname"></label>
      <input type="text" id="fname" v-model="booking.name" />
      <label for="wantedSeats"></label>
      <input type="text" id="wantedSeats" v-model="booking.numberOfSeats" />
      <input type="submit" />
    </form>

    <div :key="seatMap">
      <Seat
        v-for="seat in seats"
        :key="seat.id"
        :row="seat.row"
        :number="seat.number"
        :available="seat.available"
        @seat-check="checkSeat($event)"
      ></Seat>
    </div>

    <button id="bookSeats" onclick="bookSeats()">Book your seats</button>
    <div class="displayerBoxes">
      <table class="Displaytable">
        <tr>
          <th>Name</th>
          <th>Number of Seats</th>
          <th>Seats</th>
        </tr>
        <tr>
          <td>{{ booking.name }}</td>
          <td>{{ booking.numberOfSeats }}</td>
          <td>{{ booking.bookedSeats }}</td>
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
// localStorage.removeItem("reserved")

//

export default {
  name: "App",
  components: {
    Seat
  },
  data() {
    booking: {
      name: "";
      numberOfSeats: 0;
      bookedSeats: [];
    }
    rows: ["A", "B", "C", "D", "E", "F", "G", "H", "I", "J"];
    seats: new Map();
    max: new Map();
    maxRow: 0;
  },
  methods: {
    formInfo() {},
    bookSeats() {},
    createSeats() {
      let reserved = JSON.parse(localStorage.getItem("reserved"));

      for (let i = 0; i < this.rows.length; i += 1) {
        this.max.set(this.rows[i], 20);
      }

      for (let r = 0; r < 10; r += 1) {
        for (let x = 1; x < 21; x += 1) {
          let obj = {
            id: this.rows[r] + x,
            row: this.rows[r],
            number: x,
            available: true
          };

          this.seats.set(obj["id"], obj);
        }
      }

      if (reserved) {
        for (let i = 0; i < reserved.length; i += 1) {
          let sget = this.seats.get(reserved[i]);
          sget.available = false;
          this.max.set(sget.row, this.max.get(sget.row) - 1);
        }
      } else {
        reserved = [];
      }

      for (let i = 0; i < this.rows.length; i++) {
        if (this.max.get(this.rows[i]) > this.maxRow) {
          this.maxRow = this.max.get(this.rows[i]);
        }
      }
    },
    checkSeat(id) {
      let row = this.seats.get(id).row;
      let number = this.seats.get(id).number;
      let firstSeat = number - (this.wantedSeats - 1);
      let allSeatsAvailable = false;

      if (this.seats.get(id).available) {
        if (firstSeat < 1) {
          firstSeat = 1;
        }

        while (allSeatsAvailable == false && firstSeat <= number) {
          if (firstSeat + this.wantedSeats > 21) {
            break;
          }
          let setAvailable = true;
          for (let i = 0; i < this.wantedSeats; i += 1) {
            let seatId = row + (firstSeat + i);
            if (!this.seats.get(seatId).available) {
              setAvailable = false;
              break;
            } else {
              this.set[i] = seatId;
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
