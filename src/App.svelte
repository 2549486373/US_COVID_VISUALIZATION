<script>
  import Scroller from "@sveltejs/svelte-scroller";
  import Map from "./components/Map.svelte";
  import Graph from "./components/Graph.svelte";
  import { geoMercator } from "d3-geo";

  let count, index, offset, progress;
  let width, height;

  let geoJsonToFit = {
    type: "FeatureCollection",
    features: [
      {
        type: "Feature",
        geometry: {
          type: "Point",
          coordinates: [1, 0],
        },
      },
      {
        type: "Feature",
        geometry: {
          type: "Point",
          coordinates: [0, 1],
        },
      },
    ],
  };

  $: projection = geoMercator().fitSize([width, height], geoJsonToFit);

  const sections = Array.from({ length: 6 }, (_, i) => `This is section ${i + 1}.`);
  const dataUrls = [
    'public/data/us.csv'
  ];
</script>

<style>
  .container {
    display: flex;
    width: 100%;
    height: 100vh;
  }

  .map-container {
    width: 75%; /* 3/4 of the screen */
    height: 100vh;
    position: relative;
  }

  .sections-container {
    width: 25%; /* 1/4 of the screen */
    height: 100vh;
    overflow-y: auto;
    position: relative;
  }

  .title {
    position: absolute;
    font-size: 1.5em;
    font-weight: bold;
    background: rgba(255, 255, 255, 0.7); /* Add a background to make the text more readable */
    padding: 0.2em;
    border-radius: 0.2em;
  }

  .mainland-title {
    top: 10px;
    left: 10px;
  }

  .hawaii-title {
    top: 700px; /* Adjust these values to place the title appropriately */
    left: 180px;
  }

  .alaska-title {
    top: 700px; /* Adjust these values to place the title appropriately */
    left: 20px;
  }

  section {
    height: 80vh; /* Reduced to half of its original height */
    text-align: center;
    max-width: 750px;
    color: black;
    padding: 1em;
    margin: 0 0 2em 0;
  }

  .graph-container {
    width: 100%;
    height: 600px; /* Adjust the height as needed */
    margin-top: 1em;
  }
</style>

<div class="container">
  <div 
    class="map-container"
    bind:clientWidth={width}
    bind:clientHeight={height}
  >
    <Map bind:geoJsonToFit {index} />

    <div class="title mainland-title">US Mainland</div>
    <div class="title hawaii-title">Hawaii</div>
    <div class="title alaska-title">Alaska</div>
  </div>

  <div class="sections-container">
    <Scroller
      top={0.0}
      bottom={1}
      threshold={0.5}
      bind:count
      bind:index
      bind:offset
      bind:progress
    >
      <div class="foreground" slot="foreground">
        {#each sections as section, i}
          <section>
            {section}
            <div class="graph-container">
              <Graph {dataUrls} {index} />
            </div>
          </section>
        {/each}
      </div>
    </Scroller>
  </div>
</div>
