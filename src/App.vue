<template>
  <div>
    <button>switch</button>
    <div class="container" v-if="active">
      <div class="row mt-5">
        <div class="col">
          <h1 class="text-center">COVID-19 DATA</h1>
        </div>
      </div>
      <div class="row mt-5" v-if="arrPositive.length > 0">
        <div class="col">
          <h2 class="text-center">Dương tính</h2>
          <line-chart
            :chartData="arrPositive"
            :options="chartOptions"
            :chartColors="positiveChartColors"
            label="Positive"
          />
        </div>
      </div>

      <div class="row mt-5" v-if="arrRecovered.length > 0">
        <div class="col">
          <h2 class="text-center">Hồi phục</h2>
          <line-chart
            :chartData="arrRecovered"
            :options="chartOptions"
            :chartColors="recoveredColors"
            label="Recovered"
          />
        </div>
      </div>

      <div class="row mt-5 mb-5">
        <div class="col">
          <h2 class="text-center">Tử vong</h2>
          <line-chart
            v-if="arrDeaths.length > 0"
            :chartData="arrDeaths"
            :options="chartOptions"
            :chartColors="deathColors"
            label="Deaths"
          />
        </div>
      </div>
    </div>
    <div class="container" v-else>
      <div class="row mt-5" v-for="data in dataTinhThanh" :key="data">
        <div class="col">
          <h1 class="text-center">{{data.properties.Name}}</h1>
        </div>
        <div>So ca nhiem:{{data.properties.infected}}</div>
        <div>Tu vong:{{data.properties.death}}</div>
        <div>Hoi phuc:{{data.properties.recovered}}</div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import moment from "moment";

import LineChart from "./components/LineChart";

export default {
  components: {
    LineChart,
  },
  data() {
    return {
      arrPositive: [],
      positiveChartColors: {
        borderColor: "#077187",
        pointBorderColor: "#0E1428",
        pointBackgroundColor: "#AFD6AC",
        backgroundColor: "#74A57F",
      },
      arrRecovered: [],
      recoveredColors: {
        borderColor: "#4E5E66",
        pointBorderColor: "#4E5E66",
        pointBackgroundColor: "#31E981",
        backgroundColor: "#31E981",
      },
      arrDeaths: [],
      deathColors: {
        borderColor: "#E06D06",
        pointBorderColor: "#E06D06",
        pointBackgroundColor: "#402A2C",
        backgroundColor: "#402A2C",
      },
      chartOptions: {
        responsive: true,
        maintainAspectRatio: false,
      },
      active: true,
      tinhthanh: ["Ha Noi", "Thai Nguyen"],
      dataTinhThanh:[],
    };
  },
  async created() {
    const { data } = await axios.get(
      "https://api.covid19api.com/total/country/viet-nam"
    );
    const  data2  = await axios.get(
      "https://data.vietnam.opendevelopmentmekong.net/geoserver/ODVietnam/ows?service=WFS&version=1.0.0&request=GetFeature&typeName=ODVietnam%3Acoronavirus-covid-19-cases-in-vietnam&outputFormat=application%2Fjson"
    );
    // this.dataTinhThanh=[...data2.features]
    console.log(data2);
    data.forEach((d) => {
      const date = moment(d.Date, "YYYYMMDD").format("MM/DD");
      const { Active, Recovered, Deaths } = d;
      this.arrPositive.push({ date, total: Active });
      this.arrRecovered.push({ date, total: Recovered });
      this.arrDeaths.push({ date, total: Deaths });
    });
  },
};
</script>

<style>
</style>
