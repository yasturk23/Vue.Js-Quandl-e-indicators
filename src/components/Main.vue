<!-- eslint-disable vue/multi-word-component-names -->

<script>
import Highcharts from "highcharts";

export default {
  data() {
    return {
      apiKey: "PTViNcYniHvYxkpQ8YoS",
      selectedDataset: "",
      selectedTime: 12,
      selectedData: {},
      timePeriods: [1, 3, 6, 12, 24, 36, 48, 60, 120],
      datasets: [
        {
          id: "M2_M",
          name: "M2; Seasonally adjusted, Monthly",
          provider: "FED",
        },
        {
          id: "NAPM",
          name: "ISM Manufacturing: PMI Composite Index",
          provider: "FRED",
        },
        {
          id: "NMFCI",
          name: "ISM Non-manufacturing: NMI Composite Index",
          provider: "FRED",
        },
        {
          id: "PERMIT",
          name: "New Private Housing Units Authorized by Building Permits",
          provider: "FRED",
        },
        {
          id: "HOUST",
          name: "Housing Starts: Total: New Privately Owned Housing Units Started",
          provider: "FRED",
        },
        {
          id: "COMPUTSA",
          name: "New Privately-Owned Housing Units Completed: Total",
          provider: "FRED",
        },
      ],
      data: null,
    };
  },
  mounted() {
    this.getData();
  },
  watch: {
    selectedDataset() {
      this.getData();
    },
  },
  methods: {
    async getData() {
      const response = await fetch(
        `https://data.nasdaq.com/api/v3/datasets/${this.selectedData.provider}/${this.selectedData.id}.json?api_key=PTViNcYniHvYxkpQ8YoS`
      ).catch((error) => {
        console.log(error.message);
      });

      const json = await response.json();
      //console.log(json.dataset);
      // this.data = csv
      //   .split("\n")
      //   .slice(1)
      //   .map((row) => row.split(",")[1]);

      // extract data points from JSON
      const dates = json.dataset.data.map((observation) => observation[0]);
      const values = json.dataset.data.map((observation) => observation[1]);

      // console.log(dates);
      // console.log(values);

      // only display the last 12 entries
      // const slicedDates = dates.slice(12);
      // const slicedValues = values.slice(12);

      // create chart using Highcharts
      Highcharts.chart("chart", {
        title: {
          text: json.dataset.name,
        },
        xAxis: {
          reversed: true,
          categories: dates,
          min: 0,
          max: this.selectedTime - 1,
        },
        yAxis: {
          title: {
            text: "Value",
          },
        },
        series: [
          {
            type: "line",
            name: this.selectedDataset,
            data: values,
          },
        ],
        navigator: {
          enabled: true,
        },
      });
      console.log(values);
    },
    computed: {
      setData() {
        return this.datasets.find((data) => data.id === this.selectedDataset);
      },
      setTime() {
        return this.time.find((data) => data.id === this.selectedDataset);
      },
    },
  },
};
</script>

<template>
  <div>
    <h1>Economic Data from Quandl</h1>
    <p>Select a dataset from the dropdown menu:</p>

    <select v-model="selectedData" @change="setData">
      <option v-for="dataset in datasets" :key="dataset.id" :value="dataset">
        {{ dataset.name }}
      </option>
    </select>
    <select v-model="selectedTime">
      <option v-for="time in timePeriods" :key="time" :value="time">
        {{ time }} month(s)
      </option>
    </select>
    <button @click="getData">Get Data</button>
    <p>Data:</p>
  </div>
  <div id="chart"></div>
</template>
