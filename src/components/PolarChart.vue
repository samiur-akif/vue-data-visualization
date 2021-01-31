
<template>
  <apexchart
    type="polarArea"
    :options="chartOptions"
    :series="series"
  ></apexchart>
</template>

<script lang="ts">
interface SeriesColorType {
  value: number;
  seriesIndex: number;
  w: unknown;
}
import { Vue, Component, Prop } from "vue-property-decorator";
import VueApexCharts from "vue-apexcharts";

@Component({
  components: {
    apexchart: VueApexCharts
  }
})
export default class PolarChart extends Vue {
  @Prop() readonly series!: Array<number>;
  @Prop() readonly chartLabels!: Array<string>;
  @Prop() readonly band!: Array<string>;
  @Prop() readonly heightOffset!: number;
  private chartOptions = {
    chart: {
      type: "polarArea",
      parentHeightOffset: this.heightOffset,
      events: {
        dataPointSelection: function(event: any, chartContext: any, config: any) {
          // The last parameter config contains additional information like `seriesIndex` and `dataPointIndex` for cartesian charts
          console.log(config);
        }
      }
    },
    stroke: {
      colors: ["#f9f9f9"]
    },
    fill: {
      opacity: 0.8
    },
    responsive: [
      {
        breakpoint: 480,
        options: {
          chart: {
            width: "100%"
          },
          legend: {
            position: "bottom"
          }
        }
      }
    ],
    legend: {
      show: false
    },
    labels: this.chartLabels,
    xaxis: {
      show: true
    },
    colors: [
      ({ value, seriesIndex, w }: SeriesColorType) => {
        if (this.band[seriesIndex] === "Poor") {
          return "#FF4560";
        } else if (this.band[seriesIndex] === "OK") {
          return "#FEB019";
        } else {
          return "#00E396";
        }
      }
    ]
  }
}
</script>
