<template>
  <div>
    <polar-chart
      :chartLabels="this.jobFitData.attributes"
      :band="this.jobFitData.band"
      :series="this.jobFitData.series"
      :heightOffset="this.chartHeightOffset"
    ></polar-chart>
    <Spectrum :loading="false" :items="[32]" :height="10" />
  </div>
</template>

<script lang="ts">
interface ScoresType {
  title: string;
  value: string;
}

interface ScaleType {
  Scores: Array<ScoresType>;
  title: string;
}

interface ReportDataType {
  attribute: string;
  score: number;
  band: string;
  scales?: Array<ScoresType>;
}

interface ResultType {
  Band: string;
  Comments: Array<string>;
  Description: string;
  Language: string;
  Scale: string;
  Score: {
    "#text": number;
    type: string;
  };
}
interface JobfitType {
  attributes: Array<string | undefined>;
  series: Array<number | undefined>;
  band: Array<string | undefined>;
}

import { Component, Vue, Watch } from "vue-property-decorator";
import PolarChart from "./PolarChart.vue";
import Spectrum from "./Spectrum.vue";
import axios from "axios";

@Component({
  components: {
    PolarChart,
    Spectrum
  }
})
export default class AssesmentReport extends Vue {
  private jsonReport: any | undefined = undefined;
  private reportData: Array<ReportDataType> = [];
  private jobFitData: JobfitType = { attributes: [], band: [], series: [] };
  private chartHeightOffset = 10;

  async getJsonData() {
    const response = await axios.get("http://localhost:8080/report.json");
    this.jsonReport = response.data;
  }

  hydrateReportData() {
    this.jsonReport.Results[0].DetailResult.forEach((result: ResultType) => {
      this.reportData.push({
        attribute: result.Description,
        band: result.Band,
        score: result.Score["#text"]
      });
    });

    this.jsonReport.UserArea.Benchmark.Scales.forEach((scale: ScaleType) => {
      const obj = this.reportData.find(o => o.attribute === scale.title);
      if (obj) {
        obj.scales = scale.Scores;
      }
    });
  }

  hydrateJobfitData() {
    this.reportData.forEach((data: ReportDataType) => {
      if (data.scales && data.scales[0].value) {
        this.jobFitData.attributes.push(data.attribute);
        this.jobFitData.band.push(data.band);
        const fitScore = this.calcFitScore(data.scales[0].value, data.score);
        this.jobFitData.series.push(fitScore);
      }
    });
  }

  calcFitScore(range: string, score: number): number {
    const rangeArr = range.split(",");
    const mid =
      ((parseInt(rangeArr[rangeArr.length - 1]) - parseInt(rangeArr[0])) / 2 +
        parseInt(rangeArr[0])) ^
      0;

    return 10 - Math.abs(score - mid);
  }

  async mounted() {
    await this.getJsonData();
    this.hydrateReportData();
    this.hydrateJobfitData();
  }

  // xmlToJson() {
  //   this.jsonReport = XmlConvertor.xml2json(this.xmlReport, {
  //     compact: false,
  //     spaces: 4
  //   });
  // }
}
</script>

<style lang="scss"></style>
