<template>
  <canvas ref="myChart" :width="width" :height="height"></canvas>
</template>

<script>
import Chart from 'chart.js';

export default {
  name: 'daily-sales-schedule',
  props: {

    width: {
      type: Number,
      validator: value => value > 0
    },

    
    height: {
      type: Number,
      validator: value => value > 0
    },

    labels: Array,

    // The chart's data.datasets
    datasets: {
      type: Array,
      required: true
    },

    options: Object
  },
  data() {
    return {
      chart: null
    };
  },
  watch: {
    datasets(newDatasets) {
      // Replace the datasets and call the update() method on Chart.js
      // instance to re-render the chart.
      this.chart.data.datasets = newDatasets;
      if(newDatasets.length > 0){
        this.chart.data.labels = newDatasets[0].labels;
      }
      
      this.chart.update();
    }
  },
  mounted() {
    this.chart = new Chart(this.$refs.myChart, {
      type: 'line',
      data: {
        labels: this.labels,
        datasets: this.datasets
      },
      options: this.options
    });
  },
  beforeDestroy () {
    // Don't forget to destroy the Chart.js instance.
    if (this.chart) {
      this.chart.destroy()
    }
  }
}
</script>
