<template>
  <p v-if="message !== null" id="alert">{{ message }}</p>
  <form @submit="onSubmit">
    <select v-model="selectedCode" required>
      <option v-for="code in currencyCodes" :key="code" :value="code">
        {{ code }}
      </option>
    </select>
    <div class="input_date">
      <label for="date1">input date1</label>
      <input type="date" name="date1" id="date1" required v-model="date1" />
    </div>
    <div class="input_date">
      <label for="date2">input date2</label>
      <input type="date" name="date2" id="date2" required v-model="date2" />
    </div>
    <button type="submit" name="btn">Submit</button>
  </form>
  <p v-if="currencyRatesDiff !== null">{{ currencyRateDiff }}</p>
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
      message: null,
    };
  },
  methods: {
    onSubmit: function (e) {
      const path = "http://127.0.0.1:8000/currency_rates_diff";
      this.currencyRateDiff = null;
      this.message = null;
      if (this.date1 > this.date2) {
        this.message = "Invalid input";
      } else {
        axios({
          method: "GET",
          url: path,
          params: {
            code: this.selectedCode,
            date1: this.date1,
            date2: this.date2,
          },
        })
          .then((res) => {
            this.currencyRateDiff = res.data;
          })
          .catch((error) => {
            this.message = error.response.data.detail;
          });
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
          this.message = error.response.data.detail;
        });
    },
  },
  created() {
    this.getCurrencyCodes();
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

#alert {
  color: red;
}
</style>
