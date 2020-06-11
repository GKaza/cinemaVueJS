<template>
  <div id="app">
    <h1>Welcome to Vue Cinema</h1>

    <form @submit.prevent="formInfo" class="form-container" v-if="!this.formSubmitted">
      <div class="form-group">
        <label for="name">Name:</label>
        <input id="name" v-model="booking.name" />
        <p v-if="!this.nameIsValid" class="error-message">Please input a valid name.</p>
      </div>
      <div class="form-group">
        <label for="wantedSeats">Number of Seats:</label>
        <input id="wantedSeats" v-model.number="booking.numberOfSeats" />
        <p
          v-if="!this.numberIsValid"
          class="error-message"
        >Please pick the ammount of wanted seats between 1 and 20.</p>
      </div>

      <input :disabled="!this.formIsValid" type="submit" class="btn" />
    </form>

    <table id="seatMap">
      <tr>
        <td></td>
        <td v-for="seat in seatsInRow('A')" class="tableHeader" :key="seat.number">{{ seat.number }}</td>
      </tr>
      <tr v-for="row in rows" :key="row">
        <td>{{ row }}</td>
        <Seat
          v-for="seat in seatsInRow(row)"
          :key="seat.id"
          :id="seat.id"
          class="seat"
          :class="{ seatTaken: !seat.available, seatSelected: selected(seat.id) }"
          :row="seat.row"
          :number="seat.number"
          :available="seat.available"
          @seat-check="checkSeat($event)"
        ></Seat>
      </tr>
    </table>

    <button v-if="allSeatsAvailable" class="btn" @click="bookSeats">Book your seats</button>

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
        name: null,
        numberOfSeats: null,
        bookedSeats: []
      },
      rows: ["A", "B", "C", "D", "E", "F", "G", "H", "I", "J"],
      seats: {},
      max: {},
      formSubmitted: false,
      allSeatsAvailable: false,
      set: [],
      reserved: JSON.parse(localStorage.getItem("reserved"))
    };
  },
  methods: {
    formInfo() {
      this.formSubmitted = true;
    },
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
        if (!this.allSeatsAvailable) {
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
    },
    seatsInRow(row) {
      return this.seatsArray.filter(seat => seat.row === row);
    }
  },
  computed: {
    seatsArray() {
      return Object.values(this.seats);
    },
    nameIsValid() {
      return !!this.booking.name;
    },
    numberIsValid() {
      return (
        typeof this.booking.numberOfSeats === "number" &&
        this.booking.numberOfSeats > 0 &&
        this.booking.numberOfSeats <= 21
      );
    },
    formIsValid() {
      return this.nameIsValid && this.numberIsValid;
    }
  },
  created: function() {
    this.createSeats();
    console.log(this.seatsInRow("A"));
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

#seatMap {
  margin: auto;
}

.tableHeader {
  min-width: 20px;
}

.seatTaken {
  cursor: not-allowed;
}

.form-container {
  width: 70%;
  margin: auto;
}

.form-group {
  float: left;
  width: 50%;
}

.error-message {
  color: #ff0000;
  font-size: 0.65em;
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

:disabled {
  cursor: not-allowed;
  color: rgb(190, 190, 190);
}
</style>
