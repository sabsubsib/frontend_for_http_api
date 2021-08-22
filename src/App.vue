<template>
  <main class="box">
    <p v-if="errorMessage !== null" id="alert">{{ errorMessage }}</p>
    <h2>Difference in {{ selectedCode }} rate:</h2>
    <p id="diff" v-if="currencyRateDiff !== null">
      {{ currencyRateDiff }} &#8381;
    </p>
    <p id="diff" v-else-if="errorMessage !== null">Error</p>
    <p id="diff" v-else>Loading...</p>
    <form @submit="onSubmit">
      <div class="input-date">
        <label for="date1">First date</label>
        <input type="date" name="date1" id="date1" v-model="date1" required />
      </div>
      <div class="input-date">
        <label for="date2">Second date</label>
        <input type="date" name="date2" id="date2" v-model="date2" required />
      </div>
      <div>
        <button type="submit" name="" style="float: left">Submit</button>
      </div>
      <select
        v-model="selectedCode"
        required
      >
        <option v-for="code in currencyCodes" :key="code" :value="code">
          {{ code }}
        </option>
      </select>
    </form>
  </main>
  <footer></footer>
</template>

<script>
import axios from "axios";

export default {
  name: "App",
  data: function () {
    return {
      selectedCode: "USD",
      currencyCodes: [],
      date1: null,
      date2: null,
      currencyRateDiff: null,
      errorMessage: null,
    };
  },
  methods: {
    onSubmit: function (e) {
      this.currencyRateDiff = null;
      this.errorMessage = null;
      if (this.date1 > this.date2) {
        this.errorMessage = "First date should be more second";
      } else {
        this.getCurrencyRateDiff(this.selectedCode, this.date1, this.date2);
      }
      e.preventDefault();
    },
    getCurrencyCodes: function () {
      const path = "http://127.0.0.1:8000/currency_codes";
      axios
        .get(path)
        .then((res) => {
          this.currencyCodes = res.data;
        })
        .catch((error) => {
          this.errorMessage = error.response.data.detail;
        });
    },
    getCurrencyRateDiff: function (code, date1, date2) {
      const path = "http://127.0.0.1:8000/currency_rates_diff";
      axios({
        method: "GET",
        url: path,
        params: {
          code: code,
          date1: date1,
          date2: date2,
        },
      })
        .then((res) => {
          this.currencyRateDiff = res.data;
        })
        .catch((error) => {
          this.errorMessage = error.response.data.detail;
        });
    },
    setDefaultDates: function () {
      const now = new Date();
      const dd = now.getDate();
      const mm = now.getMonth();
      const yyyy = now.getFullYear();
      let formateDate = [
        (mm > 9 ? "" : "0") + mm,
        (dd > 9 ? "" : "0") + dd,
      ].join("-");
      this.date1 = (yyyy - 1).toString() + "-" + formateDate;
      this.date2 = yyyy.toString() + "-" + formateDate;
    },
  },

  created() {
    this.getCurrencyCodes();
    this.setDefaultDates();
    this.getCurrencyRateDiff(this.selectedCode, this.date1, this.date2);
  },
};
</script>

<style>
#app {
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

#alert {
  background-color: rgba(255, 90, 90, 0.5);
  padding: 5px;
  border-radius: 5px;
  color: rgb(255, 53, 53);
}

* {
  box-sizing: border-box;
}
body {
  font-family: sans-serif;
  height: 100vh;
  margin: 0;
  padding: 0;
}
header {
  display: none;
}
.box {
  background-color: #ffffff;
  border-radius: 10px;
  box-shadow: 0 15px 25px rgba(0, 0, 0, 0.61);
  margin: auto auto;
  padding: 30px;
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  text-align: left;
}
.box h2 {
  margin: 0 10px;
  padding: 0;
  color: #03a9f4;
  text-align: center;
}
.box .input-date label {
  color: #03a9f4;
}
.box .input-date input {
  background: transparent;
  border: none;
  border-bottom: 1px solid #03a9f4;
  color: #03a9f4;
  font-size: 18px;
  letter-spacing: 1px;
  margin-bottom: 30px;
  outline: none;
  padding: 5px;
  width: 100%;
  display: inline-block;
}
.box input[type="submit"],
.box button[type="submit"],
.box select,
a.button {
  font-family: sans-serif;
  background: #03a9f4;
  font-size: 12px;
  border: none;
  border-radius: 5px;
  color: #fff;
  cursor: pointer;
  font-weight: 600;
  letter-spacing: 2px;
  outline: none;
  text-transform: uppercase;
  text-decoration: none;
  display: inline-block;
  margin: 2px;
  padding: 10px;
}
.box select {
  float: right;
  letter-spacing: 0;
}

.box input[type="submit"]:hover,
.box button[type="submit"]:hover,
.box select:hover,
a.button:hover {
  opacity: 0.8;
}

#diff {
  font-size: 30px;
  color: #03a9f4;
  border: none;
  text-decoration: underline #03a9f4;
  text-align: center;
  margin: 15px;
}
</style>
