<template>
  <div>
    <div
      v-for="data in spectrumData"
      :key="data.title"
      style="margin-top: 50px;"
    >
      <spectrum
        :title="data.title"
        :score="data.score"
        :maxRange="10"
        :ranges="data.ranges"
      ></spectrum>
    </div>
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

interface SeriesType {
  name: string;
  data: [{ x: string; y: Array<number> }];
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

interface SpectrumType {
  title: string;
  score: number;
  ranges: Array<SeriesType> | undefined;
}

import { Component, Vue } from "vue-property-decorator";
import Spectrum from "./Spectrum.vue";
import axios from "axios";

@Component({
  components: {
    Spectrum
  }
})
export default class SpectrumReport extends Vue {
  private jsonReport: any | undefined = undefined;
  private reportData: Array<ReportDataType> = [];
  private spectrumData: Array<SpectrumType> = [];
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

  hydrateSpectrumData() {
    this.reportData.forEach((data: ReportDataType) => {
      this.spectrumData.push({
        title: data.attribute,
        score: data.score,
        ranges: this.hydrateRanges(data.scales)
      });
    });
  }

  hydrateRanges(scales: Array<ScoresType> | undefined) {
    const ranges: any= [];

    scales?.forEach((scale: any) => {
      ranges.push({
        name: scale.title,
        data: [
          {
            x: "Brand",
            y: this.getRange(scale.value)
          }
        ]
      });
    });
    return ranges;
  }

  getRange(rangeStr: string | any): Array<number> {
    if (rangeStr.length) {
      const rangeArr = rangeStr.split(",");
      return [parseInt(rangeArr[0]), parseInt(rangeArr[rangeArr.length - 1])];
    } else {
      return [0, 0];
    }
  }

  async created() {
    await this.getJsonData();
    this.hydrateReportData();
    this.hydrateSpectrumData();
    console.log(this.jsonReport)
  }
}
</script>

<style lang="scss"></style>
