<template>
  <div>
    <h1>Price</h1>
    <section v-if="errored">
      <p>
        We're sorry, we're not able to retrieve this information at the moment,
        please try back later
      </p>
    </section>

    <section v-else>
      <div v-if="loading">Loading...</div>
      <div v-for="(item, index) in info" v-bind:key="item" class="display">
        <label class="checkbox">
          <input
            type="checkbox"
            @click="currencyChange(index)"
            :value="index"
            v-model="selected"
          />
          {{ index }} - {{ item }}
        </label>
      </div>
      <line-chart
        :width="500"
        :height="300"
        :labels="displayedDatasets.labels"
        :datasets="displayedDatasets"
        :options="$options.options"
      ></line-chart>
    </section>
  </div>
</template>
<script>
import axios from "axios";
import numeral from "numeral";
import LineChart from "./LineChart";
let datasets = {
  EUR: {
    label: "EUR",
    labels: [],
    borderColor: "rgba(50, 115, 220, 0.5)",
    backgroundColor: "rgba(50, 115, 220, 0.1)",
    data: [],
  },
  JPY: {
    label: "JPY",
    labels: [],
    borderColor: "rgba(255, 56, 96, 0.5)",
    backgroundColor: "rgba(255, 56, 96, 0.1)",
    data: [],
  },
  USD: {
    label: "USD",
    labels: [],
    borderColor: "rgb(60, 179, 113, 0.5)",
    backgroundColor: "rgb(60, 179, 113, 0.1)",
    data: [],
  },
};
const options = {
  scales: {
    yAxes: [
      {
        ticks: {
          beginAtZero: true,
          callback: (value) => numeral(value).format("$0,0"),
        },
      },
    ],
  },
  tooltips: {
    mode: "index",
    callbacks: {
      label(tooltipItem, data) {
        const label = data.datasets[tooltipItem.datasetIndex].label;
        const value = numeral(tooltipItem.yLabel).format("0,0");
        return `${label}: ${value}`;
      },
    },
  },
};
export default {
  name: "daily-sales-schedule",
  datasets,
  options,
  components: {
    LineChart,
  },
  data() {
    return {
      info: null,
      labels: [],
      loading: true,
      errored: false,
      selected: ["USD"],
      Apikey:
        "31dc63daa5ef5302abcbe3829ff70b439292877c94456b76ba0d93961e33ff0b",
    };
  },
  watch: {
    currentCurrency() {
      this.getData();
    },
  },
  mounted() {
    axios
      .get(
        "https://min-api.cryptocompare.com/data/price?fsym=BTC&tsyms=USD,JPY,EUR"
      )
      .then((response) => (this.info = response.data))
       axios
        .get(
          "https://min-api.cryptocompare.com/data/v2/histohour?fsym=BTC&tsym=" +
            this.getСurrency +
            "&limit=10&" +
            this.Apikey
        )
      .catch((error) => {
        console.log(error);
        this.errored = true;
      })
      .finally(() => (this.loading = false));
  },

  methods: {
    currencyChange:function (index) {
      this.getСurrency = index;
       axios
        .get(
          "https://min-api.cryptocompare.com/data/v2/histohour?fsym=BTC&tsym=" +
            this.getСurrency +
            "&limit=10&" +
            this.Apikey
        )
        .then((response) => {
          this.currency = response.data.Data.Data;
          datasets[this.getСurrency].data = [];
          datasets[this.getСurrency].labels = [];
          for (let item of response.data.Data.Data) {
            datasets[this.getСurrency].data.push(item.high);
            var theDate = new Date(item.time * 1000);
            datasets[this.getСurrency].labels.push(theDate.toGMTString());
          }
        });
    },
  },
  beforeMount(){
    this.currencyChange()
 },
  computed: {
    displayedDatasets() {
      return this.selected.map((hours) => datasets[hours]);
    },
  },
};
</script>
<style scoped>
.display {
  display: inline-block;
  margin: 0 15px;
}
</style>