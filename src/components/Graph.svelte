<script>
  import { onMount } from 'svelte';
  import { Chart, registerables } from 'chart.js';
  import { csv } from 'd3-fetch';
  Chart.register(...registerables);

  export let dataUrls = [];
  export let index = 0;

  let canvasAccumulated;
  let canvasDailyCases;
  let canvasDailyDeaths;
  let chartAccumulated;
  let chartDailyCases;
  let chartDailyDeaths;

  onMount(() => {
    // Fetch and process data
    csv(dataUrls[0]).then(data => {
      console.log("Fetched data:", data); // Debugging line to check fetched data

      const dates = data.map(d => d.date);
      const accumulatedCases = data.map(d => +d.cases);
      const dailyCases = data.map((d, i) => i > 0 ? d.cases - data[i-1].cases : d.cases);
      const dailyDeaths = data.map((d, i) => i > 0 ? d.deaths - data[i-1].deaths : d.deaths);

      console.log("Dates:", dates); // Debugging line to check dates
      console.log("Accumulated Cases:", accumulatedCases); // Debugging line to check accumulated cases
      console.log("Daily Cases:", dailyCases); // Debugging line to check daily cases
      console.log("Daily Deaths:", dailyDeaths); // Debugging line to check daily deaths

      chartAccumulated = new Chart(canvasAccumulated, {
        type: 'line',
        data: {
          labels: dates,
          datasets: [{
            label: 'Accumulated Cases',
            data: accumulatedCases,
            borderColor: 'rgba(75, 192, 192, 1)',
            borderWidth: 1
          }]
        },
        options: {
          responsive: true,
          maintainAspectRatio: false
        }
      });

      chartDailyCases = new Chart(canvasDailyCases, {
        type: 'line',
        data: {
          labels: dates,
          datasets: [{
            label: 'Daily Cases',
            data: dailyCases,
            borderColor: 'rgba(192, 75, 75, 1)',
            borderWidth: 1
          }]
        },
        options: {
          responsive: true,
          maintainAspectRatio: false
        }
      });

      chartDailyDeaths = new Chart(canvasDailyDeaths, {
        type: 'line',
        data: {
          labels: dates,
          datasets: [{
            label: 'Daily Deaths',
            data: dailyDeaths,
            borderColor: 'rgba(75, 75, 192, 1)',
            borderWidth: 1
          }]
        },
        options: {
          responsive: true,
          maintainAspectRatio: false
        }
      });
    });

    return () => {
      chartAccumulated.destroy();
      chartDailyCases.destroy();
      chartDailyDeaths.destroy();
    };
  });
</script>

<div>
  <canvas bind:this={canvasAccumulated} style="width: 100%; height: 200px;"></canvas>
  <canvas bind:this={canvasDailyCases} style="width: 100%; height: 200px; margin-top: 20px;"></canvas>
  <canvas bind:this={canvasDailyDeaths} style="width: 100%; height: 200px; margin-top: 20px;"></canvas>
</div>

<style>
  canvas {
    width: 100%;
    height: 100%;
  }
</style>
