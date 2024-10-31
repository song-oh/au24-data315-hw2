<script>
  import * as d3 from "d3";
  import { onMount } from "svelte";
  import Map from "./Map.svelte";
  import Histogram from "./Histogram.svelte";
  import Scatterplot from "./Scatterplot.svelte";

  let data = [];
  let fullData = [];
  let filter1 = [];
  let filter2 = [];
  let filter3 = [];

  let var1 = "percentPoverty";
  let var2 = "percentUnemployed";
  let var3 = "percentUneducated";
  let var4 = "income";

  onMount(async function () {
    let table = d3.csv("chi-income.csv", (d) => ({
      ...d,
      Neigh: d["COMMUNITY AREA NAME"],
      Income: +d["PER CAPITA INCOME"],
      "Poverty Rate": +d["PERCENT HOUSEHOLDS BELOW POVERTY"],
      "Unemployment Rate": +d["PERCENT AGED 16+ UNEMPLOYED"],
      "Uneducated Rate": +d["PERCENT AGED 25+ WITHOUT HIGH SCHOOL DIPLOMA"],
    }));

    let geocoord = d3.json("chi-neighborhoods.geojson").then((d) => d.features);

    await Promise.all([table, geocoord]).then((values) => {
      let table = values[0];
      let geocoord = values[1];

      for (let i = 0; i < geocoord.length; i++) {
        let neigh = geocoord[i].properties.pri_neigh;
        let found = false;
        let j = 0;
        while (!found && table.length > j) {
          if (table[j].Neigh == neigh) {
            found = true;
            data.push(geocoord[i]);
            data[data.length - 1].properties["income"] = table[j]["Income"];
            data[data.length - 1].properties["percentPoverty"] =
              table[j]["Poverty Rate"];
            data[data.length - 1].properties["percentUnemployed"] =
              table[j]["Unemployment Rate"];
            data[data.length - 1].properties["percentUneducated"] =
              table[j]["Uneducated Rate"];
          } else {
            j++;
          }
        }
      }
      fullData = [...data];
    });
  });

  function updateData() {
    if (filter1.length > 0 && filter2.length > 0 && filter3.length > 0) {
      data = fullData.filter(
        (d) =>
          d.properties[var1] >= filter1[0] &&
          d.properties[var1] < filter1[1] &&
          d.properties[var2] >= filter2[0] &&
          d.properties[var2] < filter2[1] &&
          d.properties[var3] >= filter3[0] &&
          d.properties[var3] < filter3[1] &&
          d.properties[var4] >= filter3[2] &&
          d.properties[var4] < filter3[3],
      );
    } else if (filter1.length > 0 && filter2.length > 0) {
      data = fullData.filter(
        (d) =>
          d.properties[var1] >= filter1[0] &&
          d.properties[var1] < filter1[1] &&
          d.properties[var2] >= filter2[0] &&
          d.properties[var2] < filter2[1],
      );
    } else if (filter1.length > 0 && filter3.length > 0) {
      data = fullData.filter(
        (d) =>
          d.properties[var1] >= filter1[0] &&
          d.properties[var1] < filter1[1] &&
          d.properties[var3] >= filter3[0] &&
          d.properties[var3] < filter3[1] &&
          d.properties[var4] >= filter3[2] &&
          d.properties[var4] < filter3[3],
      );
    } else if (filter2.length > 0 && filter3.length > 0) {
      data = fullData.filter(
        (d) =>
          d.properties[var2] >= filter2[0] &&
          d.properties[var2] < filter2[1] &&
          d.properties[var3] >= filter3[0] &&
          d.properties[var3] < filter3[1] &&
          d.properties[var4] >= filter3[2] &&
          d.properties[var4] < filter3[3],
      );
    } else if (filter1.length > 0) {
      data = fullData.filter(
        (d) =>
          d.properties[var1] >= filter1[0] && d.properties[var1] < filter1[1],
      );
    } else if (filter2.length > 0) {
      data = fullData.filter(
        (d) =>
          d.properties[var2] >= filter2[0] && d.properties[var2] < filter2[1],
      );
    } else if (filter3.length > 0) {
      data = fullData.filter(
        (d) =>
          d.properties[var3] >= filter3[0] &&
          d.properties[var3] < filter3[1] &&
          d.properties[var4] >= filter3[2] &&
          d.properties[var4] < filter3[3],
      );
    } else {
      data = [...fullData];
    }
  }
</script>

<main>
  <h1>Economic Landscape of Chicago Neighborhoods</h1>
  <div class="flex-container row">
    <div class="map">
      <h2>Average Income Per Capita</h2>
      <Map {data} {fullData}></Map>
    </div>
    <div class="flex-container col">
      <div class="hist">
        <h3>Poverty Rate</h3>
        <Histogram
          {data}
          {fullData}
          variable={var1}
          bind:filter={filter1}
          update={updateData}
        ></Histogram>
        <div class="axis-labels">
          <div class="x-axis">Households Below Poverty(%)</div>
          <div class="y-axis">Count</div>
        </div>
      </div>
      <div class="hist">
        <h3>Unemployment</h3>
        <Histogram
          {data}
          {fullData}
          variable={var2}
          bind:filter={filter2}
          update={updateData}
        ></Histogram>
        <div class="axis-labels">
          <div class="x-axis">Unemployed Population(%)</div>
          <div class="y-axis">Count</div>
        </div>
      </div>
      <div class="scatter">
        <h3>Undereducation and Income</h3>
        <Scatterplot
          {data}
          {fullData}
          xVariable={var3}
          yVariable={var4}
          bind:filter={filter3}
          update={updateData}
        ></Scatterplot>
        <div class="axis-labels">
          <div class="x-axis">Population Without High School Diploma(%)</div>
          <div class="y-axis">Income($)</div>
        </div>
      </div>
    </div>
  </div>
</main>

<style>
  .flex-container {
    display: flex;
    justify-content: center;
    height: 100%;
    padding: 5px;
    gap: 5px;
  }

  .flex-container > div {
    padding: 5px;
  }

  .flex-container .col {
    display: flex;
    flex-direction: column;
  }

  .hist,
  .scatter {
    height: 200px;
    position: relative;
    top: -20px;
  }

  .axis-labels {
    position: relative;
    bottom: 20px;
    text-align: center;
    font-size: 12px;
  }

  .y-axis {
    position: relative;
    top: 60px;
    transform: rotate(-90deg);
    transform-origin: left bottom;
  }

  h1 {
    font-size: 26px;
    line-height: 0;
    margin-bottom: 10px;
    margin-left: 10px;
  }

  h2 {
    font-size: 18px;
    margin-top: 50px;
    margin-bottom: -30px;
  }

  h3 {
    font-size: 16px;
    margin-bottom: -10px;
  }
</style>
