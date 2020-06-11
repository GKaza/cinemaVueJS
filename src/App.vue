<template>
  <div id="app">
    <h1>Welcome to Vue Cinema</h1>

    <form @submit.prevent="formInfo">
      <label for="fname">Name:</label>
      <input type="text" id="fname" v-model="booking.name" />
      <label for="wantedSeats">Number of Seats:</label>
      <input type="text" id="wantedSeats" v-model="booking.numberOfSeats" />
      <input type="submit" class="btn" />
    </form>

    <div>
      <Seat
        v-for="seat in seatsArray"
        :key="seat.id"
        :id="seat.id"
        :class="{ seatTaken: !seat.available, seatSelected: selected(seat.id) }"
        :row="seat.row"
        :number="seat.number"
        :available="seat.available"
        @seat-check="checkSeat($event)"
      ></Seat>
    </div>

    <button v-if="allSeatsAvailable" @click="bookSeats">Book your seats></button>

    <div>
      <table id="bookingTable">
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
      <div>
        <button class="btn2" @click="location.href = 'https://www.netflix.com/'">Go</button>
        <button class="btn2" @click="window.location.reload();">Make new booking</button>
      </div>
    </div>
  </div>
</template>

<script>
import Seat from "./components/Seat.vue";
import swal from "sweetalert";
// localStorage.removeItem("reserved")

export default {
  name: "App",
  components: {
    Seat
  },
  data() {
    return {
      booking: {
        name: "",
        numberOfSeats: "",
        bookedSeats: []
      },
      rows: ["A", "B", "C", "D", "E", "F", "G", "H", "I", "J"],
      seats: {},
      max: {},
      allSeatsAvailable: false,
      set: [],
      reserved: JSON.parse(localStorage.getItem("reserved"))
    };
  },
  methods: {
    // formInfo() {},
    // bookSeats() {},
    createSeats() {
      // set max of all rows to 20
      for (let i = 0; i < this.rows.length; i += 1) {
        this.max[this.rows[i]] = 20;
      }

      // create an object for each seat and pass it as property value of seats
      for (let r = 0; r < 10; r += 1) {
        for (let x = 1; x < 21; x += 1) {
          let obj = {
            id: this.rows[r] + x,
            row: this.rows[r],
            number: x,
            available: true
          };
          this.seats[obj.id] = obj;
        }
      }

      // make unavailable all seats that have already been reserved
      if (this.reserved) {
        for (let i = 0; i < this.reserved.length; i++) {
          let sget = this.seats.reserved[i];
          sget.available = false;
          this.max[sget.row] = this.max[sget.row] - 1;
        }
      } else {
        this.reserved = [];
      }
    },
    checkSeat(id) {
      let row = this.seats[id].row;
      let number = this.seats[id].number;
      let firstSeat = number - (this.booking.numberOfSeats - 1);
      this.allSeatsAvailable = false;

      if (this.seats[id].available) {
        if (firstSeat < 1) {
          firstSeat = 1;
        }

        while (this.allSeatsAvailable == false && firstSeat <= number) {
          if (firstSeat + this.booking.numberOfSeats > 21) {
            break;
          }
          let setAvailable = true;
          for (let i = 0; i < this.booking.numberOfSeats; i += 1) {
            let seatId = row + (firstSeat + i);
            if (!this.seats[seatId].available) {
              setAvailable = false;
              break;
            } else {
              this.set[i] = seatId;
            }
          }
          this.allSeatsAvailable = setAvailable;
          firstSeat += 1;
        }
        if (this.allSeatsAvailable) {
          for (let i = 0; i < this.set.length; i += 1) {
            this.seats[this.set[i]].checked = true;
          }
        } else {
          swal(
            "Seat selection unavailable!",
            "Not enough space to fit you and your friends.",
            "warning"
          );
        }
      } else {
        swal(
          "Seat already reserved!",
          "Make sure the seat you are trying to select is available.",
          "error"
        );
      }
    },
    selected(id) {
      if (this.allSeatsAvailable) {
        return this.set.find(seat => seat === id);
      }
    }
  },
  computed: {
    seatsArray() {
      return Object.values(this.seats);
    }
  },
  created: function() {
    this.createSeats();
    console.log(this.seatsArray);
    console.log(this.max);
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

.seatTaken {
  cursor: not-allowed;
}

#bookingTable {
  margin-left: auto;
  margin-right: auto;
}

.btn {
  color: rgb(255, 255, 255);
  background-color: #4b9cd3;
  padding: 1%;
  border-radius: 30px;
  border: 0px;
  margin: 1px;
}
.btn:hover {
  background-color: rgb(73, 150, 201);
}
.btn2 {
  color: rgb(255, 255, 255);
  background-color: #6bd600;
  padding: 1%;
  border-radius: 30px;
  border: 0px;
  margin: 1px;
}
.btn2:hover {
  background-color: rgb(113, 226, 0);
}
button:disabled {
  cursor: not-allowed;
  color: rgb(190, 190, 190);
}
</style>
