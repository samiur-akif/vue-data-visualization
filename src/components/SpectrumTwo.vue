<template>
  <div id="chart">
    <div id="chart">
      <apexchart
        type="rangeBar"
        height="200"
        :options="chartOptions"
        :series="series"
      ></apexchart>
    </div>
  </div>
</template>

<script lang="ts">
interface SeriesType {
  name: string;
  data: { x: string; y: Array<number> };
}

interface SeriesColorType {
  value: number;
  seriesIndex: number;
  w: unknown;
}

interface RangesType {
  title: string;
  score: number;
  value: Array<{ title: string; value: string }> | undefined;
}
import { Component, Vue, Prop } from "vue-property-decorator";
import VueApexCharts from "vue-apexcharts";

@Component({
  components: {
    apexchart: VueApexCharts
  }
})
export default class Spectrum extends Vue {
  @Prop() readonly title!: string;
  @Prop() readonly score!: number;
  @Prop() readonly maxRange!: number;
  @Prop() readonly ranges!: Array<SeriesType>;

  private series = this.ranges;

  private chartOptions = {
    title: {
      text: this.title
    },
    chart: {
      height: 200,
      type: "rangeBar",
      toolbar: { show: false }
    },
    plotOptions: {
      bar: {
        horizontal: true,
        barHeight: "50%",
        rangeBarGroupRows: false
      }
    },
    fill: {
      opacity: 0.8
    },
    xaxis: {
      type: "datetime",
      max: 10
    },
    yaxis: {
      show: false
    },
    legend: {
      position: "bottom"
    },
    tooltip: {
      enabled: false
    },
    annotations: {
      xaxis: [
        {
          x: this.score,
          strokeDashArray: 0,
          borderColor: "#775DD0",
          label: {
            borderColor: "#775DD0",
            style: {
              color: "#fff",
              background: "#775DD0"
            },
            text: "Score: " + this.score
          }
        }
      ]
    },
    colors: [
      ({ value, seriesIndex, w }: SeriesColorType) => {
        if (this.series[seriesIndex].name === "Poor") {
          return "#FF4560";
        } else if (this.series[seriesIndex].name === "OK") {
          return "#FEB019";
        } else {
          return "#00E396";
        }
      }
    ]
  };
}
</script>
