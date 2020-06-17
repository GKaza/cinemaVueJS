<template>
  <div id="app">
    <h1 id="header">Welcome to Vue Cinema🎬</h1>

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
        <td v-for="seat in seatsInRow(row)" :key="seat.id">
          <Seat
            :id="seat.id"
            :row="seat.row"
            :number="seat.number"
            :class="{ seatTaken: !seat.available, seatSelected: selected(seat.id), seatDisabled: bookingDone }"
            @seat-check="checkSeat($event)"
          ></Seat>
        </td>
      </tr>
    </table>

    <button v-if="this.showBookButton" class="btn" @click="bookSeats">Book your seats</button>

    <div v-if="bookingDone">
      <table id="bookingTable">
        <tr>
          <th>Name</th>
          <th>Number of Seats</th>
          <th>Seats</th>
        </tr>
        <tr>
          <td>{{ booking.name }}</td>
          <td>{{ booking.numberOfSeats }}</td>
          <td>{{ bookedSeatsText }}</td>
        </tr>
      </table>
      <div>
        <button class="btn2" @click="go()">Go</button>
        <button class="btn2" @click="reload()">Make a new booking</button>
      </div>
    </div>

    <div id="delete">
      <button v-if="this.reserved.length" class="btn3" @click="deleteBookings()">Delete all bookings</button>
    </div>
  </div>
</template>

<script>
import Seat from "./components/Seat.vue";
import swal from "sweetalert";

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
    go() {
      window.location.href = "https://www.netflix.com/";
    },
    reload() {
      window.location.reload();
    },
    deleteBookings() {
      localStorage.removeItem("reserved");
      this.reload();
    },
    formInfo() {
      this.formSubmitted = true;
    },
    bookSeats() {
      if (this.set.length) {
        this.booking.bookedSeats = this.set;
        for (let i = 0; i < this.set.length; i += 1) {
          this.seats[this.set[i]].available = false;
        }

        localStorage.setItem(
          "reserved",
          JSON.stringify(this.reserved.concat(this.set))
        );
        swal(
          "Successful Booking " + this.booking.name + "!",
          "Your seats are " + this.bookedSeatsText + ".",
          "success"
        );
      } else {
        swal(
          "Cannot proceed with the booking!",
          "Please pick your seat.",
          "error"
        );
      }
    },
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
          let sget = this.seats[this.reserved[i]];
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

      if (this.formSubmitted) {
        if (!this.booking.bookedSeats.length) {
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
        } else {
          swal("Your booking has already been made!", "", "warning");
        }
      } else {
        swal(
          "Please submit the form first!",
          "Make sure you give us your name and ammount of wanted seats.",
          "warning"
        );
      }
    },
    selected(id) {
      if (this.allSeatsAvailable && this.set.find(seat => seat === id)) {
        return true;
      } else {
        return false;
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
    },
    bookedSeatsText() {
      return this.booking.bookedSeats.join(", ");
    },
    bookingDone() {
      if (this.booking.bookedSeats.length) {
        return true;
      } else {
        return false;
      }
    },
    showBookButton() {
      if (this.allSeatsAvailable && !this.bookingDone) {
        return true;
      } else {
        return false;
      }
    }
  },
  created: function() {
    this.createSeats();
    console.log(this.reserved);
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

#header {
  margin-bottom: 10px;
}

#seatMap {
  margin: auto;
}

.tableHeader {
  min-width: 20px;
}

#bookingTable > tr > th {
  min-width: 130px;
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
  margin: auto;
}

.btn {
  color: rgb(255, 255, 255);
  background-color: #4b9cd3;
  cursor: pointer;
  padding: 1%;
  border-radius: 30px;
  border: 0px;
  margin: 1px 1px 5px 1px;
  outline: 0px;
}

.btn:hover {
  background-color: rgb(73, 150, 201);
}

.btn2 {
  color: rgb(255, 255, 255);
  background-color: #6bd600;
  cursor: pointer;
  padding: 1%;
  border-radius: 30px;
  border: 0px;
  margin: 1px 10px;
  outline: 0px;
}

.btn2:hover {
  background-color: rgb(113, 226, 0);
}

#delete {
  margin-top: 120px;
}

.btn3 {
  color: rgb(255, 255, 255);
  background-color: #ff0000;
  border-radius: 30px;
  padding: 1%;
  border: 0px;
  height: 0.5rem;
  display: inline-flex;
  align-items: center;
  cursor: pointer;
  outline: 0px;
}

.btn3:hover {
  background-color: #e70000;
}

:disabled {
  cursor: not-allowed;
  color: rgb(190, 190, 190);
}
</style>
