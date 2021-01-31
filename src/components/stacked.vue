<template>
  <div id="chart">
    <apexchart
      type="bar"
      height="200"
      :options="chartOptions"
      :series="series"
    ></apexchart>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from "vue-property-decorator";
import VueApexCharts from "vue-apexcharts";

@Component({
  components: {
    apexchart: VueApexCharts
  }
})
export default class Spectrum extends Vue {
  private series = [
    {
      name: "Poor",
      data: [3]
    },
    {
      name: "Good",
      data: [4]
    },
    {
      name: "Ok",
      data: [3]
    }
  ];

  private chartOptions = {
    chart: {
      type: "bar",
      stacked: true
    },
    dataLabels: {
      enabled: false
    },
    plotOptions: {
      bar: {
        horizontal: true
      }
    },
    stroke: {
      width: 1,
      colors: ["#fff"]
    },
    title: {
      text: "Some text"
    },
    xaxis: {
      categories: [],
      labels: {
        formatter: function(val: any) {
          return val;
        }
      },
      max: 10
    },
    yaxis: {
      show: false
    },
    tooltip: {
      enabled: false
    },
    fill: {
      opacity: 0.8
    },
    legend: {
      position: "top",
      horizontalAlign: "right",
      offsetX: 40
    }
  };
}
</script>
